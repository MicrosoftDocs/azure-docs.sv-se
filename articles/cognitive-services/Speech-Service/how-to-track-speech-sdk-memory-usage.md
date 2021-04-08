---
title: Spåra minnes användning i tal SDK – tal tjänst
titleSuffix: Azure Cognitive Services
description: Speech service SDK stöder flera programmeringsspråk för konvertering från tal till text och text till tal, tillsammans med tal översättning. Den här artikeln beskriver minnes hanterings verktyg som är inbyggda i SDK.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/10/2019
ms.author: rhurey
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-two
ms.openlocfilehash: c7e74ce8f4d0b7889d2e5bcd117eaa43e2b6991b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "88934148"
---
# <a name="how-to-track-speech-sdk-memory-usage"></a>Spåra minnes användning för tal-SDK

Tal-SDK: n baseras på en inbyggd kodbas som projiceras i flera programmeringsspråk via en serie Interoperabilitets lager. Varje språkspecifik projektion har idiomatically rätt funktioner för att hantera objekt livs cykeln. Dessutom innehåller tal-SDK minnes hanterings verktyg för att spåra resursanvändning med objekt loggning och objekt begränsningar. 

## <a name="how-to-read-object-logs"></a>Läsa objekt loggar

Om [tal-SDK-loggning är aktive rad](how-to-use-logging.md), genereras spårnings etiketter för att aktivera historisk objekt observation. Taggarna är: 

* `TrackHandle` eller `StopTracking` 
* Objekt typen
* Det aktuella antalet objekt som spårar objekt typen och det aktuella talet som spåras.

Här är en exempel logg: 

```terminal
(284): 8604ms SPX_DBG_TRACE_VERBOSE:  handle_table.h:90 TrackHandle type=Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult handle=0x0x7f688401e1a0, ptr=0x0x7f688401e1a0, total=19
```

## <a name="set-a-warning-threshold"></a>Ange ett varnings tröskelvärde

Du har möjlighet att skapa en varnings tröskel och om tröskelvärdet överskrids (förutsatt att loggning är aktiverat) loggas ett varnings meddelande. Varnings meddelandet innehåller en dumpning av alla objekt som förekommer tillsammans med antalet. Den här informationen kan användas för att bättre förstå problem. 

Om du vill aktivera ett varnings tröskelvärde måste det anges för ett `SpeechConfig` objekt. Det här objektet kontrol leras när en ny tolk skapas. I följande exempel antar vi att du har skapat en instans med `SpeechConfig` namnet `config` :

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountWarnThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("SPEECH-ObjectCountWarnThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```ObjectiveC
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountWarnThreshold"];
```

::: zone-end

> [!TIP]
> Standardvärdet för den här egenskapen är 10 000.

## <a name="set-an-error-threshold"></a>Ange ett fel tröskelvärde 

Med hjälp av tal-SDK kan du ange det maximala antalet objekt som tillåts vid en angiven tidpunkt. Om den här inställningen är aktive rad kommer försök att skapa nya tolknings objekt att Miss lyckas när det maximala antalet träffar. Befintliga objekt kommer att fortsätta att fungera.

Här är ett exempel fel:

```terminal
Runtime error: The maximum object count of 500 has been exceeded.
The threshold can be adjusted by setting the SPEECH-ObjectCountErrorThreshold property on the SpeechConfig object.
See http://https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-object-tracking-speech-sdk for more detailed information.
Handle table dump by ojbect type:
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognitionResult 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxRecognizer 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxAudioConfig 0
class Microsoft::CognitiveServices::Speech::Impl::ISpxSpeechConfig 0
```

Om du vill aktivera ett fel tröskelvärde måste det anges för ett `SpeechConfig` objekt. Det här objektet kontrol leras när en ny tolk skapas. I följande exempel antar vi att du har skapat en instans med `SpeechConfig` namnet `config` :

::: zone pivot="programming-language-csharp"

```csharp
config.SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-cpp"

```C++
config->SetProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-java"

```java
config.setProperty("SPEECH-ObjectCountErrorThreshold", "10000");
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_config.set_property_by_name("SPEECH-ObjectCountErrorThreshold", "10000")?
```

::: zone-end

::: zone pivot="programming-language-more"

```objc
[config setPropertyTo:@"10000" byName:"SPEECH-ObjectCountErrorThreshold"];
```

::: zone-end

> [!TIP]
> Standardvärdet för den här egenskapen är det plattformsspecifika maximala värdet för en datatyp `size_t` . En typisk igenkänning kommer att förbruka mellan 7 och 10 interna objekt.

## <a name="next-steps"></a>Nästa steg

* [Läs mer om tal-SDK](speech-sdk.md)