---
title: Bädda in Video Indexer widgetar i dina appar
titleSuffix: Azure Media Services
description: Lär dig hur du bäddar in Video Indexer widgetar i dina appar.
services: media-services
author: Juliako
manager: femila
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 01/25/2021
ms.author: juliako
ms.custom: devx-track-js
ms.openlocfilehash: b13086e11e1181bba91a3255e68e9f8a32e78450
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98797775"
---
# <a name="embed-video-indexer-widgets-in-your-apps"></a>Bädda in Video Indexer widgetar i dina appar

Den här artikeln visar hur du kan bädda in Video Indexer widgetar i dina appar. Video Indexer har stöd för inbäddning av tre typer av widgetar i dina appar: *Insikt*, *Spelare* och *Redigerare*.

Från och med version 2 innehåller widgeten bas-URL den region där det angivna kontot finns. Ett konto i regionen Västra USA genererar till exempel: `https://www.videoindexer.ai/embed/insights/.../?location=westus2` .

## <a name="widget-types"></a>Widgettyper

### <a name="cognitive-insights-widget"></a>Insiktswidget

En insiktswidget innehåller alla visuella insikter som extraherades från videoindexeringsprocessen. Widgeten kognitiv Insights stöder följande valfria URL-parametrar:

|Name|Definition|Beskrivning|
|---|---|---|
|`widgets` | Strängar avgränsade med kommatecken | Gör att du kan styra de insikter som du vill återge.<br/>Exempel: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords` återger endast användare och nyckelord UI insikter.<br/>Tillgängliga alternativ: People, animatedCharacters, keywords, Labels, sentiment, känslor, topics, nyckel rutor, avskrift, OCR, talare, scener och namedEntities.|
|`controls`|Strängar avgränsade med kommatecken|Gör att du kan styra de kontroller som du vill återge.<br/>Exempel: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?controls=search,download` återger bara sökalternativet och knappen Hämta.<br/>Tillgängliga alternativ: Sök, ladda ned, för inställningar, språk.|
|`language`|En kort språkkod (språk namn)|Kontrollerar insikter-språk.<br/>Exempel: `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=es-es` <br/>eller `https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?language=spanish`|
|`locale` | En kort språkkod | Styr språket för användar gränssnittet. Standardvärdet är `en`. <br/>Exempel: `locale=de`.|
|`tab` | Den valda fliken standard | Styr fliken **insikter** som återges som standard. <br/>Exempel: `tab=timeline` återger insikter med fliken **tids linje** vald.|
|`location` ||`location`Parametern måste inkluderas i de inbäddade länkarna, se [hur du hämtar namnet på din region](regions.md). Om ditt konto är i för hands version `trial` ska det användas för plats-värdet. `trial` är standardvärdet för `location` parametern.| 

### <a name="player-widget"></a>Spelarwidget

Du kan använda Player-widgeten för att strömma video med hjälp av anpassningsbar bit hastighet. Widgeten Player stöder följande valfria URL-parametrar.

|Name|Definition|Beskrivning|
|---|---|---|
|`t` | Sekunder från start | Gör att spelaren startar uppspelningen från den angivna tids punkten.<br/> Exempel: `t=60`. |
|`captions` | En språkkod | Hämtar texten på det angivna språket när widget inläsningen ska vara tillgänglig på menyn under **texter** .<br/> Exempel: `captions=en-US`. |
|`showCaptions` | Ett booleskt värde | Gör att spelaren läses in med aktiverad textning.<br/> Exempel: `showCaptions=true`. |
|`type`| | Aktiverar ett ljuds pelar skal (video delen tas bort).<br/> Exempel: `type=audio`. |
|`autoplay` | Ett booleskt värde | Anger om spelaren ska börja spela upp videon vid inläsning. Standardvärdet är `true`.<br/> Exempel: `autoplay=false`. |
|`language`/`locale` | En språkkod | Kontrollerar Player-språket. Standardvärdet är `en-US`.<br/>Exempel: `language=de-DE`.|
|`location` ||`location`Parametern måste inkluderas i de inbäddade länkarna, se [hur du hämtar namnet på din region](regions.md). Om ditt konto är i för hands version `trial` ska det användas för plats-värdet. `trial` är standardvärdet för `location` parametern.| 

### <a name="editor-widget"></a>Editor-widget

Du kan använda Editor-widgeten för att skapa nya projekt och hantera video insikter. Editor-widgeten stöder följande valfria URL-parametrar.

|Name|Definition|Beskrivning|
|---|---|---|
|`accessToken`<sup>*</sup> | Sträng | Ger åtkomst till videor som bara finns i det konto som används för att bädda in widgeten.<br> Widgeten Editor kräver `accessToken` parametern. |
|`language` | En språkkod | Kontrollerar Player-språket. Standardvärdet är `en-US`.<br/>Exempel: `language=de-DE`. |
|`locale` | En kort språkkod | Styr insikter-språket. Standardvärdet är `en`.<br/>Exempel: `language=de`. |
|`location` ||`location`Parametern måste inkluderas i de inbäddade länkarna, se [hur du hämtar namnet på din region](regions.md). Om ditt konto är i för hands version `trial` ska det användas för plats-värdet. `trial` är standardvärdet för `location` parametern.| 

<sup>*</sup>Ägaren bör ge en `accessToken` varning.

## <a name="embedding-videos"></a>Bädda in videor

I det här avsnittet beskrivs hur du bäddar in offentligt och privat innehåll i appar.

`location`Parametern måste inkluderas i de inbäddade länkarna, se [hur du hämtar namnet på din region](regions.md). Om ditt konto är i för hands version `trial` ska det användas för plats-värdet. `trial` är standardvärdet för `location` parametern. Exempel: `https://www.videoindexer.ai/accounts/00000000-0000-0000-0000-000000000000/videos/b2b2c74b8e/?location=trial`.

> [!IMPORTANT]
> Om du delar en länk för **Player** -eller **Insights** -widgeten ingår åtkomsttoken och beviljar Läs behörighet till ditt konto.

### <a name="public-content"></a>Offentligt innehåll

1. Logga in på [video Indexer](https://www.videoindexer.ai/) webbplats.
1. Välj den video som du vill arbeta med och tryck på **Play**.
1. Välj vilken typ av widget du vill ha (**kognitiva insikter**, **spelare** eller **redigerare**).
1. Klicka på **&lt; / &gt; bädda in**.
5. Kopiera inbäddnings koden (visas i **Kopiera den inbäddade koden** i dialog rutan **resurs & bädda in** ).
6. Lägg till koden i din app.

### <a name="private-content"></a>Privat innehåll

Om du vill bädda in en privat video måste du skicka en åtkomsttoken i `src` attributet för iframe:

`https://www.videoindexer.ai/embed/[insights | player]/<accountId>/<videoId>/?accessToken=<accessToken>`
    
Använd någon av följande metoder för att hämta innehåll för kognitiv Insights-widget:

- API för [Get Insights-widgeten](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) .<br/>
- [Hämta video åtkomst-token](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Get-Video-Access-Token?). Lägg till den som en frågeparameter till URL: en. Ange den här URL: en som `src` värde för iframe, som visas ovan.

Om du vill ge redigerings funktioner för insikter i din inbäddade widget måste du skicka en åtkomsttoken som innehåller redigerings behörigheter. Använd [Get Insights-widgeten](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Insights-Widget?&pattern=widget) eller [Hämta video åtkomst-token](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Video-Access-Token?) med `&allowEdit=true` .

## <a name="widgets-interaction"></a>Interaktion med widgetar

Widgeten kognitiv Insights kan interagera med en video i din app. Det här avsnittet visar hur du uppnår den här interaktionen.

![Widgeten för kognitiva insikter Video Indexer](./media/video-indexer-embed-widgets/video-indexer-widget03.png)

### <a name="flow-overview"></a>Flödes översikt

När du redigerar avskrifterna inträffar följande flöde:

1. Du redigerar avskriften i tids linjen.
1. Video Indexer hämtar de här uppdateringarna och sparar dem i [från avskrifts redigeringarna](customize-language-model-with-website.md#customize-language-models-by-correcting-transcripts) i språk modellen.
1. Rubrikerna uppdateras:

    * Om du använder Video Indexer Player-widgeten uppdateras den automatiskt.
    * Om du använder en extern spelare får du en ny bild text fil som du kan använda för att **Hämta video under texter** .

### <a name="cross-origin-communications"></a>Kommunikation mellan ursprung

För att få Video Indexer widgetar för att kommunicera med andra komponenter, Video Indexer-tjänsten:

- Använder metoden för kommunikation med cross-origin `postMessage` .
- Verifierar meddelandet utifrån VideoIndexer.ai-ursprung.

Om du implementerar din egen Player-kod och integrerar med kognitiv Insights-widget är det ditt ansvar att verifiera ursprunget för meddelandet som kommer från VideoIndexer.ai.

### <a name="embed-widgets-in-your-app-or-blog-recommended"></a>Bädda in widgetar i din app eller blogg (rekommenderas)

I det här avsnittet visas hur du uppnår interaktionen mellan två Video Indexer-widgetar, så att spelaren går till relevant stund när en användare väljer kontrollen Insight i appen.

1. Kopiera inbäddningskoden för spelarwidgeten.
2. Kopiera inbäddningskoden för insiktswidgeten.
3. Lägg till [medlarfilen](https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js) för att hantera kommunikationen mellan de två widgetarna:<br/> 
`<script src="https://breakdown.blob.core.windows.net/public/vb.widgets.mediator.js"></script>`

Nu när en användare väljer kontrollen Insight i appen, hoppar spelaren till relevant stund.

Mer information finns i [demonstrationen av video Indexer-bädda in båda widgetarna](https://codepen.io/videoindexer/pen/NzJeOb).

### <a name="embed-the-cognitive-insights-widget-and-use-azure-media-player-to-play-the-content"></a>Bädda in insiktswidgeten och använda Azure Media Player för att spela upp innehållet

I det här avsnittet visas hur du uppnår interaktionen mellan en kognitiv Insights-widget och en Azure Media Player-instans med hjälp av [amp-plugin-programmet](https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js).

1. Lägg till ett Video Indexer-plugin-program för AMP Player:<br/> `<script src="https://breakdown.blob.core.windows.net/public/amp-vb.plugin.js"></script>`
2. Instansiera Azure Media Player med plugin-programmet Video Indexer.

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

Mer information finns i [demonstrationen Azure Media Player + vi Insights](https://codepen.io/videoindexer/pen/rYONrO).

### <a name="embed-the-video-indexer-cognitive-insights-widget-and-use-a-different-video-player"></a>Bädda in widgeten Video Indexer kognitiva insikter och Använd en annan Videos pelare

Om du använder en annan Videos pelare än Azure Media Player måste du manuellt ändra Videos pelaren för att uppnå kommunikationen.

1. Infoga videospelaren.

    Till exempel en standard HTML5-spelare:

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

Mer information finns i [demonstrationen Azure Media Player + vi Insights](https://codepen.io/videoindexer/pen/YEyPLd).

## <a name="adding-subtitles"></a>Lägga till undertexter

Om du bäddar in Video Indexer insikter med dina egna [Azure Media Player](https://aka.ms/azuremediaplayer)kan du använda `GetVttUrl` metoden för att hämta textning (under texter). Du kan också anropa en JavaScript-metod från Video Indexer AMP-plugin-programmet `getSubtitlesUrl` (se tidigare).

## <a name="customizing-embeddable-widgets"></a>Anpassa inbäddningsbara widgetar

### <a name="cognitive-insights-widget"></a>Insiktswidget

Du kan välja vilka typer av insikter du vill. Det gör du genom att ange dem som ett värde för följande URL-parameter som läggs till i den inbäddnings kod som du får (från API: et eller från webb programmet): `&widgets=<list of wanted widgets>` .

De möjliga värdena är:,,,,,,, `people` `animatedCharacters` `keywords` `labels` `sentiments` `emotions` `topics` `keyframes` `transcript` , `ocr` ,, `speakers` `scenes` , och `namedEntities` .

Om du till exempel vill bädda in en widget som bara innehåller användare och nyckelords insikter så ser iframe-inbäddningen ut så här:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?widgets=people,keywords`

Rubriken för iframe-fönstret kan också anpassas genom att tillhandahålla `&title=<YourTitle>` iframe-URL: en. (Den anpassar HTML- `<title>` värdet).
   
Om du till exempel vill ge iframe-fönstret rubriken "mina insikter" ser URL: en ut så här:

`https://www.videoindexer.ai/embed/insights/<accountId>/<videoId>/?title=MyInsights`

Observera att det här alternativet endast är relevant i fall när du behöver öppna insikterna i ett nytt fönster.

### <a name="player-widget"></a>Spelarwidget

Om du bäddar in Video Indexer-spelaren kan du välja storlek för spelaren genom att ange storlek på iframe-fönstret.

Exempel:

`<iframe width="640" height="360" src="https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/" frameborder="0" allowfullscreen />`

Som standard har Video Indexer Player automatiskt genererade textning som baseras på avskriften av videon. Avskriften extraheras från videon med det käll språk som valdes när videon laddades upp.

Om du vill bädda in med ett annat språk kan du lägga till i `&captions=<Language Code>` URL: en för inbäddning av Player. Om du vill att rubrikerna ska visas som standard kan du pass &showCaptions = True.

Inbäddnings-URL: en kommer sedan att se ut så här:

`https://www.videoindexer.ai/embed/player/<accountId>/<videoId>/?captions=en-us`

#### <a name="autoplay"></a>Spela

Som standard startar spelaren uppspelning av videon. Du kan välja att inte skicka `&autoplay=false` till den tidigare inbäddnings-URL: en.

## <a name="code-samples"></a>Kodexempel

Se de [kod exempel](https://github.com/Azure-Samples/media-services-video-indexer/tree/master/Embedding%20widgets) lagrings platsen som innehåller exempel för video INDEXER-API och widgetar:

| Fil/mapp                       | Beskrivning                                |
|-----------------------------------|--------------------------------------------|
| `azure-media-player`              | Läs in video Indexer-video i en anpassad Azure Media Player.                        |
| `azure-media-player-vi-insights`  | Bädda in VI insikter med en anpassad Azure Media Player.                             |
| `control-vi-embedded-player`      | Bädda in VI Player och kontrol lera den från utsidan.                                    |
| `custom-index-location`           | Bädda in VI insikter från en anpassad extern plats (kan vara kund en BLOB).     |
| `embed-both-insights`             | Grundläggande användning av VI insikter både i spelare och insikter.                            |
| `embed-insights-with-AMP`         | Bädda in VI Insights-widget med en anpassad Azure Media Player.                      |
| `customize-the-widgets`           | Bädda in VI-widgetar med anpassade alternativ.                                     |
| `embed-both-widgets`              | Bädda in VI och insikter och kommunicera mellan dem.                      |
| `url-generator`                   | Genererar anpassad inbäddnings-URL för widget baserat på användardefinierade alternativ.             |
| `html5-player`                    | Bädda in VI insikter med en standard HTML5 Videos pelare.                           |

## <a name="supported-browsers"></a>Webbläsare som stöds

Mer information finns i [webbläsare som stöds](video-indexer-overview.md#supported-browsers).

## <a name="next-steps"></a>Nästa steg

Information om hur du visar och redigerar Video Indexer insikter finns i [Visa och redigera video Indexer insikter](video-indexer-view-edit.md).

Ta också en titt på [video Indexer CodePen](https://codepen.io/videoindexer/pen/eGxebZ).
