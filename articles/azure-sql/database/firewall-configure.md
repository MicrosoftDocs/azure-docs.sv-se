---
title: IP-brandväggsregler
description: Konfigurera IP-brandväggsregler på servernivå för en databas i Azure SQL Database eller Azure Synapse Analytics brandväggen. Hantera åtkomst och konfigurera IP-brandväggsregler på databasnivå för SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: security
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.custom: sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 06/17/2020
ms.openlocfilehash: 200df14e7d18c4bdfb903bef46c169f6f7bf5ca5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107781783"
---
# <a name="azure-sql-database-and-azure-synapse-ip-firewall-rules"></a>Azure SQL Database och Azure Synapse IP-brandväggsregler
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

När du skapar en ny server i Azure SQL Database eller Azure Synapse Analytics med namnet *mysqlserver,* blockerar till exempel en brandvägg på servernivå all åtkomst till den offentliga slutpunkten för servern (som är tillgänglig *på mysqlserver.database.windows.net*). För enkelhetens *skull används SQL Database* att referera till både SQL Database och Azure Synapse Analytics.

> [!IMPORTANT]
> Den här artikeln *gäller* inte för *Azure SQL Managed Instance*. Information om nätverkskonfiguration finns i [Ansluta ditt program till Azure SQL Managed Instance](../managed-instance/connect-application-instance.md).
>
> Azure Synapse stöder endast IP-brandväggsregler på servernivå. Den stöder inte IP-brandväggsregler på databasnivå.

## <a name="how-the-firewall-works"></a>Så här fungerar brandväggen

Anslutningsförsök från Internet och Azure måste passera brandväggen innan de når servern eller databasen, som du ser i följande diagram.

   ![Diagram över brandväggskonfiguration][1]

### <a name="server-level-ip-firewall-rules"></a>IP-brandväggsregler på servernivå

Dessa regler gör att klienter kan komma åt hela servern, det vill säga alla databaser som hanteras av servern. Reglerna lagras i *huvuddatabasen.* Du kan ha upp till 128 IP-brandväggsregler på servernivå för en server. Om du har inställningen **Tillåt Azure-tjänster** och resurser att komma åt den här servern aktiverad räknas detta som en enskild brandväggsregel för servern.
  
Du kan konfigurera IP-brandväggsregler på servernivå med hjälp Azure Portal, PowerShell eller Transact-SQL-instruktioner.

- Om du vill använda portalen eller PowerShell måste du vara prenumerationsägare eller prenumerationsdeltagare.
- Om du vill använda Transact-SQL  måste du ansluta till huvuddatabasen som huvudsaklig inloggning på servernivå eller som Azure Active Directory administratör. (En IP-brandväggsregel på servernivå måste först skapas av en användare som har behörigheter på Azure-nivå.)

> [!NOTE]
> När du skapar en ny logisk SQL-server från Azure Portal anges inställningen Tillåt **Azure-tjänster** och resurser att komma åt den här servern som **standard.**

### <a name="database-level-ip-firewall-rules"></a>IP-brandväggsregler på databasnivå

IP-brandväggsregler på databasnivå gör det möjligt för klienter att komma åt vissa (säkra) databaser. Du skapar reglerna för varje databas (inklusive *huvuddatabasen)* och de lagras i den enskilda databasen.
  
- Du kan bara skapa och hantera IP-brandväggsregler på databasnivå för huvud- och användardatabaser med hjälp av Transact-SQL-instruktioner och endast när du har konfigurerat den första brandväggen på servernivå.
- Om du anger ett IP-adressintervall i IP-brandväggsregeln på databasnivå som ligger utanför intervallet i IP-brandväggsregeln på servernivå kan endast klienter som har IP-adresser i databasnivåintervallet komma åt databasen.
- Du kan ha högst 128 IP-brandväggsregler på databasnivå för en databas. Mer information om hur du konfigurerar IP-brandväggsregler på databasnivå finns i exemplet senare i den här artikeln och [sp_set_database_firewall_rule (Azure SQL Database).](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database)

### <a name="recommendations-for-how-to-set-firewall-rules"></a>Rekommendationer för hur du anger brandväggsregler

Vi rekommenderar att du använder IP-brandväggsregler på databasnivå när det är möjligt. Den här metoden förbättrar säkerheten och gör din databas mer portabel. Använd IP-brandväggsregler på servernivå för administratörer. Använd dem också när du har många databaser som har samma åtkomstkrav och du inte vill konfigurera varje databas individuellt.

> [!NOTE]
> Information om portabla databaser i kontexten för företagskontinuitet finns i [Autentiseringskrav för haveriberedskap](active-geo-replication-security-configure.md).

## <a name="server-level-versus-database-level-ip-firewall-rules"></a>IP-brandväggsregler på servernivå kontra databasnivå

*Ska användare av en databas vara helt isolerade från en annan databas?*

Om *ja,* använd IP-brandväggsregler på databasnivå för att bevilja åtkomst. Den här metoden undviker att använda IP-brandväggsregler på servernivå, som tillåter åtkomst genom brandväggen till alla databaser. Det skulle minska djupet i dina försvar.

*Behöver användare på IP-adresserna åtkomst till alla databaser?*

Om *ja,* använd IP-brandväggsregler på servernivå för att minska antalet gånger som du måste konfigurera IP-brandväggsregler.

*Har den person eller det team som konfigurerar IP-brandväggsreglerna endast åtkomst via Azure Portal, PowerShell eller REST API?*

I så fall måste du använda IP-brandväggsregler på servernivå. IP-brandväggsregler på databasnivå kan bara konfigureras via Transact-SQL.  

*Är den person eller det team som konfigurerar IP-brandväggsreglerna förbjudna från att ha hög behörighet på databasnivå?*

I så fall använder du IP-brandväggsregler på servernivå. Du behöver minst *behörigheten CONTROL DATABASE* på databasnivå för att konfigurera IP-brandväggsregler på databasnivå via Transact-SQL.  

*Hanterar den person eller det team som konfigurerar eller granskar IP-brandväggsreglerna centralt IP-brandväggsregler för många (kanske hundratals) databaser?*

I det här scenariot bestäms bästa praxis av dina behov och din miljö. IP-brandväggsregler på servernivå kan vara enklare att konfigurera, men skript kan konfigurera regler på databasnivå. Och även om du använder IP-brandväggsregler på servernivå kan du behöva granska  IP-brandväggsregler på databasnivå för att se om användare med kontrollbehörighet på databasen skapar IP-brandväggsregler på databasnivå.

*Kan jag använda en blandning av IP-brandväggsregler på servernivå och databasnivå?*

Ja. Vissa användare, till exempel administratörer, kan behöva IP-brandväggsregler på servernivå. Andra användare, till exempel användare av ett databasprogram, kan behöva IP-brandväggsregler på databasnivå.

### <a name="connections-from-the-internet"></a>Anslutningar från Internet

När en dator försöker ansluta till servern från Internet kontrollerar brandväggen först den ursprungliga IP-adressen för begäran mot IP-brandväggsreglerna på databasnivå för databasen som anslutningen begär.

- Om adressen ligger inom ett intervall som anges i IP-brandväggsreglerna på databasnivå beviljas anslutningen till den databas som innehåller regeln.
- Om adressen inte ligger inom ett intervall i IP-brandväggsreglerna på databasnivå kontrollerar brandväggen IP-brandväggsreglerna på servernivå. Om adressen ligger inom ett intervall som finns i IP-brandväggsreglerna på servernivå beviljas anslutningen. IP-brandväggsregler på servernivå gäller för alla databaser som hanteras av servern.  
- Om adressen inte ligger inom ett intervall som finns i någon av IP-brandväggsreglerna på databas- eller servernivå misslyckas anslutningsbegäran.

> [!NOTE]
> Om du Azure SQL Database från din lokala dator måste du se till att brandväggen i nätverket och den lokala datorn tillåter utgående kommunikation via TCP-port 1433.

### <a name="connections-from-inside-azure"></a>Anslutningar inifrån Azure

Om du vill tillåta att program som finns i Azure ansluter till DIN SQL-server måste Azure-anslutningar vara aktiverade. Om du vill aktivera Azure-anslutningar måste det finnas en brandväggsregel med start- och slut-IP-adresser inställda på 0.0.0.0.

När ett program från Azure försöker ansluta till servern kontrollerar brandväggen att Azure-anslutningar tillåts genom att verifiera att brandväggsregeln finns. Detta kan aktiveras direkt från bladet Azure Portal genom att växla Tillåt **Azure-tjänster** och resurser att komma åt den här servern till **PÅ** i inställningarna för **Brandväggar och virtuella** nätverk. Om du anger PÅ skapas en inkommande brandväggsregel för IP-adressen 0.0.0.0 – 0.0.0.0 med namnet **AllowAllWindowsIP**. Använd PowerShell eller Azure CLI för att skapa en brandväggsregel med start- och slut-IP-adresser inställda på 0.0.0.0 om du inte använder portalen. 

> [!IMPORTANT]
> Det här alternativet konfigurerar brandväggen så att den tillåter alla anslutningar från Azure, inklusive anslutningar från prenumerationer från andra kunder. Om du väljer det här alternativet kontrollerar du att dina inloggnings- och användarbehörigheter begränsar åtkomsten till endast behöriga användare.

## <a name="permissions"></a>Behörigheter

För att kunna skapa och hantera IP-brandväggsregler för Azure SQL Server måste du ha antingen:

- i rollen [SQL Server deltagare](../../role-based-access-control/built-in-roles.md#sql-server-contributor)
- i [SQL Security Manager-rollen](../../role-based-access-control/built-in-roles.md#sql-security-manager)
- ägaren till den resurs som innehåller Azure SQL Server

## <a name="create-and-manage-ip-firewall-rules"></a>Skapa och hantera IP-brandväggsregler

Du skapar den första brandväggsinställningen på servernivå med hjälp [av Azure Portal](https://portal.azure.com/) eller programmässigt med hjälp [av Azure PowerShell,](/powershell/module/az.sql) [Azure CLI](/cli/azure/sql/server/firewall-rule)eller en [Azure-REST API](/rest/api/sql/firewallrules/createorupdate). Du skapar och hanterar ytterligare IP-brandväggsregler på servernivå med hjälp av dessa metoder eller Transact-SQL.

> [!IMPORTANT]
> IP-brandväggsregler på databasnivå kan bara skapas och hanteras med hjälp av Transact-SQL.

För att förbättra prestanda cachelagras IP-brandväggsregler på servernivå tillfälligt på databasnivå. Information om hur du uppdaterar cacheminnet finns i [DBCC FLUSHAUTHCACHE](/sql/t-sql/database-console-commands/dbcc-flushauthcache-transact-sql).

> [!TIP]
> Du kan använda [databasgranskning för](../../azure-sql/database/auditing-overview.md) att granska brandväggsändringar på servernivå och databasnivå.

### <a name="use-the-azure-portal-to-manage-server-level-ip-firewall-rules"></a>Använd Azure Portal för att hantera IP-brandväggsregler på servernivå

Om du vill ange en IP-brandväggsregel på servernivå i Azure Portal du till översiktssidan för databasen eller servern.

> [!TIP]
> En självstudiekurs finns [i Skapa en databas med hjälp av Azure Portal](single-database-create-quickstart.md).

#### <a name="from-the-database-overview-page"></a>Från databasens översiktssida

1. Om du vill ange en IP-brandväggsregel på servernivå från databasens översiktssida väljer du Ange **serverns** brandvägg i verktygsfältet, som du ser i följande bild.

    ![Ip-brandväggsregel för server](./media/firewall-configure/sql-database-server-set-firewall-rule.png)

    Sidan **Brandväggsinställningar** för servern öppnas.

2. Välj **Lägg till klient-IP** i verktygsfältet för att lägga till IP-adressen för den dator som du använder och välj sedan **Spara.** En IP-brandväggsregel på servernivå skapas för din aktuella IP-adress.

    ![Ange IP-brandväggsregel på servernivå](./media/firewall-configure/sql-database-server-firewall-settings.png)

#### <a name="from-the-server-overview-page"></a>Från översiktssidan för servern

Översiktssidan för servern öppnas. Den visar det fullständigt kvalificerade servernamnet (till exempel *mynewserver20170403.database.windows.net*) och innehåller alternativ för ytterligare konfiguration.

1. Om du vill ange en regel på servernivå på den här sidan **väljer** du Brandvägg **på menyn** Inställningar till vänster.

2. Välj **Lägg till klient-IP** i verktygsfältet för att lägga till IP-adressen för den dator som du använder och välj sedan **Spara.** En IP-brandväggsregel på servernivå skapas för din aktuella IP-adress.

### <a name="use-transact-sql-to-manage-ip-firewall-rules"></a>Använda Transact-SQL för att hantera IP-brandväggsregler

| Katalogvy eller lagrad procedur | Nivå | Description |
| --- | --- | --- |
| [sys.firewall_rules](/sql/relational-databases/system-catalog-views/sys-firewall-rules-azure-sql-database) |Server |Visar aktuella IP-brandväggsregler på servernivå |
| [sp_set_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-firewall-rule-azure-sql-database) |Server |Skapar eller uppdaterar IP-brandväggsregler på servernivå |
| [sp_delete_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-firewall-rule-azure-sql-database) |Server |Tar bort IP-brandväggsregler på servernivå |
| [sys.database_firewall_rules](/sql/relational-databases/system-catalog-views/sys-database-firewall-rules-azure-sql-database) |Databas |Visar de aktuella IP-brandväggsreglerna på databasnivå |
| [sp_set_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-set-database-firewall-rule-azure-sql-database) |Databas |Skapar eller uppdaterar IP-brandväggsregler på databasnivå |
| [sp_delete_database_firewall_rule](/sql/relational-databases/system-stored-procedures/sp-delete-database-firewall-rule-azure-sql-database) |Databaser |Tar bort IP-brandväggsregler på databasnivå |

I följande exempel granskas de befintliga reglerna, aktiverar ett intervall med IP-adresser på *servern Contoso* och tar bort en IP-brandväggsregel:

```sql
SELECT * FROM sys.firewall_rules ORDER BY name;
```

Lägg sedan till en IP-brandväggsregel på servernivå.

```sql
EXECUTE sp_set_firewall_rule @name = N'ContosoFirewallRule',
   @start_ip_address = '192.168.1.1', @end_ip_address = '192.168.1.10'
```

Om du vill ta bort en IP-brandväggsregel på servernivå kör *sp_delete_firewall_rule lagrade* proceduren. I följande exempel tas regeln *ContosoFirewallRule bort:*

```sql
EXECUTE sp_delete_firewall_rule @name = N'ContosoFirewallRule'
```

### <a name="use-powershell-to-manage-server-level-ip-firewall-rules"></a>Använda PowerShell för att hantera IP-brandväggsregler på servernivå

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]
> [!IMPORTANT]
> PowerShell Azure Resource Manager modulen stöds fortfarande av Azure SQL Database, men all utveckling är nu för Az.Sql-modulen. Dessa cmdlets finns i [AzureRM.Sql](/powershell/module/AzureRM.Sql/). Argumenten för kommandona i Az- och AzureRm-modulerna är i stort sett identiska.

| Cmdlet | Nivå | Description |
| --- | --- | --- |
| [Get-AzSqlServerFirewallRule](/powershell/module/az.sql/get-azsqlserverfirewallrule) |Server |Returnerar de aktuella brandväggsreglerna på servernivå |
| [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule) |Server |Skapar en ny brandväggsregel på servernivå |
| [Set-AzSqlServerFirewallRule](/powershell/module/az.sql/set-azsqlserverfirewallrule) |Server |Uppdaterar egenskaperna för en befintlig brandväggsregel på servernivå |
| [Remove-AzSqlServerFirewallRule](/powershell/module/az.sql/remove-azsqlserverfirewallrule) |Server |Tar bort brandväggsregler på servernivå |

I följande exempel används PowerShell för att ange en IP-brandväggsregel på servernivå:

```powershell
New-AzSqlServerFirewallRule -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -FirewallRuleName "ContosoIPRange" -StartIpAddress "192.168.1.0" -EndIpAddress "192.168.1.255"
```

> [!TIP]
> Ange $servername servernamnet och inte det fullständigt kvalificerade DNS-namnet, t.ex. **ange mysqldbserver** i stället för **mysqldbserver.database.windows.net**
>
> PowerShell-exempel i kontexten för en snabbstart finns i [Skapa DB – PowerShell](powershell-script-content-guide.md) och Skapa en enkel databas och konfigurera en IP-brandväggsregel på servernivå med [hjälp av PowerShell](scripts/create-and-configure-database-powershell.md).

### <a name="use-cli-to-manage-server-level-ip-firewall-rules"></a>Använda CLI för att hantera IP-brandväggsregler på servernivå

| Cmdlet | Nivå | Description |
| --- | --- | --- |
|[az sql server firewall-rule create](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_create)|Server|Skapar en IP-brandväggsregel för servern|
|[az sql server firewall-rule list](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_list)|Server|Visar en lista över IP-brandväggsregler på en server|
|[az sql server firewall-rule show](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_show)|Server|Visar information om en IP-brandväggsregel|
|[az sql server firewall-rule update](/cli/azure/sql/server/firewall-rule##az_sql_server_firewall_rule_update)|Server|Uppdaterar en IP-brandväggsregel|
|[az sql server firewall-rule delete](/cli/azure/sql/server/firewall-rule#az_sql_server_firewall_rule_delete)|Server|Tar bort en IP-brandväggsregel|

I följande exempel används CLI för att ange en IP-brandväggsregel på servernivå:

```azurecli-interactive
az sql server firewall-rule create --resource-group myResourceGroup --server $servername \
-n ContosoIPRange --start-ip-address 192.168.1.0 --end-ip-address 192.168.1.255
```

> [!TIP]
> Ange $servername servernamnet och inte det fullständigt kvalificerade DNS-namnet, t.ex. **ange mysqldbserver** i stället för **mysqldbserver.database.windows.net**
>
> Ett CLI-exempel i kontexten för en snabbstart finns i [Skapa DB – Azure CLI](az-cli-script-samples-content-guide.md) och Skapa en enkel databas och konfigurera en IP-brandväggsregel på servernivå med [hjälp av Azure CLI.](scripts/create-and-configure-database-cli.md)

### <a name="use-a-rest-api-to-manage-server-level-ip-firewall-rules"></a>Använda en REST API för att hantera IP-brandväggsregler på servernivå

| API | Nivå | Description |
| --- | --- | --- |
| [Lista brandväggsregler](/rest/api/sql/firewallrules/listbyserver) |Server |Visar aktuella IP-brandväggsregler på servernivå |
| [Skapa eller uppdatera brandväggsregler](/rest/api/sql/firewallrules/createorupdate) |Server |Skapar eller uppdaterar IP-brandväggsregler på servernivå |
| [Ta bort brandväggsregler](/rest/api/sql/firewallrules/delete) |Server |Tar bort IP-brandväggsregler på servernivå |
| [Hämta brandväggsregler](/rest/api/sql/firewallrules/get) | Server | Hämtar IP-brandväggsregler på servernivå |

## <a name="troubleshoot-the-database-firewall"></a>Felsöka databasbrandväggen

Tänk på följande när åtkomsten till Azure SQL Database inte fungerar som förväntat.

- **Konfiguration av lokal brandvägg:**

  Innan datorn kan komma åt Azure SQL Database kan du behöva skapa ett brandväggsfel på datorn för TCP-port 1433. Om du vill skapa anslutningar inom gränsen för Azure-molnet kan du behöva öppna ytterligare portar. Mer information finns i avsnittet "SQL Database: Utanför eller inuti" i Portar [utöver 1433 för ADO.NET 4.5 och Azure SQL Database](adonet-v12-develop-direct-route-ports.md).

- **Översättning av nätverksadresser:**

  På grund av NAT (Network Address Translation) kan IP-adressen som används av datorn för att ansluta till Azure SQL Database vara annorlunda än IP-adressen i datorns IP-konfigurationsinställningar. Så här visar du IP-adressen som datorn använder för att ansluta till Azure:
    1. Logga in på portalen.
    1. Gå till fliken **Konfigurera** på den server som är värd för din databas.
    1. Den **aktuella klientens IP-adress** visas i **avsnittet Tillåtna IP-adresser.** Välj **Lägg till** för **Tillåtna IP-adresser** för att tillåta att den här datorn får åtkomst till servern.

- **Ändringar i listan över tillåtna har inte effekt ännu:**

  Det kan uppstå en fördröjning på upp till fem minuter innan ändringar Azure SQL Database brandväggskonfigurationen ska gälla.

- **Inloggningen har inte auktoriserats eller så har ett felaktigt lösenord använts:**

  Om en inloggning inte har behörighet på servern eller om lösenordet är felaktigt, nekas anslutningen till servern. Genom att skapa en brandväggsinställning får *klienterna* bara möjlighet att försöka ansluta till servern. Klienten måste fortfarande ange nödvändiga säkerhetsautentiseringsuppgifter. Mer information om hur du förbereder inloggningar finns i [Kontrollera och bevilja databasåtkomst.](logins-create-manage.md)

- **Dynamisk IP-adress:**

  Om du har en Internetanslutning som använder dynamisk IP-adressering och du har problem med att ta dig igenom brandväggen kan du prova någon av följande lösningar:
  
  - Be internetleverantören om IP-adressintervallet som har tilldelats klientdatorerna som har åtkomst till servern. Lägg till IP-adressintervallet som en IP-brandväggsregel.
  - Hämta statisk IP-adressering i stället för dina klientdatorer. Lägg till IP-adresserna som IP-brandväggsregler.

## <a name="next-steps"></a>Nästa steg

- Bekräfta att företagets nätverksmiljö tillåter inkommande kommunikation från de IP-adressintervall för beräkning (inklusive SQL-intervall) som används av Azure-datacentren. Du kan behöva lägga till dessa IP-adresser i listan över tillåtna adresser. Se [Microsoft Azure IP-intervall för datacenter.](https://www.microsoft.com/download/details.aspx?id=41653)  
- Se vår snabbstart om [att skapa en enkel databas i Azure SQL Database](single-database-create-quickstart.md).
- Hjälp med att ansluta till en databas i Azure SQL Database från program med öppen källkod eller program från tredje part finns i Klient [snabbstartskodexempel för att Azure SQL Database](connect-query-content-reference-guide.md#libraries).
- Information om ytterligare portar som du kan behöva öppna finns i avsnittet "SQL Database: Utanför kontra inuti" i Portar utöver [1433 för ADO.NET 4.5 och SQL Database](adonet-v12-develop-direct-route-ports.md)
- En översikt över Azure SQL Database finns i [Skydda databasen](security-overview.md).

<!--Image references-->
[1]: ./media/firewall-configure/sqldb-firewall-1.png
