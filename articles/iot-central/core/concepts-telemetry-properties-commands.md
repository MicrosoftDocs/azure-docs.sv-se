---
title: Telemetri, egenskaper och kommando nytto laster i Azure IoT Central | Microsoft Docs
description: Med Azure IoT Central Device templates kan du ange telemetri, egenskaper och kommandon för en enhet måste implementera. Förstå formatet på de data som en enhet kan utbyta med IoT Central.
author: dominicbetts
ms.author: dobett
ms.date: 12/19/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom: device-developer
ms.openlocfilehash: 87fb7f0eb4017a39aca081f73de543a67400d4b5
ms.sourcegitcommit: 9514d24118135b6f753d8fc312f4b702a2957780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/07/2021
ms.locfileid: "97969069"
---
# <a name="telemetry-property-and-command-payloads"></a>Telemetri, egenskaper och kommandonyttolaster

_Den här artikeln gäller för enhets utvecklare._

En enhets mall i Azure IoT Central är en skiss som definierar:

* Telemetri en enhet skickar till IoT Central.
* Egenskaper som en enhet synkroniseras med IoT Central.
* Kommandon som IoT Central anrop på en enhet.

I den här artikeln beskrivs, för enhets utvecklare, de JSON-nyttolaster som enheterna skickar och tar emot för telemetri, egenskaper och kommandon som definierats i en enhets mall.

Artikeln beskriver inte varje möjlig typ av telemetri, egenskaper och kommando nytto Last, men exemplen illustrerar alla nyckel typer.

I varje exempel visas ett kodfragment från enhets modellen som definierar typ och exempel på JSON-nyttolaster för att illustrera hur enheten ska interagera med IoT Central-programmet.

> [!NOTE]
> IoT Central accepterar en giltig JSON, men den kan bara användas för visualiseringar om den matchar en definition i enhets modellen. Du kan exportera data som inte matchar en definition, se [Exportera IoT-data till mål i Azure](howto-export-data.md).

JSON-filen som definierar enhets modellen använder den [digitala DTDL (Digital Definition Language) v2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).

Information om exempel på enhets kod som visar att vissa av dessa nytto laster används finns i själv studie kursen [skapa och ansluta ett klient program till Azure IoT Central Application](tutorial-connect-device.md) .

## <a name="view-raw-data"></a>Visa rå data

Med IoT Central kan du Visa rå data som en enhet skickar till ett program. Den här vyn är användbar för fel sökning av problem med nytto lasten som skickas från en enhet. Så här visar du de rå data som en enhet skickar:

1. Navigera till enheten från sidan **enheter** .

1. Välj fliken **rå data** :

    :::image type="content" source="media/concepts-telemetry-properties-commands/raw-data.png" alt-text="Vyn rå data":::

    I den här vyn kan du välja vilka kolumner som ska visas och ange ett tidsintervall som ska visas. I kolumnen för staplade **data** visas data från enheten som inte matchar några definitioner för egenskaper eller telemetri i enhets mal len.

## <a name="telemetry"></a>Telemetri

### <a name="primitive-types"></a>Primitiva typer

I det här avsnittet visas exempel på typer av primitiva telemetri som en enhet strömmar till ett IoT Central-program.

Följande kodfragment från en enhets modell visar definitionen av en `boolean` typ av telemetri:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "BooleanTelemetry"
  },
  "name": "BooleanTelemetry",
  "schema": "boolean"
}
```

En enhets klient ska skicka Telemetrin som JSON som ser ut som i följande exempel:

```json
{ "BooleanTelemetry": true }
```

Följande kodfragment från en enhets modell visar definitionen av en `string` typ av telemetri:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "StringTelemetry"
  },
  "name": "StringTelemetry",
  "schema": "string"
}
```

En enhets klient ska skicka Telemetrin som JSON som ser ut som i följande exempel:

```json
{ "StringTelemetry": "A string value - could be a URL" }
```

Följande kodfragment från en enhets modell visar definitionen av en `integer` typ av telemetri:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "IntegerTelemetry"
  },
  "name": "IntegerTelemetry",
  "schema": "integer"
}

```

En enhets klient ska skicka Telemetrin som JSON som ser ut som i följande exempel:

```json
{ "IntegerTelemetry": 23 }
```

Följande kodfragment från en enhets modell visar definitionen av en `double` typ av telemetri:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "DoubleTelemetry"
  },
  "name": "DoubleTelemetry",
  "schema": "double"
}
```

En enhets klient ska skicka Telemetrin som JSON som ser ut som i följande exempel:

```json
{ "DoubleTelemetry": 56.78 }
```

Följande kodfragment från en enhets modell visar definitionen av en `dateTime` typ av telemetri:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "DateTimeTelemetry"
  },
  "name": "DateTimeTelemetry",
  "schema": "dateTime"
}
```

En enhets klient ska skicka Telemetrin som JSON som ser ut som i följande exempel – `DateTime` typer måste vara i ISO 8061-format:

```json
{ "DateTimeTelemetry": "2020-08-30T19:16:13.853Z" }
```

Följande kodfragment från en enhets modell visar definitionen av en `duration` typ av telemetri:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "DurationTelemetry"
  },
  "name": "DurationTelemetry",
  "schema": "duration"
}
```

En enhets klient ska skicka Telemetrin som JSON som ser ut som i följande exempel – varaktigheten måste vara i ISO 8601-format:

```json
{ "DurationTelemetry": "PT10H24M6.169083011336625S" }
```

### <a name="complex-types"></a>Komplexa typer

Det här avsnittet innehåller exempel på komplexa typer av telemetri som en enhet strömmar till ett IoT Central-program.

Följande kodfragment från en enhets modell visar definitionen av en `geopoint` typ av telemetri:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "GeopointTelemetry"
  },
  "name": "GeopointTelemetry",
  "schema": "geopoint"
}
```

En enhets klient ska skicka Telemetrin som JSON som ser ut som i följande exempel. IoT Central visar värdet som en PIN-kod på en karta:

```json
{
  "GeopointTelemetry": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

Följande kodfragment från en enhets modell visar definitionen av en `Enum` typ av telemetri:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "EnumTelemetry"
  },
  "name": "EnumTelemetry",
  "schema": {
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

En enhets klient ska skicka Telemetrin som JSON som ser ut som i följande exempel. Möjliga värden är `0` , `1` och `2` som visas i IoT central som `Item1` , `Item2` och `Item3` :

```json
{ "EnumTelemetry": 1 }
```

Följande kodfragment från en enhets modell visar definitionen av en `Object` typ av telemetri. Det här objektet har tre fält med typerna `dateTime` , `integer` och `Enum` :

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "ObjectTelemetry"
  },
  "name": "ObjectTelemetry",
  "schema": {
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "displayName": {
          "en": "Property1"
        },
        "name": "Property1",
        "schema": "dateTime"
      },
      {
        "displayName": {
          "en": "Property2"
        },
        "name": "Property2",
        "schema": "integer"
      },
      {
        "displayName": {
          "en": "Property3"
        },
        "name": "Property3",
        "schema": {
          "@type": "Enum",
          "displayName": {
            "en": "Enum"
          },
          "valueSchema": "integer",
          "enumValues": [
            {
              "displayName": {
                "en": "Item1"
              },
              "enumValue": 0,
              "name": "Item1"
            },
            {
              "displayName": {
                "en": "Item2"
              },
              "enumValue": 1,
              "name": "Item2"
            },
            {
              "displayName": {
                "en": "Item3"
              },
              "enumValue": 2,
              "name": "Item3"
            }
          ]
        }
      }
    ]
  }
}
```

En enhets klient ska skicka Telemetrin som JSON som ser ut som i följande exempel. `DateTime` typerna måste vara ISO 8061-kompatibla. Möjliga värden för `Property3` är `0` , `1` och som visas i IoT central som `Item1` , `Item2` och `Item3` :

```json
{
  "ObjectTelemetry": {
      "Property1": "2020-09-09T03:36:46.195Z",
      "Property2": 37,
      "Property3": 2
  }
}
```

Följande kodfragment från en enhets modell visar definitionen av en `vector` typ av telemetri:

```json
{
  "@type": "Telemetry",
  "displayName": {
    "en": "VectorTelemetry"
  },
  "name": "VectorTelemetry",
  "schema": "vector"
}
```

En enhets klient ska skicka Telemetrin som JSON som ser ut som i följande exempel:

```json
{
  "VectorTelemetry": {
    "x": 74.72395045538597,
    "y": 74.72395045538597,
    "z": 74.72395045538597
  }
}
```

### <a name="event-and-state-types"></a>Händelse-och tillstånds typer

Det här avsnittet innehåller exempel på telemetri-händelser och tillstånd som en enhet skickar till ett IoT Central-program.

Följande kodfragment från en enhets modell visar definitionen av en `integer` händelse typ:

```json
{
  "@type": [
    "Telemetry",
    "Event"
  ],
  "displayName": {
    "en": "IntegerEvent"
  },
  "name": "IntegerEvent",
  "schema": "integer"
}
```

En enhets klient ska skicka händelse data som JSON som ser ut som i följande exempel:

```json
{ "IntegerEvent": 74 }
```

Följande kodfragment från en enhets modell visar definitionen av en `integer` tillstånds typ:

```json
{
  "@type": [
    "Telemetry",
    "State"
  ],
  "displayName": {
    "en": "IntegerState"
  },
  "name": "IntegerState",
  "schema": {
    "@type": "Enum",
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Level1"
        },
        "enumValue": 1,
        "name": "Level1"
      },
      {
        "displayName": {
          "en": "Level2"
        },
        "enumValue": 2,
        "name": "Level2"
      },
      {
        "displayName": {
          "en": "Level3"
        },
        "enumValue": 3,
        "name": "Level3"
      }
    ]
  }
}
```

En enhets klient ska skicka tillstånd som JSON som ser ut som i följande exempel. Möjliga heltals status värden är `1` , `2` eller `3` :

```json
{ "IntegerState": 2 }
```

## <a name="properties"></a>Egenskaper

> [!NOTE]
> Nytto Last formaten för egenskaper gäller för program som skapats på eller efter 07/14/2020.

### <a name="primitive-types"></a>Primitiva typer

Det här avsnittet innehåller exempel på primitiva egenskaps typer som en enhet skickar till ett IoT Central-program.

Följande kodfragment från en enhets modell visar definitionen av en `boolean` egenskaps typ:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "BooleanProperty"
  },
  "name": "BooleanProperty",
  "schema": "boolean",
  "writable": false
}
```

En enhets klient ska skicka en JSON-nyttolast som ser ut som i följande exempel som en rapporterad egenskap i enheten:

```json
{ "BooleanProperty": false }
```

Följande kodfragment från en enhets modell visar definitionen av en `boolean` egenskaps typ:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "LongProperty"
  },
  "name": "LongProperty",
  "schema": "long",
  "writable": false
}
```

En enhets klient ska skicka en JSON-nyttolast som ser ut som i följande exempel som en rapporterad egenskap i enheten:

```json
{ "LongProperty": 439 }
```

Följande kodfragment från en enhets modell visar definitionen av en `date` egenskaps typ:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "DateProperty"
  },
  "name": "DateProperty",
  "schema": "date",
  "writable": false
}
```

En enhets klient ska skicka en JSON-nyttolast som ser ut som i följande exempel som en rapporterad egenskap i enheten. `Date` typerna måste vara ISO 8061-kompatibla:

```json
{ "DateProperty": "2020-05-17" }
```

Följande kodfragment från en enhets modell visar definitionen av en `duration` egenskaps typ:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "DurationProperty"
  },
  "name": "DurationProperty",
  "schema": "duration",
  "writable": false
}
```

En enhets klient ska skicka en JSON-nyttolast som ser ut som i följande exempel som en rapporterad egenskap i enhetens dubbla varaktigheter måste vara ISO 8601 duration-kompatibel:

```json
{ "DurationProperty": "PT10H24M6.169083011336625S" }
```

Följande kodfragment från en enhets modell visar definitionen av en `float` egenskaps typ:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "FloatProperty"
  },
  "name": "FloatProperty",
  "schema": "float",
  "writable": false
}
```

En enhets klient ska skicka en JSON-nyttolast som ser ut som i följande exempel som en rapporterad egenskap i enheten:

```json
{ "FloatProperty": 1.9 }
```

Följande kodfragment från en enhets modell visar definitionen av en `string` egenskaps typ:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "StringProperty"
  },
  "name": "StringProperty",
  "schema": "string",
  "writable": false
}
```

En enhets klient ska skicka en JSON-nyttolast som ser ut som i följande exempel som en rapporterad egenskap i enheten:

```json
{ "StringProperty": "A string value - could be a URL" }
```

### <a name="complex-types"></a>Komplexa typer

Det här avsnittet innehåller exempel på komplexa egenskaps typer som en enhet skickar till ett IoT Central-program.

Följande kodfragment från en enhets modell visar definitionen av en `geopoint` egenskaps typ:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "GeopointProperty"
  },
  "name": "GeopointProperty",
  "schema": "geopoint",
  "writable": false
}
```

En enhets klient ska skicka en JSON-nyttolast som ser ut som i följande exempel som en rapporterad egenskap i enheten:

```json
{
  "GeopointProperty": {
    "lat": 47.64263,
    "lon": -122.13035,
    "alt": 0
  }
}
```

Följande kodfragment från en enhets modell visar definitionen av en `Enum` egenskaps typ:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "EnumProperty"
  },
  "name": "EnumProperty",
  "writable": false,
  "schema": {
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

En enhets klient ska skicka en JSON-nyttolast som ser ut som i följande exempel som en rapporterad egenskap i enheten. Möjliga värden är `0` , `1` och som visas i IoT central som `Item1` , `Item2` och `Item3` :

```json
{ "EnumProperty": 1 }
```

Följande kodfragment från en enhets modell visar definitionen av en `Object` egenskaps typ. Det här objektet har två fält med typer `string` och `integer` :

```json
{
  "@type": "Property",
  "displayName": {
    "en": "ObjectProperty"
  },
  "name": "ObjectProperty",
  "writable": false,
  "schema": {
    "@type": "Object",
    "displayName": {
      "en": "Object"
    },
    "fields": [
      {
        "displayName": {
          "en": "Field1"
        },
        "name": "Field1",
        "schema": "integer"
      },
      {
        "displayName": {
          "en": "Field2"
        },
        "name": "Field2",
        "schema": "string"
      }
    ]
  }
}
```

En enhets klient ska skicka en JSON-nyttolast som ser ut som i följande exempel som en rapporterad egenskap i enheten:

```json
{
  "ObjectProperty": {
    "Field1": 37,
    "Field2": "A string value"
  }
}
```

Följande kodfragment från en enhets modell visar definitionen av en `vector` egenskaps typ:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "VectorProperty"
  },
  "name": "VectorProperty",
  "schema": "vector",
  "writable": false
}
```

En enhets klient ska skicka en JSON-nyttolast som ser ut som i följande exempel som en rapporterad egenskap i enheten:

```json
{
  "VectorProperty": {
    "x": 74.72395045538597,
    "y": 74.72395045538597,
    "z": 74.72395045538597
  }
}
```

### <a name="writeable-property-types"></a>Skrivbara egenskaps typer

Det här avsnittet innehåller exempel på skrivbara egenskaps typer som en enhet tar emot från ett IoT Central-program.

IoT Central förväntar sig ett svar från enheten till skrivbara egenskaps uppdateringar. Svars meddelandet ska innehålla `ac` fälten och `av` . Fältet `ad` är valfritt. Se följande kodfragment för exempel.

`ac` är ett numeriskt fält som använder värdena i följande tabell:

| Värde | Etikett | Beskrivning |
| ----- | ----- | ----------- |
| `'ac': 200` | Slutförd | Egenskaps ändrings åtgärden har slutförts. |
| `'ac': 202`  eller `'ac': 201` | Väntar | Egenskaps ändrings åtgärden väntar eller pågår |
| `'ac': 4xx` | Fel | Den begärda egenskaps ändringen var inte giltig eller innehöll ett fel |
| `'ac': 5xx` | Fel | Ett oväntat fel uppstod i enheten vid bearbetning av den begärda ändringen. |

`av` är versions numret som skickas till enheten.

`ad` är en beskrivning av alternativ strängen.

Följande kodfragment från en enhets modell visar definitionen av en skrivbar `string` egenskaps typ:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "StringPropertyWritable"
  },
  "name": "StringPropertyWritable",
  "writable": true,
  "schema": "string"
}
```

Enheten får följande nytto Last från IoT Central:

```json
{  
  "StringPropertyWritable": "A string from IoT Central", "$version": 7
}
```

Enheten ska skicka följande JSON-nyttolast till IoT Central när uppdateringen har bearbetats. Det här meddelandet innehåller versions numret för den ursprungliga uppdateringen som togs emot från IoT Central. När IoT Central tar emot det här meddelandet markeras egenskapen som **synkroniserad** i användar gränssnittet:

```json
{
  "StringPropertyWritable": {
    "value": "A string from IoT Central",
    "ac": 200,
    "ad": "completed",
    "av": 7
  }
}
```

Följande kodfragment från en enhets modell visar definitionen av en skrivbar `Enum` egenskaps typ:

```json
{
  "@type": "Property",
  "displayName": {
    "en": "EnumPropertyWritable"
  },
  "name": "EnumPropertyWritable",
  "writable": true,
  "schema": {
    "@type": "Enum",
    "displayName": {
      "en": "Enum"
    },
    "valueSchema": "integer",
    "enumValues": [
      {
        "displayName": {
          "en": "Item1"
        },
        "enumValue": 0,
        "name": "Item1"
      },
      {
        "displayName": {
          "en": "Item2"
        },
        "enumValue": 1,
        "name": "Item2"
      },
      {
        "displayName": {
          "en": "Item3"
        },
        "enumValue": 2,
        "name": "Item3"
      }
    ]
  }
}
```

Enheten får följande nytto Last från IoT Central:

```json
{  
  "EnumPropertyWritable":  1 , "$version": 10
}
```

Enheten ska skicka följande JSON-nyttolast till IoT Central när uppdateringen har bearbetats. Det här meddelandet innehåller versions numret för den ursprungliga uppdateringen som togs emot från IoT Central. När IoT Central tar emot det här meddelandet markeras egenskapen som **synkroniserad** i användar gränssnittet:

```json
{
  "EnumPropertyWritable": {
    "value": 1,
    "ac": 200,
    "ad": "completed",
    "av": 10
  }
}
```

## <a name="commands"></a>Kommandon

Följande kodfragment från en enhets modell visar definitionen av ett kommando som inte har några parametrar och som inte förväntar sig att enheten ska returnera något:

```json
{
  "@type": "Command",
  "displayName": {
    "en": "CommandBasic"
  },
  "name": "CommandBasic"
}
```

Enheten får en tom nytto Last i begäran och ska returnera en tom nytto Last i svaret med en `200` http-svarskod för att indikera att det lyckades.

Följande kodfragment från en enhets modell visar definitionen av ett kommando som har en heltals parameter och som förväntar sig att enheten returnerar ett heltals värde:

```json
{
  "@type": "Command",
  "request": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": "integer"
  },
  "response": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": "integer"
  },
  "displayName": {
    "en": "CommandSimple"
  },
  "name": "CommandSimple"
}
```

Enheten får ett heltals värde som begär ande nytto Last. Enheten ska returnera ett heltals värde som svars nytto last med en `200` http-svarskod för att indikera att det lyckades.

Följande kodfragment från en enhets modell visar definitionen av ett kommando som har en objekt parameter och som förväntar sig att enheten returnerar ett objekt. I det här exemplet har båda objekten heltals-och sträng fält:

```json
{
  "@type": "Command",
  "request": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": {
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "displayName": {
            "en": "Field1"
          },
          "name": "Field1",
          "schema": "integer"
        },
        {
          "displayName": {
            "en": "Field2"
          },
          "name": "Field2",
          "schema": "string"
        }
      ]
    }
  },
  "response": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": {
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "displayName": {
            "en": "Field1"
          },
          "name": "Field1",
          "schema": "integer"
        },
        {
          "displayName": {
            "en": "Field2"
          },
          "name": "Field2",
          "schema": "string"
        }
      ]
    }
  },
  "displayName": {
    "en": "CommandComplex"
  },
  "name": "CommandComplex"
}
```

Följande fragment visar ett exempel på begäran om nytto last som skickats till enheten:

```json
{ "Field1": 56, "Field2": "A string value" }
```

I följande kodfragment visas ett exempel på en nytto last för svar som skickas från enheten. Använd en `200` http-svarskod för att indikera att det lyckades:

```json
{ "Field1": 87, "Field2": "Another string value" }
```

### <a name="long-running-commands"></a>Tids krävande kommandon

Följande kodfragment från en enhets modell visar definitionen av ett kommando. Kommandot har en heltals parameter och förväntar sig att enheten returnerar ett heltals värde:

```json
{
  "@type": "Command",
  "request": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "RequestParam"
    },
    "name": "RequestParam",
    "schema": "integer"
  },
  "response": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "ResponseParam"
    },
    "name": "ResponseParam",
    "schema": "integer"
  },
  "displayName": {
    "en": "LongRunningCommandSimple"
  },
  "name": "LongRunningCommandSimple"
}
```

Enheten får ett heltals värde som begär ande nytto Last. Om enheten behöver tid för att bearbeta det här kommandot bör den returnera en tom svars nytto last med en `202` http-svarskod för att indikera att enheten har accepterat begäran om bearbetning.

När enheten har bearbetat bearbetningen av begäran ska den skicka en egenskap till IoT Central som ser ut som i följande exempel. Egenskaps namnet måste vara detsamma som kommando namnet:

```json
{
  "LongRunningCommandSimple": {
    "value": 87
  }
}
```

### <a name="offline-commands"></a>Offline-kommandon

I IoT Central-webbgränssnittet kan du välja **kön om alternativet offline** ska användas för ett kommando. Offline-kommandon är enkelriktade meddelanden till enheten från din lösning som levereras så snart en enhet ansluter. Offline-kommandon kan ha parametrar för begäran men returnerar inte något svar.

Inställningen för **kö om offline** tas inte med om du exporterar en modell eller ett gränssnitt från enhets mal len. Du kan inte se en exporterad modell eller gränssnitts-JSON som ett kommando är ett offline-kommando.

Offline-kommandon använder [IoT Hub meddelanden från molnet till enheten](../../iot-hub/iot-hub-devguide-messages-c2d.md) för att skicka kommandot och nytto lasten till enheten.

Följande kodfragment från en enhets modell visar definitionen av ett kommando. Kommandot har en objekt parameter med ett datetime-fält och en uppräkning:

```json
{
  "@type": "Command",
  "displayName": {
    "en": "Generate Diagnostics"
  },
  "name": "GenerateDiagnostics",
  "request": {
    "@type": "CommandPayload",
    "displayName": {
      "en": "Payload"
    },
    "name": "Payload",
    "schema": {
      "@type": "Object",
      "displayName": {
        "en": "Object"
      },
      "fields": [
        {
          "displayName": {
            "en": "StartTime"
          },
          "name": "StartTime",
          "schema": "dateTime"
        },
        {
          "displayName": {
            "en": "Bank"
          },
          "name": "Bank",
          "schema": {
            "@type": "Enum",
            "displayName": {
              "en": "Enum"
            },
            "enumValues": [
              {
                "displayName": {
                  "en": "Bank 1"
                },
                "enumValue": 1,
                "name": "Bank1"
              },
              {
                "displayName": {
                  "en": "Bank2"
                },
                "enumValue": 2,
                "name": "Bank2"
              },
              {
                "displayName": {
                  "en": "Bank3"
                },
                "enumValue": 2,
                "name": "Bank3"
              }
            ],
            "valueSchema": "integer"
          }
        }
      ]
    }
  }
}
```

Om du aktiverar alternativet **kö om offline** i användar gränssnittet för enhets mal len för kommandot i föregående kodfragment, innehåller meddelandet som enheten tar emot följande egenskaper:

| Egenskapsnamn | Exempelvärde |
| ---------- | ----- |
| `custom_properties` | `{'method-name': 'GenerateDiagnostics'}` |
| `data` | `{"StartTime":"2021-01-05T08:00:00.000Z","Bank":2}` |

## <a name="next-steps"></a>Nästa steg

Som enhets utvecklare är det nu viktigt att du har lärt dig mer om enhets mallar genom att [IoT Central](./concepts-get-connected.md) läsa mer om hur du registrerar enheter med IoT Central och hur IoT Central skyddar enhets anslutningar.
