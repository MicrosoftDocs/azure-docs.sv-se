---
title: Aktivera SQL-insikter
description: Aktivera SQL-insikter i Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.openlocfilehash: 012aa364fe9e379455b6b63f7c9e541d2d5b97ed
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726906"
---
# <a name="enable-sql-insights-preview"></a>Aktivera SQL-insikter (förhandsversion)
Den här artikeln beskriver hur du aktiverar [SQL-insikter för](sql-insights-overview.md) att övervaka dina SQL-distributioner. Övervakning utförs från en virtuell Azure-dator som upprättar en anslutning till dina SQL-distributioner och använder dynamiska hanteringsvyer (DMV:er) för att samla in övervakningsdata. Du kan styra vilka datauppsättningar som samlas in och frekvensen för insamling med hjälp av en övervakningsprofil.

> [!NOTE]
> Information om hur du aktiverar SQL-insikter genom att skapa övervakningsprofilen och den virtuella datorn med hjälp av en Resource Manager-mall finns i [Resource Manager exempel på SQL-insikter.](resource-manager-sql-insights.md)

## <a name="create-log-analytics-workspace"></a>Skapa Log Analytics-arbetsyta
SQL-insikter lagrar sina data på en eller flera [Log Analytics-arbetsytor.](../logs/data-platform-logs.md#log-analytics-workspaces)  Innan du kan aktivera SQL Insights måste du antingen [skapa en arbetsyta](../logs/quick-create-workspace.md) eller välja en befintlig. En enskild arbetsyta kan användas med flera övervakningsprofiler, men arbetsytan och profilerna måste finnas i samma Azure-region. Om du vill aktivera och komma åt funktionerna i SQL Insights måste du ha [Log Analytics-deltagarrollen](../logs/manage-access.md) på arbetsytan. 

## <a name="create-monitoring-user"></a>Skapa övervakningsanvändare 
Du behöver en användare i de SQL-distributioner som du vill övervaka. Följ procedurerna nedan för olika typer av SQL-distributioner.

Anvisningarna nedan omfattar processen per typ av SQL som du kan övervaka.  Om du vill göra detta med ett skript på flera SQL-databaser samtidigt läser du följande [README-fil och](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/SQL%20Insights%20Onboarding%20Scripts/Permissions_LoginUser_Account_Creation-README.txt) [exempelskript](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/SQL%20Insights%20Onboarding%20Scripts/Permissions_LoginUser_Account_Creation.ps1).


### <a name="azure-sql-database"></a>Azure SQL-databas
Öppna Azure SQL Database med [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) eller [Frågeredigeraren (förhandsversion)](../../azure-sql/database/connect-query-portal.md) i Azure Portal.

Kör följande skript för att skapa en användare med de behörigheter som krävs. Ersätt *användaren* med ett användarnamn och *mystrongpassword* med ett lösenord.

```sql
CREATE USER [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW DATABASE STATE TO [user]; 
GO 
```

:::image type="content" source="media/sql-insights-enable/telegraf-user-database-script.png" alt-text="Skapa telegraf-användarskript." lightbox="media/sql-insights-enable/telegraf-user-database-script.png":::

Kontrollera att användaren har skapats.

:::image type="content" source="media/sql-insights-enable/telegraf-user-database-verify.png" alt-text="Verifiera telegraf-användarskriptet." lightbox="media/sql-insights-enable/telegraf-user-database-verify.png":::

```sql
select name as username,
       create_date,
       modify_date,
       type_desc as type,
       authentication_type_desc as authentication_type
from sys.database_principals
where type not in ('A', 'G', 'R', 'X')
       and sid is not null
order by username
```

### <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance
Logga in på Azure SQL Managed Instance och [använd SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) eller liknande för att köra följande skript för att skapa övervakningsanvändaren med de behörigheter som krävs. Ersätt *användaren* med ett användarnamn och *mystrongpassword* med ett lösenord.

 
```sql
USE master; 
GO 
CREATE LOGIN [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW SERVER STATE TO [user]; 
GO 
GRANT VIEW ANY DEFINITION TO [user]; 
GO 
```

### <a name="sql-server"></a>SQL Server
Logga in på den virtuella Azure-dator som kör SQL Server och använd [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) eller liknande verktyg för att köra följande skript för att skapa övervakningsanvändaren med de behörigheter som krävs. Ersätt *användaren* med ett användarnamn och *mystrongpassword* med ett lösenord.

 
```sql
USE master; 
GO 
CREATE LOGIN [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW SERVER STATE TO [user]; 
GO 
GRANT VIEW ANY DEFINITION TO [user]; 
GO
```

Kontrollera att användaren har skapats.

```sql
select name as username,
       create_date,
       modify_date,
       type_desc as type
from sys.server_principals
where type not in ('A', 'G', 'R', 'X')
       and sid is not null
order by username
```

## <a name="create-azure-virtual-machine"></a>Skapa en virtuell Azure-dator 
Du måste skapa en eller flera virtuella Azure-datorer som ska användas för att samla in data för att övervaka SQL.  

> [!NOTE]
>  [Övervakningsprofilerna](#create-sql-monitoring-profile) anger vilka data du ska samla in från de olika typerna av SQL som du vill övervaka. Varje virtuell dator för övervakning kan bara ha en associerad övervakningsprofil. Om du behöver flera övervakningsprofiler måste du skapa en virtuell dator för var och en.

### <a name="azure-virtual-machine-requirements"></a>Krav för virtuell Azure-dator
De virtuella Azure-datorerna har följande krav.

- Operativsystem: Ubuntu 18.04 
- Rekommenderade storlekar för virtuella Azure-datorer: Standard_B2s (2 processorer, 4 GiB minne) 
- Regioner som stöds: Alla [regioner som stöds av Azure Monitor agenten](../agents/azure-monitor-agent-overview.md#supported-regions)

> [!NOTE]
> Storleken Standard_B2s (2 processorer, 4 GiB minne) stöder upp till 100 anslutningssträngar. Du bör inte allokera fler än 100 anslutningar till en enda virtuell dator.

Beroende på nätverksinställningarna för dina SQL-resurser kan de virtuella datorerna behöva placeras i samma virtuella nätverk som dina SQL-resurser så att de kan göra nätverksanslutningar för att samla in övervakningsdata.  

## <a name="configure-network-settings"></a>Konfigurera nätverksinställningar
Varje typ av SQL erbjuder metoder för att din virtuella övervakningsdator ska få säker åtkomst till SQL.  I avsnitten nedan beskrivs alternativen baserat på typen av SQL.

### <a name="azure-sql-databases"></a>Azure SQL Databases  

SQL-insikter stöder åtkomst Azure SQL Database via både den offentliga slutpunkten och det virtuella nätverket.

För åtkomst via den offentliga slutpunkten lägger du till en regel under sidan **Brandväggsinställningar** och avsnittet [IP-brandväggsinställningar.](https://docs.microsoft.com/azure/azure-sql/database/network-access-controls-overview#ip-firewall-rules)  Om du vill ange åtkomst från [](https://docs.microsoft.com/azure/azure-sql/database/network-access-controls-overview#virtual-network-firewall-rules) ett virtuellt nätverk kan du ange brandväggsregler för virtuella nätverk och ange de tjänsttaggar som [krävs av Azure Monitor agenten](https://docs.microsoft.com/azure/azure-monitor/agents/azure-monitor-agent-overview#networking).  [I den](https://docs.microsoft.com/azure/azure-sql/database/network-access-controls-overview#ip-vs-virtual-network-firewall-rules) här artikeln beskrivs skillnaderna mellan dessa två typer av brandväggsregler.

:::image type="content" source="media/sql-insights-enable/set-server-firewall.png" alt-text="Konfigurera serverns brandvägg" lightbox="media/sql-insights-enable/set-server-firewall.png":::

:::image type="content" source="media/sql-insights-enable/firewall-settings.png" alt-text="Brandväggsinställningar." lightbox="media/sql-insights-enable/firewall-settings.png":::


### <a name="azure-sql-managed-instances"></a>Azure SQL Managed Instance 

Om den virtuella övervakningsdatorn kommer att finnas i samma virtuella nätverk som dina SQL MI-resurser kan du se [Anslut i samma virtuella nätverk.](https://docs.microsoft.com/azure/azure-sql/managed-instance/connect-application-instance#connect-inside-the-same-vnet) Om den virtuella övervakningsdatorn kommer att finnas i ett annat virtuellt nätverk än dina SQL MI-resurser kan du se [Anslut i ett annat virtuellt nätverk.](https://docs.microsoft.com/azure/azure-sql/managed-instance/connect-application-instance#connect-inside-a-different-vnet)


### <a name="azure-virtual-machine-and-azure-sql-virtual-machine"></a>Virtuell Azure-dator Azure SQL virtuell dator  
Om den virtuella övervakningsdatorn finns i samma virtuella nätverk som dina virtuella SQL-datorresurser kan du gå till Anslut till [SQL Server i ett virtuellt nätverk](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/ways-to-connect-to-sql#connect-to-sql-server-within-a-virtual-network). Om den virtuella övervakningsdatorn kommer att finnas i ett annat virtuellt nätverk än dina virtuella SQL-datorresurser kan du gå till [Anslut till SQL Server via Internet.](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/ways-to-connect-to-sql#connect-to-sql-server-over-the-internet)

## <a name="store-monitoring-password-in-key-vault"></a>Lagra övervakningslösenordet i Key Vault
Du bör lagra dina lösenord för SQL-användaranslutning i en Key Vault i stället för att ange dem direkt i anslutningssträngarna för övervakningsprofilen.

När du har inställningar för din profil för SQL-övervakning behöver du någon av följande behörigheter för den Key Vault som du tänker använda:

- Microsoft.Authorization/roleAssignments/write 
- Microsoft.Authorization/roleAssignments/delete-behörigheter som administratör för användaråtkomst eller ägare 

En ny åtkomstprincip skapas automatiskt när du skapar din SQL Monitoring som använder den Key Vault du har angett. Använd *Tillåt åtkomst från Alla nätverk för* Key Vault nätverksinställningar.


## <a name="create-sql-monitoring-profile"></a>Skapa sql-övervakningsprofil
Öppna SQL-insikter genom **att välja SQL (förhandsversion)** **i** avsnittet Insikter **Azure Monitor** på menyn Azure Portal. Klicka **på Skapa ny profil.** 

:::image type="content" source="media/sql-insights-enable/create-new-profile.png" alt-text="Skapa en ny profil." lightbox="media/sql-insights-enable/create-new-profile.png":::

Profilen lagrar den information som du vill samla in från dina SQL-system.  Den har specifika inställningar för: 

- Azure SQL Database 
- Azure SQL Managed Instance 
- SQL Server körs på virtuella datorer  

Du kan till exempel skapa en profil med namnet *SQL Production* och en annan med namnet *SQL Staging* med olika inställningar för insamlingsfrekvens, vilka data som ska samlas in och vilken arbetsyta som data ska skickas till. 

Profilen lagras som en [regelresurs för datainsamling](../agents/data-collection-rule-overview.md) i den prenumeration och resursgrupp som du väljer. Varje profil behöver följande:

- Namn. Det går inte att redigera när den har skapats.
- Plats. Det här är en Azure-region.
- Log Analytics-arbetsytan för att lagra övervakningsdata.
- Samlingsinställningar för frekvensen och typen av sql-övervakningsdata som ska samlas in.

> [!NOTE]
> Profilens plats ska finnas på samma plats som Log Analytics-arbetsytan som du planerar att skicka övervakningsdata till.


:::image type="content" source="media/sql-insights-enable/profile-details.png" alt-text="Profilinformation." lightbox="media/sql-insights-enable/profile-details.png":::

Klicka **på Skapa övervakningsprofil** när du har angett informationen för din övervakningsprofil. Det kan ta upp till en minut för profilen att distribueras.  Om du inte ser den nya  profilen i kombinationsrutan Övervakningsprofil klickar du på uppdateringsknappen så visas den när distributionen är klar.  När du har valt den nya profilen väljer du fliken **Hantera** profil för att lägga till en övervakningsdator som ska associeras med profilen.

### <a name="add-monitoring-machine"></a>Lägga till övervakningsdator
Välj **Lägg till övervakningsdator** för att öppna en kontextpanel för att välja den virtuella dator som ska konfigureras för att övervaka dina SQL-instanser och ange anslutningssträngarna.

Välj prenumerationen och namnet på den virtuella övervakningsdatorn. Om du använder Key Vault för att lagra lösenordet för övervakningsanvändaren väljer du Key Vault-resurser med dessa hemligheter och anger den URL och det hemliga namn som ska användas i anslutningssträngarna. I nästa avsnitt finns information om hur du identifierar anslutningssträngen för olika SQL-distributioner.


:::image type="content" source="media/sql-insights-enable/add-monitoring-machine.png" alt-text="Lägg till övervakningsdator." lightbox="media/sql-insights-enable/add-monitoring-machine.png":::


### <a name="add-connection-strings"></a>Lägga till anslutningssträngar 
Anslutningssträngen anger det användarnamn som SQL Insights ska använda när du loggar in på SQL för att köra dynamiska hanteringsvyer. Om du använder en Key Vault för att lagra lösenordet för övervakningsanvändaren anger du URL:en och namnet på den hemlighet som ska användas. 

Anslutningssträngen varierar för varje typ av SQL-resurs:

#### <a name="azure-sql-databases"></a>Azure SQL Databases 
Ange anslutningssträngen i formuläret:

```
sqlAzureConnections": [ 
   "Server=mysqlserver.database.windows.net;Port=1433;Database=mydatabase;User Id=$username;Password=$password;" 
}
```

Hämta information från **menyalternativet Anslutningssträngar** för databasen.

:::image type="content" source="media/sql-insights-enable/connection-string-sql-database.png" alt-text="Anslutningssträng för SQL-databas" lightbox="media/sql-insights-enable/connection-string-sql-database.png":::

Om du vill övervaka en läsbar sekundär sträng inkluderar du nyckelvärdet `ApplicationIntent=ReadOnly` i anslutningssträngen. SQL Insights stöder övervakning av en enda sekundär instans. Insamlade data taggas för att återspegla primära eller sekundära data. 


#### <a name="azure-virtual-machines-running-sql-server"></a>Virtuella Azure-datorer som kör SQL Server 
Ange anslutningssträngen i formuläret:

```
"sqlVmConnections": [ 
   "Server=MyServerIPAddress;Port=1433;User Id=$username;Password=$password;" 
] 
```

Om den virtuella övervakningsdatorn finns i samma virtuella nätverk använder du serverns privata IP-adress.  Annars använder du den offentliga IP-adressen. Om du använder en Azure SQL virtuell dator kan du se vilken port som ska användas här **på sidan** Säkerhet för resursen.

:::image type="content" source="media/sql-insights-enable/sql-vm-security.png" alt-text="Säkerhet för virtuella SQL-datorer" lightbox="media/sql-insights-enable/sql-vm-security.png":::


### <a name="azure-sql-managed-instances"></a>Azure SQL Managed Instance 
Ange anslutningssträngen i formuläret:

```
"sqlManagedInstanceConnections": [ 
      "Server= mysqlserver.database.windows.net;Port=1433;User Id=$username;Password=$password;", 
    ] 
```
Hämta information från **menyalternativet Anslutningssträngar** för den hanterade instansen.


:::image type="content" source="media/sql-insights-enable/connection-string-sql-managed-instance.png" alt-text="SQL Managed Instance anslutningssträng" lightbox="media/sql-insights-enable/connection-string-sql-managed-instance.png":::

Om du vill övervaka en läsbar sekundär sträng inkluderar du nyckelvärdet `ApplicationIntent=ReadOnly` i anslutningssträngen. SQL Insights stöder övervakning av en enda sekundär och insamlade data taggas för att återspegla primära eller sekundära. 


## <a name="monitoring-profile-created"></a>Övervakningsprofilen har skapats 

Välj **Lägg till virtuell övervakningsdator** för att konfigurera den virtuella datorn så att den samlar in data från dina SQL-resurser. Gå inte tillbaka till **fliken** Översikt.  Om några minuter bör statuskolumnen ändras till "Samlar in". Du bör se data för de SQL-resurser som du har valt att övervaka.

Om du inte ser några data kan du gå till [Felsöka SQL-insikter](sql-insights-troubleshoot.md) för att identifiera problemet. 

:::image type="content" source="media/sql-insights-enable/profile-created.png" alt-text="Profilen har skapats" lightbox="media/sql-insights-enable/profile-created.png":::

> [!NOTE]
> Om du behöver uppdatera din övervakningsprofil eller anslutningssträngarna på de virtuella datorerna för övervakning kan du göra det via fliken Hantera **profil för SQL-insikter.**  När dina uppdateringar har sparats tillämpas ändringarna inom cirka 5 minuter.

## <a name="next-steps"></a>Nästa steg

- Se [Felsöka SQL-insikter](sql-insights-troubleshoot.md) om SQL-insikter inte fungerar korrekt när de har aktiverats.
