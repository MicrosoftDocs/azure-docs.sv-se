---
title: Vad är Live Video Analytics på IoT Edge – Azure
description: Det här avsnittet innehåller en översikt över video analys på IoT Edge. Plattformen erbjuder funktioner som du kan använda för att förbättra IoT-lösningarna. Du kan till exempel avbilda, spela in, analysera direktsänd video och publicera resultaten (video och/eller video analys) till Azure-tjänster.
ms.topic: overview
ms.date: 05/27/2020
ms.openlocfilehash: b9b8726c5e8ad6850e05aeee48fccabee703080e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90904354"
---
# <a name="what-is-live-video-analytics-on-iot-edge-preview"></a>Vad är Live Video Analytics på IoT Edge? (förhandsversion)

Videoanalys i realtid på IoT Edge är en plattform för att skapa intelligenta videoprogram som sträcker sig längs gränsen och molnet. Plattformen ger möjlighet att sammanställa, registrera och analysera video i realtid tillsammans med att publicera resultaten (video och/eller videoanalys) i Azure-tjänster (i molnet och/eller på gränsen). Plattformen kan användas för att förbättra IoT-lösningar med video analys. Live video analys på IoT Edge funktioner kan kombineras med andra Azure IoT Edge moduler som Stream Analytics på IoT Edge, Cognitive Services på IoT Edge och Azure-tjänster i molnet, till exempel Media Services, Event Hub, Cognitive Services osv. för att bygga kraftfulla hybrid program (till exempel Edge + Cloud).

Live video analys på IoT Edge är utformad för att vara en utöknings bar plattform, så att du kan ansluta olika moduler för video analys (till exempel kognitiva tjänst behållare, anpassade Edge-moduler som har skapats med hjälp av maskin inlärnings modeller med öppen källkod eller anpassade modeller som har tränats med dina egna data) och använda dem för att analysera direktsänd video utan att behöva bekymra dig om komplexiteten

## <a name="accelerate-iot-solutions-development"></a>Påskynda utvecklingen av IoT-lösningar 

IoT-lösningar som kombinerar videoanalys med signaler från andra IoT-sensorer och/eller affärsdata kan hjälpa dig att automatisera eller halvautomatisera affärsbeslut, vilket resulterar i produktivitetsförbättringar. Med Videoanalys i realtid på IoT Edge kan du skapa sådana lösningar snabbare. Du kan fokusera på att skapa videoanalysmoduler och logik som är specifika för din verksamhet och låta plattformen dölja det komplicerade med att hantera och köra en videopipeline.

Med live video analys på IoT Edge kan du fortsätta att använda dina [CCTV-kameror](https://en.wikipedia.org/wiki/Closed-circuit_television_camera) med dina befintliga [video hanterings system (VM)](https://en.wikipedia.org/wiki/Video_management_system) och bygga video analys appar oberoende av varandra. Videoanalys i realtid på IoT Edge kan användas tillsammans med SDK:er och verktyg för visuellt innehåll för att skapa avancerade IoT-lösningar. Diagrammet nedan visar detta.

![Utveckla IoT-lösningar med real tids video analys på IoT Edge](./media/overview/product-diagram.svg)

## <a name="supported-environments"></a>Miljöer som stöds

Linux x86-64-och ARM64-miljöer stöds.
> [!NOTE]
> Stöd för ARM64-enheter är tillgängligt i builds `1.0.4` och senare.
> Stöd för att köra Azure IoT Edge runtime på ARM64-enheter finns i [offentlig för hands version](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="get-started"></a>Kom igång

Läs följande begrepps artiklar och försök sedan med en snabb start för att köra rörelse identifiering på en Live-videofeed.

### <a name="concepts"></a>Begrepp

* [Mediegraf](media-graph-concept.md)
* [Videoinspelning](video-recording-concept.md)
* [Videouppspelning](video-playback-concept.md)
* [Kontinuerlig videoinspelning](continuous-video-recording-concept.md)
* [Händelsebaserad videoinspelning](event-based-video-recording-concept.md)
* [Videoanalys i realtid utan videoinspelning](analyze-live-video-concept.md)

## <a name="next-steps"></a>Nästa steg

* Följ [snabb starten: kör live video analys med din egen modell](use-your-model-quickstart.md) artikel för att se hur du kan köra rörelse identifiering i en Live-videofeed.
* Granska [terminologi](terminology.md)
* Utforska [material med öppen källkod i Real video analys](https://github.com/Azure/live-video-analytics)

