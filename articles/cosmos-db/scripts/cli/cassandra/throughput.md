---
title: Azure CLI-skript för dataflödesåtgärder (RU/s) för Azure Cosmos DB API för Cassandra resurser
description: Azure CLI-skript för dataflödesåtgärder (RU/s) för Azure Cosmos DB API för Cassandra resurser
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: 765ad939c8de9fb1b8c6b9c41cb2f7f685657d54
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770984"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-keyspace-or-table-for-azure-cosmos-db---cassandra-api"></a>Dataflödesåtgärder (RU/s) med Azure CLI för ett nyckelutrymme eller en tabell för Azure Cosmos DB – API för Cassandra
[!INCLUDE[appliesto-cassandra-api](../../../includes/appliesto-cassandra-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.12.1 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="sample-script"></a>Exempelskript

Det här skriptet skapar ett Cassandra-nyckelutrymme med delat dataflöde och en Cassandra-tabell med dedikerat dataflöde och uppdaterar sedan dataflödet för både nyckelutrymmet och tabellen. Skriptet migrerar sedan från standard till dataflöde för automatisk skalning och läser sedan värdet för dataflödet för autoskalning när det har migrerats.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/cassandra/throughput.sh "Throughput operations for Cassandra keyspace and table.")]

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
| [az cosmosdb cassandra keyspace create](/cli/azure/cosmosdb/cassandra/keyspace#az_cosmosdb_cassandra_keyspace_create) | Skapar ett Azure Cosmos Cassandra-nyckelutrymme. |
| [az cosmosdb cassandra table create](/cli/azure/cosmosdb/cassandra/table#az_cosmosdb_cassandra_table_create) | Skapar en Azure Cosmos Cassandra-tabell. |
| [az cosmosdb cassandra keyspace throughput update](/cli/azure/cosmosdb/cassandra/keyspace/throughput#az_cosmosdb_cassandra_keyspace_throughput_update) | Uppdatera RU/s för ett Azure Cosmos Cassandra-nyckelutrymme. |
| [az cosmosdb cassandra table throughput update](/cli/azure/cosmosdb/cassandra/table/throughput#az_cosmosdb_cassandra_table_throughput_update) | Uppdatera RU/s för en Azure Cosmos Cassandra-tabell. |
| [az cosmosdb cassandra keyspace throughput migrate](/cli/azure/cosmosdb/cassandra/keyspace/throughput#az_cosmosdb_cassandra_keyspace_throughput_migrate) | Migrera dataflöde för ett Azure Cosmos Cassandra-nyckelutrymme. |
| [az cosmosdb cassandra table throughput migrate](/cli/azure/cosmosdb/cassandra/table/throughput#az_cosmosdb_cassandra_table_throughput_migrate) | Migrera dataflöde för en Azure Cosmos Cassandra-tabell. |
| [az group delete](/cli/azure/resource#az_resource_delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om cli Azure Cosmos DB finns i [Azure Cosmos DB CLI-dokumentationen](/cli/azure/cosmosdb).

Alla Azure Cosmos DB CLI-skriptexempel finns i Azure Cosmos DB [CLI GitHub Repository](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
