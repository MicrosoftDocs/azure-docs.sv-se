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
ms.date: 03/10/2021
ms.author: heikora
ms.openlocfilehash: b8e02071eca139cde02a8bad1b0e0e443db6ab86
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103555700"
---
# <a name="model-and-endpoint-lifecycle"></a>Livs cykel för modell och slut punkt

Custom Speech använder både *bas modeller* och *anpassade modeller*. Varje språk har en eller flera bas modeller. När en ny tal modell släpps till tjänsten vanliga tal, importeras den också till Custom Speech tjänsten som en ny bas modell. De uppdateras var 6 till 12 månader. Äldre modeller blir vanligt vis mindre användbara över tid eftersom den senaste modellen vanligt vis har högre noggrannhet.

Anpassade modeller skapas däremot genom att anpassa en vald bas modell med data från ditt specifika kund scenario. Du kan fortsätta att använda en viss anpassad modell under en längre tid efter att du har en som motsvarar dina behov. Men vi rekommenderar att du regelbundet uppdaterar till den senaste bas modellen och tränar om den med tiden med ytterligare data. 

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

Här är ett exempel på förfallo data från API-anropet GetModel. "DEPRECATIONDATES" visar: 
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
        "ADAPTATIONDATETIME": "2022-01-15T00:00:00Z",     // last date this model can be used for adaptation
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

## <a name="next-steps"></a>Nästa steg

* [Träna och distribuera en modell](how-to-custom-speech-train-model.md)

## <a name="additional-resources"></a>Ytterligare resurser

* [Förbered och testa dina data](./how-to-custom-speech-test-and-train.md)
* [Inspektera dina data](how-to-custom-speech-inspect-data.md)