---
title: Azure CLI-skript för data flöde (RU/s) åtgärder för Azure Cosmos DB-API för MongoDB-resurser
description: Azure CLI-skript för data flöde (RU/s) åtgärder för Azure Cosmos DB-API för MongoDB-resurser
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: 54aebb48986b94dbebea400e6b6ee155a54f514b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94566222"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-database-or-graph-for-azure-cosmos-db-api-for-mongodb"></a>Data flöde (RU/s)-åtgärder med Azure CLI för en databas eller graf för Azure Cosmos DB API för MongoDB
[!INCLUDE[appliesto-mongodb-api](../../../includes/appliesto-mongodb-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.12.1 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="sample-script"></a>Exempelskript

Det här skriptet skapar en MongoDB-databas med delat data flöde och samling med dedikerat data flöde och uppdaterar sedan data flödet för båda. Skriptet migreras sedan från standard till autoskalning genom att läsa in värdet för autoskalning av data flödet när det har migrerats.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/mongodb/throughput.sh "Throughput operations for Azure Cosmos DB API for MongoDB.")]

## <a name="clean-up-deployment"></a>Rensa distribution

När skriptexemplet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den.

```azurecli-interactive
az group delete --name $resourceGroupName
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Skapar ett Azure Cosmos DB-konto. |
| [AZ cosmosdb MongoDB Database Create](/cli/azure/cosmosdb/mongodb/database#az-cosmosdb-mongodb-database-create) | Skapar en Azure Cosmos MongoDB API-databas. |
| [Skapa AZ cosmosdb MongoDB Collection Create](/cli/azure/cosmosdb/mongodb/collection#az-cosmosdb-mongodb-collection-create) | Skapar en Azure Cosmos MongoDB API-samling. |
| [AZ cosmosdb MongoDB Database genomflödet Update](/cli/azure/cosmosdb/mongodb/database/throughput#az-cosmosdb-mongodb-database-throughput-update) | Uppdatera ru: er för en Azure Cosmos MongoDB API-databas. |
| [AZ cosmosdb MongoDB Collection data flödes uppdatering](/cli/azure/cosmosdb/mongodb/collection/throughput#az-cosmosdb-mongodb-collection-throughput-update) | Uppdatera ru: er för en Azure Cosmos MongoDB API-samling. |
| [AZ cosmosdb MongoDB Database genomflödet Migrate](/cli/azure/cosmosdb/mongodb/database/throughput#az-cosmosdb-mongodb-database-throughput-migrate) | Migrera data flöde för en databas. |
| [AZ cosmosdb MongoDB Collection data flöde migrera](/cli/azure/cosmosdb/mongodb/collection/throughput#az-cosmosdb-mongodb-collection-throughput-migrate) | Migrera data flöde för en samling. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Cosmos DB CLI finns i [Azure Cosmos DB CLI-dokumentation](/cli/azure/cosmosdb).

Alla Azure Cosmos DB CLI-skript exempel finns i [Azure Cosmos DB CLI GitHub-lagringsplatsen](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
