---
title: Azure Monitor mått som stöds efter resurs typ
description: Lista över mått som är tillgängliga för varje resurs typ med Azure Monitor.
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 02/06/2021
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: 3f9ec395e8ccf6d5162717b2e38b0650ccc84812
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/18/2021
ms.locfileid: "101091789"
---
# <a name="supported-metrics-with-azure-monitor"></a>Mått som stöds med Azure Monitor

> [!NOTE]
> Den här listan genereras i stor skala. Alla ändringar som görs i den här listan via GitHub kan skrivas över utan varning. Kontakta författaren till den här artikeln om du vill ha mer information om hur du gör permanenta uppdateringar.

Azure Monitor tillhandahåller flera olika sätt att interagera med mått, inklusive att lägga till dem i portalen, komma åt dem via REST API eller genom att fråga dem med hjälp av PowerShell eller CLI. 

Den här artikeln är en fullständig lista över alla plattformar (dvs. automatiskt insamlade) mått som för närvarande är tillgängliga med Azure Monitor den konsoliderade mått pipelinen. Mått som har ändrats eller lagts till efter datumet överst i den här artikeln kanske inte visas än. Om du vill fråga efter och komma åt listan över mått program mässigt kan du använda [2018-01-01 API-versionen](/rest/api/monitor/metricdefinitions). Andra mått som inte finns med i listan kan finnas tillgängliga i portalen eller med äldre API: er.

Måtten är ordnade efter resurs leverantörer och resurs typ. En lista över tjänster och resurs leverantörer och typer som tillhör dem finns i [Resource providers för Azure-tjänster](../../azure-resource-manager/management/azure-services-resource-providers.md).  

## <a name="exporting-platform-metrics-to-other-locations"></a>Exportera plattforms mått till andra platser

Du kan exportera plattforms måtten från Azure Monitor-pipeline till andra platser på ett av två sätt.
1. Använd [måtten REST API](/rest/api/monitor/metrics/list)
2. Använd [diagnostikinställningar](../essentials/diagnostic-settings.md) för att dirigera plattforms mått till 
    - Azure Storage
    - Azure Monitor loggar (och därmed Log Analytics)
    - Event Hub, som är hur du får dem till andra system än Microsoft 

Att använda diagnostikinställningar är det enklaste sättet att dirigera mått, men det finns vissa begränsningar: 

- Det går **inte att exportera** – alla mått kan exporteras med hjälp av REST API, men vissa kan inte exporteras med diagnostikinställningar på grund av erna i Azure Monitor-Dataservern. Kolumnen som *exporteras via diagnostikinställningar* i tabellerna nedan visar vilka mått som kan exporteras på det här sättet.  

- **Flerdimensionella mått** – att skicka flerdimensionella mått till andra platser via diagnostikinställningar stöds inte för närvarande. Mått med dimensioner exporteras som tillplattade endimensionella mått som aggregeras över dimensionsvärden. *Till exempel*: Måttet för inkommande meddelanden i en händelsehubb kan utforskas och läggas till på per-kö-nivå. När måttet exporteras via diagnostikinställningar visas det dock som alla inkommande meddelanden i alla köer i händelsehubben.

## <a name="guest-os-and-host-os-metrics"></a>Mått för gäst operativ system och värd operativ system

> [!WARNING]
> Mått för gäst operativ systemet (gäst operativ system) som körs i Azure Virtual Machines, Service Fabric och Cloud Services visas **inte** här. Gäst operativ systemets mått måste samlas in via en eller flera agenter som körs på eller som en del av gäst operativ systemet.  Gäst operativ systemets mått inkluderar prestanda räknare som spårar gäst processor procent eller minnes användning, som båda används ofta för automatisk skalning eller avisering. 
>
> **Värd-OS-mått är tillgängliga och visas nedan.** De är inte samma. Värd-OS-måtten relaterar till Hyper-V-sessionen som är värd för din gäst operativ system session. 

> [!TIP]
> Bästa praxis är att använda och konfigurera [Azure-diagnostik-tillägget](../agents/diagnostics-extension-overview.md) för att skicka gäst operativ systemets prestanda mått till samma Azure Monitor mått databas där plattforms måtten lagras. Tillägget dirigerar gäst operativ systemets mått via [anpassade mått](../essentials/metrics-custom-overview.md) -API: et. Sedan kan du Diagrama, Varna och annars använda gäst operativ systemets mått som plattforms mått. Alternativt kan du också använda Log Analytics agent för att skicka gäst operativ systemets mått till Azure Monitor loggar/Log Analytics. Du kan fråga efter dessa mått i kombination med icke-metriska data. 

Viktig ytterligare information finns i [Översikt över övervaknings agenter](../agents/agents-overview.md).

## <a name="table-formatting"></a>Tabellformatering

> [!IMPORTANT] 
> Den här senaste uppdateringen lägger till en ny kolumn och sorterat om måtten som alfabetiska. Ytterligare information innebär att tabellerna nedan kan ha en vågrät rullnings List längst ned, beroende på webbläsarfönstrets bredd. Om du tror att du saknar information använder du rullnings listen för att visa hela tabellen.

## <a name="microsoftaadiamazureadmetrics"></a>Microsoft. aadiam/azureADMetrics

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ThrottledRequests|No|ThrottledRequests|Antal|Genomsnitt|azureADMetrics typ mått|Inga dimensioner|


## <a name="microsoftanalysisservicesservers"></a>Microsoft. AnalysisServices/servers

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|CleanerCurrentPrice|Yes|Minne: aktuellt pris för renare|Antal|Genomsnitt|Aktuellt pris för minne, $/byte/tid, normaliserat till 1000.|ServerResourceType|
|CleanerMemoryNonshrinkable|Yes|Minne: det går inte att krympa rengörings minnet|Byte|Genomsnitt|Mängden minne, i byte, som inte kan rensas av bakgrunds rensaren.|ServerResourceType|
|CleanerMemoryShrinkable|Yes|Minne: krympnings utrymme för rensning|Byte|Genomsnitt|Mängden minne i byte som kan rensas av bakgrunds rensaren.|ServerResourceType|
|CommandPoolBusyThreads|Yes|Trådar: pool med upptagna kommando trådar|Antal|Genomsnitt|Antalet upptagna trådar i kommando tråds-poolen.|ServerResourceType|
|CommandPoolIdleThreads|Yes|Trådar: inaktiva trådar för kommando pool|Antal|Genomsnitt|Antal inaktiva trådar i kommando tråds-poolen.|ServerResourceType|
|CommandPoolJobQueueLength|Yes|Kölängd för kommando bassäng|Antal|Genomsnitt|Antal jobb i kön för kommando tråds poolen.|ServerResourceType|
|CurrentConnections|Yes|Anslutning: aktuella anslutningar|Antal|Genomsnitt|Aktuellt antal upprättade klient anslutningar.|ServerResourceType|
|CurrentUserSessions|Yes|Aktuella användarsessioner|Antal|Genomsnitt|Aktuellt antal användarsessioner som har upprättats.|ServerResourceType|
|LongParsingBusyThreads|Yes|Trådar: lång parsning av upptagna trådar|Antal|Genomsnitt|Antalet upptagna trådar i den långa parsande trådpoolen.|ServerResourceType|
|LongParsingIdleThreads|Yes|Trådar: inaktiva trådar för lång parsning|Antal|Genomsnitt|Antalet inaktiva trådar i den långa parsande trådpoolen.|ServerResourceType|
|LongParsingJobQueueLength|Yes|Trådar: lång parsning av jobb Kölängd|Antal|Genomsnitt|Antal jobb i kön för den långa parsande trådpoolen.|ServerResourceType|
|mashup_engine_memory_metric|Yes|M motor minne|Byte|Genomsnitt|Minnes användning per kombinations motor processer|ServerResourceType|
|mashup_engine_private_bytes_metric|Yes|M motor privata byte|Byte|Genomsnitt|Användning av privata byte med kombinations motor processer.|ServerResourceType|
|mashup_engine_qpu_metric|Yes|M-motor QPU|Antal|Genomsnitt|QPU användning av kombinations motor processer|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Yes|Virtuella byte i M-motor|Byte|Genomsnitt|Virtuella byte som används av kombinations motor processer.|ServerResourceType|
|memory_metric|Yes|Minne|Byte|Genomsnitt|Memory. Intervallet 0-25 GB för S1, 0-50 GB för S2 och 0-100 GB för S4|ServerResourceType|
|memory_thrashing_metric|Yes|Minnesförslöing|Procent|Genomsnitt|Genomsnittligt minne nedskräpning.|ServerResourceType|
|MemoryLimitHard|Yes|Minne: minnes gräns hårt|Byte|Genomsnitt|Hård minnes gräns, från konfigurations filen.|ServerResourceType|
|MemoryLimitHigh|Yes|Minne: minnes gräns hög|Byte|Genomsnitt|Hög minnes gräns, från konfigurations filen.|ServerResourceType|
|MemoryLimitLow|Yes|Minne: minnes gräns låg|Byte|Genomsnitt|Låg minnes gräns, från konfigurations filen.|ServerResourceType|
|MemoryLimitVertiPaq|Yes|Minne: minnes gräns VertiPaq|Byte|Genomsnitt|Minnes intern gräns, från konfigurations filen.|ServerResourceType|
|MemoryUsage|Yes|Minne: minnes användning|Byte|Genomsnitt|Minnes användningen för Server processen som används för att beräkna rengörings minnes priset. Lika med Counter Process\PrivateBytes plus storleken på minnesmappade data, vilket ignorerar minne som har mappats eller allokerats av xVelocity i Memory Analytics Engine (VertiPaq) utöver minnes gränsen för xVelocity-motorn.|ServerResourceType|
|private_bytes_metric|Yes|Privata byte|Byte|Genomsnitt|Privata byte.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Yes|Trådar: bearbetning av pool upptagna I/O-jobb trådar|Antal|Genomsnitt|Antal trådar som kör i/O-jobb i bearbetnings trådens pool.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Yes|Trådar: bearbetning av pool upptagna icke-I/O-trådar|Antal|Genomsnitt|Antal trådar som kör icke-I/O-jobb i bearbetningen av trådpoolen.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Yes|Trådar: behandlar inaktiva I/O-jobb trådar för poolen|Antal|Genomsnitt|Antalet inaktiva trådar för I/O-jobb i bearbetnings trådens pool.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Yes|Trådar: behandlar inaktiva icke-I/O-trådar som behandlar poolen|Antal|Genomsnitt|Antalet inaktiva trådar i bearbetnings trådens pool som är dedikerad till icke-I/O-jobb.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Yes|Trådar: bearbeta pool I/O-jobb Kölängd|Antal|Genomsnitt|Antalet I/O-jobb i kön för bearbetning av trådpoolen.|ServerResourceType|
|ProcessingPoolJobQueueLength|Yes|Bearbetar Kölängd för poolen|Antal|Genomsnitt|Antal icke-I/O-jobb i kön för bearbetning av trådpoolen.|ServerResourceType|
|qpu_metric|Yes|QPU|Antal|Genomsnitt|QPU. Intervallet 0-100 för S1, 0-200 för S2 och 0-400 för S4|ServerResourceType|
|QueryPoolBusyThreads|Yes|Upptagna trådar för frågeprocessorn|Antal|Genomsnitt|Antalet upptagna trådar i trådpoolen.|ServerResourceType|
|QueryPoolIdleThreads|Yes|Trådar: inaktiva trådar i lagringspoolen|Antal|Genomsnitt|Antalet inaktiva trådar för I/O-jobb i bearbetnings trådens pool.|ServerResourceType|
|QueryPoolJobQueueLength|Yes|Trådar: jobbkölängd för jobbkö|Antal|Genomsnitt|Antal jobb i kön för trådpoolen för Query.|ServerResourceType|
|Kvot|Yes|Minne: kvot|Byte|Genomsnitt|Aktuell minnes kvot, i byte. Minnes kvot kallas även för minnes tilldelning eller minnes reservation.|ServerResourceType|
|QuotaBlocked|Yes|Minne: kvot blockerad|Antal|Genomsnitt|Det aktuella antalet kvot begär Anden som blockeras tills andra minnes kvoter frigjorts.|ServerResourceType|
|RowsConvertedPerSec|Yes|Bearbetar: rader konverterade per sekund|CountPerSecond|Genomsnitt|Antal rader som konverterats under bearbetning.|ServerResourceType|
|RowsReadPerSec|Yes|Bearbetar: rader lästa per sekund|CountPerSecond|Genomsnitt|Antalet rader som läses från alla Relations databaser.|ServerResourceType|
|RowsWrittenPerSec|Yes|Bearbetar: rader skrivna per sekund|CountPerSecond|Genomsnitt|Antal rader som skrivs under bearbetningen.|ServerResourceType|
|ShortParsingBusyThreads|Yes|Trådar: kort parsning upptagna trådar|Antal|Genomsnitt|Antalet upptagna trådar i den kort parsar trådpoolen.|ServerResourceType|
|ShortParsingIdleThreads|Yes|Trådar: kort parsning inaktiva trådar|Antal|Genomsnitt|Antal inaktiva trådar i kort parsar trådpoolen.|ServerResourceType|
|ShortParsingJobQueueLength|Yes|Trådar: kort parsning av jobb Kölängd|Antal|Genomsnitt|Antal jobb i kö för kort parsar trådpoolen.|ServerResourceType|
|SuccessfullConnectionsPerSec|Yes|Lyckade anslutningar per sekund|CountPerSecond|Genomsnitt|Antal slutförda slut för ande av anslutningar.|ServerResourceType|
|TotalConnectionFailures|Yes|Totalt antal anslutnings problem|Antal|Genomsnitt|Totalt antal misslyckade anslutnings försök.|ServerResourceType|
|TotalConnectionRequests|Yes|Totalt antal anslutnings begär Anden|Antal|Genomsnitt|Totalt antal anslutnings begär Anden. Dessa är mottagna.|ServerResourceType|
|VertiPaqNonpaged|Yes|Minne: VertiPaq som inte är växlat|Byte|Genomsnitt|Byte av minne som är låst i arbets minnet för användning av minnes intern motorn.|ServerResourceType|
|VertiPaqPaged|Yes|Minne: VertiPaq växlat|Byte|Genomsnitt|Byte av växlings Bart minne som används för minnes intern data.|ServerResourceType|
|virtual_bytes_metric|Yes|Virtuella byte|Byte|Genomsnitt|Virtuella byte.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|BackendDuration|Yes|Varaktighet för backend-begäranden|Millisekunder|Genomsnitt|Varaktighet för backend-begäranden i millisekunder|Plats, värdnamn|
|Kapacitet|Yes|Kapacitet|Procent|Genomsnitt|Användnings mått för API Management-tjänsten|Location|
|Varaktighet|Yes|Total varaktighet för gateway-begäranden|Millisekunder|Genomsnitt|Total varaktighet för gateway-begäranden i millisekunder|Plats, värdnamn|
|EventHubDroppedEvents|Yes|Ignorerade EventHub-händelser|Antal|Totalt|Antalet händelser som hoppades över på grund av att gränsen för kös Tor lek har uppnåtts|Location|
|EventHubRejectedEvents|Yes|Avvisade EventHub-händelser|Antal|Totalt|Antal avvisade EventHub-händelser (felaktig konfiguration eller otillåten)|Location|
|EventHubSuccessfulEvents|Yes|Lyckade EventHub-händelser|Antal|Totalt|Antal lyckade EventHub-händelser|Location|
|EventHubThrottledEvents|Yes|Begränsade EventHub-händelser|Antal|Totalt|Antal begränsade EventHub-händelser|Location|
|EventHubTimedoutEvents|Yes|Tids gränsen nåddes för EventHub-händelser|Antal|Totalt|Antal inaktuella EventHub-händelser|Location|
|EventHubTotalBytesSent|Yes|Storlek på EventHub-händelser|Byte|Totalt|Total storlek för EventHub-händelser i byte|Location|
|EventHubTotalEvents|Yes|Totalt antal EventHub-händelser|Antal|Totalt|Antal händelser som skickats till EventHub|Location|
|EventHubTotalFailedEvents|Yes|Misslyckade EventHub-händelser|Antal|Totalt|Antal misslyckade EventHub-händelser|Location|
|FailedRequests|Yes|Misslyckade Gateway-begäranden (inaktuella)|Antal|Totalt|Antal misslyckade Gateway-begär Anden – Använd mått för flera dimensions begär Anden med GatewayResponseCodeCategory-dimensionen istället|Plats, värdnamn|
|NetworkConnectivity|Yes|Status för resurs för nätverks anslutning (förhands granskning)|Antal|Genomsnitt|Nätverks anslutnings status för beroende resurs typer från API Management-tjänsten|Plats, ResourceType|
|OtherRequests|Yes|Andra gateway-begäranden (inaktuella)|Antal|Totalt|Antal andra gateway-begär Anden – Använd mått för flera dimensions begär Anden med GatewayResponseCodeCategory-dimensionen istället|Plats, värdnamn|
|Begäranden|Yes|Begäranden|Antal|Totalt|Mått för gateway-begäran med flera dimensioner|Plats, värdnamn, LastErrorReason, BackendResponseCode, GatewayResponseCode, BackendResponseCodeCategory, GatewayResponseCodeCategory|
|SuccessfulRequests|Yes|Lyckade Gateway-begäranden (inaktuella)|Antal|Totalt|Antal lyckade Gateway-begäranden – Använd mått för flera dimensions begär Anden med GatewayResponseCodeCategory-dimensionen istället|Plats, värdnamn|
|TotalRequests|Yes|Totalt antal Gateway-begäranden (inaktuella)|Antal|Totalt|Antal Gateway-begäranden – Använd mått för flera dimensions begär Anden med GatewayResponseCodeCategory-dimensionen istället|Plats, värdnamn|
|UnauthorizedRequests|Yes|Obehöriga Gateway-begäranden (inaktuella)|Antal|Totalt|Antal otillåtna Gateway-begäranden – Använd dimensions mått för flera dimensioner med GatewayResponseCodeCategory dimension i stället|Plats, värdnamn|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft. AppConfiguration/configurationStores

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|HttpIncomingRequestCount|Yes|HttpIncomingRequestCount|Antal|Antal|Totalt antal inkommande HTTP-begäranden.|StatusCode, autentisering|
|HttpIncomingRequestDuration|Yes|HttpIncomingRequestDuration|Antal|Genomsnitt|Svars tid på en http-begäran.|StatusCode, autentisering|
|ThrottledHttpRequestCount|Yes|ThrottledHttpRequestCount|Antal|Antal|Begränsade HTTP-begäranden.|Inga dimensioner|


## <a name="microsoftappplatformspring"></a>Microsoft. AppPlatform/våren

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Antal aktiva timers|Yes|Antal aktiva timers|Antal|Genomsnitt|Antal aktiva timers|Distribution, AppName, Pod|
|tilldelnings pris|Yes|tilldelnings pris|Byte|Genomsnitt|Antal byte som allokerats i den hanterade heapen|Distribution, AppName, Pod|
|AppCpuUsage|Yes|CPU-användning för app (för hands version)|Procent|Genomsnitt|Den senaste CPU-användningen för appen|Distribution, AppName, Pod|
|antal sammansättningar|Yes|antal sammansättningar|Antal|Genomsnitt|Antal inlästa sammansättningar|Distribution, AppName, Pod|
|CPU-användning|Yes|CPU-användning|Procent|Genomsnitt|% tid som processen har använt processorn|Distribution, AppName, Pod|
|aktuella begär Anden|Yes|aktuella begär Anden|Antal|Genomsnitt|Totalt antal bearbetnings begär Anden under processens livs längd|Distribution, AppName, Pod|
|undantag – antal|Yes|undantag – antal|Antal|Totalt|Antal undantag|Distribution, AppName, Pod|
|misslyckade-begär Anden|Yes|misslyckade-begär Anden|Antal|Genomsnitt|Totalt antal misslyckade begär Anden under processens livs längd|Distribution, AppName, Pod|
|GC – Heap-storlek|Yes|GC – Heap-storlek|Antal|Genomsnitt|Total Heap-storlek som rapporteras av GC (MB)|Distribution, AppName, Pod|
|gen-0-GC-Count|Yes|gen-0-GC-Count|Antal|Genomsnitt|Antal GC generationer för generation 0|Distribution, AppName, Pod|
|gen-0-storlek|Yes|gen-0-storlek|Byte|Genomsnitt|Heap-storlek för generation 0|Distribution, AppName, Pod|
|gen-1-GC-antal|Yes|gen-1-GC-antal|Antal|Genomsnitt|Antal GC generationer för gen 1|Distribution, AppName, Pod|
|gen-1-storlek|Yes|gen-1-storlek|Byte|Genomsnitt|Heap-storlek för generation 1|Distribution, AppName, Pod|
|gen-2-GC – antal|Yes|gen-2-GC – antal|Antal|Genomsnitt|Antal GC generationer för generation 2|Distribution, AppName, Pod|
|gen-2-storlek|Yes|gen-2-storlek|Byte|Genomsnitt|Heap-storlek för generation 2|Distribution, AppName, Pod|
|JVM. gc. live. data. size|Yes|JVM. gc. live. data. size|Byte|Genomsnitt|Storlek på den gamla generationens lagringspool efter en fullständig GC|Distribution, AppName, Pod|
|JVM. gc. max. data storlek|Yes|JVM. gc. max. data storlek|Byte|Genomsnitt|Max storlek på den gamla generationens minnesbuffert|Distribution, AppName, Pod|
|JVM. gc. Memory. allokerat|Yes|JVM. gc. Memory. allokerat|Byte|Maximal|Ökas för en ökning av storleken på den unga generationens minnesbuffert efter en GC till före nästa|Distribution, AppName, Pod|
|JVM. gc. Memory. upphöjt|Yes|JVM. gc. Memory. upphöjt|Byte|Maximal|Antalet positiva ökningar i storleken på den gamla generationens minnesbuffert innan GC till efter GC|Distribution, AppName, Pod|
|JVM. gc. Pause. total. Count|Yes|JVM. gc. Pause. total. Count|Antal|Totalt|Antal pauser för GC|Distribution, AppName, Pod|
|JVM. gc. Pause. total. Time|Yes|JVM. gc. Pause. total. Time|Millisekunder|Totalt|Total tid för GC-paus|Distribution, AppName, Pod|
|JVM. Memory. dedikerat|Yes|JVM. Memory. dedikerat|Byte|Genomsnitt|Minne som tilldelats JVM i byte|Distribution, AppName, Pod|
|JVM. Memory. Max|Yes|JVM. Memory. Max|Byte|Maximal|Maximal mängd minne i byte som kan användas för minnes hantering|Distribution, AppName, Pod|
|JVM. Memory. används|Yes|JVM. Memory. används|Byte|Genomsnitt|App-minne som används i byte|Distribution, AppName, Pod|
|Loh-storlek|Yes|Loh-storlek|Byte|Genomsnitt|LOH Heap-storlek|Distribution, AppName, Pod|
|övervaka-låser-Contents-Count|Yes|övervaka-låser-Contents-Count|Antal|Genomsnitt|Antal gånger som det uppstod en konkurrens vid försök att ta övervaknings låset|Distribution, AppName, Pod|
|process. CPU. Usage|Yes|process. CPU. Usage|Procent|Genomsnitt|Den senaste processor användningen för JVM-processen|Distribution, AppName, Pod|
|begär Anden per sekund|Yes|begär Anden-pris|Antal|Genomsnitt|Begär ande frekvens|Distribution, AppName, Pod|
|system. CPU. Usage|Yes|system. CPU. Usage|Procent|Genomsnitt|Senaste CPU-användning för hela systemet|Distribution, AppName, Pod|
|trådpool-slutfört-objekt-antal|Yes|trådpool-slutfört-objekt-antal|Antal|Genomsnitt|Antal slutförda arbets uppgifter i trådpool|Distribution, AppName, Pod|
|trådpool-Kölängd|Yes|trådpool-Kölängd|Antal|Genomsnitt|Kölängd för arbets objekt i trådpool|Distribution, AppName, Pod|
|trådpool-tråd antal|Yes|trådpool-tråd antal|Antal|Genomsnitt|Antal trådar i trådpool|Distribution, AppName, Pod|
|tid-i-GC|Yes|tid-i-GC|Procent|Genomsnitt|% Time i GC sedan den senaste GC|Distribution, AppName, Pod|
|tomcat. global. error|Yes|tomcat. global. error|Antal|Totalt|Tomcat globalt fel|Distribution, AppName, Pod|
|tomcat. global. mottagen|Yes|tomcat. global. mottagen|Byte|Totalt|Totalt antal mottagna byte i Tomcat|Distribution, AppName, Pod|
|tomcat. global. Request. Gmsn. Time|Yes|tomcat. global. Request. Gmsn. Time|Millisekunder|Genomsnitt|Genomsnittlig tid för Tomcat-begäran|Distribution, AppName, Pod|
|tomcat. global. Request. Max|Yes|tomcat. global. Request. Max|Millisekunder|Maximal|Max tid för Tomcat-begäran|Distribution, AppName, Pod|
|tomcat. global. Request. total. Count|Yes|tomcat. global. Request. total. Count|Antal|Totalt|Totalt antal Tomcat-begäranden|Distribution, AppName, Pod|
|tomcat. global. Request. total. Time|Yes|tomcat. global. Request. total. Time|Millisekunder|Totalt|Total tid för Tomcat-begäran|Distribution, AppName, Pod|
|tomcat. global. skickat|Yes|tomcat. global. skickat|Byte|Totalt|Totalt antal skickade byte i Tomcat|Distribution, AppName, Pod|
|tomcat. sessions. Active. Current|Yes|tomcat. sessions. Active. Current|Antal|Totalt|Aktivt antal Tomcat-sessioner|Distribution, AppName, Pod|
|tomcat. sessions. Active. Max|Yes|tomcat. sessions. Active. Max|Antal|Totalt|Antal aktiva Tomcat-sessioner|Distribution, AppName, Pod|
|tomcat. sessions. Alive. Max|Yes|tomcat. sessions. Alive. Max|Millisekunder|Maximal|Maximal Alive-tid för Tomcat-session|Distribution, AppName, Pod|
|tomcat. sessions. created|Yes|tomcat. sessions. created|Antal|Totalt|Antal skapade Tomcat-sessioner|Distribution, AppName, Pod|
|tomcat. sessions. har gått ut|Yes|tomcat. sessions. har gått ut|Antal|Totalt|Antal utgångna Tomcat-sessioner|Distribution, AppName, Pod|
|tomcat. sessions. avvisad|Yes|tomcat. sessions. avvisad|Antal|Totalt|Antal nekade Tomcat-sessioner|Distribution, AppName, Pod|
|tomcat.threads.config. Max|Yes|tomcat.threads.config. Max|Antal|Totalt|Antal trådar för Tomcat-konfiguration|Distribution, AppName, Pod|
|tomcat. threads. Current|Yes|tomcat. threads. Current|Antal|Totalt|Antal Tomcat aktuella trådar|Distribution, AppName, Pod|
|Totalt-begär Anden|Yes|Totalt-begär Anden|Antal|Genomsnitt|Totalt antal begär Anden under processens livs längd|Distribution, AppName, Pod|
|arbets uppsättning|Yes|arbets uppsättning|Antal|Genomsnitt|Mängden arbets minne som används av processen (MB)|Distribution, AppName, Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft. Automation/automationAccounts

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|TotalJob|Yes|Totalt antal jobb|Antal|Totalt|Det totala antalet jobb|Runbook, status|
|TotalUpdateDeploymentMachineRuns|Yes|Antal datorspecifika körningar av uppdateringsdistributionen|Antal|Totalt|Total distributions dator för program uppdatering körs i en körning av program uppdaterings distribution|SoftwareUpdateConfigurationName, status, TargetComputer, SoftwareUpdateConfigurationRunId|
|TotalUpdateDeploymentRuns|Yes|Antal körningar av uppdateringsdistributionen|Antal|Totalt|Totalt antal körningar av program uppdaterings distribution|SoftwareUpdateConfigurationName, status|


## <a name="microsoftavsprivateclouds"></a>Microsoft. AVS/privateClouds

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|CapacityLatest|Yes|Total kapacitet för data lager disk|Byte|Genomsnitt|Den totala kapaciteten för disk i data lagret|dsname|
|DiskUsedPercentage|Yes| Procent data lager disk som används|Procent|Genomsnitt|Procent av tillgänglig disk som används i data lagret|dsname|
|EffectiveCpuAverage|Yes|Processorprocentandel|Procent|Genomsnitt|Procent andel använda processor resurser i kluster|ClusterName|
|EffectiveMemAverage|Yes|Genomsnittligt effektivt minne|Byte|Genomsnitt|Total tillgänglig mängd dator minne i kluster|ClusterName|
|OverheadAverage|Yes|Genomsnittlig minnes kapacitet|Byte|Genomsnitt|Värd fysiskt minne som används av Virtualization-infrastrukturen|ClusterName|
|TotalMbAverage|Yes|Genomsnittligt totalt minne|Byte|Genomsnitt|Totalt minne i kluster|ClusterName|
|UsageAverage|Yes|Genomsnittlig minnes användning|Procent|Genomsnitt|Minnes användning i procent av det totala konfigurerade eller tillgängliga minnet|ClusterName|
|UsedLatest|Yes|Använd data lager disk|Byte|Genomsnitt|Den totala disk mängd som används i data lagret|dsname|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.BatCH/batchAccounts

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|CoreCount|No|Dedikerat antal kärnor|Antal|Totalt|Totalt antal dedikerade kärnor i batch-kontot|Inga dimensioner|
|CreatingNodeCount|No|Antal noder skapas|Antal|Totalt|Antal noder som skapas|Inga dimensioner|
|IdleNodeCount|No|Antal inaktiva noder|Antal|Totalt|Antal inaktiva noder|Inga dimensioner|
|JobDeleteCompleteEvent|Yes|Slutför händelser för borttagning av jobb|Antal|Totalt|Totalt antal jobb som har tagits bort.|jobId|
|JobDeleteStartEvent|Yes|Start händelser för jobb borttagning|Antal|Totalt|Totalt antal jobb som har begärts att tas bort.|jobId|
|JobDisableCompleteEvent|Yes|Jobb för att inaktivera slutförda händelser|Antal|Totalt|Totalt antal jobb som har inaktiverats.|jobId|
|JobDisableStartEvent|Yes|Jobb inaktivera start händelser|Antal|Totalt|Totalt antal jobb som har begärts att inaktive ras.|jobId|
|JobStartEvent|Yes|Jobb start händelser|Antal|Totalt|Totalt antal jobb som har startats.|jobId|
|JobTerminateCompleteEvent|Yes|Slutför händelser för avsluta jobb|Antal|Totalt|Totalt antal jobb som har avslut ATS.|jobId|
|JobTerminateStartEvent|Yes|Jobb som avslutar start händelser|Antal|Totalt|Totalt antal jobb som har begärts att avslutas.|jobId|
|LeavingPoolNodeCount|No|Antal noder för att lämna pooler|Antal|Totalt|Antal noder som lämnar poolen|Inga dimensioner|
|LowPriorityCoreCount|No|Antal LowPriority kärnor|Antal|Totalt|Totalt antal låg prioritets kärnor i batch-kontot|Inga dimensioner|
|OfflineNodeCount|No|Antal offline-noder|Antal|Totalt|Antal offline-noder|Inga dimensioner|
|PoolCreateEvent|Yes|Skapa händelser för pool|Antal|Totalt|Totalt antal pooler som har skapats|poolId|
|PoolDeleteCompleteEvent|Yes|Slutför händelse för borttagning av pool|Antal|Totalt|Totalt antal borttagna pooler som har slutförts|poolId|
|PoolDeleteStartEvent|Yes|Start händelser för borttagning av pool|Antal|Totalt|Totalt antal borttagningar av pooler som har startat|poolId|
|PoolResizeCompleteEvent|Yes|Slutför händelser för storleks ändring av pool|Antal|Totalt|Totalt antal ändrade pooler som har slutförts|poolId|
|PoolResizeStartEvent|Yes|Starta händelser för att ändra storlek på poolen|Antal|Totalt|Totalt antal storleks ändringar för pooler som har startat|poolId|
|PreemptedNodeCount|No|Antal misslyckade noder|Antal|Totalt|Antal misslyckade noder|Inga dimensioner|
|RebootingNodeCount|No|Antalet noder startas om|Antal|Totalt|Antal omstarter av noder|Inga dimensioner|
|ReimagingNodeCount|No|Antal noder för avbildning|Antal|Totalt|Antal avbildnings noder|Inga dimensioner|
|RunningNodeCount|No|Antal noder som körs|Antal|Totalt|Antal noder som körs|Inga dimensioner|
|StartingNodeCount|No|Antalet noder startas|Antal|Totalt|Antal noder som börjar|Inga dimensioner|
|StartTaskFailedNodeCount|No|Starta aktivitet antal misslyckade noder|Antal|Totalt|Antal noder där start aktiviteten misslyckades|Inga dimensioner|
|TaskCompleteEvent|Yes|Uppgift slutförda händelser|Antal|Totalt|Totalt antal slutförda uppgifter|poolId, jobId|
|TaskFailEvent|Yes|Aktivitet, misslyckade händelser|Antal|Totalt|Totalt antal uppgifter som har slutförts i felaktigt tillstånd|poolId, jobId|
|TaskStartEvent|Yes|Aktivitetens start händelser|Antal|Totalt|Totalt antal aktiviteter som har startat|poolId, jobId|
|TotalLowPriorityNodeCount|No|Antal Low-Priority noder|Antal|Totalt|Totalt antal noder med låg prioritet i batch-kontot|Inga dimensioner|
|TotalNodeCount|No|Antal dedikerade noder|Antal|Totalt|Totalt antal dedikerade noder i batch-kontot|Inga dimensioner|
|UnusableNodeCount|No|Antal noder som inte kan användas|Antal|Totalt|Antal oanvändbara noder|Inga dimensioner|
|WaitingForStartTaskNodeCount|No|Väntar på att starta aktiviteter antal noder|Antal|Totalt|Antal noder som väntar på att start aktiviteten ska slutföras|Inga dimensioner|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/arbets ytor

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Aktiva kärnor|Yes|Aktiva kärnor|Antal|Genomsnitt|Antal aktiva kärnor|Scenario, kluster namn|
|Aktiva noder|Yes|Aktiva noder|Antal|Genomsnitt|Antal noder som körs|Scenario, kluster namn|
|Inaktiva kärnor|Yes|Inaktiva kärnor|Antal|Genomsnitt|Antal inaktiva kärnor|Scenario, kluster namn|
|Inaktiva noder|Yes|Inaktiva noder|Antal|Genomsnitt|Antal inaktiva noder|Scenario, kluster namn|
|Jobbet har slutförts|Yes|Jobbet har slutförts|Antal|Totalt|Antal slutförda jobb|Scenario, kluster namn, ResultType|
|Jobb skickat|Yes|Jobb skickat|Antal|Totalt|Antal skickade jobb|Scenario, kluster namn|
|Lämnar kärnor|Yes|Lämnar kärnor|Antal|Genomsnitt|Antal lämnar kärnor|Scenario, kluster namn|
|Lämnar noder|Yes|Lämnar noder|Antal|Genomsnitt|Antal lämnar noder|Scenario, kluster namn|
|Blockerade kärnor|Yes|Blockerade kärnor|Antal|Genomsnitt|Antal blockerade kärnor|Scenario, kluster namn|
|Misslyckade noder|Yes|Misslyckade noder|Antal|Genomsnitt|Antal misslyckade noder|Scenario, kluster namn|
|Kvot användning i procent|Yes|Kvot användning i procent|Antal|Genomsnitt|Procent av kvoten som används|Scenario, kluster namn, VmFamilyName, VmPriority|
|Totalt antal kärnor|Yes|Totalt antal kärnor|Antal|Genomsnitt|Antal total kärnor|Scenario, kluster namn|
|Totalt antal noder|Yes|Totalt antal noder|Antal|Genomsnitt|Antal totala noder|Scenario, kluster namn|
|Oanvändbara kärnor|Yes|Oanvändbara kärnor|Antal|Genomsnitt|Antal oanvändbara kärnor|Scenario, kluster namn|
|Oanvändbara noder|Yes|Oanvändbara noder|Antal|Genomsnitt|Antal oanvändbara noder|Scenario, kluster namn|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft. blockchain/blockchainMembers

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|BroadcastProcessedCount|Yes|BroadcastProcessedCountDisplayName|Antal|Genomsnitt|Antalet bearbetade transaktioner.|Nod, kanal, typ, status|
|ChaincodeExecuteTimeouts|Yes|ChaincodeExecuteTimeoutsDisplayName|Antal|Genomsnitt|Antalet chaincode-körningar (init eller Invoke) som har uppnådde tids gränsen.|Node, chaincode|
|ChaincodeLaunchFailures|Yes|ChaincodeLaunchFailuresDisplayName|Antal|Genomsnitt|Antalet chaincode-lanseringar som har misslyckats.|Node, chaincode|
|ChaincodeLaunchTimeouts|Yes|ChaincodeLaunchTimeoutsDisplayName|Antal|Genomsnitt|Antalet chaincode-lanseringar som har uppnådde tids gränsen.|Node, chaincode|
|ChaincodeShimRequestsCompleted|Yes|ChaincodeShimRequestsCompletedDisplayName|Antal|Genomsnitt|Antalet chaincode-shim-begäranden som har slutförts.|Node, typ, kanal, chaincode, lyckades|
|ChaincodeShimRequestsReceived|Yes|ChaincodeShimRequestsReceivedDisplayName|Antal|Genomsnitt|Antalet chaincode-shim-begäranden som tagits emot.|Nod, typ, kanal, chaincode|
|ClusterCommEgressQueueCapacity|Yes|ClusterCommEgressQueueCapacityDisplayName|Antal|Genomsnitt|Kapacitet för utgående kö.|Nod, värd, msg_type, kanal|
|ClusterCommEgressQueueLength|Yes|ClusterCommEgressQueueLengthDisplayName|Antal|Genomsnitt|Längd på utgående kö.|Nod, värd, msg_type, kanal|
|ClusterCommEgressQueueWorkers|Yes|ClusterCommEgressQueueWorkersDisplayName|Antal|Genomsnitt|Antal anställda i utgående kö.|Nod, kanal|
|ClusterCommEgressStreamCount|Yes|ClusterCommEgressStreamCountDisplayName|Antal|Genomsnitt|Antal strömmar till andra noder.|Nod, kanal|
|ClusterCommEgressTlsConnectionCount|Yes|ClusterCommEgressTlsConnectionCountDisplayName|Antal|Genomsnitt|Antal TLS-anslutningar till andra noder.|Nod|
|ClusterCommIngressStreamCount|Yes|ClusterCommIngressStreamCountDisplayName|Antal|Genomsnitt|Antal strömmar från andra noder.|Nod|
|ClusterCommMsgDroppedCount|Yes|ClusterCommMsgDroppedCountDisplayName|Antal|Genomsnitt|Antal meddelanden som tagits bort.|Nod, värd, kanal|
|ConnectionAccepted|Yes|Godkända anslutningar|Antal|Totalt|Godkända anslutningar|Nod|
|ConnectionActive|Yes|Aktiva anslutningar|Antal|Genomsnitt|Aktiva anslutningar|Nod|
|ConnectionHandled|Yes|Hanterade anslutningar|Antal|Totalt|Hanterade anslutningar|Nod|
|ConsensusEtcdraftActiveNodes|Yes|ConsensusEtcdraftActiveNodesDisplayName|Antal|Genomsnitt|Antal aktiva noder i denna kanal.|Nod, kanal|
|ConsensusEtcdraftClusterSize|Yes|ConsensusEtcdraftClusterSizeDisplayName|Antal|Genomsnitt|Antalet noder i den här kanalen.|Nod, kanal|
|ConsensusEtcdraftCommittedBlockNumber|Yes|ConsensusEtcdraftCommittedBlockNumberDisplayName|Antal|Genomsnitt|Block numret för det senaste blocket som har allokerats.|Nod, kanal|
|ConsensusEtcdraftConfigProposalsReceived|Yes|ConsensusEtcdraftConfigProposalsReceivedDisplayName|Antal|Genomsnitt|Det totala antalet förslag som tagits emot för konfigurations typ transaktioner.|Nod, kanal|
|ConsensusEtcdraftIsLeader|Yes|ConsensusEtcdraftIsLeaderDisplayName|Antal|Genomsnitt|Den aktuella nodens ledarskaps status: 1 om den är ledare Else 0.|Nod, kanal|
|ConsensusEtcdraftLeaderChanges|Yes|ConsensusEtcdraftLeaderChangesDisplayName|Antal|Genomsnitt|Antalet ledar ändringar sedan processen startades.|Nod, kanal|
|ConsensusEtcdraftNormalProposalsReceived|Yes|ConsensusEtcdraftNormalProposalsReceivedDisplayName|Antal|Genomsnitt|Det totala antalet förslag som tagits emot för normala typ transaktioner.|Nod, kanal|
|ConsensusEtcdraftProposalFailures|Yes|ConsensusEtcdraftProposalFailuresDisplayName|Antal|Genomsnitt|Antalet förslags problem.|Nod, kanal|
|ConsensusEtcdraftSnapshotBlockNumber|Yes|ConsensusEtcdraftSnapshotBlockNumberDisplayName|Antal|Genomsnitt|Block numret för den senaste ögonblicks bilden.|Nod, kanal|
|ConsensusKafkaBatchSize|Yes|ConsensusKafkaBatchSizeDisplayName|Antal|Genomsnitt|Genomsnittlig batchstorlek i byte som skickats till ämnen.|Node, ämne|
|ConsensusKafkaCompressionRatio|Yes|ConsensusKafkaCompressionRatioDisplayName|Antal|Genomsnitt|Genomsnitts komprimerings förhållandet (i procent) för ämnen.|Node, ämne|
|ConsensusKafkaIncomingByteRate|Yes|ConsensusKafkaIncomingByteRateDisplayName|Antal|Genomsnitt|Byte/sekund avlästa av utjämnare.|Nod, broker_id|
|ConsensusKafkaLastOffsetPersisted|Yes|ConsensusKafkaLastOffsetPersistedDisplayName|Antal|Genomsnitt|Den förskjutning som anges i blockets metadata för det senast allokerade blocket.|Nod, kanal|
|ConsensusKafkaOutgoingByteRate|Yes|ConsensusKafkaOutgoingByteRateDisplayName|Antal|Genomsnitt|Byte/sekund som skrivits till-utjämnare.|Nod, broker_id|
|ConsensusKafkaRecordSendRate|Yes|ConsensusKafkaRecordSendRateDisplayName|Antal|Genomsnitt|Antalet poster per sekund som skickats till ämnen.|Node, ämne|
|ConsensusKafkaRecordsPerRequest|Yes|ConsensusKafkaRecordsPerRequestDisplayName|Antal|Genomsnitt|Genomsnittligt antal poster som skickats per begäran till ämnen.|Node, ämne|
|ConsensusKafkaRequestLatency|Yes|ConsensusKafkaRequestLatencyDisplayName|Antal|Genomsnitt|Den genomsnittliga svars tiden i MS till mäklare.|Nod, broker_id|
|ConsensusKafkaRequestRate|Yes|ConsensusKafkaRequestRateDisplayName|Antal|Genomsnitt|Begär Anden/sekund som skickats till utjämnare.|Nod, broker_id|
|ConsensusKafkaRequestSize|Yes|ConsensusKafkaRequestSizeDisplayName|Antal|Genomsnitt|Genomsnittlig storlek för begäran i byte till utjämnare.|Nod, broker_id|
|ConsensusKafkaResponseRate|Yes|ConsensusKafkaResponseRateDisplayName|Antal|Genomsnitt|Begär Anden/sekund som skickats till utjämnare.|Nod, broker_id|
|ConsensusKafkaResponseSize|Yes|ConsensusKafkaResponseSizeDisplayName|Antal|Genomsnitt|Genomsnittlig svars storlek i byte från utjämnare.|Nod, broker_id|
|CpuUsagePercentageInDouble|Yes|Procent andel CPU-användning|Procent|Maximal|Procent andel CPU-användning|Nod|
|DeliverBlocksSent|Yes|DeliverBlocksSentDisplayName|Antal|Genomsnitt|Antalet block som skickats av leverans tjänsten.|Nod, kanal, filtrerad, data_type|
|DeliverRequestsCompleted|Yes|DeliverRequestsCompletedDisplayName|Antal|Genomsnitt|Antalet leverans begär Anden som har slutförts.|Nod, kanal, filtrerad, data_type, lyckades|
|DeliverRequestsReceived|Yes|DeliverRequestsReceivedDisplayName|Antal|Genomsnitt|Antalet leverans begär Anden som har tagits emot.|Nod, kanal, filtrerad, data_type|
|DeliverStreamsClosed|Yes|DeliverStreamsClosedDisplayName|Antal|Genomsnitt|Antalet GRPC-strömmar som har stängts för leverans tjänsten.|Nod|
|DeliverStreamsOpened|Yes|DeliverStreamsOpenedDisplayName|Antal|Genomsnitt|Antalet GRPC-strömmar som har öppnats för leverans tjänsten.|Nod|
|EndorserChaincodeInstantiationFailures|Yes|EndorserChaincodeInstantiationFailuresDisplayName|Antal|Genomsnitt|Antalet chaincode-instansieringar eller uppgraderingar som har misslyckats.|Node, Channel, chaincode|
|EndorserDuplicateTransactionFailures|Yes|EndorserDuplicateTransactionFailuresDisplayName|Antal|Genomsnitt|Antalet misslyckade förslag på grund av dubbletter av transaktions-ID.|Node, Channel, chaincode|
|EndorserEndorsementFailures|Yes|EndorserEndorsementFailuresDisplayName|Antal|Genomsnitt|Antalet misslyckade attesteringar.|Node, Channel, chaincode, chaincodeerror|
|EndorserProposalAclFailures|Yes|EndorserProposalAclFailuresDisplayName|Antal|Genomsnitt|Antalet förslag som misslyckade ACL-kontroller.|Node, Channel, chaincode|
|EndorserProposalSimulationFailures|Yes|EndorserProposalSimulationFailuresDisplayName|Antal|Genomsnitt|Antalet misslyckade förslags simuleringar.|Node, Channel, chaincode|
|EndorserProposalsReceived|Yes|EndorserProposalsReceivedDisplayName|Antal|Genomsnitt|Antalet mottagna förslag.|Nod|
|EndorserProposalValidationFailures|Yes|EndorserProposalValidationFailuresDisplayName|Antal|Genomsnitt|Antal förslag som inte kunde verifieras första gången.|Nod|
|EndorserSuccessfulProposals|Yes|EndorserSuccessfulProposalsDisplayName|Antal|Genomsnitt|Antalet lyckade förslag.|Nod|
|FabricVersion|Yes|FabricVersionDisplayName|Antal|Genomsnitt|Den aktiva versionen av Fabric.|Node, version|
|GossipCommMessagesReceived|Yes|GossipCommMessagesReceivedDisplayName|Antal|Genomsnitt|Antal mottagna meddelanden.|Nod|
|GossipCommMessagesSent|Yes|GossipCommMessagesSentDisplayName|Antal|Genomsnitt|Antal meddelanden som har skickats.|Nod|
|GossipCommOverflowCount|Yes|GossipCommOverflowCountDisplayName|Antal|Genomsnitt|Antal övergående buffertar i utgående kö.|Nod|
|GossipLeaderElectionLeader|Yes|GossipLeaderElectionLeaderDisplayName|Antal|Genomsnitt|Peer är ledare (1) eller följare (0).|Nod, kanal|
|GossipMembershipTotalPeersKnown|Yes|GossipMembershipTotalPeersKnownDisplayName|Antal|Genomsnitt|Totalt antal kända peer-datorer.|Nod, kanal|
|GossipPayloadBufferSize|Yes|GossipPayloadBufferSizeDisplayName|Antal|Genomsnitt|Storlek på nytto lastens buffert.|Nod, kanal|
|GossipStateHeight|Yes|GossipStateHeightDisplayName|Antal|Genomsnitt|Aktuell höjd för redovisning.|Nod, kanal|
|GrpcCommConnClosed|Yes|GrpcCommConnClosedDisplayName|Antal|Genomsnitt|gRPC-anslutningar har stängts. Öppen minus stängd är det aktiva antalet anslutningar.|Nod|
|GrpcCommConnOpened|Yes|GrpcCommConnOpenedDisplayName|Antal|Genomsnitt|gRPC-anslutningar öppnades. Öppen minus stängd är det aktiva antalet anslutningar.|Nod|
|GrpcServerStreamMessagesReceived|Yes|GrpcServerStreamMessagesReceivedDisplayName|Antal|Genomsnitt|Antal mottagna data ström meddelanden.|Node, service, metod|
|GrpcServerStreamMessagesSent|Yes|GrpcServerStreamMessagesSentDisplayName|Antal|Genomsnitt|Antal skickade strömmade meddelanden.|Node, service, metod|
|GrpcServerStreamRequestsCompleted|Yes|GrpcServerStreamRequestsCompletedDisplayName|Antal|Genomsnitt|Antal slutförda Stream-begäranden.|Nod, tjänst, metod, kod|
|GrpcServerUnaryRequestsReceived|Yes|GrpcServerUnaryRequestsReceivedDisplayName|Antal|Genomsnitt|Antalet mottagna unära begär Anden.|Node, service, metod|
|IOReadBytes|Yes|Lästa byte i IO|Byte|Totalt|Lästa byte i IO|Nod|
|IOWriteBytes|Yes|Skrivna byte i IO|Byte|Totalt|Skrivna byte i IO|Nod|
|LedgerBlockchainHeight|Yes|LedgerBlockchainHeightDisplayName|Antal|Genomsnitt|Kedjans höjd i block.|Nod, kanal|
|LedgerTransactionCount|Yes|LedgerTransactionCountDisplayName|Antal|Genomsnitt|Antal bearbetade transaktioner.|Node, Channel, transaction_type, chaincode, validation_code|
|LoggingEntriesChecked|Yes|LoggingEntriesCheckedDisplayName|Antal|Genomsnitt|Antalet logg poster som kontrol leras mot den aktiva loggnings nivån.|Nod, nivå|
|LoggingEntriesWritten|Yes|LoggingEntriesWrittenDisplayName|Antal|Genomsnitt|Antal logg poster som skrivs.|Nod, nivå|
|MemoryLimit|Yes|Minnes gräns|Byte|Genomsnitt|Minnes gräns|Nod|
|MemoryUsage|Yes|Minnesanvändning|Byte|Genomsnitt|Minnesanvändning|Nod|
|MemoryUsagePercentageInDouble|Yes|Minnes användnings procent|Procent|Genomsnitt|Minnes användnings procent|Nod|
|PendingTransactions|Yes|Väntande transaktioner|Antal|Genomsnitt|Väntande transaktioner|Nod|
|ProcessedBlocks|Yes|Bearbetade block|Antal|Totalt|Bearbetade block|Nod|
|ProcessedTransactions|Yes|Bearbetade transaktioner|Antal|Totalt|Bearbetade transaktioner|Nod|
|QueuedTransactions|Yes|Köade transaktioner|Antal|Genomsnitt|Köade transaktioner|Nod|
|RequestHandled|Yes|Hanterade begär Anden|Antal|Totalt|Hanterade begär Anden|Nod|
|StorageUsage|Yes|Lagrings användning|Byte|Genomsnitt|Lagrings användning|Nod|


## <a name="microsoftbotservicebotservices"></a>Microsoft. botservice/botservices

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|RequestLatency|Yes|Begär fördröjning|Millisekunder|Totalt|Tiden det tar för servern att bearbeta begäran|Åtgärd, autentisering, protokoll|
|RequestsTraffic|Yes|Begär trafik|Procent|Antal|Antal begär Anden som gjorts|Åtgärd, autentisering, protokoll, StatusCode, StatusCodeClass|


## <a name="microsoftcacheredis"></a>Microsoft. cache/Redis

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|allcachehits|Yes|Cacheträffar (Instance-based)|Antal|Totalt||ShardId, port, primär|
|allcachemisses|Yes|Cachemissar (instans-baserat)|Antal|Totalt||ShardId, port, primär|
|allcacheRead|Yes|Läsning av cacheminne (instans baserad)|BytesPerSecond|Maximal||ShardId, port, primär|
|allcacheWrite|Yes|Skrivcache (instans baserad)|BytesPerSecond|Maximal||ShardId, port, primär|
|allconnectedclients|Yes|Anslutna klienter (instans baserade)|Antal|Maximal||ShardId, port, primär|
|allevictedkeys|Yes|Avlägsnade nycklar (instansbaserade)|Antal|Totalt||ShardId, port, primär|
|allexpiredkeys|Yes|Utgångna nycklar (instans baserad)|Antal|Totalt||ShardId, port, primär|
|allgetcommands|Yes|Hämtar (instans baserad)|Antal|Totalt||ShardId, port, primär|
|alloperationsPerSecond|Yes|Åtgärder per sekund (instans baserad)|Antal|Maximal||ShardId, port, primär|
|allserverLoad|Yes|Server belastning (instans baserad)|Procent|Maximal||ShardId, port, primär|
|allsetcommands|Yes|Uppsättningar (instans baserad)|Antal|Totalt||ShardId, port, primär|
|alltotalcommandsprocessed|Yes|Totalt antal åtgärder (instans baserad)|Antal|Totalt||ShardId, port, primär|
|alltotalkeys|Yes|Totalt antal nycklar (instans baserat)|Antal|Maximal||ShardId, port, primär|
|allusedmemory|Yes|Använt minne (instans baserat)|Byte|Maximal||ShardId, port, primär|
|allusedmemorypercentage|Yes|Använd minnes procent (instans baserad)|Procent|Maximal||ShardId, port, primär|
|allusedmemoryRss|Yes|RSS (Instance-based) använt minne|Byte|Maximal||ShardId, port, primär|
|cachehits|Yes|Cacheträffar|Antal|Totalt||ShardId|
|cachehits0|Yes|Cacheträffar (Shard 0)|Antal|Totalt||Inga dimensioner|
|cachehits1|Yes|Cacheträffar (Shard 1)|Antal|Totalt||Inga dimensioner|
|cachehits2|Yes|Cacheträffar (Shard 2)|Antal|Totalt||Inga dimensioner|
|cachehits3|Yes|Cacheträffar (Shard 3)|Antal|Totalt||Inga dimensioner|
|cachehits4|Yes|Cacheträffar (Shard 4)|Antal|Totalt||Inga dimensioner|
|cachehits5|Yes|Cacheträffar (Shard 5)|Antal|Totalt||Inga dimensioner|
|cachehits6|Yes|Cacheträffar (Shard 6)|Antal|Totalt||Inga dimensioner|
|cachehits7|Yes|Cacheträffar (Shard 7)|Antal|Totalt||Inga dimensioner|
|cachehits8|Yes|Cacheträffar (Shard 8)|Antal|Totalt||Inga dimensioner|
|cachehits9|Yes|Cacheträffar (Shard 9)|Antal|Totalt||Inga dimensioner|
|cacheLatency|Yes|Mikrosekunder för cache-fördröjning (för hands version)|Antal|Genomsnitt||ShardId|
|cachemisses|Yes|Cachemissar|Antal|Totalt||ShardId|
|cachemisses0|Yes|Cachemissar (Shard 0)|Antal|Totalt||Inga dimensioner|
|cachemisses1|Yes|Cachemissar (Shard 1)|Antal|Totalt||Inga dimensioner|
|cachemisses2|Yes|Cachemissar (Shard 2)|Antal|Totalt||Inga dimensioner|
|cachemisses3|Yes|Cachemissar (Shard 3)|Antal|Totalt||Inga dimensioner|
|cachemisses4|Yes|Cachemissar (Shard 4)|Antal|Totalt||Inga dimensioner|
|cachemisses5|Yes|Cachemissar (Shard 5)|Antal|Totalt||Inga dimensioner|
|cachemisses6|Yes|Cachemissar (Shard 6)|Antal|Totalt||Inga dimensioner|
|cachemisses7|Yes|Cachemissar (Shard 7)|Antal|Totalt||Inga dimensioner|
|cachemisses8|Yes|Cachemissar (Shard 8)|Antal|Totalt||Inga dimensioner|
|cachemisses9|Yes|Cachemissar (Shard 9)|Antal|Totalt||Inga dimensioner|
|cachemissrate|Yes|Missar i cache|Procent|cachemissrate||ShardId|
|cacheRead|Yes|Cacheläsning|BytesPerSecond|Maximal||ShardId|
|cacheRead0|Yes|Läsning av cache (Shard 0)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead1|Yes|Läsning av cache (Shard 1)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead2|Yes|Läsning av cache (Shard 2)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead3|Yes|Läsning av cacheminne (Shard 3)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead4|Yes|Läsning av cacheminne (Shard 4)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead5|Yes|Läsning av cacheminne (Shard 5)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead6|Yes|Läsning av cache (Shard 6)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead7|Yes|Läsning av cache (Shard 7)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead8|Yes|Läsning av cache (Shard 8)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead9|Yes|Läsning av cacheminne (Shard 9)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheWrite|Yes|Cacheskrivning|BytesPerSecond|Maximal||ShardId|
|cacheWrite0|Yes|Skrivcache (Shard 0)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheWrite1|Yes|Skrivcache (Shard 1)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheWrite2|Yes|Skrivcache (Shard 2)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheWrite3|Yes|Skrivcache (Shard 3)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheWrite4|Yes|Skrivcache (Shard 4)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheWrite5|Yes|Skrivcache (Shard 5)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheWrite6|Yes|Skrivcache (Shard 6)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheWrite7|Yes|Skrivcache (Shard 7)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheWrite8|Yes|Skrivcache (Shard 8)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheWrite9|Yes|Skrivcache (Shard 9)|BytesPerSecond|Maximal||Inga dimensioner|
|connectedclients|Yes|Anslutna klienter|Antal|Maximal||ShardId|
|connectedclients0|Yes|Anslutna klienter (Shard 0)|Antal|Maximal||Inga dimensioner|
|connectedclients1|Yes|Anslutna klienter (Shard 1)|Antal|Maximal||Inga dimensioner|
|connectedclients2|Yes|Anslutna klienter (Shard 2)|Antal|Maximal||Inga dimensioner|
|connectedclients3|Yes|Anslutna klienter (Shard 3)|Antal|Maximal||Inga dimensioner|
|connectedclients4|Yes|Anslutna klienter (Shard 4)|Antal|Maximal||Inga dimensioner|
|connectedclients5|Yes|Anslutna klienter (Shard 5)|Antal|Maximal||Inga dimensioner|
|connectedclients6|Yes|Anslutna klienter (Shard 6)|Antal|Maximal||Inga dimensioner|
|connectedclients7|Yes|Anslutna klienter (Shard 7)|Antal|Maximal||Inga dimensioner|
|connectedclients8|Yes|Anslutna klienter (Shard 8)|Antal|Maximal||Inga dimensioner|
|connectedclients9|Yes|Anslutna klienter (Shard 9)|Antal|Maximal||Inga dimensioner|
|fel|Yes|Fel|Antal|Maximal||ShardId, ErrorType|
|evictedkeys|Yes|Avlägsnade nycklar|Antal|Totalt||ShardId|
|evictedkeys0|Yes|Avlägsnade nycklar (Shard 0)|Antal|Totalt||Inga dimensioner|
|evictedkeys1|Yes|Avlägsnade nycklar (Shard 1)|Antal|Totalt||Inga dimensioner|
|evictedkeys2|Yes|Avlägsnade nycklar (Shard 2)|Antal|Totalt||Inga dimensioner|
|evictedkeys3|Yes|Avlägsnade nycklar (Shard 3)|Antal|Totalt||Inga dimensioner|
|evictedkeys4|Yes|Avlägsnade nycklar (Shard 4)|Antal|Totalt||Inga dimensioner|
|evictedkeys5|Yes|Avlägsnade nycklar (Shard 5)|Antal|Totalt||Inga dimensioner|
|evictedkeys6|Yes|Avlägsnade nycklar (Shard 6)|Antal|Totalt||Inga dimensioner|
|evictedkeys7|Yes|Avlägsnade nycklar (Shard 7)|Antal|Totalt||Inga dimensioner|
|evictedkeys8|Yes|Avlägsnade nycklar (Shard 8)|Antal|Totalt||Inga dimensioner|
|evictedkeys9|Yes|Avlägsnade nycklar (Shard 9)|Antal|Totalt||Inga dimensioner|
|expiredkeys|Yes|Utgångna nycklar|Antal|Totalt||ShardId|
|expiredkeys0|Yes|Utgångna nycklar (Shard 0)|Antal|Totalt||Inga dimensioner|
|expiredkeys1|Yes|Utgångna nycklar (Shard 1)|Antal|Totalt||Inga dimensioner|
|expiredkeys2|Yes|Utgångna nycklar (Shard 2)|Antal|Totalt||Inga dimensioner|
|expiredkeys3|Yes|Utgångna nycklar (Shard 3)|Antal|Totalt||Inga dimensioner|
|expiredkeys4|Yes|Utgångna nycklar (Shard 4)|Antal|Totalt||Inga dimensioner|
|expiredkeys5|Yes|Utgångna nycklar (Shard 5)|Antal|Totalt||Inga dimensioner|
|expiredkeys6|Yes|Utgångna nycklar (Shard 6)|Antal|Totalt||Inga dimensioner|
|expiredkeys7|Yes|Utgångna nycklar (Shard 7)|Antal|Totalt||Inga dimensioner|
|expiredkeys8|Yes|Utgångna nycklar (Shard 8)|Antal|Totalt||Inga dimensioner|
|expiredkeys9|Yes|Utgångna nycklar (Shard 9)|Antal|Totalt||Inga dimensioner|
|getcommands|Yes|Hämtningar|Antal|Totalt||ShardId|
|getcommands0|Yes|Hämtar (Shard 0)|Antal|Totalt||Inga dimensioner|
|getcommands1|Yes|Hämtar (Shard 1)|Antal|Totalt||Inga dimensioner|
|getcommands2|Yes|Hämtar (Shard 2)|Antal|Totalt||Inga dimensioner|
|getcommands3|Yes|Hämtar (Shard 3)|Antal|Totalt||Inga dimensioner|
|getcommands4|Yes|Hämtar (Shard 4)|Antal|Totalt||Inga dimensioner|
|getcommands5|Yes|Hämtar (Shard 5)|Antal|Totalt||Inga dimensioner|
|getcommands6|Yes|Hämtar (Shard 6)|Antal|Totalt||Inga dimensioner|
|getcommands7|Yes|Hämtar (Shard 7)|Antal|Totalt||Inga dimensioner|
|getcommands8|Yes|Hämtar (Shard 8)|Antal|Totalt||Inga dimensioner|
|getcommands9|Yes|Hämtar (Shard 9)|Antal|Totalt||Inga dimensioner|
|operationsPerSecond|Yes|Åtgärder per sekund|Antal|Maximal||ShardId|
|operationsPerSecond0|Yes|Åtgärder per sekund (Shard 0)|Antal|Maximal||Inga dimensioner|
|operationsPerSecond1|Yes|Åtgärder per sekund (Shard 1)|Antal|Maximal||Inga dimensioner|
|operationsPerSecond2|Yes|Åtgärder per sekund (Shard 2)|Antal|Maximal||Inga dimensioner|
|operationsPerSecond3|Yes|Åtgärder per sekund (Shard 3)|Antal|Maximal||Inga dimensioner|
|operationsPerSecond4|Yes|Åtgärder per sekund (Shard 4)|Antal|Maximal||Inga dimensioner|
|operationsPerSecond5|Yes|Åtgärder per sekund (Shard 5)|Antal|Maximal||Inga dimensioner|
|operationsPerSecond6|Yes|Åtgärder per sekund (Shard 6)|Antal|Maximal||Inga dimensioner|
|operationsPerSecond7|Yes|Åtgärder per sekund (Shard 7)|Antal|Maximal||Inga dimensioner|
|operationsPerSecond8|Yes|Åtgärder per sekund (Shard 8)|Antal|Maximal||Inga dimensioner|
|operationsPerSecond9|Yes|Åtgärder per sekund (Shard 9)|Antal|Maximal||Inga dimensioner|
|percentProcessorTime|Yes|Processor|Procent|Maximal||ShardId|
|percentProcessorTime0|Yes|PROCESSOR (Shard 0)|Procent|Maximal||Inga dimensioner|
|percentProcessorTime1|Yes|PROCESSOR (Shard 1)|Procent|Maximal||Inga dimensioner|
|percentProcessorTime2|Yes|PROCESSOR (Shard 2)|Procent|Maximal||Inga dimensioner|
|percentProcessorTime3|Yes|PROCESSOR (Shard 3)|Procent|Maximal||Inga dimensioner|
|percentProcessorTime4|Yes|PROCESSOR (Shard 4)|Procent|Maximal||Inga dimensioner|
|percentProcessorTime5|Yes|PROCESSOR (Shard 5)|Procent|Maximal||Inga dimensioner|
|percentProcessorTime6|Yes|PROCESSOR (Shard 6)|Procent|Maximal||Inga dimensioner|
|percentProcessorTime7|Yes|PROCESSOR (Shard 7)|Procent|Maximal||Inga dimensioner|
|percentProcessorTime8|Yes|PROCESSOR (Shard 8)|Procent|Maximal||Inga dimensioner|
|percentProcessorTime9|Yes|PROCESSOR (Shard 9)|Procent|Maximal||Inga dimensioner|
|serverLoad|Yes|Serverbelastning|Procent|Maximal||ShardId|
|serverLoad0|Yes|Server belastning (Shard 0)|Procent|Maximal||Inga dimensioner|
|serverLoad1|Yes|Server belastning (Shard 1)|Procent|Maximal||Inga dimensioner|
|serverLoad2|Yes|Server belastning (Shard 2)|Procent|Maximal||Inga dimensioner|
|serverLoad3|Yes|Server belastning (Shard 3)|Procent|Maximal||Inga dimensioner|
|serverLoad4|Yes|Server belastning (Shard 4)|Procent|Maximal||Inga dimensioner|
|serverLoad5|Yes|Server belastning (Shard 5)|Procent|Maximal||Inga dimensioner|
|serverLoad6|Yes|Server belastning (Shard 6)|Procent|Maximal||Inga dimensioner|
|serverLoad7|Yes|Server belastning (Shard 7)|Procent|Maximal||Inga dimensioner|
|serverLoad8|Yes|Server belastning (Shard 8)|Procent|Maximal||Inga dimensioner|
|serverLoad9|Yes|Server belastning (Shard 9)|Procent|Maximal||Inga dimensioner|
|setcommands|Yes|Uppsättningar|Antal|Totalt||ShardId|
|setcommands0|Yes|Uppsättningar (Shard 0)|Antal|Totalt||Inga dimensioner|
|setcommands1|Yes|Uppsättningar (Shard 1)|Antal|Totalt||Inga dimensioner|
|setcommands2|Yes|Uppsättningar (Shard 2)|Antal|Totalt||Inga dimensioner|
|setcommands3|Yes|Uppsättningar (Shard 3)|Antal|Totalt||Inga dimensioner|
|setcommands4|Yes|Uppsättningar (Shard 4)|Antal|Totalt||Inga dimensioner|
|setcommands5|Yes|Uppsättningar (Shard 5)|Antal|Totalt||Inga dimensioner|
|setcommands6|Yes|Uppsättningar (Shard 6)|Antal|Totalt||Inga dimensioner|
|setcommands7|Yes|Uppsättningar (Shard 7)|Antal|Totalt||Inga dimensioner|
|setcommands8|Yes|Uppsättningar (Shard 8)|Antal|Totalt||Inga dimensioner|
|setcommands9|Yes|Uppsättningar (Shard 9)|Antal|Totalt||Inga dimensioner|
|totalcommandsprocessed|Yes|Totalt antal åtgärder|Antal|Totalt||ShardId|
|totalcommandsprocessed0|Yes|Totalt antal åtgärder (Shard 0)|Antal|Totalt||Inga dimensioner|
|totalcommandsprocessed1|Yes|Totalt antal åtgärder (Shard 1)|Antal|Totalt||Inga dimensioner|
|totalcommandsprocessed2|Yes|Totalt antal åtgärder (Shard 2)|Antal|Totalt||Inga dimensioner|
|totalcommandsprocessed3|Yes|Totalt antal åtgärder (Shard 3)|Antal|Totalt||Inga dimensioner|
|totalcommandsprocessed4|Yes|Totalt antal åtgärder (Shard 4)|Antal|Totalt||Inga dimensioner|
|totalcommandsprocessed5|Yes|Totalt antal åtgärder (Shard 5)|Antal|Totalt||Inga dimensioner|
|totalcommandsprocessed6|Yes|Totalt antal åtgärder (Shard 6)|Antal|Totalt||Inga dimensioner|
|totalcommandsprocessed7|Yes|Totalt antal åtgärder (Shard 7)|Antal|Totalt||Inga dimensioner|
|totalcommandsprocessed8|Yes|Totalt antal åtgärder (Shard 8)|Antal|Totalt||Inga dimensioner|
|totalcommandsprocessed9|Yes|Totalt antal åtgärder (Shard 9)|Antal|Totalt||Inga dimensioner|
|totalkeys|Yes|Totalt antal nycklar|Antal|Maximal||ShardId|
|totalkeys0|Yes|Totalt antal nycklar (Shard 0)|Antal|Maximal||Inga dimensioner|
|totalkeys1|Yes|Totalt antal nycklar (Shard 1)|Antal|Maximal||Inga dimensioner|
|totalkeys2|Yes|Totalt antal nycklar (Shard 2)|Antal|Maximal||Inga dimensioner|
|totalkeys3|Yes|Totalt antal nycklar (Shard 3)|Antal|Maximal||Inga dimensioner|
|totalkeys4|Yes|Totalt antal nycklar (Shard 4)|Antal|Maximal||Inga dimensioner|
|totalkeys5|Yes|Totalt antal nycklar (Shard 5)|Antal|Maximal||Inga dimensioner|
|totalkeys6|Yes|Totalt antal nycklar (Shard 6)|Antal|Maximal||Inga dimensioner|
|totalkeys7|Yes|Totalt antal nycklar (Shard 7)|Antal|Maximal||Inga dimensioner|
|totalkeys8|Yes|Totalt antal nycklar (Shard 8)|Antal|Maximal||Inga dimensioner|
|totalkeys9|Yes|Totalt antal nycklar (Shard 9)|Antal|Maximal||Inga dimensioner|
|usedmemory|Yes|Använt minne|Byte|Maximal||ShardId|
|usedmemory0|Yes|Använt minne (Shard 0)|Byte|Maximal||Inga dimensioner|
|usedmemory1|Yes|Använt minne (Shard 1)|Byte|Maximal||Inga dimensioner|
|usedmemory2|Yes|Använt minne (Shard 2)|Byte|Maximal||Inga dimensioner|
|usedmemory3|Yes|Använt minne (Shard 3)|Byte|Maximal||Inga dimensioner|
|usedmemory4|Yes|Använt minne (Shard 4)|Byte|Maximal||Inga dimensioner|
|usedmemory5|Yes|Använt minne (Shard 5)|Byte|Maximal||Inga dimensioner|
|usedmemory6|Yes|Använt minne (Shard 6)|Byte|Maximal||Inga dimensioner|
|usedmemory7|Yes|Använt minne (Shard 7)|Byte|Maximal||Inga dimensioner|
|usedmemory8|Yes|Använt minne (Shard 8)|Byte|Maximal||Inga dimensioner|
|usedmemory9|Yes|Använt minne (Shard 9)|Byte|Maximal||Inga dimensioner|
|usedmemorypercentage|Yes|Använt minne i procent|Procent|Maximal||ShardId|
|usedmemoryRss|Yes|RSS för använt minne|Byte|Maximal||ShardId|
|usedmemoryRss0|Yes|Använt minne RSS (Shard 0)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss1|Yes|RSS för använt minne (Shard 1)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss2|Yes|Använt minne RSS (Shard 2)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss3|Yes|Använt minne RSS (Shard 3)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss4|Yes|Använt minne RSS (Shard 4)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss5|Yes|Använt minne RSS (Shard 5)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss6|Yes|Använt minne RSS (Shard 6)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss7|Yes|Använt minne RSS (Shard 7)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss8|Yes|Använt minne RSS (Shard 8)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss9|Yes|Använt minne RSS (Shard 9)|Byte|Maximal||Inga dimensioner|


## <a name="microsoftcacheredisenterprise"></a>Microsoft. cache/redisEnterprise

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|cachehits|Yes|Cacheträffar|Antal|Totalt||Inga dimensioner|
|cacheLatency|Yes|Mikrosekunder för cache-fördröjning (för hands version)|Antal|Genomsnitt||InstanceId|
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
|usedmemoryRss|Yes|RSS för använt minne|Byte|Maximal||InstanceId|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Yes|Antal begär Anden om webb programs brand vägg|Antal|Totalt|Antalet klient begär Anden som bearbetats av brand väggen för webbaserade program|PolicyName, RuleName, åtgärd|


## <a name="microsoftcdnprofiles"></a>Microsoft. CDN/profiler

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ByteHitRatio|Yes|Kvot för byte träff|Procent|Genomsnitt|Detta är förhållandet mellan totalt antal byte som hanteras från cachen jämfört med totalt antal svars byte|Slutpunkt|
|OriginHealthPercentage|Yes|Ursprungligt hälso procent|Procent|Genomsnitt|Procent andelen lyckade hälso avsökningar från AFDX till Server delar.|Ursprung, OriginPool|
|OriginLatency|Yes|Ursprunglig svars tid|Millisekunder|Genomsnitt|Tiden räknat från när begäran skickades av AFDX Edge till Server delen tills AFDX fick den senaste svars byten från Server delen.|Ursprung, slut punkt|
|OriginRequestCount|Yes|Antal ursprungs begär Anden|Antal|Totalt|Antalet begär Anden som skickats från AFDX till ursprung.|HttpStatus, HttpStatusGroup, ursprung, slut punkt|
|Percentage4XX|Yes|Procent andel 4XX|Procent|Genomsnitt|Procent andelen av alla klient begär Anden för vilka svars status koden är 4XX|Slut punkt, ClientRegion, ClientCountry|
|Percentage5XX|Yes|Procent andel 5XX|Procent|Genomsnitt|Procent andelen av alla klient begär Anden för vilka svars status koden är 5XX|Slut punkt, ClientRegion, ClientCountry|
|RequestCount|Yes|Antal begäranden|Antal|Totalt|Antalet klient förfrågningar som hanteras av HTTP/S-proxyn|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, slut punkt|
|RequestSize|Yes|Begär ande storlek|Byte|Totalt|Antalet byte som har skickats som begär Anden från klienter till AFDX.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, slut punkt|
|ResponseSize|Yes|Svars storlek|Byte|Totalt|Antalet byte som skickats som svar från HTTP/S-proxy till klienter|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, slut punkt|
|TotalLatency|Yes|Total svars tid|Millisekunder|Genomsnitt|Den tid som beräknas från när klientbegäran togs emot av HTTP/S-proxy tills klienten bekräftade den senaste svars byten från HTTP/S-proxyn|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry, slut punkt|
|WebApplicationFirewallRequestCount|Yes|Antal begär Anden om webb programs brand vägg|Antal|Totalt|Antalet klient begär Anden som bearbetats av brand väggen för webbaserade program|PolicyName, RuleName, åtgärd|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft. ClassicCompute/domän namn/platser/roller

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Disk-lästa byte/s|No|Disk läsning|BytesPerSecond|Genomsnitt|Genomsnittligt antal byte som lästs från disk under övervaknings perioden.|RoleInstanceId|
|Disk Läs åtgärder/SEK|Yes|Disk Läs åtgärder/SEK|CountPerSecond|Genomsnitt|Disk-Läs-IOPS.|RoleInstanceId|
|Disk-skrivna byte/s|No|Disk skrivning|BytesPerSecond|Genomsnitt|Genomsnittligt antal byte som skrivs till disk under övervaknings perioden.|RoleInstanceId|
|Disk skrivnings åtgärder/SEK|Yes|Disk skrivnings åtgärder/SEK|CountPerSecond|Genomsnitt|Skriv IOPS för disk.|RoleInstanceId|
|Nätverk – inkommande|Yes|Nätverk – inkommande|Byte|Totalt|Antalet byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik).|RoleInstanceId|
|Nätverk – utgående|Yes|Nätverk – utgående|Byte|Totalt|Antalet byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik).|RoleInstanceId|
|Processorprocentandel|Yes|Processorprocentandel|Procent|Genomsnitt|Procent andelen allokerade beräknings enheter som för närvarande används av den eller de virtuella datorerna.|RoleInstanceId|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft. ClassicCompute/virtualMachines

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Disk-lästa byte/s|No|Disk läsning|BytesPerSecond|Genomsnitt|Genomsnittligt antal byte som lästs från disk under övervaknings perioden.|Inga dimensioner|
|Disk Läs åtgärder/SEK|Yes|Disk Läs åtgärder/SEK|CountPerSecond|Genomsnitt|Disk-Läs-IOPS.|Inga dimensioner|
|Disk-skrivna byte/s|No|Disk skrivning|BytesPerSecond|Genomsnitt|Genomsnittligt antal byte som skrivs till disk under övervaknings perioden.|Inga dimensioner|
|Disk skrivnings åtgärder/SEK|Yes|Disk skrivnings åtgärder/SEK|CountPerSecond|Genomsnitt|Skriv IOPS för disk.|Inga dimensioner|
|Nätverk – inkommande|Yes|Nätverk – inkommande|Byte|Totalt|Antalet byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik).|Inga dimensioner|
|Nätverk – utgående|Yes|Nätverk – utgående|Byte|Totalt|Antalet byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik).|Inga dimensioner|
|Processorprocentandel|Yes|Processorprocentandel|Procent|Genomsnitt|Procent andelen allokerade beräknings enheter som för närvarande används av den eller de virtuella datorerna.|Inga dimensioner|


## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft. ClassicStorage/storageAccounts

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Yes|Tillgänglighet|Procent|Genomsnitt|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|
|Utgående|Yes|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|Ingress|Yes|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Yes|Lyckad E2E-svarstid|Millisekunder|Genomsnitt|Svars tiden från slut punkt till slut punkt för lyckade begär anden till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Yes|Lyckad Server svars tid|Millisekunder|Genomsnitt|Svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ av typ, ApiName, autentisering|
|Transaktioner|Yes|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|
|UsedCapacity|No|Använd kapacitet|Byte|Genomsnitt|Kapacitet som används av konto|Inga dimensioner|


## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft. ClassicStorage/storageAccounts/blobServices

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Yes|Tillgänglighet|Procent|Genomsnitt|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|
|BlobCapacity|No|BLOB-kapacitet|Byte|Genomsnitt|Mängden lagrings utrymme som används av lagrings kontots Blob Service i byte.|BlobType,-nivå|
|BlobCount|No|BLOB-antal|Antal|Genomsnitt|Antalet BLOB i lagrings kontots Blob Service.|BlobType,-nivå|
|ContainerCount|Yes|Antal BLOB-behållare|Antal|Genomsnitt|Antalet behållare i lagrings kontots Blob Service.|Inga dimensioner|
|Utgående|Yes|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|IndexCapacity|No|Index kapacitet|Byte|Genomsnitt|Mängden lagrings utrymme som används av ADLS Gen2 (hierarkiskt) index i byte.|Inga dimensioner|
|Ingress|Yes|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Yes|Lyckad E2E-svarstid|Millisekunder|Genomsnitt|Svars tiden från slut punkt till slut punkt för lyckade begär anden till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Yes|Lyckad Server svars tid|Millisekunder|Genomsnitt|Svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ av typ, ApiName, autentisering|
|Transaktioner|Yes|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|


## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft. ClassicStorage/storageAccounts/fileServices

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Yes|Tillgänglighet|Procent|Genomsnitt|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ, ApiName, autentisering, FileShare|
|Utgående|Yes|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ, ApiName, autentisering, FileShare|
|FileCapacity|No|Fil kapacitet|Byte|Genomsnitt|Mängden lagrings utrymme som används av lagrings kontots fil tjänst i byte.|FileShare|
|FileCount|No|Antal filer|Antal|Genomsnitt|Antalet filer i lagrings kontots fil tjänst.|FileShare|
|FileShareCount|No|Antal fil resurser|Antal|Genomsnitt|Antalet fil resurser i lagrings kontots fil tjänst.|Inga dimensioner|
|FileShareQuota|No|Fil resursens kvot storlek|Byte|Genomsnitt|Den övre gränsen för mängden lagrings utrymme som kan användas av Azure Files tjänsten i byte.|FileShare|
|FileShareSnapshotCount|No|Antal ögonblicks bilder av fil resurs|Antal|Genomsnitt|Antal ögonblicks bilder som finns på resursen i lagrings kontots fil tjänst.|FileShare|
|FileShareSnapshotSize|No|Storlek på fil resursens ögonblicks bild|Byte|Genomsnitt|Mängden lagrings utrymme som används av ögonblicks bilderna i lagrings kontots fil tjänst i byte.|FileShare|
|Ingress|Yes|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ, ApiName, autentisering, FileShare|
|SuccessE2ELatency|Yes|Lyckad E2E-svarstid|Millisekunder|Genomsnitt|Svars tiden från slut punkt till slut punkt för lyckade begär anden till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ, ApiName, autentisering, FileShare|
|SuccessServerLatency|Yes|Lyckad Server svars tid|Millisekunder|Genomsnitt|Svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ, ApiName, autentisering, FileShare|
|Transaktioner|Yes|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering, FileShare|


## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft. ClassicStorage/storageAccounts/queueServices

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Yes|Tillgänglighet|Procent|Genomsnitt|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|
|Utgående|Yes|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|Ingress|Yes|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|QueueCapacity|Yes|Kös Kap kap.|Byte|Genomsnitt|Mängden lagrings utrymme som används av lagrings kontots Kötjänst i byte.|Inga dimensioner|
|QueueCount|Yes|Antal köer|Antal|Genomsnitt|Antalet köer i lagrings kontots Kötjänst.|Inga dimensioner|
|QueueMessageCount|Yes|Antal meddelanden i kö|Antal|Genomsnitt|Ungefärligt antal köa meddelanden i lagrings kontots Kötjänst.|Inga dimensioner|
|SuccessE2ELatency|Yes|Lyckad E2E-svarstid|Millisekunder|Genomsnitt|Svars tiden från slut punkt till slut punkt för lyckade begär anden till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Yes|Lyckad Server svars tid|Millisekunder|Genomsnitt|Svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ av typ, ApiName, autentisering|
|Transaktioner|Yes|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|


## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft. ClassicStorage/storageAccounts/tableServices

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Yes|Tillgänglighet|Procent|Genomsnitt|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|
|Utgående|Yes|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|Ingress|Yes|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Yes|Lyckad E2E-svarstid|Millisekunder|Genomsnitt|Svars tiden från slut punkt till slut punkt för lyckade begär anden till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Yes|Lyckad Server svars tid|Millisekunder|Genomsnitt|Svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ av typ, ApiName, autentisering|
|TableCapacity|Yes|Tabell kapacitet|Byte|Genomsnitt|Mängden lagrings utrymme som används av lagrings kontots Table service i byte.|Inga dimensioner|
|TableCount|Yes|Antal tabeller|Antal|Genomsnitt|Antalet tabeller i lagrings kontots Table service.|Inga dimensioner|
|TableEntityCount|Yes|Antal tabell enheter|Antal|Genomsnitt|Antalet tabell enheter i lagrings kontots Table service.|Inga dimensioner|
|Transaktioner|Yes|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft. CognitiveServices/konton

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|BlockedCalls|Yes|Blockerade anrop|Antal|Totalt|Antal anrop som har överskridit frekvensen eller kvot gränsen.|ApiName, OperationName, region|
|CharactersTrained|Yes|Upptränade tecken|Antal|Totalt|Totalt antal tecken som har tränats.|ApiName, OperationName, region|
|CharactersTranslated|Yes|Översatta tecken|Antal|Totalt|Totalt antal tecken i begäran om inkommande text.|ApiName, OperationName, region|
|ClientErrors|Yes|Klient fel|Antal|Totalt|Antal anrop med fel på klient sidan (HTTP-svarskod 4xx).|ApiName, OperationName, region|
|Datain|Yes|Data i|Byte|Totalt|Storlek på inkommande data i byte.|ApiName, OperationName, region|
|Data|Yes|Data ut|Byte|Totalt|Storlek på utgående data i byte.|ApiName, OperationName, region|
|Svarstid|Yes|Svarstid|Millisekunder|Genomsnitt|Svars tid i millisekunder.|ApiName, OperationName, region|
|LearnedEvents|Yes|Inlärda händelser|Antal|Totalt|Antal inlärda händelser.|IsMatchBaseline, mode, RunId|
|MatchedRewards|Yes|Matchade förmåner|Antal|Totalt| Antal matchade belöningar.|IsMatchBaseline, mode, RunId|
|ObservedRewards|Yes|Observerade förmåner|Antal|Totalt|Antal observerade belöningar.|IsMatchBaseline, mode, RunId|
|ProcessedCharacters|Yes|Bearbetade tecken|Antal|Totalt|Antal tecken.|ApiName, FeatureName, UsageChannel, region|
|ProcessedTextRecords|Yes|Bearbetade text poster|Antal|Totalt|Antal text poster.|ApiName, FeatureName, UsageChannel, region|
|ServerErrors|Yes|Server fel|Antal|Totalt|Antal anrop med internt tjänst fel (HTTP-svarskod 5xx).|ApiName, OperationName, region|
|SpeechSessionDuration|Yes|Tal sessionens varaktighet|Sekunder|Totalt|Total varaktighet för talläget på några sekunder.|ApiName, OperationName, region|
|SuccessfulCalls|Yes|Lyckade anrop|Antal|Totalt|Antal lyckade anrop.|ApiName, OperationName, region|
|TotalCalls|Yes|Totalt antal anrop|Antal|Totalt|Totalt antal anrop.|ApiName, OperationName, region|
|TotalErrors|Yes|Totalt antal fel|Antal|Totalt|Totalt antal anrop med fel svar (HTTP-svarskod 4xx eller 5xx).|ApiName, OperationName, region|
|TotalTokenCalls|Yes|Totalt antal token-anrop|Antal|Totalt|Totalt antal token-anrop.|ApiName, OperationName, region|
|TotalTransactions|Yes|Totalt antal transaktioner|Antal|Totalt|Totalt antal transaktioner.|Inga dimensioner|


## <a name="microsoftcommunicationcommunicationservices"></a>Microsoft. Communication/CommunicationServices

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|APIRequestAuthentication|No|API-begäranden för autentisering|Antal|Antal|Antalet förfrågningar mot kommunikations tjänstens autentiserings slut punkt.|Åtgärd, StatusCode, StatusCodeClass|
|APIRequestChat|Yes|API-begäranden i Chat|Antal|Antal|Antal förfrågningar mot kommunikations tjänstens Chat-slutpunkt.|Åtgärd, StatusCode, StatusCodeClass|
|APIRequestSMS|Yes|SMS API-begäranden|Antal|Antal|Antal begär Anden mot SMS-slutpunkten för kommunikations tjänster.|Åtgärd, StatusCode, StatusCodeClass|


## <a name="microsoftcomputecloudservices"></a>Microsoft. Compute/cloudServices

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Lästa byte på disk|Yes|Lästa byte på disk|Byte|Totalt|Byte som lästs från disk under övervaknings perioden|RoleInstanceId, RoleId|
|Disk Läs åtgärder/SEK|Yes|Disk Läs åtgärder/SEK|CountPerSecond|Genomsnitt|Disk-Läs-IOPS|RoleInstanceId, RoleId|
|Disk-skrivna byte|Yes|Disk-skrivna byte|Byte|Totalt|Byte som skrivits till disk under övervaknings perioden|RoleInstanceId, RoleId|
|Disk skrivnings åtgärder/SEK|Yes|Disk skrivnings åtgärder/SEK|CountPerSecond|Genomsnitt|Skriv IOPS för disk|RoleInstanceId, RoleId|
|Processorprocentandel|Yes|Processorprocentandel|Procent|Genomsnitt|Procent andelen allokerade beräknings enheter som för närvarande används av den eller de virtuella datorerna|RoleInstanceId, RoleId|


## <a name="microsoftcomputecloudservicesroles"></a>Microsoft. Compute/cloudServices/roles

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Lästa byte på disk|Yes|Lästa byte på disk|Byte|Totalt|Byte som lästs från disk under övervaknings perioden|RoleInstanceId, RoleId|
|Disk Läs åtgärder/SEK|Yes|Disk Läs åtgärder/SEK|CountPerSecond|Genomsnitt|Disk-Läs-IOPS|RoleInstanceId, RoleId|
|Disk-skrivna byte|Yes|Disk-skrivna byte|Byte|Totalt|Byte som skrivits till disk under övervaknings perioden|RoleInstanceId, RoleId|
|Disk skrivnings åtgärder/SEK|Yes|Disk skrivnings åtgärder/SEK|CountPerSecond|Genomsnitt|Skriv IOPS för disk|RoleInstanceId, RoleId|
|Processorprocentandel|Yes|Processorprocentandel|Procent|Genomsnitt|Procent andelen allokerade beräknings enheter som för närvarande används av den eller de virtuella datorerna|RoleInstanceId, RoleId|


## <a name="microsoftcomputedisks"></a>Microsoft. Compute/disks

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Lästa byte för sammansatt disk/SEK|No|Disk-lästa byte/SEK (för hands version)|Byte|Genomsnitt|Byte/s lästes från disk under övervaknings perioden, Observera att det här måttet är i för hands version och kan komma att ändras innan det blir allmänt tillgängligt||
|Sammansatta disk Läs åtgärder/SEK|No|Disk Läs åtgärder/SEK (för hands version)|Byte|Genomsnitt|Antalet Läs-IOs som har utförts på en disk under övervaknings perioden. Observera att det här måttet är i för hands version och kan komma att ändras innan det blir allmänt tillgängligt||
|Skrivna byte för sammansatt disk/SEK|No|Disk-skrivna byte/s (för hands version)|Byte|Genomsnitt|Byte/s skrivs till disk under övervaknings perioden, Observera att det här måttet är i för hands version och kan komma att ändras innan det blir allmänt tillgängligt||
|Skriv åtgärder för sammansatt disk/SEK|No|Disk skrivnings åtgärder/SEK (för hands version)|Byte|Genomsnitt|Antalet skriv-IOs som har utförts på en disk under övervaknings perioden, Observera att det här måttet är i för hands version och kan komma att ändras innan det blir allmänt tillgängligt||


## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Förbrukade CPU-krediter|Yes|Förbrukade CPU-krediter|Antal|Genomsnitt|Totalt antal krediter som konsumeras av den virtuella datorn|Inga dimensioner|
|Återstående CPU-krediter|Yes|Återstående CPU-krediter|Antal|Genomsnitt|Totalt antal krediter som är tillgängliga för burst|Inga dimensioner|
|Förbrukad mängd bandbredd för data disk|Yes|Förbrukad mängd bandbredd för data disk|Procent|Genomsnitt|Procent andel data diskens förbrukade bandbredd per minut|ENHETEN|
|Förbrukad rabatt i data disk IOPS|Yes|Förbrukad rabatt i data disk IOPS|Procent|Genomsnitt|Procent andel data disk-I/o-förbrukat per minut|ENHETEN|
|Data disk Max burst-bandbredd|Yes|Data disk Max burst-bandbredd|Antal|Genomsnitt|Maximalt antal byte per sekund data disk data disk kan uppnås med bursting|ENHETEN|
|Högsta burst-IOPS för data disk|Yes|Högsta burst-IOPS för data disk|Antal|Genomsnitt|Högsta IOPS-datadisk kan uppnås med bursting|ENHETEN|
|Ködjup för datadisk|Yes|Ködjup för datadisk|Antal|Genomsnitt|Data diskens ködjup (eller Kölängd)|ENHETEN|
|Lästa byte på datadisk/SEK|Yes|Lästa byte på datadisk/SEK|BytesPerSecond|Genomsnitt|Byte/s lästa från en enskild disk under övervaknings perioden|ENHETEN|
|Läs åtgärder för data disk/SEK|Yes|Läs åtgärder för data disk/SEK|CountPerSecond|Genomsnitt|Läsa IOPS från en enskild disk under övervaknings perioden|ENHETEN|
|Bandbredd för data disk mål|Yes|Bandbredd för data disk mål|Antal|Genomsnitt|Bas linje byte per sekund data disk kan uppnås utan burst-överföring|ENHETEN|
|Data disk mål IOPS|Yes|Data disk mål IOPS|Antal|Genomsnitt|Bas linje för IOPS-datadisk kan uppnås utan burst-överföring|ENHETEN|
|Procent andel data disk Använd burst BPS-kredit|Yes|Procent andel data disk Använd burst BPS-kredit|Procent|Genomsnitt|Procent andel av data diskens bandbredds krediter som använts hittills|ENHETEN|
|Procent andel förbrukade IO-krediter i data disk|Yes|Procent andel förbrukade IO-krediter i data disk|Procent|Genomsnitt|Procent andel data disk burst I/O-krediter som använts hittills|ENHETEN|
|Skrivna byte på datadisk/SEK|Yes|Skrivna byte på datadisk/SEK|BytesPerSecond|Genomsnitt|Byte/s skrivs till en enskild disk under övervaknings perioden|ENHETEN|
|Skriv åtgärder för data disk/SEK|Yes|Skriv åtgärder för data disk/SEK|CountPerSecond|Genomsnitt|Skriv IOPS från en enskild disk under övervaknings perioden|ENHETEN|
|Lästa byte på disk|Yes|Lästa byte på disk|Byte|Totalt|Byte som lästs från disk under övervaknings perioden|Inga dimensioner|
|Disk Läs åtgärder/SEK|Yes|Disk Läs åtgärder/SEK|CountPerSecond|Genomsnitt|Disk-Läs-IOPS|Inga dimensioner|
|Disk-skrivna byte|Yes|Disk-skrivna byte|Byte|Totalt|Byte som skrivits till disk under övervaknings perioden|Inga dimensioner|
|Disk skrivnings åtgärder/SEK|Yes|Disk skrivnings åtgärder/SEK|CountPerSecond|Genomsnitt|Skriv IOPS för disk|Inga dimensioner|
|Inkommande flöden|Yes|Inkommande flöden|Antal|Genomsnitt|Inkommande flöden är antalet aktuella flöden i den inkommande riktningen (trafik som går till den virtuella datorn)|Inga dimensioner|
|Högsta skapande frekvens för inkommande flöden|Yes|Högsta skapande frekvens för inkommande flöden|CountPerSecond|Genomsnitt|Högsta skapande frekvens för inkommande flöden (trafik som går till den virtuella datorn)|Inga dimensioner|
|Nätverk – inkommande|Yes|Nätverk i fakturerbart (inaktuellt)|Byte|Totalt|Antalet fakturerbara byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik) (inaktuell)|Inga dimensioner|
|Totalt nätverk|Yes|Totalt nätverk|Byte|Totalt|Antalet byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik)|Inga dimensioner|
|Nätverk – utgående|Yes|Fakturerbart för nätverk (inaktuellt)|Byte|Totalt|Antalet fakturerbara byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik) (inaktuell)|Inga dimensioner|
|Totalt nätverk|Yes|Totalt nätverk|Byte|Totalt|Antalet byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik)|Inga dimensioner|
|Förbrukade procent av OS-diskens bandbredd|Yes|Förbrukade procent av OS-diskens bandbredd|Procent|Genomsnitt|Procent andel förbrukad bandbredd för operativ system disk per minut|ENHETEN|
|Förbrukade procent av OS-diskens IOPS|Yes|Förbrukade procent av OS-diskens IOPS|Procent|Genomsnitt|Procent andel operativ system disk I/o-förbrukat per minut|ENHETEN|
|Högsta bandbredd för OS-disk|Yes|Högsta bandbredd för OS-disk|Antal|Genomsnitt|Maximalt antal byte per sekund som data flödes operativ system disk kan uppnå med burst-överföring|ENHETEN|
|Högsta burst-IOPS för OS-disk|Yes|Högsta burst-IOPS för OS-disk|Antal|Genomsnitt|Maximal IOPS OS-disk kan uppnås med bursting|ENHETEN|
|Ködjup för OS-disk|Yes|Ködjup för OS-disk|Antal|Genomsnitt|Ködjup i operativ systemets disk (eller Kölängd)|Inga dimensioner|
|Lästa byte för OS-disk/SEK|Yes|Lästa byte för OS-disk/SEK|BytesPerSecond|Genomsnitt|Byte/s lästa från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Läs åtgärder för operativ system disk/SEK|Yes|Läs åtgärder för operativ system disk/SEK|CountPerSecond|Genomsnitt|Läsa IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Bandbredd för OS-diskens mål|Yes|Bandbredd för OS-diskens mål|Antal|Genomsnitt|Bas linje byte per sekund data flödes operativ system disk kan uppnås utan burst-överföring|ENHETEN|
|OS-disk mål IOPS|Yes|OS-disk mål IOPS|Antal|Genomsnitt|Bas linje för IOPS OS-disk kan uppnås utan burst-överföring|ENHETEN|
|Procent andel förbrukade OS-diskar med burst BPS|Yes|Procent andel förbrukade OS-diskar med burst BPS|Procent|Genomsnitt|Procent andel av OS-diskens burst-bandbredds krediter som använts hittills|ENHETEN|
|Procent andel förbrukade IO-krediter i OS-disk|Yes|Procent andel förbrukade IO-krediter i OS-disk|Procent|Genomsnitt|Procent andel av OS-diskens burst-krediter som använts hittills|ENHETEN|
|Skrivna byte per operativ system disk/SEK|Yes|Skrivna byte per operativ system disk/SEK|BytesPerSecond|Genomsnitt|Byte/s skrivs till en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Skriv åtgärder för operativ system disk/SEK|Yes|Skriv åtgärder för operativ system disk/SEK|CountPerSecond|Genomsnitt|Skriv IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Utgående flöden|Yes|Utgående flöden|Antal|Genomsnitt|Utgående flöden är antalet aktuella flöden i den utgående riktningen (trafik som går ut från den virtuella datorn)|Inga dimensioner|
|Längsta skapande frekvens för utgående flöden|Yes|Längsta skapande frekvens för utgående flöden|CountPerSecond|Genomsnitt|Den maximala skapande frekvensen för utgående flöden (trafik som går ut från den virtuella datorn)|Inga dimensioner|
|Processorprocentandel|Yes|Processorprocentandel|Procent|Genomsnitt|Procent andelen allokerade beräknings enheter som för närvarande används av den eller de virtuella datorerna|Inga dimensioner|
|Läsning av Premium data disk-cache|Yes|Läsning av Premium data disk-cache|Procent|Genomsnitt|Läsning av Premium data disk-cache|ENHETEN|
|Read missar i Premium data-diskcachen|Yes|Read missar i Premium data-diskcachen|Procent|Genomsnitt|Read missar i Premium data-diskcachen|ENHETEN|
|Läsning av Premium OS-diskcachen|Yes|Läsning av Premium OS-diskcachen|Procent|Genomsnitt|Läsning av Premium OS-diskcachen|Inga dimensioner|
|Read missar i Premium OS-diskcachen|Yes|Read missar i Premium OS-diskcachen|Procent|Genomsnitt|Read missar i Premium OS-diskcachen|Inga dimensioner|
|Cachelagrad bandbredd i procent för VM|Yes|Cachelagrad bandbredd i procent för VM|Procent|Genomsnitt|Procent andel cachelagrad disk bandbredd som används av den virtuella datorn|Inga dimensioner|
|VM cachelagrad IOPS förbrukad procent|Yes|VM cachelagrad IOPS förbrukad procent|Procent|Genomsnitt|Procent andel cachelagrad disk-IOPS som används av den virtuella datorn|Inga dimensioner|
|Förbrukad procent andel förbrukade bandbredd i VM|Yes|Förbrukad procent andel förbrukade bandbredd i VM|Procent|Genomsnitt|Procent andel ej cachelagrad disk bandbredd som används av den virtuella datorn|Inga dimensioner|
|VM, ej cachelagrad IOPS, procent andel|Yes|VM, ej cachelagrad IOPS, procent andel|Procent|Genomsnitt|Procent andel ej cachelagrat disk-IOPS som används av den virtuella datorn|Inga dimensioner|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Förbrukade CPU-krediter|Yes|Förbrukade CPU-krediter|Antal|Genomsnitt|Totalt antal krediter som konsumeras av den virtuella datorn|Inga dimensioner|
|Återstående CPU-krediter|Yes|Återstående CPU-krediter|Antal|Genomsnitt|Totalt antal krediter som är tillgängliga för burst|Inga dimensioner|
|Förbrukad mängd bandbredd för data disk|Yes|Förbrukad mängd bandbredd för data disk|Procent|Genomsnitt|Procent andel data diskens förbrukade bandbredd per minut|LUN, VMName|
|Förbrukad rabatt i data disk IOPS|Yes|Förbrukad rabatt i data disk IOPS|Procent|Genomsnitt|Procent andel data disk-I/o-förbrukat per minut|LUN, VMName|
|Data disk Max burst-bandbredd|Yes|Data disk Max burst-bandbredd|Antal|Genomsnitt|Maximalt antal byte per sekund data disk data disk kan uppnås med bursting|LUN, VMName|
|Högsta burst-IOPS för data disk|Yes|Högsta burst-IOPS för data disk|Antal|Genomsnitt|Högsta IOPS-datadisk kan uppnås med bursting|LUN, VMName|
|Ködjup för datadisk|Yes|Ködjup för datadisk|Antal|Genomsnitt|Data diskens ködjup (eller Kölängd)|LUN, VMName|
|Lästa byte på datadisk/SEK|Yes|Lästa byte på datadisk/SEK|BytesPerSecond|Genomsnitt|Byte/s lästa från en enskild disk under övervaknings perioden|LUN, VMName|
|Läs åtgärder för data disk/SEK|Yes|Läs åtgärder för data disk/SEK|CountPerSecond|Genomsnitt|Läsa IOPS från en enskild disk under övervaknings perioden|LUN, VMName|
|Bandbredd för data disk mål|Yes|Bandbredd för data disk mål|Antal|Genomsnitt|Bas linje byte per sekund data disk kan uppnås utan burst-överföring|LUN, VMName|
|Data disk mål IOPS|Yes|Data disk mål IOPS|Antal|Genomsnitt|Bas linje för IOPS-datadisk kan uppnås utan burst-överföring|LUN, VMName|
|Procent andel data disk Använd burst BPS-kredit|Yes|Procent andel data disk Använd burst BPS-kredit|Procent|Genomsnitt|Procent andel av data diskens bandbredds krediter som använts hittills|LUN, VMName|
|Procent andel förbrukade IO-krediter i data disk|Yes|Procent andel förbrukade IO-krediter i data disk|Procent|Genomsnitt|Procent andel data disk burst I/O-krediter som använts hittills|LUN, VMName|
|Skrivna byte på datadisk/SEK|Yes|Skrivna byte på datadisk/SEK|BytesPerSecond|Genomsnitt|Byte/s skrivs till en enskild disk under övervaknings perioden|LUN, VMName|
|Skriv åtgärder för data disk/SEK|Yes|Skriv åtgärder för data disk/SEK|CountPerSecond|Genomsnitt|Skriv IOPS från en enskild disk under övervaknings perioden|LUN, VMName|
|Lästa byte på disk|Yes|Lästa byte på disk|Byte|Totalt|Byte som lästs från disk under övervaknings perioden|VMName|
|Disk Läs åtgärder/SEK|Yes|Disk Läs åtgärder/SEK|CountPerSecond|Genomsnitt|Disk-Läs-IOPS|VMName|
|Disk-skrivna byte|Yes|Disk-skrivna byte|Byte|Totalt|Byte som skrivits till disk under övervaknings perioden|VMName|
|Disk skrivnings åtgärder/SEK|Yes|Disk skrivnings åtgärder/SEK|CountPerSecond|Genomsnitt|Skriv IOPS för disk|VMName|
|Inkommande flöden|Yes|Inkommande flöden|Antal|Genomsnitt|Inkommande flöden är antalet aktuella flöden i den inkommande riktningen (trafik som går till den virtuella datorn)|VMName|
|Högsta skapande frekvens för inkommande flöden|Yes|Högsta skapande frekvens för inkommande flöden|CountPerSecond|Genomsnitt|Högsta skapande frekvens för inkommande flöden (trafik som går till den virtuella datorn)|VMName|
|Nätverk – inkommande|Yes|Nätverk i fakturerbart (inaktuellt)|Byte|Totalt|Antalet fakturerbara byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik) (inaktuell)|VMName|
|Totalt nätverk|Yes|Totalt nätverk|Byte|Totalt|Antalet byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik)|VMName|
|Nätverk – utgående|Yes|Fakturerbart för nätverk (inaktuellt)|Byte|Totalt|Antalet fakturerbara byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik) (inaktuell)|VMName|
|Totalt nätverk|Yes|Totalt nätverk|Byte|Totalt|Antalet byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik)|VMName|
|Förbrukade procent av OS-diskens bandbredd|Yes|Förbrukade procent av OS-diskens bandbredd|Procent|Genomsnitt|Procent andel förbrukad bandbredd för operativ system disk per minut|LUN, VMName|
|Förbrukade procent av OS-diskens IOPS|Yes|Förbrukade procent av OS-diskens IOPS|Procent|Genomsnitt|Procent andel operativ system disk I/o-förbrukat per minut|LUN, VMName|
|Högsta bandbredd för OS-disk|Yes|Högsta bandbredd för OS-disk|Antal|Genomsnitt|Maximalt antal byte per sekund som data flödes operativ system disk kan uppnå med burst-överföring|LUN, VMName|
|Högsta burst-IOPS för OS-disk|Yes|Högsta burst-IOPS för OS-disk|Antal|Genomsnitt|Maximal IOPS OS-disk kan uppnås med bursting|LUN, VMName|
|Ködjup för OS-disk|Yes|Ködjup för OS-disk|Antal|Genomsnitt|Ködjup i operativ systemets disk (eller Kölängd)|VMName|
|Lästa byte för OS-disk/SEK|Yes|Lästa byte för OS-disk/SEK|BytesPerSecond|Genomsnitt|Byte/s lästa från en enskild disk under övervaknings perioden för operativ system disken|VMName|
|Läs åtgärder för operativ system disk/SEK|Yes|Läs åtgärder för operativ system disk/SEK|CountPerSecond|Genomsnitt|Läsa IOPS från en enskild disk under övervaknings perioden för operativ system disken|VMName|
|Bandbredd för OS-diskens mål|Yes|Bandbredd för OS-diskens mål|Antal|Genomsnitt|Bas linje byte per sekund data flödes operativ system disk kan uppnås utan burst-överföring|LUN, VMName|
|OS-disk mål IOPS|Yes|OS-disk mål IOPS|Antal|Genomsnitt|Bas linje för IOPS OS-disk kan uppnås utan burst-överföring|LUN, VMName|
|Procent andel förbrukade OS-diskar med burst BPS|Yes|Procent andel förbrukade OS-diskar med burst BPS|Procent|Genomsnitt|Procent andel av OS-diskens burst-bandbredds krediter som använts hittills|LUN, VMName|
|Procent andel förbrukade IO-krediter i OS-disk|Yes|Procent andel förbrukade IO-krediter i OS-disk|Procent|Genomsnitt|Procent andel av OS-diskens burst-krediter som använts hittills|LUN, VMName|
|Skrivna byte per operativ system disk/SEK|Yes|Skrivna byte per operativ system disk/SEK|BytesPerSecond|Genomsnitt|Byte/s skrivs till en enskild disk under övervaknings perioden för operativ system disken|VMName|
|Skriv åtgärder för operativ system disk/SEK|Yes|Skriv åtgärder för operativ system disk/SEK|CountPerSecond|Genomsnitt|Skriv IOPS från en enskild disk under övervaknings perioden för operativ system disken|VMName|
|Utgående flöden|Yes|Utgående flöden|Antal|Genomsnitt|Utgående flöden är antalet aktuella flöden i den utgående riktningen (trafik som går ut från den virtuella datorn)|VMName|
|Längsta skapande frekvens för utgående flöden|Yes|Längsta skapande frekvens för utgående flöden|CountPerSecond|Genomsnitt|Den maximala skapande frekvensen för utgående flöden (trafik som går ut från den virtuella datorn)|VMName|
|Processorprocentandel|Yes|Processorprocentandel|Procent|Genomsnitt|Procent andelen allokerade beräknings enheter som för närvarande används av den eller de virtuella datorerna|VMName|
|Läsning av Premium data disk-cache|Yes|Läsning av Premium data disk-cache|Procent|Genomsnitt|Läsning av Premium data disk-cache|LUN, VMName|
|Read missar i Premium data-diskcachen|Yes|Read missar i Premium data-diskcachen|Procent|Genomsnitt|Read missar i Premium data-diskcachen|LUN, VMName|
|Läsning av Premium OS-diskcachen|Yes|Läsning av Premium OS-diskcachen|Procent|Genomsnitt|Läsning av Premium OS-diskcachen|VMName|
|Read missar i Premium OS-diskcachen|Yes|Read missar i Premium OS-diskcachen|Procent|Genomsnitt|Read missar i Premium OS-diskcachen|VMName|
|Cachelagrad bandbredd i procent för VM|Yes|Cachelagrad bandbredd i procent för VM|Procent|Genomsnitt|Procent andel cachelagrad disk bandbredd som används av den virtuella datorn|VMName|
|VM cachelagrad IOPS förbrukad procent|Yes|VM cachelagrad IOPS förbrukad procent|Procent|Genomsnitt|Procent andel cachelagrad disk-IOPS som används av den virtuella datorn|VMName|
|Förbrukad procent andel förbrukade bandbredd i VM|Yes|Förbrukad procent andel förbrukade bandbredd i VM|Procent|Genomsnitt|Procent andel ej cachelagrad disk bandbredd som används av den virtuella datorn|VMName|
|VM, ej cachelagrad IOPS, procent andel|Yes|VM, ej cachelagrad IOPS, procent andel|Procent|Genomsnitt|Procent andel ej cachelagrat disk-IOPS som används av den virtuella datorn|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft. Compute/virtualMachineScaleSets/virtualMachines

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Förbrukade CPU-krediter|Yes|Förbrukade CPU-krediter|Antal|Genomsnitt|Totalt antal krediter som konsumeras av den virtuella datorn|Inga dimensioner|
|Återstående CPU-krediter|Yes|Återstående CPU-krediter|Antal|Genomsnitt|Totalt antal krediter som är tillgängliga för burst|Inga dimensioner|
|Förbrukad mängd bandbredd för data disk|Yes|Förbrukad mängd bandbredd för data disk|Procent|Genomsnitt|Procent andel data diskens förbrukade bandbredd per minut|ENHETEN|
|Förbrukad rabatt i data disk IOPS|Yes|Förbrukad rabatt i data disk IOPS|Procent|Genomsnitt|Procent andel data disk-I/o-förbrukat per minut|ENHETEN|
|Data disk Max burst-bandbredd|Yes|Data disk Max burst-bandbredd|Antal|Genomsnitt|Maximalt antal byte per sekund data disk data disk kan uppnås med bursting|ENHETEN|
|Högsta burst-IOPS för data disk|Yes|Högsta burst-IOPS för data disk|Antal|Genomsnitt|Högsta IOPS-datadisk kan uppnås med bursting|ENHETEN|
|Ködjup för datadisk|Yes|Ködjup för datadisk|Antal|Genomsnitt|Data diskens ködjup (eller Kölängd)|ENHETEN|
|Lästa byte på datadisk/SEK|Yes|Lästa byte på datadisk/SEK|BytesPerSecond|Genomsnitt|Byte/s lästa från en enskild disk under övervaknings perioden|ENHETEN|
|Läs åtgärder för data disk/SEK|Yes|Läs åtgärder för data disk/SEK|CountPerSecond|Genomsnitt|Läsa IOPS från en enskild disk under övervaknings perioden|ENHETEN|
|Bandbredd för data disk mål|Yes|Bandbredd för data disk mål|Antal|Genomsnitt|Bas linje byte per sekund data disk kan uppnås utan burst-överföring|ENHETEN|
|Data disk mål IOPS|Yes|Data disk mål IOPS|Antal|Genomsnitt|Bas linje för IOPS-datadisk kan uppnås utan burst-överföring|ENHETEN|
|Procent andel data disk Använd burst BPS-kredit|Yes|Procent andel data disk Använd burst BPS-kredit|Procent|Genomsnitt|Procent andel av data diskens bandbredds krediter som använts hittills|ENHETEN|
|Procent andel förbrukade IO-krediter i data disk|Yes|Procent andel förbrukade IO-krediter i data disk|Procent|Genomsnitt|Procent andel data disk burst I/O-krediter som använts hittills|ENHETEN|
|Skrivna byte på datadisk/SEK|Yes|Skrivna byte på datadisk/SEK|BytesPerSecond|Genomsnitt|Byte/s skrivs till en enskild disk under övervaknings perioden|ENHETEN|
|Skriv åtgärder för data disk/SEK|Yes|Skriv åtgärder för data disk/SEK|CountPerSecond|Genomsnitt|Skriv IOPS från en enskild disk under övervaknings perioden|ENHETEN|
|Lästa byte på disk|Yes|Lästa byte på disk|Byte|Totalt|Byte som lästs från disk under övervaknings perioden|Inga dimensioner|
|Disk Läs åtgärder/SEK|Yes|Disk Läs åtgärder/SEK|CountPerSecond|Genomsnitt|Disk-Läs-IOPS|Inga dimensioner|
|Disk-skrivna byte|Yes|Disk-skrivna byte|Byte|Totalt|Byte som skrivits till disk under övervaknings perioden|Inga dimensioner|
|Disk skrivnings åtgärder/SEK|Yes|Disk skrivnings åtgärder/SEK|CountPerSecond|Genomsnitt|Skriv IOPS för disk|Inga dimensioner|
|Inkommande flöden|Yes|Inkommande flöden|Antal|Genomsnitt|Inkommande flöden är antalet aktuella flöden i den inkommande riktningen (trafik som går till den virtuella datorn)|Inga dimensioner|
|Högsta skapande frekvens för inkommande flöden|Yes|Högsta skapande frekvens för inkommande flöden|CountPerSecond|Genomsnitt|Högsta skapande frekvens för inkommande flöden (trafik som går till den virtuella datorn)|Inga dimensioner|
|Nätverk – inkommande|Yes|Nätverk i fakturerbart (inaktuellt)|Byte|Totalt|Antalet fakturerbara byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik) (inaktuell)|Inga dimensioner|
|Totalt nätverk|Yes|Totalt nätverk|Byte|Totalt|Antalet byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik)|Inga dimensioner|
|Nätverk – utgående|Yes|Fakturerbart för nätverk (inaktuellt)|Byte|Totalt|Antalet fakturerbara byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik) (inaktuell)|Inga dimensioner|
|Totalt nätverk|Yes|Totalt nätverk|Byte|Totalt|Antalet byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik)|Inga dimensioner|
|Förbrukade procent av OS-diskens bandbredd|Yes|Förbrukade procent av OS-diskens bandbredd|Procent|Genomsnitt|Procent andel förbrukad bandbredd för operativ system disk per minut|ENHETEN|
|Förbrukade procent av OS-diskens IOPS|Yes|Förbrukade procent av OS-diskens IOPS|Procent|Genomsnitt|Procent andel operativ system disk I/o-förbrukat per minut|ENHETEN|
|Högsta bandbredd för OS-disk|Yes|Högsta bandbredd för OS-disk|Antal|Genomsnitt|Maximalt antal byte per sekund som data flödes operativ system disk kan uppnå med burst-överföring|ENHETEN|
|Högsta burst-IOPS för OS-disk|Yes|Högsta burst-IOPS för OS-disk|Antal|Genomsnitt|Maximal IOPS OS-disk kan uppnås med bursting|ENHETEN|
|Ködjup för OS-disk|Yes|Ködjup för OS-disk|Antal|Genomsnitt|Ködjup i operativ systemets disk (eller Kölängd)|Inga dimensioner|
|Lästa byte för OS-disk/SEK|Yes|Lästa byte för OS-disk/SEK|BytesPerSecond|Genomsnitt|Byte/s lästa från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Läs åtgärder för operativ system disk/SEK|Yes|Läs åtgärder för operativ system disk/SEK|CountPerSecond|Genomsnitt|Läsa IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Bandbredd för OS-diskens mål|Yes|Bandbredd för OS-diskens mål|Antal|Genomsnitt|Bas linje byte per sekund data flödes operativ system disk kan uppnås utan burst-överföring|ENHETEN|
|OS-disk mål IOPS|Yes|OS-disk mål IOPS|Antal|Genomsnitt|Bas linje för IOPS OS-disk kan uppnås utan burst-överföring|ENHETEN|
|Procent andel förbrukade OS-diskar med burst BPS|Yes|Procent andel förbrukade OS-diskar med burst BPS|Procent|Genomsnitt|Procent andel av OS-diskens burst-bandbredds krediter som använts hittills|ENHETEN|
|Procent andel förbrukade IO-krediter i OS-disk|Yes|Procent andel förbrukade IO-krediter i OS-disk|Procent|Genomsnitt|Procent andel av OS-diskens burst-krediter som använts hittills|ENHETEN|
|Skrivna byte per operativ system disk/SEK|Yes|Skrivna byte per operativ system disk/SEK|BytesPerSecond|Genomsnitt|Byte/s skrivs till en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Skriv åtgärder för operativ system disk/SEK|Yes|Skriv åtgärder för operativ system disk/SEK|CountPerSecond|Genomsnitt|Skriv IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Utgående flöden|Yes|Utgående flöden|Antal|Genomsnitt|Utgående flöden är antalet aktuella flöden i den utgående riktningen (trafik som går ut från den virtuella datorn)|Inga dimensioner|
|Längsta skapande frekvens för utgående flöden|Yes|Längsta skapande frekvens för utgående flöden|CountPerSecond|Genomsnitt|Den maximala skapande frekvensen för utgående flöden (trafik som går ut från den virtuella datorn)|Inga dimensioner|
|Processorprocentandel|Yes|Processorprocentandel|Procent|Genomsnitt|Procent andelen allokerade beräknings enheter som för närvarande används av den eller de virtuella datorerna|Inga dimensioner|
|Läsning av Premium data disk-cache|Yes|Läsning av Premium data disk-cache|Procent|Genomsnitt|Läsning av Premium data disk-cache|ENHETEN|
|Read missar i Premium data-diskcachen|Yes|Read missar i Premium data-diskcachen|Procent|Genomsnitt|Read missar i Premium data-diskcachen|ENHETEN|
|Läsning av Premium OS-diskcachen|Yes|Läsning av Premium OS-diskcachen|Procent|Genomsnitt|Läsning av Premium OS-diskcachen|Inga dimensioner|
|Read missar i Premium OS-diskcachen|Yes|Read missar i Premium OS-diskcachen|Procent|Genomsnitt|Read missar i Premium OS-diskcachen|Inga dimensioner|
|Cachelagrad bandbredd i procent för VM|Yes|Cachelagrad bandbredd i procent för VM|Procent|Genomsnitt|Procent andel cachelagrad disk bandbredd som används av den virtuella datorn|Inga dimensioner|
|VM cachelagrad IOPS förbrukad procent|Yes|VM cachelagrad IOPS förbrukad procent|Procent|Genomsnitt|Procent andel cachelagrad disk-IOPS som används av den virtuella datorn|Inga dimensioner|
|Förbrukad procent andel förbrukade bandbredd i VM|Yes|Förbrukad procent andel förbrukade bandbredd i VM|Procent|Genomsnitt|Procent andel ej cachelagrad disk bandbredd som används av den virtuella datorn|Inga dimensioner|
|VM, ej cachelagrad IOPS, procent andel|Yes|VM, ej cachelagrad IOPS, procent andel|Procent|Genomsnitt|Procent andel ej cachelagrat disk-IOPS som används av den virtuella datorn|Inga dimensioner|


## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft. ContainerInstance/containerGroups

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|CpuUsage|Yes|Processoranvändning|Antal|Genomsnitt|CPU-användning på alla kärnor i millicores.|containerName|
|MemoryUsage|Yes|Minnesanvändning|Byte|Genomsnitt|Total minnes användning i byte.|containerName|
|NetworkBytesReceivedPerSecond|Yes|Mottagna nätverks byte per sekund|Byte|Genomsnitt|Mottagna nätverks byte per sekund.|Inga dimensioner|
|NetworkBytesTransmittedPerSecond|Yes|Överförda nätverks byte per sekund|Byte|Genomsnitt|Överförda nätverks byte per sekund.|Inga dimensioner|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft. ContainerRegistry/register

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|AgentPoolCPUTime|Yes|Agentpoolegenskap CPU-tid|Sekunder|Totalt|Agentpoolegenskap CPU-tid i sekunder|Inga dimensioner|
|RunDuration|Yes|Körnings tid|Millisekunder|Totalt|Körnings tid i millisekunder|Inga dimensioner|
|SuccessfulPullCount|Yes|Antal lyckade pull-överföringar|Antal|Genomsnitt|Antal lyckade bild hämtningar|Inga dimensioner|
|SuccessfulPushCount|Yes|Antal lyckade push-meddelanden|Antal|Genomsnitt|Antal lyckade avbildnings push-meddelanden|Inga dimensioner|
|TotalPullCount|Yes|Totalt antal hämtningar|Antal|Genomsnitt|Totalt antal bild hämtningar|Inga dimensioner|
|TotalPushCount|Yes|Totalt antal push-meddelanden|Antal|Genomsnitt|Totalt antal push-meddelanden för avbildningar|Inga dimensioner|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft. container service/managedClusters

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|apiserver_current_inflight_requests|No|Synlighetssekvensnummer-begäranden|Antal|Genomsnitt|Maximalt antal synlighetssekvensnummer-begäranden som används på apiserver per begär ande typ under den senaste sekunden|requestKind|
|kube_node_status_allocatable_cpu_cores|No|Totalt antal tillgängliga processor kärnor i ett hanterat kluster|Antal|Genomsnitt|Totalt antal tillgängliga processor kärnor i ett hanterat kluster||
|kube_node_status_allocatable_memory_bytes|No|Total mängd tillgängligt minne i ett hanterat kluster|Byte|Genomsnitt|Total mängd tillgängligt minne i ett hanterat kluster||
|kube_node_status_condition|No|Status för olika nod villkor|Antal|Genomsnitt|Status för olika nod villkor|villkor, status, status2, nod|
|kube_pod_status_phase|No|Antal poddar per fas|Antal|Genomsnitt|Antal poddar per fas|fas, namnrymd, Pod|
|kube_pod_status_ready|No|Antal poddar i klart läge|Antal|Genomsnitt|Antal poddar i klart läge|namnrymd, pod, villkor|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|FailedRequests|Yes|Misslyckade begäranden|Antal|Totalt|Hämtar de tillgängliga loggarna för anpassade resurs leverantörer|HttpMethod, CallPath, StatusCode|
|SuccessfullRequests|Yes|Slutförda förfrågningar|Antal|Totalt|Lyckade förfrågningar gjorda av den anpassade providern|HttpMethod, CallPath, StatusCode|


## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft. DataBoxEdge/dataBoxEdgeDevices

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Availablecapacity;)|Yes|Tillgänglig kapacitet|Byte|Genomsnitt|Tillgänglig kapacitet i byte under rapporterings perioden.|Inga dimensioner|
|BytesUploadedToCloud|Yes|Överförda moln byte (enhet)|Byte|Genomsnitt|Det totala antalet byte som överförs till Azure från en enhet under rapporterings perioden.|Inga dimensioner|
|BytesUploadedToCloudPerShare|Yes|Överförda moln byte (resurs)|Byte|Genomsnitt|Det totala antalet byte som överförs till Azure från en resurs under rapporterings perioden.|Dela|
|CloudReadThroughput|Yes|Data flöde för moln hämtning|BytesPerSecond|Genomsnitt|Molnet Ladda ned data flödet till Azure under rapporterings perioden.|Inga dimensioner|
|CloudReadThroughputPerShare|Yes|Data flöde för moln hämtning (resurs)|BytesPerSecond|Genomsnitt|Ladda ned data flödet till Azure från en resurs under rapporterings perioden.|Dela|
|CloudUploadThroughput|Yes|Moln överförings data flöde|BytesPerSecond|Genomsnitt|Moln överförings data flödet till Azure under rapporterings perioden.|Inga dimensioner|
|CloudUploadThroughputPerShare|Yes|Moln överförings data flöde (resurs)|BytesPerSecond|Genomsnitt|Ladda upp data flödet till Azure från en resurs under rapporterings perioden.|Dela|
|HyperVMemoryUtilization|Yes|Edge Compute-minnes användning|Procent|Genomsnitt|Mängden RAM-minne som används|InstanceName|
|HyperVVirtualProcessorUtilization|Yes|Edge Compute-procent CPU|Procent|Genomsnitt|CPU-användning i procent|InstanceName|
|NICReadThroughput|Yes|Läs data flöde (nätverk)|BytesPerSecond|Genomsnitt|Läs data flödet för nätverks gränssnittet på enheten i rapporterings perioden för alla volymer i gatewayen.|InstanceName|
|NICWriteThroughput|Yes|Skriv data flöde (nätverk)|BytesPerSecond|Genomsnitt|Skriv data flödet för nätverks gränssnittet på enheten i rapporterings perioden för alla volymer i gatewayen.|InstanceName|
|Enhet|Yes|Total kapacitet|Byte|Genomsnitt|Total kapacitet|Inga dimensioner|


## <a name="microsoftdatacollaborationworkspaces"></a>Microsoft. DataCollaboration/arbets ytor

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|DataAssetCount|Yes|Data till gångar har skapats|Antal|Maximal|Antal skapade data till gångar|DataAssetName|
|PipelineCount|Yes|Skapade pipelines|Antal|Maximal|Antal skapade pipelines|PipelineName|
|ProposalCount|Yes|Skapade förslag|Antal|Maximal|Antal skapade förslag|ProposalName|
|ScriptCount|Yes|Skapade skript|Antal|Maximal|Antal skapade skript|ScriptName|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft. DataFactory/datafactories

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|FailedRuns|Yes|Misslyckade körningar|Antal|Totalt||pipelineName, activityName|
|SuccessfulRuns|Yes|Lyckade körningar|Antal|Totalt||pipelineName, activityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/fabriker

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ActivityCancelledRuns|Yes|Avbrutna aktiviteter kör mått|Antal|Totalt||ActivityType, PipelineName, FailureType, Name|
|ActivityFailedRuns|Yes|Misslyckad aktivitet kör mått|Antal|Totalt||ActivityType, PipelineName, FailureType, Name|
|ActivitySucceededRuns|Yes|Genomförd aktivitet kör mått|Antal|Totalt||ActivityType, PipelineName, FailureType, Name|
|FactorySizeInGbUnits|Yes|Total fabriks storlek (GB enhet)|Antal|Maximal||Inga dimensioner|
|IntegrationRuntimeAvailableMemory|Yes|Tillgängligt minne för integration runtime|Byte|Genomsnitt||IntegrationRuntimeName, nodnamn|
|IntegrationRuntimeAvailableNodeNumber|Yes|Antal tillgängliga noder för integration runtime|Antal|Genomsnitt||IntegrationRuntimeName|
|IntegrationRuntimeAverageTaskPickupDelay|Yes|Varaktighet för integration runtime-kö|Sekunder|Genomsnitt||IntegrationRuntimeName|
|IntegrationRuntimeCpuPercentage|Yes|PROCESSOR användning för integration runtime|Procent|Genomsnitt||IntegrationRuntimeName, nodnamn|
|IntegrationRuntimeQueueLength|Yes|Kölängd för integration runtime|Antal|Genomsnitt||IntegrationRuntimeName|
|MaxAllowedFactorySizeInGbUnits|Yes|Högsta tillåtna fabriks storlek (GB enhet)|Antal|Maximal||Inga dimensioner|
|MaxAllowedResourceCount|Yes|Maximalt antal tillåtna entiteter|Antal|Maximal||Inga dimensioner|
|PipelineCancelledRuns|Yes|Avbruten pipeline kör mått|Antal|Totalt||FailureType, namn|
|PipelineElapsedTimeRuns|Yes|Pipeline för förfluten tid kör mått|Antal|Totalt||RunId, namn|
|PipelineFailedRuns|Yes|Misslyckad pipeline kör mått|Antal|Totalt||FailureType, namn|
|PipelineSucceededRuns|Yes|Slutförd pipeline kör mått|Antal|Totalt||FailureType, namn|
|ResourceCount|Yes|Totalt antal entiteter|Antal|Maximal||Inga dimensioner|
|SSISIntegrationRuntimeStartCancel|Yes|Start måtten för integrerings körningen av SSIS har avbrutits|Antal|Totalt||IntegrationRuntimeName|
|SSISIntegrationRuntimeStartFailed|Yes|Det gick inte att starta mått för integration runtime-SSIS|Antal|Totalt||IntegrationRuntimeName|
|SSISIntegrationRuntimeStartSucceeded|Yes|Slutförda start mått för integration runtime-SSIS|Antal|Totalt||IntegrationRuntimeName|
|SSISIntegrationRuntimeStopStuck|Yes|Stopp mått för SSIS-integration runtime|Antal|Totalt||IntegrationRuntimeName|
|SSISIntegrationRuntimeStopSucceeded|Yes|Slutförde SSIS för integration runtime|Antal|Totalt||IntegrationRuntimeName|
|SSISPackageExecutionCancel|Yes|Mät värden för körning av SSIS-paket|Antal|Totalt||IntegrationRuntimeName|
|SSISPackageExecutionFailed|Yes|Det gick inte att köra Mät värden för SSIS-paket|Antal|Totalt||IntegrationRuntimeName|
|SSISPackageExecutionSucceeded|Yes|SSIS-paketets körnings mått har slutförts|Antal|Totalt||IntegrationRuntimeName|
|TriggerCancelledRuns|Yes|Avbrutna utlösare kör mått|Antal|Totalt||Namn, FailureType|
|TriggerFailedRuns|Yes|Misslyckad utlösare kör mått|Antal|Totalt||Namn, FailureType|
|TriggerSucceededRuns|Yes|Lyckade utlösare kör mått|Antal|Totalt||Namn, FailureType|


## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft. DataLakeAnalytics/konton

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|JobAUEndedCancelled|Yes|Tid för avbruten AU|Sekunder|Totalt|Total AU-tid för avbrutna jobb.|Inga dimensioner|
|JobAUEndedFailure|Yes|Misslyckad AU-tid|Sekunder|Totalt|Total AU-tid för misslyckade jobb.|Inga dimensioner|
|JobAUEndedSuccess|Yes|Lyckad AU-tid|Sekunder|Totalt|Total AU-tid för lyckade jobb.|Inga dimensioner|
|JobEndedCancelled|Yes|Avbrutna jobb|Antal|Totalt|Antal avbrutna jobb.|Inga dimensioner|
|JobEndedFailure|Yes|Misslyckade jobb|Antal|Totalt|Antal misslyckade jobb.|Inga dimensioner|
|JobEndedSuccess|Yes|Slutförda jobb|Antal|Totalt|Antal lyckade jobb.|Inga dimensioner|
|JobStage|Yes|Jobb i steg|Antal|Totalt|Antal jobb i varje steg.|Inga dimensioner|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft. DataLakeStore/konton

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|DataRead|Yes|Lästa data|Byte|Totalt|Total mängd data som lästs från kontot.|Inga dimensioner|
|DataWritten|Yes|Skrivna data|Byte|Totalt|Total mängd data som skrivs till kontot.|Inga dimensioner|
|ReadRequests|Yes|Läs begär Anden|Antal|Totalt|Antal data läsnings begär anden till kontot.|Inga dimensioner|
|TotalStorage|Yes|Totalt lagringsutrymme|Byte|Maximal|Den totala mängden data som lagras i kontot.|Inga dimensioner|
|WriteRequests|Yes|Skriv förfrågningar|Antal|Totalt|Antal data skrivnings begär anden till kontot.|Inga dimensioner|


## <a name="microsoftdatashareaccounts"></a>Microsoft. DataShare/konton

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|FailedShareSubscriptionSynchronizations|Yes|Mottagna ögonblicks bilder av resurs misslyckades|Antal|Antal|Antal mottagna resurs misslyckade ögonblicks bilder i kontot|Inga dimensioner|
|FailedShareSynchronizations|Yes|Misslyckade ögonblicks bilder av skickade resurser|Antal|Antal|Antal skickade resurs misslyckade ögonblicks bilder i kontot|Inga dimensioner|
|ShareCount|Yes|Skickade resurser|Antal|Maximal|Antal skickade resurser i kontot|Resurs|
|ShareSubscriptionCount|Yes|Mottagna resurser|Antal|Maximal|Antal mottagna resurser i kontot|ShareSubscriptionName|
|SucceededShareSubscriptionSynchronizations|Yes|Mottagna delade ögonblicks bilder|Antal|Antal|Antal mottagna delade ögonblicks bilder i kontot|Inga dimensioner|
|SucceededShareSynchronizations|Yes|Slutförda ögonblicks bilder av skickade resurser|Antal|Antal|Antal skickade delade resurser i kontot|Inga dimensioner|


## <a name="microsoftdbformariadbservers"></a>Microsoft. DBforMariaDB/servers

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|active_connections|Yes|Aktiva anslutningar|Antal|Genomsnitt|Aktiva anslutningar|Inga dimensioner|
|backup_storage_used|Yes|Lagring av säkerhets kopior som används|Byte|Genomsnitt|Lagring av säkerhets kopior som används|Inga dimensioner|
|connections_failed|Yes|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inga dimensioner|
|cpu_percent|Yes|CPU-procent|Procent|Genomsnitt|CPU-procent|Inga dimensioner|
|io_consumption_percent|Yes|I/o procent|Procent|Genomsnitt|I/o procent|Inga dimensioner|
|memory_percent|Yes|Minnes procent|Procent|Genomsnitt|Minnes procent|Inga dimensioner|
|network_bytes_egress|Yes|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inga dimensioner|
|network_bytes_ingress|Yes|Nätverk – inkommande|Byte|Totalt|Nätverk i över aktiva anslutningar|Inga dimensioner|
|seconds_behind_master|Yes|Fördröjning för replikering på några sekunder|Antal|Maximal|Fördröjning för replikering på några sekunder|Inga dimensioner|
|serverlog_storage_limit|Yes|Server logg lagrings gräns|Byte|Maximal|Server logg lagrings gräns|Inga dimensioner|
|serverlog_storage_percent|Yes|Server logg lagrings procent|Procent|Genomsnitt|Server logg lagrings procent|Inga dimensioner|
|serverlog_storage_usage|Yes|Server logg lagring används|Byte|Genomsnitt|Server logg lagring används|Inga dimensioner|
|storage_limit|Yes|Lagrings gräns|Byte|Maximal|Lagrings gräns|Inga dimensioner|
|storage_percent|Yes|Lagrings procent|Procent|Genomsnitt|Lagrings procent|Inga dimensioner|
|storage_used|Yes|Använt lagrings utrymme|Byte|Genomsnitt|Använt lagrings utrymme|Inga dimensioner|


## <a name="microsoftdbformysqlflexibleservers"></a>Microsoft. DBforMySQL/flexibleServers

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|aborted_connections|Yes|Avbrutna anslutningar|Antal|Totalt|Avbrutna anslutningar|Inga dimensioner|
|active_connections|Yes|Aktiva anslutningar|Antal|Maximal|Aktiva anslutningar|Inga dimensioner|
|backup_storage_used|Yes|Lagring av säkerhets kopior som används|Byte|Maximal|Lagring av säkerhets kopior som används|Inga dimensioner|
|cpu_percent|Yes|Värd processor procent|Procent|Maximal|Värd processor procent|Inga dimensioner|
|io_consumption_percent|Yes|I/o procent|Procent|Maximal|I/o procent|Inga dimensioner|
|memory_percent|Yes|Värdens minnes procent|Procent|Maximal|Värdens minnes procent|Inga dimensioner|
|network_bytes_egress|Yes|Utgående värd nätverk|Byte|Totalt|Utgående värd nätverk i byte|Inga dimensioner|
|network_bytes_ingress|Yes|Värd nätverk i|Byte|Totalt|Ingångs värden för värd nätverk i byte|Inga dimensioner|
|Frågor|Yes|Frågor|Antal|Totalt|Frågor|Inga dimensioner|
|replication_lag|Yes|Fördröjning för replikering på några sekunder|Sekunder|Maximal|Fördröjning för replikering på några sekunder|Inga dimensioner|
|storage_limit|Yes|Lagrings gräns|Byte|Maximal|Lagrings gräns|Inga dimensioner|
|storage_percent|Yes|Lagrings procent|Procent|Maximal|Lagrings procent|Inga dimensioner|
|storage_used|Yes|Använt lagrings utrymme|Byte|Maximal|Använt lagrings utrymme|Inga dimensioner|
|total_connections|Yes|Totalt antal anslutningar|Antal|Totalt|Totalt antal anslutningar|Inga dimensioner|


## <a name="microsoftdbformysqlservers"></a>Microsoft. DBforMySQL/servers

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|active_connections|Yes|Aktiva anslutningar|Antal|Genomsnitt|Aktiva anslutningar|Inga dimensioner|
|backup_storage_used|Yes|Lagring av säkerhets kopior som används|Byte|Genomsnitt|Lagring av säkerhets kopior som används|Inga dimensioner|
|connections_failed|Yes|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inga dimensioner|
|cpu_percent|Yes|CPU-procent|Procent|Genomsnitt|CPU-procent|Inga dimensioner|
|io_consumption_percent|Yes|I/o procent|Procent|Genomsnitt|I/o procent|Inga dimensioner|
|memory_percent|Yes|Minnes procent|Procent|Genomsnitt|Minnes procent|Inga dimensioner|
|network_bytes_egress|Yes|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inga dimensioner|
|network_bytes_ingress|Yes|Nätverk – inkommande|Byte|Totalt|Nätverk i över aktiva anslutningar|Inga dimensioner|
|seconds_behind_master|Yes|Fördröjning för replikering på några sekunder|Antal|Maximal|Fördröjning för replikering på några sekunder|Inga dimensioner|
|serverlog_storage_limit|Yes|Server logg lagrings gräns|Byte|Maximal|Server logg lagrings gräns|Inga dimensioner|
|serverlog_storage_percent|Yes|Server logg lagrings procent|Procent|Genomsnitt|Server logg lagrings procent|Inga dimensioner|
|serverlog_storage_usage|Yes|Server logg lagring används|Byte|Genomsnitt|Server logg lagring används|Inga dimensioner|
|storage_limit|Yes|Lagrings gräns|Byte|Maximal|Lagrings gräns|Inga dimensioner|
|storage_percent|Yes|Lagrings procent|Procent|Genomsnitt|Lagrings procent|Inga dimensioner|
|storage_used|Yes|Använt lagrings utrymme|Byte|Genomsnitt|Använt lagrings utrymme|Inga dimensioner|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft. DBforPostgreSQL/flexibleServers

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|active_connections|Yes|Aktiva anslutningar|Antal|Genomsnitt|Aktiva anslutningar|Inga dimensioner|
|backup_storage_used|Yes|Lagring av säkerhets kopior som används|Byte|Genomsnitt|Lagring av säkerhets kopior som används|Inga dimensioner|
|connections_failed|Yes|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inga dimensioner|
|connections_succeeded|Yes|Lyckade anslutningar|Antal|Totalt|Lyckade anslutningar|Inga dimensioner|
|cpu_credits_consumed|Yes|Förbrukade CPU-krediter|Antal|Genomsnitt|Totalt antal krediter som förbrukas av databas servern|Inga dimensioner|
|cpu_credits_remaining|Yes|Återstående CPU-krediter|Antal|Genomsnitt|Totalt antal krediter som är tillgängliga för burst|Inga dimensioner|
|cpu_percent|Yes|CPU-procent|Procent|Genomsnitt|CPU-procent|Inga dimensioner|
|disk_queue_depth|Yes|Disk-ködjup|Antal|Genomsnitt|Antal utestående I/O-åtgärder till data disken|Inga dimensioner|
|IOPS|Yes|IOPS|Antal|Genomsnitt|I/o-åtgärder per sekund|Inga dimensioner|
|maximum_used_transactionIDs|Yes|Högsta antal använda transaktions-ID: n|Antal|Genomsnitt|Högsta antal använda transaktions-ID: n|Inga dimensioner|
|memory_percent|Yes|Minnes procent|Procent|Genomsnitt|Minnes procent|Inga dimensioner|
|network_bytes_egress|Yes|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inga dimensioner|
|network_bytes_ingress|Yes|Nätverk – inkommande|Byte|Totalt|Nätverk i över aktiva anslutningar|Inga dimensioner|
|read_iops|Yes|Läs IOPS|Antal|Genomsnitt|Antal Läs åtgärder för data diskar I/O per sekund|Inga dimensioner|
|read_throughput|Yes|Lästa data flöde byte/s|Antal|Genomsnitt|Lästa byte per sekund från data disken under övervaknings perioden|Inga dimensioner|
|storage_free|Yes|Ledigt lagrings utrymme|Byte|Genomsnitt|Ledigt lagrings utrymme|Inga dimensioner|
|storage_percent|Yes|Lagrings procent|Procent|Genomsnitt|Lagrings procent|Inga dimensioner|
|storage_used|Yes|Använt lagrings utrymme|Byte|Genomsnitt|Använt lagrings utrymme|Inga dimensioner|
|txlogs_storage_used|Yes|Transaktions logg lagring används|Byte|Genomsnitt|Transaktions logg lagring används|Inga dimensioner|
|write_iops|Yes|Skriv IOPS|Antal|Genomsnitt|Antal Skriv åtgärder för data diskar I/O per sekund|Inga dimensioner|
|write_throughput|Yes|Skrivna byte/s i data flöde|Antal|Genomsnitt|Skrivna byte per sekund till data disken under övervaknings perioden|Inga dimensioner|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft. DBforPostgreSQL/servers

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|active_connections|Yes|Aktiva anslutningar|Antal|Genomsnitt|Aktiva anslutningar|Inga dimensioner|
|backup_storage_used|Yes|Lagring av säkerhets kopior som används|Byte|Genomsnitt|Lagring av säkerhets kopior som används|Inga dimensioner|
|connections_failed|Yes|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inga dimensioner|
|cpu_percent|Yes|CPU-procent|Procent|Genomsnitt|CPU-procent|Inga dimensioner|
|io_consumption_percent|Yes|I/o procent|Procent|Genomsnitt|I/o procent|Inga dimensioner|
|memory_percent|Yes|Minnes procent|Procent|Genomsnitt|Minnes procent|Inga dimensioner|
|network_bytes_egress|Yes|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inga dimensioner|
|network_bytes_ingress|Yes|Nätverk – inkommande|Byte|Totalt|Nätverk i över aktiva anslutningar|Inga dimensioner|
|pg_replica_log_delay_in_bytes|Yes|Maximal fördröjning mellan repliker|Byte|Maximal|Fördröjning i byte för den flesta isolerings repliken|Inga dimensioner|
|pg_replica_log_delay_in_seconds|Yes|Replik fördröjning|Sekunder|Maximal|Replik fördröjning i sekunder|Inga dimensioner|
|serverlog_storage_limit|Yes|Server logg lagrings gräns|Byte|Maximal|Server logg lagrings gräns|Inga dimensioner|
|serverlog_storage_percent|Yes|Server logg lagrings procent|Procent|Genomsnitt|Server logg lagrings procent|Inga dimensioner|
|serverlog_storage_usage|Yes|Server logg lagring används|Byte|Genomsnitt|Server logg lagring används|Inga dimensioner|
|storage_limit|Yes|Lagrings gräns|Byte|Maximal|Lagrings gräns|Inga dimensioner|
|storage_percent|Yes|Lagrings procent|Procent|Genomsnitt|Lagrings procent|Inga dimensioner|
|storage_used|Yes|Använt lagrings utrymme|Byte|Genomsnitt|Använt lagrings utrymme|Inga dimensioner|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft. DBforPostgreSQL/serversv2

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|active_connections|Yes|Aktiva anslutningar|Antal|Genomsnitt|Aktiva anslutningar|Inga dimensioner|
|cpu_percent|Yes|CPU-procent|Procent|Genomsnitt|CPU-procent|Inga dimensioner|
|IOPS|Yes|IOPS|Antal|Genomsnitt|I/o-åtgärder per sekund|Inga dimensioner|
|memory_percent|Yes|Minnes procent|Procent|Genomsnitt|Minnes procent|Inga dimensioner|
|network_bytes_egress|Yes|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inga dimensioner|
|network_bytes_ingress|Yes|Nätverk – inkommande|Byte|Totalt|Nätverk i över aktiva anslutningar|Inga dimensioner|
|storage_percent|Yes|Lagrings procent|Procent|Genomsnitt|Lagrings procent|Inga dimensioner|
|storage_used|Yes|Använt lagrings utrymme|Byte|Genomsnitt|Använt lagrings utrymme|Inga dimensioner|


## <a name="microsoftdeviceselasticpools"></a>Microsoft. Devices/ElasticPools

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|elasticPool.requestedUsageRate|Yes|begärd användnings frekvens|Procent|Genomsnitt|begärd användnings frekvens|Inga dimensioner|


## <a name="microsoftdeviceselasticpoolsiothubtenants"></a>Microsoft. Devices/ElasticPools/IotHubTenants

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|C2D. commands. utgående. Abandon. lyckades|Yes|Övergivna C2D-meddelanden|Antal|Totalt|Antal meddelanden från moln till enhet som har övergivits av enheten|Inga dimensioner|
|C2D. commands. utgående. Complete. lyckades|Yes|C2D meddelande leveranser har slutförts|Antal|Totalt|Antalet meddelande leveranser från moln till enhet har slutförts av enheten|Inga dimensioner|
|C2D. commands. rekasta. Success|Yes|Avvisade C2D-meddelanden|Antal|Totalt|Antal meddelanden från moln till enhet som avvisats av enheten|Inga dimensioner|
|C2D. Methods. Failure|Yes|Misslyckade direkta metod anrop|Antal|Totalt|Antalet misslyckade direkta metod anrop.|Inga dimensioner|
|C2D. Methods. requestSize|Yes|Begär ande storlek för direkta metod anrop|Byte|Genomsnitt|Genomsnitt, min och Max för alla lyckade direkta metod begär Anden.|Inga dimensioner|
|C2D. Methods. responseSize|Yes|Svars storlek för direkta metod anrop|Byte|Genomsnitt|Medelvärde, min och Max för alla lyckade direkta metod svar.|Inga dimensioner|
|C2D. Methods. Success|Yes|Direkta metod anrop|Antal|Totalt|Antalet lyckade direkta metod anrop.|Inga dimensioner|
|C2D. delad.|Yes|Det gick inte att dubbla läsningar från Server delen|Antal|Totalt|Antalet dubbla läsningar som initierats av alla misslyckade backend-slutpunkter.|Inga dimensioner|
|C2D., delad. storlek|Yes|Svars storlek för dubbla läsningar från Server delen|Byte|Genomsnitt|Genomsnitt, min och Max för alla lyckade, dubbla läsningar som initieras i slut punkt.|Inga dimensioner|
|C2D. delad. lyckades|Yes|Lyckades dubbla läspaket från Server delen|Antal|Totalt|Antalet dubbla läsningar som initierats av alla lyckade.|Inga dimensioner|
|C2D. delad. Update. Failure|Yes|Misslyckade dubbla uppdateringar från Server delen|Antal|Totalt|Antalet dubbla uppdateringar som initierats av alla misslyckade backend-slutpunkter.|Inga dimensioner|
|C2D. dubbla. Update. size|Yes|Storlek på dubbla uppdateringar från Server delen|Byte|Genomsnitt|Genomsnitt, min och Max storlek för alla lyckade, dubbla uppdateringar som initieras.|Inga dimensioner|
|C2D. delad. Update. lyckades|Yes|Lyckades dubbla uppdateringar från Server delen|Antal|Totalt|Antalet fullständiga uppdateringar som initierats av alla lyckade backend-uppdateringar.|Inga dimensioner|
|C2DMessagesExpired|Yes|C2D meddelanden har upphört att gälla|Antal|Totalt|Antal utgångna meddelanden från moln till enhet|Inga dimensioner|
|konfigurationer|Yes|Konfigurations mått|Antal|Totalt|Mått för konfigurations åtgärder|Inga dimensioner|
|connectedDeviceCount|Yes|Anslutna enheter|Antal|Genomsnitt|Antal enheter som är anslutna till din IoT-hubb|Inga dimensioner|
|D2C. endpoints. utgående. Builtity. events|Yes|Routning: meddelanden som levereras till meddelanden/händelser|Antal|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till den inbyggda slut punkten (meddelanden/händelser).|Inga dimensioner|
|D2C. endpoints. utgående. eventHubs|Yes|Routning: meddelanden levererade till Händelsehubben|Antal|Totalt|Antal gånger IoT Hub routning har levererat meddelanden till Event Hub-slutpunkter.|Inga dimensioner|
|D2C. endpoints. utgående. serviceBusQueues|Yes|Routning: meddelanden levererade till Service Bus kö|Antal|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till Service Bus-köns slut punkter.|Inga dimensioner|
|D2C. endpoints. utgående. serviceBusTopics|Yes|Routning: meddelanden levererade till Service Bus ämnet|Antal|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till Service Bus ämnes slut punkter.|Inga dimensioner|
|D2C. endpoints. utgående. Storage|Yes|Routning: meddelanden som levereras till lagring|Antal|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till lagrings slut punkter.|Inga dimensioner|
|D2C. endpoints. utgående. Storage. blob|Yes|Routning: blobbar levererade till lagring|Antal|Totalt|Antal gånger IoT Hub som levererade blobbar till lagrings slut punkter.|Inga dimensioner|
|D2C. endpoints. utgående. Storage. byte|Yes|Routning: data som levereras till lagring|Byte|Totalt|Mängden data (byte) IoT Hub routning som levereras till lagrings slut punkter.|Inga dimensioner|
|D2C. endpoints. latens. Builtin. events|Yes|Routning: meddelande fördröjning för meddelanden/händelser|Millisekunder|Genomsnitt|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub och telemetri intränger in i den inbyggda slut punkten (meddelanden/händelser).|Inga dimensioner|
|D2C. endpoints. latens. eventHubs|Yes|Routning: meddelande fördröjning för Event Hub|Millisekunder|Genomsnitt|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub och meddelande intränger till en Event Hub-slutpunkt.|Inga dimensioner|
|D2C. endpoints. latens. serviceBusQueues|Yes|Routning: meddelande fördröjning för Service Bus kö|Millisekunder|Genomsnitt|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub-och telemetri-meddelanden intränger i en Service Bus Queue-slutpunkt.|Inga dimensioner|
|D2C. endpoints. latens. serviceBusTopics|Yes|Routning: meddelande fördröjning för Service Bus ämne|Millisekunder|Genomsnitt|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub-och telemetri-meddelande intränger i en Service Bus avsnitts slut punkt.|Inga dimensioner|
|D2C. endpoints. svars tid. Storage|Yes|Routning: meddelande fördröjning för lagring|Millisekunder|Genomsnitt|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub-och telemetri-meddelanden intränger i en lagrings slut punkt.|Inga dimensioner|
|D2C. telemetri. utgående.|Yes|Routning: telemetri ignoreras |Antal|Totalt|Antalet gånger som meddelanden släpptes genom IoT Hub routning på grund av död slut punkter. Det här värdet räknar inte meddelanden som levereras till återställnings vägen eftersom ignorerade meddelanden inte levereras där.|Inga dimensioner|
|D2C. telemetri. utgående. fallback|Yes|Routning: meddelanden levererade till reserv|Antal|Totalt|Antalet gånger IoT Hub vidarebefordran av meddelanden till slut punkten som är kopplad till återställnings vägen.|Inga dimensioner|
|D2C. telemetri. utgående. ogiltig|Yes|Routning: telemetri-meddelanden är inkompatibla|Antal|Totalt|Antalet gånger IoT Hub routningen kunde inte leverera meddelanden på grund av inkompatibilitet med slut punkten. Det här värdet omfattar inte återförsök.|Inga dimensioner|
|D2C. telemetri. utgående. överblivna|Yes|Routning: telemetri-meddelanden har överblivna |Antal|Totalt|Antalet gånger som meddelanden har överblivnas av IoT Hub routning eftersom de inte matchade några routningsregler (inklusive återställnings regeln). |Inga dimensioner|
|D2C. telemetri. utgående. lyckades|Yes|Routning: telemetri meddelanden levereras|Antal|Totalt|Antalet gånger som meddelanden har levererats till alla slut punkter med hjälp av IoT Hub routning. Om ett meddelande dirigeras till flera slut punkter ökar det här värdet med ett för varje lyckad leverans. Om ett meddelande skickas till samma slut punkt flera gånger ökar det här värdet med ett för varje lyckad leverans.|Inga dimensioner|
|D2C. telemetri. ingress. allProtocol|Yes|Skicka försök för telemetri|Antal|Totalt|Antalet telemetri från enhet till molnet försökte skickas till din IoT-hubb|Inga dimensioner|
|D2C. telemetri. ingress. sendThrottle|Yes|Antal begränsnings fel|Antal|Totalt|Antal begränsnings fel som beror på begränsning av enhetens data flöde|Inga dimensioner|
|D2C. telemetri. ingress. lyckades|Yes|Meddelande om telemetri|Antal|Totalt|Antal telemetri om enhet till molnet har skickats till din IoT-hubb|Inga dimensioner|
|D2C. delad.|Yes|Misslyckade dubbla läsningar från enheter|Antal|Totalt|Antalet misslyckade, dubbla läsningar som initierats av enheten.|Inga dimensioner|
|D2C., delad. storlek|Yes|Svars storlek för dubbla läsningar från enheter|Byte|Genomsnitt|Genomsnitt, min och Max för alla lyckade, dubbla läsningar som initierats av enheten.|Inga dimensioner|
|D2C. delad. lyckades|Yes|Lyckades dubbla läsningar från enheter|Antal|Totalt|Antalet framgångs rika enhets uppinitierade dubbla läsningar.|Inga dimensioner|
|D2C. delad. Update. Failure|Yes|Misslyckade dubbla uppdateringar från enheter|Antal|Totalt|Antalet misslyckade, dubbla uppdateringar som initierats av enheten.|Inga dimensioner|
|D2C. dubbla. Update. size|Yes|Storlek på dubbla uppdateringar från enheter|Byte|Genomsnitt|Genomsnitt, min och Max storlek för alla lyckade, dubbla uppdateringar som initierats av enheten.|Inga dimensioner|
|D2C. delad. Update. lyckades|Yes|Lyckade dubbla uppdateringar från enheter|Antal|Totalt|Antalet lyckade, dubbla uppdateringar som initierats av enheten.|Inga dimensioner|
|dailyMessageQuotaUsed|Yes|Totalt antal meddelanden som används|Antal|Maximal|Antal sammanlagt antal meddelanden som använts idag|Inga dimensioner|
|deviceDataUsage|Yes|Total användning av enhets data|Byte|Totalt|Överförda byte till och från alla enheter som är anslutna till IotHub|Inga dimensioner|
|deviceDataUsageV2|Yes|Total användning av enhets data (för hands version)|Byte|Totalt|Överförda byte till och från alla enheter som är anslutna till IotHub|Inga dimensioner|
|Devices. connectedDevices. allProtocol|Yes|Anslutna enheter (inaktuella) |Antal|Totalt|Antal enheter som är anslutna till din IoT-hubb|Inga dimensioner|
|enheter. totalDevices|Yes|Totalt antal enheter (inaktuella)|Antal|Totalt|Antal enheter som har registrerats för din IoT-hubb|Inga dimensioner|
|EventGridDeliveries|Yes|Event Grid leveranser|Antal|Totalt|Antalet IoT Hub-händelser som publicerats till Event Grid. Använd resultat dimensionen för antalet lyckade och misslyckade förfrågningar. EventType-dimensionen visar händelse typen ( https://aka.ms/ioteventgrid) .|Resultat, EventType|
|EventGridLatency|Yes|Event Grid svars tid|Millisekunder|Genomsnitt|Den genomsnittliga svars tiden (millisekunder) från när IoT Hub-händelsen genererades till när händelsen publicerades till Event Grid. Det här talet är ett medelvärde mellan alla händelse typer. Använd EventType-dimensionen för att se svars tiden för en speciell typ av händelse.|Typ|
|Jobs. cancelJob. Failure|Yes|Misslyckade jobb-annulleringar|Antal|Totalt|Antalet misslyckade anrop för att avbryta ett jobb.|Inga dimensioner|
|Jobs. cancelJob. lyckades|Yes|Slutförda jobb avbokningar|Antal|Totalt|Antalet lyckade anrop för att avbryta ett jobb.|Inga dimensioner|
|jobb. slutfört|Yes|Slutförda jobb|Antal|Totalt|Antalet slutförda jobb.|Inga dimensioner|
|Jobs. createDirectMethodJob. Failure|Yes|Det gick inte att skapa metod anrops jobb|Antal|Totalt|Antalet misslyckade skapande av direkta anrops jobb för metoden.|Inga dimensioner|
|Jobs. createDirectMethodJob. lyckades|Yes|Lyckade skapande av metod anrops jobb|Antal|Totalt|Antalet slutförda skapande av direkta metod anrops jobb.|Inga dimensioner|
|Jobs. createTwinUpdateJob. Failure|Yes|Det gick inte att skapa dubbla uppdaterings jobb|Antal|Totalt|Antalet misslyckade skapandet av dubbla uppdaterings jobb.|Inga dimensioner|
|Jobs. createTwinUpdateJob. lyckades|Yes|Skapandet av dubbla uppdaterings jobb lyckades|Antal|Totalt|Antalet slutförda skapandet av dubbla uppdaterings jobb.|Inga dimensioner|
|jobb. misslyckades|Yes|Misslyckade jobb|Antal|Totalt|Antalet misslyckade jobb.|Inga dimensioner|
|Jobs. listJobs. Failure|Yes|Misslyckade anrop till List jobb|Antal|Totalt|Antalet misslyckade anrop till List jobb.|Inga dimensioner|
|Jobs. listJobs. lyckades|Yes|Lyckade anrop till List jobb|Antal|Totalt|Antalet lyckade anrop till List jobb.|Inga dimensioner|
|Jobs. queryJobs. Failure|Yes|Misslyckade jobb frågor|Antal|Totalt|Antalet misslyckade anrop till jobb för frågor.|Inga dimensioner|
|Jobs. queryJobs. lyckades|Yes|Slutförda jobb frågor|Antal|Totalt|Antalet lyckade anrop för att köra frågor mot jobb.|Inga dimensioner|
|RoutingDataSizeInBytesDelivered|Yes|Meddelande storlek för routning i byte (för hands version)|Byte|Totalt|Den totala storleken i byte på meddelanden levererade av IoT Hub till en slut punkt. Du kan använda dimensionerna EndpointName och EndpointType för att visa storleken på meddelandena i byte som levereras till dina olika slut punkter. Mått värdet ökar för varje meddelande som levereras, inklusive om meddelandet levereras till flera slut punkter eller om meddelandet levereras till samma slut punkt flera gånger.|EndpointType, EndpointName, RoutingSource|
|RoutingDeliveries|Yes|Dirigera leveranser (för hands version)|Antal|Totalt|Antalet gånger IoT Hub försökte leverera meddelanden till alla slut punkter med hjälp av routning. Använd resultat dimensionen för att se antalet lyckade eller misslyckade försök. Om du vill se orsaken till fel, som ogiltig, släppt eller föräldralös, använder du FailureReasonCategory-dimensionen. Du kan också använda dimensionerna EndpointName och EndpointType för att förstå hur många meddelanden som levererats till dina olika slut punkter. Mått värdet ökar med ett för varje leverans försök, inklusive om meddelandet levereras till flera slut punkter eller om meddelandet levereras till samma slut punkt flera gånger.|EndpointType, EndpointName, FailureReasonCategory, result, RoutingSource|
|RoutingDeliveryLatency|Yes|Leverans svars tid för routning (för hands version)|Millisekunder|Genomsnitt|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub-och telemetri-meddelanden intränger i en slut punkt. Du kan använda dimensionerna EndpointName och EndpointType för att förstå svars tiden för dina olika slut punkter.|EndpointType, EndpointName, RoutingSource|
|tenantHub.requestedUsageRate|Yes|begärd användnings frekvens|Procent|Genomsnitt|begärd användnings frekvens|Inga dimensioner|
|totalDeviceCount|Yes|Totalt antal enheter|Antal|Genomsnitt|Antal enheter som har registrerats för din IoT-hubb|Inga dimensioner|
|twinQueries. Failure|Yes|Misslyckade dubbla frågor|Antal|Totalt|Antalet misslyckade dubbla frågor.|Inga dimensioner|
|twinQueries.resultSize|Yes|Resultat storlek för dubbla frågor|Byte|Genomsnitt|Genomsnitt, min och Max för resultat storleken för alla lyckade dubbla frågor.|Inga dimensioner|
|twinQueries. lyckades|Yes|Lyckades dubbla frågor|Antal|Totalt|Antalet lyckade dubbla frågor.|Inga dimensioner|


## <a name="microsoftdevicesiothubs"></a>Microsoft. Devices/IotHubs

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|C2D. commands. utgående. Abandon. lyckades|Yes|Övergivna C2D-meddelanden|Antal|Totalt|Antal meddelanden från moln till enhet som har övergivits av enheten|Inga dimensioner|
|C2D. commands. utgående. Complete. lyckades|Yes|C2D meddelande leveranser har slutförts|Antal|Totalt|Antalet meddelande leveranser från moln till enhet har slutförts av enheten|Inga dimensioner|
|C2D. commands. rekasta. Success|Yes|Avvisade C2D-meddelanden|Antal|Totalt|Antal meddelanden från moln till enhet som avvisats av enheten|Inga dimensioner|
|C2D. Methods. Failure|Yes|Misslyckade direkta metod anrop|Antal|Totalt|Antalet misslyckade direkta metod anrop.|Inga dimensioner|
|C2D. Methods. requestSize|Yes|Begär ande storlek för direkta metod anrop|Byte|Genomsnitt|Genomsnitt, min och Max för alla lyckade direkta metod begär Anden.|Inga dimensioner|
|C2D. Methods. responseSize|Yes|Svars storlek för direkta metod anrop|Byte|Genomsnitt|Medelvärde, min och Max för alla lyckade direkta metod svar.|Inga dimensioner|
|C2D. Methods. Success|Yes|Direkta metod anrop|Antal|Totalt|Antalet lyckade direkta metod anrop.|Inga dimensioner|
|C2D. delad.|Yes|Det gick inte att dubbla läsningar från Server delen|Antal|Totalt|Antalet dubbla läsningar som initierats av alla misslyckade backend-slutpunkter.|Inga dimensioner|
|C2D., delad. storlek|Yes|Svars storlek för dubbla läsningar från Server delen|Byte|Genomsnitt|Genomsnitt, min och Max för alla lyckade, dubbla läsningar som initieras i slut punkt.|Inga dimensioner|
|C2D. delad. lyckades|Yes|Lyckades dubbla läspaket från Server delen|Antal|Totalt|Antalet dubbla läsningar som initierats av alla lyckade.|Inga dimensioner|
|C2D. delad. Update. Failure|Yes|Misslyckade dubbla uppdateringar från Server delen|Antal|Totalt|Antalet dubbla uppdateringar som initierats av alla misslyckade backend-slutpunkter.|Inga dimensioner|
|C2D. dubbla. Update. size|Yes|Storlek på dubbla uppdateringar från Server delen|Byte|Genomsnitt|Genomsnitt, min och Max storlek för alla lyckade, dubbla uppdateringar som initieras.|Inga dimensioner|
|C2D. delad. Update. lyckades|Yes|Lyckades dubbla uppdateringar från Server delen|Antal|Totalt|Antalet fullständiga uppdateringar som initierats av alla lyckade backend-uppdateringar.|Inga dimensioner|
|C2DMessagesExpired|Yes|C2D meddelanden har upphört att gälla|Antal|Totalt|Antal utgångna meddelanden från moln till enhet|Inga dimensioner|
|konfigurationer|Yes|Konfigurations mått|Antal|Totalt|Mått för konfigurations åtgärder|Inga dimensioner|
|connectedDeviceCount|No|Anslutna enheter|Antal|Genomsnitt|Antal enheter som är anslutna till din IoT-hubb|Inga dimensioner|
|D2C. endpoints. utgående. Builtity. events|Yes|Routning: meddelanden som levereras till meddelanden/händelser|Antal|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till den inbyggda slut punkten (meddelanden/händelser).|Inga dimensioner|
|D2C. endpoints. utgående. eventHubs|Yes|Routning: meddelanden levererade till Händelsehubben|Antal|Totalt|Antal gånger IoT Hub routning har levererat meddelanden till Event Hub-slutpunkter.|Inga dimensioner|
|D2C. endpoints. utgående. serviceBusQueues|Yes|Routning: meddelanden levererade till Service Bus kö|Antal|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till Service Bus-köns slut punkter.|Inga dimensioner|
|D2C. endpoints. utgående. serviceBusTopics|Yes|Routning: meddelanden levererade till Service Bus ämnet|Antal|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till Service Bus ämnes slut punkter.|Inga dimensioner|
|D2C. endpoints. utgående. Storage|Yes|Routning: meddelanden som levereras till lagring|Antal|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till lagrings slut punkter.|Inga dimensioner|
|D2C. endpoints. utgående. Storage. blob|Yes|Routning: blobbar levererade till lagring|Antal|Totalt|Antal gånger IoT Hub som levererade blobbar till lagrings slut punkter.|Inga dimensioner|
|D2C. endpoints. utgående. Storage. byte|Yes|Routning: data som levereras till lagring|Byte|Totalt|Mängden data (byte) IoT Hub routning som levereras till lagrings slut punkter.|Inga dimensioner|
|D2C. endpoints. latens. Builtin. events|Yes|Routning: meddelande fördröjning för meddelanden/händelser|Millisekunder|Genomsnitt|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub och telemetri intränger in i den inbyggda slut punkten (meddelanden/händelser).|Inga dimensioner|
|D2C. endpoints. latens. eventHubs|Yes|Routning: meddelande fördröjning för Event Hub|Millisekunder|Genomsnitt|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub och meddelande intränger till en Event Hub-slutpunkt.|Inga dimensioner|
|D2C. endpoints. latens. serviceBusQueues|Yes|Routning: meddelande fördröjning för Service Bus kö|Millisekunder|Genomsnitt|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub-och telemetri-meddelanden intränger i en Service Bus Queue-slutpunkt.|Inga dimensioner|
|D2C. endpoints. latens. serviceBusTopics|Yes|Routning: meddelande fördröjning för Service Bus ämne|Millisekunder|Genomsnitt|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub-och telemetri-meddelande intränger i en Service Bus avsnitts slut punkt.|Inga dimensioner|
|D2C. endpoints. svars tid. Storage|Yes|Routning: meddelande fördröjning för lagring|Millisekunder|Genomsnitt|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub-och telemetri-meddelanden intränger i en lagrings slut punkt.|Inga dimensioner|
|D2C. telemetri. utgående.|Yes|Routning: telemetri ignoreras |Antal|Totalt|Antalet gånger som meddelanden släpptes genom IoT Hub routning på grund av död slut punkter. Det här värdet räknar inte meddelanden som levereras till återställnings vägen eftersom ignorerade meddelanden inte levereras där.|Inga dimensioner|
|D2C. telemetri. utgående. fallback|Yes|Routning: meddelanden levererade till reserv|Antal|Totalt|Antalet gånger IoT Hub vidarebefordran av meddelanden till slut punkten som är kopplad till återställnings vägen.|Inga dimensioner|
|D2C. telemetri. utgående. ogiltig|Yes|Routning: telemetri-meddelanden är inkompatibla|Antal|Totalt|Antalet gånger IoT Hub routningen kunde inte leverera meddelanden på grund av inkompatibilitet med slut punkten. Det här värdet omfattar inte återförsök.|Inga dimensioner|
|D2C. telemetri. utgående. överblivna|Yes|Routning: telemetri-meddelanden har överblivna |Antal|Totalt|Antalet gånger som meddelanden har överblivnas av IoT Hub routning eftersom de inte matchade några routningsregler (inklusive återställnings regeln). |Inga dimensioner|
|D2C. telemetri. utgående. lyckades|Yes|Routning: telemetri meddelanden levereras|Antal|Totalt|Antalet gånger som meddelanden har levererats till alla slut punkter med hjälp av IoT Hub routning. Om ett meddelande dirigeras till flera slut punkter ökar det här värdet med ett för varje lyckad leverans. Om ett meddelande skickas till samma slut punkt flera gånger ökar det här värdet med ett för varje lyckad leverans.|Inga dimensioner|
|D2C. telemetri. ingress. allProtocol|Yes|Skicka försök för telemetri|Antal|Totalt|Antalet telemetri från enhet till molnet försökte skickas till din IoT-hubb|Inga dimensioner|
|D2C. telemetri. ingress. sendThrottle|Yes|Antal begränsnings fel|Antal|Totalt|Antal begränsnings fel som beror på begränsning av enhetens data flöde|Inga dimensioner|
|D2C. telemetri. ingress. lyckades|Yes|Meddelande om telemetri|Antal|Totalt|Antal telemetri om enhet till molnet har skickats till din IoT-hubb|Inga dimensioner|
|D2C. delad.|Yes|Misslyckade dubbla läsningar från enheter|Antal|Totalt|Antalet misslyckade, dubbla läsningar som initierats av enheten.|Inga dimensioner|
|D2C., delad. storlek|Yes|Svars storlek för dubbla läsningar från enheter|Byte|Genomsnitt|Genomsnitt, min och Max för alla lyckade, dubbla läsningar som initierats av enheten.|Inga dimensioner|
|D2C. delad. lyckades|Yes|Lyckades dubbla läsningar från enheter|Antal|Totalt|Antalet framgångs rika enhets uppinitierade dubbla läsningar.|Inga dimensioner|
|D2C. delad. Update. Failure|Yes|Misslyckade dubbla uppdateringar från enheter|Antal|Totalt|Antalet misslyckade, dubbla uppdateringar som initierats av enheten.|Inga dimensioner|
|D2C. dubbla. Update. size|Yes|Storlek på dubbla uppdateringar från enheter|Byte|Genomsnitt|Genomsnitt, min och Max storlek för alla lyckade, dubbla uppdateringar som initierats av enheten.|Inga dimensioner|
|D2C. delad. Update. lyckades|Yes|Lyckade dubbla uppdateringar från enheter|Antal|Totalt|Antalet lyckade, dubbla uppdateringar som initierats av enheten.|Inga dimensioner|
|dailyMessageQuotaUsed|Yes|Totalt antal meddelanden som används|Antal|Maximal|Antal sammanlagt antal meddelanden som använts idag|Inga dimensioner|
|deviceDataUsage|Yes|Total användning av enhets data|Byte|Totalt|Överförda byte till och från alla enheter som är anslutna till IotHub|Inga dimensioner|
|deviceDataUsageV2|Yes|Total användning av enhets data (för hands version)|Byte|Totalt|Överförda byte till och från alla enheter som är anslutna till IotHub|Inga dimensioner|
|Devices. connectedDevices. allProtocol|Yes|Anslutna enheter (inaktuella) |Antal|Totalt|Antal enheter som är anslutna till din IoT-hubb|Inga dimensioner|
|enheter. totalDevices|Yes|Totalt antal enheter (inaktuella)|Antal|Totalt|Antal enheter som har registrerats för din IoT-hubb|Inga dimensioner|
|EventGridDeliveries|Yes|Event Grid leveranser|Antal|Totalt|Antalet IoT Hub-händelser som publicerats till Event Grid. Använd resultat dimensionen för antalet lyckade och misslyckade förfrågningar. EventType-dimensionen visar händelse typen ( https://aka.ms/ioteventgrid) .|Resultat, EventType|
|EventGridLatency|Yes|Event Grid svars tid|Millisekunder|Genomsnitt|Den genomsnittliga svars tiden (millisekunder) från när IoT Hub-händelsen genererades till när händelsen publicerades till Event Grid. Det här talet är ett medelvärde mellan alla händelse typer. Använd EventType-dimensionen för att se svars tiden för en speciell typ av händelse.|Typ|
|Jobs. cancelJob. Failure|Yes|Misslyckade jobb-annulleringar|Antal|Totalt|Antalet misslyckade anrop för att avbryta ett jobb.|Inga dimensioner|
|Jobs. cancelJob. lyckades|Yes|Slutförda jobb avbokningar|Antal|Totalt|Antalet lyckade anrop för att avbryta ett jobb.|Inga dimensioner|
|jobb. slutfört|Yes|Slutförda jobb|Antal|Totalt|Antalet slutförda jobb.|Inga dimensioner|
|Jobs. createDirectMethodJob. Failure|Yes|Det gick inte att skapa metod anrops jobb|Antal|Totalt|Antalet misslyckade skapande av direkta anrops jobb för metoden.|Inga dimensioner|
|Jobs. createDirectMethodJob. lyckades|Yes|Lyckade skapande av metod anrops jobb|Antal|Totalt|Antalet slutförda skapande av direkta metod anrops jobb.|Inga dimensioner|
|Jobs. createTwinUpdateJob. Failure|Yes|Det gick inte att skapa dubbla uppdaterings jobb|Antal|Totalt|Antalet misslyckade skapandet av dubbla uppdaterings jobb.|Inga dimensioner|
|Jobs. createTwinUpdateJob. lyckades|Yes|Skapandet av dubbla uppdaterings jobb lyckades|Antal|Totalt|Antalet slutförda skapandet av dubbla uppdaterings jobb.|Inga dimensioner|
|jobb. misslyckades|Yes|Misslyckade jobb|Antal|Totalt|Antalet misslyckade jobb.|Inga dimensioner|
|Jobs. listJobs. Failure|Yes|Misslyckade anrop till List jobb|Antal|Totalt|Antalet misslyckade anrop till List jobb.|Inga dimensioner|
|Jobs. listJobs. lyckades|Yes|Lyckade anrop till List jobb|Antal|Totalt|Antalet lyckade anrop till List jobb.|Inga dimensioner|
|Jobs. queryJobs. Failure|Yes|Misslyckade jobb frågor|Antal|Totalt|Antalet misslyckade anrop till jobb för frågor.|Inga dimensioner|
|Jobs. queryJobs. lyckades|Yes|Slutförda jobb frågor|Antal|Totalt|Antalet lyckade anrop för att köra frågor mot jobb.|Inga dimensioner|
|RoutingDataSizeInBytesDelivered|Yes|Meddelande storlek för routning i byte (för hands version)|Byte|Totalt|Den totala storleken i byte på meddelanden levererade av IoT Hub till en slut punkt. Du kan använda dimensionerna EndpointName och EndpointType för att visa storleken på meddelandena i byte som levereras till dina olika slut punkter. Mått värdet ökar för varje meddelande som levereras, inklusive om meddelandet levereras till flera slut punkter eller om meddelandet levereras till samma slut punkt flera gånger.|EndpointType, EndpointName, RoutingSource|
|RoutingDeliveries|Yes|Dirigera leveranser (för hands version)|Antal|Totalt|Antalet gånger IoT Hub försökte leverera meddelanden till alla slut punkter med hjälp av routning. Använd resultat dimensionen för att se antalet lyckade eller misslyckade försök. Om du vill se orsaken till fel, som ogiltig, släppt eller föräldralös, använder du FailureReasonCategory-dimensionen. Du kan också använda dimensionerna EndpointName och EndpointType för att förstå hur många meddelanden som levererats till dina olika slut punkter. Mått värdet ökar med ett för varje leverans försök, inklusive om meddelandet levereras till flera slut punkter eller om meddelandet levereras till samma slut punkt flera gånger.|EndpointType, EndpointName, FailureReasonCategory, result, RoutingSource|
|RoutingDeliveryLatency|Yes|Leverans svars tid för routning (för hands version)|Millisekunder|Genomsnitt|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub-och telemetri-meddelanden intränger i en slut punkt. Du kan använda dimensionerna EndpointName och EndpointType för att förstå svars tiden för dina olika slut punkter.|EndpointType, EndpointName, RoutingSource|
|totalDeviceCount|No|Totalt antal enheter|Antal|Genomsnitt|Antal enheter som har registrerats för din IoT-hubb|Inga dimensioner|
|twinQueries. Failure|Yes|Misslyckade dubbla frågor|Antal|Totalt|Antalet misslyckade dubbla frågor.|Inga dimensioner|
|twinQueries.resultSize|Yes|Resultat storlek för dubbla frågor|Byte|Genomsnitt|Genomsnitt, min och Max för resultat storleken för alla lyckade dubbla frågor.|Inga dimensioner|
|twinQueries. lyckades|Yes|Lyckades dubbla frågor|Antal|Totalt|Antalet lyckade dubbla frågor.|Inga dimensioner|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft. Devices/provisioningServices

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|AttestationAttempts|Yes|Attesterings försök|Antal|Totalt|Antal försök till enhets attestering|ProvisioningServiceName, status, protokoll|
|DeviceAssignments|Yes|Tilldelade enheter|Antal|Totalt|Antal enheter som har tilldelats till en IoT-hubb|ProvisioningServiceName, IotHubName|
|RegistrationAttempts|Yes|Registrerings försök|Antal|Totalt|Antal enhets registreringar som försökts|ProvisioningServiceName, IotHubName, status|


## <a name="microsoftdigitaltwinsdigitaltwinsinstances"></a>Microsoft. DigitalTwins/digitalTwinsInstances

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ApiRequests|Yes|API-begäranden|Antal|Totalt|Antalet API-begäranden som gjorts för digitala dubbla och Läs-, Skriv-, borttagnings-och fråge åtgärder.|Åtgärd, autentisering, protokoll, StatusCode, StatusCodeClass, status text|
|ApiRequestsFailureRate|Yes|Frekvens för misslyckade API-begäranden|Procent|Genomsnitt|Procent andelen API-begäranden som tjänsten tar emot för din instans som returnerar ett internt fel (500) svars kod för digitala dubbla Läs-, Skriv-, borttagnings-och fråge åtgärder.|Åtgärd, autentisering, protokoll|
|ApiRequestsLatency|Yes|Svars tid för API-begäranden|Millisekunder|Genomsnitt|Svars tiden för API-begäranden, d.v.s. från när begäran tas emot av Azure Digitals, tills tjänsten skickar ett lyckat/misslyckat resultat för digitala dubbla, Skriv-, borttagnings-och fråge åtgärder.|Åtgärd, autentisering, protokoll, StatusCode, StatusCodeClass, status text|
|BillingApiOperations|Yes|Fakturerings-API-åtgärder|Antal|Totalt|Fakturerings mått för antalet API-begäranden som gjorts mot tjänsten Azure Digitals dubbla.|MeterId|
|BillingMessagesProcessed|Yes|Behandlade fakturerings meddelanden|Antal|Totalt|Fakturerings mått för antalet meddelanden som skickas ut från Azure Digitals dubbla till externa slut punkter.|MeterId|
|BillingQueryUnits|Yes|Fakturerings frågans enheter|Antal|Totalt|Antalet fråge enheter, internt beräknade mått på användning av tjänst resurser som används för att köra frågor.|MeterId|
|IngressEvents|Yes|Ingress-händelser|Antal|Totalt|Antalet inkommande telemetri-händelser till Azure Digitals dubbla.|Resultat|
|IngressEventsFailureRate|Yes|Händelse frekvens vid inkommande händelser|Procent|Genomsnitt|Procent andelen inkommande telemetri för vilka tjänsten returnerar ett internt fel (500) svars kod.|Inga dimensioner|
|IngressEventsLatency|Yes|Ingress händelse fördröjning|Millisekunder|Genomsnitt|Tiden från när en händelse kommer till när den är redo att tas ut av Azure Digitals, då tjänsten skickar ett lyckat/misslyckat resultat.|Resultat|
|ModelCount|Yes|Modellantal|Antal|Totalt|Totalt antal modeller i Azure Digitals dubbla instansen. Använd det här måttet för att avgöra om du närmar dig tjänst gränsen för högsta antal modeller som tillåts per instans.|Inga dimensioner|
|Routning|Yes|Vidarebefordrade meddelanden|Antal|Totalt|Antalet meddelanden som dirigerats till en slut punkt för Azure-tjänster som Händelsehubben, Service Bus eller Event Grid.|EndpointType, resultat|
|RoutingFailureRate|Yes|Antal misslyckade vägar|Procent|Genomsnitt|Procent andelen händelser som resulterar i ett fel när de dirigeras från Azure Digitals dubbla till en slut punkt för Azure-tjänster som Händelsehubben, Service Bus eller Event Grid.|EndpointType|
|RoutingLatency|Yes|Svars tid för routning|Millisekunder|Genomsnitt|Tiden som förflutit mellan en händelse som skickas från Azure Digitals till när den ansluts till Azure-tjänsten för slut punkter som Händelsehubben, Service Bus eller Event Grid.|EndpointType, resultat|
|TwinCount|Yes|Antal dubbla|Antal|Totalt|Totalt antal dubbla i Azure Digitals-instansen. Använd det här måttet för att avgöra om du närmar dig tjänst gränsen för högsta antal tillåtna gränser per instans.|Inga dimensioner|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|AddRegion|Yes|Region tillagt|Antal|Antal|Region tillagt|Region|
|AutoscaleMaxThroughput|No|Skala maximalt data flöde|Antal|Maximal|Skala maximalt data flöde|DatabaseName, samlings namn|
|AvailableStorage|No|föråldrad Tillgängligt lagrings utrymme|Byte|Totalt|"Tillgängligt lagrings utrymme" tas bort från Azure Monitor i slutet av september 2023. Lagrings storleken för Cosmos DB samlingen är nu obegränsad. Den enda begränsningen är att lagrings storleken för varje logisk partitionsnyckel är 20. Du kan aktivera PartitionKeyStatistics i diagnostisk logg för att känna till lagrings förbrukningen för Top partition-nycklar. Om du vill ha mer information om Cosmos DB lagrings kvot kontrollerar du det här dokumentet https://docs.microsoft.com/azure/cosmos-db/concepts-limits . Efter utfasningen inaktive ras de återstående aviserings reglerna som fortfarande definierats för det inaktuella måttet automatiskt efter utgångs datumet.|Samlings namn, DatabaseName, region|
|CassandraConnectionClosures|No|Cassandra-anslutningens stängningar|Antal|Totalt|Antalet Cassandra-anslutningar som stängdes, rapporteras med en 1 minuts kornig het|Region, ClosureReason|
|CassandraConnectorAvgReplicationLatency|No|Genomsnittlig ReplicationLatency för Cassandra Connector|Millisekunder|Genomsnitt|Genomsnittlig ReplicationLatency för Cassandra Connector|Inga dimensioner|
|CassandraConnectorReplicationHealthStatus|No|Hälso status för Cassandra Connector-replikering|Antal|Antal|Hälso status för Cassandra Connector-replikering|NotStarted, ReplicationInProgress, fel|
|CassandraKeyspaceCreate|No|Cassandra-tecken avstånd har skapats|Antal|Antal|Cassandra-tecken avstånd har skapats|ResourceName |
|CassandraKeyspaceDelete|No|Cassandra-tecken utrymme borttaget|Antal|Antal|Cassandra-tecken utrymme borttaget|ResourceName |
|CassandraKeyspaceThroughputUpdate|No|Cassandra-dataflöde har uppdaterats|Antal|Antal|Cassandra-dataflöde har uppdaterats|ResourceName |
|CassandraKeyspaceUpdate|No|Cassandra-disk utrymme uppdaterat|Antal|Antal|Cassandra-disk utrymme uppdaterat|ResourceName |
|CassandraRequestCharges|No|Avgifter för Cassandra-begäran|Antal|Totalt|Ru: er förbrukat för Cassandra begär Anden|DatabaseName, samlings namn, region, OperationType, ResourceType|
|CassandraRequests|No|Cassandra-begäranden|Antal|Antal|Antal Cassandra-begäranden som gjorts|DatabaseName, samlings namn, region, OperationType, ResourceType, ErrorCode|
|CassandraTableCreate|No|Cassandra-tabellen har skapats|Antal|Antal|Cassandra-tabellen har skapats|ResourceName, ChildResourceName, |
|CassandraTableDelete|No|Cassandra-tabellen har tagits bort|Antal|Antal|Cassandra-tabellen har tagits bort|ResourceName, ChildResourceName, |
|CassandraTableThroughputUpdate|No|Cassandra tabell data flöde har uppdaterats|Antal|Antal|Cassandra tabell data flöde har uppdaterats|ResourceName, ChildResourceName, |
|CassandraTableUpdate|No|Cassandra-tabellen har uppdaterats|Antal|Antal|Cassandra-tabellen har uppdaterats|ResourceName, ChildResourceName, |
|CreateAccount|Yes|Kontot har skapats|Antal|Antal|Kontot har skapats|Inga dimensioner|
|DataUsage|No|Dataanvändning|Byte|Totalt|Total data användning rapporterad med 5 minuters precision|Samlings namn, DatabaseName, region|
|DeleteAccount|Yes|Kontot har tagits bort|Antal|Antal|Kontot har tagits bort|Inga dimensioner|
|DocumentCount|No|Antal dokument|Antal|Totalt|Totalt antal dokument som har rapporter ATS med 5 minuters precision|Samlings namn, DatabaseName, region|
|DocumentQuota|No|Dokument kvot|Byte|Totalt|Total lagrings kvot som rapporter ATS med 5 minuters precision|Samlings namn, DatabaseName, region|
|GremlinDatabaseCreate|No|Gremlin-databas har skapats|Antal|Antal|Gremlin-databas har skapats|ResourceName |
|GremlinDatabaseDelete|No|Gremlin-databasen har tagits bort|Antal|Antal|Gremlin-databasen har tagits bort|ResourceName |
|GremlinDatabaseThroughputUpdate|No|Gremlin Database-genomflöde har uppdaterats|Antal|Antal|Gremlin Database-genomflöde har uppdaterats|ResourceName |
|GremlinDatabaseUpdate|No|Gremlin-databasen har uppdaterats|Antal|Antal|Gremlin-databasen har uppdaterats|ResourceName |
|GremlinGraphCreate|No|Gremlin graf har skapats|Antal|Antal|Gremlin graf har skapats|ResourceName, ChildResourceName, |
|GremlinGraphDelete|No|Gremlin Graph borttagen|Antal|Antal|Gremlin Graph borttagen|ResourceName, ChildResourceName, |
|GremlinGraphThroughputUpdate|No|Gremlin Graph-genomflöde har uppdaterats|Antal|Antal|Gremlin Graph-genomflöde har uppdaterats|ResourceName, ChildResourceName, |
|GremlinGraphUpdate|No|Gremlin-diagrammet har uppdaterats|Antal|Antal|Gremlin-diagrammet har uppdaterats|ResourceName, ChildResourceName, |
|IndexUsage|No|Indexanvändning|Byte|Totalt|Total användning av index rapporterad med 5 minuters precision|Samlings namn, DatabaseName, region|
|IntegratedCacheEvictedEntriesSize|No|IntegratedCacheEvictedEntriesSize|Byte|Genomsnitt|Storlek på poster som avlägsnats från det integrerade cacheminnet|CacheType, region|
|IntegratedCacheHitRate|No|IntegratedCacheHitRate|Procent|Genomsnitt|Antal cacheträffar för integrerade cacheminnen|CacheType, region|
|IntegratedCacheSize|No|IntegratedCacheSize|Byte|Genomsnitt|Storlek på integrerade cacheminnen för dedikerade Gateway-begäranden|CacheType, region|
|IntegratedCacheTTLExpirationCount|No|IntegratedCacheTTLExpirationCount|Antal|Genomsnitt|Antal poster som tagits bort från det integrerade cacheminnet på grund av förfallo tid för TTL|CacheType, region|
|MetadataRequests|No|Begär Anden om metadata|Antal|Antal|Antal metadata-begäranden. Cosmos DB behåller systemets metadata-samling för varje konto, vilket gör att du kan räkna upp samlingar, databaser osv och deras konfigurationer kostnads fritt.|DatabaseName, samlings namn, region, StatusCode, |
|MongoCollectionCreate|No|Mongo-samling har skapats|Antal|Antal|Mongo-samling har skapats|ResourceName, ChildResourceName, |
|MongoCollectionDelete|No|Mongo-samlingen har tagits bort|Antal|Antal|Mongo-samlingen har tagits bort|ResourceName, ChildResourceName, |
|MongoCollectionThroughputUpdate|No|Mongo Collection-genomflöde har uppdaterats|Antal|Antal|Mongo Collection-genomflöde har uppdaterats|ResourceName, ChildResourceName, |
|MongoCollectionUpdate|No|Mongo-samlingen har uppdaterats|Antal|Antal|Mongo-samlingen har uppdaterats|ResourceName, ChildResourceName, |
|MongoDatabaseDelete|No|Mongo-databasen har tagits bort|Antal|Antal|Mongo-databasen har tagits bort|ResourceName |
|MongoDatabaseThroughputUpdate|No|Mongo Database-genomflöde har uppdaterats|Antal|Antal|Mongo Database-genomflöde har uppdaterats|ResourceName |
|MongoDBDatabaseCreate|No|Mongo-databas har skapats|Antal|Antal|Mongo-databas har skapats|ResourceName |
|MongoDBDatabaseUpdate|No|Mongo-databasen har uppdaterats|Antal|Antal|Mongo-databasen har uppdaterats|ResourceName |
|MongoRequestCharge|Yes|Mongo begär ande avgift|Antal|Totalt|Mongo enheter för förbrukad begäran|DatabaseName, samlings namn, region, CommandName, ErrorCode, status|
|MongoRequests|Yes|Mongo-begäranden|Antal|Antal|Antal Mongo-begäranden som gjorts|DatabaseName, samlings namn, region, CommandName, ErrorCode, status|
|MongoRequestsCount|No|föråldrad Mongo begär ande frekvens|CountPerSecond|Genomsnitt|Antal Mongo begär Anden per sekund|DatabaseName, samlings region, ErrorCode|
|MongoRequestsDelete|No|föråldrad Mongo ta bort begär ande frekvens|CountPerSecond|Genomsnitt|Mongo ta bort begäran per sekund|DatabaseName, samlings region, ErrorCode|
|MongoRequestsInsert|No|föråldrad Mongo infoga begär ande frekvens|CountPerSecond|Genomsnitt|Antal Mongo infogningar per sekund|DatabaseName, samlings region, ErrorCode|
|MongoRequestsQuery|No|föråldrad Mongo för förfrågningar|CountPerSecond|Genomsnitt|Mongo-förfrågan per sekund|DatabaseName, samlings region, ErrorCode|
|MongoRequestsUpdate|No|föråldrad Frekvens för mongo uppdaterings begär Anden|CountPerSecond|Genomsnitt|Mongo för begär ande uppdatering per sekund|DatabaseName, samlings region, ErrorCode|
|NormalizedRUConsumption|No|Normaliserad RU-förbrukning|Procent|Maximal|Max procent per minut för RU-förbrukning|Samlings namn, DatabaseName, region, PartitionKeyRangeId|
|ProvisionedThroughput|No|Etablerat dataflöde|Antal|Maximal|Etablerat dataflöde|DatabaseName, samlings namn|
|RegionFailover|Yes|Regionen har redundansväxlats|Antal|Antal|Regionen har redundansväxlats|Inga dimensioner|
|RemoveRegion|Yes|Region borttagen|Antal|Antal|Region borttagen|Region|
|ReplicationLatency|Yes|Fördröjning för P99-replikering|Millisekunder|Genomsnitt|P99 för replikering i käll-och mål regioner för geo-aktiverat konto|SourceRegion, TargetRegion|
|ServerSideLatency|No|Svars tid på Server Sidan|Millisekunder|Genomsnitt|Svars tid på Server Sidan|DatabaseName, samlings namn, region, ConnectionMode, OperationType, PublicAPIType|
|ServiceAvailability|No|Tjänst tillgänglighet|Procent|Genomsnitt|Konto begär tillgänglighet på en timme, dag eller månads kornig het|Inga dimensioner|
|SqlContainerCreate|No|SQL-behållare har skapats|Antal|Antal|SQL-behållare har skapats|ResourceName, ChildResourceName, |
|SqlContainerDelete|No|SQL-behållare borttagen|Antal|Antal|SQL-behållare borttagen|ResourceName, ChildResourceName, |
|SqlContainerThroughputUpdate|No|SQL container data flöde uppdaterat|Antal|Antal|SQL container data flöde uppdaterat|ResourceName, ChildResourceName, |
|SqlContainerUpdate|No|SQL-behållare har uppdaterats|Antal|Antal|SQL-behållare har uppdaterats|ResourceName, ChildResourceName, |
|SqlDatabaseCreate|No|SQL-databas har skapats|Antal|Antal|SQL-databas har skapats|ResourceName |
|SqlDatabaseDelete|No|SQL-databas borttagen|Antal|Antal|SQL-databas borttagen|ResourceName |
|SqlDatabaseThroughputUpdate|No|SQL Database-dataflöde uppdaterat|Antal|Antal|SQL Database-dataflöde uppdaterat|ResourceName |
|SqlDatabaseUpdate|No|SQL-databas uppdaterad|Antal|Antal|SQL-databas uppdaterad|ResourceName |
|TableTableCreate|No|AzureTable-tabellen har skapats|Antal|Antal|AzureTable-tabellen har skapats|ResourceName |
|TableTableDelete|No|AzureTable-tabellen har tagits bort|Antal|Antal|AzureTable-tabellen har tagits bort|ResourceName |
|TableTableThroughputUpdate|No|AzureTable tabell data flöde har uppdaterats|Antal|Antal|AzureTable tabell data flöde har uppdaterats|ResourceName |
|TableTableUpdate|No|AzureTable-tabellen har uppdaterats|Antal|Antal|AzureTable-tabellen har uppdaterats|ResourceName |
|TotalRequests|Yes|Totalt antal förfrågningar|Antal|Antal|Antal begär Anden som gjorts|DatabaseName, samlings namn, region, StatusCode, OperationType, status|
|TotalRequestUnits|Yes|Totalt antal enheter för programbegäran|Antal|Totalt|Förbrukade enheter för begär Ande|DatabaseName, samlings namn, region, StatusCode, OperationType, status|
|UpdateAccountKeys|Yes|Konto nycklar har uppdaterats|Antal|Antal|Konto nycklar har uppdaterats|KeyType|
|UpdateAccountNetworkSettings|Yes|Kontots nätverks inställningar har uppdaterats|Antal|Antal|Kontots nätverks inställningar har uppdaterats|Inga dimensioner|
|UpdateAccountReplicationSettings|Yes|Kontots replikeringsinställningar har uppdaterats|Antal|Antal|Kontots replikeringsinställningar har uppdaterats|Inga dimensioner|
|UpdateDiagnosticsSettings|No|Inställningarna för konto diagnostik har uppdaterats|Antal|Antal|Inställningarna för konto diagnostik har uppdaterats|DiagnosticSettingsName, ResourceGroupName|


## <a name="microsofteventgriddomains"></a>Microsoft. EventGrid/Domains

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Yes|Avancerade filter utvärderingar|Antal|Totalt|Totalt antal avancerade filter som utvärderas över händelse prenumerationer för det här ämnet.|Ämne, EventSubscriptionName, DomainEventSubscriptionName|
|DeadLetteredCount|Yes|Obeställbara, Brevade händelser|Antal|Totalt|Totalt antal inloggade obeställbara meddelanden som matchar den här händelse prenumerationen|Ämne, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|
|DeliveryAttemptFailCount|No|Misslyckade leverans händelser|Antal|Totalt|Totalt antal händelser som inte levererades till den här händelse prenumerationen|Ämne, EventSubscriptionName, DomainEventSubscriptionName, fel, ErrorType|
|DeliverySuccessCount|Yes|Levererade händelser|Antal|Totalt|Totalt antal händelser levererade till den här händelse prenumerationen|Ämne, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|No|Varaktighet för mål bearbetning|Millisekunder|Genomsnitt|Varaktighet för mål bearbetning i millisekunder|Ämne, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Yes|Ignorerade händelser|Antal|Totalt|Totalt antal ignorerade händelser som matchar den här händelse prenumerationen|Ämne, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|MatchedEventCount|Yes|Matchade händelser|Antal|Totalt|Totalt antal händelser som matchar den här händelse prenumerationen|Ämne, EventSubscriptionName, DomainEventSubscriptionName|
|PublishFailCount|Yes|Publicera misslyckade händelser|Antal|Totalt|Totalt antal händelser som inte publicerats till det här avsnittet|Ämne, ErrorType, fel|
|PublishSuccessCount|Yes|Publicerade händelser|Antal|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Avsnitt|
|PublishSuccessLatencyInMs|Yes|Slutför svars tid för publicering|Millisekunder|Totalt|Publicera lyckad svars tid i millisekunder|Inga dimensioner|


## <a name="microsofteventgrideventsubscriptions"></a>Microsoft. EventGrid/eventSubscriptions

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Obeställbara, Brevade händelser|Antal|Totalt|Totalt antal inloggade obeställbara meddelanden som matchar den här händelse prenumerationen|DeadLetterReason|
|DeliveryAttemptFailCount|No|Misslyckade leverans händelser|Antal|Totalt|Totalt antal händelser som inte levererades till den här händelse prenumerationen|Fel, ErrorType|
|DeliverySuccessCount|Yes|Levererade händelser|Antal|Totalt|Totalt antal händelser levererade till den här händelse prenumerationen|Inga dimensioner|
|DestinationProcessingDurationInMs|No|Varaktighet för mål bearbetning|Millisekunder|Genomsnitt|Varaktighet för mål bearbetning i millisekunder|Inga dimensioner|
|DroppedEventCount|Yes|Ignorerade händelser|Antal|Totalt|Totalt antal ignorerade händelser som matchar den här händelse prenumerationen|DropReason|
|MatchedEventCount|Yes|Matchade händelser|Antal|Totalt|Totalt antal händelser som matchar den här händelse prenumerationen|Inga dimensioner|


## <a name="microsofteventgridextensiontopics"></a>Microsoft. EventGrid/extensionTopics

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|PublishFailCount|Yes|Publicera misslyckade händelser|Antal|Totalt|Totalt antal händelser som inte publicerats till det här avsnittet|ErrorType, fel|
|PublishSuccessCount|Yes|Publicerade händelser|Antal|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Inga dimensioner|
|PublishSuccessLatencyInMs|Yes|Slutför svars tid för publicering|Millisekunder|Totalt|Publicera lyckad svars tid i millisekunder|Inga dimensioner|
|UnmatchedEventCount|Yes|Omatchade händelser|Antal|Totalt|Totalt antal händelser som inte matchar någon av händelse prenumerationerna för det här avsnittet|Inga dimensioner|


## <a name="microsofteventgridpartnernamespaces"></a>Microsoft. EventGrid/partnerNamespaces

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Yes|Obeställbara, Brevade händelser|Antal|Totalt|Totalt antal inloggade obeställbara meddelanden som matchar den här händelse prenumerationen|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Misslyckade leverans händelser|Antal|Totalt|Totalt antal händelser som inte levererades till den här händelse prenumerationen|Fel, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Levererade händelser|Antal|Totalt|Totalt antal händelser levererade till den här händelse prenumerationen|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Varaktighet för mål bearbetning|Millisekunder|Genomsnitt|Varaktighet för mål bearbetning i millisekunder|EventSubscriptionName|
|DroppedEventCount|Yes|Ignorerade händelser|Antal|Totalt|Totalt antal ignorerade händelser som matchar den här händelse prenumerationen|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Matchade händelser|Antal|Totalt|Totalt antal händelser som matchar den här händelse prenumerationen|EventSubscriptionName|
|PublishFailCount|Yes|Publicera misslyckade händelser|Antal|Totalt|Totalt antal händelser som inte publicerats till det här avsnittet|ErrorType, fel|
|PublishSuccessCount|Yes|Publicerade händelser|Antal|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Inga dimensioner|
|PublishSuccessLatencyInMs|Yes|Slutför svars tid för publicering|Millisekunder|Totalt|Publicera lyckad svars tid i millisekunder|Inga dimensioner|
|UnmatchedEventCount|Yes|Omatchade händelser|Antal|Totalt|Totalt antal händelser som inte matchar någon av händelse prenumerationerna för det här avsnittet|Inga dimensioner|


## <a name="microsofteventgridpartnertopics"></a>Microsoft. EventGrid/partnerTopics

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Yes|Avancerade filter utvärderingar|Antal|Totalt|Totalt antal avancerade filter som utvärderas över händelse prenumerationer för det här ämnet.|EventSubscriptionName|
|DeadLetteredCount|Yes|Obeställbara, Brevade händelser|Antal|Totalt|Totalt antal inloggade obeställbara meddelanden som matchar den här händelse prenumerationen|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Misslyckade leverans händelser|Antal|Totalt|Totalt antal händelser som inte levererades till den här händelse prenumerationen|Fel, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Levererade händelser|Antal|Totalt|Totalt antal händelser levererade till den här händelse prenumerationen|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Varaktighet för mål bearbetning|Millisekunder|Genomsnitt|Varaktighet för mål bearbetning i millisekunder|EventSubscriptionName|
|DroppedEventCount|Yes|Ignorerade händelser|Antal|Totalt|Totalt antal ignorerade händelser som matchar den här händelse prenumerationen|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Matchade händelser|Antal|Totalt|Totalt antal händelser som matchar den här händelse prenumerationen|EventSubscriptionName|
|PublishFailCount|Yes|Publicera misslyckade händelser|Antal|Totalt|Totalt antal händelser som inte publicerats till det här avsnittet|ErrorType, fel|
|PublishSuccessCount|Yes|Publicerade händelser|Antal|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Inga dimensioner|
|UnmatchedEventCount|Yes|Omatchade händelser|Antal|Totalt|Totalt antal händelser som inte matchar någon av händelse prenumerationerna för det här avsnittet|Inga dimensioner|


## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Yes|Avancerade filter utvärderingar|Antal|Totalt|Totalt antal avancerade filter som utvärderas över händelse prenumerationer för det här ämnet.|EventSubscriptionName|
|DeadLetteredCount|Yes|Obeställbara, Brevade händelser|Antal|Totalt|Totalt antal inloggade obeställbara meddelanden som matchar den här händelse prenumerationen|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Misslyckade leverans händelser|Antal|Totalt|Totalt antal händelser som inte levererades till den här händelse prenumerationen|Fel, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Levererade händelser|Antal|Totalt|Totalt antal händelser levererade till den här händelse prenumerationen|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Varaktighet för mål bearbetning|Millisekunder|Genomsnitt|Varaktighet för mål bearbetning i millisekunder|EventSubscriptionName|
|DroppedEventCount|Yes|Ignorerade händelser|Antal|Totalt|Totalt antal ignorerade händelser som matchar den här händelse prenumerationen|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Matchade händelser|Antal|Totalt|Totalt antal händelser som matchar den här händelse prenumerationen|EventSubscriptionName|
|PublishFailCount|Yes|Publicera misslyckade händelser|Antal|Totalt|Totalt antal händelser som inte publicerats till det här avsnittet|ErrorType, fel|
|PublishSuccessCount|Yes|Publicerade händelser|Antal|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Inga dimensioner|
|PublishSuccessLatencyInMs|Yes|Slutför svars tid för publicering|Millisekunder|Totalt|Publicera lyckad svars tid i millisekunder|Inga dimensioner|
|UnmatchedEventCount|Yes|Omatchade händelser|Antal|Totalt|Totalt antal händelser som inte matchar någon av händelse prenumerationerna för det här avsnittet|Inga dimensioner|


## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/ämnen

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|AdvancedFilterEvaluationCount|Yes|Avancerade filter utvärderingar|Antal|Totalt|Totalt antal avancerade filter som utvärderas över händelse prenumerationer för det här ämnet.|EventSubscriptionName|
|DeadLetteredCount|Yes|Obeställbara, Brevade händelser|Antal|Totalt|Totalt antal inloggade obeställbara meddelanden som matchar den här händelse prenumerationen|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|No|Misslyckade leverans händelser|Antal|Totalt|Totalt antal händelser som inte levererades till den här händelse prenumerationen|Fel, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Yes|Levererade händelser|Antal|Totalt|Totalt antal händelser levererade till den här händelse prenumerationen|EventSubscriptionName|
|DestinationProcessingDurationInMs|No|Varaktighet för mål bearbetning|Millisekunder|Genomsnitt|Varaktighet för mål bearbetning i millisekunder|EventSubscriptionName|
|DroppedEventCount|Yes|Ignorerade händelser|Antal|Totalt|Totalt antal ignorerade händelser som matchar den här händelse prenumerationen|DropReason, EventSubscriptionName|
|MatchedEventCount|Yes|Matchade händelser|Antal|Totalt|Totalt antal händelser som matchar den här händelse prenumerationen|EventSubscriptionName|
|PublishFailCount|Yes|Publicera misslyckade händelser|Antal|Totalt|Totalt antal händelser som inte publicerats till det här avsnittet|ErrorType, fel|
|PublishSuccessCount|Yes|Publicerade händelser|Antal|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Inga dimensioner|
|PublishSuccessLatencyInMs|Yes|Slutför svars tid för publicering|Millisekunder|Totalt|Publicera lyckad svars tid i millisekunder|Inga dimensioner|
|UnmatchedEventCount|Yes|Omatchade händelser|Antal|Totalt|Totalt antal händelser som inte matchar någon av händelse prenumerationerna för det här avsnittet|Inga dimensioner|


## <a name="microsofteventhubclusters"></a>Microsoft. EventHub/Clusters

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ActiveConnections|No|ActiveConnections|Antal|Genomsnitt|Totalt antal aktiva anslutningar för Microsoft. EventHub.||
|AvailableMemory|No|Tillgängligt minne|Procent|Maximal|Tillgängligt minne för Event Hub-klustret som en procent andel av det totala minnet.|Roll|
|CaptureBacklog|No|Samla in efter släpning.|Antal|Totalt|Avbilda efter släpning för Microsoft. EventHub.||
|CapturedBytes|No|Hämtade byte.|Byte|Totalt|Hämtade byte för Microsoft. EventHub.||
|CapturedMessages|No|Fångade meddelanden.|Antal|Totalt|Fångade meddelanden för Microsoft. EventHub.||
|ConnectionsClosed|No|Stängda anslutningar.|Antal|Genomsnitt|Stängda anslutningar för Microsoft. EventHub.||
|ConnectionsOpened|No|Öppna anslutningar.|Antal|Genomsnitt|Anslutningar som har öppnats för Microsoft. EventHub.||
|Processor|No|Processor|Procent|Maximal|CPU-användning för Event Hub-klustret i procent|Roll|
|IncomingBytes|Yes|Inkommande byte.|Byte|Totalt|Inkommande byte för Microsoft. EventHub.||
|IncomingMessages|Yes|Inkommande meddelanden|Antal|Totalt|Inkommande meddelanden för Microsoft. EventHub.||
|IncomingRequests|Yes|Inkommande förfrågningar|Antal|Totalt|Inkommande begär Anden för Microsoft. EventHub.||
|OutgoingBytes|Yes|Utgående byte.|Byte|Totalt|Utgående byte för Microsoft. EventHub.||
|OutgoingMessages|Yes|Utgående meddelanden|Antal|Totalt|Utgående meddelanden för Microsoft. EventHub.||
|QuotaExceededErrors|No|Fel på grund av överskriden kvot.|Antal|Totalt|Kvoten överskred fel för Microsoft. EventHub.|OperationResult under pågående|
|ServerErrors|No|Serverfel.|Antal|Totalt|Server fel för Microsoft. EventHub.|OperationResult under pågående|
|Storlek|No|Storlek|Byte|Genomsnitt|Storlek på en EventHub i byte.|Roll|
|SuccessfulRequests|No|Slutförda förfrågningar|Antal|Totalt|Slutförda begär Anden för Microsoft. EventHub.|OperationResult under pågående|
|ThrottledRequests|No|Begränsade förfrågningar.|Antal|Totalt|Begränsade begär Anden för Microsoft. EventHub.|OperationResult under pågående|
|UserErrors|No|Användarfel.|Antal|Totalt|Användar fel för Microsoft. EventHub.|OperationResult under pågående|


## <a name="microsofteventhubnamespaces"></a>Microsoft. EventHub/Namespaces

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ActiveConnections|No|ActiveConnections|Antal|Genomsnitt|Totalt antal aktiva anslutningar för Microsoft. EventHub.||
|CaptureBacklog|No|Samla in efter släpning.|Antal|Totalt|Avbilda efter släpning för Microsoft. EventHub.|Entitetsnamnet|
|CapturedBytes|No|Hämtade byte.|Byte|Totalt|Hämtade byte för Microsoft. EventHub.|Entitetsnamnet|
|CapturedMessages|No|Fångade meddelanden.|Antal|Totalt|Fångade meddelanden för Microsoft. EventHub.|Entitetsnamnet|
|ConnectionsClosed|No|Stängda anslutningar.|Antal|Genomsnitt|Stängda anslutningar för Microsoft. EventHub.|Entitetsnamnet|
|ConnectionsOpened|No|Öppna anslutningar.|Antal|Genomsnitt|Anslutningar som har öppnats för Microsoft. EventHub.|Entitetsnamnet|
|EHABL|Yes|Arkivera efter släpning meddelanden (inaktuellt)|Antal|Totalt|Event Hub arkivera meddelanden i efter släpning för en namnrymd (inaktuell)||
|EHAMBS|Yes|Arkiv meddelande genom strömning (inaktuellt)|Byte|Totalt|Det arkiverade meddelande flödet i Event Hub i ett namn område (inaktuellt)||
|EHAMSGS|Yes|Arkivera meddelanden (inaktuellt)|Antal|Totalt|Arkiverade meddelanden i Event Hub i ett namn område (inaktuellt)||
|EHINBYTES|Yes|Inkommande byte (inaktuellt)|Byte|Totalt|Inkommande meddelande data flöde för Event Hub för en namnrymd (inaktuell)||
|EHINMBS|Yes|Inkommande byte (inaktuell) (inaktuell)|Byte|Totalt|Inkommande meddelande data flöde för Event Hub för ett namn område. Måttet är föråldrat. Använd måttet inkommande byte i stället (inaktuellt)||
|EHINMSGS|Yes|Inkommande meddelanden (inaktuellt)|Antal|Totalt|Totalt antal inkommande meddelanden för en namnrymd (inaktuell)||
|EHOUTBYTES|Yes|Utgående byte (inaktuellt)|Byte|Totalt|Data flöde för utgående meddelande i Event Hub för en namnrymd (inaktuell)||
|EHOUTMBS|Yes|Utgående byte (inaktuell) (inaktuell)|Byte|Totalt|Data flöde för utgående meddelande i Event Hub för en namnrymd. Måttet är föråldrat. Använd utgående byte-mått i stället (inaktuellt)||
|EHOUTMSGS|Yes|Utgående meddelanden (inaktuellt)|Antal|Totalt|Totalt antal utgående meddelanden för en namnrymd (inaktuell)||
|FAILREQ|Yes|Misslyckade förfrågningar (inaktuellt)|Antal|Totalt|Totalt antal misslyckade begär Anden för en namnrymd (inaktuell)||
|IncomingBytes|Yes|Inkommande byte.|Byte|Totalt|Inkommande byte för Microsoft. EventHub.|Entitetsnamnet|
|IncomingMessages|Yes|Inkommande meddelanden|Antal|Totalt|Inkommande meddelanden för Microsoft. EventHub.|Entitetsnamnet|
|IncomingRequests|Yes|Inkommande förfrågningar|Antal|Totalt|Inkommande begär Anden för Microsoft. EventHub.|Entitetsnamnet|
|INMSGS|Yes|Inkommande meddelanden (inaktuella)|Antal|Totalt|Totalt antal inkommande meddelanden för ett namn område. Måttet är föråldrat. Använd måtten inkommande e-postmeddelanden i stället (inaktuellt)||
|INREQS|Yes|Inkommande begär Anden (inaktuellt)|Antal|Totalt|Totalt antal inkommande sändnings begär Anden för en namnrymd (inaktuell)||
|MELLAN|Yes|Interna Server fel (inaktuellt)|Antal|Totalt|Totalt antal interna Server fel för en namnrymd (inaktuell)||
|MISCERR|Yes|Andra fel (inaktuellt)|Antal|Totalt|Totalt antal misslyckade begär Anden för en namnrymd (inaktuell)||
|OutgoingBytes|Yes|Utgående byte.|Byte|Totalt|Utgående byte för Microsoft. EventHub.|Entitetsnamnet|
|OutgoingMessages|Yes|Utgående meddelanden|Antal|Totalt|Utgående meddelanden för Microsoft. EventHub.|Entitetsnamnet|
|OUTMSGS|Yes|Utgående meddelanden (inaktuella)|Antal|Totalt|Totalt antal utgående meddelanden för ett namn område. Måttet är föråldrat. Använd utgående meddelande mått i stället (inaktuellt)||
|QuotaExceededErrors|No|Fel på grund av överskriden kvot.|Antal|Totalt|Kvoten överskred fel för Microsoft. EventHub.|EntityName, OperationResult under pågående|
|ServerErrors|No|Serverfel.|Antal|Totalt|Server fel för Microsoft. EventHub.|EntityName, OperationResult under pågående|
|Storlek|No|Storlek|Byte|Genomsnitt|Storlek på en EventHub i byte.|Entitetsnamnet|
|SuccessfulRequests|No|Slutförda förfrågningar|Antal|Totalt|Slutförda begär Anden för Microsoft. EventHub.|EntityName, OperationResult under pågående|
|SUCCREQ|Yes|Lyckade förfrågningar (inaktuellt)|Antal|Totalt|Totalt antal slutförda begär Anden för en namnrymd (inaktuell)||
|SVRBSY|Yes|Serverns upptaget fel (inaktuellt)|Antal|Totalt|Totalt antal upptagen server-fel för en namnrymd (inaktuell)||
|ThrottledRequests|No|Begränsade förfrågningar.|Antal|Totalt|Begränsade begär Anden för Microsoft. EventHub.|EntityName, OperationResult under pågående|
|UserErrors|No|Användarfel.|Antal|Totalt|Användar fel för Microsoft. EventHub.|EntityName, OperationResult under pågående|


## <a name="microsofthdinsightclusters"></a>Microsoft. HDInsight/kluster

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|CategorizedGatewayRequests|Yes|Kategoriserade Gateway-begäranden|Antal|Totalt|Antal Gateway-begäranden per kategori (1xx/2xx/3xx/4xx/5xx)|HttpStatus|
|GatewayRequests|Yes|Gateway-begäranden|Antal|Totalt|Antal Gateway-begäranden|HttpStatus|
|KafkaRestProxy.ConsumerRequest.m1_delta|Yes|REST-proxy konsument RequestThroughput|CountPerSecond|Totalt|Antal konsument förfrågningar till Kafka REST proxy|Dator, ämne|
|KafkaRestProxy.ConsumerRequestTime.p95|Yes|REST-proxy konsument RequestLatency|Millisekunder|Genomsnitt|Meddelande fördröjning i en konsument förfrågan via Kafka REST proxy|Dator, ämne|
|KafkaRestProxy.MessagesIn.m1_delta|Yes|REST proxy-tillverkare MessageThroughput|CountPerSecond|Totalt|Antal producerande meddelanden via Kafka REST proxy|Dator, ämne|
|KafkaRestProxy.MessagesOut.m1_delta|Yes|REST-proxy konsument MessageThroughput|CountPerSecond|Totalt|Antal konsument meddelanden via Kafka REST proxy|Dator, ämne|
|KafkaRestProxy. Open-anslutningar|Yes|REST-proxy ConcurrentConnections|Antal|Totalt|Antal samtidiga anslutningar via Kafka REST proxy|Dator, ämne|
|KafkaRestProxy.ProducerRequest.m1_delta|Yes|REST proxy-tillverkare RequestThroughput|CountPerSecond|Totalt|Antal producerande förfrågningar till Kafka REST proxy|Dator, ämne|
|KafkaRestProxy.ProducerRequestTime.p95|Yes|REST proxy-tillverkare RequestLatency|Millisekunder|Genomsnitt|Meddelande fördröjning i en producerande begäran via Kafka REST proxy|Dator, ämne|
|NumActiveWorkers|Yes|Antal aktiva arbetare|Antal|Maximal|Antal aktiva arbetare|MetricName|


## <a name="microsofthealthcareapisservices"></a>Microsoft. HealthcareApis/Services

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Yes|Tillgänglighet|Procent|Genomsnitt|Tillgänglighets takten för tjänsten.|Inga dimensioner|
|CosmosDbCollectionSize|Yes|Storlek på Cosmos DB samling|Byte|Totalt|Storleken på Cosmos DBs samlingen i byte.|Inga dimensioner|
|CosmosDbIndexSize|Yes|Cosmos DB index storlek|Byte|Totalt|Storleken på den säkerhetskopierade Cosmos DB samlingens index i byte.|Inga dimensioner|
|CosmosDbRequestCharge|Yes|Användning av Cosmos DB RU|Antal|Totalt|RU-användningen av begär anden till tjänstens Cosmos DBs säkerhets kopiering.|Åtgärd, ResourceType|
|CosmosDbRequests|Yes|Tjänst Cosmos DB begär Anden|Antal|Sum|Det totala antalet begär Anden som gjorts till tjänstens säkerhets kopiering Cosmos DB.|Åtgärd, ResourceType|
|CosmosDbThrottleRate|Yes|Begränsnings pris för tjänst Cosmos DB|Antal|Sum|Sammanlagt antal 429 svar från en tjänsts Cosmos DBs säkerhets kopiering.|Åtgärd, ResourceType|
|IoTConnectorDeviceEvent|Yes|Antal inkommande meddelanden|Antal|Sum|Sammanlagt antal meddelanden som tagits emot av Azure IoT Connector för FHIR före eventuell normalisering.|Åtgärd, ConnectorName|
|IoTConnectorDeviceEventProcessingLatencyMs|Yes|Genomsnittlig fördröjning för normaliserad fas|Millisekunder|Genomsnitt|Genomsnittlig tid mellan en händelses inmatnings tid och den tid då händelsen bearbetas för normalisering.|Åtgärd, ConnectorName|
|IoTConnectorMeasurement|Yes|Antal mätningar|Antal|Sum|Antalet normaliserade värde läsningar som tagits emot av FHIR Conversion-fasen för Azure IoT Connector för FHIR.|Åtgärd, ConnectorName|
|IoTConnectorMeasurementGroup|Yes|Antal meddelande grupper|Antal|Sum|Det totala antalet unika grupperingar av mått över typ, enhet, patient och konfigurerad tids period som genererats av FHIR-konverterings fasen.|Åtgärd, ConnectorName|
|IoTConnectorMeasurementIngestionLatencyMs|Yes|Genomsnittlig svars tid för grupp fas|Millisekunder|Genomsnitt|Tids perioden mellan när IoT-anslutningen tog emot enhets data och när data bearbetas av FHIR-konverterings fasen.|Åtgärd, ConnectorName|
|IoTConnectorNormalizedEvent|Yes|Antal normaliserade meddelanden|Antal|Sum|Det totala antalet kopplade normaliserade normaliserade värden från normaliserings fasen för Azure IoT-anslutningsprogrammet för FHIR.|Åtgärd, ConnectorName|
|IoTConnectorTotalErrors|Yes|Totalt antal fel|Antal|Sum|Det totala antalet fel som loggats av Azure IoT Connector för FHIR|Namn, åtgärd, ErrorType, ErrorSeverity, ConnectorName|
|ServiceApiErrors|Yes|Tjänst fel|Antal|Sum|Sammanlagt antal interna Server fel som har genererats av tjänsten.|Protokoll, autentisering, åtgärd, ResourceType, StatusCode, StatusCodeClass, StatusCodeText|
|ServiceApiLatency|Yes|Tjänst svars tid|Millisekunder|Genomsnitt|Svars fördröjning för tjänsten.|Protokoll, autentisering, åtgärd, ResourceType, StatusCode, StatusCodeClass, StatusCodeText|
|ServiceApiRequests|Yes|Service Requests|Antal|Sum|Det totala antalet begär Anden som tagits emot av tjänsten.|Protokoll, autentisering, åtgärd, ResourceType, StatusCode, StatusCodeClass, StatusCodeText|
|TotalErrors|Yes|Totalt antal fel|Antal|Sum|Det totala antalet interna Server fel som har påträffats av tjänsten.|Protokoll, StatusCode, StatusCodeClass, StatusCodeText|
|TotalLatency|Yes|Total svars tid|Millisekunder|Genomsnitt|Svars fördröjning för tjänsten.|Protokoll|
|TotalRequests|Yes|Totalt antal förfrågningar|Antal|Sum|Det totala antalet begär Anden som tagits emot av tjänsten.|Protokoll|


## <a name="microsofthybridnetworknetworkfunctions"></a>Microsoft. hybridnetwork/networkfunctions

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|HyperVVirtualProcessorUtilization|Yes|Genomsnittlig processor användning|Procent|Genomsnitt|Total genomsnittlig procent andel av den virtuella processor användningen med en minuters intervall. Det totala antalet virtuella CPU: er baseras på värdet för användar konfigurationen i SKU-definitionen. Ytterligare filter kan appliceras baserat på RoleName som definierats i SKU.|InstanceName|


## <a name="microsofthybridnetworkvirtualnetworkfunctions"></a>Microsoft. hybridnetwork/virtualnetworkfunctions

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|HyperVVirtualProcessorUtilization|Yes|Genomsnittlig processor användning|Procent|Genomsnitt|Total genomsnittlig procent andel av den virtuella processor användningen med en minuters intervall. Det totala antalet virtuella CPU: er baseras på värdet för användar konfigurationen i SKU-definitionen. Ytterligare filter kan appliceras baserat på RoleName som definierats i SKU.|InstanceName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft. Insights/autoscalesettings

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|MetricThreshold|Yes|Mätnings tröskel|Antal|Genomsnitt|Det konfigurerade tröskelvärdet för autoskalning vid autoskalning kördes.|MetricTriggerRule|
|ObservedCapacity|Yes|Observerad kapacitet|Antal|Genomsnitt|Kapaciteten som rapporteras till autoskalning när den kördes.||
|ObservedMetricValue|Yes|Observerat mått värde|Antal|Genomsnitt|Det värde som beräknas genom autoskalning vid körning|MetricTriggerSource|
|ScaleActionsInitiated|Yes|Initierade skalnings åtgärder|Antal|Totalt|Riktningen för skalnings åtgärden.|ScaleDirection|


## <a name="microsoftinsightscomponents"></a>Microsoft. Insights/komponenter

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Yes|Tillgänglighet|Procent|Genomsnitt|Procent slutförda tillgänglighets test har slutförts|availabilityResult/namn, availabilityResult/plats|
|availabilityResults/antal|No|Tillgänglighetstester|Antal|Antal|Antal tillgänglighets test|availabilityResult/namn, availabilityResult/plats, availabilityResult/lyckad|
|availabilityResults/varaktighet|Yes|Tillgänglighets testets varaktighet|Millisekunder|Genomsnitt|Tillgänglighets testets varaktighet|availabilityResult/namn, availabilityResult/plats, availabilityResult/lyckad|
|browserTimings/networkDuration|Yes|Nätverks anslutnings tid för sid inläsning|Millisekunder|Genomsnitt|Tid mellan användar förfrågan och nätverks anslutning. Inkluderar DNS-sökning och transport anslutning.|Inga dimensioner|
|browserTimings/processingDuration|Yes|Klient bearbetnings tid|Millisekunder|Genomsnitt|Tiden mellan att ta emot sista byten i ett dokument tills DOM har lästs in. Asynkrona begär Anden kan fortfarande bearbetas.|Inga dimensioner|
|browserTimings/receiveDuration|Yes|Tar emot svars tid|Millisekunder|Genomsnitt|Tiden mellan den första och sista byten, eller till från koppling.|Inga dimensioner|
|browserTimings/sendDuration|Yes|Tid för att skicka begäran|Millisekunder|Genomsnitt|Tiden mellan nätverks anslutning och mottagande av den första byten.|Inga dimensioner|
|browserTimings/totalDuration|Yes|Sid inläsnings tid för webbläsare|Millisekunder|Genomsnitt|Tid från användar förfrågan tills DOM, formatmallar, skript och bilder har lästs in.|Inga dimensioner|
|beroenden/antal|No|Beroende anrop|Antal|Antal|Antal anrop gjorda av programmet till externa resurser.|beroende/typ, beroende/performanceBucket, beroende/lyckades, beroende/Target, beroende/resultCode, åtgärd/syntetisk, Cloud/roleInstance, Cloud/roleName|
|beroenden/varaktighet|Yes|Beroende varaktighet|Millisekunder|Genomsnitt|Varaktigheten för anrop gjorda av programmet till externa resurser.|beroende/typ, beroende/performanceBucket, beroende/lyckades, beroende/Target, beroende/resultCode, åtgärd/syntetisk, Cloud/roleInstance, Cloud/roleName|
|beroenden/misslyckades|No|Beroende anrops problem|Antal|Antal|Antal misslyckade beroende anrop gjorda av programmet till externa resurser.|beroende/typ, beroende/performanceBucket, beroende/mål, beroende/resultCode, åtgärd/syntetisk, Cloud/roleInstance, Cloud/roleName|
|undantag/webbläsare|No|Webbläsarundantag|Antal|Antal|Antal ej fångade undantag som har utlösts i webbläsaren.|Cloud/roleName|
|undantag/antal|Yes|Undantag|Antal|Antal|Sammanlagt antal undantag som inte har fångats.|Cloud/roleName, Cloud/roleInstance, Client/Type|
|undantag/Server|No|Server undantag|Antal|Antal|Antal ej fångade undantag som har utlösts i serverprogrammet.|Cloud/roleName, Cloud/roleInstance|
|pageViews/antal|Yes|Sid visningar|Antal|Antal|Antal sid visningar.|drift/syntetisk, Cloud/roleName|
|pageViews/varaktighet|Yes|Inläsnings tid för sid visning|Millisekunder|Genomsnitt|Inläsnings tid för sid visning|drift/syntetisk, Cloud/roleName|
|performanceCounters/exceptionsPerSecond|Yes|Undantags frekvens|CountPerSecond|Genomsnitt|Antal hanterade och ohanterade undantag som rapporter ATS till Windows, inklusive .NET-undantag och ohanterade undantag som konverterats till .NET-undantag.|Cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Yes|Tillgängligt minne|Byte|Genomsnitt|Fysiskt minne som är omedelbart tillgängligt för allokering till en process eller för system användning.|Cloud/roleInstance|
|performanceCounters/processCpuPercentage|Yes|Processor-CPU|Procent|Genomsnitt|Procent andelen av förfluten tid som alla process trådar använde processorn för att köra instruktioner. Detta kan variera mellan 0 och 100. Det här måttet anger enbart prestanda för W3wp-processen.|Cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Yes|Process-IO-hastighet|BytesPerSecond|Genomsnitt|Totalt antal byte per sekund som har lästs och skrivits till filer, nätverk och enheter.|Cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Yes|Processor tid|Procent|Genomsnitt|Den procent andel av tiden som processorn ägnat åt icke-inaktiva trådar.|Cloud/roleInstance|
|performanceCounters/processPrivateBytes|Yes|Privata byte för process|Byte|Genomsnitt|Minne som tilldelats exklusivt för de övervakade program processerna.|Cloud/roleInstance|
|performanceCounters/requestExecutionTime|Yes|Körnings tid för HTTP-begäran|Millisekunder|Genomsnitt|Körnings tid för den senaste begäran.|Cloud/roleInstance|
|performanceCounters/requestsInQueue|Yes|HTTP-begäranden i program kön|Antal|Genomsnitt|Längden på program begär ande kön.|Cloud/roleInstance|
|performanceCounters/requestsPerSecond|Yes|Hastighet för HTTP-begäran|CountPerSecond|Genomsnitt|Takten för alla förfrågningar till programmet per sekund från ASP.NET.|Cloud/roleInstance|
|begär Anden/antal|No|Server begär Anden|Antal|Antal|Antal slutförda HTTP-förfrågningar.|Request/performanceBucket, Request/resultCode, operation/syntetisk, Cloud/roleInstance, Request/lyckades, Cloud/roleName|
|begär Anden/varaktighet|Yes|Server svars tid|Millisekunder|Genomsnitt|Tiden mellan att ta emot en HTTP-förfrågan och avsluta sändningen av svaret.|Request/performanceBucket, Request/resultCode, operation/syntetisk, Cloud/roleInstance, Request/lyckades, Cloud/roleName|
|begär Anden/misslyckade|No|Misslyckade förfrågningar|Antal|Antal|Antal HTTP-begäranden som marker ATS som misslyckade. I de flesta fall är dessa förfrågningar med svars kod >= 400 och inte lika med 401.|Request/performanceBucket, Request/resultCode, operation/syntetisk, Cloud/roleInstance, Cloud/roleName|
|begär Anden/pris|No|Server begär ande frekvens|CountPerSecond|Genomsnitt|Antal server begär Anden per sekund|Request/performanceBucket, Request/resultCode, operation/syntetisk, Cloud/roleInstance, Request/lyckades, Cloud/roleName|
|spårning/antal|Yes|Spårningar|Antal|Antal|Spårnings dokument antal|Trace/severityLevel, operation/syntetisk, Cloud/roleName, Cloud/roleInstance|


## <a name="microsoftiotcentraliotapps"></a>Microsoft. IoTCentral/IoTApps

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|C2D. Property. Read. Failure|Yes|Det gick inte att läsa enhets egenskapen från IoT Central|Antal|Totalt|Antal misslyckade egenskaps läsningar som initierats från IoT Central|Inga dimensioner|
|C2D. Property. Read. lyckades|Yes|Lyckad enhets egenskaps läsning från IoT Central|Antal|Totalt|Antalet lyckade egenskaps läsningar som initierats från IoT Central|Inga dimensioner|
|C2D. Property. Update. Failure|Yes|Det gick inte att uppdatera enhets egenskapen från IoT Central|Antal|Totalt|Antalet misslyckade egenskaps uppdateringar som initierats från IoT Central|Inga dimensioner|
|C2D. Property. Update. lyckades|Yes|Lyckade uppdateringar av enhets egenskapen från IoT Central|Antal|Totalt|Antalet lyckade egenskaps uppdateringar som initierats från IoT Central|Inga dimensioner|
|connectedDeviceCount|No|Totalt antal anslutna enheter|Antal|Genomsnitt|Antal enheter som är anslutna till IoT Central|Inga dimensioner|
|D2C. Property. Read. Failure|Yes|Det gick inte att läsa enhets egenskapen från enheterna|Antal|Totalt|Antalet misslyckade egenskaps läsningar som initierats från enheter|Inga dimensioner|
|D2C. Property. Read. lyckades|Yes|Lyckad enhets egenskap läser från enheter|Antal|Totalt|Antalet lyckade egenskaps läsningar som initierats från enheter|Inga dimensioner|
|D2C. Property. Update. Failure|Yes|Misslyckade uppdateringar av enhets egenskapen från enheter|Antal|Totalt|Antalet misslyckade egenskaps uppdateringar som initierats från enheter|Inga dimensioner|
|D2C. Property. Update. lyckades|Yes|Lyckade uppdateringar av enhets egenskapen från enheter|Antal|Totalt|Antalet lyckade egenskaps uppdateringar som initierats från enheter|Inga dimensioner|
|dataexport. fel|Yes|Data export fel|Antal|Totalt|Antal fel som har påträffats för data export|exportId, exportDisplayName, destinationId, destinationDisplayName|
|Export av. meddelanden. filtrerat|Yes|Filtrerade data export meddelanden|Antal|Totalt|Antal meddelanden som har passerat genom filter i data export|exportId, exportDisplayName, destinationId, destinationDisplayName|
|Export av. meddelanden togs emot|Yes|Mottagna data export meddelanden|Antal|Totalt|Antal meddelanden som har inkommande data export till data export, innan filtrering och anrikning av bearbetning|exportId, exportDisplayName, destinationId, destinationDisplayName|
|Export av. meddelanden. skriven|Yes|Skrivna data export meddelanden|Antal|Totalt|Antal meddelanden som skrivits till ett mål|exportId, exportDisplayName, destinationId, destinationDisplayName|


## <a name="microsoftiotspacesgraph"></a>Microsoft. IoTSpaces/Graph

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ApiLatency|No|ApiLatency|6|0|Mäter svars tid för API-begäranden som gjorts till Microsoft. IoTSpaces i millisekunder|Inga dimensioner|
|FunctionExecutionLatency|No|FunctionExecutionLatency|6|0|Mäter svars tid för användardefinierad funktions körning i millisekunder för Microsoft. IoTSpaces|Inga dimensioner|
|MessageEgressFailure|No|MessageEgressFailure|2|3|Söker efter en lokaliserad sträng som liknar mått antal misslyckade i antal för Microsoft. IoTSpaces|Inga dimensioner|
|MessageEgressLatency|No|MessageEgressLatency|6|0|Mäter svars tiden från dispatcher till andra slut punkter i millisekunder för Microsoft. IoTSpaces|Inga dimensioner|
|MessageEgressSuccess|No|MessageEgressSuccess|2|3|Söker efter en lokaliserad sträng som liknar händelsen antal slutförda händelser i antal för Microsoft. IoTSpaces|Inga dimensioner|
|ProcessingLatency|No|ProcessingLatency|6|0|Åtgärds svars tid från meddelande som skickats till sändnings händelse i millisekunder för Microsoft. IoTSpaces|Inga dimensioner|


## <a name="microsoftkeyvaultmanagedhsms"></a>Microsoft. nyckel valv/managedhsms

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|No|Övergripande tjänst tillgänglighet|Procent|Genomsnitt|Tillgänglighet för tjänst begär Anden|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiHit|Yes|Totalt antal tjänst-API-träffar|Antal|Antal|Antal totala service API-träffar|ActivityType, ActivityName|
|ServiceApiLatency|No|Övergripande service API-latens|Millisekunder|Genomsnitt|Övergripande svars tid för service API-begäranden|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Yes|Totalt antal service API-resultat|Antal|Antal|Antal totala service API-resultat|ActivityType, ActivityName, StatusCode, StatusCodeClass|


## <a name="microsoftkeyvaultvaults"></a>Microsoft. nyckel valv/-valv

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Yes|Övergripande valv tillgänglighet|Procent|Genomsnitt|Tillgänglighet för valv begär Anden|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|SaturationShoebox|No|Övergripande valv beläggning|Procent|Genomsnitt|Valv kapacitet som används|ActivityType, ActivityName, TransactionType|
|ServiceApiHit|Yes|Totalt antal tjänst-API-träffar|Antal|Antal|Antal totala service API-träffar|ActivityType, ActivityName|
|ServiceApiLatency|Yes|Övergripande service API-latens|Millisekunder|Genomsnitt|Övergripande svars tid för service API-begäranden|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Yes|Totalt antal service API-resultat|Antal|Antal|Antal totala service API-resultat|ActivityType, ActivityName, StatusCode, StatusCodeClass|


## <a name="microsoftkubernetesconnectedclusters"></a>Microsoft. Kubernetes/connectedClusters

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|capacity_cpu_cores|Yes|Totalt antal processor kärnor i ett anslutet kluster|Antal|Totalt|Totalt antal processor kärnor i ett anslutet kluster||


## <a name="microsoftkustoclusters"></a>Microsoft. Kusto/kluster

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|BatchBlobCount|Yes|Batch-BLOB-antal|Antal|Genomsnitt|Antalet data källor i en aggregerad batch för inmatning.|Databas|
|BatchDuration|Yes|Batch-varaktighet|Sekunder|Genomsnitt|Varaktigheten för agg regerings fasen i inmatnings flödet.|Databas|
|BatchesProcessed|Yes|Bearbetade batchar|Antal|Totalt|Antal sammanställda batchar för inmatning. Typ av batch: anger om batchen har nått sin gräns för batchbearbetning, data storlek eller antal filer som angetts av batch-principen|Databas, SealReason|
|BatchSize|Yes|Batchstorlek|Byte|Genomsnitt|Okomprimerad förväntad data storlek i en aggregerad batch för inmatning.|Databas|
|BlobsDropped|Yes|Avbrutna blobbar|Antal|Totalt|Antalet blobbar som har avvisats permanent av en komponent.|Databas, ComponentType, ComponentName|
|BlobsProcessed|Yes|Bearbetade blobbar|Antal|Totalt|Antal blobbar som bearbetats av en komponent.|Databas, ComponentType, ComponentName|
|BlobsReceived|Yes|Mottagna blobbar|Antal|Totalt|Antal blobbar som tagits emot från indataströmmen med en komponent.|Databas, ComponentType, ComponentName|
|CacheUtilization|Yes|Användning av cache|Procent|Genomsnitt|Användnings nivå i kluster omfånget|Inga dimensioner|
|ContinuousExportMaxLatenessMinutes|Yes|Kontinuerlig export, maximal försening|Antal|Maximal|Försenad (i minuter) som rapporteras av de kontinuerliga export jobben i klustret|Inga dimensioner|
|ContinuousExportNumOfRecordsExported|Yes|Kontinuerlig export – antal exporterade poster|Antal|Totalt|Antal exporterade poster, utlöst för varje lagrings artefakt som skrivits under export åtgärden|ContinuousExportName, databas|
|ContinuousExportPendingCount|Yes|Antal väntande pågående export|Antal|Maximal|Antal väntande kontinuerliga export jobb som är klara för körning|Inga dimensioner|
|ContinuousExportResult|Yes|Resultat av kontinuerlig export|Antal|Antal|Anger om kontinuerlig export lyckades eller misslyckades|ContinuousExportName, resultat, databas|
|Processor|Yes|Processor|Procent|Genomsnitt|PROCESSOR användnings nivå|Inga dimensioner|
|DiscoveryLatency|Yes|Identifierings svars tid|Sekunder|Genomsnitt|Rapporteras av data anslutningar (om de finns). Tiden i sekunder från när ett meddelande är i kö eller om en händelse skapas tills den identifieras av data anslutningen. Den här tiden ingår inte i Azure Datautforskaren total inläsnings varaktighet.|ComponentType, ComponentName|
|EventsDropped|Yes|Ignorerade händelser|Antal|Totalt|Antalet händelser som tagits bort permanent av data anslutningen. Ett inmatnings resultat mått med en felorsak kommer att skickas.|ComponentType, ComponentName|
|EventsProcessed|Yes|Bearbetade händelser|Antal|Totalt|Antal händelser som bearbetas av klustret|ComponentType, ComponentName|
|EventsProcessedForEventHubs|Yes|Bearbetade händelser (för Event/IoT-hubbar)|Antal|Totalt|Antal händelser som bearbetas av klustret vid inmatning från händelse/IoT Hub|EventStatus|
|EventsReceived|Yes|Mottagna händelser|Antal|Totalt|Antalet händelser som tagits emot av data anslutningen.|ComponentType, ComponentName|
|ExportUtilization|Yes|Exportanvändning|Procent|Maximal|Exportera användning|Inga dimensioner|
|IngestionLatencyInSeconds|Yes|Datainmatningssvarstid|Sekunder|Genomsnitt|Svarstiden för inmatade data, från tiden då data togs emot i klustret tills att det går att köra frågor mot dem. Datainmatningssvarstiden beror på inmatningsscenariot.|Inga dimensioner|
|IngestionResult|Yes|Inmatnings resultat|Antal|Totalt|Antal inmatnings åtgärder|IngestionResultDetails|
|IngestionUtilization|Yes|Datainmatningsanvändning|Procent|Genomsnitt|Förhållandet mellan använda inmatnings platser i klustret|Inga dimensioner|
|IngestionVolumeInMB|Yes|Datainmatningsvolym|Byte|Totalt|Total mängd inmatade data till klustret|Databas|
|InstanceCount|Yes|Antal instanser|Antal|Genomsnitt|Totalt antal instanser|Inga dimensioner|
|KeepAlive|Yes|Behåll Alive|Antal|Genomsnitt|Sanity check anger att klustret svarar på frågor|Inga dimensioner|
|MaterializedViewAgeMinutes|Yes|Materialiserad vy-ålder|Antal|Genomsnitt|Den materialiserade vyns ålder på några minuter|Databas, MaterializedViewName|
|MaterializedViewDataLoss|Yes|Materialiserad visnings data förlust|Antal|Maximal|Indikerar potentiell data förlust i materialiserad vy|Databas, MaterializedViewName, sort|
|MaterializedViewExtentsRebuild|Yes|Återuppbyggnad av materialiserade visnings omfång|Antal|Genomsnitt|Återskapa antalet omfattningar|Databas, MaterializedViewName|
|MaterializedViewHealth|Yes|Materialiserad visnings hälsa|Antal|Genomsnitt|Hälso tillståndet för den materialiserade vyn (1 för felfri, 0 för icke-felfri)|Databas, MaterializedViewName|
|MaterializedViewRecordsInDelta|Yes|Materialiserade visnings poster i delta|Antal|Genomsnitt|Antalet poster i den icke-materialiserade delen av vyn|Databas, MaterializedViewName|
|MaterializedViewResult|Yes|Resultat av materialiserad vy|Antal|Genomsnitt|Resultatet av processen materialization|Databas, MaterializedViewName, resultat|
|QueryDuration|Yes|Frågans varaktighet|Millisekunder|Genomsnitt|Frågornas varaktighet i sekunder|QueryStatus|
|QueryResult|No|Frågeresultat|Antal|Antal|Totalt antal frågor.|QueryStatus|
|QueueLength|Yes|Kölängd|Antal|Genomsnitt|Antal väntande meddelanden i en komponents kö.|ComponentType|
|QueueOldestMessage|Yes|Köa äldsta meddelande|Antal|Genomsnitt|Tid i sekunder från det att det äldsta meddelandet i kön infogades.|ComponentType|
|ReceivedDataSizeBytes|Yes|Mottagna byte för data storlek|Byte|Genomsnitt|Storlek på data som tagits emot av data anslutning. Detta är storleken på data strömmen eller rå data storlek om det finns.|ComponentType, ComponentName|
|StageLatency|Yes|Fas svars tid|Sekunder|Genomsnitt|Ackumulerad tid från när ett meddelande identifieras tills det tas emot av rapporterings komponenten för bearbetning (identifierings tiden anges när ett meddelande har placerats i kö för inmatnings kön eller när det upptäcks av data anslutningen).|Databas, ComponentType|
|SteamingIngestRequestRate|Yes|Förfrågningsfrekvens för strömningsinmatning|Antal|RateRequestsPerSecond|Hastighet för strömnings inmatnings begäran (begär Anden per sekund)|Inga dimensioner|
|StreamingIngestDataRate|Yes|Datahastighet för strömningsinmatning|Antal|Genomsnitt|Strömnings data hastighet (MB per sekund)|Inga dimensioner|
|StreamingIngestDuration|Yes|Varaktighet för strömningsinmatning|Millisekunder|Genomsnitt|Hämtnings tid för strömning i millisekunder|Inga dimensioner|
|StreamingIngestResults|Yes|Resultat för strömningsinmatning|Antal|Genomsnitt|Resultat av strömnings inmatning|Resultat|
|TotalNumberOfConcurrentQueries|Yes|Totalt antal samtidiga frågor|Antal|Maximal|Totalt antal samtidiga frågor|Inga dimensioner|
|TotalNumberOfExtents|Yes|Totalt antal omfattningar|Antal|Totalt|Totalt antal data omfattningar|Inga dimensioner|
|TotalNumberOfThrottledCommands|Yes|Totalt antal begränsade kommandon|Antal|Totalt|Totalt antal begränsade kommandon|CommandType|
|TotalNumberOfThrottledQueries|Yes|Totalt antal begränsade frågor|Antal|Maximal|Totalt antal begränsade frågor|Inga dimensioner|


## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft. Logic/integrationServiceEnvironments

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ActionLatency|Yes|Åtgärds svars tid |Sekunder|Genomsnitt|Svars tid för slutförda arbets flödes åtgärder.|Inga dimensioner|
|ActionsCompleted|Yes|Slutförda åtgärder |Antal|Totalt|Antal slutförda arbets flödes åtgärder.|Inga dimensioner|
|ActionsFailed|Yes|Misslyckade åtgärder |Antal|Totalt|Antalet misslyckade arbets flödes åtgärder.|Inga dimensioner|
|ActionsSkipped|Yes|Åtgärder hoppades över |Antal|Totalt|Antal överhoppade arbets flödes åtgärder.|Inga dimensioner|
|ActionsStarted|Yes|Startade åtgärder |Antal|Totalt|Antal startade arbets flödes åtgärder.|Inga dimensioner|
|ActionsSucceeded|Yes|Genomförda åtgärder |Antal|Totalt|Antal slutförda arbets flödes åtgärder.|Inga dimensioner|
|ActionSuccessLatency|Yes|Svars tid för åtgärd |Sekunder|Genomsnitt|Svars tid för slutförda arbets flödes åtgärder.|Inga dimensioner|
|ActionThrottledEvents|Yes|Åtgärds begränsade händelser|Antal|Totalt|Antal begränsade händelser för arbets flödes åtgärd..|Inga dimensioner|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Yes|Anslutnings minnes användning för Integration Service Environment|Procent|Genomsnitt|Anslutnings minnes användning för integrerings tjänst miljön.|Inga dimensioner|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Yes|Anslutnings processor användning för Integration Service Environment|Procent|Genomsnitt|Anslutnings processor användning för integrerings tjänst miljön.|Inga dimensioner|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Yes|Minnes användning för arbets flöde för Integration Service Environment|Procent|Genomsnitt|Arbets flödets minnes användning för integrerings tjänst miljön.|Inga dimensioner|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Yes|Användning av arbets flödes processor för Integration Service Environment|Procent|Genomsnitt|Användning av arbets flödes processor för integrerings tjänst miljö.|Inga dimensioner|
|RunFailurePercentage|Yes|Procent körnings avbrott|Procent|Totalt|Procent andel av arbets flödes körningen misslyckades.|Inga dimensioner|
|RunLatency|Yes|Körnings fördröjning|Sekunder|Genomsnitt|Svars tiden för slutförda arbets flödes körningar.|Inga dimensioner|
|RunsCancelled|Yes|Avbrutna körningar|Antal|Totalt|Antal avbrutna arbets flödes körningar.|Inga dimensioner|
|RunsCompleted|Yes|Slutförda körningar|Antal|Totalt|Antal slutförda arbets flödes körningar.|Inga dimensioner|
|RunsFailed|Yes|Misslyckade körningar|Antal|Totalt|Antal misslyckade arbets flödes körningar.|Inga dimensioner|
|RunsStarted|Yes|Startade körningar|Antal|Totalt|Antal startade arbets flödes körningar.|Inga dimensioner|
|RunsSucceeded|Yes|Lyckade körningar|Antal|Totalt|Antal slutförda arbets flödes körningar.|Inga dimensioner|
|RunStartThrottledEvents|Yes|Kör starta begränsade händelser|Antal|Totalt|Antal händelser som startar begränsning av arbets flödes körning.|Inga dimensioner|
|RunSuccessLatency|Yes|Kör svars tid|Sekunder|Genomsnitt|Svars tiden för slutfört arbets flöde körs.|Inga dimensioner|
|RunThrottledEvents|Yes|Kör begränsade händelser|Antal|Totalt|Antal arbets flödes åtgärder eller utlösa begränsade händelser.|Inga dimensioner|
|TriggerFireLatency|Yes|Utlös brand fördröjning |Sekunder|Genomsnitt|Fördröjning för utlösta arbets flödes utlösare.|Inga dimensioner|
|TriggerLatency|Yes|Utlös fördröjning |Sekunder|Genomsnitt|Latens för slutförda arbets flödes utlösare.|Inga dimensioner|
|TriggersCompleted|Yes|Slutförda utlösare |Antal|Totalt|Antal slutförda arbets flödes utlösare.|Inga dimensioner|
|TriggersFailed|Yes|Misslyckade utlösare |Antal|Totalt|Antalet misslyckade arbets flödes utlösare.|Inga dimensioner|
|TriggersFired|Yes|Utlöst utlösare |Antal|Totalt|Antal utlösta arbets flödes utlösare.|Inga dimensioner|
|TriggersSkipped|Yes|Ignorerade utlösare|Antal|Totalt|Antal överhoppade arbets flödes utlösare.|Inga dimensioner|
|TriggersStarted|Yes|Startade utlösare |Antal|Totalt|Antal startade arbets flödes utlösare.|Inga dimensioner|
|TriggersSucceeded|Yes|Lyckade utlösare |Antal|Totalt|Antal slutförda arbets flödes utlösare.|Inga dimensioner|
|TriggerSuccessLatency|Yes|Utlös svars tid för lyckade |Sekunder|Genomsnitt|Svars tid för lyckade arbets flödes utlösare.|Inga dimensioner|
|TriggerThrottledEvents|Yes|Utlös begränsade händelser|Antal|Totalt|Antal begränsade händelser för arbets flödes utlösare.|Inga dimensioner|


## <a name="microsoftlogicworkflows"></a>Microsoft. Logic/arbets flöden

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ActionLatency|Yes|Åtgärds svars tid |Sekunder|Genomsnitt|Svars tid för slutförda arbets flödes åtgärder.|Inga dimensioner|
|ActionsCompleted|Yes|Slutförda åtgärder |Antal|Totalt|Antal slutförda arbets flödes åtgärder.|Inga dimensioner|
|ActionsFailed|Yes|Misslyckade åtgärder |Antal|Totalt|Antalet misslyckade arbets flödes åtgärder.|Inga dimensioner|
|ActionsSkipped|Yes|Åtgärder hoppades över |Antal|Totalt|Antal överhoppade arbets flödes åtgärder.|Inga dimensioner|
|ActionsStarted|Yes|Startade åtgärder |Antal|Totalt|Antal startade arbets flödes åtgärder.|Inga dimensioner|
|ActionsSucceeded|Yes|Genomförda åtgärder |Antal|Totalt|Antal slutförda arbets flödes åtgärder.|Inga dimensioner|
|ActionSuccessLatency|Yes|Svars tid för åtgärd |Sekunder|Genomsnitt|Svars tid för slutförda arbets flödes åtgärder.|Inga dimensioner|
|ActionThrottledEvents|Yes|Åtgärds begränsade händelser|Antal|Totalt|Antal begränsade händelser för arbets flödes åtgärd..|Inga dimensioner|
|BillableActionExecutions|Yes|Fakturerbara åtgärds körningar|Antal|Totalt|Antal körningar av arbets flödes åtgärder som faktureras.|Inga dimensioner|
|BillableTriggerExecutions|Yes|Fakturerbara Utlösar-körningar|Antal|Totalt|Antal körningar av arbets flödes utlösare som faktureras.|Inga dimensioner|
|BillingUsageNativeOperation|Yes|Fakturerings användning för intern åtgärds körningar|Antal|Totalt|Antal körningar av interna åtgärder som debiteras.|Inga dimensioner|
|BillingUsageStandardConnector|Yes|Fakturerings användning för standard kopplings körningar|Antal|Totalt|Antal standard kopplings körningar som faktureras.|Inga dimensioner|
|BillingUsageStorageConsumption|Yes|Fakturerings användning för lagrings förbruknings körningar|Antal|Totalt|Antal lagrings förbruknings körningar som faktureras.|Inga dimensioner|
|RunFailurePercentage|Yes|Procent körnings avbrott|Procent|Totalt|Procent andel av arbets flödes körningen misslyckades.|Inga dimensioner|
|RunLatency|Yes|Körnings fördröjning|Sekunder|Genomsnitt|Svars tiden för slutförda arbets flödes körningar.|Inga dimensioner|
|RunsCancelled|Yes|Avbrutna körningar|Antal|Totalt|Antal avbrutna arbets flödes körningar.|Inga dimensioner|
|RunsCompleted|Yes|Slutförda körningar|Antal|Totalt|Antal slutförda arbets flödes körningar.|Inga dimensioner|
|RunsFailed|Yes|Misslyckade körningar|Antal|Totalt|Antal misslyckade arbets flödes körningar.|Inga dimensioner|
|RunsStarted|Yes|Startade körningar|Antal|Totalt|Antal startade arbets flödes körningar.|Inga dimensioner|
|RunsSucceeded|Yes|Lyckade körningar|Antal|Totalt|Antal slutförda arbets flödes körningar.|Inga dimensioner|
|RunStartThrottledEvents|Yes|Kör starta begränsade händelser|Antal|Totalt|Antal händelser som startar begränsning av arbets flödes körning.|Inga dimensioner|
|RunSuccessLatency|Yes|Kör svars tid|Sekunder|Genomsnitt|Svars tiden för slutfört arbets flöde körs.|Inga dimensioner|
|RunThrottledEvents|Yes|Kör begränsade händelser|Antal|Totalt|Antal arbets flödes åtgärder eller utlösa begränsade händelser.|Inga dimensioner|
|TotalBillableExecutions|Yes|Totalt antal fakturerbara körningar|Antal|Totalt|Antal arbets flödes körningar som faktureras.|Inga dimensioner|
|TriggerFireLatency|Yes|Utlös brand fördröjning |Sekunder|Genomsnitt|Fördröjning för utlösta arbets flödes utlösare.|Inga dimensioner|
|TriggerLatency|Yes|Utlös fördröjning |Sekunder|Genomsnitt|Latens för slutförda arbets flödes utlösare.|Inga dimensioner|
|TriggersCompleted|Yes|Slutförda utlösare |Antal|Totalt|Antal slutförda arbets flödes utlösare.|Inga dimensioner|
|TriggersFailed|Yes|Misslyckade utlösare |Antal|Totalt|Antalet misslyckade arbets flödes utlösare.|Inga dimensioner|
|TriggersFired|Yes|Utlöst utlösare |Antal|Totalt|Antal utlösta arbets flödes utlösare.|Inga dimensioner|
|TriggersSkipped|Yes|Ignorerade utlösare|Antal|Totalt|Antal överhoppade arbets flödes utlösare.|Inga dimensioner|
|TriggersStarted|Yes|Startade utlösare |Antal|Totalt|Antal startade arbets flödes utlösare.|Inga dimensioner|
|TriggersSucceeded|Yes|Lyckade utlösare |Antal|Totalt|Antal slutförda arbets flödes utlösare.|Inga dimensioner|
|TriggerSuccessLatency|Yes|Utlös svars tid för lyckade |Sekunder|Genomsnitt|Svars tid för lyckade arbets flödes utlösare.|Inga dimensioner|
|TriggerThrottledEvents|Yes|Utlös begränsade händelser|Antal|Totalt|Antal begränsade händelser för arbets flödes utlösare.|Inga dimensioner|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft. MachineLearningServices/arbets ytor

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Aktiva kärnor|Yes|Aktiva kärnor|Antal|Genomsnitt|Antal aktiva kärnor|Scenario, kluster namn|
|Aktiva noder|Yes|Aktiva noder|Antal|Genomsnitt|Antal Active-noder. Detta är de noder som aktivt kör ett jobb.|Scenario, kluster namn|
|Avbryt begärda körningar|Yes|Avbryt begärda körningar|Antal|Totalt|Antal körningar där Cancel begärdes för den här arbets ytan. Antalet uppdateras när en avbrottsbegäran tas emot för en körning.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Avbrutna körningar|Yes|Avbrutna körningar|Antal|Totalt|Antalet körningar som avbrutits för den här arbets ytan. Antalet uppdateras när en körning har avbrutits.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Slutförda körningar|Yes|Slutförda körningar|Antal|Totalt|Antalet körningar har slutförts för den här arbets ytan. Antalet uppdateras när en körning har slutförts och utdata har samlats in.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|CpuUtilization|Yes|CpuUtilization|Antal|Genomsnitt|Procent andel användning på en processor nod. Användningen rapporteras med ett minuters intervall.|Scenario, runId, NodeId, kluster namn|
|Fel|Yes|Fel|Antal|Totalt|Antal körnings fel i den här arbets ytan. Antalet uppdateras när körningen påträffar ett fel.|Scenario|
|Misslyckade körningar|Yes|Misslyckade körningar|Antal|Totalt|Antalet körningar som misslyckades för den här arbets ytan. Antalet uppdateras när en körning Miss lyckas.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Slutför körningar|Yes|Slutför körningar|Antal|Totalt|Antal körningar som har angetts i slutfört tillstånd för den här arbets ytan. Antalet uppdateras när en körning har slutförts men insamlingen fortfarande pågår.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|GpuMemoryUtilization|Yes|GpuMemoryUtilization|Antal|Genomsnitt|Procent andel minnes användning på en GPU-nod. Användningen rapporteras med ett minuters intervall.|Scenario, runId, NodeId, DeviceId, kluster namn|
|GpuUtilization|Yes|GpuUtilization|Antal|Genomsnitt|Procent andel användning på en GPU-nod. Användningen rapporteras med ett minuters intervall.|Scenario, runId, NodeId, DeviceId, kluster namn|
|Inaktiva kärnor|Yes|Inaktiva kärnor|Antal|Genomsnitt|Antal inaktiva kärnor|Scenario, kluster namn|
|Inaktiva noder|Yes|Inaktiva noder|Antal|Genomsnitt|Antal inaktiva noder. Inaktiva noder är noder som inte kör några jobb, men som kan ta emot nya jobb om de är tillgängliga.|Scenario, kluster namn|
|Lämnar kärnor|Yes|Lämnar kärnor|Antal|Genomsnitt|Antal lämnar kärnor|Scenario, kluster namn|
|Lämnar noder|Yes|Lämnar noder|Antal|Genomsnitt|Antal noder som lämnar. Att lämna noder är de noder som precis har avslutat bearbetningen av ett jobb och kommer att gå in i inaktivt läge.|Scenario, kluster namn|
|Modelldistribution misslyckades|Yes|Modelldistribution misslyckades|Antal|Totalt|Antal modell distributioner som misslyckades på den här arbets ytan|Scenario, StatusCode|
|Modelldistribution Startad|Yes|Modelldistribution Startad|Antal|Totalt|Antal modell distributioner som startats på den här arbets ytan|Scenario|
|Modelldistribution lyckades|Yes|Modelldistribution lyckades|Antal|Totalt|Antal modell distributioner som har slutförts på den här arbets ytan|Scenario|
|Modell registreringen misslyckades|Yes|Modell registreringen misslyckades|Antal|Totalt|Antal modell registreringar som misslyckades på den här arbets ytan|Scenario, StatusCode|
|Modell registreringen har slutförts|Yes|Modell registreringen har slutförts|Antal|Totalt|Antal modell registreringar som har slutförts på den här arbets ytan|Scenario|
|Svarar inte körningar|Yes|Svarar inte körningar|Antal|Totalt|Antal körningar som inte svarar på den här arbets ytan. Antalet uppdateras när en körning inte övergår i tillstånd.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Inte startade körningar|Yes|Inte startade körningar|Antal|Totalt|Antal körningar i inte start läge för den här arbets ytan. Antalet uppdateras när en begäran tas emot för att skapa en körning men körnings information ännu inte har fyllts i. |Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Blockerade kärnor|Yes|Blockerade kärnor|Antal|Genomsnitt|Antal blockerade kärnor|Scenario, kluster namn|
|Misslyckade noder|Yes|Misslyckade noder|Antal|Genomsnitt|Antal noder som har åsidosatts. De här noderna är de noder med låg prioritet som tas bort från den tillgängliga Node-poolen.|Scenario, kluster namn|
|Förbereder körningar|Yes|Förbereder körningar|Antal|Totalt|Antal körningar som förbereds för den här arbets ytan. Antalet uppdateras när en körning går in i förberedelse tillstånd medan körnings miljön förbereds.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Etablerings körningar|Yes|Etablerings körningar|Antal|Totalt|Antal körningar som håller på att etablering för den här arbets ytan. Antalet uppdateras när en körning väntar på skapande eller etablering av beräknings mål.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Köade körningar|Yes|Köade körningar|Antal|Totalt|Antal körningar som har placerats i kö för den här arbets ytan. Antalet uppdateras när en körning placeras i kö i beräknings mål. Kan inträffa när nödvändiga datornoder ska vara klara.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Kvot användning i procent|Yes|Kvot användning i procent|Antal|Genomsnitt|Procent av kvoten som används|Scenario, kluster namn, VmFamilyName, VmPriority|
|Startade körningar|Yes|Startade körningar|Antal|Totalt|Antal körningar som körs för den här arbets ytan. Antalet uppdateras när körningen börjar köras på nödvändiga resurser.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Startar körningar|Yes|Startar körningar|Antal|Totalt|Antal körningar som har startats för den här arbets ytan. Antalet uppdateras när begäran om att skapa körnings-och körnings information, till exempel körnings-ID, har fyllts i|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Totalt antal kärnor|Yes|Totalt antal kärnor|Antal|Genomsnitt|Antal total kärnor|Scenario, kluster namn|
|Totalt antal noder|Yes|Totalt antal noder|Antal|Genomsnitt|Totalt antal noder. Den här summan innehåller några aktiva noder, inaktiva noder, oanvändbara noder, Premepted noder, lämnar noder|Scenario, kluster namn|
|Oanvändbara kärnor|Yes|Oanvändbara kärnor|Antal|Genomsnitt|Antal oanvändbara kärnor|Scenario, kluster namn|
|Oanvändbara noder|Yes|Oanvändbara noder|Antal|Genomsnitt|Antal oanvändbara noder. Oanvändbara noder fungerar inte på grund av ett problem som inte kan matchas. De här noderna återanvänds av Azure.|Scenario, kluster namn|
|Varningar|Yes|Varningar|Antal|Totalt|Antal körnings varningar i den här arbets ytan. Antalet uppdateras när en körning påträffar en varning.|Scenario|


## <a name="microsoftmapsaccounts"></a>Microsoft. Maps/konton

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Yes|Tillgänglighet|Procent|Genomsnitt|Tillgänglighet för API: erna|ApiCategory, ApiName|
|Användning|No|Användning|Antal|Antal|Antal API-anrop|ApiCategory, ApiName, ResultType, ResponseCode|


## <a name="microsoftmediamediaservices"></a>Microsoft. Media/Media Services

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|AssetCount|Yes|Antal till gångar|Antal|Genomsnitt|Hur många till gångar som redan har skapats i det aktuella medie tjänst kontot|Inga dimensioner|
|AssetQuota|Yes|Till gångs kvot|Antal|Genomsnitt|Hur många till gångar som tillåts för det aktuella Media Service-kontot|Inga dimensioner|
|AssetQuotaUsedPercentage|Yes|Använd procent andel till till gångs kvot|Procent|Genomsnitt|Procent andel till gång som används i det aktuella medie tjänst kontot|Inga dimensioner|
|ChannelsAndLiveEventsCount|Yes|Antal real tids händelser|Antal|Genomsnitt|Det totala antalet aktiva händelser i det aktuella Media Services-kontot|Inga dimensioner|
|ContentKeyPolicyCount|Yes|Antal nyckel principer för innehåll|Antal|Genomsnitt|Hur många innehålls nyckel principer som redan har skapats i det aktuella medie tjänst kontot|Inga dimensioner|
|ContentKeyPolicyQuota|Yes|Kvot för innehålls nyckel princip|Antal|Genomsnitt|Hur många innehålls nyckel principer som tillåts för det aktuella Media Service-kontot|Inga dimensioner|
|ContentKeyPolicyQuotaUsedPercentage|Yes|Procent andel av nyckel princip för innehåll|Procent|Genomsnitt|Procent andel av nyckel princip för innehåll i det aktuella medie tjänst kontot|Inga dimensioner|
|RunningChannelsAndLiveEventsCount|Yes|Antal pågående Live-händelser|Antal|Genomsnitt|Det totala antalet aktiva Live-händelser i det aktuella Media Services-kontot|Inga dimensioner|
|StreamingPolicyCount|Yes|Antal strömmande principer|Antal|Genomsnitt|Hur många strömmande principer som redan har skapats i det aktuella medie tjänst kontot|Inga dimensioner|
|StreamingPolicyQuota|Yes|Kvot för strömmande princip|Antal|Genomsnitt|Hur många strömmande principer som tillåts för det aktuella Media Service-kontot|Inga dimensioner|
|StreamingPolicyQuotaUsedPercentage|Yes|Använd procent andel av princip för strömning|Procent|Genomsnitt|Procentuell strömnings princip som används i det aktuella medie tjänst kontot|Inga dimensioner|


## <a name="microsoftmediamediaservicesliveevents"></a>Microsoft. Media/Media Services/liveEvents

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|IngestBitrate|Yes|Bit hastighet för live event-inmatning|BitsPerSecond|Genomsnitt|Inkommande bit hastighet matas in för en Live-händelse, i bitar per sekund.|TrackName|
|IngestDriftValue|Yes|TTL-värde för live event-intag|Sekunder|Maximal|Gå mellan tidsstämpeln för det inmatade innehållet och system klockan, mätt i sekunder per minut. Ett värde som inte är noll indikerar att det inmatade innehållet är långsammare än tiden för system klockan.|TrackName|
|IngestLastTimestamp|Yes|Live Event-inhämtning senaste tidsstämpel|Millisekunder|Maximal|Senaste tidsstämpeln har matats in för en Live-händelse.|TrackName|
|LiveOutputLastTimestamp|Yes|Tidsstämpel för senaste utdata|Millisekunder|Maximal|Tidsstämpel för det sista fragment som laddats upp till lagring för en Live-händelse.|TrackName|


## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft. Media/Media Services/strömnings slut punkter

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Processor|Yes|CPU-användning|Procent|Genomsnitt|CPU-användning för förstklassiga strömnings slut punkter. Dessa data är inte tillgängliga för standard slut punkter för direkt uppspelning.|VmId|
|Utgående|Yes|Utgående|Byte|Totalt|Mängden utgående data, i byte.|OutputFormat|
|EgressBandwidth|No|Utgående bandbredd|BitsPerSecond|Genomsnitt|Utgående bandbredd i bitar per sekund.|VmId|
|Begäranden|Yes|Begäranden|Antal|Totalt|Begär anden till en slut punkt för direkt uppspelning.|OutputFormat, HttpStatusCode, ErrorCode|
|SuccessE2ELatency|Yes|Slutför svars tid för slut punkt till slut punkt|Millisekunder|Genomsnitt|Genomsnittlig svars tid för lyckade begär anden i millisekunder.|OutputFormat|


## <a name="microsoftmixedrealityremoterenderingaccounts"></a>Microsoft. MixedReality/remoteRenderingAccounts

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ActiveRenderingSessions|Yes|Aktiva åter givnings sessioner|Antal|Genomsnitt|Totalt antal aktiva åter givnings sessioner|SessionType, SDKVersion|
|AssetsConverted|Yes|Konverterade till gångar|Antal|Totalt|Totalt antal konverterade till gångar|SDKVersion|


## <a name="microsoftmixedrealityspatialanchorsaccounts"></a>Microsoft. MixedReality/spatialAnchorsAccounts

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|AnchorsCreated|Yes|Skapade ankare|Antal|Totalt|Antal skapade ankare|DeviceFamily, SDKVersion|
|AnchorsDeleted|Yes|Ankare borttagna|Antal|Totalt|Antal borttagna ankare|DeviceFamily, SDKVersion|
|AnchorsQueried|Yes|Efterfrågade ankare|Antal|Totalt|Antal efterfrågade ankare|DeviceFamily, SDKVersion|
|AnchorsUpdated|Yes|Ankare uppdaterade|Antal|Totalt|Antal uppdaterade ankare|DeviceFamily, SDKVersion|
|PosesFound|Yes|Hittade|Antal|Totalt|Antal returnerade attityder|DeviceFamily, SDKVersion|
|TotalDailyAnchors|Yes|Totalt antal dagliga ankare|Antal|Genomsnitt|Totalt antal ankare – varje dag|DeviceFamily, SDKVersion|


## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft. NetApp/netAppAccounts/capacityPools

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Yes|Allokerad storlek för pool|Byte|Genomsnitt|Allokerad storlek för poolen|Inga dimensioner|
|VolumePoolAllocatedToVolumeThroughput|Yes|Allokerat data flöde för pool|BytesPerSecond|Genomsnitt|Summan av data flödet för alla volymer som tillhör poolen|Inga dimensioner|
|VolumePoolAllocatedUsed|Yes|Pool allokerad till volym storlek|Byte|Genomsnitt|Allokerad använt storlek på poolen|Inga dimensioner|
|VolumePoolProvisionedThroughput|Yes|Allokerat data flöde för poolen|BytesPerSecond|Genomsnitt|Allokerat data flöde för den här poolen|Inga dimensioner|
|VolumePoolTotalLogicalSize|Yes|Förbrukad pool storlek|Byte|Genomsnitt|Summan av den logiska storleken för alla volymer som tillhör poolen|Inga dimensioner|
|VolumePoolTotalSnapshotSize|Yes|Total ögonblicks bild storlek för poolen|Byte|Genomsnitt|Summan av ögonblicks bild storleken för alla volymer i poolen|Inga dimensioner|


## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft. NetApp/netAppAccounts/capacityPools/Volumes

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|AverageReadLatency|Yes|Genomsnittlig Läs fördröjning|Millisekunder|Genomsnitt|Genomsnittlig Läs fördröjning i millisekunder per åtgärd|Inga dimensioner|
|AverageWriteLatency|Yes|Genomsnittlig Skriv fördröjning|Millisekunder|Genomsnitt|Genomsnittlig Skriv fördröjning i millisekunder per åtgärd|Inga dimensioner|
|CbsVolumeBackupActive|Yes|Är volym säkerhets kopiering inaktive rad|Antal|Genomsnitt|Är säkerhets kopierings principen inaktive rad för volymen? 1 om ja, 0 om nej.|Inga dimensioner|
|CbsVolumeLogicalBackupBytes|Yes|Säkerhets kopiering av volym byte|Byte|Genomsnitt|Totalt antal byte som har säkerhetskopierats för den här volymen.|Inga dimensioner|
|CbsVolumeOperationComplete|Yes|Är volym säkerhets kopieringen klar|Antal|Genomsnitt|Har den senaste säkerhets kopierings-eller återställnings åtgärden slutförts? 1 om ja, 0 om nej.|Inga dimensioner|
|CbsVolumeOperationTransferredBytes|Yes|Senaste överförda byte i volym säkerhets kopia|Byte|Genomsnitt|Totalt antal byte som överförts för den senaste säkerhets kopieringen eller återställningen.|Inga dimensioner|
|CbsVolumeProtected|Yes|Är volym säkerhets kopiering aktive rad|Antal|Genomsnitt|Är säkerhets kopiering aktive rad för volymen? 1 om ja, 0 om nej.|Inga dimensioner|
|OtherThroughput|Yes|Annat data flöde|BytesPerSecond|Genomsnitt|Annat data flöde (som inte lästs eller skrivs) i byte per sekund|Inga dimensioner|
|ReadIops|Yes|Läs IOPS|CountPerSecond|Genomsnitt|Läs-/ut-åtgärder per sekund|Inga dimensioner|
|ReadThroughput|Yes|Läs data flöde|BytesPerSecond|Genomsnitt|Läs data flöde i byte per sekund|Inga dimensioner|
|TotalThroughput|Yes|Totalt data flöde|BytesPerSecond|Genomsnitt|Summan av alla data flöden i byte per sekund|Inga dimensioner|
|VolumeAllocatedSize|Yes|Allokerad volym storlek|Byte|Genomsnitt|En volyms allokerade storlek|Inga dimensioner|
|VolumeConsumedSizePercentage|Yes|Förbrukad storlek för procent volym|Procent|Genomsnitt|Procent andelen av volymen som förbrukas, inklusive ögonblicks bilder.|Inga dimensioner|
|VolumeLogicalSize|Yes|Storlek på förbrukad volym|Byte|Genomsnitt|Den logiska storleken på volymen (använda byte)|Inga dimensioner|
|VolumeSnapshotSize|Yes|Storlek på volym ögonblicks bild|Byte|Genomsnitt|Storlek på alla ögonblicks bilder i volymen|Inga dimensioner|
|WriteIops|Yes|Skriv IOPS|CountPerSecond|Genomsnitt|Skriv in/ut-åtgärder per sekund|Inga dimensioner|
|WriteThroughput|Yes|Skriv data flöde|BytesPerSecond|Genomsnitt|Skriv data flöde i byte per sekund|Inga dimensioner|
|XregionReplicationHealthy|Yes|Är Volume Replication-status felfri|Antal|Genomsnitt|Villkor för relationen, 1 eller 0.|Inga dimensioner|
|XregionReplicationLagTime|Yes|Fördröjning för Volume Replication|Sekunder|Genomsnitt|Hur lång tid i sekunder som data på speglingen lags bakom källan.|Inga dimensioner|
|XregionReplicationLastTransferDuration|Yes|Varaktighet för senaste överföring av Volume Replication|Sekunder|Genomsnitt|Hur lång tid i sekunder det tog för den senaste överföringen att slutföras.|Inga dimensioner|
|XregionReplicationLastTransferSize|Yes|Senaste överförings storlek för Volume Replication|Byte|Genomsnitt|Det totala antalet byte som överförs som en del av den senaste överföringen.|Inga dimensioner|
|XregionReplicationRelationshipProgress|Yes|Diskens replikerings förlopp|Byte|Genomsnitt|Total mängd data som överförts för den aktuella överförings åtgärden.|Inga dimensioner|
|XregionReplicationRelationshipTransferring|Yes|Överför volym replikering|Antal|Genomsnitt|Anger om status för Volume Replication är ' transfer '.|Inga dimensioner|
|XregionReplicationTotalTransferBytes|Yes|Överföring av Volume Replication totalt|Byte|Genomsnitt|Ackumulerade byte som överförts för relationen.|Inga dimensioner|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/applicationGateways

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ApplicationGatewayTotalTime|No|Application Gateway total tid|Millisekunder|Genomsnitt|Genomsnittlig tid det tar för en begäran att bearbetas och dess svar ska skickas. Detta beräknas som genomsnitt av intervallet från den tid då Application Gateway tar emot den första byten av en HTTP-begäran till den tidpunkt då åtgärden skicka svar slutförs. Det är viktigt att Observera att detta vanligt vis omfattar Application Gateway bearbetnings tid, tid då paket för begäran och svar överförs över nätverket och hur lång tid det tog att svara på backend-servern.|Lyssnare|
|AvgRequestCountPerHealthyHost|No|Begär Anden per minut per felfri värd|Antal|Genomsnitt|Genomsnittligt antal begär Anden per minut per felfri backend-värd i en pool|BackendSettingsPool|
|BackendConnectTime|No|Server dels anslutnings tid|Millisekunder|Genomsnitt|Åtgången tid för att upprätta en anslutning till en backend-server|Lyssnare, BackendServer, BackendPool, BackendHttpSetting|
|BackendFirstByteResponseTime|No|Svars tid för första byte för Server del|Millisekunder|Genomsnitt|Tidsintervall mellan början av att upprätta en anslutning till backend-servern och ta emot den första byten i svars huvudet, ungefär bearbetnings tiden för backend-servern|Lyssnare, BackendServer, BackendPool, BackendHttpSetting|
|BackendLastByteResponseTime|No|Svars tid för senaste byte för Server delen|Millisekunder|Genomsnitt|Tidsintervall mellan början av att upprätta en anslutning till backend-servern och ta emot den sista byten i svars texten|Lyssnare, BackendServer, BackendPool, BackendHttpSetting|
|BackendResponseStatus|Yes|Svars status för Server del|Antal|Totalt|Antalet HTTP-svars koder som genereras av Server dels medlemmar. Detta omfattar inte några svars koder som genereras av Application Gateway.|BackendServer, BackendPool, BackendHttpSetting, HttpStatusGroup|
|BlockedCount|Yes|Webb program brand vägg blockerade begär Anden regel distribution|Antal|Totalt|Webb program brand vägg blockerade begär Anden regel distribution|RuleGroup, RuleId|
|BlockedReqCount|Yes|Antal blockerade förfrågningar för webb program brand vägg|Antal|Totalt|Antal blockerade förfrågningar för webb program brand vägg|Inga dimensioner|
|BytesReceived|Yes|Mottagna byte|Byte|Totalt|Det totala antalet byte som tagits emot av Application Gateway från klienterna|Lyssnare|
|Bytes sent|Yes|Skickade byte|Byte|Totalt|Det totala antalet byte som har skickats av Application Gateway till klienterna|Lyssnare|
|CapacityUnits|No|Aktuella kapacitets enheter|Antal|Genomsnitt|Förbrukade kapacitets enheter|Inga dimensioner|
|ClientRtt|No|Klient-/klient|Millisekunder|Genomsnitt|Genomsnittlig fördröjning mellan klienter och Application Gateway. Det här måttet anger hur lång tid det tar att upprätta anslutningar och retur bekräftelser|Lyssnare|
|ComputeUnits|No|Aktuella beräknings enheter|Antal|Genomsnitt|Förbrukade beräknings enheter|Inga dimensioner|
|CpuUtilization|No|CPU-användning|Procent|Genomsnitt|Aktuell processor användning för Application Gateway|Inga dimensioner|
|CurrentConnections|Yes|Aktuella anslutningar|Antal|Totalt|Antal aktuella anslutningar som upprättats med Application Gateway|Inga dimensioner|
|EstimatedBilledCapacityUnits|No|Uppskattade enheter för fakturerings kapacitet|Antal|Genomsnitt|Uppskattade kapacitets enheter som ska debiteras|Inga dimensioner|
|FailedRequests|Yes|Misslyckade begäranden|Antal|Totalt|Antal misslyckade förfrågningar som Application Gateway har betjänat|BackendSettingsPool|
|FixedBillableCapacityUnits|No|Fasta fakturerbara kapacitetsenheter|Antal|Genomsnitt|Lägsta kapacitets enhet som ska debiteras|Inga dimensioner|
|HealthyHostCount|Yes|Antal felfria värdar|Antal|Genomsnitt|Antal felfria Server dels värdar|BackendSettingsPool|
|MatchedCount|Yes|Brand vägg för total regel distribution i webb program|Antal|Totalt|Brand vägg för webb program, total regel distribution för inkommande trafik|RuleGroup, RuleId|
|NewConnectionsPerSecond|No|Nya anslutningar per sekund|CountPerSecond|Genomsnitt|Nya anslutningar per sekund som upprättats med Application Gateway|Inga dimensioner|
|ResponseStatus|Yes|Svars status|Antal|Totalt|Http-svarets status returnerades av Application Gateway|HttpStatusGroup|
|Dataflöde|No|Dataflöde|BytesPerSecond|Genomsnitt|Antal byte per sekund som Application Gateway har betjänat|Inga dimensioner|
|TlsProtocol|Yes|Klientens TLS-protokoll|Antal|Totalt|Antalet TLS-och icke-TLS-begäranden som initieras av klienten som upprättade anslutningen till Application Gateway. Om du vill visa TLS-protokollets distribution filtrerar du efter dimension TLS-protokollet.|Lyssnare, TlsProtocol|
|TotalRequests|Yes|Totalt antal förfrågningar|Antal|Totalt|Antal lyckade förfrågningar som Application Gateway har betjänat|BackendSettingsPool|
|UnhealthyHostCount|Yes|Antal felaktiga värdar|Antal|Genomsnitt|Antal värdar för skadade Server delar|BackendSettingsPool|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft. Network/azurefirewalls

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ApplicationRuleHit|Yes|Antal träffar för program regler|Antal|Totalt|Antal gånger som program regler träffades|Status, orsak, protokoll|
|DataProcessed|Yes|Bearbetade data|Byte|Totalt|Total mängd data som bearbetas av den här brand väggen|Inga dimensioner|
|FirewallHealth|Yes|Hälso tillstånd för brand vägg|Procent|Genomsnitt|Anger den övergripande hälso tillståndet för den här brand väggen|Status, orsak|
|NetworkRuleHit|Yes|Antal träffar för nätverks regler|Antal|Totalt|Antal gånger som nätverks regler träffades|Status, orsak, protokoll|
|SNATPortUtilization|Yes|SNAT-port användning|Procent|Genomsnitt|Procent andel utgående SNAT-portar som används för närvarande|Protokoll|
|Dataflöde|No|Dataflöde|BitsPerSecond|Genomsnitt|Genomflöde som bearbetas av den här brand väggen|Inga dimensioner|


## <a name="microsoftnetworkconnections"></a>Microsoft. Network/Connections

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|BitsInPerSecond|Yes|BitsInPerSecond|BitsPerSecond|Genomsnitt|Inkommande bitar till Azure per sekund|Inga dimensioner|
|BitsOutPerSecond|Yes|BitsOutPerSecond|BitsPerSecond|Genomsnitt|Utgående bitar till Azure per sekund|Inga dimensioner|


## <a name="microsoftnetworkdnszones"></a>Microsoft. Network/dnszones

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|QueryVolume|Yes|Fråga volym|Antal|Totalt|Antal frågor som hanteras för en DNS-zon|Inga dimensioner|
|RecordSetCapacityUtilization|No|Kapacitets användning för post uppsättning|Procent|Maximal|Procent av post uppsättnings kapaciteten som används av en DNS-zon|Inga dimensioner|
|RecordSetCount|Yes|Antal post uppsättningar|Antal|Maximal|Antal post uppsättningar i en DNS-zon|Inga dimensioner|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft. Network/expressRouteCircuits

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ArpAvailability|Yes|ARP-tillgänglighet|Procent|Genomsnitt|ARP-tillgänglighet från MSEE: N till alla peer-datorer.|PeeringType, peer|
|BgpAvailability|Yes|BGP-tillgänglighet|Procent|Genomsnitt|BGP-tillgänglighet från MSEE: N till alla peer-datorer.|PeeringType, peer|
|BitsInPerSecond|No|BitsInPerSecond|BitsPerSecond|Genomsnitt|Inkommande bitar till Azure per sekund|PeeringType, DeviceRole|
|BitsOutPerSecond|No|BitsOutPerSecond|BitsPerSecond|Genomsnitt|Utgående bitar till Azure per sekund|PeeringType, DeviceRole|
|GlobalReachBitsInPerSecond|No|GlobalReachBitsInPerSecond|BitsPerSecond|Genomsnitt|Inkommande bitar till Azure per sekund|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|No|GlobalReachBitsOutPerSecond|BitsPerSecond|Genomsnitt|Utgående bitar till Azure per sekund|PeeredCircuitSKey|
|QosDropBitsInPerSecond|Yes|DroppedInBitsPerSecond|BitsPerSecond|Genomsnitt|Ingress BITS av data som ignoreras per sekund|Inga dimensioner|
|QosDropBitsOutPerSecond|Yes|DroppedOutBitsPerSecond|BitsPerSecond|Genomsnitt|Utgående bitar med data som ignoreras per sekund|Inga dimensioner|


## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft. Network/expressRouteCircuits/peering

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|BitsInPerSecond|Yes|BitsInPerSecond|BitsPerSecond|Genomsnitt|Inkommande bitar till Azure per sekund|Inga dimensioner|
|BitsOutPerSecond|Yes|BitsOutPerSecond|BitsPerSecond|Genomsnitt|Utgående bitar till Azure per sekund|Inga dimensioner|


## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft. Network/expressRouteGateways

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|No|BitsInPerSecond|BitsPerSecond|Genomsnitt|Inkommande bitar till Azure per sekund|ConnectionName|
|ErGatewayConnectionBitsOutPerSecond|No|BitsOutPerSecond|BitsPerSecond|Genomsnitt|Utgående bitar till Azure per sekund|ConnectionName|
|ExpressRouteGatewayCountOfRoutesAdvertisedToPeer|Yes|Antal vägar som annonseras till peer (för hands version)|Antal|Maximal|Antal vägar som annonseras till peer av ExpressRouteGateway|roleInstance|
|ExpressRouteGatewayCountOfRoutesLearnedFromPeer|Yes|Antal vägar som har lärts från peer-datorn (för hands version)|Antal|Maximal|Antal vägar som har lärts från peer-datorer av ExpressRouteGateway|roleInstance|
|ExpressRouteGatewayCpuUtilization|Yes|PROCESSOR belastning (för hands version)|Antal|Genomsnitt|CPU-användning för ExpressRoute-gatewayen|roleInstance|
|ExpressRouteGatewayFrequencyOfRoutesChanged|No|Frekvens för ändring av vägar (förhands granskning)|Antal|Totalt|Frekvens för flödes ändringar i ExpressRoute-Gateway|roleInstance|
|ExpressRouteGatewayNumberOfVmInVnet|No|Antal virtuella datorer i Virtual Network (för hands version)|Antal|Maximal|Antal virtuella datorer i Virtual Network|Inga dimensioner|
|ExpressRouteGatewayPacketsPerSecond|No|Paket per sekund (förhands granskning)|CountPerSecond|Genomsnitt|Paket antal för ExpressRoute-Gateway|roleInstance|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft. Network/expressRoutePorts

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|AdminState|Yes|AdminState|Antal|Genomsnitt|Administratörs tillstånd för porten|Länk|
|LineProtocol|Yes|LineProtocol|Antal|Genomsnitt|Status för linje protokoll för porten|Länk|
|PortBitsInPerSecond|Yes|BitsInPerSecond|BitsPerSecond|Genomsnitt|Inkommande bitar till Azure per sekund|Länk|
|PortBitsOutPerSecond|Yes|BitsOutPerSecond|BitsPerSecond|Genomsnitt|Utgående bitar till Azure per sekund|Länk|
|RxLightLevel|Yes|RxLightLevel|Antal|Genomsnitt|RX ljus nivå i dBm|Länk, Lane|
|TxLightLevel|Yes|TxLightLevel|Antal|Genomsnitt|TX-ljusnivå i dBm|Länk, Lane|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/frontdoors

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|BackendHealthPercentage|Yes|Server delens hälso procent|Procent|Genomsnitt|Procent andelen lyckade hälso avsökningar från HTTP/S-proxyn till Server delar|Backend, BackendPool|
|BackendRequestCount|Yes|Antal Server dels begär Anden|Antal|Totalt|Antalet förfrågningar som skickats från HTTP/S-proxyn till Server delar|HttpStatus, HttpStatusGroup, Server del|
|BackendRequestLatency|Yes|Svars tid för Server del|Millisekunder|Genomsnitt|Tiden som beräknas från när begäran skickades av HTTP/S-proxyn till Server delen tills HTTP/S-proxyn fick den senaste svars byten från Server delen|Serverdel|
|BillableResponseSize|Yes|Fakturerbart svars storlek|Byte|Totalt|Antalet fakturerbara byte (minsta 2KB per begäran) som skickats som svar från HTTP/S-proxy till klienter.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestCount|Yes|Antal begäranden|Antal|Totalt|Antalet klient förfrågningar som hanteras av HTTP/S-proxyn|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Yes|Begär ande storlek|Byte|Totalt|Antalet byte som har skickats som begär Anden från klienter till HTTP/S-proxyn|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Yes|Svars storlek|Byte|Totalt|Antalet byte som skickats som svar från HTTP/S-proxy till klienter|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|TotalLatency|Yes|Total svars tid|Millisekunder|Genomsnitt|Den tid som beräknas från när klientbegäran togs emot av HTTP/S-proxy tills klienten bekräftade den senaste svars byten från HTTP/S-proxyn|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|WebApplicationFirewallRequestCount|Yes|Antal begär Anden om webb programs brand vägg|Antal|Totalt|Antalet klient begär Anden som bearbetats av brand väggen för webbaserade program|PolicyName, RuleName, åtgärd|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft. Network/belastningsutjämnare

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|AllocatedSnatPorts|No|Allokerade SNAT-portar|Antal|Genomsnitt|Totalt antal SNAT-portar som allokerats inom tids perioden|FrontendIPAddress, BackendIPAddress, ProtocolType, |
|ByteCount|Yes|Antal byte|Byte|Totalt|Totalt antal byte som skickats inom tids perioden|FrontendIPAddress, FrontendPort, Direction|
|DipAvailability|Yes|Status för hälsoavsökningen|Antal|Genomsnitt|Genomsnittlig status för Load Balancer hälso avsökning per tids period|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|PacketCount|Yes|Antal paket|Antal|Totalt|Totalt antal överförda paket inom tids perioden|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|Yes|Antal SNAT-anslutningar|Antal|Totalt|Totalt antal nya SNAT-anslutningar som skapats inom tids perioden|FrontendIPAddress, BackendIPAddress, ConnectionState|
|SYNCount|Yes|Antal SYN|Antal|Totalt|Totalt antal skickade SYN paket inom tids perioden|FrontendIPAddress, FrontendPort, Direction|
|UsedSnatPorts|No|Använda SNAT-portar|Antal|Genomsnitt|Totalt antal SNAT-portar som använts inom tids perioden|FrontendIPAddress, BackendIPAddress, ProtocolType, |
|VipAvailability|Yes|Tillgänglighet för datasökvägar|Antal|Genomsnitt|Genomsnittlig tillgänglighet för Load Balancer data Sök väg per tids längd|FrontendIPAddress, FrontendPort|


## <a name="microsoftnetworknatgateways"></a>Microsoft. Network/natGateways

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ByteCount|Yes|Byte|Byte|Totalt|Totalt antal byte som skickats inom tids perioden|Protokoll, riktning|
|DatapathAvailability|Yes|Datapath tillgänglighet (för hands version)|Antal|Genomsnitt|Datapath tillgänglighet för NAT-gateway|Inga dimensioner|
|PacketCount|Yes|Skickas|Antal|Totalt|Totalt antal överförda paket inom tids perioden|Protokoll, riktning|
|PacketDropCount|Yes|Ignorerade paket|Antal|Totalt|Antal ignorerade paket|Inga dimensioner|
|SNATConnectionCount|Yes|Antal SNAT-anslutningar|Antal|Totalt|Totalt antal samtidiga aktiva anslutningar|Protokoll, ConnectionState|
|TotalConnectionCount|Yes|Totalt antal SNAT-anslutningar|Antal|Totalt|Totalt antal aktiva SNAT-anslutningar|Protokoll|


## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft. Network/networkInterfaces

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|BytesReceivedRate|Yes|Mottagna byte|Byte|Totalt|Antal byte som nätverks gränssnittet har tagits emot|Inga dimensioner|
|BytesSentRate|Yes|Skickade byte|Byte|Totalt|Antal byte som nätverks gränssnittet har skickats|Inga dimensioner|
|PacketsReceivedRate|Yes|Mottagna paket|Antal|Totalt|Antal paket som nätverks gränssnittet har fått|Inga dimensioner|
|PacketsSentRate|Yes|Skickade paket|Antal|Totalt|Antal paket som nätverks gränssnittet har skickats|Inga dimensioner|


## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft. Network/networkWatchers/connectionMonitors

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|AverageRoundtripMs|Yes|Genomsnittlig tid för tur och retur (MS) (klassisk)|Millisekunder|Genomsnitt|Genomsnittlig tid för nätverks fördröjning (MS) för anslutnings övervaknings avsökningar som skickas mellan källa och mål|Inga dimensioner|
|ChecksFailedPercent|Yes|Misslyckade kontroller i procent|Procent|Genomsnitt|% av kontrollerna för anslutnings övervakning misslyckades|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, destination port, TestGroupName, TestConfigurationName, SourceIP, DestinationIP, SourceSubnet, DestinationSubnet|
|ProbesFailedPercent|Yes|% Avsökningar misslyckades (klassisk)|Procent|Genomsnitt|% av anslutnings övervaknings avsökningarna misslyckades|Inga dimensioner|
|RoundTripTimeMs|Yes|Round-Trip tid (MS)|Millisekunder|Genomsnitt|Svars tid i millisekunder för kontrollerna för anslutnings övervakning|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, destination port, TestGroupName, TestConfigurationName, SourceIP, DestinationIP, SourceSubnet, DestinationSubnet|
|TestResult|Yes|Test resultat|Antal|Genomsnitt|Test resultat för anslutnings övervakaren|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, destination port, TestGroupName, TestConfigurationName, TestResultCriterion, SourceIP, DestinationIP, SourceSubnet, DestinationSubnet|


## <a name="microsoftnetworkp2svpngateways"></a>Microsoft. Network/p2sVpnGateways

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|P2SBandwidth|Yes|Gateway P2S-bandbredd|BytesPerSecond|Genomsnitt|Genomsnittlig punkt-till-plats-bandbredd för en gateway i byte per sekund|Inga dimensioner|
|P2SConnectionCount|Yes|Antal P2S-anslutningar|Antal|Totalt|Antal anslutningar från punkt-till-plats för en gateway|Protokoll|


## <a name="microsoftnetworkprivatednszones"></a>Microsoft. Network/privateDnsZones

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|QueryVolume|Yes|Fråga volym|Antal|Totalt|Antal frågor som hanteras för en Privat DNS zon|Inga dimensioner|
|RecordSetCapacityUtilization|No|Kapacitets användning för post uppsättning|Procent|Maximal|Procent av post uppsättnings kapaciteten som används av en Privat DNS zon|Inga dimensioner|
|RecordSetCount|Yes|Antal post uppsättningar|Antal|Maximal|Antal post uppsättningar i en Privat DNS zon|Inga dimensioner|
|VirtualNetworkLinkCapacityUtilization|No|Virtual Network länka kapacitets användning|Procent|Maximal|Procent andel Virtual Network länk kapacitet som används av en Privat DNS zon|Inga dimensioner|
|VirtualNetworkLinkCount|Yes|Antal Virtual Network länkar|Antal|Maximal|Antal virtuella nätverk som är länkade till en Privat DNS zon|Inga dimensioner|
|VirtualNetworkWithRegistrationCapacityUtilization|No|Virtual Network kapacitets användning för registrerings länk|Procent|Maximal|Procent av Virtual Network-länk med automatisk registrerings kapacitet som används av en Privat DNS zon|Inga dimensioner|
|VirtualNetworkWithRegistrationLinkCount|Yes|Antal Virtual Network registrerings länkar|Antal|Maximal|Antal virtuella nätverk som är länkade till en Privat DNS zon med automatisk registrering aktive rad|Inga dimensioner|


## <a name="microsoftnetworkprivateendpoints"></a>Microsoft. Network/privateEndpoints

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|PEBytesIn|Yes|Byte in|Antal|Totalt|Totalt antal byte ut|PrivateEndpointId|
|PEBytesOut|Yes|Byte ut|Antal|Totalt|Totalt antal byte ut|PrivateEndpointId|


## <a name="microsoftnetworkprivatelinkservices"></a>Microsoft. Network/privateLinkServices

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|PLSBytesIn|Yes|Byte in|Antal|Totalt|Totalt antal byte ut|PrivateLinkServiceId|
|PLSBytesOut|Yes|Byte ut|Antal|Totalt|Totalt antal byte ut|PrivateLinkServiceId|
|PLSNatPortsUsage|Yes|Användning av NAT-portar|Procent|Genomsnitt|Användning av NAT-portar|PrivateLinkServiceId, PrivateLinkServiceIPAddress|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft. Network/publicIPAddresses

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ByteCount|Yes|Antal byte|Byte|Totalt|Totalt antal byte som skickats inom tids perioden|Port, riktning|
|BytesDroppedDDoS|Yes|Ignorerade inkommande byte DDoS|BytesPerSecond|Maximal|Ignorerade inkommande byte DDoS|Inga dimensioner|
|BytesForwardedDDoS|Yes|Inkommande byte, vidarebefordrade DDoS|BytesPerSecond|Maximal|Inkommande byte, vidarebefordrade DDoS|Inga dimensioner|
|BytesInDDoS|Yes|DDoS för inkommande byte|BytesPerSecond|Maximal|DDoS för inkommande byte|Inga dimensioner|
|DDoSTriggerSYNPackets|Yes|Inkommande SYN paket för att utlösa DDoS-minskning|CountPerSecond|Maximal|Inkommande SYN paket för att utlösa DDoS-minskning|Inga dimensioner|
|DDoSTriggerTCPPackets|Yes|Inkommande TCP-paket för att utlösa DDoS-minskning|CountPerSecond|Maximal|Inkommande TCP-paket för att utlösa DDoS-minskning|Inga dimensioner|
|DDoSTriggerUDPPackets|Yes|Ingående UDP-paket för att utlösa DDoS-minskning|CountPerSecond|Maximal|Ingående UDP-paket för att utlösa DDoS-minskning|Inga dimensioner|
|IfUnderDDoSAttack|Yes|Under DDoS-attack eller inte|Antal|Maximal|Under DDoS-attack eller inte|Inga dimensioner|
|PacketCount|Yes|Antal paket|Antal|Totalt|Totalt antal överförda paket inom tids perioden|Port, riktning|
|PacketsDroppedDDoS|Yes|Inkommande paket som släppts DDoS|CountPerSecond|Maximal|Inkommande paket som släppts DDoS|Inga dimensioner|
|PacketsForwardedDDoS|Yes|Vidarebefordrade inkommande paket DDoS|CountPerSecond|Maximal|Vidarebefordrade inkommande paket DDoS|Inga dimensioner|
|PacketsInDDoS|Yes|DDoS för inkommande paket|CountPerSecond|Maximal|DDoS för inkommande paket|Inga dimensioner|
|SynCount|Yes|Antal SYN|Antal|Totalt|Totalt antal skickade SYN paket inom tids perioden|Port, riktning|
|TCPBytesDroppedDDoS|Yes|Inkommande TCP-byte utelämnade DDoS|BytesPerSecond|Maximal|Inkommande TCP-byte utelämnade DDoS|Inga dimensioner|
|TCPBytesForwardedDDoS|Yes|Inkommande TCP byte-vidarebefordrade DDoS|BytesPerSecond|Maximal|Inkommande TCP byte-vidarebefordrade DDoS|Inga dimensioner|
|TCPBytesInDDoS|Yes|DDoS för inkommande TCP-byte|BytesPerSecond|Maximal|DDoS för inkommande TCP-byte|Inga dimensioner|
|TCPPacketsDroppedDDoS|Yes|Inkommande TCP-paket ignorerade DDoS|CountPerSecond|Maximal|Inkommande TCP-paket ignorerade DDoS|Inga dimensioner|
|TCPPacketsForwardedDDoS|Yes|Inkommande TCP-paket, vidarebefordrade DDoS|CountPerSecond|Maximal|Inkommande TCP-paket, vidarebefordrade DDoS|Inga dimensioner|
|TCPPacketsInDDoS|Yes|DDoS inkommande TCP-paket|CountPerSecond|Maximal|DDoS inkommande TCP-paket|Inga dimensioner|
|UDPBytesDroppedDDoS|Yes|Inkommande UDP-byte utelämnade DDoS|BytesPerSecond|Maximal|Inkommande UDP-byte utelämnade DDoS|Inga dimensioner|
|UDPBytesForwardedDDoS|Yes|Inkommande UDP byte vidarebefordrade DDoS|BytesPerSecond|Maximal|Inkommande UDP byte vidarebefordrade DDoS|Inga dimensioner|
|UDPBytesInDDoS|Yes|DDoS för inkommande UDP-byte|BytesPerSecond|Maximal|DDoS för inkommande UDP-byte|Inga dimensioner|
|UDPPacketsDroppedDDoS|Yes|Ignorerade inkommande UDP-paket DDoS|CountPerSecond|Maximal|Ignorerade inkommande UDP-paket DDoS|Inga dimensioner|
|UDPPacketsForwardedDDoS|Yes|Vidarebefordrade inkommande UDP-paket DDoS|CountPerSecond|Maximal|Vidarebefordrade inkommande UDP-paket DDoS|Inga dimensioner|
|UDPPacketsInDDoS|Yes|DDoS för inkommande UDP-paket|CountPerSecond|Maximal|DDoS för inkommande UDP-paket|Inga dimensioner|
|VipAvailability|Yes|Tillgänglighet för datasökvägar|Antal|Genomsnitt|Genomsnittlig tillgänglighet för IP-adress per tids längd|Port|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft. Network/trafficManagerProfiles

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Yes|Slut punkts status efter slut punkt|Antal|Maximal|1 om avsöknings status för en slut punkt är "aktive rad", annars 0.|EndpointName|
|QpsByEndpoint|Yes|Returnerade frågor efter slut punkt|Antal|Totalt|Antal gånger som en Traffic Manager-slutpunkt returnerades inom den aktuella tids ramen|EndpointName|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft. Network/virtualNetworkGateways

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|AverageBandwidth|Yes|Gatewayens S2S-bandbredd|BytesPerSecond|Genomsnitt|Genomsnittlig bandbredd för plats-till-plats för en gateway i byte per sekund|Inga dimensioner|
|ExpressRouteGatewayCountOfRoutesAdvertisedToPeer|Yes|Antal vägar som annonseras till peer (för hands version)|Antal|Maximal|Antal vägar som annonseras till peer av ExpressRouteGateway|roleInstance|
|ExpressRouteGatewayCountOfRoutesLearnedFromPeer|Yes|Antal vägar som har lärts från peer-datorn (för hands version)|Antal|Maximal|Antal vägar som har lärts från peer-datorer av ExpressRouteGateway|roleInstance|
|ExpressRouteGatewayCpuUtilization|Yes|PROCESSOR belastning (för hands version)|Antal|Genomsnitt|CPU-användning för ExpressRoute-gatewayen|roleInstance|
|ExpressRouteGatewayFrequencyOfRoutesChanged|No|Frekvens för ändring av vägar (förhands granskning)|Antal|Totalt|Frekvens för flödes ändringar i ExpressRoute-Gateway|roleInstance|
|ExpressRouteGatewayNumberOfVmInVnet|No|Antal virtuella datorer i Virtual Network (för hands version)|Antal|Maximal|Antal virtuella datorer i Virtual Network|Inga dimensioner|
|ExpressRouteGatewayPacketsPerSecond|No|Paket per sekund (förhands granskning)|CountPerSecond|Genomsnitt|Paket antal för ExpressRoute-Gateway|roleInstance|
|P2SBandwidth|Yes|Gateway P2S-bandbredd|BytesPerSecond|Genomsnitt|Genomsnittlig punkt-till-plats-bandbredd för en gateway i byte per sekund|Inga dimensioner|
|P2SConnectionCount|Yes|Antal P2S-anslutningar|Antal|Maximal|Antal anslutningar från punkt-till-plats för en gateway|Protokoll|
|TunnelAverageBandwidth|Yes|Tunnelbandbredd|BytesPerSecond|Genomsnitt|Genomsnittlig bandbredd för en tunnel i byte per sekund|ConnectionName, RemoteIP|
|TunnelEgressBytes|Yes|Utgående byte för tunnel|Byte|Totalt|Utgående byte för en tunnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Yes|Utgående ignorerade TS-paket med matchningsfel för tunnel|Antal|Totalt|Antal utgående ignorerade paket från trafikväljare med matchningsfel för en tunnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Yes|Utgående tunnelpaket|Antal|Totalt|Antal utgående paket för en tunnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Yes|Inkommande byte för tunnel|Byte|Totalt|Inkommande byte för en tunnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Yes|Inkommande ignorerade TS-paket med matchningsfel för tunnel|Antal|Totalt|Antal inkommande ignorerade paket från trafikväljare med matchningsfel för en tunnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Yes|Inkommande tunnel paket|Antal|Totalt|Antal inkommande paket för en tunnel|ConnectionName, RemoteIP|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft. Network/virtualNetworks

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Yes|Fördröjning för ping till en virtuell dator|Millisekunder|Genomsnitt|Tur och retur-tid för pingar som skickas till en virtuell måldator|SourceCustomerAddress, DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Yes|Misslyckade pingar till en virtuell dator|Procent|Genomsnitt|Procent av antalet misslyckade pingar till totalt antal skickade pingar för en virtuell måldator|SourceCustomerAddress, DestinationCustomerAddress|


## <a name="microsoftnetworkvirtualrouters"></a>Microsoft. Network/virtualRouters

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|PeeringAvailability|Yes|BGP-tillgänglighet|Procent|Genomsnitt|BGP-tillgänglighet mellan VirtualRouter och fjärr-peers|Peer|


## <a name="microsoftnetworkvpngateways"></a>Microsoft. Network/vpnGateways

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|AverageBandwidth|Yes|Gatewayens S2S-bandbredd|BytesPerSecond|Genomsnitt|Genomsnittlig bandbredd för plats-till-plats för en gateway i byte per sekund|Inga dimensioner|
|TunnelAverageBandwidth|Yes|Tunnelbandbredd|BytesPerSecond|Genomsnitt|Genomsnittlig bandbredd för en tunnel i byte per sekund|ConnectionName, RemoteIP|
|TunnelEgressBytes|Yes|Utgående byte för tunnel|Byte|Totalt|Utgående byte för en tunnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Yes|Utgående ignorerade TS-paket med matchningsfel för tunnel|Antal|Totalt|Antal utgående ignorerade paket från trafikväljare med matchningsfel för en tunnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Yes|Utgående tunnelpaket|Antal|Totalt|Antal utgående paket för en tunnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Yes|Inkommande byte för tunnel|Byte|Totalt|Inkommande byte för en tunnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Yes|Inkommande ignorerade TS-paket med matchningsfel för tunnel|Antal|Totalt|Antal inkommande ignorerade paket från trafikväljare med matchningsfel för en tunnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Yes|Inkommande tunnel paket|Antal|Totalt|Antal inkommande paket för en tunnel|ConnectionName, RemoteIP|


## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft. NotificationHubs/Namespaces/NotificationHubs

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|inkommande|Yes|Inkommande meddelanden|Antal|Totalt|Antalet lyckade sändnings-API-anrop. |Inga dimensioner|
|inkommande. alla. failedrequests|Yes|Alla inkommande misslyckade förfrågningar|Antal|Totalt|Totalt antal inkommande misslyckade begär Anden för en Notification Hub|Inga dimensioner|
|inkommande. alla. förfrågningar|Yes|Alla inkommande begär Anden|Antal|Totalt|Totalt antal inkommande begär Anden för en Notification Hub|Inga dimensioner|
|inkommande. schemalagd|Yes|Schemalagda push-meddelanden har skickats|Antal|Totalt|Schemalagda push-meddelanden har avbrutits|Inga dimensioner|
|inkommande. schemalagd. Avbryt|Yes|Schemalagda push-meddelanden har avbrutits|Antal|Totalt|Schemalagda push-meddelanden har avbrutits|Inga dimensioner|
|installation. alla|Yes|Installations hanterings åtgärder|Antal|Totalt|Installations hanterings åtgärder|Inga dimensioner|
|installation. Delete|Yes|Ta bort installations åtgärder|Antal|Totalt|Ta bort installations åtgärder|Inga dimensioner|
|installation. get|Yes|Hämta installations åtgärder|Antal|Totalt|Hämta installations åtgärder|Inga dimensioner|
|installation. patch|Yes|Installation av korrigerings åtgärder|Antal|Totalt|Installation av korrigerings åtgärder|Inga dimensioner|
|installation. upsert|Yes|Skapa eller uppdatera installations åtgärder|Antal|Totalt|Skapa eller uppdatera installations åtgärder|Inga dimensioner|
|notificationhub. push-meddelanden|Yes|Alla utgående meddelanden|Antal|Totalt|Alla utgående aviseringar för Notification Hub|Inga dimensioner|
|utgående. allpns. badorexpiredchannel|Yes|Dåliga eller utgångna kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom kanal/token/registrationId i registreringen har upphört att gälla eller är ogiltiga.|Inga dimensioner|
|utgående. allpns. channelerror|Yes|Kanal fel|Antal|Totalt|Antal push-meddelanden som inte har misslyckats på grund av att kanalen var ogiltig och som inte är associerad med rätt app-begränsning eller upphörde att gälla.|Inga dimensioner|
|utgående. allpns. invalidpayload|Yes|Nytto Last fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom PNS returnerade ett felaktigt nytto Last fel.|Inga dimensioner|
|utgående. allpns. pnserror|Yes|Fel i externt meddelande system|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av ett problem med att kommunicera med PNS (exkluderar autentiseringsproblem).|Inga dimensioner|
|utgående. allpns. lyckades|Yes|Lyckade aviseringar|Antal|Totalt|Antalet lyckade aviseringar.|Inga dimensioner|
|utgående. APN. badchannel|Yes|APN dåligt kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av att token är ogiltig (APN-status kod: 8).|Inga dimensioner|
|utgående. APN. expiredchannel|Yes|APN utgånget i kanal fel|Antal|Totalt|Antalet token som ogiltig förklarades av APNS-feedback-kanalen.|Inga dimensioner|
|utgående. APN. invalidcredentials|Yes|APN-auktoriseringsfel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna, eller om autentiseringsuppgifterna blockeras.|Inga dimensioner|
|utgående. APN. invalidnotificationsize|Yes|APN ogiltigt meddelande storleks fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom nytto lasten var för stor (APN-status kod: 7).|Inga dimensioner|
|utgående. APN. pnserror|Yes|APN-fel|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av fel vid kommunikation med APN.|Inga dimensioner|
|utgående. APN. lyckades|Yes|APN-lyckade meddelanden|Antal|Totalt|Antalet lyckade aviseringar.|Inga dimensioner|
|utgående. GCM. authenticationerror|Yes|GCM-autentiseringsfel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna som autentiseringsuppgifterna är blockerade eller om SenderId inte har kon figurer ATS korrekt i appen (GCM result: MismatchedSenderId).|Inga dimensioner|
|utgående. GCM. badchannel|Yes|GCM felaktigt kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom registrationId i registreringen inte kändes igen (GCM-resultat: ogiltig registrering).|Inga dimensioner|
|utgående. GCM. expiredchannel|Yes|GCM utgången kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av att registrationId i registreringen har upphört att gälla (GCM-resultat: NotRegistered).|Inga dimensioner|
|utgående. GCM. invalidcredentials|Yes|GCM-auktoriseringsfel (ogiltiga autentiseringsuppgifter)|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna, eller om autentiseringsuppgifterna blockeras.|Inga dimensioner|
|utgående. GCM. invalidnotificationformat|Yes|GCM ogiltigt meddelande format|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom nytto lasten inte formaterades korrekt (GCM-resultat: InvalidDataKey eller InvalidTtl).|Inga dimensioner|
|utgående. GCM. invalidnotificationsize|Yes|GCM ogiltig meddelande storleks fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom nytto lasten var för stor (GCM-resultat: MessageTooBig).|Inga dimensioner|
|utgående. GCM. pnserror|Yes|GCM-fel|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av fel vid kommunikation med GCM.|Inga dimensioner|
|utgående. GCM. lyckades|Yes|GCM slutförda meddelanden|Antal|Totalt|Antalet lyckade aviseringar.|Inga dimensioner|
|utgående. GCM. begränsad|Yes|GCM-begränsade meddelanden|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom GCM begränsade till den här appen (GCM-status kod: 501-599 eller resultat: ej tillgängligt).|Inga dimensioner|
|utgående. GCM. wrongchannel|Yes|GCM fel kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom registrationId i registreringen inte är kopplad till den aktuella appen (GCM result: InvalidPackageName).|Inga dimensioner|
|utgående. MPNS. authenticationerror|Yes|MPNS-autentiseringsfel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna, eller om autentiseringsuppgifterna blockeras.|Inga dimensioner|
|utgående. MPNS. badchannel|Yes|MPNS felaktigt kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom ChannelURI i registreringen inte kändes igen (MPNS-status: 404 hittades inte).|Inga dimensioner|
|utgående. MPNS. channeldisconnected|Yes|MPNS-kanalen är frånkopplad|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av att ChannelURI i registreringen var frånkopplad (MPNS status: 412 hittades inte).|Inga dimensioner|
|utgående. MPNS. släppt|Yes|MPNS borttagna meddelanden|Antal|Totalt|Antal push-meddelanden som släppts av MPNS (MPNS-svars huvud: X-NotificationStatus: QueueFull eller undertrycks).|Inga dimensioner|
|utgående. MPNS. invalidcredentials|Yes|MPNS ogiltiga autentiseringsuppgifter|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna, eller om autentiseringsuppgifterna blockeras.|Inga dimensioner|
|utgående. MPNS. invalidnotificationformat|Yes|MPNS ogiltigt meddelande format|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom nytto lasten för meddelandet var för stor.|Inga dimensioner|
|utgående. MPNS. pnserror|Yes|MPNS-fel|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av fel vid kommunikation med MPNS.|Inga dimensioner|
|utgående. MPNS. lyckades|Yes|MPNS slutförda meddelanden|Antal|Totalt|Antalet lyckade aviseringar.|Inga dimensioner|
|utgående. MPNS. begränsad|Yes|MPNS-begränsade meddelanden|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom MPNS begränsar den här appen (WNS MPNS: 406 är inte acceptabel).|Inga dimensioner|
|utgående. WNS. authenticationerror|Yes|WNS-autentiseringsfel|Antal|Totalt|Meddelandet levererades inte på grund av fel vid kommunikation med ogiltiga Windows Live-autentiseringsuppgifter eller felaktigt token.|Inga dimensioner|
|utgående. WNS. badchannel|Yes|WNS felaktigt kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom ChannelURI i registreringen inte kändes igen (WNS-status: 404 hittades inte).|Inga dimensioner|
|utgående. WNS. channeldisconnected|Yes|WNS-kanalen är frånkopplad|Antal|Totalt|Meddelandet utelämnades eftersom ChannelURI i registreringen är begränsat (WNS-svars huvud: X-WNS-DeviceConnectionStatus: frånkopplat).|Inga dimensioner|
|utgående. WNS. channelthrottled|Yes|WNS-kanalen är begränsad|Antal|Totalt|Meddelandet utelämnades eftersom ChannelURI i registreringen är begränsat (WNS-svars huvud: X-WNS-NotificationStatus: channelThrottled).|Inga dimensioner|
|utgående. WNS. släppt|Yes|WNS borttagna meddelanden|Antal|Totalt|Meddelandet utelämnades eftersom ChannelURI i registreringen är begränsat (X-WNS-NotificationStatus: släppt men inte X-WNS-DeviceConnectionStatus: frånkopplat).|Inga dimensioner|
|utgående. WNS. expiredchannel|Yes|WNS utgången kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom ChannelURI har upphört att gälla (WNS-status: 410 borta).|Inga dimensioner|
|utgående. WNS. invalidcredentials|Yes|WNS-auktoriseringsfel (ogiltiga autentiseringsuppgifter)|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna, eller om autentiseringsuppgifterna blockeras. (Windows Live känner inte igen autentiseringsuppgifterna).|Inga dimensioner|
|utgående. WNS. invalidnotificationformat|Yes|WNS ogiltigt meddelande format|Antal|Totalt|Meddelandets format är ogiltigt (WNS-status: 400). Observera att WNS inte avvisar alla ogiltiga nytto laster.|Inga dimensioner|
|utgående. WNS. invalidnotificationsize|Yes|WNS ogiltig meddelande storleks fel|Antal|Totalt|Meddelande nytto lasten är för stor (WNS-status: 413).|Inga dimensioner|
|utgående. WNS. invalidtoken|Yes|WNS-auktoriseringsfel (ogiltig token)|Antal|Totalt|Den token som angetts för WNS är inte giltig (WNS-status: 401 otillåten).|Inga dimensioner|
|utgående. WNS. pnserror|Yes|WNS-fel|Antal|Totalt|Meddelandet levererades inte på grund av fel vid kommunikation med WNS.|Inga dimensioner|
|utgående. WNS. lyckades|Yes|WNS slutförda meddelanden|Antal|Totalt|Antalet lyckade aviseringar.|Inga dimensioner|
|utgående. WNS. begränsad|Yes|WNS-begränsade meddelanden|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom WNS begränsar den här appen (WNS-status: 406 är inte acceptabel).|Inga dimensioner|
|utgående. WNS. tokenproviderunreachable|Yes|WNS-auktoriseringsfel (kan inte kontaktas)|Antal|Totalt|Windows Live är inte tillgängligt.|Inga dimensioner|
|utgående. WNS. wrongtoken|Yes|WNS-auktoriseringsfel (fel token)|Antal|Totalt|Den token som angetts för WNS är giltig men för ett annat program (WNS-status: 403 förbjuden). Detta kan inträffa om ChannelURI i registreringen är kopplad till en annan app. Kontrol lera att klient programmet är associerat med samma app vars autentiseringsuppgifter finns i Notification Hub.|Inga dimensioner|
|registrering. alla|Yes|Registrerings åtgärder|Antal|Totalt|Antalet lyckade registrerings åtgärder (skapar uppdateringar av frågor och borttagningar). |Inga dimensioner|
|registrering. skapa|Yes|Skapa åtgärder för registrering|Antal|Totalt|Antalet lyckade registreringar som skapas.|Inga dimensioner|
|registrering. Delete|Yes|Åtgärder för att ta bort registrering|Antal|Totalt|Antalet lyckade registrerings borttagningar.|Inga dimensioner|
|registrering. Hämta|Yes|Läs åtgärder för registrering|Antal|Totalt|Antalet lyckade registrerings frågor.|Inga dimensioner|
|registrering. Update|Yes|Registrera uppdaterings åtgärder|Antal|Totalt|Antalet lyckade registrerings uppdateringar.|Inga dimensioner|
|schemalagt. väntar|Yes|Väntande schemalagda meddelanden|Antal|Totalt|Väntande schemalagda meddelanden|Inga dimensioner|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft. OperationalInsights/arbets ytor

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Average_% tillgängligt minne|Yes|Tillgängligt minne i procent|Antal|Genomsnitt|Average_% tillgängligt minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% tillgängligt växlings utrymme|Yes|Tillgängligt växlings utrymme i procent|Antal|Genomsnitt|Average_% tillgängligt växlings utrymme|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% allokerade byte som används|Yes|% Allokerade byte som används|Antal|Genomsnitt|Average_% allokerade byte som används|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% DPC-tid|Yes|DPC-tid i procent|Antal|Genomsnitt|Average_% DPC-tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ kostnads fri noder i procent|Yes|Kostnads fri noder i procent|Antal|Genomsnitt|Average_ kostnads fri noder i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ ledigt utrymme i procent|Yes|Ledigt utrymme i procent|Antal|Genomsnitt|Average_ ledigt utrymme i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ ledig tid i procent|Yes|Inaktivitetstid i procent|Antal|Genomsnitt|Average_ ledig tid i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Avbrotts tid i procent för Average_%|Yes|% Avbrotts tid|Antal|Genomsnitt|Avbrotts tid i procent för Average_%|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% i/o-vänte tid|Yes|% I/o-vänte tid|Antal|Genomsnitt|Average_% i/o-vänte tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% bra tid|Yes|% Trevligt tid|Antal|Genomsnitt|Average_% bra tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ privilegie rad tid i procent|Yes|Privilegie rad tid i procent|Antal|Genomsnitt|Average_ privilegie rad tid i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% processor tid|Yes|% processortid|Antal|Genomsnitt|Average_% processor tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Använd noder i procent|Yes|% Använda noder i procent|Antal|Genomsnitt|Average_% Använd noder i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% använt minne|Yes|Använt minne i procent|Antal|Genomsnitt|Average_% använt minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ använt utrymme i procent|Yes|Använt utrymme i procent|Antal|Genomsnitt|Average_ använt utrymme i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ använt växlings utrymme i procent|Yes|Använt växlings utrymme i procent|Antal|Genomsnitt|Average_ använt växlings utrymme i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% användar tid|Yes|Användar tid i procent|Antal|Genomsnitt|Average_% användar tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available megabyte|Yes|Tillgängliga megabyte|Antal|Genomsnitt|Average_Available megabyte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MB minne|Yes|Tillgängligt minne i megabyte|Antal|Genomsnitt|Average_Available MB minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Växling vid Average_Available megabyte|Yes|Tillgängliga megabyte växlings utrymme|Antal|Genomsnitt|Växling vid Average_Available megabyte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk s/läsning|Yes|Medel s/disk läsning|Antal|Genomsnitt|Average_Avg. Disk s/läsning|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk s/överföring|Yes|Medel s/disk överföring|Antal|Genomsnitt|Average_Avg. Disk s/överföring|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk s/skrivning|Yes|Medel s/disk skrivning|Antal|Genomsnitt|Average_Avg. Disk s/skrivning|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes mottagna/SEK|Yes|Mottagna byte/SEK|Antal|Genomsnitt|Average_Bytes mottagna/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Skickade Average_Bytes per sekund|Yes|Skickade byte/SEK|Antal|Genomsnitt|Skickade Average_Bytes per sekund|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes totalt/SEK|Yes|Totalt antal byte/s|Antal|Genomsnitt|Average_Bytes totalt/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current diskkölängd|Yes|Aktuell diskkölängd|Antal|Genomsnitt|Average_Current diskkölängd|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk lästa byte/s|Yes|Disk-lästa byte/s|Antal|Genomsnitt|Average_Disk lästa byte/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk läsningar/s|Yes|Disk läsningar/SEK|Antal|Genomsnitt|Average_Disk läsningar/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk överföringar/SEK|Yes|Disk överföringar/SEK|Antal|Genomsnitt|Average_Disk överföringar/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk skrivna byte/s|Yes|Disk-skrivna byte/s|Antal|Genomsnitt|Average_Disk skrivna byte/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk skrivningar/SEK|Yes|Disk skrivningar/SEK|Antal|Genomsnitt|Average_Disk skrivningar/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free megabyte|Yes|Lediga megabyte|Antal|Genomsnitt|Average_Free megabyte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free fysiskt minne|Yes|Ledigt fysiskt minne|Antal|Genomsnitt|Average_Free fysiskt minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free utrymme i växlingsfiler|Yes|Ledigt utrymme i växlingsfiler|Antal|Genomsnitt|Average_Free utrymme i växlingsfiler|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free virtuellt minne|Yes|Ledigt virtuellt minne|Antal|Genomsnitt|Average_Free virtuellt minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical Disk-byte/s|Yes|Logisk disk byte/SEK|Antal|Genomsnitt|Average_Logical Disk-byte/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page läsningar/s|Yes|Sid läsningar/s|Antal|Genomsnitt|Average_Page läsningar/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page skrivningar/SEK|Yes|Sid skrivningar/SEK|Antal|Genomsnitt|Average_Page skrivningar/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages per sekund|Yes|Sidor/s|Antal|Genomsnitt|Average_Pages per sekund|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct privilegie rad tid|Yes|PCT privilegie rad tid|Antal|Genomsnitt|Average_Pct privilegie rad tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct användar tid|Yes|PCT-användar tid|Antal|Genomsnitt|Average_Pct användar tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical Disk-byte/s|Yes|Fysisk disk-byte/SEK|Antal|Genomsnitt|Average_Physical Disk-byte/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Yes|Processer|Antal|Genomsnitt|Average_Processes|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processor Kölängd|Yes|Kölängd för processor|Antal|Genomsnitt|Average_Processor Kölängd|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size lagras i växlingsfiler|Yes|Storlek lagrad i växlingsfiler|Antal|Genomsnitt|Average_Size lagras i växlingsfiler|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total byte|Yes|Totalt antal byte|Antal|Genomsnitt|Average_Total byte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total mottagna byte|Yes|Totalt antal mottagna byte|Antal|Genomsnitt|Average_Total mottagna byte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Skickade Average_Total byte|Yes|Totalt antal överförda byte|Antal|Genomsnitt|Skickade Average_Total byte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total kollisioner|Yes|Totalt antal kollisioner|Antal|Genomsnitt|Average_Total kollisioner|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total mottagna paket|Yes|Totalt antal mottagna paket|Antal|Genomsnitt|Average_Total mottagna paket|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Skickade Average_Total paket|Yes|Totalt antal överförda paket|Antal|Genomsnitt|Skickade Average_Total paket|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total RX-fel|Yes|Totalt antal mottagna mottagnings fel|Antal|Genomsnitt|Average_Total RX-fel|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total TX-fel|Yes|Totalt antal TX-fel|Antal|Genomsnitt|Average_Total TX-fel|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Yes|Drifttid|Antal|Genomsnitt|Average_Uptime|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used megabyte växlings utrymme|Yes|Använt megabyte växlings utrymme|Antal|Genomsnitt|Average_Used megabyte växlings utrymme|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used minne i KB|Yes|Använt minne i KB|Antal|Genomsnitt|Average_Used minne i KB|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used minne i MB|Yes|Använt minne i MB|Antal|Genomsnitt|Average_Used minne i MB|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Yes|Användare|Antal|Genomsnitt|Average_Users|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual delat minne|Yes|Virtuellt delat minne|Antal|Genomsnitt|Average_Virtual delat minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Händelse|Yes|Händelse|Antal|Genomsnitt|Händelse|Källa, EventLog, dator, EventCategory, EventLevel, EventLevelName, EventID|
|Pulsslag|Yes|Pulsslag|Antal|Totalt|Pulsslag|Dator, OSType, version, SourceComputerId|
|Uppdatera|Yes|Uppdatera|Antal|Genomsnitt|Uppdatera|Dator, produkt, klassificering, UpdateState, valfri, godkänd|


## <a name="microsoftpeeringpeerings"></a>Microsoft. peering/peering

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|EgressTrafficRate|Yes|Utgående trafik hastighet|BitsPerSecond|Genomsnitt|Utgående trafik hastighet i bitar per sekund|ConnectionId, SessionIp, TrafficClass|
|IngressTrafficRate|Yes|Inkommande trafik hastighet|BitsPerSecond|Genomsnitt|Inkommande trafik hastighet i bitar per sekund|ConnectionId, SessionIp, TrafficClass|
|SessionAvailability|Yes|Tillgänglighet för sessioner|Antal|Genomsnitt|Tillgänglighet för peering-sessionen|ConnectionId, SessionIp|


## <a name="microsoftpeeringpeeringservices"></a>Microsoft. peering/peeringServices

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|PrefixLatency|Yes|Prefix svars tid|Millisekunder|Genomsnitt|Prefixlängd för median|PrefixName|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft. PowerBIDedicated/kapacitet

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|memory_metric|Yes|Minne (gen1)|Byte|Genomsnitt|Memory. Intervallet 0-3 GB för a1, 0-5 GB för a2, 0-10 GB för A3, 0-25 GB för A4, 0-50 GB för A5 och 0-100 GB för A6. Stöds endast för Power BI Embedded generation 1-resurser.|Inga dimensioner|
|memory_thrashing_metric|Yes|Nedskräpning för minne (data uppsättningar) (gen1)|Procent|Genomsnitt|Genomsnittligt minne nedskräpning. Stöds endast för Power BI Embedded generation 1-resurser.|Inga dimensioner|
|qpu_high_utilization_metric|Yes|QPU-hög användning (gen1)|Antal|Totalt|QPU hög användning under den senaste minuten, 1 för hög QPU användning, annars 0. Stöds endast för Power BI Embedded generation 1-resurser.|Inga dimensioner|
|QueryDuration|Yes|Frågans varaktighet (data uppsättningar) (gen1)|Millisekunder|Genomsnitt|DAX-fråge varaktighet i sista intervallet. Stöds endast för Power BI Embedded generation 1-resurser.|Inga dimensioner|
|QueryPoolJobQueueLength|Yes|Kölängd för jobbkö (data uppsättningar) (gen1)|Antal|Genomsnitt|Antal jobb i kön för trådpoolen för Query. Stöds endast för Power BI Embedded generation 1-resurser.|Inga dimensioner|


## <a name="microsoftprojectbabylonaccounts"></a>Microsoft. ProjectBabylon/konton

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ScanCancelled|Yes|Skanningen avbröts|Antal|Totalt|Anger antalet avsökningar som har avbrutits.|ResourceId|
|ScanCompleted|Yes|Sökningen är klar|Antal|Totalt|Anger hur många genomsökningar som har slutförts.|ResourceId|
|ScanFailed|Yes|Sökningen misslyckades|Antal|Totalt|Anger antalet misslyckade genomsökningar.|ResourceId|
|ScanTimeTaken|Yes|Tids åtgång för genomsökning|Sekunder|Totalt|Anger den totala genomsöknings tiden i sekunder.|ResourceId|


## <a name="microsoftpurviewaccounts"></a>Microsoft. avdelningens kontroll/konton

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ScanCancelled|Yes|Skanningen avbröts|Antal|Totalt|Anger antalet avsökningar som har avbrutits.||
|ScanCompleted|Yes|Sökningen är klar|Antal|Totalt|Anger hur många genomsökningar som har slutförts.||
|ScanFailed|Yes|Sökningen misslyckades|Antal|Totalt|Anger antalet misslyckade genomsökningar.||
|ScanTimeTaken|Yes|Tids åtgång för genomsökning|Sekunder|Totalt|Anger den totala genomsöknings tiden i sekunder.||


## <a name="microsoftrelaynamespaces"></a>Microsoft. Relay/namnrymder

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ActiveConnections|No|ActiveConnections|Antal|Totalt|Totalt ActiveConnections för Microsoft. Relay.|Entitetsnamnet|
|ActiveListeners|No|ActiveListeners|Antal|Totalt|Totalt ActiveListeners för Microsoft. Relay.|Entitetsnamnet|
|BytesTransferred|Yes|BytesTransferred|Byte|Totalt|Totalt BytesTransferred för Microsoft. Relay.|Entitetsnamnet|
|ListenerConnections-ClientError|No|ListenerConnections-ClientError|Antal|Totalt|ClientError på ListenerConnections för Microsoft. Relay.|EntityName, OperationResult under pågående|
|ListenerConnections-ServerError|No|ListenerConnections-ServerError|Antal|Totalt|ServerError på ListenerConnections för Microsoft. Relay.|EntityName, OperationResult under pågående|
|ListenerConnections-Success|No|ListenerConnections-Success|Antal|Totalt|Lyckade ListenerConnections för Microsoft. Relay.|EntityName, OperationResult under pågående|
|ListenerConnections-TotalRequests|No|ListenerConnections-TotalRequests|Antal|Totalt|Totalt ListenerConnections för Microsoft. Relay.|Entitetsnamnet|
|ListenerDisconnects|No|ListenerDisconnects|Antal|Totalt|Totalt ListenerDisconnects för Microsoft. Relay.|Entitetsnamnet|
|SenderConnections-ClientError|No|SenderConnections-ClientError|Antal|Totalt|ClientError på SenderConnections för Microsoft. Relay.|EntityName, OperationResult under pågående|
|SenderConnections-ServerError|No|SenderConnections-ServerError|Antal|Totalt|ServerError på SenderConnections för Microsoft. Relay.|EntityName, OperationResult under pågående|
|SenderConnections-Success|No|SenderConnections-Success|Antal|Totalt|Lyckade SenderConnections för Microsoft. Relay.|EntityName, OperationResult under pågående|
|SenderConnections-TotalRequests|No|SenderConnections-TotalRequests|Antal|Totalt|Totalt antal SenderConnections-begäranden för Microsoft. Relay.|Entitetsnamnet|
|SenderDisconnects|No|SenderDisconnects|Antal|Totalt|Totalt SenderDisconnects för Microsoft. Relay.|Entitetsnamnet|


## <a name="microsoftresourcessubscriptions"></a>Microsoft. Resources/subscriptions

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Svarstid|Yes|Latens data för inkommande HTTP-begäran|Antal|Genomsnitt|Latens data för inkommande HTTP-begäran|Metod, namnrymd, RequestRegion, ResourceType, Microsoft. SubscriptionId|
|Trafik|Yes|Trafik|Antal|Genomsnitt|Http-trafik|RequestRegion, StatusCode, StatusCodeClass, ResourceType, namnrymd, Microsoft. SubscriptionId|


## <a name="microsoftsearchsearchservices"></a>Microsoft. search/searchServices

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|SearchLatency|Yes|Sök svars tid|Sekunder|Genomsnitt|Genomsnittlig Sök fördröjning för Sök tjänsten|Inga dimensioner|
|SearchQueriesPerSecond|Yes|Sök frågor per sekund|CountPerSecond|Genomsnitt|Sök efter frågor per sekund för Sök tjänsten|Inga dimensioner|
|ThrottledSearchQueriesPercentage|Yes|Begränsade Sök frågor i procent|Procent|Genomsnitt|Procent andel Sök frågor som har begränsats för Sök tjänsten|Inga dimensioner|


## <a name="microsoftservicebusnamespaces"></a>Microsoft. Service Bus/namnrymder

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ActiveConnections|No|ActiveConnections|Antal|Totalt|Totalt antal aktiva anslutningar för Microsoft. Service Bus.||
|ActiveMessages|No|Antal aktiva meddelanden i en kö/ett ämne.|Antal|Genomsnitt|Antal aktiva meddelanden i en kö/ett ämne.|Entitetsnamnet|
|ConnectionsClosed|No|Stängda anslutningar.|Antal|Genomsnitt|Stängda anslutningar för Microsoft. Service Bus.|Entitetsnamnet|
|ConnectionsOpened|No|Öppna anslutningar.|Antal|Genomsnitt|Anslutningar som öppnats för Microsoft. Service Bus.|Entitetsnamnet|
|CPUXNS|No|PROCESSOR (inaktuell)|Procent|Maximal|Mått för CPU-användning för Service Bus Premium-namnrymd. Det här måttet är inaktuell. Använd processor måttet (NamespaceCpuUsage) i stället.|Replik|
|DeadletteredMessages|No|Antal meddelanden om obeställbara meddelanden i en kö/ett ämne.|Antal|Genomsnitt|Antal meddelanden om obeställbara meddelanden i en kö/ett ämne.|Entitetsnamnet|
|IncomingMessages|Yes|Inkommande meddelanden|Antal|Totalt|Inkommande meddelanden för Microsoft. Service Bus.|Entitetsnamnet|
|IncomingRequests|Yes|Inkommande förfrågningar|Antal|Totalt|Inkommande begär Anden för Microsoft. Service Bus.|Entitetsnamnet|
|Meddelanden|No|Antal meddelanden i kö/ämne.|Antal|Genomsnitt|Antal meddelanden i kö/ämne.|Entitetsnamnet|
|NamespaceCpuUsage|No|Processor|Procent|Maximal|Mått för CPU-användning för Service Bus Premium-namnrymd.|Replik|
|NamespaceMemoryUsage|No|Minnesanvändning|Procent|Maximal|Minnes användnings mått för Service Bus Premium-namnrymd.|Replik|
|OutgoingMessages|Yes|Utgående meddelanden|Antal|Totalt|Utgående meddelanden för Microsoft. Service Bus.|Entitetsnamnet|
|ScheduledMessages|No|Antal schemalagda meddelanden i en kö/ett ämne.|Antal|Genomsnitt|Antal schemalagda meddelanden i en kö/ett ämne.|Entitetsnamnet|
|ServerErrors|No|Serverfel.|Antal|Totalt|Server fel för Microsoft. Service Bus.|EntityName, OperationResult under pågående|
|Storlek|No|Storlek|Byte|Genomsnitt|Storlek på en kö/ett ämne i byte.|Entitetsnamnet|
|SuccessfulRequests|No|Slutförda förfrågningar|Antal|Totalt|Totalt antal slutförda begär Anden för ett namn område|EntityName, OperationResult under pågående|
|ThrottledRequests|No|Begränsade förfrågningar.|Antal|Totalt|Begränsade begär Anden för Microsoft. Service Bus.|EntityName, OperationResult under pågående|
|UserErrors|No|Användarfel.|Antal|Totalt|Användar fel för Microsoft. Service Bus.|EntityName, OperationResult under pågående|
|WSXNS|No|Minnes användning (inaktuell)|Procent|Maximal|Minnes användnings mått för Service Bus Premium-namnrymd. Måttet är föråldrat. Använd minnes användnings måttet (NamespaceMemoryUsage) i stället.|Replik|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft. ServiceFabricMesh/program

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ActualCpu|No|ActualCpu|Antal|Genomsnitt|Faktisk processor användning i Milli-kärnor|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory|No|ActualMemory|Byte|Genomsnitt|Faktisk minnes användning i MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedCpu|No|AllocatedCpu|Antal|Genomsnitt|CPU allokerad till den här behållaren i Milli-kärnor|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedMemory|No|AllocatedMemory|Byte|Genomsnitt|Minne som allokerats till den här behållaren i MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ApplicationStatus|No|ApplicationStatus|Antal|Genomsnitt|Status för Service Fabric nätprogram|ApplicationName, status|
|Container status|No|Container status|Antal|Genomsnitt|Status för behållaren i Service Fabric nätprogram|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, status|
|CpuUtilization|No|CpuUtilization|Procent|Genomsnitt|Användning av CPU för den här behållaren i procent av AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|MemoryUtilization|No|MemoryUtilization|Procent|Genomsnitt|Användning av CPU för den här behållaren i procent av AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|RestartCount|No|RestartCount|Antal|Genomsnitt|Antal omstarter för en behållare i Service Fabric nätprogram|ApplicationName, status, ServiceName, ServiceReplicaName, CodePackageName|
|ServiceReplicaStatus|No|ServiceReplicaStatus|Antal|Genomsnitt|Hälso status för en tjänst replik i Service Fabric nätprogram|ApplicationName, status, ServiceName, ServiceReplicaName|
|ServiceStatus|No|ServiceStatus|Antal|Genomsnitt|Hälso status för en tjänst i Service Fabric nätprogram|ApplicationName, status, ServiceName|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft. SignalRService/SignalR

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ConnectionCount|Yes|Antal anslutningar|Antal|Maximal|Mängden användar anslutning.|Slutpunkt|
|InboundTraffic|Yes|Inkommande trafik|Byte|Totalt|Inkommande trafik trafik för tjänsten|Inga dimensioner|
|MessageCount|Yes|Antal meddelanden|Antal|Totalt|Den totala mängden meddelanden.|Inga dimensioner|
|OutboundTraffic|Yes|Utgående trafik|Byte|Totalt|Utgående trafik för tjänsten|Inga dimensioner|
|SystemErrors|Yes|Systemfel|Procent|Maximal|Procent andelen system fel|Inga dimensioner|
|UserErrors|Yes|Användar fel|Procent|Maximal|Procent andel användar fel|Inga dimensioner|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft. SQL/managedInstances

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|avg_cpu_percent|Yes|Genomsnittlig CPU-procent|Procent|Genomsnitt|Genomsnittlig CPU-procent|Inga dimensioner|
|io_bytes_read|Yes|Lästa IO-byte|Byte|Genomsnitt|Lästa IO-byte|Inga dimensioner|
|io_bytes_written|Yes|Skrivna IO-byte|Byte|Genomsnitt|Skrivna IO-byte|Inga dimensioner|
|io_requests|Yes|Antal IO-begäranden|Antal|Genomsnitt|Antal IO-begäranden|Inga dimensioner|
|reserved_storage_mb|Yes|Reserverat lagrings utrymme|Antal|Genomsnitt|Reserverat lagrings utrymme|Inga dimensioner|
|storage_space_used_mb|Yes|Använt lagrings utrymme|Antal|Genomsnitt|Använt lagrings utrymme|Inga dimensioner|
|virtual_core_count|Yes|Antal virtuella kärnor|Antal|Genomsnitt|Antal virtuella kärnor|Inga dimensioner|


## <a name="microsoftsqlserversdatabases"></a>Microsoft. SQL/Servers/databaser

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|active_queries|Yes|Aktiva frågor|Antal|Totalt|Aktiva frågor för alla arbets belastnings grupper. Gäller endast för data lager.|Inga dimensioner|
|allocated_data_storage|Yes|Allokerat data utrymme|Byte|Genomsnitt|Allokerad data lagring. Ej tillämpligt för data lager.|Inga dimensioner|
|app_cpu_billed|Yes|App-CPU fakturerad|Antal|Totalt|App-CPU fakturerad. Gäller databaser utan server.|Inga dimensioner|
|app_cpu_percent|Yes|CPU-procent för app|Procent|Genomsnitt|CPU-procent för app. Gäller databaser utan server.|Inga dimensioner|
|app_memory_percent|Yes|Minnes procent för appar|Procent|Genomsnitt|Minnes procent för appar. Gäller databaser utan server.|Inga dimensioner|
|base_blob_size_bytes|Yes|Bas för Blob Storage-storlek|Byte|Maximal|Bas för Blob Storage-storlek. Gäller för storskaliga databaser.|Inga dimensioner|
|blocked_by_firewall|Yes|Blockerad av brandvägg|Antal|Totalt|Blockerad av brandvägg|Inga dimensioner|
|cache_hit_percent|Yes|Procent andel cacheträffar|Procent|Maximal|Procent av cacheträffar. Gäller endast för data lager.|Inga dimensioner|
|cache_used_percent|Yes|Procent andel som används|Procent|Maximal|Procent andel som används. Gäller endast för data lager.|Inga dimensioner|
|connection_failed|Yes|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inga dimensioner|
|connection_successful|Yes|Lyckade anslutningar|Antal|Totalt|Lyckade anslutningar|Inga dimensioner|
|cpu_limit|Yes|PROCESSOR gräns|Antal|Genomsnitt|PROCESSOR gräns. Gäller för vCore-baserade databaser.|Inga dimensioner|
|cpu_percent|Yes|CPU-procent|Procent|Genomsnitt|CPU-procent|Inga dimensioner|
|cpu_used|Yes|Använd CPU|Antal|Genomsnitt|Använd CPU. Gäller för vCore-baserade databaser.|Inga dimensioner|
|hamn|Yes|Dödlägen|Antal|Totalt|Låsningar. Ej tillämpligt för data lager.|Inga dimensioner|
|diff_backup_size_bytes|Yes|Lagrings storlek för differentiell säkerhets kopia|Byte|Maximal|Kumulativ lagrings storlek för differentiell säkerhets kopia. Gäller för vCore-baserade databaser. Gäller inte för storskaliga databaser.|Inga dimensioner|
|dtu_consumption_percent|Yes|DTU-procent|Procent|Genomsnitt|DTU-procent. Gäller för DTU-baserade databaser.|Inga dimensioner|
|dtu_limit|Yes|DTU-gräns|Antal|Genomsnitt|DTU-gräns. Gäller för DTU-baserade databaser.|Inga dimensioner|
|dtu_used|Yes|Använt DTU|Antal|Genomsnitt|DTU används. Gäller för DTU-baserade databaser.|Inga dimensioner|
|dwu_consumption_percent|Yes|DWU procent|Procent|Maximal|DWU procent. Gäller endast för data lager.|Inga dimensioner|
|dwu_limit|Yes|DWU-gräns|Antal|Maximal|DWU-gräns. Gäller endast för data lager.|Inga dimensioner|
|dwu_used|Yes|DWU som används|Antal|Maximal|DWU som används. Gäller endast för data lager.|Inga dimensioner|
|full_backup_size_bytes|Yes|Lagrings storlek för fullständig säkerhets kopia|Byte|Maximal|Ackumulerad lagrings storlek för fullständig säkerhets kopia. Gäller för vCore-baserade databaser. Gäller inte för storskaliga databaser.|Inga dimensioner|
|local_tempdb_usage_percent|Yes|Lokal tempdb-procent|Procent|Genomsnitt|Lokal tempdb-procent. Gäller endast för data lager.|Inga dimensioner|
|log_backup_size_bytes|Yes|Logg lagrings storlek för logg säkerhets kopia|Byte|Maximal|Sammanlagd lagrings storlek för logg säkerhets kopia. Gäller för vCore-baserade och storskaliga databaser.|Inga dimensioner|
|log_write_percent|Yes|Logg IO-procent|Procent|Genomsnitt|Logg IO-procent. Ej tillämpligt för data lager.|Inga dimensioner|
|memory_usage_percent|Yes|Minnes procent|Procent|Maximal|Minnes procent. Gäller endast för data lager.|Inga dimensioner|
|physical_data_read_percent|Yes|Data IO-procent|Procent|Genomsnitt|Data IO-procent|Inga dimensioner|
|queued_queries|Yes|Köade frågor|Antal|Totalt|Köade frågor över alla arbets belastnings grupper. Gäller endast för data lager.|Inga dimensioner|
|sessions_percent|Yes|Sessioner i procent|Procent|Genomsnitt|Sessioner i procent. Ej tillämpligt för data lager.|Inga dimensioner|
|snapshot_backup_size_bytes|Yes|Lagrings storlek för ögonblicks bild säkerhets kopia|Byte|Maximal|Lagrings storlek för kumulativ ögonblicks bild. Gäller för storskaliga databaser.|Inga dimensioner|
|sqlserver_process_core_percent|Yes|SQL Server process kärn procent|Procent|Maximal|CPU-användning som en procent andel av SQL DB-processen. Ej tillämpligt för data lager.|Inga dimensioner|
|sqlserver_process_memory_percent|Yes|SQL Server process minne i procent|Procent|Maximal|Minnes användning som en procent andel av SQL DB-processen. Ej tillämpligt för data lager.|Inga dimensioner|
|storage|Yes|Använt data utrymme|Byte|Maximal|Använt data utrymme. Ej tillämpligt för data lager.|Inga dimensioner|
|storage_percent|Yes|Använt data utrymme i procent|Procent|Maximal|Data utrymme som används i procent. Ej tillämpligt för data lager eller storskaliga databaser.|Inga dimensioner|
|tempdb_data_size|Yes|Data fil storlek i tempdb i KB|Antal|Maximal|Utrymme som används i tempdb-datafiler i kilobyte. Ej tillämpligt för data lager.|Inga dimensioner|
|tempdb_log_size|Yes|TempDB-logg fils storlek kilobyte|Antal|Maximal|Utrymme som används i tempdb-transaktionshanteraren i kilobyte. Ej tillämpligt för data lager.|Inga dimensioner|
|tempdb_log_used_percent|Yes|Procent använt tempdb-logg|Procent|Maximal|Utrymme som används i procent i tempdb-transaktionshanteraren. logg filen. Ej tillämpligt för data lager.|Inga dimensioner|
|wlg_active_queries|Yes|Aktiva frågor för arbets belastnings grupp|Antal|Totalt|Aktiva frågor inom arbets belastnings gruppen. Gäller endast för data lager.|WorkloadGroupName, IsUserDefined|
|wlg_active_queries_timeouts|Yes|Fråge tids gränser för arbets belastnings grupp|Antal|Totalt|Frågor som har nått tids gränsen för arbets belastnings gruppen. Gäller endast för data lager.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_system_percent|Yes|Allokering av arbets belastnings grupp efter system procent|Procent|Maximal|Tilldelad procent andel resurser i förhållande till hela systemet per arbets belastnings grupp. Gäller endast för data lager.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|Yes|Allokering av arbets belastnings grupp per Cap-resurs procent|Procent|Maximal|Tilldelad procent andel resurser i förhållande till de angivna Cap-resurserna per arbets belastnings grupp. Gäller endast för data lager.|WorkloadGroupName, IsUserDefined|
|wlg_effective_cap_resource_percent|Yes|Effektiv tak resurs procent|Procent|Maximal|En hård gräns för procent andelen av resurser som tillåts för arbets belastnings gruppen, med hänsyn till den lägsta resurs procent som allokerats för andra arbets belastnings grupper. Gäller endast för data lager.|WorkloadGroupName, IsUserDefined|
|wlg_effective_min_resource_percent|Yes|Effektiv minsta resurs procent|Procent|Maximal|Lägsta procent andel av resurser som är reserverade och isolerade för arbets belastnings gruppen, med hänsyn till den lägsta Service nivån. Gäller endast för data lager.|WorkloadGroupName, IsUserDefined|
|wlg_queued_queries|Yes|Köade frågor i arbets belastnings gruppen|Antal|Totalt|Köade frågor i arbets belastnings gruppen. Gäller endast för data lager.|WorkloadGroupName, IsUserDefined|
|workers_percent|Yes|Arbetare i procent|Procent|Genomsnitt|Arbetare i procent. Ej tillämpligt för data lager.|Inga dimensioner|
|xtp_storage_percent|Yes|In-Memory OLTP-lagring i procent|Procent|Genomsnitt|In-Memory OLTP-lagring i procent. Ej tillämpligt för data lager.|Inga dimensioner|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft. SQL/Servers/elasticPools

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|allocated_data_storage|Yes|Allokerat data utrymme|Byte|Genomsnitt|Allokerat data utrymme|Inga dimensioner|
|allocated_data_storage_percent|Yes|Allokerat data utrymme i procent|Procent|Maximal|Allokerat data utrymme i procent|Inga dimensioner|
|cpu_limit|Yes|PROCESSOR gräns|Antal|Genomsnitt|PROCESSOR gräns. Gäller vCore-baserade elastiska pooler.|Inga dimensioner|
|cpu_percent|Yes|CPU-procent|Procent|Genomsnitt|CPU-procent|Inga dimensioner|
|cpu_used|Yes|Använd CPU|Antal|Genomsnitt|Använd CPU. Gäller vCore-baserade elastiska pooler.|Inga dimensioner|
|database_allocated_data_storage|No|Allokerat data utrymme|Byte|Genomsnitt|Allokerat data utrymme|DatabaseResourceId|
|database_cpu_limit|No|PROCESSOR gräns|Antal|Genomsnitt|PROCESSOR gräns|DatabaseResourceId|
|database_cpu_percent|No|CPU-procent|Procent|Genomsnitt|CPU-procent|DatabaseResourceId|
|database_cpu_used|No|Använd CPU|Antal|Genomsnitt|Använd CPU|DatabaseResourceId|
|database_dtu_consumption_percent|No|DTU-procent|Procent|Genomsnitt|DTU-procent|DatabaseResourceId|
|database_eDTU_used|No|eDTU använt|Antal|Genomsnitt|eDTU använt|DatabaseResourceId|
|database_log_write_percent|No|Logg IO-procent|Procent|Genomsnitt|Logg IO-procent|DatabaseResourceId|
|database_physical_data_read_percent|No|Data IO-procent|Procent|Genomsnitt|Data IO-procent|DatabaseResourceId|
|database_sessions_percent|No|Sessioner i procent|Procent|Genomsnitt|Sessioner i procent|DatabaseResourceId|
|database_storage_used|No|Använt data utrymme|Byte|Genomsnitt|Använt data utrymme|DatabaseResourceId|
|database_workers_percent|No|Arbetare i procent|Procent|Genomsnitt|Arbetare i procent|DatabaseResourceId|
|dtu_consumption_percent|Yes|DTU-procent|Procent|Genomsnitt|DTU-procent. Gäller för DTU-baserade elastiska pooler.|Inga dimensioner|
|eDTU_limit|Yes|eDTU-gräns|Antal|Genomsnitt|eDTU-gräns. Gäller för DTU-baserade elastiska pooler.|Inga dimensioner|
|eDTU_used|Yes|eDTU använt|Antal|Genomsnitt|eDTU används. Gäller för DTU-baserade elastiska pooler.|Inga dimensioner|
|log_write_percent|Yes|Logg IO-procent|Procent|Genomsnitt|Logg IO-procent|Inga dimensioner|
|physical_data_read_percent|Yes|Data IO-procent|Procent|Genomsnitt|Data IO-procent|Inga dimensioner|
|sessions_percent|Yes|Sessioner i procent|Procent|Genomsnitt|Sessioner i procent|Inga dimensioner|
|sqlserver_process_core_percent|Yes|SQL Server process kärn procent|Procent|Maximal|CPU-användning som en procent andel av SQL DB-processen. Gäller elastiska pooler.|Inga dimensioner|
|sqlserver_process_memory_percent|Yes|SQL Server process minne i procent|Procent|Maximal|Minnes användning som en procent andel av SQL DB-processen. Gäller elastiska pooler.|Inga dimensioner|
|storage_limit|Yes|Maximal datastorlek|Byte|Genomsnitt|Maximal datastorlek|Inga dimensioner|
|storage_percent|Yes|Använt data utrymme i procent|Procent|Genomsnitt|Använt data utrymme i procent|Inga dimensioner|
|storage_used|Yes|Använt data utrymme|Byte|Genomsnitt|Använt data utrymme|Inga dimensioner|
|tempdb_data_size|Yes|Data fil storlek i tempdb i KB|Antal|Maximal|Utrymme som används i tempdb-datafiler i kilobyte.|Inga dimensioner|
|tempdb_log_size|Yes|TempDB-logg fils storlek kilobyte|Antal|Maximal|Utrymme som används i tempdb-transaktionshanteraren i kilobyte.|Inga dimensioner|
|tempdb_log_used_percent|Yes|Procent använt tempdb-logg|Procent|Maximal|Procent andel utrymme som används i tempdb-transaktionsloggen logg filen|Inga dimensioner|
|workers_percent|Yes|Arbetare i procent|Procent|Genomsnitt|Arbetare i procent|Inga dimensioner|
|xtp_storage_percent|Yes|In-Memory OLTP-lagring i procent|Procent|Genomsnitt|In-Memory OLTP-lagring i procent|Inga dimensioner|


## <a name="microsoftstoragestorageaccounts"></a>Microsoft. Storage/storageAccounts

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Yes|Tillgänglighet|Procent|Genomsnitt|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|
|Utgående|Yes|Utgående|Byte|Totalt|Mängden utgående data. Det här antalet inkluderar utgående data från den externa klienten från Azure Storage och utgående i Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|Ingress|Yes|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Yes|Lyckad E2E-svarstid|Millisekunder|Genomsnitt|Genomsnittlig svars tid från slut punkt till slut punkt för lyckade begär Anden som gjorts till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Yes|Lyckad Server svars tid|Millisekunder|Genomsnitt|Den genomsnittliga tiden det tar för Azure Storage att bearbeta en lyckad begäran. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ av typ, ApiName, autentisering|
|Transaktioner|Yes|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|
|UsedCapacity|No|Använd kapacitet|Byte|Genomsnitt|Mängden lagrings utrymme som används av lagrings kontot. För standardlagringskonton är det summan av den kapacitet som används av blobar, filer och köer. För Premium Storage-konton och Blob Storage-konton är det samma som BlobCapacity eller FileCapacity.|Inga dimensioner|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft. Storage/storageAccounts/blobServices

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Yes|Tillgänglighet|Procent|Genomsnitt|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|
|BlobCapacity|No|BLOB-kapacitet|Byte|Genomsnitt|Mängden lagrings utrymme som används av lagrings kontots Blob Service i byte.|BlobType,-nivå|
|BlobCount|No|BLOB-antal|Antal|Genomsnitt|Antalet BLOB-objekt som lagras i lagrings kontot.|BlobType,-nivå|
|BlobProvisionedSize|No|BLOB-allokerad storlek|Byte|Genomsnitt|Mängden lagrings utrymme som har allokerats i lagrings kontots Blob Service i byte.|BlobType,-nivå|
|ContainerCount|Yes|Antal BLOB-behållare|Antal|Genomsnitt|Antalet behållare i lagrings kontot.|Inga dimensioner|
|Utgående|Yes|Utgående|Byte|Totalt|Mängden utgående data. Det här antalet inkluderar utgående data från den externa klienten från Azure Storage och utgående i Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|IndexCapacity|No|Index kapacitet|Byte|Genomsnitt|Mängden lagrings utrymme som används av Azure Data Lake Storage Gen2 hierarkiskt index.|Inga dimensioner|
|Ingress|Yes|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Yes|Lyckad E2E-svarstid|Millisekunder|Genomsnitt|Genomsnittlig svars tid från slut punkt till slut punkt för lyckade begär Anden som gjorts till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Yes|Lyckad Server svars tid|Millisekunder|Genomsnitt|Den genomsnittliga tiden det tar för Azure Storage att bearbeta en lyckad begäran. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ av typ, ApiName, autentisering|
|Transaktioner|Yes|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft. Storage/storageAccounts/fileServices

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Yes|Tillgänglighet|Procent|Genomsnitt|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ, ApiName, autentisering, FileShare|
|Utgående|Yes|Utgående|Byte|Totalt|Mängden utgående data. Det här antalet inkluderar utgående data från den externa klienten från Azure Storage och utgående i Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ, ApiName, autentisering, FileShare|
|FileCapacity|No|Fil kapacitet|Byte|Genomsnitt|Mängden fil lagring som används av lagrings kontot.|FileShare|
|FileCount|No|Antal filer|Antal|Genomsnitt|Antalet filer i lagrings kontot.|FileShare|
|FileShareCapacityQuota|No|Kapacitets kvot för fil resurs|Byte|Genomsnitt|Den övre gränsen för mängden lagrings utrymme som kan användas av Azure Files tjänsten i byte.|FileShare|
|FileShareCount|No|Antal fil resurser|Antal|Genomsnitt|Antalet fil resurser i lagrings kontot.|Inga dimensioner|
|FileShareProvisionedIOPS|No|Allokerad IOPS för fil resurs|Byte|Genomsnitt|Bas linje numret för etablerade IOPS för Premium-filresursen i Premium-filernas lagrings konto. Antalet beräknas baserat på resurs kapacitetens tilldelade storlek (kvot).|FileShare|
|FileShareSnapshotCount|No|Antal ögonblicks bilder av fil resurs|Antal|Genomsnitt|Antal ögonblicks bilder som finns på resursen i lagrings kontots fil tjänst.|FileShare|
|FileShareSnapshotSize|No|Storlek på fil resursens ögonblicks bild|Byte|Genomsnitt|Mängden lagrings utrymme som används av ögonblicks bilderna i lagrings kontots fil tjänst i byte.|FileShare|
|Ingress|Yes|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ, ApiName, autentisering, FileShare|
|SuccessE2ELatency|Yes|Lyckad E2E-svarstid|Millisekunder|Genomsnitt|Genomsnittlig svars tid från slut punkt till slut punkt för lyckade begär Anden som gjorts till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ, ApiName, autentisering, FileShare|
|SuccessServerLatency|Yes|Lyckad Server svars tid|Millisekunder|Genomsnitt|Den genomsnittliga tiden det tar för Azure Storage att bearbeta en lyckad begäran. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ, ApiName, autentisering, FileShare|
|Transaktioner|Yes|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering, FileShare|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft. Storage/storageAccounts/queueServices

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Yes|Tillgänglighet|Procent|Genomsnitt|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|
|Utgående|Yes|Utgående|Byte|Totalt|Mängden utgående data. Det här antalet inkluderar utgående data från den externa klienten från Azure Storage och utgående i Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|Ingress|Yes|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|QueueCapacity|Yes|Kös Kap kap.|Byte|Genomsnitt|Mängden Queue Storage som används av lagrings kontot.|Inga dimensioner|
|QueueCount|Yes|Antal köer|Antal|Genomsnitt|Antalet köer i lagrings kontot.|Inga dimensioner|
|QueueMessageCount|Yes|Antal meddelanden i kö|Antal|Genomsnitt|Antalet meddelanden som inte har förfallit i lagrings kontot.|Inga dimensioner|
|SuccessE2ELatency|Yes|Lyckad E2E-svarstid|Millisekunder|Genomsnitt|Genomsnittlig svars tid från slut punkt till slut punkt för lyckade begär Anden som gjorts till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Yes|Lyckad Server svars tid|Millisekunder|Genomsnitt|Den genomsnittliga tiden det tar för Azure Storage att bearbeta en lyckad begäran. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ av typ, ApiName, autentisering|
|Transaktioner|Yes|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft. Storage/storageAccounts/tableServices

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Yes|Tillgänglighet|Procent|Genomsnitt|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|
|Utgående|Yes|Utgående|Byte|Totalt|Mängden utgående data. Det här antalet inkluderar utgående data från den externa klienten från Azure Storage och utgående i Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|Ingress|Yes|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Yes|Lyckad E2E-svarstid|Millisekunder|Genomsnitt|Genomsnittlig svars tid från slut punkt till slut punkt för lyckade begär Anden som gjorts till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Yes|Lyckad Server svars tid|Millisekunder|Genomsnitt|Den genomsnittliga tiden det tar för Azure Storage att bearbeta en lyckad begäran. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ av typ, ApiName, autentisering|
|TableCapacity|Yes|Tabell kapacitet|Byte|Genomsnitt|Mängden tabell lagring som används av lagrings kontot.|Inga dimensioner|
|TableCount|Yes|Antal tabeller|Antal|Genomsnitt|Antalet tabeller i lagrings kontot.|Inga dimensioner|
|TableEntityCount|Yes|Antal tabell enheter|Antal|Genomsnitt|Antalet tabell enheter i lagrings kontot.|Inga dimensioner|
|Transaktioner|Yes|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|


## <a name="microsoftstoragecachecaches"></a>Microsoft. StorageCache/cacheminnen

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ClientIOPS|Yes|Totalt antal klient-IOPS|Antal|Genomsnitt|Frekvensen av klient fil åtgärder som bearbetas av cachen.|Inga dimensioner|
|ClientLatency|Yes|Genomsnittlig klient latens|Millisekunder|Genomsnitt|Genomsnittlig svars tid för klient fil åtgärder till cacheminnet.|Inga dimensioner|
|ClientLockIOPS|Yes|Klient lås IOPS|CountPerSecond|Genomsnitt|Klient fil låsnings åtgärder per sekund.|Inga dimensioner|
|ClientMetadataReadIOPS|Yes|Lästa IOPS för klientens metadata|CountPerSecond|Genomsnitt|Frekvensen av klient fil åtgärder som skickas till cachen, exklusive data läsningar som inte ändrar beständigt tillstånd.|Inga dimensioner|
|ClientMetadataWriteIOPS|Yes|Klientens metadata Skriv IOPS|CountPerSecond|Genomsnitt|Frekvensen av klient fil åtgärder som skickas till cachen, exklusive data skrivningar, som ändrar beständigt tillstånd.|Inga dimensioner|
|ClientReadIOPS|Yes|Klient läsnings-IOPS|CountPerSecond|Genomsnitt|Klient Läs åtgärder per sekund.|Inga dimensioner|
|ClientReadThroughput|Yes|Genomsnittligt cache-läst data flöde|BytesPerSecond|Genomsnitt|Överföringshastighet för klient läsnings data.|Inga dimensioner|
|ClientWriteIOPS|Yes|Klient skrivnings-IOPS|CountPerSecond|Genomsnitt|Klient skrivnings åtgärder per sekund.|Inga dimensioner|
|ClientWriteThroughput|Yes|Genomsnittligt cacheminne Skriv data flöde|BytesPerSecond|Genomsnitt|Data överförings takt för klient skrivning.|Inga dimensioner|
|StorageTargetAsyncWriteThroughput|Yes|StorageTarget asynkron skrivning av data flöde|BytesPerSecond|Genomsnitt|Hastigheten cachen skriver data asynkront till en viss StorageTarget. Detta är Opportunistic skrivningar som inte gör att klienter blockerar.|StorageTarget|
|StorageTargetFillThroughput|Yes|StorageTarget Fill-genomflöde|BytesPerSecond|Genomsnitt|Hastigheten som cachen läser data från StorageTarget för att hantera ett cache-missar.|StorageTarget|
|StorageTargetHealth|Yes|Lagrings mål hälsa|Antal|Genomsnitt|Booleska resultat för anslutnings test mellan cache-och lagrings målen.|Inga dimensioner|
|StorageTargetIOPS|Yes|Totalt antal StorageTarget IOPS|Antal|Genomsnitt|Frekvensen för alla fil åtgärder som cachen skickar till en viss StorageTarget.|StorageTarget|
|StorageTargetLatency|Yes|StorageTarget-svars tid|Millisekunder|Genomsnitt|Genomsnittlig fördröjning för fördröjning av alla fil åtgärder som cachen skickar till en partricular-StorageTarget.|StorageTarget|
|StorageTargetMetadataReadIOPS|Yes|StorageTarget metadata Läs IOPS|CountPerSecond|Genomsnitt|Frekvensen för fil åtgärder som inte ändrar beständigt tillstånd, och som utesluter Läs åtgärden, som cachen skickar till en viss StorageTarget.|StorageTarget|
|StorageTargetMetadataWriteIOPS|Yes|StorageTarget metadata Skriv IOPS|CountPerSecond|Genomsnitt|Frekvensen för fil åtgärder som ändrar beständigt tillstånd och exklusive Skriv åtgärden, som cachen skickar till en viss StorageTarget.|StorageTarget|
|StorageTargetReadAheadThroughput|Yes|StorageTarget Read i förväg genom strömning|BytesPerSecond|Genomsnitt|Den hastighet med vilken cache-opportunisticly läser data från StorageTarget.|StorageTarget|
|StorageTargetReadIOPS|Yes|StorageTarget läsa IOPS|CountPerSecond|Genomsnitt|Frekvensen för fil läsnings åtgärder som cachen skickar till en viss StorageTarget.|StorageTarget|
|StorageTargetSyncWriteThroughput|Yes|StorageTarget synkront Skriv data flöde|BytesPerSecond|Genomsnitt|Hastigheten cachen skriver data synkront till en viss StorageTarget. Detta är skrivningar som gör att klienter blockerar.|StorageTarget|
|StorageTargetTotalReadThroughput|Yes|StorageTarget total Read-genomflöde|BytesPerSecond|Genomsnitt|Den totala taxan som cachen läser data från en viss StorageTarget.|StorageTarget|
|StorageTargetTotalWriteThroughput|Yes|StorageTarget totalt Skriv data flöde|BytesPerSecond|Genomsnitt|Den totala taxan som cachen skriver data till en viss StorageTarget.|StorageTarget|
|StorageTargetWriteIOPS|Yes|StorageTarget Skriv IOPS|Antal|Genomsnitt|Hastigheten för fil skrivnings åtgärder som cachen skickar till en viss StorageTarget.|StorageTarget|
|Drifttid|Yes|Drifttid|Antal|Genomsnitt|Booleska resultat för anslutnings test mellan cache-och övervaknings systemet.|Inga dimensioner|


## <a name="microsoftstoragesyncstoragesyncservices"></a>Microsoft. storagesync/storageSyncServices

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ServerSyncSessionResult|Yes|Resultat av Sync-session|Antal|Genomsnitt|Mått som loggar värdet 1 varje gång Server slut punkten slutför en Sync-session med moln slut punkten|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Yes|Byte som har synkroniserats|Byte|Totalt|Total fil storlek överförd för Sync-sessioner|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncRecalledNetworkBytesByApplication|Yes|Återställnings storlek för moln skikt per program|Byte|Totalt|Storlek på data som återkallas av program|SyncGroupName, ServerName, ApplicationName|
|StorageSyncRecalledTotalNetworkBytes|Yes|Återställnings storlek för moln nivå|Byte|Totalt|Storlek på data som återkallas|SyncGroupName, ServerName|
|StorageSyncRecallIOTotalSizeBytes|Yes|Återkalla moln nivå|Byte|Totalt|Total storlek på data som återkallas av servern|ServerName|
|StorageSyncRecallThroughputBytesPerSecond|Yes|Återkalla data flöde för moln nivå|BytesPerSecond|Genomsnitt|Storlek på data återkallande data flöde|SyncGroupName, ServerName|
|StorageSyncServerHeartbeat|Yes|Status för server online|Antal|Maximal|Mått som loggar värdet 1 varje gång resigtered-servern registrerar ett pulsslag med moln slut punkten|ServerName|
|StorageSyncSyncSessionAppliedFilesCount|Yes|Synkroniserade filer|Antal|Totalt|Antal filer som har synkroniserats|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Yes|Filer som inte synkroniseras|Antal|Totalt|Antal filer som inte kunde synkroniseras|SyncGroupName, ServerEndpointName, SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>Microsoft. storagesync/storageSyncServices/registeredServers

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ServerHeartbeat|Yes|Status för server online|Antal|Maximal|Mått som loggar värdet 1 varje gång resigtered-servern registrerar ett pulsslag med moln slut punkten|ServerResourceId, ServerName|
|ServerRecallIOTotalSizeBytes|Yes|Återkalla moln nivå|Byte|Totalt|Total storlek på data som återkallas av servern|ServerResourceId, ServerName|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>Microsoft. storagesync/storageSyncServices/syncGroups

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|SyncGroupBatchTransferredFileBytes|Yes|Byte som har synkroniserats|Byte|Totalt|Total fil storlek överförd för Sync-sessioner|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionAppliedFilesCount|Yes|Synkroniserade filer|Antal|Totalt|Antal filer som har synkroniserats|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|Yes|Filer som inte synkroniseras|Antal|Totalt|Antal filer som inte kunde synkroniseras|SyncGroupName, ServerEndpointName, SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ServerEndpointBatchTransferredFileBytes|Yes|Byte som har synkroniserats|Byte|Totalt|Total fil storlek överförd för Sync-sessioner|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionAppliedFilesCount|Yes|Synkroniserade filer|Antal|Totalt|Antal filer som har synkroniserats|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|Yes|Filer som inte synkroniseras|Antal|Totalt|Antal filer som inte kunde synkroniseras|ServerEndpointName, SyncDirection|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft. StreamAnalytics/streamingjobs

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|AMLCalloutFailedRequests|Yes|Misslyckade funktions begär Anden|Antal|Totalt|Misslyckade funktions begär Anden|LogicalName, PartitionId|
|AMLCalloutInputEvents|Yes|Funktions händelser|Antal|Totalt|Funktions händelser|LogicalName, PartitionId|
|AMLCalloutRequests|Yes|Funktions begär Anden|Antal|Totalt|Funktions begär Anden|LogicalName, PartitionId|
|ConversionErrors|Yes|Data konverterings fel|Antal|Totalt|Data konverterings fel|LogicalName, PartitionId|
|DeserializationError|Yes|Fel vid deserialisering av indataport|Antal|Totalt|Fel vid deserialisering av indataport|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|Yes|Händelser som inte är i ordning|Antal|Totalt|Händelser som inte är i ordning|LogicalName, PartitionId|
|EarlyInputEvents|Yes|Tidiga ingångs händelser|Antal|Totalt|Tidiga ingångs händelser|LogicalName, PartitionId|
|Fel|Yes|Körnings fel|Antal|Totalt|Körnings fel|LogicalName, PartitionId|
|InputEventBytes|Yes|Inkommande händelse-byte|Byte|Totalt|Inkommande händelse-byte|LogicalName, PartitionId|
|InputEvents|Yes|Inmatade händelser|Antal|Totalt|Inmatade händelser|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|Yes|Eftersläpande inloggade ingångs händelser|Antal|Maximal|Eftersläpande inloggade ingångs händelser|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|Yes|Mottagna inmatade källor|Antal|Totalt|Mottagna inmatade källor|LogicalName, PartitionId|
|LateInputEvents|Yes|Sena ingångs händelser|Antal|Totalt|Sena ingångs händelser|LogicalName, PartitionId|
|OutputEvents|Yes|Utgående händelser|Antal|Totalt|Utgående händelser|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|Yes|Fördröjning för vattenstämpel|Sekunder|Maximal|Fördröjning för vattenstämpel|LogicalName, PartitionId|
|ResourceUtilization|Yes|SU%-användning|Procent|Maximal|SU%-användning|LogicalName, PartitionId|


## <a name="microsoftsynapseworkspaces"></a>Microsoft. Synapse/arbets ytor

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|BuiltinSqlPoolDataProcessedBytes|No|Bearbetade data (byte)|Byte|Totalt|Mängden data som bearbetas av frågor|Inga dimensioner|
|BuiltinSqlPoolLoginAttempts|No|Inloggnings försök|Antal|Totalt|Antal inloggnings försök som distribuerats eller misslyckats|Resultat|
|BuiltinSqlPoolRequestsEnded|No|Slutförda begär Anden|Antal|Totalt|Antal begär Anden som lyckades, misslyckades eller avbröts|Resultat|
|IntegrationActivityRunsEnded|No|Aktivitets körningar avslutad|Antal|Totalt|Antal integrerings aktiviteter som lyckades, misslyckades eller avbröts|Resultat, FailureType, aktivitet, ActivityType, pipeline|
|IntegrationPipelineRunsEnded|No|Pipelinen körs avslutad|Antal|Totalt|Antal integrations pipelines körningar som lyckades, misslyckades eller avbröts|Resultat, FailureType, pipeline|
|IntegrationTriggerRunsEnded|No|Utlösare körs avslutade|Antal|Totalt|Antal integrerings utlösare som har lyckats, misslyckats eller avbrutits|Resultat, FailureType, utlösare|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft. Synapse/arbets ytor/bigDataPools

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|BigDataPoolAllocatedCores|No|Virtuella kärnor tilldelas|Antal|Maximal|Allokerade virtuella kärnor för en Apache Spark pool|SubmitterId|
|BigDataPoolAllocatedMemory|No|Allokerat minne (GB)|Antal|Maximal|Allokerat minne för apach Spark-pool (GB)|SubmitterId|
|BigDataPoolApplicationsActive|No|Aktiva Apache Spark program|Antal|Maximal|Totalt antal aktiva Apache Spark pool program|JobState|
|BigDataPoolApplicationsEnded|No|Avslutade Apache Spark program|Antal|Totalt|Antalet program för Apache Spark-pool avslutad|JobType, JobResult|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft. Synapse/arbets ytor/sqlPools

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ActiveQueries|No|Aktiva frågor|Antal|Totalt|Aktiva frågor. Om du använder det här måttet ofiltrerad och unsplit visas alla aktiva frågor som körs i systemet|IsUserDefined|
|AdaptiveCacheHitPercent|No|Procentuellt antal träffar i adaptiv cache|Procent|Maximal|Mäter hur bra arbets belastningar använder adaptiv cache. Använd det här måttet med värdet för cache träff i procent för att avgöra om du vill skala för ytterligare kapacitet eller köra arbets belastningar på nytt för att utföra cachen|Inga dimensioner|
|AdaptiveCacheUsedPercent|No|Procentuell användning av adaptiv cache|Procent|Maximal|Mäter hur bra arbets belastningar använder adaptiv cache. Använd det här måttet med den cachelagrade procentens mått för att avgöra om du vill skala för ytterligare kapacitet eller köra arbets belastningar på nytt för att utföra cachen|Inga dimensioner|
|Anslutningar|Yes|Anslutningar|Antal|Totalt|Totalt antal inloggningar till SQL-poolen|Resultat|
|ConnectionsBlockedByFirewall|No|Anslutningar blockerade av brand väggen|Antal|Totalt|Antal anslutningar blockeras av brand Väggs regler. Gå tillbaka till principer för åtkomst kontroll för SQL-poolen och övervaka dessa anslutningar om antalet är högt|Inga dimensioner|
|CPUPercent|No|Procent andel processor användning|Procent|Maximal|CPU-användning över alla noder i SQL-poolen|Inga dimensioner|
|DWULimit|No|DWU-gräns|Antal|Maximal|SQL-poolens service nivå mål|Inga dimensioner|
|DWUUsed|No|DWU som används|Antal|Maximal|Representerar en övergripande representation av användningen i SQL-poolen. Uppmätt av DWU-gränsen * DWU procent|Inga dimensioner|
|DWUUsedPercent|No|Procent Använd DWU|Procent|Maximal|Representerar en övergripande representation av användningen i SQL-poolen. Mäts genom att dra max värdet mellan CPU-procent och data IO-procent|Inga dimensioner|
|LocalTempDBUsedPercent|No|Lokal tempdb Använd procent|Procent|Maximal|Lokal tempdb-användning över alla Compute-noder – värden genereras var femte minut|Inga dimensioner|
|MemoryUsedPercent|No|Använt minne i procent|Procent|Maximal|Minnes användning för alla noder i SQL-poolen|Inga dimensioner|
|QueuedQueries|No|Köade frågor|Antal|Totalt|Antal sammanställda begär anden i kö efter att max gränsen för samtidighet nåddes|IsUserDefined|
|WLGActiveQueries|No|Aktiva frågor för arbets belastnings grupp|Antal|Totalt|Aktiva frågor i arbets belastnings gruppen. Om du använder det här måttet ofiltrerad och unsplit visas alla aktiva frågor som körs i systemet|IsUserDefined, WorkloadGroup|
|WLGActiveQueriesTimeouts|No|Fråge tids gränser för arbets belastnings grupp|Antal|Totalt|Frågor för arbets belastnings gruppen som har nått tids gränsen. Tids gränser för frågor som rapporteras av detta mått sker bara när frågan har börjat köras (den innehåller inte vänte tid på grund av låsning eller väntande resurs avbrott)|IsUserDefined, WorkloadGroup|
|WLGAllocationByEffectiveCapResourcePercent|No|Allokering av arbets belastnings grupper efter max resurs procent|Procent|Maximal|Visar procent tilldelningen av resurser i förhållande till den effektiva Kap resurs procenten per arbets belastnings grupp. Detta mått ger arbets belastnings gruppens effektiva användning|IsUserDefined, WorkloadGroup|
|WLGAllocationBySystemPercent|No|Allokering av arbets belastnings grupp efter system procent|Procent|Maximal|Procent beläggningen av resurser i förhållande till hela systemet|IsUserDefined, WorkloadGroup|
|WLGEffectiveCapResourcePercent|No|Effektiv tak resurs procent|Procent|Maximal|Den effektiva Kap resurs procenten för arbets belastnings gruppen. Om det finns andra arbets belastnings grupper med min_percentage_resource > 0 sänks effective_cap_percentage_resource proportionellt|IsUserDefined, WorkloadGroup|
|WLGEffectiveMinResourcePercent|No|Effektiv minsta resurs procent|Procent|Maximal|Inställningen för lägsta procent andel av resursen som tillåts överväger Service nivån och inställningarna för arbets belastnings gruppen. Effektiv min_percentage_resource kan justeras högre på lägre service nivåer|IsUserDefined, WorkloadGroup|
|WLGQueuedQueries|No|Köade frågor i arbets belastnings gruppen|Antal|Totalt|Antal sammanställda begär anden i kö efter att max gränsen för samtidighet nåddes|IsUserDefined, WorkloadGroup|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft. TimeSeriesInsights/miljöer

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|Yes|Inkommande mottagna byte|Byte|Totalt|Antal byte som lästs från alla händelse källor|Inga dimensioner|
|IngressReceivedInvalidMessages|Yes|Inkommande mottagna ogiltiga meddelanden|Antal|Totalt|Antal ogiltiga meddelanden som lästs från alla händelse källor för Event Hub eller IoT Hub|Inga dimensioner|
|IngressReceivedMessages|Yes|Ingress mottagna meddelanden|Antal|Totalt|Antal meddelanden som lästs från alla händelse källor för Event Hub eller IoT Hub|Inga dimensioner|
|IngressReceivedMessagesCountLag|Yes|Antal inkommande mottagna meddelanden antal fördröjning|Antal|Genomsnitt|Skillnad mellan sekvensnumret i det senaste köade meddelandet i en partition för händelse källan och sekvensnummer för meddelanden som bearbetas i ingress|Inga dimensioner|
|IngressReceivedMessagesTimeLag|Yes|Ta emot mottagna meddelanden tids fördröjning|Sekunder|Maximal|Skillnaden mellan den tid som meddelandet står i kö i händelse källan och tiden det bearbetas i ingress|Inga dimensioner|
|IngressStoredBytes|Yes|Inkommande lagrade byte|Byte|Totalt|Total storlek på händelser som har bearbetats och är tillgängliga för fråga|Inga dimensioner|
|IngressStoredEvents|Yes|Ingress lagrade händelser|Antal|Totalt|Antal förenklade händelser som har bearbetats och är tillgängliga för fråga|Inga dimensioner|
|WarmStorageMaxProperties|Yes|Maximalt antal egenskaper för varmt lagring|Antal|Maximal|Maximalt antal egenskaper som används av miljön för S1/S2 SKU och maximalt antal egenskaper som tillåts av varmt Arkiv för PAYG SKU|Inga dimensioner|
|WarmStorageUsedProperties|Yes|Egenskaper för varm lagring som används |Antal|Maximal|Antalet egenskaper som används av miljön för S1/S2 SKU och antalet egenskaper som används av varmt Arkiv för PAYG SKU|Inga dimensioner|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft. TimeSeriesInsights/miljöer/eventsources

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|Yes|Inkommande mottagna byte|Byte|Totalt|Antal byte som lästs från händelse källan|Inga dimensioner|
|IngressReceivedInvalidMessages|Yes|Inkommande mottagna ogiltiga meddelanden|Antal|Totalt|Antal ogiltiga meddelanden som lästs från händelse källan|Inga dimensioner|
|IngressReceivedMessages|Yes|Ingress mottagna meddelanden|Antal|Totalt|Antal meddelanden som lästs från händelse källan|Inga dimensioner|
|IngressReceivedMessagesCountLag|Yes|Antal inkommande mottagna meddelanden antal fördröjning|Antal|Genomsnitt|Skillnad mellan sekvensnumret i det senaste köade meddelandet i en partition för händelse källan och sekvensnummer för meddelanden som bearbetas i ingress|Inga dimensioner|
|IngressReceivedMessagesTimeLag|Yes|Ta emot mottagna meddelanden tids fördröjning|Sekunder|Maximal|Skillnaden mellan den tid som meddelandet står i kö i händelse källan och tiden det bearbetas i ingress|Inga dimensioner|
|IngressStoredBytes|Yes|Inkommande lagrade byte|Byte|Totalt|Total storlek på händelser som har bearbetats och är tillgängliga för fråga|Inga dimensioner|
|IngressStoredEvents|Yes|Ingress lagrade händelser|Antal|Totalt|Antal förenklade händelser som har bearbetats och är tillgängliga för fråga|Inga dimensioner|
|WarmStorageMaxProperties|Yes|Maximalt antal egenskaper för varmt lagring|Antal|Maximal|Maximalt antal egenskaper som används av miljön för S1/S2 SKU och maximalt antal egenskaper som tillåts av varmt Arkiv för PAYG SKU|Inga dimensioner|
|WarmStorageUsedProperties|Yes|Egenskaper för varm lagring som används |Antal|Maximal|Antalet egenskaper som används av miljön för S1/S2 SKU och antalet egenskaper som används av varmt Arkiv för PAYG SKU|Inga dimensioner|


## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft. VMwareCloudSimple/virtualMachines

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Lästa byte på disk|Yes|Lästa byte på disk|Byte|Totalt|Totalt disk data flöde på grund av Läs åtgärder under exempel perioden.|Inga dimensioner|
|Disk Läs åtgärder/SEK|Yes|Disk Läs åtgärder/SEK|CountPerSecond|Genomsnitt|Genomsnittligt antal Läs åtgärder i IO i föregående exempel period. Observera att dessa åtgärder kan vara varierande storlek.|Inga dimensioner|
|Disk-skrivna byte|Yes|Disk-skrivna byte|Byte|Totalt|Totalt disk data flöde på grund av Skriv åtgärder under exempel perioden.|Inga dimensioner|
|Disk skrivnings åtgärder/SEK|Yes|Disk skrivnings åtgärder/SEK|CountPerSecond|Genomsnitt|Genomsnittligt antal i/o-Skriv åtgärder i föregående exempel period. Observera att dessa åtgärder kan vara varierande storlek.|Inga dimensioner|
|DiskReadBytesPerSecond|Yes|Disk-lästa byte/s|BytesPerSecond|Genomsnitt|Genomsnittligt disk data flöde på grund av Läs åtgärder under exempel perioden.|Inga dimensioner|
|DiskReadLatency|Yes|Läs fördröjning för disk|Millisekunder|Genomsnitt|Total Läs fördröjning. Summan av Läs fördröjningen för enheten och kärnan.|Inga dimensioner|
|DiskReadOperations|Yes|Disk Läs åtgärder|Antal|Totalt|Antalet Läs åtgärder i IO i föregående exempel period. Observera att dessa åtgärder kan vara varierande storlek.|Inga dimensioner|
|DiskWriteBytesPerSecond|Yes|Disk-skrivna byte/s|BytesPerSecond|Genomsnitt|Genomsnittligt disk data flöde på grund av Skriv åtgärder under exempel perioden.|Inga dimensioner|
|DiskWriteLatency|Yes|Skriv fördröjning för disk|Millisekunder|Genomsnitt|Total Skriv fördröjning. Summan av Skriv fördröjningarna för enheten och kärnan.|Inga dimensioner|
|DiskWriteOperations|Yes|Disk skrivnings åtgärder|Antal|Totalt|Antalet i/o-Skriv åtgärder i föregående exempel period. Observera att dessa åtgärder kan vara varierande storlek.|Inga dimensioner|
|MemoryActive|Yes|Aktivt minne|Byte|Genomsnitt|Mängden minne som används av den virtuella datorn i det tidigare lilla tids fönstret. Detta är "true"-antalet hur mycket minne som den virtuella datorn för närvarande behöver. Ytterligare, oanvänt minne kan växlas ut eller visas utan att påverka gästens prestanda.|Inga dimensioner|
|MemoryGranted|Yes|Beviljat minne|Byte|Genomsnitt|Mängden minne som har beviljats för den virtuella datorn av värden. Minnet beviljas inte för värden förrän den är påslagen en gång och beviljat minne kan växlas ut eller visas borta om VMkernel behöver minnet.|Inga dimensioner|
|MemoryUsed|Yes|Använt minne|Byte|Genomsnitt|Mängden dator minne som används av den virtuella datorn.|Inga dimensioner|
|Nätverk – inkommande|Yes|Nätverk – inkommande|Byte|Totalt|Totalt nätverks data flöde för mottagen trafik.|Inga dimensioner|
|Nätverk – utgående|Yes|Nätverk – utgående|Byte|Totalt|Totalt nätverks data flöde för överförd trafik.|Inga dimensioner|
|NetworkInBytesPerSecond|Yes|Nätverk i byte/s|BytesPerSecond|Genomsnitt|Genomsnittligt nätverks data flöde för mottagen trafik.|Inga dimensioner|
|NetworkOutBytesPerSecond|Yes|Nätverks-out-byte/SEK|BytesPerSecond|Genomsnitt|Genomsnittligt nätverks data flöde för överförd trafik.|Inga dimensioner|
|Processorprocentandel|Yes|Processorprocentandel|Procent|Genomsnitt|PROCESSOR användningen. Det här värdet rapporteras med 100% som representerar alla processor kärnor i systemet. Till exempel är en 2-vägs virtuell dator som använder 50% av ett system med fyra kärnor fullständigt med två kärnor.|Inga dimensioner|
|PercentageCpuReady|Yes|Procent andel CPU klar|Millisekunder|Totalt|Ready Time är den tid som väntar på att CPU: er ska bli tillgängliga under det tidigare uppdaterings intervallet.|Inga dimensioner|


## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft. Web/hostingEnvironments/multiRolePools

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|ActiveRequests|Yes|Aktiva begär Anden (inaktuella)|Antal|Totalt|Aktiva begär Anden|Instans|
|AverageResponseTime|Yes|Genomsnittlig svars tid (inaktuell)|Sekunder|Genomsnitt|Genomsnittlig tid det tar för klient delen att betjäna begär Anden, i sekunder.|Instans|
|BytesReceived|Yes|Data i|Byte|Totalt|Den genomsnittliga inkommande bandbredd som används på alla klient delar, i MiB.|Instans|
|Bytes sent|Yes|Data ut|Byte|Totalt|Genomsnittlig inkommande bandbredd som används på alla klient delar i MiB.|Instans|
|CpuPercentage|Yes|CPU-procent|Procent|Genomsnitt|Den genomsnittliga CPU som används i alla instanser av klient delen.|Instans|
|DiskQueueLength|Yes|Diskkölängd|Antal|Genomsnitt|Genomsnittligt antal begär Anden om läsning och skrivning som ställts i kö för lagring. En hög diskkölängd är en indikation på en app som kan vara långsam på grund av överdriven disk-I/O.|Instans|
|Http101|Yes|Http 101|Antal|Totalt|Antalet förfrågningar som resulterar i HTTP-statuskod 101.|Instans|
|Http2xx|Yes|Http-2xx|Antal|Totalt|Antal begär Anden som resulterade i HTTP-status kod = 200 men < 300.|Instans|
|Http3xx|Yes|Http-3xx|Antal|Totalt|Antal begär Anden som resulterade i HTTP-status kod = 300 men < 400.|Instans|
|Http401|Yes|Http 401|Antal|Totalt|Antal begär Anden som resulterar i status kod för HTTP 401.|Instans|
|Http403|Yes|Http 403|Antal|Totalt|Antal begär Anden som resulterar i status kod för HTTP 403.|Instans|
|Http404|Yes|Http 404|Antal|Totalt|Antal begär Anden som resulterar i status kod för HTTP 404.|Instans|
|Http406|Yes|Http 406|Antal|Totalt|Antal begär Anden som resulterar i status kod för HTTP 406.|Instans|
|Http4xx|Yes|Http-4xx|Antal|Totalt|Antal begär Anden som resulterade i HTTP-status kod = 400 men < 500.|Instans|
|Http5xx|Yes|Http-serverfel|Antal|Totalt|Antal begär Anden som resulterade i HTTP-status kod = 500 men < 600.|Instans|
|HttpQueueLength|Yes|Längd på http-kö|Antal|Genomsnitt|Genomsnittligt antal HTTP-begäranden som måste sitta i kön innan de kunde uppfyllas. En hög eller ökande HTTP-Kölängd är ett symtom på en plan som är tungt belastad.|Instans|
|HttpResponseTime|Yes|Svars tid|Sekunder|Genomsnitt|Den tid det tar för klient delen att betjäna begär Anden, i sekunder.|Instans|
|LargeAppServicePlanInstances|Yes|Stora App Service planera arbetare|Antal|Genomsnitt|Stora App Service planera arbetare|Inga dimensioner|
|MediumAppServicePlanInstances|Yes|Medel App Service planera arbetare|Antal|Genomsnitt|Medel App Service planera arbetare|Inga dimensioner|
|MemoryPercentage|Yes|Minnes procent|Procent|Genomsnitt|Genomsnittligt minne som används i alla instanser av klient delen.|Instans|
|Begäranden|Yes|Begäranden|Antal|Totalt|Det totala antalet begär Anden oavsett den resulterande HTTP-statuskoden.|Instans|
|SmallAppServicePlanInstances|Yes|Små App Service planera arbetare|Antal|Genomsnitt|Små App Service planera arbetare|Inga dimensioner|
|TotalFrontEnds|Yes|Totalt antal klient delar|Antal|Genomsnitt|Totalt antal klient delar|Inga dimensioner|


## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft. Web/hostingEnvironments/workerPools

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|CpuPercentage|Yes|CPU-procent|Procent|Genomsnitt|Den genomsnittliga CPU som används i alla instanser av arbets gruppen.|Instans|
|MemoryPercentage|Yes|Minnes procent|Procent|Genomsnitt|Genomsnittligt minne som används i alla instanser av arbets gruppen.|Instans|
|WorkersAvailable|Yes|Tillgängliga arbetare|Antal|Genomsnitt|Tillgängliga arbetare|Inga dimensioner|
|WorkersTotal|Yes|Totalt antal arbetare|Antal|Genomsnitt|Totalt antal arbetare|Inga dimensioner|
|WorkersUsed|Yes|Använda arbetare|Antal|Genomsnitt|Använda arbetare|Inga dimensioner|


## <a name="microsoftwebserverfarms"></a>Microsoft. Web/Server grupper

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|BytesReceived|Yes|Data i|Byte|Totalt|Den genomsnittliga inkommande bandbredd som används för alla instanser av planen.|Instans|
|Bytes sent|Yes|Data ut|Byte|Totalt|Den genomsnittliga utgående bandbredd som används för alla instanser av planen.|Instans|
|CpuPercentage|Yes|CPU-procent|Procent|Genomsnitt|Den genomsnittliga CPU som används för alla instanser av planen.|Instans|
|DiskQueueLength|Yes|Diskkölängd|Antal|Genomsnitt|Genomsnittligt antal begär Anden om läsning och skrivning som ställts i kö för lagring. En hög diskkölängd är en indikation på en app som kan vara långsam på grund av överdriven disk-I/O.|Instans|
|HttpQueueLength|Yes|Längd på http-kö|Antal|Genomsnitt|Genomsnittligt antal HTTP-begäranden som måste sitta i kön innan de kunde uppfyllas. En hög eller ökande HTTP-Kölängd är ett symtom på en plan som är tungt belastad.|Instans|
|MemoryPercentage|Yes|Minnes procent|Procent|Genomsnitt|Genomsnittligt minne som används för alla instanser av planen.|Instans|
|SocketInboundAll|Yes|SocketInboundAll|Antal|Genomsnitt|SocketInboundAll|Instans|
|SocketLoopback|Yes|SocketLoopback|Antal|Genomsnitt|SocketLoopback|Instans|
|SocketOutboundAll|Yes|SocketOutboundAll|Antal|Genomsnitt|SocketOutboundAll|Instans|
|SocketOutboundEstablished|Yes|SocketOutboundEstablished|Antal|Genomsnitt|SocketOutboundEstablished|Instans|
|SocketOutboundTimeWait|Yes|SocketOutboundTimeWait|Antal|Genomsnitt|SocketOutboundTimeWait|Instans|
|TcpCloseWait|Yes|Väntan på TCP-stängning|Antal|Genomsnitt|Väntan på TCP-stängning|Instans|
|TcpClosing|Yes|TCP-stängning|Antal|Genomsnitt|TCP-stängning|Instans|
|TcpEstablished|Yes|TCP upprättad|Antal|Genomsnitt|TCP upprättad|Instans|
|TcpFinWait1|Yes|TCP räntetrans wait 1|Antal|Genomsnitt|TCP räntetrans wait 1|Instans|
|TcpFinWait2|Yes|TCP räntetrans wait 2|Antal|Genomsnitt|TCP räntetrans wait 2|Instans|
|TcpLastAck|Yes|TCP-senaste ack|Antal|Genomsnitt|TCP-senaste ack|Instans|
|TcpSynReceived|Yes|TCP-syn mottagen|Antal|Genomsnitt|TCP-syn mottagen|Instans|
|TcpSynSent|Yes|TCP-syn har skickats|Antal|Genomsnitt|TCP-syn har skickats|Instans|
|TcpTimeWait|Yes|Väntan på TCP-tid|Antal|Genomsnitt|Väntan på TCP-tid|Instans|


## <a name="microsoftwebsites"></a>Microsoft. Web/Sites

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|AppConnections|Yes|Anslutningar|Antal|Genomsnitt|Antalet kopplade socketar som är befintliga i sandbox (w3wp.exe och dess underordnade processer). En bunden socket skapas genom att anropa BIND ()/Connect ()-API: er och förblir kvar tills socketen stängs med CloseHandle ()/Closesocket ().|Instans|
|AverageMemoryWorkingSet|Yes|Genomsnittlig arbets mängd för minne|Byte|Genomsnitt|Den genomsnittliga mängden minne som används av appen, i megabyte (MiB).|Instans|
|AverageResponseTime|Yes|Genomsnittlig svars tid (inaktuell)|Sekunder|Genomsnitt|Genomsnittlig tid det tar för appen att betjäna begär Anden, i sekunder.|Instans|
|BytesReceived|Yes|Data i|Byte|Totalt|Mängden inkommande bandbredd som används av appen, i MiB.|Instans|
|Bytes sent|Yes|Data ut|Byte|Totalt|Mängden utgående bandbredd som används av appen, i MiB.|Instans|
|CpuTime|Yes|CPU-tid|Sekunder|Totalt|Mängden CPU som används av appen, i sekunder. För mer information om det här måttet. Se https://docs.microsoft.com/azure/app-service/web-sites-monitor#cpu-time-vs-cpu-percentage (processor tid jämfört med procent av processor).|Instans|
|CurrentAssemblies|Yes|Aktuella sammansättningar|Antal|Genomsnitt|Det aktuella antalet sammansättningar som har lästs in i alla AppDomains i det här programmet.|Instans|
|FileSystemUsage|Yes|Fil system användning|Byte|Genomsnitt|Procent andel av fil Systems kvoten som används av appen.|Inga dimensioner|
|FunctionExecutionCount|Yes|Funktions körnings antal|Antal|Totalt|Funktions körnings antal|Instans|
|FunctionExecutionUnits|Yes|Funktions körnings enheter|Antal|Totalt|Funktions körnings enheter|Instans|
|Gen0Collections|Yes|Skräp insamling för gen 0|Antal|Totalt|Antalet gånger som generation 0-objekt är skräp insamlat sedan program processen startades. Högre generations GC generationer omfattar all lägre generations-GC generationer.|Instans|
|Gen1Collections|Yes|Skräp insamling för gen 1|Antal|Totalt|Antalet gånger som generation 1-objekten är skräp insamlat sedan program processen startades. Högre generations GC generationer omfattar all lägre generations-GC generationer.|Instans|
|Gen2Collections|Yes|Gen 2 skräp insamling|Antal|Totalt|Antalet gånger som generation 2-objekt är skräp insamlat sedan program processen startades.|Instans|
|Konsolindataobjekt|Yes|Antal referenser|Antal|Genomsnitt|Det totala antalet handles som för närvarande är öppna av app-processen.|Instans|
|HealthCheckStatus|Yes|Hälso kontroll status|Antal|Genomsnitt|Hälso kontroll status|Instans|
|Http101|Yes|Http 101|Antal|Totalt|Antalet förfrågningar som resulterar i HTTP-statuskod 101.|Instans|
|Http2xx|Yes|Http-2xx|Antal|Totalt|Antal begär Anden som resulterade i HTTP-status kod = 200 men < 300.|Instans|
|Http3xx|Yes|Http-3xx|Antal|Totalt|Antal begär Anden som resulterade i HTTP-status kod = 300 men < 400.|Instans|
|Http401|Yes|Http 401|Antal|Totalt|Antal begär Anden som resulterar i status kod för HTTP 401.|Instans|
|Http403|Yes|Http 403|Antal|Totalt|Antal begär Anden som resulterar i status kod för HTTP 403.|Instans|
|Http404|Yes|Http 404|Antal|Totalt|Antal begär Anden som resulterar i status kod för HTTP 404.|Instans|
|Http406|Yes|Http 406|Antal|Totalt|Antal begär Anden som resulterar i status kod för HTTP 406.|Instans|
|Http4xx|Yes|Http-4xx|Antal|Totalt|Antal begär Anden som resulterade i HTTP-status kod = 400 men < 500.|Instans|
|Http5xx|Yes|Http-serverfel|Antal|Totalt|Antal begär Anden som resulterade i HTTP-status kod = 500 men < 600.|Instans|
|HttpResponseTime|Yes|Svars tid|Sekunder|Genomsnitt|Den tid det tar för appen att betjäna begär Anden, i sekunder.|Instans|
|IoOtherBytesPerSecond|Yes|I/o andra byte per sekund|BytesPerSecond|Totalt|Den hastighet med vilken app-processen utfärdar byte till I/O-åtgärder som inte omfattar data, till exempel kontroll åtgärder.|Instans|
|IoOtherOperationsPerSecond|Yes|Andra i/o-åtgärder per sekund|BytesPerSecond|Totalt|Den hastighet med vilken app-processen utfärdar I/O-åtgärder som inte är Läs-eller Skriv åtgärder.|Instans|
|IoReadBytesPerSecond|Yes|IO-lästa byte per sekund|BytesPerSecond|Totalt|Den hastighet med vilken appens process läser in byte från I/O-åtgärder.|Instans|
|IoReadOperationsPerSecond|Yes|IO-Läs åtgärder per sekund|BytesPerSecond|Totalt|Den hastighet med vilken app-processen utfärdar Läs-I/O-åtgärder.|Instans|
|IoWriteBytesPerSecond|Yes|Skrivna byte i i/o per sekund|BytesPerSecond|Totalt|Den hastighet med vilken appens process skriver byte till I/O-åtgärder.|Instans|
|IoWriteOperationsPerSecond|Yes|I/o-Skriv åtgärder per sekund|BytesPerSecond|Totalt|Den hastighet med vilken program processen utfärdar Skriv-I/O-åtgärder.|Instans|
|MemoryWorkingSet|Yes|Minnes arbets mängd|Byte|Genomsnitt|Den aktuella mängden minne som används av appen, i MiB.|Instans|
|PrivateBytes|Yes|Privata byte|Byte|Genomsnitt|Privata byte är den aktuella storleken i byte på minne som program processen har allokerat och som inte kan delas med andra processer.|Instans|
|Begäranden|Yes|Begäranden|Antal|Totalt|Det totala antalet begär Anden oavsett den resulterande HTTP-statuskoden.|Instans|
|RequestsInApplicationQueue|Yes|Begär anden i program kön|Antal|Genomsnitt|Antalet begär anden i program begär ande kön.|Instans|
|Konversation|Yes|Antal trådar|Antal|Genomsnitt|Antalet trådar som för närvarande är aktiva i program processen.|Instans|
|TotalAppDomains|Yes|Totalt antal app-domäner|Antal|Genomsnitt|Det aktuella antalet AppDomains som har lästs in i det här programmet.|Instans|
|TotalAppDomainsUnloaded|Yes|Totalt antal app-domäner som har inaktiverats|Antal|Genomsnitt|Det totala antalet inaktiverade tillämpnings domäner sedan programmet startades.|Instans|


## <a name="microsoftwebsitesslots"></a>Microsoft. Web/Sites/lotss

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|AppConnections|Yes|Anslutningar|Antal|Genomsnitt|Antalet kopplade socketar som är befintliga i sandbox (w3wp.exe och dess underordnade processer). En bunden socket skapas genom att anropa BIND ()/Connect ()-API: er och förblir kvar tills socketen stängs med CloseHandle ()/Closesocket ().|Instans|
|AverageMemoryWorkingSet|Yes|Genomsnittlig arbets mängd för minne|Byte|Genomsnitt|Den genomsnittliga mängden minne som används av appen, i megabyte (MiB).|Instans|
|AverageResponseTime|Yes|Genomsnittlig svars tid (inaktuell)|Sekunder|Genomsnitt|Genomsnittlig tid det tar för appen att betjäna begär Anden, i sekunder.|Instans|
|BytesReceived|Yes|Data i|Byte|Totalt|Mängden inkommande bandbredd som används av appen, i MiB.|Instans|
|Bytes sent|Yes|Data ut|Byte|Totalt|Mängden utgående bandbredd som används av appen, i MiB.|Instans|
|CpuTime|Yes|CPU-tid|Sekunder|Totalt|Mängden CPU som används av appen, i sekunder. För mer information om det här måttet. Se https://docs.microsoft.com/azure/app-service/web-sites-monitor#cpu-time-vs-cpu-percentage (processor tid jämfört med procent av processor).|Instans|
|CurrentAssemblies|Yes|Aktuella sammansättningar|Antal|Genomsnitt|Det aktuella antalet sammansättningar som har lästs in i alla AppDomains i det här programmet.|Instans|
|FileSystemUsage|Yes|Fil system användning|Byte|Genomsnitt|Procent andel av fil Systems kvoten som används av appen.|Inga dimensioner|
|FunctionExecutionCount|Yes|Funktions körnings antal|Antal|Totalt|Funktions körnings antal|Instans|
|FunctionExecutionUnits|Yes|Funktions körnings enheter|Antal|Totalt|Funktions körnings enheter|Instans|
|Gen0Collections|Yes|Skräp insamling för gen 0|Antal|Totalt|Antalet gånger som generation 0-objekt är skräp insamlat sedan program processen startades. Högre generations GC generationer omfattar all lägre generations-GC generationer.|Instans|
|Gen1Collections|Yes|Skräp insamling för gen 1|Antal|Totalt|Antalet gånger som generation 1-objekten är skräp insamlat sedan program processen startades. Högre generations GC generationer omfattar all lägre generations-GC generationer.|Instans|
|Gen2Collections|Yes|Gen 2 skräp insamling|Antal|Totalt|Antalet gånger som generation 2-objekt är skräp insamlat sedan program processen startades.|Instans|
|Konsolindataobjekt|Yes|Antal referenser|Antal|Genomsnitt|Det totala antalet handles som för närvarande är öppna av app-processen.|Instans|
|HealthCheckStatus|Yes|Hälso kontroll status|Antal|Genomsnitt|Hälso kontroll status|Instans|
|Http101|Yes|Http 101|Antal|Totalt|Antalet förfrågningar som resulterar i HTTP-statuskod 101.|Instans|
|Http2xx|Yes|Http-2xx|Antal|Totalt|Antal begär Anden som resulterade i HTTP-status kod = 200 men < 300.|Instans|
|Http3xx|Yes|Http-3xx|Antal|Totalt|Antal begär Anden som resulterade i HTTP-status kod = 300 men < 400.|Instans|
|Http401|Yes|Http 401|Antal|Totalt|Antal begär Anden som resulterar i status kod för HTTP 401.|Instans|
|Http403|Yes|Http 403|Antal|Totalt|Antal begär Anden som resulterar i status kod för HTTP 403.|Instans|
|Http404|Yes|Http 404|Antal|Totalt|Antal begär Anden som resulterar i status kod för HTTP 404.|Instans|
|Http406|Yes|Http 406|Antal|Totalt|Antal begär Anden som resulterar i status kod för HTTP 406.|Instans|
|Http4xx|Yes|Http-4xx|Antal|Totalt|Antal begär Anden som resulterade i HTTP-status kod = 400 men < 500.|Instans|
|Http5xx|Yes|Http-serverfel|Antal|Totalt|Antal begär Anden som resulterade i HTTP-status kod = 500 men < 600.|Instans|
|HttpResponseTime|Yes|Svars tid|Sekunder|Genomsnitt|Den tid det tar för appen att betjäna begär Anden, i sekunder.|Instans|
|IoOtherBytesPerSecond|Yes|I/o andra byte per sekund|BytesPerSecond|Totalt|Den hastighet med vilken app-processen utfärdar byte till I/O-åtgärder som inte omfattar data, till exempel kontroll åtgärder.|Instans|
|IoOtherOperationsPerSecond|Yes|Andra i/o-åtgärder per sekund|BytesPerSecond|Totalt|Den hastighet med vilken app-processen utfärdar I/O-åtgärder som inte är Läs-eller Skriv åtgärder.|Instans|
|IoReadBytesPerSecond|Yes|IO-lästa byte per sekund|BytesPerSecond|Totalt|Den hastighet med vilken appens process läser in byte från I/O-åtgärder.|Instans|
|IoReadOperationsPerSecond|Yes|IO-Läs åtgärder per sekund|BytesPerSecond|Totalt|Den hastighet med vilken app-processen utfärdar Läs-I/O-åtgärder.|Instans|
|IoWriteBytesPerSecond|Yes|Skrivna byte i i/o per sekund|BytesPerSecond|Totalt|Den hastighet med vilken appens process skriver byte till I/O-åtgärder.|Instans|
|IoWriteOperationsPerSecond|Yes|I/o-Skriv åtgärder per sekund|BytesPerSecond|Totalt|Den hastighet med vilken program processen utfärdar Skriv-I/O-åtgärder.|Instans|
|MemoryWorkingSet|Yes|Minnes arbets mängd|Byte|Genomsnitt|Den aktuella mängden minne som används av appen, i MiB.|Instans|
|PrivateBytes|Yes|Privata byte|Byte|Genomsnitt|Privata byte är den aktuella storleken i byte på minne som program processen har allokerat och som inte kan delas med andra processer.|Instans|
|Begäranden|Yes|Begäranden|Antal|Totalt|Det totala antalet begär Anden oavsett den resulterande HTTP-statuskoden.|Instans|
|RequestsInApplicationQueue|Yes|Begär anden i program kön|Antal|Genomsnitt|Antalet begär anden i program begär ande kön.|Instans|
|Konversation|Yes|Antal trådar|Antal|Genomsnitt|Antalet trådar som för närvarande är aktiva i program processen.|Instans|
|TotalAppDomains|Yes|Totalt antal app-domäner|Antal|Genomsnitt|Det aktuella antalet AppDomains som har lästs in i det här programmet.|Instans|
|TotalAppDomainsUnloaded|Yes|Totalt antal app-domäner som har inaktiverats|Antal|Genomsnitt|Det totala antalet inaktiverade tillämpnings domäner sedan programmet startades.|Instans|


## <a name="microsoftwebstaticsites"></a>Microsoft. Web/staticSites

|Metric|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|---|
|Bytes sent|Yes|Data ut|Byte|Totalt|Bytes sent|Instans|
|FunctionErrors|Yes|FunctionErrors|Antal|Totalt|FunctionErrors|Instans|
|FunctionHits|Yes|FunctionHits|Antal|Totalt|FunctionHits|Instans|
|SiteErrors|Yes|SiteErrors|Antal|Totalt|SiteErrors|Instans|
|SiteHits|Yes|SiteHits|Antal|Totalt|SiteHits|Instans|


## <a name="next-steps"></a>Nästa steg

- [Läs om mått i Azure Monitor](../data-platform.md)
- [Skapa aviseringar för mått](../alerts/alerts-overview.md)
- [Exportera mått till lagring, Event Hub eller Log Analytics](../essentials/platform-logs-overview.md)
