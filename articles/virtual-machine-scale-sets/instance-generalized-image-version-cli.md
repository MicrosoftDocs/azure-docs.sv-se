---
title: Skapa en skalnings uppsättning från en generaliserad avbildning med Azure CLI
description: Skapa en skalnings uppsättning med en generaliserad avbildning i ett galleri för delad avbildning med hjälp av Azure CLI.
author: cynthn
ms.service: virtual-machine-scale-sets
ms.subservice: imaging
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 05/01/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: e360ae3cf0c463add9e6445a96c118d9f40ce9cb
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878044"
---
# <a name="create-a-scale-set-from-a-generalized-image-with-azure-cli"></a>Skapa en skalnings uppsättning från en generaliserad avbildning med Azure CLI

Skapa en skalnings uppsättning från en generaliserad avbildnings version som lagras i ett [delat avbildnings Galleri](../virtual-machines/shared-image-galleries.md) med hjälp av Azure CLI. Om du vill skapa en skalnings uppsättning med hjälp av en specialiserad avbildnings version, se [skapa skalnings uppsättnings instanser från en specialiserad avbildning](instance-specialized-image-version-cli.md).

Om du väljer att installera och använda CLI lokalt kräver den här självstudien att du kör Azure CLI-version 2.4.0 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI]( /cli/azure/install-azure-cli).

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

Skapa skalnings uppsättningen med hjälp av [`az vmss create`](/cli/azure/vmss#az-vmss-create) . 

Använd bild Definitions-ID: t för för `--image` att skapa skalnings uppsättnings instanserna från den senaste versionen av avbildningen som är tillgänglig. Du kan också skapa skalnings uppsättnings instanser från en speciell version genom att ange avbildningens versions-ID för `--image` . Tänk på att om du använder en angiven avbildnings version kan Automation inte köras om den angivna avbildnings versionen inte är tillgänglig eftersom den har tagits bort eller tagits bort från regionen. Vi rekommenderar att du använder bild Definitions-ID: t för att skapa din nya virtuella dator, om inte en speciell avbildnings version krävs.

I det här exemplet skapar vi instanser från den senaste versionen av *myImageDefinition* -avbildningen.

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
[Azure Image Builder (för hands version)](../virtual-machines/image-builder-overview.md) kan hjälpa dig att automatisera avbildnings versionen, du kan även använda den för att uppdatera och [skapa en ny avbildnings version från en befintlig avbildnings version](../virtual-machines/linux/image-builder-gallery-update-image-version.md). 

Du kan också skapa en delad resurs för avbildnings galleriet med hjälp av mallar. Det finns flera tillgängliga Azure snabb starts mallar: 

- [Skapa ett Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Skapa en avbildningsdefinition i ett Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Skapa en avbildningsversion i ett Shared Image Gallery](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)

Mer information om delade avbildnings gallerier finns i [översikten](../virtual-machines/shared-image-galleries.md). Om du stöter på problem, se [Felsöka delade avbildnings gallerier](../virtual-machines/troubleshooting-shared-images.md).