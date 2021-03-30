---
title: Referens för OData jämförelse operator
titleSuffix: Azure Cognitive Search
description: Syntax och referens dokumentation för att använda OData-jämförelse operatorer (EQ, Ne, gt, lt, ge och Le) i Azure Kognitiv sökning-frågor.
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
ms.openlocfilehash: fc5803f96c30ea1df362676aa8c4104bb0b69db3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88934879"
---
# <a name="odata-comparison-operators-in-azure-cognitive-search---eq-ne-gt-lt-ge-and-le"></a>OData-jämförelse operatörer i Azure kognitiv sökning –,,,, `eq` `ne` `gt` `lt` `ge` och `le`

Den mest grundläggande åtgärden i ett [OData filter-uttryck](query-odata-filter-orderby-syntax.md) i Azure kognitiv sökning är att jämföra ett fält med ett angivet värde. Två typer av jämförelse är möjliga – likhets jämförelse och jämförelse av intervall. Du kan använda följande operatorer för att jämföra ett fält med ett konstant värde:

Likhets operatorer:

- `eq`: Testa om ett fält är **lika** med ett konstant värde
- `ne`: Testa om ett fält **inte är lika** med ett konstant värde

Intervall operatorer:

- `gt`: Testa om ett fält är **större än** ett konstant värde
- `lt`: Testa om ett fält är **mindre än** ett konstant värde
- `ge`: Testa om ett fält är **större än eller lika** med ett konstant värde
- `le`: Testa om ett fält är **mindre än eller lika** med ett konstant värde

Du kan använda intervall operatorerna i kombination med de [logiska operatörerna](search-query-odata-logical-operators.md) för att testa om ett fält är inom ett visst värde intervall. Se [exemplen](#examples) senare i den här artikeln.

> [!NOTE]
> Om du vill kan du ange det konstanta värdet på vänster sida av operatorn och fält namnet på den högra sidan. Innebörden av jämförelsen är omvänd för intervall operatorer. Om till exempel det konstanta värdet är till vänster, `gt` testar om det konstanta värdet är större än fältet. Du kan också använda jämförelse operatorer för att jämföra resultatet av en funktion, till exempel `geo.distance` , med ett värde. För booleska funktioner som till exempel `search.ismatch` Jämför resultatet med `true` eller `false` är valfritt.

## <a name="syntax"></a>Syntax

Följande EBNF ([utökad Backus-Naur form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definierar grammatiken i ett OData-uttryck som använder jämförelse operatorer.

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
comparison_expression ::=
    variable_or_function comparison_operator constant |
    constant comparison_operator variable_or_function

variable_or_function ::= variable | function_call

comparison_operator ::= 'gt' | 'lt' | 'ge' | 'le' | 'eq' | 'ne'
```

Ett interaktivt syntax diagram är också tillgängligt:

> [!div class="nextstepaction"]
> [OData-syntax diagram för Azure Kognitiv sökning](https://azuresearch.github.io/odata-syntax-diagram/#comparison_expression)

> [!NOTE]
> Se [referens för OData-uttryck för Azure kognitiv sökning](search-query-odata-syntax-reference.md) för den fullständiga ebnf.

Det finns två former av jämförelse uttryck. Den enda skillnaden mellan dem är huruvida konstanten visas till vänster eller höger i operatorn. Uttrycket på den andra sidan av operatorn måste vara en **variabel** eller ett funktions anrop. En variabel kan vara antingen ett fält namn eller en intervall variabel i fallet med ett lambda- [uttryck](search-query-odata-collection-operators.md).

## <a name="data-types-for-comparisons"></a>Data typer för jämförelser

Data typerna på båda sidor av en jämförelse operator måste vara kompatibla. Om till exempel den vänstra sidan är ett fält av typen `Edm.DateTimeOffset` måste den högra sidan vara en datum-och tids konstant. Numeriska data typer är mer flexibla. Du kan jämföra variabler och funktioner för en numerisk typ med konstanter av annan numerisk typ, med några begränsningar, enligt beskrivningen i följande tabell.

| Variabel eller funktions typ | Konstant värde typ | Begränsningar |
| --- | --- | --- |
| `Edm.Double` | `Edm.Double` | Jämförelse gäller [särskilda regler för `NaN` ](#special-case-nan) |
| `Edm.Double` | `Edm.Int64` | Konstant konverteras till `Edm.Double` , vilket resulterar i en precisions förlust för värden av stor storlek |
| `Edm.Double` | `Edm.Int32` | saknas |
| `Edm.Int64` | `Edm.Double` | Jämförelser till `NaN` , `-INF` eller `INF` är inte tillåtna |
| `Edm.Int64` | `Edm.Int64` | saknas |
| `Edm.Int64` | `Edm.Int32` | Konstanten konverteras till `Edm.Int64` före jämförelsen |
| `Edm.Int32` | `Edm.Double` | Jämförelser till `NaN` , `-INF` eller `INF` är inte tillåtna |
| `Edm.Int32` | `Edm.Int64` | saknas |
| `Edm.Int32` | `Edm.Int32` | saknas |

För jämförelser som inte är tillåtna, till exempel jämförelse av ett fält av typen `Edm.Int64` till `NaN` , returnerar Azure kognitiv sökning REST API fel meddelandet "http 400: felaktig begäran".

> [!IMPORTANT]
> Även om numeriska typ jämförelser är flexibla rekommenderar vi att du skriver jämförelser i filter så att det konstanta värdet är av samma datatyp som variabeln eller funktionen som den jämförs med. Detta är särskilt viktigt när du blandar flytt ALS-och heltals värden där implicita konverteringar som förlorar precision är möjliga.

<a name="special-case-nan"></a>

### <a name="special-cases-for-null-and-nan"></a>Särskilda fall för `null` och `NaN`

När du använder jämförelse operatorer är det viktigt att komma ihåg att alla fält som inte är samlings fält i Azure Kognitiv sökning kan vara `null` . I följande tabell visas alla möjliga resultat för ett jämförelse uttryck där endera sidan kan vara `null` :

| Operator | Resultat när endast fältet eller variabeln är `null` | Resultat när endast konstanten är `null` | Resultat när både fältet eller variabeln och konstanten är `null` |
| --- | --- | --- | --- |
| `gt` | `false` | HTTP 400: fel i felaktig begäran | HTTP 400: fel i felaktig begäran |
| `lt` | `false` | HTTP 400: fel i felaktig begäran | HTTP 400: fel i felaktig begäran |
| `ge` | `false` | HTTP 400: fel i felaktig begäran | HTTP 400: fel i felaktig begäran |
| `le` | `false` | HTTP 400: fel i felaktig begäran | HTTP 400: fel i felaktig begäran |
| `eq` | `false` | `false` | `true` |
| `ne` | `true` | `true` | `false` |

I sammanfattning `null` är bara lika med sig själv, och är inte mindre eller större än något annat värde.

Om ditt index har fält av typ `Edm.Double` och du överför `NaN` värden till dessa fält, måste du ha konto för det när du skriver filter. Azure Kognitiv sökning implementerar IEEE 754-standarden för hantering av `NaN` värden och jämförelser med sådana värden ger icke-uppenbara resultat, vilket visas i följande tabell.

| Operator | Resultat när minst en operand är `NaN` |
| --- | --- |
| `gt` | `false` |
| `lt` | `false` |
| `ge` | `false` |
| `le` | `false` |
| `eq` | `false` |
| `ne` | `true` |

I sammanfattningen `NaN` är inte lika med något värde, inklusive sig själv.

### <a name="comparing-geo-spatial-data"></a>Jämföra geo-spatiala data

Du kan inte direkt jämföra ett fält av typen `Edm.GeographyPoint` med ett konstant värde, men du kan använda `geo.distance` funktionen. Den här funktionen returnerar ett värde av typen `Edm.Double` , så att du kan jämföra den med en numerisk konstant för att filtrera baserat på avståndet från konstanta geo-spatiala koordinater. Se [exemplen](#examples) nedan.

### <a name="comparing-string-data"></a>Jämför sträng data

Strängar kan jämföras med filter för exakta matchningar med `eq` `ne` operatorerna och. Jämförelserna är Skift läges känsliga.

## <a name="examples"></a>Exempel

Matcha dokument där `Rating` fältet är mellan 3 och 5, inklusive:

```text
Rating ge 3 and Rating le 5
```

Matcha dokument där `Location` fältet är mindre än 2 kilo meter från den aktuella latitud och longitud:

```text
geo.distance(Location, geography'POINT(-122.031577 47.578581)') lt 2.0
```

Matcha dokument där `LastRenovationDate` fältet är större än eller lika med den 1 januari 2015, midnatt UTC:

```text
LastRenovationDate ge 2015-01-01T00:00:00.000Z
```

Matcha dokument där `Details/Sku` fältet inte är `null` :

```text
Details/Sku ne null
```

Matcha dokument för hotell där minst ett rum har typen "Deluxe-rum", där strängen för `Rooms/Type` fältet matchar filtret exakt:

```text
Rooms/any(room: room/Type eq 'Deluxe Room')
```

## <a name="next-steps"></a>Nästa steg  

- [Filter i Azure Kognitiv sökning](search-filters.md)
- [OData uttrycks språk översikt för Azure Kognitiv sökning](query-odata-filter-orderby-syntax.md)
- [Syntax-referens för OData-uttryck för Azure Kognitiv sökning](search-query-odata-syntax-reference.md)
- [Sök efter dokument &#40;Azure Kognitiv sökning REST API&#41;](/rest/api/searchservice/Search-Documents)