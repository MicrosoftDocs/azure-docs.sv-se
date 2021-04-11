---
title: Image Builder – skapa en Windows Virtual Desktop-avbildning
description: Skapa en virtuell Azure-avbildning av Windows Virtual Desktop med Azure Image Builder i PowerShell.
author: danielsollondon
ms.author: danis
ms.reviewer: cynthn
ms.date: 01/27/2021
ms.topic: article
ms.service: virtual-machines-windows
ms.collection: windows
ms.subservice: imaging
ms.openlocfilehash: 69718b219d239ac13e5d932b05a7dd29619adaa3
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105045594"
---
# <a name="create-a-windows-virtual-desktop-image-using-azure-vm-image-builder-and-powershell"></a>Skapa en Windows Virtual Desktop-avbildning med Azure VM Image Builder och PowerShell

Den här artikeln visar hur du skapar en Windows Virtual Desktop-avbildning med följande anpassningar:

* Installerar [FsLogix](https://github.com/DeanCefola/Azure-WVD/blob/master/PowerShell/FSLogixSetup.ps1).
* Köra ett [Windows-skript för optimering av virtuella skriv bord](https://github.com/The-Virtual-Desktop-Team/Virtual-Desktop-Optimization-Tool) från community-lagrings platsen.
* Installera [Microsoft Teams](../../virtual-desktop/teams-on-wvd.md).
* [Starta om](../linux/image-builder-json.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#windows-restart-customizer)
* Kör [Windows Update](../linux/image-builder-json.md?bc=%2fazure%2fvirtual-machines%2fwindows%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json#windows-update-customizer)

Vi visar dig hur du automatiserar detta med hjälp av Azure VM Image Builder och distribuerar avbildningen till ett [delat avbildnings Galleri](../shared-image-galleries.md)där du kan replikera till andra regioner, styra skalan och dela avbildningen i och utanför dina organisationer.


I det här exemplet används en Azure Resource Manager mall med Image Builder-mallen inkapslad i det här exemplet för att förenkla distributionen av en Image Builder-konfiguration. Detta ger dig andra fördelar, till exempel variabler och parameter indata. Du kan också skicka parametrar från kommando raden.

Den här artikeln är avsedd att vara en kopierings-och Inklistrings övning.

> [!NOTE]
> Skripten för att installera apparna finns på [GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/14_Building_Images_WVD). De är endast för illustration och testning och inte för produktions arbets belastningar. 

## <a name="tips-for-building-windows-images"></a>Tips för att skapa Windows-avbildningar 

- VM-storlek – standard storleken för virtuella datorer är en `Standard_D1_v2` , vilket inte är lämpligt för Windows. Använd en `Standard_D2_v2` eller fler.
- I det här exemplet används [PowerShell-anpassare-skripten](../linux/image-builder-json.md). Du måste använda de här inställningarna, annars slutar versionen att svara.

    ```json
      "runElevated": true,
      "runAsSystem": true,
    ```

    Exempel:

    ```json
      {
          "type": "PowerShell",
          "name": "installFsLogix",
          "runElevated": true,
          "runAsSystem": true,
          "scriptUri": "https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/14_Building_Images_WVD/0_installConfFsLogix.ps1"
    ```
- Kommentera din kod – AIB build-loggen (anpassning. log) är mycket utförlig, om du kommenterar skripten med "Write-Host" skickas dessa till loggarna och gör fel sökningen enklare.

    ```PowerShell
     write-host 'AIB Customization: Starting OS Optimizations script'
    ```

- Avslutnings koder – AIB förväntar sig att alla skript returnerar en avslutnings kod på 0, men en slutkod som inte är noll resulterar i att AIB Miss lyckas med anpassningen och att versionen stoppas. Om du har komplexa skript, lägger till instrumentering och sändnings slut koder visas dessa i anpassning. log.

    ```PowerShell
     Write-Host "Exit code: " $LASTEXITCODE
    ```
- Test: testa och testa din kod innan du använder en fristående virtuell dator, kontrol lera att det inte finns några användar meddelanden. du använder rätt behörighet osv.

- Nätverk – `Set-NetAdapterAdvancedProperty` . Detta ställs in i optimerings skriptet, men det går inte att skapa AIB-versionen, eftersom den kopplar bort nätverket, så är det kommenterat. Den är undersökad.

## <a name="prerequisites"></a>Förutsättningar

Du måste ha de Azure PowerShell senaste installations-CmdLetarna installerade, se [här](/powershell/azure/overview) för att installera information.

```PowerShell
# Register for Azure Image Builder Feature
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages

Get-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages

# wait until RegistrationState is set to 'Registered'

# check you are registered for the providers, ensure RegistrationState is set to 'Registered'.
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault

# If they do not saw registered, run the commented out code below.

## Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
## Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
## Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
## Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
```

## <a name="set-up-environment-and-variables"></a>Konfigurera miljö och variabler

```azurepowershell-interactive
# Step 1: Import module
Import-Module Az.Accounts

# Step 2: get existing context
$currentAzContext = Get-AzContext

# destination image resource group
$imageResourceGroup="wvdImageDemoRg"

# location (see possible locations in main docs)
$location="westus2"

# your subscription, this will get your current subscription
$subscriptionID=$currentAzContext.Subscription.Id

# image template name
$imageTemplateName="wvd10ImageTemplate01"

# distribution properties object name (runOutput), i.e. this gives you the properties of the managed image on completion
$runOutputName="sigOutput"

# create resource group
New-AzResourceGroup -Name $imageResourceGroup -Location $location
```

## <a name="permissions-user-identity-and-role"></a>Behörigheter, användar identitet och roll 


 Skapa en användar identitet.

```azurepowershell-interactive
# setup role def names, these need to be unique
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt
$idenityName="aibIdentity"+$timeInt

## Add AZ PS modules to support AzUserAssignedIdentity and Az AIB
'Az.ImageBuilder', 'Az.ManagedServiceIdentity' | ForEach-Object {Install-Module -Name $_ -AllowPrerelease}

# create identity
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName

$idenityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName).Id
$idenityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName).PrincipalId

```

Tilldela behörigheter till identiteten för att distribuera avbildningar. Det här kommandot hämtar och uppdaterar mallen med de parametrar som angavs tidigare.

```azurepowershell-interactive
$aibRoleImageCreationUrl="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json"
$aibRoleImageCreationPath = "aibRoleImageCreation.json"

# download config
Invoke-WebRequest -Uri $aibRoleImageCreationUrl -OutFile $aibRoleImageCreationPath -UseBasicParsing

((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<rgName>', $imageResourceGroup) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName) | Set-Content -Path $aibRoleImageCreationPath

# create role definition
New-AzRoleDefinition -InputFile  ./aibRoleImageCreation.json

# grant role definition to image builder service principal
New-AzRoleAssignment -ObjectId $idenityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
```

> [!NOTE] 
> Om det här felet visas: New-AzRoleDefinition: roll definitions gränsen har överskridits. Det går inte att skapa fler roll definitioner. Se den här artikeln för att lösa problemet: https://docs.microsoft.com/azure/role-based-access-control/troubleshooting .



## <a name="create-the-shared-image-gallery"></a>Skapa galleriet för delad avbildning 

Om du inte redan har ett delat bild galleri måste du skapa ett.

```azurepowershell-interactive
$sigGalleryName= "myaibsig01"
$imageDefName ="win10wvd"

# create gallery
New-AzGallery -GalleryName $sigGalleryName -ResourceGroupName $imageResourceGroup  -Location $location

# create gallery definition
New-AzGalleryImageDefinition -GalleryName $sigGalleryName -ResourceGroupName $imageResourceGroup -Location $location -Name $imageDefName -OsState generalized -OsType Windows -Publisher 'myCo' -Offer 'Windows' -Sku '10wvd'

```

## <a name="configure-the-image-template"></a>Konfigurera avbildnings mal len

I det här exemplet har vi en mall som är klar att ladda ned och uppdatera mallen med de parametrar som anges ovan, men den kommer att installera FsLogix, OS-optimeringar, Microsoft team och köra Windows Update i slutet.

Om du öppnar mallen kan du se den bild som används i käll egenskapen. i det här exemplet använder den en win 10-avbildning med flera sessioner. 

### <a name="windows-10-images"></a>Windows 10-avbildningar
Två nyckel typer du bör vara medveten om: multisession och enskild session.

Flera session-avbildningar är avsedda för användning i pooler. Här är ett exempel på avbildnings information i Azure:

```json
"publisher": "MicrosoftWindowsDesktop",
"offer": "Windows-10",
"sku": "20h2-evd",
"version": "latest"
```

Single session-avbildningar avser individuell användning. Här är ett exempel på avbildnings information i Azure:

```json
"publisher": "MicrosoftWindowsDesktop",
"offer": "Windows-10",
"sku": "19h2-ent",
"version": "latest"
```

Du kan också ändra tillgängliga Win10-avbildningar:

```azurepowershell-interactive
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsDesktop -Offer windows-10
```

## <a name="download-template-and-configure"></a>Ladda ned mall och konfigurera

Nu måste du hämta mallen och konfigurera den för användning.

```azurepowershell-interactive
$templateUrl="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/14_Building_Images_WVD/armTemplateWVD.json"
$templateFilePath = "armTemplateWVD.json"

Invoke-WebRequest -Uri $templateUrl -OutFile $templateFilePath -UseBasicParsing

((Get-Content -path $templateFilePath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<rgName>',$imageResourceGroup) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<region>',$location) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<runOutputName>',$runOutputName) | Set-Content -Path $templateFilePath

((Get-Content -path $templateFilePath -Raw) -replace '<imageDefName>',$imageDefName) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<sharedImageGalName>',$sigGalleryName) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<region1>',$location) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<imgBuilderId>',$idenityNameResourceId) | Set-Content -Path $templateFilePath

```

Om du vill visa [mallen](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/14_Building_Images_WVD/armTemplateWVD.json)kan all kod vara synlig.


## <a name="submit-the-template"></a>Skicka mallen

Din mall måste skickas till tjänsten, detta laddar ned eventuella beroende artefakter (t. ex. skript), verifierar, kontrollerar behörigheter och lagrar dem i den mellanlagrings resurs gruppen, med prefix *IT_*.

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName $imageResourceGroup -TemplateFile $templateFilePath -api-version "2020-02-14" -imageTemplateName $imageTemplateName -svclocation $location

# Optional - if you have any errors running the above, run:
$getStatus=$(Get-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName)
$getStatus.ProvisioningErrorCode 
$getStatus.ProvisioningErrorMessage
```
 
## <a name="build-the-image"></a>Skapa avbildningen
```azurepowershell-interactive
Start-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName -NoWait
```

> [!NOTE]
> Kommandot väntar inte på att Image Builder-tjänsten ska slutföra avbildnings versionen. du kan fråga status nedan.

```azurepowershell-interactive
$getStatus=$(Get-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName)

# this shows all the properties
$getStatus | Format-List -Property *

# these show the status the build
$getStatus.LastRunStatusRunState 
$getStatus.LastRunStatusMessage
$getStatus.LastRunStatusRunSubState
```
## <a name="create-a-vm"></a>Skapa en virtuell dator
Nu när versionen är färdig kan du bygga en virtuell dator från avbildningen. Använd exemplen [här](/powershell/module/az.compute/new-azvm#examples).

## <a name="clean-up"></a>Rensa

Ta bort resurs grupp mal len först, ta inte bara bort hela resurs gruppen, annars rensas inte den mellanlagrings resurs grupp (*IT_*) som används av AIB.

Ta bort avbildnings mal len.

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name wvd10ImageTemplate
```

Ta bort roll tilldelningen.

```azurepowershell-interactive
Remove-AzRoleAssignment -ObjectId $idenityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"

## remove definitions
Remove-AzRoleDefinition -Name "$idenityNamePrincipalId" -Force -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"

## delete identity
Remove-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName -Force
```

Ta bort resurs gruppen.

```azurepowershell-interactive
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>Nästa steg

Du kan prova fler exempel [på GitHub](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts).