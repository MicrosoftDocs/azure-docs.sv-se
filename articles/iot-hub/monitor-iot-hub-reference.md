---
title: Övervakning av Azure IoT Hub data-referens
description: Viktigt referensmaterial som krävs när du övervakar Azure-IoT Hub
author: robinsh
ms.author: robinsh
ms.topic: reference
ms.service: iot-hub
ms.custom: subject-monitoring
ms.date: 10/22/2020
ms.openlocfilehash: f198b59f106b7d2a29e35d77b54274328be6fa93
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581609"
---
# <a name="monitoring-azure-iot-hub-data-reference"></a>Övervakning av Azure IoT Hub data-referens

Mer information om hur du samlar in och analyserar övervaknings data för Azure IoT Hub finns i [övervaka Azure-IoT Hub](monitor-iot-hub.md) .

## <a name="metrics"></a>Mått

I det här avsnittet visas alla automatiskt insamlade plattforms mått för Azure IoT Hub. Resurs leverantörens namn område för IoT Hub mått är **Microsoft. Devices** och typ namn området är **IoTHubs**.

Följande underavsnitt visar måtten för IoT Hub Platform-mått enligt kategorin Allmänt och listar dem efter det visnings namn som de visas i Azure Portal med. Information anges också relevant för de mått som visas i varje underavsnitt.

Du kan också hitta en enskild tabell som visar alla IoT Hub plattforms mått efter Metric-namn under [Microsoft. Devices/IotHubs](../azure-monitor/platform/metrics-supported.md#microsoftdevicesiothubs) i dokumentationen för Azure Monitor. Tänk på att den här tabellen inte innehåller någon information, t. ex. [agg regeringar som stöds](#supported-aggregations) för vissa mått, som finns i den här artikeln.

Mer information om mått som stöds av andra Azure-tjänster finns i [mått som stöds med Azure Monitor](../azure-monitor/platform/metrics-supported.md).

**Ämnen i det här avsnittet**

- [Agg regeringar som stöds](#supported-aggregations)
- [Kommando mått för moln till enhet](#cloud-to-device-command-metrics)
- [Mått för moln till enhetens direkta metoder](#cloud-to-device-direct-methods-metrics)
- [Moln till enhet, dubbla drifts mått](#cloud-to-device-twin-operations-metrics)
- [Konfigurations mått](#configurations-metrics)
- [Dagliga kvot mått](#daily-quota-metrics)
- [Enhets mått](#device-metrics)
- [Mått för enhets telemetri](#device-telemetry-metrics)
- [Enhet till molnet med dubbla drifts mått](#device-to-cloud-twin-operations-metrics)
- [Event Grid-mått](#event-grid-metrics)
- [Jobb mått](#jobs-metrics)
- [Routnings mått](#routing-metrics)
- [Dubbla fråge mått](#twin-query-metrics)

### <a name="supported-aggregations"></a>Agg regeringar som stöds

Kolumnen **agg regerings typ** i varje tabell motsvarar den standard agg regering som används när måttet väljs för ett diagram eller en avisering.

   ![Skärm bild som visar agg regering för mått](./media/monitor-iot-hub-reference/aggregation-type.png)

För de flesta mått är alla agg regerings typer giltiga. men för Count-mått, de med värdet **Count** för **enhets** kolumnen, är bara vissa agg regeringar giltiga. Antalet mått kan vara en av två typer:

* IoT Hub registrerar **en enda data punkt, i stort** sett en 1--, varje gång den uppmätta åtgärden inträffar. Azure Monitor summerar dessa data punkter över den angivna granularitet. Exempel på **enkla** mått är *telemetri meddelanden som skickas* och *C2D meddelande leveranser har slutförts*. För dessa mått är den enda relevanta agg regerings typen total (sum). På portalen kan du välja lägsta, högsta och medelvärde. dessa värden är dock alltid 1.

* IoT Hub registrerar ett totalt antal när den uppmätta åtgärden inträffar för antal **ögonblicks bilder** . För närvarande finns det tre **ögonblicks bilds** mått som har spridits av IoT Hub: det *totala antalet meddelanden som används*, *Totalt antal enheter (för hands version)* och *anslutna enheter (för hands version)*. Eftersom dessa mått visar en "total"-kvantitet varje gång de genereras, är det ingen mening att summera dem över den angivna granularitet. Azure Monitor begränsar du att välja medel, minimum och maximum för sammansättnings typen för dessa mått.

### <a name="cloud-to-device-command-metrics"></a>Kommando mått för moln till enhet

|Mått visnings namn|Metric|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|
|C2D meddelanden har förfallit (förhands granskning)|C2DMessagesExpired|Antal|Totalt|Antal utgångna meddelanden från moln till enhet|Inget|
|C2D meddelande leveranser har slutförts|C2D. commands. utgående. Complete. lyckades|Antal|Totalt|Antalet meddelande leveranser från moln till enhet har slutförts av enheten|Inget|
|Övergivna C2D-meddelanden|C2D. commands. utgående. Abandon. lyckades|Antal|Totalt|Antal meddelanden från moln till enhet som har övergivits av enheten|Inget|
|Avvisade C2D-meddelanden|C2D. commands. rekasta. Success|Antal|Totalt|Antal meddelanden från moln till enhet som avvisats av enheten|Inget|

För mått med ett **enhets** **värde antal är** endast total summan (sum) agg regeringen giltig. Lägsta, högsta och genomsnittliga agg regeringar returnerar alltid 1. Mer information finns i [agg regeringar som stöds](#supported-aggregations).

### <a name="cloud-to-device-direct-methods-metrics"></a>Mått för moln till enhetens direkta metoder

|Mått visnings namn|Metric|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|
|Misslyckade direkta metod anrop|C2D. Methods. Failure|Antal|Totalt|Antalet misslyckade direkta metod anrop.|Inget|
|Begär ande storlek för direkta metod anrop|C2D. Methods. requestSize|Byte|Genomsnitt|Antalet lyckade direkta metod begär Anden.|Inget|
|Svars storlek för direkta metod anrop|C2D. Methods. responseSize|Byte|Genomsnitt|Antalet slutförda direkta metod svar.|Inget|
|Direkta metod anrop|C2D. Methods. Success|Antal|Totalt|Antalet lyckade direkta metod anrop.|Inget|

För mått med ett **enhets** värde som **endast är total mängd (** sum) agg regering giltig. Lägsta, högsta och genomsnittliga agg regeringar returnerar alltid 1. Mer information finns i [agg regeringar som stöds](#supported-aggregations).

### <a name="cloud-to-device-twin-operations-metrics"></a>Moln till enhet, dubbla drifts mått

|Mått visnings namn|Metric|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|
|Det gick inte att dubbla läsningar från Server delen|C2D. delad.|Antal|Totalt|Antalet dubbla läsningar som initierats av alla misslyckade backend-slutpunkter.|Inget|
|Misslyckade dubbla uppdateringar från Server delen|C2D. delad. Update. Failure|Antal|Totalt|Antalet dubbla uppdateringar som initierats av alla misslyckade backend-slutpunkter.|Inget|
|Svars storlek för dubbla läsningar från Server delen|C2D., delad. storlek|Byte|Genomsnitt|Antalet dubbla läsningar som initierats av alla lyckade.|Inget|
|Storlek på dubbla uppdateringar från Server delen|C2D. dubbla. Update. size|Byte|Genomsnitt|Den totala storleken på alla lyckade, dubbla uppdateringar som initieras.|Inget|
|Lyckades dubbla läspaket från Server delen|C2D. delad. lyckades|Antal|Totalt|Antalet dubbla läsningar som initierats av alla lyckade.|Inget|
|Lyckades dubbla uppdateringar från Server delen|C2D. delad. Update. lyckades|Antal|Totalt|Antalet fullständiga uppdateringar som initierats av alla lyckade backend-uppdateringar.|Inget|

För mått med ett **enhets** **värde antal är** endast total summan (sum) agg regeringen giltig. Lägsta, högsta och genomsnittliga agg regeringar returnerar alltid 1. Mer information finns i [agg regeringar som stöds](#supported-aggregations).

### <a name="configurations-metrics"></a>Konfigurations mått

|Mått visnings namn|Metric|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|
|Konfigurations mått|konfigurationer|Antal|Totalt|Antalet totala CRUD-åtgärder som utförts för enhets konfiguration och IoT Edge distribution på en uppsättning mål enheter. Detta omfattar även antalet åtgärder som ändrar enheten till dubbla eller modulerna på grund av dessa konfigurationer.|Inget|

För mått med ett **enhets** **värde antal är** endast total summan (sum) agg regeringen giltig. Lägsta, högsta och genomsnittliga agg regeringar returnerar alltid 1. Mer information finns i [agg regeringar som stöds](#supported-aggregations).

### <a name="daily-quota-metrics"></a>Dagliga kvot mått

|Mått visnings namn|Metric|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|
|Total användning av enhets data|deviceDataUsage|Byte|Totalt|Överförda byte till och från alla enheter som är anslutna till IotHub|Inget|
|Total användning av enhets data (för hands version)|deviceDataUsageV2|Byte|Totalt|Överförda byte till och från alla enheter som är anslutna till IotHub|Inget|
|Totalt antal meddelanden som används|dailyMessageQuotaUsed|Antal|Genomsnitt|Antal sammanlagt antal meddelanden som använts idag. Detta är ett ackumulerat värde som återställs till noll vid 00:00 UTC varje dag.|Inget|

För det *totala antalet meddelanden som används* stöds endast lägsta, högsta och genomsnittliga agg regeringar. Mer information finns i [agg regeringar som stöds](#supported-aggregations).

### <a name="device-metrics"></a>Enhets mått

|Mått visnings namn|Metric|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|
|Totalt antal enheter (inaktuella)|enheter. totalDevices|Antal|Totalt|Antal enheter som har registrerats för din IoT-hubb|Inget|
|Anslutna enheter (inaktuella) |Devices. connectedDevices. allProtocol|Antal|Totalt|Antal enheter som är anslutna till din IoT-hubb|Inget|
|Totalt antal enheter (förhands granskning)|totalDeviceCount|Antal|Genomsnitt|Antal enheter som har registrerats för din IoT-hubb|Inget|
|Anslutna enheter (förhands granskning)|connectedDeviceCount|Antal|Genomsnitt|Antal enheter som är anslutna till din IoT-hubb|Inget|

För *Totalt antal enheter (föråldrade)* och *anslutna enheter (inaktuella)* är endast total summan (sum) agg regering giltig. Lägsta, högsta och genomsnittliga agg regeringar returnerar alltid 1. Mer information finns i [agg regeringar som stöds](#supported-aggregations).

För *Totalt antal enheter (för hands version)* och *anslutna enheter (för hands version)* är endast lägsta, högsta och genomsnittliga agg regeringar giltiga. Mer information finns i [agg regeringar som stöds](#supported-aggregations).

*Anslutna enheter (förhands granskning)* och *Totalt antal enheter (för hands version)* kan inte exporteras via diagnostikinställningar.

### <a name="device-telemetry-metrics"></a>Mått för enhets telemetri

|Mått visnings namn|Metric|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|
|Antal begränsnings fel|D2C. telemetri. ingress. sendThrottle|Antal|Totalt|Antal begränsnings fel som beror på begränsning av enhetens data flöde|Inget|
|Skicka försök för telemetri|D2C. telemetri. ingress. allProtocol|Antal|Totalt|Antalet telemetri från enhet till molnet försökte skickas till din IoT-hubb|Inget|
|Meddelande om telemetri|D2C. telemetri. ingress. lyckades|Antal|Totalt|Antal telemetri om enhet till molnet har skickats till din IoT-hubb|Inget|

För mått med ett **enhets** **värde antal är** endast total summan (sum) agg regeringen giltig. Lägsta, högsta och genomsnittliga agg regeringar returnerar alltid 1. Mer information finns i [agg regeringar som stöds](#supported-aggregations).

### <a name="device-to-cloud-twin-operations-metrics"></a>Enhet till molnet med dubbla drifts mått

|Mått visnings namn|Metric|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|
|Misslyckade dubbla läsningar från enheter|D2C. delad.|Antal|Totalt|Antalet misslyckade, dubbla läsningar som initierats av enheten.|Inget|
|Misslyckade dubbla uppdateringar från enheter|D2C. delad. Update. Failure|Antal|Totalt|Antalet misslyckade, dubbla uppdateringar som initierats av enheten.|Inget|
|Svars storlek för dubbla läsningar från enheter|D2C., delad. storlek|Byte|Genomsnitt|Antalet framgångs rika enhets uppinitierade dubbla läsningar.|Inget|
|Storlek på dubbla uppdateringar från enheter|D2C. dubbla. Update. size|Byte|Genomsnitt|Den totala storleken på alla lyckade, dubbla uppdateringar som initierats av enheten.|Inget|
|Lyckades dubbla läsningar från enheter|D2C. delad. lyckades|Antal|Totalt|Antalet framgångs rika enhets uppinitierade dubbla läsningar.|Inget|
|Lyckade dubbla uppdateringar från enheter|D2C. delad. Update. lyckades|Antal|Totalt|Antalet lyckade, dubbla uppdateringar som initierats av enheten.|Inget|

För mått med ett **enhets** **värde antal är** endast total summan (sum) agg regeringen giltig. Lägsta, högsta och genomsnittliga agg regeringar returnerar alltid 1. Mer information finns i [agg regeringar som stöds](#supported-aggregations).

### <a name="event-grid-metrics"></a>Event Grid-mått

|Mått visnings namn|Metric|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|
|Event Grid leveranser (för hands version)|EventGridDeliveries|Antal|Totalt|Antalet IoT Hub-händelser som publicerats till Event Grid. Använd resultat dimensionen för antalet lyckade och misslyckade förfrågningar. EventType-dimensionen visar händelse typen ( https://aka.ms/ioteventgrid) .|Resultat,<br/>Typ<br>*Mer information finns i [mått dimensioner](#metric-dimensions)*.|
|Event Grid svars tid (för hands version)|EventGridLatency|Millisekunder|Genomsnitt|Den genomsnittliga svars tiden (millisekunder) från när IoT Hub-händelsen genererades till när händelsen publicerades till Event Grid. Det här talet är ett medelvärde mellan alla händelse typer. Använd EventType-dimensionen för att se svars tiden för en speciell typ av händelse.|Typ<br>*Mer information finns i [mått dimensioner](#metric-dimensions)*.|

För mått med ett **enhets** **värde antal är** endast total summan (sum) agg regeringen giltig. Lägsta, högsta och genomsnittliga agg regeringar returnerar alltid 1. Mer information finns i [agg regeringar som stöds](#supported-aggregations).

### <a name="jobs-metrics"></a>Jobb mått

|Mått visnings namn|Metric|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|
|Slutförda jobb|jobb. slutfört|Antal|Totalt|Antalet slutförda jobb.|Inget|
|Misslyckade anrop till List jobb|Jobs. listJobs. Failure|Antal|Totalt|Antalet misslyckade anrop till List jobb.|Inget|
|Det gick inte att skapa metod anrops jobb|Jobs. createDirectMethodJob. Failure|Antal|Totalt|Antalet misslyckade skapande av direkta anrops jobb för metoden.|Inget|
|Det gick inte att skapa dubbla uppdaterings jobb|Jobs. createTwinUpdateJob. Failure|Antal|Totalt|Antalet misslyckade skapandet av dubbla uppdaterings jobb.|Inget|
|Misslyckade jobb-annulleringar|Jobs. cancelJob. Failure|Antal|Totalt|Antalet misslyckade anrop för att avbryta ett jobb.|Inget|
|Misslyckade jobb frågor|Jobs. queryJobs. Failure|Antal|Totalt|Antalet misslyckade anrop till jobb för frågor.|Inget|
|Misslyckade jobb|jobb. misslyckades|Antal|Totalt|Antalet misslyckade jobb.|Inget|
|Lyckade anrop till List jobb|Jobs. listJobs. lyckades|Antal|Totalt|Antalet lyckade anrop till List jobb.|Inget|
|Lyckade skapande av metod anrops jobb|Jobs. createDirectMethodJob. lyckades|Antal|Totalt|Antalet slutförda skapande av direkta metod anrops jobb.|Inget|
|Skapandet av dubbla uppdaterings jobb lyckades|Jobs. createTwinUpdateJob.<br>lyckades|Antal|Totalt|Antalet slutförda skapandet av dubbla uppdaterings jobb.|Inget|
|Slutförda jobb avbokningar|Jobs. cancelJob. lyckades|Antal|Totalt|Antalet lyckade anrop för att avbryta ett jobb.|Inget|
|Slutförda jobb frågor|Jobs. queryJobs. lyckades|Antal|Totalt|Antalet lyckade anrop för att köra frågor mot jobb.|Inget|

För mått med ett **enhets** **värde antal är** endast total summan (sum) agg regeringen giltig. Lägsta, högsta och genomsnittliga agg regeringar returnerar alltid 1. Mer information finns i [agg regeringar som stöds](#supported-aggregations).

### <a name="routing-metrics"></a>Routnings mått

|Mått visnings namn|Metric|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|
| Routnings leverans försök (förhands granskning) |RoutingDeliveries | Antal | Totalt |Detta är mått för routnings leverans. Använd dimensionerna för att identifiera leverans status för en angiven slut punkt eller för en speciell Dirigerings källa.| Resultat,<br>RoutingSource,<br>EndpointType,<br>FailureReasonCategory,<br>EndpointName<br>*Mer information finns i [mått dimensioner](#metric-dimensions)*. |
| Flödes leverans data storlek i byte (för hands version)|RoutingDataSizeInBytesDelivered| Byte | Totalt |Det totala antalet byte som dirigerats av IoT Hub till den anpassade slut punkten och den inbyggda slut punkten. Använd dimensionerna för att identifiera data storleken dirigerad till en angiven slut punkt eller för en speciell vägkälla.| RoutingSource,<br>EndpointType<br>EndpointName<br>*Mer information finns i [mått dimensioner](#metric-dimensions)*.|
| Svars tid för routning (för hands version) |RoutingDeliveryLatency| Millisekunder | Genomsnitt |Detta är måttet för leverans fördröjning i routning. Använd dimensionerna för att identifiera svars tiden för en speciell slut punkt eller för en speciell Dirigerings källa.| RoutingSource,<br>EndpointType,<br>EndpointName<br>*Mer information finns i [mått dimensioner](#metric-dimensions)*.|
|Routning: blobbar levererade till lagring|D2C. endpoints. utgående. Storage. blob|Antal|Totalt|Antal gånger IoT Hub som levererade blobbar till lagrings slut punkter.|Inget|
|Routning: data som levereras till lagring|D2C. endpoints. utgående. Storage. byte|Byte|Totalt|Mängden data (byte) IoT Hub routning som levereras till lagrings slut punkter.|Inget|
|Routning: meddelande fördröjning för Event Hub|D2C. endpoints. latens. eventHubs|Millisekunder|Genomsnitt|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub och meddelandet intränger i anpassade slut punkter av typen Event Hub. Detta inkluderar inte meddelanden vägar till den inbyggda slut punkten (händelser).|Inget|
|Routning: meddelande fördröjning för Service Bus kö|D2C. endpoints. latens. serviceBusQueues|Millisekunder|Genomsnitt|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub och meddelandet intränger i en Service Bus Queue-slutpunkt.|Inget|
|Routning: meddelande fördröjning för Service Bus ämne|D2C. endpoints. latens. serviceBusTopics|Millisekunder|Genomsnitt|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub och meddelandet intränger i en Service Bus avsnitts slut punkt.|Inget|
|Routning: meddelande fördröjning för meddelanden/händelser|D2C. endpoints. latens. Builtin. events|Millisekunder|Genomsnitt|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub och meddelandet intränger i den inbyggda slut punkten (meddelanden/händelser) och återställnings väg.|Inget|
|Routning: meddelande fördröjning för lagring|D2C. endpoints. svars tid. Storage|Millisekunder|Genomsnitt|Den genomsnittliga svars tiden (millisekunder) mellan meddelandet intränger mot IoT Hub och meddelandet intränger i en lagrings slut punkt.|Inget|
|Routning: meddelanden levererade till Händelsehubben|D2C. endpoints. utgående. eventHubs|Antal|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till anpassade slut punkter av typen Event Hub. Detta inkluderar inte meddelanden vägar till den inbyggda slut punkten (händelser).|Inget|
|Routning: meddelanden levererade till Service Bus kö|D2C. endpoints. utgående. serviceBusQueues|Antal|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till Service Bus-köns slut punkter.|Inget|
|Routning: meddelanden levererade till Service Bus ämnet|D2C. endpoints. utgående. serviceBusTopics|Antal|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till Service Bus ämnes slut punkter.|Inget|
|Routning: meddelanden levererade till reserv|D2C. telemetri. utgående. fallback|Antal|Totalt|Antalet gånger IoT Hub vidarebefordran av meddelanden till slut punkten som är kopplad till återställnings vägen.|Inget|
|Routning: meddelanden som levereras till meddelanden/händelser|D2C. endpoints. utgående. Builtity. events|Antal|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till den inbyggda slut punkten (meddelanden/händelser) och återställnings väg.|Inget|
|Routning: meddelanden som levereras till lagring|D2C. endpoints. utgående. Storage|Antal|Totalt|Antalet gånger IoT Hub routning har levererat meddelanden till lagrings slut punkter.|Inget|
|Routning: telemetri meddelanden levereras|D2C. telemetri. utgående. lyckades|Antal|Totalt|Antalet gånger som meddelanden har levererats till alla slut punkter med hjälp av IoT Hub routning. Om ett meddelande dirigeras till flera slut punkter ökar det här värdet med ett för varje lyckad leverans. Om ett meddelande skickas till samma slut punkt flera gånger ökar det här värdet med ett för varje lyckad leverans.|Inget|
|Routning: telemetri ignoreras |D2C. telemetri. utgående.|Antal|Totalt|Antalet gånger som meddelanden släpptes genom IoT Hub routning på grund av död slut punkter. Det här värdet räknar inte meddelanden som levereras till återställnings vägen eftersom ignorerade meddelanden inte levereras där.|Inget|
|Routning: telemetri-meddelanden är inkompatibla|D2C. telemetri. utgående. ogiltig|Antal|Totalt|Antalet gånger IoT Hub routningen kunde inte leverera meddelanden på grund av inkompatibilitet med slut punkten. Ett meddelande är inkompatibelt med en slut punkt när IoT Hub försöker leverera meddelandet till en slut punkt och det Miss lyckas med ett icke-tillfälligt fel. Ogiltiga meddelanden har inte gjorts om. Det här värdet omfattar inte återförsök.|Inget|
|Routning: telemetri-meddelanden har överblivna |D2C. telemetri. utgående. överblivna|Antal|Totalt|Antalet gånger som meddelanden har överblivnas av IoT Hub routning eftersom de inte matchade någon cirkulations fråga när reserv vägen är inaktive rad.|Inget|

För mått med ett **enhets** **värde antal är** endast total summan (sum) agg regeringen giltig. Lägsta, högsta och genomsnittliga agg regeringar returnerar alltid 1. Mer information finns i [agg regeringar som stöds](#supported-aggregations).

### <a name="twin-query-metrics"></a>Dubbla fråge mått

|Mått visnings namn|Metric|Enhet|Sammansättningstyp|Description|Dimensioner|
|---|---|---|---|---|---|
|Misslyckade dubbla frågor|twinQueries. Failure|Antal|Totalt|Antalet misslyckade dubbla frågor.|Inget|
|Lyckades dubbla frågor|twinQueries. lyckades|Antal|Totalt|Antalet lyckade dubbla frågor.|Inget|
|Resultat storlek för dubbla frågor|twinQueries.resultSize|Byte|Genomsnitt|Summan av resultat storleken för alla lyckade dubbla frågor.|Inget|

För mått med ett **enhets** **värde antal är** endast total summan (sum) agg regeringen giltig. Lägsta, högsta och genomsnittliga agg regeringar returnerar alltid 1. Mer information finns i [agg regeringar som stöds](#supported-aggregations).

## <a name="metric-dimensions"></a>Mått dimensioner

Azure IoT Hub har följande dimensioner kopplade till några av måtten för Routning och händelse rutnät.

|Dimensions namn | Description|
|---|---|
||
|**EndpointName**| Slut punktens namn.|
|**EndpointType**|Något av följande: **eventHubs**, **serviceBusQueues**, **cosmosDB**, **serviceBusTopics**. **Builtin** eller **blobStorage**.|
|**Typ**| En av följande Event Grid händelse typer: **Microsoft. devices. DeviceCreated**. **Microsoft. devices. DeviceDeleted**, **Microsoft. devices. DeviceConnected**, **Microsoft. devices. DeviceDisconnected** eller **Microsoft. devices. DeviceTelemetry**. Mer information finns i [händelse typer](iot-hub-event-grid.md#event-types).|
|**FailureReasonCategory**| Något av följande: **ogiltigt**, **Borttaget**, **överbliven** eller **Null**.|
|**Resultat**| Antingen **lyckades** eller **misslyckades**.|
|**RoutingSource**| Enhets meddelanden<br>Dubbla ändrings händelser<br>Livs cykel händelser för enhet|

Mer information om mått dimensioner finns i [flerdimensionella mått](../azure-monitor/platform/data-platform-metrics.md#multi-dimensional-metrics).

## <a name="resource-logs"></a>Resursloggar

I det här avsnittet visas alla kategori typer och scheman för resurs logg som samlats in för Azure IoT Hub. Resurs leverantören och typen för alla IoT Hub loggar är [Microsoft. Devices/IotHubs](../azure-monitor/platform/resource-logs-categories.md#microsoftdevicesiothubs).

**Ämnen i det här avsnittet**

- [Anslutningar](#connections)
- [Telemetri för enhet](#device-telemetry)
- [Moln till enhet-kommandon](#cloud-to-device-commands)
- [Enhets identitets åtgärder](#device-identity-operations)
- [Filöverföringsåtgärder](#file-upload-operations)
- [Vägar](#routes)
- [Dubbla åtgärder från enhet till moln](#device-to-cloud-twin-operations)
- [Dubbla åtgärder från moln till enhet](#cloud-to-device-twin-operations)
- [Dubbla frågor](#twin-queries)
- [Jobbåtgärder](#jobs-operations)
- [Direkta metoder](#direct-methods)
- [Distribuerad spårning (för hands version)](#distributed-tracing-preview)
  - [IoT Hub D2C-loggar (från enhet till moln)](#iot-hub-d2c-device-to-cloud-logs)
  - [IoT Hub ingress-loggar](#iot-hub-ingress-logs)
  - [IoT Hub utgående loggar](#iot-hub-egress-logs)
- [Konfigurationer](#configurations)
- [Enhets strömmar (förhands granskning)](#device-streams-preview)

### <a name="connections"></a>Anslutningar

Kategorin anslutningar spårar enhets anslutning och från kopplings händelser från en IoT-hubb och fel. Den här kategorin är användbar för att identifiera otillåtna anslutnings försök och aviseringar när du förlorar anslutningen till enheter.

> [!NOTE]
> För tillförlitlig anslutnings status för enheter kontrol lera [enhetens pulsslag](iot-hub-devguide-identity-registry.md#device-heartbeat).

```json
{
   "records":
   [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "deviceConnect",
            "category": "Connections",
            "level": "Information",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"maskedIpAddress\":\"<maskedIpAddress>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="device-telemetry"></a>Telemetri för enhet

Kategorin hets telemetri spårar fel som inträffar på IoT-hubben och är relaterade till pipelinen för telemetri. Den här kategorin innehåller fel som uppstår när du skickar telemetri-händelser (till exempel begränsning) och tar emot telemetri-händelser (till exempel obehörig läsare). Den här kategorin kan inte fånga fel som orsakas av kod som körs på själva enheten.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "DeviceTelemetry",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"batching\":\"0\",\"messageSizeInBytes\":\"<messageSizeInBytes>\",\"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\",\"partitionId\":\"1\"}", 
            "location": "Resource location"
        }
    ]
}
```

### <a name="cloud-to-device-commands"></a>Moln till enhet-kommandon

Kommando kategorin för moln-till-enhet spårar fel som uppstår på IoT-hubben och är relaterade till pipeline för meddelande från moln till enhet. Den här kategorin innehåller fel som inträffar från:

* Skicka meddelanden från moln till enhet (t. ex. otillåtna avsändar fel)
* Ta emot meddelanden från moln till enhet (t. ex. antal leveranser som har överskridits) och
* Får feedback från moln till enhet (t. ex. återkopplings fel).

Den här kategorin fångar inte fel när meddelanden från molnet till enheten levereras korrekt, men hanteras sedan felaktigt av enheten.

```json
{
    "records":
    [
        {
            "time": " UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "messageExpired",
            "category": "C2DCommands",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"deviceId\":\"<deviceId>\",\"messageId\":\"<messageId>\",\"messageSizeInBytes\":\"<messageSize>\",\"protocol\":\"Amqp\",\"deliveryAcknowledgement\":\"<None, NegativeOnly, PositiveOnly, Full>\",\"deliveryCount\":\"0\",\"expiryTime\":\"<timestamp>\",\"timeInSystem\":\"<timeInSystem>\",\"ttl\":<ttl>, \"EventProcessedUtcTime\":\"<UTC timestamp>\",\"EventEnqueuedUtcTime\":\"<UTC timestamp>\", \"maskedIpAddress\": \"<maskedIpAddress>\", \"statusCode\": \"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="device-identity-operations"></a>Enhets identitets åtgärder

Kategorin enhets identitets åtgärder spårar fel som inträffar när du försöker skapa, uppdatera eller ta bort en post i din IoT Hubs identitets register. Att spåra den här kategorin är användbart för etablerings scenarier.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "get",
            "category": "DeviceIdentityOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "properties": "{\"maskedIpAddress\":\"<maskedIpAddress>\",\"deviceId\":\"<deviceId>\", \"statusCode\":\"4XX\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="file-upload-operations"></a>Filöverföringsåtgärder

Kategorin fil uppladdning spårar fel som uppstår på IoT-hubben och är relaterade till fil uppladdnings funktionen. Den här kategorin omfattar:

* Fel som uppstår med SAS-URI: n, till exempel när den upphör att gälla innan en enhet meddelar navet om en slutförd uppladdning.

* Misslyckade uppladdningar som rapporter ATS av enheten.

* Fel som inträffar när det inte går att hitta en fil i lagringen under IoT Hub meddelande om att meddelanden skapas.

Den här kategorin kan inte fånga fel som sker direkt när enheten laddar upp en fil till lagringen.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "ingress",
            "category": "FileUploadOperations",
            "level": "Error",
            "resultType": "Event status",
            "resultDescription": "MessageDescription",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\",\"blobUri\":\"http//bloburi.com\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="routes"></a>Vägar

Kategorin [meddelande cirkulation](./iot-hub-devguide-messages-d2c.md) spårar fel som inträffar under utvärderingen av meddelande vägar och slut punkts hälsa som uppfattas av IoT Hub. Den här kategorin omfattar händelser som:

* En regel utvärderas till "odefinierad",
* IoT Hub markerar en slut punkt som död eller
* Eventuella fel som tagits emot från en slut punkt.

Den här kategorin innehåller inte vissa fel meddelanden om själva meddelandena (t. ex. fel i enhets begränsning), som rapporteras i kategorin "enhets telemetri".

```json
{
    "records":
    [
        {
            "time":"2019-12-12T03:25:14Z",
            "resourceId":"/SUBSCRIPTIONS/91R34780-3DEC-123A-BE2A-213B5500DFF0/RESOURCEGROUPS/ANON-TEST/PROVIDERS/MICROSOFT.DEVICES/IOTHUBS/ANONHUB1",
            "operationName":"endpointUnhealthy",
            "category":"Routes",
            "level":"Error",
            "resultType":"403004",
            "resultDescription":"DeviceMaximumQueueDepthExceeded",
            "properties":"{\"deviceId\":null,\"endpointName\":\"anon-sb-1\",\"messageId\":null,\"details\":\"DeviceMaximumQueueDepthExceeded\",\"routeName\":null,\"statusCode\":\"403\"}",
            "location":"westus"
        }
    ]
}
```

Här är mer information om routning av resurs loggar:

* [Lista över fel koder för routning av resurs logg](troubleshoot-message-routing.md#diagnostics-error-codes)
* [Lista över routning resurs loggar operationNames](troubleshoot-message-routing.md#diagnostics-operation-names)

### <a name="device-to-cloud-twin-operations"></a>Dubbla åtgärder från enhet till moln

Kategorin enhets-till-moln-dubbla åtgärder spårar enheternas initierade händelser på enheten. De här åtgärderna kan vara att hämta rapporter med dubbla, uppdatera rapporterade egenskaper och prenumerera på önskade egenskaper.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "update",
            "category": "D2CTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"protocol\":\"<protocol>\",\"authenticationType\":\"{\\\"scope\\\":\\\"device\\\",\\\"type\\\":\\\"sas\\\",\\\"issuer\\\":\\\"iothub\\\",\\\"acceptingIpFilterRule\\\":null}\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="cloud-to-device-twin-operations"></a>Dubbla åtgärder från moln till enhet

Den dubbla drift kategorin från moln till enhet spårar händelser som initierats av tjänsten på enheten. Dessa åtgärder kan omfatta etiketter för att hämta, uppdatera eller ersätta och uppdatera eller ersätta önskade egenskaper.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "read",
            "category": "C2DTwinOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":\"<deviceId>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="twin-queries"></a>Dubbla frågor

Kategorin dubbla frågor rapporterar om förfrågningar om enhets grupper som initieras i molnet.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "query",
            "category": "TwinQueries",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"query\":\"<twin query>\",\"sdkVersion\":\"<sdkVersion>\",\"messageSize\":\"<messageSize>\",\"pageSize\":\"<pageSize>\", \"continuation\":\"<true, false>\", \"resultSize\":\"<resultSize>\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="jobs-operations"></a>Jobbåtgärder

Jobbets åtgärds kategori rapporterar om jobb begär Anden för att uppdatera enheten är dubbla eller anropar direkta metoder på flera enheter. Dessa förfrågningar initieras i molnet.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "jobCompleted",
            "category": "JobsOperations",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"jobId\":\"<jobId>\", \"sdkVersion\": \"<sdkVersion>\",\"messageSize\": <messageSize>,\"filter\":\"DeviceId IN ['1414ded9-b445-414d-89b9-e48e8c6285d5']\",\"startTimeUtc\":\"Wednesday, September 13, 2017\",\"duration\":\"0\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="direct-methods"></a>Direkta metoder

Kategorin direkta metoder spårar interaktioner för begäran och svar som skickas till enskilda enheter. Dessa förfrågningar initieras i molnet.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "send",
            "category": "DirectMethods",
            "level": "Information",
            "durationMs": "1",
            "properties": "{\"deviceId\":<messageSize>, \"RequestSize\": 1, \"ResponseSize\": 1, \"sdkVersion\": \"2017-07-11\"}",
            "location": "Resource location"
        }
    ]
}
```

### <a name="distributed-tracing-preview"></a>Distribuerad spårning (för hands version)

Den distribuerade spårnings kategorin spårar korrelations-ID: n för meddelanden som har spårnings kontext rubriken. Om du vill aktivera dessa loggar fullständigt måste kod på klient sidan uppdateras genom att följa [analys och diagnostisera IoT-program från slut punkt till slut punkt med IoT Hub Distributed tracing (för hands version)](iot-hub-distributed-tracing.md).

Observera att `correlationId` överensstämmer med [spårnings kontext](https://github.com/w3c/trace-context) förslaget för W3C, där det innehåller `trace-id` både och `span-id` .

#### <a name="iot-hub-d2c-device-to-cloud-logs"></a>IoT Hub D2C-loggar (från enhet till moln)

IoT Hub registrerar loggen när ett meddelande som innehåller giltiga spårnings egenskaper kommer till IoT Hub.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubD2C",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Receive message success",
            "durationMs": "",
            "properties": "{\"messageSize\": 1, \"deviceId\":\"<deviceId>\", \"callerLocalTimeUtc\": : \"2017-02-22T03:27:28.633Z\", \"calleeLocalTimeUtc\": \"2017-02-22T03:27:28.687Z\"}",
            "location": "Resource location"
        }
    ]
}
```

Här `durationMs` beräknas inte som IoT Hub klockan kanske inte är synkroniserad med enhets klockan och därför kan en varaktighets beräkning vara missvisande. Vi rekommenderar att du skriver logik med hjälp av tidsstämplar i `properties` avsnittet för att samla in toppar i svars tid från enhet till moln.

| Egenskap | Typ | Description |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **messageSize** | Integer | Storleken på meddelanden från enhet till moln i byte |
| **deviceId** | Sträng med ASCII 7-bitars alfanumeriska tecken | Enhetens identitet |
| **callerLocalTimeUtc** | UTC-tidsstämpel | Skapande tid för meddelandet som rapporteras av lokal enhets klocka |
| **calleeLocalTimeUtc** | UTC-tidsstämpel | Tiden för meddelande ankomsten vid IoT Hubens gateway som rapporteras av IoT Hub klockan på tjänst Sidan |

#### <a name="iot-hub-ingress-logs"></a>IoT Hub ingress-loggar

IoT Hub registrerar loggen när ett meddelande som innehåller giltiga spårnings egenskaper skriver till intern eller inbyggd Händelsehubben.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubIngress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Ingress message success",
            "durationMs": "10",
            "properties": "{\"isRoutingEnabled\": \"true\", \"parentSpanId\":\"0144d2590aacd909\"}",
            "location": "Resource location"
        }
    ]
}
```

I `properties` avsnittet innehåller den här loggen ytterligare information om meddelande ingångar.

| Egenskap | Typ | Description |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **isRoutingEnabled** | Sträng | Antingen sant eller falskt anger om meddelanderoutning är aktiverat i IoT Hub |
| **parentSpanId** | Sträng | Det överordnade meddelandets [span-ID](https://w3c.github.io/trace-context/#parent-id) , som skulle vara D2C meddelande spårning i det här fallet |

#### <a name="iot-hub-egress-logs"></a>IoT Hub utgående loggar

IoT Hub registrerar loggen när [routning](iot-hub-devguide-messages-d2c.md) är aktiverat och meddelandet skrivs till en [slut punkt](iot-hub-devguide-endpoints.md). Om routning inte är aktiverat registrerar IoT Hub inte den här loggen.

```json
{
    "records":
    [
        {
            "time": "UTC timestamp",
            "resourceId": "Resource Id",
            "operationName": "DiagnosticIoTHubEgress",
            "category": "DistributedTracing",
            "correlationId": "00-8cd869a412459a25f5b4f31311223344-98ac3578922acd26-01",
            "level": "Information",
            "resultType": "Success",
            "resultDescription":"Egress message success",
            "durationMs": "10",
            "properties": "{\"endpointType\": \"EventHub\", \"endpointName\": \"myEventHub\", \"parentSpanId\":\"349810a9bbd28730\"}",
            "location": "Resource location"
        }
    ]
}
```

I `properties` avsnittet innehåller den här loggen ytterligare information om meddelande ingångar.

| Egenskap | Typ | Description |
|--------------------|-----------------------------------------------|------------------------------------------------------------------------------------------------|
| **endpointName** | Sträng | Namnet på dirigerings slut punkten |
| **endpointType** | Sträng | Typ av Dirigerings slut punkt |
| **parentSpanId** | Sträng | [Intervall-ID: t](https://w3c.github.io/trace-context/#parent-id) för det överordnade meddelandet, som skulle vara IoT Hub ingresss meddelande spårning i det här fallet |

### <a name="configurations"></a>Konfigurationer

IoT Hub konfigurations loggar spårar händelser och fel för funktionen automatisk enhets hantering.

```json
{
    "records":
    [
         {
             "time": "2019-09-24T17:21:52Z",
             "resourceId": "Resource Id",
             "operationName": "ReadManyConfigurations",
             "category": "Configurations",
             "resultType": "",
             "resultDescription": "",
             "level": "Information",
             "durationMs": "17",
             "properties": "{\"configurationId\":\"\",\"sdkVersion\":\"2018-06-30\",\"messageSize\":\"0\",\"statusCode\":null}",
             "location": "southcentralus"
         }
    ]
}
```

### <a name="device-streams-preview"></a>Enhets strömmar (förhands granskning)

Kategorin enhets strömmar spårar interaktioner för begär ande svar som skickas till enskilda enheter.

```json
{
    "records":
    [
         {
             "time": "2019-09-19T11:12:04Z",
             "resourceId": "Resource Id",
             "operationName": "invoke",
             "category": "DeviceStreams",
             "resultType": "",
             "resultDescription": "",    
             "level": "Information",
             "durationMs": "74",
             "properties": "{\"deviceId\":\"myDevice\",\"moduleId\":\"myModule\",\"sdkVersion\":\"2019-05-01-preview\",\"requestSize\":\"3\",\"responseSize\":\"5\",\"statusCode\":null,\"requestName\":\"myRequest\",\"direction\":\"c2d\"}",
             "location": "Central US"
         }
    ]
}
```

## <a name="azure-monitor-logs-tables"></a>Azure Monitor loggar tabeller
<!-- REQUIRED. Please keep heading in this order -->

Det här avsnittet refererar till alla Azure Monitor loggar Kusto-tabeller som är relevanta för Azure IoT Hub och som är tillgängliga för Query av Log Analytics. En lista över dessa tabeller och länkar till mer information om IoT Hub resurs typ finns i [IoT Hub](/azure/azure-monitor/reference/tables/tables-resourcetype#iot-hub) i tabell referensen för Azure Monitor loggar.

En referens för alla Azure Monitor loggar/Log Analytics tabeller finns i [Azure Monitor logg tabell referens](/azure/azure-monitor/reference/tables/tables-resourcetype).

## <a name="see-also"></a>Se även

* Se [övervaka Azure-IoT Hub](monitor-iot-hub.md) för en beskrivning av övervakning av azure-IoT Hub.
* Mer information om övervakning av Azure-resurser finns i [övervaka Azure-resurser med Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md) .
