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
ms.openlocfilehash: 9556b20ba0ceac2d4c1ad92897e6f9d46293387f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96025740"
---
## <a name="create-an-image-gallery"></a>Skapa ett bild galleri 

Ett avbildnings galleri är den primära resurs som används för att aktivera avbildnings delning. 

Tillåtna tecken för Galleri namn är versaler eller gemener, siffror, punkter och punkter. Galleri namnet får inte innehålla bindestreck.   Galleri namn måste vara unika inom din prenumeration. 

Skapa ett bild galleri med [AZ sig-Create](/cli/azure/sig#az-sig-create). I följande exempel skapas en resurs grupp med namnet *myGalleryRG* i *USA, östra* och ett galleri som heter *Galleri.*

```azurecli-interactive
az group create --name myGalleryRG --location eastus
az sig create --resource-group myGalleryRG --gallery-name myGallery
```

## <a name="share-the-gallery"></a>Dela galleriet

Du kan dela avbildningar över prenumerationer med hjälp av Role-Based Access Control (RBAC). Du kan dela bilder i galleriet, bild definitionen eller bild versions nivån. Alla användare som har Läs behörighet till en avbildnings version, även över prenumerationer, kan distribuera en virtuell dator med hjälp av avbildnings versionen.

Vi rekommenderar att du delar med andra användare på Galleri nivån. Om du vill hämta objekt-ID: t för galleriet använder du [AZ sig Visa](/cli/azure/sig#az-sig-show).

```azurecli-interactive
az sig show \
   --resource-group myGalleryRG \
   --gallery-name myGallery \
   --query id
```

Använd objekt-ID: t som ett omfång, tillsammans med en e-postadress och [AZ roll tilldelning skapa](/cli/azure/role/assignment#az-role-assignment-create) för att ge en användare åtkomst till det delade avbildnings galleriet. Ersätt `<email-address>` och `<gallery iD>` med din egen information.

```azurecli-interactive
az role assignment create \
   --role "Reader" \
   --assignee <email address> \
   --scope <gallery ID>
```

Mer information om hur du delar resurser med RBAC finns i [Hantera åtkomst med RBAC och Azure CLI](../articles/role-based-access-control/role-assignments-cli.md).