---
title: Lägg till ett bubbel-lager till Android Maps | Microsoft Azure Maps
description: Lär dig hur du återger punkter på Maps som cirklar med fasta storlekar. Se hur du använder Azure Maps Android SDK för att lägga till och anpassa Bubble-lager för det här ändamålet.
author: rbrundritt
ms.author: richbrun
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 7506a2083a34832ee3f6f6222f86d35d10228728
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681834"
---
# <a name="add-a-bubble-layer-to-a-map-android-sdk"></a>Lägga till ett bubbel lager till en karta (Android SDK)

Den här artikeln visar hur du återger punkt data från en data källa som ett bubbeldiagram på en karta. Bubbeldiagram återger punkter som cirklar på kartan med en fast pixel-radie.

> [!TIP]
> Bubbel lager som standard återger koordinaterna för alla Geometries i en data källa. Om du vill begränsa lagret så att det bara återger punkt geometri funktioner, anger du `filter` alternativet för lagret till `eq(geometryType(), "Point")` . Om du vill inkludera MultiPoint-funktioner anger du även `filter` alternativet för skiktet `any(eq(geometryType(), "Point"), eq(geometryType(), "MultiPoint"))` .

## <a name="prerequisites"></a>Förutsättningar

Se till att slutföra stegen i snabb starten [: skapa ett Android-appaket](quick-android-map.md) . Kodblock i den här artikeln kan infogas i `onReady` händelse hanteraren Maps.

## <a name="add-a-bubble-layer"></a>Lägga till ett bubbelskikt

Följande kod läser in en matris med punkter i en data källa. Sedan ansluter den data punkterna till ett bubbel-lager. Bubble-lagret återger radien för varje bubbla med fem bild punkter och en fyllnings färg som är vit. Och en linje färg för blått och en linje bredd på sex bild punkter.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create point locations.
Point[] points = new Point[] {
    Point.fromLngLat(-73.985708, 40.75773),
    Point.fromLngLat(-73.985600, 40.76542),
    Point.fromLngLat(-73.985550, 40.77900),
    Point.fromLngLat(-73.975550, 40.74859),
    Point.fromLngLat(-73.968900, 40.78859)
};

//Add multiple points to the data source.
source.add(points);

//Create a bubble layer to render the filled in area of the circle, and add it to the map.
BubbleLayer layer = new BubbleLayer(source, 
    bubbleRadius(5f),
    bubbleColor("white"),
    bubbleStrokeColor("#4288f7"),
    bubbleStrokeWidth(6f)
);

map.layers.add(layer);
```

Följande skärm bild visar ovanstående kod återger punkter i ett bubbel lager.

![Mappa med punkter som återges med bubbeldiagram](media/map-add-bubble-layer-android/android-bubble-layer.png)

## <a name="show-labels-with-a-bubble-layer"></a>Visa etiketter med ett bubbel-lager

Den här koden visar hur du använder ett bubbel-lager för att återge en punkt på kartan. Och hur du använder ett symbol lager för att återge en etikett. Om du vill dölja symbolen för symbol lagret ställer du in `iconImage` alternativet till `"none"` .

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Add a data point to the map.
source.add(Point.fromLngLat(-122.336641,47.627631));

//Add a bubble layer.
map.layers.add(new BubbleLayer(source,
    bubbleRadius(5f),
    bubbleColor("white"),
    bubbleStrokeColor("#4288f7"),
    bubbleStrokeWidth(6f)
));

//Add a symbol layer to display text, hide the icon image.
map.layers.add(new SymbolLayer(source,
    //Hide the icon image.
    iconImage("none"),
    textField("Museum of History & Industry (MOHAI)"),
    textColor("#005995"),
    textOffset(new Float[]{0f, -2.2f})
));
```

Följande skärm bild visar ovanstående kod Rending en punkt i ett bubbel lager och en text etikett för punkten med ett symbol lager.

![Kart punkt som återges med hjälp av ett bubbel lager och en text etikett med symbol skikt](media/map-add-bubble-layer-android/android-bubble-symbol-layer.png)

## <a name="next-steps"></a>Nästa steg

Se följande artiklar för fler kod exempel som du kan lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Skapa en datakälla](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Lägga till ett symbolskikt](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Använda datadrivna formatuttryck](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Visa funktionsinformation](display-feature-information-android.md)
