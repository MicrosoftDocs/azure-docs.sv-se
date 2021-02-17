---
title: Långsamma Query-loggar – Azure Database for MySQL-flexibel Server
description: Beskriver långsamma fråga-loggar som är tillgängliga i Azure Database for MySQL flexibel Server.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: d311ea3158e1f9d53c51fe239103039849597d11
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100579197"
---
# <a name="slow-query-logs-in-azure-database-for-mysql-flexible-server-preview"></a>Långsamma Query-loggar i Azure Database for MySQL flexibel Server (förhands granskning)

> [!IMPORTANT] 
> Azure Database for MySQL-flexibel Server är för närvarande en offentlig för hands version.

I Azure Database for MySQL flexibel Server är den långsamma fråge loggen tillgänglig för användare att konfigurera och komma åt. Långsamma frågemeddelanden är inaktiverade som standard och kan aktive ras för att hjälpa till att identifiera Flask halsar i prestanda under fel sökning.

Mer information om den långsamma frågekörning i MySQL finns i avsnittet om [långsamma frågor](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) i dokumentationen för MySQL-motorn.

## <a name="configure-slow-query-logging"></a>Konfigurera loggning av långsam fråga 
Som standard är den långsamma fråge loggen inaktive rad. Om du vill aktivera loggar ställer du in `slow_query_log` Server parametern på *på*. Detta kan konfigureras med hjälp av Azure Portal eller Azure CLI <!-- add link to server parameter-->. 

Andra parametrar du kan ändra för att kontrol lera hur långsamma frågor fungerar är:

- **long_query_time**: logga en fråga om den tar längre tid än `long_query_time` (i sekunder) att slutföras. Standardvärdet är 10 sekunder.
- **log_slow_admin_statements**: bestämmer om administrativa uppgifter (t. ex. `ALTER_TABLE`, `ANALYZE_TABLE` ) loggas.
- **log_queries_not_using_indexes**: anger om frågor som inte använder index ska loggas.
- **log_throttle_queries_not_using_indexes**: begränsar antalet icke-indexerade frågor som kan skrivas till den långsamma fråge loggen. Den här parametern börjar gälla när `log_queries_not_using_indexes` har angetts till *på*

> [!IMPORTANT]
> Om dina tabeller inte är indexerade kan inställning av `log_queries_not_using_indexes` `log_throttle_queries_not_using_indexes` parametrarna och **på on** påverka MySQL-prestanda eftersom alla frågor som körs mot dessa icke-indexerade tabeller skrivs till den långsamma frågans logg.

Se [logg dokumentationen för en långsam fråga](https://dev.mysql.com/doc/refman/5.7/en/slow-query-log.html) i MySQL för att få en fullständig beskrivning av logg parametrarna för långsamma frågor.

## <a name="access-slow-query-logs"></a>Komma åt långsamma Query-loggar

Långsamma frågeuttryck är integrerade med Azure Monitor diagnostikinställningar. När du har aktiverat långsamma Query-loggar på din MySQL-flexibla Server kan du generera dem till Azure Monitor loggar, Event Hubs eller Azure Storage. Mer information om diagnostiska inställningar finns i [dokumentationen för diagnostikloggar](../../azure-monitor/essentials/platform-logs-overview.md). Mer information om hur du aktiverar diagnostikinställningar i Azure Portal finns i [artikeln om långsam fråga i logg portalen](how-to-configure-slow-query-logs-portal.md#set-up-diagnostics).

I följande tabell beskrivs utdata från den långsamma fråge loggen. Beroende på utmatnings metoden kan de fält som ingår och i vilken ordning de visas variera.

| **Egenskap** | **Beskrivning** |
|---|---|
| `TenantId` | Ditt klient-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated` UTC | Tidstämpel när loggen registrerades i UTC |
| `Type` | Loggens typ. Alltid `AzureDiagnostics` |
| `SubscriptionId` | GUID för den prenumeration som servern tillhör |
| `ResourceGroup` | Namnet på den resurs grupp som servern tillhör |
| `ResourceProvider` | Namnet på resurs leverantören. Alltid `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resurs-URI |
| `Resource` | Namnet på servern |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Namnet på servern |
| `start_time_t` UTC | Tiden då frågan började |
| `query_time_s` | Total tid i sekunder som frågan tog för att köras |
| `lock_time_s` | Total tid i sekunder som frågan låstes |
| `user_host_s` | Användarnamn |
| `rows_sent_s` | Antal rader som skickats |
| `rows_examined_s` | Antal rader som granskas |
| `last_insert_id_s` | [last_insert_id](https://dev.mysql.com/doc/refman/5.7/en/information-functions.html#function_last-insert-id) |
| `insert_id_s` | Infoga ID |
| `sql_text_s` | Fullständig fråga |
| `server_id_s` | Serverns ID |
| `thread_id_s` | Tråd-ID |
| `\_ResourceId` | Resurs-URI |

> [!Note]
> För `sql_text_s` kommer loggen att trunkeras om den överskrider 2048 tecken.

## <a name="analyze-logs-in-azure-monitor-logs"></a>Analysera loggar i Azure Monitor loggar

När dina långsamma fråge loggar är skickas för att Azure Monitor loggar via diagnostikloggar kan du utföra ytterligare analyser av dina långsamma frågor. Nedan visas några exempel frågor som hjälper dig att komma igång. Se till att uppdatera följande med Server namnet.

- Frågor som är längre än 10 sekunder på en viss server

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```

- Lista de 5 vanligaste frågorna på en viss server

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | order by query_time_d desc
    | take 5
    ```

- Sammanfatta långsamma frågor efter minsta, högsta, genomsnittliga och standard avvikelse på en viss server

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count(), min(query_time_d), max(query_time_d), avg(query_time_d), stdev(query_time_d), percentile(query_time_d, 95) by LogicalServerName_s
    ```

- Grafa den långsamma frågans distribution på en viss server

    ```Kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart
    ```

- Visa frågor som är längre än 10 sekunder på alla MySQL-servrar med diagnostiska loggar aktiverade

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>Nästa steg
- Läs mer om [gransknings loggar](concepts-audit-logs.md)
- Konfigurera långsamma fråge loggar från [Azure Portal](how-to-configure-slow-query-logs-portal.md)
<!-- - [How to configure slow query logs from the Azure CLI](howto-configure-server-logs-in-cli.md). -->