---
title: Strömma live med lokala kodare som skapar strömmar med flera bit hastigheter – Azure | Microsoft Docs
description: I det här avsnittet beskrivs hur du konfigurerar en kanal som tar emot en Live-bit ström från en lokal kodare.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: d9f0912d-39ec-4c9c-817b-e5d9fcf1f7ea
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 316372f091833519f0479d07355d2845c82743b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103014850"
---
# <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders"></a>Arbeta med kanaler som tar emot Live-dataströmmar med flera bit hastigheter från lokala kodare

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Med början den 12 maj 2018 kommer Live Channels inte längre att ha stöd för RTP/MPEG-2 Transport Stream-inmatnings protokoll. Migrera från RTP/MPEG-2 till RTMP eller fragmented MP4 (Smooth Streaming) insugnings protokoll.

## <a name="overview"></a>Översikt
I Azure Media Services representerar en *kanal* en pipeline för bearbetning av direktuppspelat innehåll. En kanal tar emot direktsända indata strömmar på något av två sätt:

* En lokal Live-kodare skickar en RTMP-eller Smooth Streaming-ström med flera bit hastigheter till den kanal som inte är aktive rad för att utföra direktsänd kodning med Media Services. De inmatade strömmarna passerar genom kanaler utan ytterligare bearbetning. Den här metoden kallas *genom strömning*. En Live-kodare kan också skicka en data ström med en bit hastighet till en kanal som inte är aktive rad för direktsänd kodning, men vi rekommenderar inte detta. Media Services levererar data strömmen till kunder som begär det.

  > [!NOTE]
  > Att använda en direkt metod är det mest ekonomiska sättet att göra Direktsänd strömning.


* En lokal Live-kodare skickar en data ström med en bit hastighet till den kanal som är aktive rad för att utföra direktsänd kodning med Media Services i något av följande format: RTMP eller Smooth Streaming (fragmenterad MP4). Kanalen utför sedan direktsänd kodning av den inkommande data strömmen med enkel bit hastighet till en video ström med flera bit hastigheter (adaptiv). Media Services levererar data strömmen till kunder som begär det.

Från och med Media Services 2,10-versionen när du skapar en kanal kan du ange hur du vill att din kanal ska ta emot indataströmmen. Du kan också ange om du vill att kanalen ska utföra direktsänd kodning av data strömmen. Du kan välja mellan två alternativ:

* **Släpp igenom**: Ange det här värdet om du planerar att använda en lokal Live-kodare som har en data ström med flera bit hastigheter (direkt uppspelning) som utdata. I det här fallet skickas den inkommande strömmen till utdata utan kodning. Detta är beteendet för en kanal före 2,10-versionen. Den här artikeln innehåller information om hur du arbetar med kanaler av den här typen.
* **Live encoding**: Välj det här värdet om du planerar att använda Media Services för att koda Live-dataströmmen med en bit hastighet till en data ström med flera bit hastigheter. Om du lämnar en Live encoding-kanal i ett **körnings** tillstånd debiteras fakturerings avgifter. Vi rekommenderar att du omedelbart stoppar dina kanaler som körs när din Live-streaming-händelse är klar för att undvika extra Tim debiteringar. Media Services levererar data strömmen till kunder som begär det.

> [!NOTE]
> Den här artikeln beskriver attribut för kanaler som inte är aktiverade för att utföra direktsänd kodning. Information om hur du arbetar med kanaler som är aktiverade för att utföra direktsänd kodning finns i [direkt uppspelning med Azure Media Services för att skapa data strömmar med flera bit](media-services-manage-live-encoder-enabled-channels.md)hastigheter.
>
>Information om rekommenderade lokala kodare finns i [rekommenderade lokala kodare](media-services-recommended-encoders.md).

Följande diagram visar ett Live-direktuppspelning-arbetsflöde som använder en lokal Live-kodare för att ha flera bit-eller fragmenterade MP4-strömmar (Smooth Streaming) som utdata.

![Live-arbetsflöde][live-overview]

## <a name="common-live-streaming-scenario"></a><a id="scenario"></a>Vanligt scenario för Live-direktuppspelning
Följande steg beskriver de uppgifter som ingår i att skapa vanliga program för Live-direktuppspelning.

1. Anslut en videokamera till en dator. Starta och konfigurera en lokal Live-kodare som har en RTMP-eller fragmenterad MP4-ström (Smooth Streaming) med flera bit hastigheter som utdata. Mer information finns i [Support och direktsända kodare för Azure Media Services RTMP](https://go.microsoft.com/fwlink/?LinkId=532824).

    Du kan också utföra det här steget när du har skapat din kanal.
2. Skapa och starta en kanal.

3. Hämta kanalens intag-URL.

    Live-kodaren använder inmatnings-URL: en för att skicka data strömmen till kanalen.
4. Hämta kanalens för hands versions-URL.

    Använd denna URL för att kontrollera att din kanal tar emot den direktsända dataströmmen korrekt.
5. Skapa ett program.

    När du använder Azure Portal skapas även en till gång när du skapar ett program.

    När du använder .NET SDK eller REST måste du skapa en till gång och ange om du vill använda den här till gången när du skapar ett program.
6. Publicera den till gång som är associerad med programmet.   

    >[!NOTE]
    >När ditt Azure Media Services-konto skapas läggs en **standard** slut punkt för direkt uppspelning till på ditt konto i **stoppat** tillstånd. Slutpunkten för direktuppspelning som du vill spela upp innehåll från måste ha tillståndet **Körs**.

7. Starta programmet när du är redo att börja strömma och arkivera.

8. Som alternativ kan den direktsända kodaren få signal om att starta en annons. Annonsen infogas i utdataströmmen.

9. Stoppa programmet när du vill stoppa strömningen och arkiveringen av händelsen.

10. Ta bort programmet (och eventuellt ta bort till gången).     

## <a name="description-of-a-channel-and-its-related-components"></a><a id="channel"></a>Beskrivning av en kanal och dess relaterade komponenter
### <a name="channel-input-ingest-configurations"></a><a id="channel_input"></a>Konfigurationer för kanal inmatning (inmatning)
#### <a name="ingest-streaming-protocol"></a><a id="ingest_protocols"></a>Hämta strömnings protokoll
Media Services har stöd för att mata in direktsänd feeds med hjälp av fragmenterade MP4-och multi-bit mängd med flera bit hastigheter som direkt uppspelnings protokoll. När RTMP inmatnings protokollet väljs, skapas två inmatnings slut punkter för kanalen:

* **Primär URL**: anger den fullständigt kvalificerade URL: en för kanalens primära RTMP-inmatnings slut punkt.
* **Sekundär URL** (valfritt): anger den fullständigt kvalificerade URL: en för kanalens sekundära RTMP-inmatnings slut punkt.

Använd den sekundära URL: en om du vill förbättra din inmatnings Ströms tålighet och fel tolerans (samt kodarens redundans och fel tolerans), särskilt i följande scenarier:

- Single Encoder-dubbel-push till både primära och sekundära URL: er:

    Det huvudsakliga syftet med det här scenariot är att ge mer återhämtning till nätverks fluktuationer och darr. Vissa RTMP-kodare hanterar inte nätverks från kopplings källa. När en nätverks från koppling sker kan en kodare stoppa kodningen och sedan inte skicka de buffrade data när en åter anslutning sker. Detta leder till discontinuities och data förlust. Nätverks anslutningar kan inträffa på grund av ett dåligt nätverk eller underhåll på Azure-sidan. Primära/sekundära URL: er minskar nätverks problem och ger en kontrollerad uppgraderings process. Varje gång en schemalagd nätverks från koppling sker, hanterar Media Services de primära och sekundära anslutningarna och ger en fördröjd från koppling mellan de två. Kodare har tid att fortsätta att skicka data och återansluta igen. Anslutnings ordningen kan vara slumpmässig, men det kommer alltid att finnas en fördröjning mellan primära/sekundära eller sekundära/primära URL: er. I det här scenariot är kodaren fortfarande en enskild felpunkt.

- Flera kodare, där varje kodare skickar till en dedikerad punkt:

    Det här scenariot ger både kodare och inmatning av redundans. I det här scenariot pushar encoder1 till den primära URL: en och encoder2 push-meddelanden till den sekundära URL: en. När en kodare Miss lyckas kan den andra kodaren fortsätta att skicka data. Dataredundans kan underhållas eftersom Media Services inte kopplar från primära och sekundära URL: er på samma gång. Det här scenariot förutsätter att kodare synkroniseras och ger exakt samma data.  

- Flera kodare har dubbel-push-överföring till både primära och sekundära URL: er:

    I det här scenariot kan båda kodarna skicka data till både primära och sekundära URL: er. Detta ger den bästa Tillförlitlighets-och fel toleransen, samt dataredundans. Det här scenariot kan tolerera både kodnings problem och från kopplingar, även om en kodare slutar fungera. Det förutsätter att kodare synkroniseras och ger exakt samma data.  

Information om RTMP Live Encoder finns i [Azure Media Services RTMP-support och Live Encoder](https://go.microsoft.com/fwlink/?LinkId=532824).

#### <a name="ingest-urls-endpoints"></a>Inmatnings-URL: er (slut punkter)
En kanal tillhandahåller en inmatnings slut punkt (inmatnings-URL) som du anger i Live-kodaren, så att kodaren kan skicka strömmar till dina kanaler.   

Du kan hämta inmatnings-URL: er när du skapar kanalen. För att du ska kunna hämta dessa URL: er behöver kanalen inte vara i **körnings** läge. När du är redo att börja skicka data till kanalen måste kanalen vara i **Kör** tillstånd. När kanalen börjar mata in data kan du förhandsgranska data strömmen via URL: en för för hands versionen.

Du kan välja att mata in en fragmenterad MP4 (Smooth Streaming) Live Stream via en TLS-anslutning. Om du vill mata in via TLS måste du uppdatera inmatnings-URL: en till HTTPS. För närvarande kan du inte mata in RTMP över TLS.

#### <a name="keyframe-interval"></a><a id="keyframe_interval"></a>Intervall för nyckel intervall
När du använder en lokal Live-kodare för att generera data strömmar med flera bit hastigheter anger längden på bild gruppen (GOP) som används av den externa kodaren. När kanalen har tagit emot den inkommande data strömmen kan du leverera din Live Stream till klient uppspelnings program i något av följande format: Smooth Streaming, dynamisk anpassad direkt uppspelning via HTTP (streck) och HTTP Live Streaming (HLS). När du använder Live streaming packas HLS alltid dynamiskt. Som standard beräknar Media Services automatiskt förpacknings kvoten för HLS-segment (fragment per segment) baserat på det nyckel intervall som tas emot från Live-kodaren.

Följande tabell visar hur segmentets varaktighet beräknas:

| Intervall för nyckel intervall | HLS segment, förpacknings kvot (FragmentsPerSegment) | Exempel |
| --- | --- | --- |
| Mindre än eller lika med 3 sekunder |3:1 |Om KeyFrameInterval (eller GOP) är 2 sekunder är standard förhållandet för HLS-segment 3 till 1. Detta skapar ett 6-sekunders HLS-segment. |
| 3 till 5 sekunder |2:1 |Om KeyFrameInterval (eller GOP) är 4 sekunder är standard antalet för HLS segment 2 till 1. Detta skapar ett 8-sekunders HLS-segment. |
| Mer än 5 sekunder |1:1 |Om KeyFrameInterval (eller GOP) är 6 sekunder är standard kvoten för HLS-segment 1 till 1. Detta skapar ett 6-sekunders HLS-segment. |

Du kan ändra kvoten för fragment per segment genom att konfigurera kanalens utdata och ställa in FragmentsPerSegment på ChannelOutputHls.

Du kan också ändra värdet för nyckel Rute intervallet genom att ange egenskapen KeyFrameInterval på ChannelInput. Om du uttryckligen anger KeyFrameInterval beräknas HLS segmentets förpacknings kvot med hjälp av reglerna som beskrivits tidigare.  

Om du uttryckligen ställer in både KeyFrameInterval och FragmentsPerSegment, använder Media Services de värden som du har angett.

#### <a name="allowed-ip-addresses"></a>Tillåtna IP-adresser
Du kan definiera de IP-adresser som tillåts att publicera video till den här kanalen. En tillåten IP-adress kan anges som en av följande:

* En enda IP-adress (till exempel 10.0.0.1)
* Ett IP-intervall som använder en IP-adress och en CIDR-nätmask (till exempel 10.0.0.1/22)
* Ett IP-intervall som använder en IP-adress och en punktavgränsad decimal näts mask (till exempel 10.0.0.1 (255.255.252.0))

Om inga IP-adresser har angetts och det inte finns någon regel definition, tillåts ingen IP-adress. Skapa en regel för att tillåta IP-adresser och ange 0.0.0.0/0.

### <a name="channel-preview"></a>Kanal för hands version
#### <a name="preview-urls"></a>Förhandsgransknings-URL:er
Kanaler tillhandahåller en förhands gransknings slut punkt (för hands version) som du använder för att förhandsgranska och validera data strömmen innan ytterligare bearbetning och leverans.

Du kan hämta URL: en för för hands versionen när du skapar kanalen. För att du ska kunna hämta URL: en behöver kanalen inte vara i **körnings** läge. När kanalen börjar mata in data kan du förhandsgranska data strömmen.

För närvarande kan för hands versions strömmen endast levereras i formatet för fragmenterad MP4 (Smooth Streaming), oavsett vilken Indatatyp som angetts. Du kan använda [Smooth Streaming Health Monitor](https://playready.directtaps.net/smoothstreaming/) Player för att testa den smidiga data strömmen. Du kan också använda en spelare som finns i Azure Portal för att visa din data ström.

#### <a name="allowed-ip-addresses"></a>Tillåtna IP-adresser
Du kan definiera de IP-adresser som tillåts att ansluta till förhands gransknings slut punkten. Om inga IP-adresser anges tillåts alla IP-adresser. En tillåten IP-adress kan anges som en av följande:

* En enda IP-adress (till exempel 10.0.0.1)
* Ett IP-intervall som använder en IP-adress och en CIDR-nätmask (till exempel 10.0.0.1/22)
* Ett IP-intervall som använder en IP-adress och en punktavgränsad decimal näts mask (till exempel 10.0.0.1 (255.255.252.0))

### <a name="channel-output"></a>Kanalens utdata
Information om kanal utdata finns i avsnittet om [nyckel intervall](#keyframe_interval) .

### <a name="channel-managed-programs"></a>Program som hanteras med kanal
En kanal är associerad med program som du kan använda för att styra publicering och lagring av segment i en Live-dataström. Kanaler hanterar program. Kanal-och program relationen liknar traditionella medier, där en kanal har en konstant ström med innehåll och ett program är begränsad till en viss tids period på den kanalen.

Du kan ange det antal timmar som du vill behålla inspelat innehåll för programmet genom att ställa in längden för **Arkivfönster**. Det här värdet kan anges från minst 5 minuter till högst 25 timmar. Arkiv fönster längden anger också det maximala antalet tids klienter som kan söka bakåt i tiden från den nuvarande aktiva positionen. Program kan köras under den angivna tidsperioden men innehåll som understiger fönsterlängden ignoreras kontinuerligt. Värdet för den här egenskapen avgör också hur länge klientmanifesten kan växa.

Varje program är associerat med en till gång som lagrar det strömmade innehållet. En till gång mappas till en Block-Blob-behållare i Azure Storage-kontot och filerna i till gången lagras som blobbar i den behållaren. För att publicera programmet så att dina kunder kan visa data strömmen måste du skapa en OnDemand-positionerare för den associerade till gången. Du kan använda den här lokaliseraren för att skapa en strömnings-URL som du kan tillhandahålla till dina klienter.

En kanal har stöd för upp till tre program som körs samtidigt, så du kan skapa flera Arkiv för samma inkommande data ström. Du kan publicera och arkivera olika delar av en händelse vid behov. Anta till exempel att ditt företags behov är att arkivera 6 timmar av ett program, men endast för att sända ut de senaste 10 minuterna. För att åstadkomma detta måste du skapa två program som körs samtidigt. Ett program är inställt på att arkivera sex timmar av händelsen, men programmet publiceras inte. Det andra programmet är inställt på att arkivera i 10 minuter och det här programmet publiceras.

Du bör inte återanvända befintliga program för nya händelser. Skapa i stället ett nytt program för varje händelse. Starta programmet när du är redo att börja strömma och arkivera. Stoppa programmet när du vill stoppa strömningen och arkiveringen av händelsen.

Om du vill ta bort arkiverat innehåll, stoppa och ta bort programmet och ta sedan bort den associerade till gången. En till gång kan inte tas bort om ett program använder den. Programmet måste först tas bort.

Även efter att du har stoppat och tagit bort programmet kan användarna strömma ditt arkiverade innehåll som en video på begäran, tills du tar bort till gången. Om du vill behålla det arkiverade innehållet, men inte har det tillgängligt för strömning, tar du bort streaming-lokaliseraren.

## <a name="channel-states-and-billing"></a><a id="states"></a>Kanal tillstånd och fakturering
Möjliga värden för det aktuella läget för en kanal är:

* **Stoppad**: det här är den ursprungliga statusen för kanalen när den har skapats. I det här läget kan kanal egenskaperna uppdateras, men strömning är inte tillåtet.
* **Startar**: kanalen startas. Inga uppdateringar eller strömmande tillåts under det här läget. Om ett fel inträffar återgår kanalen till **stoppat** tillstånd.
* **Körs**: kanalen kan bearbeta Live-strömmar.
* **Stoppar**: kanalen stoppas. Inga uppdateringar eller strömmande tillåts under det här läget.
* **Tar bort**: kanalen tas bort. Inga uppdateringar eller strömmande tillåts under det här läget.

Följande tabell visar hur kanal tillstånd mappas till fakturerings läget.

| Kanal tillstånd | Portal GRÄNSSNITTs indikatorer | Debiteras? |
| --- | --- | --- |
| **Startar** |**Startar** |Nej (tillfälligt tillstånd) |
| **Körs** |**Redo** (inga program som körs)<p><p>eller<p>**Strömning** (minst ett program som körs) |Ja |
| **Stoppas** |**Stoppas** |Nej (tillfälligt tillstånd) |
| **Stoppad** |**Stoppad** |Inga |

## <a name="closed-captioning-and-ad-insertion"></a><a id="cc_and_ads"></a>Dold textning och annons infogning
Följande tabell visar vilka standarder som stöds för dold textning och annons infogning.

| Standard | Kommentarer |
| --- | --- |
| CEA-708 och EIA-608 (708/608) |CEA-708 och EIA-608 är stängda – bild texter standarder för USA och Kanada.<p><p>För närvarande stöds inte textning för tillfället om den har överförts i den kodade indataströmmen. Du måste använda en Live Media-kodare som kan infoga 608-eller 708-textning i den kodade data strömmen som skickas till Media Services. Media Services levererar innehållet med infogade under texter till dina användare. |
| TTML inuti. ISMT (Smooth Streaming text spår) |Med Media Services dynamisk paketering kan klienter strömma innehåll i något av följande format: bindestreck, HLS eller Smooth Streaming. Men om du matar in fragmenterad MP4 (Smooth Streaming) med under texter inuti. ISMT (Smooth Streaming text spår) kan du endast leverera data strömmen till Smooth Streaming klienter. |
| SCTE – 35 |SCTE – 35 är ett digitalt signal system som används för att stacka annonserings infogning. Underordnade mottagare använder signalen för att använda signalen för att använda en splice-annons till strömmen för den tilldelade tiden. SCTE-35 måste skickas som ett sparse-spår i indataströmmen.<p><p>För närvarande är det enda data Ströms format som stöds som bär AD-signaler fragmenterad MP4 (Smooth Streaming). Det enda utdataformat som stöds Smooth Streaming också. |

## <a name="considerations"></a><a id="considerations"></a>Överväganden
När du använder en lokal Live-kodare för att skicka en data ström med flera bit hastigheter till en kanal gäller följande begränsningar:

* Se till att du har tillräckligt med Internet anslutning för att skicka data till inmatnings punkterna.
* Användning av en sekundär URL-adress kräver ytterligare bandbredd.
* Den inkommande data strömmen med flera bit hastigheter kan ha maximalt 10 video kvalitets nivåer (lager) och högst 5 ljud spår.
* Den högsta genomsnittliga bit hastigheten för någon av video kvalitets nivåerna bör vara lägre än 10 Mbit/s.
* Summan av de genomsnittliga bit hastigheterna för alla video-och ljud strömmar bör vara under 25 Mbit/s.
* Du kan inte ändra indataports protokoll när kanalen eller dess associerade program körs. Om du behöver olika protokoll får du skapa separata kanaler för varje indataprotokoll.
* Du kan mata in en enskild bit hastighet i din kanal. Men eftersom kanalen inte bearbetar strömmen får klient programmen även en enda bit ström. (Vi rekommenderar inte det här alternativet.)

Här följer andra överväganden om hur du arbetar med kanaler och relaterade komponenter:

* Varje gång du konfigurerar om Live-kodaren anropar du **återställnings** metoden i kanalen. Innan du återställer kanalen måste du stoppa programmet. Starta om programmet när du har återställt kanalen.

  > [!NOTE]
  > När du startar om programmet måste du associera det med en ny till gång och skapa en ny positionerare. 
  
* En kanal kan bara stoppas när den är i **körnings** läge och alla program i kanalen har stoppats.
* Som standard kan du bara lägga till fem kanaler till ditt Media Services-konto. Mer information finns i [kvoter och begränsningar](media-services-quotas-and-limitations.md).
* Du faktureras bara när din kanal är i **Kör** tillstånd. Mer information finns i avsnittet om [kanal tillstånd och fakturering](media-services-live-streaming-with-onprem-encoders.md#states) .

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="suggestions-and-feedback"></a>Förslag och feedback

[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Relaterade ämnen
[Rekommenderade lokala kodare](media-services-recommended-encoders.md)

[Specifikation för Azure Media Services fragmenterad MP4-bor](../media-services-fmp4-live-ingest-overview.md)

[Azure Media Services översikt och vanliga scenarier](media-services-overview.md)

[Media Services begrepp](media-services-concepts.md)

[live-overview]: ./media/media-services-manage-channels-overview/media-services-live-streaming-current.png
