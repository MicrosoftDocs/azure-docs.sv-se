---
title: Händelseaviseringar
titleSuffix: Azure Digital Twins
description: Se hur du tolkar olika händelse typer och deras olika meddelanden.
author: baanders
ms.author: baanders
ms.date: 4/8/2021
ms.topic: conceptual
ms.service: digital-twins
ms.custom: contperf-fy21q4
ms.openlocfilehash: 42842b00120b7e918ca5b790cce92a74ab1b99d5
ms.sourcegitcommit: 20f8bf22d621a34df5374ddf0cd324d3a762d46d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/09/2021
ms.locfileid: "107260014"
---
# <a name="event-notifications"></a>Händelseaviseringar

Olika händelser i digitala Azure-meddelanden genererar **aviseringar**, vilket gör att lösningens Server del kan vara medveten när olika åtgärder sker. De [dirigeras](concepts-route-events.md) sedan till olika platser inuti och utanför Azures digitala dubbla, som kan använda den här informationen för att vidta åtgärder.

Det finns flera typer av meddelanden som kan genereras, och meddelanden kan se olika ut beroende på vilken typ av händelse som genererade dem. Den här artikeln innehåller information om olika typer av meddelanden och hur de kan se ut.

Det här diagrammet visar olika meddelande typer:

[!INCLUDE [digital-twins-notifications.md](../../includes/digital-twins-notifications.md)]

## <a name="notification-structure"></a>Meddelande struktur

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

Uppsättningen fält som texten innehåller varierar beroende på olika meddelande typer.

I följande avsnitt finns mer information om de olika typerna av meddelanden som skickas av IoT Hub och Azure Digitals dubbla (eller andra Azure IoT-tjänster). Du kommer att läsa om de saker som utlöser varje meddelande typ och de fält som ingår i varje typ av meddelande text.

## <a name="digital-twin-change-notifications"></a>Digitala dubbla ändrings meddelanden

**Digitala dubbla ändrings meddelanden** utlöses när en Digitals Tor håller på att uppdateras, t. ex.:
* När egenskaps värden eller metadata ändras.
* När digitala dubbla eller komponent-metadata ändras. Ett exempel på det här scenariot är att ändra modellen för en digital, dubbel.

### <a name="properties"></a>Egenskaper

Här är fälten i bröd texten i ett digitalt meddelande om ändring av dubbla meddelanden.

| Name    | Värde |
| --- | --- |
| `id` | Identifierare för meddelandet, till exempel ett UUID eller en räknare som underhålls av tjänsten. `source` + `id` är unikt för varje distinkt händelse |
| `source` | Namnet på IoT Hub-eller Azure Digital-instansen, t. ex. *myhub.Azure-Devices.net* eller *mydigitaltwins.westus2.azuredigitaltwins.net* |
| `data` | En JSON-korrigerings handling som beskriver uppdateringen som gjorts till den dubbla. Mer information finns i [information om brödtext](#body-details) nedan. |
| `specversion` | *1.0*<br>Meddelandet överensstämmer med den här versionen av CloudEvents- [specifikationen](https://github.com/cloudevents/spec). |
| `type` | `Microsoft.DigitalTwins.Twin.Update` |
| `datacontenttype` | `application/json` |
| `subject` | ID för den digitala dubbla |
| `time` | Tidsstämpel för när åtgärden utfördes på den digitala, dubbla |
| `traceparent` | En W3C-spårnings kontext för händelsen |

### <a name="body-details"></a>Information om brödtext

I meddelandet `data` innehåller fältet ett JSON-korrigerings dokument som innehåller uppdateringen till den digitala dubbla.

Anta till exempel att en digital, dubbel har uppdaterats med följande korrigering.

:::code language="json" source="~/digital-twins-docs-samples/models/patch-component-2.json":::

Data i motsvarande meddelande (om synkront utförda av tjänsten, t. ex. Azure Digitals, som uppdaterar en digital enhet), har en brödtext som:

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

Detta är den information som ska visas i `data` fältet i livs cykel meddelandet.

## <a name="digital-twin-lifecycle-notifications"></a>Digitala, dubbla livs cykel meddelanden

Alla [digitala](concepts-twins-graph.md) meddelanden om generering av digitala meddelanden, oavsett om de representerar [IoT Hub enheter i Azure Digitals](how-to-ingest-iot-hub-data.md) eller inte. Detta beror på **livs cykel meddelanden**, som är ungefär digitala.

Livs cykel meddelanden utlöses när:
* En Digitals fläta skapas
* En Digitals fläta tas bort

### <a name="properties"></a>Egenskaper

Här är fälten i bröd texten i ett meddelande i livs cykeln.

| Name | Värde |
| --- | --- |
| `id` | Identifierare för meddelandet, till exempel ett UUID eller en räknare som underhålls av tjänsten. `source` + `id` är unikt för varje distinkt händelse. |
| `source` | Namnet på IoT Hub-eller Azure Digital-instansen, t. ex. *myhub.Azure-Devices.net* eller *mydigitaltwins.westus2.azuredigitaltwins.net* |
| `data` | Data från den dubbla som upplever livs cykel händelsen. Mer information finns i [information om brödtext](#body-details-1) nedan. |
| `specversion` | *1.0*<br>Meddelandet överensstämmer med den här versionen av CloudEvents- [specifikationen](https://github.com/cloudevents/spec). |
| `type` | `Microsoft.DigitalTwins.Twin.Create`<br>`Microsoft.DigitalTwins.Twin.Delete` |
| `datacontenttype` | `application/json` |
| `subject` | ID för den digitala dubbla |
| `time` | Tidsstämpel för när åtgärden utfördes på den dubbla |
| `traceparent` | En W3C-spårnings kontext för händelsen |

### <a name="body-details"></a>Information om brödtext

Här är ett exempel på ett meddelande om livs cykel meddelande: 

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

I meddelandet `data` innehåller fältet data för den aktuella digitala, dubbla, som visas i JSON-format. Schemat för det här är *Digitals sammanflätade resurs 7,1*.

För att skapa händelser `data` visar nytto lasten statusen för den dubbla efter att resursen har skapats, så den bör innehålla alla systemgenererade element precis som ett `GET` anrop.

Här är ett exempel på en data för en [IoT plug and Play-enhet (PNP)](../iot-pnp/overview-iot-plug-and-play.md) , med komponenter och inga egenskaper på den högsta nivån. Egenskaper som inte passar för enheter (t. ex. rapporterade egenskaper) ska utelämnas. Detta är den information som ska visas i `data` fältet i livs cykel meddelandet.

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

Här är ett annat exempel på digitala dubbla data. Den här baseras på en [modell](concepts-models.md)och stöder inte komponenter:

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

## <a name="digital-twin-relationship-change-notifications"></a>Digitala meddelanden om ändring av dubbla relationer

**Aviseringar om Relations ändringar** utlöses när en relation av en Digitals Tor skapas, uppdateras eller tas bort. 

### <a name="properties"></a>Egenskaper

Här är fälten i bröd texten i en meddelande om Relations ändring.

| Name    | Värde |
| --- | --- |
| `id` | Identifierare för meddelandet, till exempel ett UUID eller en räknare som underhålls av tjänsten. `source` + `id` är unikt för varje distinkt händelse |
| `source` | Namnet på den digitala Azure-instansen, till exempel *mydigitaltwins.westus2.azuredigitaltwins.net* |
| `data` | Nytto lasten för relationen som ändrades. Mer information finns i [information om brödtext](#body-details-2) nedan. |
| `specversion` | *1.0*<br>Meddelandet överensstämmer med den här versionen av CloudEvents- [specifikationen](https://github.com/cloudevents/spec). |
| `type` | `Microsoft.DigitalTwins.Relationship.Create`<br>`Microsoft.DigitalTwins.Relationship.Update`<br>`Microsoft.DigitalTwins.Relationship.Delete` |
| `datacontenttype` | `application/json` |
| `subject` | ID för relationen, t. ex. `<twinID>/relationships/<relationshipID>` |
| `time` | Tidsstämpel för när åtgärden utfördes för relationen |
| `traceparent` | En W3C-spårnings kontext för händelsen |

### <a name="body-details"></a>Information om brödtext

I meddelandet `data` innehåller fältet nytto lasten för en relation i JSON-format. Den använder samma format som en `GET` begäran om en relation via DigitalTwins- [API: et](/rest/api/digital-twins/dataplane/twins). 

Här är ett exempel på data för ett uppdaterings Relations meddelande. "Uppdatering av en relation" innebär att egenskaper för relationen har ändrats, så att data visar den uppdaterade egenskapen och dess nya värde. Detta är den information som ska användas i `data` fältet i aviserings meddelandet Digital, dubbel relation.

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

Här är ett exempel på data för ett meddelande om att skapa eller ta bort relationer. För `Relationship.Delete` är texten samma som `GET` begäran, och den hämtar det senaste läget innan det tas bort.

```json
{
    "$relationshipId": "device_to_device",
    "$etag": "W/\"72479873-0083-41a8-83e2-caedb932d881\"",
    "$relationshipName": "Connected",
    "$targetId": "device2",
    "connectionType": "WIFI"
}
```

## <a name="digital-twin-telemetry-messages"></a>Digitala meddelanden med dubbla telemetri

**Telemetri meddelanden** tas emot i Azure Digitals sammanflätar från anslutna enheter som samlar in och skickar mått.

### <a name="properties"></a>Egenskaper

Här är fälten i bröd texten i ett telemetri-meddelande.

| Name    | Värde |
| --- | --- |
| `id` | Identifierare för meddelandet, som tillhandahålls av kunden när du anropar telemetri-API: et. |
| `source` | Fullständigt kvalificerat namn på den dubbla som telemetri-händelsen skickades till. I används följande format: `<yourDigitalTwinInstance>.api.<yourRegion>.digitaltwins.azure.net/<twinId>` . |
| `specversion` | *1.0*<br>Meddelandet överensstämmer med den här versionen av CloudEvents- [specifikationen](https://github.com/cloudevents/spec). |
| `type` | `microsoft.iot.telemetry` |
| `data` | Det telemetri meddelande som har skickats till dubbla. Nytto lasten är oförändrad och kan inte justeras med det dubbla schema som har skickats till Telemetrin. |
| `dataschema` | Dataschemat är modell-ID: t för den dubbla eller komponenten som utvärderar Telemetrin. Till exempel `dtmi:example:com:floor4;2`. |
| `datacontenttype` | `application/json` |
| `traceparent` | En W3C-spårnings kontext för händelsen. |

### <a name="body-details"></a>Information om brödtext

Texten innehåller telemetri-mått tillsammans med viss sammanhangsbaserad information om enheten.

Här är ett exempel på ett meddelande om telemetri: 

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

## <a name="next-steps"></a>Nästa steg

Lär dig mer om att leverera händelser till olika mål med hjälp av slut punkter och vägar:
* [*Koncept: händelse vägar*](concepts-route-events.md)
