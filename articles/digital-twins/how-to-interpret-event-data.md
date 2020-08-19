---
title: Tolka händelsedata
titleSuffix: Azure Digital Twins
description: Se hur du tolkar olika händelse typer och deras olika meddelanden.
author: baanders
ms.author: baanders
ms.date: 6/23/2020
ms.topic: how-to
ms.service: digital-twins
ms.openlocfilehash: 10b74f7b795df2cf8c19d044fce44da3f798af7a
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88587641"
---
# <a name="understand-event-data"></a>Förstå händelse data

Olika händelser i digitala Azure-meddelanden genererar **aviseringar**, vilket gör att lösningens Server del kan vara medveten när olika åtgärder sker. De [dirigeras](concepts-route-events.md) sedan till olika platser inuti och utanför Azures digitala dubbla, som kan använda den här informationen för att vidta åtgärder.

Det finns flera typer av meddelanden som kan genereras, och meddelanden kan se olika ut beroende på vilken typ av händelse som genererade dem. Den här artikeln innehåller information om olika typer av meddelanden och hur de kan se ut.

Det här diagrammet visar olika meddelande typer:

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

I allmänhet består meddelanden av två delar: sidhuvud och brödtext. 

### <a name="event-notification-headers"></a>Händelse meddelande rubriker

Meddelande rubriker representeras med nyckel/värde-par. Beroende på vilket protokoll som används (MQTT, AMQP eller HTTP) kommer meddelandehuvuden att serialiseras på olika sätt. I det här avsnittet beskrivs allmän rubrik information för aviserings meddelanden oavsett vilket protokoll och vilka serialiseringar som valts.

Vissa meddelanden uppfyller [CloudEvents](https://cloudevents.io/) -standarden. CloudEvents-överensstämmelse är som följer.
* Meddelanden som släpps från enheter fortsätter att följa de befintliga specifikationerna för meddelanden
* Meddelanden som bearbetas och genereras av IoT Hub fortsätta att följa de befintliga specifikationerna för meddelanden, förutom där IoT Hub väljer att stödja CloudEvents, till exempel via Event Grid
* Meddelanden som släpps från [digitala garn](concepts-twins-graph.md) med en [modell](concepts-models.md) som överensstämmer med CloudEvents
* Meddelanden som bearbetas och genereras av Azure Digitals dubblare följer CloudEvents

Tjänster måste lägga till ett sekvensnummer i alla meddelanden för att indikera deras ordning eller upprätthålla sin egen ordning på något annat sätt. 

Meddelanden som skickas av digitala Digital-meddelanden till Event Grid automatiskt formateras till antingen CloudEvents-schemat eller EventGridEvent-schemat, beroende på vilken schema typ som definierats i avsnittet Event Grid. 

Tilläggets attribut för rubriker läggs till som egenskaper i Event Grid-schemat i nytto lasten. 

### <a name="event-notification-bodies"></a>Händelse meddelande texter

Organen i aviserings meddelanden beskrivs här i JSON. Beroende på vilken serialisering som önskas för meddelande texten (till exempel med JSON, CBOR, protobuf osv.) kan meddelande texten serialiseras på olika sätt.

Uppsättningen fält som texten innehåller varierar beroende på olika meddelande typer. Här är två exempel på meddelande texter, för att få en uppfattning om vad de vanligt vis ser ut och kan innehålla.

Telemetri-meddelande:

```json
{
  "specversion": "1.0",
  "id": "df5a5992-817b-4e8a-b12c-e0b18d4bf8fb",
  "type": "microsoft.iot.telemetry",
  "source": "contoso-adt.api.wus2.digitaltwins.azure.net/digitaltwins/room1",
  "data": {
    "Temperature": 10
  },
  "dataschema": "dtmi:example:com:floor4;2",
  "datacontenttype": "application/json",
  "traceparent": "00-7e3081c6d3edfb4eaf7d3244b2036baa-23d762f4d9f81741-01"
}
```

Meddelande om livs cykel meddelande:

```json
{
  "specversion": "1.0",
  "id": "d047e992-dddc-4a5a-b0af-fa79832235f8",
  "type": "Microsoft.DigitalTwins.Twin.Create",
  "source": "contoso-adt.api.wus2.digitaltwins.azure.net",
  "data": {
    "$dtId": "floor1",
    "$etag": "W/\"e398dbf4-8214-4483-9d52-880b61e491ec\"",
    "$metadata": {
      "$model": "dtmi:example:Floor;1"
    }
  },
  "subject": "floor1",
  "time": "2020-06-23T19:03:48.9700792Z",
  "datacontenttype": "application/json",
  "traceparent": "00-18f4e34b3e4a784aadf5913917537e7d-691a71e0a220d642-01"
}
```

## <a name="message-format-detail-for-different-event-types"></a>Meddelande format information för olika händelse typer

I det här avsnittet får du mer information om de olika typerna av meddelanden som genereras av IoT Hub och Azure Digitals dubbla (eller andra Azure IoT-tjänster). Du kommer att läsa om de saker som utlöser varje meddelande typ och de fält som ingår i varje typ av meddelande text.

### <a name="digital-twin-life-cycle-notifications"></a>Digitala dubbla livs cykel meddelanden

Alla [digitala](concepts-twins-graph.md) meddelanden om generering av digitala meddelanden, oavsett om de representerar [IoT Hub enheter i Azure Digitals](how-to-ingest-iot-hub-data.md) eller inte. Detta beror på ett **livs cykel meddelande**, som är cirka det digitala företaget.

Meddelanden om livs cykel utlöses när:
* En Digitals fläta skapas
* En Digitals fläta tas bort

#### <a name="properties"></a>Egenskaper

Här är fälten i bröd texten i ett meddelande om livs cykel.

| Name | Värde |
| --- | --- |
| `id` | Identifierare för meddelandet, till exempel ett UUID eller en räknare som underhålls av tjänsten. `source` + `id` är unikt för varje distinkt händelse. |
| `source` | Namnet på IoT Hub-eller Azure Digital-instansen, t. ex. *myhub.Azure-Devices.net* eller *mydigitaltwins.westus2.azuredigitaltwins.net* |
| `specversion` | *1,0*<br>Meddelandet överensstämmer med den här versionen av CloudEvents- [specifikationen](https://github.com/cloudevents/spec). |
| `type` | `Microsoft.DigitalTwins.Twin.Create`<br>`Microsoft.DigitalTwins.Twin.Delete` |
| `datacontenttype` | `application/json` |
| `subject` | ID för den digitala dubbla |
| `time` | Tidsstämpel för när åtgärden utfördes på den dubbla |
| `traceparent` | En W3C-spårnings kontext för händelsen |

#### <a name="body-details"></a>Information om brödtext

Texten är den digitala dubbla, som visas i JSON-format. Schemat för det här är *Digitals sammanflätade resurs 7,1*.

För att skapa händelser visar nytto lasten statusen för den dubbla efter att resursen har skapats, så den bör innehålla alla systemgenererade element precis som ett `GET` anrop.

Här är ett exempel på en brödtext för en [IoT plug and Play-enhet (PNP)](../iot-pnp/overview-iot-plug-and-play.md) , med komponenter och inga egenskaper på den högsta nivån. Egenskaper som inte passar för enheter (t. ex. rapporterade egenskaper) ska utelämnas.

```json
{
  "$dtId": "device-digitaltwin-01",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "thermostat": {
    "temperature": 80,
    "humidity": 45,
    "$metadata": {
      "$model": "dtmi:com:contoso:Thermostat;1",
      "temperature": {
        "desiredValue": 85,
        "desiredVersion": 3,
        "ackVersion": 2,
        "ackCode": 200,
        "ackDescription": "OK"
      },
      "humidity": {
        "desiredValue": 40,
        "desiredVersion": 1,
        "ackVersion": 1,
        "ackCode": 200,
        "ackDescription": "OK"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:contoso:Thermostat_X500;1",
  }
}
```

Här är ett annat exempel på en digital, dubbel. Den här baseras på en [modell](concepts-models.md)och stöder inte komponenter:

```json
{
  "$dtId": "logical-digitaltwin-01",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "avgTemperature": 70,
  "comfortIndex": 85,
  "$metadata": {
    "$model": "dtmi:com:contoso:Building;1",
    "avgTemperature": {
      "desiredValue": 72,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "comfortIndex": {
      "desiredValue": 90,
      "desiredVersion": 1,
      "ackVersion": 3,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

### <a name="digital-twin-relationship-change-notifications"></a>Digitala meddelanden om ändring av dubbla relationer

**Aviseringar om Relations ändringar** utlöses när en relation av en Digitals Tor skapas, uppdateras eller tas bort. 

#### <a name="properties"></a>Egenskaper

Här är fälten i bröd texten för en Edge Change-avisering.

| Name    | Värde |
| --- | --- |
| `id` | Identifierare för meddelandet, till exempel ett UUID eller en räknare som underhålls av tjänsten. `source` + `id` är unikt för varje distinkt händelse |
| `source` | Namnet på den digitala Azure-instansen, till exempel *mydigitaltwins.westus2.azuredigitaltwins.net* |
| `specversion` | *1,0*<br>Meddelandet överensstämmer med den här versionen av CloudEvents- [specifikationen](https://github.com/cloudevents/spec). |
| `type` | `Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br>`Microsoft.DigitalTwins.Relationship.Delete`
|`datacontenttype`| `application/json` |
| `subject` | ID för relationen, t. ex. `<twinID>/relationships/<relationshipID>` |
| `time` | Tidsstämpel för när åtgärden utfördes för relationen |
| `traceparent` | En W3C-spårnings kontext för händelsen |

#### <a name="body-details"></a>Information om brödtext

Texten är nytto lasten i en relation, även i JSON-format. Den använder samma format som en `GET` begäran om en relation via DigitalTwins- [API: et](how-to-use-apis-sdks.md). 

"Uppdatering av en relation" innebär att egenskaper för relationen har ändrats. 

Här är ett exempel på ett uppdaterings Relations meddelande för att uppdatera en egenskap:

```json
{
    "modelId": "dtmi:example:Floor;1",
    "patch": [
      {
        "value": "user3",
        "path": "/ownershipUser",
        "op": "replace"
      }
    ]
  }
```

För `Relationship.Delete` är texten samma som `GET` begäran, och den hämtar det senaste läget innan det tas bort.

Här är ett exempel på ett meddelande om att skapa eller ta bort relationer:

```json
{
    "$relationshipId": "device_to_device",
    "$etag": "W/\"72479873-0083-41a8-83e2-caedb932d881\"",
    "$relationshipName": "Connected",
    "$targetId": "device2",
    "connectionType": "WIFI"
}
```

### <a name="digital-twin-change-notifications"></a>Digitala dubbla ändrings meddelanden

**Digitala dubbla ändrings meddelanden** utlöses när en Digitals Tor håller på att uppdateras, t. ex.:
* När egenskaps värden eller metadata ändras.
* När digitala dubbla eller komponent-metadata ändras. Ett exempel på det här scenariot är att ändra modellen för en digital, dubbel.

#### <a name="properties"></a>Egenskaper

Här är fälten i bröd texten i ett digitalt meddelande om ändring av dubbla meddelanden.

| Name    | Värde |
| --- | --- |
| `id` | Identifierare för meddelandet, till exempel ett UUID eller en räknare som underhålls av tjänsten. `source` + `id` är unikt för varje distinkt händelse |
| `source` | Namnet på IoT Hub-eller Azure Digital-instansen, t. ex. *myhub.Azure-Devices.net* eller *mydigitaltwins.westus2.azuredigitaltwins.net*
| `specversion` | *1,0*<br>Meddelandet överensstämmer med den här versionen av CloudEvents- [specifikationen](https://github.com/cloudevents/spec). |
| `type` | `Microsoft.DigitalTwins.Twin.Update` |
| `datacontenttype` | `application/json` |
| `subject` | ID för den digitala dubbla |
| `time` | Tidsstämpel för när åtgärden utfördes på den digitala, dubbla |
| `traceparent` | En W3C-spårnings kontext för händelsen |

#### <a name="body-details"></a>Information om brödtext

Bröd texten för `Twin.Update` meddelandet är ett JSON-dokument som innehåller uppdateringen av den digitala, dubbla.

Anta till exempel att en digital, dubbel har uppdaterats med följande korrigering.

```json
[
    {
        "op": "replace",
        "value": 40,
        "path": "/Temperature"
    },
    {
        "op": "add",
        "value": 30,
        "path": "/comp1/prop1"
    }
]
```

Motsvarande meddelande (om synkront utförd av tjänsten, t. ex. Azure Digitals, som uppdaterar en digital enhet), har en brödtext som:

```json
{
    "modelId": "dtmi:example:com:floor4;2",
    "patch": [
      {
        "value": 40,
        "path": "/Temperature",
        "op": "replace"
      },
      {
        "value": 30,
        "path": "/comp1/prop1",
        "op": "add"
      }
    ]
  }
```

## <a name="next-steps"></a>Nästa steg

Se så här skapar du slut punkter och vägar för att leverera händelser:
* [*Anvisningar: hantera slut punkter och vägar*](how-to-manage-routes-apis-cli.md)

Du kan också läsa mer om Azure Digitals dubbla API: er och SDK-alternativ:
* [*Anvisningar: använda Azures digitala dubbla API: er och SDK: er*](how-to-use-apis-sdks.md)