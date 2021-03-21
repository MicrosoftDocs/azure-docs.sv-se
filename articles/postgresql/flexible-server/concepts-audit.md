---
title: Gransknings loggning – Azure Database for PostgreSQL – flexibel Server
description: Begrepp för pgAudit gransknings loggning i Azure Database for PostgreSQL-flexibel Server.
author: niklarin
ms.author: nlarin
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/22/2020
ms.openlocfilehash: b344e2a845a9da8333860599bd4ff9041108202f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100588262"
---
# <a name="audit-logging-in-azure-database-for-postgresql---flexible-server"></a>Gransknings loggning i Azure Database for PostgreSQL-flexibel Server

Gransknings loggning av databas aktiviteter i Azure Database for PostgreSQL-flexibel Server är tillgänglig via PostgreSQL gransknings tillägg: [pgAudit](https://www.pgaudit.org/). pgAudit tillhandahåller detaljerad loggning av sessioner och/eller objekt granskning.

> [!IMPORTANT]
> Azure Database for PostgreSQL-flexibel Server är i för hands version

Om du vill ha Azures resurs nivå loggar för åtgärder som beräkning och lagrings skalning, se [Azure aktivitets logg](../../azure-monitor/essentials/platform-logs-overview.md).

## <a name="usage-considerations"></a>Användnings överväganden
Som standard genereras pgAudit-logginstruktioner tillsammans med dina vanliga logginstruktioner med hjälp av standardfunktionen för loggning i Postgres. I Azure Database for PostgreSQL-flexibel Server kan du konfigurera alla loggar som ska skickas till Azure Monitor logg lager för senare analyser i Log Analytics. Om du aktiverar Azure Monitor resurs loggning skickas loggarna automatiskt (i JSON-format) till Azure Storage, Event Hubs och/eller Azure Monitor loggar, beroende på vad du väljer.

Information om hur du konfigurerar loggning för Azure Storage, Event Hubs eller Azure Monitor loggar finns i avsnittet resurs loggar i [artikeln Server loggar](concepts-logging.md).

## <a name="enabling-pgaudit"></a>Aktivera pgAudit

Om du vill aktivera pgAudit måste du ansluta till servern med hjälp av en klient (t. ex. psql) och aktivera pgAudit-tillägget genom att köra följande kommando:
```SQL
CREATE EXTENSION pgaudit;
```

## <a name="pgaudit-settings"></a>pgAudit-inställningar

med pgAudit kan du Konfigurera loggning av session-eller objekt granskning. I [gransknings loggen för sessioner](https://github.com/pgaudit/pgaudit/blob/master/README.md#session-audit-logging) anges detaljerade loggar för körda instruktioner. Granskning av [objekt granskning](https://github.com/pgaudit/pgaudit/blob/master/README.md#object-audit-logging) är begränsad till vissa relationer. Du kan välja att konfigurera en eller båda typerna av loggning. 

> [!NOTE]
> pgAudit-inställningar anges globalt och kan inte anges på en databas eller roll nivå.

När du har [aktiverat pgAudit](#enabling-pgaudit)kan du konfigurera dess parametrar för att starta loggningen. [PgAudit-dokumentationen](https://github.com/pgaudit/pgaudit/blob/master/README.md#settings) innehåller definitionen av varje parameter. Testa parametrarna först och bekräfta att du får det förväntade beteendet.

> [!NOTE]
> Om du ställer in på `pgaudit.log_client` på omdirigeras loggarna till en klient process (som psql) i stället för att skrivas till filen. Den här inställningen bör normalt vara inaktiverad. <br> <br>
> `pgaudit.log_level` är bara aktiverad när `pgaudit.log_client` är på.

> [!NOTE]
> I Azure Database for PostgreSQL-flexibel Server, `pgaudit.log` kan inte anges med en `-` (minus) Sign-genväg enligt beskrivningen i pgAudit-dokumentationen. Alla obligatoriska instruktions klasser (läsa, skriva osv.) måste anges var för sig.

## <a name="audit-log-format"></a>Format för granskningsloggar
Varje gransknings post anges i `AUDIT:` närheten av logg radens början. Formatet på resten av posten beskrivs i [pgAudit-dokumentationen](https://github.com/pgaudit/pgaudit/blob/master/README.md#format).

## <a name="getting-started"></a>Komma igång
För att snabbt komma igång, ange `pgaudit.log` till `WRITE` och öppna dina Server loggar för att granska utdata. 

## <a name="viewing-audit-logs"></a>Visa gransknings loggar
Hur du kommer åt loggarna beror på vilken slut punkt du väljer. Information om Azure Storage finns i artikeln [Logga lagrings konto](../../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) . Information om Event Hubs finns i artikeln [Stream Azure-loggar](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs) .

För Azure Monitor loggar skickas loggar till den valda arbets ytan. Postgres-loggarna använder samlings läget **AzureDiagnostics** , så att de kan frågas från AzureDiagnostics-tabellen. Fälten i tabellen beskrivs nedan. Läs mer om frågor och aviseringar i Översikt över [Azure Monitor loggar frågor](../../azure-monitor/logs/log-query-overview.md) .

Du kan använda den här frågan för att komma igång. Du kan konfigurera aviseringar baserat på frågor.

Sök efter alla pgAudit-poster i postgres-loggar för en viss server under den senaste dagen
```kusto
AzureDiagnostics
| where LogicalServerName_s == "myservername"
| where TimeGenerated > ago(1d) 
| where Message contains "AUDIT:"
```

## <a name="next-steps"></a>Nästa steg
- [Läs mer om loggning i Azure Database for PostgreSQL-flexibel Server](concepts-logging.md)
- [Lär dig att konfigurera loggning i Azure Database for PostgreSQL-flexibel Server och hur du kommer åt loggar](howto-configure-and-access-logs.md)