---
title: Azure Media Player URL-Rewriter
description: Azure Media Player skriver om en viss URL från Azure Media Services för att tillhandahålla strömmar för smidig, streck, HLS v3 och HLS v4.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: b037eec13fda0b5ec16a4f2f53ad2a64fb5f8da1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "91275565"
---
# <a name="url-rewriter"></a>URL-omskrivare #

Som standard kommer Azure Media Player att skriva om en viss URL från Azure Media Services för att tillhandahålla strömmar för smidig, streck, HLS v3 och HLS v4. Om källan till exempel anges som följer, ser Azure Media Player till att den försöker spela upp alla ovanstående protokoll:

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" />
    </video>
```

Men om du inte vill använda URL-Rewriter kan du göra det genom att lägga till `disableUrlRewriter` egenskapen i parametern. Det innebär att all information som skickas till källorna skickas direkt till spelaren utan ändringar.  Här är ett exempel på hur du lägger till två källor i spelaren, på streck och en smidig strömning.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest(format=mpd-time-csf)" type="application/dash+xml" data-setup='{"disableUrlRewriter": true}'/>
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" data-setup='{"disableUrlRewriter": true}'/>
    </video>
```

eller

```javascript
    myPlayer.src([
        { src: "//example/path/to/myVideo.ism/manifest(format=mpd-time-csf)", type: "application/dash+xml", disableUrlRewriter: true },
        { src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml", disableUrlRewriter: true }
    ]);
```

Om du vill kan du även ange de angivna strömnings format som du vill Azure Media Player skriva om till med hjälp av `streamingFormats` parametern. Alternativen inkluderar `DASH` ,,, `SMOOTH` `HLSv3` `HLSv4` , `HLS` . Skillnaden mellan HLS och HLSv3 & V4 är att HLS-formatet stöder uppspelning av FairPlay-innehåll. v3 och v4 stöder inte FairPlay. Detta är användbart om du inte har en leverans princip för ett visst protokoll tillgängligt.  Här är ett exempel på när ett BINDESTRECKs protokoll inte är aktiverat med din till gång.

```html
    <video id="vid1" class="azuremediaplayer amp-default-skin">
        <source src="//example/path/to/myVideo.ism/manifest" type="application/vnd.ms-sstr+xml" data-setup='{"streamingFormats": ["SMOOTH", "HLS","HLS-V3", "HLS-V4"] }'/>
    </video>
```

eller

```javascript
    myPlayer.src([
        { src: "//example/path/to/myVideo.ism/manifest", type: "application/vnd.ms-sstr+xml", streamingFormats: ["SMOOTH", "HLS","HLS-V3", "HLS-V4"]},
    ]);
```

Ovanstående två kan användas tillsammans med varandra för flera omständigheter baserat på din specifika till gång.

> [!NOTE]
> Widevine Protection-information finns bara kvar i streck protokollet.

## <a name="next-steps"></a>Nästa steg ##

- [Azure Media Player snabb start](azure-media-player-quickstart.md)