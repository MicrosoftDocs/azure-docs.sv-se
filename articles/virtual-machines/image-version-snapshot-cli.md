---
title: CLI – skapa en bild från en ögonblicks bild eller hanterad disk i ett galleri för delade avbildningar
description: Lär dig hur du skapar en avbildning från en ögonblicks bild eller en hanterad disk i ett delat avbildnings galleri med hjälp av Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/30/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: c809edd3699d0b9827fe15da53d5d18b12cbe6e6
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102556969"
---
# <a name="create-an-image-from-a-managed-disk-or-snapshot-in-a-shared-image-gallery-using-the-azure-cli"></a>Skapa en bild från en hanterad disk eller ögonblicks bild i ett galleri för delad avbildning med hjälp av Azure CLI

Om du har en befintlig ögonblicks bild eller en hanterad disk som du vill migrera till ett delat avbildnings Galleri kan du skapa en avbildning av en delad avbildning direkt från den hanterade disken eller ögonblicks bilden. När du har testat den nya avbildningen kan du ta bort den hanterade käll disken eller ögonblicks bilden. Du kan också skapa en avbildning från en hanterad disk eller ögonblicks bild i ett galleri för delade avbildningar med hjälp av [Azure PowerShell](image-version-snapshot-powershell.md).

Bilder i ett bild galleri har två komponenter som vi kommer att skapa i det här exemplet:
- En **bild definition** innehåller information om avbildningen och kraven för att använda den. Detta inkluderar om avbildningen är Windows eller Linux, specialiserad eller generaliserad, viktig information och lägsta och högsta minnes krav. Det är en definition av en typ av bild. 
- En **avbildnings version** är vad som används för att skapa en virtuell dator när du använder ett delat avbildnings Galleri. Du kan ha flera versioner av en avbildning efter behov för din miljö. När du skapar en virtuell dator används avbildnings versionen för att skapa nya diskar för den virtuella datorn. Avbildnings versioner kan användas flera gånger.


## <a name="before-you-begin"></a>Innan du börjar

För att slutföra den här artikeln måste du ha en ögonblicks bild eller en hanterad disk. 

Om du vill inkludera en datadisk får data disk storleken inte vara större än 1 TB.

Ersätt resurs namnen där det behövs när du arbetar i den här artikeln.

## <a name="find-the-snapshot-or-managed-disk"></a>Hitta ögonblicks bilden eller den hanterade disken 

Du kan se en lista över ögonblicks bilder som är tillgängliga i en resurs grupp med [AZ Snapshot List](/cli/azure/snapshot#az-snapshot-list). 

```azurecli-interactive
az snapshot list --query "[].[name, id]" -o tsv
```

Du kan också använda en hanterad disk i stället för en ögonblicks bild. Använd [AZ disk List](/cli/azure/disk#az-disk-list)för att hämta en hanterad disk. 

```azurecli-interactive
az disk list --query "[].[name, id]" -o tsv
```

När du har ID: t för ögonblicks bilden eller den hanterade disken och kopplat den till en variabel som kallas `$source` för senare bruk.

Du kan använda samma process för att hämta alla data diskar som du vill ska ingå i din avbildning. Tilldela dem till variabler och Använd sedan variablerna senare när du skapar avbildnings versionen.


## <a name="find-the-gallery"></a>Hitta galleriet

Du behöver information om bild galleriet för att kunna skapa avbildnings definitionen.

Visa information om tillgängliga bild gallerier med hjälp av [AZ sig-listan](/cli/azure/sig#az-sig-list). Anteckna namnet på galleriet där resurs gruppen galleriet ska användas senare.

```azurecli-interactive 
az sig list -o table
```


## <a name="create-an-image-definition"></a>Skapa en avbildnings definition

Bild definitioner skapa en logisk gruppering för avbildningar. De används för att hantera information om avbildningen. Namn på bild definitioner kan bestå av versaler eller gemener, siffror, punkter, streck och punkter. 

När du gör en avbildnings definition ser du till att har all rätt information. I det här exemplet antar vi att ögonblicks bilden eller den hanterade disken kommer från en virtuell dator som används och inte har generaliserats. Om den hanterade disken eller ögonblicks bilden tog ett generaliserat operativ system (efter att du kört Sysprep för Windows eller [waagent](https://github.com/Azure/WALinuxAgent) `-deprovision` eller `-deprovision+user` för Linux) ändrar `-OsState` du till `generalized` . 

Mer information om de värden som du kan ange för en bild definition finns i [bild definitioner](./shared-image-galleries.md#image-definitions).

Skapa en bild definition i galleriet med hjälp av [AZ sig-bild-definition Create](/cli/azure/sig/image-definition#az-sig-image-definition-create).

I det här exemplet heter avbildnings definitionen *myImageDefinition* och är för en [SPECIALISERAd](./shared-image-galleries.md#generalized-and-specialized-images) Linux OS-avbildning. Använd om du vill skapa en definition för avbildningar med hjälp av ett Windows-operativsystem `--os-type Windows` . 

I det här exemplet *heter galleriet Galleri, det* finns i resurs gruppen *myGalleryRG* och bild definitions namnet kommer att vara *mImageDefinition*.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
imageDef=myImageDefinition
az sig image-definition create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --os-state specialized
```


## <a name="create-the-image-version"></a>Skapa avbildnings versionen

Skapa en avbildnings version med [AZ avbildnings Galleri skapa-avbildning-version](/cli/azure/sig/image-version#az-sig-image-version-create). 

Tillåtna tecken för bild version är tal och punkter. Talen måste vara inom intervallet för ett 32-bitars heltal. Format: *Major version*. *MinorVersion*. *Korrigering*.

I det här exemplet är versionen av vår avbildning *1.0.0* och vi kommer att skapa en replik i regionen USA, *södra centrala* och 1 i regionen *USA, östra 2* med zon-redundant lagring. Kom ihåg att du även måste inkludera *käll* regionen för den hanterade disken eller ögonblicks bilden som mål för replikering när du väljer mål regioner för replikering.

Skicka ID: t för ögonblicks bilden eller den hanterade disken i `--os-snapshot` parametern.


```azurecli-interactive 
az sig image-version create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus2=1=standard_zrs" \
   --replica-count 2 \
   --os-snapshot $source
```

Om du vill inkludera data diskar i avbildningen måste du inkludera både `--data-snapshot-luns` parametern som angetts till LUN-numret och `--data-snapshots` värdet för data diskens eller ögonblicks bildens ID.

> [!NOTE]
> Du måste vänta tills avbildnings versionen är fullständigt slutförd och replikerad innan du kan använda samma hanterade avbildning för att skapa en annan avbildnings version.
>
> Du kan också lagra alla dina avbildnings versions repliker i [zon redundant lagring](../storage/common/storage-redundancy.md) genom att lägga till `--storage-account-type standard_zrs` den när du skapar avbildnings versionen.
>

## <a name="next-steps"></a>Nästa steg

Skapa en virtuell dator från en [specialiserad avbildnings version](vm-specialized-image-version-cli.md).

Information om hur du anger information om inköps planer finns i [tillhandahålla information om inköps plan för Azure Marketplace när du skapar avbildningar](marketplace-images.md).