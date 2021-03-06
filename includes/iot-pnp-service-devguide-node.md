---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 10/20/2020
ms.openlocfilehash: dfeeb451912dd32770a6ae92d73de83851d9d8f6
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102244828"
---
Följande resurser är också tillgängliga:

- [ Dokumentation omNode.js SDK-referens](/javascript/api/azure-iothub)
- [Tjänst klient exempel](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/twin.js)
- [Digitala dubbla exempel](https://github.com/Azure/azure-iot-sdk-node/blob/master/service/samples/javascript/get_digital_twin.js)

## <a name="iot-hub-service-client-examples"></a>Exempel på IoT Hub tjänst klient

Det här avsnittet visar JavaScript-exempel som använder IoT Hub tjänst klienten och **register** -och **klient** klasser. Du kan använda **register** klassen för att interagera med enhetens tillstånd med hjälp av enhets dubbla. Du kan också använda **register** klassen för att [fråga enhets registreringar](../articles/iot-hub/iot-hub-devguide-query-language.md) i din IoT Hub. Du använder **klient** klassen för att anropa kommandon på enheten. [DTDL](../articles/iot-pnp/concepts-digital-twin.md) -modellen för enheten definierar de egenskaper och kommandon som enheten implementerar. I kodfragmenten `deviceId` innehåller variabeln enhets-ID för iot plug and Play-enheten som registrerats med IoT Hub.

### <a name="get-the-device-twin-and-model-id"></a>Hämta enhetens dubbla och modell-ID

För att hämta enhetens dubbla och modell-ID för IoT Plug and Play-enheten som är ansluten till din IoT-hubb:

```javascript
var Registry = require('azure-iothub').Registry;

// ...

var registry = Registry.fromConnectionString(connectionString);
registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    console.log('Model Id: ' + twin.modelId);
    console.log(JSON.stringify(twin, null, 2));
  }
}
```

### <a name="update-device-twin"></a>Uppdatera enhet, dubbla

Följande kodfragment visar hur du uppdaterar `targetTemperature` egenskapen på en enhet. Exemplet visar hur du måste hämta den dubbla innan du uppdaterar den. Egenskapen definieras i standard komponenten för enheten:

```javascript
var Registry = require('azure-iothub').Registry;
var registry = Registry.fromConnectionString(connectionString);

registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    var twinPatch = {
      properties: {
        desired: {
          targetTemperature: 42
        }
      }
    };
    twin.update(twinPatch, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log(JSON.stringify(twin, null, 2));
      }
    }
  }
}
```

Följande fragment visar hur du uppdaterar `targetTemperature` egenskapen för en komponent. Exemplet visar hur du måste hämta den dubbla innan du uppdaterar den. Egenskapen definieras i **thermostat1** -komponenten:

```javascript
var Registry = require('azure-iothub').Registry;
var registry = Registry.fromConnectionString(connectionString);

registry.getTwin(deviceId, function(err, twin) {
  if (err) {
    console.error(err.message);
  } else {
    var twinPatch = {
      properties: {
        desired: {
          thermostat1:
          {
            __t: "c",
            targetTemperature: 45
          }
        }
      }
    };
    twin.update(twinPatch, function(err, twin) {
      if (err) {
        console.error(err.message);
      } else {
        console.log(JSON.stringify(twin, null, 2));
      }
    }
  }
}
```

För en egenskap i en komponent ser egenskaps korrigeringen ut som i följande exempel:

```json
{
  "thermostat1":
  {
    "__t": "c",
    "targetTemperature": 20
  }
}
```

### <a name="call-command"></a>Anrops kommando

Följande fragment visar hur du anropar `getMaxMinReport` kommandot som definierats i en standard komponent:

```javascript
var Client = require('azure-iothub').Client;

// ...

var client = Client.fromConnectionString(connectionString);

var methodParams = {
  methodName: "getMaxMinReport",
  payload: new Date().getMinutes -2,
  responseTimeoutInSeconds: 15
};

client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
  if (err) {
    console.error('Failed to invoke method \'' + methodParams.methodName + '\': ' + err.message);
  } else {
    console.log(methodParams.methodName + ' on ' + deviceId + ':');
    console.log(JSON.stringify(result, null, 2));
  }
});
```

Följande fragment visar hur du anropar `getMaxMinReport` kommandot på en komponent. Kommandot definieras i **thermostat1** -komponenten:

```javascript
var Client = require('azure-iothub').Client;

// ...

var client = Client.fromConnectionString(connectionString);

var methodParams = {
  methodName: "thermostat1*getMaxMinReport",
  payload: new Date().getMinutes -2,
  responseTimeoutInSeconds: 15
};

client.invokeDeviceMethod(deviceId, methodParams, function (err, result) {
  if (err) {
    console.error('Failed to invoke method \'' + methodParams.methodName + '\': ' + err.message);
  } else {
    console.log(methodParams.methodName + ' on ' + deviceId + ':');
    console.log(JSON.stringify(result, null, 2));
  }
});
```

## <a name="iot-hub-digital-twin-examples"></a>IoT Hub digitala, dubbla exempel

Du använder klassen **DigitalTwinClient** för att interagera med enhetens tillstånd med hjälp av digitala dubbla. [DTDL](../articles/iot-pnp/concepts-digital-twin.md) -modellen för enheten definierar de egenskaper och kommandon som enheten implementerar.

I det här avsnittet visas JavaScript-exempel med hjälp av digitala dubbla API: er.

`digitalTwinId`Variabeln innehåller enhets-ID: t för iot plug and Play-enheten registrerad i IoT Hub.

### <a name="get-the-digital-twin-and-model-id"></a>Hämta det digitala dubbla och modell-ID: t

För att hämta det digitala dubbla och modell-ID: t för IoT Plug and Play-enheten som är ansluten till din IoT-hubb:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const digitalTwin = await digitalTwinClient.getDigitalTwin(digitalTwinId);

console.log(inspect(digitalTwin));
console.log('Model Id: ' + inspect(digitalTwin.$metadata.$model));
```

### <a name="update-digital-twin"></a>Uppdatera Digital, delad

Följande kodfragment visar hur du uppdaterar `targetTemperature` egenskapen på en enhet. Egenskapen definieras i standard komponenten för enheten:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;

// ...

const credentials = new IoTHubTokenCredentials(connString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const patch = [{
  op: 'add',
  path: '/targetTemperature',
  value: 42
}];
await digitalTwinClient.updateDigitalTwin(digitalTwinId, patch);
```

Följande fragment visar hur du uppdaterar `targetTemperature` egenskapen för en komponent. Egenskapen definieras i **thermostat1** -komponenten:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;

// ...

const credentials = new IoTHubTokenCredentials(connString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const patch = [{
  op: 'add',
  path: '/thermostat1/targetTemperature',
  value: 42
}];
await digitalTwinClient.updateDigitalTwin(digitalTwinId, patch);
```

### <a name="call-command"></a>Anrops kommando

Följande fragment visar hur du anropar `getMaxMinReport` kommandot som definierats i en standard komponent:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const commandPayload = new Date().getMinutes -2;

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const options = {
  connectTimeoutInSeconds: 30,
  responseTimeoutInSeconds: 40
};
const commandResponse = await digitalTwinClient.invokeCommand(digitalTwinId, "getMaxMinReport", commandPayload, options);

console.log(inspect(commandResponse));
```

Följande fragment visar hur du anropar `getMaxMinReport` kommandot på en komponent. Kommandot definieras i **thermostat1** -komponenten:

```javascript
const IoTHubTokenCredentials = require('azure-iothub').IoTHubTokenCredentials;
const DigitalTwinClient = require('azure-iothub').DigitalTwinClient;
const { inspect } = require('util');

// ...

const commandPayload = new Date().getMinutes -2;

const credentials = new IoTHubTokenCredentials(connectionString);
const digitalTwinClient = new DigitalTwinClient(credentials);

const options = {
  connectTimeoutInSeconds: 30,
  responseTimeoutInSeconds: 40
};
const commandResponse = await digitalTwinClient.invokeComponentCommand(digitalTwinId, "thermostat1", "getMaxMinReport", commandPayload, options);

console.log(inspect(commandResponse));
```

## <a name="read-device-telemetry"></a>Läsa telemetri för enhet

IoT Plug and Play-enheter skickar telemetri som definierats i DTDL-modellen till IoT Hub. Som standard dirigerar IoT Hub telemetri till en Event Hubs-slutpunkt där du kan använda den. Läs mer i [använda IoT Hub meddelanderoutning för att skicka meddelanden från enheten till molnet till olika slut punkter](../articles/iot-hub/iot-hub-devguide-messages-d2c.md).

Följande kodfragment visar hur du läser Telemetrin från standard slut punkten för Event Hubs. Koden i det här kodfragmentet hämtas från IoT Hub snabb start [Skicka telemetri från en enhet till en IoT-hubb och läsa den med ett Server dels program](../articles/iot-hub/quickstart-send-telemetry-node.md):

```javascript
const { EventHubConsumerClient } = require("@azure/event-hubs");

var printError = function (err) {
  console.log(err.message);
};

var printMessages = function (messages) {
  for (const message of messages) {
    console.log("Telemetry received: ");
    console.log(JSON.stringify(message.body));
    console.log("Properties (set by device): ");
    console.log(JSON.stringify(message.properties));
    console.log("System properties (set by IoT Hub): ");
    console.log(JSON.stringify(message.systemProperties));
    console.log("");
  }
};

// ...

const clientOptions = {};

const consumerClient = new EventHubConsumerClient("$Default", connectionString, clientOptions);

consumerClient.subscribe({
  processEvents: printMessages,
  processError: printError,
});
```

Följande utdata från föregående kod visar de temperatur-telemetri som skickas av **TemperatureController** IoT plug and Play-enheten med flera komponenter. `dt-subject`Egenskapen system visar namnet på komponenten som skickade telemetri. I det här exemplet är de två komponenterna `thermostat1` och `thermostat2` enligt definitionen i DTDL-modellen. `dt-dataschema`Egenskapen system visar modell-ID:

```cmd/sh
Telemetry received:
{"temperature":68.77370855171125}
Properties (set by device):
undefined
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-connection-auth-method":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","iothub-connection-auth-generation-id":"637388034455888246","iothub-enqueuedtime":1603206669320,"iothub-message-source":"Telemetry","dt-subject":"thermostat1","dt-dataschema":"dtmi:com:example:TemperatureController;1","contentType":"application/json","contentEncoding":"utf-8"}

Telemetry received:
{"temperature":30.833394506549226}
Properties (set by device):
undefined
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-connection-auth-method":"{\"scope\":\"device\",\"type\":\"sas\",\"issuer\":\"iothub\",\"acceptingIpFilterRule\":null}","iothub-connection-auth-generation-id":"637388034455888246","iothub-enqueuedtime":1603206665835,"iothub-message-source":"Telemetry","dt-subject":"thermostat2","dt-dataschema":"dtmi:com:example:TemperatureController;1","contentType":"application/json","contentEncoding":"utf-8"}
```

## <a name="read-device-twin-change-notifications"></a>Läs enhets meddelanden med dubbla ändringar

Du kan konfigurera IoT Hub att generera enhets dubbla ändrings meddelanden som ska vidarebefordras till en slut punkt som stöds. Läs mer i [använda IoT Hub meddelanderoutning för att skicka meddelanden från enheten till molnet till olika slut punkter > händelser som inte är telemetri](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Den kod som visas i föregående JavaScript-kodfragment genererar följande utdata när IoT Hub genererar enhets dubbla ändrings meddelanden för en termostat-enhet utan komponent. Program egenskaperna `iothub-message-schema` och `opType` ger dig information om typen av ändrings meddelande:

```cmd/sh
Telemetry received:
{"version":4,"properties":{"reported":{"maxTempSinceLastReboot":42.1415152639582,"$metadata":{"$lastUpdated":"2020-10-21T10:01:40.1281138Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T10:01:40.1281138Z"}},"$version":3}}}
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:01:40.1281138+00:00","iothub-message-schema":"twinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274500282,"iothub-message-source":"twinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ed82d13f50","contentType":"application/json","contentEncoding":"utf-8"}
```

Den kod som visas i föregående JavaScript-kodfragment genererar följande utdata när IoT Hub genererar enhets dubbla ändrings meddelanden för en enhet med komponenter. I det här exemplet visas utdata när en temperatur sensor enhet med en termostat-komponent genererar meddelanden. Program egenskaperna `iothub-message-schema` och `opType` ger dig information om typen av ändrings meddelande:

```cmd/sh
Telemetry received:
{"version":4,"properties":{"reported":{"thermostat1":{"maxTempSinceLastReboot":3.5592971602417913,"__t":"c"},"$metadata":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z","thermostat1":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z","maxTempSinceLastReboot":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z"},"__t":{"$lastUpdated":"2020-10-21T10:07:51.8284866Z"}}},"$version":3}}}
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:07:51.8284866+00:00","iothub-message-schema":"twinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274871951,"iothub-message-source":"twinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ee605b195f","contentType":"application/json","contentEncoding":"utf-8"}
```

## <a name="read-digital-twin-change-notifications"></a>Läs digitala dubbla ändrings meddelanden

Du kan konfigurera IoT Hub att generera digitala dubbla ändrings meddelanden som ska vidarebefordras till en slut punkt som stöds. Läs mer i [använda IoT Hub meddelanderoutning för att skicka meddelanden från enheten till molnet till olika slut punkter > händelser som inte är telemetri](../articles/iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events).

Den kod som visas i föregående JavaScript-kodfragment genererar följande utdata när IoT Hub genererar digitala dubbla ändrings meddelanden för en termostat-enhet utan komponent. Program egenskaperna `iothub-message-schema` och `opType` ger dig information om typen av ändrings meddelande:

```cmd/sh
Telemetry received:
[{"op":"add","path":"/$metadata/maxTempSinceLastReboot","value":{"lastUpdateTime":"2020-10-21T10:01:40.1281138Z"}},{"op":"add","path":"/maxTempSinceLastReboot","value":42.1415152639582}]
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:01:40.1281138+00:00","iothub-message-schema":"digitalTwinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274500282,"iothub-message-source":"digitalTwinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ed82d13f50","contentType":"application/json-patch+json","contentEncoding":"utf-8"}
```

Den kod som visas i föregående JavaScript-kodfragment genererar följande utdata när IoT Hub genererar digitala dubbla ändrings meddelanden för en enhet med komponenter. I det här exemplet visas utdata när en temperatur sensor enhet med en termostat-komponent genererar meddelanden. Program egenskaperna `iothub-message-schema` och `opType` ger dig information om typen av ändrings meddelande:

```cmd/sh
Telemetry received:
[{"op":"add","path":"/thermostat1","value":{"$metadata":{"maxTempSinceLastReboot":{"lastUpdateTime":"2020-10-21T10:07:51.8284866Z"}},"maxTempSinceLastReboot":3.5592971602417913}}]
Properties (set by device):
{"hubName":"my-pnp-hub","deviceId":"my-pnp-device","operationTimestamp":"2020-10-21T10:07:51.8284866+00:00","iothub-message-schema":"digitalTwinChangeNotification","opType":"updateTwin"}
System properties (set by IoT Hub):
{"iothub-connection-device-id":"my-pnp-device","iothub-enqueuedtime":1603274871951,"iothub-message-source":"digitalTwinChangeEvents","userId":{"type":"Buffer","data":[109,121,45,112,110,112,45,104,117,98]},"correlationId":"11ee605b195f","contentType":"application/json-patch+json","contentEncoding":"utf-8"}
```
