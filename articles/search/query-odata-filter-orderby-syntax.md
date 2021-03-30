---
title: OData-språk översikt
titleSuffix: Azure Cognitive Search
description: OData-språk översikt för filter, Välj och sortera efter för Azure Kognitiv sökning-frågor.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/10/2020
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
ms.openlocfilehash: d04311fce81d147a0830918aee1d4a2a9c0808d4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88923406"
---
# <a name="odata-language-overview-for-filter-orderby-and-select-in-azure-cognitive-search"></a>OData-språk översikt för `$filter` , `$orderby` och `$select` i Azure kognitiv sökning

Azure Kognitiv sökning stöder en delmängd av OData-uttryckets syntax för **$filter**, **$OrderBy** och **$Select** uttryck. Filter uttryck utvärderas under analys av frågor, vilket begränsar sökningen till vissa fält eller lägger till matchnings villkor som används vid index genomsökningar. Order by-uttryck används som ett steg efter bearbetning över en resultat uppsättning för att sortera de dokument som returneras. Välj uttryck avgör vilka dokument fält som ska ingå i resultat uppsättningen. Syntaxen för dessa uttryck skiljer sig från den [enkla](query-simple-syntax.md) eller [fullständiga](query-lucene-syntax.md) frågesyntaxen som används i **Sök** parametern, men det finns vissa överlappande i syntaxen för att referera till fält.

Den här artikeln innehåller en översikt över det OData Expression-språk som används i filter, order by och SELECT-uttryck. Språket visas som "bottom-up", som börjar med de mest grundläggande elementen och bygger på dem. Syntaxen på den översta nivån för varje parameter beskrivs i en separat artikel:

- [$filter syntax](search-query-odata-filter.md)
- [$orderby syntax](search-query-odata-orderby.md)
- [$select syntax](search-query-odata-select.md)

OData-uttryck sträcker sig från enkla till mycket komplexa, men alla delar gemensamma element. De mest grundläggande delarna av ett OData-uttryck i Azure Kognitiv sökning:

- **Fält Sök vägar**, som refererar till vissa fält i ditt index.
- **Konstanter**, som är litterala värden för en viss datatyp.

> [!NOTE]
> Terminologi i Azure Kognitiv sökning skiljer sig från [OData-standarden](https://www.odata.org/documentation/) på några få sätt. Det som vi kallar ett **fält** i Azure kognitiv sökning kallas för en **egenskap** i OData och på samma sätt för **fält Sök väg** respektive **egenskaps Sök väg**. Ett **index** som innehåller **dokument** i Azure kognitiv sökning kallas oftare i OData som en **enhets uppsättning** som innehåller **entiteter**. Azure Kognitiv sökning-terminologin används i hela den här referensen.

## <a name="field-paths"></a>Fält Sök vägar

Följande EBNF ([utökad Backus-Naur form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definierar grammatiken för fält Sök vägar.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
field_path ::= identifier('/'identifier)*

identifier ::= [a-zA-Z_][a-zA-Z_0-9]*
```

Ett interaktivt syntax diagram är också tillgängligt:

> [!div class="nextstepaction"]
> [OData-syntax diagram för Azure Kognitiv sökning](https://azuresearch.github.io/odata-syntax-diagram/#field_path)

> [!NOTE]
> Se [referens för OData-uttryck för Azure kognitiv sökning](search-query-odata-syntax-reference.md) för den fullständiga ebnf.

En fält Sök väg består av en eller flera **identifierare** avgränsade med snedstreck. Varje identifierare är en sekvens med tecken som måste inledas med en ASCII-bokstav eller ett under streck och får bara innehålla ASCII-bokstäver, siffror eller under streck. Bokstäverna kan vara versaler eller gemener.

En identifierare kan referera antingen till namnet på ett fält eller till en **intervall variabel** i kontexten för ett [samlings uttryck](search-query-odata-collection-operators.md) ( `any` eller `all` ) i ett filter. En Range-variabel är som en loop-variabel som representerar det aktuella elementet i mängden. För komplexa samlingar representerar variabeln ett objekt, vilket är anledningen till att du kan använda fält Sök vägar för att referera till underordnade fält i variabeln. Detta motsvarar punkt notation i många programmeringsspråk.

Exempel på fält Sök vägar visas i följande tabell:

| Fält Sök väg | Beskrivning |
| --- | --- |
| `HotelName` | Refererar till ett fält på översta nivån i indexet |
| `Address/City` | Refererar till `City` under fältet för ett komplext fält i indexet, `Address` är av typen `Edm.ComplexType` i det här exemplet |
| `Rooms/Type` | Refererar till `Type` under fältet för ett komplext samlings fält i indexet, `Rooms` är av typen `Collection(Edm.ComplexType)` i det här exemplet |
| `Stores/Address/Country` | Refererar till `Country` under fältet för `Address` under fältet för ett komplext samlings fält i indexet, `Stores` är av typen `Collection(Edm.ComplexType)` och `Address` är av typen `Edm.ComplexType` i det här exemplet |
| `room/Type` | Refererar till `Type` under fältet för `room` variabeln Range, till exempel i filter uttrycket `Rooms/any(room: room/Type eq 'deluxe')` |
| `store/Address/Country` | Refererar till `Country` under fältet i `Address` under fältet för `store` variabeln Range, till exempel i filter uttrycket `Stores/any(store: store/Address/Country eq 'Canada')` |

Betydelsen av en fält Sök väg varierar beroende på kontexten. I filter refererar en fält Sök väg till värdet för en *enda instans* av ett fält i det aktuella dokumentet. I andra sammanhang, t. ex. **$OrderBy**, **$Select** eller i [fältet sökning i den fullständiga Lucene-syntaxen](query-lucene-syntax.md#bkmk_fields), refererar en fält Sök väg till själva fältet. Denna skillnad har vissa följder för hur du använder fält Sök vägar i filter.

Överväg fält Sök vägen `Address/City` . I ett filter refererar detta till en enda stad för det aktuella dokumentet, t. ex. "San Francisco". `Rooms/Type`Till skillnad från, refererar till `Type` under fältet för många rum (som "standard" för det första rummet, "Deluxe" för det andra rummet osv.). Eftersom `Rooms/Type` inte refererar till en *enskild instans* av det underordnade fältet `Type` , kan den inte användas direkt i ett filter. Om du i stället vill filtrera efter rums typ använder du ett [lambda-uttryck](search-query-odata-collection-operators.md) med en intervall variabel, så här:

```odata
Rooms/any(room: room/Type eq 'deluxe')
```

I det här exemplet visas variabeln Range `room` i `room/Type` fält Sök vägen. På så sätt `room/Type` refererar du till typen av aktuellt rum i det aktuella dokumentet. Det här är en enskild instans av `Type` under fältet, så den kan användas direkt i filtret.

### <a name="using-field-paths"></a>Använda fält Sök vägar

Fält Sök vägar används i många parametrar för [Azure KOGNITIV sökning REST-API: er](/rest/api/searchservice/). I följande tabell visas alla platser där de kan användas, samt eventuella begränsningar för användningen:

| API | Parameternamn | Begränsningar |
| --- | --- | --- |
| [Skapa](/rest/api/searchservice/create-index) eller [Uppdatera](/rest/api/searchservice/update-index) index | `suggesters/sourceFields` | Inget |
| [Skapa](/rest/api/searchservice/create-index) eller [Uppdatera](/rest/api/searchservice/update-index) index | `scoringProfiles/text/weights` | Kan endast referera till **sökbara** fält |
| [Skapa](/rest/api/searchservice/create-index) eller [Uppdatera](/rest/api/searchservice/update-index) index | `scoringProfiles/functions/fieldName` | Kan endast referera till **filter** bara fält |
| [Sök](/rest/api/searchservice/search-documents) | `search` När `queryType` är `full` | Kan endast referera till **sökbara** fält |
| [Sök](/rest/api/searchservice/search-documents) | `facet` | Kan endast referera till **fasettiska** fält |
| [Sök](/rest/api/searchservice/search-documents) | `highlight` | Kan endast referera till **sökbara** fält |
| [Sök](/rest/api/searchservice/search-documents) | `searchFields` | Kan endast referera till **sökbara** fält |
| [Föreslå](/rest/api/searchservice/suggestions) och [komplettera automatiskt](/rest/api/searchservice/autocomplete) | `searchFields` | Kan endast referera till fält som ingår i en [förslags ställare](index-add-suggesters.md) |
| [Sök](/rest/api/searchservice/search-documents), [föreslå](/rest/api/searchservice/suggestions)och [komplettera automatiskt](/rest/api/searchservice/autocomplete) | `$filter` | Kan endast referera till **filter** bara fält |
| [Sök](/rest/api/searchservice/search-documents) och [föreslå](/rest/api/searchservice/suggestions) | `$orderby` | Kan endast referera till **sorterbara** fält |
| [Sök](/rest/api/searchservice/search-documents), [föreslå](/rest/api/searchservice/suggestions)och [uppslag](/rest/api/searchservice/lookup-document) | `$select` | Kan endast referera till **hämtnings** bara fält |

## <a name="constants"></a>Konstanter

Konstanter i OData är litterala värden för en specifik EDM-typ ( [Entity Data Model](/dotnet/framework/data/adonet/entity-data-model) ). Se [data typer som stöds](/rest/api/searchservice/supported-data-types) för en lista över typer som stöds i Azure kognitiv sökning. Konstanter för samlings typer stöds inte.

I följande tabell visas exempel på konstanter för var och en av de data typer som stöds av Azure Kognitiv sökning:

| Datatyp | Exempel på konstanter |
| --- | --- |
| `Edm.Boolean` | `true`, `false` |
| `Edm.DateTimeOffset` | `2019-05-06T12:30:05.451Z` |
| `Edm.Double` | `3.14159`, `-1.2e7`, `NaN`, `INF`, `-INF` |
| `Edm.GeographyPoint` | `geography'POINT(-122.131577 47.678581)'` |
| `Edm.GeographyPolygon` | `geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'` |
| `Edm.Int32` | `123`, `-456` |
| `Edm.Int64` | `283032927235` |
| `Edm.String` | `'hello'` |

### <a name="escaping-special-characters-in-string-constants"></a>Hoppar över specialtecken i strängkonstant

Sträng konstanter i OData är avgränsade med enkla citat tecken. Om du behöver skapa en fråga med en strängkonstant som kanske bara innehåller enkla citat tecken kan du undanta de inbäddade citat tecknen genom att dubblera dem.

Till exempel skulle en fras med en oformaterad apostrof som "Alices bil" visas i OData som strängkonstant `'Alice''s car'` .

> [!IMPORTANT]
> När du skapar filter program mässigt är det viktigt att komma ihåg att undvika sträng konstanter som kommer från användarindata. Detta är att minska risken för [injektions attacker](https://wikipedia.org/wiki/SQL_injection), särskilt när du använder filter för att implementera [säkerhets trimning](search-security-trimming-for-azure-search.md).

### <a name="constants-syntax"></a>Konstant syntax

Följande EBNF ([utökade Backus-Naur form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definierar grammatiken för de flesta konstanter som visas i tabellen ovan. Grammatiken för geo-spatial-typer finns i [OData geo-spatial-funktioner i Azure kognitiv sökning](search-query-odata-geo-spatial-functions.md).

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
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
```

Ett interaktivt syntax diagram är också tillgängligt:

> [!div class="nextstepaction"]
> [OData-syntax diagram för Azure Kognitiv sökning](https://azuresearch.github.io/odata-syntax-diagram/#constant)

> [!NOTE]
> Se [referens för OData-uttryck för Azure kognitiv sökning](search-query-odata-syntax-reference.md) för den fullständiga ebnf.

## <a name="building-expressions-from-field-paths-and-constants"></a>Skapa uttryck från fält Sök vägar och konstanter

Fält Sök vägar och konstanter är den mest grundläggande delen av ett OData-uttryck, men de är redan fullständiga uttryck. I själva verket är **$Select** -parametern i Azure kognitiv sökning inget, men en kommaavgränsad lista över fält Sök vägar, och **$OrderBy** är inte mycket mer komplicerad än **$Select**. Om du har ett fält av typen `Edm.Boolean` i ditt index kan du till och med skriva ett filter som inte är något, utan fältets sökväg. Konstanterna `true` och `false` är också giltiga filter.

I de flesta fall behöver du dock mer komplexa uttryck som refererar till mer än ett fält och konstant. Dessa uttryck är inbyggda på olika sätt beroende på parametern.

Följande EBNF ([utökad Backus-Naur form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definierar grammatiken för parametrarna **$filter**, **$OrderBy** och **$Select** . Dessa är skapade med enklare uttryck som refererar till fält Sök vägar och konstanter:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
filter_expression ::= boolean_expression

order_by_expression ::= order_by_clause(',' order_by_clause)*

select_expression ::= '*' | field_path(',' field_path)*
```

Ett interaktivt syntax diagram är också tillgängligt:

> [!div class="nextstepaction"]
> [OData-syntax diagram för Azure Kognitiv sökning](https://azuresearch.github.io/odata-syntax-diagram/#filter_expression)

> [!NOTE]
> Se [referens för OData-uttryck för Azure kognitiv sökning](search-query-odata-syntax-reference.md) för den fullständiga ebnf.

Parametrarna **$OrderBy** och **$Select** är båda kommaavgränsade listor med enklare uttryck. Parametern **$filter** är ett booleskt uttryck som består av enklare del uttryck. Dessa under uttryck kombineras med logiska operatorer som [ `and` , `or` , `not` och ](search-query-odata-logical-operators.md), jämförelse operatorer som [ `eq` ,, `lt` `gt` och så](search-query-odata-comparison-operators.md)vidare, och samlings operatorer som [ `any` och `all` ](search-query-odata-collection-operators.md).

Parametrarna **$filter**, **$OrderBy** och **$Select** visas mer ingående i följande artiklar:

- [OData $filter-syntax i Azure Kognitiv sökning](search-query-odata-filter.md)
- [OData $orderby-syntax i Azure Kognitiv sökning](search-query-odata-orderby.md)
- [OData $select-syntax i Azure Kognitiv sökning](search-query-odata-select.md)

## <a name="see-also"></a>Se även  

- [Fasettisk navigering i Azure Kognitiv sökning](search-faceted-navigation.md)
- [Filter i Azure Kognitiv sökning](search-filters.md)
- [Sök efter dokument &#40;Azure Kognitiv sökning REST API&#41;](/rest/api/searchservice/Search-Documents)
- [Lucene-frågesyntax](query-lucene-syntax.md)
- [Enkel frågesyntax i Azure Kognitiv sökning](query-simple-syntax.md)