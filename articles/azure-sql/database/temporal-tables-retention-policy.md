---
title: Hantera historiska data i temporala tabeller
description: Lär dig hur du använder temporal bevarande princip för att behålla historiska data under din kontroll.
services: sql-database
ms.service: sql-db-mi
ms.subservice: development
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: how-to
author: bonova
ms.author: bonova
ms.reviewer: sstein
ms.date: 09/25/2018
ms.openlocfilehash: 1d68163a9fba3ba3bcd4c0c0f3fb5f442296e781
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91619397"
---
# <a name="manage-historical-data-in-temporal-tables-with-retention-policy"></a>Hantera historiska data i temporala tabeller med bevarande princip
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Temporala tabeller kan öka databas storleken mer än vanliga tabeller, särskilt om du behåller historiska data under en längre tids period. Bevarande princip för historiska data är därför en viktig aspekt av att planera och hantera livs cykeln för varje temporal tabell. Temporala tabeller i Azure SQL Database och Azure SQL-hanterad instans levereras med en lättanvänd mekanism för kvarhållning som hjälper dig att utföra den här uppgiften.

Kvarhållning av temporal historik kan konfigureras på den enskilda tabell nivån, vilket gör att användarna kan skapa flexibla ålders principer. Det är enkelt att använda temporal kvarhållning: det kräver bara att en parameter anges vid skapande av tabell eller schema ändring.

När du har definierat bevarande princip börjar Azure SQL Database och Azure SQL Managed instance kontrol lera regelbundet om det finns historiska rader som är tillgängliga för automatisk data rensning. Identifiering av matchande rader och borttagning av dem från historik tabellen sker transparent i bakgrunds aktiviteten som schemaläggs och körs av systemet. Ålders villkor för rader i historik tabellen kontrol leras baserat på kolumnen som representerar slutet av SYSTEM_TIMEs perioden. Om kvarhållningsperioden exempelvis är inställt på sex månader, uppfyller tabell rader som är berättigade till rensning följande villkor:

```sql
ValidTo < DATEADD (MONTH, -6, SYSUTCDATETIME())
```

I föregående exempel förutsättde vi att kolumnen **ValidTo** motsvarar slutet av SYSTEM_TIMEs perioden.

## <a name="how-to-configure-retention-policy"></a>Konfigurera bevarande princip

Innan du konfigurerar en bevarande princip för en temporal tabell kontrollerar du först om temporal historisk kvarhållning är aktiverat *på databas nivå*.

```sql
SELECT is_temporal_history_retention_enabled, name
FROM sys.databases
```

Databas flagga **is_temporal_history_retention_enabled** anges som standard, men användare kan ändra den med Alter Database-instruktionen. Den anges också automatiskt till av efter [återställnings](recovery-using-backups.md) åtgärden vid tidpunkten. Om du vill aktivera rensning av temporala historik för databasen kör du följande instruktion:

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

> [!IMPORTANT]
> Du kan konfigurera kvarhållning för temporala tabeller även om **is_temporal_history_retention_enabled** är avstängd, men automatisk rensning för inaktuella rader inte utlöses i detta fall.

Bevarande principen konfigureras när tabellen skapas genom att ange ett värde för parametern HISTORY_RETENTION_PERIOD:

```sql
CREATE TABLE dbo.WebsiteUserInfo
(  
    [UserID] int NOT NULL PRIMARY KEY CLUSTERED
  , [UserName] nvarchar(100) NOT NULL
  , [PagesVisited] int NOT NULL
  , [ValidFrom] datetime2 (0) GENERATED ALWAYS AS ROW START
  , [ValidTo] datetime2 (0) GENERATED ALWAYS AS ROW END
  , PERIOD FOR SYSTEM_TIME (ValidFrom, ValidTo)
 )  
 WITH
 (
     SYSTEM_VERSIONING = ON
     (
        HISTORY_TABLE = dbo.WebsiteUserInfoHistory,
        HISTORY_RETENTION_PERIOD = 6 MONTHS
     )
 );
```

Med Azure SQL Database och Azure SQL-hanterad instans kan du ange kvarhållningsperiod genom att använda olika tidsenheter: dagar, veckor, månader och år. Om HISTORY_RETENTION_PERIOD utelämnas förutsätts oändlig kvarhållning. Du kan också använda OÄNDLIGt nyckelord explicit.

I vissa fall kanske du vill konfigurera kvarhållning när tabellen har skapats, eller ändra det tidigare konfigurerade värdet. I så fall använder du ALTER TABLE-instruktionen:

```sql
ALTER TABLE dbo.WebsiteUserInfo
SET (SYSTEM_VERSIONING = ON (HISTORY_RETENTION_PERIOD = 9 MONTHS));
```

> [!IMPORTANT]
> Om du anger SYSTEM_VERSIONING till av *bevaras inte* värdet för kvarhållning av period. Om du anger SYSTEM_VERSIONING till på utan HISTORY_RETENTION_PERIOD anges explicit i den oändliga kvarhållningsperioden.

Om du vill granska aktuell status för bevarande principen använder du följande fråga som ansluter flaggan temporal kvarhållning av aktivering på databas nivå med kvarhållningsperiod för enskilda tabeller:

```sql
SELECT DB.is_temporal_history_retention_enabled,
SCHEMA_NAME(T1.schema_id) AS TemporalTableSchema,
T1.name as TemporalTableName,  SCHEMA_NAME(T2.schema_id) AS HistoryTableSchema,
T2.name as HistoryTableName,T1.history_retention_period,
T1.history_retention_period_unit_desc
FROM sys.tables T1  
OUTER APPLY (select is_temporal_history_retention_enabled from sys.databases
where name = DB_NAME()) AS DB
LEFT JOIN sys.tables T2
ON T1.history_table_id = T2.object_id WHERE T1.temporal_type = 2
```

## <a name="how-ages-rows-are-deleted"></a>Hur åldras rader tas bort

Rensnings processen beror på index-layouten för historik tabellen. Det är viktigt att Observera att *endast historik tabeller med ett grupperat index (B-Tree eller columnstore) kan ha en begränsad bevarande princip konfigurerad*. En bakgrunds aktivitet skapas för att utföra föråldrade data rensningar för alla temporala tabeller med begränsad kvarhållningsperiod.
Rensnings logik för det grupperade indexet rowstore (B-Tree) tar bort föråldrad rad i mindre segment (upp till 10 000) minimerar trycket på databas loggen och i/o-undersystemet. Även om rensnings logik använder det obligatoriska B-träd indexet, kan ordningen på borttagningarna för de rader som är äldre än kvarhållningsperioden inte garanteras ordentligt. Ta därför *inget beroende på rensnings ordningen i dina program*.

Rensnings aktiviteten för grupperad columnstore tar bort hela [rad grupper](/sql/relational-databases/indexes/columnstore-indexes-overview) samtidigt (innehåller vanligt vis 1 000 000 rader), vilket är mycket effektivt, särskilt när historiska data genereras i hög takt.

![Kvarhållning av grupperat columnstore-kluster](./media/temporal-tables-retention-policy/cciretention.png)

Med utmärkt data komprimering och effektiv kvarhållning av rensning görs ett grupperat columnstore-index ett perfekt val för scenarier när arbets belastningen snabbt genererar hög mängd historiska data. Det mönstret är typiskt för intensiva [arbets belastningar med transaktions bearbetning som använder temporala tabeller](/sql/relational-databases/tables/temporal-table-usage-scenarios) för ändrings spårning och granskning, trend analys eller IoT-datainmatning.

## <a name="index-considerations"></a>Index överväganden

Rensnings aktiviteten för tabeller med rowstore-grupperat index kräver index att börja med kolumnen som motsvarar slutet av SYSTEM_TIMEs perioden. Om detta index inte finns kan du inte konfigurera en begränsad kvarhållningsperiod:

*MSG 13765, nivå 16, tillstånd 1 <br> </br> inställning av begränsad kvarhållningsperiod misslyckades för den temporala system versions tabellen ' temporalstagetestdb. dbo. WebsiteUserInfo ' eftersom historik tabellen ' temporalstagetestdb. dbo. WebsiteUserInfoHistory ' inte innehåller det nödvändiga grupperade indexet. Överväg att skapa ett grupperat columnstore-eller B-träd-index som börjar med kolumnen som matchar slutet av SYSTEM_TIMEs perioden, i historik tabellen.*

Det är viktigt att Observera att standard historik tabellen som skapats av Azure SQL Database och Azure SQL-hanterad instans redan har grupperat index, vilket är kompatibelt med bevarande principen. Om du försöker ta bort indexet i en tabell med begränsad kvarhållningsperioden, Miss lyckas åtgärden med följande fel:

*MSG 13766, nivå 16, tillstånd 1 <br> </br> kan inte släppa det grupperade indexet WebsiteUserInfoHistory.IX_WebsiteUserInfoHistory eftersom det används för automatisk rensning av föråldrade data. Överväg att ange HISTORY_RETENTION_PERIOD oändlig i motsvarande system versions temporal tabell om du behöver släppa det här indexet.*

Rensning av det grupperade columnstore-indexet fungerar optimalt om historiska rader infogas i stigande ordning (sorteras efter periodens slut), vilket alltid är fallet när historik tabellen fylls exklusivt av SYSTEM_VERSIONIOING mekanismen. Om rader i historik tabellen inte sorteras efter periodens slut punkt (som kan vara fallet om du migrerade befintliga historiska data) bör du återskapa ett grupperat columnstore-index över B-Tree rowstore-indexet som är korrekt beställt för att uppnå optimala prestanda.

Undvik att återskapa det grupperade columnstore-indexet i historik tabellen med den begränsade kvarhållningsperioden, eftersom det kan ändra ordning i de rad grupper som används naturligt av system versions åtgärden. Om du behöver återskapa ett grupperat columnstore-index i historik tabellen, gör du det genom att återskapa det ovanpå det kompatibla B-träd indexet och bevara ordningen i högkvalitativa som krävs för vanlig data rensning. Samma metod bör vidtas om du skapar en temporal tabell med en befintlig historik tabell som har grupperat kolumn index utan garanterad data ordning:

```sql
/*Create B-tree ordered by the end of period column*/
CREATE CLUSTERED INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory (ValidTo)
WITH (DROP_EXISTING = ON);
GO
/*Re-create clustered columnstore index*/
CREATE CLUSTERED COLUMNSTORE INDEX IX_WebsiteUserInfoHistory ON WebsiteUserInfoHistory
WITH (DROP_EXISTING = ON);
```

När en begränsad kvarhållningsperiod har kon figurer ATS för historik tabellen med det grupperade columnstore-indexet kan du inte skapa ytterligare icke-grupperade B-träd index i tabellen:

```sql
CREATE NONCLUSTERED INDEX IX_WebHistNCI ON WebsiteUserInfoHistory ([UserName])
```

Ett försök att köra ovanstående instruktionen Miss lyckas med följande fel:

*MSG 13772, Level 16, State 1 <br> </br> kan inte skapa icke-grupperat index för en temporal historik tabell ' WebsiteUserInfoHistory ' eftersom den har en begränsad kvarhållningsperiod och ett grupperat columnstore-index har definierats.*

## <a name="querying-tables-with-retention-policy"></a>Frågar tabeller med bevarande princip

Alla frågor i den temporala tabellen filtrerar automatiskt bort historiska rader som matchar den begränsade bevarande principen, för att undvika oförutsägbara och inkonsekventa resultat, eftersom gamla rader kan tas bort av rensnings aktiviteten, *vid en viss tidpunkt och i valfri ordning*.

Följande bild visar frågeplan för en enkel fråga:

```sql
SELECT * FROM dbo.WebsiteUserInfo FOR SYSTEM_TIME ALL;
```

Frågeplan inkluderar ytterligare filter som tillämpas på periodens slut kolumn (ValidTo) i den klustrade index skannings operatorn i historik tabellen (markerad). I det här exemplet förutsätts att en kvarhållningsperiod för en månad hade angetts i WebsiteUserInfo-tabellen.

![Frågefilter för kvarhållning](./media/temporal-tables-retention-policy/queryexecplanwithretention.png)

Om du däremot söker i historik tabellen direkt kan du se rader som är äldre än den angivna kvarhållningsperioden, men utan någon garanti för upprepnings bara frågeresultat. Följande bild visar körnings planen för frågan i historik tabellen utan att fler filter tillämpas:

![Fråga efter historik utan kvarhållning filter](./media/temporal-tables-retention-policy/queryexecplanhistorytable.png)

Förlita dig inte på affärs logiken vid läsning av historik tabellen bortom kvarhållningsperioden eftersom du kan få inkonsekventa eller oväntade resultat. Vi rekommenderar att du använder temporala frågor med SYSTEM_TIME-satsen för att analysera data i temporala tabeller.

## <a name="point-in-time-restore-considerations"></a>Överväganden vid återställning av tidpunkt

När du skapar en ny databas genom [att återställa den befintliga databasen till en viss tidpunkt](recovery-using-backups.md)har den temporal kvarhållning inaktive rad på databas nivå. (**is_temporal_history_retention_enabled** flagga inställt på av). Med den här funktionen kan du undersöka alla historiska rader vid återställning, utan att oroa dig för att gamla rader tas bort innan du börjar fråga dem. Du kan använda den för att *kontrol lera historiska data efter den konfigurerade kvarhållningsperioden*.

Anta att en temporal tabell har en angiven MÅNADs kvarhållningsperiod. Om databasen har skapats i Premium-tjänstnivå skulle du kunna skapa en databas kopia med databasens tillstånd upp till 35 dagar tillbaka tidigare. På så sätt kan du analysera historiska rader som är upp till 65 dagar gamla genom att fråga historik tabellen direkt.

Om du vill aktivera rensning av temporal kvarhållning kör du följande Transact-SQL-uttryck efter tidpunkten för återställning:

```sql
ALTER DATABASE <myDB>
SET TEMPORAL_HISTORY_RETENTION  ON
```

## <a name="next-steps"></a>Nästa steg

Om du vill lära dig hur du använder temporala tabeller i dina program kan du läsa [komma igång med temporala tabeller](../temporal-tables.md).

Besök Channel 9 om du vill höra en [lyckad kund för klar ande Support artikel](https://channel9.msdn.com/Blogs/jsturtevant/Azure-SQL-Temporal-Tables-with-RockStep-Solutions) och titta på en [levande temporal demonstration](https://channel9.msdn.com/Shows/Data-Exposed/Temporal-in-SQL-Server-2016).

För detaljerad information om temporala tabeller, granska [temporala tabeller](/sql/relational-databases/tables/temporal-tables).
