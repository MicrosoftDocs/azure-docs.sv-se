---
author: trevorbye
ms.service: cognitive-services
ms.topic: include
ms.date: 01/04/2021
ms.author: trbye
ms.openlocfilehash: 407906727332f3db8d3d0a6840d0c865c6b33ff7
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105609449"
---
Börja med att läsa in din nyckelords modell fil med hjälp av den `FromFile()` statiska funktionen, som returnerar en `KeywordRecognitionModel` . Använd sökvägen till `.table` filen som du laddade ned från tal Studio. Dessutom skapar du en `AudioConfig` med hjälp av standard mikrofonen och instansierar sedan en ny `KeywordRecognizer` med hjälp av ljud konfigurationen.

```csharp
using Microsoft.CognitiveServices.Speech;
using Microsoft.CognitiveServices.Speech.Audio;

var keywordModel = KeywordRecognitionModel.FromFile("your/path/to/Activate_device.table");
using var audioConfig = AudioConfig.FromDefaultMicrophoneInput();
using var keywordRecognizer = new KeywordRecognizer(audioConfig);
```

Sedan utförs körning av nyckelords igenkänning med ett anrop till `RecognizeOnceAsync()` genom att skicka modell objekt. Detta startar en session för att känna igen som varar tills nyckelordet identifieras. Därför använder du vanligt vis det här design mönstret i program med flera trådar, eller i användnings fall där du kan vänta ett Väcknings ord i oändlighet.

```csharp
KeywordRecognitionResult result = await keywordRecognizer.RecognizeOnceAsync(keywordModel);
```

> [!NOTE]
> Exemplet som visas här använder lokal nyckelords igenkänning eftersom det inte kräver ett `SpeechConfig` objekt för autentiserings kontext och inte kontaktar Server delen. Du kan dock köra både nyckelords igenkänning och verifiering med [en direkt Server dels anslutning](../../../tutorial-voice-enable-your-bot-speech-sdk.md#view-the-source-code-that-enables-keyword).

### <a name="continuous-recognition"></a>Kontinuerlig igenkänning

Andra klasser i tal-SDK har stöd för kontinuerlig igenkänning (för både tal-och avsikts igenkänning) med nyckelords igenkänning. På så sätt kan du använda samma kod som du normalt använder för kontinuerlig igenkänning, med möjlighet att referera till en `.table` fil för din nyckelords modell.

För tal till text följer du samma design mönster som visas i [snabb](../../../get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=script%2cbrowser%2cwindowsinstall#continuous-recognition) starten för att konfigurera kontinuerlig igenkänning. Ersätt sedan anropet till `recognizer.StartContinuousRecognitionAsync()` med och `recognizer.StartKeywordRecognitionAsync(KeywordRecognitionModel)` Skicka ditt `KeywordRecognitionModel` objekt. Om du vill stoppa kontinuerlig igenkänning med nyckelords igenkänning använder du `recognizer.StopKeywordRecognitionAsync()` i stället för `recognizer.StopContinuousRecognitionAsync()` .

Avsikts igenkänning använder ett identiskt mönster med- [`StartKeywordRecognitionAsync`](/dotnet/api/microsoft.cognitiveservices.speech.intent.intentrecognizer.startkeywordrecognitionasync#Microsoft_CognitiveServices_Speech_Intent_IntentRecognizer_StartKeywordRecognitionAsync_Microsoft_CognitiveServices_Speech_KeywordRecognitionModel_) och- [`StopKeywordRecognitionAsync`](/dotnet/api/microsoft.cognitiveservices.speech.intent.intentrecognizer.stopkeywordrecognitionasync#Microsoft_CognitiveServices_Speech_Intent_IntentRecognizer_StopKeywordRecognitionAsync) funktionerna.
