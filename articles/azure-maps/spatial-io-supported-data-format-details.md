---
title: Information om data format som stöds | Microsoft Azure Maps
description: Lär dig hur avgränsade spatialdata parsas i den spatiala IO-modulen.
author: anastasia-ms
ms.author: v-stharr
ms.date: 03/03/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 7227813f607ca18ee50f503a30b290414f333e21
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91310177"
---
# <a name="supported-data-format-details"></a>Information om dataformat som stöds

Den här artikeln innehåller information om Läs-och skriv stöd för alla XML-taggar och Well-Known text geometri typer. Den innehåller också information om hur avgränsade spatialdata tolkas i den spatiala IO-modulen.

## <a name="supported-xml-namespaces"></a>XML-namnområden som stöds

Den spatiala IO-modulen stöder XML-taggar från följande namn rymder.

| Namnområdesprefix | Namnområdes-URI   | Kommentarer                                                                    |
|:------------------|:-----------------|:----------------------------------------|
| `atom`           | `http://www.w3.org/2005/Atom`   |                                         |
| `geo`            | `http://www.w3.org/2003/01/geo/wgs84_pos#`  | Skrivskyddad support i GeoRSS-filer.           |
| `georss`         | `http://www.georss.org/georss`  |                                                |
| `geourl`         | `http://geourl.org/rss/module/` | Skrivskyddad support i GeoRSS-filer.                       |
| `gml`            | `http://www.opengis.net/gml`    |                                                        |
| `gpx`            | `http://www.topografix.com/GPX/1/1` |                                                   |
| `gpxx`           | `http://www.garmin.com/xmlschemas/GpxExtensions/v3` | Skrivskyddad support i GPX-filer. Parsar och använder DisplayColor. Alla andra egenskaper som har lagts till i formens metadata. |
| `gpx_style`      | `http://www.topografix.com/GPX/gpx_style/0/2`      | Stöds i GPX-filer. Använder linje färg. |
| `gx`             | `http://www.google.com/kml/ext/2.2` |                                                      |
| `kml`            | `http://www.opengis.net/kml/2.2`    |                                                      |
| `rss`            |                                 | Skrivskyddad. GeoRSS skriver med Atom-format.              |

## <a name="supported-xml-elements"></a>XML-element som stöds

Den spatiala IO-modulen har stöd för följande XML-element. Alla XML-taggar som inte stöds kommer att konverteras till ett JSON-objekt. Sedan kommer varje tagg att läggas till som en egenskap i `properties` fältet för den överordnade formen eller lagret.

### <a name="kml-elements"></a>KML-element

Den spatiala IO-modulen har stöd för följande KML-element.

| Elementnamn         | Läsa    | Skriva   | Kommentarer                                                                                                                      |
|----------------------|---------|---------|----------------------------------------------------------------------------------------------------------------------------|
| `address`            | signatur | ja     | Objektet är parsat men används inte för placering av form.                                                                    |
| `AddressDetails`     | signatur | nej      | Objektet är parsat men används inte för placering av form.                                                                    |
| `atom:author`        | ja     | ja     |                                                                                                                            |
| `atom:link`          | ja     | ja     |                                                                                                                            |
| `atom:name`          | ja     | ja     |                                                                                                                            |
| `BalloonStyle`       | signatur | signatur | `displayMode` stöds inte. Konverterat till en `PopupTemplate` . Om du vill skriva lägger du till en `popupTemplate` egenskap som egenskap för den funktion som du vill skriva den för. |
| `begin`              | ja     | ja     |                                                                                                                            |
| `color`              | ja     | ja     | Innehåller `#AABBGGRR` och `#BBGGRR` . Parsas till en CSS-färgsträng                                                           |
| `colorMode`          | ja     | nej      |                                                                                                                            |
| `coordinates`        | ja     | ja     |                                                                                                                            |
| `Data`               | ja     | ja     |                                                                                                                            |
| `description`        | ja     | ja     |                                                                                                                            |
| `displayName`        | ja     | ja     |                                                                                                                            |
| `Document`           | ja     | ja     |                                                                                                                            |
| `drawOrder`          | signatur | nej      | Läses för mark överlägg och används för att sortera dem. 
| `east`               | ja     | ja     |                                                                                                                            |
| `end`                | ja     | ja     |                                                                                                                            |
| `ExtendedData`       | ja     | ja     | Stöder `Data` `SimpleData` `Schema` depositions-, eller-och entitets-ersättningar för formuläret `$[dataName]` .                      |
| `extrude`            | signatur | signatur | Stöds endast för polygoner. Multigeometrier som har polygoner med olika höjd kommer att delas upp i enskilda funktioner. Linje format stöds inte. Polygoner med en höjd på 0 kommer att återges som en platt polygon. Vid läsning läggs den första koordinatens höjd i den yttre ringen till som en höjd-egenskap för polygonen. Sedan kommer den första koordinatens höjd att användas för att rendera polygonen på kartan. |
| `fill`               | ja     | ja     |                                                                                                                            |
| `Folder`             | ja     | ja     |                                                                                                                            |
| `GroundOverlay`      | ja     | ja     | `color` stöds inte                                                                                                   |
| `heading`            | signatur | nej      | Parsas men återges inte av `SimpleDataLayer` . Skriver endast om data lagras i formens egenskap.                 |
| `hotSpot`            | ja     | signatur | Skriver endast om data lagras i formens egenskap. Enheter anges bara som "bild punkter".                         |
| `href`               | ja     | ja     |                                                                                                                            |
| `Icon`               | signatur | signatur | Parsas men återges inte av `SimpleDataLayer` . Skriver bara ikon egenskapen för formen om den innehåller en URI-data. `href`Stöds endast. |
| `IconStyle`          | signatur | signatur | `icon`, `heading` , `colorMode` och `hotspots` värden parsas, men de återges inte av `SimpleDataLayer`         |
| `innerBoundaryIs`    | ja     | ja     |                                                                                                                            |
| `kml`                | ja     | ja     |                                                                                                                            |
| `LabelStyle`         | nej      | nej      |                                                                                                                            |
| `LatLonBox`          | ja     | ja     |                                                                                                                            |
| `gx:LatLonQuad`      | ja     | ja     |                                                                                                                            |
| `LinearRing`         | ja     | ja     |                                                                                                                            |
| `LineString`         | ja     | ja     |                                                                                                                            |
| `LineStyle`          | ja     | ja     | `colorMode` stöds inte.                                                                                         |
| `Link`               | ja     | nej      | Endast `href` egenskapen stöds för nätverks länkar.                                                                   |
| `MultiGeometry`      | signatur | signatur | Kan delas upp i enskilda funktioner när de läses.                                                                     |
| `name`               | ja     | ja     |                                                                                                                            |
| `NetworkLink`        | ja     | nej      | Länkar måste finnas i samma domän som dokumentet.                                                                  |
| `NetworkLinkControl` | nej      | nej      |                                                                                                                            |
| `north`              | ja     | ja     |                                                                                                                            |
| `open`               | ja     | ja     |                                                                                                                            |
| `outerBoundaryIs`    | ja     | ja     |                                                                                                                            |
| `outline`            | ja     | ja     |                                                                                                                            |
| `overlayXY`          | nej      | nej      |                                                                                                                            |
| `Pair`               | signatur | nej      | Endast `normal` formatet i `StyleMap` stöds. `highlight` stöds inte.                                   |
| `phoneNumber`        | ja     | ja     |                                                                                                                            |
| `PhotoOverlay`       | nej      | nej      |                                                                                                                            |
| `Placemark`          | ja     | ja     |                                                                                                                            |
| `Point`              | ja     | ja     |                                                                                                                            |
| `Polygon`            | ja     | ja     |                                                                                                                            |
| `PolyStyle`          | ja     | ja     |                                                                                                                            |
| `Region`             | signatur | signatur | `LatLongBox` stöds på dokument nivå.                                                                      |
| `rotation`           | nej      | nej      |                                                                                                                            |
| `rotationXY`         | nej      | nej      |                                                                                                                            |
| `scale`              | nej      | nej      |                                                                                                                            |
| `Schema`             | ja     | ja     |                                                                                                                            |
| `SchemaData`         | ja     | ja     |                                                                                                                            |
| `schemaUrl`          | signatur | ja     | Stöder inte inläsning av format från externa dokument som inte ingår i en KMZ.                             |
| `ScreenOverlay`      | nej      | nej      |                                                                                                                            |
| `screenXY`           | nej      | nej      |                                                                                                                            |
| `SimpleData`         | ja     | ja     |                                                                                                                            |
| `SimpleField`        | ja     | ja     |                                                                                                                            |
| `size`               | nej      | nej      |                                                                                                                            |
| `Snippet`            | signatur | signatur | `maxLines` attributet ignoreras.                                                                                  |
| `south`              | ja     | ja     |                                                                                                                            |
| `Style`              | ja     | ja     |                                                                                                                            |
| `StyleMap`           | signatur | nej      | Endast normal formatet i `StyleMap` stöds.                                                                        |
| `styleUrl`           | signatur | ja     | URL: er för externa format stöds inte.                                                                         |
| `text`               | ja     | ja     | Ersättning av `$[geDirections]` stöds inte                                                                          |
| `textColor`          | ja     | ja     |                                                                                                                            |
| `TimeSpan`           | ja     | ja     |                                                                                                                            |
| `TimeStamp`          | ja     | ja     |                                                                                                                            |
| `value`              | ja     | ja     |                                                                                                                            |
| `viewRefreshMode`    | signatur | nej      |  Om du pekar på en WMS-tjänst `onStop` stöds endast för mark överlägg. Kommer `BBOX=[bboxWest],[bboxSouth],[bboxEast],[bboxNorth]` att läggas till i URL: en och uppdateras när kartan flyttas.  |
| `visibility`         | ja     | ja     |                                                                                                                            |
| `west`               | ja     | ja     |                                                                                                                            |
| `when`               | ja     | ja     |                                                                                                                            |
| `width`              | ja     | ja     |                                                                                                                            |

### <a name="georss-elements"></a>GeoRSS-element

Den spatiala IO-modulen har stöd för följande GeoRSS-element.

| Elementnamn             | Läsa    | Skriva | Kommentarer                                                                                          |
|--------------------------|---------|-------|------------------------------------------------------------------------------------------------|
| `atom:author`            | ja     | ja   |                                                                                                |
| `atom:category`          | ja     | ja   |                                                                                                |
| `atom:content`           | ja     | ja   |                                                                                                |
| `atom:contributor`       | ja     | ja   |                                                                                                |
| `atom:email`             | ja     | ja   |                                                                                                |
| `atom:entry`             | ja     | ja   |                                                                                                |
| `atom:feed`              | ja     | ja   |                                                                                                |
| `atom:icon`              | ja     | ja   |                                                                                                |
| `atom:id`                | ja     | ja   |                                                                                                |
| `atom:link`              | ja     | ja   |                                                                                                |
| `atom:logo`              | ja     | ja   |                                                                                                |
| `atom:name`              | ja     | ja   |                                                                                                |
| `atom:published`         | ja     | ja   |                                                                                                |
| `atom:rights`            | ja     | ja   |                                                                                                |
| `atom:source`            | ja     | ja   |                                                                                                |
| `atom:subtitle`          | ja     | ja   |                                                                                                |
| `atom:summary`           | ja     | ja   |                                                                                                |
| `atom:title`             | ja     | ja   |                                                                                                |
| `atom:updated`           | ja     | ja   |                                                                                                |
| `atom:uri`               | ja     | ja   |                                                                                                |
| `geo:lat`                | ja     | nej    | Skrivet som en `georss:point` .                                                                   |
| `geo:lon`                | ja     | nej    | Skrivet som en `georss:point` .                                                                   |
| `geo:long`               | ja     | nej    | Skrivet som en `georss:point` .                                                                   |
| `georss:box`             | ja     | nej    | Läs som en polygon och få `subType` egenskapen "rektangel"                                |
| `georss:circle`          | ja     | ja   |                                                                                                |
| `georss:elev`            | ja     | ja   |                                                                                                |
| `georss:featurename`     | ja     | ja   |                                                                                                |
| `georss:featuretypetag`  | ja     | ja   |                                                                                                |
| `georss:floor`           | ja     | ja   |                                                                                                |
| `georss:line`            | ja     | ja   |                                                                                                |
| `georss:point`           | ja     | ja   |                                                                                                |
| `georss:polygon`         | ja     | ja   |                                                                                                |
| `georss:radius`          | ja     | ja   |                                                                                                |
| `georss:relationshiptag` | ja     | ja   |                                                                                                |
| `georss:where`           | ja     | ja   |                                                                                                |
| `geourl:latitude`        | ja     | nej    | Skrivet som en `georss:point` .                                                                   |
| `geourl:longitude`       | ja     | nej    | Skrivet som en `georss:point` .                                                                   |
| `position`               | ja     | nej    | Vissa XML-matningar radbryter GML med en positions tagg i stället för att figursätta dem till en `georss:where` tagg. Kommer att läsa den här taggen, men skriver med hjälp av en `georss:where` tagg. |
| `rss`                    | ja     | nej    | GeoRSS skrivet i ATOM-format.                                                                 |
| `rss:author`             | ja     | signatur | Skriven som `atom:author` .                                                                 |
| `rss:category`           | ja     | signatur | Skriven som `atom:category` .                                                               |
| `rss:channel`            | ja     | nej    |                                                                                                |
| `rss:cloud`              | ja     | nej    |                                                                                                |
| `rss:comments`           | ja     | nej    |                                                                                                |
| `rss:copyright`          | ja     | signatur | Skrivet som en `atom:rights` IF-form har `rights` `properties` redan en egenskap.       |
| `rss:description`        | ja     | signatur | Skrivet som en `atom:content` IF-form har `content` `properties` redan en egenskap.      |
| `rss:docs`               | ja     | nej    |                                                                                                |
| `rss:enclosure`          | ja     | nej    |                                                                                                |
| `rss:generator`          | ja     | nej    |                                                                                                |
| `rss:guid`               | ja     | signatur | Skrivet som en `atom:id` IF-form har `id` `properties` redan en egenskap.         |
| `rss:image`              | ja     | signatur | Skrivet som en `atom:logo` IF-form har `logo` `properties` redan en egenskap.      |
| `rss:item`               | ja     | signatur | Skriven som `atom:entry` .                                                                  |
| `rss:language`           | ja     | nej    |                                                                                                |
| `rss:lastBuildDate`      | ja     | signatur | Skrivet som en `atom:updated` IF-form har `updated` `properties` redan en egenskap.     |
| `rss:link`               | ja     | signatur | Skriven som `atom:link` .                                                                   |
| `rss:managingEditor`     | ja     | signatur | Skriven som `atom:contributor` .                                                            |
| `rss:pubDate`            | ja     | signatur | Skrivet som en `atom:published` IF-form har `published` `properties` redan en egenskap.  |
| `rss:rating`             | ja     | nej    |                                                                                                |
| `rss:skipDays`           | ja     | nej    |                                                                                                |
| `rss:skipHours`          | ja     | nej    |                                                                                                |
| `rss:source`             | ja     | signatur | Skriven som en `atom:source` innehåller en `atom:link` .                                       |
| `rss:textInput`          | ja     | nej    |                                                                                                |
| `rss:title`              | ja     | signatur | Skriven som `atom:title` .                                                                  |
| `rss:ttl`                | ja     | nej    |                                                                                                |
| `rss:webMaster`          | ja     | nej    |                                                                                                |

### <a name="gml-elements"></a>GML-element

Den spatiala IO-modulen har stöd för följande GML-element. 

| Elementnamn            | Läsa | Skriva | Kommentarer                                                                                  |
|-------------------------|------|-------|----------------------------------------------------------------------------------------|
| `gml:coordinates`       | ja  | nej    | Skrivs som `gml:posList` .                                                              |
| `gml:curveMember`       | ja  | nej    |                                                                                        |
| `gml:curveMembers`      | ja  | nej    |                                                                                        |
| `gml:Box`               | ja  | nej    | Skrivs som `gml:Envelope` .                                                             |
| `gml:description`       | ja  | ja   |                                                                                        |
| `gml:Envelope`          | ja  | ja   |                                                                                        |
| `gml:exterior`          | ja  | ja   |                                                                                        |
| `gml:Feature`           | ja  | nej    | Skriven som en form.                                                                    |
| `gml:FeatureCollection` | ja  | nej    | Skrivet som en geometri samling.                                                      |
| `gml:featureMember`     | ja  | nej    | Skrivet som en geometri samling.                                                      |
| `gml:geometry`          | ja  | nej    | Skriven som en form.                                                                    |
| `gml:geometryMember`    | ja  | ja   |                                                                                        |
| `gml:geometryMembers`   | ja  | ja   |                                                                                        |
| `gml:identifier`        | ja  | ja   |                                                                                        |
| `gml:innerBoundaryIs`   | ja  | nej    | Skrivet med `gml.interior` .                                                          |
| `gml:interior`          | ja  | ja   |                                                                                        |
| `gml:LinearRing`        | ja  | ja   |                                                                                        |
| `gml:LineString`        | ja  | ja   |                                                                                        |
| `gml:lineStringMember`  | ja  | ja   |                                                                                        |
| `gml:lineStringMembers` | ja  | nej    |                                                                                        |
| `gml:MultiCurve`        | ja  | nej    | Läser bara `gml:LineString` medlemmar. Skrivet som `gml.MultiLineString`                  |
| `gml:MultiGeometry`     | signatur  | signatur   | Läs bara som en FeatureCollection.                                              |
| `gml:MultiLineString`   | ja  | ja   |                                                                                        |
| `gml:MultiPoint`        | ja  | ja   |                                                                                        |
| `gml:MultiPolygon`      | ja  | ja   |                                                                                        |
| `gml:MultiSurface`      | ja  | nej    | Läser bara `gml:Polygon` medlemmar. Skrivet som `gml.MultiPolygon`                        |
| `gml:name`              | ja  | ja   |                                                                                        |
| `gml:outerBoundaryIs`   | ja  | nej    | Skrivet med `gml.exterior` .                                                          |
| `gml:Point`             | ja  | ja   |                                                                                        |
| `gml:pointMember`       | ja  | ja   |                                                                                        |
| `gml:pointMembers`      | ja  | nej    |                                                                                        |
| `gml:Polygon`           | ja  | ja   |                                                                                        |
| `gml:polygonMember`     | ja  | ja   |                                                                                        |
| `gml:polygonMembers`    | ja  | nej    |                                                                                        |
| `gml:pos`               | ja  | ja   |                                                                                        |
| `gml:posList`           | ja  | ja   |                                                                                        |
| `gml:surfaceMember`     | ja  | ja   |                                                                                        |

#### <a name="additional-notes"></a>Ytterligare information

- Medlems element kommer att sökas efter en geometri som kan vara täckt inom underordnade element. Den här Sök åtgärden krävs eftersom många XML-format som sträcker sig från GML inte kan placera en geometri som ett direkt underordnat objekt till ett medlems element.
- `srsName` stöds delvis för WGS84-koordinater och följande koder:[EPSG: 4326](https://epsg.io/4326)) och webb Mercator ([EPSG: 3857](https://epsg.io/3857) eller någon av dess alternativa koder. Alla andra koordinatsystem kommer att tolkas som WGS84.
- Om inget annat anges vid läsning av ett XML-flöde bestäms axel ordningen utifrån tips i XML-flödet. En preferens anges för axel ordningen "latitud, longitud".
- Om inte ett anpassat GML-namnområde har angetts för egenskaperna vid skrivning till en GML-fil, kommer ytterligare egenskaps information inte att läggas till.

### <a name="gpx-elements"></a>GPX-element

Den spatiala IO-modulen har stöd för följande GPX-element.

| Elementnamn             | Läsa    | Skriva   | Kommentarer                                                                                       |
|--------------------------|---------|---------|---------------------------------------------------------------------------------------------|
| `gpx:ageofdgpsdata`      | ja     | ja     |                                                                                             |
| `gpx:author`             | ja     | ja     |                                                                                             |
| `gpx:bounds`             | ja     | ja     | Konverteras till en LocationRect när den läses.                                                    |
| `gpx:cmt`                | ja     | ja     |                                                                                             |
| `gpx:copyright`          | ja     | ja     |                                                                                             |
| `gpx:desc`               | ja     | ja     | Kopieras till en beskrivnings egenskap när den läses för att justeras mot andra XML-format.               |
| `gpx:dgpsid`             | ja     | ja     |                                                                                             |
| `gpx:ele`                | ja     | ja     |                                                                                             |
| `gpx:extensions`         | signatur | signatur | Vid läsning extraheras format information. Alla andra tillägg kommer att förenklas till ett enkelt JSON-objekt. Endast form stils information skrivs. |
| `gpx:geoidheight`        | ja     | ja     |                                                                                             |
| `gpx:gpx`                | ja     | ja     |                                                                                             |
| `gpx:hdop`               | ja     | ja     |                                                                                             |
| `gpx:link`               | ja     | ja     |                                                                                             |
| `gpx:magvar`             | ja     | ja     |                                                                                             |
| `gpx:metadata`           | ja     | ja     |                                                                                             |
| `gpx:name`               | ja     | ja     |                                                                                             |
| `gpx:pdop`               | ja     | ja     |                                                                                             |
| `gpx:rte`                | ja     | ja     |                                                                                             |
| `gpx:rtept`              | ja     | ja     |                                                                                             |
| `gpx:sat`                | ja     | ja     |                                                                                             |
| `gpx:src`                | ja     | ja     |                                                                                             |
| `gpx:sym`                | ja     | ja     | Värdet fångas, men det används inte för att ändra kartnål-ikonen.                               |
| `gpx:text`               | ja     | ja     |                                                                                             |
| `gpx:time`               | ja     | ja     |                                                                                             |
| `gpx:trk`                | ja     | ja     |                                                                                             |
| `gpx:trkpt`              | ja     | ja     |                                                                                             |
| `gpx:trkseg`             | ja     | ja     |                                                                                             |
| `gpx:type`               | ja     | ja     |                                                                                             |
| `gpx:vdop`               | ja     | ja     |                                                                                             |
| `gpx:wpt`                | ja     | ja     |                                                                                             |
| `gpx_style:color`        | ja     | ja     |                                                                                             |
| `gpx_style:line`         | signatur | signatur | `color`, `opacity` , `width` , `lineCap` stöds.                                           |
| `gpx_style:opacity`      | ja     | ja     |                                                                                             |
| `gpx_style:width`        | ja     | ja     |                                                                                             |
| `gpxx:DisplayColor`      | ja     | nej      | Används för att ange färgen på en form. När du skriver `gpx_style:line` används färgen i stället.  |
| `gpxx:RouteExtension`    | signatur | nej      | Alla egenskaper läses in i `properties` . `DisplayColor`Används endast.                     |
| `gpxx:TrackExtension`    | signatur | nej      | Alla egenskaper läses in i `properties` . `DisplayColor`Används endast.                     |
| `gpxx:WaypointExtension` | signatur | nej      | Alla egenskaper läses in i `properties` . `DisplayColor`Används endast.                     |
| `gpx:keywords`           | ja     | ja     |                                                                                             |
| `gpx:fix`                | ja     | ja     |                                                                                             |

#### <a name="additional-notes"></a>Ytterligare information

Vid skrivning,

- Multipoäng delas upp i enskilda waypoints.
- Polygoner och multipolygoner skrivs som spår. 
  
## <a name="supported-well-known-text-geometry-types"></a>Well-Known text geometri typer som stöds

| Geometri typ | Läsa | Skriva |
|--------------|:----:|:-----:|
| PEKAREN | x | x |
| PUNKT Z | x | x | 
| PUNKT M | x | x<sup>[2]</sup> |
| PUNKT ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| Lin Est ring | x | x |
| LIN EST RING Z | x | x | 
| LIN EST RING M | x | x<sup>[2]</sup> |
| LIN EST RING ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| POLYGONLASSO | x | x |
| POLYGON Z | x | x |
| POLYGON M | x | x<sup>[2]</sup> |
| POLYGON ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| MULTIPOINT | x | x |
| MULTIPOINT Z | x | x | 
| MULTIPOINT M | x | x<sup>[2]</sup> |
| POMULTIPOINTINT ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| MULTILINESTRING | x | x |
| MULTILINESTRING Z | x | x | 
| MULTILINESTRING M | x | x<sup>[2]</sup> |
| MULTILINESTRING ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| MULTIPOLYGON | x | x |
| MULTIPOLYGON Z | x | x | 
| MULTIPOLYGON M | x | x<sup>[2]</sup> |
| MULTIPOLYGON-ZM | x<sup>[1]</sup><sup>[2]</sup> | | 
| GEOMETRYCOLLECTION | x | x |
| GEOMETRYCOLLECTION Z | x | x | 
| GEOMETRYCOLLECTION M | x | x<sup>[2]</sup> | 
| GEOMETRYCOLLECTION ZM | x<sup>[1]</sup><sup>[2]</sup> | x | 

\[1 \] endast Z-parameter samlas in och läggs till som ett tredje värde i position svärdet.

\[\]parametern 2 M har inte registrerats.

## <a name="delimited-spatial-data-support"></a>Stöd för avgränsad spatial data

Avgränsade spatialdata, till exempel CSV-filer (kommaavgränsade filer) har ofta kolumner som innehåller spatialdata. Det kan till exempel finnas kolumner som innehåller information om latitud och longitud. I Well-Known text format kan det finnas en kolumn som innehåller data för spatial geometri.

### <a name="spatial-data-column-detection"></a>Identifiering av kolumn för avstånds data

När du läser en avgränsad fil som innehåller spatialdata kommer rubriken att analyseras för att avgöra vilka kolumner som innehåller plats fält. Om rubriken innehåller typ information används den för att omvandla cellvärdena till lämplig typ. Om du inte anger något sidhuvud kommer den första raden att analyseras och användas för att generera ett sidhuvud. När den första raden analyseras utförs en kontroll för att matcha kolumn namn med följande namn på ett skift läges okänsligt sätt. Ordningen på namnen är prioritet, om det finns två eller fler namn i en fil.

#### <a name="latitude"></a>Latitud

- `latitude`
- `lat`
- `latdd`
- `lat_dd`
- `latitude83`
- `latdecdeg`
- `y`
- `ycenter`
- `point-y`

#### <a name="longitude"></a>Longitud

- `longitude`
- `lon`
- `lng`
- `long`
- `longdd`
- `long_dd`
- `longitude83`
- `longdecdeg`
- `x`
- `xcenter`
- `point-x`

#### <a name="elevation"></a>Höjning

- `elevation`
- `elv`
- `altitude`
- `alt`
- `z`

#### <a name="geography"></a>Geografi

Den första raden med data söks igenom efter strängar som har Well-Known text format. 

### <a name="delimited-data-column-types"></a>Avgränsade data kolumn typer

När du skannar rubrik raden kommer all typ information som finns i kolumn namnet att extraheras och användas för att omvandla cellerna i kolumnen. Här är ett exempel på ett kolumn namn som har ett typ värde: "ColumnName (typeName)". Följande Skift läges känsliga typ namn stöds:

#### <a name="numbers"></a>Tal

- EDM. Int64
- int
- long
- EDM. Double
- flyt
- double
- antal

#### <a name="booleans"></a>Booleska värden

- EDM. Boolean
- boolesk
- boolean

#### <a name="dates"></a>Datum

- EDM. DateTime
- date
- datetime

#### <a name="geography"></a>Geografi

- EDM. geography
- geography

#### <a name="strings"></a>Strängar

- EDM. String
- varchar
- text
- sträng

Om ingen typ information kan extraheras från rubriken och alternativet för dynamiskt Skriv aktive ras när du läser, kommer varje cell att analyseras individuellt för att avgöra vilken data typ som passar bäst för att omvandlas till.

## <a name="next-steps"></a>Nästa steg

Se följande artiklar för fler kod exempel som du kan lägga till i dina kartor:

[Läsa och skriva rumsliga data](spatial-io-read-write-spatial-data.md)
