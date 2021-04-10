---
title: Översikt över direkt uppspelning med Azure Media Services | Microsoft Docs
description: Den här artikeln ger en översikt över direkt uppspelning med Microsoft Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: fb63502e-914d-4c1f-853c-4a7831bb08e8
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 195333f5f06145a1dfa84f0de2c5984190e3d7ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103014833"
---
# <a name="overview-of-live-streaming-using-media-services"></a>Översikt över direkt uppspelning med Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen [Media Services v3](../latest/index.yml). Se även [vägledning för migrering från v2 till v3](../latest/migrate-v-2-v-3-migration-introduction.md)

## <a name="overview"></a>Översikt

När du levererar direkt uppspelnings händelser med Azure Media Services följande komponenter ofta inblandade:

* En kamera som används för att sända en händelse.
* En live-videokodare som konverterar signaler från kameran till dataströmmar som skickas till en tjänst för liveuppspelning.

    Du kan också välja flera kodare som synkroniserar tiden live. För vissa viktiga live-händelser som kräver mycket hög tillgänglighet och kvalitet rekommenderar vi att du använder redundanta aktiv/aktiv-kodare med tidssynkronisering för problemfri sömlös redundans utan att förlora data.
* En liveuppspelningstjänst som ger dig möjlighet att göra följande:

  * infoga innehållet via olika protokoll för liveuppspelning (till exempel RTMP eller Smooth Streaming)
  * (valfritt) koda strömmen till ström med anpassningsbar bithastighet
  * förhandsgranska din liveuppspelning
  * spela in och lagra det infogade innehållet för att kunna strömma senare (Video-on-Demand)
  * leverera innehållet via vanliga strömningsprotokoll (t.ex. MPEG DASH, Smooth, HLS) direkt till dina kunder eller till ett nätverk för innehållsleverans för vidare distribution.

**Microsoft Azure Media Services** (AMS) ger möjligheten att infoga, koda, förhandsgranska, lagra och leverera liveströmmat innehåll.

Med Media Services kan du dra nytta av [dynamisk paketering](media-services-dynamic-packaging-overview.md), vilket gör att du kan skicka in dina Live-STRÖMMAR i MPEG-streck, HLS och Smooth Streaming format från det bidrags flöde som skickas till tjänsten. Dina användare kan spela upp direktsänd ström med alla HLS-, tank-eller Smooth Streaming-kompatibla spelare. Du kan använda Azure Media Player i dina webb-eller mobil program för att leverera data strömmen i något av dessa protokoll.

> [!NOTE]
> Med början den 12 maj 2018 kommer Live Channels inte längre att ha stöd för RTP/MPEG-2 Transport Stream-inmatnings protokoll. Migrera från RTP/MPEG-2 till RTMP eller fragmented MP4 (Smooth Streaming) insugnings protokoll.

## <a name="streaming-endpoints-channels-programs"></a>Slut punkter för direkt uppspelning, kanaler, program

I Azure Media Services hanterar **kanaler**, **program** och **strömningsslutpunkter** alla funktioner för liveuppspelning, inklusive infogande, formatering, DVR, säkerhet, skalbarhet och redundans.

En **kanal** representerar en pipeline för bearbetning av liveuppspelningsinnehåll. En kanal kan ta emot en live-indataström på följande sätt:

* En lokal livekodare skickar **RTMP** eller **Smooth Streaming** med flera bithastigheter (fragmenterad MP4) till den kanal som är konfigurerad för **direkt** leverans. Den **direkta** leveransen är när de infogade strömmarna passerar genom **kanalerna** utan vidare bearbetning. Du kan använda följande Live-kodare som utvärderar multi-bitrs Smooth Streaming: MediaExcel, Ateme, Föreställing Communications, Envivio, Cisco och grundämne. Följande Live-kodare utdata RTMP: multistream Wirecast, Haivision, Teradek Encoder.  En livekodare kan även skicka en ström med en enda bithastighet till en kanal som inte har aktiverats för Live Encoding, men det rekommenderas inte. På begäran levererar Media Services strömmen till kunder.

  > [!NOTE]
  > Genomströmningsmetoden är det mest ekonomiska sättet för liveuppspelning när du utför flera händelser under en längre tid och du redan har investerat i lokala kodare. Se [prisuppgifter](https://azure.microsoft.com/pricing/details/media-services/).
  > 
  > 
* En lokal Live-kodare skickar en data ström med en bit hastighet till den kanal som är aktive rad för att utföra direktsänd kodning med Media Services i något av följande format: RTMP eller Smooth Streaming (fragmenterad MP4). Följande Live-kodare med RTMP-utdata är kända för att fungera med kanaler av den här typen: Wirecast för strömning. Kanalen utför sedan Live Encoding av strömmen med en enda bithastighet till en video-ström med flera bithastigheter (anpassningsbar). På begäran levererar Media Services strömmen till kunder.

Från och med Media Services 2,10-versionen när du skapar en kanal, kan du ange i vilket sätt du vill att din kanal ska ta emot indataströmmen och om du vill att kanalen ska kunna använda direktsänd kodning av data strömmen. Du kan välja mellan två alternativ:

* **Ingen** (genom strömning) – Ange det här värdet, om du planerar att använda en lokal Live-kodare som ska mata ut data strömmar med flera bit hastigheter (en direkt uppspelning). I det här fallet skickas inkommande data ström till utdata utan kodning. Detta är beteendet för en kanal före 2,10-versionen.  
* **Standard** – Välj det här värdet om du planerar att använda Media Services för att koda en Live-dataström med en bit hastighet till en data ström med flera bit hastigheter. Den här metoden är mer ekonomisk för att skala upp snabbt för infrekventa händelser. Tänk på att det finns en fakturerings påverkan för Live encoding och kom ihåg att om du lämnar en Live encoding-kanal i läget "körs" debiteras fakturerings avgifter.  Vi rekommenderar att du omedelbart stoppar dina kanaler som körs när din Live streaming-händelse är klar för att undvika extra Tim debiteringar.

## <a name="comparison-of-channel-types"></a>Jämförelse av kanal typer

Följande tabell innehåller en guide för att jämföra de två kanal typer som stöds i Media Services

| Funktion | Direkt kanal | Standard kanal |
| --- | --- | --- |
| Inmatade enstaka bit hastighet kodas till flera bit hastigheter i molnet |Inga |Ja |
| Högsta upplösning, antal lager |1080p, 8 lager, 60 + fps |720p, 6 lager, 30 fps |
| Protokoll för indataport |RTMP, Smooth Streaming |RTMP, Smooth Streaming |
| Pris |Se [sidan med priser](https://azure.microsoft.com/pricing/details/media-services/) och klicka på fliken "live video" |Se [sidan med priser](https://azure.microsoft.com/pricing/details/media-services/) |
| Maximal kör tid |runt |8 timmar |
| Stöd för att infoga mellanliggande |Inga |Ja |
| Stöd för AD-signalering |Inga |Ja |
| Pass-through CEA 608/708-textning |Ja |Ja |
| Stöd för icke-uniform GOPs |Ja |Nej – indatamängden måste vara fast 2sec GOPs |
| Stöd för variabla bild Rute frekvens inmatade |Ja |Nej – indatatyper måste vara fasta bild hastigheter.<br/>Mindre variationer tolereras, till exempel vid hög rörelse i bakgrunden. Men kodare kan inte släppa till 10 bild rutor/SEK. |
| Shutoff av kanaler när inmatnings flöde förloras |Inga |Efter 12 timmar, om inget program körs |

## <a name="working-with-channels-that-receive-multi-bitrate-live-stream-from-on-premises-encoders-pass-through"></a>Arbeta med kanaler som tar emot liveström med flera bithastigheter från lokala kodare (genomströmning)

I följande diagram visas de huvudsakliga delarna i AMS-plattformen som ingår i arbetsflödet **Genomströmning**.

![Diagram som visar de viktigaste delarna av en M S-plattform för "direkt"-arbets flödet.](./media/media-services-live-streaming-workflow/media-services-live-streaming-current.png)

Mer information finns i [Arbeta med kanaler som tar emot liveström med flera bithastigheter från lokala kodare](media-services-live-streaming-with-onprem-encoders.md).

## <a name="working-with-channels-that-are-enabled-to-perform-live-encoding-with-azure-media-services"></a>Arbeta med kanaler som är aktiverade för att utföra Live Encoding med Azure Media Services

I följande diagram visas de huvudsakliga delarna i AMS-plattformen som ingår i arbetsflödet för liveuppspelning där en kanal är aktiverad för att utföra Live Encoding med Media Services.

![Live-arbetsflöde](./media/media-services-live-streaming-workflow/media-services-live-streaming-new.png)

Mer information finns i [Arbeta med kanaler som är aktiverade för att utföra Live Encoding med Azure Media Services](media-services-manage-live-encoder-enabled-channels.md).

## <a name="description-of-a-channel-and-its-related-components"></a>Beskrivning av en kanal och dess relaterade komponenter

### <a name="channel"></a>Kanal

I Media Services är [kanal](/rest/api/media/operations/channel)s ansvarig för bearbetning av direktsänd strömmande innehåll. En kanal tillhandahåller en inmatnings slut punkt (inmatnings-URL) som du sedan anger till en Live-kodare. Kanalen tar emot direktsända indata strömmar från direktsänd kodare och gör den tillgänglig för strömning via en eller flera strömnings slut punkter. Kanaler tillhandahåller också en förhands gransknings slut punkt (för hands version) som du använder för att förhandsgranska och validera data strömmen innan ytterligare bearbetning och leverans.

Du kan hämta inmatnings-URL: en och URL: en för för hands versionen när du skapar kanalen. För att hämta dessa URL: er behöver kanalen inte vara i Start läge. När du är redo att börja skicka data från en Live-kodare till kanalen måste du starta kanalen. När Live-kodaren börjar mata in data kan du förhandsgranska data strömmen.

Varje Media Services konto kan innehålla flera kanaler, flera program och flera strömnings slut punkter. Beroende på bandbredds-och säkerhets behoven kan StreamingEndpoint-tjänster vara dedikerade till en eller flera kanaler. Alla StreamingEndpoint kan hämta från vilken kanal som helst.

När du skapar en kanal kan du ange tillåtna IP-adresser i något av följande format: IpV4-adress med 4 nummer, CIDR-adressintervall.

### <a name="program"></a>Program
Ett [program](/rest/api/media/operations/program) gör att du kan styra publicering och lagring av segment i en Live-dataström. Kanaler hanterar program. Relationen mellan kanal och program liknar den för traditionella media där en kanal har en konstant ström av innehåll och ett program är begränsat till en viss tidsinställd händelse på kanalen.
Du kan ange hur många timmar du vill behålla det inspelade innehållet för programmet genom att ange egenskapen **ArchiveWindowLength** . Det här värdet kan anges från minst 5 minuter till högst 25 timmar.

ArchiveWindowLength anger också den maximala tid som klienter kan söka bakåt i tiden från den nuvarande aktiva positionen. Program kan köras under den angivna tidsperioden men innehåll som understiger fönsterlängden ignoreras kontinuerligt. Värdet för den här egenskapen avgör också hur länge klient manifesten kan växa.

Varje program är associerat med en tillgång. För att publicera programmet måste du skapa en positionerare för den associerade till gången. Med den här lokaliseraren kan du skapa en strömnings-URL som du kan tillhandahålla till dina klienter.

En kanal har stöd för upp till tre program som körs samtidigt så du kan skapa flera arkiv för samma inkommande dataström. På så sätt kan du publicera och arkivera olika delar av en händelse efter behov. Ditt verksamhetsbehov kan till exempel vara att arkivera 6 timmar av ett program, men bara sända 10 minuter. För att åstadkomma detta måste du skapa två program som körs samtidigt. Ett program ställs in för att arkivera 6 timmar av händelsen, men programmet publiceras inte. Det andra programmet ställs in för att arkivera i 10 minuter och det här programmet publiceras.

## <a name="billing-implications"></a>Debiterings konsekvenser
En kanal börjar fakturera så fort dess tillstånds över gångar till "körs" via API: et.  

Följande tabell visar hur kanal tillstånd mappas till fakturerings tillstånd i API och Azure Portal. Observera att tillstånden skiljer sig något från API: et och portalens gränssnitt. När en kanal är i läget "körs" via API eller i läget "Ready" eller "streaming" i Azure Portal, kommer faktureringen att aktive ras.

Om du vill stoppa kanalen från faktureringen måste du stoppa kanalen via API: et eller i Azure Portal.
Du ansvarar för att stoppa kanalerna när du är färdig med kanalen. Om du inte stoppar kanalen leder det till fortsatt fakturering.

> [!NOTE]
> När du arbetar med standard kanaler kommer AMS automatiskt att shutoff alla kanaler som fortfarande befinner sig i "kör" tillstånd 12 timmar efter att inmatnings flödet förloras och det inte finns några program som körs. Men du kommer fortfarande att faktureras för den tidpunkt då kanalen kördes.
>
>

### <a name="channel-states-and-how-they-map-to-the-billing-mode"></a><a id="states"></a>Kanal tillstånd och hur de mappas till fakturerings läget
Aktuell status för en kanal. Möjliga värden är:

* **Stoppades**. Det här är den ursprungliga statusen för kanalen när den har skapats (om Autostart har marker ATS i portalen). Ingen fakturering sker i det här läget. I det här läget kan kanal egenskaperna uppdateras, men strömning är inte tillåtet.
* **Startar**. Kanalen startas. Ingen fakturering sker i det här läget. Inga uppdateringar eller strömmande tillåts under det här läget. Om ett fel inträffar återgår kanalen till stoppat tillstånd.
* **Körs**. Kanalen kan bearbeta Live-strömmar. Nu faktureras användning. Du måste stoppa kanalen för att förhindra ytterligare fakturering.
* **Stoppar**. Kanalen stoppas. Ingen fakturering sker i det här tillfälliga läget. Inga uppdateringar eller strömmande tillåts under det här läget.
* **Tar bort**. Kanalen tas bort. Ingen fakturering sker i det här tillfälliga läget. Inga uppdateringar eller strömmande tillåts under det här läget.

Följande tabell visar hur kanal tillstånd mappas till fakturerings läget.

| Kanal tillstånd | Portal GRÄNSSNITTs indikatorer | Faktureras det? |
| --- | --- | --- |
| Startar |Startar |Nej (tillfälligt tillstånd) |
| Körs |Redo (inga program som körs)<br/>eller<br/>Strömning (minst ett program som körs) |JA |
| Stoppas |Stoppas |Nej (tillfälligt tillstånd) |
| Stoppad |Stoppad |Inga |

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Relaterade ämnen
[Azure Media Services-specifikation för fragmenterad MP4 Live](../media-services-fmp4-live-ingest-overview.md)

[Arbeta med kanaler som är aktiverade för att utföra Live Encoding med Azure Media Services](media-services-manage-live-encoder-enabled-channels.md)

[Arbeta med kanaler som tar emot liveuppspelningar med flera bithastigheter från lokala kodare](media-services-live-streaming-with-onprem-encoders.md)

[Kvoter och begränsningar](media-services-quotas-and-limitations.md).  

[Media Services begrepp](media-services-concepts.md)
