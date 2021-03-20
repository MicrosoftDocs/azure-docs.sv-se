---
title: Fråga på Azure IoT Hub meddelanderoutning | Microsoft Docs
description: Lär dig mer om det IoT Hub meddelande om meddelanderoutning som du kan använda för att använda omfattande frågor till meddelanden för att ta emot de data som är viktiga för dig.
author: ash2017
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 08/13/2018
ms.author: asrastog
ms.custom:
- 'Role: Cloud Development'
- 'Role: Data Analytics'
ms.openlocfilehash: 83c290adea02915db1dc52bd359b4d3165611522
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92547715"
---
# <a name="iot-hub-message-routing-query-syntax"></a>IoT Hub-frågesyntaxen för meddelandedirigering

Med meddelanderoutning kan användare dirigera olika data typer, nämligen telemetri meddelanden, enhets livs cykel händelser och enhets dubbla ändrings händelser till olika slut punkter. Du kan också använda omfattande frågor till dessa data innan du dirigerar dem för att ta emot de data som är viktiga för dig. Den här artikeln beskriver frågespråket för IoT Hub Message Routning och innehåller några vanliga fråge mönster.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-partial.md)]

Med meddelanderoutning kan du fråga efter meddelande egenskaper och meddelande text samt enhetens dubbla Taggar och enhetens dubbla egenskaper. Om meddelande texten inte är JSON kan meddelande routning fortfarande dirigera meddelandet, men det går inte att använda frågor i meddelande texten.  Frågor beskrivs som booleska uttryck där ett booleskt värde gör att frågan lyckas och som dirigerar alla inkommande data och booleska falskt Miss lyckas frågan och inga data vidarebefordras. Om uttrycket utvärderas till null eller odefinierad, behandlas det som falskt och ett fel genereras i IoT Hub [vägar resurs loggar](monitor-iot-hub-reference.md#routes) loggar i händelse av fel. Frågesyntaxen måste vara korrekt för att vägen ska kunna sparas och utvärderas.  

## <a name="message-routing-query-based-on-message-properties"></a>Meddelande cirkulations fråga baserat på meddelande egenskaper 

IoT Hub definierar ett [gemensamt format](iot-hub-devguide-messages-construct.md) för all kommunikation från enhet till moln för samverkan mellan protokoll. IoT Hub meddelandet antar följande JSON-representation av meddelandet. System egenskaper läggs till för alla användare och identifierar innehållet i meddelandet. Användare kan selektivt lägga till program egenskaper i meddelandet. Vi rekommenderar att du använder unika egenskaps namn som IoT Hub enhet-till-moln-meddelanden inte är Skift läges känsliga. Om du till exempel har flera egenskaper med samma namn kommer IoT Hub endast att skicka en av egenskaperna.  

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

System egenskaper hjälper till att identifiera innehåll och källa för meddelandena. 

| Egenskap | Typ | Beskrivning |
| -------- | ---- | ----------- |
| Innehålls | sträng | Användaren anger meddelandets innehålls typ. Om du vill tillåta frågan på meddelande texten ska det här värdet ställas in Application/JSON. |
| contentEncoding | sträng | Användaren anger meddelandets kodnings typ. Tillåtna värden är UTF-8, UTF-16, UTF-32 om contentType är inställt på Application/JSON. |
| iothub-Connection-Device-ID | sträng | Det här värdet anges av IoT Hub och identifierar enhetens ID. Om du vill fråga använder du `$connectionDeviceId` . |
| iothub – enqueuedtime | sträng | Det här värdet anges av IoT Hub och representerar den faktiska tiden för att köa meddelandet i UTC. Om du vill fråga använder du `enqueuedTime` . |
| DT-dataschema | sträng |  Det här värdet anges av IoT Hub på enhet-till-moln-meddelanden. Det innehåller det enhets modell-ID som angetts i enhets anslutningen. Om du vill fråga använder du `$dt-dataschema` . |
| DT-ämne | sträng | Namnet på komponenten som skickar meddelanden från enheten till molnet. Om du vill fråga använder du `$dt-subject` . |

Som det beskrivs i [IoT Hub-meddelanden](iot-hub-devguide-messages-construct.md)finns det ytterligare system egenskaper i ett meddelande. Utöver egenskaperna ovan i föregående tabell kan du också fråga **connectionDeviceId**, **connectionModuleId**.

### <a name="application-properties"></a>Egenskaper för program

Program egenskaperna är användardefinierade strängar som kan läggas till i meddelandet. Dessa fält är valfria.  

### <a name="query-expressions"></a>Frågeuttryck

En fråga om meddelande system egenskaper måste föregås av `$` symbolen. Frågor om program egenskaper nås med sitt namn och bör inte föregås av `$` symbolen. Om ett program egenskaps namn börjar med `$` kommer IoT Hub att söka efter det i System egenskaperna, och det går inte att hitta det. därefter kommer det att se ut i program egenskaperna. Exempel: 

Så här frågar du efter system egenskapen contentEncoding 

```sql
$contentEncoding = 'UTF-8'
```

Så här frågar du efter program egenskapen processingPath:

```sql
processingPath = 'hot'
```

Om du vill kombinera dessa frågor kan du använda booleska uttryck och funktioner:

```sql
$contentEncoding = 'UTF-8' AND processingPath = 'hot'
```

En fullständig lista över operatörer och funktioner som stöds visas i [uttryck och villkor](iot-hub-devguide-query-language.md#expressions-and-conditions).

## <a name="message-routing-query-based-on-message-body"></a>Meddelande cirkulations fråga baserat på meddelande text

Om du vill aktivera frågor i meddelande texten ska meddelandet vara i en JSON-kodad antingen UTF-8, UTF-16 eller UTF-32. `contentType`Måste anges till `application/JSON` och `contentEncoding` till en av de UTF-kodningar som stöds i system egenskapen. Om de här egenskaperna inte anges kommer IoT Hub inte att utvärdera frågeuttrycket i meddelande texten. 

I följande exempel visas hur du skapar ett meddelande med en korrekt utformad och kodad JSON-text: 

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
> Här visas hur du hanterar kodningen för bröd texten i Java Script. Om du vill se ett exempel i C#, laddar du ned [Azure IoT C#-exempel](https://github.com/Azure-Samples/azure-iot-samples-csharp/archive/master.zip). Zippa upp master.zip-filen. Filen program. cs i Visual Studio-lösningen *SimulatedDevice* visar hur du kodar och skickar meddelanden till en IoT Hub. Det här är samma exempel som används för att testa meddelanderoutning, som beskrivs i [själv studie kursen för meddelanderoutning](tutorial-routing.md). Längst ned i program. cs finns det också en metod för att läsa i en av de kodade filerna, avkoda den och skriva tillbaka den som ASCII så att du kan läsa den. 


### <a name="query-expressions"></a>Frågeuttryck

En fråga om meddelande text måste föregås av `$body` . Du kan använda en Body-referens, en text mat ris referens eller flera text referenser i frågeuttrycket. Frågeuttrycket kan också kombinera en brödtext referens med meddelande system egenskaper och referens för meddelande program egenskaper. Till exempel är följande giltiga frågeuttryck: 

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

## <a name="message-routing-query-based-on-device-twin"></a>Meddelande cirkulations fråga baserat på enhet, dubbla 

Med meddelanderoutning kan du fråga på [enhetens dubbla](iot-hub-devguide-device-twins.md) Taggar och egenskaper, som är JSON-objekt. Det finns även stöd för frågor i modulerna "delad". Ett exempel på enhets dubbla Taggar och egenskaper visas nedan.

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

En fråga om meddelandets dubbla måste föregås av `$twin` . Frågeuttrycket kan också kombinera en kombinations-eller egenskaps referens med en innehålls referens, meddelande system egenskaper och referens för meddelande program egenskaper. Vi rekommenderar att du använder unika namn i taggar och egenskaper eftersom frågan inte är Skift läges känslig. Detta gäller för både enhetens dubbla och modul. Du kan också avstå från att använda `twin` , `$twin` , `body` , eller `$body` , som egenskaps namn. Till exempel är följande giltiga frågeuttryck: 

```sql
$twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$body.Weather.Temperature = 50 AND $twin.properties.desired.telemetryConfig.sendFrequency = '5m'
```

```sql
$twin.tags.deploymentLocation.floor = 1 
```

Routnings frågan på bröd texten eller enheten med en punkt i nytto lasten eller egenskaps namnet stöds inte.

## <a name="next-steps"></a>Nästa steg

* Lär dig [mer om meddelanderoutning](iot-hub-devguide-messages-d2c.md).
* Prova [själv studie kursen om meddelanderoutning](tutorial-routing.md).
