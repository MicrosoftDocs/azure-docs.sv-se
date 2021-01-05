---
title: Självstudie – ta bort en app som körs i Azure Service Fabric nät
description: I den här självstudien lär du dig hur du tar bort ett program som körs i Service Fabric Mesh och tar bort resurserna.
author: georgewallace
ms.topic: tutorial
ms.date: 01/11/2019
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: 4aa2fd08491616c1202cc19fb1a6b9bc8e89853c
ms.sourcegitcommit: e7179fa4708c3af01f9246b5c99ab87a6f0df11c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/30/2020
ms.locfileid: "97826059"
---
# <a name="tutorial-remove-an-application-and-resources"></a>Självstudie: Ta bort ett program och resurser

Den här självstudien är del fyra i en serie. Du får lära dig hur du tar bort ett program som körs som [tidigare har distribuerat till Service Fabric Mesh](service-fabric-mesh-tutorial-template-deploy-app.md). 

I del fyra i serien lär du dig att:

> [!div class="checklist"]
> * Ta bort ett program som körs i Service Fabric Mesh
> * Ta bort programresurserna

I den här självstudieserien får du lära du dig att:
> [!div class="checklist"]
> * [Distribuera ett program till Service Fabric Mesh med en mall](service-fabric-mesh-tutorial-template-deploy-app.md)
> * [Skala ett program som körs i Service Fabric Mesh](service-fabric-mesh-tutorial-template-scale-services.md)
> * [Uppgradera ett program som körs i Service Fabric Mesh](service-fabric-mesh-tutorial-template-upgrade-app.md)
> * Ta bort ett program

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Innan du börjar den här självstudien:

* Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

* Öppna [Azure Cloud Shell](service-fabric-mesh-howto-setup-cli.md), eller [installera Azure CLI och Service Fabric Mesh CLI lokalt](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli).

## <a name="delete-the-resource-group-and-all-the-resources"></a>Tar bort en resursgrupp och alla dess resurser

När de inte längre behövs kan du ta bort alla skapade resurser. Tidigare [skapade du en ny resursgrupp](service-fabric-mesh-tutorial-template-deploy-app.md#create-a-container-registry) som värd för Azure Container Registry-instansen och Service Fabric Mesh-programresurserna.  Du kan ta bort den här resursgruppen, vilket även tar bort alla associerade resurser.

```azurecli
az group delete --resource-group myResourceGroup
```

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="individually-delete-the-resources"></a>Ta bort resurserna individuellt
Du kan också ta bort ACR-instansen, Service Fabric Mesh-programmet och nätverksresurserna individuellt.

Ta bort ACR-instansen:

```azurecli
az acr delete --resource-group myResourceGroup --name myContainerRegistry
```

Ta bort Service Fabric Mesh-programmet:

```azurecli
az mesh app delete --resource-group myResourceGroup --name todolistapp
```

Ta bort nätverket:
```azurecli
az mesh network delete --resource-group myResourceGroup --name todolistappNetwork
```

## <a name="next-steps"></a>Nästa steg

I den här självstudiedelen lärde du dig att:

> [!div class="checklist"]
> * Ta bort ett program som körs i Service Fabric Mesh
> * Ta bort programresurserna
