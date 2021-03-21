---
title: Lägg till ett panel lager till Android Maps | Microsoft Azure Maps
description: Lär dig hur du lägger till ett panel lager till en karta. Se ett exempel som använder Azure Maps Android SDK för att lägga till ett väderleks överlägg i en karta.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 6a920dc222cae4aedd77b667644de317637bbb69
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102047510"
---
# <a name="add-a-tile-layer-to-a-map-android-sdk"></a>Lägga till ett panel lager till en karta (Android SDK)

Den här artikeln visar hur du återger ett panel lager på en karta med hjälp av Azure Maps Android SDK. Med panel lager kan du placera bilder ovanpå Azure Maps bas kart paneler. Mer information om Azure Maps displacerings systemet finns i dokumentationen för [zoomnings nivåer och Brick rutnät](zoom-levels-and-tile-grid.md) .

Ett panel lager läses in i paneler från en server. Dessa bilder kan förrenderas och lagras på samma sätt som andra bilder på en server med hjälp av en namngivnings konvention som panel lagret förstår. Eller så kan de här avbildningarna återges med en dynamisk tjänst som genererar avbildningarna nära real tid. Det finns tre olika namngivnings konventioner för panel tjänster som stöds av Azure Maps TileLayer-klassen:

* X-, Y-, zoomnings-och zoomnings nivå, x är kolumnen och Y är panelens rad position i panel rutnätet.
* Quadkey notation – kombination x, y, zoomnings information till ett enda sträng värde som är en unik identifierare för en panel.
* Koordinater för avgränsnings rutor kan användas för att ange en bild i formatet `{west},{south},{east},{north}` som ofta används av [webb mappnings tjänster (WMS)](https://www.opengeospatial.org/standards/wms).

> [!TIP]
> En TileLayer är ett bra sätt att visualisera stora data uppsättningar på kartan. Ett panel lager kan inte bara genereras från en bild, men vektor data kan även återges som ett panel lager. Genom att återge vektor data som ett panel lager behöver kart kontrollen bara läsa in panelerna, vilket kan vara mycket mindre i fil storleken än de vektor data de representerar. Den här tekniken används av många som behöver rendera miljon tals rader med data på kartan.

Panel-URL: en som skickas till ett panel lager måste vara en HTTP/HTTPS-URL till en TileJSON-resurs eller en panel-URL-mall som använder följande parametrar: 

* `{x}` -X position i rutan. Du måste också ha `{y}` och `{z}` .
* `{y}` -Y position i panelen. Du måste också ha `{x}` och `{z}` .
* `{z}` -Zoomnings nivå i panelen. Du måste också ha `{x}` och `{y}` .
* `{quadkey}` -Panel quadkey identifierare baserat på Bing Maps-panelens system namngivnings konvention.
* `{bbox-epsg-3857}` – En sträng med avgränsnings rutor med formatet `{west},{south},{east},{north}` i EPSG 3857 rums referens system.
* `{subdomain}` – En plats hållare för under domänens värden, om värdet under domän är angivet.
* `azmapsdomain.invalid` – En plats hållare för att justera domänen och autentiseringen av panel begär Anden med samma värden som används av kartan. Använd det här när du anropar en panel tjänst som körs av Azure Maps.

## <a name="prerequisites"></a>Förutsättningar

För att slutföra processen i den här artikeln måste du installera [Azure Maps Android SDK](how-to-use-android-map-control-library.md) för att läsa in en karta.

## <a name="add-a-tile-layer-to-the-map"></a>Lägg till ett panel lager på kartan

Det här exemplet visar hur du skapar ett panel lager som pekar på en uppsättning paneler. Det här exemplet använder systemet "x, y, zoom". Källan till det här panel lagret är [OpenSeaMap-projektet](https://openseamap.org/index.php), som innehåller massor av förflyttade nautiska diagram. Ofta när du visar panel lager, är det önskvärt att kunna se etiketterna för städer på kartan tydligt. Detta kan uppnås genom att infoga panel lagret under kart etikettens lager.

::: zone pivot="programming-language-java-android"

```java
TileLayer layer = new TileLayer(
    tileUrl("https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png"),
    opacity(0.8f),
    tileSize(256),
    minSourceZoom(7),
    maxSourceZoom(17)
);

map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = TileLayer(
    tileUrl("https://tiles.openseamap.org/seamark/{z}/{x}/{y}.png"),
    opacity(0.8f),
    tileSize(256),
    minSourceZoom(7),
    maxSourceZoom(17)
)

map.layers.add(layer, "labels")
```

::: zone-end

Följande skärm bild visar ovanstående kod som visar ett panel lager med nautisk information på en karta som har ett mörkt grå Skale format.

![Android-karta som visar panel lager](media/how-to-add-tile-layer-android-map/xyz-tile-layer-android.png)

## <a name="next-steps"></a>Nästa steg

I följande artikel finns mer information om hur du kan täcka över bilder på en karta.

> [!div class="nextstepaction"]
> [Bild skikt](map-add-image-layer-android.md)
