---
title: OData filter-referens
titleSuffix: Azure Cognitive Search
description: OData-Språkreferens och fullständig syntax som används för att skapa filter uttryck i Azure Kognitiv sökning-frågor.
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
ms.openlocfilehash: 0f33b5a28d7c83be7e546c3f61bc517047c51312
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88934862"
---
# <a name="odata-filter-syntax-in-azure-cognitive-search"></a>OData $filter-syntax i Azure Kognitiv sökning

Azure Kognitiv sökning använder [OData filter-uttryck](query-odata-filter-orderby-syntax.md) för att tillämpa ytterligare villkor för en Sök fråga förutom full texts öknings termer. I den här artikeln beskrivs syntaxen för filter i detalj. Mer allmän information om vilka filter som är och hur du använder dem för att realisera vissa fråge scenarier finns i [filter i Azure kognitiv sökning](search-filters.md).

## <a name="syntax"></a>Syntax

Ett filter i OData-språket är ett booleskt uttryck, vilket i sin tur kan vara en av flera typer av uttryck, som visas i följande EBNF ([utökad Backus-Naur form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
boolean_expression ::=
    collection_filter_expression
    | logical_expression
    | comparison_expression
    | boolean_literal
    | boolean_function_call
    | '(' boolean_expression ')'
    | variable

/* This can be a range variable in the case of a lambda, or a field path. */
variable ::= identifier | field_path
```

Ett interaktivt syntax diagram är också tillgängligt:

> [!div class="nextstepaction"]
> [OData-syntax diagram för Azure Kognitiv sökning](https://azuresearch.github.io/odata-syntax-diagram/#boolean_expression)

> [!NOTE]
> Se [referens för OData-uttryck för Azure kognitiv sökning](search-query-odata-syntax-reference.md) för den fullständiga ebnf.

Typerna av booleska uttryck är:

- Samlings filter uttryck med `any` eller `all` . Dessa använder filter villkor i samlings fält. Mer information finns i [OData Collection-operatörer i Azure kognitiv sökning](search-query-odata-collection-operators.md).
- Logiska uttryck som kombinerar andra booleska uttryck med operatorerna `and` , `or` och `not` . Mer information finns i avsnittet [om logiska OData-operatörer i Azure kognitiv sökning](search-query-odata-logical-operators.md).
- Jämförelse uttryck, som jämför fält eller intervall-variabler med konstanta värden med operatorerna,,,, `eq` `ne` `gt` `lt` `ge` och `le` . Mer information finns i [OData-jämförelse operatörer i Azure kognitiv sökning](search-query-odata-comparison-operators.md). Jämförelse uttryck används också för att jämföra avstånd mellan geo-spatiala koordinater med `geo.distance` funktionen. Mer information finns i [OData geo-spatial-funktioner i Azure kognitiv sökning](search-query-odata-geo-spatial-functions.md).
- Booleska litteraler `true` och `false` . Dessa konstanter kan vara användbara ibland när du skapar filter på ett programmerings sätt, men annars inte brukar användas i praktiken.
- Anrop till booleska funktioner, inklusive:
  - `geo.intersects`, som testar om en viss punkt är inom en viss polygon. Mer information finns i [OData geo-spatial-funktioner i Azure kognitiv sökning](search-query-odata-geo-spatial-functions.md).
  - `search.in`, som jämför en fält-eller intervall variabel med varje värde i en lista med värden. Mer information finns [i OData- `search.in` funktionen i Azure kognitiv sökning](search-query-odata-search-in-function.md).
  - `search.ismatch` och `search.ismatchscoring` , som kör full texts öknings åtgärder i en filter kontext. Mer information finns i [OData full text search-funktioner i Azure kognitiv sökning](search-query-odata-full-text-search-functions.md).
- Fält Sök vägar eller intervall-variabler av typen `Edm.Boolean` . Om ditt index till exempel har ett Boolean-fält som heter `IsEnabled` och du vill returnera alla dokument där det här fältet är `true` , kan filter uttrycket bara vara namnet `IsEnabled` .
- Booleska uttryck inom parentes. Med hjälp av parenteser kan du uttryckligen bestämma ordningen på åtgärderna i ett filter. Mer information om OData-operatörernas standard prioritet finns i nästa avsnitt.

### <a name="operator-precedence-in-filters"></a>Operator prioritet i filter

Om du skriver ett filter uttryck utan parentes runt dess under uttryck, kommer Azure Kognitiv sökning att utvärdera det enligt en uppsättning av operatorernas prioritets regler. Dessa regler baseras på vilka operatorer som används för att kombinera under uttryck. I följande tabell visas en lista över operatorer i ordning från högsta till lägsta prioritet:

| Group | Operator (er) |
| --- | --- |
| Logiska operatorer | `not` |
| Jämförelseoperatorer | `eq`, `ne`, `gt`, `lt`, `ge`, `le` |
| Logiska operatorer | `and` |
| Logiska operatorer | `or` |

En operator som är högre i tabellen ovan blir "bind mer tätt" till dess operander än andra operatorer. Till exempel `and` är högre prioritet än `or` och jämförelse operatorer har högre prioritet än någon av dem, så följande två uttryck är likvärdiga:

```odata-filter-expr
    Rating gt 0 and Rating lt 3 or Rating gt 7 and Rating lt 10
    ((Rating gt 0) and (Rating lt 3)) or ((Rating gt 7) and (Rating lt 10))
```

`not`Operatorn har högst prioritet för alla – ännu högre än jämförelse operatorerna. Det är därför om du försöker skriva ett filter så här:

```odata-filter-expr
    not Rating gt 5
```

Du får det här fel meddelandet:

```text
    Invalid expression: A unary operator with an incompatible type was detected. Found operand type 'Edm.Int32' for operator kind 'Not'.
```

Det här felet beror på att operatorn bara är kopplad till `Rating` fältet, vilket är av typen `Edm.Int32` och inte med hela jämförelse uttrycket. Korrigeringen är att ge operanden `not` i parentes:

```odata-filter-expr
    not (Rating gt 5)
```

<a name="bkmk_limits"></a>

### <a name="filter-size-limitations"></a>Begränsningar för filter storlek

Det finns gränser för storlek och komplexitet för filter uttryck som du kan skicka till Azure Kognitiv sökning. Gränserna baseras ungefär på antalet satser i filter uttrycket. En lämplig rikt linje är att om du har hundratals satser, riskerar du att överskrida gränsen. Vi rekommenderar att du utformar ditt program på ett sådant sätt att det inte genererar filter av obegränsad storlek.

> [!TIP]
> Om du använder [ `search.in` funktionen](search-query-odata-search-in-function.md) i stället för långa disknutar av likhets jämförelser kan du undvika filter sats gränsen, eftersom ett funktions anrop räknas som en enskild sats.

## <a name="examples"></a>Exempel

Hitta alla hotell med minst ett rum med en bas taxa som är mindre än $200 som klassificeras enligt eller över 4:

```odata-filter-expr
    $filter=Rooms/any(room: room/BaseRate lt 200.0) and Rating ge 4
```

Hitta alla andra hotell än "sto View Motel" som har renovated sedan 2010:

```odata-filter-expr
    $filter=HotelName ne 'Sea View Motel' and LastRenovationDate ge 2010-01-01T00:00:00Z
```

Hitta alla hotell som var renovated i 2010 eller senare. Datetime-literalen innehåller tids zons information för Pacific, normal tid:  

```odata-filter-expr
    $filter=LastRenovationDate ge 2010-01-01T00:00:00-08:00
```

Hitta alla hotell som har parkera och där alla rum är icke-rökning:

```odata-filter-expr
    $filter=ParkingIncluded and Rooms/all(room: not room/SmokingAllowed)
```

 \- Eller  

```odata-filter-expr
    $filter=ParkingIncluded eq true and Rooms/all(room: room/SmokingAllowed eq false)
```

Hitta alla hotell som är lyxen eller innehåller parkering och har en klassificering på 5:  

```odata-filter-expr
    $filter=(Category eq 'Luxury' or ParkingIncluded eq true) and Rating eq 5
```

Hitta alla hotell med taggen "WiFi" i minst ett rum (där varje rum har Taggar lagrade i ett `Collection(Edm.String)` fält):  

```odata-filter-expr
    $filter=Rooms/any(room: room/Tags/any(tag: tag eq 'wifi'))
```

Hitta alla hotell med alla rum:  

```odata-filter-expr
    $filter=Rooms/any()
```

Hitta alla hotell som inte har några rum:

```odata-filter-expr
    $filter=not Rooms/any()
```

Hitta alla hotell inom 10 kilo meter från en viss referens punkt (där `Location` är ett fält av typen `Edm.GeographyPoint` ):

```odata-filter-expr
    $filter=geo.distance(Location, geography'POINT(-122.131577 47.678581)') le 10
```

Hitta alla hotell inom ett angivet visnings område som beskrivs som en polygon (där `Location` är ett fält av typen EDM. GeographyPoint). Polygonen måste vara stängd, vilket innebär att de första och sista punkt uppsättningarna måste vara desamma. Dessutom [måste punkterna visas i motsols ordning](/rest/api/searchservice/supported-data-types#Anchor_1).

```odata-filter-expr
    $filter=geo.intersects(Location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))')
```

Hitta alla hotell där fältet Beskrivning är null. Fältet kommer att vara null om det aldrig har angetts, eller om det uttryckligen har angetts till null:  

```odata-filter-expr
    $filter=Description eq null
```

Hitta alla hotell med namn som motsvarar antingen "Sea View Motel" eller "budget hotell"). Dessa fraser innehåller blank steg och blank steg är en standard avgränsare. Du kan ange en alternativ avgränsare i enkla citat tecken som den tredje sträng parametern:  

```odata-filter-expr
    $filter=search.in(HotelName, 'Sea View motel,Budget hotel', ',')
```

Hitta alla hotell med namn som är lika med "Sea View Motel" eller "budget hotellet", separerade med "|"):  

```odata-filter-expr
    $filter=search.in(HotelName, 'Sea View motel|Budget hotel', '|')
```

Hitta alla hotell där alla rum har taggen "WiFi" eller "tub":

```odata-filter-expr
    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub'))
```

Hitta en matchning på fraser i en samling, t. ex. "uppvärmd hand duks-rack" eller "Hairdryer ingår" i taggar.

```odata-filter-expr
    $filter=Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))
```

Hitta dokument med ordet "Waterfront". Den här filter frågan är identisk med en [search-begäran](/rest/api/searchservice/search-documents) med `search=waterfront` .

```odata-filter-expr
    $filter=search.ismatchscoring('waterfront')
```

Hitta dokument med ordet "Hostel" och klassificera större eller lika med 4 eller dokument med ordet "Motel" och betyget är lika med 5. Den här begäran kunde inte uttryckas utan `search.ismatchscoring` funktionen eftersom den kombinerar full texts ökning med filter åtgärder med hjälp av `or` .

```odata-filter-expr
    $filter=search.ismatchscoring('hostel') and rating ge 4 or search.ismatchscoring('motel') and rating eq 5
```

Hitta dokument utan ordet "lyxen".

```odata-filter-expr
    $filter=not search.ismatch('luxury')
```

Hitta dokument med frasen "oceanen" eller klassificeringen lika med 5. `search.ismatchscoring`Frågan körs bara mot fält `HotelName` och `Description` . Dokument som matchade bara den andra satsen i disknuten returneras för--hotell med `Rating` lika med 5. De dokumenten kommer att returneras med poängen lika med noll för att det ska vara uppenbart att de inte matchar någon av de förväntade delarna i uttrycket.

```odata-filter-expr
    $filter=search.ismatchscoring('"ocean view"', 'Description,HotelName') or Rating eq 5
```

Hitta hotell där villkoren "hotell" och "flyg plats" inte innehåller fler än fem ord i beskrivningen och där alla rum är icke-rökning. Den här frågan använder det [fullständiga Lucene-frågespråket](query-lucene-syntax.md).

```odata-filter-expr
    $filter=search.ismatch('"hotel airport"~5', 'Description', 'full', 'any') and not Rooms/any(room: room/SmokingAllowed)
```

## <a name="next-steps"></a>Nästa steg  

- [Filter i Azure Kognitiv sökning](search-filters.md)
- [OData uttrycks språk översikt för Azure Kognitiv sökning](query-odata-filter-orderby-syntax.md)
- [Syntax-referens för OData-uttryck för Azure Kognitiv sökning](search-query-odata-syntax-reference.md)
- [Sök efter dokument &#40;Azure Kognitiv sökning REST API&#41;](/rest/api/searchservice/Search-Documents)