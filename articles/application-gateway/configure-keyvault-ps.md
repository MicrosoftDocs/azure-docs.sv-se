---
title: Konfigurera TLS-avslutning med Key Vault certifikat – PowerShell
titleSuffix: Azure Application Gateway
description: Lär dig hur du använder ett Azure PowerShell-skript för att integrera nyckel valvet med din Application Gateway för certifikat för TLS/SSL-avslutning.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 05/26/2020
ms.author: victorh
ms.openlocfilehash: aaaeed9d8d6a2d84fa13f495f581dc1f5fdc19e2
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91323432"
---
# <a name="configure-tls-termination-with-key-vault-certificates-using-azure-powershell"></a>Konfigurera TLS-avslutning med Key Vault certifikat med Azure PowerShell

[Azure Key Vault](../key-vault/general/overview.md) är ett plattforms hanterat hemligt arkiv som du kan använda för att skydda hemligheter, nycklar och TLS/SSL-certifikat. Azure Application Gateway stöder integrering med Key Vault för Server certifikat som är anslutna till HTTPS-aktiverade lyssnare. Detta stöd är begränsat till Application Gateway v2-SKU: n.

Läs mer i [TLS-avslutning med Key Vault-certifikat](key-vault-certs.md).

Den här artikeln visar hur du använder ett Azure PowerShell-skript för att integrera nyckel valvet med din Application Gateway för certifikat för TLS/SSL-avslutning.

Den här artikeln kräver Azure PowerShell module version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du vill köra kommandona i den här artikeln måste du också skapa en anslutning till Azure genom att köra `Connect-AzAccount` .

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar måste du ha installerat ManagedServiceIdentity-modulen:

```azurepowershell
Install-Module -Name Az.ManagedServiceIdentity
Connect-AzAccount
Select-AzSubscription -Subscription <your subscription>
```

## <a name="example-script"></a>Exempelskript

### <a name="set-up-variables"></a>Skapa variabler

```azurepowershell
$rgname = "KeyVaultTest"
$location = "East US"
$kv = "<your key vault name>"
$appgwName = "AppGwKVIntegration"
```
> [!IMPORTANT]
> Namnet på nyckel valvet måste vara globalt unikt.

### <a name="create-a-resource-group-and-a-user-managed-identity"></a>Skapa en resurs grupp och en användar hanterad identitet

```azurepowershell
$resourceGroup = New-AzResourceGroup -Name $rgname -Location $location
$identity = New-AzUserAssignedIdentity -Name "appgwKeyVaultIdentity" `
  -Location $location -ResourceGroupName $rgname
```

### <a name="create-a-key-vault-policy-and-certificate-to-be-used-by-the-application-gateway"></a>Skapa ett nyckel valv, en princip och ett certifikat som ska användas av Application Gateway

```azurepowershell
$keyVault = New-AzKeyVault -Name $kv -ResourceGroupName $rgname -Location $location -EnableSoftDelete 
Set-AzKeyVaultAccessPolicy -VaultName $kv -PermissionsToSecrets get -ObjectId $identity.PrincipalId

$policy = New-AzKeyVaultCertificatePolicy -ValidityInMonths 12 `
  -SubjectName "CN=www.contoso11.com" -IssuerName self `
  -RenewAtNumberOfDaysBeforeExpiry 30
Set-AzKeyVaultAccessPolicy -VaultName $kv -EmailAddress <your email address> -PermissionsToCertificates create,get,list
$certificate = Add-AzKeyVaultCertificate -VaultName $kv -Name "cert1" -CertificatePolicy $policy
$certificate = Get-AzKeyVaultCertificate -VaultName $kv -Name "cert1"
$secretId = $certificate.SecretId.Replace($certificate.Version, "")
```
> [!NOTE]
> Flaggan-EnableSoftDelete måste användas för att TLS-avslutning ska fungera korrekt. Om du konfigurerar [Key Vault mjuk borttagning via portalen](../key-vault/general/soft-delete-overview.md#soft-delete-behavior), måste kvarhållningsperioden vara i 90 dagar, standardvärdet. Application Gateway stöder inte en annan kvarhållningsperiod än. 

### <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

```azurepowershell
$sub1 = New-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -AddressPrefix "10.0.0.0/24"
$sub2 = New-AzVirtualNetworkSubnetConfig -Name "backendSubnet" -AddressPrefix "10.0.1.0/24"
$vnet = New-AzvirtualNetwork -Name "Vnet1" -ResourceGroupName $rgname -Location $location `
  -AddressPrefix "10.0.0.0/16" -Subnet @($sub1, $sub2)
```

### <a name="create-a-static-public-virtual-ip-vip-address"></a>Skapa en statisk offentlig virtuell IP-adress (VIP)

```azurepowershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $rgname -name "AppGwIP" `
  -location $location -AllocationMethod Static -Sku Standard
```

### <a name="create-pool-and-front-end-ports"></a>Skapa pool och klient dels portar

```azurepowershell
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name "appgwSubnet" -VirtualNetwork $vnet

$gipconfig = New-AzApplicationGatewayIPConfiguration -Name "AppGwIpConfig" -Subnet $gwSubnet
$fipconfig01 = New-AzApplicationGatewayFrontendIPConfig -Name "fipconfig" -PublicIPAddress $publicip
$pool = New-AzApplicationGatewayBackendAddressPool -Name "pool1" `
  -BackendIPAddresses testbackend1.westus.cloudapp.azure.com, testbackend2.westus.cloudapp.azure.com
$fp01 = New-AzApplicationGatewayFrontendPort -Name "port1" -Port 443
$fp02 = New-AzApplicationGatewayFrontendPort -Name "port2" -Port 80
```

### <a name="point-the-tlsssl-certificate-to-your-key-vault"></a>Peka TLS/SSL-certifikatet till ditt nyckel valv

```azurepowershell
$sslCert01 = New-AzApplicationGatewaySslCertificate -Name "SSLCert1" -KeyVaultSecretId $secretId
```

### <a name="create-listeners-rules-and-autoscale"></a>Skapa lyssnare, regler och autoskalning

```azurepowershell
$listener01 = New-AzApplicationGatewayHttpListener -Name "listener1" -Protocol Https `
  -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp01 -SslCertificate $sslCert01
$listener02 = New-AzApplicationGatewayHttpListener -Name "listener2" -Protocol Http `
  -FrontendIPConfiguration $fipconfig01 -FrontendPort $fp02
$poolSetting01 = New-AzApplicationGatewayBackendHttpSetting -Name "setting1" -Port 80 `
  -Protocol Http -CookieBasedAffinity Disabled
$rule01 = New-AzApplicationGatewayRequestRoutingRule -Name "rule1" -RuleType basic `
  -BackendHttpSettings $poolSetting01 -HttpListener $listener01 -BackendAddressPool $pool
$rule02 = New-AzApplicationGatewayRequestRoutingRule -Name "rule2" -RuleType basic `
  -BackendHttpSettings $poolSetting01 -HttpListener $listener02 -BackendAddressPool $pool
$autoscaleConfig = New-AzApplicationGatewayAutoscaleConfiguration -MinCapacity 3
$sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
```

### <a name="assign-the-user-managed-identity-to-the-application-gateway"></a>Tilldela den användar hanterade identiteten till Application Gateway

```azurepowershell
$appgwIdentity = New-AzApplicationGatewayIdentity -UserAssignedIdentityId $identity.Id
```

### <a name="create-the-application-gateway"></a>Skapa programgatewayen

```azurepowershell
$appgw = New-AzApplicationGateway -Name $appgwName -Identity $appgwIdentity -ResourceGroupName $rgname `
  -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting01 `
  -GatewayIpConfigurations $gipconfig -FrontendIpConfigurations $fipconfig01 `
  -FrontendPorts @($fp01, $fp02) -HttpListeners @($listener01, $listener02) `
  -RequestRoutingRules @($rule01, $rule02) -Sku $sku `
  -SslCertificates $sslCert01 -AutoscaleConfiguration $autoscaleConfig
```

## <a name="next-steps"></a>Nästa steg

[Läs mer om TLS-terminering](ssl-overview.md)
