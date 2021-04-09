---
title: Lägg till ett panel lager till Android Maps | Microsoft Azure Maps
description: Lär dig hur du lägger till ett panel lager till en karta. Se ett exempel som använder Azure Maps Android SDK för att lägga till ett väderleks överlägg i en karta.
author: rbrundritt
ms.author: richbrun
ms.date: 3/25/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: ac37a4e6d68decdf6780560963a0c534689e8dbb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105608993"
---
# <a name="add-a-tile-layer-to-a-map-android-sdk"></a>Lägga till ett panel lager till en karta (Android SDK)

Den här artikeln visar hur du återger ett panel lager på en karta med hjälp av Azure Maps Android SDK. Med panel lager kan du placera bilder ovanpå Azure Maps bas kart paneler. Mer information om Azure Maps displacerings systemet finns i dokumentationen för [zoomnings nivåer och Brick rutnät](zoom-levels-and-tile-grid.md) .

Ett panel lager läses in i paneler från en server. Dessa bilder kan förrenderas och lagras på samma sätt som andra bilder på en server med hjälp av en namngivnings konvention som panel lagret förstår. Eller så kan de här avbildningarna återges med en dynamisk tjänst som genererar avbildningarna nära real tid. Det finns tre olika namngivnings konventioner för panel tjänster som stöds av Azure Maps TileLayer-klassen:

* X-, Y-, zoomnings-och zoomnings nivå, x är kolumnen och Y är panelens rad position i panel rutnätet.
* Quadkey notation – kombination x, y, zoomnings information till ett enda sträng värde som är en unik identifierare för en panel.
* Koordinater för avgränsnings rutor kan användas för att ange en bild i formatet `{west},{south},{east},{north}` , som ofta används av [webb mappnings tjänster (WMS)](https://www.opengeospatial.org/standards/wms).

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

## <a name="add-an-ogc-web-mapping-service-wms"></a>Lägga till en OGC-tjänst för webb mappning (WMS)

En webb mappnings tjänst (WMTS) är en Open Geospatial Consortium (OGC) standard för att betjäna avbildningar av kart data. Det finns många öppna data uppsättningar som är tillgängliga i det här formatet som du kan använda med Azure Maps. Den här typen av tjänst kan användas med ett panel lager om tjänsten stöder `EPSG:3857` systemets referens system för koordinater. När du använder en WMS-tjänst ställer du in parametrarna för bredd och höjd till samma värde som stöds av tjänsten, se till att ange samma värde i `tileSize` alternativet. I den formaterade URL: en anger du `BBOX` parametern för tjänsten med `{bbox-epsg-3857}` plats hållaren.

::: zone pivot="programming-language-java-android"

``` java
TileLayer layer = new TileLayer(
    tileUrl("https://mrdata.usgs.gov/services/gscworld?FORMAT=image/png&HEIGHT=1024&LAYERS=geology&REQUEST=GetMap&STYLES=default&TILED=true&TRANSPARENT=true&WIDTH=1024&VERSION=1.3.0&SERVICE=WMS&CRS=EPSG:3857&BBOX={bbox-epsg-3857}"),
    tileSize(1024)
);

map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = TileLayer(
    tileUrl("https://mrdata.usgs.gov/services/gscworld?FORMAT=image/png&HEIGHT=1024&LAYERS=geology&REQUEST=GetMap&STYLES=default&TILED=true&TRANSPARENT=true&WIDTH=1024&VERSION=1.3.0&SERVICE=WMS&CRS=EPSG:3857&BBOX={bbox-epsg-3857}"),
    tileSize(1024)
)

map.layers.add(layer, "labels")
```

::: zone-end

Följande skärm bild visar koden ovan som överlappar en webb kart tjänst av geologiska data från den [USGS datauppsättningen (U.S. geologisk Survey)](https://mrdata.usgs.gov/) ovanpå en karta, under etiketterna.

![Android-karta som visar WMS-Brick skikt](media/how-to-add-tile-layer-android-map/android-tile-layer-wms.jpg)

## <a name="add-an-ogc-web-mapping-tile-service-wmts"></a>Lägg till en OGC-WMTS (Web-Mapping panel)

En webb kart panels tjänst (WMTS) är en Open Geospatial Consortium (OGC) standard för att betjäna sida vid sida-baserade överlägg för Maps. Det finns många öppna data uppsättningar som är tillgängliga i det här formatet som du kan använda med Azure Maps. Den här typen av tjänst kan användas med ett panel lager om tjänsten stöder `EPSG:3857` eller `GoogleMapsCompatible` koordinerar referens systemet (boknings system). När du använder en WMTS-tjänst ställer du in parametrarna för bredd och höjd till samma värde som stöds av tjänsten, se till att ange samma värde i `tileSize` alternativet. Ersätt följande plats hållare i den formaterade URL: en:

* `{TileMatrix}` => `{z}`
* `{TileRow}` => `{y}`
* `{TileCol}` => `{x}`

::: zone pivot="programming-language-java-android"

``` java
TileLayer layer = new TileLayer(
    tileUrl("https://basemap.nationalmap.gov/arcgis/rest/services/USGSImageryOnly/MapServer/WMTS/tile/1.0.0/USGSImageryOnly/default/GoogleMapsCompatible/{z}/{y}/{x}"),
    tileSize(256),
    bounds(-173.25000107492872, 0.0005794121990209753, 146.12527718104752, 71.506811402077),
    maxSourceZoom(18)
);

map.layers.add(layer, "transit");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
val layer = TileLayer(
    tileUrl("https://basemap.nationalmap.gov/arcgis/rest/services/USGSImageryOnly/MapServer/WMTS/tile/1.0.0/USGSImageryOnly/default/GoogleMapsCompatible/{z}/{y}/{x}"),
    tileSize(256),
    bounds(-173.25000107492872, 0.0005794121990209753, 146.12527718104752, 71.506811402077),
    maxSourceZoom(18)
)

map.layers.add(layer, "transit")
```

::: zone-end

På följande skärm bild visas ovanstående kod som överlappar en webb kart panels tjänst av bilder från den [nationella kartan för USGS datauppsättningen (U.S. geologisk Survey)](https://viewer.nationalmap.gov/services/) ovanpå en karta, under vägar och etiketter.

![Android-karta som visar WMTS panel lager](media/how-to-add-tile-layer-android-map/android-tile-layer-wmts.jpg)

## <a name="next-steps"></a>Nästa steg

I följande artikel finns mer information om hur du kan täcka över bilder på en karta.

> [!div class="nextstepaction"]
> [Bild skikt](map-add-image-layer-android.md)
