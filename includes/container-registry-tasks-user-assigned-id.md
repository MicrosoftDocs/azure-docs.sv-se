---
title: ta med fil
description: ta med fil
services: container-registry
author: dlepow
ms.service: container-registry
ms.topic: include
ms.date: 07/12/2019
ms.author: danlep
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: d81b6f5367efa92c9249956faa058441edf98561
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92755407"
---
### <a name="create-a-user-assigned-identity"></a>Skapa en användartilldelad identitet

Skapa en identitet med namnet *myACRTasksId* i din prenumeration med kommandot [AZ Identity Create][az-identity-create] . Du kan använda samma resurs grupp som du använde tidigare för att skapa ett behållar register, eller en annan.

```azurecli
az identity create \
  --resource-group myResourceGroup \
  --name myACRTasksId
```

Om du vill konfigurera den användarspecifika identiteten i följande steg, använder du kommandot [AZ Identity show][az-identity-show] för att lagra identitetens resurs-ID, huvud-ID och klient-ID i variabler.

```azurecli
# Get resource ID of the user-assigned identity
resourceID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query id --output tsv)

# Get principal ID of the task's user-assigned identity
principalID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query principalId --output tsv)

# Get client ID of the user-assigned identity
clientID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACRTasksId \
  --query clientId --output tsv)
```

<!-- LINKS - Internal -->
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-show]: /cli/azure/identity#az-identity-show
