---
title: OData Select-referens
titleSuffix: Azure Cognitive Search
description: Syntax och språk referens för explicit val av fält att returnera i Sök resultatet av Azure Kognitiv sökning-frågor.
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
ms.openlocfilehash: 54b6ae227fc4b3b951717799660543c02874dda0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88919666"
---
# <a name="odata-select-syntax-in-azure-cognitive-search"></a>OData $select-syntax i Azure Kognitiv sökning

 Du kan använda [OData **$Select** -parametern](query-odata-filter-orderby-syntax.md) för att välja vilka fält som ska ingå i sök resultatet från Azure kognitiv sökning. I den här artikeln beskrivs syntaxen för **$Select** i detalj. Mer allmän information om hur du använder **$Select** när du presenterar Sök resultat finns i [så här arbetar du med Sök resultat i Azure kognitiv sökning](search-pagination-page-layout.md).

## <a name="syntax"></a>Syntax

Parametern **$Select** avgör vilka fält för varje dokument som returneras i frågeresultatet. Följande EBNF ([utökad Backus-Naur form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definierar grammatiken för **$Select** -parametern:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*
```

Ett interaktivt syntax diagram är också tillgängligt:

> [!div class="nextstepaction"]
> [OData-syntax diagram för Azure Kognitiv sökning](https://azuresearch.github.io/odata-syntax-diagram/#select_expression)

> [!NOTE]
> Se [referens för OData-uttryck för Azure kognitiv sökning](search-query-odata-syntax-reference.md) för den fullständiga ebnf.

**$Select** -parametern finns i två former:

1. En enda stjärna ( `*` ) som visar att alla hämtnings bara fält ska returneras, eller
1. En kommaavgränsad lista över fält Sök vägar, som identifierar vilka fält som ska returneras.

När du använder det andra formuläret kan du bara ange hämtnings bara fält i listan.

Om du anger ett komplext fält utan att uttryckligen ange dess underordnade fält, inkluderas alla hämtnings bara underordnade fält i frågeresultatet. Anta till exempel att indexet har ett `Address` fält med `Street` , `City` och `Country` under fält som alla kan hämtas. Om du anger `Address` i **$Select** kommer frågeresultatet att innehålla alla tre under fält.

## <a name="examples"></a>Exempel

Inkludera `HotelId` fälten, `HotelName` och `Rating` den översta nivån i resultaten, samt `City` under fältet för `Address` :

```odata-filter-expr
    $select=HotelId, HotelName, Rating, Address/City
```

Ett exempel på resultat kan se ut så här:

```json
{
  "HotelId": "1",
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "City": "New York"
  }
}
```

Ta med `HotelName` fältet översta nivån i resultaten, samt alla under fält i och `Address` `Type` `BaseRate` under fälten för varje objekt i `Rooms` samlingen:

```odata-filter-expr
    $select=HotelName, Address, Rooms/Type, Rooms/BaseRate
```

Ett exempel på resultat kan se ut så här:

```json
{
  "HotelName": "Secret Point Motel",
  "Rating": 4,
  "Address": {
    "StreetAddress": "677 5th Ave",
    "City": "New York",
    "StateProvince": "NY",
    "Country": "USA",
    "PostalCode": "10022"
  },
  "Rooms": [
    {
      "Type": "Budget Room",
      "BaseRate": 9.69
    },
    {
      "Type": "Budget Room",
      "BaseRate": 8.09
    }
  ]
}
```

## <a name="next-steps"></a>Nästa steg  

- [Så här arbetar du med Sök resultat i Azure Kognitiv sökning](search-pagination-page-layout.md)
- [OData uttrycks språk översikt för Azure Kognitiv sökning](query-odata-filter-orderby-syntax.md)
- [Syntax-referens för OData-uttryck för Azure Kognitiv sökning](search-query-odata-syntax-reference.md)
- [Sök efter dokument &#40;Azure Kognitiv sökning REST API&#41;](/rest/api/searchservice/Search-Documents)