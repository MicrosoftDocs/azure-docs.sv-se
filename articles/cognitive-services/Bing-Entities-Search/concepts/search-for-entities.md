---
title: Söka efter entiteter med API:t Entitetssökning i Bing
titleSuffix: Azure Cognitive Services
description: Använd API för entitetsökning i Bing för att extrahera och söka efter entiteter och platser från sökfrågor.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-entity-search
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: aahi
ms.openlocfilehash: 9dabceda17defb24f2a916cd641f625feb551c6a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96353297"
---
# <a name="searching-for-entities-with-the-bing-entity-api"></a>Söka efter entiteter med entitets-API:t i Bing

> [!WARNING]
> API:er för Bing-sökresultat flyttas från Cognitive Services till Bing-sökning tjänster. Från och med den **30 oktober 2020** måste alla nya instanser av Bing-sökning tillhandahållas enligt processen som dokumenteras [här](/bing/search-apis/bing-web-search/create-bing-search-service-resource).
> API:er för Bing-sökresultat som har tillhandahållits med hjälp av Cognitive Services kommer att stödjas under de kommande tre åren eller tills Enterprise-avtals slut, beroende på vilket som sker först.
> Instruktioner för migrering finns i [Bing-sökning Services](/bing/search-apis/bing-web-search/create-bing-search-service-resource).

## <a name="suggest-search-terms-with-the-bing-autosuggest-api"></a>Föreslå söktermer med API:t Automatiska förslag i Bing

Om du tillhandahåller en sökruta där användaren anger sin sökterm bör du använda [API för automatiska förslag i Bing ](../../bing-autosuggest/get-suggested-search-terms.md) för att ge bättre funktioner. API:t returnerar föreslagna frågesträngar baserat på partiella söktermer som användaren skriver in.

När användaren har angett sin sökterm kodar URL:en termen innan den ställer in [q](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#query) frågeparametern. Om användaren till exempel anger *Marcus Appel* anger du `q` till *Marcus + Appel* eller *Marcus%20Appel*.

Om söktermen innehåller en felstavning inkluderar söksvaret ett [QueryContext](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#querycontext)-objekt. Objektet visar den ursprungliga stavningen och den korrigerade stavningen som Bing använde för sökningen.

```json
"queryContext": {
    "originalQuery": "hollo wrld",
    "alteredQuery": "hello world",
    "alterationOverrideQuery": "+hollo wrld",
    "adultIntent": false
}
```

## <a name="the-bing-entity-search-api-response"></a>Svar i API:t Entitetssökning i Bing

API-svaret innehåller ett [SearchResponse](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#searchresponse)-objekt. Om Bing hittar en entitet eller plats som är relevant innehåller objektet fältet `entities`, fältet `places` eller båda. Annars innehåller inte svarsobjektet något av fälten.
> [!NOTE]
> Entitetssvar stöder flera marknader, men svaret för platser stöder endast företagsplatser i USA. 

Fältet `entities` är ett [EntityAnswer](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)-objekt som innehåller en lista över [Entity](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entity)-objekt (se fältet `value`). Listan kan innehålla en enda dominerande entitet, flera entiteter som löser tvetydigheter eller båda. 

En dominerande entitet returneras när Bing tror att det är den enda entitet som uppfyller begäran (det finns inga tvetydigheter om vilken entitet som uppfyller begäran). Om flera entiteter kan uppfylla begäran innehåller listan mer än en entitet som löser tvetydigheter. Om denna begäran till exempel använder den generiska titeln för en filmserie innehåller listan sannolikt entiteter som löser tvetydigheter. Men om begäran anger en viss titel från serien innehåller listan sannolikt en enda dominerande enhet.

Entiteter innehåller välkända personer såsom sångare, skådespelare, idrottare, modeller osv., platser och landmärken såsom Mount Rainier eller Lincoln Memorial samt saker såsom en banan, goldendoodle, bok eller filmtitel. Fältet [entityPresentationInfo](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entitypresentationinfo) innehåller tips som identifierar entitetens typ. Exempel kan vara huruvida den är en person, en film, ett djur eller en attraktion. En lista över möjliga typer finns i [Entitetstyper](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entity-types)

```json
"entityPresentationInfo": {
    "entityScenario": "DominantEntity",
    "entityTypeHints": ["Attraction"],
    "entityTypeDisplayHint": "Mountain"
}, ...
```

Nedan visas ett svar som innehåller en dominerande entitet och en entitet som löser tvetydigheter.

```json
{
    "_type": "SearchResponse",
    "queryContext": {
        "originalQuery": "Mount Rainier"
    },
    "entities": {
        "value": [{
            "contractualRules": [{
                "_type": "ContractualRules/LicenseAttribution",
                "targetPropertyName": "description",
                "mustBeCloseToContent": true,
                "license": {
                    "name": "CC-BY-SA",
                    "url": "https://creativecommons.org/licenses/by-sa/3.0/"
                },
                "licenseNotice": "Text under CC-BY-SA license"
            },
            {
                "_type": "ContractualRules/LinkAttribution",
                "targetPropertyName": "description",
                "mustBeCloseToContent": true,
                "text": "contoso.com",
                "url": "http://contoso.com/mount_rainier"
            },
            {
                "_type": "ContractualRules/MediaAttribution",
                "targetPropertyName": "image",
                "mustBeCloseToContent": true,
                "url": "http://contoso.com/mount-rainier"
            }],
            "webSearchUrl": "https://www.bing.com/search?q=Mount%20Rainier...",
            "name": "Mount Rainier",
            "url": "http://www.northwindtraders.com/",
            "image": {
                "name": "Mount Rainier",
                "thumbnailUrl": "https://www.bing.com/th?id=A4ae343983daa4...",
                "provider": [{
                    "_type": "Organization",
                    "url": "http://contoso.com/mount_rainier"
                }],
                "hostPageUrl": "http://contoso.com/commons/7/72/mount_rain...",
                "width": 110,
                "height": 110
            },
            "description": "Mount Rainier is 14,411 ft tall and the highest mountain...",
            "entityPresentationInfo": {
                "entityScenario": "DominantEntity",
                "entityTypeHints": ["Attraction"]
            },
            "bingId": "38b9431e-cf91-93be-0584-c42a3ecbfdc7"
        },
        {
            "contractualRules": [{
                "_type": "ContractualRules/MediaAttribution",
                "targetPropertyName": "image",
                "mustBeCloseToContent": true,
                "url": "http://contoso.com/mount_rainier_national_park"
            }],
            "webSearchUrl": "https://www.bing.com/search?q=Mount%20Rainier%20National...",
            "name": "Mount Rainier National Park",
            "url": "http://worldwideimporters.com/",
            "image": {
                "name": "Mount Rainier National Park",
                "thumbnailUrl": "https://www.bing.com/th?id=A91bdc5a1b648a695a39...",
                "provider": [{
                    "_type": "Organization",
                    "url": "http://contoso.com/mount_rainier_national_park"
                }],
                "hostPageUrl": "http://contoso.com/en/7/7a...",
                "width": 50,
                "height": 50
            },
            "description": "Mount Rainier National Park is a United States National Park...",
            "entityPresentationInfo": {
                "entityScenario": "DisambiguationItem",
                "entityTypeHints": ["Organization"]
            },
            "bingId": "29d4b681-227a-3924-7bb1-8a54e8666b8c"
        }]
    }
}
```

Entiteten innehåller fälten `name`, `description` och `image`. När du visar de här fälten i användarfunktionen måste du attributera dem. Fältet `contractualRules` innehåller en lista över attributioner som du måste tillämpa. Kontraktsregeln identifierar det fält som attributionen gäller för. Information om hur du tillämpar attribution finns i [Attribution](#data-attribution).

```json
"contractualRules": [{
    "_type": "ContractualRules/LicenseAttribution",
    "targetPropertyName": "description",
    "mustBeCloseToContent": true,
    "license": {
        "name": "CC-BY-SA",
        "url": "https://creativecommons.org/licenses/by-sa/3.0/"
    },
    "licenseNotice": "Text under CC-BY-SA license"
},
{
    "_type": "ContractualRules/LinkAttribution",
    "targetPropertyName": "description",
    "mustBeCloseToContent": true,
    "text": "contoso.com",
    "url": "http://contoso.com/wiki/Mount_Rainier"
},
{
    "_type": "ContractualRules/MediaAttribution",
    "targetPropertyName": "image",
    "mustBeCloseToContent": true,
    "url": "http://contoso.com/wiki/Mount_Rainier"
}], ...
```

När du visar entitetsinformationen (namn, beskrivning och bild) måste du även använda URL:en i fältet `webSearchUrl` för att länka till den Bing-sökresultatsida som innehåller entiteten.

## <a name="find-places"></a>Söka efter platser

Fältet `places` är ett [LocalEntityAnswer](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference)-objekt som innehåller en lista med [Place](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#place)-objekt (mer information finns i [Etitetstyper](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entity-types)). Listan innehåller en eller flera lokala entiteter som uppfyller begäran.

Platser omfattar restauranger, hotell och lokala företag. Fältet [entityPresentationInfo](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entitypresentationinfo) innehåller tips som identifierar den lokala entitetens typ. Listan innehåller en lista över tips såsom plats, lokalt företag och restaurang. Varje efterföljande tips i matrisen begränsar entitetens typ. En lista över möjliga typer finns i [Entitetstyper](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#entity-types)

```json
"entityPresentationInfo": {
    "entityScenario": "ListItem",
    "entityTypeHints": ["Place",
    "LocalBusiness",
    "Restaurant"]
}, ...
```
> [!NOTE]
> Entitetssvar stöder flera marknader, men svaret för platser stöder endast företagsplatser i USA. 

Platsmedvetna entitetsfrågor såsom *restaurang nära mig* kräver användarens plats för att tillhandahålla korrekta resultat. Dina begäranden bör alltid använda sidhuvidena X-Search-Location och X-MSEdge-ClientIP för att ange användarens plats. Om Bing tror frågan skulle kunna dra nytta av användarens plats anger fältet `askUserForLocation` i [QueryContext](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#querycontext) till **sant**. 

```json
{
    "_type": "SearchResponse",
    "queryContext": {
        "originalQuery": "Sinful Bakery and Cafe",
        "askUserForLocation": true
    },
    ...
}
```

Platsresultat innehåller platsens namn, adress, telefonnummer och URL till entitetens webbplats. När du visar entitetsinformationen måste du även använda URL:en i fältet `webSearchUrl` för att länka till den Bing-sökresultatsida som innehåller entiteten.

```json
"places": {
    "value": [{
        "_type": "Restaurant",
        "webSearchUrl": "https://www.bing.com/search?q=Sinful%20Bakery...",
        "name": "Liberty's Delightful Sinful Bakery & Cafe",
        "url": "http://libertysdelightfulsinfulbakeryandcafe.com/",
        "entityPresentationInfo": {
            "entityScenario": "ListItem",
            "entityTypeHints": ["Place",
            "LocalBusiness",
            "Restaurant"]
        },
        "address": {
            "addressLocality": "Seattle",
            "addressRegion": "WA",
            "postalCode": "98112",
            "addressCountry": "US",
            "neighborhood": "Madison Park"
        },
        "telephone": "(800) 555-1212"
    }]
}
```

> [!NOTE]
> Du eller en tredje part för din räkning får inte använda, behålla, lagra, cachelagra, dela eller distribuera data från entitets-API:et i syfte att testa, utveckla, träna, distribuera eller tillgängliggöra tjänster eller funktioner som inte kommer från Microsoft.  

## <a name="data-attribution"></a>Dataattribution

Bings entitets-API-svar innehåller information som ägs av tredje part. Du ansvarar för att se till att din användning är lämplig, till exempel genom att följa gällande Creative Commons-licenser som din användarfunktion är beroende av.

Om ett svar eller resultat innehåller fälten `contractualRules`, `attributions` eller `provider` måste du attributera data. Om svaret inte innehåller några av de här fälten krävs ingen attribution. Om svaret innehåller fältet `contractualRules` och fälten `attributions` och/eller `provider` måste du använda kontraktsreglerna för att attributera data.

I följande exempel visas en entitet som innehåller en MediaAttribution-kontraktsregel och en bild som innehåller ett `provider`-fält. MediaAttribution-regeln identifierar bilden som målet för regeln, så du ignorerar bildens `provider`-fält och använder i stället MediaAttribution-regeln för att ge attribution.  

```json
"value": [{
    "contractualRules": [
        ...
        {
            "_type": "ContractualRules/MediaAttribution",
            "targetPropertyName": "image",
            "mustBeCloseToContent": true,
            "url": "http://contoso.com/mount_rainier"
        }
    ],
    ...
    "image": {
        "name": "Mount Rainier",
        "thumbnailUrl": "https://www.bing.com/th?id=A46378861201...",
        "provider": [{
            "_type": "Organization",
            "url": "http://contoso.com/mount_rainier"
        }],
        "hostPageUrl": "http://www.graphicdesigninstitute.com/Uploaded...",
        "width": 110,
        "height": 110
    },
    ...
}]
```

Om en kontraktsregel innehåller fältet `targetPropertyName` gäller regeln endast för målfältet. Annars gäller regeln för det överordnade objekt som innehåller fältet `contractualRules`.

I följande exempel innehåller regeln `LinkAttribution` fältet `targetPropertyName`, så regeln gäller för fältet `description`. För regler som gäller för specifika fält måste du inkludera en rad som följer omedelbart efter måldata som innehåller en hyperlänk till källans webbplats. Exempel: för att attributera beskrivningen inkluderar du en rad omedelbart efter den beskrivande texten som innehåller en hyperlänk till data på källans webbplats. I det här fallet skapar du en länk till contoso.com.

```json
"entities": {
    "value": [{
            ...
            "description": "Marcus Appel is a former American....",
            ...
            "contractualRules": [{
                    "_type": "ContractualRules/LinkAttribution",
                    "targetPropertyName": "description",
                    "mustBeCloseToContent": true,
                    "text": "contoso.com",
                    "url": "http://contoso.com/cr?IG=B8AD73..."
                 },
            ...
  
```

### <a name="license-attribution"></a>Licensattribution

Om listan över kontraktsregler innehåller en [LicenseAttribution](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#licenseattribution)-regel måste du visa meddelandet på raden direkt efter det innehåll som licensen gäller för. `LicenseAttribution`-regeln använder fältet `targetPropertyName` för att identifiera den egenskap som licensen gäller för.

Följande visar ett exempel som innehåller en `LicenseAttribution`-regel.

![Licensattribution](../media/cognitive-services-bing-entities-api/licenseattribution.png)

Det licensmeddelande som du visar måste innehålla en hyperlänk till den webbplats som innehåller information om licensen. Normalt gör du namnet på licensen till en hyperlänk. Exempel: om meddelandet är **Text under CC-AV-SA-licensen** och CC-AV-SA är namnet på licensen, gör du CC-AV-SA till en hyperlänk.

### <a name="link-and-text-attribution"></a>Länk- och textattribution

Reglerna [LinkAttribution](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#linkattribution) och [TextAttribution](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#textattribution) används vanligtvis för att identifiera källan till data. Fältet `targetPropertyName` identifierar det fält som regeln gäller för.

För att attributera källorna inkluderar du en rad omedelbart efter det innehåll som attributionerna gäller för (till exempel målfältet). Raden ska vara tydligt märkt för att visa källorna till data. Ett exempel kan vara ”Data från: contoso.com”. För `LinkAttribution`-regler måste du skapa en hyperlänk till källans webbplats.

Följande visar ett exempel som innehåller `LinkAttribution`- och `TextAttribution`-regler.

![Länktextattribution](../media/cognitive-services-bing-entities-api/linktextattribution.png)

### <a name="media-attribution"></a>Medieattribution

Om entiteten innehåller en bild och du visar den måste du tillhandahålla en klickbar länk till källans webbplats. Om entiteten innehåller en [MediaAttribution](/rest/api/cognitiveservices-bingsearch/bing-entities-api-v7-reference#mediaattribution)-regel använder du regelns URL för att skapa den klickbara länken. Annars använder du den URL som ingår i bildens `provider`-fält för att skapa den klickbara länken.

Följande visar ett exempel som innehåller en bilds `provider`-fält och kontraktsregler. Eftersom exemplet innehåller kontraktsregeln ignorerar du bildens `provider`-fält och tillämpar `MediaAttribution`-regeln.

![Medieattribution](../media/cognitive-services-bing-entities-api/mediaattribution.png)

### <a name="search-or-search-like-experience"></a>Sökfunktion eller sökningsliknande funktion

Precis som med API:et för webbsökning i Bing får API:et för entitetsökning i Bing bara användas som resultat av en direkt användarfråga eller -sökning eller som resultat av en åtgärd i en app eller en upplevelse som logiskt kan tolkas som en användares sökbegäran. För tydlighetens skull följer här några exempel på godkända sökfunktioner eller sökningsliknande funktioner.

- Användaren anger en fråga direkt i en sökruta i en app
- Användaren markerar viss text eller en bild och begär ”läs mer” eller ”mer information”
- Användaren frågar en sökrobot om ett visst ämne
- Användaren funderar på ett visst objekt eller en viss entitet i ett scenario med visuell sökning

Om du inte är säker på om funktionen kan betraktas som en sökningsliknande funktion rekommenderar vi att du kontaktar Microsoft.

## <a name="throttling-requests"></a>Begränsningsbegäranden

[!INCLUDE [cognitive-services-bing-throttling-requests](../../../../includes/cognitive-services-bing-throttling-requests.md)]

## <a name="next-steps"></a>Nästa steg

* Prova en [snabbstart](../quickstarts/csharp.md) om du vill komma igång med att söka efter entiteter med API:t Entitetssökning i Bing.