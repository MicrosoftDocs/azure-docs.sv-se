---
title: Komma igång med temporala tabeller
description: Lär dig hur du kommer igång med att använda temporala tabeller i Azure SQL Database och Azure SQL-hanterad instans.
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: bonova
ms.author: bonova
ms.reviewer: sstein
ms.date: 06/26/2019
ms.openlocfilehash: ea037d12417c8fad9d80b77df69285ed2c8df31b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91618666"
---
# <a name="getting-started-with-temporal-tables-in-azure-sql-database-and-azure-sql-managed-instance"></a>Komma igång med temporala tabeller i Azure SQL Database och Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqldb-sqlmi](includes/appliesto-sqldb-sqlmi.md)]

Temporala tabeller är en programmerings funktion i Azure SQL Database och Azure SQL-hanterad instans som gör att du kan spåra och analysera den fullständiga historiken för ändringar i dina data, utan att behöva anpassad kod. Temporala tabeller förvarar data nära relaterade till tids sammanhang så att lagrade fakta kan tolkas som endast giltiga inom den angivna perioden. Den här egenskapen för temporala tabeller möjliggör effektiv tidsbaserad analys och hämtar insikter från data utvecklingen.

## <a name="temporal-scenario"></a>Temporalt scenario

Den här artikeln beskriver hur du använder temporala tabeller i ett program scenario. Anta att du vill spåra användar aktiviteter på en ny webbplats som har utvecklats från början eller på en befintlig webbplats som du vill utöka med användar aktivitets analys. I det här förenklade exemplet förutsätter vi att antalet besökta webb sidor under en tids period är en indikator som måste fångas och övervakas i webbplats databasen som finns på Azure SQL Database eller Azure SQL-hanterad instans. Målet med den historiska analysen av användar aktivitet är att hämta indata till omdesignering av webbplatser och ge besökarna bättre upplevelse.

Databas modellen för det här scenariot är mycket enkelt-användares aktivitets mått representeras av ett enda heltals fält, **PageVisited** och samlas in tillsammans med grundläggande information om användar profilen. För tidsbaserad analys skulle du dessutom ha en serie rader för varje användare, där varje rad representerar antalet sidor som en viss användare besökt inom en viss tids period.

![Schema](./media/temporal-tables/AzureTemporal1.png)

Lyckligt vis behöver du inte göra något arbete i din app för att underhålla den här aktivitets informationen. Med temporala tabeller är den här processen automatiserad, vilket ger dig fullständig flexibilitet under webbplatsens design och mer tid att fokusera på själva data analysen. Det enda du behöver göra är att se till att **WebSiteInfo** -tabellen är konfigurerad som [temporal system versions hantering](/sql/relational-databases/tables/temporal-tables#what-is-a-system-versioned-temporal-table). De exakta stegen för att använda temporala tabeller i det här scenariot beskrivs nedan.

## <a name="step-1-configure-tables-as-temporal"></a>Steg 1: Konfigurera tabeller som temporala

Du kan antingen skapa temporala tabeller eller ändra befintliga genom att lägga till temporala attribut, beroende på om du startar ny utveckling eller uppgraderar befintliga program. I generella fall kan ditt scenario vara en blandning av dessa två alternativ. Utför dessa åtgärder med hjälp av [SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms) (SSMS), [SQL Server Data Tools](/sql/ssdt/download-sql-server-data-tools-ssdt) (SSDT), [Azure Data Studio](/sql/azure-data-studio/download-azure-data-studio)eller något annat verktyg för Transact-SQL-utveckling.

> [!IMPORTANT]
> Vi rekommenderar att du alltid använder den senaste versionen av Management Studio att vara synkroniserad med uppdateringar till Azure SQL Database och Azure SQL-hanterad instans. [Uppdatera SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

### <a name="create-new-table"></a>Skapa ny tabell

Använd snabb meny alternativet "ny System-Versioned tabell" i SSMS Object Explorer för att öppna Frågeredigeraren med ett skript för en temporal tabell mal len och Använd sedan "Ange värden för mallparametrar" (CTRL + SHIFT + M) för att fylla i mallen:

![SSMSNewTable](./media/temporal-tables/AzureTemporal2.png)

I SSDT väljer du mallen "temporal tabell (system versioned)" när du lägger till nya objekt i databas projektet. Detta öppnar Table Designer och gör att du enkelt kan ange tabellayout:

![SSDTNewTable](./media/temporal-tables/AzureTemporal3.png)

Du kan också skapa en temporal tabell genom att ange Transact-SQL-uttryck direkt, som du ser i exemplet nedan. Observera att de obligatoriska elementen för varje temporal tabell är PERIOD definitionen och SYSTEM_VERSIONING-satsen med en referens till en annan användar tabell som ska lagra historiska rad versioner:

```sql
CREATE TABLE WebsiteUserInfo
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
```

När du skapar den temporala system versions tabellen skapas automatiskt den tillhör ande historik tabellen med standard konfigurationen. Standard historik tabellen innehåller ett grupperat B-träd-index för period kolumnerna (End, start) med sid komprimering aktive rad. Den här konfigurationen är optimal för de flesta scenarier där temporala tabeller används, särskilt för [data granskning](/sql/relational-databases/tables/temporal-table-usage-scenarios#enabling-system-versioning-on-a-new-table-for-data-audit).

I det här fallet syftar vi på att utföra tidsbaserad trend analys över en längre data historik och med större data uppsättningar, så att lagrings alternativet för historik tabellen är ett grupperat columnstore-index. Ett grupperat columnstore ger mycket bra komprimering och prestanda för analytiska frågor. Temporala tabeller ger dig flexibiliteten att konfigurera index i aktuella och temporala tabeller helt oberoende av varandra.

> [!NOTE]
> Columnstore-index finns på Affärskritisk-, Generell användning-och Premium-nivåerna och på standard nivån, S3 och högre.

Följande skript visar hur standard indexet i historik tabellen kan ändras till det grupperade columnstore-klustret:

```sql
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
```

Temporala tabeller representeras i Object Explorer med den speciella ikonen för enklare identifiering, medan historik tabellen visas som en underordnad nod.

![Det går att omvara](./media/temporal-tables/AzureTemporal4.png)

### <a name="alter-existing-table-to-temporal"></a>Ändra befintlig tabell till temporal

Vi ska gå igenom det alternativa scenariot där WebsiteUserInfo-tabellen redan finns, men inte har utformats för att bevara ändringar i historiken. I det här fallet kan du helt enkelt utöka den befintliga tabellen för att bli temporal, som du ser i följande exempel:

```sql
ALTER TABLE WebsiteUserInfo
ADD
    ValidFrom datetime2 (0) GENERATED ALWAYS AS ROW START HIDDEN  
        constraint DF_ValidFrom DEFAULT DATEADD(SECOND, -1, SYSUTCDATETIME())
    , ValidTo datetime2 (0)  GENERATED ALWAYS AS ROW END HIDDEN
        constraint DF_ValidTo DEFAULT '9999.12.31 23:59:59.99'
    , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo);

ALTER TABLE WebsiteUserInfo  
SET (SYSTEM_VERSIONING = ON (HISTORY_TABLE = dbo.WebsiteUserInfoHistory));
GO

CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory
ON dbo.WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
```

## <a name="step-2-run-your-workload-regularly"></a>Steg 2: kör din arbets belastning regelbundet

Den största fördelen med temporala tabeller är att du inte behöver ändra eller justera din webbplats på något sätt för att utföra ändrings spårning. När du har skapat en temporal tabell behålls tidigare rad versioner transparent varje gång du gör ändringar i dina data.

För att kunna utnyttja automatisk ändrings spårning för det här scenariot, ska vi bara uppdatera kolumnen **PagesVisited** varje gång en användare avslutar sin session på webbplatsen:

```sql
UPDATE WebsiteUserInfo  SET [PagesVisited] = 5
WHERE [UserID] = 1;
```

Det är viktigt att Observera att uppdaterings frågan inte behöver känna till exakt den tidpunkt då den faktiska åtgärden ägde rum eller hur historiska data bevaras för framtida analyser. Båda aspekterna hanteras automatiskt av Azure SQL Database och Azure SQL-hanterad instans. Följande diagram illustrerar hur historik data genereras vid varje uppdatering.

![TemporalArchitecture](./media/temporal-tables/AzureTemporal5.png)

## <a name="step-3-perform-historical-data-analysis"></a>Steg 3: utför historisk data analys

Nu när temporal system versions hantering är aktive rad är historisk data analys bara en fråga från dig. I den här artikeln innehåller vi några exempel på hur du kan åtgärda vanliga analys scenarier – om du vill lära dig mer om den här artikeln kan du utforska olika alternativ som introduceras med [FOR SYSTEM_TIME](/sql/relational-databases/tables/temporal-tables#how-do-i-query-temporal-data) -satsen.

Kör den här frågan för att se de 10 främsta användare som beställts av antalet besökta webb sidor per timme sedan:

```sql
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
SELECT TOP 10 * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME AS OF @hourAgo
ORDER BY PagesVisited DESC
```

Du kan enkelt ändra den här frågan om du vill analysera plats besöken på en dag sedan, en månad sedan eller var som helst efter att du önskar.

Använd följande exempel för att utföra grundläggande statistiska analyser för föregående dag:

```sql
DECLARE @twoDaysAgo datetime2 = DATEADD(DAY, -2, SYSUTCDATETIME());
DECLARE @aDayAgo datetime2 = DATEADD(DAY, -1, SYSUTCDATETIME());

SELECT UserID, SUM (PagesVisited) as TotalVisitedPages, AVG (PagesVisited) as AverageVisitedPages,
MAX (PagesVisited) AS MaxVisitedPages, MIN (PagesVisited) AS MinVisitedPages,
STDEV (PagesVisited) as StDevViistedPages
FROM dbo.WebsiteUserInfo
FOR SYSTEM_TIME BETWEEN @twoDaysAgo AND @aDayAgo
GROUP BY UserId
```

Om du vill söka efter aktiviteter för en viss användare inom en viss tids period använder du satsen ingår i:

```sql
DECLARE @hourAgo datetime2 = DATEADD(HOUR, -1, SYSUTCDATETIME());
DECLARE @twoHoursAgo datetime2 = DATEADD(HOUR, -2, SYSUTCDATETIME());
SELECT * FROM dbo.WebsiteUserInfo
FOR SYSTEM_TIME CONTAINED IN (@twoHoursAgo, @hourAgo)
WHERE [UserID] = 1;
```

Grafisk visualisering är särskilt praktisk för temporala frågor eftersom du kan visa trender och användnings mönster på ett intuitivt sätt enkelt:

![TemporalGraph](./media/temporal-tables/AzureTemporal6.png)

## <a name="evolving-table-schema"></a>Schema för utveckling av tabell

Normalt måste du ändra det temporala tabell schemat medan du gör en app-utveckling. För det här kan du bara köra vanliga ALTER TABLE-instruktioner och Azure SQL Database eller Azure SQL-hanterad instans för att på lämpligt sätt sprida ändringar i historik tabellen. Följande skript visar hur du kan lägga till ytterligare attribut för spårning:

```sql
/*Add new column for tracking source IP address*/
ALTER TABLE dbo.WebsiteUserInfo
ADD  [IPAddress] varchar(128) NOT NULL CONSTRAINT DF_Address DEFAULT 'N/A';
```

På samma sätt kan du ändra kolumn definitionen medan din arbets belastning är aktiv:

```sql
/*Increase the length of name column*/
ALTER TABLE dbo.WebsiteUserInfo
    ALTER COLUMN  UserName nvarchar(256) NOT NULL;
```

Slutligen kan du ta bort en kolumn som du inte behöver längre.

```sql
/*Drop unnecessary column */
ALTER TABLE dbo.WebsiteUserInfo
    DROP COLUMN TemporaryColumn;
```

Du kan också använda de senaste [SSDT](/sql/ssdt/download-sql-server-data-tools-ssdt) för att ändra temporal Table-schemat när du är ansluten till databasen (online-läge) eller som en del av databas projektet (offline-läge).

## <a name="controlling-retention-of-historical-data"></a>Kontrol lera kvarhållning av historiska data

Med system versions temporala tabeller kan historik tabellen öka databas storleken mer än vanliga tabeller. En stor och ständigt växande historik tabell kan bli ett problem både på grund av rena lagrings kostnader och för att införa en prestanda skatt på temporala frågor. Därför är det viktigt att planera och hantera livs cykeln för varje temporal tabell genom att utveckla en data lagrings princip för att hantera data i historik tabellen. Med Azure SQL Database och Azure SQL-hanterad instans har du följande metoder för att hantera historiska data i den temporala tabellen:

- [Tabellpartitionering](/sql/relational-databases/tables/manage-retention-of-historical-data-in-system-versioned-temporal-tables#using-table-partitioning-approach)
- [Anpassat rensnings skript](/sql/relational-databases/tables/manage-retention-of-historical-data-in-system-versioned-temporal-tables#using-custom-cleanup-script-approach)

## <a name="next-steps"></a>Nästa steg

- Mer information om temporala tabeller finns i checka ut [temporala tabeller](/sql/relational-databases/tables/temporal-tables).
- Besök Channel 9 om du vill höra en [lyckad kund för klar ande Support artikel](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) och titta på en [levande temporal demonstration](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).
