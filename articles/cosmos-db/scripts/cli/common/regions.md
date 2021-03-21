---
title: Lägg till regioner, ändra prioritet för redundans, Utlös redundans för ett Azure Cosmos-konto
description: Lägg till regioner, ändra prioritet för redundans, Utlös redundans för ett Azure Cosmos-konto
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 07/29/2020
ms.openlocfilehash: 0ae724f9900eea713af7d295eba26bf03cdf471a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94563247"
---
# <a name="add-regions-change-failover-priority-trigger-failover-for-an-azure-cosmos-account-using-azure-cli"></a>Lägg till regioner, ändra prioritet för redundans, Utlös redundans för ett Azure Cosmos-konto med Azure CLI
[!INCLUDE[appliesto-all-apis](../../../includes/appliesto-all-apis.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../../../includes/azure-cli-prepare-your-environment.md)]

- Den här artikeln kräver version 2.9.1 eller senare av Azure CLI. Om du använder Azure Cloud Shell är den senaste versionen redan installerad.

## <a name="sample-script"></a>Exempelskript

Det här skriptet visar tre åtgärder.

- Lägg till en region i ett befintligt Azure Cosmos-konto.
- Ändra prioritet för regional redundans (gäller konton med automatisk redundans)
- Utlös en manuell redundansväxling från primär till sekundär region (gäller konton med manuell redundans)

> [!NOTE]
> Det går inte att lägga till och ta bort regions åtgärder på ett Cosmos-konto medan andra egenskaper ändras.

> [!NOTE]
> Det här exemplet demonstrerar användningen av ett SQL-API-konto men dessa åtgärder är identiska för alla databas-API: er i Cosmos DB.

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
| [az group create](/cli/azure/group#az-group-create) | Skapar en resursgrupp där alla resurser lagras. |
| [az cosmosdb create](/cli/azure/cosmosdb#az-cosmosdb-create) | Skapar ett Azure Cosmos DB-konto. |
| [az cosmosdb update](/cli/azure/cosmosdb#az-cosmosdb-update) | Uppdaterar ett Azure Cosmos DB konto (Lägg till eller ta bort region). |
| [AZ cosmosdb redundans-prioritet-ändra](/cli/azure/cosmosdb#az-cosmosdb-failover-priority-change) | Uppdatera växlings prioritet eller utlös redundans för ett Azure Cosmos DB-konto. |
| [az group delete](/cli/azure/resource#az-resource-delete) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Cosmos DB CLI finns i [Azure Cosmos DB CLI-dokumentation](/cli/azure/cosmosdb).

Alla Azure Cosmos DB CLI-skript exempel finns i [Azure Cosmos DB CLI GitHub-lagringsplatsen](https://github.com/Azure-Samples/azure-cli-samples/tree/master/cosmosdb).
