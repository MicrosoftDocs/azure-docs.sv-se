---
title: Använda tal-SDK för uttal av uttal
titleSuffix: Azure Cognitive Services
description: 'Tal-SDK: n stöder uttal av uttal, som utvärderar uttal av tal ingångar, med indikatorer på precision, Fluency, fullständighet osv.'
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: yulili
ms.custom: references_regions
zone_pivot_groups: programming-languages-speech-services-nomore-variant
ms.openlocfilehash: 2d1b5e490b7c8212e6103e3d169c1b5491d01dde
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2021
ms.locfileid: "106167438"
---
# <a name="pronunciation-assessment"></a>Uttal av uttal

Uttal-utvärderingen utvärderar tal uttal och ger talare feedback om precisionen och Fluency av talade ljud.
Med uttals-utvärdering kan språklärare öva, få snabb feedback och förbättra sina uttal så att de kan tala och presentera med förtroende.
Lärare kan använda funktionen för att utvärdera uttal av flera högtalare i real tid.

I den här artikeln lär du dig att konfigurera `PronunciationAssessmentConfig` och hämta `PronunciationAssessmentResult` med hjälp av talet SDK.

> [!NOTE]
> Funktionen för ututtals bedömning stöder för närvarande `en-US` språk, som är tillgänglig i alla [tal-till-text-regioner](regions.md#speech-to-text-text-to-speech-and-translation). Stöd för `en-GB` och `zh-CN` språk är under för hands version, som är tillgängligt `westus` på `eastasia` och `centralindia` regioner.

## <a name="pronunciation-assessment-with-the-speech-sdk"></a>Uttal av tal-SDK

I exemplen nedan skapar du en `PronunciationAssessmentConfig` och tillämpar den på en `SpeechRecognizer` .

Följande kodfragment visar hur du använder automatisk språk identifiering i dina appar:

::: zone pivot="programming-language-csharp"

```csharp
var pronunciationAssessmentConfig = new PronunciationAssessmentConfig(
    "reference text", GradingSystem.HundredMark, Granularity.Phoneme);

using (var recognizer = new SpeechRecognizer(
    speechConfig,
    audioConfig))
{
    // apply the pronunciation assessment configuration to the speech recognizer
    pronunciationAssessmentConfig.ApplyTo(recognizer);
    var speechRecognitionResult = await recognizer.RecognizeOnceAsync();
    var pronunciationAssessmentResult =
        PronunciationAssessmentResult.FromResult(speechRecognitionResult);
    var pronunciationScore = pronunciationAssessmentResult.PronunciationScore;
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto pronunciationAssessmentConfig =
    PronunciationAssessmentConfig::Create("reference text",
        PronunciationAssessmentGradingSystem::HundredMark,
        PronunciationAssessmentGranularity::Phoneme);

auto recognizer = SpeechRecognizer::FromConfig(
    speechConfig,
    audioConfig);

// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig->ApplyTo(recognizer);
speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto pronunciationAssessmentResult =
    PronunciationAssessmentResult::FromResult(speechRecognitionResult);
auto pronunciationScore = pronunciationAssessmentResult->PronunciationScore;
```

::: zone-end

::: zone pivot="programming-language-java"

```java
PronunciationAssessmentConfig pronunciationAssessmentConfig =
    new PronunciationAssessmentConfig("reference text", 
        PronunciationAssessmentGradingSystem.HundredMark,
        PronunciationAssessmentGranularity.Phoneme);

SpeechRecognizer recognizer = new SpeechRecognizer(
    speechConfig,
    audioConfig);

// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig.applyTo(recognizer);
Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
PronunciationAssessmentResult pronunciationAssessmentResult =
    PronunciationAssessmentResult.fromResult(result);
Double pronunciationScore = pronunciationAssessmentResult.getPronunciationScore();

recognizer.close();
speechConfig.close();
audioConfig.close();
pronunciationAssessmentConfig.close();
result.close();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
pronunciation_assessment_config = \
        speechsdk.PronunciationAssessmentConfig(reference_text='reference text',
                grading_system=msspeech.PronunciationAssessmentGradingSystem.HundredMark,
                granularity=msspeech.PronunciationAssessmentGranularity.Phoneme)
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, \
        audio_config=audio_config)

# apply the pronunciation assessment configuration to the speech recognizer
pronunciation_assessment_config.apply_to(speech_recognizer)
result = speech_recognizer.recognize_once()
pronunciation_assessment_result = speechsdk.PronunciationAssessmentResult(result)
pronunciation_score = pronunciation_assessment_result.pronunciation_score
```

::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var pronunciationAssessmentConfig = new SpeechSDK.PronunciationAssessmentConfig("reference text",
    PronunciationAssessmentGradingSystem.HundredMark,
    PronunciationAssessmentGranularity.Word, true);
var speechRecognizer = SpeechSDK.SpeechRecognizer.FromConfig(speechConfig, audioConfig);
// apply the pronunciation assessment configuration to the speech recognizer
pronunciationAssessmentConfig.applyTo(speechRecognizer);

speechRecognizer.recognizeOnceAsync((result: SpeechSDK.SpeechRecognitionResult) => {
        var pronunciationAssessmentResult = SpeechSDK.PronunciationAssessmentResult.fromResult(result);
        var pronunciationScore = pronunciationAssessmentResult.pronunciationScore;
        var wordLevelResult = pronunciationAssessmentResult.detailResult.Words;
},
{});
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXPronunciationAssessmentConfiguration* pronunciationAssessmentConfig =
    [[SPXPronunciationAssessmentConfiguration alloc]init:@"reference text"
                                           gradingSystem:SPXPronunciationAssessmentGradingSystem_HundredMark
                                             granularity:SPXPronunciationAssessmentGranularity_Phoneme];

SPXSpeechRecognizer* speechRecognizer = \
        [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                              audioConfiguration:audioConfig];

// apply the pronunciation assessment configuration to the speech recognizer
[pronunciationAssessmentConfig applyToRecognizer:speechRecognizer];

SPXSpeechRecognitionResult *result = [speechRecognizer recognizeOnce];
SPXPronunciationAssessmentResult* pronunciationAssessmentResult = [[SPXPronunciationAssessmentResult alloc] init:result];
double pronunciationScore = pronunciationAssessmentResult.pronunciationScore;
```

::: zone-end

### <a name="pronunciation-assessment-configuration-parameters"></a>Konfigurations parametrar för uttal-bedömning

Den här tabellen innehåller konfigurations parametrar för uttal-utvärdering.

| Parameter | Beskrivning | Obligatoriskt? |
|-----------|-------------|---------------------|
| ReferenceText | Texten som uttalet kommer att utvärderas mot. | Obligatorisk |
| GradingSystem | Punkt systemet för resultat kalibrering. `FivePoint`Systemet ger ett flytt ALS värde på 0-5 och `HundredMark` ger en 0-100 flytt ALS poäng. Standard: `FivePoint`. | Valfritt |
| Precision | Utvärderings precisionen. Godkända värden är `Phoneme` , som visar poängen på den fullständiga text-, Word-och fonem-nivån, `Word` som visar poängen på den fullständiga text-och ord nivån, `FullText` som bara visar poängen på hela text nivån. Standard: `Phoneme`. | Valfritt |
| EnableMiscue | Aktiverar miscue-beräkning. Med det här alternativet kommer uttalade ord att jämföras med referens texten och markeras med utelämnanden/infogning baserat på jämförelsen. Godkända värden är `False` och `True` . Standard: `False`. | Valfritt |
| ScenarioId | Ett GUID som anger ett anpassat punkt system. | Valfritt |

### <a name="pronunciation-assessment-result-parameters"></a>Resultat parametrar för uttal-bedömning

I den här tabellen visas resultat parametrar för uttal-utvärdering.

| Parameter | Beskrivning |
|-----------|-------------|
| `AccuracyScore` | Talts uttal. Noggrannhet anger hur nära fonem som matchar en ursprunglig talares uttal. Precisions poängen för ord och full text nivå sammanställs från precisions poängen på fonem nivå. |
| `FluencyScore` | Fluency för det tal som angetts. Fluency anger hur tätt talet ska matcha en inbyggd högtalares användning av tyst raster mellan ord. |
| `CompletenessScore` | Talet är klart och bestäms genom att beräkna förhållandet mellan uttalade ord och referenser till text ingångar. |
| `PronunciationScore` | Totalt antal poäng som anger uttal av det tal som anges. Detta sammanställs från `AccuracyScore` `FluencyScore` och `CompletenessScore` med vikt. |
| `ErrorType` | Det här värdet anger om ett ord utelämnas, infogas eller blir dåligt uttalad jämfört med `ReferenceText` . Möjliga värden är `None` (vilket innebär inget fel på det här ordet), `Omission` `Insertion` och `Mispronunciation` . |

### <a name="sample-responses"></a>Exempel svar

En typisk uttals utvärdering resulterar i JSON:

```json
{
  "RecognitionStatus": "Success",
  "Offset": "400000",
  "Duration": "11000000",
  "NBest": [
      {
        "Confidence" : "0.87",
        "Lexical" : "good morning",
        "ITN" : "good morning",
        "MaskedITN" : "good morning",
        "Display" : "Good morning.",
        "PronunciationAssessment":
        {
            "PronScore" : 84.4,
            "AccuracyScore" : 100.0,
            "FluencyScore" : 74.0,
            "CompletenessScore" : 100.0,
        },
        "Words": [
            {
              "Word" : "Good",
              "Offset" : 500000,
              "Duration" : 2700000,
              "PronunciationAssessment":
              {
                "AccuracyScore" : 100.0,
                "ErrorType" : "None"
              }
            },
            {
              "Word" : "morning",
              "Offset" : 5300000,
              "Duration" : 900000,
              "PronunciationAssessment":
              {
                "AccuracyScore" : 100.0,
                "ErrorType" : "None"
              }
            }
        ]
      }
  ]
}
```

## <a name="next-steps"></a>Nästa steg

<!-- TODO: update JavaScript sample links after release -->

* Titta på [video introduktionen](https://www.youtube.com/watch?v=cBE8CUHOFHQ) och [video guiden](https://www.youtube.com/watch?v=zFlwm7N4Awc) om uttal av uttal

* Prova demonstration av [uttals bedömning](https://github.com/Azure-Samples/Cognitive-Speech-TTS/tree/master/PronunciationAssessment/BrowserJS)

::: zone pivot="programming-language-csharp"
* Se [exempel koden](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#L949) på GitHub for uttal-utvärdering.
::: zone-end

::: zone pivot="programming-language-cpp"
* Se [exempel koden](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#L633) på GitHub for uttal-utvärdering.
::: zone-end

::: zone pivot="programming-language-java"
* Se [exempel koden](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechRecognitionSamples.java#L697) på GitHub for uttal-utvärdering.
::: zone-end

::: zone pivot="programming-language-python"
* Se [exempel koden](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py#L576) på GitHub for uttal-utvärdering.
::: zone-end

::: zone pivot="programming-language-objectivec"
* Se [exempel koden](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/objective-c/ios/speech-samples/speech-samples/ViewController.m#L642) på GitHub for uttal-utvärdering.
::: zone-end

* [Dokumentation om tal SDK-referens](speech-sdk.md)

* [Skapa ett kostnadsfritt Azure-konto](https://azure.microsoft.com/free/cognitive-services/)
