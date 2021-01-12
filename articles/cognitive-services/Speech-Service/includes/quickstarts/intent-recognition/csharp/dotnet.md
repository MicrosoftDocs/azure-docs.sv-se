---
author: trevorbye
ms.service: cognitive-services
ms.subservice: speech-service
ms.date: 04/04/2020
ms.topic: include
ms.author: trbye
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: c14dfadfb7e8724c196b125e9b93c439fac249c3
ms.sourcegitcommit: 48e5379c373f8bd98bc6de439482248cd07ae883
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98109371"
---
## <a name="prerequisites"></a>Förutsättningar

Innan du börjar:

* <a href="~/articles/cognitive-services/Speech-Service/quickstarts/setup-platform.md?tabs=dotnet&pivots=programming-language-csharp" target="_blank">Installera tal-SDK för utvecklings miljön och skapa ett tomt exempel projekt <span class="docon docon-navigate-external x-hidden-focus"></span> </a>.

## <a name="create-a-luis-app-for-intent-recognition"></a>Skapa en LUIS-app för avsikts igenkänning

[!INCLUDE [Create a LUIS app for intent recognition](../luis-sign-up.md)]

## <a name="open-your-project-in-visual-studio"></a>Öppna projektet i Visual Studio

Öppna sedan projektet i Visual Studio.

1. Starta Visual Studio 2019.
2. Läs in projektet och öppna `Program.cs` .

## <a name="start-with-some-boilerplate-code"></a>Börja med viss exempel kod

Nu ska vi lägga till kod som fungerar som en Skeleton för vårt projekt. Observera att du har skapat en async-metod som kallas `RecognizeIntentAsync()` .
[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=7-17,77-86)]

## <a name="create-a-speech-configuration"></a>Skapa en tal konfiguration

Innan du kan initiera ett `IntentRecognizer` objekt måste du skapa en konfiguration som använder nyckeln och platsen för din Luis förutsägelse resurs.

> [!IMPORTANT]
> Start nyckeln och redigerings nycklarna fungerar inte. Du måste använda din förutsägelse nyckel och plats som du skapade tidigare. Mer information finns i [skapa en Luis-app för avsikts igenkänning](#create-a-luis-app-for-intent-recognition).

Infoga den här koden i- `RecognizeIntentAsync()` metoden. Se till att du uppdaterar dessa värden:

* Ersätt `"YourLanguageUnderstandingSubscriptionKey"` med din Luis-förutsägelse nyckel.
* Ersätt `"YourLanguageUnderstandingServiceRegion"` med din Luis-plats. Använd **regions identifierare** från [region](../../../../regions.md).

>[!TIP]
> Om du behöver hjälp med att hitta dessa värden kan du läsa [skapa en Luis-app för avsikts igenkänning](#create-a-luis-app-for-intent-recognition).

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=26)]

I det här exemplet används `FromSubscription()` metoden för att bygga `SpeechConfig` . En fullständig lista över tillgängliga metoder finns i [SpeechConfig-klass](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig?preserve-view=true&view=azure-dotnet).

Tal-SDK: n kommer att känna igen med en-US för språket, se [Ange käll språk för tal till text](../../../../how-to-specify-source-language.md) om du vill ha information om hur du väljer käll språk.

## <a name="initialize-an-intentrecognizer"></a>Initiera en IntentRecognizer

Nu ska vi skapa en `IntentRecognizer` . Det här objektet skapas i en using-instruktion för att säkerställa en korrekt version av ohanterade resurser. Infoga den här koden i `RecognizeIntentAsync()` metoden, precis under din tal konfiguration.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=29-30,76)]

## <a name="add-a-languageunderstandingmodel-and-intents"></a>Lägg till ett LanguageUnderstandingModel och avsikter

Du måste associera en `LanguageUnderstandingModel` med avsikts igenkänningen och lägga till de avsikter som du vill identifiera. Vi ska använda avsikter från den färdiga domänen för start automatisering. Infoga den här koden i using-instruktionen från föregående avsnitt. Se till att du ersätter `"YourLanguageUnderstandingAppId"` med ditt Luis-app-ID.

>[!TIP]
> Om du behöver hjälp med att hitta det här värdet kan du läsa [skapa en Luis-app för avsikts igenkänning](#create-a-luis-app-for-intent-recognition).

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=33-35)]

I det här exemplet används `AddIntent()` funktionen för att individuellt lägga till avsikter. Använd och skicka modellen om du vill lägga till alla avsikter från en modell `AddAllIntents(model)` . 

> [!NOTE]
> Tal-SDK stöder endast LUIS v 2.0-slut punkter.
> Du måste manuellt ändra slutpunkts-URL: en för v 3.0 som påträffades i exempel fråga fältet för att använda ett v 2.0 URL-mönster.
> LUIS v 2.0-slut punkter följer alltid ett av följande två mönster:
> * `https://{AzureResourceName}.cognitiveservices.azure.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`
> * `https://{Region}.api.cognitive.microsoft.com/luis/v2.0/apps/{app-id}?subscription-key={subkey}&verbose=true&q=`

## <a name="recognize-an-intent"></a>Identifiera en avsikt

Från `IntentRecognizer` -objektet kommer du att anropa- `RecognizeOnceAsync()` metoden. Med den här metoden kan röst tjänsten veta att du skickar en enda fras för igenkänning och att när frasen har identifierats för att sluta identifiera tal.

I instruktionen using lägger du till den här koden under din modell.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=46)]

## <a name="display-recognition-results-or-errors"></a>Visa tolknings resultat (eller fel)

När igenkännings resultatet returneras av tal tjänsten vill du göra något med det. Vi ska hålla det enkelt och skriva ut resultatet till-konsolen.

I using-instruktionen nedan `RecognizeOnceAsync()` lägger du till den här koden:

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=49-75)]

## <a name="check-your-code"></a>Kontrol lera koden

Nu bör din kod se ut så här:

> [!NOTE]
> Vi har lagt till några kommentarer till den här versionen.

[!code-csharp[](~/samples-cognitive-services-speech-sdk/quickstart/csharp/dotnet/intent-recognition/helloworld/Program.cs?range=7-86)]

## <a name="build-and-run-your-app"></a>Skapa och kör din app

Nu är du redo att bygga din app och testa vår tal igenkänning med röst tjänsten.

1. **Kompilera koden** – från meny raden i Visual Studio väljer du **bygge**  >  **build-lösning**.
2. **Starta din app** – från meny raden väljer du **Felsök**  >  **Starta fel sökning** eller tryck på <kbd>F5</kbd>.
3. **Starta igenkänning** – du uppmanas att tala en fras på engelska. Ditt tal skickas till tal tjänsten, skrivs som text och återges i-konsolen.

## <a name="next-steps"></a>Nästa steg

[!INCLUDE [footer](./footer.md)]