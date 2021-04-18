---
title: Azure Monitor mått som stöds efter resurstyp
description: Lista över tillgängliga mått för varje resurstyp med Azure Monitor.
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 04/15/2021
ms.author: robb
ms.openlocfilehash: 1091d103428315a065dd1ff9800ce2ad16632df0
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600020"
---
# <a name="supported-metrics-with-azure-monitor"></a>Mått som stöds med Azure Monitor

> [!NOTE]
> Den här listan genereras huvudsakligen automatiskt. Ändringar som görs i den här listan via GitHub kan skrivas över utan varning. Kontakta artikelförfattaren om du vill ha mer information om hur du gör permanenta uppdateringar.

Azure Monitor finns flera sätt att interagera med mått, inklusive att visa dem i portalen, komma åt dem via REST API eller köra frågor mot dem med hjälp av PowerShell eller CLI. 

Den här artikeln är en fullständig lista över alla plattformsmått (det vill säga automatiskt insamlade) som för närvarande är tillgängliga med Azure Monitor konsoliderade måttpipeline. Mått som ändrats eller lagts till efter datumet längst upp i den här artikeln kanske inte visas nedan. Om du vill fråga efter och komma åt listan över mått programmatiskt använder du [API-versionen 2018-01-01.](/rest/api/monitor/metricdefinitions) Andra mått som inte finns med i den här listan kan vara tillgängliga i portalen eller med hjälp av äldre API:er.

Måtten ordnas efter resursproviders och resurstyp. En lista över tjänster och resursproviders och resurstyper som tillhör dem finns i [Resursproviders för Azure-tjänster.](../../azure-resource-manager/management/azure-services-resource-providers.md)  

## <a name="exporting-platform-metrics-to-other-locations"></a>Exportera plattformsmått till andra platser

Du kan exportera plattformsmåtten från Azure Monitor-pipelinen till andra platser på något av två sätt.
1. Använd [måtten REST API](/rest/api/monitor/metrics/list)
2. Använd [diagnostikinställningar](../essentials/diagnostic-settings.md) för att dirigera plattformsmått till 
    - Azure Storage
    - Azure Monitor loggar (och därmed Log Analytics)
    - Event Hubs, som är hur du tar dem till system som inte kommer från Microsoft 

Att använda diagnostikinställningar är det enklaste sättet att dirigera måtten, men det finns vissa begränsningar: 

- **Vissa kan inte exporteras** – Alla mått kan exporteras med hjälp av REST API, men vissa kan inte exporteras med hjälp av diagnostikinställningar på grund av intrikat i Azure Monitor backend. Kolumnen *Exporterbar via diagnostikinställningar* i tabellerna nedan visar vilka mått som kan exporteras på det här sättet.  

- **Flerdimensionella mått –** Det finns för närvarande inte stöd för att skicka flerdimensionella mått till andra platser via diagnostikinställningar. Mått med dimensioner exporteras som tillplattade endimensionella mått som aggregeras över dimensionsvärden. *Till exempel*: Måttet för inkommande meddelanden i en händelsehubb kan utforskas och läggas till på per-kö-nivå. När måttet exporteras via diagnostikinställningar visas det dock som alla inkommande meddelanden i alla köer i händelsehubben.

## <a name="guest-os-and-host-os-metrics"></a>Mått för gästoperativsystem och värdoperativsystem

> [!WARNING]
> Mått för gästoperativsystemet (gästoperativsystem) som körs i Azure Virtual Machines, Service Fabric och Cloud Services **visas** INTE här. Mått för gästoperativsystem måste samlas in via en eller flera agenter som körs på eller som en del av gästoperativsystemet.  Mått för gästoperativsystem omfattar prestandaräknare som spårar procentandel av gästprocessorer eller minnesanvändning, som båda ofta används för automatisk skalning eller avisering. 
>
> **Mått för värdoperativsystem är tillgängliga och visas nedan.** De är inte samma sak. Måtten för värdoperativsystem är relaterade till Hyper-V-sessionen som är värd för din gästoperativsystemsession. 

> [!TIP]
> Bästa praxis är att använda och konfigurera [Azure Diagnostics-tillägget](../agents/diagnostics-extension-overview.md) för att skicka prestandamått för gästoperativsystem till samma Azure Monitor-måttdatabas där plattformsmått lagras. Tillägget dirigerar mått för gästoperativsystem via [API:et för anpassade](../essentials/metrics-custom-overview.md) mått. Sedan kan du diagram, aviseringar och på annat sätt använda mått för gästoperativsystem som plattformsmått. Du kan också använda Log Analytics-agenten för att skicka mått för gästoperativsystem till Azure Monitor-loggar/Log Analytics. Där kan du köra frågor mot dessa mått i kombination med icke-måttdata. 

Viktig ytterligare information finns i Översikt [över övervakningsagenter.](../agents/agents-overview.md)

## <a name="table-formatting"></a>Tabellformatering

> [!IMPORTANT] 
> Den senaste uppdateringen lägger till en ny kolumn och sorterade om måtten så att de är alfabetiska. Additionsinformationen innebär att tabellerna nedan kan ha en vågrät rullningslist längst ned, beroende på webbläsarfönstrets bredd. Om du tror att du saknar information kan du använda rullningslisten för att se hela tabellen.
## <a name="microsoftaadiamazureadmetrics"></a>microsoft.a azuream/azureADMetrics

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ThrottledRequests|No|ThrottledRequests|Antal|Genomsnitt|azureADMetrics-typmått|Inga dimensioner|


## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|CleanerCurrentPrice|Yes|Minne: Renare aktuellt pris|Antal|Genomsnitt|Aktuellt pris för minne, $/byte/time, normaliserat till 1 000.|ServerResourceType|
|CleanerMemoryNonshrinkable|Yes|Minne: Rensat minne som inte kan krympa|Byte|Genomsnitt|Mängden minne, i byte, som inte kan rensas av bakgrundsrensaren.|ServerResourceType|
|CleanerMemoryShrinkable|Yes|Minne: Rensat minne krympbart|Byte|Genomsnitt|Mängden minne, i byte, som kan rensas av bakgrundsrensaren.|ServerResourceType|
|CommandPoolBusyThreads|Yes|Trådar: Trådar i kommandopoolen|Antal|Genomsnitt|Antal upptagna trådar i kommandotrådpoolen.|ServerResourceType|
|CommandPoolIdleThreads|Yes|Trådar: Inaktiva trådar i kommandopoolen|Antal|Genomsnitt|Antal inaktiva trådar i kommandotrådpoolen.|ServerResourceType|
|CommandPoolJobQueueLength|Yes|Jobbkölängd för kommandopool|Antal|Genomsnitt|Antal jobb i kön för kommandotrådpoolen.|ServerResourceType|
|CurrentConnections|Yes|Anslutning: Aktuella anslutningar|Antal|Genomsnitt|Aktuellt antal klientanslutningar som upprättats.|ServerResourceType|
|CurrentUserSessions|Yes|Aktuella användarsessioner|Antal|Genomsnitt|Aktuellt antal användarsessioner har upprättats.|ServerResourceType|
|LongParsingBusyThreads|Yes|Trådar: Lång parsning av upptagna trådar|Antal|Genomsnitt|Antal upptagna trådar i den långa trådpoolen för parsning.|ServerResourceType|
|LongParsingIdleThreads|Yes|Trådar: Långa parsning av inaktiva trådar|Antal|Genomsnitt|Antal inaktiva trådar i den långa parsningstrådpoolen.|ServerResourceType|
|LongParsingJobQueueLength|Yes|Trådar: Lång kölängd för parsning av jobb|Antal|Genomsnitt|Antal jobb i kön för den långa parsningstrådpoolen.|ServerResourceType|
|mashup_engine_memory_metric|Yes|M-motorminne|Byte|Genomsnitt|Minnesanvändning av processer för kombinationsmotorn|ServerResourceType|
|mashup_engine_private_bytes_metric|Yes|Privata byte för M-motor|Byte|Genomsnitt|Användning av privata byte av processer för kombinationsmotorn.|ServerResourceType|
|mashup_engine_qpu_metric|Yes|QPU för M-motor|Antal|Genomsnitt|QPU-användning av processer för kombinationsmotorn|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Yes|Virtuella byte för M-motor|Byte|Genomsnitt|Användning av virtuella byte av processer för kombinationsmotorn.|ServerResourceType|
|memory_metric|Yes|Minne|Byte|Genomsnitt|Memory. Intervallet 0–25 GB för S1, 0–50 GB för S2 och 0–100 GB för S4|ServerResourceType|
|memory_thrashing_metric|Yes|Minnesförslöing|Procent|Genomsnitt|Genomsnittlig minnessming.|ServerResourceType|
|MemoryLimitHard|Yes|Minne: Minnesgräns hård|Byte|Genomsnitt|Hård minnesgräns från konfigurationsfilen.|ServerResourceType|
|MemoryLimitHigh|Yes|Minne: Hög minnesgräns|Byte|Genomsnitt|Hög minnesgräns från konfigurationsfilen.|ServerResourceType|
|MemoryLimitLow|Yes|Minne: Minnesgränsen är låg|Byte|Genomsnitt|Låg minnesgräns från konfigurationsfilen.|ServerResourceType|
|MemoryLimitVertiPaq|Yes|Minne: VertiPaq för minnesgräns|Byte|Genomsnitt|Minnesgräns från konfigurationsfilen.|ServerResourceType|
|MemoryUsage|Yes|Minne: Minnesanvändning|Byte|Genomsnitt|Minnesanvändningen för serverprocessen som används vid beräkning av priset på renare minne. Lika med counter Process\PrivateBytes plus storleken på minnesmappade data, och ignorerar allt minne som mappats eller allokerats av xVelocity In-Memory Analytics-motorn (VertiPaq) som överstiger minnesgränsen för xVelocity-motorn.|ServerResourceType|
|private_bytes_metric|Yes|Privata byte|Byte|Genomsnitt|Privata byte.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Yes|Trådar: Bearbetning av I/O-jobbtrådar i poolen|Antal|Genomsnitt|Antal trådar som kör I/O-jobb i bearbetningstrådpoolen.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Yes|Trådar: Bearbetningspoolen är upptagen med icke-I/O-trådar|Antal|Genomsnitt|Antal trådar som kör icke-I/O-jobb i bearbetningstrådpoolen.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Yes|Trådar: Bearbeta inaktiva I/O-jobbtrådar för pool|Antal|Genomsnitt|Antal inaktiva trådar för I/O-jobb i bearbetningstrådpoolen.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Yes|Trådar: Bearbeta inaktiva pooltrådar som inte är I/O|Antal|Genomsnitt|Antal inaktiva trådar i bearbetningstrådpoolen som är dedikerade till icke-I/O-jobb.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Yes|Trådar: Kölängd för bearbetning av pool-I/O-jobb|Antal|Genomsnitt|Antal I/O-jobb i kön för bearbetningstrådpoolen.|ServerResourceType|
|ProcessingPoolJobQueueLength|Yes|Kölängd för bearbetning av pooljobb|Antal|Genomsnitt|Antal icke-I/O-jobb i kön för bearbetningstrådpoolen.|ServerResourceType|
|qpu_metric|Yes|QPU|Antal|Genomsnitt|QPU. Intervall 0–100 för S1, 0–200 för S2 och 0–400 för S4|ServerResourceType|
|QueryPoolBusyThreads|Yes|Upptagna trådar för frågepool|Antal|Genomsnitt|Antal upptagna trådar i frågetrådpoolen.|ServerResourceType|
|QueryPoolIdleThreads|Yes|Trådar: Inaktiva trådar för frågepool|Antal|Genomsnitt|Antal inaktiva trådar för I/O-jobb i bearbetningstrådpoolen.|ServerResourceType|
|QueryPoolJobQueueLength|Yes|Trådar: Frågepoolens jobbkölängd|Antal|Genomsnitt|Antal jobb i kön för frågetrådpoolen.|ServerResourceType|
|Kvot|Yes|Minne: Kvot|Byte|Genomsnitt|Aktuell minneskvot, i byte. Minneskvoten kallas även för minnestilldelning eller minnesreservation.|ServerResourceType|
|Blockerad kvot|Yes|Minne: Kvoten är blockerad|Antal|Genomsnitt|Aktuellt antal kvotbegäranden som blockeras tills andra minneskvoter frigörs.|ServerResourceType|
|RowsConvertedPerSec|Yes|Bearbetning: Rader konverteras per sekund|CountPerSecond|Genomsnitt|Frekvens för rader som konverteras under bearbetningen.|ServerResourceType|
|RowsReadPerSec|Yes|Bearbetning: Rader läses per sekund|CountPerSecond|Genomsnitt|Antal rader som läses från alla relationsdatabaser.|ServerResourceType|
|RowsWrittenPerSec|Yes|Bearbetning: Rader skrivna per sekund|CountPerSecond|Genomsnitt|Antal rader som skrivits under bearbetningen.|ServerResourceType|
|ShortParsingBusyThreads|Yes|Trådar: Kort parsning av upptagna trådar|Antal|Genomsnitt|Antal upptagna trådar i den korta parsningstrådpoolen.|ServerResourceType|
|ShortParsingIdleThreads|Yes|Trådar: Kort parsning av inaktiva trådar|Antal|Genomsnitt|Antal inaktiva trådar i den korta parsningstrådpoolen.|ServerResourceType|
|ShortParsingJobQueueLength|Yes|Trådar: Kort parsning av jobbkölängd|Antal|Genomsnitt|Antal jobb i kön för den korta parsningstrådpoolen.|ServerResourceType|
|SuccessfullConnectionsPerSec|Yes|Lyckade anslutningar per sekund|CountPerSecond|Genomsnitt|Frekvens för slutförda anslutningar.|ServerResourceType|
|TotalConnectionFailures|Yes|Totalt antal anslutningsfel|Antal|Genomsnitt|Totalt antal misslyckade anslutningsförsök.|ServerResourceType|
|TotalConnectionRequests|Yes|Totalt antal anslutningsbegäranden|Antal|Genomsnitt|Totalt antal anslutningsbegäranden. Det här är ankomster.|ServerResourceType|
|VertiPaqNonpaged|Yes|Minne: VertiPaq nonpaged|Byte|Genomsnitt|Byte av minne som är låsta i arbetsminnet för användning av den minnesbaserade motorn.|ServerResourceType|
|VertiPaqPaged|Yes|Minne: VertiPaq Paged|Byte|Genomsnitt|Byte av växlingsminne som används för minnesdata.|ServerResourceType|
|virtual_bytes_metric|Yes|Virtuella byte|Byte|Genomsnitt|Virtuella byte.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|BackendDuration|Yes|Varaktighet för begäranden från backend|Millisekunder|Genomsnitt|Varaktighet för förfrågningar från backend i millisekunder|Plats, Värdnamn|
|Kapacitet|Yes|Kapacitet|Procent|Genomsnitt|Användningsmått för ApiManagement-tjänsten|Location|
|Varaktighet|Yes|Total varaktighet för gatewaybegäranden|Millisekunder|Genomsnitt|Total varaktighet för gatewaybegäranden i millisekunder|Plats, Värdnamn|
|EventHubDroppedEvents|Yes|Bort ignorerade EventHub-händelser|Antal|Totalt|Antalet händelser som hoppas över på grund av att storleksgränsen för kön har nåtts|Location|
|EventHubRejectedEvents|Yes|Avvisade EventHub-händelser|Antal|Totalt|Antal avvisade EventHub-händelser (felaktig konfiguration eller obehörig)|Location|
|EventHubSuccessfulEvents|Yes|Lyckade EventHub-händelser|Antal|Totalt|Antal lyckade EventHub-händelser|Location|
|EventHubThrottledEvents|Yes|Begränsade EventHub-händelser|Antal|Totalt|Antal begränsade EventHub-händelser|Location|
|EventHubTimedoutEvents|Yes|EventHub-händelser över en time out|Antal|Totalt|Antal EventHub-händelser som har time out|Location|
|EventHubTotalBytesSent|Yes|Storleken på EventHub-händelser|Byte|Totalt|Total storlek på EventHub-händelser i byte|Location|
|EventHubTotalEvents|Yes|Totalt antal EventHub-händelser|Antal|Totalt|Antal händelser som skickats till EventHub|Location|
|EventHubTotalFailedEvents|Yes|Misslyckade EventHub-händelser|Antal|Totalt|Antal misslyckade EventHub-händelser|Location|
|FailedRequests|Yes|Misslyckade gatewaybegäranden (inaktuella)|Antal|Totalt|Antal fel i gateway-begäranden – Använd måttet för flerdimensionell begäran med dimensionen GatewayResponseCodeCategory i stället|Plats, Värdnamn|
|Nätverksanslutning|Yes|Nätverksanslutningsstatus för resurser (förhandsversion)|Antal|Genomsnitt|Status för nätverksanslutning för beroende resurstyper från API Management tjänst|Plats, ResourceType|
|OtherRequests|Yes|Andra gatewaybegäranden (inaktuella)|Antal|Totalt|Antal andra gatewaybegäranden – Använd måttet för flerdimensionell begäran med dimensionen GatewayResponseCodeCategory i stället|Plats, Värdnamn|
|Begäranden|Yes|Begäranden|Antal|Totalt|Mått för gatewaybegäran med flera dimensioner|Location, Hostname, LastErrorReason, BackendResponseCode, GatewayResponseCode, BackendResponseCodeCategory, GatewayResponseCodeCategory|
|SuccessfulRequests|Yes|Lyckade gatewaybegäranden (inaktuella)|Antal|Totalt|Antal lyckade gatewaybegäranden – Använd måttet för flerdimensionell begäran med dimensionen GatewayResponseCodeCategory i stället|Plats, Värdnamn|
|TotalRequests|Yes|Totalt antal gatewaybegäranden (inaktuella)|Antal|Totalt|Antal gatewaybegäranden – Använd måttet för flerdimensionell begäran med dimensionen GatewayResponseCodeCategory i stället|Plats, Värdnamn|
|UnauthorizedRequests|Yes|Obehöriga gatewaybegäranden (inaktuella)|Antal|Totalt|Antal obehöriga gatewaybegäranden – Använd måttet för flerdimensionell begäran med dimensionen GatewayResponseCodeCategory i stället|Plats, Värdnamn|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft.AppConfiguration/configurationStores

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|HttpIncomingRequestCount|Yes|HttpIncomingRequestCount|Antal|Antal|Totalt antal inkommande HTTP-begäranden.|StatusCode, Authentication|
|HttpIncomingRequestDuration|Yes|HttpIncomingRequestDuration|Antal|Genomsnitt|Svarstid för en HTTP-begäran.|StatusCode, Authentication|
|ThrottledHttpRequestCount|Yes|ThrottledHttpRequestCount|Antal|Antal|Begränsade HTTP-begäranden.|Inga dimensioner|

## <a name="microsoftappplatformspring"></a>Microsoft.AppPlatform/Spring

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|active-timer-count|Yes|active-timer-count|Antal|Genomsnitt|Antal timers som för närvarande är aktiva|Distribution, AppName, Pod|
|alloc-rate|Yes|alloc-rate|Byte|Genomsnitt|Antal byte som allokerats i den hanterade heapen|Distribution, AppName, Pod|
|AppCpuUsage|Yes|Programmets processoranvändning |Procent|Genomsnitt|Den senaste CPU-användningen för appen|Distribution, AppName, Pod|
|sammansättningsantal|Yes|sammansättningsantal|Antal|Genomsnitt|Antal inlästa sammansättningar|Distribution, AppName, Pod|
|cpu-användning|Yes|cpu-användning|Procent|Genomsnitt|% tid då processorn har använts|Distribution, AppName, Pod|
|aktuella begäranden|Yes|current-requests|Antal|Genomsnitt|Totalt antal begäranden som bearbetas under hela processens livslängd|Distribution, AppName, Pod|
|undantagsantal|Yes|undantagsantal|Antal|Totalt|Antal undantag|Distribution, AppName, Pod|
|misslyckade begäranden|Yes|misslyckade begäranden|Antal|Genomsnitt|Totalt antal misslyckade begäranden under hela processens livslängd|Distribution, AppName, Pod|
|gc-heap-size|Yes|gc-heap-size|Antal|Genomsnitt|Total heap-storlek som rapporterats av GC (MB)|Distribution, AppName, Pod|
|gen-0-gc-count|Yes|gen-0-gc-count|Antal|Genomsnitt|Antal Gen 0 GCs|Distribution, AppName, Pod|
|gen-0-size|Yes|gen-0-size|Byte|Genomsnitt|Heap-storlek gen 0|Distribution, AppName, Pod|
|gen-1-gc-count|Yes|gen-1-gc-count|Antal|Genomsnitt|Antal Gen 1-GCs|Distribution, AppName, Pod|
|gen-1-size|Yes|gen-1-size|Byte|Genomsnitt|Gen 1 Heap Size|Distribution, AppName, Pod|
|gen-2-gc-count|Yes|gen-2-gc-count|Antal|Genomsnitt|Antal Gen 2-GCs|Distribution, AppName, Pod|
|gen-2-size|Yes|gen-2-size|Byte|Genomsnitt|Heap-storlek gen 2|Distribution, AppName, Pod|
|jvm.gc.live.data.size|Yes|jvm.gc.live.data.size|Byte|Genomsnitt|Storleken på den gamla generationens minnespool efter en fullständig GC|Distribution, AppName, Pod|
|jvm.gc.max.data.size|Yes|jvm.gc.max.data.size|Byte|Genomsnitt|Maximal storlek på den gamla generationens minnespool|Distribution, AppName, Pod|
|jvm.gc.memory.allocated|Yes|jvm.gc.memory.allocated|Byte|Maximal|Ökade för en ökning av storleken på den unga generationens minnespool efter en GC till före nästa|Distribution, AppName, Pod|
|jvm.gc.memory.promoted|Yes|jvm.gc.memory.promoted|Byte|Maximal|Antalet positiva ökningar i storleken på den gamla generationens minnespool före GC till efter GC|Distribution, AppName, Pod|
|jvm.gc.pause.total.count|Yes|jvm.gc.pause.total.count|Antal|Totalt|Antal pauser i GC|Distribution, AppName, Pod|
|jvm.gc.pause.total.time|Yes|jvm.gc.pause.total.time|Millisekunder|Totalt|GC Pausa total tid|Distribution, AppName, Pod|
|jvm.memory.committed|Yes|jvm.memory.committed|Byte|Genomsnitt|Minne som tilldelats JVM i byte|Distribution, AppName, Pod|
|jvm.memory.max|Yes|jvm.memory.max|Byte|Maximal|Den maximala mängden minne i byte som kan användas för minneshantering|Distribution, AppName, Pod|
|jvm.memory.used|Yes|jvm.memory.used|Byte|Genomsnitt|Appminne som används i byte|Distribution, AppName, Pod|
|loh-size|Yes|loh-size|Byte|Genomsnitt|LOH Heap-storlek|Distribution, AppName, Pod|
|monitor-lock-contention-count|Yes|monitor-lock-contention-count|Antal|Genomsnitt|Antal gånger som det fanns en spärr när övervakaren skulle låsas|Distribution, AppName, Pod|
|process.cpu.usage|Yes|process.cpu.usage|Procent|Genomsnitt|Den senaste CPU-användningen för JVM-processen|Distribution, AppName, Pod|
|begäranden per sekund|Yes|requests-rate|Antal|Genomsnitt|Begärandefrekvens|Distribution, AppName, Pod|
|system.cpu.usage|Yes|system.cpu.usage|Procent|Genomsnitt|Den senaste CPU-användningen för hela systemet|Distribution, AppName, Pod|
|threadpool-completed-items-count|Yes|threadpool-completed-items-count|Antal|Genomsnitt|Antal slutförda arbetsobjekt för ThreadPool|Distribution, AppName, Pod|
|threadpool-queue-length|Yes|threadpool-queue-length|Antal|Genomsnitt|Queue Length för ThreadPool-arbetsobjekt|Distribution, AppName, Pod|
|threadpool-thread-count|Yes|threadpool-thread-count|Antal|Genomsnitt|Antal ThreadPool-trådar|Distribution, AppName, Pod|
|time-in-gc|Yes|time-in-gc|Procent|Genomsnitt|% tid i GC sedan den senaste GC:en|Distribution, AppName, Pod|
|tomcat.global.error|Yes|tomcat.global.error|Antal|Totalt|Globalt tomcat-fel|Distribution, AppName, Pod|
|tomcat.global.received|Yes|tomcat.global.received|Byte|Totalt|Totalt antal mottagna byte för Tomcat|Distribution, AppName, Pod|
|tomcat.global.request.avg.time|Yes|tomcat.global.request.avg.time|Millisekunder|Genomsnitt|Genomsnittlig tid för Tomcat-begäran|Distribution, AppName, Pod|
|tomcat.global.request.max|Yes|tomcat.global.request.max|Millisekunder|Maximal|Maxtid för Tomcat-begäran|Distribution, AppName, Pod|
|tomcat.global.request.total.count|Yes|tomcat.global.request.total.count|Antal|Totalt|Totalt antal Tomcat-förfrågningar|Distribution, AppName, Pod|
|tomcat.global.request.total.time|Yes|tomcat.global.request.total.time|Millisekunder|Totalt|Total tid för Tomcat-begäran|Distribution, AppName, Pod|
|tomcat.global.sent|Yes|tomcat.global.sent|Byte|Totalt|Totalt antal skickade tomcat-byte|Distribution, AppName, Pod|
|tomcat.sessions.active.current|Yes|tomcat.sessions.active.current|Antal|Totalt|Antal aktiva Tomcat-sessioner|Distribution, AppName, Pod|
|tomcat.sessions.active.max|Yes|tomcat.sessions.active.max|Antal|Totalt|Max antal aktiva för Tomcat-session|Distribution, AppName, Pod|
|tomcat.sessions.alive.max|Yes|tomcat.sessions.alive.max|Millisekunder|Maximal|Maxtid för Tomcat-session|Distribution, AppName, Pod|
|tomcat.sessions.created|Yes|tomcat.sessions.created|Antal|Totalt|Antal skapade Tomcat-sessioner|Distribution, AppName, Pod|
|tomcat.sessions.expired|Yes|tomcat.sessions.expired|Antal|Totalt|Antal inaktuella Tomcat-sessioner|Distribution, AppName, Pod|
|tomcat.sessions.rejected|Yes|tomcat.sessions.rejected|Antal|Totalt|Antal avvisade Tomcat-sessioner|Distribution, AppName, Pod|
|tomcat.threads.config.max|Yes|tomcat.threads.config.max|Antal|Totalt|Maxantal trådar för Tomcat-konfiguration|Distribution, AppName, Pod|
|tomcat.threads.current|Yes|tomcat.threads.current|Antal|Totalt|Antal aktuella trådar i Tomcat|Distribution, AppName, Pod|
|totalt antal begäranden|Yes|totalt antal begäranden|Antal|Genomsnitt|Totalt antal begäranden under hela processens livslängd|Distribution, AppName, Pod|
|arbetsuppsättning|Yes|arbetsuppsättning|Antal|Genomsnitt|Mängden arbetsuppsättning som används av processen (MB)|Distribution, AppName, Pod|

## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|TotalJob|Yes|Totalt antal jobb|Antal|Totalt|Det totala antalet jobb|Runbook, Status|
|TotalUpdateDeploymentMachineRuns|Yes|Antal datorspecifika körningar av uppdateringsdistributionen|Antal|Totalt|Totalt antal programuppdateringsdistributionsdator körs i en programuppdateringsdistributionskörning|SoftwareUpdateConfigurationName, Status, TargetComputer, SoftwareUpdateConfigurationRunId|
|TotalUpdateDeploymentRuns|Yes|Antal körningar av uppdateringsdistributionen|Antal|Totalt|Totalt antal programuppdateringsdistributionskörningar|SoftwareUpdateConfigurationName, Status|


## <a name="microsoftavsprivateclouds"></a>Microsoft.AVS/privateClouds

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|CapacityLatest|Yes|Total kapacitet för datalagerdisk|Byte|Genomsnitt|Den totala kapaciteten för disken i datalagringen|dsname|
|DiskUsedPercentage|Yes| Procentandel datalagerdisk som används|Procent|Genomsnitt|Procent av den tillgängliga disken som används i datalager|dsname|
|EffectiveCpuAverage|Yes|Processorprocentandel|Procent|Genomsnitt|Procentandel använda CPU-resurser i kluster|klusternamn|
|EffectiveMemAverage|Yes|Genomsnittligt effektivt minne|Byte|Genomsnitt|Total tillgänglig mängd datorminne i kluster|klusternamn|
|OverheadAverage|Yes|Genomsnittlig minneskostnad|Byte|Genomsnitt|Vara värd för fysiskt minne som används av virtualiseringsinfrastrukturen|klusternamn|
|TotalMbAverage|Yes|Genomsnittligt totalt minne|Byte|Genomsnitt|Totalt minne i kluster|klusternamn|
|UsageAverage|Yes|Genomsnittlig minnesanvändning|Procent|Genomsnitt|Minnesanvändning som procentandel av totalt konfigurerat eller tillgängligt minne|klusternamn|
|UsedLatest|Yes|Datalagerdisk som används|Byte|Genomsnitt|Den totala mängden disk som används i datalagringen|dsname|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|CoreCount|No|Antal dedikerade kärnor|Antal|Totalt|Totalt antal dedikerade kärnor i Batch-kontot|Inga dimensioner|
|SkapaNodeCount|No|Skapa antal noder|Antal|Totalt|Antal noder som skapas|Inga dimensioner|
|IdleNodeCount|No|Antal inaktiva noder|Antal|Totalt|Antal inaktiva noder|Inga dimensioner|
|JobDeleteCompleteEvent|Yes|Slutförandehändelser för borttagning av jobb|Antal|Totalt|Totalt antal jobb som har tagits bort.|jobId|
|JobDeleteStartEvent|Yes|Starthändelser för borttagning av jobb|Antal|Totalt|Totalt antal jobb som har begärts att tas bort.|jobId|
|JobDisableCompleteEvent|Yes|Jobbet inaktiverar slutförda händelser|Antal|Totalt|Totalt antal jobb som har inaktiverats.|jobId|
|JobDisableStartEvent|Yes|Inaktivera starthändelser för jobb|Antal|Totalt|Totalt antal jobb som har begärts att inaktiveras.|jobId|
|JobStartEvent|Yes|Jobbstartshändelser|Antal|Totalt|Totalt antal jobb som har startats.|jobId|
|JobTerminateCompleteEvent|Yes|Slutförandehändelser för jobb|Antal|Totalt|Totalt antal jobb som har avslutats.|jobId|
|JobTerminateStartEvent|Yes|Starthändelser för jobb som avslutas|Antal|Totalt|Totalt antal jobb som har begärts att avslutas.|jobId|
|LeavingPoolNodeCount|No|Lämna antalet poolnoder|Antal|Totalt|Antal noder som lämnar poolen|Inga dimensioner|
|LowPriorityCoreCount|No|Antal lowPriority-kärnor|Antal|Totalt|Totalt antal lågprioriterade kärnor i Batch-kontot|Inga dimensioner|
|OfflineNodeCount|No|Antal offlinenoder|Antal|Totalt|Antal offlinenoder|Inga dimensioner|
|PoolSkapaEvent|Yes|Skapa händelser för pooler|Antal|Totalt|Totalt antal pooler som har skapats|poolId|
|PoolDeleteCompleteEvent|Yes|Slutförandehändelser för borttagning av pool|Antal|Totalt|Totalt antal pool borttagningar som har slutförts|poolId|
|PoolDeleteStartEvent|Yes|Starthändelser för borttagning av pool|Antal|Totalt|Totalt antal pool borttagningar som har startats|poolId|
|PoolResizeCompleteEvent|Yes|Slutför händelser för storleksändring av pool|Antal|Totalt|Totalt antal storleksändringar för poolen som har slutförts|poolId|
|PoolResizeStartEvent|Yes|Starthändelser för storleksändring av pool|Antal|Totalt|Totalt antal storleksändringar för pool som har startat|poolId|
|PreemptedNodeCount|No|Antal avindelade noder|Antal|Totalt|Antal avindelade noder|Inga dimensioner|
|Starta omNodeCount|No|Starta om antal noder|Antal|Totalt|Antal noder som startas om|Inga dimensioner|
|Avbildning avNodeCount|No|Avbildning av nodantal|Antal|Totalt|Antal avbildningsnoder|Inga dimensioner|
|RunningNodeCount|No|Antal noder som körs|Antal|Totalt|Antal noder som körs|Inga dimensioner|
|StartingNodeCount|No|Antal startnoder|Antal|Totalt|Antalet noder som startar|Inga dimensioner|
|StartTaskFailedNodeCount|No|Antal misslyckade noder för startaktivitet|Antal|Totalt|Antal noder där startaktiviteten misslyckades|Inga dimensioner|
|TaskCompleteEvent|Yes|Aktivitets slutförda händelser|Antal|Totalt|Totalt antal uppgifter som har slutförts|poolId, jobId|
|TaskFailEvent|Yes|Aktivitetshändelser som misslyckas|Antal|Totalt|Totalt antal uppgifter som har slutförts i ett misslyckat tillstånd|poolId, jobId|
|TaskStartEvent|Yes|Aktivitetsstartshändelser|Antal|Totalt|Totalt antal aktiviteter som har startat|poolId, jobId|
|TotalLowPriorityNodeCount|No|Low-Priority antal noder|Antal|Totalt|Totalt antal noder med låg prioritet i Batch-kontot|Inga dimensioner|
|TotalNodeCount|No|Antal dedikerade noder|Antal|Totalt|Totalt antal dedikerade noder i Batch-kontot|Inga dimensioner|
|UnusableNodeCount|No|Antal oanvändbara noder|Antal|Totalt|Antal oanvändbara noder|Inga dimensioner|
|WaitingForStartTaskNodeCount|No|Väntar på antal startaktivitetsnoder|Antal|Totalt|Antal noder som väntar på att startaktiviteten ska slutföras|Inga dimensioner|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/workspaces

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Aktiva kärnor|Yes|Aktiva kärnor|Antal|Genomsnitt|Antal aktiva kärnor|Scenario, ClusterName|
|Aktiva noder|Yes|Aktiva noder|Antal|Genomsnitt|Antal noder som körs|Scenario, ClusterName|
|Inaktiva kärnor|Yes|Inaktiva kärnor|Antal|Genomsnitt|Antal inaktiva kärnor|Scenario, ClusterName|
|Inaktiva noder|Yes|Inaktiva noder|Antal|Genomsnitt|Antal inaktiva noder|Scenario, ClusterName|
|Jobbet har slutförts|Yes|Jobbet har slutförts|Antal|Totalt|Antal slutförda jobb|Scenario, ClusterName, ResultType|
|Jobbet skickades|Yes|Jobbet skickades|Antal|Totalt|Antal jobb som skickats|Scenario, ClusterName|
|Lämna kärnor|Yes|Lämna kärnor|Antal|Genomsnitt|Antal lämnande kärnor|Scenario, ClusterName|
|Lämna noder|Yes|Lämna noder|Antal|Genomsnitt|Antal noder som lämnar|Scenario, ClusterName|
|Avinstallerade kärnor|Yes|Avinstallerade kärnor|Antal|Genomsnitt|Antal avindelade kärnor|Scenario, ClusterName|
|Avindelade noder|Yes|Avindelade noder|Antal|Genomsnitt|Antal avindelade noder|Scenario, ClusterName|
|Kvotanvändning i procent|Yes|Kvotanvändning i procent|Antal|Genomsnitt|Procent av utnyttjad kvot|Scenario, ClusterName, VmFamilyName, VmPriority|
|Totalt antal kärnor|Yes|Totalt antal kärnor|Antal|Genomsnitt|Antal totalt antal kärnor|Scenario, ClusterName|
|Totalt antal noder|Yes|Totalt antal noder|Antal|Genomsnitt|Antal totalt antal noder|Scenario, ClusterName|
|Oanvändbara kärnor|Yes|Oanvändbara kärnor|Antal|Genomsnitt|Antal oanvändbara kärnor|Scenario, ClusterName|
|Oanvändbara noder|Yes|Oanvändbara noder|Antal|Genomsnitt|Antal oanvändbara noder|Scenario, ClusterName|

## <a name="microsoftbingaccounts"></a>microsoft.bing/accounts

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|BlockedCalls|Yes|Blockerade anrop|Antal|Totalt|Antal anrop som överskridit frekvensen eller kvotgränsen|ApiName, ServingRegion, StatusCode|
|ClientErrors|Yes|Klientfel|Antal|Totalt|Antal anrop med klientfel (HTTP-statuskod 4xx)|ApiName, ServingRegion, StatusCode|
|DataIn|Yes|Indata|Byte|Totalt|Innehållslängd för inkommande begäran i byte|ApiName, ServingRegion, StatusCode|
|DataOut|Yes|Data ut|Byte|Totalt|Utgående svar Innehållslängd i byte|ApiName, ServingRegion, StatusCode|
|Svarstid|Yes|Svarstid|Millisekunder|Genomsnitt|Svarstid i millisekunder|ApiName, ServingRegion, StatusCode|
|ServerErrors|Yes|Serverfel|Antal|Totalt|Antal anrop med serverfel (HTTP-statuskod 5xx)|ApiName, ServingRegion, StatusCode|
|SuccessfulCalls|Yes|Lyckade anrop|Antal|Totalt|Antal lyckade anrop (HTTP-statuskod 2xx)|ApiName, ServingRegion, StatusCode|
|TotalCalls|Yes|Totalt antal anrop|Antal|Totalt|Totalt antal anrop|ApiName, ServingRegion, StatusCode|
|TotalErrors|Yes|Totalt antal fel|Antal|Totalt|Antal anrop med fel (HTTP-statuskod 4xx eller 5xx)|ApiName, ServingRegion, StatusCode|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft.Blockchain/blockchainMembers

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|BroadcastProcessedCount|Yes|BroadcastProcessedCountDisplayName|Antal|Genomsnitt|Antalet bearbetade transaktioner.|Nod, kanal, typ, status|
|ChaincodeExecuteTimeouts|Yes|ChaincodeExecuteTimeoutsDisplayName|Antal|Genomsnitt|Antalet kedjekodskörningar (Init eller Invoke) som har en time out.|Nod, kedjakod|
|ChaincodeLaunchFailures|Yes|ChaincodeLaunchFailuresDisplayName|Antal|Genomsnitt|Antalet kedjekodsstarter som har misslyckats.|Nod, kedjakod|
|ChaincodeLaunchTimeouts|Yes|ChaincodeLaunchTimeoutsDisplayName|Antal|Genomsnitt|Antalet kedjekodsstarter som har en time out.|Nod, kedjakod|
|ChaincodeShimRequestsCompleted|Yes|ChaincodeShimRequestsCompletedDisplayName|Antal|Genomsnitt|Antalet kedjekods-shim-begäranden som slutförts.|Node, type, channel, chaincode, success|
|ChaincodeShimRequestsReceived|Yes|ChaincodeShimRequestsReceivedDisplayName|Antal|Genomsnitt|Antalet kedjekodade shim-begäranden som tagits emot.|Nod, typ, kanal, kedjakod|
|ClusterCommEgressQueueCapacity|Yes|ClusterCommEgressQueueCapacityDisplayName|Antal|Genomsnitt|Utgående kös kapacitet.|Node, host, msg_type, channel|
|ClusterCommEgressQueueLength|Yes|ClusterCommEgressQueueLengthDisplayName|Antal|Genomsnitt|Längden på den utgående kön.|Node, host, msg_type, channel|
|ClusterCommEgressQueueWorkers|Yes|ClusterCommEgressQueueWorkersDisplayName|Antal|Genomsnitt|Antal utgående köarbetare.|Nod, kanal|
|ClusterCommEgressStreamCount|Yes|ClusterCommEgressStreamCountDisplayName|Antal|Genomsnitt|Antal strömmar till andra noder.|Nod, kanal|
|ClusterCommEgressTlsConnectionCount|Yes|ClusterCommEgressTlsConnectionCountDisplayName|Antal|Genomsnitt|Antal TLS-anslutningar till andra noder.|Nod|
|ClusterCommIngressStreamCount|Yes|ClusterCommIngressStreamCountDisplayName|Antal|Genomsnitt|Antal strömmar från andra noder.|Nod|
|ClusterCommMsgDroppedCount|Yes|ClusterCommMsgDroppedCountDisplayName|Antal|Genomsnitt|Antal meddelanden som har släppts.|Nod, värd, kanal|
|ConnectionAccepted|Yes|Godkända anslutningar|Antal|Totalt|Godkända anslutningar|Nod|
|ConnectionActive|Yes|Aktiva anslutningar|Antal|Genomsnitt|Aktiva anslutningar|Nod|
|ConnectionHandled|Yes|Hanterade anslutningar|Antal|Totalt|Hanterade anslutningar|Nod|
|ConsensusEtcdraftActiveNodes|Yes|ConsensusEtcdraftActiveNodesDisplayName|Antal|Genomsnitt|Antalet aktiva noder i den här kanalen.|Nod, kanal|
|ConsensusEtcdraftClusterSize|Yes|ConsensusEtcdraftClusterSizeDisplayName|Antal|Genomsnitt|Antalet noder i den här kanalen.|Nod, kanal|
|ConsensusEtcdraftCommittedBlockNumber|Yes|ConsensusEtcdraftCommittedBlockNumberDisplayName|Antal|Genomsnitt|Blocknumret för det senaste blocket har utförts.|Nod, kanal|
|ConsensusEtcdraftConfigProposalsReceived|Yes|ConsensusEtcdraftConfigProposalsReceivedDisplayName|Antal|Genomsnitt|Det totala antalet mottagna förslag för transaktioner av konfigurationstyp.|Nod, kanal|
|ConsensusEtcdraftIsLeader|Yes|ConsensusEtcdraftIsLeaderDisplayName|Antal|Genomsnitt|Den aktuella nodens ledningsstatus: 1 om det är ledaren annars 0.|Nod, kanal|
|ConsensusEtcdraftLeaderChanges|Yes|ConsensusEtcdraftLeaderChangesDisplayName|Antal|Genomsnitt|Antalet ledande ändringar sedan processen startar.|Nod, kanal|
|ConsensusEtcdraftNormalProposalsReceived|Yes|ConsensusEtcdraftNormalProposalsReceivedDisplayName|Antal|Genomsnitt|Det totala antalet förslag som tagits emot för transaktioner av normal typ.|Nod, kanal|
|ConsensusEtcdraftProposalFailures|Yes|ConsensusEtcdraftProposalFailuresDisplayName|Antal|Genomsnitt|Antalet förslagsfel.|Nod, kanal|
|ConsensusEtcdraftSnapshotBlockNumber|Yes|ConsensusEtcdraftSnapshotBlockNumberDisplayName|Antal|Genomsnitt|Blocknumret för den senaste ögonblicksbilden.|Nod, kanal|
|ConsensusKafkaBatchSize|Yes|ConsensusKafkaBatchSizeDisplayName|Antal|Genomsnitt|Den genomsnittliga batchstorleken i byte som skickas till ämnen.|Nod, ämne|
|ConsensusKafkaCompressionRatio|Yes|ConsensusKafkaCompressionRatioDisplayName|Antal|Genomsnitt|Medelkomprimeringsförhållandet (i procent) för ämnen.|Nod, ämne|
|ConsensusKafkaIncomingByteRate|Yes|ConsensusKafkaIncomingByteRateDisplayName|Antal|Genomsnitt|Byte/andra avläsning av a brokers.|Nod, broker_id|
|ConsensusKafkaLastOffsetPersisted|Yes|ConsensusKafkaLastOffsetPersistedDisplayName|Antal|Genomsnitt|Förskjutningen som anges i blockmetadata för det senast inräknade blocket.|Nod, kanal|
|ConsensusKafkaOutgoingByteRate|Yes|ConsensusKafkaOutgoingByteRateDisplayName|Antal|Genomsnitt|Byte/sekund skrivs till a brokers.|Nod, broker_id|
|ConsensusKafkaRecordSendRate|Yes|ConsensusKafkaRecordSendRateDisplayName|Antal|Genomsnitt|Antalet poster per sekund som skickas till ämnen.|Nod, ämne|
|ConsensusKafkaRecordsPerRequest|Yes|ConsensusKafkaRecordsPerRequestDisplayName|Antal|Genomsnitt|Det genomsnittliga antalet poster som skickas per begäran till ämnen.|Nod, ämne|
|ConsensusKafkaRequestLatency|Yes|ConsensusKafkaRequestLatencyDisplayName|Antal|Genomsnitt|Den genomsnittliga svarstiden för förfrågningar i ms till a brokers.|Node, broker_id|
|ConsensusKafkaRequestRate|Yes|ConsensusKafkaRequestRateDisplayName|Antal|Genomsnitt|Begäranden/sekund skickas till a brokers.|Node, broker_id|
|ConsensusKafkaRequestSize|Yes|ConsensusKafkaRequestSizeDisplayName|Antal|Genomsnitt|Den genomsnittliga begärandestorleken i byte till a brokers.|Node, broker_id|
|ConsensusKafkaResponseRate|Yes|ConsensusKafkaResponseRateDisplayName|Antal|Genomsnitt|Begäranden/sekund skickas till a brokers.|Node, broker_id|
|ConsensusKafkaResponseSize|Yes|ConsensusKafkaResponseSizeDisplayName|Antal|Genomsnitt|Den genomsnittliga svarsstorleken i byte från a brokers.|Nod, broker_id|
|CpuUsagePercentageInDouble|Yes|Processoranvändning i procent|Procent|Maximal|Processoranvändning i procent|Nod|
|DeliverBlocksSent|Yes|DeliverBlocksSentDisplayName|Antal|Genomsnitt|Antalet block som skickas av leveranstjänsten.|Nod, kanal, filtrerad, data_type|
|DeliverRequestsCompleted|Yes|DeliverRequestsCompletedDisplayName|Antal|Genomsnitt|Antalet leveransbegäranden som har slutförts.|Nod, kanal, filtrerad, data_type, lyckades|
|DeliverRequestsReceived|Yes|DeliverRequestsReceivedDisplayName|Antal|Genomsnitt|Antalet leveransbegäranden som har tagits emot.|Nod, kanal, filtrerad, data_type|
|DeliverStreamsClosed|Yes|DeliverStreamsClosedDisplayName|Antal|Genomsnitt|Antalet GRPC-strömmar som har stängts för leveranstjänsten.|Nod|
|DeliverStreamsOpened|Yes|DeliverStreamsOpenedDisplayName|Antal|Genomsnitt|Antalet GRPC-strömmar som har öppnats för leveranstjänsten.|Nod|
|RekommenderarChaincodeInstantiationFailures|Yes|RekommenderarChaincodeInstantiationFailuresDisplayName|Antal|Genomsnitt|Antalet instansiering eller uppgradering av kedjekod som har misslyckats.|Nod, kanal, kedjakod|
|EndorserDuplicateTransactionFailures|Yes|EndorserDuplicateTransactionFailuresDisplayName|Antal|Genomsnitt|Antalet misslyckade förslag på grund av duplicerat transaktions-ID.|Nod, kanal, kedjakod|
|EndorserEndorsementFailures|Yes|EndorserEndorsementFailuresDisplayName|Antal|Genomsnitt|Antalet misslyckade bekräftelser.|Node, channel, chaincode, chaincodeerror|
|RekommenderareProposalAclFailures|Yes|RekommenderarProposalAclFailuresDisplayName|Antal|Genomsnitt|Antalet förslag som misslyckade ACL-kontroller.|Nod, kanal, kedjakod|
|RekommenderarProposalSimulationFailures|Yes|RekommenderarProposalSimulationFailuresDisplayName|Antal|Genomsnitt|Antalet misslyckade förslagssimuleringar.|Nod, kanal, kedjakod|
|RekommenderarProposalsReceived|Yes|RekommenderarProposalsReceivedDisplayName|Antal|Genomsnitt|Antalet mottagna förslag.|Nod|
|RekommenderarProposalValidationFailures|Yes|RekommenderarProposalValidationFailuresDisplayName|Antal|Genomsnitt|Antalet förslag som har misslyckats med den inledande verifieringen.|Nod|
|RekommenderarEuccessfulProposals|Yes|RekommenderarEuccessfulProposalsDisplayName|Antal|Genomsnitt|Antalet lyckade förslag.|Nod|
|FabricVersion|Yes|FabricVersionDisplayName|Antal|Genomsnitt|Den aktiva versionen av Infrastruktur.|Nod, version|
|MessageCommMessagesReceived|Yes|MessageCommMessagesReceivedDisplayName|Antal|Genomsnitt|Antal mottagna meddelanden.|Nod|
|MessageCommMessagesSent|Yes|MessageMessagesSentDisplayName|Antal|Genomsnitt|Antal skickade meddelanden.|Nod|
|KommandotCommOverflowCount|Yes|KommandoCommOverflowCountDisplayName|Antal|Genomsnitt|Antal utgående köbuffertspill.|Nod|
|FferLeaderElectionLeader|Yes|FferLeaderElectionLeaderDisplayName|Antal|Genomsnitt|Peer är ledare (1) eller följare (0).|Nod, kanal|
|MembershipTotalPeersKnown|Yes|MembershipTotalPeersKnownDisplayName|Antal|Genomsnitt|Totalt antal kända peer-peers.|Nod, kanal|
|PayloadBufferSize|Yes|PayloadBufferSizeDisplayName|Antal|Genomsnitt|Nyttolastbuffertens storlek.|Nod, kanal|
|StateHeight|Yes|StateHeightDisplayName|Antal|Genomsnitt|Aktuell höjd för huvudbok.|Nod, kanal|
|GrpcCommConnClosed|Yes|GrpcCommConnClosedDisplayName|Antal|Genomsnitt|gRPC-anslutningar har stängts. Öppet minus stängt är det aktiva antalet anslutningar.|Nod|
|GrpcCommConnOpened|Yes|GrpcCommConnOpenedDisplayName|Antal|Genomsnitt|gRPC-anslutningar öppnade. Öppet minus stängt är det aktiva antalet anslutningar.|Nod|
|GrpcServerStreamMessagesReceived|Yes|GrpcServerStreamMessagesReceivedDisplayName|Antal|Genomsnitt|Antalet mottagna strömmeddelanden.|Nod, tjänst, metod|
|GrpcServerStreamMessagesSent|Yes|GrpcServerStreamMessagesSentDisplayName|Antal|Genomsnitt|Antalet dataströmmeddelanden som skickats.|Nod, tjänst, metod|
|GrpcServerStreamRequestsCompleted|Yes|GrpcServerStreamRequestsCompletedDisplayName|Antal|Genomsnitt|Antalet slutförda dataströmsbegäranden.|Nod, tjänst, metod, kod|
|GrpcServerUnaryRequestsReceived|Yes|GrpcServerUnaryRequestsReceivedDisplayName|Antal|Genomsnitt|Antalet unära begäranden som tagits emot.|Node, service, method|
|IOReadBytes|Yes|Lästa byte för I//S|Byte|Totalt|Lästa byte för I//S|Nod|
|IOWriteBytes|Yes|Skrivbyte för I/O|Byte|Totalt|I/O-skrivningsbyte|Nod|
|LedgerBlockchainHeight|Yes|LedgerBlockchainHeightDisplayName|Antal|Genomsnitt|Höjden på kedjan i block.|Nod, kanal|
|LedgerTransactionCount|Yes|LedgerTransactionCountDisplayName|Antal|Genomsnitt|Antal bearbetade transaktioner.|Node, channel, transaction_type, chaincode, validation_code|
|LoggningsposterCheckad|Yes|LoggingEntriesCheckedDisplayName|Antal|Genomsnitt|Antal loggposter som kontrolleras mot den aktiva loggningsnivån.|Nod, nivå|
|LoggingEntriesWritten|Yes|LoggingEntriesWrittenDisplayName|Antal|Genomsnitt|Antal loggposter som har skrivits.|Nod, nivå|
|MemoryLimit|Yes|Minnesgräns|Byte|Genomsnitt|Minnesgräns|Nod|
|MinneAnvändning|Yes|Minnesanvändning|Byte|Genomsnitt|Minnesanvändning|Nod|
|MemoryUsagePercentageInDouble|Yes|Minnesanvändning i procent|Procent|Genomsnitt|Minnesanvändning i procent|Nod|
|PendingTransactions|Yes|Väntande transaktioner|Antal|Genomsnitt|Väntande transaktioner|Nod|
|ProcessedBlocks|Yes|Bearbetade block|Antal|Totalt|Bearbetade block|Nod|
|ProcessedTransactions|Yes|Bearbetade transaktioner|Antal|Totalt|Bearbetade transaktioner|Nod|
|QueuedTransactions|Yes|Transaktioner i kö|Antal|Genomsnitt|Transaktioner i kö|Nod|
|RequestHandled|Yes|Hanterade begäranden|Antal|Totalt|Hanterade begäranden|Nod|
|StorageUsage|Yes|Lagringsanvändning|Byte|Genomsnitt|Lagringsanvändning|Nod|


## <a name="microsoftbotservicebotservices"></a>microsoft.botservice/botservices

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|RequestLatency|Yes|Svarstid för begäran|Millisekunder|Totalt|Tid det tar för servern att bearbeta begäran|Operation, Authentication, Protocol, DataCenter|
|RequestsTraffic|Yes|Begär trafik|Procent|Antal|Antal begäranden som gjorts|Operation, Authentication, Protocol, StatusCode, StatusCodeClass, DataCenter|


## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|allcachehits|Yes|Cacheträffar (instansbaserade)|Antal|Totalt||ShardId, Port, Primary|
|allcachemisses|Yes|Cachemissar (instansbaserad)|Antal|Totalt||ShardId, Port, Primary|
|allcacheRead|Yes|Cacheläsning (instansbaserad)|BytesPerSecond|Maximal||ShardId, Port, Primary|
|allcacheWrite|Yes|Cacheskrivning (instansbaserad)|BytesPerSecond|Maximal||ShardId, Port, Primary|
|allconnectedclients|Yes|Anslutna klienter (instansbaserad)|Antal|Maximal||ShardId, Port, Primary|
|allevictedkeys|Yes|Avlägsnade nycklar (instansbaserad)|Antal|Totalt||ShardId, Port, Primary|
|allexpiredkeys|Yes|Utgångna nycklar (instansbaserade)|Antal|Totalt||ShardId, Port, Primary|
|allgetcommands|Yes|Hämtar (instansbaserad)|Antal|Totalt||ShardId, Port, Primary|
|alloperationsPerSecond|Yes|Åtgärder per sekund (instansbaserad)|Antal|Maximal||ShardId, Port, Primary|
|allserverLoad|Yes|Serverbelastning (instansbaserad)|Procent|Maximal||ShardId, Port, Primary|
|allsetcommands|Yes|Uppsättningar (instansbaserad)|Antal|Totalt||ShardId, Port, Primary|
|alltotalcommandsprocessed|Yes|Totalt antal åtgärder (instansbaserad)|Antal|Totalt||ShardId, Port, Primary|
|alltotalkeys|Yes|Totalt antal nycklar (instansbaserad)|Antal|Maximal||ShardId, Port, Primary|
|allusedmemory|Yes|Använt minne (instansbaserat)|Byte|Maximal||ShardId, Port, Primary|
|allusedmemorypercentage|Yes|Använt minne i procent (instansbaserad)|Procent|Maximal||ShardId, Port, Primary|
|allusedmemoryRss|Yes|ANVÄNT minne RSS (instansbaserad)|Byte|Maximal||ShardId, Port, Primary|
|cachehits|Yes|Cacheträffar|Antal|Totalt||ShardId|
|cachehits0|Yes|Cacheträffar (shard 0)|Antal|Totalt||Inga dimensioner|
|cachehits1|Yes|Cacheträffar (shard 1)|Antal|Totalt||Inga dimensioner|
|cachehits2|Yes|Cacheträffar (shard 2)|Antal|Totalt||Inga dimensioner|
|cachehits3|Yes|Cacheträffar (shard 3)|Antal|Totalt||Inga dimensioner|
|cachehits4|Yes|Cacheträffar (shard 4)|Antal|Totalt||Inga dimensioner|
|cachehits5|Yes|Cacheträffar (shard 5)|Antal|Totalt||Inga dimensioner|
|cachehits6|Yes|Cacheträffar (shard 6)|Antal|Totalt||Inga dimensioner|
|cachehits7|Yes|Cacheträffar (shard 7)|Antal|Totalt||Inga dimensioner|
|cachehits8|Yes|Cacheträffar (shard 8)|Antal|Totalt||Inga dimensioner|
|cachehits9|Yes|Cacheträffar (shard 9)|Antal|Totalt||Inga dimensioner|
|cacheLatency|Yes|Mikrosekunder med cachesvarstid (förhandsversion)|Antal|Genomsnitt||ShardId|
|cachemisses|Yes|Cachemissar|Antal|Totalt||ShardId|
|cachemisses0|Yes|Cachemissar (shard 0)|Antal|Totalt||Inga dimensioner|
|cachemisses1|Yes|Cachemissar (shard 1)|Antal|Totalt||Inga dimensioner|
|cachemisses2|Yes|Cachemissar (shard 2)|Antal|Totalt||Inga dimensioner|
|cachemisses3|Yes|Cachemissar (shard 3)|Antal|Totalt||Inga dimensioner|
|cachemisses4|Yes|Cachemissar (shard 4)|Antal|Totalt||Inga dimensioner|
|cachemisses5|Yes|Cachemissar (shard 5)|Antal|Totalt||Inga dimensioner|
|cachemisses6|Yes|Cachemissar (shard 6)|Antal|Totalt||Inga dimensioner|
|cachemisses7|Yes|Cachemissar (shard 7)|Antal|Totalt||Inga dimensioner|
|cachemisses8|Yes|Cachemissar (shard 8)|Antal|Totalt||Inga dimensioner|
|cachemisses9|Yes|Cachemissar (Shard 9)|Antal|Totalt||Inga dimensioner|
|cachemissrate|Yes|Missfrekvens för cache|Procent|cachemissrate||ShardId|
|cacheRead|Yes|Cacheläsning|BytesPerSecond|Maximal||ShardId|
|cacheRead0|Yes|Cacheläsning (shard 0)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead1|Yes|Cacheläsning (shard 1)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead2|Yes|Cacheläsning (shard 2)|BytePerSecond|Maximal||Inga dimensioner|
|cacheRead3|Yes|Cacheläsning (shard 3)|BytePerSecond|Maximal||Inga dimensioner|
|cacheRead4|Yes|Cacheläsning (shard 4)|BytePerSecond|Maximal||Inga dimensioner|
|cacheRead5|Yes|Cacheläsning (shard 5)|BytePerSecond|Maximal||Inga dimensioner|
|cacheRead6|Yes|Cacheläsning (shard 6)|BytePerSecond|Maximal||Inga dimensioner|
|cacheRead7|Yes|Cacheläsning (shard 7)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead8|Yes|Cacheläsning (shard 8)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead9|Yes|Cacheläsning (shard 9)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheWrite|Yes|Cacheskrivning|BytesPerSecond|Maximal||ShardId|
|cacheWrite0|Yes|Cacheskrivning (shard 0)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheWrite1|Yes|Cacheskrivning (shard 1)|BytePerSecond|Maximal||Inga dimensioner|
|cacheWrite2|Yes|Cacheskrivning (shard 2)|BytePerSecond|Maximal||Inga dimensioner|
|cacheWrite3|Yes|Cacheskrivning (shard 3)|BytePerSecond|Maximal||Inga dimensioner|
|cacheWrite4|Yes|Cacheskrivning (shard 4)|BytePerSecond|Maximal||Inga dimensioner|
|cacheWrite5|Yes|Cacheskrivning (shard 5)|BytePerSecond|Maximal||Inga dimensioner|
|cacheWrite6|Yes|Cacheskrivning (shard 6)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheWrite7|Yes|Cacheskrivning (shard 7)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheWrite8|Yes|Cacheskrivning (shard 8)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheWrite9|Yes|Cacheskrivning (shard 9)|BytesPerSecond|Maximal||Inga dimensioner|
|connectedclients|Yes|Anslutna klienter|Antal|Maximal||ShardId|
|connectedclients0|Yes|Anslutna klienter (shard 0)|Antal|Maximal||Inga dimensioner|
|connectedclients1|Yes|Anslutna klienter (shard 1)|Antal|Maximal||Inga dimensioner|
|connectedclients2|Yes|Anslutna klienter (shard 2)|Antal|Maximal||Inga dimensioner|
|connectedclients3|Yes|Anslutna klienter (shard 3)|Antal|Maximal||Inga dimensioner|
|connectedclients4|Yes|Anslutna klienter (shard 4)|Antal|Maximal||Inga dimensioner|
|connectedclients5|Yes|Anslutna klienter (shard 5)|Antal|Maximal||Inga dimensioner|
|connectedclients6|Yes|Anslutna klienter (shard 6)|Antal|Maximal||Inga dimensioner|
|connectedclients7|Yes|Anslutna klienter (Shard 7)|Antal|Maximal||Inga dimensioner|
|connectedclients8|Yes|Anslutna klienter (shard 8)|Antal|Maximal||Inga dimensioner|
|connectedclients9|Yes|Anslutna klienter (shard 9)|Antal|Maximal||Inga dimensioner|
|fel|Yes|Fel|Antal|Maximal||ShardId, ErrorType|
|evictedkeys|Yes|Avlägsnade nycklar|Antal|Totalt||ShardId|
|evictedkeys0|Yes|Avlägsnade nycklar (shard 0)|Antal|Totalt||Inga dimensioner|
|evictedkeys1|Yes|Avlägsnade nycklar (shard 1)|Antal|Totalt||Inga dimensioner|
|evictedkeys2|Yes|Avlägsnade nycklar (shard 2)|Antal|Totalt||Inga dimensioner|
|evictedkeys3|Yes|Avlägsnade nycklar (shard 3)|Antal|Totalt||Inga dimensioner|
|evictedkeys4|Yes|Avlägsnade nycklar (shard 4)|Antal|Totalt||Inga dimensioner|
|evictedkeys5|Yes|Avlägsnade nycklar (shard 5)|Antal|Totalt||Inga dimensioner|
|evictedkeys6|Yes|Avlägsnade nycklar (shard 6)|Antal|Totalt||Inga dimensioner|
|evictedkeys7|Yes|Avlägsnade nycklar (shard 7)|Antal|Totalt||Inga dimensioner|
|evictedkeys8|Yes|Avlägsnade nycklar (shard 8)|Antal|Totalt||Inga dimensioner|
|evictedkeys9|Yes|Avlägsnade nycklar (shard 9)|Antal|Totalt||Inga dimensioner|
|expiredkeys|Yes|Utgångna nycklar|Antal|Totalt||ShardId|
|expiredkeys0|Yes|Utgångna nycklar (shard 0)|Antal|Totalt||Inga dimensioner|
|expiredkeys1|Yes|Utgångna nycklar (shard 1)|Antal|Totalt||Inga dimensioner|
|expiredkeys2|Yes|Utgångna nycklar (shard 2)|Antal|Totalt||Inga dimensioner|
|expiredkeys3|Yes|Utgångna nycklar (shard 3)|Antal|Totalt||Inga dimensioner|
|expiredkeys4|Yes|Utgångna nycklar (Shard 4)|Antal|Totalt||Inga dimensioner|
|expiredkeys5|Yes|Utgångna nycklar (shard 5)|Antal|Totalt||Inga dimensioner|
|expiredkeys6|Yes|Nycklar som har upphört att gälla (Shard 6)|Antal|Totalt||Inga dimensioner|
|expiredkeys7|Yes|Nycklar som har upphört att gälla (Shard 7)|Antal|Totalt||Inga dimensioner|
|expiredkeys8|Yes|Nycklar som har upphört att gälla (Shard 8)|Antal|Totalt||Inga dimensioner|
|expiredkeys9|Yes|Nycklar som har upphört att gälla (shard 9)|Antal|Totalt||Inga dimensioner|
|getcommands|Yes|Hämtningar|Antal|Totalt||ShardId|
|getcommands0|Yes|Hämtar (shard 0)|Antal|Totalt||Inga dimensioner|
|getcommands1|Yes|Hämtar (shard 1)|Antal|Totalt||Inga dimensioner|
|getcommands2|Yes|Hämtar (shard 2)|Antal|Totalt||Inga dimensioner|
|getcommands3|Yes|Hämtar (shard 3)|Antal|Totalt||Inga dimensioner|
|getcommands4|Yes|Hämtar (shard 4)|Antal|Totalt||Inga dimensioner|
|getcommands5|Yes|Hämtar (shard 5)|Antal|Totalt||Inga dimensioner|
|getcommands6|Yes|Hämtar (shard 6)|Antal|Totalt||Inga dimensioner|
|getcommands7|Yes|Hämtar (shard 7)|Antal|Totalt||Inga dimensioner|
|getcommands8|Yes|Hämtar (shard 8)|Antal|Totalt||Inga dimensioner|
|getcommands9|Yes|Hämtar (shard 9)|Antal|Totalt||Inga dimensioner|
|operationsPerSecond|Yes|Åtgärder per sekund|Antal|Maximal||ShardId|
|operationsPerSecond0|Yes|Åtgärder per sekund (shard 0)|Antal|Maximal||Inga dimensioner|
|operationsPerSecond1|Yes|Åtgärder per sekund (shard 1)|Antal|Maximal||Inga dimensioner|
|operationsPerSecond2|Yes|Åtgärder per sekund (shard 2)|Antal|Maximal||Inga dimensioner|
|operationsPerSecond3|Yes|Åtgärder per sekund (shard 3)|Antal|Maximal||Inga dimensioner|
|operationsPerSecond4|Yes|Åtgärder per sekund (shard 4)|Antal|Maximal||Inga dimensioner|
|operationsPerSecond5|Yes|Åtgärder per sekund (shard 5)|Antal|Maximal||Inga dimensioner|
|operationsPerSecond6|Yes|Åtgärder per sekund (shard 6)|Antal|Maximal||Inga dimensioner|
|operationsPerSecond7|Yes|Åtgärder per sekund (shard 7)|Antal|Maximal||Inga dimensioner|
|operationsPerSecond8|Yes|Åtgärder per sekund (shard 8)|Antal|Maximal||Inga dimensioner|
|operationsPerSecond9|Yes|Åtgärder per sekund (shard 9)|Antal|Maximal||Inga dimensioner|
|percentProcessorTime|Yes|Processor|Procent|Maximal||ShardId|
|percentProcessorTime0|Yes|CPU (Shard 0)|Procent|Maximal||Inga dimensioner|
|percentProcessorTime1|Yes|CPU (Shard 1)|Procent|Maximal||Inga dimensioner|
|percentProcessorTime2|Yes|CPU (Shard 2)|Procent|Maximal||Inga dimensioner|
|percentProcessorTime3|Yes|CPU (Shard 3)|Procent|Maximal||Inga dimensioner|
|percentProcessorTime4|Yes|CPU (Shard 4)|Procent|Maximal||Inga dimensioner|
|percentProcessorTime5|Yes|CPU (Shard 5)|Procent|Maximal||Inga dimensioner|
|percentProcessorTime6|Yes|CPU (Shard 6)|Procent|Maximal||Inga dimensioner|
|percentProcessorTime7|Yes|CPU (Shard 7)|Procent|Maximal||Inga dimensioner|
|percentProcessorTime8|Yes|CPU (Shard 8)|Procent|Maximal||Inga dimensioner|
|percentProcessorTime9|Yes|CPU (Shard 9)|Procent|Maximal||Inga dimensioner|
|serverLoad|Yes|Serverbelastning|Procent|Maximal||ShardId|
|serverLoad0|Yes|Serverbelastning (shard 0)|Procent|Maximal||Inga dimensioner|
|serverLoad1|Yes|Serverbelastning (shard 1)|Procent|Maximal||Inga dimensioner|
|serverLoad2|Yes|Serverbelastning (shard 2)|Procent|Maximal||Inga dimensioner|
|serverLoad3|Yes|Serverbelastning (shard 3)|Procent|Maximal||Inga dimensioner|
|serverLoad4|Yes|Serverbelastning (shard 4)|Procent|Maximal||Inga dimensioner|
|serverLoad5|Yes|Serverbelastning (shard 5)|Procent|Maximal||Inga dimensioner|
|serverLoad6|Yes|Serverbelastning (shard 6)|Procent|Maximal||Inga dimensioner|
|serverLoad7|Yes|Serverbelastning (shard 7)|Procent|Maximal||Inga dimensioner|
|serverLoad8|Yes|Serverbelastning (shard 8)|Procent|Maximal||Inga dimensioner|
|serverLoad9|Yes|Serverbelastning (shard 9)|Procent|Maximal||Inga dimensioner|
|setcommands|Yes|Uppsättningar|Antal|Totalt||ShardId|
|setcommands0|Yes|Uppsättningar (shard 0)|Antal|Totalt||Inga dimensioner|
|setcommands1|Yes|Uppsättningar (shard 1)|Antal|Totalt||Inga dimensioner|
|setcommands2|Yes|Uppsättningar (shard 2)|Antal|Totalt||Inga dimensioner|
|setcommands3|Yes|Uppsättningar (shard 3)|Antal|Totalt||Inga dimensioner|
|setcommands4|Yes|Uppsättningar (shard 4)|Antal|Totalt||Inga dimensioner|
|setcommands5|Yes|Uppsättningar (shard 5)|Antal|Totalt||Inga dimensioner|
|setcommands6|Yes|Uppsättningar (shard 6)|Antal|Totalt||Inga dimensioner|
|setcommands7|Yes|Uppsättningar (shard 7)|Antal|Totalt||Inga dimensioner|
|setcommands8|Yes|Uppsättningar (shard 8)|Antal|Totalt||Inga dimensioner|
|setcommands9|Yes|Uppsättningar (shard 9)|Antal|Totalt||Inga dimensioner|
|totalcommandsprocessed|Yes|Totalt antal åtgärder|Antal|Totalt||ShardId|
|totalcommandsprocessed0|Yes|Totalt antal åtgärder (shard 0)|Antal|Totalt||Inga dimensioner|
|totalcommandsprocessed1|Yes|Totalt antal åtgärder (shard 1)|Antal|Totalt||Inga dimensioner|
|totalcommandsprocessed2|Yes|Totalt antal åtgärder (shard 2)|Antal|Totalt||Inga dimensioner|
|totalcommandsprocessed3|Yes|Totalt antal åtgärder (shard 3)|Antal|Totalt||Inga dimensioner|
|totalcommandsprocessed4|Yes|Totalt antal åtgärder (shard 4)|Antal|Totalt||Inga dimensioner|
|totalcommandsprocessed5|Yes|Totalt antal åtgärder (shard 5)|Antal|Totalt||Inga dimensioner|
|totalcommandsprocessed6|Yes|Totalt antal åtgärder (shard 6)|Antal|Totalt||Inga dimensioner|
|totalcommandsprocessed7|Yes|Totalt antal åtgärder (shard 7)|Antal|Totalt||Inga dimensioner|
|totalcommandsprocessed8|Yes|Totalt antal åtgärder (shard 8)|Antal|Totalt||Inga dimensioner|
|totalcommandsprocessed9|Yes|Totalt antal åtgärder (shard 9)|Antal|Totalt||Inga dimensioner|
|totalkeys|Yes|Totalt antal nycklar|Antal|Maximal||ShardId|
|totalkeys0|Yes|Totalt antal nycklar (shard 0)|Antal|Maximal||Inga dimensioner|
|totalkeys1|Yes|Totalt antal nycklar (shard 1)|Antal|Maximal||Inga dimensioner|
|totalkeys2|Yes|Totalt antal nycklar (shard 2)|Antal|Maximal||Inga dimensioner|
|totalkeys3|Yes|Totalt antal nycklar (shard 3)|Antal|Maximal||Inga dimensioner|
|totalkeys4|Yes|Totalt antal nycklar (shard 4)|Antal|Maximal||Inga dimensioner|
|totalkeys5|Yes|Totalt antal nycklar (shard 5)|Antal|Maximal||Inga dimensioner|
|totalkeys6|Yes|Totalt antal nycklar (shard 6)|Antal|Maximal||Inga dimensioner|
|totalkeys7|Yes|Totalt antal nycklar (shard 7)|Antal|Maximal||Inga dimensioner|
|totalkeys8|Yes|Totalt antal nycklar (shard 8)|Antal|Maximal||Inga dimensioner|
|totalkeys9|Yes|Totalt antal nycklar (shard 9)|Antal|Maximal||Inga dimensioner|
|usedmemory|Yes|Använt minne|Byte|Maximal||ShardId|
|usedmemory0|Yes|Använt minne (shard 0)|Byte|Maximal||Inga dimensioner|
|usedmemory1|Yes|Använt minne (shard 1)|Byte|Maximal||Inga dimensioner|
|usedmemory2|Yes|Använt minne (shard 2)|Byte|Maximal||Inga dimensioner|
|usedmemory3|Yes|Använt minne (shard 3)|Byte|Maximal||Inga dimensioner|
|usedmemory4|Yes|Använt minne (shard 4)|Byte|Maximal||Inga dimensioner|
|usedmemory5|Yes|Använt minne (shard 5)|Byte|Maximal||Inga dimensioner|
|usedmemory6|Yes|Använt minne (shard 6)|Byte|Maximal||Inga dimensioner|
|usedmemory7|Yes|Använt minne (shard 7)|Byte|Maximal||Inga dimensioner|
|usedmemory8|Yes|Använt minne (shard 8)|Byte|Maximal||Inga dimensioner|
|usedmemory9|Yes|Använt minne (shard 9)|Byte|Maximal||Inga dimensioner|
|usedmemorypercentage|Yes|Använt minne i procent|Procent|Maximal||ShardId|
|usedmemoryRss|Yes|RSS för använt minne|Byte|Maximal||ShardId|
|usedmemoryRss0|Yes|ANVÄNT minne RSS (Shard 0)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss1|Yes|ANVÄNT minne RSS (Shard 1)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss2|Yes|ANVÄNT minne RSS (Shard 2)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss3|Yes|ANVÄNT minne RSS (Shard 3)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss4|Yes|ANVÄNT minne RSS (Shard 4)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss5|Yes|ANVÄNT minne RSS (Shard 5)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss6|Yes|ANVÄNT minne RSS (Shard 6)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss7|Yes|ANVÄNT minne RSS (Shard 7)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss8|Yes|ANVÄNT minne RSS (Shard 8)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss9|Yes|ANVÄNT minne RSS (Shard 9)|Byte|Maximal||Inga dimensioner|


## <a name="microsoftcacheredisenterprise"></a>Microsoft.Cache/redisEnterprise

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|cachehits|Yes|Cacheträffar|Antal|Totalt||Inga dimensioner|
|cacheLatency|Yes|Mikrosekunder med cachesvarstid (förhandsversion)|Antal|Genomsnitt||InstanceId|
|cachemisses|Yes|Cachemissar|Antal|Totalt||InstanceId|
|cacheRead|Yes|Cacheläsning|BytesPerSecond|Maximal||InstanceId|
|cacheWrite|Yes|Cacheskrivning|BytesPerSecond|Maximal||InstanceId|
|connectedclients|Yes|Anslutna klienter|Antal|Maximal||InstanceId|
|fel|Yes|Fel|Antal|Maximal||InstanceId, ErrorType|
|evictedkeys|Yes|Avlägsnade nycklar|Antal|Totalt||Inga dimensioner|
|expiredkeys|Yes|Utgångna nycklar|Antal|Totalt||Inga dimensioner|
|getcommands|Yes|Hämtningar|Antal|Totalt||Inga dimensioner|
|operationsPerSecond|Yes|Åtgärder per sekund|Antal|Maximal||Inga dimensioner|
|percentProcessorTime|Yes|Processor|Procent|Maximal||InstanceId|
|serverLoad|Yes|Serverbelastning|Procent|Maximal||Inga dimensioner|
|setcommands|Yes|Uppsättningar|Antal|Totalt||Inga dimensioner|
|totalcommandsprocessed|Yes|Totalt antal åtgärder|Antal|Totalt||Inga dimensioner|
|totalkeys|Yes|Totalt antal nycklar|Antal|Maximal||Inga dimensioner|
|usedmemory|Yes|Använt minne|Byte|Maximal||Inga dimensioner|
|usedmemorypercentage|Yes|Använt minne i procent|Procent|Maximal||InstanceId|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft.Cdn/cdnwebapplicationfirewallpolicies

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Yes|Web Application Firewall antal förfrågningar|Antal|Totalt|Antalet klientbegäranden som bearbetas av Web Application Firewall|PolicyName, RuleName, Action|


## <a name="microsoftcdnprofiles"></a>Microsoft.Cdn/profiles

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ByteHitRatio|Yes|Byteträffsförhållande|Procent|Genomsnitt|Det här är förhållandet mellan det totala antalet byte som betjänas från cachen jämfört med det totala antalet svarsbyte|Slutpunkt|
|OriginHealthPercentage|Yes|Hälsoprocent för ursprung|Procent|Genomsnitt|Procentandelen lyckade hälsoavsökningar från AFDX till backends.|Origin, OriginGroup|
|OriginLatency|Yes|Svarstid för ursprung|Millisekunder|Genomsnitt|Den tid som beräknas från när begäran skickades av AFDX-kanten till backend tills AFDX tog emot den senaste svarsbyten från backend.|Ursprung, slutpunkt|
|OriginRequestCount|Yes|Antal ursprungsbegäran|Antal|Totalt|Antalet begäranden som skickas från AFDX till ursprung.|HttpStatus, HttpStatusGroup, Origin, Endpoint|
|Procent4XX|Yes|Procent av 4XX|Procent|Genomsnitt|Procentandelen av alla klientbegäranden där svarsstatuskoden är 4XX|Endpoint, ClientRegion, ClientCountry|
|Procent5XX|Yes|Procent av 5XX|Procent|Genomsnitt|Procentandelen av alla klientbegäranden där svarsstatuskoden är 5XX|Endpoint, ClientRegion, ClientCountry|
|RequestCount|Yes|Antal begäranden|Antal|Totalt|Antalet klientbegäranden som betjänas av HTTP/S-proxyn|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, Endpoint|
|RequestSize|Yes|Begärandestorlek|Byte|Totalt|Antalet byte som skickas som begäranden från klienter till AFDX.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, Endpoint|
|ResponseSize|Yes|Svarsstorlek|Byte|Totalt|Antalet byte som skickas som svar från HTTP/S-proxyn till klienter|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, Endpoint|
|TotalLatency|Yes|Total svarstid|Millisekunder|Genomsnitt|Den tid som beräknas från den tidpunkt då klientbegäran togs emot av HTTP/S-proxyn tills klienten bekräftade den sista svarsbyten från HTTP/S-proxyn|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, Endpoint|
|WebApplicationFirewallRequestCount|Yes|Web Application Firewall antal förfrågningar|Antal|Totalt|Antalet klientbegäranden som bearbetas av Web Application Firewall|PolicyName, RuleName, Action|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft.ClassicCompute/domainNames/slots/roles

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Byte/sek för diskläsning|No|Diskläsning|BytesPerSecond|Genomsnitt|Genomsnittlig byteläsning från disk under övervakningsperioden.|RoleInstanceId|
|Diskläsningsåtgärder/s|Yes|Diskläsningsåtgärder/s|CountPerSecond|Genomsnitt|Diskläsnings-IOPS.|RoleInstanceId|
|Diskskrivningsbyte/s|No|Diskskrivning|BytePerSecond|Genomsnitt|Genomsnittliga byte som skrivs till disk under övervakningsperioden.|RoleInstanceId|
|Diskskrivningsåtgärder/s|Yes|Diskskrivningsåtgärder/s|CountPerSecond|Genomsnitt|Diskskrivnings-IOPS.|RoleInstanceId|
|Nätverk – inkommande|Yes|Nätverk – inkommande|Byte|Totalt|Antalet byte som tas emot i alla nätverksgränssnitt av de virtuella datorerna (inkommande trafik).|RoleInstanceId|
|Nätverk – utgående|Yes|Nätverk – utgående|Byte|Totalt|Antalet byte ut i alla nätverksgränssnitt av de virtuella datorerna (utgående trafik).|RoleInstanceId|
|Processorprocentandel|Yes|Processorprocentandel|Procent|Genomsnitt|Procentandelen allokerade beräkningsenheter som för närvarande används av de virtuella datorerna.|RoleInstanceId|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Byte/sek för diskläsning|No|Diskläsning|BytesPerSecond|Genomsnitt|Genomsnittlig byteläsning från disk under övervakningsperioden.|Inga dimensioner|
|Diskläsningsåtgärder/s|Yes|Diskläsningsåtgärder/s|CountPerSecond|Genomsnitt|Diskläsnings-IOPS.|Inga dimensioner|
|Diskskrivningsbyte/sek|No|Diskskrivning|BytesPerSecond|Genomsnitt|Genomsnittliga byte som skrivs till disk under övervakningsperioden.|Inga dimensioner|
|Diskskrivningsåtgärder/sek|Yes|Diskskrivningsåtgärder/s|CountPerSecond|Genomsnitt|Diskskrivnings-IOPS.|Inga dimensioner|
|Nätverk – inkommande|Yes|Nätverk – inkommande|Byte|Totalt|Antalet byte som tas emot i alla nätverksgränssnitt av de virtuella datorerna (inkommande trafik).|Inga dimensioner|
|Nätverk – utgående|Yes|Nätverk – utgående|Byte|Totalt|Antalet byte ut i alla nätverksgränssnitt av de virtuella datorerna (utgående trafik).|Inga dimensioner|
|Processorprocentandel|Yes|Processorprocentandel|Procent|Genomsnitt|Procentandelen allokerade beräkningsenheter som för närvarande används av de virtuella datorerna.|Inga dimensioner|


## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft.ClassicStorage/storageAccounts

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Yes|Tillgänglighet|Procent|Genomsnitt|Procentandelen tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType, ApiName, Autentisering|
|Utgående|Yes|Utgående|Byte|Totalt|Mängden utgående data i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|GeoType, ApiName, Autentisering|
|Ingress|Yes|Ingress|Byte|Totalt|Mängden inkommande data i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|GeoType, ApiName, Autentisering|
|SuccessE2ELatency|Yes|Lyckad E2E-svarstid|Millisekunder|Genomsnitt|Svarstiden från slutet till slut för lyckade begäranden som görs till en lagringstjänst eller den angivna API-åtgärden, i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|GeoType, ApiName, Autentisering|
|SuccessServerLatency|Yes|Lyckad serversvarstid|Millisekunder|Genomsnitt|Svarstiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|GeoType, ApiName, Autentisering|
|Transaktioner|Yes|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType-dimension för antalet olika svarstyper.|ResponseType, GeoType, ApiName, Authentication|
|UsedCapacity|No|Använd kapacitet|Byte|Genomsnitt|Kapacitet som används av konto|Inga dimensioner|


## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft.ClassicStorage/storageAccounts/blobServices

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Yes|Tillgänglighet|Procent|Genomsnitt|Procentandelen tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType, ApiName, Autentisering|
|BlobCapacity|No|Blobkapacitet|Byte|Genomsnitt|Mängden lagringsutrymme som används av lagringskontots Blob Service i byte.|BlobType, Tier|
|BlobCount|No|Antal blobar|Antal|Genomsnitt|Antalet blobar i lagringskontots blobtjänst.|BlobType, Tier|
|ContainerCount|Yes|Antal blobcontainrar|Antal|Genomsnitt|Antalet containrar i lagringskontots Blob Service.|Inga dimensioner|
|Utgående|Yes|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|GeoType, ApiName, Autentisering|
|IndexCapacity|No|Indexkapacitet|Byte|Genomsnitt|Mängden lagringsutrymme som används av ADLS Gen2 (hierarkiskt) index i byte.|Inga dimensioner|
|Ingress|Yes|Ingress|Byte|Totalt|Mängden inkommande data i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|GeoType, ApiName, Autentisering|
|SuccessE2ELatency|Yes|Lyckad E2E-svarstid|Millisekunder|Genomsnitt|Svarstiden från slutet till slut för lyckade begäranden som görs till en lagringstjänst eller den angivna API-åtgärden, i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|GeoType, ApiName, Autentisering|
|SuccessServerLatency|Yes|Lyckad serversvarstid|Millisekunder|Genomsnitt|Svarstiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|GeoType, ApiName, Autentisering|
|Transaktioner|Yes|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType-dimension för antalet olika svarstyper.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft.ClassicStorage/storageAccounts/fileServices

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Yes|Tillgänglighet|Procent|Genomsnitt|Procentandelen tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType, ApiName, Authentication, FileShare|
|Utgående|Yes|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|GeoType, ApiName, Authentication, FileShare|
|FileCapacity|No|Filkapacitet|Byte|Genomsnitt|Mängden lagringsutrymme som används av lagringskontots filtjänst i byte.|FileShare|
|FileCount|No|Antal filer|Antal|Genomsnitt|Antalet filer i lagringskontots filtjänst.|FileShare|
|FileShareCount|No|Antal filresurs|Antal|Genomsnitt|Antalet filresurser i lagringskontots filtjänst.|Inga dimensioner|
|FileShareQuota|No|Storlek på filresurskvot|Byte|Genomsnitt|Den övre gränsen för mängden lagringsutrymme som kan användas av Azure Files Service i byte.|FileShare|
|FileShareSnapshotCount|No|Antal ögonblicksbilder av filresurs|Antal|Genomsnitt|Antalet ögonblicksbilder som finns på resursen i lagringskontots Files Service.|FileShare|
|FileShareSnapshotSize|No|Storlek på ögonblicksbild av filresurs|Byte|Genomsnitt|Mängden lagringsutrymme som används av ögonblicksbilderna i lagringskontots filtjänst i byte.|FileShare|
|Ingress|Yes|Ingress|Byte|Totalt|Mängden inkommande data i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|GeoType, ApiName, Autentisering, Filresurs|
|SuccessE2ELatency|Yes|Lyckad E2E-svarstid|Millisekunder|Genomsnitt|Svarstiden från slutet till slut för lyckade begäranden som görs till en lagringstjänst eller den angivna API-åtgärden, i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|GeoType, ApiName, Authentication, FileShare|
|SuccessServerLatency|Yes|Lyckad serversvarstid|Millisekunder|Genomsnitt|Svarstiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|GeoType, ApiName, Authentication, FileShare|
|Transaktioner|Yes|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType-dimension för antalet olika svarstyper.|ResponseType, GeoType, ApiName, Authentication, FileShare|


## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft.ClassicStorage/storageAccounts/queueServices

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Yes|Tillgänglighet|Procent|Genomsnitt|Procentandelen tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType, ApiName, Autentisering|
|Utgående|Yes|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|GeoType, ApiName, Autentisering|
|Ingress|Yes|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|GeoType, ApiName, Autentisering|
|QueueCapacity|Yes|Kökapacitet|Byte|Genomsnitt|Mängden lagringsutrymme som används av lagringskontots lagringskonto Kötjänst i byte.|Inga dimensioner|
|QueueCount|Yes|Antal köer|Antal|Genomsnitt|Antalet köer i lagringskontots Kötjänst.|Inga dimensioner|
|QueueMessageCount|Yes|Antal kömeddelanden|Antal|Genomsnitt|Det ungefärliga antalet kömeddelanden i lagringskontots Kötjänst.|Inga dimensioner|
|SuccessE2ELatency|Yes|Lyckad E2E-svarstid|Millisekunder|Genomsnitt|Svarstiden från slutet till slut för lyckade begäranden som görs till en lagringstjänst eller den angivna API-åtgärden, i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|GeoType, ApiName, Autentisering|
|SuccessServerLatency|Yes|Lyckad serversvarstid|Millisekunder|Genomsnitt|Svarstiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|GeoType, ApiName, Autentisering|
|Transaktioner|Yes|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType-dimensionen för antalet olika typer av svar.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft.ClassicStorage/storageAccounts/tableServices

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Yes|Tillgänglighet|Procent|Genomsnitt|Procentandelen tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType, ApiName, Autentisering|
|Utgående|Yes|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|GeoType, ApiName, Autentisering|
|Ingress|Yes|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|GeoType, ApiName, Autentisering|
|SuccessE2ELatency|Yes|Lyckad E2E-svarstid|Millisekunder|Genomsnitt|Svarstiden från slutet till slut för lyckade begäranden som görs till en lagringstjänst eller den angivna API-åtgärden, i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|GeoType, ApiName, Autentisering|
|SuccessServerLatency|Yes|Lyckad serversvarstid|Millisekunder|Genomsnitt|Svarstiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|GeoType, ApiName, Autentisering|
|TableCapacity|Yes|Tabellkapacitet|Byte|Genomsnitt|Mängden lagringsutrymme som används av lagringskontots Table Service i byte.|Inga dimensioner|
|TableCount|Yes|Antal tabeller|Antal|Genomsnitt|Antalet tabeller i lagringskontots table-tjänst.|Inga dimensioner|
|TableEntityCount|Yes|Antal entiteter i tabellen|Antal|Genomsnitt|Antalet tabellentiteter i lagringskontots Tabelltjänst.|Inga dimensioner|
|Transaktioner|Yes|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType-dimension för antalet olika typer av svar.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|BlockedCalls|Yes|Blockerade anrop|Antal|Totalt|Antal anrop som har överskridit frekvensen eller kvotgränsen.|ApiName, OperationName, Region|
|TeckenTränad|Yes|Tecken tränade|Antal|Totalt|Totalt antal tecken som tränats.|ApiName, OperationName, Region|
|TeckenTranslaterad|Yes|Översatta tecken|Antal|Totalt|Totalt antal tecken i inkommande textbegäran.|ApiName, OperationName, Region|
|ClientErrors|Yes|Klientfel|Antal|Totalt|Antal anrop med fel på klientsidan (HTTP-svarskod 4xx).|ApiName, OperationName, Region|
|DataIn|Yes|Indata|Byte|Totalt|Storleken på inkommande data i byte.|ApiName, OperationName, Region|
|DataOut|Yes|Data ut|Byte|Totalt|Storleken på utgående data i byte.|ApiName, OperationName, Region|
|Svarstid|Yes|Svarstid|Millisekunder|Genomsnitt|Svarstid i millisekunder.|ApiName, OperationName, Region|
|LearnedEvents|Yes|Inlärda händelser|Antal|Totalt|Antal inlärda händelser.|IsMatchBaseline, Mode, RunId|
|MatchedRewards|Yes|Matchade förmåner|Antal|Totalt| Antal matchade förmåner.|IsMatchBaseline, Mode, RunId|
|ObservedRewards|Yes|Observerade förmåner|Antal|Totalt|Antal observerade förmåner.|IsMatchBaseline, Mode, RunId|
|ProcessedCharacters|Yes|Bearbetade tecken|Antal|Totalt|Antal tecken.|ApiName, FeatureName, UsageChannel, Region|
|ProcessedTextRecords|Yes|Bearbetade textposter|Antal|Totalt|Antal textposter.|ApiName, FeatureName, UsageChannel, Region|
|ServerErrors|Yes|Serverfel|Antal|Totalt|Antal anrop med internt tjänstfel (HTTP-svarskod 5xx).|ApiName, OperationName, Region|
|SpeechSessionDuration|Yes|Varaktighet för talsession|Sekunder|Totalt|Total varaktighet för talsession i sekunder.|ApiName, OperationName, Region|
|SuccessfulCalls|Yes|Lyckade anrop|Antal|Totalt|Antal lyckade anrop.|ApiName, OperationName, Region|
|TotalCalls|Yes|Totalt antal anrop|Antal|Totalt|Totalt antal anrop.|ApiName, OperationName, Region|
|TotalErrors|Yes|Totalt antal fel|Antal|Totalt|Totalt antal anrop med felsvar (HTTP-svarskod 4xx eller 5xx).|ApiName, OperationName, Region|
|TotalTokenCalls|Yes|Totalt antal token-anrop|Antal|Totalt|Totalt antal token-anrop.|ApiName, OperationName, Region|
|TotalTransactions|Yes|Totalt antal transaktioner|Antal|Totalt|Totalt antal transaktioner.|Inga dimensioner|


## <a name="microsoftcommunicationcommunicationservices"></a>Microsoft.Communication/CommunicationServices

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|APIRequestAuthentication|No|API-autentiseringsbegäranden|Antal|Antal|Antal förfrågningar mot slutpunkten för Communication Services autentisering.|Åtgärd, StatusCode, StatusCodeClass|
|APIRequestChat|Yes|Api-chattbegäranden|Antal|Antal|Antal alla begäranden mot den Communication Services chattslutpunkten.|Åtgärd, StatusCode, StatusCodeClass|
|APIRequestSMS|Yes|SMS-API begäranden|Antal|Antal|Antal förfrågningar mot SMS-Communication Services slutpunkten.|Åtgärd, StatusCode, StatusCodeClass|


## <a name="microsoftcomputecloudservices"></a>Microsoft.Compute/cloudServices

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Byte för diskläsning|Yes|Byte för diskläsning|Byte|Totalt|Byte som lästs från disk under övervakningsperioden|RoleInstanceId, RoleId|
|Diskläsningsåtgärder/s|Yes|Diskläsningsåtgärder/s|CountPerSecond|Genomsnitt|Diskläsnings-IOPS|RoleInstanceId, RoleId|
|Diskskrivningsbyte|Yes|Diskskrivningsbyte|Byte|Totalt|Byte som skrivs till disk under övervakningsperioden|RoleInstanceId, RoleId|
|Diskskrivningsåtgärder/s|Yes|Diskskrivningsåtgärder/s|CountPerSecond|Genomsnitt|Diskskrivnings-IOPS|RoleInstanceId, RoleId|
|Processorprocentandel|Yes|Processorprocentandel|Procent|Genomsnitt|Procentandelen allokerade beräkningsenheter som för närvarande används av de virtuella datorerna|RoleInstanceId, RoleId|


## <a name="microsoftcomputecloudservicesroles"></a>Microsoft.Compute/cloudServices/roles

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Byte för diskläsning|Yes|Byte för diskläsning|Byte|Totalt|Byte som lästs från disk under övervakningsperioden|RoleInstanceId, RoleId|
|Diskläsningsåtgärder/s|Yes|Diskläsningsåtgärder/s|CountPerSecond|Genomsnitt|Diskläsnings-IOPS|RoleInstanceId, RoleId|
|Diskskrivningsbyte|Yes|Diskskrivningsbyte|Byte|Totalt|Byte som skrivs till disk under övervakningsperioden|RoleInstanceId, RoleId|
|Diskskrivningsåtgärder/sek|Yes|Diskskrivningsåtgärder/sek|CountPerSecond|Genomsnitt|Diskskrivnings-IOPS|RoleInstanceId, RoleId|
|Processorprocentandel|Yes|Processorprocentandel|Procent|Genomsnitt|Procentandelen allokerade beräkningsenheter som för närvarande används av de virtuella datorerna|RoleInstanceId, RoleId|


## <a name="microsoftcomputedisks"></a>microsoft.compute/disks

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Sammansatta diskläsningsbyte/sek|No|Byte/sek för diskläsning (förhandsversion)|Byte|Genomsnitt|Byte/sek som läses från disk under övervakningsperioden. Observera att det här måttet är i förhandsversion och kan ändras innan det blir allmänt tillgängligt||
|Läsåtgärder för sammansatt disk/s|No|Diskläsningsåtgärder/s (förhandsversion)|Byte|Genomsnitt|Antal läs-I/O:er som utförs på en disk under övervakningsperioden. Observera att det här måttet är i förhandsversion och kan komma att ändras innan det blir allmänt tillgängligt||
|Sammansatta diskskrivningsbyte/sek|No|Diskskrivningsbyte/s (förhandsversion)|Byte|Genomsnitt|Byte/sek som skrivs till disk under övervakningsperioden. Observera att det här måttet är i förhandsversion och kan ändras innan det blir allmänt tillgängligt||
|Skrivåtgärder för sammansatta diskar/s|No|Diskskrivningsåtgärder/s (förhandsversion)|Byte|Genomsnitt|Antal skriv-I/O:er som utförs på en disk under övervakningsperioden. Observera att det här måttet är i förhandsversion och kan ändras innan det blir allmänt tillgängligt||


## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Förbrukade CPU-krediter|Yes|Förbrukade CPU-krediter|Antal|Genomsnitt|Totalt antal krediter som förbrukats av den virtuella datorn. Endast tillgängligt på virtuella datorer som kan burst-användas i B-serien|Inga dimensioner|
|Återstående CPU-krediter|Yes|Återstående CPU-krediter|Antal|Genomsnitt|Totalt antal krediter som är tillgängliga för burst. Endast tillgängligt på virtuella datorer som kan burst-användas i B-serien|Inga dimensioner|
|Förbrukad datadiskbandbredd i procent|Yes|Förbrukad datadiskbandbredd i procent|Procent|Genomsnitt|Procentandel förbrukad datadiskbandbredd per minut|Lun|
|Förbrukad IOPS-procentandel för datadisk|Yes|Förbrukad IOPS-procentandel för datadisk|Procent|Genomsnitt|Procentandel datadisk-I/O som förbrukas per minut|Lun|
|Maximal burst-bandbredd för datadisk|Yes|Maximal burst-bandbredd för datadisk|Antal|Genomsnitt|Maximalt antal byte per sekund dataflöde Datadisk kan uppnås med bursting|Lun|
|Maximal burst-IOPS för datadisk|Yes|Maximal burst-IOPS för datadisk|Antal|Genomsnitt|Maximal IOPS-datadisk kan uppnås med burst-ning|Lun|
|Ködjup för datadisk|Yes|Ködjup för datadisk|Antal|Genomsnitt|Ködjup för datadisk (eller kölängd)|Lun|
|Byte/sek för datadiskläsning|Yes|Byte/sek för datadiskläsning|BytesPerSecond|Genomsnitt|Byte/sek från en enskild disk under övervakningsperioden|Lun|
|Läsåtgärder för datadisk/s|Yes|Läsåtgärder för datadisk/s|CountPerSecond|Genomsnitt|Läsa IOPS från en enskild disk under övervakningsperioden|Lun|
|Målbandbredd för datadisk|Yes|Målbandbredd för datadisk|Antal|Genomsnitt|Baslinjebyte per sekund dataflöde Datadisk kan uppnå utan bursting|Lun|
|IOPS för datadiskmål|Yes|IOPS för datadiskmål|Antal|Genomsnitt|Baslinje-IOPS-datadisk kan uppnå utan bursting|Lun|
|Använda burst-krediter i bit/s-procent för datadisk|Yes|Använda burst-krediter i bit/s-procent för datadisk|Procent|Genomsnitt|Procentandel av datadiskens burst-bandbredd som använts hittills|Lun|
|Procentandel burst-I/O-krediter som används av datadisk|Yes|Procentandel burst-I/O-krediter som används av datadisk|Procent|Genomsnitt|Procentandel av datadiskens burst-I/O-krediter som använts hittills|Lun|
|Byte/sek för datadiskskrivning|Yes|Byte/sek för skrivning av datadisk|BytesPerSecond|Genomsnitt|Byte/sek som skrivs till en enskild disk under övervakningsperioden|Lun|
|Datadiskskrivningsåtgärder/sek|Yes|Datadiskskrivningsåtgärder/sek|CountPerSecond|Genomsnitt|Skriva IOPS från en enskild disk under övervakningsperioden|Lun|
|Byte för diskläsning|Yes|Byte för diskläsning|Byte|Totalt|Byte som lästs från disk under övervakningsperioden|Inga dimensioner|
|Diskläsningsåtgärder/s|Yes|Diskläsningsåtgärder/s|CountPerSecond|Genomsnitt|Diskläsnings-IOPS|Inga dimensioner|
|Diskskrivningsbyte|Yes|Diskskrivningsbyte|Byte|Totalt|Byte som skrivs till disk under övervakningsperioden|Inga dimensioner|
|Diskskrivningsåtgärder/s|Yes|Diskskrivningsåtgärder/s|CountPerSecond|Genomsnitt|Diskskrivnings-IOPS|Inga dimensioner|
|Inkommande flöden|Yes|Inkommande flöden|Antal|Genomsnitt|Inkommande flöden är antalet aktuella flöden i inkommande riktning (trafik till den virtuella datorn)|Inga dimensioner|
|Maximal skapandefrekvens för inkommande flöden|Yes|Maximal skapandefrekvens för inkommande flöden|CountPerSecond|Genomsnitt|Maximal skapandefrekvens för inkommande flöden (trafik till den virtuella datorn)|Inga dimensioner|
|Nätverk – inkommande|Yes|Nätverk i fakturerbart (inaktuellt)|Byte|Totalt|Antalet fakturerbara byte som tas emot i alla nätverksgränssnitt av de virtuella datorerna (inkommande trafik) (inaktuell)|Inga dimensioner|
|Nätverk totalt|Yes|Totalt nätverk|Byte|Totalt|Antalet byte som tas emot i alla nätverksgränssnitt av de virtuella datorerna (inkommande trafik)|Inga dimensioner|
|Nätverk – utgående|Yes|Fakturerbar för nätverk (inaktuell)|Byte|Totalt|Antalet fakturerbara byte i alla nätverksgränssnitt av de virtuella datorerna (utgående trafik) (inaktuell)|Inga dimensioner|
|Nätverk ut totalt|Yes|Nätverk ut totalt|Byte|Totalt|Antalet byte ut i alla nätverksgränssnitt av de virtuella datorerna (utgående trafik)|Inga dimensioner|
|Förbrukad bandbredd för operativsystemdisk i procent|Yes|Förbrukad bandbredd för operativsystemdisk i procent|Procent|Genomsnitt|Procentandel förbrukad diskbandbredd för operativsystem per minut|Lun|
|Förbrukad IOPS-procentandel för OS-disk|Yes|Förbrukad IOPS-procentandel för OS-disk|Procent|Genomsnitt|Procentandel operativsystemdisk-I/O som förbrukas per minut|Lun|
|Maximal burst-bandbredd för OS-disk|Yes|Maximal burst-bandbredd för OS-disk|Antal|Genomsnitt|Maximalt antal byte per sekund genomflöde OS-disken kan uppnå med bursting|Lun|
|Os-diskens högsta burst-IOPS|Yes|Os-diskens högsta burst-IOPS|Antal|Genomsnitt|Maximal IOPS OS-disk kan uppnå med bursting|Lun|
|Ködjup för OS-disk|Yes|Ködjup för OS-disk|Antal|Genomsnitt|Ködjup för operativsystemdisk (eller kölängd)|Inga dimensioner|
|Byte/s för os-diskläsning|Yes|Byte/sek för os-diskläsning|BytePerSecond|Genomsnitt|Byte/sek-läsning från en enskild disk under övervakningsperioden för OS-disk|Inga dimensioner|
|Läsåtgärder/s för OS-disk|Yes|Läsåtgärder/s för OS-disk|CountPerSecond|Genomsnitt|Läsa IOPS från en enskild disk under övervakningsperioden för OS-disk|Inga dimensioner|
|Målbandbredd för OS-disk|Yes|Målbandbredd för OS-disk|Antal|Genomsnitt|Baslinjebyte per sekund– OS-disk med dataflöde kan uppnås utan bursting|Lun|
|IOPS för OS-diskmål|Yes|IOPS för OS-diskmål|Antal|Genomsnitt|Baslinje-IOPS OS-disk kan uppnås utan bursting|Lun|
|Os-disk som används burst-bit/s-krediter i procent|Yes|Os-disk som används burst-bit/s-krediter i procent|Procent|Genomsnitt|Procentandel av os-disk burst-bandbreddskrediter som använts hittills|Lun|
|Procentandel burst-I/O-krediter på OS-disk|Yes|Procentandel burst-I/O-krediter på OS-disk|Procent|Genomsnitt|Procentandel av I/O-krediter för os-disk burst som använts hittills|Lun|
|Byte/sek för os-diskskrivning|Yes|Byte/sek för os-diskskrivning|BytesPerSecond|Genomsnitt|Byte/sek som skrivs till en enskild disk under övervakningsperioden för OS-disk|Inga dimensioner|
|Os-diskskrivningsåtgärder/sek|Yes|Os-diskskrivningsåtgärder/s|CountPerSecond|Genomsnitt|Skriva IOPS från en enskild disk under övervakningsperioden för OS-disken|Inga dimensioner|
|Utgående flöden|Yes|Utgående flöden|Antal|Genomsnitt|Utgående flöden är antalet aktuella flöden i den utgående riktningen (trafik som går ut från den virtuella datorn)|Inga dimensioner|
|Maximal skapandefrekvens för utgående flöden|Yes|Maximal skapandefrekvens för utgående flöden|CountPerSecond|Genomsnitt|Maximal skapandefrekvens för utgående flöden (trafik som går ut från den virtuella datorn)|Inga dimensioner|
|Processorprocentandel|Yes|Processorprocentandel|Procent|Genomsnitt|Procentandelen allokerade beräkningsenheter som för närvarande används av de virtuella datorerna|Inga dimensioner|
|Lästräff för Premium-datadiskcache|Yes|Lästräff för Premium-datadiskcache|Procent|Genomsnitt|Lästräff för Premium-datadiskcache|Lun|
|Läsmiss för Premium-datadiskcache|Yes|Läsmiss för Premium-datadiskcache|Procent|Genomsnitt|Läsmiss för Premium-datadiskcache|Lun|
|Lästräff för Premium OS-diskcache|Yes|Lästräff för Premium OS-diskcache|Procent|Genomsnitt|Lästräff för Premium OS-diskcache|Inga dimensioner|
|Läsmiss för Premium OS-diskcache|Yes|Läsmiss för Premium OS-diskcache|Procent|Genomsnitt|Läsmiss för Premium OS-diskcache|Inga dimensioner|
|Förbrukad bandbredd i procent för cachelagrad virtuell dator|Yes|Förbrukad bandbredd i procent för cachelagrad virtuell dator|Procent|Genomsnitt|Procentandel cachelagrad diskbandbredd som förbrukas av den virtuella datorn|Inga dimensioner|
|Procent förbrukad IOPS för cachelagrad virtuell dator|Yes|Förbrukad IOPS-procentandel för cachelagrad virtuell dator|Procent|Genomsnitt|Procentandel cachelagrad disk-IOPS som förbrukas av den virtuella datorn|Inga dimensioner|
|Förbrukad vm-bandbredd i procent|Yes|VM Uncached Bandwidth Consumed Percentage|Procent|Genomsnitt|Procentandel av den bortkopplade diskbandbredden som förbrukas av den virtuella datorn|Inga dimensioner|
|Procent förbrukad VM-frikopplad IOPS|Yes|Procent förbrukad VM-frikopplad IOPS|Procent|Genomsnitt|Procentandel IOPS för frikopplad disk som förbrukas av den virtuella datorn|Inga dimensioner|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Förbrukade CPU-krediter|Yes|Förbrukade CPU-krediter|Antal|Genomsnitt|Totalt antal krediter som förbrukats av den virtuella datorn. Endast tillgängligt på virtuella datorer som kan burst-användas i B-serien|Inga dimensioner|
|Återstående CPU-krediter|Yes|Återstående CPU-krediter|Antal|Genomsnitt|Totalt antal krediter som är tillgängliga för burst. Endast tillgängligt på B-seriens burstable-VM:ar|Inga dimensioner|
|Förbrukad datadiskbandbredd i procent|Yes|Förbrukad datadiskbandbredd i procent|Procent|Genomsnitt|Procentandel förbrukad datadiskbandbredd per minut|LUN, VMName|
|Förbrukad IOPS-procentandel för datadisk|Yes|Förbrukad IOPS-procentandel för datadisk|Procent|Genomsnitt|Procentandel datadisk-I/O som förbrukas per minut|LUN, VMName|
|Maximal burst-bandbredd för datadisk|Yes|Maximal burst-bandbredd för datadisk|Antal|Genomsnitt|Maximalt antal byte per sekund dataflöde Datadisk kan uppnås med bursting|LUN, VMName|
|Maximal burst-IOPS för datadisk|Yes|Maximal burst-IOPS för datadisk|Antal|Genomsnitt|Maximal IOPS-datadisk kan uppnås med burst-ning|LUN, VMName|
|Ködjup för datadisk|Yes|Ködjup för datadisk|Antal|Genomsnitt|Ködjup för datadisk (eller kölängd)|LUN, VMName|
|Byte/sek för datadiskläsning|Yes|Byte/sek för datadiskläsning|BytesPerSecond|Genomsnitt|Byte/sek-läsning från en enskild disk under övervakningsperioden|LUN, VMName|
|Läsåtgärder för datadisk/s|Yes|Läsåtgärder för datadisk/s|CountPerSecond|Genomsnitt|Läsa IOPS från en enskild disk under övervakningsperioden|LUN, VMName|
|Målbandbredd för datadisk|Yes|Målbandbredd för datadisk|Antal|Genomsnitt|Baslinjebyte per sekund dataflöde Datadisk kan uppnå utan bursting|LUN, VMName|
|IOPS för datadiskmål|Yes|IOPS för datadiskmål|Antal|Genomsnitt|Baslinje-IOPS-datadisk kan uppnå utan bursting|LUN, VMName|
|Använda burst-krediter i bit/s-procent för datadisk|Yes|Använda burst-krediter i bit/s-procent för datadisk|Procent|Genomsnitt|Procentandel av datadiskens burst-bandbredd som använts hittills|LUN, VMName|
|Procentandel burst-I/O-krediter som används av datadisk|Yes|Procentandel burst-I/O-krediter som används på datadisk|Procent|Genomsnitt|Procentandel av datadiskens burst-I/O-krediter som använts hittills|LUN, VMName|
|Byte/sek för datadiskskrivning|Yes|Byte/sek för skrivning av datadisk|BytesPerSecond|Genomsnitt|Byte/sek som skrivs till en enskild disk under övervakningsperioden|LUN, VMName|
|Datadiskskrivningsåtgärder/sek|Yes|Datadiskskrivningsåtgärder/sek|CountPerSecond|Genomsnitt|Skriva IOPS från en enskild disk under övervakningsperioden|LUN, VMName|
|Byte för diskläsning|Yes|Byte för diskläsning|Byte|Totalt|Byte som lästs från disk under övervakningsperioden|VMName|
|Diskläsningsåtgärder/s|Yes|Diskläsningsåtgärder/s|CountPerSecond|Genomsnitt|Diskläsnings-IOPS|VMName|
|Diskskrivningsbyte|Yes|Diskskrivningsbyte|Byte|Totalt|Byte som skrivs till disk under övervakningsperioden|VMName|
|Diskskrivningsåtgärder/s|Yes|Diskskrivningsåtgärder/s|CountPerSecond|Genomsnitt|Diskskrivnings-IOPS|VMName|
|Inkommande flöden|Yes|Inkommande flöden|Antal|Genomsnitt|Inkommande flöden är antalet aktuella flöden i inkommande riktning (trafik till den virtuella datorn)|VMName|
|Maximal skapandefrekvens för inkommande flöden|Yes|Maximal skapandefrekvens för inkommande flöden|CountPerSecond|Genomsnitt|Maximal skapandefrekvens för inkommande flöden (trafik till den virtuella datorn)|VMName|
|Nätverk – inkommande|Yes|Nätverk i fakturerbart (inaktuellt)|Byte|Totalt|Antalet fakturerbara byte som tas emot i alla nätverksgränssnitt av de virtuella datorerna (inkommande trafik) (inaktuell)|VMName|
|Nätverk totalt|Yes|Nätverk totalt|Byte|Totalt|Antalet byte som tas emot i alla nätverksgränssnitt av de virtuella datorerna (inkommande trafik)|VMName|
|Nätverk – utgående|Yes|Fakturerbar för nätverk (inaktuell)|Byte|Totalt|Antalet fakturerbara byte i alla nätverksgränssnitt av de virtuella datorerna (utgående trafik) (inaktuell)|VMName|
|Nätverk ut totalt|Yes|Nätverk ut totalt|Byte|Totalt|Antalet byte ut i alla nätverksgränssnitt av de virtuella datorerna (utgående trafik)|VMName|
|Förbrukad bandbredd för operativsystemdisk i procent|Yes|Förbrukad bandbredd för operativsystemdisk i procent|Procent|Genomsnitt|Procentandel förbrukad diskbandbredd för operativsystem per minut|LUN, VMName|
|Förbrukad IOPS-procentandel för OS-disk|Yes|Förbrukad IOPS-procentandel för OS-disk|Procent|Genomsnitt|Procentandel operativsystemdisk-I/O som förbrukas per minut|LUN, VMName|
|Maximal burst-bandbredd för OS-disk|Yes|Maximal burst-bandbredd för OS-disk|Antal|Genomsnitt|Maximalt antal byte per sekund som OS-disken kan uppnå med burst-flöden|LUN, VMName|
|OS-disk Max Burst IOPS|Yes|OS-disk Max Burst IOPS|Antal|Genomsnitt|Maximal iOPS OS-disk kan uppnås med bursting|LUN, VMName|
|Ködjup för OS-disk|Yes|Ködjup för OS-disk|Antal|Genomsnitt|Ködjup för OS-disk (eller kölängd)|VMName|
|Byte/sek för os-diskläsning|Yes|Byte/sek för os-diskläsning|BytePerSecond|Genomsnitt|Byte/sek-läsning från en enskild disk under övervakningsperioden för OS-disk|VMName|
|Os-diskläsningsåtgärder/s|Yes|Läsåtgärder/s för OS-disk|CountPerSecond|Genomsnitt|Läsa IOPS från en enskild disk under övervakningsperioden för OS-disk|VMName|
|Målbandbredd för OS-disk|Yes|Målbandbredd för OS-disk|Antal|Genomsnitt|Baslinjebyte per sekund– OS-disk med dataflöde kan uppnås utan bursting|LUN, VMName|
|IOPS för OS-diskmål|Yes|IOPS för OS-diskmål|Antal|Genomsnitt|Baslinje-IOPS OS-disk kan uppnå utan bursting|LUN, VMName|
|Operativsystemets disk som används burst-bit/s-krediter i procent|Yes|Operativsystemets disk som används burst-bit/s-krediter i procent|Procent|Genomsnitt|Procentandel av os-diskens burst-bandbreddskrediter som använts hittills|LUN, VMName|
|Procentandel burst-I/O-krediter på OS-disk|Yes|Procentandel burst-I/O-krediter på OS-disk|Procent|Genomsnitt|Procentandel av I/O-krediter för os-disk burst som använts hittills|LUN, VMName|
|Operativsystemets diskskrivningsbyte/sek|Yes|Byte/sek för os-diskskrivning|BytesPerSecond|Genomsnitt|Byte/sek som skrivs till en enskild disk under övervakningsperioden för OS-disk|VMName|
|Os-diskskrivningsåtgärder/sek|Yes|Os-diskskrivningsåtgärder/sek|CountPerSecond|Genomsnitt|Skriva IOPS från en enskild disk under övervakningsperioden för OS-disk|VMName|
|Utgående flöden|Yes|Utgående flöden|Antal|Genomsnitt|Utgående flöden är antalet aktuella flöden i utgående riktning (trafik som går ut från den virtuella datorn)|VMName|
|Maximal skapandefrekvens för utgående flöden|Yes|Maximal skapandefrekvens för utgående flöden|CountPerSecond|Genomsnitt|Maximal skapandefrekvens för utgående flöden (trafik som går ut från den virtuella datorn)|VMName|
|Processorprocentandel|Yes|Processorprocentandel|Procent|Genomsnitt|Procentandelen allokerade beräkningsenheter som för närvarande används av de virtuella datorerna|VMName|
|Lästräff för Premium-datadiskcache|Yes|Lästräff för Premium-datadiskcache|Procent|Genomsnitt|Lästräff för Premium-datadiskcache|LUN, VMName|
|Läsmiss för Premium-datadiskcache|Yes|Läsmiss för Premium-datadiskcache|Procent|Genomsnitt|Läsmiss för Premium-datadiskcache|LUN, VMName|
|Premium OS-diskcacheläsningsträff|Yes|Premium OS-diskcacheläsningsträff|Procent|Genomsnitt|Premium OS-diskcacheläsningsträff|VMName|
|Premium OS-diskcache – läsmiss|Yes|Premium OS-diskcache – läsmiss|Procent|Genomsnitt|Premium OS-diskcache – läsmiss|VMName|
|Förbrukad bandbredd i procent för den virtuella datorns cachelagrade bandbredd|Yes|Förbrukad bandbredd i procent för den virtuella datorns cachelagrade bandbredd|Procent|Genomsnitt|Procentandel cachelagrad diskbandbredd som förbrukas av den virtuella datorn|VMName|
|Förbrukad IOPS-procentandel för vm-cache|Yes|Förbrukad IOPS-procentandel för vm-cache|Procent|Genomsnitt|Procentandel cachelagrad disk-IOPS som förbrukas av den virtuella datorn|VMName|
|VM Uncached Bandwidth Consumed Percentage|Yes|Förbrukad bandbredd för virtuell dator i procent|Procent|Genomsnitt|Procentandel av den diskbandbredd som förbrukas av den virtuella datorn|VMName|
|Procentandel förbrukade virtuella IOPS-enheter som inte är frånkopplade|Yes|Procentandel förbrukade virtuella IOPS-enheter som inte är frånkopplade|Procent|Genomsnitt|Procentandel IOPS för obekopplad disk som förbrukas av den virtuella datorn|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Förbrukade CPU-krediter|Yes|Förbrukade CPU-krediter|Antal|Genomsnitt|Totalt antal krediter som förbrukats av den virtuella datorn. Endast tillgängligt på B-seriens burst-sbara virtuella datorer|Inga dimensioner|
|Återstående CPU-krediter|Yes|Återstående CPU-krediter|Antal|Genomsnitt|Totalt antal krediter som är tillgängliga för burst. Endast tillgängligt på B-seriens burst-sbara virtuella datorer|Inga dimensioner|
|Förbrukad datadiskbandbredd i procent|Yes|Förbrukad datadiskbandbredd i procent|Procent|Genomsnitt|Procentandel förbrukad datadiskbandbredd per minut|Lun|
|IOPS-förbrukningsprocent för datadisk|Yes|IOPS-förbrukningsprocent för datadisk|Procent|Genomsnitt|Procentandel datadisk-I/O som förbrukas per minut|Lun|
|Maximal burst-bandbredd för datadisk|Yes|Maximal burst-bandbredd för datadisk|Antal|Genomsnitt|Maximalt antal byte per sekund dataflöde Datadisk kan uppnå med bursting|Lun|
|Maximal burst-IOPS för datadisk|Yes|Maximal burst-IOPS för datadisk|Antal|Genomsnitt|Maximal IOPS-datadisk kan uppnå med bursting|Lun|
|Ködjup för datadisk|Yes|Ködjup för datadisk|Antal|Genomsnitt|Ködjup för datadisk (eller kölängd)|Lun|
|Byte/sek för datadiskläsning|Yes|Byte/sek för datadiskläsning|BytePerSecond|Genomsnitt|Byte/sek-läsning från en enskild disk under övervakningsperioden|Lun|
|Datadiskläsningsåtgärder/sek|Yes|Datadiskläsningsåtgärder/sek|CountPerSecond|Genomsnitt|Läsa IOPS från en enskild disk under övervakningsperioden|Lun|
|Målbandbredd för datadisk|Yes|Målbandbredd för datadisk|Antal|Genomsnitt|Baslinjebyte per sekund dataflöde Datadisk kan uppnå utan bursting|Lun|
|IOPS för datadiskmål|Yes|IOPS för datadiskmål|Antal|Genomsnitt|Baslinje-IOPS-datadisk kan uppnås utan burst-ning|Lun|
|Datadisk som används burst-bit/s-krediter i procent|Yes|Datadisk som används burst-bit/s-krediter i procent|Procent|Genomsnitt|Procentandel av datadiskens burst-bandbreddskrediter som använts hittills|Lun|
|Procentandel burst-I/O-krediter som används på datadisk|Yes|Procentandel burst-I/O-krediter som används på datadisk|Procent|Genomsnitt|Procentandel av datadiskens burst-I/O-krediter som använts hittills|Lun|
|Byte/sek för datadiskskrivning|Yes|Byte/sek för datadiskskrivning|BytePerSecond|Genomsnitt|Byte/sek skrivs till en enskild disk under övervakningsperioden|Lun|
|Skrivåtgärder för datadisk/s|Yes|Skrivåtgärder för datadisk/s|CountPerSecond|Genomsnitt|Skriva IOPS från en enskild disk under övervakningsperioden|Lun|
|Byte för diskläsning|Yes|Byte för diskläsning|Byte|Totalt|Byte som lästs från disk under övervakningsperioden|Inga dimensioner|
|Diskläsningsåtgärder/s|Yes|Diskläsningsåtgärder/s|CountPerSecond|Genomsnitt|Diskläsnings-IOPS|Inga dimensioner|
|Diskskrivningsbyte|Yes|Diskskrivningsbyte|Byte|Totalt|Byte som skrivs till disk under övervakningsperioden|Inga dimensioner|
|Diskskrivningsåtgärder/sek|Yes|Diskskrivningsåtgärder/sek|CountPerSecond|Genomsnitt|Diskskrivnings-IOPS|Inga dimensioner|
|Inkommande flöden|Yes|Inkommande flöden|Antal|Genomsnitt|Inkommande flöden är antalet aktuella flöden i inkommande riktning (trafik till den virtuella datorn)|Inga dimensioner|
|Maximal skapandefrekvens för inkommande flöden|Yes|Maximal skapandefrekvens för inkommande flöden|CountPerSecond|Genomsnitt|Maximal skapandefrekvens för inkommande flöden (trafik till den virtuella datorn)|Inga dimensioner|
|Nätverk – inkommande|Yes|Nätverk i fakturerbar (inaktuell)|Byte|Totalt|Antalet fakturerbara byte som tas emot i alla nätverksgränssnitt av de virtuella datorerna (inkommande trafik) (inaktuell)|Inga dimensioner|
|Nätverk totalt|Yes|Nätverk totalt|Byte|Totalt|Antalet byte som tas emot i alla nätverksgränssnitt av de virtuella datorerna (inkommande trafik)|Inga dimensioner|
|Nätverk – utgående|Yes|Fakturerbar för nätverk (inaktuell)|Byte|Totalt|Antalet fakturerbara byte i alla nätverksgränssnitt av de virtuella datorerna (utgående trafik) (inaktuell)|Inga dimensioner|
|Totalt nätverk ut|Yes|Totalt nätverk ut|Byte|Totalt|Antalet byte ut i alla nätverksgränssnitt av de virtuella datorerna (utgående trafik)|Inga dimensioner|
|Förbrukad bandbredd för operativsystemdisk i procent|Yes|Förbrukad bandbredd för operativsystemdisk i procent|Procent|Genomsnitt|Procentandel diskbandbredd för operativsystem som förbrukas per minut|Lun|
|IOPS-förbrukningsprocent för OS-disk|Yes|IOPS-förbrukningsprocent för OS-disk|Procent|Genomsnitt|Procentandel operativsystemdisk-I/O som förbrukas per minut|Lun|
|Maximal burst-bandbredd för OS-disk|Yes|Maximal burst-bandbredd för OS-disk|Antal|Genomsnitt|Maximalt antal byte per sekund som OS-disken kan uppnå med burst-flöden|Lun|
|OS-disk Max Burst IOPS|Yes|OS-disk Max Burst IOPS|Antal|Genomsnitt|Maximal iOPS OS-disk kan uppnås med bursting|Lun|
|Ködjup för OS-disk|Yes|Ködjup för OS-disk|Antal|Genomsnitt|Ködjup för OS-disk (eller kölängd)|Inga dimensioner|
|Byte/sek för os-diskläsning|Yes|Byte/sek för os-diskläsning|BytesPerSecond|Genomsnitt|Byte/sek-läsning från en enskild disk under övervakningsperioden för OS-disk|Inga dimensioner|
|Läsåtgärder/sek för OS-disk|Yes|Läsåtgärder/sek för OS-disk|CountPerSecond|Genomsnitt|Läsa IOPS från en enskild disk under övervakningsperioden för OS-disk|Inga dimensioner|
|Målbandbredd för OS-disk|Yes|Målbandbredd för OS-disk|Antal|Genomsnitt|Baslinjebyte per sekund– OS-disk med dataflöde kan uppnås utan bursting|Lun|
|IOPS för OS-diskmål|Yes|IOPS för OS-diskmål|Antal|Genomsnitt|Baslinje-IOPS OS-disk kan uppnå utan bursting|Lun|
|Os-disk som används burst-bit/s-krediter i procent|Yes|Os-disk som används burst-bit/s-krediter i procent|Procent|Genomsnitt|Procentandel av os-diskens burst-bandbreddskrediter som använts hittills|Lun|
|Procentandel burst-I/O-krediter på OS-disk|Yes|Procentandel burst-I/O-krediter på OS-disk|Procent|Genomsnitt|Procentandel av I/O-krediter för burst-fel i OS-diskar som använts hittills|Lun|
|Skrivningsbyte per sekund för OS-disk|Yes|Skrivningsbyte/sek för OS-disk|BytePerSecond|Genomsnitt|Byte/sek skrivs till en enskild disk under övervakningsperioden för OS-disken|Inga dimensioner|
|Os-diskskrivningsåtgärder/sek|Yes|Os-diskskrivningsåtgärder/sek|CountPerSecond|Genomsnitt|Skriva IOPS från en enskild disk under övervakningsperioden för OS-disk|Inga dimensioner|
|Utgående flöden|Yes|Utgående flöden|Antal|Genomsnitt|Utgående flöden är antalet aktuella flöden i utgående riktning (trafik som går ut från den virtuella datorn)|Inga dimensioner|
|Maximal skapandefrekvens för utgående flöden|Yes|Maximal skapandefrekvens för utgående flöden|CountPerSecond|Genomsnitt|Maximal skapandefrekvens för utgående flöden (trafik som går ut från den virtuella datorn)|Inga dimensioner|
|Processorprocentandel|Yes|Processorprocentandel|Procent|Genomsnitt|Procentandelen allokerade beräkningsenheter som för närvarande används av de virtuella datorerna|Inga dimensioner|
|Lästräff för Premium-datadiskcache|Yes|Lästräff för Premium-datadiskcache|Procent|Genomsnitt|Lästräff för Premium-datadiskcache|Lun|
|Läsmiss för Premium-datadiskcache|Yes|Läsmiss för Premium-datadiskcache|Procent|Genomsnitt|Läsmiss för Premium-datadiskcache|Lun|
|Premium OS-diskcacheläsningsträff|Yes|Premium OS-diskcacheläsningsträff|Procent|Genomsnitt|Premium OS-diskcacheläsningsträff|Inga dimensioner|
|Premium OS-diskcache – läsmiss|Yes|Premium OS-diskcache – läsmiss|Procent|Genomsnitt|Premium OS-diskcache – läsmiss|Inga dimensioner|
|Förbrukad bandbredd i procent för den virtuella datorns cachelagrade bandbredd|Yes|Förbrukad bandbredd i procent för den virtuella datorns cachelagrade bandbredd|Procent|Genomsnitt|Procentandel cachelagrad diskbandbredd som förbrukas av den virtuella datorn|Inga dimensioner|
|IOPS-förbrukning i procent för vm-cachelagrad|Yes|IOPS-förbrukning i procent för vm-cachelagrad|Procent|Genomsnitt|Procentandel cachelagrad disk-IOPS som förbrukas av den virtuella datorn|Inga dimensioner|
|Förbrukad bandbredd för virtuell dator i procent|Yes|Förbrukad vm-bandbredd i procent|Procent|Genomsnitt|Procentandel av den ledigt diskbandbredd som förbrukas av den virtuella datorn|Inga dimensioner|
|Procentandel förbrukade virtuella IOPS-enheter som inte är frånkopplade|Yes|Procentandel förbrukade virtuella IOPS-enheter som inte är frånkopplade|Procent|Genomsnitt|Procentandel IOPS för obekopplad disk som förbrukas av den virtuella datorn|Inga dimensioner|


## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft.ContainerInstance/containerGroups

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|CpuUsage|Yes|Processoranvändning|Antal|Genomsnitt|CPU-användning på alla kärnor i kärnor.|containerName|
|MinneAnvändning|Yes|Minnesanvändning|Byte|Genomsnitt|Total minnesanvändning i byte.|containerName|
|NetworkBytesReceivedPerSecond|Yes|Mottagna nätverksbyte per sekund|Byte|Genomsnitt|Mottagna nätverksbyte per sekund.|Inga dimensioner|
|NetworkBytesTransmittedPerSecond|Yes|Nätverksbyte som överförs per sekund|Byte|Genomsnitt|De nätverksbyte som överförs per sekund.|Inga dimensioner|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft.ContainerRegistry/registries

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|AgentPoolCPUTime|Yes|AgentPool CPU-tid|Sekunder|Totalt|AgentPool CPU-tid i sekunder|Inga dimensioner|
|RunDuration|Yes|Körningens varaktighet|Millisekunder|Totalt|Körningens varaktighet i millisekunder|Inga dimensioner|
|SuccessfulPullCount|Yes|Antal lyckade pull-ningar|Antal|Genomsnitt|Antal lyckade avbildnings-pull-fel|Inga dimensioner|
|SuccessfulPushCount|Yes|Antal lyckade push-meddelanden|Antal|Genomsnitt|Antal lyckade push-meddelanden för avbildningar|Inga dimensioner|
|TotalPullCount|Yes|Totalt antal pull-intr|Antal|Genomsnitt|Totalt antal avbildnings-pull-bilder|Inga dimensioner|
|TotalPushCount|Yes|Totalt antal push-meddelanden|Antal|Genomsnitt|Totalt antal push-meddelanden för avbildningar|Inga dimensioner|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft.ContainerService/managedClusters

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|apiserver_current_inflight_requests|No|Inflight-begäranden|Antal|Genomsnitt|Maximalt antal för närvarande använda aktiva begäranden på API-servern per typ av begäran under den senaste sekunden|requestKind|
|cluster_autoscaler_cluster_safe_to_autoscale|No|Klusterhälsa|Antal|Genomsnitt|Avgör om autoskalning av kluster ska vidta åtgärder i klustret||
|cluster_autoscaler_scale_down_in_cooldown|No|Skala ned ned nedskalning|Antal|Genomsnitt|Anger om nedskalningen är i nedstängning – Inga noder tas bort under den här tidsramen||
|cluster_autoscaler_unneeded_nodes_count|No|Noder som inte behövers|Antal|Genomsnitt|Klusterauotscaler markerar dessa noder som kandidater för borttagning och tas så småningom bort||
|cluster_autoscaler_unschedulable_pods_count|No|Oplanerade poddar|Antal|Genomsnitt|Antal poddar som för närvarande är oplanerade i klustret||
|kube_node_status_allocatable_cpu_cores|No|Totalt antal tillgängliga processorkärnor i ett hanterat kluster|Antal|Genomsnitt|Totalt antal tillgängliga processorkärnor i ett hanterat kluster||
|kube_node_status_allocatable_memory_bytes|No|Total mängd tillgängligt minne i ett hanterat kluster|Byte|Genomsnitt|Total mängd tillgängligt minne i ett hanterat kluster||
|kube_node_status_condition|No|Status för olika nodvillkor|Antal|Genomsnitt|Status för olika nodvillkor|condition, status, status2, node|
|kube_pod_status_phase|No|Antal poddar efter fas|Antal|Genomsnitt|Antal poddar efter fas|phase, namespace, pod|
|kube_pod_status_ready|No|Antal poddar med statusen Klar|Antal|Genomsnitt|Antal poddar med statusen Klar|namespace, pod, condition|
|node_cpu_usage_millicores|Yes|Cpu-användning – kärnor|Cores|Genomsnitt|Aggregerat mått på CPU-användning icores i klustret|node, nodepool|
|node_cpu_usage_percentage|Yes|Processoranvändning i procent|Procent|Genomsnitt|Aggregerad genomsnittlig CPU-användning mätt i procent i klustret|node, nodepool|
|node_disk_usage_bytes|Yes|Disk som används byte|Byte|Genomsnitt|Diskutrymme som används i byte per enhet|node, nodepool, device|
|node_disk_usage_percentage|Yes|Disk som används i procent|Procent|Genomsnitt|Diskutrymme som används i procent per enhet|node, nodepool, device|
|node_memory_rss_bytes|Yes|RSS-byte för minne|Byte|Genomsnitt|RSS-containerminne som används i byte|node, nodepool|
|node_memory_rss_percentage|Yes|RSS-procent för minne|Procent|Genomsnitt|RSS-containerminne som används i procent|node, nodepool|
|node_memory_working_set_bytes|Yes|Byte för minnesarbetsuppsättning|Byte|Genomsnitt|Arbetsminne för container som används i byte|node, nodepool|
|node_memory_working_set_percentage|Yes|Procentandel minnesarbetsuppsättning|Procent|Genomsnitt|Minne för arbetsminne för container som används i procent|node, nodepool|
|node_network_in_bytes|Yes|Nätverk i byte|Byte|Genomsnitt|Mottagna byte för nätverk|node, nodepool|
|node_network_out_bytes|Yes|Utgående byte för nätverk|Byte|Genomsnitt|Överförda byte för nätverk|node, nodepool|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft.CustomProviders/resourceproviders

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|FailedRequests|Yes|Misslyckade begäranden|Antal|Totalt|Hämtar tillgängliga loggar för anpassade resursproviders|HttpMethod, CallPath, StatusCode|
|SuccessfullRequests|Yes|Slutförda förfrågningar|Antal|Totalt|Lyckade begäranden gjorda av den anpassade providern|HttpMethod, CallPath, StatusCode|


## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft.DataBoxEdge/dataBoxEdgeDevices

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|AvailableCapacity|Yes|Tillgänglig kapacitet|Byte|Genomsnitt|Tillgänglig kapacitet i byte under rapporteringsperioden.|Inga dimensioner|
|BytesUploadedToCloud|Yes|Överförda molnbyte (enhet)|Byte|Genomsnitt|Det totala antalet byte som laddas upp till Azure från en enhet under rapporteringsperioden.|Inga dimensioner|
|ByteUploadedToCloudPerShare|Yes|Överförda molnbyte (resurs)|Byte|Genomsnitt|Det totala antalet byte som laddas upp till Azure från en resurs under rapporteringsperioden.|Dela|
|CloudReadThroughput|Yes|Dataflöde för nedladdning i molnet|BytePerSecond|Genomsnitt|Molnet laddar ned dataflöde till Azure under rapporteringsperioden.|Inga dimensioner|
|CloudReadThroughputPerShare|Yes|Dataflöde för nedladdning i molnet (resurs)|BytesPerSecond|Genomsnitt|Dataflödet för nedladdning till Azure från en resurs under rapporteringsperioden.|Dela|
|CloudUploadThroughput|Yes|Dataflöde för molnuppladdning|BytesPerSecond|Genomsnitt|Molnuppladdningens dataflöde till Azure under rapporteringsperioden.|Inga dimensioner|
|CloudUploadThroughputPerShare|Yes|Dataflöde för molnuppladdning (resurs)|BytesPerSecond|Genomsnitt|Uppladdningens dataflöde till Azure från en resurs under rapporteringsperioden.|Dela|
|HyperVMemoryUtilization|Yes|Edge Compute – minnesanvändning|Procent|Genomsnitt|Mängden RAM-minne som används|InstanceName|
|HyperVVirtualProcessorUtilization|Yes|Edge Compute – Processorprocentandel|Procent|Genomsnitt|Procent CPU-användning|InstanceName|
|NICReadThroughput|Yes|Läs dataflöde (nätverk)|BytesPerSecond|Genomsnitt|Läsgenomflödet för nätverksgränssnittet på enheten under rapporteringsperioden för alla volymer i gatewayen.|InstanceName|
|NICWriteThroughput|Yes|Skriva dataflöde (nätverk)|BytePerSecond|Genomsnitt|Skrivningsgenomflödet för nätverksgränssnittet på enheten under rapporteringsperioden för alla volymer i gatewayen.|InstanceName|
|TotalCapacity|Yes|Total kapacitet|Byte|Genomsnitt|Total kapacitet|Inga dimensioner|


## <a name="microsoftdatacollaborationworkspaces"></a>Microsoft.DataCollaboration/workspaces

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|DataAssetCount|Yes|Skapade datatillgångar|Antal|Maximal|Antal skapade datatillgångar|DataAssetName|
|PipelineCount|Yes|Skapade pipelines|Antal|Maximal|Antal skapade pipelines|PipelineName|
|Förslagskonto|Yes|Skapade förslag|Antal|Maximal|Antal skapade förslag|ProposalName|
|ScriptCount|Yes|Skapade skript|Antal|Maximal|Antal skapade skript|ScriptName|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft.DataFactory/datafactories

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|FailedRuns|Yes|Misslyckade körningar|Antal|Totalt||pipelineName, activityName|
|SuccessfulRuns|Yes|Lyckade körningar|Antal|Totalt||pipelineName, activityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft.DataFactory/factories

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ActivityCancelledRuns|Yes|Mått för att avbryta aktivitetskörningar|Antal|Totalt||ActivityType, PipelineName, FailureType, Name|
|ActivityFailedRuns|Yes|Mått för misslyckade aktivitetskörningar|Antal|Totalt||ActivityType, PipelineName, FailureType, Name|
|ActivitySucceededRuns|Yes|Mått för körning av aktiviteter som lyckades|Antal|Totalt||ActivityType, PipelineName, FailureType, Name|
|FactorySizeInGbUnits|Yes|Total fabriksstorlek (GB-enhet)|Antal|Maximal||Inga dimensioner|
|IntegrationRuntimeAvailableMemory|Yes|Tillgängligt minne för Integration Runtime|Byte|Genomsnitt||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeAvailableNodeNumber|Yes|Antal tillgängliga noder för Integration Runtime|Antal|Genomsnitt||IntegrationRuntimeName|
|IntegrationRuntimeAverageTaskPickupDelay|Yes|Integration Runtime-kövaraktighet|Sekunder|Genomsnitt||IntegrationRuntimeName|
|IntegrationRuntimeCpuPercentage|Yes|Processoranvändning för Integration Runtime|Procent|Genomsnitt||IntegrationRuntimeName, NodeName|
|IntegrationRuntimeQueueLength|Yes|Kölängd för Integration Runtime|Antal|Genomsnitt||IntegrationRuntimeName|
|MaxAllowedFactorySizeInGbUnits|Yes|Högsta tillåtna fabriksstorlek (GB-enhet)|Antal|Maximal||Inga dimensioner|
|MaxAllowedResourceCount|Yes|Maximalt antal tillåtna entiteter|Antal|Maximal||Inga dimensioner|
|PipelineCancelledRuns|Yes|Mått för avbokning av pipelinekörningar|Antal|Totalt||FailureType, Name|
|PipelineElapsedTimeRuns|Yes|Mått för körningar av pipeline för förfluten tid|Antal|Totalt||RunId, Name|
|PipelineFailedRuns|Yes|Mått för misslyckade pipelinekörningar|Antal|Totalt||FailureType, Name|
|PipelineSucceededRuns|Yes|Mått för körning av en pipeline som lyckades|Antal|Totalt||FailureType, Name|
|ResourceCount|Yes|Totalt antal entiteter|Antal|Maximal||Inga dimensioner|
|SSISIntegrationRuntimeStartCancel|Yes|Startmått för SSIS Integration Runtime har avbrutits|Antal|Totalt||IntegrationRuntimeName|
|SSISIntegrationRuntimeStartFailed|Yes|Startmått för SSIS Integration Runtime misslyckades|Antal|Totalt||IntegrationRuntimeName|
|SSISIntegrationRuntimeStartSucceeded|Yes|Startmått för SSIS Integration Runtime har lyckats|Antal|Totalt||IntegrationRuntimeName|
|SSISIntegrationRuntimeStopSttillstånd|Yes|Stoppmått för SSIS Integration Runtime har fastnat|Antal|Totalt||IntegrationRuntimeName|
|SSISIntegrationRuntimeStopSucceeded|Yes|Stopmått för SSIS Integration Runtime har lyckats|Antal|Totalt||IntegrationRuntimeName|
|SSISPackageExecutionCancel|Yes|Körningsmått för SSIS-paket avbröts|Antal|Totalt||IntegrationRuntimeName|
|SSISPackageExecutionFailed|Yes|Mått för misslyckad körning av SSIS-paket|Antal|Totalt||IntegrationRuntimeName|
|SSISPackageExecutionSucceeded|Yes|Körningsmått för SSIS-paket lyckades|Antal|Totalt||IntegrationRuntimeName|
|TriggerCancelledRuns|Yes|Mått för avbokning av utlösare körs|Antal|Totalt||Name, FailureType|
|TriggerFailedRuns|Yes|Mått för misslyckade utlösare körs|Antal|Totalt||Name, FailureType|
|TriggerSucceededRuns|Yes|Mått för körning av utlösare som lyckades|Antal|Totalt||Namn, Feltyp|


## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|JobAUEndedCancelled|Yes|Au-tid avbröts|Sekunder|Totalt|Total AU-tid för avbokningar av jobb.|Inga dimensioner|
|JobAUEndedFailure|Yes|Misslyckad AU-tid|Sekunder|Totalt|Total AU-tid för misslyckade jobb.|Inga dimensioner|
|JobAUEndedSuccess|Yes|Lyckad AU-tid|Sekunder|Totalt|Total AU-tid för lyckade jobb.|Inga dimensioner|
|JobEndedCancelled|Yes|Avbokningsjobb|Antal|Totalt|Antal av annullerade jobb.|Inga dimensioner|
|JobEndedFailure|Yes|Misslyckade jobb|Antal|Totalt|Antal misslyckade jobb.|Inga dimensioner|
|JobEndedSuccess|Yes|Lyckade jobb|Antal|Totalt|Antal lyckade jobb.|Inga dimensioner|
|JobStage|Yes|Jobb i fas|Antal|Totalt|Antal jobb i varje fas.|Inga dimensioner|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|DataRead|Yes|Läsa data|Byte|Totalt|Total mängd data som lästs från kontot.|Inga dimensioner|
|DataWritten|Yes|Skrivna data|Byte|Totalt|Total mängd data som skrivs till kontot.|Inga dimensioner|
|ReadRequests|Yes|Läsbegäranden|Antal|Totalt|Antal dataläsningsbegäranden till kontot.|Inga dimensioner|
|TotalStorage|Yes|Totalt lagringsutrymme|Byte|Maximal|Total mängd data som lagras i kontot.|Inga dimensioner|
|WriteRequests|Yes|Skrivbegäranden|Antal|Totalt|Antal dataskrivningsbegäranden till kontot.|Inga dimensioner|


## <a name="microsoftdatashareaccounts"></a>Microsoft.DataShare/accounts

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|FailedShareSubscriptionSynchronizations|Yes|Mottagna resurs misslyckade ögonblicksbilder|Antal|Antal|Antal mottagna ögonblicksbilder av resurs som misslyckats i kontot|Inga dimensioner|
|FailedShareSynchronizations|Yes|Skickade resurs misslyckade ögonblicksbilder|Antal|Antal|Antalet skickade ögonblicksbilder av resurs som misslyckades i kontot|Inga dimensioner|
|ShareCount|Yes|Skickade resurser|Antal|Maximal|Antal skickade resurser i kontot|Resursnamn|
|ShareSubscriptionCount|Yes|Mottagna resurser|Antal|Maximal|Antal mottagna resurser i kontot|ShareSubscriptionName|
|SucceededShareSubscriptionSynchronizations|Yes|Mottagna ögonblicksbilder av resursen lyckades|Antal|Antal|Antal mottagna ögonblicksbilder av resursen som har lyckats i kontot|Inga dimensioner|
|SucceededShareSynchronizations|Yes|Skickade ögonblicksbilder av resursen lyckades|Antal|Antal|Antal skickade ögonblicksbilder av resursen som har lyckats i kontot|Inga dimensioner|


## <a name="microsoftdbformariadbservers"></a>Microsoft.DBforServeraDB/servers

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|active_connections|Yes|Aktiva anslutningar|Antal|Genomsnitt|Aktiva anslutningar|Inga dimensioner|
|backup_storage_used|Yes|Lagringsenhet för säkerhetskopiering som används|Byte|Genomsnitt|Lagringsenhet för säkerhetskopiering som används|Inga dimensioner|
|connections_failed|Yes|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inga dimensioner|
|cpu_percent|Yes|CPU-procent|Procent|Genomsnitt|CPU-procent|Inga dimensioner|
|io_consumption_percent|Yes|I/O-procent|Procent|Genomsnitt|I/O-procent|Inga dimensioner|
|memory_percent|Yes|Minnesprocent|Procent|Genomsnitt|Minnesprocent|Inga dimensioner|
|network_bytes_egress|Yes|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inga dimensioner|
|network_bytes_ingress|Yes|Nätverk – inkommande|Byte|Totalt|Nätverk in över aktiva anslutningar|Inga dimensioner|
|seconds_behind_master|Yes|Replikeringsfördröjning i sekunder|Antal|Maximal|Replikeringsfördröjning i sekunder|Inga dimensioner|
|serverlog_storage_limit|Yes|Lagringsgräns för serverlogg|Byte|Maximal|Lagringsgräns för serverlogg|Inga dimensioner|
|serverlog_storage_percent|Yes|Lagringsprocent för serverlogg|Procent|Genomsnitt|Lagringsprocent för serverlogg|Inga dimensioner|
|serverlog_storage_usage|Yes|Serverlogglagring används|Byte|Genomsnitt|Serverlogglagring används|Inga dimensioner|
|storage_limit|Yes|Lagringsgräns|Byte|Maximal|Lagringsgräns|Inga dimensioner|
|storage_percent|Yes|Lagringsprocent|Procent|Genomsnitt|Lagringsprocent|Inga dimensioner|
|storage_used|Yes|Lagring som används|Byte|Genomsnitt|Lagring som används|Inga dimensioner|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft.DBforMySQL/flexibleServers

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|aborted_connections|Yes|Avbrutna anslutningar|Antal|Totalt|Avbrutna anslutningar|Inga dimensioner|
|active_connections|Yes|Aktiva anslutningar|Antal|Maximal|Aktiva anslutningar|Inga dimensioner|
|backup_storage_used|Yes|Använt lagringsutrymme för säkerhetskopiering|Byte|Maximal|Använt lagringsutrymme för säkerhetskopiering|Inga dimensioner|
|cpu_percent|Yes|Processorprocent för värd|Procent|Maximal|Processorprocent för värd|Inga dimensioner|
|io_consumption_percent|Yes|I/O-procent|Procent|Maximal|I/O-procent|Inga dimensioner|
|memory_percent|Yes|Värdminnesprocent|Procent|Maximal|Värdminnesprocent|Inga dimensioner|
|network_bytes_egress|Yes|Värdnätverk ut|Byte|Totalt|Utgående värdnätverk i byte|Inga dimensioner|
|network_bytes_ingress|Yes|Värdnätverk i|Byte|Totalt|Värdnätverksingress i byte|Inga dimensioner|
|Frågor|Yes|Frågor|Antal|Totalt|Frågor|Inga dimensioner|
|replication_lag|Yes|Replikeringsfördröjning i sekunder|Sekunder|Maximal|Replikeringsfördröjning i sekunder|Inga dimensioner|
|storage_limit|Yes|Lagringsgräns|Byte|Maximal|Lagringsgräns|Inga dimensioner|
|storage_percent|Yes|Lagringsprocent|Procent|Maximal|Lagringsprocent|Inga dimensioner|
|storage_used|Yes|Använt lagringsutrymme|Byte|Maximal|Använt lagringsutrymme|Inga dimensioner|
|total_connections|Yes|Totalt antal anslutningar|Antal|Totalt|Totalt antal anslutningar|Inga dimensioner|


## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|active_connections|Yes|Aktiva anslutningar|Antal|Genomsnitt|Aktiva anslutningar|Inga dimensioner|
|backup_storage_used|Yes|Säkerhetskopieringslagring används|Byte|Genomsnitt|Säkerhetskopieringslagring används|Inga dimensioner|
|connections_failed|Yes|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inga dimensioner|
|cpu_percent|Yes|CPU-procent|Procent|Genomsnitt|CPU-procent|Inga dimensioner|
|io_consumption_percent|Yes|I/O-procent|Procent|Genomsnitt|I/O-procent|Inga dimensioner|
|memory_percent|Yes|Minnesprocent|Procent|Genomsnitt|Minnesprocent|Inga dimensioner|
|network_bytes_egress|Yes|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inga dimensioner|
|network_bytes_ingress|Yes|Nätverk – inkommande|Byte|Totalt|Inkommande nätverk mellan aktiva anslutningar|Inga dimensioner|
|seconds_behind_master|Yes|Replikeringsfördröjning i sekunder|Antal|Maximal|Replikeringsfördröjning i sekunder|Inga dimensioner|
|serverlog_storage_limit|Yes|Lagringsgräns för serverlogg|Byte|Maximal|Lagringsgräns för serverlogg|Inga dimensioner|
|serverlog_storage_percent|Yes|Lagringsprocent för serverlogg|Procent|Genomsnitt|Lagringsprocent för serverlogg|Inga dimensioner|
|serverlog_storage_usage|Yes|Serverlogglagring används|Byte|Genomsnitt|Serverlogglagring används|Inga dimensioner|
|storage_limit|Yes|Lagringsgräns|Byte|Maximal|Lagringsgräns|Inga dimensioner|
|storage_percent|Yes|Lagringsprocent|Procent|Genomsnitt|Lagringsprocent|Inga dimensioner|
|storage_used|Yes|Lagring som används|Byte|Genomsnitt|Lagring som används|Inga dimensioner|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft.DBforPostgreSQL/flexibleServers

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|active_connections|Yes|Aktiva anslutningar|Antal|Genomsnitt|Aktiva anslutningar|Inga dimensioner|
|backup_storage_used|Yes|Lagringsenhet för säkerhetskopiering används|Byte|Genomsnitt|Lagringsenhet för säkerhetskopiering används|Inga dimensioner|
|connections_failed|Yes|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inga dimensioner|
|connections_succeeded|Yes|Anslutningar som har lyckats|Antal|Totalt|Anslutningar som har lyckats|Inga dimensioner|
|cpu_credits_consumed|Yes|Förbrukade CPU-krediter|Antal|Genomsnitt|Totalt antal krediter som förbrukats av databasservern|Inga dimensioner|
|cpu_credits_remaining|Yes|Återstående CPU-krediter|Antal|Genomsnitt|Totalt antal krediter som är tillgängliga för burst|Inga dimensioner|
|cpu_percent|Yes|CPU-procent|Procent|Genomsnitt|CPU-procent|Inga dimensioner|
|disk_queue_depth|Yes|Diskködjup|Antal|Genomsnitt|Antal utestående I/O-åtgärder till datadisken|Inga dimensioner|
|Iops|Yes|IOPS|Antal|Genomsnitt|I/O-åtgärder per sekund|Inga dimensioner|
|maximum_used_transactionIDs|Yes|Maximalt antal använda transaktions-ID:er|Antal|Genomsnitt|Maximalt antal använda transaktions-ID:er|Inga dimensioner|
|memory_percent|Yes|Minnesprocent|Procent|Genomsnitt|Minnesprocent|Inga dimensioner|
|network_bytes_egress|Yes|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inga dimensioner|
|network_bytes_ingress|Yes|Nätverk – inkommande|Byte|Totalt|Inkommande nätverk mellan aktiva anslutningar|Inga dimensioner|
|read_iops|Yes|Läsa IOPS|Antal|Genomsnitt|Antal I/O-läsåtgärder för datadiskar per sekund|Inga dimensioner|
|read_throughput|Yes|Lästa dataflödesbyte/sek|Antal|Genomsnitt|Byte som läses per sekund från datadisken under övervakningsperioden|Inga dimensioner|
|storage_free|Yes|Storage Free|Byte|Genomsnitt|Storage Free|Inga dimensioner|
|storage_percent|Yes|Lagringsprocent|Procent|Genomsnitt|Lagringsprocent|Inga dimensioner|
|storage_used|Yes|Använt lagringsutrymme|Byte|Genomsnitt|Använt lagringsutrymme|Inga dimensioner|
|txlogs_storage_used|Yes|Lagring av transaktionslogg används|Byte|Genomsnitt|Lagring av transaktionslogg används|Inga dimensioner|
|write_iops|Yes|Skriva IOPS|Antal|Genomsnitt|Antal I/O-skrivåtgärder för datadiskar per sekund|Inga dimensioner|
|write_throughput|Yes|Skrivning av dataflödesbyte/s|Antal|Genomsnitt|Byte som skrivs per sekund till datadisken under övervakningsperioden|Inga dimensioner|


## <a name="microsoftdbforpostgresqlservergroupsv2"></a>Microsoft.DBForPostgreSQL/serverGroupsv2

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|active_connections|Yes|Aktiva anslutningar|Antal|Genomsnitt|Aktiva anslutningar|ServerName|
|cpu_percent|Yes|CPU-procent|Procent|Genomsnitt|CPU-procent|ServerName|
|Iops|Yes|IOPS|Antal|Genomsnitt|I/S-åtgärder per sekund|ServerName|
|memory_percent|Yes|Minnesprocent|Procent|Genomsnitt|Minnesprocent|ServerName|
|network_bytes_egress|Yes|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|ServerName|
|network_bytes_ingress|Yes|Nätverk – inkommande|Byte|Totalt|Nätverk in över aktiva anslutningar|ServerName|
|storage_percent|Yes|Lagringsprocent|Procent|Genomsnitt|Lagringsprocent|ServerName|
|storage_used|Yes|Använt lagringsutrymme|Byte|Genomsnitt|Använt lagringsutrymme|ServerName|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|active_connections|Yes|Aktiva anslutningar|Antal|Genomsnitt|Aktiva anslutningar|Inga dimensioner|
|backup_storage_used|Yes|Använt lagringsutrymme för säkerhetskopiering|Byte|Genomsnitt|Använt lagringsutrymme för säkerhetskopiering|Inga dimensioner|
|connections_failed|Yes|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inga dimensioner|
|cpu_percent|Yes|CPU-procent|Procent|Genomsnitt|CPU-procent|Inga dimensioner|
|io_consumption_percent|Yes|I/O-procent|Procent|Genomsnitt|I/O-procent|Inga dimensioner|
|memory_percent|Yes|Minnesprocent|Procent|Genomsnitt|Minnesprocent|Inga dimensioner|
|network_bytes_egress|Yes|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inga dimensioner|
|network_bytes_ingress|Yes|Nätverk – inkommande|Byte|Totalt|Inkommande nätverk mellan aktiva anslutningar|Inga dimensioner|
|pg_replica_log_delay_in_bytes|Yes|Maximal fördröjning mellan repliker|Byte|Maximal|Fördröjning i byte för den replik som ligger efter|Inga dimensioner|
|pg_replica_log_delay_in_seconds|Yes|Replikfördröjning|Sekunder|Maximal|Replikfördröjning i sekunder|Inga dimensioner|
|serverlog_storage_limit|Yes|Lagringsgräns för serverlogg|Byte|Maximal|Lagringsgräns för serverlogg|Inga dimensioner|
|serverlog_storage_percent|Yes|Lagringsprocent för serverlogg|Procent|Genomsnitt|Lagringsprocent för serverlogg|Inga dimensioner|
|serverlog_storage_usage|Yes|Serverlogglagring används|Byte|Genomsnitt|Serverlogglagring används|Inga dimensioner|
|storage_limit|Yes|Lagringsgräns|Byte|Maximal|Lagringsgräns|Inga dimensioner|
|storage_percent|Yes|Lagringsprocent|Procent|Genomsnitt|Lagringsprocent|Inga dimensioner|
|storage_used|Yes|Lagring som används|Byte|Genomsnitt|Lagring som används|Inga dimensioner|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft.DBforPostgreSQL/serversv2

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|active_connections|Yes|Aktiva anslutningar|Antal|Genomsnitt|Aktiva anslutningar|Inga dimensioner|
|cpu_percent|Yes|CPU-procent|Procent|Genomsnitt|CPU-procent|Inga dimensioner|
|Iops|Yes|IOPS|Antal|Genomsnitt|I/O-åtgärder per sekund|Inga dimensioner|
|memory_percent|Yes|Minnesprocent|Procent|Genomsnitt|Minnesprocent|Inga dimensioner|
|network_bytes_egress|Yes|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inga dimensioner|
|network_bytes_ingress|Yes|Nätverk – inkommande|Byte|Totalt|Nätverk in över aktiva anslutningar|Inga dimensioner|
|storage_percent|Yes|Lagringsprocent|Procent|Genomsnitt|Lagringsprocent|Inga dimensioner|
|storage_used|Yes|Använt lagringsutrymme|Byte|Genomsnitt|Använt lagringsutrymme|Inga dimensioner|


## <a name="microsoftdeviceselasticpools"></a>Microsoft.Devices/ElasticPools

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|elasticPool.requestedUsageRate|Yes|begärd användningshastighet|Procent|Genomsnitt|begärd användningshastighet|Inga dimensioner|


## <a name="microsoftdeviceselasticpoolsiothubtenants"></a>Microsoft.Devices/ElasticPools/IotHubTenants

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|c2d.commands.egress.abandon.success|Yes|C2D-meddelanden övergivna|Antal|Totalt|Antal meddelanden från moln till enhet som lämnats av enheten|Inga dimensioner|
|c2d.commands.egress.complete.success|Yes|C2D-meddelandeleveranser slutförda|Antal|Totalt|Antalet leveranser med moln-till-enhet-meddelanden som har slutförts av enheten|Inga dimensioner|
|c2d.commands.egress.reject.success|Yes|C2D-meddelanden avvisas|Antal|Totalt|Antal meddelanden från moln till enhet som avvisats av enheten|Inga dimensioner|
|c2d.methods.failure|Yes|Misslyckade anrop av direktmetod|Antal|Totalt|Antalet misslyckade direktmetods-anrop.|Inga dimensioner|
|c2d.methods.requestSize|Yes|Begärandestorlek för direkta metodanrop|Byte|Genomsnitt|Medelvärde, min och max för alla lyckade direkta metodbegäranden.|Inga dimensioner|
|c2d.methods.responseSize|Yes|Svarsstorlek för direkta metodanrop|Byte|Genomsnitt|Medelvärde, min och max för alla lyckade direkta metodsvar.|Inga dimensioner|
|c2d.methods.success|Yes|Lyckade anrop av direktmetod|Antal|Totalt|Antalet lyckade direkta metod-anrop.|Inga dimensioner|
|c2d.twin.read.failure|Yes|Misslyckade tvillingläsningar från backend|Antal|Totalt|Antalet misslyckade läsningar av backend-initierade tvillingar.|Inga dimensioner|
|c2d.twin.read.size|Yes|Svarsstorlek för tvillingläsningar från backend|Byte|Genomsnitt|Medelvärde, min och max för alla lyckade läsningar som initierats av en tvilling på en backend-initierad.|Inga dimensioner|
|c2d.twin.read.success|Yes|Lyckade tvillingläsningar från backend|Antal|Totalt|Antalet lyckade läsningar på en backend-initierad tvilling.|Inga dimensioner|
|c2d.twin.update.failure|Yes|Misslyckade tvillinguppdateringar från backend|Antal|Totalt|Antalet misslyckade uppdateringar av en backend-initierad tvilling.|Inga dimensioner|
|c2d.twin.update.size|Yes|Storlek på tvillinguppdateringar från backend|Byte|Genomsnitt|Den genomsnittliga, minsta och högsta storleken för alla lyckade uppdateringar som initierats av en tvilling på en backend-väg.|Inga dimensioner|
|c2d.twin.update.success|Yes|Lyckade tvillinguppdateringar från backend|Antal|Totalt|Antalet lyckade uppdateringar av en backend-initierad tvilling.|Inga dimensioner|
|C2DMessagesExpired|Yes|C2D-meddelanden har upphört att gälla|Antal|Totalt|Antal meddelanden från molnet till enheten som har upphört att gälla|Inga dimensioner|
|Konfigurationer|Yes|Konfigurationsmått|Antal|Totalt|Mått för konfigurationsåtgärder|Inga dimensioner|
|connectedDeviceCount|Yes|Anslutna enheter|Antal|Genomsnitt|Antal enheter som är anslutna till din IoT-hubb|Inga dimensioner|
|d2c.endpoints.egress.builtIn.events|Yes|Routning: meddelanden som levereras till meddelanden/händelser|Antal|Totalt|Antalet gånger som IoT Hub har levererat meddelanden till den inbyggda slutpunkten (meddelanden/händelser).|Inga dimensioner|
|d2c.endpoints.egress.eventHubs|Yes|Routning: meddelanden som levereras till händelsehubben|Antal|Totalt|Antalet gånger som IoT Hub har levererat meddelanden till Event Hub-slutpunkter.|Inga dimensioner|
|d2c.endpoints.egress.serviceBusQueues|Yes|Routning: meddelanden som levereras Service Bus kö|Antal|Totalt|Antalet gånger som IoT Hub har levererat meddelanden till Service Bus köslutpunkter.|Inga dimensioner|
|d2c.endpoints.egress.serviceBusTopics|Yes|Routning: meddelanden som levereras Service Bus ämne|Antal|Totalt|Antalet gånger som IoT Hub har levererat meddelanden till Service Bus ämnesslutpunkter.|Inga dimensioner|
|d2c.endpoints.egress.storage|Yes|Routning: meddelanden som levereras till lagring|Antal|Totalt|Antalet gånger som IoT Hub har levererat meddelanden till lagringsslutpunkter.|Inga dimensioner|
|d2c.endpoints.egress.storage.blobs|Yes|Routning: blobar som levereras till lagring|Antal|Totalt|Antalet gånger som IoT Hub levererade blobar till lagringsslutpunkter.|Inga dimensioner|
|d2c.endpoints.egress.storage.bytes|Yes|Routning: data som levereras till lagring|Byte|Totalt|Mängden data (byte) som IoT Hub till lagringsslutpunkter.|Inga dimensioner|
|d2c.endpoints.latency.builtIn.events|Yes|Routning: meddelandesvarstid för meddelanden/händelser|Millisekunder|Genomsnitt|Den genomsnittliga svarstiden (millisekunder) mellan meddelandeingressen till IoT Hub och telemetrimeddelandeingressen till den inbyggda slutpunkten (meddelanden/händelser).|Inga dimensioner|
|d2c.endpoints.latency.eventHubs|Yes|Routning: meddelandesvarstid för händelsehubb|Millisekunder|Genomsnitt|Den genomsnittliga svarstiden (millisekunder) mellan meddelandeingressen till IoT Hub och meddelandeingressen till en Event Hub-slutpunkt.|Inga dimensioner|
|d2c.endpoints.latency.serviceBusQueues|Yes|Routning: meddelandesvarstid för Service Bus kö|Millisekunder|Genomsnitt|Den genomsnittliga svarstiden (millisekunder) mellan meddelandeingressen till IoT Hub och telemetrimeddelandeingressen till en Service Bus-köslutpunkt.|Inga dimensioner|
|d2c.endpoints.latency.serviceBusTopics|Yes|Routning: meddelandesvarstid för Service Bus ämne|Millisekunder|Genomsnitt|Den genomsnittliga svarstiden (millisekunder) mellan meddelandeingressen till IoT Hub och telemetrimeddelandeingressen till en Service Bus ämnesslutpunkt.|Inga dimensioner|
|d2c.endpoints.latency.storage|Yes|Routning: meddelandesvarstid för lagring|Millisekunder|Genomsnitt|Den genomsnittliga svarstiden (millisekunder) mellan meddelandeingressen till IoT Hub och telemetrimeddelandeingressen till en lagringsslutpunkt.|Inga dimensioner|
|d2c.telemetry.egress.dropped|Yes|Routning: telemetrimeddelanden har släppts |Antal|Totalt|Antalet gånger som meddelanden togs bort av en IoT Hub routning på grund av döda slutpunkter. Det här värdet räknar inte meddelanden som levereras till reservvägen eftersom ignorerade meddelanden inte levereras där.|Inga dimensioner|
|d2c.telemetry.egress.fallback|Yes|Routning: meddelanden som levereras till återställning|Antal|Totalt|Antalet gånger som IoT Hub skickade meddelanden till slutpunkten som är associerad med reservvägen.|Inga dimensioner|
|d2c.telemetry.egress.invalid|Yes|Routning: inkompatibla telemetrimeddelanden|Antal|Totalt|Antalet gånger som IoT Hub inte kunde leverera meddelanden på grund av en inkompatibilitet med slutpunkten. Det här värdet inkluderar inte återförsök.|Inga dimensioner|
|d2c.telemetry.egress.orphaned|Yes|Routning: telemetrimeddelanden överblivna |Antal|Totalt|Antalet gånger som meddelanden överblivs av IoT Hub routning eftersom de inte matchade några routningsregler (inklusive återställningsregeln). |Inga dimensioner|
|d2c.telemetry.egress.success|Yes|Routning: telemetrimeddelanden levereras|Antal|Totalt|Antalet gånger som meddelanden har levererats till alla slutpunkter med hjälp av IoT Hub routning. Om ett meddelande dirigeras till flera slutpunkter ökar det här värdet med en för varje lyckad leverans. Om ett meddelande levereras till samma slutpunkt flera gånger, ökar det här värdet med ett för varje lyckad leverans.|Inga dimensioner|
|d2c.telemetry.ingress.allProtocol|Yes|Skicka försök med telemetrimeddelanden|Antal|Totalt|Antal telemetrimeddelanden från enhet till moln som försökte skickas till din IoT-hubb|Inga dimensioner|
|d2c.telemetry.ingress.sendThrottle|Yes|Antal begränsningsfel|Antal|Totalt|Antal begränsningsfel på grund av begränsningar i enhetens dataflöde|Inga dimensioner|
|d2c.telemetry.ingress.success|Yes|Skickade telemetrimeddelanden|Antal|Totalt|Antal telemetrimeddelanden från enhet till moln som skickats till din IoT-hubb|Inga dimensioner|
|d2c.twin.read.failure|Yes|Misslyckade tvillingläsningar från enheter|Antal|Totalt|Antalet misslyckade enhetsinitierade tvillingläsningar.|Inga dimensioner|
|d2c.twin.read.size|Yes|Svarsstorlek för tvillingläsningar från enheter|Byte|Genomsnitt|Medelvärde, min och max för alla lyckade enhetsinitierade tvillingläsningar.|Inga dimensioner|
|d2c.twin.read.success|Yes|Lyckade tvillingläsningar från enheter|Antal|Totalt|Antalet lyckade enhetsinitierade tvillingläsningar.|Inga dimensioner|
|d2c.twin.update.failure|Yes|Misslyckade tvillinguppdateringar från enheter|Antal|Totalt|Antalet misslyckade enhetsinitierade tvillinguppdateringar.|Inga dimensioner|
|d2c.twin.update.size|Yes|Storlek på tvillinguppdateringar från enheter|Byte|Genomsnitt|Den genomsnittliga, minsta och högsta storleken för alla lyckade enhetsinitierade tvillinguppdateringar.|Inga dimensioner|
|d2c.twin.update.success|Yes|Lyckade tvillinguppdateringar från enheter|Antal|Totalt|Antalet lyckade enhetsinitierade tvillinguppdateringar.|Inga dimensioner|
|dailyMessageQuotaUsed|Yes|Totalt antal använda meddelanden|Antal|Maximal|Antal totalt antal meddelanden som används i dag|Inga dimensioner|
|deviceDataUsage|Yes|Total enhetsdataanvändning|Byte|Totalt|Byte som överförs till och från alla enheter som är anslutna till IotHub|Inga dimensioner|
|deviceDataUsageV2|Yes|Total användning av enhetsdata (förhandsversion)|Byte|Totalt|Byte som överförs till och från alla enheter som är anslutna till IotHub|Inga dimensioner|
|devices.connectedDevices.allProtocol|Yes|Anslutna enheter (inaktuella) |Antal|Totalt|Antal enheter som är anslutna till din IoT-hubb|Inga dimensioner|
|devices.totalDevices|Yes|Totalt antal enheter (inaktuella)|Antal|Totalt|Antal enheter som är registrerade i din IoT-hubb|Inga dimensioner|
|EventGridDeliveries|Yes|Event Grid leveranser|Antal|Totalt|Antalet händelser IoT Hub publicerats för Event Grid. Använd dimensionen Resultat för antalet lyckade och misslyckade begäranden. EventType-dimensionen visar händelsetypen ( https://aka.ms/ioteventgrid) .|Result, EventType|
|EventGridLatency|Yes|Event Grid svarstid|Millisekunder|Genomsnitt|Den genomsnittliga svarstiden (millisekunder) från när Iot Hub-händelsen genererades till när händelsen publicerades till Event Grid. Det här talet är ett medelvärde mellan alla händelsetyper. Använd dimensionen EventType för att se svarstiden för en viss typ av händelse.|Eventtype|
|jobs.cancelJob.failure|Yes|Misslyckad jobbavsulleringar|Antal|Totalt|Antalet misslyckade anrop för att avbryta ett jobb.|Inga dimensioner|
|jobs.cancelJob.success|Yes|Lyckade jobb som avbryts|Antal|Totalt|Antalet lyckade anrop för att avbryta ett jobb.|Inga dimensioner|
|jobs.completed|Yes|Slutförda jobb|Antal|Totalt|Antalet slutförda jobb.|Inga dimensioner|
|jobs.createDirectMethodJob.failure|Yes|Misslyckade genereringer av metodanropsjobb|Antal|Totalt|Antalet misslyckade skapande av direkta metodanropsjobb.|Inga dimensioner|
|jobs.createDirectMethodJob.success|Yes|Lyckade skapande av metodanropsjobb|Antal|Totalt|Antalet lyckade skapande av direkta metodanropsjobb.|Inga dimensioner|
|jobs.createTwinUpdateJob.failure|Yes|Det gick inte att skapa tvillinguppdateringsjobb|Antal|Totalt|Antalet misslyckade jobb för att skapa tvillinguppdateringsjobb.|Inga dimensioner|
|jobs.createTwinUpdateJob.success|Yes|Lyckade skapanden av tvillinguppdateringsjobb|Antal|Totalt|Antalet lyckade skapande av tvillinguppdateringsjobb.|Inga dimensioner|
|jobs.failed|Yes|Misslyckade jobb|Antal|Totalt|Antalet misslyckade jobb.|Inga dimensioner|
|jobs.listJobs.failure|Yes|Misslyckade anrop för att lista jobb|Antal|Totalt|Antalet misslyckade anrop för att lista jobb.|Inga dimensioner|
|jobs.listJobs.success|Yes|Lyckade anrop för att lista jobb|Antal|Totalt|Antalet lyckade anrop för att lista jobb.|Inga dimensioner|
|jobs.queryJobs.failure|Yes|Misslyckade jobbfrågor|Antal|Totalt|Antalet misslyckade anrop till frågejobb.|Inga dimensioner|
|jobs.queryJobs.success|Yes|Lyckade jobbfrågor|Antal|Totalt|Antalet lyckade anrop till frågejobb.|Inga dimensioner|
|RoutingDataSizeInBytesDelivered|Yes|Storlek på routningsleveransmeddelande i byte (förhandsversion)|Byte|Totalt|Den totala storleken i byte av meddelanden som levereras av IoT Hub till en slutpunkt. Du kan använda dimensionerna EndpointName och EndpointType för att visa storleken på meddelandena i byte som levereras till dina olika slutpunkter. Måttvärdet ökar för varje meddelande som levereras, inklusive om meddelandet levereras till flera slutpunkter eller om meddelandet levereras till samma slutpunkt flera gånger.|EndpointType, EndpointName, RoutingSource|
|RoutingDeliveries|Yes|Routningsleveranser (förhandsversion)|Antal|Totalt|Antalet gånger som IoT Hub försökt leverera meddelanden till alla slutpunkter med routning. Om du vill se antalet lyckade eller misslyckade försök använder du dimensionen Resultat. Om du vill se orsaken till felet, t.ex. ogiltig, bortkopplad eller överbliblimt, använder du dimensionen FailureReasonCategory. Du kan också använda dimensionerna EndpointName och EndpointType för att förstå hur många meddelanden som levererades till dina olika slutpunkter. Måttvärdet ökar med ett för varje leveransförsök, inklusive om meddelandet levereras till flera slutpunkter eller om meddelandet levereras till samma slutpunkt flera gånger.|EndpointType, EndpointName, FailureReasonCategory, Result, RoutingSource|
|RoutingDeliveryLatency|Yes|Svarstid för routningsleverans (förhandsversion)|Millisekunder|Genomsnitt|Den genomsnittliga svarstiden (millisekunder) mellan meddelandeingressen till IoT Hub och telemetrimeddelandeingressen till en slutpunkt. Du kan använda dimensionerna EndpointName och EndpointType för att förstå svarstiden för dina olika slutpunkter.|EndpointType, EndpointName, RoutingSource|
|tenantHub.requestedUsageRate|Yes|begärd användningshastighet|Procent|Genomsnitt|begärd användningshastighet|Inga dimensioner|
|totalDeviceCount|Yes|Totalt antal enheter|Antal|Genomsnitt|Antal enheter som är registrerade i din IoT-hubb|Inga dimensioner|
|twinQueries.failure|Yes|Misslyckade tvillingfrågor|Antal|Totalt|Antalet misslyckade tvillingfrågor.|Inga dimensioner|
|twinQueries.resultSize|Yes|Resultatstorlek för tvillingfrågor|Byte|Genomsnitt|Medelvärde, min och max för resultatstorleken för alla lyckade tvillingfrågor.|Inga dimensioner|
|twinQueries.success|Yes|Lyckade tvillingfrågor|Antal|Totalt|Antalet lyckade tvillingfrågor.|Inga dimensioner|


## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|c2d.commands.egress.abandon.success|Yes|C2D-meddelanden övergivna|Antal|Totalt|Antal moln-till-enhet-meddelanden som har lämnats av enheten|Inga dimensioner|
|c2d.commands.egress.complete.success|Yes|C2D-meddelandeleveranser slutförda|Antal|Totalt|Antalet leveranser av moln-till-enhet-meddelanden som har slutförts av enheten|Inga dimensioner|
|c2d.commands.egress.reject.success|Yes|C2D-meddelanden avvisades|Antal|Totalt|Antal meddelanden från moln till enhet som avvisats av enheten|Inga dimensioner|
|c2d.methods.failure|Yes|Misslyckade anrop av direktmetod|Antal|Totalt|Antalet misslyckade direkta metod-anrop.|Inga dimensioner|
|c2d.methods.requestSize|Yes|Begärandestorleken för direkta metodanrop|Byte|Genomsnitt|Medelvärde, min och max för alla lyckade direkta metodbegäranden.|Inga dimensioner|
|c2d.methods.responseSize|Yes|Svarsstorlek för direkta metodanrop|Byte|Genomsnitt|Medelvärde, min och max för alla lyckade direkta metodsvar.|Inga dimensioner|
|c2d.methods.success|Yes|Lyckade anrop av direktmetod|Antal|Totalt|Antalet lyckade direkta metod-anrop.|Inga dimensioner|
|c2d.twin.read.failure|Yes|Misslyckade tvillingläsningar från backend|Antal|Totalt|Antalet misslyckade läsningar av backend-initierade tvillingar.|Inga dimensioner|
|c2d.twin.read.size|Yes|Svarsstorlek för tvillingläsningar från backend|Byte|Genomsnitt|Medelvärde, min och max för alla lyckade läsningar som initierats av en tvilling på en backend-initierad.|Inga dimensioner|
|c2d.twin.read.success|Yes|Lyckade tvillingläsningar från backend|Antal|Totalt|Antalet lyckade tvillingläsningar som initierats av backend-|Inga dimensioner|
|c2d.twin.update.failure|Yes|Misslyckade tvillinguppdateringar från backend|Antal|Totalt|Antalet misslyckade uppdateringar av en backend-initierad tvilling.|Inga dimensioner|
|c2d.twin.update.size|Yes|Storlek på tvillinguppdateringar från backend|Byte|Genomsnitt|Den genomsnittliga, minsta och högsta storleken för alla lyckade uppdateringar av en backend-initierad tvilling.|Inga dimensioner|
|c2d.twin.update.success|Yes|Lyckade tvillinguppdateringar från backend|Antal|Totalt|Antalet lyckade uppdateringar av en backend-initierad tvilling.|Inga dimensioner|
|C2DMessagesExpired|Yes|C2D-meddelanden har upphört att gälla|Antal|Totalt|Antal moln-till-enhet-meddelanden som har upphört att gälla|Inga dimensioner|
|Konfigurationer|Yes|Konfigurationsmått|Antal|Totalt|Mått för konfigurationsåtgärder|Inga dimensioner|
|connectedDeviceCount|No|Anslutna enheter|Antal|Genomsnitt|Antal enheter som är anslutna till din IoT-hubb|Inga dimensioner|
|d2c.endpoints.egress.builtIn.events|Yes|Routning: meddelanden som levereras till meddelanden/händelser|Antal|Totalt|Antalet gånger som IoT Hub har levererat meddelanden till den inbyggda slutpunkten (meddelanden/händelser).|Inga dimensioner|
|d2c.endpoints.egress.eventHubs|Yes|Routning: meddelanden som levereras till händelsehubben|Antal|Totalt|Antalet gånger som IoT Hub har levererat meddelanden till Event Hub-slutpunkter.|Inga dimensioner|
|d2c.endpoints.egress.serviceBusQueues|Yes|Routning: meddelanden som levereras Service Bus kö|Antal|Totalt|Antalet gånger som IoT Hub har levererat meddelanden till Service Bus köslutpunkter.|Inga dimensioner|
|d2c.endpoints.egress.serviceBusTopics|Yes|Routning: meddelanden som levereras Service Bus ämne|Antal|Totalt|Antalet gånger som IoT Hub har levererat meddelanden till Service Bus ämnesslutpunkter.|Inga dimensioner|
|d2c.endpoints.egress.storage|Yes|Routning: meddelanden som levereras till lagring|Antal|Totalt|Det antal gånger som IoT Hub har levererat meddelanden till lagringsslutpunkter.|Inga dimensioner|
|d2c.endpoints.egress.storage.blobs|Yes|Routning: blobar som levereras till lagring|Antal|Totalt|Antalet gånger som blobar IoT Hub dirigeras till lagringsslutpunkter.|Inga dimensioner|
|d2c.endpoints.egress.storage.bytes|Yes|Routning: data som levereras till lagring|Byte|Totalt|Mängden data (byte) som IoT Hub till lagringsslutpunkter.|Inga dimensioner|
|d2c.endpoints.latency.builtIn.events|Yes|Routning: meddelandesvarstid för meddelanden/händelser|Millisekunder|Genomsnitt|Den genomsnittliga svarstiden (millisekunder) mellan inkommande meddelanden till IoT Hub och inkommande telemetrimeddelanden till den inbyggda slutpunkten (meddelanden/händelser).|Inga dimensioner|
|d2c.endpoints.latency.eventHubs|Yes|Routning: meddelandesvarstid för händelsehubb|Millisekunder|Genomsnitt|Den genomsnittliga svarstiden (millisekunder) mellan meddelandeingressen till IoT Hub och meddelandeingressen till en Event Hub-slutpunkt.|Inga dimensioner|
|d2c.endpoints.latency.serviceBusQueues|Yes|Routning: meddelandesvarstid för Service Bus kö|Millisekunder|Genomsnitt|Den genomsnittliga svarstiden (millisekunder) mellan meddelandeingressen till IoT Hub och telemetrimeddelandets ingress till en Service Bus köslutpunkt.|Inga dimensioner|
|d2c.endpoints.latency.serviceBusTopics|Yes|Routning: meddelandesvarstid för Service Bus ämne|Millisekunder|Genomsnitt|Den genomsnittliga svarstiden (millisekunder) mellan meddelandeingressen till IoT Hub och telemetrimeddelandets ingress till en Service Bus ämnesslutpunkt.|Inga dimensioner|
|d2c.endpoints.latency.storage|Yes|Routning: meddelandesvarstid för lagring|Millisekunder|Genomsnitt|Den genomsnittliga svarstiden (millisekunder) mellan meddelandeingressen till IoT Hub och telemetrimeddelandeingressen till en lagringsslutpunkt.|Inga dimensioner|
|d2c.telemetry.egress.dropped|Yes|Routning: telemetrimeddelanden har släppts |Antal|Totalt|Antalet gånger som meddelanden togs bort av en IoT Hub på grund av döda slutpunkter. Det här värdet räknar inte meddelanden som levereras till återställningsvägen eftersom ignorerade meddelanden inte levereras där.|Inga dimensioner|
|d2c.telemetry.egress.fallback|Yes|Routning: meddelanden som levereras till återställning|Antal|Totalt|Antalet gånger som IoT Hub skickade meddelanden till slutpunkten som är associerad med reservvägen.|Inga dimensioner|
|d2c.telemetry.egress.invalid|Yes|Routning: Inkompatibla telemetrimeddelanden|Antal|Totalt|Antalet gånger som routningen IoT Hub kunde inte leverera meddelanden på grund av en inkompatibilitet med slutpunkten. Det här värdet inkluderar inte återförsök.|Inga dimensioner|
|d2c.telemetry.egress.orphaned|Yes|Routning: överblivna telemetrimeddelanden |Antal|Totalt|Antalet gånger som meddelanden överblivnas av IoT Hub routning eftersom de inte matchade några routningsregler (inklusive återställningsregeln). |Inga dimensioner|
|d2c.telemetry.egress.success|Yes|Routning: telemetrimeddelanden levereras|Antal|Totalt|Antalet gånger som meddelanden har levererats till alla slutpunkter med hjälp av IoT Hub routning. Om ett meddelande dirigeras till flera slutpunkter ökar det här värdet med en för varje lyckad leverans. Om ett meddelande levereras till samma slutpunkt flera gånger ökar det här värdet med ett för varje lyckad leverans.|Inga dimensioner|
|d2c.telemetry.ingress.allProtocol|Yes|Försök att skicka telemetrimeddelanden|Antal|Totalt|Antalet telemetrimeddelanden från enhet till moln som försökte skickas till din IoT-hubb|Inga dimensioner|
|d2c.telemetry.ingress.sendThrottle|Yes|Antal begränsningsfel|Antal|Totalt|Antal begränsningsfel på grund av begränsningar i enhetens dataflöde|Inga dimensioner|
|d2c.telemetry.ingress.success|Yes|Skickade telemetrimeddelanden|Antal|Totalt|Antal telemetrimeddelanden från enhet till moln som skickats till din IoT-hubb|Inga dimensioner|
|d2c.twin.read.failure|Yes|Misslyckade tvillingläsningar från enheter|Antal|Totalt|Antalet misslyckade enhetsinitierade tvillingläsningar.|Inga dimensioner|
|d2c.twin.read.size|Yes|Svarsstorlek för tvillingläsningar från enheter|Byte|Genomsnitt|Medelvärde, min och max för alla lyckade enhetsinitierade tvillingläsningar.|Inga dimensioner|
|d2c.twin.read.success|Yes|Lyckade tvillingläsningar från enheter|Antal|Totalt|Antalet lyckade enhetsinitierade tvillingläsningar.|Inga dimensioner|
|d2c.twin.update.failure|Yes|Misslyckade tvillinguppdateringar från enheter|Antal|Totalt|Antalet misslyckade enhetsinitierade tvillinguppdateringar.|Inga dimensioner|
|d2c.twin.update.size|Yes|Storlek på tvillinguppdateringar från enheter|Byte|Genomsnitt|Genomsnittlig, minsta och högsta storlek för alla lyckade enhetsinitierade tvillinguppdateringar.|Inga dimensioner|
|d2c.twin.update.success|Yes|Lyckade tvillinguppdateringar från enheter|Antal|Totalt|Antalet lyckade enhetsinitierade tvillinguppdateringar.|Inga dimensioner|
|dailyMessageQuotaUsed|Yes|Totalt antal använda meddelanden|Antal|Maximal|Antal totalt antal meddelanden som används idag|Inga dimensioner|
|deviceDataUsage|Yes|Total användning av enhetsdata|Byte|Totalt|Byte som överförs till och från alla enheter som är anslutna till IotHub|Inga dimensioner|
|deviceDataUsageV2|Yes|Total användning av enhetsdata (förhandsversion)|Byte|Totalt|Byte som överförs till och från alla enheter som är anslutna till IotHub|Inga dimensioner|
|devices.connectedDevices.allProtocol|Yes|Anslutna enheter (inaktuella) |Antal|Totalt|Antal enheter som är anslutna till din IoT-hubb|Inga dimensioner|
|devices.totalDevices|Yes|Totalt antal enheter (inaktuella)|Antal|Totalt|Antal enheter som är registrerade i din IoT-hubb|Inga dimensioner|
|EventGridDeliveries|Yes|Event Grid leveranser|Antal|Totalt|Antalet händelser som IoT Hub publicerats till Event Grid. Använd resultatdimensionen för antalet lyckade och misslyckade begäranden. EventType-dimensionen visar typen av händelse ( https://aka.ms/ioteventgrid) .|Result, EventType|
|EventGridLatency|Yes|Event Grid svarstid|Millisekunder|Genomsnitt|Den genomsnittliga svarstiden (millisekunder) från när Iot Hub-händelsen genererades till när händelsen publicerades till Event Grid. Det här talet är ett medelvärde mellan alla händelsetyper. Använd Dimensionen EventType för att se svarstiden för en viss typ av händelse.|Eventtype|
|jobs.cancelJob.failure|Yes|Misslyckad jobbavsulleringar|Antal|Totalt|Antalet misslyckade anrop för att avbryta ett jobb.|Inga dimensioner|
|jobs.cancelJob.success|Yes|Lyckade jobb som avbryts|Antal|Totalt|Antalet lyckade anrop för att avbryta ett jobb.|Inga dimensioner|
|jobs.completed|Yes|Slutförda jobb|Antal|Totalt|Antalet slutförda jobb.|Inga dimensioner|
|jobs.createDirectMethodJob.failure|Yes|Misslyckade genereringer av metodanropsjobb|Antal|Totalt|Antalet misslyckade genereringen av direkta metodanropsjobb.|Inga dimensioner|
|jobs.createDirectMethodJob.success|Yes|Lyckade genereringer av metodanropsjobb|Antal|Totalt|Antalet lyckade genereringen av direkta metodanropsjobb.|Inga dimensioner|
|jobs.createTwinUpdateJob.failure|Yes|Det gick inte att skapa tvillinguppdateringsjobb|Antal|Totalt|Antalet misslyckade skapande av tvillinguppdateringsjobb.|Inga dimensioner|
|jobs.createTwinUpdateJob.success|Yes|Lyckade skapanden av tvillinguppdateringsjobb|Antal|Totalt|Antalet lyckade skapande av tvillinguppdateringsjobb.|Inga dimensioner|
|jobs.failed|Yes|Misslyckade jobb|Antal|Totalt|Antalet misslyckade jobb.|Inga dimensioner|
|jobs.listJobs.failure|Yes|Misslyckade anrop för att lista jobb|Antal|Totalt|Antalet misslyckade anrop för att visa jobb.|Inga dimensioner|
|jobs.listJobs.success|Yes|Lyckade anrop för att lista jobb|Antal|Totalt|Antalet lyckade anrop för att lista jobb.|Inga dimensioner|
|jobs.queryJobs.failure|Yes|Misslyckade jobbfrågor|Antal|Totalt|Antalet misslyckade anrop för att köra frågor mot jobb.|Inga dimensioner|
|jobs.queryJobs.success|Yes|Lyckade jobbfrågor|Antal|Totalt|Antalet lyckade anrop till frågejobb.|Inga dimensioner|
|RoutingDataSizeInBytesDelivered|Yes|Storlek på routningsleveransmeddelande i byte (förhandsversion)|Byte|Totalt|Den totala storleken i byte av meddelanden som levereras av IoT Hub till en slutpunkt. Du kan använda dimensionerna EndpointName och EndpointType för att visa storleken på meddelandena i byte som levereras till dina olika slutpunkter. Måttvärdet ökar för varje meddelande som levereras, inklusive om meddelandet levereras till flera slutpunkter eller om meddelandet levereras till samma slutpunkt flera gånger.|EndpointType, EndpointName, RoutingSource|
|RoutingDeliveries|Yes|Routningsleveranser (förhandsversion)|Antal|Totalt|Antalet gånger som IoT Hub att leverera meddelanden till alla slutpunkter med routning. Om du vill se antalet lyckade eller misslyckade försök använder du dimensionen Resultat. Om du vill se orsaken till felet, till exempel ogiltig, bortkopplad eller överbliblimt, använder du dimensionen FailureReasonCategory. Du kan också använda dimensionerna EndpointName och EndpointType för att förstå hur många meddelanden som levererades till dina olika slutpunkter. Måttvärdet ökar med ett för varje leveransförsök, inklusive om meddelandet levereras till flera slutpunkter eller om meddelandet levereras till samma slutpunkt flera gånger.|EndpointType, EndpointName, FailureReasonCategory, Result, RoutingSource|
|RoutingDeliveryLatency|Yes|Svarstid för routningsleverans (förhandsversion)|Millisekunder|Genomsnitt|Den genomsnittliga svarstiden (millisekunder) mellan meddelandeingressen till IoT Hub och telemetrimeddelandeingressen till en slutpunkt. Du kan använda dimensionerna EndpointName och EndpointType för att förstå svarstiden till dina olika slutpunkter.|EndpointType, EndpointName, RoutingSource|
|totalDeviceCount|No|Totalt antal enheter|Antal|Genomsnitt|Antal enheter som är registrerade i din IoT-hubb|Inga dimensioner|
|twinQueries.failure|Yes|Misslyckade tvillingfrågor|Antal|Totalt|Antalet misslyckade tvillingfrågor.|Inga dimensioner|
|twinQueries.resultSize|Yes|Resultatstorlek för tvillingfrågor|Byte|Genomsnitt|Medelvärde, min och max för resultatstorleken för alla lyckade tvillingfrågor.|Inga dimensioner|
|twinQueries.success|Yes|Lyckade tvillingfrågor|Antal|Totalt|Antalet lyckade tvillingfrågor.|Inga dimensioner|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft.Devices/provisioningServices

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|AttestationAttempts|Yes|Attestationsförsök|Antal|Totalt|Antal enhetsatesteringar som har försökts|ProvisioningServiceName, Status, Protocol|
|DeviceAssignments|Yes|Tilldelade enheter|Antal|Totalt|Antal enheter som tilldelats till en IoT-hubb|ProvisioningServiceName, IotHubName|
|RegistrationAttempts|Yes|Registreringsförsök|Antal|Totalt|Antal enhetsregistreringar som har försökts|ProvisioningServiceName, IotHubName, Status|


## <a name="microsoftdigitaltwinsdigitaltwinsinstances"></a>Microsoft.DigitalTwins/digitalTwinsInstances

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ApiRequests|Yes|API-begäranden|Antal|Totalt|Antalet API-begäranden som gjorts för Digital Twins åtgärder för att läsa, skriva, ta bort och fråga.|Operation, Authentication, Protocol, StatusCode, StatusCodeClass, StatusText|
|ApiRequestsFailureRate|Yes|Felfrekvens för API-begäranden|Procent|Genomsnitt|Procentandelen API-begäranden som tjänsten tar emot för din instans som returnerar en intern felsvarskod (500) för åtgärder Digital Twins läsa, skriva, ta bort och fråga.|Åtgärd, autentisering, protokoll|
|ApiRequestsLatency|Yes|Svarstid för API-begäranden|Millisekunder|Genomsnitt|Svarstiden för API-begäranden, det vill säga från den tidpunkt då begäran tas emot av Azure Digital Twins tills tjänsten skickar ett lyckat/misslyckat resultat för Digital Twins läs-, skriv-, borttagnings- och frågeåtgärder.|Operation, Authentication, Protocol, StatusCode, StatusCodeClass, StatusText|
|BillingApiOperations|Yes|API-åtgärder för fakturering|Antal|Totalt|Faktureringsmått för antalet API-begäranden som görs mot Azure Digital Twins tjänsten.|MeterId|
|BillingMessagesProcessed|Yes|Bearbetade faktureringsmeddelanden|Antal|Totalt|Faktureringsmått för antalet meddelanden som skickas ut från Azure Digital Twins till externa slutpunkter.|MeterId|
|BillingQueryUnits|Yes|Frågeenheter för fakturering|Antal|Totalt|Antalet frågeenheter, ett internt beräknat mått på användningen av tjänstresurser, som används för att köra frågor.|MeterId|
|IngressEvents|Yes|Ingresshändelser|Antal|Totalt|Antalet inkommande telemetrihändelser till Azure Digital Twins.|Resultat|
|IngressEventsFailureRate|Yes|Felfrekvens för inträffar|Procent|Genomsnitt|Procentandelen inkommande telemetrihändelser som tjänsten returnerar ett internt fel (500) svarskod för.|Inga dimensioner|
|IngressEventsLatency|Yes|Svarstid för ingresshändelser|Millisekunder|Genomsnitt|Tiden från det att en händelse anländer till när den är redo att utgående av Azure Digital Twins, då skickar tjänsten ett lyckat/misslyckat resultat.|Resultat|
|ModelCount|Yes|Modellantal|Antal|Totalt|Totalt antal modeller i Azure Digital Twins instansen. Använd det här måttet för att avgöra om du närmar dig tjänstgränsen för maximalt antal modeller som tillåts per instans.|Inga dimensioner|
|Routning|Yes|Meddelanden som dirigeras|Antal|Totalt|Antalet meddelanden som dirigeras till en Azure-slutpunktstjänst, till exempel Event Hub, Service Bus eller Event Grid.|EndpointType, Result|
|RoutingFailureRate|Yes|Frekvens för routningsfel|Procent|Genomsnitt|Procentandelen händelser som resulterar i ett fel när de dirigeras från Azure Digital Twins till en Azure-slutpunktstjänst som Event Hub, Service Bus eller Event Grid.|EndpointType|
|RoutingLatency|Yes|Svarstid för routning|Millisekunder|Genomsnitt|Tiden som förflutit mellan en händelse som dirigeras från Azure Digital Twins till när den publiceras till Azure-slutpunktstjänsten, till exempel Event Hub, Service Bus eller Event Grid.|EndpointType, Result|
|TwinCount|Yes|Antal tvillingar|Antal|Totalt|Totalt antal tvillingar i den Azure Digital Twins instansen. Använd det här måttet för att avgöra om du närmar dig tjänstgränsen för maximalt antal tillåtna tvillingar per instans.|Inga dimensioner|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|AddRegion|Yes|Tillagd region|Antal|Antal|Tillagd region|Region|
|AutoscaleMaxThroughput|No|Skala maximalt dataflöde automatiskt|Antal|Maximal|Skala maximalt dataflöde automatiskt|DatabaseName, CollectionName|
|AvailableStorage|No|(inaktuell) Tillgängligt lagringsutrymme|Byte|Totalt|"Tillgängligt lagringsutrymme" tas bort från Azure Monitor i slutet av september 2023. Cosmos DB samlingslagringsstorlek är nu obegränsad. Den enda begränsningen är att lagringsstorleken för varje logisk partitionsnyckel är 20 GB. Du kan aktivera PartitionKeyStatistics i diagnostikloggen om du vill veta lagringsförbrukningen för de viktigaste partitionsnycklarna. Mer information om hur Cosmos DB lagringskvot finns i det här https://docs.microsoft.com/azure/cosmos-db/concepts-limits dokumentet. Efter utfasningen inaktiveras de återstående aviseringsreglerna som fortfarande är definierade för det inaktuella måttet automatiskt efter utfasningsdatumet.|CollectionName, DatabaseName, Region|
|CassandraConnectionClosures|No|Stängning av Cassandra-anslutning|Antal|Totalt|Antal Cassandra-anslutningar som stängdes, rapporterat med en kornighet på 1 minut|Region, ClosureReason|
|CassandraConnectorAvgReplicationLatency|No|Genomsnittlig replikeringslatency för Cassandra-anslutning|Millisekunder|Genomsnitt|Genomsnittlig replikering för Cassandra-anslutning|Inga dimensioner|
|CassandraConnectorReplicationHealthStatus|No|Hälsostatus för Cassandra-anslutningsappens replikering|Antal|Antal|Hälsostatus för Cassandra-anslutningsappens replikering|NotStarted, ReplicationInProgress, Error|
|CassandraKeyspaceSkapa|No|Cassandra Keyspace Har skapats|Antal|Antal|Cassandra Keyspace Har skapats|ResourceName, |
|CassandraKeyspaceDelete|No|Cassandra-nyckelrymden har tagits bort|Antal|Antal|Cassandra-nyckelrymden har tagits bort|ResourceName, |
|CassandraKeyspaceThroughputUpdate|No|Cassandra Keyspace-dataflödet har uppdaterats|Antal|Antal|Cassandra Keyspace-dataflödet har uppdaterats|ResourceName, |
|CassandraKeyspaceUpdate|No|Cassandra Keyspace har uppdaterats|Antal|Antal|Cassandra Keyspace har uppdaterats|ResourceName, |
|CassandraRequestCharges|No|Avgifter för Cassandra-begäran|Antal|Totalt|RU:er som förbrukats för Cassandra-begäranden gjorda|DatabaseName, CollectionName, Region, OperationType, ResourceType|
|CassandraRequests|No|Cassandra-begäranden|Antal|Antal|Antal cassandra-begäranden som gjorts|DatabaseName, CollectionName, Region, OperationType, ResourceType, ErrorCode|
|CassandraTableSkapa|No|Cassandra-tabell skapad|Antal|Antal|Cassandra-tabell skapad|ResourceName, ChildResourceName, |
|CassandraTableDelete|No|Cassandra-tabell borttagna|Antal|Antal|Cassandra-tabell borttagna|ResourceName, ChildResourceName, |
|CassandraTableThroughputUpdate|No|Dataflödet för Cassandra-tabellen har uppdaterats|Antal|Antal|Dataflödet för Cassandra-tabellen har uppdaterats|ResourceName, ChildResourceName, |
|CassandraTableUpdate|No|Cassandra-tabellen har uppdaterats|Antal|Antal|Cassandra-tabellen har uppdaterats|ResourceName, ChildResourceName, |
|CreateAccount|Yes|Kontot har skapats|Antal|Antal|Kontot har skapats|Inga dimensioner|
|DataUsage|No|Dataanvändning|Byte|Totalt|Total dataanvändning rapporterad med 5 minuters kornighet|CollectionName, DatabaseName, Region|
|DedicatedGatewayRequests|Yes|DedicatedGatewayRequests|Antal|Antal|Begäranden vid den dedikerade gatewayen|DatabaseName, CollectionName, CacheExerciserade, OperationName, Region|
|DeleteAccount|Yes|Borttagna konton|Antal|Antal|Borttagna konton|Inga dimensioner|
|DocumentCount|No|Antal dokument|Antal|Totalt|Totalt antal dokument som rapporterats med 5 minuters kornighet|CollectionName, DatabaseName, Region|
|DocumentQuota|No|Dokumentkvot|Byte|Totalt|Total lagringskvot rapporterad med 5 minuters kornighet|CollectionName, DatabaseName, Region|
|GremlinDatabaseSkapa|No|Gremlin-databasen har skapats|Antal|Antal|Gremlin-databasen har skapats|ResourceName, |
|GremlinDatabaseDelete|No|Gremlin-databasen har tagits bort|Antal|Antal|Gremlin-databasen har tagits bort|ResourceName, |
|GremlinDatabaseThroughputUpdate|No|Gremlin Database-dataflödet har uppdaterats|Antal|Antal|Gremlin Database-dataflödet har uppdaterats|ResourceName, |
|GremlinDatabaseUpdate|No|Gremlin-databasen har uppdaterats|Antal|Antal|Gremlin-databasen har uppdaterats|ResourceName, |
|GremlinGraphSkapa|No|Gremlin Graph har skapats|Antal|Antal|Gremlin Graph har skapats|ResourceName, ChildResourceName, |
|GremlinGraphDelete|No|Gremlin Graph Borttagna|Antal|Antal|Gremlin Graph Borttagna|ResourceName, ChildResourceName, |
|GremlinGraphThroughputUpdate|No|Gremlin Graph-dataflödet har uppdaterats|Antal|Antal|Gremlin Graph-dataflödet har uppdaterats|ResourceName, ChildResourceName, |
|GremlinGraphUpdate|No|Gremlin Graph har uppdaterats|Antal|Antal|Gremlin Graph har uppdaterats|ResourceName, ChildResourceName, |
|IndexUsage|No|Indexanvändning|Byte|Totalt|Total indexanvändning rapporterad med 5 minuters kornighet|CollectionName, DatabaseName, Region|
|IntegratedCacheEvictedEntriesSize|No|IntegratedCacheEvictedEntriesSize|Byte|Genomsnitt|Storleken på de poster som avlägsnats från det integrerade cacheminnet|CacheType, Region|
|IntegratedCacheHitRate|No|IntegratedCacheHitRate|Procent|Genomsnitt|Cacheträffsfrekvens för integrerade cacheminnen|CacheType, Region|
|IntegratedCacheSize|No|IntegratedCacheSize|Byte|Genomsnitt|Storleken på integrerade cacheminnen för dedikerade gatewaybegäranden|CacheType, Region|
|IntegratedCacheTTLExpirationCount|No|IntegratedCacheTTLExpirationCount|Antal|Genomsnitt|Antal poster som har tagits bort från det integrerade cacheminnet på grund av TTL-upphörande|CacheType, Region|
|MetadataRequests|No|Metadatabegäranden|Antal|Antal|Antal metadatabegäranden. Cosmos DB underhåller systemmetadatainsamling för varje konto, vilket gör att du kan räkna upp samlingar, databaser osv. och deras konfigurationer kostnadsfritt.|DatabaseName, CollectionName, Region, StatusCode, |
|MongoCollectionSkapa|No|Mongo-samlingen har skapats|Antal|Antal|Mongo-samlingen har skapats|ResourceName, ChildResourceName, |
|MongoCollectionDelete|No|Mongo-samlingen har tagits bort|Antal|Antal|Mongo-samlingen har tagits bort|ResourceName, ChildResourceName, |
|MongoCollectionThroughputUpdate|No|Mongo-samlingens dataflöde har uppdaterats|Antal|Antal|Mongo-samlingens dataflöde har uppdaterats|ResourceName, ChildResourceName, |
|MongoCollectionUpdate|No|Mongo-samlingen har uppdaterats|Antal|Antal|Mongo-samlingen har uppdaterats|ResourceName, ChildResourceName, |
|MongoDatabaseDelete|No|Mongo-databasen har tagits bort|Antal|Antal|Mongo-databasen har tagits bort|ResourceName, |
|MongoDatabaseThroughputUpdate|No|Mongo-databasens dataflöde har uppdaterats|Antal|Antal|Mongo-databasens dataflöde har uppdaterats|ResourceName, |
|MongoDBDatabaseSkapa|No|Mongo-databasen har skapats|Antal|Antal|Mongo-databasen har skapats|ResourceName, |
|MongoDBDatabaseUpdate|No|Mongo-databasen har uppdaterats|Antal|Antal|Mongo-databasen har uppdaterats|ResourceName, |
|MongoRequestCharge|Yes|Avgift för Mongo-begäran|Antal|Totalt|Förbrukade Enheter för Mongo-begäran|DatabaseName, CollectionName, Region, CommandName, ErrorCode, Status|
|MongoRequests|Yes|Mongo-begäranden|Antal|Antal|Antal Mongo-begäranden som gjorts|DatabaseName, CollectionName, Region, CommandName, ErrorCode, Status|
|MongoRequestsCount|No|(inaktuell) Mongo-begärandefrekvens|CountPerSecond|Genomsnitt|Antal Mongo-förfrågningar per sekund|DatabaseName, CollectionName, Region, ErrorCode|
|MongoRequestsDelete|No|(inaktuell) Begärandefrekvens för Borttagning av Mongo|CountPerSecond|Genomsnitt|Begäran om borttagning av Mongo per sekund|DatabaseName, CollectionName, Region, ErrorCode|
|MongoRequestsInsert|No|(inaktuell) Begärandefrekvens för Mongo-infogning|CountPerSecond|Genomsnitt|Antal Mongo-infogningar per sekund|DatabaseName, CollectionName, Region, ErrorCode|
|MongoRequestsQuery|No|(inaktuell) Begärandefrekvens för Mongo-fråga|CountPerSecond|Genomsnitt|Mongo-frågebegäran per sekund|DatabaseName, CollectionName, Region, ErrorCode|
|MongoRequestsUpdate|No|(inaktuell) Begärandefrekvens för Mongo-uppdatering|CountPerSecond|Genomsnitt|Mongo-uppdateringsbegäran per sekund|DatabaseName, CollectionName, Region, ErrorCode|
|NormalizedRUConsumption|No|Normaliserad RU-förbrukning|Procent|Maximal|Maximal ru-förbrukning i procent per minut|CollectionName, DatabaseName, Region, PartitionKeyRangeId|
|ProvisionedThroughput|No|Etablerat dataflöde|Antal|Maximal|Etablerat dataflöde|DatabaseName, CollectionName|
|RegionFailover|Yes|Region som har fördr i tiden|Antal|Antal|Region som har fördr i tiden|Inga dimensioner|
|RemoveRegion|Yes|Borttagen region|Antal|Antal|Region borttagen|Region|
|ReplicationLatency|Yes|P99-replikeringssvarstid|Millisekunder|Genomsnitt|P99-replikeringssvarstid mellan käll- och målregioner för geo-aktiverat konto|SourceRegion, TargetRegion|
|ServerSideLatency|No|Svarstid på serversidan|Millisekunder|Genomsnitt|Svarstid på serversidan|DatabaseName, CollectionName, Region, ConnectionMode, OperationType, PublicAPIType|
|ServiceAvailability|No|Tjänsttillgänglighet|Procent|Genomsnitt|Tillgänglighet för kontobegäranden med en precision på en timme, dag eller månad|Inga dimensioner|
|SqlContainerSkapa|No|Sql-containern har skapats|Antal|Antal|Sql-containern har skapats|ResourceName, ChildResourceName, |
|SqlContainerDelete|No|Sql-containern har tagits bort|Antal|Antal|Sql-containern har tagits bort|ResourceName, ChildResourceName, |
|SqlContainerThroughputUpdate|No|Dataflödet för SQL-containern har uppdaterats|Antal|Antal|Dataflödet för SQL-containern har uppdaterats|ResourceName, ChildResourceName, |
|SqlContainerUpdate|No|SQL-containern har uppdaterats|Antal|Antal|SQL-containern har uppdaterats|ResourceName, ChildResourceName, |
|SqlDatabaseSkapa|No|Sql Database har skapats|Antal|Antal|Sql Database har skapats|ResourceName, |
|SqlDatabaseDelete|No|Sql Database borttagna|Antal|Antal|Sql Database borttagna|ResourceName, |
|SqlDatabaseThroughputUpdate|No|Sql Database-dataflödet har uppdaterats|Antal|Antal|Sql Database-dataflödet har uppdaterats|ResourceName, |
|SqlDatabaseUpdate|No|Sql Database har uppdaterats|Antal|Antal|Sql Database har uppdaterats|ResourceName, |
|TableTableSkapa|No|AzureTable-tabell skapad|Antal|Antal|AzureTable-tabell skapad|ResourceName, |
|TableTableDelete|No|AzureTable-tabell borttagna|Antal|Antal|AzureTable-tabell borttagna|ResourceName, |
|TableTableThroughputUpdate|No|Dataflödet i AzureTable-tabellen har uppdaterats|Antal|Antal|Dataflödet i AzureTable-tabellen har uppdaterats|ResourceName, |
|TableTableUpdate|No|AzureTable-tabellen har uppdaterats|Antal|Antal|AzureTable-tabellen har uppdaterats|ResourceName, |
|TotalRequests|Yes|Totalt antal förfrågningar|Antal|Antal|Antal begäranden som gjorts|DatabaseName, CollectionName, Region, StatusCode, OperationType, Status|
|TotalRequestUnits|Yes|Totalt antal enheter för begäran|Antal|Totalt|Förbrukade enheter för begäran|DatabaseName, CollectionName, Region, StatusCode, OperationType, Status|
|UpdateAccountKeys|Yes|Kontonycklarna har uppdaterats|Antal|Antal|Kontonycklarna har uppdaterats|Keytype|
|UpdateAccountNetworkSettings|Yes|Kontonätverksinställningarna har uppdaterats|Antal|Antal|Kontonätverksinställningarna har uppdaterats|Inga dimensioner|
|UpdateAccountReplicationSettings|Yes|Kontoreplikeringsinställningarna har uppdaterats|Antal|Antal|Kontoreplikeringsinställningarna har uppdaterats|Inga dimensioner|
|UpdateDiagnosticsSettings|No|Kontots diagnostikinställningar har uppdaterats|Antal|Antal|Kontots diagnostikinställningar har uppdaterats|DiagnosticSettingsName, ResourceGroupName|


## <a name="microsofteventgriddomains"></a>Microsoft.EventGrid/domäner

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Yes|Avancerade filterutvärderingar|Antal|Totalt|Totalt antal avancerade filter som utvärderats över händelseprenumerationer för det här ämnet.|Ämne, EventSubscriptionName, DomainEventSubscriptionName|
|DeadLetteredCount|Yes|Händelser med dead letter|Antal|Totalt|Totalt antal dead letter-händelser som matchar den här händelseprenumerationen|Topic, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|
|DeliveryAttemptFailCount|No|Händelser som inte kunde levereras|Antal|Totalt|Totalt antal händelser kunde inte levereras till den här händelseprenumerationen|Topic, EventSubscriptionName, DomainEventSubscriptionName, Error, ErrorType|
|DeliverySuccessCount|Yes|Levererade händelser|Antal|Totalt|Totalt antal händelser som levereras till den här händelseprenumerationen|Topic, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|No|Varaktighet för målbearbetning|Millisekunder|Genomsnitt|Varaktighet för målbearbetning i millisekunder|Topic, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Yes|Bort ignorerade händelser|Antal|Totalt|Totalt antal bort ignorerade händelser som matchar den här händelseprenumerationen|Topic, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|MatchedEventCount|Yes|Matchade händelser|Antal|Totalt|Totalt antal händelser som matchas mot den här händelseprenumerationen|Topic, EventSubscriptionName, DomainEventSubscriptionName|
|PublishFailCount|Yes|Publicera misslyckade händelser|Antal|Totalt|Totalt antal händelser som inte kunde publiceras till det här avsnittet|Ämne, ErrorType, Fel|
|PublishSuccessCount|Yes|Publicerade händelser|Antal|Totalt|Totalt antal händelser som publicerats till det här ämnet|Avsnitt|
|PublishSuccessLatencyInMs|Yes|Publicera lyckade svarstider|Millisekunder|Totalt|Publicera lyckade svarstider i millisekunder|Inga dimensioner|


## <a name="microsofteventgrideventsubscriptions"></a>Microsoft.EventGrid/eventSubscriptions

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Händelser med dead letter|Antal|Totalt|Totalt antal dead letter-händelser som matchar den här händelseprenumerationen|DeadLetterReason|
|DeliveryAttemptFailCount|No|Händelser som inte kunde levereras|Antal|Totalt|Totalt antal händelser kunde inte levereras till den här händelseprenumerationen|Fel, ErrorType|
|DeliverySuccessCount|Yes|Levererade händelser|Antal|Totalt|Totalt antal händelser som levereras till den här händelseprenumerationen|Inga dimensioner|
|DestinationProcessingDurationInMs|No|Varaktighet för målbearbetning|Millisekunder|Genomsnitt|Varaktighet för målbearbetning i millisekunder|Inga dimensioner|
|DroppedEventCount|Yes|Bort ignorerade händelser|Antal|Totalt|Totalt antal bort ignorerade händelser som matchar den här händelseprenumerationen|DropReason|
|MatchedEventCount|Yes|Matchade händelser|Antal|Totalt|Totalt antal händelser som matchas mot den här händelseprenumerationen|Inga dimensioner|


## <a name="microsofteventgridextensiontopics"></a>Microsoft.EventGrid/extensionTopics

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|PublishFailCount|Yes|Publicera misslyckade händelser|Antal|Totalt|Totalt antal händelser kunde inte publiceras i det här avsnittet|ErrorType, Error|
|PublishSuccessCount|Yes|Publicerade händelser|Antal|Totalt|Totalt antal händelser som publicerats till det här ämnet|Inga dimensioner|
|PublishSuccessLatencyInMs|Yes|Publicera lyckade svarstider|Millisekunder|Totalt|Publicera lyckade svarstider i millisekunder|Inga dimensioner|
|UnmatchedEventCount|Yes|Omatchade händelser|Antal|Totalt|Totalt antal händelser som inte matchar någon av händelseprenumerationer för det här ämnet|Inga dimensioner|


## <a name="microsofteventgridpartnernamespaces"></a>Microsoft.EventGrid/partnerNamespaces

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Händelser med dead letter|Antal|Totalt|Totalt antal händelser med dead letter-händelser som matchar den här händelseprenumerationen|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Händelser som inte kunde levereras|Antal|Totalt|Totalt antal händelser kunde inte levereras till den här händelseprenumerationen|Error, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Levererade händelser|Antal|Totalt|Totalt antal händelser som levereras till den här händelseprenumerationen|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Varaktighet för målbearbetning|Millisekunder|Genomsnitt|Varaktighet för målbearbetning i millisekunder|EventSubscriptionName|
|DroppedEventCount|Yes|Bort ignorerade händelser|Antal|Totalt|Totalt antal bort ignorerade händelser som matchar den här händelseprenumerationen|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Matchade händelser|Antal|Totalt|Totalt antal händelser som matchats mot den här händelseprenumerationen|EventSubscriptionName|
|PublishFailCount|Yes|Publicera misslyckade händelser|Antal|Totalt|Totalt antal händelser kunde inte publiceras i det här avsnittet|ErrorType, Error|
|PublishSuccessCount|Yes|Publicerade händelser|Antal|Totalt|Totalt antal händelser som publicerats till det här ämnet|Inga dimensioner|
|PublishSuccessLatencyInMs|Yes|Publicera lyckade svarstider|Millisekunder|Totalt|Publicera lyckade svarstider i millisekunder|Inga dimensioner|
|UnmatchedEventCount|Yes|Omatchade händelser|Antal|Totalt|Totalt antal händelser som inte matchar någon av händelseprenumerationer för det här ämnet|Inga dimensioner|


## <a name="microsofteventgridpartnertopics"></a>Microsoft.EventGrid/partnerTopics

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Yes|Avancerade filterutvärderingar|Antal|Totalt|Totalt antal avancerade filter som utvärderats i händelseprenumerationer för det här ämnet.|EventSubscriptionName|
|DeadLetteredCount|Yes|Händelser med dead letter|Antal|Totalt|Totalt antal händelser med dead letter-händelser som matchar den här händelseprenumerationen|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Händelser som inte kunde levereras|Antal|Totalt|Totalt antal händelser kunde inte levereras till den här händelseprenumerationen|Error, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Levererade händelser|Antal|Totalt|Totalt antal händelser som levereras till den här händelseprenumerationen|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Varaktighet för målbearbetning|Millisekunder|Genomsnitt|Varaktighet för målbearbetning i millisekunder|EventSubscriptionName|
|DroppedEventCount|Yes|Bort ignorerade händelser|Antal|Totalt|Totalt antal bort ignorerade händelser som matchar den här händelseprenumerationen|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Matchade händelser|Antal|Totalt|Totalt antal händelser som matchats mot den här händelseprenumerationen|EventSubscriptionName|
|PublishFailCount|Yes|Publicera misslyckade händelser|Antal|Totalt|Totalt antal händelser kunde inte publiceras i det här avsnittet|ErrorType, Error|
|PublishSuccessCount|Yes|Publicerade händelser|Antal|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Inga dimensioner|
|UnmatchedEventCount|Yes|Omatchade händelser|Antal|Totalt|Totalt antal händelser som inte matchar någon av händelseprenumerationer för det här ämnet|Inga dimensioner|


## <a name="microsofteventgridsystemtopics"></a>Microsoft.EventGrid/systemTopics

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Yes|Avancerade filterutvärderingar|Antal|Totalt|Totalt antal avancerade filter som utvärderats i händelseprenumerationer för det här ämnet.|EventSubscriptionName|
|DeadLetteredCount|Yes|Händelser med dead letter|Antal|Totalt|Totalt antal händelser med dead letter-händelser som matchar den här händelseprenumerationen|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Händelser som inte kunde levereras|Antal|Totalt|Totalt antal händelser kunde inte levereras till den här händelseprenumerationen|Error, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Levererade händelser|Antal|Totalt|Totalt antal händelser som levereras till den här händelseprenumerationen|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Varaktighet för målbearbetning|Millisekunder|Genomsnitt|Varaktighet för målbearbetning i millisekunder|EventSubscriptionName|
|DroppedEventCount|Yes|Bort ignorerade händelser|Antal|Totalt|Totalt antal bort ignorerade händelser som matchar den här händelseprenumerationen|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Matchade händelser|Antal|Totalt|Totalt antal händelser som matchats mot den här händelseprenumerationen|EventSubscriptionName|
|PublishFailCount|Yes|Publicera misslyckade händelser|Antal|Totalt|Totalt antal händelser som inte kunde publiceras till det här avsnittet|ErrorType, Error|
|PublishSuccessCount|Yes|Publicerade händelser|Antal|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Inga dimensioner|
|PublishSuccessLatencyInMs|Yes|Publicera lyckade svarstider|Millisekunder|Totalt|Publicera lyckade svarstider i millisekunder|Inga dimensioner|
|UnmatchedEventCount|Yes|Omatchade händelser|Antal|Totalt|Totalt antal händelser som inte matchar någon av händelseprenumerationer för det här ämnet|Inga dimensioner|


## <a name="microsofteventgridtopics"></a>Microsoft.EventGrid/topics

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Yes|Avancerade filterutvärderingar|Antal|Totalt|Totalt antal avancerade filter som utvärderats över händelseprenumerationer för det här ämnet.|EventSubscriptionName|
|DeadLetteredCount|Yes|Händelser med dead letter|Antal|Totalt|Totalt antal dead letter-händelser som matchar den här händelseprenumerationen|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Händelser som inte kunde levereras|Antal|Totalt|Totalt antal händelser kunde inte levereras till den här händelseprenumerationen|Error, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Levererade händelser|Antal|Totalt|Totalt antal händelser som levereras till den här händelseprenumerationen|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Varaktighet för målbearbetning|Millisekunder|Genomsnitt|Varaktighet för målbearbetning i millisekunder|EventSubscriptionName|
|DroppedEventCount|Yes|Bort ignorerade händelser|Antal|Totalt|Totalt antal bort ignorerade händelser som matchar den här händelseprenumerationen|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Matchade händelser|Antal|Totalt|Totalt antal händelser som matchas mot den här händelseprenumerationen|EventSubscriptionName|
|PublishFailCount|Yes|Publicera misslyckade händelser|Antal|Totalt|Totalt antal händelser som inte kunde publiceras till det här avsnittet|ErrorType, Error|
|PublishSuccessCount|Yes|Publicerade händelser|Antal|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Inga dimensioner|
|PublishSuccessLatencyInMs|Yes|Publicera lyckade svarstider|Millisekunder|Totalt|Publicera lyckade svarstider i millisekunder|Inga dimensioner|
|UnmatchedEventCount|Yes|Omatchade händelser|Antal|Totalt|Totalt antal händelser som inte matchar någon av händelseprenumerationer för det här ämnet|Inga dimensioner|


## <a name="microsofteventhubclusters"></a>Microsoft.EventHub/clusters

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ActiveConnections|No|ActiveConnections|Antal|Genomsnitt|Totalt antal aktiva anslutningar för Microsoft.EventHub.||
|AvailableMemory|No|Tillgängligt minne|Procent|Maximal|Tillgängligt minne för Händelsehubbklustret som en procentandel av det totala minnet.|Roll|
|CaptureBacklog|No|Samla in loggar.|Antal|Totalt|Capture Backlog for Microsoft.EventHub.||
|CapturedBytes|No|Avbildade byte.|Byte|Totalt|Avbildade byte för Microsoft.EventHub.||
|CapturedMessages|No|Infångade meddelanden.|Antal|Totalt|Infångade meddelanden för Microsoft.EventHub.||
|Anslutningarclosed|No|Stängda anslutningar.|Antal|Genomsnitt|Anslutningar som har stängts för Microsoft.EventHub.||
|AnslutningarÖppnad|No|Öppna anslutningar.|Antal|Genomsnitt|Anslutningar öppnade för Microsoft.EventHub.||
|Processor|No|Processor|Procent|Maximal|CPU-användning för händelsehubbklustret i procent|Roll|
|IncomingBytes|Yes|Inkommande byte.|Byte|Totalt|Inkommande byte för Microsoft.EventHub.||
|IncomingMessages|Yes|Inkommande meddelanden|Antal|Totalt|Inkommande meddelanden för Microsoft.EventHub.||
|IncomingRequests|Yes|Inkommande förfrågningar|Antal|Totalt|Inkommande begäranden för Microsoft.EventHub.||
|Utgåendebyte|Yes|Utgående byte.|Byte|Totalt|Utgående byte för Microsoft.EventHub.||
|OutgoingMessages|Yes|Utgående meddelanden|Antal|Totalt|Utgående meddelanden för Microsoft.EventHub.||
|QuotaExceededErrors|No|Fel på grund av överskriden kvot.|Antal|Totalt|Fel om överskriden kvot för Microsoft.EventHub.|OperationResult|
|ServerErrors|No|Serverfel.|Antal|Totalt|Serverfel för Microsoft.EventHub.|OperationResult|
|Storlek|No|Storlek|Byte|Genomsnitt|Storleken på en EventHub i byte.|Roll|
|SuccessfulRequests|No|Slutförda förfrågningar|Antal|Totalt|Lyckade begäranden för Microsoft.EventHub.|OperationResult|
|ThrottledRequests|No|Begränsade förfrågningar.|Antal|Totalt|Begränsade begäranden för Microsoft.EventHub.|OperationResult|
|UserErrors|No|Användarfel.|Antal|Totalt|Användarfel för Microsoft.EventHub.|OperationResult|


## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namnrymder

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ActiveConnections|No|ActiveConnections|Antal|Genomsnitt|Totalt antal aktiva anslutningar för Microsoft.EventHub.||
|CaptureBacklog|No|Samla in loggar.|Antal|Totalt|Capture Backlog for Microsoft.EventHub.|Entityname|
|CapturedBytes|No|Avbildade byte.|Byte|Totalt|Avbildade byte för Microsoft.EventHub.|Entityname|
|CapturedMessages|No|Avbildade meddelanden.|Antal|Totalt|Avbildade meddelanden för Microsoft.EventHub.|Entityname|
|Anslutningarclosed|No|Stängda anslutningar.|Antal|Genomsnitt|Anslutningar som har stängts för Microsoft.EventHub.|Entityname|
|AnslutningarÖppnad|No|Öppna anslutningar.|Antal|Genomsnitt|Anslutningar öppnade för Microsoft.EventHub.|Entityname|
|E UPPL|Yes|Arkivera eftersläpna meddelanden (inaktuella)|Antal|Totalt|Händelsehubbarkivmeddelanden i eftersläpna för ett namnområde (inaktuellt)||
|EHAMBS|Yes|Arkivera meddelandegenomflöde (inaktuellt)|Byte|Totalt|Händelsehubb arkiverat meddelandegenomflöde i ett namnområde (inaktuellt)||
|EHAMSGS|Yes|Arkivera meddelanden (inaktuella)|Antal|Totalt|Händelsehubbarkiverade meddelanden i ett namnområde (inaktuellt)||
|EHINBYTE|Yes|Inkommande byte (inaktuell)|Byte|Totalt|Inkommande meddelandeflöde för händelsehubb för ett namnområde (inaktuellt)||
|EHINMBS|Yes|Inkommande byte (inaktuell) (inaktuell)|Byte|Totalt|Händelsehubb inkommande meddelandegenomflöde för ett namnområde. Det här måttet är inaktuellt. Använd måttet Inkommande byte i stället (inaktuellt)||
|EHINMSGS|Yes|Inkommande meddelanden (inaktuella)|Antal|Totalt|Totalt antal inkommande meddelanden för ett namnområde (inaktuellt)||
|EHOUTBYTES|Yes|Utgående byte (inaktuell)|Byte|Totalt|Utgående meddelandeflöde för händelsehubb för ett namnområde (inaktuellt)||
|EHOUTMBS|Yes|Utgående byte (inaktuellt) (inaktuellt)|Byte|Totalt|Utgående meddelandeflöde för händelsehubb för ett namnområde. Det här måttet är inaktuellt. Använd måttet Utgående byte i stället (inaktuellt)||
|EHOUTMSGS|Yes|Utgående meddelanden (inaktuella)|Antal|Totalt|Totalt antal utgående meddelanden för ett namnområde (inaktuellt)||
|FAILREQ|Yes|Misslyckade begäranden (inaktuella)|Antal|Totalt|Totalt antal misslyckade begäranden för ett namnområde (inaktuellt)||
|IncomingBytes|Yes|Inkommande byte.|Byte|Totalt|Inkommande byte för Microsoft.EventHub.|Entityname|
|IncomingMessages|Yes|Inkommande meddelanden|Antal|Totalt|Inkommande meddelanden för Microsoft.EventHub.|Entityname|
|IncomingRequests|Yes|Inkommande förfrågningar|Antal|Totalt|Inkommande begäranden för Microsoft.EventHub.|Entityname|
|INMSGS|Yes|Inkommande meddelanden (inaktuell) (inaktuell)|Antal|Totalt|Totalt antal inkommande meddelanden för ett namnområde. Det här måttet är inaktuellt. Använd måttet Inkommande meddelanden i stället (inaktuellt)||
|INREQS|Yes|Inkommande begäranden (inaktuella)|Antal|Totalt|Totalt antal inkommande begäranden för ett namnområde (inaktuellt)||
|INTERR|Yes|Interna serverfel (inaktuella)|Antal|Totalt|Totalt antal interna serverfel för ett namnområde (inaktuellt)||
|MISCERR|Yes|Andra fel (inaktuella)|Antal|Totalt|Totalt antal misslyckade begäranden för ett namnområde (inaktuellt)||
|Utgåendebyte|Yes|Utgående byte.|Byte|Totalt|Utgående byte för Microsoft.EventHub.|Entityname|
|OutgoingMessages|Yes|Utgående meddelanden|Antal|Totalt|Utgående meddelanden för Microsoft.EventHub.|Entityname|
|OUTMSGS|Yes|Utgående meddelanden (föråldrade) (inaktuella)|Antal|Totalt|Totalt antal utgående meddelanden för ett namnområde. Det här måttet är inaktuellt. Använd måttet Utgående meddelanden i stället (inaktuellt)||
|QuotaExceededErrors|No|Fel på grund av överskriden kvot.|Antal|Totalt|Fel om överskriden kvot för Microsoft.EventHub.|EntityName, OperationResult|
|ServerErrors|No|Serverfel.|Antal|Totalt|Serverfel för Microsoft.EventHub.|EntityName, OperationResult|
|Storlek|No|Storlek|Byte|Genomsnitt|Storleken på en EventHub i byte.|Entityname|
|SuccessfulRequests|No|Slutförda förfrågningar|Antal|Totalt|Lyckade begäranden för Microsoft.EventHub.|EntityName, OperationResult|
|SUCCREQ|Yes|Lyckade begäranden (inaktuella)|Antal|Totalt|Totalt antal lyckade begäranden för ett namnområde (inaktuellt)||
|SVRBSY|Yes|Fel vid upptagen server (inaktuell)|Antal|Totalt|Totalt antal fel vid upptagen server för ett namnområde (inaktuellt)||
|ThrottledRequests|No|Begränsade förfrågningar.|Antal|Totalt|Begränsade begäranden för Microsoft.EventHub.|EntityName, OperationResult|
|UserErrors|No|Användarfel.|Antal|Totalt|Användarfel för Microsoft.EventHub.|EntityName, OperationResult|


## <a name="microsofthdinsightclusters"></a>Microsoft.HDInsight/clusters

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|CategorizedGatewayRequests|Yes|Kategoriserade gatewaybegäranden|Antal|Totalt|Antal gateway-begäranden efter kategorier (1xx/2xx/3xx/4xx/5xx)|HttpStatus|
|GatewayRequests|Yes|Gateway-begäranden|Antal|Totalt|Antal gatewaybegäranden|HttpStatus|
|KafkaRestProxy.ConsumerRequest.m1_delta|Yes|REST-proxy för konsumentbegäranGenomflöde|CountPerSecond|Totalt|Antal konsumentbegäranden till Kafka REST-proxy|Dator, ämne|
|KafkaRestProxy.ConsumerRequestFail.m1_delta|Yes|BEGÄRANDEN om misslyckad REST-proxykonsument|CountPerSecond|Totalt|Undantag för konsumentbegäran|Dator, ämne|
|KafkaRestProxy.ConsumerRequestTime.p95|Yes|REST proxy Consumer RequestLatency|Millisekunder|Genomsnitt|Meddelandefördröjning i en konsumentbegäran via Kafka REST-proxy|Dator, ämne|
|KafkaRestProxy.ConsumerRequestContinInQueueTime.p95|Yes|Förfrågningslogg för REST-proxykonsument|Millisekunder|Genomsnitt|Kölängd för konsument-REST-proxy|Dator, ämne|
|KafkaRestProxy.MessagesIn.m1_delta|Yes|REST-proxyproducentmeddelandeGenomflöde|CountPerSecond|Totalt|Antal producentmeddelanden via Kafka REST-proxy|Dator, ämne|
|KafkaRestProxy.MessagesOut.m1_delta|Yes|KONSUMENTmeddelande för REST-proxy|CountPerSecond|Totalt|Antal konsumentmeddelanden via Kafka REST-proxy|Dator, ämne|
|KafkaRestProxy.OpenConnections|Yes|REST-proxy concurrentConnections|Antal|Totalt|Antal samtidiga anslutningar via Kafka REST-proxy|Dator, ämne|
|KafkaRestProxy.ProducerRequest.m1_delta|Yes|REST proxy Producer RequestThroughput|CountPerSecond|Totalt|Antal producentbegäranden till Kafka REST-proxy|Dator, ämne|
|KafkaRestProxy.ProducerRequestFail.m1_delta|Yes|REST-proxyproducent misslyckade begäranden|CountPerSecond|Totalt|Undantag för producentbegäran|Dator, ämne|
|KafkaRestProxy.ProducerRequestTime.p95|Yes|REST-proxyproducent RequestLatency|Millisekunder|Genomsnitt|Meddelandesvarstid i en producentbegäran via Kafka REST-proxy|Dator, ämne|
|KafkaRestProxy.ProducerRequestContinInQueueTime.p95|Yes|REST-proxy producentbegäran eftersläpning|Millisekunder|Genomsnitt|Längd på producent-REST-proxykö|Dator, ämne|
|NumActiveWorkers|Yes|Antal aktiva arbetare|Antal|Maximal|Antal aktiva arbetare|MetricName|


## <a name="microsofthealthcareapisservices"></a>Microsoft.HealthcareApis/services

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Yes|Tillgänglighet|Procent|Genomsnitt|Tjänstens tillgänglighetsfrekvens.|Inga dimensioner|
|CosmosDbCollectionSize|Yes|Cosmos DB samlingsstorlek|Byte|Totalt|Storleken på Cosmos DB i byte.|Inga dimensioner|
|CosmosDbIndexSize|Yes|Cosmos DB indexstorlek|Byte|Totalt|Storleken på Cosmos DB samlingens index, i byte.|Inga dimensioner|
|CosmosDbRequestCharge|Yes|Cosmos DB RU-användning|Antal|Totalt|RU-användningen av begäranden till tjänstens Cosmos DB.|Åtgärd, ResourceType|
|CosmosDbRequests|Yes|Service Cosmos DB begäranden|Antal|Sum|Det totala antalet begäranden som görs till en tjänsts Cosmos DB.|Åtgärd, ResourceType|
|CosmosDbThrottleRate|Yes|Begränsningsfrekvens Cosmos DB tjänsttjänster|Antal|Sum|Det totala antalet 429 svar från en tjänsts Cosmos DB.|Åtgärd, ResourceType|
|IoTConnectorDeviceEvent|Yes|Antal inkommande meddelanden|Antal|Sum|Det totala antalet meddelanden som tagits emot av Azure IoT Connector FHIR före normalisering.|Åtgärd, ConnectorName|
|IoTConnectorDeviceEventProcessingLatencyMs|Yes|Genomsnittlig normalisering av fassvarstid|Millisekunder|Genomsnitt|Genomsnittlig tid mellan en händelses inmatningstid och den tid då händelsen bearbetas för normalisering.|Åtgärd, ConnectorName|
|IoTConnectorMeasurement|Yes|Antal mått|Antal|Sum|Antalet normaliserade värdeavläsningar som tagits emot i FHIR-konverteringsfasen för Azure IoT Connector för FHIR.|Åtgärd, ConnectorName|
|IoTConnectorMeasurementGroup|Yes|Antal meddelandegrupper|Antal|Sum|Det totala antalet unika grupper av mått över typ, enhet, patient och konfigurerad tidsperiod som genereras av FHIR-konverteringsfasen.|Åtgärd, ConnectorName|
|IoTConnectorMeasurementIngestionLatencyMs|Yes|Genomsnittlig svarstid för gruppsteg|Millisekunder|Genomsnitt|Tidsperioden mellan den tidpunkt IoT Connector tog emot enhetsdata och när data bearbetas av FHIR-konverteringsfasen.|Åtgärd, ConnectorName|
|IoTConnectorNormalizedEvent|Yes|Antal normaliserade meddelanden|Antal|Sum|Det totala antalet mappade normaliserade värden som matas ut från normaliseringssteget för Azure IoT Connector för FHIR.|Åtgärd, ConnectorName|
|IoTConnectorTotalErrors|Yes|Totalt antal fel|Antal|Sum|Det totala antalet fel som loggats av Azure IoT Connector för FHIR|Name, Operation, ErrorType, ErrorSeverity, ConnectorName|
|ServiceApiErrors|Yes|Tjänstfel|Antal|Sum|Det totala antalet interna serverfel som genererats av tjänsten.|Protokoll, Autentisering, Åtgärd, ResourceType, StatusCode, StatusCodeClass, StatusCodeText|
|ServiceApiLatency|Yes|Tjänstens svarstid|Millisekunder|Genomsnitt|Tjänstens svarstid.|Protokoll, Autentisering, Åtgärd, ResourceType, StatusCode, StatusCodeClass, StatusCodeText|
|ServiceApiRequests|Yes|Service Requests|Antal|Sum|Det totala antalet begäranden som tagits emot av tjänsten.|Protokoll, Autentisering, Åtgärd, ResourceType, StatusCode, StatusCodeClass, StatusCodeText|
|TotalErrors|Yes|Totalt antal fel|Antal|Sum|Det totala antalet interna serverfel som tjänsten har påträffat.|Protokoll, StatusCode, StatusCodeClass, StatusCodeText|
|TotalLatency|Yes|Total svarstid|Millisekunder|Genomsnitt|Svarstiden för svaret för tjänsten.|Protokoll|
|TotalRequests|Yes|Totalt antal förfrågningar|Antal|Sum|Det totala antalet begäranden som tagits emot av tjänsten.|Protokoll|


## <a name="microsofthybridnetworknetworkfunctions"></a>microsoft.hybridnetwork/networkfunctions

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|HyperVVirtualProcessorUtilization|Yes|Genomsnittlig cpu-användning|Procent|Genomsnitt|Total genomsnittlig procentandel av den virtuella processoranvändningen med ett minutsintervall. Det totala antalet virtuella processorer baseras på användarkonfigurerat värde i SKU-definitionen. Ytterligare filter kan tillämpas baserat på RoleName som definierats i SKU.|InstanceName|


## <a name="microsofthybridnetworkvirtualnetworkfunctions"></a>microsoft.hybridnetwork/virtualnetworkfunctions

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|HyperVVirtualProcessorUtilization|Yes|Genomsnittlig cpu-användning|Procent|Genomsnitt|Total genomsnittlig procentandel av den virtuella processoranvändningen med ett minutsintervall. Det totala antalet virtuella processorer baseras på användarkonfigurerat värde i SKU-definitionen. Ytterligare filter kan tillämpas baserat på RoleName som definierats i SKU.|InstanceName|


## <a name="microsoftinsightsautoscalesettings"></a>microsoft.insights/autoscalesettings

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|MetricThreshold|Yes|Tröskelvärde för mått|Antal|Genomsnitt|Det konfigurerade tröskelvärdet för autoskalning när autoskalning kördes.|MetricTriggerRule|
|ObservedCapacity|Yes|Observerad kapacitet|Antal|Genomsnitt|Kapaciteten rapporterades till autoskalning när den kördes.||
|ObservedMetricValue|Yes|Observerat måttvärde|Antal|Genomsnitt|Värdet som beräknas med autoskalning när det körs|MetricTriggerSource|
|ScaleActionsInitiated|Yes|Initierade skalningsåtgärder|Antal|Totalt|Skalningsåtgärdens riktning.|ScaleDirection|


## <a name="microsoftinsightscomponents"></a>Microsoft.Insights/Components

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Yes|Tillgänglighet|Procent|Genomsnitt|Procentandel slutförda tillgänglighetstester|availabilityResult/name, availabilityResult/location|
|availabilityResults/count|No|Tillgänglighetstester|Antal|Antal|Antal tillgänglighetstester|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|availabilityResults/duration|Yes|Varaktighet för tillgänglighetstest|Millisekunder|Genomsnitt|Varaktighet för tillgänglighetstest|availabilityResult/name, availabilityResult/location, availabilityResult/success|
|browserTimings/networkDuration|Yes|Nätverks connect-tid för sidinläsning|Millisekunder|Genomsnitt|Tiden mellan användarbegäran och nätverksanslutning. Innehåller DNS-sökning och transportanslutning.|Inga dimensioner|
|browserTimings/processingDuration|Yes|Klientbearbetningstid|Millisekunder|Genomsnitt|Tiden mellan mottagandet av den sista byten i ett dokument tills DOM har lästs in. Asynkrona begäranden kan fortfarande bearbetas.|Inga dimensioner|
|browserTimings/receiveDuration|Yes|Tar emot svarstid|Millisekunder|Genomsnitt|Tid mellan första och sista byte eller till frånkoppling.|Inga dimensioner|
|browserTimings/sendDuration|Yes|Tid för att skicka begäran|Millisekunder|Genomsnitt|Tiden mellan nätverksanslutningen och mottagandet av den första byten.|Inga dimensioner|
|browserTimings/totalDuration|Yes|Webbläsarens sidinläsningstid|Millisekunder|Genomsnitt|Tid från användarbegäran tills DOM, formatmallar, skript och bilder har lästs in.|Inga dimensioner|
|beroenden/antal|No|Beroendeanrop|Antal|Antal|Antal anrop som görs av programmet till externa resurser.|dependency/type, dependency/performanceBucket, dependency/success, dependency/target, dependency/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|beroenden/varaktighet|Yes|Beroendevaraktighet|Millisekunder|Genomsnitt|Varaktighet för anrop som görs av programmet till externa resurser.|dependency/type, dependency/performanceBucket, dependency/success, dependency/target, dependency/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|beroenden/misslyckades|No|Fel vid beroendeanrop|Antal|Antal|Antal misslyckade beroendeanrop som gjorts av programmet till externa resurser.|dependency/type, dependency/performanceBucket, dependency/target, dependency/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|undantag/webbläsare|No|Webbläsarundantag|Antal|Antal|Antal undantag som inte har uttts i webbläsaren.|cloud/roleName|
|undantag/antal|Yes|Undantag|Antal|Antal|Kombinerat antal av alla undantag som inte är undantag.|cloud/roleName, cloud/roleInstance, client/type|
|undantag/server|No|Serverundantag|Antal|Antal|Antal undantag som inte har uttölts i serverprogrammet.|cloud/roleName, cloud/roleInstance|
|pageViews/count|Yes|Sidvisningar|Antal|Antal|Antal sidvisningar.|operation/synthetic, cloud/roleName|
|pageViews/duration|Yes|Inläsningstid för sidvisning|Millisekunder|Genomsnitt|Inläsningstid för sidvisning|operation/synthetic, cloud/roleName|
|performanceCounters/exceptionsPerSecond|Yes|Undantagsfrekvens|CountPerSecond|Genomsnitt|Antal hanterade och ohanterade undantag som rapporteras till Windows, inklusive .NET-undantag och ohanterade undantag som konverteras till .NET-undantag.|cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Yes|Tillgängligt minne|Byte|Genomsnitt|Fysiskt minne som omedelbart är tillgängligt för allokering till en process eller för systemanvändning.|cloud/roleInstance|
|performanceCounters/processCpuPercentage|Yes|Processprocessor|Procent|Genomsnitt|Den procentandel av förfluten tid som alla processtrådar använde processorn för att köra instruktioner. Detta kan variera mellan 0 och 100. Det här måttet anger prestanda för enbart w3wp-processen.|cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Yes|Process-I/S-hastighet|BytePerSecond|Genomsnitt|Totalt antal byte per sekund som lästs och skrivits till filer, nätverk och enheter.|cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Yes|Processortid|Procent|Genomsnitt|Den procentandel tid som processorn tillbringar i icke-inaktiva trådar.|cloud/roleInstance|
|performanceCounters/processPrivateBytes|Yes|Bearbeta privata byte|Byte|Genomsnitt|Minnet tilldelas exklusivt till det övervakade programmets processer.|cloud/roleInstance|
|performanceCounters/requestExecutionTime|Yes|Körningstid för HTTP-begäran|Millisekunder|Genomsnitt|Körningstid för den senaste begäran.|cloud/roleInstance|
|performanceCounters/requestsInQueue|Yes|HTTP-begäranden i programkö|Antal|Genomsnitt|Längden på kön för programbegäran.|cloud/roleInstance|
|performanceCounters/requestsPerSecond|Yes|HTTP-begärandefrekvens|CountPerSecond|Genomsnitt|Frekvens för alla begäranden till programmet per sekund från ASP.NET.|cloud/roleInstance|
|begäranden/antal|No|Serverbegäranden|Antal|Antal|Antal SLUTFÖRDa HTTP-begäranden.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|begäranden/varaktighet|Yes|Serversvarstid|Millisekunder|Genomsnitt|Tiden mellan att ta emot en HTTP-begäran och slutföra svaret.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|begäranden/misslyckades|No|Misslyckade förfrågningar|Antal|Antal|Antal HTTP-begäranden som markerats som misslyckade. I de flesta fall är det begäranden med en svarskod >= 400 och inte lika med 401.|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, cloud/roleName|
|begäranden/hastighet|No|Frekvens för serverbegäran|CountPerSecond|Genomsnitt|Frekvens för serverbegäranden per sekund|request/performanceBucket, request/resultCode, operation/synthetic, cloud/roleInstance, request/success, cloud/roleName|
|traces/count|Yes|Spårningar|Antal|Antal|Antal spårningsdokument|trace/severityLevel, operation/synthetic, cloud/roleName, cloud/roleInstance|


## <a name="microsoftiotcentraliotapps"></a>Microsoft.IoTCentral/IoTApps

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|c2d.commands.failure|Yes|Misslyckade kommandoanrop|Antal|Totalt|Antalet misslyckade kommandobegäranden som initierats från IoT Central|Inga dimensioner|
|c2d.commands.requestSize|Yes|Begärandestorleken för kommandoanrop|Byte|Totalt|Begärandestorlek för alla kommandobegäranden som initierats från IoT Central|Inga dimensioner|
|c2d.commands.responseSize|Yes|Svarsstorlek för kommandoanrop|Byte|Totalt|Svarsstorlek för alla kommandosvar som initierats från IoT Central|Inga dimensioner|
|c2d.commands.success|Yes|Lyckade kommandoanrop|Antal|Totalt|Antalet lyckade kommandobegäranden som initierats från IoT Central|Inga dimensioner|
|c2d.property.read.failure|Yes|Det gick inte att läsa enhetsegenskapen från IoT Central|Antal|Totalt|Antalet misslyckade egenskapsläsningar som initierats från IoT Central|Inga dimensioner|
|c2d.property.read.success|Yes|Lyckade enhetsegenskapsläsningar från IoT Central|Antal|Totalt|Antalet lyckade egenskapsläsningar som initierats från IoT Central|Inga dimensioner|
|c2d.property.update.failure|Yes|Misslyckade uppdateringar av enhetsegenskap från IoT Central|Antal|Totalt|Antalet misslyckade egenskapsuppdateringar som initierats från IoT Central|Inga dimensioner|
|c2d.property.update.success|Yes|Lyckade uppdateringar av enhetsegenskap från IoT Central|Antal|Totalt|Antalet lyckade egenskapsuppdateringar som initierats från IoT Central|Inga dimensioner|
|connectedDeviceCount|No|Totalt antal anslutna enheter|Antal|Genomsnitt|Antal enheter som är anslutna till IoT Central|Inga dimensioner|
|d2c.property.read.failure|Yes|Misslyckade enhetsegenskapsläsningar från enheter|Antal|Totalt|Antalet misslyckade egenskapsläsningar som initierats från enheter|Inga dimensioner|
|d2c.property.read.success|Yes|Lyckade enhetsegenskapsläsningar från enheter|Antal|Totalt|Antalet lyckade egenskapsläsningar som initierats från enheter|Inga dimensioner|
|d2c.property.update.failure|Yes|Misslyckade enhetsegenskapsuppdateringar från enheter|Antal|Totalt|Antalet misslyckade egenskapsuppdateringar som initierats från enheter|Inga dimensioner|
|d2c.property.update.success|Yes|Lyckade enhetsegenskapsuppdateringar från enheter|Antal|Totalt|Antalet lyckade egenskapsuppdateringar som initierats från enheter|Inga dimensioner|
|d2c.telemetry.ingress.allProtocol|Yes|Totalt antal försök att skicka telemetrimeddelanden|Antal|Totalt|Antal telemetrimeddelanden från enhet till moln som försökte skickas till IoT Central program|Inga dimensioner|
|d2c.telemetry.ingress.success|Yes|Totalt antal skickade telemetrimeddelanden|Antal|Totalt|Antal telemetrimeddelanden från enhet till moln som har skickats till IoT Central program|Inga dimensioner|
|dataExport.error|Yes|Dataexportfel|Antal|Totalt|Antal påträffade fel för dataexport|exportId, exportDisplayName, destinationId, destinationDisplayName|
|dataExport.messages.filtered|Yes|Filtrerade dataexportmeddelanden|Antal|Totalt|Antal meddelanden som har passerat filter i dataexporten|exportId, exportDisplayName, destinationId, destinationDisplayName|
|dataExport.messages.received|Yes|Mottagna dataexportmeddelanden|Antal|Totalt|Antal meddelanden som inkommande till dataexport innan filtrering och berikning bearbetas|exportId, exportDisplayName, destinationId, destinationDisplayName|
|dataExport.messages.written|Yes|Dataexportmeddelanden som skrivits|Antal|Totalt|Antal meddelanden som skrivits till ett mål|exportId, exportDisplayName, destinationId, destinationDisplayName|
|dataExport.statusChange|Yes|Ändring av status för dataexport|Antal|Totalt|Antal statusändringar|exportId, exportDisplayName, destinationId, destinationDisplayName, status|
|deviceDataUsage|Yes|Total enhetsdataanvändning|Byte|Totalt|Byte som överförs till och från alla enheter som är anslutna IoT Central program|Inga dimensioner|
|provisionedDeviceCount|No|Totalt antal etablerade enheter|Antal|Genomsnitt|Antal enheter som etablerats i IoT Central program|Inga dimensioner|

## <a name="microsoftkeyvaultmanagedhsms"></a>microsoft.keyvault/managedhsms

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|No|Övergripande tjänsttillgänglighet|Procent|Genomsnitt|Tillgänglighet för tjänstbegäranden|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiHit|Yes|Totalt antal service-API-träffar|Antal|Antal|Antal totalt antal service-API-träffar|ActivityType, ActivityName|
|ServiceApiLatency|No|Övergripande svarstid för tjänst-API|Millisekunder|Genomsnitt|Övergripande svarstid för tjänst-API-begäranden|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Yes|Totalt antal service-API-resultat|Antal|Antal|Antal totalt antal service-API-resultat|ActivityType, ActivityName, StatusCode, StatusCodeClass|


## <a name="microsoftkeyvaultvaults"></a>Microsoft.KeyVault/vaults

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Yes|Övergripande valvtillgänglighet|Procent|Genomsnitt|Tillgänglighet för valvbegäranden|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|SaturationShoebox|No|Övergripande valvmättnad|Procent|Genomsnitt|Valvkapacitet som används|ActivityType, ActivityName, TransactionType|
|ServiceApiHit|Yes|Totalt antal service-API-träffar|Antal|Antal|Antal totalt antal service-API-träffar|ActivityType, ActivityName|
|ServiceApiLatency|Yes|Övergripande svarstid för tjänst-API|Millisekunder|Genomsnitt|Övergripande svarstid för tjänst-API-begäranden|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Yes|Totalt antal service-API-resultat|Antal|Antal|Antal totalt antal service-API-resultat|ActivityType, ActivityName, StatusCode, StatusCodeClass|


## <a name="microsoftkubernetesconnectedclusters"></a>microsoft.kubernetes/connectedClusters

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|capacity_cpu_cores|Yes|Totalt antal processorkärnor i ett anslutet kluster|Antal|Totalt|Totalt antal processorkärnor i ett anslutet kluster||


## <a name="microsoftkustoclusters"></a>Microsoft.Kusto/Clusters

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|BatchBlobCount|Yes|Antal Batch-blobar|Antal|Genomsnitt|Antal datakällor i en aggregerad batch för inmatning.|Databas|
|BatchDuration|Yes|Batchvaraktighet|Sekunder|Genomsnitt|Varaktigheten för aggregeringsfasen i inmatningsflödet.|Databas|
|BatchesProcessed|Yes|Bearbetade batchar|Antal|Totalt|Antal batchar som aggregerats för inmatning. Batchbearbetningstyp: om batchen nått batchtid, datastorlek eller antal filer som angetts av batchbearbetningsprincipen|Database, SealReason|
|BatchSize|Yes|Batchstorlek|Byte|Genomsnitt|Okomprimerad förväntad datastorlek i en aggregerad batch för inmatning.|Databas|
|BlobarDropped|Yes|Bort ignorerade blobar|Antal|Totalt|Antal blobar som avvisats permanent av en komponent.|Database, ComponentType, ComponentName|
|BlobarProcessed|Yes|Bearbetade blobar|Antal|Totalt|Antal blobar som bearbetas av en komponent.|Database, ComponentType, ComponentName|
|BlobarReceived|Yes|Mottagna blobar|Antal|Totalt|Antal blobar som tagits emot från indataströmmen av en komponent.|Database, ComponentType, ComponentName|
|CacheUtilization|Yes|Cacheanvändning|Procent|Genomsnitt|Användningsnivå i klusteromfånget|Inga dimensioner|
|ContinuousExportMaxLatenessMinutes|Yes|Maximal lateness för löpande export|Antal|Maximal|Den laten (i minuter) som rapporteras av de kontinuerliga exportjobben i klustret|Inga dimensioner|
|ContinuousExportNumOfRecordsExported|Yes|Löpande export – antal exporterade poster|Antal|Totalt|Antalet poster som exporteras, utades för varje lagringsartefakt som skrivits under exportåtgärden|ContinuousExportName, Database|
|ContinuousExportPendingCount|Yes|Antal väntande löpande exporter|Antal|Maximal|Antalet väntande kontinuerliga exportjobb som är redo för körning|Inga dimensioner|
|ContinuousExportResult|Yes|Resultat av löpande export|Antal|Antal|Anger om den löpande exporten lyckades eller misslyckades|ContinuousExportName, Result, Database|
|Processor|Yes|Processor|Procent|Genomsnitt|Processoranvändningsnivå|Inga dimensioner|
|DiscoveryLatency|Yes|Svarstid för identifiering|Sekunder|Genomsnitt|Rapporteras av dataanslutningar (om sådana finns). Tid i sekunder från det att ett meddelande står i enqueu eller händelse skapas tills det identifieras av dataanslutningen. Den här tiden ingår inte i Azure Data Explorer totala inmatningens varaktighet.|ComponentType, ComponentName|
|EventsDropped|Yes|Bort ignorerade händelser|Antal|Totalt|Antal händelser som tas bort permanent av dataanslutningen. Ett inmatningsresultatmått med en felorsak skickas.|ComponentType, ComponentName|
|EventsProcessed|Yes|Bearbetade händelser|Antal|Totalt|Antal händelser som bearbetas av klustret|ComponentType, ComponentName|
|EventsProcessedForEventHubs|Yes|Bearbetade händelser (för Event/IoT Hubs)|Antal|Totalt|Antal händelser som bearbetas av klustret vid inmatning från Händelse/IoT Hub|EventStatus|
|HändelserReceived|Yes|Mottagna händelser|Antal|Totalt|Antal händelser som tagits emot av dataanslutningen.|ComponentType, ComponentName|
|ExportUtilization|Yes|Exportanvändning|Procent|Maximal|Exportanvändning|Inga dimensioner|
|IngestionLatencyInSeconds|Yes|Datainmatningssvarstid|Sekunder|Genomsnitt|Svarstiden för inmatade data, från tiden då data togs emot i klustret tills att det går att köra frågor mot dem. Datainmatningssvarstiden beror på inmatningsscenariot.|Inga dimensioner|
|IngestionResult|Yes|Datainmatningsresultat|Antal|Totalt|Antal inmatningsåtgärder|IngestionResultDetails|
|IngestionUtilization|Yes|Datainmatningsanvändning|Procent|Genomsnitt|Förhållandet mellan använda inmatningsfack i klustret|Inga dimensioner|
|IngestionVolumeInMB|Yes|Datainmatningsvolym|Byte|Totalt|Övergripande volym av indata till klustret|Databas|
|InstanceCount|Yes|Antal instanser|Antal|Genomsnitt|Totalt antal instanser|Inga dimensioner|
|Keepalive|Yes|Håll dig vid liv|Antal|Genomsnitt|Sanity check (Sanitetskontroll) anger att klustret svarar på frågor|Inga dimensioner|
|MaterializedViewAgeMinutes|Yes|Materialiserad vyålder|Antal|Genomsnitt|Materialiserad vyålder i minuter|Database, MaterializedViewName|
|MaterializedViewDataLoss|Yes|Dataförlust för materialiserad vy|Antal|Maximal|Anger potentiell dataförlust i materialiserad vy|Database, MaterializedViewName, Kind|
|MaterializedViewExtentsRebuild|Yes|Återskapa materialiserad vy|Antal|Genomsnitt|Antal återskapning av omfattningar|Database, MaterializedViewName|
|MaterializedViewHealth|Yes|Hälsotillstånd för materialiserad vy|Antal|Genomsnitt|Hälsotillståndet för den materialiserade vyn (1 för felfri, 0 för icke-felfri)|Database, MaterializedViewName|
|MaterializedViewRecordsInDelta|Yes|Poster för materialiserad vy i Delta|Antal|Genomsnitt|Antalet poster i den icke-materialiserade delen av vyn|Database, MaterializedViewName|
|MaterializedViewResult|Yes|Resultat av materialiserad vy|Antal|Genomsnitt|Resultatet av materialiseringsprocessen|Database, MaterializedViewName, Result|
|QueryDuration|Yes|Frågevaraktighet|Millisekunder|Genomsnitt|Frågors varaktighet i sekunder|QueryStatus|
|QueryResult|No|Frågeresultat|Antal|Antal|Totalt antal frågor.|QueryStatus|
|QueueLength|Yes|Kölängd|Antal|Genomsnitt|Antal väntande meddelanden i en komponents kö.|ComponentType|
|QueueOldestMessage|Yes|Det äldsta kömeddelandet|Antal|Genomsnitt|Tid i sekunder från när det äldsta meddelandet i kön infogades.|ComponentType|
|ReceivedDataSizeBytes|Yes|Mottagna datastorleksbyte|Byte|Genomsnitt|Storleken på data som tas emot av dataanslutningen. Det här är storleken på dataströmmen eller storleken på rådata om det finns.|ComponentType, ComponentName|
|StageLatency|Yes|Fasfördröjning|Sekunder|Genomsnitt|Kumulativ tid från det att ett meddelande identifieras tills det tas emot av rapportkomponenten för bearbetning (identifieringstiden anges när meddelandet ställs i kö för inmatningskö eller när det identifieras av dataanslutningen).|Database, ComponentType|
|SteamingIngestRequestRate|Yes|Förfrågningsfrekvens för strömningsinmatning|Antal|RateRequestsPerSecond|Begärandefrekvens för strömningsinmatning (begäranden per sekund)|Inga dimensioner|
|StreamingIngestDataRate|Yes|Datahastighet för strömningsinmatning|Antal|Genomsnitt|Datahastighet för strömningsinmatning (MB per sekund)|Inga dimensioner|
|StreamingIngestDuration|Yes|Varaktighet för strömningsinmatning|Millisekunder|Genomsnitt|Varaktighet för strömningsinmatning i millisekunder|Inga dimensioner|
|StreamingIngestResults|Yes|Resultat för strömningsinmatning|Antal|Genomsnitt|Resultat av strömningsinmatning|Resultat|
|TotalNumberOfConcurrentQueries|Yes|Totalt antal samtidiga frågor|Antal|Maximal|Totalt antal samtidiga frågor|Inga dimensioner|
|TotalNumberOfExtents|Yes|Totalt antal omfattningar|Antal|Totalt|Totalt antal datakällor|Inga dimensioner|
|TotalNumberOfThrottledCommands|Yes|Totalt antal begränsade kommandon|Antal|Totalt|Totalt antal begränsade kommandon|CommandType|
|TotalNumberOfThrottledQueries|Yes|Totalt antal begränsade frågor|Antal|Maximal|Totalt antal begränsade frågor|Inga dimensioner|


## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft.Logic/integrationServiceEnvironments

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ActionLatency|Yes|Åtgärdssvarstid |Sekunder|Genomsnitt|Svarstid för slutförda arbetsflödesåtgärder.|Inga dimensioner|
|ÅtgärderCompleted|Yes|Slutförda åtgärder |Antal|Totalt|Antal slutförda arbetsflödesåtgärder.|Inga dimensioner|
|ActionsFailed|Yes|Åtgärder misslyckades |Antal|Totalt|Antalet misslyckade arbetsflödesåtgärder.|Inga dimensioner|
|ActionsSkipped|Yes|Överhoppade åtgärder |Antal|Totalt|Antal överhoppade arbetsflödesåtgärder.|Inga dimensioner|
|ÅtgärderStartad|Yes|Åtgärder har startats |Antal|Totalt|Antal arbetsflödesåtgärder som har startats.|Inga dimensioner|
|ÅtgärderSucceeded|Yes|Åtgärder som har lyckats |Antal|Totalt|Antalet arbetsflödesåtgärder som har lyckats.|Inga dimensioner|
|ActionSuccessLatency|Yes|Svarstid för lyckad åtgärd |Sekunder|Genomsnitt|Svarstiden för de åtgärder som har lyckats med arbetsflödet.|Inga dimensioner|
|ActionThrottledEvents|Yes|Åtgärdsbegränsningar|Antal|Totalt|Antal begränsade händelser för arbetsflödesåtgärd..|Inga dimensioner|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Yes|Minnesanvändning för anslutning för Integration Service Environment|Procent|Genomsnitt|Minnesanvändning för anslutningstjänsten för integreringstjänstmiljön.|Inga dimensioner|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Yes|Användning av anslutningsprocessor för Integration Service Environment|Procent|Genomsnitt|Användning av anslutningsprocessorer för integreringstjänstmiljön.|Inga dimensioner|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Yes|Minnesanvändning för arbetsflöde för Integration Service Environment|Procent|Genomsnitt|Minnesanvändning för arbetsflöden för integreringstjänstmiljön.|Inga dimensioner|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Yes|Användning av arbetsflödesprocessor för Integration Service Environment|Procent|Genomsnitt|Processoranvändning för arbetsflöden för integreringstjänstmiljön.|Inga dimensioner|
|RunFailurePercentage|Yes|Procentandel körningsfel|Procent|Totalt|Procentandel misslyckade arbetsflödeskörningar.|Inga dimensioner|
|RunLatency|Yes|Körningssvarstid|Sekunder|Genomsnitt|Svarstiden för slutförda arbetsflödeskörningar.|Inga dimensioner|
|RunsCancelled|Yes|Körningar avbröts|Antal|Totalt|Antalet avbrutna arbetsflödeskörningar.|Inga dimensioner|
|RunsCompleted|Yes|Slutförda körningar|Antal|Totalt|Antalet slutförda arbetsflödeskörningar.|Inga dimensioner|
|RunsFailed|Yes|Körningar misslyckades|Antal|Totalt|Antalet misslyckade arbetsflödeskörningar.|Inga dimensioner|
|KörningarStartad|Yes|Körningar har startats|Antal|Totalt|Antalet startade arbetsflödeskörningar.|Inga dimensioner|
|RunsSucceeded|Yes|Körningarna lyckades|Antal|Totalt|Antalet arbetsflödeskörningar som har lyckats.|Inga dimensioner|
|RunStartThrottledEvents|Yes|Köra begränsade händelser för körning|Antal|Totalt|Antal startbegränsningar för arbetsflödeskörningar.|Inga dimensioner|
|RunSuccessLatency|Yes|Svarstid för lyckad körning|Sekunder|Genomsnitt|Svarstiden för körningar av arbetsflöden som har lyckats.|Inga dimensioner|
|RunThrottledEvents|Yes|Köra begränsade händelser|Antal|Totalt|Antal arbetsflödesåtgärder eller begränsade händelser som utlöses.|Inga dimensioner|
|TriggerFireLatency|Yes|Svarstid för utlösare |Sekunder|Genomsnitt|Svarstid för utlösta arbetsflödesutlösare.|Inga dimensioner|
|TriggerLatency|Yes|Svarstid för utlösare |Sekunder|Genomsnitt|Svarstid för slutförda arbetsflödesutlösare.|Inga dimensioner|
|UtlösareCompleted|Yes|Utlösare har slutförts |Antal|Totalt|Antalet slutförda arbetsflödesutlösare.|Inga dimensioner|
|TriggersFailed|Yes|Utlösare misslyckades |Antal|Totalt|Antalet misslyckade arbetsflödesutlösare.|Inga dimensioner|
|UtlösareFired|Yes|Utlösta utlösare |Antal|Totalt|Antal utlösta arbetsflödesutlösare.|Inga dimensioner|
|UtlösareSkipped|Yes|Överhoppade utlösare|Antal|Totalt|Antal överhoppade arbetsflödesutlösare.|Inga dimensioner|
|UtlösareStartad|Yes|Utlösare har startats |Antal|Totalt|Antalet arbetsflödesutlösare som startats.|Inga dimensioner|
|UtlösareSucceeded|Yes|Utlösare lyckades |Antal|Totalt|Antalet arbetsflödesutlösare lyckades.|Inga dimensioner|
|TriggerSuccessLatency|Yes|Svarstid för lyckad utlösare |Sekunder|Genomsnitt|Svarstiden för lyckades arbetsflödesutlösare.|Inga dimensioner|
|TriggerThrottledEvents|Yes|Begränsade händelser för utlösare|Antal|Totalt|Antalet begränsade händelser för arbetsflödesutlösare.|Inga dimensioner|


## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ActionLatency|Yes|Åtgärdssvarstid |Sekunder|Genomsnitt|Svarstid för slutförda arbetsflödesåtgärder.|Inga dimensioner|
|ÅtgärderSlutförd|Yes|Slutförda åtgärder |Antal|Totalt|Antal slutförda arbetsflödesåtgärder.|Inga dimensioner|
|ActionsFailed|Yes|Åtgärder misslyckades |Antal|Totalt|Antalet misslyckade arbetsflödesåtgärder.|Inga dimensioner|
|ActionsSkipped|Yes|Överhoppade åtgärder |Antal|Totalt|Antal arbetsflödesåtgärder som hoppas över.|Inga dimensioner|
|ÅtgärderStartad|Yes|Åtgärder har startats |Antal|Totalt|Antal arbetsflödesåtgärder som har startats.|Inga dimensioner|
|ÅtgärderSucceeded|Yes|Åtgärder som har lyckats |Antal|Totalt|Antalet arbetsflödesåtgärder som har lyckats.|Inga dimensioner|
|ActionSuccessLatency|Yes|Svarstid för lyckad åtgärd |Sekunder|Genomsnitt|Svarstiden för de åtgärder som har lyckats med arbetsflödet.|Inga dimensioner|
|ActionThrottledEvents|Yes|Händelser som begränsas av åtgärder|Antal|Totalt|Antal begränsade händelser i arbetsflödesåtgärden.|Inga dimensioner|
|BillableActionExecutions|Yes|Fakturerbara åtgärdskörningar|Antal|Totalt|Antal arbetsflödesåtgärdskörningar som debiteras.|Inga dimensioner|
|BillableTriggerExecutions|Yes|Fakturerbara utlösarkörningar|Antal|Totalt|Antal körningar av arbetsflödesutlösare som debiteras.|Inga dimensioner|
|BillingUsageNativeOperation|Yes|Faktureringsanvändning för interna åtgärdskörningar|Antal|Totalt|Antal interna åtgärdskörningar som debiteras.|Inga dimensioner|
|BillingUsageStandardConnector|Yes|Faktureringsanvändning för Standard Connector-körningar|Antal|Totalt|Antal standardkörningar av anslutningsappar som debiteras.|Inga dimensioner|
|BillingUsageStorageConsumption|Yes|Faktureringsanvändning för lagringsförbrukningskörningar|Antal|Totalt|Antal körningar av lagringsförbrukning som debiteras.|Inga dimensioner|
|RunFailurePercentage|Yes|Procentandel körningsfel|Procent|Totalt|Procentandelen misslyckade arbetsflödeskörningar.|Inga dimensioner|
|RunLatency|Yes|Körningssvarstid|Sekunder|Genomsnitt|Svarstiden för slutförda arbetsflödeskörningar.|Inga dimensioner|
|RunsCancelled|Yes|Körningar avbröts|Antal|Totalt|Antalet avbrutna arbetsflödeskörningar.|Inga dimensioner|
|RunsCompleted|Yes|Slutförda körningar|Antal|Totalt|Antalet slutförda arbetsflödeskörningar.|Inga dimensioner|
|RunsFailed|Yes|Körningar misslyckades|Antal|Totalt|Antalet misslyckade arbetsflödeskörningar.|Inga dimensioner|
|KörningarStartat|Yes|Körningar har startats|Antal|Totalt|Antalet arbetsflödeskörningar som startats.|Inga dimensioner|
|RunsSucceeded|Yes|Körningarna lyckades|Antal|Totalt|Antalet arbetsflödeskörningar som har lyckats.|Inga dimensioner|
|RunStartThrottledEvents|Yes|Köra begränsade händelser för körning|Antal|Totalt|Antalet starthändelser som begränsas av arbetsflödeskörningen.|Inga dimensioner|
|RunSuccessLatency|Yes|Svarstid för lyckad körning|Sekunder|Genomsnitt|Svarstiden för körningar av lyckades arbetsflöden.|Inga dimensioner|
|RunThrottledEvents|Yes|Köra begränsade händelser|Antal|Totalt|Antal arbetsflödesåtgärder eller begränsade händelser som utlöses.|Inga dimensioner|
|TotalBillableExecutions|Yes|Totalt antal fakturerbara körningar|Antal|Totalt|Antal arbetsflödeskörningar som debiteras.|Inga dimensioner|
|TriggerFireLatency|Yes|Utlösarbrandfördröjning |Sekunder|Genomsnitt|Svarstiden för utlösta arbetsflödesutlösare.|Inga dimensioner|
|TriggerLatency|Yes|Svarstid för utlösare |Sekunder|Genomsnitt|Svarstid för slutförda arbetsflödesutlösare.|Inga dimensioner|
|UtlösareCompleted|Yes|Utlösare har slutförts |Antal|Totalt|Antalet slutförda arbetsflödesutlösare.|Inga dimensioner|
|TriggersFailed|Yes|Utlösare misslyckades |Antal|Totalt|Antalet misslyckade arbetsflödesutlösare.|Inga dimensioner|
|UtlösareFired|Yes|Utlösta utlösare |Antal|Totalt|Antal utlösta arbetsflödesutlösare.|Inga dimensioner|
|UtlösareSkipped|Yes|Överhoppade utlösare|Antal|Totalt|Antal överhoppade arbetsflödesutlösare.|Inga dimensioner|
|UtlösareStartat|Yes|Utlösare har startats |Antal|Totalt|Antal arbetsflödesutlösare som startats.|Inga dimensioner|
|UtlösareSucceeded|Yes|Utlösare lyckades |Antal|Totalt|Antalet arbetsflödesutlösare lyckades.|Inga dimensioner|
|TriggerSuccessLatency|Yes|Svarstid för lyckad utlösare |Sekunder|Genomsnitt|Svarstiden för lyckades arbetsflödesutlösare.|Inga dimensioner|
|TriggerThrottledEvents|Yes|Begränsade händelser för utlösare|Antal|Totalt|Antalet begränsade händelser för arbetsflödesutlösare.|Inga dimensioner|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft.MachineLearningServices/workspaces

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Aktiva kärnor|Yes|Aktiva kärnor|Antal|Genomsnitt|Antal aktiva kärnor|Scenario, ClusterName|
|Aktiva noder|Yes|Aktiva noder|Antal|Genomsnitt|Antal acitve-noder. Det här är noderna som aktivt kör ett jobb.|Scenario, ClusterName|
|Avbryt begärda körningar|Yes|Avbryt begärda körningar|Antal|Totalt|Antal körningar där avbryt har begärts för den här arbetsytan. Antalet uppdateras när en annulleringsbegäran har tagits emot för en körning.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Avbokningskörningar|Yes|Avbokningskörningar|Antal|Totalt|Antal körningar som har avbrutits för den här arbetsytan. Antalet uppdateras när en körning har avbrutits.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Slutförda körningar|Yes|Slutförda körningar|Antal|Totalt|Antalet körningar som har slutförts för den här arbetsytan. Antalet uppdateras när en körning har slutförts och utdata har samlats in.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|CpuUtilization|Yes|CpuUtilization|Antal|Genomsnitt|Procentandel av användningen på en CPU-nod. Användningen rapporteras med en minuts intervall.|Scenario, runId, NodeId, ClusterName|
|Fel|Yes|Fel|Antal|Totalt|Antal körningsfel på den här arbetsytan. Antalet uppdateras när körningen påträffar ett fel.|Scenario|
|Misslyckade körningar|Yes|Misslyckade körningar|Antal|Totalt|Antalet körningar misslyckades för den här arbetsytan. Antalet uppdateras när en körning misslyckas.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Slutföra körningar|Yes|Slutföra körningar|Antal|Totalt|Antalet körningar som har angetts som slutförandetillstånd för den här arbetsytan. Antalet uppdateras när en körning har slutförts men utdatasamlingen fortfarande pågår.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|GpuMemoryUtilization|Yes|GpuMemoryUtilization|Antal|Genomsnitt|Procentandel minnesanvändning på en GPU-nod. Användningen rapporteras med ett minutintervall.|Scenario, runId, NodeId, DeviceId, ClusterName|
|GpuUtilization|Yes|GpuUtilization|Antal|Genomsnitt|Utnyttjandeprocent på en GPU-nod. Användningen rapporteras med ett minutintervall.|Scenario, runId, NodeId, DeviceId, ClusterName|
|Inaktiva kärnor|Yes|Inaktiva kärnor|Antal|Genomsnitt|Antal inaktiva kärnor|Scenario, ClusterName|
|Inaktiva noder|Yes|Inaktiva noder|Antal|Genomsnitt|Antal inaktiva noder. Inaktiva noder är noder som inte kör några jobb men som kan ta emot nya jobb om det är tillgängligt.|Scenario, ClusterName|
|Lämna kärnor|Yes|Lämna kärnor|Antal|Genomsnitt|Antal lämnande kärnor|Scenario, ClusterName|
|Lämna noder|Yes|Lämna noder|Antal|Genomsnitt|Antal noder som lämnar. Noder som lämnar är de noder som precis har slutfört bearbetningen av ett jobb och som går till inaktivt tillstånd.|Scenario, ClusterName|
|Det gick inte att distribuera modellen|Yes|Det gick inte att distribuera modellen|Antal|Totalt|Antal modelldistributioner som misslyckades på den här arbetsytan|Scenario, StatusCode|
|Modell-distributionen har startats|Yes|Modell-distributionen har startats|Antal|Totalt|Antalet modelldistributioner som startats på den här arbetsytan|Scenario|
|Modellde distribuera lyckades|Yes|Modellde distribuera lyckades|Antal|Totalt|Antal modelldistributioner som lyckades på den här arbetsytan|Scenario|
|Modellregistret misslyckades|Yes|Modellregistret misslyckades|Antal|Totalt|Antal modellregistreringar som misslyckades på den här arbetsytan|Scenario, StatusCode|
|Modellregistret lyckades|Yes|Modellregistret lyckades|Antal|Totalt|Antal modellregistreringar som lyckades på den här arbetsytan|Scenario|
|Svarar inte körningar|Yes|Svarar inte körningar|Antal|Totalt|Antalet körningar som inte svarar för den här arbetsytan. Antalet uppdateras när en körning förlöper i tillståndet Svarar inte.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Inte startad körningar|Yes|Inte startad körningar|Antal|Totalt|Antalet körningar i tillståndet Inte startad för den här arbetsytan. Antalet uppdateras när en begäran tas emot för att skapa en körning, men körningsinformationen har ännu inte fyllts i. |Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Avinstallerade kärnor|Yes|Avinstallerade kärnor|Antal|Genomsnitt|Antal avindelade kärnor|Scenario, ClusterName|
|Avindelade noder|Yes|Avindelade noder|Antal|Genomsnitt|Antal avindelade noder. Dessa noder är noder med låg prioritet som tas bort från den tillgängliga nodpoolen.|Scenario, ClusterName|
|Förbereda körningar|Yes|Förbereda körningar|Antal|Totalt|Antal körningar som förbereder för den här arbetsytan. Antal uppdateras när en körning förbereder statusen medan körningsmiljön förbereds.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Etableringskörningar|Yes|Etableringskörningar|Antal|Totalt|Antal körningar som etableras för den här arbetsytan. Antalet uppdateras när en körning väntar på skapande eller etablering av beräkningsmål.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Köade körningar|Yes|Köade körningar|Antal|Totalt|Antal körningar i kö för den här arbetsytan. Antalet uppdateras när en körning köas i beräkningsmålet. Kan inträffa när du väntar på att nödvändiga beräkningsnoder ska vara redo.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Kvotanvändning i procent|Yes|Kvotanvändning i procent|Antal|Genomsnitt|Procent av utnyttjad kvot|Scenario, ClusterName, VmFamilyName, VmPriority|
|Startade körningar|Yes|Startade körningar|Antal|Totalt|Antal körningar som körs för den här arbetsytan. Antalet uppdateras när körningen börjar köras på nödvändiga resurser.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Starta körningar|Yes|Starta körningar|Antal|Totalt|Antalet körningar som har startats för den här arbetsytan. Antalet uppdateras när begäran om att skapa körnings- och körningsinformation, till exempel Körnings-ID, har fyllts i|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Totalt antal kärnor|Yes|Totalt antal kärnor|Antal|Genomsnitt|Antal totalt antal kärnor|Scenario, ClusterName|
|Totalt antal noder|Yes|Totalt antal noder|Antal|Genomsnitt|Antal totalt antal noder. Den här summan inkluderar några aktiva noder, inaktiva noder, oanvändbara noder, premeptade noder, lämna noder|Scenario, ClusterName|
|Oanvändbara kärnor|Yes|Oanvändbara kärnor|Antal|Genomsnitt|Antal oanvändbara kärnor|Scenario, ClusterName|
|Oanvändbara noder|Yes|Oanvändbara noder|Antal|Genomsnitt|Antal oanvändbara noder. Oanvändbara noder fungerar inte på grund av ett olöst problem. Azure återanvänder dessa noder.|Scenario, ClusterName|
|Varningar|Yes|Varningar|Antal|Totalt|Antal körningsvarningar på den här arbetsytan. Antalet uppdateras när en körning påträffar en varning.|Scenario|


## <a name="microsoftmapsaccounts"></a>Microsoft.Maps/accounts

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Yes|Tillgänglighet|Procent|Genomsnitt|Tillgänglighet för API:erna|ApiCategory, ApiName|
|Användning|No|Användning|Antal|Antal|Antal API-anrop|ApiCategory, ApiName, ResultType, ResponseCode|


## <a name="microsoftmediamediaservices"></a>Microsoft.Media/mediaservices

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|AssetCount|Yes|Antal tillgångar|Antal|Genomsnitt|Hur många tillgångar som redan har skapats i det aktuella mediatjänstkontot|Inga dimensioner|
|AssetQuota|Yes|Tillgångskvot|Antal|Genomsnitt|Hur många tillgångar som tillåts för aktuellt mediatjänstkonto|Inga dimensioner|
|AssetQuotaUsedPercentage|Yes|Procentandel använd tillgångskvot|Procent|Genomsnitt|Tillgång som används i procent i aktuellt mediatjänstkonto|Inga dimensioner|
|ChannelsAndLiveEventsCount|Yes|Antal live-händelser|Antal|Genomsnitt|Det totala antalet livehändelser i det aktuella Media Services-kontot|Inga dimensioner|
|ContentKeyPolicyCount|Yes|Antal innehållsnyckelprinciper|Antal|Genomsnitt|Hur många innehållsnyckelprinciper som redan har skapats i det aktuella mediatjänstkontot|Inga dimensioner|
|ContentKeyPolicyQuota|Yes|Kvot för innehållsnyckelprincip|Antal|Genomsnitt|Hur många innehållsnyckel-polices tillåts för det aktuella mediatjänstkontot|Inga dimensioner|
|ContentKeyPolicyQuotaUsedPercentage|Yes|Kvot för innehållsnyckelprincip som används i procent|Procent|Genomsnitt|Princip för innehållsnyckel som används i procent i det aktuella mediatjänstkontot|Inga dimensioner|
|MaxChannelsAndLiveEventsCount|Yes|Maximal kvot för livehändelse|Antal|Maximal|Det maximala antalet livehändelser som tillåts i det aktuella Media Services-kontot|Inga dimensioner|
|MaxRunningChannelsAndLiveEventsCount|Yes|Maximal kvot för livehändelse som körs|Antal|Maximal|Det maximala antalet livehändelser som tillåts i det aktuella Media Services-kontot|Inga dimensioner|
|RunningChannelsAndLiveEventsCount|Yes|Antal live-händelser som körs|Antal|Genomsnitt|Det totala antalet livehändelser som körs i det aktuella Media Services-kontot|Inga dimensioner|
|StreamingPolicyCount|Yes|Antal direktuppspelningsprinciper|Antal|Genomsnitt|Hur många strömningsprinciper som redan har skapats i det aktuella mediatjänstkontot|Inga dimensioner|
|StreamingPolicyQuota|Yes|Kvot för direktuppspelningsprincip|Antal|Genomsnitt|Hur många strömningsprinciper som tillåts för det aktuella mediatjänstkontot|Inga dimensioner|
|StreamingPolicyQuotaUsedPercentage|Yes|Kvot för direktuppspelningsprincip som används i procent|Procent|Genomsnitt|Direktuppspelningsprincip som används i procent i aktuellt mediatjänstkonto|Inga dimensioner|


## <a name="microsoftmediamediaservicesliveevents"></a>Microsoft.Media/mediaservices/liveEvents

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|IngestBitrate|Yes|Bithastighet för inmatning av livehändelse|BitsPerSecond|Genomsnitt|Den inkommande bithastighet som matas in för en livehändelse, i bitar per sekund.|TrackName|
|IngestDriftValue|Yes|Inmatningsavdriftsvärde för livehändelse|Sekunder|Maximal|Drift mellan tidsstämpeln för det in matade innehållet och systemklockan, mätt i sekunder per minut. Ett värde som inte är noll anger att det inkommande innehållet anländer långsammare än systemklockan.|TrackName|
|IngestLastTimestamp|Yes|Inmatning av livehändelse senaste tidsstämpel|Millisekunder|Maximal|Senaste tidsstämpel som matats in för en livehändelse.|TrackName|
|LiveOutputLastTimestamp|Yes|Tidsstämpel för senaste utdata|Millisekunder|Maximal|Tidsstämpel för det senaste fragmentet som laddats upp till lagring för livehändelseutdata.|TrackName|


## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft.Media/mediaservices/streamingEndpoints

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Processor|Yes|CPU-användning|Procent|Genomsnitt|CPU-användning för premium-slutpunkter för direktuppspelning. Dessa data är inte tillgängliga för standardslutpunkter för direktuppspelning.|Inga dimensioner|
|Utgående|Yes|Utgående|Byte|Totalt|Mängden utgående data, i byte.|OutputFormat|
|EgressBandwidth|No|Utgående bandbredd|BitsPerSecond|Genomsnitt|Utgående bandbredd i bitar per sekund.|Inga dimensioner|
|Begäranden|Yes|Begäranden|Antal|Totalt|Begäranden till en slutpunkt för direktuppspelning.|OutputFormat, HttpStatusCode, ErrorCode|
|SuccessE2ELatency|Yes|Lyckad svarstid från slut till slut|Millisekunder|Genomsnitt|Den genomsnittliga svarstiden för lyckade begäranden i millisekunder.|OutputFormat|


## <a name="microsoftmixedrealityremoterenderingaccounts"></a>Microsoft.MixedReality/remoteRenderingAccounts

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ActiveRenderingSessions|Yes|Aktiva renderingssessioner|Antal|Genomsnitt|Totalt antal aktiva renderingssessioner|SessionType, SDKVersion|
|TillgångarKonverterad|Yes|Konverterade tillgångar|Antal|Totalt|Totalt antal konverterade tillgångar|SDKVersion|


## <a name="microsoftmixedrealityspatialanchorsaccounts"></a>Microsoft.MixedReality/spatialAnchorsAccounts

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|AnchorsCreated|Yes|Fästpunkter har skapats|Antal|Totalt|Antal ankare som skapats|DeviceFamily, SDKVersion|
|AnchorsDeleted|Yes|Borttagna fästpunkter|Antal|Totalt|Antal borttagna fästpunkter|DeviceFamily, SDKVersion|
|AnchorsQueried|Yes|Ankare efterfrågas|Antal|Totalt|Antal Spatial Anchors frågor|DeviceFamily, SDKVersion|
|AnchorsUpdated|Yes|Ankare har uppdaterats|Antal|Totalt|Antalet uppdaterade fästpunkter|DeviceFamily, SDKVersion|
|PosesFound|Yes|Poser hittades|Antal|Totalt|Antal poser som returneras|DeviceFamily, SDKVersion|
|TotalDailyAnchors|Yes|Totalt antal dagliga fästpunkter|Antal|Genomsnitt|Totalt antal fästpunkter – varje dag|DeviceFamily, SDKVersion|


## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft.NetApp/netAppAccounts/capacityPools

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Yes|Allokerad storlek för pool|Byte|Genomsnitt|Den här poolens etablerade storlek|Inga dimensioner|
|VolumePoolAllocatedToVolumeThroughput|Yes|Allokerat dataflöde för pool|BytesPerSecond|Genomsnitt|Summan av dataflödet för alla volymer som tillhör poolen|Inga dimensioner|
|VolumePoolAllocatedUsed|Yes|Pool allokerad till volymstorlek|Byte|Genomsnitt|Allokerad använd storlek för poolen|Inga dimensioner|
|VolumePoolProvisionedThroughput|Yes|Etablerat dataflöde för poolen|BytesPerSecond|Genomsnitt|Etablerat dataflöde för den här poolen|Inga dimensioner|
|VolumePoolTotalLogicalSize|Yes|Förbrukad poolstorlek|Byte|Genomsnitt|Summan av den logiska storleken för alla volymer som hör till poolen|Inga dimensioner|
|VolumePoolTotalSnapshotSize|Yes|Total storlek på ögonblicksbilder för poolen|Byte|Genomsnitt|Summan av ögonblicksbildstorleken för alla volymer i den här poolen|Inga dimensioner|


## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft.NetApp/netAppAccounts/capacityPools/volumes

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|AverageReadLatency|Yes|Genomsnittlig lässvarstid|Millisekunder|Genomsnitt|Genomsnittlig lässvarstid i millisekunder per åtgärd|Inga dimensioner|
|AverageWriteLatency|Yes|Genomsnittlig skrivfördröjning|Millisekunder|Genomsnitt|Genomsnittlig skrivfördröjning i millisekunder per åtgärd|Inga dimensioner|
|CbsVolumeBackupActive|Yes|Har säkerhetskopiering av volym inaktiverats|Antal|Genomsnitt|Pausas säkerhetskopieringsprincipen för volymen? 0 om ja, 1 om nej.|Inga dimensioner|
|CbsVolumeLogicalBackupBytes|Yes|Byte för volymsäkerhetskopiering|Byte|Genomsnitt|Totalt antal byte som säkerhetskopierats för den här volymen.|Inga dimensioner|
|CbsVolumeOperationComplete|Yes|Åtgärden för volymsäkerhetskopiering har slutförts|Antal|Genomsnitt|Slutfördes den senaste säkerhetskopieringen eller återställningen av volymen? 1 om ja, 0 om nej.|Inga dimensioner|
|CbsVolumeOperationTransferredBytes|Yes|Senast överförda byte för volymsäkerhetskopiering|Byte|Genomsnitt|Totalt antal byte som överförts för senaste säkerhetskopierings- eller återställningsåtgärd.|Inga dimensioner|
|CbsVolumeProtected|Yes|Är volymsäkerhetskopiering aktiverat|Antal|Genomsnitt|Är säkerhetskopiering aktiverat för volymen? 1 om ja, 0 om nej.|Inga dimensioner|
|AnnatGenomflöde|Yes|Annat dataflöde|BytePerSecond|Genomsnitt|Annat dataflöde (som inte är läsning eller skrivning) i byte per sekund|Inga dimensioner|
|ReadIops|Yes|Läsa iops|CountPerSecond|Genomsnitt|Läs in/ut-åtgärder per sekund|Inga dimensioner|
|ReadThroughput|Yes|Läs dataflöde|BytePerSecond|Genomsnitt|Läsa dataflöde i byte per sekund|Inga dimensioner|
|TotalThroughput|Yes|Totalt dataflöde|BytePerSecond|Genomsnitt|Summan av allt dataflöde i byte per sekund|Inga dimensioner|
|VolumeAllocatedSize|Yes|Allokerad volymstorlek|Byte|Genomsnitt|Den etablerade storleken på en volym|Inga dimensioner|
|VolumeConsumedSizePercentage|Yes|Förbrukningsstorlek för procentvolym|Procent|Genomsnitt|Procentandelen av den förbrukade volymen, inklusive ögonblicksbilder.|Inga dimensioner|
|VolumeLogicalSize|Yes|Volymförbrukningsstorlek|Byte|Genomsnitt|Volymens logiska storlek (använda byte)|Inga dimensioner|
|VolumeSnapshotSize|Yes|Storlek på volymögonblicksbild|Byte|Genomsnitt|Storleken på alla ögonblicksbilder i volym|Inga dimensioner|
|WriteIops|Yes|Skriva iops|CountPerSecond|Genomsnitt|Skriva in/ut-åtgärder per sekund|Inga dimensioner|
|WriteThroughput|Yes|Skriva dataflöde|BytePerSecond|Genomsnitt|Skriva dataflöde i byte per sekund|Inga dimensioner|
|XregionReplicationHealthy|Yes|Är volymreplikeringsstatus felfri|Antal|Genomsnitt|Villkor för relationen, 1 eller 0.|Inga dimensioner|
|XregionReplicationLagTime|Yes|Fördröjning för volymreplikering|Sekunder|Genomsnitt|Hur lång tid i sekunder som data på speglingen ligger efter källan.|Inga dimensioner|
|XregionReplicationLastTransferDuration|Yes|Varaktighet för volymreplikering vid senaste överföring|Sekunder|Genomsnitt|Hur lång tid i sekunder det tog för den senaste överföringen att slutföras.|Inga dimensioner|
|XregionReplicationLastTransferSize|Yes|Volymreplikering, senaste överföringsstorlek|Byte|Genomsnitt|Det totala antalet byte som överförts som en del av den senaste överföringen.|Inga dimensioner|
|XregionReplicationRelationshipProgress|Yes|Förlopp för volymreplikering|Byte|Genomsnitt|Total mängd data som överförts för den aktuella överföringsåtgärden.|Inga dimensioner|
|XregionReplicationRelationship Transfer|Yes|Överför volymreplikering|Antal|Genomsnitt|Om statusen för volymreplikeringen är "överföring".|Inga dimensioner|
|XregionReplicationTotalTransferBytes|Yes|Total överföring av volymreplikering|Byte|Genomsnitt|Kumulativa byte som överförts för relationen.|Inga dimensioner|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ApplicationGatewayTotalTime|No|Application Gateway total tid|Millisekunder|Genomsnitt|Genomsnittlig tid det tar för en begäran att bearbetas och dess svar skickas. Detta beräknas som medelvärdet av intervallet från den tidpunkt då Application Gateway tar emot den första byten av en HTTP-begäran till den tidpunkt då svarsåtgärden slutförs. Det är viktigt att observera att detta vanligtvis omfattar Application Gateway bearbetningstid, tid då begäran- och svarspaketen skickas via nätverket och den tid det tog för backend-servern att svara.|Lyssnare|
|AvgRequestCountPerHealthyHost|No|Begäranden per minut per felfri värd|Antal|Genomsnitt|Genomsnittligt antal förfrågningar per minut per felfri backend-värd i en pool|BackendSettingsPool|
|BackendConnectTime|No|Anslutningstid för backend|Millisekunder|Genomsnitt|Tid som ägnats åt att upprätta en anslutning till en server|Listener, BackendServer, BackendPool, BackendHttpSetting|
|BackendFirstByteResponseTime|No|Svarstid för första byte för backend|Millisekunder|Genomsnitt|Tidsintervallet mellan början av upprättandet av en anslutning till backend-servern och mottagandet av den första byten i svarshuvudet, ungefär bearbetningstiden för backend-servern|Listener, BackendServer, BackendPool, BackendHttpSetting|
|BackendLastByteResponseTime|No|Svarstid för sista byte för backend|Millisekunder|Genomsnitt|Tidsintervall mellan början av upprättandet av en anslutning till serverdelen och mottagandet av den sista byten i svarstexten|Listener, BackendServer, BackendPool, BackendHttpSetting|
|BackendResponseStatus|Yes|Svarsstatus för backend|Antal|Totalt|Antalet HTTP-svarskoder som genereras av backend-medlemmarna. Detta inkluderar inte några svarskoder som genereras av Application Gateway.|BackendServer, BackendPool, BackendHttpSetting, HttpStatusGroup|
|BlockedCount|Yes|Web Application Firewall regeldistribution av blockerade begäranden|Antal|Totalt|Web Application Firewall regeldistribution av blockerade begäranden|RuleGroup, RuleId|
|BlockedReqCount|Yes|antal Web Application Firewall blockerade begäranden|Antal|Totalt|Web Application Firewall antal blockerade begäranden|Inga dimensioner|
|ByteReceived|Yes|Mottagna byte|Byte|Totalt|Det totala antalet byte som tagits emot av Application Gateway från klienterna|Lyssnare|
|ByteSent|Yes|Skickade byte|Byte|Totalt|Det totala antalet byte som skickats av Application Gateway till klienterna|Lyssnare|
|CapacityUnits|No|Aktuella kapacitetsenheter|Antal|Genomsnitt|Förbrukade kapacitetsenheter|Inga dimensioner|
|ClientRtt|No|Klient-RTT|Millisekunder|Genomsnitt|Genomsnittlig tur och retur-tid mellan klienter och Application Gateway. Det här måttet anger hur lång tid det tar att upprätta anslutningar och bekräfta returer|Lyssnare|
|ComputeUnits|No|Aktuella beräkningsenheter|Antal|Genomsnitt|Förbrukade beräkningsenheter|Inga dimensioner|
|CpuUtilization|No|CPU-användning|Procent|Genomsnitt|Aktuell processoranvändning för Application Gateway|Inga dimensioner|
|CurrentConnections|Yes|Aktuella anslutningar|Antal|Totalt|Antal aktuella anslutningar som upprättats med Application Gateway|Inga dimensioner|
|EstimatedBilledCapacityUnits|No|Uppskattade fakturerade kapacitetsenheter|Antal|Genomsnitt|Uppskattade kapacitetsenheter som kommer att debiteras|Inga dimensioner|
|FailedRequests|Yes|Misslyckade begäranden|Antal|Totalt|Antal misslyckade begäranden som Application Gateway har betjänat|BackendSettingsPool|
|FixedBillableCapacityUnits|No|Fasta fakturerbara kapacitetsenheter|Antal|Genomsnitt|Minsta kapacitetsenheter som debiteras|Inga dimensioner|
|HealthyHostCount|Yes|Antal felfria värdar|Antal|Genomsnitt|Antal felfria värdar i serverdelen|BackendSettingsPool|
|MatchedCount|Yes|Web Application Firewall total regelfördelning|Antal|Totalt|Web Application Firewall total regelfördelning för inkommande trafik|RuleGroup, RuleId|
|NewConnectionsPerSecond|No|Nya anslutningar per sekund|CountPerSecond|Genomsnitt|Nya anslutningar per sekund upprättas med Application Gateway|Inga dimensioner|
|ResponseStatus|Yes|Svarsstatus|Antal|Totalt|HTTP-svarsstatus som returneras av Application Gateway|HttpStatusGroup|
|Dataflöde|No|Dataflöde|BytePerSecond|Genomsnitt|Antal byte per sekund som Application Gateway har betjänat|Inga dimensioner|
|TlsProtocol|Yes|TLS-klientprotokoll|Antal|Totalt|Antalet TLS- och icke-TLS-begäranden som initierats av klienten som upprättat en anslutning till Application Gateway. Om du vill visa TLS-protokolldistributionen filtrerar du efter dimensionens TLS-protokoll.|Lyssnare, TlsProtocol|
|TotalRequests|Yes|Totalt antal förfrågningar|Antal|Totalt|Antal lyckade begäranden som Application Gateway har betjänat|BackendSettingsPool|
|UnhealthyHostCount|Yes|Antal värdar med feltillstånd|Antal|Genomsnitt|Antal värdar i serverdelen med feltillstånd|BackendSettingsPool|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft.Network/azurefirewalls

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ApplicationRuleHit|Yes|Antal träffar för programregler|Antal|Totalt|Antal gånger som programregler har körts|Status, Orsak, Protokoll|
|DataBearbetad|Yes|Bearbetade data|Byte|Totalt|Total mängd data som bearbetas av den här brandväggen|Inga dimensioner|
|FirewallHealth|Yes|Hälsotillstånd för brandvägg|Procent|Genomsnitt|Anger den övergripande hälsan för brandväggen|Status, orsak|
|NetworkRuleHit|Yes|Antal träffar för nätverksregler|Antal|Totalt|Antal gånger som nätverksregler har körts|Status, Orsak, Protokoll|
|SNATPortUtilization|Yes|SNAT-portanvändning|Procent|Genomsnitt|Procentandel utgående SNAT-portar som används för närvarande|Protokoll|
|Dataflöde|No|Dataflöde|BitsPerSecond|Genomsnitt|Dataflöde som bearbetas av den här brandväggen|Inga dimensioner|


## <a name="microsoftnetworkbastionhosts"></a>microsoft.network/bastionHosts

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|pingmesh|No|Kommunikationsstatus för Bastion|Antal|Genomsnitt|Kommunikationsstatusen visar 1 om all kommunikation är bra och 0 om den är dålig.||
|Sessioner|No|Sessionsantal|Antal|Totalt|Antal sessioner för Bastion. Visa i summa och per instans.|värd|
|totalt|Yes|Totalt minne|Antal|Genomsnitt|Total minnesstatistik.|värd|
|usage_user|No|Använd CPU|Antal|Genomsnitt|Cpu-användningsstatistik.|cpu, host|
|använt|Yes|Använt minne|Antal|Genomsnitt|Statistik för minnesanvändning.|värd|


## <a name="microsoftnetworkconnections"></a>Microsoft.Network/connections

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|BitsInPerSecond|Yes|BitsInPerSecond|BitsPerSecond|Genomsnitt|Inkommande bitar till Azure per sekund|Inga dimensioner|
|BitsOutPerSecond|Yes|BitsOutPerSecond|BitsPerSecond|Genomsnitt|Utgående bitar till Azure per sekund|Inga dimensioner|


## <a name="microsoftnetworkdnszones"></a>Microsoft.Network/dnszones

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|QueryVolume|No|Frågevolym|Antal|Totalt|Antal frågor som hanteras för en DNS-zon|Inga dimensioner|
|RecordSetCapacityUtilization|No|Postuppsättningens kapacitetsutnyttjande|Procent|Maximal|Procent av postuppsättningens kapacitet som används av en DNS-zon|Inga dimensioner|
|RecordSetCount|No|Antal postuppsättningar|Antal|Maximal|Antal postuppsättningar i en DNS-zon|Inga dimensioner|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ArpAvailability|Yes|Tillgänglighet för Arp|Procent|Genomsnitt|ARP-tillgänglighet från MSEE till alla peer-peers.|PeeringType, Peer|
|BgpAvailability|Yes|Bgp-tillgänglighet|Procent|Genomsnitt|BGP-tillgänglighet från MSEE till alla peer-peers.|PeeringType, Peer|
|BitsInPerSecond|No|BitsInPerSecond|BitsPerSecond|Genomsnitt|Inkommande bitar till Azure per sekund|PeeringType, DeviceRole|
|BitsOutPerSecond|No|BitsOutPerSecond|BitsPerSecond|Genomsnitt|Utgående bitar till Azure per sekund|PeeringType, DeviceRole|
|GlobalReachBitsInPerSecond|No|GlobalReachBitsInPerSecond|BitsPerSecond|Genomsnitt|Inkommande bitar till Azure per sekund|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|No|GlobalReachBitsOutPerSecond|BitsPerSecond|Genomsnitt|Utgående bitar till Azure per sekund|PeeredCircuitSKey|
|QosDropBitsInPerSecond|Yes|DroppedInBitsPerSecond|BitsPerSecond|Genomsnitt|Indata bitar av data som tas bort per sekund|Inga dimensioner|
|QosDropBitsOutPerSecond|Yes|DroppedOutBitsPerSecond|BitsPerSecond|Genomsnitt|Utgående bitar data som tas bort per sekund|Inga dimensioner|


## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft.Network/expressRouteCircuits/peerings

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|BitsInPerSecond|Yes|BitsInPerSecond|BitsPerSecond|Genomsnitt|Inkommande bitar till Azure per sekund|Inga dimensioner|
|BitsOutPerSecond|Yes|BitsOutPerSecond|BitsPerSecond|Genomsnitt|Utgående bitar till Azure per sekund|Inga dimensioner|


## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft.Network/expressRouteGateways

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|No|BitsInPerSecond|BitsPerSecond|Genomsnitt|Inkommande bitar till Azure per sekund|ConnectionName|
|ErGatewayConnectionBitsOutPerSecond|No|BitsOutPerSecond|BitsPerSecond|Genomsnitt|Utgående bitar till Azure per sekund|ConnectionName|
|ExpressRouteGatewayCountOfRoutesAdvertedToPeer|Yes|Antal vägar som annonseras till peer (förhandsversion)|Antal|Maximal|Antal vägar som annonseras till peer av ExpressRouteGateway|roleInstance|
|ExpressRouteGatewayCountOfRoutesLearnedFromPeer|Yes|Antal inlärda vägar från peer (förhandsversion)|Antal|Maximal|Antal vägar som lärts från peer av ExpressRouteGateway|roleInstance|
|ExpressRouteGatewayCpuUtilization|Yes|CPU-användning|Antal|Genomsnitt|Cpu-användning av ExpressRoute-gatewayen|roleInstance|
|ExpressRouteGatewayFrequencyOfRoutesChanged|No|Ändringsfrekvens för vägar (förhandsversion)|Antal|Totalt|Ändringsfrekvens för vägar i ExpressRoute-gateway|roleInstance|
|ExpressRouteGatewayNumberOfVmInVnet|No|Antal virtuella datorer i Virtual Network (förhandsversion)|Antal|Maximal|Antal virtuella datorer i Virtual Network|Inga dimensioner|
|ExpressRouteGatewayPacketsPerSecond|No|Paket per sekund|CountPerSecond|Genomsnitt|Antal paket för ExpressRoute-gateway|roleInstance|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft.Network/expressRoutePorts

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|AdminState|Yes|AdminState|Antal|Genomsnitt|Administratörstillstånd för porten|Länk|
|LineProtocol|Yes|LineProtocol|Antal|Genomsnitt|Status för radprotokoll för porten|Länk|
|PortBitsInPerSecond|Yes|BitsInPerSecond|BitsPerSecond|Genomsnitt|Inkommande bitar till Azure per sekund|Länk|
|PortBitsOutPerSecond|Yes|BitsOutPerSecond|BitsPerSecond|Genomsnitt|Utgående bitar till Azure per sekund|Länk|
|RxLightLevel|Yes|RxLightLevel|Antal|Genomsnitt|Rx-ljusnivå i dBm|Länk, band|
|TxLightLevel|Yes|TxLightLevel|Antal|Genomsnitt|Tx-ljusnivå i dBm|Länk, band|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft.Network/frontdoors

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|BackendHealthPercentage|Yes|Hälsoprocent för backend|Procent|Genomsnitt|Procentandelen lyckade hälsoavsökningar från HTTP/S-proxyn till backends|Backend, BackendPool|
|BackendRequestCount|Yes|Antal förfrågningar från backend|Antal|Totalt|Antalet begäranden som skickas från HTTP/S-proxyn till backends|HttpStatus, HttpStatusGroup, Backend|
|BackendRequestLatency|Yes|Svarstid för backend-begäran|Millisekunder|Genomsnitt|Den tid som beräknas från när begäran skickades av HTTP/S-proxyn till backend tills HTTP/S-proxyn tog emot den sista svarsbyten från backend|Serverdel|
|BillableResponseSize|Yes|Fakturerbar svarsstorlek|Byte|Totalt|Antalet fakturerbara byte (minst 2 kB per begäran) som skickas som svar från HTTP/S-proxyn till klienter.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestCount|Yes|Antal begäranden|Antal|Totalt|Antalet klientbegäranden som betjänas av HTTP/S-proxyn|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Yes|Begärandestorlek|Byte|Totalt|Antalet byte som skickas som begäranden från klienter till HTTP/S-proxyn|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Yes|Svarsstorlek|Byte|Totalt|Antalet byte som skickas som svar från HTTP/S-proxyn till klienter|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|TotalLatency|Yes|Total svarstid|Millisekunder|Genomsnitt|Den tid som beräknas från den tidpunkt då klientbegäran togs emot av HTTP/S-proxyn tills klienten bekräftade den sista svarsbyten från HTTP/S-proxyn|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|WebApplicationFirewallRequestCount|Yes|Web Application Firewall antal förfrågningar|Antal|Totalt|Antalet klientbegäranden som bearbetas av Web Application Firewall|PolicyName, RuleName, Action|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft.Network/loadBalancers

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|AllocatedSnatPorts|No|Allokerade SNAT-portar|Antal|Genomsnitt|Totalt antal tilldelade SNAT-portar inom tidsperioden|FrontendIPAddress, BackendIPAddress, ProtocolType, |
|ByteCount|Yes|Antal byte|Byte|Totalt|Totalt antal byte som överförs inom en tidsperiod|FrontendIPAddress, FrontendPort, Direction|
|DipAvailability|Yes|Status för hälsoavsökningen|Antal|Genomsnitt|Genomsnittlig Load Balancer status för hälsoavsökningen per tid|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|PacketCount|Yes|Antal paket|Antal|Totalt|Totalt antal paket som överförs inom en tidsperiod|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|Yes|Antal SNAT-anslutningar|Antal|Totalt|Totalt antal nya SNAT-anslutningar som skapats inom tidsperioden|FrontendIPAddress, BackendIPAddress, ConnectionState|
|SYNCount|Yes|SYN-antal|Antal|Totalt|Totalt antal SYN-paket som överförs inom en tidsperiod|FrontendIPAddress, FrontendPort, Direction|
|UsedSnatPorts|No|Använda SNAT-portar|Antal|Genomsnitt|Totalt antal SNAT-portar som används inom tidsperioden|FrontendIPAddress, BackendIPAddress, ProtocolType, |
|VipAvailability|Yes|Tillgänglighet för datasökvägar|Antal|Genomsnitt|Genomsnittlig Load Balancer tillgänglighet för datasökväg per tid|FrontendIPAddress, FrontendPort|


## <a name="microsoftnetworknatgateways"></a>Microsoft.Network/natGateways

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ByteCount|Yes|Byte|Byte|Totalt|Totalt antal byte som överförs inom en tidsperiod|Protokoll, riktning|
|DatapathAvailability|Yes|Datapath-tillgänglighet (förhandsversion)|Antal|Genomsnitt|NAT Gateway tillgänglighet för Datapath|Inga dimensioner|
|PacketCount|Yes|Paket|Antal|Totalt|Totalt antal paket som överförs inom en tidsperiod|Protokoll, riktning|
|PacketDropCount|Yes|Bort ignorerade paket|Antal|Totalt|Antal bort ignorerade paket|Inga dimensioner|
|SNATConnectionCount|Yes|Antal SNAT-anslutningar|Antal|Totalt|Totalt antal samtidiga aktiva anslutningar|Protokoll, ConnectionState|
|TotalConnectionCount|Yes|Totalt antal SNAT-anslutningar|Antal|Totalt|Totalt antal aktiva SNAT-anslutningar|Protokoll|


## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft.Network/networkInterfaces

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|BytesReceivedRate|Yes|Mottagna byte|Byte|Totalt|Antal byte som nätverksgränssnittet har tagit emot|Inga dimensioner|
|BytesSentRate|Yes|Skickade byte|Byte|Totalt|Antal byte som nätverksgränssnittet har skickat|Inga dimensioner|
|PacketsReceivedRate|Yes|Mottagna paket|Antal|Totalt|Antal paket som nätverksgränssnittet har tagit emot|Inga dimensioner|
|PaketSentRate|Yes|Skickade paket|Antal|Totalt|Antal paket som nätverksgränssnittet har skickat|Inga dimensioner|


## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft.Network/networkWatchers/connectionMonitors

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|AverageRoundtripMs|Yes|Genomsnittlig tid för tur och retur (ms) (klassisk)|Millisekunder|Genomsnitt|Genomsnittlig tid för nätverkets tur och retur (ms) för anslutningsövervakningsavsökningar som skickas mellan källa och mål|Inga dimensioner|
|ChecksFailedPercent|Yes|Misslyckade kontroller i procent|Procent|Genomsnitt|% av anslutningsövervakningskontrollerna misslyckades|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName, SourceIP, DestinationIP, SourceSubnet, DestinationSubnet|
|ProbesFailedPercent|Yes|% misslyckade avsökningar (klassisk)|Procent|Genomsnitt|% av anslutningsövervakningsavsökningarna misslyckades|Inga dimensioner|
|RoundTripTimeMs|Yes|Round-Trip (ms)|Millisekunder|Genomsnitt|Tid för tur och retur i millisekunder för anslutningsövervakningskontroller|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName, SourceIP, DestinationIP, SourceSubnet, DestinationSubnet|
|TestResult|Yes|Testresultat|Antal|Genomsnitt|Testresultat för anslutningsövervakaren|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, DestinationPort, TestGroupName, TestConfigurationName, TestResultCriterion, SourceIP, DestinationIP, SourceSubnet, DestinationSubnet|


## <a name="microsoftnetworkp2svpngateways"></a>Microsoft.Network/p2sVpnGateways

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|P2SBandwidth|Yes|Gateway P2S-bandbredd|BytePerSecond|Genomsnitt|Genomsnittlig bandbredd för punkt-till-plats för en gateway i byte per sekund|Inga dimensioner|
|P2SConnectionCount|Yes|Antal P2S-anslutningar|Antal|Totalt|Antal anslutningar från punkt-till-plats för en gateway|Protokoll|


## <a name="microsoftnetworkprivatednszones"></a>Microsoft.Network/privateDnsZones

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|QueryVolume|Yes|Frågevolym|Antal|Totalt|Antal frågor som besvaras för en Privat DNS zon|Inga dimensioner|
|RecordSetCapacityUtilization|No|Kapacitetsutnyttjande för postuppsättning|Procent|Maximal|Procent av postuppsättningens kapacitet som används av en Privat DNS zon|Inga dimensioner|
|RecordSetCount|Yes|Antal postuppsättning|Antal|Maximal|Antal postuppsättningar i en Privat DNS zon|Inga dimensioner|
|VirtualNetworkLinkCapacityUtilization|No|Virtual Network länka kapacitetsutnyttjande|Procent|Maximal|Procent av Virtual Network Link-kapacitet som används av en Privat DNS zon|Inga dimensioner|
|VirtualNetworkLinkCount|Yes|Virtual Network antal länkar|Antal|Maximal|Antal virtuella nätverk som är länkade till en Privat DNS zon|Inga dimensioner|
|VirtualNetworkWithRegistrationCapacityUtilization|No|Virtual Network för registreringslänkens kapacitetsutnyttjande|Procent|Maximal|Procent av Virtual Network Link med kapacitet för automatisk registrering som används av en Privat DNS zon|Inga dimensioner|
|VirtualNetworkWithRegistrationLinkCount|Yes|Virtual Network antal registreringslänk|Antal|Maximal|Antal virtuella nätverk som är länkade Privat DNS en zon med automatisk registrering aktiverat|Inga dimensioner|


## <a name="microsoftnetworkprivateendpoints"></a>Microsoft.Network/privateEndpoints

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|PEBytesIn|Yes|Inkommande byte|Antal|Totalt|Totalt antal utgående byte|PrivateEndpointId|
|PEBytesOut|Yes|Utgående byte|Antal|Totalt|Totalt antal utgående byte|PrivateEndpointId|


## <a name="microsoftnetworkprivatelinkservices"></a>Microsoft.Network/privateLinkServices

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|PLSBytesIn|Yes|Byte in|Antal|Totalt|Totalt antal utgående byte|PrivateLinkServiceId|
|PLSBytesOut|Yes|Utgående byte|Antal|Totalt|Totalt antal utgående byte|PrivateLinkServiceId|
|PLSNatPortsUsage|Yes|Nat-portanvändning|Procent|Genomsnitt|Nat-portanvändning|PrivateLinkServiceId, PrivateLinkServiceIPAddress|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft.Network/publicIPAddresses

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ByteCount|Yes|Antal byte|Byte|Totalt|Totalt antal byte som överförs inom en tidsperiod|Port, riktning|
|BytesDroppedDDoS|Yes|Inkommande byte bortförda DDoS|BytePerSecond|Maximal|Inkommande byte bortförda DDoS|Inga dimensioner|
|BytesForwardedDDoS|Yes|Inkommande byte vidarebefordrade DDoS|BytePerSecond|Maximal|Inkommande byte vidarebefordrade DDoS|Inga dimensioner|
|BytesInDDoS|Yes|Inkommande byte DDoS|BytePerSecond|Maximal|Inkommande byte DDoS|Inga dimensioner|
|DDoSTriggerSYNPackets|Yes|Inkommande SYN-paket som ska utlösa DDoS-åtgärd|CountPerSecond|Maximal|Inkommande SYN-paket som ska utlösa DDoS-åtgärd|Inga dimensioner|
|DDoSTriggerTCPPackets|Yes|Inkommande TCP-paket som ska utlösa DDoS-åtgärd|CountPerSecond|Maximal|Inkommande TCP-paket som ska utlösa DDoS-åtgärd|Inga dimensioner|
|DDoSTriggerUDPPackets|Yes|Inkommande UDP-paket som ska utlösa DDoS-åtgärd|CountPerSecond|Maximal|Inkommande UDP-paket som ska utlösa DDoS-åtgärd|Inga dimensioner|
|IfUnderDDoSAttack|Yes|Under DDoS-attack eller inte|Antal|Maximal|Under DDoS-attack eller inte|Inga dimensioner|
|PacketCount|Yes|Antal paket|Antal|Totalt|Totalt antal paket som överförs inom en tidsperiod|Port, riktning|
|PacketsDroppedDDoS|Yes|Inkommande paket som släppts med DDoS|CountPerSecond|Maximal|Inkommande paket som släppts med DDoS|Inga dimensioner|
|PacketsForwardedDDoS|Yes|Inkommande paket vidarebefordrade DDoS|CountPerSecond|Maximal|Inkommande paket vidarebefordrade DDoS|Inga dimensioner|
|PacketsInDDoS|Yes|Inkommande paket DDoS|CountPerSecond|Maximal|Inkommande paket DDoS|Inga dimensioner|
|SynCount|Yes|SYN-antal|Antal|Totalt|Totalt antal SYN-paket som överförs inom en tidsperiod|Port, riktning|
|TCPBytesDroppedDDoS|Yes|Inkommande TCP-byte utelämnade DDoS|BytePerSecond|Maximal|Inkommande TCP-byte utelämnade DDoS|Inga dimensioner|
|TCPBytesForwardedDDoS|Yes|Inkommande TCP-byte vidarebefordrade DDoS|BytesPerSecond|Maximal|Inkommande TCP-byte vidarebefordrade DDoS|Inga dimensioner|
|TCPBytesInDDoS|Yes|Inkommande TCP byte DDoS|BytesPerSecond|Maximal|Inkommande TCP byte DDoS|Inga dimensioner|
|TCPPacketsDroppedDDoS|Yes|Inkommande TCP-paket utelämnade DDoS|CountPerSecond|Maximal|Inkommande TCP-paket utelämnade DDoS|Inga dimensioner|
|TCPPacketsForwardedDDoS|Yes|Inkommande TCP-paket vidarebefordrade DDoS|CountPerSecond|Maximal|Inkommande TCP-paket vidarebefordrade DDoS|Inga dimensioner|
|TCPPacketsInDDoS|Yes|Inkommande TCP-paket DDoS|CountPerSecond|Maximal|Inkommande TCP-paket DDoS|Inga dimensioner|
|UDPBytesDroppedDDoS|Yes|Inkommande UDP-byte har släppt DDoS|BytePerSecond|Maximal|Inkommande UDP-byte har släppt DDoS|Inga dimensioner|
|UDPBytesForwardedDDoS|Yes|Inkommande UDP-byte vidarebefordrade DDoS|BytePerSecond|Maximal|Inkommande UDP-byte vidarebefordrade DDoS|Inga dimensioner|
|UDPBytesInDDoS|Yes|Inkommande UDP byte DDoS|BytePerSecond|Maximal|Inkommande UDP byte DDoS|Inga dimensioner|
|UDPPacketsDroppedDDoS|Yes|Inkommande UDP-paket har släppt DDoS|CountPerSecond|Maximal|Inkommande UDP-paket har släppt DDoS|Inga dimensioner|
|UDPPacketsForwardedDDoS|Yes|Inkommande UDP-paket vidarebefordrade DDoS|CountPerSecond|Maximal|Inkommande UDP-paket vidarebefordrade DDoS|Inga dimensioner|
|UDPPacketsInDDoS|Yes|Inkommande UDP-paket DDoS|CountPerSecond|Maximal|Inkommande UDP-paket DDoS|Inga dimensioner|
|VipAvailability|Yes|Tillgänglighet för datasökvägar|Antal|Genomsnitt|Genomsnittlig tillgänglighet för IP-adresser per tid|Port|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft.Network/trafficManagerProfiles

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Yes|Slutpunktsstatus efter slutpunkt|Antal|Maximal|1 om en slutpunkts avsökningsstatus är "Aktiverad", annars 0.|EndpointName|
|QpsByEndpoint|Yes|Frågor efter returnerad slutpunkt|Antal|Totalt|Antal gånger som en Traffic Manager slutpunkt returnerades inom den angivna tidsramen|EndpointName|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft.Network/virtualNetworkGateways

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|AverageBandwidth|Yes|Gateway-S2S-bandbredd|BytePerSecond|Genomsnitt|Genomsnittlig bandbredd för plats-till-plats för en gateway i byte per sekund|Inga dimensioner|
|ExpressRouteGatewayCountOfRoutesAdvertedToPeer|Yes|Antal vägar som annonseras till peer (förhandsversion)|Antal|Maximal|Antal vägar som annonseras till peer av ExpressRouteGateway|roleInstance|
|ExpressRouteGatewayCountOfRoutesLearnedFromPeer|Yes|Antal inlärda vägar från peer (förhandsversion)|Antal|Maximal|Antal vägar som lärts från peer av ExpressRouteGateway|roleInstance|
|ExpressRouteGatewayCpuUtilization|Yes|CPU-användning|Antal|Genomsnitt|Cpu-användning av ExpressRoute-gatewayen|roleInstance|
|ExpressRouteGatewayFrequencyOfRoutesChanged|No|Ändringsfrekvens för vägar (förhandsversion)|Antal|Totalt|Ändringsfrekvens för vägar i ExpressRoute-gateway|roleInstance|
|ExpressRouteGatewayNumberOfVmInVnet|No|Antal virtuella datorer i Virtual Network (förhandsversion)|Antal|Maximal|Antal virtuella datorer i Virtual Network|Inga dimensioner|
|ExpressRouteGatewayPacketsPerSecond|No|Paket per sekund|CountPerSecond|Genomsnitt|Antal paket för ExpressRoute-gateway|roleInstance|
|P2SBandwidth|Yes|Gateway P2S-bandbredd|BytePerSecond|Genomsnitt|Genomsnittlig bandbredd för punkt-till-plats för en gateway i byte per sekund|Inga dimensioner|
|P2SConnectionCount|Yes|Antal P2S-anslutningar|Antal|Maximal|Antal anslutningar från punkt-till-plats för en gateway|Protokoll|
|TunnelAverageBandwidth|Yes|Tunnelbandbredd|BytePerSecond|Genomsnitt|Genomsnittlig bandbredd för en tunnel i byte per sekund|ConnectionName, RemoteIP|
|TunnelEgressBytes|Yes|Utgående byte för tunnel|Byte|Totalt|Utgående byte för en tunnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Yes|Utgående ignorerade TS-paket med matchningsfel för tunnel|Antal|Totalt|Antal utgående ignorerade paket från trafikväljare med matchningsfel för en tunnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Yes|Utgående tunnelpaket|Antal|Totalt|Antal utgående paket för en tunnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Yes|Inkommande byte för tunnel|Byte|Totalt|Inkommande byte för en tunnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Yes|Inkommande ignorerade TS-paket med matchningsfel för tunnel|Antal|Totalt|Antal inkommande ignorerade paket från trafikväljare med matchningsfel för en tunnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Yes|Inkommande paket för tunnel|Antal|Totalt|Antal inkommande paket för en tunnel|ConnectionName, RemoteIP|
|TunnelNatAllocations|No|NAT-allokeringar för tunnel|Antal|Totalt|Antal allokeringar för en NAT-regel på en tunnel|NatRule, ConnectionName, RemoteIP|
|TunnelNatedBytes|No|NATed-byte för tunnel|Byte|Totalt|Antal byte som har NATed på en tunnel av en NAT-regel |NatRule, ConnectionName, RemoteIP|
|TunnelNatedPackets|No|Tunnel-NATed-paket|Antal|Totalt|Antal paket som har NATed i en tunnel av en NAT-regel|NatRule, ConnectionName, RemoteIP|
|TunnelNatFlowCount|No|NAT-tunnelflöden|Antal|Totalt|Antal NAT-flöden i en tunnel efter flödestyp och NAT-regel|NatRule, ConnectionName, RemoteIP, FlowType|
|TunnelNatPacketDrop|No|Tunnel-NAT-paket som ignoreras|Antal|Totalt|Antal NATed-paket i en tunnel som släppts av släpptyp och NAT-regel|NatRule, ConnectionName, RemoteIP, DropType|
|TunnelReverseNatedBytes|No|Omvända NATed-byte för tunnel|Byte|Totalt|Antal byte som har omvänd NATed på en tunnel av en NAT-regel|NatRule, ConnectionName, RemoteIP|
|TunnelReverseNatedPackets|No|Omvända NATed-paket för tunnel|Antal|Totalt|Antal paket i en tunnel som har omvänd NATed av en NAT-regel|NatRule, ConnectionName, RemoteIP|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft.Network/virtualNetworks

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Yes|Tur och retur-tid för pingar till en virtuell dator|Millisekunder|Genomsnitt|Tur och retur-tid för pingar som skickas till en virtuell måldatorn|SourceCustomerAddress, DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Yes|Misslyckade pingar till en virtuell dator|Procent|Genomsnitt|Procent av antalet misslyckade pingar till totalt antal skickade pingar för en virtuell måldatorn|SourceCustomerAddress, DestinationCustomerAddress|


## <a name="microsoftnetworkvirtualrouters"></a>Microsoft.Network/virtualRouters

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|PeeringAvailability|Yes|Bgp-tillgänglighet|Procent|Genomsnitt|BGP-tillgänglighet mellan VirtualRouter och fjärranslutna peer-datorer|Peer|


## <a name="microsoftnetworkvpngateways"></a>Microsoft.Network/vpnGateways

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|AverageBandwidth|Yes|Gateway S2S-bandbredd|BytesPerSecond|Genomsnitt|Genomsnittlig bandbredd för plats-till-plats för en gateway i byte per sekund|Inga dimensioner|
|TunnelAverageBandwidth|Yes|Tunnelbandbredd|BytesPerSecond|Genomsnitt|Genomsnittlig bandbredd för en tunnel i byte per sekund|ConnectionName, RemoteIP|
|TunnelEgressBytes|Yes|Utgående byte för tunnel|Byte|Totalt|Utgående byte för en tunnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Yes|Utgående ignorerade TS-paket med matchningsfel för tunnel|Antal|Totalt|Antal utgående ignorerade paket från trafikväljare med matchningsfel för en tunnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Yes|Utgående tunnelpaket|Antal|Totalt|Antal utgående paket för en tunnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Yes|Inkommande byte för tunnel|Byte|Totalt|Inkommande byte för en tunnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Yes|Inkommande ignorerade TS-paket med matchningsfel för tunnel|Antal|Totalt|Antal inkommande ignorerade paket från trafikväljare med matchningsfel för en tunnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Yes|Inkommande tunnelpaket|Antal|Totalt|Antal inkommande paket för en tunnel|ConnectionName, RemoteIP|
|TunnelNatAllocations|No|NAT-allokeringar för tunnel|Antal|Totalt|Antal allokeringar för en NAT-regel i en tunnel|NatRule, ConnectionName, RemoteIP|
|TunnelNatedBytes|No|Tunnel-NATed-byte|Byte|Totalt|Antal byte som har NATed i en tunnel av en NAT-regel |NatRule, ConnectionName, RemoteIP|
|TunnelNatedPackets|No|Tunnel-NATed-paket|Antal|Totalt|Antal paket som har NATed i en tunnel av en NAT-regel|NatRule, ConnectionName, RemoteIP|
|TunnelNatFlowCount|No|NAT-tunnelflöden|Antal|Totalt|Antal NAT-flöden i en tunnel efter flödestyp och NAT-regel|NatRule, ConnectionName, RemoteIP, FlowType|
|TunnelNatPacketDrop|No|Tunnel-NAT-paket ignoreras|Antal|Totalt|Antal NATed-paket i en tunnel som släppts per släpptyp och NAT-regel|NatRule, ConnectionName, RemoteIP, DropType|
|TunnelReverseNatedBytes|No|Omvända NATed-byte för tunnel|Byte|Totalt|Antal byte som har omvänd NATed på en tunnel av en NAT-regel|NatRule, ConnectionName, RemoteIP|
|TunnelReverseNatedPackets|No|Omvända NATed-paket för tunnel|Antal|Totalt|Antal paket i en tunnel som har omvänd NATed av en NAT-regel|NatRule, ConnectionName, RemoteIP|


## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Inkommande|Yes|Inkommande meddelanden|Antal|Totalt|Antalet lyckade API-anrop. |Inga dimensioner|
|incoming.all.failedrequests|Yes|Alla inkommande misslyckade begäranden|Antal|Totalt|Totalt antal inkommande misslyckade begäranden för en meddelandehubb|Inga dimensioner|
|incoming.all.requests|Yes|Alla inkommande begäranden|Antal|Totalt|Totalt antal inkommande begäranden för en meddelandehubb|Inga dimensioner|
|incoming.scheduled|Yes|Schemalagda push-meddelanden skickade|Antal|Totalt|Schemalagda push-meddelanden har avbrutits|Inga dimensioner|
|incoming.scheduled.cancel|Yes|Schemalagda push-meddelanden avbröts|Antal|Totalt|Schemalagda push-meddelanden avbröts|Inga dimensioner|
|installation.all|Yes|Installationshanteringsåtgärder|Antal|Totalt|Installationshanteringsåtgärder|Inga dimensioner|
|installation.delete|Yes|Ta bort installationsåtgärder|Antal|Totalt|Ta bort installationsåtgärder|Inga dimensioner|
|installation.get|Yes|Hämta installationsåtgärder|Antal|Totalt|Hämta installationsåtgärder|Inga dimensioner|
|installation.patch|Yes|Åtgärder för korrigeringsinstallation|Antal|Totalt|Åtgärder för korrigeringsinstallation|Inga dimensioner|
|installation.upsert|Yes|Skapa eller uppdatera installationsåtgärder|Antal|Totalt|Skapa eller uppdatera installationsåtgärder|Inga dimensioner|
|notificationhub.pushes|Yes|Alla utgående meddelanden|Antal|Totalt|Alla utgående meddelanden från meddelandehubben|Inga dimensioner|
|outgoing.allpns.badorexpiredchannel|Yes|Felaktiga eller utgångna kanalfel|Antal|Totalt|Antalet push-meddelanden som misslyckades på grund av att channel/token/registrationId i registreringen har upphört att gälla eller är ogiltigt.|Inga dimensioner|
|outgoing.allpns.channelerror|Yes|Kanalfel|Antal|Totalt|Antalet push-meddelanden som misslyckades på grund av att kanalen var ogiltig är inte associerat med rätt app begränsat eller har upphört att gälla.|Inga dimensioner|
|outgoing.allpns.invalidpayload|Yes|Nyttolastfel|Antal|Totalt|Antalet push-meddelanden som misslyckades eftersom PNS returnerade ett felaktigt nyttolastfel.|Inga dimensioner|
|outgoing.allpns.pnserror|Yes|Fel i externt meddelandesystem|Antal|Totalt|Antalet push-meddelanden som misslyckades eftersom det uppstod ett problem med att kommunicera med PNS (utesluter autentiseringsproblem).|Inga dimensioner|
|outgoing.allpns.success|Yes|Meddelanden om att det lyckades|Antal|Totalt|Antalet lyckade meddelanden.|Inga dimensioner|
|outgoing.apns.badchannel|Yes|Apns-fel med felaktig kanal|Antal|Totalt|Antalet push-meddelanden som misslyckades eftersom token är ogiltig (APNS-statuskod: 8).|Inga dimensioner|
|outgoing.apns.expiredchannel|Yes|APNS-kanalfel som har upphört att gälla|Antal|Totalt|Antalet token som har ogiltigförklarats av APNS-feedbackkanalen.|Inga dimensioner|
|outgoing.apns.invalidcredentials|Yes|APNS-auktoriseringsfel|Antal|Totalt|Antalet push-meddelanden som misslyckades eftersom PNS inte accepterar de angivna autentiseringsuppgifterna eller autentiseringsuppgifterna blockeras.|Inga dimensioner|
|outgoing.apns.invalidnotificationsize|Yes|Fel med ogiltig meddelandestorlek i APNS|Antal|Totalt|Antalet push-meddelanden som misslyckades eftersom nyttolasten var för stor (APNS-statuskod: 7).|Inga dimensioner|
|outgoing.apns.pnserror|Yes|APNS-fel|Antal|Totalt|Antalet push-meddelanden som misslyckades på grund av fel som kommunicerar med APNS.|Inga dimensioner|
|outgoing.apns.success|Yes|Apns-meddelanden har lyckats|Antal|Totalt|Antalet lyckade meddelanden.|Inga dimensioner|
|outgoing.gcm.authenticationerror|Yes|GCM-autentiseringsfel|Antal|Totalt|Antalet push-meddelanden som misslyckades på grund av att PNS inte accepterar de angivna autentiseringsuppgifterna blockeras eller senderId är inte korrekt konfigurerat i appen (GCM-resultat: MismatchedSenderId).|Inga dimensioner|
|outgoing.gcm.badchannel|Yes|Fel med felaktig GCM-kanal|Antal|Totalt|Antalet push-meddelanden som misslyckades eftersom registrationId i registreringen inte identifierades (GCM-resultat: Ogiltig registrering).|Inga dimensioner|
|outgoing.gcm.expiredchannel|Yes|GCM-kanalfel som har upphört att gälla|Antal|Totalt|Antalet push-meddelanden som misslyckades eftersom registrationId i registreringen har upphört att gälla (GCM-resultat: NotRegistered).|Inga dimensioner|
|outgoing.gcm.invalidcredentials|Yes|GCM-auktoriseringsfel (ogiltiga autentiseringsuppgifter)|Antal|Totalt|Antalet push-meddelanden som misslyckades eftersom PNS inte accepterar de angivna autentiseringsuppgifterna eller autentiseringsuppgifterna blockeras.|Inga dimensioner|
|outgoing.gcm.invalidnotificationformat|Yes|Ogiltigt meddelandeformat för GCM|Antal|Totalt|Antalet push-meddelanden som misslyckades eftersom nyttolasten inte har formaterats korrekt (GCM-resultat: InvalidDataKey eller InvalidTtl).|Inga dimensioner|
|outgoing.gcm.invalidnotificationsize|Yes|Fel med ogiltig meddelandestorlek för GCM|Antal|Totalt|Antalet push-meddelanden som misslyckades eftersom nyttolasten var för stor (GCM-resultat: MessageTooBig).|Inga dimensioner|
|outgoing.gcm.pnserror|Yes|GCM-fel|Antal|Totalt|Antalet push-meddelanden som misslyckades på grund av fel som kommunicerar med GCM.|Inga dimensioner|
|outgoing.gcm.success|Yes|Meddelanden om att GCM lyckades|Antal|Totalt|Antalet lyckade meddelanden.|Inga dimensioner|
|outgoing.gcm.throttled|Yes|GCM-begränsade meddelanden|Antal|Totalt|Antalet push-meddelanden som misslyckades på grund av att GCM begränsade den här appen (GCM-statuskod: 501–599 eller resultat:Ej tillgänglig).|Inga dimensioner|
|outgoing.gcm.wrongchannel|Yes|Fel kanalfel i GCM|Antal|Totalt|Antalet push-meddelanden som misslyckades eftersom registrationId i registreringen inte är associerat med den aktuella appen (GCM-resultat: InvalidPackageName).|Inga dimensioner|
|outgoing.mpns.authenticationerror|Yes|MPNS-autentiseringsfel|Antal|Totalt|Antalet push-meddelanden som misslyckades eftersom PNS inte accepterar de angivna autentiseringsuppgifterna eller autentiseringsuppgifterna blockeras.|Inga dimensioner|
|outgoing.mpns.badchannel|Yes|FEL PÅ MPNS – felaktig kanal|Antal|Totalt|Antalet push-meddelanden som misslyckades eftersom ChannelURI i registreringen inte identifierades (MPNS-status: 404 hittades inte).|Inga dimensioner|
|outgoing.mpns.channeldisconnected|Yes|MPNS-kanal frånkopplad|Antal|Totalt|Antalet push-meddelanden som misslyckades eftersom ChannelURI i registreringen kopplades från (MPNS-status: 412 hittades inte).|Inga dimensioner|
|outgoing.mpns.dropped|Yes|MPNS-meddelanden som släppts|Antal|Totalt|Antalet push-meddelanden som togs bort av MPNS (MPNS-svarshuvud: X-NotificationStatus: QueueFull eller Suppressed).|Inga dimensioner|
|outgoing.mpns.invalidcredentials|Yes|Ogiltiga MPNS-autentiseringsuppgifter|Antal|Totalt|Antalet push-meddelanden som misslyckades eftersom PNS inte accepterar de angivna autentiseringsuppgifterna eller autentiseringsuppgifterna blockeras.|Inga dimensioner|
|outgoing.mpns.invalidnotificationformat|Yes|Ogiltigt meddelandeformat för MPNS|Antal|Totalt|Antalet push-meddelanden som misslyckades eftersom meddelandets nyttolast var för stor.|Inga dimensioner|
|outgoing.mpns.pnserror|Yes|MPNS-fel|Antal|Totalt|Antalet push-meddelanden som misslyckades på grund av fel som kommunicerar med MPNS.|Inga dimensioner|
|outgoing.mpns.success|Yes|MPNS-meddelanden har lyckats|Antal|Totalt|Antalet lyckade meddelanden.|Inga dimensioner|
|outgoing.mpns.throttled|Yes|MPNS-begränsade meddelanden|Antal|Totalt|Antalet push-meddelanden som misslyckades eftersom MPNS är begränsning av den här appen (WNS MPNS: 406 Inte acceptabelt).|Inga dimensioner|
|outgoing.wns.authenticationerror|Yes|WNS-autentiseringsfel|Antal|Totalt|Meddelandet levereras inte på grund av fel som kommunicerar med ogiltiga autentiseringsuppgifter för Windows Live eller fel token.|Inga dimensioner|
|outgoing.wns.badchannel|Yes|Fel med felaktig WNS-kanal|Antal|Totalt|Antalet push-meddelanden som misslyckades eftersom ChannelURI i registreringen inte identifierades (WNS-status: 404 hittades inte).|Inga dimensioner|
|outgoing.wns.channeldisconnected|Yes|WNS-kanal frånkopplad|Antal|Totalt|Meddelandet togs bort eftersom ChannelURI i registreringen är begränsad (WNS-svarshuvud: X-WNS-DeviceConnectionStatus: frånkopplad).|Inga dimensioner|
|outgoing.wns.channelthrottled|Yes|Begränsning av WNS-kanal|Antal|Totalt|Meddelandet togs bort eftersom ChannelURI i registreringen är begränsad (WNS-svarshuvud: X-WNS-NotificationStatus:channelThrottled).|Inga dimensioner|
|outgoing.wns.dropped|Yes|Ignorerade WNS-meddelanden|Antal|Totalt|Meddelandet togs bort eftersom ChannelURI i registreringen är begränsad (X-WNS-NotificationStatus: utelämnad men inte X-WNS-DeviceConnectionStatus: frånkopplad).|Inga dimensioner|
|outgoing.wns.expiredchannel|Yes|WNS-kanalfel som har upphört att gälla|Antal|Totalt|Antalet push-meddelanden som misslyckades eftersom ChannelURI har upphört att gälla (WNS-status: 410 Borta).|Inga dimensioner|
|outgoing.wns.invalidcredentials|Yes|WNS-auktoriseringsfel (ogiltiga autentiseringsuppgifter)|Antal|Totalt|Antalet push-meddelanden som misslyckades eftersom PNS inte accepterar de angivna autentiseringsuppgifterna eller autentiseringsuppgifterna blockeras. (Windows Live känner inte igen autentiseringsuppgifterna).|Inga dimensioner|
|outgoing.wns.invalidnotificationformat|Yes|Ogiltigt meddelandeformat för WNS|Antal|Totalt|Formatet för meddelandet är ogiltigt (WNS-status: 400). Observera att WNS inte avvisar alla ogiltiga nyttolaster.|Inga dimensioner|
|outgoing.wns.invalidnotificationsize|Yes|Fel med ogiltig meddelandestorlek för WNS|Antal|Totalt|Meddelandenyttolasten är för stor (WNS-status: 413).|Inga dimensioner|
|outgoing.wns.invalidtoken|Yes|WNS-auktoriseringsfel (ogiltig token)|Antal|Totalt|Den token som tillhandahålls till WNS är inte giltig (WNS-status: 401 Obehörig).|Inga dimensioner|
|outgoing.wns.pnserror|Yes|WNS-fel|Antal|Totalt|Meddelandet levereras inte på grund av fel som kommunicerar med WNS.|Inga dimensioner|
|outgoing.wns.success|Yes|Meddelanden om att WNS lyckades|Antal|Totalt|Antalet lyckade meddelanden.|Inga dimensioner|
|outgoing.wns.throttled|Yes|Begränsade WNS-meddelanden|Antal|Totalt|Antalet push-meddelanden som misslyckades eftersom WNS är begränsning av den här appen (WNS-status: 406 Inte acceptabelt).|Inga dimensioner|
|outgoing.wns.tokenproviderunreachable|Yes|WNS-auktoriseringsfel (det går inte att nå)|Antal|Totalt|Windows Live kan inte nås.|Inga dimensioner|
|outgoing.wns.wrongtoken|Yes|WNS-auktoriseringsfel (fel token)|Antal|Totalt|Den token som tillhandahålls till WNS är giltig men för ett annat program (WNS-status: 403 Förbjuden). Detta kan inträffa om ChannelURI i registreringen är associerad med en annan app. Kontrollera att klientappen är associerad med samma app vars autentiseringsuppgifter finns i meddelandehubben.|Inga dimensioner|
|registration.all|Yes|Registreringsåtgärder|Antal|Totalt|Antalet lyckade registreringsåtgärder (skapande uppdaterar frågor och borttagningar). |Inga dimensioner|
|registration.create|Yes|Åtgärder för att skapa registrering|Antal|Totalt|Antalet lyckade registreringsskapanden.|Inga dimensioner|
|registration.delete|Yes|Borttagningsåtgärder för registrering|Antal|Totalt|Antalet lyckade registreringsborttagningar.|Inga dimensioner|
|registration.get|Yes|Läsåtgärder för registrering|Antal|Totalt|Antalet lyckade registreringsfrågor.|Inga dimensioner|
|registration.update|Yes|Åtgärder för registreringsuppdatering|Antal|Totalt|Antalet lyckade registreringsuppdateringar.|Inga dimensioner|
|scheduled.pending|Yes|Väntande schemalagda meddelanden|Antal|Totalt|Väntande schemalagda meddelanden|Inga dimensioner|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft.OperationalInsights/workspaces

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Average_ % tillgängligt minne|Yes|% tillgängligt minne|Antal|Genomsnitt|Average_ % tillgängligt minne|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % tillgängligt växlingsutrymme|Yes|% tillgängligt växlingsutrymme|Antal|Genomsnitt|Average_ % tillgängligt växlingsutrymme|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% indeerade byte som används|Yes|% indeade byte som används|Antal|Genomsnitt|Average_% indeerade byte som används|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% DPC-tid|Yes|% DPC-tid|Antal|Genomsnitt|Average_% DPC-tid|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % kostnadsfria i-ode|Yes|% kostnadsfria i-ode|Antal|Genomsnitt|Average_ % kostnadsfria i-ode|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % ledigt utrymme|Yes|% ledigt utrymme|Antal|Genomsnitt|Average_ % ledigt utrymme|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % inaktivitetstid|Yes|Inaktivitetstid i procent|Antal|Genomsnitt|Average_ % inaktivitetstid|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % avbrottstid|Yes|% avbrottstid|Antal|Genomsnitt|Average_ % avbrottstid|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % I/S-väntetid|Yes|% I/O-väntetid|Antal|Genomsnitt|Average_ % I/S-väntetid|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % bra tid|Yes|% bra tid|Antal|Genomsnitt|Average_ % bra tid|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% privilegierad tid|Yes|% privilegierad tid|Antal|Genomsnitt|Average_% privilegierad tid|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % processortid|Yes|% processortid|Antal|Genomsnitt|Average_ % processortid|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % används i -undernoder|Yes|% används i -undernoder|Antal|Genomsnitt|Average_ % används i -undernoder|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % använt minne|Yes|% använt minne|Antal|Genomsnitt|Average_ % använt minne|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % använt utrymme|Yes|% använt utrymme|Antal|Genomsnitt|Average_ % använt utrymme|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % använt växlingsutrymme|Yes|% använt växlingsutrymme|Antal|Genomsnitt|Average_ % använt växlingsutrymme|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ % användartid|Yes|% användartid|Antal|Genomsnitt|Average_ % användartid|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MB|Yes|Tillgängliga megabyte|Antal|Genomsnitt|Average_Available MB|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available mb minne|Yes|Tillgängligt mb minne|Antal|Genomsnitt|Average_Available mb minne|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available mb byte|Yes|Tillgängligt byte i MB|Antal|Genomsnitt|Average_Available mb byte|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk s/läsning|Yes|Genomsnittlig disk s/läsning|Antal|Genomsnitt|Average_Avg. Disk s/läsning|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk s/överföring|Yes|Genomsnittlig disk s/överföring|Antal|Genomsnitt|Average_Avg. Disk s/överföring|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk s/skrivning|Yes|Genomsnittlig disk s/skrivning|Antal|Genomsnitt|Average_Avg. Disk s/skrivning|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes mottagna per sekund|Yes|Mottagna byte/sek|Antal|Genomsnitt|Average_Bytes mottagna per sekund|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes skickade per sekund|Yes|Skickade byte/sek|Antal|Genomsnitt|Average_Bytes skickade per sekund|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes summa/sek|Yes|Totalt antal byte/sek|Antal|Genomsnitt|Average_Bytes summa/sek|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current diskkölängd|Yes|Aktuell diskkölängd|Antal|Genomsnitt|Average_Current diskkölängd|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk lästa byte/s|Yes|Byte/s för diskläsning|Antal|Genomsnitt|Average_Disk lästa byte/sek|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk läsningar/sek|Yes|Diskläsningar/s|Antal|Genomsnitt|Average_Disk läsningar/sek|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk överföringar/sek|Yes|Disköverföringar/sek|Antal|Genomsnitt|Average_Disk överföringar/sek|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk skrivningsbyte/sek|Yes|Diskskrivningsbyte/s|Antal|Genomsnitt|Average_Disk skrivningsbyte/sek|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk skrivningar/sek|Yes|Disk-skrivningar/sek|Antal|Genomsnitt|Average_Disk skrivningar/sek|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free megabyte|Yes|Kostnadsfria megabyte|Antal|Genomsnitt|Average_Free megabyte|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free fysiskt minne|Yes|Ledigt fysiskt minne|Antal|Genomsnitt|Average_Free fysiskt minne|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free utrymme i växlingsfiler|Yes|Ledigt utrymme i växlingsfiler|Antal|Genomsnitt|Average_Free utrymme i växlingsfiler|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free virtuellt minne|Yes|Ledigt virtuellt minne|Antal|Genomsnitt|Average_Free virtuellt minne|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical diskbyte per sekund|Yes|Byte per sekund för logisk disk|Antal|Genomsnitt|Average_Logical diskbyte per sekund|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page läsningar/sek|Yes|Sidläsningar/s|Antal|Genomsnitt|Average_Page läsningar/sek|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page skrivningar per sekund|Yes|Sid skrivningar/sek|Antal|Genomsnitt|Average_Page skrivningar per sekund|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages per sekund|Yes|Sidor/sek|Antal|Genomsnitt|Average_Pages per sekund|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct privilegierad tid|Yes|Pct Privileged Time|Antal|Genomsnitt|Average_Pct privilegierad tid|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct användartid|Yes|Pct-användartid|Antal|Genomsnitt|Average_Pct användartid|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical diskbyte per sekund|Yes|Byte per sekund för fysisk disk|Antal|Genomsnitt|Average_Physical diskbyte per sekund|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Yes|Processer|Antal|Genomsnitt|Average_Processes|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processor kölängd|Yes|Kölängd för processor|Antal|Genomsnitt|Average_Processor kölängd|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size lagras i växlingsfiler|Yes|Storlek som lagras i växlingsfiler|Antal|Genomsnitt|Average_Size lagras i växlingsfiler|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total byte|Yes|Totalt antal byte|Antal|Genomsnitt|Average_Total byte|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total mottagna byte|Yes|Totalt antal mottagna byte|Antal|Genomsnitt|Average_Total mottagna byte|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total överförda byte|Yes|Totalt antal överförda byte|Antal|Genomsnitt|Average_Total överförda byte|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total kollisioner|Yes|Totala kollisioner|Antal|Genomsnitt|Average_Total kollisioner|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total mottagna paket|Yes|Totalt antal mottagna paket|Antal|Genomsnitt|Average_Total mottagna paket|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total överförda paket|Yes|Totalt antal överförda paket|Antal|Genomsnitt|Average_Total överförda paket|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Rx-fel|Yes|Totalt antal Rx-fel|Antal|Genomsnitt|Average_Total Rx-fel|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total Tx-fel|Yes|Totalt antal Tx-fel|Antal|Genomsnitt|Average_Total Tx-fel|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Yes|Drifttid|Antal|Genomsnitt|Average_Uptime|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used MB växlingsutrymme|Yes|Använt mb växlingsutrymme|Antal|Genomsnitt|Average_Used MB växlingsutrymme|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used minne kByte|Yes|Använt minne kByte|Antal|Genomsnitt|Average_Used kByte minne|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used mb minne|Yes|Använt minne mb|Antal|Genomsnitt|Average_Used mb minne|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Yes|Användare|Antal|Genomsnitt|Average_Users|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual delat minne|Yes|Virtuellt delat minne|Antal|Genomsnitt|Average_Virtual delat minne|Computer, ObjectName, InstanceName, CounterPath, SourceSystem|
|Händelse|Yes|Händelse|Antal|Genomsnitt|Händelse|Source, EventLog, Computer, EventCategory, EventLevel, EventLevelName, EventID|
|Pulsslag|Yes|Pulsslag|Antal|Totalt|Pulsslag|Computer, OSType, Version, SourceComputerId|
|Uppdatera|Yes|Uppdatera|Antal|Genomsnitt|Uppdatera|Computer, Product, Classification, UpdateState, Optional, Approved|


## <a name="microsoftpeeringpeerings"></a>Microsoft.Peering/peerings

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|EgressTrafficRate|Yes|Frekvens för utgående trafik|BitsPerSecond|Genomsnitt|Utgående trafikhastighet i bitar per sekund|ConnectionId, SessionIp, TrafficClass|
|IngressTrafficRate|Yes|Hastighet för ingående trafik|BitsPerSecond|Genomsnitt|Ingresstrafikhastighet i bitar per sekund|ConnectionId, SessionIp, TrafficClass|
|Sessionstillgänglighet|Yes|Sessionstillgänglighet|Antal|Genomsnitt|Tillgänglighet för peeringsessionen|ConnectionId, SessionIp|


## <a name="microsoftpeeringpeeringservices"></a>Microsoft.Peering/peeringServices

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|PrefixLatency|Yes|Prefixfördröjning|Millisekunder|Genomsnitt|Medianfördröjning för prefix|PrefixName|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft.PowerBIDedicated/capacities

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|memory_metric|Yes|Minne (Gen1)|Byte|Genomsnitt|Memory. Intervallet 0–3 GB för A1, 0–5 GB för A2, 0–10 GB för A3, 0–25 GB för A4, 0–50 GB för A5 och 0–100 GB för A6. Stöds endast för Power BI Embedded Generation 1-resurser.|Inga dimensioner|
|memory_thrashing_metric|Yes|Minness thrashing (datauppsättningar) (Gen1)|Procent|Genomsnitt|Genomsnittlig minnessmingring. Stöds endast för Power BI Embedded Generation 1-resurser.|Inga dimensioner|
|qpu_high_utilization_metric|Yes|Hög användning av QPU (Gen1)|Antal|Totalt|Hög användning av QPU under den senaste minuten, 1 för hög QPU-användning, annars 0. Stöds endast för Power BI Embedded Generation 1-resurser.|Inga dimensioner|
|QueryDuration|Yes|Frågevaraktighet (datauppsättningar) (Gen1)|Millisekunder|Genomsnitt|DAX-frågevaraktighet i det senaste intervallet. Stöds endast för Power BI Embedded Generation 1-resurser.|Inga dimensioner|
|QueryPoolJobQueueLength|Yes|Jobbkölängd för frågepool (datauppsättningar) (Gen1)|Antal|Genomsnitt|Antal jobb i kön för frågetrådpoolen. Stöds endast för Power BI Embedded Generation 1-resurser.|Inga dimensioner|


## <a name="microsoftpurviewaccounts"></a>microsoft.purview/accounts

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ScanCancelled|Yes|Genomsökningen avbröts|Antal|Totalt|Anger hur många genomsökningar som avbröts.||
|ScanCompleted|Yes|Genomsökningen har slutförts|Antal|Totalt|Anger antalet genomsökningar som har slutförts.||
|ScanFailed|Yes|Genomsökningen misslyckades|Antal|Totalt|Anger antalet misslyckade genomsökningar.||
|ScanTimeTaken|Yes|Tidsgenomsökning|Sekunder|Totalt|Anger den totala genomsökningstiden i sekunder.||


## <a name="microsoftrelaynamespaces"></a>Microsoft.Relay/namnområden

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ActiveConnections|No|ActiveConnections|Antal|Totalt|Totalt antal ActiveConnections för Microsoft.Relay.|Entityname|
|ActiveListeners|No|ActiveListeners|Antal|Totalt|Totalt antal ActiveListeners för Microsoft.Relay.|Entityname|
|BytesTransferred|Yes|BytesTransferred|Byte|Totalt|Totalt antal byteTransferred för Microsoft.Relay.|Entityname|
|ListenerConnections-ClientError|No|ListenerConnections-ClientError|Antal|Totalt|ClientError på ListenerConnections för Microsoft.Relay.|EntityName, OperationResult|
|ListenerConnections-ServerError|No|ListenerConnections-ServerError|Antal|Totalt|ServerError på ListenerConnections för Microsoft.Relay.|EntityName, OperationResult|
|ListenerConnections-Success|No|ListenerConnections-Success|Antal|Totalt|Lyckad ListenerConnections för Microsoft.Relay.|EntityName, OperationResult|
|ListenerConnections-TotalRequests|No|ListenerConnections-TotalRequests|Antal|Totalt|Totalt antal ListenerConnections för Microsoft.Relay.|Entityname|
|ListenerDisconnects|No|ListenerDisconnects|Antal|Totalt|Totalt antal ListenerDisconnects för Microsoft.Relay.|Entityname|
|SenderConnections-ClientError|No|SenderConnections-ClientError|Antal|Totalt|ClientError på SenderConnections för Microsoft.Relay.|EntityName, OperationResult|
|SenderConnections-ServerError|No|SenderConnections-ServerError|Antal|Totalt|ServerError på SenderConnections för Microsoft.Relay.|EntityName, OperationResult|
|SenderConnections-Success|No|SenderConnections-Success|Antal|Totalt|Successful SenderConnections for Microsoft.Relay.|EntityName, OperationResult|
|SenderConnections-TotalRequests|No|SenderConnections-TotalRequests|Antal|Totalt|Totalt antal SenderConnections-begäranden för Microsoft.Relay.|Entityname|
|SenderDisconnects|No|SenderDisconnects|Antal|Totalt|Totalt antal SenderDisconnects för Microsoft.Relay.|Entityname|


## <a name="microsoftresourcessubscriptions"></a>microsoft.resources/subscriptions

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Svarstid|Yes|Svarstidsdata för HTTP-inkommande begäranden|Antal|Genomsnitt|Svarstidsdata för HTTP-inkommande begäranden|Metod, Namnområde, RequestRegion, ResourceType, Microsoft.SubscriptionId|
|Trafik|Yes|Trafik|Antal|Genomsnitt|HTTP-trafik|RequestRegion, StatusCode, StatusCodeClass, ResourceType, Namespace, Microsoft.SubscriptionId|


## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|SearchLatency|Yes|Svarstid för sökning|Sekunder|Genomsnitt|Genomsnittlig söksvarstid för söktjänsten|Inga dimensioner|
|SearchQueriesPerSecond|Yes|Sökfrågor per sekund|CountPerSecond|Genomsnitt|Sökfrågor per sekund för söktjänsten|Inga dimensioner|
|ThrottledSearchQueriesPercentage|Yes|Procentandel begränsade sökfrågor|Procent|Genomsnitt|Procentandel sökfrågor som har begränsats för söktjänsten|Inga dimensioner|


## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ActiveConnections|No|ActiveConnections|Antal|Totalt|Totalt antal aktiva anslutningar för Microsoft.ServiceBus.||
|ActiveMessages|No|Antal aktiva meddelanden i en kö/ett ämne.|Antal|Genomsnitt|Antal aktiva meddelanden i en kö/ett ämne.|Entityname|
|Anslutningarclosed|No|Stängda anslutningar.|Antal|Genomsnitt|Anslutningar som har stängts för Microsoft.ServiceBus.|Entityname|
|AnslutningarÖppnad|No|Öppna anslutningar.|Antal|Genomsnitt|Anslutningar öppnade för Microsoft.ServiceBus.|Entityname|
|CPUXNS|No|CPU (inaktuell)|Procent|Maximal|Processoranvändningsmått för Service Bus Premium-namnområdet. Det här måttet är inaktuellt. Använd CPU-måttet (NamespaceCpuUsage) i stället.|Replik|
|DeadletteredMessages|No|Antal meddelanden med obokade meddelanden i en kö/ett ämne.|Antal|Genomsnitt|Antal meddelanden med obokade meddelanden i en kö/ett ämne.|Entityname|
|IncomingMessages|Yes|Inkommande meddelanden|Antal|Totalt|Inkommande meddelanden för Microsoft.ServiceBus.|Entityname|
|IncomingRequests|Yes|Inkommande förfrågningar|Antal|Totalt|Inkommande begäranden för Microsoft.ServiceBus.|Entityname|
|Meddelanden|No|Antal meddelanden i kö/ämne.|Antal|Genomsnitt|Antal meddelanden i kö/ämne.|Entityname|
|NamespaceCpuUsage|No|Processor|Procent|Maximal|Processoranvändningsmått för Premium-namnområdet i Service Bus.|Replik|
|NamespaceMemoryUsage|No|Minnesanvändning|Procent|Maximal|Minnesanvändningsmått för Service Bus Premium-namnrymd.|Replik|
|OutgoingMessages|Yes|Utgående meddelanden|Antal|Totalt|Utgående meddelanden för Microsoft.ServiceBus.|Entityname|
|ScheduledMessages|No|Antal schemalagda meddelanden i en kö/ett ämne.|Antal|Genomsnitt|Antal schemalagda meddelanden i en kö/ett ämne.|Entityname|
|ServerErrors|No|Serverfel.|Antal|Totalt|Serverfel för Microsoft.ServiceBus.|EntityName, OperationResult|
|Storlek|No|Storlek|Byte|Genomsnitt|Storleken på en kö/ett ämne i byte.|Entityname|
|SuccessfulRequests|No|Slutförda förfrågningar|Antal|Totalt|Totalt antal lyckade begäranden för ett namnområde|EntityName, OperationResult|
|ThrottledRequests|No|Begränsade förfrågningar.|Antal|Totalt|Begränsade begäranden för Microsoft.ServiceBus.|EntityName, OperationResult|
|UserErrors|No|Användarfel.|Antal|Totalt|Användarfel för Microsoft.ServiceBus.|EntityName, OperationResult|
|WSXNS|No|Minnesanvändning (inaktuell)|Procent|Maximal|Minnesanvändningsmått för Service Bus Premium-namnrymd. Det här måttet är inaktuellt. Använd måttet Minnesanvändning (NamespaceMemoryUsage) i stället.|Replik|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft.ServiceFabricMesh/applications

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ActualCpu|No|ActualCpu|Antal|Genomsnitt|Faktisk CPU-användning i kärnor|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory|No|ActualMemory|Byte|Genomsnitt|Faktisk minnesanvändning i MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedCpu|No|AllocatedCpu|Antal|Genomsnitt|Processor som allokerats till den här containern i kärnor|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedMemory|No|AllocatedMemory|Byte|Genomsnitt|Minne som allokerats till den här containern i MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ApplicationStatus|No|ApplicationStatus|Antal|Genomsnitt|Status för Service Fabric Mesh program|ApplicationName, Status|
|ContainerStatus|No|ContainerStatus|Antal|Genomsnitt|Status för containern i Service Fabric Mesh program|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, Status|
|CpuUtilization|No|CpuUtilization|Procent|Genomsnitt|Processoranvändningen för den här containern i procent av AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|MemoryUtilization|No|MemoryUtilization|Procent|Genomsnitt|Processoranvändningen för den här containern i procent av AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|RestartCount|No|RestartCount|Antal|Genomsnitt|Antal omstarter för en container i Service Fabric Mesh program|ApplicationName, Status, ServiceName, ServiceReplicaName, CodePackageName|
|ServiceReplicaStatus|No|ServiceReplicaStatus|Antal|Genomsnitt|Hälsostatus för en tjänstreplik i Service Fabric Mesh program|ApplicationName, Status, ServiceName, ServiceReplicaName|
|ServiceStatus|No|ServiceStatus|Antal|Genomsnitt|Hälsostatus för en tjänst i Service Fabric Mesh program|ApplicationName, Status, ServiceName|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft.SignalRService/SignalR

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ConnectionCount|Yes|Antal anslutningar|Antal|Maximal|Mängden användaranslutning.|Slutpunkt|
|InboundTraffic|Yes|Inkommande trafik|Byte|Totalt|Tjänstens inkommande trafik|Inga dimensioner|
|MessageCount|Yes|Antal meddelanden|Antal|Totalt|Den totala mängden meddelanden.|Inga dimensioner|
|Utgåendetraffic|Yes|Utgående trafik|Byte|Totalt|Den utgående trafiken i tjänsten|Inga dimensioner|
|SystemErrors|Yes|Systemfel|Procent|Maximal|Procentandelen systemfel|Inga dimensioner|
|UserErrors|Yes|Användarfel|Procent|Maximal|Procentandelen användarfel|Inga dimensioner|


## <a name="microsoftsignalrservicewebpubsub"></a>Microsoft.SignalRService/WebPubSub

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|InboundTraffic|Yes|Inkommande trafik|Byte|Totalt|Tjänstens inkommande trafik|Inga dimensioner|
|Utgåendetraffic|Yes|Utgående trafik|Byte|Totalt|Tjänstens utgående trafik|Inga dimensioner|
|TotalConnectionCount|Yes|Antal anslutningar|Antal|Maximal|Mängden användaranslutning.|Inga dimensioner|

## <a name="microsoftsqlmanagedinstances"></a>Microsoft.Sql/managedInstances

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|avg_cpu_percent|Yes|Genomsnittlig CPU-procent|Procent|Genomsnitt|Genomsnittlig CPU-procent|Inga dimensioner|
|io_bytes_read|Yes|Lästa I//S-byte|Byte|Genomsnitt|Lästa I//S-byte|Inga dimensioner|
|io_bytes_written|Yes|Skrivna I//S-byte|Byte|Genomsnitt|Skrivna I//S-byte|Inga dimensioner|
|io_requests|Yes|Antal I/O-begäranden|Antal|Genomsnitt|Antal I/O-begäranden|Inga dimensioner|
|reserved_storage_mb|Yes|Reserverat lagringsutrymme|Antal|Genomsnitt|Reserverat lagringsutrymme|Inga dimensioner|
|storage_space_used_mb|Yes|Använt lagringsutrymme|Antal|Genomsnitt|Använt lagringsutrymme|Inga dimensioner|
|virtual_core_count|Yes|Antal virtuella kärnor|Antal|Genomsnitt|Antal virtuella kärnor|Inga dimensioner|


## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|active_queries|Yes|Aktiva frågor|Antal|Totalt|Aktiva frågor i alla arbetsbelastningsgrupper. Gäller endast för informationslager.|Inga dimensioner|
|allocated_data_storage|Yes|Allokerat datautrymme|Byte|Genomsnitt|Allokerad datalagring. Gäller inte för informationslager.|Inga dimensioner|
|app_cpu_billed|Yes|Debiterad app-CPU|Antal|Totalt|App-CPU debiteras. Gäller för serverlösa databaser.|Inga dimensioner|
|app_cpu_percent|Yes|App CPU-procent|Procent|Genomsnitt|App CPU-procent. Gäller för serverlösa databaser.|Inga dimensioner|
|app_memory_percent|Yes|Appminnesprocent|Procent|Genomsnitt|Appminnesprocent. Gäller för serverlösa databaser.|Inga dimensioner|
|base_blob_size_bytes|Yes|Storlek på basbloblagring|Byte|Maximal|Storlek på basbloblagring. Gäller för hyperskaladatabaser.|Inga dimensioner|
|blocked_by_firewall|Yes|Blockerad av brandvägg|Antal|Totalt|Blockerad av brandvägg|Inga dimensioner|
|cache_hit_percent|Yes|Cache träffprocent|Procent|Maximal|Cache träffprocent. Gäller endast för informationslager.|Inga dimensioner|
|cache_used_percent|Yes|Procent använt cacheminne|Procent|Maximal|Använt cacheminne i procent. Gäller endast för informationslager.|Inga dimensioner|
|connection_failed|Yes|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inga dimensioner|
|connection_successful|Yes|Lyckade anslutningar|Antal|Totalt|Lyckade anslutningar|Inga dimensioner|
|cpu_limit|Yes|CPU-gräns|Antal|Genomsnitt|CPU-gräns. Gäller för vCore-baserade databaser.|Inga dimensioner|
|cpu_percent|Yes|CPU-procent|Procent|Genomsnitt|CPU-procent|Inga dimensioner|
|cpu_used|Yes|Processor som används|Antal|Genomsnitt|Cpu som används. Gäller för vCore-baserade databaser.|Inga dimensioner|
|Dödläge|Yes|Dödlägen|Antal|Totalt|Dödlägen. Gäller inte för informationslager.|Inga dimensioner|
|diff_backup_size_bytes|Yes|Lagringsstorlek för differentiell säkerhetskopia|Byte|Maximal|Kumulativ differentiell lagringsstorlek för säkerhetskopiering. Gäller för vCore-baserade databaser. Inte tillämpligt för hyperskaladatabaser.|Inga dimensioner|
|dtu_consumption_percent|Yes|DTU-procent|Procent|Genomsnitt|DTU-procent. Gäller för DTU-baserade databaser.|Inga dimensioner|
|dtu_limit|Yes|DTU-gräns|Antal|Genomsnitt|DTU-gräns. Gäller för DTU-baserade databaser.|Inga dimensioner|
|dtu_used|Yes|DTU används|Antal|Genomsnitt|DTU används. Gäller för DTU-baserade databaser.|Inga dimensioner|
|dwu_consumption_percent|Yes|DWU-procent|Procent|Maximal|DWU-procent. Gäller endast för informationslager.|Inga dimensioner|
|dwu_limit|Yes|DWU-gräns|Antal|Maximal|DWU-gräns. Gäller endast för informationslager.|Inga dimensioner|
|dwu_used|Yes|DWU används|Antal|Maximal|DWU används. Gäller endast för informationslager.|Inga dimensioner|
|full_backup_size_bytes|Yes|Fullständig lagringsstorlek för säkerhetskopiering|Byte|Maximal|Kumulativ lagringsstorlek för fullständig säkerhetskopiering. Gäller för vCore-baserade databaser. Inte tillämpligt för hyperskaladatabaser.|Inga dimensioner|
|local_tempdb_usage_percent|Yes|Lokal tempdb-procent|Procent|Genomsnitt|Lokal tempdb-procent. Gäller endast för informationslager.|Inga dimensioner|
|log_backup_size_bytes|Yes|Lagringsstorlek för loggsäkerhetskopia|Byte|Maximal|Lagringsstorlek för kumulativ loggsäkerhetskopia. Gäller för vCore-baserade databaser och hyperskala-databaser.|Inga dimensioner|
|log_write_percent|Yes|Logg-I/A-procent|Procent|Genomsnitt|Logg-I/A-procent. Gäller inte för informationslager.|Inga dimensioner|
|memory_usage_percent|Yes|Minnesprocent|Procent|Maximal|Minnesprocent. Gäller endast för informationslager.|Inga dimensioner|
|physical_data_read_percent|Yes|Data IO-procent|Procent|Genomsnitt|Data IO-procent|Inga dimensioner|
|queued_queries|Yes|Köade frågor|Antal|Totalt|Köade frågor i alla arbetsbelastningsgrupper. Gäller endast för informationslager.|Inga dimensioner|
|sessions_percent|Yes|Sessionsprocent|Procent|Genomsnitt|Sessionsprocent. Gäller inte för informationslager.|Inga dimensioner|
|snapshot_backup_size_bytes|Yes|Lagringsstorlek för ögonblicksbildssäkerhetskopia|Byte|Maximal|Lagringsstorlek för kumulativ ögonblicksbild för säkerhetskopiering. Gäller för hyperskaladatabaser.|Inga dimensioner|
|sqlserver_process_core_percent|Yes|SQL Server processkärnprocent|Procent|Maximal|CPU-användning som en procentandel av SQL DB-processen. Gäller inte för informationslager.|Inga dimensioner|
|sqlserver_process_memory_percent|Yes|SQL Server processminnesprocent|Procent|Maximal|Minnesanvändning som en procentandel av SQL DB-processen. Gäller inte för informationslager.|Inga dimensioner|
|storage|Yes|Använt datautrymme|Byte|Maximal|Använt datautrymme. Gäller inte för informationslager.|Inga dimensioner|
|storage_percent|Yes|Använt datautrymme i procent|Procent|Maximal|Använt datautrymme i procent. Gäller inte för informationslager eller databaser i hyperskala.|Inga dimensioner|
|tempdb_data_size|Yes|Tempdb-datafilens storlek, kilobyte|Antal|Maximal|Utrymme som används i tempdb-datafiler i kilobyte. Gäller inte för informationslager.|Inga dimensioner|
|tempdb_log_size|Yes|Tempdb-loggfilens storlek kilobyte|Antal|Maximal|Utrymme som används i tempdb-transaktionsloggfilen i kilobyte. Gäller inte för informationslager.|Inga dimensioner|
|tempdb_log_used_percent|Yes|Tempdb-procentlogg används|Procent|Maximal|Använt utrymme i procent i tempdb-transaktionsloggfilen. Gäller inte för informationslager.|Inga dimensioner|
|wlg_active_queries|Yes|Aktiva frågor för arbetsbelastningsgrupp|Antal|Totalt|Aktiva frågor i arbetsbelastningsgruppen. Gäller endast för informationslager.|WorkloadGroupName, IsUserDefined|
|wlg_active_queries_timeouts|Yes|Tidsgränser för frågor för arbetsbelastningsgrupp|Antal|Totalt|Frågor som har tagit för lång tid för arbetsbelastningsgruppen. Gäller endast för informationslager.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_system_percent|Yes|Allokering av arbetsbelastningsgrupp efter systemprocent|Procent|Maximal|Allokerad procentandel resurser i förhållande till hela systemet per arbetsbelastningsgrupp. Gäller endast för informationslager.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|Yes|Allokering av arbetsbelastningsgrupp efter takresursprocent|Procent|Maximal|Allokerad procentandel resurser i förhållande till de angivna takresurserna per arbetsbelastningsgrupp. Gäller endast för informationslager.|WorkloadGroupName, IsUserDefined|
|wlg_effective_cap_resource_percent|Yes|Effektiv cap-resursprocent|Procent|Maximal|En hård gräns för procentandelen resurser som tillåts för arbetsbelastningsgruppen, med hänsyn till effektiv minsta resursprocent som allokerats för andra arbetsbelastningsgrupper. Gäller endast för informationslager.|WorkloadGroupName, IsUserDefined|
|wlg_effective_min_resource_percent|Yes|Effektiv minsta resursprocent|Procent|Maximal|Minsta procentandel resurser som reserverats och isolerats för arbetsbelastningsgruppen, med hänsyn till lägsta servicenivå. Gäller endast för informationslager.|WorkloadGroupName, IsUserDefined|
|wlg_queued_queries|Yes|Frågor i kö för arbetsbelastningsgrupp|Antal|Totalt|Köade frågor i arbetsbelastningsgruppen. Gäller endast för informationslager.|WorkloadGroupName, IsUserDefined|
|workers_percent|Yes|Procentandel arbetare|Procent|Genomsnitt|Arbetare i procent. Gäller inte för informationslager.|Inga dimensioner|
|xtp_storage_percent|Yes|In-Memory OLTP-lagring i procent|Procent|Genomsnitt|In-Memory OLTP-lagringsprocent. Gäller inte för informationslager.|Inga dimensioner|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|allocated_data_storage|Yes|Allokerat datautrymme|Byte|Genomsnitt|Allokerat datautrymme|Inga dimensioner|
|allocated_data_storage_percent|Yes|Allokerat datautrymme i procent|Procent|Maximal|Allokerat datautrymme i procent|Inga dimensioner|
|cpu_limit|Yes|CPU-gräns|Antal|Genomsnitt|CPU-gräns. Gäller för vCore-baserade elastiska pooler.|Inga dimensioner|
|cpu_percent|Yes|CPU-procent|Procent|Genomsnitt|CPU-procent|Inga dimensioner|
|cpu_used|Yes|Processor som används|Antal|Genomsnitt|Processor som används. Gäller för vCore-baserade elastiska pooler.|Inga dimensioner|
|database_allocated_data_storage|No|Allokerat datautrymme|Byte|Genomsnitt|Allokerat datautrymme|DatabaseResourceId|
|database_cpu_limit|No|CPU-gräns|Antal|Genomsnitt|CPU-gräns|DatabaseResourceId|
|database_cpu_percent|No|CPU-procent|Procent|Genomsnitt|CPU-procent|DatabaseResourceId|
|database_cpu_used|No|Processor som används|Antal|Genomsnitt|Processor som används|DatabaseResourceId|
|database_dtu_consumption_percent|No|DTU-procent|Procent|Genomsnitt|DTU-procent|DatabaseResourceId|
|database_eDTU_used|No|eDTU används|Antal|Genomsnitt|eDTU används|DatabaseResourceId|
|database_log_write_percent|No|Logg-I/A-procent|Procent|Genomsnitt|Logg-I/A-procent|DatabaseResourceId|
|database_physical_data_read_percent|No|Data IO-procent|Procent|Genomsnitt|Data IO-procent|DatabaseResourceId|
|database_sessions_percent|No|Sessionsprocent|Procent|Genomsnitt|Sessionsprocent|DatabaseResourceId|
|database_storage_used|No|Använt datautrymme|Byte|Genomsnitt|Använt datautrymme|DatabaseResourceId|
|database_workers_percent|No|Procentandel arbetare|Procent|Genomsnitt|Procentandel arbetare|DatabaseResourceId|
|dtu_consumption_percent|Yes|DTU-procent|Procent|Genomsnitt|DTU-procent. Gäller för DTU-baserade elastiska pooler.|Inga dimensioner|
|eDTU_limit|Yes|eDTU-gräns|Antal|Genomsnitt|eDTU-gräns. Gäller för DTU-baserade elastiska pooler.|Inga dimensioner|
|eDTU_used|Yes|eDTU används|Antal|Genomsnitt|eDTU används. Gäller för DTU-baserade elastiska pooler.|Inga dimensioner|
|log_write_percent|Yes|Logg-I/A-procent|Procent|Genomsnitt|Logg-I/A-procent|Inga dimensioner|
|physical_data_read_percent|Yes|Data IO-procent|Procent|Genomsnitt|Data IO-procent|Inga dimensioner|
|sessions_percent|Yes|Sessionsprocent|Procent|Genomsnitt|Sessionsprocent|Inga dimensioner|
|sqlserver_process_core_percent|Yes|SQL Server processkärnprocent|Procent|Maximal|CPU-användning som en procentandel av SQL DB-processen. Gäller för elastiska pooler.|Inga dimensioner|
|sqlserver_process_memory_percent|Yes|SQL Server för processminne i procent|Procent|Maximal|Minnesanvändning som en procentandel av SQL DB-processen. Gäller för elastiska pooler.|Inga dimensioner|
|storage_limit|Yes|Maximal datastorlek|Byte|Genomsnitt|Maximal datastorlek|Inga dimensioner|
|storage_percent|Yes|Använt datautrymme i procent|Procent|Genomsnitt|Använt datautrymme i procent|Inga dimensioner|
|storage_used|Yes|Använt datautrymme|Byte|Genomsnitt|Använt datautrymme|Inga dimensioner|
|tempdb_data_size|Yes|Tempdb-datafilens storlek, kilobyte|Antal|Maximal|Utrymme som används i tempdb-datafiler i kilobyte.|Inga dimensioner|
|tempdb_log_size|Yes|Tempdb-loggfilens storlek kilobyte|Antal|Maximal|Utrymme som används i tempdb-transaktionsloggfilen i kilobyte.|Inga dimensioner|
|tempdb_log_used_percent|Yes|Tempdb-procentlogg används|Procent|Maximal|Använt utrymme i procent i tempdb-transaktionsloggfilen|Inga dimensioner|
|workers_percent|Yes|Procentandel arbetare|Procent|Genomsnitt|Procentandel arbetare|Inga dimensioner|
|xtp_storage_percent|Yes|In-Memory OLTP-lagring i procent|Procent|Genomsnitt|In-Memory OLTP-lagring i procent|Inga dimensioner|


## <a name="microsoftstoragestorageaccounts"></a>Microsoft.Storage/storageAccounts

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Yes|Tillgänglighet|Procent|Genomsnitt|Procentandelen tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType, ApiName, Autentisering|
|Utgående|Yes|Utgående|Byte|Totalt|Mängden utgående data. Det här talet omfattar utgående data till externa klienter Azure Storage och utgående data i Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|GeoType, ApiName, Autentisering|
|Ingress|Yes|Ingress|Byte|Totalt|Mängden inkommande data i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|GeoType, ApiName, Autentisering|
|SuccessE2ELatency|Yes|Lyckad E2E-svarstid|Millisekunder|Genomsnitt|Den genomsnittliga svarstiden från slutet till slut för lyckade begäranden som görs till en lagringstjänst eller den angivna API-åtgärden, i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|GeoType, ApiName, Autentisering|
|SuccessServerLatency|Yes|Lyckad serversvarstid|Millisekunder|Genomsnitt|Den genomsnittliga tiden det tar för Azure Storage att bearbeta en lyckad begäran. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|GeoType, ApiName, Autentisering|
|Transaktioner|Yes|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType-dimension för antalet olika svarstyper.|ResponseType, GeoType, ApiName, Authentication|
|UsedCapacity|Yes|Använd kapacitet|Byte|Genomsnitt|Mängden lagringsutrymme som används av lagringskontot. För standardlagringskonton är det summan av den kapacitet som används av blobar, filer och köer. För Premium Storage-konton och Blob Storage-konton är det samma som BlobCapacity eller FileCapacity.|Inga dimensioner|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft.Storage/storageAccounts/blobServices

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Yes|Tillgänglighet|Procent|Genomsnitt|Procentandelen tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType, ApiName, Autentisering|
|BlobCapacity|No|Blobkapacitet|Byte|Genomsnitt|Mängden lagringsutrymme som används av lagringskontots Blob Service i byte.|BlobType, Tier|
|BlobCount|No|Antal blobar|Antal|Genomsnitt|Antalet blobobjekt som lagras i lagringskontot.|BlobType, Tier|
|BlobProvisionedSize|No|Blob-etableringsstorlek|Byte|Genomsnitt|Mängden lagringsutrymme som etableras i lagringskontots Blob Service i byte.|BlobType, Tier|
|ContainerCount|Yes|Antal blobcontainrar|Antal|Genomsnitt|Antalet containrar i lagringskontot.|Inga dimensioner|
|Utgående|Yes|Utgående|Byte|Totalt|Mängden utgående data. Det här numret omfattar utgående data till externa klienter Azure Storage och utgående data i Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|GeoType, ApiName, Autentisering|
|IndexCapacity|No|Indexkapacitet|Byte|Genomsnitt|Mängden lagringsutrymme som används av Azure Data Lake Storage Gen2 hierarkiskt index.|Inga dimensioner|
|Ingress|Yes|Ingress|Byte|Totalt|Mängden inkommande data i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|GeoType, ApiName, Autentisering|
|SuccessE2ELatency|Yes|Lyckad E2E-svarstid|Millisekunder|Genomsnitt|Den genomsnittliga svarstiden från slutet till slut för lyckade begäranden som görs till en lagringstjänst eller den angivna API-åtgärden, i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|GeoType, ApiName, Autentisering|
|SuccessServerLatency|Yes|Lyckad serversvarstid|Millisekunder|Genomsnitt|Den genomsnittliga tiden det tar för Azure Storage att bearbeta en lyckad begäran. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|GeoType, ApiName, Autentisering|
|Transaktioner|Yes|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType-dimension för antalet olika svarstyper.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft.Storage/storageAccounts/fileServices

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Yes|Tillgänglighet|Procent|Genomsnitt|Procentandelen tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType, ApiName, Authentication, FileShare|
|Utgående|Yes|Utgående|Byte|Totalt|Mängden utgående data. Det här numret omfattar utgående till extern klient från Azure Storage samt utgående i Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|GeoType, ApiName, Authentication, FileShare|
|FileCapacity|No|Filkapacitet|Byte|Genomsnitt|Mängden File Storage som används av lagringskontot.|FileShare|
|FileCount|No|Antal filer|Antal|Genomsnitt|Antalet filer i lagringskontot.|FileShare|
|FileShareCapacityQuota|No|Kapacitetskvot för filresurs|Byte|Genomsnitt|Den övre gränsen för mängden lagringsutrymme som kan användas av Azure Files Service i byte.|FileShare|
|FileShareCount|No|Antal filresurs|Antal|Genomsnitt|Antalet filresurser i lagringskontot.|Inga dimensioner|
|FileShareProvisionedIOPS|No|Filresursetableerad IOPS|Byte|Genomsnitt|Baslinjenumret för etablerade IOPS för Premium-filresursen i lagringskontot för Premium-filer. Det här antalet beräknas baserat på den etablerade storleken (kvoten) för resurskapaciteten.|FileShare|
|FileShareSnapshotCount|No|Antal ögonblicksbilder av filresurs|Antal|Genomsnitt|Antalet ögonblicksbilder som finns på resursen i lagringskontots Files Service.|FileShare|
|FileShareSnapshotSize|No|Storlek på ögonblicksbild av filresurs|Byte|Genomsnitt|Mängden lagringsutrymme som används av ögonblicksbilderna i lagringskontots filtjänst i byte.|FileShare|
|Ingress|Yes|Ingress|Byte|Totalt|Mängden inkommande data i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|GeoType, ApiName, Autentisering, Filresurs|
|SuccessE2ELatency|Yes|Lyckad E2E-svarstid|Millisekunder|Genomsnitt|Den genomsnittliga svarstiden från slutet till slut för lyckade begäranden som görs till en lagringstjänst eller den angivna API-åtgärden, i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|GeoType, ApiName, Authentication, FileShare|
|SuccessServerLatency|Yes|Lyckad serversvarstid|Millisekunder|Genomsnitt|Den genomsnittliga tiden det tar för Azure Storage att bearbeta en lyckad begäran. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|GeoType, ApiName, Authentication, FileShare|
|Transaktioner|Yes|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType-dimension för antalet olika svarstyper.|ResponseType, GeoType, ApiName, Authentication, FileShare|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft.Storage/storageAccounts/queueServices

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Yes|Tillgänglighet|Procent|Genomsnitt|Procentandelen tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType, ApiName, Autentisering|
|Utgående|Yes|Utgående|Byte|Totalt|Mängden utgående data. Det här numret omfattar utgående till extern klient från Azure Storage samt utgående i Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|GeoType, ApiName, Autentisering|
|Ingress|Yes|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|GeoType, ApiName, Autentisering|
|QueueCapacity|Yes|Kökapacitet|Byte|Genomsnitt|Mängden Queue Storage som används av lagringskontot.|Inga dimensioner|
|QueueCount|Yes|Antal köer|Antal|Genomsnitt|Antalet köer i lagringskontot.|Inga dimensioner|
|QueueMessageCount|Yes|Antal kömeddelanden|Antal|Genomsnitt|Antalet kömeddelanden som inte har förts in i lagringskontot.|Inga dimensioner|
|SuccessE2ELatency|Yes|Lyckad E2E-svarstid|Millisekunder|Genomsnitt|Den genomsnittliga svarstiden från slutet till slut för lyckade begäranden som görs till en lagringstjänst eller den angivna API-åtgärden, i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|GeoType, ApiName, Autentisering|
|SuccessServerLatency|Yes|Lyckad serversvarstid|Millisekunder|Genomsnitt|Den genomsnittliga tiden det tar för Azure Storage att bearbeta en lyckad begäran. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|GeoType, ApiName, Autentisering|
|Transaktioner|Yes|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType-dimensionen för antalet olika typer av svar.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft.Storage/storageAccounts/tableServices

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Yes|Tillgänglighet|Procent|Genomsnitt|Procentandelen tillgänglighet för lagringstjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|GeoType, ApiName, Autentisering|
|Utgående|Yes|Utgående|Byte|Totalt|Mängden utgående data. Det här numret omfattar utgående till extern klient från Azure Storage samt utgående i Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|GeoType, ApiName, Autentisering|
|Ingress|Yes|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|GeoType, ApiName, Autentisering|
|SuccessE2ELatency|Yes|Lyckad E2E-svarstid|Millisekunder|Genomsnitt|Den genomsnittliga svarstiden från slutet till slut för lyckade begäranden som görs till en lagringstjänst eller den angivna API-åtgärden, i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|GeoType, ApiName, Autentisering|
|SuccessServerLatency|Yes|Lyckad serversvarstid|Millisekunder|Genomsnitt|Den genomsnittliga tiden det tar för Azure Storage att bearbeta en lyckad begäran. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|GeoType, ApiName, Autentisering|
|TableCapacity|Yes|Tabellkapacitet|Byte|Genomsnitt|Mängden Table Storage som används av lagringskontot.|Inga dimensioner|
|TableCount|Yes|Antal tabeller|Antal|Genomsnitt|Antalet tabeller i lagringskontot.|Inga dimensioner|
|TableEntityCount|Yes|Antal entiteter i tabellen|Antal|Genomsnitt|Antalet tabellentiteter i lagringskontot.|Inga dimensioner|
|Transaktioner|Yes|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType-dimensionen för antalet olika typer av svar.|ResponseType, GeoType, ApiName, Authentication|


## <a name="microsoftstoragecachecaches"></a>Microsoft.StorageCache/cacheminnen

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ClientIOPS|Yes|Totalt antal klient-IOPS|Antal|Genomsnitt|Frekvensen för klientfilåtgärder som bearbetas av cachen.|Inga dimensioner|
|ClientLatency|Yes|Genomsnittlig klientsvarstid|Millisekunder|Genomsnitt|Genomsnittlig svarstid för klientfilåtgärder till cachen.|Inga dimensioner|
|ClientLockIOPS|Yes|IOPS för klientlås|CountPerSecond|Genomsnitt|Klientfillåsningsåtgärder per sekund.|Inga dimensioner|
|ClientMetadataReadIOPS|Yes|Läs-IOPS för klientmetadata|CountPerSecond|Genomsnitt|Frekvensen för klientfilåtgärder som skickas till cachen, exklusive dataläsningar, som inte ändrar beständigt tillstånd.|Inga dimensioner|
|ClientMetadataWriteIOPS|Yes|Skriv-IOPS för klientmetadata|CountPerSecond|Genomsnitt|Frekvensen för klientfilåtgärder som skickas till cachen, exklusive data skrivningar, som ändrar beständigt tillstånd.|Inga dimensioner|
|ClientReadIOPS|Yes|Klientläsnings-IOPS|CountPerSecond|Genomsnitt|Klientläsningsåtgärder per sekund.|Inga dimensioner|
|ClientReadThroughput|Yes|Genomsnittligt cacheläsningsgenomflöde|BytePerSecond|Genomsnitt|Överföringshastighet för klientläsningsdata.|Inga dimensioner|
|ClientWriteIOPS|Yes|Klientskrivnings-IOPS|CountPerSecond|Genomsnitt|Klientskrivningsåtgärder per sekund.|Inga dimensioner|
|ClientWriteThroughput|Yes|Genomsnittligt cacheskrivningsgenomflöde|BytePerSecond|Genomsnitt|Överföringshastighet för klientskrivningsdata.|Inga dimensioner|
|StorageTargetAsyncWriteThroughput|Yes|StorageTarget asynkront skrivgenomflöde|BytesPerSecond|Genomsnitt|Den hastighet som cachen asynkront skriver data till en viss StorageTarget. Det här är opportunistiska skrivningar som inte gör att klienter blockerar.|StorageTarget|
|StorageTargetFillThroughput|Yes|StorageMåla fyllningsflöde|BytesPerSecond|Genomsnitt|Den hastighet som cachen läser data från StorageTarget för att hantera en cachemiss.|StorageTarget|
|StorageTargetHealth|Yes|Hälsotillstånd för lagringsmål|Antal|Genomsnitt|Booleska resultat av anslutningstestet mellan cache- och lagringsmålen.|Inga dimensioner|
|StorageTargetIOPS|Yes|Totalt antal StorageTarget IOPS|Antal|Genomsnitt|Frekvensen för alla filåtgärder som cachen skickar till en viss StorageTarget.|StorageTarget|
|StorageTargetLatency|Yes|LagringMålsvarstid|Millisekunder|Genomsnitt|Den genomsnittliga svarstiden för tur och retur för alla filåtgärder som cachen skickar till en annan StorageTarget.|StorageTarget|
|StorageTargetMetadataReadIOPS|Yes|StorageTarget Metadata Read IOPS|CountPerSecond|Genomsnitt|Frekvensen för filåtgärder som inte ändrar beständigt tillstånd, och exklusive läsåtgärden, som cachen skickar till ett visst StorageTarget.|StorageTarget|
|StorageTargetMetadataWriteIOPS|Yes|StorageTarget Metadata Write IOPS|CountPerSecond|Genomsnitt|Frekvensen för filåtgärder som ändrar beständigt tillstånd och exklusive skrivåtgärden, som Cache skickar till en viss StorageTarget.|StorageTarget|
|StorageTargetReadAheadThroughput|Yes|StorageTarget Read Ahead Throughput|BytePerSecond|Genomsnitt|Den hastighet som cacheminnet läser data från StorageTarget.|StorageTarget|
|StorageTargetReadIOPS|Yes|StorageTarget Read IOPS|CountPerSecond|Genomsnitt|Frekvensen för filläsningsåtgärder som cachen skickar till en viss StorageTarget.|StorageTarget|
|StorageTargetSyncWriteThroughput|Yes|StorageTarget synkront skrivgenomflöde|BytesPerSecond|Genomsnitt|Den hastighet som cachen synkront skriver data till en viss StorageTarget. Det här är skrivningar som gör att klienter blockerar.|StorageTarget|
|StorageTargetTotalReadThroughput|Yes|StorageMåla totalt läsgenomflöde|BytesPerSecond|Genomsnitt|Den totala hastigheten som cachen läser data från en viss StorageTarget.|StorageTarget|
|StorageTargetTotalWriteThroughput|Yes|StorageMåla totalt skrivgenomflöde|BytesPerSecond|Genomsnitt|Den totala hastigheten som cachen skriver data till en viss StorageTarget.|StorageTarget|
|StorageTargetWriteIOPS|Yes|StorageTarget– skriv-IOPS|Antal|Genomsnitt|Frekvensen för filskrivningsåtgärder som cachen skickar till en viss StorageTarget.|StorageTarget|
|Drifttid|Yes|Drifttid|Antal|Genomsnitt|Booleska resultat av anslutningstestet mellan cache- och övervakningssystemet.|Inga dimensioner|


## <a name="microsoftstoragesyncstoragesyncservices"></a>microsoft.storagesync/storageSyncServices

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ServerSyncSessionResult|Yes|Synkroniseringssessionsresultat|Antal|Genomsnitt|Mått som loggar värdet 1 varje gång serverslutpunkten slutför en synkroniseringssession med molnslutpunkten|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Yes|Synkroniserade byte|Byte|Totalt|Total filstorlek överförd för synkroniseringssessioner|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncRecallComputedSuccessRate|Yes|Frekvens för lyckade återkallningsnivåer för molnnivåinkallning|Procent|Genomsnitt|Procentandel av alla återkallningar som lyckades|SyncGroupName, ServerName|
|StorageSyncRecalledNetworkBytesByApplication|Yes|Storlek på återkallande av molnnivå efter program|Byte|Totalt|Storleken på data som återkallas av programmet|SyncGroupName, ServerName, ApplicationName|
|StorageSyncRecalledTotalNetworkBytes|Yes|Storlek för återkallande av molnnivå|Byte|Totalt|Storlek på återkallade data|SyncGroupName, ServerName|
|StorageSyncRecallIOTotalSizeBytes|Yes|Återkallande av molnnivåinkallning|Byte|Totalt|Total storlek på data som återkallats av servern|ServerName|
|StorageSyncRecallThroughputBytesPerSecond|Yes|Dataflöde för återkallande av molnnivåinkallning|BytesPerSecond|Genomsnitt|Dataflödesstorlek för data recall|SyncGroupName, ServerName|
|StorageSyncServerPoolbeat|Yes|Server Online Status|Antal|Maximal|Mått som loggar värdet 1 varje gång den återsigererade servern registrerar ett pulsslag med molnslutpunkten|ServerName|
|StorageSyncSyncSessionAppliedFilesCount|Yes|Synkroniserade filer|Antal|Totalt|Antal synkroniserade filer|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Yes|Filer synkroniseras inte|Antal|Totalt|Det gick inte att synkronisera antalet filer|SyncGroupName, ServerEndpointName, SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>microsoft.storagesync/storageSyncServices/registeredServers

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Server Enbeat|Yes|Server Online Status|Antal|Maximal|Mått som loggar värdet 1 varje gång den återselade servern registrerar ett pulsslag med molnslutpunkten|ServerResourceId, ServerName|
|ServerRecallIOTotalSizeBytes|Yes|Återkalla lagringsnivåer för moln|Byte|Totalt|Total storlek på data som återkallats av servern|ServerResourceId, ServerName|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>microsoft.storagesync/storageSyncServices/syncGroups

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|SyncGroupBatchTransferredFileBytes|Yes|Synkroniserade byte|Byte|Totalt|Total filstorlek överförd för synkroniseringssessioner|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionAppliedFilesCount|Yes|Synkroniserade filer|Antal|Totalt|Antal synkroniserade filer|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|Yes|Filer synkroniseras inte|Antal|Totalt|Det gick inte att synkronisera antalet filer|SyncGroupName, ServerEndpointName, SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>microsoft.storagesync/storageSyncServices/syncGroups/serverEndpoints

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ServerEndpointBatchTransferredFileBytes|Yes|Synkroniserade byte|Byte|Totalt|Total filstorlek överförd för synkroniseringssessioner|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionAppliedFilesCount|Yes|Synkroniserade filer|Antal|Totalt|Antal synkroniserade filer|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|Yes|Filer synkroniseras inte|Antal|Totalt|Det gick inte att synkronisera antalet filer|ServerEndpointName, SyncDirection|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|AMLCalloutFailedRequests|Yes|Misslyckade funktionsbegäranden|Antal|Totalt|Misslyckade funktionsbegäranden|LogicalName, PartitionId|
|AMLCalloutInputEvents|Yes|Funktionshändelser|Antal|Totalt|Funktionshändelser|LogicalName, PartitionId|
|AMLCalloutRequests|Yes|Funktionsbegäranden|Antal|Totalt|Funktionsbegäranden|LogicalName, PartitionId|
|ConversionErrors|Yes|Datakonverteringsfel|Antal|Totalt|Datakonverteringsfel|LogicalName, PartitionId|
|DeserializationError|Yes|Deserialiseringsfel för indata|Antal|Totalt|Deserialiseringsfel för indata|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|Yes|Händelser i ordningsföljd|Antal|Totalt|Händelser i ordningsföljd|LogicalName, PartitionId|
|EarlyInputEvents|Yes|Tidiga indatahändelser|Antal|Totalt|Tidiga indatahändelser|LogicalName, PartitionId|
|Fel|Yes|Körningsfel|Antal|Totalt|Körningsfel|LogicalName, PartitionId|
|InputEventBytes|Yes|Byte för indatahändelse|Byte|Totalt|Byte för indatahändelse|LogicalName, PartitionId|
|InputEvents|Yes|Indatahändelser|Antal|Totalt|Indatahändelser|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|Yes|Bakåtloggade indatahändelser|Antal|Maximal|Bakåtloggade indatahändelser|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|Yes|Mottagna indatakällor|Antal|Totalt|Mottagna indatakällor|LogicalName, PartitionId|
|LateInputEvents|Yes|Händelser med försenad indata|Antal|Totalt|Händelser med försenad indata|LogicalName, PartitionId|
|OutputEvents|Yes|Utdatahändelser|Antal|Totalt|Utdatahändelser|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|Yes|Fördröjning för vattenstämpel|Sekunder|Maximal|Fördröjning för vattenstämpel|LogicalName, PartitionId|
|ProcessCPUUsagePercentage|Yes|Processoranvändning i procent (förhandsversion)|Procent|Maximal|Processoranvändning i procent (förhandsversion)|LogicalName, PartitionId|
|ResourceUtilization|Yes|SU % utnyttjande|Procent|Maximal|SU % utnyttjande|LogicalName, PartitionId|


## <a name="microsoftsynapseworkspaces"></a>Microsoft.Synapse/workspaces
|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|BuiltinSqlPoolDataProcessedBytes|No|Bearbetade data (byte)|Byte|Totalt|Mängden data som bearbetas av frågor|Inga dimensioner|
|BuiltinSqlPoolLoginAttempts|No|Inloggningsförsök|Antal|Totalt|Antal inloggningsförsök som har lyckats eller misslyckats|Resultat|
|BuiltinSqlPoolRequestsEnded|No|Begäranden som avslutats|Antal|Totalt|Antal begäranden som har lyckats, misslyckats eller avbrutits|Resultat|
|IntegrationActivityRunsEnded|No|Aktivitetskörningar har avslutats|Antal|Totalt|Antal integreringsaktiviteter som har lyckats, misslyckats eller avbrutits|Result, FailureType, Activity, ActivityType, Pipeline|
|IntegrationPipelineRunsEnded|No|Pipelinekörningar avslutades|Antal|Totalt|Antal integreringspipelinekörningar som har lyckats, misslyckats eller avbrutits|Result, FailureType, Pipeline|
|IntegrationTriggerRunsEnded|No|Utlösarkörningar har avslutats|Antal|Totalt|Antal integreringsutlösare som har lyckats, misslyckats eller avbrutits|Result, FailureType, Trigger|
|SQLStreamingBackloggedInputEventSources|No|Bakåtloggade indatahändelser (förhandsversion)|Antal|Totalt|Det här är ett förhandsgranskningsmått som är tillgängligt i USA, östra, Europa, västra. Antalet inkommande händelsekällor som har loggats in igen.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingConversionErrors|No|Datakonverteringsfel (förhandsversion)|Antal|Totalt|Det här är ett förhandsgranskningsmått som är tillgängligt i USA, östra, Europa, västra. Antal utdatahändelser som inte kunde konverteras till det förväntade utdataschemat. Felprincipen kan ändras till "Drop" för att ta bort händelser som stöter på det här scenariot.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingDeserializationError|No|Deserialiseringsfel för indata (förhandsversion)|Antal|Totalt|Det här är ett förhandsgranskningsmått som är tillgängligt i USA, östra, Europa, västra. Antal indatahändelser som inte kunde deserialiseras.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingEarlyInputEvents|No|Tidiga indatahändelser (förhandsversion)|Antal|Totalt|Det här är ett förhandsgranskningsmått som är tillgängligt i USA, östra, Europa, västra. Antal indatahändelser som programtiden anses vara tidig jämfört med ankomsttiden, enligt principen för tidig ankomst.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingInputEventBytes|No|Byte för indatahändelse (förhandsversion)|Antal|Totalt|Det här är ett förhandsgranskningsmått som är tillgängligt i USA, östra, Europa, västra. Mängden data som tas emot av strömningsjobbet, i byte. Detta kan användas för att verifiera att händelser skickas till indatakällan.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingInputEvents|No|Indatahändelser (förhandsversion)|Antal|Totalt|Det här är ett förhandsgranskningsmått som är tillgängligt i USA, östra, Europa, västra. Antal indatahändelser.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingInputEventsSourcesPerSecond|No|Mottagna indatakällor (förhandsversion)|Antal|Totalt|Det här är ett förhandsgranskningsmått som är tillgängligt i USA, östra, Europa, västra. Antal indatahändelser per sekund.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingLateInputEvents|No|Händelser med försenad indata (förhandsversion)|Antal|Totalt|Det här är ett förhandsgranskningsmått som är tillgängligt i USA, östra, Europa, västra. Antal indatahändelser som programtiden anses vara försenad jämfört med ankomsttiden, enligt principen för försenad ankomst.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingOutOfOrderEvents|No|Händelser i ordningsföljd (förhandsversion)|Antal|Totalt|Det här är ett förhandsgranskningsmått som är tillgängligt i USA, östra, Europa, västra. Antal händelser i händelsehubben (serialiserade meddelanden) som tagits emot av händelsehubbens indataadapter, som tagits emot i oordning och som antingen tagits bort eller fått en justerad tidsstämpel, baserat på händelseordningsprincipen.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingOutputEvents|No|Utdatahändelser (förhandsversion)|Antal|Totalt|Det här är ett förhandsgranskningsmått som är tillgängligt i USA, östra, Europa, västra. Antal utdatahändelser.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingOutputWatermarkDelaySeconds|No|Vattenstämpelfördröjning (förhandsversion)|Antal|Maximal|Det här är ett förhandsgranskningsmått som är tillgängligt i USA, östra, Europa, västra. Vattenstämpelfördröjning i sekunder.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingResourceUtilization|No|Resursanvändning i procent (förhandsversion)|Procent|Maximal|Det här är ett förhandsgranskningsmått som är tillgängligt i USA, östra, Europa, västra.
 Resursutnyttjande uttryckt i procent. Hög användning anger att jobbet använder nära det högsta allokerade antalet resurser.|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|
|SQLStreamingRuntimeErrors|No|Körningsfel (förhandsversion)|Antal|Totalt|Det här är ett förhandsgranskningsmått som är tillgängligt i USA, östra, Europa, västra. Totalt antal fel som rör frågebearbetning (exklusive fel som påträffas vid inmatning av händelser eller utdataresultat).|ResourceName, SQLPoolName, SQLDatabaseName, JobName, LogicalName, PartitionId, ProcessorInstance|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft.Synapse/workspaces/bigDataPools

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|BigDataPoolAllocatedCores|No|Allokerade virtuella kärnor|Antal|Maximal|Allokerade virtuella kärnor för en Apache Spark pool|SubmitterId|
|BigDataPoolAllocatedMemory|No|Allokerat minne (GB)|Antal|Maximal|Allokerat minne för Apach Spark-pool (GB)|SubmitterId|
|BigDataPoolApplicationsActive|No|Aktiva Apache Spark program|Antal|Maximal|Totalt antal aktiva Apache Spark poolprogram|JobState|
|BigDataPoolApplicationsEnded|No|Avslutade Apache Spark program|Antal|Totalt|Antal program Apache Spark har avslutats|JobType, JobResult|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft.Synapse/workspaces/sqlPools

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ActiveQueries|No|Aktiva frågor|Antal|Totalt|Aktiva frågor. Om du använder det här måttet ofiltrerat och intesplit visas alla aktiva frågor som körs i systemet|IsUserDefined|
|AdaptiveCacheHitPercent|No|Adaptiv cache träffprocent|Procent|Maximal|Mäter hur bra arbetsbelastningar använder den anpassningsbara cachen. Använd det här måttet med måttet träffprocent för cache för att avgöra om du ska skala om för ytterligare kapacitet eller köra om arbetsbelastningar för att eskalera cachen|Inga dimensioner|
|AdaptiveCacheUsedPercent|No|Procent adaptiv cache som används|Procent|Maximal|Mäter hur bra arbetsbelastningar använder den anpassningsbara cachen. Använd det här måttet med det cachelagrade procentmåttet för att avgöra om du ska skala om för ytterligare kapacitet eller köra om arbetsbelastningar för att cachen ska skalas om|Inga dimensioner|
|Anslutningar|Yes|Anslutningar|Antal|Totalt|Antal totala inloggningar till SQL-poolen|Resultat|
|AnslutningarBlockedByFirewall|No|Anslutningar som blockeras av brandväggen|Antal|Totalt|Antal anslutningar som blockerats av brandväggsregler. Gå tillbaka till åtkomstkontrollprinciper för din SQL-pool och övervaka dessa anslutningar om antalet är högt|Inga dimensioner|
|CPUPercent|No|Processoranvändning i procent|Procent|Maximal|CPU-användning över alla noder i SQL-poolen|Inga dimensioner|
|DWULimit|No|DWU-gräns|Antal|Maximal|Servicenivåmål för SQL-poolen|Inga dimensioner|
|DWUUsed|No|DWU används|Antal|Maximal|Representerar en högnivårepresentation av användningen i SQL-poolen. Mäts efter DWU-gräns * DWU-procent|Inga dimensioner|
|DWUUsedPercent|No|DWU-använt procenttal|Procent|Maximal|Representerar en högnivårepresentation av användningen i SQL-poolen. Mäts genom att ta maxvärdet mellan CPU-procent och data-I/S-procent|Inga dimensioner|
|LocalTempDBUsedPercent|No|Använt procenttal för lokal tempdb|Procent|Maximal|Lokal tempdb-användning för alla beräkningsnoder – värden genereras var femte minut|Inga dimensioner|
|MemoryUsedPercent|No|Använt minne i procent|Procent|Maximal|Minnesanvändning över alla noder i SQL-poolen|Inga dimensioner|
|QueuedQueries|No|Köade frågor|Antal|Totalt|Kumulativt antal begäranden i kö efter att den maximala samtidighetsgränsen har nåtts|IsUserDefined|
|WLGActiveQueries|No|Aktiva frågor för arbetsbelastningsgrupp|Antal|Totalt|Aktiva frågor i arbetsbelastningsgruppen. Om du använder det här måttet ofiltrerat och osplitt visas alla aktiva frågor som körs i systemet|IsUserDefined, WorkloadGroup|
|WLGActiveQueriesTimeouts|No|Tidsgränser för frågor för arbetsbelastningsgrupp|Antal|Totalt|Frågor för den arbetsbelastningsgrupp som har en time out. Tidsgränser för frågor som rapporteras av det här måttet är bara när frågan har börjat köras (det omfattar inte väntetid på grund av låsning eller resursväntetid)|IsUserDefined, WorkloadGroup|
|WLGAllocationByEffectiveCapResourcePercent|No|Allokering av arbetsbelastningsgrupp efter maximal resursprocent|Procent|Maximal|Visar den procentuella allokeringen av resurser i förhållande till effektiv takresursprocent per arbetsbelastningsgrupp. Det här måttet ger arbetsbelastningsgruppens effektiva användning|IsUserDefined, WorkloadGroup|
|WLGAllocationBySystemPercent|No|Allokering av arbetsbelastningsgrupp efter systemprocent|Procent|Maximal|Den procentuella allokeringen av resurser i förhållande till hela systemet|IsUserDefined, WorkloadGroup|
|WLGEffectiveCapResourcePercent|No|Effektiv takresursprocent|Procent|Maximal|Den effektiva takresursprocenten för arbetsbelastningsgruppen. Om det finns andra arbetsbelastningsgrupper min_percentage_resource > 0 sänks effective_cap_percentage_resource proportionellt|IsUserDefined, WorkloadGroup|
|WLGEffectiveMinResourcePercent|No|Effektiv minsta resursprocent|Procent|Maximal|Den effektiva minsta tillåtna resursprocentinställningen med hänsyn till inställningarna för tjänstnivå och arbetsbelastningsgrupp. Den effektiva min_percentage_resource kan justeras högre på lägre servicenivåer|IsUserDefined, WorkloadGroup|
|WLGQueuedQueries|No|Frågor i kö för arbetsbelastningsgrupp|Antal|Totalt|Kumulativt antal begäranden i kö efter att den maximala samtidighetsgränsen har nåtts|IsUserDefined, WorkloadGroup|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft.TimeSeriesInsights/environments

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|Yes|Mottagna inkommande byte|Byte|Totalt|Antal byte som lästs från alla händelsekällor|Inga dimensioner|
|IngressReceivedInvalidMessages|Yes|Ogiltiga meddelanden för ingressen togs emot|Antal|Totalt|Antal ogiltiga meddelanden som lästs från alla händelsehubben eller IoT-hubbens händelsekällor|Inga dimensioner|
|IngressReceivedMessages|Yes|Mottagna meddelanden för inkommande|Antal|Totalt|Antal meddelanden som lästs från alla händelsehubben eller IoT Hub-händelsekällor|Inga dimensioner|
|IngressReceivedMessagesCountLag|Yes|Fördröjning för antal mottagna meddelanden för inkommande meddelanden|Antal|Genomsnitt|Skillnaden mellan sekvensnumret för det senaste i ordningsföljdsmeddelandet i händelsekällans partition och sekvensantalet meddelanden som bearbetas i ingressen|Inga dimensioner|
|IngressReceivedMessagesTimeLag|Yes|Tidsfördröjning för mottagna inkommande meddelanden|Sekunder|Maximal|Skillnaden mellan den tid då meddelandet står i en dialogrering i händelsekällan och den tid det bearbetas i ingressen|Inga dimensioner|
|IngressStoredBytes|Yes|Inkommande lagrade byte|Byte|Totalt|Total storlek på händelser som har bearbetats och är tillgängliga för fråga|Inga dimensioner|
|IngressStoredEvents|Yes|Ingress-lagrade händelser|Antal|Totalt|Antal utplattade händelser som har bearbetats och är tillgängliga för frågor|Inga dimensioner|
|WarmStorageMaxProperties|Yes|Maxegenskaper för varm lagring|Antal|Maximal|Maximalt antal egenskaper som tillåts av miljön för S1/S2 SKU och maximalt antal egenskaper som tillåts av varmlager för PAYG SKU|Inga dimensioner|
|WarmStorageUsedProperties|Yes|Egenskaper som används för varm lagring |Antal|Maximal|Antal egenskaper som används av miljön för S1/S2 SKU och antalet egenskaper som används av Varmlager för PAYG SKU|Inga dimensioner|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft.TimeSeriesInsights/environments/eventsources

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|Yes|Mottagna inkommande byte|Byte|Totalt|Antal byte som lästs från händelsekällan|Inga dimensioner|
|IngressReceivedInvalidMessages|Yes|Ogiltiga meddelanden för ingressen togs emot|Antal|Totalt|Antal ogiltiga meddelanden som lästs från händelsekällan|Inga dimensioner|
|IngressReceivedMessages|Yes|Mottagna meddelanden för inkommande|Antal|Totalt|Antal meddelanden som lästs från händelsekällan|Inga dimensioner|
|IngressReceivedMessagesCountLag|Yes|Fördröjning för antal mottagna meddelanden för inkommande meddelanden|Antal|Genomsnitt|Skillnaden mellan sekvensnumret för det senaste i ordningsföljdsmeddelandet i händelsekällans partition och sekvensantalet meddelanden som bearbetas i ingressen|Inga dimensioner|
|IngressReceivedMessagesTimeLag|Yes|Tidsfördröjning för mottagna inkommande meddelanden|Sekunder|Maximal|Skillnaden mellan den tid då meddelandet står i en dialogrering i händelsekällan och den tid det bearbetas i ingressen|Inga dimensioner|
|IngressStoredBytes|Yes|Inkommande lagrade byte|Byte|Totalt|Total storlek på händelser som har bearbetats och är tillgängliga för fråga|Inga dimensioner|
|IngressStoredEvents|Yes|Ingress-lagrade händelser|Antal|Totalt|Antal utplattade händelser som har bearbetats och är tillgängliga för frågor|Inga dimensioner|
|WarmStorageMaxProperties|Yes|Maxegenskaper för varm lagring|Antal|Maximal|Maximalt antal egenskaper som tillåts av miljön för S1/S2 SKU och maximalt antal egenskaper som tillåts av varmlager för PAYG SKU|Inga dimensioner|
|WarmStorageUsedProperties|Yes|Egenskaper som används för varm lagring |Antal|Maximal|Antal egenskaper som används av miljön för S1/S2 SKU och antalet egenskaper som används av Varmlager för PAYG SKU|Inga dimensioner|


## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft.VMwareCloudSimple/virtualMachines

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Byte för diskläsning|Yes|Byte för diskläsning|Byte|Totalt|Totalt diskgenomflöde på grund av läsåtgärder under exempelperioden.|Inga dimensioner|
|Diskläsningsåtgärder/s|Yes|Diskläsningsåtgärder/s|CountPerSecond|Genomsnitt|Det genomsnittliga antalet I/O-läsåtgärder i föregående exempelperiod. Observera att de här åtgärderna kan ha varierande storlek.|Inga dimensioner|
|Diskskrivningsbyte|Yes|Diskskrivningsbyte|Byte|Totalt|Totalt diskgenomflöde på grund av skrivåtgärder under exempelperioden.|Inga dimensioner|
|Diskskrivningsåtgärder/s|Yes|Diskskrivningsåtgärder/s|CountPerSecond|Genomsnitt|Det genomsnittliga antalet I/O-skrivåtgärder i föregående exempelperiod. Observera att de här åtgärderna kan ha varierande storlek.|Inga dimensioner|
|DiskReadBytesPerSecond|Yes|Byte/s för diskläsning|BytePerSecond|Genomsnitt|Genomsnittligt diskgenomflöde på grund av läsåtgärder under exempelperioden.|Inga dimensioner|
|DiskReadLatency|Yes|Svarstid för diskläsning|Millisekunder|Genomsnitt|Total läsfördröjning. Summan av enheten och svarstiderna för kernelläsning.|Inga dimensioner|
|DiskReadOperations|Yes|Diskläsningsåtgärder|Antal|Totalt|Antalet I/O-läsåtgärder i föregående exempelperiod. Observera att de här åtgärderna kan ha varierande storlek.|Inga dimensioner|
|DiskWriteBytesPerSecond|Yes|Diskskrivningsbyte/sek|BytesPerSecond|Genomsnitt|Genomsnittligt diskgenomflöde på grund av skrivåtgärder under exempelperioden.|Inga dimensioner|
|DiskWriteLatency|Yes|Svarstid för diskskrivning|Millisekunder|Genomsnitt|Total svarstid för skrivning. Summan av enhetens och kernelns skrivsvarstid.|Inga dimensioner|
|DiskWriteOperations|Yes|Diskskrivningsåtgärder|Antal|Totalt|Antalet I/O-skrivåtgärder i föregående exempelperiod. Observera att de här åtgärderna kan ha varierande storlek.|Inga dimensioner|
|MemoryActive|Yes|Aktivt minne|Byte|Genomsnitt|Mängden minne som används av den virtuella datorn under de senaste små tidsperioderna. Det här är det "sanna" antalet av hur mycket minne den virtuella datorn för närvarande behöver. Ytterligare, oanvänt minne kan växlas ut eller ballongas utan att gästens prestanda påverkas.|Inga dimensioner|
|MemoryGranted|Yes|Beviljat minne|Byte|Genomsnitt|Mängden minne som har beviljats till den virtuella datorn av värden. Minnet beviljas inte till värden förrän det har berörts en gång och minnet kan växlas ut eller ballongas bort om VMkernel behöver minnet.|Inga dimensioner|
|MemoryUsed|Yes|Använt minne|Byte|Genomsnitt|Mängden datorminne som används av den virtuella datorn.|Inga dimensioner|
|Nätverk – inkommande|Yes|Nätverk – inkommande|Byte|Totalt|Totalt nätverksgenomflöde för mottagen trafik.|Inga dimensioner|
|Nätverk – utgående|Yes|Nätverk – utgående|Byte|Totalt|Totalt nätverksgenomflöde för överförd trafik.|Inga dimensioner|
|NetworkInBytesPerSecond|Yes|Nätverk i byte/sek|BytesPerSecond|Genomsnitt|Genomsnittligt nätverksgenomflöde för mottagen trafik.|Inga dimensioner|
|NetworkOutBytesPerSecond|Yes|Utgående nätverksbyte/sek|BytesPerSecond|Genomsnitt|Genomsnittligt nätverksgenomflöde för överförd trafik.|Inga dimensioner|
|Processorprocentandel|Yes|Processorprocentandel|Procent|Genomsnitt|Processoranvändningen. Det här värdet rapporteras med 100 % som representerar alla processorkärnor i systemet. Till exempel använder en 2-vägs virtuell dator med 50 % av ett system med fyra kärnor helt två kärnor.|Inga dimensioner|
|PercentageCpuReady|Yes|Processorprocentandel klar|Millisekunder|Totalt|Redotid är den tid som väntar på att CPU:er ska bli tillgängliga under det senaste uppdateringsintervallet.|Inga dimensioner|


## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ActiveRequests|Yes|Aktiva begäranden (inaktuella)|Antal|Totalt|Aktiva begäranden|Instans|
|AverageResponseTime|Yes|Genomsnittlig svarstid (inaktuell)|Sekunder|Genomsnitt|Den genomsnittliga tid det tar för frontend att betjäna begäranden, i sekunder.|Instans|
|ByteReceived|Yes|Indata|Byte|Totalt|Den genomsnittliga inkommande bandbredden som används över alla frontend-ändarna, i MiB.|Instans|
|ByteSent|Yes|Data ut|Byte|Totalt|Den genomsnittliga inkommande bandbredden som används i hela frontend, i MiB.|Instans|
|CpuPercentage|Yes|CPU-procent|Procent|Genomsnitt|Den genomsnittliga processoranvändningen för alla instanser av frontend.|Instans|
|DiskQueueLength|Yes|Diskkölängd|Antal|Genomsnitt|Det genomsnittliga antalet både läs- och skrivbegäranden som har köat för lagring. En hög diskkölängd är en indikation på en app som kan bli långsammare på grund av överdriven disk-I/O.|Instans|
|Http101|Yes|HTTP 101|Antal|Totalt|Antalet begäranden som resulterar i http-statuskoden 101.|Instans|
|Http2xx|Yes|HTTP 2xx|Antal|Totalt|Antalet begäranden som resulterar i en HTTP-statuskod = 200 men < 300.|Instans|
|Http3xx|Yes|HTTP 3xx|Antal|Totalt|Antalet begäranden som resulterar i en HTTP-statuskod = 300 men < 400.|Instans|
|Http401|Yes|HTTP 401|Antal|Totalt|Antalet begäranden som resulterar i HTTP 401-statuskod.|Instans|
|Http403|Yes|HTTP 403|Antal|Totalt|Antalet begäranden som resulterar i HTTP 403-statuskod.|Instans|
|Http404|Yes|HTTP 404|Antal|Totalt|Antalet begäranden som resulterar i HTTP 404-statuskod.|Instans|
|Http406|Yes|HTTP 406|Antal|Totalt|Antalet begäranden som resulterar i HTTP 406-statuskod.|Instans|
|Http4xx|Yes|HTTP 4xx|Antal|Totalt|Antalet begäranden som resulterar i en HTTP-statuskod = 400 men < 500.|Instans|
|Http5xx|Yes|HTTP-serverfel|Antal|Totalt|Antalet begäranden som resulterar i en HTTP-statuskod = 500 men < 600.|Instans|
|HttpQueueLength|Yes|HTTP-kölängd|Antal|Genomsnitt|Det genomsnittliga antalet HTTP-begäranden som måste vara i kön innan de uppfylldes. En hög eller ökande HTTP-kölängd är ett symtom på en plan med hög belastning.|Instans|
|HttpResponseTime|Yes|Svarstid|Sekunder|Genomsnitt|Den tid det tar för frontend att betjäna begäranden, i sekunder.|Instans|
|LargeAppServicePlanInstances|Yes|Stora App Service planarbetare|Antal|Genomsnitt|Stora App Service planarbetare|Inga dimensioner|
|MediumAppServicePlanInstances|Yes|Medelstor App Service planarbetare|Antal|Genomsnitt|Medelstor App Service planarbetare|Inga dimensioner|
|MemoryPercentage|Yes|Minnesprocent|Procent|Genomsnitt|Genomsnittligt minne som används för alla instanser av frontend.|Instans|
|Begäranden|Yes|Begäranden|Antal|Totalt|Det totala antalet begäranden oavsett resulterande HTTP-statuskod.|Instans|
|SmallAppServicePlanInstances|Yes|Små App Service planarbetare|Antal|Genomsnitt|Små App Service planarbetare|Inga dimensioner|
|TotalFrontEnds|Yes|Totalt antal frontend-ändarna|Antal|Genomsnitt|Totalt antal frontend-ändarna|Inga dimensioner|


## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|CpuPercentage|Yes|CPU-procent|Procent|Genomsnitt|Den genomsnittliga processoranvändningen för alla instanser av arbetspoolen.|Instans|
|MemoryPercentage|Yes|Minnesprocent|Procent|Genomsnitt|Genomsnittligt minne som används för alla instanser av arbetspoolen.|Instans|
|WorkersAvailable|Yes|Tillgängliga arbetare|Antal|Genomsnitt|Tillgängliga arbetare|Inga dimensioner|
|WorkersTotal|Yes|Totalt antal arbetare|Antal|Genomsnitt|Totalt antal arbetare|Inga dimensioner|
|ArbetareAnvänds|Yes|Använda arbetare|Antal|Genomsnitt|Använda arbetare|Inga dimensioner|


## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ByteReceived|Yes|Indata|Byte|Totalt|Den genomsnittliga inkommande bandbredden som används för alla instanser av planen.|Instans|
|ByteSent|Yes|Data ut|Byte|Totalt|Den genomsnittliga utgående bandbredden som används för alla instanser av planen.|Instans|
|CpuPercentage|Yes|CPU-procent|Procent|Genomsnitt|Den genomsnittliga processoranvändningen för alla instanser av planen.|Instans|
|DiskQueueLength|Yes|Diskkölängd|Antal|Genomsnitt|Det genomsnittliga antalet både läs- och skrivbegäranden som har köat för lagring. En hög diskkölängd är en indikation på en app som kan bli långsammare på grund av överdriven disk-I/O.|Instans|
|HttpQueueLength|Yes|HTTP-kölängd|Antal|Genomsnitt|Det genomsnittliga antalet HTTP-begäranden som måste vara i kön innan de uppfylldes. En hög eller ökande HTTP-kölängd är ett symtom på en plan med hög belastning.|Instans|
|MemoryPercentage|Yes|Minnesprocent|Procent|Genomsnitt|Genomsnittligt minne som används för alla instanser av planen.|Instans|
|SocketInboundAll|Yes|SocketInboundAll|Antal|Genomsnitt|SocketInboundAll|Instans|
|SocketLoopback|Yes|SocketLoopback|Antal|Genomsnitt|SocketLoopback|Instans|
|SocketOutboundAll|Yes|SocketOutboundAll|Antal|Genomsnitt|SocketOutboundAll|Instans|
|SocketOutboundEstablished|Yes|SocketOutboundEstablished|Antal|Genomsnitt|SocketOutboundEstablished|Instans|
|SocketOutboundTimeWait|Yes|SocketOutboundTimeWait|Antal|Genomsnitt|SocketOutboundTimeWait|Instans|
|TcpCloseWait|Yes|TCP-stängningsvänte|Antal|Genomsnitt|TCP-stängningsvänte|Instans|
|TcpClosing|Yes|TCP-stängning|Antal|Genomsnitt|TCP-stängning|Instans|
|TcpEstablished|Yes|TCP upprättad|Antal|Genomsnitt|TCP upprättad|Instans|
|TcpFinWait1|Yes|TCP Fin Wait 1|Antal|Genomsnitt|TCP Fin Wait 1|Instans|
|TcpFinWait2|Yes|TCP Fin Wait 2|Antal|Genomsnitt|TCP Fin Wait 2|Instans|
|TcpLastAck|Yes|TCP Last Ack|Antal|Genomsnitt|TCP Last Ack|Instans|
|TcpSynReceived|Yes|TCP-syn mottagen|Antal|Genomsnitt|TCP-syn mottagen|Instans|
|TcpSynSent|Yes|TCP Syn Sent|Antal|Genomsnitt|TCP Syn Sent|Instans|
|TcpTimeWait|Yes|TCP-tidsväntetid|Antal|Genomsnitt|TCP-tidsväntetid|Instans|


## <a name="microsoftwebsites"></a>Microsoft.Web/sites

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|AppConnections|Yes|Anslutningar|Antal|Genomsnitt|Antalet bound sockets som finns i sandbox-miljön (w3wp.exe och dess underordnade processer). En bindningssocket skapas genom att bind()/connect() API:er anropas och förblir tills denna socket stängs med CloseHandle()/closesocket().|Instans|
|AverageMemoryWorkingSet|Yes|Genomsnittlig arbetsminnesuppsättning|Byte|Genomsnitt|Den genomsnittliga mängden minne som används av appen, i megabyte (MiB).|Instans|
|AverageResponseTime|Yes|Genomsnittlig svarstid (inaktuell)|Sekunder|Genomsnitt|Den genomsnittliga tid det tar för appen att betjäna begäranden, i sekunder.|Instans|
|ByteReceived|Yes|Indata|Byte|Totalt|Mängden inkommande bandbredd som förbrukas av appen i MiB.|Instans|
|ByteSent|Yes|Data ut|Byte|Totalt|Mängden utgående bandbredd som förbrukas av appen i MiB.|Instans|
|CpuTime|Yes|CPU-tid|Sekunder|Totalt|Mängden cpu som förbrukas av appen, i sekunder. Mer information om det här måttet. Gäller inte för Azure Functions. Se https://aka.ms/website-monitor-cpu-time-vs-cpu-percentage (CPU-tid jämfört med CPU-procent).|Instans|
|CurrentAssemblies|Yes|Aktuella sammansättningar|Antal|Genomsnitt|Det aktuella antalet sammansättningar som lästs in över alla AppDomains i det här programmet.|Instans|
|FileSystemUsage|Yes|Filsystemanvändning|Byte|Genomsnitt|Procentandelen filsystemskvot som förbrukas av appen.|Inga dimensioner|
|FunctionExecutionCount|Yes|Antal funktionskörningar|Antal|Totalt|Antal funktionskörningar. Finns endast för Azure Functions.|Instans|
|FunctionExecutionUnits|Yes|Funktionskörningsenheter|Antal|Totalt|Funktionskörningsenheter. Finns endast för Azure Functions.|Instans|
|Gen0Collections|Yes|Gen 0 Garbage Collections|Antal|Totalt|Antalet gånger som generering 0-objekt har skräpinsamlats sedan appprocessens början. GCs med högre generation innehåller alla GCs med lägre generation.|Instans|
|Gen1Collections|Yes|Gen 1 skräpinsamlingar|Antal|Totalt|Antalet gånger som objekten i generation 1 skräpinsamlas sedan appprocessens början. GCs med högre generation innehåller alla GCs med lägre generation.|Instans|
|Gen2Collections|Yes|Gen 2 skräpsamlingar|Antal|Totalt|Antalet gånger som objekten i generation 2 skräpinsamlas sedan appprocessens början.|Instans|
|Hanterar|Yes|Antal referenser|Antal|Genomsnitt|Det totala antalet referenser som för närvarande är öppna av appprocessen.|Instans|
|HealthCheckStatus|Yes|Hälsokontrollstatus|Antal|Genomsnitt|Hälsokontrollstatus|Instans|
|Http101|Yes|HTTP 101|Antal|Totalt|Antalet begäranden som resulterar i http-statuskoden 101.|Instans|
|Http2xx|Yes|HTTP 2xx|Antal|Totalt|Antalet begäranden som resulterar i en HTTP-statuskod = 200 men < 300.|Instans|
|Http3xx|Yes|HTTP 3xx|Antal|Totalt|Antalet begäranden som resulterar i en HTTP-statuskod = 300 men < 400.|Instans|
|Http401|Yes|HTTP 401|Antal|Totalt|Antalet begäranden som resulterar i HTTP 401-statuskod.|Instans|
|Http403|Yes|HTTP 403|Antal|Totalt|Antalet begäranden som resulterar i HTTP 403-statuskod.|Instans|
|Http404|Yes|HTTP 404|Antal|Totalt|Antalet begäranden som resulterar i HTTP 404-statuskod.|Instans|
|Http406|Yes|HTTP 406|Antal|Totalt|Antalet begäranden som resulterar i HTTP 406-statuskod.|Instans|
|Http4xx|Yes|HTTP 4xx|Antal|Totalt|Antalet begäranden som resulterar i en HTTP-statuskod = 400 men < 500.|Instans|
|Http5xx|Yes|HTTP-serverfel|Antal|Totalt|Antalet begäranden som resulterar i en HTTP-statuskod = 500 men < 600.|Instans|
|HttpResponseTime|Yes|Svarstid|Sekunder|Genomsnitt|Den tid det tar för appen att betjäna begäranden, i sekunder.|Instans|
|IoOtherBytesPerSecond|Yes|Övriga I/O-byte per sekund|BytePerSecond|Totalt|Den hastighet med vilken appprocessen utfärdar byte till I/O-åtgärder som inte omfattar data, till exempel kontrollåtgärder.|Instans|
|IoOtherOperationsPerSecond|Yes|Övriga I/O-åtgärder per sekund|BytePerSecond|Totalt|Den hastighet med vilken appprocessen utfärdar I/O-åtgärder som inte är läs- eller skrivåtgärder.|Instans|
|IoReadBytesPerSecond|Yes|I/O-byte för läsning per sekund|BytePerSecond|Totalt|Den hastighet med vilken appprocessen läser byte från I/O-åtgärder.|Instans|
|IoReadOperationsPerSecond|Yes|Läsåtgärder för I/O per sekund|BytesPerSecond|Totalt|Den hastighet med vilken appprocessen utfärdar läs-I/O-åtgärder.|Instans|
|IoWriteBytesPerSecond|Yes|I/O-skrivbyte per sekund|BytesPerSecond|Totalt|Den hastighet med vilken appprocessen skriver byte till I/O-åtgärder.|Instans|
|IoWriteOperationsPerSecond|Yes|I/O-skrivåtgärder per sekund|BytesPerSecond|Totalt|Den hastighet med vilken appprocessen utfärdar skriv-I/O-åtgärder.|Instans|
|MemoryWorkingSet|Yes|Minnesarbetsuppsättning|Byte|Genomsnitt|Den aktuella mängden minne som används av appen i MiB.|Instans|
|PrivateBytes|Yes|Privata byte|Byte|Genomsnitt|Privata byte är den aktuella storleken i byte av minne som appprocessen har allokerat och som inte kan delas med andra processer.|Instans|
|Begäranden|Yes|Begäranden|Antal|Totalt|Det totala antalet begäranden oavsett resulterande HTTP-statuskod.|Instans|
|RequestsInApplicationQueue|Yes|Begäranden i programkö|Antal|Genomsnitt|Antalet begäranden i kön för programbegäran.|Instans|
|Trådar|Yes|Antal trådar|Antal|Genomsnitt|Antalet trådar som för närvarande är aktiva i appprocessen.|Instans|
|TotalAppDomains|Yes|Totalt antal appdomäner|Antal|Genomsnitt|Det aktuella antalet AppDomains som lästs in i det här programmet.|Instans|
|TotalAppDomainsUnloaded|Yes|Totalt antal bortladdade appdomäner|Antal|Genomsnitt|Det totala antalet AppDomains som har inaktiverats sedan programmets start.|Instans|


## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|AppConnections|Yes|Anslutningar|Antal|Genomsnitt|Antalet bound sockets som finns i sandbox-miljön (w3wp.exe och dess underordnade processer). En bindningssocket skapas genom att bind()/connect() API:er anropas och förblir tills denna socket stängs med CloseHandle()/closesocket().|Instans|
|AverageMemoryWorkingSet|Yes|Genomsnittlig arbetsminnesuppsättning|Byte|Genomsnitt|Den genomsnittliga mängden minne som används av appen, i megabyte (MiB).|Instans|
|AverageResponseTime|Yes|Genomsnittlig svarstid (inaktuell)|Sekunder|Genomsnitt|Den genomsnittliga tid det tar för appen att betjäna begäranden, i sekunder.|Instans|
|ByteReceived|Yes|Indata|Byte|Totalt|Mängden inkommande bandbredd som förbrukas av appen i MiB.|Instans|
|ByteSent|Yes|Data ut|Byte|Totalt|Mängden utgående bandbredd som förbrukas av appen i MiB.|Instans|
|CpuTime|Yes|CPU-tid|Sekunder|Totalt|Mängden cpu som förbrukas av appen, i sekunder. Mer information om det här måttet. Gäller inte för Azure Functions. Se https://aka.ms/website-monitor-cpu-time-vs-cpu-percentage (CPU-tid jämfört med CPU-procent).|Instans|
|CurrentAssemblies|Yes|Aktuella sammansättningar|Antal|Genomsnitt|Det aktuella antalet sammansättningar som lästs in över alla AppDomains i det här programmet.|Instans|
|FileSystemUsage|Yes|Filsystemanvändning|Byte|Genomsnitt|Procentandelen filsystemskvot som förbrukas av appen.|Inga dimensioner|
|FunctionExecutionCount|Yes|Antal funktionskörningar|Antal|Totalt|Antal funktionskörningar|Instans|
|FunctionExecutionUnits|Yes|Funktionskörningsenheter|Antal|Totalt|Funktionskörningsenheter|Instans|
|Gen0Collections|Yes|Gen 0 Garbage Collections|Antal|Totalt|Antalet gånger som generering 0-objekt har skräpinsamlats sedan appprocessens början. GCs med högre generation innehåller alla GCs med lägre generation.|Instans|
|Gen1Collections|Yes|Gen 1 skräpinsamlingar|Antal|Totalt|Antalet gånger som objekten i generation 1 skräpinsamlas sedan appprocessens början. GCs med högre generation innehåller alla GCs med lägre generation.|Instans|
|Gen2Collections|Yes|Gen 2 skräpsamlingar|Antal|Totalt|Antalet gånger som objekten i generation 2 skräpinsamlas sedan appprocessens början.|Instans|
|Hanterar|Yes|Antal referenser|Antal|Genomsnitt|Det totala antalet referenser som för närvarande är öppna av appprocessen.|Instans|
|HealthCheckStatus|Yes|Hälsokontrollstatus|Antal|Genomsnitt|Hälsokontrollstatus|Instans|
|Http101|Yes|HTTP 101|Antal|Totalt|Antalet begäranden som resulterar i http-statuskoden 101.|Instans|
|Http2xx|Yes|HTTP 2xx|Antal|Totalt|Antalet begäranden som resulterar i en HTTP-statuskod = 200 men < 300.|Instans|
|Http3xx|Yes|HTTP 3xx|Antal|Totalt|Antalet begäranden som resulterar i en HTTP-statuskod = 300 men < 400.|Instans|
|Http401|Yes|HTTP 401|Antal|Totalt|Antalet begäranden som resulterar i HTTP 401-statuskod.|Instans|
|Http403|Yes|HTTP 403|Antal|Totalt|Antalet begäranden som resulterar i HTTP 403-statuskod.|Instans|
|Http404|Yes|HTTP 404|Antal|Totalt|Antalet begäranden som resulterar i HTTP 404-statuskod.|Instans|
|Http406|Yes|HTTP 406|Antal|Totalt|Antalet begäranden som resulterar i HTTP 406-statuskod.|Instans|
|Http4xx|Yes|HTTP 4xx|Antal|Totalt|Antalet begäranden som resulterar i en HTTP-statuskod = 400 men < 500.|Instans|
|Http5xx|Yes|HTTP-serverfel|Antal|Totalt|Antalet begäranden som resulterar i en HTTP-statuskod = 500 men < 600.|Instans|
|HttpResponseTime|Yes|Svarstid|Sekunder|Genomsnitt|Den tid det tar för appen att betjäna begäranden, i sekunder.|Instans|
|IoOtherBytesPerSecond|Yes|Övriga I/O-byte per sekund|BytePerSecond|Totalt|Den hastighet med vilken appprocessen utfärdar byte till I/O-åtgärder som inte omfattar data, till exempel kontrollåtgärder.|Instans|
|IoOtherOperationsPerSecond|Yes|Andra I/S-åtgärder per sekund|BytePerSecond|Totalt|Den hastighet med vilken appprocessen utfärdar I/O-åtgärder som inte är läs- eller skrivåtgärder.|Instans|
|IoReadBytesPerSecond|Yes|Läsbyte per sekund för I/O|BytesPerSecond|Totalt|Den hastighet med vilken appprocessen läser byte från I/O-åtgärder.|Instans|
|IoReadOperationsPerSecond|Yes|Läsåtgärder för I/O per sekund|BytesPerSecond|Totalt|Den hastighet med vilken appprocessen utfärdar läs-I/O-åtgärder.|Instans|
|IoWriteBytesPerSecond|Yes|I/O-skrivningsbyte per sekund|BytesPerSecond|Totalt|Den hastighet med vilken appprocessen skriver byte till I/O-åtgärder.|Instans|
|IoWriteOperationsPerSecond|Yes|I/O-skrivåtgärder per sekund|BytesPerSecond|Totalt|Den hastighet med vilken appprocessen utfärdar skriv-I/O-åtgärder.|Instans|
|MemoryWorkingSet|Yes|Minnesarbetsuppsättning|Byte|Genomsnitt|Den aktuella mängden minne som används av appen i MiB.|Instans|
|PrivateBytes|Yes|Privata byte|Byte|Genomsnitt|Privata byte är den aktuella storleken i byte av minne som appprocessen har allokerat och som inte kan delas med andra processer.|Instans|
|Begäranden|Yes|Begäranden|Antal|Totalt|Det totala antalet begäranden oavsett resulterande HTTP-statuskod.|Instans|
|RequestsInApplicationQueue|Yes|Begäranden i programkö|Antal|Genomsnitt|Antalet begäranden i kön för programbegäran.|Instans|
|Trådar|Yes|Antal trådar|Antal|Genomsnitt|Antalet trådar som för närvarande är aktiva i appprocessen.|Instans|
|TotalAppDomains|Yes|Totalt antal appdomäner|Antal|Genomsnitt|Det aktuella antalet AppDomains som lästs in i det här programmet.|Instans|
|TotalAppDomainsUnloaded|Yes|Totalt antal bortladdade appdomäner|Antal|Genomsnitt|Det totala antalet AppDomains som har inaktiverats sedan programmets start.|Instans|


## <a name="microsoftwebstaticsites"></a>Microsoft.Web/staticSites

|Metric|Kan exporteras via diagnostikinställningar?|Visningsnamn för mått|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ByteSent|Yes|Data ut|Byte|Totalt|ByteSent|Instans|
|FunctionErrors|Yes|FunctionErrors|Antal|Totalt|FunctionErrors|Instans|
|FunctionHits|Yes|FunctionHits|Antal|Totalt|FunctionHits|Instans|
|SiteErrors|Yes|SiteErrors|Antal|Totalt|SiteErrors|Instans|
|SiteHits|Yes|SiteHits|Antal|Totalt|SiteHits|Instans|

## <a name="next-steps"></a>Nästa steg

- [Läs mer om mått i Azure Monitor](../data-platform.md)
- [Skapa aviseringar för mått](../alerts/alerts-overview.md)
- [Exportera mått till lagring, Event Hub eller Log Analytics](../essentials/platform-logs-overview.md)
