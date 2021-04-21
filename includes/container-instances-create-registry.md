---
title: ta med fil
description: ta med fil
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: include
ms.date: 08/13/2020
ms.author: danlep
ms.custom: include file, devx-track-azurecli
ms.openlocfilehash: 8d4178ea1f6f12def938dfc91e7ae0e6ee3b738c
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107786931"
---
## <a name="create-azure-container-registry"></a>Skapa Azure Container Registry

Innan du skapar containerregistret måste du ha en *resursgrupp* att distribuera den till. En resursgrupp är en logisk samling där alla Azure-resurser distribueras och hanteras.

Skapa en resursgrupp med kommandot [az group create][az-group-create]. I följande exempel skapas en resursgrupp med namnet *myResourceGroup* i regionen *eastus*:

```azurecli
az group create --name myResourceGroup --location eastus
```

När du har skapat resursgruppen skapar du ett Azure-containerregister med kommandot [az acr create][az-acr-create]. Namnet på containerregistret måste vara unikt i Azure och innehålla 5–50 alfanumeriska tecken. Ersätt `<acrName>` med ett unikt namn för ditt register:

```azurecli
az acr create --resource-group myResourceGroup --name <acrName> --sku Basic
```

Här är partiella utdata för ett nytt Azure-containerregister med namnet *mycontainerregistry082*:

```output
{
  "creationDate": "2020-07-16T21:54:47.297875+00:00",
  "id": "/subscriptions/<Subscription ID>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/mycontainerregistry082",
  "location": "eastus",
  "loginServer": "mycontainerregistry082.azurecr.io",
  "name": "mycontainerregistry082",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

I resten av den här självstudien använder vi `<acrName>` som platshållare för det containerregisternamn du väljer i det här steget.

## <a name="log-in-to-container-registry"></a>Logga in till containerregistret

Du måste logga in på din Azure Container Registry-instans innan du push-överför avbildningar till den. Använd kommandot [az acr login][az-acr-login] till att slutföra åtgärden. Du måste ange det unika namn du valde för containerregistret när du skapade det.

```azurecli
az acr login --name <acrName>
```

Exempel:

```azurecli
az acr login --name mycontainerregistry082
```

Kommandot returnerar `Login Succeeded` när det har slutförts:

```output
Login Succeeded
```

<!-- LINKS - Internal -->
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-group-create]: /cli/azure/group#az_group_create
