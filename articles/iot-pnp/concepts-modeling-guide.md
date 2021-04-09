---
title: Förstå IoT Plug and Play enhets modeller | Microsoft Docs
description: Förstå DTDL-modell språket (Digital Definition Language) för IoT plug and Play-enheter. Artikeln beskriver primitiva och komplexa data typer, återanvänd mönster som använder komponenter och arv och semantiska typer. Artikeln innehåller vägledning om valet av enhets dubbla modell identifierare och verktygs stöd för modell redigering.
author: dominicbetts
ms.author: dobett
ms.date: 03/09/2021
ms.topic: conceptual
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 85888370106f34c723be4e47738114f7df33dcf4
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106057481"
---
# <a name="iot-plug-and-play-modeling-guide"></a>IoT Plug and Play modellerings guide

I IoT Plug and Play är en enhets _modell_ som beskriver enhetens funktioner för ett IoT plug and Play-aktiverat program. Den här modellen är strukturerad som en uppsättning gränssnitt som definierar:

- _Egenskaper_ som representerar ett skrivskyddat eller skrivbart tillstånd för en enhet eller annan enhet. Ett enhets serie nummer kan till exempel vara en skrivskyddad egenskap och en mål temperatur på en termostat kan vara en skrivbar egenskap.
- _Telemetridata_ som definierar de data som skickas av en enhet, oavsett om data är en vanlig ström av sensor avläsningar, ett tillfälligt fel eller ett informations meddelande.
- _Kommandon_ som beskriver en funktion eller åtgärd som kan utföras på en enhet. Ett kommando kan till exempel starta om en gateway eller ta en bild med en fjärran sluten kamera.

Mer information om hur IoT Plug and Play använder enhets modeller finns i [iot plug and Play enhets guide för utvecklare](concepts-developer-guide-device.md) och [IoT plug and Play service Developer Guide](concepts-developer-guide-service.md).

Om du vill definiera en modell använder du DTDL (Digital garn Definition Language). DTDL använder en JSON-variant som kallas [JSON-LD](https://json-ld.org/). I följande kodfragment visas modellen för en termostat-enhet som:

- Har ett unikt modell-ID: `dtmi:com:example:Thermostat;1` .
- Skickar temperatur telemetri.
- Har en skrivbar egenskap för att ange mål temperatur.
- Har en skrivskyddad egenskap för att rapportera den maximala temperaturen sedan den senaste omstarten.
- Svarar på ett kommando som begär högsta, lägsta och genomsnittliga temperaturer under en tids period.

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "targetTemperature",
      "schema": "double",
      "displayName": "Target Temperature",
      "description": "Allows to remotely specify the desired target temperature.",
      "unit": "degreeCelsius",
      "writable": true
    },
    {
      "@type": [
        "Property",
        "Temperature"
      ],
      "name": "maxTempSinceLastReboot",
      "schema": "double",
      "unit": "degreeCelsius",
      "displayName": "Max temperature since last reboot.",
      "description": "Returns the max temperature since last device reboot."
    },
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
        "name": "tempReport",
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
              "name": "avgTemp",
              "displayName": "Average Temperature",
              "schema": "double"
            },
            {
              "name": "startTime",
              "displayName": "Start Time",
              "schema": "dateTime"
            },
            {
              "name": "endTime",
              "displayName": "End Time",
              "schema": "dateTime"
            }
          ]
        }
      }
    }
  ]
}
```

Termostat-modellen har ett enda gränssnitt. Senare exempel i den här artikeln visar mer komplexa modeller som använder komponenter och arv.

Den här artikeln beskriver hur du utformar och redigerar egna modeller och omfattar ämnen som data typer, modell struktur och verktyg.

Mer information finns i specifikationen [digital-definitions språk v2](https://github.com/Azure/opendigitaltwins-dtdl) .

## <a name="model-structure"></a>Modellstruktur

Egenskaper, telemetri och kommandon grupperas i gränssnitt. I det här avsnittet beskrivs hur du kan använda gränssnitt för att beskriva enkla och komplexa modeller med hjälp av komponenter och arv.

### <a name="model-ids"></a>Modell-ID: n

Varje gränssnitt har ett unikt digitalt modell-ID (DTMI). Komplexa modeller använder DTMIs för att identifiera komponenter. Program kan använda DTMIs som enheterna skickar för att hitta modell definitioner i en lagrings plats.

DTMIs bör följa namngivnings konventionen som krävs av [IoT plug and Play Model-lagringsplatsen](https://github.com/Azure/iot-plugandplay-models):

- DTMI-prefixet är `dtmi:` .
- DTMI-suffixet är versions nummer för modellen, till exempel `;2` .
- Bröd texten i DTMI mappar till mappen och filen i modell databasen där modellen lagras. Versions numret är en del av fil namnet.

Modellen som identifieras av DTMI lagras till exempel `dtmi:com:Example:Thermostat;2` i *DTMI/com/exempel/thermostat-2.jsi* filen.

Följande fragment visar dispositionen för en gränssnitts definition med dess unika DTMI:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;2",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    ...
  ]
}
```

### <a name="no-components"></a>Inga komponenter

En enkel modell, till exempel termostat som visas tidigare, använder inte inbäddade eller överlappande komponenter. Telemetri, egenskaper och kommandon definieras i `contents` noden i gränssnittet.

I följande exempel visas en del av en enkel modell som inte använder komponenter:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:Thermostat;1",
  "@type": "Interface",
  "displayName": "Thermostat",
  "description": "Reports current temperature and provides desired temperature control.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "Temperature"
      ],
      "name": "temperature",
      "displayName": "Temperature",
      "description": "Temperature in degrees Celsius.",
      "schema": "double",
      "unit": "degreeCelsius"
    },
    {
      "@type": [
        "Property",
...
```

Verktyg som Azure IoT Explorer och IoT Central Device Template designer har ett fristående gränssnitt som termostat som en _standard komponent_.

Följande skärm bild visar hur modellen visas i Azure IoT Explorer-verktyget:

:::image type="content" source="media/concepts-modeling-guide/default-component.png" alt-text="Standard komponent i Azure IoT Explorer":::

Följande skärm bild visar hur modellen visas som standard komponent i IoT Central Device Template designer. Välj **Visa identitet** om du vill se DTMI för modellen:

:::image type="content" source="media/concepts-modeling-guide/iot-central-model.png" alt-text="Skärm bild som visar termostat-modellen i IoT Central designer":::

Modell-ID: t lagras i en enhets dubbla-egenskap som följande skärm bild visar:

:::image type="content" source="media/concepts-modeling-guide/twin-model-id.png" alt-text="Modell-ID i digital, dubbel egenskap":::

En DTDL-modell utan komponenter är en användbar förenkling för en enhet eller en IoT Edge modul med en enda uppsättning telemetri, egenskaper och kommandon. En modell som inte använder komponenter gör det enkelt att migrera en befintlig enhet eller modul till en IoT Plug and Play enhet eller modul – du skapar en DTDL-modell som beskriver din faktiska enhet eller modul utan att behöva definiera några komponenter.

> [!TIP]
> En modul kan vara en enhets [modul](../iot-hub/iot-hub-devguide-module-twins.md) eller en [IoT Edge-modul](../iot-edge/about-iot-edge.md).

### <a name="reuse"></a>Återanvända

Det finns två sätt att återanvända gränssnitts definitioner. Använd flera komponenter i en modell för att referera till andra gränssnitts definitioner. Använd arv för att utöka befintliga gränssnitts definitioner.

### <a name="multiple-components"></a>Flera komponenter

Med komponenter kan du bygga ett modell gränssnitt som en sammansättning av andra gränssnitt.

Till exempel definieras [termostat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) -gränssnittet som en modell. Du kan lägga till det här gränssnittet som en eller flera komponenter när du definierar [modell för temperatur styrenheten](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json). I följande exempel kallas dessa komponenter `thermostat1` och `thermostat2` .

För en DTDL-modell med flera komponenter finns det två eller flera komponent avsnitt. Varje avsnitt har `@type` angetts till `Component` och uttryckligen refererar till ett schema som visas i följande kodfragment:

```json
{
  "@context": "dtmi:dtdl:context;2",
  "@id": "dtmi:com:example:TemperatureController;1",
  "@type": "Interface",
  "displayName": "Temperature Controller",
  "description": "Device with two thermostats and remote reboot.",
  "contents": [
    {
      "@type": [
        "Telemetry",
        "DataSize"
      ],
      "name": "workingSet",
      "displayName": "Working Set",
      "description": "Current working set of the device memory in KiB.",
      "schema": "double",
      "unit": "kibibyte"
    },
    {
      "@type": "Property",
      "name": "serialNumber",
      "displayName": "Serial Number",
      "description": "Serial number of the device.",
      "schema": "string"
    },
    {
      "@type": "Command",
      "name": "reboot",
      "displayName": "Reboot",
      "description": "Reboots the device after waiting the number of seconds specified.",
      "request": {
        "name": "delay",
        "displayName": "Delay",
        "description": "Number of seconds to wait before rebooting the device.",
        "schema": "integer"
      }
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat1",
      "displayName": "Thermostat One",
      "description": "Thermostat One of Two."
    },
    {
      "@type" : "Component",
      "schema": "dtmi:com:example:Thermostat;1",
      "name": "thermostat2",
      "displayName": "Thermostat Two",
      "description": "Thermostat Two of Two."
    },
    {
      "@type": "Component",
      "schema": "dtmi:azure:DeviceManagement:DeviceInformation;1",
      "name": "deviceInformation",
      "displayName": "Device Information interface",
      "description": "Optional interface with basic device hardware information."
    }
  ]
}
```

Den här modellen har tre komponenter som definieras i avsnittet innehåll – två `Thermostat` komponenter och en `DeviceInformation` komponent. Avsnittet innehåll innehåller också egenskaper, telemetri och kommando definitioner.

Följande skärm bilder visar hur den här modellen visas i IoT Central. Egenskaperna, telemetri och kommando definitioner i temperatur styrenheten visas i **standard komponenten** på den översta nivån. Egenskaperna, telemetri och kommando definitioner för varje termostat visas i komponent definitionerna:

:::image type="content" source="media/concepts-modeling-guide/temperature-controller.png" alt-text="Skärm bild som visar enhets mal len för temperatur styrenheten i IoT Central.":::

:::image type="content" source="media/concepts-modeling-guide/temperature-controller-components.png" alt-text="Skärm bild som visar termostat-komponenterna i enhets mal len för temperatur styrenheten i IoT Central.":::

Information om hur du skriver enhets kod som samverkar med komponenter finns i [IoT plug and Play Device Developer Guide](concepts-developer-guide-device.md).

Information om hur du skriver service kod som interkatter innehåller komponenter på en enhet finns i [IoT plug and Play service Developer Guide](concepts-developer-guide-service.md).

### <a name="inheritance"></a>Arv

Med arv kan du återanvända funktioner i ett bas gränssnitt för att utöka funktionerna i ett gränssnitt. Till exempel kan flera enhets modeller dela vanliga funktioner, till exempel ett serie nummer:

:::image type="content" source="media/concepts-modeling-guide/inheritance.png" alt-text="Exempel på arv i en enhets modell som visar en termostat och en flödes styrenhet som delar funktioner från ett bas gränssnitt." border="false":::

I följande kodfragment visas en DTML-modell som använder `extends` nyckelordet för att definiera arvs relationen som visas i föregående diagram:

```json
[
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:Thermostat;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double",
        "unit": "degreeCelsius"
      },
      {
        "@type": "Property",
        "name": "targetTemperature",
        "schema": "double",
        "unit": "degreeCelsius",
        "writable": true
      }
    ],
    "extends": [
      "dtmi:com:example:baseDevice;1"
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:baseDevice;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Property",
        "name": "SerialNumber",
        "schema": "double",
        "writable": false
      }
    ]
  }
]
```

Följande skärm bild visar den här modellen i IoT Central enhets mal len miljö:

:::image type="content" source="media/concepts-modeling-guide/iot-central-inheritance.png" alt-text="Skärm bild som visar gränssnitts arv i IoT Central":::

När du skriver enhets-eller kod på tjänst sidan behöver koden inte göra något särskilt för att hantera ärvda gränssnitt. I exemplet som visas i det här avsnittet rapporterar enhets koden serie numret som om det är en del av termostat-gränssnittet.

### <a name="tips"></a>Tips

Du kan kombinera komponenter och arv när du skapar en modell. Följande diagram visar en `thermostat` modell som ärver från ett `baseDevice` gränssnitt. `baseDevice`Gränssnittet har en komponent som i sig själv ärver från ett annat gränssnitt:

:::image type="content" source="media/concepts-modeling-guide/inheritance-components.png" alt-text="Diagram som visar en modell som använder både komponenter och arv." border="false":::

I följande kodfragment visas en DTML-modell som använder- `extends` och- `component` nyckelordet för att definiera arvs relationen och komponent användningen som visas i föregående diagram:

```json
[
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:Thermostat;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double",
        "unit": "degreeCelsius"
      },
      {
        "@type": "Property",
        "name": "targetTemperature",
        "schema": "double",
        "unit": "degreeCelsius",
        "writable": true
      }
    ],
    "extends": [
      "dtmi:com:example:baseDevice;1"
    ]
  },
  {
    "@context": "dtmi:dtdl:context;2",
    "@id": "dtmi:com:example:baseDevice;1",
    "@type": "Interface",
    "contents": [
      {
        "@type": "Property",
        "name": "SerialNumber",
        "schema": "double",
        "writable": false
      },
      {
        "@type" : "Component",
        "schema": "dtmi:com:example:baseComponent;1",
        "name": "baseComponent"
      }
    ]
  }
]
```

## <a name="data-types"></a>Datatyper

Använd data typer för att definiera telemetri, egenskaper och kommando parametrar. Data typer kan vara primitiva eller komplexa. Komplexa data typer använder primitiver eller andra komplexa typer. Det maximala djupet för komplexa typer är fem nivåer.

### <a name="primitive-types"></a>Primitiva typer

I följande tabell visas en uppsättning primitiva typer som du kan använda:

| Primitiv typ | Beskrivning |
| --- | --- |
| `boolean` | Ett booleskt värde |
| `date` | Ett fullständigt datum enligt definitionen i [avsnitt 5,6 i RFC 3339](https://tools.ietf.org/html/rfc3339#section-5.6) |
| `dateTime` | En datum/tid enligt definitionen i [RFC 3339](https://tools.ietf.org/html/rfc3339) |
| `double` | En flyttal med IEEE 8 byte |
| `duration` | En varaktighet i ISO 8601-format |
| `float` | En IEEE 4-bytes flyttal |
| `integer` | Ett signerat 4-byte-heltal |
| `long` | Ett signerat 8-byte-heltal |
| `string` | En UTF8-sträng |
| `time` | En fullständig tid enligt definitionen i [avsnitt 5,6 i RFC 3339](https://tools.ietf.org/html/rfc3339#section-5.6) |

Följande fragment visar en exempel på telemetri-definition som använder `double` typen i `schema` fältet:

```json
{
  "@type": "Telemetry",
  "name": "temperature",
  "displayName": "Temperature",
  "schema": "double"
}
```

### <a name="complex-datatypes"></a>Komplexa data typer

Komplexa data typer är en *matris*, en *uppräkning*, en *karta*, ett *objekt* eller en av de geospatiala typerna.

#### <a name="arrays"></a>Matriser

En matris är en indexerad datatyp där alla element är av samma typ. Element typen kan vara en primitiv eller komplex typ.

I följande fragment visas en exempel på telemetri som använder `Array` typen i `schema` fältet. Elementen i matrisen är booleska värden:

```json
{
  "@type": "Telemetry",
  "name": "ledState",
  "schema": {
    "@type": "Array",
    "elementSchema": "boolean"
  }
}
```

#### <a name="enumerations"></a>Uppräkningar

En uppräkning beskriver en typ med en uppsättning med namngivna etiketter som mappar till värden. Värdena kan vara antingen heltal eller strängar, men etiketterna är alltid strängar.

I följande fragment visas en exempel på telemetri som använder `Enum` typen i `schema` fältet. Värdena i uppräkningen är heltal:

```json
{
  "@type": "Telemetry",
  "name": "state",
  "schema": {
    "@type": "Enum",
    "valueSchema": "integer",
    "enumValues": [
      {
        "name": "offline",
        "displayName": "Offline",
        "enumValue": 1
      },
      {
        "name": "online",
        "displayName": "Online",
        "enumValue": 2
      }
    ]
  }
}
```

#### <a name="maps"></a>Maps

En karta är en typ med nyckel/värde-par där alla värden har samma typ. Nyckeln i en karta måste vara en sträng. Värdena i en karta kan vara vilken typ som helst, inklusive en annan komplex typ.

I följande kodfragment visas en exempel egenskaps definition som använder `Map` typen i `schema` fältet. Värdena i kartan är strängar:

```json
{
  "@type": "Property",
  "name": "modules",
  "writable": true,
  "schema": {
    "@type": "Map",
    "mapKey": {
      "name": "moduleName",
      "schema": "string"
    },
    "mapValue": {
      "name": "moduleState",
      "schema": "string"
    }
  }
}
```

### <a name="objects"></a>Objekt

En objekt typ består av namngivna fält. Typerna av fält i en objekt karta kan vara primitiva eller komplexa typer.

I följande fragment visas en exempel på telemetri som använder `Object` typen i `schema` fältet. Fälten i objektet är `dateTime` , `duration` -och- `string` typer:

```json
{
  "@type": "Telemetry",
  "name": "monitor",
  "schema": {
    "@type": "Object",
    "fields": [
      {
        "name": "start",
        "schema": "dateTime"
      },
      {
        "name": "interval",
        "schema": "duration"
      },
      {
        "name": "status",
        "schema": "string"
      }
    ]
  }
}
```

#### <a name="geospatial-types"></a>Geospatiala typer

DTDL tillhandahåller en uppsättning geospatiala typer, baserat på geografiskt [JSON](https://geojson.org/), för modellering av geografiska data strukturer:,,,, `point` `multiPoint` `lineString` `multiLineString` `polygon` och `multiPolygon` . Dessa typer är fördefinierade kapslade strukturer av matriser, objekt och uppräkningar.

Följande fragment visar en exempel på telemetri-definition som använder `point` typen i `schema` fältet:

```json
{
  "@type": "Telemetry",
  "name": "location",
  "schema": "point"
}
```

Eftersom de geospatiala typerna är mat risbaserade, kan de inte användas för närvarande i egenskaps definitioner.

## <a name="semantic-types"></a>Semantiska typer

Data typen för en egenskaps-eller telemetri-definition anger formatet på de data som en enhet utbyter med en tjänst. Den semantiska typen ger information om telemetri och egenskaper som ett program kan använda för att avgöra hur du ska bearbeta eller visa ett värde. Varje semantisk typ har en eller flera tillhör ande enheter. Till exempel Celsius och Fahrenheit är enheter för semantisk typ av temperatur. IoT Central instrument paneler och analyser kan använda semantisk typ information för att avgöra hur du ska rita telemetri eller egenskaps värden och visnings enheter. Information om hur du kan använda modell parsern för att läsa de semantiska typerna finns i [förstå Digitals modell tolkare](concepts-model-parser.md).

I följande fragment visas en exempel på telemetri som innehåller information om semantisk typ. Den semantiska typen `Temperature` läggs till i `@type` matrisen och `unit` värdet `degreeCelsius` är en av de giltiga enheterna för den semantiska typen:

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

## <a name="localization"></a>Lokalisering

Program, till exempel IoT Central, använder information i modellen för att dynamiskt bygga ett gränssnitt runt data som utbyts med en IoT Plug and Play-enhet. Panelerna på en instrument panel kan till exempel Visa namn och beskrivningar för telemetri, egenskaper och kommandon.

Valfria- `description` och- `displayName` fälten i modellen innehåller strängar som ska användas i ett användar gränssnitt. Dessa fält kan innehålla lokaliserade strängar som ett program kan använda för att återge ett lokaliserat användar gränssnitt.

Följande fragment visar en definition för temperatur telemetri som innehåller lokaliserade strängar:

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "description": {
    "en": "Temperature in degrees Celsius.",
    "it": "Temperatura in gradi Celsius."
  },
  "displayName": {
    "en": "Temperature",
    "it": "Temperatura"
  },
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

Det är valfritt att lägga till lokaliserade strängar. Följande exempel har bara ett enda standard språk:

```json
{
  "@type": [
    "Telemetry",
    "Temperature"
  ],
  "description": "Temperature in degrees Celsius.",
  "displayName": "Temperature",
  "name": "temperature",
  "schema": "double",
  "unit": "degreeCelsius"
}
```

## <a name="lifecycle-and-tools"></a>Livs cykel och verktyg

De fyra livs cykel stegen för en enhets modell är redigering, publicering, användning och versions hantering:

### <a name="author"></a>Författare

DTML-enhets modeller är JSON-dokument som du kan skapa i en text redigerare. I IoT Central kan du dock använda GUI-miljön för enhets mal len för att skapa en DTML-modell. I IoT Central kan du:

- Skapa gränssnitt som definierar egenskaper, telemetri och kommandon.
- Använd komponenter för att sätta samman flera gränssnitt.
- Definiera arvs relationer mellan gränssnitt.
- Importera och exportera DTML-modeller.

Mer information finns i [definiera en ny IoT-enhets typ i ditt Azure IoT Central-program](../iot-central/core/howto-set-up-template.md).

[DTDL-redigeraren för Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) ger dig en text baserad redigerings miljö med autentisering och automatisk komplettering för att få bättre kontroll över modell redigeringen.

### <a name="publish"></a>Publicera

För att DTML-modeller ska kunna delas och upptäckas kan du publicera dem i en lagrings plats för enhets modeller.

Innan du publicerar en modell i det offentliga lagret kan du använda `dmr-client` verktygen för att validera din modell.

Läs mer i [enhets modellens lagrings plats](concepts-model-repository.md).

### <a name="use"></a>Användning

Program, till exempel IoT Central, använder enhets modeller. I IoT Central är en modell en del av enhets mal len som beskriver enhetens funktioner. IoT Central använder enhets mal len för att dynamiskt bygga ett användar gränssnitt för enheten, inklusive instrument paneler och analyser.

En anpassad lösning kan använda [Digitals modell tolkare](concepts-model-parser.md) för att förstå funktionerna i en enhet som implementerar modellen. Läs mer i [använda iot plug and Play-modeller i en IoT-lösning](concepts-model-discovery.md).

### <a name="version"></a>Version

För att säkerställa att enheter och lösningar på Server sidan som använder modeller fortsätter att fungera, kan inte publicerade modeller ändras.

DTMI innehåller ett versions nummer som du kan använda för att skapa flera versioner av en modell. Enheter och lösningar på Server sidan kan använda den aktuella version som de har utformats för att användas.

IoT Central implementerar fler versions regler för enhets modeller. Om du har version av en enhets mall och dess modell i IoT Central kan du migrera enheter från tidigare versioner till senare versioner. Migrerade enheter kan dock inte använda nya funktioner utan uppgradering av inbyggd program vara. Mer information finns i [skapa en ny version av enhets mal len](../iot-central/core/howto-version-device-template.md).

## <a name="limits-and-constraints"></a>Begränsningar och begränsningar

I följande lista sammanfattas några viktiga begränsningar och begränsningar för modeller:

- För närvarande är det maximala djupet för matriser, Maps och objekt fem nivåer av djupet.
- Du kan inte använda matriser i egenskaps definitioner.
- Du kan utöka gränssnitten till ett djup på 10 nivåer.
- Ett gränssnitt kan utökas med de flesta två andra gränssnitt.
- En komponent kan inte innehålla en annan komponent.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig om enhets modellering är här några ytterligare resurser:

- [Installera och Använd DTDL redigerings verktyg](howto-use-dtdl-authoring-tools.md)
- [Digitalt flätade Definition Language v2 (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl)
- [Modell databaser](./concepts-model-repository.md)
