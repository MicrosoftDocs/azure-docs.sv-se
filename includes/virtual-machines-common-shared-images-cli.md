---
title: ta med fil
description: ta med fil
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 03/24/2020
ms.author: cynthn
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: fc35f277bfafa80f6239ef807f1a83591646a503
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107800268"
---
## <a name="create-an-image-gallery"></a>Skapa ett bildgalleri 

Ett bildgalleri är den primära resurs som används för att aktivera bilddelning. 

Tillåtna tecken för Gallerinamn är versaler eller gemener, siffror, punkter och punkter. Gallerinamnet får inte innehålla bindestreck.   Gallerinamn måste vara unika i din prenumeration. 

Skapa ett avbildningsgalleri med [az sig create](/cli/azure/sig#az_sig_create). I följande exempel skapas en resursgrupp med namnet gallery med namnet *myGalleryRG* i *USA,* östra och ett galleri med namnet *myGallery*.

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="share-the-gallery"></a>Dela galleriet

Du kan dela avbildningar mellan prenumerationer med Role-Based Access Control (RBAC). Du kan dela bilder på galleri-, bilddefinitions- eller bildversionsnivå. Alla användare som har läsbehörighet till en avbildningsversion, även mellan prenumerationer, kommer att kunna distribuera en virtuell dator med avbildningsversionen.

Vi rekommenderar att du delar med andra användare på gallerinivå. Hämta galleriets objekt-ID med [az sig show](/cli/azure/sig#az_sig_show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Använd objekt-ID:t som ett omfång, tillsammans med en e-postadress och [az role assignment create](/cli/azure/role/assignment#az_role_assignment_create) för att ge en användare åtkomst till det delade bildgalleriet. Ersätt `<email-address>` och med din egen `<gallery iD>` information.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

Mer information om hur du delar resurser med hjälp av RBAC finns i [Hantera åtkomst med hjälp av RBAC och Azure CLI.](../articles/role-based-access-control/role-assignments-cli.md)