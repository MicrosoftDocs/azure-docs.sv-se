---
title: Fråga om Azure IoT Hub routning av | Microsoft Docs
description: Lär dig mer IoT Hub dirigering av meddelanden som du kan använda för att tillämpa omfattande frågor på meddelanden för att ta emot de data som är viktiga för dig.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.custom:
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: c4ba48377d868404ff130ec458e50e2b42fae977
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790525"
---
# <a name="iot-hub-message-routing-query-syntax"></a>IoT Hub-frågesyntaxen för meddelandedirigering

Med meddelanderoutning kan användare dirigera olika datatyper, nämligen enhettelemetrimeddelanden, enhetslivscykelhändelser och enhetstvillingändringshändelser till olika slutpunkter. Du kan också använda omfattande frågor på dessa data innan du dirigerar dem för att ta emot de data som är viktiga för dig. Den här artikeln beskriver IoT Hub frågespråket för meddelanderoutning och innehåller några vanliga frågemönster.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Med meddelanderoutning kan du köra frågor mot meddelandeegenskaperna och meddelandetexten samt taggar för enhetstvillingar och egenskaper för enhetstvillingarna. Om meddelandetexten inte är JSON kan meddelanderoutning fortfarande dirigera meddelandet, men frågor kan inte tillämpas på meddelandetexten.  Frågor beskrivs som booleska uttryck där ett booleskt sant gör att frågan lyckas, vilket dirigerar alla inkommande data, och booleskt falskt misslyckas med frågan och inga data dirigeras. Om uttrycket utvärderas till null eller odefinierat behandlas det som falskt och ett [](monitor-iot-hub-reference.md#routes) fel genereras i IoT Hub dirigerar resursloggar om det uppstår ett fel. Frågesyntaxen måste vara korrekt för att vägen ska sparas och utvärderas.  

## <a name="message-routing-query-based-on-message-properties"></a>Fråga om meddelanderoutning baserat på meddelandeegenskaper 

Den IoT Hub definierar [ett gemensamt format](iot-hub-devguide-messages-construct.md) för alla meddelanden från enhet till moln för samverkan mellan protokoll. IoT Hub här meddelandet förutsätter följande JSON-återgivning av meddelandet. Systemegenskaper läggs till för alla användare och identifierar innehållet i meddelandet. Användare kan selektivt lägga till programegenskaper i meddelandet. Vi rekommenderar att du använder unika egenskapsnamn IoT Hub meddelanden från enhet till moln inte är ärendekänsliga. Om du till exempel har flera egenskaper med samma namn IoT Hub bara en av egenskaperna.  

```json
{ 
  "message": { 
    "systemProperties": { 
      "contentType": "application/json", 
      "contentEncoding": "UTF-8", 
      "iothub-message-source": "deviceMessages", 
      "iothub-enqueuedtime": "2017-05-08T18:55:31.8514657Z" 
    }, 
    "appProperties": { 
      "processingPath": "{cold | warm | hot}", 
      "verbose": "{true, false}", 
      "severity": 1-5, 
      "testDevice": "{true | false}" 
    }, 
    "body": "{\"Weather\":{\"Temperature\":50}}" 
  } 
} 
```

### <a name="system-properties"></a>Systemegenskaper

Systemegenskaper hjälper dig att identifiera innehållet och källan för meddelandena. 

| Egenskap | Typ | Description |
| -------- | ---- | ----------- |
| Contenttype | sträng | Användaren anger meddelandets innehållstyp. Om du vill tillåta frågor i meddelandetexten ska det här värdet anges för application/JSON. |
| contentEncoding | sträng | Användaren anger kodningstypen för meddelandet. Tillåtna värden är UTF-8, UTF-16, UTF-32 om contentType är inställt på application/JSON. |
| iothub-connection-device-id | sträng | Det här värdet anges IoT Hub och identifierar ENHETENs ID. Om du vill fråga använder du `$connectionDeviceId` . |
| iothub-enqueuedtime | sträng | Det här värdet anges av IoT Hub och representerar den faktiska tiden för att ställa meddelandet i enqueuing i UTC. Om du vill fråga använder du `enqueuedTime` . |
| dt-dataschema | sträng |  Det här värdet anges av IoT Hub på meddelanden från enheten till molnet. Den innehåller enhetsmodell-ID:t som angetts i enhetsanslutningen. Om du vill fråga använder du `$dt-dataschema` . |
| dt-subject | sträng | Namnet på den komponent som skickar meddelanden från enheten till molnet. Om du vill fråga använder du `$dt-subject` . |

Enligt beskrivningen [i IoT Hub meddelanden](iot-hub-devguide-messages-construct.md)finns det ytterligare systemegenskaper i ett meddelande. Förutom ovanstående egenskaper i föregående tabell kan du även fråga **connectionDeviceId**, **connectionModuleId**.

### <a name="application-properties"></a>Egenskaper för program

Programegenskaper är användardefinierade strängar som kan läggas till i meddelandet. De här fälten är valfria.  

### <a name="query-expressions"></a>Frågeuttryck

En fråga om meddelandesystemegenskaper måste föregås av `$` symbolen . Frågor om programegenskaper nås med deras namn och ska inte föregås av `$` symbolen . Om ett programegenskapsnamn börjar med IoT Hub söker efter det i systemegenskaperna och det inte hittas. Då söker det i `$` programegenskaperna. Exempel: 

Fråga efter systemegenskapsinnehållKoda 

```sql
$contentEncoding = 'UTF-8'
```

Så här frågar du efter processingPath för programegenskap:

```sql
processingPath = 'hot'
```

Om du vill kombinera dessa frågor kan du använda booleska uttryck och funktioner:

```sql
$contentEncoding = 'UTF-8' AND processingPath = 'hot'
```

En fullständig lista över operatorer och funktioner som stöds visas i [Uttryck och villkor](iot-hub-devguide-query-language.md#expressions-and-conditions).

## <a name="message-routing-query-based-on-message-body"></a>Meddelanderoutningsfråga baserat på meddelandetext

Om du vill aktivera frågor om meddelandetexten ska meddelandet finnas i en JSON-kodad i UTF-8, UTF-16 eller UTF-32. måste `contentType` anges till och till en av de `application/JSON` `contentEncoding` UTF-kodningar som stöds i systemegenskapen. Om dessa egenskaper inte anges IoT Hub inte frågeuttrycket i meddelandetexten. 

I följande exempel visas hur du skapar ett meddelande med en korrekt utformad och kodad JSON-brödtext: 

```javascript
var messageBody = JSON.stringify(Object.assign({}, {
    "Weather": {
        "Temperature": 50,
        "Time": "2017-03-09T00:00:00.000Z",
        "PrevTemperatures": [
            20,
            30,
            40
        ],
        "IsEnabled": true,
        "Location": {
            "Street": "One Microsoft Way",
            "City": "Redmond",
            "State": "WA"
        },
        "HistoricalData": [
            {
                "Month": "Feb",
                "Temperature": 40
            },
            {
                "Month": "Jan",
                "Temperature": 30
            }
        ]
    }
}));

// Encode message body using UTF-8  
var messageBytes = Buffer.from(messageBody, "utf8");

var message = new Message(messageBytes);

// Set message body type and content encoding 
message.contentEncoding = "utf-8";
message.contentType = "application/json";

// Add other custom application properties   
message.properties.add("Status", "Active");

deviceClient.sendEvent(message, (err, res) => {
    if (err) console.log('error: ' + err.toString());
    if (res) console.log('status: ' + res.constructor.name);
});
```

> [!NOTE] 
> Detta visar hur du hanterar kodningen av brödtexten i javascript. Om du vill se ett exempel i C# laddar du ned [Azure IoT C#-exempel](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Packa upp master.zip fil. I Visual Studio *simulatedDevices* Program.cs-fil visas hur du kodar och skickar meddelanden till en IoT Hub. Det här är samma exempel som används för att testa meddelanderoutning, enligt förklaringen i [självstudien om meddelanderoutning.](tutorial-routing.md) Längst ned i Program.cs finns också en metod för att läsa i någon av de kodade filerna, avkoda den och skriva tillbaka den som ASCII så att du kan läsa den. 


### <a name="query-expressions"></a>Frågeuttryck

En fråga i meddelandetexten måste föregås av `$body` . Du kan använda en brödtextreferens, en brödtextmatrisreferens eller flera brödtextreferenser i frågeuttrycket. Frågeuttrycket kan också kombinera en brödtextreferens med meddelandesystemegenskaper och referens för meddelandeprogramegenskaper. Följande är till exempel alla giltiga frågeuttryck: 

```sql
$body.Weather.HistoricalData[0].Month = 'Feb' 
```

```sql
$body.Weather.Temperature = 50 AND $body.Weather.IsEnabled 
```

```sql
length($body.Weather.Location.State) = 2 
```

```sql
$body.Weather.Temperature = 50 AND processingPath = 'hot'
```

> [!NOTE] 
> Du kan bara köra frågor och funktioner på egenskaper i brödtextreferensen. Du kan inte köra frågor eller funktioner på hela brödtextreferensen. Följande fråga stöds till exempel *inte och* returnerar `undefined` :
> 
> ```sql
> $body[0] = 'Feb'
> ```

## <a name="message-routing-query-based-on-device-twin"></a>Fråga om meddelanderoutning baserat på enhetstvilling 

Med meddelanderoutning kan du köra frågor mot taggar [och](iot-hub-devguide-device-twins.md) egenskaper för enhetstvillingen, som är JSON-objekt. Frågor om modultvilling stöds också. Ett exempel på taggar och egenskaper för enhetstvillingen visas nedan.

```JSON
{
    "tags": { 
        "deploymentLocation": { 
            "building": "43", 
            "floor": "1" 
        } 
    }, 
    "properties": { 
        "desired": { 
            "telemetryConfig": { 
                "sendFrequency": "5m" 
            }, 
            "$metadata" : {...}, 
            "$version": 1 
        }, 
        "reported": { 
            "telemetryConfig": { 
                "sendFrequency": "5m", 
                "status": "success" 
            },
            "batteryLevel": 55, 
            "$metadata" : {...}, 
            "$version": 4 
        } 
    } 
} 
```

### <a name="query-expressions"></a>Frågeuttryck

En fråga för meddelandetvillingen måste föregås av `$twin` . Ditt frågeuttryck kan också kombinera en tvillingtagg eller egenskapsreferens med en brödtextreferens, meddelandesystemegenskaper och referens för meddelandeprogramegenskaper. Vi rekommenderar att du använder unika namn i taggar och egenskaper eftersom frågan inte är fallkänslig. Detta gäller både enhetstvillingarna och modultvillingarna. Undvik också att `twin` använda , , eller som `$twin` `body` `$body` egenskapsnamn. Följande är till exempel alla giltiga frågeuttryck: 

```sql
$twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$body.Weather.Temperature = 50 AND $twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$twin.tags.deploymentLocation.floor = 1 
```

Routningsfråga på brödtext eller enhetstvilling med en punkt i nyttolasten eller egenskapsnamnet stöds inte.

## <a name="next-steps"></a>Nästa steg

* Läs mer om [meddelanderoutning.](iot-hub-devguide-messages-d2c.md)
* Prova [självstudien om meddelanderoutning.](tutorial-routing.md)
