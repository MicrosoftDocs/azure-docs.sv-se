---
title: Azure Monitor mått som stöds efter resurs typ
description: Lista över mått som är tillgängliga för varje resurs typ med Azure Monitor.
author: rboucher
services: azure-monitor
ms.topic: reference
ms.date: 12/09/2020
ms.author: robb
ms.subservice: metrics
ms.openlocfilehash: 04f6cdae8a7601f94251516cf5c3c1fab07994a6
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929114"
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
2. Använd [diagnostikinställningar](diagnostic-settings.md) för att dirigera plattforms mått till 
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
> Bästa praxis är att använda och konfigurera [Azure-diagnostik-tillägget](diagnostics-extension-overview.md) för att skicka gäst operativ systemets prestanda mått till samma Azure Monitor mått databas där plattforms måtten lagras. Tillägget dirigerar gäst operativ systemets mått via [anpassade mått](metrics-custom-overview.md) -API: et. Sedan kan du Diagrama, Varna och annars använda gäst operativ systemets mått som plattforms mått. Alternativt kan du också använda Log Analytics agent för att skicka gäst operativ systemets mått till Azure Monitor loggar/Log Analytics. Du kan fråga efter dessa mått i kombination med icke-metriska data. 

Viktig ytterligare information finns i [Översikt över övervaknings agenter](agents-overview.md).

## <a name="table-formatting"></a>Tabellformatering

> [!IMPORTANT] 
> Den här senaste uppdateringen lägger till en ny kolumn och sorterat om måtten som alfabetiska. Ytterligare information innebär att tabellerna nedan kan ha en vågrät rullnings List längst ned, beroende på webbläsarfönstrets bredd. Om du tror att du saknar information använder du rullnings listen för att visa hela tabellen.

## <a name="microsoftanalysisservicesservers"></a>Microsoft. AnalysisServices/servers

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|CleanerCurrentPrice|Ja|Minne: aktuellt pris för renare|Antal|Genomsnitt|Aktuellt pris för minne, $/byte/tid, normaliserat till 1000.|ServerResourceType|
|CleanerMemoryNonshrinkable|Ja|Minne: det går inte att krympa rengörings minnet|Byte|Genomsnitt|Mängden minne, i byte, som inte kan rensas av bakgrunds rensaren.|ServerResourceType|
|CleanerMemoryShrinkable|Ja|Minne: krympnings utrymme för rensning|Byte|Genomsnitt|Mängden minne i byte som kan rensas av bakgrunds rensaren.|ServerResourceType|
|CommandPoolBusyThreads|Ja|Trådar: pool med upptagna kommando trådar|Antal|Genomsnitt|Antalet upptagna trådar i kommando tråds-poolen.|ServerResourceType|
|CommandPoolIdleThreads|Ja|Trådar: inaktiva trådar för kommando pool|Antal|Genomsnitt|Antal inaktiva trådar i kommando tråds-poolen.|ServerResourceType|
|CommandPoolJobQueueLength|Ja|Kölängd för kommando bassäng|Antal|Genomsnitt|Antal jobb i kön för kommando tråds poolen.|ServerResourceType|
|CurrentConnections|Ja|Anslutning: aktuella anslutningar|Antal|Genomsnitt|Aktuellt antal upprättade klient anslutningar.|ServerResourceType|
|CurrentUserSessions|Ja|Aktuella användarsessioner|Antal|Genomsnitt|Aktuellt antal användarsessioner som har upprättats.|ServerResourceType|
|LongParsingBusyThreads|Ja|Trådar: lång parsning av upptagna trådar|Antal|Genomsnitt|Antalet upptagna trådar i den långa parsande trådpoolen.|ServerResourceType|
|LongParsingIdleThreads|Ja|Trådar: inaktiva trådar för lång parsning|Antal|Genomsnitt|Antalet inaktiva trådar i den långa parsande trådpoolen.|ServerResourceType|
|LongParsingJobQueueLength|Ja|Trådar: lång parsning av jobb Kölängd|Antal|Genomsnitt|Antal jobb i kön för den långa parsande trådpoolen.|ServerResourceType|
|mashup_engine_memory_metric|Ja|M motor minne|Byte|Genomsnitt|Minnes användning per kombinations motor processer|ServerResourceType|
|mashup_engine_private_bytes_metric|Ja|M motor privata byte|Byte|Genomsnitt|Användning av privata byte med kombinations motor processer.|ServerResourceType|
|mashup_engine_qpu_metric|Ja|M-motor QPU|Antal|Genomsnitt|QPU användning av kombinations motor processer|ServerResourceType|
|mashup_engine_virtual_bytes_metric|Ja|Virtuella byte i M-motor|Byte|Genomsnitt|Virtuella byte som används av kombinations motor processer.|ServerResourceType|
|memory_metric|Ja|Minne|Byte|Genomsnitt|Memory. Intervallet 0-25 GB för S1, 0-50 GB för S2 och 0-100 GB för S4|ServerResourceType|
|memory_thrashing_metric|Ja|Minnesförslöing|Procent|Genomsnitt|Genomsnittligt minne nedskräpning.|ServerResourceType|
|MemoryLimitHard|Ja|Minne: minnes gräns hårt|Byte|Genomsnitt|Hård minnes gräns, från konfigurations filen.|ServerResourceType|
|MemoryLimitHigh|Ja|Minne: minnes gräns hög|Byte|Genomsnitt|Hög minnes gräns, från konfigurations filen.|ServerResourceType|
|MemoryLimitLow|Ja|Minne: minnes gräns låg|Byte|Genomsnitt|Låg minnes gräns, från konfigurations filen.|ServerResourceType|
|MemoryLimitVertiPaq|Ja|Minne: minnes gräns VertiPaq|Byte|Genomsnitt|Minnes intern gräns, från konfigurations filen.|ServerResourceType|
|MemoryUsage|Ja|Minne: minnes användning|Byte|Genomsnitt|Minnes användningen för Server processen som används för att beräkna rengörings minnes priset. Lika med Counter Process\PrivateBytes plus storleken på minnesmappade data, vilket ignorerar minne som har mappats eller allokerats av xVelocity i Memory Analytics Engine (VertiPaq) utöver minnes gränsen för xVelocity-motorn.|ServerResourceType|
|private_bytes_metric|Ja|Privata byte|Byte|Genomsnitt|Privata byte.|ServerResourceType|
|ProcessingPoolBusyIOJobThreads|Ja|Trådar: bearbetning av pool upptagna I/O-jobb trådar|Antal|Genomsnitt|Antal trådar som kör i/O-jobb i bearbetnings trådens pool.|ServerResourceType|
|ProcessingPoolBusyNonIOThreads|Ja|Trådar: bearbetning av pool upptagna icke-I/O-trådar|Antal|Genomsnitt|Antal trådar som kör icke-I/O-jobb i bearbetningen av trådpoolen.|ServerResourceType|
|ProcessingPoolIdleIOJobThreads|Ja|Trådar: behandlar inaktiva I/O-jobb trådar för poolen|Antal|Genomsnitt|Antalet inaktiva trådar för I/O-jobb i bearbetnings trådens pool.|ServerResourceType|
|ProcessingPoolIdleNonIOThreads|Ja|Trådar: behandlar inaktiva icke-I/O-trådar som behandlar poolen|Antal|Genomsnitt|Antalet inaktiva trådar i bearbetnings trådens pool som är dedikerad till icke-I/O-jobb.|ServerResourceType|
|ProcessingPoolIOJobQueueLength|Ja|Trådar: bearbeta pool I/O-jobb Kölängd|Antal|Genomsnitt|Antalet I/O-jobb i kön för bearbetning av trådpoolen.|ServerResourceType|
|ProcessingPoolJobQueueLength|Ja|Bearbetar Kölängd för poolen|Antal|Genomsnitt|Antal icke-I/O-jobb i kön för bearbetning av trådpoolen.|ServerResourceType|
|qpu_metric|Ja|QPU|Antal|Genomsnitt|QPU. Intervallet 0-100 för S1, 0-200 för S2 och 0-400 för S4|ServerResourceType|
|QueryPoolBusyThreads|Ja|Upptagna trådar för frågeprocessorn|Antal|Genomsnitt|Antalet upptagna trådar i trådpoolen.|ServerResourceType|
|QueryPoolIdleThreads|Ja|Trådar: inaktiva trådar i lagringspoolen|Antal|Genomsnitt|Antalet inaktiva trådar för I/O-jobb i bearbetnings trådens pool.|ServerResourceType|
|QueryPoolJobQueueLength|Ja|Trådar: jobbkölängd för jobbkö|Antal|Genomsnitt|Antal jobb i kön för trådpoolen för Query.|ServerResourceType|
|Kvot|Ja|Minne: kvot|Byte|Genomsnitt|Aktuell minnes kvot, i byte. Minnes kvot kallas även för minnes tilldelning eller minnes reservation.|ServerResourceType|
|QuotaBlocked|Ja|Minne: kvot blockerad|Antal|Genomsnitt|Det aktuella antalet kvot begär Anden som blockeras tills andra minnes kvoter frigjorts.|ServerResourceType|
|RowsConvertedPerSec|Ja|Bearbetar: rader konverterade per sekund|CountPerSecond|Genomsnitt|Antal rader som konverterats under bearbetning.|ServerResourceType|
|RowsReadPerSec|Ja|Bearbetar: rader lästa per sekund|CountPerSecond|Genomsnitt|Antalet rader som läses från alla Relations databaser.|ServerResourceType|
|RowsWrittenPerSec|Ja|Bearbetar: rader skrivna per sekund|CountPerSecond|Genomsnitt|Antal rader som skrivs under bearbetningen.|ServerResourceType|
|ShortParsingBusyThreads|Ja|Trådar: kort parsning upptagna trådar|Antal|Genomsnitt|Antalet upptagna trådar i den kort parsar trådpoolen.|ServerResourceType|
|ShortParsingIdleThreads|Ja|Trådar: kort parsning inaktiva trådar|Antal|Genomsnitt|Antal inaktiva trådar i kort parsar trådpoolen.|ServerResourceType|
|ShortParsingJobQueueLength|Ja|Trådar: kort parsning av jobb Kölängd|Antal|Genomsnitt|Antal jobb i kö för kort parsar trådpoolen.|ServerResourceType|
|SuccessfullConnectionsPerSec|Ja|Lyckade anslutningar per sekund|CountPerSecond|Genomsnitt|Antal slutförda slut för ande av anslutningar.|ServerResourceType|
|TotalConnectionFailures|Ja|Totalt antal anslutnings problem|Antal|Genomsnitt|Totalt antal misslyckade anslutnings försök.|ServerResourceType|
|TotalConnectionRequests|Ja|Totalt antal anslutnings begär Anden|Antal|Genomsnitt|Totalt antal anslutnings begär Anden. Dessa är mottagna.|ServerResourceType|
|VertiPaqNonpaged|Ja|Minne: VertiPaq som inte är växlat|Byte|Genomsnitt|Byte av minne som är låst i arbets minnet för användning av minnes intern motorn.|ServerResourceType|
|VertiPaqPaged|Ja|Minne: VertiPaq växlat|Byte|Genomsnitt|Byte av växlings Bart minne som används för minnes intern data.|ServerResourceType|
|virtual_bytes_metric|Ja|Virtuella byte|Byte|Genomsnitt|Virtuella byte.|ServerResourceType|


## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|BackendDuration|Ja|Varaktighet för backend-begäranden|Millisekunder|Genomsnitt|Varaktighet för backend-begäranden i millisekunder|Plats, värdnamn|
|Kapacitet|Ja|Kapacitet|Procent|Genomsnitt|Användnings mått för API Management-tjänsten|Plats|
|Varaktighet|Ja|Total varaktighet för gateway-begäranden|Millisekunder|Genomsnitt|Total varaktighet för gateway-begäranden i millisekunder|Plats, värdnamn|
|EventHubDroppedEvents|Ja|Ignorerade EventHub-händelser|Antal|Totalt|Antalet händelser som hoppades över på grund av att gränsen för kös Tor lek har uppnåtts|Plats|
|EventHubRejectedEvents|Ja|Avvisade EventHub-händelser|Antal|Totalt|Antal avvisade EventHub-händelser (felaktig konfiguration eller otillåten)|Plats|
|EventHubSuccessfulEvents|Ja|Lyckade EventHub-händelser|Antal|Totalt|Antal lyckade EventHub-händelser|Plats|
|EventHubThrottledEvents|Ja|Begränsade EventHub-händelser|Antal|Totalt|Antal begränsade EventHub-händelser|Plats|
|EventHubTimedoutEvents|Ja|Tids gränsen nåddes för EventHub-händelser|Antal|Totalt|Antal inaktuella EventHub-händelser|Plats|
|EventHubTotalBytesSent|Ja|Storlek på EventHub-händelser|Byte|Totalt|Total storlek för EventHub-händelser i byte|Plats|
|EventHubTotalEvents|Ja|Totalt antal EventHub-händelser|Antal|Totalt|Antal händelser som skickats till EventHub|Plats|
|EventHubTotalFailedEvents|Ja|Misslyckade EventHub-händelser|Antal|Totalt|Antal misslyckade EventHub-händelser|Plats|
|FailedRequests|Ja|Misslyckade Gateway-begäranden (inaktuella)|Antal|Totalt|Antal misslyckade Gateway-begär Anden – Använd mått för flera dimensions begär Anden med GatewayResponseCodeCategory-dimensionen istället|Plats, värdnamn|
|NetworkConnectivity|Ja|Status för resurs för nätverks anslutning (förhands granskning)|Antal|Genomsnitt|Nätverks anslutnings status för beroende resurs typer från API Management-tjänsten|Plats, ResourceType|
|OtherRequests|Ja|Andra gateway-begäranden (inaktuella)|Antal|Totalt|Antal andra gateway-begär Anden – Använd mått för flera dimensions begär Anden med GatewayResponseCodeCategory-dimensionen istället|Plats, värdnamn|
|Begäranden|Ja|Begäranden|Antal|Totalt|Mått för gateway-begäran med flera dimensioner|Plats, värdnamn, LastErrorReason, BackendResponseCode, GatewayResponseCode, BackendResponseCodeCategory, GatewayResponseCodeCategory|
|SuccessfulRequests|Ja|Lyckade Gateway-begäranden (inaktuella)|Antal|Totalt|Antal lyckade Gateway-begäranden – Använd mått för flera dimensions begär Anden med GatewayResponseCodeCategory-dimensionen istället|Plats, värdnamn|
|TotalRequests|Ja|Totalt antal Gateway-begäranden (inaktuella)|Antal|Totalt|Antal Gateway-begäranden – Använd mått för flera dimensions begär Anden med GatewayResponseCodeCategory-dimensionen istället|Plats, värdnamn|
|UnauthorizedRequests|Ja|Obehöriga Gateway-begäranden (inaktuella)|Antal|Totalt|Antal otillåtna Gateway-begäranden – Använd dimensions mått för flera dimensioner med GatewayResponseCodeCategory dimension i stället|Plats, värdnamn|


## <a name="microsoftappconfigurationconfigurationstores"></a>Microsoft. AppConfiguration/configurationStores

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|HttpIncomingRequestCount|Ja|HttpIncomingRequestCount|Antal|Antal|Totalt antal inkommande HTTP-begäranden.|StatusCode, autentisering|
|HttpIncomingRequestDuration|Ja|HttpIncomingRequestDuration|Antal|Genomsnitt|Svars tid på en http-begäran.|StatusCode, autentisering|
|ThrottledHttpRequestCount|Ja|ThrottledHttpRequestCount|Antal|Antal|Begränsade HTTP-begäranden.|Inga dimensioner|


## <a name="microsoftappplatformspring"></a>Microsoft. AppPlatform/våren

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|JVM. gc. live. data. size|Ja|JVM. gc. live. data. size|Byte|Genomsnitt|Storlek på den gamla generationens lagringspool efter en fullständig GC|Distribution, AppName, Pod|
|JVM. gc. max. data storlek|Ja|JVM. gc. max. data storlek|Byte|Genomsnitt|Max storlek på den gamla generationens minnesbuffert|Distribution, AppName, Pod|
|JVM. gc. Memory. allokerat|Ja|JVM. gc. Memory. allokerat|Byte|Maximal|Ökas för en ökning av storleken på den unga generationens minnesbuffert efter en GC till före nästa|Distribution, AppName, Pod|
|JVM. gc. Memory. upphöjt|Ja|JVM. gc. Memory. upphöjt|Byte|Maximal|Antalet positiva ökningar i storleken på den gamla generationens minnesbuffert innan GC till efter GC|Distribution, AppName, Pod|
|JVM. gc. Pause. total. Count|Ja|JVM. gc. Pause. total. Count|Antal|Totalt|Antal pauser för GC|Distribution, AppName, Pod|
|JVM. gc. Pause. total. Time|Ja|JVM. gc. Pause. total. Time|Millisekunder|Totalt|Total tid för GC-paus|Distribution, AppName, Pod|
|JVM. Memory. dedikerat|Ja|JVM. Memory. dedikerat|Byte|Genomsnitt|Minne som tilldelats JVM i byte|Distribution, AppName, Pod|
|JVM. Memory. Max|Ja|JVM. Memory. Max|Byte|Maximal|Maximal mängd minne i byte som kan användas för minnes hantering|Distribution, AppName, Pod|
|JVM. Memory. används|Ja|JVM. Memory. används|Byte|Genomsnitt|App-minne som används i byte|Distribution, AppName, Pod|
|process. CPU. Usage|Ja|process. CPU. Usage|Procent|Genomsnitt|Den senaste processor användningen för JVM-processen|Distribution, AppName, Pod|
|system. CPU. Usage|Ja|system. CPU. Usage|Procent|Genomsnitt|Senaste CPU-användning för hela systemet|Distribution, AppName, Pod|
|tomcat. global. error|Ja|tomcat. global. error|Antal|Totalt|Tomcat globalt fel|Distribution, AppName, Pod|
|tomcat. global. mottagen|Ja|tomcat. global. mottagen|Byte|Totalt|Totalt antal mottagna byte i Tomcat|Distribution, AppName, Pod|
|tomcat. global. Request. Gmsn. Time|Ja|tomcat. global. Request. Gmsn. Time|Millisekunder|Genomsnitt|Genomsnittlig tid för Tomcat-begäran|Distribution, AppName, Pod|
|tomcat. global. Request. Max|Ja|tomcat. global. Request. Max|Millisekunder|Maximal|Max tid för Tomcat-begäran|Distribution, AppName, Pod|
|tomcat. global. Request. total. Count|Ja|tomcat. global. Request. total. Count|Antal|Totalt|Totalt antal Tomcat-begäranden|Distribution, AppName, Pod|
|tomcat. global. Request. total. Time|Ja|tomcat. global. Request. total. Time|Millisekunder|Totalt|Total tid för Tomcat-begäran|Distribution, AppName, Pod|
|tomcat. global. skickat|Ja|tomcat. global. skickat|Byte|Totalt|Totalt antal skickade byte i Tomcat|Distribution, AppName, Pod|
|tomcat. sessions. Active. Current|Ja|tomcat. sessions. Active. Current|Antal|Totalt|Aktivt antal Tomcat-sessioner|Distribution, AppName, Pod|
|tomcat. sessions. Active. Max|Ja|tomcat. sessions. Active. Max|Antal|Totalt|Antal aktiva Tomcat-sessioner|Distribution, AppName, Pod|
|tomcat. sessions. Alive. Max|Ja|tomcat. sessions. Alive. Max|Millisekunder|Maximal|Maximal Alive-tid för Tomcat-session|Distribution, AppName, Pod|
|tomcat. sessions. created|Ja|tomcat. sessions. created|Antal|Totalt|Antal skapade Tomcat-sessioner|Distribution, AppName, Pod|
|tomcat. sessions. har gått ut|Ja|tomcat. sessions. har gått ut|Antal|Totalt|Antal utgångna Tomcat-sessioner|Distribution, AppName, Pod|
|tomcat. sessions. avvisad|Ja|tomcat. sessions. avvisad|Antal|Totalt|Antal nekade Tomcat-sessioner|Distribution, AppName, Pod|
|tomcat.threads.config. Max|Ja|tomcat.threads.config. Max|Antal|Totalt|Antal trådar för Tomcat-konfiguration|Distribution, AppName, Pod|
|tomcat. threads. Current|Ja|tomcat. threads. Current|Antal|Totalt|Antal Tomcat aktuella trådar|Distribution, AppName, Pod|


## <a name="microsoftautomationautomationaccounts"></a>Microsoft. Automation/automationAccounts

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|TotalJob|Ja|Totalt antal jobb|Antal|Totalt|Det totala antalet jobb|Runbook, status|
|TotalUpdateDeploymentMachineRuns|Ja|Antal datorspecifika körningar av uppdateringsdistributionen|Antal|Totalt|Total distributions dator för program uppdatering körs i en körning av program uppdaterings distribution|SoftwareUpdateConfigurationName, status, TargetComputer, SoftwareUpdateConfigurationRunId|
|TotalUpdateDeploymentRuns|Ja|Antal körningar av uppdateringsdistributionen|Antal|Totalt|Totalt antal körningar av program uppdaterings distribution|SoftwareUpdateConfigurationName, status|


## <a name="microsoftbatchbatchaccounts"></a>Microsoft.BatCH/batchAccounts

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|CoreCount|Inga|Dedikerat antal kärnor|Antal|Totalt|Totalt antal dedikerade kärnor i batch-kontot|Inga dimensioner|
|CreatingNodeCount|Inga|Antal noder skapas|Antal|Totalt|Antal noder som skapas|Inga dimensioner|
|IdleNodeCount|Inga|Antal inaktiva noder|Antal|Totalt|Antal inaktiva noder|Inga dimensioner|
|JobDeleteCompleteEvent|Ja|Slutför händelser för borttagning av jobb|Antal|Totalt|Totalt antal jobb som har tagits bort.|jobId|
|JobDeleteStartEvent|Ja|Start händelser för jobb borttagning|Antal|Totalt|Totalt antal jobb som har begärts att tas bort.|jobId|
|JobDisableCompleteEvent|Ja|Jobb för att inaktivera slutförda händelser|Antal|Totalt|Totalt antal jobb som har inaktiverats.|jobId|
|JobDisableStartEvent|Ja|Jobb inaktivera start händelser|Antal|Totalt|Totalt antal jobb som har begärts att inaktive ras.|jobId|
|JobStartEvent|Ja|Jobb start händelser|Antal|Totalt|Totalt antal jobb som har startats.|jobId|
|JobTerminateCompleteEvent|Ja|Slutför händelser för avsluta jobb|Antal|Totalt|Totalt antal jobb som har avslut ATS.|jobId|
|JobTerminateStartEvent|Ja|Jobb som avslutar start händelser|Antal|Totalt|Totalt antal jobb som har begärts att avslutas.|jobId|
|LeavingPoolNodeCount|Inga|Antal noder för att lämna pooler|Antal|Totalt|Antal noder som lämnar poolen|Inga dimensioner|
|LowPriorityCoreCount|Inga|Antal LowPriority kärnor|Antal|Totalt|Totalt antal låg prioritets kärnor i batch-kontot|Inga dimensioner|
|OfflineNodeCount|Inga|Antal offline-noder|Antal|Totalt|Antal offline-noder|Inga dimensioner|
|PoolCreateEvent|Ja|Skapa händelser för pool|Antal|Totalt|Totalt antal pooler som har skapats|poolId|
|PoolDeleteCompleteEvent|Ja|Slutför händelse för borttagning av pool|Antal|Totalt|Totalt antal borttagna pooler som har slutförts|poolId|
|PoolDeleteStartEvent|Ja|Start händelser för borttagning av pool|Antal|Totalt|Totalt antal borttagningar av pooler som har startat|poolId|
|PoolResizeCompleteEvent|Ja|Slutför händelser för storleks ändring av pool|Antal|Totalt|Totalt antal ändrade pooler som har slutförts|poolId|
|PoolResizeStartEvent|Ja|Starta händelser för att ändra storlek på poolen|Antal|Totalt|Totalt antal storleks ändringar för pooler som har startat|poolId|
|PreemptedNodeCount|Inga|Antal misslyckade noder|Antal|Totalt|Antal misslyckade noder|Inga dimensioner|
|RebootingNodeCount|Inga|Antalet noder startas om|Antal|Totalt|Antal omstarter av noder|Inga dimensioner|
|ReimagingNodeCount|Inga|Antal noder för avbildning|Antal|Totalt|Antal avbildnings noder|Inga dimensioner|
|RunningNodeCount|Inga|Antal noder som körs|Antal|Totalt|Antal noder som körs|Inga dimensioner|
|StartingNodeCount|Inga|Antalet noder startas|Antal|Totalt|Antal noder som börjar|Inga dimensioner|
|StartTaskFailedNodeCount|Inga|Starta aktivitet antal misslyckade noder|Antal|Totalt|Antal noder där start aktiviteten misslyckades|Inga dimensioner|
|TaskCompleteEvent|Ja|Uppgift slutförda händelser|Antal|Totalt|Totalt antal slutförda uppgifter|poolId, jobId|
|TaskFailEvent|Ja|Aktivitet, misslyckade händelser|Antal|Totalt|Totalt antal uppgifter som har slutförts i felaktigt tillstånd|poolId, jobId|
|TaskStartEvent|Ja|Aktivitetens start händelser|Antal|Totalt|Totalt antal aktiviteter som har startat|poolId, jobId|
|TotalLowPriorityNodeCount|Inga|Antal Low-Priority noder|Antal|Totalt|Totalt antal noder med låg prioritet i batch-kontot|Inga dimensioner|
|TotalNodeCount|Inga|Antal dedikerade noder|Antal|Totalt|Totalt antal dedikerade noder i batch-kontot|Inga dimensioner|
|UnusableNodeCount|Inga|Antal noder som inte kan användas|Antal|Totalt|Antal oanvändbara noder|Inga dimensioner|
|WaitingForStartTaskNodeCount|Inga|Väntar på att starta aktiviteter antal noder|Antal|Totalt|Antal noder som väntar på att start aktiviteten ska slutföras|Inga dimensioner|


## <a name="microsoftbatchaiworkspaces"></a>Microsoft.BatchAI/arbets ytor

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Aktiva kärnor|Ja|Aktiva kärnor|Antal|Genomsnitt|Antal aktiva kärnor|Scenario, kluster namn|
|Aktiva noder|Ja|Aktiva noder|Antal|Genomsnitt|Antal noder som körs|Scenario, kluster namn|
|Inaktiva kärnor|Ja|Inaktiva kärnor|Antal|Genomsnitt|Antal inaktiva kärnor|Scenario, kluster namn|
|Inaktiva noder|Ja|Inaktiva noder|Antal|Genomsnitt|Antal inaktiva noder|Scenario, kluster namn|
|Jobbet har slutförts|Ja|Jobbet har slutförts|Antal|Totalt|Antal slutförda jobb|Scenario, kluster namn, ResultType|
|Jobb skickat|Ja|Jobb skickat|Antal|Totalt|Antal skickade jobb|Scenario, kluster namn|
|Lämnar kärnor|Ja|Lämnar kärnor|Antal|Genomsnitt|Antal lämnar kärnor|Scenario, kluster namn|
|Lämnar noder|Ja|Lämnar noder|Antal|Genomsnitt|Antal lämnar noder|Scenario, kluster namn|
|Blockerade kärnor|Ja|Blockerade kärnor|Antal|Genomsnitt|Antal blockerade kärnor|Scenario, kluster namn|
|Misslyckade noder|Ja|Misslyckade noder|Antal|Genomsnitt|Antal misslyckade noder|Scenario, kluster namn|
|Kvot användning i procent|Ja|Kvot användning i procent|Antal|Genomsnitt|Procent av kvoten som används|Scenario, kluster namn, VmFamilyName, VmPriority|
|Totalt antal kärnor|Ja|Totalt antal kärnor|Antal|Genomsnitt|Antal total kärnor|Scenario, kluster namn|
|Totalt antal noder|Ja|Totalt antal noder|Antal|Genomsnitt|Antal totala noder|Scenario, kluster namn|
|Oanvändbara kärnor|Ja|Oanvändbara kärnor|Antal|Genomsnitt|Antal oanvändbara kärnor|Scenario, kluster namn|
|Oanvändbara noder|Ja|Oanvändbara noder|Antal|Genomsnitt|Antal oanvändbara noder|Scenario, kluster namn|


## <a name="microsoftblockchainblockchainmembers"></a>Microsoft. blockchain/blockchainMembers

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|BroadcastProcessedCount|Ja|BroadcastProcessedCountDisplayName|Antal|Genomsnitt|Antalet bearbetade transaktioner.|Nod, kanal, typ, status|
|ConnectionAccepted|Ja|Godkända anslutningar|Antal|Totalt|Godkända anslutningar|Node|
|ConnectionActive|Ja|Aktiva anslutningar|Antal|Genomsnitt|Aktiva anslutningar|Node|
|ConnectionHandled|Ja|Hanterade anslutningar|Antal|Totalt|Hanterade anslutningar|Node|
|ConsensusEtcdraftCommittedBlockNumber|Ja|ConsensusEtcdraftCommittedBlockNumberDisplayName|Antal|Genomsnitt|Block numret för det senaste blocket som har allokerats.|Nod, kanal|
|CpuUsagePercentageInDouble|Ja|Procent andel CPU-användning|Procent|Maximal|Procent andel CPU-användning|Node|
|EndorserEndorsementFailures|Ja|EndorserEndorsementFailuresDisplayName|Antal|Genomsnitt|Antalet misslyckade attesteringar.|Node, Channel, chaincode, chaincodeerror|
|GossipLeaderElectionLeader|Ja|GossipLeaderElectionLeaderDisplayName|Antal|Genomsnitt|Peer är ledare (1) eller följare (0).|Nod, kanal|
|GossipMembershipTotalPeersKnown|Ja|GossipMembershipTotalPeersKnownDisplayName|Antal|Genomsnitt|Totalt antal kända peer-datorer.|Nod, kanal|
|GossipStateHeight|Ja|GossipStateHeightDisplayName|Antal|Genomsnitt|Aktuell höjd för redovisning.|Nod, kanal|
|IOReadBytes|Ja|Lästa byte i IO|Byte|Totalt|Lästa byte i IO|Node|
|IOWriteBytes|Ja|Skrivna byte i IO|Byte|Totalt|Skrivna byte i IO|Node|
|LedgerTransactionCount|Ja|LedgerTransactionCountDisplayName|Antal|Genomsnitt|Antal bearbetade transaktioner.|Node, Channel, transaction_type, chaincode, validation_code|
|MemoryLimit|Ja|Minnes gräns|Byte|Genomsnitt|Minnes gräns|Node|
|MemoryUsage|Ja|Minnesanvändning|Byte|Genomsnitt|Minnesanvändning|Node|
|MemoryUsagePercentageInDouble|Ja|Minnes användnings procent|Procent|Genomsnitt|Minnes användnings procent|Node|
|PendingTransactions|Ja|Väntande transaktioner|Antal|Genomsnitt|Väntande transaktioner|Node|
|ProcessedBlocks|Ja|Bearbetade block|Antal|Totalt|Bearbetade block|Node|
|ProcessedTransactions|Ja|Bearbetade transaktioner|Antal|Totalt|Bearbetade transaktioner|Node|
|QueuedTransactions|Ja|Köade transaktioner|Antal|Genomsnitt|Köade transaktioner|Node|
|RequestHandled|Ja|Hanterade begär Anden|Antal|Totalt|Hanterade begär Anden|Node|
|StorageUsage|Ja|Lagrings användning|Byte|Genomsnitt|Lagrings användning|Node|


## <a name="microsoftcacheredis"></a>Microsoft. cache/Redis

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|allcachehits|Ja|Cacheträffar (Instance-based)|Antal|Totalt||ShardId, port, primär|
|allcachemisses|Ja|Cachemissar (instans-baserat)|Antal|Totalt||ShardId, port, primär|
|allcacheRead|Ja|Läsning av cacheminne (instans baserad)|BytesPerSecond|Maximal||ShardId, port, primär|
|allcacheWrite|Ja|Skrivcache (instans baserad)|BytesPerSecond|Maximal||ShardId, port, primär|
|allconnectedclients|Ja|Anslutna klienter (instans baserade)|Antal|Maximal||ShardId, port, primär|
|allevictedkeys|Ja|Avlägsnade nycklar (instansbaserade)|Antal|Totalt||ShardId, port, primär|
|allexpiredkeys|Ja|Utgångna nycklar (instans baserad)|Antal|Totalt||ShardId, port, primär|
|allgetcommands|Ja|Hämtar (instans baserad)|Antal|Totalt||ShardId, port, primär|
|alloperationsPerSecond|Ja|Åtgärder per sekund (instans baserad)|Antal|Maximal||ShardId, port, primär|
|allserverLoad|Ja|Server belastning (instans baserad)|Procent|Maximal||ShardId, port, primär|
|allsetcommands|Ja|Uppsättningar (instans baserad)|Antal|Totalt||ShardId, port, primär|
|alltotalcommandsprocessed|Ja|Totalt antal åtgärder (instans baserad)|Antal|Totalt||ShardId, port, primär|
|alltotalkeys|Ja|Totalt antal nycklar (instans baserat)|Antal|Maximal||ShardId, port, primär|
|allusedmemory|Ja|Använt minne (instans baserat)|Byte|Maximal||ShardId, port, primär|
|allusedmemorypercentage|Ja|Använd minnes procent (instans baserad)|Procent|Maximal||ShardId, port, primär|
|allusedmemoryRss|Ja|RSS (Instance-based) använt minne|Byte|Maximal||ShardId, port, primär|
|cachehits|Ja|Cacheträffar|Antal|Totalt||ShardId|
|cachehits0|Ja|Cacheträffar (Shard 0)|Antal|Totalt||Inga dimensioner|
|cachehits1|Ja|Cacheträffar (Shard 1)|Antal|Totalt||Inga dimensioner|
|cachehits2|Ja|Cacheträffar (Shard 2)|Antal|Totalt||Inga dimensioner|
|cachehits3|Ja|Cacheträffar (Shard 3)|Antal|Totalt||Inga dimensioner|
|cachehits4|Ja|Cacheträffar (Shard 4)|Antal|Totalt||Inga dimensioner|
|cachehits5|Ja|Cacheträffar (Shard 5)|Antal|Totalt||Inga dimensioner|
|cachehits6|Ja|Cacheträffar (Shard 6)|Antal|Totalt||Inga dimensioner|
|cachehits7|Ja|Cacheträffar (Shard 7)|Antal|Totalt||Inga dimensioner|
|cachehits8|Ja|Cacheträffar (Shard 8)|Antal|Totalt||Inga dimensioner|
|cachehits9|Ja|Cacheträffar (Shard 9)|Antal|Totalt||Inga dimensioner|
|cacheLatency|Ja|Mikrosekunder för cache-fördröjning (för hands version)|Antal|Genomsnitt||ShardId|
|cachemisses|Ja|Cachemissar|Antal|Totalt||ShardId|
|cachemisses0|Ja|Cachemissar (Shard 0)|Antal|Totalt||Inga dimensioner|
|cachemisses1|Ja|Cachemissar (Shard 1)|Antal|Totalt||Inga dimensioner|
|cachemisses2|Ja|Cachemissar (Shard 2)|Antal|Totalt||Inga dimensioner|
|cachemisses3|Ja|Cachemissar (Shard 3)|Antal|Totalt||Inga dimensioner|
|cachemisses4|Ja|Cachemissar (Shard 4)|Antal|Totalt||Inga dimensioner|
|cachemisses5|Ja|Cachemissar (Shard 5)|Antal|Totalt||Inga dimensioner|
|cachemisses6|Ja|Cachemissar (Shard 6)|Antal|Totalt||Inga dimensioner|
|cachemisses7|Ja|Cachemissar (Shard 7)|Antal|Totalt||Inga dimensioner|
|cachemisses8|Ja|Cachemissar (Shard 8)|Antal|Totalt||Inga dimensioner|
|cachemisses9|Ja|Cachemissar (Shard 9)|Antal|Totalt||Inga dimensioner|
|cachemissrate|Ja|Missar i cache|Procent|cachemissrate||ShardId|
|cacheRead|Ja|Cacheläsning|BytesPerSecond|Maximal||ShardId|
|cacheRead0|Ja|Läsning av cache (Shard 0)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead1|Ja|Läsning av cache (Shard 1)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead2|Ja|Läsning av cache (Shard 2)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead3|Ja|Läsning av cacheminne (Shard 3)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead4|Ja|Läsning av cacheminne (Shard 4)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead5|Ja|Läsning av cacheminne (Shard 5)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead6|Ja|Läsning av cache (Shard 6)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead7|Ja|Läsning av cache (Shard 7)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead8|Ja|Läsning av cache (Shard 8)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheRead9|Ja|Läsning av cacheminne (Shard 9)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheWrite|Ja|Cacheskrivning|BytesPerSecond|Maximal||ShardId|
|cacheWrite0|Ja|Skrivcache (Shard 0)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheWrite1|Ja|Skrivcache (Shard 1)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheWrite2|Ja|Skrivcache (Shard 2)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheWrite3|Ja|Skrivcache (Shard 3)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheWrite4|Ja|Skrivcache (Shard 4)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheWrite5|Ja|Skrivcache (Shard 5)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheWrite6|Ja|Skrivcache (Shard 6)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheWrite7|Ja|Skrivcache (Shard 7)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheWrite8|Ja|Skrivcache (Shard 8)|BytesPerSecond|Maximal||Inga dimensioner|
|cacheWrite9|Ja|Skrivcache (Shard 9)|BytesPerSecond|Maximal||Inga dimensioner|
|connectedclients|Ja|Anslutna klienter|Antal|Maximal||ShardId|
|connectedclients0|Ja|Anslutna klienter (Shard 0)|Antal|Maximal||Inga dimensioner|
|connectedclients1|Ja|Anslutna klienter (Shard 1)|Antal|Maximal||Inga dimensioner|
|connectedclients2|Ja|Anslutna klienter (Shard 2)|Antal|Maximal||Inga dimensioner|
|connectedclients3|Ja|Anslutna klienter (Shard 3)|Antal|Maximal||Inga dimensioner|
|connectedclients4|Ja|Anslutna klienter (Shard 4)|Antal|Maximal||Inga dimensioner|
|connectedclients5|Ja|Anslutna klienter (Shard 5)|Antal|Maximal||Inga dimensioner|
|connectedclients6|Ja|Anslutna klienter (Shard 6)|Antal|Maximal||Inga dimensioner|
|connectedclients7|Ja|Anslutna klienter (Shard 7)|Antal|Maximal||Inga dimensioner|
|connectedclients8|Ja|Anslutna klienter (Shard 8)|Antal|Maximal||Inga dimensioner|
|connectedclients9|Ja|Anslutna klienter (Shard 9)|Antal|Maximal||Inga dimensioner|
|fel|Ja|Fel|Antal|Maximal||ShardId, ErrorType|
|evictedkeys|Ja|Avlägsnade nycklar|Antal|Totalt||ShardId|
|evictedkeys0|Ja|Avlägsnade nycklar (Shard 0)|Antal|Totalt||Inga dimensioner|
|evictedkeys1|Ja|Avlägsnade nycklar (Shard 1)|Antal|Totalt||Inga dimensioner|
|evictedkeys2|Ja|Avlägsnade nycklar (Shard 2)|Antal|Totalt||Inga dimensioner|
|evictedkeys3|Ja|Avlägsnade nycklar (Shard 3)|Antal|Totalt||Inga dimensioner|
|evictedkeys4|Ja|Avlägsnade nycklar (Shard 4)|Antal|Totalt||Inga dimensioner|
|evictedkeys5|Ja|Avlägsnade nycklar (Shard 5)|Antal|Totalt||Inga dimensioner|
|evictedkeys6|Ja|Avlägsnade nycklar (Shard 6)|Antal|Totalt||Inga dimensioner|
|evictedkeys7|Ja|Avlägsnade nycklar (Shard 7)|Antal|Totalt||Inga dimensioner|
|evictedkeys8|Ja|Avlägsnade nycklar (Shard 8)|Antal|Totalt||Inga dimensioner|
|evictedkeys9|Ja|Avlägsnade nycklar (Shard 9)|Antal|Totalt||Inga dimensioner|
|expiredkeys|Ja|Utgångna nycklar|Antal|Totalt||ShardId|
|expiredkeys0|Ja|Utgångna nycklar (Shard 0)|Antal|Totalt||Inga dimensioner|
|expiredkeys1|Ja|Utgångna nycklar (Shard 1)|Antal|Totalt||Inga dimensioner|
|expiredkeys2|Ja|Utgångna nycklar (Shard 2)|Antal|Totalt||Inga dimensioner|
|expiredkeys3|Ja|Utgångna nycklar (Shard 3)|Antal|Totalt||Inga dimensioner|
|expiredkeys4|Ja|Utgångna nycklar (Shard 4)|Antal|Totalt||Inga dimensioner|
|expiredkeys5|Ja|Utgångna nycklar (Shard 5)|Antal|Totalt||Inga dimensioner|
|expiredkeys6|Ja|Utgångna nycklar (Shard 6)|Antal|Totalt||Inga dimensioner|
|expiredkeys7|Ja|Utgångna nycklar (Shard 7)|Antal|Totalt||Inga dimensioner|
|expiredkeys8|Ja|Utgångna nycklar (Shard 8)|Antal|Totalt||Inga dimensioner|
|expiredkeys9|Ja|Utgångna nycklar (Shard 9)|Antal|Totalt||Inga dimensioner|
|getcommands|Ja|Hämtningar|Antal|Totalt||ShardId|
|getcommands0|Ja|Hämtar (Shard 0)|Antal|Totalt||Inga dimensioner|
|getcommands1|Ja|Hämtar (Shard 1)|Antal|Totalt||Inga dimensioner|
|getcommands2|Ja|Hämtar (Shard 2)|Antal|Totalt||Inga dimensioner|
|getcommands3|Ja|Hämtar (Shard 3)|Antal|Totalt||Inga dimensioner|
|getcommands4|Ja|Hämtar (Shard 4)|Antal|Totalt||Inga dimensioner|
|getcommands5|Ja|Hämtar (Shard 5)|Antal|Totalt||Inga dimensioner|
|getcommands6|Ja|Hämtar (Shard 6)|Antal|Totalt||Inga dimensioner|
|getcommands7|Ja|Hämtar (Shard 7)|Antal|Totalt||Inga dimensioner|
|getcommands8|Ja|Hämtar (Shard 8)|Antal|Totalt||Inga dimensioner|
|getcommands9|Ja|Hämtar (Shard 9)|Antal|Totalt||Inga dimensioner|
|operationsPerSecond|Ja|Åtgärder per sekund|Antal|Maximal||ShardId|
|operationsPerSecond0|Ja|Åtgärder per sekund (Shard 0)|Antal|Maximal||Inga dimensioner|
|operationsPerSecond1|Ja|Åtgärder per sekund (Shard 1)|Antal|Maximal||Inga dimensioner|
|operationsPerSecond2|Ja|Åtgärder per sekund (Shard 2)|Antal|Maximal||Inga dimensioner|
|operationsPerSecond3|Ja|Åtgärder per sekund (Shard 3)|Antal|Maximal||Inga dimensioner|
|operationsPerSecond4|Ja|Åtgärder per sekund (Shard 4)|Antal|Maximal||Inga dimensioner|
|operationsPerSecond5|Ja|Åtgärder per sekund (Shard 5)|Antal|Maximal||Inga dimensioner|
|operationsPerSecond6|Ja|Åtgärder per sekund (Shard 6)|Antal|Maximal||Inga dimensioner|
|operationsPerSecond7|Ja|Åtgärder per sekund (Shard 7)|Antal|Maximal||Inga dimensioner|
|operationsPerSecond8|Ja|Åtgärder per sekund (Shard 8)|Antal|Maximal||Inga dimensioner|
|operationsPerSecond9|Ja|Åtgärder per sekund (Shard 9)|Antal|Maximal||Inga dimensioner|
|percentProcessorTime|Ja|Processor|Procent|Maximal||ShardId|
|percentProcessorTime0|Ja|PROCESSOR (Shard 0)|Procent|Maximal||Inga dimensioner|
|percentProcessorTime1|Ja|PROCESSOR (Shard 1)|Procent|Maximal||Inga dimensioner|
|percentProcessorTime2|Ja|PROCESSOR (Shard 2)|Procent|Maximal||Inga dimensioner|
|percentProcessorTime3|Ja|PROCESSOR (Shard 3)|Procent|Maximal||Inga dimensioner|
|percentProcessorTime4|Ja|PROCESSOR (Shard 4)|Procent|Maximal||Inga dimensioner|
|percentProcessorTime5|Ja|PROCESSOR (Shard 5)|Procent|Maximal||Inga dimensioner|
|percentProcessorTime6|Ja|PROCESSOR (Shard 6)|Procent|Maximal||Inga dimensioner|
|percentProcessorTime7|Ja|PROCESSOR (Shard 7)|Procent|Maximal||Inga dimensioner|
|percentProcessorTime8|Ja|PROCESSOR (Shard 8)|Procent|Maximal||Inga dimensioner|
|percentProcessorTime9|Ja|PROCESSOR (Shard 9)|Procent|Maximal||Inga dimensioner|
|serverLoad|Ja|Serverbelastning|Procent|Maximal||ShardId|
|serverLoad0|Ja|Server belastning (Shard 0)|Procent|Maximal||Inga dimensioner|
|serverLoad1|Ja|Server belastning (Shard 1)|Procent|Maximal||Inga dimensioner|
|serverLoad2|Ja|Server belastning (Shard 2)|Procent|Maximal||Inga dimensioner|
|serverLoad3|Ja|Server belastning (Shard 3)|Procent|Maximal||Inga dimensioner|
|serverLoad4|Ja|Server belastning (Shard 4)|Procent|Maximal||Inga dimensioner|
|serverLoad5|Ja|Server belastning (Shard 5)|Procent|Maximal||Inga dimensioner|
|serverLoad6|Ja|Server belastning (Shard 6)|Procent|Maximal||Inga dimensioner|
|serverLoad7|Ja|Server belastning (Shard 7)|Procent|Maximal||Inga dimensioner|
|serverLoad8|Ja|Server belastning (Shard 8)|Procent|Maximal||Inga dimensioner|
|serverLoad9|Ja|Server belastning (Shard 9)|Procent|Maximal||Inga dimensioner|
|setcommands|Ja|Inställningar|Antal|Totalt||ShardId|
|setcommands0|Ja|Uppsättningar (Shard 0)|Antal|Totalt||Inga dimensioner|
|setcommands1|Ja|Uppsättningar (Shard 1)|Antal|Totalt||Inga dimensioner|
|setcommands2|Ja|Uppsättningar (Shard 2)|Antal|Totalt||Inga dimensioner|
|setcommands3|Ja|Uppsättningar (Shard 3)|Antal|Totalt||Inga dimensioner|
|setcommands4|Ja|Uppsättningar (Shard 4)|Antal|Totalt||Inga dimensioner|
|setcommands5|Ja|Uppsättningar (Shard 5)|Antal|Totalt||Inga dimensioner|
|setcommands6|Ja|Uppsättningar (Shard 6)|Antal|Totalt||Inga dimensioner|
|setcommands7|Ja|Uppsättningar (Shard 7)|Antal|Totalt||Inga dimensioner|
|setcommands8|Ja|Uppsättningar (Shard 8)|Antal|Totalt||Inga dimensioner|
|setcommands9|Ja|Uppsättningar (Shard 9)|Antal|Totalt||Inga dimensioner|
|totalcommandsprocessed|Ja|Totalt antal åtgärder|Antal|Totalt||ShardId|
|totalcommandsprocessed0|Ja|Totalt antal åtgärder (Shard 0)|Antal|Totalt||Inga dimensioner|
|totalcommandsprocessed1|Ja|Totalt antal åtgärder (Shard 1)|Antal|Totalt||Inga dimensioner|
|totalcommandsprocessed2|Ja|Totalt antal åtgärder (Shard 2)|Antal|Totalt||Inga dimensioner|
|totalcommandsprocessed3|Ja|Totalt antal åtgärder (Shard 3)|Antal|Totalt||Inga dimensioner|
|totalcommandsprocessed4|Ja|Totalt antal åtgärder (Shard 4)|Antal|Totalt||Inga dimensioner|
|totalcommandsprocessed5|Ja|Totalt antal åtgärder (Shard 5)|Antal|Totalt||Inga dimensioner|
|totalcommandsprocessed6|Ja|Totalt antal åtgärder (Shard 6)|Antal|Totalt||Inga dimensioner|
|totalcommandsprocessed7|Ja|Totalt antal åtgärder (Shard 7)|Antal|Totalt||Inga dimensioner|
|totalcommandsprocessed8|Ja|Totalt antal åtgärder (Shard 8)|Antal|Totalt||Inga dimensioner|
|totalcommandsprocessed9|Ja|Totalt antal åtgärder (Shard 9)|Antal|Totalt||Inga dimensioner|
|totalkeys|Ja|Totalt antal nycklar|Antal|Maximal||ShardId|
|totalkeys0|Ja|Totalt antal nycklar (Shard 0)|Antal|Maximal||Inga dimensioner|
|totalkeys1|Ja|Totalt antal nycklar (Shard 1)|Antal|Maximal||Inga dimensioner|
|totalkeys2|Ja|Totalt antal nycklar (Shard 2)|Antal|Maximal||Inga dimensioner|
|totalkeys3|Ja|Totalt antal nycklar (Shard 3)|Antal|Maximal||Inga dimensioner|
|totalkeys4|Ja|Totalt antal nycklar (Shard 4)|Antal|Maximal||Inga dimensioner|
|totalkeys5|Ja|Totalt antal nycklar (Shard 5)|Antal|Maximal||Inga dimensioner|
|totalkeys6|Ja|Totalt antal nycklar (Shard 6)|Antal|Maximal||Inga dimensioner|
|totalkeys7|Ja|Totalt antal nycklar (Shard 7)|Antal|Maximal||Inga dimensioner|
|totalkeys8|Ja|Totalt antal nycklar (Shard 8)|Antal|Maximal||Inga dimensioner|
|totalkeys9|Ja|Totalt antal nycklar (Shard 9)|Antal|Maximal||Inga dimensioner|
|usedmemory|Ja|Använt minne|Byte|Maximal||ShardId|
|usedmemory0|Ja|Använt minne (Shard 0)|Byte|Maximal||Inga dimensioner|
|usedmemory1|Ja|Använt minne (Shard 1)|Byte|Maximal||Inga dimensioner|
|usedmemory2|Ja|Använt minne (Shard 2)|Byte|Maximal||Inga dimensioner|
|usedmemory3|Ja|Använt minne (Shard 3)|Byte|Maximal||Inga dimensioner|
|usedmemory4|Ja|Använt minne (Shard 4)|Byte|Maximal||Inga dimensioner|
|usedmemory5|Ja|Använt minne (Shard 5)|Byte|Maximal||Inga dimensioner|
|usedmemory6|Ja|Använt minne (Shard 6)|Byte|Maximal||Inga dimensioner|
|usedmemory7|Ja|Använt minne (Shard 7)|Byte|Maximal||Inga dimensioner|
|usedmemory8|Ja|Använt minne (Shard 8)|Byte|Maximal||Inga dimensioner|
|usedmemory9|Ja|Använt minne (Shard 9)|Byte|Maximal||Inga dimensioner|
|usedmemorypercentage|Ja|Använt minne i procent|Procent|Maximal||ShardId|
|usedmemoryRss|Ja|RSS för använt minne|Byte|Maximal||ShardId|
|usedmemoryRss0|Ja|Använt minne RSS (Shard 0)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss1|Ja|RSS för använt minne (Shard 1)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss2|Ja|Använt minne RSS (Shard 2)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss3|Ja|Använt minne RSS (Shard 3)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss4|Ja|Använt minne RSS (Shard 4)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss5|Ja|Använt minne RSS (Shard 5)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss6|Ja|Använt minne RSS (Shard 6)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss7|Ja|Använt minne RSS (Shard 7)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss8|Ja|Använt minne RSS (Shard 8)|Byte|Maximal||Inga dimensioner|
|usedmemoryRss9|Ja|Använt minne RSS (Shard 9)|Byte|Maximal||Inga dimensioner|


## <a name="microsoftcdncdnwebapplicationfirewallpolicies"></a>Microsoft. CDN/cdnwebapplicationfirewallpolicies

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|WebApplicationFirewallRequestCount|Ja|Antal begär Anden om webb programs brand vägg|Antal|Totalt|Antalet klient begär Anden som bearbetats av brand väggen för webbaserade program|PolicyName, RuleName, åtgärd|


## <a name="microsoftclassiccomputedomainnamesslotsroles"></a>Microsoft. ClassicCompute/domän namn/platser/roller

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Disk-lästa byte/s|Inga|Disk läsning|BytesPerSecond|Genomsnitt|Genomsnittligt antal byte som lästs från disk under övervaknings perioden.|RoleInstanceId|
|Disk Läs åtgärder/SEK|Ja|Disk Läs åtgärder/SEK|CountPerSecond|Genomsnitt|Disk-Läs-IOPS.|RoleInstanceId|
|Disk-skrivna byte/s|Inga|Disk skrivning|BytesPerSecond|Genomsnitt|Genomsnittligt antal byte som skrivs till disk under övervaknings perioden.|RoleInstanceId|
|Disk skrivnings åtgärder/SEK|Ja|Disk skrivnings åtgärder/SEK|CountPerSecond|Genomsnitt|Skriv IOPS för disk.|RoleInstanceId|
|Nätverk – inkommande|Ja|Nätverk – inkommande|Byte|Totalt|Antalet byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik).|RoleInstanceId|
|Nätverk – utgående|Ja|Nätverk – utgående|Byte|Totalt|Antalet byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik).|RoleInstanceId|
|Processorprocentandel|Ja|Processorprocentandel|Procent|Genomsnitt|Procent andelen allokerade beräknings enheter som för närvarande används av den eller de virtuella datorerna.|RoleInstanceId|


## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft. ClassicCompute/virtualMachines

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Disk-lästa byte/s|Inga|Disk läsning|BytesPerSecond|Genomsnitt|Genomsnittligt antal byte som lästs från disk under övervaknings perioden.|Inga dimensioner|
|Disk Läs åtgärder/SEK|Ja|Disk Läs åtgärder/SEK|CountPerSecond|Genomsnitt|Disk-Läs-IOPS.|Inga dimensioner|
|Disk-skrivna byte/s|Inga|Disk skrivning|BytesPerSecond|Genomsnitt|Genomsnittligt antal byte som skrivs till disk under övervaknings perioden.|Inga dimensioner|
|Disk skrivnings åtgärder/SEK|Ja|Disk skrivnings åtgärder/SEK|CountPerSecond|Genomsnitt|Skriv IOPS för disk.|Inga dimensioner|
|Nätverk – inkommande|Ja|Nätverk – inkommande|Byte|Totalt|Antalet byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik).|Inga dimensioner|
|Nätverk – utgående|Ja|Nätverk – utgående|Byte|Totalt|Antalet byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik).|Inga dimensioner|
|Processorprocentandel|Ja|Processorprocentandel|Procent|Genomsnitt|Procent andelen allokerade beräknings enheter som för närvarande används av den eller de virtuella datorerna.|Inga dimensioner|


## <a name="microsoftclassicstoragestorageaccounts"></a>Microsoft. ClassicStorage/storageAccounts

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Ja|Tillgänglighet|Procent|Genomsnitt|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|
|Utgående|Ja|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|Ingress|Ja|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Ja|Lyckad E2E-svarstid|Millisekunder|Genomsnitt|Svars tiden från slut punkt till slut punkt för lyckade begär anden till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Ja|Lyckad Server svars tid|Millisekunder|Genomsnitt|Svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ av typ, ApiName, autentisering|
|Transaktioner|Ja|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|
|UsedCapacity|Inga|Använd kapacitet|Byte|Genomsnitt|Kapacitet som används av konto|Inga dimensioner|


## <a name="microsoftclassicstoragestorageaccountsblobservices"></a>Microsoft. ClassicStorage/storageAccounts/blobServices

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Ja|Tillgänglighet|Procent|Genomsnitt|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|
|BlobCapacity|Inga|BLOB-kapacitet|Byte|Genomsnitt|Mängden lagrings utrymme som används av lagrings kontots Blob Service i byte.|BlobType,-nivå|
|BlobCount|Inga|BLOB-antal|Antal|Genomsnitt|Antalet BLOB i lagrings kontots Blob Service.|BlobType,-nivå|
|ContainerCount|Ja|Antal BLOB-behållare|Antal|Genomsnitt|Antalet behållare i lagrings kontots Blob Service.|Inga dimensioner|
|Utgående|Ja|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|IndexCapacity|Inga|Index kapacitet|Byte|Genomsnitt|Mängden lagrings utrymme som används av ADLS Gen2 (hierarkiskt) index i byte.|Inga dimensioner|
|Ingress|Ja|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Ja|Lyckad E2E-svarstid|Millisekunder|Genomsnitt|Svars tiden från slut punkt till slut punkt för lyckade begär anden till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Ja|Lyckad Server svars tid|Millisekunder|Genomsnitt|Svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ av typ, ApiName, autentisering|
|Transaktioner|Ja|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|


## <a name="microsoftclassicstoragestorageaccountsfileservices"></a>Microsoft. ClassicStorage/storageAccounts/fileServices

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Ja|Tillgänglighet|Procent|Genomsnitt|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ, ApiName, autentisering, FileShare|
|Utgående|Ja|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ, ApiName, autentisering, FileShare|
|FileCapacity|Inga|Fil kapacitet|Byte|Genomsnitt|Mängden lagrings utrymme som används av lagrings kontots fil tjänst i byte.|FileShare|
|FileCount|Inga|Antal filer|Antal|Genomsnitt|Antalet filer i lagrings kontots fil tjänst.|FileShare|
|FileShareCount|Inga|Antal fil resurser|Antal|Genomsnitt|Antalet fil resurser i lagrings kontots fil tjänst.|Inga dimensioner|
|FileShareQuota|Inga|Fil resursens kvot storlek|Byte|Genomsnitt|Den övre gränsen för mängden lagrings utrymme som kan användas av Azure Files tjänsten i byte.|FileShare|
|FileShareSnapshotCount|Inga|Antal ögonblicks bilder av fil resurs|Antal|Genomsnitt|Antal ögonblicks bilder som finns på resursen i lagrings kontots fil tjänst.|FileShare|
|FileShareSnapshotSize|Inga|Storlek på fil resursens ögonblicks bild|Byte|Genomsnitt|Mängden lagrings utrymme som används av ögonblicks bilderna i lagrings kontots fil tjänst i byte.|FileShare|
|Ingress|Ja|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ, ApiName, autentisering, FileShare|
|SuccessE2ELatency|Ja|Lyckad E2E-svarstid|Millisekunder|Genomsnitt|Svars tiden från slut punkt till slut punkt för lyckade begär anden till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ, ApiName, autentisering, FileShare|
|SuccessServerLatency|Ja|Lyckad Server svars tid|Millisekunder|Genomsnitt|Svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ, ApiName, autentisering, FileShare|
|Transaktioner|Ja|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering, FileShare|


## <a name="microsoftclassicstoragestorageaccountsqueueservices"></a>Microsoft. ClassicStorage/storageAccounts/queueServices

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Ja|Tillgänglighet|Procent|Genomsnitt|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|
|Utgående|Ja|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|Ingress|Ja|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|QueueCapacity|Ja|Kös Kap kap.|Byte|Genomsnitt|Mängden lagrings utrymme som används av lagrings kontots Kötjänst i byte.|Inga dimensioner|
|QueueCount|Ja|Antal köer|Antal|Genomsnitt|Antalet köer i lagrings kontots Kötjänst.|Inga dimensioner|
|QueueMessageCount|Ja|Antal meddelanden i kö|Antal|Genomsnitt|Ungefärligt antal köa meddelanden i lagrings kontots Kötjänst.|Inga dimensioner|
|SuccessE2ELatency|Ja|Lyckad E2E-svarstid|Millisekunder|Genomsnitt|Svars tiden från slut punkt till slut punkt för lyckade begär anden till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Ja|Lyckad Server svars tid|Millisekunder|Genomsnitt|Svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ av typ, ApiName, autentisering|
|Transaktioner|Ja|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|


## <a name="microsoftclassicstoragestorageaccountstableservices"></a>Microsoft. ClassicStorage/storageAccounts/tableServices

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Ja|Tillgänglighet|Procent|Genomsnitt|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|
|Utgående|Ja|Utgående|Byte|Totalt|Mängden utgående data, i byte. Det här värdet innefattar utgående data från en extern klient till Azure Storage samt utgående data inom Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|Ingress|Ja|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Ja|Lyckad E2E-svarstid|Millisekunder|Genomsnitt|Svars tiden från slut punkt till slut punkt för lyckade begär anden till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Ja|Lyckad Server svars tid|Millisekunder|Genomsnitt|Svars tiden som används av Azure Storage för att bearbeta en lyckad begäran, i millisekunder. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ av typ, ApiName, autentisering|
|TableCapacity|Ja|Tabell kapacitet|Byte|Genomsnitt|Mängden lagrings utrymme som används av lagrings kontots Table service i byte.|Inga dimensioner|
|TableCount|Ja|Antal tabeller|Antal|Genomsnitt|Antalet tabeller i lagrings kontots Table service.|Inga dimensioner|
|TableEntityCount|Ja|Antal tabell enheter|Antal|Genomsnitt|Antalet tabell enheter i lagrings kontots Table service.|Inga dimensioner|
|Transaktioner|Ja|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|


## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft. CognitiveServices/konton

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|BlockedCalls|Ja|Blockerade anrop|Antal|Totalt|Antal anrop som har överskridit frekvensen eller kvot gränsen.|ApiName, OperationName, region|
|CharactersTrained|Ja|Upptränade tecken|Antal|Totalt|Totalt antal tecken som har tränats.|ApiName, OperationName, region|
|CharactersTranslated|Ja|Översatta tecken|Antal|Totalt|Totalt antal tecken i begäran om inkommande text.|ApiName, OperationName, region|
|ClientErrors|Ja|Klient fel|Antal|Totalt|Antal anrop med fel på klient sidan (HTTP-svarskod 4xx).|ApiName, OperationName, region|
|Datain|Ja|Data i|Byte|Totalt|Storlek på inkommande data i byte.|ApiName, OperationName, region|
|Data|Ja|Data ut|Byte|Totalt|Storlek på utgående data i byte.|ApiName, OperationName, region|
|Svarstid|Ja|Svarstid|Millisekunder|Genomsnitt|Svars tid i millisekunder.|ApiName, OperationName, region|
|ProcessedImages|Ja|Bearbetade bilder|Antal|Totalt|Antal transaktioner för bild bearbetning.|ApiName, FeatureName, UsageChannel, region|
|ServerErrors|Ja|Server fel|Antal|Totalt|Antal anrop med internt tjänst fel (HTTP-svarskod 5xx).|ApiName, OperationName, region|
|SpeechSessionDuration|Ja|Tal sessionens varaktighet|Sekunder|Totalt|Total varaktighet för talläget på några sekunder.|ApiName, OperationName, region|
|SuccessfulCalls|Ja|Lyckade anrop|Antal|Totalt|Antal lyckade anrop.|ApiName, OperationName, region|
|TotalCalls|Ja|Totalt antal anrop|Antal|Totalt|Totalt antal anrop.|ApiName, OperationName, region|
|TotalErrors|Ja|Totalt antal fel|Antal|Totalt|Totalt antal anrop med fel svar (HTTP-svarskod 4xx eller 5xx).|ApiName, OperationName, region|
|TotalTokenCalls|Ja|Totalt antal token-anrop|Antal|Totalt|Totalt antal token-anrop.|ApiName, OperationName, region|
|TotalTransactions|Ja|Totalt antal transaktioner|Antal|Totalt|Totalt antal transaktioner.|Inga dimensioner|


## <a name="microsoftcomputecloudservices"></a>Microsoft. Compute/cloudServices

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Lästa byte på disk|Ja|Lästa byte på disk|Byte|Totalt|Byte som lästs från disk under övervaknings perioden|RoleInstanceId|
|Disk Läs åtgärder/SEK|Ja|Disk Läs åtgärder/SEK|CountPerSecond|Genomsnitt|Disk-Läs-IOPS|RoleInstanceId|
|Disk-skrivna byte|Ja|Disk-skrivna byte|Byte|Totalt|Byte som skrivits till disk under övervaknings perioden|RoleInstanceId|
|Disk skrivnings åtgärder/SEK|Ja|Disk skrivnings åtgärder/SEK|CountPerSecond|Genomsnitt|Skriv IOPS för disk|RoleInstanceId|
|Processorprocentandel|Ja|Processorprocentandel|Procent|Genomsnitt|Procent andelen allokerade beräknings enheter som för närvarande används av den eller de virtuella datorerna|RoleInstanceId|


## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Förbrukade CPU-krediter|Ja|Förbrukade CPU-krediter|Antal|Genomsnitt|Totalt antal krediter som konsumeras av den virtuella datorn|Inga dimensioner|
|Återstående CPU-krediter|Ja|Återstående CPU-krediter|Antal|Genomsnitt|Totalt antal krediter som är tillgängliga för burst|Inga dimensioner|
|Förbrukad mängd bandbredd för data disk|Ja|Förbrukad mängd bandbredd för data disk|Procent|Genomsnitt|Procent andel data diskens förbrukade bandbredd per minut|ENHETEN|
|Förbrukad rabatt i data disk IOPS|Ja|Förbrukad rabatt i data disk IOPS|Procent|Genomsnitt|Procent andel data disk-I/o-förbrukat per minut|ENHETEN|
|Ködjup för datadisk|Ja|Data disk Queue djup (för hands version)|Antal|Genomsnitt|Data diskens ködjup (eller Kölängd)|ENHETEN|
|Lästa byte på datadisk/SEK|Ja|Lästa byte på datadisk/SEK (för hands version)|CountPerSecond|Genomsnitt|Byte/s lästa från en enskild disk under övervaknings perioden|ENHETEN|
|Läs åtgärder för data disk/SEK|Ja|Läs åtgärder för data disk/SEK (för hands version)|CountPerSecond|Genomsnitt|Läsa IOPS från en enskild disk under övervaknings perioden|ENHETEN|
|Skrivna byte på datadisk/SEK|Ja|Skrivna byte på datadisk/SEK (för hands version)|CountPerSecond|Genomsnitt|Byte/s skrivs till en enskild disk under övervaknings perioden|ENHETEN|
|Skriv åtgärder för data disk/SEK|Ja|Skriv åtgärder för data disk/SEK (för hands version)|CountPerSecond|Genomsnitt|Skriv IOPS från en enskild disk under övervaknings perioden|ENHETEN|
|Lästa byte på disk|Ja|Lästa byte på disk|Byte|Totalt|Byte som lästs från disk under övervaknings perioden|Inga dimensioner|
|Disk Läs åtgärder/SEK|Ja|Disk Läs åtgärder/SEK|CountPerSecond|Genomsnitt|Disk-Läs-IOPS|Inga dimensioner|
|Disk-skrivna byte|Ja|Disk-skrivna byte|Byte|Totalt|Byte som skrivits till disk under övervaknings perioden|Inga dimensioner|
|Disk skrivnings åtgärder/SEK|Ja|Disk skrivnings åtgärder/SEK|CountPerSecond|Genomsnitt|Skriv IOPS för disk|Inga dimensioner|
|Inkommande flöden|Ja|Inkommande flöden|Antal|Genomsnitt|Inkommande flöden är antalet aktuella flöden i den inkommande riktningen (trafik som går till den virtuella datorn)|Inga dimensioner|
|Högsta skapande frekvens för inkommande flöden|Ja|Högsta skapande frekvens för inkommande flöden|CountPerSecond|Genomsnitt|Högsta skapande frekvens för inkommande flöden (trafik som går till den virtuella datorn)|Inga dimensioner|
|Nätverk – inkommande|Ja|Nätverk i fakturerbart (inaktuellt)|Byte|Totalt|Antalet fakturerbara byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik) (inaktuell)|Inga dimensioner|
|Totalt nätverk|Ja|Totalt nätverk|Byte|Totalt|Antalet byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik)|Inga dimensioner|
|Nätverk – utgående|Ja|Fakturerbart för nätverk (inaktuellt)|Byte|Totalt|Antalet fakturerbara byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik) (inaktuell)|Inga dimensioner|
|Totalt nätverk|Ja|Totalt nätverk|Byte|Totalt|Antalet byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik)|Inga dimensioner|
|Förbrukade procent av OS-diskens bandbredd|Ja|Förbrukade procent av OS-diskens bandbredd|Procent|Genomsnitt|Procent andel förbrukad bandbredd för operativ system disk per minut|ENHETEN|
|Förbrukade procent av OS-diskens IOPS|Ja|Förbrukade procent av OS-diskens IOPS|Procent|Genomsnitt|Procent andel operativ system disk I/o-förbrukat per minut|ENHETEN|
|Ködjup för OS-disk|Ja|Ködjup för OS-disk (för hands version)|Antal|Genomsnitt|Ködjup i operativ systemets disk (eller Kölängd)|Inga dimensioner|
|Lästa byte för OS-disk/SEK|Ja|Lästa byte för operativ system disk/SEK (för hands version)|CountPerSecond|Genomsnitt|Byte/s lästa från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Läs åtgärder för operativ system disk/SEK|Ja|Läs åtgärder för operativ system disk/SEK (för hands version)|CountPerSecond|Genomsnitt|Läsa IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Skrivna byte per operativ system disk/SEK|Ja|Skrivna byte per operativ system disk/SEK (för hands version)|CountPerSecond|Genomsnitt|Byte/s skrivs till en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Skriv åtgärder för operativ system disk/SEK|Ja|Skriv åtgärder för operativ system disk/SEK (för hands version)|CountPerSecond|Genomsnitt|Skriv IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|KÖDJUP för operativ system per disk|Ja|OS-KÖDJUP (inaktuellt)|Antal|Genomsnitt|Ködjup i operativ systemets disk (eller Kölängd)|Inga dimensioner|
|Lästa byte per operativ system disk/SEK|Ja|Lästa byte för OS-disk/SEK (inaktuell)|CountPerSecond|Genomsnitt|Byte/s lästa från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Läs åtgärder för operativ system per disk/SEK|Ja|Läs åtgärder för operativ system disk/SEK (inaktuellt)|CountPerSecond|Genomsnitt|Läsa IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Skrivna byte per operativ system disk/SEK|Ja|Skrivna byte per operativ system disk/SEK (inaktuellt)|CountPerSecond|Genomsnitt|Byte/s skrivs till en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Skriv åtgärder för operativ system per disk/SEK|Ja|Skriv åtgärder för operativ system disk/SEK (inaktuellt)|CountPerSecond|Genomsnitt|Skriv IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Utgående flöden|Ja|Utgående flöden|Antal|Genomsnitt|Utgående flöden är antalet aktuella flöden i den utgående riktningen (trafik som går ut från den virtuella datorn)|Inga dimensioner|
|Längsta skapande frekvens för utgående flöden|Ja|Längsta skapande frekvens för utgående flöden|CountPerSecond|Genomsnitt|Den maximala skapande frekvensen för utgående flöden (trafik som går ut från den virtuella datorn)|Inga dimensioner|
|Per disk KÖDJUP|Ja|KÖDJUP för data disk (inaktuellt)|Antal|Genomsnitt|Data diskens ködjup (eller Kölängd)|SlotId|
|Lästa byte per disk/SEK|Ja|Lästa byte på datadisk/SEK (inaktuellt)|CountPerSecond|Genomsnitt|Byte/s lästa från en enskild disk under övervaknings perioden|SlotId|
|Läs åtgärder per disk/SEK|Ja|Läs åtgärder för data disk/SEK (inaktuellt)|CountPerSecond|Genomsnitt|Läsa IOPS från en enskild disk under övervaknings perioden|SlotId|
|Skrivna byte per disk/SEK|Ja|Skrivna byte på datadisk/SEK (inaktuellt)|CountPerSecond|Genomsnitt|Byte/s skrivs till en enskild disk under övervaknings perioden|SlotId|
|Skriv åtgärder per disk/SEK|Ja|Skriv åtgärder för data disk/SEK (inaktuellt)|CountPerSecond|Genomsnitt|Skriv IOPS från en enskild disk under övervaknings perioden|SlotId|
|Processorprocentandel|Ja|Processorprocentandel|Procent|Genomsnitt|Procent andelen allokerade beräknings enheter som för närvarande används av den eller de virtuella datorerna|Inga dimensioner|
|Läsning av Premium data disk-cache|Ja|Läsning av Premium data disk-cache (för hands version)|Procent|Genomsnitt|Läsning av Premium data disk-cache|ENHETEN|
|Read missar i Premium data-diskcachen|Ja|Read missar i Premium data disk cache (för hands version)|Procent|Genomsnitt|Read missar i Premium data-diskcachen|ENHETEN|
|Läsning av Premium OS-diskcachen|Ja|Läsning av Premium OS-diskcachen (för hands version)|Procent|Genomsnitt|Läsning av Premium OS-diskcachen|Inga dimensioner|
|Read missar i Premium OS-diskcachen|Ja|Read missar i Premium OS-diskcachen (för hands version)|Procent|Genomsnitt|Read missar i Premium OS-diskcachen|Inga dimensioner|
|Cachelagrad bandbredd i procent för VM|Ja|Cachelagrad bandbredd i procent för VM|Procent|Genomsnitt|Procent andel cachelagrad disk bandbredd som används av den virtuella datorn|Inga dimensioner|
|VM cachelagrad IOPS förbrukad procent|Ja|VM cachelagrad IOPS förbrukad procent|Procent|Genomsnitt|Procent andel cachelagrad disk-IOPS som används av den virtuella datorn|Inga dimensioner|
|Förbrukad procent andel förbrukade bandbredd i VM|Ja|Förbrukad procent andel förbrukade bandbredd i VM|Procent|Genomsnitt|Procent andel ej cachelagrad disk bandbredd som används av den virtuella datorn|Inga dimensioner|
|VM, ej cachelagrad IOPS, procent andel|Ja|VM, ej cachelagrad IOPS, procent andel|Procent|Genomsnitt|Procent andel ej cachelagrat disk-IOPS som används av den virtuella datorn|Inga dimensioner|


## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Förbrukade CPU-krediter|Ja|Förbrukade CPU-krediter|Antal|Genomsnitt|Totalt antal krediter som konsumeras av den virtuella datorn|Inga dimensioner|
|Återstående CPU-krediter|Ja|Återstående CPU-krediter|Antal|Genomsnitt|Totalt antal krediter som är tillgängliga för burst|Inga dimensioner|
|Ködjup för datadisk|Ja|Data disk Queue djup (för hands version)|Antal|Genomsnitt|Data diskens ködjup (eller Kölängd)|LUN, VMName|
|Lästa byte på datadisk/SEK|Ja|Lästa byte på datadisk/SEK (för hands version)|CountPerSecond|Genomsnitt|Byte/s lästa från en enskild disk under övervaknings perioden|LUN, VMName|
|Läs åtgärder för data disk/SEK|Ja|Läs åtgärder för data disk/SEK (för hands version)|CountPerSecond|Genomsnitt|Läsa IOPS från en enskild disk under övervaknings perioden|LUN, VMName|
|Skrivna byte på datadisk/SEK|Ja|Skrivna byte på datadisk/SEK (för hands version)|CountPerSecond|Genomsnitt|Byte/s skrivs till en enskild disk under övervaknings perioden|LUN, VMName|
|Skriv åtgärder för data disk/SEK|Ja|Skriv åtgärder för data disk/SEK (för hands version)|CountPerSecond|Genomsnitt|Skriv IOPS från en enskild disk under övervaknings perioden|LUN, VMName|
|Lästa byte på disk|Ja|Lästa byte på disk|Byte|Totalt|Byte som lästs från disk under övervaknings perioden|VMName|
|Disk Läs åtgärder/SEK|Ja|Disk Läs åtgärder/SEK|CountPerSecond|Genomsnitt|Disk-Läs-IOPS|VMName|
|Disk-skrivna byte|Ja|Disk-skrivna byte|Byte|Totalt|Byte som skrivits till disk under övervaknings perioden|VMName|
|Disk skrivnings åtgärder/SEK|Ja|Disk skrivnings åtgärder/SEK|CountPerSecond|Genomsnitt|Skriv IOPS för disk|VMName|
|Inkommande flöden|Ja|Inkommande flöden|Antal|Genomsnitt|Inkommande flöden är antalet aktuella flöden i den inkommande riktningen (trafik som går till den virtuella datorn)|VMName|
|Högsta skapande frekvens för inkommande flöden|Ja|Högsta skapande frekvens för inkommande flöden|CountPerSecond|Genomsnitt|Högsta skapande frekvens för inkommande flöden (trafik som går till den virtuella datorn)|VMName|
|Nätverk – inkommande|Ja|Nätverk i fakturerbart (inaktuellt)|Byte|Totalt|Antalet fakturerbara byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik) (inaktuell)|VMName|
|Totalt nätverk|Ja|Totalt nätverk|Byte|Totalt|Antalet byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik)|VMName|
|Nätverk – utgående|Ja|Fakturerbart för nätverk (inaktuellt)|Byte|Totalt|Antalet fakturerbara byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik) (inaktuell)|VMName|
|Totalt nätverk|Ja|Totalt nätverk|Byte|Totalt|Antalet byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik)|VMName|
|Ködjup för OS-disk|Ja|Ködjup för OS-disk (för hands version)|Antal|Genomsnitt|Ködjup i operativ systemets disk (eller Kölängd)|VMName|
|Lästa byte för OS-disk/SEK|Ja|Lästa byte för operativ system disk/SEK (för hands version)|CountPerSecond|Genomsnitt|Byte/s lästa från en enskild disk under övervaknings perioden för operativ system disken|VMName|
|Läs åtgärder för operativ system disk/SEK|Ja|Läs åtgärder för operativ system disk/SEK (för hands version)|CountPerSecond|Genomsnitt|Läsa IOPS från en enskild disk under övervaknings perioden för operativ system disken|VMName|
|Skrivna byte per operativ system disk/SEK|Ja|Skrivna byte per operativ system disk/SEK (för hands version)|CountPerSecond|Genomsnitt|Byte/s skrivs till en enskild disk under övervaknings perioden för operativ system disken|VMName|
|Skriv åtgärder för operativ system disk/SEK|Ja|Skriv åtgärder för operativ system disk/SEK (för hands version)|CountPerSecond|Genomsnitt|Skriv IOPS från en enskild disk under övervaknings perioden för operativ system disken|VMName|
|KÖDJUP för operativ system per disk|Ja|OS-KÖDJUP (inaktuellt)|Antal|Genomsnitt|Ködjup i operativ systemets disk (eller Kölängd)|Inga dimensioner|
|Lästa byte per operativ system disk/SEK|Ja|Lästa byte för OS-disk/SEK (inaktuell)|CountPerSecond|Genomsnitt|Byte/s lästa från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Läs åtgärder för operativ system per disk/SEK|Ja|Läs åtgärder för operativ system disk/SEK (inaktuellt)|CountPerSecond|Genomsnitt|Läsa IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Skrivna byte per operativ system disk/SEK|Ja|Skrivna byte per operativ system disk/SEK (inaktuellt)|CountPerSecond|Genomsnitt|Byte/s skrivs till en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Skriv åtgärder för operativ system per disk/SEK|Ja|Skriv åtgärder för operativ system disk/SEK (inaktuellt)|CountPerSecond|Genomsnitt|Skriv IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Utgående flöden|Ja|Utgående flöden|Antal|Genomsnitt|Utgående flöden är antalet aktuella flöden i den utgående riktningen (trafik som går ut från den virtuella datorn)|VMName|
|Längsta skapande frekvens för utgående flöden|Ja|Längsta skapande frekvens för utgående flöden|CountPerSecond|Genomsnitt|Den maximala skapande frekvensen för utgående flöden (trafik som går ut från den virtuella datorn)|VMName|
|Per disk KÖDJUP|Ja|KÖDJUP för data disk (inaktuellt)|Antal|Genomsnitt|Data diskens ködjup (eller Kölängd)|SlotId|
|Lästa byte per disk/SEK|Ja|Lästa byte på datadisk/SEK (inaktuellt)|CountPerSecond|Genomsnitt|Byte/s lästa från en enskild disk under övervaknings perioden|SlotId|
|Läs åtgärder per disk/SEK|Ja|Läs åtgärder för data disk/SEK (inaktuellt)|CountPerSecond|Genomsnitt|Läsa IOPS från en enskild disk under övervaknings perioden|SlotId|
|Skrivna byte per disk/SEK|Ja|Skrivna byte på datadisk/SEK (inaktuellt)|CountPerSecond|Genomsnitt|Byte/s skrivs till en enskild disk under övervaknings perioden|SlotId|
|Skriv åtgärder per disk/SEK|Ja|Skriv åtgärder för data disk/SEK (inaktuellt)|CountPerSecond|Genomsnitt|Skriv IOPS från en enskild disk under övervaknings perioden|SlotId|
|Processorprocentandel|Ja|Processorprocentandel|Procent|Genomsnitt|Procent andelen allokerade beräknings enheter som för närvarande används av den eller de virtuella datorerna|VMName|
|Läsning av Premium data disk-cache|Ja|Läsning av Premium data disk-cache (för hands version)|Procent|Genomsnitt|Läsning av Premium data disk-cache|LUN, VMName|
|Read missar i Premium data-diskcachen|Ja|Read missar i Premium data disk cache (för hands version)|Procent|Genomsnitt|Read missar i Premium data-diskcachen|LUN, VMName|
|Läsning av Premium OS-diskcachen|Ja|Läsning av Premium OS-diskcachen (för hands version)|Procent|Genomsnitt|Läsning av Premium OS-diskcachen|VMName|
|Read missar i Premium OS-diskcachen|Ja|Read missar i Premium OS-diskcachen (för hands version)|Procent|Genomsnitt|Read missar i Premium OS-diskcachen|VMName|


## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft. Compute/virtualMachineScaleSets/virtualMachines

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Förbrukade CPU-krediter|Ja|Förbrukade CPU-krediter|Antal|Genomsnitt|Totalt antal krediter som konsumeras av den virtuella datorn|Inga dimensioner|
|Återstående CPU-krediter|Ja|Återstående CPU-krediter|Antal|Genomsnitt|Totalt antal krediter som är tillgängliga för burst|Inga dimensioner|
|Ködjup för datadisk|Ja|Data disk Queue djup (för hands version)|Antal|Genomsnitt|Data diskens ködjup (eller Kölängd)|ENHETEN|
|Lästa byte på datadisk/SEK|Ja|Lästa byte på datadisk/SEK (för hands version)|CountPerSecond|Genomsnitt|Byte/s lästa från en enskild disk under övervaknings perioden|ENHETEN|
|Läs åtgärder för data disk/SEK|Ja|Läs åtgärder för data disk/SEK (för hands version)|CountPerSecond|Genomsnitt|Läsa IOPS från en enskild disk under övervaknings perioden|ENHETEN|
|Skrivna byte på datadisk/SEK|Ja|Skrivna byte på datadisk/SEK (för hands version)|CountPerSecond|Genomsnitt|Byte/s skrivs till en enskild disk under övervaknings perioden|ENHETEN|
|Skriv åtgärder för data disk/SEK|Ja|Skriv åtgärder för data disk/SEK (för hands version)|CountPerSecond|Genomsnitt|Skriv IOPS från en enskild disk under övervaknings perioden|ENHETEN|
|Lästa byte på disk|Ja|Lästa byte på disk|Byte|Totalt|Byte som lästs från disk under övervaknings perioden|Inga dimensioner|
|Disk Läs åtgärder/SEK|Ja|Disk Läs åtgärder/SEK|CountPerSecond|Genomsnitt|Disk-Läs-IOPS|Inga dimensioner|
|Disk-skrivna byte|Ja|Disk-skrivna byte|Byte|Totalt|Byte som skrivits till disk under övervaknings perioden|Inga dimensioner|
|Disk skrivnings åtgärder/SEK|Ja|Disk skrivnings åtgärder/SEK|CountPerSecond|Genomsnitt|Skriv IOPS för disk|Inga dimensioner|
|Inkommande flöden|Ja|Inkommande flöden|Antal|Genomsnitt|Inkommande flöden är antalet aktuella flöden i den inkommande riktningen (trafik som går till den virtuella datorn)|Inga dimensioner|
|Högsta skapande frekvens för inkommande flöden|Ja|Högsta skapande frekvens för inkommande flöden|CountPerSecond|Genomsnitt|Högsta skapande frekvens för inkommande flöden (trafik som går till den virtuella datorn)|Inga dimensioner|
|Nätverk – inkommande|Ja|Nätverk i fakturerbart (inaktuellt)|Byte|Totalt|Antalet fakturerbara byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik) (inaktuell)|Inga dimensioner|
|Totalt nätverk|Ja|Totalt nätverk|Byte|Totalt|Antalet byte som tagits emot på alla nätverks gränssnitt av den eller de virtuella datorerna (inkommande trafik)|Inga dimensioner|
|Nätverk – utgående|Ja|Fakturerbart för nätverk (inaktuellt)|Byte|Totalt|Antalet fakturerbara byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik) (inaktuell)|Inga dimensioner|
|Totalt nätverk|Ja|Totalt nätverk|Byte|Totalt|Antalet byte ut i alla nätverks gränssnitt av den eller de virtuella datorerna (utgående trafik)|Inga dimensioner|
|Ködjup för OS-disk|Ja|Ködjup för OS-disk (för hands version)|Antal|Genomsnitt|Ködjup i operativ systemets disk (eller Kölängd)|Inga dimensioner|
|Lästa byte för OS-disk/SEK|Ja|Lästa byte för operativ system disk/SEK (för hands version)|CountPerSecond|Genomsnitt|Byte/s lästa från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Läs åtgärder för operativ system disk/SEK|Ja|Läs åtgärder för operativ system disk/SEK (för hands version)|CountPerSecond|Genomsnitt|Läsa IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Skrivna byte per operativ system disk/SEK|Ja|Skrivna byte per operativ system disk/SEK (för hands version)|CountPerSecond|Genomsnitt|Byte/s skrivs till en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Skriv åtgärder för operativ system disk/SEK|Ja|Skriv åtgärder för operativ system disk/SEK (för hands version)|CountPerSecond|Genomsnitt|Skriv IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|KÖDJUP för operativ system per disk|Ja|OS-KÖDJUP (inaktuellt)|Antal|Genomsnitt|Ködjup i operativ systemets disk (eller Kölängd)|Inga dimensioner|
|Lästa byte per operativ system disk/SEK|Ja|Lästa byte för OS-disk/SEK (inaktuell)|CountPerSecond|Genomsnitt|Byte/s lästa från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Läs åtgärder för operativ system per disk/SEK|Ja|Läs åtgärder för operativ system disk/SEK (inaktuellt)|CountPerSecond|Genomsnitt|Läsa IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Skrivna byte per operativ system disk/SEK|Ja|Skrivna byte per operativ system disk/SEK (inaktuellt)|CountPerSecond|Genomsnitt|Byte/s skrivs till en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Skriv åtgärder för operativ system per disk/SEK|Ja|Skriv åtgärder för operativ system disk/SEK (inaktuellt)|CountPerSecond|Genomsnitt|Skriv IOPS från en enskild disk under övervaknings perioden för operativ system disken|Inga dimensioner|
|Utgående flöden|Ja|Utgående flöden|Antal|Genomsnitt|Utgående flöden är antalet aktuella flöden i den utgående riktningen (trafik som går ut från den virtuella datorn)|Inga dimensioner|
|Längsta skapande frekvens för utgående flöden|Ja|Längsta skapande frekvens för utgående flöden|CountPerSecond|Genomsnitt|Den maximala skapande frekvensen för utgående flöden (trafik som går ut från den virtuella datorn)|Inga dimensioner|
|Per disk KÖDJUP|Ja|KÖDJUP för data disk (inaktuellt)|Antal|Genomsnitt|Data diskens ködjup (eller Kölängd)|SlotId|
|Lästa byte per disk/SEK|Ja|Lästa byte på datadisk/SEK (inaktuellt)|CountPerSecond|Genomsnitt|Byte/s lästa från en enskild disk under övervaknings perioden|SlotId|
|Läs åtgärder per disk/SEK|Ja|Läs åtgärder för data disk/SEK (inaktuellt)|CountPerSecond|Genomsnitt|Läsa IOPS från en enskild disk under övervaknings perioden|SlotId|
|Skrivna byte per disk/SEK|Ja|Skrivna byte på datadisk/SEK (inaktuellt)|CountPerSecond|Genomsnitt|Byte/s skrivs till en enskild disk under övervaknings perioden|SlotId|
|Skriv åtgärder per disk/SEK|Ja|Skriv åtgärder för data disk/SEK (inaktuellt)|CountPerSecond|Genomsnitt|Skriv IOPS från en enskild disk under övervaknings perioden|SlotId|
|Processorprocentandel|Ja|Processorprocentandel|Procent|Genomsnitt|Procent andelen allokerade beräknings enheter som för närvarande används av den eller de virtuella datorerna|Inga dimensioner|
|Läsning av Premium data disk-cache|Ja|Läsning av Premium data disk-cache (för hands version)|Procent|Genomsnitt|Läsning av Premium data disk-cache|ENHETEN|
|Read missar i Premium data-diskcachen|Ja|Read missar i Premium data disk cache (för hands version)|Procent|Genomsnitt|Read missar i Premium data-diskcachen|ENHETEN|
|Läsning av Premium OS-diskcachen|Ja|Läsning av Premium OS-diskcachen (för hands version)|Procent|Genomsnitt|Läsning av Premium OS-diskcachen|Inga dimensioner|
|Read missar i Premium OS-diskcachen|Ja|Read missar i Premium OS-diskcachen (för hands version)|Procent|Genomsnitt|Read missar i Premium OS-diskcachen|Inga dimensioner|


## <a name="microsoftcontainerinstancecontainergroups"></a>Microsoft. ContainerInstance/containerGroups

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|CpuUsage|Ja|Processoranvändning|Antal|Genomsnitt|CPU-användning på alla kärnor i millicores.|containerName|
|MemoryUsage|Ja|Minnesanvändning|Byte|Genomsnitt|Total minnes användning i byte.|containerName|
|NetworkBytesReceivedPerSecond|Ja|Mottagna nätverks byte per sekund|Byte|Genomsnitt|Mottagna nätverks byte per sekund.|Inga dimensioner|
|NetworkBytesTransmittedPerSecond|Ja|Överförda nätverks byte per sekund|Byte|Genomsnitt|Överförda nätverks byte per sekund.|Inga dimensioner|


## <a name="microsoftcontainerregistryregistries"></a>Microsoft. ContainerRegistry/register

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|AgentPoolCPUTime|Ja|Agentpoolegenskap CPU-tid|Sekunder|Totalt|Agentpoolegenskap CPU-tid i sekunder|Inga dimensioner|
|RunDuration|Ja|Körnings tid|Millisekunder|Totalt|Körnings tid i millisekunder|Inga dimensioner|
|SuccessfulPullCount|Ja|Antal lyckade pull-överföringar|Antal|Genomsnitt|Antal lyckade bild hämtningar|Inga dimensioner|
|SuccessfulPushCount|Ja|Antal lyckade push-meddelanden|Antal|Genomsnitt|Antal lyckade avbildnings push-meddelanden|Inga dimensioner|
|TotalPullCount|Ja|Totalt antal hämtningar|Antal|Genomsnitt|Totalt antal bild hämtningar|Inga dimensioner|
|TotalPushCount|Ja|Totalt antal push-meddelanden|Antal|Genomsnitt|Totalt antal push-meddelanden för avbildningar|Inga dimensioner|


## <a name="microsoftcontainerservicemanagedclusters"></a>Microsoft. container service/managedClusters

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|kube_node_status_allocatable_cpu_cores|Inga|Totalt antal tillgängliga processor kärnor i ett hanterat kluster|Antal|Genomsnitt|Totalt antal tillgängliga processor kärnor i ett hanterat kluster|Inga dimensioner|
|kube_node_status_allocatable_memory_bytes|Inga|Total mängd tillgängligt minne i ett hanterat kluster|Byte|Genomsnitt|Total mängd tillgängligt minne i ett hanterat kluster|Inga dimensioner|
|kube_node_status_condition|Inga|Status för olika nod villkor|Antal|Genomsnitt|Status för olika nod villkor|villkor, status, status2, nod|
|kube_pod_status_phase|Inga|Antal poddar per fas|Antal|Genomsnitt|Antal poddar per fas|fas, namnrymd, Pod|
|kube_pod_status_ready|Inga|Antal poddar i klart läge|Antal|Genomsnitt|Antal poddar i klart läge|namnrymd, pod, villkor|


## <a name="microsoftcustomprovidersresourceproviders"></a>Microsoft. CustomProviders/resourceproviders

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|FailedRequests|Ja|Misslyckade begäranden|Antal|Totalt|Hämtar de tillgängliga loggarna för anpassade resurs leverantörer|HttpMethod, CallPath, StatusCode|
|SuccessfullRequests|Ja|Slutförda förfrågningar|Antal|Totalt|Lyckade förfrågningar gjorda av den anpassade providern|HttpMethod, CallPath, StatusCode|


## <a name="microsoftdataboxedgedataboxedgedevices"></a>Microsoft. DataBoxEdge/dataBoxEdgeDevices

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Availablecapacity;)|Ja|Tillgänglig kapacitet|Byte|Genomsnitt|Tillgänglig kapacitet i byte under rapporterings perioden.|Inga dimensioner|
|BytesUploadedToCloud|Ja|Överförda moln byte (enhet)|Byte|Genomsnitt|Det totala antalet byte som överförs till Azure från en enhet under rapporterings perioden.|Inga dimensioner|
|BytesUploadedToCloudPerShare|Ja|Överförda moln byte (resurs)|Byte|Genomsnitt|Det totala antalet byte som överförs till Azure från en resurs under rapporterings perioden.|Dela|
|CloudReadThroughput|Ja|Data flöde för moln hämtning|BytesPerSecond|Genomsnitt|Molnet Ladda ned data flödet till Azure under rapporterings perioden.|Inga dimensioner|
|CloudReadThroughputPerShare|Ja|Data flöde för moln hämtning (resurs)|BytesPerSecond|Genomsnitt|Ladda ned data flödet till Azure från en resurs under rapporterings perioden.|Dela|
|CloudUploadThroughput|Ja|Moln överförings data flöde|BytesPerSecond|Genomsnitt|Moln överförings data flödet till Azure under rapporterings perioden.|Inga dimensioner|
|CloudUploadThroughputPerShare|Ja|Moln överförings data flöde (resurs)|BytesPerSecond|Genomsnitt|Ladda upp data flödet till Azure från en resurs under rapporterings perioden.|Dela|
|HyperVMemoryUtilization|Ja|Edge Compute-minnes användning|Procent|Genomsnitt|Mängden RAM-minne som används|InstanceName|
|HyperVVirtualProcessorUtilization|Ja|Edge Compute-procent CPU|Procent|Genomsnitt|CPU-användning i procent|InstanceName|
|NICReadThroughput|Ja|Läs data flöde (nätverk)|BytesPerSecond|Genomsnitt|Läs data flödet för nätverks gränssnittet på enheten i rapporterings perioden för alla volymer i gatewayen.|InstanceName|
|NICWriteThroughput|Ja|Skriv data flöde (nätverk)|BytesPerSecond|Genomsnitt|Skriv data flödet för nätverks gränssnittet på enheten i rapporterings perioden för alla volymer i gatewayen.|InstanceName|
|Enhet|Ja|Total kapacitet|Byte|Genomsnitt|Total kapacitet|Inga dimensioner|


## <a name="microsoftdatafactorydatafactories"></a>Microsoft. DataFactory/datafactories

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|FailedRuns|Ja|Misslyckade körningar|Antal|Totalt||pipelineName, activityName|
|SuccessfulRuns|Ja|Lyckade körningar|Antal|Totalt||pipelineName, activityName|


## <a name="microsoftdatafactoryfactories"></a>Microsoft. DataFactory/fabriker

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|ActivityCancelledRuns|Ja|Avbrutna aktiviteter kör mått|Antal|Totalt||ActivityType, PipelineName, FailureType, Name|
|ActivityFailedRuns|Ja|Misslyckad aktivitet kör mått|Antal|Totalt||ActivityType, PipelineName, FailureType, Name|
|ActivitySucceededRuns|Ja|Genomförd aktivitet kör mått|Antal|Totalt||ActivityType, PipelineName, FailureType, Name|
|FactorySizeInGbUnits|Ja|Total fabriks storlek (GB enhet)|Antal|Maximal||Inga dimensioner|
|IntegrationRuntimeAvailableMemory|Ja|Tillgängligt minne för integration runtime|Byte|Genomsnitt||IntegrationRuntimeName, nodnamn|
|IntegrationRuntimeAvailableNodeNumber|Ja|Antal tillgängliga noder för integration runtime|Antal|Genomsnitt||IntegrationRuntimeName|
|IntegrationRuntimeAverageTaskPickupDelay|Ja|Varaktighet för integration runtime-kö|Sekunder|Genomsnitt||IntegrationRuntimeName|
|IntegrationRuntimeCpuPercentage|Ja|PROCESSOR användning för integration runtime|Procent|Genomsnitt||IntegrationRuntimeName, nodnamn|
|IntegrationRuntimeQueueLength|Ja|Kölängd för integration runtime|Antal|Genomsnitt||IntegrationRuntimeName|
|MaxAllowedFactorySizeInGbUnits|Ja|Högsta tillåtna fabriks storlek (GB enhet)|Antal|Maximal||Inga dimensioner|
|MaxAllowedResourceCount|Ja|Maximalt antal tillåtna entiteter|Antal|Maximal||Inga dimensioner|
|PipelineCancelledRuns|Ja|Avbruten pipeline kör mått|Antal|Totalt||FailureType, namn|
|PipelineFailedRuns|Ja|Misslyckad pipeline kör mått|Antal|Totalt||FailureType, namn|
|PipelineSucceededRuns|Ja|Slutförd pipeline kör mått|Antal|Totalt||FailureType, namn|
|ResourceCount|Ja|Totalt antal entiteter|Antal|Maximal||Inga dimensioner|
|TriggerCancelledRuns|Ja|Avbrutna utlösare kör mått|Antal|Totalt||Namn, FailureType|
|TriggerFailedRuns|Ja|Misslyckad utlösare kör mått|Antal|Totalt||Namn, FailureType|
|TriggerSucceededRuns|Ja|Lyckade utlösare kör mått|Antal|Totalt||Namn, FailureType|


## <a name="microsoftdatalakestoreaccounts"></a>Microsoft. DataLakeStore/konton

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|DataRead|Ja|Lästa data|Byte|Totalt|Total mängd data som lästs från kontot.|Inga dimensioner|
|DataWritten|Ja|Skrivna data|Byte|Totalt|Total mängd data som skrivs till kontot.|Inga dimensioner|
|ReadRequests|Ja|Läs begär Anden|Antal|Totalt|Antal data läsnings begär anden till kontot.|Inga dimensioner|
|TotalStorage|Ja|Totalt lagringsutrymme|Byte|Maximal|Den totala mängden data som lagras i kontot.|Inga dimensioner|
|WriteRequests|Ja|Skriv förfrågningar|Antal|Totalt|Antal data skrivnings begär anden till kontot.|Inga dimensioner|


## <a name="microsoftdbformariadbservers"></a>Microsoft. DBforMariaDB/servers

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|active_connections|Ja|Aktiva anslutningar|Antal|Genomsnitt|Aktiva anslutningar|Inga dimensioner|
|backup_storage_used|Ja|Lagring av säkerhets kopior som används|Byte|Genomsnitt|Lagring av säkerhets kopior som används|Inga dimensioner|
|connections_failed|Ja|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inga dimensioner|
|cpu_percent|Ja|CPU-procent|Procent|Genomsnitt|CPU-procent|Inga dimensioner|
|io_consumption_percent|Ja|I/o procent|Procent|Genomsnitt|I/o procent|Inga dimensioner|
|memory_percent|Ja|Minnes procent|Procent|Genomsnitt|Minnes procent|Inga dimensioner|
|network_bytes_egress|Ja|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inga dimensioner|
|network_bytes_ingress|Ja|Nätverk – inkommande|Byte|Totalt|Nätverk i över aktiva anslutningar|Inga dimensioner|
|seconds_behind_master|Ja|Fördröjning för replikering på några sekunder|Antal|Maximal|Fördröjning för replikering på några sekunder|Inga dimensioner|
|serverlog_storage_limit|Ja|Server logg lagrings gräns|Byte|Genomsnitt|Server logg lagrings gräns|Inga dimensioner|
|serverlog_storage_percent|Ja|Server logg lagrings procent|Procent|Genomsnitt|Server logg lagrings procent|Inga dimensioner|
|serverlog_storage_usage|Ja|Server logg lagring används|Byte|Genomsnitt|Server logg lagring används|Inga dimensioner|
|storage_limit|Ja|Lagrings gräns|Byte|Maximal|Lagrings gräns|Inga dimensioner|
|storage_percent|Ja|Lagrings procent|Procent|Genomsnitt|Lagrings procent|Inga dimensioner|
|storage_used|Ja|Använt lagrings utrymme|Byte|Genomsnitt|Använt lagrings utrymme|Inga dimensioner|


## <a name="microsoftdbformysqlservers"></a>Microsoft. DBforMySQL/servers

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|active_connections|Ja|Aktiva anslutningar|Antal|Genomsnitt|Aktiva anslutningar|Inga dimensioner|
|backup_storage_used|Ja|Lagring av säkerhets kopior som används|Byte|Genomsnitt|Lagring av säkerhets kopior som används|Inga dimensioner|
|connections_failed|Ja|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inga dimensioner|
|cpu_percent|Ja|CPU-procent|Procent|Genomsnitt|CPU-procent|Inga dimensioner|
|io_consumption_percent|Ja|I/o procent|Procent|Genomsnitt|I/o procent|Inga dimensioner|
|memory_percent|Ja|Minnes procent|Procent|Genomsnitt|Minnes procent|Inga dimensioner|
|network_bytes_egress|Ja|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inga dimensioner|
|network_bytes_ingress|Ja|Nätverk – inkommande|Byte|Totalt|Nätverk i över aktiva anslutningar|Inga dimensioner|
|seconds_behind_master|Ja|Fördröjning för replikering på några sekunder|Antal|Maximal|Fördröjning för replikering på några sekunder|Inga dimensioner|
|serverlog_storage_limit|Ja|Server logg lagrings gräns|Byte|Maximal|Server logg lagrings gräns|Inga dimensioner|
|serverlog_storage_percent|Ja|Server logg lagrings procent|Procent|Genomsnitt|Server logg lagrings procent|Inga dimensioner|
|serverlog_storage_usage|Ja|Server logg lagring används|Byte|Genomsnitt|Server logg lagring används|Inga dimensioner|
|storage_limit|Ja|Lagrings gräns|Byte|Maximal|Lagrings gräns|Inga dimensioner|
|storage_percent|Ja|Lagrings procent|Procent|Genomsnitt|Lagrings procent|Inga dimensioner|
|storage_used|Ja|Använt lagrings utrymme|Byte|Genomsnitt|Använt lagrings utrymme|Inga dimensioner|


## <a name="microsoftdbforpostgresqlflexibleservers"></a>Microsoft. DBforPostgreSQL/flexibleServers

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|active_connections|Ja|Aktiva anslutningar|Antal|Genomsnitt|Aktiva anslutningar|Inga dimensioner|
|backup_storage_used|Ja|Lagring av säkerhets kopior som används|Byte|Genomsnitt|Lagring av säkerhets kopior som används|Inga dimensioner|
|connections_failed|Ja|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inga dimensioner|
|connections_succeeded|Ja|Lyckade anslutningar|Antal|Totalt|Lyckade anslutningar|Inga dimensioner|
|cpu_credits_consumed|Ja|Förbrukade CPU-krediter|Antal|Genomsnitt|Totalt antal krediter som förbrukas av databas servern|Inga dimensioner|
|cpu_credits_remaining|Ja|Återstående CPU-krediter|Antal|Genomsnitt|Totalt antal krediter som är tillgängliga för burst|Inga dimensioner|
|cpu_percent|Ja|CPU-procent|Procent|Genomsnitt|CPU-procent|Inga dimensioner|
|disk_queue_depth|Ja|Disk-ködjup|Antal|Genomsnitt|Antal utestående I/O-åtgärder till data disken|Inga dimensioner|
|IOPS|Ja|IOPS|Antal|Genomsnitt|I/o-åtgärder per sekund|Inga dimensioner|
|maximum_used_transactionIDs|Ja|Högsta antal använda transaktions-ID: n|Antal|Genomsnitt|Högsta antal använda transaktions-ID: n|Inga dimensioner|
|memory_percent|Ja|Minnes procent|Procent|Genomsnitt|Minnes procent|Inga dimensioner|
|network_bytes_egress|Ja|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inga dimensioner|
|network_bytes_ingress|Ja|Nätverk – inkommande|Byte|Totalt|Nätverk i över aktiva anslutningar|Inga dimensioner|
|read_iops|Ja|Läs IOPS|Antal|Genomsnitt|Antal Läs åtgärder för data diskar I/O per sekund|Inga dimensioner|
|read_throughput|Ja|Lästa data flöde byte/s|Antal|Genomsnitt|Lästa byte per sekund från data disken under övervaknings perioden|Inga dimensioner|
|storage_free|Ja|Ledigt lagrings utrymme|Byte|Genomsnitt|Ledigt lagrings utrymme|Inga dimensioner|
|storage_percent|Ja|Lagrings procent|Procent|Genomsnitt|Lagrings procent|Inga dimensioner|
|storage_used|Ja|Använt lagrings utrymme|Byte|Genomsnitt|Använt lagrings utrymme|Inga dimensioner|
|txlogs_storage_used|Ja|Transaktions logg lagring används|Byte|Genomsnitt|Transaktions logg lagring används|Inga dimensioner|
|write_iops|Ja|Skriv IOPS|Antal|Genomsnitt|Antal Skriv åtgärder för data diskar I/O per sekund|Inga dimensioner|
|write_throughput|Ja|Skrivna byte/s i data flöde|Antal|Genomsnitt|Skrivna byte per sekund till data disken under övervaknings perioden|Inga dimensioner|


## <a name="microsoftdbforpostgresqlservers"></a>Microsoft. DBforPostgreSQL/servers

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|active_connections|Ja|Aktiva anslutningar|Antal|Genomsnitt|Aktiva anslutningar|Inga dimensioner|
|backup_storage_used|Ja|Lagring av säkerhets kopior som används|Byte|Genomsnitt|Lagring av säkerhets kopior som används|Inga dimensioner|
|connections_failed|Ja|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inga dimensioner|
|cpu_percent|Ja|CPU-procent|Procent|Genomsnitt|CPU-procent|Inga dimensioner|
|io_consumption_percent|Ja|I/o procent|Procent|Genomsnitt|I/o procent|Inga dimensioner|
|memory_percent|Ja|Minnes procent|Procent|Genomsnitt|Minnes procent|Inga dimensioner|
|network_bytes_egress|Ja|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inga dimensioner|
|network_bytes_ingress|Ja|Nätverk – inkommande|Byte|Totalt|Nätverk i över aktiva anslutningar|Inga dimensioner|
|pg_replica_log_delay_in_bytes|Ja|Maximal fördröjning mellan repliker|Byte|Maximal|Fördröjning i byte för den flesta isolerings repliken|Inga dimensioner|
|pg_replica_log_delay_in_seconds|Ja|Replik fördröjning|Sekunder|Maximal|Replik fördröjning i sekunder|Inga dimensioner|
|serverlog_storage_limit|Ja|Server logg lagrings gräns|Byte|Maximal|Server logg lagrings gräns|Inga dimensioner|
|serverlog_storage_percent|Ja|Server logg lagrings procent|Procent|Genomsnitt|Server logg lagrings procent|Inga dimensioner|
|serverlog_storage_usage|Ja|Server logg lagring används|Byte|Genomsnitt|Server logg lagring används|Inga dimensioner|
|storage_limit|Ja|Lagrings gräns|Byte|Maximal|Lagrings gräns|Inga dimensioner|
|storage_percent|Ja|Lagrings procent|Procent|Genomsnitt|Lagrings procent|Inga dimensioner|
|storage_used|Ja|Använt lagrings utrymme|Byte|Genomsnitt|Använt lagrings utrymme|Inga dimensioner|


## <a name="microsoftdbforpostgresqlserversv2"></a>Microsoft. DBforPostgreSQL/serversv2

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|active_connections|Ja|Aktiva anslutningar|Antal|Genomsnitt|Aktiva anslutningar|Inga dimensioner|
|cpu_percent|Ja|CPU-procent|Procent|Genomsnitt|CPU-procent|Inga dimensioner|
|IOPS|Ja|IOPS|Antal|Genomsnitt|I/o-åtgärder per sekund|Inga dimensioner|
|memory_percent|Ja|Minnes procent|Procent|Genomsnitt|Minnes procent|Inga dimensioner|
|network_bytes_egress|Ja|Nätverk – utgående|Byte|Totalt|Nätverk ut över aktiva anslutningar|Inga dimensioner|
|network_bytes_ingress|Ja|Nätverk – inkommande|Byte|Totalt|Nätverk i över aktiva anslutningar|Inga dimensioner|
|storage_percent|Ja|Lagrings procent|Procent|Genomsnitt|Lagrings procent|Inga dimensioner|
|storage_used|Ja|Använt lagrings utrymme|Byte|Genomsnitt|Använt lagrings utrymme|Inga dimensioner|


## <a name="microsoftdevicesiothubs"></a>Microsoft. Devices/IotHubs

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|C2D. commands. utgående. Abandon. lyckades|Ja|Övergivna C2D-meddelanden|Antal|Totalt|Antal meddelanden från moln till enhet som har övergivits av enheten|Inga dimensioner|
|C2D. commands. utgående. Complete. lyckades|Ja|C2D meddelande leveranser har slutförts|Antal|Totalt|Antalet meddelande leveranser från moln till enhet har slutförts av enheten|Inga dimensioner|
|C2D. commands. rekasta. Success|Ja|Avvisade C2D-meddelanden|Antal|Totalt|Antal meddelanden från moln till enhet som avvisats av enheten|Inga dimensioner|
|C2D. Methods. Failure|Ja|Misslyckade direkta metod anrop|Antal|Totalt|Antalet misslyckade direkta metod anrop.|Inga dimensioner|
|C2D. Methods. requestSize|Ja|Begär ande storlek för direkta metod anrop|Byte|Genomsnitt|Genomsnitt, min och Max för alla lyckade direkta metod begär Anden.|Inga dimensioner|
|C2D. Methods. responseSize|Ja|Svars storlek för direkta metod anrop|Byte|Genomsnitt|Medelvärde, min och Max för alla lyckade direkta metod svar.|Inga dimensioner|
|C2D. Methods. Success|Ja|Direkta metod anrop|Antal|Totalt|Antalet lyckade direkta metod anrop.|Inga dimensioner|
|C2D. delad.|Ja|Det gick inte att dubbla läsningar från Server delen|Antal|Totalt|Antalet dubbla läsningar som initierats av alla misslyckade backend-slutpunkter.|Inga dimensioner|
|C2D., delad. storlek|Ja|Svars storlek för dubbla läsningar från Server delen|Byte|Genomsnitt|Genomsnitt, min och Max för alla lyckade, dubbla läsningar som initieras i slut punkt.|Inga dimensioner|
|C2D. delad. lyckades|Ja|Lyckades dubbla läspaket från Server delen|Antal|Totalt|Antalet dubbla läsningar som initierats av alla lyckade.|Inga dimensioner|
|C2D. delad. Update. Failure|Ja|Misslyckade dubbla uppdateringar från Server delen|Antal|Totalt|Antalet dubbla uppdateringar som initierats av alla misslyckade backend-slutpunkter.|Inga dimensioner|
|C2D. dubbla. Update. size|Ja|Storlek på dubbla uppdateringar från Server delen|Byte|Genomsnitt|Genomsnitt, min och Max storlek för alla lyckade, dubbla uppdateringar som initieras.|Inga dimensioner|
|C2D. delad. Update. lyckades|Ja|Lyckades dubbla uppdateringar från Server delen|Antal|Totalt|Antalet fullständiga uppdateringar som initierats av alla lyckade backend-uppdateringar.|Inga dimensioner|
|C2DMessagesExpired|Ja|C2D meddelanden har upphört att gälla|Antal|Totalt|Antal utgångna meddelanden från moln till enhet|Inga dimensioner|
|konfigurationer|Ja|Konfigurations mått|Antal|Totalt|Mått för konfigurations åtgärder|Inga dimensioner|
|connectedDeviceCount|Inga|Anslutna enheter|Antal|Genomsnitt|Antal enheter som är anslutna till din IoT-hubb|Inga dimensioner|
|D2C. endpoints. utgående. Builtity. events|Ja|Routning: meddelanden som levereras till meddelanden/händelser|Antal|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till den inbyggda slut punkten (meddelanden/händelser).|Inga dimensioner|
|D2C. endpoints. utgående. eventHubs|Ja|Routning: meddelanden levererade till Händelsehubben|Antal|Totalt|Antal gånger IoT Hub routning har levererat meddelanden till Event Hub-slutpunkter.|Inga dimensioner|
|D2C. endpoints. utgående. serviceBusQueues|Ja|Routning: meddelanden levererade till Service Bus kö|Antal|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till Service Bus-köns slut punkter.|Inga dimensioner|
|D2C. endpoints. utgående. serviceBusTopics|Ja|Routning: meddelanden levererade till Service Bus ämnet|Antal|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till Service Bus ämnes slut punkter.|Inga dimensioner|
|D2C. endpoints. utgående. Storage|Ja|Routning: meddelanden som levereras till lagring|Antal|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till lagrings slut punkter.|Inga dimensioner|
|D2C. endpoints. utgående. Storage. blob|Ja|Routning: blobbar levererade till lagring|Antal|Totalt|Antal gånger IoT Hub som levererade blobbar till lagrings slut punkter.|Inga dimensioner|
|D2C. endpoints. utgående. Storage. byte|Ja|Routning: data som levereras till lagring|Byte|Totalt|Mängden data (byte) IoT Hub routning som levereras till lagrings slut punkter.|Inga dimensioner|
|D2C. endpoints. latens. Builtin. events|Ja|Routning: meddelande fördröjning för meddelanden/händelser|Millisekunder|Genomsnitt|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub och telemetri intränger in i den inbyggda slut punkten (meddelanden/händelser).|Inga dimensioner|
|D2C. endpoints. latens. eventHubs|Ja|Routning: meddelande fördröjning för Event Hub|Millisekunder|Genomsnitt|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub och meddelande intränger till en Event Hub-slutpunkt.|Inga dimensioner|
|D2C. endpoints. latens. serviceBusQueues|Ja|Routning: meddelande fördröjning för Service Bus kö|Millisekunder|Genomsnitt|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub-och telemetri-meddelanden intränger i en Service Bus Queue-slutpunkt.|Inga dimensioner|
|D2C. endpoints. latens. serviceBusTopics|Ja|Routning: meddelande fördröjning för Service Bus ämne|Millisekunder|Genomsnitt|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub-och telemetri-meddelande intränger i en Service Bus avsnitts slut punkt.|Inga dimensioner|
|D2C. endpoints. svars tid. Storage|Ja|Routning: meddelande fördröjning för lagring|Millisekunder|Genomsnitt|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub-och telemetri-meddelanden intränger i en lagrings slut punkt.|Inga dimensioner|
|D2C. telemetri. utgående.|Ja|Routning: telemetri ignoreras |Antal|Totalt|Antalet gånger som meddelanden släpptes genom IoT Hub routning på grund av död slut punkter. Det här värdet räknar inte meddelanden som levereras till återställnings vägen eftersom ignorerade meddelanden inte levereras där.|Inga dimensioner|
|D2C. telemetri. utgående. fallback|Ja|Routning: meddelanden levererade till reserv|Antal|Totalt|Antalet gånger IoT Hub vidarebefordran av meddelanden till slut punkten som är kopplad till återställnings vägen.|Inga dimensioner|
|D2C. telemetri. utgående. ogiltig|Ja|Routning: telemetri-meddelanden är inkompatibla|Antal|Totalt|Antalet gånger IoT Hub routningen kunde inte leverera meddelanden på grund av inkompatibilitet med slut punkten. Det här värdet omfattar inte återförsök.|Inga dimensioner|
|D2C. telemetri. utgående. överblivna|Ja|Routning: telemetri-meddelanden har överblivna |Antal|Totalt|Antalet gånger som meddelanden har överblivnas av IoT Hub routning eftersom de inte matchade några routningsregler (inklusive återställnings regeln). |Inga dimensioner|
|D2C. telemetri. utgående. lyckades|Ja|Routning: telemetri meddelanden levereras|Antal|Totalt|Antalet gånger som meddelanden har levererats till alla slut punkter med hjälp av IoT Hub routning. Om ett meddelande dirigeras till flera slut punkter ökar det här värdet med ett för varje lyckad leverans. Om ett meddelande skickas till samma slut punkt flera gånger ökar det här värdet med ett för varje lyckad leverans.|Inga dimensioner|
|D2C. telemetri. ingress. allProtocol|Ja|Skicka försök för telemetri|Antal|Totalt|Antalet telemetri från enhet till molnet försökte skickas till din IoT-hubb|Inga dimensioner|
|D2C. telemetri. ingress. sendThrottle|Ja|Antal begränsnings fel|Antal|Totalt|Antal begränsnings fel som beror på begränsning av enhetens data flöde|Inga dimensioner|
|D2C. telemetri. ingress. lyckades|Ja|Meddelande om telemetri|Antal|Totalt|Antal telemetri om enhet till molnet har skickats till din IoT-hubb|Inga dimensioner|
|D2C. delad.|Ja|Misslyckade dubbla läsningar från enheter|Antal|Totalt|Antalet misslyckade, dubbla läsningar som initierats av enheten.|Inga dimensioner|
|D2C., delad. storlek|Ja|Svars storlek för dubbla läsningar från enheter|Byte|Genomsnitt|Genomsnitt, min och Max för alla lyckade, dubbla läsningar som initierats av enheten.|Inga dimensioner|
|D2C. delad. lyckades|Ja|Lyckades dubbla läsningar från enheter|Antal|Totalt|Antalet framgångs rika enhets uppinitierade dubbla läsningar.|Inga dimensioner|
|D2C. delad. Update. Failure|Ja|Misslyckade dubbla uppdateringar från enheter|Antal|Totalt|Antalet misslyckade, dubbla uppdateringar som initierats av enheten.|Inga dimensioner|
|D2C. dubbla. Update. size|Ja|Storlek på dubbla uppdateringar från enheter|Byte|Genomsnitt|Genomsnitt, min och Max storlek för alla lyckade, dubbla uppdateringar som initierats av enheten.|Inga dimensioner|
|D2C. delad. Update. lyckades|Ja|Lyckade dubbla uppdateringar från enheter|Antal|Totalt|Antalet lyckade, dubbla uppdateringar som initierats av enheten.|Inga dimensioner|
|dailyMessageQuotaUsed|Ja|Totalt antal meddelanden som används|Antal|Maximal|Antal sammanlagt antal meddelanden som använts idag|Inga dimensioner|
|deviceDataUsage|Ja|Total användning av enhets data|Byte|Totalt|Överförda byte till och från alla enheter som är anslutna till IotHub|Inga dimensioner|
|deviceDataUsageV2|Ja|Total användning av enhets data (för hands version)|Byte|Totalt|Överförda byte till och från alla enheter som är anslutna till IotHub|Inga dimensioner|
|Devices. connectedDevices. allProtocol|Ja|Anslutna enheter (inaktuella) |Antal|Totalt|Antal enheter som är anslutna till din IoT-hubb|Inga dimensioner|
|enheter. totalDevices|Ja|Totalt antal enheter (inaktuella)|Antal|Totalt|Antal enheter som har registrerats för din IoT-hubb|Inga dimensioner|
|EventGridDeliveries|Ja|Event Grid leveranser|Antal|Totalt|Antalet IoT Hub-händelser som publicerats till Event Grid. Använd resultat dimensionen för antalet lyckade och misslyckade förfrågningar. EventType-dimensionen visar händelse typen ( https://aka.ms/ioteventgrid) .|Resultat, EventType|
|EventGridLatency|Ja|Event Grid svars tid|Millisekunder|Genomsnitt|Den genomsnittliga svars tiden (millisekunder) från när IoT Hub-händelsen genererades till när händelsen publicerades till Event Grid. Det här talet är ett medelvärde mellan alla händelse typer. Använd EventType-dimensionen för att se svars tiden för en speciell typ av händelse.|Typ|
|Jobs. cancelJob. Failure|Ja|Misslyckade jobb-annulleringar|Antal|Totalt|Antalet misslyckade anrop för att avbryta ett jobb.|Inga dimensioner|
|Jobs. cancelJob. lyckades|Ja|Slutförda jobb avbokningar|Antal|Totalt|Antalet lyckade anrop för att avbryta ett jobb.|Inga dimensioner|
|jobb. slutfört|Ja|Slutförda jobb|Antal|Totalt|Antalet slutförda jobb.|Inga dimensioner|
|Jobs. createDirectMethodJob. Failure|Ja|Det gick inte att skapa metod anrops jobb|Antal|Totalt|Antalet misslyckade skapande av direkta anrops jobb för metoden.|Inga dimensioner|
|Jobs. createDirectMethodJob. lyckades|Ja|Lyckade skapande av metod anrops jobb|Antal|Totalt|Antalet slutförda skapande av direkta metod anrops jobb.|Inga dimensioner|
|Jobs. createTwinUpdateJob. Failure|Ja|Det gick inte att skapa dubbla uppdaterings jobb|Antal|Totalt|Antalet misslyckade skapandet av dubbla uppdaterings jobb.|Inga dimensioner|
|Jobs. createTwinUpdateJob. lyckades|Ja|Skapandet av dubbla uppdaterings jobb lyckades|Antal|Totalt|Antalet slutförda skapandet av dubbla uppdaterings jobb.|Inga dimensioner|
|jobb. misslyckades|Ja|Misslyckade jobb|Antal|Totalt|Antalet misslyckade jobb.|Inga dimensioner|
|Jobs. listJobs. Failure|Ja|Misslyckade anrop till List jobb|Antal|Totalt|Antalet misslyckade anrop till List jobb.|Inga dimensioner|
|Jobs. listJobs. lyckades|Ja|Lyckade anrop till List jobb|Antal|Totalt|Antalet lyckade anrop till List jobb.|Inga dimensioner|
|Jobs. queryJobs. Failure|Ja|Misslyckade jobb frågor|Antal|Totalt|Antalet misslyckade anrop till jobb för frågor.|Inga dimensioner|
|Jobs. queryJobs. lyckades|Ja|Slutförda jobb frågor|Antal|Totalt|Antalet lyckade anrop för att köra frågor mot jobb.|Inga dimensioner|
|RoutingDataSizeInBytesDelivered|Ja|Meddelande storlek för routning i byte (för hands version)|Byte|Totalt|Den totala storleken i byte på meddelanden levererade av IoT Hub till en slut punkt. Du kan använda dimensionerna EndpointName och EndpointType för att visa storleken på meddelandena i byte som levereras till dina olika slut punkter. Mått värdet ökar för varje meddelande som levereras, inklusive om meddelandet levereras till flera slut punkter eller om meddelandet levereras till samma slut punkt flera gånger.|EndpointType, EndpointName, RoutingSource|
|RoutingDeliveries|Ja|Dirigera leveranser (för hands version)|Antal|Totalt|Antalet gånger IoT Hub försökte leverera meddelanden till alla slut punkter med hjälp av routning. Använd resultat dimensionen för att se antalet lyckade eller misslyckade försök. Om du vill se orsaken till fel, som ogiltig, släppt eller föräldralös, använder du FailureReasonCategory-dimensionen. Du kan också använda dimensionerna EndpointName och EndpointType för att förstå hur många meddelanden som levererats till dina olika slut punkter. Mått värdet ökar med ett för varje leverans försök, inklusive om meddelandet levereras till flera slut punkter eller om meddelandet levereras till samma slut punkt flera gånger.|EndpointType, EndpointName, FailureReasonCategory, result, RoutingSource|
|RoutingDeliveryLatency|Ja|Leverans svars tid för routning (för hands version)|Millisekunder|Genomsnitt|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub-och telemetri-meddelanden intränger i en slut punkt. Du kan använda dimensionerna EndpointName och EndpointType för att förstå svars tiden för dina olika slut punkter.|EndpointType, EndpointName, RoutingSource|
|totalDeviceCount|Inga|Totalt antal enheter|Antal|Genomsnitt|Antal enheter som har registrerats för din IoT-hubb|Inga dimensioner|
|twinQueries. Failure|Ja|Misslyckade dubbla frågor|Antal|Totalt|Antalet misslyckade dubbla frågor.|Inga dimensioner|
|twinQueries.resultSize|Ja|Resultat storlek för dubbla frågor|Byte|Genomsnitt|Genomsnitt, min och Max för resultat storleken för alla lyckade dubbla frågor.|Inga dimensioner|
|twinQueries. lyckades|Ja|Lyckades dubbla frågor|Antal|Totalt|Antalet lyckade dubbla frågor.|Inga dimensioner|


## <a name="microsoftdevicesprovisioningservices"></a>Microsoft. Devices/provisioningServices

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|AttestationAttempts|Ja|Attesterings försök|Antal|Totalt|Antal försök till enhets attestering|ProvisioningServiceName, status, protokoll|
|DeviceAssignments|Ja|Tilldelade enheter|Antal|Totalt|Antal enheter som har tilldelats till en IoT-hubb|ProvisioningServiceName, IotHubName|
|RegistrationAttempts|Ja|Registrerings försök|Antal|Totalt|Antal enhets registreringar som försökts|ProvisioningServiceName, IotHubName, status|


## <a name="microsoftdocumentdbdatabaseaccounts"></a>Microsoft.DocumentDB/databaseAccounts

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|AddRegion|Ja|Region tillagt|Antal|Antal|Region tillagt|Region|
|AutoscaleMaxThroughput|Inga|Skala maximalt data flöde|Antal|Maximal|Skala maximalt data flöde|DatabaseName, samlings namn|
|AvailableStorage|Inga|föråldrad Tillgängligt lagrings utrymme|Byte|Totalt|"Tillgängligt lagrings utrymme" tas bort från Azure Monitor i slutet av september 2023. Lagrings storleken för Cosmos DB samlingen är nu obegränsad. Den enda begränsningen är att lagrings storleken för varje logisk partitionsnyckel är 20. Du kan aktivera PartitionKeyStatistics i diagnostisk logg för att känna till lagrings förbrukningen för Top partition-nycklar. Om du vill ha mer information om Cosmos DB lagrings kvot kontrollerar du det här dokumentet https://docs.microsoft.com/azure/cosmos-db/concepts-limits . Efter utfasningen inaktive ras de återstående aviserings reglerna som fortfarande definierats för det inaktuella måttet automatiskt efter utgångs datumet.|Samlings namn, DatabaseName, region|
|CassandraConnectionClosures|Inga|Cassandra-anslutningens stängningar|Antal|Totalt|Antalet Cassandra-anslutningar som stängdes, rapporteras med en 1 minuts kornig het|Region, ClosureReason|
|CassandraConnectorAvgReplicationLatency|Inga|Genomsnittlig ReplicationLatency för Cassandra Connector|Millisekunder|Genomsnitt|Genomsnittlig ReplicationLatency för Cassandra Connector|Inga dimensioner|
|CassandraConnectorReplicationHealthStatus|Inga|Hälso status för Cassandra Connector-replikering|Antal|Antal|Hälso status för Cassandra Connector-replikering|NotStarted, ReplicationInProgress, fel|
|CassandraKeyspaceCreate|Inga|Cassandra-tecken avstånd har skapats|Antal|Antal|Cassandra-tecken avstånd har skapats|ResourceName |
|CassandraKeyspaceDelete|Inga|Cassandra-tecken utrymme borttaget|Antal|Antal|Cassandra-tecken utrymme borttaget|ResourceName |
|CassandraKeyspaceThroughputUpdate|Inga|Cassandra-dataflöde har uppdaterats|Antal|Antal|Cassandra-dataflöde har uppdaterats|ResourceName |
|CassandraKeyspaceUpdate|Inga|Cassandra-disk utrymme uppdaterat|Antal|Antal|Cassandra-disk utrymme uppdaterat|ResourceName |
|CassandraRequestCharges|Inga|Avgifter för Cassandra-begäran|Antal|Totalt|Ru: er förbrukat för Cassandra begär Anden|DatabaseName, samlings namn, region, OperationType, ResourceType|
|CassandraRequests|Inga|Cassandra-begäranden|Antal|Antal|Antal Cassandra-begäranden som gjorts|DatabaseName, samlings namn, region, OperationType, ResourceType, ErrorCode|
|CassandraTableCreate|Inga|Cassandra-tabellen har skapats|Antal|Antal|Cassandra-tabellen har skapats|ResourceName, ChildResourceName, |
|CassandraTableDelete|Inga|Cassandra-tabellen har tagits bort|Antal|Antal|Cassandra-tabellen har tagits bort|ResourceName, ChildResourceName, |
|CassandraTableThroughputUpdate|Inga|Cassandra tabell data flöde har uppdaterats|Antal|Antal|Cassandra tabell data flöde har uppdaterats|ResourceName, ChildResourceName, |
|CassandraTableUpdate|Inga|Cassandra-tabellen har uppdaterats|Antal|Antal|Cassandra-tabellen har uppdaterats|ResourceName, ChildResourceName, |
|CreateAccount|Ja|Kontot har skapats|Antal|Antal|Kontot har skapats|Inga dimensioner|
|DataUsage|Inga|Dataanvändning|Byte|Totalt|Total data användning rapporterad med 5 minuters precision|Samlings namn, DatabaseName, region|
|DeleteAccount|Ja|Kontot har tagits bort|Antal|Antal|Kontot har tagits bort|Inga dimensioner|
|DocumentCount|Inga|Antal dokument|Antal|Totalt|Totalt antal dokument som har rapporter ATS med 5 minuters precision|Samlings namn, DatabaseName, region|
|DocumentQuota|Inga|Dokument kvot|Byte|Totalt|Total lagrings kvot som rapporter ATS med 5 minuters precision|Samlings namn, DatabaseName, region|
|GremlinDatabaseCreate|Inga|Gremlin-databas har skapats|Antal|Antal|Gremlin-databas har skapats|ResourceName |
|GremlinDatabaseDelete|Inga|Gremlin-databasen har tagits bort|Antal|Antal|Gremlin-databasen har tagits bort|ResourceName |
|GremlinDatabaseThroughputUpdate|Inga|Gremlin Database-genomflöde har uppdaterats|Antal|Antal|Gremlin Database-genomflöde har uppdaterats|ResourceName |
|GremlinDatabaseUpdate|Inga|Gremlin-databasen har uppdaterats|Antal|Antal|Gremlin-databasen har uppdaterats|ResourceName |
|GremlinGraphCreate|Inga|Gremlin graf har skapats|Antal|Antal|Gremlin graf har skapats|ResourceName, ChildResourceName, |
|GremlinGraphDelete|Inga|Gremlin Graph borttagen|Antal|Antal|Gremlin Graph borttagen|ResourceName, ChildResourceName, |
|GremlinGraphThroughputUpdate|Inga|Gremlin Graph-genomflöde har uppdaterats|Antal|Antal|Gremlin Graph-genomflöde har uppdaterats|ResourceName, ChildResourceName, |
|GremlinGraphUpdate|Inga|Gremlin-diagrammet har uppdaterats|Antal|Antal|Gremlin-diagrammet har uppdaterats|ResourceName, ChildResourceName, |
|IndexUsage|Inga|Indexanvändning|Byte|Totalt|Total användning av index rapporterad med 5 minuters precision|Samlings namn, DatabaseName, region|
|MetadataRequests|Inga|Begär Anden om metadata|Antal|Antal|Antal metadata-begäranden. Cosmos DB behåller systemets metadata-samling för varje konto, vilket gör att du kan räkna upp samlingar, databaser osv och deras konfigurationer kostnads fritt.|DatabaseName, samlings namn, region, StatusCode, |
|MongoCollectionCreate|Inga|Mongo-samling har skapats|Antal|Antal|Mongo-samling har skapats|ResourceName, ChildResourceName, |
|MongoCollectionDelete|Inga|Mongo-samlingen har tagits bort|Antal|Antal|Mongo-samlingen har tagits bort|ResourceName, ChildResourceName, |
|MongoCollectionThroughputUpdate|Inga|Mongo Collection-genomflöde har uppdaterats|Antal|Antal|Mongo Collection-genomflöde har uppdaterats|ResourceName, ChildResourceName, |
|MongoCollectionUpdate|Inga|Mongo-samlingen har uppdaterats|Antal|Antal|Mongo-samlingen har uppdaterats|ResourceName, ChildResourceName, |
|MongoDatabaseDelete|Inga|Mongo-databasen har tagits bort|Antal|Antal|Mongo-databasen har tagits bort|ResourceName |
|MongoDatabaseThroughputUpdate|Inga|Mongo Database-genomflöde har uppdaterats|Antal|Antal|Mongo Database-genomflöde har uppdaterats|ResourceName |
|MongoDBDatabaseCreate|Inga|Mongo-databas har skapats|Antal|Antal|Mongo-databas har skapats|ResourceName |
|MongoDBDatabaseUpdate|Inga|Mongo-databasen har uppdaterats|Antal|Antal|Mongo-databasen har uppdaterats|ResourceName |
|MongoRequests|Ja|Mongo-begäranden|Antal|Antal|Antal Mongo-begäranden som gjorts|DatabaseName, samlings namn, region, CommandName, ErrorCode, status|
|NormalizedRUConsumption|Inga|Normaliserad RU-förbrukning|Procent|Maximal|Max procent per minut för RU-förbrukning|Samlings namn, DatabaseName, region, PartitionKeyRangeId|
|ProvisionedThroughput|Inga|Etablerat dataflöde|Antal|Maximal|Etablerat dataflöde|DatabaseName, samlings namn|
|RegionFailover|Ja|Regionen har redundansväxlats|Antal|Antal|Regionen har redundansväxlats|Inga dimensioner|
|RemoveRegion|Ja|Region borttagen|Antal|Antal|Region borttagen|Region|
|ReplicationLatency|Ja|Fördröjning för P99-replikering|Millisekunder|Genomsnitt|P99 för replikering i käll-och mål regioner för geo-aktiverat konto|SourceRegion, TargetRegion|
|ServerSideLatency|Inga|Svars tid på Server Sidan|Millisekunder|Genomsnitt|Svars tid på Server Sidan|DatabaseName, samlings namn, region, ConnectionMode, OperationType, PublicAPIType|
|ServiceAvailability|Inga|Tjänst tillgänglighet|Procent|Genomsnitt|Konto begär tillgänglighet på en timme, dag eller månads kornig het|Inga dimensioner|
|SqlContainerCreate|Inga|SQL-behållare har skapats|Antal|Antal|SQL-behållare har skapats|ResourceName, ChildResourceName, |
|SqlContainerDelete|Inga|SQL-behållare borttagen|Antal|Antal|SQL-behållare borttagen|ResourceName, ChildResourceName, |
|SqlContainerThroughputUpdate|Inga|SQL container data flöde uppdaterat|Antal|Antal|SQL container data flöde uppdaterat|ResourceName, ChildResourceName, |
|SqlContainerUpdate|Inga|SQL-behållare har uppdaterats|Antal|Antal|SQL-behållare har uppdaterats|ResourceName, ChildResourceName, |
|SqlDatabaseCreate|Inga|SQL-databas har skapats|Antal|Antal|SQL-databas har skapats|ResourceName |
|SqlDatabaseDelete|Inga|SQL-databas borttagen|Antal|Antal|SQL-databas borttagen|ResourceName |
|SqlDatabaseThroughputUpdate|Inga|SQL Database-dataflöde uppdaterat|Antal|Antal|SQL Database-dataflöde uppdaterat|ResourceName |
|SqlDatabaseUpdate|Inga|SQL-databas uppdaterad|Antal|Antal|SQL-databas uppdaterad|ResourceName |
|TableTableCreate|Inga|AzureTable-tabellen har skapats|Antal|Antal|AzureTable-tabellen har skapats|ResourceName |
|TableTableDelete|Inga|AzureTable-tabellen har tagits bort|Antal|Antal|AzureTable-tabellen har tagits bort|ResourceName |
|TableTableThroughputUpdate|Inga|AzureTable tabell data flöde har uppdaterats|Antal|Antal|AzureTable tabell data flöde har uppdaterats|ResourceName |
|TableTableUpdate|Inga|AzureTable-tabellen har uppdaterats|Antal|Antal|AzureTable-tabellen har uppdaterats|ResourceName |
|TotalRequests|Ja|Totalt antal förfrågningar|Antal|Antal|Antal begär Anden som gjorts|DatabaseName, samlings namn, region, StatusCode, OperationType, status|
|TotalRequestUnits|Ja|Totalt antal enheter för programbegäran|Antal|Totalt|Förbrukade enheter för begär Ande|DatabaseName, samlings namn, region, StatusCode, OperationType, status|
|UpdateAccountKeys|Ja|Konto nycklar har uppdaterats|Antal|Antal|Konto nycklar har uppdaterats|KeyType|
|UpdateAccountNetworkSettings|Ja|Kontots nätverks inställningar har uppdaterats|Antal|Antal|Kontots nätverks inställningar har uppdaterats|Inga dimensioner|
|UpdateAccountReplicationSettings|Ja|Kontots replikeringsinställningar har uppdaterats|Antal|Antal|Kontots replikeringsinställningar har uppdaterats|Inga dimensioner|
|UpdateDiagnosticsSettings|Inga|Inställningarna för konto diagnostik har uppdaterats|Antal|Antal|Inställningarna för konto diagnostik har uppdaterats|DiagnosticSettingsName, ResourceGroupName|


## <a name="microsofteventgriddomains"></a>Microsoft. EventGrid/Domains

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Ja|Obeställbara, Brevade händelser|Antal|Totalt|Totalt antal inloggade obeställbara meddelanden som matchar den här händelse prenumerationen|Ämne, EventSubscriptionName, DomainEventSubscriptionName, DeadLetterReason|
|DeliveryAttemptFailCount|Inga|Misslyckade leverans händelser|Antal|Totalt|Totalt antal händelser som inte levererades till den här händelse prenumerationen|Ämne, EventSubscriptionName, DomainEventSubscriptionName, fel, ErrorType|
|DeliverySuccessCount|Ja|Levererade händelser|Antal|Totalt|Totalt antal händelser levererade till den här händelse prenumerationen|Ämne, EventSubscriptionName, DomainEventSubscriptionName|
|DestinationProcessingDurationInMs|Inga|Varaktighet för mål bearbetning|Millisekunder|Genomsnitt|Varaktighet för mål bearbetning i millisekunder|Ämne, EventSubscriptionName, DomainEventSubscriptionName|
|DroppedEventCount|Ja|Ignorerade händelser|Antal|Totalt|Totalt antal ignorerade händelser som matchar den här händelse prenumerationen|Ämne, EventSubscriptionName, DomainEventSubscriptionName, DropReason|
|MatchedEventCount|Ja|Matchade händelser|Antal|Totalt|Totalt antal händelser som matchar den här händelse prenumerationen|Ämne, EventSubscriptionName, DomainEventSubscriptionName|
|PublishFailCount|Ja|Publicera misslyckade händelser|Antal|Totalt|Totalt antal händelser som inte publicerats till det här avsnittet|Ämne, ErrorType, fel|
|PublishSuccessCount|Ja|Publicerade händelser|Antal|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Ämne|
|PublishSuccessLatencyInMs|Ja|Slutför svars tid för publicering|Millisekunder|Totalt|Publicera lyckad svars tid i millisekunder|Inga dimensioner|


## <a name="microsofteventgrideventsubscriptions"></a>Microsoft. EventGrid/eventSubscriptions

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Ja|Obeställbara, Brevade händelser|Antal|Totalt|Totalt antal inloggade obeställbara meddelanden som matchar den här händelse prenumerationen|DeadLetterReason|
|DeliveryAttemptFailCount|Inga|Misslyckade leverans händelser|Antal|Totalt|Totalt antal händelser som inte levererades till den här händelse prenumerationen|Fel, ErrorType|
|DeliverySuccessCount|Ja|Levererade händelser|Antal|Totalt|Totalt antal händelser levererade till den här händelse prenumerationen|Inga dimensioner|
|DestinationProcessingDurationInMs|Inga|Varaktighet för mål bearbetning|Millisekunder|Genomsnitt|Varaktighet för mål bearbetning i millisekunder|Inga dimensioner|
|DroppedEventCount|Ja|Ignorerade händelser|Antal|Totalt|Totalt antal ignorerade händelser som matchar den här händelse prenumerationen|DropReason|
|MatchedEventCount|Ja|Matchade händelser|Antal|Totalt|Totalt antal händelser som matchar den här händelse prenumerationen|Inga dimensioner|


## <a name="microsofteventgridextensiontopics"></a>Microsoft. EventGrid/extensionTopics

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|PublishFailCount|Ja|Publicera misslyckade händelser|Antal|Totalt|Totalt antal händelser som inte publicerats till det här avsnittet|ErrorType, fel|
|PublishSuccessCount|Ja|Publicerade händelser|Antal|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Inga dimensioner|
|PublishSuccessLatencyInMs|Ja|Slutför svars tid för publicering|Millisekunder|Totalt|Publicera lyckad svars tid i millisekunder|Inga dimensioner|
|UnmatchedEventCount|Ja|Omatchade händelser|Antal|Totalt|Totalt antal händelser som inte matchar någon av händelse prenumerationerna för det här avsnittet|Inga dimensioner|


## <a name="microsofteventgridsystemtopics"></a>Microsoft. EventGrid/systemTopics

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Ja|Obeställbara, Brevade händelser|Antal|Totalt|Totalt antal inloggade obeställbara meddelanden som matchar den här händelse prenumerationen|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|Inga|Misslyckade leverans händelser|Antal|Totalt|Totalt antal händelser som inte levererades till den här händelse prenumerationen|Fel, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Ja|Levererade händelser|Antal|Totalt|Totalt antal händelser levererade till den här händelse prenumerationen|EventSubscriptionName|
|DestinationProcessingDurationInMs|Inga|Varaktighet för mål bearbetning|Millisekunder|Genomsnitt|Varaktighet för mål bearbetning i millisekunder|EventSubscriptionName|
|DroppedEventCount|Ja|Ignorerade händelser|Antal|Totalt|Totalt antal ignorerade händelser som matchar den här händelse prenumerationen|DropReason, EventSubscriptionName|
|MatchedEventCount|Ja|Matchade händelser|Antal|Totalt|Totalt antal händelser som matchar den här händelse prenumerationen|EventSubscriptionName|
|PublishFailCount|Ja|Publicera misslyckade händelser|Antal|Totalt|Totalt antal händelser som inte publicerats till det här avsnittet|ErrorType, fel|
|PublishSuccessCount|Ja|Publicerade händelser|Antal|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Inga dimensioner|
|PublishSuccessLatencyInMs|Ja|Slutför svars tid för publicering|Millisekunder|Totalt|Publicera lyckad svars tid i millisekunder|Inga dimensioner|
|UnmatchedEventCount|Ja|Omatchade händelser|Antal|Totalt|Totalt antal händelser som inte matchar någon av händelse prenumerationerna för det här avsnittet|Inga dimensioner|


## <a name="microsofteventgridtopics"></a>Microsoft. EventGrid/ämnen

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|DeadLetteredCount|Ja|Obeställbara, Brevade händelser|Antal|Totalt|Totalt antal inloggade obeställbara meddelanden som matchar den här händelse prenumerationen|DeadLetterReason, EventSubscriptionName|
|DeliveryAttemptFailCount|Inga|Misslyckade leverans händelser|Antal|Totalt|Totalt antal händelser som inte levererades till den här händelse prenumerationen|Fel, ErrorType, EventSubscriptionName|
|DeliverySuccessCount|Ja|Levererade händelser|Antal|Totalt|Totalt antal händelser levererade till den här händelse prenumerationen|EventSubscriptionName|
|DestinationProcessingDurationInMs|Inga|Varaktighet för mål bearbetning|Millisekunder|Genomsnitt|Varaktighet för mål bearbetning i millisekunder|EventSubscriptionName|
|DroppedEventCount|Ja|Ignorerade händelser|Antal|Totalt|Totalt antal ignorerade händelser som matchar den här händelse prenumerationen|DropReason, EventSubscriptionName|
|MatchedEventCount|Ja|Matchade händelser|Antal|Totalt|Totalt antal händelser som matchar den här händelse prenumerationen|EventSubscriptionName|
|PublishFailCount|Ja|Publicera misslyckade händelser|Antal|Totalt|Totalt antal händelser som inte publicerats till det här avsnittet|ErrorType, fel|
|PublishSuccessCount|Ja|Publicerade händelser|Antal|Totalt|Totalt antal händelser som publicerats i det här avsnittet|Inga dimensioner|
|PublishSuccessLatencyInMs|Ja|Slutför svars tid för publicering|Millisekunder|Totalt|Publicera lyckad svars tid i millisekunder|Inga dimensioner|
|UnmatchedEventCount|Ja|Omatchade händelser|Antal|Totalt|Totalt antal händelser som inte matchar någon av händelse prenumerationerna för det här avsnittet|Inga dimensioner|


## <a name="microsofteventhubclusters"></a>Microsoft. EventHub/Clusters

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|ActiveConnections|Inga|ActiveConnections|Antal|Genomsnitt|Totalt antal aktiva anslutningar för Microsoft. EventHub.|Inga dimensioner|
|AvailableMemory|Inga|Tillgängligt minne|Procent|Maximal|Tillgängligt minne för Event Hub-klustret som en procent andel av det totala minnet.|Role|
|CaptureBacklog|Inga|Samla in efter släpning.|Antal|Totalt|Avbilda efter släpning för Microsoft. EventHub.|Inga dimensioner|
|CapturedBytes|Inga|Hämtade byte.|Byte|Totalt|Hämtade byte för Microsoft. EventHub.|Inga dimensioner|
|CapturedMessages|Inga|Fångade meddelanden.|Antal|Totalt|Fångade meddelanden för Microsoft. EventHub.|Inga dimensioner|
|ConnectionsClosed|Inga|Stängda anslutningar.|Antal|Genomsnitt|Stängda anslutningar för Microsoft. EventHub.|Inga dimensioner|
|ConnectionsOpened|Inga|Öppna anslutningar.|Antal|Genomsnitt|Anslutningar som har öppnats för Microsoft. EventHub.|Inga dimensioner|
|Processor|Inga|Processor|Procent|Maximal|CPU-användning för Event Hub-klustret i procent|Role|
|IncomingBytes|Ja|Inkommande byte.|Byte|Totalt|Inkommande byte för Microsoft. EventHub.|Inga dimensioner|
|IncomingMessages|Ja|Inkommande meddelanden|Antal|Totalt|Inkommande meddelanden för Microsoft. EventHub.|Inga dimensioner|
|IncomingRequests|Ja|Inkommande förfrågningar|Antal|Totalt|Inkommande begär Anden för Microsoft. EventHub.|Inga dimensioner|
|OutgoingBytes|Ja|Utgående byte.|Byte|Totalt|Utgående byte för Microsoft. EventHub.|Inga dimensioner|
|OutgoingMessages|Ja|Utgående meddelanden|Antal|Totalt|Utgående meddelanden för Microsoft. EventHub.|Inga dimensioner|
|QuotaExceededErrors|Inga|Fel på grund av överskriden kvot.|Antal|Totalt|Kvoten överskred fel för Microsoft. EventHub.|Inga dimensioner|
|ServerErrors|Inga|Serverfel.|Antal|Totalt|Server fel för Microsoft. EventHub.|Inga dimensioner|
|Storlek|Inga|Storlek|Byte|Genomsnitt|Storlek på en EventHub i byte.|Role|
|SuccessfulRequests|Inga|Slutförda förfrågningar|Antal|Totalt|Slutförda begär Anden för Microsoft. EventHub.|Inga dimensioner|
|ThrottledRequests|Inga|Begränsade förfrågningar.|Antal|Totalt|Begränsade begär Anden för Microsoft. EventHub.|Inga dimensioner|
|UserErrors|Inga|Användarfel.|Antal|Totalt|Användar fel för Microsoft. EventHub.|Inga dimensioner|


## <a name="microsofteventhubnamespaces"></a>Microsoft. EventHub/Namespaces

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|ActiveConnections|Inga|ActiveConnections|Antal|Genomsnitt|Totalt antal aktiva anslutningar för Microsoft. EventHub.|Inga dimensioner|
|CaptureBacklog|Inga|Samla in efter släpning.|Antal|Totalt|Avbilda efter släpning för Microsoft. EventHub.|Entitetsnamnet|
|CapturedBytes|Inga|Hämtade byte.|Byte|Totalt|Hämtade byte för Microsoft. EventHub.|Entitetsnamnet|
|CapturedMessages|Inga|Fångade meddelanden.|Antal|Totalt|Fångade meddelanden för Microsoft. EventHub.|Entitetsnamnet|
|ConnectionsClosed|Inga|Stängda anslutningar.|Antal|Genomsnitt|Stängda anslutningar för Microsoft. EventHub.|Entitetsnamnet|
|ConnectionsOpened|Inga|Öppna anslutningar.|Antal|Genomsnitt|Anslutningar som har öppnats för Microsoft. EventHub.|Entitetsnamnet|
|EHABL|Ja|Arkivera efter släpning meddelanden (inaktuellt)|Antal|Totalt|Event Hub arkivera meddelanden i efter släpning för en namnrymd (inaktuell)|Inga dimensioner|
|EHAMBS|Ja|Arkiv meddelande genom strömning (inaktuellt)|Byte|Totalt|Det arkiverade meddelande flödet i Event Hub i ett namn område (inaktuellt)|Inga dimensioner|
|EHAMSGS|Ja|Arkivera meddelanden (inaktuellt)|Antal|Totalt|Arkiverade meddelanden i Event Hub i ett namn område (inaktuellt)|Inga dimensioner|
|EHINBYTES|Ja|Inkommande byte (inaktuellt)|Byte|Totalt|Inkommande meddelande data flöde för Event Hub för en namnrymd (inaktuell)|Inga dimensioner|
|EHINMBS|Ja|Inkommande byte (inaktuell) (inaktuell)|Byte|Totalt|Inkommande meddelande data flöde för Event Hub för ett namn område. Måttet är föråldrat. Använd måttet inkommande byte i stället (inaktuellt)|Inga dimensioner|
|EHINMSGS|Ja|Inkommande meddelanden (inaktuellt)|Antal|Totalt|Totalt antal inkommande meddelanden för en namnrymd (inaktuell)|Inga dimensioner|
|EHOUTBYTES|Ja|Utgående byte (inaktuellt)|Byte|Totalt|Data flöde för utgående meddelande i Event Hub för en namnrymd (inaktuell)|Inga dimensioner|
|EHOUTMBS|Ja|Utgående byte (inaktuell) (inaktuell)|Byte|Totalt|Data flöde för utgående meddelande i Event Hub för en namnrymd. Måttet är föråldrat. Använd utgående byte-mått i stället (inaktuellt)|Inga dimensioner|
|EHOUTMSGS|Ja|Utgående meddelanden (inaktuellt)|Antal|Totalt|Totalt antal utgående meddelanden för en namnrymd (inaktuell)|Inga dimensioner|
|FAILREQ|Ja|Misslyckade förfrågningar (inaktuellt)|Antal|Totalt|Totalt antal misslyckade begär Anden för en namnrymd (inaktuell)|Inga dimensioner|
|IncomingBytes|Ja|Inkommande byte.|Byte|Totalt|Inkommande byte för Microsoft. EventHub.|Entitetsnamnet|
|IncomingMessages|Ja|Inkommande meddelanden|Antal|Totalt|Inkommande meddelanden för Microsoft. EventHub.|Entitetsnamnet|
|IncomingRequests|Ja|Inkommande förfrågningar|Antal|Totalt|Inkommande begär Anden för Microsoft. EventHub.|Entitetsnamnet|
|INMSGS|Ja|Inkommande meddelanden (inaktuella)|Antal|Totalt|Totalt antal inkommande meddelanden för ett namn område. Måttet är föråldrat. Använd måtten inkommande e-postmeddelanden i stället (inaktuellt)|Inga dimensioner|
|INREQS|Ja|Inkommande begär Anden (inaktuellt)|Antal|Totalt|Totalt antal inkommande sändnings begär Anden för en namnrymd (inaktuell)|Inga dimensioner|
|MELLAN|Ja|Interna Server fel (inaktuellt)|Antal|Totalt|Totalt antal interna Server fel för en namnrymd (inaktuell)|Inga dimensioner|
|MISCERR|Ja|Andra fel (inaktuellt)|Antal|Totalt|Totalt antal misslyckade begär Anden för en namnrymd (inaktuell)|Inga dimensioner|
|OutgoingBytes|Ja|Utgående byte.|Byte|Totalt|Utgående byte för Microsoft. EventHub.|Entitetsnamnet|
|OutgoingMessages|Ja|Utgående meddelanden|Antal|Totalt|Utgående meddelanden för Microsoft. EventHub.|Entitetsnamnet|
|OUTMSGS|Ja|Utgående meddelanden (inaktuella)|Antal|Totalt|Totalt antal utgående meddelanden för ett namn område. Måttet är föråldrat. Använd utgående meddelande mått i stället (inaktuellt)|Inga dimensioner|
|QuotaExceededErrors|Inga|Fel på grund av överskriden kvot.|Antal|Totalt|Kvoten överskred fel för Microsoft. EventHub.|Entitetsnamnet |
|ServerErrors|Inga|Serverfel.|Antal|Totalt|Server fel för Microsoft. EventHub.|Entitetsnamnet |
|Storlek|Inga|Storlek|Byte|Genomsnitt|Storlek på en EventHub i byte.|Entitetsnamnet|
|SuccessfulRequests|Inga|Slutförda förfrågningar|Antal|Totalt|Slutförda begär Anden för Microsoft. EventHub.|Entitetsnamnet |
|SUCCREQ|Ja|Lyckade förfrågningar (inaktuellt)|Antal|Totalt|Totalt antal slutförda begär Anden för en namnrymd (inaktuell)|Inga dimensioner|
|SVRBSY|Ja|Serverns upptaget fel (inaktuellt)|Antal|Totalt|Totalt antal upptagen server-fel för en namnrymd (inaktuell)|Inga dimensioner|
|ThrottledRequests|Inga|Begränsade förfrågningar.|Antal|Totalt|Begränsade begär Anden för Microsoft. EventHub.|Entitetsnamnet |
|UserErrors|Inga|Användarfel.|Antal|Totalt|Användar fel för Microsoft. EventHub.|Entitetsnamnet |


## <a name="microsofthdinsightclusters"></a>Microsoft. HDInsight/kluster

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|CategorizedGatewayRequests|Ja|Kategoriserade Gateway-begäranden|Antal|Totalt|Antal Gateway-begäranden per kategori (1xx/2xx/3xx/4xx/5xx)|HttpStatus|
|GatewayRequests|Ja|Gateway-begäranden|Antal|Totalt|Antal Gateway-begäranden|HttpStatus|
|NumActiveWorkers|Ja|Antal aktiva arbetare|Antal|Maximal|Antal aktiva arbetare|MetricName|


## <a name="microsoftinsightsautoscalesettings"></a>Microsoft. Insights/AutoscaleSettings

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|MetricThreshold|Ja|Mätnings tröskel|Antal|Genomsnitt|Det konfigurerade tröskelvärdet för autoskalning vid autoskalning kördes.|MetricTriggerRule|
|ObservedCapacity|Ja|Observerad kapacitet|Antal|Genomsnitt|Kapaciteten som rapporteras till autoskalning när den kördes.|Inga dimensioner|
|ObservedMetricValue|Ja|Observerat mått värde|Antal|Genomsnitt|Det värde som beräknas genom autoskalning vid körning|MetricTriggerSource|
|ScaleActionsInitiated|Ja|Initierade skalnings åtgärder|Antal|Totalt|Riktningen för skalnings åtgärden.|ScaleDirection|


## <a name="microsoftinsightscomponents"></a>Microsoft. Insights/komponenter

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|availabilityResults/availabilityPercentage|Ja|Tillgänglighet|Procent|Genomsnitt|Procent slutförda tillgänglighets test har slutförts|availabilityResult/namn, availabilityResult/plats|
|availabilityResults/antal|Inga|Tillgänglighetstester|Antal|Antal|Antal tillgänglighets test|availabilityResult/namn, availabilityResult/plats, availabilityResult/lyckad|
|availabilityResults/varaktighet|Ja|Tillgänglighets testets varaktighet|Millisekunder|Genomsnitt|Tillgänglighets testets varaktighet|availabilityResult/namn, availabilityResult/plats, availabilityResult/lyckad|
|browserTimings/networkDuration|Ja|Nätverks anslutnings tid för sid inläsning|Millisekunder|Genomsnitt|Tid mellan användar förfrågan och nätverks anslutning. Inkluderar DNS-sökning och transport anslutning.|Inga dimensioner|
|browserTimings/processingDuration|Ja|Klient bearbetnings tid|Millisekunder|Genomsnitt|Tiden mellan att ta emot sista byten i ett dokument tills DOM har lästs in. Asynkrona begär Anden kan fortfarande bearbetas.|Inga dimensioner|
|browserTimings/receiveDuration|Ja|Tar emot svars tid|Millisekunder|Genomsnitt|Tiden mellan den första och sista byten, eller till från koppling.|Inga dimensioner|
|browserTimings/sendDuration|Ja|Tid för att skicka begäran|Millisekunder|Genomsnitt|Tiden mellan nätverks anslutning och mottagande av den första byten.|Inga dimensioner|
|browserTimings/totalDuration|Ja|Sid inläsnings tid för webbläsare|Millisekunder|Genomsnitt|Tid från användar förfrågan tills DOM, formatmallar, skript och bilder har lästs in.|Inga dimensioner|
|beroenden/antal|Inga|Beroende anrop|Antal|Antal|Antal anrop gjorda av programmet till externa resurser.|beroende/typ, beroende/performanceBucket, beroende/lyckades, beroende/Target, beroende/resultCode, åtgärd/syntetisk, Cloud/roleInstance, Cloud/roleName|
|beroenden/varaktighet|Ja|Beroende varaktighet|Millisekunder|Genomsnitt|Varaktigheten för anrop gjorda av programmet till externa resurser.|beroende/typ, beroende/performanceBucket, beroende/lyckades, beroende/Target, beroende/resultCode, åtgärd/syntetisk, Cloud/roleInstance, Cloud/roleName|
|beroenden/misslyckades|Inga|Beroende anrops problem|Antal|Antal|Antal misslyckade beroende anrop gjorda av programmet till externa resurser.|beroende/typ, beroende/performanceBucket, beroende/mål, beroende/resultCode, åtgärd/syntetisk, Cloud/roleInstance, Cloud/roleName|
|undantag/webbläsare|Inga|Webbläsarundantag|Antal|Antal|Antal ej fångade undantag som har utlösts i webbläsaren.|Cloud/roleName|
|undantag/antal|Ja|Undantag|Antal|Antal|Sammanlagt antal undantag som inte har fångats.|Cloud/roleName, Cloud/roleInstance, Client/Type|
|undantag/Server|Inga|Server undantag|Antal|Antal|Antal ej fångade undantag som har utlösts i serverprogrammet.|Cloud/roleName, Cloud/roleInstance|
|pageViews/antal|Ja|Sid visningar|Antal|Antal|Antal sid visningar.|drift/syntetisk, Cloud/roleName|
|pageViews/varaktighet|Ja|Inläsnings tid för sid visning|Millisekunder|Genomsnitt|Inläsnings tid för sid visning|drift/syntetisk, Cloud/roleName|
|performanceCounters/exceptionsPerSecond|Ja|Undantags frekvens|CountPerSecond|Genomsnitt|Antal hanterade och ohanterade undantag som rapporter ATS till Windows, inklusive .NET-undantag och ohanterade undantag som konverterats till .NET-undantag.|Cloud/roleInstance|
|performanceCounters/memoryAvailableBytes|Ja|Tillgängligt minne|Byte|Genomsnitt|Fysiskt minne som är omedelbart tillgängligt för allokering till en process eller för system användning.|Cloud/roleInstance|
|performanceCounters/processCpuPercentage|Ja|Processor-CPU|Procent|Genomsnitt|Procent andelen av förfluten tid som alla process trådar använde processorn för att köra instruktioner. Detta kan variera mellan 0 och 100. Det här måttet anger enbart prestanda för W3wp-processen.|Cloud/roleInstance|
|performanceCounters/processIOBytesPerSecond|Ja|Process-IO-hastighet|BytesPerSecond|Genomsnitt|Totalt antal byte per sekund som har lästs och skrivits till filer, nätverk och enheter.|Cloud/roleInstance|
|performanceCounters/processorCpuPercentage|Ja|Processor tid|Procent|Genomsnitt|Den procent andel av tiden som processorn ägnat åt icke-inaktiva trådar.|Cloud/roleInstance|
|performanceCounters/processPrivateBytes|Ja|Privata byte för process|Byte|Genomsnitt|Minne som tilldelats exklusivt för de övervakade program processerna.|Cloud/roleInstance|
|performanceCounters/requestExecutionTime|Ja|Körnings tid för HTTP-begäran|Millisekunder|Genomsnitt|Körnings tid för den senaste begäran.|Cloud/roleInstance|
|performanceCounters/requestsInQueue|Ja|HTTP-begäranden i program kön|Antal|Genomsnitt|Längden på program begär ande kön.|Cloud/roleInstance|
|performanceCounters/requestsPerSecond|Ja|Hastighet för HTTP-begäran|CountPerSecond|Genomsnitt|Takten för alla förfrågningar till programmet per sekund från ASP.NET.|Cloud/roleInstance|
|begär Anden/antal|Inga|Server begär Anden|Antal|Antal|Antal slutförda HTTP-förfrågningar.|Request/performanceBucket, Request/resultCode, operation/syntetisk, Cloud/roleInstance, Request/lyckades, Cloud/roleName|
|begär Anden/varaktighet|Ja|Server svars tid|Millisekunder|Genomsnitt|Tiden mellan att ta emot en HTTP-förfrågan och avsluta sändningen av svaret.|Request/performanceBucket, Request/resultCode, operation/syntetisk, Cloud/roleInstance, Request/lyckades, Cloud/roleName|
|begär Anden/misslyckade|Inga|Misslyckade förfrågningar|Antal|Antal|Antal HTTP-begäranden som marker ATS som misslyckade. I de flesta fall är dessa förfrågningar med svars kod >= 400 och inte lika med 401.|Request/performanceBucket, Request/resultCode, operation/syntetisk, Cloud/roleInstance, Cloud/roleName|
|begär Anden/pris|Inga|Server begär ande frekvens|CountPerSecond|Genomsnitt|Antal server begär Anden per sekund|Request/performanceBucket, Request/resultCode, operation/syntetisk, Cloud/roleInstance, Request/lyckades, Cloud/roleName|
|spårning/antal|Ja|Spårningar|Antal|Antal|Spårnings dokument antal|Trace/severityLevel, operation/syntetisk, Cloud/roleName, Cloud/roleInstance|


## <a name="microsoftiotcentraliotapps"></a>Microsoft. IoTCentral/IoTApps

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|C2D. Property. Read. Failure|Ja|Det gick inte att läsa enhets egenskapen från IoT Central|Antal|Totalt|Antal misslyckade egenskaps läsningar som initierats från IoT Central|Inga dimensioner|
|C2D. Property. Read. lyckades|Ja|Lyckad enhets egenskaps läsning från IoT Central|Antal|Totalt|Antalet lyckade egenskaps läsningar som initierats från IoT Central|Inga dimensioner|
|C2D. Property. Update. Failure|Ja|Det gick inte att uppdatera enhets egenskapen från IoT Central|Antal|Totalt|Antalet misslyckade egenskaps uppdateringar som initierats från IoT Central|Inga dimensioner|
|C2D. Property. Update. lyckades|Ja|Lyckade uppdateringar av enhets egenskapen från IoT Central|Antal|Totalt|Antalet lyckade egenskaps uppdateringar som initierats från IoT Central|Inga dimensioner|
|connectedDeviceCount|Inga|Totalt antal anslutna enheter|Antal|Genomsnitt|Antal enheter som är anslutna till IoT Central|Inga dimensioner|
|D2C. Property. Read. Failure|Ja|Det gick inte att läsa enhets egenskapen från enheterna|Antal|Totalt|Antalet misslyckade egenskaps läsningar som initierats från enheter|Inga dimensioner|
|D2C. Property. Read. lyckades|Ja|Lyckad enhets egenskap läser från enheter|Antal|Totalt|Antalet lyckade egenskaps läsningar som initierats från enheter|Inga dimensioner|
|D2C. Property. Update. Failure|Ja|Misslyckade uppdateringar av enhets egenskapen från enheter|Antal|Totalt|Antalet misslyckade egenskaps uppdateringar som initierats från enheter|Inga dimensioner|
|D2C. Property. Update. lyckades|Ja|Lyckade uppdateringar av enhets egenskapen från enheter|Antal|Totalt|Antalet lyckade egenskaps uppdateringar som initierats från enheter|Inga dimensioner|


## <a name="microsoftkeyvaultvaults"></a>Microsoft. nyckel valv/-valv

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Ja|Övergripande valv tillgänglighet|Procent|Genomsnitt|Tillgänglighet för valv begär Anden|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|SaturationShoebox|Inga|Övergripande valv beläggning|Procent|Genomsnitt|Valv kapacitet som används|ActivityType, ActivityName, TransactionType|
|ServiceApiHit|Ja|Totalt antal tjänst-API-träffar|Antal|Antal|Antal totala service API-träffar|ActivityType, ActivityName|
|ServiceApiLatency|Ja|Övergripande service API-latens|Millisekunder|Genomsnitt|Övergripande svars tid för service API-begäranden|ActivityType, ActivityName, StatusCode, StatusCodeClass|
|ServiceApiResult|Ja|Totalt antal service API-resultat|Antal|Antal|Antal totala service API-resultat|ActivityType, ActivityName, StatusCode, StatusCodeClass|


## <a name="microsoftkustoclusters"></a>Microsoft. Kusto/kluster

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|BatchBlobCount|Ja|Batch-BLOB-antal|Antal|Genomsnitt|Antalet data källor i en aggregerad batch för inmatning.|Databas|
|BatchDuration|Ja|Batch-varaktighet|Sekunder|Genomsnitt|Varaktigheten för agg regerings fasen i inmatnings flödet.|Databas|
|BatchesProcessed|Ja|Bearbetade batchar|Antal|Genomsnitt|Antal sammanställda batchar för inmatning. Typ av batch: anger om batchen har nått sin gräns för batchbearbetning, data storlek eller antal filer som angetts av batch-principen|Databas, SealReason|
|BatchSize|Ja|Batchstorlek|Byte|Genomsnitt|Okomprimerad förväntad data storlek i en aggregerad batch för inmatning.|Databas|
|BlobsProcessed|Ja|Bearbetade blobbar|Antal|Genomsnitt|Antal blobbar som bearbetats av en komponent.|Databas, ComponentType, ComponentName|
|BlobsReceived|Ja|Mottagna blobbar|Antal|Genomsnitt|Antal blobbar som tagits emot från indataströmmen med en komponent.|Databas, ComponentType, ComponentName|
|BlobsRejected|Ja|Avvisade blobbar|Antal|Genomsnitt|Antalet blobbar som har avvisats permanent av en komponent.|Databas, ComponentType, ComponentName|
|CacheUtilization|Ja|Användning av cache|Procent|Genomsnitt|Användnings nivå i kluster omfånget|Inga dimensioner|
|ContinuousExportMaxLatenessMinutes|Ja|Kontinuerlig export, maximal försening|Antal|Maximal|Försenad (i minuter) som rapporteras av de kontinuerliga export jobben i klustret|Inga dimensioner|
|ContinuousExportNumOfRecordsExported|Ja|Kontinuerlig export – antal exporterade poster|Antal|Totalt|Antal exporterade poster, utlöst för varje lagrings artefakt som skrivits under export åtgärden|ContinuousExportName, databas|
|ContinuousExportPendingCount|Ja|Antal väntande pågående export|Antal|Maximal|Antal väntande kontinuerliga export jobb som är klara för körning|Inga dimensioner|
|ContinuousExportResult|Ja|Resultat av kontinuerlig export|Antal|Antal|Anger om kontinuerlig export lyckades eller misslyckades|ContinuousExportName, resultat, databas|
|Processor|Ja|Processor|Procent|Genomsnitt|PROCESSOR användnings nivå|Inga dimensioner|
|CumulativeLatency|Ja|Ackumulerad latens|Sekunder|Genomsnitt|Ackumulerad tid från när ett meddelande identifieras tills det tas emot av rapporterings komponenten för bearbetning (identifierings tiden anges när ett meddelande har placerats i kö för inmatnings kön eller när det upptäcks av data anslutningen).|Databas, ComponentType|
|DiscoveryLatency|Ja|Identifierings svars tid|Sekunder|Genomsnitt|Rapporteras av data anslutningar (om de finns). Tiden i sekunder från när ett meddelande är i kö eller om en händelse skapas tills den identifieras av data anslutningen. Den här tiden ingår inte i Azure Datautforskaren total inläsnings varaktighet.|ComponentType, ComponentName|
|EventsProcessedForEventHubs|Ja|Bearbetade händelser (för Event/IoT-hubbar)|Antal|Totalt|Antal händelser som bearbetas av klustret vid inmatning från händelse/IoT Hub|EventStatus|
|ExportUtilization|Ja|Exportanvändning|Procent|Maximal|Exportera användning|Inga dimensioner|
|IngestionLatencyInSeconds|Ja|Inmatnings svars tid|Sekunder|Genomsnitt|Hämtnings tiden från källan (t. ex. meddelandet är i EventHub) till klustret på några sekunder|Inga dimensioner|
|IngestionResult|Ja|Inmatnings resultat|Antal|Antal|Antal inmatnings åtgärder|IngestionResultDetails|
|IngestionUtilization|Ja|Datainmatningsanvändning|Procent|Genomsnitt|Förhållandet mellan använda inmatnings platser i klustret|Inga dimensioner|
|IngestionVolumeInMB|Ja|Inmatnings volym (i MB)|Byte|Totalt|Total mängd inmatade data till klustret (i MB)|Inga dimensioner|
|InstanceCount|Ja|Antal instanser|Antal|Genomsnitt|Totalt antal instanser|Inga dimensioner|
|KeepAlive|Ja|Behåll Alive|Antal|Genomsnitt|Sanity check anger att klustret svarar på frågor|Inga dimensioner|
|MaterializedViewAgeMinutes|Ja|Materialiserad vy-ålder|Antal|Genomsnitt|Den materialiserade vyns ålder på några minuter|Databas, MaterializedViewName|
|MaterializedViewDataLoss|Ja|Materialiserad visnings data förlust|Antal|Maximal|Indikerar potentiell data förlust i materialiserad vy|Databas, MaterializedViewName, sort|
|MaterializedViewExtentsRebuild|Ja|Återuppbyggnad av materialiserade visnings omfång|Antal|Genomsnitt|Återskapa antalet omfattningar|Databas, MaterializedViewName|
|MaterializedViewHealth|Ja|Materialiserad visnings hälsa|Antal|Genomsnitt|Hälso tillståndet för den materialiserade vyn (1 för felfri, 0 för icke-felfri)|Databas, MaterializedViewName|
|MaterializedViewRecordsInDelta|Ja|Materialiserade visnings poster i delta|Antal|Genomsnitt|Antalet poster i den icke-materialiserade delen av vyn|Databas, MaterializedViewName|
|MaterializedViewResult|Ja|Resultat av materialiserad vy|Antal|Genomsnitt|Resultatet av processen materialization|Databas, MaterializedViewName, resultat|
|QueryDuration|Ja|Frågans varaktighet|Millisekunder|Genomsnitt|Frågornas varaktighet i sekunder|QueryStatus|
|QueryResult|Inga|Frågeresultat|Antal|Antal|Totalt antal frågor.|Status|
|SteamingIngestRequestRate|Ja|Förfrågningsfrekvens för strömningsinmatning|Antal|RateRequestsPerSecond|Hastighet för strömnings inmatnings begäran (begär Anden per sekund)|Inga dimensioner|
|StreamingIngestDataRate|Ja|Datahastighet för strömningsinmatning|Antal|Genomsnitt|Strömnings data hastighet (MB per sekund)|Inga dimensioner|
|StreamingIngestDuration|Ja|Varaktighet för strömningsinmatning|Millisekunder|Genomsnitt|Hämtnings tid för strömning i millisekunder|Inga dimensioner|
|StreamingIngestResults|Ja|Resultat för strömningsinmatning|Antal|Genomsnitt|Resultat av strömnings inmatning|Resultat|
|TotalNumberOfConcurrentQueries|Ja|Totalt antal samtidiga frågor|Antal|Totalt|Totalt antal samtidiga frågor|Inga dimensioner|
|TotalNumberOfExtents|Ja|Totalt antal omfattningar|Antal|Totalt|Totalt antal data omfattningar|Inga dimensioner|
|TotalNumberOfThrottledCommands|Ja|Totalt antal begränsade kommandon|Antal|Totalt|Totalt antal begränsade kommandon|CommandType|
|TotalNumberOfThrottledQueries|Ja|Totalt antal begränsade frågor|Antal|Totalt|Totalt antal begränsade frågor|Inga dimensioner|


## <a name="microsoftlogicintegrationserviceenvironments"></a>Microsoft. Logic/integrationServiceEnvironments

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|ActionLatency|Ja|Åtgärds svars tid |Sekunder|Genomsnitt|Svars tid för slutförda arbets flödes åtgärder.|Inga dimensioner|
|ActionsCompleted|Ja|Slutförda åtgärder |Antal|Totalt|Antal slutförda arbets flödes åtgärder.|Inga dimensioner|
|ActionsFailed|Ja|Misslyckade åtgärder |Antal|Totalt|Antalet misslyckade arbets flödes åtgärder.|Inga dimensioner|
|ActionsSkipped|Ja|Åtgärder hoppades över |Antal|Totalt|Antal överhoppade arbets flödes åtgärder.|Inga dimensioner|
|ActionsStarted|Ja|Startade åtgärder |Antal|Totalt|Antal startade arbets flödes åtgärder.|Inga dimensioner|
|ActionsSucceeded|Ja|Genomförda åtgärder |Antal|Totalt|Antal slutförda arbets flödes åtgärder.|Inga dimensioner|
|ActionSuccessLatency|Ja|Svars tid för åtgärd |Sekunder|Genomsnitt|Svars tid för slutförda arbets flödes åtgärder.|Inga dimensioner|
|ActionThrottledEvents|Ja|Åtgärds begränsade händelser|Antal|Totalt|Antal begränsade händelser för arbets flödes åtgärd..|Inga dimensioner|
|IntegrationServiceEnvironmentConnectorMemoryUsage|Ja|Anslutnings minnes användning för Integration Service Environment|Procent|Genomsnitt|Anslutnings minnes användning för integrerings tjänst miljön.|Inga dimensioner|
|IntegrationServiceEnvironmentConnectorProcessorUsage|Ja|Anslutnings processor användning för Integration Service Environment|Procent|Genomsnitt|Anslutnings processor användning för integrerings tjänst miljön.|Inga dimensioner|
|IntegrationServiceEnvironmentWorkflowMemoryUsage|Ja|Minnes användning för arbets flöde för Integration Service Environment|Procent|Genomsnitt|Arbets flödets minnes användning för integrerings tjänst miljön.|Inga dimensioner|
|IntegrationServiceEnvironmentWorkflowProcessorUsage|Ja|Användning av arbets flödes processor för Integration Service Environment|Procent|Genomsnitt|Användning av arbets flödes processor för integrerings tjänst miljö.|Inga dimensioner|
|RunFailurePercentage|Ja|Procent körnings avbrott|Procent|Totalt|Procent andel av arbets flödes körningen misslyckades.|Inga dimensioner|
|RunLatency|Ja|Körnings fördröjning|Sekunder|Genomsnitt|Svars tiden för slutförda arbets flödes körningar.|Inga dimensioner|
|RunsCancelled|Ja|Avbrutna körningar|Antal|Totalt|Antal avbrutna arbets flödes körningar.|Inga dimensioner|
|RunsCompleted|Ja|Slutförda körningar|Antal|Totalt|Antal slutförda arbets flödes körningar.|Inga dimensioner|
|RunsFailed|Ja|Misslyckade körningar|Antal|Totalt|Antal misslyckade arbets flödes körningar.|Inga dimensioner|
|RunsStarted|Ja|Startade körningar|Antal|Totalt|Antal startade arbets flödes körningar.|Inga dimensioner|
|RunsSucceeded|Ja|Lyckade körningar|Antal|Totalt|Antal slutförda arbets flödes körningar.|Inga dimensioner|
|RunStartThrottledEvents|Ja|Kör starta begränsade händelser|Antal|Totalt|Antal händelser som startar begränsning av arbets flödes körning.|Inga dimensioner|
|RunSuccessLatency|Ja|Kör svars tid|Sekunder|Genomsnitt|Svars tiden för slutfört arbets flöde körs.|Inga dimensioner|
|RunThrottledEvents|Ja|Kör begränsade händelser|Antal|Totalt|Antal arbets flödes åtgärder eller utlösa begränsade händelser.|Inga dimensioner|
|TriggerFireLatency|Ja|Utlös brand fördröjning |Sekunder|Genomsnitt|Fördröjning för utlösta arbets flödes utlösare.|Inga dimensioner|
|TriggerLatency|Ja|Utlös fördröjning |Sekunder|Genomsnitt|Latens för slutförda arbets flödes utlösare.|Inga dimensioner|
|TriggersCompleted|Ja|Slutförda utlösare |Antal|Totalt|Antal slutförda arbets flödes utlösare.|Inga dimensioner|
|TriggersFailed|Ja|Misslyckade utlösare |Antal|Totalt|Antalet misslyckade arbets flödes utlösare.|Inga dimensioner|
|TriggersFired|Ja|Utlöst utlösare |Antal|Totalt|Antal utlösta arbets flödes utlösare.|Inga dimensioner|
|TriggersSkipped|Ja|Ignorerade utlösare|Antal|Totalt|Antal överhoppade arbets flödes utlösare.|Inga dimensioner|
|TriggersStarted|Ja|Startade utlösare |Antal|Totalt|Antal startade arbets flödes utlösare.|Inga dimensioner|
|TriggersSucceeded|Ja|Lyckade utlösare |Antal|Totalt|Antal slutförda arbets flödes utlösare.|Inga dimensioner|
|TriggerSuccessLatency|Ja|Utlös svars tid för lyckade |Sekunder|Genomsnitt|Svars tid för lyckade arbets flödes utlösare.|Inga dimensioner|
|TriggerThrottledEvents|Ja|Utlös begränsade händelser|Antal|Totalt|Antal begränsade händelser för arbets flödes utlösare.|Inga dimensioner|


## <a name="microsoftlogicworkflows"></a>Microsoft. Logic/arbets flöden

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|ActionLatency|Ja|Åtgärds svars tid |Sekunder|Genomsnitt|Svars tid för slutförda arbets flödes åtgärder.|Inga dimensioner|
|ActionsCompleted|Ja|Slutförda åtgärder |Antal|Totalt|Antal slutförda arbets flödes åtgärder.|Inga dimensioner|
|ActionsFailed|Ja|Misslyckade åtgärder |Antal|Totalt|Antalet misslyckade arbets flödes åtgärder.|Inga dimensioner|
|ActionsSkipped|Ja|Åtgärder hoppades över |Antal|Totalt|Antal överhoppade arbets flödes åtgärder.|Inga dimensioner|
|ActionsStarted|Ja|Startade åtgärder |Antal|Totalt|Antal startade arbets flödes åtgärder.|Inga dimensioner|
|ActionsSucceeded|Ja|Genomförda åtgärder |Antal|Totalt|Antal slutförda arbets flödes åtgärder.|Inga dimensioner|
|ActionSuccessLatency|Ja|Svars tid för åtgärd |Sekunder|Genomsnitt|Svars tid för slutförda arbets flödes åtgärder.|Inga dimensioner|
|ActionThrottledEvents|Ja|Åtgärds begränsade händelser|Antal|Totalt|Antal begränsade händelser för arbets flödes åtgärd..|Inga dimensioner|
|BillableActionExecutions|Ja|Fakturerbara åtgärds körningar|Antal|Totalt|Antal körningar av arbets flödes åtgärder som faktureras.|Inga dimensioner|
|BillableTriggerExecutions|Ja|Fakturerbara Utlösar-körningar|Antal|Totalt|Antal körningar av arbets flödes utlösare som faktureras.|Inga dimensioner|
|BillingUsageNativeOperation|Ja|Fakturerings användning för intern åtgärds körningar|Antal|Totalt|Antal körningar av interna åtgärder som debiteras.|Inga dimensioner|
|BillingUsageStandardConnector|Ja|Fakturerings användning för standard kopplings körningar|Antal|Totalt|Antal standard kopplings körningar som faktureras.|Inga dimensioner|
|BillingUsageStorageConsumption|Ja|Fakturerings användning för lagrings förbruknings körningar|Antal|Totalt|Antal lagrings förbruknings körningar som faktureras.|Inga dimensioner|
|RunFailurePercentage|Ja|Procent körnings avbrott|Procent|Totalt|Procent andel av arbets flödes körningen misslyckades.|Inga dimensioner|
|RunLatency|Ja|Körnings fördröjning|Sekunder|Genomsnitt|Svars tiden för slutförda arbets flödes körningar.|Inga dimensioner|
|RunsCancelled|Ja|Avbrutna körningar|Antal|Totalt|Antal avbrutna arbets flödes körningar.|Inga dimensioner|
|RunsCompleted|Ja|Slutförda körningar|Antal|Totalt|Antal slutförda arbets flödes körningar.|Inga dimensioner|
|RunsFailed|Ja|Misslyckade körningar|Antal|Totalt|Antal misslyckade arbets flödes körningar.|Inga dimensioner|
|RunsStarted|Ja|Startade körningar|Antal|Totalt|Antal startade arbets flödes körningar.|Inga dimensioner|
|RunsSucceeded|Ja|Lyckade körningar|Antal|Totalt|Antal slutförda arbets flödes körningar.|Inga dimensioner|
|RunStartThrottledEvents|Ja|Kör starta begränsade händelser|Antal|Totalt|Antal händelser som startar begränsning av arbets flödes körning.|Inga dimensioner|
|RunSuccessLatency|Ja|Kör svars tid|Sekunder|Genomsnitt|Svars tiden för slutfört arbets flöde körs.|Inga dimensioner|
|RunThrottledEvents|Ja|Kör begränsade händelser|Antal|Totalt|Antal arbets flödes åtgärder eller utlösa begränsade händelser.|Inga dimensioner|
|TotalBillableExecutions|Ja|Totalt antal fakturerbara körningar|Antal|Totalt|Antal arbets flödes körningar som faktureras.|Inga dimensioner|
|TriggerFireLatency|Ja|Utlös brand fördröjning |Sekunder|Genomsnitt|Fördröjning för utlösta arbets flödes utlösare.|Inga dimensioner|
|TriggerLatency|Ja|Utlös fördröjning |Sekunder|Genomsnitt|Latens för slutförda arbets flödes utlösare.|Inga dimensioner|
|TriggersCompleted|Ja|Slutförda utlösare |Antal|Totalt|Antal slutförda arbets flödes utlösare.|Inga dimensioner|
|TriggersFailed|Ja|Misslyckade utlösare |Antal|Totalt|Antalet misslyckade arbets flödes utlösare.|Inga dimensioner|
|TriggersFired|Ja|Utlöst utlösare |Antal|Totalt|Antal utlösta arbets flödes utlösare.|Inga dimensioner|
|TriggersSkipped|Ja|Ignorerade utlösare|Antal|Totalt|Antal överhoppade arbets flödes utlösare.|Inga dimensioner|
|TriggersStarted|Ja|Startade utlösare |Antal|Totalt|Antal startade arbets flödes utlösare.|Inga dimensioner|
|TriggersSucceeded|Ja|Lyckade utlösare |Antal|Totalt|Antal slutförda arbets flödes utlösare.|Inga dimensioner|
|TriggerSuccessLatency|Ja|Utlös svars tid för lyckade |Sekunder|Genomsnitt|Svars tid för lyckade arbets flödes utlösare.|Inga dimensioner|
|TriggerThrottledEvents|Ja|Utlös begränsade händelser|Antal|Totalt|Antal begränsade händelser för arbets flödes utlösare.|Inga dimensioner|


## <a name="microsoftmachinelearningservicesworkspaces"></a>Microsoft. MachineLearningServices/arbets ytor

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Aktiva kärnor|Ja|Aktiva kärnor|Antal|Genomsnitt|Antal aktiva kärnor|Scenario, kluster namn|
|Aktiva noder|Ja|Aktiva noder|Antal|Genomsnitt|Antal Active-noder. Detta är de noder som aktivt kör ett jobb.|Scenario, kluster namn|
|Avbryt begärda körningar|Ja|Avbryt begärda körningar|Antal|Totalt|Antal körningar där Cancel begärdes för den här arbets ytan. Antalet uppdateras när en avbrottsbegäran tas emot för en körning.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Avbrutna körningar|Ja|Avbrutna körningar|Antal|Totalt|Antalet körningar som avbrutits för den här arbets ytan. Antalet uppdateras när en körning har avbrutits.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Slutförda körningar|Ja|Slutförda körningar|Antal|Totalt|Antalet körningar har slutförts för den här arbets ytan. Antalet uppdateras när en körning har slutförts och utdata har samlats in.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|CpuUtilization|Ja|CpuUtilization|Antal|Genomsnitt|Procent andel användning på en processor nod. Användningen rapporteras med ett minuters intervall.|Scenario, runId, NodeId, kluster namn|
|Fel|Ja|Fel|Antal|Totalt|Antal körnings fel i den här arbets ytan. Antalet uppdateras när körningen påträffar ett fel.|Scenario|
|Misslyckade körningar|Ja|Misslyckade körningar|Antal|Totalt|Antalet körningar som misslyckades för den här arbets ytan. Antalet uppdateras när en körning Miss lyckas.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Slutför körningar|Ja|Slutför körningar|Antal|Totalt|Antal körningar som har angetts i slutfört tillstånd för den här arbets ytan. Antalet uppdateras när en körning har slutförts men insamlingen fortfarande pågår.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|GpuUtilization|Ja|GpuUtilization|Antal|Genomsnitt|Procent andel användning på en GPU-nod. Användningen rapporteras med ett minuters intervall.|Scenario, runId, NodeId, DeviceId, kluster namn|
|Inaktiva kärnor|Ja|Inaktiva kärnor|Antal|Genomsnitt|Antal inaktiva kärnor|Scenario, kluster namn|
|Inaktiva noder|Ja|Inaktiva noder|Antal|Genomsnitt|Antal inaktiva noder. Inaktiva noder är noder som inte kör några jobb, men som kan ta emot nya jobb om de är tillgängliga.|Scenario, kluster namn|
|Lämnar kärnor|Ja|Lämnar kärnor|Antal|Genomsnitt|Antal lämnar kärnor|Scenario, kluster namn|
|Lämnar noder|Ja|Lämnar noder|Antal|Genomsnitt|Antal noder som lämnar. Att lämna noder är de noder som precis har avslutat bearbetningen av ett jobb och kommer att gå in i inaktivt läge.|Scenario, kluster namn|
|Modelldistribution misslyckades|Ja|Modelldistribution misslyckades|Antal|Totalt|Antal modell distributioner som misslyckades på den här arbets ytan|Scenario, StatusCode|
|Modelldistribution Startad|Ja|Modelldistribution Startad|Antal|Totalt|Antal modell distributioner som startats på den här arbets ytan|Scenario|
|Modelldistribution lyckades|Ja|Modelldistribution lyckades|Antal|Totalt|Antal modell distributioner som har slutförts på den här arbets ytan|Scenario|
|Modell registreringen misslyckades|Ja|Modell registreringen misslyckades|Antal|Totalt|Antal modell registreringar som misslyckades på den här arbets ytan|Scenario, StatusCode|
|Modell registreringen har slutförts|Ja|Modell registreringen har slutförts|Antal|Totalt|Antal modell registreringar som har slutförts på den här arbets ytan|Scenario|
|Svarar inte körningar|Ja|Svarar inte körningar|Antal|Totalt|Antal körningar som inte svarar på den här arbets ytan. Antalet uppdateras när en körning inte övergår i tillstånd.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Inte startade körningar|Ja|Inte startade körningar|Antal|Totalt|Antal körningar i inte start läge för den här arbets ytan. Antalet uppdateras när en begäran tas emot för att skapa en körning men körnings information ännu inte har fyllts i. |Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Blockerade kärnor|Ja|Blockerade kärnor|Antal|Genomsnitt|Antal blockerade kärnor|Scenario, kluster namn|
|Misslyckade noder|Ja|Misslyckade noder|Antal|Genomsnitt|Antal noder som har åsidosatts. De här noderna är de noder med låg prioritet som tas bort från den tillgängliga Node-poolen.|Scenario, kluster namn|
|Förbereder körningar|Ja|Förbereder körningar|Antal|Totalt|Antal körningar som förbereds för den här arbets ytan. Antalet uppdateras när en körning går in i förberedelse tillstånd medan körnings miljön förbereds.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Etablerings körningar|Ja|Etablerings körningar|Antal|Totalt|Antal körningar som håller på att etablering för den här arbets ytan. Antalet uppdateras när en körning väntar på skapande eller etablering av beräknings mål.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Köade körningar|Ja|Köade körningar|Antal|Totalt|Antal körningar som har placerats i kö för den här arbets ytan. Antalet uppdateras när en körning placeras i kö i beräknings mål. Kan inträffa när nödvändiga datornoder ska vara klara.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Kvot användning i procent|Ja|Kvot användning i procent|Antal|Genomsnitt|Procent av kvoten som används|Scenario, kluster namn, VmFamilyName, VmPriority|
|Startade körningar|Ja|Startade körningar|Antal|Totalt|Antal körningar som körs för den här arbets ytan. Antalet uppdateras när körningen börjar köras på nödvändiga resurser.|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Startar körningar|Ja|Startar körningar|Antal|Totalt|Antal körningar som har startats för den här arbets ytan. Antalet uppdateras när begäran om att skapa körnings-och körnings information, till exempel körnings-ID, har fyllts i|Scenario, RunType, PublishedPipelineId, ComputeType, PipelineStepType, ExperimentName|
|Totalt antal kärnor|Ja|Totalt antal kärnor|Antal|Genomsnitt|Antal total kärnor|Scenario, kluster namn|
|Totalt antal noder|Ja|Totalt antal noder|Antal|Genomsnitt|Totalt antal noder. Den här summan innehåller några aktiva noder, inaktiva noder, oanvändbara noder, Premepted noder, lämnar noder|Scenario, kluster namn|
|Oanvändbara kärnor|Ja|Oanvändbara kärnor|Antal|Genomsnitt|Antal oanvändbara kärnor|Scenario, kluster namn|
|Oanvändbara noder|Ja|Oanvändbara noder|Antal|Genomsnitt|Antal oanvändbara noder. Oanvändbara noder fungerar inte på grund av ett problem som inte kan matchas. De här noderna återanvänds av Azure.|Scenario, kluster namn|
|Varningar|Ja|Varningar|Antal|Totalt|Antal körnings varningar i den här arbets ytan. Antalet uppdateras när en körning påträffar en varning.|Scenario|


## <a name="microsoftmapsaccounts"></a>Microsoft. Maps/konton

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Ja|Tillgänglighet|Procent|Genomsnitt|Tillgänglighet för API: erna|ApiCategory, ApiName|
|Användning|Inga|Användning|Antal|Antal|Antal API-anrop|ApiCategory, ApiName, ResultType, ResponseCode|


## <a name="microsoftmediamediaservices"></a>Microsoft. Media/Media Services

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|AssetCount|Ja|Antal till gångar|Antal|Genomsnitt|Hur många till gångar som redan har skapats i det aktuella medie tjänst kontot|Inga dimensioner|
|AssetQuota|Ja|Till gångs kvot|Antal|Genomsnitt|Hur många till gångar som tillåts för det aktuella Media Service-kontot|Inga dimensioner|
|AssetQuotaUsedPercentage|Ja|Använd procent andel till till gångs kvot|Procent|Genomsnitt|Procent andel till gång som används i det aktuella medie tjänst kontot|Inga dimensioner|
|ContentKeyPolicyCount|Ja|Antal nyckel principer för innehåll|Antal|Genomsnitt|Hur många innehålls nyckel principer som redan har skapats i det aktuella medie tjänst kontot|Inga dimensioner|
|ContentKeyPolicyQuota|Ja|Kvot för innehålls nyckel princip|Antal|Genomsnitt|Hur många innehålls nyckel principer som tillåts för det aktuella Media Service-kontot|Inga dimensioner|
|ContentKeyPolicyQuotaUsedPercentage|Ja|Procent andel av nyckel princip för innehåll|Procent|Genomsnitt|Procent andel av nyckel princip för innehåll i det aktuella medie tjänst kontot|Inga dimensioner|
|StreamingPolicyCount|Ja|Antal strömmande principer|Antal|Genomsnitt|Hur många strömmande principer som redan har skapats i det aktuella medie tjänst kontot|Inga dimensioner|
|StreamingPolicyQuota|Ja|Kvot för strömmande princip|Antal|Genomsnitt|Hur många strömmande principer som tillåts för det aktuella Media Service-kontot|Inga dimensioner|
|StreamingPolicyQuotaUsedPercentage|Ja|Använd procent andel av princip för strömning|Procent|Genomsnitt|Procentuell strömnings princip som används i det aktuella medie tjänst kontot|Inga dimensioner|


## <a name="microsoftmediamediaservicesstreamingendpoints"></a>Microsoft. Media/Media Services/strömnings slut punkter

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Utgående|Ja|Utgående|Byte|Totalt|Mängden utgående data, i byte.|OutputFormat|
|Begäranden|Ja|Begäranden|Antal|Totalt|Begär anden till en slut punkt för direkt uppspelning.|OutputFormat, HttpStatusCode, ErrorCode|
|SuccessE2ELatency|Ja|Slutför svars tid för slut punkt till slut punkt|Millisekunder|Genomsnitt|Genomsnittlig svars tid för lyckade begär anden i millisekunder.|OutputFormat|


## <a name="microsoftnetappnetappaccountscapacitypools"></a>Microsoft. NetApp/netAppAccounts/capacityPools

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|VolumePoolAllocatedSize|Ja|Allokerad storlek för pool|Byte|Genomsnitt|Allokerad storlek för poolen|Inga dimensioner|
|VolumePoolAllocatedUsed|Ja|Pool allokerad till volym storlek|Byte|Genomsnitt|Allokerad använt storlek på poolen|Inga dimensioner|
|VolumePoolTotalLogicalSize|Ja|Förbrukad pool storlek|Byte|Genomsnitt|Summan av den logiska storleken för alla volymer som tillhör poolen|Inga dimensioner|
|VolumePoolTotalSnapshotSize|Ja|Total ögonblicks bild storlek för poolen|Byte|Genomsnitt|Summan av ögonblicks bild storleken för alla volymer i poolen|Inga dimensioner|


## <a name="microsoftnetappnetappaccountscapacitypoolsvolumes"></a>Microsoft. NetApp/netAppAccounts/capacityPools/Volumes

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|AverageReadLatency|Ja|Genomsnittlig Läs fördröjning|Millisekunder|Genomsnitt|Genomsnittlig Läs fördröjning i millisekunder per åtgärd|Inga dimensioner|
|AverageWriteLatency|Ja|Genomsnittlig Skriv fördröjning|Millisekunder|Genomsnitt|Genomsnittlig Skriv fördröjning i millisekunder per åtgärd|Inga dimensioner|
|CbsVolumeBackupActive|Ja|Aktivt tillstånd för säkerhets kopiering av volym|Antal|Genomsnitt|Är säkerhets kopian inaktive rad för volymen.|Inga dimensioner|
|CbsVolumeLogicalBackupBytes|Ja|Logiska byte som har säkerhetskopierats|Byte|Genomsnitt|Totalt antal icke-komprimerade/icke-krypterade byte som har säkerhetskopierats för den här volymen.|Inga dimensioner|
|CbsVolumeOperationComplete|Ja|Åtgärds tillstånd|Antal|Genomsnitt|Har den senaste säkerhets kopieringen/återställnings åtgärden slutförts.|Inga dimensioner|
|CbsVolumeOperationTransferredBytes|Ja|Överförda byte för åtgärd|Byte|Genomsnitt|Totalt antal byte som överförts för den senaste säkerhets kopieringen/återställningen.|Inga dimensioner|
|CbsVolumeProtected|Ja|Volymens skyddade tillstånd|Antal|Genomsnitt|Är volym som skyddas av Cloud backup service.|Inga dimensioner|
|ReadIops|Ja|Läs IOPS|CountPerSecond|Genomsnitt|Läs-/ut-åtgärder per sekund|Inga dimensioner|
|VolumeAllocatedSize|Ja|Allokerad volym storlek|Byte|Genomsnitt|En volyms allokerade storlek|Inga dimensioner|
|VolumeLogicalSize|Ja|Storlek på förbrukad volym|Byte|Genomsnitt|Den logiska storleken på volymen (använda byte)|Inga dimensioner|
|VolumeSnapshotSize|Ja|Storlek på volym ögonblicks bild|Byte|Genomsnitt|Storlek på alla ögonblicks bilder i volymen|Inga dimensioner|
|WriteIops|Ja|Skriv IOPS|CountPerSecond|Genomsnitt|Skriv in/ut-åtgärder per sekund|Inga dimensioner|
|XregionReplicationHealthy|Ja|Är Volume Replication-status felfri|Antal|Genomsnitt|Villkor för relationen, 1 eller 0.|Inga dimensioner|
|XregionReplicationLagTime|Ja|Fördröjning för Volume Replication|Sekunder|Genomsnitt|Hur lång tid i sekunder som data på speglingen lags bakom källan.|Inga dimensioner|
|XregionReplicationLastTransferDuration|Ja|Varaktighet för senaste överföring av Volume Replication|Sekunder|Genomsnitt|Hur lång tid i sekunder det tog för den senaste överföringen att slutföras.|Inga dimensioner|
|XregionReplicationLastTransferSize|Ja|Senaste överförings storlek för Volume Replication|Byte|Genomsnitt|Det totala antalet byte som överförs som en del av den senaste överföringen.|Inga dimensioner|
|XregionReplicationRelationshipProgress|Ja|Diskens replikerings förlopp|Byte|Genomsnitt|Total mängd data som överförts för den aktuella överförings åtgärden.|Inga dimensioner|
|XregionReplicationRelationshipTransferring|Ja|Överför volym replikering|Antal|Genomsnitt|Anger om status för Volume Replication är ' transfer '.|Inga dimensioner|
|XregionReplicationTotalTransferBytes|Ja|Överföring av Volume Replication totalt|Byte|Genomsnitt|Ackumulerade byte som överförts för relationen.|Inga dimensioner|


## <a name="microsoftnetworkapplicationgateways"></a>Microsoft. Network/applicationGateways

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|ApplicationGatewayTotalTime|Inga|Application Gateway total tid|Millisekunder|Genomsnitt|Genomsnittlig tid det tar för en begäran att bearbetas och dess svar ska skickas. Detta beräknas som genomsnitt av intervallet från den tid då Application Gateway tar emot den första byten av en HTTP-begäran till den tidpunkt då åtgärden skicka svar slutförs. Det är viktigt att Observera att detta vanligt vis omfattar Application Gateway bearbetnings tid, tid då paket för begäran och svar överförs över nätverket och hur lång tid det tog att svara på backend-servern.|Lyssnare|
|AvgRequestCountPerHealthyHost|Inga|Begär Anden per minut per felfri värd|Antal|Genomsnitt|Genomsnittligt antal begär Anden per minut per felfri backend-värd i en pool|BackendSettingsPool|
|BackendConnectTime|Inga|Server dels anslutnings tid|Millisekunder|Genomsnitt|Åtgången tid för att upprätta en anslutning till en backend-server|Lyssnare, BackendServer, BackendPool, BackendHttpSetting|
|BackendFirstByteResponseTime|Inga|Svars tid för första byte för Server del|Millisekunder|Genomsnitt|Tidsintervall mellan början av att upprätta en anslutning till backend-servern och ta emot den första byten i svars huvudet, ungefär bearbetnings tiden för backend-servern|Lyssnare, BackendServer, BackendPool, BackendHttpSetting|
|BackendLastByteResponseTime|Inga|Svars tid för senaste byte för Server delen|Millisekunder|Genomsnitt|Tidsintervall mellan början av att upprätta en anslutning till backend-servern och ta emot den sista byten i svars texten|Lyssnare, BackendServer, BackendPool, BackendHttpSetting|
|BackendResponseStatus|Ja|Svars status för Server del|Antal|Totalt|Antalet HTTP-svars koder som genereras av Server dels medlemmar. Detta omfattar inte några svars koder som genereras av Application Gateway.|BackendServer, BackendPool, BackendHttpSetting, HttpStatusGroup|
|BlockedCount|Ja|Webb program brand vägg blockerade begär Anden regel distribution|Antal|Totalt|Webb program brand vägg blockerade begär Anden regel distribution|RuleGroup, RuleId|
|BlockedReqCount|Ja|Antal blockerade förfrågningar för webb program brand vägg|Antal|Totalt|Antal blockerade förfrågningar för webb program brand vägg|Inga dimensioner|
|BytesReceived|Ja|Mottagna byte|Byte|Totalt|Det totala antalet byte som tagits emot av Application Gateway från klienterna|Lyssnare|
|Bytes sent|Ja|Skickade byte|Byte|Totalt|Det totala antalet byte som har skickats av Application Gateway till klienterna|Lyssnare|
|CapacityUnits|Inga|Aktuella kapacitets enheter|Antal|Genomsnitt|Förbrukade kapacitets enheter|Inga dimensioner|
|ClientRtt|Inga|Klient-/klient|Millisekunder|Genomsnitt|Genomsnittlig fördröjning mellan klienter och Application Gateway. Det här måttet anger hur lång tid det tar att upprätta anslutningar och retur bekräftelser|Lyssnare|
|ComputeUnits|Inga|Aktuella beräknings enheter|Antal|Genomsnitt|Förbrukade beräknings enheter|Inga dimensioner|
|CpuUtilization|Inga|CPU-användning|Procent|Genomsnitt|Aktuell processor användning för Application Gateway|Inga dimensioner|
|CurrentConnections|Ja|Aktuella anslutningar|Antal|Totalt|Antal aktuella anslutningar som upprättats med Application Gateway|Inga dimensioner|
|EstimatedBilledCapacityUnits|Inga|Uppskattade enheter för fakturerings kapacitet|Antal|Genomsnitt|Uppskattade kapacitets enheter som ska debiteras|Inga dimensioner|
|FailedRequests|Ja|Misslyckade begäranden|Antal|Totalt|Antal misslyckade förfrågningar som Application Gateway har betjänat|BackendSettingsPool|
|FixedBillableCapacityUnits|Inga|Fasta fakturerbara kapacitetsenheter|Antal|Genomsnitt|Lägsta kapacitets enhet som ska debiteras|Inga dimensioner|
|HealthyHostCount|Ja|Antal felfria värdar|Antal|Genomsnitt|Antal felfria Server dels värdar|BackendSettingsPool|
|MatchedCount|Ja|Brand vägg för total regel distribution i webb program|Antal|Totalt|Brand vägg för webb program, total regel distribution för inkommande trafik|RuleGroup, RuleId|
|NewConnectionsPerSecond|Inga|Nya anslutningar per sekund|CountPerSecond|Genomsnitt|Nya anslutningar per sekund som upprättats med Application Gateway|Inga dimensioner|
|ResponseStatus|Ja|Svars status|Antal|Totalt|Http-svarets status returnerades av Application Gateway|HttpStatusGroup|
|Dataflöde|Inga|Dataflöde|BytesPerSecond|Genomsnitt|Antal byte per sekund som Application Gateway har betjänat|Inga dimensioner|
|TlsProtocol|Ja|Klientens TLS-protokoll|Antal|Totalt|Antalet TLS-och icke-TLS-begäranden som initieras av klienten som upprättade anslutningen till Application Gateway. Om du vill visa TLS-protokollets distribution filtrerar du efter dimension TLS-protokollet.|Lyssnare, TlsProtocol|
|TotalRequests|Ja|Totalt antal förfrågningar|Antal|Totalt|Antal lyckade förfrågningar som Application Gateway har betjänat|BackendSettingsPool|
|UnhealthyHostCount|Ja|Antal felaktiga värdar|Antal|Genomsnitt|Antal värdar för skadade Server delar|BackendSettingsPool|


## <a name="microsoftnetworkazurefirewalls"></a>Microsoft. Network/azurefirewalls

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|ApplicationRuleHit|Ja|Antal träffar för program regler|Antal|Totalt|Antal gånger som program regler träffades|Status, orsak, protokoll|
|DataProcessed|Ja|Bearbetade data|Byte|Totalt|Total mängd data som bearbetas av den här brand väggen|Inga dimensioner|
|FirewallHealth|Ja|Hälso tillstånd för brand vägg|Procent|Genomsnitt|Anger den övergripande hälso tillståndet för den här brand väggen|Status, orsak|
|NetworkRuleHit|Ja|Antal träffar för nätverks regler|Antal|Totalt|Antal gånger som nätverks regler träffades|Status, orsak, protokoll|
|SNATPortUtilization|Ja|SNAT-port användning|Procent|Genomsnitt|Procent andel utgående SNAT-portar som används för närvarande|Protokoll|
|Dataflöde|Inga|Dataflöde|BitsPerSecond|Genomsnitt|Genomflöde som bearbetas av den här brand väggen|Inga dimensioner|


## <a name="microsoftnetworkconnections"></a>Microsoft. Network/Connections

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|BitsInPerSecond|Ja|BitsInPerSecond|BitsPerSecond|Genomsnitt|Inkommande bitar till Azure per sekund|Inga dimensioner|
|BitsOutPerSecond|Ja|BitsOutPerSecond|BitsPerSecond|Genomsnitt|Utgående bitar till Azure per sekund|Inga dimensioner|


## <a name="microsoftnetworkdnszones"></a>Microsoft. Network/dnszones

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|QueryVolume|Ja|Fråga volym|Antal|Totalt|Antal frågor som hanteras för en DNS-zon|Inga dimensioner|
|RecordSetCapacityUtilization|Inga|Kapacitets användning för post uppsättning|Procent|Maximal|Procent av post uppsättnings kapaciteten som används av en DNS-zon|Inga dimensioner|
|RecordSetCount|Ja|Antal post uppsättningar|Antal|Maximal|Antal post uppsättningar i en DNS-zon|Inga dimensioner|


## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft. Network/expressRouteCircuits

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|ArpAvailability|Ja|ARP-tillgänglighet|Procent|Genomsnitt|ARP-tillgänglighet från MSEE: N till alla peer-datorer.|PeeringType, peer|
|BgpAvailability|Ja|BGP-tillgänglighet|Procent|Genomsnitt|BGP-tillgänglighet från MSEE: N till alla peer-datorer.|PeeringType, peer|
|BitsInPerSecond|Inga|BitsInPerSecond|BitsPerSecond|Genomsnitt|Inkommande bitar till Azure per sekund|PeeringType, DeviceRole|
|BitsOutPerSecond|Inga|BitsOutPerSecond|BitsPerSecond|Genomsnitt|Utgående bitar till Azure per sekund|PeeringType, DeviceRole|
|GlobalReachBitsInPerSecond|Inga|GlobalReachBitsInPerSecond|BitsPerSecond|Genomsnitt|Inkommande bitar till Azure per sekund|PeeredCircuitSKey|
|GlobalReachBitsOutPerSecond|Inga|GlobalReachBitsOutPerSecond|BitsPerSecond|Genomsnitt|Utgående bitar till Azure per sekund|PeeredCircuitSKey|
|QosDropBitsInPerSecond|Ja|DroppedInBitsPerSecond|BitsPerSecond|Genomsnitt|Ingress BITS av data som ignoreras per sekund|Inga dimensioner|
|QosDropBitsOutPerSecond|Ja|DroppedOutBitsPerSecond|BitsPerSecond|Genomsnitt|Utgående bitar med data som ignoreras per sekund|Inga dimensioner|


## <a name="microsoftnetworkexpressroutecircuitspeerings"></a>Microsoft. Network/expressRouteCircuits/peering

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|BitsInPerSecond|Ja|BitsInPerSecond|BitsPerSecond|Genomsnitt|Inkommande bitar till Azure per sekund|Inga dimensioner|
|BitsOutPerSecond|Ja|BitsOutPerSecond|BitsPerSecond|Genomsnitt|Utgående bitar till Azure per sekund|Inga dimensioner|


## <a name="microsoftnetworkexpressroutegateways"></a>Microsoft. Network/expressRouteGateways

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|ErGatewayConnectionBitsInPerSecond|Inga|BitsInPerSecond|BitsPerSecond|Genomsnitt|Inkommande bitar till Azure per sekund|ConnectionName|
|ErGatewayConnectionBitsOutPerSecond|Inga|BitsOutPerSecond|BitsPerSecond|Genomsnitt|Utgående bitar till Azure per sekund|ConnectionName|


## <a name="microsoftnetworkexpressrouteports"></a>Microsoft. Network/expressRoutePorts

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|AdminState|Ja|AdminState|Antal|Genomsnitt|Administratörs tillstånd för porten|Länk|
|LineProtocol|Ja|LineProtocol|Antal|Genomsnitt|Status för linje protokoll för porten|Länk|
|PortBitsInPerSecond|Ja|BitsInPerSecond|BitsPerSecond|Genomsnitt|Inkommande bitar till Azure per sekund|Länk|
|PortBitsOutPerSecond|Ja|BitsOutPerSecond|BitsPerSecond|Genomsnitt|Utgående bitar till Azure per sekund|Länk|
|RxLightLevel|Ja|RxLightLevel|Antal|Genomsnitt|RX ljus nivå i dBm|Länk, Lane|
|TxLightLevel|Ja|TxLightLevel|Antal|Genomsnitt|TX-ljusnivå i dBm|Länk, Lane|


## <a name="microsoftnetworkfrontdoors"></a>Microsoft. Network/frontdoors

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|BackendHealthPercentage|Ja|Server delens hälso procent|Procent|Genomsnitt|Procent andelen lyckade hälso avsökningar från HTTP/S-proxyn till Server delar|Backend, BackendPool|
|BackendRequestCount|Ja|Antal Server dels begär Anden|Antal|Totalt|Antalet förfrågningar som skickats från HTTP/S-proxyn till Server delar|HttpStatus, HttpStatusGroup, Server del|
|BackendRequestLatency|Ja|Svars tid för Server del|Millisekunder|Genomsnitt|Tiden som beräknas från när begäran skickades av HTTP/S-proxyn till Server delen tills HTTP/S-proxyn fick den senaste svars byten från Server delen|Serverdel|
|BillableResponseSize|Ja|Fakturerbart svars storlek|Byte|Totalt|Antalet fakturerbara byte (minsta 2KB per begäran) som skickats som svar från HTTP/S-proxy till klienter.|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestCount|Ja|Antal begäranden|Antal|Totalt|Antalet klient förfrågningar som hanteras av HTTP/S-proxyn|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|RequestSize|Ja|Begär ande storlek|Byte|Totalt|Antalet byte som har skickats som begär Anden från klienter till HTTP/S-proxyn|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|ResponseSize|Ja|Svars storlek|Byte|Totalt|Antalet byte som skickats som svar från HTTP/S-proxy till klienter|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|TotalLatency|Ja|Total svars tid|Millisekunder|Genomsnitt|Den tid som beräknas från när klientbegäran togs emot av HTTP/S-proxy tills klienten bekräftade den senaste svars byten från HTTP/S-proxyn|HttpStatus, HttpStatusGroup, ClientRegion, ClientCountry|
|WebApplicationFirewallRequestCount|Ja|Antal begär Anden om webb programs brand vägg|Antal|Totalt|Antalet klient begär Anden som bearbetats av brand väggen för webbaserade program|PolicyName, RuleName, åtgärd|


## <a name="microsoftnetworkloadbalancers"></a>Microsoft. Network/belastningsutjämnare

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|AllocatedSnatPorts|Inga|Allokerade SNAT-portar|Antal|Genomsnitt|Totalt antal SNAT-portar som allokerats inom tids perioden|FrontendIPAddress, BackendIPAddress, ProtocolType, |
|ByteCount|Ja|Antal byte|Byte|Totalt|Totalt antal byte som skickats inom tids perioden|FrontendIPAddress, FrontendPort, Direction|
|DipAvailability|Ja|Status för hälsoavsökningen|Antal|Genomsnitt|Genomsnittlig status för Load Balancer hälso avsökning per tids period|ProtocolType, BackendPort, FrontendIPAddress, FrontendPort, BackendIPAddress|
|PacketCount|Ja|Antal paket|Antal|Totalt|Totalt antal överförda paket inom tids perioden|FrontendIPAddress, FrontendPort, Direction|
|SnatConnectionCount|Ja|Antal SNAT-anslutningar|Antal|Totalt|Totalt antal nya SNAT-anslutningar som skapats inom tids perioden|FrontendIPAddress, BackendIPAddress, ConnectionState|
|SYNCount|Ja|Antal SYN|Antal|Totalt|Totalt antal skickade SYN paket inom tids perioden|FrontendIPAddress, FrontendPort, Direction|
|UsedSnatPorts|Inga|Använda SNAT-portar|Antal|Genomsnitt|Totalt antal SNAT-portar som använts inom tids perioden|FrontendIPAddress, BackendIPAddress, ProtocolType, |
|VipAvailability|Ja|Tillgänglighet för datasökvägar|Antal|Genomsnitt|Genomsnittlig tillgänglighet för Load Balancer data Sök väg per tids längd|FrontendIPAddress, FrontendPort|


## <a name="microsoftnetworknetworkinterfaces"></a>Microsoft. Network/networkInterfaces

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|BytesReceivedRate|Ja|Mottagna byte|Byte|Totalt|Antal byte som nätverks gränssnittet har tagits emot|Inga dimensioner|
|BytesSentRate|Ja|Skickade byte|Byte|Totalt|Antal byte som nätverks gränssnittet har skickats|Inga dimensioner|
|PacketsReceivedRate|Ja|Mottagna paket|Antal|Totalt|Antal paket som nätverks gränssnittet har fått|Inga dimensioner|
|PacketsSentRate|Ja|Skickade paket|Antal|Totalt|Antal paket som nätverks gränssnittet har skickats|Inga dimensioner|


## <a name="microsoftnetworknetworkwatchersconnectionmonitors"></a>Microsoft. Network/networkWatchers/connectionMonitors

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|AverageRoundtripMs|Ja|Genomsnittlig tid för fördröjning (MS)|Millisekunder|Genomsnitt|Genomsnittlig tid för nätverks fördröjning (MS) för anslutnings övervaknings avsökningar som skickas mellan källa och mål|Inga dimensioner|
|ChecksFailedPercent|Ja|Misslyckade kontroller i procent (för hands version)|Procent|Genomsnitt|% av kontrollerna för anslutnings övervakning misslyckades|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, destination port, TestGroupName, TestConfigurationName, SourceIP, DestinationIP, SourceSubnet, DestinationSubnet|
|ProbesFailedPercent|Ja|% Avsökningar misslyckades|Procent|Genomsnitt|% av anslutnings övervaknings avsökningarna misslyckades|Inga dimensioner|
|RoundTripTimeMs|Ja|Round-Trip tid (MS) (för hands version)|Millisekunder|Genomsnitt|Svars tid i millisekunder för kontrollerna för anslutnings övervakning|SourceAddress, SourceName, SourceResourceId, SourceType, Protocol, DestinationAddress, DestinationName, DestinationResourceId, DestinationType, destination port, TestGroupName, TestConfigurationName, SourceIP, DestinationIP, SourceSubnet, DestinationSubnet|


## <a name="microsoftnetworkpublicipaddresses"></a>Microsoft. Network/publicIPAddresses

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|ByteCount|Ja|Antal byte|Byte|Totalt|Totalt antal byte som skickats inom tids perioden|Port, riktning|
|BytesDroppedDDoS|Ja|Ignorerade inkommande byte DDoS|BytesPerSecond|Maximal|Ignorerade inkommande byte DDoS|Inga dimensioner|
|BytesForwardedDDoS|Ja|Inkommande byte, vidarebefordrade DDoS|BytesPerSecond|Maximal|Inkommande byte, vidarebefordrade DDoS|Inga dimensioner|
|BytesInDDoS|Ja|DDoS för inkommande byte|BytesPerSecond|Maximal|DDoS för inkommande byte|Inga dimensioner|
|DDoSTriggerSYNPackets|Ja|Inkommande SYN paket för att utlösa DDoS-minskning|CountPerSecond|Maximal|Inkommande SYN paket för att utlösa DDoS-minskning|Inga dimensioner|
|DDoSTriggerTCPPackets|Ja|Inkommande TCP-paket för att utlösa DDoS-minskning|CountPerSecond|Maximal|Inkommande TCP-paket för att utlösa DDoS-minskning|Inga dimensioner|
|DDoSTriggerUDPPackets|Ja|Ingående UDP-paket för att utlösa DDoS-minskning|CountPerSecond|Maximal|Ingående UDP-paket för att utlösa DDoS-minskning|Inga dimensioner|
|IfUnderDDoSAttack|Ja|Under DDoS-attack eller inte|Antal|Maximal|Under DDoS-attack eller inte|Inga dimensioner|
|PacketCount|Ja|Antal paket|Antal|Totalt|Totalt antal överförda paket inom tids perioden|Port, riktning|
|PacketsDroppedDDoS|Ja|Inkommande paket som släppts DDoS|CountPerSecond|Maximal|Inkommande paket som släppts DDoS|Inga dimensioner|
|PacketsForwardedDDoS|Ja|Vidarebefordrade inkommande paket DDoS|CountPerSecond|Maximal|Vidarebefordrade inkommande paket DDoS|Inga dimensioner|
|PacketsInDDoS|Ja|DDoS för inkommande paket|CountPerSecond|Maximal|DDoS för inkommande paket|Inga dimensioner|
|SynCount|Ja|Antal SYN|Antal|Totalt|Totalt antal skickade SYN paket inom tids perioden|Port, riktning|
|TCPBytesDroppedDDoS|Ja|Inkommande TCP-byte utelämnade DDoS|BytesPerSecond|Maximal|Inkommande TCP-byte utelämnade DDoS|Inga dimensioner|
|TCPBytesForwardedDDoS|Ja|Inkommande TCP byte-vidarebefordrade DDoS|BytesPerSecond|Maximal|Inkommande TCP byte-vidarebefordrade DDoS|Inga dimensioner|
|TCPBytesInDDoS|Ja|DDoS för inkommande TCP-byte|BytesPerSecond|Maximal|DDoS för inkommande TCP-byte|Inga dimensioner|
|TCPPacketsDroppedDDoS|Ja|Inkommande TCP-paket ignorerade DDoS|CountPerSecond|Maximal|Inkommande TCP-paket ignorerade DDoS|Inga dimensioner|
|TCPPacketsForwardedDDoS|Ja|Inkommande TCP-paket, vidarebefordrade DDoS|CountPerSecond|Maximal|Inkommande TCP-paket, vidarebefordrade DDoS|Inga dimensioner|
|TCPPacketsInDDoS|Ja|DDoS inkommande TCP-paket|CountPerSecond|Maximal|DDoS inkommande TCP-paket|Inga dimensioner|
|UDPBytesDroppedDDoS|Ja|Inkommande UDP-byte utelämnade DDoS|BytesPerSecond|Maximal|Inkommande UDP-byte utelämnade DDoS|Inga dimensioner|
|UDPBytesForwardedDDoS|Ja|Inkommande UDP byte vidarebefordrade DDoS|BytesPerSecond|Maximal|Inkommande UDP byte vidarebefordrade DDoS|Inga dimensioner|
|UDPBytesInDDoS|Ja|DDoS för inkommande UDP-byte|BytesPerSecond|Maximal|DDoS för inkommande UDP-byte|Inga dimensioner|
|UDPPacketsDroppedDDoS|Ja|Ignorerade inkommande UDP-paket DDoS|CountPerSecond|Maximal|Ignorerade inkommande UDP-paket DDoS|Inga dimensioner|
|UDPPacketsForwardedDDoS|Ja|Vidarebefordrade inkommande UDP-paket DDoS|CountPerSecond|Maximal|Vidarebefordrade inkommande UDP-paket DDoS|Inga dimensioner|
|UDPPacketsInDDoS|Ja|DDoS för inkommande UDP-paket|CountPerSecond|Maximal|DDoS för inkommande UDP-paket|Inga dimensioner|
|VipAvailability|Ja|Tillgänglighet för datasökvägar|Antal|Genomsnitt|Genomsnittlig tillgänglighet för IP-adress per tids längd|Port|


## <a name="microsoftnetworktrafficmanagerprofiles"></a>Microsoft. Network/trafficManagerProfiles

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|ProbeAgentCurrentEndpointStateByProfileResourceId|Ja|Slut punkts status efter slut punkt|Antal|Maximal|1 om avsöknings status för en slut punkt är "aktive rad", annars 0.|EndpointName|
|QpsByEndpoint|Ja|Returnerade frågor efter slut punkt|Antal|Totalt|Antal gånger som en Traffic Manager-slutpunkt returnerades inom den aktuella tids ramen|EndpointName|


## <a name="microsoftnetworkvirtualnetworkgateways"></a>Microsoft. Network/virtualNetworkGateways

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|AverageBandwidth|Ja|Gatewayens S2S-bandbredd|BytesPerSecond|Genomsnitt|Genomsnittlig bandbredd för plats-till-plats för en gateway i byte per sekund|Inga dimensioner|
|P2SBandwidth|Ja|Gateway P2S-bandbredd|BytesPerSecond|Genomsnitt|Genomsnittlig punkt-till-plats-bandbredd för en gateway i byte per sekund|Inga dimensioner|
|P2SConnectionCount|Ja|Antal P2S-anslutningar|Antal|Maximal|Antal anslutningar från punkt-till-plats för en gateway|Protokoll|
|TunnelAverageBandwidth|Ja|Tunnelbandbredd|BytesPerSecond|Genomsnitt|Genomsnittlig bandbredd för en tunnel i byte per sekund|ConnectionName, RemoteIP|
|TunnelEgressBytes|Ja|Utgående byte för tunnel|Byte|Totalt|Utgående byte för en tunnel|ConnectionName, RemoteIP|
|TunnelEgressPacketDropTSMismatch|Ja|Utgående ignorerade TS-paket med matchningsfel för tunnel|Antal|Totalt|Antal utgående ignorerade paket från trafikväljare med matchningsfel för en tunnel|ConnectionName, RemoteIP|
|TunnelEgressPackets|Ja|Utgående tunnelpaket|Antal|Totalt|Antal utgående paket för en tunnel|ConnectionName, RemoteIP|
|TunnelIngressBytes|Ja|Inkommande byte för tunnel|Byte|Totalt|Inkommande byte för en tunnel|ConnectionName, RemoteIP|
|TunnelIngressPacketDropTSMismatch|Ja|Inkommande ignorerade TS-paket med matchningsfel för tunnel|Antal|Totalt|Antal inkommande ignorerade paket från trafikväljare med matchningsfel för en tunnel|ConnectionName, RemoteIP|
|TunnelIngressPackets|Ja|Inkommande tunnel paket|Antal|Totalt|Antal inkommande paket för en tunnel|ConnectionName, RemoteIP|


## <a name="microsoftnetworkvirtualnetworks"></a>Microsoft. Network/virtualNetworks

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|PingMeshAverageRoundtripMs|Ja|Fördröjning för ping till en virtuell dator|Millisekunder|Genomsnitt|Tur och retur-tid för pingar som skickas till en virtuell måldator|SourceCustomerAddress, DestinationCustomerAddress|
|PingMeshProbesFailedPercent|Ja|Misslyckade pingar till en virtuell dator|Procent|Genomsnitt|Procent av antalet misslyckade pingar till totalt antal skickade pingar för en virtuell måldator|SourceCustomerAddress, DestinationCustomerAddress|


## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft. NotificationHubs/Namespaces/NotificationHubs

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|inkommande|Ja|Inkommande meddelanden|Antal|Totalt|Antalet lyckade sändnings-API-anrop. |Inga dimensioner|
|inkommande. alla. failedrequests|Ja|Alla inkommande misslyckade förfrågningar|Antal|Totalt|Totalt antal inkommande misslyckade begär Anden för en Notification Hub|Inga dimensioner|
|inkommande. alla. förfrågningar|Ja|Alla inkommande begär Anden|Antal|Totalt|Totalt antal inkommande begär Anden för en Notification Hub|Inga dimensioner|
|inkommande. schemalagd|Ja|Schemalagda push-meddelanden har skickats|Antal|Totalt|Schemalagda push-meddelanden har avbrutits|Inga dimensioner|
|inkommande. schemalagd. Avbryt|Ja|Schemalagda push-meddelanden har avbrutits|Antal|Totalt|Schemalagda push-meddelanden har avbrutits|Inga dimensioner|
|installation. alla|Ja|Installations hanterings åtgärder|Antal|Totalt|Installations hanterings åtgärder|Inga dimensioner|
|installation. Delete|Ja|Ta bort installations åtgärder|Antal|Totalt|Ta bort installations åtgärder|Inga dimensioner|
|installation. get|Ja|Hämta installations åtgärder|Antal|Totalt|Hämta installations åtgärder|Inga dimensioner|
|installation. patch|Ja|Installation av korrigerings åtgärder|Antal|Totalt|Installation av korrigerings åtgärder|Inga dimensioner|
|installation. upsert|Ja|Skapa eller uppdatera installations åtgärder|Antal|Totalt|Skapa eller uppdatera installations åtgärder|Inga dimensioner|
|notificationhub. push-meddelanden|Ja|Alla utgående meddelanden|Antal|Totalt|Alla utgående aviseringar för Notification Hub|Inga dimensioner|
|utgående. allpns. badorexpiredchannel|Ja|Dåliga eller utgångna kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom kanal/token/registrationId i registreringen har upphört att gälla eller är ogiltiga.|Inga dimensioner|
|utgående. allpns. channelerror|Ja|Kanal fel|Antal|Totalt|Antal push-meddelanden som inte har misslyckats på grund av att kanalen var ogiltig och som inte är associerad med rätt app-begränsning eller upphörde att gälla.|Inga dimensioner|
|utgående. allpns. invalidpayload|Ja|Nytto Last fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom PNS returnerade ett felaktigt nytto Last fel.|Inga dimensioner|
|utgående. allpns. pnserror|Ja|Fel i externt meddelande system|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av ett problem med att kommunicera med PNS (exkluderar autentiseringsproblem).|Inga dimensioner|
|utgående. allpns. lyckades|Ja|Lyckade aviseringar|Antal|Totalt|Antalet lyckade aviseringar.|Inga dimensioner|
|utgående. APN. badchannel|Ja|APN dåligt kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av att token är ogiltig (APN-status kod: 8).|Inga dimensioner|
|utgående. APN. expiredchannel|Ja|APN utgånget i kanal fel|Antal|Totalt|Antalet token som ogiltig förklarades av APNS-feedback-kanalen.|Inga dimensioner|
|utgående. APN. invalidcredentials|Ja|APN-auktoriseringsfel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna, eller om autentiseringsuppgifterna blockeras.|Inga dimensioner|
|utgående. APN. invalidnotificationsize|Ja|APN ogiltigt meddelande storleks fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom nytto lasten var för stor (APN-status kod: 7).|Inga dimensioner|
|utgående. APN. pnserror|Ja|APN-fel|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av fel vid kommunikation med APN.|Inga dimensioner|
|utgående. APN. lyckades|Ja|APN-lyckade meddelanden|Antal|Totalt|Antalet lyckade aviseringar.|Inga dimensioner|
|utgående. GCM. authenticationerror|Ja|GCM-autentiseringsfel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna som autentiseringsuppgifterna är blockerade eller om SenderId inte har kon figurer ATS korrekt i appen (GCM result: MismatchedSenderId).|Inga dimensioner|
|utgående. GCM. badchannel|Ja|GCM felaktigt kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom registrationId i registreringen inte kändes igen (GCM-resultat: ogiltig registrering).|Inga dimensioner|
|utgående. GCM. expiredchannel|Ja|GCM utgången kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av att registrationId i registreringen har upphört att gälla (GCM-resultat: NotRegistered).|Inga dimensioner|
|utgående. GCM. invalidcredentials|Ja|GCM-auktoriseringsfel (ogiltiga autentiseringsuppgifter)|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna, eller om autentiseringsuppgifterna blockeras.|Inga dimensioner|
|utgående. GCM. invalidnotificationformat|Ja|GCM ogiltigt meddelande format|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom nytto lasten inte formaterades korrekt (GCM-resultat: InvalidDataKey eller InvalidTtl).|Inga dimensioner|
|utgående. GCM. invalidnotificationsize|Ja|GCM ogiltig meddelande storleks fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom nytto lasten var för stor (GCM-resultat: MessageTooBig).|Inga dimensioner|
|utgående. GCM. pnserror|Ja|GCM-fel|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av fel vid kommunikation med GCM.|Inga dimensioner|
|utgående. GCM. lyckades|Ja|GCM slutförda meddelanden|Antal|Totalt|Antalet lyckade aviseringar.|Inga dimensioner|
|utgående. GCM. begränsad|Ja|GCM-begränsade meddelanden|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom GCM begränsade till den här appen (GCM-status kod: 501-599 eller resultat: ej tillgängligt).|Inga dimensioner|
|utgående. GCM. wrongchannel|Ja|GCM fel kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom registrationId i registreringen inte är kopplad till den aktuella appen (GCM result: InvalidPackageName).|Inga dimensioner|
|utgående. MPNS. authenticationerror|Ja|MPNS-autentiseringsfel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna, eller om autentiseringsuppgifterna blockeras.|Inga dimensioner|
|utgående. MPNS. badchannel|Ja|MPNS felaktigt kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom ChannelURI i registreringen inte kändes igen (MPNS-status: 404 hittades inte).|Inga dimensioner|
|utgående. MPNS. channeldisconnected|Ja|MPNS-kanalen är frånkopplad|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av att ChannelURI i registreringen var frånkopplad (MPNS status: 412 hittades inte).|Inga dimensioner|
|utgående. MPNS. släppt|Ja|MPNS borttagna meddelanden|Antal|Totalt|Antal push-meddelanden som släppts av MPNS (MPNS-svars huvud: X-NotificationStatus: QueueFull eller undertrycks).|Inga dimensioner|
|utgående. MPNS. invalidcredentials|Ja|MPNS ogiltiga autentiseringsuppgifter|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna, eller om autentiseringsuppgifterna blockeras.|Inga dimensioner|
|utgående. MPNS. invalidnotificationformat|Ja|MPNS ogiltigt meddelande format|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom nytto lasten för meddelandet var för stor.|Inga dimensioner|
|utgående. MPNS. pnserror|Ja|MPNS-fel|Antal|Totalt|Antal push-meddelanden som misslyckades på grund av fel vid kommunikation med MPNS.|Inga dimensioner|
|utgående. MPNS. lyckades|Ja|MPNS slutförda meddelanden|Antal|Totalt|Antalet lyckade aviseringar.|Inga dimensioner|
|utgående. MPNS. begränsad|Ja|MPNS-begränsade meddelanden|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom MPNS begränsar den här appen (WNS MPNS: 406 är inte acceptabel).|Inga dimensioner|
|utgående. WNS. authenticationerror|Ja|WNS-autentiseringsfel|Antal|Totalt|Meddelandet levererades inte på grund av fel vid kommunikation med ogiltiga Windows Live-autentiseringsuppgifter eller felaktigt token.|Inga dimensioner|
|utgående. WNS. badchannel|Ja|WNS felaktigt kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom ChannelURI i registreringen inte kändes igen (WNS-status: 404 hittades inte).|Inga dimensioner|
|utgående. WNS. channeldisconnected|Ja|WNS-kanalen är frånkopplad|Antal|Totalt|Meddelandet utelämnades eftersom ChannelURI i registreringen är begränsat (WNS-svars huvud: X-WNS-DeviceConnectionStatus: frånkopplat).|Inga dimensioner|
|utgående. WNS. channelthrottled|Ja|WNS-kanalen är begränsad|Antal|Totalt|Meddelandet utelämnades eftersom ChannelURI i registreringen är begränsat (WNS-svars huvud: X-WNS-NotificationStatus: channelThrottled).|Inga dimensioner|
|utgående. WNS. släppt|Ja|WNS borttagna meddelanden|Antal|Totalt|Meddelandet utelämnades eftersom ChannelURI i registreringen är begränsat (X-WNS-NotificationStatus: släppt men inte X-WNS-DeviceConnectionStatus: frånkopplat).|Inga dimensioner|
|utgående. WNS. expiredchannel|Ja|WNS utgången kanal fel|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom ChannelURI har upphört att gälla (WNS-status: 410 borta).|Inga dimensioner|
|utgående. WNS. invalidcredentials|Ja|WNS-auktoriseringsfel (ogiltiga autentiseringsuppgifter)|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom PNS inte accepterade de angivna autentiseringsuppgifterna, eller om autentiseringsuppgifterna blockeras. (Windows Live känner inte igen autentiseringsuppgifterna).|Inga dimensioner|
|utgående. WNS. invalidnotificationformat|Ja|WNS ogiltigt meddelande format|Antal|Totalt|Meddelandets format är ogiltigt (WNS-status: 400). Observera att WNS inte avvisar alla ogiltiga nytto laster.|Inga dimensioner|
|utgående. WNS. invalidnotificationsize|Ja|WNS ogiltig meddelande storleks fel|Antal|Totalt|Meddelande nytto lasten är för stor (WNS-status: 413).|Inga dimensioner|
|utgående. WNS. invalidtoken|Ja|WNS-auktoriseringsfel (ogiltig token)|Antal|Totalt|Den token som angetts för WNS är inte giltig (WNS-status: 401 otillåten).|Inga dimensioner|
|utgående. WNS. pnserror|Ja|WNS-fel|Antal|Totalt|Meddelandet levererades inte på grund av fel vid kommunikation med WNS.|Inga dimensioner|
|utgående. WNS. lyckades|Ja|WNS slutförda meddelanden|Antal|Totalt|Antalet lyckade aviseringar.|Inga dimensioner|
|utgående. WNS. begränsad|Ja|WNS-begränsade meddelanden|Antal|Totalt|Antal push-meddelanden som misslyckades eftersom WNS begränsar den här appen (WNS-status: 406 är inte acceptabel).|Inga dimensioner|
|utgående. WNS. tokenproviderunreachable|Ja|WNS-auktoriseringsfel (kan inte kontaktas)|Antal|Totalt|Windows Live är inte tillgängligt.|Inga dimensioner|
|utgående. WNS. wrongtoken|Ja|WNS-auktoriseringsfel (fel token)|Antal|Totalt|Den token som angetts för WNS är giltig men för ett annat program (WNS-status: 403 förbjuden). Detta kan inträffa om ChannelURI i registreringen är kopplad till en annan app. Kontrol lera att klient programmet är associerat med samma app vars autentiseringsuppgifter finns i Notification Hub.|Inga dimensioner|
|registrering. alla|Ja|Registrerings åtgärder|Antal|Totalt|Antalet lyckade registrerings åtgärder (skapar uppdateringar av frågor och borttagningar). |Inga dimensioner|
|registrering. skapa|Ja|Skapa åtgärder för registrering|Antal|Totalt|Antalet lyckade registreringar som skapas.|Inga dimensioner|
|registrering. Delete|Ja|Åtgärder för att ta bort registrering|Antal|Totalt|Antalet lyckade registrerings borttagningar.|Inga dimensioner|
|registrering. Hämta|Ja|Läs åtgärder för registrering|Antal|Totalt|Antalet lyckade registrerings frågor.|Inga dimensioner|
|registrering. Update|Ja|Registrera uppdaterings åtgärder|Antal|Totalt|Antalet lyckade registrerings uppdateringar.|Inga dimensioner|
|schemalagt. väntar|Ja|Väntande schemalagda meddelanden|Antal|Totalt|Väntande schemalagda meddelanden|Inga dimensioner|


## <a name="microsoftoperationalinsightsworkspaces"></a>Microsoft. OperationalInsights/arbets ytor

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Average_% tillgängligt minne|Ja|Tillgängligt minne i procent|Antal|Genomsnitt|Average_% tillgängligt minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% tillgängligt växlings utrymme|Ja|Tillgängligt växlings utrymme i procent|Antal|Genomsnitt|Average_% tillgängligt växlings utrymme|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% allokerade byte som används|Ja|% Allokerade byte som används|Antal|Genomsnitt|Average_% allokerade byte som används|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% DPC-tid|Ja|DPC-tid i procent|Antal|Genomsnitt|Average_% DPC-tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ kostnads fri noder i procent|Ja|Kostnads fri noder i procent|Antal|Genomsnitt|Average_ kostnads fri noder i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ ledigt utrymme i procent|Ja|Ledigt utrymme i procent|Antal|Genomsnitt|Average_ ledigt utrymme i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ ledig tid i procent|Ja|Inaktivitetstid i procent|Antal|Genomsnitt|Average_ ledig tid i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Avbrotts tid i procent för Average_%|Ja|% Avbrotts tid|Antal|Genomsnitt|Avbrotts tid i procent för Average_%|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% i/o-vänte tid|Ja|% I/o-vänte tid|Antal|Genomsnitt|Average_% i/o-vänte tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% bra tid|Ja|% Trevligt tid|Antal|Genomsnitt|Average_% bra tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ privilegie rad tid i procent|Ja|Privilegie rad tid i procent|Antal|Genomsnitt|Average_ privilegie rad tid i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% processor tid|Ja|% processortid|Antal|Genomsnitt|Average_% processor tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% Använd noder i procent|Ja|% Använda noder i procent|Antal|Genomsnitt|Average_% Använd noder i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% använt minne|Ja|Använt minne i procent|Antal|Genomsnitt|Average_% använt minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ använt utrymme i procent|Ja|Använt utrymme i procent|Antal|Genomsnitt|Average_ använt utrymme i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_ använt växlings utrymme i procent|Ja|Använt växlings utrymme i procent|Antal|Genomsnitt|Average_ använt växlings utrymme i procent|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_% användar tid|Ja|Användar tid i procent|Antal|Genomsnitt|Average_% användar tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available megabyte|Ja|Tillgängliga megabyte|Antal|Genomsnitt|Average_Available megabyte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Available MB minne|Ja|Tillgängligt minne i megabyte|Antal|Genomsnitt|Average_Available MB minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Växling vid Average_Available megabyte|Ja|Tillgängliga megabyte växlings utrymme|Antal|Genomsnitt|Växling vid Average_Available megabyte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk s/läsning|Ja|Medel s/disk läsning|Antal|Genomsnitt|Average_Avg. Disk s/läsning|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk s/överföring|Ja|Medel s/disk överföring|Antal|Genomsnitt|Average_Avg. Disk s/överföring|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Avg. Disk s/skrivning|Ja|Medel s/disk skrivning|Antal|Genomsnitt|Average_Avg. Disk s/skrivning|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes mottagna/SEK|Ja|Mottagna byte/SEK|Antal|Genomsnitt|Average_Bytes mottagna/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Skickade Average_Bytes per sekund|Ja|Skickade byte/SEK|Antal|Genomsnitt|Skickade Average_Bytes per sekund|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Bytes totalt/SEK|Ja|Totalt antal byte/s|Antal|Genomsnitt|Average_Bytes totalt/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Current diskkölängd|Ja|Aktuell diskkölängd|Antal|Genomsnitt|Average_Current diskkölängd|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk lästa byte/s|Ja|Disk-lästa byte/s|Antal|Genomsnitt|Average_Disk lästa byte/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk läsningar/s|Ja|Disk läsningar/SEK|Antal|Genomsnitt|Average_Disk läsningar/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk överföringar/SEK|Ja|Disk överföringar/SEK|Antal|Genomsnitt|Average_Disk överföringar/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk skrivna byte/s|Ja|Disk-skrivna byte/s|Antal|Genomsnitt|Average_Disk skrivna byte/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Disk skrivningar/SEK|Ja|Disk skrivningar/SEK|Antal|Genomsnitt|Average_Disk skrivningar/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free megabyte|Ja|Lediga megabyte|Antal|Genomsnitt|Average_Free megabyte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free fysiskt minne|Ja|Ledigt fysiskt minne|Antal|Genomsnitt|Average_Free fysiskt minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free utrymme i växlingsfiler|Ja|Ledigt utrymme i växlingsfiler|Antal|Genomsnitt|Average_Free utrymme i växlingsfiler|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Free virtuellt minne|Ja|Ledigt virtuellt minne|Antal|Genomsnitt|Average_Free virtuellt minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Logical Disk-byte/s|Ja|Logisk disk byte/SEK|Antal|Genomsnitt|Average_Logical Disk-byte/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page läsningar/s|Ja|Sid läsningar/s|Antal|Genomsnitt|Average_Page läsningar/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Page skrivningar/SEK|Ja|Sid skrivningar/SEK|Antal|Genomsnitt|Average_Page skrivningar/SEK|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pages per sekund|Ja|Sidor/s|Antal|Genomsnitt|Average_Pages per sekund|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct privilegie rad tid|Ja|PCT privilegie rad tid|Antal|Genomsnitt|Average_Pct privilegie rad tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Pct användar tid|Ja|PCT-användar tid|Antal|Genomsnitt|Average_Pct användar tid|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Physical Disk-byte/s|Ja|Fysisk disk-byte/SEK|Antal|Genomsnitt|Average_Physical Disk-byte/s|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processes|Ja|Processer|Antal|Genomsnitt|Average_Processes|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Processor Kölängd|Ja|Kölängd för processor|Antal|Genomsnitt|Average_Processor Kölängd|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Size lagras i växlingsfiler|Ja|Storlek lagrad i växlingsfiler|Antal|Genomsnitt|Average_Size lagras i växlingsfiler|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total byte|Ja|Totalt antal byte|Antal|Genomsnitt|Average_Total byte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total mottagna byte|Ja|Totalt antal mottagna byte|Antal|Genomsnitt|Average_Total mottagna byte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Skickade Average_Total byte|Ja|Totalt antal överförda byte|Antal|Genomsnitt|Skickade Average_Total byte|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total kollisioner|Ja|Totalt antal kollisioner|Antal|Genomsnitt|Average_Total kollisioner|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total mottagna paket|Ja|Totalt antal mottagna paket|Antal|Genomsnitt|Average_Total mottagna paket|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Skickade Average_Total paket|Ja|Totalt antal överförda paket|Antal|Genomsnitt|Skickade Average_Total paket|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total RX-fel|Ja|Totalt antal mottagna mottagnings fel|Antal|Genomsnitt|Average_Total RX-fel|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Total TX-fel|Ja|Totalt antal TX-fel|Antal|Genomsnitt|Average_Total TX-fel|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Uptime|Ja|Drifttid|Antal|Genomsnitt|Average_Uptime|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used megabyte växlings utrymme|Ja|Använt megabyte växlings utrymme|Antal|Genomsnitt|Average_Used megabyte växlings utrymme|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used minne i KB|Ja|Använt minne i KB|Antal|Genomsnitt|Average_Used minne i KB|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Used minne i MB|Ja|Använt minne i MB|Antal|Genomsnitt|Average_Used minne i MB|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Users|Ja|Användare|Antal|Genomsnitt|Average_Users|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Average_Virtual delat minne|Ja|Virtuellt delat minne|Antal|Genomsnitt|Average_Virtual delat minne|Dator, ObjectName, InstanceName, CounterPath, SourceSystem|
|Händelse|Ja|Händelse|Antal|Genomsnitt|Händelse|Källa, EventLog, dator, EventCategory, EventLevel, EventLevelName, EventID|
|Pulsslag|Ja|Pulsslag|Antal|Totalt|Pulsslag|Dator, OSType, version, SourceComputerId|
|Uppdatera|Ja|Uppdatera|Antal|Genomsnitt|Uppdatera|Dator, produkt, klassificering, UpdateState, valfri, godkänd|


## <a name="microsoftpeeringpeerings"></a>Microsoft. peering/peering

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|EgressTrafficRate|Ja|Utgående trafik hastighet|BitsPerSecond|Genomsnitt|Utgående trafik hastighet i bitar per sekund|ConnectionId, SessionIp, TrafficClass|
|IngressTrafficRate|Ja|Inkommande trafik hastighet|BitsPerSecond|Genomsnitt|Inkommande trafik hastighet i bitar per sekund|ConnectionId, SessionIp, TrafficClass|


## <a name="microsoftpeeringpeeringservices"></a>Microsoft. peering/peeringServices

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|PrefixLatency|Ja|Prefix svars tid|Millisekunder|Genomsnitt|Prefixlängd för median|PrefixName|


## <a name="microsoftpowerbidedicatedcapacities"></a>Microsoft. PowerBIDedicated/kapacitet

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|memory_metric|Ja|Minne|Byte|Genomsnitt|Memory. Intervallet 0-3 GB för a1, 0-5 GB för a2, 0-10 GB för A3, 0-25 GB för A4, 0-50 GB för A5 och 0-100 GB för A6|Inga dimensioner|
|memory_thrashing_metric|Ja|Nedskräpning för minne (data uppsättningar)|Procent|Genomsnitt|Genomsnittligt minne nedskräpning.|Inga dimensioner|
|qpu_high_utilization_metric|Ja|Hög användning av QPU|Antal|Totalt|QPU hög användning under den senaste minuten, 1 för hög QPU användning, annars 0|Inga dimensioner|
|QueryDuration|Ja|Frågans varaktighet (data uppsättningar)|Millisekunder|Genomsnitt|DAX-frågans varaktighet i det sista intervallet|Inga dimensioner|
|QueryPoolJobQueueLength|Ja|Kölängd för jobbkö (data uppsättningar)|Antal|Genomsnitt|Antal jobb i kön för trådpoolen för Query.|Inga dimensioner|


## <a name="microsoftrelaynamespaces"></a>Microsoft. Relay/namnrymder

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|ActiveConnections|Inga|ActiveConnections|Antal|Totalt|Totalt ActiveConnections för Microsoft. Relay.|Entitetsnamnet|
|ActiveListeners|Inga|ActiveListeners|Antal|Totalt|Totalt ActiveListeners för Microsoft. Relay.|Entitetsnamnet|
|BytesTransferred|Ja|BytesTransferred|Byte|Totalt|Totalt BytesTransferred för Microsoft. Relay.|Entitetsnamnet|
|ListenerConnections-ClientError|Inga|ListenerConnections-ClientError|Antal|Totalt|ClientError på ListenerConnections för Microsoft. Relay.|Entitetsnamnet |
|ListenerConnections-ServerError|Inga|ListenerConnections-ServerError|Antal|Totalt|ServerError på ListenerConnections för Microsoft. Relay.|Entitetsnamnet |
|ListenerConnections-Success|Inga|ListenerConnections-Success|Antal|Totalt|Lyckade ListenerConnections för Microsoft. Relay.|Entitetsnamnet |
|ListenerConnections-TotalRequests|Inga|ListenerConnections-TotalRequests|Antal|Totalt|Totalt ListenerConnections för Microsoft. Relay.|Entitetsnamnet|
|ListenerDisconnects|Inga|ListenerDisconnects|Antal|Totalt|Totalt ListenerDisconnects för Microsoft. Relay.|Entitetsnamnet|
|SenderConnections-ClientError|Inga|SenderConnections-ClientError|Antal|Totalt|ClientError på SenderConnections för Microsoft. Relay.|Entitetsnamnet |
|SenderConnections-ServerError|Inga|SenderConnections-ServerError|Antal|Totalt|ServerError på SenderConnections för Microsoft. Relay.|Entitetsnamnet |
|SenderConnections-Success|Inga|SenderConnections-Success|Antal|Totalt|Lyckade SenderConnections för Microsoft. Relay.|Entitetsnamnet |
|SenderConnections-TotalRequests|Inga|SenderConnections-TotalRequests|Antal|Totalt|Totalt antal SenderConnections-begäranden för Microsoft. Relay.|Entitetsnamnet|
|SenderDisconnects|Inga|SenderDisconnects|Antal|Totalt|Totalt SenderDisconnects för Microsoft. Relay.|Entitetsnamnet|


## <a name="microsoftsearchsearchservices"></a>Microsoft. search/searchServices

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|SearchLatency|Ja|Sök svars tid|Sekunder|Genomsnitt|Genomsnittlig Sök fördröjning för Sök tjänsten|Inga dimensioner|
|SearchQueriesPerSecond|Ja|Sök frågor per sekund|CountPerSecond|Genomsnitt|Sök efter frågor per sekund för Sök tjänsten|Inga dimensioner|
|ThrottledSearchQueriesPercentage|Ja|Begränsade Sök frågor i procent|Procent|Genomsnitt|Procent andel Sök frågor som har begränsats för Sök tjänsten|Inga dimensioner|


## <a name="microsoftservicebusnamespaces"></a>Microsoft. Service Bus/namnrymder

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|ActiveConnections|Inga|ActiveConnections|Antal|Totalt|Totalt antal aktiva anslutningar för Microsoft. Service Bus.|Inga dimensioner|
|ActiveMessages|Inga|Antal aktiva meddelanden i en kö/ett ämne.|Antal|Genomsnitt|Antal aktiva meddelanden i en kö/ett ämne.|Entitetsnamnet|
|ConnectionsClosed|Inga|Stängda anslutningar.|Antal|Genomsnitt|Stängda anslutningar för Microsoft. Service Bus.|Entitetsnamnet|
|ConnectionsOpened|Inga|Öppna anslutningar.|Antal|Genomsnitt|Anslutningar som öppnats för Microsoft. Service Bus.|Entitetsnamnet|
|CPUXNS|Inga|PROCESSOR (inaktuell)|Procent|Maximal|Mått för CPU-användning för Service Bus Premium-namnrymd. Det här måttet är inaktuell. Använd processor måttet (NamespaceCpuUsage) i stället.|Inga dimensioner|
|DeadletteredMessages|Inga|Antal meddelanden om obeställbara meddelanden i en kö/ett ämne.|Antal|Genomsnitt|Antal meddelanden om obeställbara meddelanden i en kö/ett ämne.|Entitetsnamnet|
|IncomingMessages|Ja|Inkommande meddelanden|Antal|Totalt|Inkommande meddelanden för Microsoft. Service Bus.|Entitetsnamnet|
|IncomingRequests|Ja|Inkommande förfrågningar|Antal|Totalt|Inkommande begär Anden för Microsoft. Service Bus.|Entitetsnamnet|
|Meddelanden|Inga|Antal meddelanden i kö/ämne.|Antal|Genomsnitt|Antal meddelanden i kö/ämne.|Entitetsnamnet|
|NamespaceCpuUsage|Inga|Processor|Procent|Maximal|Mått för CPU-användning för Service Bus Premium-namnrymd.|Inga dimensioner|
|NamespaceMemoryUsage|Inga|Minnesanvändning|Procent|Maximal|Minnes användnings mått för Service Bus Premium-namnrymd.|Inga dimensioner|
|OutgoingMessages|Ja|Utgående meddelanden|Antal|Totalt|Utgående meddelanden för Microsoft. Service Bus.|Entitetsnamnet|
|ScheduledMessages|Inga|Antal schemalagda meddelanden i en kö/ett ämne.|Antal|Genomsnitt|Antal schemalagda meddelanden i en kö/ett ämne.|Entitetsnamnet|
|ServerErrors|Inga|Serverfel.|Antal|Totalt|Server fel för Microsoft. Service Bus.|Entitetsnamnet |
|Storlek|Inga|Storlek|Byte|Genomsnitt|Storlek på en kö/ett ämne i byte.|Entitetsnamnet|
|SuccessfulRequests|Inga|Slutförda förfrågningar|Antal|Totalt|Totalt antal slutförda begär Anden för ett namn område|Entitetsnamnet |
|ThrottledRequests|Inga|Begränsade förfrågningar.|Antal|Totalt|Begränsade begär Anden för Microsoft. Service Bus.|Entitetsnamnet |
|UserErrors|Inga|Användarfel.|Antal|Totalt|Användar fel för Microsoft. Service Bus.|Entitetsnamnet |
|WSXNS|Inga|Minnes användning (inaktuell)|Procent|Maximal|Minnes användnings mått för Service Bus Premium-namnrymd. Måttet är föråldrat. Använd minnes användnings måttet (NamespaceMemoryUsage) i stället.|Inga dimensioner|


## <a name="microsoftservicefabricmeshapplications"></a>Microsoft. ServiceFabricMesh/program

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|ActualCpu|Inga|ActualCpu|Antal|Genomsnitt|Faktisk processor användning i Milli-kärnor|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ActualMemory|Inga|ActualMemory|Byte|Genomsnitt|Faktisk minnes användning i MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedCpu|Inga|AllocatedCpu|Antal|Genomsnitt|CPU allokerad till den här behållaren i Milli-kärnor|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|AllocatedMemory|Inga|AllocatedMemory|Byte|Genomsnitt|Minne som allokerats till den här behållaren i MB|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|ApplicationStatus|Inga|ApplicationStatus|Antal|Genomsnitt|Status för Service Fabric nätprogram|ApplicationName, status|
|Container status|Inga|Container status|Antal|Genomsnitt|Status för behållaren i Service Fabric nätprogram|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName, status|
|CpuUtilization|Inga|CpuUtilization|Procent|Genomsnitt|Användning av CPU för den här behållaren i procent av AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|MemoryUtilization|Inga|MemoryUtilization|Procent|Genomsnitt|Användning av CPU för den här behållaren i procent av AllocatedCpu|ApplicationName, ServiceName, CodePackageName, ServiceReplicaName|
|RestartCount|Inga|RestartCount|Antal|Genomsnitt|Antal omstarter för en behållare i Service Fabric nätprogram|ApplicationName, status, ServiceName, ServiceReplicaName, CodePackageName|
|ServiceReplicaStatus|Inga|ServiceReplicaStatus|Antal|Genomsnitt|Hälso status för en tjänst replik i Service Fabric nätprogram|ApplicationName, status, ServiceName, ServiceReplicaName|
|ServiceStatus|Inga|ServiceStatus|Antal|Genomsnitt|Hälso status för en tjänst i Service Fabric nätprogram|ApplicationName, status, ServiceName|


## <a name="microsoftsignalrservicesignalr"></a>Microsoft. SignalRService/SignalR

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|ConnectionCount|Ja|Antal anslutningar|Antal|Maximal|Mängden användar anslutning.|Slutpunkt|
|InboundTraffic|Ja|Inkommande trafik|Byte|Totalt|Inkommande trafik trafik för tjänsten|Inga dimensioner|
|MessageCount|Ja|Antal meddelanden|Antal|Totalt|Den totala mängden meddelanden.|Inga dimensioner|
|OutboundTraffic|Ja|Utgående trafik|Byte|Totalt|Utgående trafik för tjänsten|Inga dimensioner|
|SystemErrors|Ja|Systemfel|Procent|Maximal|Procent andelen system fel|Inga dimensioner|
|UserErrors|Ja|Användar fel|Procent|Maximal|Procent andel användar fel|Inga dimensioner|


## <a name="microsoftsqlmanagedinstances"></a>Microsoft. SQL/managedInstances

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|avg_cpu_percent|Ja|Genomsnittlig CPU-procent|Procent|Genomsnitt|Genomsnittlig CPU-procent|Inga dimensioner|
|io_bytes_read|Ja|Lästa IO-byte|Byte|Genomsnitt|Lästa IO-byte|Inga dimensioner|
|io_bytes_written|Ja|Skrivna IO-byte|Byte|Genomsnitt|Skrivna IO-byte|Inga dimensioner|
|io_requests|Ja|Antal IO-begäranden|Antal|Genomsnitt|Antal IO-begäranden|Inga dimensioner|
|reserved_storage_mb|Ja|Reserverat lagrings utrymme|Antal|Genomsnitt|Reserverat lagrings utrymme|Inga dimensioner|
|storage_space_used_mb|Ja|Använt lagrings utrymme|Antal|Genomsnitt|Använt lagrings utrymme|Inga dimensioner|
|virtual_core_count|Ja|Antal virtuella kärnor|Antal|Genomsnitt|Antal virtuella kärnor|Inga dimensioner|


## <a name="microsoftsqlserversdatabases"></a>Microsoft. SQL/Servers/databaser

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|active_queries|Ja|Aktiva frågor|Antal|Totalt|Aktiva frågor för alla arbets belastnings grupper. Gäller endast för data lager.|Inga dimensioner|
|allocated_data_storage|Ja|Allokerat data utrymme|Byte|Genomsnitt|Allokerad data lagring. Ej tillämpligt för data lager.|Inga dimensioner|
|app_cpu_billed|Ja|App-CPU fakturerad|Antal|Totalt|App-CPU fakturerad. Gäller databaser utan server.|Inga dimensioner|
|app_cpu_percent|Ja|CPU-procent för app|Procent|Genomsnitt|CPU-procent för app. Gäller databaser utan server.|Inga dimensioner|
|app_memory_percent|Ja|Minnes procent för appar|Procent|Genomsnitt|Minnes procent för appar. Gäller databaser utan server.|Inga dimensioner|
|base_blob_size_bytes|Ja|Bas för Blob Storage-storlek|Byte|Maximal|Bas för Blob Storage-storlek. Gäller för storskaliga databaser.|Inga dimensioner|
|blocked_by_firewall|Ja|Blockerad av brandvägg|Antal|Totalt|Blockerad av brandvägg|Inga dimensioner|
|cache_hit_percent|Ja|Procent andel cacheträffar|Procent|Maximal|Procent av cacheträffar. Gäller endast för data lager.|Inga dimensioner|
|cache_used_percent|Ja|Procent andel som används|Procent|Maximal|Procent andel som används. Gäller endast för data lager.|Inga dimensioner|
|connection_failed|Ja|Misslyckade anslutningar|Antal|Totalt|Misslyckade anslutningar|Inga dimensioner|
|connection_successful|Ja|Lyckade anslutningar|Antal|Totalt|Lyckade anslutningar|Inga dimensioner|
|cpu_limit|Ja|PROCESSOR gräns|Antal|Genomsnitt|PROCESSOR gräns. Gäller för vCore-baserade databaser.|Inga dimensioner|
|cpu_percent|Ja|CPU-procent|Procent|Genomsnitt|CPU-procent|Inga dimensioner|
|cpu_used|Ja|Använd CPU|Antal|Genomsnitt|Använd CPU. Gäller för vCore-baserade databaser.|Inga dimensioner|
|hamn|Ja|Dödlägen|Antal|Totalt|Låsningar. Ej tillämpligt för data lager.|Inga dimensioner|
|diff_backup_size_bytes|Ja|Lagrings storlek för differentiell säkerhets kopia|Byte|Maximal|Kumulativ lagrings storlek för differentiell säkerhets kopia. Gäller för vCore-baserade databaser. Gäller inte för storskaliga databaser.|Inga dimensioner|
|dtu_consumption_percent|Ja|DTU-procent|Procent|Genomsnitt|DTU-procent. Gäller för DTU-baserade databaser.|Inga dimensioner|
|dtu_limit|Ja|DTU-gräns|Antal|Genomsnitt|DTU-gräns. Gäller för DTU-baserade databaser.|Inga dimensioner|
|dtu_used|Ja|Använt DTU|Antal|Genomsnitt|DTU används. Gäller för DTU-baserade databaser.|Inga dimensioner|
|dwu_consumption_percent|Ja|DWU procent|Procent|Maximal|DWU procent. Gäller endast för data lager.|Inga dimensioner|
|dwu_limit|Ja|DWU-gräns|Antal|Maximal|DWU-gräns. Gäller endast för data lager.|Inga dimensioner|
|dwu_used|Ja|DWU som används|Antal|Maximal|DWU som används. Gäller endast för data lager.|Inga dimensioner|
|full_backup_size_bytes|Ja|Lagrings storlek för fullständig säkerhets kopia|Byte|Maximal|Ackumulerad lagrings storlek för fullständig säkerhets kopia. Gäller för vCore-baserade databaser. Gäller inte för storskaliga databaser.|Inga dimensioner|
|local_tempdb_usage_percent|Ja|Lokal tempdb-procent|Procent|Genomsnitt|Lokal tempdb-procent. Gäller endast för data lager.|Inga dimensioner|
|log_backup_size_bytes|Ja|Logg lagrings storlek för logg säkerhets kopia|Byte|Maximal|Sammanlagd lagrings storlek för logg säkerhets kopia. Gäller för vCore-baserade och storskaliga databaser.|Inga dimensioner|
|log_write_percent|Ja|Logg IO-procent|Procent|Genomsnitt|Logg IO-procent. Ej tillämpligt för data lager.|Inga dimensioner|
|memory_usage_percent|Ja|Minnes procent|Procent|Maximal|Minnes procent. Gäller endast för data lager.|Inga dimensioner|
|physical_data_read_percent|Ja|Data IO-procent|Procent|Genomsnitt|Data IO-procent|Inga dimensioner|
|queued_queries|Ja|Köade frågor|Antal|Totalt|Köade frågor över alla arbets belastnings grupper. Gäller endast för data lager.|Inga dimensioner|
|sessions_percent|Ja|Sessioner i procent|Procent|Genomsnitt|Sessioner i procent. Ej tillämpligt för data lager.|Inga dimensioner|
|snapshot_backup_size_bytes|Ja|Lagrings storlek för ögonblicks bild säkerhets kopia|Byte|Maximal|Lagrings storlek för kumulativ ögonblicks bild. Gäller för storskaliga databaser.|Inga dimensioner|
|sqlserver_process_core_percent|Ja|SQL Server process kärn procent|Procent|Maximal|CPU-användning som en procent andel av SQL DB-processen. Ej tillämpligt för data lager.|Inga dimensioner|
|sqlserver_process_memory_percent|Ja|SQL Server process minne i procent|Procent|Maximal|Minnes användning som en procent andel av SQL DB-processen. Ej tillämpligt för data lager.|Inga dimensioner|
|storage|Ja|Använt data utrymme|Byte|Maximal|Använt data utrymme. Ej tillämpligt för data lager.|Inga dimensioner|
|storage_percent|Ja|Använt data utrymme i procent|Procent|Maximal|Data utrymme som används i procent. Ej tillämpligt för data lager eller storskaliga databaser.|Inga dimensioner|
|tempdb_data_size|Ja|Data fil storlek i tempdb i KB|Antal|Maximal|Utrymme som används i tempdb-datafiler i kilobyte. Ej tillämpligt för data lager.|Inga dimensioner|
|tempdb_log_size|Ja|TempDB-logg fils storlek kilobyte|Antal|Maximal|Utrymme som används i tempdb-transaktionshanteraren i kilobyte. Ej tillämpligt för data lager.|Inga dimensioner|
|tempdb_log_used_percent|Ja|Procent använt tempdb-logg|Procent|Maximal|Utrymme som används i procent i tempdb-transaktionshanteraren. logg filen. Ej tillämpligt för data lager.|Inga dimensioner|
|wlg_active_queries|Ja|Aktiva frågor för arbets belastnings grupp|Antal|Totalt|Aktiva frågor inom arbets belastnings gruppen. Gäller endast för data lager.|WorkloadGroupName, IsUserDefined|
|wlg_active_queries_timeouts|Ja|Fråge tids gränser för arbets belastnings grupp|Antal|Totalt|Frågor som har nått tids gränsen för arbets belastnings gruppen. Gäller endast för data lager.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_system_percent|Ja|Allokering av arbets belastnings grupp efter system procent|Procent|Maximal|Tilldelad procent andel resurser i förhållande till hela systemet per arbets belastnings grupp. Gäller endast för data lager.|WorkloadGroupName, IsUserDefined|
|wlg_allocation_relative_to_wlg_effective_cap_percent|Ja|Allokering av arbets belastnings grupp per Cap-resurs procent|Procent|Maximal|Tilldelad procent andel resurser i förhållande till de angivna Cap-resurserna per arbets belastnings grupp. Gäller endast för data lager.|WorkloadGroupName, IsUserDefined|
|wlg_effective_cap_resource_percent|Ja|Effektiv tak resurs procent|Procent|Maximal|En hård gräns för procent andelen av resurser som tillåts för arbets belastnings gruppen, med hänsyn till den lägsta resurs procent som allokerats för andra arbets belastnings grupper. Gäller endast för data lager.|WorkloadGroupName, IsUserDefined|
|wlg_effective_min_resource_percent|Ja|Effektiv minsta resurs procent|Procent|Maximal|Lägsta procent andel av resurser som är reserverade och isolerade för arbets belastnings gruppen, med hänsyn till den lägsta Service nivån. Gäller endast för data lager.|WorkloadGroupName, IsUserDefined|
|wlg_queued_queries|Ja|Köade frågor i arbets belastnings gruppen|Antal|Totalt|Köade frågor i arbets belastnings gruppen. Gäller endast för data lager.|WorkloadGroupName, IsUserDefined|
|workers_percent|Ja|Arbetare i procent|Procent|Genomsnitt|Arbetare i procent. Ej tillämpligt för data lager.|Inga dimensioner|
|xtp_storage_percent|Ja|In-Memory OLTP-lagring i procent|Procent|Genomsnitt|In-Memory OLTP-lagring i procent. Ej tillämpligt för data lager.|Inga dimensioner|


## <a name="microsoftsqlserverselasticpools"></a>Microsoft. SQL/Servers/elasticPools

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|allocated_data_storage|Ja|Allokerat data utrymme|Byte|Genomsnitt|Allokerat data utrymme|Inga dimensioner|
|allocated_data_storage_percent|Ja|Allokerat data utrymme i procent|Procent|Maximal|Allokerat data utrymme i procent|Inga dimensioner|
|cpu_limit|Ja|PROCESSOR gräns|Antal|Genomsnitt|PROCESSOR gräns. Gäller vCore-baserade elastiska pooler.|Inga dimensioner|
|cpu_percent|Ja|CPU-procent|Procent|Genomsnitt|CPU-procent|Inga dimensioner|
|cpu_used|Ja|Använd CPU|Antal|Genomsnitt|Använd CPU. Gäller vCore-baserade elastiska pooler.|Inga dimensioner|
|database_allocated_data_storage|Inga|Allokerat data utrymme|Byte|Genomsnitt|Allokerat data utrymme|DatabaseResourceId|
|database_cpu_limit|Inga|PROCESSOR gräns|Antal|Genomsnitt|PROCESSOR gräns|DatabaseResourceId|
|database_cpu_percent|Inga|CPU-procent|Procent|Genomsnitt|CPU-procent|DatabaseResourceId|
|database_cpu_used|Inga|Använd CPU|Antal|Genomsnitt|Använd CPU|DatabaseResourceId|
|database_dtu_consumption_percent|Inga|DTU-procent|Procent|Genomsnitt|DTU-procent|DatabaseResourceId|
|database_eDTU_used|Inga|eDTU använt|Antal|Genomsnitt|eDTU använt|DatabaseResourceId|
|database_log_write_percent|Inga|Logg IO-procent|Procent|Genomsnitt|Logg IO-procent|DatabaseResourceId|
|database_physical_data_read_percent|Inga|Data IO-procent|Procent|Genomsnitt|Data IO-procent|DatabaseResourceId|
|database_sessions_percent|Inga|Sessioner i procent|Procent|Genomsnitt|Sessioner i procent|DatabaseResourceId|
|database_storage_used|Inga|Använt data utrymme|Byte|Genomsnitt|Använt data utrymme|DatabaseResourceId|
|database_workers_percent|Inga|Arbetare i procent|Procent|Genomsnitt|Arbetare i procent|DatabaseResourceId|
|dtu_consumption_percent|Ja|DTU-procent|Procent|Genomsnitt|DTU-procent. Gäller för DTU-baserade elastiska pooler.|Inga dimensioner|
|eDTU_limit|Ja|eDTU-gräns|Antal|Genomsnitt|eDTU-gräns. Gäller för DTU-baserade elastiska pooler.|Inga dimensioner|
|eDTU_used|Ja|eDTU använt|Antal|Genomsnitt|eDTU används. Gäller för DTU-baserade elastiska pooler.|Inga dimensioner|
|log_write_percent|Ja|Logg IO-procent|Procent|Genomsnitt|Logg IO-procent|Inga dimensioner|
|physical_data_read_percent|Ja|Data IO-procent|Procent|Genomsnitt|Data IO-procent|Inga dimensioner|
|sessions_percent|Ja|Sessioner i procent|Procent|Genomsnitt|Sessioner i procent|Inga dimensioner|
|sqlserver_process_core_percent|Ja|SQL Server process kärn procent|Procent|Maximal|CPU-användning som en procent andel av SQL DB-processen. Gäller elastiska pooler.|Inga dimensioner|
|sqlserver_process_memory_percent|Ja|SQL Server process minne i procent|Procent|Maximal|Minnes användning som en procent andel av SQL DB-processen. Gäller elastiska pooler.|Inga dimensioner|
|storage_limit|Ja|Maximal datastorlek|Byte|Genomsnitt|Maximal datastorlek|Inga dimensioner|
|storage_percent|Ja|Använt data utrymme i procent|Procent|Genomsnitt|Använt data utrymme i procent|Inga dimensioner|
|storage_used|Ja|Använt data utrymme|Byte|Genomsnitt|Använt data utrymme|Inga dimensioner|
|tempdb_data_size|Ja|Data fil storlek i tempdb i KB|Antal|Maximal|Utrymme som används i tempdb-datafiler i kilobyte.|Inga dimensioner|
|tempdb_log_size|Ja|TempDB-logg fils storlek kilobyte|Antal|Maximal|Utrymme som används i tempdb-transaktionshanteraren i kilobyte.|Inga dimensioner|
|tempdb_log_used_percent|Ja|Procent använt tempdb-logg|Procent|Maximal|Procent andel utrymme som används i tempdb-transaktionsloggen logg filen|Inga dimensioner|
|workers_percent|Ja|Arbetare i procent|Procent|Genomsnitt|Arbetare i procent|Inga dimensioner|
|xtp_storage_percent|Ja|In-Memory OLTP-lagring i procent|Procent|Genomsnitt|In-Memory OLTP-lagring i procent|Inga dimensioner|


## <a name="microsoftstoragestorageaccounts"></a>Microsoft. Storage/storageAccounts

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Ja|Tillgänglighet|Procent|Genomsnitt|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|
|Utgående|Ja|Utgående|Byte|Totalt|Mängden utgående data. Det här antalet inkluderar utgående data från den externa klienten från Azure Storage och utgående i Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|Ingress|Ja|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Ja|Lyckad E2E-svarstid|Millisekunder|Genomsnitt|Genomsnittlig svars tid från slut punkt till slut punkt för lyckade begär Anden som gjorts till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Ja|Lyckad Server svars tid|Millisekunder|Genomsnitt|Den genomsnittliga tiden det tar för Azure Storage att bearbeta en lyckad begäran. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ av typ, ApiName, autentisering|
|Transaktioner|Ja|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|
|UsedCapacity|Inga|Använd kapacitet|Byte|Genomsnitt|Mängden lagrings utrymme som används av lagrings kontot. För standardlagringskonton är det summan av den kapacitet som används av blobar, filer och köer. För Premium Storage-konton och Blob Storage-konton är det samma som BlobCapacity eller FileCapacity.|Inga dimensioner|


## <a name="microsoftstoragestorageaccountsblobservices"></a>Microsoft. Storage/storageAccounts/blobServices

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Ja|Tillgänglighet|Procent|Genomsnitt|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|
|BlobCapacity|Inga|BLOB-kapacitet|Byte|Genomsnitt|Mängden lagrings utrymme som används av lagrings kontots Blob Service i byte.|BlobType,-nivå|
|BlobCount|Inga|BLOB-antal|Antal|Genomsnitt|Antalet BLOB-objekt som lagras i lagrings kontot.|BlobType,-nivå|
|BlobProvisionedSize|Inga|BLOB-allokerad storlek|Byte|Genomsnitt|Mängden lagrings utrymme som har allokerats i lagrings kontots Blob Service i byte.|BlobType,-nivå|
|ContainerCount|Ja|Antal BLOB-behållare|Antal|Genomsnitt|Antalet behållare i lagrings kontot.|Inga dimensioner|
|Utgående|Ja|Utgående|Byte|Totalt|Mängden utgående data. Det här antalet inkluderar utgående data från den externa klienten från Azure Storage och utgående i Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|IndexCapacity|Inga|Index kapacitet|Byte|Genomsnitt|Mängden lagrings utrymme som används av Azure Data Lake Storage Gen2 hierarkiskt index.|Inga dimensioner|
|Ingress|Ja|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Ja|Lyckad E2E-svarstid|Millisekunder|Genomsnitt|Genomsnittlig svars tid från slut punkt till slut punkt för lyckade begär Anden som gjorts till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Ja|Lyckad Server svars tid|Millisekunder|Genomsnitt|Den genomsnittliga tiden det tar för Azure Storage att bearbeta en lyckad begäran. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ av typ, ApiName, autentisering|
|Transaktioner|Ja|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|


## <a name="microsoftstoragestorageaccountsfileservices"></a>Microsoft. Storage/storageAccounts/fileServices

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Ja|Tillgänglighet|Procent|Genomsnitt|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ, ApiName, autentisering, FileShare|
|Utgående|Ja|Utgående|Byte|Totalt|Mängden utgående data. Det här antalet inkluderar utgående data från den externa klienten från Azure Storage och utgående i Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ, ApiName, autentisering, FileShare|
|FileCapacity|Inga|Fil kapacitet|Byte|Genomsnitt|Mängden fil lagring som används av lagrings kontot.|FileShare|
|FileCount|Inga|Antal filer|Antal|Genomsnitt|Antalet filer i lagrings kontot.|FileShare|
|FileShareCapacityQuota|Inga|Kapacitets kvot för fil resurs|Byte|Genomsnitt|Den övre gränsen för mängden lagrings utrymme som kan användas av Azure Files tjänsten i byte.|FileShare|
|FileShareCount|Inga|Antal fil resurser|Antal|Genomsnitt|Antalet fil resurser i lagrings kontot.|Inga dimensioner|
|FileShareProvisionedIOPS|Inga|Allokerad IOPS för fil resurs|Byte|Genomsnitt|Bas linje numret för etablerade IOPS för Premium-filresursen i Premium-filernas lagrings konto. Antalet beräknas baserat på resurs kapacitetens tilldelade storlek (kvot).|FileShare|
|FileShareSnapshotCount|Inga|Antal ögonblicks bilder av fil resurs|Antal|Genomsnitt|Antal ögonblicks bilder som finns på resursen i lagrings kontots fil tjänst.|FileShare|
|FileShareSnapshotSize|Inga|Storlek på fil resursens ögonblicks bild|Byte|Genomsnitt|Mängden lagrings utrymme som används av ögonblicks bilderna i lagrings kontots fil tjänst i byte.|FileShare|
|Ingress|Ja|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ, ApiName, autentisering, FileShare|
|SuccessE2ELatency|Ja|Lyckad E2E-svarstid|Millisekunder|Genomsnitt|Genomsnittlig svars tid från slut punkt till slut punkt för lyckade begär Anden som gjorts till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ, ApiName, autentisering, FileShare|
|SuccessServerLatency|Ja|Lyckad Server svars tid|Millisekunder|Genomsnitt|Den genomsnittliga tiden det tar för Azure Storage att bearbeta en lyckad begäran. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ, ApiName, autentisering, FileShare|
|Transaktioner|Ja|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering, FileShare|


## <a name="microsoftstoragestorageaccountsqueueservices"></a>Microsoft. Storage/storageAccounts/queueServices

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Ja|Tillgänglighet|Procent|Genomsnitt|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|
|Utgående|Ja|Utgående|Byte|Totalt|Mängden utgående data. Det här antalet inkluderar utgående data från den externa klienten från Azure Storage och utgående i Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|Ingress|Ja|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|QueueCapacity|Ja|Kös Kap kap.|Byte|Genomsnitt|Mängden Queue Storage som används av lagrings kontot.|Inga dimensioner|
|QueueCount|Ja|Antal köer|Antal|Genomsnitt|Antalet köer i lagrings kontot.|Inga dimensioner|
|QueueMessageCount|Ja|Antal meddelanden i kö|Antal|Genomsnitt|Antalet meddelanden som inte har förfallit i lagrings kontot.|Inga dimensioner|
|SuccessE2ELatency|Ja|Lyckad E2E-svarstid|Millisekunder|Genomsnitt|Genomsnittlig svars tid från slut punkt till slut punkt för lyckade begär Anden som gjorts till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Ja|Lyckad Server svars tid|Millisekunder|Genomsnitt|Den genomsnittliga tiden det tar för Azure Storage att bearbeta en lyckad begäran. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ av typ, ApiName, autentisering|
|Transaktioner|Ja|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|


## <a name="microsoftstoragestorageaccountstableservices"></a>Microsoft. Storage/storageAccounts/tableServices

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Tillgänglighet|Ja|Tillgänglighet|Procent|Genomsnitt|Procent andelen tillgänglighet för lagrings tjänsten eller den angivna API-åtgärden. Tillgängligheten beräknas genom att värdet för TotalBillableRequests delas med antalet tillämpliga förfrågningar, även sådana som genererat oväntade fel. Alla oväntade fel leder till minskad tillgänglighet för lagringstjänsten eller den angivna API-åtgärden.|Typ av typ, ApiName, autentisering|
|Utgående|Ja|Utgående|Byte|Totalt|Mängden utgående data. Det här antalet inkluderar utgående data från den externa klienten från Azure Storage och utgående i Azure. Därför motsvarar inte det här värdet fakturerbara utgående data.|Typ av typ, ApiName, autentisering|
|Ingress|Ja|Ingress|Byte|Totalt|Mängden inkommande data, i byte. Det här värdet innefattar inkommande data från en extern klient till Azure Storage samt inkommande data inom Azure.|Typ av typ, ApiName, autentisering|
|SuccessE2ELatency|Ja|Lyckad E2E-svarstid|Millisekunder|Genomsnitt|Genomsnittlig svars tid från slut punkt till slut punkt för lyckade begär Anden som gjorts till en lagrings tjänst eller angiven API-åtgärd i millisekunder. Värdet innefattar bearbetningstiden som krävs i Azure Storage för att läsa begäran, skicka svaret och ta emot en bekräftelse av svaret.|Typ av typ, ApiName, autentisering|
|SuccessServerLatency|Ja|Lyckad Server svars tid|Millisekunder|Genomsnitt|Den genomsnittliga tiden det tar för Azure Storage att bearbeta en lyckad begäran. Det här värdet innefattar inte nätverksfördröjningen som anges i SuccessE2ELatency.|Typ av typ, ApiName, autentisering|
|TableCapacity|Ja|Tabell kapacitet|Byte|Genomsnitt|Mängden tabell lagring som används av lagrings kontot.|Inga dimensioner|
|TableCount|Ja|Antal tabeller|Antal|Genomsnitt|Antalet tabeller i lagrings kontot.|Inga dimensioner|
|TableEntityCount|Ja|Antal tabell enheter|Antal|Genomsnitt|Antalet tabell enheter i lagrings kontot.|Inga dimensioner|
|Transaktioner|Ja|Transaktioner|Antal|Totalt|Antalet begäranden som görs till en lagringstjänst eller för den angivna API-åtgärden. I det här värdet räknas både lyckade och misslyckade förfrågningar samt förfrågningar som genererat fel. Använd ResponseType dimension för antalet olika typer av svar.|ResponseType, typ av typ, ApiName, autentisering|


## <a name="microsoftstoragesyncstoragesyncservices"></a>Microsoft. storagesync/storageSyncServices

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|ServerSyncSessionResult|Ja|Resultat av Sync-session|Antal|Genomsnitt|Mått som loggar värdet 1 varje gång Server slut punkten slutför en Sync-session med moln slut punkten|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncBatchTransferredFileBytes|Ja|Byte som har synkroniserats|Byte|Totalt|Total fil storlek överförd för Sync-sessioner|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncRecalledNetworkBytesByApplication|Ja|Återställnings storlek för moln skikt per program|Byte|Totalt|Storlek på data som återkallas av program|SyncGroupName, ServerName, ApplicationName|
|StorageSyncRecalledTotalNetworkBytes|Ja|Återställnings storlek för moln nivå|Byte|Totalt|Storlek på data som återkallas|SyncGroupName, ServerName|
|StorageSyncRecallIOTotalSizeBytes|Ja|Återkalla moln nivå|Byte|Totalt|Total storlek på data som återkallas av servern|ServerName|
|StorageSyncRecallThroughputBytesPerSecond|Ja|Återkalla data flöde för moln nivå|BytesPerSecond|Genomsnitt|Storlek på data återkallande data flöde|SyncGroupName, ServerName|
|StorageSyncServerHeartbeat|Ja|Status för server online|Antal|Maximal|Mått som loggar värdet 1 varje gång resigtered-servern registrerar ett pulsslag med moln slut punkten|ServerName|
|StorageSyncSyncSessionAppliedFilesCount|Ja|Synkroniserade filer|Antal|Totalt|Antal filer som har synkroniserats|SyncGroupName, ServerEndpointName, SyncDirection|
|StorageSyncSyncSessionPerItemErrorsCount|Ja|Filer som inte synkroniseras|Antal|Totalt|Antal filer som inte kunde synkroniseras|SyncGroupName, ServerEndpointName, SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicesregisteredservers"></a>Microsoft. storagesync/storageSyncServices/registeredServers

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|ServerHeartbeat|Ja|Status för server online|Antal|Maximal|Mått som loggar värdet 1 varje gång resigtered-servern registrerar ett pulsslag med moln slut punkten|ServerResourceId, ServerName|
|ServerRecallIOTotalSizeBytes|Ja|Återkalla moln nivå|Byte|Totalt|Total storlek på data som återkallas av servern|ServerResourceId, ServerName|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroups"></a>Microsoft. storagesync/storageSyncServices/syncGroups

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|SyncGroupBatchTransferredFileBytes|Ja|Byte som har synkroniserats|Byte|Totalt|Total fil storlek överförd för Sync-sessioner|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionAppliedFilesCount|Ja|Synkroniserade filer|Antal|Totalt|Antal filer som har synkroniserats|SyncGroupName, ServerEndpointName, SyncDirection|
|SyncGroupSyncSessionPerItemErrorsCount|Ja|Filer som inte synkroniseras|Antal|Totalt|Antal filer som inte kunde synkroniseras|SyncGroupName, ServerEndpointName, SyncDirection|


## <a name="microsoftstoragesyncstoragesyncservicessyncgroupsserverendpoints"></a>Microsoft. storagesync/storageSyncServices/syncGroups/serverEndpoints

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|ServerEndpointBatchTransferredFileBytes|Ja|Byte som har synkroniserats|Byte|Totalt|Total fil storlek överförd för Sync-sessioner|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionAppliedFilesCount|Ja|Synkroniserade filer|Antal|Totalt|Antal filer som har synkroniserats|ServerEndpointName, SyncDirection|
|ServerEndpointSyncSessionPerItemErrorsCount|Ja|Filer som inte synkroniseras|Antal|Totalt|Antal filer som inte kunde synkroniseras|ServerEndpointName, SyncDirection|


## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft. StreamAnalytics/streamingjobs

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|AMLCalloutFailedRequests|Ja|Misslyckade funktions begär Anden|Antal|Totalt|Misslyckade funktions begär Anden|LogicalName, PartitionId|
|AMLCalloutInputEvents|Ja|Funktions händelser|Antal|Totalt|Funktions händelser|LogicalName, PartitionId|
|AMLCalloutRequests|Ja|Funktions begär Anden|Antal|Totalt|Funktions begär Anden|LogicalName, PartitionId|
|ConversionErrors|Ja|Data konverterings fel|Antal|Totalt|Data konverterings fel|LogicalName, PartitionId|
|DeserializationError|Ja|Fel vid deserialisering av indataport|Antal|Totalt|Fel vid deserialisering av indataport|LogicalName, PartitionId|
|DroppedOrAdjustedEvents|Ja|Händelser som inte är i ordning|Antal|Totalt|Händelser som inte är i ordning|LogicalName, PartitionId|
|EarlyInputEvents|Ja|Tidiga ingångs händelser|Antal|Totalt|Tidiga ingångs händelser|LogicalName, PartitionId|
|Fel|Ja|Körnings fel|Antal|Totalt|Körnings fel|LogicalName, PartitionId|
|InputEventBytes|Ja|Inkommande händelse-byte|Byte|Totalt|Inkommande händelse-byte|LogicalName, PartitionId|
|InputEvents|Ja|Inmatade händelser|Antal|Totalt|Inmatade händelser|LogicalName, PartitionId|
|InputEventsSourcesBacklogged|Ja|Eftersläpande inloggade ingångs händelser|Antal|Maximal|Eftersläpande inloggade ingångs händelser|LogicalName, PartitionId|
|InputEventsSourcesPerSecond|Ja|Mottagna inmatade källor|Antal|Totalt|Mottagna inmatade källor|LogicalName, PartitionId|
|LateInputEvents|Ja|Sena ingångs händelser|Antal|Totalt|Sena ingångs händelser|LogicalName, PartitionId|
|OutputEvents|Ja|Utgående händelser|Antal|Totalt|Utgående händelser|LogicalName, PartitionId|
|OutputWatermarkDelaySeconds|Ja|Fördröjning för vattenstämpel|Sekunder|Maximal|Fördröjning för vattenstämpel|LogicalName, PartitionId|
|ResourceUtilization|Ja|SU%-användning|Procent|Maximal|SU%-användning|LogicalName, PartitionId|


## <a name="microsoftsynapseworkspaces"></a>Microsoft. Synapse/arbets ytor

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|BuiltinSqlPoolDataProcessedBytes|Inga|Bearbetade data (byte)|Byte|Totalt|Mängden data som bearbetas av frågor|Inga dimensioner|
|BuiltinSqlPoolLoginAttempts|Inga|Inloggnings försök|Antal|Totalt|Antal inloggnings försök som distribuerats eller misslyckats|Resultat|
|BuiltinSqlPoolRequestsEnded|Inga|Slutförda begär Anden|Antal|Totalt|Antal begär Anden som lyckades, misslyckades eller avbröts|Resultat|
|IntegrationActivityRunsEnded|Inga|Aktivitets körningar avslutad|Antal|Totalt|Antal integrerings aktiviteter som lyckades, misslyckades eller avbröts|Resultat, FailureType, aktivitet, ActivityType, pipeline|
|IntegrationPipelineRunsEnded|Inga|Pipelinen körs avslutad|Antal|Totalt|Antal integrations pipelines körningar som lyckades, misslyckades eller avbröts|Resultat, FailureType, pipeline|
|IntegrationTriggerRunsEnded|Inga|Utlösare körs avslutade|Antal|Totalt|Antal integrerings utlösare som har lyckats, misslyckats eller avbrutits|Resultat, FailureType, utlösare|


## <a name="microsoftsynapseworkspacesbigdatapools"></a>Microsoft. Synapse/arbets ytor/bigDataPools

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|BigDataPoolAllocatedCores|Inga|Virtuella kärnor tilldelas|Antal|Maximal|Allokerade virtuella kärnor för en Apache Spark pool|SubmitterId|
|BigDataPoolAllocatedMemory|Inga|Allokerat minne (GB)|Antal|Maximal|Allokerat minne för apach Spark-pool (GB)|SubmitterId|
|BigDataPoolApplicationsActive|Inga|Aktiva Apache Spark program|Antal|Antal|Totalt antal aktiva Apache Spark pool program|JobState|
|BigDataPoolApplicationsEnded|Inga|Avslutade Apache Spark program|Antal|Antal|Antalet program för Apache Spark-pool avslutad|JobType, JobResult|


## <a name="microsoftsynapseworkspacessqlpools"></a>Microsoft. Synapse/arbets ytor/sqlPools

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|ActiveQueries|Inga|Aktiva frågor|Antal|Totalt|Aktiva frågor. Om du använder det här måttet ofiltrerad och unsplit visas alla aktiva frågor som körs i systemet|IsUserDefined|
|AdaptiveCacheHitPercent|Inga|Procentuellt antal träffar i adaptiv cache|Procent|Maximal|Mäter hur bra arbets belastningar använder adaptiv cache. Använd det här måttet med värdet för cache träff i procent för att avgöra om du vill skala för ytterligare kapacitet eller köra arbets belastningar på nytt för att utföra cachen|Inga dimensioner|
|AdaptiveCacheUsedPercent|Inga|Procentuell användning av adaptiv cache|Procent|Maximal|Mäter hur bra arbets belastningar använder adaptiv cache. Använd det här måttet med den cachelagrade procentens mått för att avgöra om du vill skala för ytterligare kapacitet eller köra arbets belastningar på nytt för att utföra cachen|Inga dimensioner|
|Anslutningar|Ja|Anslutningar|Antal|Totalt|Totalt antal inloggningar till SQL-poolen|Resultat|
|ConnectionsBlockedByFirewall|Inga|Anslutningar blockerade av brand väggen|Antal|Totalt|Antal anslutningar blockeras av brand Väggs regler. Gå tillbaka till principer för åtkomst kontroll för SQL-poolen och övervaka dessa anslutningar om antalet är högt|Inga dimensioner|
|CPUPercent|Inga|Procent andel processor användning|Procent|Maximal|CPU-användning över alla noder i SQL-poolen|Inga dimensioner|
|DWULimit|Inga|DWU-gräns|Antal|Maximal|SQL-poolens service nivå mål|Inga dimensioner|
|DWUUsed|Inga|DWU som används|Antal|Maximal|Representerar en övergripande representation av användningen i SQL-poolen. Uppmätt av DWU-gränsen * DWU procent|Inga dimensioner|
|DWUUsedPercent|Inga|Procent Använd DWU|Procent|Maximal|Representerar en övergripande representation av användningen i SQL-poolen. Mäts genom att dra max värdet mellan CPU-procent och data IO-procent|Inga dimensioner|
|LocalTempDBUsedPercent|Inga|Lokal tempdb Använd procent|Procent|Maximal|Lokal tempdb-användning över alla Compute-noder – värden genereras var femte minut|Inga dimensioner|
|MemoryUsedPercent|Inga|Använt minne i procent|Procent|Maximal|Minnes användning för alla noder i SQL-poolen|Inga dimensioner|
|QueuedQueries|Inga|Köade frågor|Antal|Totalt|Antal sammanställda begär anden i kö efter att max gränsen för samtidighet nåddes|IsUserDefined|
|wlg_effective_min_resource_percent|Ja|Effektiv minsta resurs procent|Procent|Minimum|Inställningen för lägsta procent andel av resursen som tillåts överväger Service nivån och inställningarna för arbets belastnings gruppen. Effektiv min_percentage_resource kan justeras högre på lägre service nivåer|IsUserDefined, WorkloadGroup|
|WLGActiveQueries|Inga|Aktiva frågor för arbets belastnings grupp|Antal|Totalt|Aktiva frågor i arbets belastnings gruppen. Om du använder det här måttet ofiltrerad och unsplit visas alla aktiva frågor som körs i systemet|IsUserDefined, WorkloadGroup|
|WLGActiveQueriesTimeouts|Inga|Fråge tids gränser för arbets belastnings grupp|Antal|Totalt|Frågor för arbets belastnings gruppen som har nått tids gränsen. Tids gränser för frågor som rapporteras av detta mått sker bara när frågan har börjat köras (den innehåller inte vänte tid på grund av låsning eller väntande resurs avbrott)|IsUserDefined, WorkloadGroup|
|WLGAllocationByMaxResourcePercent|Inga|Allokering av arbets belastnings grupper efter max resurs procent|Procent|Maximal|Visar procent tilldelningen av resurser i förhållande till den effektiva Kap resurs procenten per arbets belastnings grupp. Detta mått ger arbets belastnings gruppens effektiva användning|IsUserDefined, WorkloadGroup|
|WLGAllocationBySystemPercent|Inga|Allokering av arbets belastnings grupp efter system procent|Procent|Maximal|Procent beläggningen av resurser i förhållande till hela systemet|IsUserDefined, WorkloadGroup|
|WLGEffectiveCapResourcePercent|Inga|Effektiv tak resurs procent|Procent|Maximal|Den effektiva Kap resurs procenten för arbets belastnings gruppen. Om det finns andra arbets belastnings grupper med min_percentage_resource > 0 sänks effective_cap_percentage_resource proportionellt|IsUserDefined, WorkloadGroup|
|WLGQueuedQueries|Inga|Köade frågor i arbets belastnings gruppen|Antal|Totalt|Antal sammanställda begär anden i kö efter att max gränsen för samtidighet nåddes|IsUserDefined, WorkloadGroup|


## <a name="microsofttimeseriesinsightsenvironments"></a>Microsoft. TimeSeriesInsights/miljöer

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|Ja|Inkommande mottagna byte|Byte|Totalt|Antal byte som lästs från alla händelse källor|Inga dimensioner|
|IngressReceivedInvalidMessages|Ja|Inkommande mottagna ogiltiga meddelanden|Antal|Totalt|Antal ogiltiga meddelanden som lästs från alla händelse källor för Event Hub eller IoT Hub|Inga dimensioner|
|IngressReceivedMessages|Ja|Ingress mottagna meddelanden|Antal|Totalt|Antal meddelanden som lästs från alla händelse källor för Event Hub eller IoT Hub|Inga dimensioner|
|IngressReceivedMessagesCountLag|Ja|Antal inkommande mottagna meddelanden antal fördröjning|Antal|Genomsnitt|Skillnad mellan sekvensnumret i det senaste köade meddelandet i en partition för händelse källan och sekvensnummer för meddelanden som bearbetas i ingress|Inga dimensioner|
|IngressReceivedMessagesTimeLag|Ja|Ta emot mottagna meddelanden tids fördröjning|Sekunder|Maximal|Skillnaden mellan den tid som meddelandet står i kö i händelse källan och tiden det bearbetas i ingress|Inga dimensioner|
|IngressStoredBytes|Ja|Inkommande lagrade byte|Byte|Totalt|Total storlek på händelser som har bearbetats och är tillgängliga för fråga|Inga dimensioner|
|IngressStoredEvents|Ja|Ingress lagrade händelser|Antal|Totalt|Antal förenklade händelser som har bearbetats och är tillgängliga för fråga|Inga dimensioner|
|WarmStorageMaxProperties|Ja|Maximalt antal egenskaper för varmt lagring|Antal|Maximal|Maximalt antal egenskaper som används av miljön för S1/S2 SKU och maximalt antal egenskaper som tillåts av varmt Arkiv för PAYG SKU|Inga dimensioner|
|WarmStorageUsedProperties|Ja|Egenskaper för varm lagring som används |Antal|Maximal|Antalet egenskaper som används av miljön för S1/S2 SKU och antalet egenskaper som används av varmt Arkiv för PAYG SKU|Inga dimensioner|


## <a name="microsofttimeseriesinsightsenvironmentseventsources"></a>Microsoft. TimeSeriesInsights/miljöer/eventsources

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|IngressReceivedBytes|Ja|Inkommande mottagna byte|Byte|Totalt|Antal byte som lästs från händelse källan|Inga dimensioner|
|IngressReceivedInvalidMessages|Ja|Inkommande mottagna ogiltiga meddelanden|Antal|Totalt|Antal ogiltiga meddelanden som lästs från händelse källan|Inga dimensioner|
|IngressReceivedMessages|Ja|Ingress mottagna meddelanden|Antal|Totalt|Antal meddelanden som lästs från händelse källan|Inga dimensioner|
|IngressReceivedMessagesCountLag|Ja|Antal inkommande mottagna meddelanden antal fördröjning|Antal|Genomsnitt|Skillnad mellan sekvensnumret i det senaste köade meddelandet i en partition för händelse källan och sekvensnummer för meddelanden som bearbetas i ingress|Inga dimensioner|
|IngressReceivedMessagesTimeLag|Ja|Ta emot mottagna meddelanden tids fördröjning|Sekunder|Maximal|Skillnaden mellan den tid som meddelandet står i kö i händelse källan och tiden det bearbetas i ingress|Inga dimensioner|
|IngressStoredBytes|Ja|Inkommande lagrade byte|Byte|Totalt|Total storlek på händelser som har bearbetats och är tillgängliga för fråga|Inga dimensioner|
|IngressStoredEvents|Ja|Ingress lagrade händelser|Antal|Totalt|Antal förenklade händelser som har bearbetats och är tillgängliga för fråga|Inga dimensioner|
|WarmStorageMaxProperties|Ja|Maximalt antal egenskaper för varmt lagring|Antal|Maximal|Maximalt antal egenskaper som används av miljön för S1/S2 SKU och maximalt antal egenskaper som tillåts av varmt Arkiv för PAYG SKU|Inga dimensioner|
|WarmStorageUsedProperties|Ja|Egenskaper för varm lagring som används |Antal|Maximal|Antalet egenskaper som används av miljön för S1/S2 SKU och antalet egenskaper som används av varmt Arkiv för PAYG SKU|Inga dimensioner|


## <a name="microsoftvmwarecloudsimplevirtualmachines"></a>Microsoft. VMwareCloudSimple/virtualMachines

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|Lästa byte på disk|Ja|Lästa byte på disk|Byte|Totalt|Totalt disk data flöde på grund av Läs åtgärder under exempel perioden.|Inga dimensioner|
|Disk Läs åtgärder/SEK|Ja|Disk Läs åtgärder/SEK|CountPerSecond|Genomsnitt|Genomsnittligt antal Läs åtgärder i IO i föregående exempel period. Observera att dessa åtgärder kan vara varierande storlek.|Inga dimensioner|
|Disk-skrivna byte|Ja|Disk-skrivna byte|Byte|Totalt|Totalt disk data flöde på grund av Skriv åtgärder under exempel perioden.|Inga dimensioner|
|Disk skrivnings åtgärder/SEK|Ja|Disk skrivnings åtgärder/SEK|CountPerSecond|Genomsnitt|Genomsnittligt antal i/o-Skriv åtgärder i föregående exempel period. Observera att dessa åtgärder kan vara varierande storlek.|Inga dimensioner|
|DiskReadBytesPerSecond|Ja|Disk-lästa byte/s|BytesPerSecond|Genomsnitt|Genomsnittligt disk data flöde på grund av Läs åtgärder under exempel perioden.|Inga dimensioner|
|DiskReadLatency|Ja|Läs fördröjning för disk|Millisekunder|Genomsnitt|Total Läs fördröjning. Summan av Läs fördröjningen för enheten och kärnan.|Inga dimensioner|
|DiskReadOperations|Ja|Disk Läs åtgärder|Antal|Totalt|Antalet Läs åtgärder i IO i föregående exempel period. Observera att dessa åtgärder kan vara varierande storlek.|Inga dimensioner|
|DiskWriteBytesPerSecond|Ja|Disk-skrivna byte/s|BytesPerSecond|Genomsnitt|Genomsnittligt disk data flöde på grund av Skriv åtgärder under exempel perioden.|Inga dimensioner|
|DiskWriteLatency|Ja|Skriv fördröjning för disk|Millisekunder|Genomsnitt|Total Skriv fördröjning. Summan av Skriv fördröjningarna för enheten och kärnan.|Inga dimensioner|
|DiskWriteOperations|Ja|Disk skrivnings åtgärder|Antal|Totalt|Antalet i/o-Skriv åtgärder i föregående exempel period. Observera att dessa åtgärder kan vara varierande storlek.|Inga dimensioner|
|MemoryActive|Ja|Aktivt minne|Byte|Genomsnitt|Mängden minne som används av den virtuella datorn i det tidigare lilla tids fönstret. Detta är "true"-antalet hur mycket minne som den virtuella datorn för närvarande behöver. Ytterligare, oanvänt minne kan växlas ut eller visas utan att påverka gästens prestanda.|Inga dimensioner|
|MemoryGranted|Ja|Beviljat minne|Byte|Genomsnitt|Mängden minne som har beviljats för den virtuella datorn av värden. Minnet beviljas inte för värden förrän den är påslagen en gång och beviljat minne kan växlas ut eller visas borta om VMkernel behöver minnet.|Inga dimensioner|
|MemoryUsed|Ja|Använt minne|Byte|Genomsnitt|Mängden dator minne som används av den virtuella datorn.|Inga dimensioner|
|Nätverk – inkommande|Ja|Nätverk – inkommande|Byte|Totalt|Totalt nätverks data flöde för mottagen trafik.|Inga dimensioner|
|Nätverk – utgående|Ja|Nätverk – utgående|Byte|Totalt|Totalt nätverks data flöde för överförd trafik.|Inga dimensioner|
|NetworkInBytesPerSecond|Ja|Nätverk i byte/s|BytesPerSecond|Genomsnitt|Genomsnittligt nätverks data flöde för mottagen trafik.|Inga dimensioner|
|NetworkOutBytesPerSecond|Ja|Nätverks-out-byte/SEK|BytesPerSecond|Genomsnitt|Genomsnittligt nätverks data flöde för överförd trafik.|Inga dimensioner|
|Processorprocentandel|Ja|Processorprocentandel|Procent|Genomsnitt|PROCESSOR användningen. Det här värdet rapporteras med 100% som representerar alla processor kärnor i systemet. Till exempel är en 2-vägs virtuell dator som använder 50% av ett system med fyra kärnor fullständigt med två kärnor.|Inga dimensioner|
|PercentageCpuReady|Ja|Procent andel CPU klar|Millisekunder|Totalt|Ready Time är den tid som väntar på att CPU: er ska bli tillgängliga under det tidigare uppdaterings intervallet.|Inga dimensioner|


## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft. Web/hostingEnvironments/multiRolePools

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|ActiveRequests|Ja|Aktiva begär Anden (inaktuella)|Antal|Totalt|Aktiva begär Anden|Instans|
|AverageResponseTime|Ja|Genomsnittlig svars tid (inaktuell)|Sekunder|Genomsnitt|Genomsnittlig tid det tar för klient delen att betjäna begär Anden, i sekunder.|Instans|
|BytesReceived|Ja|Data i|Byte|Totalt|Den genomsnittliga inkommande bandbredd som används på alla klient delar, i MiB.|Instans|
|Bytes sent|Ja|Data ut|Byte|Totalt|Genomsnittlig inkommande bandbredd som används på alla klient delar i MiB.|Instans|
|CpuPercentage|Ja|CPU-procent|Procent|Genomsnitt|Den genomsnittliga CPU som används i alla instanser av klient delen.|Instans|
|DiskQueueLength|Ja|Diskkölängd|Antal|Genomsnitt|Genomsnittligt antal begär Anden om läsning och skrivning som ställts i kö för lagring. En hög diskkölängd är en indikation på en app som kan vara långsam på grund av överdriven disk-I/O.|Instans|
|Http101|Ja|Http 101|Antal|Totalt|Antalet förfrågningar som resulterar i HTTP-statuskod 101.|Instans|
|Http2xx|Ja|Http-2xx|Antal|Totalt|Antal begär Anden som resulterade i HTTP-status kod = 200 men < 300.|Instans|
|Http3xx|Ja|Http-3xx|Antal|Totalt|Antal begär Anden som resulterade i HTTP-status kod = 300 men < 400.|Instans|
|Http401|Ja|Http 401|Antal|Totalt|Antal begär Anden som resulterar i status kod för HTTP 401.|Instans|
|Http403|Ja|Http 403|Antal|Totalt|Antal begär Anden som resulterar i status kod för HTTP 403.|Instans|
|Http404|Ja|Http 404|Antal|Totalt|Antal begär Anden som resulterar i status kod för HTTP 404.|Instans|
|Http406|Ja|Http 406|Antal|Totalt|Antal begär Anden som resulterar i status kod för HTTP 406.|Instans|
|Http4xx|Ja|Http-4xx|Antal|Totalt|Antal begär Anden som resulterade i HTTP-status kod = 400 men < 500.|Instans|
|Http5xx|Ja|Http-serverfel|Antal|Totalt|Antal begär Anden som resulterade i HTTP-status kod = 500 men < 600.|Instans|
|HttpQueueLength|Ja|Längd på http-kö|Antal|Genomsnitt|Genomsnittligt antal HTTP-begäranden som måste sitta i kön innan de kunde uppfyllas. En hög eller ökande HTTP-Kölängd är ett symtom på en plan som är tungt belastad.|Instans|
|LargeAppServicePlanInstances|Ja|Stora App Service planera arbetare|Antal|Genomsnitt|Stora App Service planera arbetare|Inga dimensioner|
|MediumAppServicePlanInstances|Ja|Medel App Service planera arbetare|Antal|Genomsnitt|Medel App Service planera arbetare|Inga dimensioner|
|MemoryPercentage|Ja|Minnes procent|Procent|Genomsnitt|Genomsnittligt minne som används i alla instanser av klient delen.|Instans|
|Begäranden|Ja|Begäranden|Antal|Totalt|Det totala antalet begär Anden oavsett den resulterande HTTP-statuskoden.|Instans|
|SmallAppServicePlanInstances|Ja|Små App Service planera arbetare|Antal|Genomsnitt|Små App Service planera arbetare|Inga dimensioner|
|TotalFrontEnds|Ja|Totalt antal klient delar|Antal|Genomsnitt|Totalt antal klient delar|Inga dimensioner|


## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft. Web/hostingEnvironments/workerPools

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|CpuPercentage|Ja|CPU-procent|Procent|Genomsnitt|Den genomsnittliga CPU som används i alla instanser av arbets gruppen.|Instans|
|MemoryPercentage|Ja|Minnes procent|Procent|Genomsnitt|Genomsnittligt minne som används i alla instanser av arbets gruppen.|Instans|
|WorkersAvailable|Ja|Tillgängliga arbetare|Antal|Genomsnitt|Tillgängliga arbetare|Inga dimensioner|
|WorkersTotal|Ja|Totalt antal arbetare|Antal|Genomsnitt|Totalt antal arbetare|Inga dimensioner|
|WorkersUsed|Ja|Använda arbetare|Antal|Genomsnitt|Använda arbetare|Inga dimensioner|


## <a name="microsoftwebserverfarms"></a>Microsoft. Web/Server grupper

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|BytesReceived|Ja|Data i|Byte|Totalt|Den genomsnittliga inkommande bandbredd som används för alla instanser av planen.|Instans|
|Bytes sent|Ja|Data ut|Byte|Totalt|Den genomsnittliga utgående bandbredd som används för alla instanser av planen.|Instans|
|CpuPercentage|Ja|CPU-procent|Procent|Genomsnitt|Den genomsnittliga CPU som används för alla instanser av planen.|Instans|
|DiskQueueLength|Ja|Diskkölängd|Antal|Genomsnitt|Genomsnittligt antal begär Anden om läsning och skrivning som ställts i kö för lagring. En hög diskkölängd är en indikation på en app som kan vara långsam på grund av överdriven disk-I/O.|Instans|
|HttpQueueLength|Ja|Längd på http-kö|Antal|Genomsnitt|Genomsnittligt antal HTTP-begäranden som måste sitta i kön innan de kunde uppfyllas. En hög eller ökande HTTP-Kölängd är ett symtom på en plan som är tungt belastad.|Instans|
|MemoryPercentage|Ja|Minnes procent|Procent|Genomsnitt|Genomsnittligt minne som används för alla instanser av planen.|Instans|
|SocketInboundAll|Ja|SocketInboundAll|Antal|Genomsnitt|SocketInboundAll|Instans|
|SocketLoopback|Ja|SocketLoopback|Antal|Genomsnitt|SocketLoopback|Instans|
|SocketOutboundAll|Ja|SocketOutboundAll|Antal|Genomsnitt|SocketOutboundAll|Instans|
|SocketOutboundEstablished|Ja|SocketOutboundEstablished|Antal|Genomsnitt|SocketOutboundEstablished|Instans|
|SocketOutboundTimeWait|Ja|SocketOutboundTimeWait|Antal|Genomsnitt|SocketOutboundTimeWait|Instans|
|TcpCloseWait|Ja|Väntan på TCP-stängning|Antal|Genomsnitt|Väntan på TCP-stängning|Instans|
|TcpClosing|Ja|TCP-stängning|Antal|Genomsnitt|TCP-stängning|Instans|
|TcpEstablished|Ja|TCP upprättad|Antal|Genomsnitt|TCP upprättad|Instans|
|TcpFinWait1|Ja|TCP räntetrans wait 1|Antal|Genomsnitt|TCP räntetrans wait 1|Instans|
|TcpFinWait2|Ja|TCP räntetrans wait 2|Antal|Genomsnitt|TCP räntetrans wait 2|Instans|
|TcpLastAck|Ja|TCP-senaste ack|Antal|Genomsnitt|TCP-senaste ack|Instans|
|TcpSynReceived|Ja|TCP-syn mottagen|Antal|Genomsnitt|TCP-syn mottagen|Instans|
|TcpSynSent|Ja|TCP-syn har skickats|Antal|Genomsnitt|TCP-syn har skickats|Instans|
|TcpTimeWait|Ja|Väntan på TCP-tid|Antal|Genomsnitt|Väntan på TCP-tid|Instans|


## <a name="microsoftwebsites"></a>Microsoft. Web/Sites

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|AppConnections|Ja|Anslutningar|Antal|Genomsnitt|Antalet kopplade socketar som är befintliga i sandbox (w3wp.exe och dess underordnade processer). En bunden socket skapas genom att anropa BIND ()/Connect ()-API: er och förblir kvar tills socketen stängs med CloseHandle ()/Closesocket ().|Instans|
|AverageMemoryWorkingSet|Ja|Genomsnittlig arbets mängd för minne|Byte|Genomsnitt|Den genomsnittliga mängden minne som används av appen, i megabyte (MiB).|Instans|
|AverageResponseTime|Ja|Genomsnittlig svars tid (inaktuell)|Sekunder|Genomsnitt|Genomsnittlig tid det tar för appen att betjäna begär Anden, i sekunder.|Instans|
|BytesReceived|Ja|Data i|Byte|Totalt|Mängden inkommande bandbredd som används av appen, i MiB.|Instans|
|Bytes sent|Ja|Data ut|Byte|Totalt|Mängden utgående bandbredd som används av appen, i MiB.|Instans|
|CpuTime|Ja|CPU-tid|Sekunder|Totalt|Mängden CPU som används av appen, i sekunder. För mer information om det här måttet. Se https://docs.microsoft.com/azure/app-service/web-sites-monitor#cpu-time-vs-cpu-percentage (processor tid jämfört med procent av processor).|Instans|
|CurrentAssemblies|Ja|Aktuella sammansättningar|Antal|Genomsnitt|Det aktuella antalet sammansättningar som har lästs in i alla AppDomains i det här programmet.|Instans|
|FileSystemUsage|Ja|Fil system användning|Byte|Genomsnitt|Procent andel av fil Systems kvoten som används av appen.|Inga dimensioner|
|FunctionExecutionCount|Ja|Funktions körnings antal|Antal|Totalt|Funktions körnings antal|Instans|
|FunctionExecutionUnits|Ja|Funktions körnings enheter|Antal|Totalt|Funktions körnings enheter|Instans|
|Gen0Collections|Ja|Skräp insamling för gen 0|Antal|Totalt|Antalet gånger som generation 0-objekt är skräp insamlat sedan program processen startades. Högre generations GC generationer omfattar all lägre generations-GC generationer.|Instans|
|Gen1Collections|Ja|Skräp insamling för gen 1|Antal|Totalt|Antalet gånger som generation 1-objekten är skräp insamlat sedan program processen startades. Högre generations GC generationer omfattar all lägre generations-GC generationer.|Instans|
|Gen2Collections|Ja|Gen 2 skräp insamling|Antal|Totalt|Antalet gånger som generation 2-objekt är skräp insamlat sedan program processen startades.|Instans|
|Konsolindataobjekt|Ja|Antal referenser|Antal|Genomsnitt|Det totala antalet handles som för närvarande är öppna av app-processen.|Instans|
|HealthCheckStatus|Ja|Hälso kontroll status|Antal|Genomsnitt|Hälso kontroll status|Instans|
|Http101|Ja|Http 101|Antal|Totalt|Antalet förfrågningar som resulterar i HTTP-statuskod 101.|Instans|
|Http2xx|Ja|Http-2xx|Antal|Totalt|Antal begär Anden som resulterade i HTTP-status kod = 200 men < 300.|Instans|
|Http3xx|Ja|Http-3xx|Antal|Totalt|Antal begär Anden som resulterade i HTTP-status kod = 300 men < 400.|Instans|
|Http401|Ja|Http 401|Antal|Totalt|Antal begär Anden som resulterar i status kod för HTTP 401.|Instans|
|Http403|Ja|Http 403|Antal|Totalt|Antal begär Anden som resulterar i status kod för HTTP 403.|Instans|
|Http404|Ja|Http 404|Antal|Totalt|Antal begär Anden som resulterar i status kod för HTTP 404.|Instans|
|Http406|Ja|Http 406|Antal|Totalt|Antal begär Anden som resulterar i status kod för HTTP 406.|Instans|
|Http4xx|Ja|Http-4xx|Antal|Totalt|Antal begär Anden som resulterade i HTTP-status kod = 400 men < 500.|Instans|
|Http5xx|Ja|Http-serverfel|Antal|Totalt|Antal begär Anden som resulterade i HTTP-status kod = 500 men < 600.|Instans|
|HttpResponseTime|Ja|Svars tid|Sekunder|Genomsnitt|Den tid det tar för appen att betjäna begär Anden, i sekunder.|Instans|
|IoOtherBytesPerSecond|Ja|I/o andra byte per sekund|BytesPerSecond|Totalt|Den hastighet med vilken app-processen utfärdar byte till I/O-åtgärder som inte omfattar data, till exempel kontroll åtgärder.|Instans|
|IoOtherOperationsPerSecond|Ja|Andra i/o-åtgärder per sekund|BytesPerSecond|Totalt|Den hastighet med vilken app-processen utfärdar I/O-åtgärder som inte är Läs-eller Skriv åtgärder.|Instans|
|IoReadBytesPerSecond|Ja|IO-lästa byte per sekund|BytesPerSecond|Totalt|Den hastighet med vilken appens process läser in byte från I/O-åtgärder.|Instans|
|IoReadOperationsPerSecond|Ja|IO-Läs åtgärder per sekund|BytesPerSecond|Totalt|Den hastighet med vilken app-processen utfärdar Läs-I/O-åtgärder.|Instans|
|IoWriteBytesPerSecond|Ja|Skrivna byte i i/o per sekund|BytesPerSecond|Totalt|Den hastighet med vilken appens process skriver byte till I/O-åtgärder.|Instans|
|IoWriteOperationsPerSecond|Ja|I/o-Skriv åtgärder per sekund|BytesPerSecond|Totalt|Den hastighet med vilken program processen utfärdar Skriv-I/O-åtgärder.|Instans|
|MemoryWorkingSet|Ja|Minnes arbets mängd|Byte|Genomsnitt|Den aktuella mängden minne som används av appen, i MiB.|Instans|
|PrivateBytes|Ja|Privata byte|Byte|Genomsnitt|Privata byte är den aktuella storleken i byte på minne som program processen har allokerat och som inte kan delas med andra processer.|Instans|
|Begäranden|Ja|Begäranden|Antal|Totalt|Det totala antalet begär Anden oavsett den resulterande HTTP-statuskoden.|Instans|
|RequestsInApplicationQueue|Ja|Begär anden i program kön|Antal|Genomsnitt|Antalet begär anden i program begär ande kön.|Instans|
|Konversation|Ja|Antal trådar|Antal|Genomsnitt|Antalet trådar som för närvarande är aktiva i program processen.|Instans|
|TotalAppDomains|Ja|Totalt antal app-domäner|Antal|Genomsnitt|Det aktuella antalet AppDomains som har lästs in i det här programmet.|Instans|
|TotalAppDomainsUnloaded|Ja|Totalt antal app-domäner som har inaktiverats|Antal|Genomsnitt|Det totala antalet inaktiverade tillämpnings domäner sedan programmet startades.|Instans|


## <a name="microsoftwebsitesslots"></a>Microsoft. Web/Sites/lotss

|Mått|Kan exporteras via diagnostikinställningar?|Mått visnings namn|Enhet|Sammansättningstyp|Beskrivning|Dimensioner|
|---|---|---|---|---|---|---|
|AppConnections|Ja|Anslutningar|Antal|Genomsnitt|Antalet kopplade socketar som är befintliga i sandbox (w3wp.exe och dess underordnade processer). En bunden socket skapas genom att anropa BIND ()/Connect ()-API: er och förblir kvar tills socketen stängs med CloseHandle ()/Closesocket ().|Instans|
|AverageMemoryWorkingSet|Ja|Genomsnittlig arbets mängd för minne|Byte|Genomsnitt|Den genomsnittliga mängden minne som används av appen, i megabyte (MiB).|Instans|
|AverageResponseTime|Ja|Genomsnittlig svars tid (inaktuell)|Sekunder|Genomsnitt|Genomsnittlig tid det tar för appen att betjäna begär Anden, i sekunder.|Instans|
|BytesReceived|Ja|Data i|Byte|Totalt|Mängden inkommande bandbredd som används av appen, i MiB.|Instans|
|Bytes sent|Ja|Data ut|Byte|Totalt|Mängden utgående bandbredd som används av appen, i MiB.|Instans|
|CpuTime|Ja|CPU-tid|Sekunder|Totalt|Mängden CPU som används av appen, i sekunder. För mer information om det här måttet. Se https://docs.microsoft.com/azure/app-service/web-sites-monitor#cpu-time-vs-cpu-percentage (processor tid jämfört med procent av processor).|Instans|
|CurrentAssemblies|Ja|Aktuella sammansättningar|Antal|Genomsnitt|Det aktuella antalet sammansättningar som har lästs in i alla AppDomains i det här programmet.|Instans|
|FileSystemUsage|Ja|Fil system användning|Byte|Genomsnitt|Procent andel av fil Systems kvoten som används av appen.|Inga dimensioner|
|FunctionExecutionCount|Ja|Funktions körnings antal|Antal|Totalt|Funktions körnings antal|Instans|
|FunctionExecutionUnits|Ja|Funktions körnings enheter|Antal|Totalt|Funktions körnings enheter|Instans|
|Gen0Collections|Ja|Skräp insamling för gen 0|Antal|Totalt|Antalet gånger som generation 0-objekt är skräp insamlat sedan program processen startades. Högre generations GC generationer omfattar all lägre generations-GC generationer.|Instans|
|Gen1Collections|Ja|Skräp insamling för gen 1|Antal|Totalt|Antalet gånger som generation 1-objekten är skräp insamlat sedan program processen startades. Högre generations GC generationer omfattar all lägre generations-GC generationer.|Instans|
|Gen2Collections|Ja|Gen 2 skräp insamling|Antal|Totalt|Antalet gånger som generation 2-objekt är skräp insamlat sedan program processen startades.|Instans|
|Konsolindataobjekt|Ja|Antal referenser|Antal|Genomsnitt|Det totala antalet handles som för närvarande är öppna av app-processen.|Instans|
|HealthCheckStatus|Ja|Hälso kontroll status|Antal|Genomsnitt|Hälso kontroll status|Instans|
|Http101|Ja|Http 101|Antal|Totalt|Antalet förfrågningar som resulterar i HTTP-statuskod 101.|Instans|
|Http2xx|Ja|Http-2xx|Antal|Totalt|Antal begär Anden som resulterade i HTTP-status kod = 200 men < 300.|Instans|
|Http3xx|Ja|Http-3xx|Antal|Totalt|Antal begär Anden som resulterade i HTTP-status kod = 300 men < 400.|Instans|
|Http401|Ja|Http 401|Antal|Totalt|Antal begär Anden som resulterar i status kod för HTTP 401.|Instans|
|Http403|Ja|Http 403|Antal|Totalt|Antal begär Anden som resulterar i status kod för HTTP 403.|Instans|
|Http404|Ja|Http 404|Antal|Totalt|Antal begär Anden som resulterar i status kod för HTTP 404.|Instans|
|Http406|Ja|Http 406|Antal|Totalt|Antal begär Anden som resulterar i status kod för HTTP 406.|Instans|
|Http4xx|Ja|Http-4xx|Antal|Totalt|Antal begär Anden som resulterade i HTTP-status kod = 400 men < 500.|Instans|
|Http5xx|Ja|Http-serverfel|Antal|Totalt|Antal begär Anden som resulterade i HTTP-status kod = 500 men < 600.|Instans|
|HttpResponseTime|Ja|Svars tid|Sekunder|Genomsnitt|Den tid det tar för appen att betjäna begär Anden, i sekunder.|Instans|
|IoOtherBytesPerSecond|Ja|I/o andra byte per sekund|BytesPerSecond|Totalt|Den hastighet med vilken app-processen utfärdar byte till I/O-åtgärder som inte omfattar data, till exempel kontroll åtgärder.|Instans|
|IoOtherOperationsPerSecond|Ja|Andra i/o-åtgärder per sekund|BytesPerSecond|Totalt|Den hastighet med vilken app-processen utfärdar I/O-åtgärder som inte är Läs-eller Skriv åtgärder.|Instans|
|IoReadBytesPerSecond|Ja|IO-lästa byte per sekund|BytesPerSecond|Totalt|Den hastighet med vilken appens process läser in byte från I/O-åtgärder.|Instans|
|IoReadOperationsPerSecond|Ja|IO-Läs åtgärder per sekund|BytesPerSecond|Totalt|Den hastighet med vilken app-processen utfärdar Läs-I/O-åtgärder.|Instans|
|IoWriteBytesPerSecond|Ja|Skrivna byte i i/o per sekund|BytesPerSecond|Totalt|Den hastighet med vilken appens process skriver byte till I/O-åtgärder.|Instans|
|IoWriteOperationsPerSecond|Ja|I/o-Skriv åtgärder per sekund|BytesPerSecond|Totalt|Den hastighet med vilken program processen utfärdar Skriv-I/O-åtgärder.|Instans|
|MemoryWorkingSet|Ja|Minnes arbets mängd|Byte|Genomsnitt|Den aktuella mängden minne som används av appen, i MiB.|Instans|
|PrivateBytes|Ja|Privata byte|Byte|Genomsnitt|Privata byte är den aktuella storleken i byte på minne som program processen har allokerat och som inte kan delas med andra processer.|Instans|
|Begäranden|Ja|Begäranden|Antal|Totalt|Det totala antalet begär Anden oavsett den resulterande HTTP-statuskoden.|Instans|
|RequestsInApplicationQueue|Ja|Begär anden i program kön|Antal|Genomsnitt|Antalet begär anden i program begär ande kön.|Instans|
|Konversation|Ja|Antal trådar|Antal|Genomsnitt|Antalet trådar som för närvarande är aktiva i program processen.|Instans|
|TotalAppDomains|Ja|Totalt antal app-domäner|Antal|Genomsnitt|Det aktuella antalet AppDomains som har lästs in i det här programmet.|Instans|
|TotalAppDomainsUnloaded|Ja|Totalt antal app-domäner som har inaktiverats|Antal|Genomsnitt|Det totala antalet inaktiverade tillämpnings domäner sedan programmet startades.|Instans|


## <a name="next-steps"></a>Nästa steg

- [Läs om mått i Azure Monitor](data-platform.md)
- [Skapa aviseringar för mått](alerts-overview.md)
- [Exportera mått till lagring, Event Hub eller Log Analytics](platform-logs-overview.md)
