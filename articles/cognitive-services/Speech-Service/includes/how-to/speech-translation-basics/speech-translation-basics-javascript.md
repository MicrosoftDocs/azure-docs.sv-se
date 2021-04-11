---
author: v-demjoh
ms.service: cognitive-services
ms.topic: include
ms.date: 07/14/2020
ms.author: v-demjoh
ms.custom: devx-track-js
ms.openlocfilehash: b43c0da3303b4cdfd4941f9d76b663f8089a1417
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105105254"
---
En av de viktigaste funktionerna i tal tjänsten är möjligheten att identifiera mänskligt tal och översätta den till andra språk. I den här snabb starten lär du dig hur du använder tal-SDK i dina appar och produkter för att utföra högkvalitativ tal översättning. Den här snabb starten behandlar ämnen, inklusive:

* Översättning av tal till text
* Översätta tal till flera mål språk
* Utföra direkt tal-till-tal-Översättning

## <a name="skip-to-samples-on-github"></a>Hoppa till exempel på GitHub

Om du vill hoppa över direkt till exempel kod kan du läsa [snabb starts exemplen för Java Script](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/javascript/browser/translate-speech-to-text) på GitHub.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du har ett Azure-konto och en röst tjänst prenumeration. Om du inte har ett konto och en prenumeration kan du [prova att använda tal tjänsten kostnads fritt](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installera Speech SDK

Innan du kan göra något måste du installera <a href="https://www.npmjs.com/package/microsoft-cognitiveservices-speech-sdk" target="_blank">Speech SDK för Java Script </a>. Använd följande instruktioner, beroende på plattform:
- <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-sdk?tabs=nodejs#get-the-speech-sdk" target="_blank">Node.js <span 
class="docon docon-navigate-external x-hidden-focus"></span></a>
- <a href="/azure/cognitive-services/speech-service/speech-sdk?tabs=browser#get-the-speech-sdk" target="_blank">Webbläsare </a>

Beroende på mål miljön använder du dessutom något av följande:

# <a name="script"></a>[skript](#tab/script)

Hämta och extrahera <a href="https://aka.ms/csspeech/jsbrowserpackage" target="_blank">talet SDK för java script</a> *microsoft.cognitiveservices.speech.sdk.bundle.js* -filen och placera den i en mapp som är tillgänglig för HTML-filen.

```html
<script src="microsoft.cognitiveservices.speech.sdk.bundle.js"></script>;
```

> [!TIP]
> Om du är mål för en webbläsare och använder `<script>` taggen, `sdk` behövs inte prefixet. `sdk`Prefixet är ett alias som används för att namnge `require` modulen.

# <a name="import"></a>[export](#tab/import)

```javascript
import * from "microsoft-cognitiveservices-speech-sdk";
```

Mer information finns i `import` <a href="https://javascript.info/import-export" target="_blank">Exportera och importera </a>.

# <a name="require"></a>[innebära](#tab/require)

```javascript
const sdk = require("microsoft-cognitiveservices-speech-sdk");
```

Mer information om `require` finns i <a href="https://nodejs.org/en/knowledge/getting-started/what-is-require/" target="_blank">Vad kräver? </a>.

---

## <a name="create-a-translation-configuration"></a>Skapa en översättnings konfiguration

Om du vill anropa översättnings tjänsten med hjälp av tal-SDK måste du skapa en [`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig) . Den här klassen innehåller information om din prenumeration, till exempel din nyckel och tillhör ande region, slut punkt, värd eller token för auktorisering.

> [!NOTE]
> Oavsett om du utför tal igenkänning, tal syntes, översättning eller avsikts igenkänning, skapar du alltid en konfiguration.
Det finns några sätt som du kan initiera en [`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig) :

* Med en prenumeration: skicka i en nyckel och tillhör ande region.
* Med en slut punkt: skicka i en röst tjänst slut punkt. En nyckel eller autentiseringstoken är valfri.
* Med en värd: skicka in en värd adress. En nyckel eller autentiseringstoken är valfri.
* Med en autentiseringstoken: skicka in en autentiseringstoken och den associerade regionen.

Låt oss ta en titt på hur en [`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig) har skapats med hjälp av en nyckel och region. Hämta dessa autentiseringsuppgifter genom att följa stegen i [testa tal tjänsten kostnads fritt](../../../overview.md#try-the-speech-service-for-free).

```javascript
const speechTranslationConfig = SpeechTranslationConfig.fromSubscription("YourSubscriptionKey", "YourServiceRegion");
```

## <a name="initialize-a-translator"></a>Initiera en översättare

När du har skapat en [`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig) är nästa steg att initiera en [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) . När du initierar en måste [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) du skicka den `speechTranslationConfig` . Detta ger de autentiseringsuppgifter som översättnings tjänsten behöver för att verifiera din begäran.

Om du översätter tal som tillhandahålls via enhetens standard mikrofon [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) ska du se vad som ska se ut så här:

```javascript
const translator = new TranslationRecognizer(speechTranslationConfig);
```

Om du vill ange enheten för ljud inspelning måste du skapa en [`AudioConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig) och ange `audioConfig` parametern när du initierar [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) .

> [!TIP]
> [Lär dig hur du hämtar enhets-ID: t för din enhet för ljud inspelning](../../../how-to-select-audio-input-devices.md).
Referera till `AudioConfig` objektet enligt följande:

```javascript
const audioConfig = AudioConfig.fromDefaultMicrophoneInput();
const recognizer = new TranslationRecognizer(speechTranslationConfig, audioConfig);
```

Om du vill ange en ljudfil i stället för att använda en mikrofon måste du ändå ange en `audioConfig` . Detta kan dock endast göras när mål **Node.js** och när du skapar en [`AudioConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/audioconfig) , i stället för att anropa `fromDefaultMicrophoneInput` , anropar `fromWavFileOutput` och skickar `filename` parametern.

```javascript
const audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
const recognizer = new TranslationRecognizer(speechTranslationConfig, audioConfig);
```

## <a name="translate-speech"></a>Översätt tal

[TranslationRecognizer-klassen](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) för tal-SDK för Java Script visar några metoder som du kan använda för tal översättning.

* Översättning av enskild bild (async) – utför översättning i ett icke-blockerande (asynkront) läge. Detta översätter en enskild uttryck. Slutet på en enskild uttryck bestäms genom att lyssna efter tystnad i slutet eller tills maximalt 15 sekunders ljud bearbetas.
* Kontinuerlig översättning (asynkron) – initierar en kontinuerlig översättnings åtgärd asynkront. Användaren registrerar sig för händelser och hanterar olika program tillstånd. Anropa för att stoppa asynkron kontinuerlig översättning [`stopContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#stopcontinuousrecognitionasync) .

> [!NOTE]
> Läs mer om hur du [väljer ett tal igenkännings läge](../../../get-started-speech-to-text.md).
### <a name="specify-a-target-language"></a>Ange ett mål språk

För att översätta måste du ange både ett käll språk och minst ett mål språk.
Du kan välja ett käll språk med hjälp av ett språk som anges i [tal översättnings tabellen](../../../language-support.md#speech-translation). Hitta dina alternativ för översatt språk på samma länk. Alternativen för mål språken skiljer sig när du vill visa text eller vill höra syntetiskt översatt tal. Om du vill översätta från engelska till tyska ändrar du översättnings konfigurations objekt:

```javascript
speechTranslationConfig.speechRecognitionLanguage = "en-US";
speechTranslationConfig.addTargetLanguage("de");
```

### <a name="single-shot-recognition"></a>Igenkänning av enstaka bild

Här är ett exempel på en asynkron översättning av en enskild bild med [`recognizeOnceAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#recognizeonceasync) :

```javascript
recognizer.recognizeOnceAsync(result => {
    // Interact with result
});
```

Du måste skriva kod för att hantera resultatet. Det här exemplet utvärderar [`result.reason`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognitionresult) för översättning till tyska:

```javascript
recognizer.recognizeOnceAsync(
  function (result) {
    let translation = result.translations.get("de");
    window.console.log(translation);
    recognizer.close();
  },
  function (err) {
    window.console.log(err);
    recognizer.close();
});
```

Din kod kan också hantera uppdateringar som tillhandahålls medan översättningen bearbetas.
Du kan använda de här uppdateringarna för att ge visuell feedback om översättnings förloppet.
I [det här JavaScript-Node.js exemplet](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/js/node/translation.js) på exempel kod som visar uppdateringar under översättnings processen. Följande kod visar även information som skapas under översättnings processen.

```javascript
recognizer.recognizing = function (s, e) {
    var str = ("(recognizing) Reason: " + SpeechSDK.ResultReason[e.result.reason] +
            " Text: " +  e.result.text +
            " Translation:");
    str += e.result.translations.get("de");
    console.log(str);
};
recognizer.recognized = function (s, e) {
    var str = "\r\n(recognized)  Reason: " + SpeechSDK.ResultReason[e.result.reason] +
            " Text: " + e.result.text +
            " Translation:";
    str += e.result.translations.get("de");
    str += "\r\n";
    console.log(str);
};
```

### <a name="continuous-translation"></a>Kontinuerlig översättning

Kontinuerlig översättning är lite mer engagerande än igenkänning av enstaka steg. Det kräver att du prenumererar på `recognizing` -, `recognized` -och- `canceled` händelserna för att få igenkännings resultatet. Om du vill stoppa översättningen måste du anropa [`stopContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#stopcontinuousrecognitionasync) . Här är ett exempel på hur kontinuerlig översättning utförs på en fil för ljud inspelning.

Vi börjar med att definiera indatamängden och initiera en [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) :

```javascript
const translator = new TranslationRecognizer(speechTranslationConfig);
```

Vi kommer att prenumerera på de händelser som skickas från [`TranslationRecognizer`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer) .

* [`recognizing`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#recognizing): Signal för händelser som innehåller mellanliggande översättnings resultat.
* [`recognized`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#recognized): Signal för händelser som innehåller slutgiltiga översättnings resultat (indikerar ett lyckad översättnings försök).
* [`sessionStopped`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#sessionstopped): Signal för händelser som indikerar slutet på en översättnings session (åtgärd).
* [`canceled`](/javascript/api/microsoft-cognitiveservices-speech-sdk/translationrecognizer#canceled): Signal för händelser som innehåller avbrutna översättnings resultat (som anger ett översättnings försök som avbrutits på grund av ett resultat eller en direkt uppsägnings förfrågan eller, även ett transport-eller protokoll haveri).

```javascript
recognizer.recognizing = (s, e) => {
    console.log(`TRANSLATING: Text=${e.result.text}`);
};
recognizer.recognized = (s, e) => {
    if (e.result.reason == ResultReason.RecognizedSpeech) {
        console.log(`TRANSLATED: Text=${e.result.text}`);
    }
    else if (e.result.reason == ResultReason.NoMatch) {
        console.log("NOMATCH: Speech could not be translated.");
    }
};
recognizer.canceled = (s, e) => {
    console.log(`CANCELED: Reason=${e.reason}`);
    if (e.reason == CancellationReason.Error) {
        console.log(`"CANCELED: ErrorCode=${e.errorCode}`);
        console.log(`"CANCELED: ErrorDetails=${e.errorDetails}`);
        console.log("CANCELED: Did you update the subscription info?");
    }
    recognizer.stopContinuousRecognitionAsync();
};
recognizer.sessionStopped = (s, e) => {
    console.log("\n    Session stopped event.");
    recognizer.stopContinuousRecognitionAsync();
};
```

Med allt konfigurerat kan vi anropa [`startContinuousRecognitionAsync`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechrecognizer#startcontinuousrecognitionasync) .

```javascript
// Starts continuous recognition. Uses stopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync();
// Something later can call, stops recognition.
// recognizer.StopContinuousRecognitionAsync();
```

## <a name="choose-a-source-language"></a>Välj ett käll språk

En vanlig uppgift för tal översättning är att ange språk för indata (eller källa). Låt oss ta en titt på hur du ändrar indatamängds språk till italienska. Leta upp din kod i koden [`SpeechTranslationConfig`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig) och Lägg sedan till följande rad direkt under den.

```javascript
speechTranslationConfig.speechRecognitionLanguage = "it-IT";
```

[`speechRecognitionLanguage`](/javascript/api/microsoft-cognitiveservices-speech-sdk/speechtranslationconfig#speechrecognitionlanguage)Egenskapen förväntar sig en språk språks format sträng. Du kan ange valfritt värde i kolumnen **språk** i listan [över språk som stöds.](../../../language-support.md)

## <a name="choose-one-or-more-target-languages"></a>Välj ett eller flera mål språk

Talet SDK kan översätta till flera mål språk parallellt. Vilka språk som är tillgängliga skiljer sig något från käll språk listan och du anger mål språk med hjälp av en språkkod i stället för en språk kod.
Se en lista över språk koder för text mål i [tabellen för tal översättning på sidan språk stöd](../../../language-support.md#speech-translation). Du kan också hitta information om översättning till syntetiskt språk där.

Följande kod lägger till tyska som mål språk:

```javascript
translationConfig.addTargetLanguage("de");
```

Eftersom det är möjligt med flera översättningar av mål språket, måste din kod ange mål språket när du undersöker resultatet. Följande kod hämtar översättnings resultat för tyska.

```javascript
recognizer.recognized = function (s, e) {
    var str = "\r\n(recognized)  Reason: " +
            sdk.ResultReason[e.result.reason] +
            " Text: " + e.result.text + " Translations:";
    var language = "de";
    str += " [" + language + "] " + e.result.translations.get(language);
    str += "\r\n";
    // show str somewhere
};
```