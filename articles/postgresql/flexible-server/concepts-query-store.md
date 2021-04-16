---
title: Query Store – Azure Database for PostgreSQL – Flex Server
description: Den här artikeln beskriver Query Store-funktionen i Azure Database for PostgreSQL – Flex Server.
author: ssen-msft
ms.author: ssen
ms.service: postgresql
ms.topic: conceptual
ms.date: 04/01/2021
ms.openlocfilehash: 6ab2ea6f6544bcf561e92f00b5afee693393c157
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107559206"
---
# <a name="monitor-performance-with-query-store"></a>Övervaka prestanda med Query Store
**Gäller för:** Azure Database for PostgreSQL – Flex Server version 11 och senare

Query Store-funktionen i Azure Database for PostgreSQL ett sätt att spåra frågeprestanda över tid. Query Store förenklar prestandafelsökning genom att hjälpa dig att snabbt hitta de långvarigaste och mest resursintensiva frågorna. Query Store samlar automatiskt in en historik över frågor och körningsstatistik och behåller dem för granskning. Den segmentar data efter tid så att du kan se användningsmönster för tid. Data för alla användare, databaser och frågor lagras i en databas med **azure_sys** i Azure Database for PostgreSQL instansen.

> [!IMPORTANT]
> Ändra inte **azure_sys** databasen eller dess schema. Detta förhindrar att Query Store och relaterade prestandafunktioner fungerar korrekt.
## <a name="enabling-query-store"></a>Aktivera Query Store
Query Store är en funktion för att välja, så den är inte aktiverad som standard på en server. Query Store är aktiverat eller inaktiverat globalt för alla databaser på en viss server och kan inte aktiveras eller inaktiveras per databas.
### <a name="enable-query-store-using-the-azure-portal"></a>Aktivera Query Store med hjälp av Azure Portal
1. Logga in på Azure Portal och välj din Azure Database for PostgreSQL server.
2. Välj **Serverparametrar** i **avsnittet** Inställningar på menyn.
3. Sök efter `pg_qs.query_capture_mode` parametern .
4. Ange värdet till `TOP` eller `ALL` och **Spara**.
Vänta 20 minuter för den första databatchen ska hinna sparas i databasen azure_sys.
## <a name="information-in-query-store"></a>Information i Query Store
Query Store har ett arkiv:
- Ett körningsstatistikarkiv för att bevara informationen om frågekörningsstatistik.

Vanliga scenarier för att använda Query Store är:
- Fastställa hur många gånger en fråga kördes i ett visst tidsfönster
- Jämföra den genomsnittliga körningstiden för en fråga över tidsfönster för att se stora delta
- Identifiera långvarigaste frågor under de senaste timmarna För att minimera utrymmesanvändningen aggregeras körningsstatistiken i körningsstatistikarkivet över en fast, konfigurerbar tidsperiod. Informationen i dessa butiker kan efterfrågas med hjälp av vyer.
## <a name="access-query-store-information"></a>Få åtkomst till Query Store-information
Query Store-data lagras i azure_sys på Postgres-servern. Följande fråga returnerar information om frågor i Query Store:
```sql
SELECT * FROM query_store.qs_view; 
``` 

## <a name="configuration-options"></a>Konfigurationsalternativ
När Query Store är aktiverat sparas data i 15-minuters aggregeringsfönster, upp till 500 distinkta frågor per fönster. Följande alternativ är tillgängliga för att konfigurera Query Store-parametrar.

| **Parameter** | **Beskrivning** | **Standardvärde** | **Intervall**|
|---|---|---|---|
| pg_qs.query_capture_mode | Anger vilka instruktioner som spåras. | inget | none, top, all |
| pg_qs.max_query_text_length | Anger den maximala frågelängd som kan sparas. Längre frågor trunkeras. | 6000 | 100–10 000 |
| pg_qs.retention_period_in_days | Anger kvarhållningsperioden. | 7 | 1 - 30 |
| pg_qs.track_utility | Anger om verktygskommandon spåras | på | på, av |

Använd Azure Portal [för](howto-configure-server-parameters-using-portal.md) att hämta eller ange ett annat värde för en parameter.

## <a name="views-and-functions"></a>Vyer och funktioner
Visa och hantera Query Store med hjälp av följande vyer och funktioner. Alla i den offentliga PostgreSQL-rollen kan använda dessa vyer för att se data i Query Store. Dessa vyer är bara  tillgängliga i azure_sys databasen.
Frågor normaliseras genom att titta på deras struktur när du har tagit bort literaler och konstanter. Om två frågor är identiska förutom för literalvärden har de samma queryId.
### <a name="query_storeqs_view"></a>query_store.qs_view
Den här vyn returnerar alla data i Query Store. Det finns en rad för varje distinkt databas-ID, användar-ID och fråge-ID. 

|**Namn**   |**Typ** | **Referenser**  | **Beskrivning**|
|---|---|---|---|
|runtime_stats_entry_id |bigint | | ID från runtime_stats_entries tabellen|
|user_id    |Oid    |pg_authid.oid  |OID för användaren som körde instruktionen|
|db_id  |Oid    |pg_database.oid    |OID för databasen där -instruktionen kördes|
|query_id   |bigint  || Intern hashkod som beräknas från -instruktionens parseträd|
|query_sql_text |Varchar(10000)  || Text i ett representativt uttryck. Olika frågor med samma struktur är klustrade. Den här texten är texten för den första av frågorna i klustret.|
|plan_id    |bigint |   |ID för planen som motsvarar den här frågan, inte tillgängligt ännu|
|start_time |timestamp  ||  Frågor aggregeras efter tids buckets – tidsintervallet för en bucket är som standard 15 minuter. Det här är den starttid som motsvarar tids bucketen för den här posten.|
|end_time   |timestamp  ||  Sluttid som motsvarar tids bucketen för den här posten.|
|Samtal  |bigint  || Antal gånger som frågan körs|
|total_time |dubbel precision   ||  Total körningstid för frågor, i millisekunder|
|min_time   |dubbel precision   ||  Minsta körningstid för frågor, i millisekunder|
|max_time   |dubbel precision   ||  Maximal frågekörningstid, i millisekunder|
|mean_time  |dubbel precision   ||  Medelvärde för frågekörningstid, i millisekunder|
|stddev_time|   dubbel precision    ||  Standardavvikelse för frågekörningstiden, i millisekunder |
|Rader   |bigint ||  Totalt antal rader som hämtats eller påverkats av -instruktionen|
|shared_blks_hit|   bigint  ||  Totalt antal träffar för delad blockcache av -instruktionen|
|shared_blks_read|  bigint  ||  Totalt antal delade block som lästs av -instruktionen|
|shared_blks_dirtied|   bigint   || Totalt antal delade block som instruktionen har delat |
|shared_blks_written|   bigint  ||  Totalt antal delade block som skrivits av -instruktionen|
|local_blks_hit|    bigint ||   Totalt antal träffar i lokal blockcache av -instruktionen|
|local_blks_read|   bigint   || Totalt antal lokala block som lästs av -instruktionen|
|local_blks_dirtied|    bigint  ||  Totalt antal lokala block som instruktionen har|
|local_blks_written|    bigint  ||  Totalt antal lokala block som skrivits av -instruktionen|
|temp_blks_read |bigint  || Totalt antal temporära block som lästs av -instruktionen|
|temp_blks_written| bigint   || Totalt antal temporära block som skrivits av -instruktionen|
|blk_read_time  |dubbel precision    || Total tid som instruktionen ägnat åt att läsa block, i millisekunder (om track_io_timing är aktiverat, annars noll)|
|blk_write_time |dubbel precision    || Total tid som instruktionen ägnat åt att skriva block, i millisekunder (om track_io_timing har aktiverats, annars noll)|
    
### <a name="query_storequery_texts_view"></a>query_store.query_texts_view
Den här vyn returnerar frågetextdata i Query Store. Det finns en rad för varje distinkt query_text.

| **Namn** | **Typ** | **Beskrivning** |
|--|--|--|
| query_text_id | bigint | ID för query_texts tabellen |
| query_sql_text | Varchar(10000) | Text i ett representativt uttryck. Olika frågor med samma struktur är klustrade. Den här texten är texten för den första av frågorna i klustret. |

### <a name="functions"></a>Functions
`qs_reset` tar bort all statistik som har samlats in hittills av Query Store. Den här funktionen kan bara köras av serveradministratörsrollen.

`staging_data_reset` tar bort all statistik som samlats in i minnet av Query Store (det vill säga data i minnet som ännu inte har rensats till databasen). Den här funktionen kan bara köras av serveradministratörsrollen.

## <a name="limitations-and-known-issues"></a>Begränsningar och kända problem
- Om en PostgreSQL-server har parametern default_transaction_read_only på samlar Query Store inte in några data.
## <a name="next-steps"></a>Nästa steg
- Läs mer om [scenarier där Query Store kan vara särskilt användbart.](concepts-query-store-scenarios.md)
- Läs mer om [metodtips för att använda Query Store](concepts-query-store-best-practices.md).
