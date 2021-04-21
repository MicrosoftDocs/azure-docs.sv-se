---
title: ta med fil
description: ta med fil
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/25/2019
ms.author: cynthn
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: b966f68e19794aadebff76e3e9b29ed79a32eebe
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800354"
---
## <a name="update-resources"></a>Uppdatera resurser

Det finns vissa begränsningar för vad som kan uppdateras. Följande objekt kan uppdateras: 

Delat bildgalleri:
- Description

Bilddefinition:
- Rekommenderade virtuella processorer
- Rekommenderat minne
- Description
- Slutdatum

Avbildningsversion:
- Antal regionala repliker
- Målregioner
- Undantag från senaste
- Slutdatum

Ta inte bort den hanterade källavbildningen om du planerar att lägga till replikregioner. Den hanterade källavbildningen behövs för att replikera avbildningsversionen till ytterligare regioner. 

Uppdatera beskrivningen av ett galleri med hjälp av ([az sig update](/cli/azure/sig#az_sig_update). 

```azurecli-interactive
az sig update \
   --gallery-name myGallery \
   --resource-group myGalleryRG \
   --set description="My updated description."
```


Uppdatera beskrivningen av en avbildningsdefinition med [az sig image-definition update](/cli/azure/sig/image-definition#az_sig_image_definition_update).

```azurecli-interactive
az sig image-definition update \
   --gallery-name myGallery\
   --resource-group myGalleryRG \
   --gallery-image-definition myImageDefinition \
   --set description="My updated description."
```

Uppdatera en avbildningsversion för att lägga till en region att replikera till [med az sig image-version update](/cli/azure/sig/image-definition#az_sig_image_definition_update). Den här ändringen tar en stund när avbildningen replikeras till den nya regionen.

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --add publishingProfile.targetRegions  name=eastus
```

Det här exemplet visar hur du använder [az sig image-version update för](/cli/azure/sig/image-definition#az_sig_image_definition_update) att undanta den här avbildningsversionen från att användas som den senaste *avbildningen.*

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=true
```

Det här exemplet visar hur du använder [az sig image-version update för](/cli/azure/sig/image-definition#az_sig_image_definition_update) att inkludera den här avbildningsversionen i som övervägs för den senaste *avbildningen.*

```azurecli-interactive
az sig image-version update \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 \
   --set publishingProfile.excludeFromLatest=false
```

## <a name="delete-resources"></a>Ta bort resurser

Du måste ta bort resurser i omvänd ordning genom att först ta bort avbildningsversionen. När du har tagit bort alla avbildningsversioner kan du ta bort avbildningsdefinitionen. När du har tagit bort alla bilddefinitioner kan du ta bort galleriet. 

Ta bort en avbildningsversion [med az sig image-version delete](/cli/azure/sig/image-version#az_sig_image_version_delete).

```azurecli-interactive
az sig image-version delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition \
   --gallery-image-version 1.0.0 
```

Ta bort en avbildningsdefinition [med az sig image-definition delete](/cli/azure/sig/image-definition#az_sig_image_definition_delete).

```azurecli-interactive
az sig image-definition delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --gallery-image-definition myImageDefinition
```


Ta bort ett avbildningsgalleri [med az sig delete](/cli/azure/sig#az_sig_delete).

```azurecli-interactive
az sig delete \
   --resource-group myGalleryRG \
   --gallery-name myGallery
```