---
title: Direkt uppspelnings kodare rekommenderas av Media Services
description: Lär dig mer om direkt uppspelning av lokala kodare som rekommenderas av Media Services
services: media-services
keywords: Encoding; encoders; Media
author: johndeu
manager: johndeu
ms.author: johndeu
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: media-services
ms.openlocfilehash: bf282b3fbba0f22cd41c0420014c46ddb7c958eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98955147"
---
# <a name="verified-on-premises-live-streaming-encoders"></a>Verifierade lokala direkt uppspelnings kodare

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

I Azure Media Services representerar en [Live Event](/rest/api/media/liveevents) (kanal) en pipeline för bearbetning av direktuppspelat innehåll. Live-händelsen tar emot direktsända indata strömmar på ett av två sätt.

* En lokal Live-kodare skickar en RTMP-eller Smooth Streaming-ström (fragmenterad MP4) med flera bit hastigheter till den direktsända händelse som inte är aktive rad för att utföra direktsänd kodning med Media Services. De inmatade strömmarna passerar Live-händelser utan ytterligare bearbetning. Den här metoden kallas **genom strömning**. Vi rekommenderar att Live Encoder skickar strömmar med flera bit hastigheter i stället för en data ström med en bit hastighet till en direkt sändnings händelse för att möjliggöra direkt uppspelning av bit hastighet till klienten. 

    Om du använder data strömmar med flera bit hastigheter för direkt sändnings händelse måste videons GOP storlek och videofragmenten på olika bit hastigheter synkroniseras för att undvika oväntad beteende på uppspelnings sidan.

  > [!TIP]
  > Att använda en direkt metod är det mest ekonomiska sättet att göra Direktsänd strömning.
 
* En lokal Live-kodare skickar en data ström med en bit hastighet till den direktsända händelse som är aktive rad för att utföra direktsänd kodning med Media Services i något av följande format: RTMP eller Smooth Streaming (fragmenterad MP4). Live-händelsen utför sedan direktsänd kodning av den inkommande data strömmen med en bit hastighet till en anpassningsbar video ström med flera bit hastigheter.

Den här artikeln beskriver verifierade lokala direkt uppspelnings kodare. Verifieringen görs via självverifierade leverantörer eller kundverifiering. Microsoft Azure Media Services är inte fullständig eller rigorös testning av varje kodare, och omverifierar inte kontinuerligt vid uppdateringar på nytt. Instruktioner för hur du verifierar din lokala Live-kodare finns i [Verifiera din lokala kodare](become-on-premises-encoder-partner.md)

Detaljerad information om Live encoding med Media Services finns i [direkt uppspelning med Media Services v3](live-streaming-overview.md).

## <a name="encoder-requirements"></a>Kodarens krav

Kodare måste ha stöd för TLS 1,2 när du använder HTTPS-eller RTMP-protokoll.

## <a name="live-encoders-that-output-rtmp"></a>Live-kodare som utdata av RTMP

Media Services rekommenderar att du använder någon av följande livekodare som har RTMP som utdata. URL-scheman som stöds är `rtmp://` eller `rtmps://` .

Vid direktuppspelning via RTMP ska du kontrollera inställningarna för brandvägg och /eller proxy för att bekräfta att de utgående TCP-portarna 1935 och 1936 är öppna.<br/><br/>
Vid direktuppspelning via RTMPS ska du kontrollera inställningarna för brandvägg och /eller proxy för att bekräfta att de utgående TCP-portarna 2935 och 2936 är öppna.

> [!NOTE]
> Kodare måste ha stöd för TLS 1,2 när de använder RTMP-protokollen.

- Adobe Flash Media Live Encoder 3.2
- [ANTIX Digital](http://www.antixdigital.com/) StreamZ Live (tidigare Tänk på kommunikation SelenioFlex Live)
- [Blackmagic ATEM Mini-och ATEM-mini PRO](https://www.blackmagicdesign.com/products/atemmini)
- [Cambria Live 4,3](https://www.capellasystems.net/products/cambria-live/)
- Grundämne Live (version 2.14.15 och senare)
- [Ffmpeg](https://www.ffmpeg.org)
- [GoPro](https://gopro.com/help/articles/block/getting-started-with-live-streaming) Hjälte 7 och hjälte 8
- Haivision KB
- Haivision Makito X HEVC
- [Restream.io](https://restream.io/)
- OBS Studio
- [Streamlabs ONLINEBANKSYSTEM](https://streamlabs.com/)
- [Switcher Studio (iOS)](https://www.switcherstudio.com/)
- Wirecast för multistream (version 13.0.2 eller högre på grund av TLS 1,2-krav)
- Multistream Wirecast S (endast RTMP stöds. Inga RTMP-stöd på grund av brist på TLS 1.2 +)
- Teradek Slice 756
- VMIX
- xStream

> [!WARNING]
> Listan över kodare är bara en rekommendations lista. Kodare testas eller verifieras inte av Microsoft på kontinuerlig basis och uppdateringar eller avbrotts ändringar kan införas av Encoder-leverantörer eller projekt med öppen källkod som kan bryta kompatibiliteten. 

## <a name="live-encoders-that-output-fragmented-mp4-smooth-streaming-ingest"></a>Live-kodare som visar fragmenterad MP4 (Smooth Streaming inmatning)

Media Services rekommenderar att du använder någon av följande Live-kodare med multi-bit-Smooth Streaming (fragmenterad MP4) som utdata. URL-scheman som stöds är `http://` eller `https://` .

> [!NOTE]
> Kodare måste ha stöd för TLS 1,2 när de använder HTTPS-protokoll.

- Ateme TITAN Live
- [ANTIX Digital](http://www.antixdigital.com/) StreamZ Live (tidigare Tänk på kommunikation SelenioFlex Live)
- Cisco Digital Media Encoder 2200
- Grundämne Live (version 2.14.15 och högre på grund av kraven för TLS 1,2)
- Envivio 4Caster C4 Gen III 
- [Ffmpeg](https://www.ffmpeg.org)
- Media Excel Hero Live och Hero 4K (UHD/HEVC)

> [!TIP]
>  Om du strömmar Live-händelser på flera språk (till exempel ett engelskt ljud spår och ett spanskt ljud spår) kan du göra detta med mediet för Live-kodare i media som kon figurer ATS för att skicka Live-flödet till en direkt sändnings händelse.

> [!WARNING]
> Listan över kodare är bara en rekommendations lista. Kodare testas eller verifieras inte av Microsoft på kontinuerlig basis och support eller buggar kan introduceras av kodarens leverantörer eller med projekt med öppen källkod som bryter kompatibiliteten när som helst. 

## <a name="configuring-on-premises-live-encoder-settings"></a>Konfigurera lokala inställningar för Live-kodare

Information om vilka inställningar som är giltiga för din live event-typ finns i [jämförelse av aktiva händelse typer](live-event-types-comparison.md).

### <a name="playback-requirements"></a>Uppspelningskrav

Om du vill spela upp innehåll måste både ljud-och video strömmar finnas. Det finns inte stöd för uppspelning av data strömmen med video.

### <a name="configuration-tips"></a>Konfigurationstips

- Använd en direktkopplad internetanslutning när det är möjligt.
- När du bestämmer bandbredds kraven kan du dubblera bit hastigheterna för strömmande data. Även om detta inte är obligatoriskt bidrar den här enkla regeln till att minimera påverkan av nätverks belastning.
- När du använder programvarubaserade kodare, Stäng alla onödiga program.
- Om du ändrar konfigurationen för konfigurationen efter det att sändningen har startat, har det negativa effekter på evenemanget. Konfigurations ändringar kan orsaka att händelsen blir instabil. 
- Testa alltid och validera nya versioner av kod program vara för fortsatt kompatibilitet med Azure Media Services. Microsoft omvaliderar inte kodare i den här listan och de flesta verifieringar utförs av program varu leverantörerna direkt som en "själv certifiering".
- Se till att du ger dig tid att konfigurera evenemanget. För storskaliga händelser rekommenderar vi att du startar installationen en timme före evenemanget.
- Använd H. 264 video-och AAC-LC-ljud-codec-utdata.
- Håll dig till lösningar som stöds och bild frekvenser för den typ av direkt sändnings händelse som du skickar till (till exempel 60FPS för närvarande nekas.)
- Se till att det finns nyckel bilds-eller GOP temporal justering över video kvaliteter.
- Se till att det finns ett unikt ström namn för varje video kvalitet.
- Använd strikt CBR-kodning som rekommenderas för bästa prestanda för anpassad bit hastighet.

> [!IMPORTANT]
> Titta på datorns fysiska tillstånd (CPU/minne/etc) som att överföra fragment till molnet inbegriper CPU-och IO-åtgärder. Om du ändrar några inställningar i kodaren måste du vara noga med att återställa kanaler/live-händelsen för att ändringen ska börja gälla.

## <a name="see-also"></a>Se även

[Direktsänd strömning med Media Services v3](live-streaming-overview.md)

## <a name="next-steps"></a>Nästa steg

[Så här verifierar du din kodare](become-on-premises-encoder-partner.md)
