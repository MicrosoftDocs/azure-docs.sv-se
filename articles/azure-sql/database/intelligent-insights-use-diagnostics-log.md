---
title: Intelligent Insights prestandadiagnostik
description: Intelligent Insights innehåller en diagnostisk logg över prestanda problem för Azure SQL Database och Azure SQL-hanterad instans
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 06/12/2020
ms.openlocfilehash: b03c21eea18c966616154b5cfc5df5d8924fd335
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100589316"
---
# <a name="use-the-intelligent-insights-performance-diagnostics-log-of-azure-sql-database-and-azure-sql-managed-instance-performance-issues"></a>Använd Intelligent Insights prestanda-diagnostikloggar för Azure SQL Database och prestanda problem med Azure SQL-hanterad instans
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Den här sidan innehåller information om hur du använder loggen för prestandadiagnostik som genereras av [intelligent Insights](intelligent-insights-overview.md) av Azure SQL Database och prestanda problem i Azure SQL-hanterad instans, dess format och de data som den innehåller för dina anpassade utvecklings behov. Du kan skicka den här Diagnostic-loggen till [Azure Monitor loggar](../../azure-monitor/insights/azure-sql.md), [Azure Event Hubs](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs), [Azure Storage](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#stream-into-azure-storage)eller en lösning från tredje part för anpassade DevOps-aviseringar och rapporterings funktioner.

> [!NOTE]
> Intelligenta insikter är en förhands gransknings funktion som inte är tillgänglig i följande regioner: Västeuropa, norra Europa, västra USA 1 och östra USA 1.

## <a name="log-header"></a>Logg huvud

Diagnostic-loggen använder JSON-standardformat för att mata in Intelligent Insights resultat. Egenskapen exakt kategori för att komma åt en Intelligent Insights logg är det fasta värdet "SQLInsights".

Loggens rubrik är gemensam och består av den tidsstämpel (TimeGenerated) som visar när en post skapades. Den innehåller också ett resurs-ID (ResourceId) som refererar till den specifika databas som posten avser. Kategorin (kategori), nivån (nivån) och åtgärds namnet (OperationName) är fasta egenskaper vars värden inte ändras. De anger att logg posten är information och att den kommer från Intelligent Insights (SQLInsights).

```json
"TimeGenerated" : "2017-9-25 11:00:00", // time stamp of the log entry
"ResourceId" : "database identifier", // value points to a database resource
"Category": "SQLInsights", // fixed property
"Level" : "Informational", // fixed property
"OperationName" : "Insight", // fixed property
```

## <a name="issue-id-and-database-affected"></a>Problem-ID och databas som påverkas

Egenskapen identifiering av problem (issueId_d) ger ett sätt att på ett unikt sätt spåra prestanda problemen tills de är lösta. Flera händelse poster i logg rapporterings statusen för samma problem kommer att dela samma problem-ID.

Tillsammans med ärende-ID: t rapporterar Diagnostic-loggen start (intervalStartTime_t) och slutdatum (intervalEndTme_t) för den specifika händelsen som är relaterad till ett problem som rapporteras i Diagnostic-loggen.

Egenskapen elastisk pool (elasticPoolName_s) anger vilken elastisk pool databasen med ett problem tillhör. Om databasen inte är en del av en elastisk pool har den här egenskapen inget värde. Databasen där ett problem upptäcktes visas i egenskapen databas namn (databaseName_s).

```json
"intervalStartTime_t": "2017-9-25 11:00", // start of the issue reported time stamp
"intervalEndTme_t":"2017-9-25 12:00", // end of the issue reported time stamp
"elasticPoolName_s" : "", // resource elastic pool (if applicable)
"databaseName_s" : "db_name", // database name
"issueId_d" : 1525, // unique ID of the issue detected
"status_s" : "Active" // status of the issue – possible values: "Active", "Verifying", and "Complete"
```

## <a name="detected-issues"></a>Identifierade problem

Nästa avsnitt i Intelligent Insights prestanda loggen innehåller prestanda problem som upptäcktes genom inbyggd artificiell intelligens. Identifieringar visas i egenskaperna i JSON-diagnostikloggar. Dessa identifieringar består av en kategori för ett problem, effekten av problemet, de frågor som påverkas och måtten. Identifierings egenskaperna kan innehålla flera prestanda problem som har identifierats.

Identifierade prestanda problem rapporteras med följande egenskaps struktur för identifiering:

```json
"detections_s" : [{
"impact" : 1 to 3, // impact of the issue detected, possible values 1-3 (1 low, 2 moderate, 3 high impact)
"category" : "Detectable performance pattern", // performance issue detected, see the table
"details": <Details outputted> // details of an issue (see the table)
}]
```

Identifierade prestanda mönster och information som returneras av diagnostikloggar finns i följande tabell.

### <a name="detection-category"></a>Identifierings kategori

Kategorin kategori (kategori) beskriver kategorin med identifierade prestanda mönster. I följande tabell finns alla möjliga kategorier med identifierade prestanda mönster. Mer information finns i [Felsöka problem med databas prestanda med intelligent Insights](intelligent-insights-troubleshoot-performance.md).

Beroende på det identifierade prestanda problemet skiljer sig detaljerna i den diagnostiska logg filen på lämpligt sätt.

| Identifierbara prestandamönster | Information som returneras |
| :------------------- | ------------------- |
| Når resurs gränser | <li>Resurser som påverkas</li><li>Fråga hashar</li><li>Resurs förbrukning i procent</li> |
| Ökad arbets belastning | <li>Antal frågor vars körning har ökat</li><li>Fråga hashar av frågor med störst bidrag till arbets belastnings ökningen</li> |
| Minnestryck | <li>Minnes ansvarig</li> |
| Låsning | <li>Frågans hashar som påverkas</li><li>Blockerar frågans hash-värden</li> |
| Ökad MAXDOP | <li>Fråga hashar</li><li>CXP vänte tider</li><li>Vänte tider</li> |
| PAGELATCH-konkurrens | <li>Fråga hashar för frågor som orsakar konkurrens</li> |
| Index saknas | <li>Fråga hashar</li> |
| Ny fråga | <li>Fråga hash för de nya frågorna</li> |
| Ovanlig wait-statistik | <li>Ovanliga vänte typer</li><li>Fråga hashar</li><li>Vänte tid för fråga</li> |
| TempDB-konkurrens | <li>Fråga hashar för frågor som orsakar konkurrens</li><li>Frågans tilldelning till den övergripande vänte tiden för databas PAGELATCH [%]</li> |
| DTU-underskott för elastisk pool | <li>Elastisk pool</li><li>Högsta DTU-förbrukar databas</li><li>Procent av pool-DTU som används av topp konsumenten</li> |
| Plan regression | <li>Fråga hashar</li><li>Lämplig plan-ID</li><li>Dåliga plan-ID: n</li> |
| Ändra Database-Scoped konfigurations värde | <li>Konfigurations ändringar i databasen jämfört med standardvärdena</li> |
| Långsam klient | <li>Fråga hashar</li><li>Vänte tider</li> |
| Nedgradering av pris nivå | <li>SMS-meddelande</li> |

### <a name="impact"></a>Påverkan

Egenskapen effekt (effekt) beskriver hur mycket ett identifierat beteende som bidragit till problemet som en databas har. Påverkar intervallet från 1 till 3, med 3 som högsta bidrag, 2 som måttlig och 1 som det lägsta bidraget. Värdet för påverkan kan användas som indatamängd för anpassad aviserings automatisering, beroende på dina behov. Egenskaps frågorna som påverkas (QueryHashes) innehåller en lista över de fråga-hashar som påverkades av en viss identifiering.

### <a name="impacted-queries"></a>Påverkade frågor

Nästa avsnitt av Intelligent Insights loggen innehåller information om specifika frågor som påverkades av de prestanda problem som upptäckts. Den här informationen visas som en matris med objekt som är inbäddade i egenskapen impact_s. Egenskapen påverkan består av entiteter och mått. Entiteter refererar till en viss fråga (typ: fråga). Den unika frågans hash-värde visas under egenskapen Value (värde). Dessutom följs alla frågor som har lämnats av ett mått och ett värde som indikerar ett identifierat prestanda problem.

I följande logg exempel upptäcktes frågan med hash-0x9102EXZ4 för att få en ökad varaktighet för körningen (Metric: DurationIncreaseSeconds). Värdet på 110 sekunder anger att den här specifika frågan tog 110 sekunder längre att köras. Eftersom flera frågor kan identifieras kan detta specifika logg avsnitt innehålla flera poster i frågan.

```json
"impact" : [{
"entity" : {
"Type" : "Query", // type of entity - query
"Value" : "query hash value", // for example "0x9102EXZ4" query hash value },
"Metric" : "DurationIncreaseSeconds", // measured metric and the measurement unit (in this case seconds)
"Value" : 110 // value of the measured metric (in this case seconds)
}]
```

### <a name="metrics"></a>Mått

Mått enheten för varje mått som rapporteras anges under egenskapen Metric (Metric) med möjliga värden i sekunder, tal och procent. Värdet för ett uppmätt mått rapporteras i värde-egenskapen (värde).

Egenskapen DurationIncreaseSeconds tillhandahåller mått enheten i sekunder. Mått enheten CriticalErrorCount är ett tal som representerar ett antal fel.

```json
"metric" : "DurationIncreaseSeconds", // issue metric type – possible values: DurationIncreaseSeconds, CriticalErrorCount, WaitingSeconds
"value" : 102 // value of the measured metric (in this case seconds)
```

## <a name="root-cause-analysis-and-improvement-recommendations"></a>Rotor Saks analys och förbättringar av rekommendationer

Den sista delen av Intelligent Insights prestanda loggen avser den automatiserade rotor Saks analysen av det identifierade problemet för prestanda försämring. Informationen visas i human-vänliga verbiage i egenskapen rotor Saks analys (rootCauseAnalysis_s). Förbättrings rekommendationer ingår i loggen där det är möjligt.

```json
// example of reported root cause analysis of the detected performance issue, in a human-readable format

"rootCauseAnalysis_s" : "High data IO caused performance to degrade. It seems that this database is missing some indexes that could help."
```

Du kan använda Intelligent Insights prestanda logg med [Azure Monitor loggar]( https://docs.microsoft.com/azure/log-analytics/log-analytics-azure-sql) eller en lösning från tredje part för anpassade DevOps-aviseringar och rapporterings funktioner.

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [intelligent Insights](intelligent-insights-overview.md) begrepp.
- Lär dig hur du [felsöker prestanda problem med intelligent Insights](intelligent-insights-troubleshoot-performance.md).
- Lär dig hur du [övervakar prestanda problem med Azure SQL-analys](../../azure-monitor/insights/azure-sql.md).
- Lär dig hur du [samlar in och använder loggdata från dina Azure-resurser](../../azure-monitor/essentials/platform-logs-overview.md).