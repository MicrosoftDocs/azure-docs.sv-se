---
title: Direkt uppspelning med Azure Media Services för att skapa data strömmar med flera bit hastigheter | Microsoft Docs
description: I det här avsnittet beskrivs hur du konfigurerar en kanal som tar emot en enda bit hastighets ström från en lokal kodare och sedan utför live encoding på en anpassad bit hastighets ström med Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 30ce6556-b0ff-46d8-a15d-5f10e4c360e2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: anilmur
ms.reviewer: juliako
ms.openlocfilehash: b9b4cd54375a13da95259e27da680255f785df45
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013218"
---
# <a name="live-streaming-using-azure-media-services-to-create-multi-bitrate-streams"></a>Liveuppspelning med Azure Media Services för att skapa dataströmmar med flera bithastigheter

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!NOTE]
> Med början den 12 maj 2018 kommer Live Channels inte längre att ha stöd för RTP/MPEG-2 Transport Stream-inmatnings protokoll. Migrera från RTP/MPEG-2 till RTMP eller fragmented MP4 (Smooth Streaming) insugnings protokoll.

## <a name="overview"></a>Översikt
I Azure Media Services (AMS) representerar en **kanal** en pipeline för bearbetning av direktsänd strömmande innehåll. En **kanal** tar emot direktsända indata strömmar på något av två sätt:

* En lokal Live-kodare skickar en data ström med en bit hastighet till den kanal som är aktive rad för att utföra direktsänd kodning med Media Services i något av följande format: RTMP eller Smooth Streaming (fragmenterad MP4). Kanalen utför sedan Live Encoding av strömmen med en enda bithastighet till en video-ström med flera bithastigheter (anpassningsbar). På begäran levererar Media Services strömmen till kunder.
* En lokal Live-kodare skickar en **RTMP** med flera bit hastigheter eller **Smooth Streaming** (fragmenterad MP4) till den kanal som inte är aktive rad för att utföra direktsänd kodning med AMS. De inmatade strömmarna passerar genom **kanal** s utan ytterligare bearbetning. Den här metoden kallas **genom strömning**. Du kan använda följande Live-kodare som utvärderar multi-bitrs Smooth Streaming: MediaExcel, Ateme, Föreställing Communications, Envivio, Cisco och grundämne. Följande Live-kodare utdata RTMP: [multistream Wirecast](media-services-configure-wirecast-live-encoder.md), Haivision, Teradek Encoder.  En livekodare kan även skicka en ström med en enda bithastighet till en kanal som inte har aktiverats för Live Encoding, men det rekommenderas inte. På begäran levererar Media Services strömmen till kunder.

  > [!NOTE]
  > Att använda en direkt metod är det mest ekonomiska sättet att göra Direktsänd strömning.
  > 
  > 

Från och med Media Services 2,10-versionen när du skapar en kanal, kan du ange i vilket sätt du vill att din kanal ska ta emot indataströmmen och om du vill att kanalen ska kunna använda direktsänd kodning av data strömmen. Du kan välja mellan två alternativ:

* **Ingen** – ange det här värdet, om du planerar att använda en lokal Live-kodare som ska mata ut data strömmar med flera bit hastigheter (en direkt uppspelning). I det här fallet skickas inkommande data ström till utdata utan kodning. Detta är beteendet för en kanal före 2,10-versionen.  Mer detaljerad information om hur du arbetar med kanaler av den här typen finns i [Direktsänd strömning med lokala kodare som skapar strömmar med flera bit](media-services-live-streaming-with-onprem-encoders.md)hastigheter.
* **Standard** – Välj det här värdet om du planerar att använda Media Services för att koda en Live-dataström med en bit hastighet till en data ström med flera bit hastigheter. Tänk på att det finns en fakturerings påverkan för Live encoding och kom ihåg att om du lämnar en Live encoding-kanal i läget "körs" debiteras fakturerings avgifter.  Vi rekommenderar att du omedelbart stoppar dina kanaler som körs när din Live streaming-händelse är klar för att undvika extra Tim debiteringar.

> [!NOTE]
> I det här avsnittet diskuteras attribut för kanaler som är aktiverade för att utföra Live encoding (**standard** kodnings typ). Information om hur du arbetar med kanaler som inte är aktiverade för att utföra direktsänd kodning finns i [Direktsänd strömning med lokala kodare som skapar strömmar med flera bit](media-services-live-streaming-with-onprem-encoders.md)hastigheter.
> 
> Se till att gå igenom avsnittet [överväganden](media-services-manage-live-encoder-enabled-channels.md#Considerations) .
> 
> 

## <a name="billing-implications"></a>Debiterings konsekvenser
En Live encoding-kanal börjar fakturera så fort dess tillstånds över gångar till "körs" via API: et.   Du kan också visa statusen i Azure Portal eller i Azure Media Services Explorer-verktyget ( https://aka.ms/amse) .

Följande tabell visar hur kanal tillstånd mappas till fakturerings tillstånd i API och Azure Portal. Tillstånden skiljer sig något från API: et och portalens gränssnitt. När en kanal är i läget "körs" via API eller i läget "Ready" eller "streaming" i Azure Portal, kommer faktureringen att aktive ras.
Om du vill stoppa kanalen från faktureringen måste du stoppa kanalen via API: et eller i Azure Portal.
Du ansvarar för att stoppa dina kanaler när du är färdig med Live encoding Channel.  Om du inte stoppar en kodnings kanal leder det till fortsatt fakturering.

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

### <a name="automatic-shut-off-for-unused-channels"></a>Automatisk avstängning för oanvända kanaler
Från och med 25 januari 2016 har Media Services lanserat en uppdatering som automatiskt stoppar en kanal (där Live encoding är aktiverat) när den har körts i ett oanvänt tillstånd under en längre tid. Detta gäller för kanaler som inte har några aktiva program och som inte har tagit emot någon inmatnings bidrag under en längre tids period.

Tröskelvärdet för en oanvänd period är Nominellt 12 timmar, men kan komma att ändras.

## <a name="live-encoding-workflow"></a>Live Encoding arbets flöde
Följande diagram visar ett Live streaming-arbetsflöde där en kanal tar emot en enda bit ström i ett av följande protokoll: RTMP eller Smooth Streaming; Den kodar sedan data strömmen till en data ström med flera bit hastigheter. 

![Live-arbetsflöde][live-overview]

## <a name="common-live-streaming-scenario"></a><a id="scenario"></a>Vanligt scenario för Live-direktuppspelning
Följande steg är allmänna steg som ingår i att skapa vanliga program för direktsänd strömning.

> [!NOTE]
> Den rekommenderade maximala längden för en direktsänd händelse är för närvarande 8 timmar.
>
> Det finns en fakturerings påverkan för Live encoding och du bör komma ihåg att om du lämnar en Live encoding-kanal i läget "körs" debiteras fakturerings avgifter per timme. Vi rekommenderar att du omedelbart stoppar dina kanaler som körs när din Live streaming-händelse är klar för att undvika extra Tim debiteringar. 

1. Anslut en videokamera till en dator. Starta och konfigurera en lokal Live-kodare som kan mata ut en **enda** bit ström i ett av följande protokoll: RTMP eller Smooth Streaming. 

    Det här steget kan också utföras när du har skapat din kanal.
2. Skapa och starta en kanal. 
3. Hämta kanalens infognings-URL. 

    Infognings-URL:en används av livekodaren för att skicka dataströmmen till kanalen.
4. Hämta kanalens förhandsgransknings-URL. 

    Använd denna URL för att kontrollera att din kanal tar emot den direktsända dataströmmen korrekt.
5. Skapa ett program. 

    När du använder Azure Portal skapar ett program också en till gång. 

    När du använder .NET SDK eller REST måste du skapa en till gång och ange att du vill använda den här till gången när du skapar ett program. 
6. Publicera till gången som är associerad med programmet.   

    >[!NOTE]
    >När ditt AMS-konto skapas läggs en **standard**-slutpunkt för direktuppspelning till på ditt konto med tillståndet **Stoppad**. Slutpunkten för direktuppspelning som du vill spela upp innehåll från måste ha tillståndet **Körs**. 

7. Starta programmet när du är redo att påbörja strömning och arkivering.
8. Som alternativ kan den direktsända kodaren få signal om att starta en annons. Annonsen infogas i utdataströmmen.
9. Stoppa programmet när du vill stoppa strömningen och arkiveringen av händelsen.
10. Ta bort programmet (och ta eventuellt bort tillgången).   

> [!NOTE]
> Det är mycket viktigt att inte glömma att stoppa en Live Encoding kanal. Tänk på att det finns en fakturerings effekt per timme för direktsänd kodning och du bör komma ihåg att om du lämnar en Live encoding-kanal i läget "körs" debiteras fakturerings avgifter.  Vi rekommenderar att du omedelbart stoppar dina kanaler som körs när din Live streaming-händelse är klar för att undvika extra Tim debiteringar. 
> 
> 

## <a name="channels-input-ingest-configurations"></a><a id="channel"></a>Konfigurationer för kanalens indata (inmatnings konfiguration)
### <a name="ingest-streaming-protocol"></a><a id="Ingest_Protocols"></a>Hämta strömnings protokoll
Om **kodarens typ** är inställd på **standard** är giltiga alternativ:

* **RTMP** med enkel bit hastighet
* Enkel bit hastighet, **fragmenterad MP4** (Smooth Streaming)

#### <a name="single-bitrate-rtmp"></a><a id="single_bitrate_RTMP"></a>RTMP med enkel bithastighet
Överväganden:

* Inkommande data ström får inte innehålla video med flera bit hastigheter
* Video strömmen bör ha en genomsnittlig bit hastighet under 15 Mbit/s
* Ljud strömmen bör ha en genomsnittlig bit hastighet under 1 Mbit/s
* Följande är de codecs som stöds:
* MPEG-4 AVC/H. 264-video
* Bas linje, Main, hög profil (8-bitars 4:2:0)
* Hög 10-profil (10-bitars 4:2:0)
* Hög 422-profil (10-bitars 4:2:2)
* MPEG-2 AAC-LC-ljud
* Mono, stereo, surround (5,1, 7,1)
* samplings frekvens 44,1 kHz
* MPEG-2 Style ADTS-paket
* Rekommenderade kodare inkluderar:
* [Wirecast för strömning](media-services-configure-wirecast-live-encoder.md)
* Flash Media Live Encoder

#### <a name="single-bitrate-fragmented-mp4-smooth-streaming"></a>Fragmenterad MP4 med enkel bithastighet (Smooth Streaming)
Vanligt användnings fall:

Använd lokala Live-kodare från leverantörer som grundämne-teknik, Ericsson, Ateme, Envivio för att skicka indataströmmen över öppna Internet till ett närliggande Azure-datacenter.

Överväganden:

Samma som för [RTMP med enkel bit hastighet](media-services-manage-live-encoder-enabled-channels.md#single_bitrate_RTMP).

#### <a name="other-considerations"></a>Ytterligare överväganden
* Du kan inte ändra indataprotokollet när kanalen eller dess associerade program körs. Om du behöver olika protokoll får du skapa separata kanaler för varje indataprotokoll.
* Den maximala upplösningen för den inkommande video strömmen är 1920x1080 och högst 60 fält/sekund om den är sammanflätad eller 30 bild rutor per sekund om progressivt.

### <a name="ingest-urls-endpoints"></a>Inmatnings-URL: er (slut punkter)
En kanal tillhandahåller en inmatnings slut punkt (inmatnings-URL) som du anger i Live-kodaren, så att kodaren kan skicka strömmar till dina kanaler.

Du kan hämta inmatnings-URL: er när du har skapat en kanal. För att hämta dessa URL: er behöver kanalen inte vara i **körnings** läge. När du är redo att börja skicka data till kanalen måste den vara i **Kör** tillstånd. När kanalen börjar mata in data kan du förhandsgranska data strömmen via URL: en för för hands versionen.

Du kan välja att mata in fragmenterad MP4 (Smooth Streaming) Live Stream via en TLS-anslutning. Om du vill mata in via TLS måste du uppdatera inmatnings-URL: en till HTTPS. För närvarande stöder AMS inte TLS med anpassade domäner.  

### <a name="allowed-ip-addresses"></a>Tillåtna IP-adresser
Du kan definiera de IP-adresser som tillåts att publicera video till den här kanalen. Tillåtna IP-adresser kan anges antingen som en enskild IP-adress (till exempel 10.0.0.1), ett IP-intervall med en IP-adress och en CIDR-nätmask (till exempel "10.0.0.1/22") eller ett IP-intervall med en IP-adress och en punktavgränsad decimal näts mask (till exempel 10.0.0.1 (255.255.252.0)).

Om inga IP-adresser har angetts och det saknas regeldefinitioner kommer ingen IP-adress att tillåtas. Skapa en regel för att tillåta IP-adresser och ange 0.0.0.0/0.

## <a name="channel-preview"></a>Kanal för hands version
### <a name="preview-urls"></a>Förhandsgransknings-URL:er
Kanaler tillhandahåller en förhands gransknings slut punkt (för hands version) som du använder för att förhandsgranska och validera data strömmen innan ytterligare bearbetning och leverans.

Du kan hämta URL: en för för hands versionen när du skapar kanalen. Kanalen behöver inte vara i **körnings** läge för att hämta URL: en.

När kanalen börjar mata in data kan du förhandsgranska data strömmen.

> [!NOTE]
> För närvarande kan för hands versions strömmen bara levereras i formatet för fragmenterad MP4 (Smooth Streaming) oavsett den angivna indatatypen.  Du kan använda en spelare som finns i Azure Portal för att visa din data ström.
> 
> 

### <a name="allowed-ip-addresses"></a>Tillåtna IP-adresser
Du kan definiera de IP-adresser som tillåts att ansluta till förhands gransknings slut punkten. Om inga IP-adresser anges kommer en IP-adress att tillåtas. Tillåtna IP-adresser kan anges antingen som en enskild IP-adress (till exempel 10.0.0.1), ett IP-intervall med en IP-adress och en CIDR-nätmask (till exempel "10.0.0.1/22") eller ett IP-intervall med en IP-adress och en punktavgränsad decimal näts mask (till exempel 10.0.0.1 (255.255.252.0)).

## <a name="live-encoding-settings"></a>Inställningar för Live-kodning
I det här avsnittet beskrivs hur inställningarna för Live-kodaren i kanalen kan justeras när **kodnings typen** för en kanal är inställd på **standard**.

> [!NOTE]
> Din bidrags matning får bara innehålla ett enda ljud spår – inmatningen av flera ljud spår stöds inte för närvarande. När du gör en Live [-kodning med lokala real tids koder](media-services-live-streaming-with-onprem-encoders.md)kan du skicka en bidrags matning i Smooth Streaming-protokollet som innehåller flera ljud spår.
> 
> 

### <a name="ad-marker-source"></a>Källa för AD-markör
Du kan ange källan för AD-markörer-signaler. Standardvärdet är **API**, vilket indikerar att Live-kodaren i kanalen ska lyssna på ett asynkront **API för AD-markör**.

### <a name="cea-708-closed-captions"></a>CEA 708-textning
En valfri flagga som instruerar Live-kodaren att ignorera eventuella CEA 708-textning som är inbäddade i den inkommande videon. När flaggan har angetts till false (standard), kommer kodaren att identifiera och infoga CEA 708-data i utdata för video strömmar.

#### <a name="index"></a>Index
Vi rekommenderar att du skickar i en enda program transport ström (SPTS). Om indataströmmen innehåller flera program, tolkar Live-kodaren i kanalen program kart tabellen (betalning) i indata. identifierar de indata som har ett data ström typs namn för MPEG-2 AAC-ADTS eller AC-3-system – A eller AC-3 system-B eller MPEG-2 privat PARAMETERENTITETER eller MPEG-1 ljud-eller MPEG-2-ljud och ordnar dem i den ordning som anges i betalning. Det nollbaserade indexet används sedan för att hämta den n:te posten i den ordningen.

#### <a name="language"></a>Språk
Språk identifieraren för ljud strömmen, som följer ISO 639-2, till exempel ENG. Om detta inte anges är standardvärdet UND (odefinierat).

### <a name="system-preset"></a><a id="preset"></a>System förval
Anger den för inställning som ska användas av direktsänd kodare i den här kanalen. För närvarande är det enda tillåtna värdet **Default720p** (standard).

**Default720p** kommer att koda videon till följande 6 lager.

#### <a name="output-video-stream"></a>Video ström för utdata

| Hastigheten | Bredd | Höjd | MaxFPS | Profil | Namn på utdataström |
| --- | --- | --- | --- | --- | --- |
| 3500 |1280 |720 |30 |Högt |Video_1280x720_3500kbps |
| 2200 |960 |540 |30 |Högt |Video_960x540_2200kbps |
| 1350 |704 |396 |30 |Högt |Video_704x396_1350kbps |
| 850 |512 |288 |30 |Högt |Video_512x288_850kbps |
| 550 |384 |216 |30 |Högt |Video_384x216_550kbps |
| 200 |340 |192 |30 |Högt |Video_340x192_200kbps |

#### <a name="output-audio-stream"></a>Utdata, ljud ström

Ljudet kodas till stereo AAC-LC vid 128 kbit/s, samplings frekvens på 48 kHz.

## <a name="signaling-advertisements"></a>Signalerar annonser
När din kanal har Live Encoding aktive rad har du en komponent i din pipeline som bearbetar video och kan ändra den. Du kan signalera till kanalen om du vill infoga mellanliggande och/eller annonser i den utgående data strömmen med anpassningsbar bit hastighet. Skriv-och skriv åtgärder är stillbilder som du kan använda för att hämta in direktsänd matning i vissa fall (till exempel under en kommersiell rast). Annonserings signaler, är tidssynkroniserade signaler som du bäddar in i den utgående strömmen för att tala om för videos pelaren att vidta särskilda åtgärder, till exempel för att växla till en annons vid rätt tidpunkt. I den här [bloggen](https://codesequoia.wordpress.com/2014/02/24/understanding-scte-35/) finns en översikt över den SCTE-35-signalering som används för det här ändamålet. Nedan visas ett typiskt scenario som du kan implementera i din Live-händelse.

1. Se till att dina visnings program får en för händelse avbildning innan händelsen startar.
2. Se till att dina visnings program får en bild efter händelse när händelsen slutar.
3. Se till att dina visnings program får en fel-händelse avbildning om det uppstår problem under händelsen (till exempel strömavbrott i Stadium).
4. Skicka en AD BREAK-bild för att dölja live event-flödet under ett kommersiellt avbrott.

Här följer de egenskaper som du kan ange vid signalering av annonser. 

### <a name="duration"></a>Varaktighet
Tiden, i sekunder, för det kommersiella avbrott. Det måste vara ett positivt värde som inte är noll för att kunna starta den kommersiella rasten. När en kommersiell rast pågår och varaktigheten är inställd på noll med CueId som matchar den pågående kommersiella rasten, avbryts den rasten.

### <a name="cueid"></a>CueId
Ett unikt ID för den kommersiella rasten som ska användas av ett underordnat program för att vidta lämpliga åtgärder. Måste vara ett positivt heltal. Du kan ange det här värdet till ett slumpmässigt positivt heltal eller använda ett överordnat system för att spåra stack-ID: n. Var säker på att normalisera ID: n till positiva heltal innan du skickar via API: et.

### <a name="show-slate"></a>Visa Skriv platta
Valfritt. Signalerar Live-kodaren för att växla till [standard](media-services-manage-live-encoder-enabled-channels.md#default_slate) sidan för bakgrunds bilden under ett kommersiellt avbrott och dölja det inkommande video flödet. Ljudet stängs också av. Standardvärdet är **false**. 

Den avbildning som används är den som anges via standard egenskaps-ID för mitt till gång när kanalen skapas. Arbets sidan sträcks ut så att den passar visnings bildens storlek. 

## <a name="insert-slate--images"></a>Infoga bakgrunds bilder
Live-kodaren i kanalen kan signaleras för att växla till en bakgrunds bild. Det kan också signaleras för att avsluta ett pågående Skriv sätt. 

Live-kodaren kan konfigureras för att växla till en bakgrunds bild och dölja den inkommande video signalen i vissa situationer, till exempel under en AD Break. Om en sådan inte är konfigurerad, maskeras inte inmatad video under denna AD Break.

### <a name="duration"></a>Varaktighet
Sidans varaktighet i sekunder. Det måste vara ett positivt värde som inte är noll för att starta sidan. Om det finns ett pågående Skriv sätt och en varaktighet på noll anges, avslutas den pågående ingångs perioden.

### <a name="insert-slate-on-ad-marker"></a>Infoga Skriv platta på AD-markör
När värdet är true konfigurerar den här inställningen att Live-kodaren ska infoga en bakgrunds bild under en annons rast. Standardvärdet är True. 

### <a name="default-slate-asset-id"></a><a id="default_slate"></a>Standardformat till gångs-ID

Valfritt. Anger till gångs-ID för den Media Services till gång som innehåller bakgrunds bilden. Standardvärdet är null. 


> [!NOTE] 
> Innan du skapar kanalen ska du ladda upp bild bakgrunds bilden med följande begränsningar (inga andra filer ska vara i den här till gången). Den här bilden används endast när Live-kodaren infogar en bakgrunds bild på grund av en annons rast eller om den uttryckligen har signaler ATS till att infoga en Skriv platta. Det finns för närvarande inget alternativ för att använda en anpassad avbildning när Live-kodaren går in i en sådan status. Du kan rösta på den här funktionen [här](https://feedback.azure.com/forums/169396-azure-media-services/suggestions/10190457-define-custom-slate-image-on-a-live-encoder-channel).

* Högst 1920x1080 lösning.
* Högst 3 megabyte i storlek.
* Fil namnet måste ha fil namns tillägget *. jpg.
* Avbildningen måste överföras till en till gång som den enda AssetFile i den till gången och denna AssetFile ska vara markerad som primär fil. Till gången kan inte krypteras.

Om **standardvärdet för mitt till gångs-ID** inte anges och **Infoga på-sidan på AD-markören** är inställt på **sant**, används en standard Azure Media Services-avbildning för att dölja indata-videoströmmen. Ljudet stängs också av. 

## <a name="channels-programs"></a>Kanalens program
En kanal är associerad till program som gör att du kan styra publicering och lagring av segment i en direktsänd dataström. Kanaler hanterar program. Kanal-och program relationen påminner mycket om traditionella medier där en kanal har en konstant ström med innehåll och ett program är begränsad till en viss tids period på den kanalen.

Du kan ange det antal timmar som du vill behålla inspelat innehåll för programmet genom att ställa in längden för **Arkivfönster**. Det här värdet kan anges från minst 5 minuter till högst 25 timmar. Arkiv fönster längden anger också det maximala antalet tids klienter som kan söka bakåt i tiden från den nuvarande aktiva positionen. Program kan köras under den angivna tidsperioden men innehåll som understiger fönsterlängden ignoreras kontinuerligt. Värdet för den här egenskapen avgör också hur länge klientmanifesten kan växa.

Varje program är associerat med en till gång som lagrar det strömmade innehållet. En till gång mappas till en Block Blob-behållare i Azure Storage-kontot och filerna i till gången lagras som blobbar i behållaren. För att publicera programmet så att dina kunder kan visa data strömmen måste du skapa en OnDemand-positionerare för den associerade till gången. Med den här lokaliseraren kan du skapa en strömnings-URL som du kan tillhandahålla till dina klienter.

En kanal har stöd för upp till tre program som körs samtidigt, så att du kan skapa flera Arkiv för samma inkommande data ström. På så sätt kan du publicera och arkivera olika delar av en händelse efter behov. Ditt verksamhetsbehov kan till exempel vara att arkivera 6 timmar av ett program, men bara sända 10 minuter. För att åstadkomma detta måste du skapa två program som körs samtidigt. Ett program ställs in för att arkivera 6 timmar av händelsen, men programmet publiceras inte. Det andra programmet ställs in för att arkivera i 10 minuter och det här programmet publiceras.

Du bör inte återanvända befintliga program för nya händelser. Skapa och starta i stället ett nytt program för varje händelse enligt beskrivningen i avsnittet programmerings program för direkt uppspelning.

Starta programmet när du är redo att påbörja strömning och arkivering. Stoppa programmet när du vill stoppa strömningen och arkiveringen av händelsen. 

Om du vill ta bort arkiverat innehåll, stoppar du och tar bort programmet och tar sedan bort den associerade tillgången. En tillgång kan inte tas bort om den används av ett program. Programmet måste tas bort först. 

Även efter att du har stoppat och tagit bort programmet skulle användarna kunna strömma ditt arkiverade innehåll som en video på begäran så länge som du inte tar bort tillgången.

Om du vill behålla det arkiverade innehållet, men inte att det ska vara tillgängligt för strömning, tar du bort strömningslokaliseraren.

## <a name="getting-a-thumbnail-preview-of-a-live-feed"></a>Få en miniatyr för hands version av en live-feed
När Live Encoding har Aktiver ATS kan du nu få en för hands version av Live-matningen när den når kanalen. Detta kan vara ett värdefullt verktyg för att kontrol lera om din live-feed faktiskt når kanalen. 

## <a name="channel-states-and-how-states-map-to-the-billing-mode"></a><a id="states"></a>Kanal tillstånd och hur tillstånd mappas till fakturerings läget
Aktuell status för en kanal. Möjliga värden är:

* **Stoppades**. Det här är den ursprungliga statusen för kanalen när den har skapats. I det här läget kan kanal egenskaperna uppdateras, men strömning är inte tillåtet.
* **Startar**. Kanalen startas. Inga uppdateringar eller strömmande tillåts under det här läget. Om ett fel inträffar återgår kanalen till stoppat tillstånd.
* **Körs**. Kanalen kan bearbeta Live-strömmar.
* **Stoppar**. Kanalen stoppas. Inga uppdateringar eller strömmande tillåts under det här läget.
* **Tar bort**. Kanalen tas bort. Inga uppdateringar eller strömmande tillåts under det här läget.

Följande tabell visar hur kanal tillstånd mappas till fakturerings läget. 

| Kanal tillstånd | Portal GRÄNSSNITTs indikatorer | Debiteras? |
| --- | --- | --- |
| Startar |Startar |Nej (tillfälligt tillstånd) |
| Körs |Redo (inga program som körs)<br/>eller<br/>Strömning (minst ett program som körs) |Ja |
| Stoppas |Stoppas |Nej (tillfälligt tillstånd) |
| Stoppad |Stoppad |Inga |

> [!NOTE]
> För närvarande är kanalens start genomsnitt cirka 2 minuter, men ibland kan det ta upp till 20 minuter. Det kan ta upp till 5 minuter att återställa kanal.
> 
> 

## <a name="considerations"></a><a id="Considerations"></a>Överväganden
* När en kanal med **standard** kodnings typ får en förlust av inmatnings källa/bidrags flöden, kompenseras det genom att ersätta käll videon/ljudet med fel på Skriv-och-ljud. Kanalen fortsätter att generera en arbets gång tills inmatnings-/bidrags flödet återupptas. Vi rekommenderar att en Live-kanal inte lämnas i ett sådant tillstånd längre än 2 timmar. Utöver den punkten garanteras inte beteendet för kanalen vid åter anslutning av inaktivitet, varken dess beteende som svar på ett återställnings kommando. Du måste stoppa kanalen, ta bort den och skapa en ny.
* Du kan inte ändra indataprotokollet när kanalen eller dess associerade program körs. Om du behöver olika protokoll får du skapa separata kanaler för varje indataprotokoll.
* Varje gång du konfigurerar om Live-kodaren anropar du **återställnings** metoden i kanalen. Innan du återställer kanalen måste du stoppa programmet. Starta om programmet när du har återställt kanalen.
* En kanal kan bara stoppas när den är i körnings läge och alla program på kanalen har stoppats.
* Som standard kan du bara lägga till 5 kanaler till ditt Media Services-konto. Detta är en mjuk kvot för alla nya konton. Mer information finns i [kvoter och begränsningar](media-services-quotas-and-limitations.md).
* Du kan inte ändra indataprotokollet när kanalen eller dess associerade program körs. Om du behöver olika protokoll får du skapa separata kanaler för varje indataprotokoll.
* Du faktureras bara när din kanal är i **körnings** läge. Mer information finns i [det här](media-services-manage-live-encoder-enabled-channels.md#states) avsnittet.
* Den rekommenderade maximala längden för en direktsänd händelse är för närvarande 8 timmar. 
* Se till att ha den slut punkt för direkt uppspelning som du vill strömma innehåll i **körnings** läge för.
* Kodnings för inställningen använder begreppet "Max bild frekvens" av 30 fps. Så om indatamängden är 60FPS/59.94 i, släpps/deflätas de till 30/29.97 fps. Om indatamängden är 50fps/50i, släpps/deflätas de till 25 fps. Om indata är 25 fps förblir utdata kvar vid 25 fps.
* Glöm inte att stoppa KANALERna när du är färdig. Om du inte gör det fortsätter faktureringen.

## <a name="known-issues"></a>Kända problem
* Kanalens start tid har förbättrats till ett genomsnitt på 2 minuter, men vid ökad efter frågan kunde det fortfarande ta upp till 20 minuter.
* Skriv platta bilder bör följa begränsningar som beskrivs [här](media-services-manage-live-encoder-enabled-channels.md#default_slate). Om du försöker skapa en kanal med en standard sida som är större än 1920x1080, kommer begäran att bli fel.
* En gång till.... Glöm inte att stoppa KANALERna när du är färdig med strömningen. Om du inte gör det fortsätter faktureringen.

## <a name="need-help"></a>Behöver du hjälp?

Du kan öppna ett support ärende genom att gå till [nytt support ärende](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="next-step"></a>Nästa steg

Granska sökvägarna för Media Services-utbildning.

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Relaterade ämnen
[Leverera händelser via strömning live med Azure Media Services](media-services-overview.md)

[Skapa kanaler som utför live encoding från en enkel bit hastighet till data ström med anpassningsbar bit hastighet med portalen](media-services-portal-creating-live-encoder-enabled-channel.md)

[Skapa kanaler som utför live encoding från en enkel bit hastighet till data ström med anpassningsbar bit hastighet med .NET SDK](media-services-dotnet-creating-live-encoder-enabled-channel.md)

[Hantera kanaler med REST API](/rest/api/media/operations/channel)

[Media Services begrepp](media-services-concepts.md)

[Azure Media Services-specifikation för fragmenterad MP4 Live](../media-services-fmp4-live-ingest-overview.md)

[live-overview]: ./media/media-services-manage-live-encoder-enabled-channels/media-services-live-streaming-new.png
