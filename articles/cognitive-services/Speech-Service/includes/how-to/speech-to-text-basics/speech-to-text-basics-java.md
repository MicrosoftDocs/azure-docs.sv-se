---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.custom: devx-track-java
ms.author: trbye
ms.openlocfilehash: ad0c0023965b68c24d17e1e540b7758115650ecd
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/09/2021
ms.locfileid: "102510900"
---
En av de viktigaste funktionerna i tal tjänsten är möjligheten att känna igen mänskligt tal (kallas ofta tal till text). I den här snabb starten får du lära dig hur du använder tal-SDK i dina appar och produkter för att utföra högkvalitativt tal-till-text-konvertering.

## <a name="skip-to-samples-on-github"></a>Hoppa till exempel på GitHub

Om du vill hoppa över direkt till exempel kod, se [Java snabb starts exemplen](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre) på GitHub.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du har ett Azure-konto och en röst tjänst prenumeration. Om du inte har ett konto och en prenumeration kan du [prova att använda tal tjänsten kostnads fritt](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installera Speech SDK

Innan du kan göra något måste du installera talet SDK. Använd följande instruktioner, beroende på plattform:

* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=jre&pivots=programming-language-java" target="_blank">Java-körning </a>
* <a href="https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/setup-platform?tabs=android&pivots=programming-language-java" target="_blank">Android </a>

## <a name="create-a-speech-configuration"></a>Skapa en tal konfiguration

Om du vill anropa tal tjänsten med hjälp av tal-SDK måste du skapa en [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig) . Den här klassen innehåller information om din prenumeration, till exempel din nyckel och tillhör ande region, slut punkt, värd eller token för auktorisering. Skapa en [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig) med hjälp av din nyckel och region. På sidan [Sök efter nycklar och region](../../../overview.md#find-keys-and-region) hittar du ett nyckel områdes par.

```java
import com.microsoft.cognitiveservices.speech.*;
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Future;

public class Program {
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        SpeechConfig speechConfig = SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
    }
}
```

Det finns några andra sätt som du kan initiera [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig) :

* Med en slut punkt: skicka i en röst tjänst slut punkt. En nyckel eller autentiseringstoken är valfri.
* Med en värd: skicka in en värd adress. En nyckel eller autentiseringstoken är valfri.
* Med en autentiseringstoken: skicka in en autentiseringstoken och den associerade regionen.

> [!NOTE]
> Oavsett om du utför tal igenkänning, tal syntes, översättning eller avsikts igenkänning, skapar du alltid en konfiguration.

## <a name="recognize-from-microphone"></a>Identifiera från mikrofonen

Skapa en med hjälp av om du vill känna igen tal med din enhets mikrofon `AudioConfig` `fromDefaultMicrophoneInput()` . Initiera sedan en [`SpeechRecognizer`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer) , och skicka in `audioConfig` och `config` .

```java
import com.microsoft.cognitiveservices.speech.*;
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Future;

public class Program {
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        SpeechConfig speechConfig = SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
        fromMic(speechConfig);
    }

    public static void fromMic(SpeechConfig speechConfig) throws InterruptedException, ExecutionException {
        AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();
        SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioConfig);

        System.out.println("Speak into your microphone.");
        Future<SpeechRecognitionResult> task = recognizer.recognizeOnceAsync();
        SpeechRecognitionResult result = task.get();
        System.out.println("RECOGNIZED: Text=" + result.getText());
    }
}
```

Om du vill använda en *speciell* enhet för ljud inspelning måste du ange enhets-ID i `AudioConfig` . Lär dig [hur du hämtar enhets-ID](../../../how-to-select-audio-input-devices.md) : t för din enhet för ljud inspelning.

## <a name="recognize-from-file"></a>Identifiera från fil

Om du vill känna igen tal från en ljudfil i stället för att använda en mikrofon måste du ändå skapa en `AudioConfig` . Men när du skapar [`AudioConfig`](/java/api/com.microsoft.cognitiveservices.speech.audio.audioconfig) , i stället för att anropa `fromDefaultMicrophoneInput()` , anropa `fromWavFileInput()` och skicka fil Sök vägen.

```java
import com.microsoft.cognitiveservices.speech.*;
import com.microsoft.cognitiveservices.speech.audio.AudioConfig;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Future;

public class Program {
    public static void main(String[] args) throws InterruptedException, ExecutionException {
        SpeechConfig speechConfig = SpeechConfig.fromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
        fromFile(speechConfig);
    }

    public static void fromFile(SpeechConfig speechConfig) throws InterruptedException, ExecutionException {
        AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
        SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, audioConfig);
        
        Future<SpeechRecognitionResult> task = recognizer.recognizeOnceAsync();
        SpeechRecognitionResult result = task.get();
        System.out.println("RECOGNIZED: Text=" + result.getText());
    }
}
```

## <a name="error-handling"></a>Felhantering

I föregående exempel får du bara den tolkade texten med `result.getText()` , men för att hantera fel och andra svar måste du skriva kod för att hantera resultatet. I följande exempel utvärderas [`result.getReason()`](/java/api/com.microsoft.cognitiveservices.speech.recognitionresult.getreason) och:

* Skriver ut resultatet för igenkänning: `ResultReason.RecognizedSpeech`
* Om det inte finns någon igenkännings matchning, informera användaren: `ResultReason.NoMatch`
* Om ett fel påträffas skriver du ut fel meddelandet: `ResultReason.Canceled`

```java
switch (result.getReason()) {
    case ResultReason.RecognizedSpeech:
        System.out.println("We recognized: " + result.getText());
        exitCode = 0;
        break;
    case ResultReason.NoMatch:
        System.out.println("NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled: {
            CancellationDetails cancellation = CancellationDetails.fromResult(result);
            System.out.println("CANCELED: Reason=" + cancellation.getReason());

            if (cancellation.getReason() == CancellationReason.Error) {
                System.out.println("CANCELED: ErrorCode=" + cancellation.getErrorCode());
                System.out.println("CANCELED: ErrorDetails=" + cancellation.getErrorDetails());
                System.out.println("CANCELED: Did you update the subscription info?");
            }
        }
        break;
}
```

## <a name="continuous-recognition"></a>Kontinuerlig igenkänning

I de föregående exemplen används ett enda bilds igenkännings läge, som identifierar en enda uttryck. Slutet på en enskild uttryck bestäms genom att lyssna efter tystnad i slutet eller tills maximalt 15 sekunders ljud bearbetas.

Kontinuerlig igenkänning används däremot när du vill **styra** när du vill sluta identifiera. Det kräver att du prenumererar på `recognizing` -, `recognized` -och- `canceled` händelserna för att få igenkännings resultatet. Om du vill stoppa igenkänningen måste du anropa [`stopContinuousRecognitionAsync`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync) . Här är ett exempel på hur kontinuerlig igenkänning utförs på en inspelnings fil.

Vi börjar med att definiera indatamängden och initiera en [`SpeechRecognizer`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer) :

```java
AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
SpeechRecognizer recognizer = new SpeechRecognizer(config, audioConfig);
```

Nu ska vi skapa en variabel för att hantera läget för tal igenkänning. För att starta ska vi deklarera en `Semaphore` i klass omfånget.

```java
private static Semaphore stopTranslationWithFileSemaphore;
```

Vi kommer att prenumerera på de händelser som skickas från [`SpeechRecognizer`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer) .

* [`recognizing`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognizing): Signal för händelser som innehåller mellanliggande igenkännings resultat.
* [`recognized`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.recognized): Signal för händelser som innehåller slutgiltiga igenkännings resultat (indikerar ett lyckat igenkännings försök).
* [`sessionStopped`](/java/api/com.microsoft.cognitiveservices.speech.recognizer.sessionstopped): Signal för händelser som indikerar att en avläsnings session avslutas (åtgärd).
* [`canceled`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.canceled): Signal för händelser som innehåller avbrutna igenkännings resultat (vilket indikerar ett igenkännings försök som avbrutits som ett resultat eller en direkt uppsägnings förfrågan eller, alternativt, ett transport-eller protokoll haveri).

```java
// First initialize the semaphore.
stopTranslationWithFileSemaphore = new Semaphore(0);

recognizer.recognizing.addEventListener((s, e) -> {
    System.out.println("RECOGNIZING: Text=" + e.getResult().getText());
});

recognizer.recognized.addEventListener((s, e) -> {
    if (e.getResult().getReason() == ResultReason.RecognizedSpeech) {
        System.out.println("RECOGNIZED: Text=" + e.getResult().getText());
    }
    else if (e.getResult().getReason() == ResultReason.NoMatch) {
        System.out.println("NOMATCH: Speech could not be recognized.");
    }
});

recognizer.canceled.addEventListener((s, e) -> {
    System.out.println("CANCELED: Reason=" + e.getReason());

    if (e.getReason() == CancellationReason.Error) {
        System.out.println("CANCELED: ErrorCode=" + e.getErrorCode());
        System.out.println("CANCELED: ErrorDetails=" + e.getErrorDetails());
        System.out.println("CANCELED: Did you update the subscription info?");
    }

    stopTranslationWithFileSemaphore.release();
});

recognizer.sessionStopped.addEventListener((s, e) -> {
    System.out.println("\n    Session stopped event.");
    stopTranslationWithFileSemaphore.release();
});
```

Med allt konfigurerat kan vi anropa [`startContinuousRecognitionAsync`](/java/api/com.microsoft.cognitiveservices.speech.speechrecognizer.startcontinuousrecognitionasync) .

```java
// Starts continuous recognition. Uses StopContinuousRecognitionAsync() to stop recognition.
recognizer.startContinuousRecognitionAsync().get();

// Waits for completion.
stopTranslationWithFileSemaphore.acquire();

// Stops recognition.
recognizer.stopContinuousRecognitionAsync().get();
```

### <a name="dictation-mode"></a>Diktamensläge

När du använder kontinuerlig igenkänning kan du aktivera dikterings bearbetning genom att använda motsvarande "Aktivera diktering"-funktion. Det här läget kommer att göra att tal konfigurations instansen tolkar ord beskrivningar av menings strukturer som interpunktion. Till exempel skulle uttryck "är du bor i stadens frågetecken" tolkas som texten "är du bor i staden?".

Om du vill aktivera dikteringsläget använder du [`enableDictation`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig.enabledictation) metoden på din [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig) .

```java
config.enableDictation();
```

## <a name="change-source-language"></a>Ändra käll språk

En vanlig uppgift för tal igenkänning anger språk för indata (eller källa). Låt oss ta en titt på hur du ändrar indatamängds språk till franska. Leta upp din kod i koden [`SpeechConfig`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig) och Lägg sedan till den här raden direkt under den.

```java
config.setSpeechRecognitionLanguage("fr-FR");
```

[`setSpeechRecognitionLanguage`](/java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechrecognitionlanguage) är en parameter som tar en sträng som ett argument. Du kan ange ett värde i listan över [språk](../../../language-support.md)som stöds.

## <a name="improve-recognition-accuracy"></a>Förbättra igenkännings precisionen

Fras listor används för att identifiera kända fraser i ljuddata, t. ex. en persons namn eller en viss plats. Genom att tillhandahålla en lista över fraser förbättrar du precisionen för tal igenkänning.

Om du till exempel har ett kommando "flytta till" och ett möjligt mål för "inåt" som kan bli talade kan du lägga till en post för "flytta till inåt". Om du lägger till en fras ökar sannolikheten att när ljudet identifieras som "flytta till inåt" identifieras i stället för att "gå till"

Enstaka ord eller fullständiga fraser kan läggas till i en fras lista. Under igenkänning används en post i en fras lista för att förbättra igenkänningen av ord och fraser i listan även om posterna visas i mitten av uttryck. 

> [!IMPORTANT]
> Fras List funktionen är tillgänglig på följande språk: en-US, de-DE, en-AU, en-CA, en-GB, es-ES, ES-MX, fr-CA, fr-FR, IT-IT, ja-JP, ko-KR, pt-BR, zh-CN

Om du vill använda en fras lista måste du först skapa ett [`PhraseListGrammar`](/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar) objekt och sedan lägga till vissa ord och fraser med [`AddPhrase`](/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar.addphrase#com_microsoft_cognitiveservices_speech_PhraseListGrammar_addPhrase_String_) .

Eventuella ändringar [`PhraseListGrammar`](/java/api/com.microsoft.cognitiveservices.speech.phraselistgrammar) börjar gälla nästa igenkänning eller efter en åter anslutning till tal-tjänsten.

```java
PhraseListGrammar phraseList = PhraseListGrammar.fromRecognizer(recognizer);
phraseList.addPhrase("Supercalifragilisticexpialidocious");
```

Om du behöver rensa fras listan: 

```java
phraseList.clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Andra alternativ för att förbättra igenkännings precisionen

Fras listor är bara ett alternativ för att förbättra igenkännings precisionen. Du kan även: 

* [Förbättra noggrannheten med anpassat tal](../../../custom-speech-overview.md)
* [Förbättra noggrannheten med klientmodeller](../../../tutorial-tenant-model.md)
