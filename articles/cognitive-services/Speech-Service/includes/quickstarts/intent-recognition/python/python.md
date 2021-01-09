---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.topic: include
ms.author: trbye
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: 8eada631b7e1f632148993e586c70b6811cf9ac9
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98052732"
---
## <a name="prerequisites"></a>Förutsättningar

Innan du börjar:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?pivots=programming-language-python" target="_blank">Installera tal-SDK för utvecklings miljön och skapa ett tomt exempel projekt <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.

## <a name="create-a-luis-app-for-intent-recognition"></a>Skapa en LUIS-app för avsikts igenkänning

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project"></a>Öppna projektet

1. Öppna önskad IDE.
2. Skapa ett nytt projekt och skapa filen med namnet `quickstart.py` och öppna den sedan.

## <a name="start-with-some-boilerplate-code"></a>Börja med viss exempel kod

Nu ska vi lägga till kod som fungerar som en Skeleton för vårt projekt.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-7)]

## <a name="create-a-speech-configuration"></a>Skapa en tal konfiguration

Innan du kan initiera ett `IntentRecognizer` objekt måste du skapa en konfiguration som använder nyckeln och platsen för din Luis förutsägelse resurs.

Infoga den här koden i `quickstart.py` . Se till att du uppdaterar dessa värden:

* Ersätt `"YourLanguageUnderstandingSubscriptionKey"` med din Luis-förutsägelse nyckel.
* Ersätt `"YourLanguageUnderstandingServiceRegion"` med din Luis-plats. Använd **regions identifierare** från [region](../../../../regions.md)

>[!TIP]
> Om du behöver hjälp med att hitta dessa värden kan du läsa [skapa en Luis-app för avsikts igenkänning](#create-a-luis-app-for-intent-recognition).

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=12)]

Det här exemplet skapar `SpeechConfig` objektet med Luis nyckel och region. En fullständig lista över tillgängliga metoder finns i [SpeechConfig-klass](/python/api/azure-cognitiveservices-speech/azure.cognitiveservices.speech.speechconfig).

Tal-SDK: n kommer att känna igen med en-US för språket, se [Ange käll språk för tal till text](../../../../how-to-specify-source-language.md) om du vill ha information om hur du väljer käll språk.

## <a name="initialize-an-intentrecognizer"></a>Initiera en IntentRecognizer

Nu ska vi skapa en `IntentRecognizer` . Infoga den här koden direkt under din tal konfiguration.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=15)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Lägg till ett LanguageUnderstandingModel och avsikter

Du måste associera en `LanguageUnderstandingModel` med avsikts tolken och lägga till de avsikter som du vill identifiera. Vi ska använda avsikter från den färdiga domänen för start automatisering.

Infoga den här koden under din `IntentRecognizer` . Se till att du ersätter `"YourLanguageUnderstandingAppId"` med ditt Luis-app-ID. 

>[!TIP]
> Om du behöver hjälp med att hitta det här värdet kan du läsa [skapa en Luis-app för avsikts igenkänning](#create-a-luis-app-for-intent-recognition).

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=19-27)]

I det här exemplet används `add_intents()` funktionen för att lägga till en lista med uttryckligen definierade syften. Använd och skicka modellen om du vill lägga till alla avsikter från en modell `add_all_intents(model)` .

> [!NOTE]
> Du kan skapa en LanguageUnderstandingModel genom att skicka en slut punkts-URL till FromEndpoint-metoden.
> Tal-SDK stöder endast LUIS v 2.0-slut punkter och LUIS v 2.0-slutpunkter följer alltid ett av följande två mönster:
> * `https://{AzureResourceName}.cognitiveservices.azure.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`
> * `https://{Region}.api.cognitive.microsoft.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`

## <a name="recognize-an-intent"></a>Identifiera en avsikt

Från `IntentRecognizer` -objektet kommer du att anropa- `recognize_once()` metoden. Med den här metoden kan röst tjänsten veta att du skickar en enda fras för igenkänning och att när frasen har identifierats för att sluta identifiera tal.

Infoga den här koden under din modell.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=35)]

## <a name="display-the-recognition-results-or-errors"></a>Visa tolknings resultat (eller fel)

När igenkännings resultatet returneras av tal tjänsten vill du göra något med det. Vi ska hålla det enkelt och skriva ut resultatet till-konsolen.

Under ditt anrop till `recognize_once()` lägger du till den här koden.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=38-47)]

## <a name="check-your-code"></a>Kontrol lera koden

Nu bör din kod se ut så här.

> [!NOTE]
> Vi har lagt till några kommentarer till den här versionen.

[!code-python[](~/samples-cognitive-services-speech-sdk/quickstart/python/intent-recognition/quickstart.py?range=5-47)]

## <a name="build-and-run-your-app"></a>Skapa och kör din app

Kör exemplet från-konsolen eller i IDE:

```
python quickstart.py
```

Följande 15 sekunder av talindata från mikrofonen identifieras och loggas i konsolfönstret.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]