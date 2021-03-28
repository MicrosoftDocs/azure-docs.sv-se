---
title: Koncept för Live-händelser och Live-utdata
description: Det här avsnittet innehåller en översikt över direktsända händelser och direktsända utdata i Azure Media Services v3.
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
ms.date: 10/23/2020
ms.author: inhenkel
ms.openlocfilehash: 0eff5b13a79f64de1863e82a2c9285788accf6fc
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2021
ms.locfileid: "105641535"
---
# <a name="live-events-and-live-outputs-in-media-services"></a>Live-händelser och Live-utdata i Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Med Azure Media Services kan du leverera Live-händelser till dina kunder i Azure-molnet. Om du vill konfigurera direkt uppspelnings händelser i Media Services v3 måste du förstå de begrepp som beskrivs i den här artikeln.

> [!TIP]
> För kunder som migrerar från Media Services v2-API: er ersätter **Live Event** -enheten **kanal** i v2 och **Live output** ersätter **program**.

## <a name="live-events"></a>Livehändelser

[Live-händelser](/rest/api/media/liveevents) ansvarar för att mata in och bearbeta direktsända video flöden. När du skapar en Live-händelse skapas en slut punkt för primär och sekundär ingång som du kan använda för att skicka en Live-signal från en fjär kodare. Remote Live Encoder skickar bidrags flödet till den angivna slut punkten med hjälp av antingen [RTMP](https://www.adobe.com/devnet/rtmp.html) -eller [Smooth Streaming](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251) -protokollet (fragmenterad-MP4). I RTMP-inmatnings protokollet kan innehållet skickas i klartext ( `rtmp://` ) eller säkert krypterat i kabeln ( `rtmps://` ). För det Smooth Streaming inmatnings protokollet är de URL-scheman som stöds `http://` eller `https://` .  

## <a name="live-event-types"></a>Direktsända händelse typer

En [Live-händelse](/rest/api/media/liveevents) kan ställas in till antingen en *direkt* uppspelning (en lokal Live-kodare som skickar en data ström med flera bit hastigheter) eller *direktsänd kodning* (en lokal Live-kodare skickar en data ström med en bit hastighet). Typerna anges när du skapar med [LiveEventEncodingType](/rest/api/media/liveevents/create#liveeventencodingtype):

* **LiveEventEncodingType. None**: en lokal Live-kodare skickar en data ström med flera bit hastigheter. Den inmatade strömmen passerar genom Live-händelsen utan ytterligare bearbetning. Kallas även genom strömnings läge.
* **LiveEventEncodingType. standard**: en lokal Live-kodare skickar en data ström med en bit hastighet till Live-händelsen och Media Services skapar flera bit hastighets strömmar. Om bidrags flödet är av 720p eller högre, kommer **Default720p** att koda en uppsättning med 6 lösnings-/bit hastighets par.
* **LiveEventEncodingType. Premium1080p**: en lokal Live-kodare skickar en data ström med en bit hastighet till Live-händelsen och Media Services skapar flera bit hastighets strömmar. Default1080p för för inställning anger utdata för paren resolution/bit hastighet.

### <a name="pass-through"></a>Direkt

![direkt sändnings händelse med Media Services exempel diagram](./media/live-streaming/pass-through.svg)

När du använder direkt **sändnings evenemang** förlitar du dig på din lokala Live-kodare för att generera en video ström med flera bit hastigheter och skicka den som bidrags flödet till Live-händelsen (med hjälp av RTMP eller fragmenterat MP4-protokoll). Live-händelsen utför sedan de inkommande video strömmarna utan vidare bearbetning. En sådan direkt sändnings händelse är optimerad för långvariga Live-händelser eller linjär direkt uppspelning med 24x365. När du skapar den här typen av Live-händelse anger du ingen (LiveEventEncodingType. None).

Du kan skicka bidragsflödet med upplösningar på upp till 4K och i en bildfrekvens på 60 bilder/sekund, med antingen videocodecen H.264/AVC eller H.265/HEVC och ljudcodecen AAC (AAC-LC, HE-AACv1 eller HE-AACv2). Mer information finns i [jämförelse av live event types](live-event-types-comparison.md).

> [!NOTE]
> Att använda en direkt metod är det mest ekonomiska sättet att göra Direktsänd strömning när du utför flera händelser under en lång tids period och du redan har investerat i lokala kodare. Se [pris](https://azure.microsoft.com/pricing/details/media-services/) information.
>

Se exempel på .NET-kod för att skapa en direkt sändnings händelse i [Live event med DVR](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/4a436376e77bad57d6cbfdc02d7df6c615334574/Live/LiveEventWithDVR/Program.cs#L214).

### <a name="live-encoding"></a>Live Encoding  

![Live encoding med Media Services exempel diagram](./media/live-streaming/live-encoding.svg)

När du använder Live encoding med Media Services konfigurerar du din lokala Live-kodare för att skicka en video med en bit hastighet som bidrags flödet till Live-händelsen (med hjälp av RTMP eller Fragmented-Mp4 protokoll). Sedan kan du konfigurera en Live-händelse så att den kodar en data ström med [flera bit hastigheter till video strömmen med flera bit](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)hastigheter och gör utdata tillgängligt för leverans för att spela upp enheter via protokoll som MPEG-streck, HLS och Smooth Streaming.

När du använder Live encoding kan du bara skicka bidrags flödet vid upplösningar upp till 1080p-resolution med en bild Rute hastighet på 30 bild rutor per sekund, med H. 264/AVC video-codec och AAC (AAC-LC, HE-AACv1 eller HE-AACv2) ljud-codec. Observera att direkt sändnings händelser kan stödja lösningar upp till 4K med 60 bild rutor per sekund. Mer information finns i [jämförelse av live event types](live-event-types-comparison.md).

Lösningarna och bit hastigheterna i utdata från Live-kodaren bestäms av för inställningen. Om du använder en **vanlig** Live-kodare (LiveEventEncodingType. standard) anger *Default720p* -förvalet en uppsättning med sex lösnings-och bit hastighets par, och går från 720p 3,5 Mbit/s till 192p vid 200 kbit/s. Annars, om du använder en **Premium1080p** Live-kodare (LiveEventEncodingType. Premium1080p), anger *Default1080p* -förvalet en uppsättning med sex lösnings-/bit hastighets par, från 1080p vid 3,5 Mbit/s till 180p vid 200 kbit/s. Mer information finns i [Systemförinställningar](live-event-types-comparison.md#system-presets).

> [!NOTE]
> Om du behöver anpassa för inställningen för direkt kodning öppnar du ett support ärende via Azure Portal. Ange önskad tabell för upplösning och bit hastigheter. Kontrol lera att det bara finns ett lager vid 720p (om du begär en för inställning för en vanlig Live-kodare) eller på 1080p (om du begär en för inställning för en Premium1080p Live-kodare) och 6 lager högst.

## <a name="creating-live-events"></a>Skapa Live-händelser

### <a name="options"></a>Alternativ

När du skapar en Live-händelse kan du ange följande alternativ:

* Du kan ge Live-händelsen ett namn och en beskrivning.
* Cloud encoding innehåller direkt (ingen moln kodning), standard (upp till 720p) eller Premium (upp till 1080p). För standard-och Premium-kodning kan du välja utsträcknings läget för den kodade videon.
  * Ingen: den upplösnings upplösning som anges i kodnings förvalet utan att du beaktar bild punkts förhållandet eller visnings förhållandet för indata-videon.
  * AutoSize: åsidosätter upplösningen och ändrar den så att den matchar visnings bredd förhållandet för indata, utan utfyllnad. Om t. ex. indata är 1920x1080 och kodnings för inställningen frågar efter 1280x1280, åsidosätts värdet i förvalet och utdata kommer att finnas på 1280x720, vilket bibehåller inmatnings proportionen på 16:9.
  * Autopassa: utvärderar utdata (med antingen Letterbox-eller pelare-rutan) för att följa upplösningen och se till att det aktiva Video området i utdata har samma bredd-förhållande som indata. Om t. ex. indata är 1920x1080 och kodnings för inställningen frågar efter 1280x1280, kommer utdata att finnas på 1280x1280, som innehåller en inre rektangel med 1280x720 vid proportionerna 16:9, med pelare Box-regioner 280 bild punkter brett till vänster och höger.
* Direkt uppspelnings protokoll (för närvarande stöds RTMP-och Smooth Streaming-protokoll). Du kan inte ändra alternativet protokoll när Live-händelsen eller dess associerade Live-utdata körs. Om du behöver olika protokoll skapar du en separat Live-händelse för varje strömmande protokoll.
* Indata-ID som är en globalt unik identifierare för den aktiva data strömmen i real tid.
* Prefix för statiskt värdnamn som innehåller inget (i vilket fall visas en slumpmässig 128-bitars hex-sträng), Använd Live-händelseloggen eller Använd anpassat namn.  När du väljer att använda ett kundnamn är det här värdet det anpassade hostname-prefixet.
* Du kan minska svars tiden från slut punkt till slut punkt mellan live-sändningen och uppspelningen genom att ange intervallet för inmatnings nyckelns ram, vilket är varaktigheten (i sekunder) för varje medie segment i HLS-utdata. Värdet ska vara ett heltal som inte är noll i intervallet 0,5 till 20 sekunder.  Standardvärdet är 2 sekunder om *ingen* av ram intervallen för indata-eller nedmatnings nycklar har angetts. Nyckel Rute intervallet tillåts endast för direkt inloggnings händelser.
* När du skapar händelsen kan du ange att den ska starta. När Autostart är inställt på True startas direkt händelsen när den har skapats. Faktureringen börjar så snart direkt händelsen börjar köras. Du måste uttryckligen anropa Stop Event-resursen för att stoppa ytterligare fakturering. Du kan också starta händelsen när du är redo att börja strömma.

> [!NOTE]
> Max hastigheten är 30 fps för både standard-och Premium-kodning.

## <a name="standby-mode"></a>Vänte läge

När du skapar en Live-händelse kan du ange den som vänte läge. När händelsen är i vänte läge kan du redigera beskrivningen, det statiska hostname-prefixet och begränsa inställningarna för inåtkomst-och förhands granskning.  StandBy-läget är fortfarande ett fakturerbart läge, men det kostar något annorlunda än när du startar en Live-dataström.

Mer information finns i [händelse tillstånd och fakturering](live-event-states-billing.md).

* IP-begränsningar på infogning och förhandsgranskning. Du kan definiera de IP-adresser som får hämta en video till den här Live-händelsen. Tillåtna IP-adresser kan anges som en enskild IP-adress (till exempel 10.0.0.1), ett IP-intervall med IP-adress och en CIDR-nätmask (till exempel 10.0.0.1/22) eller ett IP-intervall med en IP-adress och en prickad decimalnätmask (till exempel 10.0.0.1(255.255.252.0)).
<br/><br/>
Om inga IP-adresser har angetts och det inte finns någon regel definition kommer ingen IP-adress att tillåtas. Skapa en regel för att tillåta IP-adresser och ange 0.0.0.0/0.<br/>IP-adresserna måste vara i något av följande format: IpV4-adress med fyra nummer eller CIDR-adressintervall.
<br/><br/>
Om du vill aktivera vissa IP-adresser i dina egna brand väggar eller vill begränsa indata till dina Live-händelser till Azure IP-adresser, laddar du ned en JSON-fil från [Azure datacenter-IP-adressintervall](https://www.microsoft.com/download/details.aspx?id=41653). Om du vill ha mer information om den här filen väljer du avsnittet **information** på sidan.

* När du skapar händelsen kan du välja att aktivera Live-avskrifter. Direkt avskrift är inaktive rad som standard. Mer information om Live-avskrifter finns i [Live-avskrifter](live-transcription.md).

### <a name="naming-rules"></a>Namngivningsregler

* Max namnet för Live-händelser är 32 tecken.
* Namnet ska följa det här [regex](/dotnet/standard/base-types/regular-expression-language-quick-reference) -mönstret: `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$` .

Se även [namn konventioner för direkt uppspelnings slut punkter](streaming-endpoint-concept.md#naming-convention).

> [!TIP]
> För att garantera att ditt live-evenemang är unikt kan du generera ett GUID och sedan ta bort alla bindestreck och klammerparenteser (om det finns några). Strängen blir unik för alla Live-händelser och dess längd garanterar att den är 32.

## <a name="live-event-ingest-urls"></a>Hämtnings webb adresser för live event

När Live-händelsen har skapats kan du hämta URL: er för inmatning som du kommer att ge till den lokala kodaren i real tid. Livekodaren använder dessa URL:er för att mata in en direktsänd dataström. Mer information finns i [rekommenderade lokala direkt kodare](recommended-on-premises-live-encoders.md).

>[!NOTE]
> Från och med 2020-05-01 API-versionen kallas "anpassad"-URL: er som statiska värdnamn (useStaticHostname: true)


> [!NOTE]
> För att en inmatnings-URL ska vara statisk och förutsägbar för användning i en maskin varu kodare ställer du in egenskapen **useStaticHostname** på True och anger egenskapen **ACCESSTOKEN** till samma GUID för varje skapande. 

### <a name="example-liveevent-and-liveeventinput-configuration-settings-for-a-static-non-random-ingest-rtmp-url"></a>Exempel på LiveEvent-och LiveEventInput-konfigurationsinställningar för en statisk (icke-slumpmässig) inhämtning av RTMP-URL.

```csharp
             LiveEvent liveEvent = new LiveEvent(
                    location: mediaService.Location,
                    description: "Sample LiveEvent from .NET SDK sample",
                    // Set useStaticHostname to true to make the ingest and preview URL host name the same. 
                    // This can slow things down a bit. 
                    useStaticHostname: true,

                    // 1) Set up the input settings for the Live event...
                    input: new LiveEventInput(
                        streamingProtocol: LiveEventInputProtocol.RTMP,  // options are RTMP or Smooth Streaming ingest format.
                                                                         // This sets a static access token for use on the ingest path. 
                                                                         // Combining this with useStaticHostname:true will give you the same ingest URL on every creation.
                                                                         // This is helpful when you only want to enter the URL into a single encoder one time for this Live Event name
                        accessToken: "acf7b6ef-8a37-425f-b8fc-51c2d6a5a86a",  // Use this value when you want to make sure the ingest URL is static and always the same. If omitted, the service will generate a random GUID value.
                        accessControl: liveEventInputAccess, // controls the IP restriction for the source encoder.
                        keyFrameIntervalDuration: "PT2S" // Set this to match the ingest encoder's settings
                    ),
```

* Icke-statiskt värdnamn

    Ett icke-statiskt värdnamn är standard läget i Media Services v3 när du skapar en **LiveEvent**. Du kan få direkt sändningen något snabbare, men den inmatnings-URL som du skulle behöva för din maskin vara eller program vara för direktsänd kodning kommer att slumpmässigt visas. URL-adressen kommer att ändras om du stoppar eller startar direkt sändningen. Icke-statiska värdnamn är bara användbara i scenarier där en användare vill strömma med hjälp av en app som behöver få en Live-händelse mycket snabbt och med en dynamisk inmatnings-URL inte är ett problem.

    Om en klient app inte behöver förgenerera en inmatnings-URL innan direkt sändnings händelsen skapas, kan Media Services automatiskt generera åtkomsttoken för Live-händelsen.

* Statiska värdnamn 

    Läget för statiskt värdnamn föredras av de flesta operatörer som vill förkonfigurera sin maskinvaru-eller program vara för direkt kodning med en RTMP-inmatnings-URL som aldrig ändras vid skapande eller stopp/start av en viss direkt händelse. Dessa operatörer vill ha en förutsägande RTMP-inmatnings-URL som inte ändras över tid. Detta är också användbart när du behöver skicka en statisk RTMP-inmatnings-URL till konfigurations inställningarna för en maskin varu kodnings enhet, t. ex. BlackMagic Atem Mini Pro, eller liknande maskin varu kodning och produktions verktyg. 

    > [!NOTE]
    > I Azure Portal kallas den statiska hostname-URL: en "*statisk hostname*"-prefix.

    Om du vill ange det här läget i API: t väljer `useStaticHostName` du `true` vid skapande tid (standard är `false` ). Om `useStaticHostname` är inställt på sant, `hostnamePrefix` anger den första delen av det värdnamn som tilldelats för hands versionen av live event och matar in slut punkter. Det sista värd namnet är en kombination av det här prefixet, medie tjänstens konto namn och en kort kod för Azure Media Services data Center.

    För att undvika en slumpmässig token i URL: en måste du också skicka din egen åtkomsttoken ( `LiveEventInput.accessToken` ) när du skapar den.  Åtkomsttoken måste vara en giltig GUID-sträng (med eller utan bindestreck). När läget har angetts kan det inte uppdateras.

    Åtkomsttoken måste vara unik i din Azure-region och Media Services konto. Om appen behöver använda en statisk URL för värdnamn, bör du alltid skapa en ny GUID-instans för användning med en viss kombination av region, Media Services-konto och Live-händelse.

    Använd följande API: er för att aktivera den statiska hostname-URL: en och ange åtkomsttoken till ett giltigt GUID (till exempel `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"` ).  

    |Språk|Aktivera statisk värdnamn-URL|Ange åtkomst-token|
    |---|---|---|
    |REST|[egenskaper. useStaticHostname](/rest/api/media/liveevents/create#liveevent)|[LiveEventInput.useStaticHostname](/rest/api/media/liveevents/create#liveeventinput)|
    |CLI|[--Använd-static-hostname](/cli/azure/ams/live-event#az-ams-live-event-create)|[--åtkomsttoken](/cli/azure/ams/live-event#optional-parameters)|
    |.NET|[LiveEvent.useStaticHostname](/dotnet/api/microsoft.azure.management.media.models.liveevent.usestatichostname?view=azure-dotnet#Microsoft_Azure_Management_Media_Models_LiveEvent_UseStaticHostname)|[LiveEventInput. AccessToken](/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|

### <a name="live-ingest-url-naming-rules"></a>Namn regler för Live-inmatnings-URL

* Den *slumpmässiga* strängen nedan är ett 128-bitars hexadecimalt tal (som består av 32 tecken mellan 0 och 9 och a–f).
* *din åtkomsttoken*: den giltiga GUID-sträng som du anger när du använder den statiska inställningen för värdnamn. Till exempel `"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`.
* *data ström namn*: anger data Ströms namnet för en speciell anslutning. Data ström namn svärdet läggs vanligt vis till av den Live-kodare som du använder. Du kan konfigurera Live-kodaren att använda ett namn som beskriver anslutningen, till exempel: "video1_audio1", "video2_audio1", "Stream".

#### <a name="non-static-hostname-ingest-url"></a>Inmatnings-URL för icke-statisk värdnamn

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/live/<auto-generated access token>/<stream name>`<br/>
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/live/<auto-generated access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Smidig strömning

`http://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>

#### <a name="static-hostname-ingest-url"></a>URL för inmatning av statiskt värdnamn

I följande sökvägar `<live-event-name>` innebär detta antingen namnet på händelsen eller det anpassade namnet som används för att skapa Live-händelsen.

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/live/<your access token>/<stream name>`<br/>
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/live/<your access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Smidig strömning

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>

## <a name="live-event-preview-url"></a>URL för förhands granskning av live event

När Live-händelsen börjar ta emot mottagar flödet kan du använda dess förhands gransknings slut punkt för att förhandsgranska och validera att du tar emot den aktiva strömmen innan du publicerar den. När du har kontrollerat att förhands gransknings strömmen är korrekt kan du använda direkt sändningen för att göra en Live-dataström tillgänglig för leverans via en eller flera slut punkter för direkt uppspelning. Det gör du genom att skapa en ny [Live-utmatning](/rest/api/media/liveoutputs) för Live-evenemanget.

> [!IMPORTANT]
> Se till att videon flödar till förhands gransknings-URL: en innan du fortsätter!

## <a name="live-event-long-running-operations"></a>Tids krävande åtgärder för live event

Mer information finns i [tids krävande åtgärder](media-services-apis-overview.md#long-running-operations).

## <a name="live-outputs"></a>Live-utdata

När strömmen flödar in i Live-evenemanget kan du starta den strömmande händelsen genom att skapa en [till gång](/rest/api/media/assets), en [Live-utgång](/rest/api/media/liveoutputs)och en [strömmande positionerare](/rest/api/media/streaminglocators). Live-utdata kommer att arkivera strömmen och göra den tillgänglig för användare via [slut punkten för direkt uppspelning](/rest/api/media/streamingendpoints).  

Detaljerad information om direktsända utdata finns i [använda en moln-DVR](live-event-cloud-dvr.md).

## <a name="live-event-output-questions"></a>Frågor om Live Events-utdata

Se artikeln vanliga [frågor och svar om live-evenemang](questions-collection.md#live-streaming) .
