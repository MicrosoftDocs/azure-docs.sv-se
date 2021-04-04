---
title: API för webbsökning i Bing svars struktur och svars typer
titleSuffix: Azure Cognitive Services
description: När du skickar Webbsökning i Bing en Sök förfrågan returneras ett `SearchResponse` objekt i svars texten.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: aahi
ms.custom: seodec2018
ms.openlocfilehash: 3dda95312a0b9191ddc11de62959f308ee19fff4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "94380988"
---
# <a name="bing-web-search-api-response-structure-and-answer-types"></a>API för webbsökning i Bing svars struktur och svars typer  

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

När du skickar Webbsökning i Bing en Sök förfrågan returneras ett [`SearchResponse`](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse) objekt i svars texten. Objektet innehåller ett fält för varje svar som Bing bestämt var relevant för fråga. Det här exemplet illustrerar ett Response-objekt om Bing returnerade alla svar:

```json
{
    "_type": "SearchResponse",
    "queryContext": {...},
    "webPages": {...},
    "images": {...},
    "relatedSearches": {...},
    "videos": {...},
    "news": {...},
    "spellSuggestion": {...},
    "computation": {...},
    "timeZone": {...},
    "rankingResponse": {...}
}, ...
```

Vanligt vis returnerar Webbsökning i Bing en delmängd av svaren. Om till exempel termen har *seglings-dinghies* kan svaret omfatta `webPages` , `images` och `rankingResponse` . Om du inte har använt [responseFilter](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#responsefilter) för att filtrera ut webb sidor, innehåller svaret alltid `webpages` och `rankingResponse` svaren.

[!INCLUDE [cognitive-services-bing-url-note](../../../includes/cognitive-services-bing-url-note.md)]

## <a name="webpages-answer"></a>Svar på webb sidor

Svaret på [webb sidan](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webanswer) innehåller en lista med länkar till webb sidor som webbsökning i Bing fastställt var relevanta för frågan. Varje [webb sida](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#webpage) i listan kommer att innehålla: sidans namn, URL, VISNINGS-URL, en kort beskrivning av innehållet och datumet Bing hittade innehållet.

```json
{
    "id": "https:\/\/api.cognitive.microsoft.com\/api\/v7\/#WebPages.0",
    "name": "Dinghy sailing",
    "url": "https:\/\/www.bing.com\/cr?IG=3A43CA5...",
    "displayUrl": "https:\/\/en.contoso.com\/wiki\/Dinghy_sailing",
    "snippet": "Dinghy sailing is the activity of sailing small boats...",
    "dateLastCrawled": "2017-04-05T16:25:00"
}, ...
```

Använd `name` och `url` för att skapa en hyperlänk som tar användaren till webb sidan.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display the webpage in a search results page.

![Rendered webpage example](./media/cognitive-services-bing-web-api/bing-rendered-webpage-example.PNG)
-->

## <a name="images-answer"></a>Svar på bilder

Svar på [avbildningar](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#images) innehåller en lista med bilder som Bing trodde var relevanta för frågan. Varje [bild](/rest/api/cognitiveservices-bingsearch/bing-images-api-v7-reference#image) i listan innehåller URL: en för bilden, dess storlek, dess dimensioner och dess kodnings format. Bildobjektet inkluderar även URL för en miniatyrbild av bilden och miniatyrbildens mått.

```json
{
    "name": "Rich Passage Sailing Dinghy",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=3A43CA5CA64...",
    "thumbnailUrl": "https:\/\/tse1.mm.bing.net\/th?id=OIP....",
    "datePublished": "2011-10-29T11:26:00",
    "contentUrl": "http:\/\/upload.contoso.com\/sailing\/...",
    "hostPageUrl": "http:\/\/www.bing.com\/cr?IG=3A43CA5CA6464....",
    "contentSize": "79239 B",
    "encodingFormat": "jpeg",
    "hostPageDisplayUrl": "http:\/\/en.contoso.com\/wiki\/File...",
    "width": 526,
    "height": 688,
    "thumbnail": {
        "width": 229,
        "height": 300
    },
    "insightsSourcesSummary": {
        "shoppingSourcesCount": 0,
        "recipeSourcesCount": 0
    }
}, ...
```

Beroende på användarens enhet visar vanligt vis en delmängd av miniatyr bilderna, med ett alternativ som användaren kan använda för att [Växla mellan](./paging-search-results.md) de återstående bilderna.

<!-- Remove until this can be replaced with a sanitized version.
![List of thumbnail images](./media/cognitive-services-bing-web-api/bing-web-image-thumbnails.PNG)
-->

Du kan även expandera miniatyrbilden när användaren hovrar markören över den. Se till att tillskriva bildkällan om du expanderar bilden. Genom att till exempel extrahera värden från `hostPageDisplayUrl` och visa den under avbildningen. Information om att ändra storlek på miniatyrbilden finns i [Resizing and Cropping Thumbnails](./resize-and-crop-thumbnails.md) (Ändra storlek på och beskär miniatyrbilder).

<!-- Remove until this can be replaced with a sanitized version.
![Expanded view of thumbnail image](./media/cognitive-services-bing-web-api/bing-web-image-thumbnail-expansion.PNG)
-->

Om användaren klickar på miniatyren använder `webSearchUrl` du för att ta användaren till Bing-sidan Sök Resultat för bilder som innehåller ett collage av bilderna.

Mer information om bildsvar och avbildningar finns i [bildsökning API](../bing-image-search/overview.md).

## <a name="related-searches-answer"></a>Relaterade söknings svar

[RelatedSearches](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#searchresponse-relatedsearches) -svaret innehåller en lista över de populäraste relaterade frågorna från andra användare. Varje [fråga](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#query_obj) i listan innehåller en frågesträng ( `text` ), en frågesträng med träff markerings tecken ( `displayText` ) och en URL ( `webSearchUrl` ) till Bing: s Sök resultat sida för den frågan.

```json
{
    "text": "dinghy racing teams",
    "displayText": "dinghy racing teams",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=96C4CF214A0..."
}, ...
```

Använd `displayText` frågesträngen och `webSearchUrl` URL: en för att skapa en hyperlänk som tar användaren till Bing search-resultat sidan för den relaterade frågan. Du kan också använda `text` frågesträngen i din egen webbsökning API-fråga och visa resultatet själv.

Information om hur du hanterar markerings markörerna i `displayText` finns i [träff markering](../bing-web-search/hit-highlighting.md).

Nedan visas ett exempel på de relaterade frågor som används i Bing.com.

![Exempel på relaterade sökningar i Bing](./media/cognitive-services-bing-web-api/bing-web-rendered-relatedsearches.GIF)

## <a name="videos-answer"></a>Videoklipps svar

[Video](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#videos) svaret innehåller en lista över videor som Bing trodde var relevanta för frågan. Varje [video](/rest/api/cognitiveservices-bingsearch/bing-video-api-v7-reference#video) i listan innehåller URL: en för videon, dess varaktighet, dess dimensioner och dess kodnings format. Videoobjektet innehåller även webbadressen till en miniatyrbild av videon och miniatyrbildens mått.

```json
{
    "name": "Sailing dinghy",
    "description": "Northwind Traders is a 12 foot gunter rigged...",
    "webSearchUrl": "https:\/\/www.bing.com\/cr?IG=1CAE739681D84...",
    "thumbnailUrl": "https:\/\/tse2.mm.bing.net\/th?id=OVP.wsKiL...",
    "datePublished": "2013-11-06T01:56:28",
    "publisher": [{
        "name": "Fabrikam"
    }],
    "contentUrl": "https:\/\/www.fabrikam.com\/watch?v=MrVBWZpJjX",
    "hostPageUrl": "https:\/\/www.bing.com\/cr?IG=1CAE739681D8400DB...",
    "encodingFormat": "mp4",
    "hostPageDisplayUrl": "https:\/\/www.fabrikam.com\/watch?v=MrBWZpJjXo",
    "width": 1280,
    "height": 720,
    "duration": "PT3M47S",
    "motionThumbnailUrl": "https:\/\/tse2.mm.bing.net\/th?id=OM.oa...",
    "embedHtml": "<iframe width=\"1280\" height=\"720\" src=\"http:\/\/www....><\/iframe>",
    "allowHttpsEmbed": true,
    "viewCount": 19089,
    "thumbnail": {
        "width": 300,
        "height": 168
    },
    "allowMobileEmbed": true,
    "isSuperfresh": false
}, ...
```

Beroende på användarens enhet visar du oftast en delmängd av videor med ett alternativ för användaren att visa de återstående videor. Du kan visa en miniatyr bild av videon med videons längd, beskrivning (namn) och behörighet (utgivare).

<!-- Remove until this can be replaced with a sanitized version.
![List of video thumbnails](./media/cognitive-services-bing-web-api/bing-web-video-thumbnails.PNG)
-->

När användaren hovrar över miniatyren kan du använda `motionThumbnailUrl` för att spela upp en miniatyr version av videon. Se till att du tillskriver ursprunget när du visar en video.

<!-- Remove until this can be replaced with a sanitized version.
![Motion thumbnail of a video](./media/cognitive-services-bing-web-api/bing-web-video-motion-thumbnail.PNG)
-->

Om användaren klickar på miniatyrbilden finns följande visningsalternativ för videon:

- Använd `hostPageUrl` för att visa videon på värd webbplatsen (till exempel YouTube)
- Använd `webSearchUrl` för att visa videon i Bing Video Browser
- Använd `embedHtml` för att bädda in videon i din egen upplevelse

Mer information om video svar och videor finns [videosökning API](../bing-video-search/overview.md).

## <a name="news-answer"></a>Nyhets svar

[Nyhets](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#news) svaret innehåller en lista över nyhets artiklar som Bing trodde var relevant för frågan. Varje [nyhetsartikel](/rest/api/cognitiveservices-bingsearch/bing-news-api-v7-reference#newsarticle) i listan innehåller artikels namn, beskrivning och webbadressen till artikeln på värdens webbplats. Om artikeln innehåller en bild innehåller objektet även en miniatyr för bilden.

```json
{
    "name": "WC Sailing Qualifies for America Trophy with...",
    "url": "http:\/\/www.bing.com\/cr?IG=3445EEF15DAF4FFFBF7...",
    "image": {
        "contentUrl": "http:\/\/www.contoso.com\/sports\/sail...",
        "thumbnail": {
            "contentUrl": "https:\/\/www.bing.com\/th?id=ON.1...",
            "width": 400,
            "height": 272
        }
    },
    "description": "The WC sailing team qualified for a...",
    "provider": [{
        "_type": "Organization",
        "name": "contoso.com"
    }],
    "datePublished": "2017-04-16T21:56:00"
}, ...
```

Beroende på användarens enhet visas en delmängd av nyhets artiklarna med ett alternativ för användaren att visa de återstående artiklarna. Använd `name` och `url` till att skapa en hyperlänk som tar användaren till nyhetsartikeln på värdens webbplats. Om artikeln innehåller en bild kan du klicka på avbildningen med hjälp av `url` . Se till att du tillskriver artikeln med `provider`.

<!-- Remove until this can be replaced with a sanitized version.
The following shows an example of how you might display articles in a search results page.

![List of news articles](./media/cognitive-services-bing-web-api/bing-web-news-list.PNG)
-->

Mer information om nyhets svaret och nyhets artiklarna finns i [NYHETSSÖKNING API](../bing-news-search/search-the-web.md).

## <a name="computation-answer"></a>Beräknings svar

Om användaren anger ett matematiskt uttryck eller en enhets konverterings fråga kan svaret innehålla ett [beräknings](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#computation) svar. `computation`Svaret innehåller det normaliserade uttrycket och dess resultat.

En enhets konverterings fråga är en fråga som konverterar en enhet till en annan. Till exempel *hur många fötter i 10 meter?* eller *hur många tablespoons i en 1/4-bägare?*

Följande visar svaret på `computation` *hur många fötter i 10 meter?*

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "10 meters",
    "value": "32.808399 feet"
}, ...
```

Nedan visas exempel på matematiska frågor och deras motsvarande `computation` svar.

```
Query: (5+3)(10/2)+8
Encoded query: %285%2B3%29%2810%2F2%29%2B8
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "((5+3)*(10\/2))+8",
    "value": "48"
}
```

```
Query: sqrt(4^2+8^2)
Encoded query: sqrt%284^2%2B8^2%29
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
    "expression": "sqrt((4^2)+(8^2))",
    "value": "8.94427191"
}
```

```
Query: 30 6/8 - 18 8/16
Encoded query: 30%206%2F8%20-%2018%208%2F16
```

```json
"computation": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#WolframAlpha",
    "expression": "30 6\/8-18 8\/16",
    "value": "12.25"
}
```

```
Query: 8^2+11^2-2*8*11*cos(37)
Encoded query: 8^2%2B11^2-2*8*11*cos%2837%29
```

```json
"computation": {
        "id": "https:\/\/www.bing.com\/api\/v7\/#Computation",
        "expression": "(8^2)+(11^2)-(2*8*11*cos(37))",
        "value": "44.4401502"
}
```

Ett matematiskt uttryck kan innehålla följande symboler:

|Symbol|Beskrivning|
|------------|-----------------|
|+|Addition|
|-|Subtraktion|
|/|Division|
|*|Multiplikation|
|^|Ström|
|!|Fakultet|
|.|Decimal|
|()|Prioritets gruppering|
|[]|Funktion|

Ett matematiskt uttryck kan innehålla följande konstanter:

|Symbol|Beskrivning|
|------------|-----------------|
|Pi|3,14159...|
|Antal|Antal|
|i|Imaginärt nummer|
|e|e, 2,71828...|
|GoldenRatio|Gyllene förhållandet 1,61803...|

Ett matematiskt uttryck kan innehålla följande funktioner:

|Symbol|Beskrivning|
|------------|-----------------|
|Sortera|Kvadratrot|
|Sin [x], cos [x], Tan [x]<br />CSC [x], SEK [x], COT [x]|Trigonometriska funktioner (med argument i radianer)|
|Bågar i [x], ArcCos [x], ArcTan [x]<br />ArcCsc [x], ArcSec [x], ArcCot [x]|Invertera trigonometriska funktioner (ger resultat i radianer)|
|EXP [x], E ^ x|Exponentiell funktion|
|Logg [x]|Naturlig logaritm|
|Sinh [x], cosh [x], tanh [x]<br />Csch [x], sech [x], COTH [x]|Hyperboliska funktioner|
|ArcSinh [x], ArcCosh [x], ArcTanh [x]<br />ArcCsch [x], ArcSech [x], ArcCoth [x]|Inverterade hyperboliska funktioner|

Matematiska uttryck som innehåller variabler (till exempel 4x + 6 = 18, där x är variabeln) stöds inte.

## <a name="timezone-answer"></a>Svar på tidszon

Om användaren anger en tid eller datum fråga kan svaret innehålla ett [tids zons](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#timezone) svar. Det här svaret stöder implicita eller explicita frågor. En implicit fråga, till exempel *vilken tid är den?*, returnerar den lokala tiden baserat på användarens plats. En explicit fråga, till exempel *vilken tid som finns i Seattle?*, returnerar den lokala tiden för Seattle, WA.

`timeZone`Svaret innehåller namnet på platsen, aktuellt UTC-datum och tid på den angivna platsen och UTC-förskjutningen. Om platsens gräns ligger inom flera tids zoner, innehåller svaret det aktuella UTC-datumet och-tiden för alla tids zoner inom gränsen. Eftersom Florida-tillstånd ligger inom två tids zoner, innehåller svaret till exempel det lokala datumet och tiden för båda tids zonerna.  

Om frågan begär tiden för en delstat eller ett land/en region, fastställer Bing den primära staden inom platsens geografiska gräns och returnerar den i `primaryCityTime` fältet. Om gränsen innehåller flera tids zoner returneras återstående tids zoner i `otherCityTimes` fältet.

Följande exempel visar frågor som returnerar `timeZone` svaret.

```
Query: What time is it?

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Redmond, Washington, United States",
        "time": "2015-10-27T08:38:12.1189231Z",
        "utcOffset": "UTC-7"
    }
}

Query: What time is it in the Pacific time zone?

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Pacific Time Zone",
        "time": "2015-10-23T12:33:19.0728146Z",
        "utcOffset": "UTC-7"
    }
}

Query: Time in Florida?

"timeZone": {
        "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
        "primaryCityTime": {
            "location": "Tallahassee, Florida, United States",
            "time": "2015-10-23T13:04:56.6774389Z",
            "utcOffset": "UTC-4"
        },
        "otherCityTimes": [{
            "location": "Pensacola",
            "time": "2015-10-23T12:04:56.6664294Z",
            "utcOffset": "UTC-5"
        }]
}

Query: What time is it in the U.S.

"timeZone": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#TimeZone",
    "primaryCityTime": {
        "location": "Washington, D.C., United States",
        "time": "2015-10-23T15:27:59.8892745Z",
        "utcOffset": "UTC-4"
    },
    "otherCityTimes": [{
        "location": "Honolulu",
        "time": "2015-10-23T09:27:59.8892745Z",
        "utcOffset": "UTC-10"
    },
    {
        "location": "Anchorage",
        "time": "2015-10-23T11:27:59.8892745Z",
        "utcOffset": "UTC-8"
    },
    {
        "location": "Phoenix",
        "time": "2015-10-23T12:27:59.8892745Z",
        "utcOffset": "UTC-7"
    },
    {
        "location": "Los Angeles",
        "time": "2015-10-23T12:27:59.8942788Z",
        "utcOffset": "UTC-7"
    },
    {
        "location": "Denver",
        "time": "2015-10-23T13:27:59.8812681Z",
        "utcOffset": "UTC-6"
    },
    {
        "location": "Chicago",
        "time": "2015-10-23T14:27:59.8892745Z",
        "utcOffset": "UTC-5"
    }]
}
```

## <a name="spellsuggestion-answer"></a>SpellSuggestion-svar

Om Bing avgör att användaren kan ha tänkt att söka efter något annat, innehåller svaret ett [SpellSuggestions](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference#spellsuggestions) -objekt. Om användaren till exempel söker efter Carlos- *penna* kan Bing se till att användaren förmodligen vill söka efter Carlos-Pena i stället (baserat på tidigare sökningar av andra av *Carlos-pennan*). Nedan visas ett exempel på ett stavnings svar.

```json
"spellSuggestions": {
    "id": "https:\/\/www.bing.com\/api\/v7\/#SpellSuggestions",
    "value": [{
        "text": "carlos pena",
        "displayText": "carlos pena"
    }]
}, ...
```

## <a name="response-headers"></a>Svarshuvuden

Svar från API för webbsökning i Bing kan innehålla följande rubriker:

| Huvud | Beskrivning |
|-|-|
|`X-MSEdge-ClientID`|Det unika ID som Bing har tilldelat till användaren|
|`BingAPIs-Market`|Marknaden som användes för att uppfylla begäran|
|`BingAPIs-TraceId`|Logg posten på Bing API-servern för den här begäran (för support)|

Det är särskilt viktigt att spara klient-ID och returnera det till efterföljande begär Anden. När du gör detta använder sökningen tidigare kontext i ranknings Sök Resultat och ger också en konsekvent användar upplevelse.

Men när du anropar API för webbsökning i Bing från java script kan webbläsarens inbyggda säkerhetsfunktioner (CORS) förhindra åtkomst till värdena i dessa huvuden.

Om du vill få åtkomst till sidhuvudena kan du göra API för webbsökning i Bing begäran via en CORS-proxy. Svaret från en sådan proxy har ett `Access-Control-Expose-Headers` huvud som filtrerar svarshuvuden och gör dem tillgängliga för Java Script.

Det är enkelt att installera en CORS-proxy så att vår [självstudie](tutorial-bing-web-search-single-page-app.md) kan komma åt de valfria klient rubrikerna. [Installera Node.js](https://nodejs.org/en/download/) om du inte redan har det. Ange sedan följande kommando i en kommandotolk.

```console
npm install -g cors-proxy-server
```

Ändra sedan API för webbsökning i Bing-slutpunkten i HTML-filen till: \
`http://localhost:9090/https://api.cognitive.microsoft.com/bing/v7.0/search`

Slutligen startar du CORS-proxyn med följande kommando:

```console
cors-proxy-server
```

Lämna kommandofönstret öppet medan du använder självstudieappen. Om du stänger fönstret stoppas proxyn. I det expanderbara avsnittet om HTTP-huvuden nedan kan du nu se `X-MSEdge-ClientID`-huvudet (bland annat) under sökresultatet och du kan kontrollera att det är samma för varje begäran.

## <a name="response-headers-in-production"></a>Svars rubriker i produktion

Den CORS-proxy metoden som beskrivs i föregående svar är lämplig för utveckling, testning och inlärning.

I en produktions miljö bör du vara värd för ett skript på Server sidan på samma domän som webb sidan som använder API för webbsökning i Bing. Det här skriptet bör göra API-anrop på begäran från webb sidans Java Script och skicka alla resultat, inklusive huvuden, tillbaka till klienten. Eftersom de två resurserna (sida och skript) delar ett ursprung, används inte CORS och de särskilda rubrikerna är tillgängliga för Java Script på webb sidan.

Den här metoden skyddar också API-nyckeln från att exponeras för allmänheten, eftersom endast skript på Server sidan behöver det. Skriptet kan använda en annan metod för att se till att begäran är auktoriserad.

Följande visar hur Bing använder stavnings förslag.

![Exempel på Bing-stavnings förslag](./media/cognitive-services-bing-web-api/bing-web-spellingsuggestion.GIF)  

## <a name="next-steps"></a>Nästa steg  

* Läs dokumentationen om [begränsning av begäran](throttling-requests.md) .  

## <a name="see-also"></a>Se även  

* [API för webbsökning i Bing referens](/rest/api/cognitiveservices-bingsearch/bing-web-api-v7-reference)