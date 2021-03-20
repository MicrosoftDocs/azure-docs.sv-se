---
title: Funktions typer och kortkommandon för rit verktyg på kartan | Microsoft Azure Maps
description: Rita och redigera former med hjälp av en mus, touch-skärm eller tangent bord i Microsoft Azure Maps-webbsdk
author: rbrundritt
ms.author: richbrun
ms.date: 12/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: b609a21348222150faf5fb828f2cc38ca6658f5d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92895825"
---
# <a name="interaction-types-and-keyboard-shortcuts-in-the-drawing-tools-module"></a>Interaktions typer och kortkommandon i modulen rit verktyg

Den här artikeln beskriver alla olika sätt att rita och redigera former med hjälp av en mus, touch-skärm eller kortkommandon.

Drawing Manager har stöd för tre olika sätt att interagera med kartan, för att rita former.

* `click` -Koordinater läggs till när musen eller touchen klickas.
* `freehand ` -Koordinater läggs till när musen eller touchen dras på kartan.
* `hybrid` -Koordinater läggs till när musen eller touchen klickas eller dras.

## <a name="how-to-draw-shapes"></a>Rita former

 Innan en form kan ritas, ställer du in `drawingMode` alternativet för ritnings hanteraren till en ritnings inställning som stöds. Den här inställningen kan vara programmerad eller anropas genom att trycka på en av rit knapparna i verktygsfältet. Rit läget förblir aktiverat, även när en form har ritats, vilket gör det enkelt att rita ytterligare former av samma typ. Ställer in rit läget program mässigt i inaktivt läge. Eller växla till inaktivt läge genom att klicka på knappen nuvarande ritnings lägen i verktygsfältet.

I nästa avsnitt beskrivs alla olika sätt som former kan ritas på kartan.

### <a name="how-to-draw-a-point"></a>Så här ritar du en punkt

När ritnings hanteraren är i `draw-point` rit läge kan du utföra följande åtgärder för att rita punkter på kartan. Dessa metoder fungerar med alla interaktions lägen.

**Starta ritning**
 - Klicka på den vänstra mus knappen eller tryck på kartan för att lägga till en punkt på kartan. 
 - Om musen är över kartan trycker du på tangenten så `F` läggs en punkt till i mus pekarens koordinat. Den här metoden ger högre precision för att lägga till en punkt på kartan. Det kommer att röra sig om mindre rörelser på musen på grund av den vänstra mus knappens rörelse.
 - Fortsätt att klicka, vidrör eller trycka `F` för att lägga till fler punkter på kartan.
 
**Slutför ritning**
 - Klicka på en knapp i verktygsfältet Rita. 
 - Ange rit läget program mässigt. 
 - Tryck på knappen `C` .

**Avbryt ritning**
 - Tryck på knappen `Escape` .

### <a name="how-to-draw-a-line"></a>Så här ritar du en linje

När ritnings hanteraren är i `draw-line` läget kan du utföra följande åtgärder för att rita punkter på kartan, beroende på interaktions läget.

**Starta ritning**
 - Klicka på läge
   * Klicka på den vänstra mus knappen eller tryck på kartan för att lägga till varje punkt på en linje på kartan. En koordinat läggs till i raden för varje klick eller touch. 
   * Om musen är över kartan trycker du på tangenten så `F` läggs en punkt till i mus pekarens koordinat. Den här metoden ger högre precision för att lägga till en punkt på kartan. Det kommer att röra sig om mindre rörelser på musen på grund av den vänstra mus knappens rörelse.
   * Fortsätt att klicka tills alla önskade punkter har lagts till på raden.
 - FreeHand-läge
   * Tryck på vänster MUSKNAPP eller tryck på kartan och dra musen eller berörings punkten runt. Koordinater läggs till på raden när musen eller tryck punkten flyttas runt kartan. När musen eller touch-händelsen utlöses, slutförs ritningen. Den frekvens med vilken koordinater läggs till definieras av alternativet rit hanterare `freehandInterval` .
 - Hybrid läge
   * Växla mellan klicknings-och FreeHand-metoder, efter behov, när du ritar en enskild rad. Klicka till exempel på några punkter, håll och dra musen för att lägga till en bunt med punkter och klicka sedan på några fler. 

**Slutför ritning**
 - Hybrid/klick läge
   * Dubbelklicka på kartan vid den sista punkten. 
   * Klicka på en knapp i verktygsfältet Rita. 
   * Ange rit läget program mässigt. 
 - FreeHand-läge
   * Släpp mus knappen eller touch-punkten.
 - Tryck på knappen `C` .

**Avbryt ritning**
 - Tryck på knappen `Escape` .

### <a name="how-to-draw-a-polygon"></a>Så här ritar du en polygon

När ritnings hanteraren är i `draw-polygon` läget kan du utföra följande åtgärder för att rita punkter på kartan, beroende på interaktions läget.

**Starta ritning**
 - Klicka på läge
   * Klicka på den vänstra mus knappen eller tryck på kartan för att lägga till varje punkt i en polygon på kartan. En koordinat läggs till i polygonen för varje klick eller touch. 
   * Om musen är över kartan trycker du på tangenten så `F` läggs en punkt till i mus pekarens koordinat. Den här metoden ger högre precision för att lägga till en punkt på kartan. Det kommer att röra sig om mindre rörelser på musen på grund av den vänstra mus knappens rörelse.
   * Fortsätt att klicka tills alla önskade punkter har lagts till i polygonen.
 - FreeHand-läge
   * Tryck på vänster MUSKNAPP eller tryck på kartan och dra musen eller berörings punkten runt. Koordinater läggs till i polygonen när musen eller touch-punkten flyttas runt kartan. När musen eller touch-händelsen utlöses, slutförs ritningen. Den frekvens med vilken koordinater läggs till definieras av alternativet rit hanterare `freehandInterval` .
 - Hybrid läge
   * Växla mellan klicknings-och FreeHand-metoder efter behov, när du ritar en enda polygon. Klicka till exempel på några punkter, håll och dra musen för att lägga till en bunt med punkter och klicka sedan på några fler. 

**Slutför ritning**
 - Hybrid/klick läge
   * Dubbelklicka på kartan vid den sista punkten. 
   * Klicka på den första punkten i polygonen.
   * Klicka på en knapp i verktygsfältet Rita. 
   * Ange rit läget program mässigt. 
 - FreeHand-läge
   * Släpp mus knappen eller touch-punkten.
 - Tryck på knappen `C` .

**Avbryt ritning**
 - Tryck på knappen `Escape` .

### <a name="how-to-draw-a-rectangle"></a>Så här ritar du en rektangel

När ritnings hanteraren är i `draw-rectangle` läget kan du utföra följande åtgärder för att rita punkter på kartan, beroende på interaktions läget. Den genererade formen följer den [utökade INTERjson-specifikationen för rektanglar](extend-geojson.md#rectangle).

**Starta ritning**
 - Tryck på knappen med vänster MUSKNAPP eller tryck på kartan för att lägga till det första hörnet i rektangeln och dra för att skapa rektangeln. 

**Slutför ritning**
 - Släpp mus knappen eller touch-punkten.
 - Ange rit läget program mässigt. 
 - Tryck på knappen `C` .

**Avbryt ritning**
 - Tryck på knappen `Escape` .

### <a name="how-to-draw-a-circle"></a>Så här ritar du en cirkel

När ritnings hanteraren är i `draw-circle` läget kan du utföra följande åtgärder för att rita punkter på kartan, beroende på interaktions läget. Den genererade formen följer den [utökade INTERjson-specifikationen för cirklar](extend-geojson.md#circle).

**Starta ritning**
 - Tryck på vänster MUSKNAPP eller tryck på kartan för att lägga till cirkelns centrum och dra ge cirklarna en radie. 

**Slutför ritning**
 - Släpp mus knappen eller touch-punkten.
 - Ange rit läget program mässigt. 
 - Tryck på knappen `C` .

**Avbryt ritning**
 - Tryck på knappen `Escape` .

## <a name="keyboard-shortcuts"></a>Kortkommandon

Rit verktygen stöder kortkommandon. Dessa kortkommandon fungerar när kartan är i fokus.

| Nyckel      | Action                            |
|----------|-----------------------------------|
| `C` | Slutför alla ritningar som pågår och ställer in ritnings läget på inaktiv. Fokus flyttas till ett kart element på översta nivån.  |
| `Escape` | Avbryter alla ritningar som pågår och ställer in ritnings läget på inaktivt. Fokus flyttas till ett kart element på översta nivån.  |
| `F` | Lägger till en koordinat till en punkt, linje eller polygon om musen är över kartan. Motsvarande åtgärd för att klicka på kartan när du är i klick eller hybrid läge. Med den här genvägen kan du använda mer exakta och snabbare ritningar. Du kan använda en hand för att placera musen och andra för att trycka på knappen utan att flytta musen från tryck gesten. |

## <a name="next-steps"></a>Nästa steg

Läs mer om klasserna i modulen rit verktyg:

> [!div class="nextstepaction"]
> [Drawing Manager](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)

> [!div class="nextstepaction"]
> [Verktygsfältet Rita](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)