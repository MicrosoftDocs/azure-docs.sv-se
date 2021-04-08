---
title: 'Azure-ExpressRoute: Konfigurera MACsec'
description: Den här artikeln hjälper dig att konfigurera MACsec för att skydda anslutningarna mellan dina gräns-routrar och Microsofts gräns routrar.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: how-to
ms.date: 10/22/2019
ms.author: duau
ms.openlocfilehash: 8ee7f42a4b05def7c2239c7a1e5bcef54f6f7e50
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "89395782"
---
# <a name="configure-macsec-on-expressroute-direct-ports"></a>Konfigurera MACsec på ExpressRoute Direct-portar

Den här artikeln hjälper dig att konfigurera MACsec för att skydda anslutningarna mellan dina gräns-routrar och Microsofts gräns routrar med PowerShell.

## <a name="before-you-begin"></a>Innan du börjar

Innan du startar konfigurationen kontrollerar du följande:

* Du förstår [ExpressRoute Direct etablering av arbets flöden](expressroute-erdirect-about.md).
* Du har skapat en [ExpressRoute Direct-port resurs](expressroute-howto-erdirect.md).
* Om du vill köra PowerShell lokalt kontrollerar du att den senaste versionen av Azure PowerShell är installerad på datorn.

### <a name="working-with-azure-powershell"></a>Arbeta med Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/hybrid-az-ps.md)]

[!INCLUDE [expressroute-cloudshell](../../includes/expressroute-cloudshell-powershell-about.md)]

### <a name="sign-in-and-select-the-right-subscription"></a>Logga in och välj rätt prenumeration

För att starta konfigurationen loggar du in på ditt Azure-konto och väljer den prenumeration som du vill använda.

   [!INCLUDE [sign in](../../includes/expressroute-cloud-shell-connect.md)]

## <a name="1-create-azure-key-vault-macsec-secrets-and-user-identity"></a>1. Skapa Azure Key Vault, MACsec hemligheter och användar identitet

1. Skapa en Key Vault-instans för att lagra MACsec hemligheter i en ny resurs grupp.

    ```azurepowershell-interactive
    New-AzResourceGroup -Name "your_resource_group" -Location "resource_location"
    $keyVault = New-AzKeyVault -Name "your_key_vault_name" -ResourceGroupName "your_resource_group" -Location "resource_location" -EnableSoftDelete 
    ```

    Om du redan har ett nyckel valv eller en resurs grupp kan du återanvända dem. Det är dock viktigt att du aktiverar funktionen för [ **mjuk borttagning**](../key-vault/general/soft-delete-overview.md) i ditt befintliga nyckel valv. Om mjuk borttagning inte har Aktiver ATS kan du använda följande kommandon för att aktivera det:

    ```azurepowershell-interactive
    ($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName "your_existing_keyvault").ResourceId).Properties | Add-Member -MemberType "NoteProperty" -Name "enableSoftDelete" -Value "true"
    Set-AzResource -resourceid $resource.ResourceId -Properties $resource.Properties
    ```
2. Skapa en användar identitet.

    ```azurepowershell-interactive
    $identity = New-AzUserAssignedIdentity  -Name "identity_name" -Location "resource_location" -ResourceGroupName "your_resource_group"
    ```

    Om New-AzUserAssignedIdentity inte identifieras som en giltig PowerShell-cmdlet installerar du följande modul (i administratörs läge) och kör kommandot ovan igen.

    ```azurepowershell-interactive
    Install-Module -Name Az.ManagedServiceIdentity
    ```
3. Skapa en anslutnings Associations nyckel (CAK) och ett anslutnings Associations nyckel namn (CKN) och lagra dem i nyckel valvet.

    ```azurepowershell-interactive
    $CAK = ConvertTo-SecureString "your_key" -AsPlainText -Force
    $CKN = ConvertTo-SecureString "your_key_name" -AsPlainText -Force
    $MACsecCAKSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CAK_name" -SecretValue $CAK
    $MACsecCKNSecret = Set-AzKeyVaultSecret -VaultName "your_key_vault_name" -Name "CKN_name" -SecretValue $CKN
    ```
4. Tilldela användar identiteten GET-behörighet.

    ```azurepowershell-interactive
    Set-AzKeyVaultAccessPolicy -VaultName "your_key_vault_name" -PermissionsToSecrets get -ObjectId $identity.PrincipalId
    ```

   Nu kan den här identiteten Hämta hemligheter, till exempel CAK och CKN, från nyckel valvet.
5. Ange att den här användar identiteten ska användas av ExpressRoute.

    ```azurepowershell-interactive
    $erIdentity = New-AzExpressRoutePortIdentity -UserAssignedIdentityId $identity.Id
    ```
 
## <a name="2-configure-macsec-on-expressroute-direct-ports"></a>2. Konfigurera MACsec på ExpressRoute Direct-portar

### <a name="to-enable-macsec"></a>Så här aktiverar du MACsec

Varje ExpressRoute Direct-instans har två fysiska portar. Du kan välja att aktivera MACsec på båda portarna samtidigt eller aktivera MACsec på en port i taget. Genom att göra det en port i taget (genom att växla trafik till en aktiv port samtidigt som den andra porten betjänas) kan du minimera avbrottet om din ExpressRoute Direct redan är i tjänst.

1. Ange MACsec-hemligheter och cipher och koppla användar identiteten till porten så att ExpressRoute kan komma åt MACsec hemligheter om det behövs.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[0]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $MacSecCKNSecret.Id
    $erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $MacSecCAKSecret.Id
    $erDirect.Links[1]. MacSecConfig.Cipher = "GcmAes256"
    $erDirect.identity = $erIdentity
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```
2. Valfritt Om portarna är i läget för administrativ avstängning kan du köra följande kommandon för att ta fram portarna.

    ```azurepowershell-interactive
    $erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
    $erDirect.Links[0].AdminState = "Enabled"
    $erDirect.Links[1].AdminState = "Enabled"
    Set-AzExpressRoutePort -ExpressRoutePort $erDirect
    ```

    I det här läget är MACsec aktiverat på ExpressRoute Direct-portarna på Microsoft-sidan. Om du inte har konfigurerat den på dina gräns enheter kan du fortsätta att konfigurera dem med samma MACsec-hemligheter och-chiffer.

### <a name="to-disable-macsec"></a>Så här inaktiverar du MACsec

Om MACsec inte längre behövs för din ExpressRoute-direkta instans kan du köra följande kommandon för att inaktivera det.

```azurepowershell-interactive
$erDirect = Get-AzExpressRoutePort -ResourceGroupName "your_resource_group" -Name "your_direct_port_name"
$erDirect.Links[0]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[0]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CknSecretIdentifier = $null
$erDirect.Links[1]. MacSecConfig.CakSecretIdentifier = $null
$erDirect.identity = $null
Set-AzExpressRoutePort -ExpressRoutePort $erDirect
```

I det här läget är MACsec inaktiverat på ExpressRoute Direct-portarna på Microsoft-sidan.

### <a name="test-connectivity"></a>Testa anslutning
När du har konfigurerat MACsec (inklusive MACsec Key Update) på dina ExpressRoute Direct-portar, [kontrollerar](expressroute-troubleshooting-expressroute-overview.md) du om BGP-sessionerna för kretsarna är igång. Om du inte har någon krets på portarna ännu, skapar du en först och konfigurerar Azures privata peering eller Microsoft-peering av kretsen. Om MACsec är felkonfigurerat, inklusive MACsec-nyckel matchning mellan dina nätverks enheter och Microsofts nätverks enheter, ser du inte ARP-matchning i Layer 2 och BGP-etablering på nivå 3. Om allt är korrekt konfigurerat bör du se att BGP-vägarna annonseras korrekt i båda riktningarna och ditt program data flöde i enlighet med ExpressRoute.

## <a name="next-steps"></a>Nästa steg
1. [Skapa en ExpressRoute-krets på ExpressRoute Direct](expressroute-howto-erdirect.md)
2. [Länka en ExpressRoute-krets till ett virtuellt Azure-nätverk](expressroute-howto-linkvnet-arm.md)
3. [Verifiera ExpressRoute-anslutningen](expressroute-troubleshooting-expressroute-overview.md)
