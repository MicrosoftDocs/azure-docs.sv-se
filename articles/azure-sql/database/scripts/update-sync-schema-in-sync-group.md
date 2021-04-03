---
title: 'PowerShell: uppdatera SQL Data Sync Sync-schema'
description: Azure PowerShell-exempelskript för uppdatering av synkroniseringsschemat för SQL Data Sync
services: sql-database
ms.service: sql-database
ms.subservice: data-movement
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 7d346d1ff30c138667749822b258bab4c6a621f4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92792726"
---
# <a name="use-powershell-to-update-the-sync-schema-in-an-existing-sync-group"></a>Använd PowerShell för att uppdatera synkroniseringsschemat i en befintlig synkroniseringsgrupp
[!INCLUDE[appliesto-sqldb](../../includes/appliesto-sqldb.md)]

I det här Azure PowerShell-exemplet uppdateras synkroniseringsschemat i en befintlig SQL Data Sync Sync-grupp. När du synkroniserar flera tabeller får du hjälp av det här skriptet att uppdatera synkroniseringsschemat på ett effektivt sätt. Det här exemplet visar hur skriptet **UpdateSyncSchema** används. Det finns på GitHub som [UpdateSyncSchema.ps1](https://github.com/Microsoft/sql-server-samples/tree/master/samples/features/sql-data-sync/UpdateSyncSchema.ps1).

[!INCLUDE [quickstarts-free-trial-note](../../../../includes/quickstarts-free-trial-note.md)]
[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]
[!INCLUDE [cloud-shell-try-it.md](../../../../includes/cloud-shell-try-it.md)]

Om du väljer att installera och använda PowerShell lokalt kräver den här självstudien AZ PowerShell-1.4.0 eller senare. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul). Om du kör PowerShell lokalt måste du också köra `Connect-AzAccount` för att skapa en anslutning till Azure.

En översikt över SQL Data Sync finns i [Synkronisera data i flera moln och lokala databaser med Azure SQL Data Sync](../sql-data-sync-data-sql-server-sql-database.md).

> [!IMPORTANT]
> SQL Data Sync stöder inte Azure SQL-hanterad instans för tillfället.

## <a name="examples"></a>Exempel

### <a name="add-all-tables-to-the-sync-schema"></a>Lägg till alla tabeller i synkroniseringsschemat

I följande exempel uppdateras databasschemat och alla giltiga tabeller läggs till i synkroniseringsschemat i hubbdatabasen.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscriptionId> -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> `
    -SyncGroupName <syncGroupName> -RefreshDatabaseSchema $true -AddAllTables $true
```

### <a name="add-and-remove-tables-and-columns"></a>Lägga till och ta bort tabeller och kolumner

I följande exempel läggs `[dbo].[Table1]` och `[dbo].[Table2].[Column1]` till i synkroniseringsschemas, och `[dbo].[Table3]` tas bort.

```powershell-interactive
UpdateSyncSchema.ps1 -SubscriptionId <subscriptionId> -ResourceGroupName <resourceGroupName> -ServerName <serverName> -DatabaseName <databaseName> `
    -SyncGroupName <syncGroupName> -TablesAndColumnsToAdd "[dbo].[Table1],[dbo].[Table2].[Column1]" -TablesAndColumnsToRemove "[dbo].[Table3]"
```

## <a name="script-parameters"></a>Skriptparametrar

Skriptet **UpdateSyncSchema** innehåller följande parametrar:

| Parameter | Kommentarer |
|---|---|
| $subscriptionId | Den prenumeration där synkroniseringsgruppen skapas. |
| $resourceGroupName | Den resursgrupp där synkroniseringsgruppen skapas.|
| $serverName | Hubbdatabasens servernamn.|
| $databaseName | Hubbdatabasens namn. |
| $syncGroupName | Synkroniseringsgruppens namn. |
| $memberName | Ange medlemsnamnet om du vill läsa in databasschemat från synkroniseringsmedlemmen i stället för från hubbdatabasen. Lämna den här parametern tom om du vill läsa in databasschemat från hubben. |
| $timeoutInSeconds | Tidsgräns när skriptet uppdaterar databasschemat. Standardvärdet är 900 sekunder. |
| $refreshDatabaseSchema | Ange om skriptet behöver uppdatera databasschemat. Om ditt databas schema har ändrats från den tidigare konfigurationen (till exempel om du har lagt till en ny tabell eller på nytt-kolumn) måste du uppdatera schemat innan du konfigurerar om det. Standardvärdet är false. |
| $addAllTables | Om värdet är true läggs alla giltiga tabeller och kolumner till i synkroniseringsschemat. Värdena för $TablesAndColumnsToAdd och $TablesAndColumnsToRemove ignoreras. |
| $tablesAndColumnsToAdd | Ange tabeller eller kolumner som ska läggas till i synkroniseringsschemat. Alla tabell- och kolumnnamn måste vara helt avgränsade med schemanamnet. Exempel: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Flera tabell-eller kolumn namn kan anges och avgränsas med kommatecken (,). |
| $tablesAndColumnsToRemove | Ange tabeller eller kolumner som ska tas bort från synkroniseringsschemat. Alla tabell- och kolumnnamn måste vara helt avgränsade med schemanamnet. Exempel: `[dbo].[Table1]`, `[dbo].[Table2].[Column1]`. Flera tabell-eller kolumn namn kan anges och avgränsas med kommatecken (,). |

## <a name="script-explanation"></a>Förklaring av skript

Skriptet **UpdateSyncSchema** använder följande parametrar. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Kommentarer |
|---|---|
| [Get-AzSqlSyncGroup](/powershell/module/az.sql/get-azsqlsyncgroup) | Returnerar information om en Sync-grupp. |
| [Uppdatera – AzSqlSyncGroup](/powershell/module/az.sql/update-azsqlsyncgroup) | Uppdaterar en synkroniseringsgrupp. |
| [Get-AzSqlSyncMember](/powershell/module/az.sql/get-azsqlsyncmember) | Returnerar information om en synkroniserad medlem. |
| [Get-AzSqlSyncSchema](/powershell/module/az.sql/get-azsqlsyncschema) | Returnerar information om ett synkroniseringsschema. |
| [Uppdatera – AzSqlSyncSchema](/powershell/module/az.sql/update-azsqlsyncschema) | Uppdaterar ett synkroniseringsschema. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](/powershell/azure/).

Ytterligare PowerShell-skriptexempel för SQL Database finns i [PowerShell-skript för Azure SQL Database](../powershell-script-content-guide.md).

Mer information om SQL Data Sync finns i:

- Översikt – [synkronisera data mellan Azure SQL Database och SQL Server med SQL Data Sync i Azure](../sql-data-sync-data-sql-server-sql-database.md)
- Konfigurera Data Sync
    - Använd Azure Portal- [självstudie: konfigurera SQL Data Sync för att synkronisera data mellan Azure SQL Database och SQL Server](../sql-data-sync-sql-server-configure.md)
    - Använd PowerShell
        -  [Använd PowerShell för att synkronisera data mellan flera databaser i Azure SQL Database](sql-data-sync-sync-data-between-sql-databases.md)
        -  [Använd PowerShell för att synkronisera data mellan Azure SQL Database och SQL Server](sql-data-sync-sync-data-between-azure-onprem.md)
- Data Sync-agent – [Data Sync-agent för SQL Data Sync i Azure](../sql-data-sync-agent-overview.md)
- Bästa praxis – [metod tips för SQL Data Sync i Azure](../sql-data-sync-best-practices.md)
- Övervaka [SQL Data Sync med Azure Monitor loggar](../monitor-tune-overview.md)
- Felsök – [Felsök problem med SQL Data Sync i Azure](../sql-data-sync-troubleshoot.md)
- Uppdatera synkroniseringsschemat
    - Använda Transact-SQL – [automatisera replikeringen av schema ändringar i SQL Data Sync i Azure](../sql-data-sync-update-sync-schema.md)

Mer information om SQL Database finns i:

- [Översikt över SQL Database](../sql-database-paas-overview.md)
- [Livscykelhantering för databas](/previous-versions/sql/sql-server-guides/jj907294(v=sql.110))