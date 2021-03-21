---
title: PowerShell – skapa en bild från en ögonblicks bild eller hanterad disk i ett delat avbildnings Galleri
description: Lär dig hur du skapar en avbildning från en ögonblicks bild eller en hanterad disk i ett delat avbildnings galleri med PowerShell.
author: cynthn
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure
ms.date: 06/30/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 70ef194ab7f7403f31ad431eba25db5f71dfb580
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102556911"
---
# <a name="create-an-image-from-a-managed-disk-or-snapshot-in-a-shared-image-gallery-using-powershell"></a>Skapa en bild från en hanterad disk eller ögonblicks bild i ett delat avbildnings galleri med PowerShell

Om du har en befintlig ögonblicks bild eller en hanterad disk som du vill migrera till ett delat avbildnings Galleri kan du skapa en avbildning av en delad avbildning direkt från den hanterade disken eller ögonblicks bilden. När du har testat den nya avbildningen kan du ta bort den hanterade käll disken eller ögonblicks bilden. Du kan också skapa en avbildning från en hanterad disk eller ögonblicks bild i ett galleri för delade avbildningar med hjälp av [Azure CLI](image-version-snapshot-cli.md).

Bilder i ett bild galleri har två komponenter som vi kommer att skapa i det här exemplet:
- En **bild definition** innehåller information om avbildningen och kraven för att använda den. Detta inkluderar om avbildningen är Windows eller Linux, specialiserad eller generaliserad, viktig information och lägsta och högsta minnes krav. Det är en definition av en typ av bild. 
- En **avbildnings version** är vad som används för att skapa en virtuell dator när du använder ett delat avbildnings Galleri. Du kan ha flera versioner av en avbildning efter behov för din miljö. När du skapar en virtuell dator används avbildnings versionen för att skapa nya diskar för den virtuella datorn. Avbildnings versioner kan användas flera gånger.


## <a name="before-you-begin"></a>Innan du börjar

För att slutföra den här artikeln måste du ha en ögonblicks bild eller en hanterad disk. 

Om du vill inkludera en datadisk får data disk storleken inte vara större än 1 TB.

Ersätt resurs namnen där det behövs när du arbetar i den här artikeln.


## <a name="get-the-snapshot-or-managed-disk"></a>Hämta ögonblicks bilden eller den hanterade disken

Du kan se en lista över ögonblicks bilder som är tillgängliga i en resurs grupp med hjälp av [Get-AzSnapshot](/powershell/module/az.compute/get-azsnapshot). 

```azurepowershell-interactive
get-azsnapshot | Format-Table -Property Name,ResourceGroupName
```

När du känner till namnet på ögonblicks bilden och vilken resurs grupp det finns i kan du använda `Get-AzSnapshot` igen för att hämta Snapshot-objektet och lagra det i en variabel som ska användas senare. Det här exemplet hämtar en ögonblicks bild med namnet ' *ögonblicks bild* "från resurs gruppen" myResourceGroup "och tilldelar den variabeln *$Source*. 

```azurepowershell-interactive
$source = Get-AzSnapshot `
   -SnapshotName mySnapshot `
   -ResourceGroupName myResourceGroup
```

Du kan också använda en hanterad disk i stället för en ögonblicks bild. Använd [Get-AzDisk](/powershell/module/az.compute/get-azdisk)för att hämta en hanterad disk. 

```azurepowershell-interactive
Get-AzDisk | Format-Table -Property Name,ResourceGroupName
```

Hämta sedan den hanterade disken och tilldela den till `$source` variabeln.

```azurepowershell-interactive
$source = Get-AzDisk `
   -Name myDisk
   -ResourceGroupName myResourceGroup
```

Du kan använda samma cmdlets för att hämta alla data diskar som du vill ska ingå i din avbildning. Tilldela dem till variabler och Använd sedan variablerna senare när du skapar avbildnings versionen.


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

När du gör en avbildnings definition ser du till att har all rätt information. I det här exemplet antar vi att ögonblicks bilden eller den hanterade disken kommer från en virtuell dator som används och inte har generaliserats. Om den hanterade disken eller ögonblicks bilden tog ett generaliserat operativ system (efter att du kört Sysprep för Windows eller [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` eller `-deprovision+user` för Linux) ändrar `-OsState` du till `generalized` . 

Mer information om de värden som du kan ange för en bild definition finns i [bild definitioner](./shared-image-galleries.md#image-definitions).

Skapa avbildnings definitionen med [New-AzGalleryImageDefinition](/powershell/module/az.compute/new-azgalleryimageversion). I det här exemplet heter avbildnings definitionen *myImageDefinition* och är för ett specialiserat Windows-operativsystem. Använd om du vill skapa en definition för avbildningar med hjälp av ett Linux-operativsystem `-OsType Linux` . 

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

### <a name="purchase-plan-information"></a>Inköpsplaninformation

I vissa fall måste du skicka information om inköps planer i när du skapar en virtuell dator från en avbildning som baseras på en Azure Marketplace-avbildning. I dessa fall rekommenderar vi att du inkluderar information om inköps planen i avbildnings definitionen. I det här fallet, se [Ange inköps prenumerations information för Azure Marketplace när du skapar avbildningar](marketplace-images.md).


## <a name="create-an-image-version"></a>Skapa en avbildningsversion

Skapa en avbildnings version från ögonblicks bilden med [New-AzGalleryImageVersion](/powershell/module/az.compute/new-azgalleryimageversion). 

Tillåtna tecken för bild version är tal och punkter. Talen måste vara inom intervallet för ett 32-bitars heltal. Format: *Major version*. *MinorVersion*. *Korrigering*.

Om du vill att avbildningen ska innehålla en datadisk, förutom OS-disken, lägger du till `-DataDiskImage` parametern och anger den till ID: t för ögonblicks bilden av en data disk eller en hanterad disk.

I det här exemplet är avbildnings versionen *1.0.0* och replikeras till både *västra centrala* USA och *södra centrala* Data Center. Kom ihåg att du även måste inkludera *käll* regionen som mål för replikering när du väljer mål regioner för replikering.


```azurepowershell-interactive
$region1 = @{Name='South Central US';ReplicaCount=1}
$region2 = @{Name='West Central US';ReplicaCount=2}
$targetRegions = @($region1,$region2)
$job = $imageVersion = New-AzGalleryImageVersion `
   -GalleryImageDefinitionName $imageDefinition.Name `
   -GalleryImageVersionName '1.0.0' `
   -GalleryName $gallery.Name `
   -ResourceGroupName $gallery.ResourceGroupName `
   -Location $gallery.Location `
   -TargetRegion $targetRegions  `
   -OSDiskImage @{Source = @{Id=$source.Id}; HostCaching = "ReadOnly" } `
   -PublishingProfileEndOfLifeDate '2025-01-01' `
   -asJob 
```

Det kan ta en stund att replikera avbildningen till alla mål regioner, så vi har skapat ett jobb så att vi kan spåra förloppet. Om du vill se förloppet för jobbet skriver du `$job.State` .

```azurepowershell-interactive
$job.State
```

> [!NOTE]
> Du måste vänta tills avbildnings versionen är fullständigt slutförd och replikerad innan du kan använda samma ögonblicks bild för att skapa en avbildnings version. 
>
> Du kan också lagra avbildnings versionen i [zon redundant lagring](../storage/common/storage-redundancy.md) genom att lägga till `-StorageAccountType Standard_ZRS` den när du skapar avbildnings versionen.
>

## <a name="delete-the-source"></a>Ta bort källan

När du har kontrollerat att den nya avbildnings versionen fungerar som den ska kan du ta bort källan för avbildningen med antingen [Remove-AzSnapshot](/powershell/module/Az.Compute/Remove-AzSnapshot) eller [Remove-AzDisk](/powershell/module/az.compute/remove-azdisk).


## <a name="next-steps"></a>Nästa steg

När du har verifierat att replikeringen är klar kan du skapa en virtuell dator från den [specialiserade avbildningen](vm-specialized-image-version-powershell.md).

Information om hur du anger information om inköps planer finns i [tillhandahålla information om inköps plan för Azure Marketplace när du skapar avbildningar](marketplace-images.md).