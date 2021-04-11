---
title: Modell-och slut punkts livs cykel för Custom Speech tal-tjänsten
titleSuffix: Azure Cognitive Services
description: Custom Speech tillhandahåller bas modeller för anpassning och gör det möjligt att skapa anpassade modeller från dina data. I den här artikeln beskrivs tids linjer för modeller och för slut punkter som använder dessa modeller.
services: cognitive-services
author: heikora
manager: dongli
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/2/2021
ms.author: heikora
ms.openlocfilehash: b82a732533c3d069b519b07c3209d4b96c472900
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2021
ms.locfileid: "106385033"
---
# <a name="model-and-endpoint-lifecycle"></a>Livs cykel för modell och slut punkt

Vårt standard tal (inte anpassat) bygger på AI-modeller som vi anropar bas modeller. I de flesta fall tränar vi en annan bas modell för varje talade språk som vi stöder.  Vi uppdaterar tal tjänsten med nya bas modeller per månad för att förbättra precisionen och kvaliteten.  
Med Custom Speech skapas anpassade modeller genom att anpassa en vald bas modell med data från ditt specifika kund scenario. När du har skapat en anpassad modell kommer den modellen inte att uppdateras eller ändras, även om motsvarande bas modell som den anpassades från uppdateras i standard tal tjänsten.  
Med den här principen kan du fortsätta att använda en viss anpassad modell under en längre tid efter att du har en anpassad modell som uppfyller dina behov.  Men vi rekommenderar att du regelbundet återskapar din anpassade modell så att du kan anpassa från den senaste bas modellen för att dra nytta av den förbättrade precisionen och kvaliteten.

Andra viktiga villkor som rör modell livs cykeln är:

* **Anpassning**: ta en bas modell och anpassa den till domänen/scenariot med hjälp av text data och/eller ljuddata.
* **Avkodning**: använda en modell och utföra tal igenkänning (avkodning av ljud i text).
* **Slut punkt**: en användarspecifik distribution av antingen en bas modell eller en anpassad modell som *endast* är tillgänglig för en specifik användare.

### <a name="expiration-timeline"></a>Tids linje för förfallo datum

När nya modeller och nya funktioner blir tillgängliga och äldre, tas mindre exakta modeller bort, se följande tids linjer för modell-och slut punkts utgång:

**Bas modeller** 

* Anpassning: tillgängligt i ett år. När modellen har importer ATS är den tillgänglig i ett år för att skapa anpassade modeller. Efter ett år måste nya anpassade modeller skapas från en senare bas modell version.  
* Avkodning: tillgänglig i två år efter importen. Så du kan skapa en slut punkt och använda batch-avskrift för två år med den här modellen. 
* Slut punkter: tillgängliga på samma tids linje som avkodning.

**Anpassade modeller**

* Avkodning: tillgänglig i två år efter att modellen har skapats. Så du kan använda den anpassade modellen i två år (batch/Real/test) när den har skapats. Efter två år *bör du träna modellen* på grund av att bas modellen normalt sett är föråldrad för anpassning.  
* Slut punkter: tillgängliga på samma tids linje som avkodning.

När antingen en bas modell eller anpassad modell upphör att gälla, kommer den alltid att återgå till den *senaste bas modell versionen*. Därför kommer din implementering aldrig att brytas, men det kan bli mindre exakt för *dina data* om anpassade modeller upphör att gälla. Du kan se förfallo datum för en modell på följande platser i Custom Speech-avsnittet i tal Studio:

* Sammanfattning av modell utbildning
* Information om modell utbildning
* Distributionssammanfattning
* Distributions information

Här följer ett exempel på Sammanfattning av modell utbildning:

![Sammanfattning av modell utbildning ](media/custom-speech/custom-speech-model-training-with-expiry.png) och även från sidan modell övnings information:

![Information om modell utbildning](media/custom-speech/custom-speech-model-details-with-expiry.png)

Du kan också kontrol lera förfallo datumen via [`GetModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetModel) och [`GetBaseModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetBaseModel) anpassade tal-API: er under `deprecationDates` egenskapen i JSON-svaret.

Här är ett exempel på förfallo data från API-anropet GetModel. **DEPRECATIONDATES** visas när modellen upphör att gälla: 
```json
{
    "SELF": "HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/{id}",
    "BASEMODEL": {
    "SELF": HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/BASE/{id}
    },
    "DATASETS": [
    {
        "SELF": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/datasets/{id}
    }
    ],
    "LINKS": {
    "MANIFEST": "HTTPS://WESTUS2.API.COGNITIVE.MICROSOFT.COM/SPEECHTOTEXT/V3.0/MODELS/{id}/MANIFEST",
    "COPYTO": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/models/{id}/copyto
    },
    "PROJECT": {
        "SELF": https://westus2.api.cognitive.microsoft.com/speechtotext/v3.0/projects/{id}
    },
    "PROPERTIES": {
    "DEPRECATIONDATES": {
        "ADAPTATIONDATETIME": "2022-01-15T00:00:00Z",     // last date the base model can be used for adaptation
        "TRANSCRIPTIONDATETIME": "2023-03-01T21:27:29Z"   // last date this model can be used for decoding
    }
    },
    "LASTACTIONDATETIME": "2021-03-01T21:27:40Z",
    "STATUS": "SUCCEEDED",
    "CREATEDDATETIME": "2021-03-01T21:27:29Z",
    "LOCALE": "EN-US",
    "DISPLAYNAME": "EXAMPLE MODEL",
    "DESCRIPTION": "",
    "CUSTOMPROPERTIES": {
    "PORTALAPIVERSION": "3"
    }
}
```
Observera att du kan uppgradera modellen på en anpassad tal slut punkt utan drift stopp genom att ändra modellen som används av slut punkten i distributions avsnittet i tal Studio, eller via det anpassade tal-API: et.

## <a name="what-happens-when-models-expire-and-how-to-update-them"></a>Vad händer när modeller upphör att gälla och hur de uppdateras
Vad som händer när en modell upphör att gälla och hur du uppdaterar modellen beror på hur den används.
### <a name="batch-transcription"></a>Batch-transkription
Om en modell upphör att gälla som används med avskrifts begär Anden för [batch-avskrift](batch-transcription.md) Miss Miss 4xx med ett fel. För att förhindra att den här uppdateringen uppdateras `model` parametern i JSON som skickas i begäran om att **skapa avskrift** , antingen till en senare bas modell eller nyare anpassad modell. Du kan också ta bort `model` posten från JSON om du alltid vill använda den senaste bas modellen.
### <a name="custom-speech-endpoint"></a>Anpassad tal slut punkt
Om en modell upphör att gälla som används av en [anpassad tal slut punkt](how-to-custom-speech-train-model.md)kommer tjänsten automatiskt att återgå till att använda den senaste bas modellen för det språk som du använder. använder du du kan välja **distribution** på **Custom Speech** -menyn längst upp på sidan och sedan klicka på slut punktens namn för att se information om den. Längst upp på sidan information visas en knapp för **uppdaterings modell** som gör att du sömlöst kan uppdatera modellen som används av slut punkten utan drift avbrott. Du kan också göra den här ändringen program mässigt genom att använda [**uppdaterings modellens**](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/UpdateModel) REST-API.

## <a name="next-steps"></a>Nästa steg

* [Träna och distribuera en modell](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>Ytterligare resurser

* [Förbered och testa dina data](./how-to-custom-speech-test-and-train.md)
* [Inspektera dina data](how-to-custom-speech-inspect-data.md)