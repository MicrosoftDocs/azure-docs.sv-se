---
title: Azure Media Servicess specifikation för fragmenterad MP4 Live Microsoft Docs
description: I den här specifikationen beskrivs protokoll och format för fragmenterad MP4-baserad direkt uppspelning av Azure Media Services. Det här dokumentet innehåller också metod tips för att skapa mycket redundanta och robusta direktsända inmatnings mekanismer.
services: media-services
documentationcenter: ''
author: cenkdin
manager: femila
editor: ''
ms.assetid: 43fac263-a5ea-44af-8dd5-cc88e423b4de
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: juliako
ms.openlocfilehash: 7323ae611431e1d91fd1a8471914be388fcc4712
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92019519"
---
# <a name="azure-media-services-fragmented-mp4-live-ingest-specification"></a>Azure Media Services-specifikation för fragmenterad MP4 Live 

I den här specifikationen beskrivs protokoll och format för fragmenterad MP4-baserad direkt uppspelning av Azure Media Services. Media Services tillhandahåller en live streaming-tjänst som kunder kan använda för att strömma direktsända händelser och skicka innehåll i real tid med hjälp av Azure som moln plattform. Det här dokumentet innehåller också metod tips för att skapa mycket redundanta och robusta direktsända inmatnings mekanismer.

## <a name="1-conformance-notation"></a>1. överensstämmelse notation
Nyckelorden "MUST", "" får inte, "" krävs "," "ska inte," "ska inte," "ska inte," "bör inte," "rekommenderas," ",", "och" valfritt "i det här dokumentet tolkas som de beskrivs i RFC 2119.

## <a name="2-service-diagram"></a>2. tjänst diagram
Följande diagram visar den övergripande arkitekturen i Live streaming service i Media Services:

1. En Live Encoder pushar direktsänd feeds till kanaler som skapas och tillhandahålls via Azure Media Services SDK.
1. Kanaler, program och slut punkter för direkt uppspelning i Media Services hantera alla funktioner för direkt uppspelning, inklusive inmatning, formatering, moln-DVR, säkerhet, skalbarhet och redundans.
1. Kunder kan också välja att distribuera ett Azure Content Delivery Network-lager mellan slut punkten för direkt uppspelning och klient slut punkter.
1. Klient slut punkter strömmas från slut punkten för direkt uppspelning med hjälp av HTTP-anpassade protokoll för direkt uppspelning. Exempel på detta är Microsoft Smooth Streaming, dynamisk adaptiv strömning via HTTP (bindestreck eller MPEG-streck) och Apple HTTP Live Streaming (HLS).

![inmatnings flöde][image1]

## <a name="3-bitstream-format--iso-14496-12-fragmented-mp4"></a>3. Bitstream-format – ISO 14496-12-fragmenterad MP4
Överförings formatet för direktsänd strömning som beskrivs i det här dokumentet baseras på [ISO-14496-12]. En detaljerad förklaring av det fragmenterade MP4-formatet och tillägg för både video-on-demand-filer och Live streaming-inmatning finns i [[MS-SSTR]](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251).

### <a name="live-ingest-format-definitions"></a>Format definitioner för Live-intag
I följande lista beskrivs särskilda format definitioner som gäller för Live-inmatning i Azure Media Services:

1. Rutorna **ftyp**, **Live Server-manifest** och **Moov** måste skickas med varje begäran (http post). De här rutorna måste skickas i början av data strömmen och varje gång kodaren måste återanslutas för att återuppta strömningen. Mer information finns i avsnitt 6 i [1].
1. Avsnitt 3.3.2 i [1] definierar en valfri ruta med namnet **StreamManifestBox** för direktsänd inmatning. På grund av cirkulations logiken för Azure Load Balancer är den här rutan föråldrad. Rutan bör inte finnas när du matar in till Media Services. Om den här rutan är tillgänglig ignorerar Media Services tyst.
1. Rutan **TrackFragmentExtendedHeaderBox** som definieras i 3.2.3.2 i [1] måste finnas för varje fragment.
1. Version 2 av rutan **TRACKFRAGMENTEXTENDEDHEADERBOX** ska användas för att generera medie segment som har identiska URL: er i flera data Center. Fältet fragment index krävs för redundans av över-datacenter i indexbaserade strömnings format, till exempel Apple-HLS och indexbaserade MPEG-streck. Om du vill aktivera redundans i flera data Center måste fragment-indexet synkroniseras över flera kodare och ökas med 1 för varje efterföljande medie fragment, även om omstarter eller fel uppstår i en kodare.
1. Avsnitt 3.3.6 i [1] definierar en ruta med namnet **MovieFragmentRandomAccessBox** (**MFRA**) som kan skickas i slutet av Live-inmatningen för att ange slut punkt för ström (EOS) till kanalen. På grund av inmatnings logiken för Media Services är användningen av EOS föråldrad och **mfra** -rutan för direkt inmatning ska inte skickas. Om den skickas ignoreras Media Services tyst. Vi rekommenderar att du använder [kanal återställning](/rest/api/media/operations/channel#reset_channels)för att återställa inmatnings platsens tillstånd. Vi rekommenderar också att du använder [program stopp](/rest/api/media/operations/program#stop_programs) för att avsluta en presentation och strömma.
1. Längden på MP4-fragment bör vara konstant, för att minska storleken på klient manifesten. En varaktighet för en konstant MP4-fragment förbättrar också klient hämtnings heuristiken genom användning av upprepade taggar. Varaktigheten kan variera för att kompensera för ramar som inte är heltals nivåer.
1. Längden på MP4-fragment måste vara mellan cirka 2 och 6 sekunder.
1. Tidsstämplar för MP4-fragment och index (**TrackFragmentExtendedHeaderBox** `fragment_ absolute_ time` och `fragment_index` ) bör komma i stigande ordning. Även om Media Services är elastiskt till dubbletter av fragment, har det begränsad möjlighet att ändra ordning på fragmenten enligt medie tids linjen.

## <a name="4-protocol-format--http"></a>4. protokoll format – HTTP
ISO-fragmenterad MP4-baserad Live-inmatning för Media Services använder en standard-lång HTTP POST-begäran för att överföra kodade medie data som är förpackade i fragmenterat MP4-format till tjänsten. Varje HTTP POST skickar ett fullständigt fragmenterat MP4-Bitstream ("Stream"), som börjar från början med sidhuvud-rutor (**ftyp**, **Live Server manifest Box** och **Moov** ) och fortsätter med en sekvens med fragment (**moof** och **mdat** -rutor). URL-syntax för HTTP POST-begäran finns i avsnitt 9,2 i [1]. Ett exempel på POST-URL: en är: 

`http://customer.channel.mediaservices.windows.net/ingest.isml/streams(720p)`

### <a name="requirements"></a>Krav
Här följer de detaljerade kraven:

1. Kodaren ska starta sändningen genom att skicka en HTTP POST-begäran med en tom "brödtext" (noll innehålls längd) genom att använda samma inmatnings-URL. Detta kan hjälpa kodaren att snabbt identifiera om slut punkten för direkt inmatningen är giltig och om det finns någon autentisering eller andra villkor som krävs. Per HTTP-protokoll kan servern inte skicka tillbaka ett HTTP-svar förrän hela begäran, inklusive INLÄGGs texten, tas emot. Med tanke på långvarig natur som en Live-händelse, utan det här steget, kanske kodaren inte kan upptäcka några fel förrän den har slutfört sändningen av alla data.
1. Kodaren måste hantera eventuella fel eller autentiseringsbegäranden på grund av (1). Om (1) lyckas med ett 200-svar fortsätter du.
1. Kodaren måste starta en ny HTTP POST-begäran med den fragmenterade MP4-strömmen. Nytto lasten måste börja med rubrik rutorna följt av fragment. Observera att rutorna **ftyp**, **Live Server manifest** och **Moov** (i den här ordningen) måste skickas med varje begäran, även om kodaren måste återansluta på grund av att den tidigare begäran avbröts innan strömmens slut. 
1. Kodaren måste använda Chunked Transfer Encoding för överföring, eftersom det är omöjligt att förutsäga hela innehålls längden för Live-händelsen.
1. När händelsen är över, efter att det sista fragmentet har skickats, måste kodaren avsluta den segmenterade överförings meddelande sekvensen (de flesta HTTP-klienter hanterar den automatiskt). Kodaren måste vänta på att tjänsten returnerar slut svars koden och sedan avsluta anslutningen. 
1. Kodaren får inte använda `Events()` Substantiv enligt beskrivningen i 9,2 i [1] för direkt inmatning i Media Services.
1. Om HTTP POST-begäran upphör eller tar slut under ett TCP-fel innan data strömmen avslutas, måste kodaren utfärda en ny POST-begäran med en ny anslutning och följa föregående krav. Dessutom måste kodaren skicka de föregående två MP4-fragmenten för varje spår i strömmen och återuppta utan att införa en avvikelse i medie tids linjen. Att skicka de senaste två MP4-fragmenten för varje spår ser till att det inte finns någon data förlust. Med andra ord, om en data ström innehåller både ett ljud och ett video spår och den aktuella POST-begäran Miss lyckas, måste kodaren återansluta och skicka de sista två fragmenten för ljud spåret, som tidigare har skickats, och de sista två fragmenten för video spåret, som tidigare har skickats, för att säkerställa att det inte finns någon data förlust. Kodaren måste ha en "Forward"-buffert för medie fragment, som den återsänder när den återansluter.

## <a name="5-timescale"></a>5. tids skala
[[MS-SSTR]](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251) beskriver användningen av tids skala för **SmoothStreamingMedia** (section 2.2.2.1), **StreamElement** (avsnitt 2.2.2.3), **StreamFragmentElement** (section 2.2.2.6) och **LiveSMIL** (section 2.2.7.3.1). Om tids Skale svärdet inte finns används standardvärdet 10 000 000 (10 MHz). Även om Smooth Streaming format specifikationen inte blockerar användning av andra tids skals värden, använder de flesta kodarens standardvärden (10 MHz) för att generera Smooth Streaming inmatnings data. På grund av funktionen [Azure Media Dynamic packning](./previous/media-services-dynamic-packaging-overview.md) rekommenderar vi att du använder en 90-kHz-tidsskala för video strömmar och 44,1 khz eller 48,1 kHz för ljud strömmar. Om olika tids skaliga värden används för olika strömmar måste data strömmen på ström nivån skickas. Mer information finns i [[MS-SSTR]](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251).     

## <a name="6-definition-of-stream"></a>6. definition av "Stream"
Stream är den grundläggande enhet som används i Live-inmatningen för att skapa live-presentationer, hantering av strömmande redundans och redundans scenarier. Stream definieras som en unik, fragmenterad MP4-Bitstream som kan innehålla ett enda spår eller flera spår. En fullständig live-presentation kan innehålla en eller flera strömmar, beroende på konfigurationen av Live-kodarna. I följande exempel visas olika alternativ för att använda strömmar för att skapa en fullständig live-presentation.

**Exempel:** 

En kund vill skapa en live streaming-presentation som innehåller följande bit hastigheter för ljud/video:

Video – 3000 kbps, 1500 kbit/s, 750 kbit/s

Ljud – 128 kbps

### <a name="option-1-all-tracks-in-one-stream"></a>Alternativ 1: alla spår i en ström
I det här alternativet genererar en enskild kodare alla ljud-/video spår och bunta dem sedan till en fragmenterad MP4-Bitstream. Den fragmenterade MP4-Bitstream skickas sedan via en enda HTTP POST-anslutning. I det här exemplet finns det bara en ström för den här Live-presentationen.

![Strömmar – ett spår][image2]

### <a name="option-2-each-track-in-a-separate-stream"></a>Alternativ 2: varje spår i en separat ström
I det här alternativet placerar kodaren ett spår i varje fragment MP4-Bitstream och publicerar sedan alla strömmar över separata HTTP-anslutningar. Detta kan göras med en kodare eller med flera kodare. Live-inmatningen ser den här Live-presentationen som består av fyra strömmar.

![Strömmar – separata spår][image3]

### <a name="option-3-bundle-audio-track-with-the-lowest-bitrate-video-track-into-one-stream"></a>Alternativ 3: paketera ljud spår med den lägsta bit hastigheten för video spår till en ström
I det här alternativet väljer kunden att paketera ljud spåret med video spår med lägsta bit hastighet i en fragment MP4-Bitstream och lämna de andra två video spåren som separata strömmar. 

![Strömmar – ljud-och video spår][image4]

### <a name="summary"></a>Sammanfattning
Detta är inte en fullständig lista över alla möjliga inmatnings alternativ för det här exemplet. I själva verket stöds gruppering av spår i strömmar av direkt inmatning. Kunder och kodare leverantörer kan välja sina egna implementeringar baserat på teknik komplexitet, kodarens kapacitet och redundans och redundans. I de flesta fall finns det dock bara ett ljud spår för hela Live-presentationen. Det är därför viktigt att se till att healthiness för den insugnings ström som innehåller ljud spåret. Detta innebär ofta att placera ljud spåret i en egen ström (som i alternativ 2) eller att strömma det med video spår med låg bit hastighet (som i alternativ 3). För bättre redundans och fel tolerans kan du också skicka samma ljud spår i två olika strömmar (alternativ 2 med redundanta ljud spår) eller strömma ljud spåret med minst två av de lägsta bit hastighets video spåren (alternativ 3 med ljud som paketerats i minst två video strömmar) är starkt rekommenderat för direktsänd inmatning i Media Services.

## <a name="7-service-failover"></a>7. redundans för tjänst
Med tanke på Live-strömningens natur är stöd för felfri redundans avgörande för att säkerställa tillgängligheten för tjänsten. Media Services har utformats för att hantera olika typer av fel, inklusive nätverks fel, Server fel och lagrings problem. När de används tillsammans med korrekt redundansrelation från Live Encoder-sidan, kan kunder få en mycket tillförlitlig Live streaming-tjänst från molnet.

I det här avsnittet diskuterar vi scenarier för tjänst växling. I det här fallet uppstår felet någonstans i tjänsten och den manifesterar sig själv som ett nätverks fel. Här följer några rekommendationer för kodarens implementering för att hantera redundansväxling av tjänster:

1. Använd en tids gräns på 10 sekunder för att upprätta TCP-anslutningen. Om ett försök att upprätta anslutningen tar längre tid än 10 sekunder avbryter du åtgärden och försöker igen. 
1. Använd en kort tids gräns för att skicka meddelande segment för HTTP-begäran. Om målets MP4-fragment är N sekunder, använder du en sändnings-timeout mellan N och 2 N sekunder; om till exempel längden på MP4-fragment är 6 sekunder, använder du en tids gräns på 6 till 12 sekunder. Om tids gränsen uppnåddes, återställer du anslutningen, öppnar en ny anslutning och återupptar Stream-inmatningen på den nya anslutningen. 
1. Underhålla en rullande buffert som har de två sista fragmenten för varje spår som har skickats och skickas fullständigt till tjänsten.  Om HTTP POST-begäran för en data ström avbryts eller avbryts innan strömmen bryts, öppna en ny anslutning och påbörja en annan HTTP POST-begäran, skicka Stream-huvuden igen, skicka om de senaste två fragmenten för varje spår och återuppta strömmen utan att införa en avvikelse i medie tids linjen. Detta minskar risken för data förlust.
1. Vi rekommenderar att kodaren inte begränsar antalet försök att upprätta en anslutning eller återuppta direkt uppspelning när ett TCP-fel uppstår.
1. Efter ett TCP-fel:
  
    a. Den aktuella anslutningen måste stängas och en ny anslutning måste skapas för en ny HTTP POST-begäran.

    b. Den nya HTTP POST-URL: en måste vara samma som den första POST-URL: en.
  
    c. Det nya HTTP-inlägget måste innehålla data Ströms rubriker (**ftyp**, **Live Server manifest Box** och **Moov** rutor) som är identiska med data ström rubrikerna i det första inlägget.
  
    d. De sista två fragmenten som skickats för varje spår måste skickas igen och streaming måste återupptas utan att det införs någon avvikelse i medie tids linjen. Tidsstämplar för MP4-fragment måste öka kontinuerligt, även över HTTP POST-begäranden.
1. Kodaren ska avsluta HTTP POST-begäran om data inte skickas enligt en hastighet som motsvarar MP4-Fragmentets varaktighet.  En HTTP POST-begäran som inte skickar data kan förhindra att Media Services snabbt kopplar från kodaren i händelse av en tjänst uppdatering. Därför bör HTTP POST för sparse-spår (AD-signal) vara kort livs längd och avslutas så snart den glesa fragmenten skickas.

## <a name="8-encoder-failover"></a>8. Encoder-redundans
Kodarens redundans är den andra typen av redundansväxling som måste åtgärdas för leverans från slut punkt till slut punkt för direkt uppspelning. I det här scenariot inträffar fel tillståndet på kodarens sida. 

![kodarens redundans][image5]

Följande förväntningar gäller från Live-inmatnings slut punkten när redundansväxlingen sker:

1. En ny kodare-instans ska skapas för att fortsätta strömningen, som illustreras i diagrammet (Stream for 3000k video, med streckad linje).
1. Den nya kodaren måste använda samma URL för HTTP POST-begäranden som den misslyckade instansen.
1. Den nya kodarens POST-begäran måste innehålla samma fragmenterade MP4-rubrik rutor som den misslyckade instansen.
1. Den nya kodaren måste synkroniseras korrekt med alla andra körnings kodare för samma live-presentation för att skapa synkroniserade ljud-och video exempel med justerade fragment-gränser.
1. Den nya data strömmen måste vara semantiskt likvärdig med den tidigare strömmen och utbytbara på rubrik-och fragment nivå.
1. Den nya kodaren bör försöka minimera data förlust. `fragment_absolute_time`Och- `fragment_index` MEDIE fragment bör öka från den punkt där Encoder senast stoppades. `fragment_absolute_time`Och `fragment_index` bör öka på ett kontinuerligt sätt, men det är tillåtet att införa en avvikelse vid behov. Media Services ignorerar fragment som redan har tagits emot och bearbetats, så det är bättre att fel på sidan om att skicka fragment igen än att införa discontinuities i medie tids linjen. 

## <a name="9-encoder-redundancy"></a>9. kodarens redundans
För vissa kritiska direktsända händelser som kräver ännu högre tillgänglighet och kvalitet på upplevelsen rekommenderar vi att du använder aktiva-aktiva redundanta kodare för att uppnå sömlös redundans utan data förlust.

![kodarens redundans][image6]

Som vi illustrerar i det här diagrammet skickar två grupper med kodare två kopior av varje data ström samtidigt till Live-tjänsten. Den här installationen stöds eftersom Media Services kan filtrera ut dubbletter av fragment baserat på Stream-ID och fragmentering av fragment. Den resulterande Live-strömmen och arkivet är en enda kopia av alla data strömmar som är bästa möjliga agg regering från de två källorna. Till exempel, i ett hypotetiskt extrema fall, så länge det finns en kodare (det behöver inte vara samma) som körs vid en viss tidpunkt för varje data ström, är den resulterande Live-strömmen från tjänsten kontinuerlig utan data förlust. 

Kraven för det här scenariot är nästan samma som kraven i "Encoder-redundansväxlingen"-fallet, med undantag för att den andra uppsättningen kodare körs samtidigt som de primära kodarna.

## <a name="10-service-redundancy"></a>10. redundans för tjänst
Ibland måste du ha en säkerhets kopia över flera regioner för att kunna hantera regionala haverier för en mycket redundant global distribution. Genom att utöka topologin "kodarens redundans" kan kunderna välja att ha en redundant tjänst distribution i en annan region som är ansluten till den andra uppsättningen kodare. Kunder kan också arbeta med en Content Delivery Network-Provider för att distribuera en global Traffic Manager framför de två tjänst distributionerna för att sömlöst dirigera klient trafiken. Kraven för kodare är desamma som i "kodarens redundans"-fall. Det enda undantaget är att den andra uppsättningen kodare måste peka till en annan Live-inmatnings slut punkt. Följande diagram visar den här konfigurationen:

![tjänstens redundans][image7]

## <a name="11-special-types-of-ingestion-formats"></a>11. särskilda typer av inmatnings format
I det här avsnittet beskrivs särskilda typer av direkt inmatnings format som är utformade för att hantera specifika scenarier.

### <a name="sparse-track"></a>Sparse-spår
När du levererar en Live-strömmande presentation med en omfattande klient upplevelse, är det ofta nödvändigt att överföra tidssynkroniserade händelser eller signalera in-band med huvud medie data. Ett exempel på detta är dynamisk infogning av AD Live AD. Den här typen av händelse signalering skiljer sig från vanlig direkt uppspelning av ljud/video på grund av dess sparse-natur. Med andra ord sker signal data normalt inte kontinuerligt och intervallet kan vara svårt att förutse. Begreppet renulled Track har utformats för att mata in och sända signal data på band.

Följande steg är en rekommenderad implementering för att mata in ett sparse-spår:

1. Skapa en separat fragmenterad MP4-Bitstream som bara innehåller null-optimerade spår, utan ljud-/video spår.
1. I **rutan Live Server manifest** , som definieras i avsnitt 6 i [1], använder du parametern *parentTrackName* för att ange namnet på det överordnade spåret. Mer information finns i avsnittet 4.2.1.2.1.2 i [1].
1. I **rutan Live Server-manifest** måste **manifestOutput** vara inställt på **True**.
1. Med tanke på signal händelsens sparse-typ rekommenderar vi följande:
   
    a. I början av Live-händelsen skickar kodaren de inledande rubrik rutorna till tjänsten, vilket gör att tjänsten kan registrera den sparse-spårningen i klient manifestet.
   
    b. Kodaren ska avsluta HTTP POST-begäran när data inte skickas. Ett långvarigt HTTP-inlägg som inte skickar data kan förhindra att Media Services snabbt kopplar från kodaren i händelse av en tjänst uppdatering eller Server omstart. I dessa fall blockeras medie servern tillfälligt i en mottagnings åtgärd på socketen.
   
    c. Under tiden som signalerar data inte är tillgängliga ska kodaren stänga HTTP POST-begäran. När POST-begäran är aktiv ska kodaren skicka data.

    d. När du skickar sparse-fragment kan kodaren ange en explicit Content-Length-rubrik, om den är tillgänglig.

    e. När du skickar sparse-fragment med en ny anslutning, ska kodaren börja skicka från rubrik rutorna följt av de nya fragmenten. Detta är för fall där redundansväxlingen sker mellan och den nya sparse-anslutningen upprättas till en ny server som inte har sett den sparse-spårningen tidigare.

    f. Det sparse-spårets fragment blir tillgängligt för klienten när motsvarande överordnade spårnings fragment som har ett lika eller större tidsstämpel-värde görs tillgängligt för klienten. Om till exempel det sparse-fragment som har en tidsstämpel på t = 1000, förväntas att efter att klienten ser "video" (förutsatt att namnet på den överordnade spårningen är "video") Fragmentets tidsstämpel 1000 eller senare kan det Ladda ned den sparse-filen t = 1000. Observera att den faktiska signalen kan användas för en annan plats i presentations tids linjen för det avsedda ändamålet. I det här exemplet är det möjligt att sparse-fragmentet t = 1000 har en XML-nyttolast, vilket är att lägga till en annons i en plats som är några sekunder senare.

    ex. Nytto lasten för uppdelade spår fragment kan vara i olika format (till exempel XML, text eller binärt), beroende på scenariot.

### <a name="redundant-audio-track"></a>Redundant ljud spår
I ett typiskt HTTP-anpassat direkt uppspelnings scenario (till exempel Smooth Streaming eller tank streck) finns det ofta bara ett ljud spår i hela presentationen. Till skillnad från video spår, som har flera kvalitets nivåer som klienten kan välja mellan, kan ljud spåret vara en enskild felpunkt om inmatningen av data strömmen som innehåller ljud spåret bryts. 

För att lösa det här problemet har Media Services stöd för direkt inmatning av redundanta ljud spår. Tanken är att samma ljud spår kan skickas flera gånger i olika strömmar. Även om tjänsten endast registrerar ljud spåret en gång i klient manifestet, kan det använda redundanta ljud spår som säkerhets kopior för att hämta ljudfragment om det finns problem med det primära ljud spåret. För att mata in redundanta ljud spår måste kodaren:

1. Skapa samma ljud spår i flera fragment MP4-bitstreams. De redundanta ljud spåren måste vara semantiskt likvärdiga, med samma fragment-tidsstämplar och vara utbytbara på rubrik-och fragment nivå.
1. Se till att "ljud"-posten i Live Server-manifestet (avsnitt 6 i [1]) är densamma för alla redundanta ljud spår.

Följande implementering rekommenderas för redundanta ljud spår:

1. Skicka varje unikt ljud spår i en ström. Skicka även en redundant ström för var och en av dessa ljud spårs strömmar, där den andra strömmen skiljer sig från den första endast av identifieraren i HTTP POST-URL: {Protocol}://{server adress}/{Publishing punkt Sök väg}/Streams ({Identifier}).
1. Använd separata strömmar för att skicka de två lägsta video bit hastigheterna. Var och en av dessa strömmar bör också innehålla en kopia av varje unikt ljud spår. Till exempel, när flera språk stöds, ska dessa strömmar innehålla ljud spår för varje språk.
1. Använd separata Server-instanser (kodare) för att koda och skicka de redundanta strömmar som anges i (1) och (2). 

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

[image1]: ./media/media-services-fmp4-live-ingest-overview/media-services-image1.png
[image2]: ./media/media-services-fmp4-live-ingest-overview/media-services-image2.png
[image3]: ./media/media-services-fmp4-live-ingest-overview/media-services-image3.png
[image4]: ./media/media-services-fmp4-live-ingest-overview/media-services-image4.png
[image5]: ./media/media-services-fmp4-live-ingest-overview/media-services-image5.png
[image6]: ./media/media-services-fmp4-live-ingest-overview/media-services-image6.png
[image7]: ./media/media-services-fmp4-live-ingest-overview/media-services-image7.png