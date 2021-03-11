---
title: Skapa en virtuell dator från en specialiserad avbildnings version med hjälp av Azure CLI
description: Skapa en virtuell dator med hjälp av en specialiserad avbildnings version i ett galleri för delad avbildning med hjälp av Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/23/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: fe081b0e74acf771e10406c15a3dea4e09956c37
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102560979"
---
# <a name="create-a-vm-using-a-specialized-image-version-with-the-azure-cli"></a>Skapa en virtuell dator med hjälp av en specialiserad avbildnings version med Azure CLI

Skapa en virtuell dator från en [specialiserad avbildnings version](./shared-image-galleries.md#generalized-and-specialized-images) som lagras i ett delat avbildnings Galleri. Om du vill skapa en virtuell dator med en generaliserad avbildnings version, se [skapa en virtuell dator från en generaliserad avbildnings version](vm-generalized-image-version-cli.md).

Ersätt resurs namn efter behov i det här exemplet. 

Visa en lista över bild definitionerna i ett galleri med hjälp av [AZ sig-bild definitions listan](/cli/azure/sig/image-definition#az-sig-image-definition-list) för att se namn och ID för definitionerna.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

Skapa den virtuella datorn med [AZ VM Create](/cli/azure/vm#az-vm-create) med hjälp av parametern--specialiserad för att indikera att avbildningen är en specialiserad avbildning. 

Använd bild Definitions-ID: t för för `--image` att skapa den virtuella datorn från den senaste versionen av avbildningen som är tillgänglig. Du kan också skapa den virtuella datorn från en angiven version genom att ange avbildningens versions-ID för `--image` . 

I det här exemplet skapar vi en virtuell dator från den senaste versionen av *myImageDefinition* -avbildningen.

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```


## <a name="next-steps"></a>Nästa steg
[Azure Image Builder (för hands version)](./image-builder-overview.md) kan hjälpa dig att automatisera avbildnings versionen, du kan även använda den för att uppdatera och [skapa en ny avbildnings version från en befintlig avbildnings version](./linux/image-builder-gallery-update-image-version.md). 

Du kan också skapa en delad resurs för avbildnings galleriet med hjälp av mallar. Det finns flera tillgängliga Azure snabb starts mallar: 

- [Skapa ett Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Skapa en avbildningsdefinition i ett Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Skapa en avbildningsversion i ett Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Skapa en virtuell dator från avbildningsversion](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)