---
title: Azure Media Services viktig information | Microsoft Docs
description: Den här artikeln visar viktig information om Microsoft Azure Media Services v2.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: media
ms.devlang: dotnet
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 019f0bc98de45fa1fe6f9b8c72ef74beb50ea2c2
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103017366"
---
# <a name="azure-media-services-release-notes"></a>Viktig information om Azure Media Services

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

I den här versionen av Azure Media Services sammanfattas ändringar från tidigare versioner och kända problem.

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen [Media Services v3](../latest/index.yml). Se även [vägledning för migrering från v2 till v3](../latest/migrate-v-2-v-3-migration-introduction.md)

Vi vill höra från våra kunder så att vi kan fokusera på att åtgärda problem som påverkar dig. Om du vill rapportera ett problem eller ställa frågor kan du skicka in ett inlägg i [Azure Media Services MSDN-forumet]. 

## <a name="known-issues"></a><a name="issues"></a>Kända problem
### <a name="media-services-general-issues"></a><a name="general_issues"></a>Media Services allmänna problem

| Problem | Beskrivning |
| --- | --- |
| Det finns flera vanliga HTTP-huvuden i REST API. |Om du utvecklar Media Services program med hjälp av REST API, ser du att några vanliga HTTP-huvudfält (inklusive CLIENT-REQUEST-ID, begärande-ID och retur-CLIENT-REQUEST-ID) inte stöds. Rubrikerna kommer att läggas till i en framtida uppdatering. |
| Procent-encoding är inte tillåtet. |Media Services använder värdet för egenskapen IAssetFile.Name när du skapar URL: er för strömmande innehåll (till exempel `http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters` ). Därför är procent kodning inte tillåten. Värdet för namn egenskapen får inte ha något av följande [%-encoding-reserverade tecken](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters):! * ' ();: @ &= + $,/?% # [] ". Det kan också finnas en "." för fil namns tillägget. |
| ListBlobs-metoden som ingår i Azure Storage SDK version 3. x Miss lyckas. |Media Services genererar SAS-URL: er baserat på [2012-02-12](/rest/api/storageservices/version-2012-02-12) -versionen. Om du vill använda Storage SDK för att lista blobbar i en BLOB-behållare använder du metoden [CloudBlobContainer. ListBlobs](/dotnet/api/microsoft.azure.storage.blob.cloudblobcontainer.listblobs) som ingår i Storage SDK version 2. x. |
| Den Media Services begränsnings metoden begränsar resursanvändningen för program som gör orimliga begär anden till tjänsten. Tjänsten kan returnera HTTP-statuskod "tjänst ej tillgänglig" 503. |Mer information finns i beskrivningen av HTTP-statuskoden 503 i [Media Services fel koder](media-services-encoding-error-codes.md). |
| När du frågar entiteter returneras en gräns på 1 000 entiteter vid en tidpunkt eftersom den offentliga REST-versionen 2 begränsar frågeresultat till 1 000-resultat. |Använd hoppa över och ta (.NET)/Top (REST) enligt beskrivningen i [det här .net-exemplet](media-services-dotnet-manage-entities.md#enumerating-through-large-collections-of-entities) och [det här REST API exemplet](media-services-rest-manage-entities.md#enumerating-through-large-collections-of-entities). |
| Vissa klienter kan komma över ett problem med upprepad tagg i Smooth Streaming manifestet. |Mer information finns i [det här avsnittet](media-services-deliver-content-overview.md#known-issues). |
| Media Services .NET SDK-objekt kan inte serialiseras och därför fungerar det inte med Azure cache för Redis. |Om du försöker serialisera SDK AssetCollection-objektet för att lägga till det i Azure cache för Redis genereras ett undantag. |
|REST API svarar med ett fel meddelande om att "filtret inte kan nås av den här versionen av REST API" vid försök att hämta ett till gångs-eller konto nivå filter.|Filtret har skapats eller ändrats med en senare API-version än vad som används för att hämta filtret. Detta kan inträffa om två API-versioner används av kod eller verktyg som används av kunden.  Den bästa lösningen här är att uppgradera koden eller verktygen för att använda de nyare eller de två API-versionerna.|

## <a name="rest-api-version-history"></a><a name="rest_version_history"></a>REST API versions historik
Information om Media Services REST API versions historik finns i [Azure Media Services REST API referens].

## <a name="february-2021"></a>Februari 2021

### <a name="azure-media-services-v2-api-and-sdks-deprecation-announcement"></a>Azure Media Services v2 API och SDK-utfasnings meddelande

#### <a name="update-your-azure-media-services-rest-api-and-sdks-to-v3-by-29-february-2024"></a>Uppdatera din Azure Media Services REST API och SDK: er till v3 senast den 29 februari 2024

Eftersom version 3 av Azure Media Services REST API-och klient-SDK: er för .NET och Java erbjuder fler funktioner än version 2, kommer vi att ta bort version 2 av Azure Media Services REST API-och klient-SDK: er för .NET och Java. Vi rekommenderar att du gör switchen tidigare för att få bättre fördelar med version 3 av Azure Media Services REST API-och klient-SDK: er för .NET och Java.
Version 3 tillhandahåller:
 
- Real tids support dygnet runt
- ARM-REST-API: er, klient-SDK: er för .NET Core, Node.js, python, Java, go och Ruby.
- Kundhanterade nycklar, betrodd lagrings integrering, stöd för privat länk och [mycket mer](https://review.docs.microsoft.com/en-us/azure/media-services/latest/migrate-v-2-v-3-migration-benefits)

#### <a name="action-required"></a>Åtgärd krävs:

Du kan minimera störningar i arbets belastningarna genom att granska [migreringsguiden](https://go.microsoft.com/fwlink/?linkid=2149150&clcid=0x409) för att överföra koden från version 2 API och SDK: er till version 3 API och SDK före den 29 februari 2024.
**Efter 29 februari 2024** kommer Azure Media Services inte längre att acceptera trafik på version 2 REST API, arm-kontots hanterings-API-version 2015-10-01 eller från version 2 .net-klient-SDK: er. Detta omfattar klient-SDK: er med öppen källkod från tredje part som kan anropa API: t för version 2.  

Se det officiella [uppdaterings meddelandet för Azure](https://azure.microsoft.com/updates/update-your-azure-media-services-rest-api-and-sdks-to-v3-by-29-february-2024/).

## <a name="september-2020"></a>September 2020

Följande v2-egenskaper kommer inte längre att fyllas i med historiska jobb förlopps data:

* [HistoricalEvents](/dotnet/api/microsoft.windowsazure.mediaservices.client.itask.historicalevents)
* [PerfMessage](/dotnet/api/microsoft.windowsazure.mediaservices.client.itask.perfmessage)

Om du vill hämta aktivitets historiken ska du använda v2-jobb meddelanden via Webhooks eller Kömeddelanden med aviserings slut punkter. Mer information finns i:

* [Använd Azure Queue Storage för att övervaka Media Services jobb meddelanden](media-services-dotnet-check-job-progress-with-queues.md)
* [Använd Azure Webhooks för att övervaka Media Services jobb meddelanden](media-services-dotnet-check-job-progress-with-webhooks.md)

## <a name="february-2020"></a>Februari 2020

Vissa analys medie processorer kommer att dras tillbaka. Information om datum för indragning finns i avsnittet om [äldre komponenter](legacy-components.md) .

## <a name="september-2019"></a>September 2019

### <a name="deprecation-of-media-processors"></a>Utfasning av medie processorer

Vi presenterar utfasning av *Azure Media Indexer* och *Azure Media Indexer 2 för hands version*. [Azure Media Services video Indexer](../video-indexer/index.yml) ersätter dessa äldre medie processorer.

Se det här avsnittet om [äldre komponenter](legacy-components.md) för datum för indragningen.

Se även [Migrera från Azure Media Indexer och Azure Media Indexer 2 för att Azure Media Services video Indexer](migrate-indexer-v1-v2.md).

## <a name="august-2019"></a>Augusti 2019

### <a name="deprecation-of-media-processors"></a>Utfasning av medie processorer

Vi presenterar utfasningen av medie processorerna för *Windows Azure Media Encoder* (WAME) och *Azure Media Encoder* (amn). Se det här avsnittet om [äldre komponenter](legacy-components.md) för datum för indragningen.

Mer information finns i [MIGRERA WAME till Media Encoder Standard](./migrate-windows-azure-media-encoder.md) och [migrera amn till Media Encoder Standard](./migrate-azure-media-encoder.md).

## <a name="march-2019"></a>Mars 2019

Förhands gransknings funktionen för mediet för Azure Media Services var inaktuell.

## <a name="december-2018"></a>December 2018

Funktionen för förhands granskning av mediet i Azure Media Services kommer snart att dras tillbaka. Med början den 19 december 2018 kommer Media Services inte längre att göra ändringar eller förbättringar i mediet. Den 29 mars 2019 kommer den att dras tillbaka och är inte längre tillgänglig.

## <a name="october-2018"></a>Oktober 2018

### <a name="cmaf-support"></a>CMAF-stöd

Krypterings stöd för CMAF och CBCS för Apple HLS (iOS 11 +) och MPEG-STRECKs pelare som stöder CMAF.

### <a name="web-vtt-thumbnail-sprites"></a>Webb VTT-miniatyrer

Du kan nu använda Media Services för att generera miniatyr bilder av webb VTT med våra v2-API: er. Mer information finns i [Generera en miniatyr sprit](generate-thumbnail-sprite.md).

## <a name="july-2018"></a>Juli 2018

Med den senaste versionen av tjänsten finns det små formateringsändringar i de fel meddelanden som returneras av tjänsten när ett jobb Miss lyckas, i förhållande till hur det är uppdelat i två eller fler rader.

## <a name="may-2018"></a>Maj 2018 

Med början den 12 maj 2018 kommer Live Channels inte längre att ha stöd för RTP/MPEG-2 Transport Stream-inmatnings protokoll. Migrera från RTP/MPEG-2 till RTMP eller fragmented MP4 (Smooth Streaming) insugnings protokoll.

## <a name="october-2017-release"></a>Version oktober 2017
> [!IMPORTANT] 
> Media Services är inaktuellt stöd för Azure Access Control Service-autentiseringsinställningar. Den 22 juni 2018 kan du inte längre autentisera med Media Services server delen via kod med hjälp av Access Control Service nycklar. Du måste uppdatera koden för att använda Azure Active Directory (Azure AD) per [Azure AD-baserad autentisering](media-services-use-aad-auth-to-access-ams-api.md). Titta efter varningar om den här ändringen i Azure Portal.

### <a name="updates-for-october-2017"></a>Uppdateringar för oktober 2017
#### <a name="sdks"></a>SDK:er
* .NET SDK har uppdaterats för att ge stöd för Azure AD-autentisering. Stöd för Access Control Service autentisering har tagits bort från de senaste .NET SDK: n på Nuget.org för att främja snabbare migrering till Azure AD. 
* JAVA SDK har uppdaterats för att ge stöd för Azure AD-autentisering. Stöd för Azure AD-autentisering har lagts till i Java SDK. Information om hur du använder Java SDK med Media Services finns i [Kom igång med Java-klient-SDK för Azure Media Services](media-services-java-how-to-use.md)

#### <a name="file-based-encoding"></a>Filbaserad kodning
* Nu kan du använda Premium-kodaren för att koda ditt innehåll till H. 265 video-codec för video kodning (HEVC) med hög effektivitet. Det finns ingen inverkan på prissättningen om du väljer H. 265 över andra codecenheter, till exempel H. 264. Information om HEVC patent licenser finns i [villkor för online tjänster](https://azure.microsoft.com/support/legal/).
* För käll video som är kodad med video-codecen H. 265 (HEVC), till exempel video som fångats med iOS11 eller GoPro hjälte 6, kan du nu använda antingen Premium-kodaren eller Standard-kodaren för att koda dessa videor. Information om patent licenser finns i [villkor för online tjänster](https://azure.microsoft.com/support/legal/).
* För innehåll som innehåller flera språk ljud spår måste språkvärdena vara korrekt märkta enligt motsvarande fil format specifikation (till exempel ISO MP4). Sedan kan du använda standard-kodaren för att koda innehållet för strömning. Den resulterande streaming-lokaliseraren visar de tillgängliga ljud språken.
* Standard-kodaren stöder nu två nya ljud-och ljud för inställningar, "AAC Audio" och "AAC-ljud med bra kvalitet". Både produktion av AAC-utdata (stereo Advanced Audio kod), med bit hastigheter på 128 kbps och 192 kbit/s.
* Premium-kodaren stöder nu QuickTime/MOV-filformat som indata. Video-codecen måste vara en av de [Apple ProRes-typer som anges i den här GitHub-artikeln](./media-services-media-encoder-standard-formats.md). Ljudet måste vara antingen AAC eller Pulse Code modulering (PCM). Premium-kodaren stöder inte t. ex. enheter/DVCPro video i QuickTime/MOV-filer som indatafiler. Standard-kodaren stöder dessa video-codecenheter.
* Följande fel korrigeringar har gjorts i kodare:

    * Nu kan du skicka jobb med hjälp av en inmatad till gång. När dessa jobb har slutförts kan du ändra till gången (till exempel lägga till, ta bort eller byta namn på filer inom till gången) och skicka ytterligare jobb.
    * Kvaliteten på JPEG-miniatyrer som skapas av Standard-kodaren har förbättrats.
    * Standard-kodaren hanterar indata-metadata och miniatyr av miniatyrer bättre vid mycket korta varaktighets videor.
    * Förbättringar av H. 264-avkodaren som används i Standard-kodaren eliminerar vissa ovanliga artefakter. 

#### <a name="media-analytics"></a>Medieanalys
Allmän tillgänglighet för Azure Media Redactor: den här medie processorn utför anonymisering genom att oskärpa av de valda individerna och är perfekt för användning i offentliga säkerhets-och nyhets medie scenarier. 

En översikt över den nya processorn finns i [det här blogg inlägget](https://azure.microsoft.com/blog/azure-media-redactor/). Information om dokumentation och inställningar finns i [bortredigering ansikten med Azure-medieanalys](media-services-face-redaction.md).



## <a name="june-2017-release"></a>Version från juni 2017

Media Services stöder nu [Azure AD-baserad autentisering](media-services-use-aad-auth-to-access-ams-api.md).

> [!IMPORTANT]
> Media Services stöder för närvarande Access Control Service Authentication-modellen. Access Control Service auktoriseringen kommer att föråldras den 1 juni 2018. Vi rekommenderar att du migrerar till Azure AD-autentiseringsmodellen så snart som möjligt.

## <a name="march-2017-release"></a>Version mars 2017

Nu kan du använda standard-kodaren för att [automatiskt generera en bit hastighets stegning](media-services-autogen-bitrate-ladder-with-mes.md) genom att ange en för inställnings strängen "anpassningsbar strömning" när du skapar en encoding-aktivitet. Om du vill koda en video för strömning med Media Services använder du för inställningen "anpassningsbar strömning". Om du vill anpassa en kodnings för inställning för ditt scenario kan du börja med [de här för inställningarna](media-services-mes-presets-overview.md).

Du kan nu använda Media Encoder Standard eller Media Encoder Premium Workflow för att [skapa en kodnings uppgift som genererar fMP4-segment](media-services-generate-fmp4-chunks.md). 

## <a name="february-2017-release"></a>Version februari 2017

Från och med den 1 april 2017 raderas alla jobb poster i ditt konto som är äldre än 90 dagar automatiskt, tillsammans med tillhör ande uppgifts poster. Borttagning sker även om det totala antalet poster unders tiger Max kvoten. Om du vill arkivera informationen om jobb/aktivitet kan du använda koden som beskrivs i [Hantera till gångar och relaterade entiteter med Media Services .NET SDK](media-services-dotnet-manage-entities.md).

## <a name="january-2017-release"></a>Version från januari 2017

I Media Services representerar en strömmande slut punkt en strömmande tjänst som kan leverera innehåll direkt till ett klient Player-program eller till ett nätverk för innehålls leverans (CDN) för vidare distribution. Media Services ger också sömlös integrering av Azure Content Delivery Network. Den utgående strömmen från en StreamingEndpoint-tjänst kan vara en Live Stream, en video på begäran eller en progressiv nedladdning av din till gång på ditt Media Servicess konto. Varje Media Services konto innehåller en standard slut punkt för direkt uppspelning. Ytterligare slut punkter för direkt uppspelning kan skapas under kontot. 

Det finns två versioner av slut punkter för direkt uppspelning, 1,0 och 2,0. Från och med 10 januari 2017 innehåller alla nyskapade Media Services-konton version 2,0 som standard slut punkt för direkt uppspelning. Ytterligare slut punkter för direkt uppspelning som du lägger till i det här kontot är också version 2,0. Den här ändringen påverkar inte befintliga konton. Befintliga slut punkter för direkt uppspelning är version 1,0 och kan uppgraderas till version 2,0. Det finns funktions-, fakturerings-och funktions ändringar med den här ändringen. Mer information finns i [Översikt över strömningsslutpunkter](media-services-streaming-endpoints-overview.md).

Från och med 2,15-versionen har Media Services lagt till följande egenskaper i entiteten för streaming-slutpunkten:

* CdnProvider 
* CdnProfile
* FreeTrialEndTime 
* StreamingEndpointVersion 

Mer information om dessa egenskaper finns i [StreamingEndpoint](/rest/api/media/operations/streamingendpoint). 

## <a name="december-2016-release"></a>Version december 2016

 Nu kan du använda Media Services för att få åtkomst till telemetri/Mät data för sina tjänster. Du kan använda den aktuella versionen av Media Services för att samla in telemetridata för Live Channel, direkt uppspelnings slut punkt och Arkiv-entiteter. Mer information finns i [Media Services telemetri](media-services-telemetry-overview.md).

## <a name="july-2016-release"></a><a name="july_changes16"></a>Version juli 2016
### <a name="updates-to-the-manifest-file-ism-generated-by-encoding-tasks"></a>Uppdateringar av manifest filen (*. ISM) som skapats av kodnings aktiviteter
När en kodnings uppgift skickas till Media Encoder Standard eller Media Encoder Premium genererar encoding-aktiviteten en [strömmande manifest fil](media-services-deliver-content-overview.md) (*. ISM) i utmatnings till gången. Med den senaste tjänst versionen har syntaxen för den här strömmande manifest filen uppdaterats.

> [!NOTE]
> Syntaxen för den strömmande manifest filen (. ISM) är reserverad för intern användning. Det kan komma att ändras i framtida versioner. Ändra eller ändra inte innehållet i den här filen.
> 
> 

### <a name="a-new-client-manifest-ismc-file-is-generated-in-the-output-asset-when-an-encoding-task-outputs-one-or-more-mp4-files"></a>Ett nytt klient manifest (*. ISMC) skapas i utmatnings till gången när en encoding-uppgift matar ut en eller flera MP4-filer
Från och med den senaste Service Release-versionen, efter att en kodnings uppgift som genererar en eller flera MP4-filer har slutförts, innehåller utdata till gången också en fil för strömnings klient manifest (*. ismc). Ismc-filen hjälper till att förbättra prestandan för dynamisk strömning. 

> [!NOTE]
> Syntaxen för klient manifest filen (. ismc) är reserverad för intern användning. Det kan komma att ändras i framtida versioner. Ändra eller ändra inte innehållet i den här filen.
> 
> 

Mer information finns i [det här blogginlägget](/archive/blogs/randomnumber/encoder-changes-within-azure-media-services-now-create-ismc-file).

### <a name="known-issues"></a>Kända problem
Vissa klienter kan komma över ett problem med upprepad tagg i Smooth Streaming manifestet. Mer information finns i [det här avsnittet](media-services-deliver-content-overview.md#known-issues).

## <a name="april-2016-release"></a><a id="apr_changes16"></a>Version från april 2016
### <a name="media-analytics"></a>Medieanalys
 Media Services introducerade Medieanalys för kraftfull video information. Mer information finns i [Översikt över Media Services Analytics](./legacy-components.md).

### <a name="apple-fairplay-preview"></a>Apple-FairPlay (för hands version)
Nu kan du använda Media Services för att dynamiskt Kryptera ditt HTTP Live Streaming (HLS)-innehåll med Apple FairPlay. Du kan också använda leverans tjänsten Media Services licens för att leverera FairPlay-licenser till klienter. Mer information finns i "använda Azure Media Services för att strömma ditt HLS-innehåll som skyddas med Apple FairPlay".

## <a name="february-2016-release"></a><a id="feb_changes16"></a>Version februari 2016
Den senaste versionen av Media Services SDK för .NET (3.5.3) innehåller en Google Widevine-relaterad fel korrigering. Det var omöjligt att återanvända AssetDeliveryPolicy för flera till gångar som är krypterade med Widevine. Som en del av den här fel korrigeringen lades följande egenskap till i SDK: WidevineBaseLicenseAcquisitionUrl.

```csharp
Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
    new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
{
    {AssetDeliveryPolicyConfigurationKey.WidevineBaseLicenseAcquisitionUrl,"http://testurl"},

};
```

## <a name="january-2016-release"></a><a id="jan_changes_16"></a>Version från januari 2016
Encoding-reserverade enheter har bytt namn för att minska förvirringen med kodarens namn.

De reserverade enheterna Basic, standard och Premium har bytt namn till S1-, S2-och S3-reserverade enheter. Kunder som använder Basic encoding-reserverade enheter idag se S1 som etikett i Azure Portal (och i fakturan). Kunder som använder standard och Premium ser etiketterna S2 respektive S3. 

## <a name="december-2015-release"></a><a id="dec_changes_15"></a>Version december 2015

### <a name="media-encoder-deprecation-announcement"></a>Meddelande om Media Encoder-utfasning

 Media Encoder kommer att bli föråldrad från och med cirka 12 månader från lanseringen av Media Encoder Standard.

### <a name="azure-sdk-for-php"></a>Azure SDK för PHP
Azure SDK-teamet publicerade en ny version av [Azure SDK för php](https://github.com/Azure/azure-sdk-for-php) -paketet som innehåller uppdateringar och nya funktioner för Media Services. I synnerhet stöder Media Services SDK för PHP de senaste funktionerna för [innehålls skydd](media-services-content-protection-overview.md) . Dessa funktioner är dynamiska kryptering med AES och DRM (PlayReady och Widevine) med och utan token-begränsningar. Det stöder också skalnings [kodnings enheter](media-services-dotnet-encoding-units.md).

Mer information finns i:

* Följande [kod exempel](https://github.com/Azure/azure-sdk-for-php/tree/master/examples/MediaServices) hjälper dig att komma igång snabbt:
  * **vodworkflow_aes. php**: den här php-filen visar hur du använder aes-128 dynamisk kryptering och Key Delivery Service. Det baseras på .NET-exemplet som beskrivs i [använda AES-128 dynamisk kryptering och Key Delivery Service](media-services-protect-with-aes128.md).
  * **vodworkflow_aes. php**: den här php-filen visar hur du använder PlayReady Dynamic Encryption och licens leverans tjänsten. Det baseras på .NET-exemplet som beskrivs i [använda PlayReady och/eller Widevine Dynamic common Encryption](media-services-protect-with-playready-widevine.md).
  * **scale_encoding_units. php**: den här php-filen visar hur du skalar kodade reserverade enheter.

## <a name="november-2015-release"></a><a id="nov_changes_15"></a>Version november 2015
 Media Services erbjuder nu licens leverans tjänsten för Widevine i molnet. Mer information finns i [det här blogginlägget](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/). Se även [den här självstudien](media-services-protect-with-playready-widevine.md) och [GitHub-lagringsplatsen](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-drm). 

Widevine licens leverans tjänster som tillhandahålls av Media Services är i för hands version. Mer information finns i [det här blogginlägget](https://azure.microsoft.com/blog/announcing-google-widevine-license-delivery-services-public-preview-in-azure-media-services/).

## <a name="october-2015-release"></a><a id="oct_changes_15"></a>Utgåva från oktober 2015
Media Services är nu Live i följande data Center: södra Brasilien, västra Indien, södra Indien och centrala Indien. Du kan nu använda Azure Portal för att [skapa media service-konton](media-services-portal-create-account.md) och utföra olika uppgifter som beskrivs på [webb sidan för Media Services dokumentation](https://azure.microsoft.com/documentation/services/media-services/). Live Encoding är inte aktive rad i dessa data Center. Dessutom är inte alla typer av kodnings bara reserverade enheter tillgängliga i dessa data Center.

* Södra Brasilien: endast standard-och Basic encoding-reserverade enheter är tillgängliga.
* Västra Indien, södra Indien, södra Indien och centrala Indien: det finns bara reserverade enheter för Basic encoding.

## <a name="september-2015-release"></a><a id="september_changes_15"></a>Version september 2015
Media Services erbjuder nu möjlighet att skydda både video på begäran och direktsända strömmar med Widevine modulära DRM-teknik. Du kan använda följande leverans tjänst partner för att få hjälp att leverera Widevine-licenser:
* [Axinom](https://www.axinom.com) 
* [EZDRM](https://ezdrm.com/) 
* [castLabs](https://castlabs.com/company/partners/azure/) 

Mer information finns i [det här blogginlägget](https://azure.microsoft.com/blog/azure-media-services-adds-google-widevine-packaging-for-delivering-multi-drm-stream/).
  
Du kan använda [Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) (från och med version 3.5.1) eller REST API för att konfigurera din AssetDeliveryConfiguration för att använda Widevine. 
* Media Services lagt till stöd för Apple ProRes-videor. Nu kan du ladda upp videor för QuickTime-källfiler som använder Apple-ProRes eller andra codec-filer. Mer information finns i [det här blogginlägget](https://azure.microsoft.com/blog/announcing-support-for-apple-prores-videos-in-azure-media-services/).
* Du kan nu använda Media Encoder Standard för att göra under Urklipp och extrahering av direktsända arkiv. Mer information finns i [det här blogginlägget](https://azure.microsoft.com/blog/sub-clipping-and-live-archive-extraction-with-media-encoder-standard/).
* Följande filtrerings uppdateringar har gjorts: 
  
  * Du kan nu använda Apple HLS-formatet med ett ljud filter. Du kan använda den här uppdateringen för att ta bort ett ljud spår genom att ange (endast ljud = falskt) i URL: en.
  * När du definierar filter för dina till gångar kan du nu kombinera flera (upp till tre) filter i en enda URL.
    
    Mer information finns i [det här blogginlägget](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).
* Media Services stöder nu i-ramar i HLS version 4. I-ram-stödet optimerar åtgärder för snabb spolning framåt och bakåt. Som standard innehåller alla HLS version 4-utdata i spelnings List rutan (EXT-X-I-FRAME-STREAM-INF).
Mer information finns i [det här blogginlägget](https://azure.microsoft.com/blog/azure-media-services-release-dynamic-manifest-composition-remove-hls-audio-only-track-and-hls-i-frame-track-support/).

## <a name="august-2015-release"></a><a id="august_changes_15"></a>Version augusti 2015
* Media Services SDK för Java-version 0.8.0-versionen och nya exempel är nu tillgängliga. Mer information finns i:
    
* Azure Media Player har uppdaterats med stöd för flera ljud strömmar. Mer information finns i [det här blogginlägget](https://azure.microsoft.com/blog/2015/08/13/azure-media-player-update-with-multi-audio-stream-support/).

## <a name="july-2015-release"></a><a id="july_changes_15"></a>Version juli 2015
* Den allmänna tillgängligheten för Media Encoder Standard annonserades. Mer information finns i [det här blogginlägget](https://azure.microsoft.com/blog/2015/07/16/announcing-the-general-availability-of-media-encoder-standard/).
  
    Media Encoder Standard använder för inställningar enligt beskrivningen i [det här avsnittet](./media-services-mes-presets-overview.md). När du använder en för inställning för 4K-kodningar hämtar du den reserverade enhets typen Premium. Mer information finns i [skala kodning](media-services-scale-media-processing-overview.md).
* Real tids textning i real tid användes med Media Services och Media Player. Mer information finns i [det här blogginlägget](https://azure.microsoft.com/blog/2015/07/08/live-real-time-captions-with-azure-media-services-and-player/).

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK-uppdateringar
Media Services .NET SDK är nu version 3.4.0.0. Följande uppdateringar har gjorts: 

* Stöd har implementerats för Live Archive. Du kan inte ladda ned en till gång som innehåller ett Live-Arkiv.
* Stöd har implementerats för dynamiska filter.
* Funktionen implementerades så att användarna kan behålla en lagrings behållare medan de tar bort en till gång.
* Fel korrigeringar har gjorts relaterade till principer för nya försök i kanaler.
* Media Encoder Premium Workflow aktiverades.

## <a name="june-2015-release"></a><a id="june_changes_15"></a>Version från juni 2015
### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK-uppdateringar
Media Services .NET SDK är nu version 3.3.0.0. Följande uppdateringar har gjorts: 

* Stöd har lagts till för OpenId Connect Discovery-specifikationen.
* Stöd har lagts till för förnyelse av hanterings nycklar på sidan med identitets leverantören.

Om du använder en identitets leverantör som exponerar ett OpenID Connect Discovery-dokument (som Azure AD, Google och Salesforce), kan du instruera Media Services för att hämta signerings nycklar för validering av JSON Web tokens (JWTs) från OpenID Connect Discovery-spec. 

Mer information finns i [använda JSON-webbnycklar från OpenID Connect Discovery-specifikationen för att arbeta med JWT-autentisering i Media Services](http://gtrifonov.com/2015/06/07/using-json-web-keys-from-openid-connect-discovery-spec-to-work-with-jwt-token-authentication-in-azure-media-services/).

## <a name="may-2015-release"></a><a id="may_changes_15"></a>Version maj 2015
Följande nya funktioner har annonser ATS:

* [En för hands version av Live encoding med Media Services](media-services-manage-live-encoder-enabled-channels.md)
* [Dynamiskt manifest](media-services-dynamic-manifest-overview.md)

## <a name="april-2015-release"></a><a id="april_changes_15"></a>Version från april 2015
### <a name="general-media-services-updates"></a>Allmänna Media Services uppdateringar
* [Media Player](https://azure.microsoft.com/blog/2015/04/15/announcing-azure-media-player/) annonserades.
* Från och med Media Services REST 2,10 skapas kanaler som är konfigurerade för att mata in ett Real-Time meddelande protokoll (RTMP) med primära och sekundära inmatnings-URL: er. Mer information finns i kanal inmatning- [konfigurationer](media-services-live-streaming-with-onprem-encoders.md#channel_input).
* Azure Media Indexer uppdaterades.
* Stöd för spanska språk har lagts till.
* En ny konfiguration för XML-formatet har lagts till.

Mer information finns i [det här blogginlägget](https://azure.microsoft.com/blog/2015/04/13/azure-media-indexer-spanish-v1-2/).

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK-uppdateringar
Media Services .NET SDK är nu version 3.2.0.0. Följande uppdateringar har gjorts:

* Överordnad ändring: TokenRestrictionTemplate. Issuer och TokenRestrictionTemplate. Audience har ändrats till en sträng typ.
* Uppdateringar har gjorts relaterade till att skapa anpassade principer för återförsök.
* Fel korrigeringar har gjorts relaterade till överföring och hämtning av filer.
* MediaServicesCredentials-klassen accepterar nu primära och sekundära slut punkter för åtkomst kontroll för autentisering mot.

## <a name="march-2015-release"></a><a id="march_changes_15"></a>Version mars 2015
### <a name="general-media-services-updates"></a>Allmänna Media Services uppdateringar
* Media Services har nu Content Delivery Network-integration. För att stödja integreringen har egenskapen CdnEnabled lagts till i StreamingEndpoint. CdnEnabled kan användas med REST API: er som börjar med version 2,9. Mer information finns i [StreamingEndpoint](/rest/api/media/operations/streamingendpoint). CdnEnabled kan användas med .NET SDK från och med version 3.1.0.2. Mer information finns i [StreamingEndpoint](/archive/blogs/randomnumber/encoder-changes-within-azure-media-services-now-create-ismc-file).
* Media Encoder Premium Workflow annonserades. Mer information finns [i Introduktion till Premium encoding i Azure Media Services](https://azure.microsoft.com/blog/2015/03/05/introducing-premium-encoding-in-azure-media-services/).

## <a name="february-2015-release"></a><a id="february_changes_15"></a>Version februari 2015
### <a name="general-media-services-updates"></a>Allmänna Media Services uppdateringar
Media Services REST API är nu version 2,9. Från och med den här versionen kan du aktivera Content Delivery Network-integrering med slut punkter för direkt uppspelning. Mer information finns i [StreamingEndpoint](/rest/api/media/operations/streamingendpoint).

## <a name="january-2015-release"></a><a id="january_changes_15"></a>Version från januari 2015
### <a name="general-media-services-updates"></a>Allmänna Media Services uppdateringar
Den allmänna tillgängligheten för innehålls skydd med dynamisk kryptering annonserades. Mer information finns i [Media Services förbättrar strömmande säkerhet med allmän tillgänglighet för DRM-teknik](https://azure.microsoft.com/blog/2015/01/29/azure-media-services-enhances-streaming-security-with-general-availability-of-drm-technology/).

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK-uppdateringar
Media Services .NET SDK är nu version 3.1.0.1.

Den här versionen markerade standardvärdet för Microsoft. WindowsAzure. Media Services. client. ContentKeyAuthorization. TokenRestrictionTemplate som föråldrad. Den nya konstruktorn tar TokenType som ett argument.

```csharp
TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
```


## <a name="december-2014-release"></a><a id="december_changes_14"></a>Version december 2014
### <a name="general-media-services-updates"></a>Allmänna Media Services uppdateringar
* Vissa uppdateringar och nya funktioner har lagts till i Media Indexer. Mer information finns i [Azure Media Indexer version 1.1.6.7 version Notes](https://azure.microsoft.com/blog/2014/12/03/azure-media-indexer-version-1-1-6-7-release-notes/).
* Ett nytt REST API har lagts till som du kan använda för att uppdatera kodade reserverade enheter. Mer information finns i [EncodingReservedUnitType with rest](/rest/api/media/operations/encodingreservedunittype).
* CORS-stöd har lagts till för Key Delivery Service.
* Prestanda förbättringar har gjorts för att fråga efter alternativ för Auktoriseringsprinciper.
* I data centret i Kina är [URL: en för nyckel leverans](/rest/api/media/operations/contentkey#get_delivery_service_url) nu per kund (precis som i andra data Center).
* HLS för automatisk mål har lagts till. När du utför Direktsänd strömning paketeras HLS alltid dynamiskt. Som standard beräknar Media Services automatiskt HLS segmentets förpacknings kvot (FragmentsPerSegment) baserat på nyckel bilds intervallet (KeyFrameInterval). Den här metoden kallas även för en grupp bilder (GOP) som tas emot från Live-kodaren. Mer information finns i [arbeta med Media Services Live streaming](/previous-versions/azure/dn783466(v=azure.100)).

### <a name="media-services-net-sdk-updates"></a>Media Services .NET SDK-uppdateringar
[Media Services .NET SDK](https://www.nuget.org/packages/windowsazure.mediaservices/) är nu version 3.1.0.0. Följande uppdateringar har gjorts:

* .NET SDK-beroendet har uppgraderats till .NET 4,5-ramverket.
* Ett nytt API som du kan använda för att uppdatera kodade reserverade enheter har lagts till. Mer information finns i [Uppdatera reserverad enhets typ och öka encoding-reserverade enheter med hjälp av .net](media-services-dotnet-encoding-units.md).
* JWT-stöd för token-autentisering har lagts till. Mer information finns i [JWT token-autentisering i Media Services och dynamisk kryptering](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).
* Relativa förskjutningar för BeginDate och ExpirationDate i licens mal len för PlayReady lades till.

## <a name="november-2014-release"></a><a id="november_changes_14"></a>Version november 2014
* Nu kan du använda Media Services för att mata in direktsänd Smooth Streaming (fMP4)-innehåll via en TLS-anslutning. Om du vill mata in via TLS måste du uppdatera inmatnings-URL: en till HTTPS. Media Services stöder för närvarande inte TLS med anpassade domäner. Mer information om direkt uppspelning finns i [arbeta med Azure Media Services Live streaming](/previous-versions/azure/dn783466(v=azure.100)).
* För närvarande kan du inte mata in en RTMP Live Stream via en TLS-anslutning.
* Du kan strömma över TLS endast om den strömnings slut punkt från vilken du levererar ditt innehåll skapades efter den 10 september 2014. Om dina strömnings-URL: er baseras på de strömnings slut punkter som skapats efter den 10 september 2014 innehåller URL: en "streaming.mediaservices.windows.net" (det nya formatet). Strömnings-URL: er som innehåller "origin.mediaservices.windows.net" (det gamla formatet) stöder inte TLS. Om din URL är i det gamla formatet och du vill strömma över TLS, [skapar du en ny slut punkt för direkt uppspelning](media-services-portal-manage-streaming-endpoints.md). Om du vill strömma ditt innehåll över TLS använder du webb adresser baserade på den nya slut punkten för direkt uppspelning.

### <a name="media-services-net-sdk"></a><a id="oct_sdk"></a>Media Services .NET SDK
Media Services SDK för .NET-tillägg är nu version 2.0.0.3.

Media Services SDK för .NET är nu version 3.0.0.8. Följande uppdateringar har gjorts:

* Omfactoring implementerades i princip klasser för återförsök.
* En användar agent sträng lades till i huvuden för HTTP-begäran.
* Ett NuGet återställnings steg har lagts till.
* Scenario test har åtgärd ATS för att använda x509-certifikat från lagrings platsen.
* Verifierings inställningarna lades till för när kanal-och direkt uppspelnings uppdateringen slutfördes.

### <a name="new-github-repository-to-host-media-services-samples"></a>Ny GitHub-lagringsplats till värd Media Services exempel
Exempel finns i [Media Services-exempel GitHub-lagringsplatsen](https://github.com/Azure/Azure-Media-Services-Samples).

## <a name="september-2014-release"></a><a id="september_changes_14"></a>Version september 2014
Media Services REST-metadata är nu version 2,7. Mer information om de senaste REST-uppdateringarna finns i [referens för Media Services REST API](/rest/api/media/operations/azure-media-services-rest-api-reference).

Media Services SDK för .NET är nu version 3.0.0.7

### <a name="breaking-changes"></a><a id="sept_14_breaking_changes"></a>Icke-bakåtkompatibla ändringar
* Ursprungets namn ändrades till [StreamingEndpoint].
* En ändring har gjorts i standard beteendet när du använder Azure Portal för att koda och sedan publicera MP4-filer.

### <a name="new-featuresscenarios-that-are-part-of-the-general-availability-release"></a><a id="sept_14_GA_changes"></a>Nya funktioner/scenarier som ingår i den allmänna tillgänglighets versionen
* Media Indexer medie processorn introducerades. Mer information finns i [index Media Files med Media Indexer](/previous-versions/azure/dn783455(v=azure.100)).
* Du kan använda entiteten [StreamingEndpoint] för att lägga till anpassade domän namn (värdnamn).
  
    Om du vill använda ett anpassat domän namn som Media Services slut punkts namn för direkt uppspelning lägger du till anpassade värdnamn i slut punkten för direkt uppspelning. Använd Media Services REST-API: er eller .NET SDK för att lägga till anpassade värdnamn.
  
    Följande gäller:
  
  * Du måste ha ägarskapet till det anpassade domän namnet.
  * Ägarskapet för domän namnet måste verifieras av Media Services. Verifiera domänen genom att skapa en CName som mappar MediaServicesAccountId överordnade domän för att verifiera DNS-Media Services-DNS-Zone.
  * Du måste skapa en annan CName som mappar det anpassade värd namnet (till exempel sports.contoso.com) till ditt Media Services StreamingEndpoint-värdnamn (till exempel amstest.streaming.mediaservices.windows.net).

    Mer information finns i egenskapen CustomHostNames i artikeln [StreamingEndpoint](/rest/api/media/operations/streamingendpoint) .

### <a name="new-featuresscenarios-that-are-part-of-the-public-preview-release"></a><a id="sept_14_preview_changes"></a>Nya funktioner/scenarier som ingår i den offentliga för hands versionen
* För hands version av Live-direktuppspelning. Mer information finns i [arbeta med Media Services Live streaming](/previous-versions/azure/dn783466(v=azure.100)).
* Nyckel leverans tjänst. Mer information finns i [använda AES-128 dynamisk kryptering och Key Delivery Service](/previous-versions/azure/dn783457(v=azure.100)).
* Dynamisk AES-kryptering. Mer information finns i [använda AES-128 dynamisk kryptering och Key Delivery Service](/previous-versions/azure/dn783457(v=azure.100)).
* PlayReady licens leverans tjänst. 
* PlayReady Dynamic Encryption. 
* Media Services PlayReady-licens mal len. Mer information finns i [Översikt över Media Services PlayReady-licens mal len].
* Strömma Storage-krypterade till gångar. Mer information finns i [Stream Storage-krypterat innehåll](/previous-versions/azure/dn783451(v=azure.100)).

## <a name="august-2014-release"></a><a id="august_changes_14"></a>Version augusti 2014
När du kodar en till gång skapas en utmatnings till gång när kodnings jobbet är klart. Till och med den här versionen producerade Media Services Encoder metadata om utdata till gångar. Från och med den här versionen skapar kodaren även metadata om indata till gångar. Mer information finns i [metadata för indata] och [utdata].

## <a name="july-2014-release"></a><a id="july_changes_14"></a>Version juli 2014
Följande fel korrigeringar har gjorts för Azure Media Services Paketeraren och Krypteraren:

* När en Live Archive-till gång överförs till HLS spelas bara ljud tillbaka upp: det här problemet har åtgärd ATS och nu kan både ljud och video spelas upp.
* När en till gång är paketerad för HLS-och AES 128-bitars kryptering spelas de paketerade strömmarna inte tillbaka på Android-enheter: det här felet har åtgärd ATS och den paketerade strömmen spelas upp på Android-enheter som stöder HLS.

## <a name="may-2014-release"></a><a id="may_changes_14"></a>Version maj 2014
### <a name="general-media-services-updates"></a><a id="may_14_changes"></a>Allmänna Media Services uppdateringar
Nu kan du använda [dynamisk paketering] för att strömma HLS version 3. Om du vill strömma HLS version 3 lägger du till följande format i sökvägen till ursprungs lokaliseraren: *. ISM/manifest (format = M3U8-AAPL-v3). Mer information finns i [det här forumet](https://social.msdn.microsoft.com/Forums/en-US/13b8a776-9519-4145-b9ed-d2b632861fde/dynamic-packaging-to-hls-v3).

Dynamisk paketering stöder nu även att leverera HLS (version 3 och version 4) som är krypterad med PlayReady baserat på Smooth Streaming statiskt krypterat med PlayReady. Information om hur du krypterar Smooth Streaming med PlayReady finns i [skydda Smooth Streaming med PlayReady](/previous-versions/azure/dn189154(v=azure.100)).

### <a name="media-services-net-sdk-updates"></a><a name="may_14_donnet_changes"></a>Media Services .NET SDK-uppdateringar
Media Services .NET SDK är nu version 3.0.0.5. Följande uppdateringar har gjorts:

* Hastighet och återhämtning är bättre när du laddar upp och laddar ned medie till gångar.
* Förbättringar har gjorts i logik för omförsök och hantering av tillfälliga undantag: 
  
  * Den tillfälliga fel identifieringen och omprövnings logiken har förbättrats för undantag som orsakas när du frågar, sparar ändringar och laddar upp eller laddar ned filer. 
  * När du hämtar webb undantag (till exempel under en Access Control Service-Tokenbegäran) går det inte att återställa allvarliga fel nu.

Mer information finns i avsnittet [om att försöka köra logiken i Media Services SDK för .net].

## <a name="januaryfebruary-2014-releases"></a><a id="jan_feb_changes_14"></a>Versioner från januari/februari 2014
### <a name="media-services-net-sdk-3001-3002-and-3003"></a><a name="jan_fab_14_donnet_changes"></a>Media Services .NET SDK 3.0.0.1, 3.0.0.2 och 3.0.0.3
Ändringarna i 3.0.0.1 och 3.0.0.2 är:

* Problem som rör användningen av LINQ-frågor med OrderBy-instruktioner har åtgärd ATS.
* Test lösningar i [GitHub] har delats in i enhets tester och scenariobaserade tester.

Mer information om ändringarna finns i [Media Services .NET SDK-3.0.0.1 och 3.0.0.2-versioner](http://gtrifonov.com/2014/02/07/windows-azure-media-services-net-sdk-3-0-0-2-release/index.html).

Följande ändringar har gjorts i version 3.0.0.3:

* Azure Storage-beroenden har uppgraderats för att använda version 3.0.3.0.
* Ett bakåtkompatibla kompatibilitetsproblem har åtgärd ATS för 3,0. *.* exekutiv.

## <a name="december-2013-release"></a><a id="december_changes_13"></a>Version december 2013
### <a name="media-services-net-sdk-3000"></a><a name="dec_13_donnet_changes"></a>Media Services .NET SDK-3.0.0.0
> [!NOTE]
> Versionerna 3.0. x. x är inte bakåtkompatibla med 2,4. x. x-versioner.
> 
> 

Den senaste versionen av Media Services SDK är nu 3.0.0.0. Du kan hämta det senaste paketet från NuGet eller hämta bitarna från [GitHub].

Från och med Media Services SDK-versionen 3.0.0.0 kan du återanvända [Azure AD Access Control Service](/previous-versions/azure/azure-services/hh147631(v=azure.100)) -token. Mer information finns i avsnittet "Återanvänd Access Control Service tokens" i [Anslut till Media Services med Media Services SDK för .net](/previous-versions/azure/jj129571(v=azure.100)).

### <a name="media-services-net-sdk-extensions-2000"></a><a name="dec_13_donnet_ext_changes"></a>Media Services .NET SDK-tillägg 2.0.0.0
 Media Services .NET SDK-tillägg är en uppsättning tilläggs metoder och hjälp funktioner som fören klar koden och gör det lättare att utveckla med Media Services. Du kan hämta de senaste bitarna från [Media Services .NET SDK-tillägg](https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev).

## <a name="november-2013-release"></a><a id="november_changes_13"></a>Version november 2013
### <a name="media-services-net-sdk-changes"></a><a name="nov_13_donnet_changes"></a>Media Services ändringar i .NET SDK
Från och med den här versionen hanterar Media Services SDK för .NET tillfälliga fel som kan uppstå när anrop görs till Media Services REST API skiktet.

## <a name="august-2013-release"></a><a id="august_changes_13"></a>Version augusti 2013
### <a name="media-services-powershell-cmdlets-included-in-azure-sdk-tools"></a><a name="aug_13_powershell_changes"></a>Media Services PowerShell-cmdletar som ingår i Azure SDK-verktyg
Följande Media Services PowerShell-cmdletar ingår nu i [Azure SDK-verktyg](https://github.com/Azure/azure-sdk-tools):

* Get-AzureMediaServices 

    Exempelvis: `Get-AzureMediaServicesAccount`
* New-AzureMediaServicesAccount 
  
    Exempelvis: `New-AzureMediaServicesAccount -Name "MediaAccountName" -Location "Region" -StorageAccountName "StorageAccountName"`
* New-AzureMediaServicesKey 
  
    Exempelvis: `New-AzureMediaServicesKey -Name "MediaAccountName" -KeyType Secondary -Force`
* Remove-AzureMediaServicesAccount 
  
    Exempelvis: `Remove-AzureMediaServicesAccount -Name "MediaAccountName" -Force`

## <a name="june-2013-release"></a><a id="june_changes_13"></a>Version från juni 2013
### <a name="media-services-changes"></a><a name="june_13_general_changes"></a>Media Services ändringar
Följande ändringar som nämns i det här avsnittet är uppdateringar som ingår i juni 2013 Media Services-versioner:

* Möjlighet att länka flera lagrings konton till ett media service-konto. 
    * StorageAccount
    * Till gång. StorageAccountName och till gång. StorageAccount
* Möjlighet att uppdatera Job. Priority. 
* Aviserings-relaterade entiteter och egenskaper: 
    * JobNotificationSubscription
    * NotificationEndPoint
    * Jobb
* Till gång. URI 
* Locator.Name 

### <a name="media-services-net-sdk-changes"></a><a name="june_13_dotnet_changes"></a>Media Services ändringar i .NET SDK
Följande ändringar ingår i juni 2013 Media Services SDK-versionerna. Den senaste Media Services SDK finns på GitHub.

* Från och med version 2.3.0.0 stöder Media Services SDK länkar flera lagrings konton till ett Media Services-konto. Följande API: er har stöd för den här funktionen:
  
    * Typ av IStorageAccount
    * Microsoft. WindowsAzure. Media Services. client. CloudMediaContext. StorageAccounts-egenskap
    * StorageAccount-egenskap
    * StorageAccountName-egenskap
  
      Mer information finns i [hantera Media Services till gångar över flera lagrings konton](/previous-versions/azure/dn271889(v=azure.100)).
* API: er för aviseringar. Från och med version 2.2.0.0 kan du lyssna på lagrings meddelanden i Azure Queue. Mer information finns i [hantera Media Services jobb meddelanden](/previous-versions/azure/dn261241(v=azure.100)).
  
    * Microsoft. WindowsAzure. Media Services. client. IJob. JobNotificationSubscriptions-egenskap
    * Microsoft. WindowsAzure. Media Services. client. INotificationEndPoint-typ
    * Microsoft. WindowsAzure. Media Services. client. IJobNotificationSubscription-typ
    * Microsoft. WindowsAzure. Media Services. client. NotificationEndPointCollection-typ
    * Microsoft. WindowsAzure. Media Services. client. NotificationEndPointType-typ
* Beroende av lagrings klientens SDK 2,0 (Microsoft.WindowsAzure.StorageClient.dll)
* Beroende av OData 5,5 (Microsoft.Data.OData.dll)

## <a name="december-2012-release"></a><a id="december_changes_12"></a>Version december 2012
### <a name="media-services-net-sdk-changes"></a><a name="dec_12_dotnet_changes"></a>Media Services ändringar i .NET SDK
* IntelliSense: IntelliSense-dokumentation som saknas har lagts till för många typer.
* Microsoft. Practices. TransientFaultHandling. Core: ett problem har åtgärd ATS där SDK fortfarande hade ett beroende till en gammal version av den här sammansättningen. SDK refererar nu till version 5.1.1209.1 av den här sammansättningen.

Korrigeringar för problem som finns i november 2012 SDK:

* IAsset. locations. Count: det här antalet rapporteras nu korrekt på nya IAsset-gränssnitt när alla positioner har tagits bort.
* IAssetFile. ContentFileSize: det här värdet anges nu korrekt efter en uppladdning av IAssetFile. upload (sökväg).
* IAssetFile. ContentFileSize: den här egenskapen kan nu anges när du skapar en till gångs fil. Den har redan lästs.
* IAssetFile. upload (sökväg): ett problem har åtgärd ATS där denna synkrona överförings metod returnerade följande fel när flera filer överfördes till till gången. Felet var "servern kunde inte autentisera begäran. Se till att värdet för Authorization-huvudet skapas korrekt, inklusive signaturen. "
* IAssetFile. UploadAsync: ett problem har åtgärd ATS som begränsade den samtidiga överföringen av filer till fem filer.
* IAssetFile. UploadProgressChanged: den här händelsen tillhandahålls nu av SDK: n.
* IAssetFile. DownloadAsync (sträng, BlobTransferClient, ILocator, CancellationToken): den här metoden överbelastning har nu angetts.
* IAssetFile. DownloadAsync: ett problem har åtgärd ATS som begränsade den samtidiga nedladdningen av filer till fem filer.
* IAssetFile. Delete (): ett problem har åtgärd ATS där anropet ta bort kan utlösa ett undantag om ingen fil har laddats upp för IAssetFile.
* Jobb: ett problem har åtgärd ATS vid länkning av en "MP4-till jämna strömmar" med en "PlayReady-skydds uppgift" med hjälp av en jobbmall som inte skapade några aktiviteter alls.
* EncryptionUtils. GetCertificateFromStore (): den här metoden returnerar inte längre ett null-referensundantag på grund av ett fel när certifikatet skulle hittas baserat på certifikat konfigurations problem.

## <a name="november-2012-release"></a><a id="november_changes_12"></a>Version november 2012
Ändringarna som nämns i det här avsnittet var uppdateringar som ingår i november 2012-SDK: n (version 2.0.0.0). Dessa ändringar kan kräva all kod som skrivits för Preview SDK-versionen från juni 2012 för att ändras eller skrivas över.

* Tillgångar
  
    * IAsset. Create (assetName) är den *enda* funktionen för att skapa till gångar. IAsset. Create tar inte längre bort filer som en del av metod anropet. Använd IAssetFile för att ladda upp.
    * Metoden IAsset. publish och uppräkning svärdet AssetState. Publish togs bort från SDK: n för tjänster. All kod som förlitar sig på detta värde måste skrivas om.
* FileInfo
  
    * Den här klassen har tagits bort och ersatts av IAssetFile.
  
* IAssetFiles
  
    * IAssetFile ersätter FileInfo och har ett annat beteende. Om du vill använda den instansierar du IAssetFiles-objektet följt av en fil uppladdning antingen med hjälp av Media Services SDK eller SDK för lagring. Du kan använda följande IAssetFile. upload Overloads:
  
        * IAssetFile. upload (sökväg): den här synkrona metoden blockerar tråden och vi rekommenderar den bara när du laddar upp en enskild fil.
        * IAssetFile. UploadAsync (sökväg, blobTransferClient, Locator, cancellationToken): den här asynkrona metoden är den prioriterade överförings mekanismen. 
    
            Känt fel: om du använder token för annullering avbryts överföringen. Aktiviteterna kan ha många avbrotts tillstånd. Du måste fånga och hantera undantag på rätt sätt.
* Positionerare
  
    * De ursprungs versioner som är aktuella har tagits bort. Den SAS-speciella kontexten. Lokaliserare. CreateSasLocator (till gång, Access policy) kommer att markeras som inaktuellt eller tas bort av allmän tillgänglighet. Se avsnittet "Locators" under "nya funktioner" för det uppdaterade beteendet.

## <a name="june-2012-preview-release"></a><a id="june_changes_12"></a>För hands version av juni 2012
Följande funktioner var nya i november-versionen av SDK:

* Tar bort entiteter
  
    * IAsset-, IAssetFile-, ILocator-, IAccessPolicy-och IContentKey-objekt tas nu bort på objekt nivå, d.v.s. IObject. Delete (), i stället för att kräva en borttagning i samlingen, det vill säga cloudMediaContext. ObjCollection. Delete (objInstance).
* Positionerare
  
    * Lokaliserare måste nu skapas med hjälp av metoden CreateLocator. De måste använda LocatorType. SAS eller LocatorType. OnDemandOrigin Enum-värden som argument för den angivna typen av positionerare som du vill skapa.
    * Nya egenskaper har lagts till i positionerare för att göra det enklare att få användbara URI: er för ditt innehåll. Den här omdesignen av lokaliserare ger större flexibilitet för framtida utökningar från tredje part och ökar enkelt användningen för medie klient program.
* Stöd för asynkrona metoder
  
    * Asynkront stöd har lagts till i alla metoder.

## <a name="additional-notes"></a>Ytterligare information

* Widevine är en tjänst som tillhandahålls av Google Inc. och omfattas av villkoren i tjänste-och sekretess policyn för Google, Inc.

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

<!-- Anchors. -->

<!-- Images. -->

<!--- URLs. --->
[Microsoft Q&A question page for Azure Media Services]: /answers/topics/azure-media-services.html
[Azure Media Services REST API referens]: /rest/api/media/operations/azure-media-services-rest-api-reference
[Media Services pricing details]: https://azure.microsoft.com/pricing/details/media-services/
[Inkommande metadata]: ./media-services-input-metadata-schema.md
[Utgående metadata]: ./media-services-output-metadata-schema.md
[Deliver content]: /previous-versions/azure/hh973618(v=azure.100)
[Index media files with the Azure Media Indexer]: /previous-versions/azure/dn783455(v=azure.100)
[StreamingEndpoint]: /rest/api/media/operations/streamingendpoint
[Work with Media Services live streaming]: /previous-versions/azure/dn783466(v=azure.100)
[Use AES-128 dynamic encryption and the key delivery service]: /previous-versions/azure/dn783457(v=azure.100)
[Use PlayReady dynamic encryption and the license delivery service]: /previous-versions/azure/dn783467(v=azure.100)
[Preview features]: https://azure.microsoft.com/services/preview/
[Översikt för Media Services PlayReady-licensmall]: /previous-versions/azure/dn783459(v=azure.100)
[Stream storage-encrypted content]: /previous-versions/azure/dn783451(v=azure.100)
[Azure portal]: https://portal.azure.com
[Dynamisk paketering]: /previous-versions/azure/jj889436(v=azure.100)
[Nick Drouin's blog]: http://blog-ndrouin.azurewebsites.net/hls-v3-new-old-thing/
[Protect Smooth Streaming with PlayReady]: /previous-versions/azure/dn189154(v=azure.100)
[Omprövnings logik i Media Services SDK för .NET]: ./media-services-retry-logic-in-dotnet-sdk.md
[Grass Valley announces EDIUS 7 streaming through the cloud]: https://www.streamingmedia.com/Producer/Articles/ReadArticle.aspx?ArticleID=96351&utm_source=dlvr.it&utm_medium=twitter
[Control Media Services Encoder output file names]: /previous-versions/azure/dn303341(v=azure.100)
[Create overlays]: /previous-versions/azure/dn640496(v=azure.100)
[Stitch video segments]: /previous-versions/azure/dn640504(v=azure.100)
[Azure Media Services .NET SDK 3.0.0.1 and 3.0.0.2 releases]: http://www.gtrifonov.com/2014/02/07/windows-azure-media-services-.net-sdk-3.0.0.2-release/
[Azure AD Access Control Service]: /previous-versions/azure/azure-services/hh147631(v=azure.100)
[Connect to Media Services with the Media Services SDK for .NET]: /previous-versions/azure/jj129571(v=azure.100)
[Media Services .NET SDK extensions]: https://github.com/Azure/azure-sdk-for-media-services-extensions/tree/dev
[Azure SDK tools]: https://github.com/Azure/azure-sdk-tools
[GitHub]: https://github.com/Azure/azure-sdk-for-media-services
[Manage Media Services assets across multiple Storage accounts]: /previous-versions/azure/dn271889(v=azure.100)
[Handle Media Services job notifications]: /previous-versions/azure/dn261241(v=azure.100)