---
title: Hantera kart händelser i Android Maps | Microsoft Azure Maps
description: Lär dig vilka händelser som utlöses när användarna interagerar med Maps. Visa en lista över alla mappnings händelser som stöds. Se hur du använder Azure Maps Android SDK för att hantera händelser.
author: rbrundritt
ms.author: richbrun
ms.date: 12/08/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: cpendle
ms.openlocfilehash: 818d33947fa079a130c3009a34dcb9b72ad52f91
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681668"
---
# <a name="interact-with-the-map-android-sdk"></a>Interagera med kartan (Android SDK)

Den här artikeln visar hur du använder Maps Events Manager.

## <a name="interact-with-the-map"></a>Interagera med kartan

Kartan hanterar alla händelser via dess `events` egenskap. I följande tabell visas alla mappnings händelser som stöds.

| Händelse                  | Händelse hanterarens format | Beskrivning |
|------------------------|----------------------|-------------|
| `OnCameraIdle`         | `()`                 | <p>Utlöses efter att den sista bild rutan renderades innan kartan övergår i tillståndet "inaktive":<ul><li>Ingen kamera över gång pågår.</li><li>Alla begärda paneler har lästs in.</li><li>Alla animeringar av tona/över gångar har slutförts.</li></ul></p> |
| `OnCameraMove`         | `()`                 | Utlöses upprepade gånger under en animerad över gång från en vy till en annan, som ett resultat av en användar interaktion eller metoder. |
| `OnCameraMoveCanceled` | `()`                 | Utlöses när en flyttnings förfrågan till kameran har avbrutits. |
| `OnCameraMoveStarted`  | `(int reason)`       | Utlöses precis innan kartan påbörjar en över gång från en vy till en annan, som ett resultat av en användar interaktion eller metoder. `reason`Argumentet för händelse lyssnaren returnerar ett heltals värde som innehåller information om hur kamera rörelsen initierades. Följande är en lista över möjliga orsaker:<ul><li>1: gest</li><li>2: Developer-animering</li><li>3: API-animering</li></ul>   |
| `OnClick`              | `(double lat, double lon)` | Utlöses när kartan trycks ned och släpps på samma plats på kartan. |
| `OnFeatureClick`       | `(List<Feature>)`    | Utlöses när kartan trycks ned och släpps på samma ställe i en funktion.  |
| `OnLongClick`          | `(double lat, double lon)` | Utlöses när kartan trycks ned, hålls i ett ögonblick och släpps sedan på samma plats på kartan. |
| `OnLongFeatureClick `  | `(List<Feature>)`    | Utlöses när kartan trycks ned, hålls i ett ögonblick och släpps sedan på samma ställe i en funktion. |
| `OnReady`              | `(AzureMap map)`     | Utlöses när kartan ursprungligen läses in eller när appens orientering ändras och de lägsta obligatoriska kart resurserna läses in och kartan är klar att program mässigt interagera med. |

Följande kod visar hur du lägger till `OnClick` -, `OnFeatureClick` -och- `OnCameraMove` händelser till kartan.

```java
map.events.add((OnClick) (lat, lon) -> {
    //Map clicked.
});

map.events.add((OnFeatureClick) (features) -> {
    //Feature clicked.
});

map.events.add((OnCameraMove) () -> {
    //Map camera moved.
});
```

Mer information finns i Översikt över [kart](how-to-use-android-map-control-library.md#navigating-the-map) dokumentationen om hur du interagerar med kartan och utlöser händelser.

## <a name="scope-feature-events-to-layer"></a>Omfattnings funktions händelser till lager

När du lägger till `OnFeatureClick` eller `OnLongFeatureClick` -händelserna i kartan kan ett lager-ID skickas som en andra parameter. När ett lager-ID skickas i, utlöses händelsen bara om händelsen inträffar på det lagret. Händelser som är begränsade till lager stöds av symbol-, bubbeldiagram-, linje-och polygonens lager.

```java
//Create a data source.
DataSource source = new DataSource();
map.sources.add(source);

//Add data to the data source.
source.add(Point.fromLngLat(0, 0));

//Create a layer and add it to the map.
BubbleLayer layer = new BubbleLayer(source);
map.layers.add(layer);

//Add a feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnFeatureClick) (features) -> {
    //One or more features clicked.
}, layer.getId());

//Add a long feature click event to the map and pass the layer ID to limit the event to the specified layer.
map.events.add((OnLongFeatureClick) (features) -> {
    //One or more features long clicked.
}, layer.getId());
```

## <a name="next-steps"></a>Nästa steg

I följande artiklar finns fullständiga kod exempel:

> [!div class="nextstepaction"]
> [Navigera i kartan](how-to-use-android-map-control-library.md#navigating-the-map)

> [!div class="nextstepaction"]
> [Lägga till ett symbolskikt](how-to-add-symbol-to-android-map.md)

> [!div class="nextstepaction"]
> [Lägga till ett bubbelskikt](map-add-bubble-layer-android.md)

> [!div class="nextstepaction"]
> [Lägg till ett linjeskikt](android-map-add-line-layer.md)

> [!div class="nextstepaction"]
> [Lägg till ett polygonskikt](how-to-add-shapes-to-android-map.md)
