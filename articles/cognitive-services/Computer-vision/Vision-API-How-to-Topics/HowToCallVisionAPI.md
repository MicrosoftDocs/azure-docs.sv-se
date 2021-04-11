---
title: Anropa API för bild analys
titleSuffix: Azure Cognitive Services
description: Lär dig att anropa API för bild analys och konfigurera dess beteende.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: sample
ms.date: 09/09/2019
ms.author: kefre
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 3f9a6afe3202df40e26332c3a8c91b8c3eca8a32
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012276"
---
# <a name="call-the-image-analysis-api"></a>Anropa API för bild analys

Den här artikeln visar hur du anropar API: et för bild analys för att returnera information om en bilds visuella funktioner.

Den här guiden förutsätter att du redan har <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" skapat en visuellt innehåll resurs "  target="_blank"> skapa en visuellt innehåll resurs </a> och erhållit en prenumerations nyckel och en slut punkts-URL. Om du inte har gjort det följer du en [snabb start](../quickstarts-sdk/image-analysis-client-library.md) för att komma igång.
  
## <a name="submit-data-to-the-service"></a>Skicka data till tjänsten

Du skickar antingen en lokal avbildning eller en fjärran sluten avbildning till analys-API: et. För lokalt anger du binära avbildnings data i text för HTTP-begäran. För fjärran sluten anger du avbildningens URL genom att formatera begär ande texten som följande: `{"url":"http://example.com/images/test.jpg"}` .

## <a name="determine-how-to-process-the-data"></a>Bestämma hur data ska bearbetas

###  <a name="select-visual-features"></a>Välj visuella funktioner

I [analys-API: et](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/56f91f2e778daf14a499f21b) får du till gång till alla funktioner för avbildnings analys. Du måste ange vilka funktioner du vill använda genom att ange parametrarna för URL-frågor. En parameter kan ha flera värden, avgränsade med kommatecken. Varje funktion som du anger kommer att kräva ytterligare beräknings tid, så ange bara vad du behöver.

|URL-parameter | Värde | Beskrivning|
|---|---|--|
|`visualFeatures`|`Adult` | identifierar om bilden är pornografiskt (visar nakenhet eller en kön Act) eller är fullständig (visar extrema våld eller blod). Sexuellt innehåll (aka vågat-innehåll) identifieras också.|
||`Brands` | identifierar olika varumärken inom en avbildning, inklusive den ungefärliga platsen. Argumentet märken är bara tillgängligt på engelska.|
||`Categories` | kategoriserar bild innehåll enligt en taxonomi som definierats i dokumentationen. Detta är standardvärdet för `visualFeatures` .|
||`Color` | anger accentfärg, dominerande färg och om en bild är svart&vit.|
||`Description` | beskriver bild innehållet med en hel mening i språk som stöds.|
||`Faces` | identifierar om det finns ansikten. Om detta finns genererar du koordinater, kön och ålder.|
||`ImageType` | identifierar om bilden är ClipArt eller en linje ritning.|
||`Objects` | identifierar olika objekt i en avbildning, inklusive den ungefärliga platsen. Objekt argumentet är endast tillgängligt på engelska.|
||`Tags` | Taggar avbildningen med en detaljerad lista över ord relaterade till bild innehållet.|
|`details`| `Celebrities` | identifierar kändisar om det upptäcks i avbildningen.|
||`Landmarks` |identifierar landmärken om de upptäcks i avbildningen.|

En ifylld URL kan se ut så här:

`https://{endpoint}/vision/v2.1/analyze?visualFeatures=Description,Tags&details=Celebrities`

### <a name="specify-languages"></a>Ange språk

Du kan också ange språket för de data som returneras. Följande URL-frågeparameter anger språket. Standardvärdet är `en`.

|URL-parameter | Värde | Beskrivning|
|---|---|--|
|`language`|`en` | Engelska|
||`es` | Spanska|
||`ja` | Japanska|
||`pt` | Portugisiska|
||`zh` | Förenklad kinesiska|

En ifylld URL kan se ut så här:

`https://{endpoint}/vision/v2.1/analyze?visualFeatures=Description,Tags&details=Celebrities&language=en`

> [!NOTE]
> **API-anrop i omfång**
>
> Några av funktionerna i bild analys kan anropas direkt och genom API-anropet analyseras. Du kan till exempel göra en begränsad analys av enbart bildtaggar genom att göra en begäran till `https://{endpoint}/vision/v3.2-preview.3/tag` . Se [referens dokumentationen](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/56f91f2e778daf14a499f21b) för andra funktioner som kan anropas separat.

## <a name="get-results-from-the-service"></a>Hämta resultat från tjänsten

Tjänsten returnerar ett `200` http-svar och texten innehåller returnerade data i form av en JSON-sträng. Följande är ett exempel på ett JSON-svar.

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

I följande tabell visas förklaringar av fälten i det här exemplet:

Fält | Typ | Innehåll
------|------|------|
Taggar  | `object` | Objektet på den översta nivån för en matris med taggar.
tags[].Name | `string`    | Nyckelordet från taggarnas klassificerare.
tags[].Score    | `number`    | Förtroende poängen, mellan 0 och 1.
beskrivning     | `object`    | Objektet på den översta nivån för en bild beskrivning.
description.tags[] |    `string`    | Listan med taggar. Om det inte finns tillräckligt med förtroende för möjligheten att skapa en bildtext kan taggarna vara den enda information som är tillgänglig för anroparen.
description.captions[].text    | `string`    | En mening som beskriver bilden.
description.captions[].confidence    | `number`    | Förtroende poängen för frasen.

### <a name="error-codes"></a>Felkoder

Se följande lista över möjliga fel och deras orsaker:

* 400
    * InvalidImageUrl-bild-URL: en är felaktigt formaterad eller inte tillgänglig.
    * InvalidImageFormat-indata är inte en giltig avbildning.
    * InvalidImageSize-indata-bilden är för stor.
    * NotSupportedVisualFeature-angiven funktions typ är inte giltig.
    * NotSupportedImage-avbildning som inte stöds, t. ex. underordnad pornografi.
    * InvalidDetails – parameter värde som inte stöds `detail` .
    * NotSupportedLanguage-den begärda åtgärden stöds inte på det angivna språket.
    * BadArgument – ytterligare information finns i fel meddelandet.
* 415-fel för medie typ som inte stöds. Innehålls typen är inte av tillåtna typer:
    * För en bild-URL: Content-Type ska vara Application/JSON
    * För en binär avbildnings data: Content-Type ska vara Application/oktett-Stream eller multipart/form-data
* 500
    * FailedToProcess
    * Timeout-tids gränsen nåddes för avbildnings bearbetningen.
    * InternalServerError

## <a name="next-steps"></a>Nästa steg

Om du vill testa REST API går du till [API-referens för avbildnings analys](https://westus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-2-preview-3/operations/56f91f2e778daf14a499f21b).
