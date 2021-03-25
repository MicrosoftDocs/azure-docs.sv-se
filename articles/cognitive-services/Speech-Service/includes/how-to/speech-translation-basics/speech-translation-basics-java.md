---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.custom: devx-track-java
ms.author: trbye
ms.openlocfilehash: e030e6466570d3fc2abf809ca09d46c9f596b765
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105104319"
---
En av de viktigaste funktionerna i tal tjänsten är möjligheten att identifiera mänskligt tal och översätta den till andra språk. I den här snabb starten lär du dig hur du använder tal-SDK i dina appar och produkter för att utföra högkvalitativ tal översättning. Den här snabb starten behandlar ämnen, inklusive:

* Översättning av tal till text
* Översätta tal till flera mål språk
* Utföra direkt tal-till-tal-Översättning

## <a name="skip-to-samples-on-github"></a>Hoppa till exempel på GitHub

Om du vill hoppa över direkt till exempel kod, se [Java snabb starts exemplen](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/java/jre/translate-speech-to-text) på GitHub.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du har ett Azure-konto och en röst tjänst prenumeration. Om du inte har ett konto och en prenumeration kan du [prova att använda tal tjänsten kostnads fritt](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installera Speech SDK

Innan du kan göra något måste du installera talet SDK. Beroende på din plattform följer du anvisningarna under Hämta avsnittet om <a href="/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">tal-SDK</a> i artikeln _om tal SDK_ .

## <a name="import-dependencies"></a>Importera beroenden

Om du vill köra exemplen i den här artikeln inkluderar du följande `import` instruktioner överst i **. Java* -kodsida.

```java
package speech;

import java.io.*;
import java.util.*;
import java.util.concurrent.*;
import com.microsoft.cognitiveservices.speech.*;
import com.microsoft.cognitiveservices.speech.audio.*;
import com.microsoft.cognitiveservices.speech.translation.*;
```

## <a name="sensitive-data-and-environment-variables"></a>Känsliga data och miljövariabler

Exempel käll koden i den här artikeln är beroende av miljövariabler för lagring av känsliga data, till exempel nyckel och region för tal resurs prenumerationen. Java-kodfragmentet innehåller två `static final String` värden som tilldelas från värd datorns miljövariabler, nämligen `SPEECH__SUBSCRIPTION__KEY` och `SPEECH__SERVICE__REGION` . Båda dessa fält är i klass omfånget, vilket gör dem tillgängliga inom metod kroppar av klassen. Mer information om miljövariabler finns i [miljövariabler och program konfiguration](../../../../cognitive-services-security.md#environment-variables-and-application-configuration).

```java
public class App {

    static final String SPEECH__SUBSCRIPTION__KEY = System.getenv("SPEECH__SUBSCRIPTION__KEY");
    static final String SPEECH__SERVICE__REGION = System.getenv("SPEECH__SERVICE__REGION");

    public static void main(String[] args) { }
}
```

## <a name="create-a-speech-translation-configuration"></a>Skapa en konfiguration för tal Översättning

Om du vill anropa tal tjänsten med hjälp av tal-SDK måste du skapa en [`SpeechTranslationConfig`][config] . Den här klassen innehåller information om din prenumeration, till exempel din nyckel och tillhör ande region, slut punkt, värd eller token för auktorisering.

> [!TIP]
> Oavsett om du utför tal igenkänning, tal syntes, översättning eller avsikts igenkänning, skapar du alltid en konfiguration.

Det finns några sätt som du kan initiera en [`SpeechTranslationConfig`][config] :

* Med en prenumeration: skicka i en nyckel och tillhör ande region.
* Med en slut punkt: skicka i en röst tjänst slut punkt. En nyckel eller autentiseringstoken är valfri.
* Med en värd: skicka in en värd adress. En nyckel eller autentiseringstoken är valfri.
* Med en autentiseringstoken: skicka in en autentiseringstoken och den associerade regionen.

Låt oss ta en titt på hur en [`SpeechTranslationConfig`][config] har skapats med hjälp av en nyckel och region. Hämta dessa autentiseringsuppgifter genom att följa stegen i [testa tal tjänsten kostnads fritt](../../../overview.md#try-the-speech-service-for-free).

```java
public class App {

    static final String SPEECH__SUBSCRIPTION__KEY = System.getenv("SPEECH__SERVICE__KEY");
    static final String SPEECH__SERVICE__REGION = System.getenv("SPEECH__SERVICE__REGION");

    public static void main(String[] args) {
        try {
            translateSpeech();
            System.exit(0);
        } catch (Exception ex) {
            System.out.println(ex);
            System.exit(1);
        }
    }

    static void translateSpeech() {
        SpeechTranslationConfig config = SpeechTranslationConfig.fromSubscription(
            SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    }
}
```

## <a name="change-source-language"></a>Ändra käll språk

En vanlig uppgift för tal översättning anger indata (eller käll språket). Låt oss ta en titt på hur du ändrar indatamängds språk till italienska. Interagera med [`SpeechTranslationConfig`][config] instansen i koden och anropar `setSpeechRecognitionLanguage` metoden.

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    // Source (input) language
    translationConfig.setSpeechRecognitionLanguage("it-IT");
}
```

[`setSpeechRecognitionLanguage`][recognitionlang]Funktionen förväntar sig en språk språks format sträng. Du kan ange valfritt värde i kolumnen **språk** i listan [över språk som stöds.](../../../language-support.md)

## <a name="add-translation-language"></a>Lägg till översättnings språk

En annan vanlig uppgift för tal översättning är att ange språk för mål översättning, minst ett krävs men multiplar stöds. Följande kodfragment anger både franska och tyska som översättnings språk mål.

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    translationConfig.setSpeechRecognitionLanguage("it-IT");

    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig.addTargetLanguage("fr");
    translationConfig.addTargetLanguage("de");
}
```

Med varje anrop till [`addTargetLanguage`][addlang] anges ett nytt mål översättnings språk. Med andra ord, när tal identifieras från käll språket, är varje mål översättning tillgänglig som en del av den resulterande översättnings åtgärden.

## <a name="initialize-a-translation-recognizer"></a>Initiera en översättnings tolk

När du har skapat en [`SpeechTranslationConfig`][config] är nästa steg att initiera en [`TranslationRecognizer`][recognizer] . När du initierar en måste [`TranslationRecognizer`][recognizer] du skicka den `translationConfig` . Konfigurationsobjektet ger de autentiseringsuppgifter som krävs av tal tjänsten för att verifiera din begäran.

Om du känner igen tal med hjälp av enhetens standard mikrofon [`TranslationRecognizer`][recognizer] ska du se vad som ska se ut så här:

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
    }
}
```

Om du vill ange enheten för ljud inspelning måste du skapa en [`AudioConfig`][audioconfig] och ange `audioConfig` parametern när du initierar [`TranslationRecognizer`][recognizer] .

> [!TIP]
> [Lär dig hur du hämtar enhets-ID: t för din enhet för ljud inspelning](../../../how-to-select-audio-input-devices.md).

Först ska du referera till `AudioConfig` objektet på följande sätt:

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    AudioConfig audioConfig = AudioConfig.fromDefaultMicrophoneInput();
    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig, audioConfig)) {
        
    }
}
```

Om du vill ange en ljudfil i stället för att använda en mikrofon måste du ändå ange en `audioConfig` . Men när du skapar en [`AudioConfig`][audioconfig] , i stället för att anropa `fromDefaultMicrophoneInput` , kommer du `fromWavFileInput` att anropa och skicka `filename` parametern.

```java
static void translateSpeech() {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    AudioConfig audioConfig = AudioConfig.fromWavFileInput("YourAudioFile.wav");
    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig, audioConfig)) {
        
    }
}
```

## <a name="translate-speech"></a>Översätt tal

För att översätta tal är tal-SDK: n beroende av en mikrofon eller en inspelning av ljud filen. Tal igenkänning inträffar före tal översättning. När alla objekt har initierats anropar du funktionen recognize-Once och hämtar resultatet.

```java
static void translateSpeech() throws ExecutionException, InterruptedException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "it", "fr", "de" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                System.out.printf("Translated into '%s': %s\n", pair.getKey(), pair.getValue());
            }
        }
    }
}
```

Mer information om tal till text finns i [grunderna i tal igenkänning](../../../get-started-speech-to-text.md).

## <a name="synthesize-translations"></a>Syntetisera översättningar

Efter en lyckad tal igenkänning och översättning innehåller resultatet alla översättningar i en ord lista. [`getTranslations`][translations]Funktionen returnerar en ord lista med nyckeln som mål översättnings språk och värdet är den översatta texten. Identifierat tal kan översättas och sedan syntetiseras på ett annat språk (tal-till-tal).

### <a name="event-based-synthesis"></a>Händelse-baserad syntes

`TranslationRecognizer`Objektet visar en `synthesizing` händelse. Händelsen utlöses flera gånger och ger en mekanism för att hämta det syntetiserade ljudet från resultatet av översättnings igenkänningen. Om du översätter till flera språk, se [manuell syntes](#manual-synthesis). Ange syntes rösten genom att tilldela en [`setVoiceName`][voicename] och ange en händelse hanterare för `synthesizing` händelsen, Hämta ljudet. I följande exempel sparas det översatta ljudet som en *. wav* -fil.

> [!IMPORTANT]
> Den händelsebaserade syntesen fungerar bara med en enda översättning, Lägg **inte** till flera mål översättnings språk. Dessutom bör det [`setVoiceName`][voicename] vara samma språk som mål översättnings språket, till exempel, `"de"` kan mappas till `"de-DE-Hedda"` .

```java
static void translateSpeech() throws ExecutionException, FileNotFoundException, InterruptedException, IOException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    String fromLanguage = "en-US";
    String toLanguage = "de";
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    translationConfig.addTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig.setVoiceName("de-DE-Hedda");

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        recognizer.synthesizing.addEventListener((s, e) -> {
            byte[] audio = e.getResult().getAudio();
            int size = audio.length;
            System.out.println("Audio synthesized: " + size + " byte(s)" + (size == 0 ? "(COMPLETE)" : ""));

            if (size > 0) {
                try (FileOutputStream file = new FileOutputStream("translation.wav")) {
                    file.write(audio);
                } catch (IOException ex) {
                    ex.printStackTrace();
                }
            }
        });

        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                String language = pair.getKey();
                String translation = pair.getValue();
                System.out.printf("Translated into '%s': %s\n", language, translation);
            }
        }
    }
}
```

### <a name="manual-synthesis"></a>Manuell syntes

[`getTranslations`][translations]Funktionen returnerar en ord lista som kan användas för att syntetisera ljud från översättnings texten. Iterera igenom varje översättning och syntetisera översättningen. När du skapar en `SpeechSynthesizer` instans `SpeechConfig` måste dess [`setSpeechSynthesisVoiceName`][speechsynthesisvoicename] egenskap ha angetts till önskad röst för objektet. I följande exempel översätts till fem språk och varje översättning syntetiseras sedan till en ljudfil på motsvarande neurala-språk.

```java
static void translateSpeech() throws ExecutionException, InterruptedException {
    SpeechTranslationConfig translationConfig = SpeechTranslationConfig.fromSubscription(
        SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    String fromLanguage = "en-US";
    String[] toLanguages = { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig.setSpeechRecognitionLanguage(fromLanguage);
    for (String language : toLanguages) {
        translationConfig.addTargetLanguage(language);
    }

    try (TranslationRecognizer recognizer = new TranslationRecognizer(translationConfig)) {
        System.out.printf("Say something in '%s' and we'll translate...", fromLanguage);

        TranslationRecognitionResult result = recognizer.recognizeOnceAsync().get();
        if (result.getReason() == ResultReason.TranslatedSpeech) {
            // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
            Map<String, String> languageToVoiceMap = new HashMap<String, String>();
            languageToVoiceMap.put("de", "de-DE-KatjaNeural");
            languageToVoiceMap.put("en", "en-US-AriaNeural");
            languageToVoiceMap.put("it", "it-IT-ElsaNeural");
            languageToVoiceMap.put("pt", "pt-BR-FranciscaNeural");
            languageToVoiceMap.put("zh-Hans", "zh-CN-XiaoxiaoNeural");

            System.out.printf("Recognized: \"%s\"\n", result.getText());
            for (Map.Entry<String, String> pair : result.getTranslations().entrySet()) {
                String language = pair.getKey();
                String translation = pair.getValue();
                System.out.printf("Translated into '%s': %s\n", language, translation);

                SpeechConfig speechConfig =
                    SpeechConfig.fromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
                speechConfig.setSpeechSynthesisVoiceName(languageToVoiceMap.get(language));

                AudioConfig audioConfig = AudioConfig.fromWavFileOutput(language + "-translation.wav");
                try (SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig)) {
                    synthesizer.SpeakTextAsync(translation).get();
                }
            }
        }
    }
}
```

Mer information om tal syntes finns i [grunderna för tal syntes](../../../get-started-text-to-speech.md).

[config]: /java/api/com.microsoft.cognitiveservices.speech.translation.SpeechTranslationConfig
[audioconfig]: /java/api/com.microsoft.cognitiveservices.speech.audio.AudioConfig
[recognizer]: /java/api/com.microsoft.cognitiveservices.speech.translation.TranslationRecognizer
[recognitionlang]: /java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechrecognitionlanguage
[addlang]: /java/api/com.microsoft.cognitiveservices.speech.translation.speechtranslationconfig.addtargetlanguage
[translations]: /java/api/com.microsoft.cognitiveservices.speech.translation.translationrecognitionresult.gettranslations
[voicename]: /java/api/com.microsoft.cognitiveservices.speech.translation.speechtranslationconfig.setvoicename
[speechsynthesisvoicename]: /java/api/com.microsoft.cognitiveservices.speech.speechconfig.setspeechsynthesisvoicename