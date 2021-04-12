---
title: Skriver plugin-program för Azure Media Player
description: Lär dig hur du skriver ett plugin-program med Azure Media Player med Java Script
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: how-to
ms.date: 04/05/2021
ms.custom: devx-track-js
ms.openlocfilehash: 7f14be29a33a885bfcef27fa9ef2775e72df16d9
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449808"
---
# <a name="writing-plugins-for-azure-media-player"></a>Skriver plugin-program för Azure Media Player #

Ett plugin-program är ett JavaScript-skript som skrivs för att utöka eller förbättra spelaren. Du kan skriva plugin-program som ändrar Azure Media Playerens utseende, dess funktioner eller även har IT-gränssnittet med andra tjänster. Du kan göra detta i två enkla steg:

## <a name="step-1"></a>Steg 1 ##

Skriv java script i en funktion så här:

```javascript

    (function () {
        amp.plugin('yourPluginName', function (options) {
        var myPlayer = this;
           myPlayer.addEventListener(amp.eventName.ready, function () {
        console.log("player is ready!");
            });
        });
    }).call(this);
```

Du kan skriva koden direkt i HTML-sidan i `<script>` taggar eller i en extern JavaScript-fil. Om du gör det senare, se till att du inkluderar JavaScript-filen på `<head>` HTML-sidan *efter* amp-skriptet.

Exempel:

```javascript
    <!--*****START OF Azure Media Player Scripts*****-->
    <script src="//amp.azure.net/libs/amp/latest/azuremediaplayer.min.js"></script>
    <link href="//amp.azure.net/libs/amp/latest/skins/amp-default/azuremediaplayer.min.css" rel="stylesheet">
    <!--*****END OF Azure Media Player Scripts*****-->
    <!--Add Plugins-->
    <script src="yourPluginName.js"></script>
```

## <a name="step-2"></a>Steg 2 ##
Initiera plugin-programmet med Java Script på något av följande sätt:

Metod 1:

```javascript
    var myOptions = {
        autoplay: true,
        controls: true,
        width: "640",
        height: "400",
        poster: "",
        plugins: {
            yourPluginName: {
                [your plugin options]: [example options]
           }
        }
    };     
    var myPlayer = amp([videotag id], myOptions);
```

Metod 2:

```javascript
    var video = amp([videotag id]);
    video.yourPluginName({[your plugins option]: [example option]});
```

Plugin-alternativ krävs inte, även om de bara tillåter utvecklare att använda ditt plugin-program för att konfigurera dess beteende utan att behöva ändra käll koden.

För inspiration och fler exempel på hur du skapar ett plugin-program kan du ta en titt på vårt [Galleri](azure-media-player-plugin-gallery.md)

>[!NOTE]
> Kod för plugin-programmet ändrar dynamiskt objekt i DOM under visnings förloppets spel upplevelse, men gör det aldrig permanenta ändringar i spelarens källkod. Det är här som det är praktiskt att förstå webbläsarens utvecklingsverktyg. Om du till exempel vill ändra utseendet på ett element i spelaren kan du hitta dess HTML-element med klass namnet och sedan lägga till eller ändra attribut därifrån. Här är en bra resurs vid [ändring av HTML-attribut.](http://www.w3schools.com/js/js_htmldom_html.asp)

### <a name="integrated-plugins"></a>Integrerade plugin-program ###

 Det finns för närvarande två plugin-bakade till AMP: [tids tips](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/timetip/example.html) och [snabb tangenter](http://sr-test.azurewebsites.net/Tests/Plugin%20Gallery/plugins/hotkeys/example.html). Dessa plugin-program utvecklades ursprungligen för att vara modulära plugin-program för spelaren, men ingår nu i käll koden för Player.

### <a name="plugin-gallery"></a>Plugin-Galleri ###

[Plugin-galleriet](https://aka.ms/ampplugins) har flera plugin-program som communityn redan har bidragit med för funktioner som tids linje markörer, zoomning, analyser och mer. Sidan ger till gång till plugin-programmet och instruktioner om hur du konfigurerar det och en demonstration som visar plugin-programmet i praktiken. Om du skapar ett coolbar-plugin-program som du tror ska ingå i vårt galleri kan du skicka det så att vi kan checka ut det.

## <a name="next-steps"></a>Nästa steg ##

- [Azure Media Player snabb start](azure-media-player-quickstart.md)
