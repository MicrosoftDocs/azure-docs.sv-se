---
title: Klona en hanterad avbildning till ett delat avbildnings Galleri
description: Lär dig hur du använder Azure PowerShell för att klona en hanterad avbildning till en avbildnings version i ett delat avbildnings Galleri.
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: cec4f258cfaa0584c24f2cfc92ec1a536f6277cc
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102556988"
---
# <a name="clone-a-managed-image-to-a-shared-image-gallery-image"></a>Klona en hanterad avbildning till en bild för ett delat bild galleri

Om du har en befintlig hanterad avbildning som du vill klona och flytta till ett delat bild galleri, kan du skapa en avbildning av en delad avbildning direkt från den hanterade avbildningen. När du har testat din nya avbildning kan du ta bort den hanterade käll avbildningen. Du kan också migrera från en hanterad avbildning till ett delat avbildnings galleri med hjälp av [Azure CLI](image-version-managed-image-cli.md).

Bilder i ett bild galleri har två komponenter som vi kommer att skapa i det här exemplet:
- En **bild definition** innehåller information om avbildningen och kraven för att använda den. Detta inkluderar om avbildningen är Windows eller Linux, specialiserad eller generaliserad, viktig information och lägsta och högsta minnes krav. Det är en definition av en typ av bild. 
- En **avbildnings version** är vad som används för att skapa en virtuell dator när du använder ett delat avbildnings Galleri. Du kan ha flera versioner av en avbildning efter behov för din miljö. När du skapar en virtuell dator används avbildnings versionen för att skapa nya diskar för den virtuella datorn. Avbildnings versioner kan användas flera gånger.


## <a name="before-you-begin"></a>Innan du börjar

Du måste ha en befintlig hanterad avbildning för att kunna slutföra den här artikeln. Om den hanterade avbildningen innehåller en datadisk får data disk storleken inte vara större än 1 TB.

När du arbetar med den här artikeln ersätter du resurs gruppen och VM-namnen där det behövs.

## <a name="get-the-gallery"></a>Hämta galleriet

Du kan visa alla gallerier och bild definitioner efter namn. Resultaten är i formatet `gallery\image definition\image version` .

```azurepowershell-interactive
Get-AzResource -ResourceType Microsoft.Compute/galleries | Format-Table
```

När du har hittat rätt Galleri skapar du en variabel som ska användas senare. I det här exemplet hämtas galleriet till galleriet som heter *Galleri* i resurs gruppen *myResourceGroup* .

```azurepowershell-interactive
$gallery = Get-AzGallery `
   -Name myGallery `
   -ResourceGroupName myResourceGroup
```


## <a name="create-an-image-definition"></a>Skapa en avbildnings definition 

Bild definitioner skapa en logisk gruppering för avbildningar. De används för att hantera information om avbildningen. Namn på bild definitioner kan bestå av versaler eller gemener, siffror, punkter, streck och punkter. 

När du gör en avbildnings definition ser du till att har all rätt information. Eftersom hanterade avbildningar alltid är generaliserade bör du ställa in `-OsState generalized` . 

Mer information om de värden som du kan ange för en bild definition finns i [bild definitioner](./shared-image-galleries.md#image-definitions).

Skapa avbildnings definitionen med [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion). I det här exemplet heter avbildnings definitionen *myImageDefinition* och är för ett generaliserat Windows-operativsystem. Använd om du vill skapa en definition för avbildningar med hjälp av ett Linux-operativsystem `-OsType Linux` . 

```azurepowershell-interactive
$imageDefinition = New-AzGalleryImageDefinition `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -Name 'myImageDefinition' `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myPublisher' `
   -Offer 'myOffer' `
   -Sku 'mySKU'
```

## <a name="get-the-managed-image"></a>Hämta den hanterade avbildningen

Du kan se en lista över avbildningar som är tillgängliga i en resurs grupp med hjälp av [Get-AzImage](/powershell/module/az.compute/get-azimage). När du känner till avbildningens namn och vilken resurs grupp det finns i kan du använda `Get-AzImage` igen för att hämta objektet och lagra det i en variabel som ska användas senare. Det här exemplet hämtar en bild med namnet *image* från resurs gruppen "myResourceGroup" och tilldelar den variabeln *$managedImage*. 

```azurepowershell-interactive
$managedImage = Get-AzImage `
   -ImageName myImage `
   -ResourceGroupName myResourceGroup
```


## <a name="create-an-image-version"></a>Skapa en avbildningsversion

Skapa en avbildnings version från den hanterade avbildningen med [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion). 

Tillåtna tecken för bild version är tal och punkter. Talen måste vara inom intervallet för ett 32-bitars heltal. Format: *Major version*. *MinorVersion*. *Korrigering*.

I det här exemplet är avbildnings versionen *1.0.0* och replikeras till både *västra centrala* USA och *södra centrala* Data Center. Kom ihåg att du även måste inkludera *käll* regionen som mål för replikering när du väljer mål regioner för replikering. 


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $imageDefinition.ResourceGroupName `
   -Location $imageDefinition.Location `
   -TargetRegion $targetRegions  `
   -SourceImageId $managedImage.Id.ToString() `
   -PublishingProfileEndOfLifeDate '2020-12-31' `
   -asJob 
```

Det kan ta en stund att replikera avbildningen till alla mål regioner, så vi har skapat ett jobb så att vi kan spåra förloppet. Om du vill se förloppet skriver du `$job.State` .

```azurepowershell-interactive
$job.State
```


> [!NOTE]
> Du måste vänta tills avbildnings versionen är fullständigt slutförd och replikerad innan du kan använda samma hanterade avbildning för att skapa en annan avbildnings version. 
>
> Du kan också lagra din avbildning i Premium Storage genom att lägga till `-StorageAccountType Premium_LRS` eller [zonens redundant lagring](../storage/common/storage-redundancy.md) genom att lägga till `-StorageAccountType Standard_ZRS` när du skapar avbildnings versionen.
>

## <a name="delete-the-managed-image"></a>Ta bort den hanterade avbildningen

När du har kontrollerat att den nya avbildnings versionen fungerar som den ska kan du ta bort den hanterade avbildningen.

```azurepowershell-interactive
Remove-AzImage `
   -ImageName $managedImage.Name `
   -ResourceGroupName $managedImage.ResourceGroupName
```

## <a name="next-steps"></a>Nästa steg

När du har verifierat att replikeringen är klar kan du skapa en virtuell dator från den [generaliserade avbildningen](vm-generalized-image-version-powershell.md).

Information om hur du anger information om inköps planer finns i [tillhandahålla information om inköps plan för Azure Marketplace när du skapar avbildningar](marketplace-images.md).