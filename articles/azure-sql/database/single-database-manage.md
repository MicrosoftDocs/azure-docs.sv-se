---
title: Skapa & hantera servrar och enskilda databaser
description: Lär dig mer om att skapa och hantera servrar och enkla databaser i Azure SQL Database med hjälp av Azure Portal, PowerShell, Azure CLI, Transact-SQL (T-SQL) och REST-API.
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
ms.openlocfilehash: e50cce74f7291a6673e5d43f3485a1c63c81d827
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93319282"
---
# <a name="create-and-manage-servers-and-single-databases-in-azure-sql-database"></a>Skapa och hantera servrar och enkla databaser i Azure SQL Database

Du kan skapa och hantera servrar och enkla databaser i Azure SQL Database med hjälp av Azure Portal, PowerShell, Azure CLI, REST API och Transact-SQL.

## <a name="the-azure-portal"></a>Azure Portal

Du kan skapa resurs gruppen för Azure SQL Database i förväg eller när du skapar själva servern.

### <a name="create-a-server"></a>Skapa en server

Om du vill skapa en server med hjälp av [Azure Portal](https://portal.azure.com)skapar du en ny [Server](logical-servers.md) resurs från Azure Marketplace. Du kan också skapa-servern när du distribuerar en Azure SQL Database.

  ![skapa server](./media/single-database-manage/create-logical-sql-server.png)

### <a name="create-a-blank-or-sample-database"></a>Skapa en tom databas eller en exempel databas

Om du vill skapa en enskild Azure SQL Database med hjälp av [Azure Portal](https://portal.azure.com)väljer du Azure SQL Database resurs på Azure Marketplace. Du kan skapa en resurs grupp och en server i förväg eller samtidigt som du skapar själva databasen. Du kan skapa en tom databas eller skapa en exempel databas baserad på Adventure Works LT.

  ![skapa databas-1](./media/single-database-manage/create-database-1.png)

> [!IMPORTANT]
> Information om hur du väljer pris nivå för din databas finns i [DTU-baserad inköps modell](service-tiers-dtu.md) och [vCore-baserad inköps modell](service-tiers-vcore.md).

## <a name="manage-an-existing-server"></a>Hantera en befintlig server

Om du vill hantera en befintlig server navigerar du till servern med ett antal metoder – till exempel från en speciell databas sida, sidan **SQL-servrar** eller sidan **alla resurser** .

Om du vill hantera en befintlig databas går du till sidan **SQL-databaser** och väljer den databas som du vill hantera. Följande skärm bild visar hur du börjar ställa in en brand vägg på server nivå för en databas från **översikts** sidan för en databas.

   ![brandväggsregler för server](./media/single-database-manage/server-firewall-rule.png)

> [!IMPORTANT]
> Om du vill konfigurera prestanda egenskaper för en databas, se [DTU-baserad inköps modell](service-tiers-dtu.md) och [vCore-baserad inköps modell](service-tiers-vcore.md).
> [!TIP]
> En Azure Portal snabb start finns i [skapa en databas i SQL Database i Azure Portal](single-database-create-quickstart.md).

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande av Azure SQL Database, men all framtida utveckling gäller AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska.

Använd följande PowerShell-cmdletar om du vill skapa och hantera servrar, enkla databaser och-brand väggar på server nivå med Azure PowerShell. Om du behöver installera eller uppgradera PowerShell, se [installera Azure PowerShell modul](/powershell/azure/install-az-ps).

> [!TIP]
> För PowerShell-exempel skript, se [Använd PowerShell för att skapa en databas i SQL Database och konfigurera en brand Väggs regel på server nivå](scripts/create-and-configure-database-powershell.md) och [övervaka och skala en databas i SQL Database med PowerShell](scripts/monitor-and-scale-database-powershell.md).

| Cmdlet | Beskrivning |
| --- | --- |
|[New-AzSqlDatabase](/powershell/module/az.sql/new-azsqldatabase)|Skapar en databas |
|[Get-AzSqlDatabase](/powershell/module/az.sql/get-azsqldatabase)|Hämtar en eller flera databaser|
|[Set-AzSqlDatabase](/powershell/module/az.sql/set-azsqldatabase)|Anger egenskaper för en databas eller flyttar en befintlig databas till en elastisk pool|
|[Remove-AzSqlDatabase](/powershell/module/az.sql/remove-azsqldatabase)|Tar bort en databas|
|[New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)|Skapar en resurs grupp|
|[New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver)|Skapar en server|
|[Get-AzSqlServer](/powershell/module/az.sql/get-azsqlserver)|Returnerar information om servrar|
|[Set-AzSqlServer](/powershell/module/az.sql/set-azsqlserver)|Ändrar egenskaper för en server|
|[Remove-AzSqlServer](/powershell/module/az.sql/remove-azsqlserver)|Tar bort en server|
|[New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule)|Skapar en brand Väggs regel på server nivå |
|[Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule)|Hämtar brand Väggs regler för en server|
|[Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule)|Ändrar en brand Väggs regel på en server|
|[Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule)|Tar bort en brand Väggs regel från en server.|
| New-AzSqlServerVirtualNetworkRule | Skapar en [*regel för virtuella nätverk*](vnet-service-endpoint-rule-overview.md), baserat på ett undernät som är en Virtual Network tjänst slut punkt. |

## <a name="the-azure-cli"></a>Azure CLI

Använd följande [Azure CLI](/cli/azure/sql/db) -kommandon om du vill skapa och hantera servrar, databaser och brand väggar med [Azure CLI](/cli/azure). Använd [Cloud Shell](../../cloud-shell/overview.md) för att köra CLI i webbläsaren eller [installera](/cli/azure/install-azure-cli) det på macOS, Linux eller Windows. Information om hur du skapar och hanterar elastiska pooler finns i [elastiska pooler](elastic-pool-overview.md).

> [!TIP]
> En snabb start för Azure CLI finns i [skapa en enda Azure SQL Database med hjälp av Azure CLI](az-cli-script-samples-content-guide.md). För Azure CLI-exempel skript, se [Använd CLI för att skapa en databas i Azure SQL Database och konfigurera en SQL Database brand Väggs regel](scripts/create-and-configure-database-cli.md) och [Använd CLI för att övervaka och skala en databas i Azure SQL Database](scripts/monitor-and-scale-database-cli.md).
>

| Cmdlet | Beskrivning |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az-sql-db-create) |Skapar en databas|
|[AZ SQL DB-lista](/cli/azure/sql/db#az-sql-db-list)|Visar alla databaser och informations lager på en server, eller alla databaser i en elastisk pool|
|[AZ SQL DB List-Editions](/cli/azure/sql/db#az-sql-db-list-editions)|Visar en lista över tillgängliga tjänst mål och lagrings gränser|
|[AZ SQL DB List-usages](/cli/azure/sql/db#az-sql-db-list-usages)|Returnerar databas användningar|
|[AZ SQL DB show](/cli/azure/sql/db#az-sql-db-show)|Hämtar en databas eller ett informations lager|
|[az sql db update](/cli/azure/sql/db#az-sql-db-update)|Uppdaterar en databas|
|[AZ SQL DB Delete](/cli/azure/sql/db#az-sql-db-delete)|Tar bort en databas|
|[az group create](/cli/azure/group#az-group-create)|Skapar en resurs grupp|
|[az sql server create](/cli/azure/sql/server#az-sql-server-create)|Skapar en server|
|[AZ SQL Server-lista](/cli/azure/sql/server#az-sql-server-list)|Listar servrar|
|[AZ SQL Server List-usages](/cli/azure/sql/server#az-sql-server-list-usages)|Returnerar Server användningar|
|[AZ SQL Server show](/cli/azure/sql/server#az-sql-server-show)|Hämtar en server|
|[AZ SQL Server Update](/cli/azure/sql/server#az-sql-server-update)|Uppdaterar en server|
|[AZ SQL Server Delete](/cli/azure/sql/server#az-sql-server-delete)|Tar bort en server|
|[AZ SQL Server-brandvägg-regel skapa](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-create)|Skapar en server brand Väggs regel|
|[AZ SQL Server Firewall-Rule List](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-list)|Visar en lista över brand Väggs regler på en server|
|[AZ för SQL Server-brandvägg – regel show](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-show)|Visar information om en brand Väggs regel|
|[AZ SQL Server-brandvägg-regel uppdatering](/cli/azure/sql/server/firewall-rule##az-sql-server-firewall-rule-update)|Uppdaterar en brand Väggs regel|
|[AZ SQL Server Firewall-Rule Delete](/cli/azure/sql/server/firewall-rule#az-sql-server-firewall-rule-delete)|Tar bort en brand Väggs regel|

## <a name="transact-sql-t-sql"></a>Transact-SQL (T-SQL)

Om du vill skapa och hantera servrar, databaser och brand väggar med Transact-SQL använder du följande T-SQL-kommandon. Du kan utfärda dessa kommandon med hjälp av Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs)eller andra program som kan ansluta till en server i SQL Database och skicka Transact-SQL-kommandon. Information om hur du hanterar elastiska pooler finns i [elastiska pooler](elastic-pool-overview.md).

> [!TIP]
> En snabb start som använder SQL Server Management Studio på Microsoft Windows finns [Azure SQL Database: använd SQL Server Management Studio för att ansluta och fråga efter data](connect-query-ssms.md). En snabb start med Visual Studio Code på macOS, Linux eller Windows finns [Azure SQL Database: använda Visual Studio Code för att ansluta och fråga efter data](connect-query-vscode.md).
> [!IMPORTANT]
> Du kan inte skapa eller ta bort en server med hjälp av Transact-SQL.

| Kommando | Beskrivning |
| --- | --- |
|[SKAPA DATABAS](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current&preserve-view=true)|Skapar en ny enkel databas. Du måste vara ansluten till huvud databasen för att skapa en ny databas.|
| [ALTER DATABASE](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current&preserve-view=true) |Ändrar en databas eller elastisk pool. |
|[DROP DATABASE](/sql/t-sql/statements/drop-database-transact-sql)|Tar bort en databas.|
|[sys.database_service_objectives](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Returnerar utgåvan (tjänst nivån), tjänst målet (pris nivån) och ett elastiskt poolnamn, om det finns Azure SQL Database eller en dedikerad SQL-pool i Azure Synapse Analytics. Om du är inloggad på huvud databasen på en server i SQL Database, returnerar information om alla databaser. För Azure Synapse Analytics måste du vara ansluten till huvud databasen.|
|[sys.dm_db_resource_stats](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Returnerar CPU-, IO-och minnes förbrukning för en databas i Azure SQL Database. Det finns en rad för var 15: e sekund, även om det inte finns någon aktivitet i databasen.|
|[sys.resource_stats](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Returnerar CPU-användning och lagrings data för en databas i Azure SQL Database. Data samlas in och sammanställs inom fem minuters intervall.|
|[sys.database_connection_stats](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Innehåller statistik för SQL Database anslutnings händelser som ger en översikt över lyckade och misslyckade databas anslutningar. |
|[sys.event_log](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Returnerar lyckade Azure SQL Database anslutningar, anslutnings problem och död lägen. Du kan använda den här informationen för att spåra eller felsöka din databas aktivitet med SQL Database.|
|[sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Skapar eller uppdaterar brand Väggs inställningarna på server nivå för servern. Den här lagrade proceduren är bara tillgänglig i huvud databasen för inloggning på server nivå. Det går bara att skapa en brand Väggs regel på server nivå med hjälp av Transact-SQL när den första brand Väggs regeln på server nivå har skapats av en användare med Azure-nivå behörigheter|
|[sys.firewall_rules](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Returnerar information om de brand Väggs inställningar på server nivå som är kopplade till databasen i Azure SQL Database.|
|[sp_delete_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Tar bort brand Väggs inställningar på server nivå från servern. Den här lagrade proceduren är bara tillgänglig i huvud databasen för inloggning på server nivå.|
|[sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Skapar eller uppdaterar brand Väggs reglerna på databas nivå för din databas i Azure SQL Database. Regler för databas brand vägg kan konfigureras för huvud databasen och för användar databaser på SQL Database. Regler för databas brand väggar är användbara när inneslutna databas användare används. |
|[sys.database_firewall_rules](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Returnerar information om de brand Väggs inställningar på databas nivå som är kopplade till databasen i Azure SQL Database. |
|[sp_delete_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Tar bort brand Väggs inställningen på databas nivå från en databas. |

## <a name="rest-api"></a>REST-API

Använd dessa REST API begär Anden för att skapa och hantera servrar, databaser och brand väggar.

| Kommando | Beskrivning |
| --- | --- |
|[Servrar-skapa eller uppdatera](/rest/api/sql/servers/createorupdate)|Skapar eller uppdaterar en ny server.|
|[Servrar-ta bort](/rest/api/sql/servers/delete)|Tar bort en SQL-Server.|
|[Servrar-Hämta](/rest/api/sql/servers/get)|Hämtar en server.|
|[Servrar-lista](/rest/api/sql/servers/list)|Returnerar en lista med servrar i en prenumeration.|
|[Servrar – lista efter resurs grupp](/rest/api/sql/servers/listbyresourcegroup)|Returnerar en lista med servrar i en resurs grupp.|
|[Servrar-uppdatera](/rest/api/sql/servers/update)|Uppdaterar en befintlig server.|
|[Databaser – skapa eller uppdatera](/rest/api/sql/databases/createorupdate)|Skapar en ny databas eller uppdaterar en befintlig databas.|
|[Databaser-ta bort](/rest/api/sql/databases/delete)|Tar bort en databas.|
|[Databaser – Hämta](/rest/api/sql/databases/get)|Hämtar en databas.|
|[Databaser – lista med elastisk pool](/rest/api/sql/databases/listbyelasticpool)|Returnerar en lista med databaser i en elastisk pool.|
|[Databaser – lista efter server](/rest/api/sql/databases/listbyserver)|Returnerar en lista med databaser på en server.|
|[Databaser – uppdatera](/rest/api/sql/databases/update)|Uppdaterar en befintlig databas.|
|[Brand Väggs regler – skapa eller uppdatera](/rest/api/sql/firewallrules/createorupdate)|Skapar eller uppdaterar en brand Väggs regel.|
|[Brand Väggs regler – ta bort](/rest/api/sql/firewallrules/delete)|Tar bort en brand Väggs regel.|
|[Brand Väggs regler – Hämta](/rest/api/sql/firewallrules/get)|Hämtar en brand Väggs regel.|
|[Brand Väggs regler – lista efter server](/rest/api/sql/firewallrules/listbyserver)|Returnerar en lista med brand Väggs regler.|

## <a name="next-steps"></a>Nästa steg

- Information om hur du migrerar en SQL Server-databas till Azure finns i [migrera till Azure SQL Database](migrate-to-database-from-sql-server.md).
- Information om vilka funktioner som stöds finns i avsnittet [Funktioner](features-comparison.md).
 
