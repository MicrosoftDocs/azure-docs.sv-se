---
author: dominicbetts
ms.author: dobett
ms.service: iot-pnp
ms.topic: include
ms.date: 11/19/2020
ms.openlocfilehash: 0aa13d8d23f4f18004131a25f8eb42388d79834b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582809"
---
## <a name="model-id-announcement"></a>Meddelande om modell-ID

För att meddela modell-ID måste enheten inkludera den i anslutnings informationen:

```nodejs
const modelIdObject = { modelId: 'dtmi:com:example:Thermostat;1' };
const client = Client.fromConnectionString(deviceConnectionString, Protocol);
await client.setOptions(modelIdObject);
await client.open();
```

> [!TIP]
> För moduler och IoT Edge använder du `ModuleClient` i stället för `Client` .

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

Som beskrivs i [förstå komponenter i IoT plug and Play-modeller](../articles/iot-pnp/concepts-modeling-guide.md)måste enhets byggare bestämma om de vill använda komponenter för att beskriva sina enheter. När du använder komponenter måste enheterna följa reglerna som beskrivs i det här avsnittet.

### <a name="telemetry"></a>Telemetri

En standard komponent kräver ingen särskild egenskap.

När du använder kapslade komponenter måste enheterna ange en meddelande egenskap med namnet på komponenten:

```nodejs
async function sendTelemetry(deviceClient, data, index, componentName) {
  const msg = new Message(data);
  if (!!(componentName)) {
    msg.properties.add(messageSubjectProperty, componentName);
  }
  msg.contentType = 'application/json';
  msg.contentEncoding = 'utf-8';
  await deviceClient.sendEvent(msg);
}
```

### <a name="read-only-properties"></a>Skrivskyddade egenskaper

Rapportering av en egenskap från standard komponenten kräver ingen särskild konstruktion:

```nodejs
const createReportPropPatch = (propertiesToReport) => {
  let patch;
  patch = { };
  patch = propertiesToReport;
  return patch;
};

deviceTwin = await client.getTwin();
patchThermostat = createReportPropPatch({
  maxTempSinceLastReboot: 38.7
});

deviceTwin.properties.reported.update(patchThermostat, function (err) {
  if (err) throw err;
});
```

Enheten "delad" uppdateras med nästa rapporterade egenskap:

```json
{
  "reported": {
      "maxTempSinceLastReboot" : 38.7
  }
}
```

När du använder kapslade komponenter måste du skapa egenskaper i komponent namnet.:

```nodejs
helperCreateReportedPropertiesPatch = (propertiesToReport, componentName) => {
  let patch;
  if (!!(componentName)) {
    patch = { };
    propertiesToReport.__t = 'c';
    patch[componentName] = propertiesToReport;
  } else {
    patch = { };
    patch = propertiesToReport;
  }
  return patch;
};

deviceTwin = await client.getTwin();
patchThermostat1Info = helperCreateReportedPropertiesPatch({
  maxTempSinceLastReboot: 38.7,
}, 'thermostat1');

deviceTwin.properties.reported.update(patchThermostat1Info, function (err) {
  if (err) throw err;
});
```

Enheten "delad" uppdateras med nästa rapporterade egenskap:

```json
{
  "reported": {
    "thermostat1" : {  
      "__t" : "c",  
      "maxTempSinceLastReboot" : 38.7
     } 
  }
}
```

### <a name="writable-properties"></a>Skrivbara egenskaper

Dessa egenskaper kan ställas in av enheten eller uppdateras av lösningen. Om lösningen uppdaterar en egenskap får klienten ett meddelande som ett återanrop i `Client` eller `ModuleClient` . För att följa IoT Plug and Play-konventionerna måste enheten meddela tjänsten att egenskapen har tagits emot.

#### <a name="report-a-writable-property"></a>Rapportera en skrivbar egenskap

När en enhet rapporterar en skrivbar egenskap måste den innehålla de `ack` värden som definieras i konventionerna.

Så här rapporterar du en skrivbar egenskap från standard komponenten:

```nodejs
patch = {
  targetTemperature:
    {
      'value': 23.2,
      'ac': 200,  // using HTTP status codes
      'ad': 'reported default value',
      'av': 0  // not read from a desired property
    }
};
deviceTwin.properties.reported.update(patch, function (err) {
  if (err) throw err;
});
```

Enheten "delad" uppdateras med nästa rapporterade egenskap:

```json
{
  "reported": {
    "targetTemperature": {
      "value": 23.2,
      "ac": 200,
      "av": 0,
      "ad": "reported default value"
    }
  }
}
```

Om du vill rapportera en skrivbar egenskap från en kapslad komponent måste den dubbla innehålla en markör:

```nodejs
patch = {
  thermostat1: {
    '__t' : 'c',
    targetTemperature: {
      'value': 23.2,
      'ac': 200,  // using HTTP status codes
      'ad': 'reported default value',
      'av': 0  // not read from a desired property
    }
  }
};
deviceTwin.properties.reported.update(patch, function (err) {
  if (err) throw err;
});
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
          "av": 0,
          "ad": "complete"
      }
    }
  }
}
```

#### <a name="subscribe-to-desired-property-updates"></a>Prenumerera på önskade egenskaps uppdateringar

Tjänster kan uppdatera önskade egenskaper som utlöser ett meddelande på de anslutna enheterna. Det här meddelandet innehåller uppdaterade önskade egenskaper, inklusive versions numret som identifierar uppdateringen. Enheter måste svara med samma `ack` meddelande som rapporterade egenskaper.

En standard komponent ser den enskilda egenskapen och skapar rapporterad `ack` med den mottagna versionen:

```nodejs
const propertyUpdateHandler = (deviceTwin, propertyName, reportedValue, desiredValue, version) => {
  const patch = createReportPropPatch(
    { [propertyName]:
      {
        'value': desiredValue,
        'ac': 200,
        'ad': 'Successfully executed patch for ' + propertyName,
        'av': version
      }
    });
  updateComponentReportedProperties(deviceTwin, patch);
};

desiredPropertyPatchHandler = (deviceTwin) => {
  deviceTwin.on('properties.desired', (delta) => {
    const versionProperty = delta.$version;

    Object.entries(delta).forEach(([propertyName, propertyValue]) => {
      if (propertyName !== '$version') {
        propertyUpdateHandler(deviceTwin, propertyName, null, propertyValue, versionProperty);
      }
    });
  });
};
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

```nodejs
const desiredPropertyPatchListener = (deviceTwin, componentNames) => {
  deviceTwin.on('properties.desired', (delta) => {
    Object.entries(delta).forEach(([key, values]) => {
      const version = delta.$version;
      if (!!(componentNames) && componentNames.includes(key)) { // then it is a component we are expecting
        const componentName = key;
        const patchForComponents = { [componentName]: {} };
        Object.entries(values).forEach(([propertyName, propertyValue]) => {
          if (propertyName !== '__t' && propertyName !== '$version') {
            const propertyContent = { value: propertyValue };
            propertyContent.ac = 200;
            propertyContent.ad = 'Successfully executed patch';
            propertyContent.av = version;
            patchForComponents[componentName][propertyName] = propertyContent;
          }
        });
        updateComponentReportedProperties(deviceTwin, patchForComponents, componentName);
      }
      else if  (key !== '$version') { // individual property for root
        const patchForRoot = { };
        const propertyContent = { value: values };
        propertyContent.ac = 200;
        propertyContent.ad = 'Successfully executed patch';
        propertyContent.av = version;
        patchForRoot[key] = propertyContent;
        updateComponentReportedProperties(deviceTwin, patchForRoot, null);
      }
    });
  });
};
```

Enhetens dubbla för komponenter visar de önskade och rapporterade avsnitten enligt följande:

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

```nodejs
const commandHandler = async (request, response) => {
  switch (request.methodName) {
  
  // ...

  case 'thermostat1*reboot': {
    await response.send(200, 'reboot response');
    break;
  }
  default:
    await response.send(404, 'unknown method');
    break;
  }
};

client.onDeviceMethod('thermostat1*reboot', commandHandler);
```

#### <a name="request-and-response-payloads"></a>Nytto laster för begäran och svar

Kommandon använder typer för att definiera sina nytto laster för begäran och svar. En enhet måste deserialisera inkommande indataparameter och serialisera svaret. I följande exempel visas hur du implementerar ett kommando med komplexa typer som definieras i nytto lasterna:

```json
{
  "@type": "Command",
  "name": "getMaxMinReport",
  "displayName": "Get Max-Min report.",
  "description": "This command returns the max, min and average temperature from the specified time to the current time.",
  "request": {
    "name": "since",
    "displayName": "Since",
    "description": "Period to return the max-min report.",
    "schema": "dateTime"
  },
  "response": {
    "name" : "tempReport",
    "displayName": "Temperature Report",
    "schema": {
      "@type": "Object",
      "fields": [
        {
          "name": "maxTemp",
          "displayName": "Max temperature",
          "schema": "double"
        },
        {
          "name": "minTemp",
          "displayName": "Min temperature",
          "schema": "double"
        },
        {
          "name" : "avgTemp",
          "displayName": "Average Temperature",
          "schema": "double"
        },
        {
          "name" : "startTime",
          "displayName": "Start Time",
          "schema": "dateTime"
        },
        {
          "name" : "endTime",
          "displayName": "End Time",
          "schema": "dateTime"
        }
      ]
    }
  }
}
```

Följande kodfragment visar hur en enhet implementerar den här kommando definitionen, inklusive de typer som används för att aktivera serialisering och deserialisering:

```nodejs
class TemperatureSensor {

  // ...

  getMaxMinReportObject() {
    return {
      maxTemp: this.maxTemp,
      minTemp: this.minTemp,
      avgTemp: this.cumulativeTemperature / this.numberOfTemperatureReadings,
      endTime: (new Date(Date.now())).toISOString(),
      startTime: this.startTime
    };
  }
}

// ...

const deviceTemperatureSensor = new TemperatureSensor();

const commandHandler = async (request, response) => {
  switch (request.methodName) {
  case commandMaxMinReport: {
    console.log('MaxMinReport ' + request.payload);
    await response.send(200, deviceTemperatureSensor.getMaxMinReportObject());
    break;
  }
  default:
    await response.send(404, 'unknown method');
    break;
  }
};
```

> [!Tip]
> Namnen på begäran och svaren finns inte i de serialiserade nytto lasterna som överförs via kabeln.
