---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/19/2020
ms.openlocfilehash: f731e07de4936bc8de396c4f9bcd3a52913cb502
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99616589"
---
## <a name="model-id-announcement"></a>Meddelande om modell-ID

För att meddela modell-ID måste enheten inkludera den i anslutnings informationen:

```csharp
DeviceClient.CreateFromConnectionString(
  connectionString,
  TransportType.Mqtt,
  new ClientOptions() { ModelId = modelId })
```

Den nya `ClientOptions` överlagringen är tillgänglig i alla `DeviceClient` metoder som används för att initiera en anslutning.

> [!TIP]
> För moduler och IoT Edge använder du `ModuleClient` i stället för `DeviceClient` .

> [!TIP]
> Det här är den enda tid som en enhet kan ställa in modell-ID: t kan inte uppdateras efter att enheten har anslutits.

## <a name="dps-payload"></a>DPS-nyttolast

Enheter som använder [enhets etablerings tjänsten (DPS)](../articles/iot-dps/about-iot-dps.md) kan inkludera den `modelId` som ska användas under etablerings processen med hjälp av följande JSON-nyttolast.

```json
{
    "modelId" : "dtmi:com:example:Thermostat;1"
}
```

## <a name="implement-telemetry-properties-and-commands"></a>Implementera telemetri, egenskaper och kommandon

Som beskrivs i [förstå komponenter i IoT plug and Play-modeller](../articles/iot-pnp/concepts-components.md)måste enhets byggare bestämma om de vill använda komponenter för att beskriva sina enheter. När du använder komponenter måste enheterna följa reglerna som beskrivs i det här avsnittet.

### <a name="telemetry"></a>Telemetri

En standard komponent kräver ingen särskild egenskap.

När du använder kapslade komponenter måste enheterna ange en meddelande egenskap med namnet på komponenten:

```c#
public async Task SendComponentTelemetryValueAsync(string componentName, string serializedTelemetry)
{
  var message = new Message(Encoding.UTF8.GetBytes(serializedTelemetry));
  message.ComponentName = componentName;
  message.ContentType = "application/json";
  message.ContentEncoding = "utf-8";
  await client.SendEventAsync(message);
}
```

### <a name="read-only-properties"></a>Skrivskyddade egenskaper

Rapportering av en egenskap från standard komponenten kräver ingen särskild konstruktion:

```csharp
TwinCollection reportedProperties = new TwinCollection();
reportedProperties["maxTemperature"] = 38.7;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Enheten "delad" uppdateras med nästa rapporterade egenskap:

```json
{
  "reported": {
      "maxTemperature" : 38.7
  }
}
```

När du använder kapslade komponenter måste du skapa egenskaper i komponent namnet:

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
component["maxTemperature"] = 38.7;
component["__t"] = "c"; // marker to identify a component
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Enheten "delad" uppdateras med nästa rapporterade egenskap:

```json
{
  "reported": {
    "thermostat1" : {  
      "__t" : "c",  
      "maxTemperature" : 38.7
     } 
  }
}
```

### <a name="writable-properties"></a>Skrivbara egenskaper

Dessa egenskaper kan ställas in av enheten eller uppdateras av lösningen. Om lösningen uppdaterar en egenskap får klienten ett meddelande som ett återanrop i `DeviceClient` eller `ModuleClient` . För att följa IoT Plug and Play-konventionerna måste enheten meddela tjänsten att egenskapen har tagits emot.

#### <a name="report-a-writable-property"></a>Rapportera en skrivbar egenskap

När en enhet rapporterar en skrivbar egenskap måste den innehålla de `ack` värden som definieras i konventionerna.

Så här rapporterar du en skrivbar egenskap från standard komponenten:

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not readed from a desired property
ackProps["ad"] = "reported default value";
reportedProperties["targetTemperature"] = ackProps;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Enheten "delad" uppdateras med nästa rapporterade egenskap:

```json
{
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

Om du vill rapportera en skrivbar egenskap från en kapslad komponent måste den dubbla innehålla en markör:

```csharp
TwinCollection reportedProperties = new TwinCollection();
TwinCollection component = new TwinCollection();
TwinCollection ackProps = new TwinCollection();
component["__t"] = "c"; // marker to identify a component
ackProps["value"] = 23.2;
ackProps["ac"] = 200; // using HTTP status codes
ackProps["av"] = 0; // not read from a desired property
ackProps["ad"] = "reported default value";
component["targetTemperature"] = ackProps;
reportedProperties["thermostat1"] = component;
await client.UpdateReportedPropertiesAsync(reportedProperties);
```

Enheten "delad" uppdateras med nästa rapporterade egenskap:

```json
{
  "reported": {
    "thermostat1": {
      "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

#### <a name="subscribe-to-desired-property-updates"></a>Prenumerera på önskade egenskaps uppdateringar

Tjänster kan uppdatera önskade egenskaper som utlöser ett meddelande på de anslutna enheterna. Det här meddelandet innehåller uppdaterade önskade egenskaper, inklusive versions numret som identifierar uppdateringen. Enheter måste svara med samma `ack` meddelande som rapporterade egenskaper.

En standard komponent ser den enskilda egenskapen och skapar rapporterad `ack` med den mottagna versionen:

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) => 
{
  JValue targetTempJson = desired["targetTemperature"];
  double targetTemperature = targetTempJson.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200;
  ackProps["av"] = desired.Version; 
  ackProps["ad"] = "desired property received";
  reportedProperties["targetTemperature"] = ackProps;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
```

Enheten, som visar egenskapen i de önskade och rapporterade avsnitten:

```json
{
  "desired" : {
    "targetTemperature": 23.2,
    "$version" : 3
  },
  "reported": {
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
  }
}
```

En kapslad komponent tar emot önskade egenskaper omslutna med komponent namnet och bör rapportera tillbaka den `ack` rapporterade egenskapen:

```csharp
await client.SetDesiredPropertyUpdateCallbackAsync(async (desired, ctx) =>
{
  JObject thermostatComponent = desired["thermostat1"];
  JToken targetTempProp = thermostatComponent["targetTemperature"];
  double targetTemperature = targetTempProp.Value<double>();

  TwinCollection reportedProperties = new TwinCollection();
  TwinCollection component = new TwinCollection();
  TwinCollection ackProps = new TwinCollection();
  component["__t"] = "c"; // marker to identify a component
  ackProps["value"] = targetTemperature;
  ackProps["ac"] = 200; // using HTTP status codes
  ackProps["av"] = desired.Version; // not readed from a desired property
  ackProps["ad"] = "desired property received";
  component["targetTemperature"] = ackProps;
  reportedProperties["thermostat1"] = component;

  await client.UpdateReportedPropertiesAsync(reportedProperties);
}, null);
```

Enheten för en kapslad komponent visar de önskade och rapporterade avsnitten enligt följande:

```json
{
  "desired" : {
    "thermostat1" : {
        "__t" : "c",
        "targetTemperature": 23.2,
    }
    "$version" : 3
  },
  "reported": {
    "thermostat1" : {
        "__t" : "c",
      "targetTemperature": {
          "value": 23.2,
          "ac": 200,
          "av": 3,
          "ad": "complete"
      }
    }
  }
}
```

### <a name="commands"></a>Kommandon

En standard komponent får kommando namnet som det anropades av tjänsten.

En kapslad komponent får det kommando namn som föregås av komponent namnet och `*` avgränsaren.

```csharp
await client.SetMethodHandlerAsync("themostat*reboot", (MethodRequest req, object ctx) =>
{
  Console.WriteLine("REBOOT");
  return Task.FromResult(new MethodResponse(200));
},
null);
```

#### <a name="request-and-response-payloads"></a>Nytto laster för begäran och svar

Kommandon använder typer för att definiera sina nytto laster för begäran och svar. En enhet måste deserialisera inkommande indataparameter och serialisera svaret. I följande exempel visas hur du implementerar ett kommando med komplexa typer som definieras i nytto lasterna:

```json
{
  "@type": "Command",
  "name": "start",
  "request": {
    "name": "startRequest",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "startPriority",
          "schema": "integer"
        },
        {
          "name": "startMessage",
          "schema" : "string"
        }
      ]
    }
  },
  "response": {
    "name": "startReponse",
    "schema": {
      "@type": "Object",
      "fields": [
        {
            "name": "startupTime",
            "schema": "integer" 
        },
        {
          "name": "startupMessage",
          "schema": "string"
        }
      ]
    }
  }
}
```

Följande kodfragment visar hur en enhet implementerar den här kommando definitionen, inklusive de typer som används för att aktivera serialisering och deserialisering:

```csharp
class startRequest
{
  public int startPriority { get; set; }
  public string startMessage { get; set; }
}

class startResponse
{
  public int startupTime { get; set; }
  public string startupMessage { get; set; }
}

// ... 

await client.SetMethodHandlerAsync("start", (MethodRequest req, object ctx) =>
{
  var startRequest = JsonConvert.DeserializeObject<startRequest>(req.DataAsJson);
  Console.WriteLine($"Received start command with priority ${startRequest.startPriority} and ${startRequest.startMessage}");

  var startResponse = new startResponse
  {
    startupTime = 123,
    startupMessage = "device started with message " + startRequest.startMessage
  };

  string responsePayload = JsonConvert.SerializeObject(startResponse);
  MethodResponse response = new MethodResponse(Encoding.UTF8.GetBytes(responsePayload), 200);
  return Task.FromResult(response);
},null);
```

> [!Tip]
> Namnen på begäran och svaren finns inte i de serialiserade nytto lasterna som överförs via kabeln.
