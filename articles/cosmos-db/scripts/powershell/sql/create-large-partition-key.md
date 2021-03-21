---
title: PowerShell-skript för att skapa en Azure Cosmos DB behållare med en stor partitionsnyckel
description: Azure PowerShell skript exempel – skapa en behållare med en stor partitionsnyckel i ett Azure Cosmos DB konto
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 05/13/2020
ms.author: mjbrown
ms.openlocfilehash: aaa37bda077e8ee8c5142ca9efe772a59cda0063
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98677869"
---
# <a name="create-a-container-with-a-large-partition-key-in-an-azure-cosmos-db-account-using-powershell"></a>Skapa en behållare med en stor partitionsnyckel i ett Azure Cosmos DB konto med hjälp av PowerShell
[!INCLUDE[appliesto-sql-api](../../../includes/appliesto-sql-api.md)]

[!INCLUDE [updated-for-az](../../../../../includes/updated-for-az.md)]

Det här exemplet kräver Azure PowerShell AZ 5.4.0 eller senare. Kör `Get-Module -ListAvailable Az` för att se vilka versioner som är installerade.
Om du behöver installera, se [installera Azure PowerShell-modulen](/powershell/azure/install-az-ps).

Kör [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) för att logga in på Azure.

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../../../powershell_scripts/cosmosdb/sql/ps-container-large-partition-key.ps1 "Create a container with a large partition key in an Azure Cosmos account")]

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
| [New-AzCosmosDBSqlContainer](/powershell/module/az.cosmosdb/new-azcosmosdbsqlcontainer) | Skapar en Cosmos DB SQL-behållare. |
|**Resurs grupper i Azure**| |
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/).