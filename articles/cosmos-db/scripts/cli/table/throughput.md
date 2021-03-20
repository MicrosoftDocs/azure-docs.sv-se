---
title: Azure CLI-skript för data flöde (RU/s) åtgärder för Azure Cosmos DB Tabell-API resurser
description: Azure CLI-skript för data flöde (RU/s) åtgärder för Azure Cosmos DB Tabell-API resurser
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: sample
ms.date: 10/07/2020
ms.openlocfilehash: 6e76da71cb14122817090e64354babf5a618db8b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94562669"
---
# <a name="throughput-rus-operations-with-azure-cli-for-a-table-for-azure-cosmos-db-table-api"></a>Data flöde (RU/s)-åtgärder med Azure CLI för en tabell för Azure Cosmos DB Tabell-API
[!INCLUDE[appliesto-table-api](../../../includes/appliesto-table-api.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.12.1 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="sample-script"></a>Exempelskript

Det här skriptet skapar en Tabell-API tabell och uppdaterar sedan data flödet i tabellen. Skriptet migreras sedan från standard till autoskalning genom att läsa in värdet för autoskalning av data flödet när det har migrerats.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/table/throughput.sh "Throughput operations for Table API.")]

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
| [AZ cosmosdb Table Create](/cli/azure/cosmosdb/table#az-cosmosdb-table-create) | Skapar en Azure-Cosmos Tabell-API tabell. |
| [AZ cosmosdb Table data flödes uppdatering](/cli/azure/cosmosdb/table/throughput#az-cosmosdb-table-throughput-update) | Uppdaterings data flöde för en Azure-Cosmos Tabell-API tabell. |
| [AZ cosmosdb Table genomflödet Migrate](/cli/azure/cosmosdb/table/throughput#az-cosmosdb-table-throughput-migrate) | Migrera data flöde för en Azure Cosmos Tabell-API-tabell. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Cosmos DB CLI finns i [Azure Cosmos DB CLI-dokumentation](/cli/azure/cosmosdb).

Alla Azure Cosmos DB CLI-skript exempel finns i [Azure Cosmos DB CLI GitHub-lagringsplatsen](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
