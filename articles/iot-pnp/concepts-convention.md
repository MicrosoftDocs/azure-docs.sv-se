---
title: IoT Plug and Play-konventioner | Microsoft Docs
description: Beskrivning av konventionerna IoT Plug and Play förväntar sig att enheter ska användas när de skickar telemetri och egenskaper och hanterar kommandon och egenskaps uppdateringar.
author: rido-min
ms.author: rmpablos
ms.date: 07/10/2020
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: a8dfb3cacf798559273361ec75cab4570a8a5228
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582688"
---
# <a name="iot-plug-and-play-conventions"></a>Konventioner för IoT Plug and Play

IoT Plug and Play-enheter bör följa en uppsättning konventioner när de utbyter meddelanden med IoT Hub. IoT Plug and Play-enheter använder MQTT-protokollet för att kommunicera med IoT Hub.

Enheter kan innehålla [moduler](../iot-hub/iot-hub-devguide-module-twins.md)eller implementeras i en [IoT Edge modul](../iot-edge/about-iot-edge.md) som hanteras av IoT Edge Runtime.

Du beskriver telemetri, egenskaper och kommandon som en IoT Plug and Play-enhet implementerar med en digital- _modell_ med en [definitions språk v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl) . Det finns två typer av modeller som anges i den här artikeln:

- **Ingen komponent** -en modell utan komponenter. Modellen förklarar telemetri, egenskaper och kommandon som egenskaper på den högsta nivån i avsnittet innehåll i huvud gränssnittet. I Azure IoT Explorer-verktyget visas den här modellen som en enda _standard komponent_.
- **Flera komponenter** – en modell som består av två eller flera gränssnitt. Ett huvud gränssnitt som visas som _standard komponent_, med telemetri, egenskaper och kommandon. Ett eller flera gränssnitt som har deklarerats som komponenter med ytterligare telemetri, egenskaper och kommandon.

Mer information finns i [IoT plug and Play Modeling guide](concepts-modeling-guide.md).

## <a name="identify-the-model"></a>Identifiera modellen

För att meddela den modell som den implementerar, inkluderar en IoT Plug and Play-enhet eller-modul modell-ID: t i anslutnings paketet MQTT genom att lägga till `model-id` i `USERNAME` fältet.

För att identifiera vilken modell en enhet eller modul implementerar, kan en tjänst Hämta modell-ID: t från:

- Enhetens dubbla `modelId` fält.
- Det digitala dubbla `$metadata.$model` fältet.
- Ett digitalt meddelande om ändring av två meddelanden.

## <a name="telemetry"></a>Telemetri

Telemetri som skickas från ingen komponent enhet kräver inga extra metadata. Systemet lägger till `dt-dataschema` egenskapen.

Telemetri som skickas från en enhet med flera komponenter måste läggas till `$.sub` som en meddelande egenskap. Systemet lägger till `dt-subject` egenskaperna och `dt-dataschema` .

## <a name="read-only-properties"></a>Skrivskyddade egenskaper

### <a name="sample-no-component-read-only-property"></a>Exempel på en skrivskyddad komponent egenskap

En enhet eller modul kan skicka en giltig JSON som följer DTDL v2-reglerna.

DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:example: Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "temperature",
      "schema": "double"
    }
  ]
}
```

Exempel på rapporterad egenskaps nytto last:

```json
"reported" :
{
  "temperature" : 21.3
}
```

### <a name="sample-multiple-components-read-only-property"></a>Exempel på en skrivskyddad egenskap med flera komponenter

Enheten eller modulen måste lägga till `{"__t": "c"}` markören för att indikera att elementet refererar till en komponent.

DTDL som refererar till en komponent:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "contents": [
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1"
    }
  ]
}
```

DTDL som definierar komponenten:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "temperature",
      "schema": "double"
    }
  ]
}
```

Exempel på rapporterad egenskaps nytto last:

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "temperature": 21.3
  }
}
```

## <a name="writable-properties"></a>Skrivbara egenskaper

Enheten eller modulen bör bekräfta att den har fått egenskapen genom att skicka en rapporterad egenskap. Den rapporterade egenskapen ska innehålla:

- `value` – egenskapens faktiska värde (vanligt vis det mottagna värdet, men enheten kan välja att rapportera ett annat värde).
- `ac` – en bekräftelse kod som använder en HTTP-statuskod.
- `av` – en bekräftelse version som refererar till den `$version` önskade egenskapen. Du kan hitta det här värdet i den önskade egenskapen JSON-nyttolast.
- `ad` – en valfri bekräftelse beskrivning.

När en enhet startar bör den begära enheten, och kontrol lera eventuella uppdateringar av skrivbara egenskaper. Om versionen av en skrivbar egenskap ökar medan enheten var offline, ska enheten skicka ett svar som har rapporter ATS för att bekräfta att uppdateringen har tagits emot.

När en enhet startar för första gången kan den skicka ett start värde för en rapporterad egenskap om den inte får en inledande önskad egenskap från hubben. I det här fallet ska enheten anges `av` till `1` . Exempel:

```json
"reported": {
  "targetTemperature": {
    "value": 20.0,
    "ac": 200,
    "av": 1,
    "ad": "initialize"
  }
}
```

En enhet kan använda den rapporterade egenskapen för att tillhandahålla annan information till hubben. Enheten kan till exempel svara med en serie pågående meddelanden, till exempel:

```json
"reported": {
  "targetTemperature": {
    "value": 35.0,
    "ac": 202,
    "av": 3,
    "ad": "In-progress - reporting current temperature"
  }
}
```

När enheten når mål temperaturen skickas följande meddelande:

```json
"reported": {
  "targetTemperature": {
    "value": 20.0,
    "ac": 200,
    "av": 3,
    "ad": "Reached target temperature"
  }
}
```

En enhet kan rapportera ett fel som:

```json
"reported": {
  "targetTemperature": {
    "value": 120.0,
    "ac": 500,
    "av": 3,
    "ad": "Target temperature out of range. Valid range is 10 to 99."
  }
}
```

### <a name="sample-no-component-writable-property"></a>Exempel på ingen skrivbar komponent egenskap

När en enhet får flera rapporterade egenskaper i en enda nytto last kan den skicka de rapporterade egenskaps Svaren över flera nytto laster.

En enhet eller modul kan skicka en giltig JSON som följer DTDL v2-reglerna:

DTDL:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:example: Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "targetTemperature",
      "schema": "double",
      "writable": true
    }
  ]
}
```

Exempel på önskad egenskaps nytto last:

```json
"desired" :
{
  "targetTemperature" : 21.3,
  "targetHumidity" : 80
},
"$version" : 3
```

Exempel på rapporterad egenskap första nytto last:

```json
"reported": {
  "targetTemperature": {
    "value": 21.3,
    "ac": 200,
    "av": 3,
    "ad": "complete"
  }
}
```

Exempel på en andra nytto last för egenskap som rapporter ATS:

```json
"reported": {
  "targetHumidity": {
    "value": 80,
    "ac": 200,
    "av": 3,
    "ad": "complete"
  }
}
```

### <a name="sample-multiple-components-writable-property"></a>Exempel på en skrivbar egenskap med flera komponenter

Enheten eller modulen måste lägga till `{"__t": "c"}` markören för att indikera att elementet refererar till en komponent.

Markören skickas bara för uppdateringar till egenskaper som definierats i en komponent. Uppdateringar av egenskaper som definieras i standard komponenten inkluderar inte markören, se [exempel på ingen skrivbar komponent egenskap](#sample-no-component-writable-property)

När en enhet får flera rapporterade egenskaper i en enda nytto last kan den skicka de rapporterade egenskaps Svaren över flera nytto laster.

Enheten eller modulen bör bekräfta att den har fått egenskaperna genom att skicka rapporterade egenskaper:

DTDL som refererar till en komponent:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "contents": [
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1"
    }
  ]
}
```

DTDL som definierar komponenten:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "contents": [
    {
      "@type": "Property",
      "name": "targetTemperature",
      "schema": "double",
      "writable": true
    }
  ]
}
```

Exempel på önskad egenskaps nytto last:

```json
"desired": {
  "thermostat1": {
    "__t": "c",
    "targetTemperature": 21.3,
    "targetHumidity": 80
  }
},
"$version" : 3
```

Exempel på rapporterad egenskap första nytto last:

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "targetTemperature": {
      "value": 23,
      "ac": 200,
      "av": 3,
      "ad": "complete"
    }
  }
}
```

Exempel på en andra nytto last för egenskap som rapporter ATS:

```json
"reported": {
  "thermostat1": {
    "__t": "c",
    "targetHumidity": {
      "value": 80,
      "ac": 200,
      "av": 3,
      "ad": "complete"
    }
  }
}
```

## <a name="commands"></a>Kommandon

Inga komponent gränssnitt använder kommando namnet utan ett prefix.

På en enhet eller modul använder flera komponent gränssnitt kommando namn med följande format: `componentName*commandName` .

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om IoT Plug and Play-konventioner finns här några ytterligare resurser:

- [Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [C (enhets-SDK)](/azure/iot-hub/iot-c-sdk-ref/)
- [IoT-REST API](/rest/api/iothub/device)
- [IoT Plug and Play modellerings guide](concepts-modeling-guide.md)