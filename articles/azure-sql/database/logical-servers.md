---
title: Vad är en server i Azure SQL Database och Azure Synapse Analytics?
titleSuffix: ''
description: Lär dig om logiska SQL-servrar som används av Azure SQL Database och Azure Synapse Analytics och hur du hanterar dem.
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
ms.openlocfilehash: e0736e58983d4c28b8f2f4a20241cd36ac84fdfa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96445387"
---
# <a name="what-is-a-logical-sql-server-in-azure-sql-database-and-azure-synapse"></a>Vad är en logisk SQL-Server i Azure SQL Database-och Azure-Synapse?
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

I Azure SQL Database och Azure Synapse Analytics är en server en logisk konstruktion som fungerar som en central administrativ plats för en samling databaser. På server nivå kan du administrera [inloggningar](logins-create-manage.md), [brand Väggs regler](firewall-configure.md), [gransknings regler](../../azure-sql/database/auditing-overview.md), [principer för hot identifiering](threat-detection-configure.md)och [grupper för automatisk redundans](auto-failover-group-overview.md). En server kan vara i en annan region än dess resurs grupp. Servern måste finnas innan du kan skapa en databas i Azure SQL Database eller i en informations lager databas i Azure Synapse Analytics. Alla databaser som hanteras av en enda server skapas i samma region som servern.

Den här servern är distinkt från en SQL Server-instans som du kanske känner till i den lokala världen. Mer specifikt finns det inga garantier avseende platsen för databaserna eller informations lager databasen i relation till den server som hanterar dem. Dessutom exponerar varken Azure SQL Database eller Azure-Synapse någon åtkomst eller funktioner på instans nivå. Instans databaser i en hanterad instans är däremot alla fysiskt samplacerade – på samma sätt som du är van vid att SQL Server i en lokal eller virtuell dators värld.

När du skapar en-server anger du ett inloggnings konto och lösen ord för servern som har administrativ behörighet till huvud databasen på den servern och alla databaser som skapats på den servern. Det här initiala kontot är ett SQL-inloggningsnamn. Azure SQL Database och Azure Synapse Analytics stöder SQL-autentisering och Azure Active Directory autentisering för autentisering. Information om inloggningar och autentisering finns [i hantera databaser och inloggningar i Azure SQL Database](logins-create-manage.md). Windows-autentisering stöds inte.

En server i SQL Database och Azure-Synapse:

- Skapas i en Azure-prenumeration, men kan flyttas till en annan prenumeration tillsammans med de resurser som den innehåller.
- Är den överordnade resursen för databaser, elastiska pooler och informationslager.
- Innehåller ett namn område för databaser, elastiska pooler och informations lager databasen
- Är en logisk behållare med en stark livs längd – ta bort en server och tar bort dess databaser, elastiska pooler och SQK pooler
- Deltar i [Azure rollbaserad åtkomst kontroll (Azure RBAC)](../../role-based-access-control/overview.md) -databaser, elastiska pooler och informations lager databasen i en server ärver åtkomst rättigheter från servern
- Är ett högordnat element i identiteten för databaser, elastiska pooler och informations lager databasen för Azure Resource Management-syfte (se URL-schemat för databaser och pooler)
- Samlar resurser i en region.
- Tillhandahåller en anslutningsslutpunkt för databasåtkomst (`<serverName>`.database.windows.net)
- Ger åtkomst till metadata för inneslutna resurser via DMV:er genom att ansluta till en huvuddatabas.
- Tillhandahåller omfattning för hanterings principer som gäller för databaserna – inloggningar, brand vägg, granskning, Hot identifiering och sådan
- Begränsas av en kvot inom den överordnade prenumerationen (sex servrar per prenumeration som standard – [Se prenumerations begränsningar här](../../azure-resource-manager/management/azure-subscription-service-limits.md))
- Tillhandahåller omfattningen för databas kvoten och DTU-eller vCore-kvoten för de resurser som den innehåller (till exempel 45 000 DTU)
- Är versions definitions området för funktioner som är aktiverade på inneslutna resurser
- Huvudkontoinloggningar på servernivå kan hantera alla databaser på en server.
- Kan innehålla inloggningar som liknar dem i instanser av SQL Server i din lokala miljö som beviljas åtkomst till en eller flera databaser på servern, och som kan beviljas begränsade administrativa rättigheter. Mer information finns i avsnittet om [inloggningar](logins-create-manage.md).
- Standard sorteringen för alla databaser som skapas på en server är `SQL_LATIN1_GENERAL_CP1_CI_AS` , där `LATIN1_GENERAL` är engelska (USA), `CP1` tecken tabell 1252, `CI` är Skift läges okänslig och `AS` är accenttecken.

## <a name="manage-servers-databases-and-firewalls-using-the-azure-portal"></a>Hantera servrar, databaser och brand väggar med hjälp av Azure Portal

Du kan skapa resurs gruppen för en server i förväg eller samtidigt som du skapar själva servern. Det finns flera metoder för att komma till ett nytt SQL Server-formulär, antingen genom att skapa en ny SQL Server eller som en del av att skapa en ny databas.

### <a name="create-a-blank-server"></a>Skapa en tom Server

Om du vill skapa en server (utan en databas, elastisk pool eller informations lager databas) med hjälp av [Azure Portal](https://portal.azure.com)går du till en tom SQL Server-form (logisk SQL-Server).

### <a name="create-a-blank-or-sample-database-in-azure-sql-database"></a>Skapa en tom databas eller en exempel databas i Azure SQL Database

Om du vill skapa en databas i SQL Database med hjälp av [Azure Portal](https://portal.azure.com)navigerar du till ett tomt SQL Database formulär och anger den begärda informationen. Du kan skapa resurs gruppen och servern i förväg eller när du skapar själva databasen. Du kan skapa en tom databas eller skapa en exempel databas baserad på Adventure Works LT.

  ![skapa databas-1](./media/logical-servers/create-database-1.png)

> [!IMPORTANT]
> Information om hur du väljer pris nivå för din databas finns i [DTU-baserad inköps modell](service-tiers-dtu.md) och [vCore-baserad inköps modell](service-tiers-vcore.md).

Information om hur du skapar en hanterad instans finns i [skapa en hanterad instans](../managed-instance/instance-create-quickstart.md)

### <a name="manage-an-existing-server"></a>Hantera en befintlig server

Om du vill hantera en befintlig server navigerar du till servern med ett antal metoder – till exempel från sidan för en speciell databas, sidan **SQL-servrar** eller sidan **alla resurser** .

Om du vill hantera en befintlig databas går du till sidan **SQL-databaser** och klickar på den databas som du vill hantera. Följande skärm bild visar hur du börjar ställa in en brand vägg på server nivå för en databas från **översikts** sidan för en databas.

   ![brandväggsregler för server](./media/single-database-create-quickstart/server-firewall-rule.png)

> [!IMPORTANT]
> Om du vill konfigurera prestanda egenskaper för en databas, se [DTU-baserad inköps modell](service-tiers-dtu.md) och [vCore-baserad inköps modell](service-tiers-vcore.md).
> [!TIP]
> En Azure Portal snabb start finns i [skapa en databas i SQL Database i Azure Portal](single-database-create-quickstart.md).

## <a name="manage-servers-databases-and-firewalls-using-powershell"></a>Hantera servrar, databaser och brand väggar med PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager-modulen stöds fortfarande, men all framtida utveckling gäller AZ. SQL-modulen. De här cmdletarna finns i [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenten för kommandona i AZ-modulen och i AzureRm-modulerna är i stort sett identiska.

Om du vill skapa och hantera servrar, databaser och brand väggar med Azure PowerShell använder du följande PowerShell-cmdletar. Om du behöver installera eller uppgradera PowerShell, se [installera Azure PowerShell modul](/powershell/azure/install-az-ps). Information om hur du skapar och hanterar elastiska pooler finns i [elastiska pooler](elastic-pool-overview.md).

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

> [!TIP]
> En PowerShell-snabb start finns [i skapa en databas i Azure SQL Database med PowerShell](single-database-create-quickstart.md). För PowerShell-exempel skript, se [Använd PowerShell för att skapa en databas i Azure SQL Database och konfigurera en brand Väggs regel](scripts/create-and-configure-database-powershell.md) och [övervaka och skala en databas i Azure SQL Database med PowerShell](scripts/monitor-and-scale-database-powershell.md).
>

## <a name="manage-servers-databases-and-firewalls-using-the-azure-cli"></a>Hantera servrar, databaser och brand väggar med hjälp av Azure CLI

Använd följande [Azure cli SQL Database](/cli/azure/sql/db) -kommandon för att skapa och hantera servrar, databaser och brand väggar med [Azure CLI](/cli/azure). Använd [Cloud Shell](../../cloud-shell/overview.md) för att köra CLI i webbläsaren eller [installera](/cli/azure/install-azure-cli) det på macOS, Linux eller Windows. Information om hur du skapar och hanterar elastiska pooler finns i [elastiska pooler](elastic-pool-overview.md).

| Cmdlet | Beskrivning |
| --- | --- |
|[az sql db create](/cli/azure/sql/db#az-sql-db-create) |Skapar en databas|
|[AZ SQL DB-lista](/cli/azure/sql/db#az-sql-db-list)|Visar alla databaser som hanteras av en server eller alla databaser i en elastisk pool|
|[AZ SQL DB List-Editions](/cli/azure/sql/db#az-sql-db-list-editions)|Visar en lista över tillgängliga tjänst mål och lagrings gränser|
|[AZ SQL DB List-usages](/cli/azure/sql/db#az-sql-db-list-usages)|Returnerar databas användningar|
|[AZ SQL DB show](/cli/azure/sql/db#az-sql-db-show)|Hämtar en databas
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

> [!TIP]
> En snabb start för Azure CLI finns [i skapa en databas i Azure SQL Database med Azure CLI](az-cli-script-samples-content-guide.md). Exempel skript för Azure CLI finns i [använda CLI för att skapa en databas i Azure SQL Database och konfigurera en brand Väggs regel](scripts/create-and-configure-database-cli.md) och [använda CLI för att övervaka och skala en databas i Azure SQL Database](scripts/monitor-and-scale-database-cli.md).
>

## <a name="manage-servers-databases-and-firewalls-using-transact-sql"></a>Hantera servrar, databaser och brand väggar med hjälp av Transact-SQL

Använd följande T-SQL-kommandon för att skapa och hantera servrar, databaser och brand väggar med Transact-SQL. Du kan utfärda dessa kommandon med hjälp av Azure Portal, [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs)eller andra program som kan ansluta till en server och skicka Transact-SQL-kommandon. Information om hur du hanterar elastiska pooler finns i [elastiska pooler](elastic-pool-overview.md).

> [!IMPORTANT]
> Du kan inte skapa eller ta bort en server med hjälp av Transact-SQL.

| Kommando | Beskrivning |
| --- | --- |
|[SKAPA databas (Azure SQL Database)](/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current) | Skapar en ny databas i Azure SQL Database. Du måste vara ansluten till huvud databasen för att skapa en ny databas.|
|[SKAPA databas (Azure-Synapse)](/sql/t-sql/statements/create-database-transact-sql?view=azure-sqldw-latest) | Skapar en ny informations lager databas i Azure-Synapse. Du måste vara ansluten till huvud databasen för att skapa en ny databas.|
| [ALTER DATABASE (Azure SQL Database)](/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current) |Ändrar databas eller elastisk pool. |
|[ALTER DATABASE (Azure Synapse Analytics)](/sql/t-sql/statements/alter-database-transact-sql?view=sql-server-ver15)|Ändrar en informations lager databas i Azure Synapse.|
|[SLÄPP databas (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Tar bort en databas.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Returnerar utgåvan (tjänst nivån), tjänst målet (pris nivån) och namnet på den elastiska poolen, om det finns någon, för en databas. Om du är inloggad på huvud databasen för en server returneras information om alla databaser. Du måste vara ansluten till huvud databasen för Azure-Synapse.|
|[sys.dm_db_resource_stats (Azure SQL Database)](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-resource-stats-azure-sql-database)| Returnerar CPU-, IO-och minnes förbrukning för en databas i Azure SQL Database. Det finns en rad för var 15: e sekund, även om det inte finns någon aktivitet i databasen.|
|[sys.resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-resource-stats-azure-sql-database)|Returnerar CPU-användning och lagrings data för en databas i Azure SQL Database. Data samlas in och sammanställs inom fem minuters intervall.|
|[sys.database_connection_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-connection-stats-azure-sql-database)|Innehåller statistik för databas anslutnings händelser för Azure SQL Database, vilket ger en översikt över lyckade och misslyckade databas anslutningar. |
|[sys.event_log (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-event-log-azure-sql-database)|Returnerar lyckade Azure SQL Database databas anslutningar, anslutnings problem och död lägen för Azure SQL Database. Du kan använda den här informationen för att spåra eller felsöka din databas aktivitet.|
|[sp_set_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database)|Skapar eller uppdaterar brand Väggs inställningarna på server nivå för servern. Den här lagrade proceduren är bara tillgänglig i huvud databasen för inloggning på server nivå. Det går bara att skapa en brand Väggs regel på server nivå med hjälp av Transact-SQL när den första brand Väggs regeln på server nivå har skapats av en användare med Azure-nivå behörigheter|
|[sys.firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database)|Returnerar information om de brand Väggs inställningar på server nivå som är associerade med en server.|
|[sp_delete_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database)|Tar bort brand Väggs inställningar på server nivå från en server. Den här lagrade proceduren är bara tillgänglig i huvud databasen för inloggning på server nivå.|
|[sp_set_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)|Skapar eller uppdaterar brand Väggs reglerna på databas nivå för en databas i Azure SQL Database. Regler för databas brand vägg kan konfigureras för huvud databasen och för användar databaser i SQL Database. Regler för databas brand väggar är användbara när inneslutna databas användare används. Regler för databas brand väggar stöds inte i Azure-Synapse.|
|[sys.database_firewall_rules (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database)|Returnerar information om brand Väggs inställningarna på databas nivå för en databas i Azure SQL Database. |
|[sp_delete_database_firewall_rule (Azure SQL Database)](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database)|Tar bort brand Väggs inställningen på databas nivå för en databas med din Azure SQL Database. |

> [!TIP]
> En snabb start som använder SQL Server Management Studio på Microsoft Windows finns [Azure SQL Database: använd SQL Server Management Studio för att ansluta och fråga efter data](connect-query-ssms.md). En snabb start med Visual Studio Code på macOS, Linux eller Windows finns [Azure SQL Database: använda Visual Studio Code för att ansluta och fråga efter data](connect-query-vscode.md).

## <a name="manage-servers-databases-and-firewalls-using-the-rest-api"></a>Hantera servrar, databaser och brand väggar med hjälp av REST API

Använd dessa REST API begär Anden för att skapa och hantera servrar, databaser och brand väggar.

| Kommando | Beskrivning |
| --- | --- |
|[Servrar-skapa eller uppdatera](/rest/api/sql/servers/createorupdate)|Skapar eller uppdaterar en ny server.|
|[Servrar-ta bort](/rest/api/sql/servers/delete)|Tar bort en server.|
|[Servrar-Hämta](/rest/api/sql/servers/get)|Hämtar en server.|
|[Servrar-lista](/rest/api/sql/servers/list)|Returnerar en lista med servrar.|
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

- Information om hur du migrerar en SQL Server databas till Azure SQL Database finns i [migrera till Azure SQL Database](migrate-to-database-from-sql-server.md).
- Information om vilka funktioner som stöds finns i avsnittet [Funktioner](features-comparison.md).