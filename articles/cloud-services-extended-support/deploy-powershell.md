---
title: Distribuera en moln tjänst (utökad support) – PowerShell
description: Distribuera en moln tjänst (utökad support) med PowerShell
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 08a8dde815a6dea5d69e5e2a385cbaa03fba681a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99832702"
---
# <a name="deploy-a-cloud-service-extended-support-using-azure-powershell"></a>Distribuera en moln tjänst (utökad support) med Azure PowerShell

Den här artikeln visar hur du använder `Az.CloudService` PowerShell-modulen för att distribuera Cloud Services (utökad support) i Azure som har flera roller (webrole och WorkerRole) och fjärr skrivbords tillägg. 

> [!IMPORTANT]
> Cloud Services (utökad support) är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="before-you-begin"></a>Innan du börjar

Granska [distributions kraven](deploy-prerequisite.md) för Cloud Services (utökad support) och skapa de associerade resurserna. 

## <a name="deploy-a-cloud-services-extended-support"></a>Distribuera en Cloud Services (utökad support)
1. Installera AZ. CloudService PowerShell-modulen  

    ```powershell
    Install-Module -Name Az.CloudService 
    ```

2. Skapa en ny resursgrupp. Det här steget är valfritt om du använder en befintlig resurs grupp.   

    ```powershell
    New-AzResourceGroup -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

3. Skapa ett lagrings konto och en behållare som ska användas för att lagra moln tjänst paketets (. cspkg) och tjänst konfigurations filen (. cscfg). Du måste använda ett unikt namn för lagrings konto namnet. 

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName “ContosOrg” -Name “contosostorageaccount” -Location “East US” -SkuName “Standard_RAGRS” -Kind “StorageV2” 
    $container = New-AzStorageContainer -Name “contosocontainer” -Context $storageAccount.Context -Permission Blob 
    ```

4. Överför ditt Cloud Service-paket (cspkg) till lagrings kontot.

    ```powershell
    $tokenStartTime = Get-Date 
    $tokenEndTime = $tokenStartTime.AddYears(1) 
    $cspkgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cspkg” -Container “contosocontainer” -Blob “ContosoApp.cspkg” -Context $storageAccount.Context 
    $cspkgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cspkgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cspkgUrl = $cspkgBlob.ICloudBlob.Uri.AbsoluteUri + $cspkgToken 
    ```
 

5.  Överför din moln tjänst konfiguration (cscfg) till lagrings kontot. 

    ```powershell
    $cscfgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cscfg” -Container contosocontainer -Blob “ContosoApp.cscfg” -Context $storageAccount.Context 
    $cscfgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cscfgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cscfgUrl = $cscfgBlob.ICloudBlob.Uri.AbsoluteUri + $cscfgToken 
    ```

6. Skapa ett virtuellt nätverk och ett undernät. Det här steget är valfritt om du använder ett befintligt nätverk och undernät. I det här exemplet används ett enda virtuellt nätverk och undernät för både moln tjänst roller (webrole och WorkerRole). 

    ```powershell
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "ContosoWebTier1" -AddressPrefix "10.0.0.0/24" -WarningAction SilentlyContinue 
    $virtualNetwork = New-AzVirtualNetwork -Name “ContosoVNet” -Location “East US” -ResourceGroupName “ContosOrg” -AddressPrefix "10.0.0.0/24" -Subnet $subnet 
    ```
 
7. Skapa en offentlig IP-adress och (valfritt) ange egenskapen DNS-etikett för den offentliga IP-adressen. Om du använder en statisk IP-adress måste den refereras till som en Reserverad IP i tjänst konfigurations filen.  

    ```powershell
    $publicIp = New-AzPublicIpAddress -Name “ContosIp” -ResourceGroupName “ContosOrg” -Location “East US” -AllocationMethod Dynamic -IpAddressVersion IPv4 -DomainNameLabel “contosoappdns” -Sku Basic 
    ```

8. Skapa objektet nätverks profil och associera den offentliga IP-adressen till klient delen för den plattform som har skapat belastningsutjämnaren.  

    ```powershell
    $publicIP = Get-AzPublicIpAddress -ResourceGroupName ContosOrg -Name ContosIp  
    $feIpConfig = New-AzCloudServiceLoadBalancerFrontendIPConfigurationObject -Name 'ContosoFe' -PublicIPAddressId $publicIP.Id 
    $loadBalancerConfig = New-AzCloudServiceLoadBalancerConfigurationObject -Name 'ContosoLB' -FrontendIPConfiguration $feIpConfig 
    $networkProfile = @{loadBalancerConfiguration = $loadBalancerConfig} 
    ```
 
9. Skapa ett nyckelvalv. Den här Key Vault kommer att användas för att lagra certifikat som är associerade med moln tjänsten (utökade stöd) roller. Se till att du har aktiverat "åtkomst principer" (i portalen) för åtkomst till "Azure Virtual Machines for Deployment" och "Azure Resource Manager for Template Deployment". Key Vault måste finnas i samma region och prenumeration som moln tjänsten och ha ett unikt namn. Mer information finns i [använda certifikat med Azure Cloud Services (utökad support)](certificates-and-key-vault.md).

    ```powershell
    New-AzKeyVault -Name "ContosKeyVault” -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

10. Uppdatera Key Vault åtkomst princip och bevilja certifikat behörigheter till ditt användar konto. 

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -UserPrincipalName 'user@domain.com' -PermissionsToCertificates create,get,list,delete 
    ```

    Du kan också ange åtkomst princip via ObjectId (som kan hämtas genom att köra `Get-AzADUser` ) 
    
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -ObjectId 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' -PermissionsToCertificates create,get,list,delete 
    ```
 

11. I det här exemplet ska vi lägga till ett självsignerat certifikat till en Key Vault. Tumavtryck för certifikatet måste läggas till i filen för moln tjänst konfiguration (. cscfg) för distribution i moln tjänst roller. 

    ```powershell
    $Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal 
    Add-AzKeyVaultCertificate -VaultName "ContosKeyVault" -Name "ContosCert" -CertificatePolicy $Policy 
    ```
 
12. Skapa ett OS-profil i minnes objekt. OS-profil anger de certifikat som är kopplade till moln tjänst roller. Detta är samma certifikat som skapades i föregående steg. 

    ```powershell
    $keyVault = Get-AzKeyVault -ResourceGroupName ContosOrg -VaultName ContosKeyVault 
    $certificate = Get-AzKeyVaultCertificate -VaultName ContosKeyVault -Name ContosCert 
    $secretGroup = New-AzCloudServiceVaultSecretGroupObject -Id $keyVault.ResourceId -CertificateUrl $certificate.SecretId 
    $osProfile = @{secret = @($secretGroup)} 
    ```

13. Skapa en roll profil i-minnes objekt. Roll profilen definierar ett SKU-/regionsspecifika egenskaper, till exempel namn, kapacitet och nivå. I det här exemplet har vi definierat två roller: frontendRole och backendRole. Roll profil informationen bör överensstämma med roll konfigurationen som definierats i konfigurations filen (cscfg) och tjänst definitions filen (csdef). 

    ```powershell
    $frontendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoFrontend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $backendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoBackend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $roleProfile = @{role = @($frontendRole, $backendRole)} 
    ```

14. Valfritt Skapa ett tilläggs profil i minnes objekt som du vill lägga till i din moln tjänst. I det här exemplet ska vi lägga till RDP-tillägg. 

    ```powershell
    $credential = Get-Credential 
    $expiration = (Get-Date).AddYears(1) 
    $rdpExtension = New-AzCloudServiceRemoteDesktopExtensionObject -Name 'RDPExtension' -Credential $credential -Expiration $expiration -TypeHandlerVersion '1.2.1' 

    $storageAccountKey = Get-AzStorageAccountKey -ResourceGroupName "ContosOrg" -Name "contosostorageaccount"
    $configFile = "<WAD public configuration file path>"
    $wadExtension = New-AzCloudServiceDiagnosticsExtension -Name "WADExtension" -ResourceGroupName "ContosOrg" -CloudServiceName "ContosCS" -StorageAccountName "contosostorageaccount" -StorageAccountKey $storageAccountKey[0].Value -DiagnosticsConfigurationPath $configFile -TypeHandlerVersion "1.5" -AutoUpgradeMinorVersion $true 
    $extensionProfile = @{extension = @($rdpExtension, $wadExtension)} 
    ```
    Observera att configFile endast ska ha PublicConfig-Taggar och ska innehålla ett namn område enligt följande:
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        ...............
    </PublicConfig>
    ```
15. Valfritt Definiera taggar som en PowerShell-hash-tabell som du vill lägga till i din moln tjänst. 

    ```powershell
    $tag=@{"Owner" = "Contoso"} 
    ```

17. Skapa moln tjänst distribution med hjälp av profil objekt & SAS-URL: er.

    ```powershell
    $cloudService = New-AzCloudService ` 
    -Name “ContosoCS” ` 
    -ResourceGroupName “ContosOrg” ` 
    -Location “East US” ` 
    -PackageUrl $cspkgUrl ` 
    -ConfigurationUrl $cscfgUrl ` 
    -UpgradeMode 'Auto' ` 
    -RoleProfile $roleProfile ` 
    -NetworkProfile $networkProfile  ` 
    -ExtensionProfile $extensionProfile ` 
    -OSProfile $osProfile `
    -Tag $tag 
    ```

## <a name="next-steps"></a>Nästa steg 
- Läs igenom [vanliga frågor och svar](faq.md) om Cloud Services (utökad support).
- Distribuera en moln tjänst (utökad support) med hjälp av [Azure Portal](deploy-portal.md), [PowerShell](deploy-powershell.md), [mall](deploy-template.md) eller [Visual Studio](deploy-visual-studio.md).
- Besök den [Cloud Services (utökad support) exempel lagrings plats](https://github.com/Azure-Samples/cloud-services-extended-support)
