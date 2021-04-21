---
title: Skapa en virtuell dator från en generaliserad avbildning med hjälp av Azure CLI
description: Skapa en virtuell dator från en generaliserad avbildningsversion med hjälp av Azure CLI.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: a5e0e5544c5e66f43b56de49beaa3ef3932d33f9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776887"
---
# <a name="create-a-vm-from-a-generalized-image-version-using-the-azure-cli"></a>Skapa en virtuell dator från en generaliserad avbildningsversion med hjälp av Azure CLI

Skapa en virtuell dator från en [generaliserad avbildningsversion som](./shared-image-galleries.md#generalized-and-specialized-images) lagras i en Shared Image Gallery. Om du vill skapa en virtuell dator med hjälp av en specialiserad avbildning kan du se [Skapa en virtuell dator från en specialiserad avbildning.](vm-specialized-image-version-powershell.md) 


## <a name="get-the-image-id"></a>Hämta avbildnings-ID:t

Visa en lista med bilddefinitionerna i ett galleri [med az sig image-definition list](/cli/azure/sig/image-definition#az_sig_image_definition_list) för att se namn och ID för definitionerna.

```azurecli-interactive 
resourceGroup=myGalleryRG
gallery=myGallery
az sig image-definition list --resource-group $resourceGroup --gallery-name $gallery --query "[].[name, id]" --output tsv
```

## <a name="create-the-vm"></a>Skapa den virtuella datorn

Skapa en virtuell dator med [az vm create](/cli/azure/vm#az_vm_create). Om du vill använda den senaste versionen av avbildningen anger `--image` du bilddefinitionens ID. 

Ersätt resursnamnen efter behov i det här exemplet. 

```azurecli-interactive 
imgDef="/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition"
vmResourceGroup=myResourceGroup
location=eastus
vmName=myVM
adminUsername=azureuser


az group create --name $vmResourceGroup --location $location

az vm create\
   --resource-group $vmResourceGroup \
   --name $vmName \
   --image $imgDef \
   --admin-username $adminUsername \
   --generate-ssh-keys
```

Du kan också använda en specifik version med hjälp av avbildningsversions-ID:t för `--image` parametern . Om du till exempel vill använda avbildningsversion *1.0.0 skriver* du: `--image "/subscriptions/<subscription ID where the gallery is located>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition/versions/1.0.0"` .

## <a name="next-steps"></a>Nästa steg

[Azure Image Builder (förhandsversion)](./image-builder-overview.md) kan hjälpa dig att automatisera skapandet av avbildningsversion. Du kan till och med använda det för att uppdatera och skapa en ny avbildningsversion från [en befintlig avbildningsversion.](./linux/image-builder-gallery-update-image-version.md)