---
title: Så här använder du Shaka Player med Azure Media Services
description: Den här artikeln förklarar hur du använder Shaka Player med Azure Media Services
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: devx-track-js
ms.openlocfilehash: 7b4bda5a439b1e0b35dc6d3af99e0273e4987568
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101091343"
---
# <a name="how-to-use-the-shaka-player-with-azure-media-services"></a>Så här använder du Shaka Player med Azure Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview"></a>Översikt

Shaka Player är ett JavaScript-bibliotek med öppen källkod för anpassningsbara medier. Den spelar in anpassade medie format (till exempel bindestreck och HLS) i en webbläsare, utan att använda plugin-program eller Flash. I stället använder Shaka Player de öppna webb standard tilläggen och krypterade medie tillägg.

Vi rekommenderar att du använder [Mux.js](https://github.com/videojs/mux.js/) som, utan den, stöder SHAKA Player HLS cmaf-format, men inte HLS TS.

Den officiella dokumentationen finns i dokumentationen för [Shaka Player](https://shaka-player-demo.appspot.com/docs/api/tutorial-welcome.html).

## <a name="sample-code"></a>Exempelkod

Exempel kod för den här artikeln finns på [Azure-samples/Media-Services-3rdParty-Player-samples](https://github.com/Azure-Samples/media-services-3rdparty-player-samples).

## <a name="implementing-the-player"></a>Implementera spelaren

Följ dessa anvisningar om du behöver implementera din egen instans av spelaren:

1. Skapa en `index.html` fil där du är värd för spelaren. Lägg till följande rader med kod (du kan ersätta versionerna för senare om tillämpligt):

    ```html
    <html>
      <head>
        <script src="//cdn.jsdelivr.net/npm/shaka-player@3.0.1/dist/shaka-player.compiled.js"></script>
        <script src="//cdn.jsdelivr.net/npm/mux.js@5.6.3/dist/mux.js"></script>
        <script type="module" src="index.js"></script>
      </head>
      <body>
        <video id="video" controls></video>
      </body>
    </html>
    ```

1. Lägg till en JavaScript-fil med följande kod:

    ```javascript
    // myScript.js
    shaka.polyfill.installAll();

    var video = document.getElementById('video');
    var player = new shaka.Player(video);
    window.player = player;

    var manifestUrl = 'https://amsplayeraccount-usw22.streaming.media.azure.net/00000000-0000-0000-0000-000000000000/sample-vod.ism/manifest(format=m3u8-aapl)';
    player.load(manifestUrl);
    ```

1. Ersätt `manifestUrl` med HLS eller streck-URL: en från din till gångs strömmande plats, som du hittar på sidan streaming Locator i Azure Portal.
    ![URL: er för strömmande adresser](media/how-to-shaka-player/streaming-urls.png)

1. Kör en server (till exempel med `npm http-server` ) och din spelare ska fungera...

## <a name="set-up-captions"></a>Konfigurera under texter

### <a name="set-up-vod-captions"></a>Konfigurera VOD-beskrivningar

Kör följande kodrader och Ersätt `captionUrl` med. VTT-katalogen (VTT-filen måste finnas på samma värd för att undvika CORS-fel), `lang` med den två brev koden för språk och `type` med antingen `caption` eller `subtitle` :

```javascript
player.configure('streaming.alwaysStreamText', true)
player.load(manifestUrl).then(function(){
        player.addTextTrack(captionUrl, lang, type, 'text/vtt');
        var tracks = player.getTextTracks();
        player.selectTextTrack(tracks[0]);
});
```

### <a name="set-up-live-stream-captions"></a>Konfigurera direkt uppspelnings under texter

Aktivera under texter i Real strömmen konfigureras genom att lägga till följande kodrad:

```javascript
player.setTextTrackVisibility(true)
```

## <a name="set-up-token-authentication"></a>Konfigurera token-autentisering

Kör följande rader med kod och Ersätt `token` med din token-sträng:

```javascript
player.getNetworkingEngine().registerRequestFilter(function (type, request) {
  if (type === shaka.net.NetworkingEngine.RequestType.LICENSE) {
    request.headers['Authorization'] = 'Bearer ' + token;
  }
});
```

## <a name="set-up-aes-128-encryption"></a>Konfigurera AES-128-kryptering

Shaka Player stöder för närvarande inte AES-128-kryptering.

En länk till ett GitHub- [problem](https://github.com/google/shaka-player/issues/850) för att följa den här funktionens status.

## <a name="set-up-drm-protection"></a>Konfigurera DRM-skydd

Shaka Player använder krypterade medie tillägg (EME), vilket kräver att en säker URL används. För att testa alla DRM-skyddade innehåll är det nödvändigt att använda https. Om platsen använder HTTPS måste manifestet och varje segment också använda https. Detta beror på blandade innehålls krav.

Prioritetsordningen för Shaka hantering av URL: erna för dess licens servrar:

1. ClearKey config, som används för fel sökning, ska åsidosätta allt annat. (Programmet kan fortfarande ange en ClearKey licens Server.)
2. Program-konfigurerade servrar, om sådana finns, ska åsidosätta allt från manifestet.
3. Manifest-tillhandahållna licens servrar används endast om inget annat anges.

För att ange licens serverns URL för Widevine eller PlayReady kan vi använda följande kod:

```javascript
player.configure({
  drm: {
    servers: {
      "com.widevine.alpha": "YOUR WIDEVINE LICENSE URL",
      "com.microsoft.playready": "YOUR PLAYREADY LICENSE URL"
    }
  }
});

```

Allt FairPlay-innehåll kräver att du anger ett Server certifikat. Den anges i Player-konfigurationen:

```javascript
const req = await fetch("YOUR FAIRPLAY CERTIFICATE URL");
const cert = await req.arrayBuffer();
player.configure('drm.advanced.com\\.apple\\.fps\\.1_0.serverCertificate', new Uint8Array(cert));
```

Mer information finns i [dokumentationen för Shaka Player DRM Protection](https://shaka-player-demo.appspot.com/docs/api/tutorial-drm-config.html).

## <a name="next-steps"></a>Nästa steg

* [Använd Azure Media Player](../azure-media-player/azure-media-player-overview.md)
* [Snabb start: Kryptera innehåll](encrypt-content-quickstart.md)
