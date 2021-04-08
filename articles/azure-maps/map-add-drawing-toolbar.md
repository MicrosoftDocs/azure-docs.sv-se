---
title: Lägg till verktygsfältet rit verktyg i kartan | Microsoft Azure Maps
description: Så här lägger du till ett verktygsfält för ritning i en karta med Azure Maps Web SDK
author: anastasia-ms
ms.author: v-stharr
ms.date: 09/04/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: devx-track-js
ms.openlocfilehash: b00628ec5a9f41b027bf90b93421f3aa1404e97a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92896403"
---
# <a name="add-a-drawing-tools-toolbar-to-a-map"></a>Lägga till ett verktygsfält för rit verktyg i en karta

Den här artikeln visar hur du använder modulen för rit verktyg och hur du visar verktygsfältet Rita på kartan. [DrawingToolbar](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar) -kontrollen lägger till verktygsfältet Rita i kartan. Du får lära dig hur du skapar kartor med bara ett och alla rit verktyg och hur du anpassar åter givningen av ritnings formerna i Drawing Manager.

## <a name="add-drawing-toolbar"></a>Lägga till verktygsfält för ritning

Följande kod skapar en instans av Drawing Manager och visar verktygsfältet på kartan.

```javascript
//Create an instance of the drawing manager and display the drawing toolbar.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'dark'
        })
    });
```

Nedan visas det fullständiga kod exemplet för funktionerna ovan:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Lägga till verktygsfält för ritning" src="//codepen.io/azuremaps/embed/ZEzLeRg/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Se <a href='https://codepen.io/azuremaps/pen/ZEzLeRg/'>verktygsfältet Lägg till rit objekt</a> genom att Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="limit-displayed-toolbar-options"></a>Begränsa visade verktygsfälts alternativ

Följande kod skapar en instans av Drawing Manager och visar verktygsfältet med bara ett polygonverktyget rit verktyg på kartan. 

```javascript
//Create an instance of the drawing manager and display the drawing toolbar with polygon drawing tool.
drawingManager = new atlas.drawing.DrawingManager(map, {
        toolbar: new atlas.control.DrawingToolbar({
            position: 'top-right',
            style: 'light',
            buttons: ["draw-polygon"]
        })
    });
```

Nedan visas det fullständiga kod exemplet för funktionerna ovan:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Lägg till ett rit verktyg för polygon" src="//codepen.io/azuremaps/embed/OJLWWMy/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Se rit <a href='https://codepen.io/azuremaps/pen/OJLWWMy/'>verktyget Lägg till ett polygon-verktyg för polygon</a> genom Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="change-drawing-rendering-style"></a>Ändra ritnings åter givnings format

Formatet på de former som ritas kan anpassas genom att hämta de underliggande lagren i ritnings hanteraren med hjälp av `drawingManager.getLayers()` funktionen och sedan ange alternativ på de enskilda skikten. De drag handtag som visas för koordinater när du redigerar en form är HTML-markörer. Formatet för drag handtagen kan anpassas genom att skicka alternativ för HTML-markören `dragHandleStyle` till `secondaryDragHandleStyle` alternativen och i Drawing Manager.  

Följande kod hämtar åter givnings skikten från Drawing Manager och ändrar åter givnings formatet för ritningen genom att ändra deras alternativ. I det här fallet kommer punkter att återges med en blå markör ikon. Linjerna blir röda och fyra bild punkter breda. Polygoner får en grön fyllnings färg och en orange kontur. Den ändrar sedan formaten för drag handtagen så att de är fyrkantiga ikoner. 

```javascript
//Get rendering layers of drawing manager.
var layers = drawingManager.getLayers();

//Change the icon rendered for points.
layers.pointLayer.setOptions({
    iconOptions: {
        image: 'marker-blue'
    }
});

//Change the color and width of lines.
layers.lineLayer.setOptions({
    strokeColor: 'red',
    strokeWidth: 4
});

//Change fill color of polygons.
layers.polygonLayer.setOptions({
    fillColor: 'green'
});

//Change the color of polygon outlines.
layers.polygonOutlineLayer.setOptions({
    strokeColor: 'orange'
});

//Update the style of the drag handles that appear when editting.
drawingManager.setOptions({
    //Primary drag handle that represents coordinates in the shape.
    dragHandleStyle: {
        anchor: 'center',
        htmlContent: '<svg width="15" height="15" viewBox="0 0 15 15" xmlns="http://www.w3.org/2000/svg" style="cursor:pointer"><rect x="0" y="0" width="15" height="15" style="stroke:black;fill:white;stroke-width:4px;"/></svg>',
        draggable: true
    },

    //Secondary drag hanle that represents mid-point coordinates that users can grab to add new cooridnates in the middle of segments.
    secondaryDragHandleStyle: {
        anchor: 'center',
        htmlContent: '<svg width="10" height="10" viewBox="0 0 10 10" xmlns="http://www.w3.org/2000/svg" style="cursor:pointer"><rect x="0" y="0" width="10" height="10" style="stroke:white;fill:black;stroke-width:4px;"/></svg>',
        draggable: true
    }
});  
```

Nedan visas det fullständiga kod exemplet för funktionerna ovan:

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Ändra ritnings åter givnings format" src="//codepen.io/azuremaps/embed/OJLWpyj/?height=265&theme-id=0&default-tab=js,result&editable=true" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Se <a href='https://codepen.io/azuremaps/pen/OJLWpyj/'>åter givnings formatet för Penn ändrings ritning</a> efter Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>


## <a name="next-steps"></a>Nästa steg

Lär dig hur du använder ytterligare funktioner i modulen rit verktyg:

> [!div class="nextstepaction"]
> [Hämta formdata](map-get-shape-data.md)

> [!div class="nextstepaction"]
> [Reagera på rithändelser](drawing-tools-events.md)

> [!div class="nextstepaction"]
> [Interaktionstyper och kortkommandon](drawing-tools-interactions-keyboard-shortcuts.md)

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Karta](/javascript/api/azure-maps-control/atlas.map)

> [!div class="nextstepaction"]
> [Verktygsfältet Rita](/javascript/api/azure-maps-drawing-tools/atlas.control.drawingtoolbar)

> [!div class="nextstepaction"]
> [Drawing Manager](/javascript/api/azure-maps-drawing-tools/atlas.drawing.drawingmanager)