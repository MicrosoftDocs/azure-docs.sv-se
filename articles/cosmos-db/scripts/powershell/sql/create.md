---
title: PowerShell-skript för att skapa Azure Cosmos DB SQL API-databas och container
description: Azure PowerShell skript – Azure Cosmos DB skapa SQL API-databas och behållare
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 05/13/2020
ms.author: mjbrown
ms.openlocfilehash: 2a098d3eff7bc4a8a78a880386145457b80b42d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98675570"
---
# <a name="create-a-database-and-container-for-azure-cosmos-db---sql-api"></a>Skapa en databas och behållare för Azure Cosmos DB-SQL API
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Det här exemplet kräver Azure PowerShell AZ 5.4.0 eller senare. Kör `Get-Module -ListAvailable Az` för att se vilka versioner som är installerade.
Om du behöver installera, se [installera Azure PowerShell-modulen](/powershell/azure/install-az-ps).

Kör [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) för att logga in på Azure.

## <a name="sample-script"></a>Exempelskript

Det här skriptet skapar ett Cosmos-konto för SQL-API i två regioner med konsekvens för sessionsläge, en databas och en behållare med en partitionsnyckel, anpassad indexerings princip, unik nyckel policy, TTL, dedikerat data flöde och senaste skrivarens WINS-konflikt lösnings princip med en anpassad matchnings Sök väg som ska användas när `multipleWriteLocations=true` .

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-sql-create.ps1 "Create an account, database, and container for SQL API")]

## <a name="clean-up-deployment"></a>Rensa distribution

När skriptexemplet har körts kan följande kommando användas för att ta bort resursgruppen och alla resurser som är kopplade till den.

```powershell
Remove-AzResourceGroup -ResourceGroupName "myResourceGroup"
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
|**Azure Cosmos DB**| |
| [New-AzCosmosDBAccount](/powershell/module/az.cosmosdb/new-azcosmosdbaccount) | Skapar ett Cosmos DB-konto. |
| [New-AzCosmosDBSqlDatabase](/powershell/module/az.cosmosdb/new-azcosmosdbsqldatabase) | Skapar ett Cosmos DB SQL Database. |
| [New-AzCosmosDBSqlUniqueKey](/powershell/module/az.cosmosdb/new-azcosmosdbsqluniquekey) | Skapar ett PSSqlUniqueKey-objekt som används som en parameter för New-AzCosmosDBSqlUniqueKeyPolicy. |
| [New-AzCosmosDBSqlUniqueKeyPolicy](/powershell/module/az.cosmosdb/new-azcosmosdbsqluniquekeypolicy) | Skapar ett PSSqlUniqueKeyPolicy-objekt som används som en parameter för New-AzCosmosDBSqlContainer. |
| [New-AzCosmosDBSqlCompositePath](/powershell/module/az.cosmosdb/new-azcosmosdbsqlcompositepath) | Skapar ett PSCompositePath-objekt som används som en parameter för New-AzCosmosDBSqlIndexingPolicy. |
| [New-AzCosmosDBSqlIncludedPathIndex](/powershell/module/az.cosmosdb/new-azcosmosdbsqlincludedpathindex) | Skapar ett PSIndexes-objekt som används som en parameter för New-AzCosmosDBSqlIncludedPath. |
| [New-AzCosmosDBSqlIncludedPath](/powershell/module/az.cosmosdb/new-azcosmosdbsqlincludedpath) | Skapar ett PSIncludedPath-objekt som används som en parameter för New-AzCosmosDBSqlIndexingPolicy. |
| [New-AzCosmosDBSqlIndexingPolicy](/powershell/module/az.cosmosdb/new-azcosmosdbsqlindexingpolicy) | Skapar ett PSSqlIndexingPolicy-objekt som används som en parameter för New-AzCosmosDBSqlContainer. |
| [New-AzCosmosDBSqlConflictResolutionPolicy](/powershell/module/az.cosmosdb/new-azcosmosdbsqlconflictresolutionpolicy) | Skapar ett PSSqlConflictResolutionPolicy-objekt som används som en parameter för New-AzCosmosDBSqlContainer. |
| [New-AzCosmosDBSqlContainer](/powershell/module/az.cosmosdb/new-azcosmosdbsqlcontainer) | Skapar en ny Cosmos DB SQL-behållare. |
|**Resurs grupper i Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/).