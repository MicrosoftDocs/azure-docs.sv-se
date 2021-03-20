---
title: Skapa en IoT Hub med hjälp av Azure CLI | Microsoft Docs
description: Lär dig hur du använder Azure CLI-kommandon för att skapa en resurs grupp och sedan skapa en IoT-hubb i resurs gruppen. Lär dig också hur du tar bort hubben.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: robinsh
ms.openlocfilehash: e9757b94330f889869080a0e961ab2728cee86f6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94659938"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Skapa en IoT-hubb med Azure CLI

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Den här artikeln visar hur du skapar en IoT Hub med Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Använd Azure CLI för att skapa en resursgrupp och lägg sedan till en IoT-hubb.

1. När du skapar en IoT-hubb måste du skapa den i en resurs grupp. Använd en befintlig resursgrupp eller kör följande [kommando för att skapa en resursgrupp](/cli/azure/resource):
    
   ```azurecli-interactive
   az group create --name {your resource group name} --location westus
   ```

   > [!TIP]
   > I föregående exempel skapas en resursgrupp i USA, västra. Du kan visa en lista över tillgängliga platser genom att köra det här kommandot: 
   >
   > ```azurecli-interactive
   > az account list-locations -o table
   > ```
   >

2. Kör följande [kommando för att skapa en IoT-hubb](/cli/azure/iot/hub#az-iot-hub-create) i din resursgrupp med ett globalt unikt namn för IoT-hubben:
    
   ```azurecli-interactive
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


Föregående kommando skapar en IoT-hubb i pris nivån S1 som du faktureras för. Mer information finns i [priser för Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="remove-an-iot-hub"></a>Ta bort ett IoT Hub

Du kan använda Azure CLI för att [ta bort en enskild resurs](/cli/azure/resource), till exempel en IoT-hubb eller ta bort en resurs grupp och alla dess resurser, inklusive IoT-hubbar.

Om du vill [ta bort en IoT-hubb](/cli/azure/iot/hub#az-iot-hub-delete)kör du följande kommando:

```azurecli-interactive
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

Om du vill [ta bort en resurs grupp](/cli/azure/group#az-group-delete) och alla dess resurser kör du följande kommando:

```azurecli-interactive
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder en IoT Hub finns i följande artiklar:

* [Guide för IoT Hub utvecklare](iot-hub-devguide.md)
* [Använda Azure Portal för att hantera IoT Hub](iot-hub-create-through-portal.md)