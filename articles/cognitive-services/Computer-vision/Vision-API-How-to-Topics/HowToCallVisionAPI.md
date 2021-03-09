---
title: Anropa API:et för visuellt innehåll
titleSuffix: Azure Cognitive Services
description: Lär dig hur du anropar API för visuellt innehåll med hjälp av REST API i Azure Cognitive Services.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: abb367b64da0811a1ff46efe60b60485375f809f
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102486071"
---
# <a name="call-the-computer-vision-api"></a>Anropa API:et för visuellt innehåll

Den här artikeln visar hur du anropar API för visuellt innehåll med hjälp av REST API. Exemplen skrivs både i C# med hjälp av API för visuellt innehåll klient biblioteket och som HTTP POST eller GET-anrop. Artikeln fokuserar på:

- Hämta taggar, en beskrivning och kategorier
- Hämta domänbaserad information eller "kändisar"

I exemplen i den här artikeln demonstreras följande funktioner:

* Analysera en bild för att returnera en matris med taggar och en beskrivning
* Analysera en avbildning med en domänbaserad modell (särskilt "kändisar"-modellen) för att returnera motsvarande resultat i JSON

Funktionerna erbjuder följande alternativ:

- **Alternativ 1**: analys med omfång – analysera bara en angiven modell
- **Alternativ 2**: förbättrad analys – analysera för att tillhandahålla ytterligare information genom att använda [86-kategorier, taxonomi](../Category-Taxonomy.md)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* När du har en Azure-prenumeration <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" skapar du en visuellt innehåll resurs "  target="_blank"> skapa en visuellt innehåll resurs </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till Visuellt innehåll-tjänsten. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.
* En bild-URL eller en sökväg till en lokalt lagrad avbildning
* Indata metoder som stöds: en RAW-bildbinärfil i form av en program/oktett-Stream eller en bild-URL
* Avbildnings fil format som stöds: JPEG, PNG, GIF och BMP
* Bild fils storlek: 4 MB eller mindre
* Bild dimensioner: 50 &times; 50 pixlar eller mer
  
## <a name="authorize-the-api-call"></a>Auktorisera API-anropet

Varje anrop till ett API för visuellt innehåll kräver en prenumerationsnyckel. Den här nyckeln måste antingen skickas via en frågeparameter-parameter eller anges i begär ande huvudet.

Du kan skicka prenumerations nyckeln genom att göra något av följande:

* Skicka den via en frågesträng, som i det här API för visuellt innehåll exempel:

  ```
  https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
  ```

* Ange den i rubriken HTTP-begäran:

  ```
  ocp-apim-subscription-key: <Your subscription key>
  ```

* När du använder klient biblioteket skickar du nyckeln genom ComputerVisionClient-konstruktorn och anger regionen i en egenskap för klienten:

    ```
    var visionClient = new ComputerVisionClient(new ApiKeyServiceClientCredentials("Your subscriptionKey"))
    {
        Endpoint = "https://westus.api.cognitive.microsoft.com"
    }
    ```

## <a name="upload-an-image-to-the-computer-vision-api-service"></a>Ladda upp en avbildning till API för visuellt innehåll tjänsten

Det grundläggande sättet att utföra API för visuellt innehåll-anropet är genom att ladda upp en avbildning direkt för att returnera taggar, en beskrivning och kändisar. Du gör detta genom att skicka en "POST"-begäran med den binära avbildningen i HTTP-innehållet tillsammans med data som läses från avbildningen. Överförings metoden är samma för alla API för visuellt innehåll-anrop. Den enda skillnaden är de frågeparametrar som du anger. 

Hämta Taggar och en beskrivning för en angiven avbildning med något av följande alternativ:

### <a name="option-1-get-a-list-of-tags-and-a-description"></a>Alternativ 1: Hämta en lista över taggar och en beskrivning

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?visualFeatures=Description,Tags&subscription-key=<Your subscription key>
```

```csharp
using System.IO;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;

ImageAnalysis imageAnalysis;
var features = new VisualFeatureTypes[] { VisualFeatureTypes.Tags, VisualFeatureTypes.Description };

using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  imageAnalysis = await visionClient.AnalyzeImageInStreamAsync(fs, features);
}
```

### <a name="option-2-get-a-list-of-tags-only-or-a-description-only"></a>Alternativ 2: Hämta endast en lista med taggar eller en beskrivning

Kör följande endast för Taggar:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/tag?subscription-key=<Your subscription key>
var tagResults = await visionClient.TagImageAsync("http://contoso.com/example.jpg");
```

För en beskrivning, kör:

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/describe?subscription-key=<Your subscription key>
using (var fs = new FileStream(@"C:\Vision\Sample.jpg", FileMode.Open))
{
  imageDescription = await visionClient.DescribeImageInStreamAsync(fs);
}
```

## <a name="get-domain-specific-analysis-celebrities"></a>Hämta domänbaserad analys (kändisar)

### <a name="option-1-scoped-analysis---analyze-only-a-specified-model"></a>Alternativ 1: analys med omfång – analysera bara en angiven modell
```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/models/celebrities/analyze
var celebritiesResult = await visionClient.AnalyzeImageInDomainAsync(url, "celebrities");
```

För det här alternativet är alla andra frågeparametrar (visualFeatures, information) ogiltiga. Om du vill se alla modeller använder du:

```
GET https://westus.api.cognitive.microsoft.com/vision/v2.1/models 
var models = await visionClient.ListModelsAsync();
```

### <a name="option-2-enhanced-analysis---analyze-to-provide-additional-details-by-using-86-categories-taxonomy"></a>Alternativ 2: förbättrad analys – analysera för att tillhandahålla ytterligare information genom att använda 86-kategorier, taxonomi

För program där du vill hämta en allmän bild analys utöver information från en eller flera domänbaserade modeller utökar du v1-API: et genom att använda parametrarna för modeller.

```
POST https://westus.api.cognitive.microsoft.com/vision/v2.1/analyze?details=celebrities
```

När du anropar den här metoden anropar du först [86-kategori-](../Category-Taxonomy.md) klassificeraren. Om någon av kategorierna stämmer överens med en känd eller matchande modell sker ett andra steg i klassificeringen av klassificeraren. Om t. ex. "information = all" eller "information" innehåller "kändisar" anropar du kändisar-modellen när du anropar klassificeraren 86-kategori. Resultatet inkluderar kategori personen. Till skillnad från alternativ 1 ökar den här metoden svars tiden för användare som är intresserade av kändisar.

I det här fallet fungerar alla v1-frågeparametrar på samma sätt. Om du inte anger visualFeatures = kategorier aktive ras den implicit.

## <a name="retrieve-and-understand-the-json-output-for-analysis"></a>Hämta och förstå JSON-utdata för analys

Här är ett exempel:

```json
{  
  "tags":[  
    {  
      "name":"outdoor",
      "score":0.976
    },
    {  
      "name":"bird",
      "score":0.95
    }
  ],
  "description":{  
    "tags":[  
      "outdoor",
      "bird"
    ],
    "captions":[  
      {  
        "text":"partridge in a pear tree",
        "confidence":0.96
      }
    ]
  }
}
```

Fält | Typ | Innehåll
------|------|------|
Taggar  | `object` | Objektet på den översta nivån för en matris med taggar.
tags[].Name | `string`    | Nyckelordet från taggarnas klassificerare.
tags[].Score    | `number`    | Förtroende poängen, mellan 0 och 1.
beskrivning     | `object`    | Objektet på den översta nivån för en beskrivning.
description.tags[] |    `string`    | Listan med taggar.  Om det inte finns tillräckligt med förtroende för möjligheten att skapa en bildtext kan taggarna vara den enda information som är tillgänglig för anroparen.
description.captions[].text    | `string`    | En mening som beskriver bilden.
description.captions[].confidence    | `number`    | Förtroende poängen för frasen.

## <a name="retrieve-and-understand-the-json-output-of-domain-specific-models"></a>Hämta och förstå JSON-utdata för domänbaserade modeller

### <a name="option-1-scoped-analysis---analyze-only-a-specified-model"></a>Alternativ 1: analys med omfång – analysera bara en angiven modell

Utdata är en matris med taggar, som du ser i följande exempel:

```json
{  
  "result":[  
    {  
      "name":"golden retriever",
      "score":0.98
    },
    {  
      "name":"Labrador retriever",
      "score":0.78
    }
  ]
}
```

### <a name="option-2-enhanced-analysis---analyze-to-provide-additional-details-by-using-the-86-categories-taxonomy"></a>Alternativ 2: förbättrad analys – analysera för att tillhandahålla ytterligare information genom att använda "86-Categories"-taxonomin

För företagsspecifika modeller som använder alternativ 2 (utökad analys) utökas kategoriernas retur typ, som visas i följande exempel:

```json
{  
  "requestId":"87e44580-925a-49c8-b661-d1c54d1b83b5",
  "metadata":{  
    "width":640,
    "height":430,
    "format":"Jpeg"
  },
  "result":{  
    "celebrities":[  
      {  
        "name":"Richard Nixon",
        "faceRectangle":{  
          "left":107,
          "top":98,
          "width":165,
          "height":165
        },
        "confidence":0.9999827
      }
    ]
  }
}
```

Fältet kategorier är en lista över en eller flera av 86- [kategorierna](../Category-Taxonomy.md) i den ursprungliga taxonomin. Kategorier som slutar med ett under streck matchar kategorin och dess underordnade objekt (till exempel "people_" eller "people_group" för kändisar-modellen).

Fält    | Typ    | Innehåll
------|------|------|
kategorier | `object`    | Objektet på den översta nivån.
categories[].name     | `string`    | Namnet från listan med taxonomier i 86-kategori.
categories[].score    | `number`    | Förtroende poängen, mellan 0 och 1.
categories[].detail     | `object?`      | Valfritt Objektet detaljerat.

Om flera kategorier matchar varandra (till exempel om en klassificerare i 86-kategori returnerar en poäng för både "people_" och "people_young", när Model = kändisar), är informationen kopplad till den mest generella nivån ("people_" i det här exemplet).

## <a name="error-responses"></a>Fel svar

Dessa fel är identiska med de som är i syn. analysera, med ytterligare NotSupportedModel-fel (HTTP 400), som kan returneras i båda scenarierna alternativ 1 och alternativ 2. För alternativ 2 (utökad analys), om någon av de modeller som anges i informationen inte känns igen, returnerar API: t en NotSupportedModel, även om en eller flera av dem är giltiga. För att ta reda på vilka modeller som stöds kan du anropa List mod Els.

## <a name="next-steps"></a>Nästa steg

Om du vill använda REST-API, gå till [API-referens för datorvision](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/56f91f2e778daf14a499f21b).
