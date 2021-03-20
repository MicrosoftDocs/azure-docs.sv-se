---
title: Ange käll språk för tal till text
titleSuffix: Azure Cognitive Services
description: Med tal-SDK kan du ange käll språket när du konverterar tal till text. Den här artikeln beskriver hur du använder metoderna FromConfig och SourceLanguageConfig för att låta röst tjänsten känna till käll språket och tillhandahålla ett anpassat modell mål.
services: cognitive-services
author: susanhu
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: qiohu
zone_pivot_groups: programming-languages-set-two
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 1b134fd3d09eeda340e7323638a36b68336242c2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91362035"
---
# <a name="specify-source-language-for-speech-to-text"></a>Ange käll språk för tal till text

I den här artikeln får du lära dig hur du anger käll språket för ljud indata som skickas till tal-SDK för tal igenkänning. Dessutom tillhandahålls exempel kod för att ange en anpassad tal modell för bättre igenkänning.

::: zone pivot="programming-language-csharp"

## <a name="how-to-specify-source-language-in-c"></a>Ange käll språk i C #

I följande exempel anges käll språket explicit som en parameter med hjälp av `SpeechRecognizer` konstruktionen.

```csharp
var recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

I följande exempel anges käll språket med hjälp av `SourceLanguageConfig` . Sedan skickas den `sourceLanguageConfig` som en parameter för att `SpeechRecognizer` konstruera.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

I följande exempel anges käll språket och den anpassade slut punkten med hjälp av `SourceLanguageConfig` . Sedan skickas den `sourceLanguageConfig` som en parameter för att `SpeechRecognizer` konstruera.

```csharp
var sourceLanguageConfig = SourceLanguageConfig.FromLanguage("de-DE", "The Endpoint ID for your custom model.");
var recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SpeechRecognitionLanguage` och `EndpointId` set-metoder är föråldrade från `SpeechConfig` klassen i C#. Användningen av dessa metoder rekommenderas inte och bör inte användas när du skapar en `SpeechRecognizer` .

::: zone-end

::: zone pivot="programming-language-cpp"


## <a name="how-to-specify-source-language-in-c"></a>Ange käll språk i C++

I följande exempel anges käll språket explicit som en parameter med hjälp av `FromConfig` metoden.

```C++
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, "de-DE", audioConfig);
```

I följande exempel anges käll språket med hjälp av `SourceLanguageConfig` . Sedan skickas den `sourceLanguageConfig` som en parameter till `FromConfig` när du skapar `recognizer` .

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

I följande exempel anges käll språket och den anpassade slut punkten med hjälp av `SourceLanguageConfig` . `sourceLanguageConfig`Skickas som en parameter till `FromConfig` när du skapar `recognizer` .

```C++
auto sourceLanguageConfig = SourceLanguageConfig::FromLanguage("de-DE", "The Endpoint ID for your custom model.");
auto recognizer = SpeechRecognizer::FromConfig(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `SetSpeechRecognitionLanguage` och `SetEndpointId` är föråldrade metoder från- `SpeechConfig` klassen i C++ och Java. Användningen av dessa metoder rekommenderas inte och bör inte användas när du skapar en `SpeechRecognizer` .

::: zone-end

::: zone pivot="programming-language-java"

## <a name="how-to-specify-source-language-in-java"></a>Ange käll språk i Java

I följande exempel anges käll språket explicit när du skapar en ny `SpeechRecognizer` .

```Java
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, "de-DE", audioConfig);
```

I följande exempel anges käll språket med hjälp av `SourceLanguageConfig` . Sedan skickas den `sourceLanguageConfig` som en parameter när du skapar en ny `SpeechRecognizer` .

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

I följande exempel anges käll språket och den anpassade slut punkten med hjälp av `SourceLanguageConfig` . Sedan skickas den `sourceLanguageConfig` som en parameter när du skapar en ny `SpeechRecognizer` .

```Java
SourceLanguageConfig sourceLanguageConfig = SourceLanguageConfig.fromLanguage("de-DE", "The Endpoint ID for your custom model.");
SpeechRecognizer recognizer = new SpeechRecognizer(speechConfig, sourceLanguageConfig, audioConfig);
```

>[!Note]
> `setSpeechRecognitionLanguage` och `setEndpointId` är föråldrade metoder från- `SpeechConfig` klassen i C++ och Java. Användningen av dessa metoder rekommenderas inte och bör inte användas när du skapar en `SpeechRecognizer` .

::: zone-end

::: zone pivot="programming-language-python"

## <a name="how-to-specify-source-language-in-python"></a>Ange käll språk i python

I följande exempel anges käll språket explicit som en parameter med hjälp av `SpeechRecognizer` konstruktionen.

```Python
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, language="de-DE", audio_config=audio_config)
```

I följande exempel anges käll språket med hjälp av `SourceLanguageConfig` . Sedan skickas den `SourceLanguageConfig` som en parameter för att `SpeechRecognizer` konstruera.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

I följande exempel anges käll språket och den anpassade slut punkten med hjälp av `SourceLanguageConfig` . Sedan skickas den `SourceLanguageConfig` som en parameter för att `SpeechRecognizer` konstruera.

```Python
source_language_config = speechsdk.languageconfig.SourceLanguageConfig("de-DE", "The Endpoint ID for your custom model.")
speech_recognizer = speechsdk.SpeechRecognizer(
        speech_config=speech_config, source_language_config=source_language_config, audio_config=audio_config)
```

>[!Note]
> `speech_recognition_language` och `endpoint_id` egenskaper är inaktuella från `SpeechConfig` klassen i python. Användningen av dessa egenskaper rekommenderas inte, och de bör inte användas för att skapa en `SpeechRecognizer` .

::: zone-end

::: zone pivot="programming-language-more"

## <a name="how-to-specify-source-language-in-javascript"></a>Ange käll språk i Java Script

Det första steget är att skapa en `SpeechConfig` :

```Javascript
var speechConfig = sdk.SpeechConfig.fromSubscription("YourSubscriptionkey", "YourRegion");
```

Ange sedan käll språket för ditt ljud med `speechRecognitionLanguage` :

```Javascript
speechConfig.speechRecognitionLanguage = "de-DE";
```

Om du använder en anpassad modell för igenkänning kan du ange slut punkten med `endpointId` :

```Javascript
speechConfig.endpointId = "The Endpoint ID for your custom model.";
```

## <a name="how-to-specify-source-language-in-objective-c"></a>Ange käll språk i mål-C

I följande exempel anges käll språket explicit som en parameter med hjälp av `SPXSpeechRecognizer` konstruktionen.

```Objective-C
SPXSpeechRecognizer* speechRecognizer = \
    [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig language:@"de-DE" audioConfiguration:audioConfig];
```

I följande exempel anges käll språket med hjälp av `SPXSourceLanguageConfiguration` . Sedan skickas den `SPXSourceLanguageConfiguration` som en parameter för att `SPXSpeechRecognizer` konstruera.

```Objective-C
SPXSourceLanguageConfiguration* sourceLanguageConfig = [[SPXSourceLanguageConfiguration alloc]init:@"de-DE"];
SPXSpeechRecognizer* speechRecognizer = [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                                                     sourceLanguageConfiguration:sourceLanguageConfig
                                                                              audioConfiguration:audioConfig];
```

I följande exempel anges käll språket och den anpassade slut punkten med hjälp av `SPXSourceLanguageConfiguration` . Sedan skickas den `SPXSourceLanguageConfiguration` som en parameter för att `SPXSpeechRecognizer` konstruera.

```Objective-C
SPXSourceLanguageConfiguration* sourceLanguageConfig = \
        [[SPXSourceLanguageConfiguration alloc]initWithLanguage:@"de-DE"
                                                     endpointId:@"The Endpoint ID for your custom model."];
SPXSpeechRecognizer* speechRecognizer = [[SPXSpeechRecognizer alloc] initWithSpeechConfiguration:speechConfig
                                                                     sourceLanguageConfiguration:sourceLanguageConfig
                                                                              audioConfiguration:audioConfig];
```

>[!Note]
> `speechRecognitionLanguage` och `endpointId` egenskaper är inaktuella från `SPXSpeechConfiguration` klassen i mål-C. Användningen av dessa egenskaper rekommenderas inte, och de bör inte användas för att skapa en `SPXSpeechRecognizer` .

::: zone-end

## <a name="see-also"></a>Se även

* En lista över språk som stöds och nationella inställningar för tal till text finns i [språk stöd](language-support.md).

## <a name="next-steps"></a>Nästa steg

* [Dokumentation om tal SDK-referens](speech-sdk.md)