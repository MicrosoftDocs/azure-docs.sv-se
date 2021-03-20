---
title: Azure Application Insights standard mått | Microsoft Docs
description: I den här artikeln visas Azure Application Insights-mått med stödda agg regeringar och dimensioner.
services: azure-monitor
ms.topic: reference
ms.date: 07/03/2019
ms.subservice: application-insights
ms.openlocfilehash: 0a18088fa434efa76007607c067feec107bdae57
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100572349"
---
# <a name="application-insights-standard-metrics"></a>Application Insights standard mått

Standard mått är församlade under samlingen och har bättre prestanda vid tidpunkten för frågan. Detta gör dem till det bästa valet för instrument paneler och aviseringar i real tid.

## <a name="availability-metrics"></a>Tillgänglighets mått

Mått i kategorin tillgänglighet gör att du kan se hälso tillståndet för ditt webb program som observerats från platser runtom i världen. [Konfigurera tillgänglighets testerna](../app/monitor-web-app-availability.md) för att börja använda mått från den här kategorin.

### <a name="availability-availabilityresultsavailabilitypercentage"></a>Tillgänglighet (availabilityResults/availabilityPercentage)
*Tillgänglighets* måttet visar procent andelen av de webb test körningar som inte identifierade några problem. Det lägsta möjliga värdet är 0, vilket innebär att alla webb test körningar har misslyckats. Värdet 100 innebär att alla webbtester körs och att verifierings villkoren har körts.

|Måttenhet|Agg regeringar som stöds|Dimensioner som stöds|
|---|---|---|---|---|---|
|Procent|Genomsnitt|`Run location`, `Test name`|


### <a name="availability-test-duration-availabilityresultsduration"></a>Tillgänglighets testets varaktighet (availabilityResults/duration)

Måttet för *tillgänglighets testets varaktighet* visar hur lång tid det tog för webb testet att köras. För [webbtester med flera steg](../app/availability-multistep.md)återspeglar måttet den totala körnings tiden för alla steg.

|Måttenhet|Agg regeringar som stöds|Dimensioner som stöds|
|---|---|---|---|---|---|
|Millisekunder|Genomsnitt, min, max|`Run location`, `Test name`, `Test result`

### <a name="availability-tests-availabilityresultscount"></a>Tillgänglighets test (availabilityResults/Count)

Måttet *tillgänglighets test* visar antalet webbtester som körs av Azure Monitor.

|Måttenhet|Agg regeringar som stöds|Dimensioner som stöds|
|---|---|---|---|---|---|
|Antal|Antal|`Run location`, `Test name`, `Test result`|


## <a name="browser-metrics"></a>Webb läsar mått

Webb läsar mått samlas in av Application Insights JavaScript SDK från verkliga webbläsare för slutanvändare. De ger fantastiska insikter om användarnas upplevelse med din webbapp. Webb läsar måtten samplas vanligt vis inte, vilket innebär att de ger högre precision av användnings numren jämfört med mått på Server sidan som kan skevas genom sampling.

> [!NOTE]
> För att samla in webb läsar mått måste ditt program instrumenteras med [Application Insights JavaScript SDK](../app/javascript.md).

### <a name="browser-page-load-time-browsertimingstotalduration"></a>Sid inläsnings tid för webbläsare (browserTimings/totalDuration)

|Måttenhet|Agg regeringar som stöds| Dimensioner som stöds|
|---|---|---|
|Millisekunder|Genomsnitt, min, max|Inget|

### <a name="client-processing-time-browsertimingprocessingduration"></a>Klient bearbetnings tid (browserTiming/processingDuration)

|Måttenhet|Agg regeringar som stöds|Dimensioner som stöds|
|---|---|---|
|Millisekunder|Genomsnitt, min, max|Inget|

### <a name="page-load-network-connect-time-browsertimingsnetworkduration"></a>Sid inläsning nätverks anslutnings tid (browserTimings/networkDuration)

|Måttenhet|Agg regeringar som stöds| Dimensioner som stöds|
|---|---|---|
|Millisekunder|Genomsnitt, min, max|Inget|

### <a name="receiving-response-time-browsertimingsreceiveduration"></a>Tar emot svars tid (browserTimings/receiveDuration)

|Måttenhet|Agg regeringar som stöds| Dimensioner som stöds|
|---|---|---|
|Millisekunder|Genomsnitt, min, max|Inget|

### <a name="send-request-time-browsertimingssendduration"></a>Sändnings tid för begäran (browserTimings/sendDuration)

|Måttenhet|Agg regeringar som stöds| Dimensioner som stöds|
|---|---|---|
|Millisekunder|Genomsnitt, min, max|Inget|

## <a name="failure-metrics"></a>Felaktiga mått

Måtten i **fel** visar problem med bearbetnings begär Anden, beroende anrop och utlösta undantag.

### <a name="browser-exceptions-exceptionsbrowser"></a>Webb läsar undantag (undantag/webbläsare)

Det här måttet visar antalet utlösta undantag från din program kod som körs i webbläsaren. Endast undantag som spåras med ett ```trackException()``` Application Insights API-anrop ingår i måttet.

|Måttenhet|Agg regeringar som stöds | Dimensioner som stöds|
|---|---|---|---|
| Antal | Antal | `Cloud role name` |

### <a name="dependency-call-failures-dependenciesfailed"></a>Misslyckade beroende anrop (beroenden/misslyckade)

Antalet misslyckade beroende anrop.

|Måttenhet|Agg regeringar som stöds | Dimensioner som stöds |
|---|---|---|---|
|Antal|Antal| `Cloud role instance`, `Cloud role name`, `Dependency performance`, `Dependency type`, `Is traffic synthetic`, `Result code`, `Rarget of dependency call`.


### <a name="exceptions-exceptionscount"></a>Undantag (undantag/antal)

Varje gång du loggar ett undantag till Application Insights finns det ett anrop till [trackException ()-metoden](../app/api-custom-events-metrics.md#trackexception) i SDK: n. Undantags måttet visar antalet loggade undantag.

|Måttenhet|Agg regeringar som stöds | Dimensioner som stöds |
|---|---|---|---|
|Antal|Antal|`Cloud role instance`, `Cloud role name`, `Device type`|

### <a name="failed-requests-requestsfailed"></a>Misslyckade förfrågningar (begär Anden/misslyckade)

Antalet spårade server begär Anden som marker ATS som *misslyckade*. Som standard märker Application Insights SDK automatiskt varje serverbegäran som returnerade HTTP-svarskod 5xx eller 4xx som en misslyckad begäran. Du kan anpassa den här logiken genom att ändra egenskapen  *lyckades* för objektet begär telemetri i en [anpassad telemetri-initierare](../app/api-filtering-sampling.md#addmodify-properties-itelemetryinitializer).


|Måttenhet|Agg regeringar som stöds | Dimensioner som stöds |
|---|---|---|---|
|Antal|Antal|`Cloud role instance`, `Cloud role name`, `Is synthetic traffic`, `Request performance`, `Result code`|


### <a name="server-exceptions-exceptionsserver"></a>Server undantag (undantag/Server)

Det här måttet visar antalet Server undantag.

|Måttenhet|Agg regeringar som stöds | Dimensioner som stöds |
|---|---|---|---|
|Antal|Antal|`Cloud role instance`, `Cloud role name`|

## <a name="performance-counters"></a>Prestandaräknare

Använd mått i kategorin **prestanda räknare** för att få åtkomst till [system prestanda räknare som samlats in av Application Insights](../app/performance-counters.md).

### <a name="available-memory-performancecountersavailablememory"></a>Tillgängligt minne (performanceCounters/availableMemory)

|Måttenhet|Agg regeringar som stöds | Dimensioner som stöds |
|---|---|---|---|
|Data beroende: megabyte, Gigabyte|Genomsnitt, Max, min|`Cloud role instance`|


### <a name="exception-rate-performancecountersexceptionrate"></a>Undantags frekvens (performanceCounters/exceptionRate)

|Måttenhet|Agg regeringar som stöds | Dimensioner som stöds |
|---|---|---|---|
| Antal | Genomsnitt, Max, min | `Cloud role instance` |


### <a name="http-request-execution-time-performancecountersrequestexecutiontime"></a>Körnings tid för HTTP-begäran (performanceCounters/requestExecutionTime)

|Måttenhet|Agg regeringar som stöds | Dimensioner som stöds |
|---|---|---|---|
| Millisekunder | Genomsnitt, Max, min | `Cloud role instance` |

### <a name="http-request-rate-performancecountersrequestspersecond"></a>HTTP-begär ande frekvens (performanceCounters/requestsPerSecond)

|Måttenhet|Agg regeringar som stöds | Dimensioner som stöds |
|---|---|---|---|
| Förfrågningar per sekund | Genomsnitt, Max, min | `Cloud role instance` |

### <a name="http-requests-in-application-queue-performancecountersrequestsinqueue"></a>HTTP-begäranden i program kön (performanceCounters/requestsInQueue)

|Måttenhet|Agg regeringar som stöds | Dimensioner som stöds |
|---|---|---|---|
| Antal | Genomsnitt, Max, min | `Cloud role instance` |


### <a name="process-cpu-performancecountersprocesscpupercentage"></a>Process-CPU (performanceCounters/processCpuPercentage)

Måttet visar hur mycket av den totala processor kapaciteten som förbrukas av processen som är värd för din övervakade app.

|Måttenhet|Agg regeringar som stöds|Dimensioner som stöds|
|---|---|---|
|Procent|Genomsnitt, Max, min| `Cloud role instance` |


### <a name="process-io-rate-performancecountersprocessiobytespersecond"></a>Processorns IO-frekvens (performanceCounters/processIOBytesPerSecond)

|Måttenhet|Agg regeringar som stöds|Dimensioner som stöds|
|---|---|---|
|Byte per sekund|Genomsnitt, min, max|`Cloud role instance` |



### <a name="process-private-bytes-performancecountersprocessprivatebytes"></a>Bearbeta privata byte (performanceCounters/processPrivateBytes)

Mängden icke-delat minne som den övervakade processen har allokerat för sina data.

|Måttenhet|Agg regeringar som stöds|Dimensioner som stöds|
|---|---|---|
|Byte|Genomsnitt, min, max|`Cloud role instance` |

### <a name="processor-time-performancecountersprocessorcpupercentage"></a>Processor tid (performanceCounters/processorCpuPercentage)

PROCESSOR förbrukning för *alla* processer som körs på den övervakade Server instansen.

|Måttenhet|Agg regeringar som stöds|Dimensioner som stöds|
|---|---|---|
|Procent|Genomsnitt, min, max|`Cloud role instance` |

>[!NOTE]
> Måttet för processor tiden är inte tillgängligt för de program som finns i Azure App Services. Använd  [process processor](#process-cpu-performancecountersprocesscpupercentage) måttet för att spåra processor användningen för de webb program som finns i app Services.

## <a name="server-metrics"></a>Server mått

### <a name="dependency-calls-dependenciescount"></a>Beroende anrop (beroenden/antal)

Måttet är i förhållande till antalet beroende anrop.

|Måttenhet|Agg regeringar som stöds|Dimensioner som stöds|
|---|---|---|
| Antal | Antal | `Cloud role instance`, `Cloud role name`, `Dependency performance`, `Dependency type`, `Is traffic synthetic`, `Result code`, `Successful call`, `Target of a dependency call` |

### <a name="dependency-duration-dependenciesduration"></a>Beroende varaktighet (beroenden/varaktighet)

Det här måttet avser varaktigheten för beroende anrop.

|Måttenhet|Agg regeringar som stöds|Dimensioner som stöds|
|---|---|---|
| Tid | Genomsnitt, min, max | `Cloud role instance`, `Cloud role name`, `Dependency performance`, `Dependency type`, `Is traffic synthetic`, `Result code`, `Successful call`, `Target of a dependency call` |


### <a name="server-request-rate-requestscount"></a>Server begär ande frekvens (begär Anden/antal)

Det här måttet visar antalet inkommande server-begäranden som tagits emot av ditt webb program.

|Måttenhet|Agg regeringar som stöds|Dimensioner som stöds|
|---|---|---|
| Antal | Genomsnitt | `Cloud role instance`, `Cloud role name`, `Is traffic synthetic`, `Result performance` `Result code`, `Successful request` |

### <a name="server-requests-requestscount"></a>Server begär Anden (antal begär Anden/antal)

|Måttenhet|Agg regeringar som stöds|Dimensioner som stöds|
|---|---|---|
| Antal | Antal | `Cloud role instance`, `Cloud role name`, `Is traffic synthetic`, `Result performance` `Result code`, `Successful request` |

### <a name="server-response-time-requestsduration"></a>Server svars tid (begär Anden/varaktighet)

Det här måttet visar hur lång tid det tog för servrarna att bearbeta inkommande begär Anden.

|Måttenhet|Agg regeringar som stöds|Dimensioner som stöds|
|---|---|---|
| Tid | Genomsnitt, min, max | `Cloud role instance`, `Cloud role name`, `Is traffic synthetic`, `Result performance` `Result code`, `Successful request` |

## <a name="usage-metrics"></a>Användningsstatistik

### <a name="page-view-load-time-pageviewsduration"></a>Inläsnings tid för sid visning (pageViews/duration)

Det här måttet avser hur lång tid det tog för sid visningar-händelser att läsas in.

|Måttenhet|Agg regeringar som stöds|Dimensioner som stöds|
|---|---|---|
| Tid | Genomsnitt, min, max | `Cloud role name`, `Is traffic synthetic` |

### <a name="page-views-pageviewscount"></a>Sid visningar (pageViews/Count)

Antalet sid visningar-händelser som loggats med TrackPageView () Application Insights API.

|Måttenhet|Agg regeringar som stöds|Dimensioner som stöds|
|---|---|---|
| Antal | Antal | `Cloud role name`, `Is traffic synthetic` |

### <a name="traces-tracescount"></a>Spår (spår/antal)

Antalet spårnings instruktioner som loggats med TrackTrace () Application Insights API-anrop.

|Måttenhet|Agg regeringar som stöds|Dimensioner som stöds|
|---|---|---|
| Antal | Antal | `Cloud role instance`, `Cloud role name`,  `Is traffic synthetic`, `Severity level` |


## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [log-baserade och föraggregerade mått](./pre-aggregated-metrics-log-metrics.md).
* [Loggbaserade mått frågor och definitioner](../essentials/app-insights-metrics.md).