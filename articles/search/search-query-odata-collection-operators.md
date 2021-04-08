---
title: Referens för OData Collection-operator
titleSuffix: Azure Cognitive Search
description: När du skapar filter uttryck i Azure Kognitiv sökning-frågor använder du operatorerna "any" och "alla" i lambda-uttryck när filtret är i en samling eller ett komplext samlings fält.
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
ms.openlocfilehash: 4c1243d5d9122539466e94b6bbfdd5ced588e69a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88934913"
---
# <a name="odata-collection-operators-in-azure-cognitive-search---any-and-all"></a>OData Collection-operatörer i Azure Kognitiv sökning – `any` och `all`

När du skriver ett [OData filter-uttryck](query-odata-filter-orderby-syntax.md) som ska användas med Azure kognitiv sökning är det ofta användbart att filtrera på samlings fält. Du kan åstadkomma detta med `any` `all` operatorerna och.

## <a name="syntax"></a>Syntax

Följande EBNF ([utökad Backus-Naur form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definierar grammatiken i ett OData-uttryck som använder `any` eller `all` .

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression
```

Ett interaktivt syntax diagram är också tillgängligt:

> [!div class="nextstepaction"]
> [OData-syntax diagram för Azure Kognitiv sökning](https://azuresearch.github.io/odata-syntax-diagram/#collection_filter_expression)

> [!NOTE]
> Se [referens för OData-uttryck för Azure kognitiv sökning](search-query-odata-syntax-reference.md) för den fullständiga ebnf.

Det finns tre typer av uttryck som filtrerar samlingar.

- De två första itern över ett samlings fält och använder ett predikat som anges i form av ett lambda-uttryck för varje element i mängden.
  - Ett uttryck som använder `all` returnerar `true` om predikatet är sant för varje element i mängden.
  - Ett uttryck som använder `any` returnerar `true` om predikatet är sant för minst ett element i mängden.
- Den tredje formen av samlings filtret använder `any` utan lambda-uttryck för att testa om ett samlings fält är tomt. Om samlingen innehåller element returneras `true` . Om samlingen är tom returneras `false` .

Ett **lambda-uttryck** i ett samlings filter är som bröd texten i en slinga i ett programmeringsspråk. Den definierar en variabel, som kallas **variabeln Range**, som innehåller det aktuella elementet i samlingen under iteration. Den definierar också ett annat booleskt uttryck som är de filter villkor som ska tillämpas på variabeln Range för varje element i samlingen.

## <a name="examples"></a>Exempel

Matcha dokument vars `tags` fält innehåller exakt strängen "WiFi":

```text
tags/any(t: t eq 'wifi')
```

Matcha dokument där varje element i `ratings` fältet faller mellan 3 och 5, inklusive:

```text
ratings/all(r: r ge 3 and r le 5)
```

Matcha dokument där något av de geo-koordinaterna i `locations` fältet är inom den aktuella polygonen:

```text
locations/any(loc: geo.intersects(loc, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))
```

Matcha dokument där `rooms` fältet är tomt:

```text
not rooms/any()
```

Matcha dokument där för alla rum, `rooms/amenities` fältet innehåller "TV" och `rooms/baseRate` är mindre än 100:

```text
rooms/all(room: room/amenities/any(a: a eq 'tv') and room/baseRate lt 100.0)
```

## <a name="limitations"></a>Begränsningar

Alla funktioner i filter uttryck är inte tillgängliga i bröd texten i ett lambda-uttryck. Begränsningarna varierar beroende på data typen för det samlings fält som du vill filtrera. I följande tabell sammanfattas begränsningarna.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

Mer information om dessa begränsningar och exempel finns i [Felsöka samlings filter i Azure kognitiv sökning](search-query-troubleshoot-collection-filters.md). Mer detaljerad information om varför dessa begränsningar finns finns [i förstå samlings filter i Azure kognitiv sökning](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Nästa steg  

- [Filter i Azure Kognitiv sökning](search-filters.md)
- [OData uttrycks språk översikt för Azure Kognitiv sökning](query-odata-filter-orderby-syntax.md)
- [Syntax-referens för OData-uttryck för Azure Kognitiv sökning](search-query-odata-syntax-reference.md)
- [Sök efter dokument &#40;Azure Kognitiv sökning REST API&#41;](/rest/api/searchservice/Search-Documents)