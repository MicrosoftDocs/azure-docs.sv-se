---
title: Bädda in en video med MPEG-streck för anpassningsbar strömning i ett HTML5-program med DASH.js | Microsoft Docs
description: I det här avsnittet visas hur du bäddar in ett MPEG-STRECKs anpassningsbart strömmande video i ett HTML5-program med DASH.js.
author: IngridAtMicrosoft
manager: femila
editor: ''
services: media-services
documentationcenter: ''
ms.assetid: 5aa0e7b6-f5c3-4cc1-aa33-ed16ea4780c2
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 820c10a2f5bb43ff931027954b7ecf10c5b4c59f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013575"
---
# <a name="embedding-an-mpeg-dash-adaptive-streaming-video-in-an-html5-application-with-dashjs"></a>Bädda in MPEG-DASH-anpassad direktuppspelad video i ett HTML5-program med DASH.js

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)] 

## <a name="overview"></a>Översikt
MPEG-bindestreck är en ISO-standard för anpassningsbar strömning av video innehåll, vilket ger avsevärda fördelar för utvecklare som vill leverera högkvalitativ, anpassad video strömnings kvalitet. Med MPEG-streck justeras video strömmen automatiskt till en lägre definition när nätverket blir överbelastat. Detta minskar sannolikheten för att visnings programmet ser en "pausad" video medan spelaren laddar ned de kommande sekunderna för uppspelning (det vill säga att det minskar sannolikheten för buffring). När nätverket överbelastas minskar Videos pelaren i tur och tillbaka till en data ström med högre kvalitet. Den här möjligheten att anpassa bandbredden som krävs leder också till en snabbare start tid för video. Det innebär att de första sekunderna kan spelas upp i ett fast-till-nedladdning av lägre kvalitets segment och sedan gå upp till en högre kvalitet när tillräckligt med innehåll har buffrats.

Dash.js är en MPEG-STRECKad video spelare med öppen källkod skriven i Java Script. Målet är att tillhandahålla en robust, plattforms oberoende spelare som kan återanvändas fritt i program som kräver video uppspelning. Den innehåller uppspelning av MPEG-streck i alla webbläsare som stöder W3C-medie käll tillägg (MSE), idag som är Chrome, Microsoft Edge och IE11 (andra webbläsare har angett sin avsikt att stödja MSE). Mer information om DASH.js finns i GitHub dash.js-lagringsplatsen.

## <a name="creating-a-browser-based-streaming-video-player"></a>Skapa en webbläsarbaserad strömmande Videos pelare
Om du vill skapa en enkel webb sida som visar en Videos pelare med de förväntade kontrollerna som en uppspelning, paus, återspolning osv., måste du:

1. Skapa en HTML-sida
2. Lägg till video tag gen
3. Lägg till dash.js Player
4. Initiera spelaren
5. Lägg till CSS-format
6. Visa resultaten i en webbläsare som implementerar MSE

Det går bara att utföra en fåtal av rader med JavaScript-kod när spelaren initieras. Med dash.js är det mycket enkelt att bädda in video med MPEG-streck i dina webbläsarbaserade program.

## <a name="creating-the-html-page"></a>Skapa HTML-sidan
Det första steget är att skapa en standard-HTML-sida som innehåller **video** elementet, spara filen som basicPlayer.html, som i följande exempel visas:

```html
    <!DOCTYPE html>
    <html>
      <head><title>Adaptive Streaming in HTML5</title></head>
      <body>
        <h1>Adaptive Streaming with HTML5</h1>
        <video id="videoplayer" controls></video>
      </body>
    </html>
```

## <a name="adding-the-dashjs-player"></a>Lägga till DASH.js Player
Om du vill lägga till dash.js referens implementering i programmet måste du ta tag i dash.all.js-filen från den senaste versionen av dash.js-projektet. Detta bör sparas i Java Script-mappen i ditt program. Den här filen är en bekväm fil som hämtar all nödvändig dash.js kod till en enda fil. Om du har en titt på dash.js-lagringsplatsen kan du hitta de enskilda filerna, testa koden och mycket mer, men om du vill använda dash.js, är dash.all.js filen det du behöver.

Lägg till en skript tagg till head-avsnittet i basicPlayer.html om du vill lägga till dash.js Player i dina program:

```html
    <!-- DASH-AVC/265 reference implementation -->
    < script src="js/dash.all.js"></script>
```

Skapa sedan en funktion för att initiera spelaren när sidan läses in. Lägg till följande skript efter den rad där du läser in dash.all.js:

```html
    <script>
    // setup the video element and attach it to the Dash player
    function setupVideo() {
      var url = "http://wams.edgesuite.net/media/MPTExpressionData02/BigBuckBunny_1080p24_IYUV_2ch.ism/manifest(format=mpd-time-csf)";
      var context = new Dash.di.DashContext();
      var player = new MediaPlayer(context);
                      player.startup();
                      player.attachView(document.querySelector("#videoplayer"));
                      player.attachSource(url);
    }
    </script>
```

Den här funktionen skapar först en DashContext. Detta används för att konfigurera programmet för en specifik körnings miljö. Från en teknisk synpunkt definierar den de klasser som beroende inmatnings ramverket ska använda när programmet konstrueras. I de flesta fall använder du bindestreck. di. DashContext.

Sedan instansierar du den primära klassen i dash.js Framework, Media Player. Den här klassen innehåller de kärn metoder som krävs, till exempel Play och Pause, hanterar relationen med video elementet och hanterar även tolkningen av filen med medie presentations beskrivningen (MPD), som beskriver videon som ska spelas upp.

Funktionen Startup () i Media Player-klassen anropas för att säkerställa att spelaren är redo att spela upp video. Bland annat säkerställer funktionen att alla nödvändiga klasser (som definieras av kontexten) har lästs in. När spelaren är klar kan du koppla video-elementet till det med hjälp av funktionen attachView (). Start funktionen gör att Media Player kan mata in video strömmen i-elementet och även styra uppspelningen vid behov.

Skicka webb adressen till MPD-filen till Media Player så att den vet om videon som förväntas spelas upp. Funktionen setupVideo () som nyss skapats måste utföras när sidan har lästs in helt. Gör detta med hjälp av onLoad-händelsen i Body-elementet. Ändra ditt `<body>` element till:

```html
    <body onload="setupVideo()">
```

Ange Slutligen storleken på video elementet med CSS. I en anpassningsbar strömmande miljö är detta särskilt viktigt eftersom storleken på videon som spelas upp kan ändras när uppspelningen anpassas till att ändra nätverks förhållandena. I den här enkla demon tvingar du bara video elementet att vara 80% av det tillgängliga webbläsarfönstret genom att lägga till följande CSS i head-avsnittet på sidan:

```html
    <style>
    video {
      width: 80%;
      height: 80%;
    }
    </style>
```

## <a name="playing-a-video"></a>Spela upp en video
Om du vill spela upp en video, pekar du på webbläsaren i basicPlayback.html-filen och klickar på spela upp i Videos pelaren som visas.

## <a name="media-services-learning-paths"></a>Sökvägar för Media Services-utbildning
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Ge feedback
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

## <a name="see-also"></a>Se även

[GitHub dash.js-lagringsplats](https://github.com/Dash-Industry-Forum/dash.js) 

