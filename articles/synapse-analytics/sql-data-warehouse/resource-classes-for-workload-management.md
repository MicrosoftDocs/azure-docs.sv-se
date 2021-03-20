---
title: Resurs klasser för hantering av arbets belastning
description: Vägledning för att använda resurs klasser för att hantera samtidighet och beräknings resurser för frågor i Azure Synapse Analytics.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 02/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 7714ce748eb172565357723924ab2212e9559e1f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98685335"
---
# <a name="workload-management-with-resource-classes-in-azure-synapse-analytics"></a>Arbets belastnings hantering med resurs klasser i Azure Synapse Analytics

Vägledning för att använda resurs klasser för att hantera minne och samtidighet för Synapse i Azure Synapse.  

## <a name="what-are-resource-classes"></a>Vad är resurs klasser

Prestanda kapaciteten för en fråga bestäms av användarens resurs klass.  Resurs klasser är i förväg bestämda resurs gränser i Synapse SQL-pool som styr beräknings resurser och samtidighet för frågekörningen. Resurs klasser kan hjälpa dig att konfigurera resurser för dina frågor genom att ange gränser för antalet frågor som körs samtidigt och på beräknings resurserna som tilldelas varje fråga.  Det finns en kompromiss mellan minne och samtidighet.

- Mindre resurs klasser minskar högsta mängd minne per fråga, men ökar samtidigheten.
- Större resurs klasser ökar det maximala minnet per fråga, men minskar samtidigheten.

Det finns två typer av resurs klasser:

- Statiska resurs klasser, som lämpar sig väl för ökad samtidighet i en data uppsättnings storlek som har åtgärd ATS.
- Dynamiska resurs klasser, som lämpar sig väl för data uppsättningar som växer och behöver bättre prestanda när tjänste nivån skalas upp.

Resurs klasser använder samtidiga platser för att mäta resursförbrukning.  [Samtidiga platser](#concurrency-slots) beskrivs senare i den här artikeln.

- Om du vill visa resursutnyttjande för resurs klasserna, se [minnes-och samtidiga gränser](memory-concurrency-limits.md).
- Du kan ändra resurs klassen genom att köra frågan under en annan användare eller [ändra den aktuella användarens resurs klass](#change-a-users-resource-class) medlemskap.

### <a name="static-resource-classes"></a>Statiska resurs klasser

Statiska resurs klasser allokerar samma mängd minne oavsett den aktuella prestanda nivån, som mäts i [informations lager enheter](what-is-a-data-warehouse-unit-dwu-cdwu.md). Eftersom frågor får samma minnesallokering oavsett prestanda nivå kan du [skala ut data lagret](quickstart-scale-compute-portal.md) för att köra fler frågor i en resurs klass.  Statiska resurs klasser är idealiska om data volymen är känd och konstant.

De statiska resurs klasserna implementeras med följande fördefinierade databas roller:

- staticrc10
- staticrc20
- staticrc30
- staticrc40
- staticrc50
- staticrc60
- staticrc70
- staticrc80

### <a name="dynamic-resource-classes"></a>Dynamiska resurs klasser

Dynamiska resurs klasser allokerar en varierande mängd minne beroende på aktuell tjänste nivå. Även om statiska resurs klasser är fördelaktiga för högre samtidiga och statiska data volymer, passar dynamiska resurs klasser bättre för en växande eller varierande mängd data.  När du skalar upp till en större service nivå får dina frågor automatiskt mer minne.  

De dynamiska resurs klasserna implementeras med följande fördefinierade databas roller:

- smallrc
- mediumrc
- largerc
- xlargerc

Minnes tilldelningen för varje resurs klass är följande.

| Servicenivå  | smallrc           | mediumrc               | largerc                | xlargerc               |
|:--------------:|:-----------------:|:----------------------:|:----------------------:|:----------------------:|
| DW100c         | 25 %               | 25 %                    | 25 %                    | 70 %                    |
| DW200c         | 12,5%             | 12,5%                  | 22.2                    | 70 %                    |
| DW300c         | 8 %                | 10 %                    | 22.2                    | 70 %                    |
| DW400c         | 6.25%             | 10 %                    | 22.2                    | 70 %                    |
| DW500c         | 5 %                | 10 %                    | 22.2                    | 70 %                    |
| DW1000c till<br> DW30000c | 3 %       | 10 %                    | 22.2                    | 70 %                    |

### <a name="default-resource-class"></a>Standard resurs klass

Som standard är varje användare medlem i den dynamiska resurs klassen **smallrc**.

Tjänst administratörens resurs klass är fast i smallrc och kan inte ändras.  Tjänst administratören är den användare som skapas under etablerings processen.  Tjänst administratören i den här kontexten är den inloggning som anges för "server admin-inloggning" när en ny Synapse-SQL-pool skapas med en ny server.

> [!NOTE]
> Användare eller grupper som definieras som Active Directory administratör är också tjänst administratörer.
>
>

## <a name="resource-class-operations"></a>Resurs klass åtgärder

Resurs klasser är utformade för att förbättra prestandan för data hanterings-och Manipulerings aktiviteter. Komplexa frågor kan också dra nytta av att köra under en stor resurs klass. Till exempel kan fråga om prestanda för stora kopplingar och sorteringar förbättra när resurs klassen är tillräckligt stor för att frågan ska kunna köras i minnet.

### <a name="operations-governed-by-resource-classes"></a>Åtgärder som styrs av resurs klasser

Dessa åtgärder styrs av resurs klasser:

- INFOGA-VÄLJ, UPPDATERA, TA BORT
- Välj (när du frågar efter användar tabeller)
- ÄNDRA INDEX-återskapa eller ordna om
- ÄNDRA TABELL ÅTERUPPBYGGNAD
- CREATE INDEX
- SKAPA GRUPPERAT COLUMNSTORE-INDEX
- CREATE TABLE SOM SELECT (CTAS)
- Läsa in data
- Data flyttnings åtgärder som utförs av data flytt tjänsten (DMS)

> [!NOTE]  
> SELECT-instruktioner i vyer för dynamisk hantering (DMV: er) eller andra systemvyer styrs inte av någon av samtidiga begränsningarna. Du kan övervaka systemet oavsett hur många frågor som körs på den.
>
>

### <a name="operations-not-governed-by-resource-classes"></a>Åtgärder som inte styrs av resurs klasser

Vissa frågor körs alltid i resurs klassen smallrc även om användaren är medlem i en större resurs klass. Dessa undantags frågor räknas inte mot samtidighets gränsen. Om samtidigheten till exempel är 16 kan många användare välja från systemvyer utan att de tillgängliga samtidiga platserna påverkas.

Följande instruktioner är undantagna från resurs klasser och körs alltid i smallrc:

- Skapa eller ta bort tabell
- ÄNDRA TABELL... Växla, dela eller sammanfoga PARTITION
- ÄNDRA INDEX INAKTIVERA
- DROP INDEX
- SKAPA, uppdatera eller släppa statistik
- TRUNCATE TABLE
- ÄNDRA AUKTORISERING
- SKAPA INLOGGNING
- SKAPA, ändra eller släppa användare
- SKAPA, ändra eller ta bort procedur
- Skapa eller släppa vy
- INFOGA VÄRDEN
- Välj från systemvyer och DMV: er
- EXPLAIN
- DBCC

<!--
Removed as these two are not confirmed / supported under Azure Synapse Analytics
- CREATE REMOTE TABLE AS SELECT
- CREATE EXTERNAL TABLE AS SELECT
- REDISTRIBUTE
-->

## <a name="concurrency-slots"></a>Samtidiga platser

Samtidiga platser är ett bekvämt sätt att spåra resurserna som är tillgängliga för frågekörningen. De är som biljetter som du köper till att reservera platser i en konsert eftersom platsen är begränsad. Det totala antalet samtidiga platser per informations lager fastställs av Service nivån. Innan en fråga kan börja köra måste den kunna reservera tillräckligt många samtidiga platser. När en fråga har slutförts släpper den sina samtidiga platser.  

- En fråga som kör med 10 samtidiga platser kan komma åt 5 gånger mer beräknings resurser än en fråga som körs med 2 samtidiga platser.
- Om varje fråga kräver 10 samtidiga platser och det finns 40 concurrency-kortplatser kan endast 4 frågor köras samtidigt.

Endast resurs reglerade frågor förbrukar samtidiga platser. System frågor och vissa enkla frågor använder inte några platser. Det exakta antalet förbrukade concurrency-platser bestäms av frågans resurs klass.

## <a name="view-the-resource-classes"></a>Visa resurs klasserna

Resurs klasser implementeras som fördefinierade databas roller. Det finns två typer av resurs klasser: dynamiska och statiska. Om du vill visa en lista över resurs klasser använder du följande fråga:

```sql
SELECT name
FROM   sys.database_principals
WHERE  name LIKE '%rc%' AND type_desc = 'DATABASE_ROLE';
```

## <a name="change-a-users-resource-class"></a>Ändra en användares resurs klass

Resurs klasser implementeras genom att tilldela användare till databas roller. När en användare kör en fråga körs frågan med användarens resurs klass. Om en användare till exempel är medlem i databas rollen staticrc10, körs frågorna med små mängder minne. Om en databas användare är medlem i databas rollerna xlargerc eller staticrc80, körs frågorna med stora mängder minne.

Om du vill öka en användares resurs klass använder du [sp_addrolemember](/sql/relational-databases/system-stored-procedures/sp-addrolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true) för att lägga till användaren till en databas roll för en stor resurs klass.  Koden nedan lägger till en användare i largerc-databas rollen.  Varje begäran får 22% av system minnet.

```sql
EXEC sp_addrolemember 'largerc', 'loaduser';
```

Om du vill minska resurs klassen använder du [sp_droprolemember](/sql/relational-databases/system-stored-procedures/sp-droprolemember-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest&preserve-view=true).  Om ' loaduser ' inte är en medlem eller någon annan resurs klass hamnar de i standard resurs klassen smallrc med 3% minnes tilldelning.  

```sql
EXEC sp_droprolemember 'largerc', 'loaduser';
```

## <a name="resource-class-precedence"></a>Resurs klass prioritet

Användare kan vara medlemmar i flera resurs klasser. När en användare tillhör fler än en resurs klass:

- Dynamiska resurs klasser prioriteras över statiska resurs klasser. Om en användare till exempel är medlem i både mediumrc (dynamisk) och staticrc80 (statisk), körs frågor med mediumrc.
- Större resurs klasser prioriteras framför mindre resurs klasser. Om en användare till exempel är medlem i mediumrc och largerc, kör frågor med largerc. Om en användare däremot är medlem i både staticrc20 och statirc80, körs frågor med staticrc80 resurs tilldelningar.

## <a name="recommendations"></a>Rekommendationer

>[!NOTE]
>Överväg att använda funktioner för hantering av arbets belastning ([arbets belastnings isolering](sql-data-warehouse-workload-isolation.md), [klassificering](sql-data-warehouse-workload-classification.md) och [prioritet](sql-data-warehouse-workload-importance.md)) för mer kontroll över din arbets belastning och förutsägbar prestanda.  
>
>

Vi rekommenderar att du skapar en användare som är dedikerad för att köra en särskild typ av fråga eller inläsnings åtgärd. Ge den användaren en permanent resurs klass i stället för att ändra resurs klassen regelbundet. Statiska resurs klasser ger bättre övergripande kontroll över arbets belastningen, så vi rekommenderar att du använder statiska resurs klasser innan du överväger dynamiska resurs klasser.

### <a name="resource-classes-for-load-users"></a>Resurs klasser för inläsning av användare

`CREATE TABLE` använder grupperade columnstore-index som standard. Komprimering av data i ett columnstore-index är en minnes intensiv åtgärd och minnes belastningen kan minska index kvaliteten. Minnes belastning kan leda till att en högre resurs klass krävs vid inläsning av data. För att säkerställa att belastningen har tillräckligt med minne, kan du skapa en användare som är avsedd för att köra belastningar och tilldela användaren till en högre resurs klass.

Vilken mängd minne som krävs för att bearbeta belastningarna beror på vilken tabell som lästs in och data storleken. Mer information om minnes krav finns i [maximera radgrupps-kvalitet](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).

När du har fastställt minnes kravet väljer du om du vill tilldela inläsnings användaren till en statisk eller dynamisk resurs klass.

- Använd en statisk resurs klass när kraven på tabell minnet ligger inom ett angivet intervall. Läser in körning med lämpligt minne. När du skalar data lagret behöver inte belastningarna mer minne. Genom att använda en statisk resurs klass förblir minnes tilldelningen konstant. Den här konsekvensen sparar minne och gör att fler frågor kan köras samtidigt. Vi rekommenderar att nya lösningar använder de statiska resurs klasserna först eftersom dessa ger större kontroll.
- Använd en dynamisk resurs klass när kraven på tabell minne varierar mycket. Belastningar kan kräva mer minne än den aktuella DWU-eller cDWU-nivån. Skalning av informations lagret lägger till mer minne för att läsa in åtgärder, vilket gör att belastningen kan utföras snabbare.

### <a name="resource-classes-for-queries"></a>Resurs klasser för frågor

Vissa frågor är beräknings intensiva och andra inte.  

- Välj en dynamisk resurs klass när frågor är komplexa, men behöver inte hög samtidighet.  Till exempel är det ett tillfälligt behov av resurser att generera dagliga eller vecko Visa rapporter. Om rapporterna bearbetar stora mängder data ger data lagret mer minne till användarens befintliga resurs klass.
- Välj en statisk resurs klass när resurs förväntningarna varierar under dagen. En statisk resurs klass fungerar till exempel bra om data lagret efter frågas av många personer. När du skalar data lagret ändras inte mängden minne som allokerats till användaren. Det innebär att fler frågor kan köras parallellt i systemet.

Lämpliga minnes anslag är beroende av många faktorer, till exempel mängden data som frågas, tabell schemannas beskaffenhet och olika kopplingar, väljer och grupperar predikat. I allmänhet kan du allokera mer minne för att utföra snabbare frågor, men minskar den övergripande samtidigheten. Om samtidighet inte är ett problem skadar överallokerat minne inte data flödet.

Använd olika resurs klasser för att justera prestanda. Nästa avsnitt innehåller en lagrad procedur som hjälper dig att avgöra den bästa resurs klassen.

## <a name="example-code-for-finding-the-best-resource-class"></a>Exempel kod för att hitta den bästa resurs klassen

Du kan använda följande angivna lagrade procedur för att ta reda på samtidighet och minnes tilldelning per resurs klass vid en viss service nivå mål och den bästa resurs klassen för minnes intensiva åtgärder på en icke-partitionerad CCI-tabell i en viss resurs klass:

Här är syftet med den här lagrade proceduren:

1. För att se samtidighet och minnes tilldelning per resurs klass vid en viss service nivå mål. Användaren måste ange NULL för både schema och TableName, vilket visas i det här exemplet.  
2. För att se den bästa resurs klassen för de minnes intensiva CCI-åtgärderna (belastning, kopiera tabell, återskapa index osv.) i en icke-partitionerad CCI-tabell i en specifik resurs klass. Den lagrade proceduren använder Table schema för att ta reda på vilken minnes tilldelning som krävs.

### <a name="dependencies--restrictions"></a>Beroende & begränsningar

- Den här lagrade proceduren är inte utformad för att beräkna minnes kravet för en partitionerad CCI-tabell.
- Den här lagrade proceduren tar inte hänsyn till minnes kraven för SELECT-delen av CTAS/INSERT-SELECT och förutsätter att det är ett val.
- Den här lagrade proceduren använder en temporär tabell som är tillgänglig i den session där den lagrade proceduren skapades.
- Den här lagrade proceduren beror på aktuella erbjudanden (t. ex. maskin varu konfiguration, DMS-konfiguration) och om någon av dessa ändringar inte fungerar som den ska.  
- Den här lagrade proceduren beror på befintliga samtidiga gräns erbjudanden och om dessa ändringar inte fungerar på rätt sätt fungerar inte den här lagrade proceduren.  
- Den här lagrade proceduren är beroende av befintliga resurs klass erbjudanden och om den här ändringen så fungerar inte den här lagrade proceduren som den ska.  

>[!NOTE]  
>Om du inte får utdata när du har kört den lagrade proceduren med angivna parametrar, kan det finnas två fall.
>
>1. En DW-parameter innehåller ett ogiltigt värde för service nivå mål
>2. Eller så finns det ingen matchande resurs klass för åtgärden CCI i tabellen.
>
>I DW100c är till exempel den högsta tillgängliga minnes tilldelningen 1 GB och om Table schema är tillräckligt bred för att överskrida kravet på 1 GB.

### <a name="usage-example"></a>Exempel på användning

Syntax:  
`EXEC dbo.prc_workload_management_by_DWU @DWU VARCHAR(7), @SCHEMA_NAME VARCHAR(128), @TABLE_NAME VARCHAR(128)`
  
1. @DWU: Ange antingen en NULL-parameter för att extrahera den aktuella DWU från DW DB eller ange eventuella DWU som stöds i formatet "DW100c"
2. @SCHEMA_NAME: Ange ett schema namn för tabellen
3. @TABLE_NAME: Ange ett tabell namn för intresset

Exempel som kör den här lagrade proceduren:

```sql
EXEC dbo.prc_workload_management_by_DWU 'DW2000c', 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU NULL, 'dbo', 'Table1';  
EXEC dbo.prc_workload_management_by_DWU 'DW6000c', NULL, NULL;  
EXEC dbo.prc_workload_management_by_DWU NULL, NULL, NULL;  
```

Följande instruktion skapar Table1 som används i föregående exempel.
`CREATE TABLE Table1 (a int, b varchar(50), c decimal (18,10), d char(10), e varbinary(15), f float, g datetime, h date);`

### <a name="stored-procedure-definition"></a>Definition av lagrad procedur

```sql
-------------------------------------------------------------------------------
-- Dropping prc_workload_management_by_DWU procedure if it exists.
-------------------------------------------------------------------------------
IF EXISTS (SELECT * FROM sys.objects WHERE type = 'P' AND name = 'prc_workload_management_by_DWU')
DROP PROCEDURE dbo.prc_workload_management_by_DWU
GO

-------------------------------------------------------------------------------
-- Creating prc_workload_management_by_DWU.
-------------------------------------------------------------------------------
CREATE PROCEDURE dbo.prc_workload_management_by_DWU
(@DWU VARCHAR(8),
 @SCHEMA_NAME VARCHAR(128),
 @TABLE_NAME VARCHAR(128)
)
AS

IF @DWU IS NULL
BEGIN
-- Selecting proper DWU for the current DB if not specified.

SELECT @DWU = 'DW'+ CAST(CASE WHEN Mem> 4 THEN Nodes*500
  ELSE Mem*100
  END AS VARCHAR(10)) +'c'
    FROM (
      SELECT Nodes=count(distinct n.pdw_node_id), Mem=max(i.committed_target_kb/1000/1000/60)
        FROM sys.dm_pdw_nodes n
        CROSS APPLY sys.dm_pdw_nodes_os_sys_info i
        WHERE type = 'COMPUTE')A
END

-- Dropping temp table if exists.
IF OBJECT_ID('tempdb..#ref') IS NOT NULL
BEGIN
  DROP TABLE #ref;
END;

-- Creating ref. temp table (CTAS) to hold mapping info.
CREATE TABLE #ref
WITH (DISTRIBUTION = ROUND_ROBIN)
AS
WITH
-- Creating concurrency slots mapping for various DWUs.
alloc
AS
(
SELECT 'DW100c' AS DWU,4 AS max_queries,4 AS max_slots,1 AS slots_used_smallrc,1 AS slots_used_mediumrc,2 AS slots_used_largerc,4 AS slots_used_xlargerc,1 AS slots_used_staticrc10,2 AS slots_used_staticrc20,4 AS slots_used_staticrc30,4 AS slots_used_staticrc40,4 AS slots_used_staticrc50,4 AS slots_used_staticrc60,4 AS slots_used_staticrc70,4 AS slots_used_staticrc80
  UNION ALL
   SELECT 'DW200c',8,8,1,2,4,8,1,2,4,8,8,8,8,8
  UNION ALL
   SELECT 'DW300c',12,12,1,2,4,8,1,2,4,8,8,8,8,8
  UNION ALL
   SELECT 'DW400c',16,16,1,4,8,16,1,2,4,8,16,16,16,16
  UNION ALL
   SELECT 'DW500c',20,20,1,4,8,16,1,2,4,8,16,16,16,16
  UNION ALL
   SELECT 'DW1000c',32,40,1,4,8,28,1,2,4,8,16,32,32,32
  UNION ALL
   SELECT 'DW1500c',32,60,1,6,13,42,1,2,4,8,16,32,32,32
  UNION ALL
   SELECT 'DW2000c',48,80,2,8,17,56,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW2500c',48,100,3,10,22,70,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW3000c',64,120,3,12,26,84,1,2,4,8,16,32,64,64
  UNION ALL
   SELECT 'DW5000c',64,200,6,20,44,140,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW6000c',128,240,7,24,52,168,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW7500c',128,300,9,30,66,210,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW10000c',128,400,12,40,88,280,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW15000c',128,600,18,60,132,420,1,2,4,8,16,32,64,128
  UNION ALL
   SELECT 'DW30000c',128,1200,36,120,264,840,1,2,4,8,16,32,64,128
)
-- Creating workload mapping to their corresponding slot consumption and default memory grant.
,map  
AS
(
  SELECT CONVERT(varchar(20), 'SloDWGroupSmall') AS wg_name, slots_used_smallrc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupMedium') AS wg_name, slots_used_mediumrc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupLarge') AS wg_name, slots_used_largerc AS slots_used FROM alloc WHERE DWU = @DWU
UNION ALL
  SELECT CONVERT(varchar(20), 'SloDWGroupXLarge') AS wg_name, slots_used_xlargerc AS slots_used FROM alloc WHERE DWU = @DWU
  UNION ALL
  SELECT 'SloDWGroupC00',1
  UNION ALL
    SELECT 'SloDWGroupC01',2
  UNION ALL
    SELECT 'SloDWGroupC02',4
  UNION ALL
    SELECT 'SloDWGroupC03',8
  UNION ALL
    SELECT 'SloDWGroupC04',16
  UNION ALL
    SELECT 'SloDWGroupC05',32
  UNION ALL
    SELECT 'SloDWGroupC06',64
  UNION ALL
    SELECT 'SloDWGroupC07',128
)

-- Creating ref based on current / asked DWU.
, ref
AS
(
  SELECT  a1.*
  ,       m1.wg_name          AS wg_name_smallrc
  ,       m1.slots_used * 250 AS tgt_mem_grant_MB_smallrc
  ,       m2.wg_name          AS wg_name_mediumrc
  ,       m2.slots_used * 250 AS tgt_mem_grant_MB_mediumrc
  ,       m3.wg_name          AS wg_name_largerc
  ,       m3.slots_used * 250 AS tgt_mem_grant_MB_largerc
  ,       m4.wg_name          AS wg_name_xlargerc
  ,       m4.slots_used * 250 AS tgt_mem_grant_MB_xlargerc
  ,       m5.wg_name          AS wg_name_staticrc10
  ,       m5.slots_used * 250 AS tgt_mem_grant_MB_staticrc10
  ,       m6.wg_name          AS wg_name_staticrc20
  ,       m6.slots_used * 250 AS tgt_mem_grant_MB_staticrc20
  ,       m7.wg_name          AS wg_name_staticrc30
  ,       m7.slots_used * 250 AS tgt_mem_grant_MB_staticrc30
  ,       m8.wg_name          AS wg_name_staticrc40
  ,       m8.slots_used * 250 AS tgt_mem_grant_MB_staticrc40
  ,       m9.wg_name          AS wg_name_staticrc50
  ,       m9.slots_used * 250 AS tgt_mem_grant_MB_staticrc50
  ,       m10.wg_name          AS wg_name_staticrc60
  ,       m10.slots_used * 250 AS tgt_mem_grant_MB_staticrc60
  ,       m11.wg_name          AS wg_name_staticrc70
  ,       m11.slots_used * 250 AS tgt_mem_grant_MB_staticrc70
  ,       m12.wg_name          AS wg_name_staticrc80
  ,       m12.slots_used * 250 AS tgt_mem_grant_MB_staticrc80
  FROM alloc a1
  JOIN map   m1  ON a1.slots_used_smallrc     = m1.slots_used and m1.wg_name = 'SloDWGroupSmall'
  JOIN map   m2  ON a1.slots_used_mediumrc    = m2.slots_used and m2.wg_name = 'SloDWGroupMedium'
  JOIN map   m3  ON a1.slots_used_largerc     = m3.slots_used and m3.wg_name = 'SloDWGroupLarge'
  JOIN map   m4  ON a1.slots_used_xlargerc    = m4.slots_used and m4.wg_name = 'SloDWGroupXLarge'
  JOIN map   m5  ON a1.slots_used_staticrc10    = m5.slots_used and m5.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m6  ON a1.slots_used_staticrc20    = m6.slots_used and m6.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m7  ON a1.slots_used_staticrc30    = m7.slots_used and m7.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m8  ON a1.slots_used_staticrc40    = m8.slots_used and m8.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m9  ON a1.slots_used_staticrc50    = m9.slots_used and m9.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m10  ON a1.slots_used_staticrc60    = m10.slots_used and m10.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m11  ON a1.slots_used_staticrc70    = m11.slots_used and m11.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  JOIN map   m12  ON a1.slots_used_staticrc80    = m12.slots_used and m12.wg_name NOT IN ('SloDWGroupSmall','SloDWGroupMedium','SloDWGroupLarge','SloDWGroupXLarge')
  WHERE   a1.DWU = @DWU
)
SELECT  DWU
,       max_queries
,       max_slots
,       slots_used
,       wg_name
,       tgt_mem_grant_MB
,       up1 as rc
,       (ROW_NUMBER() OVER(PARTITION BY DWU ORDER BY DWU)) as rc_id
FROM
(
    SELECT  DWU
    ,       max_queries
    ,       max_slots
    ,       slots_used
    ,       wg_name
    ,       tgt_mem_grant_MB
    ,       REVERSE(SUBSTRING(REVERSE(wg_names),1,CHARINDEX('_',REVERSE(wg_names),1)-1)) as up1
    ,       REVERSE(SUBSTRING(REVERSE(tgt_mem_grant_MBs),1,CHARINDEX('_',REVERSE(tgt_mem_grant_MBs),1)-1)) as up2
    ,       REVERSE(SUBSTRING(REVERSE(slots_used_all),1,CHARINDEX('_',REVERSE(slots_used_all),1)-1)) as up3
    FROM    ref AS r1
    UNPIVOT
    (
        wg_name FOR wg_names IN (wg_name_smallrc,wg_name_mediumrc,wg_name_largerc,wg_name_xlargerc,
        wg_name_staticrc10, wg_name_staticrc20, wg_name_staticrc30, wg_name_staticrc40, wg_name_staticrc50,
        wg_name_staticrc60, wg_name_staticrc70, wg_name_staticrc80)
    ) AS r2
    UNPIVOT
    (
        tgt_mem_grant_MB FOR tgt_mem_grant_MBs IN (tgt_mem_grant_MB_smallrc,tgt_mem_grant_MB_mediumrc,
        tgt_mem_grant_MB_largerc,tgt_mem_grant_MB_xlargerc, tgt_mem_grant_MB_staticrc10, tgt_mem_grant_MB_staticrc20,
        tgt_mem_grant_MB_staticrc30, tgt_mem_grant_MB_staticrc40, tgt_mem_grant_MB_staticrc50,
        tgt_mem_grant_MB_staticrc60, tgt_mem_grant_MB_staticrc70, tgt_mem_grant_MB_staticrc80)
    ) AS r3
    UNPIVOT
    (
        slots_used FOR slots_used_all IN (slots_used_smallrc,slots_used_mediumrc,slots_used_largerc,
        slots_used_xlargerc, slots_used_staticrc10, slots_used_staticrc20, slots_used_staticrc30,
        slots_used_staticrc40, slots_used_staticrc50, slots_used_staticrc60, slots_used_staticrc70,
        slots_used_staticrc80)
    ) AS r4
) a
WHERE   up1 = up2
AND     up1 = up3
;

-- Getting current info about workload groups.
WITH  
dmv  
AS  
(
  SELECT
          rp.name                                           AS rp_name
  ,       rp.max_memory_kb*1.0/1048576                      AS rp_max_mem_GB
  ,       (rp.max_memory_kb*1.0/1024)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_MB
  ,       (rp.max_memory_kb*1.0/1048576)
          *(request_max_memory_grant_percent/100)           AS max_memory_grant_GB
  ,       wg.name                                           AS wg_name
  ,       wg.importance                                     AS importance
  ,       wg.request_max_memory_grant_percent               AS request_max_memory_grant_percent
  FROM    sys.dm_pdw_nodes_resource_governor_workload_groups wg
  JOIN    sys.dm_pdw_nodes_resource_governor_resource_pools rp    ON  wg.pdw_node_id  = rp.pdw_node_id
                                                                  AND wg.pool_id      = rp.pool_id
  WHERE   rp.name = 'SloDWPool'
  GROUP BY
          rp.name
  ,       rp.max_memory_kb
  ,       wg.name
  ,       wg.importance
  ,       wg.request_max_memory_grant_percent
)
-- Creating resource class name mapping.
,names
AS
(
  SELECT 'smallrc' as resource_class, 1 as rc_id
  UNION ALL
    SELECT 'mediumrc', 2
  UNION ALL
    SELECT 'largerc', 3
  UNION ALL
    SELECT 'xlargerc', 4
  UNION ALL
    SELECT 'staticrc10', 5
  UNION ALL
    SELECT 'staticrc20', 6
  UNION ALL
    SELECT 'staticrc30', 7
  UNION ALL
    SELECT 'staticrc40', 8
  UNION ALL
    SELECT 'staticrc50', 9
  UNION ALL
    SELECT 'staticrc60', 10
  UNION ALL
    SELECT 'staticrc70', 11
  UNION ALL
    SELECT 'staticrc80', 12
)
,base AS
(   SELECT  schema_name
    ,       table_name
    ,       SUM(column_count)                   AS column_count
    ,       ISNULL(SUM(short_string_column_count),0)   AS short_string_column_count
    ,       ISNULL(SUM(long_string_column_count),0)    AS long_string_column_count
    FROM    (   SELECT  sm.name                                             AS schema_name
                ,       tb.name                                             AS table_name
                ,       COUNT(co.column_id)                                 AS column_count
                           ,       CASE    WHEN co.system_type_id IN (36,43,106,108,165,167,173,175,231,239)
                                AND  co.max_length <= 32
                                THEN COUNT(co.column_id)
                        END                                                 AS short_string_column_count
                ,       CASE    WHEN co.system_type_id IN (165,167,173,175,231,239)
                                AND  co.max_length > 32 and co.max_length <=8000
                                THEN COUNT(co.column_id)
                        END                                                 AS long_string_column_count
                FROM    sys.schemas AS sm
                JOIN    sys.tables  AS tb   on sm.[schema_id] = tb.[schema_id]
                JOIN    sys.columns AS co   ON tb.[object_id] = co.[object_id]
                           WHERE tb.name = @TABLE_NAME AND sm.name = @SCHEMA_NAME
                GROUP BY sm.name
                ,        tb.name
                ,        co.system_type_id
                ,        co.max_length            ) a
GROUP BY schema_name
,        table_name
)
, size AS
(
SELECT  schema_name
,       table_name
,       75497472                                            AS table_overhead

,       column_count*1048576*8                              AS column_size
,       short_string_column_count*1048576*32                       AS short_string_size,       (long_string_column_count*16777216) AS long_string_size
FROM    base
UNION
SELECT CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as schema_name
         ,CASE WHEN COUNT(*) = 0 THEN 'EMPTY' END as table_name
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as table_overhead
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as column_size
         ,CASE WHEN COUNT(*) = 0 THEN 0 END as short_string_size

,CASE WHEN COUNT(*) = 0 THEN 0 END as long_string_size
FROM   base
)
, load_multiplier as
(
SELECT  CASE
          WHEN FLOOR(8 * (CAST (CAST(REPLACE(REPLACE(@DWU,'DW',''),'c','') AS INT) AS FLOAT)/6000)) > 0
            AND CHARINDEX(@DWU,'c')=0
          THEN FLOOR(8 * (CAST (CAST(REPLACE(REPLACE(@DWU,'DW',''),'c','') AS INT) AS FLOAT)/6000))
          ELSE 1
        END AS multiplication_factor
)
       SELECT  r1.DWU
       , schema_name
       , table_name
       , rc.resource_class as closest_rc_in_increasing_order
       , max_queries_at_this_rc = CASE
             WHEN (r1.max_slots / r1.slots_used > r1.max_queries)
                  THEN r1.max_queries
             ELSE r1.max_slots / r1.slots_used
                  END
       , r1.max_slots as max_concurrency_slots
       , r1.slots_used as required_slots_for_the_rc
       , r1.tgt_mem_grant_MB  as rc_mem_grant_MB
       , CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) AS est_mem_grant_required_for_cci_operation_MB
       FROM    size
       , load_multiplier
       , #ref r1, names  rc
       WHERE r1.rc_id=rc.rc_id
                     AND CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) < r1.tgt_mem_grant_MB
       ORDER BY ABS(CAST((table_overhead*1.0+column_size+short_string_size+long_string_size)*multiplication_factor/1048576    AS DECIMAL(18,2)) - r1.tgt_mem_grant_MB)
GO
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du hanterar databas användare och säkerhet finns i [skydda en databas i SYNAPSE SQL](sql-data-warehouse-overview-manage-security.md). Mer information om hur större resurs klasser kan förbättra grupperade columnstore-index kvalitet finns i [minnes optimeringar för columnstore-komprimering](sql-data-warehouse-memory-optimizations-for-columnstore-compression.md).
