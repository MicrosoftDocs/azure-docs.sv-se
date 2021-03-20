---
title: Lägg till ett polygon-lager till en karta | Microsoft Azure Maps
description: Lär dig hur du lägger till polygoner eller cirklar till kartor. Se hur du använder Azure Maps Web SDK för att anpassa geometriska former och göra dem enkla att uppdatera och underhålla.
author: anastasia-ms
ms.author: v-stharr
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: 96553e75d4b982cfe67d03961d4356a3844f253c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92891005"
---
# <a name="add-a-polygon-layer-to-the-map"></a>Lägg till ett polygon-lager till kartan

Den här artikeln visar hur du återger områdena i `Polygon` och `MultiPolygon` funktions Geometries på kartan med ett polygon-lager. Azure Maps Web SDK stöder även skapande av cirkel-Geometries som definierats i det [utökade INTERjson-schemat](extend-geojson.md#circle). Dessa cirklar omvandlas till polygoner när de återges på kartan. Alla funktions Geometries kan enkelt uppdateras när de omslutits med [atlasen. Shape](/javascript/api/azure-maps-control/atlas.shape) -klass.

## <a name="use-a-polygon-layer"></a>Använda ett polygon-lager 

När ett polygon-lager är anslutet till en data källa och läses in på kartan, återges ytan med `Polygon` och- `MultiPolygon` funktioner. Om du vill skapa en polygon lägger du till den i en data källa och återger den med ett polygon lager med hjälp av klassen [PolygonLayer](/javascript/api/azure-maps-control/atlas.layer.polygonlayer) .

```javascript
//Create a data source and add it to the map.
var dataSource = new atlas.source.DataSource();
map.sources.add(dataSource);

//Create a rectangular polygon.
dataSource.add(new atlas.data.Feature(
    new atlas.data.Polygon([[
        [-73.98235, 40.76799],
        [-73.95785, 40.80044],
        [-73.94928, 40.7968],
        [-73.97317, 40.76437],
        [-73.98235, 40.76799]
    ]])
));

//Create and add a polygon layer to render the polygon to the map, below the label layer.
map.layers.add(new atlas.layer.PolygonLayer(dataSource, null,{
    fillColor: 'red',
    fillOpacity: 0.7
}), 'labels');
```

Nedan visas ett komplett och löpande exempel på ovanstående kod.

<br/>

<iframe height='500' scrolling='no' title='Lägg till en polygon till en karta ' src='//codepen.io/azuremaps/embed/yKbOvZ/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/yKbOvZ/'>Lägg till en polygon till en karta </a> efter Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="use-a-polygon-and-line-layer-together"></a>Använda ett polygon-och linje lager tillsammans

Ett linje lager används för att återge konturen för polygoner. I följande kod exempel återges en polygon som i föregående exempel, men nu läggs ett linje lager till. Detta linje skikt är ett andra lager som är anslutet till data källan.  

<br/>

<iframe height='500' scrolling='no' title='Polygon och linje skikt för att lägga till polygon' src='//codepen.io/azuremaps/embed/aRyEPy/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Lägg till polygon med Azure Maps () på CodePen i Pen- <a href='https://codepen.io/azuremaps/pen/aRyEPy/'>polygonen och linje lagret</a> <a href='https://codepen.io/azuremaps'>@azuremaps</a> . <a href='https://codepen.io'></a>
</iframe>

## <a name="fill-a-polygon-with-a-pattern"></a>Fylla en polygon med ett mönster

Förutom att fylla i en polygon med en färg kan du använda ett bild mönster för att fylla polygonen. Läs in ett bild mönster i Maps-avbildningen Sprite-resurser och referera sedan till den här avbildningen med `fillPattern` egenskapen för polygon-lagret.

<br/>

<iframe height="500" style="width: 100%;" scrolling="no" title="Fyllnings mönster för polygon" src="//codepen.io/azuremaps/embed/JzQpYX/?height=500&theme-id=0&default-tab=js,result" frameborder='no' loading="lazy" allowtransparency="true" allowfullscreen="true">
Se <a href='https://codepen.io/azuremaps/pen/JzQpYX/'>Fyllnings mönstret</a> för pen-polygonen genom att Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>


> [!TIP]
> Azure Maps Web SDK innehåller flera anpassningsbara bildmallar som du kan använda som fyllnings mönster. Mer information finns i dokumentet om [att använda bildmallar](how-to-use-image-templates-web-sdk.md) .

## <a name="customize-a-polygon-layer"></a>Anpassa ett polygon-lager

Polygon-lagret har bara några få format alternativ. Här är ett verktyg för att testa dem.

<br/>

<iframe height='700' scrolling='no' title='LXvxpg' src='//codepen.io/azuremaps/embed/LXvxpg/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/LXvxpg/'>LXvxpg</a> av Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

<a id="addACircle"></a>

## <a name="add-a-circle-to-the-map"></a>Lägg till en cirkel till kartan

Azure Maps använder en utökad version av det injson-schema som tillhandahåller en definition för cirklar, som du ser [här](extend-geojson.md#circle). En cirkel återges på kartan genom att en funktion skapas `Point` . Detta `Point` har en `subType` egenskap med värdet `"Circle"` och en `radius` egenskap med ett tal som representerar radien i meter. 

```javascript
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}  
```

Azure Maps Web SDK konverterar dessa `Point` funktioner till `Polygon` funktioner. Sedan återges dessa funktioner på kartan med hjälp av polygon-och linje lager enligt följande kod exempel.

<br/>

<iframe height='500' scrolling='no' title='Lägga till en cirkel till en karta' src='//codepen.io/azuremaps/embed/PRmzJX/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se pennan <a href='https://codepen.io/azuremaps/pen/PRmzJX/'>Lägg till en cirkel till en karta</a> efter Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="make-a-geometry-easy-to-update"></a>Gör en geometri lätt att uppdatera

En `Shape` klass radbryter en [geometri](/javascript/api/azure-maps-control/atlas.data.geometry) eller [funktion](/javascript/api/azure-maps-control/atlas.data.feature) och gör det enkelt att uppdatera och underhålla dessa funktioner. Om du vill instansiera en form variabel skickar du en geometri eller en uppsättning egenskaper till form-konstruktorn.

```javascript
//Creating a shape by passing in a geometry and a object containing properties.
var shape1 = new atlas.Shape(new atlas.data.Point[0,0], { myProperty: 1 });

//Creating a shape using a feature.
var shape2 = new atlas.Shape(new atlas.data.Feature(new atlas.data.Point[0,0], { myProperty: 1 });
```

Följande kod exempel visar hur du omsluter ett cirkel-interjson-objekt med en Shape-klass. Som värdet för RADIUS-ändringar i formen återges cirkeln automatiskt på kartan.

<br/>

<iframe height='500' scrolling='no' title='Uppdatera form egenskaper' src='//codepen.io/azuremaps/embed/ZqMeQY/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se egenskaperna för Penn <a href='https://codepen.io/azuremaps/pen/ZqMeQY/'>uppdaterings formen</a> genom Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [Polygonlasso](/javascript/api/azure-maps-control/atlas.data.polygon)

> [!div class="nextstepaction"]
> [PolygonLayer](/javascript/api/azure-maps-control/atlas.layer.polygonlayer)

> [!div class="nextstepaction"]
> [PolygonLayerOptions](/javascript/api/azure-maps-control/atlas.polygonlayeroptions)

Fler kod exempel som kan läggas till i dina kartor finns i följande artiklar:

> [!div class="nextstepaction"]
> [Skapa en datakälla](create-data-source-web-sdk.md)

> [!div class="nextstepaction"]
> [Lägg till ett popup-fönster](map-add-popup.md)

> [!div class="nextstepaction"]
> [Använda datadrivna formatuttryck](data-driven-style-expressions-web-sdk.md)

> [!div class="nextstepaction"]
> [Så här använder du avbildningsmallar](how-to-use-image-templates-web-sdk.md)

> [!div class="nextstepaction"]
> [Lägg till ett linjeskikt](map-add-line-layer.md)

Ytterligare resurser:

> [!div class="nextstepaction"]
> [Tillägg för Azure Mapsal JSON-specifikation](extend-geojson.md#circle)