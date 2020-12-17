---
title: Query Store – Azure Database for PostgreSQL-enskild server
description: I den här artikeln beskrivs funktionen Query Store i Azure Database for PostgreSQL-enskild server.
author: sunilagarwal
ms.author: sunila
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/01/2020
ms.openlocfilehash: 5dff78989eef17f95d8b8dd108baafc53a3f761a
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97657030"
---
# <a name="monitor-performance-with-the-query-store"></a>Övervaka prestanda med Query Store

**Gäller för:** Azure Database for PostgreSQL-enskild server version 9,6 och senare

Funktionen Query Store i Azure Database for PostgreSQL ger dig ett sätt att spåra frågeresultaten över tid. Query Store fören klar prestanda fel sökningen genom att hjälpa dig att snabbt hitta de allra som körs och de flesta resurs intensiva frågor. Query Store samlar automatiskt in en historik över frågor och körnings statistik och behåller dem för din granskning. Den separerar data efter tids period, så att du kan se databas användnings mönster. Data för alla användare, databaser och frågor lagras i en databas med namnet **azure_sys** i Azure Database for PostgreSQL-instansen.

> [!IMPORTANT]
> Ändra inte **azure_sys** -databasen eller dess scheman. Om du gör det förhindras Frågearkivet och relaterade prestanda funktioner från att fungera korrekt.

## <a name="enabling-query-store"></a>Aktivera Query Store
Frågearkivet är en valbar funktion, så den är inte aktiv som standard på en server. Store är aktiverat eller inaktiverat globalt för alla databaser på en viss server och kan inte aktive ras eller stängas av per databas.

### <a name="enable-query-store-using-the-azure-portal"></a>Aktivera Query Store med hjälp av Azure Portal
1. Logga in på Azure Portal och välj Azure Database for PostgreSQL-servern.
2. Välj **Server parametrar** i avsnittet **Inställningar** på menyn.
3. Sök efter `pg_qs.query_capture_mode` parametern.
4. Ange värdet till `TOP` och **Spara**.

Så här aktiverar du väntande statistik i Frågearkivet: 
1. Sök efter `pgms_wait_sampling.query_capture_mode` parametern.
1. Ange värdet till `ALL` och **Spara**.


Du kan också ange dessa parametrar med hjälp av Azure CLI.
```azurecli-interactive
az postgres server configuration set --name pg_qs.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value TOP
az postgres server configuration set --name pgms_wait_sampling.query_capture_mode --resource-group myresourcegroup --server mydemoserver --value ALL
```

Vänta 20 minuter för den första databatchen ska hinna sparas i databasen azure_sys.

## <a name="information-in-query-store"></a>Information i Frågearkivet
Frågearkivet har två butiker:
- En körnings statistik för att spara information om statistik för körning av fråga.
- En väntande stat för att spara information om väntande statistik.

Vanliga scenarier för att använda Query Store är:
- Bestämma hur många gånger en fråga kördes under ett angivet tidsintervall
- Jämför genomsnittlig körnings tid för en fråga över tid i Windows för att se stora delta
- Identifiera de längsta körnings frågorna de senaste X timmarna
- Identifiera de N främsta frågorna som väntar på resurser
- Förstå vänte natur för en viss fråga

För att minimera utrymmes användningen sammanställs körnings statistiken för körning i körnings statistik lagret över en fast, konfigurerbar tids period. Informationen i dessa butiker är synlig genom att fråga vyn över frågearkivet.

## <a name="access-query-store-information"></a>Hämta information om Frågearkivet

Query Store-data lagras i azure_sys-databasen på din postgres-Server. 

Följande fråga returnerar information om frågor i Frågearkivet:
```sql
SELECT * FROM query_store.qs_view; 
``` 

Eller den här frågan för wait-statistik:
```sql
SELECT * FROM query_store.pgms_wait_sampling_view;
```

## <a name="finding-wait-queries"></a>Hitta väntande frågor
Väntande händelse typer kombinerar olika vänte händelser till buckets efter likhet. Frågearkivet innehåller vänte händelse typ, ett särskilt namn på wait-händelsen och frågan i fråga. Om du vill korrelera denna wait-information med frågans körnings statistik innebär det att du får en djupare förståelse för vad som bidrar till att fråga prestanda egenskaperna.

Här följer några exempel på hur du kan få mer insikter om din arbets belastning med väntande statistik i Frågearkivet:

| **Permanenta** | **Åtgärd** |
|---|---|
|Hög lås väntar | Kontrol lera fråge texterna för de berörda frågorna och identifiera målentiteten. Leta i Frågearkivet efter andra frågor som ändrar samma entitet, som körs ofta och/eller har hög varaktighet. När du har identifierat dessa frågor bör du överväga att ändra program logiken till att förbättra samtidigheten eller använda en mindre begränsande isolerings nivå.|
| Hög buffert i/o väntar | Hitta frågorna med ett stort antal fysiska läsningar i Frågearkivet. Om de matchar frågorna med höga i/o-väntan, bör du införa ett index för den underliggande entiteten för att göra det i stället för genomsökningar. Detta skulle minimera IO-omkostnaderna för frågorna. Kontrol lera **prestanda rekommendationerna** för servern i portalen för att se om det finns några index rekommendationer för den här servern som skulle optimera frågorna.|
| Hög minnes väntan | Hitta de mest krävande minnes frågorna i Frågearkivet. Dessa frågor fördröjer förmodligen ytterligare förloppet för de frågor som påverkas. Kontrol lera **prestanda rekommendationerna** för servern i portalen för att se om det finns några index rekommendationer som skulle optimera dessa frågor.|

## <a name="configuration-options"></a>Konfigurationsalternativ
När Query Store har Aktiver ATS sparas data i 15-minuters agg regerings fönster, upp till 500 distinkta frågor per fönster. 

Följande alternativ är tillgängliga för att konfigurera parametrar för Frågearkivet.

| **Parameter** | **Beskrivning** | **Objekt** | **Intervall**|
|---|---|---|---|
| pg_qs pg_qs.query_capture_mode | Anger vilka instruktioner som spåras. | inget | ingen, Top, alla |
| pg_qs pg_qs.max_query_text_length | Anger den maximala fråge längden som kan sparas. Längre frågor kommer att trunkeras. | 6000 | 100 – 10 000 |
| pg_qs pg_qs.retention_period_in_days | Anger kvarhållningsperioden. | 7 | 1 - 30 |
| pg_qs pg_qs.track_utility | Anger om verktygs kommandon spåras | på | på, av |

Följande alternativ gäller specifikt för väntande statistik.

| **Parameter** | **Beskrivning** | **Objekt** | **Intervall**|
|---|---|---|---|
| pgms_wait_sampling pgms_wait_sampling.query_capture_mode | Anger vilka instruktioner som spåras för väntande statistik. | inget | ingen, alla|
| Pgms_wait_sampling Pgms_wait_sampling.history_period | Ange frekvensen, i millisekunder, vid sampling av väntande händelser. | 100 | 1-600000 |

> [!NOTE] 
> **pg_qs. query_capture_mode** ersätter **pgms_wait_sampling. query_capture_mode**. Om pg_qs. query_capture_mode är ingen, har inställningen pgms_wait_sampling. query_capture_mode ingen påverkan.


Använd [Azure Portal](howto-configure-server-parameters-using-portal.md) eller [Azure CLI](howto-configure-server-parameters-using-cli.md) för att hämta eller ange ett annat värde för en parameter.

## <a name="views-and-functions"></a>Vyer och funktioner
Visa och hantera Frågearkivet med följande vyer och funktioner. Alla i den offentliga PostgreSQL-rollen kan använda dessa vyer för att se data i Frågearkivet. Dessa vyer är bara tillgängliga i **azure_sys** -databasen.

Frågorna normaliseras genom att titta på deras struktur efter att du tagit bort litteraler och konstanter. Om två frågor är identiska förutom literala värden, har de samma hash.

### <a name="query_storeqs_view"></a>query_store query_store.qs_view
Den här vyn returnerar alla data i Frågearkivet. Det finns en rad för varje distinkt databas-ID, användar-ID och fråge-ID. 

|**Namn**   |**Typ** | **Referenser**  | **Beskrivning**|
|---|---|---|---|
|runtime_stats_entry_id |bigint | | ID från runtime_stats_entriess tabellen|
|user_id    |OID    |pg_authid. OID  |OID för den användare som körde instruktionen|
|db_id  |OID    |pg_database. OID    |OID för databasen där instruktionen kördes|
|query_id   |bigint  || Intern hash-kod, beräknad från instruktionens parse-träd|
|query_sql_text |Varchar (10000)  || Text för en representativ instruktion. Olika frågor med samma struktur grupperas tillsammans. den här texten är texten för den första av frågorna i klustret.|
|plan_id    |bigint |   |ID för planen som motsvarar den här frågan, inte tillgängligt ännu|
|start_time |timestamp  ||  Frågor sammanställs av tidsbuckets – tids perioden för en Bucket är 15 minuter som standard. Detta är start tiden som motsvarar tidsbucket för den här posten.|
|end_time   |timestamp  ||  Slut tid som motsvarar tidsbucket för den här posten.|
|fjärrproceduranrop  |bigint  || Antal gånger som frågan kördes|
|total_time |dubbel precision   ||  Total körnings tid i millisekunder för fråga|
|min_time   |dubbel precision   ||  Minsta körnings tid för fråga, i millisekunder|
|max_time   |dubbel precision   ||  Maximal tid för frågekörning, i millisekunder|
|mean_time  |dubbel precision   ||  Genomsnittlig tid för körning av fråga, i millisekunder|
|stddev_time|   dubbel precision    ||  Standard avvikelse för frågans körnings tid, i millisekunder |
|raderna   |bigint ||  Totalt antal rader som hämtats eller påverkats av instruktionen|
|shared_blks_hit|   bigint  ||  Totalt antal träffar för delade block-cache med instruktionen|
|shared_blks_read|  bigint  ||  Totalt antal delade block som lästs av instruktionen|
|shared_blks_dirtied|   bigint   || Totalt antal delade block som dirtied av instruktionen |
|shared_blks_written|   bigint  ||  Totalt antal delade block som skrivits av instruktionen|
|local_blks_hit|    bigint ||   Totalt antal lokala block cacheträffar per instruktionen|
|local_blks_read|   bigint   || Totalt antal lokala block som lästs av instruktionen|
|local_blks_dirtied|    bigint  ||  Totalt antal lokala block dirtied med instruktionen|
|local_blks_written|    bigint  ||  Totalt antal lokala block som skrivits av instruktionen|
|temp_blks_read |bigint  || Totalt antal temporära block som lästs av instruktionen|
|temp_blks_written| bigint   || Totalt antal temporära block som skrivits av instruktionen|
|blk_read_time  |dubbel precision    || Total tid som instruktionen ägnat åt att läsa block, i millisekunder (om track_io_timing är aktive rad, annars noll)|
|blk_write_time |dubbel precision    || Total tid som instruktionen ägnat åt att skriva block, i millisekunder (om track_io_timing har Aktiver ATS, annars noll)|
    
### <a name="query_storequery_texts_view"></a>query_store query_store.query_texts_view
Den här vyn returnerar text data i Frågearkivet. Det finns en rad för varje distinkt query_text.

| **Namn** | **Typ** | **Beskrivning** |
|--|--|--|
| query_text_id | bigint | ID för query_textss tabellen |
| query_sql_text | Varchar (10000) | Text för en representativ instruktion. Olika frågor med samma struktur grupperas tillsammans. den här texten är texten för den första av frågorna i klustret. |

### <a name="query_storepgms_wait_sampling_view"></a>query_store query_store.pgms_wait_sampling_view
Den här vyn returnerar information om väntande händelser i Frågearkivet. Det finns en rad för varje distinkt databas-ID, användar-ID, fråge-ID och händelse.

| **Namn** | **Typ** | **Referenser** | **Beskrivning** |
|--|--|--|--|
| user_id | OID | pg_authid. OID | OID för den användare som körde instruktionen |
| db_id | OID | pg_database. OID | OID för databasen där instruktionen kördes |
| query_id | bigint |  | Intern hash-kod, beräknad från instruktionens parse-träd |
| event_type | text |  | Den typ av händelse som server delen väntar på |
| händelse | text |  | Vänte händelse namnet om Server delen väntar på att stoppas |
| fjärrproceduranrop | Integer |  | Antal insamlade händelser |

### <a name="functions"></a>Functions

Query_store Query_store.qs_reset () returnerar void

`qs_reset` ignorerar all statistik som har samlats in hittills i Query Store. Den här funktionen kan bara utföras av Server administratörs rollen.

Query_store Query_store.staging_data_reset () returnerar void

`staging_data_reset` ignorerar all statistik som samlas in i minnet av Frågearkivet (det vill säga data i minnet som inte har tömts till databasen). Den här funktionen kan bara utföras av Server administratörs rollen.


## <a name="azure-monitor"></a>Azure Monitor
Azure Database for PostgreSQL är integrerat med [Azure Monitor diagnostikinställningar](../azure-monitor/platform/diagnostic-settings.md). Med diagnostikinställningar kan du skicka postgres-loggar i JSON-format till [Azure Monitor loggar](../azure-monitor/log-query/log-query-overview.md) för analys och aviseringar, Event Hubs för strömning och Azure Storage för arkivering.

>[!IMPORTANT]
> Den här Diagnostic-funktionen för är bara tillgänglig i Generell användning och minnesoptimerade pris nivåer.

### <a name="configure-diagnostic-settings"></a>Konfigurera diagnostikinställningar
Du kan aktivera diagnostikinställningar för postgres-servern med hjälp av Azure Portal, CLI, REST API och PowerShell. De logg kategorier som ska konfigureras är **QueryStoreRuntimeStatistics** och **QueryStoreWaitStatistics**. 

Så här aktiverar du resurs loggar med hjälp av Azure Portal:

1. I portalen går du till diagnostikinställningar i navigerings menyn på postgres-servern.
2. Välj Lägg till diagnostisk inställning.
3. Ge den här inställningen ett namn.
4. Välj önskad slut punkt (lagrings konto, händelsehubben, Log Analytics).
5. Välj logg typerna **QueryStoreRuntimeStatistics** och **QueryStoreWaitStatistics**.
6. Spara inställningen.

Om du vill aktivera den här inställningen med PowerShell, CLI eller REST API går du till [artikeln diagnostiska inställningar](../azure-monitor/platform/diagnostic-settings.md).

### <a name="json-log-format"></a>JSON-logg format
I följande tabeller beskrivs fälten för de två logg typerna. Beroende på vilken slut punkt för utdata du väljer kan de fält som ingår och i vilken ordning de visas variera.

#### <a name="querystoreruntimestatistics"></a>QueryStoreRuntimeStatistics
|**Fält** | **Beskrivning** |
|---|---|
| TimeGenerated [UTC] | Tidstämpel när loggen registrerades i UTC |
| ResourceId | Postgres-serverns Azure-resurs-URI |
| Kategori | `QueryStoreRuntimeStatistics` |
| OperationName | `QueryStoreRuntimeStatisticsEvent` |
| LogicalServerName_s | Postgres Server namn | 
| runtime_stats_entry_id_s | ID från runtime_stats_entriess tabellen |
| user_id_s | OID för den användare som körde instruktionen |
| db_id_s | OID för databasen där instruktionen kördes |
| query_id_s | Intern hash-kod, beräknad från instruktionens parse-träd |
| end_time_s | Slut tid som motsvarar tids perioden för den här posten |
| calls_s | Antal gånger som frågan kördes |
| total_time_s | Total körnings tid i millisekunder för fråga |
| min_time_s | Minsta körnings tid för fråga, i millisekunder |
| max_time_s | Maximal tid för frågekörning, i millisekunder |
| mean_time_s | Genomsnittlig tid för körning av fråga, i millisekunder |
| ResourceGroup | Resurs gruppen | 
| SubscriptionId | Ditt prenumerations-ID |
| ResourceProvider | `Microsoft.DBForPostgreSQL` | 
| Resurs | Postgres Server namn |
| ResourceType | `Servers` | 


#### <a name="querystorewaitstatistics"></a>QueryStoreWaitStatistics
|**Fält** | **Beskrivning** |
|---|---|
| TimeGenerated [UTC] | Tidstämpel när loggen registrerades i UTC |
| ResourceId | Postgres-serverns Azure-resurs-URI |
| Kategori | `QueryStoreWaitStatistics` |
| OperationName | `QueryStoreWaitEvent` |
| user_id_s | OID för den användare som körde instruktionen |
| db_id_s | OID för databasen där instruktionen kördes |
| query_id_s | Frågans interna hash-kod |
| calls_s | Antal insamlade händelser |
| event_type_s | Den typ av händelse som server delen väntar på |
| event_s | Vänte händelse namnet om Server delen väntar på att vänta |
| start_time_t | Händelsens start tid |
| end_time_s | Händelsens slut tid | 
| LogicalServerName_s | Postgres Server namn | 
| ResourceGroup | Resurs gruppen | 
| SubscriptionId | Ditt prenumerations-ID |
| ResourceProvider | `Microsoft.DBForPostgreSQL` | 
| Resurs | Postgres Server namn |
| ResourceType | `Servers` | 

## <a name="limitations-and-known-issues"></a>Begränsningar och kända problem
- Om en PostgreSQL-Server har parametern default_transaction_read_only på, kan Query Store inte samla in data.
- Query Store-funktionen kan avbrytas om den påträffar långa Unicode-frågor (>= 6000 byte).
- [Läs repliker](concepts-read-replicas.md) replikerar Query Store-data från den primära servern. Det innebär att en Läs repliks Frågearkivet inte tillhandahåller statistik om frågor som körs på Läs repliken.


## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [scenarier där Query Store kan vara särskilt användbart](concepts-query-store-scenarios.md).
- Lär dig mer om [metod tips för att använda Query Store](concepts-query-store-best-practices.md).
