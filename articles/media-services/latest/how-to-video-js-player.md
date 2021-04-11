---
title: Använd Video.js Player med Azure Media Services
description: Den här artikeln förklarar hur du använder HTML video-objektet och Java Script med Azure Media Services
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
ms.openlocfilehash: 406390b34e1450e532205dc903df1933749d210f
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106061969"
---
# <a name="how-to-use-the-videojs-player-with-azure-media-services"></a>Använda Video.js Player med Azure Media Services

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

## <a name="overview"></a>Översikt

Video.js är en webb Videos pelare som skapats för en HTML5-värld. Den spelar in anpassade medie format (till exempel bindestreck och HLS) i en webbläsare, utan att använda plugin-program eller Flash. I stället använder Video.js tilläggen Open Web Standards MediaSource-tillägg och krypterade medie tillägg. Dessutom stöder den videouppspelning på Station ära datorer och mobila enheter.

Den officiella dokumentationen finns på [https://docs.videojs.com/](https://docs.videojs.com/) .

## <a name="sample-code"></a>Exempelkod
Exempel kod för den här artikeln finns på [Azure-samples/Media-Services-3rdParty-Player-samples](https://github.com/Azure-Samples/media-services-3rdparty-player-samples).

## <a name="implement-the-player"></a>Implementera spelaren

1. Skapa en `index.html` fil där du är värd för spelaren. Lägg till följande rader med kod (du kan ersätta versionerna för senare om tillämpligt):

    ```html
    <html>
      <head>
        <link href="https://vjs.zencdn.net/7.8.2/video-js.css" rel="stylesheet" />
      </head>
      <body>
        <video id="video" class="video-js vjs-default-skin vjs-16-9" controls data-setup="{}">
        </video>

        <script src="https://vjs.zencdn.net/7.8.2/video.js"></script>
        <script src="https://cdn.jsdelivr.net/npm/videojs-contrib-eme@3.7.0/dist/videojs-contrib-eme.min.js"></script>
        <script type="module" src="index.js"></script>
      </body>
    <html>
    ```

2. Lägg till en `index.js` fil med följande kod:

    ```javascript
    var videoJS = videojs("video");
    videoJS.src({
      src: "manifestUrl",
      type: "protocolType",
    });
    ```

3. Ersätt `manifestUrl` med HLS eller streck-URL: en från den strömmande lokaliseraren för din till gång som finns på sidan streaming Locator i Azure Portal.
    ![URL: er för strömmande adresser](media/how-to-shaka-player/streaming-urls.png)

4. Ersätt `protocolType` med följande alternativ:

    - "Application/x-mpegURL" för HLS-protokoll
    - "program/bindestreck + XML" för BINDESTRECKs protokoll

### <a name="set-up-captions"></a>Konfigurera under texter

Kör `addRemoteTextTrack` metoden och Ersätt:

- `subtitleKind` med antingen `"captions"` , `"subtitles"` , `"descriptions"` eller `"metadata"`  
- `caption` med fil Sök vägen. VTT (VTT-filen måste finnas på samma värd för att undvika CORS-fel)
- `subtitleLang` med BCP 47-koden för språk, till exempel `"eng"` för engelska eller `"es"` spanska
- `subtitleLabel` med det önskade visnings namnet för rubrik

```javascript
videojs.players.video.addRemoteTextTrack({
  kind: subtitleKind,
  src: caption,
  srclang: subtitleLang,
  label: subtitleLabel
});
```

### <a name="set-up-token-authentication"></a>Konfigurera token-autentisering

Token måste anges i fältet Authorization i rubriken för begäran. För att undvika problem med CORS måste denna token endast anges i dessa begär Anden med `'keydeliver'` i URL: en. Följande kod rader ska fungera:

```javascript
setupTokenForDecrypt (options) {
  if (options.uri.includes('keydeliver')) {
    options.headers = options.headers || {}
    options.headers.Authorization = 'Bearer=' + this.getInputToken()
  }

  return options
}
```

Därefter måste funktionen ovan vara kopplad till `videojs.Hls.xhr.beforeRequest` händelsen.

```javascript
videojs.Hls.xhr.beforeRequest = setupTokenForDecrypt;
```

### <a name="set-up-aes-128-encryption"></a>Konfigurera AES-128-kryptering

Video.js stöder kryptering med AES-128 utan ytterligare konfiguration. 

> [!NOTE] 
> Det finns för närvarande ett [problem](https://github.com/videojs/video.js/issues/6717) med kryptering och HLS/streck-cmaf innehåll, som inte kan spelas upp.

### <a name="set-up-drm-protection"></a>Konfigurera DRM-skydd

För att stödja DRM-skydd måste du lägga till tillägget [VideoJS-contrib-EME](https://github.com/videojs/videojs-contrib-eme) statsstöd. En CDN-version fungerar också.

1. I `index.js` filen som beskrivs ovan måste du INITIERA EME-tillägget genom att lägga till `videoJS.eme();` *innan* du lägger till video källan:

   ```javascript
    videoJS.eme();
   ```

2. Nu kan du definiera URL: er för DRM-tjänsterna och URL: erna för motsvarande licenser enligt följande:

   ```javascript
   videoJS.src({
       keySystems: {
           "com.microsoft.playready": "YOUR PLAYREADY LICENSE URL",
           "com.widevine.alpha": "YOUR WIDEVINE LICENSE URL",
           "com.apple.fps.1_0": {
            certificateUri: "YOUR FAIRPLAY CERTIFICATE URL",
            licenseUri: "YOUR FAIRPLAY LICENSE URL"
           }
         }
       })

   ```

#### <a name="acquiring-the-license-url"></a>Hämtar licens-URL

För att erhålla licens-URL: en kan du:

- Kontakta konfigurationen av DRM-providern
- eller, om du använder exemplet, se det dokument som `output.json` genererades när du körde *setup-vod.ps1* PowerShell-skriptet för VODs, eller *start-live.ps1* skriptet för Live-strömmar. Du hittar också barnen i den här filen.

#### <a name="using-tokenized-drm"></a>Använda token DRM

För att kunna stödja token DRM-skydd måste du lägga till följande rad i `src` egenskapen för Player:

```javascript
videoJS.src({
src: ...,
emeHeaders: {'Authorization': "Bearer=" + "YOUR TOKEN"},
keySystems: {...
```

## <a name="next-steps"></a>Nästa steg

- [Använd Azure Media Player](../azure-media-player/azure-media-player-overview.md)  
- [Snabb start: Kryptera innehåll](drm-encrypt-content-how-to.md)
