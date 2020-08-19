---
title: 'Snabbstart: Identifiera ansikten i en bild med Azure REST API och cURL'
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten ska du använda Azure ansikts-REST API med cURL för att identifiera ansikten i en bild.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 08/05/2020
ms.author: pafarley
ms.openlocfilehash: 8afb6f018e9c01ee42a9e43cc726a442fa4c8965
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88539346"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-curl"></a>Snabbstart: Identifiera ansikten i en bild med hjälp av ansikts-REST API och cURL

I den här snabb starten ska du använda Azures ansikts REST API med sväng för att identifiera mänskliga ansikten i en bild.

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/cognitive-services/) innan du börjar. 

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* När du har en Azure-prenumeration <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title=" skapar du en ansikts resurs "  target="_blank"> skapa en ansikts resurs <span class="docon docon-navigate-external x-hidden-focus"></span> </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till Ansikts-API. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.

## <a name="write-the-command"></a>Skriva kommandot
 
Du ska använda ett kommando som följande för att anropa Ansikts-API och hämta attribut data från en avbildning. Kopiera först koden till ett redigeringsprogram&mdash;du måste göra ändringar av vissa delar av kommandot innan du kan köra den.

```shell
curl -H "Ocp-Apim-Subscription-Key: <Subscription Key>" "https://<My Endpoint String>.com/face/v1.0/detect?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise" -H "Content-Type: application/json" --data-ascii "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg\"}"
```

### <a name="subscription-key"></a>Prenumerationsnyckel
Ersätt `<Subscription Key>` med en giltig ansiktsprenumerationsnyckel.

### <a name="face-endpoint-url"></a>Ansiktsslutpunktens webbadress

Webbadressen `https://<My Endpoint String>.com/face/v1.0/detect` anger Azure-ansiktsslutpunkten för att fråga. Du kan behöva ändra den första delen av den här URL: en så att den matchar slut punkten som motsvarar din prenumerations nyckel.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

### <a name="url-query-string"></a>URL-frågesträng

Frågesträngen för ansiktsslutpunktens webbadress anger vilka ansiktsattribut som ska hämtas. Du kanske vill ändra den här strängen beroende på den avsedda användningen.

```
?returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise
```

### <a name="image-source-url"></a>URL för bildkälla
URL:en för källa anger bilden som ska användas som indata. Du kan ändra det så att det pekar på en bild som du vill analysera.

```
https://upload.wikimedia.org/wikipedia/commons/c/c3/RH_Louise_Lillian_Gish.jpg
``` 

## <a name="run-the-command"></a>Köra kommandot

När du har gjort dina ändringar öppnar du en kommandotolk och anger det nya kommandot. Du bör se ansiktsinformationen som visas som JSON-data i konsolfönstret. Exempel:

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

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skrev du ett spiral kommando som anropar Azure Face service för att identifiera ansikten i en bild och returnera deras attribut. Läs ansikts-API-referensdokumentationen om du vill veta mer.

> [!div class="nextstepaction"]
> [Ansikts-API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
