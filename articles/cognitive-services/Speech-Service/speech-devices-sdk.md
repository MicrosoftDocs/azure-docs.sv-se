---
title: Tal enheter SDK – tal service
titleSuffix: Azure Cognitive Services
description: Kom igång med tal enheter SDK. Tal tjänsten fungerar med en mängd olika enheter och ljud källor. Tal enheter SDK är ett förjusterat bibliotek som är länkat till design paket för bygge, mikrofon mat ris.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: erhopf
ms.openlocfilehash: 0309329b37436022dc88f8951853c4bb09a2c080
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "95015281"
---
# <a name="what-is-the-speech-devices-sdk"></a>Vad är det för tal enheter SDK?

[Tal tjänsten](overview.md) fungerar med en mängd olika enheter och ljud källor. Nu kan du ta dina tal program till nästa nivå med matchad maskin vara och program vara. Tal enheter SDK är ett förjusterat bibliotek som är länkat till design paket för bygge, mikrofon mat ris.

Med hjälp av tal enheter SDK kan du:

- Testa snabbt nya röst scenarier.
- Integrera enkelt den molnbaserade tal tjänsten i din enhet.
- Skapa en enastående användar upplevelse för dina kunder.

Tal enheternas SDK använder tal- [SDK](speech-sdk.md). Använd våra avancerade algoritmer för ljud bearbetning med enhetens mikrofon mat ris för att skicka ljudet till [tal tjänsten](overview.md). Det ger korrekt [tal igenkänning](speech-to-text.md) i långt fält via brus Undertryckning, avvisning av eko, beamforming och dereverberation.

Du kan också använda tal enhets-SDK: n för att bygga omgivande enheter som har ditt eget [anpassade nyckelord](./custom-keyword-basics.md). Ett anpassat nyckelord är en stack-ikon som startar en användar interaktion som är unik för ditt varumärke.

Med tal enheter SDK kan du använda olika röst funktioner, till exempel [röst assistenter](./voice-assistants.md), enhets sorterings system, [konversations avskrift](./conversation-transcription.md)och smarta högtalare. Du kan svara på användare med text, tala tillbaka till dem på en standard-eller [anpassad röst](./how-to-custom-voice-create-voice.md), tillhandahålla Sök resultat, [översätta](speech-translation.md) till andra språk och mycket annat. Vi ser fram emot att se vad du skapar!

## <a name="get-the-speech-devices-sdk"></a>Hämta Speech Devices SDK

### <a name="android"></a>Android

Tal enheter SDK för Android har stöd för [Roobo v1](speech-devices-sdk-roobo-v1.md) och motsvarande enheter, för dessa Ladda ned den senaste versionen av [Android Speech-enheterna SDK](https://aka.ms/sdsdk-download-android).


Om du har en annan Android-enhet, till exempel en telefon eller mobil, börjar du med [Android-talet SDK](speech-sdk.md)


### <a name="windows"></a>Windows

För Windows tillhandahålls exempel programmet som ett plattforms oberoende Java-program. Ladda ned den senaste versionen av [JRE Speech Devices SDK](https://aka.ms/sdsdk-download-JRE).
Programmet har skapats med tal-SDK-paketet och Sol förmörkelse Java IDE (v4) på 64-bitars Windows. Det körs i en 64-bitars Java 8-körningsmiljö (JRE).

### <a name="linux"></a>Linux

För Linux tillhandahålls exempel programmet som ett plattforms oberoende Java-program. Ladda ned den senaste versionen av [JRE Speech Devices SDK](https://aka.ms/sdsdk-download-JRE).
Programmet har skapats med tal-SDK-paketet och Sol förmörkelse Java IDE (v4) på 64-bitars Linux (Ubuntu 16,04, Ubuntu 18,04, Debian 9, RHEL 8, CentOS 8). Det körs i en 64-bitars Java 8-körningsmiljö (JRE).

Ytterligare binärfiler tillhandahålls för att stödja kommande enheter, [Roobo v2-DDK](https://aka.ms/sdsdk-download-roobov2), [Urbetter DDK](https://aka.ms/sdsdk-download-urbetter), [GGEC-högtalare](https://aka.ms/sdsdk-download-speaker), [Linux-ARM32](https://aka.ms/sdsdk-download-linux-arm32)och [Linux arm64](https://aka.ms/sdsdk-download-linux-arm64).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Välj din tal enhet](get-speech-devices-sdk.md)
> [!div class="nextstepaction"]
> [Hämta en prenumerations nyckel för Speech service kostnads fritt](overview.md#try-the-speech-service-for-free)