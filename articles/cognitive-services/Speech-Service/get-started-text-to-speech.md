---
title: Text till tal-snabb start – tal tjänst
titleSuffix: Azure Cognitive Services
description: Lär dig hur du använder tal-SDK för att konvertera text till tal. I den här snabb starten får du lära dig om objekt konstruktions-och design mönster, stöd för ljud uppspelning, tal-CLI och anpassade konfigurations alternativ för tal syntes.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: quickstart
ms.date: 10/01/2020
ms.author: trbye
ms.custom: devx-track-python, devx-track-js, devx-track-csharp, cog-serv-seo-aug-2020
zone_pivot_groups: programming-languages-set-twenty-four
keywords: text till tal
ms.openlocfilehash: 7a41c4d9c1074b376da3de556caf63ced0bc84ec
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102428240"
---
# <a name="get-started-with-text-to-speech"></a>Komma igång med text till tal

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-csharp.md)]
::: zone-end

::: zone pivot="programming-language-cpp"
[!INCLUDE [C++ Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cpp.md)]
::: zone-end

::: zone pivot="programming-language-java"
[!INCLUDE [Java Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-java.md)]
::: zone-end

::: zone pivot="programming-language-javascript"
[!INCLUDE [JavaScript Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-javascript.md)]
::: zone-end

::: zone pivot="programming-languages-objectivec-swift"
[!INCLUDE [Objective-C and Swift Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-objectivec-swift.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-python.md)]
::: zone-end

::: zone pivot="programming-language-curl"
[!INCLUDE [REST include](includes/how-to/text-to-speech-basics/text-to-speech-basics-curl.md)]
::: zone-end

::: zone pivot="programmer-tool-spx"
[!INCLUDE [CLI Basics include](includes/how-to/text-to-speech-basics/text-to-speech-basics-cli.md)]
::: zone-end

## <a name="get-position-information"></a>Hämta positions information

Ditt projekt kan behöva känna till när ett ord läses upp av tal-till-text så att det kan ta en speciell åtgärd baserat på den tiden. Om du till exempel vill markera ord som de var talade måste du veta vad du ska markera, när du ska markera det och hur länge du vill markera det.

Du kan göra detta med hjälp av `WordBoundary` händelsen som är tillgänglig i `SpeechSynthesizer` . Den här händelsen inträffar i början av varje nytt talade ord och ger en tids förskjutning i den talade data strömmen samt en text förskjutning i indata-prompten.

* `AudioOffset` rapporterar hur lång tid det tar att mata ut ljudet mellan början av syntesen och början av nästa ord. Detta mäts i hundra-nanosekunder-enheter (HNS) med 10 000 HNS motsvarande 1 millisekunder.
* `WordOffset` rapporterar tecken positionen i Indatasträngen (ursprunglig text eller [SSML](speech-synthesis-markup.md)) omedelbart före det ord som ska läsas.

> [!NOTE]
> `WordBoundary` händelser aktive ras när ljud data för utdata blir tillgängliga, vilket är snabbare än uppspelning till en utdataenhet. Synkronisering av ström-timing måste utföras av anroparen på lämpligt sätt.

Du hittar exempel på hur du kan använda `WordBoundary` i [text till tal-exempel](https://aka.ms/csspeech/samples) på GitHub.

## <a name="next-steps"></a>Nästa steg

* [Komma igång med Custom Voice](how-to-custom-voice.md)
* [Förbättra syntesen med SSML](speech-synthesis-markup.md)
* Lär dig hur du använder [långa ljud-API: er](long-audio-api.md) för stora text exempel som böcker och nyhets artiklar
* Se [snabb starts exemplen](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/quickstart) på GitHub
