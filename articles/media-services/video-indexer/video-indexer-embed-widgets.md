---
title: Bädda Video Indexer widgetar i dina appar
titleSuffix: Azure Media Services
description: Lär dig hur du bäddar Video Indexer widgetar i dina appar.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/25/2021
ms.author: juliako
ms.custom: devx-track-js
ms.openlocfilehash: 822d50bca6bc1139e9b5f0554bcf9a56a8fcbd74
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532883"
---
# <a name="embed-video-indexer-widgets-in-your-apps"></a>Bädda Video Indexer widgetar i dina appar

Den här artikeln visar hur du kan bädda Video Indexer widgetar i dina appar. Video Indexer har stöd för inbäddning av tre typer av widgetar i dina appar: *Insikt*, *Spelare* och *Redigerare*.

Från och med version 2 innehåller widgetens bas-URL regionen för det angivna kontot. Till exempel genererar ett konto i regionen USA, västra: `https://www.videoindexer.ai/embed/insights/.../?location=westus2` .

## <a name="widget-types"></a>Widgettyper

### <a name="cognitive-insights-widget"></a>Insiktswidget

En insiktswidget innehåller alla visuella insikter som extraherades från videoindexeringsprocessen. Insiktswidgeten stöder följande valfria URL-parametrar:

|Name|Definition|Description|
|---|---|---|
|`widgets` | Strängar avgränsade med kommatecken | Gör att du kan styra de insikter som du vill återge.<br/>Exempel: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords` återger endast insikter om personer och nyckelord i användargränssnittet.<br/>Tillgängliga alternativ: people, animatedCharacters, keywords, labels, sentiments, emotions, topics, keyframes, transcript, ocr, speakers, scenes och namedEntities.|
|`controls`|Strängar avgränsade med kommatecken|Gör att du kan styra de kontroller som du vill rendera.<br/>Exempel: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?controls=search,download` renderar endast sökalternativ och nedladdningsknapp.<br/>Tillgängliga alternativ: sök, ladda ned, förinställningar, språk.|
|`language`|En kort språkkod (språknamn)|Styr insiktsspråket.<br/>Exempel: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=es-es` <br/>Eller `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=spanish`|
|`locale` | En kort språkkod | Styr språket i användargränssnittet. Standardvärdet är `en`. <br/>Exempel: `locale=de`.|
|`tab` | Den valda standardfliken | Styr **fliken Insikter** som återges som standard. <br/>Exempel: `tab=timeline` återger insikterna med **fliken Tidslinje** vald.|
|`location` ||Parametern `location` måste inkluderas i de inbäddade länkarna. Se [hur du hämtar namnet på din region.](regions.md) Om ditt konto är i förhandsversion `trial` ska användas för platsvärdet. `trial` är standardvärdet för `location` parametern .| 

### <a name="player-widget"></a>Spelarwidget

Du kan använda spelarwidgeten för att strömma video med anpassningsbar bithastighet. Spelarwidgeten stöder följande valfria URL-parametrar.

|Name|Definition|Description|
|---|---|---|
|`t` | Sekunder från början | Gör att spelaren börjar spela upp från den angivna tidpunkten.<br/> Exempel: `t=60`. |
|`captions` | En språkkod | Hämtar undertexten på det angivna språket under widgetinläsningen för att vara tillgänglig på **menyn Undertexter.**<br/> Exempel: `captions=en-US`. |
|`showCaptions` | Ett booleskt värde | Gör att spelaren läses in med aktiverad textning.<br/> Exempel: `showCaptions=true`. |
|`type`| | Aktiverar en ljudspelares hud (videodelen tas bort).<br/> Exempel: `type=audio`. |
|`autoplay` | Ett booleskt värde | Anger om spelaren ska börja spela upp videon när den har lästs in. Standardvärdet är `true`.<br/> Exempel: `autoplay=false`. |
|`language`/`locale` | En språkkod | Styr spelarspråket. Standardvärdet är `en-US`.<br/>Exempel: `language=de-DE`.|
|`location` ||Parametern `location` måste inkluderas i de inbäddade länkarna. Se [hur du hämtar namnet på din region.](regions.md) Om ditt konto finns i `trial` förhandsversionen ska användas för platsvärdet. `trial` är standardvärdet för `location` parametern .| 

### <a name="editor-widget"></a>Redigeringswidget

Du kan använda redigeringswidgeten för att skapa nya projekt och hantera en videoinsikter. Redigerarwidgeten stöder följande valfria URL-parametrar.

|Name|Definition|Description|
|---|---|---|
|`accessToken`<sup>*</sup> | Sträng | Ger åtkomst till videor som bara finns i det konto som används för att bädda in widgeten.<br> Redigeringswidgeten kräver `accessToken` parametern . |
|`language` | En språkkod | Styr spelarspråket. Standardvärdet är `en-US`.<br/>Exempel: `language=de-DE`. |
|`locale` | En kort språkkod | Styr insiktsspråket. Standardvärdet är `en`.<br/>Exempel: `language=de`. |
|`location` ||Parametern `location` måste inkluderas i de inbäddade länkarna. Se [hur du hämtar namnet på din region.](regions.md) Om ditt konto är i förhandsversion `trial` ska användas för platsvärdet. `trial` är standardvärdet för `location` parametern .| 

<sup>*</sup>Ägaren bör vara `accessToken` försiktig.

## <a name="embed-videos"></a>Bädda in videor

I det här avsnittet beskrivs hur du bäddar in videor [med hjälp av portalen](#the-portal-experience) eller genom att montera [URL:en manuellt](#assemble-the-url-manually) i appar. 

Parametern `location` måste inkluderas i de inbäddade länkarna. Se [hur du hämtar namnet på din region.](regions.md) Om ditt konto är i förhandsversion `trial` ska användas för platsvärdet. `trial` är standardvärdet för `location` parametern . Exempel: `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`.

### <a name="the-portal-experience"></a>Portalupplevelsen

Om du vill bädda in en video använder du portalen enligt beskrivningen nedan:

1. Logga in på [Video Indexer](https://www.videoindexer.ai/) webbplats.
1. Välj den video som du vill arbeta med och tryck på **Spela upp.**
1. Välj den typ av widget som du vill ha (**Cognitive Insights,** **Player** eller **Editor**).
1. Klicka **&lt; / &gt; på Bädda in**.
5. Kopiera inbäddningskoden (visas i **Kopiera den inbäddade koden** i dialogrutan Dela & **Bädda** in).
6. Lägg till koden i din app.

> [!NOTE]
> Om du delar en länk **för spelar-** **eller insiktswidgeten** inkluderas åtkomsttoken och de skrivskyddade behörigheterna beviljas till ditt konto.

### <a name="assemble-the-url-manually"></a>Sätt ihop URL:en manuellt

#### <a name="public-videos"></a>Offentliga videor

Du kan bädda in offentliga videor som monterar URL:en på följande sätt:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>`
  
  
#### <a name="private-videos"></a>Privata videor

Om du vill bädda in en privat video måste du skicka en åtkomsttoken (använd [Hämta videoåtkomsttoken](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Access-Token) `src` i attributet för iframe:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
  
### <a name="provide-editing-insights-capabilities"></a>Tillhandahålla funktioner för redigering av insikter

Om du vill ge funktioner för redigering av insikter i din inbäddade widget måste du skicka en åtkomsttoken som innehåller redigeringsbehörigheter. Använd [Hämta videoåtkomsttoken](https://api-portal.videoindexer.ai/api-details#api=Operations&operation=Get-Video-Access-Token) med `&allowEdit=true` .

## <a name="widgets-interaction"></a>Interaktion med widgetar

Insiktswidgeten kan interagera med en video i din app. Det här avsnittet visar hur du uppnår den här interaktionen.

![Insiktswidget Video Indexer](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="flow-overview"></a>Flow-översikt

När du redigerar avskrifterna sker följande flöde:

1. Du redigerar avskriften på tidslinjen.
1. Video Indexer hämtar dessa uppdateringar och sparar dem i [från avskriftsredigeringar](customize-language-model-with-website.md#customize-language-models-by-correcting-transcripts) i språkmodellen.
1. Undertexterna uppdateras:

    * Om du använder Video Indexer spelarwidgeten uppdateras den automatiskt.
    * Om du använder en extern spelare får du en ny undertextfilanvändare med anropet **Hämta videotexter.**

### <a name="cross-origin-communications"></a>Kommunikation mellan ursprung

För att Video Indexer att få widgetar att kommunicera med andra komponenter Video Indexer tjänsten:

- Använder HTML5-metoden för kommunikation mellan `postMessage` ursprung.
- Verifierar meddelandet utifrån VideoIndexer.ai-ursprung.

Om du implementerar din egen spelarkod och integrerar med Insiktswidgetar är det ditt ansvar att verifiera ursprunget för meddelandet som kommer från VideoIndexer.ai.

### <a name="embed-widgets-in-your-app-or-blog-recommended"></a>Bädda in widgetar i din app eller blogg (rekommenderas)

Det här avsnittet visar hur du uppnår interaktion mellan två Video Indexer-widgetar så att spelaren hoppar till relevant tidpunkt när en användare väljer insiktskontrollen i din app.

1. Kopiera inbäddningskoden för spelarwidgeten.
2. Kopiera inbäddningskoden för insiktswidgeten.
3. Lägg till [medlarfilen](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) för att hantera kommunikationen mellan de två widgetarna:<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

Nu när en användare väljer insiktskontrollen i din app hoppar spelaren till det relevanta tillfället.

Mer information finns i demonstrationen [Video Indexer - Bädda in båda widgetarna.](https://codepen.io/videoindexer/pen/NzJeOb)

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>Bädda in insiktswidgeten och använda Azure Media Player för att spela upp innehållet

Det här avsnittet visar hur du uppnår interaktion mellan en Insiktswidget och en Azure Media Player instans med hjälp av [AMP-plugin-programmet](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js).

1. Lägg till Video Indexer plugin-program för AMP-spelaren:<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Instansiera Azure Media Player med Video Indexer plugin-programmet.

    ```javascript
    // Init the source.
    function initSource() {
        var tracks = [{
        kind: 'captions',
        // To load vtt from VI, replace it with your vtt URL.
        src: this.getSubtitlesUrl("c4c1ad4c9a", "English"),
        srclang: 'en',
        label: 'English'
        }];
        myPlayer.src([
        {
            "src": "//amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
            "type": "application/vnd.ms-sstr+xml"
        }
        ], tracks);
    }

    // Init your AMP instance.
    var myPlayer = amp('vid1', { /* Options */
        "nativeControlsForTouch": false,
        autoplay: true,
        controls: true,
        width: "640",
        height: "400",
        poster: "",
        plugins: {
        videobreakedown: {}
        }
    }, function () {
        // Activate the plug-in.
        this.videobreakdown({
        videoId: "c4c1ad4c9a",
        syncTranscript: true,
        syncLanguage: true,
        location: "trial" /* location option for paid accounts (default is trial) */
        });

        // Set the source dynamically.
        initSource.call(this);
    });
    ```

3. Kopiera inbäddningskoden för insiktswidgeten.

Nu kan du kommunicera med Azure Media Player.

Mer information finns i [demonstrationen Azure Media Player + VI Insights.](https://codepen.io/videoindexer/pen/rYONrO)

### <a name="embed-the-video-indexer-cognitive-insights-widget-and-use-a-different-video-player"></a>Bädda in Video Indexer Cognitive Insights-widget och använd en annan videospelare

Om du använder en annan videospelare än Azure Media Player måste du manuellt manipulera videospelaren för att uppnå kommunikationen.

1. Infoga videospelaren.

    Till exempel en HTML5-standardspelare:

    ```html
    <video id="vid1" width="640" height="360" controls autoplay preload>
       <source src="//breakdown.blob.core.windows.net/public/Microsoft%20HoloLens-%20RoboRaid.mp4" type="video/mp4" /> 
       Your browser does not support the video tag.
    </video>
    ```

2. Bädda in insiktswidgeten.
3. Implementera kommunikation för spelaren genom att lyssna på ”message”-händelsen. Exempel:

    ```javascript
    <script>
    
        (function(){
        // Reference your player instance.
        var playerInstance = document.getElementById('vid1');
        
        function jumpTo(evt) {
          var origin = evt.origin || evt.originalEvent.origin;
        
          // Validate that the event comes from the videobreakdown domain.
          if ((origin === "https://www.videobreakdown.com") && evt.data.time !== undefined){
                
            // Call your player's "jumpTo" implementation.
            playerInstance.currentTime = evt.data.time;
               
            // Confirm the arrival to us.
            if ('postMessage' in window) {
              evt.source.postMessage({confirm: true, time: evt.data.time}, origin);
            }
          }
        }
        
        // Listen to the message event.
        window.addEventListener("message", jumpTo, false);
          
        }())    
        
    </script>
    ```

Mer information finns i demon [Azure Media Player + VI Insights.](https://codepen.io/videoindexer/pen/YEyPLd)

## <a name="adding-subtitles"></a>Lägga till undertexter

Om du bäddar Video Indexer insikter med dina [egna Azure Media Player](https://aka.ms/azuremediaplayer)kan du använda metoden för att `GetVttUrl` hämta undertexter . Du kan också anropa en JavaScript-metod från Video Indexer AMP-plugin-programmet `getSubtitlesUrl` (som visades tidigare).

## <a name="customizing-embeddable-widgets"></a>Anpassa inbäddningsbara widgetar

### <a name="cognitive-insights-widget"></a>Insiktswidget

Du kan välja vilka typer av insikter du vill ha. Det gör du genom att ange dem som ett värde till följande URL-parameter som läggs till i den inbäddningskod som du får (från API:et eller från webbappen): `&widgets=<list of wanted widgets>` .

Möjliga värden är: `people` , , , , , , , , `animatedCharacters` , , , `keywords` , , `labels` och `sentiments` `emotions` `topics` `keyframes` `transcript` `ocr` `speakers` `scenes` `namedEntities` .

Om du till exempel vill bädda in en widget som endast innehåller insikter om personer och nyckelord ser iframe-inbäddnings-URL:en ut så här:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords`

Iframe-fönstrets rubrik kan också anpassas genom att ange `&title=<YourTitle>` iframe-URL:en. (Det anpassar `<title>` HTML-värdet).
   
Om du till exempel vill ge iframe-fönstret rubriken "MyInsights" ser URL:en ut så här:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

Observera att det här alternativet endast är relevant i fall när du behöver öppna insikterna i ett nytt fönster.

### <a name="player-widget"></a>Spelarwidget

Om du bäddar in Video Indexer-spelaren kan du välja storlek för spelaren genom att ange storlek på iframe-fönstret.

Exempel:

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

Som standard Video Indexer player automatiskt genererade undertexter som baseras på avskriften av videon. Avskriften extraheras från videon med det källspråk som valdes när videon laddades upp.

Om du vill bädda in med ett annat språk kan du lägga till i `&captions=<Language Code>` inbäddningsspelar-URL:en. Om du vill att undertexterna ska visas som standard kan du skicka &showCaptions=true.

Inbäddnings-URL:en ser då ut så här:

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=en-us`

#### <a name="autoplay"></a>Autoplay

Spelaren börjar spela upp videon som standard. du kan välja att inte skicka till `&autoplay=false` föregående inbäddnings-URL.

## <a name="code-samples"></a>Kodexempel

Se [lagringsplatsen med kodexempel](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Embedding%20widgets) som innehåller exempel Video Indexer API och widgetar:

| Fil/mapp                       | Description                                |
|-----------------------------------|--------------------------------------------|
| `azure-media-player`              | Läsa in videoindexeringsvideo i en anpassad Azure Media Player.                        |
| `azure-media-player-vi-insights`  | Bädda in VI Insights med en anpassad Azure Media Player.                             |
| `control-vi-embedded-player`      | Bädda in VI Player och kontrollera den utifrån.                                    |
| `custom-index-location`           | Bädda in VI Insights från en anpassad extern plats (kan vara kund för en blob).     |
| `embed-both-insights`             | Grundläggande användning av VI Insights för både spelare och insikter.                            |
| `embed-insights-with-AMP`         | Bädda in VI Insights-widget med en anpassad Azure Media Player.                      |
| `customize-the-widgets`           | Bädda in VI-widgetar med anpassade alternativ.                                     |
| `embed-both-widgets`              | Bädda in VI Player och Insights och kommunicera mellan dem.                      |
| `url-generator`                   | Genererar widgetar anpassad inbäddnings-URL baserat på användar angivna alternativ.             |
| `html5-player`                    | Bädda in VI Insights med en HTML5-standardvideospelare.                           |

## <a name="supported-browsers"></a>Webbläsare som stöds

Mer information finns i Webbläsare [som stöds.](video-indexer-overview.md#supported-browsers)

## <a name="next-steps"></a>Nästa steg

Information om hur du visar och redigerar Video Indexer insights finns i [Visa och redigera Video Indexer insikter.](video-indexer-view-edit.md)

Kolla även in [Video Indexer CodePen](https://codepen.io/videoindexer/pen/eGxebZ).
