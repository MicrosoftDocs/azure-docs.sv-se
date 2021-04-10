---
title: Azure Media Player snabb start
description: Lär dig de grundläggande stegen för att ställa in Azure Media Player.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: quickstart
ms.date: 04/20/2020
ms.openlocfilehash: 0d0fa88fdf182ae2214da40ee3fe8b20ced025e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104956068"
---
# <a name="azure-media-player-quickstart"></a>Snabbstart för Azure Media Player
Azure Media Player är enkelt att konfigurera. Det tar bara några minuter att få en grundläggande uppspelning av medie innehåll från ditt Azure Media Services-konto. I det här avsnittet visas de grundläggande stegen utan mer detaljerad information. I avsnitten som följer får du information om hur du konfigurerar och konfigurerar Azure Media Player.  Lägg bara till följande inkluderingar i dokumentets `<head>`:

```html
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <script src= "//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
```

> [!IMPORTANT]
> Du bör **inte** använda `latest` versionen i produktion eftersom det kan komma att ändras på begäran. Ersätt `latest` med en version av Azure Media Player, till exempel Ersätt `latest` med `1.0.0` . Azure Media Player versioner kan frågas [härifrån.](azure-media-player-changelog.md)

## <a name="use-the-video-element"></a>Använd video elementet

Sedan använder `<video>` du bara elementet som vanligt, men med ett ytterligare `data-setup` attribut som innehåller alternativ. De här alternativen kan innehålla alla Azure Media Services alternativ i ett giltigt JSON-objekt.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin" autoplay controls width="640" height="400" poster="poster.jpg" data-setup='{"nativeControlsForTouch": false}'>
        <source src="http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest" type="application/vnd.ms-sstr+xml" />
        <p class="amp-no-js">
            To view this video please enable JavaScript, and consider upgrading to a web browser that supports HTML5 video
        </p>
    </video>
```

Om du inte vill använda automatisk installation kan du utelämna `data-setup` attributet och initiera ett video element manuellt.

```javascript
    var myPlayer = amp('vid1', { /* Options */
            "nativeControlsForTouch": false,
            autoplay: false,
            controls: true,
            width: "640",
            height: "400",
            poster: ""
        }, function() {
              console.log('Good to go!');
               // add an event listener
              this.addEventListener('ended', function() {
                console.log('Finished!');
            });
          }
    );
    myPlayer.src([{
        src: "http://amssamples.streaming.mediaservices.windows.net/91492735-c523-432b-ba01-faba6c2206a2/AzureMediaServicesPromo.ism/manifest",
        type: "application/vnd.ms-sstr+xml"
    }]);
```

## <a name="next-steps"></a>Nästa steg ##

- [Azure Media Player fullständig installation](./azure-media-player-full-setup.md)
