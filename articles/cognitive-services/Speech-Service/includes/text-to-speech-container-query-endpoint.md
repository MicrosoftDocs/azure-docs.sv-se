---
title: Slut punkt för frågans text till tal-behållare
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/31/2020
ms.author: aahi
ms.openlocfilehash: 9a29244745b154aa81997813fcf4e1457f599270
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103622063"
---
Behållaren tillhandahåller [rest-baserade slut punkts-API: er](../rest-text-to-speech.md). Det finns många [exempel på käll kods projekt](https://github.com/Azure-Samples/Cognitive-Speech-TTS) för plattforms-, ramverk-och språkvarianter som är tillgängliga.

Med standard-eller neurala text till tal-behållare bör du förlita dig på språket och rösten för den bildtagg som du laddade ned. Om du till exempel `latest` har hämtat taggen är standard språket `en-US` och `AriaNeural` rösten. `{VOICE_NAME}`Argumentet skulle då vara [`en-US-AriaNeural`](../language-support.md#neural-voices) . Se exempel SSML nedan:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="en-US-AriaNeural">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

För *anpassad text till tal* måste du dock Hämta **rösten/modellen** från den [anpassade röst portalen](https://aka.ms/custom-voice-portal). Namnet på den anpassade modellen är synonymt med röst namnet. Gå till sidan **utbildning** och kopiera den **röst/modell** som ska användas som `{VOICE_NAME}` argument.
<br><br>
:::image type="content" source="../media/custom-voice/custom-voice-model-voice-name.png" alt-text="Anpassad röst modell – röst namn":::

Se exempel SSML nedan:

```xml
<speak version="1.0" xml:lang="en-US">
    <voice name="custom-voice-model">
        This text will get converted into synthesized speech.
    </voice>
</speak>
```

Nu ska vi skapa en HTTP POST-begäran, vilket ger några rubriker och en data nytto Last. Ersätt `{VOICE_NAME}` plats hållaren med ditt eget värde.

```curl
curl -s -v -X POST http://localhost:5000/speech/synthesize/cognitiveservices/v1 \
 -H 'Accept: audio/*' \
 -H 'Content-Type: application/ssml+xml' \
 -H 'X-Microsoft-OutputFormat: riff-24khz-16bit-mono-pcm' \
 -d '<speak version="1.0" xml:lang="en-US"><voice name="{VOICE_NAME}">This is a test, only a test.</voice></speak>' > output.wav
```


Det här kommandot:

* Skapar en HTTP POST-begäran för `speech/synthesize/cognitiveservices/v1` slut punkten.
* Anger ett `Accept` sidhuvud för `audio/*`
* Anger ett `Content-Type` huvud för `application/ssml+xml` Mer information finns i brödtext för [begäran](../rest-text-to-speech.md#request-body).
* Anger ett `X-Microsoft-OutputFormat` sidhuvud för `riff-16khz-16bit-mono-pcm` fler alternativ se [ljud uppspelning](../rest-text-to-speech.md#audio-outputs).
* Skickar [SSML-begäran (Speech syntes Markup Language)](../speech-synthesis-markup.md) `{VOICE_NAME}` till slut punkten.
