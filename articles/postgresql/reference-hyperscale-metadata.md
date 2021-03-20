---
title: System tabeller – storskalig (citus) – Azure Database for PostgreSQL
description: Metadata för körning av distribuerad fråga
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 74403365fe48584fa5d1db0e349c9dfc3772d874
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97652864"
---
# <a name="system-tables-and-views"></a>System tabeller och vyer

Citus (storskalig) skapar och underhåller särskilda tabeller som innehåller information om distribuerade data i Server gruppen. Koordinator-noden läser dessa tabeller när du planerar hur du ska köra frågor på arbetsnoderna.

## <a name="coordinator-metadata"></a>Koordinera metadata

Med citus) delas varje distribuerad tabell upp i flera logiska Shards baserat på distributions kolumnen. Koordinatorn hanterar sedan metadata tabeller för att spåra statistik och information om hälsan och platsen för dessa Shards.

I det här avsnittet beskriver vi var och en av dessa metadata-tabeller och deras schema.
Du kan visa och fråga tabellerna med hjälp av SQL efter att du loggat in på koordinator-noden.

> [!NOTE]
>
> Citus-Server grupper som kör äldre versioner av citus-motorn kanske inte erbjuder alla tabeller som anges nedan.

### <a name="partition-table"></a>Partitionstabell

I \_ \_ partitionstabellen i PG-tabellen lagras metadata om vilka tabeller i databasen som distribueras. För varje distribuerad tabell lagras även information om distributions metoden och detaljerad information om distributions kolumnen.

| Namn         | Typ     | Beskrivning                                                                                                                                                                                                                                           |
|--------------|----------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| logicalrelid | regclass | Distribuerad tabell som den här raden motsvarar. Det här värdet refererar till kolumnen relfilenode i tabellen pg_class system katalog.                                                                                                                   |
| partmethod   | char     | Den metod som används för partitionering/distribution. Värdena i den här kolumnen som motsvarar olika distributions metoder är Lägg till: "a", hash: "h", referens tabell: "n"                                                                          |
| partkey      | text     | Detaljerad information om distributions kolumnen, inklusive kolumn nummer, typ och annan relevant information.                                                                                                                                      |
| colocationid | heltal  | Samplacerings grupp som den här tabellen tillhör. Tabeller i samma grupp tillåter samplacerade kopplingar och distribuerade sammanslagningar bland andra optimeringar. Det här värdet refererar till kolumnen colocationid i tabellen pg_dist_colocation.                      |
| repmodel     | char     | Den metod som används för datareplikering. Värdena i den här kolumnen som motsvarar olika metoder för replikering: citus-baserad replikering: "c", postgresql streaming replikering: ', genomförande i två faser (för referens tabeller): ' t ' |

```
SELECT * from pg_dist_partition;
 logicalrelid  | partmethod |                                                        partkey                                                         | colocationid | repmodel 
---------------+------------+------------------------------------------------------------------------------------------------------------------------+--------------+----------
 github_events | h          | {VAR :varno 1 :varattno 4 :vartype 20 :vartypmod -1 :varcollid 0 :varlevelsup 0 :varnoold 1 :varoattno 4 :location -1} |            2 | c
 (1 row)
```

### <a name="shard-table"></a>Shard-tabell

Tabellen PG \_ förd \_ Shard lagrar metadata om enskilda Shards i en tabell. Pg_dist_shard innehåller information om vilken Distributed Table Shards tillhör, och statistik om distributions kolumnen för Shards.
För Lägg till distribuerade tabeller motsvarar den här statistiken minsta/högsta värden för distributions kolumnen. För hash-distribuerade tabeller är de hash-token som tilldelats den Shard. Dessa statistik används för att rensa bort icke-relaterade Shards under SELECT-frågor.

| Namn          | Typ     | Beskrivning                                                                                                                                                                                  |
|---------------|----------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| logicalrelid  | regclass | Distribuerad tabell som den här raden motsvarar. Det här värdet refererar till kolumnen relfilenode i tabellen pg_class system katalog.                                                          |
| shardid       | bigint   | Globalt unik identifierare som tilldelats den här Shard.                                                                                                                                           |
| shardstorage  | char     | Typ av lagring som används för den här Shard. Olika lagrings typer beskrivs i tabellen nedan.                                                                                               |
| shardminvalue | text     | För Lägg till distribuerade tabeller, minsta värde för distributions kolumnen i denna Shard (inklusive). För hash-distribuerade tabeller är lägsta hash-token kopplat till den Shard (inklusive). |
| shardmaxvalue | text     | För Lägg till distribuerade tabeller är det maximala värdet för distributions kolumnen i denna Shard (inklusive). För hash-distribuerade tabeller är det högsta värdet för hash-token som tilldelats den Shard (inklusive). |

```
SELECT * from pg_dist_shard;
 logicalrelid  | shardid | shardstorage | shardminvalue | shardmaxvalue 
---------------+---------+--------------+---------------+---------------
 github_events |  102026 | t            | 268435456     | 402653183
 github_events |  102027 | t            | 402653184     | 536870911
 github_events |  102028 | t            | 536870912     | 671088639
 github_events |  102029 | t            | 671088640     | 805306367
 (4 rows)
```

#### <a name="shard-storage-types"></a>Shard lagrings typer

Kolumnen shardstorage i PG \_ förd \_ Shard anger vilken typ av lagring som används för Shard. En kort översikt över olika Shard lagrings typer och deras representation är nedan.

| Lagringstyp | Shardstorage-värde | Beskrivning                                                                        |
|--------------|--------------------|------------------------------------------------------------------------------------|
| PARTITIONSTABELL        | inte                | Anger att Shard lagrar data som tillhör en vanlig distribuerad tabell.         |
| KOLUMNBASERAD     | c                | Anger att Shard lagrar kolumn data. (Används av distribuerade cstore_fdw-tabeller) |
| UTLÄNDSK      | b                | Anger att Shard lagrar externa data. (Används av distribuerade file_fdw-tabeller)    |

### <a name="shard-placement-table"></a>Shard placerings tabell

\_ \_ Placerings tabellen för PG-dist spårar platsen för Shard-repliker på arbetsnoder. Varje replik av en Shard som har tilldelats till en speciell nod kallas för en Shard-placering. Den här tabellen innehåller information om hälsan och platsen för varje Shard placering.

| Namn        | Typ   | Beskrivning                                                                                                                               |
|-------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------|
| shardid     | bigint | Shard-ID som är kopplat till den här placeringen. Det här värdet refererar till kolumnen shardid i tabellen pg_dist_shard katalog.             |
| shardstate  | int    | Beskriver den här placeringens tillstånd. Olika Shard-tillstånd beskrivs i avsnittet nedan.                                         |
| shardlength | bigint | För Lägg till distribuerade tabeller, storleken på Shard-placeringen på arbetsnoden i byte. För hash-distribuerade tabeller, noll.            |
| placementid | bigint | Unikt automatiskt genererad identifierare för varje enskild placering.                                                                           |
| GroupID     | int    | Anger en grupp med en primär server och noll eller flera sekundära servrar när modellen för strömning av data används. |

```
SELECT * from pg_dist_placement;
  shardid | shardstate | shardlength | placementid | groupid
 ---------+------------+-------------+-------------+---------
   102008 |          1 |           0 |           1 |       1
   102008 |          1 |           0 |           2 |       2
   102009 |          1 |           0 |           3 |       2
   102009 |          1 |           0 |           4 |       3
   102010 |          1 |           0 |           5 |       3
   102010 |          1 |           0 |           6 |       4
   102011 |          1 |           0 |           7 |       4
```

#### <a name="shard-placement-states"></a>Shard placerings tillstånd

Citus (storskalig) hanterar Shard-hälsa per placering. Om en placering placerar systemet i ett inkonsekvent tillstånd markeras citus automatiskt som otillgängligt. Placerings status registreras i pg_dist_shard_placement tabellen i kolumnen shardstate. Här är en kort översikt över olika Shard placerings tillstånd:

| Tillstånds namn | Shardstate-värde | Beskrivning                                                                                                                                                                                                                                                                                                                                                                                                                         |
|------------|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| SLUTFÖRA  | 1                | Den nya Shards skapas i. Shard placeringar i det här läget betraktas som aktuella och används vid planering och körning av frågor.                                                                                                                                                                                                                                                                                 |
| INAKTIVERA   | 3                | Shard placeringar i det här läget anses vara inaktiva på grund av att de inte är synkroniserade med andra repliker av samma Shard. Status kan inträffa när det inte går att lägga till, ändra (Infoga, uppdatera eller ta bort) eller en DDL-åtgärd för den här placeringen. Query Planner kommer att ignorera platser i det här läget under planering och körning. Användare kan synkronisera data i dessa Shards med en slutgiltig replik som bakgrunds aktivitet. |
| TO_DELETE  | 4                | Om citus försöker släppa en Shard-placering som svar på ett master_apply_delete_command-anrop och Miss lyckas, flyttas placeringen till det här läget. Användarna kan sedan ta bort dessa Shards som en efterföljande bakgrunds aktivitet.                                                                                                                                                                                                              |

### <a name="worker-node-table"></a>Tabell för Work Node

Tabellen PG \_ Dist \_ Node innehåller information om arbetsnoder i klustret.

| Namn             | Typ    | Beskrivning                                                                                                                                                                                |
|------------------|---------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| nodeId           | int     | Automatiskt genererad identifierare för en enskild nod.                                                                                                                                          |
| GroupID          | int     | Identifierare som används för att beteckna en grupp av en primär server och noll eller flera sekundära servrar, när modellen för strömning av data används. Som standard är det samma som nodeId.         |
| nodename         | text    | Värd namnet eller IP-adressen för PostgreSQL Worker-noden.                                                                                                                                     |
| nodeport         | int     | Port numret som PostgreSQL Worker-noden lyssnar på.                                                                                                                              |
| noderack         | text    | Valfritt Information om rack placering för arbetsnoden.                                                                                                                                 |
| hasmetadata      | boolean | Reserverat för internt bruk.                                                                                                                                                                 |
| isActive         | boolean | Om noden är aktiv accepterar Shard-placeringar.                                                                                                                                     |
| noderole         | text    | Om noden är primär eller sekundär                                                                                                                                                 |
| nodecluster      | text    | Namnet på klustret som innehåller noden                                                                                                                                               |
| shouldhaveshards | boolean | Om värdet är False kommer Shards att flyttas från en nod (töms) vid ombalansering, och kommer inte heller att Shards från nya distribuerade tabeller på noden, om de inte finns med Shards som redan finns där |

```
SELECT * from pg_dist_node;
 nodeid | groupid | nodename  | nodeport | noderack | hasmetadata | isactive | noderole | nodecluster | shouldhaveshards
--------+---------+-----------+----------+----------+-------------+----------+----------+-------------+------------------
      1 |       1 | localhost |    12345 | default  | f           | t        | primary  | default     | t
      2 |       2 | localhost |    12346 | default  | f           | t        | primary  | default     | t
      3 |       3 | localhost |    12347 | default  | f           | t        | primary  | default     | t
(3 rows)
```

### <a name="distributed-object-table"></a>Tabellen Distributed Object

Tabellen citus.pg \_ Dist- \_ objekt innehåller en lista över objekt, till exempel typer och funktioner som har skapats på koordinator-noden och spridits till arbetsnoder. När en administratör lägger till nya arbetsnoder i klustret, skapar citus (storskalig) automatiskt kopior av de distribuerade objekten på de nya noderna (i rätt ordning för att uppfylla objekt beroenden).

| Namn                        | Typ    | Beskrivning                                          |
|-----------------------------|---------|------------------------------------------------------|
| ClassID                     | OID     | Den distribuerade objektets klass                      |
| objid                       | OID     | Objekt-ID för det distribuerade objektet                  |
| objsubid                    | heltal | Underordnat objekt-ID för det distribuerade objektet, till exempel attnum |
| typ                        | text    | En del av den stabila adressen som används vid PG-uppgraderingar   |
| object_names                | text []  | En del av den stabila adressen som används vid PG-uppgraderingar   |
| object_args                 | text []  | En del av den stabila adressen som används vid PG-uppgraderingar   |
| distribution_argument_index | heltal | Endast giltigt för distribuerade funktioner/procedurer      |
| colocationid                | heltal | Endast giltigt för distribuerade funktioner/procedurer      |

\"Stabila adresser \" identifierar unikt objekt oberoende av en speciell Server. Citus) spårar objekt under en PostgreSQL-uppgradering med hjälp av stabila adresser som skapats med funktionen [PG \_ identifiera \_ objekt \_ som \_ address ()](https://www.postgresql.org/docs/current/functions-info.html#FUNCTIONS-INFO-OBJECT-TABLE) .

Här är \' ett exempel på hur `create_distributed_function()` lägger till poster i `citus.pg_dist_object` tabellen:

```psql
CREATE TYPE stoplight AS enum ('green', 'yellow', 'red');

CREATE OR REPLACE FUNCTION intersection()
RETURNS stoplight AS $$
DECLARE
        color stoplight;
BEGIN
        SELECT *
          FROM unnest(enum_range(NULL::stoplight)) INTO color
         ORDER BY random() LIMIT 1;
        RETURN color;
END;
$$ LANGUAGE plpgsql VOLATILE;

SELECT create_distributed_function('intersection()');

-- will have two rows, one for the TYPE and one for the FUNCTION
TABLE citus.pg_dist_object;
```

```
-[ RECORD 1 ]---------------+------
classid                     | 1247
objid                       | 16780
objsubid                    | 0
type                        |
object_names                |
object_args                 |
distribution_argument_index |
colocationid                |
-[ RECORD 2 ]---------------+------
classid                     | 1255
objid                       | 16788
objsubid                    | 0
type                        |
object_names                |
object_args                 |
distribution_argument_index |
colocationid                |
```

### <a name="colocation-group-table"></a>Grupp tabell för samplacering

Tabellen PG \_ förd \_ samplacering innehåller information om vilka tabeller \' Shards ska placeras tillsammans eller [samplacerade](concepts-hyperscale-colocation.md).
När två tabeller finns i samma samplacerings grupp, ser citus ut att Shards med samma partitionsalternativ placeras på samma arbetsnoder.
Samplacering möjliggör kopplings optimering, vissa distribuerade sammanslagningar och stöd för sekundär nyckel. Shard-samplaceringen härleds när Shard-antal, replikeringsräknare och partition-kolumn skriver all matchning mellan två tabeller. en anpassad grupp för samplacering kan dock anges när du skapar en distribuerad tabell, om det behövs.

| Namn                   | Typ | Beskrivning                                                                   |
|------------------------|------|-------------------------------------------------------------------------------|
| colocationid           | int  | Unik identifierare för den samplacerings grupp som den här raden motsvarar.          |
| shardcount             | int  | Antal Shard för alla tabeller i den här samplacerings gruppen                          |
| replicationfactor      | int  | Replikeringsrelation för alla tabeller i den här samplacerings gruppen.                  |
| distributioncolumntype | OID  | Typ av distributions kolumn för alla tabeller i den här samplacerings gruppen. |

```
SELECT * from pg_dist_colocation;
  colocationid | shardcount | replicationfactor | distributioncolumntype 
 --------------+------------+-------------------+------------------------
             2 |         32 |                 2 |                     20
  (1 row)
```

### <a name="rebalancer-strategy-table"></a>Strategi tabell för ombalansering

I den här tabellen definieras strategier som [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) kan använda för att avgöra var Shards ska flyttas.

| Namn                           | Typ    | Beskrivning                                                                                                                                       |
|--------------------------------|---------|---------------------------------------------------------------------------------------------------------------------------------------------------|
| default_strategy               | boolean | Om rebalance_table_shards ska välja den här strategin som standard. Använd citus_set_default_rebalance_strategy för att uppdatera den här kolumnen             |
| shard_cost_function            | regproc | Identifierare för en kostnads funktion, som måste ta en shardid som bigint, och returnera dess begreppet kostnad som typen verkligt                                |
| node_capacity_function         | regproc | Identifierare för en kapacitets funktion, som måste ha en nodeId som int och returnera begreppet Node-kapacitet som typen verkligt                          |
| shard_allowed_on_node_function | regproc | Identifierare för en funktion som gav shardid bigint och nodeidarg INT returnerar Boolean för om citus kan lagra Shard på noden |
| default_threshold              | float4  | Tröskelvärde för att betrakta en nod för full eller för tom, som avgör när rebalance_table_shards ska försöka flytta Shards                    |
| minimum_threshold              | float4  | En skydds åtgärd för att förhindra att tröskelvärdet för rebalance_table_shards () är för lågt                                                  |

En citus-installation (storskalig) levereras med följande strategier i tabellen:

```postgresql
SELECT * FROM pg_dist_rebalance_strategy;
```

```
-[ RECORD 1 ]-------------------+-----------------------------------
Name                            | by_shard_count
default_strategy                | true
shard_cost_function             | citus_shard_cost_1
node_capacity_function          | citus_node_capacity_1
shard_allowed_on_node_function  | citus_shard_allowed_on_node_true
default_threshold               | 0
minimum_threshold               | 0
-[ RECORD 2 ]-------------------+-----------------------------------
Name                            | by_disk_size
default_strategy                | false
shard_cost_function             | citus_shard_cost_by_disk_size
node_capacity_function          | citus_node_capacity_1
shard_allowed_on_node_function  | citus_shard_allowed_on_node_true
default_threshold               | 0.1
minimum_threshold               | 0.01
```

Standard strategin `by_shard_count` tilldelar varje Shard samma kostnad. Dess inverkan är att utjämna antalet Shard mellan noder. Den andra fördefinierade strategin `by_disk_size` tilldelar en kostnad till varje Shard som matchar disk storleken i byte plus den Shards som finns med i den. Disk storleken beräknas med hjälp av `pg_total_relation_size` , så den innehåller index. Den här strategin försöker uppnå samma disk utrymme på varje nod. Observera tröskelvärdet på 0,1 – det förhindrar onödig Shard rörelse som orsakas av obetydliga skillnader i disk utrymme.

#### <a name="creating-custom-rebalancer-strategies"></a>Skapa anpassade strategier för ombalansering

Här följer exempel på funktioner som kan användas i nya Shard-strategier för ombalansering och som registreras i [pg_dist_rebalance_strategy](reference-hyperscale-metadata.md?#rebalancer-strategy-table) med funktionen [citus_add_rebalance_strategy](reference-hyperscale-functions.md#citus_add_rebalance_strategy) .

-   Ange ett kapacitets undantag per hostname-mönster:

    ```postgresql
    CREATE FUNCTION v2_node_double_capacity(nodeidarg int)
        RETURNS boolean AS $$
        SELECT
            (CASE WHEN nodename LIKE '%.v2.worker.citusdata.com' THEN 2 ELSE 1 END)
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;
    ```

-   Ombalansera med antalet frågor som går till en Shard, mätt i [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table):

    ```postgresql
    -- example of shard_cost_function

    CREATE FUNCTION cost_of_shard_by_number_of_queries(shardid bigint)
        RETURNS real AS $$
        SELECT coalesce(sum(calls)::real, 0.001) as shard_total_queries
        FROM citus_stat_statements
        WHERE partition_key is not null
            AND get_shard_id_for_distribution_column('tab', partition_key) = shardid;
    $$ LANGUAGE sql;
    ```

-   Isolera en speciell Shard (10000) på en nod (adress \' 10.0.0.1 \' ):

    ```postgresql
    -- example of shard_allowed_on_node_function

    CREATE FUNCTION isolate_shard_10000_on_10_0_0_1(shardid bigint, nodeidarg int)
        RETURNS boolean AS $$
        SELECT
            (CASE WHEN nodename = '10.0.0.1' THEN shardid = 10000 ELSE shardid != 10000 END)
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;

    -- The next two definitions are recommended in combination with the above function.
    -- This way the average utilization of nodes is not impacted by the isolated shard.
    CREATE FUNCTION no_capacity_for_10_0_0_1(nodeidarg int)
        RETURNS real AS $$
        SELECT
            (CASE WHEN nodename = '10.0.0.1' THEN 0 ELSE 1 END)::real
        FROM pg_dist_node where nodeid = nodeidarg
        $$ LANGUAGE sql;
    CREATE FUNCTION no_cost_for_10000(shardid bigint)
        RETURNS real AS $$
        SELECT
            (CASE WHEN shardid = 10000 THEN 0 ELSE 1 END)::real
        $$ LANGUAGE sql;
    ```

### <a name="query-statistics-table"></a>Tabellen fråga statistik

Citus (storskalig) ger `citus_stat_statements` statistik om hur frågor körs och vem. Den \' motsvarar (och kan kopplas till) vyn [PG \_ stat- \_ instruktioner](https://www.postgresql.org/docs/current/static/pgstatstatements.html) i postgresql, som spårar statistik om frågans hastighet.

Den här vyn kan spåra frågor till ursprungs klienter i ett program med flera klienter, vilket hjälper till att bestämma när klient isolering ska utföras.

| Namn          | Typ   | Beskrivning                                                                      |
|---------------|--------|----------------------------------------------------------------------------------|
| fråge       | bigint | identifierare (passar pg_stat_statements kopplingar)                                   |
| userid        | OID    | användare som körde frågan                                                           |
| DBID          | OID    | databas instans av koordinator                                                 |
| DocumentDB         | text   | frågesträng för anonymiserats                                                          |
| utförar      | text   | Citus utförar används: adaptiv, real tids, uppgifts spår, router eller Infoga-Välj |
| partition_key | text   | värde för distributions kolumn i frågor som körs av routern, annars NULL               |
| fjärrproceduranrop         | bigint | antal gånger som frågan kördes                                                |

```sql
-- create and populate distributed table
create table foo ( id int );
select create_distributed_table('foo', 'id');
insert into foo select generate_series(1,100);

-- enable stats
-- pg_stat_statements must be in shared_preload libraries
create extension pg_stat_statements;

select count(*) from foo;
select * from foo where id = 42;

select * from citus_stat_statements;
```

Resultat:

```
-[ RECORD 1 ]-+----------------------------------------------
queryid       | -909556869173432820
userid        | 10
dbid          | 13340
query         | insert into foo select generate_series($1,$2)
executor      | insert-select
partition_key |
calls         | 1
-[ RECORD 2 ]-+----------------------------------------------
queryid       | 3919808845681956665
userid        | 10
dbid          | 13340
query         | select count(*) from foo;
executor      | adaptive
partition_key |
calls         | 1
-[ RECORD 3 ]-+----------------------------------------------
queryid       | 5351346905785208738
userid        | 10
dbid          | 13340
query         | select * from foo where id = $1
executor      | adaptive
partition_key | 42
calls         | 1
```

Varningar

-   Statistik informationen är inte replikerad och vann \' t överleva databas krascher eller redundans
-   Spårar ett begränsat antal frågor, som anges av `pg_stat_statements.max` guc (standard 5000)
-   Om du vill trunkera tabellen använder du `citus_stat_statements_reset()` funktionen

### <a name="distributed-query-activity"></a>Distribuerad fråga-aktivitet

Citus (storskalig) innehåller särskilda vyer för att se frågor och lås i hela klustret, inklusive Shard frågor som används internt för att bygga resultat för distribuerade frågor.

-   **citus \_ förd \_ stat- \_ aktivitet**: visar de distribuerade frågor som körs på alla noder. En supermängd av `pg_stat_activity` , användbar när den senare är.
-   **citus \_ Work \_ stat- \_ aktivitet**: visar frågor om arbetare, inklusive fragment-frågor mot enskilda Shards.
-   **citus- \_ låset \_ väntar**: blockerade frågor i hela klustret.

De första två vyerna inkluderar alla kolumner av [PG \_ stat- \_ aktivitet](https://www.postgresql.org/docs/current/static/monitoring-stats.html#PG-STAT-ACTIVITY-VIEW) plus värd värden/-porten för den arbetare som initierade frågan och värd/port för koordinator-noden i klustret.

Överväg till exempel att räkna raderna i en distribuerad tabell:

```postgresql
-- run from worker on localhost:9701

SELECT count(*) FROM users_table;
```

Vi kan se frågan visas i `citus_dist_stat_activity` :

```postgresql
SELECT * FROM citus_dist_stat_activity;

-[ RECORD 1 ]----------+----------------------------------
query_hostname         | localhost
query_hostport         | 9701
master_query_host_name | localhost
master_query_host_port | 9701
transaction_number     | 1
transaction_stamp      | 2018-10-05 13:27:20.691907+03
datid                  | 12630
datname                | postgres
pid                    | 23723
usesysid               | 10
usename                | citus
application\_name      | psql
client\_addr           | 
client\_hostname       | 
client\_port           | -1
backend\_start         | 2018-10-05 13:27:14.419905+03
xact\_start            | 2018-10-05 13:27:16.362887+03
query\_start           | 2018-10-05 13:27:20.682452+03
state\_change          | 2018-10-05 13:27:20.896546+03
wait\_event_type       | Client
wait\_event            | ClientRead
state                  | idle in transaction
backend\_xid           | 
backend\_xmin          | 
query                  | SELECT count(*) FROM users_table;
backend\_type          | client backend
```

Den här frågan kräver information från alla Shards. En del av informationen finns i Shard `users_table_102038` , vilket sker om att lagras i `localhost:9700` . Vi kan se en fråga som kommer åt Shard genom att titta på `citus_worker_stat_activity` vyn:

```postgresql
SELECT * FROM citus_worker_stat_activity;

-[ RECORD 1 ]----------+-----------------------------------------------------------------------------------------
query_hostname         | localhost
query_hostport         | 9700
master_query_host_name | localhost
master_query_host_port | 9701
transaction_number     | 1
transaction_stamp      | 2018-10-05 13:27:20.691907+03
datid                  | 12630
datname                | postgres
pid                    | 23781
usesysid               | 10
usename                | citus
application\_name      | citus
client\_addr           | ::1
client\_hostname       | 
client\_port           | 51773
backend\_start         | 2018-10-05 13:27:20.75839+03
xact\_start            | 2018-10-05 13:27:20.84112+03
query\_start           | 2018-10-05 13:27:20.867446+03
state\_change          | 2018-10-05 13:27:20.869889+03
wait\_event_type       | Client
wait\_event            | ClientRead
state                  | idle in transaction
backend\_xid           | 
backend\_xmin          | 
query                  | COPY (SELECT count(*) AS count FROM users_table_102038 users_table WHERE true) TO STDOUT
backend\_type          | client backend
```

`query`Fältet visar data som kopieras från Shard som ska räknas.

> [!NOTE]
> Om en router-fråga (t. ex. en enskild klient i ett program med flera klienter) väljer du
> * FRÅN tabell där tenant_id = X) körs utan ett transaktions block, \_ kommer huvud frågans \_ värdnamn \_ och huvud \_ frågas \_ värde \_ port kolumner vara null i citus \_ Work stat \_ - \_ aktivitet.

`citus_lock_waits`Vi kan se hur fungerar genom att skapa en låsnings situation manuellt. Först \' ska vi konfigurera en test tabell från koordinatorn:

```postgresql
CREATE TABLE numbers AS
  SELECT i, 0 AS j FROM generate_series(1,10) AS i;
SELECT create_distributed_table('numbers', 'i');
```

Sedan kan vi använda två sessioner i koordinatorn för att köra denna sekvens med instruktioner:

```postgresql
-- session 1                           -- session 2
-------------------------------------  -------------------------------------
BEGIN;
UPDATE numbers SET j = 2 WHERE i = 1;
                                       BEGIN;
                                       UPDATE numbers SET j = 3 WHERE i = 1;
                                       -- (this blocks)
```

I `citus_lock_waits` vyn visas situationen.

```postgresql
SELECT * FROM citus_lock_waits;

-[ RECORD 1 ]-------------------------+----------------------------------------
waiting_pid                           | 88624
blocking_pid                          | 88615
blocked_statement                     | UPDATE numbers SET j = 3 WHERE i = 1;
current_statement_in_blocking_process | UPDATE numbers SET j = 2 WHERE i = 1;
waiting_node_id                       | 0
blocking_node_id                      | 0
waiting_node_name                     | coordinator_host
blocking_node_name                    | coordinator_host
waiting_node_port                     | 5432
blocking_node_port                    | 5432
```

I det här exemplet har frågorna som kommer från koordinatorn, men vyn kan också visa en lista över lås mellan frågor som har sitt ursprung i arbets tagarna (exekveras med storskalig skalning (citus) MX för instans).

## <a name="next-steps"></a>Nästa steg

* Lär dig hur vissa [storskaliga (citus)-funktioner](reference-hyperscale-functions.md) ändrar system tabeller
* Granska begreppen för [noder och tabeller](concepts-hyperscale-nodes.md)
