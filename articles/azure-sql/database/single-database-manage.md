---
title: Skapa & hantera servrar och enskilda databaser
description: Lär dig mer om att skapa och hantera servrar och enkla databaser i Azure SQL Database med hjälp av Azure Portal, PowerShell, Azure CLI, Transact-SQL (T-SQL) och Rest-API.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 4aaabdb3d21c41b973b21e6e52442be132796196
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781603"
---
# <a name="create-and-manage-servers-and-single-databases-in-azure-sql-database"></a>Skapa och hantera servrar och enkla databaser i Azure SQL Database

Du kan skapa och hantera servrar och enkla databaser i Azure SQL Database med hjälp av Azure Portal, PowerShell, Azure CLI, REST API och Transact-SQL.

## <a name="the-azure-portal"></a>Azure Portal

Du kan skapa resursgruppen för Azure SQL Database i förväg eller när du skapar själva servern.

### <a name="create-a-server"></a>Skapa en server

Om du vill skapa en server [med Azure Portal](https://portal.azure.com)skapar du en ny [serverresurs](logical-servers.md) från Azure Marketplace. Du kan också skapa servern när du distribuerar en Azure SQL Database.

  ![skapa server](./media/single-database-manage/create-logical-sql-server.png)

### <a name="create-a-blank-or-sample-database"></a>Skapa en tom databas eller en exempeldatabas

Om du vill skapa en Azure SQL Database med [Azure Portal](https://portal.azure.com)väljer du Azure SQL Database resurs i Azure Marketplace. Du kan skapa resursgruppen och servern i förväg eller när du skapar själva den enskilda databasen. Du kan skapa en tom databas eller skapa en exempeldatabas baserat på Adventure Works LT.

  ![skapa databas-1](./media/single-database-manage/create-database-1.png)

> [!IMPORTANT]
> Information om hur du väljer prisnivå för din databas finns i Köpmodell baserad på [DTU](service-tiers-dtu.md) och [köpmodell baserad på vCore.](service-tiers-vcore.md)

## <a name="manage-an-existing-server"></a>Hantera en befintlig server

Om du vill hantera en befintlig server navigerar du till servern med ett antal metoder , till exempel från en specifik databassida, **sidan SQL-servrar** eller **sidan Alla** resurser.

Om du vill hantera en befintlig databas går du **till sidan SQL-databaser** och väljer den databas som du vill hantera. Följande skärmbild visar hur du börjar ställa in en brandvägg på servernivå för en databas från **översiktssidan** för en databas.

   ![brandväggsregler för server](./media/single-database-manage/server-firewall-rule.png)

> [!IMPORTANT]
> Information om hur du konfigurerar prestandaegenskaper för en databas finns i Köpmodell baserad på [DTU](service-tiers-dtu.md) och [köpmodell baserad på virtuella kärnor.](service-tiers-vcore.md)
> [!TIP]
> En snabbstart Azure Portal finns i [Skapa en databas i SQL Database i Azure Portal](single-database-create-quickstart.md).

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling är för Az.Sql-modulen. För dessa cmdlets, se [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Argumenten för kommandona i Az-modulen och i AzureRm-modulerna är betydligt identiska.

Om du vill skapa och hantera servrar, enkla databaser och pooldatabaser och brandväggar på servernivå med Azure PowerShell använder du följande PowerShell-cmdlets. Om du behöver installera eller uppgradera PowerShell kan du gå till [Installera Azure PowerShell modulen](/powershell/azure/install-az-ps).

> [!TIP]
> Exempelskript för PowerShell finns i Använda PowerShell för att skapa en databas i SQL Database och konfigurera en brandväggsregel på [servernivå](scripts/create-and-configure-database-powershell.md) samt Övervaka och skala en databas i SQL Database med Hjälp av [PowerShell.](scripts/monitor-and-scale-database-powershell.md)

| Cmdlet | Beskrivning |
| --- | --- |
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Skapar en databas |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Hämtar en eller flera databaser|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Anger egenskaper för en databas eller flyttar en befintlig databas till en elastisk pool|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Tar bort en databas|
|[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)|Skapar en resursgrupp|
|[New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)|Skapar en server|
|[Get-AzSqlServer](/powershell/module/az.sql/get-azsqlserver)|Returnerar information om servrar|
|[Set-AzSqlServer](/powershell/module/az.sql/set-azsqlserver)|Ändrar egenskaperna för en server|
|[Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)|Tar bort en server|
|[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)|Skapar en brandväggsregel på servernivå |
|[Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule)|Hämtar brandväggsregler för en server|
|[Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule)|Ändrar en brandväggsregel på en server|
|[Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule)|Tar bort en brandväggsregel från en server.|
| New-AzSqlServerVirtualNetworkRule | Skapar en [*regel för virtuellt*](vnet-service-endpoint-rule-overview.md)nätverk baserat på ett undernät som är Virtual Network tjänstslutpunkt. |

## <a name="the-azure-cli"></a>Azure CLI

Använd följande [Azure CLI-kommandon](/cli/azure)för att skapa och hantera servrar, databaser och brandväggar med Azure [CLI.](/cli/azure/sql/db) Använd [Cloud Shell](../../cloud-shell/overview.md) för att köra CLI i webbläsaren eller [installera](/cli/azure/install-azure-cli) det på macOS, Linux eller Windows. Information om hur du skapar och hanterar elastiska pooler finns i [Elastiska pooler.](elastic-pool-overview.md)

> [!TIP]
> En Azure CLI-snabbstart finns i [Skapa en Azure SQL Database med Hjälp av Azure CLI.](az-cli-script-samples-content-guide.md) Exempelskript för Azure CLI finns i Använda CLI för att skapa en databas i [Azure SQL Database](scripts/create-and-configure-database-cli.md) och konfigurera en SQL Database-brandväggsregel och Använda CLI för att övervaka och skala en databas [i Azure SQL Database](scripts/monitor-and-scale-database-cli.md).
>

| Cmdlet | Beskrivning |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az_sql_db_create) |Skapar en databas|
|[az sql db list](/cli/azure/sql/db#az_sql_db_list)|Visar en lista över alla databaser och informationslager på en server eller alla databaser i en elastisk pool|
|[az sql db list-editions](/cli/azure/sql/db#az_sql_db_list_editions)|Visar tillgängliga tjänstmål och lagringsgränser|
|[az sql db list-usages](/cli/azure/sql/db#az_sql_db_list_usages)|Returnerar databasanvändningar|
|[az sql db show](/cli/azure/sql/db#az_sql_db_show)|Hämtar en databas eller ett informationslager|
|[az sql db update](/cli/azure/sql/db#az_sql_db_update)|Uppdaterar en databas|
|[az sql db delete](/cli/azure/sql/db#az_sql_db_delete)|Tar bort en databas|
|[az group create](/cli/azure/group#az_group_create)|Skapar en resursgrupp|
|[az sql server create](/cli/azure/sql/server#az_sql_server_create)|Skapar en server|
|[az sql server list](/cli/azure/sql/server#az_sql_server_list)|Visar en lista över servrar|
|[az sql server list-usages](/cli/azure/sql/server#az_sql_server_list-usages)|Returnerar serveranvändningar|
|[az sql server show](/cli/azure/sql/server#az_sql_server_show)|Hämtar en server|
|[az sql server update](/cli/azure/sql/server#az_sql_server_update)|Uppdaterar en server|
|[az sql server delete](/cli/azure/sql/server#az_sql_server_delete)|Tar bort en server|
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|Skapar en brandväggsregel för servern|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|Visar en lista över brandväggsregler på en server|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|Visar information om en brandväggsregel|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|Uppdaterar en brandväggsregel|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|Tar bort en brandväggsregel|

## <a name="transact-sql-t-sql"></a>Transact-SQL (T-SQL)

Använd följande T-SQL-kommandon för att skapa och hantera servrar, databaser och brandväggar med Transact-SQL. Du kan utfärda dessa kommandon med hjälp av Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs)eller något annat program som kan ansluta till en server i SQL Database och skicka Transact-SQL-kommandon. Information om hur du hanterar elastiska pooler finns i [Elastiska pooler.](elastic-pool-overview.md)

> [!TIP]
> En snabbstart med att SQL Server Management Studio i Microsoft Windows finns i Azure SQL Database: Använda SQL Server Management Studio [för att ansluta och fråga efter data.](connect-query-ssms.md) En snabbstart med Visual Studio Code på macOS, Linux eller Windows finns i Azure SQL Database: Använda Visual Studio Code för att [ansluta och fråga efter data.](connect-query-vscode.md)
> [!IMPORTANT]
> Du kan inte skapa eller ta bort en server med hjälp av Transact-SQL.

| Kommando | Beskrivning |
| --- | --- |
|[SKAPA DATABAS](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true)|Skapar en ny enkel databas. Du måste vara ansluten till huvuddatabasen för att skapa en ny databas.|
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |Ändrar en databas eller elastisk pool. |
|[DROP DATABASE](/sql/t-sql/statements/drop-database-transact-sql)|Tar bort en databas.|
|[sys.database_service_objectives](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Returnerar utgåvan (tjänstnivån), tjänstmålet (prisnivån) och eventuellt namnet på den elastiska poolen för Azure SQL Database eller en dedikerad SQL-pool i Azure Synapse Analytics. Om du är inloggad på huvuddatabasen på en server i SQL Database returnerar information om alla databaser. För Azure Synapse Analytics måste du vara ansluten till huvuddatabasen.|
|[sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Returnerar CPU, IO och minnesförbrukning för en databas i Azure SQL Database. Det finns en rad för var 15:e sekund, även om det inte finns någon aktivitet i databasen.|
|[sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Returnerar PROCESSORanvändning och lagringsdata för en databas i Azure SQL Database. Data samlas in och aggregeras inom femminutersintervall.|
|[sys.database_connection_stats](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Innehåller statistik för SQL Database anslutningshändelser, vilket ger en översikt över lyckade och misslyckade databasanslutningar. |
|[sys.event_log](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Returnerar Azure SQL Database anslutningar, anslutningsfel och dödlägen. Du kan använda den här informationen för att spåra eller felsöka din databasaktivitet med SQL Database.|
|[sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Skapar eller uppdaterar brandväggsinställningarna på servernivå för servern. Den här lagrade proceduren är endast tillgänglig i huvuddatabasen för huvudsaklig inloggning på servernivå. En brandväggsregel på servernivå kan bara skapas med Hjälp av Transact-SQL när den första brandväggsregeln på servernivå har skapats av en användare med behörigheter på Azure-nivå|
|[sys.firewall_rules](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Returnerar information om brandväggsinställningarna på servernivå som är associerade med databasen i Azure SQL Database.|
|[sp_delete_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Tar bort brandväggsinställningar på servernivå från servern. Den här lagrade proceduren är endast tillgänglig i huvuddatabasen för huvudsaklig inloggning på servernivå.|
|[sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Skapar eller uppdaterar brandväggsreglerna på databasnivå för databasen i Azure SQL Database. Brandväggsregler för databasen kan konfigureras för huvuddatabasen och för användardatabaser på SQL Database. Brandväggsregler för databaser är användbara när du använder användare av inneslutna databaser. |
|[sys.database_firewall_rules](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Returnerar information om brandväggsinställningarna på databasnivå som är associerade med databasen i Azure SQL Database. |
|[sp_delete_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Tar bort brandväggsinställningen på databasnivå från en databas. |

## <a name="rest-api"></a>REST-API

Om du vill skapa och hantera servrar, databaser och brandväggar använder du dessa REST API begäranden.

| Kommando | Beskrivning |
| --- | --- |
|[Servrar – Skapa eller uppdatera](/rest/api/sql/servers/createorupdate)|Skapar eller uppdaterar en ny server.|
|[Servrar – Ta bort](/rest/api/sql/servers/delete)|Tar bort en SQL-server.|
|[Servrar – Hämta](/rest/api/sql/servers/get)|Hämtar en server.|
|[Servrar – lista](/rest/api/sql/servers/list)|Returnerar en lista över servrar i en prenumeration.|
|[Servrar – lista efter resursgrupp](/rest/api/sql/servers/listbyresourcegroup)|Returnerar en lista med servrar i en resursgrupp.|
|[Servrar – Uppdatera](/rest/api/sql/servers/update)|Uppdaterar en befintlig server.|
|[Databaser – Skapa eller uppdatera](/rest/api/sql/databases/createorupdate)|Skapar en ny databas eller uppdaterar en befintlig databas.|
|[Databaser – Ta bort](/rest/api/sql/databases/delete)|Tar bort en databas.|
|[Databaser – Hämta](/rest/api/sql/databases/get)|Hämtar en databas.|
|[Databaser – lista efter elastisk pool](/rest/api/sql/databases/listbyelasticpool)|Returnerar en lista över databaser i en elastisk pool.|
|[Databaser – lista efter server](/rest/api/sql/databases/listbyserver)|Returnerar en lista med databaser på en server.|
|[Databaser – Uppdatera](/rest/api/sql/databases/update)|Uppdaterar en befintlig databas.|
|[Brandväggsregler – Skapa eller uppdatera](/rest/api/sql/firewallrules/createorupdate)|Skapar eller uppdaterar en brandväggsregel.|
|[Brandväggsregler – Ta bort](/rest/api/sql/firewallrules/delete)|Tar bort en brandväggsregel.|
|[Brandväggsregler – Hämta](/rest/api/sql/firewallrules/get)|Hämtar en brandväggsregel.|
|[Brandväggsregler – lista efter server](/rest/api/sql/firewallrules/listbyserver)|Returnerar en lista över brandväggsregler.|

## <a name="next-steps"></a>Nästa steg

- Mer information om hur du migrerar en SQL Server-databas till Azure finns [i Migrera till Azure SQL Database](migrate-to-database-from-sql-server.md).
- Information om vilka funktioner som stöds finns i avsnittet [Funktioner](features-comparison.md).
