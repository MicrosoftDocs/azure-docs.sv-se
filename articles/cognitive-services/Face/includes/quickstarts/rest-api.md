---
title: Snabb start för ansikts REST API
description: Använd ansikts REST API med sväng för att identifiera ansikten, hitta liknande (ansikts sökning efter bild), identifiera ansikten (ansikts igenkännings sökning) och migrera dina ansikts data.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: include
ms.date: 12/06/2020
ms.author: pafarley
ms.openlocfilehash: 5523d89548ba08dfc5a43f5c21b5a12a5e5955c3
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102510729"
---
Kom igång med ansikts igenkänning med ansikts REST API. Ansikts tjänsten ger dig till gång till avancerade algoritmer för att identifiera och identifiera mänskliga ansikten i bilder.

Använd REST API ansikte för att:

* [Identifiera ansikten i en bild](#detect-faces-in-an-image)
* [Hitta liknande ansikten](#find-similar-faces)

> [!NOTE]
> I den här snabb starten används spiral kommandon för att anropa REST API. Du kan också anropa REST API med ett programmeringsspråk. Se exemplen för GitHub för exempel i [C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/Face/rest), [python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/Face/rest), [Java](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/Face/rest), [Java Script](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/Face/rest)och [Go](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/Face/rest).

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* När du har en Azure-prenumeration <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" skapar du en ansikts resurs "  target="_blank"> skapa en ansikts resurs </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till Ansikts-API. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.
* [PowerShell version 6.0 +](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows)eller liknande kommando rads program.


## <a name="detect-faces-in-an-image"></a>Identifiera ansikten i en bild

Du ska använda ett kommando som följande för att anropa Ansikts-API och hämta attribut data från en avbildning. Kopiera först koden till ett redigeringsprogram&mdash;du måste göra ändringar av vissa delar av kommandot innan du kan köra den.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="detection_model_2":::

Gör följande ändringar:
1. Tilldela `Ocp-Apim-Subscription-Key` din giltiga prenumerations nyckel för ansikts.
1. Ändra den första delen av fråge-URL: en så att den matchar slut punkten som motsvarar din prenumerations nyckel.
   [!INCLUDE [subdomains-note](../../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. Du kan också ändra URL: en i bröd texten i begäran så att den pekar på en annan bild.

När du har gjort dina ändringar öppnar du en kommandotolk och anger det nya kommandot. 

### <a name="examine-the-results"></a>Granska resultaten

Du bör se ansiktsinformationen som visas som JSON-data i konsolfönstret. Exempel:

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    }
  }
]  
```

### <a name="get-face-attributes"></a>Hämta ansikts attribut
 
Om du vill extrahera ansikts attribut anropar du identifierings-API: et igen, men angett `detectionModel` till `detection_01` . Lägg `returnFaceAttributes` även till Frågeparametern. Kommandot bör nu se ut ungefär så här. Som tidigare infogar du din prenumerations nyckel och slut punkt för ansikte.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="detection_model_1":::

### <a name="examine-the-results"></a>Granska resultaten

Den returnerade ansikts informationen innehåller nu ansikts attribut. Exempel:

```json
[
  {
    "faceId": "49d55c17-e018-4a42-ba7b-8cbbdfae7c6f",
    "faceRectangle": {
      "top": 131,
      "left": 177,
      "width": 162,
      "height": 162
    },
    "faceAttributes": {
      "smile": 0,
      "headPose": {
        "pitch": 0,
        "roll": 0.1,
        "yaw": -32.9
      },
      "gender": "female",
      "age": 22.9,
      "facialHair": {
        "moustache": 0,
        "beard": 0,
        "sideburns": 0
      },
      "glasses": "NoGlasses",
      "emotion": {
        "anger": 0,
        "contempt": 0,
        "disgust": 0,
        "fear": 0,
        "happiness": 0,
        "neutral": 0.986,
        "sadness": 0.009,
        "surprise": 0.005
      },
      "blur": {
        "blurLevel": "low",
        "value": 0.06
      },
      "exposure": {
        "exposureLevel": "goodExposure",
        "value": 0.67
      },
      "noise": {
        "noiseLevel": "low",
        "value": 0
      },
      "makeup": {
        "eyeMakeup": true,
        "lipMakeup": true
      },
      "accessories": [],
      "occlusion": {
        "foreheadOccluded": false,
        "eyeOccluded": false,
        "mouthOccluded": false
      },
      "hair": {
        "bald": 0,
        "invisible": false,
        "hairColor": [
          {
            "color": "brown",
            "confidence": 1
          },
          {
            "color": "black",
            "confidence": 0.87
          },
          {
            "color": "other",
            "confidence": 0.51
          },
          {
            "color": "blond",
            "confidence": 0.08
          },
          {
            "color": "red",
            "confidence": 0.08
          },
          {
            "color": "gray",
            "confidence": 0.02
          }
        ]
      }
    }
  }
]
```

## <a name="find-similar-faces"></a>Hitta liknande ansikten

Den här åtgärden tar en enda identifierad ansikte (källa) och söker i en uppsättning andra ansikten (mål) för att hitta matchningar (ansikts sökning efter bild). När en matchning hittas, skrivs ID: t för den matchade ytan till-konsolen ut.

### <a name="detect-faces-for-comparison"></a>Identifiera ytor för jämförelse

Först måste du identifiera ansikten i bilder innan du kan jämföra dem. Kör det här kommandot som du gjorde i avsnittet [identifiera ansikten](#detect-faces-in-an-image) . Den här identifierings metoden är optimerad för jämförelse åtgärder. Den extraherar inte detaljerade ansikts attribut som i avsnittet ovan och använder en annan identifierings modell.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="detect_for_similar":::

Hitta `"faceId"` värdet i JSON-svaret och spara det på en tillfällig plats. Anropa sedan kommandot ovan igen för dessa andra bild-URL: er och spara deras ansikts-ID. Du använder dessa ID: n som mål grupp för ansikten från vilka en liknande ansikte ska hittas.

:::code source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar_group":::

Identifiera slutligen den enda källans ansikte som du ska använda för matchning och spara dess ID. Behåll detta ID separat från de andra.

:::code source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar_matcher":::

### <a name="find-matches"></a>Hitta matchningar

Kopiera följande kommando till en text redigerare.

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar":::

Gör sedan följande ändringar:
1. Tilldela `Ocp-Apim-Subscription-Key` din giltiga prenumerations nyckel för ansikts.
1. Ändra den första delen av fråge-URL: en så att den matchar slut punkten som motsvarar din prenumerations nyckel.

Använd följande JSON-innehåll för `body` värdet:

:::code language="JSON" source="~/cognitive-services-quickstart-code/curl/face/detect.sh" ID="similar_body":::

1. Använd källans ansikts-ID för `"faceId"` .
1. Klistra in övriga ansikts-ID: n som termer i `"faceIds"` matrisen.

### <a name="examine-the-results"></a>Granska resultaten

Du får ett JSON-svar som visar ID: n för de ansikten som matchar din frågas ansikte.

```json
[
    {
        "persistedFaceId" : "015839fb-fbd9-4f79-ace9-7675fc2f1dd9",
        "confidence" : 0.82
    },
    ...
] 
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du använder ansikts REST API för att utföra grundläggande ansikts igenkännings uppgifter. Sedan kan du utforska referens dokumentationen och läsa mer om biblioteket.

> [!div class="nextstepaction"]
> [Ansikts-API referens](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)

* [Vad är tjänsten Ansiktsigenkänning?](../../overview.md)