---
title: Hantera elastiska pooler
description: Skapa och hantera Azure SQL Database elastiska pooler med hjälp av Azure Portal, PowerShell, Azure CLI, Transact-SQL (T-SQL) och Rest API.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein
ms.date: 03/12/2019
ms.custom: seoapril2019 sqldbrb=1, devx-track-azurecli
ms.openlocfilehash: 555b18a7edbc3cc00cd7dcaf7f4897a4608d52e7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787223"
---
# <a name="manage-elastic-pools-in-azure-sql-database"></a>Hantera elastiska pooler i Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Med en elastisk pool bestämmer du hur mycket resurser som den elastiska poolen kräver för att hantera arbetsbelastningen för dess databaser och mängden resurser för varje pooldatabas.

## <a name="azure-portal"></a>Azure Portal

Alla poolinställningar finns på en plats: **bladet Konfigurera** pool. Du kommer hit genom att söka efter en elastisk pool i Azure Portal och klicka på Konfigurera **pool** antingen överst på bladet eller på resursmenyn till vänster.

Härifrån kan du göra valfri kombination av följande ändringar och spara dem i en batch:

1. Ändra tjänstnivån för poolen
2. Skala prestanda (DTU eller virtuella kärnor) och lagring upp eller ned
3. Lägga till eller ta bort databaser till/från poolen
4. Ange en minsta (garanterad) och högsta prestandagräns för databaserna i poolerna
5. Granska kostnadssammanfattningen för att visa eventuella ändringar i fakturan som ett resultat av dina nya val

![Konfigurationsblad för elastisk pool](./media/elastic-pool-manage/configure-pool.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling är för Az.Sql-modulen. För dessa cmdlets, se [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Argumenten för kommandona i Az-modulen och i AzureRm-modulerna är betydligt identiska.

Om du vill skapa SQL Database hantera elastiska pooler och pooldatabaser med Azure PowerShell använder du följande PowerShell-cmdlets. Om du behöver installera eller uppgradera PowerShell kan du gå till [Installera Azure PowerShell modulen](/powershell/azure/install-az-ps). Information om hur du skapar och hanterar servrarna för en elastisk pool finns [i Skapa och hantera servrar.](logical-servers.md) Information om hur du skapar och hanterar brandväggsregler finns [i Skapa och hantera brandväggsregler med Hjälp av PowerShell.](firewall-configure.md#use-powershell-to-manage-server-level-ip-firewall-rules)

> [!TIP]
> Exempelskript för PowerShell finns i Skapa elastiska pooler och flytta databaser mellan pooler och från en pool med hjälp av [PowerShell](scripts/move-database-between-elastic-pools-powershell.md) och Använda PowerShell för att övervaka och skala en [elastisk SQL-pool i Azure SQL Database](scripts/monitor-and-scale-pool-powershell.md).
>

| Cmdlet | Beskrivning |
| --- | --- |
|[New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool)|Skapar en elastisk pool.|
|[Get-AzSqlElasticPool](/powershell/module/az.sql/get-azsqlelasticpool)|Hämtar elastiska pooler och deras egenskapsvärden.|
|[Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool)|Ändrar egenskaper för en elastisk pool Till exempel kan du använda **egenskapen StorageMB** för att ändra maxlagringen för en elastisk pool.|
|[Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool)|Tar bort en elastisk pool.|
|[Get-AzSqlElasticPoolActivity](/powershell/module/az.sql/get-azsqlelasticpoolactivity)|Hämtar status för åtgärder på en elastisk pool|
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Skapar en ny databas i en befintlig pool eller som en enkel databas. |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Hämtar en eller flera databaser.|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Anger egenskaper för en databas eller flyttar en befintlig databas till, från eller mellan elastiska pooler.|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Tar bort en databas.|

> [!TIP]
> Det kan ta tid att skapa många databaser i en elastisk pool när du är klar med portalen eller PowerShell-cmdlets som bara skapar en enkel databas i taget. Information om hur du automatiserar skapandet till en elastisk pool [finns i CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).

## <a name="azure-cli"></a>Azure CLI

Om du vill skapa SQL Database hantera elastiska pooler [med Azure CLI](/cli/azure)använder du följande Azure [CLI SQL Database kommandon.](/cli/azure/sql/db) Använd [Cloud Shell](../../cloud-shell/overview.md) för att köra CLI i webbläsaren eller [installera](/cli/azure/install-azure-cli) det på macOS, Linux eller Windows.

> [!TIP]
> Exempelskript för Azure CLI finns i Använda CLI för att flytta en databas i SQL Database i en [elastisk SQL-pool](scripts/move-database-between-elastic-pools-cli.md) och Använda Azure CLI för att skala en [elastisk SQL-pool i Azure SQL Database](scripts/scale-pool-cli.md).
>

| Cmdlet | Beskrivning |
| --- | --- |
|[az sql elastic-pool create](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create)|Skapar en elastisk pool.|
|[az sql elastic-pool list](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list)|Returnerar en lista över elastiska pooler på en server.|
|[az sql elastic-pool list-dbs](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list_dbs)|Returnerar en lista över databaser i en elastisk pool.|
|[az sql elastic-pool list-editions](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list_editions)|Innehåller även tillgängliga DTU-inställningar för pooler, lagringsgränser och inställningar per databas. För att minska ordagrannhet döljs ytterligare lagringsgränser och inställningar per databas som standard.|
|[az sql elastic-pool update](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update)|Uppdaterar en elastisk pool.|
|[az sql elastic-pool delete](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_delete)|Tar bort den elastiska poolen.|

## <a name="transact-sql-t-sql"></a>Transact-SQL (T-SQL)

Använd följande T-SQL-kommandon för att skapa och flytta databaser i befintliga elastiska pooler eller returnera information om en SQL Database elastisk pool med Transact-SQL. Du kan utfärda dessa kommandon med hjälp av Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs)eller något annat program som kan ansluta till en server och skicka Transact-SQL-kommandon. Information om hur du skapar och hanterar brandväggsregler med hjälp av T-SQL finns i [Hantera brandväggsregler med Hjälp av Transact-SQL.](firewall-configure.md#use-transact-sql-to-manage-ip-firewall-rules)

> [!IMPORTANT]
> Du kan inte skapa, uppdatera eller ta bort en Azure SQL Database elastisk pool med hjälp av Transact-SQL. Du kan lägga till eller ta bort databaser från en elastisk pool och du kan använda DMV:er för att returnera information om befintliga elastiska pooler.
>

| Kommando | Beskrivning |
| --- | --- |
|[CREATE DATABASE (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Skapar en ny databas i en befintlig pool eller som en enkel databas. Du måste vara ansluten till huvuddatabasen för att skapa en ny databas.|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Flytta en databas till, från eller mellan elastiska pooler.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Tar bort en databas.|
|[sys.elastic_pool_resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Returnerar resursanvändningsstatistik för alla elastiska pooler på en server. För varje elastisk pool finns det en rad för varje rapportfönster på 15 sekunder (fyra rader per minut). Detta inkluderar CPU, I/O, logg, lagringsförbrukning och samtidig begäran/sessionsanvändning av alla databaser i poolen.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Returnerar utgåvan (tjänstnivån), tjänstmålet (prisnivå) och eventuellt namnet på den elastiska poolen för en databas i SQL Database eller Azure Synapse Analytics. Om du är inloggad på huvuddatabasen på en server returneras information om alla databaser. För Azure Synapse Analytics måste du vara ansluten till huvuddatabasen.|

## <a name="rest-api"></a>REST-API

Om du vill skapa SQL Database hantera elastiska pooler och pooldatabaser använder du dessa REST API begäranden.

| Kommando | Beskrivning |
| --- | --- |
|[Elastiska pooler – Skapa eller uppdatera](/rest/api/sql/elasticpools/createorupdate)|Skapar en ny elastisk pool eller uppdaterar en befintlig elastisk pool.|
|[Elastiska pooler – Ta bort](/rest/api/sql/elasticpools/delete)|Tar bort den elastiska poolen.|
|[Elastiska pooler – Hämta](/rest/api/sql/elasticpools/get)|Hämtar en elastisk pool.|
|[Elastiska pooler – lista efter server](/rest/api/sql/elasticpools/listbyserver)|Returnerar en lista över elastiska pooler på en server.|
|[Elastiska pooler – Uppdatera](/rest/api/sql/2020-11-01-preview/elasticpools/update)|Uppdaterar en befintlig elastisk pool.|
|[Aktiviteter för elastisk pool](/rest/api/sql/elasticpoolactivities)|Returnerar elastiska poolaktiviteter.|
|[Aktiviteter för elastisk pooldatabas](/rest/api/sql/elasticpooldatabaseactivities)|Returnerar aktivitet på databaser i en elastisk pool.|
|[Databaser – Skapa eller uppdatera](/rest/api/sql/databases/createorupdate)|Skapar en ny databas eller uppdaterar en befintlig databas.|
|[Databaser – Hämta](/rest/api/sql/databases/get)|Hämtar en databas.|
|[Databaser – lista efter elastisk pool](/rest/api/sql/databases/listbyelasticpool)|Returnerar en lista över databaser i en elastisk pool.|
|[Databaser – lista efter server](/rest/api/sql/databases/listbyserver)|Returnerar en lista med databaser på en server.|
|[Databaser – Uppdatera](/rest/api/sql/databases/update)|Uppdaterar en befintlig databas.|

## <a name="next-steps"></a>Nästa steg

* Läs mer om designmönster för SaaS-program med elastiska pooler i [Designmönster för SaaS-program med flera klienter med Azure SQL Database](saas-tenancy-app-design-patterns.md).
* En SaaS-självstudiekurs om att använda elastiska pooler finns i [Introduktion till Wingtip SaaS-programmet](saas-dbpertenant-wingtip-app-overview.md).
