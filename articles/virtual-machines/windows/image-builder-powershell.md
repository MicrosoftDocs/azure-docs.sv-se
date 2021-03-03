---
title: Skapa en virtuell Windows-dator med Azure Image Builder med PowerShell
description: Skapa en virtuell Windows-dator med Azure Image Builder PowerShell-modulen.
author: cynthn
ms.author: cynthn
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subervice: image-builder
ms.colletion: windows
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 90d09763f2c9e167d6a0a34adbbc444ebad14c46
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693466"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder-using-powershell"></a>För hands version: skapa en virtuell Windows-dator med Azure Image Builder med PowerShell

Den här artikeln visar hur du kan skapa en anpassad Windows-avbildning med hjälp av PowerShell-modulen för Azure VM Image Builder.

> [!CAUTION]
> Azure Image Builder är för närvarande en offentlig för hands version. Förhandsversionen tillhandahålls utan serviceavtal. Den rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Förutsättningar

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

Om du väljer att använda PowerShell lokalt kräver den här artikeln att du installerar AZ PowerShell-modulen och ansluter till ditt Azure-konto med hjälp av cmdleten [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) . Mer information om hur du installerar AZ PowerShell-modulen finns i [installera Azure PowerShell](/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Även om PowerShell-modulerna **AZ. ImageBuilder** och **AZ. ManagedServiceIdentity** finns i för hands version, måste du installera dem separat med hjälp av `Install-Module` cmdleten med `AllowPrerelease` parametern. När de här PowerShell-modulerna blir allmänt tillgängliga blir de en del av framtida versioner av AZ PowerShell-modulen och är tillgängliga internt inifrån Azure Cloud Shell.

```azurepowershell-interactive
'Az.ImageBuilder', 'Az.ManagedServiceIdentity' | ForEach-Object {Install-Module -Name $_ -AllowPrerelease}
```

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Om du har flera Azure-prenumerationer väljer du lämplig prenumeration där resurserna ska faktureras. Välj en speciell prenumeration med cmdleten [set-AzContext](/powershell/module/az.accounts/set-azcontext) .

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

### <a name="register-features"></a>Registrera funktioner

Om det här är första gången du använder Azure Image Builder under för hands versionen registrerar du den nya **VirtualMachineTemplatePreview** -funktionen.

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.VirtualMachineImages -FeatureName VirtualMachineTemplatePreview
```

Kontrol lera status för funktions registreringen.

> [!NOTE]
> **RegistrationState** kan vara i ett `Registering` tillstånd i flera minuter innan du ändrar till `Registered` . Vänta tills statusen har **registrerats** innan du fortsätter.

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.VirtualMachineImages -FeatureName VirtualMachineTemplatePreview
```

Registrera följande resurs leverantörer för användning med din Azure-prenumeration om de inte redan har registrerats.

- Microsoft.Compute
- Microsoft. nyckel valv
- Microsoft.Storage
- Microsoft.Network
- Microsoft. VirtualMachineImages

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute, Microsoft.KeyVault, Microsoft.Storage, Microsoft.VirtualMachineImages, Microsoft.Network |
  Where-Object RegistrationState -ne Registered |
    Register-AzResourceProvider
```

## <a name="define-variables"></a>Definiera variabler

Du kommer att använda flera olika informations delar flera gånger. Skapa variabler för att lagra informationen.

```azurepowershell-interactive
# Destination image resource group name
$imageResourceGroup = 'myWinImgBuilderRG'

# Azure region
$location = 'WestUS2'

# Name of the image to be created
$imageTemplateName = 'myWinImage'

# Distribution properties of the managed image upon completion
$runOutputName = 'myDistResults'
```

Skapa en variabel för ditt Azure-prenumerations-ID. För att bekräfta att `subscriptionID` variabeln innehåller ditt prenumerations-ID kan du köra den andra raden i följande exempel.

```azurepowershell-interactive
# Your Azure Subscription ID
$subscriptionID = (Get-AzContext).Subscription.Id
Write-Output $subscriptionID
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en [Azure-resurs grupp](../../azure-resource-manager/management/overview.md) med cmdlet: en [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras som en grupp.

I följande exempel skapas en resurs grupp baserat på namnet i `$imageResourceGroup` variabeln i den region som anges i `$location` variabeln. Den här resurs gruppen används för att lagra bild konfigurations mal len artefakt och avbildningen.

```azurepowershell-interactive
New-AzResourceGroup -Name $imageResourceGroup -Location $location
```

## <a name="create-user-identity-and-set-role-permissions"></a>Skapa användar identitet och ange roll behörigheter

Ge Azure Image Builder behörighet att skapa avbildningar i den angivna resurs gruppen med hjälp av följande exempel. Utan den här behörigheten slutförs inte avbildnings Bygg processen.

Skapa variabler för roll definitions-och identitets namn. Dessa värden måste vara unika.

```azurepowershell-interactive
[int]$timeInt = $(Get-Date -UFormat '%s')
$imageRoleDefName = "Azure Image Builder Image Def $timeInt"
$identityName = "myIdentity$timeInt"
```

Skapa en användar identitet.

```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName
```

Lagra identitets resursen och ägar-ID: n i variabler.

```azurepowershell-interactive
$identityNameResourceId = (Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId = (Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId
```

### <a name="assign-permissions-for-identity-to-distribute-images"></a>Tilldela behörigheter för identitet för att distribuera avbildningar

Ladda ned. JSON-konfigurationsfil och ändra den baserat på inställningarna som definierats i den här artikeln.

```azurepowershell-interactive
$myRoleImageCreationUrl = 'https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json'
$myRoleImageCreationPath = "$env:TEMP\myRoleImageCreation.json"

Invoke-WebRequest -Uri $myRoleImageCreationUrl -OutFile $myRoleImageCreationPath -UseBasicParsing

$Content = Get-Content -Path $myRoleImageCreationPath -Raw
$Content = $Content -replace '<subscriptionID>', $subscriptionID
$Content = $Content -replace '<rgName>', $imageResourceGroup
$Content = $Content -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName
$Content | Out-File -FilePath $myRoleImageCreationPath -Force
```

Skapa roll definitionen.

```azurepowershell-interactive
New-AzRoleDefinition -InputFile $myRoleImageCreationPath
```

Tilldela roll definitionen till tjänstens huvud namn för Image Builder.

```azurepowershell-interactive
$RoleAssignParams = @{
  ObjectId = $identityNamePrincipalId
  RoleDefinitionName = $imageRoleDefName
  Scope = "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
}
New-AzRoleAssignment @RoleAssignParams
```

> [!NOTE]
> Om du får felet: "_New-AzRoleDefinition: roll definitions gränsen har överskridits. Det går inte att skapa fler roll definitioner._ mer information finns i [FELSÖKA Azure RBAC](../../role-based-access-control/troubleshooting.md).

## <a name="create-a-shared-image-gallery"></a>Skapa ett Shared Image Gallery

Skapa galleriet.

```azurepowershell-interactive
$myGalleryName = 'myImageGallery'
$imageDefName = 'winSvrImages'

New-AzGallery -GalleryName $myGalleryName -ResourceGroupName $imageResourceGroup -Location $location
```

Skapa en Galleri definition.

```azurepowershell-interactive
$GalleryParams = @{
  GalleryName = $myGalleryName
  ResourceGroupName = $imageResourceGroup
  Location = $location
  Name = $imageDefName
  OsState = 'generalized'
  OsType = 'Windows'
  Publisher = 'myCo'
  Offer = 'Windows'
  Sku = 'Win2019'
}
New-AzGalleryImageDefinition @GalleryParams
```

## <a name="create-an-image"></a>Skapa en avbildning

Skapa ett käll objekt för Azure Image Builder. Se [hitta Windows VM-avbildningar på Azure Marketplace med Azure PowerShell](./cli-ps-findimage.md) för giltiga parameter värden.

```azurepowershell-interactive
$SrcObjParams = @{
  SourceTypePlatformImage = $true
  Publisher = 'MicrosoftWindowsServer'
  Offer = 'WindowsServer'
  Sku = '2019-Datacenter'
  Version = 'latest'
}
$srcPlatform = New-AzImageBuilderSourceObject @SrcObjParams
```

Skapa ett Azure Image Builder-distributörs objekt.

```azurepowershell-interactive
$disObjParams = @{
  SharedImageDistributor = $true
  ArtifactTag = @{tag='dis-share'}
  GalleryImageId = "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup/providers/Microsoft.Compute/galleries/$myGalleryName/images/$imageDefName"
  ReplicationRegion = $location
  RunOutputName = $runOutputName
  ExcludeFromLatest = $false
}
$disSharedImg = New-AzImageBuilderDistributorObject @disObjParams
```

Skapa ett anpassnings objekt för Azure Image Builder.

```azurepowershell-interactive
$ImgCustomParams01 = @{
  PowerShellCustomizer = $true
  CustomizerName = 'settingUpMgmtAgtPath'
  RunElevated = $false
  Inline = @("mkdir c:\\buildActions", "mkdir c:\\buildArtifacts", "echo Azure-Image-Builder-Was-Here  > c:\\buildActions\\buildActionsOutput.txt")
}
$Customizer01 = New-AzImageBuilderCustomizerObject @ImgCustomParams01
```

Skapa ett andra anpassnings objekt för Azure Image Builder.

```azurepowershell-interactive
$ImgCustomParams02 = @{
  FileCustomizer = $true
  CustomizerName = 'downloadBuildArtifacts'
  Destination = 'c:\\buildArtifacts\\index.html'
  SourceUri = 'https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html'
}
$Customizer02 = New-AzImageBuilderCustomizerObject @ImgCustomParams02
```

Skapa en Azure Image Builder-mall.

```azurepowershell-interactive
$ImgTemplateParams = @{
  ImageTemplateName = $imageTemplateName
  ResourceGroupName = $imageResourceGroup
  Source = $srcPlatform
  Distribute = $disSharedImg
  Customize = $Customizer01, $Customizer02
  Location = $location
  UserAssignedIdentityId = $identityNameResourceId
}
New-AzImageBuilderTemplate @ImgTemplateParams
```

När det är klart returneras ett meddelande och en konfigurations mall för avbildnings byggare skapas i `$imageResourceGroup` .

Du kan använda följande exempel för att avgöra om processen har skapats av en mall.

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup |
  Select-Object -Property Name, LastRunStatusRunState, LastRunStatusMessage, ProvisioningState
```

I bakgrunden skapar Image Builder också en resurs grupp för mellanlagring i din prenumeration. Den här resurs gruppen används för avbildnings versionen. Formatet är i formatet: `IT_<DestinationResourceGroup>_<TemplateName>` .

> [!WARNING]
> Ta inte bort den mellanlagrings resurs gruppen direkt. Om du tar bort bild mal len artefakt, så kommer den mellanlagrings resurs gruppen att tas bort.

Om tjänsten rapporterar ett problem under överföringen av avbildnings konfigurations mal len:

- Se [fel sökning av fel i Azure VM Image build (AIB)](../linux/image-builder-troubleshoot.md).
- Ta bort mallen med hjälp av följande exempel innan du försöker igen.

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup
```

## <a name="start-the-image-build"></a>Starta Image-versionen

Skicka avbildnings konfigurationen till tjänsten VM Image Builder.

```azurepowershell-interactive
Start-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName
```

Vänta tills Bygg processen för avbildningen har slutförts. Det här steget kan ta upp till en timme.

Om du stöter på fel kan du läsa [fel sökning av problem med Azure VM Image build (AIB)](../linux/image-builder-troubleshoot.md).

## <a name="create-a-vm"></a>Skapa en virtuell dator

Lagra inloggnings uppgifter för den virtuella datorn i en variabel. Lösen ordet måste vara komplext.

```azurepowershell-interactive
$Cred = Get-Credential
```

Skapa den virtuella datorn med den avbildning som du har skapat.

```azurepowershell-interactive
$ArtifactId = (Get-AzImageBuilderRunOutput -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup).ArtifactId

New-AzVM -ResourceGroupName $imageResourceGroup -Image $ArtifactId -Name myWinVM01 -Credential $Cred
```

## <a name="verify-the-customizations"></a>Verifiera anpassningarna

Skapa en fjärr skrivbords anslutning till den virtuella datorn med det användar namn och lösen ord som du angav när du skapade den virtuella datorn. I den virtuella datorn öppnar du PowerShell och kör `Get-Content` som visas i följande exempel:

```azurepowershell-interactive
Get-Content -Path C:\buildActions\buildActionsOutput.txt
```

Du bör se utdata baserat på innehållet i filen som skapades under bild anpassnings processen.

```Output
Azure-Image-Builder-Was-Here
```

I samma PowerShell-session kontrollerar du att den andra anpassningen har slutförts genom att leta efter filen `c:\buildArtifacts\index.html` som visas i följande exempel:

```azurepowershell-interactive
Get-ChildItem c:\buildArtifacts\
```

Resultatet bör vara en katalog lista som visar filen som hämtats under bild anpassnings processen.

```Output
    Directory: C:\buildArtifacts

Mode                 LastWriteTime         Length Name
----                 -------------         ------ ----
-a---          29/01/2021    10:04            276 index.html
```


## <a name="clean-up-resources"></a>Rensa resurser

Om resurserna som skapas i den här artikeln inte behövs kan du ta bort dem genom att köra följande exempel.

### <a name="delete-the-image-builder-template"></a>Ta bort Image Builder-mallen

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName
```

### <a name="delete-the-image-resource-group"></a>Ta bort avbildnings resurs gruppen

> [!CAUTION]
> I följande exempel tas den angivna resurs gruppen och alla resurser som ingår i den bort.
> Om resurser utanför omfånget för den här artikeln finns i den angivna resurs gruppen kommer de också att tas bort.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $imageResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Mer information om komponenterna i. JSON-filen som används i den här artikeln finns i [referens för Image Builder-mallar](../linux/image-builder-json.md).
