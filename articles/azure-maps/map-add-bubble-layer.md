---
title: Lägg till ett bubbel-lager till en karta | Microsoft Azure Maps
description: Lär dig hur du återger punkter på Maps som cirklar med fasta storlekar. Se hur du använder Azure Maps Web SDK för att lägga till och anpassa Bubble-lager för det här ändamålet.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: cae29dcc0d334a2296199da0d8e3bc4562e275d3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92895336"
---
# <a name="add-a-bubble-layer-to-a-map"></a>Lägg till ett bubbel-lager till en karta

Den här artikeln visar hur du återger punkt data från en data källa som ett bubbeldiagram på en karta. Bubbeldiagram återger punkter som cirklar på kartan med en fast pixel-radie. 

> [!TIP]
> Bubbel lager som standard återger koordinaterna för alla Geometries i en data källa. För att begränsa lagret så att det bara återger punkt geometri funktioner anger du `filter` egenskapen för lagret till `['==', ['geometry-type'], 'Point']` eller `['any', ['==', ['geometry-type'], 'Point'], ['==', ['geometry-type'], 'MultiPoint']]` om du även vill inkludera Multipoint-funktioner.

## <a name="add-a-bubble-layer"></a>Lägga till ett bubbelskikt

Följande kod läser in en matris med punkter i en data källa. Sedan ansluter den data punkterna till ett [bubbel-lager](/javascript/api/azure-maps-control/atlas.layer.bubblelayer). Bubble-lagret återger radien för varje bubbla med fem bild punkter och en fyllnings färg som är vit. Och en linje färg för blått och en linje bredd på sex bild punkter. 

```javascript
//Add point locations.
var points = [
    new atlas.data.Point([-73.985708, 40.75773]),
    new atlas.data.Point([-73.985600, 40.76542]),
    new atlas.data.Point([-73.985550, 40.77900]),
    new atlas.data.Point([-73.975550, 40.74859]),
    new atlas.data.Point([-73.968900, 40.78859])
];

//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Add multiple points to the data source.
dataSource.add(points);

//Create a bubble layer to render the filled in area of the circle, and add it to the map.
map.layers.add(new atlas.layer.BubbleLayer(dataSource, null, {
    radius: 5,
    strokeColor: "#4288f7",
    strokeWidth: 6, 
    color: "white" 
}));
```

Nedan visas det fullständiga kod exemplet för ovanstående funktioner.

<br/>

<iframe height='500' scrolling='no' title='BubbleLayer data Källa' src='//codepen.io/azuremaps/embed/mzqaKB/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se <a href='https://codepen.io/azuremaps/pen/mzqaKB/'>data källan</a> för pen BubbleLayer genom att Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="show-labels-with-a-bubble-layer"></a>Visa etiketter med ett bubbel-lager

Den här koden visar hur du använder ett bubbel-lager för att återge en punkt på kartan. Och hur du använder ett symbol lager för att återge en etikett. Om du vill dölja symbolen för symbol lagret anger du `image` egenskapen för ikon alternativen till `'none'` .

<br/>

<iframe height='500' scrolling='no' title='MultiLayer data Källa' src='//codepen.io/azuremaps/embed/rqbQXy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se <a href='https://codepen.io/azuremaps/pen/rqbQXy/'>data källan</a> för pen MultiLayer genom att Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-bubble-layer"></a>Anpassa ett bubbel-lager

Bubble-lagret har bara några format alternativ. Här är ett verktyg för att testa dem.

<br/>

<iframe height='700' scrolling='no' title='Alternativ för bubbel Layer' src='//codepen.io/azuremaps/embed/eQxbGm/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se alternativen för rit <a href='https://codepen.io/azuremaps/pen/eQxbGm/'>skiktet</a> ritstift efter Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [BubbleLayer](/javascript/api/azure-maps-control/atlas.layer.bubblelayer)

> [!div class="nextstepaction"]
> [BubbleLayerOptions](/javascript/api/azure-maps-control/atlas.bubblelayeroptions)

Se följande artiklar för fler kod exempel som du kan lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Skapa en datakälla](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Lägga till ett symbolskikt](map-add-pin.md)

> [!div class="nextstepaction"]
> [Använda datadrivna formatuttryck](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Kodexempel](/samples/browse/?products=azure-maps)