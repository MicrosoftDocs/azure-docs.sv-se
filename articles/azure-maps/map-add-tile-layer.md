---
title: Lägga till ett panel lager till en karta | Microsoft Azure Maps
description: Lär dig hur du översätter bilder på kartor. Se ett exempel som använder Azure Maps Web SDK för att lägga till ett panel lager som innehåller ett väderleks överlägg i en karta.
author: rbrundritt
ms.author: richbrun
ms.date: 07/29/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.custom: codepen, devx-track-js
ms.openlocfilehash: b3619995739c51d68b00f37ebea3a38680a6b6e7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92890985"
---
# <a name="add-a-tile-layer-to-a-map"></a>Lägga till ett panelskikt till en karta

Den här artikeln visar hur du lägger till ett panel lager på kartan. Med panel lager kan du placera bilder ovanpå Azure Maps bas kart paneler. Mer information om hur du Azure Maps i systemet finns i [zoomnings nivåer och panel rutnät](zoom-levels-and-tile-grid.md).

Ett panel lager läses in i paneler från en server. Dessa bilder kan antingen förrenderas eller renderas dynamiskt. Förrenderade bilder lagras som en annan bild på en server med hjälp av en namngivnings konvention som panel lagret förstår. Dynamiskt återgivna bilder använder en tjänst för att läsa in bilderna nära real tid. Det finns tre olika namngivnings konventioner för panel tjänster som stöds av Azure Maps [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer) -klassen: 

* X, Y, zoom notation-X är kolumnen, Y är panelens rad position i panel rutnätet och zoomningen formaterar ett värde baserat på zoomnivån.
* Quadkey-notation – kombinerar x-, y-och zoomnings information till ett enda sträng värde. Detta sträng värde blir en unik identifierare för en enda panel.
* Avgränsnings ruta – ange en bild i markerings rutans koordinater-format: `{west},{south},{east},{north}` . Det här formatet används ofta av [Web mappnings tjänster (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> En [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer) är ett bra sätt att visualisera stora data uppsättningar på kartan. Om du inte bara vill att ett panel lager ska genereras från en bild kan vektor data även återges som ett panel lager. Genom att återge vektor data som ett panel lager behöver kart kontrollen bara läsa in panelerna som är mindre i fil storleken än de vektor data de representerar. Den här tekniken används ofta för att återge miljon tals rader med data på kartan.

Panel-URL: en som skickas till ett panel lager måste vara en http-eller HTTPS-URL till en TileJSON-resurs eller en panel-URL-mall som använder följande parametrar: 

* `{x}` -X position i rutan. Du måste också ha `{y}` och `{z}` .
* `{y}` -Y position i panelen. Du måste också ha `{x}` och `{z}` .
* `{z}` -Zoomnings nivå i panelen. Du måste också ha `{x}` och `{y}` .
* `{quadkey}` -Panel quadkey identifierare baserat på Bing Maps-panelens system namngivnings konvention.
* `{bbox-epsg-3857}` – En sträng med avgränsnings rutor med formatet `{west},{south},{east},{north}` i EPSG 3857 rums referens system.
* `{subdomain}` – En plats hållare för under domänens värden, om den är angiven, `subdomain` kommer att läggas till.
* `{azMapsDomain}` – En plats hållare för att justera domänen och autentiseringen av panel begär Anden med samma värden som används av kartan.

## <a name="add-a-tile-layer"></a>Lägga till ett panelskikt

 Det här exemplet visar hur du skapar ett panel lager som pekar på en uppsättning paneler. I det här exemplet används systemet x, y, zoom-inpassning. Källan till det här panel lagret är ett väderleks överlägg av [Iowa-miljön för Mesonet i Iowa State University](https://mesonet.agron.iastate.edu/ogc/). När du visar radar data skulle användarna tydligt se etiketterna för städer när de navigerar i kartan. Detta beteende kan implementeras genom att infoga panel lagret under `labels` lagret.

```javascript
//Create a tile layer and add it to the map below the label layer.
//Weather radar tiles from Iowa Environmental Mesonet of Iowa State University.
map.layers.add(new atlas.layer.TileLayer({
    tileUrl: 'https://mesonet.agron.iastate.edu/cache/tile.py/1.0.0/nexrad-n0q-900913/{z}/{x}/{y}.png',
    opacity: 0.8,
    tileSize: 256
}), 'labels');
```

Nedan visas det fullständiga kod exemplet för ovanstående funktioner.

<br/>

<iframe height='500' scrolling='no' title='Panel lager med X, Y och Z' src='//codepen.io/azuremaps/embed/BGEQjG/?height=500&theme-id=0&default-tab=js,result&embed-version=2&editable=true' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se Penn <a href='https://codepen.io/azuremaps/pen/BGEQjG/'>panels lagret med X, Y och Z</a> efter Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="customize-a-tile-layer"></a>Anpassa ett panel lager

Panel skikts klassen har många format alternativ. Här är ett verktyg för att testa dem.

<br/>

<iframe height='700' scrolling='no' title='Alternativ för panel lager' src='//codepen.io/azuremaps/embed/xQeRWX/?height=700&theme-id=0&default-tab=result' frameborder='no' loading="lazy" allowtransparency='true' allowfullscreen='true' style='width: 100%;'>Se alternativ för Penn <a href='https://codepen.io/azuremaps/pen/xQeRWX/'>panels lager</a> efter Azure Maps ( <a href='https://codepen.io/azuremaps'>@azuremaps</a> ) på <a href='https://codepen.io'>CodePen</a>.
</iframe>

## <a name="next-steps"></a>Nästa steg

Läs mer om de klasser och metoder som används i den här artikeln:

> [!div class="nextstepaction"]
> [TileLayer](/javascript/api/azure-maps-control/atlas.layer.tilelayer)

> [!div class="nextstepaction"]
> [TileLayerOptions](/javascript/api/azure-maps-control/atlas.tilelayeroptions)

Se följande artiklar för fler kod exempel som du kan lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Lägga till ett avbildningsskikt](./map-add-image-layer.md)