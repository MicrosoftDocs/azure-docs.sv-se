---
title: Gransknings loggar – Azure Database for MySQL
description: Beskriver de gransknings loggar som är tillgängliga i Azure Database for MySQL och de tillgängliga parametrarna för att aktivera loggnings nivåer.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/24/2020
ms.openlocfilehash: fa845e7c402073a64f51d75b1da51f56142eee2e
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98630510"
---
# <a name="audit-logs-in-azure-database-for-mysql"></a>Gransknings loggar i Azure Database for MySQL

I Azure Database for MySQL är gransknings loggen tillgänglig för användare. Gransknings loggen kan användas för att spåra aktivitet på databas nivå och används ofta för efterlevnad.

## <a name="configure-audit-logging"></a>Konfigurera gransknings loggning

>[!IMPORTANT]
> Vi rekommenderar att du bara loggar de händelse typer och användare som krävs för gransknings syfte för att säkerställa att serverns prestanda inte påverkas kraftigt.

Som standard är gransknings loggen inaktive rad. Om du vill aktivera det anger `audit_log_enabled` du till på.

Andra parametrar som du kan justera är:

- `audit_log_events`: styr vilka händelser som ska loggas. Se nedanstående tabell för vissa gransknings händelser.
- `audit_log_include_users`: MySQL-användare ska inkluderas för loggning. Standardvärdet för den här parametern är tomt, som innehåller alla användare som ska loggas. Detta har högre prioritet än `audit_log_exclude_users` . Parameterns max längd är 512 tecken.
- `audit_log_exclude_users`: MySQL-användare ska undantas från loggning. Parameterns max längd är 512 tecken.

> [!NOTE]
> `audit_log_include_users` har högre prioritet än `audit_log_exclude_users` . Till exempel, om `audit_log_include_users`  =  `demouser` och `audit_log_exclude_users`  =  `demouser` , kommer användaren att inkluderas i gransknings loggarna eftersom `audit_log_include_users` har högre prioritet.

| **Händelse** | **Beskrivning** |
|---|---|
| `CONNECTION` | -Anslutnings initiering (lyckades eller misslyckades) <br> – Användarautentisering med annan användare/lösen ord under sessionen <br> -Anslutnings terminering |
| `DML_SELECT`| Välj frågor |
| `DML_NONSELECT` | Infoga/ta bort/uppdatera frågor |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | Frågor som "släpp databas" |
| `DCL` | Frågor som "beviljande behörighet" |
| `ADMIN` | Frågor som "Visa STATUS" |
| `GENERAL` | Alla i DML_SELECT, DML_NONSELECT, DML, DDL, DCL och ADMIN |
| `TABLE_ACCESS` | – Tillgängligt för MySQL 5,7 och MySQL 8,0 <br> – Läs instruktioner för tabell, t. ex. SELECT eller INSERT INTO... SELECT <br> – Tabell borttagnings instruktioner, till exempel ta bort eller TRUNCATE TABLE <br> – Infoga uttryck för tabeller, till exempel infoga eller Ersätt <br> – Tabell uppdaterings instruktioner, till exempel uppdatering |

## <a name="access-audit-logs"></a>Åtkomst till granskningsloggar

Spårningsloggar är integrerade i Azure Monitor Diagnostic Logs. När du har aktiverat spårningsloggar för din MySQL-server kan du skicka dem till Azure Monitor-loggar, händelsehubbar eller Azure Storage. Mer information om hur du aktiverar diagnostikloggar i Azure Portal finns i [artikeln Gransknings logg Portal](howto-configure-audit-logs-portal.md#set-up-diagnostic-logs).

## <a name="diagnostic-logs-schemas"></a>Diagnostiska loggar scheman

I följande avsnitt beskrivs vad som utdata av MySQLs gransknings loggar baserat på händelse typen. Beroende på utmatnings metoden kan de fält som ingår och i vilken ordning de visas variera.

### <a name="connection"></a>Anslutning

| **Egenskap** | **Beskrivning** |
|---|---|
| `TenantId` | Ditt klient-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Tidstämpel när loggen registrerades i UTC |
| `Type` | Loggens typ. Alltid `AzureDiagnostics` |
| `SubscriptionId` | GUID för den prenumeration som servern tillhör |
| `ResourceGroup` | Namnet på den resurs grupp som servern tillhör |
| `ResourceProvider` | Namnet på resurs leverantören. Alltid `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resurs-URI |
| `Resource` | Namnet på servern |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Namnet på servern |
| `event_class_s` | `connection_log` |
| `event_subclass_s` | `CONNECT`, `DISCONNECT` , `CHANGE USER` (endast tillgängligt för MySQL 5,7) |
| `connection_id_d` | Unikt anslutnings-ID som genererats av MySQL |
| `host_s` | Tom |
| `ip_s` | IP-adressen för klienten som ansluter till MySQL |
| `user_s` | Namn på användaren som kör frågan |
| `db_s` | Namnet på databasen som är ansluten till |
| `\_ResourceId` | Resurs-URI |

### <a name="general"></a>Allmänt

Schemat nedan gäller för händelse typerna allmänt, DML_SELECT, DML_NONSELECT, DML, DDL, DCL och ADMIN.

> [!NOTE]
> För `sql_text` kommer loggen att trunkeras om den överskrider 2048 tecken.

| **Egenskap** | **Beskrivning** |
|---|---|
| `TenantId` | Ditt klient-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Tidstämpel när loggen registrerades i UTC |
| `Type` | Loggens typ. Alltid `AzureDiagnostics` |
| `SubscriptionId` | GUID för den prenumeration som servern tillhör |
| `ResourceGroup` | Namnet på den resurs grupp som servern tillhör |
| `ResourceProvider` | Namnet på resurs leverantören. Alltid `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resurs-URI |
| `Resource` | Namnet på servern |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Namnet på servern |
| `event_class_s` | `general_log` |
| `event_subclass_s` | `LOG`, `ERROR` , `RESULT` (endast tillgängligt för MySQL 5,6) |
| `event_time` | Frågans start tid i UTC-tidsstämpel |
| `error_code_d` | Felkod om frågan misslyckades. `0` innebär inget fel |
| `thread_id_d` | ID för tråd som körde frågan |
| `host_s` | Tom |
| `ip_s` | IP-adressen för klienten som ansluter till MySQL |
| `user_s` | Namn på användaren som kör frågan |
| `sql_text_s` | Fullständig frågetext |
| `\_ResourceId` | Resurs-URI |

### <a name="table-access"></a>Tabell åtkomst

> [!NOTE]
> Tabell åtkomst loggar är bara utdata för MySQL 5,7.<br>För `sql_text` kommer loggen att trunkeras om den överskrider 2048 tecken.

| **Egenskap** | **Beskrivning** |
|---|---|
| `TenantId` | Ditt klient-ID |
| `SourceSystem` | `Azure` |
| `TimeGenerated [UTC]` | Tidstämpel när loggen registrerades i UTC |
| `Type` | Loggens typ. Alltid `AzureDiagnostics` |
| `SubscriptionId` | GUID för den prenumeration som servern tillhör |
| `ResourceGroup` | Namnet på den resurs grupp som servern tillhör |
| `ResourceProvider` | Namnet på resurs leverantören. Alltid `MICROSOFT.DBFORMYSQL` |
| `ResourceType` | `Servers` |
| `ResourceId` | Resurs-URI |
| `Resource` | Namnet på servern |
| `Category` | `MySqlAuditLogs` |
| `OperationName` | `LogEvent` |
| `LogicalServerName_s` | Namnet på servern |
| `event_class_s` | `table_access_log` |
| `event_subclass_s` | `READ`, `INSERT` , `UPDATE` eller `DELETE` |
| `connection_id_d` | Unikt anslutnings-ID som genererats av MySQL |
| `db_s` | Namnet på databasen som används |
| `table_s` | Namnet på tabellen har öppnats |
| `sql_text_s` | Fullständig frågetext |
| `\_ResourceId` | Resurs-URI |

## <a name="analyze-logs-in-azure-monitor-logs"></a>Analysera loggar i Azure Monitor loggar

När gransknings loggarna är skickas för att Azure Monitor loggar via diagnostikloggar kan du utföra ytterligare analyser av de granskade händelserna. Nedan visas några exempel frågor som hjälper dig att komma igång. Se till att uppdatera följande med Server namnet.

- Lista allmänna händelser på en viss server

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "general_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last 
    ```

- Visa en lista över ANSLUTNINGS händelser på en viss server

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs' and event_class_s == "connection_log"
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ```

- Sammanfatta granskade händelser på en viss server

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by event_class_s, event_subclass_s, user_s, ip_s
    ```

- Diagrammet gransknings händelse typ distribution på en viss server

    ```kusto
    AzureDiagnostics
    | where LogicalServerName_s == '<your server name>'
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | summarize count() by LogicalServerName_s, bin(TimeGenerated, 5m)
    | render timechart 
    ```

- Visa en lista över granskade händelser över alla MySQL-servrar med diagnostiska loggar som är aktiverade för gransknings loggar

    ```kusto
    AzureDiagnostics
    | where Category == 'MySqlAuditLogs'
    | project TimeGenerated, LogicalServerName_s, event_class_s, event_subclass_s, event_time_t, user_s , ip_s , sql_text_s 
    | order by TimeGenerated asc nulls last
    ``` 

## <a name="next-steps"></a>Nästa steg

- [Så här konfigurerar du gransknings loggar i Azure Portal](howto-configure-audit-logs-portal.md)
