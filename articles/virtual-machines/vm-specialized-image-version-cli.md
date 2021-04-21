---
title: Skapa en virtuell dator från en specialiserad avbildningsversion med hjälp av Azure CLI
description: Skapa en virtuell dator med en specialiserad avbildningsversion i en Shared Image Gallery med hjälp av Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 04/23/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.custom: devx-track-azurecli
ms.openlocfilehash: b3498037f3d2088459784ab066b8e94ba344a275
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792191"
---
# <a name="create-a-vm-using-a-specialized-image-version-with-the-azure-cli"></a>Skapa en virtuell dator med en specialiserad avbildningsversion med Azure CLI

Skapa en virtuell dator från en [specialiserad avbildningsversion](./shared-image-galleries.md#generalized-and-specialized-images) som lagras i en Shared Image Gallery. Om du vill skapa en virtuell dator med en generaliserad avbildningsversion kan du gå [till Skapa en virtuell dator från en generaliserad avbildningsversion.](vm-generalized-image-version-cli.md)

Ersätt resursnamnen efter behov i det här exemplet. 

Visa en lista med bilddefinitionerna i ett galleri [med az sig image-definition list](/cli/azure/sig/image-definition#az_sig_image_definition_list) för att se namn och ID för definitionerna.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list \
   --resource-group $resourceGroup \
   --gallery-name $gallery \
   --query "[].[name, id]" \
   --output tsv
```

Skapa den virtuella datorn med [az vm create med](/cli/azure/vm#az_vm_create) parametern --specialized för att indikera att avbildningen är en specialiserad avbildning. 

Använd avbildningsdefinitions-ID för att skapa den virtuella datorn från den `--image` senaste versionen av avbildningen som är tillgänglig. Du kan också skapa den virtuella datorn från en specifik version genom att ange avbildningsversions-ID:t för `--image` . 

I det här exemplet skapar vi en virtuell dator från den senaste versionen av avbildningen *myImageDefinition.*

```azurecli
az group create --name myResourceGroup --location eastus
az vm create --resource-group myResourceGroup \
    --name myVM \
    --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" \
    --specialized
```


## <a name="next-steps"></a>Nästa steg
[Azure Image Builder (förhandsversion)](./image-builder-overview.md) kan hjälpa dig att automatisera skapandet av avbildningsversion. Du kan till och med använda det för att uppdatera och skapa en ny avbildningsversion från [en befintlig avbildningsversion.](./linux/image-builder-gallery-update-image-version.md) 

Du kan också skapa Shared Image Gallery resurs med hjälp av mallar. Det finns flera tillgängliga Azure-snabbstartsmallar: 

- [Skapa ett Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Skapa en avbildningsdefinition i ett Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Skapa en avbildningsversion i ett Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Skapa en virtuell dator från avbildningsversion](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)