---
title: Mått för export beteende med NULL-värden och nollvärden
description: Diskussion av NULL-värden jämfört med nollvärden vid export av mått och en pekare till en lista över vilka mått som inte kan exporteras.
services: azure-monitor
ms.topic: reference
ms.date: 07/22/2020
ms.subservice: metrics
ms.openlocfilehash: 8f503623031b7071c5e0a206d078175e16a7e919
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100623519"
---
# <a name="azure-monitor-platform-metrics-exportable-via-diagnostic-settings"></a>Azure Monitor-plattformsmått som kan exporteras via diagnostikinställningar

Azure Monitor tillhandahåller [plattforms mått](../essentials/data-platform-metrics.md) som standard utan konfiguration. Det ger dig flera olika sätt att interagera med plattforms mått, inklusive att lägga till dem i portalen, komma åt dem via REST API eller genom att fråga dem med hjälp av PowerShell eller CLI. Se [mått – stöds](../platform/metrics-supported.md) för en fullständig lista över plattforms mått som för närvarande är tillgängliga med Azure Monitor den konsoliderade mått pipelinen. Använd [2018-01-01 API-versionen](/rest/api/monitor/metricdefinitions)för att fråga efter och komma åt dessa mått. Andra mått kan vara tillgängliga i portalen eller med äldre API: er.

## <a name="metrics-not-exportable-via-diagnostic-settings"></a>Mått som inte kan exporteras via diagnostikinställningar

Det innehåll som användes för att finnas på den här platsen har flyttats till [listan över Azure Monitor mått som stöds](../platform/metrics-supported.md#exporting-platform-metrics-to-other-locations).

Det finns begränsningar när du exporterar mått via diagnostikinställningar. Alla mått kan exporteras med REST API. 

## <a name="exported-zero-vs-null-values"></a>Exporterade noll vs NULL-värden 

Mått har olika beteenden vid hantering av 0-eller NULL-värden.  Vissa mått rapporter 0 när inga data hämtas, till exempel statistik för http-problem. Andra mått lagrar NULL-värden när inga data hämtas, eftersom det kan betyda att resursen är offline. Du kan se skillnaden när du diagramerar måtten med NULL-värden som visas som [streckade linjer](metrics-troubleshoot.md#chart-shows-dashed-line). 

När plattforms måtten kan exporteras via diagnostikinställningar matchar de beteendet för måttet. Det vill säga att de exporterar NULL-värden när resursen inte skickar några data.  De exporterar bara 0 när de har genererats av den underliggande resursen. 

Om du tar bort en resurs grupp eller en resurs skickas mått data från de berörda resurserna inte längre till diagnostikinställningar för att ställa in export destinationer. Det innebär att den inte längre visas i Event Hubs Azure Storage konton och Log Analytics arbets ytor.

### <a name="metrics-that-used-to-export-zero-for-null"></a>Mått som används för att exportera noll för NULL

Före den 1 juni 2020 användes måtten nedan **för att** generera "0" när det inte fanns några data. Dessa nollor kan sedan exporteras till efterföljande system som Log Analytics och Azure Storage.  Detta medförde en viss förvirring mellan verkliga "0s" (som har spridits av resursen) och tolkade "0s" (NULL-värden) och så att beteendet ändrades till att matcha det bakomliggande måttets värde som nämnts i föregående avsnitt. 

Ändringen skedde i alla offentliga och privata moln.

Ändringen påverkar inte beteendet hos någon av följande upplevelser: 
   - Plattforms resurs loggar som exporter ATS via diagnostikinställningar
   - Mått diagram i Metrics Explorer
   - Avisering om plattforms mått
 
Följande är en lista över de mått vars beteende har ändrats. 

| ResourceType                    | Metric               |  MetricDisplayName  | 
|---------------------------------|----------------------|---------------------|
| Microsoft.ApiManagement/service | UnauthorizedRequests |  Obehöriga Gateway-begäranden (inaktuella)  | 
| Microsoft.ApiManagement/service | TotalRequests |  Totalt antal Gateway-begäranden (inaktuella)  | 
| Microsoft.ApiManagement/service | SuccessfulRequests |  Lyckade Gateway-begäranden (inaktuella)  | 
| Microsoft.ApiManagement/service | Begäranden |  Begäranden  | 
| Microsoft.ApiManagement/service | OtherRequests |  Andra gateway-begäranden (inaktuella)  | 
| Microsoft.ApiManagement/service | FailedRequests |  Misslyckade Gateway-begäranden (inaktuella)  | 
| Microsoft.ApiManagement/service | EventHubTotalFailedEvents |  Misslyckade EventHub-händelser  | 
| Microsoft.ApiManagement/service | EventHubTotalEvents |  Totalt antal EventHub-händelser  | 
| Microsoft.ApiManagement/service | EventHubTotalBytesSent |  Storlek på EventHub-händelser  | 
| Microsoft.ApiManagement/service | EventHubTimedoutEvents |  Tids gränsen nåddes för EventHub-händelser  | 
| Microsoft.ApiManagement/service | EventHubThrottledEvents |  Begränsade EventHub-händelser  | 
| Microsoft.ApiManagement/service | EventHubSuccessfulEvents |  Lyckade EventHub-händelser  | 
| Microsoft.ApiManagement/service | EventHubRejectedEvents |  Avvisade EventHub-händelser  | 
| Microsoft.ApiManagement/service | EventHubDroppedEvents |  Ignorerade EventHub-händelser  | 
| Microsoft.ApiManagement/service | Varaktighet |  Total varaktighet för gateway-begäranden  | 
| Microsoft.ApiManagement/service | BackendDuration |  Varaktighet för backend-begäranden  | 
| Microsoft. DBforMariaDB/servers | storage_used |  Använt lagrings utrymme  | 
| Microsoft. DBforMariaDB/servers | storage_percent |  Lagrings procent  | 
| Microsoft. DBforMariaDB/servers | storage_limit |  Lagrings gräns  | 
| Microsoft. DBforMariaDB/servers | serverlog_storage_usage |  Server logg lagring används  | 
| Microsoft. DBforMariaDB/servers | serverlog_storage_percent |  Server logg lagrings procent  | 
| Microsoft. DBforMariaDB/servers | serverlog_storage_limit |  Server logg lagrings gräns  | 
| Microsoft. DBforMariaDB/servers | seconds_behind_master |  Fördröjning för replikering på några sekunder  | 
| Microsoft. DBforMariaDB/servers | network_bytes_ingress |  Nätverk – inkommande  | 
| Microsoft. DBforMariaDB/servers | network_bytes_egress |  Nätverk – utgående  | 
| Microsoft. DBforMariaDB/servers | memory_percent |  Minnes procent  | 
| Microsoft. DBforMariaDB/servers | io_consumption_percent |  I/o procent  | 
| Microsoft. DBforMariaDB/servers | cpu_percent |  CPU-procent  | 
| Microsoft. DBforMariaDB/servers | connections_failed |  Misslyckade anslutningar  | 
| Microsoft. DBforMariaDB/servers | backup_storage_used |  Lagring av säkerhets kopior som används  | 
| Microsoft. DBforMariaDB/servers | active_connections |  Aktiva anslutningar  | 
| Microsoft. DBforMySQL/servers | storage_used |  Använt lagrings utrymme  | 
| Microsoft. DBforMySQL/servers | storage_percent |  Lagrings procent  | 
| Microsoft. DBforMySQL/servers | storage_limit |  Lagrings gräns  | 
| Microsoft. DBforMySQL/servers | serverlog_storage_usage |  Server logg lagring används  | 
| Microsoft. DBforMySQL/servers | serverlog_storage_percent |  Server logg lagrings procent  | 
| Microsoft. DBforMySQL/servers | serverlog_storage_limit |  Server logg lagrings gräns  | 
| Microsoft. DBforMySQL/servers | seconds_behind_master |  Fördröjning för replikering på några sekunder  | 
| Microsoft. DBforMySQL/servers | network_bytes_ingress |  Nätverk – inkommande  | 
| Microsoft. DBforMySQL/servers | network_bytes_egress |  Nätverk – utgående  | 
| Microsoft. DBforMySQL/servers | memory_percent |  Minnes procent  | 
| Microsoft. DBforMySQL/servers | io_consumption_percent |  I/o procent  | 
| Microsoft. DBforMySQL/servers | cpu_percent |  CPU-procent  | 
| Microsoft. DBforMySQL/servers | connections_failed |  Misslyckade anslutningar  | 
| Microsoft. DBforMySQL/servers | backup_storage_used |  Lagring av säkerhets kopior som används  | 
| Microsoft. DBforMySQL/servers | active_connections |  Aktiva anslutningar  | 
| Microsoft. Devices/Account | digitaltwins. telemetri. noder |  Plats hållare för telemetri för digitala dubbla noder  | 
| Microsoft. Devices/IotHubs | twinQueries. lyckades |  Lyckades dubbla frågor  | 
| Microsoft. Devices/IotHubs | twinQueries.resultSize |  Resultat storlek för dubbla frågor  | 
| Microsoft. Devices/IotHubs | twinQueries. Failure |  Misslyckade dubbla frågor  | 
| Microsoft. Devices/IotHubs | Jobs. queryJobs. lyckades |  Slutförda jobb frågor  | 
| Microsoft. Devices/IotHubs | Jobs. queryJobs. Failure |  Misslyckade jobb frågor  | 
| Microsoft. Devices/IotHubs | Jobs. listJobs. lyckades |  Lyckade anrop till List jobb  | 
| Microsoft. Devices/IotHubs | Jobs. listJobs. Failure |  Misslyckade anrop till List jobb  | 
| Microsoft. Devices/IotHubs | jobb. misslyckades |  Misslyckade jobb  | 
| Microsoft. Devices/IotHubs | Jobs. createTwinUpdateJob. lyckades |  Skapandet av dubbla uppdaterings jobb lyckades  | 
| Microsoft. Devices/IotHubs | Jobs. createTwinUpdateJob. Failure |  Det gick inte att skapa dubbla uppdaterings jobb  | 
| Microsoft. Devices/IotHubs | Jobs. createDirectMethodJob. lyckades |  Lyckade skapande av metod anrops jobb  | 
| Microsoft. Devices/IotHubs | Jobs. createDirectMethodJob. Failure |  Det gick inte att skapa metod anrops jobb  | 
| Microsoft. Devices/IotHubs | jobb. slutfört |  Slutförda jobb  | 
| Microsoft. Devices/IotHubs | Jobs. cancelJob. lyckades |  Slutförda jobb avbokningar  | 
| Microsoft. Devices/IotHubs | Jobs. cancelJob. Failure |  Misslyckade jobb-annulleringar  | 
| Microsoft. Devices/IotHubs | EventGridLatency |  Event Grid svars tid (för hands version)  | 
| Microsoft. Devices/IotHubs | EventGridDeliveries |  Event Grid leveranser (för hands version)  | 
| Microsoft. Devices/IotHubs | enheter. totalDevices |  Totalt antal enheter (inaktuella)  | 
| Microsoft. Devices/IotHubs | Devices. connectedDevices. allProtocol |  Anslutna enheter (inaktuella)   | 
| Microsoft. Devices/IotHubs | deviceDataUsageV2 |  Total användning av enhets data (för hands version)  | 
| Microsoft. Devices/IotHubs | deviceDataUsage |  Total användning av enhets data  | 
| Microsoft. Devices/IotHubs | dailyMessageQuotaUsed |  Totalt antal meddelanden som används  | 
| Microsoft. Devices/IotHubs | D2C. delad. Update. lyckades |  Lyckade dubbla uppdateringar från enheter  | 
| Microsoft. Devices/IotHubs | D2C. dubbla. Update. size |  Storlek på dubbla uppdateringar från enheter  | 
| Microsoft. Devices/IotHubs | D2C. delad. Update. Failure |  Misslyckade dubbla uppdateringar från enheter  | 
| Microsoft. Devices/IotHubs | D2C. delad. lyckades |  Lyckades dubbla läsningar från enheter  | 
| Microsoft. Devices/IotHubs | D2C., delad. storlek |  Svars storlek för dubbla läsningar från enheter  | 
| Microsoft. Devices/IotHubs | D2C. delad. |  Misslyckade dubbla läsningar från enheter  | 
| Microsoft. Devices/IotHubs | D2C. telemetri. ingress. lyckades |  Meddelande om telemetri  | 
| Microsoft. Devices/IotHubs | D2C. telemetri. ingress. sendThrottle |  Antal begränsnings fel  | 
| Microsoft. Devices/IotHubs | D2C. telemetri. ingress. allProtocol |  Skicka försök för telemetri  | 
| Microsoft. Devices/IotHubs | D2C. telemetri. utgående. lyckades |  Routning: telemetri meddelanden levereras  | 
| Microsoft. Devices/IotHubs | D2C. telemetri. utgående. överblivna |  Routning: telemetri-meddelanden har överblivna   | 
| Microsoft. Devices/IotHubs | D2C. telemetri. utgående. ogiltig |  Routning: telemetri-meddelanden är inkompatibla  | 
| Microsoft. Devices/IotHubs | D2C. telemetri. utgående. fallback |  Routning: meddelanden levererade till reserv  | 
| Microsoft. Devices/IotHubs | D2C. telemetri. utgående. |  Routning: telemetri ignoreras   | 
| Microsoft. Devices/IotHubs | D2C. endpoints. svars tid. Storage |  Routning: meddelande fördröjning för lagring  | 
| Microsoft. Devices/IotHubs | D2C. endpoints. latens. serviceBusTopics |  Routning: meddelande fördröjning för Service Bus ämne  | 
| Microsoft. Devices/IotHubs | D2C. endpoints. latens. serviceBusQueues |  Routning: meddelande fördröjning för Service Bus kö  | 
| Microsoft. Devices/IotHubs | D2C. endpoints. latens. eventHubs |  Routning: meddelande fördröjning för Event Hub  | 
| Microsoft. Devices/IotHubs | D2C. endpoints. latens. Builtin. events |  Routning: meddelande fördröjning för meddelanden/händelser  | 
| Microsoft. Devices/IotHubs | D2C. endpoints. utgående. Storage. byte |  Routning: data som levereras till lagring  | 
| Microsoft. Devices/IotHubs | D2C. endpoints. utgående. Storage. blob |  Routning: blobbar levererade till lagring  | 
| Microsoft. Devices/IotHubs | D2C. endpoints. utgående. Storage |  Routning: meddelanden som levereras till lagring  | 
| Microsoft. Devices/IotHubs | D2C. endpoints. utgående. serviceBusTopics |  Routning: meddelanden levererade till Service Bus ämnet  | 
| Microsoft. Devices/IotHubs | D2C. endpoints. utgående. serviceBusQueues |  Routning: meddelanden levererade till Service Bus kö  | 
| Microsoft. Devices/IotHubs | D2C. endpoints. utgående. eventHubs |  Routning: meddelanden levererade till Händelsehubben  | 
| Microsoft. Devices/IotHubs | D2C. endpoints. utgående. Builtity. events |  Routning: meddelanden som levereras till meddelanden/händelser  | 
| Microsoft. Devices/IotHubs | konfigurationer |  Konfigurations mått  | 
| Microsoft. Devices/IotHubs | C2DMessagesExpired |  C2D meddelanden har förfallit (förhands granskning)  | 
| Microsoft. Devices/IotHubs | C2D. delad. Update. lyckades |  Lyckades dubbla uppdateringar från Server delen  | 
| Microsoft. Devices/IotHubs | C2D. dubbla. Update. size |  Storlek på dubbla uppdateringar från Server delen  | 
| Microsoft. Devices/IotHubs | C2D. delad. Update. Failure |  Misslyckade dubbla uppdateringar från Server delen  | 
| Microsoft. Devices/IotHubs | C2D. delad. lyckades |  Lyckades dubbla läspaket från Server delen  | 
| Microsoft. Devices/IotHubs | C2D., delad. storlek |  Svars storlek för dubbla läsningar från Server delen  | 
| Microsoft. Devices/IotHubs | C2D. delad. |  Det gick inte att dubbla läsningar från Server delen  | 
| Microsoft. Devices/IotHubs | C2D. Methods. Success |  Direkta metod anrop  | 
| Microsoft. Devices/IotHubs | C2D. Methods. responseSize |  Svars storlek för direkta metod anrop  | 
| Microsoft. Devices/IotHubs | C2D. Methods. requestSize |  Begär ande storlek för direkta metod anrop  | 
| Microsoft. Devices/IotHubs | C2D. Methods. Failure |  Misslyckade direkta metod anrop  | 
| Microsoft. Devices/IotHubs | C2D. commands. rekasta. Success |  Avvisade C2D-meddelanden  | 
| Microsoft. Devices/IotHubs | C2D. commands. utgående. Complete. lyckades |  C2D meddelande leveranser har slutförts  | 
| Microsoft. Devices/IotHubs | C2D. commands. utgående. Abandon. lyckades |  Övergivna C2D-meddelanden  | 
| Microsoft. Devices/provisioningServices | RegistrationAttempts |  Registrerings försök  | 
| Microsoft. Devices/provisioningServices | DeviceAssignments |  Tilldelade enheter  | 
| Microsoft. Devices/provisioningServices | AttestationAttempts |  Attesterings försök  | 
| Microsoft.DocumentDB/databaseAccounts | TotalRequestUnits |  Totalt antal enheter för programbegäran  | 
| Microsoft.DocumentDB/databaseAccounts | TotalRequests |  Totalt antal förfrågningar  | 
| Microsoft.DocumentDB/databaseAccounts | MongoRequests |  Mongo-begäranden  | 
| Microsoft.DocumentDB/databaseAccounts | MongoRequestCharge |  Mongo begär ande avgift  | 
| Microsoft. EventGrid/Domains | PublishSuccessLatencyInMs |  Slutför svars tid för publicering  | 
| Microsoft. EventGrid/Domains | PublishSuccessCount |  Publicerade händelser  | 
| Microsoft. EventGrid/Domains | PublishFailCount |  Publicera misslyckade händelser  | 
| Microsoft. EventGrid/Domains | MatchedEventCount |  Matchade händelser  | 
| Microsoft. EventGrid/Domains | DroppedEventCount |  Ignorerade händelser  | 
| Microsoft. EventGrid/Domains | DeliverySuccessCount |  Levererade händelser  | 
| Microsoft. EventGrid/Domains | DeadLetteredCount |  Obeställbara, Brevade händelser  | 
| Microsoft. EventGrid/eventSubscriptions | MatchedEventCount |  Matchade händelser  | 
| Microsoft. EventGrid/eventSubscriptions | DroppedEventCount |  Ignorerade händelser  | 
| Microsoft. EventGrid/eventSubscriptions | DeliverySuccessCount |  Levererade händelser  | 
| Microsoft. EventGrid/eventSubscriptions | DeadLetteredCount |  Obeställbara, Brevade händelser  | 
| Microsoft. EventGrid/extensionTopics | UnmatchedEventCount |  Omatchade händelser  | 
| Microsoft. EventGrid/extensionTopics | PublishSuccessLatencyInMs |  Slutför svars tid för publicering  | 
| Microsoft. EventGrid/extensionTopics | PublishSuccessCount |  Publicerade händelser  | 
| Microsoft. EventGrid/extensionTopics | PublishFailCount |  Publicera misslyckade händelser  | 
| Microsoft. EventGrid/ämnen | UnmatchedEventCount |  Omatchade händelser  | 
| Microsoft. EventGrid/ämnen | PublishSuccessLatencyInMs |  Slutför svars tid för publicering  | 
| Microsoft. EventGrid/ämnen | PublishSuccessCount |  Publicerade händelser  | 
| Microsoft. EventGrid/ämnen | PublishFailCount |  Publicera misslyckade händelser  | 
| Microsoft. EventHub/Clusters | OutgoingMessages |  Utgående meddelanden  | 
| Microsoft. EventHub/Clusters | OutgoingBytes |  Utgående byte.  | 
| Microsoft. EventHub/Clusters | IncomingRequests |  Inkommande förfrågningar  | 
| Microsoft. EventHub/Clusters | IncomingMessages |  Inkommande meddelanden  | 
| Microsoft. EventHub/Clusters | IncomingBytes |  Inkommande byte.  | 
| Microsoft. EventHub/Namespaces | SVRBSY |  Serverns upptaget fel (inaktuellt)  | 
| Microsoft. EventHub/Namespaces | SUCCREQ |  Lyckade förfrågningar (inaktuellt)  | 
| Microsoft. EventHub/Namespaces | OUTMSGS |  Utgående meddelanden (inaktuella)  | 
| Microsoft. EventHub/Namespaces | OutgoingMessages |  Utgående meddelanden  | 
| Microsoft. EventHub/Namespaces | OutgoingBytes |  Utgående byte.  | 
| Microsoft. EventHub/Namespaces | MISCERR |  Andra fel (inaktuellt)  | 
| Microsoft. EventHub/Namespaces | MELLAN |  Interna Server fel (inaktuellt)  | 
| Microsoft. EventHub/Namespaces | INREQS |  Inkommande begär Anden (inaktuellt)  | 
| Microsoft. EventHub/Namespaces | INMSGS |  Inkommande meddelanden (inaktuella)  | 
| Microsoft. EventHub/Namespaces | IncomingRequests |  Inkommande förfrågningar  | 
| Microsoft. EventHub/Namespaces | IncomingMessages |  Inkommande meddelanden  | 
| Microsoft. EventHub/Namespaces | IncomingBytes |  Inkommande byte.  | 
| Microsoft. EventHub/Namespaces | FAILREQ |  Misslyckade förfrågningar (inaktuellt)  | 
| Microsoft. EventHub/Namespaces | EHOUTMSGS |  Utgående meddelanden (inaktuellt)  | 
| Microsoft. EventHub/Namespaces | EHOUTMBS |  Utgående byte (inaktuell) (inaktuell)  | 
| Microsoft. EventHub/Namespaces | EHOUTBYTES |  Utgående byte (inaktuellt)  | 
| Microsoft. EventHub/Namespaces | EHINMSGS |  Inkommande meddelanden (inaktuellt)  | 
| Microsoft. EventHub/Namespaces | EHINMBS |  Inkommande byte (inaktuell) (inaktuell)  | 
| Microsoft. EventHub/Namespaces | EHINBYTES |  Inkommande byte (inaktuellt)  | 
| Microsoft. EventHub/Namespaces | EHAMSGS |  Arkivera meddelanden (inaktuellt)  | 
| Microsoft. EventHub/Namespaces | EHAMBS |  Arkiv meddelande genom strömning (inaktuellt)  | 
| Microsoft. EventHub/Namespaces | EHABL |  Arkivera efter släpning meddelanden (inaktuellt)  | 
| Microsoft. HDInsight/kluster | NumActiveWorkers |  Antal aktiva arbetare  | 
| Microsoft. HDInsight/kluster | GatewayRequests |  Gateway-begäranden  | 
| Microsoft. HDInsight/kluster | CategorizedGatewayRequests |  Kategoriserade Gateway-begäranden  | 
| Microsoft. Insights/AutoscaleSettings | ScaleActionsInitiated |  Initierade skalnings åtgärder  | 
| Microsoft. Insights/komponenter | spårning/antal |  Spårningar  | 
| Microsoft. Insights/komponenter | performanceCounters/requestsPerSecond |  Hastighet för HTTP-begäran  | 
| Microsoft. Insights/komponenter | performanceCounters/requestsInQueue |  HTTP-begäranden i program kön  | 
| Microsoft. Insights/komponenter | performanceCounters/exceptionsPerSecond |  Undantags frekvens  | 
| Microsoft. Insights/komponenter | pageViews/antal |  Sid visningar  | 
| Microsoft. Insights/komponenter | undantag/antal |  Undantag  | 
| Microsoft. Kusto/kluster | StreamingIngestResults |  Resultat för strömningsinmatning  | 
| Microsoft. Kusto/kluster | StreamingIngestDuration |  Varaktighet för strömningsinmatning  | 
| Microsoft. Kusto/kluster | StreamingIngestDataRate |  Datahastighet för strömningsinmatning  | 
| Microsoft. Kusto/kluster | SteamingIngestRequestRate |  Förfrågningsfrekvens för strömningsinmatning  | 
| Microsoft. Kusto/kluster | QueryDuration |  Frågans varaktighet  | 
| Microsoft. Kusto/kluster | KeepAlive |  Behåll Alive  | 
| Microsoft. Kusto/kluster | IngestionVolumeInMB |  Inmatnings volym (i MB)  | 
| Microsoft. Kusto/kluster | IngestionUtilization |  Datainmatningsanvändning  | 
| Microsoft. Kusto/kluster | IngestionResult |  Inmatnings resultat  | 
| Microsoft. Kusto/kluster | IngestionLatencyInSeconds |  Inmatnings svars tid (i sekunder)  | 
| Microsoft. Kusto/kluster | ExportUtilization |  Exportanvändning  | 
| Microsoft. Kusto/kluster | EventsProcessedForEventHubs |  Bearbetade händelser (för Event/IoT-hubbar)  | 
| Microsoft. Kusto/kluster | Processor |  Processor  | 
| Microsoft. Kusto/kluster | ContinuousExportResult |  Resultat av kontinuerlig export  | 
| Microsoft. Kusto/kluster | ContinuousExportPendingCount |  Antal väntande pågående export  | 
| Microsoft. Kusto/kluster | ContinuousExportNumOfRecordsExported |  Kontinuerlig export-antal exporterade poster  | 
| Microsoft. Kusto/kluster | ContinuousExportMaxLatenessMinutes |  Högsta antal förseningar i minuter för kontinuerlig export  | 
| Microsoft. Kusto/kluster | CacheUtilization |  Användning av cache  | 
| Microsoft. Logic/integrationServiceEnvironments | TriggerThrottledEvents |  Utlös begränsade händelser  | 
| Microsoft. Logic/integrationServiceEnvironments | TriggersSucceeded |  Lyckade utlösare   | 
| Microsoft. Logic/integrationServiceEnvironments | TriggersStarted |  Startade utlösare   | 
| Microsoft. Logic/integrationServiceEnvironments | TriggersSkipped |  Ignorerade utlösare  | 
| Microsoft. Logic/integrationServiceEnvironments | TriggersFired |  Utlöst utlösare   | 
| Microsoft. Logic/integrationServiceEnvironments | TriggersFailed |  Misslyckade utlösare   | 
| Microsoft. Logic/integrationServiceEnvironments | TriggersCompleted |  Slutförda utlösare   | 
| Microsoft. Logic/integrationServiceEnvironments | RunThrottledEvents |  Kör begränsade händelser  | 
| Microsoft. Logic/integrationServiceEnvironments | RunStartThrottledEvents |  Kör starta begränsade händelser  | 
| Microsoft. Logic/integrationServiceEnvironments | RunsSucceeded |  Lyckade körningar  | 
| Microsoft. Logic/integrationServiceEnvironments | RunsStarted |  Startade körningar  | 
| Microsoft. Logic/integrationServiceEnvironments | RunsFailed |  Misslyckade körningar  | 
| Microsoft. Logic/integrationServiceEnvironments | RunsCompleted |  Slutförda körningar  | 
| Microsoft. Logic/integrationServiceEnvironments | RunsCancelled |  Körningarna har avbrutits  | 
| Microsoft. Logic/integrationServiceEnvironments | RunFailurePercentage |  Procent körnings avbrott  | 
| Microsoft. Logic/integrationServiceEnvironments | ActionThrottledEvents |  Åtgärds begränsade händelser  | 
| Microsoft. Logic/integrationServiceEnvironments | ActionsSucceeded |  Genomförda åtgärder   | 
| Microsoft. Logic/integrationServiceEnvironments | ActionsStarted |  Startade åtgärder   | 
| Microsoft. Logic/integrationServiceEnvironments | ActionsSkipped |  Åtgärder hoppades över   | 
| Microsoft. Logic/integrationServiceEnvironments | ActionsFailed |  Misslyckade åtgärder   | 
| Microsoft. Logic/integrationServiceEnvironments | ActionsCompleted |  Slutförda åtgärder   | 
| Microsoft. Logic/arbets flöden | TriggerThrottledEvents |  Utlös begränsade händelser  | 
| Microsoft. Logic/arbets flöden | TriggersSucceeded |  Lyckade utlösare   | 
| Microsoft. Logic/arbets flöden | TriggersStarted |  Startade utlösare   | 
| Microsoft. Logic/arbets flöden | TriggersSkipped |  Ignorerade utlösare  | 
| Microsoft. Logic/arbets flöden | TriggersFired |  Utlöst utlösare   | 
| Microsoft. Logic/arbets flöden | TriggersFailed |  Misslyckade utlösare   | 
| Microsoft. Logic/arbets flöden | TriggersCompleted |  Slutförda utlösare   | 
| Microsoft. Logic/arbets flöden | TotalBillableExecutions |  Totalt antal fakturerbara körningar  | 
| Microsoft. Logic/arbets flöden | RunThrottledEvents |  Kör begränsade händelser  | 
| Microsoft. Logic/arbets flöden | RunStartThrottledEvents |  Kör starta begränsade händelser  | 
| Microsoft. Logic/arbets flöden | RunsSucceeded |  Lyckade körningar  | 
| Microsoft. Logic/arbets flöden | RunsStarted |  Startade körningar  | 
| Microsoft. Logic/arbets flöden | RunsFailed |  Misslyckade körningar  | 
| Microsoft. Logic/arbets flöden | RunsCompleted |  Slutförda körningar  | 
| Microsoft. Logic/arbets flöden | RunsCancelled |  Körningarna har avbrutits  | 
| Microsoft. Logic/arbets flöden | RunFailurePercentage |  Procent körnings avbrott  | 
| Microsoft. Logic/arbets flöden | BillingUsageStorageConsumption |  Fakturerings användning för lagrings förbruknings körningar  | 
| Microsoft. Logic/arbets flöden | BillingUsageStorageConsumption |  Fakturerings användning för lagrings förbruknings körningar  | 
| Microsoft. Logic/arbets flöden | BillingUsageStandardConnector |  Fakturerings användning för standard kopplings körningar  | 
| Microsoft. Logic/arbets flöden | BillingUsageStandardConnector |  Fakturerings användning för standard kopplings körningar  | 
| Microsoft. Logic/arbets flöden | BillingUsageNativeOperation |  Fakturerings användning för intern åtgärds körningar  | 
| Microsoft. Logic/arbets flöden | BillingUsageNativeOperation |  Fakturerings användning för intern åtgärds körningar  | 
| Microsoft. Logic/arbets flöden | BillableTriggerExecutions |  Fakturerbara Utlösar-körningar  | 
| Microsoft. Logic/arbets flöden | BillableActionExecutions |  Fakturerbara åtgärds körningar  | 
| Microsoft. Logic/arbets flöden | ActionThrottledEvents |  Åtgärds begränsade händelser  | 
| Microsoft. Logic/arbets flöden | ActionsSucceeded |  Genomförda åtgärder   | 
| Microsoft. Logic/arbets flöden | ActionsStarted |  Startade åtgärder   | 
| Microsoft. Logic/arbets flöden | ActionsSkipped |  Åtgärder hoppades över   | 
| Microsoft. Logic/arbets flöden | ActionsFailed |  Misslyckade åtgärder   | 
| Microsoft. Logic/arbets flöden | ActionsCompleted |  Slutförda åtgärder   | 
| Microsoft. Network/frontdoors | WebApplicationFirewallRequestCount |  Antal begär Anden om webb programs brand vägg  | 
| Microsoft. Network/frontdoors | TotalLatency |  Total svars tid  | 
| Microsoft. Network/frontdoors | ResponseSize |  Svars storlek  | 
| Microsoft. Network/frontdoors | RequestSize |  Begär ande storlek  | 
| Microsoft. Network/frontdoors | RequestCount |  Antal begäranden  | 
| Microsoft. Network/frontdoors | BillableResponseSize |  Fakturerbart svars storlek  | 
| Microsoft. Network/frontdoors | BackendRequestLatency |  Svars tid för Server del  | 
| Microsoft. Network/frontdoors | BackendRequestCount |  Antal Server dels begär Anden  | 
| Microsoft. Network/frontdoors | BackendHealthPercentage |  Server delens hälso procent  | 
| Microsoft. Network/trafficManagerProfiles | QpsByEndpoint |  Returnerade frågor efter slut punkt  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | schemalagt. väntar |  Väntande schemalagda meddelanden  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | registrering. Update |  Registrera uppdaterings åtgärder  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | registrering. Hämta |  Läs åtgärder för registrering  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | registrering. Delete |  Åtgärder för att ta bort registrering  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | registrering. skapa |  Skapa åtgärder för registrering  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | registrering. alla |  Registrerings åtgärder  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. WNS. wrongtoken |  WNS-auktoriseringsfel (fel token)  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. WNS. tokenproviderunreachable |  WNS-auktoriseringsfel (kan inte kontaktas)  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. WNS. begränsad |  WNS-begränsade meddelanden  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. WNS. lyckades |  WNS slutförda meddelanden  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. WNS. pnserror |  WNS-fel  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. WNS. invalidtoken |  WNS-auktoriseringsfel (ogiltig token)  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. WNS. invalidnotificationsize |  WNS ogiltig meddelande storleks fel  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. WNS. invalidnotificationformat |  WNS ogiltigt meddelande format  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. WNS. invalidcredentials |  WNS-auktoriseringsfel (ogiltiga autentiseringsuppgifter)  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. WNS. expiredchannel |  WNS utgången kanal fel  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. WNS. släppt |  WNS borttagna meddelanden  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. WNS. channelthrottled |  WNS-kanalen är begränsad  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. WNS. channeldisconnected |  WNS-kanalen är frånkopplad  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. WNS. badchannel |  WNS felaktigt kanal fel  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. WNS. authenticationerror |  WNS-autentiseringsfel  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. MPNS. begränsad |  MPNS-begränsade meddelanden  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. MPNS. lyckades |  MPNS slutförda meddelanden  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. MPNS. pnserror |  MPNS-fel  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. MPNS. invalidnotificationformat |  MPNS ogiltigt meddelande format  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. MPNS. invalidcredentials |  MPNS ogiltiga autentiseringsuppgifter  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. MPNS. släppt |  MPNS borttagna meddelanden  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. MPNS. channeldisconnected |  MPNS-kanalen är frånkopplad  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. MPNS. badchannel |  MPNS felaktigt kanal fel  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. MPNS. authenticationerror |  MPNS-autentiseringsfel  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. GCM. wrongchannel |  GCM fel kanal fel  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. GCM. begränsad |  GCM-begränsade meddelanden  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. GCM. lyckades |  GCM slutförda meddelanden  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. GCM. pnserror |  GCM-fel  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. GCM. invalidnotificationsize |  GCM ogiltig meddelande storleks fel  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. GCM. invalidnotificationformat |  GCM ogiltigt meddelande format  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. GCM. invalidcredentials |  GCM-auktoriseringsfel (ogiltiga autentiseringsuppgifter)  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. GCM. expiredchannel |  GCM utgången kanal fel  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. GCM. badchannel |  GCM felaktigt kanal fel  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. GCM. authenticationerror |  GCM-autentiseringsfel  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. APN. lyckades |  APN-lyckade meddelanden  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. APN. pnserror |  APN-fel  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. APN. invalidnotificationsize |  APN ogiltigt meddelande storleks fel  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. APN. invalidcredentials |  APN-auktoriseringsfel  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. APN. expiredchannel |  APN utgånget i kanal fel  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. APN. badchannel |  APN dåligt kanal fel  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. allpns. lyckades |  Lyckade aviseringar  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. allpns. pnserror |  Fel i externt meddelande system  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. allpns. invalidpayload |  Nytto Last fel  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | utgående. allpns. channelerror |  Kanal fel  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | notificationhub. push-meddelanden |  Alla utgående meddelanden  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | installation. upsert |  Skapa eller uppdatera installations åtgärder  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | installation. patch |  Installation av korrigerings åtgärder  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | installation. get |  Hämta installations åtgärder  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | installation. Delete |  Ta bort installations åtgärder  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | installation. alla |  Installations hanterings åtgärder  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | inkommande. schemalagd. Avbryt |  Schemalagda push-meddelanden har avbrutits  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | inkommande. schemalagd |  Schemalagda push-meddelanden har skickats  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | inkommande. alla. förfrågningar |  Alla inkommande begär Anden  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | inkommande. alla. failedrequests |  Alla inkommande misslyckade förfrågningar  | 
| Microsoft. NotificationHubs/Namespaces/NotificationHubs | inkommande |  Inkommande meddelanden  | 
| Microsoft. OperationalInsights/arbets ytor | Pulsslag |  Pulsslag  | 
| Microsoft. Relay/namnrymder | BytesTransferred |  BytesTransferred  | 
| Microsoft. Service Bus/namnrymder | OutgoingMessages |  Utgående meddelanden  | 
| Microsoft. Service Bus/namnrymder | IncomingRequests |  Inkommande förfrågningar  | 
| Microsoft. Service Bus/namnrymder | IncomingMessages |  Inkommande meddelanden  | 
| Microsoft. SignalRService/SignalR | UserErrors |  Användar fel  | 
| Microsoft. SignalRService/SignalR | SystemErrors |  Systemfel  | 
| Microsoft. SignalRService/SignalR | OutboundTraffic |  Utgående trafik  | 
| Microsoft. SignalRService/SignalR | MessageCount |  Antal meddelanden  | 
| Microsoft. SignalRService/SignalR | InboundTraffic |  Inkommande trafik  | 
| Microsoft. SignalRService/SignalR | ConnectionCount |  Antal anslutningar  | 
| Microsoft. SQL/managedInstances | avg_cpu_percent |  Genomsnittlig CPU-procent  | 
| Microsoft. SQL/Servers | dtu_used |  Använt DTU  | 
| Microsoft. SQL/Servers | dtu_consumption_percent |  DTU-procent  | 
| Microsoft. SQL/Servers/databaser | xtp_storage_percent |  In-Memory OLTP-lagring i procent  | 
| Microsoft. SQL/Servers/databaser | workers_percent |  Arbetare i procent  | 
| Microsoft. SQL/Servers/databaser | sessions_percent |  Sessioner i procent  | 
| Microsoft. SQL/Servers/databaser | physical_data_read_percent |  Data IO-procent  | 
| Microsoft. SQL/Servers/databaser | log_write_percent |  Logg IO-procent  | 
| Microsoft. SQL/Servers/databaser | dwu_used |  DWU som används  | 
| Microsoft. SQL/Servers/databaser | dwu_consumption_percent |  DWU procent  | 
| Microsoft. SQL/Servers/databaser | dtu_used |  Använt DTU  | 
| Microsoft. SQL/Servers/databaser | dtu_consumption_percent |  DTU-procent  | 
| Microsoft. SQL/Servers/databaser | hamn |  Dödlägen  | 
| Microsoft. SQL/Servers/databaser | cpu_used |  Använd CPU  | 
| Microsoft. SQL/Servers/databaser | cpu_percent |  CPU-procent  | 
| Microsoft. SQL/Servers/databaser | connection_successful |  Lyckade anslutningar  | 
| Microsoft. SQL/Servers/databaser | connection_failed |  Misslyckade anslutningar  | 
| Microsoft. SQL/Servers/databaser | cache_hit_percent |  Procent andel cacheträffar  | 
| Microsoft. SQL/Servers/databaser | blocked_by_firewall |  Blockerad av brandvägg  | 
| Microsoft. SQL/Servers/elasticPools | xtp_storage_percent |  In-Memory OLTP-lagring i procent  | 
| Microsoft. SQL/Servers/elasticPools | workers_percent |  Arbetare i procent  | 
| Microsoft. SQL/Servers/elasticPools | sessions_percent |  Sessioner i procent  | 
| Microsoft. SQL/Servers/elasticPools | physical_data_read_percent |  Data IO-procent  | 
| Microsoft. SQL/Servers/elasticPools | log_write_percent |  Logg IO-procent  | 
| Microsoft. SQL/Servers/elasticPools | eDTU_used |  eDTU använt  | 
| Microsoft. SQL/Servers/elasticPools | dtu_consumption_percent |  DTU-procent  | 
| Microsoft. SQL/Servers/elasticPools | cpu_percent |  CPU-procent  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | TotalFrontEnds |  Totalt antal klient delar  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | SmallAppServicePlanInstances |  Små App Service planera arbetare  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | Begäranden |  Begäranden  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | MemoryPercentage |  Minnes procent  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | MediumAppServicePlanInstances |  Medel App Service planera arbetare  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | LargeAppServicePlanInstances |  Stora App Service planera arbetare  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | HttpQueueLength |  Längd på http-kö  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | Http5xx |  Http-serverfel  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | Http4xx |  Http-4xx  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | Http406 |  Http 406  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | Http404 |  Http 404  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | Http403 |  Http 403  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | Http401 |  Http 401  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | Http3xx |  Http-3xx  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | Http2xx |  Http-2xx  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | Http101 |  Http 101  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | DiskQueueLength |  Diskkölängd  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | CpuPercentage |  CPU-procent  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | Bytes sent |  Data ut  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | BytesReceived |  Data i  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | AverageResponseTime |  Genomsnittlig svars tid  | 
| Microsoft. Web/hostingEnvironments/multiRolePools | ActiveRequests |  Aktiva begär Anden  | 
| Microsoft. Web/hostingEnvironments/workerPools | WorkersUsed |  Använda arbetare  | 
| Microsoft. Web/hostingEnvironments/workerPools | WorkersTotal |  Totalt antal arbetare  | 
| Microsoft. Web/hostingEnvironments/workerPools | WorkersAvailable |  Tillgängliga arbetare  | 
| Microsoft. Web/hostingEnvironments/workerPools | MemoryPercentage |  Minnes procent  | 
| Microsoft. Web/hostingEnvironments/workerPools | CpuPercentage |  CPU-procent  | 
| Microsoft. Web/Server grupper | TcpTimeWait |  Väntan på TCP-tid  | 
| Microsoft. Web/Server grupper | TcpSynSent |  TCP-syn har skickats  | 
| Microsoft. Web/Server grupper | TcpSynReceived |  TCP-syn mottagen  | 
| Microsoft. Web/Server grupper | TcpLastAck |  TCP-senaste ack  | 
| Microsoft. Web/Server grupper | TcpFinWait2 |  TCP räntetrans wait 2  | 
| Microsoft. Web/Server grupper | TcpFinWait1 |  TCP räntetrans wait 1  | 
| Microsoft. Web/Server grupper | TcpEstablished |  TCP upprättad  | 
| Microsoft. Web/Server grupper | TcpClosing |  TCP-stängning  | 
| Microsoft. Web/Server grupper | TcpCloseWait |  Väntan på TCP-stängning  | 
| Microsoft. Web/Server grupper | MemoryPercentage |  Minnes procent  | 
| Microsoft. Web/Server grupper | HttpQueueLength |  Längd på http-kö  | 
| Microsoft. Web/Server grupper | DiskQueueLength |  Diskkölängd  | 
| Microsoft. Web/Server grupper | CpuPercentage |  CPU-procent  | 
| Microsoft. Web/Server grupper | Bytes sent |  Data ut  | 
| Microsoft. Web/Server grupper | BytesReceived |  Data i  | 
| Microsoft. Web/Sites | TotalAppDomainsUnloaded |  Totalt antal app-domäner som har inaktiverats  | 
| Microsoft. Web/Sites | TotalAppDomains |  Totalt antal app-domäner  | 
| Microsoft. Web/Sites | Konversation |  Antal trådar  | 
| Microsoft. Web/Sites | RequestsInApplicationQueue |  Begär anden i program kön  | 
| Microsoft. Web/Sites | Begäranden |  Begäranden  | 
| Microsoft. Web/Sites | PrivateBytes |  Privata byte  | 
| Microsoft. Web/Sites | MemoryWorkingSet |  Minnes arbets mängd  | 
| Microsoft. Web/Sites | IoWriteOperationsPerSecond |  I/o-Skriv åtgärder per sekund  | 
| Microsoft. Web/Sites | IoWriteBytesPerSecond |  Skrivna byte i i/o per sekund  | 
| Microsoft. Web/Sites | IoReadOperationsPerSecond |  IO-Läs åtgärder per sekund  | 
| Microsoft. Web/Sites | IoReadBytesPerSecond |  IO-lästa byte per sekund  | 
| Microsoft. Web/Sites | IoOtherOperationsPerSecond |  Andra i/o-åtgärder per sekund  | 
| Microsoft. Web/Sites | IoOtherBytesPerSecond |  I/o andra byte per sekund  | 
| Microsoft. Web/Sites | HttpResponseTime |  Svars tid  | 
| Microsoft. Web/Sites | Http5xx |  Http-serverfel  | 
| Microsoft. Web/Sites | Http4xx |  Http-4xx  | 
| Microsoft. Web/Sites | Http406 |  Http 406  | 
| Microsoft. Web/Sites | Http404 |  Http 404  | 
| Microsoft. Web/Sites | Http403 |  Http 403  | 
| Microsoft. Web/Sites | Http401 |  Http 401  | 
| Microsoft. Web/Sites | Http3xx |  Http-3xx  | 
| Microsoft. Web/Sites | Http2xx |  Http-2xx  | 
| Microsoft. Web/Sites | Http101 |  Http 101  | 
| Microsoft. Web/Sites | HealthCheckStatus |  Hälso kontroll status  | 
| Microsoft. Web/Sites | Konsolindataobjekt |  Antal referenser  | 
| Microsoft. Web/Sites | Gen2Collections |  Gen 2 skräp insamling  | 
| Microsoft. Web/Sites | Gen1Collections |  Skräp insamling för gen 1  | 
| Microsoft. Web/Sites | Gen0Collections |  Skräp insamling för gen 0  | 
| Microsoft. Web/Sites | FunctionExecutionUnits |  Funktions körnings enheter  | 
| Microsoft. Web/Sites | FunctionExecutionCount |  Funktions körnings antal  | 
| Microsoft. Web/Sites | CurrentAssemblies |  Aktuella sammansättningar  | 
| Microsoft. Web/Sites | CpuTime |  CPU-tid  | 
| Microsoft. Web/Sites | Bytes sent |  Data ut  | 
| Microsoft. Web/Sites | BytesReceived |  Data i  | 
| Microsoft. Web/Sites | AverageResponseTime |  Genomsnittlig svars tid  | 
| Microsoft. Web/Sites | AverageMemoryWorkingSet |  Genomsnittlig arbets mängd för minne  | 
| Microsoft. Web/Sites | AppConnections |  Anslutningar  | 
| Microsoft. Web/Sites/lotss | TotalAppDomainsUnloaded |  Totalt antal app-domäner som har inaktiverats  | 
| Microsoft. Web/Sites/lotss | TotalAppDomains |  Totalt antal app-domäner  | 
| Microsoft. Web/Sites/lotss | Konversation |  Antal trådar  | 
| Microsoft. Web/Sites/lotss | RequestsInApplicationQueue |  Begär anden i program kön  | 
| Microsoft. Web/Sites/lotss | Begäranden |  Begäranden  | 
| Microsoft. Web/Sites/lotss | PrivateBytes |  Privata byte  | 
| Microsoft. Web/Sites/lotss | MemoryWorkingSet |  Minnes arbets mängd  | 
| Microsoft. Web/Sites/lotss | IoWriteOperationsPerSecond |  I/o-Skriv åtgärder per sekund  | 
| Microsoft. Web/Sites/lotss | IoWriteBytesPerSecond |  Skrivna byte i i/o per sekund  | 
| Microsoft. Web/Sites/lotss | IoReadOperationsPerSecond |  IO-Läs åtgärder per sekund  | 
| Microsoft. Web/Sites/lotss | IoReadBytesPerSecond |  IO-lästa byte per sekund  | 
| Microsoft. Web/Sites/lotss | IoOtherOperationsPerSecond |  Andra i/o-åtgärder per sekund  | 
| Microsoft. Web/Sites/lotss | IoOtherBytesPerSecond |  I/o andra byte per sekund  | 
| Microsoft. Web/Sites/lotss | HttpResponseTime |  Svars tid  | 
| Microsoft. Web/Sites/lotss | Http5xx |  Http-serverfel  | 
| Microsoft. Web/Sites/lotss | Http4xx |  Http-4xx  | 
| Microsoft. Web/Sites/lotss | Http406 |  Http 406  | 
| Microsoft. Web/Sites/lotss | Http404 |  Http 404  | 
| Microsoft. Web/Sites/lotss | Http403 |  Http 403  | 
| Microsoft. Web/Sites/lotss | Http401 |  Http 401  | 
| Microsoft. Web/Sites/lotss | Http3xx |  Http-3xx  | 
| Microsoft. Web/Sites/lotss | Http2xx |  Http-2xx  | 
| Microsoft. Web/Sites/lotss | Http101 |  Http 101  | 
| Microsoft. Web/Sites/lotss | HealthCheckStatus |  Hälso kontroll status  | 
| Microsoft. Web/Sites/lotss | Konsolindataobjekt |  Antal referenser  | 
| Microsoft. Web/Sites/lotss | Gen2Collections |  Gen 2 skräp insamling  | 
| Microsoft. Web/Sites/lotss | Gen1Collections |  Skräp insamling för gen 1  | 
| Microsoft. Web/Sites/lotss | Gen0Collections |  Skräp insamling för gen 0  | 
| Microsoft. Web/Sites/lotss | FunctionExecutionUnits |  Funktions körnings enheter  | 
| Microsoft. Web/Sites/lotss | FunctionExecutionCount |  Funktions körnings antal  | 
| Microsoft. Web/Sites/lotss | CurrentAssemblies |  Aktuella sammansättningar  | 
| Microsoft. Web/Sites/lotss | CpuTime |  CPU-tid  | 
| Microsoft. Web/Sites/lotss | Bytes sent |  Data ut  | 
| Microsoft. Web/Sites/lotss | BytesReceived |  Data i  | 
| Microsoft. Web/Sites/lotss | AverageResponseTime |  Genomsnittlig svars tid  | 
| Microsoft. Web/Sites/lotss | AverageMemoryWorkingSet |  Genomsnittlig arbets mängd för minne  | 
| Microsoft. Web/Sites/lotss | AppConnections |  Anslutningar  | 
| Microsoft. SQL/Servers/databaser | cpu_percent | CPU-procent | 
| Microsoft. SQL/Servers/databaser | physical_data_read_percent | Data IO-procent | 
| Microsoft. SQL/Servers/databaser | log_write_percent | Logg IO-procent | 
| Microsoft. SQL/Servers/databaser | dtu_consumption_percent | DTU-procent | 
| Microsoft. SQL/Servers/databaser | connection_successful | Lyckade anslutningar | 
| Microsoft. SQL/Servers/databaser | connection_failed | Misslyckade anslutningar | 
| Microsoft. SQL/Servers/databaser | blocked_by_firewall | Blockerad av brandvägg | 
| Microsoft. SQL/Servers/databaser | hamn | Dödlägen | 
| Microsoft. SQL/Servers/databaser | xtp_storage_percent | In-Memory OLTP-lagring i procent | 
| Microsoft. SQL/Servers/databaser | workers_percent | Arbetare i procent | 
| Microsoft. SQL/Servers/databaser | sessions_percent | Sessioner i procent | 
| Microsoft. SQL/Servers/databaser | dtu_used | Använt DTU | 
| Microsoft. SQL/Servers/databaser | cpu_used | Använd CPU | 
| Microsoft. SQL/Servers/databaser | dwu_consumption_percent | DWU procent | 
| Microsoft. SQL/Servers/databaser | dwu_used | DWU som används | 
| Microsoft. SQL/Servers/databaser | cache_hit_percent | Procent andel cacheträffar | 
| Microsoft. SQL/Servers/databaser | wlg_allocation_relative_to_system_percent | Allokering av arbets belastnings grupp efter system procent | 
| Microsoft. SQL/Servers/databaser | wlg_allocation_relative_to_wlg_effective_cap_percent | Allokering av arbets belastnings grupper efter max resurs procent | 
| Microsoft. SQL/Servers/databaser | wlg_active_queries | Aktiva frågor för arbets belastnings grupp | 
| Microsoft. SQL/Servers/databaser | wlg_queued_queries | Köade frågor i arbets belastnings gruppen | 
| Microsoft. SQL/Servers/databaser | active_queries | Aktiva frågor | 
| Microsoft. SQL/Servers/databaser | queued_queries | Köade frågor | 
| Microsoft. SQL/Servers/databaser | wlg_active_queries_timeouts | Fråge tids gränser för arbets belastnings grupp | 
| Microsoft. SQL/Servers/databaser | wlg_queued_queries_timeouts | Timeout för kö för arbets belastnings grupp | 
| Microsoft. SQL/Servers/databaser | wlg_effective_min_resource_percent | Effektiv minsta resurs procent | 
| Microsoft. SQL/Servers/databaser | wlg_effective_cap_resource_percent | Effektiv tak resurs procent | 
| Microsoft. SQL/Servers/elasticPools | cpu_percent | CPU-procent | 
| Microsoft. SQL/Servers/elasticPools | physical_data_read_percent | Data IO-procent | 
| Microsoft. SQL/Servers/elasticPools | log_write_percent | Logg IO-procent | 
| Microsoft. SQL/Servers/elasticPools | dtu_consumption_percent | DTU-procent | 
| Microsoft. SQL/Servers/elasticPools | workers_percent | Arbetare i procent | 
| Microsoft. SQL/Servers/elasticPools | sessions_percent | Sessioner i procent | 
| Microsoft. SQL/Servers/elasticPools | eDTU_used | eDTU använt | 
| Microsoft. SQL/Servers/elasticPools | xtp_storage_percent | In-Memory OLTP-lagring i procent | 
| Microsoft. SQL/Servers | dtu_consumption_percent | DTU-procent | 
| Microsoft. SQL/Servers | dtu_used | Använt DTU | 
| Microsoft. SQL/managedInstances | avg_cpu_percent | Genomsnittlig CPU-procent | 

