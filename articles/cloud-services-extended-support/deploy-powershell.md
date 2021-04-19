---
title: Distribuera en molntjänst (utökat stöd) – PowerShell
description: Distribuera en molntjänst (utökat stöd) med PowerShell
ms.topic: tutorial
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: a7e4e76aa0619d78a91d766a9a43c0b1a02a48d3
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717395"
---
# <a name="deploy-a-cloud-service-extended-support-using-azure-powershell"></a>Distribuera en molntjänst (utökat stöd) med hjälp av Azure PowerShell

Den här artikeln visar hur du använder PowerShell-modulen för att distribuera Cloud Services (utökat stöd) i Azure som har flera roller `Az.CloudService` (WebRole och WorkerRole) och fjärrskrivbordstillägg. 

## <a name="before-you-begin"></a>Innan du börjar

Granska [distributionsförutsättningarna för](deploy-prerequisite.md) Cloud Services (utökad support) och skapa de associerade resurserna. 

## <a name="deploy-a-cloud-services-extended-support"></a>Distribuera en Cloud Services (utökat stöd)
1. Installera Az.CloudService PowerShell-modulen  

    ```powershell
    Install-Module -Name Az.CloudService 
    ```

2. Skapa en ny resursgrupp. Det här steget är valfritt om du använder en befintlig resursgrupp.   

    ```powershell
    New-AzResourceGroup -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

3. Skapa ett lagringskonto och en container som ska användas för att lagra filerna Cloud Service Package (.cspkg) och Service Configuration (.cscfg). Du måste använda ett unikt namn för lagringskontots namn. 

    ```powershell
    $storageAccount = New-AzStorageAccount -ResourceGroupName “ContosOrg” -Name “contosostorageaccount” -Location “East US” -SkuName “Standard_RAGRS” -Kind “StorageV2” 
    $container = New-AzStorageContainer -Name “contosocontainer” -Context $storageAccount.Context -Permission Blob 
    ```

4. Ladda upp cloud service-paketet (cspkg) till lagringskontot.

    ```powershell
    $tokenStartTime = Get-Date 
    $tokenEndTime = $tokenStartTime.AddYears(1) 
    $cspkgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cspkg” -Container “contosocontainer” -Blob “ContosoApp.cspkg” -Context $storageAccount.Context 
    $cspkgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cspkgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cspkgUrl = $cspkgBlob.ICloudBlob.Uri.AbsoluteUri + $cspkgToken 
    ```
 

5.  Ladda upp molntjänstkonfigurationen (cscfg) till lagringskontot. 

    ```powershell
    $cscfgBlob = Set-AzStorageBlobContent -File “./ContosoApp/ContosoApp.cscfg” -Container contosocontainer -Blob “ContosoApp.cscfg” -Context $storageAccount.Context 
    $cscfgToken = New-AzStorageBlobSASToken -Container “contosocontainer” -Blob $cscfgBlob.Name -Permission rwd -StartTime $tokenStartTime -ExpiryTime $tokenEndTime -Context $storageAccount.Context 
    $cscfgUrl = $cscfgBlob.ICloudBlob.Uri.AbsoluteUri + $cscfgToken 
    ```

6. Skapa ett virtuellt nätverk och ett undernät. Det här steget är valfritt om du använder ett befintligt nätverk och undernät. I det här exemplet används ett enda virtuellt nätverk och undernät för både molntjänstroller (WebRole och WorkerRole). 

    ```powershell
    $subnet = New-AzVirtualNetworkSubnetConfig -Name "ContosoWebTier1" -AddressPrefix "10.0.0.0/24" -WarningAction SilentlyContinue 
    $virtualNetwork = New-AzVirtualNetwork -Name “ContosoVNet” -Location “East US” -ResourceGroupName “ContosOrg” -AddressPrefix "10.0.0.0/24" -Subnet $subnet 
    ```
 
7. Skapa en offentlig IP-adress och ange egenskapen DNS-etikett för den offentliga IP-adressen. Cloud Services (utökat stöd) stöder endast [offentliga IP-adresser för Basic](https://docs.microsoft.com/azure/virtual-network/public-ip-addresses#basic) SKU. Offentliga IP-adresser för standard-SKU fungerar inte med Cloud Services.
Om du använder en statisk IP-adress måste du referera till den som en Reserverad IP i servicekonfigurationsfilen (.cscfg) 

    ```powershell
    $publicIp = New-AzPublicIpAddress -Name “ContosIp” -ResourceGroupName “ContosOrg” -Location “East US” -AllocationMethod Dynamic -IpAddressVersion IPv4 -DomainNameLabel “contosoappdns” -Sku Basic 
    ```

8. Skapa ett nätverksprofilobjekt och associera den offentliga IP-adressen med lastbalanserasens frontend. Azure-plattformen skapar automatiskt en "klassisk" SKU-lastbalanseringsresurs i samma prenumeration som molntjänstresursen. Lastbalanseringsresursen är en skrivskyddade resurs i ARM. Uppdateringar av resursen stöds endast via molntjänstdistributionsfilerna (.cscfg & .csdef)

    ```powershell
    $publicIP = Get-AzPublicIpAddress -ResourceGroupName ContosOrg -Name ContosIp  
    $feIpConfig = New-AzCloudServiceLoadBalancerFrontendIPConfigurationObject -Name 'ContosoFe' -PublicIPAddressId $publicIP.Id 
    $loadBalancerConfig = New-AzCloudServiceLoadBalancerConfigurationObject -Name 'ContosoLB' -FrontendIPConfiguration $feIpConfig 
    $networkProfile = @{loadBalancerConfiguration = $loadBalancerConfig} 
    ```
 
9. Skapa ett nyckelvalv. Den Key Vault används för att lagra certifikat som är associerade med rollerna för molntjänst (utökat stöd). Den Key Vault måste finnas i samma region och prenumeration som molntjänsten och ha ett unikt namn. Mer information finns i [Använda certifikat med Azure Cloud Services (utökat stöd).](certificates-and-key-vault.md)

    ```powershell
    New-AzKeyVault -Name "ContosKeyVault” -ResourceGroupName “ContosOrg” -Location “East US” 
    ```

10. Uppdatera Key Vault åtkomstprincip och bevilja certifikatbehörigheter till ditt användarkonto. 

    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -EnabledForDeployment
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -UserPrincipalName 'user@domain.com' -PermissionsToCertificates create,get,list,delete 
    ```

    Du kan också ange åtkomstprincip via ObjectId (som kan hämtas genom att köra `Get-AzADUser` ) 
    
    ```powershell
    Set-AzKeyVaultAccessPolicy -VaultName 'ContosKeyVault' -ResourceGroupName 'ContosOrg' -ObjectId 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx' -PermissionsToCertificates create,get,list,delete 
    ```
 

11. I det här exemplet ska vi lägga till ett själv signerat certifikat i en Key Vault. Certifikatets tumavtryck måste läggas till i Cloud Service Configuration-filen (.cscfg) för distribution i molntjänstroller. 

    ```powershell
    $Policy = New-AzKeyVaultCertificatePolicy -SecretContentType "application/x-pkcs12" -SubjectName "CN=contoso.com" -IssuerName "Self" -ValidityInMonths 6 -ReuseKeyOnRenewal 
    Add-AzKeyVaultCertificate -VaultName "ContosKeyVault" -Name "ContosCert" -CertificatePolicy $Policy 
    ```
 
12. Skapa ett objekt i minnet för en OS-profil. Os-profil anger de certifikat som är associerade med molntjänstroller. Det här är samma certifikat som skapades i föregående steg. 

    ```powershell
    $keyVault = Get-AzKeyVault -ResourceGroupName ContosOrg -VaultName ContosKeyVault 
    $certificate = Get-AzKeyVaultCertificate -VaultName ContosKeyVault -Name ContosCert 
    $secretGroup = New-AzCloudServiceVaultSecretGroupObject -Id $keyVault.ResourceId -CertificateUrl $certificate.SecretId 
    $osProfile = @{secret = @($secretGroup)} 
    ```

13. Skapa ett objekt i minnet för en rollprofil. Rollprofilen definierar en roll-SKU-specifika egenskaper, till exempel namn, kapacitet och nivå. I det här exemplet har vi definierat två roller: frontendRole och backendRole. Rollprofilinformationen ska matcha rollkonfigurationen som definierats i konfigurationsfilen (cscfg) och tjänstdefinitionsfilen (csdef). 

    ```powershell
    $frontendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoFrontend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $backendRole = New-AzCloudServiceRoleProfilePropertiesObject -Name 'ContosoBackend' -SkuName 'Standard_D1_v2' -SkuTier 'Standard' -SkuCapacity 2 
    $roleProfile = @{role = @($frontendRole, $backendRole)} 
    ```

14. (Valfritt) Skapa ett tilläggsprofilobjekt i minnet som du vill lägga till i molntjänsten. I det här exemplet lägger vi till RDP-tillägget. 

    ```powershell
    $credential = Get-Credential 
    $expiration = (Get-Date).AddYears(1) 
    $rdpExtension = New-AzCloudServiceRemoteDesktopExtensionObject -Name 'RDPExtension' -Credential $credential -Expiration $expiration -TypeHandlerVersion '1.2.1' 

    $storageAccountKey = Get-AzStorageAccountKey -ResourceGroupName "ContosOrg" -Name "contosostorageaccount"
    $configFile = "<WAD public configuration file path>"
    $wadExtension = New-AzCloudServiceDiagnosticsExtension -Name "WADExtension" -ResourceGroupName "ContosOrg" -CloudServiceName "ContosCS" -StorageAccountName "contosostorageaccount" -StorageAccountKey $storageAccountKey[0].Value -DiagnosticsConfigurationPath $configFile -TypeHandlerVersion "1.5" -AutoUpgradeMinorVersion $true 
    $extensionProfile = @{extension = @($rdpExtension, $wadExtension)} 
    ```
    Observera att configFile endast ska ha PublicConfig-taggar och ska innehålla ett namnområde enligt följande:
    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <PublicConfig xmlns="http://schemas.microsoft.com/ServiceHosting/2010/10/DiagnosticsConfiguration">
        ...............
    </PublicConfig>
    ```
15. (Valfritt) Definiera Taggar som PowerShell-hashtabell som du vill lägga till i molntjänsten. 

    ```powershell
    $tag=@{"Owner" = "Contoso"} 
    ```

17. Skapa molntjänstdistribution med hjälp av profilobjekt & SAS-URL:er.

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
- Läs [vanliga frågor och svar](faq.md) om Cloud Services (utökad support).
- Distribuera en molntjänst (utökat stöd) med [hjälp av Azure Portal,](deploy-portal.md) [PowerShell,](deploy-powershell.md) [mall](deploy-template.md) [eller Visual Studio](deploy-visual-studio.md).
- Besök [lagringsplatsen Cloud Services exempel (utökad support)](https://github.com/Azure-Samples/cloud-services-extended-support)
