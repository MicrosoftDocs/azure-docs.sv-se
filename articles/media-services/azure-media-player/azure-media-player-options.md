---
title: Azure Media Player alternativ
description: Den Azure Media Player inbäddnings koden är helt enkelt en HTML5-videotagg, så för många alternativ kan du ange alternativen med hjälp av taggen standardattribut.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/20/2020
ms.custom: devx-track-js
ms.openlocfilehash: 392ecff2ab120e713a07d130493a4a8339dae5ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92366878"
---
# <a name="options"></a>Alternativ #

## <a name="setting-options"></a>Inställningsalternativ ##

Den Azure Media Player inbäddnings koden är helt enkelt en HTML5-videotagg, så för många alternativ kan du ange alternativen med hjälp av taggen standardattribut.

`<video controls autoplay ...>`

Du kan också använda attributet data-setup för att tillhandahålla alternativ i [JSON](http://json.org/example.html) -formatet. Detta är också hur du anger alternativ som inte är standard för video tag gen.

`<video data-setup='{ "controls": true, "autoplay": false }'...>`

Slutligen, om du inte använder attributet data-setup för att utlösa installations programmet för spelaren, kan du skicka ett objekt med Player-alternativen som det andra argumentet i installations programmet för JavaScript-funktionen.

`amp("vid1", { "controls": true, "autoplay": false });`

> [!NOTE]
> Alternativen i konstruktorn anges bara för den första initieringen innan källan har angetts.  Om du vill ändra alternativen på samma initierade Azure Media Player-element måste du uppdatera alternativen innan du ändrar källan. Du kan uppdatera alternativen i Java Script med hjälp av `myPlayer.options({/*updated options*/});` . Observera att endast ändrade alternativ kommer att påverkas, men alla andra alternativ som har angetts sparas.

## <a name="individual-options"></a>Enskilda alternativ ##

> [!NOTE]
>Video tag gen attribut kan bara vara true eller false (Boolean), du inkluderar bara attributet (inte lika med-tecken) för att aktivera det, eller så utesluter du det genom att inaktivera det. Om du t. ex. vill aktivera kontroller: fel `<video controls="true" ...>` behörighet som `<video controls ...>` användare kan köra i försöker ange värdena till falskt med falskt som värde (t. ex. Controls = "false") som faktiskt stämmer överens med värdet true, eftersom attributet fortfarande ingår.

### <a name="controls"></a>kontroller ###

Alternativet kontroller anger om spelaren har kontroller som användaren kan interagera med. Utan styr det enda sättet att starta videouppspelningen med attributet Autoplay eller via API.

`<video controls ...>` eller `{ "controls": true }`

### <a name="autoplay"></a>autoplay ###

Om spela upp automatiskt är sant börjar videon spelas upp så snart sidan har lästs in (utan någon interaktion från användaren).

> [!NOTE]
> Det här alternativet stöds inte av mobila enheter som Windows Phone, Apple iOS och Android. Mobila enheter blockerar funktionen för automatisk uppspelning för att förhindra att konsuments månatliga data planer används (ofta dyra). Du måste använda touch/klickning för att starta videon i det här fallet.

`<video autoplay ...>`eller `{ "autoplay": true }`

### <a name="poster"></a>affisch ###
Attributet affisch anger den bild som visas innan videon börjar spelas upp. Detta är ofta en bild ruta i videon eller en anpassad rubrik skärm. Så fort användaren klickar på spela upp går bilden bort.

`<video poster="myPoster.jpg" ...>` eller `{ "poster": "myPoster.jpg" }`

### <a name="width"></a>bredd ###

Attributet width anger visnings bredden för videon.

`<video width="640" ...>` eller `{ "width": 640 }`

### <a name="height"></a>mankhöjd ###

Attributet height anger visnings höjden för videon.

`<video height="480" ...>` eller `{ "height": 480 }`

### <a name="plugins"></a>plugin ###

Plugin-programmet JSON avgör vilka plugin-program som läses in med den instansen av AMP, så att du kan konfigurera alternativ som plugin-programmet kan ha

   `<video... data-setup='{plugins: { "contentTitle": {"name": "Azure Medi Services Overview"}}}'...>`

Mer information om plugin-utveckling och användning finns i [skriva plugin](azure-media-player-writing-plugins.md) -program

### <a name="other-options"></a>andra alternativ ###

Andra alternativ kan anges för `<video>` taggen med hjälp av `data-setup` parametern som tar en JSON.
`<video ... data-setup='{"nativeControlsForTouch": false}'>`

#### <a name="nativecontrolsfortouch"></a>nativeControlsForTouch ####

Detta är explicit inställt på false. Genom att ställa in på false tillåter det att Azure Media Player-skalet renderas på samma olika plattformar.  Till skillnad från namnet kommer touch fortfarande att aktive ras.

### <a name="fluid"></a>ger ###

Genom att ställa in det här alternativet på True video-element får du full bredd på den överordnade behållaren och höjden justeras så att den passar en video med standard förhållandet 16:9 proportioner.

`<video ... data-setup='{"fluid": true}'>`

`fluid` alternativet åsidosätter explicita `width` och `height` Inställningar. Det här alternativet är endast tillgängligt i Azure Media Player version `2.0.0` och senare.

### <a name="playbackspeed"></a>playbackSpeed ###

`playbackSpeed` alternativ styr playbackSpeed-styrning och uppsättning av uppspelnings hastighets inställningar som är tillgängliga för användaren. `playbackSpeed` tar ett objekt. För att aktivera uppspelnings hastigheten i kontroll fältet `enabled` måste egenskapen för objektet anges till sant. Ett exempel på hur du aktiverar uppspelnings hastigheten i markeringen:

`<video ... data-setup='{"playbackSpeed": {"enabled": true}}'>`


Andra egenskaper för `playbackSpeed` inställningen anges av [PlaybackSpeedOptions](/javascript/api/azuremediaplayer/amp.player.playbackspeedoptions) -objektet.

Exempel på Inställningar för uppspelnings hastighet i Java Script:

```javascript
    var myPlayer = amp('vid1', {
        fluid: true,
        playbackSpeed: {
            enabled: true,
            initialSpeed: 1.0,
            speedLevels: [
                { name: "x4.0", value: 4.0 },
                { name: "x3.0", value: 3.0 },
                { name: "x2.0", value: 2.0 },
                { name: "x1.75", value: 1.75 },
                { name: "x1.5", value: 1.5 },
                { name: "x1.25", value: 1.25 },
                { name: "normal", value: 1.0 },
                { name: "x0.75", value: 0.75 },
                { name: "x0.5", value: 0.5 },
            ]
        }
    });
```

Det här alternativet är endast tillgängligt i Azure Media Player version 2.0.0 och senare.

### <a name="staledatatimelimitinsec"></a>staleDataTimeLimitInSec ###

`staleDataTimeLimitInSec`Alternativet är en optimering som gör att du kan konfigurera hur många sekunder av inaktuella data du vill behålla i mediaSource-buffertarna. Alternativet är inaktiverat som standard.

### <a name="cea708captionssettings"></a>cea708CaptionsSettings ###

Inställningen aktive rad till sant gör att du kan visa CEA-textning i dina Live-strömmar och Live-Arkiv. Attributet etikett krävs inte, om inte inspelade Media Player kommer att återgå till en standard etikett.

```javascript
     cea708CaptionsSettings: {
                enabled: true,
                srclang: 'en',
                label: 'Live CC'
            }
```

Det här alternativet är endast tillgängligt i Azure Media Player version 2.1.1 och senare.

## <a name="next-steps"></a>Nästa steg ##

- [Azure Media Player snabb start](azure-media-player-quickstart.md)
