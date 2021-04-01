---
title: Tal enheter SDK mikrofon mat ris rekommendationer
titleSuffix: Azure Cognitive Services
description: Tal enheter SDK mikrofon mat ris rekommendationer. Dessa array-Geometries rekommenderas för användning med Microsoft Audio-stacken.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: erhopf
ms.openlocfilehash: 698a1d52af6c2472d6c025851ead1a0b6a6dff82
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "95015349"
---
# <a name="speech-devices-sdk-microphone-array-recommendations"></a>Tal enheter SDK mikrofon mat ris rekommendationer

I den här artikeln får du lära dig hur du utformar en mikrofon för tal enheter SDK.

Tal enheter SDK fungerar bäst med en mikrofon som har utformats enligt följande rikt linjer, inklusive mikrofonens geometri och val av komponent. Vägledning ges även om integrering och andra överväganden.

## <a name="microphone-geometry"></a>Mikrofon geometri

Följande array-Geometries rekommenderas för användning med Microsoft Audio-stacken. Platsen för ljud källor och avvisande av omgivande brus har förbättrats med ett större antal mikrofoner med beroenden för vissa program, användar scenarier och enhets form faktorn.

| MICS & Geometry | Cirkulär matris | Cirkulär matris | Linjär matris | Linjär matris |
| --- | -------------- | --- | ------------ | --- |
|     | <img src="media/speech-devices-sdk/7-mic-c.png" alt="7 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-c.png" alt="4 mic circular array" width="150"/> | <img src="media/speech-devices-sdk/4-mic-l.png" alt="4 mic linear array" width="150"/> | <img src="media/speech-devices-sdk/2-mic-l.png" alt="2 mic linear array" width="150"/> |
| \# Mics | 7 | 4 | 4 | 2 |
| Geometri | 6, yttre, 1 Center, radie = 42,5 mm, jämnt fördelad | 3 yttre, 1 Center, radie = 42,5 mm, jämnt fördelad | Length = 120 mm, avstånd = 40 mm | Avstånd = 40 mm |

Mikrofon kanaler bör sorteras enligt den numrering som visas för varje över gång, vilket ökar från 0. Microsoft Audio-stacken kräver en ytterligare referens ström för ljud uppspelning för att utföra avvisning av eko.

## <a name="component-selection"></a>Val av komponent

Mikrofon komponenter bör väljas för att korrekt återge en signal utan brus och störningar.

De rekommenderade egenskaperna vid val av mikrofoner är:

| Parameter | Rekommenderas |
| --------- | ----------- |
| SNR | \>= 65 dB (1 kHz Signal 94 dBSPL, viktat brus) |
| Amplitud matchning | ± 1 dB @ 1 kHz |
| Fas matchning | ± 2 ° @ 1 kHz |
| Plats för akustisk överlagring (AOP) | \>= 120 dBSPL (THD = 10%) |
| Bit hastighet | Minst 24-bitars |
| Samplingsfrekvens | Minst 16 kHz\* |
| Frekvens svar | ± 3 dB, 200-8000 Hz flytande mask\* |
| Tillförlitlighet | Lagrings temperatur intervall – 40 ° c till 70 ° c<br />Drift temperatur intervall – 20 ° c till 55 ° c |

\*_Högre samplings frekvenser eller "bredare" frekvens intervall kan krävas för VoIP-program (High-Quality Communications)_

Val av lämplig komponent måste kombineras med en lämplig electroacoustic-integrering för att undvika att de använda komponenternas prestanda försämras. Unika användnings fall kan också kräva ytterligare krav (till exempel: drift temperatur intervall).

## <a name="microphone-array-integration"></a>Integrering av mikrofon mat ris

Mikrofonens prestanda när den är integrerad i en enhet skiljer sig från komponent specifikationen. Det är viktigt att se till att mikrofonerna är väl matchade efter integrationen. Därför bör enhetens prestanda mätas efter fast vinst eller EQ uppfylla följande rekommendationer:

| Parameter          | Rekommenderas                                        |
| ------------------ | -------------------------------------------------- |
| SNR                | \> 63 dB (1 kHz Signal 94 dBSPL, viktat brus) |
| Känslighet för utdata | – 26 dBFS/pa @ 1 kHz (rekommenderas)                  |
| Amplitud matchning | ± 2 dB, 200-8000 Hz                                |
| THD%\*             | ≤ 1%, 200-8000 Hz, 94 dBSPL, femte ordningen             |
| Frekvens svar | ± 6 dB, 200-8000 Hz flytande mask\*\*              |

\*\*_En högtalare med låg förvrängning krävs för att mäta THD (t. ex. Neumann KH120)_

\*\*_"Bredare" frekvens intervall kan vara nödvändigt för VoIP-program (High-Quality Communications)_

## <a name="speaker-integration-recommendations"></a>Rekommendationer för högtalar integrering

Eftersom avvisning av eko krävs för tal igenkännings enheter som innehåller högtalare, tillhandahålls ytterligare rekommendationer för val och integrering av talare.

| Parameter | Rekommenderas |
| --------- | ----------- |
| Överväganden vid linearitet | Ingen icke-linjär bearbetning efter en högtalar referens krävs annars en maskinvarubaserad loopback-referens ström |
| Talare loopback | Tillhandahålls via WASAPI, privata API: er, anpassade ALSA-plugin-program (Linux) eller tillhandahålls via kanal för inbyggd program vara |
| THD% | 3: e oktav band som ligger minst femte ordningen, 70 dBA-uppspelning @ 0,8 m ≤ 6,3%, 315-500 Hz ≤ 5%, 630-5000 Hz |
| Eko koppling till mikrofoner | \> – 10 dB-TCLw med ITU-T G. 122, bilaga B. 4 metod, normaliserad till MIC-nivå<br />TCLw = TCLwmeasured \+ (uppmätt nivå – känslighet för mål)<br />TCLw = TCLwmeasured \+ (uppmätt nivå – (-26)) |

## <a name="integration-design-architecture"></a>Integrations design arkitektur

Följande rikt linjer för arkitektur är nödvändiga vid integrering av mikrofoner i en enhet:

| Parameter | Rekommendation |
| --------- | -------------- |
| Likhet av MIC-port | Alla mikrofon portar har samma längd i matrisen |
| Mikrofon port dimensioner | Port storlek Ø 0,8 – 1,0 mm. Port längd/port diameter \< 2 |
| MIC-tätning         | Packning av tätningar som implementeras enhetligt i stacken. Rekommendera \> 70% komprimerings förhållande för skum packningar |
| MIC-tillförlitlighet     | Nätet ska användas för att förhindra damm och ingress (mellan PCB för nedre portade mikrofoner och tätnings-/Top-Cover) |
| MIC-isolering       | Gummi packningar och vibrationer från hela strukturen, särskilt för att isolera eventuella vibrations banor på grund av integrerade högtalare |
| Samplings klocka      | Enhets ljud måste vara fritt från Darr och kombinations fall med låg belastning |
| Post funktion   | Enheten måste kunna registrera enskilda kanals RAW-strömmar samtidigt |
| USB                 | Alla USB-enheter för ljud inspelning måste ange beskrivningar enligt [rev3-specifikationen för USB-ljud enheter](https://www.usb.org/document-library/usb-audio-devices-rev-30-and-adopters-agreement) |
| Mikrofon geometri | Driv rutiner måste implementera [mikrofoner med mikrofon mat ris](/windows-hardware/drivers/audio/ksproperty-audio-mic-array-geometry) korrekt |
| Identifierings möjligheten     | Enheter får inte ha någon avupptäcken maskin vara, inbyggd program vara eller program vara från tredje parts programvarubaserad, som inte är linjärt, till/från enheten |
| Infångnings format      | Infångnings format måste ha en minsta samplings frekvens på 16 kHz och rekommenderat 24-bitars djup |

## <a name="electrical-architecture-considerations"></a>Överväganden vid elektrisk arkitektur

I förekommande fall kan matriser vara anslutna till en USB-värd (till exempel en SoC som kör Microsoft Audio-stacken) och gränssnitt till tal tjänster eller andra program.

Maskin varu komponenter som PDM-till-TDM-konvertering bör se till att det dynamiska intervallet och SNR-mikrofonerna bevaras i omsamplingar.

Höghastighets ljud klass 2,0 bör stödjas i alla ljud-MCU för att tillhandahålla nödvändig bandbredd för upp till sju kanaler med högre samplings hastigheter och bitdjup.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om tal enheter SDK](speech-devices-sdk.md)