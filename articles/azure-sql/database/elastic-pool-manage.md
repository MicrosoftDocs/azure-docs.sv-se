---
title: Hantera elastiska pooler
description: Skapa och hantera Azure SQL Database elastiska pooler med hjälp av Azure Portal, PowerShell, Azure CLI, Transact-SQL (T-SQL) och REST API.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: sstein
ms.date: 03/12/2019
ms.custom: seoapril2019 sqldbrb=1, devx-track-azurecli
ms.openlocfilehash: 9c9af6e3bc3dfd798f4b3f0cad9319aa573c425d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96455990"
---
# <a name="manage-elastic-pools-in-azure-sql-database"></a>Hantera elastiska pooler i Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Med en elastisk pool fastställer du mängden resurser som den elastiska poolen kräver för att hantera arbets belastningen för databaserna och mängden resurser för varje databas i poolen.

## <a name="azure-portal"></a>Azure Portal

Du hittar alla poolinställningarna på samma plats: bladet **Konfigurera pool** . Hämta en elastisk pool i Azure Portal och klicka på **Konfigurera pool** , antingen längst upp på bladet eller på resurs menyn till vänster.

Härifrån kan du göra en kombination av följande ändringar och spara alla i en batch:

1. Ändra tjänst nivån för poolen
2. Skala prestandan (DTU eller virtuella kärnor) och lagring upp eller ned
3. Lägga till eller ta bort databaser i/från poolen
4. Ange lägsta (garanterade) och högsta prestanda gräns för databaserna i pooler
5. Granska kostnads översikten om du vill visa alla ändringar i fakturan som ett resultat av dina nya val

![Bladet konfiguration av Elastic pool](./media/elastic-pool-manage/configure-pool.png)

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling gäller AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska.

Använd följande PowerShell-cmdletar för att skapa och hantera SQL Database elastiska pooler och databaser i pooler med Azure PowerShell. Om du behöver installera eller uppgradera PowerShell, se [installera Azure PowerShell modul](/powershell/azure/install-az-ps). Information om hur du skapar och hanterar servrar för en elastisk pool finns i [skapa och hantera servrar](logical-servers.md). Information om att skapa och hantera brand Väggs regler finns i [skapa och hantera brand Väggs regler med hjälp av PowerShell](firewall-configure.md#use-powershell-to-manage-server-level-ip-firewall-rules).

> [!TIP]
> För PowerShell-exempel skript, se [Skapa elastiska pooler och flytta databaser mellan pooler och från en pool med hjälp av PowerShell](scripts/move-database-between-elastic-pools-powershell.md) och [Använd PowerShell för att övervaka och skala en elastisk SQL-pool i Azure SQL Database](scripts/monitor-and-scale-pool-powershell.md).
>

| Cmdlet | Beskrivning |
| --- | --- |
|[New-AzSqlElasticPool](/powershell/module/az.sql/new-azsqlelasticpool)|Skapar en elastisk pool.|
|[Get-AzSqlElasticPool](/powershell/module/az.sql/get-azsqlelasticpool)|Hämtar elastiska pooler och deras egenskaps värden.|
|[Set-AzSqlElasticPool](/powershell/module/az.sql/set-azsqlelasticpool)|Ändrar egenskaper för en elastisk pool Använd exempelvis egenskapen **StorageMB** för att ändra den maximala lagringen för en elastisk pool.|
|[Remove-AzSqlElasticPool](/powershell/module/az.sql/remove-azsqlelasticpool)|Tar bort en elastisk pool.|
|[Get-AzSqlElasticPoolActivity](/powershell/module/az.sql/get-azsqlelasticpoolactivity)|Hämtar status för åtgärder i en elastisk pool|
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Skapar en ny databas i en befintlig pool eller som en enskild databas. |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Hämtar en eller flera databaser.|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Anger egenskaper för en databas eller flyttar en befintlig databas till, från eller mellan elastiska pooler.|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Tar bort en databas.|

> [!TIP]
> Det kan ta tid att skapa många databaser i en elastisk pool när du gör det med hjälp av portalen eller PowerShell-cmdletar som bara skapar en databas åt gången. Om du vill skapa en elastisk pool automatiskt, se [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).

## <a name="azure-cli"></a>Azure CLI

Använd följande [Azure cli SQL Database](/cli/azure/sql/db) -kommandon för att skapa och hantera SQL Database elastiska pooler med [Azure CLI](/cli/azure). Använd [Cloud Shell](../../cloud-shell/overview.md) för att köra CLI i webbläsaren eller [installera](/cli/azure/install-azure-cli) det på macOS, Linux eller Windows.

> [!TIP]
> Exempel skript för Azure CLI finns i [använda CLI för att flytta en databas i SQL Database i en elastisk SQL-pool](scripts/move-database-between-elastic-pools-cli.md) och [använda Azure CLI för att skala en elastisk SQL-pool i Azure SQL Database](scripts/scale-pool-cli.md).
>

| Cmdlet | Beskrivning |
| --- | --- |
|[AZ SQL Elastic-pool Create](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-create)|Skapar en elastisk pool.|
|[AZ SQL Elastic-pool List](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list)|Returnerar en lista med elastiska pooler på en server.|
|[AZ SQL Elastic-pool List-databaser](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-dbs)|Returnerar en lista med databaser i en elastisk pool.|
|[AZ SQL Elastic-pool List-Editions](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-list-editions)|Inkluderar också tillgängliga DTU-inställningar, lagrings gränser och inställningar per databas. För att minska utförligheten är ytterligare lagrings gränser och per databas inställningar dolda som standard.|
|[AZ SQL Elastic-pool Update](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-update)|Uppdaterar en elastisk pool.|
|[AZ SQL Elastic-pool Delete](/cli/azure/sql/elastic-pool#az-sql-elastic-pool-delete)|Tar bort den elastiska poolen.|

## <a name="transact-sql-t-sql"></a>Transact-SQL (T-SQL)

Om du vill skapa och flytta databaser inom befintliga elastiska pooler eller returnera information om en SQL Database elastisk pool med Transact-SQL, använder du följande T-SQL-kommandon. Du kan utfärda dessa kommandon med hjälp av Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs)eller andra program som kan ansluta till en server och skicka Transact-SQL-kommandon. För att skapa och hantera brand Väggs regler med hjälp av T-SQL, se [Hantera brand Väggs regler med hjälp av Transact-SQL](firewall-configure.md#use-transact-sql-to-manage-ip-firewall-rules).

> [!IMPORTANT]
> Du kan inte skapa, uppdatera eller ta bort en Azure SQL Database elastisk pool med hjälp av Transact-SQL. Du kan lägga till eller ta bort databaser från en elastisk pool och du kan använda DMV: er för att returnera information om befintliga elastiska pooler.
>

| Kommando | Beskrivning |
| --- | --- |
|[SKAPA databas (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Skapar en ny databas i en befintlig pool eller som en enskild databas. Du måste vara ansluten till huvud databasen för att skapa en ny databas.|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-azure-sql-database) |Flytta en databas till, ut eller mellan elastiska pooler.|
|[SLÄPP databas (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Tar bort en databas.|
|[sys.elastic_pool_resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Returnerar statistik för resursanvändning för alla elastiska pooler på en server. För varje elastisk pool finns det en rad för varje 15 sekunders rapporterings fönster (fyra rader per minut). Detta omfattar CPU, IO, logg, lagrings användning och samtidig begäran/användning av alla databaser i poolen.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Returnerar utgåvan (tjänst nivån), tjänst målet (pris nivån) och ett elastiskt poolnamn, om det finns någon, för en databas i SQL Database eller Azure Synapse Analytics. Om du är inloggad på huvud databasen på en server returneras information om alla databaser. För Azure Synapse Analytics måste du vara ansluten till huvud databasen.|

## <a name="rest-api"></a>REST-API

Använd dessa REST API förfrågningar för att skapa och hantera SQL Database elastiska pooler och databaser i pooler.

| Kommando | Beskrivning |
| --- | --- |
|[Elastiska pooler – skapa eller uppdatera](/rest/api/sql/elasticpools/createorupdate)|Skapar en ny elastisk pool eller uppdaterar en befintlig elastisk pool.|
|[Elastiska pooler – ta bort](/rest/api/sql/elasticpools/delete)|Tar bort den elastiska poolen.|
|[Elastiska pooler – Hämta](/rest/api/sql/elasticpools/get)|Hämtar en elastisk pool.|
|[Elastiska pooler – lista efter server](/rest/api/sql/elasticpools/listbyserver)|Returnerar en lista med elastiska pooler på en server.|
|[Elastiska pooler – uppdatera](/rest/api/sql/elasticpools/listbyserver)|Uppdaterar en befintlig elastisk pool.|
|[Aktiviteter för elastisk pool](/rest/api/sql/elasticpoolactivities)|Returnerar aktiviteter för elastisk pool.|
|[Databas aktiviteter för elastisk pool](/rest/api/sql/elasticpooldatabaseactivities)|Returnerar aktivitet för databaser inuti en elastisk pool.|
|[Databaser – skapa eller uppdatera](/rest/api/sql/databases/createorupdate)|Skapar en ny databas eller uppdaterar en befintlig databas.|
|[Databaser – Hämta](/rest/api/sql/databases/get)|Hämtar en databas.|
|[Databaser – lista med elastisk pool](/rest/api/sql/databases/listbyelasticpool)|Returnerar en lista med databaser i en elastisk pool.|
|[Databaser – lista efter server](/rest/api/sql/databases/listbyserver)|Returnerar en lista med databaser på en server.|
|[Databaser – uppdatera](/rest/api/sql/databases/update)|Uppdaterar en befintlig databas.|

## <a name="next-steps"></a>Nästa steg

* Läs mer om designmönster för SaaS-program med elastiska pooler i [Designmönster för SaaS-program med flera klienter med Azure SQL Database](saas-tenancy-app-design-patterns.md).
* En SaaS-självstudie med elastiska pooler finns i [Introduktion till Wingtip SaaS-programmet](saas-dbpertenant-wingtip-app-overview.md).