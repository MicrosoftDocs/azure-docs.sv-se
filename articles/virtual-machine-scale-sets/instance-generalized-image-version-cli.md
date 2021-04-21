---
title: Skapa en skalningsuppsättning från en generaliserad avbildning med Azure CLI
description: Skapa en skalningsuppsättning med en generaliserad avbildning i en Shared Image Gallery med hjälp av Azure CLI.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: de50540345ac6170d229549cad736dafb04e488c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107792371"
---
# <a name="create-a-scale-set-from-a-generalized-image-with-azure-cli"></a>Skapa en skalningsuppsättning från en generaliserad avbildning med Azure CLI

Skapa en skalningsuppsättning från en generaliserad avbildningsversion som lagras [i en Shared Image Gallery](../virtual-machines/shared-image-galleries.md) med hjälp av Azure CLI. Om du vill skapa en skalningsuppsättning med en specialiserad avbildningsversion kan du se [Skapa skalningsuppsättningsinstanser från en specialiserad avbildning.](instance-specialized-image-version-cli.md)

Om du väljer att installera och använda CLI lokalt kräver den här självstudien att du kör Azure CLI version 2.4.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

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

Skapa skalningsuppsättningen med [`az vmss create`](/cli/azure/vmss#az_vmss_create) . 

Använd avbildningsdefinitions-ID:t för `--image` att skapa skalningsuppsättningsinstanser från den senaste versionen av avbildningen som är tillgänglig. Du kan också skapa skalningsuppsättningsinstanserna från en specifik version genom att ange avbildningsversions-ID:t för `--image` . Tänk på att användningen av en viss avbildningsversion innebär att automatiseringen kan misslyckas om den specifika avbildningsversionen inte är tillgänglig eftersom den har tagits bort eller tagits bort från regionen. Vi rekommenderar att du använder avbildningsdefinitions-ID:t för att skapa den nya virtuella datorn, såvida inte en viss avbildningsversion krävs.

I det här exemplet skapar vi instanser från den senaste versionen av avbildningen *myImageDefinition.*

```azurecli
az group create --name myResourceGroup --location eastus
az vmss create \
   --resource-group myResourceGroup \
   --name myScaleSet \
   --image "/subscriptions/<Subscription ID>/resourceGroups/myGalleryRG/providers/Microsoft.Compute/galleries/myGallery/images/myImageDefinition" 
   --admin-username azureuser \
   --generate-ssh-keys
```

Det tar några minuter att skapa och konfigurera alla skalningsuppsättningsresurser och virtuella datorer.

## <a name="next-steps"></a>Nästa steg
[Azure Image Builder (förhandsversion)](../virtual-machines/image-builder-overview.md) kan hjälpa dig att automatisera skapandet av avbildningsversion. Du kan till och med använda det för att uppdatera och skapa en ny avbildningsversion från [en befintlig avbildningsversion.](../virtual-machines/linux/image-builder-gallery-update-image-version.md) 

Du kan också skapa Shared Image Gallery resurs med hjälp av mallar. Det finns flera tillgängliga Azure-snabbstartsmallar: 

- [Skapa ett Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Skapa en avbildningsdefinition i ett Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Skapa en avbildningsversion i ett Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)

Mer information om delade bildgallerier finns i [Översikt.](../virtual-machines/shared-image-galleries.md) Om du får problem kan du gå till [Felsöka delade bildgallerier.](../virtual-machines/troubleshooting-shared-images.md)