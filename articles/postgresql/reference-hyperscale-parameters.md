---
title: Server parametrar – storskalig (citus) – Azure Database for PostgreSQL
description: Parametrar i den storskaliga (citus) SQL API
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: reference
ms.date: 08/10/2020
ms.openlocfilehash: 07f966c7b0be542f848f1a0a4eaf2b5549735b4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91336249"
---
# <a name="server-parameters"></a>Serverparametrar

Det finns olika Server parametrar som påverkar citus (Scale Scale), både från standard-PostgreSQL och som är särskilt för citus ().
De här parametrarna kan anges i Azure Portal för en citus-Server (för en storskalig). Under kategorin **Inställningar** väljer du parametrar för **Work Node** eller **noden koordinator parametrar**. Med dessa sidor kan du ange parametrar för alla arbetsnoder, eller bara för koordinator-noden.

## <a name="hyperscale-citus-parameters"></a>Citus-parametrar

> [!NOTE]
>
> Citus-Server grupper som kör äldre versioner av citus-motorn kanske inte erbjuder alla parametrar som anges nedan.

### <a name="general-configuration"></a>Allmän konfiguration

#### <a name="citususe_secondary_nodes-enum"></a>citus. Använd \_ sekundär \_ noder (Enum)

Anger den princip som ska användas när du väljer noder för URVALs frågor. Om den är inställd på Always, kommer Planner bara att fråga noder som har marker ATS som sekundära noderole i [pg_dist_node](reference-hyperscale-metadata.md#worker-node-table).

De värden som stöds för den här uppräkningen är:

-   **aldrig:** (standard) alla läsningar sker på primära noder.
-   **Always:** Läser in kör mot sekundära noder i stället, och INSERT/Update-instruktioner är inaktiverade.

#### <a name="cituscluster_name-text"></a>citus. Cluster \_ -namn (text)

Informerar Coordinator-nodens planerare som kluster IT samordnar. När kluster \_ namnet har angetts kommer Planner att fråga arbetsnoder i det klustret separat.

#### <a name="citusenable_version_checks-boolean"></a>citus. Aktivera \_ versions \_ kontroller (boolesk)

För uppgradering av citus-version (för storskalig) krävs omstart av servern (för att hämta det nya delade biblioteket) följt av kommandot ALTER EXTENSION UPDATE.  Det gick inte att köra båda stegen om du skulle kunna orsaka fel eller krascher.
Storskalig (citus) validerar därför versionen av koden och tilläggets matchning, och fel uppstår om de inte är det \' .

Standardvärdet är true och det gäller koordinatorn. I sällsynta fall kan komplexa uppgraderings processer kräva att den här parametern anges till false, vilket innebär att kontrollen inaktive ras.

#### <a name="cituslog_distributed_deadlock_detection-boolean"></a>citus. log \_ distribuerad \_ deadlock \_ upptäcktes (Boolean)

Om distribution av distribuerad deadlock ska loggas i Server loggen. Standardvärdet är false.

#### <a name="citusdistributed_deadlock_detection_factor-floating-point"></a>citus. distributions faktor för distribuerad \_ deadlock \_ \_ (flytt ALS)

Anger vänte tiden innan ett distribuerat död läge kontrol leras. I synnerhet är vänte tiden detta värde multiplicerat med PostgreSQL \' s timeout-inställning för [död läge \_ ](https://www.postgresql.org/docs/current/static/runtime-config-locks.html) . Standardvärdet är `2`. Värdet `-1` inaktiverar identifiering av distribuerat död läge.

#### <a name="citusnode_connection_timeout-integer"></a>\_ \_ tids gräns för citus. Node-anslutning (heltal)

`citus.node_connection_timeout`Guc anger maximal varaktighet (i millisekunder) för att vänta på anslutnings etablering. Citus (storskalig) aktiverar ett fel om tids gränsen förflutit innan minst en arbets anslutning upprättas. Den här GUC påverkar anslutningar från koordinatorn till arbetare och anställda till varandra.

-   Standard: fem sekunder
-   Minst: 10 millisekunder
-   Max: en timme

```postgresql
-- set to 30 seconds
ALTER DATABASE foo
SET citus.node_connection_timeout = 30000;
```

### <a name="query-statistics"></a>Fråga efter statistik

#### <a name="citusstat_statements_purge_interval-integer"></a>rensnings intervall för citus. stat \_ \_ -satser \_ (heltal)

Anger med vilken frekvens som underhålls demon tar bort poster från [citus_stat_statements](reference-hyperscale-metadata.md#query-statistics-table) som inte matchar i `pg_stat_statements` . Det här konfiguration svärdet anger tidsintervallet mellan rensningar i sekunder, med ett standardvärde på 10. Värdet 0 inaktiverar rensningarna.

```psql
SET citus.stat_statements_purge_interval TO 5;
```

Den här parametern är effektiv i koordinatorn och kan ändras vid körning.

### <a name="data-loading"></a>Data inläsning

#### <a name="citusmulti_shard_commit_protocol-enum"></a>citus. \_ Shard (Multi \_ commit \_ Protocol)

Anger vilket protokoll som ska användas vid kopiering av en hash-distribuerad tabell. På varje enskild Shard placering utförs KOPIAn i ett transaktions block för att säkerställa att inga data matas in om ett fel inträffar under KOPIERINGen. Det finns dock ett visst fel fall där KOPIERINGen lyckas på alla platser, men ett (maskin varu fel) inträffar innan alla transaktioner har genomförts. Den här parametern kan användas för att förhindra data förlust i detta fall genom att välja mellan följande commit-protokoll:

-   **2PC:** (standard) de transaktioner där kopiering utförs på Shard-placeringarna förbereds först med postgresql \' s genomförande i [två faser](http://www.postgresql.org/docs/current/static/sql-prepare-transaction.html) och sedan allokeras. Misslyckade incheckningar kan återställas manuellt eller avbrytas med genomför för beredd eller återställning.
    När du använder 2PC bör [Max för \_ beredda \_ transaktioner](http://www.postgresql.org/docs/current/static/runtime-config-resource.html) ökas för alla arbetare, vanligt vis till samma värde som maximalt antal \_ anslutningar.
-   **1PC:** De transaktioner där KOPIERINGen utförs på Shard-placeringarna allokeras i en enda runda. Data kan gå förlorade om ett genomförande Miss lyckas när KOPIERINGen lyckas på alla platser (sällsynt).

#### <a name="citusshard_replication_factor-integer"></a>citus. Shard \_ \_ (integer)

Anger Shards för det antal noder där Shards ska placeras och standardvärdet är 1. Den här parametern kan anges i körnings tid och är effektiv i koordinatorn. Det idealiska värdet för den här parametern beror på klustrets storlek och frekvensen av nodfel.  Du kanske till exempel vill öka den här replikeringslänken om du kör stora kluster och studerar nodfel oftare.

#### <a name="citusshard_count-integer"></a>antal citus. Shard \_ (heltal)

Anger antalet Shard för hash-partitionerade tabeller och standardvärdet 32.  Det här värdet används av [create_distributed_table](reference-hyperscale-functions.md#create_distributed_table) UDF när du skapar hash-partitionerade tabeller. Den här parametern kan anges i körnings tid och är effektiv i koordinatorn.

#### <a name="citusshard_max_size-integer"></a>citus. Shard \_ Max \_ storlek (heltal)

Anger den maximala storlek som en Shard ska växa till innan den delas och standardvärdet är 1 GB. När käll filens \' storlek (som används för mellanlagring) för en Shard överstiger detta konfigurations värde, säkerställer databasen att en ny Shard skapas. Den här parametern kan anges i körnings tid och är effektiv i koordinatorn.

### <a name="planner-configuration"></a>Planner-konfiguration

#### <a name="cituslimit_clause_row_fetch_count-integer"></a>citus. Limit- \_ sats \_ rad \_ hämtnings \_ antal (heltal)

Anger antalet rader som ska hämtas per uppgift för optimering av begränsnings satser.
I vissa fall väljer du frågor med begränsnings klausuler kan behöva hämta alla rader från varje aktivitet för att generera resultat. I dessa fall, och där en uppskattning skulle ge meningsfulla resultat, anger det här konfiguration svärdet antalet rader som ska hämtas från varje Shard. Limit-uppskattningar är inaktiverade som standard och den här parametern har värdet-1. Det här värdet kan ställas in i körnings tid och tillämpas på koordinatorn.

#### <a name="cituscount_distinct_error_rate-floating-point"></a>citus. räkna \_ distinkt \_ fel \_ frekvens (flytt ALS)

Storskalig (citus) kan beräkna antalet (distinkt) ungefärlig med hjälp av postgresql-HLL-tillägget. Den här konfigurations posten anger önskad fel frekvens vid beräkning av antal (distinkta). 0,0, som är standard, inaktive ras approximationer för Count (DISTINCT). och 1,0 ger inga garantier om resultatet av resultaten. Vi rekommenderar att du anger den här parametern till 0,005 för bästa möjliga resultat. Det här värdet kan ställas in i körnings tid och tillämpas på koordinatorn.

#### <a name="citustask_assignment_policy-enum"></a>citus. princip för aktivitets \_ tilldelning \_ (Enum)

> [!NOTE]
> Den här GUC gäller endast när [shard_replication_factor](reference-hyperscale-parameters.md#citusshard_replication_factor-integer) är större än en eller för frågor mot [reference_tables](concepts-hyperscale-distributed-data.md#type-2-reference-tables).

Anger den princip som ska användas när aktiviteter tilldelas till arbetare. Koordinatorn tilldelar uppgifter till arbetare baserat på Shard-platser. Detta konfigurations värde anger den princip som ska användas för att utföra dessa uppgifter.
Det finns för närvarande tre möjliga principer för aktivitets tilldelning som kan användas.

-   **girig:** Girig-principen är standard och syftar till att fördela aktiviteter mellan arbetare.
-   **resursallokering:** Principen för resursallokering tilldelar uppgifter till arbetare i en växel för resursallokering mellan olika repliker. Den här principen möjliggör bättre kluster användning när antalet Shard för en tabell är lågt jämfört med antalet arbetare.
-   **första replik:** Principen för den första repliken tilldelar uppgifter utifrån insättnings ordningen för placeringar (repliker) för Shards. Med andra ord tilldelas fragment frågan för en Shard till den arbets tagare som har den första repliken av Shard.
    Med den här metoden kan du ha starka garantier om vilka Shards som kommer att användas på vilka noder (det vill säga starkare minnes placering garantier).

Den här parametern kan anges i körnings tid och är effektiv i koordinatorn.

### <a name="intermediate-data-transfer"></a>Mellanliggande Dataöverföring

#### <a name="citusbinary_worker_copy_format-boolean"></a>formatet citus. Binary \_ Worker \_ copy \_ (boolesk)

Använd formatet binär kopia för att överföra mellanliggande data mellan arbetare.
Vid stor koppling av tabeller kan storskalig (citus) behöva partitionera om och blanda data mellan olika arbetare. Som standard överförs dessa data i text format. Om du aktiverar den här parametern instrueras databasen att använda PostgreSQL-formatet för binär serialisering för att överföra dessa data. Den här parametern är effektiv för arbets tagarna och måste ändras i filen postgresql. conf. När du har redigerat konfigurations filen kan användarna skicka en SIGHUP-signal eller starta om servern för att ändringen ska börja gälla.

#### <a name="citusbinary_master_copy_format-boolean"></a>citus. binär \_ huvud \_ kopierings \_ format (Boolean)

Använd formatet binär kopia för att överföra data mellan koordinatorn och arbets tagarna. När du kör distribuerade frågor överför arbets tagarna sina mellanliggande resultat till koordinatorn för slutlig agg regering. Som standard överförs dessa data i text format. Om du aktiverar den här parametern instrueras databasen att använda PostgreSQL-formatet för binär serialisering för att överföra dessa data.
Den här parametern kan anges vid körning och är giltig för koordinatorn.

#### <a name="citusmax_intermediate_result_size-integer"></a>citus. Max \_ storlek för mellanliggande \_ resultat \_ (heltal)

Den maximala storleken i KB av mellanliggande resultat för CTE som inte kan flyttas ned till arbetsnoder för körning och för komplexa under frågor. Standardvärdet är 1 GB och värdet-1 innebär ingen gräns.
Frågor som överskrider gränsen avbryts och genererar ett fel meddelande.

### <a name="ddl"></a>-

#### <a name="citusenable_ddl_propagation-boolean"></a>citus. Aktivera \_ DDL \_ -spridning (boolesk)

Anger om DDL-ändringar ska spridas automatiskt från koordinatorn till alla arbetare. Standardvärdet är True. Eftersom vissa schema ändringar kräver ett åtkomst exklusivt lås i tabeller, och eftersom den automatiska spridningen gäller för alla arbetare i turordning, kan det göra ett citus-kluster (för hög skalning) tillfälligt mindre. Du kan välja att inaktivera den här inställningen och sprida ändringar manuellt.

### <a name="executor-configuration"></a>Utförar-konfiguration

#### <a name="general"></a>Allmänt

##### <a name="citusall_modifications_commutative"></a>citus. alla \_ ändringar \_ commutative

Citus (storskalig) framtvingar commutativity-regler och hämtar lämpliga lås för ändrings åtgärder för att garantera att beteendet är korrekt. Till exempel förutsätter vi att en INSERT-instruktion följer en annan INSERT-instruktion, men inte med en UPDATE-eller DELETE-instruktion. På samma sätt förutsätter vi att en UPDATE-eller DELETE-instruktion inte återkommer till en annan UPDATE-eller DELETE-instruktion. Den här försiktighets åtgärden innebär att uppdateringar och borttagningar kräver storskalig skalning (citus) för att få starkare lås.

Om du har UPPDATERINGs instruktioner som är commutative med dina infogningar eller andra uppdateringar kan du minska dessa commutativity-antaganden genom att ange den här parametern till true. När den här parametern har angetts till True betraktas alla kommandon som commutative och gör anspråk på ett delat lås, vilket kan förbättra det totala data flödet. Den här parametern kan anges vid körning och är giltig för koordinatorn.

##### <a name="citusremote_task_check_interval-integer"></a>citus. \_ kontroll intervall för fjärraktivitet \_ \_ (heltal)

Anger med vilken frekvens citus ska kontrol lera status för jobb som hanteras av aktivitets spåraren utförar. Standardvärdet är 10 MS. Koordinatorn tilldelar uppgifter till arbetare och kontrollerar sedan regelbundet med dem om varje aktivitet \' s förlopp. Detta konfigurations värde anger tidsintervallet mellan två efterföljande kontroller. Den här parametern är effektiv i koordinatorn och kan ställas in vid körning.

##### <a name="citustask_executor_type-enum"></a>citus. Task \_ utförar \_ -typ (Enum)

Citus (storskalig) har tre utförar-typer för att köra distribuerade URVALs frågor.  Du kan välja önskad utförar genom att ange den här konfigurations parametern. De godkända värdena för den här parametern är:

-   **adaptiv:** Standardvärdet. Det är optimalt för snabba svar på frågor som omfattar agg regeringar och samplacerade kopplingar över flera Shards.
-   **uppgifts Spårare:** Utförar för aktivitetens spår passar bra för långvariga, komplexa frågor som kräver blandning av data över arbetsnoder och effektiv resurs hantering.
-   **real tid:** (inaktuell) fungerar på ett liknande sätt som den anpassade utförar, men det är mindre flexibelt och kan orsaka mer anslutnings belastning på arbetsnoder.

Den här parametern kan anges i körnings tid och är effektiv i koordinatorn.

##### <a name="citusmulti_task_query_log_level-enum-multi_task_logging"></a>citus. \_ fråga efter \_ loggnings nivå för flera aktiviteter \_ \_ {#multi_task_logging}

Anger en loggnings nivå för alla frågor som genererar fler än en aktivitet (det vill säga fler än en Shard). Loggning är användbart under en programmigrering för flera innehavare, eftersom du kan välja att fel eller varna för sådana frågor, för att hitta dem och lägga till ett klient \_ -ID-filter till dem. Den här parametern kan anges vid körning och är giltig för koordinatorn. Standardvärdet för den här parametern är \' inaktiverat \' .

De värden som stöds för den här uppräkningen är:

-   **av:** Inaktivera loggning av frågor som genererar flera aktiviteter (det vill säga flera Shards)
-   **fel sökning:** Logs-instruktioner vid allvarlighets grad för fel sökning.
-   **logg:** Loggar-instruktion på allvarlighets nivå för logg. Logg raden kommer att innehålla SQL-frågan som kördes.
-   **meddelande:** Loggar instruktion vid MEDDELANDEts allvarlighets grad.
-   **Varning:** Loggar uttryck på VARNINGs nivå.
-   **fel:** Loggar-instruktion vid allvarlighets grad för fel.

Det kan vara användbart att använda `error` under utvecklings testningen och en lägre loggnivå som `log` under den faktiska produktions distributionen.
Om du väljer `log` så visas frågor med flera aktiviteter i databas loggarna med frågan som visas efter \" instruktionen.\"

```
LOG:  multi-task query about to be executed
HINT:  Queries are split to multiple tasks if they have to be split into several queries on the workers.
STATEMENT:  select * from foo;
```

##### <a name="citusenable_repartition_joins-boolean"></a>citus. Aktivera \_ ompartitionering \_ kopplingar (boolesk)

Normalt kommer försök att utföra ompartitionering av kopplingar med den anpassade utförar att Miss lyckas med ett fel meddelande.  Om du däremot anger `citus.enable_repartition_joins` true kan citus (Scale Scale) tillfälligt växla till utförar i aktivitets spåraren för att utföra kopplingen.  Standardvärdet är false.

#### <a name="task-tracker-executor-configuration"></a>Konfiguration av aktivitets spår utförar

##### <a name="citustask_tracker_delay-integer"></a>citus. fördröjning för uppgifts \_ spår \_ (heltal)

Den här parametern anger inströms tiden för aktivitets Spårare mellan aktivitets hanteringen avrundas och är som standard 200 MS. Processen för uppgifts Spårare aktive ras regelbundet, går igenom alla uppgifter som har tilldelats den och schemalägger och kör dessa uppgifter.  Sedan inaktive aktivitets Spårare för en tids period innan du går vidare över aktiviteterna igen.
Detta konfigurations värde bestämmer längden på den vilande perioden. Den här parametern är effektiv för arbets tagarna och måste ändras i filen postgresql. conf. När du har redigerat konfigurations filen kan användarna skicka en SIGHUP-signal eller starta om servern för att ändringen ska börja gälla.

Den här parametern kan minskas för att trimma fördröjningen som orsakas av aktivitets spårets utförar genom att minska tids skillnaden mellan hanterings avrundning.
Att minska fördröjningen är användbart i fall när Shard-frågorna är korta och uppdaterar sedan sin status regelbundet.

##### <a name="citusmax_assign_task_batch_size-integer"></a>citus. max. \_ tilldela \_ uppgiftens \_ batchstorlek \_ (heltal)

Utförar för uppgifts spårare i koordinatorn tilldelar synkrona uppgifter i batchar till daemonen på arbets tagarna. Den här parametern anger det maximala antalet uppgifter som ska tilldelas i en enskild batch. Att välja en större batchstorlek möjliggör snabbare aktivitets tilldelning. Men om antalet arbetare är stort kan det ta längre tid för alla arbetare att hämta uppgifter.  Den här parametern kan anges vid körning och är giltig för koordinatorn.

##### <a name="citusmax_running_tasks_per_node-integer"></a>citus. Max \_ aktiviteter som körs \_ \_ per \_ nod (heltal)

Aktivitets spåraren bearbetar scheman och kör de uppgifter som har tilldelats dem efter behov. Det här konfiguration svärdet anger det maximala antalet aktiviteter som ska köras samtidigt på en nod och standardvärdet är 8.

Gränsen garanterar att du inte \' har många aktiviteter som når disk samtidigt, och hjälper till att undvika disk-I/O-konkurrens. Om dina frågor skickas från minnes-eller SSD kan du öka högsta antalet \_ aktiva \_ aktiviteter \_ per \_ nod utan att det är mycket viktigt.

##### <a name="cituspartition_buffer_size-integer"></a>citus. partitionens \_ Buffertstorlek \_ (heltal)

Anger buffertstorleken som ska användas för partition åtgärder och standardvärdet är 8 MB.
Storskalig (citus) gör att tabell data kan partitioneras om i flera filer när två stora tabeller kopplas. När den här bufferten fylls, töms ompartitionerade data i filer på disk.  Den här konfigurations posten kan ställas in i körnings tid och gäller för arbets tagarna.

#### <a name="explain-output"></a>Förklara utdata

##### <a name="citusexplain_all_tasks-boolean"></a>citus. förklara \_ alla \_ uppgifter (booleska)

Som standard visar disscale (citus) utdata från en enskild, valfri aktivitet när du kör en [förklaring](http://www.postgresql.org/docs/current/static/sql-explain.html) i en distribuerad fråga. I de flesta fall liknar förklaringen resultatet av olika uppgifter. Ibland kommer vissa av uppgifterna att planeras annorlunda eller ha mycket högre körnings tider. I dessa fall kan det vara praktiskt att aktivera den här parametern, efter vilken förklarar resultatet kommer att innehålla alla uppgifter. Att förklara alla aktiviteter kan göra att förklaringen tar längre tid.

## <a name="postgresql-parameters"></a>PostgreSQL-parametrar

* [DateStyle](https://www.postgresql.org/docs/current/datatype-datetime.html#DATATYPE-DATETIME-OUTPUT) – anger visnings formatet för datum-och tids värden
* [IntervalStyle](https://www.postgresql.org/docs/current/datatype-datetime.html#DATATYPE-INTERVAL-OUTPUT) – anger visnings formatet för intervall värden
* [Timezone](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-TIMEZONE) – anger tids zonen för visning och tolkning av tidsstämplar
* [Application_name](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-APPLICATION-NAME) – anger det program namn som ska rapporteras i statistik och loggar
* [array_nulls](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-ARRAY-NULLS) – aktiverar inmatade null-element i matriser
* [autovakuum](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM) – startar autovakuum-underprocessen
* [autovacuum_analyze_scale_factor](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-ANALYZE-SCALE-FACTOR) antal tuple infogningar, uppdateringar eller borttagningar innan de analyseras som en bråkdel av reltuples
* [autovacuum_analyze_threshold](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-ANALYZE-THRESHOLD) -minsta antal tuple infogningar, uppdateringar eller borttagningar innan analys
* [autovacuum_naptime](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-NAPTIME) tid till vilo läge mellan autovakuum-körningar
* [autovacuum_vacuum_cost_delay](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-COST-DELAY) -vakuum-kostnads fördröjning i millisekunder för autovakuum
* [autovacuum_vacuum_cost_limits](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-COST-LIMIT) vakuum kostnads belopp som är tillgängligt innan napping, för autovakuum
* [autovacuum_vacuum_scale_factor](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-SCALE-FACTOR) -antal tuple-uppdateringar eller borttagningar före vakuum som en bråkdel av reltuples
* [autovacuum_vacuum_threshold](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#GUC-AUTOVACUUM-VACUUM-THRESHOLD) -minsta antal tuple-uppdateringar eller borttagningar före vakuum
* [autovacuum_work_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-AUTOVACUUM-WORK-MEM) – anger högsta mängd minne som ska användas av varje autovakuum-arbets process
* [backend_flush_after](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BACKEND-FLUSH-AFTER) antal sidor efter vilka tidigare utförda skrivningar har rensats till disk
* [backslash_quote](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-BACKSLASH-QUOTE) -anger om " \' " tillåts i sträng litteraler
* [bgwriter_delay](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-DELAY) bakgrunds tiden för bakgrunds skrivaren mellan avrundning
* [bgwriter_flush_after](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-FLUSH-AFTER) antal sidor efter vilka tidigare utförda skrivningar har rensats till disk
* [bgwriter_lru_maxpages](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-LRU-MAXPAGES) -Background Writer maximalt antal LRU sidor som ska rensas per tur
* [bgwriter_lru_multiplier](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-BGWRITER-LRU-MULTIPLIER) – multipel av genomsnittlig buffert-användning för kostnads fritt per runda
* [bytea_output](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-BYTEA-OUTPUT) -anger utdataformat för bytea
* [check_function_bodies](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CHECK-FUNCTION-BODIES) – kontrollerar funktions kroppar under funktionen Skapa
* [checkpoint_completion_target](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-CHECKPOINT-COMPLETION-TARGET) -åtgången för tömning av felaktiga buffertar under kontroll punkten, som en del av kontroll punkts intervallet
* [checkpoint_timeout](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-CHECKPOINT-TIMEOUT) -anger den maximala tiden mellan automatiska Wal-kontrollpunkter
* [checkpoint_warning](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-CHECKPOINT-WARNING) – aktiverar varningar om kontroll punkts segmenten fylls oftare än detta
* [client_encoding](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CLIENT-ENCODING) -anger kodningen för klientens teckenuppsättning
* [client_min_messages](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-CLIENT-MIN-MESSAGES) – anger de meddelande nivåer som skickas till klienten
* [commit_delay](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-COMMIT-DELAY) – anger fördröjningen i mikrosekunder mellan transaktions genomförande och tömning av Wal till disk
* [commit_siblings](https://www.postgresql.org/docs/12/runtime-config-wal.html#GUC-COMMIT-SIBLINGS) – anger minimi antalet samtidiga öppna transaktioner innan du utför commit_delay
* [constraint_exclusion](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CONSTRAINT-EXCLUSION) – gör det möjligt för planeraren att använda begränsningar för att optimera frågor
* [cpu_index_tuple_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CPU-INDEX-TUPLE-COST) – anger en uppskattning av kostnaden för bearbetning av varje index post under en indexs ökning
* [cpu_operator_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CPU-OPERATOR-COST) – ställer in Planerarens uppskattning av kostnaden för bearbetning av varje operatör eller funktions anrop
* [cpu_tuple_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CPU-TUPLE-COST) – anger en uppskattning av kostnaden för bearbetning av varje tupel (rad)
* [cursor_tuple_fraction](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-CURSOR-TUPLE-FRACTION) – anger en uppskattning av den andel av en markörs rader som kommer att hämtas
* [deadlock_timeout](https://www.postgresql.org/docs/current/runtime-config-locks.html#GUC-DEADLOCK-TIMEOUT) – anger tiden, i millisekunder, för att vänta på Lås innan den söker efter deadlock
* [debug_pretty_print](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.3.1.3) -indrag visar analys-och plan träd
* [debug_print_parse](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.2.1.3) loggar varje frågas parse-träd
* [debug_print_plan](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.2.1.3) loggar varje frågas körnings plan
* [debug_print_rewritten](https://www.postgresql.org/docs/current/runtime-config-logging.html#id-1.6.6.11.5.2.2.1.3) loggar varje frågas omskrivna analys träd
* [default_statistics_target](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-DEFAULT-STATISTICS-TARGET) -anger standard statistik mål
* [default_tablespace](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TABLESPACE) -anger standard register utrymmet för att skapa tabeller och index i
* [default_text_search_config](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TEXT-SEARCH-CONFIG) -anger konfiguration av standard texts ökning
* [default_transaction_deferrable](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TRANSACTION-DEFERRABLE) -Anger standardvärdet för uppskjuten status för nya transaktioner
* [default_transaction_isolation](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TRANSACTION-ISOLATION) -anger transaktions isolerings nivån för varje ny transaktion
* [default_transaction_read_only](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-DEFAULT-TRANSACTION-READ-ONLY) -anger standardinställningen för skrivskyddad status för nya transaktioner
* default_with_oids – skapar nya tabeller med OID som standard
* [effective_cache_size](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-EFFECTIVE-CACHE-SIZE) – anger Planerarens antagande om storleken på diskcachen
* [enable_bitmapscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-BITMAPSCAN) – gör det möjligt för Planerarens användning av cykel-skannings planer
* [enable_gathermerge](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-GATHERMERGE) – gör det möjligt för Planerarens användning av samlade sammanfognings planer
* [enable_hashagg](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-HASHAGG) – gör det möjligt för Planerarens användning av hashkodade sammansättnings planer
* [enable_hashjoin](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-HASHJOIN) – aktiverar Planerarens användning av hash Join-planer
* [enable_indexonlyscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-INDEXONLYSCAN) – gör det möjligt för Planerarens användning av enbart index – skannings planer
* [enable_indexscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-INDEXSCAN) – gör det möjligt för Planerarens användning av index – skannings planer
* [enable_material](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-MATERIAL) – aktiverar Planerarens användning av materialization
* [enable_mergejoin](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-MERGEJOIN) – gör det möjligt att använda Planerarens användning av sammanfognings planer
* [enable_nestloop](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-NESTLOOP) – gör det möjligt för Planerarens användning av kapslade loopar till planer
* [enable_seqscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-SEQSCAN) – gör det möjligt för Planerarens användning av sekventiella skannings planer
* [enable_sort](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-SORT) – gör det möjligt för planeraren att använda explicita sorterings steg
* [enable_tidscan](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-ENABLE-TIDSCAN) – gör det möjligt för Planerarens användning av tid Scan planer
* [escape_string_warning](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-ESCAPE-STRING-WARNING) – varningar om Escape-meddelanden i vanliga sträng strängar
* [exit_on_error](https://www.postgresql.org/docs/current/runtime-config-error-handling.html#GUC-EXIT-ON-ERROR) -avslutar sessionen på ett fel
* [extra_float_digits](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-EXTRA-FLOAT-DIGITS) – anger antalet siffror som visas för flytt ALS värden
* [force_parallel_mode](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-FORCE-PARALLEL-MODE) -tvingar användning av parallella fråge funktioner
* [from_collapse_limit](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-FROM-COLLAPSE-LIMIT) – anger storleken på en lista utöver vilken under frågor inte komprimeras
* [geqo](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO) – möjliggör optimering av genetiska frågor
* [geqo_effort](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-EFFORT) -geqo: ansträngning används för att ange standardvärdet för andra geqo-parametrar
* [geqo_generations](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-GENERATIONS) -geqo: antal iterationer av algoritmen
* [geqo_pool_size](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-POOL-SIZE) -geqo: antal individer i populationen
* [geqo_seed](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-SEED) -geqo: Start för val av slumpmässig sökväg
* [geqo_selection_bias](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-SELECTION-BIAS) -geqo: selektivt tryck i populationen
* [geqo_threshold](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-GEQO-THRESHOLD) – anger tröskelvärdet för från objekt utöver vilka geqo används
* [gin_fuzzy_search_limit](https://www.postgresql.org/docs/current/runtime-config-client.html#id-1.6.6.14.5.2.2.1.3) – anger det högsta tillåtna resultatet för exakt sökning med gin
* [gin_pending_list_limit](https://www.postgresql.org/docs/current/runtime-config-client.html#id-1.6.6.14.2.2.23.1.3) – anger den maximala storleken för den väntande listan för gin-index
* [idle_in_transaction_session_timeout](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-IDLE-IN-TRANSACTION-SESSION-TIMEOUT) – anger den längsta tillåtna varaktigheten för en tom tomgång-transaktion
* [join_collapse_limit](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-JOIN-COLLAPSE-LIMIT) – anger storleken på en lista utöver vilken Join-konstruktioner inte förenklas
* [LC_MONETARY](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LC-MONETARY) – anger språkvarianten för formatering av penning belopp
* [LC_NUMERIC](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LC-NUMERIC) – anger språk inställningen för formatering av tal
* [lo_compat_privileges](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-LO-COMPAT-PRIVILEGES) – aktiverar bakåtkompatibelt kompatibilitetsläge för behörighets kontroller för stora objekt
* [LOCK_TIMEOUT](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-LOCK-TIMEOUT) – anger den längsta tillåtna varaktigheten (i millisekunder) av väntande lås. 0 inaktiverar detta
* [log_autovacuum_min_duration](https://www.postgresql.org/docs/current/runtime-config-autovacuum.html#) – anger den minsta körnings tid som ska användas för autovakuum-åtgärder
* [log_checkpoints](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-CHECKPOINTS) loggar varje kontroll punkt
* [log_connections](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-CONNECTIONS) -loggar varje lyckad anslutning
* [log_destination](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DESTINATION) -anger målet för Server loggens utdata
* [log_disconnections](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DISCONNECTIONS) loggar slutet av en session, inklusive varaktighet
* [log_duration](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-DURATION) – loggar varaktigheten för varje SLUTFÖRd SQL-instruktion
* [log_error_verbosity](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-ERROR-VERBOSITY) -anger utförligheten för loggade meddelanden
* [log_lock_waits](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LOCK-WAITS) loggar långa lås väntar
* [log_min_duration_statement](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-MIN-DURATION-STATEMENT) – anger den minsta körnings tiden (i millisekunder) som-satserna ska loggas. -1 inaktiverar varaktighet för loggnings instruktion
* [log_min_error_statement](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-MIN-ERROR-STATEMENT) -gör att alla instruktioner genererar fel på eller ovanför den här nivån loggas
* [log_min_messages](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-MIN-MESSAGES) -anger de meddelande nivåer som loggas
* [log_replication_commands](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-REPLICATION-COMMANDS) -loggar varje kommando för replikering
* [log_statement](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-STATEMENT) – anger vilken typ av uttryck som loggats
* [log_statement_stats](https://www.postgresql.org/docs/current/runtime-config-statistics.html#id-1.6.6.12.3.2.1.1.3) – för varje fråga skriver den kumulativa prestanda statistiken till Server loggen
* [log_temp_files](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-TEMP-FILES) -loggar användning av temporära filer som är större än det här antalet kilobyte
* [maintenance_work_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAINTENANCE-WORK-MEM) – anger högsta mängd minne som ska användas för underhålls åtgärder
* [max_parallel_workers](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PARALLEL-WORKERS) – anger det maximala antalet parallella arbetare som kan vara aktiva samtidigt
* [max_parallel_workers_per_gather](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-MAX-PARALLEL-WORKERS-PER-GATHER) -anger maximalt antal parallella processer per utförar-nod
* [max_pred_locks_per_page](https://www.postgresql.org/docs/current/runtime-config-locks.html#GUC-MAX-PRED-LOCKS-PER-PAGE) – anger maximalt antal predikat-låsta tupler per sida
* [max_pred_locks_per_relation](https://www.postgresql.org/docs/current/runtime-config-locks.html#GUC-MAX-PRED-LOCKS-PER-RELATION) – anger maximalt antal predikat-låsta sidor och tupler per relation
* [max_standby_archive_delay](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-MAX-STANDBY-ARCHIVE-DELAY) – anger den maximala fördröjningen innan frågor avbryts när en snabb växlings Server bearbetar ARKIVERAde Wal-data
* [max_standby_streaming_delay](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-MAX-STANDBY-STREAMING-DELAY) – anger den maximala fördröjningen innan frågor avbryts när en snabb växlings Server bearbetar STRÖMMAde Wal-data
* [max_sync_workers_per_subscription](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-MAX-SYNC-WORKERS-PER-SUBSCRIPTION) -maximalt antal arbets arbetare per prenumeration för tabell synkronisering
* [max_wal_size](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-MAX-WAL-SIZE) – anger den Wal storlek som utlöser en kontroll punkt
* [min_parallel_index_scan_size](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-MIN-PARALLEL-INDEX-SCAN-SIZE) – anger den minsta mängden index data för en parallell skanning
* [min_wal_size](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-MIN-WAL-SIZE) -anger minimi storleken för att minska Wal till
* [operator_precedence_warning](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-OPERATOR-PRECEDENCE-WARNING) -skapar en varning för konstruktioner som har ändrat innebörd sedan postgresql 9,4
* [parallel_setup_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-PARALLEL-SETUP-COST) – ställer in Planerarens beräkning av kostnaden för att starta arbets processer för parallell fråga
* [parallel_tuple_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-PARALLEL-TUPLE-COST) – anger en uppskattning av kostnaden för att skicka varje tupel (rad) från arbetare till huvud server del
* [pg_stat_statements. Save](https://www.postgresql.org/docs/current/pgstatstatements.html#id-1.11.7.38.8) – sparar pg_stat_statements statistik över Server avstängningar
* [pg_stat_statements. Track](https://www.postgresql.org/docs/current/pgstatstatements.html#id-1.11.7.38.8) – väljer vilka instruktioner som spåras av pg_stat_statements
* [pg_stat_statements. track_utility](https://www.postgresql.org/docs/current/pgstatstatements.html#id-1.11.7.38.8) – väljer om verktygs kommandon spåras av pg_stat_statements
* [quote_all_identifiers](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-QUOTE-ALL-IDENTIFIERS) – när du genererar SQL-fragment, offerter alla identifierare
* [random_page_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-RANDOM-PAGE-COST) – anger en uppskattning av kostnaden för en disk sida som inte är sekventiellt hämtade
* [row_security](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-ROW-SECURITY) – aktiverar rad säkerhet
* [search_path](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SEARCH-PATH) – anger schema Sök ordningen för namn som inte är schema-kvalificerade
* [seq_page_cost](https://www.postgresql.org/docs/current/runtime-config-query.html#GUC-SEQ-PAGE-COST) – anger en uppskattning av kostnaden för en sekventiellt hämtade disk sida
* [session_replication_role](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-SESSION-REPLICATION-ROLE) – anger sessionens beteende för utlösare och omskrivnings regler
* [standard_conforming_strings](https://www.postgresql.org/docs/current/runtime-config-compatible.html#id-1.6.6.16.2.2.7.1.3) -orsaker "..." strängar för att hantera omvända snedstreck bokstavligen
* [statement_timeout](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-STATEMENT-TIMEOUT) -anger högsta tillåtna varaktighet (i millisekunder) för en instruktion. 0 inaktiverar detta
* [synchronize_seqscans](https://www.postgresql.org/docs/current/runtime-config-compatible.html#id-1.6.6.16.2.2.8.1.3) – aktiverar synkroniserade sekventiella genomsökningar
* [synchronous_commit](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-SYNCHRONOUS-COMMIT) – anger den aktuella transaktionens synkroniseringsnivå
* [tcp_keepalives_count](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-TCP-KEEPALIVES-COUNT) -maximalt antal TCP keepalive-återsändningar
* [tcp_keepalives_idle](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-TCP-KEEPALIVES-IDLE) -tid mellan att skicka TCP keepalive-certifikat
* [tcp_keepalives_interval](https://www.postgresql.org/docs/current/runtime-config-connection.html#GUC-TCP-KEEPALIVES-INTERVAL) tiden mellan TCP keepalive-återsändningar
* [temp_buffers](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-TEMP-BUFFERS) – anger det maximala antalet tillfälliga buffertar som används av varje databas-session
* [temp_tablespaces](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-TEMP-TABLESPACES) – anger de tabell utrymmen som ska användas för temporära tabeller och sorterings filer
* [track_activities](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-ACTIVITIES) – samlar in information om att köra kommandon
* [track_counts](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-COUNTS) – samlar in statistik för databas aktivitet
* [track_functions](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-FUNCTIONS) – samlar in statistik på funktions nivå för databas aktivitet
* [track_io_timing](https://www.postgresql.org/docs/current/runtime-config-statistics.html#GUC-TRACK-IO-TIMING) – samlar in tids statistik för databas-I/O-aktivitet
* [transform_null_equals](https://www.postgresql.org/docs/current/runtime-config-compatible.html#GUC-TRANSFORM-NULL-EQUALS) -behandlar "UTTR = null" som "uttryck är null"
* [vacuum_cost_delay](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-DELAY) -vakuum-kostnads fördröjning i millisekunder
* [vacuum_cost_limit](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-LIMIT) -vakuum kostnads belopp tillgängligt innan napping
* [vacuum_cost_page_dirtys](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-PAGE-DIRTY) vakuum kostnad för en sida som dirtieds av vakuum
* [vacuum_cost_page_hit](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-PAGE-HIT) -vakuum-kostnad för en sida som finns i bufferten
* [vacuum_cost_page_miss](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-VACUUM-COST-PAGE-MISS) -vakuum-kostnad för en sida som inte finns i bufferten
* [vacuum_defer_cleanup_age](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-VACUUM-DEFER-CLEANUP-AGE) -antalet transaktioner med vilka vakuum och snabb rensning ska skjutas upp, om något
* [vacuum_freeze_min_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-FREEZE-MIN-AGE) -lägsta ålder då vakuum ska låsa en tabell rad
* [vacuum_freeze_table_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-FREEZE-TABLE-AGE) -ålder där vakuum genomsöker hela tabellen för att frysa tupler
* [vacuum_multixact_freeze_min_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-MULTIXACT-FREEZE-MIN-AGE) -lägsta ålder som vakuum ska låsa en MultiXactId på en tabell rad
* [vacuum_multixact_freeze_table_age](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-VACUUM-MULTIXACT-FREEZE-TABLE-AGE) -multixact ålder där vakuum ska genomsöka hela tabellen för att frysa tupler
* [wal_receiver_status_interval](https://www.postgresql.org/docs/current/runtime-config-replication.html#GUC-WAL-RECEIVER-STATUS-INTERVAL) – anger det maximala intervallet mellan status rapporter för Wal-mottagare och primär
* [wal_writer_delay](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-WAL-WRITER-DELAY) tiden mellan Wal-tömningar som utförs i Wal-skrivaren
* [wal_writer_flush_after](https://www.postgresql.org/docs/current/runtime-config-wal.html#GUC-WAL-WRITER-FLUSH-AFTER) -mängden Wal som skrivits av Wal Writer som utlöser en tömning
* [work_mem](https://www.postgresql.org/docs/current/runtime-config-resource.html#GUC-WORK-MEM) – anger mängden minne som ska användas av interna sorterings åtgärder och hash-tabeller innan de skrivs till temporära diskpartitioner
* [xmlbinary](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-XMLBINARY) – anger hur binära värden ska kodas i XML
* [xmloption](https://www.postgresql.org/docs/current/runtime-config-client.html#GUC-XMLOPTION) – anger om XML-data i implicit tolknings-och serialiserings åtgärd ska betraktas som dokument eller innehålls fragment

## <a name="next-steps"></a>Nästa steg

* En annan typ av konfiguration, förutom Server parametrar, är resurs [konfigurations alternativen](concepts-hyperscale-configuration-options.md) i en citus-Server (storskalig).
* Den underliggande data basen för PostgreSQL har också [konfigurations parametrar](http://www.postgresql.org/docs/current/static/runtime-config.html).
