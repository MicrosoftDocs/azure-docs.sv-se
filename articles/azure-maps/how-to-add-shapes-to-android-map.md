---
title: Lägg till ett polygon-lager till Android Maps | Microsoft Azure Maps
description: Lär dig hur du lägger till polygoner eller cirklar till kartor. Se hur du använder Azure Maps Android SDK för att anpassa geometriska former och göra dem enkla att uppdatera och underhålla.
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 1712cedab9cef23108fcc48b8e09bdc3e33065c4
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97679481"
---
# <a name="add-a-polygon-layer-to-the-map-android-sdk"></a>Lägg till ett polygon-lager till kartan (Android SDK)

Den här artikeln visar hur du återger områdena i `Polygon` och `MultiPolygon` funktions Geometries på kartan med ett polygon-lager.

## <a name="prerequisites"></a>Förutsättningar

Se till att slutföra stegen i snabb starten [: skapa ett Android-appaket](quick-android-map.md) . Kodblock i den här artikeln kan infogas i `onReady` händelse hanteraren Maps.

## <a name="use-a-polygon-layer"></a>Använda ett polygon-lager

När ett polygon-lager är anslutet till en data källa och läses in på kartan, återges ytan med `Polygon` och- `MultiPolygon` funktioner. Om du vill skapa en polygon lägger du till den i en data källa och återger den med ett polygon lager med hjälp av `PolygonLayer` klassen.

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

Följande skärm bild visar ovanstående kod som återger ytan i en polygon med ett polygon-lager.

![Polygon med sitt fyllnings yta återges](media/how-to-add-shapes-to-android-map/android-polygon-layer.png)

## <a name="use-a-polygon-and-line-layer-together"></a>Använda ett polygon-och linje lager tillsammans

Ett linje lager används för att återge konturen för polygoner. I följande kod exempel återges en polygon som i föregående exempel, men nu läggs ett linje lager till. Detta linje skikt är ett andra lager som är anslutet till data källan.  

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

Följande skärm bild visar ovanstående kod åter givning av en polygon med dess kontur med ett linje lager.

![Polygon med sitt fyllnads yta och disposition som återges](media/how-to-add-shapes-to-android-map/android-polygon-and-line-layer.png)

> [!TIP]
> När du ska disponera en polygon med ett linje lager måste du stänga alla ringar i polygoner, så att varje matris med punkter har samma start-och slut punkt. Om detta inte är färdigt, kan inte rad lagret ansluta den sista punkten i polygonen till den första punkten.

## <a name="next-steps"></a>Nästa steg

Se följande artiklar för fler kod exempel som du kan lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Skapa en datakälla](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Använda datadrivna formatuttryck](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Lägg till ett linjeskikt](android-map-add-line-layer.md)
