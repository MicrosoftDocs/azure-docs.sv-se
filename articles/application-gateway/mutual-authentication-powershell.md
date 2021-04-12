---
title: Konfigurera ömsesidig autentisering på Azure Application Gateway via PowerShell
description: Lär dig hur du konfigurerar en Application Gateway att ha ömsesidig autentisering via PowerShell
services: application-gateway
author: mscatyao
ms.service: application-gateway
ms.topic: how-to
ms.date: 04/02/2021
ms.author: caya
ms.openlocfilehash: 95534760c09ca9e1f7f09d6079886216127c7eb0
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/02/2021
ms.locfileid: "106231510"
---
# <a name="configure-mutual-authentication-with-application-gateway-through-powershell-preview"></a>Konfigurera ömsesidig autentisering med Application Gateway via PowerShell (för hands version)
Den här artikeln beskriver hur du använder PowerShell för att konfigurera ömsesidig autentisering på Application Gateway. Ömsesidig autentisering innebär att Application Gateway autentiserar klienten som skickar begäran med hjälp av det klient certifikat som du överför till Application Gateway. 

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Den här artikeln kräver Azure PowerShell module version 1.0.0 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Login-AzAccount` för att skapa en anslutning till Azure.

## <a name="before-you-begin"></a>Innan du börjar

Om du vill konfigurera ömsesidig autentisering med en Application Gateway behöver du ett klient certifikat att ladda upp till gatewayen. Klient certifikatet kommer att användas för att verifiera det certifikat som klienten kommer att presentera för att Application Gateway. I test syfte kan du använda ett självsignerat certifikat. Detta rekommenderas dock inte för produktions arbets belastningar eftersom de är svårare att hantera och inte är helt säkra.

Mer information, särskilt om vilken typ av klient certifikat som du kan ladda upp, finns i [Översikt över ömsesidig autentisering med Application Gateway](./mutual-authentication-overview.md#certificates-supported-for-mutual-authentication).

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa först en ny resurs grupp i din prenumeration. 

```azurepowershell
$resourceGroup = New-AzResourceGroup -Name $rgname -Location $location -Tags @{ testtag = "APPGw tag"}
```
## <a name="create-a-virtual-network"></a>Skapa ett virtuellt nätverk

Distribuera ett virtuellt nätverk som Application Gateway ska distribueras i.

```azurepowershell
$gwSubnet = New-AzVirtualNetworkSubnetConfig -Name $gwSubnetName -AddressPrefix 10.0.0.0/24
$vnet = New-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgname -Location $location -AddressPrefix 10.0.0.0/16 -Subnet $gwSubnet
$vnet = Get-AzVirtualNetwork -Name $vnetName -ResourceGroupName $rgname
$gwSubnet = Get-AzVirtualNetworkSubnetConfig -Name $gwSubnetName -VirtualNetwork $vnet
```

## <a name="create-a-public-ip"></a>Skapa en offentlig IP-adress

Skapa en offentlig IP-adress som ska användas med din Application Gateway. 

```azurepowershell
$publicip = New-AzPublicIpAddress -ResourceGroupName $rgname -name $publicIpName -location $location -AllocationMethod Static -sku Standard
```

## <a name="create-the-application-gateway-ip-configuration"></a>Skapa Application Gateway IP-konfiguration

Skapa IP-konfigurationer och frontend-porten. 

```azurepowershell
$gipconfig = New-AzApplicationGatewayIPConfiguration -Name $gipconfigname -Subnet $gwSubnet
$fipconfig = New-AzApplicationGatewayFrontendIPConfig -Name $fipconfigName -PublicIPAddress $publicip
$port = New-AzApplicationGatewayFrontendPort -Name $frontendPortName  -Port 443
```

## <a name="configure-frontend-ssl"></a>Konfigurera klient dels-SSL 

Konfigurera SSL-certifikat för Application Gateway.

```azurepowershell
$password = ConvertTo-SecureString "P@ssw0rd" -AsPlainText -Force
$sslCertPath = $basedir + "/ScenarioTests/Data/ApplicationGatewaySslCert1.pfx"
$sslCert = New-AzApplicationGatewaySslCertificate -Name $sslCertName -CertificateFile $sslCertPath -Password $password
```

## <a name="configure-client-authentication"></a>Konfigurera klientautentisering 

Konfigurera klientautentisering på Application Gateway. Mer information om hur du extraherar certifikat kedjor för betrodda certifikat UTFÄRDAre som används här finns i [extrahera betrodda certifikat för certifikat utfärdare](./mutual-authentication-certificate-management.md).

> [!IMPORTANT]
> Kontrol lera att du laddar upp hela klient certifikat utfärdarens certifikat kedja i en fil och bara en kedja per fil.  

> [!NOTE]
> Vi rekommenderar att du använder TLS 1,2 med ömsesidig autentisering som TLS 1,2 kommer att godkännas i framtiden. 

```azurepowershell
$clientCertFilePath = $basedir + "/ScenarioTests/Data/TrustedClientCertificate.cer"
$trustedClient01 = New-AzApplicationGatewayTrustedClientCertificate -Name $trustedClientCert01Name -CertificateFile $clientCertFilePath
$sslPolicy = New-AzApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName "AppGwSslPolicy20170401S"
$clientAuthConfig = New-AzApplicationGatewayClientAuthConfiguration -VerifyClientCertIssuerDN
$sslProfile01 = New-AzApplicationGatewaySslProfile -Name $sslProfile01Name -SslPolicy $sslPolicy -ClientAuthConfiguration $clientAuthConfig -TrustedClientCertificates $trustedClient01
$listener = New-AzApplicationGatewayHttpListener -Name $listenerName -Protocol Https -SslCertificate $sslCert -FrontendIPConfiguration $fipconfig -FrontendPort $port -SslProfile $sslProfile01
```

## <a name="configure-the-backend-pool-and-settings"></a>Konfigurera backend-poolen och-inställningarna

Konfigurera backend-poolen och inställningar för din Application Gateway. Du kan också konfigurera server delens betrodda rot certifikat för SSL-kryptering från slut punkt till slut punkt.  

```azurepowershell
$certFilePath = $basedir + "/ScenarioTests/Data/ApplicationGatewayAuthCert.cer"
$trustedRoot = New-AzApplicationGatewayTrustedRootCertificate -Name $trustedRootCertName -CertificateFile $certFilePath
$pool = New-AzApplicationGatewayBackendAddressPool -Name $poolName -BackendIPAddresses www.microsoft.com, www.bing.com
$poolSetting = New-AzApplicationGatewayBackendHttpSettings -Name $poolSettingName -Port 443 -Protocol Https -CookieBasedAffinity Enabled -PickHostNameFromBackendAddress -TrustedRootCertificate $trustedRoot
```

## <a name="configure-the-rule"></a>Konfigurera regeln

Konfigurera en regel på din Application Gateway.

```azurepowershell
$rule = New-AzApplicationGatewayRequestRoutingRule -Name $ruleName -RuleType basic -BackendHttpSettings $poolSetting -HttpListener $listener -BackendAddressPool $pool
```

## <a name="set-up-default-ssl-policy-for-future-listeners"></a>Konfigurera standard-SSL-princip för framtida lyssnare

Du har konfigurerat en lyssnare-speciell SSL-princip när du konfigurerar ömsesidig autentisering. I det här steget kan du välja att ange standard-SSL-principen för framtida lyssnare som du skapar. 

```azurepowershell
$sslPolicyGlobal = New-AzApplicationGatewaySslPolicy -PolicyType Predefined -PolicyName "AppGwSslPolicy20170401"
```

## <a name="create-the-application-gateway"></a>Skapa Application Gateway

Distribuera din Application Gateway med allt vi skapade ovan.

```azurepowershell
$sku = New-AzApplicationGatewaySku -Name Standard_v2 -Tier Standard_v2
$appgw = New-AzApplicationGateway -Name $appgwName -ResourceGroupName $rgname -Zone 1,2 -Location $location -BackendAddressPools $pool -BackendHttpSettingsCollection $poolSetting -FrontendIpConfigurations $fipconfig -GatewayIpConfigurations $gipconfig -FrontendPorts $port -HttpListeners $listener -RequestRoutingRules $rule -Sku $sku -SslPolicy $sslPolicyGlobal -TrustedRootCertificate $trustedRoot -AutoscaleConfiguration $autoscaleConfig -TrustedClientCertificates $trustedClient01 -SslProfiles $sslProfile01 -SslCertificates $sslCert
```

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort resurs gruppen, Application Gateway och alla relaterade resurser som använder [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)när de inte längre behövs.

```azurepowershell
Remove-AzResourceGroup -Name $rgname
```

## <a name="renew-expired-client-ca-certificates"></a>Förnya utgångna klient certifikat för certifikat utfärdare

Om klient certifikat utfärdarens certifikat har upphört att gälla kan du uppdatera certifikatet på din gateway genom att följa stegen nedan: 

1. Logga in på Azure
    ```azurepowershell
    Connect-AzAccount
    Select-AzSubscription -Subscription "<sub name>"
    ```
2. Hämta din Application Gateway konfiguration
    ```azurepowershell
    $gateway = Get-AzApplicationGateway -Name "<gateway-name>" -ResourceGroupName "<resource-group-name>"
    ```
3. Ta bort det betrodda klient certifikatet från gatewayen 
    ```azurepowershell
    Remove-AzApplicationGatewayTrustedClientCertificate -Name "<name-of-client-certificate>" -ApplicationGateway $gateway
    ``` 
4. Lägg till det nya certifikatet på gatewayen 
    ```azurepowershell
    Add-AzApplicationGatewayTrustedClientCertificate -ApplicationGateway $gateway -Name "<name-of-new-cert>" -CertificateFile "<path-to-certificate-file>"
    ```
5. Uppdatera gatewayen med det nya certifikatet 
    ```azurepowershell
    Set-AzApplicationGateway -ApplicationGateway $gateway
    ```

## <a name="next-steps"></a>Nästa steg

- [Hantera webbtrafik med en programgateway som använder Azure CLI](./tutorial-manage-web-traffic-cli.md)