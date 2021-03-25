---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: trbye
ms.custom: devx-track-csharp
ms.openlocfilehash: fdcbf2622ef92b7659ae55547b1afb4bcdfa236d
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105105842"
---
En av de viktigaste funktionerna i tal tjänsten är möjligheten att känna igen mänskligt tal (kallas ofta tal till text). I den här snabb starten får du lära dig hur du använder tal-SDK i dina appar och produkter för att utföra högkvalitativt tal-till-text-konvertering.

## <a name="skip-to-samples-on-github"></a>Hoppa till exempel på GitHub

Om du vill hoppa över direkt till exempel kod, se [snabb starts exemplen för C#](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart/csharp/dotnet) på GitHub.

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du har ett Azure-konto och en röst tjänst prenumeration. Om du inte har ett konto och en prenumeration kan du [prova att använda tal tjänsten kostnads fritt](../../../overview.md#try-the-speech-service-for-free).

## <a name="install-the-speech-sdk"></a>Installera Speech SDK

Om du bara vill att paket namnet ska komma igång kör `Install-Package Microsoft.CognitiveServices.Speech` du i NuGet-konsolen.

Information om plattformsspecifika installations anvisningar finns i följande länkar:

* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=dotnet" target="_blank">.NET Framework </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=dotnetcore" target="_blank">.NET Core </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=unity" target="_blank">Unity </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=uwps" target="_blank">UWP </a>
* <a href="/azure/cognitive-services/speech-service/quickstarts/setup-platform?pivots=programming-language-csharp&tabs=xaml" target="_blank">Xamarin </a>

## <a name="create-a-speech-configuration"></a>Skapa en tal konfiguration

Om du vill anropa tal tjänsten med hjälp av tal-SDK måste du skapa en [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig) . Den här klassen innehåller information om din prenumeration, till exempel din nyckel och tillhör ande region, slut punkt, värd eller token för auktorisering. Skapa en [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig) med hjälp av din nyckel och region. På sidan [Sök efter nycklar och region](../../../overview.md#find-keys-and-region) hittar du ett nyckel områdes par.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

class Program 
{
    async static Task Main(string[] args)
    {
        var speechConfig = SpeechConfig.FromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
    }
}
```

Det finns några andra sätt som du kan initiera [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig) :

* Med en slut punkt: skicka i en röst tjänst slut punkt. En nyckel eller autentiseringstoken är valfri.
* Med en värd: skicka in en värd adress. En nyckel eller autentiseringstoken är valfri.
* Med en autentiseringstoken: skicka in en autentiseringstoken och den associerade regionen.

> [!NOTE]
> Oavsett om du utför tal igenkänning, tal syntes, översättning eller avsikts igenkänning, skapar du alltid en konfiguration.

## <a name="recognize-from-microphone"></a>Identifiera från mikrofonen

Skapa en med hjälp av om du vill känna igen tal med din enhets mikrofon `AudioConfig` `FromDefaultMicrophoneInput()` . Initiera sedan en [`SpeechRecognizer`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer) , och skicka in `audioConfig` och `speechConfig` .

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

class Program 
{
    async static Task FromMic(SpeechConfig speechConfig)
    {
        using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
        using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

        Console.WriteLine("Speak into your microphone.");
        var result = await recognizer.RecognizeOnceAsync();
        Console.WriteLine($"RECOGNIZED: Text={result.Text}");
    }

    async static Task Main(string[] args)
    {
        var speechConfig = SpeechConfig.FromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
        await FromMic(speechConfig);
    }
}
```

Om du vill använda en *speciell* enhet för ljud inspelning måste du ange enhets-ID i `AudioConfig` . Lär dig [hur du hämtar enhets-ID](../../../how-to-select-audio-input-devices.md) : t för din enhet för ljud inspelning.

## <a name="recognize-from-file"></a>Identifiera från fil

Om du vill känna igen tal från en ljudfil i stället för en mikrofon måste du ändå skapa en `AudioConfig` . Men när du skapar i [`AudioConfig`](/dotnet/api/microsoft.cognitiveservices.speech.audio.audioconfig) stället för att anropa anropar `FromDefaultMicrophoneInput()` du `FromWavFileInput()` och överför fil Sök vägen.

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

class Program 
{
    async static Task FromFile(SpeechConfig speechConfig)
    {
        using var audioConfig = AudioConfig.FromWavFileInput("PathToFile.wav");
        using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

        var result = await recognizer.RecognizeOnceAsync();
        Console.WriteLine($"RECOGNIZED: Text={result.Text}");
    }

    async static Task Main(string[] args)
    {
        var speechConfig = SpeechConfig.FromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
        await FromFile(speechConfig);
    }
}
```

## <a name="recognize-from-in-memory-stream"></a>Identifiera från minnes intern ström

För många användnings fall är det troligt att ljud data kommer från Blob Storage eller att det på annat sätt redan finns i minnet som en `byte[]` eller liknande rå data struktur. I följande exempel används en [`PushAudioInputStream`](/dotnet/api/microsoft.cognitiveservices.speech.audio.pushaudioinputstream) för att känna igen tal, som i princip är en abstrakt minnes ström. Exempel koden gör följande:

* Skriver rå ljuddata (PCM) till `PushAudioInputStream` `Write()` funktionen using, som accepterar en `byte[]` .
* Läser en `.wav` fil med hjälp av ett `FileReader` i demonstrations syfte, men om du redan har ljuddata i en `byte[]` kan du hoppa direkt till att skriva innehållet till indataströmmen.
* Standardvärdet är 16 bitar, 16khz mono PCM. Om du vill anpassa formatet kan du skicka ett [`AudioStreamFormat`](/dotnet/api/microsoft.cognitiveservices.speech.audio.audiostreamformat) objekt till `CreatePushStream()` med hjälp av den statiska funktionen `AudioStreamFormat.GetWaveFormatPCM(sampleRate, (byte)bitRate, (byte)channels)` .

```csharp
using System;
using System.IO;
using System.Threading.Tasks;
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

class Program 
{
    async static Task FromStream(SpeechConfig speechConfig)
    {
        var reader = new BinaryReader(File.OpenRead("PathToFile.wav"));
        using var audioInputStream = AudioInputStream.CreatePushStream();
        using var audioConfig = AudioConfig.FromStreamInput(audioInputStream);
        using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);

        byte[] readBytes;
        do
        {
            readBytes = reader.ReadBytes(1024);
            audioInputStream.Write(readBytes, readBytes.Length);
        } while (readBytes.Length > 0);

        var result = await recognizer.RecognizeOnceAsync();
        Console.WriteLine($"RECOGNIZED: Text={result.Text}");
    }

    async static Task Main(string[] args)
    {
        var speechConfig = SpeechConfig.FromSubscription("<paste-your-subscription-key>", "<paste-your-region>");
        await FromStream(speechConfig);
    }
}
```

Om du använder en push-dataström som indata förutsätter vi att ljuddata är en RAW PCM, t. ex. om du hoppar över rubriker.
API fungerar fortfarande i vissa fall om huvudet inte har hoppats över, men för bästa resultat bör du överväga att implementera logiken för att läsa upp rubrikerna så att `byte[]` börjar i *början av ljuddata*.

## <a name="error-handling"></a>Felhantering

I föregående exempel får du bara den tolkade texten från `result.text` , men för att hantera fel och andra svar måste du skriva kod för att hantera resultatet. Följande kod utvärderar [`result.Reason`](/dotnet/api/microsoft.cognitiveservices.speech.recognitionresult.reason) egenskapen och:

* Skriver ut resultatet för igenkänning: `ResultReason.RecognizedSpeech`
* Om det inte finns någon igenkännings matchning, informera användaren: `ResultReason.NoMatch`
* Om ett fel påträffas skriver du ut fel meddelandet: `ResultReason.Canceled`

```csharp
switch (result.Reason)
{
    case ResultReason.RecognizedSpeech:
        Console.WriteLine($"RECOGNIZED: Text={result.Text}");
        break;
    case ResultReason.NoMatch:
        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
        break;
    case ResultReason.Canceled:
        var cancellation = CancellationDetails.FromResult(result);
        Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");

        if (cancellation.Reason == CancellationReason.Error)
        {
            Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
            Console.WriteLine($"CANCELED: ErrorDetails={cancellation.ErrorDetails}");
            Console.WriteLine($"CANCELED: Did you update the subscription info?");
        }
        break;
}
```

## <a name="continuous-recognition"></a>Kontinuerlig igenkänning

I de föregående exemplen används ett enda bilds igenkännings läge, som identifierar en enda uttryck. Slutet på en enskild uttryck bestäms genom att lyssna efter tystnad i slutet eller tills maximalt 15 sekunders ljud bearbetas.

Kontinuerlig igenkänning används däremot när du vill **styra** när du vill sluta identifiera. Det kräver att du prenumererar på `Recognizing` -, `Recognized` -och- `Canceled` händelserna för att få igenkännings resultatet. Om du vill stoppa igenkänningen måste du anropa [`StopContinuousRecognitionAsync`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.stopcontinuousrecognitionasync) . Här är ett exempel på hur kontinuerlig igenkänning utförs på en inspelnings fil.

Börja med att definiera indatamängden och initiera en [`SpeechRecognizer`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer) :

```csharp
using var audioConfig = AudioConfig.FromWavFileInput("YourAudioFile.wav");
using var recognizer = new SpeechRecognizer(speechConfig, audioConfig);
```

Skapa sedan en `TaskCompletionSource<int>` för att hantera läget för tal igenkänning.

```csharp
var stopRecognition = new TaskCompletionSource<int>();
```

Prenumerera sedan på de händelser som skickas från [`SpeechRecognizer`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer) .

* [`Recognizing`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognizing): Signal för händelser som innehåller mellanliggande igenkännings resultat.
* [`Recognized`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.recognized): Signal för händelser som innehåller slutgiltiga igenkännings resultat (indikerar ett lyckat igenkännings försök).
* [`SessionStopped`](/dotnet/api/microsoft.cognitiveservices.speech.recognizer.sessionstopped): Signal för händelser som indikerar att en avläsnings session avslutas (åtgärd).
* [`Canceled`](/dotnet/api/microsoft.cognitiveservices.speech.speechrecognizer.canceled): Signal för händelser som innehåller avbrutna igenkännings resultat (vilket indikerar ett igenkännings försök som avbrutits som ett resultat eller en direkt uppsägnings förfrågan eller, alternativt, ett transport-eller protokoll haveri).

```csharp
recognizer.Recognizing += (s, e) =>
{
    Console.WriteLine($"RECOGNIZING: Text={e.Result.Text}");
};

recognizer.Recognized += (s, e) =>
{
    if (e.Result.Reason == ResultReason.RecognizedSpeech)
    {
        Console.WriteLine($"RECOGNIZED: Text={e.Result.Text}");
    }
    else if (e.Result.Reason == ResultReason.NoMatch)
    {
        Console.WriteLine($"NOMATCH: Speech could not be recognized.");
    }
};

recognizer.Canceled += (s, e) =>
{
    Console.WriteLine($"CANCELED: Reason={e.Reason}");

    if (e.Reason == CancellationReason.Error)
    {
        Console.WriteLine($"CANCELED: ErrorCode={e.ErrorCode}");
        Console.WriteLine($"CANCELED: ErrorDetails={e.ErrorDetails}");
        Console.WriteLine($"CANCELED: Did you update the subscription info?");
    }

    stopRecognition.TrySetResult(0);
};

recognizer.SessionStopped += (s, e) =>
{
    Console.WriteLine("\n    Session stopped event.");
    stopRecognition.TrySetResult(0);
};
```

När allt är konfigurerat kan `StartContinuousRecognitionAsync` du ringa till Start igenkänning.

```csharp
await recognizer.StartContinuousRecognitionAsync();

// Waits for completion. Use Task.WaitAny to keep the task rooted.
Task.WaitAny(new[] { stopRecognition.Task });

// make the following call at some point to stop recognition.
// await recognizer.StopContinuousRecognitionAsync();
```

### <a name="dictation-mode"></a>Diktamensläge

När du använder kontinuerlig igenkänning kan du aktivera dikterings bearbetning genom att använda motsvarande "Aktivera diktering"-funktion. Det här läget kommer att göra att tal konfigurations instansen tolkar ord beskrivningar av menings strukturer som interpunktion. Till exempel skulle uttryck "är du bor i stadens frågetecken" tolkas som texten "är du bor i staden?".

Om du vill aktivera dikteringsläget använder du [`EnableDictation`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.enabledictation) metoden på din [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig) .

```csharp
speechConfig.EnableDictation();
```

## <a name="change-source-language"></a>Ändra käll språk

En vanlig uppgift för tal igenkänning anger språk för indata (eller källa). Låt oss ta en titt på hur du ändrar indatamängds språk till italienska. Leta upp din kod i koden [`SpeechConfig`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig) och Lägg sedan till den här raden direkt under den.

```csharp
speechConfig.SpeechRecognitionLanguage = "it-IT";
```

[`SpeechRecognitionLanguage`](/dotnet/api/microsoft.cognitiveservices.speech.speechconfig.speechrecognitionlanguage)Egenskapen förväntar sig en språk språks format sträng. Du kan ange valfritt värde i kolumnen **språk** i listan [över språk som stöds.](../../../language-support.md)

## <a name="improve-recognition-accuracy"></a>Förbättra igenkännings precisionen

Fras listor används för att identifiera kända fraser i ljuddata, t. ex. en persons namn eller en viss plats. Genom att tillhandahålla en lista över fraser förbättrar du precisionen för tal igenkänning.

Om du till exempel har ett kommando "flytta till" och ett möjligt mål för "inåt" som kan bli talade kan du lägga till en post för "flytta till inåt". Om du lägger till en fras ökar sannolikheten att när ljudet identifieras som "flytta till inåt" identifieras i stället för att "gå till"

Enstaka ord eller fullständiga fraser kan läggas till i en fras lista. Under igenkänning används en post i en fras lista för att förbättra igenkänningen av ord och fraser i listan även om posterna visas i mitten av uttryck. 

> [!IMPORTANT]
> Fras List funktionen är tillgänglig på följande språk: en-US, de-DE, en-AU, en-CA, en-GB, es-ES, ES-MX, fr-CA, fr-FR, IT-IT, ja-JP, ko-KR, pt-BR, zh-CN

Om du vill använda en fras lista måste du först skapa ett [`PhraseListGrammar`](/dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar) objekt och sedan lägga till vissa ord och fraser med [`AddPhrase`](/dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar.addphrase) .

Eventuella ändringar [`PhraseListGrammar`](/dotnet/api/microsoft.cognitiveservices.speech.phraselistgrammar) börjar gälla nästa igenkänning eller efter en åter anslutning till tal-tjänsten.

```csharp
var phraseList = PhraseListGrammar.FromRecognizer(recognizer);
phraseList.AddPhrase("Supercalifragilisticexpialidocious");
```

Om du behöver rensa fras listan: 

```csharp
phraseList.Clear();
```

### <a name="other-options-to-improve-recognition-accuracy"></a>Andra alternativ för att förbättra igenkännings precisionen

Fras listor är bara ett alternativ för att förbättra igenkännings precisionen. Du kan även: 

* [Förbättra noggrannheten med anpassat tal](../../../custom-speech-overview.md)
* [Förbättra noggrannheten med klientmodeller](../../../tutorial-tenant-model.md)