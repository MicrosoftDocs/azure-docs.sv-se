---
title: Smooth Streaming-plugin-program för Media Framework med öppen källkod
description: Lär dig hur du använder Azure Media Services Smooth Streaming-plugin-programmet för medie ramverket Adobe Open Source.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 6068151f-b6b0-4507-9346-f03416d3d572
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.custom: devx-track-csharp
ms.openlocfilehash: 70042a5c4c27ec5d163e5f05089ae50b92140c28
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/11/2021
ms.locfileid: "103011263"
---
# <a name="how-to-use-the-microsoft-smooth-streaming-plugin-for-the-adobe-open-source-media-framework"></a>Använda Microsoft Smooth Streaming-plugin-programmet för Adobe Open Source Media Framework

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

## <a name="overview"></a>Översikt
Microsoft Smooth Streaming-plugin-programmet för Open Source Media Framework 2,0 (SS för OSMF) utökar standard funktionerna i OSMF och lägger till Microsoft Smooth Streaming innehålls uppspelning till nya och befintliga OSMF-spelare. Plugin-programmet lägger också till Smooth Streaming uppspelnings funktioner i SMP (Media uppspelning).

SS för OSMF innehåller två versioner av plugin-programmet:

* Statiskt Smooth Streaming-plugin-program för OSMF (. SWC)
* Dynamiskt Smooth Streaming-plugin-program för OSMF (. swf)

Det här dokumentet förutsätter att läsaren har ett allmänt fungerande kunnande om OSMF-och OSMF-plugin-program. Mer information om OSMF finns i dokumentationen på den [officiella OSMF-webbplatsen](http://osmf.org/).

### <a name="smooth-streaming-plugin-for-osmf-20"></a>Smooth Streaming-plugin-program för OSMF 2,0
Plugin-programmet stöder inläsning och uppspelning av Smooth Streaming innehåll på begäran med följande funktioner:

* Smooth Streaming uppspelning på begäran (spela upp, pausa, söka, stoppa)
* Live Smooth Streaming-uppspelning (uppspelning)
* Direktsända DVR-funktioner (pausa, Sök, DVR-uppspelning, go-to-Live)
* Stöd för video-codec – H. 264
* Stöd för ljud-codec-AAC
* Flera ljud språks växling med inbyggda API: er för OSMF
* Val av maximal uppspelnings kvalitet med inbyggda API: er för OSMF
* Sidvagn med dold textning med OSMF textnings-plugin
* Adobe &reg; Flash &reg; Player 11,4 eller senare.
* Den här versionen stöder endast OSMF 2,0.

## <a name="supported-features-and-known-issues"></a>Funktioner som stöds och kända problem
En fullständig lista över funktioner som stöds, funktioner som inte stöds och kända problem finns i [det här dokumentet](https://azure.microsoft.com/blog/microsoft-adaptive-streaming-plugin-for-osmf-update/).

## <a name="loading-the-plugin"></a>Läser in plugin-programmet
OSMF-plugin-program kan läsas in statiskt (vid kompilering) eller dynamiskt (vid körning). Smooth Streaming-plugin-programmet för OSMF-hämtning innehåller både dynamiska och statiska versioner.

* Statisk inläsning: för att läsa in en statisk biblioteks fil (SWC) krävs en statisk biblioteks fil. Statiska plugin-program läggs till som en referens till projekten och sammanfogas i den slutgiltiga utdatafilen vid kompileringen.
* Dynamisk inläsning: för att läsa in dynamiskt krävs en förkompilerad (SWF) fil. Dynamiska plugin-program läses in i körningen och ingår inte i projektets utdata. (Kompilerade utdata) Dynamiska plugin-program kan läsas in med HTTP-och fil protokoll.

Mer information om statisk och dynamisk inläsning finns på den officiella [OSMF-plugin-sidan](http://osmf.org/dev/osmf/OtherPDFs/osmf_plugin_dev_guide.pdf).

### <a name="ss-for-osmf-static-loading"></a>SS för OSMF statisk inläsning
Kodfragmentet nedan visar hur du läser in SS-plugin-programmet för OSMF statiskt och spelar en grundläggande video med hjälp av OSMF MediaFactory-klassen. Innan du inkluderar OSMF-koden SS måste du se till att projekt referensen innehåller det statiska plugin-programmet "MSAdaptiveStreamingPlugin-v 1.0.3-OSMF 2.0. SWC".

```csharp
package 
{

    import com.microsoft.azure.media.AdaptiveStreamingPluginInfo;

    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;



    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;


        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;

            initMediaPlayer();

        }

        private function initMediaPlayer():void
        {

            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);
            _mediaPlayerSprite.scaleMode = ScaleMode.NONE;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            //Adds the container to the stage
            addChild(_mediaPlayerSprite);

            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();

            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );

            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  

        }

        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;

            pluginResource = new PluginInfoResource(new AdaptiveStreamingPluginInfo( )); 
            _mediaFactory.loadPlugin( pluginResource ); 
        }

        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.
            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.
        loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")

        }

        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }


        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;

            state =  event.state;

            switch (state)
            {
                case MediaPlayerState.LOADING: 

                    // A new source is started to load.

                    break;

                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 

                    break;

                case MediaPlayerState.BUFFERING :

                    break;

                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }

        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }

        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.

            var resource:URLResource= new URLResource( sourceURL );

            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;

            // Add the media element
            _mediaPlayerSprite.media = element;
        }     

    }
}
```


### <a name="ss-for-osmf-dynamic-loading"></a>SS för dynamisk inläsning av OSMF
Kodfragmentet nedan visar hur du läser in SS-plugin-programmet för OSMF dynamiskt och spelar en grundläggande video med hjälp av OSMF MediaFactory-klassen. Innan du inkluderar koden SS för OSMF kopierar du det dynamiska plugin-programmet "MSAdaptiveStreamingPlugin-v 1.0.3-OSMF 2.0. swf" till projektmappen om du vill läsa in med hjälp av fil protokoll eller kopiera under en webb server för HTTP-belastning. Du behöver inte inkludera "MSAdaptiveStreamingPlugin-v 1.0.3-OSMF 2.0. SWC" i projekt referenserna.

```csharp
package 
{

    import flash.display.*;
    import org.osmf.media.*;
    import org.osmf.containers.MediaContainer;
    import org.osmf.events.MediaErrorEvent;
    import org.osmf.events.MediaFactoryEvent;
    import org.osmf.events.MediaPlayerStateChangeEvent;
    import org.osmf.layout.*;
    import flash.events.Event;
    import flash.system.Capabilities;


    //Sets the size of the SWF

    [SWF(width="1024", height="768", backgroundColor='#405050', frameRate="25")]
    public class TestPlayer extends Sprite
    {        
        public var _container:MediaContainer;
        public var _mediaFactory:DefaultMediaFactory;
        private var _mediaPlayerSprite:MediaPlayerSprite;


        public function TestPlayer( )
        {
            stage.quality = StageQuality.HIGH;
            initMediaPlayer();
        }

        private function initMediaPlayer():void
        {

            // Create the container (sprite) for managing display and layout
            _mediaPlayerSprite = new MediaPlayerSprite();    
            _mediaPlayerSprite.addEventListener(MediaErrorEvent.MEDIA_ERROR, onPlayerFailed);
            _mediaPlayerSprite.addEventListener(MediaPlayerStateChangeEvent.MEDIA_PLAYER_STATE_CHANGE, onPlayerStateChange);

            //Adds the container to the stage
            addChild(_mediaPlayerSprite);

            // Create a mediafactory instance
            _mediaFactory = new DefaultMediaFactory();

            // Add the listeners for PLUGIN_LOADING
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD,onPluginLoaded);
            _mediaFactory.addEventListener(MediaFactoryEvent.PLUGIN_LOAD_ERROR, onPluginLoadFailed );

            // Load the plugin class 
            loadAdaptiveStreamingPlugin( );  

        }

        private function loadAdaptiveStreamingPlugin( ):void
        {
            var pluginResource:MediaResourceBase;
            var adaptiveStreamingPluginUrl:String;

            // Your dynamic plugin web server needs to host a valid crossdomain.xml file to allow loading plugins.

            adaptiveStreamingPluginUrl = "http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf";
            pluginResource = new URLResource(adaptiveStreamingPluginUrl);
            _mediaFactory.loadPlugin( pluginResource ); 

        }

        private function onPluginLoaded( event:MediaFactoryEvent ):void
        {
            // The plugin is loaded successfully.

            // Your web server needs to host a valid crossdomain.xml file to allow plugin to download Smooth Streaming files.

    loadMediaSource("http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest")
        }

        private function onPluginLoadFailed( event:MediaFactoryEvent ):void
        {
            // The plugin is failed to load ...
        }


        private function onPlayerStateChange(event:MediaPlayerStateChangeEvent) : void
        {
            var state:String;

            state =  event.state;

            switch (state)
            {
                case MediaPlayerState.LOADING: 

                    // A new source is started to load.

                    break;

                case  MediaPlayerState.READY :   
                    // Add code to deal with Player Ready when it is hit the first load after a source is loaded. 

                    break;

                case MediaPlayerState.BUFFERING :

                    break;

                case  MediaPlayerState.PAUSED :
                    break;      
                // other states ...          
            }
        }

        private function onPlayerFailed(event:MediaErrorEvent) : void
        {
            // Media Player is failed .           
        }

        private function loadMediaSource(sourceURL : String):void 
        {
            // Take an URL of SmoothStreamingSource's manifest and add it to the page.

            var resource:URLResource= new URLResource( sourceURL );

            var element:MediaElement = _mediaFactory.createMediaElement( resource );
            _mediaPlayerSprite.scaleMode = ScaleMode.LETTERBOX;
            _mediaPlayerSprite.width = stage.stageWidth;
            _mediaPlayerSprite.height = stage.stageHeight;
            // Add the media element
            _mediaPlayerSprite.media = element;
        }     

    }
}
```

## <a name="strobe-media--playback-with-the-ss-odmf-dynamic-plugin"></a>Kasta medie uppspelning med SS-ODMF dynamiskt plugin-program
Smooth Streaming för dynamiskt plugin-program för OSMF är kompatibelt med [SMP (strobe Media uppspelning)](https://sourceforge.net/adobe/smp/home/Strobe%20Media%20Playback/). Du kan använda SS för OSMF-plugin-programmet för att lägga till Smooth Streaming innehålls uppspelning till SMP. Det gör du genom att kopiera "MSAdaptiveStreamingPlugin-v 1.0.3-OSMF 2.0. swf" under en webb server för HTTP-belastning med hjälp av följande steg:

1. Bläddra i [installations sidan för ljud uppspelnings mediet](http://osmf.org/dev/2.0gm/setup.html). 
2. Ange src till en Smooth Streaming källa (t. ex. http: \/ /devplatem.VO.msecnd.net/Sintel/Sintel_H264.ISM/manifest) 
3. Gör önskade konfigurations ändringar och klicka på Förhandsgranska och uppdatera.
   
   **Obs!** En giltig crossdomain.xml krävs för webb servern för innehållet. 
4. Kopiera och klistra in koden på en enkel HTML-sida med hjälp av din text redigerare, till exempel i följande exempel:

    ```html
    <html>
    <body>
    <object width="920" height="640"> 
    <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
    <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest &autoPlay=true"></param>
    <param name="allowFullScreen" value="true"></param>
    <param name="allowscriptaccess" value="always"></param>
    <param name="wmode" value="direct"></param>
    <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
        type="application/x-shockwave-flash" 
        allowscriptaccess="always" 
        allowfullscreen="true" 
        wmode="direct" 
        width="920" 
        height="640" 
        flashvars=" src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true">
    </embed>
    </object>
    </body>
    </html>
    ```


1. Lägg till Smooth Streaming OSMF-plugin-programmet i inbäddnings koden och spara.
   
    ```html
    <html>
    <object width="920" height="640"> 
    <param name="movie" value="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf"></param>
    <param name="flashvars" value="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10"></param>
    <param name="allowFullScreen" value="true"></param>
    <param name="allowscriptaccess" value="always"></param>
    <param name="wmode" value="direct"></param>
    <embed src="http://osmf.org/dev/2.0gm/StrobeMediaPlayback.swf" 
        type="application/x-shockwave-flash" 
        allowscriptaccess="always" 
        allowfullscreen="true" 
        wmode="direct" 
        width="920" 
        height="640" 
        flashvars="src=http://devplatem.vo.msecnd.net/Sintel/Sintel_H264.ism/manifest&autoPlay=true&plugin_AdaptiveStreamingPlugin=http://yourdomain/MSAdaptiveStreamingPlugin-v1.0.3-osmf2.0.swf&AdaptiveStreamingPlugin_retryLive=true&AdaptiveStreamingPlugin_retryInterval=10">
    </embed>
    </object>
    </html>
    ```

2. Spara HTML-sidan och publicera på en webb server. Bläddra till den publicerade webb sidan med din favorit webbläsare i Flash &reg; Player (Internet Explorer, Chrome, Firefox, så vidare).
3. Njut av Smooth Streaming innehåll i Adobe &reg; Flash &reg; Player.

Mer information om allmän OSMF utveckling finns på den officiella OSMF- [utvecklings sidan](http://osmf.org/resources.html).

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se även
[Microsoft adaptiv streaming-plugin för OSMF-uppdatering](https://azure.microsoft.com/blog/2014/10/27/microsoft-adaptive-streaming-plugin-for-osmf-update/) 

