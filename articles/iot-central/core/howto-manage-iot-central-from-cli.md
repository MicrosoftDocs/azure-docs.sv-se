---
title: Hantera IoT Central från Azure CLI | Microsoft Docs
description: I den här artikeln beskrivs hur du skapar och hanterar IoT Central program med CLI. Du kan visa, ändra och ta bort programmet med hjälp av CLI.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/27/2020
ms.topic: how-to
ms.custom: devx-track-azurecli
manager: philmea
ms.openlocfilehash: c30781cb83436e15a217a1d43c0e39facae9f52d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770442"
---
# <a name="manage-iot-central-from-azure-cli"></a>Hantera IoT Central från Azure CLI

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

I stället för att skapa IoT Central hantera program [på Azure IoT Central application manager-webbplatsen](https://aka.ms/iotcentral) kan du använda Azure [CLI för](/cli/azure/) att hantera dina program.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - Om du behöver köra CLI-kommandona i en annan Azure-prenumeration kan du läsa [Ändra den aktiva prenumerationen.](/cli/azure/manage-azure-subscriptions-azure-cli#change-the-active-subscription)

## <a name="create-an-application"></a>Skapa ett program

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

Använd kommandot [az iot central app create för](/cli/azure/iot/central/app#az_iot_central_app_create) att skapa ett IoT Central i din Azure-prenumeration. Exempel:

```azurecli-interactive
# Create a resource group for the IoT Central application
az group create --location "East US" \
    --name "MyIoTCentralResourceGroup"
```

```azurecli-interactive
# Create an IoT Central application
az iot central app create \
  --resource-group "MyIoTCentralResourceGroup" \
  --name "myiotcentralapp" --subdomain "mysubdomain" \
  --sku ST1 --template "iotc-pnp-preview" \
  --display-name "My Custom Display Name"
```

De här kommandona skapar först en resursgrupp i regionen USA, östra för programmet. I följande tabell beskrivs de parametrar som används med **kommandot az iot central app** create:

| Parameter         | Beskrivning |
| ----------------- | ----------- |
| resource-group    | Den resursgrupp som innehåller programmet. Den här resursgruppen måste redan finnas i din prenumeration. |
| location          | Som standard använder det här kommandot platsen från resursgruppen. För närvarande kan du skapa IoT Central program i **geografierna Australien, Asien och stillahavsområdet,** **Europa,** **USA,**  Storbritannien och Japan.  |
| name              | Namnet på programmet i Azure Portal. |
| Underdomän         | Underdomänen i url:en för programmet. I det här exemplet är program-URL:en `https://mysubdomain.azureiotcentral.com` . |
| sku               | För närvarande kan du använda **antingen ST1** eller **ST2.** Se [Azure IoT Central prissättning.](https://azure.microsoft.com/pricing/details/iot-central/) |
| mall          | Programmallen som ska användas. Se tabellen nedan för mer information. |
| visningsnamn      | Namnet på programmet som det visas i användargränssnittet. |

[!INCLUDE [iot-central-template-list](../../../includes/iot-central-template-list.md)]

## <a name="view-your-applications"></a>Visa dina program

Använd kommandot [az iot central app list för](/cli/azure/iot/central/app#az_iot_central_app_list) att visa IoT Central program och visa metadata.

## <a name="modify-an-application"></a>Ändra ett program

Använd kommandot [az iot central app update](/cli/azure/iot/central/app#az_iot_central_app_update) för att uppdatera metadata för ett IoT Central program. Om du till exempel vill ändra visningsnamnet för ditt program:

```azurecli-interactive
az iot central app update --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup \
  --set displayName="My new display name"
```

## <a name="remove-an-application"></a>Ta bort ett program

Använd kommandot [az iot central app delete för](/cli/azure/iot/central/app#az_iot_central_app_delete) att ta bort ett IoT Central program. Exempel:

```azurecli-interactive
az iot central app delete --name myiotcentralapp \
  --resource-group MyIoTCentralResourceGroup
```

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du hanterar Azure IoT Central från Azure CLI föreslår vi nästa steg:

> [!div class="nextstepaction"]
> [Administrera ditt program](howto-administer.md)
