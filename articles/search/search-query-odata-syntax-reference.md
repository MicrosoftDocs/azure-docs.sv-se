---
title: Syntax-referens för OData-uttryck
titleSuffix: Azure Cognitive Search
description: Formell grammatik-och syntax-specifikation för OData-uttryck i Azure Kognitiv sökning frågor.
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
ms.openlocfilehash: 83e9ae4aa68bc0c819d02a0cc6c39758549811cb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88928861"
---
# <a name="odata-expression-syntax-reference-for-azure-cognitive-search"></a>Syntax-referens för OData-uttryck för Azure Kognitiv sökning

Azure Kognitiv sökning använder [OData-uttryck](https://docs.oasis-open.org/odata/odata/v4.01/odata-v4.01-part2-url-conventions.html) som parametrar i API: et. Oftast används OData-uttryck för `$orderby` `$filter` parametrarna och. Dessa uttryck kan vara komplexa, som innehåller flera satser, funktioner och operatorer. Men även enkla OData-uttryck som egenskaps Sök vägar används i många delar av Azure Kognitiv sökning REST API. Sök vägs uttryck används till exempel för att referera till underordnade fält i komplexa fält överallt i API: t, till exempel när du visar underordnade fält i en [förslags](index-add-suggesters.md)funktion, en [bedömnings funktion](index-add-scoring-profiles.md), `$select` parametern eller T.o.m. [fälts ökning i Lucene-frågor](query-lucene-syntax.md).

I den här artikeln beskrivs alla dessa former av OData-uttryck med hjälp av en formell grammatik. Det finns också ett [interaktivt diagram](#syntax-diagram) som hjälper dig att visuellt utforska grammatiken.

## <a name="formal-grammar"></a>Formell grammatik

Vi kan beskriva den delmängd av OData-språket som stöds av Azure Kognitiv sökning med en EBNF ([utökad Backus-Naur form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) grammatik. Reglerna visas i "uppifrån och ned", med de mest komplexa uttrycken och de delas upp i fler primitiva uttryck. Längst upp är de grammatikverktyg som motsvarar vissa parametrar för Azure Kognitiv sökning REST API:

- [`$filter`](search-query-odata-filter.md), som definieras av `filter_expression` regeln.
- [`$orderby`](search-query-odata-orderby.md), som definieras av `order_by_expression` regeln.
- [`$select`](search-query-odata-select.md), som definieras av `select_expression` regeln.
- Fält Sök vägar, som definieras av `field_path` regeln. Fält Sök vägar används i hela API: et. De kan referera till fält på den översta nivån i ett index eller under fält med ett eller flera [komplexa fält](search-howto-complex-data-types.md) överordnade.

När EBNF är ett bläddringsbar- [syntaxfel](https://en.wikipedia.org/wiki/Syntax_diagram) som gör det möjligt att interaktivt utforska grammatiken och relationerna mellan reglerna.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
/* Top-level rules */

filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*

field_path ::= identifier('/'identifier)*


/* Shared base rules */

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*


/* Rules for $orderby */

order_by_clause ::= (field_path | sortable_function) ('asc' | 'desc')?

sortable_function ::= geo_distance_call | 'search.score()'


/* Rules for $filter */

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

collection_filter_expression ::=
    field_path'/all(' lambda_expression ')'
    | field_path'/any(' lambda_expression ')'
    | field_path'/any()'

lambda_expression ::= identifier ':' boolean_expression

logical_expression ::=
    boolean_expression ('and' | 'or') boolean_expression
    | 'not' boolean_expression

comparison_expression ::= 
    variable_or_function comparison_operator constant | 
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'


/* Rules for constants and literals */

constant ::=
    string_literal
    | date_time_offset_literal
    | integer_literal
    | float_literal
    | boolean_literal
    | 'null'

string_literal ::= "'"([^'] | "''")*"'"

date_time_offset_literal ::= date_part'T'time_part time_zone

date_part ::= year'-'month'-'day

time_part ::= hour':'minute(':'second('.'fractional_seconds)?)?

zero_to_fifty_nine ::= [0-5]digit

digit ::= [0-9]

year ::= digit digit digit digit

month ::= '0'[1-9] | '1'[0-2]

day ::= '0'[1-9] | [1-2]digit | '3'[0-1]

hour ::= [0-1]digit | '2'[0-3]

minute ::= zero_to_fifty_nine

second ::= zero_to_fifty_nine

fractional_seconds ::= integer_literal

time_zone ::= 'Z' | sign hour':'minute

sign ::= '+' | '-'

/* In practice integer literals are limited in length to the precision of
the corresponding EDM data type. */
integer_literal ::= digit+

float_literal ::=
    sign? whole_part fractional_part? exponent?
    | 'NaN'
    | '-INF'
    | 'INF'

whole_part ::= integer_literal

fractional_part ::= '.'integer_literal

exponent ::= 'e' sign? integer_literal

boolean_literal ::= 'true' | 'false'


/* Rules for functions */

function_call ::=
    geo_distance_call |
    boolean_function_call

geo_distance_call ::=
    'geo.distance(' variable ',' geo_point ')'
    | 'geo.distance(' geo_point ',' variable ')'

geo_point ::= "geography'POINT(" lon_lat ")'"

lon_lat ::= float_literal ' ' float_literal

boolean_function_call ::=
    geo_intersects_call |
    search_in_call |
    search_is_match_call

geo_intersects_call ::=
    'geo.intersects(' variable ',' geo_polygon ')'

/* You need at least four points to form a polygon, where the first and
last points are the same. */
geo_polygon ::=
    "geography'POLYGON((" lon_lat ',' lon_lat ',' lon_lat ',' lon_lat_list "))'"

lon_lat_list ::= lon_lat(',' lon_lat)*

search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'

/* Note that it is illegal to call search.ismatch or search.ismatchscoring
from inside a lambda expression. */
search_is_match_call ::=
    'search.ismatch'('scoring')?'(' search_is_match_parameters ')'

search_is_match_parameters ::=
    string_literal(',' string_literal(',' query_type ',' search_mode)?)?

query_type ::= "'full'" | "'simple'"

search_mode ::= "'any'" | "'all'"
```

## <a name="syntax-diagram"></a>Syntax-diagram

För att visuellt utforska OData-språkgrammen som stöds av Azure Kognitiv sökning, prova med det interaktiva syntax diagrammet:

> [!div class="nextstepaction"]
> [OData-syntax diagram för Azure Kognitiv sökning](https://azuresearch.github.io/odata-syntax-diagram/)

## <a name="see-also"></a>Se även  

- [Filter i Azure Kognitiv sökning](search-filters.md)
- [Sök efter dokument &#40;Azure Kognitiv sökning REST API&#41;](/rest/api/searchservice/Search-Documents)
- [Lucene-frågesyntax](query-lucene-syntax.md)
- [Enkel frågesyntax i Azure Kognitiv sökning](query-simple-syntax.md)