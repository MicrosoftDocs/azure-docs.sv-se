---
title: Övervaka Azure-IoT Hub
description: Börja här om du vill veta mer om hur du övervakar Azure-IoT Hub
author: robinsh
ms.author: robinsh
ms.topic: conceptual
ms.service: iot-hub
ms.custom: subject-monitoring
ms.date: 11/06/2020
ms.openlocfilehash: c9e6107e981d2bae96c5d9f368c8122d3ce245f0
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100570951"
---
# <a name="monitoring-azure-iot-hub"></a>Övervaka Azure-IoT Hub

När du har viktiga program och affärs processer som förlitar sig på Azure-resurser, vill du övervaka resurserna för deras tillgänglighet, prestanda och drift. I den här artikeln beskrivs övervaknings data som genereras av Azure IoT Hub och hur du kan använda funktionerna i Azure Monitor för att analysera och varna för dessa data.

## <a name="monitor-overview"></a>Övervaka översikt

**Översikts** sidan i Azure Portal för varje IoT-hubb innehåller diagram som innehåller några användnings mått, till exempel antalet meddelanden som används och antalet enheter som är anslutna till IoT Hub.

:::image type="content" source="media/monitor-iot-hub/overview-portal.png" alt-text="Översikts sidan standard mått diagram på IoT Hub.":::

Tänk på att värdet för antal meddelanden kan fördröjas med 1 minut och IoT Hub att värdet kan ibland studsa mellan högre och lägre värden vid uppdatering. Den här räknaren bör bara vara felaktig för värden som räknats över den senaste minuten.

Informationen som visas i översikts fönstret är användbar, men representerar bara en liten mängd övervaknings data som är tillgängliga för en IoT-hubb. Vissa övervaknings data samlas in automatiskt och är tillgängliga för analys så snart du skapar din IoT Hub. Du kan aktivera ytterligare typer av data insamling med en del konfiguration.

## <a name="what-is-azure-monitor"></a>Vad är Azure Monitor?

Azure IoT Hub skapar övervaknings data med hjälp av [Azure Monitor](../azure-monitor/overview.md), som är en fullständig stack övervaknings tjänst i Azure som innehåller en fullständig uppsättning funktioner för att övervaka dina Azure-resurser, förutom resurser i andra moln och lokalt.

Börja med artikeln [övervakning av Azure-resurser med Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md), vilket beskriver följande begrepp:

- Vad är Azure Monitor?
- Kostnader för övervakning
- Övervaknings data som samlas in i Azure
- Konfigurerar data insamling
- Standard verktyg i Azure för analys och avisering om övervaknings data

Följande avsnitt bygger på den här artikeln genom att beskriva specifika data som samlats in för Azure IoT Hub och tillhandahålla exempel för att konfigurera data insamling och analysera data med Azure-verktyg.

## <a name="monitoring-data"></a>Övervaka data

Azure IoT Hub samlar in samma typer av övervaknings data som andra Azure-resurser som beskrivs i [övervaknings data från Azure-resurser](../azure-monitor/essentials/monitor-azure-resource.md#monitoring-data).

Mer information om mått och loggar som skapats av Azure IoT Hub finns i [övervakning av azure IoT Hub data Reference](monitor-iot-hub-reference.md) .

> [!IMPORTANT]
> De händelser som genereras av IoT Hub tjänsten med hjälp av Azure Monitor resurs loggar är inte garanterat tillförlitliga eller beställda. Vissa händelser kan gå förlorade eller levereras i rätt ordning. Resurs loggar är inte heller avsedda att vara i real tid och det kan ta flera minuter innan händelserna loggas till ditt val av mål.

## <a name="collection-and-routing"></a>Samling och routning

Plattforms mått och aktivitets loggen samlas in och lagras automatiskt, men kan dirigeras till andra platser med hjälp av en diagnostisk inställning.

Resurs loggar samlas inte in och lagras förrän du skapar en diagnostisk inställning och dirigerar dem till en eller flera platser.

Mått och loggar kan dirigeras till flera platser, inklusive:
- Azure Monitor loggar lager via en associerad Log Analytics arbets yta. De kan analyseras med hjälp av Log Analytics.
- Azure Storage för arkivering och offline-analys 
- En Event Hubs-slutpunkt där de kan läsas av externa program, till exempel SIEM-verktyg från tredje part.

I Azure Portal kan du välja **diagnostikinställningar** under **övervakning** i den vänstra rutan i IoT Hub följt av **Lägg till diagnostikinställningar** för att skapa diagnostikinställningar som är begränsade till loggarna och plattforms måtten som har spridits av din IoT Hub.

Följande skärm bild visar en diagnostisk inställning för att vidarebefordra resurs logg typen *anslutnings åtgärder* och alla plattforms mått till en Log Analytics-arbetsyta.

:::image type="content" source="media/monitor-iot-hub/diagnostic-setting-portal.png" alt-text="Fönstret diagnostikinställningar för en IoT Hub.":::

Mer information om hur du skapar en diagnostisk inställning med hjälp av Azure Portal, CLI eller PowerShell finns i [skapa diagnostisk inställning för att samla in plattforms loggar och statistik i Azure](../azure-monitor/essentials/diagnostic-settings.md) . När du skapar en diagnostisk inställning anger du vilka kategorier av loggar som ska samlas in. Kategorierna för Azure IoT Hub visas under [resurs loggar i data referens för övervakning av Azure-IoT Hub](monitor-iot-hub-reference.md#resource-logs).

Tänk på följande när du dirigerar IoT Hub plattforms mått till andra platser:

- Det går inte att exportera följande plattforms mått via diagnostikinställningar: *anslutna enheter (för hands version)* och *Totalt antal enheter (för hands version)*.

- Flerdimensionella mått, till exempel vissa mått för [routning](monitor-iot-hub-reference.md#routing-metrics), exporteras för närvarande som sammansatta enkla mått för dimensions värden. Mer information finns i [Exportera plattforms mått till andra platser](../azure-monitor/essentials/metrics-supported.md#exporting-platform-metrics-to-other-locations).

## <a name="analyzing-metrics"></a>Analyserar mått

Du kan analysera mått för Azure IoT Hub med mått från andra Azure-tjänster med hjälp av Metric Explorer genom att öppna **mått** från **Azure Monitor** -menyn. Mer information om hur du använder det här verktyget finns i [komma igång med Azure Metrics Explorer](../azure-monitor/essentials/metrics-getting-started.md) .

I Azure Portal kan du välja **mått** under **övervakning** i den vänstra rutan i din IoT-hubb för att öppna området med mått Utforskaren som standard för de plattforms mått som skickas av din IoT Hub:

:::image type="content" source="media/monitor-iot-hub/metrics-portal.png" alt-text="Sidan Metrics Explorer för en IoT-hubb.":::

En lista över de plattforms mått som samlas in för Azure IoT Hub finns i [mått i data referens för övervakning av Azure-IoT Hub](monitor-iot-hub-reference.md#metrics). En lista över de plattforms mått som samlas in för alla Azure-tjänster finns i [mått som stöds med Azure Monitor](../azure-monitor/essentials/metrics-supported.md).

För IoT Hub plattforms mått som samlas in i antal enheter kan vissa agg regeringar inte vara tillgängliga eller användbara. Mer information finns i [agg regeringar som stöds i data referens för övervakning av Azure-IoT Hub](monitor-iot-hub-reference.md#supported-aggregations).

Vissa IoT Hub mått, till exempel [routnings mått](monitor-iot-hub-reference.md#routing-metrics), är flerdimensionella. För dessa mått kan du använda [filter](../azure-monitor/essentials/metrics-charts.md#filters) och [dela upp](../azure-monitor/essentials/metrics-charts.md#apply-splitting) i dina diagram baserat på en dimension.

## <a name="analyzing-logs"></a>Analysera loggar

Data i Azure Monitor loggar lagras i tabeller där varje tabell har en egen uppsättning unika egenskaper. Data i dessa tabeller är associerade med en Log Analytics arbets yta och kan efter frågas i Log Analytics. Mer information om Azure Monitor loggar finns i [Översikt över Azure Monitor loggar](../azure-monitor/logs/data-platform-logs.md) i Azure Monitor-dokumentationen. 

Om du vill dirigera data till Azure Monitor loggar måste du skapa en diagnostisk inställning för att skicka resurs loggar eller plattforms mått till en Log Analytics arbets yta. Mer information finns i [insamling och routning](#collection-and-routing).

I Azure Portal kan du välja **loggar** under **övervakning** i den vänstra rutan i IoT hub för att utföra Log Analytics frågor, som standard, till de loggar och mått som samlas in i Azure Monitor loggar för din IoT-hubb.

:::image type="content" source="media/monitor-iot-hub/logs-portal.png" alt-text="Sidan loggar för en IoT-hubb.":::

En lista över de tabeller som används av Azure Monitor loggar och efter frågas av Log Analytics finns i [Azure Monitor loggar tabeller i data referens för övervakning av Azure IoT Hub](monitor-iot-hub-reference.md#azure-monitor-logs-tables).

Alla resurs loggar i Azure Monitor har samma fält följt av tjänstespecific-fält. Det gemensamma schemat beskrivs i [Azure Monitor resurs logg schema](../azure-monitor/essentials/resource-logs-schema.md#top-level-common-schema). Du kan hitta schemat och kategorierna för resurs loggar som samlats in för Azure IoT Hub i [resurs loggar i data referens för övervakning av Azure-IoT Hub](monitor-iot-hub-reference.md#resource-logs).

[Aktivitets loggen](../azure-monitor/essentials/activity-log.md) är en plattforms logg i Azure som ger inblick i händelser på prenumerations nivå. Du kan visa den oberoende av varandra eller dirigera den till Azure Monitor loggar, där du kan göra mycket mer komplexa frågor med Log Analytics.  

Tänk på följande när du dirigerar IoT Hub plattforms mått till Azure Monitor loggar:

- Det går inte att exportera följande plattforms mått via diagnostikinställningar: *anslutna enheter (för hands version)* och *Totalt antal enheter (för hands version)*.

- Flerdimensionella mått, till exempel vissa mått för [routning](monitor-iot-hub-reference.md#routing-metrics), exporteras för närvarande som sammansatta enkla mått för dimensions värden. Mer information finns i [Exportera plattforms mått till andra platser](../azure-monitor/essentials/metrics-supported.md#exporting-platform-metrics-to-other-locations).

Några vanliga frågor med IoT Hub finns i [exempel på Kusto-frågor](#sample-kusto-queries). Detaljerad information om hur du använder Log Analytics frågor finns i [Översikt över logg frågor i Azure Monitor](../azure-monitor/logs/log-query-overview.md).

### <a name="sdk-version-in-iot-hub-logs"></a>SDK-version i IoT Hub loggar

Vissa åtgärder i IoT Hub resurs loggar returnerar en `sdkVersion` egenskap i sitt `properties` objekt. För dessa åtgärder, när en enhets-eller backend-app använder en av Azure IoT-SDK: erna, innehåller den här egenskapen information om SDK: n som används, SDK-versionen och plattformen som SDK: n körs på. I följande exempel visas `sdkVersion` egenskapen som har genererats för en [`deviceConnect`](monitor-iot-hub-reference.md#connections) åtgärd när du använder Node.js-enhets-SDK: `"azure-iot-device/1.17.1 (node v10.16.0; Windows_NT 10.0.18363; x64)"` . Här är ett exempel på det värde som har avsänts för .NET (C#) SDK: `".NET/1.21.2 (.NET Framework 4.8.4200.0; Microsoft Windows 10.0.17763 WindowsProduct:0x00000004; X86)"` .

I följande tabell visas SDK-namnet som används för olika Azure IoT SDK: er:

| SDK-namn i egenskapen sdkVersion | Språk |
|----------|----------|
| .NET | .NET (C#) |
| Microsoft. Azure. Devices | SDK för .NET (C#) service |
| Microsoft. Azure. devices. client | .NET (C#) enhets-SDK |
| iothubclient | C eller python v1 (föråldrad) enhets-SDK |
| iothubserviceclient | Service SDK för C eller python v1 (föråldrad) |
| Azure-IoT-Device-iothub-py | Python-enhets-SDK |
| azure-iot-device | Node.js-enhets-SDK |
| azure-iothub | Node.js tjänst-SDK |
| com. Microsoft. Azure. iothub-Java-client | Java-enhets-SDK |
| com. Microsoft. Azure. iothub. service. SDK | Java-tjänst-SDK |
| com. Microsoft. Azure. SDK. IoT. IoT-Device-client | Java-enhets-SDK |
| com. Microsoft. Azure. SDK. IoT. IoT-service-client | Java-tjänst-SDK |
| C | Inbäddat C |
| C + (OSSimplified = Azure återställnings tider) | Azure-återställningstider |

Du kan extrahera SDK-version-egenskapen när du kör frågor mot IoT Hub resurs loggar. Följande fråga extraherar t. ex. egenskapen SDK-version (och enhets-ID) från egenskaperna som returneras av anslutnings åtgärder. Dessa två egenskaper skrivs till resultaten tillsammans med tiden för åtgärden och resurs-ID: t för den IoT-hubb som enheten ansluter till.

```kusto
// SDK version of devices
// List of devices and their SDK versions that connect to IoT Hub
AzureDiagnostics
| where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
| where Category == "Connections"
| extend parsed_json = parse_json(properties_s) 
| extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId)
| distinct DeviceId, SDKVersion, TimeGenerated, _ResourceId
```

### <a name="sample-kusto-queries"></a>Exempel på Kusto-frågor

> [!IMPORTANT]
> När du väljer **loggar** från IoT Hub-menyn öppnas Log Analytics med fråge omfånget som är inställt på den aktuella IoT-hubben. Det innebär att logg frågor bara innehåller data från den resursen. Om du vill köra en fråga som innehåller data från andra IoT-hubbar eller data från andra Azure-tjänster väljer du **loggar** på **Azure Monitor** -menyn. Mer information finns i [logg frågans omfång och tidsintervall i Azure Monitor Log Analytics](../azure-monitor/logs/scope.md) .

Följande är frågor som du kan använda för att övervaka din IoT Hub.

- Anslutnings fel: identifiera anslutnings fel för enheten.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" and Level == "Error"
    ```

- Begränsnings fel: identifiera enheter som gjorde de flesta förfrågningar som resulterade i begränsnings fel.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where ResultType == "429001"
    | extend DeviceId = tostring(parse_json(properties_s).deviceId)
    | summarize count() by DeviceId, Category, _ResourceId
    | order by count_ desc
    ```

- Död slut punkter: identifiera förlorade eller felaktiga slut punkter med antalet gånger som problemet rapporterades, samt orsaken till varför.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Routes" and OperationName in ("endpointDead", "endpointUnhealthy")
    | extend parsed_json = parse_json(properties_s)
    | extend Endpoint = tostring(parsed_json.endpointName), Reason = tostring(parsed_json.details) 
    | summarize count() by Endpoint, OperationName, Reason, _ResourceId
    | order by count_ desc
    ```

- Fel Sammanfattning: antal fel för alla åtgärder efter typ.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Level == "Error"
    | summarize count() by ResultType, ResultDescription, Category, _ResourceId
    ```

- Nyligen anslutna enheter: lista över enheter som IoT Hub såg anslutning under den angivna tids perioden.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" and OperationName == "deviceConnect"
    | extend DeviceId = tostring(parse_json(properties_s).deviceId)
    | summarize max(TimeGenerated) by DeviceId, _ResourceId
    ```

- SDK-version av enheter: lista över enheter och deras SDK-versioner för enhets anslutningar eller enheter till molnet med dubbla åtgärder.

    ```kusto
    AzureDiagnostics
    | where ResourceProvider == "MICROSOFT.DEVICES" and ResourceType == "IOTHUBS"
    | where Category == "Connections" or Category == "D2CTwinOperations"
    | extend parsed_json = parse_json(properties_s)
    | extend SDKVersion = tostring(parsed_json.sdkVersion) , DeviceId = tostring(parsed_json.deviceId)
    | distinct DeviceId, SDKVersion, TimeGenerated, _ResourceId
    ```

### <a name="read-logs-from-azure-event-hubs"></a>Läs loggar från Azure Event Hubs

När du har konfigurerat händelse loggning via diagnostikinställningar kan du skapa program som läser loggarna så att du kan vidta åtgärder baserat på informationen i dem. Den här exempel koden hämtar loggar från en Event Hub:

```csharp
class Program
{ 
    static string connectionString = "{your AMS eventhub endpoint connection string}";
    static string monitoringEndpointName = "{your AMS event hub endpoint name}";
    static EventHubClient eventHubClient;
    //This is the Diagnostic Settings schema
    class AzureMonitorDiagnosticLog
    {
        string time { get; set; }
        string resourceId { get; set; }
        string operationName { get; set; }
        string category { get; set; }
        string level { get; set; }
        string resultType { get; set; }
        string resultDescription { get; set; }
        string durationMs { get; set; }
        string callerIpAddress { get; set; }
        string correlationId { get; set; }
        string identity { get; set; }
        string location { get; set; }
        Dictionary<string, string> properties { get; set; }
    };

    static void Main(string[] args)
    {
        Console.WriteLine("Monitoring. Press Enter key to exit.\n");
        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName);
        var d2cPartitions = eventHubClient.GetRuntimeInformationAsync().PartitionIds;
        CancellationTokenSource cts = new CancellationTokenSource();
        var tasks = new List<Task>();
        foreach (string partition in d2cPartitions)
        {
            tasks.Add(ReceiveMessagesFromDeviceAsync(partition, cts.Token));
        }
        Console.ReadLine();
        Console.WriteLine("Exiting...");
        cts.Cancel();
        Task.WaitAll(tasks.ToArray());
    }

    private static async Task ReceiveMessagesFromDeviceAsync(string partition, CancellationToken ct)
    {
        var eventHubReceiver = eventHubClient.GetDefaultConsumerGroup().CreateReceiver(partition, DateTime.UtcNow);
        while (true)
        {
            if (ct.IsCancellationRequested)
            {
                await eventHubReceiver.CloseAsync();
                break;
            }
            EventData eventData = await eventHubReceiver.ReceiveAsync(new TimeSpan(0,0,10));
            if (eventData != null)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
                Console.WriteLine("Message received. Partition: {0} Data: '{1}'", partition, data);
                var deserializer = new JavaScriptSerializer();
                //deserialize json data to azure monitor object
                AzureMonitorDiagnosticLog message = new JavaScriptSerializer().Deserialize<AzureMonitorDiagnosticLog>(result);
            }
        }
    }
}
```

## <a name="alerts"></a>Aviseringar

Azure Monitor aviseringar proaktivt meddela dig när viktiga villkor finns i dina övervaknings data. De gör att du kan identifiera och åtgärda problem i systemet innan kunderna märker dem. Du kan ställa in aviseringar för [mått](../azure-monitor/alerts/alerts-metric-overview.md), [loggar](../azure-monitor/alerts/alerts-unified-log.md)och [aktivitets loggen](../azure-monitor/alerts/activity-log-alerts.md). Olika typer av aviseringar har fördelar och nack delar.

När du skapar en varnings regel baserat på plattforms mått bör du vara medveten om att för IoT Hub plattforms mått som samlas in i antal enheter, kan vissa agg regeringar inte vara tillgängliga eller användbara. Mer information finns i [agg regeringar som stöds i data referens för övervakning av Azure-IoT Hub](monitor-iot-hub-reference.md#supported-aggregations).

## <a name="monitor-per-device-disconnects-with-event-grid"></a>Övervaka varje enhet som kopplas från med Event Grid

Azure Monitor tillhandahåller ett mått, *anslutna enheter*, som du kan använda för att övervaka antalet enheter som är anslutna till din IoT Hub och utlösa en avisering när antalet anslutna enheter sjunker under ett tröskelvärde. Det kan vara tillräckligt för vissa scenarier, [Azure Event Grid](../event-grid/index.yml) ger en övervaknings lösning med låg latens per enhet som du kan använda för att spåra enhets anslutningar för kritiska enheter och infrastruktur.

Med Event Grid kan du prenumerera på IoT Hub [ **DeviceConnected** -och **DeviceDisconnected** -händelser](iot-hub-event-grid.md#event-types) för att utlösa aviseringar och övervaka enhetens anslutnings tillstånd. Event Grid ger mycket lägre händelse latens än Azure Monitor och du kan övervaka per enhet, i stället för det totala antalet anslutna enheter. Dessa faktorer gör Event Grid den bästa metoden för att övervaka anslutningar för kritiska enheter och infrastruktur. Vi rekommenderar starkt att du använder Event Grid för att övervaka enhets anslutningar i produktions miljöer.

Mer detaljerad information om övervakning av enhets anslutningar med Event Grid och Azure Monitor finns i [övervaka, diagnostisera och felsöka från kopplingar till Azure-IoT Hub](iot-hub-troubleshoot-connectivity.md).

## <a name="next-steps"></a>Nästa steg

- Se [övervakning av Azure IoT Hub data Reference](monitor-iot-hub-reference.md) för en referens till mått, loggar och andra viktiga värden som skapats av [tjänst namn].

- Mer information om övervakning av Azure-resurser finns i [övervaka Azure-resurser med Azure Monitor](../azure-monitor/essentials/monitor-azure-resource.md) .
