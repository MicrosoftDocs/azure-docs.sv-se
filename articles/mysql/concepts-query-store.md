---
title: Frågearkivet – Azure Database for MySQL
description: Lär dig mer om Query Store-funktionen i Azure Database for MySQL som hjälper dig att spåra prestanda över tid.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 5/12/2020
ms.openlocfilehash: 82482b260233994672e603c16fe8cf919c92337f
ms.sourcegitcommit: 2bd0a039be8126c969a795cea3b60ce8e4ce64fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98201033"
---
# <a name="monitor-azure-database-for-mysql-performance-with-query-store"></a>Övervaka Azure Database for MySQL prestanda med Query Store

**Gäller för:** Azure Database for MySQL 5,7, 8,0

Funktionen Query Store i Azure Database for MySQL ger dig ett sätt att spåra frågeresultaten över tid. Query Store fören klar prestanda fel sökningen genom att hjälpa dig att snabbt hitta de allra som körs och de flesta resurs intensiva frågor. Query Store samlar automatiskt in en historik över frågor och körnings statistik och behåller dem för din granskning. Den separerar data efter tids period, så att du kan se databas användnings mönster. Data för alla användare, databaser och frågor lagras i databasen **MySQL** schema i Azure Database for MySQL-instansen.

## <a name="common-scenarios-for-using-query-store"></a>Vanliga scenarier för att använda Query Store

Query Store kan användas i ett antal scenarier, inklusive följande:

- Identifiera försämrat-frågor
- Bestämma hur många gånger en fråga kördes under ett angivet tidsintervall
- Jämför genomsnittlig körnings tid för en fråga över tid i Windows för att se stora delta

## <a name="enabling-query-store"></a>Aktivera Query Store

Frågearkivet är en valbar funktion, så den är inte aktiv som standard på en server. Frågearkivet är aktiverat eller inaktiverat globalt för alla databaser på en viss server och kan inte aktive ras eller inaktive ras per databas.

### <a name="enable-query-store-using-the-azure-portal"></a>Aktivera Query Store med hjälp av Azure Portal

1. Logga in på Azure Portal och välj Azure Database for MySQL-servern.
1. Välj **Server parametrar** i avsnittet **Inställningar** på menyn.
1. Sök efter parametern query_store_capture_mode.
1. Ange värdet till alla och **Spara**.

Så här aktiverar du väntande statistik i Frågearkivet:

1. Sök efter parametern query_store_wait_sampling_capture_mode.
1. Ange värdet till alla och **Spara**.

Låt upp till 20 minuter innan den första data mängden sparas i MySQL-databasen.

## <a name="information-in-query-store"></a>Information i Frågearkivet

Frågearkivet har två butiker:

- Ett körnings statistik Arkiv för att spara information om statistik för körning av frågor.
- En väntande statistik lagring för information om beständig information om väntande statistik.

För att minimera utrymmes användningen sammanställs körnings statistik för körning i körnings statistik arkivet över ett fast, konfigurerbart tids fönster. Informationen i dessa butiker är synlig genom att fråga vyn över frågearkivet.

Följande fråga returnerar information om frågor i Frågearkivet:

```sql
SELECT * FROM mysql.query_store;
```

Eller den här frågan för väntande statistik:

```sql
SELECT * FROM mysql.query_store_wait_stats;
```

## <a name="finding-wait-queries"></a>Hitta väntande frågor

> [!NOTE]
> Väntande statistik bör inte aktive ras under arbets belastningen med hög belastning eller aktive ras på obestämd tid för känsliga arbets belastningar. <br>För arbets belastningar som körs med hög processor användning eller på servrar som kon figurer ATS med lägre virtuella kärnor bör du vara försiktig när du aktiverar väntande statistik. Den bör inte aktive ras på obestämd tid.

Väntande händelse typer kombinerar olika vänte händelser till buckets efter likhet. Frågearkivet innehåller vänte händelse typ, ett särskilt namn på wait-händelsen och frågan i fråga. Om du vill korrelera denna wait-information med frågans körnings statistik innebär det att du får en djupare förståelse för vad som bidrar till att fråga prestanda egenskaperna.

Här följer några exempel på hur du kan få mer insikter om din arbets belastning med väntande statistik i Frågearkivet:

| **Permanenta** | **Åtgärd** |
|---|---|
|Hög lås väntar | Kontrol lera fråge texterna för de berörda frågorna och identifiera målentiteten. Leta i Frågearkivet efter andra frågor som ändrar samma entitet, som körs ofta och/eller har hög varaktighet. När du har identifierat dessa frågor bör du överväga att ändra program logiken till att förbättra samtidigheten eller använda en mindre begränsande isolerings nivå. |
|Hög buffert i/o väntar | Hitta frågorna med ett stort antal fysiska läsningar i Frågearkivet. Om de matchar frågorna med höga i/o-vänte tid, bör du överväga att introducera ett index på den underliggande entiteten för att göra sökningar i stället för genomsökningar. Detta skulle minimera IO-omkostnaderna för frågorna. Kontrol lera **prestanda rekommendationerna** för servern i portalen för att se om det finns några index rekommendationer för den här servern som skulle optimera frågorna. |
|Hög minnes väntan | Hitta de mest krävande minnes frågorna i Frågearkivet. Dessa frågor fördröjer förmodligen ytterligare förloppet för de frågor som påverkas. Kontrol lera **prestanda rekommendationerna** för servern i portalen för att se om det finns några index rekommendationer som skulle optimera dessa frågor. |

## <a name="configuration-options"></a>Konfigurationsalternativ

När Query Store har Aktiver ATS sparas data i 15-minuters agg regerings fönster, upp till 500 distinkta frågor per fönster.

Följande alternativ är tillgängliga för att konfigurera parametrar för Frågearkivet.

| **Parameter** | **Beskrivning** | **Standardvärde** | **Intervall** |
|---|---|---|---|
| query_store_capture_mode | Aktivera/inaktivera funktionen för Query Store baserat på värdet. OBS! om performance_schema är avstängd aktiverar query_store_capture_mode performance_schema och en delmängd av de prestanda schema instrument som krävs för den här funktionen. | ALL | INGEN, ALLA |
| query_store_capture_interval | Hämtnings intervallet för frågearkivet i minuter. Tillåter att du anger det intervall som används för att aggregera frågeresultaten | 15 | 5 - 60 |
| query_store_capture_utility_queries | Aktivera eller inaktivera för att avbilda alla verktygs frågor som körs i systemet. | NO | JA, NEJ |
| query_store_retention_period_in_days | Tids period i dagar för att behålla data i frågearkivet. | 7 | 1 - 30 |

Följande alternativ gäller specifikt för väntande statistik.

| **Parameter** | **Beskrivning** | **Standardvärde** | **Intervall** |
|---|---|---|---|
| query_store_wait_sampling_capture_mode | Gör det möjligt att aktivera/inaktivera väntande statistik. | ALTERNATIVET | INGEN, ALLA |
| query_store_wait_sampling_frequency | Ändrar frekvensen för vänta-sampling i sekunder. 5 till 300 sekunder. | 30 | 5-300 |

> [!NOTE]
> För närvarande ersätter **query_store_capture_mode** den här konfigurationen, vilket innebär att både **query_store_capture_mode** och **query_store_wait_sampling_capture_mode** måste aktive ras för att vänta på att statistik ska fungera. Om **query_store_capture_mode** är inaktive rad inaktive ras väntande statistik, eftersom väntande statistik använder performance_schema aktiverat och query_text som fångas av frågearkivet.

Använd [Azure Portal](howto-server-parameters.md) eller [Azure CLI](howto-configure-server-parameters-using-cli.md) för att hämta eller ange ett annat värde för en parameter.

## <a name="views-and-functions"></a>Vyer och funktioner

Visa och hantera Frågearkivet med följande vyer och funktioner. Alla i [rollen Välj offentlig behörighet](howto-create-users.md#to-create-more-admin-users-in-azure-database-for-mysql) kan använda dessa vyer för att se data i frågearkivet. Dessa vyer är bara tillgängliga i **MySQL** -databasen.

Frågorna normaliseras genom att titta på deras struktur efter att du tagit bort litteraler och konstanter. Om två frågor är identiska förutom literala värden, har de samma hash.

### <a name="mysqlquery_store"></a>mysql.query_store

Den här vyn returnerar alla data i Frågearkivet. Det finns en rad för varje distinkt databas-ID, användar-ID och fråge-ID.

| **Namn** | **Datatyp** | **IS_NULLABLE** | **Beskrivning** |
|---|---|---|---|
| `schema_name`| varchar (64) | NO | Schemats namn |
| `query_id`| bigint (20) | NO| Unikt ID som skapats för den specifika frågan, om samma fråga körs i ett annat schema, genereras ett nytt ID |
| `timestamp_id` | timestamp| NO| Tidsstämpeln som frågan körs i. Detta baseras på query_store_interval konfigurationen|
| `query_digest_text`| longtext| NO| Den normaliserade frågetexten efter borttagning av alla litteraler|
| `query_sample_text` | longtext| NO| Det första utseendet på den faktiska frågan med litteraler|
| `query_digest_truncated` | bit| JA| Anger om frågetexten har trunkerats. Värdet blir Ja om frågan är längre än 1 KB|
| `execution_count` | bigint (20)| NO| Antalet gånger som frågan kördes för det här tidsstämpel-ID: t/under den konfigurerade intervall perioden|
| `warning_count` | bigint (20)| NO| Antal varningar som frågan genererade under den interna|
| `error_count` | bigint (20)| NO| Antal fel som den här frågan genererade under intervallet|
| `sum_timer_wait` | double| JA| Den totala körnings tiden för den här frågan under intervallet|
| `avg_timer_wait` | double| JA| Genomsnittlig körnings tid för den här frågan under intervallet|
| `min_timer_wait` | double| JA| Minsta körnings tid för den här frågan|
| `max_timer_wait` | double| JA| Maximal körnings tid|
| `sum_lock_time` | bigint (20)| NO| Den totala tids åtgången för alla Lås för den här körningen av frågan under den här tids perioden|
| `sum_rows_affected` | bigint (20)| NO| Antal påverkade rader|
| `sum_rows_sent` | bigint (20)| NO| Antal rader som skickats till klienten|
| `sum_rows_examined` | bigint (20)| NO| Antal rader som granskas|
| `sum_select_full_join` | bigint (20)| NO| Antal fullständiga kopplingar|
| `sum_select_scan` | bigint (20)| NO| Antal urvals sökningar |
| `sum_sort_rows` | bigint (20)| NO| Antal sorterade rader|
| `sum_no_index_used` | bigint (20)| NO| Antal gånger som frågan inte använde några index|
| `sum_no_good_index_used` | bigint (20)| NO| Antal gånger som det inte gick att använda några användbara index i motorn för körning av fråga|
| `sum_created_tmp_tables` | bigint (20)| NO| Totalt antal skapade tabell temporära tabeller|
| `sum_created_tmp_disk_tables` | bigint (20)| NO| Totalt antal temporära tabeller som skapats i disk (genererar I/O)|
| `first_seen` | timestamp| NO| Frågans första förekomst (UTC) under agg regerings perioden|
| `last_seen` | timestamp| NO| Den sista förekomsten (UTC) av frågan under detta agg regerings fönster|

### <a name="mysqlquery_store_wait_stats"></a>mysql.query_store_wait_stats

Den här vyn returnerar information om väntande händelser i Frågearkivet. Det finns en rad för varje distinkt databas-ID, användar-ID, fråge-ID och händelse.

| **Namn**| **Datatyp** | **IS_NULLABLE** | **Beskrivning** |
|---|---|---|---|
| `interval_start` | timestamp | NO| Början av intervallet (15 minuters ökning)|
| `interval_end` | timestamp | NO| Slutet av intervallet (15 minuters ökning)|
| `query_id` | bigint (20) | NO| Genererat unikt ID för den normaliserade frågan (från frågearkivet)|
| `query_digest_id` | varchar (32) | NO| Den normaliserade frågetexten efter borttagning av alla litteraler (från Query Store) |
| `query_digest_text` | longtext | NO| Det första utseendet på den faktiska frågan med litteraler (från Query Store) |
| `event_type` | varchar (32) | NO| Vänte händelsens kategori |
| `event_name` | varchar (128) | NO| Namn på wait-händelsen |
| `count_star` | bigint (20) | NO| Antal väntande händelser som samplats under intervallet för frågan |
| `sum_timer_wait_ms` | double | NO| Total vänte tid (i millisekunder) för den här frågan under intervallet |

### <a name="functions"></a>Funktioner

| **Namn**| **Beskrivning** |
|---|---|
| `mysql.az_purge_querystore_data(TIMESTAMP)` | Rensar alla frågedata före den aktuella tidsstämpeln |
| `mysql.az_procedure_purge_querystore_event(TIMESTAMP)` | Rensar alla väntande händelse data före den aktuella tidsstämpeln |
| `mysql.az_procedure_purge_recommendation(TIMESTAMP)` | Rensar rekommendationer vars förfallo datum är före den aktuella tidsstämpeln |

## <a name="limitations-and-known-issues"></a>Begränsningar och kända problem

- Om en MySQL-server har parametern `default_transaction_read_only` på kan Query Store inte samla in data.
- Query Store-funktionen kan avbrytas om den påträffar långa Unicode-frågor ( \> = 6000 byte).
- Kvarhållningsperioden för väntande statistik är 24 timmar.
- Väntande statistik använder exempel för att avbilda en del av händelser. Frekvensen kan ändras med hjälp av parametern `query_store_wait_sampling_frequency` .

## <a name="next-steps"></a>Nästa steg

- Läs mer om [frågor om prestanda insikter](concepts-query-performance-insight.md)
