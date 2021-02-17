---
title: Gransknings loggning – Azure Database for PostgreSQL-enskild server
description: Begrepp för pgAudit gransknings loggning i Azure Database for PostgreSQL-enskild server.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/28/2020
ms.openlocfilehash: 33fa6420f52cae9c869cc75a04ea82de0ec48262
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100596304"
---
# <a name="audit-logging-in-azure-database-for-postgresql---single-server"></a>Gransknings loggning i Azure Database for PostgreSQL-enskild server

Gransknings loggning av databas aktiviteter i Azure Database for PostgreSQL – enskild server är tillgänglig via PostgreSQL gransknings tillägg: [pgAudit](https://www.pgaudit.org/). pgAudit tillhandahåller detaljerad loggning av sessioner och/eller objekt granskning.

> [!NOTE]
> pgAudit är en för hands version av Azure Database for PostgreSQL.
> Tillägget kan bara aktive ras på Generell användning och minnesoptimerade servrar.

Om du vill ha Azures resurs nivå loggar för åtgärder som beräkning och lagrings skalning, se [Azure aktivitets logg](../azure-monitor/essentials/platform-logs-overview.md).

## <a name="usage-considerations"></a>Användnings överväganden
Som standard genereras pgAudit-logginstruktioner tillsammans med dina vanliga logginstruktioner med hjälp av standardfunktionen för loggning i Postgres. I Azure Database for PostgreSQL kan dessa .log-filer laddas ned via Azure-portalen eller CLI. Det maximala lagrings utrymmet för samling av filer är 1 GB och varje fil är tillgänglig i högst sju dagar (Standardvärdet är tre dagar). Den här tjänsten är ett alternativ för kortsiktig lagring.

Alternativt kan du konfigurera alla loggar så att de skickas till Azure Monitor logg lager för senare analys i Log Analytics. Om du aktiverar Azure Monitor resurs loggning skickas loggarna automatiskt (i JSON-format) till Azure Storage, Event Hubs och/eller Azure Monitor loggar, beroende på vad du väljer.

Aktivering av pgAudit genererar en stor volym av loggning på en server, vilket påverkar prestanda och logglagring. Vi rekommenderar att du använder Azure Monitor loggar som erbjuder lagrings alternativ på längre sikt, samt analys-och aviserings funktioner. Vi rekommenderar att du inaktiverar standardloggning för att minska prestandapåverkan från ytterligare loggning:

   1. Ange parametern `logging_collector` till av. 
   2. Starta om servern för att tillämpa den här ändringen.

Information om hur du konfigurerar loggning för Azure Storage, Event Hubs eller Azure Monitor loggar finns i avsnittet resurs loggar i [artikeln Server loggar](concepts-server-logs.md).

## <a name="installing-pgaudit"></a>Installerar pgAudit

Om du vill installera pgAudit måste du ta med den i serverns delade preload-bibliotek. En ändring av postgres- `shared_preload_libraries` parametern kräver att en omstart av servern börjar gälla. Du kan ändra parametrar med hjälp av [Azure Portal](howto-configure-server-parameters-using-portal.md), [Azure CLI](howto-configure-server-parameters-using-cli.md)eller [REST API](/rest/api/postgresql/configurations/createorupdate).

Använda [Azure Portal](https://portal.azure.com):

   1. Välj din Azure Database for PostgreSQL-server.
   2. Välj **Server parametrar** på sid panelen.
   3. Sök efter `shared_preload_libraries` parametern.
   4. Välj **pgaudit**.
   5. Starta om servern för att tillämpa ändringen.

   6. Anslut till servern med hjälp av en klient (som psql) och aktivera pgAudit-tillägget
      ```SQL
      CREATE EXTENSION pgaudit;
      ```

> [!TIP]
> Om du ser ett fel bekräftar du att du har startat om servern när du har sparat `shared_preload_libraries` .

## <a name="pgaudit-settings"></a>pgAudit-inställningar

med pgAudit kan du Konfigurera loggning av session-eller objekt granskning. I [gransknings loggen för sessioner](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) anges detaljerade loggar för körda instruktioner. Granskning av [objekt granskning](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) är begränsad till vissa relationer. Du kan välja att konfigurera en eller båda typerna av loggning. 

> [!NOTE]
> pgAudit-inställningar har angetts gloabally och kan inte anges på en databas eller roll nivå.

När du har [installerat pgAudit](#installing-pgaudit)kan du konfigurera dess parametrar för att starta loggningen. [PgAudit-dokumentationen](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) innehåller definitionen av varje parameter. Testa parametrarna först och bekräfta att du får det förväntade beteendet.

> [!NOTE]
> Om du ställer in på `pgaudit.log_client` på omdirigeras loggarna till en klient process (som psql) i stället för att skrivas till filen. Den här inställningen bör normalt vara inaktiverad. <br> <br>
> `pgaudit.log_level` är bara aktive rad när `pgaudit.log_client` är på.

> [!NOTE]
> I Azure Database for PostgreSQL `pgaudit.log` kan inte anges med en `-` (minus) Sign-genväg enligt beskrivningen i pgAudit-dokumentationen. Alla obligatoriska instruktionsklasser (READ, WRITE osv.) ska anges var för sig.

### <a name="audit-log-format"></a>Format för granskningsloggar
Varje gransknings post anges i `AUDIT:` närheten av logg radens början. Formatet på resten av posten beskrivs i [pgAudit-dokumentationen](https://github.com/pgaudit/pgaudit/blob/master/README.md#format).

Om du behöver andra fält för att uppfylla gransknings kraven använder du parametern postgres `log_line_prefix` . `log_line_prefix` är en sträng som matas in i början av varje postgres-loggfil. Till exempel `log_line_prefix` tillhandahåller följande inställning tidstämpel, användar namn, databas namn och process-ID:

```
t=%m u=%u db=%d pid=[%p]:
```

Mer information `log_line_prefix` finns i [dokumentationen för postgresql](https://www.postgresql.org/docs/current/runtime-config-logging.html#GUC-LOG-LINE-PREFIX).

### <a name="getting-started"></a>Komma igång
För att snabbt komma igång, ange `pgaudit.log` till `WRITE` och öppna dina loggar för att granska utdata. 

## <a name="viewing-audit-logs"></a>Visa gransknings loggar
Om du använder. log-filer tas gransknings loggarna med i samma fil som fel loggarna för PostgreSQL. Du kan ladda ned loggfiler från Azure- [portalen](howto-configure-server-logs-in-portal.md) eller [CLI](howto-configure-server-logs-using-cli.md). 

Om du använder Azures resurs loggning beror det på hur du kommer åt loggarna på vilken slut punkt du väljer. Information om Azure Storage finns i artikeln [Logga lagrings konto](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) . Information om Event Hubs finns i artikeln [Stream Azure-loggar](../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs) .

För Azure Monitor loggar skickas loggar till den valda arbets ytan. Postgres-loggarna använder samlings läget **AzureDiagnostics** , så att de kan frågas från AzureDiagnostics-tabellen. Fälten i tabellen beskrivs nedan. Läs mer om frågor och aviseringar i Översikt över [Azure Monitor loggar frågor](../azure-monitor/logs/log-query-overview.md) .

Du kan använda den här frågan för att komma igång. Du kan konfigurera aviseringar baserat på frågor.

Sök efter alla postgres-loggar för en viss server under den senaste dagen
```
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>Nästa steg
- [Läs mer om loggning i Azure Database for PostgreSQL](concepts-server-logs.md)
- Lär dig hur du ställer in parametrar med hjälp av [Azure Portal](howto-configure-server-parameters-using-portal.md), [Azure CLI](howto-configure-server-parameters-using-cli.md)eller [REST API](/rest/api/postgresql/configurations/createorupdate).