---
title: ta med fil
description: ta med fil
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 01/28/2020
ms.author: cynthn
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 6432c125c4fedd962faa28a4c84c7494300b0472
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92756088"
---
## <a name="list-information"></a>List information

Hämta plats, status och annan information om tillgängliga bild gallerier med hjälp av [AZ sig-listan](/cli/azure/sig#az-sig-list).

```azurecli-interactive 
az sig list -o table
```

Visa en lista över bild definitionerna i ett galleri, inklusive information om OS-typ och status, med hjälp av [AZ sig-avbildnings definitions lista](/cli/azure/sig/image-definition#az-sig-image-definition-list).

```azurecli-interactive 
az sig image-definition list --resource-group myGalleryRG --gallery-name myGallery -o table
```

Visa en lista med delade avbildnings versioner i ett galleri med hjälp av [AZ sig-avbildning – versions lista](/cli/azure/sig/image-version#az-sig-image-version-list).

```azurecli-interactive
az sig image-version list --resource-group myGalleryRG --gallery-name myGallery --gallery-image-definition myImageDefinition -o table
```

Hämta ID för en avbildnings version med hjälp av [AZ sig-avbildning-version show](/cli/azure/sig/image-version#az-sig-image-version-show).

```azurecli-interactive
az sig image-version show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --query "id"
```
