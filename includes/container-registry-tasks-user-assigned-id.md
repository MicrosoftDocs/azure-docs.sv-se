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
ms.openlocfilehash: 70bd79ef944e5918d750a130bd2e2b2c2b656bf4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781152"
---
### <a name="create-a-user-assigned-identity"></a>Skapa en användartilldelad identitet

Skapa en identitet med *namnet myACRTasksId i* din prenumeration med hjälp av [kommandot az identity create.][az-identity-create] Du kan använda samma resursgrupp som du använde tidigare för att skapa ett containerregister eller ett annat.

```azurecli
az identity create \
  --resource-group myResourceGroup \
  --name myACRTasksId
```

Om du vill konfigurera den användartilldelningsidentiteten i följande steg använder du kommandot [az identity show][az-identity-show] för att lagra identitetens resurs-ID, huvudnamns-ID och klient-ID i variabler.

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
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-identity-show]: /cli/azure/identity#az_identity_show
