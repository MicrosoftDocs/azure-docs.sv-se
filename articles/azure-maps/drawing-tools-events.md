---
title: Händelser i rit verktyg | Microsoft Azure Maps
description: I den här artikeln får du lära dig hur du lägger till ett verktygsfält för ritning till en karta med Microsoft Azure Maps-webbsdk
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 6abe0ed88adbdf8263aa27d340fb2fff156d98e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "90089351"
---
# <a name="drawing-tool-events"></a>Rit verktygs händelser

När du använder rit verktyg på en karta är det bra att reagera på vissa händelser när användaren ritar på kartan. I den här tabellen listas alla händelser som stöds av `DrawingManager` klassen.

| Händelse | Beskrivning |
|-------|-------------|
| `drawingchanged` | Utlöses när en koordinat i en form har lagts till eller ändrats. | 
| `drawingchanging` | Utlöses när en förhands gransknings koordinat för en form visas. Den här händelsen kommer till exempel att utlösa flera gånger när en koordinat dras. | 
| `drawingcomplete` | Utlöses när en form har tagits bort eller tagits bort från redigerings läget. |
| `drawingmodechanged` | Utlöses när rit läget har ändrats. Det nya ritnings läget överförs till händelse hanteraren. |
| `drawingstarted` | Utlöses när användaren börjar att rita en form eller placerar en form i redigerings läge.  |

Följande kod visar hur händelserna i modulen rit verktyg fungerar. Rita former på kartan och se vilka händelser som utlöses.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Rit verktygs händelser" src="https://codepen.io/azuremaps/embed/dyPMRWo?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Se händelser för rit verktyg för rit <a href='https://codepen.io/azuremaps/pen/dyPMRWo'>verktyg</a> efter Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) i <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="examples"></a>Exempel

Nu ska vi se några vanliga scenarier som använder rit verktygs händelser.

### <a name="select-points-in-polygon-area"></a>Välj punkter i polygon-ytan

Den här koden visar hur du övervakar en händelse av en användares ritnings former. I det här exemplet övervakar koden former av polygoner, rektanglar och cirklar. Sedan avgör den vilka data punkter på kartan som ligger inom det ritade fältet. `drawingcomplete`Händelsen används för att utlösa Select-logiken. I SELECT Logic loopar koden genom alla data punkter på kartan. Det kontrollerar om det finns en skärning av punkten och området för den ritade figuren. Det här exemplet använder [Turf.jss ](https://turfjs.org/) biblioteket med öppen källkod för att utföra en spatial skärnings beräkning.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Välj data i ritat polygon-ytdiagram" src="https://codepen.io/azuremaps/embed/XWJdeja?height=500&theme-id=default&default-tab=result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Se pennan <a href='https://codepen.io/azuremaps/pen/XWJdeja'>Markera data i det ritade polygonområdet</a> genom att Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="draw-and-search-in-polygon-area"></a>Rita och Sök i polygon-område

Den här koden söker efter antecknings punkter inuti en forms yta efter att användaren har ritat formen. Du kan ändra och köra koden genom att klicka på Redigera på kod penna i det övre högra hörnet i ramen. `drawingcomplete`Händelsen används för att utlösa Sök logiken. Om användaren ritar en rektangel eller polygon utförs en sökning i geometrin. Om en cirkel ritas, används Radi-och Center positionen för att utföra en orienterings punkts ökning. `drawingmodechanged`Händelsen används för att avgöra när användaren växlar till ritnings läget och den här händelsen rensar arbets ytan.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Rita och Sök i polygon-område" src="https://codepen.io/azuremaps/embed/eYmZGNv?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Se Penn <a href='https://codepen.io/azuremaps/pen/eYmZGNv'>ritning och Sök i polygon-område</a> efter Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

### <a name="create-a-measuring-tool"></a>Skapa ett Mät verktyg

Koden nedan visar hur du kan använda rit händelser för att skapa ett Mät verktyg. `drawingchanging`Används för att övervaka formen, när den ritas. När användaren flyttar musen beräknas formens dimensioner. `drawingcomplete`Händelsen används för att göra en slutgiltig beräkning på formen när den har ritats. `drawingmodechanged`Händelsen används för att avgöra när användaren växlar till ett rit läge. Dessutom  `drawingmodechanged` rensar händelsen arbets ytan och rensar gammal mått information.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Mät verktyg" src="https://codepen.io/azuremaps/embed/RwNaZXe?height=500&theme-id=default&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Se Penn <a href='https://codepen.io/azuremaps/pen/RwNaZXe'>Mät verktyget</a> genom att Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

<br/>

## <a name="next-steps"></a>Nästa steg

Lär dig hur du använder ytterligare funktioner i modulen rit verktyg:

> [!div class="nextstepaction"]
> [Hämta formdata](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Interaktionstyper och kortkommandon](drawing-tools-interactions-keyboard-shortcuts.md)

Läs mer om modulen tjänster:

> [!div class="nextstepaction"]
> [Tjänstmodul](how-to-use-services-module.md)

Se fler kod exempel:

> [!div class="nextstepaction"]
> [Kod exempel sida](https://aka.ms/AzureMapsSamples)
