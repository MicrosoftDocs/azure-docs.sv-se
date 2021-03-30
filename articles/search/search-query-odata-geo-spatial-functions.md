---
title: OData geo-spatial funktions referens
titleSuffix: Azure Cognitive Search
description: Syntax och referens dokumentation för att använda OData-funktioner för geo-spatial, geo. Distance och geo. korsar varandra i Azure Kognitiv sökning-frågor.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
translation.priority.mt:
- de-de
- es-es
- fr-fr
- it-it
- ja-jp
- ko-kr
- pt-br
- ru-ru
- zh-cn
- zh-tw
ms.openlocfilehash: 376cece922ca424ec78011224852b1fa5499da16
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88934845"
---
# <a name="odata-geo-spatial-functions-in-azure-cognitive-search---geodistance-and-geointersects"></a>OData geo-spatial-funktioner i Azure Kognitiv sökning – `geo.distance` och `geo.intersects`

Azure Kognitiv sökning stöder geo-spatiala frågor i [OData filter-uttryck](query-odata-filter-orderby-syntax.md) via- `geo.distance` och- `geo.intersects` funktionerna. `geo.distance`Funktionen returnerar avståndet i kilo meter mellan två punkter, ett fält eller en intervall variabel, och en är en konstant som ska skickas som en del av filtret. `geo.intersects`Funktionen returnerar `true` om en viss punkt är inom en viss polygon, där punkten är en fält-eller intervall variabel och polygonen anges som en konstant som skickas som en del av filtret.

`geo.distance`Funktionen kan också användas i [ **$OrderBy** -parametern](search-query-odata-orderby.md) för att sortera Sök Resultat efter avstånd från en viss punkt. Syntaxen för `geo.distance` i **$OrderBy** är samma som i **$filter**. När du använder `geo.distance` i **$OrderBy** måste det fält som det gäller för vara av typen `Edm.GeographyPoint` och det måste också vara **sorterbart**.

> [!NOTE]
> När `geo.distance` du använder i **$OrderBy** -parametern får fältet som du skickar till funktionen bara innehålla en enda geo-punkt. Med andra ord måste den vara av typen `Edm.GeographyPoint` och inte `Collection(Edm.GeographyPoint)` . Det går inte att sortera i samlings fält i Azure Kognitiv sökning.

## <a name="syntax"></a>Syntax

Följande EBNF ([Extended Backus-Naur form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definierar grammatiken i `geo.distance` och `geo.intersects` -funktionerna samt de geo-spatial-värden som de fungerar på:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
geo_distance_call ::=
    'geo.distance(' variable ',' geo_point ')'
    | 'geo.distance(' geo_point ',' variable ')'

geo_point ::= "geography'POINT(" lon_lat ")'"

lon_lat ::= float_literal ' ' float_literal

geo_intersects_call ::=
    'geo.intersects(' variable ',' geo_polygon ')'

/* You need at least four points to form a polygon, where the first and
last points are the same. */
geo_polygon ::=
    "geography'POLYGON((" lon_lat ',' lon_lat ',' lon_lat ',' lon_lat_list "))'"

lon_lat_list ::= lon_lat(',' lon_lat)*
```

Ett interaktivt syntax diagram är också tillgängligt:

> [!div class="nextstepaction"]
> [OData-syntax diagram för Azure Kognitiv sökning](https://azuresearch.github.io/odata-syntax-diagram/#geo_distance_call)

> [!NOTE]
> Se [referens för OData-uttryck för Azure kognitiv sökning](search-query-odata-syntax-reference.md) för den fullständiga ebnf.

### <a name="geodistance"></a>Geo. Distance

`geo.distance`Funktionen använder två parametrar av typen `Edm.GeographyPoint` och returnerar ett `Edm.Double` värde som är avståndet mellan dem i kilo meter. Detta skiljer sig från andra tjänster som stöder OData geo-spatiala åtgärder, som vanligt vis returnerar avstånd i meter.

En av parametrarna `geo.distance` måste vara en konstant med en geografisk punkt och den andra måste vara en fält Sök väg (eller en intervall variabel om ett filter ska itereras över ett fält av typen `Collection(Edm.GeographyPoint)` ). Ordningen på dessa parametrar spelar ingen roll.

Geografisk punkts konstant är av formen `geography'POINT(<longitude> <latitude>)'` där longitud och latitud är numeriska konstanter.

> [!NOTE]
> När `geo.distance` du använder i ett filter måste du jämföra avståndet som returneras av funktionen med en konstant med hjälp av `lt` , `le` , `gt` eller `ge` . Operatorerna `eq` och `ne` stöds inte när du jämför avstånd. Detta är till exempel en korrekt användning av `geo.distance` : `$filter=geo.distance(location, geography'POINT(-122.131577 47.678581)') le 5` .

### <a name="geointersects"></a>Geo. snitt

`geo.intersects`Funktionen tar en variabel av typen `Edm.GeographyPoint` och en konstant `Edm.GeographyPolygon` och returnerar en `Edm.Boolean`  --  `true` om punkten är inom gränserna för polygonen, `false` annars.

Polygonen är en tvådimensionell yta som lagras som en sekvens med punkter som definierar en avgränsnings ring (se [exemplen](#examples) nedan). Polygonen måste stängas, vilket innebär att de första och sista punkt uppsättningarna måste vara desamma. [Punkter i en polygon måste vara i moturs ordning](/rest/api/searchservice/supported-data-types#Anchor_1).

### <a name="geo-spatial-queries-and-polygons-spanning-the-180th-meridian"></a>Geo-spatiala frågor och polygoner som sträcker sig över 180th-Meri Dian

För många geo-spatiala fråge bibliotek skapas en fråga som innehåller 180th-Meri Dian (nära datum gränsen,) eller som kräver en lösning, t. ex. delning av polygonen i två, en på endera sidan av Meri Dian.

I Azure Kognitiv sökning fungerar geo-spatiala frågor som innehåller 180-graders longitud som förväntat om frågetexten är rektangulär och koordinaterna stämmer överens med en rutnäts layout utmed longitud och latitud (till exempel `geo.intersects(location, geography'POLYGON((179 65, 179 66, -179 66, -179 65, 179 65))'` ). Annars, för icke-rektangulära eller ej justerade former, bör du tänka på metoden dela upp polygon.  

### <a name="geo-spatial-functions-and-null"></a>Geo-spatiala funktioner och `null`

Precis som alla andra icke-samlings fält i Azure Kognitiv sökning kan fält av typen `Edm.GeographyPoint` innehålla `null` värden. När Azure-Kognitiv sökning utvärderar `geo.intersects` för ett fält som är `null` , blir resultatet alltid `false` . Beteendet för `geo.distance` i det här fallet beror på kontexten:

- I filter, `geo.distance` för ett `null` fält resulterar i `null` . Det innebär att dokumentet inte överensstämmer eftersom det `null` jämförs med ett värde som inte är null utvärderas till `false` .
- När du sorterar resultat med hjälp av **$OrderBy**, i `geo.distance` ett `null` fält resultat på maximalt möjligt avstånd. Dokument med sådana fält sorteras lägre än alla andra när sorterings riktningen `asc` används (standard) och högre än för alla andra när riktningen är `desc` .

## <a name="examples"></a>Exempel

### <a name="filter-examples"></a>Filterexempel

Hitta alla hotell inom 10 kilo meter från en viss referens punkt (där platsen är ett fält av typen `Edm.GeographyPoint` ):

```odata-filter-expr
    geo.distance(location, geography'POINT(-122.131577 47.678581)') le 10
```

Hitta alla hotell inom ett angivet visnings område som beskrivs som en polygon (där platsen är ett fält av typen `Edm.GeographyPoint` ). Observera att polygonen är stängd (de första och sista punkt uppsättningarna måste vara identiska) och [punkterna måste anges i motsols ordning](/rest/api/searchservice/supported-data-types#Anchor_1).

```odata-filter-expr
    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')
```

### <a name="order-by-examples"></a>Ordning – exempel

Sortera hotell fallande efter `rating` , sedan stigande efter avstånd från givna koordinater:

```odata-filter-expr
    rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```

Sortera hotell i fallande ordning efter `search.score` och och `rating` sedan i stigande ordning efter avstånd från givna koordinater, så att de två hotellen med identiska klassificeringar visas först:

```odata-filter-expr
    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```

## <a name="next-steps"></a>Nästa steg  

- [Filter i Azure Kognitiv sökning](search-filters.md)
- [OData uttrycks språk översikt för Azure Kognitiv sökning](query-odata-filter-orderby-syntax.md)
- [Syntax-referens för OData-uttryck för Azure Kognitiv sökning](search-query-odata-syntax-reference.md)
- [Sök efter dokument &#40;Azure Kognitiv sökning REST API&#41;](/rest/api/searchservice/Search-Documents)