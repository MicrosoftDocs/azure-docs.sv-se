---
title: Vägledning för prestanda justering för program och databaser
titleSuffix: Azure SQL Database & Azure SQL Managed Instance
description: Lär dig mer om att finjustera databas program och databaser för prestanda i Azure SQL Database och Azure SQL-hanterad instans.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 03/10/2020
ms.openlocfilehash: 40657ad2f3b69d62e0e0d9c7d9e0f0be7343547b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96490609"
---
# <a name="tune-applications-and-databases-for-performance-in-azure-sql-database-and-azure-sql-managed-instance"></a>Finjustera program och databaser för prestanda i Azure SQL Database och Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

När du har identifierat ett prestanda problem som du står inför Azure SQL Database och en hanterad Azure SQL-instans är den här artikeln utformad för att hjälpa dig att:

- Finjustera ditt program och tillämpa några metod tips som kan förbättra prestandan.
- Finjustera databasen genom att ändra index och frågor till mer effektivt arbete med data.

Den här artikeln förutsätter att du redan har arbetat med [rekommendationerna i Azure SQL Database Database Advisor](database-advisor-implement-performance-recommendations.md) och Azure SQL Database [Automatisk justering](automatic-tuning-overview.md), om tillämpligt. Det förutsätter också att du har granskat [en översikt över övervakning och justering](monitor-tune-overview.md) och dess relaterade artiklar som rör fel sökning av prestanda problem. Den här artikeln förutsätter att du inte har några processor resurser, ett prestanda problem som kan lösas genom att öka beräknings storleken eller tjänst nivån för att ge fler resurser till din databas.

## <a name="tune-your-application"></a>Finjustera ditt program

I traditionella lokala SQL Server separeras processen för inledande kapacitets planering ofta från processen för att köra ett program i produktion. Maskin-och produkt licenser köps först och prestanda justering görs efteråt. När du använder Azure SQL är det en bra idé att försätta processen att köra ett program och justera det. Med modellen för att betala för kapacitet på begäran kan du anpassa ditt program till att använda de lägsta resurserna som behövs nu, i stället för att få mer information om maskin vara baserat på gissningar på framtida tillväxt planer för ett program, vilket ofta är felaktigt. Vissa kunder kan välja att inte justera ett program och i stället välja att överetablera maskin varu resurser. Den här metoden kan vara en bra idé om du inte vill ändra ett nyckel program under en upptagen period. Men att justera ett program kan minimera resurs kraven och minska månads räkningar när du använder tjänst nivåerna i Azure SQL Database och Azure SQL-hanterad instans.

### <a name="application-characteristics"></a>Program egenskaper

Även om Azure SQL Database-och Azure SQL-hanterade instans tjänst nivåerna är utformade för att förbättra prestanda stabilitet och förutsägbarhet för ett program, kan vissa metod tips hjälpa dig att finjustera ditt program så att det blir lättare att utnyttja resurserna i en beräknings storlek. Även om många program har avsevärda prestanda vinster genom att växla till en högre beräknings storlek eller tjänst nivå, behöver vissa program ytterligare justering för att dra nytta av en högre tjänst nivå. Överväg ytterligare program justering för program som har följande egenskaper för bättre prestanda:

- **Program som har långsamma prestanda på grund av "chatty"-beteende**

  Samtals program gör stora data åtkomst åtgärder känsliga för nätverks fördröjning. Du kan behöva ändra dessa typer av program för att minska antalet data åtkomst åtgärder till-databasen. Du kan till exempel förbättra programmets prestanda genom att använda metoder som att gruppera ad hoc-frågor eller flytta frågor till lagrade procedurer. Mer information finns i [batch-frågor](#batch-queries).

- **Databaser med en intensiv arbets belastning som inte stöds av en hel enskild dator**

   Databaser som överskrider den högsta Premium Compute-storleken kan ha nytta av att skala ut arbets belastningen. Mer information finns i horisontell partitionering och [funktionell partitionering](#functional-partitioning) [mellan databaser](#cross-database-sharding) .

- **Program som har under optimala frågor**

  Program, särskilt de i data åtkomst skiktet, som har dåligt anpassade frågor kanske inte har en högre beräknings storlek. Detta inkluderar frågor som saknar en WHERE-sats, har saknade index eller har inaktuell statistik. De här programmen drar nytta av standard metoder för prestanda justering för frågor. Mer information finns i [saknade index](#identifying-and-adding-missing-indexes) och inställning av [frågor och tips](#query-tuning-and-hinting).

- **Program som har under optimal data åtkomst design**

   Program som har samtidiga data åtkomst samtidighets problem, till exempel död läge, har kanske inte nytta av en högre beräknings storlek. Överväg att minska antalet tur och utdata mot databasen genom att cachelagra data på klient sidan med Azure caching service eller någon annan teknik för cachelagring. Se [cachelagring av program nivå](#application-tier-caching).

## <a name="tune-your-database"></a>Finjustera databasen

I det här avsnittet tittar vi på några tekniker som du kan använda för att finjustera databasen för att få bästa möjliga prestanda för ditt program och köra det med minsta möjliga beräknings storlek. Några av dessa tekniker stämmer överens med traditionella SQL Server justerings metoder, men andra är bara för Azure SQL Database och Azure SQL-hanterad instans. I vissa fall kan du undersöka de förbrukade resurserna för en databas för att hitta områden för att ytterligare justera och utöka traditionella SQL Server tekniker för att arbeta i Azure SQL Database och Azure SQL-hanterad instans.

### <a name="identifying-and-adding-missing-indexes"></a>Identifiera och lägga till saknade index

Ett vanligt problem i OLTP-databasens prestanda relaterar till den fysiska databas designen. Databas scheman är ofta utformade och levererade utan testning i skala (antingen i belastning eller på data volym). Tyvärr kan prestandan för en frågeplan vara acceptabel i en liten skala men försämras kraftigt under data volymer på produktions nivå. Den vanligaste orsaken till det här problemet är att det inte finns tillräckligt med index för att uppfylla filter eller andra begränsningar i en fråga. Ofta manifest som saknas indexeras som en tabells ökning när det kan vara tillräckligt med index sökning.

I det här exemplet använder den valda fråg en sökning när en sökning skulle räcka:

```sql
DROP TABLE dbo.missingindex;
CREATE TABLE dbo.missingindex (col1 INT IDENTITY PRIMARY KEY, col2 INT);
DECLARE @a int = 0;
SET NOCOUNT ON;
BEGIN TRANSACTION
    WHILE @a < 20000
    BEGIN
        INSERT INTO dbo.missingindex(col2) VALUES (@a);
        SET @a += 1;
    END
    COMMIT TRANSACTION;
    GO
SELECT m1.col1
    FROM dbo.missingindex m1 INNER JOIN dbo.missingindex m2 ON(m1.col1=m2.col1)
    WHERE m1.col2 = 4;
```

![En frågeplan med saknade index](./media/performance-guidance/query_plan_missing_indexes.png)

Azure SQL Database och Azure SQL-hanterad instans kan hjälpa dig att hitta och åtgärda vanliga saknade index villkor. DMV: er som är inbyggda i Azure SQL Database och Azure SQL-hanterad instans tittar på där ett index avsevärt minskar den beräknade kostnaden för att köra en fråga. Vid frågekörningen spårar databas motorn hur ofta varje frågeplan körs och spårar det uppskattade avståndet mellan den pågående frågeplanen och den tänkta var indexet fanns. Du kan använda dessa DMV: er för att snabbt gissa vilka ändringar av den fysiska databas designen som kan förbättra den totala arbets belastnings kostnaden för en databas och dess verkliga arbets belastning.

Du kan använda den här frågan för att utvärdera potentiella index som saknas:

```sql
SELECT
   CONVERT (varchar, getdate(), 126) AS runtime
   , mig.index_group_handle
   , mid.index_handle
   , CONVERT (decimal (28,1), migs.avg_total_user_cost * migs.avg_user_impact *
        (migs.user_seeks + migs.user_scans)) AS improvement_measure
   , 'CREATE INDEX missing_index_' + CONVERT (varchar, mig.index_group_handle) + '_' +
        CONVERT (varchar, mid.index_handle) + ' ON ' + mid.statement + '
        (' + ISNULL (mid.equality_columns,'')
        + CASE WHEN mid.equality_columns IS NOT NULL
        AND mid.inequality_columns IS NOT NULL
        THEN ',' ELSE '' END + ISNULL (mid.inequality_columns, '') + ')'
        + ISNULL (' INCLUDE (' + mid.included_columns + ')', '') AS create_index_statement
   , migs.*
   , mid.database_id
   , mid.[object_id]
FROM sys.dm_db_missing_index_groups AS mig
   INNER JOIN sys.dm_db_missing_index_group_stats AS migs
      ON migs.group_handle = mig.index_group_handle
   INNER JOIN sys.dm_db_missing_index_details AS mid
      ON mig.index_handle = mid.index_handle
 ORDER BY migs.avg_total_user_cost * migs.avg_user_impact * (migs.user_seeks + migs.user_scans) DESC
```

I det här exemplet resulterade frågan i det här förslaget:

```sql
CREATE INDEX missing_index_5006_5005 ON [dbo].[missingindex] ([col2])  
```

När den har skapats väljer samma SELECT-instruktion en annan plan, som använder en sökning i stället för en genomsökning och kör sedan planen mer effektivt:

![En frågeplan med korrigerade index](./media/performance-guidance/query_plan_corrected_indexes.png)

Den viktigaste insikten är att IO-kapaciteten för ett delat, råvaru system är mer begränsad än för en dedikerad server maskin. Det finns ett bidrag för att minimera onödig i/o för att dra nytta av systemet i resurserna för varje beräknings storlek på tjänst nivåerna. Lämpliga alternativ för fysisk databas design kan avsevärt förbättra svars tiden för enskilda frågor, förbättra data flödet för samtidiga förfrågningar som hanteras per skalnings enhet och minimera de kostnader som krävs för att uppfylla frågan. Mer information om det saknade indexet DMV: er finns [sys.dm_db_missing_index_details](/sql/relational-databases/system-dynamic-management-views/sys-dm-db-missing-index-details-transact-sql).

### <a name="query-tuning-and-hinting"></a>Fråga om justering och tips

Frågans optimering i Azure SQL Database och Azure SQL-hanterad instans liknar den traditionella SQL Server frågans optimering. De flesta av de bästa metoderna för att justera frågor och förstå de orsaker till modell begränsningarna för frågans optimering gäller även för Azure SQL Database och Azure SQL-hanterad instans. Om du ställer in frågor i Azure SQL Database och Azure SQL-hanterad instans kan du få ytterligare fördelar med att minska de sammanställda resurs kraven. Ditt program kanske kan köras till en lägre kostnad än en avpassad motsvarighet eftersom det kan köras med en lägre beräknings storlek.

Ett exempel som är vanligt i SQL Server och som även gäller för Azure SQL Database och Azure SQL-hanterad instans är hur parametrarna för frågans optimering "sniffer". Vid kompileringen utvärderar Query Optimering det aktuella värdet för en parameter för att avgöra om den kan generera en mer optimal frågeplan. Även om den här strategin ofta kan leda till en frågeplan som är betydligt snabbare än en plan som kompilerats utan kända parameter värden, fungerar den för närvarande i SQL Server, i Azure SQL Database och Azure SQL-hanterad instans. Ibland går det inte att använda parametern, och ibland identifieras parametern, men den genererade planen är under optimal för den fullständiga uppsättningen parameter värden i en arbets belastning. Microsoft innehåller frågetipset (direktiv) så att du kan ange avsikten mer avsiktligt och åsidosätta standard beteendet för parameter avlyssning. Om du använder tips kan du ofta åtgärda fall där standard SQL Server, Azure SQL Database och Azure SQL-hanterad instans beteende är perfekt för en specifik kund arbets belastning.

I nästa exempel visas hur frågeprocessorn kan generera en plan som är under optimal både för prestanda-och resurs krav. I det här exemplet visas även att om du använder ett frågeuttryck kan du minska körnings tid och resurs krav för fråga för databasen:

```sql
DROP TABLE psptest1;
CREATE TABLE psptest1(col1 int primary key identity, col2 int, col3 binary(200));
DECLARE @a int = 0;
SET NOCOUNT ON;
BEGIN TRANSACTION
   WHILE @a < 20000
   BEGIN
     INSERT INTO psptest1(col2) values (1);
     INSERT INTO psptest1(col2) values (@a);
     SET @a += 1;
   END
   COMMIT TRANSACTION
   CREATE INDEX i1 on psptest1(col2);
GO

CREATE PROCEDURE psp1 (@param1 int)
   AS
   BEGIN
      INSERT INTO t1 SELECT * FROM psptest1
      WHERE col2 = @param1
      ORDER BY col2;
    END
    GO

CREATE PROCEDURE psp2 (@param2 int)
   AS
   BEGIN
      INSERT INTO t1 SELECT * FROM psptest1 WHERE col2 = @param2
      ORDER BY col2
      OPTION (OPTIMIZE FOR (@param2 UNKNOWN))
   END
   GO

CREATE TABLE t1 (col1 int primary key, col2 int, col3 binary(200));
GO
```

Installations koden skapar en tabell som har skevad data distribution. Den optimala fråge planen varierar beroende på vilken parameter som väljs. Tyvärr kan inte plan caching-beteendet kompilera om frågan utifrån det vanligaste parametervärdet. Därför är det möjligt att ett under optimalt schema cachelagras och används för många värden, även om en annan plan kan vara ett bättre plan val i genomsnitt. Sedan skapar fråge planen två lagrade procedurer som är identiska, förutom att en har ett särskilt frågeuttryck.

```sql
-- Prime Procedure Cache with scan plan
EXEC psp1 @param1=1;
TRUNCATE TABLE t1;

-- Iterate multiple times to show the performance difference
DECLARE @i int = 0;
WHILE @i < 1000
   BEGIN
      EXEC psp1 @param1=2;
      TRUNCATE TABLE t1;
      SET @i += 1;
    END
```

Vi rekommenderar att du väntar minst 10 minuter innan du börjar del 2 av exemplet, så att resultaten är distinkta i de resulterande telemetridata.

```sql
EXEC psp2 @param2=1;
TRUNCATE TABLE t1;

DECLARE @i int = 0;
    WHILE @i < 1000
    BEGIN
        EXEC psp2 @param2=2;
        TRUNCATE TABLE t1;
        SET @i += 1;
    END
```

Varje del av det här exemplet försöker köra en parameter INSERT-instruktion 1 000 gånger (för att generera en tillräckligt stor belastning för att använda som en test data uppsättning). När den kör lagrade procedurer undersöker frågeprocessorn det parameter värde som skickas till proceduren under den första kompileringen (parameter "Sniffing"). Processorn cachelagrar den resulterande planen och använder den för senare anrop, även om parametervärdet är annorlunda. Den optimala planen kanske inte används i samtliga fall. Ibland behöver du guida optimeringen för att välja en plan som är bättre för det genomsnittliga fallet snarare än det speciella fallet från när frågan först kompilerades. I det här exemplet skapar den ursprungliga planen en "skanning"-plan som läser alla rader för att hitta varje värde som matchar parametern:

![Fråga efter justering med hjälp av en skannings plan](./media/performance-guidance/query_tuning_1.png)

Eftersom vi körde proceduren genom att använda värdet 1, var den resulterande planen optimal för värdet 1 men var under optimal för alla andra värden i tabellen. Resultatet är troligen inte vad du skulle vilja om du väljer att välja varje plan slumpmässigt, eftersom planen presterar långsammare och använder fler resurser.

Om du kör testet med `SET STATISTICS IO` inställt på `ON` , utförs den logiska genomsökningen i det här exemplet i bakgrunden. Du kan se att det finns 1 148 läsningar som utförs av planen (vilket är ineffektivt, om det genomsnittliga fallet är att returnera bara en rad):

![Fråga efter justering med hjälp av en logisk sökning](./media/performance-guidance/query_tuning_2.png)

I den andra delen av exemplet används ett frågetipset för att se till att optimeringen använder ett speciellt värde under kompileringen. I det här fallet tvingar den Query-processorn att ignorera värdet som skickas som parameter och i stället för att anta `UNKNOWN` . Detta avser ett värde som har den genomsnittliga frekvensen i tabellen (ignorerar skevning). Den resulterande planen är en söknings baserad plan som är snabbare och använder färre resurser i genomsnitt än planen i del 1 av det här exemplet:

![Fråga efter justering med ett frågeuttryck](./media/performance-guidance/query_tuning_3.png)

Du kan se resultatet i **sys.resource_statss** tabellen (det uppstår en fördröjning från den tid som du utför testet och när data fyller tabellen). I det här exemplet körs del 1 under perioden 22:25:00 och del 2 körs vid 22:35:00. I det tidigare tidsfönstret användes fler resurser i den tids perioden än det senare (på grund av plan effektivitets förbättringar).

```sql
SELECT TOP 1000 *
FROM sys.resource_stats
WHERE database_name = 'resource1'
ORDER BY start_time DESC
```

![Exempel på frågeresultat för frågor](./media/performance-guidance/query_tuning_4.png)

> [!NOTE]
> Även om volymen i det här exemplet är avsiktligt liten, kan effekterna av under-optimala parametrar vara betydande, särskilt i större databaser. Skillnaden i extrema fall kan vara mellan några sekunder för snabba fall och timmar för långsamma fall.

Du kan undersöka **sys.resource_stats** för att avgöra om resursen för ett test använder mer eller färre resurser än ett annat test. När du jämför data, separerar du tidsintervallet för testerna så att de inte är i samma 5-minuters fönster i vyn **sys.resource_stats** . Syftet med övningen är att minimera den totala mängden resurser som används och inte minimera de högsta resurserna. Att optimera en del av koden för svars tiden minskar vanligt vis resurs förbrukningen. Kontrol lera att de ändringar du gör i ett program är nödvändiga och att ändringarna inte negativt påverkar kund upplevelsen för någon som kanske använder frågetipset i programmet.

Om en arbets belastning har en uppsättning upprepade frågor, är det ofta klokt att samla in och validera den optimala planen för dina prenumerations val eftersom den innehåller den minsta resurs storleks enhet som krävs för att vara värd för databasen. När du har verifierat det kan du ibland undersöka planerna för att hjälpa dig att se till att de inte försämras. Du kan lära dig mer om [frågetipset (Transact-SQL)](/sql/t-sql/queries/hints-transact-sql-query).

### <a name="very-large-database-architectures"></a>Mycket stora databas arkitekturer

Före lanseringen av den [storskaliga](service-tier-hyperscale.md) tjänst nivån för enskilda databaser i Azure SQL Database, använder kunderna kapacitets gränser för enskilda databaser. Dessa kapacitets gränser finns fortfarande för databaser i pooler i Azure SQL Database elastiska pooler och instans databaser i Azure SQL-hanterade instanser. I följande två avsnitt diskuteras två alternativ för att lösa problem med mycket stora databaser i Azure SQL Database och Azure SQL-hanterad instans när du inte kan använda den storskaliga tjänst nivån.

### <a name="cross-database-sharding"></a>Horisontell partitionering mellan databaser

Eftersom Azure SQL Database och Azure SQL-hanterad instans körs på maskin vara, är kapacitets gränserna för en enskild databas lägre än för en traditionell lokal SQL Server-installation. Vissa kunder använder horisontell partitionering-tekniker för att sprida databas åtgärder över flera databaser när åtgärderna inte ryms inom gränserna för en enskild databas i Azure SQL Database och Azure SQL-hanterad instans. De flesta kunder som använder horisontell partitionering-tekniker i Azure SQL Database och Azure SQL-hanterad instans delar sina data på en enskild dimension över flera databaser. För den här metoden måste du förstå att OLTP-programmen ofta utför transaktioner som endast gäller för en rad eller en liten grupp rader i schemat.

> [!NOTE]
> Azure SQL Database har nu ett bibliotek som hjälper dig med horisontell partitionering. Mer information finns i [Översikt över Elastic Database klient bibliotek](elastic-database-client-library.md).

Om en databas till exempel har kund namn, order och beställnings information (t. ex. Northwind-databasen med SQL Server) kan du dela upp dessa data i flera databaser genom att gruppera en kund med relaterad order-och order detalj information. Du kan garantera att kundens data finns kvar i en enskild databas. Programmet skulle dela olika kunder över databaserna och på ett effektivt sätt sprida belastningen över flera databaser. Med horisontell partitionering kan kunder inte bara undvika den maximala gränsen för databas storlek, men Azure SQL Database och Azure SQL Managed instance kan också bearbeta arbets belastningar som är betydligt större än gränserna för de olika beräknings storlekarna, så länge som varje enskild databas passar i tjänst nivå gränserna.

Även om databas horisontell partitionering inte minskar den sammanställda resurs kapaciteten för en lösning, är det mycket effektivt att stödja mycket stora lösningar som sprids över flera databaser. Varje databas kan köras med en annan beräknings storlek för att stödja mycket stora, "effektiva" databaser med höga resurs krav.

#### <a name="functional-partitioning"></a>Funktionell partitionering

Användare kombinerar ofta många funktioner i en enskild databas. Om ett program exempelvis har logik för att hantera inventering för en butik, kan den databasen ha logik kopplad till inventering, spåra inköps order, lagrade procedurer och indexerade eller materialiserade vyer som hanterar månads rapportering. Den här tekniken gör det lättare att administrera databasen för åtgärder som säkerhets kopiering, men du måste också ändra maskin varans storlek för att hantera belastningen för alla funktioner i ett program.

Om du använder en skalbar arkitektur i Azure SQL Database och en hanterad Azure SQL-instans, är det en bra idé att dela upp olika funktioner i ett program i olika databaser. Genom att använda den här metoden skalar varje program oberoende av varandra. När ett program blir busier (och belastningen på databasen ökar) kan administratören välja oberoende beräknings storlekar för varje funktion i programmet. I och med den här arkitekturen kan ett program vara större än en enda dator som kan hantera eftersom belastningen sprids över flera datorer.

### <a name="batch-queries"></a>Batch-frågor

För program som har åtkomst till data med hjälp av hög volym, frekventa, ad hoc-frågor, ägnas en stor mängd svars tid på nätverkskommunikation mellan program nivån och databas nivån. Även om både programmet och databasen finns i samma data Center, kan nätverks fördröjningen mellan två förstoras av ett stort antal data åtkomst åtgärder. Överväg att använda alternativet för att antingen gruppera ad hoc-frågorna eller kompilera dem som lagrade procedurer för att minska antalet nätverks fördröjningar för data åtkomst åtgärder. Om du batcherar ad hoc-frågorna kan du skicka flera frågor som en stor grupp i en enda resa till-databasen. Om du kompilerar ad hoc-frågor i en lagrad procedur kan du få samma resultat som om du grupperar dem. Med hjälp av en lagrad procedur får du också fördelarna med att öka risken för att cachelagra fråge planerna i databasen så att du kan använda den lagrade proceduren igen.

Vissa program är av Skriv intensiva. Ibland kan du minska den totala IO-belastningen på en databas genom att fundera över hur du skriver tillsammans. Detta är ofta lika enkelt som att använda explicita transaktioner i stället för transaktioner som ska genomföras automatiskt i lagrade procedurer och ad hoc-batchar. En utvärdering av olika tekniker som du kan använda finns i [batching-tekniker för databas program i Azure](../performance-improve-use-batching.md). Experimentera med din egen arbets belastning för att hitta rätt modell för batchbearbetning. Se till att förstå att en modell kan ha något annorlunda konsekvens garantier för transaktioner. Att hitta rätt arbets belastning som minimerar resursanvändningen kräver att du hittar rätt kombination av konsekvens-och prestanda kompromisser.

### <a name="application-tier-caching"></a>Cachelagring på program nivå

Vissa databas program har Läs-tunga arbets belastningar. Cachelagring av lager kan minska belastningen på databasen och kan eventuellt minska den beräknings storlek som krävs för att stödja en databas med hjälp av Azure SQL Database och Azure SQL-hanterad instans. Med [Azure cache för Redis](https://azure.microsoft.com/services/cache/)kan du, om du har en skrivskyddad arbets belastning, läsa data en gång (eller kanske en gång per dator på program nivå, beroende på hur den har kon figurer ATS) och sedan lagra dessa data utanför databasen. Detta är ett sätt att minska databas belastningen (CPU och Läs-i/o), men det finns en inverkan på transaktionell konsekvens eftersom data som läses från cachen inte kan synkroniseras med data i databasen. Även om i många program är en viss nivå av inkonsekvens acceptabel, men det är inte sant för alla arbets belastningar. Du bör fullständigt förstå alla program krav innan du implementerar en strategi för cachelagring på program nivå.

## <a name="next-steps"></a>Nästa steg

- Mer information om DTU-baserade tjänst nivåer finns i [DTU-baserad inköps modell](service-tiers-dtu.md).
- Mer information om vCore-baserade tjänst nivåer finns i [vCore-baserad inköps modell](service-tiers-vcore.md).
- Mer information om elastiska pooler finns i [Vad är en elastisk Azure-pool?](elastic-pool-overview.md)
- Information om prestanda och elastiska pooler finns i [när du ska överväga en elastisk pool](elastic-pool-overview.md)