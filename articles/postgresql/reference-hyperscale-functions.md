---
title: SQL Functions – storskalig (citus) – Azure Database for PostgreSQL
description: Funktioner i den storskaliga (citus) SQL API
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: f324ef44d002f50bf27c08072e904c1d92b5512f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95026241"
---
# <a name="functions-in-the-hyperscale-citus-sql-api"></a>Funktioner i den storskaliga (citus) SQL API

Det här avsnittet innehåller referensinformation för användardefinierade funktioner som tillhandahålls av skalning (citus). Dessa funktioner bidrar till att ge ytterligare distribuerade funktioner till citus (storskalig) förutom standard-SQL-kommandona.

> [!NOTE]
>
> Citus-Server grupper som kör äldre versioner av citus-motorn kan inte erbjuda alla funktioner som anges nedan.

## <a name="table-and-shard-ddl"></a>Tabell-och Shard DDL

### <a name="create_distributed_table"></a>skapa \_ distribuerad \_ tabell

Funktionen Create \_ Distributed \_ Table () används för att definiera en distribuerad tabell och skapa dess Shards om det är en hash-distribuerad tabell. Den här funktionen tar i tabell namn, distributions kolumn och en valfri distributions metod och infogar lämpliga metadata för att markera tabellen som distribuerad. Funktionen använder standardinställningen "hash" om ingen distributions metod har angetts. Om tabellen är hash-distribuerad skapar funktionen även Worker-Shards baserat på konfigurations värden för Shard antal och Shard. Om tabellen innehåller rader, distribueras de automatiskt till arbetsnoder.

Den här funktionen ersätter användningen av Master \_ create \_ Distributed \_ Table () följt av Master \_ create \_ Worker \_ Shards ().

#### <a name="arguments"></a>Argument

**tabell \_ Namn:** namnet på den tabell som måste distribueras.

**distributions \_ kolumn:** kolumnen som tabellen ska distribueras till.

**distributions \_ Typ:** (valfritt) metoden enligt vilken tabellen ska distribueras. Tillåtna värden är append eller hash med standardvärdet hash.

**samplacera \_ med:** (valfritt) inkludera den aktuella tabellen i den samplacerings gruppen för en annan tabell. Som standard är tabeller samplacerade när de distribueras av kolumner av samma typ, har samma Shard antal och har samma replikeringsrelation. Möjliga värden för `colocate_with` är `default` , `none` för att starta en ny grupp för samplacering, eller namnet på en annan tabell som ska befinna sig i tabellen.  (Se [tabell-samplacering](concepts-hyperscale-colocation.md).)

Tänk på att standardvärdet för är `colocate_with` implicit samplacering. [Samplacering](concepts-hyperscale-colocation.md) kan vara bra när tabellerna är relaterade eller kommer att anslutas.  Men när två tabeller inte är relaterade, men som ska använda samma data typ för sina distributions kolumner, kan det hända att de minskar prestandan vid [Shard ombalansering](howto-hyperscale-scale-rebalance.md).  Tabellen Shards kommer att flyttas samman i onödan i en \" överlappande.\"

Om en ny distribuerad tabell inte är relaterad till andra tabeller, är det bäst att ange `colocate_with => 'none'` .

#### <a name="return-value"></a>Returvärde

Ej tillämpligt

#### <a name="example"></a>Exempel

Det här exemplet informerar databasen om att GitHub \_ Events-tabellen ska distribueras av hash i \_ kolumnen lagrings platsen ID.

```postgresql
SELECT create_distributed_table('github_events', 'repo_id');

-- alternatively, to be more explicit:
SELECT create_distributed_table('github_events', 'repo_id',
                                colocate_with => 'github_repo');
```

### <a name="create_reference_table"></a>skapa \_ referens \_ tabell

Funktionen Create \_ Reference \_ Table () används för att definiera en liten referens eller dimensions tabell. Den här funktionen tar i ett tabell namn och skapar en distribuerad tabell med bara en Shard som replikeras till varje arbetsnoden.

#### <a name="arguments"></a>Argument

**tabell \_ Namn:** namnet på den lilla dimensions-eller referens tabell som måste distribueras.

#### <a name="return-value"></a>Returvärde

Ej tillämpligt

#### <a name="example"></a>Exempel

I det här exemplet informerar databasen att tabellen nation ska definieras som en referens tabell

```postgresql
SELECT create_reference_table('nation');
```

### <a name="upgrade_to_reference_table"></a>uppgradera \_ till \_ referens \_ tabell

Funktionen Upgrade \_ to \_ Reference \_ Table () använder en befintlig distribuerad tabell som har ett Shard antal och uppgraderar den till en känd referens tabell. När den här funktionen har anropats blir tabellen som om den hade skapats med [create_reference_table](#create_reference_table).

#### <a name="arguments"></a>Argument

**tabell \_ Namn:** namnet på den distribuerade tabellen (med Shard count = 1) som ska distribueras som en referens tabell.

#### <a name="return-value"></a>Returvärde

Ej tillämpligt

#### <a name="example"></a>Exempel

I det här exemplet informerar databasen att tabellen nation ska definieras som en referens tabell

```postgresql
SELECT upgrade_to_reference_table('nation');
```

### <a name="mark_tables_colocated"></a>Markera \_ tabeller som \_ samplacerade

Funktionen markerings \_ tabeller ( \_ ) tar en distribuerad tabell (källan) och en lista över andra (mål) och placerar målen i samma samplacerings grupp som källan. Om källan inte finns i en grupp, skapar den här funktionen en och tilldelar källan och mål gruppen till den.

Samplacering av tabeller bör göras vid tabell distributions tiden via `colocate_with` parametern för [create_distributed_table](#create_distributed_table), men `mark_tables_colocated` kan ta hand om det senare om det behövs.

#### <a name="arguments"></a>Argument

**käll \_ tabell \_ Namn:** namnet på den distribuerade tabellen vars samplacerings grupp målen ska tilldelas till varandra.

**mål \_ tabell \_ Namn:** matris med namn för de distribuerade mål tabellerna får inte vara tom. Dessa distribuerade tabeller måste matcha käll tabellen i:

> -   distributions metod
> -   typ av distributions kolumn
> -   typ av replikering
> -   antal Shard

Om inget av ovanstående tillämpas, kommer skalningen (citus) att generera ett fel. Försök till exempel att samplacera tabeller `apples` och `oranges` vilka distributions kolumn typer skiljer sig från:

```
ERROR:  XX000: cannot colocate tables apples and oranges
DETAIL:  Distribution column types don't match for apples and oranges.
```

#### <a name="return-value"></a>Returvärde

Ej tillämpligt

#### <a name="example"></a>Exempel

I det här exemplet placeras `products` och `line_items` i samma samplacerings grupp som `stores` . Exemplet förutsätter att dessa tabeller är alla distribuerade i en kolumn med matchande typ, troligen ett \" butiks-ID.\"

```postgresql
SELECT mark_tables_colocated('stores', ARRAY['products', 'line_items']);
```

### <a name="create_distributed_function"></a>skapa \_ distribuerad \_ funktion

Sprider en funktion från koordinator-noden till arbetare och markerar den för distribuerad körning. När en distribuerad funktion anropas i koordinatorn använder skalnings argumentet (citus) värdet för \" argumentet distribution \" för att välja en arbetsnod för att köra funktionen. Att köra funktionen på arbetare ökar parallellt och kan ta koden närmare data i Shards för kortare latens.

Sök vägen för postgres har inte spridits från koordinatorn till arbetare vid körning av distribuerad funktion, så den distribuerade funktions koden bör fullständigt kvalificera namnen på databas objekt. Meddelanden som skickas av funktionerna visas inte för användaren.

#### <a name="arguments"></a>Argument

**funktions \_ Namn:** namnet på den funktion som ska distribueras. Namnet måste innehålla funktionens parameter typer inom parentes, eftersom flera funktioner kan ha samma namn i PostgreSQL. Till exempel `'foo(int)'` skiljer sig från `'foo(int, text)'` .

**namn på distributions- \_ arg \_ :** (valfritt) argument namnet som ska distribueras. För enkelhetens skull (eller om funktions argumenten inte har namn), tillåts en plats hållare, till exempel `'$1'` . Om den här parametern inte anges skapas funktionen med namnet av `function_name` bara på arbetarna. Om arbetsnoder läggs till i framtiden kommer funktionen automatiskt att skapas där.

**samplacera \_ med:** (valfritt) när den distribuerade funktionen läser eller skriver till en distribuerad tabell (eller, mer generellt, samplacerings grupp), se till att namnge tabellen med hjälp av `colocate_with` parametern. Sedan kommer varje anrop av funktionen att köras på arbetsnoden som innehåller relevanta Shards.

#### <a name="return-value"></a>Returvärde

Ej tillämpligt

#### <a name="example"></a>Exempel

```postgresql
-- an example function which updates a hypothetical
-- event_responses table which itself is distributed by event_id
CREATE OR REPLACE FUNCTION
  register_for_event(p_event_id int, p_user_id int)
RETURNS void LANGUAGE plpgsql AS $fn$
BEGIN
  INSERT INTO event_responses VALUES ($1, $2, 'yes')
  ON CONFLICT (event_id, user_id)
  DO UPDATE SET response = EXCLUDED.response;
END;
$fn$;

-- distribute the function to workers, using the p_event_id argument
-- to determine which shard each invocation affects, and explicitly
-- colocating with event_responses which the function updates
SELECT create_distributed_function(
  'register_for_event(int, int)', 'p_event_id',
  colocate_with := 'event_responses'
);
```

## <a name="metadata--configuration-information"></a>Metadata/konfigurations information

### <a name="master_get_table_metadata"></a>huvud \_ Hämta \_ metadata för tabell \_

Funktionen Master \_ get \_ Table \_ metadata () kan användas för att returnera distributions-relaterade metadata för en distribuerad tabell. Dessa metadata inkluderar relations-ID, lagrings typ, distributions metod, distributions kolumn, antal replikeringar, maximal Shard storlek och Shard placerings princip för tabellen. Den här funktionen frågar i citus-metadata tabeller för att hämta nödvändig information och sammanfogar den till en tupel innan den returneras till användaren.

#### <a name="arguments"></a>Argument

**tabell \_ Namn:** namnet på den distribuerade tabell som du vill hämta metadata för.

#### <a name="return-value"></a>Returvärde

En tupel som innehåller följande information:

**logisk \_ relid:** OID för den distribuerade tabellen. Den refererar till kolumnen relfilenode i \_ system katalog tabellen för PG-klassen.

**del \_ lagrings \_ Typ:** typen av lagring som används för tabellen. Kan vara "(standard tabell)," f "(sekundär tabell) eller" c "(kolumn tabell).

**del \_ metod:** den distributions metod som används för tabellen. Kan vara "a" (append) eller "h" (hash).

**del \_ nyckel:** distributions kolumn för tabellen.

**\_ antal del repliker \_ :** Aktuellt antal Shard-replikeringar.

**\_ Max \_ storlek för del:** aktuell maximal Shard storlek i byte.

**princip för del \_ placering \_ :** Shard placerings princip som används för att placera tabellens Shards. Kan vara 1 (lokal-nod-First) eller 2 (Round-Robin).

#### <a name="example"></a>Exempel

I exemplet nedan hämtas och visas tabellens metadata för GitHub \_ Events-tabellen.

```postgresql
SELECT * from master_get_table_metadata('github_events');
 logical_relid | part_storage_type | part_method | part_key | part_replica_count | part_max_size | part_placement_policy 
---------------+-------------------+-------------+----------+--------------------+---------------+-----------------------
         24180 | t                 | h           | repo_id  |                  2 |    1073741824 |                     2
(1 row)
```

### <a name="get_shard_id_for_distribution_column"></a>Hämta \_ Shard \_ -ID \_ för \_ distributions \_ kolumn

Med citus) tilldelas alla rader i en distribuerad tabell till en Shard baserat på värdet för radens distributions kolumn och tabellens metod för distribution. I de flesta fall är den exakta mappningen en låg nivå information som databas administratören kan ignorera. Det kan dock vara användbart att fastställa en rads Shard, antingen för manuella databas underhålls aktiviteter eller bara för att uppfylla Curiosity. `get_shard_id_for_distribution_column`Funktionen tillhandahåller den här informationen för hash-och Range-distribuerade tabeller samt referens tabeller. Den fungerar inte för distribution av tillägg.

#### <a name="arguments"></a>Argument

**tabell \_ Namn:** den distribuerade tabellen.

**distributions \_ värde:** värdet för distributions kolumnen.

#### <a name="return-value"></a>Returvärde

Shard-ID-citus () associeras med värdet för den aktuella tabellens distributions kolumn.

#### <a name="example"></a>Exempel

```postgresql
SELECT get_shard_id_for_distribution_column('my_table', 4);

 get_shard_id_for_distribution_column
--------------------------------------
                               540007
(1 row)
```

### <a name="column_to_column_name"></a>kolumn \_ till \_ kolumn \_ namn

Översätter `partkey` kolumnen i `pg_dist_partition` till ett text kolumn namn. Översättningen är användbar för att fastställa distributions kolumnen i en distribuerad tabell.

En mer detaljerad diskussion finns i [välja en distributions kolumn](concepts-hyperscale-choose-distribution-column.md).

#### <a name="arguments"></a>Argument

**tabell \_ Namn:** den distribuerade tabellen.

**kolumn \_ var \_ text:** värdet för `partkey` i `pg_dist_partition` tabellen.

#### <a name="return-value"></a>Returvärde

Namnet på `table_name` distributions kolumnen.

#### <a name="example"></a>Exempel

```postgresql
-- get distribution column name for products table

SELECT column_to_column_name(logicalrelid, partkey) AS dist_col_name
  FROM pg_dist_partition
 WHERE logicalrelid='products'::regclass;
```

Utdata:

```
┌───────────────┐
│ dist_col_name │
├───────────────┤
│ company_id    │
└───────────────┘
```

### <a name="citus_relation_size"></a>citus \_ Relations \_ storlek

Hämta det disk utrymme som används av alla Shards för den angivna distribuerade tabellen.
Disk utrymmet inkluderar storleken på \" huvud delen, \" men utesluter Synlighets kartan och karta över ledigt utrymme för Shards.

#### <a name="arguments"></a>Argument

**logicalrelid:** namnet på en distribuerad tabell.

#### <a name="return-value"></a>Returvärde

Storlek i byte som bigint.

#### <a name="example"></a>Exempel

```postgresql
SELECT pg_size_pretty(citus_relation_size('github_events'));
```

```
pg_size_pretty
--------------
23 MB
```

### <a name="citus_table_size"></a>citus \_ tabell \_ storlek

Hämta det disk utrymme som används av alla Shards för den angivna distribuerade tabellen, exklusive index (men inklusive popup, karta över ledigt utrymme och Synlighets karta).

#### <a name="arguments"></a>Argument

**logicalrelid:** namnet på en distribuerad tabell.

#### <a name="return-value"></a>Returvärde

Storlek i byte som bigint.

#### <a name="example"></a>Exempel

```postgresql
SELECT pg_size_pretty(citus_table_size('github_events'));
```

```
pg_size_pretty
--------------
37 MB
```

### <a name="citus_total_relation_size"></a>citus \_ total \_ Relations \_ storlek

Hämta det totala disk utrymme som används av alla Shards för den angivna distribuerade tabellen, inklusive alla index och popup-data.

#### <a name="arguments"></a>Argument

**logicalrelid:** namnet på en distribuerad tabell.

#### <a name="return-value"></a>Returvärde

Storlek i byte som bigint.

#### <a name="example"></a>Exempel

```postgresql
SELECT pg_size_pretty(citus_total_relation_size('github_events'));
```

```
pg_size_pretty
--------------
73 MB
```

### <a name="citus_stat_statements_reset"></a>återställning av citus \_ stat- \_ uttryck \_

Tar bort alla rader från [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table).
Den här funktionen fungerar oberoende av `pg_stat_statements_reset()` . Om du vill återställa all statistik anropar du båda funktionerna.

#### <a name="arguments"></a>Argument

Ej tillämpligt

#### <a name="return-value"></a>Returvärde

Inget

## <a name="server-group-management-and-repair"></a>Hantering och reparation av Server grupper

### <a name="master_copy_shard_placement"></a>placering av Master \_ copy- \_ Shard \_

Om en Shard placering inte kan uppdateras under ett ändrings kommando eller en DDL-åtgärd, markeras den som inaktiv. \_ \_ Shard \_ placerings funktion för huvud kopiering kan sedan anropas för att reparera en inaktiv Shard-placering med hjälp av data från en felplacerad placering.

För att reparera en Shard släpper funktionen först den felaktiga Shard-placeringen och återskapar den med hjälp av schemat på koordinatorn. När Shard-placeringen har skapats kopierar funktionen data från den felfria placeringen och uppdaterar metadata för att markera den nya Shard-placeringen som felfri. Den här funktionen säkerställer att Shard kommer att skyddas från alla samtidiga ändringar under reparationen.

#### <a name="arguments"></a>Argument

**Shard \_ -ID:** ID för det Shard som ska repare ras.

**nodnamn \_ \_ :** DNS-namnet på den nod där den felfria Shard finns ( \" källnod \" ).

**källport \_ \_ :** porten på käll arbets noden som databas servern lyssnar på.

**nodnamn \_ \_ :** DNS-namnet på den nod där den ogiltiga Shard-placeringen finns ( \" målnod \" ).

**målport \_ \_ :** porten på mål arbets noden som databas servern lyssnar på.

#### <a name="return-value"></a>Returvärde

Ej tillämpligt

#### <a name="example"></a>Exempel

Exemplet nedan reparerar en inaktiv Shard-placering på Shard 12345, som finns på databas servern som körs på den felaktiga \_ värden på port 5432. För att reparera den, kommer den att använda data från en felfri Shard-placering på servern som körs på den "fungerande \_ värden" på porten
5432.

```postgresql
SELECT master_copy_shard_placement(12345, 'good_host', 5432, 'bad_host', 5432);
```

### <a name="master_move_shard_placement"></a>\_ \_ Shard \_ placering för huvud flyttning

Den här funktionen flyttar en specifik Shard (och Shards som samplaceras med den) från en nod till en annan. Den används vanligt vis indirekt under Shard ombalansering i stället för att anropas direkt av en databas administratör.

Det finns två sätt att flytta data: blockera eller blockera. Den blockerande metoden innebär att under flyttningen av alla ändringar av Shard har pausats.
Det andra sättet, som förhindrar blockering av Shard-skrivningar, förlitar sig på postgres 10 logisk replikering.

Efter en lyckad flytt åtgärd tas Shards i Källnoden bort. Om flyttningen Miss lyckas när som helst genererar den här funktionen ett fel och låter käll-och mål noderna vara oförändrade.

#### <a name="arguments"></a>Argument

**Shard \_ -ID:** ID för Shard som ska flyttas.

**nodnamn \_ \_ :** DNS-namnet på den nod där den felfria Shard finns ( \" källnod \" ).

**källport \_ \_ :** porten på käll arbets noden som databas servern lyssnar på.

**nodnamn \_ \_ :** DNS-namnet på den nod där den ogiltiga Shard-placeringen finns ( \" målnod \" ).

**målport \_ \_ :** porten på mål arbets noden som databas servern lyssnar på.

**Shard- \_ överförings \_ läge:** (valfritt) Ange metoden för replikering, om du vill använda postgresql logiska replikering eller ett kopierings kommando för olika arbets tagare. Möjliga värden är:

> -   `auto`: Kräv replik identitet om logisk replikering är möjlig, Använd annars tidigare beteende (t. ex. Shard Repair, PostgreSQL 9,6). Detta är standardvärdet.
> -   `force_logical`: Använd logisk replikering även om tabellen inte har en replik identitet. Alla samtidiga uppdaterings-och borttagnings instruktioner till tabellen kommer inte att fungera under replikeringen.
> -   `block_writes`: Använd kopiering (blockerar skrivningar) för tabeller som saknar primär nyckel eller replik identitet.

#### <a name="return-value"></a>Returvärde

Ej tillämpligt

#### <a name="example"></a>Exempel

```postgresql
SELECT master_move_shard_placement(12345, 'from_host', 5432, 'to_host', 5432);
```

### <a name="rebalance_table_shards"></a>Shards för ombalansering av \_ tabell \_

\_ \_ Funktionen Shards () för att balansera tabeller flyttar Shards för den aktuella tabellen så att de blir jämnt fördelade mellan arbets tagarna. Funktionen beräknar först listan över flyttningar som måste göras för att säkerställa att Server gruppen fördelas inom det angivna tröskelvärdet. Sedan flyttar den Shard placeringar en i taget från Källnoden till målnoden och uppdaterar motsvarande Shard-metadata för att återspegla flytten.

Varje Shard tilldelas en kostnad när man bestämmer om Shards är \" jämnt distribuerat. \" Som standard har varje Shard samma kostnad (värdet 1), så distribution för att jämna ut kostnaden för arbets tagarna är detsamma som att lika med antalet Shards för varje. Strategin för konstant kostnad anropas \" av \_ Shard \_ antal \" och är standard strategin för ombalansering.

Standard strategin är lämplig under följande omständigheter:

*  Shards har ungefär samma storlek
*  Shards får ungefär samma mängd trafik
*  Arbetsnoder har samma storlek/typ
*  Shards har inte fästs på vissa arbetare

Om något av dessa antaganden inte kvarhålls, kan standard ombalanseringen resultera i en dålig plan. I det här fallet kan du anpassa strategin med hjälp av- `rebalance_strategy` parametern.

Vi rekommenderar att du anropar [get_rebalance_table_shards_plan](#get_rebalance_table_shards_plan) innan du kör ombalansing \_ Table \_ Shards för att se och kontrol lera vilka åtgärder som ska utföras.

#### <a name="arguments"></a>Argument

**tabell \_ Namn:** (valfritt) namnet på den tabell vars Shards måste ombalanseras. Om värdet är NULL balanserar du om alla befintliga samplacerings grupper.

**tröskel:** (valfritt) ett flytt ALS nummer mellan 0,0 och 1,0 som anger den största skillnaden i användningen av användning från genomsnittlig användning. Om du till exempel anger 0,1 kommer Shard-ombalanseringen att försöka utjämna alla noder så att de rymmer samma antal Shards ± 10%.
Mer specifikt försöker Shard-ombalanseringen att konvergera användningen av alla arbetsnoder till \* genomsnitts förbrukningen (1-tröskel) \_ \. . (1
+ tröskel) \* Genomsnittligt \_ användnings intervall.

**Max \_ Shard- \_ flyttningar:** (valfritt) det maximala antalet Shards som ska flyttas.

**undantagen \_ Shard \_ lista:** (valfritt) identifierare av Shards som inte ska flyttas under återbalanserings åtgärden.

**Shard- \_ överförings \_ läge:** (valfritt) Ange metoden för replikering, om du vill använda postgresql logiska replikering eller ett kopierings kommando för olika arbets tagare. Möjliga värden är:

> -   `auto`: Kräv replik identitet om logisk replikering är möjlig, Använd annars tidigare beteende (t. ex. Shard Repair, PostgreSQL 9,6). Detta är standardvärdet.
> -   `force_logical`: Använd logisk replikering även om tabellen inte har en replik identitet. Alla samtidiga uppdaterings-och borttagnings instruktioner till tabellen kommer inte att fungera under replikeringen.
> -   `block_writes`: Använd kopiering (blockerar skrivningar) för tabeller som saknar primär nyckel eller replik identitet.

**Töm \_ endast:** (valfritt) om värdet är sant flyttas Shards av arbetsnoder som har `shouldhaveshards` angetts till falskt i [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table). flytta inga andra Shards.

**ombalans \_ strategi:** (valfritt) namnet på en strategi i [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table).
Om argumentet utelämnas väljer funktionen standard strategin som anges i tabellen.

#### <a name="return-value"></a>Returvärde

Ej tillämpligt

#### <a name="example"></a>Exempel

I exemplet nedan görs ett försök att balansera om Shards för tabellen GitHub \_ Events inom standard tröskelvärdet.

```postgresql
SELECT rebalance_table_shards('github_events');
```

I den här exempel användningen görs ett försök att balansera om GitHub \_ Events-tabellen utan att flytta Shards med ID 1 och 2.

```postgresql
SELECT rebalance_table_shards('github_events', excluded_shard_list:='{1,2}');
```

### <a name="get_rebalance_table_shards_plan"></a>Hämta \_ Shards- \_ \_ \_ plan för ombalansering av tabell

Spara de planerade Shard-förflyttningarna av [rebalance_table_shards](#rebalance_table_shards) utan att utföra dem.
Även om det är osannolikt \_ kan en Shards-plan för ombalansering returnera \_ \_ \_ en något annorlunda plan än vad en ombalans \_ tabell \_ Shards anropar med samma argument. De utförs inte samtidigt, så fakta om Server gruppen, \- till exempel disk utrymme, \- kan skilja sig åt mellan anropen.

#### <a name="arguments"></a>Argument

Samma argument som \_ \_ Shards: relation, tröskel, Max \_ Shard \_ flyttar, Exkluderad \_ Shard \_ -lista och \_ endast dränering. Se dokumentationen för funktionen för argumenten.

#### <a name="return-value"></a>Returvärde

Tupel som innehåller följande kolumner:

-   **tabell \_ namn**: den tabell vars Shards skulle flyttas
-   **shardid**: Shard i fråga
-   **Shard \_ storlek**: storlek i byte
-   **SourceName**: hostname för Källnoden
-   **sourceport**: källport för Källnoden
-   **målnamn**: värdnamn för målnoden
-   **targetport**: porten för målnoden

### <a name="get_rebalance_progress"></a>Hämta \_ ombalanserings \_ förlopp

När en Shard balansering börjar `get_rebalance_progress()` visas en lista över förloppet för varje Shard som berörs. Den övervakar flytten och genomförd av `rebalance_table_shards()` .

#### <a name="arguments"></a>Argument

Ej tillämpligt

#### <a name="return-value"></a>Returvärde

Tupel som innehåller följande kolumner:

-   **SessionID**: postgres PID för den ombalanserande övervakaren
-   **tabell \_ namn**: den tabell vars Shards flyttas
-   **shardid**: Shard i fråga
-   **Shard \_ storlek**: storlek i byte
-   **SourceName**: hostname för Källnoden
-   **sourceport**: källport för Källnoden
-   **målnamn**: värdnamn för målnoden
-   **targetport**: porten för målnoden
-   **förlopp**: 0 = väntar på att flyttas. 1 = flytta; 2 = Slutför

#### <a name="example"></a>Exempel

```sql
SELECT * FROM get_rebalance_progress();
```

```
┌───────────┬────────────┬─────────┬────────────┬───────────────┬────────────┬───────────────┬────────────┬──────────┐
│ sessionid │ table_name │ shardid │ shard_size │  sourcename   │ sourceport │  targetname   │ targetport │ progress │
├───────────┼────────────┼─────────┼────────────┼───────────────┼────────────┼───────────────┼────────────┼──────────┤
│      7083 │ foo        │  102008 │    1204224 │ n1.foobar.com │       5432 │ n4.foobar.com │       5432 │        0 │
│      7083 │ foo        │  102009 │    1802240 │ n1.foobar.com │       5432 │ n4.foobar.com │       5432 │        0 │
│      7083 │ foo        │  102018 │     614400 │ n2.foobar.com │       5432 │ n4.foobar.com │       5432 │        1 │
│      7083 │ foo        │  102019 │       8192 │ n3.foobar.com │       5432 │ n4.foobar.com │       5432 │        2 │
└───────────┴────────────┴─────────┴────────────┴───────────────┴────────────┴───────────────┴────────────┴──────────┘
```

### <a name="citus_add_rebalance_strategy"></a>citus \_ Lägg till \_ ombalans \_ strategi

Lägg till en rad i [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md?#rebalancer-strategy-table) .

#### <a name="arguments"></a>Argument

Mer information om dessa argument finns i motsvarande kolumn värden i `pg_dist_rebalance_strategy` .

**Namn:** identifierare för den nya strategin

**Shard \_ Cost- \_ funktion:** identifierar den funktion som används för att fastställa \" kostnaden \" för varje Shard

**\_ funktionen nod kapacitet \_ :** identifierar funktionen för att mäta nodens kapacitet

**Shard \_ tillåts \_ i \_ Node- \_ funktionen:** identifierar den funktion som avgör vilka Shards som kan placeras på vilka noder

**standard \_ tröskel:** ett flytt ALS tröskelvärde som justerar hur exakt den ackumulerade Shard-kostnaden ska bal anse ras mellan noderna

**minsta \_ tröskelvärde:** (valfritt) en skydds kolumn som innehåller det lägsta tillåtna värdet för tröskelvärdet för ombalansering av \_ tabellens \_ Shards (). Standardvärdet är 0

#### <a name="return-value"></a>Returvärde

Ej tillämpligt

### <a name="citus_set_default_rebalance_strategy"></a>citus \_ ange \_ standard \_ strategi för ombalansering \_

Uppdatera [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table) tabellen, ändra strategin som heter med dess argument som standard när du ombalanserar Shards.

#### <a name="arguments"></a>Argument

**Namn:** namnet på strategin i den \_ \_ ombalansa strategin för PG-dist. \_

#### <a name="return-value"></a>Returvärde

Ej tillämpligt

#### <a name="example"></a>Exempel

```postgresql
SELECT citus_set_default_rebalance_strategy('by_disk_size');
```

### <a name="citus_remote_connection_stats"></a>citus \_ fjärr \_ anslutnings \_ statistik

Funktionen citus \_ fjärr \_ anslutning \_ statistik () visar antalet aktiva anslutningar till varje fjärrnod.

#### <a name="arguments"></a>Argument

Ej tillämpligt

#### <a name="example"></a>Exempel

```postgresql
SELECT * from citus_remote_connection_stats();
```

```
    hostname    | port | database_name | connection_count_to_node
----------------+------+---------------+--------------------------
 citus_worker_1 | 5432 | postgres      |                        3
(1 row)
```

### <a name="master_drain_node"></a>huvud \_ tömnings \_ nod

Funktionen Master \_ dränering \_ Node () flyttar Shards av den angivna noden och till andra noder som har `shouldhaveshards` angetts till true i [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table). Anropa funktionen innan du tar bort en nod från Server gruppen och stänger av nodens fysiska server.

#### <a name="arguments"></a>Argument

**nodnamn:** Hostname-namnet för noden som ska tömmas.

**nodeport:** Port numret för noden som ska tömmas.

**Shard- \_ överförings \_ läge:** (valfritt) Ange metoden för replikering, om du vill använda postgresql logiska replikering eller ett kopierings kommando för olika arbets tagare. Möjliga värden är:

> -   `auto`: Kräv replik identitet om logisk replikering är möjlig, Använd annars tidigare beteende (t. ex. Shard Repair, PostgreSQL 9,6). Detta är standardvärdet.
> -   `force_logical`: Använd logisk replikering även om tabellen inte har en replik identitet. Alla samtidiga uppdaterings-och borttagnings instruktioner till tabellen kommer inte att fungera under replikeringen.
> -   `block_writes`: Använd kopiering (blockerar skrivningar) för tabeller som saknar primär nyckel eller replik identitet.

**ombalans \_ strategi:** (valfritt) namnet på en strategi i [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md#rebalancer-strategy-table).
Om argumentet utelämnas väljer funktionen standard strategin som anges i tabellen.

#### <a name="return-value"></a>Returvärde

Ej tillämpligt

#### <a name="example"></a>Exempel

Här är de vanligaste stegen för att ta bort en nod (till exempel 10.0.0.1 på en standard port för PostgreSQL):

1.  Töm noden.

    ```postgresql
    SELECT * from master_drain_node('10.0.0.1', 5432);
    ```

2.  Vänta tills kommandot har slutförts

3.  Ta bort noden

När du tömmer flera noder rekommenderar vi att du använder [rebalance_table_shards](#rebalance_table_shards) i stället. Om du gör det kan citus (Scale Scale) planera framåt och flytta Shards det minsta antalet gånger.

1.  Kör för varje nod som du vill ta bort:

    ```postgresql
    SELECT * FROM master_set_node_property(node_hostname, node_port, 'shouldhaveshards', false);
    ```

2.  Töm alla samtidigt med [rebalance_table_shards](#rebalance_table_shards)

    ```postgresql
    SELECT * FROM rebalance_table_shards(drain_only := true);
    ```

3.  Vänta tills avbalanseringen har slutförts

4.  Ta bort noderna

### <a name="replicate_table_shards"></a>replikera \_ tabell \_ Shards

\_Funktionen replikera tabell \_ Shards () replikerar den replikerade Shards för den aktuella tabellen. Funktionen beräknar först listan över replikerade Shards och platser som de kan hämtas från för replikering. Funktionen kopierar sedan över dessa Shards och uppdaterar motsvarande Shard-metadata för att avspegla kopian.

#### <a name="arguments"></a>Argument

**tabell \_ Namn:** namnet på den tabell vars Shards måste replikeras.

**Shard \_ \_ :** (valfritt) den önskade replikeringslänken som ska uppnås för varje Shard.

**Max \_ Shard \_ kopior:** (valfritt) maximalt antal Shards som ska kopieras för att uppnå den önskade replikeringslänken.

**undantagen \_ Shard \_ lista:** (valfritt) identifierare av Shards som inte ska kopieras under replikeringen.

#### <a name="return-value"></a>Returvärde

Ej tillämpligt

#### <a name="examples"></a>Exempel

I exemplet nedan görs ett försök att replikera Shards för GitHub \_ Events-tabellen till Shard \_ \_ .

```postgresql
SELECT replicate_table_shards('github_events');
```

I det här exemplet görs ett försök att hämta Shards för GitHub \_ Events-tabellen till den önskade replikeringslänken med högst 10 Shard kopior. Ombalanseringen kopierar högst 10 Shards i försöket att uppnå den önskade replikeringslänken.

```postgresql
SELECT replicate_table_shards('github_events', max_shard_copies:=10);
```

### <a name="isolate_tenant_to_new_shard"></a>isolera \_ klienten \_ till \_ nya \_ Shard

Den här funktionen skapar en ny Shard som innehåller rader med ett visst enskilt värde i kolumnen distribution. Det är särskilt användbart för användnings fall i citus (Multi-Tenant-storskalig), där en stor klient kan placeras separat på sin egen Shard och slutligen sin egen fysiska nod.

#### <a name="arguments"></a>Argument

**tabell \_ Namn:** namnet på tabellen för att hämta en ny Shard.

**klient \_ -ID:** värdet för den distributions kolumn som ska tilldelas den nya Shard.

**\_ alternativet kaskad:** (valfritt) när värdet är \" KASKAD, \" isolerar också en Shard från alla tabeller i den aktuella tabellens [samplacerings grupp](concepts-hyperscale-colocation.md).

#### <a name="return-value"></a>Returvärde

**Shard \_ -ID:** funktionen returnerar det unika ID som tilldelats den nyligen skapade Shard.

#### <a name="examples"></a>Exempel

Skapa en ny Shard som innehåller rad objekt för klient organisationen 135:

```postgresql
SELECT isolate_tenant_to_new_shard('lineitem', 135);
```

```
┌─────────────────────────────┐
│ isolate_tenant_to_new_shard │
├─────────────────────────────┤
│                      102240 │
└─────────────────────────────┘
```

## <a name="next-steps"></a>Nästa steg

* Många av funktionerna i den här artikeln ändra tabeller för system [metadata](reference-hyperscale-metadata.md)
* [Server parametrar](reference-hyperscale-parameters.md) anpassa beteendet för vissa funktioner
