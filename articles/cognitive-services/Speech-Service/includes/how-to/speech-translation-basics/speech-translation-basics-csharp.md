---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 04/13/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: 798db8e438a78fb818bd0af18f84c87c1f9ceb84
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105105201"
---
En av de viktigaste funktionerna i tal tjänsten är möjligheten att identifiera mänskligt tal och översätta den till andra språk. I den här snabb starten lär du dig hur du använder tal-SDK i dina appar och produkter för att utföra högkvalitativ tal översättning. Den här snabb starten behandlar ämnen, inklusive:

* Översättning av tal till text
* Översätta tal till flera mål språk
* Utföra direkt tal-till-tal-Översättning

## <a name="skip-to-samples-on-github"></a>Hoppa till exempel på GitHub

Om du vill hoppa över direkt till exempel kod, se [snabb starts exemplen för C#](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet/translate-speech-to-text) på GitHub.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du har ett Azure-konto och en röst tjänst prenumeration. Om du inte har ett konto och en prenumeration kan du [prova att använda tal tjänsten kostnads fritt](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installera Speech SDK

Innan du kan göra något måste du installera talet SDK. Beroende på din plattform följer du anvisningarna under Hämta avsnittet om <a href="/azure/cognitive-services/speech-service/speech-sdk#get-the-speech-sdk" target="_blank">tal-SDK</a> i artikeln _om tal SDK_ .

## <a name="import-dependencies"></a>Importera beroenden

Om du vill köra exemplen i den här artikeln inkluderar du följande `using` instruktioner överst i filen *program. cs* .

```csharp
using System;
using System.Collections.Generic;
using System.IO;
using System.Text;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;
using Microsoft.CognitiveServices.Speech.Translation;
```

## <a name="sensitive-data-and-environment-variables"></a>Känsliga data och miljövariabler

Exempel käll koden i den här artikeln är beroende av miljövariabler för lagring av känsliga data, till exempel nyckel och region för tal resurs prenumerationen. `Program`Klassen innehåller två `static readonly string` värden som tilldelas från värd datorns miljövariabler, nämligen `SPEECH__SUBSCRIPTION__KEY` och `SPEECH__SERVICE__REGION` . Båda dessa fält är i klass omfånget, vilket gör dem tillgängliga inom metod kroppar av klassen. Mer information om miljövariabler finns i [miljövariabler och program konfiguration](../../../../cognitive-services-security.md#environment-variables-and-application-configuration).

```csharp
public class Program
{
    static readonly string SPEECH__SUBSCRIPTION__KEY =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SUBSCRIPTION__KEY));
    
    static readonly string SPEECH__SERVICE__REGION =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SERVICE__REGION));

    static Task Main() => Task.CompletedTask;
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

```csharp
public class Program
{
    static readonly string SPEECH__SUBSCRIPTION__KEY =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SUBSCRIPTION__KEY));
    
    static readonly string SPEECH__SERVICE__REGION =
        Environment.GetEnvironmentVariable(nameof(SPEECH__SERVICE__REGION));

    static Task Main() => TranslateSpeechAsync();

    static async Task TranslateSpeechAsync()
    {
        var translationConfig =
            SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    }
}
```

## <a name="change-source-language"></a>Ändra käll språk

En vanlig uppgift för tal översättning anger indata (eller käll språket). Låt oss ta en titt på hur du ändrar indatamängds språk till italienska. I din kod interagerar du med [`SpeechTranslationConfig`][config] instansen och tilldelar `SpeechRecognitionLanguage` egenskapen.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    // Source (input) language
    translationConfig.SpeechRecognitionLanguage = "it-IT";
}
```

[`SpeechRecognitionLanguage`][recognitionlang]Egenskapen förväntar sig en språk språks format sträng. Du kan ange valfritt värde i kolumnen **språk** i listan [över språk som stöds.](../../../language-support.md)

## <a name="add-translation-language"></a>Lägg till översättnings språk

En annan vanlig uppgift för tal översättning är att ange språk för mål översättning, minst ett krävs men multiplar stöds. Följande kodfragment anger både franska och tyska som översättnings språk mål.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    translationConfig.SpeechRecognitionLanguage = "it-IT";
    
    // Translate to languages. See, https://aka.ms/speech/sttt-languages
    translationConfig.AddTargetLanguage("fr");
    translationConfig.AddTargetLanguage("de");
}
```

Med varje anrop till [`AddTargetLanguage`][addlang] anges ett nytt mål översättnings språk. Med andra ord, när tal identifieras från käll språket, är varje mål översättning tillgänglig som en del av den resulterande översättnings åtgärden.

## <a name="initialize-a-translation-recognizer"></a>Initiera en översättnings tolk

När du har skapat en [`SpeechTranslationConfig`][config] är nästa steg att initiera en [`TranslationRecognizer`][recognizer] . När du initierar en måste [`TranslationRecognizer`][recognizer] du skicka den `translationConfig` . Konfigurationsobjektet ger de autentiseringsuppgifter som krävs av tal tjänsten för att verifiera din begäran.

Om du känner igen tal med hjälp av enhetens standard mikrofon [`TranslationRecognizer`][recognizer] ska du se vad som ska se ut så här:

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);

    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);
}
```

Om du vill ange enheten för ljud inspelning måste du skapa en [`AudioConfig`][audioconfig] och ange `audioConfig` parametern när du initierar [`TranslationRecognizer`][recognizer] .

> [!TIP]
> [Lär dig hur du hämtar enhets-ID: t för din enhet för ljud inspelning](../../../how-to-select-audio-input-devices.md).

Först ska du referera till `AudioConfig` objektet på följande sätt:

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
    using var recognizer = new TranslationRecognizer(translationConfig, audioConfig);
}
```

Om du vill ange en ljudfil i stället för att använda en mikrofon måste du ändå ange en `audioConfig` . Men när du skapar en [`AudioConfig`][audioconfig] , i stället för att anropa `FromDefaultMicrophoneInput` , kommer du `FromWavFileInput` att anropa och skicka `filename` parametern.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var audioConfig = AudioConfig.FromWavFileInput("YourAudioFile.wav");
    using var recognizer = new TranslationRecognizer(translationConfig, audioConfig);
}
```

## <a name="translate-speech"></a>Översätt tal

För att översätta tal är tal-SDK: n beroende av en mikrofon eller en inspelning av ljud filen. Tal igenkänning inträffar före tal översättning. När alla objekt har initierats anropar du funktionen recognize-Once och hämtar resultatet.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "it", "fr", "de" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{string.Join("', '", toLanguages)}'.\n");
    
    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        Console.WriteLine($"Recognized: \"{result.Text}\":");
        foreach (var (language, translation) in result.Translations)
        {
            Console.WriteLine($"Translated into '{language}': {translation}");
        }
    }
}
```

Mer information om tal till text finns i [grunderna i tal igenkänning](../../../get-started-speech-to-text.md).

## <a name="synthesize-translations"></a>Syntetisera översättningar

Efter en lyckad tal igenkänning och översättning innehåller resultatet alla översättningar i en ord lista. [`Translations`][translations]Ord listans nyckel är mål översättnings språket och värdet är den översatta texten. Identifierat tal kan översättas och sedan syntetiseras på ett annat språk (tal-till-tal).

### <a name="event-based-synthesis"></a>Händelse-baserad syntes

`TranslationRecognizer`Objektet visar en `Synthesizing` händelse. Händelsen utlöses flera gånger och ger en mekanism för att hämta det syntetiserade ljudet från resultatet av översättnings igenkänningen. Om du översätter till flera språk, se [manuell syntes](#manual-synthesis). Ange syntes rösten genom att tilldela en [`VoiceName`][voicename] och ange en händelse hanterare för `Synthesizing` händelsen, Hämta ljudet. I följande exempel sparas det översatta ljudet som en *. wav* -fil.

> [!IMPORTANT]
> Den händelsebaserade syntesen fungerar bara med en enda översättning, Lägg **inte** till flera mål översättnings språk. Dessutom bör det [`VoiceName`][voicename] vara samma språk som mål översättnings språket, till exempel, `"de"` kan mappas till `"de-DE-Hedda"` .

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SUBSCRIPTION__KEY, SPEECH__SERVICE__REGION);
    
    var fromLanguage = "en-US";
    var toLanguage = "de";
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    translationConfig.AddTargetLanguage(toLanguage);

    // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
    translationConfig.VoiceName = "de-DE-Hedda";

    using var recognizer = new TranslationRecognizer(translationConfig);

    recognizer.Synthesizing += (_, e) =>
    {
        var audio = e.Result.GetAudio();
        Console.WriteLine($"Audio synthesized: {audio.Length:#,0} byte(s) {(audio.Length == 0 ? "(Complete)" : "")}");

        if (audio.Length > 0)
        {
            File.WriteAllBytes("YourAudioFile.wav", audio);
        }
    };

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{toLanguage}'.\n");

    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        Console.WriteLine($"Recognized: \"{result.Text}\"");
        Console.WriteLine($"Translated into '{toLanguage}': {result.Translations[toLanguage]}");
    }
}
```

### <a name="manual-synthesis"></a>Manuell syntes

[`Translations`][translations]Ord listan kan användas för att syntetisera ljud från översättnings texten. Iterera igenom varje översättning och syntetisera översättningen. När du skapar en `SpeechSynthesizer` instans `SpeechConfig` måste dess [`SpeechSynthesisVoiceName`][speechsynthesisvoicename] egenskap ha angetts till önskad röst för objektet. I följande exempel översätts till fem språk och varje översättning syntetiseras sedan till en ljudfil på motsvarande neurala-språk.

```csharp
static async Task TranslateSpeechAsync()
{
    var translationConfig =
        SpeechTranslationConfig.FromSubscription(SPEECH__SERVICE__KEY, SPEECH__SERVICE__REGION);

    var fromLanguage = "en-US";
    var toLanguages = new List<string> { "de", "en", "it", "pt", "zh-Hans" };
    translationConfig.SpeechRecognitionLanguage = fromLanguage;
    toLanguages.ForEach(translationConfig.AddTargetLanguage);

    using var recognizer = new TranslationRecognizer(translationConfig);

    Console.Write($"Say something in '{fromLanguage}' and ");
    Console.WriteLine($"we'll translate into '{string.Join("', '", toLanguages)}'.\n");

    var result = await recognizer.RecognizeOnceAsync();
    if (result.Reason == ResultReason.TranslatedSpeech)
    {
        // See: https://aka.ms/speech/sdkregion#standard-and-neural-voices
        var languageToVoiceMap = new Dictionary<string, string>
        {
            ["de"] = "de-DE-KatjaNeural",
            ["en"] = "en-US-AriaNeural",
            ["it"] = "it-IT-ElsaNeural",
            ["pt"] = "pt-BR-FranciscaNeural",
            ["zh-Hans"] = "zh-CN-XiaoxiaoNeural"
        };

        Console.WriteLine($"Recognized: \"{result.Text}\"");

        foreach (var (language, translation) in result.Translations)
        {
            Console.WriteLine($"Translated into '{language}': {translation}");

            var speechConfig =
                SpeechConfig.FromSubscription(
                    SPEECH__SERVICE__KEY, SPEECH__SERVICE__REGION);
            speechConfig.SpeechSynthesisVoiceName = languageToVoiceMap[language];

            using var audioConfig = AudioConfig.FromWavFileOutput($"{language}-translation.wav");
            using var synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);
            
            await synthesizer.SpeakTextAsync(translation);
        }
    }
}
```

Mer information om tal syntes finns i [grunderna för tal syntes](../../../get-started-text-to-speech.md).

[config]: /dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig
[audioconfig]: /dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig
[recognizer]: /dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognizer
[recognitionlang]: /dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechrecognitionlanguage
[addlang]: /dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.addtargetlanguage
[translations]: /dotnet/api/microsoft.cognitiveservices.speech.translation.translationrecognitionresult.translations
[voicename]: /dotnet/api/microsoft.cognitiveservices.speech.speechtranslationconfig.voicename
[speechsynthesisvoicename]: /dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechsynthesisvoicename