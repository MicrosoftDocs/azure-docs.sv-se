---
title: Extrahering av nyckel fraser med Textanalys REST API
titleSuffix: Azure Cognitive Services
description: Extrahera nyckel fraser genom att använda Textanalys REST API från Azure Cognitive Services.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 12/15/2020
ms.author: aahi
ms.openlocfilehash: e5d25e71e4700f3f327319e4f444d2060c7ab5f6
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97561893"
---
# <a name="example-how-to-extract-key-phrases-using-text-analytics"></a>Exempel: extrahera viktiga fraser med Textanalys

[API:et för extrahering av nyckelfras](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) utvärderar ostrukturerad text och returnerar en lista med nyckelfraser för varje JSON-dokument.

Den här funktionen är användbar om du snabbt behöver identifiera de viktigaste punkterna i en samling av dokument. Exempel: med den inmatade texten ”Maten var härlig och personalen var underbar” returnerar API:et de huvudsakliga diskussionsämnena: ”mat” och ”underbar personal”.

Mer information finns i [språk som stöds](../language-support.md).

> [!TIP]
> * Textanalys ger även en Linux-baserad Docker-containeravbildning för nyckelfrasextrahering, så att du kan [installera och köra Textanalys-containern](text-analytics-how-to-install-containers.md) nära dina data.
> * Du kan också använda den här funktionen [asynkront](text-analytics-how-to-call-api.md) med `/analyze` slut punkten.

## <a name="preparation"></a>Förberedelse

[!INCLUDE [v3 region availability](../includes/v3-region-availability.md)]

Extrahering av nyckelfraser fungerar bäst när du ger den en större mängd text att arbeta med. Det här är motsatt från känsloanalys som fungerar bättre på mindre mängder text. För att få bästa resultat från båda åtgärderna kan du överväga omstrukturering av indata på lämpligt sätt.

Du måste ha JSON-dokument i det här formatet: ID, text, språk

Dokument storleken måste vara 5 120 eller färre tecken per dokument och du kan ha upp till 1 000 objekt (ID) per samling. Samlingen skickas i begäranstexten. Följande exempel är en illustration av innehåll som du kan skicka in för extrahering av nyckelfraser. 

Mer information om begär ande-och svars objekt finns i [så här anropar du API för textanalys](text-analytics-how-to-call-api.md) .  

### <a name="example-synchronous-request-object"></a>Exempel på synkront begär ande objekt


```json
    {
        "documents": [
            {
                "language": "en",
                "id": "1",
                "text": "We love this trail and make the trip every year. The views are breathtaking and well worth the hike!"
            },
            {
                "language": "en",
                "id": "2",
                "text": "Poorly marked trails! I thought we were goners. Worst hike ever."
            },
            {
                "language": "en",
                "id": "3",
                "text": "Everyone in my family liked the trail but thought it was too challenging for the less athletic among us. Not necessarily recommended for small children."
            },
            {
                "language": "en",
                "id": "4",
                "text": "It was foggy so we missed the spectacular views, but the trail was ok. Worth checking out if you are in the area."
            },
            {
                "language": "en",
                "id": "5",
                "text": "This is my favorite trail. It has beautiful views and many places to stop and rest"
            }
        ]
    }
```

### <a name="example-asynchronous-request-object"></a>Exempel på asynkront begär ande objekt

Från `v3.1-preview.3` och med kan du skicka ner-begäranden asynkront med `/analyze` slut punkten.


```json
{
    "displayName": "My Job",
    "analysisInput": {
        "documents": [
            {
                "id": "doc1",
                "text": "It's incredibly sunny outside! I'm so happy"
            },
            {
                "id": "doc2",
                "text": "Pike place market is my favorite Seattle attraction."
            }
        ]
    },
    "tasks": {
        "keyPhraseExtractionTasks": [{
            "parameters": {
                "model-version": "latest"
            }
        }],
    }
}
```

## <a name="step-1-structure-the-request"></a>Steg 1: Strukturera begäran

Information om definition av begäran finns i [så här anropar du API för textanalys](text-analytics-how-to-call-api.md). Följande punkter har anges på nytt för enkelhetens skull:

+ Skicka en **POST**-begäran. Läs API-dokumentationen för denna begäran: [API för nyckel fraser](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases).

+ Ange HTTP-slutpunkten för extrahering av nyckel fraser genom att antingen använda en Textanalys-resurs på Azure eller en instansierad [textanalys-behållare](text-analytics-how-to-install-containers.md). Om du använder API: et synkront måste du ta med `/text/analytics/v3.0/keyPhrases` i URL: en. Till exempel: `https://<your-custom-subdomain>.api.cognitiveservices.azure.com/text/analytics/v3.0/keyPhrases`.

+ Ange ett rubrik för begäran för att inkludera [åtkomst nyckeln](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) för textanalys åtgärder.

+ Ange den JSON-dokumentsamling som du har förberett för den här analysen i begärandetexten.

> [!Tip]
> Använd [Postman](text-analytics-how-to-call-api.md) eller öppna **API-testkonsolen** i [dokumentationen](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) för att strukturera en begäran och skicka den till tjänsten.

## <a name="step-2-post-the-request"></a>Steg 2: Publicera begäran

Analysen utförs när begäran har tagits emot. Information om storlek och antal begär Anden som du kan skicka per minut eller per sekund finns i avsnittet [data begränsningar](../overview.md#data-limits) i översikten.

Kom ihåg att tjänsten är tillståndslös. Inga data lagras i ditt konto. Resultaten returneras omedelbart i svaret.

## <a name="step-3-view-results"></a>Steg 3: Visa resultat

Alla POST-begäranden returnerar ett JSON-formaterat svar med ID:n och identifierade egenskaper. Ordningen på de returnerade nyckel fraserna fastställs internt av modellen.

Utdata returneras direkt. Du kan strömma resultaten till ett program som stöder JSON eller spara utdata till en fil på den lokala datorn och sedan importera den till ett program som gör så att du kan sortera, söka och hantera data.

Ett exempel på utdata för extrahering av nyckel fraser från v 3.1 – för hands version. 2 slut punkt visas här:

### <a name="synchronous-result"></a>Synkront resultat

```json
    {
       "documents":[
          {
             "id":"1",
             "keyPhrases":[
                "year",
                "trail",
                "trip",
                "views",
                "hike"
             ],
             "warnings":[]
          },
          {
             "id":"2",
             "keyPhrases":[
                "marked trails",
                "Worst hike",
                "goners"
             ],
             "warnings":[]
          },
          {
             "id":"3",
             "keyPhrases":[
                "trail",
                "small children",
                "family"
             ],
             "warnings":[]
          },
          {
             "id":"4",
             "keyPhrases":[
                "spectacular views",
                "trail",
                "Worth",
                "area"
             ],
             "warnings":[]
          },
          {
             "id":"5",
             "keyPhrases":[
                "places",
                "beautiful views",
                "favorite trail",
                "rest"
             ],
             "warnings":[]
          }
       ],
       "errors":[],
       "modelVersion":"2020-07-01"
    }

```
Som anges söker analysen och tar bort icke-viktiga ord, och det behåller enkla termer eller fraser som visas som ämne eller objekt i en mening.

### <a name="asynchronous-result"></a>Asynkront resultat

Om du använder `/analyze` slut punkten för asynkron åtgärd får du ett svar som innehåller de uppgifter som du har skickat till API: et.

```json
{
  "displayName": "My Analyze Job",
  "jobId": "dbec96a8-ea22-4ad1-8c99-280b211eb59e_637408224000000000",
  "lastUpdateDateTime": "2020-11-13T04:01:14Z",
  "createdDateTime": "2020-11-13T04:01:13Z",
  "expirationDateTime": "2020-11-14T04:01:13Z",
  "status": "running",
  "errors": [],
  "tasks": {
      "details": {
          "name": "My Analyze Job",
          "lastUpdateDateTime": "2020-11-13T04:01:14Z"
      },
      "completed": 1,
      "failed": 0,
      "inProgress": 2,
      "total": 3,
      "keyPhraseExtractionTasks": [
          {
              "name": "My Analyze Job",
              "lastUpdateDateTime": "2020-11-13T04:01:14.3763516Z",
              "results": {
                  "inTerminalState": true,
                  "documents": [
                      {
                          "id": "doc1",
                          "keyPhrases": [
                              "sunny outside"
                          ],
                          "warnings": []
                      },
                      {
                          "id": "doc2",
                          "keyPhrases": [
                              "favorite Seattle attraction",
                              "Pike place market"
                          ],
                          "warnings": []
                      }
                  ],
                  "errors": [],
                  "modelVersion": "2020-07-01"
              }
          }
      ]
  }
}
```


## <a name="summary"></a>Sammanfattning

I den här artikeln har du lärt dig begrepp och arbets flöde för extrahering av nyckel fraser genom att använda Textanalys i Cognitive Services. Sammanfattningsvis:

+ [API för extrahering av nyckelfraser](https://westus2.dev.cognitive.microsoft.com/docs/services/TextAnalytics-v3-0/operations/KeyPhrases) är tillgängligt för utvalda språk.
+ JSON-dokument i begär ande texten innehåller ID, text och språkkod.
+ POST-begäran är till `/keyphrases` en `/analyze` -eller slut punkt med hjälp av en anpassad [åtkomst nyckel och en slut punkt](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource) som är giltig för din prenumeration.
+ Svars resultatet, som består av viktiga ord och fraser för varje dokument-ID, kan strömmas till alla appar som accepterar JSON, inklusive Microsoft Office Excel och Power BI, för att ge några.

## <a name="see-also"></a>Se även

 [Översikt över Textanalys](../overview.md) [Vanliga frågor och svar](../text-analytics-resource-faq.md)</br>
 [Produktsida för textanalys](//go.microsoft.com/fwlink/?LinkID=759712)

## <a name="next-steps"></a>Nästa steg

* [Översikt över Textanalys](../overview.md)
* [Använda klient biblioteket för Textanalys](../quickstarts/client-libraries-rest-api.md)
* [Nyheter](../whats-new.md)
