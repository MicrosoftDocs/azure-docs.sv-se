---
title: Azure Media Services LiveEvent-typer
description: I Azure Media Services kan en Live-händelse ställas in på antingen en *direkt-* eller *direktsänd kodning*. Den här artikeln innehåller en detaljerad tabell som jämför aktiva händelse typer.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 3126cbf4f9d87e9baa7267e309210b751a47069d
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98897806"
---
# <a name="live-event-types-comparison"></a>Jämförelse av händelse typer i real tid

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

I Azure Media Services kan en  [Live-händelse](/rest/api/media/liveevents) ställas in på antingen en *direkt* uppspelning (en lokal Live-kodare) som skickar en data ström med flera bit hastigheter eller *direktsänd kodning* (en lokal Live-kodare skickar en data ström med en bit hastighet). 

I den här artikeln jämförs funktionerna i de olika typerna av Live-händelser.

## <a name="types-comparison"></a>Jämförelse mellan typer 

I följande tabell jämförs funktionerna i de olika typerna av Live-händelser. Typerna anges när du skapar med [LiveEventEncodingType](/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType. None** – en lokal Live-kodare skickar en data ström med flera bit hastigheter. De inmatade strömmarna passerar genom Live-händelsen utan ytterligare bearbetning. Kallas även direkt sändnings händelse.
* **LiveEventEncodingType. standard** – en lokal Live-kodare skickar en data ström med en bit hastighet till Live-händelsen och Media Services skapar flera bit hastighets strömmar. Om bidrags flödet är av 720p eller högre, kommer **Default720p** för för inställningen att koda en uppsättning med 6 lösnings-/bit hastighets par (information följer senare i artikeln).
* **LiveEventEncodingType. Premium1080p** – en lokal Live-kodare skickar en data ström med en bit hastighet till Live-händelsen och Media Services skapar flera bit hastighets strömmar. För inställningen Default1080p anger utdata för upplösnings-och bit hastighets par (information följer senare i artikeln). 

| Funktion | Direkt sändnings händelse | Standard-eller Premium1080p Live-händelse |
| --- | --- | --- |
| Inmatade enstaka bit hastighet kodas till flera bit hastigheter i molnet |Inga |Ja |
| Maximal video upplösning för bidrags flöden |4K (4096x2160 vid 60 bild rutor/SEK) |1080p (1920x1088 vid 30 bild rutor/SEK)|
| Rekommenderade högsta skikt i bidrags flödet|Upp till 12|Ett ljud|
| Maximalt antal lager i utdata| Samma som indatamängden|Upp till 6 (se system för inställningar nedan)|
| Maximal sammanställd bandbredd för bidrags flödet|60 Mbit/s|Ej tillämpligt|
| Högsta bit hastighet för ett enskilt lager i bidraget |20 Mbit/s|20 Mbit/s|
| Stöd för flera språk ljud spår|Ja|Inga|
| Codecs för inkompatibla video |H. 264/AVC och H. 265/HEVC|H. 264/AVC|
| Video-codecar som stöds|Samma som indatamängden|H. 264/AVC|
| Video bitdjup, indata och utdata som stöds|Upp till 10 bitar, inklusive HDR 10/HLG|8-bitars|
| Ljud-codec som stöds|AAC-LC, HE-AAC v1, HE-AAC v2|AAC-LC, HE-AAC v1, HE-AAC v2|
| Ljud-codec som stöds|Samma som indatamängden|AAC-LC|
| Maximal video upplösning för video om utdata|Samma som indatamängden|Standard-720p, Premium1080p – 1080p|
| Högsta bild Rute frekvens för inmatad video|60 bild rutor per sekund|Standard-eller Premium1080p-30 bild rutor per sekund|
| Protokoll för indataport|RTMP, fragmenterad – MP4 (Smooth Streaming)|RTMP, fragmenterad – MP4 (Smooth Streaming)|
| Pris|Se [sidan med priser](https://azure.microsoft.com/pricing/details/media-services/) och klicka på fliken "live video"|Se [sidan med priser](https://azure.microsoft.com/pricing/details/media-services/) och klicka på fliken "live video"|
| Maximal kör tid| 24 timmar x 365 dagar, direkt linjärt | 24 timmar x 365 dagar, direkt linjärt (för hands version)|
| Möjlighet att gå igenom inbäddade CEA 608/708-textning|Ja|Ja|
| Möjlighet att aktivera direkt avskrift|Ja|Ja|
| Stöd för att infoga mellanliggande|Inga|Inga|
| Stöd för AD-signalering via API| Inga|Inga|
| Stöd för AD-signalering via SCTE-35-meddelanden på band|Ja|Ja|
| Möjlighet att återställa från korta bås i bidrags flödet|Ja|Delvis|
| Stöd för icke-uniform GOPs|Ja|Nej – indatatyper måste ha fast GOP varaktighet|
| Stöd för variabla bild Rute frekvens inmatade|Ja|Nej – indatatyper måste vara fasta bild hastigheter. Mindre variationer tolereras, till exempel vid hög rörelse i bakgrunden. Men bidrags flödet kan inte släppa bild Rute frekvensen (till exempel till 15 bild rutor per sekund).|
| Shutoff av direkt sändning när inmatnings flöde förloras|Inga|Efter 12 timmar, om ingen LiveOutput körs|

## <a name="system-presets"></a>System för inställningar

Lösningarna och bit hastigheterna i utdata från Live-kodaren bestäms av [presetName](/rest/api/media/liveevents/create#liveeventencoding). Om du använder en **vanlig** Live-kodare (LiveEventEncodingType. standard) anger *Default720p* -förvalet en uppsättning med 6 lösning/bit hastighets par som beskrivs nedan. Annars, om du använder en **Premium1080p** Live-kodare (LiveEventEncodingType. Premium1080p), anger *Default1080p* -förvalet utdatatypen för par med upplösning/bit hastighet.

> [!NOTE]
> Du kan inte använda Default1080p för en aktiv händelse om den har kon figurer ATS för vanlig Live-kodning – du får ett fel meddelande. Du får också ett fel meddelande om du försöker tillämpa Default720p på en Premium1080p Live-kodare.

### <a name="output-video-streams-for-default720p"></a>Utgående video strömmar för Default720p

Om bidrags flödet är av 720p eller högre, kodas **Default720p** för för inställningen till följande 6 lager. I tabellen nedan representerar bit hastigheten i kbit/s, MaxFPS representerar den högsta tillåtna bild Rute frekvensen (i ramar/Second) och profilen H. 264-profilen används.

| Hastigheten | Bredd | Höjd | MaxFPS | Profil |
| --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Högt |
| 2200 |960 |540 |30 |Högt |
| 1350 |704 |396 |30 |Högt |
| 850 |512 |288 |30 |Högt |
| 550 |384 |216 |30 |Högt |
| 200 |340 |192 |30 |Högt |

> [!NOTE]
> Om du behöver anpassa för hands kodning av direktsänd kodning öppnar du ett support ärende via Azure Portal. Du bör ange önskad tabell med video upplösning och bit hastigheter. Anpassning av bit hastighet för ljud kodning stöds inte. Kontrollera att det bara finns ett lager vid 720p och högst 6 lager. Ange också att du begär en för inställning.

### <a name="output-video-streams-for-default1080p"></a>Utgående video strömmar för Default1080p

Om bidrags flödet är av 1080p-upplösning kommer **Default1080p** -förvalet att koda flödet till följande 6 lager.

| Hastigheten | Bredd | Höjd | MaxFPS | Profil |
| --- | --- | --- | --- | --- |
| 5500 |1920 |1080 |30 |Högt |
| 3000 |1280 |720 |30 |Högt |
| 1600 |960 |540 |30 |Högt |
| 800 |640 |360 |30 |Högt |
| 400 |480 |270 |30 |Högt |
| 200 |320 |180 |30 |Högt |

> [!NOTE]
> Om du behöver anpassa för hands kodning av direktsänd kodning öppnar du ett support ärende via Azure Portal. Du bör ange den önskade tabellen med upplösning och bithastigheter. Kontrol lera att det bara finns ett lager i 1080p och högst 6 lager. Ange också att du begär en för inställning för en Premium1080p Live-kodare. De exakta värdena för bit hastigheterna och lösningarna kan justeras med tiden.

### <a name="output-audio-stream-for-default720p-and-default1080p"></a>Mata ut ljud ström för Default720p och Default1080p

För både *Default720p* -och *Default1080p* -för inställningar kodas ljudet till stereo AAC-LC vid 128 kbit/s. Samplings frekvensen följer efter ljud spåret i bidrags flödet.

## <a name="implicit-properties-of-the-live-encoder"></a>Implicita egenskaper för Live-kodaren

I föregående avsnitt beskrivs egenskaperna för den Live-kodare som kan styras uttryckligen, via för inställningen, till exempel antalet lager, lösningar och bit hastigheter. Det här avsnittet förklarar de implicita egenskaperna.

### <a name="group-of-pictures-gop-duration"></a>Grupp med bilder (GOP) varaktighet

Live-kodaren följer [GOP](https://en.wikipedia.org/wiki/Group_of_pictures) -strukturen för bidrags flödet, vilket innebär att output-lagren har samma GOP-varaktighet. Därför rekommenderar vi att du konfigurerar den lokala kodare för att skapa en bidrags flöde som har fast GOP varaktighet (vanligt vis 2 sekunder). Detta säkerställer att utgående HLS-och MPEG-dataströmmar från tjänsten också har fasta GOP-varaktigheter. Små variationer i GOP-varaktigheter kan förmodligen tolereras av de flesta enheter.

### <a name="frame-rate"></a>Bild Rute frekvens

Live-kodaren följer också varaktigheten för enskilda video bild rutor i bidrags flödet – vilket innebär att utmatnings lagren har bild rutor med samma varaktighet. Därför rekommenderar vi att du konfigurerar den lokala kodare för att skapa en bidrags flöde som har fast bild Rute frekvens (högst 30 bild rutor per sekund). Detta säkerställer att de utgående HLS och MPEG-dataströmmarna från tjänsten också har varaktigheter för fasta bild hastigheter. Små variationer i bild Rute frekvenser kan tolereras av de flesta enheter, men det finns ingen garanti för att Live-kodaren kommer att skapa utdata som spelas upp korrekt. Din lokala Live-kodare ska inte släppa ramar (t. ex. under låg energi förhållanden) eller varierande bild Rute frekvens på något sätt.

### <a name="resolution-of-contribution-feed-and-output-layers"></a>Lösning av bidrags flöde och utmatnings lager

Live-kodaren är konfigurerad för att undvika att omvandla bidrags flödet. Det innebär att den maximala upplösningen för utdata inte överstiger den som används för bidrags flödet.

Om du till exempel skickar en bidrags väg vid 720p till en Live-händelse som kon figurer ATS för Default1080p Live encoding, kommer utdata bara att ha 5 lager, från och med 720p på 3Mbps, gå ned till 1080p vid 200 kbit/s. Eller om du skickar en bidrags matning på 360p till en Live-händelse som kon figurer ATS för vanlig Live-kodning, kommer utdata att innehålla 3 lager (vid upplösningen 288p, 216p och 192p). I degenererad-fallet, om du skickar en bidrags utmatning av, t. ex. 160x90 bild punkter till en vanlig Live-kodare, kommer utdata att innehålla ett lager med 160x90-upplösning på samma bit hastighet som bidrags flödet.

### <a name="bitrate-of-contribution-feed-and-output-layers"></a>Bit hastighet för bidrags flöde och utmatnings lager

Live-kodaren har kon figurer ATS för att respektera bit hastighets inställningarna i för inställningen, oavsett bit hastighet för bidrags flödet. Detta resulterar i att bit hastigheten för de utgående lagren kan överstiga bidrags flödet. Om du till exempel skickar en bidrags-feed till en upplösning på 1 Mbit/s, förblir utmatnings lagren samma som i [tabellen](live-event-types-comparison.md#output-video-streams-for-default720p) ovan.

## <a name="next-steps"></a>Nästa steg

[Översikt över direkt uppspelning](live-streaming-overview.md)
