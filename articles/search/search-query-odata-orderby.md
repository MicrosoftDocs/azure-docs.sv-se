---
title: OData-ordning – efter referens
titleSuffix: Azure Cognitive Search
description: Syntax och språk referens dokumentation för att använda order by i Azure Kognitiv sökning-frågor.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 08/05/2020
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
ms.openlocfilehash: 83ab2c6b97435ace0d2bc508cbf522600391b60b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88926838"
---
# <a name="odata-orderby-syntax-in-azure-cognitive-search"></a>OData $orderby-syntax i Azure Kognitiv sökning

 Du kan använda [OData **$OrderBy** -parametern](query-odata-filter-orderby-syntax.md) för att tillämpa en anpassad sorterings ordning för sök resultat i Azure kognitiv sökning. I den här artikeln beskrivs syntaxen för **$OrderBy** i detalj. Mer allmän information om hur du använder **$OrderBy** när du presenterar Sök resultat finns i [så här arbetar du med Sök resultat i Azure kognitiv sökning](search-pagination-page-layout.md).

## <a name="syntax"></a>Syntax

Parametern **$OrderBy** accepterar en kommaavgränsad lista med upp till 32 **order by-satser**. Syntaxen för en order by-sats beskrivs av följande EBNF ([utökad Backus-Naur form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)):

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'
```

Ett interaktivt syntax diagram är också tillgängligt:

> [!div class="nextstepaction"]
> [OData-syntax diagram för Azure Kognitiv sökning](https://azuresearch.github.io/odata-syntax-diagram/#order_by_clause)

> [!NOTE]
> Se [referens för OData-uttryck för Azure kognitiv sökning](search-query-odata-syntax-reference.md) för den fullständiga ebnf.

Varje sats har sorterings villkor, eventuellt följt av en sorterings riktning ( `asc` för stigande eller `desc` för fallande). Om du inte anger någon riktning, är standardvärdet stigande. Om det finns null-värden i fältet visas null-värden först om sorteringen är `asc` och sista om sorteringen är `desc` .

Sorterings villkoren kan antingen vara sökvägen till ett `sortable` fält eller ett anrop till endera eller- [`geo.distance`](search-query-odata-geo-spatial-functions.md) [`search.score`](search-query-odata-search-score-function.md) funktionerna.

Om flera dokument har samma sorterings villkor och `search.score` funktionen inte används (till exempel om du sorterar efter ett numeriskt `Rating` fält och tre dokument har en klassificering på 4) bryts bindningarna av dokument poängen i fallande ordning. När dokument resultatet är detsamma (till exempel när det inte finns någon text Sök fråga som anges i begäran) är den relativa ordningen för de bundna dokumenten obestämd.

Du kan ange flera sorterings villkor. Ordningen på uttryck avgör den slutliga sorterings ordningen. Om du till exempel vill sortera fallande efter poäng, följt av klassificering, skulle syntaxen vara `$orderby=search.score() desc,Rating desc` .

Syntaxen för `geo.distance` i **$OrderBy** är samma som i **$filter**. När du använder `geo.distance` i **$OrderBy** måste det fält som det gäller för vara av typen `Edm.GeographyPoint` och det måste också vara `sortable` .

Syntaxen för `search.score` i **$OrderBy** är `search.score()` . Funktionen `search.score` tar inga parametrar.

## <a name="examples"></a>Exempel

Sortera hotell stigande efter grund pris:

```odata-filter-expr
    $orderby=BaseRate asc
```

Sortera hotell fallande efter klassificering och sedan stigande efter bas pris (kom ihåg att stigande är standard):

```odata-filter-expr
    $orderby=Rating desc,BaseRate
```

Sortera hotell fallande efter klassificering och sedan stigande efter avstånd från givna koordinater:

```odata-filter-expr
    $orderby=Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc
```

Sortera hotell i fallande ordning efter sökning. score och betyg och sedan i stigande ordning efter avstånd från givna koordinater. Mellan två hotell med identiska resultat och omdömen visas den närmast första:

```odata-filter-expr
    $orderby=search.score() desc,Rating desc,geo.distance(Location, geography'POINT(-122.131577 47.678581)') asc
```

## <a name="next-steps"></a>Nästa steg  

- [Så här arbetar du med Sök resultat i Azure Kognitiv sökning](search-pagination-page-layout.md)
- [OData uttrycks språk översikt för Azure Kognitiv sökning](query-odata-filter-orderby-syntax.md)
- [Syntax-referens för OData-uttryck för Azure Kognitiv sökning](search-query-odata-syntax-reference.md)
- [Sök efter dokument &#40;Azure Kognitiv sökning REST API&#41;](/rest/api/searchservice/Search-Documents)