---
title: Skapa en Core (SQL) API-databas och container med autoskalning för Azure Cosmos DB
description: Skapa en Core (SQL) API-databas och container med autoskalning för Azure Cosmos DB
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: a75177f7ac42c01fcce3bc406a0ad2886ee61b17
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107778795"
---
# <a name="create-an-azure-cosmos-core-sql-api-account-database-and-container-with-autoscale-using-azure-cli"></a>Skapa ett Azure Cosmos Core-API-konto, en databas och en container med autoskalning med Hjälp av Azure CLI
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.0.73 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="sample-script"></a>Exempelskript

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/sql/autoscale.sh "Create an Azure Cosmos DB Core (SQL) API account, database, and container with autoscale.")]

## <a name="clean-up-deployment"></a>Rensa distribution

När skriptexemplet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [az group create](/cli/azure/group#az_group_create) | Skapar en resursgrupp där alla resurser lagras. |
| [az cosmosdb create](/cli/azure/cosmosdb#az_cosmosdb_create) | Skapar ett Azure Cosmos DB-konto. |
| [az cosmosdb sql database create](/cli/azure/cosmosdb/sql/database#az_cosmosdb_sql_database_create) | Skapar en Azure Cosmos SQL-databas (Core). |
| [az cosmosdb sql container create](/cli/azure/cosmosdb/sql/container#az_cosmosdb_sql_container_create) | Skapar en Azure Cosmos SQL-container (Core). |
| [az group delete](/cli/azure/resource#az_resource_delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om cli Azure Cosmos DB finns i [Azure Cosmos DB CLI-dokumentationen](/cli/azure/cosmosdb).

Alla Azure Cosmos DB CLI-skriptexempel finns i Azure Cosmos DB [CLI GitHub Repository](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
