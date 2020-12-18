---
title: Lägg till ett linje lager i Android Maps | Microsoft Azure Maps
description: Lär dig hur du lägger till rader till kartor. Se exempel som använder Azure Maps Android SDK för att lägga till linje lager i kartor och för att anpassa rader med symboler och färg toningar.
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 3e68be79a4405af103512a9009187857a0d9af39
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681745"
---
# <a name="add-a-line-layer-to-the-map-android-sdk"></a>Lägg till ett linje lager till kartan (Android SDK)

Ett linje lager kan användas för att återge `LineString` och `MultiLineString` funktioner som sökvägar eller vägar på kartan. Ett linje lager kan också användas för att återge konturen för `Polygon` och `MultiPolygon` funktionerna. En data källa är ansluten till ett linje lager för att ge den data som ska återges.

> [!TIP]
> Linje lager som standard återger koordinaterna för polygoner och linjer i en data källa. Om du vill begränsa lagret så att det bara återger lin Est ring Geometry-funktioner anger du `filter` alternativet för lagret till `eq(geometryType(), "LineString")` . Om du vill inkludera MultiLineString-funktioner kan du även ange `filter` alternativet för lagret till `any(eq(geometryType(), "LineString"), eq(geometryType(), "MultiLineString"))` .

## <a name="prerequisites"></a>Förutsättningar

Se till att slutföra stegen i snabb starten [: skapa ett Android-appaket](quick-android-map.md) . Kodblock i den här artikeln kan infogas i `onReady` händelse hanteraren Maps.

## <a name="add-a-line-layer"></a>Lägg till ett linjeskikt

Följande kod visar hur du skapar en rad. Lägg till raden i en data källa och återge den sedan med ett linje lager med hjälp av `LineLayer` klassen.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a list of points.
List<Point> points = Arrays.asList(
    Point.fromLngLat(-73.972340, 40.743270),
    Point.fromLngLat(-74.004420, 40.756800));

//Create a LineString geometry and add it to the data source.
source.add(LineString.fromLngLats(points));

//Create a line layer and add it to the map.
LineLayer layer = new LineLayer(source,
    strokeColor("blue"),
    strokeWidth(5f)
);

map.layers.add(layer);
```

Följande skärm bild visar ovanstående kod åter givning av en linje i ett linje lager.

![Mappa med en linje som återges med hjälp av linje skikt](media/android-map-add-line-layer/android-line-layer.png)

## <a name="data-drive-line-style"></a>Linje format för data enhet

Följande kod skapar två linje funktioner och lägger till ett hastighets gräns värde som en egenskap för varje rad. Ett linje lager använder ett uttryck för data enhets stil som raderna baseras på hastighets begränsning svärdet. Eftersom linje data överlappar utmed vägar lägger koden nedan till linje skiktet under etikett lagret så att väg etiketter kan läsas tydligt.

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Create a line feature.
Feature feature = Feature.fromGeometry(
    LineString.fromLngLats(Arrays.asList(
        Point.fromLngLat(-122.131821, 47.704033),
        Point.fromLngLat(-122.099919, 47.703678))));

//Add a property to the feature.
feature.addNumberProperty("speedLimitMph", 35);

//Add the feature to the data source.
source.add(feature);

//Create a second line feature.
Feature feature2 = Feature.fromGeometry(
    LineString.fromLngLats(Arrays.asList(
        Point.fromLngLat(-122.126662, 47.708265),
        Point.fromLngLat(-122.126877, 47.703980))));

//Add a property to the second feature.
feature2.addNumberProperty("speedLimitMph", 15);

//Add the second feature to the data source.
source.add(feature2);

//Create a line layer and add it to the map.
LineLayer layer = new LineLayer(source,
    strokeColor(
        interpolate(
            linear(),
            get("speedLimitMph"),
            stop(0, color(Color.GREEN)),
            stop(30, color(Color.YELLOW)),
            stop(60, color(Color.RED))
        )
    ),
    strokeWidth(5f)
);

map.layers.add(layer, "labels");
```

Följande skärm bild visar ovanstående kod åter givning av två rader i ett linje lager med deras färg som hämtas från ett data drivet format uttryck som baseras på en egenskap i linje funktionerna.

![Mappa med data Drive-formaterade linjer som återges i ett linje lager](media/android-map-add-line-layer/android-line-layer-data-drive-style.png)

## <a name="add-symbols-along-a-line"></a>Lägg till symboler längs en linje

Det här exemplet visar hur du lägger till pil-ikoner längs en linje på kartan. När du använder ett symbol lager ställer du in `symbolPlacement` alternativet på `SymbolPlacement.LINE` . Med det här alternativet återges symbolerna längs linjen och ikonerna roteras (0 grader = höger).

```java
//Create a data source and add it to the map.
DataSource source = new DataSource();
map.sources.add(source);

//Load a image of an arrow into the map image sprite and call it "arrow-icon".
map.images.add("arrow-icon", R.drawable.purple-arrow-right);

//Create and add a line to the data source.
source.add(LineString.fromLngLats(Arrays.asList(
        Point.fromLngLat(-122.18822, 47.63208),
        Point.fromLngLat(-122.18204, 47.63196),
        Point.fromLngLat(-122.17243, 47.62976),
        Point.fromLngLat(-122.16419, 47.63023),
        Point.fromLngLat(-122.15852, 47.62942),
        Point.fromLngLat(-122.15183, 47.62988),
        Point.fromLngLat(-122.14256, 47.63451),
        Point.fromLngLat(-122.13483, 47.64041),
        Point.fromLngLat(-122.13466, 47.64422),
        Point.fromLngLat(-122.13844, 47.65440),
        Point.fromLngLat(-122.13277, 47.66515),
        Point.fromLngLat(-122.12779, 47.66712),
        Point.fromLngLat(-122.11595, 47.66712),
        Point.fromLngLat(-122.11063, 47.66735),
        Point.fromLngLat(-122.10668, 47.67035),
        Point.fromLngLat(-122.10565, 47.67498))));

//Create a line layer and add it to the map.
map.layers.add(new LineLayer(source,
    strokeColor("DarkOrchid"),
    strokeWidth(5f)
));

//Create a symbol layer and add it to the map.
map.layers.add(new SymbolLayer(source,
    //Space symbols out along line.
    symbolPlacement(SymbolPlacement.LINE),

    //Spread the symbols out 100 pixels apart.
    symbolSpacing(100f),
    
    //Use the arrow icon as the symbol.
    iconImage("arrow-icon"),

    //Allow icons to overlap so that they aren't hidden if they collide with other map elements.
    iconAllowOverlap(true),

    //Center the symbol icon.
    iconAnchor(AnchorType.CENTER),

    //Scale the icon size.
    iconSize(0.8f)
));
```

I det här exemplet lästes följande bild in i appens drawable-mapp.

| ![Bild av lila piltangent](media/android-map-add-line-layer/purple-arrow-right.png)|
|:-----------------------------------------------------------------------:|
|                                                  |

Skärm bilden nedan visar ovanstående kod åter givning av en linje med pilar som visas längs den.

![Mappa med data Drive-formaterade linjer med pilar som återges i ett linje lager](media/android-map-add-line-layer/android-symbols-along-line-path.png)

## <a name="next-steps"></a>Nästa steg

Se följande artiklar för fler kod exempel som du kan lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Skapa en datakälla](create-data-source-android-sdk.md)

> [!div class="nextstepaction"]
> [Använda datadrivna formatuttryck](data-driven-style-expressions-android-sdk.md)

> [!div class="nextstepaction"]
> [Lägg till ett polygonskikt](how-to-add-shapes-to-android-map.md)
