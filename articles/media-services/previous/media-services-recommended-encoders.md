---
title: Läs mer om kodare som rekommenderas av Azure Media Services | Microsoft Docs
description: Den här artikeln innehåller en lista över lokala kodare som rekommenderas av Azure Media Services.
services: media-services
keywords: Encoding; encoders; Media
author: dbgeorge
manager: johndeu
ms.author: johndeu
ms.date: 03/20/2019
ms.topic: article
ms.service: media-services
ms.openlocfilehash: 11456a8dc55a98778bf44225bf659e506e2d3a3f
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97510064"
---
# <a name="recommended-on-premises-encoders"></a>Rekommenderade lokala kodare

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

När Live streaming med Azure Media Services kan du ange hur du vill att din kanal ska ta emot indataströmmen. Om du väljer att använda en lokal kodare med en Live encoding-kanal bör kodaren skicka en högkvalitativ data ström med hög kvalitet som utdata. Om du väljer att använda en lokal kodare med en genom strömnings kanal, bör kodaren skicka en data ström med flera bit hastigheter som utdata med alla önskade utgångs kvaliteter. Mer information finns i [Direktsänd strömning med lokala kodare](media-services-live-streaming-with-onprem-encoders.md).

## <a name="encoder-requirements"></a>Kodarens krav

Kodare måste ha stöd för TLS 1,2 när du använder HTTPS-eller RTMP-protokoll.

## <a name="live-encoders-that-output-rtmp"></a>Live-kodare som utdata av RTMP 

Azure Media Services rekommenderar att du använder någon av följande Live-kodare som har RTMP som utdata:

- Adobe Flash Media Live Encoder 3.2
- Haivision Makito X HEVC
- Haivision KB
- Wirecast för multistream (version 13.0.2 eller högre på grund av TLS 1,2-krav)

  Kodare måste ha stöd för TLS 1,2 när du använder RTMP-protokoll.
- Teradek Slice 756
- OBS Studio
- VMIX
- xStream
- Switcher Studio (iOS)

## <a name="live-encoders-that-output-fragmented-mp4"></a>Live-kodare som utdata fragmenterade MP4 

Azure Media Services rekommenderar att du använder någon av följande Live-kodare med fragmenterad-MP4 (Smooth Streaming) med flera bit hastigheter som utdata:

- Media Excel Hero Live och Hero 4K (UHD/HEVC)
- Ateme TITAN Live
- Cisco Digital Media Encoder 2200
- Grundämne Live (version 2.14.15 och högre på grund av kraven för TLS 1,2)

  Kodare måste ha stöd för TLS 1,2 när de använder HTTPS-protokoll.
- Envivio 4Caster C4 Gen III
- Föreställ dig Selenio-MCP3

> [!NOTE]
> En Live-kodare kan skicka en data ström med en bit hastighet till en direkt kanal, men den här konfigurationen rekommenderas inte eftersom den inte tillåter strömning med anpassad bit hastighet till klienten.

## <a name="how-to-become-an-on-premises-encoder-partner"></a>Så här blir du en lokal kodare-partner

Som en Azure Media Services lokal Encoder-partner Media Services befordrar din produkt genom att rekommendera din kodare till företags kunder. För att bli en lokal kodare-partner måste du kontrol lera kompatibiliteten för din lokala kodare med Media Services. Det gör du genom att utföra följande verifieringar:

Släpp igenom kanal verifiering
1. Skapa eller besök ditt Azure Media Services konto
2. Skapa och starta en **genom strömnings** kanal
3. Konfigurera kodaren för att skicka en Live-bit-ström med flera bit hastigheter.
4. Skapa en publicerad Live-händelse
5. Kör din Live-kodare i cirka 10 minuter
6. Stoppa Live-händelsen
7. Skapa, starta en slut punkt för direkt uppspelning, Använd en spelare som [Azure Media Player](https://aka.ms/azuremediaplayer) för att se den arkiverade till gången för att säkerställa att uppspelningen inte har några synliga fel för alla kvalitets nivåer (eller titta på och verifiera via URL: en för för hands versionen under Live-sessionen innan steg 6)
8. Registrera till gångs-ID, publicerad strömmande URL för Live-arkivet och de inställningar och den version som används i din Live Encoder
9. Återställ kanal statusen när du har skapat varje exempel
10. Upprepa steg 3 till och med 9 för alla konfigurationer som stöds av kodaren (med och utan AD-signalering/under texter/olika kodnings hastigheter)

Verifiering av direktsänd kodnings kanal
1. Skapa eller besök ditt Azure Media Services konto
2. Skapa och starta en **Live encoding** -kanal
3. Konfigurera kodaren för att skicka en Live-dataström med enkel bit hastighet.
4. Skapa en publicerad Live-händelse
5. Kör din Live-kodare i cirka 10 minuter
6. Stoppa Live-händelsen
7. Skapa, starta en slut punkt för direkt uppspelning, Använd en spelare som [Azure Media Player](https://aka.ms/azuremediaplayer) för att se den arkiverade till gången för att säkerställa att uppspelningen inte har några synliga fel för alla kvalitets nivåer (eller titta på och verifiera via URL: en för för hands versionen under Live-sessionen innan steg 6)
8. Registrera till gångs-ID, publicerad strömmande URL för Live-arkivet och de inställningar och den version som används i din Live Encoder
9. Återställ kanal statusen när du har skapat varje exempel
10. Upprepa steg 3 till och med 9 för alla konfigurationer som stöds av kodaren (med och utan AD-signalering/under texter/olika kodnings hastigheter)

Longevity-verifiering
1. Skapa eller besök ditt Azure Media Services konto
2. Skapa och starta en **genom strömnings** kanal
3. Konfigurera kodaren för att skicka en Live-bit-ström med flera bit hastigheter.
4. Skapa en publicerad Live-händelse
5. Kör din Live-kodare i en vecka eller längre
6. Använd en spelare som [Azure Media Player](https://aka.ms/azuremediaplayer) för att se direkt uppspelningen från tid till gång (eller Arkiverad till gång) för att säkerställa att uppspelningen inte har några synliga fel
7. Stoppa Live-händelsen
8. Registrera till gångs-ID, publicerad strömmande URL för Live-arkivet och de inställningar och den version som används i din Live Encoder

Skicka slutligen dina inspelade inställningar och Live Archive-parametrarna till Media Services via e-post amsstreaming@microsoft.com . Vid inleverans utför Media Services verifierings test på exemplen från din Live Encoder. Du kan kontakta Media Services om du har frågor om den här processen.
