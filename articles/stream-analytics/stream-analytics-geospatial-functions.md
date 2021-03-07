---
title: Introduktion till Azure Stream Analytics geospatiala funktioner
description: I den här artikeln beskrivs geospatiala funktioner som används i Azure Stream Analytics-jobb.
ms.service: stream-analytics
author: enkrumah
ms.author: ebnkruma
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: a5e65ddacfd4c168d4b97816e448951bacc2b534
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102438548"
---
# <a name="introduction-to-stream-analytics-geospatial-functions"></a>Introduktion till Stream Analytics geospatiala funktioner

Geospatiala funktioner i Azure Stream Analytics möjliggör analys i real tid på strömmande geospatiala data. Med bara några rader med kod kan du utveckla en lösning för produktions klass för komplexa scenarier. Dessa funktioner har stöd för alla well-typer och interjson-punkt, polygon och lin Est ring.

Exempel på scenarier som kan dra nytta av geospatiala funktioner är:

* Delning av delning
* Flottans hantering
* Tillgångar
* Geo-staket
* Telefon spårning över cell platser

Stream Analytics frågespråk har sju inbyggda geospatiala funktioner: **CreateLineString**, **CreatePoint**, **CreatePolygon**, **ST_DISTANCE**, **ST_OVERLAPS**, **ST_INTERSECTS** och **ST_WITHIN**.

## <a name="createlinestring"></a>CreateLineString

`CreateLineString`Funktionen accepterar punkter och returnerar en lin Est ring som kan ritas som en linje på en karta. Du måste ha minst två punkter för att skapa en lin Est ring. Lin Est ring punkterna kommer att anslutas i ordning.

Följande fråga används `CreateLineString` för att skapa en lin Est ring med tre punkter. Den första punkten skapas från strömmande indata, medan de andra två skapas manuellt.

```SQL 
SELECT  
     CreateLineString(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5))  
FROM input  
```  

### <a name="input-example"></a>Exempel på inmatade  
  
|latitude|Long|  
|--------------|---------------|  
|3.0|– 10,2|  
|– 87,33|20,2321|  
  
### <a name="output-example"></a>Exempel på utdata  

 {"typ": "lin Est ring", "koordinater": [[-10,2, 3,0], [10,0, 10,0], [10,5, 10,5]]}

 {"typ": "lin Est ring", "koordinater": [[20,2321,-87,33], [10,0, 10,0], [10,5, 10,5]]}

Mer information finns på [CreateLineString](/stream-analytics-query/createlinestring) -referensen.

## <a name="createpoint"></a>CreatePoint

`CreatePoint`Funktionen accepterar en latitud och en longitud och returnerar en subjson-punkt som kan ritas på en karta. Dina latitud och longitudar måste vara av typen **float** .

I följande exempel fråga används `CreatePoint` för att skapa en punkt med hjälp av latitud och longitudar från strömmande indata.

```SQL 
SELECT  
     CreatePoint(input.latitude, input.longitude)  
FROM input 
```  

### <a name="input-example"></a>Exempel på inmatade  
  
|latitude|Long|  
|--------------|---------------|  
|3.0|– 10,2|  
|– 87,33|20,2321|  
  
### <a name="output-example"></a>Exempel på utdata
  
 {"typ": "punkt", "koordinater": [-10,2, 3,0]}  
  
 {"typ": "punkt", "koordinater": [20,2321,-87,33]}  

Mer information finns på [CreatePoint](/stream-analytics-query/createpoint) -referensen.

## <a name="createpolygon"></a>CreatePolygon

`CreatePolygon`Funktionen accepterar punkter och returnerar en POLYjson-polygon. Ordningen på punkter måste följa höger ring orientering eller moturs. Föreställ dig att flytta från en punkt till en annan i den ordning som de deklarerades. Mitten av polygonen skulle vara till vänster hela tiden.

I följande exempel fråga används `CreatePolygon` för att skapa en polygon från tre punkter. De första två punkterna skapas manuellt och den sista punkten skapas från indata.

```SQL 
SELECT  
     CreatePolygon(CreatePoint(input.latitude, input.longitude), CreatePoint(10.0, 10.0), CreatePoint(10.5, 10.5), CreatePoint(input.latitude, input.longitude))  
FROM input  
```  

### <a name="input-example"></a>Exempel på inmatade  
  
|latitude|Long|  
|--------------|---------------|  
|3.0|– 10,2|  
|– 87,33|20,2321|  
  
### <a name="output-example"></a>Exempel på utdata  

 {"typ": "polygon", "koordinater": [[[-10,2, 3,0], [10,0, 10,0], [10,5, 10,5], [-10,2, 3,0]]]}
 
 {"typ": "polygon", "koordinater": [[[20,2321,-87,33], [10,0, 10,0], [10,5, 10,5], [20,2321,-87,33]]]}

Mer information finns på [CreatePolygon](/stream-analytics-query/createpolygon) -referensen.


## <a name="st_distance"></a>ST_DISTANCE
`ST_DISTANCE`Funktionen returnerar avståndet mellan två Geometries i meter. 

Följande fråga används `ST_DISTANCE` för att generera en händelse när en gas Station är mindre än 10 km från bilen.

```SQL
SELECT Cars.Location, Station.Location 
FROM Cars c  
JOIN Station s ON ST_DISTANCE(c.Location, s.Location) < 10 * 1000
```

Mer information finns på [ST_DISTANCE](/stream-analytics-query/st-distance) referens.

## <a name="st_overlaps"></a>ST_OVERLAPS
`ST_OVERLAPS`Funktionen Jämför två Geometries. Om Geometries överlappar varandra returnerar funktionen 1. Funktionen returnerar 0 om Geometries inte överlappar varandra. 

Följande fråga används `ST_OVERLAPS` för att generera en händelse när en byggnad är inom en möjlig överbelastnings zon.

```SQL
SELECT Building.Polygon, Building.Polygon 
FROM Building b 
JOIN Flooding f ON ST_OVERLAPS(b.Polygon, b.Polygon) 
```

I följande exempel fråga genereras en händelse när en storm är rubrik mot en bil.

```SQL
SELECT Cars.Location, Storm.Course
FROM Cars c, Storm s
JOIN Storm s ON ST_OVERLAPS(c.Location, s.Course)
```

Mer information finns på [ST_OVERLAPS](/stream-analytics-query/st-overlaps) referens.

## <a name="st_intersects"></a>ST_INTERSECTS
`ST_INTERSECTS`Funktionen Jämför två Geometries. Om Geometries korsar, returnerar funktionen 1. Funktionen returnerar 0 om Geometries inte överlappar varandra.

I följande exempel fråga används `ST_INTERSECTS` för att avgöra om en förberedelse väg överlappar en smuts väg.

```SQL 
SELECT  
     ST_INTERSECTS(input.pavedRoad, input.dirtRoad)  
FROM input  
```  

### <a name="input-example"></a>Exempel på inmatade  
  
|datacenterArea|stormArea|  
|--------------------|---------------|  
|{"typ": "lin Est ring", "koordinater": [[-10,0, 0,0], [0,0, 0,0], [10,0, 0,0]]}|{"typ": "lin Est ring", "koordinater": [[0,0, 10,0], [0,0, 0,0], [0,0,-10,0]]}|  
|{"typ": "lin Est ring", "koordinater": [[-10,0, 0,0], [0,0, 0,0], [10,0, 0,0]]}|{"typ": "lin Est ring", "koordinater": [[-10,0, 10,0], [0,0, 10,0], [10,0, 10,0]]}|  
  
### <a name="output-example"></a>Exempel på utdata  

 1  
  
 0  

Mer information finns på [ST_INTERSECTS](/stream-analytics-query/st-intersects) referens.

## <a name="st_within"></a>ST_WITHIN
`ST_WITHIN`Funktionen avgör om en geometri är i en annan geometri. Om den första finns i den sista, returnerar funktionen 1. Funktionen kommer att returnera 0 om den första geometrin inte finns i den sista.

I följande exempel fråga används `ST_WITHIN` för att avgöra om leverans destinations platsen är inom den aktuella lager polygonen.

```SQL 
SELECT  
     ST_WITHIN(input.deliveryDestination, input.warehouse)  
FROM input 
```  

### <a name="input-example"></a>Exempel på inmatade  
  
|deliveryDestination|lagerinleveransen|  
|-------------------------|---------------|  
|{"typ": "punkt", "koordinater": [76,6, 10,1]}|{"typ": "polygon", "koordinater": [[0,0, 0,0], [10,0, 0,0], [10,0, 10,0], [0,0, 10,0], [0,0, 0,0]]}|  
|{"typ": "punkt", "koordinater": [15,0, 15,0]}|{"typ": "polygon", "koordinater": [[10,0, 10,0], [20,0, 10,0], [20,0, 20,0], [10,0, 20,0], [10,0, 10,0]]}|  
  
### <a name="output-example"></a>Exempel på utdata  

 0  
  
 1  

Mer information finns på [ST_WITHIN](/stream-analytics-query/st-within) referens.

## <a name="next-steps"></a>Nästa steg

* [Introduktion till Azure Stream Analytics](stream-analytics-introduction.md)
* [Komma igång med Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Skala Azure Stream Analytics-jobb](stream-analytics-scale-jobs.md)
* [Referens för Azure Stream Analytics-frågespråket](/stream-analytics-query/stream-analytics-query-language-reference)
* [Referens för Azure Stream Analytics Management REST-API:et](/rest/api/streamanalytics/)
