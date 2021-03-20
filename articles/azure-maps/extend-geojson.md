---
title: Utökat interjson-Geometries | Microsoft Azure Maps
description: Lär dig hur Azure Maps utökar den geometriska JSON-specifikationen för att inkludera ytterligare geometriska former. Visa exempel som konfigurerar cirklar och rektanglar som ska användas i Maps.
author: sataneja
ms.author: sataneja
ms.date: 05/17/2018
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: ''
ms.openlocfilehash: 87a5dec2582796c6703bfb37b76e26e53a1192c0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92895791"
---
# <a name="extended-geojson-geometries"></a>Utökat interjson-Geometries

Azure Maps innehåller en lista med kraftfulla API: er för att söka i och efter geografiska funktioner. Dessa API: er följer standarden för geografi- [JSON][1] som representerar geografiska funktioner.  

Den [INTERjson-specifikationen][1] stöder endast följande Geometries:

* GeometryCollection
* Lin Est ring
* MultiLineString
* MultiPoint
* MultiPolygon
* Pekaren
* Polygonlasso

Vissa Azure Maps API: er accepterar Geometries som inte är en del av den [INTERjson-specifikationen][1]. Till exempel accepterar [search i Geometry](/rest/api/maps/search/postsearchinsidegeometry) API cirkel och polygoner.

Den här artikeln innehåller en detaljerad förklaring av hur Azure Maps utökar den [INTERjson-specifikationen][1] som representerar vissa Geometries.

## <a name="circle"></a>Cirkel

`Circle`Geometrin stöds inte av den geometriska [JSON-specifikationen][1]. Vi använder ett `GeoJSON Point Feature` objekt för att representera en cirkel.

En `Circle` geometri som representeras med hjälp av `GeoJSON Feature` objektet __måste__ innehålla följande koordinater och egenskaper:

- Centrera

    Cirkelns mitt punkt representeras med ett `GeoJSON Point` objekt.

- Radius

    Cirkelns `radius` Egenskaper representeras med hjälp av `GeoJSON Feature` egenskaperna. RADIUS-värdet är i _meter_ och måste vara av typen `double` .

- SubType

    Cirkel geometrin måste också innehålla `subType` egenskapen. Den här egenskapen måste vara en del av `GeoJSON Feature` egenskaperna och dess värde måste vara en _cirkel_

#### <a name="example"></a>Exempel

Så här kommer du att representera en cirkel med ett `GeoJSON Feature` objekt. Nu ska vi centrera cirkeln vid latitud: 47,639754 och longitud:-122,126986 och tilldela den en radie som är lika med 100 meter:

```json            
{
    "type": "Feature",
    "geometry": {
        "type": "Point",
        "coordinates": [-122.126986, 47.639754]
    },
    "properties": {
        "subType": "Circle",
        "radius": 100
    }
}          
```

## <a name="rectangle"></a>Rektangel

`Rectangle`Geometrin stöds inte av den geometriska [JSON-specifikationen][1]. Vi använder ett `GeoJSON Polygon Feature` objekt för att representera en rektangel. Tillägget Rectangle används främst av Web SDK: s verktyg för rit verktyg.

En `Rectangle` geometri som representeras med hjälp av `GeoJSON Polygon Feature` objektet __måste__ innehålla följande koordinater och egenskaper:

- Hörn

    Rektangelns hörn visas med koordinaterna för ett `GeoJSON Polygon` objekt. Det måste finnas fem koordinater, en för varje hörn. Och en femte koordinat som är samma som den första koordinaten för att stänga polygonen. Det förutsätts att dessa koordinater stämmer och att utvecklaren kan rotera dem som de vill.

- SubType

    Rektangelns geometri måste också innehålla `subType` egenskapen. Den här egenskapen måste vara en del av `GeoJSON Feature` egenskaperna och dess värde ska vara en _rektangel_

### <a name="example"></a>Exempel

```json
{
    "type": "Feature",
    "geometry": {
        "type": "Polygon",
        "coordinates": [[[5,25],[14,25],[14,29],[5,29],[5,25]]]
    },
    "properties": {
        "subType": "Rectangle"
    }
}

```
## <a name="next-steps"></a>Nästa steg

Läs mer om data i multijson i Azure Maps:

> [!div class="nextstepaction"]
> [Polystängsel, geografiskt JSON-format](geofence-geojson.md)

Granska ord listan med vanliga tekniska villkor som är associerade med Azure Maps-och plats informations program:

> [!div class="nextstepaction"]
> [Azure Maps-ordlista](glossary.md)

[1]: https://tools.ietf.org/html/rfc7946