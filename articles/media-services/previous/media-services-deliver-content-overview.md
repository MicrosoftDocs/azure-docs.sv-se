---
title: Leverera innehåll till kunder
description: Det här avsnittet ger en översikt över vad som ingår i att leverera ditt innehåll med Azure Media Services.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.assetid: 89ede54a-6a9c-4814-9858-dcfbb5f4fed5
ms.service: media-services
ms.workload: media
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 1ad89345a2779766fde4559758e61dff92023741
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "103016619"
---
# <a name="deliver-content-to-customers"></a>Leverera innehåll till kunder

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

När du levererar ditt material för strömning eller video på begäran till kunder, är ditt mål att leverera video med hög kvalitet till olika enheter under olika nätverks förhållanden.

För att uppnå det här målet kan du:

* Koda data strömmen till en video ström med flera bit hastigheter (anpassningsbar bit hastighet). Detta kommer att ta hand om kvalitets-och nätverks förhållanden.
* Använd Microsoft Azure Media Services [dynamisk paketering](media-services-dynamic-packaging-overview.md) för att dynamiskt paketera om strömmen till olika protokoll. Det tar hand om strömning på olika enheter. Media Services stöder leverans av följande strömmande tekniker med anpassningsbar bit hastighet: <br/>
    * **Http Live streaming** (HLS) – Lägg till "(format = M3U8-AAPL)" sökvägen till "/manifest"-delen av URL: en för att tala om att den strömmande ursprungs servern returnerar tillbaka HLS innehåll för användning på **Apple iOS** -enheter (mer information finns i [platser och](#locators) [URL: er](#URLs))
    * **MPEG-streck** – Lägg till "(format = mpd-Time-CSF)" i "/manifest"-delen av URL: en för att tala om att den strömmande ursprungs servern returnerar MPEG-bindestreck (mer information finns i [platser och](#locators) [URL: er](#URLs))
    * **Smooth Streaming**.

>[!NOTE]
>När ditt AMS-konto skapas läggs en **standard**-slutpunkt för direktuppspelning till på ditt konto med tillståndet **Stoppad**. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet **Körs**. 

Den här artikeln ger en översikt över viktiga koncept för innehålls leverans.

Se [kända problem](media-services-deliver-content-overview.md#known-issues)för att kontrol lera kända problem.

## <a name="dynamic-packaging"></a>Dynamisk paketering
Med den dynamiska paketering som Media Services tillhandahåller kan du leverera din anpassningsbara bit hastighets-MP4 eller Smooth Streaming kodat innehåll i strömnings format som stöds av Media Services (MPEG-streck, HLS, Smooth Streaming) utan att behöva paketera om till dessa strömnings format. Vi rekommenderar att leverera innehåll med dynamisk paketering.

Om du vill dra nytta av dynamisk paketering måste du koda din mezzaninfil (källa) till en uppsättning MP4-filer med anpassningsbar bit hastighet eller anpassade bit hastigheter Smooth Streaming filer.

Med dynamisk paketering kan du lagra och betala för filerna i ett enda lagrings format. Media Services skapar och betjänar lämpligt svar baserat på dina begär Anden.

Dynamisk paketering är tillgänglig för slut punkter för standard-och Premium-direktuppspelning. 

Mer information finns i [dynamisk paketering](media-services-dynamic-packaging-overview.md).

## <a name="filters-and-dynamic-manifests"></a>Filter och dynamiska manifest
Du kan definiera filter för dina till gångar med Media Services. Dessa filter är regler på Server sidan som hjälper dina kunder att göra saker som att spela upp ett särskilt avsnitt i en video eller ange en delmängd av ljud-och video åter givningar som kundens enhet kan hantera (i stället för alla renderingar som är associerade med till gången). Den här filtreringen uppnås via *dynamiska manifest* som skapas när kunden begär att strömma en video baserat på ett eller flera angivna filter.

Mer information finns i [filter och dynamiska manifest](media-services-dynamic-manifest-overview.md).

## <a name="locators"></a><a name="locators"></a>Positionerare
För att ge din användare en URL som kan användas för att strömma eller hämta ditt innehåll måste du först publicera din till gång genom att skapa en positionerare. En positionerare ger en start punkt för att komma åt filerna som finns i en till gång. Media Services stöder två typer av lokaliserare:

* OnDemandOrigin-positionerare. Dessa används för att strömma media (till exempel MPEG-streck, HLS eller Smooth Streaming) eller hämta filer progressivt.
* URL-positioner för signatur för delad åtkomst (SAS). Dessa används för att hämta mediefiler till den lokala datorn.

En *åtkomst princip* används för att definiera behörigheter (till exempel läsa, skriva och lista) och varaktighet för vilken en klient har åtkomst till en viss till gång. Observera att List behörigheten (AccessPermissions. list) inte ska användas för att skapa en OnDemandOrigin-lokaliserare.

Positionerare har förfallo datum. Azure Portal anger ett förfallo datum 100 år i framtiden för positionerare.

> [!NOTE]
> Om du använde Azure Portal för att skapa lokaliserare före mars 2015 var dessa positionerare inställda på att gå ut efter två år.
> 
> 

Du uppdaterar ett utgångsdatum för en lokaliserare med [REST](/rest/api/media/operations/locator#update_a_locator)- eller [.NET](/dotnet/api/microsoft.windowsazure.mediaservices.client.ilocator)-API:er. Observera att URL:en ändras när du uppdaterar en SAS-lokaliserare.

Lokaliserare är inte utformade för att hantera åtkomst kontroll per användare. Du kan ge olika åtkomst rättigheter till enskilda användare med hjälp av DRM-lösningar (Digital Rights Management). Mer information finns i [skydda Media](/previous-versions/azure/dn282272(v=azure.100)).

När du skapar en positionerare kan det finnas en fördröjning på 30 sekunder på grund av nödvändiga lagrings-och spridnings processer i Azure Storage.

## <a name="adaptive-streaming"></a>Anpassningsbar strömning
Tekniken för anpassningsbar bit hastighet gör det möjligt för Video Player-program att fastställa nätverks förhållanden och välja mellan flera bit hastigheter. När nätverks kommunikationen degraderas kan klienten välja en lägre bit hastighet så uppspelningen kan fortsätta med lägre video kvalitet. När nätverks förhållandena ökar kan klienten växla till en högre bit hastighet med förbättrad video kvalitet. Azure Media Services stöder följande tekniker för anpassningsbar bit hastighet: HTTP Live Streaming (HLS), Smooth Streaming och MPEG-streck.

För att ge användare med direkt uppspelnings-URL: er måste du först skapa en OnDemandOrigin-lokaliserare. Genom att skapa en positionerare får du bas sökvägen till den till gång som innehåller det innehåll som du vill strömma. För att kunna strömma det här innehållet måste du dock ändra sökvägen ytterligare. Om du vill skapa en fullständig URL till den strömmande manifest filen måste du sammanfoga Sök vägs värdets sökväg och manifest filen (filnamn. ISM). Lägg sedan till **/manifest** och ett lämpligt format (om det behövs) till sökvägen till lokaliseraren.

> [!NOTE]
> Du kan också strömma ditt innehåll via en TLS-anslutning. Det gör du genom att se till att dina strömmande webb adresser börjar med HTTPS. Observera att AMS inte har stöd för TLS med anpassade domäner.  
> 

Du kan bara strömma över TLS om den strömmande slut punkten från vilken du levererar ditt innehåll har skapats efter den 10 september 2014. Om dina strömnings-URL: er baseras på de slut punkter för direkt uppspelning som skapats efter den 10 september 2014 innehåller URL: en "streaming.mediaservices.windows.net". Strömnings-URL: er som innehåller "origin.mediaservices.windows.net" (det gamla formatet) stöder inte TLS. Om din URL är i det gamla formatet och du vill kunna strömma över TLS, skapar du en ny slut punkt för direkt uppspelning. Använd URL: er baserat på den nya slut punkten för direkt uppspelning för att strömma ditt innehåll över TLS.

## <a name="streaming-url-formats"></a><a name="URLs"></a>Strömmande URL-format

### <a name="mpeg-dash-format"></a>MPEG-format
{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

http: \/ /testendpoint-testaccount.streaming.MediaServices.Windows.net/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest (format = mpd-Time-CSF)

### <a name="apple-http-live-streaming-hls-v4-format"></a>V4-format för Apple HTTP Live Streaming (HLS)
{namn på slutpunkt för direktuppspelning-namn på mediaservicekonto}.streaming.mediaservices.windows.net/{lokalisator-ID}/{filnamn}.ism/Manifest(format=m3u8-aapl)

http: \/ /testendpoint-testaccount.streaming.MediaServices.Windows.net/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest (format = M3U8-AAPL)

### <a name="apple-http-live-streaming-hls-v3-format"></a>Apple HTTP Live Streaming (HLS) v3-format
{namn på direkt uppspelnings slut punkt – Media Services konto namn}. streaming. Media Services. Windows. net/{Locator ID}/{filename}.ism/Manifest (format = M3U8-AAPL-v3)

http: \/ /testendpoint-testaccount.streaming.MediaServices.Windows.net/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest (format = M3U8-AAPL-v3)

### <a name="apple-http-live-streaming-hls-format-with-audio-only-filter"></a>Apple HTTP Live Streaming-format (HLS) med endast ljud filter
Som standard ingår ljud spår i HLS-manifestet. Detta krävs för Apple Store-certifiering för mobila nätverk. I det här fallet, om en klient inte har tillräckligt med bandbredd eller är ansluten över en 2G-anslutning, växlar uppspelningen till endast ljud. Detta hjälper till att hålla innehålls strömningen utan att buffra, men det finns ingen video. I vissa fall kan spelarens buffring vara prioriterad framför endast ljud. Om du vill ta bort ljud spåret ska du bara lägga till **ljudonly = falskt** till URL: en.

http: \/ /testendpoint-testaccount.streaming.MediaServices.Windows.net/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest (format = M3U8-AAPL-v3, endast ljud = falskt)

Mer information finns i [stöd för dynamisk manifest sammansättning och HLS-utdata ytterligare funktioner](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

### <a name="smooth-streaming-format"></a>Smooth Streaming format
{namn på slutpunkt för direktuppspelning-namn på mediaservicekonto}.streaming.mediaservices.windows.net/lokalisator-ID}/{filnamn}.ism/Manifest

Exempel:

http: \/ /testendpoint-testaccount.streaming.MediaServices.Windows.net/fecebb23-46F6-490d-8b70-203e86b0df58/BigBuckBunny.ISM/manifest

### <a name="smooth-streaming-20-manifest-legacy-manifest"></a><a id="fmp4_v20"></a>Smooth Streaming 2,0 manifest (bakåtkompatibelt manifest)
Som standard innehåller Smooth Streaming manifest formatet REPEAT-taggen (r-tag). Vissa spelare stöder dock inte r-taggen. Klienter med dessa spelare kan använda ett format som inaktiverar r-taggen:

{namn på direkt uppspelnings slut punkt – Media Services konto namn}. streaming. Media Services. Windows. net/{Locator ID}/{filename}.ism/Manifest (format = fmp4-V20)

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=fmp4-v20)`

## <a name="progressive-download"></a>Progressiv nedladdning
Med progressiv nedladdning kan du starta uppspelning av media innan hela filen har laddats ned. Det går inte att progressivt Hämta. ISM *-filer (ISMV, Isma, ISMT eller ismc).

Om du vill ladda ned innehåll progressivt använder du OnDemandOrigin typ. I följande exempel visas den URL som baseras på OnDemandOrigin typ:

`http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4`

Du måste dekryptera alla lagrings krypterade till gångar som du vill strömma från ursprungs tjänsten för progressiv nedladdning.

## <a name="download"></a>Ladda ned
Om du vill ladda ned innehållet till en klient enhet måste du skapa en SAS-positionerare. SAS-lokaliseraren ger dig åtkomst till den Azure Storage-behållare där filen finns. Om du vill bygga nedladdnings-URL: en måste du bädda in fil namnet mellan värden och SAS-signaturen.

I följande exempel visas den URL som baseras på SAS-lokaliseraren:

`https://test001.blob.core.windows.net/asset-ca7a4c3f-9eb5-4fd8-a898-459cb17761bd/BigBuckBunny.mp4?sv=2012-02-12&se=2014-05-03T01%3A23%3A50Z&sr=c&si=7c093e7c-7dab-45b4-beb4-2bfdff764bb5&sig=msEHP90c6JHXEOtTyIWqD7xio91GtVg0UIzjdpFscHk%3D`

Följande gäller:

* Du måste dekryptera alla lagrings krypterade till gångar som du vill strömma från ursprungs tjänsten för progressiv nedladdning.
* En hämtning som inte har avslut ATS inom 12 timmar kommer att Miss inträffat.

## <a name="streaming-endpoints"></a>Slutpunkter för direktuppspelning

En slut punkt för direkt uppspelning representerar en strömmande tjänst som kan leverera innehåll direkt till ett klients pelar program eller till ett Content Delivery Network (CDN) för vidare distribution. Den utgående strömmen från en slut punkt för direkt uppspelning kan vara en Live-ström eller en till gång till en video på begäran på ditt Media Services-konto. Det finns två typer av slut punkter för direkt uppspelning, **standard** och **Premium**. Mer information finns i [Översikt över strömningsslutpunkter](media-services-streaming-endpoints-overview.md).

>[!NOTE]
>När ditt AMS-konto skapas läggs en **standard**-slutpunkt för direktuppspelning till på ditt konto med tillståndet **Stoppad**. Om du vill starta direktuppspelning av innehåll och dra nytta av dynamisk paketering och dynamisk kryptering måste slutpunkten för direktuppspelning som du vill spela upp innehåll från ha tillståndet **Körs**. 

## <a name="known-issues"></a>Kända problem
### <a name="changes-to-smooth-streaming-manifest-version"></a>Ändringar av Smooth Streaming manifest version
Innan den 2016 Service Release – när till gångar som produceras av Media Encoder Standard, Media Encoder Premium Workflow eller tidigare Azure Media Encoder strömmas med hjälp av dynamisk paketering--Smooth Streaming manifestet som returnerades uppfyller version 2,0. I version 2,0 använder Fragmentets varaktighet inte den så kallade REPEAT-Taggar (r). Exempel:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="8000" TimeScale="1000">
    <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
        <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
        <c t="0" d="2000" n="0" />
        <c d="2000" />
        <c d="2000" />
        <c d="2000" />
    </StreamIndex>
</SmoothStreamingMedia>
```

I 2016-versionen från juli är det genererade Smooth Streaming manifestet som överensstämmer med version 2,2, med fragmenterade varaktigheter med hjälp av upprepade taggar. Exempel:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<SmoothStreamingMedia MajorVersion="2" MinorVersion="2" Duration="8000" TimeScale="1000">
    <StreamIndex Chunks="4" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="3" Subtype="" Name="video" TimeScale="1000">
        <QualityLevel Index="0" Bitrate="1000000" FourCC="AVC1" MaxWidth="640" MaxHeight="360" CodecPrivateData="00000001674D4029965201405FF2E02A100000030010000003032E0A000F42400040167F18E3050007A12000200B3F8C70ED0B16890000000168EB7352" />
        <c t="0" d="2000" r="4" />
    </StreamIndex>
</SmoothStreamingMedia>
```

Några av de äldre Smooth Streaming-klienterna kanske inte stöder upprepnings taggarna och kommer inte att kunna läsa in manifestet. Du kan åtgärda det här problemet genom att använda den äldre manifest format parametern **(format = fmp4-V20)** eller uppdatera klienten till den senaste versionen, som stöder upprepnings taggar. Mer information finns i [Smooth Streaming 2,0](media-services-deliver-content-overview.md#fmp4_v20).

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="related-topics"></a>Relaterade ämnen
[Uppdatera Media Services positionerare efter nycklar för rullande lagring](media-services-roll-storage-access-keys.md)