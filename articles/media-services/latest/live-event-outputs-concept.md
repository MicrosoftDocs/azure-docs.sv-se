---
title: Begrepp för livehändelser och liveutdata
description: Det här avsnittet innehåller en översikt över livehändelser och liveutdata i Azure Media Services v3.
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
ms.openlocfilehash: 44ab9e4ff83fec2ddfbd1cb44f503298d12789d1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107766307"
---
# <a name="live-events-and-live-outputs-in-media-services"></a>Livehändelser och liveutdata i Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Azure Media Services kan du leverera livehändelser till dina kunder i Azure-molnet. Om du vill konfigurera liveuppspelningshändelser i Media Services v3 måste du förstå de begrepp som beskrivs i den här artikeln.

> [!TIP]
> För kunder som migrerar från Media Services v2-API:er ersätter **entiteten livehändelse** **Kanal** i v2 och **liveutdata** ersätter **programmet**.

## <a name="live-events"></a>Livehändelser

[Livehändelser](/rest/api/media/liveevents) ansvarar för att mata in och bearbeta livevideofeeds. När du skapar en livehändelse skapas en primär och sekundär indataslutpunkt som du kan använda för att skicka en livesignal från en fjärrkodare. Den fjärranslutna livekodaren skickar bidragsflödet till den indataslutpunkten med hjälp av antingen [RTMP-](https://www.adobe.com/devnet/rtmp.html) eller Smooth Streaming-indataprotokollet (fragmented-MP4). [](/openspecs/windows_protocols/ms-sstr/8383f27f-7efe-4c60-832a-387274457251) För RTMP-inmatningsprotokollet kan innehållet skickas i klar eller krypterat på ett `rtmp://` säkert sätt på kabeln( `rtmps://` ). För det Smooth Streaming inmatningsprotokollet är de URL-scheman som stöds `http://` eller `https://` .  

## <a name="live-event-types"></a>Livehändelsetyper

En [livehändelse](/rest/api/media/liveevents) kan antingen ställas in på en genomströmning (en lokal livekodare skickar en dataström med flera bithastigheter) eller livekodning *(en* lokal *livekodare* skickar en dataström med enkel bithastighet). Typerna anges när de skapas med [Hjälp av LiveEventEncodingType:](/rest/api/media/liveevents/create#liveeventencodingtype)

* **LiveEventEncodingType.None:** En lokal livekodare skickar en dataström med flera bithastigheter. Den in matade dataströmmen passerar genom livehändelsen utan ytterligare bearbetning. Kallas även för direktläge.
* **LiveEventEncodingType.Standard:** En lokal livekodare skickar en dataström med enkel bithastighet till livehändelsen och Media Services skapar dataströmmar med flera bithastigheter. Om bidragsflödet har en upplösning på 720p eller högre kodar **default720p-förinställningen** en uppsättning par med 6 upplösning/bithastigheter.
* **LiveEventEncodingType.Premium1080p:** En lokal livekodare skickar en dataström med enkel bithastighet till livehändelsen och Media Services skapar dataströmmar med flera bithastigheter. Förinställningen Default1080p anger utdatauppsättningen med par med upplösning/bithastighet.

### <a name="pass-through"></a>Direkt

![direktsändning med ett Media Services exempeldiagram](./media/live-streaming/pass-through.svg)

När du använder direktsändningen förlitar du dig på din lokala livekodare för att generera en videoström med flera bithastigheter och skicka den som bidragsflöde till livehändelsen (med RTMP eller fragmenterat MP4-protokoll). Livehändelsen passerar sedan igenom inkommande videoströmmar utan vidare bearbetning. En sådan direktsändning är optimerad för långvariga livehändelser eller linjär 24x365-direktsänd strömning. När du skapar den här typen av livehändelse anger du Ingen (LiveEventEncodingType.None).

Du kan skicka bidragsflödet med upplösningar på upp till 4K och i en bildfrekvens på 60 bilder/sekund, med antingen videocodecen H.264/AVC eller H.265/HEVC och ljudcodecen AAC (AAC-LC, HE-AACv1 eller HE-AACv2). Mer information finns i Jämförelse [av livehändelsetyper.](live-event-types-comparison-reference.md)

> [!NOTE]
> Att använda en genomströmningsmetod är det mest ekonomiska sättet att göra liveuppspelning när du utför flera händelser under en längre tidsperiod och du redan har investerat i lokala kodare. Se [Prisinformation.](https://azure.microsoft.com/pricing/details/media-services/)
>

Se .NET-kodexempel för att skapa en direktsändning i [livehändelse med DVR](https://github.com/Azure-Samples/media-services-v3-dotnet/blob/4a436376e77bad57d6cbfdc02d7df6c615334574/Live/LiveEventWithDVR/Program.cs#L214).

### <a name="live-encoding"></a>Live Encoding  

![livekodning med Media Services exempeldiagram](./media/live-streaming/live-encoding.svg)

När du använder livekodning med Media Services konfigurerar du din lokala livekodare så att den skickar en video med enkel bithastighet som bidragsflöde till livehändelsen (med RTMP eller Fragmented-Mp4-protokoll). Sedan ställer du in en livehändelse så att den kodar den inkommande strömmen med enkel bithastighet till en [videoström](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)med flera bithastigheter och gör utdata tillgängliga för leverans till uppspelningsenheter via protokoll som MPEG-DASH, HLS och Smooth Streaming.

När du använder livekodning kan du endast skicka bidragsflödet med upplösningar på upp till 1 080p med en bildfrekvens på 30 bildrutor/sekund, med H.264/AVC-videokodning och AAC-ljudkodning (AAC-LC, HE-AACv1 eller HE-AACv2). Observera att direktsändning kan ha stöd för upplösningar på upp till 4 000 bildrutor/sekund. Mer information finns i Jämförelse [av livehändelsetyper.](live-event-types-comparison-reference.md)

Upplösningarna och bithastigheterna som finns i utdata från livekodaren bestäms av förinställningen. Om du använder en **Standard** Live-kodare (LiveEventEncodingType.Standard) anger förinställningen *Default720p* en uppsättning par med sex upplösnings-/bithastigheter, från 720p vid 3,5 Mbit/s ned till 192p vid 200 kbit/s. Om du använder en **Premium1080p** livekodare (LiveEventEncodingType.Premium1080p) anger förinställningen *Default1080p* en uppsättning par med sex upplösnings-/bithastigheter som går från 1080p vid 3,5 Mbit/s ned till 180p vid 200 kbit/s. Mer information finns i [Systemförinställningar](live-event-types-comparison-reference.md#system-presets).

> [!NOTE]
> Om du behöver anpassa förinställningen för livekodning öppnar du en supportbiljett via Azure Portal. Ange önskad tabell med upplösning och bithastigheter. Kontrollera att det bara finns ett lager vid 720p (om du begär en förinställning för en Standard Live-kodare) eller på 1080p (om du begär en förinställning för en livekodare för Premium1080p) och högst 6 lager.

## <a name="creating-live-events"></a>Skapa livehändelser

### <a name="options"></a>Alternativ

När du skapar en livehändelse kan du ange följande alternativ:

* Du kan ge livehändelsen ett namn och en beskrivning.
* Molnkodning innehåller direkt (ingen molnkodning), Standard (upp till 720p) eller Premium (upp till 1080p). För Standard- och Premium-kodning kan du välja stretchläge för den kodade videon.
  * Ingen: Respekterar strikt den utdataupplösning som anges i kodningsförinställningen utan att ta hänsyn till bildpunktsproportionens förhållande eller visningsproportionförhållandet för indatavideon.
  * AutoSize (AutoSize): Åsidosätter utdataupplösningen och ändrar den så att den matchar visningsproportionens förhållande för indata, utan utfyllnad. Om indata till exempel är 1920 x 1080 och kodningsförinställningen frågar efter 1280 x 1280 åsidosätts värdet i förinställningen och utdata blir 1280 x 720, vilket upprätthåller bredd–höjd-förhållandet på 16:9.
  * Autofit: Matar ut utdata (med antingen letterbox eller pelare) för att respektera utdataupplösningen, samtidigt som du ser till att den aktiva videoregionen i utdata har samma proportioner som indata. Om indata till exempel är 1 920 x 1 080 och kodningsförinställningen frågar efter 1280 x 1280 blir utdata vid 1280 x 1280, som innehåller en inre rektangel med 1280 x 720 vid breddförhållandet 16:9, med pillar box-regioner 280 bildpunkter brett till vänster och höger.
* Strömningsprotokoll (för närvarande stöds RTMP- Smooth Streaming protokoll). Du kan inte ändra protokollalternativet när livehändelsen eller dess associerade liveutdata körs. Om du behöver olika protokoll skapar du en separat livehändelse för varje strömningsprotokoll.
* Indata-ID som är en globalt unik identifierare för livehändelseindataströmmen.
* Statiskt värdnamnsprefix som inte innehåller något (i så fall används en slumpmässig 128-bitars hexadetalssträng), Använd livehändelsenamn eller Använd anpassat namn.  När du väljer att använda ett kundnamn är det här värdet prefixet Anpassat värdnamn.
* Du kan minska svarstiden från slutet till slut mellan direktsändningen och uppspelningen genom att ange intervallet för indatanyckelramen, som är varaktigheten (i sekunder) för varje mediesegment i HLS-utdata. Värdet ska vara ett heltal som inte är noll inom intervallet 0,5 till 20 sekunder.  Värdet är som standard 2 sekunder om inget av *intervallen* för indata- eller utdatanyckelramar har angetts. Nyckelramsintervallet tillåts endast vid direkthändelser.
* När du skapar händelsen kan du ställa in den på autostart. När autostart har angetts till true startas livehändelsen när den har skapats. Faktureringen startar så fort livehändelsen börjar köras. Du måste uttryckligen anropa Stop på livehändelseresursen för att stoppa ytterligare fakturering. Du kan också starta händelsen när du är redo att börja strömma.

> [!NOTE]
> Max framerate är 30 fp för både Standard- och Premium-kodning.

## <a name="standby-mode"></a>Passningsläge

När du skapar en livehändelse kan du ställa in den på StandBy-läge. När händelsen är i StandBy-läge kan du redigera beskrivningen, prefixet Statiskt värdnamn och begränsa åtkomstinställningarna för indata och förhandsgranskning.  StandBy-läget är fortfarande ett fakturerbart läge, men priset skiljer sig från när du startar en liveström.

Mer information finns i [Livehändelse-tillstånd och fakturering.](live-event-states-billing-concept.md)

* IP-begränsningar på infogning och förhandsgranskning. Du kan definiera de IP-adresser som får mata in en video till den här direktsändningen. Tillåtna IP-adresser kan anges som en enskild IP-adress (till exempel 10.0.0.1), ett IP-intervall med IP-adress och en CIDR-nätmask (till exempel 10.0.0.1/22) eller ett IP-intervall med en IP-adress och en prickad decimalnätmask (till exempel 10.0.0.1(255.255.252.0)).
<br/><br/>
Om inga IP-adresser anges och det inte finns någon regeldefinition tillåts ingen IP-adress. Skapa en regel för att tillåta IP-adresser och ange 0.0.0.0/0.<br/>IP-adresserna måste ha något av följande format: IpV4-adress med fyra siffror eller CIDR-adressintervall.
<br/><br/>
Om du vill aktivera vissa IP-adresser i dina egna brandväggar eller om du vill begränsa indata till dina livehändelser till Azure IP-adresser laddar du ned en JSON-fil från IP-adressintervallen för [Azure Datacenter.](https://www.microsoft.com/download/details.aspx?id=41653) Om du vill ha mer information om den här filen **väljer du** avsnittet Information på sidan.

* När du skapar händelsen kan du välja att aktivera live-transkriptioner. Som standard är live-transkription inaktiverad. Mer information om live-transkription finns i [Live-transkription](live-event-live-transcription-how-to.md).

### <a name="naming-rules"></a>Namngivningsregler

* Maximalt namn på livehändelse är 32 tecken.
* Namnet bör följa det här [regex-mönstret:](/dotnet/standard/base-types/regular-expression-language-quick-reference) `^[a-zA-Z0-9]+(-*[a-zA-Z0-9])*$` .

Se även [namngivningskonventioner för slutpunkter för direktuppspelning.](stream-streaming-endpoint-concept.md#naming-convention)

> [!TIP]
> För att garantera att livehändelsenamnet är unikt kan du generera ett GUID och sedan ta bort alla bindestreck och klammerparenteser (om det finns några). Strängen är unik för alla livehändelser och längden är garanterat 32.

## <a name="live-event-ingest-urls"></a>URL:er för inmatning av livehändelse

När livehändelsen har skapats kan du hämta ingest-URL:er som du anger till den lokala livekodaren. Livekodaren använder dessa URL:er för att mata in en direktsänd dataström. Mer information finns i [Rekommenderade lokala livekodare.](encode-recommended-on-premises-live-encoders.md)

>[!NOTE]
> Från och med API-versionen 2020-05-01 kallas "anpassade" URL:er statiska värdnamn (useStaticHostname: true)


> [!NOTE]
> För att en inmatnings-URL ska vara statisk och förutsägbar för användning i en maskinvarukodarkonfiguration anger du egenskapen **useStaticHostname** till true och anger **egenskapen accessToken** till samma GUID vid varje skapande. 

### <a name="example-liveevent-and-liveeventinput-configuration-settings-for-a-static-non-random-ingest-rtmp-url"></a>Exempel på konfigurationsinställningar för LiveEvent och LiveEventInput för en statisk (icke-slumpmässig) inmatnings-RTMP-URL.

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

    Ett icke-statiskt värdnamn är standardläget i Media Services v3 när du skapar en **LiveEvent**. Du kan få livehändelsen allokerad lite snabbare, men den inmatnings-URL som du behöver för din maskinvara eller programvara för livekodning kommer att slumpmässigt. URL:en ändras om du stoppar/startar livehändelsen. Icke-statiska värdnamn är bara användbara i scenarier där en slutanvändare vill strömma med hjälp av en app som behöver få en livehändelse mycket snabbt och med en dynamisk inmatnings-URL inte är ett problem.

    Om en klientapp inte behöver generera en inmatnings-URL i förväg innan livehändelsen skapas kan du Media Services generera åtkomsttoken automatiskt för livehändelsen.

* Statiska värdnamn 

    Statiskt värdnamnsläge föredras av de flesta operatörer som vill förkonfigurera sin maskinvara eller programvara för livekodning med en RTMP-inmatnings-URL som aldrig ändras när en specifik livehändelse skapas eller stoppas/startas. Dessa operatorer vill ha en förutsägelse-URL för RTMP-inmatning som inte ändras med tiden. Detta är också mycket användbart när du behöver skicka en statisk RTMP-inmatnings-URL till konfigurationsinställningarna för en maskinvarukodningsenhet som BlackMagic Atem Mini Pro eller liknande maskinvarukodning och produktionsverktyg. 

    > [!NOTE]
    > I Azure Portal den statiska värddatornamns-URL:en "*Statiskt värdnamnsprefix*".

    Om du vill ange det här läget i API:et `useStaticHostName` anger du till vid `true` skapandetiden (standardvärdet är `false` ). När är inställt på true anger den första delen av värdnamnet som `useStaticHostname` `hostnamePrefix` tilldelats till förhandsversionen av livehändelsen och matar in slutpunkter. Det slutliga värdnamnet är en kombination av det här prefixet, medietjänstkontots namn och en kort kod för Azure Media Services datacentret.

    För att undvika en slumpmässig token i URL:en måste du även skicka din egen åtkomsttoken ( `LiveEventInput.accessToken` ) när du skapar den.  Åtkomsttoken måste vara en giltig GUID-sträng (med eller utan bindestreck). När läget är inställt kan det inte uppdateras.

    Åtkomsttoken måste vara unik i din Azure-region och Media Services konto. Om din app behöver använda en statisk hostname-inmatnings-URL rekommenderar vi att du alltid skapar en ny GUID-instans för användning med en specifik kombination av region, Media Services-konto och livehändelse.

    Använd följande API:er för att aktivera den statiska värdnamnets URL och ange åtkomsttoken till ett giltigt GUID (till exempel `"accessToken": "1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"` ).  

    |Språk|Aktivera statisk värddatornamns-URL|Ange åtkomst-token|
    |---|---|---|
    |REST|[properties.useStaticHostname](/rest/api/media/liveevents/create#liveevent)|[LiveEventInput.useStaticHostname](/rest/api/media/liveevents/create#liveeventinput)|
    |CLI|[--use-static-hostname](/cli/azure/ams/live-event#az_ams_live_event_create)|[--access-token](/cli/azure/ams/live-event#optional-parameters)|
    |.NET|[LiveEvent.useStaticHostname](/dotnet/api/microsoft.azure.management.media.models.liveevent.usestatichostname?view=azure-dotnet&preserve-view=true#Microsoft_Azure_Management_Media_Models_LiveEvent_UseStaticHostname)|[LiveEventInput.AccessToken](/dotnet/api/microsoft.azure.management.media.models.liveeventinput.accesstoken#Microsoft_Azure_Management_Media_Models_LiveEventInput_AccessToken)|

### <a name="live-ingest-url-naming-rules"></a>Namngivningsregler för URL för liveinmatning

* Den *slumpmässiga* strängen nedan är ett 128-bitars hexadecimalt tal (som består av 32 tecken mellan 0 och 9 och a–f).
* *din åtkomsttoken:* Den giltiga GUID-sträng som du anger när du använder inställningen statiskt värdnamn. Till exempel `"1fce2e4b-fb15-4718-8adc-68c6eb4c26a7"`.
* *strömnamn:* Anger strömnamnet för en specifik anslutning. Värdet för dataströmmens namn läggs vanligtvis till av livekodaren som du använder. Du kan konfigurera livekodaren så att den använder vilket namn som helst för att beskriva anslutningen, till exempel: "video1_audio1", "video2_audio1", "stream".

#### <a name="non-static-hostname-ingest-url"></a>Inmatnings-URL för icke-statiskt värdnamn

##### <a name="rtmp"></a>RTMP

`rtmp://<random 128bit hex string>.channel.media.azure.net:1935/live/<auto-generated access token>/<stream name>`<br/>
`rtmp://<random 128bit hex string>.channel.media.azure.net:1936/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2935/live/<auto-generated access token>/<stream name>`<br/>
`rtmps://<random 128bit hex string>.channel.media.azure.net:2936/live/<auto-generated access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Jämn strömning

`http://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<random 128bit hex string>.channel.media.azure.net/<auto-generated access token>/ingest.isml/streams(<stream name>)`<br/>

#### <a name="static-hostname-ingest-url"></a>Inmatnings-URL för statiskt värdnamn

I följande sökvägar innebär antingen det namn som har getts till händelsen eller det `<live-event-name>` anpassade namnet som användes när livehändelsen skapades.

##### <a name="rtmp"></a>RTMP

`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1935/live/<your access token>/<stream name>`<br/>
`rtmp://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:1936/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2935/live/<your access token>/<stream name>`<br/>
`rtmps://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net:2936/live/<your access token>/<stream name>`<br/>

##### <a name="smooth-streaming"></a>Jämn strömning

`http://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>
`https://<live event name>-<ams account name>-<region abbrev name>.channel.media.azure.net/<your access token>/ingest.isml/streams(<stream name>)`<br/>

## <a name="live-event-preview-url"></a>URL för förhandsgranskning av livehändelse

När livehändelsen börjar ta emot bidragsflödet kan du använda dess slutpunkt för förhandsversion för att förhandsgranska och verifiera att du får liveströmmen innan du publicerar vidare. När du har kontrollerat att förhandsgranskningsströmmen är bra kan du använda livehändelsen för att göra liveströmmen tillgänglig för leverans via en eller flera (i förväg skapade) slutpunkter för direktuppspelning. För att åstadkomma detta skapar du en ny [live-utdata](/rest/api/media/liveoutputs) på livehändelsen.

> [!IMPORTANT]
> Kontrollera att videon flödar till förhandsgransknings-URL:en innan du fortsätter!

## <a name="live-event-long-running-operations"></a>Långvariga åtgärder för livehändelse

Mer information finns [i långvariga åtgärder.](media-services-apis-overview.md#long-running-operations)

## <a name="live-outputs"></a>Liveutdata

När du har strömmat till livehändelsen kan du starta direktuppspelningshändelsen genom att skapa en [tillgång,](/rest/api/media/assets) [liveutdata](/rest/api/media/liveoutputs)och [positionerare för direktuppspelning.](/rest/api/media/streaminglocators) liveutdata arkiverar dataströmmen och gör den tillgänglig för tittarna via [slutpunkten för direktuppspelning.](/rest/api/media/streamingendpoints)  

Detaljerad information om liveutdata finns i Använda [en moln-DVR.](live-event-cloud-dvr-time-how-to.md)

## <a name="live-event-output-questions"></a>Frågor om livehändelseutdata

Se artikeln [med livehändelseutdatafrågor.](questions-collection.md#live-streaming)
