---
title: Exportera data från Azure IoT Central | Microsoft Docs
description: Hur du använder den nya data exporten för att exportera dina IoT-data till Azure och anpassade moln mål.
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 01/27/2021
ms.topic: how-to
ms.service: iot-central
ms.custom: contperf-fy21q1, contperf-fy21q3
ms.openlocfilehash: 7152012c7c4a342c7491e5f8b835eaede4269c4c
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2021
ms.locfileid: "100522622"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export"></a>Exportera IoT-data till moln mål med hjälp av data export

> [!Note]
> I den här artikeln beskrivs data export funktionerna i IoT Central. Information om äldre funktioner för data export finns i [Exportera IoT-data till moln mål med hjälp av data export (bakåtkompatibelt)](./howto-export-data-legacy.md).

Den här artikeln beskriver hur du använder den nya data export funktionen i Azure IoT Central. Använd den här funktionen för att kontinuerligt exportera filtrerade och berikade IoT-data från ditt IoT Central-program. Data exporten skickar ändringar i nära real tid till andra delar av moln lösningen för att kunna använda insikter, analyser och lagring med varma sökvägar.

Du kan till exempel:

- Exportera kontinuerligt data och egenskaps ändringar i JSON-format i nära real tid.
- Filtrera data strömmar för att exportera data som matchar anpassade villkor.
- Utöka data strömmarna med anpassade värden och egenskaps värden från enheten.
- Skicka data till mål som Azure Event Hubs, Azure Service Bus, Azure Blob Storage och webhook-slutpunkter.

> [!Tip]
> När du aktiverar data export får du bara data från dessa tidpunkter. För närvarande går det inte att hämta data under en tid då data exporten var avstängd. Aktivera data export tidigt om du vill behålla mer historiska data.

## <a name="prerequisites"></a>Förutsättningar

Om du vill använda data export funktioner måste du ha ett [v3-program](howto-get-app-info.md)och du måste ha behörighet för [data export](howto-manage-users-roles.md) .

Om du har ett v2-program, se [migrera ditt v2 IoT Central-program till v3](howto-migrate.md).

## <a name="set-up-export-destination"></a>Konfigurera export mål

Export målet måste finnas innan du konfigurerar dina data export. Följande mål typer är tillgängliga för närvarande:

- Azure Event Hubs
- Azure Service Bus-kö
- Azure Service Bus-ämne
- Azure Blob Storage
- Webhook

### <a name="create-an-event-hubs-destination"></a>Skapa ett Event Hubs-mål

Följ dessa steg om du inte har ett befintligt Event Hubs namn område att exportera till:

1. Skapa ett [nytt Event Hubs-namnområde i Azure Portal](https://ms.portal.azure.com/#create/Microsoft.EventHub). Du kan läsa mer i [Azure Event Hubs-dokument](../../event-hubs/event-hubs-create.md).

1. Skapa en Event Hub i Event Hubs namn området. Gå till ditt namn område och välj **+ Event Hub** överst för att skapa en Event Hub-instans.

1. Generera en nyckel som ska användas när du konfigurerar data export i IoT Central:

    - Välj den instans av händelsehubben som du skapade.
    - Välj **inställningar > principer för delad åtkomst**.
    - Skapa en ny nyckel eller Välj en befintlig nyckel som har **send** -behörighet.
    - Kopiera antingen den primära eller sekundära anslutnings strängen. Du använder den här anslutnings strängen för att skapa ett nytt mål i IoT Central.
    - Du kan också skapa en anslutnings sträng för hela Event Hubs namn området:
        1. Gå till ditt Event Hubs namnrum i Azure Portal.
        2. Under **Inställningar** väljer du **principer för delad åtkomst**
        3. Skapa en ny nyckel eller Välj en befintlig nyckel som har **send** -behörighet.
        4. Kopiera antingen den primära eller sekundära anslutnings strängen
        
### <a name="create-a-service-bus-queue-or-topic-destination"></a>Skapa en Service Bus kö eller ett ämnes mål

Följ dessa steg om du inte har ett befintligt Service Bus namn område att exportera till:

1. Skapa ett [nytt Service Bus-namnområde i Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Du kan läsa mer i [Azure Service Bus dokument](../../service-bus-messaging/service-bus-create-namespace-portal.md).

1. Om du vill skapa en kö eller ett ämne att exportera till går du till din Service Bus-namnrymd och väljer **+ kö** eller **+ ämne**.

1. Generera en nyckel som ska användas när du konfigurerar data export i IoT Central:

    - Välj den kö eller det ämne som du skapade.
    - Välj **Inställningar/principer för delad åtkomst**.
    - Skapa en ny nyckel eller Välj en befintlig nyckel som har **send** -behörighet.
    - Kopiera antingen den primära eller sekundära anslutnings strängen. Du använder den här anslutnings strängen för att skapa ett nytt mål i IoT Central.
    - Du kan också skapa en anslutnings sträng för hela Service Bus namn området:
        1. Gå till ditt Service Bus namnrum i Azure Portal.
        2. Under **Inställningar** väljer du **principer för delad åtkomst**
        3. Skapa en ny nyckel eller Välj en befintlig nyckel som har **send** -behörighet.
        4. Kopiera antingen den primära eller sekundära anslutnings strängen

### <a name="create-an-azure-blob-storage-destination"></a>Skapa ett Azure Blob Storage-mål

Följ dessa steg om du inte har ett befintligt Azure Storage-konto att exportera till:

1. Skapa ett [nytt lagrings konto i Azure Portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Du kan lära dig mer om att skapa nya [Azure Blob Storage-konton](../../storage/blobs/storage-quickstart-blobs-portal.md) eller [Azure Data Lake Storage v2-lagrings konton](../../storage/common/storage-account-create.md). Data export kan bara skriva data till lagrings konton som stöder block-blobbar. I följande lista visas kända kompatibla lagrings konto typer:

    |Prestanda nivå|Kontotyp|
    |-|-|
    |Standard|Generell användning v2|
    |Standard|Generell användning v1|
    |Standard|Blob Storage|
    |Premium|Block Blob Storage|

1. Om du vill skapa en behållare i ditt lagrings konto går du till ditt lagrings konto. Under **BLOB service** väljer du **Bläddra i blobbar**. Välj **+ behållare** överst för att skapa en ny behållare.

1. Skapa en anslutnings sträng för ditt lagrings konto genom att gå till **inställningar > åtkomst nycklar**. Kopiera en av de två anslutnings strängarna.

### <a name="create-a-webhook-endpoint"></a>Skapa en webhook-slutpunkt

Du kan exportera data till en offentligt tillgänglig HTTP webhook-slutpunkt. Du kan skapa en test-webhook-slutpunkt med [RequestBin](https://requestbin.net/). RequestBin begränsar begäran när gränsen för begäran nås:

1. Öppna [RequestBin](https://requestbin.net/).
2. Skapa en ny RequestBin och kopiera **bin-URL: en**. Du använder den här URL: en när du testar dina data export.

## <a name="set-up-data-export"></a>Konfigurera data export

Nu när du har ett mål för att exportera data till, konfigurerar du data export i IoT Central program:

1. Logga in på ditt IoT Central-program.

1. Välj **data export** i det vänstra fönstret.

    > [!Tip]
    > Om du inte ser **data export** i det vänstra fönstret har du inte behörighet att konfigurera data export i din app. Prata med en administratör för att konfigurera data export.

1. Välj **+ ny export**.

1. Ange ett visnings namn för den nya exporten och se till att data export har **Aktiver ATS**.

1. Välj vilken typ av data som ska exporteras. I följande tabell visas de data export typer som stöds:

    | Datatyp | Beskrivning | Dataformat |
    | :------------- | :---------- | :----------- |
    |  Telemetri | Exportera telemetri meddelanden från enheter i nära real tid. Varje exporterat meddelande innehåller det fullständiga innehållet i det ursprungliga enhets meddelandet, normaliserat.   |  [Meddelande format för telemetri](#telemetry-format)   |
    | Egenskaps ändringar | Exportera ändringar av enhets-och moln egenskaper i nära real tid. För skrivskyddade enhets egenskaper exporteras ändringar av de rapporterade värdena. För Läs-och skriv egenskaper exporteras både rapporterade och önskade värden. | [Meddelande format för egenskaps ändring](#property-changes-format) |

<a name="DataExportFilters"></a>
1. Du kan också lägga till filter för att minska mängden exporterade data. Det finns olika typer av filter tillgängliga för varje data export typ:

    Om du vill filtrera telemetri kan du:

    - **Filtrera** den exporterade data strömmen så att den bara innehåller telemetri från enheter som matchar enhets namn, enhets-ID och enhets mal len filter villkor.
    - **Filtrera** över funktioner: om du väljer ett telemetri-objekt i list rutan **namn** innehåller den exporterade strömmen endast telemetri som uppfyller filter villkoret. Om du väljer en enhet eller ett moln egenskaps objekt i list rutan **namn** , innehåller den exporterade strömmen bara telemetri från enheter med egenskaper som matchar filter villkoret.
    - **Filter för meddelande egenskap**: enheter som använder enhets-SDK: er kan skicka *meddelande egenskaper* eller *program egenskaper* för varje telemetri. Egenskaperna är en väska med nyckel/värde-par som Taggar meddelandet med anpassade identifierare. Om du vill skapa ett filter för meddelande egenskaper anger du den meddelande egenskaps nyckel som du söker efter och anger ett villkor. Endast telemetri-meddelanden med egenskaper som matchar det angivna filter villkoret exporteras. Följande sträng jämförelse operatorer stöds: Equals, är inte lika med, innehåller inte, innehåller inte, finns inte. [Läs mer om program egenskaper från IoT Hub dokument](../../iot-hub/iot-hub-devguide-messages-construct.md).

    Om du vill filtrera egenskaps ändringar använder du ett **funktions filter**. Välj ett egenskaps objekt i list rutan. Den exporterade data strömmen innehåller bara ändringar av den valda egenskapen som uppfyller filter villkoret.

<a name="DataExportEnrichmnents"></a>
1. Du kan också utöka exporterade meddelanden med ytterligare metadata för nyckel/värde-par. Följande-funktioner är tillgängliga för telemetri-och egenskaps ändringar data export typer:

    - **Anpassad sträng**: lägger till en anpassad statisk sträng i varje meddelande. Ange valfri nyckel och ange valfritt sträng värde.
    - **Egenskap**: lägger till den aktuella enheten rapporterade egenskapen eller moln egenskap svärdet i varje meddelande. Ange valfri nyckel och välj en enhets-eller moln egenskap. Om det exporterade meddelandet kommer från en enhet som inte har den angivna egenskapen så får inte det exporterade meddelandet berikning.

1. Lägg till ett nytt mål eller Lägg till ett mål som du redan har skapat. Välj länken **skapa en ny** länk och Lägg till följande information:

    - **Målnamn**: visnings namnet för målet i IoT Central.
    - **Måltyp: Välj** typ av mål. Om du inte redan har konfigurerat målet läser du [Konfigurera export destination](#set-up-export-destination).
    - För Azure Event Hubs, Azure Service Bus kö eller ämne, klistra in anslutnings strängen för resursen och ange Skift läges känslig händelsehubben, kö eller ämnes namn om det behövs.
    - För Azure Blob Storage klistrar du in anslutnings strängen för resursen och anger Skift läges känslig behållar namn vid behov.
    - För webhook klistrar du in återanrops-URL: en för webhook-slutpunkten. Du kan också konfigurera webhook-auktorisering (OAuth 2,0 och autentiseringstoken) och lägga till anpassade rubriker. 
        - För OAuth 2,0 stöds endast flödet för klientautentiseringsuppgifter. När målet sparas kommer IoT Central att kommunicera med OAuth-providern för att hämta en autentiseringstoken. Denna token kopplas till rubriken "Authorization" för varje meddelande som skickas till det här målet.
        - För autentiseringstoken kan du ange ett token-värde som ska kopplas direkt till "Authorization"-huvudet för varje meddelande som skickas till det här målet.
    - Välj **Skapa**.

1. Välj **+ destination** och välj ett mål i list rutan. Du kan lägga till upp till fem destinationer till en enda export.

1. När du är klar med konfigurationen av exporten väljer du **Spara**. Efter några minuter visas dina data i dina mål.

## <a name="monitor-your-export"></a>Övervaka exporten

Förutom att se status för dina exporter i IoT Central kan du använda [Azure Monitor](../../azure-monitor/overview.md) för att se hur mycket data du exporterar och eventuella export fel. Du kan komma åt export-och enhets hälso mått i diagram i Azure Portal, med en REST API eller med frågor i PowerShell eller Azure CLI. För närvarande kan du övervaka följande data export mått i Azure Monitor:

- Antal meddelanden inkommande att exportera innan filter tillämpas.
- Antal meddelanden som passerar genom filter.
- Antal meddelanden som har exporter ATS till mål.
- Antal fel som har påträffats.

Mer information finns i [övervaka den övergripande hälsan för ett IoT Central program](howto-monitor-application-health.md).

## <a name="destinations"></a>Mål

### <a name="azure-blob-storage-destination"></a>Azure Blob Storage-mål

Data exporteras en gång per minut, med varje fil som innehåller ändrings gruppen sedan föregående export. Exporterade data sparas i JSON-format. Standard Sök vägarna till de exporterade data i ditt lagrings konto är:

- Telemetri: _{container}/{app-id}/{partition_id}/{YYYY}/{MM}/{DD}/{hh}/{mm}/{filename}_
- Egenskaps ändringar: _{container}/{app-id}/{partition_id}/{YYYY}/{MM}/{DD}/{hh}/{mm}/{filename}_

Om du vill bläddra bland de exporterade filerna i Azure Portal navigerar du till filen och väljer **Redigera BLOB**.

### <a name="azure-event-hubs-and-azure-service-bus-destinations"></a>Azure Event Hubs och Azure Service Bus destinationer

Data exporteras i nära real tid. Informationen finns i meddelande texten och är i JSON-format kodad som UTF-8.

Meddelandets anteckningar eller systemets egenskaps uppsättning innehåller `iotcentral-device-id` fälten,, `iotcentral-application-id` `iotcentral-message-source` och `iotcentral-message-type` som har samma värden som motsvarande fält i meddelande texten.

### <a name="webhook-destination"></a>Webhook-mål

För Webhooks-mål exporteras även data i nära real tid. Informationen i meddelande texten är i samma format som för Event Hubs och Service Bus.

## <a name="telemetry-format"></a>Telemetri-format

Varje exporterat meddelande innehåller en normaliserad form av det fullständiga meddelande som enheten skickade i meddelande texten. Meddelandet är i JSON-format och kodat som UTF-8. Information i varje meddelande inkluderar:

- `applicationId`: ID: t för det IoT Central programmet.
- `messageSource`: Källan för meddelandet- `telemetry` .
- `deviceId`: ID för enheten som skickade telemetri meddelandet.
- `schema`: Namn och version för nytto Last schema.
- `templateId`: ID: t för den enhets mall som är kopplad till enheten.
- `enrichments`: Eventuella berikade inställningar i exporten.
- `messageProperties`: Ytterligare egenskaper som enheten skickade med meddelandet. Dessa egenskaper kallas ibland för *program egenskaper*. [Läs mer i IoT Hub dokument](../../iot-hub/iot-hub-devguide-messages-construct.md).

För Event Hubs och Service Bus exporterar IoT Central ett nytt meddelande snabbt när det får meddelandet från en enhet. I användar egenskaperna (kallas även program egenskaper) för varje meddelande, `iotcentral-device-id` `iotcentral-application-id` tas, och `iotcentral-message-source` inkluderas automatiskt i.

För Blob Storage grupperas och exporteras meddelanden en gång per minut.

I följande exempel visas ett exporterat telemetri-meddelande:

```json

{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "telemetry",
    "deviceId": "1vzb5ghlsg1",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:26:55.455Z",
    "telemetry": {
        "Activity": "running",
        "BloodPressure": {
            "Diastolic": 7,
            "Systolic": 71
        },
        "BodyTemperature": 98.73447010562934,
        "HeartRate": 88,
        "HeartRateVariability": 17,
        "RespiratoryRate": 13
    },
    "enrichments": {
      "userSpecifiedKey": "sampleValue"
    },
    "messageProperties": {
      "messageProp": "value"
    }
}
```
### <a name="message-properties"></a>Meddelande egenskaper

Telemetri-meddelanden har egenskaper för metadata förutom nytto lasten för telemetri. Föregående kodfragment visar exempel på system meddelanden som `deviceId` och `enqueuedTime` . Mer information om system meddelande egenskaperna finns i [system egenskaper för D2C IoT Hub-meddelanden](../../iot-hub/iot-hub-devguide-messages-construct.md#system-properties-of-d2c-iot-hub-messages).

Du kan lägga till egenskaper för telemetri-meddelanden om du behöver lägga till anpassade metadata i dina telemetri-meddelanden. Du måste till exempel lägga till en tidsstämpel när enheten skapar meddelandet.

Följande kodfragment visar hur du lägger till `iothub-creation-time-utc` egenskapen i meddelandet när du skapar den på enheten:

# <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
async function sendTelemetry(deviceClient, index) {
  console.log('Sending telemetry message %d...', index);
  const msg = new Message(
    JSON.stringify(
      deviceTemperatureSensor.updateSensor().getCurrentTemperatureObject()
    )
  );
  msg.properties.add("iothub-creation-time-utc", new Date().toISOString());
  msg.contentType = 'application/json';
  msg.contentEncoding = 'utf-8';
  await deviceClient.sendEvent(msg);
}
```

# <a name="java"></a>[Java](#tab/java)

```java
private static void sendTemperatureTelemetry() {
  String telemetryName = "temperature";
  String telemetryPayload = String.format("{\"%s\": %f}", telemetryName, temperature);

  Message message = new Message(telemetryPayload);
  message.setContentEncoding(StandardCharsets.UTF_8.name());
  message.setContentTypeFinal("application/json");
  message.setProperty("iothub-creation-time-utc", Instant.now().toString());

  deviceClient.sendEventAsync(message, new MessageIotHubEventCallback(), message);
  log.debug("My Telemetry: Sent - {\"{}\": {}°C} with message Id {}.", telemetryName, temperature, message.getMessageId());
  temperatureReadings.put(new Date(), temperature);
}
```

# <a name="c"></a>[C#](#tab/csharp)

```csharp
private async Task SendTemperatureTelemetryAsync()
{
  const string telemetryName = "temperature";

  string telemetryPayload = $"{{ \"{telemetryName}\": {_temperature} }}";
  using var message = new Message(Encoding.UTF8.GetBytes(telemetryPayload))
  {
      ContentEncoding = "utf-8",
      ContentType = "application/json",
  };
  message.Properties.Add("iothub-creation-time-utc", DateTime.UtcNow.ToString("yyyy-MM-ddTHH:mm:ssZ"));
  await _deviceClient.SendEventAsync(message);
  _logger.LogDebug($"Telemetry: Sent - {{ \"{telemetryName}\": {_temperature}°C }}.");
}
```

# <a name="python"></a>[Python](#tab/python)

```python
async def send_telemetry_from_thermostat(device_client, telemetry_msg):
    msg = Message(json.dumps(telemetry_msg))
    msg.custom_properties["iothub-creation-time-utc"] = datetime.now(timezone.utc).isoformat()
    msg.content_encoding = "utf-8"
    msg.content_type = "application/json"
    print("Sent message")
    await device_client.send_message(msg)
```

---

Följande fragment visar den här egenskapen i meddelandet som exporter ATS till Blob Storage:

```json
{
  "applicationId":"5782ed70-b703-4f13-bda3-1f5f0f5c678e",
  "messageSource":"telemetry",
  "deviceId":"sample-device-01",
  "schema":"default@v1",
  "templateId":"urn:modelDefinition:mkuyqxzgea:e14m1ukpn",
  "enqueuedTime":"2021-01-29T16:45:39.143Z",
  "telemetry":{
    "temperature":8.341033560421833
  },
  "messageProperties":{
    "iothub-creation-time-utc":"2021-01-29T16:45:39.021Z"
  },
  "enrichments":{}
}
```

## <a name="property-changes-format"></a>Format för egenskaps ändringar

Varje meddelande eller post representerar en ändring i en enhets-eller moln egenskap. För enhets egenskaper exporteras endast ändringar i det rapporterade värdet som ett separat meddelande. Informationen i det exporterade meddelandet innehåller:

- `applicationId`: ID: t för det IoT Central programmet.
- `messageSource`: Källan för meddelandet- `properties` .
- `messageType`: Antingen `cloudPropertyChange` , `devicePropertyDesiredChange` eller `devicePropertyReportedChange` .
- `deviceId`: ID för enheten som skickade telemetri meddelandet.
- `schema`: Namn och version för nytto Last schema.
- `templateId`: ID: t för den enhets mall som är kopplad till enheten.
- `enrichments`: Eventuella berikade inställningar i exporten.

För Event Hubs och Service Bus exporterar IoT Central nya meddelande data till händelsehubben eller Service Bus kö eller ämne i nära real tid. I användar egenskaperna (kallas även program egenskaper) för varje meddelande, inkluderas,, `iotcentral-device-id` `iotcentral-application-id` och tas `iotcentral-message-source` automatiskt med `iotcentral-message-type` .

För Blob Storage grupperas och exporteras meddelanden en gång per minut.

I följande exempel visas ett meddelande om att en exporterad egenskaps ändring mottagits i Azure Blob Storage.

```json
{
    "applicationId": "1dffa667-9bee-4f16-b243-25ad4151475e",
    "messageSource": "properties",
    "messageType": "cloudPropertyChange",
    "deviceId": "18a985g1fta",
    "schema": "default@v1",
    "templateId": "urn:qugj6vbw5:___qbj_27r",
    "enqueuedTime": "2020-08-05T22:37:32.942Z",
    "properties": [{
        "name": "MachineSerialNumber",
        "value": "abc"
    }],
    "enrichments": {
        "userSpecifiedKey" : "sampleValue"
    }
}
```

## <a name="comparison-of-legacy-data-export-and-data-export"></a>Jämförelse av äldre data export och data export

I följande tabell visas skillnaderna mellan [äldre data export](howto-export-data-legacy.md) och nya funktioner för data export:

| Funktioner  | Äldre data export | Ny data export |
| :------------- | :---------- | :----------- |
| Tillgängliga data typer | Telemetri, enheter, enhetsspecifika mallar | Telemetri, egenskaps ändringar |
| Filtrering | Inget | Beror på vilken data typ som exporteras. För telemetri, filtrera efter telemetri, meddelande egenskaper, egenskaps värden |
| Berikningar | Inget | Utöka med en anpassad sträng eller ett egenskaps värde på enheten |
| Mål | Azure Event Hubs, Azure Service Bus köer och ämnen, Azure Blob Storage | Samma som för äldre data export plus Webhooks|
| Program versioner som stöds | V2, V3 | Endast v3 |
| Viktiga begränsningar | 5 exporter per app, 1 mål per export | 10 exporter – mål anslutningar per app |

## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du använder den nya data exporten är ett föreslaget nästa steg att lära dig [hur du använder analyser i IoT Central](./howto-create-analytics.md)
