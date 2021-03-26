---
title: HTTP-tillägg-protokollet – Azure
description: I den här artikeln får du lära dig att använda HTTP-tilläggsprogram för att skicka meddelanden mellan Live Video Analytics-modulen och AI-eller CV-modulen.
ms.topic: overview
ms.date: 09/14/2020
ms.openlocfilehash: 54b25894c60a39de9c0ec00cdc4982f691bf1ee3
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105565749"
---
# <a name="http-extension-protocol"></a>Protokoll för HTTP-tillägg

Med live video analys på IoT Edge kan du utöka medie diagrammets bearbetnings funktioner via en [nod i diagram tillägget](./media-graph-extension-concept.md). Om du använder HTTP-tilläggsbegäranden som tilläggsprovider är kommunikationen mellan Live Video Analytics-modulen och din AI-eller ka-modul över HTTP

I den här artikeln får du lära dig att använda HTTP-tilläggsprogram för att skicka meddelanden mellan Live Video Analytics-modulen och AI-eller CV-modulen. 

HTTP-kontraktet definieras mellan följande två komponenter:

* HTTP-Server
* Live video analys i IoT Edge-modulen fungerar som HTTP-klient

## <a name="request"></a>Förfrågan

Förfrågningar från Live Video Analytics-modulen till HTTP-servern är följande:

|Tangent|Värde|
|---|---|
|POST|`https://hostname/optional-path?optional-query`|
|Acceptera|Application/JSON,  */*|
|Auktorisering|Basic, Digest, Bearer (via anpassad huvud support)|
|Content-Type|bild/jpeg<br/>bild/png<br/>bild/bmp<br/>bild/x – RAW|
|Längd på innehålls längd, i byte|
|User-Agent|Azure Media Services|
|Brödtext|Bild byte, binärt kodat i en av de innehålls typer som stöds.|

### <a name="example"></a>Exempel

```
POST http://localhost:8080/inference HTTP/1.1
Host: localhost:8080
x-ms-client-request-id: d6050cd4-c9f2-42d3-9adc-53ba7e440f17
Content-Type: image/bmp
Content-Length: 519222

(Image Binary Content)
```

## <a name="response"></a>Svarsåtgärder

Svar från modulen till video analys modulen i real tid bör vara följande:

|Tangent|Värde|
|---|---|
|Statuskoder|200 OK-Härlednings resultat hittades<br/>204 inget innehåll – inget innehåll hittades av AI-filen<br/>400 Felaktig begäran-inte förväntades<br/>500 internt Server fel-ej förväntat<br/>503 servern är upptagen – AMS kommer att stängas av baserat på sidhuvudet "retry-after" eller baserat på en standard tids period i fall rubriken inte är förinställd.|
|Content-Type|application/json|
|Innehålls längd|Längd på brödtext, i byte|
|Brödtext|JSON-objekt med enskild "inferences"-egenskap.|

### <a name="example"></a>Exempel

```
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 468
Server: Microsoft-HTTPAPI/2.0
Date: Fri, 17 Apr 2020 04:44:01 GMT

{
  "inferences": [
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.9048132 },
        "box": { "l": 0.42681578, "t": 0.47660735, "w": 0.019501392, "h": 0.020954132 }
      }
    },
    {
      "type": "entity",
      "entity": {
        "tag": { "value": "car", "confidence": 0.8953932 },
        "box": { "l": 0.55083525, "t": 0.4843858, "w": 0.046550274, "h": 0.046502113 }
      }
    }    
  ]
}
```

Vi rekommenderar starkt att svaren returneras med giltiga JSON-dokument efter det företablerade schemat som definierats enligt [schema objekt modellen för härledning av metadata](./inference-metadata-schema.md). Detta säkerställer bättre samverkan med andra komponenter och möjliga framtida funktioner som läggs till i modulen för video analys i real tid.

Om din modul returnerar ett svar där innehålls typen inte är "Application/JSON", kodar live video analys meddelandet som ett bas 64-innehåll och serialiserar det som en ogenomskinlig JSON-nyttolast.

Om din modul returnerar ett svar med innehålls typen "Application/JSON", men JSON-schemat inte följer det schema för härlednings-metadata som beskrivs nedan, vidarebefordras meddelande nytto lasten via pipelinen, men interoperabiliteten kommer att minska. I det [här](./inference-metadata-schema.md) avsnittet finns detaljerad information om ett schema för data härlednings metadata.

> [!NOTE]
> Om modulen inte genererar något resultat ska den returnera HTTP 204-statuskod (inget innehåll) med en tom svars text. Live Video Analytics kommer att förstå detta som ett tomt resultat och vidarebefordrar inte händelsen i pipelinen.


## <a name="next-steps"></a>Nästa steg

[Protokoll för gRPC-tillägg](./grpc-extension-protocol.md)