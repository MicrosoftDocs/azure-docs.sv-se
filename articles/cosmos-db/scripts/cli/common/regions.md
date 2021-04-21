---
title: Lägga till regioner, ändra redundansprioritet, utlösa redundans för ett Azure Cosmos-konto
description: Lägga till regioner, ändra redundansprioritet, utlösa redundans för ett Azure Cosmos-konto
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 4a1a061945fe1c6c6a95eb62d286d40a158281ca
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770832"
---
# <a name="add-regions-change-failover-priority-trigger-failover-for-an-azure-cosmos-account-using-azure-cli"></a>Lägga till regioner, ändra redundansprioritet, utlösa redundans för ett Azure Cosmos-konto med Hjälp av Azure CLI
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.9.1 eller senare av Azure CLI. Om du Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="sample-script"></a>Exempelskript

Det här skriptet visar tre åtgärder.

- Lägg till en region i ett befintligt Azure Cosmos-konto.
- Ändra regional redundansprioritet (gäller konton som använder automatisk redundans)
- Utlösa en manuell redundans från primära till sekundära regioner (gäller konton med manuell redundans)

> [!NOTE]
> Det går inte att lägga till och ta bort regionåtgärder på ett Cosmos-konto när du ändrar andra egenskaper.

> [!NOTE]
> Det här exemplet visar hur du använder ett SQL (Core) API-konto, men dessa åtgärder är identiska i alla databas-API:er i Cosmos DB.

[!code-azurecli-interactive[main](../../../../../cli_scripts/cosmosdb/common/regions.sh "Regional operations for Cosmos DB.")]

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
| [az cosmosdb update](/cli/azure/cosmosdb#az_cosmosdb_update) | Uppdaterar ett Azure Cosmos DB (lägg till eller ta bort region). |
| [az cosmosdb failover-priority-change](/cli/azure/cosmosdb#az_cosmosdb_failover_priority_change) | Uppdatera redundansprioritet eller utlösa redundans för ett Azure Cosmos DB konto. |
| [az group delete](/cli/azure/resource#az_resource_delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om cli Azure Cosmos DB finns i [cli Azure Cosmos DB dokumentationen](/cli/azure/cosmosdb).

Alla Azure Cosmos DB CLI-skriptexempel finns i Azure Cosmos DB [CLI GitHub Repository](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
