---
title: Exportera data från Azure IoT Central (bakåtkompatibelt) | Microsoft Docs
description: Så här exporterar du data från ditt Azure IoT Central-program till Azure Event Hubs, Azure Service Bus och Azure Blob Storage
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 06/25/2020
ms.topic: how-to
ms.service: iot-central
ms.openlocfilehash: d4c099d29a843b4c354ffb218887dc7ffab51771
ms.sourcegitcommit: 2488894b8ece49d493399d2ed7c98d29b53a5599
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2021
ms.locfileid: "98065447"
---
# <a name="export-iot-data-to-cloud-destinations-using-data-export-legacy"></a>Exportera IoT-data till moln mål med hjälp av data export (bakåtkompatibelt)

> [!Note]
> I den här artikeln beskrivs äldre data export funktioner i IoT Central.
>
> - Information om de senaste data export funktionerna finns i [Exportera IoT-data till moln mål med hjälp av data export](./howto-export-data.md).
> - Mer information om skillnaderna mellan funktionerna för för hands versions export och äldre data export finns i [jämförelse tabellen](./howto-export-data.md#comparison-of-legacy-data-export-and-data-export).

Den här artikeln beskriver hur du använder funktionen för data export i Azure IoT Central. Med den här funktionen kan du exportera dina data kontinuerligt till **azure Event Hubs**, **Azure Service Bus** eller **Azure Blob Storage** -instanser. Data export använder JSON-formatet och kan omfatta telemetri, enhets information och information om enhets mal len. Använd exporterade data för:

- Insikter och analys av varma sökvägar. Det här alternativet inkluderar utlöser anpassade regler i Azure Stream Analytics, utlöser anpassade arbets flöden i Azure Logic Apps eller skickar dem via Azure Functions som ska omvandlas.
- Kall vägs analys, till exempel utbildnings modeller i Azure Machine Learning eller långsiktig trend analys i Microsoft Power BI.

> [!Note]
> När du aktiverar data export får du bara data från dessa tidpunkter. För närvarande går det inte att hämta data under en tid då data exporten var avstängd. Aktivera data export tidigt om du vill behålla mer historiska data.

## <a name="prerequisites"></a>Förutsättningar

Du måste vara administratör i IoT Central programmet eller ha behörighet för data export.

## <a name="set-up-export-destination"></a>Konfigurera export mål

Export målet måste finnas innan du konfigurerar dina data export.

### <a name="create-event-hubs-namespace"></a>Skapa en namnrymd för Event Hubs

Följ dessa steg om du inte har ett befintligt Event Hubs namn område att exportera till:

1. Skapa ett [nytt Event Hubs-namnområde i Azure Portal](https://ms.portal.azure.com/#create/Microsoft.EventHub). Du kan läsa mer i [Azure Event Hubs-dokument](../../event-hubs/event-hubs-create.md).

2. Välj en prenumeration. Du kan exportera data till andra prenumerationer som inte är i samma prenumeration som ditt IoT Central-program. Du ansluter med hjälp av en anslutnings sträng i det här fallet.

3. Skapa en Event Hub i Event Hubs namn området. Gå till ditt namn område och välj **+ Event Hub** överst för att skapa en Event Hub-instans.

### <a name="create-service-bus-namespace"></a>Skapa Service Bus namn område

Följ dessa steg om du inte har ett befintligt Service Bus namn område att exportera till:

1. Skapa ett [nytt Service Bus-namnområde i Azure Portal](https://ms.portal.azure.com/#create/Microsoft.ServiceBus.1.0.5). Du kan läsa mer i [Azure Service Bus dokument](../../service-bus-messaging/service-bus-create-namespace-portal.md).
2. Välj en prenumeration. Du kan exportera data till andra prenumerationer som inte är i samma prenumeration som ditt IoT Central-program. Du ansluter med hjälp av en anslutnings sträng i det här fallet.

3. Om du vill skapa en kö eller ett ämne att exportera till går du till din Service Bus-namnrymd och väljer **+ kö** eller **+ ämne**.

När du väljer Service Bus som export mål får inte köer och ämnen ha sessioner eller dubblettidentifiering aktiverade. Om något av dessa alternativ är aktiverat kommer vissa meddelanden inte att tas emot i din kö eller ämne.

### <a name="create-storage-account"></a>Skapa lagringskonto

Följ dessa steg om du inte har ett befintligt Azure Storage-konto att exportera till:

1. Skapa ett [nytt lagrings konto i Azure Portal](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM). Du kan lära dig mer om att skapa nya [Azure Blob Storage-konton](../../storage/blobs/storage-quickstart-blobs-portal.md) eller [Azure Data Lake Storage v2-lagrings konton](../../storage/common/storage-account-create.md). Data export kan bara skriva data till lagrings konton som stöder block-blobbar. I följande lista visas kända kompatibla lagrings konto typer:

    |Prestanda nivå|Kontotyp|
    |-|-|
    |Standard|Generell användning v2|
    |Standard|Generell användning v1|
    |Standard|Blob Storage|
    |Premium|Block Blob Storage|

2. Skapa en behållare i ditt lagrings konto. Gå till ditt lagringskonto. Under **BLOB service** väljer du **Bläddra i blobbar**. Välj **+ behållare** överst för att skapa en ny behållare.

## <a name="set-up-data-export"></a>Konfigurera data export

Nu när du har ett mål att exportera data till, följer du dessa steg för att konfigurera data export.

1. Logga in på ditt IoT Central-program.

2. Välj **data export** i det vänstra fönstret.

    > [!Tip]
    > Om du inte ser **data export** i det vänstra fönstret har du inte behörighet att konfigurera data export i din app. Prata med en administratör för att konfigurera data export.

3. Välj knappen **+ ny** . Välj ett av **azure Blob Storage**, **azure Event Hubs**, **Azure Service Bus kö** eller **Azure Service Bus ämne** som export mål. Det maximala antalet exporter per program är fem.

4. Ange ett namn på exporten. I list rutan väljer du ditt **namn område** eller **anger en anslutnings sträng**.

    - Du ser bara lagrings konton, Event Hubs namnrymder och Service Bus namnrum i samma prenumeration som ditt IoT Central-program. Om du vill exportera till ett mål utanför den här prenumerationen väljer du **Ange en anslutnings sträng** och se steg 6.
    - För appar som har skapats med den kostnads fria pris Planen är det enda sättet att konfigurera data exporten via en anslutnings sträng. Appar i den kostnads fria pris Planen har ingen tillhör ande Azure-prenumeration.

    ![Skapa ny händelsehubben](media/howto-export-data-legacy/export-event-hub.png)

5. Välj en händelsehubben, en kö, ett ämne eller en behållare i list rutan.

6. Valfritt Om du väljer **Ange en anslutnings sträng** visas en ny ruta där du kan klistra in anslutnings strängen. Så här hämtar du anslutnings strängen för din:

    - Event Hubs eller Service Bus går du till namn området i Azure Portal:
        - Så här använder du en anslutnings sträng för hela namn området:
            1. Under **Inställningar** väljer du **principer för delad åtkomst**
            2. Skapa en ny nyckel eller Välj en befintlig nyckel som har **send** -behörighet.
            3. Kopiera antingen den primära eller sekundära anslutnings strängen
        - Om du vill använda anslutnings strängen för en speciell instans av Event Hub eller Service Bus kö eller ämne går du till **entiteter > Event Hubs** eller **entiteter > köer** eller **entiteter > ämnen**. Välj en angiven instans och följ stegen ovan för att få en anslutnings sträng.
    - Lagrings konto går du till lagrings kontot i Azure Portal:
        - Det finns bara stöd för anslutnings strängar för hela lagrings kontot. Anslutnings strängar som är begränsade till en enda behållare stöds inte.
          1. Under **Inställningar** väljer du **åtkomst nycklar**
          2. Kopiera antingen anslutnings strängen KEY1 eller key2-anslutningssträngen

    Klistra in i anslutnings strängen. Skriv in instans-eller Skift läges känslig **behållar namn**.

7. Under **data som ska exporteras** väljer du de typer av data som ska exporteras genom att ange typen till **på**.

8. **Om du** vill aktivera data export kontrollerar du att aktivera växling är **aktiverat** . Välj **Spara**.

9. Efter några minuter visas dina data i det valda målet.

## <a name="export-contents-and-format"></a>Exportera innehåll och format

Exporterade telemetridata innehåller hela det meddelande som dina enheter skickade till IoT Central, inte bara själva telemetri värden. Exporterade enhets data innehåller ändringar av egenskaper och metadata för alla enheter och exporterade enheter innehåller ändringar i alla enhets mallar.

För Event Hubs och Service Bus exporteras data i nära real tid. Datan finns i `body` egenskapen och är i JSON-format. Se nedan för exempel.

För Blob Storage exporteras data en gång per minut, med varje fil som innehåller ändrings gruppen sedan den senaste exporterade filen. Exporterade data placeras i tre mappar i JSON-format. Standard Sök vägarna i ditt lagrings konto är:

- Telemetri: _{container}/{app-ID}/Telemetry/{YYYY}/{MM}/{DD}/{hh}/{mm}/{filename}_
- Enheter: _{container}/{app-ID}/Devices/{YYYY}/{MM}/{DD}/{hh}/{mm}/{filename}_
- Enhets mallar: _{container}/{app-ID}/deviceTemplates/{YYYY}/{MM}/{DD}/{hh}/{mm}/{filename}_

Om du vill bläddra bland de exporterade filerna i Azure Portal navigerar du till filen och väljer fliken **Redigera BLOB** .

## <a name="telemetry"></a>Telemetri

För Event Hubs och Service Bus exporterar IoT Central ett nytt meddelande snabbt när det får meddelandet från en enhet. Varje exporterat meddelande innehåller det fullständiga meddelande som enheten skickade i egenskapen Body i JSON-format.

För Blob Storage grupperas och exporteras meddelanden en gång per minut. De exporterade filerna använder samma format som de meddelandefiler som exporteras av [IoT Hub](../../iot-hub/tutorial-routing.md) meddelanderoutning till Blob Storage.

> [!NOTE]
> För Blob Storage kontrollerar du att enheterna skickar meddelanden som har `contentType: application/JSON` och `contentEncoding:utf-8` (eller `utf-16` `utf-32` ). Se [IoT Hub-dokumentationen](../../iot-hub/iot-hub-devguide-routing-query-syntax.md#message-routing-query-based-on-message-body) för ett exempel.

Enheten som skickade telemetri representeras av enhets-ID: t (se följande avsnitt). För att hämta namnen på enheterna, exportera enhets data och korrelera varje meddelande med hjälp av **connectionDeviceId** som matchar enhets meddelandets **deviceId** .

I följande exempel visas ett meddelande från en händelsehubben eller Service Bus kö eller ett ämne:

```json
{
  "temp":81.129693132351775,
  "humid":59.488071477541247,
  "EventProcessedUtcTime":"2020-04-07T09:41:15.2877981Z",
  "PartitionId":0,
  "EventEnqueuedUtcTime":"2020-04-07T09:38:32.7380000Z"
}
```

Det här meddelandet innehåller inte enhets-ID: t för den sändande enheten.

Om du vill hämta enhets-ID: t från meddelande data i en Azure Stream Analytics fråga använder du funktionen [GetMetadataPropertyValue](/stream-analytics-query/getmetadatapropertyvalue) . Ett exempel finns i frågan i [utöka Azure IoT Central med anpassade regler med hjälp av Stream Analytics, Azure Functions och SendGrid](./howto-create-custom-rules.md).

Om du vill hämta enhets-ID: t i en Azure Databricks eller Apache Spark arbets yta använder du [systemProperties](https://github.com/Azure/azure-event-hubs-spark/blob/master/docs/structured-streaming-eventhubs-integration.md). Ett exempel finns i arbets ytan Databricks i [utöka Azure IoT Central med anpassad analys med Azure Databricks](./howto-create-custom-analytics.md).

I följande exempel visas en post som exporter ATS till Blob Storage:

```json
{
  "EnqueuedTimeUtc":"2019-09-26T17:46:09.8870000Z",
  "Properties":{

  },
  "SystemProperties":{
    "connectionDeviceId":"<deviceid>",
    "connectionAuthMethod":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}",
    "connectionDeviceGenerationId":"637051167384630591",
    "contentType":"application/json",
    "contentEncoding":"utf-8",
    "enqueuedTime":"2019-09-26T17:46:09.8870000Z"
  },
  "Body":{
    "temp":49.91322758395974,
    "humid":49.61214852573155,
    "pm25":25.87332214661367
  }
}
```

## <a name="devices"></a>Egenskaper

Varje meddelande eller post i en ögonblicks bild representerar en eller flera ändringar av en enhet och dess enhets-och moln egenskaper sedan det senaste exporterade meddelandet. Meddelandet innehåller:

- `id` av enheten i IoT Central
- `displayName` av enheten
- Mall-ID för enhet i `instanceOf`
- `simulated` flagga, sant om enheten är en simulerad enhet
- `provisioned` flagga, sant om enheten har etablerats
- `approved` flagga, sant om enheten har godkänts för att skicka data
- Egenskaps värden
- `properties` inklusive värden för enhets-och moln egenskaper

Borttagna enheter exporteras inte. För närvarande finns det inga indikatorer i exporterade meddelanden för borttagna enheter.

För Event Hubs och Service Bus skickar IoT Central meddelanden som innehåller enhets data till händelsehubben eller Service Bus kö eller ämne i nära real tid.

För Blob Storage exporteras en ny ögonblicks bild som innehåller alla ändringar sedan den senast skrevs en gång per minut.

I följande exempel meddelande visas information om enheter och egenskaper i en Event Hub-eller Service Bus kö eller ett ämne:

```json
{
  "body":{
    "id": "<device Id>",
    "etag": "<etag>",
    "displayName": "Sensor 1",
    "instanceOf": "<device template Id>",
    "simulated": false,
    "provisioned": true,
    "approved": true,
    "properties": {
        "sensorComponent": {
            "setTemp": "30",
            "fwVersion": "2.0.1",
            "status": { "first": "first", "second": "second" },
            "$metadata": {
                "setTemp": {
                    "desiredValue": "30",
                    "desiredVersion": 3,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 3
                },
                "fwVersion": { "ackVersion": 3 },
                "status": {
                    "desiredValue": {
                        "first": "first",
                        "second": "second"
                    },
                    "desiredVersion": 2,
                    "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                    "ackVersion": 2
                }
            },
            
        }
    },
    "installDate": { "installDate": "2020-02-01" }
},
  "annotations":{
    "iotcentral-message-source":"devices",
    "x-opt-partition-key":"<partitionKey>",
    "x-opt-sequence-number":39740,
    "x-opt-offset":"<offset>",
    "x-opt-enqueued-time":1539274959654
  },
  "partitionKey":"<partitionKey>",
  "sequenceNumber":39740,
  "enqueuedTimeUtc":"2020-02-01T18:14:49.3820326Z",
  "offset":"<offset>"
}
```

Den här ögonblicks bilden är ett exempel meddelande som visar enheter och egenskaps data i Blob Storage. Exporterade filer innehåller en enskild rad per post.

```json
{
  "id": "<device Id>",
  "etag": "<etag>",
  "displayName": "Sensor 1",
  "instanceOf": "<device template Id>",
  "simulated": false,
  "provisioned": true,
  "approved": true,
  "properties": {
      "sensorComponent": {
          "setTemp": "30",
          "fwVersion": "2.0.1",
          "status": { "first": "first", "second": "second" },
          "$metadata": {
              "setTemp": {
                  "desiredValue": "30",
                  "desiredVersion": 3,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 3
              },
              "fwVersion": { "ackVersion": 3 },
              "status": {
                  "desiredValue": {
                      "first": "first",
                      "second": "second"
                  },
                  "desiredVersion": 2,
                  "desiredTimestamp": "2020-02-01T17:15:08.9284049Z",
                  "ackVersion": 2
              }
          },
          
      }
  },
  "installDate": { "installDate": "2020-02-01" }
}
```

## <a name="device-templates"></a>Enhetsmallar

Varje meddelande-eller ögonblicks bild post representerar en eller flera ändringar i en publicerad enhets mall sedan det senaste exporterade meddelandet. Information som skickas i varje meddelande eller post innehåller:

- `id` för den enhets mall som matchar enhets `instanceOf` strömmen ovan
- `displayName` av enhets mal len
- Enheten `capabilityModel` , inklusive dess `interfaces` , och definitioner av telemetri, egenskaper och kommandon
- `cloudProperties` definieras
- Åsidosättningar och initiala värden, infogade i `capabilityModel`

Borttagna enhetsspecifika mappar exporteras inte. För närvarande finns det inga indikatorer i exporterade meddelanden för borttagna enhets mallar.

För Event Hubs och Service Bus skickar IoT Central meddelanden som innehåller enhets mal lin data till din Event Hub eller Service Bus kö eller ämne i nära real tid.

För Blob Storage exporteras en ny ögonblicks bild som innehåller alla ändringar sedan den senast skrevs en gång per minut.

Det här exemplet visar ett meddelande om enhets data i händelsehubben eller Service Bus kö eller ämne:

```json
{
  "body":{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "sensorComponent",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorConfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
  },
    "annotations":{
      "iotcentral-message-source":"deviceTemplates",
      "x-opt-partition-key":"<partitionKey>",
      "x-opt-sequence-number":25315,
      "x-opt-offset":"<offset>",
      "x-opt-enqueued-time":1539274985085
    },
    "partitionKey":"<partitionKey>",
    "sequenceNumber":25315,
    "enqueuedTimeUtc":"2019-10-02T16:23:05.085Z",
    "offset":"<offset>"
  }
}
```

Detta exempel på en ögonblicks bild visar ett meddelande som innehåller enhets-och egenskaps data i Blob Storage. Exporterade filer innehåller en enskild rad per post.

```json
{
      "id": "<device template id>",
      "etag": "<etag>",
      "types": ["DeviceModel"],
      "displayName": "Sensor template",
      "capabilityModel": {
          "@id": "<capability model id>",
          "@type": ["CapabilityModel"],
          "contents": [],
          "implements": [
              {
                  "@id": "<component Id>",
                  "@type": ["InterfaceInstance"],
                  "name": "Sensor component",
                  "schema": {
                      "@id": "<interface Id>",
                      "@type": ["Interface"],
                      "displayName": "Sensor interface",
                      "contents": [
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry"],
                              "displayName": "Humidity",
                              "name": "humidity",
                              "schema": "double"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Telemetry", "SemanticType/Event"],
                              "displayName": "Error event",
                              "name": "error",
                              "schema": "integer"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Set temperature",
                              "name": "setTemp",
                              "writable": true,
                              "schema": "integer",
                              "unit": "Units/Temperature/fahrenheit",
                              "initialValue": "30"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Firmware version read only",
                              "name": "fwversion",
                              "schema": "string"
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Property"],
                              "displayName": "Display status",
                              "name": "status",
                              "writable": true,
                              "schema": {
                                  "@id": "urn:testInterface:status:obj:ka8iw8wka:1",
                                  "@type": ["Object"]
                              }
                          },
                          {
                              "@id": "<id>",
                              "@type": ["Command"],
                              "request": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Configuration",
                                  "name": "config",
                                  "schema": "string"
                              },
                              "response": {
                                  "@id": "<id>",
                                  "@type": ["SchemaField"],
                                  "displayName": "Response",
                                  "name": "response",
                                  "schema": "string"
                              },
                              "displayName": "Configure sensor",
                              "name": "sensorconfig"
                          }
                      ]
                  }
              }
          ],
          "displayName": "Sensor capability model"
      },
      "solutionModel": {
          "@id": "<id>",
          "@type": ["SolutionModel"],
          "cloudProperties": [
              {
                  "@id": "<id>",
                  "@type": ["CloudProperty"],
                  "displayName": "Install date",
                  "name": "installDate",
                  "schema": "dateTime",
                  "valueDetail": {
                      "@id": "<id>",
                      "@type": ["ValueDetail/DateTimeValueDetail"]
                  }
              }
          ]
      }
  }
```

## <a name="data-format-change-notice"></a>Ändrings meddelande för data format

> [!Note]
> Data formatet för telemetri påverkas inte av den här ändringen. Endast data strömmar för enheter och enheter påverkas.

Om du har en befintlig data export i ditt för hands versions program där strömmarna *enheter* och *enhets mallar* är aktiverade, uppdaterar du exporten senast **30 juni 2020**. Detta krav gäller för export till Azure Blob Storage, Azure Event Hubs och Azure Service Bus.

Från och med 3 februari 2020 kommer data formatet som beskrivs ovan att visas i alla nya exporter i program med enheter och enhetsspecifika aktiverade. Alla exporter som skapats före det här datumet förblir i det gamla data formatet fram till den 30 juni 2020, då dessa exporter automatiskt migreras till det nya data formatet. Det nya data formatet matchar [enheten](/rest/api/iotcentral/devices/get), [enhets egenskapen](/rest/api/iotcentral/devices/getproperties), [enhetens moln egenskap](/rest/api/iotcentral/devices/getcloudproperties)och [enhets mal len](/rest/api/iotcentral/devicetemplates/get) objekt i IoT Central offentliga API: et.

För **enheter** är viktiga skillnader mellan det gamla data formatet och det nya data formatet:
- `@id` för enheten tas bort `deviceId` byter namn till `id` 
- `provisioned` flagga läggs till för att beskriva enhetens etablerings status
- `approved` flagga har lagts till för att beskriva enhetens godkännande tillstånd
- `properties` inklusive egenskaper för enhet och moln, matchar entiteter i det offentliga API: et

Viktiga skillnader mellan det gamla data formatet och det nya data **formatet är:**

- `@id` för enhets mal len byter namn till `id`
- `@type` för enhets mal len byter namn till `types` , och är nu en matris

### <a name="devices-format-deprecated-as-of-3-february-2020"></a>Enheter (format föråldrat från och med 3 februari 2020)

```json
{
  "@id":"<id-value>",
  "@type":"Device",
  "displayName":"Airbox",
  "data":{
    "$cloudProperties":{
        "Color":"blue"
    },
    "EnvironmentalSensor":{
      "thsensormodel":{
        "reported":{
          "value":"Neque quia et voluptatem veritatis assumenda consequuntur quod.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "pm25sensormodel":{
        "reported":{
          "value":"Aut alias odio.",
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    },
    "urn_azureiot_DeviceManagement_DeviceInformation":{
      "totalStorage":{
        "reported":{
          "value":27900.9730905171,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      },
      "totalMemory":{
        "reported":{
          "value":4667.82916715811,
          "$lastUpdatedTimestamp":"2019-09-30T20:35:43.8478978Z"
        }
      }
    }
  },
  "instanceOf":"<template-id>",
  "deviceId":"<device-id>",
  "simulated":true
}
```

### <a name="device-templates-format-deprecated-as-of-3-february-2020"></a>Enhets mallar (format föråldras från och med 3 februari 2020)

```json
{
  "@id":"<template-id>",
  "@type":"DeviceModelDefinition",
  "displayName":"Airbox",
  "capabilityModel":{
    "@id":"<id>",
    "@type":"CapabilityModel",
    "implements":[
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"EnvironmentalSensor",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "comment":"Requires temperature and humidity sensors.",
          "description":"Provides functionality to report temperature, humidity. Provides telemetry, commands and read-write properties",
          "displayName":"Environmental Sensor",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current temperature on the device",
              "displayName":"Temperature",
              "name":"temp",
              "schema":"double",
              "unit":"Units/Temperature/celsius",
              "valueDetail":{
                "@id":"<id>",
                "@type":"ValueDetail/NumberValueDetail",
                "minValue":{
                  "@value":"50"
                }
              },
              "visualizationDetail":{
                "@id":"<id>",
                "@type":"VisualizationDetail"
              }
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current humidity on the device",
              "displayName":"Humidity",
              "name":"humid",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Telemetry",
              "description":"Current PM2.5 on the device",
              "displayName":"PM2.5",
              "name":"pm25",
              "schema":"integer"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"T&H Sensor Model Name",
              "displayName":"T&H Sensor Model",
              "name":"thsensormodel",
              "schema":"string"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "description":"PM2.5 Sensor Model Name",
              "displayName":"PM2.5 Sensor Model",
              "name":"pm25sensormodel",
              "schema":"string"
            }
          ]
        }
      },
      {
        "@id":"<id>",
        "@type":"InterfaceInstance",
        "name":"urn_azureiot_DeviceManagement_DeviceInformation",
        "schema":{
          "@id":"<id>",
          "@type":"Interface",
          "displayName":"Device information",
          "contents":[
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available storage on the device in kilobytes. Ex. 20480000 kilobytes.",
              "displayName":"Total storage",
              "name":"totalStorage",
              "displayUnit":"kilobytes",
              "schema":"long"
            },
            {
              "@id":"<id>",
              "@type":"Property",
              "comment":"Total available memory on the device in kilobytes. Ex. 256000 kilobytes.",
              "displayName":"Total memory",
              "name":"totalMemory",
              "displayUnit":"kilobytes",
              "schema":"long"
            }
          ]
        }
      }
    ],
    "displayName":"AAEONAirbox52"
  },
  "solutionModel":{
    "@id":"<id>",
    "@type":"SolutionModel",
    "cloudProperties":[
      {
        "@id":"<id>",
        "@type":"CloudProperty",
        "displayName":"Color",
        "name":"Color",
        "schema":"string",
        "valueDetail":{
          "@id":"<id>",
          "@type":"ValueDetail/StringValueDetail"
        },
        "visualizationDetail":{
          "@id":"<id>",
          "@type":"VisualizationDetail"
        }
      }
    ]
  }
}
```

## <a name="next-steps"></a>Nästa steg

Nu när du vet hur du exporterar dina data till Azure Event Hubs, Azure Service Bus och Azure Blob Storage, fortsätter du till nästa steg:

> [!div class="nextstepaction"]
> [Så här kör du Custom Analytics med Databricks](./howto-create-custom-analytics.md)