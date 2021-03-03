---
title: Använda Azure Image Builder med ett bild galleri för virtuella Windows-datorer (för hands version)
description: Skapa Azures delade Galleri avbildnings versioner med Azure Image Builder och Azure PowerShell.
author: cynthn
ms.author: cynthn
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subervice: image-builder
ms.colletion: windows
ms.openlocfilehash: e8caf9f742217161c60ce90351989999f18adabb
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694095"
---
# <a name="preview-create-a-windows-image-and-distribute-it-to-a-shared-image-gallery"></a>För hands version: skapa en Windows-avbildning och distribuera den till ett delat avbildnings Galleri 

Den här artikeln visar hur du kan använda Azure Image Builder och Azure PowerShell för att skapa en avbildnings version i ett [delat avbildnings Galleri](../shared-image-galleries.md)och sedan distribuera avbildningen globalt. Du kan också göra detta med hjälp av [Azure CLI](../linux/image-builder-gallery.md).

Vi kommer att använda en. JSON-mall för att konfigurera avbildningen. JSON-filen som vi använder är här: [armTemplateWinSIG.jspå](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json). Vi kommer att hämta och redigera en lokal version av mallen, så den här artikeln skrivs med hjälp av den lokala PowerShell-sessionen.

För att distribuera avbildningen till ett delat bild galleri använder mallen [sharedImage](../linux/image-builder-json.md#distribute-sharedimage) som värde för `distribute` avsnittet i mallen.

Azure Image Builder kör automatiskt Sysprep för att generalisera avbildningen, det här är ett allmänt Sysprep-kommando som du kan [åsidosätta](../linux/image-builder-troubleshoot.md#vms-created-from-aib-images-do-not-create-successfully) om det behövs. 

Tänk på hur många gånger du lager anpassningar. Du kan köra Sysprep-kommandot upp till 8 gånger på en enda Windows-avbildning. När du har kört Sysprep 8 gånger måste du återskapa Windows-avbildningen. Mer information finns i [gränser för hur många gånger du kan köra Sysprep](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep). 

> [!IMPORTANT]
> Azure Image Builder är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-features"></a>Registrera funktionerna
Om du vill använda Azure Image Builder i för hands versionen måste du registrera den nya funktionen.

```powershell
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

Kontrol lera status för funktions registreringen.

```powershell
Get-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

Vänta tills `RegistrationState` är `Registered` innan du går vidare till nästa steg.

Kontrol lera leverantörs registreringarna. Se till att varje retur `Registered` .

```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage | Format-table -Property ResourceTypes,RegistrationState 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.Network | Format-table -Property ResourceTypes,RegistrationState
```

Om de inte returnerar `Registered` , använder du följande för att registrera providern:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

Installera PowerShell-moduler:
```powerShell
'Az.ImageBuilder', 'Az.ManagedServiceIdentity' | ForEach-Object {Install-Module -Name $_ -AllowPrerelease}
```

## <a name="create-variables"></a>Skapa variabler

Vi kommer att använda vissa delar av informationen flera gånger, så vi skapar några variabler för att lagra informationen. Ersätt värdena för variablerna, till exempel `username` och `vmpassword` , med din egen information.

```powershell
# Get existing context
$currentAzContext = Get-AzContext

# Get your current subscription ID. 
$subscriptionID=$currentAzContext.Subscription.Id

# Destination image resource group
$imageResourceGroup="aibwinsig"

# Location
$location="westus"

# Image distribution metadata reference name
$runOutputName="aibCustWinManImg02ro"

# Image template name
$imageTemplateName="helloImageTemplateWin02ps"

# Distribution properties object name (runOutput).
# This gives you the properties of the managed image on completion.
$runOutputName="winclientR01"

# Create a resource group for Image Template and Shared Image Gallery
New-AzResourceGroup `
   -Name $imageResourceGroup `
   -Location $location
```


## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>Skapa en användardefinierad identitet och ange behörigheter för resurs gruppen
Image Builder använder den [användar identitet](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md) som tillhandahölls för att mata in avbildningen i Azure-galleriet för delad avbildning (sig). I det här exemplet ska du skapa en Azure-roll definition som innehåller detaljerade åtgärder för att distribuera avbildningen till SIG själv. Roll definitionen tilldelas sedan till användar identiteten.

```powershell
# setup role def names, these need to be unique
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt
$identityName="aibIdentity"+$timeInt

## Add AZ PS module to support AzUserAssignedIdentity
Install-Module -Name Az.ManagedServiceIdentity

# create identity
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName

$identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId
```


### <a name="assign-permissions-for-identity-to-distribute-images"></a>Tilldela behörigheter för identitet för att distribuera avbildningar

Det här kommandot hämtar en mall för Azure Role definition och uppdaterar mallen med de parametrar som angavs tidigare.

```powershell
$aibRoleImageCreationUrl="https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json"
$aibRoleImageCreationPath = "aibRoleImageCreation.json"

# download config
Invoke-WebRequest -Uri $aibRoleImageCreationUrl -OutFile $aibRoleImageCreationPath -UseBasicParsing

((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<rgName>', $imageResourceGroup) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName) | Set-Content -Path $aibRoleImageCreationPath

# create role definition
New-AzRoleDefinition -InputFile  ./aibRoleImageCreation.json

# grant role definition to image builder service principal
New-AzRoleAssignment -ObjectId $identityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"

### NOTE: If you see this error: 'New-AzRoleDefinition: Role definition limit exceeded. No more role definitions can be created.' See this article to resolve:
https://docs.microsoft.com/azure/role-based-access-control/troubleshooting
```


## <a name="create-the-shared-image-gallery"></a>Skapa galleriet för delad avbildning

Om du vill använda Image Builder med ett delat bild galleri måste du ha ett befintligt bild galleri och en bild definition. Image Builder skapar inte bild galleriet och bild definitionen åt dig.

Om du inte redan har ett galleri och en bild definition som ska användas börjar du med att skapa dem. Börja med att skapa ett bild galleri.

```powershell
# Image gallery name
$sigGalleryName= "myIBSIG"

# Image definition name
$imageDefName ="winSvrimage"

# additional replication region
$replRegion2="eastus"

# Create the gallery
New-AzGallery `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup  `
   -Location $location

# Create the image definition
New-AzGalleryImageDefinition `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup `
   -Location $location `
   -Name $imageDefName `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myCompany' `
   -Offer 'WindowsServer' `
   -Sku 'WinSrv2019'
```



## <a name="download-and-configure-the-template"></a>Hämta och konfigurera mallen

Hämta. JSON-mallen och konfigurera den med dina variabler.

```powershell

$templateFilePath = "armTemplateWinSIG.json"

Invoke-WebRequest `
   -Uri "https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json" `
   -OutFile $templateFilePath `
   -UseBasicParsing

(Get-Content -path $templateFilePath -Raw ) `
   -replace '<subscriptionID>',$subscriptionID | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<rgName>',$imageResourceGroup | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<runOutputName>',$runOutputName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<imageDefName>',$imageDefName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<sharedImageGalName>',$sigGalleryName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region1>',$location | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region2>',$replRegion2 | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<imgBuilderId>',$identityNameResourceId) | Set-Content -Path $templateFilePath
```


## <a name="create-the-image-version"></a>Skapa avbildnings versionen

Din mall måste skickas till tjänsten. då laddas alla beroende artefakter, som skript, och lagras i den mellanlagrings resurs gruppen, med *IT_*.

```powershell
New-AzResourceGroupDeployment `
   -ResourceGroupName $imageResourceGroup `
   -TemplateFile $templateFilePath `
   -apiversion "2020-02-14" `
   -imageTemplateName $imageTemplateName `
   -svclocation $location
```

För att skapa den avbildning som du behöver för att köra körningen på mallen.

```powershell
Invoke-AzResourceAction `
   -ResourceName $imageTemplateName `
   -ResourceGroupName $imageResourceGroup `
   -ResourceType Microsoft.VirtualMachineImages/imageTemplates `
   -ApiVersion "2020-02-14" `
   -Action Run
```

Det kan ta en stund att skapa avbildningen och replikera den till båda regionerna. Vänta tills den här delen är klar innan du fortsätter med att skapa en virtuell dator.

Information om alternativ för att automatisera skapandet av avbildnings status finns i [viktigt]
```powershell
Get-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup |
  Select-Object -Property Name, LastRunStatusRunState, LastRunStatusMessage, ProvisioningState
```

## <a name="create-the-vm"></a>Skapa den virtuella datorn

Skapa en virtuell dator från avbildnings versionen som skapades av Azure Image Builder.

Hämta avbildnings versionen som du har skapat.
```powershell
$imageVersion = Get-AzGalleryImageVersion `
   -ResourceGroupName $imageResourceGroup `
   -GalleryName $sigGalleryName `
   -GalleryImageDefinitionName $imageDefName
```

Skapa den virtuella datorn i den andra regionen där avbildningen replikerades.

```powershell
$vmResourceGroup = "myResourceGroup"
$vmName = "myVMfromImage"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup -Name $vmResourceGroup -Location $replRegion2

# Network pieces
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the shared image
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -Id $imageVersion.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $vmResourceGroup -Location $replRegion2 -VM $vmConfig
```

## <a name="verify-the-customization"></a>Verifiera anpassningen
Skapa en fjärr skrivbords anslutning till den virtuella datorn med det användar namn och lösen ord som du angav när du skapade den virtuella datorn. Öppna en kommando tolk i den virtuella datorn och skriv:

```console
dir c:\
```

Du bör se en katalog med namnet `buildActions` som skapades under bild anpassningen.


## <a name="clean-up-resources"></a>Rensa resurser
Om du nu vill försöka anpassa avbildnings versionen för att skapa en ny version av samma avbildning, **hoppar du över det här steget** och fortsätter med att [använda Azure Image Builder för att skapa en annan avbildnings version](image-builder-gallery-update-image-version.md).


Detta tar bort den avbildning som har skapats, tillsammans med alla andra resursfiler. Kontrol lera att du är färdig med distributionen innan du tar bort resurserna.

Ta först bort resurs grupp mal len, annars kommer den mellanlagrings resurs grupp (*IT_*) som används av AIB inte att rensas.

Hämta ResourceID för avbildnings mal len. 

```powerShell
$resTemplateId = Get-AzResource -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14"
```

Ta bort avbildnings mal len.

```powerShell
Remove-AzResource -ResourceId $resTemplateId.ResourceId -Force
```

Ta bort roll tilldelning

```powerShell
Remove-AzRoleAssignment -ObjectId $identityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
```

ta bort definitioner

```powerShell
Remove-AzRoleDefinition -Name "$identityNamePrincipalId" -Force -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
```

ta bort identitet

```powerShell
Remove-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName -Force
```

ta bort resurs gruppen.

```powerShell
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

Information om hur du uppdaterar avbildnings versionen som du skapade finns i [använda Azure Image Builder för att skapa en annan avbildnings version](image-builder-gallery-update-image-version.md).
