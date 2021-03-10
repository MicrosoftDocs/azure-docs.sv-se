---
title: Kopiera en avbildnings version från ett annat galleri med hjälp av CLI
description: Kopiera en avbildnings version från ett annat galleri med Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 0bea4fbac062b498dabe04e6e58d530d09b16d6d
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102553110"
---
# <a name="copy-an-image-from-another-gallery-using-the-azure-cli"></a>Kopiera en bild från ett annat galleri med hjälp av Azure CLI

Om du har flera gallerier i din organisation kan du också skapa avbildnings versioner från befintliga avbildnings versioner som lagras i andra gallerier. Du kan till exempel ha ett utvecklings-och test Galleri för att skapa och testa nya avbildningar. När de är redo att användas i produktionen kan du kopiera dem till ett produktions galleri med hjälp av det här exemplet. Du kan också skapa en bild från en bild i ett annat galleri med hjälp av [Azure PowerShell](image-version-another-gallery-powershell.md).



## <a name="before-you-begin"></a>Innan du börjar

För att slutföra den här artikeln måste du ha ett befintligt käll Galleri, bild definition och avbildnings version. Du bör också ha ett mål Galleri. 

Käll avbildnings versionen måste replikeras till den region där mål galleriet finns. 

Ersätt resurs namnen där det behövs när du arbetar i den här artikeln.



## <a name="get-information-from-the-source-gallery"></a>Hämta information från käll galleriet

Du behöver information från käll avbildnings definitionen så att du kan skapa en kopia av den i ditt nya Galleri.

Visa information om det tillgängliga bild galleriet med hjälp av [AZ sig-listan](/cli/azure/sig#az-sig-list) för att hitta information om käll galleriet.

```azurecli-interactive 
az sig list -o table
```

Visa en lista över bild definitionerna i ett galleri med hjälp av [AZ sig-bild definitions lista](/cli/azure/sig/image-definition#az-sig-image-definition-list). I det här exemplet söker vi efter bild definitioner i galleriet som heter *Galleri* i resurs gruppen *myGalleryRG* .

```azurecli-interactive 
az sig image-definition list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   -o table
```

Visa en lista med versioner av en bild i ett galleri med hjälp av [AZ sig-avbildning – versions lista](/cli/azure/sig/image-version#az-sig-image-version-list) för att hitta den avbildnings version som du vill kopiera till det nya galleriet. I det här exemplet letar vi efter alla avbildnings versioner som är en del av *myImageDefinition* -avbildnings definitionen.

```azurecli-interactive
az sig image-version list \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   -o table
```

När du har all den information du behöver kan du hämta ID för käll avbildnings versionen med hjälp av [AZ sig-avbildning-version show](/cli/azure/sig/image-version#az-sig-image-version-show).

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id" -o tsv
```


## <a name="create-the-image-definition"></a>Skapa avbildnings definitionen 

Du måste skapa en avbildnings definition som matchar bild definitionen för käll avbildningens version. Du kan se all information som du behöver för att återskapa avbildnings definitionen i det nya galleriet med hjälp av [AZ sig-bild-definition show](/cli/azure/sig/image-definition#az-sig-image-definition-show).

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

Skapa en ny avbildnings definition i det nya galleriet med hjälp av informationen från utdata ovan.


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


## <a name="create-the-image-version"></a>Skapa avbildnings versionen

Skapa versioner med [AZ avbildnings Galleri skapa-avbildning-version](/cli/azure/sig/image-version#az-sig-image-version-create). Du måste skicka in ID: t för den hanterade avbildningen som ska användas som bas linje för att skapa avbildnings versionen. Du kan använda [AZ avbildnings lista](/cli/azure/image?view#az-image-list) för att hämta information om avbildningar som finns i en resurs grupp. 

Tillåtna tecken för bild version är tal och punkter. Talen måste vara inom intervallet för ett 32-bitars heltal. Format: *Major version*. *MinorVersion*. *Korrigering*.

I det här exemplet är versionen av vår avbildning *1.0.0* och vi kommer att skapa en replik i regionen USA, *södra centrala* och 1 i regionen *USA, östra* med zon-redundant lagring.


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
> Du måste vänta tills avbildnings versionen är fullständigt slutförd och replikerad innan du kan använda samma hanterade avbildning för att skapa en annan avbildnings version.
>
> Du kan också lagra din avbildning i Premium Storage genom att lägga till `--storage-account-type  premium_lrs` eller [zonens redundant lagring](../storage/common/storage-redundancy.md) genom att lägga till `--storage-account-type  standard_zrs` när du skapar avbildnings versionen.
>

## <a name="next-steps"></a>Nästa steg

Skapa en virtuell dator från en [generaliserad](vm-generalized-image-version-cli.md) eller [specialiserad](vm-specialized-image-version-cli.md) avbildnings version.

Prova också att använda [Azure Image Builder (för hands version)](./image-builder-overview.md) för att automatisera avbildnings versionen, du kan även använda den för att uppdatera och [skapa en ny avbildnings version från en befintlig avbildnings version](./linux/image-builder-gallery-update-image-version.md). 

Information om hur du anger information om inköps planer finns i [tillhandahålla information om inköps plan för Azure Marketplace när du skapar avbildningar](marketplace-images.md).