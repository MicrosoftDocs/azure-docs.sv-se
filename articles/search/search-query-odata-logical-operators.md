---
title: Referens för OData logisk operator
titleSuffix: Azure Cognitive Search
description: Syntax och referens dokumentation för användning av logiska OData-operatörer och, eller, och inte, i Azure Kognitiv sökning-frågor.
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
ms.openlocfilehash: 27d5427d34de591f9cfeab2310d79a2fde217624
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88917881"
---
# <a name="odata-logical-operators-in-azure-cognitive-search---and-or-not"></a>Logiska OData-operatörer i Azure Kognitiv sökning- `and` , `or` , `not`

[OData filter-uttryck](query-odata-filter-orderby-syntax.md) i Azure kognitiv sökning är booleska uttryck som utvärderas till `true` eller `false` . Du kan skriva ett komplext filter genom att skriva en serie med [enklare filter](search-query-odata-comparison-operators.md) och skriva dem med hjälp av logiska operatorer från [booleska algebra](https://en.wikipedia.org/wiki/Boolean_algebra):

- `and`: En binär operator som utvärderar till `true` om både vänster och höger under uttryck utvärderas till `true` .
- `or`: En binär operator som utvärderar till `true` om något av dess vänstra eller högra del uttryck utvärderas till `true` .
- `not`: En unär operator som utvärderar till `true` om dess under uttryck utvärderas till `false` och vice versa.

Dessa, tillsammans med [samlings operatörerna `any` och `all` ](search-query-odata-collection-operators.md), låter dig skapa filter som kan uttrycka mycket komplexa Sök kriterier.

## <a name="syntax"></a>Syntax

Följande EBNF ([utökad Backus-Naur form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definierar grammatiken i ett OData-uttryck som använder logiska operatorer.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression
```

Ett interaktivt syntax diagram är också tillgängligt:

> [!div class="nextstepaction"]
> [OData-syntax diagram för Azure Kognitiv sökning](https://azuresearch.github.io/odata-syntax-diagram/#logical_expression)

> [!NOTE]
> Se [referens för OData-uttryck för Azure kognitiv sökning](search-query-odata-syntax-reference.md) för den fullständiga ebnf.

Det finns två former av logiska uttryck: Binary ( `and` / `or` ), där det finns två under uttryck och unära ( `not` ), där det bara finns ett. Under uttryck kan vara booleska uttryck av valfri typ:

- Fält eller intervall-variabler av typen `Edm.Boolean`
- Funktioner som returnerar värden av typen `Edm.Boolean` , t. ex. `geo.intersects` eller `search.ismatch`
- [Jämförelse uttryck](search-query-odata-comparison-operators.md), till exempel `rating gt 4`
- [Samlings uttryck](search-query-odata-collection-operators.md), till exempel `Rooms/any(room: room/Type eq 'Deluxe Room')`
- Booleska litteraler `true` eller `false` .
- Andra logiska uttryck som konstrueras med `and` , `or` och `not` .

> [!IMPORTANT]
> Det finns vissa situationer där inte alla typer av under uttryck kan användas med `and` / `or` , särskilt i lambda-uttryck. Mer information finns i [OData Collection-operatörer i Azure kognitiv sökning](search-query-odata-collection-operators.md#limitations) .

### <a name="logical-operators-and-null"></a>Logiska operatorer och `null`

De flesta booleska uttryck, till exempel Functions och jämförelser kan inte skapa `null` värden, och logiska operatorer kan inte användas för `null` literalen direkt (till exempel `x and null` tillåts inte). Booleska fält kan dock vara `null` , så du måste vara medveten om hur `and` `or` `not` operatorerna, och fungerar i närvaron av null. Detta sammanfattas i följande tabell, där `b` är ett fält av typen `Edm.Boolean` :

| Uttryck | Resultat när `b` är `null` |
| --- | --- |
| `b` | `false` |
| `not b` | `true` |
| `b eq true` | `false` |
| `b eq false` | `false` |
| `b eq null` | `true` |
| `b ne true` | `true` |
| `b ne false` | `true` |
| `b ne null` | `false` |
| `b and true` | `false` |
| `b and false` | `false` |
| `b or true` | `true` |
| `b or false` | `false` |

När ett Boolean-fält visas ensamt `b` i ett filter uttryck, beter sig det som om det hade skrivits `b eq true` , så om `b` är `null` uttrycket utvärderas till `false` . Fungerar på samma sätt `not b` som `not (b eq true)` , så den utvärderas till `true` . På det här sättet `null` har fälten samma som `false` . Detta är konsekvent med hur de fungerar när de kombineras med andra uttryck med hjälp av `and` och `or` , som visas i tabellen ovan. Trots detta kommer en direkt jämförelse mellan `false` ( `b eq false` ) fortfarande att utvärderas till `false` . Med andra ord `null` är inte lika med `false` , även om det beter sig som det i booleska uttryck.

## <a name="examples"></a>Exempel

Matcha dokument där `rating` fältet är mellan 3 och 5, inklusive:

```odata-filter-expr
    rating ge 3 and rating le 5
```

Matcha dokument där alla element i `ratings` fältet är mindre än 3 eller större än 5:

```odata-filter-expr
    ratings/all(r: r lt 3 or r gt 5)
```

Matcha dokument där `location` fältet finns inom den aktuella polygonen och dokumentet inte innehåller termen "offentlig".

```odata-filter-expr
    geo.intersects(location, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))') and not search.ismatch('public')
```

Matcha dokument för hotell i Vancouver, Kanada där det finns ett Deluxe-rum med en bas taxa på mindre än 160:

```odata-filter-expr
    Address/City eq 'Vancouver' and Address/Country eq 'Canada' and Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 160)
```

## <a name="next-steps"></a>Nästa steg  

- [Filter i Azure Kognitiv sökning](search-filters.md)
- [OData uttrycks språk översikt för Azure Kognitiv sökning](query-odata-filter-orderby-syntax.md)
- [Syntax-referens för OData-uttryck för Azure Kognitiv sökning](search-query-odata-syntax-reference.md)
- [Sök efter dokument &#40;Azure Kognitiv sökning REST API&#41;](/rest/api/searchservice/Search-Documents)