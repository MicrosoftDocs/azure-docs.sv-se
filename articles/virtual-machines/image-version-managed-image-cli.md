---
title: Klona en hanterad avbildning till en avbildningsversion med Azure CLI
description: Lär dig hur du klonar en hanterad avbildning till en avbildningsversion i Shared Image Gallery med Hjälp av Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: 1d0644b9ec9009fe5d1db7701834cb9788f86ab0
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790175"
---
# <a name="clone-a-managed-image-to-an-image-version-using-the-azure-cli"></a>Klona en hanterad avbildning till en avbildningsversion med hjälp av Azure CLI
Om du har en befintlig hanterad avbildning som du vill klona till en Shared Image Gallery kan du skapa en Shared Image Gallery-avbildning direkt från den hanterade avbildningen. När du har testat den nya avbildningen kan du ta bort den hanterade källavbildningen. Du kan också migrera från en hanterad avbildning till en Shared Image Gallery med [hjälp av PowerShell](image-version-managed-image-powershell.md).

Bilder i ett bildgalleri har två komponenter, som vi skapar i det här exemplet:
- En **bilddefinition** innehåller information om avbildningen och kraven för att använda den. Detta omfattar huruvida avbildningen är Windows eller Linux, specialiserad eller generaliserad, versionsanteckningar samt krav på minsta och högsta minne. Det är en definition av en typ av bild. 
- En **avbildningsversion** används för att skapa en virtuell dator när du använder en Shared Image Gallery. Du kan ha flera versioner av en avbildning efter behov för din miljö. När du skapar en virtuell dator används avbildningsversionen för att skapa nya diskar för den virtuella datorn. Avbildningsversioner kan användas flera gånger.


## <a name="before-you-begin"></a>Innan du börjar

För att kunna slutföra den här artikeln måste du ha en [Shared Image Gallery](shared-images-cli.md). 

För att kunna slutföra exemplet i den här artikeln måste du ha en befintlig hanterad avbildning av en generaliserad virtuell dator. Mer information finns i Avbilda [en hanterad avbildning.](./linux/capture-image.md) Om den hanterade avbildningen innehåller en datadisk får datadiskstorleken inte vara större än 1 TB.

När du arbetar med den här artikeln ersätter du resursgruppen och vm-namnen där det behövs.



## <a name="create-an-image-definition"></a>Skapa en avbildningsdefinition

Eftersom hanterade avbildningar alltid är generaliserade avbildningar skapar du en avbildningsdefinition med `--os-state generalized` hjälp av för en generaliserad avbildning.

Bilddefinitionsnamn kan består av versaler eller gemener, siffror, punkter, bindestreck och punkter. 

Mer information om de värden som du kan ange för en bilddefinition finns i [Bilddefinitioner](./shared-image-galleries.md#image-definitions).

Skapa en avbildningsdefinition i galleriet med [az sig image-definition create](/cli/azure/sig/image-definition#az_sig_image_definition_create).

I det här exemplet heter avbildningsdefinitionen *myImageDefinition* och är för en [generaliserad](./shared-image-galleries.md#generalized-and-specialized-images) Linux OS-avbildning. Om du vill skapa en definition för avbildningar med hjälp av ett Windows-operativsystem använder du `--os-type Windows` . 

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
   --os-state generalized
```


## <a name="create-the-image-version"></a>Skapa avbildningsversionen

Skapa versioner med [az image gallery create-image-version](/cli/azure/sig/image-version#az_sig_image_version_create). Du måste skicka ID:t för den hanterade avbildningen som ska användas som baslinje för att skapa avbildningsversionen. Du kan använda [az image list](/cli/azure/image?view#az_image_list) för att hämta DINA AVBILDNINGAR. 

```azurecli-interactive
az image list --query "[].[name, id]" -o tsv
```

Tillåtna tecken för bildversion är siffror och punkter. Tal måste vara inom intervallet för ett 32-bitars heltal. Format: *MajorVersion*. *MinorVersion*. *Korrigera*.

I det här exemplet är versionen av avbildningen *1.0.0* och vi ska skapa en replik i regionen USA, södra *centrala* och en replik i regionen USA, östra *2* med zonredundant lagring. När du väljer målregioner för replikering bör du komma ihåg att du även måste inkludera *källregionen* som ett mål för replikering.

Skicka ID:t för den hanterade avbildningen i `--managed-image` parametern .


```azurecli-interactive 
imageID="/subscriptions/<subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.Compute/images/myImage"
az sig image-version create \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --gallery-image-definition $imageDef \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus2=1=standard_zrs" \
   --replica-count 2 \
   --managed-image $imageID
```

> [!NOTE]
> Du måste vänta tills avbildningsversionen har skapats och replikerats helt innan du kan använda samma hanterade avbildning för att skapa en annan avbildningsversion.
>
> Du kan också lagra alla repliker av avbildningsversion i [Zonredundant lagring genom](../storage/common/storage-redundancy.md) att lägga `--storage-account-type standard_zrs` till när du skapar avbildningsversionen.
>

## <a name="next-steps"></a>Nästa steg

Skapa en virtuell dator från en [generaliserad avbildningsversion](vm-generalized-image-version-cli.md).

Information om hur du tillhandahåller information om inköpsplan finns i Ange Azure Marketplace [när du skapar avbildningar.](marketplace-images.md)