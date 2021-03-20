---
title: Använd direkta metoder i real tids video analys på IoT Edge – Azure
description: Live video analys på IoT Edge visar flera direkta metoder. De direkta metoderna baseras på konventionerna som beskrivs i det här avsnittet.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 8b5c16dc72beed4ec757e48461a2fc194c113f8d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97656248"
---
# <a name="direct-methods"></a>Direkta metoder

Live video analys på IoT Edge visar flera direkta metoder som kan anropas från IoT Hub. Direkta metoder representerar en förfrågan-svar-interaktion med en enhet som liknar ett HTTP-anrop i som de lyckas eller Miss lyckas omedelbart (efter en användardefinierad tids gräns). Den här metoden passar bra i scenarier där vad som ska utföras beror på om enheten svarar eller inte. Mer information finns i [förstå och anropa direkt metoder från IoT Hub](../../iot-hub/iot-hub-devguide-direct-methods.md).

I det här avsnittet beskrivs dessa metoder och konventioner.

## <a name="conventions"></a>Konventioner

De direkta metoderna baseras på följande konventioner:

1. Direkta metoder har en version som anges i större. MINDRE format (som visas i följande exempel):

    ```
    {
        "methodName": "GraphTopologySet",
        "payload": {
            // API version matches matches module MAJOR.MINOR version
            "@apiVersion": "1.0"
            //payload here…
        }
    }
    ```

2. En specifik version av Live Video Analytics i IoT Edge modul kommer att ha stöd för alla direkta metoder till den aktuella versionen. Modul version 1,3 kommer till exempel att stödja direkta metoder med versioner 1,3, 1,2, 1,1 och 1,0 versioner.
3. Alla direkta metoder är synkrona.
4. Fel resultat följer OData-fel schema.
5. Namn bör Observera följande begränsningar:
    
    * Endast alfanumeriska tecken och bindestreck så länge det inte startar och slutar med ett bindestreck
    * Inga blank steg
    * Högst 32 tecken

### <a name="example"></a>Exempel

```
{
  "status": 400,
  "payload": {
    "error": {
      "code": "BadRequest",
      "message": "Graph instance is not found."
    }
  }
}
```    

### <a name="top-level-error-codes"></a>Felkoder på översta nivån     

|Status |Kod   |Meddelande|
|---|---|---|
|400|   BadRequest| Begäran är inte giltig|
|400|   InvalidResource|    Resursen är inte giltig|
|400|   InvalidVersion| API-versionen är inte giltig|
|402|   ConnectivityRequired    |Edge-modulen kräver att moln anslutningen fungerar korrekt.|
|404|   NotFound    |Resursen hittades inte|
|409|   Konflikt|   Åtgärds konflikt|
|500|   InternalServerError|    Internt serverfel|
|503|   ServerBusy| Servern är upptagen|

### <a name="detailed-error-codes"></a>Detaljerade felkoder

Detaljerade fel uppgifter, till exempel valideringar av graf-modul, läggs till som fel information:

```
{
  "status": 400,
  "payload": {
    "error": {
      "code": "InvalidResource",
      "message": "The resource format is invalid.",
      "details": [
        {
          "code": "RtspEndpointUrlInvalidScheme",
          "target": "$.Properties.Sources[0]",
          "message": "Uri scheme 'http' is not valid for an RTSP source endpoint. Valid values are: rtsp"
        },
        {
          "code": "PropertyValueInvalidPattern",
          "target": "$.Properties.Sinks[0].AssetNamePattern",
          "message": "The value must match the regular expression '^[^<>%&:\\\\\\/?*+.']{1,260}$'"
        }
      ]
    }
  }
}
}
```

|Status|    Detaljerad kod   |Beskrivning|
|---|---|---|
|400|   GraphValidationError|   Allmänna diagram fel som cykler eller partitionering osv.|
|400|   ModuleValidationError|  Verksamhetsspecifika verifierings fel.|
|409|   GraphTopologyInUse| Diagram sto pol Ogin refereras fortfarande till av en eller flera diagram instanser.|
|409|   OperationNotAllowedInState| Den begärda åtgärden kan inte utföras i det aktuella läget.|
|409|   ResourceValidationError|    Refererad resurs (exempel: till gång) är inte i ett giltigt tillstånd.|

## <a name="details"></a>Information  

### <a name="graphtopologyget"></a>GraphTopologyGet

Den här direkta metoden hämtar en enda diagram sto pol Ogin.

#### <a name="request"></a>Förfrågan

```
{
    "methodName": "GraphTopologyGet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "CaptureAndRecord"
    }
}
```

#### <a name="response"></a>Svarsåtgärder

```
{
    "status": 200,
    "payload": {
        "name": "CaptureAndRecord",
        "properties": {
            // Complete graph topology
        }
    }
}
```

#### <a name="status-codes"></a>Statuskoder

| Villkor | Statuskod | Detaljerad felkod |
|--|--|--|
| Entitet hittades | 200 | Ej tillämpligt |
| Allmänna användar fel | 400 intervall |  |
| Entiteten hittades inte | 404 |  |
| Allmänna Server fel | 500 intervall |  |

### <a name="graphtopologyset"></a>GraphTopologySet

Skapar en enskild topologi om det inte finns någon befintlig med det aktuella namnet eller uppdateringarna och en befintlig med samma namn.

Viktiga aspekter:

* Topologin kan uppdateras fritt finns det inga grafer som refererar till den.
* Topologin kan uppdateras fritt om alla refererande grafer inaktive ras så länge som:

    * Nyligen tillagda parametrar har standardvärden
    * Det finns inga referenser till borttagna parametrar i grafen
* Topologi uppdateringar är inte tillåtna om det finns aktiva grafer

#### <a name="request"></a>Förfrågan

```
{
    "methodName": "GraphTopologySet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "CaptureAndRecord",
        "properties": {
            // Desired graph topology properties
        }
    }
}
```

#### <a name="response"></a>Svarsåtgärder

```
{
    "status": 201,
    "payload": {
        "name": " CaptureAndRecord ",
        "properties": {
            // Complete graph topology
        }
    }
}
```

#### <a name="status-codes"></a>Statuskoder

|Villkor  |Statuskod    |Detaljerad felkod|
|---|---|---|
Den befintliga entiteten har uppdaterats |200|   Ej tillämpligt|
En ny entitet har skapats  |201|   Ej tillämpligt|
Allmänna användar fel |400 intervall  ||
Diagram verifierings fel |400    |GraphValidationError|
Modul validerings fel|   400 |ModuleValidationError|
Allmänna Server fel   |500 intervall  ||

### <a name="graphtopologydelete"></a>GraphTopologyDelete

Tar bort en enskild diagram sto pol Ogin.

#### <a name="request"></a>Förfrågan

```
{
    "methodName": "GraphTopologyDelete",
    "payload": {
        "@apiVersion": "1.0",
        "name": "CaptureAndRecord"
    }
}
```

#### <a name="response"></a>Svarsåtgärder

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>Statuskoder

| Villkor | Statuskod | Detaljerad felkod |
|--|--|--|
| Entitet borttagen | 200 | Ej tillämpligt |
| Entiteten hittades inte | 204 | Ej tillämpligt |
| Allmänna användar fel | 400 intervall |  |
| Diagram sto pol Ogin refereras till av en eller flera diagram instanser | 409 | GraphTopologyInUse |
| Allmänna Server fel | 500 intervall |  |

### <a name="graphtopologylist"></a>GraphTopologyList

Hämtar en lista över alla Graph-topologier som matchar filter kriterierna.

#### <a name="request"></a>Förfrågan

```
{
    "methodName": "GraphTopologyList",
    "payload": {
        // Required
        "@apiVersion": "1.0",
        
        // Optional
        "@continuationToken": "xxxxx",    
        "@query": "$orderby=name asc"
    }
}
```

#### <a name="response"></a>Svarsåtgärder

```
{
    "status": 200,
    "payload": {
        "@continuationToken": "xxxx",
        "value": [
            {
                "name": "Capture & Record",
                // ...
            },
            {
                "name": "Capture, Analyze & Record",
                // ...
            }
        ]
    }
}
```

#### <a name="filter-support"></a>Filter stöd

|Åtgärd |Fält    |Operatorer|
|---|---|---|
|$orderby|name  |stigande|


#### <a name="status-codes"></a>Statuskoder

| Villkor | Statuskod | Detaljerad felkod |
|--|--|--|
| Klart | 200 | Ej tillämpligt |
| Allmänna användar fel | 400 intervall |  |
| Allmänna Server fel | 500 intervall |  |

### <a name="graphinstanceget"></a>GraphInstanceGet

Hämtar en enstaka graf-förekomst:

#### <a name="request"></a>Förfrågan

```
{
    "methodName": "GraphInstanceGet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>Svarsåtgärder

```
{
    "status": 200,
    "payload": {
        "name": "Camera001",
        "properties": {
            // Complete graph instance
        }
    }
}
```

#### <a name="status-codes"></a>Statuskoder

| Villkor | Statuskod | Detaljerad felkod |
|--|--|--|
| Entitet hittades | 200 | Ej tillämpligt |
| Allmänna användar fel | 400 intervall |  |
| Entiteten hittades inte | 404 |  |
| Allmänna Server fel | 500 intervall |  |

### <a name="graphinstanceset"></a>GraphInstanceSet

Skapar en enstaka diagram förekomst om det inte finns någon befintlig med det aktuella namnet eller uppdateringarna och en befintlig med samma namn.

Viktiga aspekter:

* Diagram instansen kan uppdateras fritt i läget "inaktive rad".

    * Grafen verifieras på om varje uppdatering.
* Uppdatering av diagram instanser är delvis begränsad medan grafen inte är i inaktivt läge.
* Diagram instans uppdateringar är inte tillåtna i aktiva diagram.

#### <a name="request"></a>Förfrågan

```
{
"methodName": "GraphInstanceSet",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001",
        "properties": {
            // Desired graph instance properties
        }
    }
}
```

#### <a name="response"></a>Svarsåtgärder

````
{
    "status": 201,
    "payload": {
        "name": " Camera001",
        "properties": {
            // Complete graph instance
        }   
    }
}
````

#### <a name="status-codes"></a>Statuskoder

| Villkor | Statuskod | Detaljerad felkod |
|--|--|--|
| Den befintliga entiteten har uppdaterats | 200 | Ej tillämpligt |
| En ny entitet har skapats | 201 | Ej tillämpligt |
| Allmänna användar fel | 400 intervall |  |
| Diagram verifierings fel | 400 | GraphValidationError |
| Modul validerings fel | 400 | ModuleValidationError |
| Resurs validerings fel | 409 | ResourceValidationError |
| Allmänna Server fel | 500 intervall |  |  |

### <a name="graphinstancedelete"></a>GraphInstanceDelete

Tar bort en enstaka diagram instans.

Viktiga aspekter:

* Endast inaktiverade diagram kan tas bort.

#### <a name="request"></a>Förfrågan

```
{
    "methodName": "GraphInstanceDelete",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>Svarsåtgärder

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>Statuskoder

| Villkor | Statuskod | Detaljerad felkod |
|--|--|--|
| Grafen har tagits bort | 200 | Ej tillämpligt |
| Grafen hittades inte | 204 | Ej tillämpligt |
| Allmänna användar fel | 400 intervall |  |
| Grafen är inte i läget "stoppad" | 409 | OperationNotAllowedInState |
| Allmänna Server fel | 500 intervall |  |

### <a name="graphinstancelist"></a>GraphInstanceList

Detta liknar GraphTopologyList. Den gör det möjligt att räkna upp graf-instanser.
Hämtar en lista över alla grafer-instanser som matchar filter kriterierna.

#### <a name="request"></a>Förfrågan

```
{
    "methodName": "GraphInstanceList",
    "payload": {
        // Required
        "@apiVersion": "1.0",
        
        // Optional
        "@continuationToken": "xxxxx",    
        "@query": "$orderby=name asc"
    }
}
```

#### <a name="response"></a>Svarsåtgärder

```
{
    "status": 200,
    "payload": {
        "@continuationToken": "xxxx",
        "value": [
            {
                "name": "Capture & Record",
                // ...
            },
            {
                "name": "Capture, Analyze & Record",
                // ...
            }
        ]
    }
}
```

#### <a name="filter-support"></a>Filter stöd

|Åtgärd  |   Fält|   Operatorer|
|---|---|---|
|$orderby|  name|   stigande|

#### <a name="status-codes"></a>Statuskoder

| Villkor | Statuskod | Detaljerad felkod |
|--|--|--|
| Klart | 200 | Ej tillämpligt |
| Allmänna användar fel | 400 intervall |  |
| Allmänna Server fel | 500 intervall |  |

### <a name="graphinstanceactivate"></a>GraphInstanceActivate

Aktiverar en enskild diagram instans. 

Viktiga aspekter

* Metoden returnerar endast när graf är aktiverat 
* Diagrammet antar tillståndet "aktivering" när det aktive ras.

    * En lista/Hämta-åtgärd i grafen skulle returnera grafen till rätt tillstånd.
* Idempotens

    * Att starta en graf om tillståndet "aktivering" fungerar på samma sätt som om grafen inaktiverades (det vill säga: anropa block tills grafen har Aktiver ATS)
    * Aktivering av ett diagram på "aktivt" läge returnerar omedelbart.

#### <a name="request"></a>Förfrågan

```
{
    "methodName": "GraphInstanceActivate",
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>Svarsåtgärder

```
{
    "status": 200,
    "payload": null
}
```

#### <a name="status-codes"></a>Statuskoder

| Villkor | Statuskod | Detaljerad felkod |
|--|--|--|
| Grafen har Aktiver ATS | 200 | Ej tillämpligt |
| En ny entitet har skapats | 201 | Ej tillämpligt |
| Allmänna användar fel | 400 intervall |  |
| Modul validerings fel | 400 | ModuleValidationError |
| Resurs validerings fel | 409 | ResourceValidationError |
| Diagrammet är i inaktive rings läge | 409 | OperationNotAllowedInState |
| Allmänna Server fel | 500 intervall |  |

### <a name="graphinstancedeactivate"></a>GraphInstanceDeactivate

Inaktiverar en enstaka diagram instans. Inaktive ring av ett diagram gör det möjligt att inaktivera medie bearbetningen och säkerställer att alla händelser och medier som lagras i kanten är allokerade till molnet, när så är tillämpligt.

Viktiga aspekter:

* Metoden returnerar endast när grafen är inaktive rad
* Diagrammet antar tillståndet "inaktive ring" medan inaktive ras.

    * En lista/Hämta-åtgärd i grafen skulle returnera grafen till rätt tillstånd.
    * Stoppa bara Slutför när alla media har överförts till molnet.
* Idempotens

    * Inaktive ring av diagram på status "inaktive ring" fungerar på samma sätt som om grafen inaktiverades (det vill säga: anropa block tills grafen är inaktive rad)
    * Inaktive ring av en graf i tillståndet "inaktiv" returneras omedelbart.

#### <a name="request"></a>Förfrågan

```
{
    "methodName": "GraphInstanceDeactivate",
    // A high response timeout is recommended here since there might be data movement    // being executed as part of this operation
    "responseTimeoutInSeconds": 300,
    "payload": {
        "@apiVersion": "1.0",
        "name": "Camera001"
    }
}
```

#### <a name="response"></a>Svarsåtgärder

```
{
    "status": 200,
    "payload": null
}
```

| Villkor | Statuskod | Detaljerad felkod |
|--|--|--|
| Grafen har Aktiver ATS | 200 | Ej tillämpligt |
| En ny entitet har skapats | 201 | Ej tillämpligt |
| Allmänna användar fel | 400 intervall |  |
| Grafen är i aktiverings läge | 409 | OperationNotAllowedInState |
| Allmänna Server fel | 500 intervall |  |

## <a name="next-steps"></a>Nästa steg

[Konfigurationsschema för modultvilling](module-twin-configuration-schema.md)
