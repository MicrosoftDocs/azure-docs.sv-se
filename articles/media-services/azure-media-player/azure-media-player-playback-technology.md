---
title: Azure Media Player uppspelnings teknik
description: Lär dig mer om uppspelnings tekniken som används för att spela upp videon eller ljudet.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: overview
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: 24a38fde3d1b6bbe1efef9241458da846c186112
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97916345"
---
# <a name="playback-technology-tech"></a>Uppspelnings teknik ("Tech") #

Uppspelnings teknik avser den speciella webbläsare eller plugin-teknik som används för att spela upp videon eller ljudet.

- **azureHtml5JS**: använder MSE-och EME-standarder tillsammans med video elementet för plugin-mindre baserat uppspelning av streck innehåll med stöd för AES-128-bitars krypterat innehåll eller DRM-vanligt krypterat innehåll (via PlayReady och Widevine när webbläsaren stöder det) från Azure Media Services
- **blixt**: använder Flash Player-teknik för att spela upp smidigt innehåll med stöd för AES-128 bitars dekryptering från Azure Media Services – kräver Flash-version av 11,4 eller högre
- **html5FairPlayHLS**: använder Safari som är särskilt i webbläsarbaserad uppspelnings teknik via HLS med video elementet. Den här techen kräver att FairPlay-skyddat innehåll spelas upp från Azure Media Services och har lagts till i techOrder från och med 10/19/16
- **Silverlight**: använder Silverlight-teknik för att spela upp smidigt innehåll med stöd för PlayReady-skyddat innehåll från Azure Media Services.
- **HTML5**: använder i webbläsarbaserad uppspelnings teknik med video elementet.  När du använder en Apple iOS-eller Android-enhet, tillåter den här Tech uppspelning av HLS-strömmar med vissa grundläggande stöd för AES-128-bitars kryptering eller DRM-innehåll (via FairPlay när webbläsaren stöder det).

## <a name="tech-order"></a>Teknisk ordning ##

För att säkerställa att din till gång kan spelas upp på en mängd olika enheter rekommenderas följande tekniska ordning och är standard om: `techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS","silverlightSS", "html5"]` och kan anges direkt på `<video>` eller program mässigt i alternativen:

`<video data-setup='{"techOrder": ["azureHtml5JS", "flashSS", "html5FairPlayHLS, "silverlightSS", "html5"]}`

eller

```javascript
    amp("vid1", {
          techOrder: ["azureHtml5JS", "flashSS", "html5FairPlayHLS, "silverlightSS", "html5"]
    });
```

## <a name="compatibility-matrix"></a>Kompatibilitet mat ris ##

Med den rekommenderade tekniska ordningen med strömmande innehåll från Azure Media Services förväntas följande matris för kompatibilitets uppspelning

| Webbläsare        | Operativsystem                                                       | Förväntad teknik (klar)  | Förväntad teknik (AES)  | Förväntad teknik (DRM)          |
|----------------|----------------------------------------------------------|------------------------|----------------------|------------------------------|
| Kant 11      | Windows 10, Windows 8,1, Windows Phone 10<sup>1</sup>               | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (PlayReady)     |
| Internet Explorer 11          | Windows 7, Windows Vista<sup>1</sup>                     | blixt                | blixt              | Silverlight (PlayReady)    |
| Internet Explorer 11          | Windows Phone 8.1                                        | azureHtml5JS           | azureHtml5JS         | stöds inte                |
| Edge           | Xbox One<sup>1</sup> (nov 2015-uppdatering)                   | azureHtml5JS           | azureHtml5JS         | stöds inte                |
| Chrome 37 +     | Windows 10, Windows 8,1, macOS X Yosemite<sup>1</sup>   | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Firefox 47 +    | Windows 10, Windows 8,1, macOS X Yosemite +<sup>1</sup>  | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Firefox 42-46  | Windows 10, Windows 8,1, macOS X Yosemite +<sup>1</sup>  | azureHtml5JS           | azureHtml5JS         | Silverlight (PlayReady)    |
| Firefox 35-41  | Windows 10, Windows 8,1                                  | blixt                | blixt              | Silverlight (PlayReady)    |
| Safari         | iOS 6+                                                   | HTML5                  | HTML5 (ingen token) 3    | stöds inte                |
| Safari 8 +      | OS X Yosemite +                                           | azureHtml5JS           | azureHtml5JS         | html5FairPlayHLS (FairPlay)  |
| Safari 6       | OS X Mountain Lion<sup>1</sup>                           | blixt                | blixt              | Silverlight (PlayReady)    |
| Chrome 37 +     | Android-4.4.4 +<sup>2</sup>                               | azureHtml5JS           | azureHtml5JS         | azureHtml5JS (Widevine)      |
| Chrome 37 +     | Android 4,02                                             | HTML5                  | HTML5 (ingen token)<sup>3</sup>    | stöds inte                |
| Firefox 42 +    | Android 5.0 +<sup>2</sup>                                 | azureHtml5JS           | azureHtml5JS         | stöds inte                |
| IE 8, IE 9, IE 10  | Windows                                                  | stöds inte          | stöds inte        | stöds inte                |

<sup>1</sup> konfiguration stöds inte eller testas. listad som referens för slut för ande.

<sup>2</sup> lyckad uppspelning på Android-enheter kräver en kombination av enhets funktioner, grafik stöd, codec-åter GIVNING, OS-support med mera. Eftersom Android är en plattform med öppen källkod som gör det möjligt för telefon tillverkare att ändra vanilj Android OS som tillhandahålls av Google, detta orsakar fragmentering i Android-utrymmet, och vissa enheter kanske inte stöds på grund av brist på funktioner. Vissa Android-enheter har inte heller stöd för alla codecenheter.  

<sup>3</sup> i de fall där det inte finns något stöd för token kan du använda en proxy för att lägga till den här funktionen. Ta en titt på den här [bloggen](https://azure.microsoft.com/blog/2015/03/06/how-to-make-token-authorized-aes-encrypted-hls-stream-working-in-safari/) om du vill veta mer om den här lösningen.

> [!NOTE]
> Om den förväntade Tech som valts kräver att ett plugin-program installeras, t. ex. Flash, och inte är installerat på användarens dator, fortsätter AMP att kontrol lera funktionerna i nästa Tech, tillsammans med käll typer och skydds information, i Tech-listan. Om du till exempel försöker visa en oskyddad data ström i Safari 8 på OS X Yosemite, och både Flash och Silverlight inte är installerade, väljer AMP den inbyggda HTML5-Tech för uppspelning.<br/><br/>Nya webb läsar tekniker håller på att bli nya dagligen och kan påverka denna matris.

## <a name="next-steps"></a>Nästa steg ##

- [Azure Media Player snabb start](azure-media-player-quickstart.md)