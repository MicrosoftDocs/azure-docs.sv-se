---
title: Azure Media Player API-metoder
description: 'Med Azure Media Player-API: et kan du interagera med videon via Java Script, oavsett om webbläsaren spelar videon via HTML5-video, Flash, Silverlight eller någon annan uppspelnings tekniker som stöds.'
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: reference
ms.date: 04/05/2021
ms.openlocfilehash: 3b199223c7e77f31cd4bf6e99d6a170f6868848a
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448703"
---
# <a name="api"></a>API #

Med Azure Media Player-API: et kan du interagera med videon via Java Script, oavsett om webbläsaren spelar videon via HTML5-video, Flash, Silverlight eller någon annan uppspelnings tekniker som stöds.

## <a name="referencing-the-player"></a>Referera till spelaren ##

Om du vill använda API-funktioner måste du ha åtkomst till Player-objektet. Som tur är är det enkelt att hämta. Du behöver bara se till att video tag gen har ett ID. Exemplet inbäddnings kod har ett ID för `vid1` . Om du har flera videor på en sida ser du till att alla video etiketter har ett unikt ID.

`var myPlayer = amp('vid1');`

> [!NOTE]
> Om spelaren inte har initierats än via attributet data konfiguration eller någon annan metod initieras även spelaren.

## <a name="wait-until-the-player-is-ready"></a>Vänta tills spelaren är klar ##

Hur lång tid det tar Azure Media Player att konfigurera videon och API: et beror på vilken uppspelnings teknik som används. HTML5 är ofta mycket snabbare att läsa in än Flash eller Silverlight. Av den anledningen ska spelarens färdiga funktion användas för att utlösa all kod som kräver spelarens API.

```javacript
    amp("vid_1").ready(function(){
      var myPlayer = this;

      // EXAMPLE: Start playing the video.
      myPlayer.play();
    });
```

ELLER

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
```

## <a name="api-methods"></a>API-metoder ##

Nu när du har åtkomst till en färdig spelare kan du styra videon, hämta värden eller svara på video händelser. De Azure Media Player API-funktions namnen försöker följa [HTML5-medie-API: et](http://www.whatwg.org/specs/web-apps/current-work/multipage/the-video-element.html). Den största skillnaden är att get-/set-funktionerna används för video egenskaper.

```javacript
    // setting a property on a bare HTML5 video element
    myVideoElement.currentTime = 120;

    // setting a property with Azure Media Player
    myPlayer.currentTime(120);
```

## <a name="registering-for-events"></a>Registrera för händelser ##
Händelser bör registreras direkt efter att spelaren har initierats för första gången för att säkerställa att alla händelser rapporteras korrekt till programmet och bör utföras utanför den färdiga händelsen.

```javacript
    var myPlayer = amp("vid_1", myOptions, function(){
        //this is the ready function and will only execute after the player is loaded
    });
    myPlayer.addEventListener(amp.eventName.error, _ampEventHandler);
    //add other event listeners
```

## <a name="next-steps"></a>Nästa steg ##

<!---Some context for the following links goes here--->
- [Azure Media Player snabb start](azure-media-player-quickstart.md)