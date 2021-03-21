---
title: Azure IoT Hub Operations Monitoring (inaktuell) | Microsoft Docs
description: Så här använder du Azure IoT Hub Operations Monitoring för att övervaka status för åtgärder i IoT Hub i real tid.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: robinsh
ms.custom: amqp, devx-track-csharp
ms.openlocfilehash: 045d5693c4388c6285bc6983ac2a385ceac9f6d0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "94408132"
---
# <a name="iot-hub-operations-monitoring-deprecated"></a>IoT Hub Operations Monitoring (inaktuell)

Med IoT Hub åtgärds övervakning kan du övervaka statusen för åtgärder i IoT Hub i real tid. IoT Hub spårar händelser i flera kategorier av åtgärder. Du kan välja att skicka händelser från en eller flera kategorier till en slut punkt i IoT Hub för bearbetning. Du kan övervaka data för fel eller ställa in mer komplex bearbetning baserat på data mönster.

>[!NOTE]
>IoT Hub **drifts övervakning är föråldrad och har tagits bort från IoT Hub den 10 mars 2019**. Information om hur du övervakar åtgärder och hälsa för IoT Hub finns i [övervaka IoT Hub](monitor-iot-hub.md). Mer information om tids linjen för tids linjen finns i [övervaka dina Azure IoT-lösningar med Azure Monitor och Azure Resource Health](https://azure.microsoft.com/blog/monitor-your-azure-iot-solutions-with-azure-monitor-and-azure-resource-health).

IoT Hub övervakar sex kategorier av händelser:

* Enhets identitets åtgärder
* Telemetri för enhet
* Meddelanden från moln till enhet
* Anslutningar
* Fil överföringar
* Meddelanderoutning

> [!IMPORTANT]
> IoT Hub Operations Monitoring garanterar inte tillförlitlig eller ordnad leverans av händelser. Beroende på IoT Hub underliggande infrastrukturen kan vissa händelser gå förlorade eller levereras i rätt ordning. Använd Operations Monitoring för att generera aviseringar baserat på fel signaler, till exempel misslyckade anslutnings försök eller anslutnings försök med hög frekvens för vissa enheter. Du bör inte förlita dig på åtgärder för övervakning av händelser för att skapa en konsekvent lagring för enhets tillstånd, t. ex. en lagrad eller frånkopplad status för en enhet. 

## <a name="how-to-enable-operations-monitoring"></a>Aktivera drift övervakning

1. Skapa en IoT-hubb. Du hittar anvisningar om hur du skapar en IoT Hub i guiden [Kom igång](quickstart-send-telemetry-dotnet.md) .

2. Öppna bladet med din IoT Hub. Därifrån klickar du på **Åtgärds övervakning**.

    ![Åtkomst till övervaknings konfiguration i portalen](./media/iot-hub-operations-monitoring/enable-OM-1.png)

3. Välj de övervaknings kategorier som du vill övervaka och klicka sedan på **Spara**. Händelserna är tillgängliga för läsning från den Event Hub-kompatibla slut punkten som visas i **övervaknings inställningarna**. IoT Hub slut punkten anropas `messages/operationsmonitoringevents` .

    ![Konfigurera drift övervakning på din IoT Hub](./media/iot-hub-operations-monitoring/enable-OM-2.png)

> [!NOTE]
> Om du väljer **utförlig** övervakning för kategorin **anslutningar** kan IoT Hub generera ytterligare diagnostiska meddelanden. För alla andra kategorier ändrar **utförlig** inställning mängden information IoT Hub innehåller i varje fel meddelande.

## <a name="event-categories-and-how-to-use-them"></a>Händelse kategorier och hur de används

Varje kategori för åtgärds övervakning spårar en annan typ av interaktion med IoT Hub, och varje övervaknings kategori har ett schema som definierar hur händelser i den kategorin är strukturerade.

### <a name="device-identity-operations"></a>Enhets identitets åtgärder

Kategorin enhets identitets åtgärder spårar fel som inträffar när du försöker skapa, uppdatera eller ta bort en post i din IoT Hubs identitets register. Att spåra den här kategorin är användbart för etablerings scenarier.

```json
{
    "time": "UTC timestamp",
    "operationName": "create",
    "category": "DeviceIdentityOperations",
    "level": "Error",
    "statusCode": 4XX,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "durationMs": 1234,
    "userAgent": "userAgent",
    "sharedAccessPolicy": "accessPolicy"
}
```

### <a name="device-telemetry"></a>Telemetri för enhet

Kategorin hets telemetri spårar fel som inträffar på IoT-hubben och är relaterade till pipelinen för telemetri. Den här kategorin innehåller fel som uppstår när du skickar telemetri-händelser (till exempel begränsning) och tar emot telemetri-händelser (till exempel obehörig läsare). Den här kategorin kan inte fånga fel som orsakas av kod som körs på själva enheten.

```json
{
    "messageSizeInBytes": 1234,
    "batching": 0,
    "protocol": "Amqp",
    "authType": "{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "time": "UTC timestamp",
    "operationName": "ingress",
    "category": "DeviceTelemetry",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "EventProcessedUtcTime": "UTC timestamp",
    "PartitionId": 1,
    "EventEnqueuedUtcTime": "UTC timestamp"
}
```

### <a name="cloud-to-device-commands"></a>Moln till enhet-kommandon

Kommando kategorin för moln-till-enhet spårar fel som uppstår på IoT-hubben och är relaterade till pipeline för meddelande från moln till enhet. Den här kategorin innehåller fel som uppstår när meddelanden skickas från molnet till enheten (t. ex. obehörig avsändare), mottagning av meddelanden från moln till enhet (t. ex. leverans antal har överskridits) och mottagning av meddelanden från moln till enhet (till exempel feedback upphör). Den här kategorin fångar inte upp fel från en enhet som felaktigt hanterar ett meddelande från molnet till enheten om meddelandet från molnet till enheten levererades utan problem.

```json
{
    "messageSizeInBytes": 1234,
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "deliveryAcknowledgement": 0,
    "protocol": "Amqp",
    "time": " UTC timestamp",
    "operationName": "ingress",
    "category": "C2DCommands",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "EventProcessedUtcTime": "UTC timestamp",
    "PartitionId": 1,
    "EventEnqueuedUtcTime": "UTC timestamp"
}
```

### <a name="connections"></a>Anslutningar

Kategorin anslutningar spårar fel som inträffar när enheter ansluter eller kopplar från en IoT-hubb. Spårning av den här kategorin är användbar för att identifiera otillåtna anslutnings försök och för att spåra när en anslutning förloras för enheter i områden med dålig anslutning.

```json
{
    "durationMs": 1234,
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "protocol": "Amqp",
    "time": " UTC timestamp",
    "operationName": "deviceConnect",
    "category": "Connections",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID"
}
```

### <a name="file-uploads"></a>Fil överföringar

Kategorin fil uppladdning spårar fel som uppstår på IoT-hubben och är relaterade till fil uppladdnings funktionen. Den här kategorin omfattar:

* Fel som uppstår med SAS-URI: n, till exempel när den upphör att gälla innan en enhet meddelar navet om en slutförd uppladdning.

* Misslyckade uppladdningar som rapporter ATS av enheten.

* Fel som inträffar när det inte går att hitta en fil i lagringen under IoT Hub meddelande om att meddelanden skapas.

Den här kategorin kan inte fånga fel som sker direkt när enheten laddar upp en fil till lagringen.

```json
{
    "authType": "{\"scope\":\"hub\",\"type\":\"sas\",\"issuer\":\"iothub\"}",
    "protocol": "HTTP",
    "time": " UTC timestamp",
    "operationName": "ingress",
    "category": "fileUpload",
    "level": "Error",
    "statusCode": 4XX,
    "statusType": 4XX001,
    "statusDescription": "MessageDescription",
    "deviceId": "device-ID",
    "blobUri": "http//bloburi.com",
    "durationMs": 1234
}
```

### <a name="message-routing"></a>Meddelanderoutning

Kategorin meddelande cirkulation spårar fel som inträffar under utvärderingen av meddelande vägar och slut punkts hälsa som uppfattas av IoT Hub. Den här kategorin innehåller händelser, till exempel när en regel utvärderas till "odefinierad", när IoT Hub markerar en slut punkt som död och andra fel som tas emot från en slut punkt. Den här kategorin innehåller inga fel meddelanden om själva meddelandena (till exempel enhets begränsnings fel), som rapporteras i kategorin "enhets telemetri".

```json
{
    "messageSizeInBytes": 1234,
    "time": "UTC timestamp",
    "operationName": "ingress",
    "category": "routes",
    "level": "Error",
    "deviceId": "device-ID",
    "messageId": "ID of message",
    "routeName": "myroute",
    "endpointName": "myendpoint",
    "details": "ExternalEndpointDisabled"
}
```

## <a name="connect-to-the-monitoring-endpoint"></a>Anslut till övervaknings slut punkten

Övervaknings slut punkten på din IoT Hub är en Event Hub-kompatibel slut punkt. Du kan använda valfri mekanism som fungerar med Event Hubs för att läsa övervaknings meddelanden från den här slut punkten. Följande exempel skapar en grundläggande läsare som inte lämpar sig för en hög data flödes distribution. Mer information om hur du bearbetar meddelanden från Event Hubs finns i självstudiekursen [Komma igång med Event Hubs](../event-hubs/event-hubs-dotnet-standard-getstarted-send.md).

Om du vill ansluta till övervaknings slut punkten behöver du en anslutnings sträng och slut punkts namnet. Följande steg visar hur du hittar de värden som krävs i portalen:

1. I portalen navigerar du till ditt IoT Hub resurs blad.

2. Välj **Åtgärds övervakning** och anteckna det **Event Hub-kompatibla namn** -och **Event Hub-kompatibla slut punkts** värden:

    ![Event Hub-kompatibla slut punkts värden](./media/iot-hub-operations-monitoring/monitoring-endpoint.png)

3. Välj **principer för delad åtkomst** och välj sedan **tjänst**. Anteckna värdet för **primär nyckel** :

    ![Princip primär nyckel för tjänst för delad åtkomst](./media/iot-hub-operations-monitoring/service-key.png)

Följande C#-kod exempel tas från en Visual Studio **Windows klassisk Desktop** C#-konsol app. Projektet har NuGet-paketet **windowsazure. Service Bus** installerat.

* Ersätt plats hållaren för anslutnings strängen med en anslutnings sträng som använder den **Event Hub-kompatibla slut punkten** och tjänstens **primära nyckel** värden som du antecknade tidigare, enligt följande exempel:

    ```csharp
    "Endpoint={your Event Hub-compatible endpoint};SharedAccessKeyName=service;SharedAccessKey={your service primary key value}"
    ```

* Ersätt plats hållaren för övervakningens slut punkt med det **Event Hub-kompatibla namn** som du antecknade tidigare.

```csharp
class Program
{
    static string connectionString = "{your monitoring endpoint connection string}";
    static string monitoringEndpointName = "{your monitoring endpoint name}";
    static EventHubClient eventHubClient;

    static void Main(string[] args)
    {
        Console.WriteLine("Monitoring. Press Enter key to exit.\n");

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, monitoringEndpointName);
        var d2cPartitions = eventHubClient.GetRuntimeInformation().PartitionIds;
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
            }
        }
    }
}
```

## <a name="next-steps"></a>Nästa steg

Mer information om hur du använder Azure Monitor för att övervaka IoT Hub finns i:

* [Övervaka IoT Hub](monitor-iot-hub.md)

* [Migrera från IoT Hub åtgärds övervakning till Azure Monitor](iot-hub-migrate-to-diagnostics-settings.md)