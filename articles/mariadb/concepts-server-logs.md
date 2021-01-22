---
title: Långsamma Query-loggar – Azure Database for MariaDB
description: Beskriver de loggar som är tillgängliga i Azure Database for MariaDB och de tillgängliga parametrarna för att aktivera olika loggnings nivåer.
author: savjani
ms.author: pariks
ms.service: jroth
ms.topic: conceptual
ms.date: 11/6/2020
ms.openlocfilehash: 2d3c3c92cc4d0da38f18c7895f4f7f3f3385c653
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98664171"
---
# <a name="slow-query-logs-in-azure-database-for-mariadb"></a>Långsamma Query-loggar i Azure Database for MariaDB
I Azure Database for MariaDB är den långsamma fråge loggen tillgänglig för användare. Åtkomst till transaktions loggen stöds inte. Den långsamma frågans logg kan användas för att identifiera Flask halsar i prestanda för fel sökning.

Mer information om den långsamma frågans logg finns i MariaDB-dokumentationen för [långsam frågekörning](https://mariadb.com/kb/en/library/slow-query-log-overview/).

När [query Store](concepts-query-store.md) är aktiverat på servern kan du se frågorna som " `CALL mysql.az_procedure_collect_wait_stats (900, 30);` " loggade i dina långsamma frågemeddelanden. Det här beteendet förväntas när funktionen Query Store samlar in statistik om dina frågor. 

## <a name="configure-slow-query-logging"></a>Konfigurera loggning av långsam fråga
Som standard är den långsamma frågans logg inaktive rad. Om du vill aktivera det anger `slow_query_log` du till på. Detta kan aktive ras med Azure Portal eller Azure CLI. 

Andra parametrar som du kan justera är:

- **long_query_time**: om en fråga tar längre tid än long_query_time (i sekunder) som frågan loggas. Standardvärdet är 10 sekunder.
- **log_slow_admin_statements**: om on innehåller administrativa uttryck som ALTER_TABLE och ANALYZE_TABLE i de instruktioner som skrivs till slow_query_log.
- **log_queries_not_using_indexes**: bestämmer om frågor som inte använder index ska loggas i slow_query_log
- **log_throttle_queries_not_using_indexes**: den här parametern begränsar antalet icke-indexfrågor som kan skrivas till den långsamma fråge loggen. Den här parametern börjar gälla när log_queries_not_using_indexes är inställt på på.
- **log_output**: om "File", tillåter att den långsamma fråge loggen skrivs till både den lokala serverns lagrings plats och för att Azure Monitor diagnostikloggar. Om du anger ”None” skrivs loggen för långsamma frågor bara till Azure Monitor Diagnostic Logs. 

> [!IMPORTANT]
> Om dina tabeller inte är indexerade kan inställning av `log_queries_not_using_indexes` `log_throttle_queries_not_using_indexes` parametrarna och på på påverka MariaDB prestanda eftersom alla frågor som körs mot dessa icke-indexerade tabeller skrivs till den långsamma frågans logg.<br><br>
> Om du planerar att logga långsamma förfrågningar under en längre tid, rekommenderar vi att du anger `log_output` till "ingen". Om detta är inställt på "File" skrivs dessa loggar till den lokala serverns lagring och kan påverka MariaDB-prestanda. 

En fullständig beskrivning av logg parametrarna för långsamma frågor finns i dokumentationen om MariaDB [långsam logg dokumentation](https://mariadb.com/kb/en/library/slow-query-log-overview/) .

## <a name="access-slow-query-logs"></a>Komma åt långsamma Query-loggar
Det finns två alternativ för att komma åt långsamma Query-loggar i Azure Database for MariaDB: lokala server lagrings-eller Azure Monitor diagnostikloggar. Detta anges med hjälp av `log_output` parametern.

För lokal server lagring kan du Visa och ladda ned långsamma frågemeddelanden med hjälp av Azure Portal eller Azure CLI. I Azure Portal navigerar du till servern i Azure Portal. Under **övervaknings** rubriken väljer du sidan **Server loggar** . Mer information om Azure CLI finns i [Konfigurera och komma åt Server loggar med Azure CLI](howto-configure-server-logs-cli.md). 

Med Azure Monitor diagnostikloggar kan du skicka en långsam loggfil till Azure Monitor loggar (Log Analytics), Azure Storage eller Event Hubs. Se [nedan](concepts-server-logs.md#diagnostic-logs) för mer information.

## <a name="local-server-storage-log-retention"></a>Kvarhållning av lagrings logg för lokal server
När du loggar till serverns lokala lagring är loggarna tillgängliga i upp till sju dagar från skapandet. Om den totala storleken på de tillgängliga loggarna överstiger 7 GB, tas de äldsta filerna bort tills utrymmet är tillgängligt.

Loggarna roteras var 24: e timme eller 7 GB, beroende på vilket som kommer först.

> [!Note]
> Logg kvarhållning ovan gäller inte för loggar som är skickas med Azure Monitor diagnostikloggar. Du kan ändra kvarhållningsperioden för data mottagare som skickas till (t. ex. Azure Storage).

## <a name="diagnostic-logs"></a>Diagnostikloggar
Azure Database for MariaDB är integrerat med Azure Monitor diagnostikloggar. När du har aktiverat långsamma Query-loggar på MariaDB-servern kan du välja att de ska skickas till Azure Monitor loggar, Event Hubs eller Azure Storage. Om du vill veta mer om hur du aktiverar diagnostikloggar kan du läsa avsnittet så här i [dokumentationen för diagnostikloggar](../azure-monitor/platform/platform-logs-overview.md).

I följande tabell beskrivs vad som finns i varje logg. Beroende på utmatnings metoden kan de fält som ingår och i vilken ordning de visas variera.

| **Egenskap** | **Beskrivning** |
|---|---|
| `TenantId` | Ditt klient-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated` UTC | Tidstämpel när loggen registrerades i UTC |
| `Type` | Loggens typ. Alltid `AzureDiagnostics` |
| `SubscriptionId` | GUID för den prenumeration som servern tillhör |
| `ResourceGroup` | Namnet på den resurs grupp som servern tillhör |
| `ResourceProvider` | Namnet på resurs leverantören. Alltid `MICROSOFT.DBFORMARIADB` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resurs-URI |
| `Resource` | Namnet på servern |
| `Category` | `MySqlSlowLogs` |
| `OperationName` | `LogEvent` |
| `Logical_server_name_s` | Namnet på servern |
| `start_time_t` UTC | Tiden då frågan började |
| `query_time_s` | Total tid som frågan tog att köra |
| `lock_time_s` | Total tid då frågan låstes |
| `user_host_s` | Användarnamn |
| `rows_sent_s` | Antal rader som skickats |
| `rows_examined_s` | Antal rader som granskas |
| `last_insert_id_s` | [last_insert_id](https://mariadb.com/kb/en/library/last_insert_id/) |
| `insert_id_s` | Infoga ID |
| `sql_text_s` | Fullständig fråga |
| `server_id_s` | Server-ID |
| `thread_id_s` | Tråd-ID |
| `\_ResourceId` | Resurs-URI |

> [!Note]
> För `sql_text` kommer loggen att trunkeras om den överskrider 2048 tecken.

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

- Visa frågor som är längre än 10 sekunder på alla MariaDB-servrar med diagnostiska loggar aktiverade

    ```Kusto
    AzureDiagnostics
    | where Category == 'MySqlSlowLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, start_time_t , query_time_d, sql_text_s 
    | where query_time_d > 10
    ```    
    
## <a name="next-steps"></a>Nästa steg
- [Så här konfigurerar du långsamma Query-loggar från Azure Portal](howto-configure-server-logs-portal.md)
- [Så här konfigurerar du långsamma Query-loggar från Azure CLI](howto-configure-server-logs-cli.md)
