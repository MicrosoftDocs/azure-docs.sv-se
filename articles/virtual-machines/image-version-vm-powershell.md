---
title: Skapa en avbildning från en virtuell dator
description: Lär dig hur du använder Azure PowerShell för att skapa en avbildning i ett delat avbildnings Galleri från en befintlig virtuell dator i Azure.
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: imaging
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: a7b8cb10f75d7a99198ddfdc1a1bbef3c34a03da
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98685114"
---
# <a name="create-an-image-from-a-vm"></a>Skapa en avbildning från en virtuell dator

Om du har en befintlig virtuell dator som du vill använda för att göra flera identiska virtuella datorer kan du använda den virtuella datorn för att skapa en avbildning i ett delat avbildnings galleri med hjälp av Azure PowerShell. Du kan också skapa en avbildning från en virtuell dator med hjälp av [Azure CLI](image-version-vm-cli.md).

Du kan avbilda en avbildning från både [specialiserade och generaliserade](./shared-image-galleries.md#generalized-and-specialized-images) virtuella datorer med hjälp av Azure PowerShell. 

Bilder i ett bild galleri har två komponenter som vi kommer att skapa i det här exemplet:
- En **bild definition** innehåller information om avbildningen och kraven för att använda den. Detta inkluderar om avbildningen är Windows eller Linux, specialiserad eller generaliserad, viktig information och lägsta och högsta minnes krav. Det är en definition av en typ av bild. 
- En **avbildnings version** är vad som används för att skapa en virtuell dator när du använder ett delat avbildnings Galleri. Du kan ha flera versioner av en avbildning efter behov för din miljö. När du skapar en virtuell dator används avbildnings versionen för att skapa nya diskar för den virtuella datorn. Avbildnings versioner kan användas flera gånger.


## <a name="before-you-begin"></a>Innan du börjar

Du måste ha ett befintligt delat avbildnings galleri och en befintlig virtuell dator i Azure för att kunna använda som källa för att slutföra den här artikeln. 

Om den virtuella datorn har en kopplad datadisk får data disk storleken inte vara större än 1 TB.

Ersätt resurs namnen där det behövs när du arbetar i den här artikeln.


## <a name="get-the-gallery"></a>Hämta galleriet

Du kan visa alla gallerier och bild definitioner efter namn. Resultaten är i formatet `gallery\image definition\image version` .

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

När du har hittat rätt galleri-och bild definitioner skapar du variabler som du kan använda senare. I det här exemplet hämtas galleriet till galleriet som heter *Galleri* i resurs gruppen *myResourceGroup* .

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myResourceGroup
```

## <a name="get-the-vm"></a>Hämta den virtuella datorn

Du kan se en lista över virtuella datorer som är tillgängliga i en resurs grupp med hjälp av [Get-AzVM](/powershell/module/az.compute/get-azvm). När du känner till namnet på den virtuella datorn och vilken resurs grupp det finns i kan du använda `Get-AzVM` igen för att hämta det virtuella datorobjektet och lagra det i en variabel som ska användas senare. Det här exemplet hämtar en virtuell dator med namnet *sourceVM* från resurs gruppen "myResourceGroup" och tilldelar den variabeln *$sourceVm*. 

```azurepowershell-interactive
$sourceVm = Get-AzVM `
   -Name sourceVM `
   -ResourceGroupName myResourceGroup
```

Det är en bra idé att stop\deallocate den virtuella datorn innan du skapar en avbildning med hjälp av [Stop-AzVM](/powershell/module/az.compute/stop-azvm).

```azurepowershell-interactive
Stop-AzVM `
   -ResourceGroupName $sourceVm.ResourceGroupName `
   -Name $sourceVm.Name `
   -Force
```

## <a name="create-an-image-definition"></a>Skapa en avbildnings definition 

Bild definitioner skapa en logisk gruppering för avbildningar. De används för att hantera information om avbildningen. Namn på bild definitioner kan bestå av versaler eller gemener, siffror, punkter, streck och punkter. 

När du gör en avbildnings definition ser du till att har all rätt information. Om du generaliserar den virtuella datorn (med Sysprep för Windows eller waagent för Linux) bör du skapa en avbildnings definition med hjälp av `-OsState generalized` . Om du inte generaliserade den virtuella datorn skapar du en avbildnings definition med hjälp av `-OsState specialized` .

Mer information om de värden som du kan ange för en bild definition finns i [bild definitioner](./shared-image-galleries.md#image-definitions).

Skapa avbildnings definitionen med [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion). 

I det här exemplet heter avbildnings definitionen *myImageDefinition* och är för en SPECIALISERAD virtuell dator som kör Windows. Om du vill skapa en definition för avbildningar som använder Linux använder du `-OsType Linux` . 

```azurepowershell-interactive
$imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState specialized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```


## <a name="create-an-image-version"></a>Skapa en avbildningsversion

Skapa en avbildnings version med [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion). 

Tillåtna tecken för bild version är tal och punkter. Talen måste vara inom intervallet för ett 32-bitars heltal. Format: *Major version*. *MinorVersion*. *Korrigering*.

I det här exemplet är avbildnings versionen *1.0.0* och replikeras till både *västra centrala* USA och *södra centrala* Data Center. Kom ihåg att du även måste inkludera *käll* regionen som mål för replikering när du väljer mål regioner för replikering.

Om du vill skapa en avbildnings version från den virtuella datorn använder du `$vm.Id.ToString()` för `-SourceImageId` .

```azurepowershell-interactive
   $region1 = @{Name='South Central US';ReplicaCount=1}
   $region2 = @{Name='East US';ReplicaCount=2}
   $targetRegions = @($region1,$region2)

$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name`
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -TargetRegion $targetRegions  `
   -SourceImageId $sourceVm.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-01' `  
   -asJob 
```

Det kan ta en stund att replikera avbildningen till alla mål regioner, så vi har skapat ett jobb så att vi kan spåra förloppet. Om du vill se förloppet för jobbet skriver du `$job.State` .

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> Du måste vänta tills avbildnings versionen är fullständigt slutförd och replikerad innan du kan använda samma hanterade avbildning för att skapa en annan avbildnings version.
>
> Du kan också lagra din avbildning i Premium Storage genom att lägga till `-StorageAccountType Premium_LRS` eller [zonens redundant lagring](../storage/common/storage-redundancy.md) genom att lägga till `-StorageAccountType Standard_ZRS` när du skapar avbildnings versionen.
>

## <a name="next-steps"></a>Nästa steg

När du har kontrollerat att den nya avbildnings versionen fungerar som den ska kan du skapa en virtuell dator. Skapa en virtuell dator från en [specialiserad avbildnings version](vm-specialized-image-version-powershell.md) eller en [generaliserad avbildnings version](vm-generalized-image-version-powershell.md).

Information om hur du anger information om inköps planer finns i [tillhandahålla information om inköps plan för Azure Marketplace när du skapar avbildningar](marketplace-images.md).