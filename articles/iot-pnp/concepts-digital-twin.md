---
title: Lär dig mer om digitala tvillingar i IoT Plug and Play
description: Förstå hur IoT Plug and Play använder digitala dubbla
author: prashmo
ms.author: prashmo
ms.date: 12/14/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 99c957e5bf6ffe69c94e109796590f5ab975c3cf
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/17/2020
ms.locfileid: "97656894"
---
# <a name="understand-iot-plug-and-play-digital-twins"></a>Lär dig mer om digitala tvillingar i IoT Plug and Play

En IoT plug and Play-enhet implementerar en modell som beskrivs av [DTDL-schemat (Digital Definition Language)](https://github.com/Azure/opendigitaltwins-dtdl) . En modell beskriver en uppsättning av komponenter, egenskaper, kommandon och telemetri som en viss enhet kan ha.

IoT Plug and Play använder DTDL version 2. Mer information om den här versionen finns i avsnittet [digital-definitions språk (DTDL)-version 2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) på GitHub.

> [!NOTE]
> DTDL är inte exklusiv för IoT Plug and Play. Andra IoT-tjänster, till exempel [Azure Digital-dubbla](../digital-twins/overview.md), använder den för att representera hela miljöer som byggnader och energi nät.

SDK: er för Azure IoT-tjänster innehåller API: er som gör att en tjänst interagerar med en enhets digitala enhet En tjänst kan till exempel läsa enhets egenskaper från den digitala enheten eller använda digitala dubbla för att anropa ett kommando på en enhet. Läs mer i [IoT Hub digitala dubbla exempel](concepts-developer-guide-service.md#iot-hub-digital-twin-examples).

Exemplet IoT Plug and Play Device i den här artikeln implementerar en [modell för temperatur styrenheter](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) som har [termostat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) -komponenter.

## <a name="device-twins-and-digital-twins"></a>Enhets dubbla och digitala dubbla

Med en digital enhet har Azure IoT Hub också en *enhet med dubbla* för varje ansluten enhet. En enhet är på samma sätt som en digital enhet på så sätt att den är en representation av en enhets egenskaper. SDK: er för Azure IoT-tjänsten innehåller API: er för att interagera med enhets dubbla.

En IoT-hubb initierar en digital enhet och en enhet sammanflätar första gången en IoT Plug and Play-enhet ansluter.

Enhets dubbla är JSON-dokument som lagrar information om enhets tillstånd, inklusive metadata, konfigurationer och villkor. Läs mer i [IoT Hub tjänst klient exempel](concepts-developer-guide-service.md#iot-hub-service-client-examples). Både enhets-och lösnings utvecklare kan fortsätta att använda samma uppsättning med dubbla API: er och SDK: er för att implementera enheter och lösningar med IoT Plug and Play konventioner.

De digitala dubbla API: erna hanterar DTDL-konstruktioner på hög nivå, till exempel komponenter, egenskaper och kommandon. De digitala dubbla API: erna gör det enklare för lösnings byggare att skapa IoT Plug and Play-lösningar.

I en enhet är det en skrivbar egenskap som delas upp i avsnitten *önskade egenskaper* och *rapporterade egenskaper* . Alla skrivskyddade egenskaper är tillgängliga i avsnittet rapporterade egenskaper.

I en digital, finns det en enhetlig vy av egenskapens aktuella och önskade tillstånd. Synkroniseringsstatus för en specifik egenskap lagras i motsvarande standard komponent `$metadata` avsnitt.

### <a name="device-twin-json-example"></a>Enhets dubbla JSON-exempel

Följande fragment visar en IoT Plug and Play-enhet som är dubbelt formaterad som ett JSON-objekt:

```json
{
  "deviceId": "sample-device",
  "modelId": "dtmi:com:example:TemperatureController;1",
  "version": 15,
  "properties": {
    "desired": {
      "thermostat1": {
        "__t": "c",
        "targetTemperature": 21.8
      },
      "$metadata": {...},
      "$version": 4
    },
    "reported": {
      "serialNumber": "alwinexlepaho8329",
      "thermostat1": {
        "maxTempSinceLastReboot": 25.3,
        "__t": "c",
        "targetTemperature": {
          "value": 21.8,
          "ac": 200,
          "ad": "Successfully executed patch",
        }
      },
      "$metadata": {...},
      "$version": 11
    }
  }
}
```

### <a name="digital-twin-example"></a>Digitalt dubbla exempel

Följande fragment visar det digitala dubbla formatet som ett JSON-objekt:

```json
{
  "$dtId": "sample-device",
  "serialNumber": "alwinexlepaho8329",
  "thermostat1": {
    "maxTempSinceLastReboot": 25.3,
    "targetTemperature": 21.8,
    "$metadata": {
      "targetTemperature": {
        "desiredValue": 21.8,
        "desiredVersion": 4,
        "ackVersion": 4,
        "ackCode": 200,
        "ackDescription": "Successfully executed patch",
        "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
      },
      "maxTempSinceLastReboot": {
         "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:example:TemperatureController;1",
    "serialNumber": {
      "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
    }
  }
}
```

I följande tabell beskrivs fälten i det digitala dubbla JSON-objektet:

| Fältnamn | Beskrivning |
| --- | --- |
| `$dtId` | En användardefinierad sträng som representerar ID: t för enheten Digital |
| `{propertyName}` | Värdet för en egenskap i JSON |
| `$metadata.$model` | Valfritt ID: t för det modell gränssnitt som kännetecknar detta digitala dubbla |
| `$metadata.{propertyName}.desiredValue` | [Endast för skrivbara egenskaper] Det önskade värdet för den angivna egenskapen |
| `$metadata.{propertyName}.desiredVersion` | [Endast för skrivbara egenskaper] Den version av det önskade värdet som underhålls av IoT Hub|
| `$metadata.{propertyName}.ackVersion` | [Krävs, endast för skrivbara egenskaper] Versionen som bekräftats av enheten som implementerar den digitala enheten måste vara större än eller lika med den önskade versionen |
| `$metadata.{propertyName}.ackCode` | [Krävs, endast för skrivbara egenskaper] `ack` Koden som returneras av enhets appen som implementerar den digitala dubbla |
| `$metadata.{propertyName}.ackDescription` | [Valfritt, endast för skrivbara egenskaper] `ack` Beskrivningen som returneras av enhets appen som implementerar den digitala dubbla |
| `$metadata.{propertyName}.lastUpdateTime` | IoT Hub behåller tidsstämpeln för den senaste uppdateringen av egenskapen av enheten. Tidsstämplar är UTC-kodade och kodade i formatet ISO8601 ÅÅÅÅ-MM-DDTHH: MM: SS. mmmZ |
| `{componentName}` | Ett JSON-objekt som innehåller komponentens egenskaps värden och metadata. |
| `{componentName}.{propertyName}` | Värdet för komponentens egenskap i JSON |
| `{componentName}.$metadata` | Information om metadata för komponenten. |

### <a name="properties"></a>Egenskaper

Egenskaper är data fält som representerar statusen för en entitet (som egenskaper i många objektorienterade programmeringsspråk).

#### <a name="read-only-property"></a>Skrivskyddad egenskap

DTDL-schema:

```json
{
    "@type": "Property",
    "name": "serialNumber",
    "displayName": "Serial Number",
    "description": "Serial number of the device.",
    "schema": "string"
}
```

I det här exemplet `alwinexlepaho8329` är det aktuella värdet för den `serialNumber` skrivskyddade egenskapen som rapporteras av enheten.
I följande kodfragment visas en sida-vid-sida-JSON-representation av `serialNumber` egenskapen:

:::row:::
   :::column span="":::
      **Enhetstvilling**

```json
"properties": {
  "reported": {
    "serialNumber": "alwinexlepaho8329"
  }
}
```

   :::column-end:::
   :::column span="":::
      **Digital, dubbel**

```json
"serialNumber": "alwinexlepaho8329"
```

   :::column-end:::
:::row-end:::

#### <a name="writable-property"></a>Skrivbar egenskap

I följande exempel visas en skrivbar egenskap i standard komponenten.

DTDL:

```json
{
  "@type": "Property",
  "name": "fanSpeed",
  "displayName": "Fan Speed",
  "writable": true,
  "schema": "double"
}
```

:::row:::
   :::column span="":::
      **Enhetstvilling**

```json
{
  "properties": {
    "desired": {
      "fanSpeed": 2.0,
    },
    "reported": {
      "fanSpeed": {
        "value": 3.0,
        "ac": 200,
        "av": 1,
        "ad": "Successfully executed patch version 1"
      }
    }
  },
}
```

   :::column-end:::
   :::column span="":::
      **Digital, dubbel**

```json
{
  "fanSpeed": 3.0,
  "$metadata": {
    "fanSpeed": {
      "desiredValue": 2.0,
      "desiredVersion": 2,
      "ackVersion": 1,
      "ackCode": 200,
      "ackDescription": "Successfully executed patch version 1",
      "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
    }
  }
}
```

   :::column-end:::
:::row-end:::

I det här exemplet `3.0` är det aktuella värdet för den `fanSpeed` egenskap som rapporteras av enheten. `2.0` är det önskade värdet som anges av lösningen. Det önskade värdet och synkroniseringsstatus för en rot nivå egenskap anges inom rot nivån `$metadata` för en digital enhet. När enheten är online kan den installera den här uppdateringen och rapportera det uppdaterade värdet.

### <a name="components"></a>Komponenter

Med komponenter kan du skapa modell gränssnitt som en sammansättning av andra gränssnitt.
Till exempel kan [termostat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) -gränssnittet införlivas som komponenter `thermostat1` och  `thermostat2` i modell modellen för [temperatur styrenheten](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) .

En komponent identifieras av markören i en enhet `{ "__t": "c"}` . I en Digitals, är förekomsten av `$metadata` en komponent att markeras.

I det här exemplet `thermostat1` är en komponent med två egenskaper:

- `maxTempSinceLastReboot` är en skrivskyddad egenskap.
- `targetTemperature` är en skrivbar egenskap som har synkroniserats av enheten. Det önskade värdet och synkroniseringsstatus för dessa egenskaper finns i komponentens `$metadata` .

I följande kodfragment visas en sida-vid-sida-JSON-representation av `thermostat1` komponenten:

:::row:::
   :::column span="":::
      **Enhetstvilling**

```json
"properties": {
  "desired": {
    "thermostat1": {
      "__t": "c",
      "targetTemperature": 21.8
    },
    "$metadata": {
    },
    "$version": 4
  },
  "reported": {
    "thermostat1": {
      "maxTempSinceLastReboot": 25.3,
      "__t": "c",
      "targetTemperature": {
        "value": 21.8,
        "ac": 200,
        "ad": "Successfully executed patch",
        "av": 4
      }
    },
    "$metadata": {
    },
    "$version": 11
  }
}
```

   :::column-end:::
   :::column span="":::
      **Digital, dubbel**

```json
"thermostat1": {
  "maxTempSinceLastReboot": 25.3,
  "targetTemperature": 21.8,
  "$metadata": {
    "targetTemperature": {
      "desiredValue": 21.8,
      "desiredVersion": 4,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "Successfully executed patch",
      "lastUpdateTime": "2020-07-17T06:11:04.9309159Z"
    },
    "maxTempSinceLastReboot": {
       "lastUpdateTime": "2020-07-17T06:10:31.9609233Z"
    }
  }
}
```

   :::column-end:::
:::row-end:::

## <a name="digital-twin-apis"></a>Digitala dubbla API: er

De digitala dubbla API: erna omfattar kommandot för att **få digitala** dubbla, **Uppdatera digitala dubbla**, **anropa komponent** och **anropa kommando** åtgärder för att hantera en digital, dubbel. Du kan antingen använda [REST-API: erna](/rest/api/iothub/service/digitaltwin) direkt eller via ett [service SDK](../iot-pnp/libraries-sdks.md).

## <a name="digital-twin-change-events"></a>Ändringshändelser för digitala tvillingar

När ändringshändelser för digitala tvillingar är aktiverat utlöses en händelse när komponentens eller egenskapens aktuella eller önskade värde ändras. Digitala dubbla ändrings händelser skapas i [JSON patch](http://jsonpatch.com/) -format. Motsvarande händelser genereras i enhetens dubbla format om dubbla ändrings händelser är aktiverade.

Information om hur du aktiverar routning för enhet och digitala dubbla händelser finns i [använda IoT Hub meddelanderoutning för att skicka meddelanden från enhet till moln till olika slut punkter](../iot-hub/iot-hub-devguide-messages-d2c.md#non-telemetry-events). Information om meddelande formatet finns i [skapa och läsa IoT Hub meddelanden](../iot-hub/iot-hub-devguide-messages-construct.md).

Till exempel utlöses följande digitala dubbla ändrings händelser när `targetTemperature` den anges av lösningen:

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/17/2020 6:11:04 AM
iothub-message-source:digitalTwinChangeEvents
correlation-id:275d463fa034
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature",
    "value": {
      "desiredValue": 21.8,
      "desiredVersion": 4
    }
  }
]
```

Följande digitala dubbla ändrings händelser utlöses när enheten rapporterar att ovanstående önskade ändring tillämpades:

```json
iothub-connection-device-id:sample-device
iothub-enqueuedtime:7/17/2020 6:11:05 AM
iothub-message-source:digitalTwinChangeEvents
correlation-id:275d464a2c80
content-type:application/json-patch+json
content-encoding:utf-8
[
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/ackCode",
    "value": 200
  },
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/ackDescription",
    "value": "Successfully executed patch"
  },
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/ackVersion",
    "value": 4
  },
  {
    "op": "add",
    "path": "/thermostat1/$metadata/targetTemperature/lastUpdateTime",
    "value": "2020-07-17T06:11:04.9309159Z"
  },
  {
    "op": "add",
    "path": "/thermostat1/targetTemperature",
    "value": 21.8
  }
]
```

> [!NOTE]
> Dubbla ändrings meddelanden dubbleras när de aktive ras i meddelande om både enhets-och digitala meddelanden.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om digitala dubbla, finns här några ytterligare resurser:

- [Använda IoT Plug and Play digitala dubbla API: er](howto-manage-digital-twin.md)
- [Interagera med en enhet från din lösning](quickstart-service.md)
- [IoT Digital, dubbla REST API](/rest/api/iothub/service/digitaltwin)
- [Azure IoT Explorer](howto-use-iot-explorer.md)