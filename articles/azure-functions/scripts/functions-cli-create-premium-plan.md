---
title: Skapa en Function-app i en Premium plan – Azure CLI
description: Skapa en Function-app i en skalbar Premium-plan i Azure med hjälp av Azure CLI
ms.service: azure-functions
ms.topic: sample
ms.date: 11/23/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 714dd8d886f567bcdf07c93803662ef4b2c635f3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94565203"
---
# <a name="create-a-function-app-in-a-premium-plan---azure-cli"></a>Skapa en Function-app i en Premium plan – Azure CLI

Det här exempelskriptet för Azure Functions skapar en funktionsapp som blir container åt dina funktioner. Function-appen som skapas använder en [skalbar Premium-plan](../functions-premium-plan.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - I den här självstudien krävs version 2,0 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="sample-script"></a>Exempelskript

Det här skriptet skapar en Function-app med hjälp av en [Premium-plan](../functions-premium-plan.md).

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-premium-plan/create-function-app-premium-plan.sh "Create an Azure Function on an App Service plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

Varje kommando i tabellen länkar till kommandospecifik dokumentation. I det här skriptet används följande kommandon:

| Kommando | Kommentarer |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Konfigurerar ett Azure Storage-konto. |
| [AZ functionapp plan Create](/cli/azure/functionapp/plan#az-functionapp-plan-create) | Skapar en Premium-plan i en [angiven SKU](../functions-premium-plan.md#available-instance-skus). |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Skapar en funktionsapp i App Service-planen. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för Azure Functions finns i [Azure Functions-dokumentationen](../functions-cli-samples.md).
