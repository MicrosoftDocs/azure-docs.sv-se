---
title: Azure Application insikter log-baserade mått | Microsoft Docs
description: I den här artikeln visas Azure Application Insights-mått med stödda agg regeringar och dimensioner. Informationen om loggbaserade mått inkluderar de underliggande Kusto-frågeuttryck.
author: vgorbenko
services: azure-monitor
ms.topic: reference
ms.date: 07/03/2019
ms.author: vitalyg
ms.subservice: application-insights
ms.openlocfilehash: 400f239f3e7b736196bf950e81148fa2e39aca96
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100621370"
---
# <a name="application-insights-log-based-metrics"></a>Application Insights log-baserade mått

Med Application Insights loggbaserade mått kan du analysera hälso tillståndet för dina övervakade appar, skapa kraftfulla instrument paneler och konfigurera aviseringar. Det finns två typer av mått:

* [Loggbaserade mått](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics) bakom scenen översätts till Kusto- [frågor](/azure/kusto/query/) från lagrade händelser.
* [Standard mått](../app/pre-aggregated-metrics-log-metrics.md#pre-aggregated-metrics) lagras som församlade tids serier.

Eftersom *standard mått* är församlade under samlingen har de bättre prestanda vid tidpunkten för frågan. Detta gör dem till ett bättre alternativ för instrument paneler och i real tids aviseringar. De *loggbaserade måtten* har fler dimensioner, vilket gör dem till det överordnade alternativet för data analys och ad hoc-diagnostik. Använd [namn områdes väljaren](../platform/metrics-getting-started.md#create-your-first-metric-chart) för att växla mellan log-baserade och standard mått i [Metrics Explorer](../platform/metrics-getting-started.md).

## <a name="interpret-and-use-queries-from-this-article"></a>Tolka och använda frågor från den här artikeln

Den här artikeln innehåller mått med agg regeringar och dimensioner som stöds. Informationen om loggbaserade mått inkluderar de underliggande Kusto-frågeuttryck. För enkelhetens skull använder varje fråga standardvärden för tids kornig het, diagram typ och delar ibland upp dimension som fören klar användningen av frågan i Log Analytics utan att behöva ändra.

När du ritar samma mått i [Metrics Explorer](../platform/metrics-getting-started.md)finns det inga standardvärden – frågan justeras dynamiskt baserat på diagrammets inställningar:

- Det valda **tidsintervallet** översätts till en ytterligare *WHERE-timestamp...* -sats för att endast välja händelser från det valda tidsintervallet. Till exempel, ett diagram som visar data för de senaste 24 timmarna, inkluderar frågan *| där tidsstämpeln > sedan (24 h)*.

- Den valda **tids kornig het** placeras i den slutliga *sammanfattningen... per bin-sats (timestamp, [Time kornig])* .

- Alla valda **filter** dimensioner översätts till ytterligare *WHERE* -satser.

- Den markerade dimensionen för **delad diagram** översätts till en extra sammanfattnings egenskap. Om du till exempel delar diagrammet efter *plats* och ritar med en tids kornig het på 5 minuter sammanfattas *sammanfattnings* satsen *... per Bing (tidsstämpel, 5 m), plats*.

> [!NOTE]
> Om du är nybörjare på Kusto-frågespråket börjar du med att kopiera och klistra in Kusto-uttryck i rutan Log Analytics fråga utan att göra några ändringar. Klicka på **Kör** för att visa det grundläggande diagrammet. När du börjar förstå syntaxen för frågespråket kan du börja göra små ändringar och se hur ändringen påverkar. Att utforska dina egna data är ett bra sätt att börja realisera den fulla kraften hos [Log Analytics](../log-query/log-analytics-tutorial.md) och [Azure Monitor](../overview.md).

## <a name="availability-metrics"></a>Tillgänglighets mått

Mått i kategorin tillgänglighet gör att du kan se hälso tillståndet för ditt webb program som observerats från platser runtom i världen. [Konfigurera tillgänglighets testerna](../app/monitor-web-app-availability.md) för att börja använda mått från den här kategorin.

### <a name="availability-availabilityresultsavailabilitypercentage"></a>Tillgänglighet (availabilityResults/availabilityPercentage)
*Tillgänglighets* måttet visar procent andelen av de webb test körningar som inte identifierade några problem. Det lägsta möjliga värdet är 0, vilket innebär att alla webb test körningar har misslyckats. Värdet 100 innebär att alla webbtester körs och att verifierings villkoren har körts.

|Måttenhet|Agg regeringar som stöds|Dimensioner som stöds|
|---|---|---|---|---|---|
|Procent|Genomsnitt|Körnings plats, test namn|

```Kusto
availabilityResults 
| summarize sum(todouble(success == 1) * 100) / count() by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-test-duration-availabilityresultsduration"></a>Tillgänglighets testets varaktighet (availabilityResults/duration)

Måttet för *tillgänglighets testets varaktighet* visar hur lång tid det tog för webb testet att köras. För [webbtester med flera steg](../app/availability-multistep.md)återspeglar måttet den totala körnings tiden för alla steg.

|Måttenhet|Agg regeringar som stöds|Dimensioner som stöds|
|---|---|---|---|---|---|
|Millisekunder|Genomsnitt, min, max|Körnings plats, test namn, test resultat

```Kusto
availabilityResults
| where notempty(duration)
| extend availabilityResult_duration = iif(itemType == 'availabilityResult', duration, todouble(''))
| summarize sum(availabilityResult_duration)/sum(itemCount) by bin(timestamp, 5m), location
| render timechart
```

### <a name="availability-tests-availabilityresultscount"></a>Tillgänglighets test (availabilityResults/Count)

Måttet *tillgänglighets test* visar antalet webbtester som körs av Azure Monitor.

|Måttenhet|Agg regeringar som stöds|Dimensioner som stöds|
|---|---|---|---|---|---|
|Antal|Antal|Körnings plats, test namn, test resultat|

```Kusto
availabilityResults
| summarize sum(itemCount) by bin(timestamp, 5m)
| render timechart
```

## <a name="browser-metrics"></a>Webb läsar mått

Webb läsar mått samlas in av Application Insights JavaScript SDK från verkliga webbläsare för slutanvändare. De ger fantastiska insikter om användarnas upplevelse med din webbapp. Webb läsar måtten samplas vanligt vis inte, vilket innebär att de ger högre precision av användnings numren jämfört med mått på Server sidan som kan skevas genom sampling.

> [!NOTE]
> För att samla in webb läsar mått måste ditt program instrumenteras med [Application Insights JavaScript SDK](../app/javascript.md).

### <a name="browser-page-load-time-browsertimingstotalduration"></a>Sid inläsnings tid för webbläsare (browserTimings/totalDuration)

|Måttenhet|Agg regeringar som stöds|Föraggregerade dimensioner|
|---|---|---|
|Millisekunder|Genomsnitt, min, max|Inget|

```Kusto
browserTimings
| where notempty(totalDuration)
| extend _sum = totalDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="client-processing-time-browsertimingprocessingduration"></a>Klient bearbetnings tid (browserTiming/processingDuration)

|Måttenhet|Agg regeringar som stöds|Föraggregerade dimensioner|
|---|---|---|
|Millisekunder|Genomsnitt, min, max|Inget|

```Kusto
browserTimings
| where notempty(processingDuration)
| extend _sum = processingDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="page-load-network-connect-time-browsertimingsnetworkduration"></a>Sid inläsning nätverks anslutnings tid (browserTimings/networkDuration)

|Måttenhet|Agg regeringar som stöds|Föraggregerade dimensioner|
|---|---|---|
|Millisekunder|Genomsnitt, min, max|Inget|

```Kusto
browserTimings
| where notempty(networkDuration)
| extend _sum = networkDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="receiving-response-time-browsertimingsreceiveduration"></a>Tar emot svars tid (browserTimings/receiveDuration)

|Måttenhet|Agg regeringar som stöds|Föraggregerade dimensioner|
|---|---|---|
|Millisekunder|Genomsnitt, min, max|Inget|

```Kusto
browserTimings
| where notempty(receiveDuration)
| extend _sum = receiveDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

### <a name="send-request-time-browsertimingssendduration"></a>Sändnings tid för begäran (browserTimings/sendDuration)

|Måttenhet|Agg regeringar som stöds|Föraggregerade dimensioner|
|---|---|---|
|Millisekunder|Genomsnitt, min, max|Inget|

```Kusto
browserTimings
| where notempty(sendDuration)
| extend _sum = sendDuration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render timechart
```

## <a name="failure-metrics"></a>Felaktiga mått

Måtten i **fel** visar problem med bearbetnings begär Anden, beroende anrop och utlösta undantag.

### <a name="browser-exceptions-exceptionsbrowser"></a>Webb läsar undantag (undantag/webbläsare)

Det här måttet visar antalet utlösta undantag från din program kod som körs i webbläsaren. Endast undantag som spåras med ett ```trackException()``` Application Insights API-anrop ingår i måttet.

|Måttenhet|Agg regeringar som stöds|Föraggregerade dimensioner|Kommentarer|
|---|---|---|---|
|Antal|Antal|Inget|Log-baserad version använder **Summa** agg regering|

```Kusto
exceptions
| where notempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-call-failures-dependenciesfailed"></a>Misslyckade beroende anrop (beroenden/misslyckade)

Antalet misslyckade beroende anrop.

|Måttenhet|Agg regeringar som stöds|Föraggregerade dimensioner|Kommentarer|
|---|---|---|---|
|Antal|Antal|Inget|Log-baserad version använder **Summa** agg regering|

```Kusto
dependencies
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="exceptions-exceptionscount"></a>Undantag (undantag/antal)

Varje gång du loggar ett undantag till Application Insights finns det ett anrop till [trackException ()-metoden](../app/api-custom-events-metrics.md#trackexception) i SDK: n. Undantags måttet visar antalet loggade undantag.

|Måttenhet|Agg regeringar som stöds|Föraggregerade dimensioner|Kommentarer|
|---|---|---|---|
|Antal|Antal|Moln roll namn, moln roll instans, enhets typ|Log-baserad version använder **Summa** agg regering|

```Kusto
exceptions
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="failed-requests-requestsfailed"></a>Misslyckade förfrågningar (begär Anden/misslyckade)

Antalet spårade server begär Anden som marker ATS som *misslyckade*. Som standard märker Application Insights SDK automatiskt varje serverbegäran som returnerade HTTP-svarskod 5xx eller 4xx som en misslyckad begäran. Du kan anpassa den här logiken genom att ändra egenskapen  *lyckades* för objektet begär telemetri i en [anpassad telemetri-initierare](../app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer).

|Måttenhet|Agg regeringar som stöds|Föraggregerade dimensioner|Kommentarer|
|---|---|---|---|
|Antal|Antal|Moln roll instans, moln roll namn, verklig eller syntetisk trafik, prestanda för begäran, svarskod|Log-baserad version använder **Summa** agg regering|

```Kusto
requests
| where success == 'False'
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-exceptions-exceptionsserver"></a>Server undantag (undantag/Server)

Det här måttet visar antalet Server undantag.

|Måttenhet|Agg regeringar som stöds|Föraggregerade dimensioner|Kommentarer|
|---|---|---|---|
|Antal|Antal|Moln roll namn, moln roll instans|Log-baserad version använder **Summa** agg regering|

```Kusto
exceptions
| where isempty(client_Browser)
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

## <a name="performance-counters"></a>Prestandaräknare

Använd mått i kategorin **prestanda räknare** för att få åtkomst till [system prestanda räknare som samlats in av Application Insights](../app/performance-counters.md).

### <a name="available-memory-performancecountersavailablememory"></a>Tillgängligt minne (performanceCounters/availableMemory)

```Kusto
performanceCounters
| where ((category == "Memory" and counter == "Available Bytes") or name == "availableMemory")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="exception-rate-performancecountersexceptionrate"></a>Undantags frekvens (performanceCounters/exceptionRate)

```Kusto
performanceCounters
| where ((category == ".NET CLR Exceptions" and counter == "# of Exceps Thrown / sec") or name == "exceptionRate")
| extend performanceCounter_value = iif(itemType == 'performanceCounter',value,todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-execution-time-performancecountersrequestexecutiontime"></a>Körnings tid för HTTP-begäran (performanceCounters/requestExecutionTime)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Request Execution Time") or name == "requestExecutionTime")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-request-rate-performancecountersrequestspersecond"></a>HTTP-begär ande frekvens (performanceCounters/requestsPerSecond)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests/Sec") or name == "requestsPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="http-requests-in-application-queue-performancecountersrequestsinqueue"></a>HTTP-begäranden i program kön (performanceCounters/requestsInQueue)

```Kusto
performanceCounters
| where ((category == "ASP.NET Applications" and counter == "Requests In Application Queue") or name == "requestsInQueue")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-cpu-performancecountersprocesscpupercentage"></a>Process-CPU (performanceCounters/processCpuPercentage)

Måttet visar hur mycket av den totala processor kapaciteten som förbrukas av processen som är värd för din övervakade app.

|Måttenhet|Agg regeringar som stöds|Dimensioner som stöds|
|---|---|---|
|Procent|Genomsnitt, min, max|Moln roll instans

```Kusto
performanceCounters
| where ((category == "Process" and counter == "% Processor Time Normalized") or name == "processCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-io-rate-performancecountersprocessiobytespersecond"></a>Processorns IO-frekvens (performanceCounters/processIOBytesPerSecond)

|Måttenhet|Agg regeringar som stöds|Dimensioner som stöds|
|---|---|---|
|Byte per sekund|Genomsnitt, min, max|Moln roll instans

```Kusto
performanceCounters
| where ((category == "Process" and counter == "IO Data Bytes/sec") or name == "processIOBytesPerSecond")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="process-private-bytes-performancecountersprocessprivatebytes"></a>Bearbeta privata byte (performanceCounters/processPrivateBytes)

Mängden icke-delat minne som den övervakade processen har allokerat för sina data.

|Måttenhet|Agg regeringar som stöds|Dimensioner som stöds|
|---|---|---|
|Byte|Genomsnitt, min, max|Moln roll instans

```Kusto
performanceCounters
| where ((category == "Process" and counter == "Private Bytes") or name == "processPrivateBytes")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

### <a name="processor-time-performancecountersprocessorcpupercentage"></a>Processor tid (performanceCounters/processorCpuPercentage)

PROCESSOR förbrukning för *alla* processer som körs på den övervakade Server instansen.

|Måttenhet|Agg regeringar som stöds|Dimensioner som stöds|
|---|---|---|
|Procent|Genomsnitt, min, max|Moln roll instans

>[!NOTE]
> Måttet för processor tiden är inte tillgängligt för de program som finns i Azure App Services. Använd  [process processor](#process-cpu-performancecountersprocesscpupercentage) måttet för att spåra processor användningen för de webb program som finns i app Services.

```Kusto
performanceCounters
| where ((category == "Processor" and counter == "% Processor Time") or name == "processorCpuPercentage")
| extend performanceCounter_value = iif(itemType == "performanceCounter", value, todouble(''))
| summarize sum(performanceCounter_value) / count() by bin(timestamp, 1m)
| render timechart
```

## <a name="server-metrics"></a>Server mått

### <a name="dependency-calls-dependenciescount"></a>Beroende anrop (beroenden/antal)

Måttet är i förhållande till antalet beroende anrop.

```Kusto
dependencies
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="dependency-duration-dependenciesduration"></a>Beroende varaktighet (beroenden/varaktighet)

Det här måttet avser varaktigheten för beroende anrop.

```Kusto
dependencies
| where notempty(duration)
| extend dependency_duration = iif(itemType == 'dependency',duration,todouble(''))
| extend _sum = dependency_duration
| extend _count = itemCount
| extend _sum = _sum*_count
| summarize sum(_sum)/sum(_count) by bin(timestamp, 1m)
| render timechart
```

### <a name="server-requests-requestscount"></a>Server begär Anden (antal begär Anden/antal)

Det här måttet visar antalet inkommande server-begäranden som tagits emot av ditt webb program.

```Kusto
requests
| summarize sum(itemCount) by bin(timestamp, 5m)
| render barchart
```

### <a name="server-response-time-requestsduration"></a>Server svars tid (begär Anden/varaktighet)

Det här måttet visar hur lång tid det tog för servrarna att bearbeta inkommande begär Anden.

```Kusto
requests
| where notempty(duration)
| extend request_duration = iif(itemType == 'request', duration, todouble(''))
| extend _sum = request_duration
| extend _count = itemCount
| extend _sum = _sum*_count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 1m)
| render timechart
```

## <a name="usage-metrics"></a>Användningsstatistik

### <a name="page-view-load-time-pageviewsduration"></a>Inläsnings tid för sid visning (pageViews/duration)

Det här måttet avser hur lång tid det tog för sid visningar-händelser att läsas in.

```Kusto
pageViews
| where notempty(duration)
| extend pageView_duration = iif(itemType == 'pageView', duration, todouble(''))
| extend _sum = pageView_duration
| extend _count = itemCount
| extend _sum = _sum * _count
| summarize sum(_sum) / sum(_count) by bin(timestamp, 5m)
| render barchart
```

### <a name="page-views-pageviewscount"></a>Sid visningar (pageViews/Count)

Antalet sid visningar-händelser som loggats med TrackPageView () Application Insights API.

```Kusto
pageViews
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="sessions-sessionscount"></a>Sessioner (sessioner/antal)

Det här måttet avser antalet distinkta sessions-ID: n.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(session_Id)
| summarize dcount(session_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="traces-tracescount"></a>Spår (spår/antal)

Antalet spårnings instruktioner som loggats med TrackTrace () Application Insights API-anrop.

```Kusto
traces
| summarize sum(itemCount) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-userscount"></a>Användare (användare/antal)

Antalet distinkta användare som har åtkomst till ditt program. Noggrannheten för det här måttet kan påverkas avsevärt genom att använda telemetri-sampling och filtrering.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_Id)
| summarize dcount(user_Id) by bin(timestamp, 1h)
| render barchart
```

### <a name="users-authenticated-usersauthenticated"></a>Användare, autentiserade (användare/autentiserade)

Antalet distinkta användare som autentiserats i ditt program.

```Kusto
union traces, requests, pageViews, dependencies, customEvents, availabilityResults, exceptions, customMetrics, browserTimings
| where notempty(user_AuthenticatedId)
| summarize dcount(user_AuthenticatedId) by bin(timestamp, 1h)
| render barchart
```