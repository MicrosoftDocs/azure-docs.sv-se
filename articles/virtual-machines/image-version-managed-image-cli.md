---
title: Klona en hanterad avbildning till en avbildnings version med Azure CLI
description: Lär dig hur du klonar en hanterad avbildning till en avbildnings version i ett galleri för delade avbildningar med hjälp av Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: cae82072785838d410453b2eb83685905b0ba04e
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102553790"
---
# <a name="clone-a-managed-image-to-an-image-version-using-the-azure-cli"></a>Klona en hanterad avbildning till en avbildnings version med hjälp av Azure CLI
Om du har en befintlig hanterad avbildning som du vill klona till ett delat avbildnings Galleri kan du skapa en avbildning av en delad avbildning direkt från den hanterade avbildningen. När du har testat din nya avbildning kan du ta bort den hanterade käll avbildningen. Du kan också migrera från en hanterad avbildning till ett delat avbildnings galleri med hjälp av [PowerShell](image-version-managed-image-powershell.md).

Bilder i ett bild galleri har två komponenter som vi kommer att skapa i det här exemplet:
- En **bild definition** innehåller information om avbildningen och kraven för att använda den. Detta inkluderar om avbildningen är Windows eller Linux, specialiserad eller generaliserad, viktig information och lägsta och högsta minnes krav. Det är en definition av en typ av bild. 
- En **avbildnings version** är vad som används för att skapa en virtuell dator när du använder ett delat avbildnings Galleri. Du kan ha flera versioner av en avbildning efter behov för din miljö. När du skapar en virtuell dator används avbildnings versionen för att skapa nya diskar för den virtuella datorn. Avbildnings versioner kan användas flera gånger.


## <a name="before-you-begin"></a>Innan du börjar

Du måste ha ett befintligt [delat avbildnings Galleri](shared-images-cli.md)för att kunna slutföra den här artikeln. 

Du måste ha en befintlig hanterad avbildning av en generaliserad virtuell dator för att kunna slutföra exemplet i den här artikeln. Mer information finns i [avbilda en hanterad avbildning](./linux/capture-image.md). Om den hanterade avbildningen innehåller en datadisk får data disk storleken inte vara större än 1 TB.

När du arbetar med den här artikeln ersätter du resurs gruppen och VM-namnen där det behövs.



## <a name="create-an-image-definition"></a>Skapa en avbildnings definition

Eftersom hanterade avbildningar alltid är generaliserade avbildningar skapar du en avbildnings definition med hjälp av `--os-state generalized` för en generaliserad avbildning.

Namn på bild definitioner kan bestå av versaler eller gemener, siffror, punkter, streck och punkter. 

Mer information om de värden som du kan ange för en bild definition finns i [bild definitioner](./shared-image-galleries.md#image-definitions).

Skapa en bild definition i galleriet med hjälp av [AZ sig-bild-definition Create](/cli/azure/sig/image-definition#az-sig-image-definition-create).

I det här exemplet heter avbildnings definitionen *myImageDefinition* och är för en [GENERALISERAd](./shared-image-galleries.md#generalized-and-specialized-images) Linux OS-avbildning. Använd om du vill skapa en definition för avbildningar med hjälp av ett Windows-operativsystem `--os-type Windows` . 

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


## <a name="create-the-image-version"></a>Skapa avbildnings versionen

Skapa versioner med [AZ avbildnings Galleri skapa-avbildning-version](/cli/azure/sig/image-version#az-sig-image-version-create). Du måste skicka in ID: t för den hanterade avbildningen som ska användas som bas linje för att skapa avbildnings versionen. Du kan använda [AZ bild lista](/cli/azure/image?view#az-image-list) för att hämta ID: n för dina avbildningar. 

```azurecli-interactive
az image list --query "[].[name, id]" -o tsv
```

Tillåtna tecken för bild version är tal och punkter. Talen måste vara inom intervallet för ett 32-bitars heltal. Format: *Major version*. *MinorVersion*. *Korrigering*.

I det här exemplet är versionen av vår avbildning *1.0.0* och vi kommer att skapa en replik i regionen USA, *södra centrala* och 1 i regionen *USA, östra 2* med zon-redundant lagring. Kom ihåg att du även måste inkludera *käll* regionen som mål för replikering när du väljer mål regioner för replikering.

Skicka ID: t för den hanterade avbildningen i `--managed-image` parametern.


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
> Du måste vänta tills avbildnings versionen är fullständigt slutförd och replikerad innan du kan använda samma hanterade avbildning för att skapa en annan avbildnings version.
>
> Du kan också lagra alla dina avbildnings versions repliker i [zon redundant lagring](../storage/common/storage-redundancy.md) genom att lägga till `--storage-account-type standard_zrs` den när du skapar avbildnings versionen.
>

## <a name="next-steps"></a>Nästa steg

Skapa en virtuell dator från en [generaliserad avbildnings version](vm-generalized-image-version-cli.md).

Information om hur du anger information om inköps planer finns i [tillhandahålla information om inköps plan för Azure Marketplace när du skapar avbildningar](marketplace-images.md).