---
title: Självstudie – Skapa anpassade VM-avbildningar med Azure PowerShell
description: I den här självstudien får du lära dig hur du använder Azure PowerShell för att skapa en anpassad avbildning av en Windows-avbildning som lagras i ett Azure-delat avbildnings Galleri.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 05/01/2020
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 145b7300272243099cfccad0c99d8f69e83e6762
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102555830"
---
# <a name="tutorial-create-windows-vm-images-with-azure-powershell"></a>Självstudie: skapa virtuella Windows-avbildningar med Azure PowerShell

Avbildningar kan användas för att starta distributioner och säkerställa konsekvens över flera virtuella datorer. I den här självstudien skapar du en egen specialiserad avbildning av en virtuell Azure-dator med hjälp av PowerShell och lagrar den i ett delat avbildnings Galleri. Lär dig att:

> [!div class="checklist"]
> * Skapa ett Shared Image Gallery
> * Skapa en avbildnings definition
> * Skapa en avbildningsversion
> * Skapa en virtuell dator från en avbildning 
> * Dela ett avbildnings Galleri



## <a name="before-you-begin"></a>Innan du börjar

Stegen nedan beskriver hur du tar en befintlig virtuell dator och omvandlar den till en återanvändbar anpassad avbildning som du kan använda för att skapa nya virtuella datorer.

Du måste ha en befintlig virtuell dator för att kunna utföra exemplet i den här självstudiekursen. Om det behövs kan du se [PowerShell-snabb](quick-create-powershell.md) starten för att skapa en virtuell dator som ska användas för den här självstudien. När du arbetar igenom självstudien ersätter du resurs namnen där det behövs.

## <a name="overview"></a>Översikt

Ett [delat bild galleri](../shared-image-galleries.md) fören klar en anpassad bild delning i hela organisationen. Anpassade avbildningar liknar Marketplace-avbildningar, men du skapar dem själv. Anpassade avbildningar kan användas för startkonfigurationer, till exempel förinläsning av program, programkonfigurationer och andra OS-konfigurationer. 

Med galleriet för delade avbildningar kan du dela dina anpassade VM-avbildningar med andra. Välj vilka bilder du vill dela, vilka regioner du vill göra tillgängliga i och vilka du vill dela dem med. 

Funktionen för delad bild galleri har flera resurs typer:

[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]


## <a name="launch-azure-cloud-shell"></a>Starta Azure Cloud Shell

Azure Cloud Shell är ett interaktivt gränssnitt som du kan använda för att utföra stegen i den här artikeln. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. 

Om du vill öppna Cloud Shell väljer du bara **Prova** från det övre högra hörnet i ett kodblock. Du kan också starta Cloud Shell på en separat webbläsare-flik genom att gå till [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . Kopiera kodblocket genom att välja **Kopiera**, klistra in det i Cloud Shell och kör det genom att trycka på RETUR.

## <a name="get-the-vm"></a>Hämta den virtuella datorn

Du kan se en lista över virtuella datorer som är tillgängliga i en resurs grupp med hjälp av [Get-AzVM](/powershell/module/az.compute/get-azvm). När du känner till namnet på den virtuella datorn och vilken resurs grupp kan du använda `Get-AzVM` igen för att hämta det virtuella datorobjektet och lagra det i en variabel som ska användas senare. Det här exemplet hämtar en virtuell dator med namnet *sourceVM* från resurs gruppen "myResourceGroup" och tilldelar den variabeln *$sourceVM*. 

```azurepowershell-interactive
$sourceVM = Get-AzVM `
   -Name sourceVM `
   -ResourceGroupName myResourceGroup
```

## <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en resursgrupp med kommandot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. I följande exempel skapas en resurs grupp med namnet *myGalleryRG* i regionen *östra* :

```azurepowershell-interactive
$resourceGroup = New-AzResourceGroup `
   -Name 'myGalleryRG' `
   -Location 'EastUS'
```

## <a name="create-an-image-gallery"></a>Skapa ett bild galleri 

Ett avbildnings galleri är den primära resurs som används för att aktivera avbildnings delning. Tillåtna tecken för Galleri namn är versaler eller gemener, siffror, punkter och punkter. Galleri namnet får inte innehålla bindestreck. Galleri namn måste vara unika inom din prenumeration. 

Skapa ett bild galleri med [New-AzGallery](/powershell/module/az.compute/new-azgallery). I följande exempel skapas ett galleri med namnet min *Galleri* i resurs gruppen *myGalleryRG* .

```azurepowershell-interactive
$gallery = New-AzGallery `
   -GalleryName 'myGallery' `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -Description 'Shared Image Gallery for my organization'  
```


## <a name="create-an-image-definition"></a>Skapa en avbildnings definition 

Bild definitioner skapa en logisk gruppering för avbildningar. De används för att hantera information om de avbildnings versioner som skapas i dem. Namn på bild definitioner kan bestå av versaler eller gemener, siffror, punkter, streck och punkter. Mer information om de värden som du kan ange för en bild definition finns i [bild definitioner](../shared-image-galleries.md#image-definitions).

Skapa avbildnings definitionen med [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion). I det här exemplet heter Galleri bilden *myGalleryImage* och skapas för en specialiserad avbildning. 

```azurepowershell-interactive
$galleryImage = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-an-image-version"></a>Skapa en avbildningsversion

Skapa en avbildnings version från en virtuell dator med [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion). 

Tillåtna tecken för bild version är tal och punkter. Talen måste vara inom intervallet för ett 32-bitars heltal. Format: *Major version*. *MinorVersion*. *Korrigering*.

I det här exemplet är avbildnings versionen *1.0.0* och replikeras till både *östra USA* och *södra centrala USA* -datacenter. När du väljer mål regioner för replikering måste du inkludera *käll* regionen som mål för replikering.

Om du vill skapa en avbildnings version från den virtuella datorn använder du `$vm.Id.ToString()` för `-Source` .

```azurepowershell-interactive
   $region1 = @{Name='South Central US';ReplicaCount=1}
   $region2 = @{Name='East US';ReplicaCount=2}
   $targetRegions = @($region1,$region2)

New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $galleryImage.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $resourceGroup.ResourceGroupName `
   -Location $resourceGroup.Location `
   -TargetRegion $targetRegions  `
   -Source $sourceVM.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01'
```

Det kan ta en stund att replikera avbildningen till alla mål regioner.


## <a name="create-a-vm"></a>Skapa en virtuell dator 

När du har en specialiserad avbildning kan du skapa en eller flera nya virtuella datorer. Använda cmdleten [New-AzVM](/powershell/module/az.compute/new-azvm) . Om du vill använda avbildningen använder du `Set-AzVMSourceImage` och ställer in `-Id` till bild Definitions-ID: t ($GalleryImage. ID i det här fallet) så att den alltid använder den senaste avbildnings versionen. 

Ersätt resurs namn efter behov i det här exemplet. 

```azurepowershell-interactive
# Create some variables for the new VM.
$resourceGroup = "myResourceGroup"
$location = "South Central US"
$vmName = "mySpecializedVM"

# Create a resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create the network resources.
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name $vmName -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the image version.
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMSourceImage -Id $galleryImage.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```


## <a name="share-the-gallery"></a>Dela galleriet

Vi rekommenderar att du delar åtkomst på avbildnings Galleri nivån. Använd en e-postadress och cmdleten [Get-AzADUser](/powershell/module/az.resources/get-azaduser) för att hämta objekt-ID: t för användaren och Använd sedan [New-AzRoleAssignment](/powershell/module/Az.Resources/New-AzRoleAssignment) för att ge dem åtkomst till galleriet. Ersätt e-postmeddelandet alinne_montes@contoso.com i det här exemplet med din egen information.

```azurepowershell-interactive
# Get the object ID for the user
$user = Get-AzADUser -StartsWith alinne_montes@contoso.com
# Grant access to the user for our gallery
New-AzRoleAssignment `
   -ObjectId $user.Id `
   -RoleDefinitionName Reader `
   -ResourceName $gallery.Name `
   -ResourceType Microsoft.Compute/galleries `
   -ResourceGroupName $resourceGroup.ResourceGroupName
```
   
## <a name="clean-up-resources"></a>Rensa resurser

När de inte längre behövs kan du använda cmdleten [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) för att ta bort resurs gruppen och alla relaterade resurser:

```azurepowershell-interactive
# Delete the gallery 
Remove-AzResourceGroup -Name myGalleryRG

# Delete the VM
Remove-AzResourceGroup -Name myResoureceGroup
```

## <a name="azure-image-builder"></a>Azure Image Builder

Azure erbjuder också en tjänst som bygger på Packer, [Azure VM Image Builder](../image-builder-overview.md). Du behöver bara beskriva dina anpassningar i en mall så att du kan hantera avbildnings skapandet. 

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du skapat en specialiserad VM-avbildning. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa ett Shared Image Gallery
> * Skapa en avbildnings definition
> * Skapa en avbildningsversion
> * Skapa en virtuell dator från en avbildning 
> * Dela ett avbildnings Galleri

Gå vidare till nästa självstudie om du vill veta mer om hur du skapar virtuella datorer med hög tillgänglighet.

> [!div class="nextstepaction"]
> [Skapa virtuella datorer med hög tillgänglighet](tutorial-availability-sets.md)