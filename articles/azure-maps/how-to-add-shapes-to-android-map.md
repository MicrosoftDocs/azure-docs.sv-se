---
title: Lägg till ett polygon-lager till Android Maps | Microsoft Azure Maps
description: Lär dig hur du lägger till polygoner eller cirklar till kartor. Se hur du använder Azure Maps Android SDK för att anpassa geometriska former och göra dem enkla att uppdatera och underhålla.
author: rbrundritt
ms.author: richbrun
ms.date: 2/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 68d68424e71bcf60bf504ae174b84b9c361b8637
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102097269"
---
# <a name="add-a-polygon-layer-to-the-map-android-sdk"></a>Lägg till ett polygon-lager till kartan (Android SDK)

Den här artikeln visar hur du återger områdena i `Polygon` och `MultiPolygon` funktions Geometries på kartan med ett polygon-lager.

## <a name="prerequisites"></a>Förutsättningar

Se till att slutföra stegen i snabb starten [: skapa ett Android-appaket](quick-android-map.md) . Kodblock i den här artikeln kan infogas i `onReady` händelse hanteraren Maps.

## <a name="use-a-polygon-layer"></a>Använda ett polygon-lager

När ett polygon-lager är anslutet till en data källa och läses in på kartan, återges ytan med `Polygon` och- `MultiPolygon` funktioner. Om du vill skapa en polygon lägger du till den i en data källa och återger den med ett polygon lager med hjälp av `PolygonLayer` klassen.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a rectangular polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-73.98235, 40.76799),
            Point.fromLngLat(-73.95785, 40.80044),
            Point.fromLngLat(-73.94928, 40.79680),
            Point.fromLngLat(-73.97317, 40.76437),
            Point.fromLngLat(-73.98235, 40.76799)
        )
    )
));

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(new PolygonLayer(source, 
    fillColor("red"),
    fillOpacity(0.7f)
), "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a rectangular polygon.
source.add(
    Polygon.fromLngLats(
        Arrays.asList(
            Arrays.asList(
                Point.fromLngLat(-73.98235, 40.76799),
                Point.fromLngLat(-73.95785, 40.80044),
                Point.fromLngLat(-73.94928, 40.79680),
                Point.fromLngLat(-73.97317, 40.76437),
                Point.fromLngLat(-73.98235, 40.76799)
            )
        )
    )
)

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(
    PolygonLayer(
        source,
        fillColor("red"),
        fillOpacity(0.7f)
    ), "labels"
)
```

::: zone-end

Följande skärm bild visar ovanstående kod som återger ytan i en polygon med ett polygon-lager.

![Polygon med sitt fyllnings yta återges](media/how-to-add-shapes-to-android-map/android-polygon-layer.png)

## <a name="use-a-polygon-and-line-layer-together"></a>Använda ett polygon-och linje lager tillsammans

Ett linje lager används för att återge konturen för polygoner. I följande kod exempel återges en polygon som i föregående exempel, men nu läggs ett linje lager till. Detta linje skikt är ett andra lager som är anslutet till data källan.

::: zone pivot="programming-language-java-android"

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a rectangular polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-73.98235, 40.76799),
            Point.fromLngLat(-73.95785, 40.80044),
            Point.fromLngLat(-73.94928, 40.79680),
            Point.fromLngLat(-73.97317, 40.76437),
            Point.fromLngLat(-73.98235, 40.76799)
        )
    )
));

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(new PolygonLayer(source,
    fillColor("rgba(0, 200, 200, 0.5)")
), "labels");

//Create and add a line layer to render the outline of the polygon.
map.layers.add(new LineLayer(source,
    strokeColor("red"),
    strokeWidth(2f)
));
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a rectangular polygon.
source.add(
    Polygon.fromLngLats(
        Arrays.asList(
            Arrays.asList(
                Point.fromLngLat(-73.98235, 40.76799),
                Point.fromLngLat(-73.95785, 40.80044),
                Point.fromLngLat(-73.94928, 40.79680),
                Point.fromLngLat(-73.97317, 40.76437),
                Point.fromLngLat(-73.98235, 40.76799)
            )
        )
    )
)

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(
    PolygonLayer(
        source,
        fillColor("rgba(0, 200, 200, 0.5)")
    ), "labels"
)

//Create and add a line layer to render the outline of the polygon.
map.layers.add(
    LineLayer(
        source,
        strokeColor("red"),
        strokeWidth(2f)
    )
)
```

::: zone-end

Följande skärm bild visar ovanstående kod åter givning av en polygon med dess kontur med ett linje lager.

![Polygon med sitt fyllnads yta och disposition som återges](media/how-to-add-shapes-to-android-map/android-polygon-and-line-layer.png)

> [!TIP]
> När du ska disponera en polygon med ett linje lager måste du stänga alla ringar i polygoner, så att varje matris med punkter har samma start-och slut punkt. Om detta inte är färdigt, kan inte rad lagret ansluta den sista punkten i polygonen till den första punkten.

## <a name="fill-a-polygon-with-a-pattern"></a>Fylla en polygon med ett mönster

Förutom att fylla i en polygon med en färg kan du använda ett bild mönster för att fylla polygonen. Läs in ett bild mönster i Maps-avbildningen Sprite-resurser och referera sedan till den här bilden med `fillPattern` alternativet för polygon-lagret.

::: zone pivot="programming-language-java-android"

```java
//Load an image pattern into the map image sprite.
map.images.add("fill-checker-red", R.drawable.fill_checker_red);

//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a polygon.
source.add(Polygon.fromLngLats(
    Arrays.asList(
        Arrays.asList(
            Point.fromLngLat(-50, -20),
            Point.fromLngLat(0, 40),
            Point.fromLngLat(50, -20),
            Point.fromLngLat(-50, -20)
        )
    )
));

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(new PolygonLayer(source,
        fillPattern("fill-checker-red"),
        fillOpacity(0.5f)
), "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Load an image pattern into the map image sprite.
map.images.add("fill-checker-red", R.drawable.fill_checker_red)

//Create a data source and add it to the map.
val source = DataSource()
map.sources.add(source)

//Create a polygon.
source.add(
    Polygon.fromLngLats(
        Arrays.asList(
            Arrays.asList(
                Point.fromLngLat(-50, -20),
                Point.fromLngLat(0, 40),
                Point.fromLngLat(50, -20),
                Point.fromLngLat(-50, -20)
            )
        )
    )
)

//Create and add a polygon layer to render the polygon on the map, below the label layer.
map.layers.add(
    PolygonLayer(
        source,
        fillPattern("fill-checker-red"),
        fillOpacity(0.5f)
    ), "labels"
)
```

::: zone-end

I det här exemplet lästes följande bild in i appens drawable-mapp.

| ![Bild av lila piltangent](media/how-to-add-shapes-to-android-map/fill-checker-red.png)|
|:-----------------------------------------------------------------------:|
| fill_checker_red.png                                                    |

Följande är en skärm bild av koden ovan som återger en polygon med ett fyllnings mönster på kartan.

![Polygon med ett fyllnings mönster återges på kartan](media/how-to-add-shapes-to-android-map/android-polygon-pattern.jpg)

## <a name="next-steps"></a>Nästa steg

Se följande artiklar för fler kod exempel som du kan lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Skapa en datakälla](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Använda datadrivna formatuttryck](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Lägg till ett linjeskikt](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Lägga till ett polygonprojektionslager](map-extruded-polygon-android.md)
