---
title: Mata in telemetri från IoT Hub
titleSuffix: Azure Digital Twins
description: Se hur du matar in meddelanden för telemetri från IoT Hub.
author: alexkarcher-msft
ms.author: alkarche
ms.date: 8/11/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 47e4bb291d031c41c89c88435a795004490e20a1
ms.sourcegitcommit: 54d8052c09e847a6565ec978f352769e8955aead
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88505333"
---
# <a name="ingest-iot-hub-telemetry-into-azure-digital-twins"></a>Mata in IoT Hub telemetri i Azure Digitals, dubbla

Digitala Azure-enheter drivs med data från IoT-enheter och andra källor. En gemensam källa för enhets data som ska användas i Azure Digitals dubbla är [IoT Hub](../iot-hub/about-iot-hub.md).

Processen för att mata in data i Azure Digitals, är att konfigurera en extern beräknings resurs, till exempel en [Azure-funktion](../azure-functions/functions-overview.md), som tar emot data och använder [DigitalTwins-API: er](how-to-use-apis-sdks.md) för att ange egenskaper eller brand släcknings händelser på [digitala dubbla](concepts-twins-graph.md) . 

Det här dokumentet vägleder dig genom processen för att skriva en Azure-funktion som kan mata in telemetri från IoT Hub.

## <a name="prerequisites"></a>Krav

Innan du fortsätter med det här exemplet måste du konfigurera följande resurser som krav:
* **En IoT-hubb**. Anvisningar finns i avsnittet *skapa en IoT Hub* i [den här IoT Hub snabb](../iot-hub/quickstart-send-telemetry-cli.md)starten.
* **En Azure-funktion** med rätt behörighet för att anropa den digitala dubbla instansen. Instruktioner finns i [*instruktion: Konfigurera en Azure-funktion för bearbetning av data*](how-to-create-azure-function.md). 
* **En digital Azure-instans** som tar emot din enhets telemetri. Anvisningar finns i anvisningar [*: Konfigurera en digital Azure-instans och autentisering*](./how-to-set-up-instance-portal.md).

### <a name="example-telemetry-scenario"></a>Exempel scenario för telemetri

Den här instruktionen beskriver hur du skickar meddelanden från IoT Hub till Azures digitala dubbla, med hjälp av en Azure-funktion. Det finns många möjliga konfigurationer och matchnings strategier som du kan använda för detta, men exemplet för den här artikeln innehåller följande delar:
* En termometer-enhet i IoT Hub, med ett känt enhets-ID.
* En digital enhet som representerar enheten, med ett matchande ID

> [!NOTE]
> I det här exemplet används en enkel ID-matchning mellan enhets-ID: t och motsvarande digitala dubbla ID, men det är möjligt att ge mer sofistikerade mappningar från enheten till dess dubbla (till exempel med en mappnings tabell).

När en händelse för temperatur telemetri skickas av termometer-enheten, bör *temperatur* egenskapen för den digitala enheten uppdateras. Det här scenariot beskrivs i ett diagram nedan:

:::image type="content" source="media/how-to-ingest-iot-hub-data/events.png" alt-text="Ett diagram som visar ett flödes diagram. I diagrammet visas en IoT Hub enhet som skickar temperatur telemetri via IoT Hub till en Azure-funktion som uppdaterar en temperatur egenskap på en enhet i en digital i Azure Digitals." border="false":::

## <a name="add-a-model-and-twin"></a>Lägg till en modell och dubbla

Du behöver en dubbla för att uppdatera med IoT Hub-information.

Modellen ser ut så här:
```JSON
{
  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",
  "@type": "Interface",
  "@context": "dtmi:dtdl:context;2",
  "contents": [
    {
      "@type": "Property",
      "name": "Temperature",
      "schema": "double"
    }
  ]
}
```

Om du vill **överföra den här modellen till din dubbla instansen**öppnar du Azure CLI och kör följande kommando:
```azurecli-interactive
az dt model create --models '{  "@id": "dtmi:contosocom:DigitalTwins:Thermostat;1",  "@type": "Interface",  "@context": "dtmi:dtdl:context;2",  "contents": [    {      "@type": "Property",      "name": "Temperature",      "schema": "double"    }  ]}' -n {digital_twins_instance_name}
```

Sedan måste du **skapa en dubbel med den här modellen**. Använd följande kommando för att skapa en dubbla och ange 0,0 som första temperatur värde.
```azurecli-interactive
az dt twin create --dtmi "dtmi:contosocom:DigitalTwins:Thermostat;1" --twin-id thermostat67 --properties '{"Temperature": 0.0,}' --dt-name {digital_twins_instance_name}
```

Utdata från ett lyckat dubbelt skapande-kommando bör se ut så här:
```json
{
  "$dtId": "thermostat67",
  "$etag": "W/\"0000000-9735-4f41-98d5-90d68e673e15\"",
  "$metadata": {
    "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
    "Temperature": {
      "ackCode": 200,
      "ackDescription": "Auto-Sync",
      "ackVersion": 1,
      "desiredValue": 0.0,
      "desiredVersion": 1
    }
  },
  "Temperature": 0.0
}
```

## <a name="create-an-azure-function"></a>Skapa en Azure-funktion

I det här avsnittet används samma start steg i Visual Studio och Azure Function Skeleton från [*anvisningar: Konfigurera en Azure-funktion för bearbetning av data*](how-to-create-azure-function.md). Skeleton hanterar autentisering och skapar en tjänst klient som är redo att bearbeta data och anropa Azure Digitals dubbla API: er som svar. 

I de steg som följer ska du lägga till en speciell kod i den för att bearbeta IoT telemetri-händelser från IoT Hub.  

### <a name="add-telemetry-processing"></a>Lägg till telemetri-bearbetning
    
Telemetri händelser levereras i form av meddelanden från enheten. Det första steget vid tillägg av telemetri – bearbetnings kod extraherar relevant del av enhets meddelandet från Event Grid-händelsen. 

Olika enheter kan strukturera sina meddelanden på olika sätt, så koden för **det här steget beror på den anslutna enheten.** 

Följande kod visar ett exempel på en enkel enhet som skickar telemetri som JSON. Det här exemplet är helt utforskat i [*Självstudier: Anslut en lösning från slut punkt till slut punkt*](./tutorial-end-to-end.md). Följande kod hittar enhets-ID: t för enheten som skickade meddelandet, samt temperatur svärdet.

```csharp
JObject deviceMessage = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
string deviceId = (string)deviceMessage["systemProperties"]["iothub-connection-device-id"];
var temperature = deviceMessage["body"]["Temperature"];
```

Nästa kod exempel tar med ID och temperatur värde och använder dem i "patch" (gör uppdateringar till) som är dubbla.

```csharp
//Update twin using device temperature
var uou = new UpdateOperationsUtility();
uou.AppendReplaceOp("/Temperature", temperature.Value<double>());
await client.UpdateDigitalTwinAsync(deviceId, uou.Serialize());
...
```

### <a name="update-your-azure-function-code"></a>Uppdatera din Azure Function-kod

Nu när du förstår koden från de tidigare exemplen öppnar du Visual Studio och ersätter din Azure Function-kod med denna exempel kod.

```csharp
using System;
using System.Net.Http;
using Azure.Core.Pipeline;
using Azure.DigitalTwins.Core;
using Azure.DigitalTwins.Core.Serialization;
using Azure.Identity;
using Microsoft.Azure.EventGrid.Models;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.EventGrid;
using Microsoft.Extensions.Logging;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;

namespace IotHubtoTwins
{
    public class IoTHubtoTwins
    {
        private static readonly string adtInstanceUrl = Environment.GetEnvironmentVariable("ADT_SERVICE_URL");
        private static readonly HttpClient httpClient = new HttpClient();

        [FunctionName("IoTHubtoTwins")]
        public async void Run([EventGridTrigger] EventGridEvent eventGridEvent, ILogger log)
        {
            if (adtInstanceUrl == null) log.LogError("Application setting \"ADT_SERVICE_URL\" not set");

            try
            {
                //Authenticate with Digital Twins
                ManagedIdentityCredential cred = new ManagedIdentityCredential("https://digitaltwins.azure.net");
                DigitalTwinsClient client = new DigitalTwinsClient(
                    new Uri(adtInstanceUrl), cred, new DigitalTwinsClientOptions 
                    { Transport = new HttpClientTransport(httpClient) });
                log.LogInformation($"ADT service client connection created.");
            
                if (eventGridEvent != null && eventGridEvent.Data != null)
                {
                    log.LogInformation(eventGridEvent.Data.ToString());

                    // Reading deviceId and temperature for IoT Hub JSON
                    JObject deviceMessage = (JObject)JsonConvert.DeserializeObject(eventGridEvent.Data.ToString());
                    string deviceId = (string)deviceMessage["systemProperties"]["iothub-connection-device-id"];
                    var temperature = deviceMessage["body"]["Temperature"];
                    
                    log.LogInformation($"Device:{deviceId} Temperature is:{temperature}");

                    //Update twin using device temperature
                    var uou = new UpdateOperationsUtility();
                    uou.AppendReplaceOp("/Temperature", temperature.Value<double>());
                    await client.UpdateDigitalTwinAsync(deviceId, uou.Serialize());
                }
            }
            catch (Exception e)
            {
                log.LogError($"Error in ingest function: {e.Message}");
            }
        }
    }
}
```

## <a name="connect-your-function-to-iot-hub"></a>Anslut din funktion till IoT Hub

1. Konfigurera ett händelse mål för Hub-data. I [Azure Portal](https://portal.azure.com/)navigerar du till IoT Hub-instansen. Under **händelser**skapar du en prenumeration för Azure-funktionen. 

    :::image type="content" source="media/how-to-ingest-iot-hub-data/add-event-subscription.png" alt-text="Skärm bild av Azure Portal som visar hur du lägger till en händelse prenumeration.":::

2. På sidan **Skapa händelse prenumeration** fyller du i fälten enligt följande:
    1. Namn på den prenumeration du vill ha under **namn**.
    2. Under **händelse schema**väljer du **Event Grid schema**.
    3. Under **namn på system ämne**väljer du ett unikt namn.
    4. Under **händelse typer**väljer du **enhets telemetri** som händelse typ för att filtrera till.
    5. Under **slut punkts information**väljer du din Azure-funktion som en slut punkt.

    :::image type="content" source="media/how-to-ingest-iot-hub-data/event-subscription-2.png" alt-text="Skärm bild av Azure Portal som visar information om händelse prenumerationen":::

## <a name="send-simulated-iot-data"></a>Skicka simulerade IoT-data

Testa din nya ingångs funktion genom att använda enhets simulatorn från [*självstudien: Anslut en lösning från slut punkt till slut punkt*](./tutorial-end-to-end.md). Den själv studie kursen drivs av ett exempel projekt som skrivits i C#. Exempel koden finns här: [Azure Digitals dubbla exempel](https://docs.microsoft.com/samples/azure-samples/digital-twins-samples/digital-twins-samples). Du kommer att använda **DeviceSimulator** -projektet på den lagrings platsen.

I självstudierna från slut punkt till slut punkt utför du följande steg:
1. [*Registrera den simulerade enheten med IoT Hub*](./tutorial-end-to-end.md#register-the-simulated-device-with-iot-hub)
2. [*Konfigurera och kör simuleringen*](./tutorial-end-to-end.md#configure-and-run-the-simulation)

## <a name="validate-your-results"></a>Verifiera dina resultat

När du kör enhets simulatorn ovan ändras temperatur värdet för din digitala garn. I Azure CLI kör du följande kommando för att visa temperatur svärdet.

```azurecli-interactive
az dt twin query -q "select * from digitaltwins" -n {digital_twins_instance_name}
```

Dina utdata bör innehålla ett temperatur värde som detta:

```json
{
  "result": [
    {
      "$dtId": "thermostat67",
      "$etag": "W/\"0000000-1e83-4f7f-b448-524371f64691\"",
      "$metadata": {
        "$model": "dtmi:contosocom:DigitalTwins:Thermostat;1",
        "Temperature": {
          "ackCode": 200,
          "ackDescription": "Auto-Sync",
          "ackVersion": 1,
          "desiredValue": 69.75806974934324,
          "desiredVersion": 1
        }
      },
      "Temperature": 69.75806974934324
    }
  ]
}
```

Om du vill se värde ändringen kör du kommandot query upprepade gånger.

## <a name="next-steps"></a>Nästa steg

Läs om data ingress och utgånget med Azure Digitals:
* [*Koncept: integrering med andra tjänster*](concepts-integration.md)
