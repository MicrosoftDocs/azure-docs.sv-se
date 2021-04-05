---
title: Skapa en Function-app med Azure Cosmos DB – Azure CLI
description: Exempel på Azure CLI-skript – Skapa en Azure-funktion som ansluter till Azure Cosmos DB
ms.topic: sample
ms.date: 07/03/2018
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 9ec4d3cb9d47608aa98075ba98aacfde51f341cd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97934432"
---
# <a name="create-an-azure-function-that-connects-to-an-azure-cosmos-db"></a>Skapa en Azure-funktion som ansluter till Azure Cosmos DB

Det här exempelskriptet för Azure Functions skapar en funktionsapp och ansluter funktionen till en Azure Cosmos DB-databas. Appinställningen som skapas och som innehåller anslutningen kan användas med en [lagringsutlösare eller bindning för Azure Cosmos DB](../functions-bindings-cosmosdb.md).

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

 - I den här självstudien krävs version 2,0 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad. 

## <a name="sample-script"></a>Exempelskript

Det här exemplet skapar en Azure-funktionsapp och lägger till en Cosmos DB-slutpunkt och åtkomstnyckel i appinställningar.

[!code-azurecli-interactive[main](../../../cli_scripts/azure-functions/create-function-app-connect-to-cosmos-db/create-function-app-connect-to-cosmos-db.sh "Create an Azure Function that connects to an Azure Cosmos DB")]

[!INCLUDE [cli-script-clean-up](../../../includes/cli-script-clean-up.md)]

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon: varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Skapa en resursgrupp med plats |
| [az storage accounts create](/cli/azure/storage/account#az-storage-account-create) | Skapa ett lagringskonto |
| [az functionapp create](/cli/azure/functionapp#az-functionapp-create) | Skapar en Function-app i [förbruknings planen](../consumption-plan.md)utan server. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Skapa en Azure Cosmos DB-databas. |
| [AZ cosmosdb show](/cli/azure/cosmosdb#az-cosmosdb-show)| Hämtar databas konto anslutningen. |
| [az cosmosdb list-keys](/cli/azure/cosmosdb#az-cosmosdb-list-keys)| Hämtar nycklar för databasen. |
| [AZ functionapp config appSettings set](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set) | Anger anslutnings strängen som en app-inställning i Function-appen. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure CLI finns i [Azure CLI-dokumentationen](/cli/azure).

Ytterligare CLI-skriptexempel för Azure Functions finns i [Azure Functions-dokumentationen](../functions-cli-samples.md).




