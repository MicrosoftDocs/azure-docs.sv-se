---
title: Filter och dynamiska manifest | Microsoft Docs
description: I det här avsnittet beskrivs hur du skapar filter så att klienten kan använda dem för att strömma vissa delar av en ström. Media Services skapar dynamiska manifest för att arkivera denna selektiva strömning.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: ff102765-8cee-4c08-a6da-b603db9e2054
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: ne
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 002a7c61d6760decf65016870739ab62b15d5c72
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013694"
---
# <a name="filters-and-dynamic-manifests"></a>Filter och dynamiska manifest

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

> [!div class="op_single_selector" title1="Välj den version av Media Services som du använder:"]
> * [Version 2](media-services-dynamic-manifest-overview.md)
> * [Version 3](../latest/filters-dynamic-manifest-overview.md)

Från och med 2,17-versionen kan du med Media Services definiera filter för dina till gångar. Dessa filter är regler på Server sidan som gör att dina kunder kan välja att göra saker som: spela bara upp en del av en video (i stället för att spela upp hela videon) eller ange endast en delmängd av ljud-och video åter givningar som kundens enhet kan hantera (i stället för alla renderingar som är associerade med till gången). Den här filtreringen av dina till gångar uppnås genom **dynamiskt manifest** s som skapas på kundens begäran att strömma en video som baseras på angivna filter.

I det här avsnittet beskrivs vanliga scenarier där du kan använda filter för dina kunder och länkar till ämnen som visar hur du skapar filter program mässigt.

## <a name="overview"></a>Översikt
När du levererar ditt innehåll till kunder (strömma live-händelser eller video på begäran) är målet att leverera en video med hög kvalitet till olika enheter under olika nätverks förhållanden. För att uppnå det här målet gör du följande:

* koda din ström till video strömmen med multibit hastighet ([anpassningsbar bit hastighet](https://en.wikipedia.org/wiki/Adaptive_bitrate_streaming)) (detta tar hand om kvalitets-och nätverks förhållanden) och 
* Använd Media Services [dynamisk paketering](media-services-dynamic-packaging-overview.md) för att dynamiskt paketera om strömmen till olika protokoll (detta tar hand om strömning på olika enheter). Media Services stöder leverans av följande strömmande tekniker med anpassningsbar bit hastighet: HTTP Live Streaming (HLS), Smooth Streaming och MPEG-streck. 

### <a name="manifest-files"></a>MANIFEST filer
När du kodar en till gång för strömning med anpassningsbar bit hastighet skapas en **manifest** fil (Playlist) (filen är text-baserad eller XML-baserad). **Manifest** filen innehåller strömmande metadata som: spår typ (ljud, video eller text), spår namn, start-och slut tid, bit hastighet (kvalitet), spåra språk, presentations fönster (glidande fönster med fast varaktighet), video-codec (FourCC). Det instruerar också spelaren att hämta nästa fragment genom att tillhandahålla information om nästa uppspelnings bara videofragment som är tillgängliga och var de befinner sig. Fragment (eller segment) är de faktiska "segmenten" av ett video innehåll.

Här är ett exempel på en manifest fil: 

```xml
<?xml version="1.0" encoding="UTF-8"?>    
<SmoothStreamingMedia MajorVersion="2" MinorVersion="0" Duration="330187755" TimeScale="10000000">

<StreamIndex Chunks="17" Type="video" Url="QualityLevels({bitrate})/Fragments(video={start time})" QualityLevels="8">
<QualityLevel Index="0" Bitrate="5860941" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931300016E360000E4E1FF8C7076850A4580000000168E9093525" />
<QualityLevel Index="1" Bitrate="4602724" FourCC="H264" MaxWidth="1920" MaxHeight="1080" CodecPrivateData="0000000167640028AC2CA501E0089F97015202020280000003008000001931100011EDC00002CD29FF8C7076850A45800000000168E9093525" />
<QualityLevel Index="2" Bitrate="3319311" FourCC="H264" MaxWidth="1280" MaxHeight="720" CodecPrivateData="000000016764001FAC2CA5014016EC054808080A00000300020000030064C0800067C28000103667F8C7076850A4580000000168E9093525" />
<QualityLevel Index="3" Bitrate="2195119" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C1000044AA0000ABA9FE31C1DA14291600000000168E9093525" />
<QualityLevel Index="4" Bitrate="1469881" FourCC="H264" MaxWidth="960" MaxHeight="540" CodecPrivateData="000000016764001FAC2CA503C045FBC054808080A000000300200000064C04000B71A0000E4E1FF8C7076850A4580000000168E9093525" />
<QualityLevel Index="5" Bitrate="978815" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C08001E8480004C4B7F8C7076850A45800000000168E9093525" />
<QualityLevel Index="6" Bitrate="638374" FourCC="H264" MaxWidth="640" MaxHeight="360" CodecPrivateData="000000016764001EAC2CA50280BFE5C0548303032000000300200000064C080013D60000C65DFE31C1DA1429160000000168E9093525" />
<QualityLevel Index="7" Bitrate="388851" FourCC="H264" MaxWidth="320" MaxHeight="180" CodecPrivateData="000000016764000DAC2CA505067E7C054830303200000300020000030064C040030D40003D093F8C7076850A45800000000168E9093525" />

<c t="0" d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="20000000" /><c d="9600000"/>
</StreamIndex>


<StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_128kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_128kbps">
<QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="125658" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />

<c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>


<StreamIndex Chunks="17" Type="audio" Url="QualityLevels({bitrate})/Fragments(AAC_und_ch2_56kbps={start time})" QualityLevels="1" Name="AAC_und_ch2_56kbps">
<QualityLevel AudioTag="255" Index="0" BitsPerSample="16" Bitrate="53655" FourCC="AACL" CodecPrivateData="1210" Channels="2" PacketSize="4" SamplingRate="44100" />

<c t="0" d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201361" /><c d="20201360" /><c d="20201361" /><c d="20201360" /><c d="6965987" /></StreamIndex>

</SmoothStreamingMedia>
```

### <a name="dynamic-manifests"></a>Dynamiska manifest
Det finns [scenarier](media-services-dynamic-manifest-overview.md#scenarios) när din klient behöver större flexibilitet än vad som beskrivs i standard till gångens manifest fil. Exempel:

* Enhets information: leverera endast de angivna åter givningarna och/eller angivna språk spår som stöds av den enhet som används för att spela upp innehållet ("åter givnings filtrering"). 
* Minska manifestet för att visa ett under klipp av en Live-händelse ("del klipps filtrering").
* Trimma början på en video ("trimma en video").
* Justera presentations fönster (DVR) för att tillhandahålla en begränsad längd på DVR-fönstret i spelaren ("justera presentations fönstret").

För att uppnå den här flexibiliteten erbjuder Media Services **dynamiska manifest** baserade på fördefinierade [filter](media-services-dynamic-manifest-overview.md#filters).  När du definierar filtren kan klienterna använda dem för att strömma en bestämd åter givning eller del klipp av videon. De anger filter i strömnings-URL: en. Filter kan tillämpas på strömnings protokoll med anpassningsbar bit hastighet som stöds av [dynamisk paketering](media-services-dynamic-packaging-overview.md): HLS, MPEG-streck och Smooth Streaming. Exempel:

MPEG-streck-URL med filter

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf,filter=MyLocalFilter)`

Smooth Streaming URL med filter

`http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyLocalFilter)`


Mer information om hur du levererar innehåll och skapar strömmande URL: er finns i [leverera innehåll översikt](media-services-deliver-content-overview.md).

> [!NOTE]
> Observera att dynamiska manifest inte ändrar till gången och standard manifestet för till gången. Klienten kan välja att begära en ström med eller utan filter. 
> 
> 

### <a name="filters"></a><a id="filters"></a>Filter
Det finns två typer av till gångs filter: 

* Globala filter (kan användas för alla till gångar i Azure Media Services kontot, har en livs längd för kontot) och 
* Lokala filter (kan bara användas för en till gång som filtret var associerat med när det skapades, har en livstid för till gången). 

Globala och lokala filter typer har exakt samma egenskaper. Den största skillnaden mellan de två är för vilka scenarier vilken typ av filer är lämpligare. Globala filter är vanligt vis lämpliga för enhets profiler (åter givnings filtrering) där lokala filter kan användas för att trimma en speciell till gång.

## <a name="common-scenarios"></a><a id="scenarios"></a>Vanliga scenarier
Som nämnts tidigare, när du levererar ditt innehåll till kunder (strömmande Live-händelser eller video på begäran) är målet att leverera en video med hög kvalitet till olika enheter under olika nätverks förhållanden. Dessutom kanske du har andra krav som inbegriper filtrering av till gångar och användning av **dynamiskt manifest**. I följande avsnitt får du en kort översikt över olika filtrerings scenarier.

* Ange endast en delmängd av ljud-och video åter givningar som vissa enheter kan hantera (i stället för alla renderingar som är associerade med till gången). 
* Uppspelning av endast en del av en video (i stället för att spela upp hela videon).
* Justera fönstret DVR-presentation.

## <a name="rendition-filtering"></a>Åter givnings filtrering
Du kan välja att koda till gången till flera kodnings profiler (H. 264 bas linje, H. 264 hög, AACL, AACH, Dolby Digital Plus) och flera bit hastigheter. Alla klient enheter kommer dock inte att ha stöd för alla dina till gångs profiler och bit hastigheter. Till exempel stöder äldre Android-enheter endast H. 264-bas linje + AACL. Skicka högre bit hastigheter till en enhet som inte kan ta emot förmåner, bandbredd och enhets beräkning. Sådan enhet måste avkoda all den information som anges, endast för att skala ned den för visning.

Med dynamiskt manifest kan du skapa enhets profiler som mobil, konsol, HD/SD, osv. och inkludera de spår och kvaliteter som du vill ska vara en del av varje profil.

![Exempel på åter givnings filtrering][renditions2]

I följande exempel användes en kodare för att koda en mezzaninfil till sju ISO hastigheter video åter givningar (från 180p till 1080p). Kodad till gång kan paketeras dynamiskt i något av följande strömnings protokoll: HLS, jämna och MPEG-streck.  Överst i diagrammet visas HLS-manifestet för till gången utan filter (det innehåller alla sju åter givningar).  I det nedre vänstra hörnet visas HLS-manifestet som ett filter med namnet "ott" användes för. Filtret "ott" anger att ta bort alla bit hastigheter under 1Mbps, vilket resulterade i att de nedre två kvalitets nivåerna avbröts i svaret. Längst ned till höger visas HLS-manifestet som ett filter med namnet "mobil" har tillämpats på. Filtret "mobil" anger att du vill ta bort renderingar där upplösningen är större än 720p, vilket ledde till att de två 1080p-återgivningarna har tagits bort.

![Åter givnings filtrering][renditions1]

## <a name="removing-language-tracks"></a>Ta bort språk spår
Dina till gångar kan innehålla flera ljud språk, till exempel engelska, spanska, franska osv. Vanligt vis är spelarens SDK-hanterare standard val av ljud spår och tillgängliga ljud spår per val av användare. Det är svårt att utveckla sådana SDK: er för spelare, den kräver olika implementeringar i enhetsspecifika Player-ramverk. På vissa plattformar är dock API-API: erna begränsade och innehåller inte ljud urvals funktion där användare inte kan välja eller ändra standard ljud spår. Med till gångs filter kan du styra beteendet genom att skapa filter som bara innehåller önskade ljud språk.

![Språk spårar filtrering][language_filter]

## <a name="trimming-start-of-an-asset"></a>Trimning av en till gångs start
I de flesta direkt uppspelnings händelser kör operatörer vissa tester före den faktiska händelsen. De kan till exempel innehålla ett sådant som detta innan händelsen startades: "programmet kommer att påbörjas tillfälligt". Om programmet arkiveras, arkiveras även test-och bakgrunds data och tas med i presentationen. Den här informationen bör dock inte visas för klienterna. Med dynamiskt manifest kan du skapa ett start tids filter och ta bort oönskade data från manifestet.

![Trimning börjar][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Skapa under klipp (vyer) från ett Live-Arkiv
Många Live-händelser körs länge och Live-arkivet kan innehålla flera händelser. När Live-händelsen är slut kan det hända att sändningarna vill dela upp Live-arkivet i logiska programs start-och stopp sekvenser. Publicera sedan dessa virtuella program separat utan att bearbeta bearbetningen av Live-arkivet och skapa inte separata till gångar (som inte ger fördelarna med befintliga cachelagrade fragment i CDN). Exempel på sådana virtuella program är kvartalen för ett fotbolls-eller basket boll spel, innings i baseboll eller enskilda idrotts program.

Med dynamiskt manifest kan du skapa filter med start-och slut tider och skapa virtuella vyer överst i ditt Live-Arkiv. 

![Under klipps filter][subclip_filter]

Filtrerad till gång:

![Skidåkning][skiing]

## <a name="adjusting-presentation-window-dvr"></a>Justera presentations fönster (DVR)
Azure Media Services erbjuder för närvarande ett cirkulärt arkiv där varaktigheten kan konfigureras mellan 5 minuter-25 timmar. Manifest filtrering kan användas för att skapa ett rullande DVR-fönster överst i arkivet, utan att ta bort mediet. Det finns många scenarier där sändningarna vill ge ett begränsat DVR-fönster att flyttas med Live Edge och samtidigt behålla ett större Arkiv fönster. En utsändare kan vilja använda de data som finns utanför DVR-fönstret för att markera klipp, eller så kanske de vill ge olika DVR-fönster för olika enheter. De flesta av de mobila enheterna hanterar till exempel inte stora DVR-fönster (du kan ha ett DVR-fönster på 2 minuter för mobila enheter och en timme för Skriv bords klienter).

![DVR-fönster][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>Justera LiveBackoff (Live position)
Manifest filtrering kan användas för att ta bort flera sekunder från Live Edge i ett Live-program. Filtrering gör att sändningarna kan titta på presentationen på publikationens publicerings plats och skapa annons insättnings punkter innan visnings programmen tar emot data strömmen (säkerhets kopie ras med 30 sekunder). Sändarna kan sedan skicka dessa annonser till sina klient ramverk för att de ska kunna ta emot och bearbeta informationen innan annons möjligheten.

Förutom stöd för annonsering kan LiveBackoff-inställningen användas för att justera visnings positionen så att när klienterna stöter på och når den levande kanten kan de fortfarande hämta fragment från servern i stället för att få ett HTTP 404-eller 412-fel.

![livebackoff_filter][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Kombinera flera regler i ett enda filter
Du kan kombinera flera filtrerings regler i ett enda filter. Som exempel kan du definiera en "intervall regel" för att ta bort bakgrunds värden från ett Live-Arkiv och även filtrera ut tillgängliga bit hastigheter. När du använder flera filtrerings regler är slut resultatet skärnings punkten för alla regler.

![flera regler][multiple-rules]

## <a name="create-filters-programmatically"></a>Skapa filter program mässigt
I följande artikel beskrivs Media Services entiteter som är relaterade till filter. Artikeln visar också hur du program mässigt skapar filter.  

[Skapa filter med REST API: er](media-services-rest-dynamic-manifest.md).

## <a name="combining-multiple-filters-filter-composition"></a>Kombinera flera filter (filter komposition)
Du kan också kombinera flera filter i en enda URL. 

Följande scenario visar varför du kanske vill kombinera filter:

1. Du måste filtrera dina video kvaliteter för mobila enheter som Android eller iPAD (för att begränsa video kvaliteterna). Om du vill ta bort oönskade kvaliteter skapar du ett globalt filter som passar för enhets profilerna. Som nämnts tidigare i den här artikeln kan globala filter användas för alla dina till gångar under samma Media Services-konto utan ytterligare Association. 
2. Du vill också trimma start-och slut tid för en till gång. För att åstadkomma detta skulle du skapa ett lokalt filter och ange start-/slut tid. 
3. Du vill kombinera båda dessa filter (utan kombination måste du lägga till kvalitets filtrering i trimnings filtret, vilket gör att filter användningen blir svårare).

Om du vill kombinera filter måste du ange filter namnen till URL: en för manifest/spelnings lista med semikolon avgränsad. Vi antar att du har ett filter med namnet *MyMobileDevice* som filtrerar kvaliteterna och att du har en annan namngiven *MyStartTime* för att ange en angiven start tid. Du kan kombinera dem så här:

`http://teststreaming.streaming.mediaservices.windows.net/3d56a4d-b71d-489b-854f-1d67c0596966/64ff1f89-b430-43f8-87dd-56c87b7bd9e2.ism/Manifest(filter=MyMobileDevice;MyStartTime)`

Du kan kombinera upp till tre filter. 

Mer information finns i [den här](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/) bloggen.

## <a name="know-issues-and-limitations"></a>Kända problem och begränsningar
* Dynamiskt manifest fungerar i GOP-gränser (nyckel ramar) och därför har GOP precision. 
* Du kan använda samma filter namn för lokala och globala filter. Lokala filter har högre prioritet och åsidosätter globala filter.
* Om du uppdaterar ett filter kan det ta upp till 2 minuter för strömnings slut punkten att uppdatera reglerna. Om innehållet har betjänats med hjälp av vissa filter (och cachelagrats i proxyservrar och CDN-cacheminnen) kan uppdatering av dessa filter leda till Player-problem. Vi rekommenderar att du rensar cachen när du har uppdaterat filtret. Om det här alternativet inte är möjligt bör du överväga att använda ett annat filter.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se även
[Leverera innehåll till kund översikt](media-services-deliver-content-overview.md)

[renditions1]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/media-services-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/media-services-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/media-services-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/media-services-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/media-services-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/media-services-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/media-services-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/media-services-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/media-services-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/media-services-dynamic-manifest-overview/media-services-skiing.png
