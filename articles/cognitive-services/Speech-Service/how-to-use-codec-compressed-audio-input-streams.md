---
title: Strömma codec-komprimerat ljud med Speech SDK – Speech-tjänsten
titleSuffix: Azure Cognitive Services
description: Lär dig hur du strömmar komprimerat ljud till Speech-tjänsten med Speech SDK. Tillgänglig för C++, C# och Java för Linux, Java i Android och Objective-C i iOS.
services: cognitive-services
author: amitkumarshukla
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: amishu
ms.custom: devx-track-csharp
zone_pivot_groups: programming-languages-set-twenty-two
ms.openlocfilehash: f02a9a3b493ed0f3068e6e0ccd2daa40850a4fb6
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726186"
---
# <a name="use-codec-compressed-audio-input-with-the-speech-sdk"></a>Använda codec-komprimerade ljudindata med Speech SDK

Speech Service SDK är ett sätt att skicka komprimerade ljudformat direkt till Speech-tjänsten med hjälp av antingen eller (ingen av metodströmmarna direkt till `PullStream` backend-datorn, en rå PCM skickas fortfarande till `PushStream` tjänsten).

Plattform | Språk | GStreamer-version som stöds
| :--- | ---: | :---:
Windows (exklusive UWP)  | C++, C#, Java, Python | [1.18.3](https://gstreamer.freedesktop.org/data/pkg/windows/1.18.3/)
Linux  | C++, C#, Java, Python | [Linux-distributioner och målarkitekturer som stöds](~/articles/cognitive-services/speech-service/speech-sdk.md)
Android  | Java | [1.18.3](https://gstreamer.freedesktop.org/data/pkg/android/1.18.3/)

## <a name="speech-sdk-version-required-for-compressed-audio-input"></a>Speech SDK-version krävs för komprimerade ljudindata
* Speech SDK version 1.10.0 eller senare krävs för RHEL 8 och CentOS 8
* Speech SDK version 1.11.0 eller senare krävs för Windows.
* Speech SDK version 1.16.0 eller senare för senaste gstreamer på Windows och Android.

[!INCLUDE [supported-audio-formats](includes/supported-audio-formats.md)]

## <a name="gstreamer-required-to-handle-compressed-audio"></a>GStreamer krävs för att hantera komprimerat ljud

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/prerequisites.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/prerequisites.md)]
::: zone-end

## <a name="example-code-using-codec-compressed-audio-input"></a>Exempelkod med codec-komprimerade ljudindata

::: zone pivot="programming-language-csharp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/csharp/examples.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/cpp/examples.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/java/examples.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [prerequisites](includes/how-to/compressed-audio-input/python/examples.md)]
::: zone-end

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig att känna igen tal](./get-started-speech-to-text.md)