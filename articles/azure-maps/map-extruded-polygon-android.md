---
title: Lägga till ett polygon extrusion-lager i en Android-karta | Microsoft Azure Maps
description: Så här lägger du till ett polygon extrusion-lager i Microsoft Azure mappar Android SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 02/19/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: b62c2540dc8cf2c7d4f67d465b2d464cbc0c3091
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102055054"
---
# <a name="add-a-polygon-extrusion-layer-to-the-map-android-sdk"></a>Lägg till ett polygon extrusion-lager till kartan (Android SDK)

Den här artikeln visar hur du använder polygon extrusion-lagret för att återge områden i `Polygon` och `MultiPolygon` funktions Geometries som extruderade former.

## <a name="use-a-polygon-extrusion-layer"></a>Använda ett polygon extrusion-lager

Anslut polygon extrusion-skiktet till en data källa. Sedan läser du in den på kartan. Polygon extrusion-lagret återger områdena i en- `Polygon` och- `MultiPolygon` funktioner som extruderade former. `height`Egenskaperna och `base` för polygonens extrusion-lager definierar bas avståndet från den malda formens och höjden i **meter**. Följande kod visar hur du skapar en polygon, lägger till den i en data källa och återger den med hjälp av skikt klassen polygon extrusion.

> [!Note]
> `base`Värdet som definieras i polygon extrusion-lagret ska vara mindre än eller lika med för `height` .

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-73.95838379859924, 40.80027995478159),
            Point.fromLngLat(-73.98154735565186, 40.76845986171129),
            Point.fromLngLat(-73.98124694824219, 40.767761062136955),
            Point.fromLngLat(-73.97361874580382, 40.76461637311633),
            Point.fromLngLat(-73.97306084632874, 40.76512830937617),
            Point.fromLngLat(-73.97259950637817, 40.76490890860481),
            Point.fromLngLat(-73.9494466781616, 40.79658450499243),
            Point.fromLngLat(-73.94966125488281, 40.79708807289436),
            Point.fromLngLat(-73.95781517028809, 40.80052360358227),
            Point.fromLngLat(-73.95838379859924, 40.80027995478159)
        )
    )
));

//Create and add a polygon extrusion layer to the map below the labels so that they are still readable.
PolygonExtrusionLayer layer = new PolygonExtrusionLayer(source,
    fillColor("#fc0303"),
    fillOpacity(0.7f),
    height(500f)
);

//Create and add a polygon extrusion layer to the map below the labels so that they are still readable.
map.layers.add(layer, "labels");
```

Följande skärm bild visar ovanstående kod Rending en polygon som sträcker sig lodrätt med ett polygon extrusion lager.

![Mappa med polygon som sträcker sig lodrätt med ett polygon extrusion-lager](media/map-extruded-polygon-android/polygon-extrusion-layer.jpg)

## <a name="add-data-driven-polygons"></a>Lägg till data drivna polygoner

En choropleth-karta kan återges med hjälp av polygon extrusion-lagret. Ange `height` egenskaperna och `fillColor` för extrusion-lagrets mått för den statistiska variabeln i `Polygon` `MultiPolygon` funktionen och Geometries. I följande kod exempel visas en extruderad choropleth-karta över USA baserat på måttet för populationens densitet per tillstånd.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Import the geojson data and add it to the data source.
Utils.importData("https://azuremapscodesamples.azurewebsites.net/Common/data/geojson/US_States_Population_Density.json", this,  (String result) -> {
    //Parse the data as a GeoJSON Feature Collection.
    FeatureCollection fc = FeatureCollection.fromJson(result);

    //Add the feature collection to the data source.
    source.add(fc);
});

//Create and add a polygon extrusion layer to the map below the labels so that they are still readable.
PolygonExtrusionLayer layer = new PolygonExtrusionLayer(source,
    fillOpacity(0.7f),
    fillColor(
        step(
            get("density"),
            literal("rgba(0, 255, 128, 1)"),
            stop(10, "rgba(9, 224, 118, 1)"),
            stop(20, "rgba(11, 191, 103, 1)"),
            stop(50, "rgba(247, 227, 5, 1)"),
            stop(100, "rgba(247, 199, 7, 1)"),
            stop(200, "rgba(247, 130, 5, 1)"),
            stop(500, "rgba(247, 94, 5, 1)"),
            stop(1000, "rgba(247, 37, 5, 1)")
        )
    ),
    height(
        interpolate(
            linear(),
            get("density"),
            stop(0, 100),
            stop(1200, 960000)
        )
    )
);

//Create and add a polygon extrusion layer to the map below the labels so that they are still readable.
map.layers.add(layer, "labels");
```

På följande skärm bild visas en choropleth karta över amerikanska delstater som är färgade och sträcker sig lodrätt som extruderade polygoner baserade på populationens densitet.

![En choropleth karta över amerikanska lägen är färgad och utsträckt lodrätt som utskjutande polygoner baserat på populationens densitet](media/map-extruded-polygon-android/android-extruded-choropleth.jpg)

## <a name="next-steps"></a>Nästa steg

Se följande artiklar för fler kod exempel som du kan lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Skapa en datakälla](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Använda datadrivna formatuttryck](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Lägg till ett polygonskikt](how-to-add-shapes-to-android-map.md)
