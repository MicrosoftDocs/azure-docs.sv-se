---
title: Skapa en Funktionsapp i en App Service plan – Azure CLI
description: Azure CLI-skriptexempel – Skapa en funktionsapp i en App Service plan
ms.assetid: 0e221db6-ee2d-4e16-9bf6-a456cd05b6e7
ms.topic: sample
ms.date: 07/03/2018
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 020bd064628554703bb375c06c72e68d4536a2a3
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97934449"
---
# <a name="create-a-function-app-in-an-app-service-plan"></a>Skapa en funktionsapp i en App Service plan

Det här exempelskriptet för Azure Functions skapar en funktionsapp som blir container åt dina funktioner. Funktionsappen som skapas använder en dedikerad App Service plan, vilket innebär att dina serverresurser alltid är på.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - I den här självstudien krävs version 2,0 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad. 

## <a name="sample-script"></a>Exempelskript

Det här skriptet skapar en Azure Function-app som använder en dedikerad [App Service plan](../dedicated-plan.md).

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-app-service-plan/create-function-app-app-service-plan.sh "Create an Azure Function on an App Service plan")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

Varje kommando i tabellen länkar till kommandospecifik dokumentation. I det här skriptet används följande kommandon:

| Kommando | Kommentarer |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az storage account create](/cli/azure/storage/account#az-storage-account-create) | Konfigurerar ett Azure Storage-konto. |
| [AZ functionapp plan Create](/cli/azure/functionapp/plan#az-functionapp-plan-create) | Skapar en Premium-plan. |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Skapar en funktionsapp i App Service-planen. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för Azure Functions finns i [Azure Functions-dokumentationen](../functions-cli-samples.md).
