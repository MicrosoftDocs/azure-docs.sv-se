---
title: Skapa en IoT Hub med Hjälp av Azure CLI | Microsoft Docs
description: Lär dig hur du använder Azure CLI-kommandon för att skapa en resursgrupp och sedan skapa en IoT-hubb i resursgruppen. Lär dig också hur du tar bort hubben.
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/23/2018
ms.author: robinsh
ms.openlocfilehash: eff0085a4a739e0831b25b1bd28cd234fdbcde3d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766469"
---
# <a name="create-an-iot-hub-using-the-azure-cli"></a>Skapa en IoT-hubb med Hjälp av Azure CLI

[!INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

Den här artikeln visar hur du skapar en IoT-hubb med Hjälp av Azure CLI.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

## <a name="create-an-iot-hub"></a>Skapa en IoT Hub

Använd Azure CLI för att skapa en resursgrupp och lägg sedan till en IoT-hubb.

1. När du skapar en IoT-hubb måste du skapa den i en resursgrupp. Använd en befintlig resursgrupp eller kör följande [kommando för att skapa en resursgrupp](/cli/azure/resource):
    
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

2. Kör följande [kommando för att skapa en IoT-hubb](/cli/azure/iot/hub#az_iot_hub_create) i din resursgrupp med ett globalt unikt namn för IoT-hubben:
    
   ```azurecli-interactive
   az iot hub create --name {your iot hub name} \
      --resource-group {your resource group name} --sku S1
   ```

   [!INCLUDE [iot-hub-pii-note-naming-hub](../../includes/iot-hub-pii-note-naming-hub.md)]


Föregående kommando skapar en IoT-hubb på prisnivån S1 som du debiteras för. Mer information finns i [Azure IoT Hub prissättning.](https://azure.microsoft.com/pricing/details/iot-hub/)

## <a name="remove-an-iot-hub"></a>Ta bort en IoT Hub

Du kan använda Azure CLI för att ta bort en enskild [resurs,](/cli/azure/resource)till exempel en IoT-hubb, eller ta bort en resursgrupp och alla dess resurser, inklusive alla IoT-hubbar.

Om [du vill ta bort en IoT-hubb](/cli/azure/iot/hub#az_iot_hub_delete)kör du följande kommando:

```azurecli-interactive
az iot hub delete --name {your iot hub name} -\
  -resource-group {your resource group name}
```

Om [du vill ta bort en](/cli/azure/group#az_group_delete) resursgrupp och alla dess resurser kör du följande kommando:

```azurecli-interactive
az group delete --name {your resource group name}
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder en IoT-hubb finns i följande artiklar:

* [IoT Hub utvecklarguide](iot-hub-devguide.md)
* [Använda Azure Portal för att hantera IoT Hub](iot-hub-create-through-portal.md)