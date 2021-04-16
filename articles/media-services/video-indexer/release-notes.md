---
title: Azure Media Services Video Indexer den här | Microsoft Docs
description: Den här artikeln innehåller de senaste uppdateringarna för att hålla dig uppdaterad med den senaste utvecklingen Azure Media Services Video Indexer.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.subservice: video-indexer
ms.workload: na
ms.topic: article
ms.custom: references_regions
ms.date: 03/30/2021
ms.author: juliako
ms.openlocfilehash: b3602d421718cbd1de3509751491ec6db65b1b01
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532894"
---
# <a name="azure-media-services-video-indexer-release-notes"></a>Azure Media Services Video Indexer den här versionen

>Få ett meddelande om när du ska gå tillbaka till den här sidan för uppdateringar genom att kopiera och klistra in den här `https://docs.microsoft.com/api/search/rss?search=%22Azure+Media+Services+Video+Indexer+release+notes%22&locale=en-us` URL:en: i RSS-flödesläsaren.

Den här artikeln innehåller information om hur du håller dig uppdaterad med den senaste utvecklingen:

* De senaste versionerna
* Kända problem
* Felkorrigeringar
* Föråldrade funktioner

## <a name="march-2021"></a>Mars 2021

### <a name="audio-analysis"></a>Ljudanalys 

Ljudanalys är nu tillgängligt i ytterligare ett nytt paket med ljudfunktioner till en annan prispunkt. Den nya **förinställningen Grundläggande** ljudanalys ger ett alternativ till låg kostnad för att endast extrahera taltranskribering, översättning och formatera undertexter för utdata. Förinställningen **Grundläggande ljud** skapar två separata mätare på fakturan, inklusive en rad för transkription och en separat rad för textning och undertextformatering. Mer information om prissättningen finns på [Media Services prissättningssidan.](https://azure.microsoft.com/pricing/details/media-services/)

Det nyligen tillagda paketet är tillgängligt när du indexerar eller indexerar om filen genom att välja förinställningen Avancerat alternativ Grundläggande ljud (under listrutan  ->   **Video +** ljudindexering).

### <a name="new-developer-portal"></a>Ny utvecklarportal 

Video Indexer har en ny [](https://api-portal.videoindexer.ai/)utvecklarportal kan du prova de nya Video Indexer-API:erna och hitta alla relevanta resurser på ett och samma ställe: GitHub-lagringsplatsen, [Stack overflow](https://stackoverflow.com/questions/tagged/video-indexer), [Video Indexer Tech Community](https://techcommunity.microsoft.com/t5/azure-media-services/bg-p/AzureMediaServices/label-name/Video%20Indexer) med relevanta blogginlägg, vanliga frågor och svar om [Video Indexer,](faq.md) [User](https://feedback.azure.com/forums/932041-cognitive-services?category_id=399016) [Voice](https://github.com/Azure-Samples/media-services-video-indexer)för att ge feedback och föreslå funktioner samt [codePen-länk](https://codepen.io/videoindexer) med kodexempel för widgetar. 
 
### <a name="advanced-customization-capabilities-for-insight-widget"></a>Avancerade anpassningsfunktioner för insiktswidget 

SDK är nu tillgängligt för att bädda Video Indexer insiktswidget i din egen tjänst och anpassa dess stil och data. SDK:n har stöd för Video Indexer insiktswidget och en helt anpassningsbar insiktswidget. Kodexempel finns på [GitHub Video Indexer databasen](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Embedding%20widgets/widget-customization). Med de här avancerade anpassningsfunktionerna kan lösningsutvecklare använda anpassad formatering och ta med kundens egna AI-data och presentera dem i insiktswidgeten (med eller utan Video Indexer insikter). 

### <a name="video-indexer-deployed-in-the-us-north-central--us-west-and-canada-central"></a>Video Indexer distribueras i USA, norra centrala, USA, västra och Kanada, centrala 

Nu kan du skapa ett Video Indexer betalkonto i regionerna USA, norra centrala, USA, västra och Kanada, centrala
 
### <a name="new-source-languages-support-for-speech-to-text-stt-translation-and-search"></a>Stöd för nya källspråk för tal till text (STT), översättning och sökning 

Video Indexer stöder nu STT, översättning och sökning på danska ('da-DK'), norska('nb-NO'), svenska('sv-SE'), finska('fi-FI'), franska ('fr-CA'), thailändska ('th-TH'), arabiska ('ar-NL', "ar-EG", "ar-IQ", "ar-JO", "ar-KW", "ar-LB", "ar-OM", "ar-QA", "ar-S" och "ar-SY" och turkiska('tr-TR'). Dessa språk är tillgängliga i både API och Video Indexer webbplats. 
 
### <a name="search-by-topic-in-video-indexer-website"></a>Sök efter ämne på Video Indexer webbplats 

Du kan nu använda sökfunktionen överst på sidan Video Indexer [webbplats](https://www.videoindexer.ai/account/login) för att söka efter videor med specifika ämnen. 

## <a name="february-2021"></a>Februari 2021

### <a name="multiple-account-owners"></a>Flera kontoinnehavare 

Rollen kontoägare har lagts till i Video Indexer. Du kan lägga till, ändra och ta bort användare. ändra sin roll. Mer information om hur du delar ett konto finns i [Bjud in användare.](invite-users.md)

### <a name="audio-event-detection-public-preview"></a>Identifiering av ljudhändelse (offentlig förhandsversion)

> [!NOTE]
> Den här funktionen är endast tillgänglig i utvärderingskonton. 

Video Indexer identifierar nu följande ljudeffekter i innehållets icke-talsegment: kanonshot, glasser, larm, siren, explosion, hunddricks, barnlyssnare, publika reaktioner (rop, klappning och rop) och tystnad. 

Den nya ljudindelningen påverkar funktionen när du indexerar filen genom att välja alternativet Avancerat förinställt ljud  ->   (under Video + ljudindexering). Standardindexering omfattar endast tystnad **och** reaktioner **från människor.** 

Den **typ av klappningshändelse** som ingick i den tidigare ljudeffektsmodellen extraheras nu som en del av händelsen **för** reaktioner hos människor.

När du väljer att **se insikter** från videon [på Video Indexer](https://www.videoindexer.ai/) webbplats visas ljudeffekterna på sidan.

:::image type="content" source="./media/release-notes/audio-detection.png" alt-text="Identifiering av ljudhändelse":::

### <a name="named-entities-enhancement"></a>Förbättrad namngivna entiteter  

Den extraherade listan över personer och platser har utökats och uppdaterats i allmänhet. 

Dessutom innehåller modellen nu personer och platser i ett sammanhang som inte är berömda, till exempel "Sam" eller "Home" i videon. 

## <a name="january-2021"></a>Januari 2021

### <a name="video-indexer-is-deployed-on-us-government-cloud"></a>Video Indexer distribueras i US Government-molnet 

Nu kan du skapa ett Video Indexer ett betalt konto i regionerna Virginia och Arizona för amerikanska myndigheter. Video Indexer kostnadsfri utvärderingsversion är inte tillgängligt i den här regionen. Mer information finns i Video Indexer dokumentationen. 

### <a name="video-indexer-deployed-in-the-india-central-region"></a>Video Indexer distribueras i regionen Indien, centrala 

Nu kan du skapa ett Video Indexer betalkonto i regionen Indien, centrala. 

### <a name="new-dark-mode-for-the-video-indexer-website-experience"></a>Nytt mörkt läge för Video Indexer webbplatsen

De Video Indexer webbplatsupplevelserna är nu tillgängliga i mörkt läge. Om du vill aktivera mörkt läge öppnar du inställningspanelen och växlar alternativet **Mörkt** läge. 

:::image type="content" source="./media/release-notes/dark-mode.png" alt-text="Inställning för mörkt läge":::

## <a name="december-2020"></a>December 2020

### <a name="video-indexer-deployed-in-the-switzerland-west-and-switzerland-north"></a>Video Indexer i Schweiz, västra och Schweiz, norra

Nu kan du skapa ett Video Indexer betalkonto i regionerna Schweiz, västra och Schweiz, norra.

## <a name="october-2020"></a>Oktober 2020

### <a name="animated-character-identification-improvements"></a>Förbättrad animerad teckenidentifiering  

Video Indexer har stöd för identifiering, gruppering och igenkänning av tecken i animerat innehåll via integrering med Cognitive Services Custom Vision. Vi har lagt till en stor förbättring av den här AI-algoritmen i identifieringen och teckenigenkänningen, eftersom precisionen för insikter och identifierade tecken har förbättrats avsevärt.

### <a name="planned-video-indexer-website-authenticatication-changes"></a>Planerade Video Indexer ändringar av webbplatsens autentisering

Från och med 1 mars 2021 kommer du inte längre att kunna registrera dig och logga in på [Video Indexer-webbplatsens](https://www.videoindexer.ai/) [](video-indexer-use-apis.md) utvecklarportal med Facebook eller LinkedIn.

Du kommer att kunna registrera dig och logga in med någon av dessa leverantörer: Azure AD, Microsoft och Google.

> [!NOTE]
> De Video Indexer-konton som är anslutna till LinkedIn och Facebook kommer inte att vara tillgängliga efter 1 mars 2021. 
> 
> Du bör [bjuda in](invite-users.md) en e-postadress för Azure AD, Microsoft eller Google som du äger Video Indexer-kontot så att du fortfarande har åtkomst. Du kan lägga till ytterligare en ägare till leverantörer som stöds, enligt beskrivningen [i](invite-users.md)Bjud in . <br/>
> Du kan också skapa ett betalt konto och migrera data.

## <a name="august-2020"></a>Augusti 2020

### <a name="mobile-design-for-the-video-indexer-website"></a>Mobil design för Video Indexer webbplatsen

Nu Video Indexer webbplatsen stöd för mobila enheter. Användarupplevelsen anpassas efter din mobila skärmstorlek (exklusive anpassnings-API:er). 

### <a name="accessibility-improvements-and-bug-fixes"></a>Hjälpmedelsförbättringar och felkorrigeringar 

Som en del av WCAG (riktlinjer för hjälpmedel för webbinnehåll) är Video Indexer webbplatsupplevelserna anpassade till klass C, som en del av Microsofts hjälpmedelsstandarder. Flera buggar och förbättringar relaterade till tangentbordsnavigering, programmatisk åtkomst och skärmläsare löstes. 

## <a name="july-2020"></a>Juli 2020

### <a name="ga-for-multi-language-identification"></a>GA för identifiering på flera språk

Identifiering på flera språk flyttas från förhandsversion till GA och är redo för produktiv användning.

Det finns ingen prispåverkan som rör övergången "förhandsversion till ga".

### <a name="video-indexer-website-improvements"></a>Video Indexer webbplatsförbättringar

#### <a name="adjustments-in-the-video-gallery"></a>Justeringar i videogalleriet

Nytt sökfält för djupinsiktssökning med ytterligare filtreringsfunktioner har lagts till. Sökresultaten har också förbättrats.

Ny listvy med möjlighet att sortera och hantera videoarkiv med flera filer.

#### <a name="new-panel-for-easy-selection-and-configuration"></a>Ny panel för enkelt val och konfiguration

Sidopanelen för enkelt val och användarkonfiguration har lagts till, vilket gör att du snabbt och enkelt kan skapa och dela konton samt konfigurera inställningar.

Sidopanelen används också för användarinställningar och hjälp.

## <a name="june-2020"></a>Juni 2020

### <a name="search-by-topics"></a>Söka efter ämnen

Du kan nu använda sök-API:et för att söka efter videor med specifika ämnen (endast API).

Ämnen läggs till som en del av `textScope` (valfri parameter). Mer information finns i [API.](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Search-Videos)  

### <a name="labels-enhancement"></a>Förbättringar av etiketter

Etiketttaggaren har uppgraderats och innehåller nu fler visuella etiketter som kan identifieras.

## <a name="may-2020"></a>Maj 2020

### <a name="video-indexer-deployed-in-the-east-us"></a>Video Indexer distribueras i USA, östra

Nu kan du skapa ett Video Indexer betalkonto i regionen USA, östra.
 
### <a name="video-indexer-url"></a>Video Indexer URL

Video Indexer regionala slutpunkter var alla enhetliga för att endast börja med www. Inget åtgärdsobjekt krävs.

Från och med nu når du www.videoindexer.ai om det är för inbäddningswidgetar eller för att logga in Video Indexer webbappar.

Även wus.videoindexer.ai omdirigeras till www. Mer information finns i Bädda in [Video Indexer widgetar i dina appar.](video-indexer-embed-widgets.md)

## <a name="april-2020"></a>April 2020

### <a name="new-widget-parameters-capabilities"></a>Nya funktioner för widgetparametrar

**Insiktswidgeten** innehåller nya parametrar: och `language` `control` .

**Spelarwidgeten** har en ny `locale` parameter. Både `locale` - `language` och -parametrarna styr spelarens språk.

Mer information finns i avsnittet [om widgettyper.](video-indexer-embed-widgets.md#widget-types) 

### <a name="new-player-skin"></a>Ny spelares hud

En ny spelare som har startats med uppdaterad design.

### <a name="prepare-for-upcoming-changes"></a>Förbereda för kommande ändringar

* I dag returnerar följande API:er ett kontoobjekt:

    * [Skapa ett betalt konto](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Paid-Account)
    * [Hämta konto](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Account)
    * [Get-Accounts-Authorization](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Accounts-Authorization)
    * [Hämta konton med token](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Accounts-With-Token)
 
    Objektet Konto har ett `Url` fält som pekar på platsen för Video Indexer [webbplats](https://www.videoindexer.ai/).
För betalda konton `Url` pekar fältet för närvarande på en intern URL i stället för den offentliga webbplatsen.
Under de kommande veckorna kommer vi att ändra den och returnera [url Video Indexer webbplatsen](https://www.videoindexer.ai/) för alla konton (utvärderingsversion och betald).

    Använd inte de interna URL:erna, du bör använda de Video Indexer [offentliga API:erna](https://api-portal.videoindexer.ai/).
* Om du bäddar in Video Indexer-URL:er i dina program och URL:erna inte pekar på [Video Indexer-webbplatsen](https://www.videoindexer.ai/) eller Video Indexer API-slutpunkten ( ), utan i stället till en regional slutpunkt (till exempel `https://api.videoindexer.ai` ), `https://wus2.videoindexer.ai` återskapar du URL:erna.

   Du kan göra det genom att antingen:

    * Ersätta URL:en med en URL som pekar Video Indexer API:er för widgeten (till exempel [insiktswidgeten](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Insights-Widget))
    * Använd Video Indexer webbplats för att generera en ny inbäddad URL:
         
         Tryck **på** Spela upp för att komma till **&lt; / &gt;** videons sida > på knappen Bädda in - > kopiera URL:en till ditt program:
   
    Regionala URL:er stöds inte och kommer att blockeras under de kommande veckorna.

## <a name="january-2020"></a>Januari 2020
 
### <a name="custom-language-support-for-additional-languages"></a>Anpassat språkstöd för ytterligare språk

Video Indexer stöder nu anpassade språkmodeller `ar-SY` för , och `en-UK` `en-AU` (endast API).
 
### <a name="delete-account-timeframe-action-update"></a>Åtgärdsuppdatering för ta bort tidsram för konto

Åtgärden Ta bort konto tar nu bort kontot inom 90 dagar i stället för 48 timmar.
 
### <a name="new-video-indexer-github-repository"></a>Ny Video Indexer GitHub-lagringsplats

En ny Video Indexer GitHub med olika projekt, guider för att komma igång och kodexempel är nu tillgängliga: https://github.com/Azure-Samples/media-services-video-indexer
 
### <a name="swagger-update"></a>Swagger-uppdatering

Video Indexer enhetliga **autentiseringar och** åtgärder **till** en enda [Video Indexer OpenAPI-specifikation (swagger).](https://api-portal.videoindexer.ai/api-details#api=Operations&operation) Utvecklare hittar API:erna i [Video Indexer Developer Portal](https://api-portal.videoindexer.ai/).

## <a name="december-2019"></a>December 2019

### <a name="update-transcript-with-the-new-api"></a>Uppdatera avskriften med det nya API:et

Uppdatera ett specifikt avsnitt i avskriften med hjälp [av API:et Update-Video-Index.](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Index)

### <a name="fix-account-configuration-from-the-video-indexer-portal"></a>Åtgärda kontokonfigurationen från Video Indexer portalen

Du kan nu uppdatera Media Services-anslutningskonfigurationen för att självhjälpa med problem som: 

* felaktig Azure Media Services resurs
* lösenordsändringar
* Media Services har flyttats mellan prenumerationer  

Du kan åtgärda kontokonfigurationen genom att gå Video Indexer till fliken Inställningar > konto (som ägare).

### <a name="configure-the-custom-vision-account"></a>Konfigurera Custom Vision-kontot

Konfigurera Custom Vision-kontot för betalda konton med hjälp Video Indexer -portalen (tidigare stöds detta endast av API). Det gör du genom att logga in på Video Indexer portalen, välja Modellanpassning och > animerade tecken > Konfigurera. 

### <a name="scenes-shots-and-keyframes--now-in-one-insight-pane"></a>Scener, bilder och nyckelbilder – nu i ett insiktsfönster

Scener, bilder och nyckelbilder sammanfogas nu i en enda insikt för enklare förbrukning och navigering. När du väljer önskad scen kan du se vilka bilder och nyckelbildbilder som den består av. 

### <a name="notification-about-a-long-video-name"></a>Meddelande om ett långt videonamn

När ett videonamn är längre än 80 tecken visas Video Indexer ett beskrivande fel vid uppladdning.

### <a name="streaming-endpoint-is-disabled-notification"></a>Meddelande om att slutpunkten för direktuppspelning är inaktiverad

När strömningsslutpunkten är inaktiverad Video Indexer ett beskrivande fel på spelarsidan.

### <a name="error-handling-improvement"></a>Förbättring av felhantering

Statuskod 409 returneras nu från API:erna [Re-Index Video](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Re-Index-Video) och [Update Video Index](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Index) om en video aktivt indexeras, för att förhindra att de aktuella omindexeringsändringarna åsidosätts av misstag.

## <a name="november-2019"></a>November 2019
 
* Stöd för koreanska anpassade språkmodeller

    Video Indexer har nu stöd för anpassade språkmodeller på koreanska ( `ko-KR` ) i både API:et och portalen. 
* Nya språk som stöds för tal till text (STT)

    Video Indexer API:er har nu stöd för STT på arabiska Levantine (ar-SY), engelska dialekter i Storbritannien (en-GB) och engelska australiska dialekter (en-AU).
    
    För videouppladdning har vi ersatt zh-HANS till zh-CN, båda stöds men zh-CN rekommenderas och är mer exakt.
    
## <a name="october-2019"></a>Oktober 2019
 
* Söka efter animerade tecken i galleriet

    När du indexerar animerade tecken kan du nu söka efter dem i kontots video galley. Mer information finns i [Animerad teckenigenkänning](animated-characters-recognition.md).

## <a name="september-2019"></a>September 2019
 
Flera framsteg presenterades på IBC 2019:
 
* Animerad teckenläsning (offentlig förhandsversion)

    Möjlighet att identifiera grupp-AD-tecken i animerat innehåll via integrering med Custom Vision. Mer information finns i Identifiering [av animerade tecken.](animated-characters-recognition.md)
* Identifiering av flera språk (offentlig förhandsversion)

    Identifiera segment på flera språk i ljudspåret och skapa en flerspråkig avskrift baserat på dem. Inledande support: engelska, spanska, tyska och franska. Mer information finns i [Identifiera och transkribera innehåll på olika språk automatiskt](multi-language-identification-transcription.md).
* Extrahering av namngiven entitet för Personer och plats

    Extraherar varumärken, platser och personer från tal och visuell text via bearbetning av naturligt språk (NLP).
* Klassificering av typ av redigeringsbild

    Taggning av bilder med redigeringstyper som närbild, medelhög bild, två bilder, inomhus, utomhus osv. Mer information finns i [Identifiering av typ av redigeringsbild.](scenes-shots-keyframes.md#editorial-shot-type-detection)
* Förbättring av ämnes inferens – omfattar nu nivå 2
    
    Ämnesinferensmodellen stöder nu djupare kornighet för IPTC-taxonomin. Läs fullständig information Azure Media Services [den nya AI-baserade innovationen](https://azure.microsoft.com/blog/azure-media-services-new-ai-powered-innovation/).

## <a name="august-2019"></a>Augusti 2019
 
### <a name="video-indexer-deployed-in-uk-south"></a>Video Indexer distribueras i Storbritannien, södra

Nu kan du skapa ett Video Indexer betalkonto i regionen Storbritannien, södra.

### <a name="new-editorial-shot-type-insights-available"></a>Nya insikter om redigeringsbildtyper är tillgängliga

Nya taggar som läggs till i videobilder ger redigering av "bildtyper" för att identifiera dem med vanliga redigeringsfraser som används i arbetsflödet för att skapa innehåll, till exempel: extrem närbild, närbild, bred, medel, två bilder, utomhus, inomhus, vänster ansikte och höger ansikte (finns i JSON).

### <a name="new-people-and-locations-entities-extraction-available"></a>Extrahering av entiteter för nya personer och platser är tillgänglig

Video Indexer identifierar namngivna platser och personer via bearbetning av naturligt språk (NLP) från videons OCR och transkription. Video Indexer använder maskininlärningsalgoritm för att identifiera när specifika platser (till exempel Towern) eller personer (till exempel John Doe) anropas i en video.

### <a name="keyframes-extraction-in-native-resolution"></a>Extrahering av nyckelramar i intern upplösning

Nyckelrutor som extraheras av Video Indexer är tillgängliga i videons ursprungliga upplösning.
 
### <a name="ga-for-training-custom-face-models-from-images"></a>GA för träning av anpassade ansiktsmodeller från bilder

Tränings ansikten från bilder som flyttats från förhandsgranskningsläget till GA (tillgängligt via API och i portalen).

> [!NOTE]
> Det finns ingen prispåverkan som rör övergången "förhandsversion till ga".

### <a name="hide-gallery-toggle-option"></a>Växlingsalternativet Dölj galleri

Användaren kan välja att dölja gallerifliken från portalen (liknar att dölja exempelfliken).
 
### <a name="maximum-url-size-increased"></a>Maximal URL-storlek ökad

Stöd för URL-frågesträng på 4096 (i stället för 2048) för indexering av en video.
 
### <a name="support-for-multi-lingual-projects"></a>Stöd för flera språkprojekt

Projekt kan nu skapas baserat på videor som indexeras på olika språk (endast API).

## <a name="july-2019"></a>Juli 2019

### <a name="editor-as-a-widget"></a>Redigerare som en widget

Den Video Indexer AI-redigeraren är nu tillgänglig som en widget som kan bäddas in i kundprogram.

### <a name="update-custom-language-model-from-closed-caption-file-from-the-portal"></a>Uppdatera anpassad språkmodell från undertextfilen från portalen

Kunder kan tillhandahålla FILFORMAT för VTT, SRT och TTML som indata för språkmodeller på portalens anpassningssida.

## <a name="june-2019"></a>Juni 2019

### <a name="video-indexer-deployed-to-japan-east"></a>Video Indexer distribuerad till Japan, östra

Nu kan du skapa ett Video Indexer betalkonto i regionen Japan, östra.

### <a name="create-and-repair-account-api-preview"></a>Skapa och reparera konto-API (förhandsversion)

Ett nytt API har lagts till som gör att du [kan uppdatera Azure Media Service-anslutningsslutpunkten eller nyckeln](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Paid-Account-Azure-Media-Services).

### <a name="improve-error-handling-on-upload"></a>Förbättra felhantering vid uppladdning 

Ett beskrivande meddelande returneras vid felaktig konfiguration av den underliggande Azure Media Services konto.

### <a name="player-timeline-keyframes-preview"></a>Förhandsgranskning av nyckelbilder för spelarens tidslinje 

Nu kan du se en bildförhandsvisning för varje gång på spelarens tidslinje.

### <a name="editor-semi-select"></a>Halvvälj redigerare

Nu kan du se en förhandsgranskning av alla insikter som väljs när du väljer en specifik tidsram för insikter i redigeraren.

## <a name="may-2019"></a>Maj 2019

### <a name="update-custom-language-model-from-closed-caption-file"></a>Uppdatera anpassad språkmodell från undertextfil

[Skapa anpassad språkmodell och](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Create-Language-Model) [Uppdatera](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Language-Model) API:er för anpassade språkmodeller har nu stöd för MALLT-, SRT- och TTML-filformat som indata för språkmodeller.

När du [anropar API:et för videoavskriftsuppdatering](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Update-Video-Transcript)läggs avskriften till automatiskt. Träningsmodellen som är associerad med videon uppdateras också automatiskt. Information om hur du anpassar och tränar dina språkmodeller finns i [Anpassa en språkmodell med Video Indexer](customize-language-model-overview.md).

### <a name="new-download-transcript-formats--txt-and-csv"></a>Nya format för hämtning av avskrifter – TXT och CSV

Förutom det undertextformat som redan stöds (SRT, TXTT och TTML) stöder Video Indexer nu nedladdning av avskriften i TXT- och CSV-format.

## <a name="next-steps"></a>Nästa steg

[Översikt](video-indexer-overview.md)
