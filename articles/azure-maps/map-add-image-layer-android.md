---
title: Lägga till ett bild lager i en Android-karta | Microsoft Azure Maps
description: Lär dig hur du lägger till avbildningar till en karta. Se hur du använder Azure Maps Android SDK för att anpassa bild lager och överläggs bilder på fasta uppsättningar koordinater.
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: e1d99297c0357039606149bdf7e5a526258fc7c5
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054943"
---
# <a name="add-an-image-layer-to-a-map-android-sdk"></a>Lägga till ett bild lager till en karta (Android SDK)

Den här artikeln visar hur du lägger till en bild i en fast uppsättning koordinater. Här följer några exempel på olika typer av avbildningar som kan skrivas över på Maps:

* Avbildningar hämtade från drönare
* Skapa Floorplans
* Historiska eller andra specialiserade kart bilder
* Ritningar av jobb webbplatser

> [!TIP]
> Ett bild lager är ett enkelt sätt att täcka över en bild på en karta. Observera att stora avbildningar kan förbruka mycket minne och kan orsaka prestanda problem. I det här fallet kan du dela upp bilden i paneler och läsa in dem i kartan som ett panel lager.

## <a name="add-an-image-layer"></a>Lägga till ett avbildningsskikt

Följande kod överlappar en bild av en [karta över Newark, New Jersey, från 1922](https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg) på kartan. Den här avbildningen läggs till i `drawable` mappen i projektet. Ett bild lager skapas genom att ställa in bilden och koordinaterna för de fyra hörnen i formatet `[Top Left Corner, Top Right Corner, Bottom Right Corner, Bottom Left Corner]` . Det är ofta önskvärt att lägga till bild lager under `label` lagret.

::: zone pivot="programming-language-java-android"

```java
//Create an image layer.
ImageLayer layer = new ImageLayer(
    imageCoordinates(
        new Position[] {
            new Position(-74.22655, 40.773941), //Top Left Corner
            new Position(-74.12544, 40.773941), //Top Right Corner
            new Position(-74.12544, 40.712216), //Bottom Right Corner
            new Position(-74.22655, 40.712216)  //Bottom Left Corner
        }
    ),
    setImage(R.drawable.newark_nj_1922)
);

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create an image layer.
val layer = ImageLayer(
    imageCoordinates(
        arrayOf<Position>(
            Position(-74.22655, 40.773941),  //Top Left Corner
            Position(-74.12544, 40.773941),  //Top Right Corner
            Position(-74.12544, 40.712216),  //Bottom Right Corner
            Position(-74.22655, 40.712216)   //Bottom Left Corner
        )
    ),
    setImage(R.drawable.newark_nj_1922)
)

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels")
```

::: zone-end

Alternativt kan du ange en URL till en bild som finns på online. Om ditt scenario tillåter det kan du dock lägga till avbildningen i `drawable` mappen Projects, som kommer att inaktive ras snabbare eftersom avbildningen kommer att vara lokalt tillgänglig och inte behöver laddas ned.

::: zone pivot="programming-language-java-android"

```java
//Create an image layer.
ImageLayer layer = new ImageLayer(
    imageCoordinates(
        new Position[] {
            new Position(-74.22655, 40.773941), //Top Left Corner
            new Position(-74.12544, 40.773941), //Top Right Corner
            new Position(-74.12544, 40.712216), //Bottom Right Corner
            new Position(-74.22655, 40.712216)  //Bottom Left Corner
        }
    ),
    setUrl("https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg")
);

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Create an image layer.
val layer = ImageLayer(
    imageCoordinates(
        arrayOf<Position>(
            Position(-74.22655, 40.773941),  //Top Left Corner
            Position(-74.12544, 40.773941),  //Top Right Corner
            Position(-74.12544, 40.712216),  //Bottom Right Corner
            Position(-74.22655, 40.712216) //Bottom Left Corner
        )
    ),
    setUrl("https://www.lib.utexas.edu/maps/historical/newark_nj_1922.jpg")
)

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels")
```

::: zone-end

Följande skärm bild visar en karta över Newark, New Jersey, från 1922 som har beställts med ett bild lager.

![Karta över Newark, New Jersey, från 1922 som har överanvänts med ett bild lager](media/map-add-image-layer-android/android-image-layer.gif)

## <a name="import-a-kml-file-as-ground-overlay"></a>Importera en KML-fil som mark överlägg

Det här exemplet visar hur du lägger till KML-information för mark överlägg som ett bild lager på kartan. KML-överlägg ger norra, syd-, sydöstra-och västra koordinater och en rotations medsols rotation. Men bild lagret förväntar sig att koordinaterna för varje hörn i bilden. KML-markplanet i det här exemplet är för Chartres-Cathedral och det kommer från [Wikimedia](https://commons.wikimedia.org/wiki/File:Chartres.svg/overlay.kml).

```xml
<?xml version="1.0" encoding="UTF-8"?>
<kml xmlns="http://www.opengis.net/kml/2.2" xmlns:gx="http://www.google.com/kml/ext/2.2" xmlns:kml="http://www.opengis.net/kml/2.2" xmlns:atom="http://www.w3.org/2005/Atom">
<GroundOverlay>
    <name>Map of Chartres cathedral</name>
    <Icon>
        <href>https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Chartres.svg/1600px-Chartres.svg.png</href>
        <viewBoundScale>0.75</viewBoundScale>
    </Icon>
    <LatLonBox>
        <north>48.44820923628113</north>
        <south>48.44737203258976</south>
        <east>1.488833825534365</east>
        <west>1.486788581643038</west>
        <rotation>46.44067597839695</rotation>
    </LatLonBox>
</GroundOverlay>
</kml>
```

Koden använder den statiska `getCoordinatesFromEdges` metoden från `ImageLayer` klassen. Den här metoden beräknar bildens fyra hörn med hjälp av Nord-, syd-, sydöstra-, väst-och rotations informationen för KML-markplanet.

::: zone pivot="programming-language-java-android"

```java
//Calculate the corner coordinates of the ground overlay.
Position[] corners = ImageLayer.getCoordinatesFromEdges(
    //North, south, east, west
    48.44820923628113, 48.44737203258976, 1.488833825534365, 1.486788581643038,

    //KML rotations are counter-clockwise, subtract from 360 to make them clockwise.
    360 - 46.44067597839695
);

//Create an image layer.
ImageLayer layer = new ImageLayer(
    imageCoordinates(corners),
    setUrl("https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Chartres.svg/1600px-Chartres.svg.png")
);

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels");
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Calculate the corner coordinates of the ground overlay.
val corners: Array<Position> =
    ImageLayer.getCoordinatesFromEdges( //North, south, east, west
        48.44820923628113,
        48.44737203258976,
        1.488833825534365,
        1.486788581643038,  //KML rotations are counter-clockwise, subtract from 360 to make them clockwise.
        360 - 46.44067597839695
    )

//Create an image layer.
val layer = ImageLayer(
    imageCoordinates(corners),
    setUrl("https://upload.wikimedia.org/wikipedia/commons/thumb/e/e3/Chartres.svg/1600px-Chartres.svg.png")
)

//Add the image layer to the map, below the label layer.
map.layers.add(layer, "labels")
```

::: zone-end

Följande skärm bild visar en karta med ett KML-markplan som är överlappat med ett bild lager.

![Mappa med ett KMLt markplan som är överlappat med ett bild lager](media/map-add-image-layer-android/android-ground-overlay.jpg)

> [!TIP]
> Använd `getPixels` metoderna och `getPositions` för bild skikts klassen för att konvertera mellan geografiska koordinater för det placerade bild lagret och koordinaterna för de lokala bild punkterna.

## <a name="next-steps"></a>Nästa steg

I följande artikel finns mer information om hur du kan täcka över bilder på en karta.

> [!div class="nextstepaction"]
> [Lägga till ett panelskikt](how-to-add-tile-layer-android-map.md)