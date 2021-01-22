---
title: PowerShell-skript för att skapa Azure Cosmos DB API för Cassandra tecken utrymme och tabell
description: Azure PowerShell skript – Azure Cosmos DB skapa API för Cassandrat tecken utrymme och tabell
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: sample
ms.date: 05/13/2020
ms.author: mjbrown
ms.openlocfilehash: 3d5dec36481a57eadeb68d7ec3182cff76d6b081
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98684427"
---
# <a name="create-a-keyspace-and-table-for-azure-cosmos-db---cassandra-api"></a>Skapa ett tecken utrymme och en tabell för Azure Cosmos DB-API för Cassandra
[!INCLUDE[appliesto-cassandra-api](../../../includes/appliesto-cassandra-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Det här exemplet kräver Azure PowerShell AZ 5.4.0 eller senare. Kör `Get-Module -ListAvailable Az` för att se vilka versioner som är installerade.
Om du behöver installera, se [installera Azure PowerShell-modulen](/powershell/azure/install-az-ps).

Kör [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) för att logga in på Azure.

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/cassandra/ps-cassandra-create.ps1 "Create a keyspace and table for Cassandra API")]

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
| [New-AzCosmosDBCassandraKeyspace](/powershell/module/az.cosmosdb/new-azcosmosdbcassandrakeyspace) | Skapar ett Cosmos DB API för Cassandrat tecken utrymme. |
| [New-AzCosmosDBCassandraClusterKey](/powershell/module/az.cosmosdb/new-azcosmosdbcassandraclusterkey) | Skapar en Cosmos DB API för Cassandra kluster nyckel. |
| [New-AzCosmosDBCassandraColumn](/powershell/module/az.cosmosdb/new-azcosmosdbcassandracolumn) | Skapar en Cosmos DB API för Cassandra-kolumn. |
| [New-AzCosmosDBCassandraSchema](/powershell/module/az.cosmosdb/new-azcosmosdbcassandraschema) | Skapar ett Cosmos DB API för Cassandras schema. |
| [New-AzCosmosDBCassandraTable](/powershell/module/az.cosmosdb/new-azcosmosdbcassandratable) | Skapar en Cosmos DB API för Cassandra tabell. |
|**Resurs grupper i Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/).