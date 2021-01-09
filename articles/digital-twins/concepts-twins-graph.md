---
title: Digitala tvillingenheter och tvillingdiagrammet
titleSuffix: Azure Digital Twins
description: Förstå begreppet digital, och hur deras relationer gör en graf.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: d9a6eb572b1ab870fdb848f8b0989f88e6dbc3c0
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98045962"
---
# <a name="understand-digital-twins-and-their-twin-graph"></a>Förstå digitala sammanflätade och deras dubbla diagram

I en Azure digital-lösning för dubbla lösningar representeras entiteterna i din miljö av Azures **digitala dubbla**. En digital, dubbel är en instans av en av dina anpassade [modeller](concepts-models.md). Det kan anslutas till andra digitala dubbla nätverk via **relationer** för att bilda ett sammanslaget **diagram**: det här dubbla diagrammet är en representation av hela miljön.

> [!TIP]
> "Azure Digital-dubbla" syftar på den här Azure-tjänsten som helhet. "Digitals dubbla (s)" eller "dubbla (s)" syftar på enskilda dubbla noder i din instans av tjänsten.

## <a name="digital-twins"></a>Digitala tvillingenheter

Innan du kan skapa en digital i din Azure Digitals-instans måste du ha en *modell* som överförs till tjänsten. En modell beskriver uppsättningen egenskaper, telemetri-meddelanden och relationer som en viss, t. ex. kan ha, bland annat. Information om vilka typer av information som definieras i en modell finns i [*begrepp: anpassade modeller*](concepts-models.md).

När du har skapat och laddat upp en modell kan ditt klient program skapa en instans av typen. Detta är en digital, dubbel. När du har skapat en *vånings* modell kan du till exempel skapa en eller flera digitala garn som använder den här typen (t. ex. en typ av *golv* som kallas *GroundFloor*, en annan som kallas *Floor2* osv.). 

## <a name="relationships-a-graph-of-digital-twins"></a>Relationer: ett diagram över digitala dubbla

Dubbla är anslutna till ett dubbel diagram av deras relationer. Relationerna som en snöre kan ha definieras som en del av modellen.  

Modell *ytan* kan till exempel definiera en *contains* -relation som är riktad mot varandra av typen *Room*. Med den här definitionen kan du med hjälp av Azure Digitals dubbla sammanhållen *skapa relationer* från valfri *våning* till *rummets* dubbla (inklusive dubbla som är av *Room* -undertyper). 

Resultatet av den här processen är en uppsättning noder (de digitala dubbla) som är anslutna via gränser (deras relationer) i ett diagram.

[!INCLUDE [visualizing with Azure Digital Twins explorer](../../includes/digital-twins-visualization.md)]

## <a name="create-with-the-apis"></a>Skapa med API: er

Det här avsnittet visar hur det ser ut för att skapa digitala dubbla objekt och relationer från ett klient program. Den innehåller exempel på .NET-kod som använder [DigitalTwins-API: er](/rest/api/digital-twins/dataplane/twins)för att ge ytterligare kontext för vad som händer i var och en av dessa begrepp.

### <a name="create-digital-twins"></a>Skapa digitala dubbla

Nedan visas ett fragment med klient koden som använder [DigitalTwins-API: er](/rest/api/digital-twins/dataplane/twins) för att instansiera ett garn av typen *Room*.

Du kan initiera egenskaperna för en dubbel när den har skapats, eller ange dem senare. Om du vill skapa en dubbla med initierade egenskaper skapar du ett JSON-dokument som innehåller de nödvändiga initierings värdena.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/twin_operations_other.cs" id="CreateTwin_noHelper":::

Du kan också använda en hjälp klass som kallas `BasicDigitalTwin` för att lagra egenskaps fält i ett "" dubbla "objekt mer direkt, som ett alternativ till att använda en ord lista. Mer information om hjälp klassen och exempel på hur det används finns i avsnittet [*skapa en digital*](how-to-manage-twin.md#create-a-digital-twin) enhet med *anvisningar: hantera digitala dubbla*.

>[!NOTE]
>Även om dubbla egenskaper behandlas som valfria och därför inte behöver initieras, måste alla [komponenter](concepts-models.md#elements-of-a-model) på **den dubbla anges** när den skapas. De kan vara tomma objekt, men själva komponenterna måste finnas.

### <a name="create-relationships"></a>Skapa relationer

Här är ett exempel på en klient kod som använder [DigitalTwins-API: er](/rest/api/digital-twins/dataplane/twins) för att bygga en relation mellan en *vånings* typ, Digital, som kallas *GroundFloor* och en av *rums* typen digital, som kallas *Cafe*.

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/graph_operations_other.cs" id="CreateRelationship_3":::

## <a name="json-representations-of-graph-elements"></a>JSON-representationer av diagram element

Digitala dubbla data och Relations data lagras både i JSON-format. Det innebär att när du [frågar det dubbla diagrammet](how-to-query-graph.md) i din Azure Digital-instansen blir resultatet en JSON-representation av digitala dubbla och relationer som du har skapat.

### <a name="digital-twin-json-format"></a>Digitalt dubbla JSON-format

När ett digitalt objekt visas som ett JSON-objekt visas följande fält:

| Fältnamn | Beskrivning |
| --- | --- |
| `$dtId` | En användardefinierad sträng som representerar ID: t för den digitala dubbla |
| `$etag` | Standard-HTTP-fält som tilldelas av webb servern |
| `$conformance` | En uppräkning som innehåller proformat-tillståndet för denna digitala garn (*överensstämmande*, *icke-överensstämmande*, *okänd*) |
| `{propertyName}` | Värdet för en egenskap i JSON ( `string` , tal typ eller objekt) |
| `$relationships` | URL till sökvägen till Relations samlingen. Det här fältet saknas om det digitala området inte har några utgående Relations kanter. |
| `$metadata.$model` | Valfritt ID: t för det modell gränssnitt som kännetecknar detta digitala dubbla |
| `$metadata.{propertyName}.desiredValue` | [Endast för skrivbara egenskaper] Det önskade värdet för den angivna egenskapen |
| `$metadata.{propertyName}.desiredVersion` | [Endast för skrivbara egenskaper] Versionen för det önskade värdet |
| `$metadata.{propertyName}.ackVersion` | Versionen som bekräftats av enhets appen som implementerar den digitala dubbla |
| `$metadata.{propertyName}.ackCode` | [Endast för skrivbara egenskaper] `ack` Koden som returneras av enhets appen som implementerar den digitala dubbla |
| `$metadata.{propertyName}.ackDescription` | [Endast för skrivbara egenskaper] `ack` Beskrivningen som returneras av enhets appen som implementerar den digitala dubbla |
| `{componentName}` | Ett JSON-objekt som innehåller komponentens egenskaps värden och metadata som liknar dem för rotobjektet. Det här objektet finns även om komponenten inte har några egenskaper. |
| `{componentName}.{propertyName}` | Värdet för komponentens egenskap i JSON ( `string` , tal typ eller objekt) |
| `{componentName}.$metadata` | Metadata-informationen för komponenten, som liknar rot nivån `$metadata` |

Här är ett exempel på en digital, dubbels formaterad som ett JSON-objekt:

```json
{
  "$dtId": "Cafe",
  "$etag": "W/\"e59ce8f5-03c0-4356-aea9-249ecbdc07f9\"",
  "Temperature": 72,
  "Location": {
    "x": 101,
    "y": 33
  },
  "component": {
    "TableOccupancy": 1,
    "$metadata": {
      "TableOccupancy": {
        "desiredValue": 1,
        "desiredVersion": 3,
        "ackVersion": 2,
        "ackCode": 200,
        "ackDescription": "OK"
      }
    }
  },
  "$metadata": {
    "$model": "dtmi:com:contoso:Room;1",
    "Temperature": {
      "desiredValue": 72,
      "desiredVersion": 5,
      "ackVersion": 4,
      "ackCode": 200,
      "ackDescription": "OK"
    },
    "Location": {
      "desiredValue": {
        "x": 101,
        "y": 33,
      },
      "desiredVersion": 8,
      "ackVersion": 8,
      "ackCode": 200,
      "ackDescription": "OK"
    }
  }
}
```

### <a name="relationship-json-format"></a>Relations-JSON-format

När den visas som ett JSON-objekt, visas följande fält i en relation från en digital:

| Fältnamn | Beskrivning |
| --- | --- |
| `$relationshipId` | En användardefinierad sträng som representerar ID: t för den här relationen. Den här strängen är unik i kontexten för källan Digital, som också innebär att `sourceId`  +  `relationshipId` är unik i kontexten för Azure Digitals-instansen. |
| `$etag` | Standard-HTTP-fält som tilldelas av webb servern |
| `$sourceId` | ID: t för den digitala källan |
| `$targetId` | ID: t för målets digitala dubbla |
| `$relationshipName` | Namnet på relationen |
| `{propertyName}` | Valfritt Värdet för en egenskap för den här relationen, i JSON ( `string` , siffer typ eller objekt) |

Här är ett exempel på en relation som är formaterad som ett JSON-objekt:

```json
{
  "$relationshipId": "relationship-01",
  "$etag": "W/\"506e8391-2b21-4ac9-bca3-53e6620f6a90\"",
  "$sourceId": "GroundFloor",
  "$targetId": "Cafe",
  "$relationshipName": "contains",
  "startDate": "2020-02-04"
}
```

## <a name="next-steps"></a>Nästa steg

Se Hantera diagram element med Azures digitala dubbla API: er:
* [*Anvisningar: hantera digitala dubbla*](how-to-manage-twin.md)
* [*Anvisningar: hantera den dubbla grafen med relationer*](how-to-manage-graph.md)

Eller Lär dig mer om att skicka frågor till Azure Digitals dubbla grafer för information:
* [*Begrepp: frågespråk*](concepts-query-language.md)