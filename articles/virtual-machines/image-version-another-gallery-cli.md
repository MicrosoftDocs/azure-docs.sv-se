---
title: Kopiera en avbildningsversion från ett annat galleri med hjälp av CLI
description: Kopiera en avbildningsversion från ett annat galleri med Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: e2cd885d886a0f13783e61a04c7243efdf12967e
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784991"
---
# <a name="copy-an-image-from-another-gallery-using-the-azure-cli"></a>Kopiera en bild från ett annat galleri med hjälp av Azure CLI

Om du har flera gallerier i din organisation kan du även skapa avbildningsversioner från befintliga avbildningsversioner som lagras i andra gallerier. Du kan till exempel ha ett utvecklings- och testgalleri för att skapa och testa nya bilder. När de är redo att användas i produktion kan du kopiera dem till ett produktionsgalleri med hjälp av det här exemplet. Du kan också skapa en avbildning från en avbildning i ett annat galleri med [hjälp av Azure PowerShell](image-version-another-gallery-powershell.md).



## <a name="before-you-begin"></a>Innan du börjar

För att kunna slutföra den här artikeln måste du ha ett befintligt källgalleri, en bilddefinition och en bildversion. Du bör också ha ett målgalleri. 

Källavbildningsversionen måste replikeras till den region där målgalleriet finns. 

När du går igenom den här artikeln ersätter du resursnamnen där det behövs.



## <a name="get-information-from-the-source-gallery"></a>Hämta information från källgalleriet

Du behöver information från källavbildningsdefinitionen så att du kan skapa en kopia av den i det nya galleriet.

Visa information om tillgängliga bildgallerier med [az sig list för](/cli/azure/sig#az_sig_list) att hitta information om källgalleriet.

```azurecli-interactive 
az sig list -o table
```

Visa en lista med bilddefinitionerna i ett galleri med [az sig image-definition list](/cli/azure/sig/image-definition#az_sig_image_definition_list). I det här exemplet söker vi efter bilddefinitioner i galleriet med namnet *myGallery* i resursgruppen *myGalleryRG.*

```azurecli-interactive 
az sig image-definition list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   -o table
```

Visa en lista över versionerna av en avbildning i ett galleri med [az sig image-version list](/cli/azure/sig/image-version#az_sig_image_version_list) för att hitta den avbildningsversion som du vill kopiera till det nya galleriet. I det här exemplet letar vi efter alla avbildningsversioner som ingår i avbildningsdefinitionen *myImageDefinition.*

```azurecli-interactive
az sig image-version list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```

När du har all information du behöver kan du hämta ID:t för källavbildningsversionen med [az sig image-version show](/cli/azure/sig/image-version#az_sig_image_version_show).

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id" -o tsv
```


## <a name="create-the-image-definition"></a>Skapa avbildningsdefinitionen 

Du måste skapa en bilddefinition som matchar bilddefinitionen för din källavbildningsversion. Du kan se all information som du behöver för att återskapa bilddefinitionen i det nya galleriet med [az sig image-definition show](/cli/azure/sig/image-definition#az_sig_image_definition_show).

```azurecli-interactive
az sig image-definition show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```

Utdata ser ut ungefär så här:

```output
{
  "description": null,
  "disallowed": null,
  "endOfLifeDate": null,
  "eula": null,
  "hyperVgeneration": "V1",
  "id": "/subscriptions/1111abcd-1a23-4b45-67g7-1234567de123/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition",
  "identifier": {
    "offer": "myOffer",
    "publisher": "myPublisher",
    "sku": "mySKU"
  },
  "location": "eastus",
  "name": "myImageDefinition",
  "osState": "Specialized",
  "osType": "Linux",
  "privacyStatementUri": null,
  "provisioningState": "Succeeded",
  "purchasePlan": null,
  "recommended": null,
  "releaseNoteUri": null,
  "resourceGroup": "myGalleryRG",
  "tags": null,
  "type": "Microsoft.Compute/galleries/images"
}
```

Skapa en ny bilddefinition i det nya galleriet med hjälp av informationen i utdata ovan.


```azurecli-interactive 
az sig image-definition create \
   --resource-group myNewGalleryRG \
   --gallery-name myNewGallery \
   --gallery-image-definition myImageDefinition \
   --publisher myPublisher \
   --offer myOffer \
   --sku mySKU \
   --os-type Linux \
   --hyper-v-generation V1 \
   --os-state specialized 
```


## <a name="create-the-image-version"></a>Skapa avbildningsversionen

Skapa versioner med [az image gallery create-image-version](/cli/azure/sig/image-version#az_sig_image_version_create). Du måste skicka IN ID:t för den hanterade avbildningen som ska användas som baslinje för att skapa avbildningsversionen. Du kan använda [az image list för](/cli/azure/image?view#az_image_list) att hämta information om avbildningar som finns i en resursgrupp. 

Tillåtna tecken för bildversion är siffror och punkter. Tal måste vara inom intervallet för ett 32-bitars heltal. Format: *MajorVersion*. *MinorVersion*. *Korrigera*.

I det här exemplet är versionen av avbildningen *1.0.0* och vi ska skapa en replik  i regionen USA, södra *centrala* och en replik i regionen USA, östra med zonredundant lagring.


```azurecli-interactive 
az sig image-version create \
   --resource-group myNewGalleryRG \
   --gallery-name myNewGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --target-regions "southcentralus=1" "eastus=1=standard_zrs" \
   --replica-count 2 \
   --managed-image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"
```

> [!NOTE]
> Du måste vänta tills avbildningsversionen är klar och replikeras innan du kan använda samma hanterade avbildning för att skapa en annan avbildningsversion.
>
> Du kan också lagra avbildningen i Premium Storage genom att lägga `--storage-account-type  premium_lrs` till eller [zonredundant lagring](../storage/common/storage-redundancy.md) genom att lägga till när du skapar `--storage-account-type  standard_zrs` avbildningsversionen.
>

## <a name="next-steps"></a>Nästa steg

Skapa en virtuell dator från en [generaliserad eller](vm-generalized-image-version-cli.md) specialiserad avbildningsversion. [](vm-specialized-image-version-cli.md)

Prova också att [använda Azure Image Builder (förhandsversion)](./image-builder-overview.md) för att automatisera skapandet av avbildningsversion. Du kan till och med använda det för att uppdatera och skapa en ny avbildningsversion från en befintlig avbildningsversion. [](./linux/image-builder-gallery-update-image-version.md) 

Information om hur du tillhandahåller information om inköpsplanen finns i [Ange Azure Marketplace information om inköpsplanen när du skapar avbildningar.](marketplace-images.md)