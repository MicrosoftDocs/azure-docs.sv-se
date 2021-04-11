---
title: Identifiera språk med Textanalys REST API
titleSuffix: Azure Cognitive Services
description: Identifiera språk genom att använda Textanalys REST API från Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: sample
ms.date: 04/02/2021
ms.author: aahi
ms.openlocfilehash: e2148f56c216795c5022b86b6a1d90b476a4672e
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106277291"
---
# <a name="example-detect-language-with-text-analytics"></a>Exempel: identifiera språk med Textanalys

[Språkidentifiering](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) funktionen i Azure textanalys REST API utvärderar text ingångar för varje dokument och returnerar språk identifierare med en poäng som visar analys styrkan.

Den här funktionen är användbar för innehållslager samlar in godtycklig text, där språket är okänt. Du kan parsa resultatet av den här analysen för att avgöra vilket språk som används i dokumentet. Svaret returnerar också en poäng som återspeglar modellens tillförlitlighet. Poäng svärdet är mellan 0 och 1.

Språkidentifiering funktionen kan identifiera en mängd olika språk, varianter, dialekter och vissa regionala eller kulturella språk. Den exakta listan över språk för den här funktionen har inte publicerats.

Om du har innehåll som uttryckts på ett mindre vanligt språk kan du prova Språkidentifiering funktionen för att se om den returnerar en kod. Svaret för språk som inte kan identifieras är `unknown` .

> [!TIP]
> Textanalys ger även en Linux-baserad Docker-containeravbildning för språkidentifiering, så att du kan [installera och köra Textanalys-containern](text-analytics-how-to-install-containers.md) nära dina data.

## <a name="preparation"></a>Förberedelse

Du måste ha JSON-dokument i det här formatet: ID och text.

Dokument storleken måste vara under 5 120 tecken per dokument. Du kan ha upp till 1 000 objekt (ID) per samling. Samlingen skickas i begäranstexten. Följande exempel är ett exempel på innehåll som du kan skicka för språk identifiering:

```json
{
    "documents": [
        {
            "id": "1",
            "text": "This document is in English."
        },
        {
            "id": "2",
            "text": "Este documento está en inglés."
        },
        {
            "id": "3",
            "text": "Ce document est en anglais."
        },
        {
            "id": "4",
            "text": "本文件为英文"
        },
        {
            "id": "5",
            "text": "Этот документ на английском языке."
        }
    ]
}
```

## <a name="step-1-structure-the-request"></a>Steg 1: Strukturera begäran

Mer information om definition av begäran finns i [anropa API för textanalys](text-analytics-how-to-call-api.md). Följande punkter har anges på nytt för enkelhetens skull:

+ Skicka en POST-begäran. Information om hur du granskar API-dokumentationen för den här begäran finns i [SPRÅKIDENTIFIERING API](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages).

+ Ange HTTP-slutpunkt för språkidentifiering. Använd antingen en Textanalys-resurs på Azure eller en instansierad [textanalys-behållare](text-analytics-how-to-install-containers.md). Du måste ta med `/text/analytics/v3.0/languages` i URL: en. Exempel: `https://<your-custom-subdomain>.cognitiveservices.azure.com/text/analytics/v3.0/languages`.

+ Ange ett rubrik för begäran för att inkludera [åtkomst nyckeln](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) för textanalys åtgärder.

+ Ange den JSON-dokumentsamling som du har förberett för den här analysen i begärandetexten.

> [!Tip]
> Använd [Postman](text-analytics-how-to-call-api.md) eller öppna **API-testkonsolen** i [dokumentationen](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) för att strukturera en begäran och skicka den till tjänsten.

## <a name="step-2-post-the-request"></a>Steg 2: publicera begäran

Analysen utförs när begäran har tagits emot. Information om storlek och antal begär Anden som du kan skicka per minut och sekund finns i artikeln [data begränsningar](../concepts/data-limits.md) .

Kom ihåg att tjänsten är tillståndslös. Inga data lagras i ditt konto. Resultaten returneras omedelbart i svaret.


## <a name="step-3-view-the-results"></a>Steg 3: Visa resultaten

Alla POST-förfrågningar returnerar ett JSON-formaterat svar med ID: n och identifierade egenskaper.

Utdata returneras direkt. Du kan strömma resultaten till ett program som accepterar JSON eller spara utdata till en fil på det lokala systemet. Importera sedan utdata till ett program som du kan använda för att sortera, söka och ändra data.

Resultat för exempelbegäran bör se ut som följande JSON. Observera att det är ett dokument med flera objekt. Utdata är på engelska. Språkidentifierarna innehåller ett eget namn och en språkkod i [ISO 639-1](https://www.iso.org/standard/22109.html)-format.

Ett positivt resultat på 1.0 uttrycker högsta möjliga förtroendenivå för analysen.

```json
{
    "documents":[
        {
            "detectedLanguage":{
                "confidenceScore":0.99,
                "iso6391Name":"en",
                "name":"English"
            },
            "id":"1",
            "warnings":[
                
            ]
        },
        {
            "detectedLanguage":{
                "confidenceScore":1.0,
                "iso6391Name":"es",
                "name":"Spanish"
            },
            "id":"2",
            "warnings":[
                
            ]
        },
        {
            "detectedLanguage":{
                "confidenceScore":1.0,
                "iso6391Name":"fr",
                "name":"French"
            },
            "id":"3",
            "warnings":[
                
            ]
        },
        {
            "detectedLanguage":{
                "confidenceScore":1.0,
                "iso6391Name":"zh_chs",
                "name":"Chinese_Simplified"
            },
            "id":"4",
            "warnings":[
                
            ]
        },
        {
            "detectedLanguage":{
                "confidenceScore":1.0,
                "iso6391Name":"ru",
                "name":"Russian"
            },
            "id":"5",
            "warnings":[
                
            ]
        }
    ],
    "errors":[
        
    ],
    "modelVersion":"2020-09-01"
}
```

### <a name="ambiguous-content"></a>Tvetydig innehåll

I vissa fall kan det vara svårt att disambiguate språk baserat på indatamängden. Du kan använda `countryHint` -parametern för att ange en landskod på 2 bokstäver. Som standard använder API: t "US" som standard-countryHint, för att ta bort det här alternativet kan du återställa den här parametern genom att ange värdet till en tom sträng `countryHint = ""` .

Till exempel är "omöjlig" gemensamt för både engelska och franska och om det ges med begränsad kontext, kommer svaret att baseras på "US"-lands-/region tipset. Om det är fastställt att textens ursprung är franskt, så kan detta ges som ett tips.

**Indata**

```json
{
    "documents": [
        {
            "id": "1",
            "text": "impossible"
        },
        {
            "id": "2",
            "text": "impossible",
            "countryHint": "fr"
        }
    ]
}
```

Tjänsten har nu ytterligare kontext för att göra en bättre bedömning: 

**Resultat**

```json
{
    "documents":[
        {
            "detectedLanguage":{
                "confidenceScore":0.62,
                "iso6391Name":"en",
                "name":"English"
            },
            "id":"1",
            "warnings":[
                
            ]
        },
        {
            "detectedLanguage":{
                "confidenceScore":1.0,
                "iso6391Name":"fr",
                "name":"French"
            },
            "id":"2",
            "warnings":[
                
            ]
        }
    ],
    "errors":[
        
    ],
    "modelVersion":"2020-09-01"
}
```

Om analysen inte kan parsa inmataren returneras `(Unknown)` . Ett exempel är om du skickar ett textblock som består av enbart arabiska siffror.

```json
{
    "documents":[
        {
            "detectedLanguage":{
                "confidenceScore":0.0,
                "iso6391Name":"(Unknown)",
                "name":"(Unknown)"
            },
            "id":"1",
            "warnings":[
                
            ]
        }
    ],
    "errors":[
        
    ],
    "modelVersion":"2020-09-01"
}
```

### <a name="mixed-language-content"></a>Blandat språk innehåll

Blandat språk innehåll i samma dokument returnerar det språk som har störst representation i innehållet, men med en lägre positiv klassificering. Omdömet visar marginal styrkan i utvärderingen. I följande exempel är indata en blandning av engelska, spanska och franska. Det dominerande språket fastställs genom att analysatorn räknar tecken i varje segment.

**Indata**

```json
{
    "documents": [
        {
            "id": "1",
            "text": "Hello, I would like to take a class at your University. ¿Se ofrecen clases en español? Es mi primera lengua y más fácil para escribir. Que diriez-vous des cours en français?"
        }
    ]
}
```

**Resultat**

Resultatet består av det dominerande språket, med en poäng på mindre än 1,0, vilket tyder på en svagare nivå av förtroende.

```json
{
    "documents":[
        {
            "detectedLanguage":{
                "confidenceScore":0.94,
                "iso6391Name":"es",
                "name":"Spanish"
            },
            "id":"1",
            "warnings":[
                
            ]
        }
    ],
    "errors":[
        
    ],
    "modelVersion":"2020-09-01"
}
```

## <a name="summary"></a>Sammanfattning

I den här artikeln har du lärt dig begrepp och arbets flöde för språk identifiering genom att använda Textanalys i Azure Cognitive Services. Följande punkter förklarades och demonstreras:

+ [Språk identifiering](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/Languages) är tillgängligt för en mängd olika språk, varianter, dialekter och vissa regionala eller kulturella språk.
+ JSON-dokument i begär ande texten innehåller ett ID och text.
+ POST-begäran är till en `/languages` slut punkt genom att använda en anpassad [åtkomst nyckel och en slut punkt](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) som är giltig för din prenumeration.
+ Svars utdata består av språk identifierare för varje dokument-ID. Utdata kan strömmas till alla appar som accepterar JSON. Exempel på appar är Excel och Power BI, så att du kan namnge några.

## <a name="see-also"></a>Se även

* [Översikt över Textanalys](../overview.md)
* [Använda klient biblioteket för Textanalys](../quickstarts/client-libraries-rest-api.md)
* [Nyheter](../whats-new.md)
