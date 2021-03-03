---
title: Gransknings loggar – Azure Database for MySQL-flexibel Server
description: Beskriver gransknings loggarna som är tillgängliga i Azure Database for MySQL flexibel Server.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 9/21/2020
ms.openlocfilehash: 1232a0753c988f5a28ebba28f9819aa67ce28603
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101718751"
---
# <a name="track-database-activity-with-audit-logs-in-azure-database-for-mysql-flexible-server"></a>Spåra databas aktivitet med gransknings loggar i Azure Database for MySQL flexibel Server

> [!IMPORTANT] 
> Azure Database for MySQL flexibel Server är för närvarande en offentlig för hands version

Azure Database for MySQL flexibel Server ger användare möjlighet att konfigurera gransknings loggar. Gransknings loggar kan användas för att spåra aktiviteter på databas nivå, inklusive anslutning, admin, DDL och DML-händelser. Den här typen av loggar används ofta för att uppfylla efterlevnadskrav.

## <a name="configure-audit-logging"></a>Konfigurera gransknings loggning

>[!IMPORTANT]
> Vi rekommenderar att du bara loggar de händelse typer och användare som krävs för gransknings syfte för att säkerställa att serverns prestanda inte påverkas kraftigt.

Gransknings loggar är inaktiverade som standard. Om du vill aktivera dem ställer du in `audit_log_enabled` Server parametern på *på*. Detta kan konfigureras med hjälp av Azure Portal eller Azure CLI <!-- add link to server parameter-->. 

Andra parametrar som du kan justera för att styra gransknings loggnings beteendet är:

- `audit_log_events`: styr vilka händelser som ska loggas. Se nedanstående tabell för vissa gransknings händelser.
- `audit_log_include_users`: MySQL-användare ska inkluderas för loggning. Standardvärdet för den här parametern är tomt, som innehåller alla användare som ska loggas. Detta har högre prioritet än `audit_log_exclude_users` . Parameterns max längd är 512 tecken.
- `audit_log_exclude_users`: MySQL-användare ska undantas från loggning. Parameterns max längd är 512 tecken.

> [!NOTE]
> `audit_log_include_users` har högre prioritet än `audit_log_exclude_users` . Till exempel, om `audit_log_include_users`  =  `demouser` och `audit_log_exclude_users`  =  `demouser` , kommer användaren att inkluderas i gransknings loggarna eftersom `audit_log_include_users` har högre prioritet.

| **Händelse** | **Beskrivning** |
|---|---|
| `CONNECTION` | -Anslutnings initiering (lyckades eller misslyckades) <br> -Omautentisering av användare med annan användare/lösen ord under sessionen <br> -Anslutnings terminering |
| `DML_SELECT`| Välj frågor |
| `DML_NONSELECT` | Infoga/ta bort/uppdatera frågor |
| `DML` | DML = DML_SELECT + DML_NONSELECT |
| `DDL` | Frågor som "släpp databas" |
| `DCL` | Frågor som "beviljande behörighet" |
| `ADMIN` | Frågor som "Visa STATUS" |
| `GENERAL` | Alla i DML_SELECT, DML_NONSELECT, DML, DDL, DCL och ADMIN |
| `TABLE_ACCESS` | – Läs instruktioner för tabell, t. ex. SELECT eller INSERT INTO... SELECT <br> – Tabell borttagnings instruktioner, till exempel ta bort eller TRUNCATE TABLE <br> – Infoga uttryck för tabeller, till exempel infoga eller Ersätt <br> – Tabell uppdaterings instruktioner, till exempel uppdatering |

## <a name="access-audit-logs"></a>Åtkomst till granskningsloggar

Gransknings loggar är integrerade med Azure Monitor diagnostikinställningar. När du har aktiverat gransknings loggar på din MySQL-flexibla Server kan du generera dem till Azure Monitor loggar, Event Hubs eller Azure Storage. Mer information om diagnostiska inställningar finns i [dokumentationen för diagnostikloggar](../../azure-monitor/essentials/platform-logs-overview.md). Mer information om hur du aktiverar diagnostikinställningar i Azure Portal finns i [artikeln Gransknings logg Portal](how-to-configure-audit-logs-portal.md#set-up-diagnostics).

I följande avsnitt beskrivs utdata från de MySQL-gransknings loggar som baseras på händelse typen. Beroende på utmatnings metoden kan de fält som ingår och i vilken ordning de visas variera.

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
| `event_subclass_s` | `CONNECT`, `DISCONNECT`, `CHANGE USER` |
| `connection_id_d` | Unikt anslutnings-ID som genererats av MySQL |
| `host_s` | Tom |
| `ip_s` | IP-adressen för klienten som ansluter till MySQL |
| `user_s` | Namn på användaren som kör frågan |
| `db_s` | Namnet på databasen som är ansluten till |
| `\_ResourceId` | Resurs-URI |

### <a name="general"></a>Allmänt

Schemat nedan gäller för händelse typerna allmänt, DML_SELECT, DML_NONSELECT, DML, DDL, DCL och ADMIN.

> [!NOTE]
> För `sql_text_s` kommer loggen att trunkeras om den överskrider 2048 tecken.

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
> För `sql_text_s` kommer loggen att trunkeras om den överskrider 2048 tecken.

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
- Läs mer om [långsamma Query-loggar](concepts-slow-query-logs.md)
- Konfigurera gransknings frågans loggar från [Azure Portal](how-to-configure-audit-logs-portal.md)
<!-- - [How to configure audit logs in the Azure portal](howto-configure-audit-logs-portal.md)-->
