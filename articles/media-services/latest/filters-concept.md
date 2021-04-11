---
title: Definiera filter i Azure Media Services
description: I det här avsnittet beskrivs hur du skapar filter så att klienten kan använda dem för att strömma vissa delar av en ström. Media Services skapar dynamiska manifest för att uppnå denna selektiva strömning.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 6ba9a4d5ac427533c2ce005a74fe43b0ce80de79
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106109449"
---
# <a name="filters"></a>Filter

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

När du levererar ditt innehåll till kunder (direkt uppspelnings händelser eller video på begäran) kan din klient behöva större flexibilitet än vad som beskrivs i standard till gångens manifest fil. Azure Media Services erbjuder [dynamiska manifest](filters-dynamic-manifest-concept.md) baserat på fördefinierade filter. 

Filter är regler på Server sidan som gör det möjligt för dina kunder att göra saker som: 

- Spela bara upp en del av en video (i stället för att spela upp hela videon). Exempel:
  - Minska manifestet för att visa ett under klipp av en live event ("under klipps filtrering") eller
  - Trimma början på en video ("trimma en video").
- Leverera endast de angivna åter givningarna och/eller angivna språk spår som stöds av enheten som används för att spela upp innehållet ("åter givnings filtrering"). 
- Justera presentations fönster (DVR) för att tillhandahålla en begränsad längd på DVR-fönstret i spelaren ("justera presentations fönstret").

Med Media Services kan du skapa **konto filter** och **till gångs filter** för ditt innehåll. Dessutom kan du associera dina redan skapade filter med en **strömmande positionerare**.

## <a name="defining-filters"></a>Definiera filter

Det finns två typer av filter: 

* [Konto filter](/rest/api/media/accountfilters) (global) – kan användas för alla till gångar i Azure Media Services kontot, ha en livstid för kontot.
* [Till gångs filter](/rest/api/media/assetfilters) (lokal) – kan bara användas för en till gång som filtret var associerat med när det skapades, har en livs längd för till gången. 

**Konto filter** och typer av **till gångs filter** har exakt samma egenskaper för att definiera/beskriva filtret. Förutom när du skapar **till gångs filtret** måste du ange namnet på den till gång som du vill koppla filtret till.

Beroende på ditt scenario bestämmer du vilken typ av filter som är lämpligare (till gångs filter eller konto filter). Konto filter är lämpliga för enhets profiler (åter givnings filtrering) där till gångs filter kan användas för att trimma en speciell till gång.

Du kan använda följande egenskaper för att beskriva filtren. 

|Name|Beskrivning|
|---|---|
|firstQuality|Den första kvalitets bit hastigheten för filtret.|
|presentationTimeRange|Tids området för presentationen. Den här egenskapen används för att filtrera manifest start-/slut punkter, presentations fönster längd och start position. <br/>Mer information finns i [PresentationTimeRange](#presentationtimerange).|
|spårar|Villkoren för att spåra val. Mer information finns i [spår](#tracks)|

### <a name="presentationtimerange"></a>presentationTimeRange

Använd den här egenskapen med **till gångs filter**. Vi rekommenderar inte att du anger egenskapen med **konto filter**.

|Name|Beskrivning|
|---|---|
|**endTimestamp**|Gäller för video på begäran (VoD).<br/>För direkt uppspelnings presentationen ignoreras den tyst och tillämpas när presentationen slutar och strömmen blir VoD.<br/>Detta är ett långt värde som representerar en absolut slut punkt i presentationen, avrundat till närmaste nästa GOP-start. Enheten är tids skala, så en endTimestamp på 1800000000 skulle vara i 3 minuter.<br/>Använd startTimestamp och endTimestamp för att trimma de fragment som ska finnas i spelnings listan (manifest).<br/>Exempel: startTimestamp = 40000000 och endTimestamp = 100000000 med standard tids skalan genererar en spelnings lista som innehåller fragment från mellan 4 och 10 sekunder i VoD-presentationen. Om ett fragment följer gränserna tas hela fragmentet med i manifestet.|
|**forceEndTimestamp**|Gäller endast för direktsänd strömning.<br/>Anger om egenskapen endTimestamp måste finnas. Om värdet är true måste endTimestamp anges eller så returneras en felaktig kod för begäran.<br/>Tillåtna värden: falskt, sant.|
|**liveBackoffDuration**|Gäller endast för direktsänd strömning.<br/> Det här värdet definierar den senaste aktiva platsen som en klient kan söka till.<br/>Med den här egenskapen kan du fördröja uppspelnings positionen i real tid och skapa en buffert för Server sidan för spelare.<br/>Enheten för den här egenskapen är tids skala (se nedan).<br/>Den maximala Live-startaktiviteten är 300 sekunder (3000000000).<br/>Till exempel innebär värdet 2000000000 att det senaste tillgängliga innehållet är 20 sekunder försenat från den verkliga Live-gränsen.|
|**presentationWindowDuration**|Gäller endast för direktsänd strömning.<br/>Använd presentationWindowDuration om du vill använda ett glidande fönster med fragment som ska ingå i en spelnings lista.<br/>Enheten för den här egenskapen är tids skala (se nedan).<br/>Ange till exempel presentationWindowDuration = 1200000000 om du vill använda ett glidande fönster på två minuter. Mediet inom 2 minuter från den levande kanten tas med i spelnings listan. Om ett fragment följer gränserna, kommer hela fragmentet att tas med i spelnings listan. Det minsta presentations fönstrets varaktighet är 60 sekunder.|
|**startTimestamp**|Gäller video på begäran (VoD) eller direkt uppspelning.<br/>Detta är ett långt värde som representerar data strömmens absoluta start punkt. Värdet avrundas till närmaste nästa GOP-start. Enheten är tids skala, så en startTimestamp på 150000000 skulle vara i 15 sekunder.<br/>Använd startTimestamp och endTimestampp för att trimma de fragment som ska finnas i spelnings listan (manifest).<br/>Exempel: startTimestamp = 40000000 och endTimestamp = 100000000 med standard tids skalan genererar en spelnings lista som innehåller fragment från mellan 4 och 10 sekunder i VoD-presentationen. Om ett fragment följer gränserna tas hela fragmentet med i manifestet.|
|**tidsplan**|Gäller för alla tidsstämplar och varaktigheter i ett presentations tidsintervall, angivet som antalet steg i en sekund.<br/>Standardvärdet är 10000000 – 10 000 000-steg på en sekund, där varje ökning blir 100 nanosekunder länge.<br/>Om du till exempel vill ställa in en startTimestamp på 30 sekunder använder du värdet 300000000 när du använder standard tids skalan.|

### <a name="tracks"></a>Spårar

Du anger en lista med villkor för filtrerings spårning (FilterTrackPropertyConditions) baserat på vilka spår från data strömmen (direkt uppspelning eller video på begäran) som ska ingå i dynamiskt skapade manifest. Filtren kombineras med hjälp av en  logisk och **en åtgärd.**

Filtrera egenskaper för spåra egenskaper Beskriv spår typer, värden (beskrivs i följande tabell) och åtgärder (lika med, NotEqual). 

|Name|Beskrivning|
|---|---|
|**Hastigheten**|Använd bit hastigheten för spårningen för filtrering.<br/><br/>Det rekommenderade värdet är ett intervall med bit hastigheter, i bitar per sekund. Till exempel "0-2427000".<br/><br/>Obs! även om du kan använda ett visst bit hastighets värde, t. ex. 250000 (bitar per sekund), rekommenderas inte den här metoden eftersom de exakta bit hastigheterna kan variera från en till gång till en annan.|
|**FourCC**|Använd FourCC-värdet för spårningen för filtrering.<br/><br/>Värdet är det första elementet i codec-formatet, enligt vad som anges i [RFC 6381](https://tools.ietf.org/html/rfc6381). För närvarande stöds följande codecenheter: <br/>För video: "avc1", "hev1", "hvc1"<br/>För ljud: "MP4A", "EC-3"<br/><br/>Hämta och granska manifest filen för att fastställa FourCC-värden för spår i en till gång.|
|**Språk**|Använd språket i spåret för filtrering.<br/><br/>Värdet är taggen för ett språk som du vill ta med, enligt vad som anges i RFC 5646. Till exempel "en".|
|**Namn**|Använd namnet på spåret för filtrering.|
|**Typ**|Använd typen av spårning för filtrering.<br/><br/>Följande värden är tillåtna: "video", "Audio" eller "text".|

### <a name="example"></a>Exempel

I följande exempel definieras ett filter för direkt uppspelning: 

```json
{
  "properties": {
    "presentationTimeRange": {
      "startTimestamp": 0,
      "endTimestamp": 170000000,
      "presentationWindowDuration": 9223372036854776000,
      "liveBackoffDuration": 0,
      "timescale": 10000000,
      "forceEndTimestamp": false
    },
    "firstQuality": {
      "bitrate": 128000
    },
    "tracks": [
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Audio"
          },
          {
            "property": "Language",
            "operation": "NotEqual",
            "value": "en"
          },
          {
            "property": "FourCC",
            "operation": "NotEqual",
            "value": "EC-3"
          }
        ]
      },
      {
        "trackSelections": [
          {
            "property": "Type",
            "operation": "Equal",
            "value": "Video"
          },
          {
            "property": "Bitrate",
            "operation": "Equal",
            "value": "3000000-5000000"
          }
        ]
      }
    ]
  }
}
```

## <a name="associating-filters-with-streaming-locator"></a>Associera filter med streaming Locator

Du kan ange en lista över [till gångs-eller konto filter](filters-concept.md) på din [Utströmnings positionerare](/rest/api/media/streaminglocators/create#request-body). Den [dynamiska Paketeraren](encode-dynamic-packaging-concept.md) använder den här listan med filter tillsammans med de som klienten anger i URL: en. Den här kombinationen genererar ett [dynamiskt manifest](filters-dynamic-manifest-concept.md), som baseras på filter i URL + filter som du anger på den strömmande lokaliseraren. 

Se följande exempel:

* [Associera filter med strömmande lokalisering – .NET](filters-dynamic-manifest-dotnet-how-to.md#associate-filters-with-streaming-locator)
* [Associera filter med strömmande lokalisering – CLI](filters-dynamic-manifest-cli-how-to.md#associate-filters-with-streaming-locator)

## <a name="updating-filters"></a>Uppdaterar filter
 
**Strömmande positionerare** kan inte uppdateras medan filter kan uppdateras. 

Vi rekommenderar inte att du uppdaterar definitionen av filter som är associerade med en aktivt publicerad **strömmande Locator**, särskilt när CDN är aktiverat. Strömmande servrar och CDN kan ha interna cacheminnen som kan resultera i att inaktuella cachelagrade data returneras. 

Om filter definitionen behöver ändras bör du skapa ett nytt filter och lägga till det i URL: en för **streaming Locator** eller publicera en ny **strömmande positionerare** som refererar till filtret direkt.

## <a name="next-steps"></a>Nästa steg

Följande artiklar visar hur du skapar filter program mässigt.  

- [Skapa filter med REST API: er](filters-dynamic-manifest-rest-howto.md)
- [Skapa filter med .NET](filters-dynamic-manifest-dotnet-how-to.md)
- [Skapa filter med CLI](filters-dynamic-manifest-cli-how-to.md)
