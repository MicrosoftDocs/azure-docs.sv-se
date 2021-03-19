---
title: Aktivera SQL Insights
description: Aktivera SQL insikter i Azure Monitor
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 03/15/2021
ms.openlocfilehash: 5ab51fc4ea64dfd678f5c9acfc80b5e380782153
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104610143"
---
# <a name="enable-sql-insights-preview"></a>Aktivera SQL Insights (förhands granskning)
I den här artikeln beskrivs hur du aktiverar [SQL](sql-insights-overview.md) Insights för att övervaka dina SQL-distributioner. Övervakning utförs från en virtuell Azure-dator som ansluter till dina SQL-distributioner och använder Dynamic Management views (DMV: er) för att samla in övervaknings data. Du kan styra vilka data uppsättningar som samlas in och hur ofta samlingar används med hjälp av en övervaknings profil.

## <a name="create-log-analytics-workspace"></a>Skapa Log Analytics-arbetsyta
SQL Insights lagrar data i en eller flera [Log Analytics arbets ytor](../logs/data-platform-logs.md#log-analytics-workspaces).  Innan du kan aktivera SQL Insights måste du antingen [skapa en arbets yta](../logs/quick-create-workspace.md) eller välja en befintlig. En enda arbets yta kan användas med flera övervaknings profiler, men arbets ytan och profilerna måste finnas i samma Azure-region. Om du vill aktivera och komma åt funktionerna i SQL Insights måste du ha [rollen Log Analytics Contributor](../logs/manage-access.md) i arbets ytan. 

## <a name="create-monitoring-user"></a>Skapa övervaknings användare 
Du behöver en användare på de SQL-distributioner som du vill övervaka. Följ anvisningarna nedan för olika typer av SQL-distributioner.

### <a name="azure-sql-database"></a>Azure SQL-databas
Öppna Azure SQL Database med [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) eller [Frågeredigeraren (för hands version)](../../azure-sql/database/connect-query-portal.md) i Azure Portal.

Kör följande skript för att skapa en användare med de behörigheter som krävs. Ersätt *User* med ett användar namn och *mystrongpassword* med ett lösen ord.

```
CREATE USER [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW DATABASE STATE TO [user]; 
GO 
```

:::image type="content" source="media/sql-insights-enable/telegraf-user-database-script.png" alt-text="Skapa användar skript för teleympkvistar." lightbox="media/sql-insights-enable/telegraf-user-database-script.png":::

Verifiera att användaren har skapats.

:::image type="content" source="media/sql-insights-enable/telegraf-user-database-verify.png" alt-text="Verifiera användar skript för teleympkvistar." lightbox="media/sql-insights-enable/telegraf-user-database-verify.png":::

### <a name="azure-sql-managed-instance"></a>Azure SQL Managed Instance
Logga in på den hanterade Azure SQL-instansen och Använd [SSMS](../../azure-sql/database/connect-query-ssms.md) eller liknande verktyg för att köra följande skript för att skapa en övervaknings användare med de behörigheter som krävs. Ersätt *User* med ett användar namn och *mystrongpassword* med ett lösen ord.

 
```
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
Logga in på den virtuella Azure-datorn som kör SQL Server och Använd [SQL Server Management Studio](../../azure-sql/database/connect-query-ssms.md) eller liknande verktyg för att köra följande skript för att skapa en övervaknings användare med de behörigheter som krävs. Ersätt *User* med ett användar namn och *mystrongpassword* med ett lösen ord.

 
```
USE master; 
GO 
CREATE LOGIN [user] WITH PASSWORD = N'mystrongpassword'; 
GO 
GRANT VIEW SERVER STATE TO [user]; 
GO 
GRANT VIEW ANY DEFINITION TO [user]; 
GO
```

## <a name="create-azure-virtual-machine"></a>Skapa virtuell Azure-dator 
Du måste skapa en eller flera virtuella Azure-datorer som ska användas för att samla in data för att övervaka SQL.  

> [!NOTE]
>  [Övervaknings profilerna](#create-sql-monitoring-profile) anger vilka data som ska samlas in från de olika typerna av SQL som du vill övervaka. Det går bara att koppla en övervaknings profil till varje virtuell dator för övervakning. Om du behöver flera övervaknings profiler måste du skapa en virtuell dator för var och en.

### <a name="azure-virtual-machine-requirements"></a>Krav för virtuell Azure-dator
De virtuella Azure-datorerna har följande krav.

- Operativ system: Ubuntu 18,04 
- Rekommenderade storlekar för virtuella Azure-datorer: Standard_B2s (2 processorer, 4 GiB-minne) 
- Regioner som stöds: vilken [region som helst som stöds av Azure Monitor agenten](../agents/azure-monitor-agent-overview.md#supported-regions)

> [!NOTE]
> Storleken på den virtuella datorn med Standard_B2s (2 processorer, 4 GiB minne) stöder upp till 100 anslutnings strängar. Du bör inte allokera fler än 100 anslutningar till en enda virtuell dator.

De virtuella datorerna måste placeras i samma VNET som SQL-systemen så att de kan göra nätverks anslutningar för att samla in övervaknings data. Om du använder den virtuella datorn för övervakning för att övervaka SQL som körs på virtuella Azure-datorer eller som en Azure-hanterad instans, bör du överväga att placera den virtuella datorn i en program säkerhets grupp eller samma virtuella nätverk som resurserna så att du inte behöver ange en offentlig nätverks slut punkt för övervakning av SQL-servern. 

## <a name="configure-network-settings"></a>Konfigurera nätverksinställningar
Varje typ av SQL erbjuder metoder för övervakning av den virtuella datorn för säker åtkomst till SQL.  Avsnitten nedan beskriver alternativen baserat på typen av SQL.

### <a name="azure-sql-databases"></a>Azure SQL Databases  

[Självstudie – ansluta till en Azure SQL-Server med hjälp av en privat Azure-slutpunkt – Azure Portal](../../private-link/tutorial-private-endpoint-sql-portal.md) innehåller ett exempel på hur du konfigurerar en privat slut punkt som du kan använda för att få åtkomst till din databas.  Om du använder den här metoden måste du se till att de virtuella datorerna som övervakas finns i samma VNET och undernät som du kommer att använda för den privata slut punkten.  Du kan sedan skapa den privata slut punkten i databasen om du inte redan har gjort det. 

Om du använder en [brand Väggs inställning](../../azure-sql/database/firewall-configure.md) för att ge åtkomst till din SQL Database måste du lägga till en brand Väggs regel för att ge åtkomst från den offentliga IP-adressen för den virtuella övervakningen. Du kan komma åt brand Väggs inställningarna från sidan **Azure SQL Database översikt** i portalen. 

:::image type="content" source="media/sql-insights-enable/set-server-firewall.png" alt-text="Konfigurera serverns brandvägg" lightbox="media/sql-insights-enable/set-server-firewall.png":::

:::image type="content" source="media/sql-insights-enable/firewall-settings.png" alt-text="Brand Väggs inställningar." lightbox="media/sql-insights-enable/firewall-settings.png":::

### <a name="azure-sql-managed-instances"></a>Azure SQL Managed Instance 

Om den virtuella övervaknings datorn kommer att finnas i samma VNet som SQL-MI-resurserna kan du se [Anslut inuti samma VNet](https://docs.microsoft.com/azure/azure-sql/managed-instance/connect-application-instance#connect-inside-the-same-vnet). Om den virtuella övervaknings datorn kommer att finnas i ett annat virtuellt nätverk än SQL-MI-resurserna kan du se [Anslut i ett annat virtuellt nätverk](https://docs.microsoft.com/azure/azure-sql/managed-instance/connect-application-instance#connect-inside-a-different-vnet).


### <a name="azure-virtual-machine-and-azure-sql-virtual-machine"></a>Virtuell Azure-dator och virtuell Azure SQL-dator  
Om den virtuella övervaknings datorn finns i samma VNet som dina SQL-resurser för virtuella datorer kan du se [Anslut till SQL Server i ett virtuellt nätverk](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/ways-to-connect-to-sql#connect-to-sql-server-within-a-virtual-network). Om den virtuella övervaknings datorn kommer att finnas i det andra virtuella nätverket än dina virtuella SQL-datorer, kan  [du se Anslut till SQL Server via Internet](https://docs.microsoft.com/azure/azure-sql/virtual-machines/windows/ways-to-connect-to-sql#connect-to-sql-server-over-the-internet).

## <a name="store-monitoring-password-in-key-vault"></a>Lagra övervaknings lösen ord i Key Vault
Du bör lagra lösen ord för SQL-användare i en Key Vault i stället för att ange dem direkt i dina anslutnings strängar för övervaknings profilen.

När du konfigurerar profilen för SQL-övervakning behöver du någon av följande behörigheter för den Key Vault resurs som du vill använda:

- Microsoft.Authorization/roleAssignments/write 
- Microsoft. Authorization/roleAssignments/Delete-behörigheter som användar åtkomst administratör eller ägare 

En ny åtkomst princip skapas automatiskt som en del av att skapa din SQL Monitoring profil som använder den Key Vault som du har angett. Använd *Tillåt åtkomst från alla nätverk* för Key Vault nätverks inställningar.


## <a name="create-sql-monitoring-profile"></a>Skapa SQL-övervaknings profil
Öppna SQL Insights genom att välja **SQL (för hands version)** i avsnittet **insikter** på **Azure Monitor** -menyn i Azure Portal. Klicka på **Skapa ny profil**. 

:::image type="content" source="media/sql-insights-enable/create-new-profile.png" alt-text="Skapa en ny profil." lightbox="media/sql-insights-enable/create-new-profile.png":::

Profilen lagrar den information som du vill samla in från SQL-systemen.  Den har vissa inställningar för: 

- Azure SQL Database 
- Azure SQL Managed Instance 
- SQL Server körs på virtuella datorer  

Du kan till exempel skapa en profil med namnet *SQL-produktion* och en annan namngiven *SQL-mellanlagring* med olika inställningar för data insamling, vilka data som ska samlas in och vilken arbets yta som data ska skickas till. 

Profilen lagras som en [data insamlings regel](../agents/data-collection-rule-overview.md) resurs i den prenumeration och resurs grupp som du väljer. Varje profil behöver följande:

- Namn. Det går inte att redigera när du har skapat.
- Plats. Det här är en Azure-region.
- Log Analytics arbets ytan för att lagra övervaknings data.
- Samlings inställningar för den frekvens och typ av SQL-övervaknings data som ska samlas in.

> [!NOTE]
> Platsen för profilen ska finnas på samma plats som Log Analytics arbets ytan som du planerar att skicka övervaknings data till.


:::image type="content" source="media/sql-insights-enable/profile-details.png" alt-text="Profil information." lightbox="media/sql-insights-enable/profile-details.png":::

Klicka på **skapa övervaknings profil** när du har angett informationen för din övervaknings profil. Det kan ta upp till en minut innan profilen har distribuerats.  Om du inte ser den nya profilen som visas i kombinations rutan för **övervaknings profil** klickar du på knappen Uppdatera så visas den när distributionen har slutförts.  När du har valt den nya profilen väljer du fliken **Hantera profil** för att lägga till en övervaknings dator som ska associeras med profilen.

### <a name="add-monitoring-machine"></a>Lägg till övervaknings dator
Välj **Lägg till övervaknings dator** om du vill öppna en kontext panel och välja den virtuella datorn för att övervaka dina SQL-instanser och ange anslutnings strängarna.

Välj prenumeration och namn på den virtuella dator som ska övervakas. Om du använder Key Vault för att lagra ditt lösen ord för övervaknings användaren, väljer du Key Vault resurser med dessa hemligheter och anger URL: en och det hemliga namnet som ska användas i anslutnings strängarna. Se nästa avsnitt för information om hur du identifierar anslutnings strängen för olika SQL-distributioner.


:::image type="content" source="media/sql-insights-enable/add-monitoring-machine.png" alt-text="Lägg till övervaknings dator." lightbox="media/sql-insights-enable/add-monitoring-machine.png":::


### <a name="add-connection-strings"></a>Lägg till anslutnings strängar 
Anslutnings strängen anger det användar namn som SQL Insights ska använda vid inloggning i SQL för att köra vyer för dynamisk hantering. Om du använder en Key Vault för att lagra lösen ordet för din övervaknings användare anger du URL: en och namnet på hemligheten som ska användas. 

Anslutnings strängen varierar för varje typ av SQL-resurs:

#### <a name="azure-sql-databases"></a>Azure SQL Databases 
Ange anslutnings strängen i formatet:

```
sqlAzureConnections": [ 
   "Server=mysqlserver.database.windows.net;Port=1433;Database=mydatabase;User Id=$username;Password=$password;" 
}
```

Hämta information från meny alternativet **anslutnings strängar** för databasen.

:::image type="content" source="media/sql-insights-enable/connection-string-sql-database.png" alt-text="SQL Database-anslutningssträng" lightbox="media/sql-insights-enable/connection-string-sql-database.png":::

Om du vill övervaka en läsbar sekundär, inkluderar du nyckel värdet `ApplicationIntent=ReadOnly` i anslutnings strängen.


#### <a name="azure-virtual-machines-running-sql-server"></a>Virtuella Azure-datorer som kör SQL Server 
Ange anslutnings strängen i formatet:

```
"sqlVmConnections": [ 
   "Server=MyServerIPAddress;Port=1433;User Id=$username;Password=$password;" 
] 
```

Om den virtuella övervaknings datorn finns i samma VNET använder du serverns privata IP-adress.  Annars använder du den offentliga IP-adressen. Om du använder en virtuell Azure SQL-dator kan du se vilken port som ska användas här på sidan **säkerhet** för resursen.

:::image type="content" source="media/sql-insights-enable/sql-vm-security.png" alt-text="Säkerhet för virtuella SQL-datorer" lightbox="media/sql-insights-enable/sql-vm-security.png":::

Om du vill övervaka en läsbar sekundär, inkluderar du nyckel värdet `ApplicationIntent=ReadOnly` i anslutnings strängen.


### <a name="azure-sql-managed-instances"></a>Azure SQL Managed Instance 
Ange anslutnings strängen i formatet:

```
"sqlManagedInstanceConnections": [ 
      "Server= mysqlserver.database.windows.net;Port=1433;User Id=$username;Password=$password;", 
    ] 
```
Hämta information från meny alternativet **anslutnings strängar** för den hanterade instansen.


:::image type="content" source="media/sql-insights-enable/connection-string-sql-managed-instance.png" alt-text="SQL-hanterad instans anslutnings sträng" lightbox="media/sql-insights-enable/connection-string-sql-managed-instance.png":::

Om du vill övervaka en läsbar sekundär, inkluderar du nyckel värdet `ApplicationIntent=ReadOnly` i anslutnings strängen.



## <a name="profile-created"></a>Profilen har skapats 
Välj **Lägg till övervakning virtuell dator** om du vill konfigurera den virtuella datorn för att samla in data från SQL-distributionerna. Gå inte tillbaka till fliken **Översikt** .  Om några minuter ska kolumnen status ändras till Säg "insamling" bör du se data för de system som du har valt att övervaka.

Om du inte ser några data läser du [FELSÖKA SQL Insights](sql-insights-troubleshoot.md) för att identifiera problemet. 

:::image type="content" source="media/sql-insights-enable/profile-created.png" alt-text="Profilen har skapats" lightbox="media/sql-insights-enable/profile-created.png":::

## <a name="next-steps"></a>Nästa steg

- Se [FELSÖKA SQL Insights](sql-insights-troubleshoot.md) om SQL Insights inte fungerar som det ska när det har Aktiver ATS.
