---
title: CLI – Skapa en avbildning från en ögonblicksbild eller en hanterad disk i en Shared Image Gallery
description: Lär dig hur du skapar en avbildning från en ögonblicksbild eller en hanterad disk i Shared Image Gallery med hjälp av Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/30/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 2dc6d99b8b1c913479fc584b52f6ff919dfac675
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792299"
---
# <a name="create-an-image-from-a-managed-disk-or-snapshot-in-a-shared-image-gallery-using-the-azure-cli"></a>Skapa en avbildning från en hanterad disk eller ögonblicksbild i en Shared Image Gallery med hjälp av Azure CLI

Om du har en befintlig ögonblicksbild eller hanterad disk som du vill migrera till en Shared Image Gallery kan du skapa en Shared Image Gallery-avbildning direkt från den hanterade disken eller ögonblicksbilden. När du har testat den nya avbildningen kan du ta bort den hanterade källdisken eller ögonblicksbilden. Du kan också skapa en avbildning från en hanterad disk eller ögonblicksbild i en Shared Image Gallery med hjälp av [Azure PowerShell](image-version-snapshot-powershell.md).

Bilder i ett bildgalleri har två komponenter, som vi skapar i det här exemplet:
- En **bilddefinition** innehåller information om avbildningen och kraven för att använda den. Detta omfattar huruvida avbildningen är Windows eller Linux, specialiserad eller generaliserad, versionsanteckningar samt krav på minsta och högsta minne. Det är en definition av en typ av bild. 
- En **avbildningsversion** används för att skapa en virtuell dator när du använder en Shared Image Gallery. Du kan ha flera versioner av en avbildning efter behov för din miljö. När du skapar en virtuell dator används avbildningsversionen för att skapa nya diskar för den virtuella datorn. Avbildningsversioner kan användas flera gånger.


## <a name="before-you-begin"></a>Innan du börjar

För att kunna slutföra den här artikeln måste du ha en ögonblicksbild eller en hanterad disk. 

Om du vill inkludera en datadisk får storleken på datadisken inte vara större än 1 TB.

När du arbetar med den här artikeln ersätter du resursnamnen där det behövs.

## <a name="find-the-snapshot-or-managed-disk"></a>Hitta ögonblicksbilden eller den hanterade disken 

Du kan se en lista över ögonblicksbilder som är tillgängliga i en resursgrupp med [az snapshot list](/cli/azure/snapshot#az_snapshot_list). 

```azurecli-interactive
az snapshot list --query "[].[name, id]" -o tsv
```

Du kan också använda en hanterad disk i stället för en ögonblicksbild. Hämta en hanterad disk med [az disk list](/cli/azure/disk#az_disk_list). 

```azurecli-interactive
az disk list --query "[].[name, id]" -o tsv
```

När du har ID:t för ögonblicksbilden eller den hanterade disken och tilldelar den till en variabel med namnet `$source` som ska användas senare.

Du kan använda samma process för att hämta alla datadiskar som du vill ta med i avbildningen. Tilldela dem till variabler och använd sedan dessa variabler senare när du skapar avbildningsversionen.


## <a name="find-the-gallery"></a>Hitta galleriet

Du behöver information om bildgalleriet för att kunna skapa avbildningsdefinitionen.

Visa information om tillgängliga avbildningsgallerier med [az sig list](/cli/azure/sig#az_sig_list). Observera gallerinamnet som den resursgrupp som galleriet finns i för senare användning.

```azurecli-interactive 
az sig list -o table
```


## <a name="create-an-image-definition"></a>Skapa en avbildningsdefinition

Bilddefinitioner skapar en logisk gruppering för bilder. De används för att hantera information om avbildningen. Bilddefinitionsnamn kan består av versaler eller gemener, siffror, punkter, bindestreck och punkter. 

När du gör avbildningsdefinitionen ska du kontrollera att har all rätt information. I det här exemplet antar vi att ögonblicksbilden eller den hanterade disken kommer från en virtuell dator som används och inte har generaliserats. Om den hanterade disken eller ögonblicksbilden togs av ett generaliserat operativsystem (efter körning av Sysprep för Windows eller [waagent](https://github.com/Azure/WALinuxAgent) eller för Linux) ändrar `-deprovision` du `-deprovision+user` till `-OsState` `generalized` . 

Mer information om de värden som du kan ange för en bilddefinition finns i [Bilddefinitioner](./shared-image-galleries.md#image-definitions).

Skapa en avbildningsdefinition i galleriet med [az sig image-definition create](/cli/azure/sig/image-definition#az_sig_image_definition_create).

I det här exemplet heter avbildningsdefinitionen *myImageDefinition* och är för en specialiserad [Linux](./shared-image-galleries.md#generalized-and-specialized-images) OS-avbildning. Om du vill skapa en definition för avbildningar med hjälp av ett Windows-operativsystem använder du `--os-type Windows` . 

I det här exemplet heter galleriet *myGallery*, det finns i resursgruppen *myGalleryRG* och avbildningsdefinitionsnamnet *blir mImageDefinition*.

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


## <a name="create-the-image-version"></a>Skapa avbildningsversionen

Skapa en avbildningsversion [med az image gallery create-image-version](/cli/azure/sig/image-version#az_sig_image_version_create). 

Tillåtna tecken för bildversion är siffror och punkter. Tal måste vara inom intervallet för ett 32-bitars heltal. Format: *MajorVersion*. *MinorVersion*. *Korrigera*.

I det här exemplet är versionen av avbildningen *1.0.0* och vi ska skapa en replik i regionen USA, södra *centrala* och en replik i regionen USA, östra *2* med zonredundant lagring. När du väljer målregioner för replikering måste  du även inkludera källregionen för den hanterade disken eller ögonblicksbilden som ett mål för replikering.

Skicka ID:t för ögonblicksbilden eller den hanterade disken i `--os-snapshot` parametern .


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

Om du vill inkludera datadiskar i avbildningen måste du inkludera både parametern som är inställd på LUN-numret och uppsättningen till ID:t för `--data-snapshot-luns` `--data-snapshots` datadisken eller ögonblicksbilden.

> [!NOTE]
> Du måste vänta tills avbildningsversionen har skapats och replikerats helt innan du kan använda samma hanterade avbildning för att skapa en annan avbildningsversion.
>
> Du kan också lagra alla repliker av avbildningsversion i [Zonredundant lagring genom](../storage/common/storage-redundancy.md) att lägga `--storage-account-type standard_zrs` till när du skapar avbildningsversionen.
>

## <a name="next-steps"></a>Nästa steg

Skapa en virtuell dator från en [specialiserad avbildningsversion](vm-specialized-image-version-cli.md).

Information om hur du tillhandahåller information om inköpsplan finns i Ange Azure Marketplace [när du skapar avbildningar.](marketplace-images.md)