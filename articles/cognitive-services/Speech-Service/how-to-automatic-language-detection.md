---
title: Så här använder du automatisk språk identifiering för tal till text
titleSuffix: Azure Cognitive Services
description: 'Tal-SDK: n stöder automatisk språk identifiering för tal till text. När du använder den här funktionen jämförs ljudet med den angivna listan med språk och den mest sannolika matchningen fastställs. Det returnerade värdet kan sedan användas för att välja den språk modell som används för tal till text.'
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: trbye
zone_pivot_groups: programming-languages-speech-services-nomore-variant
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: e62333f241cf24620773314fb7aa4846af651eaf
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98185877"
---
# <a name="automatic-language-detection-for-speech-to-text"></a>Automatisk språk identifiering för tal till text

Automatisk språk identifiering används för att avgöra den mest sannolika matchningen för ljud som skickas till tal-SDK: n jämfört med en lista med angivna språk. Värdet som returneras av automatisk språk identifiering används sedan för att välja språk modell för tal till text, vilket ger en mer exakt avskrift. Information om vilka språk som är tillgängliga finns i [språk stöd](language-support.md).

I den här artikeln får du lära dig hur du använder `AutoDetectSourceLanguageConfig` för att skapa ett `SpeechRecognizer` objekt och hämta det identifierade språket.

> [!IMPORTANT]
> Den här funktionen är endast tillgänglig för tal-SDK med C#, C++, Java, python, Java Script och mål-C.

## <a name="automatic-language-detection-with-the-speech-sdk"></a>Automatisk språk identifiering med talet SDK

Den automatiska språk identifieringen har för närvarande en gräns på fyra språk per identifiering för tjänster. Behåll den här begränsningen i åtanke när du har byggt ditt `AudoDetectSourceLanguageConfig` objekt. I exemplen nedan skapar du en `AutoDetectSourceLanguageConfig` och använder den sedan för att skapa en `SpeechRecognizer` .

> [!TIP]
> Du kan också ange en anpassad modell som ska användas när du utför tal till text. Mer information finns i [använda en anpassad modell för automatisk språk identifiering](#use-a-custom-model-for-automatic-language-detection).

Följande kodfragment visar hur du använder automatisk språk identifiering i dina appar:

::: zone pivot="programming-language-csharp"

```csharp
var autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.FromLanguages(
        new string[] { "en-US", "de-DE" });

using (var recognizer = new SpeechRecognizer(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig))
{
    var speechRecognitionResult = await recognizer.RecognizeOnceAsync();
    var autoDetectSourceLanguageResult =
        AutoDetectSourceLanguageResult.FromResult(speechRecognitionResult);
    var detectedLanguage = autoDetectSourceLanguageResult.Language;
}
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig::FromLanguages({ "en-US", "de-DE" });

auto recognizer = SpeechRecognizer::FromConfig(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig
    );

speechRecognitionResult = recognizer->RecognizeOnceAsync().get();
auto autoDetectSourceLanguageResult =
    AutoDetectSourceLanguageResult::FromResult(speechRecognitionResult);
auto detectedLanguage = autoDetectSourceLanguageResult->Language;
```

::: zone-end

::: zone pivot="programming-language-java"

```java
AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.fromLanguages(Arrays.asList("en-US", "de-DE"));

SpeechRecognizer recognizer = new SpeechRecognizer(
    speechConfig,
    autoDetectSourceLanguageConfig,
    audioConfig);

Future<SpeechRecognitionResult> future = recognizer.recognizeOnceAsync();
SpeechRecognitionResult result = future.get(30, TimeUnit.SECONDS);
AutoDetectSourceLanguageResult autoDetectSourceLanguageResult =
    AutoDetectSourceLanguageResult.fromResult(result);
String detectedLanguage = autoDetectSourceLanguageResult.getLanguage();

recognizer.close();
speechConfig.close();
autoDetectSourceLanguageConfig.close();
audioConfig.close();
result.close();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
auto_detect_source_language_config = \
        speechsdk.languageconfig.AutoDetectSourceLanguageConfig(languages=["en-US", "de-DE"])
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, 
        auto_detect_source_language_config=auto_detect_source_language_config, 
        audio_config=audio_config)
result = speech_recognizer.recognize_once()
auto_detect_source_language_result = speechsdk.AutoDetectSourceLanguageResult(result)
detected_language = auto_detect_source_language_result.language
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
NSArray *languages = @[@"zh-CN", @"de-DE"];
SPXAutoDetectSourceLanguageConfiguration* autoDetectSourceLanguageConfig = \
        [[SPXAutoDetectSourceLanguageConfiguration alloc]init:languages];
SPXSpeechRecognizer* speechRecognizer = \
        [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                           autoDetectSourceLanguageConfiguration:autoDetectSourceLanguageConfig
                                              audioConfiguration:audioConfig];
SPXSpeechRecognitionResult *result = [speechRecognizer recognizeOnce];
SPXAutoDetectSourceLanguageResult *languageDetectionResult = [[SPXAutoDetectSourceLanguageResult alloc] init:result];
NSString *detectedLanguage = [languageDetectionResult language];
```

::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var autoDetectConfig = SpeechSDK.AutoDetectSourceLanguageConfig.fromLanguages(["en-US", "de-DE"]);
var speechRecognizer = SpeechSDK.SpeechRecognizer.FromConfig(speechConfig, autoDetectConfig, audioConfig);
speechRecognizer.recognizeOnceAsync((result: SpeechSDK.SpeechRecognitionResult) => {
        var languageDetectionResult = SpeechSDK.AutoDetectSourceLanguageResult.fromResult(result);
        var detectedLanguage = languageDetectionResult.language;
},
{});
```

::: zone-end

## <a name="use-a-custom-model-for-automatic-language-detection"></a>Använd en anpassad modell för automatisk språk identifiering

Förutom språk identifiering med tal tjänst modeller kan du ange en anpassad modell för bättre igenkänning. Om ingen anpassad modell har angetts använder tjänsten standard språk modellen.

Kodfragmenten nedan visar hur du anger en anpassad modell i ditt anrop till röst tjänsten. Om det identifierade språket är `en-US` , används standard modellen. Om det identifierade språket är `fr-FR` , används slut punkten för den anpassade modellen:

::: zone pivot="programming-language-csharp"

```csharp
var sourceLanguageConfigs = new SourceLanguageConfig[]
{
    SourceLanguageConfig.FromLanguage("en-US"),
    SourceLanguageConfig.FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR")
};
var autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.FromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
std::vector<std::shared_ptr<SourceLanguageConfig>> sourceLanguageConfigs;
sourceLanguageConfigs.push_back(
    SourceLanguageConfig::FromLanguage("en-US"));
sourceLanguageConfigs.push_back(
    SourceLanguageConfig::FromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));

auto autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig::FromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-java"

```java
List sourceLanguageConfigs = new ArrayList<SourceLanguageConfig>();
sourceLanguageConfigs.add(
    SourceLanguageConfig.fromLanguage("en-US"));
sourceLanguageConfigs.add(
    SourceLanguageConfig.fromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR"));

AutoDetectSourceLanguageConfig autoDetectSourceLanguageConfig =
    AutoDetectSourceLanguageConfig.fromSourceLanguageConfigs(
        sourceLanguageConfigs);
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
 en_language_config = speechsdk.languageconfig.SourceLanguageConfig("en-US")
 fr_language_config = speechsdk.languageconfig.SourceLanguageConfig("fr-FR", "The Endpoint Id for custom model of fr-FR")
 auto_detect_source_language_config = speechsdk.languageconfig.AutoDetectSourceLanguageConfig(
        sourceLanguageConfigs=[en_language_config, fr_language_config])
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXSourceLanguageConfiguration* enLanguageConfig = [[SPXSourceLanguageConfiguration alloc]init:@"en-US"];
SPXSourceLanguageConfiguration* frLanguageConfig = \
        [[SPXSourceLanguageConfiguration alloc]initWithLanguage:@"fr-FR"
                                                     endpointId:@"The Endpoint Id for custom model of fr-FR"];
NSArray *languageConfigs = @[enLanguageConfig, frLanguageConfig];
SPXAutoDetectSourceLanguageConfiguration* autoDetectSourceLanguageConfig = \
        [[SPXAutoDetectSourceLanguageConfiguration alloc]initWithSourceLanguageConfigurations:languageConfigs];
```

::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var enLanguageConfig = SpeechSDK.SourceLanguageConfig.fromLanguage("en-US");
var frLanguageConfig = SpeechSDK.SourceLanguageConfig.fromLanguage("fr-FR", "The Endpoint Id for custom model of fr-FR");
var autoDetectConfig = SpeechSDK.AutoDetectSourceLanguageConfig.fromSourceLanguageConfigs([enLanguageConfig, frLanguageConfig]);
```

::: zone-end

## <a name="next-steps"></a>Nästa steg

::: zone pivot="programming-language-csharp"
* Se [exempel koden](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/csharp/sharedcontent/console/speech_recognition_samples.cs#L741) på GitHub för automatisk språk identifiering
::: zone-end

::: zone pivot="programming-language-cpp"
* Se [exempel koden](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/cpp/windows/console/samples/speech_recognition_samples.cpp#L507) på GitHub för automatisk språk identifiering
::: zone-end

::: zone pivot="programming-language-java"
* Se [exempel koden](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/java/jre/console/src/com/microsoft/cognitiveservices/speech/samples/console/SpeechRecognitionSamples.java#L521) på GitHub för automatisk språk identifiering
::: zone-end

::: zone pivot="programming-language-python"
* Se [exempel koden](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/python/console/speech_sample.py#L458) på GitHub för automatisk språk identifiering
::: zone-end

::: zone pivot="programming-language-objectivec"
* Se [exempel koden](https://github.com/Azure-Samples/cognitive-services-speech-sdk/blob/master/samples/objective-c/ios/speech-samples/speech-samples/ViewController.m#L525) på GitHub för automatisk språk identifiering
::: zone-end

* [Dokumentation om tal SDK-referens](speech-sdk.md)
