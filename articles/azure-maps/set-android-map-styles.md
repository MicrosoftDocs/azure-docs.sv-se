---
title: Ange en kart stil i Android Maps | Microsoft Azure Maps
description: Lär dig två sätt att ställa in formatet på en karta. Se hur du använder Azure Maps Android SDK i layout filen eller i klassen Activity för att justera formatet.
author: rbrundritt
ms.author: richbrun
ms.date: 02/26/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
zone_pivot_groups: azure-maps-android
ms.openlocfilehash: 9dcb5b84faa47b7307ce8441003e8591d7c2757b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105604795"
---
# <a name="set-map-style-android-sdk"></a>Ange kart stil (Android SDK)

Den här artikeln visar två sätt att ange kart stilar med hjälp av Azure Maps Android SDK. Azure Maps har sex olika typer av kartor att välja mellan. Mer information om mappnings format som stöds finns i [mappnings format som stöds i Azure Maps](supported-map-styles.md).

## <a name="prerequisites"></a>Förutsättningar

Se till att slutföra stegen i snabb starten [: skapa ett Android-appaket](quick-android-map.md) .

## <a name="set-map-style-in-the-layout"></a>Ange kart stil i layouten

Du kan ange en kart stil i layout filen för din aktivitets klass när du lägger till kart kontrollen. Följande kod anger centrum-plats, zoomnings nivå och kart stil.

```XML
<com.microsoft.azure.maps.mapcontrol.MapControl
    android:id="@+id/mapcontrol"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    app:mapcontrol_centerLat="47.602806"
    app:mapcontrol_centerLng="-122.329330"
    app:mapcontrol_zoom="12"
    app:mapcontrol_style="grayscale_dark"
    />
```

Följande skärm bild visar koden ovan som visar en karta med mörkt grå Skale format.

![Mappa med mörkt schema format för grå Skale kartor](media/set-android-map-styles/android-grayscale-dark.png)

## <a name="set-map-style-in-code"></a>Ange kart stil i kod

Map-formatet kan anges via programmering i kod med hjälp av `setStyle` kart metoden. Följande kod anger centrum-platsen och zoomnings nivån med hjälp av Maps- `setCamera` metoden och kart formatet till `SATELLITE_ROAD_LABELS` .

::: zone pivot="programming-language-java-android"

```java
mapControl.onReady(map -> {

    //Set the camera of the map.
    map.setCamera(center(Point.fromLngLat(-122.33, 47.64)), zoom(14));

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE_ROAD_LABELS));
});
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
mapControl!!.onReady { map: AzureMap ->
    //Set the camera of the map.
    map.setCamera(center(Point.fromLngLat(-122.33, 47.64)), zoom(14))

    //Set the style of the map.
    map.setStyle(style(MapStyle.SATELLITE_ROAD_LABELS))
}
```

::: zone-end

Följande skärm bild visar koden ovan som visar en karta med stilen för satellit vägs etiketter.

![Mappa med väg etiketts format](media/set-android-map-styles/android-satellite-road-labels.png)

## <a name="setting-the-map-camera"></a>Ställa in kart kameran

Kart kameran styr vilken del av världen som visas i kart visnings området. Kameran kan vara i layouten genom programmering i kod. När du anger den i kod finns det två huvudsakliga metoder för att ange placeringen av kartan. använda centrera och zooma eller skicka i en markerings ruta. Följande kod visar hur du ställer in alla valfria kamera alternativ när du använder `center` och `zoom` .

::: zone pivot="programming-language-java-android"

```java
//Set the camera of the map using center and zoom.
map.setCamera(
    center(Point.fromLngLat(-122.33, 47.64)), 

    //The zoom level. Typically a value between 0 and 22.
    zoom(14),

    //The amount of tilt in degrees the map where 0 is looking straight down.
    pitch(45),

    //Direction the top of the map is pointing in degrees. 0 = North, 90 = East, 180 = South, 270 = West
    bearing(90),

    //The minimum zoom level the map will zoom-out to when animating from one location to another on the map.
    minZoom(10),
    
    //The maximum zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Set the camera of the map using center and zoom.
map.setCamera(
    center(Point.fromLngLat(-122.33, 47.64)), 

    //The zoom level. Typically a value between 0 and 22.
    zoom(14),

    //The amount of tilt in degrees the map where 0 is looking straight down.
    pitch(45),

    //Direction the top of the map is pointing in degrees. 0 = North, 90 = East, 180 = South, 270 = West
    bearing(90),

    //The minimum zoom level the map will zoom-out to when animating from one location to another on the map.
    minZoom(10),
    
    //The maximum zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
)
```

::: zone-end

Det är ofta önskvärt att fokusera kartan över en uppsättning data. En avgränsnings ruta kan beräknas från funktioner med hjälp av `MapMath.fromData` metoden och kan skickas till `bounds` kart kamerans alternativ. När du anger en kart visning baserad på en markerings ram är det ofta användbart att ange ett `padding` värde för pixel storleken för punkter som återges som bubblor eller symboler. Följande kod visar hur du ställer in alla valfria kamera alternativ när du använder en begränsnings ruta för att ställa in kamerans placering.

::: zone pivot="programming-language-java-android"

```java
//Set the camera of the map using a bounding box.
map.setCamera(
    //The area to focus the map on.
    bounds(BoundingBox.fromLngLats(
        //West
        -122.4594,

        //South
        47.4333,
        
        //East
        -122.21866,
        
        //North
        47.75758
    )),

    //Amount of pixel buffer around the bounding box to provide extra space around the bounding box.
    padding(20),

    //The maximum zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
);
```

::: zone-end

::: zone pivot="programming-language-kotlin"

```kotlin
//Set the camera of the map using a bounding box.
map.setCamera(
    //The area to focus the map on.
    bounds(BoundingBox.fromLngLats(
        //West
        -122.4594,

        //South
        47.4333,
        
        //East
        -122.21866,
        
        //North
        47.75758
    )),

    //Amount of pixel buffer around the bounding box to provide extra space around the bounding box.
    padding(20),

    //The maximum zoom level the map will zoom-in to when animating from one location to another on the map.
    maxZoom(14)
)
```

::: zone-end

Proportionerna för en avgränsnings ruta får inte vara samma som proportionerna för kartan, eftersom kartan ofta visar hela gränsen, men det är ofta bara tätt lodrätt eller vågrätt.

## <a name="next-steps"></a>Nästa steg

Se följande artiklar för fler kod exempel som du kan lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Lägga till ett symbolskikt](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Lägga till ett bubbelskikt](map-add-bubble-layer-android.md)
