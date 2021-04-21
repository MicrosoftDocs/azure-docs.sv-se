---
title: Vad är en server i Azure SQL Database och Azure Synapse Analytics?
titleSuffix: ''
description: Lär dig mer om logiska SQL-servrar Azure SQL Database och Azure Synapse Analytics och hur du hanterar dem.
services: sql-database
ms.service: sql-database
ms.subservice: single-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: c76d3ae78bf2b9b4a71d9520f7f1c6c2c322483b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784523"
---
# <a name="what-is-a-logical-sql-server-in-azure-sql-database-and-azure-synapse"></a>Vad är en logisk SQL-server i Azure SQL Database och Azure Synapse?
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

I Azure SQL Database och Azure Synapse Analytics är en server en logisk konstruktion som fungerar som en central administrativ plats för en samling databaser. På servernivå kan du administrera [inloggningar,](firewall-configure.md) [brandväggsregler,](logins-create-manage.md) [granskningsregler,](../../azure-sql/database/auditing-overview.md) [](threat-detection-configure.md)principer för hotidentifiering och automatiska [redundansgrupper.](auto-failover-group-overview.md) En server kan finnas i en annan region än dess resursgrupp. Servern måste finnas innan du kan skapa en databas i Azure SQL Database eller en informationslagerdatabas i Azure Synapse Analytics. Alla databaser som hanteras av en enskild server skapas inom samma region som servern.

Den här servern skiljer sig SQL Server en instans som du kanske är bekant med i den lokala världen. Mer specifikt finns det inga garantier angående platsen för databaserna eller informationslagerdatabasen i förhållande till den server som hanterar dem. Dessutom kan varken Azure SQL Database eller Azure Synapse exponera åtkomst eller funktioner på instansnivå. Däremot är alla instansdatabaser i en hanterad instans fysiskt placerade på samma sätt som du är bekant med SQL Server i den lokala eller virtuella datorn.

När du skapar en server anger du ett serverinloggningskonto och lösenord som har administrativa rättigheter till huvuddatabasen på den servern och alla databaser som skapats på den servern. Det här första kontot är ett SQL-inloggningskonto. Azure SQL Database och Azure Synapse Analytics SQL-autentisering och Azure Active Directory autentisering för autentisering. Information om inloggningar och autentisering finns i [Hantera databaser och inloggningar i Azure SQL Database](logins-create-manage.md). Windows-autentisering stöds inte.

En server i SQL Database och Azure Synapse:

- Skapas i en Azure-prenumeration, men kan flyttas till en annan prenumeration tillsammans med de resurser som den innehåller.
- Är den överordnade resursen för databaser, elastiska pooler och informationslager.
- Tillhandahåller ett namnområde för databaser, elastiska pooler och informationslagerdatabas
- Är en logisk container med stark livslängdssemantik – ta bort en server och ta bort dess databaser, elastiska pooler och SQK-pooler
- Deltar i [rollbaserad åtkomstkontroll i Azure (Azure RBAC)](../../role-based-access-control/overview.md) – databaser, elastiska pooler och informationslagerdatabaser på en server ärver åtkomstbehörigheter från servern
- Är ett element med hög ordning för identiteten för databaser, elastiska pooler och informationslagerdatabaser för Azure-resurshanteringsändamål (se URL-schemat för databaser och pooler)
- Samlar resurser i en region.
- Tillhandahåller en anslutningsslutpunkt för databasåtkomst (`<serverName>`.database.windows.net)
- Ger åtkomst till metadata för inneslutna resurser via DMV:er genom att ansluta till en huvuddatabas.
- Tillhandahåller omfånget för hanteringsprinciper som gäller för dess databaser – inloggningar, brandvägg, granskning, hotidentifiering och så här
- Begränsas av en kvot inom den överordnade prenumerationen (sex servrar per prenumeration som standard – [se Prenumerationsgränser här)](../../azure-resource-manager/management/azure-subscription-service-limits.md)
- Tillhandahåller omfånget för databaskvoten och DTU- eller vCore-kvoten för de resurser som den innehåller (till exempel 45 000 DTU)
- Är versionsomfånget för funktioner som är aktiverade för inneslutna resurser
- Huvudkontoinloggningar på servernivå kan hantera alla databaser på en server.
- Kan innehålla inloggningar som liknar dem i instanser av SQL Server i din lokala miljö som beviljas åtkomst till en eller flera databaser på servern och kan beviljas begränsade administrativa rättigheter. Mer information finns i avsnittet om [inloggningar](logins-create-manage.md).
- Standard sorteringen för alla databaser som skapas på en server är , där är engelska (USA), är teckensidan `SQL_LATIN1_GENERAL_CP1_CI_AS` `LATIN1_GENERAL` `CP1` 1252, är icke-känslig för case och är `CI` `AS` accentkänslig.

## <a name="manage-servers-databases-and-firewalls-using-the-azure-portal"></a>Hantera servrar, databaser och brandväggar med hjälp av Azure Portal

Du kan skapa resursgruppen för en server i förväg eller när du skapar själva servern. Det finns flera metoder för att komma till ett nytt SQL Server-formulär, antingen genom att skapa en ny SQL-server eller som en del av att skapa en ny databas.

### <a name="create-a-blank-server"></a>Skapa en tom server

Om du vill skapa en server (utan en databas, elastisk pool eller informationslagerdatabas) med [hjälp av Azure Portal](https://portal.azure.com)navigerar du till ett tomt SQL-serverformulär (logisk SQL-server).

### <a name="create-a-blank-or-sample-database-in-azure-sql-database"></a>Skapa en tom databas eller en exempeldatabas i Azure SQL Database

Om du vill skapa en databas SQL Database med [Azure Portal](https://portal.azure.com)navigerar du till ett tomt SQL Database-formulär och anger den begärda informationen. Du kan skapa resursgruppen och servern i förväg eller när du skapar själva databasen. Du kan skapa en tom databas eller skapa en exempeldatabas baserat på Adventure Works LT.

  ![skapa databas-1](./media/logical-servers/create-database-1.png)

> [!IMPORTANT]
> Information om hur du väljer prisnivå för din databas finns i Köpmodell baserad på [DTU](service-tiers-dtu.md) och [köpmodell baserad på vCore.](service-tiers-vcore.md)

Information om hur du skapar en hanterad instans finns [i Skapa en hanterad instans](../managed-instance/instance-create-quickstart.md)

### <a name="manage-an-existing-server"></a>Hantera en befintlig server

Om du vill hantera en befintlig server navigerar du till servern med ett antal metoder , till exempel från en specifik databassida, sidan **SQL-servrar** **eller sidan Alla** resurser.

Om du vill hantera en befintlig databas går du till **sidan SQL-databaser** och klickar på den databas som du vill hantera. Följande skärmbild visar hur du börjar ställa in en brandvägg på servernivå för en databas från **översiktssidan** för en databas.

   ![brandväggsregler för server](./media/single-database-create-quickstart/server-firewall-rule.png)

> [!IMPORTANT]
> Information om hur du konfigurerar prestandaegenskaper för en databas finns i Köpmodell baserad på [DTU](service-tiers-dtu.md) och [köpmodell baserad på virtuella kärnor.](service-tiers-vcore.md)
> [!TIP]
> En snabbstart Azure Portal finns i [Skapa en databas i SQL Database i Azure Portal](single-database-create-quickstart.md).

## <a name="manage-servers-databases-and-firewalls-using-powershell"></a>Hantera servrar, databaser och brandväggar med PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager modulen stöds fortfarande, men all framtida utveckling är till för Az.Sql-modulen. Dessa cmdlets finns i [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Argumenten för kommandona i Az-modulen och i AzureRm-modulerna är betydligt identiska.

Använd följande PowerShell-cmdlets för att skapa Azure PowerShell hantera servrar, databaser och brandväggar med hjälp av följande PowerShell-cmdlets. Om du behöver installera eller uppgradera PowerShell kan du gå [till Installera Azure PowerShell modulen](/powershell/azure/install-az-ps). Information om hur du skapar och hanterar elastiska pooler finns i [Elastiska pooler.](elastic-pool-overview.md)

| Cmdlet | Beskrivning |
| --- | --- |
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Skapar en databas |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Hämtar en eller flera databaser|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Anger egenskaper för en databas eller flyttar en befintlig databas till en elastisk pool|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Tar bort en databas|
|[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)|Skapar en resursgrupp|
|[New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)|Skapar en server|
|[Get-AzSqlServer](/powershell/module/az.sql/get-azsqlserver)|Returnerar information om servrar|
|[Set-AzSqlServer](/powershell/module/az.sql/set-azsqlserver)|Ändrar egenskaper för en server|
|[Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)|Tar bort en server|
|[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)|Skapar en brandväggsregel på servernivå |
|[Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule)|Hämtar brandväggsregler för en server|
|[Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule)|Ändrar en brandväggsregel på en server|
|[Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule)|Tar bort en brandväggsregel från en server.|
| New-AzSqlServerVirtualNetworkRule | Skapar en [*regel för virtuellt*](vnet-service-endpoint-rule-overview.md)nätverk baserat på ett undernät som är Virtual Network tjänstslutpunkt. |

> [!TIP]
> En PowerShell-snabbstart finns i [Skapa en databas i Azure SQL Database powershell.](single-database-create-quickstart.md) För PowerShell-exempelskript, se Använda [PowerShell](scripts/create-and-configure-database-powershell.md) för att skapa en databas i Azure SQL Database och konfigurera en brandväggsregel och Övervaka och skala en databas i [Azure SQL Database med Hjälp av PowerShell](scripts/monitor-and-scale-database-powershell.md).
>

## <a name="manage-servers-databases-and-firewalls-using-the-azure-cli"></a>Hantera servrar, databaser och brandväggar med hjälp av Azure CLI

Om du vill skapa och hantera servrar, databaser och brandväggar med [Azure CLI](/cli/azure)använder du följande [Azure CLI SQL Database](/cli/azure/sql/db) kommandon. Använd [Cloud Shell](../../cloud-shell/overview.md) för att köra CLI i webbläsaren eller [installera](/cli/azure/install-azure-cli) det på macOS, Linux eller Windows. Information om hur du skapar och hanterar elastiska pooler finns i [Elastiska pooler.](elastic-pool-overview.md)

| Cmdlet | Beskrivning |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az_sql_db_create) |Skapar en databas|
|[az sql db list](/cli/azure/sql/db#az_sql_db_list)|Visar en lista över alla databaser som hanteras av en server eller alla databaser i en elastisk pool|
|[az sql db list-editions](/cli/azure/sql/db#az_sql_db_list_editions)|Visar tillgängliga tjänstmål och lagringsgränser|
|[az sql db list-usages](/cli/azure/sql/db#az_sql_db_list_usages)|Returnerar databasanvändningar|
|[az sql db show](/cli/azure/sql/db#az_sql_db_show)|Hämtar en databas
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

> [!TIP]
> En Azure CLI-snabbstart finns i [Skapa en databas i Azure SQL Database med Hjälp av Azure CLI.](az-cli-script-samples-content-guide.md) Exempelskript för Azure CLI finns i Använda CLI för att skapa en databas i [Azure SQL Database](scripts/create-and-configure-database-cli.md) och konfigurera en brandväggsregel och Använda CLI för att övervaka och skala en databas [i Azure SQL Database](scripts/monitor-and-scale-database-cli.md).
>

## <a name="manage-servers-databases-and-firewalls-using-transact-sql"></a>Hantera servrar, databaser och brandväggar med Hjälp av Transact-SQL

Använd följande T-SQL-kommandon för att skapa och hantera servrar, databaser och brandväggar med Transact-SQL. Du kan utfärda dessa kommandon med hjälp av Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs)eller något annat program som kan ansluta till en server och skicka Transact-SQL-kommandon. Information om hur du hanterar elastiska pooler finns i [Elastiska pooler.](elastic-pool-overview.md)

> [!IMPORTANT]
> Du kan inte skapa eller ta bort en server med hjälp av Transact-SQL.

| Kommando | Beskrivning |
| --- | --- |
|[SKAPA DATABAS (Azure SQL Database)](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true) | Skapar en ny databas i Azure SQL Database. Du måste vara ansluten till huvuddatabasen för att skapa en ny databas.|
|[CREATE DATABASE (Azure Synapse)](/sql/t-sql/statements/create-database-transact-sql?view=azure-sqldw-latest&preserve-view=true) | Skapar en ny informationslagerdatabas i Azure Synapse. Du måste vara ansluten till huvuddatabasen för att skapa en ny databas.|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |Ändrar databasen eller den elastiska poolen. |
|[ALTER DATABASE (Azure Synapse Analytics)](/sql/t-sql/statements/alter-database-transact-sql?view=azure-sqldw-latest&preserve-view=true&tabs=sqlpool)|Ändrar en informationslagerdatabas i Azure Synapse.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Tar bort en databas.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Returnerar utgåvan (tjänstnivån), tjänstmålet (prisnivån) och eventuellt namnet på den elastiska poolen för en databas. Om du är inloggad på huvuddatabasen för en server returneras information om alla databaser. För Azure Synapse måste du vara ansluten till huvuddatabasen.|
|[sys.dm_db_resource_stats (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Returnerar CPU, IO och minnesförbrukning för en databas i Azure SQL Database. Det finns en rad för var 15:e sekund, även om det inte finns någon aktivitet i databasen.|
|[sys.resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Returnerar PROCESSORanvändning och lagringsdata för en databas i Azure SQL Database. Data samlas in och aggregeras inom femminutersintervall.|
|[sys.database_connection_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Innehåller statistik för databasanslutningshändelser för Azure SQL Database, vilket ger en översikt över lyckade och misslyckade databasanslutningar. |
|[sys.event_log (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Returnerar Azure SQL Database databasanslutningar, anslutningsfel och dödlägen för Azure SQL Database. Du kan använda den här informationen för att spåra eller felsöka databasaktiviteten.|
|[sp_set_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Skapar eller uppdaterar brandväggsinställningarna på servernivå för servern. Den här lagrade proceduren är endast tillgänglig i huvuddatabasen för huvudsaklig inloggning på servernivå. En brandväggsregel på servernivå kan bara skapas med Hjälp av Transact-SQL när den första brandväggsregeln på servernivå har skapats av en användare med behörigheter på Azure-nivå|
|[sys.firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Returnerar information om brandväggsinställningarna på servernivå som är associerade med en server.|
|[sp_delete_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Tar bort brandväggsinställningar på servernivå från en server. Den här lagrade proceduren är endast tillgänglig i huvuddatabasen för huvudsaklig inloggning på servernivå.|
|[sp_set_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Skapar eller uppdaterar brandväggsreglerna på databasnivå för en databas i Azure SQL Database. Brandväggsregler för databasen kan konfigureras för huvuddatabasen och för användardatabaser i SQL Database. Brandväggsregler för databaser är användbara när du använder användare av inneslutna databaser. Brandväggsregler för databaser stöds inte i Azure Synapse.|
|[sys.database_firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Returnerar information om brandväggsinställningarna på databasnivå för en databas i Azure SQL Database. |
|[sp_delete_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Tar bort brandväggsinställningen på databasnivå för en databas i Azure SQL Database. |

> [!TIP]
> En snabbstart med att SQL Server Management Studio i Microsoft Windows finns i [Azure SQL Database: Använda SQL Server Management Studio för att ansluta och fråga efter data.](connect-query-ssms.md) En snabbstart med Visual Studio Code i macOS, Linux eller Windows finns i Azure SQL Database: Använda Visual Studio Code för att ansluta [och fråga efter data.](connect-query-vscode.md)

## <a name="manage-servers-databases-and-firewalls-using-the-rest-api"></a>Hantera servrar, databaser och brandväggar med hjälp av REST API

Om du vill skapa och hantera servrar, databaser och brandväggar använder du dessa REST API begäranden.

| Kommando | Beskrivning |
| --- | --- |
|[Servrar – Skapa eller uppdatera](/rest/api/sql/servers/createorupdate)|Skapar eller uppdaterar en ny server.|
|[Servrar – Ta bort](/rest/api/sql/servers/delete)|Tar bort en server.|
|[Servrar – Hämta](/rest/api/sql/servers/get)|Hämtar en server.|
|[Servrar – lista](/rest/api/sql/servers/list)|Returnerar en lista med servrar.|
|[Servrar – Lista efter resursgrupp](/rest/api/sql/servers/listbyresourcegroup)|Returnerar en lista över servrar i en resursgrupp.|
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

- Mer information om hur du migrerar en SQL Server-databas till Azure SQL Database finns [i Migrera till Azure SQL Database](migrate-to-database-from-sql-server.md).
- Information om vilka funktioner som stöds finns i avsnittet [Funktioner](features-comparison.md).