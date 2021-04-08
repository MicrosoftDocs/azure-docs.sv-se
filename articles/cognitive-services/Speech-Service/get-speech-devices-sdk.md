---
title: Hämta Speech Devices SDK
titleSuffix: Azure Cognitive Services
description: Tal tjänsten fungerar med en mängd olika enheter och ljud källor. Nu kan du ta dina tal program till nästa nivå med matchad maskin vara och program vara. I den här artikeln får du lära dig hur du får åtkomst till tal enheter SDK och börjar utveckla.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2019
ms.author: erhopf
ms.openlocfilehash: 69898e64934c363a18a3ce2fa5e678116624bd24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "95026377"
---
# <a name="get-the-cognitive-services-speech-devices-sdk"></a>Hämta Cognitive Services Speech-enheter SDK

Tal enheter SDK är ett förjusterat bibliotek som utformats för att fungera med syftes färdiga utvecklings paket och varierande konfigurationer för mikrofon mat ris.

## <a name="choose-a-development-kit"></a>Välj ett utvecklings paket

|Enheter|Specifikation|Beskrivning|Scenarier|
|--|--|--|--|
|[Urbetter dev-paket](http://www.urbetter.com/products_56/278.html) ![ URbetter DDK](media/speech-devices-sdk/device-urbetter.jpg)|7 MIC-matris, ARM-SOC, WIFI, Ethernet, HDMI, USB-kamera. <br>Linux|En nivå för tal enheter på bransch nivå som anpassar Microsoft MIC-matrisen och stöder utökade I/O, till exempel HDMI/Ethernet och mer USB-kringutrustning <br> [Kontakta Urbetter](http://www.urbetter.com/products_56/278.html)|Konversations avskrift, utbildning, sjukhus, robots, OTT Box, Voice agent, enhet till|
|[Roobo Smart Audio dev-paket](http://ddk.roobo.com)<br>[Installations program](speech-devices-sdk-roobo-v1.md)  /  [Snabb start](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android) ![ Roobo Smart Audio dev-paket](media/speech-devices-sdk/device-roobo-v1.jpg)|7 MIC-matris, ARM-SOC, WIFI, ljud ut, i/o. <br>[Android](./speech-devices-sdk-quickstart.md?pivots=platform-android%253fpivots%253dplatform-android)|De första tal enheternas SDK för att anpassa Microsoft MIC-matrisen och front bearbetnings-SDK: n för att utveckla högkvalitativa avskrifter och tal scenarier|Konversations avskrift, smart högtalare, röst agent, Wearable|
|[Azure Kinect DK](https://azure.microsoft.com/services/kinect-dk/)<br>[Installations program](../../kinect-dk/set-up-azure-kinect-dk.md)  /  [Snabb start](./speech-devices-sdk-quickstart.md?pivots=platform-windows%253fpivots%253dplatform-windows) ![ Azure Kinect DK](media/speech-devices-sdk/device-azure-kinect-dk.jpg)|7 MIC mat ris RGB-och djup kameror. <br>[Windows](./speech-devices-sdk-quickstart.md?pivots=platform-windows%253fpivots%253dplatform-windows) / [Linux](./speech-devices-sdk-quickstart.md?pivots=platform-linux%253fpivots%253dplatform-linux)|Ett Developer Kit med AI-sensorer (Advanced artificiell Intelligence) för att skapa sofistikerade modeller för dator vision och tal. Den kombinerar en förstklassig mat ris-och djup kamera med hög klass med en video kamera och orienterings sensor – allt i en liten enhet med flera lägen, alternativ och SDK: er för att hantera en mängd olika beräknings typer.|Konversations avskrift, Robotics, smart uppbyggnad|
|Roobo Smart Audio dev kit 2<br>[Installation](speech-devices-sdk-roobo-v2.md)<br>![Roobo Smart Audio dev kit 2](media/speech-devices-sdk/device-roobo-v2.jpg)|7 MIC-matris, ARM-SOC, WIFI, Bluetooth, i/o. <br>Linux|De andra generationens tal enheter SDK som tillhandahåller alternativa OS och fler funktioner i en kostnads effektiv referens design.|Konversations avskrift, smart högtalare, röst agent, Wearable|


## <a name="download-the-speech-devices-sdk"></a>Hämta tal enheter SDK

Hämta [tal enheter SDK](./speech-devices-sdk.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Kom igång med tal enheter SDK](./speech-devices-sdk-quickstart.md?pivots=platform-android)