---
title: Filtrera dina manifest med hjälp av dynamisk Paketeraren
description: Lär dig hur du skapar filter med dynamisk Paketeraren för att filtrera och selektivt strömma dina manifest.
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
ms.openlocfilehash: 3ffdb41752630e0e5e22303ff58ecd798595a890
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98897670"
---
# <a name="filter-your-manifests-using-dynamic-packager"></a>Filtrera dina manifest med hjälp av dynamisk Paketeraren

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

När du levererar strömnings innehåll med anpassningsbar bit hastighet till enheter måste du ibland publicera flera versioner av ett manifest för att rikta in dig på specifika enhets funktioner eller tillgänglig nätverks bandbredd. Med den [dynamiska Paketeraren](dynamic-packaging-overview.md) kan du ange filter som kan filtrera ut vissa codecenheter, lösningar, bit hastigheter och kombinationer av ljud spår direkt. Den här filtreringen tar bort behovet av att skapa flera kopior. Du behöver bara publicera en ny URL med en viss uppsättning filter som kon figurer ATS för mål enheterna (iOS, Android, SmartTV eller webbläsare) och nätverksfunktioner (scenarier med hög bandbredd, mobil eller låg bandbredd). I det här fallet kan klienterna ändra strömningen för ditt innehåll genom frågesträngen (genom att ange tillgängliga [till gångs filter eller konto filter](filters-concept.md)) och använda filter för att strömma vissa delar av en ström.

Vissa leverans scenarier kräver att du ser till att en kund inte kan komma åt specifika spår. Till exempel kanske du inte vill publicera ett manifest som innehåller HD-spår till en viss prenumerations nivå. Eller så kanske du vill ta bort vissa ABR-spår (adaptiv bit hastighet) för att minska leverans kostnaderna till en speciell enhet som inte drar nytta av de ytterligare spåren. I det här fallet kan du associera en lista med i förväg skapade filter med din [strömmande lokalisering](streaming-locators-concept.md) när du skapade den. Klienter kan sedan inte ändra hur innehållet strömmas eftersom det definieras av **streaming Locator**.

Du kan kombinera filtrering genom att ange [filter på strömmande lokalisering](filters-concept.md#associating-filters-with-streaming-locator) + ytterligare enhetsspecifika filter som klienten anger i URL: en. Den här kombinationen är användbar för att begränsa ytterligare spår som metadata eller händelse strömmar, ljud språk eller beskrivande ljud spår.

Den här möjligheten att ange olika filter i data strömmen ger en kraftfull lösning för **dynamisk manifest** hantering för att rikta in sig på flera användnings Falls scenarier för mål enheterna. Det här avsnittet beskriver begrepp som rör **dynamiska manifest** och innehåller exempel på scenarier där du kan använda den här funktionen.

> [!NOTE]
> Dynamiska manifest ändrar inte till gången och standard manifestet för till gången.

## <a name="overview-of-manifests"></a>Översikt över manifest

Azure Media Services stöder HLS, MPEG-streck och Smooth Streaming protokoll. Som en del av en [dynamisk paketering](dynamic-packaging-overview.md)genereras direkt uppspelnings klientens manifest (HLS Master Playlist, streck medie beskrivning [mpd] och Smooth Streaming) dynamiskt baserat på format väljaren i URL: en. Mer information finns i leverans protokollen i [vanliga arbets flöden på begäran](dynamic-packaging-overview.md#to-prepare-your-source-files-for-delivery).

### <a name="get-and-examine-manifest-files"></a>Hämta och undersök manifest filen

Du kan ange en lista över villkor för filter spårnings egenskaper baserat på vilka spår i data strömmen (Live eller video på begäran [VOD]) som ska ingå i ett dynamiskt skapat manifest. Om du vill hämta och undersöka egenskaperna för spåren måste du först läsa in Smooth Streaming manifestet.

Självstudien [Ladda upp, koda och strömma filer med .net](stream-files-tutorial-with-api.md#get-streaming-urls) visar hur du skapar strömmande URL: er med .net. Om du kör appen pekar en av webb adresserna på Smooth Streaming manifestet: `https://amsaccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-0000000000000/ignite.ism/manifest` .<br/> Kopiera och klistra in webb adressen i adress fältet i en webbläsare. Filen kommer att hämtas. Du kan öppna den i valfri text redigerare.

Ett REST-exempel finns i [Ladda upp, koda och strömma filer med rest](stream-files-tutorial-with-rest.md#list-paths-and-build-streaming-urls).

### <a name="monitor-the-bitrate-of-a-video-stream"></a>Övervaka bit hastigheten för en video ström

Du kan använda [sidan Azure Media Player demonstration](https://aka.ms/azuremediaplayer) för att övervaka bit hastigheten för en video ström. På sidan demonstration visas diagnostikinformation på fliken **diagnostik** :

![Azure Media Player diagnostik][amp_diagnostics]

### <a name="examples-urls-with-filters-in-query-string"></a>Exempel: URL: er med filter i frågesträng

Du kan använda filter på ABR streaming-protokoll: HLS, MPEG-streck och Smooth Streaming. I följande tabell visas några exempel på URL: er med filter:

|Protokoll|Exempel|
|---|---|
|HLS|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=m3u8-aapl,filter=myAccountFilter)`|
|MPEG DASH|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(format=mpd-time-csf,filter=myAssetFilter)`|
|Smooth Streaming|`https://amsv3account-usw22.streaming.media.azure.net/fecebb23-46f6-490d-8b70-203e86b0df58/bigbuckbunny.ism/manifest(filter=myAssetFilter)`|

## <a name="rendition-filtering"></a>Åter givnings filtrering

Du kan välja att koda till gången till flera kodnings profiler (H. 264 bas linje, H. 264 hög, AACL, AACH, Dolby Digital Plus) och flera bit hastigheter. Alla klient enheter kommer dock inte att ha stöd för alla dina till gångs profiler och bit hastigheter. Äldre Android-enheter stöder till exempel endast H. 264-bas linje + AACL. Att skicka högre bit hastigheter till en enhet som inte kan få fördelarna med bandbredd och enhets beräkning. En sådan enhet måste avkoda all den information som anges, endast för att skala ned den för visning.

Med dynamiskt manifest kan du skapa enhets profiler (till exempel mobil, konsol eller HD/SD) och ta med de spår och kvaliteter som du vill ska vara en del av varje profil. Det kallas för åter givnings filtrering. I följande diagram visas ett exempel på det.

![Exempel på åter givnings filtrering med dynamiskt manifest][renditions2]

I följande exempel användes en kodare för att koda en mezzaninfil till sju ISO hastigheter video åter givningar (från 180p till 1080p). Kodad till gång kan [paketeras dynamiskt](dynamic-packaging-overview.md) i något av följande strömnings protokoll: HLS, MPEG-streck och smidighet.

Överst i följande diagram visas HLS-manifestet för till gången utan filter. (Den innehåller alla sju åter givningar.)  I det nedre vänstra hörnet visar diagrammet ett HLS-manifest där ett filter med namnet "ott" tillämpades. Filtret "ott" anger borttagning av alla bit hastigheter under 1 Mbit/s, så de nedre två kvalitets nivåerna har tagits bort i svaret. I det nedre högra hörnet visar diagrammet HLS-manifestet där ett filter med namnet "Mobile" tillämpades. Filtret "mobil" anger borttagning av renderingar där upplösningen är större än 720p, så de två 1080p-återgivningarna har tagits bort.

![Rendera filtrering med dynamiskt manifest][renditions1]

## <a name="removing-language-tracks"></a>Ta bort språk spår
Dina till gångar kan innehålla flera ljud språk, till exempel engelska, spanska, franska och så vidare. Normalt hanterar Player SDK standard val av ljud spår och tillgängliga ljud spår per val av användare.

Det är svårt att utveckla sådana SDK-SDK: er eftersom det krävs olika implementeringar i de enhetsspecifika Player-ramverken. På vissa plattformar är Player-API: erna begränsade och innehåller inte funktionen för ljud markering där användarna inte kan välja eller ändra standard ljud spåret. Med till gångs filter kan du styra beteendet genom att skapa filter som bara innehåller önskade ljud språk.

![Filtrering av språk spår med dynamiskt manifest][language_filter]

## <a name="trimming-the-start-of-an-asset"></a>Trimma starten av en till gång

I de flesta direkt uppspelnings händelser kör operatörer vissa tester före den faktiska händelsen. De kan till exempel innehålla ett sådant som det här innan händelsen startades: "programmet kommer att starta tillfälligt".

Om programmet arkiveras, arkiveras även test-och bakgrunds data och tas med i presentationen. Den här informationen bör dock inte visas för klienterna. Med dynamiskt manifest kan du skapa ett start tids filter och ta bort oönskade data från manifestet.

![Trimning av början av en till gång med dynamiskt manifest][trim_filter]

## <a name="creating-subclips-views-from-a-live-archive"></a>Skapa under klipp (vyer) från ett Live-Arkiv

Många Live-händelser körs länge och Live-arkivet kan innehålla flera händelser. När Live-händelsen är slut kan det hända att sändningarna vill dela upp Live-arkivet i logiska programs start-och stopp sekvenser.

Du kan publicera de här virtuella programmen separat utan att bearbeta bearbetningen av Live-arkivet och inte skapa separata till gångar (som inte får fördelarna med befintliga cachelagrade fragment i CDN). Exempel på sådana virtuella program är kvartalen för ett fotbolls-eller basket boll spel, innings i baseboll eller enskilda idrotts program.

Med dynamiskt manifest kan du skapa filter genom att använda start-och slut tider och skapa virtuella vyer överst i ditt Live-Arkiv.

![Under klipps filter med dynamiskt manifest][subclip_filter]

Här är den filtrerade till gången:

![Filtrerad till gång med dynamiskt manifest][skiing]

## <a name="adjusting-the-presentation-window-dvr"></a>Justera presentations fönstret (DVR)

Azure Media Services erbjuder för närvarande ett cirkulärt arkiv där varaktigheten kan konfigureras mellan 1 minut-25 timmar. Manifest filtrering kan användas för att skapa ett rullande DVR-fönster överst i arkivet, utan att ta bort mediet. Det finns många scenarier där sändningarna vill ge ett begränsat DVR-fönster att flyttas med Live Edge och samtidigt behålla ett större Arkiv fönster. En utsändare kan vilja använda de data som är av DVR-fönstret för att markera klipp, eller så kanske de vill ge olika DVR-fönster för olika enheter. De flesta av de mobila enheterna hanterar till exempel inte stora DVR-fönster (du kan ha ett DVR-fönster på 2 minuter för mobila enheter och en timme för Skriv bords klienter).

![DVR-fönster med dynamiskt manifest][dvr_filter]

## <a name="adjusting-livebackoff-live-position"></a>Justera LiveBackoff (Live position)

Manifest filtrering kan användas för att ta bort flera sekunder från Live Edge i ett Live-program. Filtrering tillåter att sändningarna tittar på presentationen på publikationens publicerings plats och skapar annons insättnings punkter innan visnings programmen tar emot data strömmen (med utgångs punkt från 30 sekunder). Sedan kan de sända dessa annonser till deras klient ramverk för att kunna ta emot och bearbeta informationen innan annonsen är aktiv.

Förutom stöd för annons hantering kan Live-off-inställningen användas för att justera visnings läget, så att när klienterna når och når Live-gränsen kan de fortfarande få fragment från servern. På så sätt får klienterna inget HTTP 404-eller 412-fel.

![Filtrera för Live-återställning med dynamiskt manifest][livebackoff_filter]

## <a name="combining-multiple-rules-in-a-single-filter"></a>Kombinera flera regler i ett enda filter

Du kan kombinera flera filtrerings regler i ett enda filter. Du kan till exempel definiera en "intervall regel" för att ta bort bakgrunds värden från ett Live-Arkiv och även filtrera ut tillgängliga bit hastigheter. När du använder flera filtrerings regler är slut resultatet skärnings punkten för alla regler.

![Flera filtrerings regler med dynamiskt manifest][multiple-rules]

## <a name="combining-multiple-filters-filter-composition"></a>Kombinera flera filter (filter komposition)

Du kan också kombinera flera filter i en enda URL. Följande scenario visar varför du kanske vill kombinera filter:

1. Du måste filtrera dina video egenskaper för mobila enheter, t. ex. Android eller iPad (för att begränsa video kvaliteterna). Om du vill ta bort oönskade kvaliteter skapar du ett konto filter som är lämpligt för enhets profilerna. Du kan använda konto filter för alla till gångar under samma Media Services konto utan någon ytterligare Association.
1. Du vill också trimma start-och slut tid för en till gång. Om du vill göra en trimning skapar du ett till gångs filter och anger start-/slut tiden.
1. Du vill kombinera båda dessa filter. Utan kombination skulle du behöva lägga till kvalitets filtrering till trimnings filtret, vilket skulle göra filter användningen svårare.

Om du vill kombinera filter ställer du in filter namnen på URL: en för manifest/spelnings lista i semikolon-avgränsat format. Vi antar att du har ett filter med namnet *MyMobileDevice* som filtrerar kvaliteterna och att du har en annan namngiven *MyStartTime* för att ange en angiven start tid. Du kan kombinera upp till tre filter.

Mer information finns i [det här blogginlägget](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

## <a name="considerations-and-limitations"></a>Överväganden och begränsningar

- Värdena för **forceEndTimestamp**, **presentationWindowDuration** och **liveBackoffDuration** bör inte anges för ett VOD-filter. De används bara för direkt filter scenarier.
- Ett dynamiskt manifest fungerar i GOP-gränser (nyckel ramar), så trimning har GOP precision.
- Du kan använda samma filter namn för konto-och till gångs filter. Till gångs filter har högre prioritet och kommer att åsidosätta konto filter.
- Om du uppdaterar ett filter kan det ta upp till 2 minuter för strömnings slut punkten att uppdatera reglerna. Om du använde filter för att hantera innehållet (och du cachelagrade innehållet i proxyservrar och CDN-cacheminnen), kan uppdatering av dessa filter leda till spelarens problem. Vi rekommenderar att du rensar cachen när du har uppdaterat filtret. Om det här alternativet inte är möjligt bör du överväga att använda ett annat filter.
- Kunder måste hämta manifestet manuellt och analysera den exakta start tids stämplingen och tids skalan.

    - [Hämta och granska manifest filen](#get-and-examine-manifest-files)för att fastställa egenskaper för spåren i en till gång.
    - Formeln för att ange tids stämplings egenskaper för till gångs filter är: <br/>startTimestamp = &lt; Start tid i manifestet &gt;  +   &lt; förväntat filter start tid i sekunder &gt; * tids skala

## <a name="next-steps"></a>Nästa steg

Följande artiklar visar hur du skapar filter program mässigt:  

- [Skapa filter med REST API: er](filters-dynamic-manifest-rest-howto.md)
- [Skapa filter med .NET](filters-dynamic-manifest-dotnet-howto.md)
- [Skapa filter med CLI](filters-dynamic-manifest-cli-howto.md)

[renditions1]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter.png
[renditions2]: ./media/filters-dynamic-manifest-overview/media-services-rendition-filter2.png

[rendered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[timeline_trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[timeline_trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png

[multiple-rules]:./media/filters-dynamic-manifest-overview/media-services-multiple-rules-filters.png

[subclip_filter]: ./media/filters-dynamic-manifest-overview/media-services-subclips-filter.png
[trim_event]: ./media/filters-dynamic-manifests/media-services-timeline-trim-event.png
[trim_subclip]: ./media/filters-dynamic-manifests/media-services-timeline-trim-subclip.png
[trim_filter]: ./media/filters-dynamic-manifest-overview/media-services-trim-filter.png
[redered_subclip]: ./media/filters-dynamic-manifests/media-services-rendered-subclip.png
[livebackoff_filter]: ./media/filters-dynamic-manifest-overview/media-services-livebackoff-filter.png
[language_filter]: ./media/filters-dynamic-manifest-overview/media-services-language-filter.png
[dvr_filter]: ./media/filters-dynamic-manifest-overview/media-services-dvr-filter.png
[skiing]: ./media/filters-dynamic-manifest-overview/media-services-skiing.png
[amp_diagnostics]: ./media/filters-dynamic-manifest-overview/amp_diagnostics.png
