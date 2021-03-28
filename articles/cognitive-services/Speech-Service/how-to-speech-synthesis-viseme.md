---
title: Så här hämtar du ansikts attityd händelser för Lip-Sync
titleSuffix: Azure Cognitive Services
description: 'Tal-SDK: n stöder viseme-händelse i tal syntes, som används för att representera viktiga faktorer i observerade tal, till exempel positionen för läppar, Jaw och tunghet vid framställning av en viss fonem.'
services: cognitive-services
author: yulin-li
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: yulili
ms.custom: references_regions
zone_pivot_groups: programming-languages-speech-services-nomore-variant
ms.openlocfilehash: e97c48d4e42627d0fc2caaa4f66e81b9a0cafa86
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105643894"
---
# <a name="get-facial-pose-events"></a>Hämta händelser för ansikts attityd

> [!NOTE]
> Viseme fungerar bara för `en-US-AriaNeural` Voice för tillfället.

En viseme är den visuella beskrivningen av en fonem i talade språk.
Den definierar ytans position och mun när du talar ett ord.
Varje viseme illustrerar nyckelns ansikts attityder för en speciell uppsättning fonem.
Det finns ingen en-till-en-korrespondens mellan visemes och fonem.
Flera fonem motsvarar ofta en enda viseme, eftersom flera fonem ser likadana ut på ytan när de skapas, till exempel `s` och `z` .
Se [mappnings tabellen mellan visemes och fonem](#map-phonemes-to-visemes).

Med visemes kan du skapa mer naturliga och intelligenta nyhets sändningar, mer interaktiva spel och tecknade tecken och mer intuitiva språkundervisnings videor. Personer med hörsel försämring kan också hämta ljud visuellt och "Lip-Read"-tal innehåll som visar visemes på en animerad ansikts.

## <a name="get-viseme-events-with-the-speech-sdk"></a>Hämta viseme-händelser med talet SDK

För att göra viseme-händelser konverterar vi inmatad text till en uppsättning fonem sekvenser och deras motsvarande viseme sekvenser. Vi uppskattar start tiden för varje viseme i tal ljudet. Viseme-händelser innehåller en sekvens med Viseme-ID: n, var och en med en förskjutning i ljudet där Viseme visas. Dessa händelser kan driva mun animeringar som simulerar en person som talar om inmatad text.

| Parameter | Beskrivning |
|-----------|-------------|
| Viseme-ID | Heltals värde som anger ett viseme. På engelska (USA) erbjuder vi 22 olika visemes för att illustrera mun figurerna för en särskild uppsättning fonem. Se [mappnings tabellen mellan Viseme-ID och fonem](#map-phonemes-to-visemes).  |
| Ljud förskjutning | Start tiden för varje viseme i Tick (100 nanosekunder). |

Du kan hämta viseme-händelser genom att prenumerera på `VisemeReceived` händelsen i tal-SDK.
Följande kodfragment visar hur du prenumererar på viseme-händelsen.

::: zone pivot="programming-language-csharp"

```csharp
using (var synthesizer = new SpeechSynthesizer(speechConfig, audioConfig))
{
    // Subscribes to viseme received event
    synthesizer.VisemeReceived += (s, e) =>
    {
        Console.WriteLine($"Viseme event received. Audio offset: " +
            $"{e.AudioOffset / 10000}ms, viseme id: {e.VisemeId}.");
    };

    var result = await synthesizer.SpeakSsmlAsync(ssml));
}

```

::: zone-end

::: zone pivot="programming-language-cpp"

```cpp
auto synthesizer = SpeechSynthesizer::FromConfig(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer->VisemeReceived += [](const SpeechSynthesisVisemeEventArgs& e)
{
    cout << "viseme event received. "
        // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
        << "Audio offset: " << e.AudioOffset / 10000 << "ms, "
        << "viseme id: " << e.VisemeId << "." << endl;
};

auto result = synthesizer->SpeakSsmlAsync(ssml).get();
```

::: zone-end

::: zone pivot="programming-language-java"

```java
SpeechSynthesizer synthesizer = new SpeechSynthesizer(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer.VisemeReceived.addEventListener((o, e) -> {
    // The unit of e.AudioOffset is tick (1 tick = 100 nanoseconds), divide by 10,000 to convert to milliseconds.
    System.out.print("Viseme event received. Audio offset: " + e.getAudioOffset() / 10000 + "ms, ");
    System.out.println("viseme id: " + e.getVisemeId() + ".");
});

SpeechSynthesisResult result = synthesizer.SpeakSsmlAsync(ssml).get();
```

::: zone-end

::: zone pivot="programming-language-python"

```Python
speech_synthesizer = speechsdk.SpeechSynthesizer(speech_config=speech_config, audio_config=audio_config)

# Subscribes to viseme received event
speech_synthesizer.viseme_received.connect(lambda evt: print(
    "Viseme event received: audio offset: {}ms, viseme id: {}.".format(evt.audio_offset / 10000, evt.viseme_id)))

result = speech_synthesizer.speak_ssml_async(ssml).get()
```

::: zone-end

::: zone pivot="programming-language-javascript"

```Javascript
var synthesizer = new SpeechSDK.SpeechSynthesizer(speechConfig, audioConfig);

// Subscribes to viseme received event
synthesizer.visemeReceived = function (s, e) {
    window.console.log("(Viseme), Audio offset: " + e.audioOffset / 10000 + "ms. Viseme ID: " + e.visemeId);
}

synthesizer.speakSsmlAsync(ssml);
```

::: zone-end

::: zone pivot="programming-language-objectivec"

```Objective-C
SPXSpeechSynthesizer *synthesizer =
    [[SPXSpeechSynthesizer alloc] initWithSpeechConfiguration:speechConfig
                                           audioConfiguration:audioConfig];

// Subscribes to viseme received event
[synthesizer addVisemeReceivedEventHandler: ^ (SPXSpeechSynthesizer *synthesizer, SPXSpeechSynthesisVisemeEventArgs *eventArgs) {
    NSLog(@"Viseme event received. Audio offset: %fms, viseme id: %lu.", eventArgs.audioOffset/10000., eventArgs.visemeId);
}];

[synthesizer speakSsml:ssml];
```

::: zone-end

## <a name="map-phonemes-to-visemes"></a>Mappa fonem till visemes

Visemes varierar efter språk. Varje språk har en uppsättning visemes som motsvarar dess specifika fonem. I följande tabell visas korrespondensen mellan IPA-fonem (International fonetisk alfabet) och viseme-ID: n för engelska (USA).

| IPA | Exempel | Viseme-ID |
|-----|---------|-----------|
| i   | **EA** t   | 6 |
| ɪ   | **i** f | 6 |
| eɪ  | **en** te | 4 |
| ɛ | **e** mycket | 4 |
|æ  |   **en** ctive        |1|
|ɑ  |   **o**-bstinate     |2|
|ɔ  |   c **au** se         |3|
|ʊ  |   b **säkerhets** k          |4|
|oʊ |   **o** LD           |8|
|ä  |   **U** ber          |7|
|ʌ  |   **u** ncle         |1|
|aɪ |   **i**-CE           |11|
|aʊ |   **OU** t           |9|
|ɔɪ |   **OI** l           |10|
|ju |   **Yu** ma          |[6, 7]|
|ə  |   **en** go           |1|
|ɪɹ |   **öron** s          |[6, 13]|
|ɛɹ |   **luft** plan      |[4, 13]|
|ʊɹ |   c **ditt** e          |[4, 13]|
|aɪ(ə)ɹ |   **Irland**, mark   |[11, 13]|
|aʊ(ə)ɹ |   **timme** s     |[9, 13]|
|ɔɹ |   **eller** ange        |[3, 13]|
|ɑɹ |   **ar**-tist        |[2, 13]|
|ɝ  |   **öron**         |[5, 13]|
|ɚ  |   alla **er**-Gy       |[1, 13]|
|w  |   **w** i:te, s **UE**   |7|
|j  |   **y**-ARD, f **e** w     |6|
|P  |   **p** ut           |21|
|b  |   **b**-ig           |21|
|t  |   **t**-ALK          |19|
|d  |   **d**-ig           |19|
|k  |   **c** ut           |20|
|g  |   **g** o            |20|
|m  |   **m**, s **m**-Ash    |21|
|n  |   **n** o, s **n**      |19|
|ŋ  |   Li **n** k          |20|
|f  |   **f** ork          |18|
|v  |   **v** ärde         |18|
|θ  |   **th** i          |17|
|ð  |   **en**          |17|
|s  |   **s**           |15|
|z  |   **ö**-AP           |15|
|ʃ  |   **sh** e           |16|
|ʒ  |   **J** acques       |16|
|h  |   **h**-ELP          |12|
|tʃ |   **CH** in          |16|
|dʒ |   **j** Oy           |16|
|l  |   **l**-ID, g **l** AD     |14|
|ɹ  |   **r** Ed, b **r** ing    |13|


## <a name="next-steps"></a>Nästa steg

* [Dokumentation om tal SDK-referens](speech-sdk.md)
