---
title: Lägga till kontroller i en Android-karta | Microsoft Azure Maps
description: Så här lägger du till zoomnings kontroll, vinkel kontroll, rotera kontroll och en stil väljare till en karta i Microsoft Azure mappar Android SDK.
author: rbrundritt
ms.author: richbrun
ms.date: 02/19/2021
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 8224192ed0d13af2ff6ac60aac5aa928589ff01a
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102055102"
---
# <a name="add-controls-to-a-map-android-sdk"></a>Lägga till kontroller till en karta (Android SDK)

Den här artikeln visar hur du lägger till UI-kontroller till kartan.

## <a name="add-zoom-control"></a>Lägg till zoomkontrollen

En zoomnings kontroll lägger till knappar för att zooma in och ut kartan. Följande kod exempel skapar en instans av `ZoomControl` klassen och lägger till den i en karta.

```java
map.controls.add(new ZoomControl());
```

Skärm bilden nedan är en zoomnings kontroll som har lästs in på en karta.

![Zoomnings kontrollen har lagts till i kartan](media/map-add-controls-android/android-zoom-control.jpg)

## <a name="add-pitch-control"></a>Lägg till vinkel kontroll

En lutnings kontroll lägger till knappar för att luta bredden för att mappa i förhållande till horisonten. Följande kod exempel skapar en instans av `PitchControl` klassen och lägger till den i en karta.

```java
//Construct a pitch control and add it to the map.
map.controls.add(new PitchControl());
```

Skärm bilden nedan är en bredd kontroll som läses in på en karta.

![Kanna-kontroll har lagts till i kartan](media/map-add-controls-android/android-pitch-control.jpg)

## <a name="add-compass-control"></a>Lägg till kompass kontroll

En kompass-kontroll lägger till en knapp för att rotera kartan. Följande kod exempel skapar en instans av `CompassControl` klassen och lägger till den i en karta.

```java
//Construct a compass control and add it to the map.
map.controls.add(new CompassControl());
```

Skärm bilden nedan är en kompass kontroll som har lästs in på en karta.

![Kompass kontroll tillagd till karta](media/map-add-controls-android/android-compass-control.jpg)

## <a name="add-traffic-control"></a>Lägg till trafik kontroll

En trafik kontroll lägger till en knapp för att växla synlighet för trafik data på kartan. Följande kod exempel skapar en instans av `TrafficControl` klassen och lägger till den i en karta.

```java
//Construct a traffic control and add it to the map.
map.controls.add(new TrafficControl());
```

Skärm bilden nedan är en trafik kontroll som läses in på en karta.

![Trafik kontroll har lagts till i kartan](media/map-add-controls-android/android-traffic-control.jpg)

## <a name="a-map-with-all-controls"></a>En karta med alla kontroller

Flera kontroller kan placeras i en matris och läggas till i kartan samtidigt och placeras i samma område av kartan för att förenkla utvecklingen. Följande lägger till standard navigerings kontrollerna till kartan med den här metoden.

```java
map.controls.add(
    new Control[]{
        new ZoomControl(),
        new CompassControl(),
        new PitchControl(),
        new TrafficControl()
    }
);
```

Skärm bilden nedan visar alla kontroller som lästs in på en karta. Observera att den ordning de läggs till i kartan, är den ordning som de kommer att visas.

![Alla kontroller har lagts till i kartan](media/map-add-controls-android/android-all-controls.jpg)

## <a name="next-steps"></a>Nästa steg

Se följande artiklar för fler kod exempel som du kan lägga till i dina kartor:

> [!div class="nextstepaction"]
> [Lägga till ett symbolskikt](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Lägga till ett bubbelskikt](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Lägg till ett linjeskikt](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Lägg till ett polygonskikt](how-to-add-shapes-to-android-map.md)
