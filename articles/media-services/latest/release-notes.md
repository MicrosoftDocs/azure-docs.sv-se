---
title: Viktig information om Azure Media Services v3
description: Den här artikeln innehåller de senaste uppdateringarna för Azure Media Services v3 för att hålla dig uppdaterad med den senaste utvecklingen.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: na
ms.topic: article
ms.date: 10/21/2020
ms.author: inhenkel
ms.openlocfilehash: 2254762ec2cc9d99946afaef12f452fdfbfea9e2
ms.sourcegitcommit: 100390fefd8f1c48173c51b71650c8ca1b26f711
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98898247"
---
# <a name="azure-media-services-v3-release-notes"></a>Viktig information om Azure Media Services v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

>Bli informerad om när du ska gå tillbaka till den här sidan för uppdateringar genom att kopiera och klistra in den här URL: en `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+v3+release+notes%22&locale=en-us` i din RSS-feed läsare.

För att hålla dig uppdaterad med den senaste utvecklingen ger den här artikeln information om:

* De senaste versionerna
* Kända problem
* Felkorrigeringar
* Föråldrade funktioner

## <a name="known-issues"></a>Kända problem

> [!NOTE]
> Du kan använda [Azure Portal](https://portal.azure.com/) för att hantera v3 [Live-händelser](live-events-outputs-concept.md), Visa v3- [till gångar](assets-concept.md) och jobb, hämta information om åtkomst till API: er, Kryptera innehåll. För alla andra hanterings aktiviteter (till exempel Hantera transformeringar och jobb) använder du [REST API](/rest/api/media/accountfilters), [CLI](/cli/azure/ams)eller någon av de [SDK](media-services-apis-overview.md#sdks): er som stöds.
>
> Mer information finns i: [Azure Portal begränsningar för Media Services v3](frequently-asked-questions.md#what-are-the-azure-portal-limitations-for-media-services-v3).

## <a name="december-2020"></a>December 2020

### <a name="regional-availability"></a>Regional tillgänglighet

Azure Media Services är nu tillgängligt i regionen Norge, östra i Azure Portal.  Det finns ingen restV2 i den här regionen.

## <a name="october-2020"></a>Oktober 2020

### <a name="basic-audio-analysis"></a>Grundläggande ljud analys

För inställningen för ljud analys innehåller nu en pris nivå för Basic-läge. Det nya grundläggande ljud analys läget ger dig ett billigt alternativ för att extrahera tal avskrifter och för att formatera utdata och under texter. I det här läget utförs tal-till-text-avskrift och generering av en VTT-under text fil. Utdata från det här läget är en JSON-fil för insikter som bara innehåller nyckelord, avskrifter och tids inställnings information. Automatisk språk identifiering och talare diarization ingår inte i det här läget. Se listan över [språk som stöds.](analyzing-video-audio-files-concept.md#built-in-presets)

Kunder som använder Indexer v1 och Indexer v2 bör migrera till den grundläggande inställningen för ljud analys.

Mer information om Basic Audio Analyzer-läget finns i [analys av video-och ljudfiler](analyzing-video-audio-files-concept.md).  Information om hur du använder Basic Audio Analyzer-läget med REST API finns i [så här skapar du en grundläggande ljud omvandling](how-to-create-basic-audio-transform.md).

### <a name="live-events"></a>Livehändelser

Uppdateringar av de flesta egenskaper tillåts nu när Live-händelser stoppas. Dessutom kan användare ange ett prefix för det statiska värd namnet för Live Events URL: er för inaktuella indatamängds-och för hands versioner. VanityUrl anropas nu `useStaticHostName` för att bättre avspegla syftet med egenskapen.

Live-händelser har nu ett vänte läge.  Se [Live-händelser och Live-utdata i Media Services](./live-events-outputs-concept.md).

En Live-händelse stöder mottagning av olika proportioner för indataport. Med utsträcknings läge kan kunderna ange utsträcknings beteendet för utdata.

Med Live encoding kan du nu lägga till funktioner för att placera fragment i fasta nyckel intervall mellan 0,5 och 20 sekunder.

### <a name="accounts"></a>Konton

> [!WARNING]
> Om du skapar ett Media Services-konto med 2020-05-01 API-versionen fungerar det inte med RESTv2 

## <a name="august-2020"></a>Augusti 2020

### <a name="dynamic-encryption"></a>Dynamisk kryptering
Stöd för äldre PlayReady Protected PIFF-kryptering (1,1) är nu tillgängligt i den dynamiska Paketeraren. Detta ger stöd för äldre Smart TV-uppsättningar från Samsung och LG som implementerade de tidiga utkasten av Common Encryption Standard (CENC) som publicerats av Microsoft.  PIFF 1,1-formatet kallas även krypterings format som tidigare stöddes av Silverlight-klientcertifikatet. Idag är det enda användnings Falls scenariot för det här krypterings formatet att rikta in sig på den äldre marknaden för smart TV där det finns ett icke-trivialt antal smarta TV-apparater i vissa regioner som endast stöder Smooth Streaming med PIFF 1,1-kryptering. 

Om du vill använda det nya PIFF 1,1-krypterings stödet ändrar du kryptering svärdet till "Piff" i URL-sökvägen för den strömmande lokaliseraren. Mer information finns i [Översikt över Content Protection.](content-protection-overview.md)
Exempel: `https://amsv3account-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/ignite.ism/manifest(encryption=piff)`|

> [!NOTE]
> PIFF 1,1-support tillhandahålls som en bakåtkompatibla lösning för smart TV (Samsung, LG) som implementerade den tidiga "Silverlight-versionen av Common Encryption. Vi rekommenderar att du bara använder det PIFF-format som krävs för att stöda äldre Samsung eller LG Smart-TV levereras mellan 2009-2015 som har stöd för PIFF 1,1-versionen av PlayReady-kryptering. 

## <a name="july-2020"></a>Juli 2020

### <a name="live-transcriptions"></a>Direkt avskrifter

Direkt avskrifter stöder nu 19 språk och åtta regioner.

### <a name="protecting-your-content-with-media-services-and-azure-ad"></a>Skydda ditt innehåll med Media Services och Azure AD

Vi har publicerat en självstudie [som kallas slut punkt till slut punkt för innehålls skydd med hjälp av Azure AD](./azure-ad-content-protection.md).

### <a name="high-availability"></a>Hög tillgänglighet

Vi har publicerat en hög tillgänglighet med Media Services-och VOD- [Översikt](./media-services-high-availability-encoding.md) och- [exempel](https://github.com/Azure-Samples/media-services-v3-dotnet/tree/master/HighAvailabilityEncodingStreaming).

## <a name="june-2020"></a>Juni 2020

### <a name="live-video-analytics-on-iot-edge-preview-release"></a>Video analys i real tid för IoT Edge för hands version

För hands versionen av video analys på IoT Edge gick att publicera. Mer information finns i [versions anteckningar](../live-video-analytics-edge/release-notes.md).

Live video analys på IoT Edge är en expansion till medie tjänst familjen. Det gör att du kan analysera direktsänd video med AI-modeller som du väljer på dina egna gräns enheter och eventuellt fånga och spela in videon. Nu kan du skapa appar med video analys i real tid utan att bekymra dig om komplexiteten med att bygga och driva en live video-pipeline.

## <a name="may-2020"></a>Maj 2020

Azure Media Services är nu allmänt tillgänglig i följande regioner: "Tyskland, norra", "Tyskland, västra centrala", "Schweiz, norra" och "Schweiz, västra". Kunder kan distribuera Media Services till dessa regioner med hjälp av Azure Portal.

## <a name="april-2020"></a>April 2020

### <a name="improvements-in-documentation"></a>Förbättringar i dokumentation

Azure Media Player dokument migrerades till [Azure-dokumentationen](../azure-media-player/azure-media-player-overview.md).

## <a name="january-2020"></a>Januari 2020

### <a name="improvements-in-media-processors"></a>Förbättringar i medie processorer

- Förbättrat stöd för sammanflätade källor i video analys – det här innehållet är nu sammanflätat på rätt sätt innan de skickas till en härlednings motor.
- När du skapar miniatyrer med "bästa" läge söker kodaren nu över 30 sekunder för att välja en ram som inte är monokrom.

### <a name="azure-government-cloud-updates"></a>Azure Government moln uppdateringar

Media Services GA'ed i följande Azure Government regioner: *USGov Arizona* och *USGov Texas*.

## <a name="december-2019"></a>December 2019

Stöd för CDN-stöd har lagts till för *ursprungligt stöd för hämtnings* rubriker för både Live och video på begäran. tillgängligt för kunder som har direkt kontrakt med Akamai CDN. Origin-Assist CDN-Prefetch-funktionen omfattar följande HTTP-huvud utbyten mellan Akamai CDN och Azure Media Services ursprung:

|HTTP-huvud|Värden|Avsändare|Mottagare|Syfte|
| ---- | ---- | ---- | ---- | ----- |
|CDN-Origin-Assist-prefetch-Enabled | 1 (standard) eller 0 |CDN|Ursprung|För att indikera att CDN är för hämtning aktiverat|
|CDN-Origin-Assist-för-hämtning-sökväg| Exempel: <br/>Fragment (video = 1400000000, format = mpd-Time-cmaf)|Ursprung|CDN|Ange sökväg för hämtning till CDN|
|CDN-Origin-Assist-för hämtning-begäran|1 (för hämtnings förfrågan) eller 0 (vanlig begäran)|CDN|Ursprung|För att ange att begäran från CDN är en för hämtning|

Du kan prova följande steg för att se en del av det att huvud utbytet i fungerar:

1. Använd Postman eller sväng för att skicka en begäran till Media Services ursprung för ett ljud-eller video segment eller fragment. Se till att lägga till huvudet CDN-Origin-Assist-för-Fetch-Enabled: 1 i begäran.
2. I svaret bör du se rubriken CDN-Origin-support-prefetch-Path med en relativ sökväg som sitt värde.

## <a name="november-2019"></a>November 2019

### <a name="live-transcription-preview"></a>Förhands granskning av direktsänd skrift

Direkt avskrift är nu i en offentlig för hands version och är tillgänglig för användning i regionen USA, västra 2.

Direkt avskrift har utformats för att fungera tillsammans med Live-händelser som en tilläggs funktion.  Den stöds både genom direkt-och standard-eller Premium-kodning av Live-händelser.  När den här funktionen är aktive rad använder tjänsten funktionen [tal-till-text](../../cognitive-services/speech-service/speech-to-text.md) i Cognitive Services för att skriva över talade ord i inkommande ljud till text. Den här texten görs sedan tillgänglig för leverans tillsammans med video och ljud i MPEG-streck och HLS-protokoll. Faktureringen baseras på en ny indikator för tillägg som är extra kostnad för Live-händelsen när den är i läget "körs".  Mer information om direkt avskrift och fakturering finns i [direkt skriftering](live-transcription.md)

> [!NOTE]
> Direkt avskrift är för närvarande endast tillgängligt som en förhands gransknings funktion i regionen USA, västra 2. Det stöder avskriftering av talade ord på engelska (en-US) endast för tillfället.

### <a name="content-protection"></a>Innehållsskydd

Funktionen för att *förhindra repetition av token* som släpps i begränsade regioner i september är nu tillgänglig i alla regioner.
Media Services kunder kan nu ange en gräns för hur många gånger samma token kan användas för att begära en nyckel eller en licens. Mer information finns i avsnittet om att [förhindra repetition av token](content-protection-overview.md#token-replay-prevention).

### <a name="new-recommended-live-encoder-partners"></a>Nya rekommenderade Live Encoder-partner

Stöd har lagts till för följande nya rekommenderade partner kodare för RTMP Live streaming:

- [Cambria Live 4,3](https://www.capellasystems.net/products/cambria-live/)
- [GoPro Hero7/8 och max.](https://gopro.com/help/articles/block/getting-started-with-live-streaming)
- [Restream.io](https://restream.io/)

### <a name="file-encoding-enhancements"></a>Fil kodnings förbättringar

- Nu är en ny innehålls medveten kodnings inställning tillgänglig. Den skapar en uppsättning GOP hastigheter med hjälp av innehålls medveten kodning. Med allt inmatat innehåll utför tjänsten en inledande, förenklad analys av indatamängds innehåll. Den använder dessa resultat för att fastställa det optimala antalet lager, lämplig bit hastighet och upplösnings inställningar för leverans av anpassad direkt uppspelning. Den här för inställningen är särskilt effektiv för video med låg komplexitet och medelhög komplexitet, där utdatafilerna är i lägre bit hastigheter men till en kvalitet som fortfarande ger en bra upplevelse av läsarna. Utdata kommer att innehålla MP4-filer med video och ljud som är överlagrade. Mer information finns i de [öppna API-specifikationerna](https://github.com/Azure/azure-rest-api-specs/blob/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01/Encoding.json).
- Bättre prestanda och multi-threading för storleks pekaren i Standard-kodaren. Under vissa villkor bör kunden se en prestanda ökning mellan 5-40% VOD kodning. Låg komplexitets information som är kodad till flera bit hastigheter ser den högsta prestandan ökar. 
- Standard kodningen innehåller nu en vanlig GOP-takt för VFR-innehåll (variabeln Frame rate) under VOD encoding när den tidsbaserade GOP-inställningen används.  Det innebär att kunden skickar blandat bild Rute frekvens innehåll som varierar mellan 15-30 fps, till exempel bör nu se vanliga GOP avstånd som beräknas på utdata till strömmande MP4-filer med anpassningsbar bit hastighet. Detta förbättrar möjligheten att växla sömlöst mellan spår när du levererar över HLS eller tank streck. 
-  Förbättrad AV AV-synkronisering för VFR-käll innehåll (Variable Frame rate)

### <a name="video-indexer-video-analytics"></a>Video Indexer, video analys

- Nyckel rutor som extraheras med VideoAnalyzer-förinställningarna finns nu i den ursprungliga upplösningen för videon i stället för att storleks änd ras. Extrahering av nyckel rutor med hög upplösning ger dig ursprungliga kvalitets avbildningar och gör att du kan använda de bildbaserade, artificiell intelligens-modeller som tillhandahålls av Microsoft Visuellt innehåll och Custom Vision-tjänster för att få ännu mer insikter från din video.

## <a name="september-2019"></a>September 2019

###  <a name="media-services-v3"></a>Media Services v3  

#### <a name="live-linear-encoding-of-live-events"></a>Direkt linjär kodning av Live-händelser

Media Services v3 presenterar för hands versionen av 24 timmar x 365 dagar av direkt linjär kodning av direktsända händelser.

###  <a name="media-services-v2"></a>Media Services v2  

#### <a name="deprecation-of-media-processors"></a>Utfasning av medie processorer

Vi presenterar utfasning av *Azure Media Indexer* och *Azure Media Indexer 2 för hands version*. Information om hur datum för indragningen går till finns i artikeln om  [äldre komponenter](../previous/legacy-components.md) . [Azure Media Services video Indexer](../video-indexer/index.yml) ersätter dessa äldre medie processorer.

Mer information finns i [Migrera från Azure Media Indexer och Azure Media Indexer 2 till Azure Media Services video Indexer](../previous/migrate-indexer-v1-v2.md).

## <a name="august-2019"></a>Augusti 2019

###  <a name="media-services-v3"></a>Media Services v3  

#### <a name="south-africa-regional-pair-is-open-for-media-services"></a>Södra Afrika, regionala par är öppet för Media Services 

Media Services är nu tillgängligt i södra Afrika, norra och södra Afrika, västra regioner.

Mer information finns i [moln och regioner där Media Services v3 finns](azure-clouds-regions.md).

###  <a name="media-services-v2"></a>Media Services v2  

#### <a name="deprecation-of-media-processors"></a>Utfasning av medie processorer

Vi presenterar utfasningen av medie processorerna för *Windows Azure Media Encoder* (WAME) och *Azure Media Encoder* (amn), som dras tillbaka. I [den här artikeln](../previous/legacy-components.md) om inaktuella indragnings datum.

Mer information finns i [MIGRERA WAME till Media Encoder Standard](../previous/migrate-windows-azure-media-encoder.md) och [migrera amn till Media Encoder Standard](../previous/migrate-azure-media-encoder.md).
 
## <a name="july-2019"></a>Juli 2019

### <a name="content-protection"></a>Innehållsskydd

När strömmat innehåll skyddas med begränsning av token måste slutanvändare hämta en token som skickas som en del av begäran om nyckel leverans. Funktionen för att *förhindra repetition av token* tillåter Media Services kunder att ange en gräns för hur många gånger samma token kan användas för att begära en nyckel eller en licens. Mer information finns i avsnittet om att [förhindra repetition av token](content-protection-overview.md#token-replay-prevention).

Från och med juli var för hands versions funktionen bara tillgänglig i USA, Central och USA, västra centrala.

## <a name="june-2019"></a>Juni 2019

### <a name="video-subclipping"></a>Video under Urklipp

Nu kan du trimma eller under klipp en video när du kodar den med ett [jobb](/rest/api/media/jobs). 

Den här funktionen fungerar med alla [transformeringar](/rest/api/media/transforms) som har skapats med antingen [BuiltInStandardEncoderPreset](/rest/api/media/transforms/createorupdate#builtinstandardencoderpreset) för inställningar eller [StandardEncoderPreset](/rest/api/media/transforms/createorupdate#standardencoderpreset) för inställningar. 

Se exempel:

* [Klipp en video med .NET](subclip-video-dotnet-howto.md)
* [Klipp en video med REST](subclip-video-rest-howto.md)

## <a name="may-2019"></a>Maj 2019

### <a name="azure-monitor-support-for-media-services-diagnostic-logs-and-metrics"></a>Azure Monitor stöd för Media Services diagnostikloggar och mått

Du kan nu använda Azure Monitor för att Visa telemetri-data som har avsänts av Media Services.

* Använd Azure Monitor diagnostikloggar för att övervaka begär Anden som skickas av Media Services Key Delivery-slutpunkten. 
* Övervaka Mät värden som genereras av Media Services [slut punkter för direkt uppspelning](streaming-endpoint-concept.md).   

Mer information finns i [övervaka Media Services statistik och diagnostikloggar](media-services-metrics-diagnostic-logs.md).

### <a name="multi-audio-tracks-support-in-dynamic-packaging"></a>Stöd för flera ljud spår i dynamisk paketering 

När strömmande till gångar som har flera ljud spår med flera codecenheter och språk, stöder [dynamisk paketering](dynamic-packaging-overview.md) nu flera ljud spår för HLS-utdata (version 4 eller senare).

### <a name="korea-regional-pair-is-open-for-media-services"></a>Det regionala paret i Korea är öppet för Media Services 

Media Services är nu tillgängligt i regionerna Korea, centrala och Korea, södra. 

Mer information finns i [moln och regioner där Media Services v3 finns](azure-clouds-regions.md).

### <a name="performance-improvements"></a>Prestandaförbättringar

Nya uppdateringar som omfattar Media Services prestanda förbättringar.

* Den maximala fil storleken som stöds för bearbetning har uppdaterats. Se, [kvoter och begränsningar](limits-quotas-constraints.md).
* [Förbättringar i kodnings hastigheten](concept-media-reserved-units.md).

## <a name="april-2019"></a>April 2019

### <a name="new-presets"></a>Nya för inställningar

* [FaceDetectorPreset](/rest/api/media/transforms/createorupdate#facedetectorpreset) har lagts till i de inbyggda för hands inställningarna i Analyzer.
* [ContentAwareEncodingExperimental](/rest/api/media/transforms/createorupdate#encodernamedpreset) har lagts till i de inbyggda kodare-för inställningarna. Mer information finns i [Content medveten encoding](content-aware-encoding.md). 

## <a name="march-2019"></a>Mars 2019

Dynamisk paketering stöder nu Dolby Atmos. Mer information finns i [codec för ljud som stöds av dynamisk paketering](dynamic-packaging-overview.md#audio-codecs-supported-by-dynamic-packaging).

Nu kan du ange en lista över till gångs-eller konto filter som ska gälla för din strömmande positionerare. Mer information finns i [associera filter med strömmande positionerare](filters-concept.md#associating-filters-with-streaming-locator).

## <a name="february-2019"></a>Februari 2019

Media Services v3 stöds nu i Azure National-moln. Alla funktioner är inte tillgängliga i alla moln än. Mer information finns i [moln och regioner där Azure Media Services v3 finns](azure-clouds-regions.md).

[Microsoft. Media. JobOutputProgress](media-services-event-schemas.md#monitoring-job-output-progress) -händelsen har lagts till i Azure Event Grid scheman för Media Services.

## <a name="january-2019"></a>Januari 2019

### <a name="media-encoder-standard-and-mpi-files"></a>Media Encoder Standard-och MPI-filer 

När du kodar med Media Encoder Standard för att skapa MP4-filer skapas en ny. MPI-fil och läggs till i utmatnings till gången. Den här MPI-filen är avsedd att förbättra prestanda för dynamiska scenarier för [paketering](dynamic-packaging-overview.md) och strömning.

Du bör inte ändra eller ta bort MPI-filen, eller ta bort beroendet i din tjänst om det finns en sådan fil.

## <a name="december-2018"></a>December 2018

Uppdateringar från GA-versionen av v3-API: et är:
       
* **PresentationTimeRange** -egenskaperna är inte längre obligatoriska för **till gångs filter** och **konto filter**. 
* Alternativen $top och $skips fråga för **jobb** och **transformeringar** har tagits bort och $OrderBy lagts till. Som en del av att lägga till nya funktioner för beställning upptäcktes att $top-och $skip alternativen hade kunnat exponeras tidigare trots att de inte har implementerats.
* Utökningen av uppräkningen aktiverades igen. Den här funktionen har Aktiver ATS i för hands versionerna av SDK och har inaktiverats av misstag i GA-versionen.
* Två fördefinierade strömmande principer har bytt namn. **SecureStreaming** är nu **MultiDrmCencStreaming**. **SecureStreamingWithFairPlay** är nu **Predefined_MultiDrmStreaming**.

## <a name="november-2018"></a>November 2018

Modulen CLI 2,0 är nu tillgänglig för [Azure Media Services v3 ga](/cli/azure/ams?view=azure-cli-latest&preserve-view=true) – v 2.0.50.

### <a name="new-commands"></a>Nya kommandon

- [AZ AMS-konto](/cli/azure/ams/account?view=azure-cli-latest&preserve-view=true)
- [AZ AMS-konto – filter](/cli/azure/ams/account-filter?view=azure-cli-latest&preserve-view=true)
- [AZ AMS-till gång](/cli/azure/ams/asset?view=azure-cli-latest&preserve-view=true)
- [AZ AMS Asset-filter](/cli/azure/ams/asset-filter?view=azure-cli-latest&preserve-view=true)
- [AZ AMS-innehåll – nyckel-princip](/cli/azure/ams/content-key-policy?view=azure-cli-latest&preserve-view=true)
- [AZ AMS-jobb](/cli/azure/ams/job?view=azure-cli-latest&preserve-view=true)
- [AZ AMS live-event](/cli/azure/ams/live-event?view=azure-cli-latest&preserve-view=true)
- [AZ AMS Live-output](/cli/azure/ams/live-output?view=azure-cli-latest&preserve-view=true)
- [AZ AMS-direktuppspelning – slut punkt](/cli/azure/ams/streaming-endpoint?view=azure-cli-latest&preserve-view=true)
- [AZ AMS streaming-Locator](/cli/azure/ams/streaming-locator?view=azure-cli-latest&preserve-view=true)
- [AZ AMS Account MRU](/cli/azure/ams/account/mru?view=azure-cli-latest&preserve-view=true) – gör att du kan hantera enheter som är reserverade för media. Mer information finns i [skala medie reserverade enheter](media-reserved-units-cli-how-to.md).

### <a name="new-features-and-breaking-changes"></a>Nya funktioner och större ändringar

#### <a name="asset-commands"></a>Till gångs kommandon

- ```--storage-account``` och ```--container``` argument har lagts till.
- Standardvärden för förfallo tid (nu + 23h) och behörigheter (läsa) i ```az ams asset get-sas-url``` kommandot tillagt.

#### <a name="job-commands"></a>Jobb kommandon

- ```--correlation-data``` och ```--label``` tillagda argument
- ```--output-asset-names``` har bytt namn till ```--output-assets``` . Nu accepterar den en blankstegsavgränsad lista med till gångar i formatet "assetName = Label". En till gång utan etikett kan skickas så här: "assetName =".

#### <a name="streaming-locator-commands"></a>Kommandon för streaming Locator

- ```az ams streaming locator``` kommandot Base ersattes med ```az ams streaming-locator``` .
- ```--streaming-locator-id``` och ```--alternative-media-id support``` argument har lagts till.
- ```--content-keys argument``` argumentet har uppdaterats.
- ```--content-policy-name``` har bytt namn till ```--content-key-policy-name``` .

#### <a name="streaming-policy-commands"></a>Kommandon för direkt uppspelnings princip

- ```az ams streaming policy``` kommandot Base ersattes med ```az ams streaming-policy``` .
- Krypterings parameter stöd ```az ams streaming-policy create``` har lagts till.

#### <a name="transform-commands"></a>Omvandlings kommandon

- ```--preset-names``` argumentet ersatt med ```--preset``` . Nu kan du bara ange 1 utdata/för inställning i taget (för att lägga till fler du behöver köra ```az ams transform output add``` ). Du kan också ange anpassade StandardEncoderPreset genom att skicka sökvägen till din anpassade JSON.
- ```az ams transform output remove``` kan utföras genom att skicka det utgående index som ska tas bort.
- ```--relative-priority, --on-error, --audio-language and --insights-to-extract``` argument har lagts till i ```az ams transform create``` och- ```az ams transform output add``` kommandon.

## <a name="october-2018---ga"></a>Oktober 2018 – GA

I det här avsnittet beskrivs Azure Media Services (AMS) uppdateringar från oktober.

### <a name="rest-v3-ga-release"></a>REST v3 GA-version

[Rest v3 ga-versionen](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/mediaservices/resource-manager/Microsoft.Media/stable/2018-07-01) innehåller fler API: er för manifest filter för Live, konto/till gångs nivå och DRM-stöd.

#### <a name="azure-resource-management"></a>Resurs hantering i Azure 

Stöd för Azure Resource Management möjliggör Unified Management och Operations-API (nu allt på ett ställe).

Från och med den här versionen kan du använda Resource Manager-mallar för att skapa Live-händelser.

#### <a name="improvement-of-asset-operations"></a>Förbättringar av till gångs åtgärder 

Följande förbättringar introducerades:

- Mata in från HTTP (s) webb adresser eller Azure Blob Storage SAS-URL: er.
- Ange egna behållar namn för till gångar. 
- Enklare stöd för utdata för att skapa anpassade arbets flöden med Azure Functions.

#### <a name="new-transform-object"></a>Nytt Transform-objekt

Det nya **Transform** -objektet fören klar kodnings modellen. Det nya objektet gör det enkelt att skapa och dela koda Resource Manager-mallar och för inställningar. 

#### <a name="azure-active-directory-authentication-and-azure-rbac"></a>Azure Active Directory autentisering och Azure RBAC

Azure AD-autentisering och rollbaserad åtkomst kontroll i Azure (Azure RBAC) möjliggör säker transformering, LiveEvents, innehålls nyckel principer eller till gångar efter roll eller användare i Azure AD.

#### <a name="client-sdks"></a>Klient-SDK: er  

Språk som stöds i Media Services v3: .NET Core, Java, Node.js, ruby, typescript, python, go.

#### <a name="live-encoding-updates"></a>Live encoding-uppdateringar

Följande Live encoding-uppdateringar introduceras:

- Nytt läge för låg latens för Live (10 sekunders slut punkt till slut punkt).
- Förbättrat stöd för RTMP (ökad stabilitet och fler stöd för käll kodare).
- RTMPs säker inmatning.

    När du skapar en Live-händelse får du nu 4 inmatnings-URL: er. De 4 inmatnings-URL: erna är nästan identiska, har samma strömnings-token (AppId), men endast port nummer delen är annorlunda. Två av URL: erna är primära och säkerhets kopiering för RTMP. 
- stöd för 24-timmarsformat. 
- Förbättrat stöd för AD-signalering i RTMP via SCTE35.

#### <a name="improved-event-grid-support"></a>Förbättrat stöd för Event Grid

Du kan se följande Event Grid support förbättringar:

- Azure Event Grid integration för enklare utveckling med Logic Apps och Azure Functions. 
- Prenumerera på händelser om kodning, Live Channels med mera.

### <a name="cmaf-support"></a>CMAF-stöd

Krypterings stöd för CMAF och CBCS för Apple HLS (iOS 11 +) och MPEG-STRECKs pelare som stöder CMAF.

### <a name="video-indexer"></a>Video Indexer

Video Indexer GA-versionen presenterades i augusti. För ny information om funktioner som stöds för närvarande, se [Vad är video Indexer](../video-indexer/video-indexer-overview.md?bc=/azure/media-services/video-indexer/breadcrumb/toc.json&toc=/azure/media-services/video-indexer/toc.json). 

### <a name="plans-for-changes"></a>Planer för ändringar

#### <a name="azure-cli-20"></a>Azure CLI 2.0
 
Azure CLI 2,0-modulen som innehåller åtgärder för alla funktioner (inklusive Live, innehålls nyckel principer, konto-och till gångs filter, strömmande principer) kommer snart. 

### <a name="known-issues"></a>Kända problem

Endast kunder som använde för hands versionen av API för till gångs-eller AccountFilters påverkas av följande problem.

Om du har skapat till gångar eller konto filter mellan 09/28 och 10/12 med Media Services v3 CLI eller API: er, måste du ta bort alla till gångar och AccountFilters och återskapa dem på grund av en versions konflikt. 

## <a name="may-2018---preview"></a>Maj 2018 – för hands version

### <a name="net-sdk"></a>.NET SDK

Följande funktioner finns i .NET SDK:

* **Transformeringar** och **jobb** för att koda eller analysera medie innehåll. Exempel finns i [strömma filer](stream-files-tutorial-with-api.md) och [analysera](analyze-videos-tutorial-with-api.md).
* **Strömmande positionerare** för att publicera och strömma innehåll till slut användar enheter
* **Strömmande principer** och **innehålls nyckel principer** för att konfigurera nyckel leverans och innehålls skydd (DRM) när innehåll levereras.
* **Live-händelser** och **Live-utdata** för att konfigurera inmatning och arkivering av Live streaming-innehåll.
* **Till gångar** för att lagra och publicera medie innehåll i Azure Storage. 
* **Slut punkter för direkt uppspelning** för att konfigurera och skala dynamisk paketering, kryptering och strömning för både Live-och medie innehåll på begäran.

### <a name="known-issues"></a>Kända problem

* När du skickar ett jobb kan du ange att du vill mata in din käll video med hjälp av HTTPS-URL: er, SAS-URL: er eller sökvägar till filer som finns i Azure Blob Storage. För närvarande stöder Media Services v3 inte Chunked Transfer Encoding över HTTPS-URL: er.

## <a name="ask-questions-give-feedback-get-updates"></a>Ställ frågor, ge feedback, hämta uppdateringar

Kolla in [Azure Media Services community](media-services-community.md) -artikeln för att se olika sätt att ställa frågor, lämna feedback och få uppdateringar om Media Services.

## <a name="see-also"></a>Se även

[Vägledning för migrering för att flytta från Media Services v2 till v3](migrate-v-2-v-3-migration-introduction.md).

## <a name="next-steps"></a>Nästa steg

- [Översikt](media-services-overview.md)
- [Uppdateringar för Media Services v3-dokumentation](docs-release-notes.md)
- [Viktig information om Media Services v2](../previous/media-services-release-notes.md)