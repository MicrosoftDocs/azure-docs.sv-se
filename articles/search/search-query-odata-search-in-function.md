---
title: OData search.in-funktions referens
titleSuffix: Azure Cognitive Search
description: Syntax och referens dokumentation för att använda funktionen search.in i Azure Kognitiv sökning-frågor.
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
ms.openlocfilehash: 9ad6f89392846564631b70f0acfb5658a050be80
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88922828"
---
# <a name="odata-searchin-function-in-azure-cognitive-search"></a>OData- `search.in` funktion i Azure kognitiv sökning

Ett vanligt scenario i [OData filter-uttryck](query-odata-filter-orderby-syntax.md) är att kontrol lera om ett enskilt fält i varje dokument är lika med ett av många möjliga värden. Detta är till exempel hur vissa program implementerar [säkerhets trimning](search-security-trimming-for-azure-search.md) – genom att kontrol lera ett fält som innehåller ett eller flera huvud namns-ID: n mot en lista över huvud namns-ID: n som representerar den användare som utfärdar frågan. Ett sätt att skriva en fråga som detta är att använda [`eq`](search-query-odata-comparison-operators.md) [`or`](search-query-odata-logical-operators.md) operatorerna och:

```odata-filter-expr
    group_ids/any(g: g eq '123' or g eq '456' or g eq '789')
```

Det finns dock ett kortare sätt att skriva på detta med hjälp av `search.in` funktionen:

```odata-filter-expr
    group_ids/any(g: search.in(g, '123, 456, 789'))
```

> [!IMPORTANT]
> Förutom att vara kortare och enklare att läsa, `search.in` ger du också [prestanda för delar](#bkmk_performance) och undviker vissa [storleks begränsningar för filter](search-query-odata-filter.md#bkmk_limits) när det finns hundratals eller till och med tusentals värden som ska ingå i filtret. Av den anledningen rekommenderar vi starkt att du använder `search.in` i stället för en mer komplex disknutning av likhets uttryck.

> [!NOTE]
> I version 4,01 av OData standard har nyligen lanserat [ `in` operatorn](https://docs.oasis-open.org/odata/odata/v4.01/cs01/part2-url-conventions/odata-v4.01-cs01-part2-url-conventions.html#_Toc505773230), som har samma beteende som `search.in` funktionen i Azure kognitiv sökning. Azure Kognitiv sökning stöder dock inte den här operatorn, så du måste använda `search.in` funktionen i stället.

## <a name="syntax"></a>Syntax

Följande EBNF ([utökad Backus-Naur form](https://en.wikipedia.org/wiki/Extended_Backus–Naur_form)) definierar grammatiken för `search.in` funktionen:

<!-- Upload this EBNF using https://bottlecaps.de/rr/ui to create a downloadable railroad diagram. -->

```
search_in_call ::=
    'search.in(' variable ',' string_literal(',' string_literal)? ')'
```

Ett interaktivt syntax diagram är också tillgängligt:

> [!div class="nextstepaction"]
> [OData-syntax diagram för Azure Kognitiv sökning](https://azuresearch.github.io/odata-syntax-diagram/#search_in_call)

> [!NOTE]
> Se [referens för OData-uttryck för Azure kognitiv sökning](search-query-odata-syntax-reference.md) för den fullständiga ebnf.

`search.in`Funktionen testar om ett angivet sträng fält eller intervall variabel är lika med en av en specifik lista med värden. Likheten mellan variabeln och varje värde i listan bestäms av Skift läges känsligt, på samma sätt som för `eq` operatorn. Därför är ett uttryck som `search.in(myfield, 'a, b, c')` motsvarar `myfield eq 'a' or myfield eq 'b' or myfield eq 'c'` , men det `search.in` ger mycket bättre prestanda.

Det finns två överlagringar av `search.in` funktionen:

- `search.in(variable, valueList)`
- `search.in(variable, valueList, delimiters)`

Parametrarna definieras i följande tabell:

| Parameternamn | Typ | Beskrivning |
| --- | --- | --- |
| `variable` | `Edm.String` | En sträng fält referens (eller en intervall variabel över ett sträng samlings fält i fallet där `search.in` används inuti ett `any` eller `all` -uttryck). |
| `valueList` | `Edm.String` | En sträng som innehåller en avgränsad lista med värden som ska matchas mot `variable` parametern. Om `delimiters` parametern inte anges, är standard avgränsarna blank steg och kommatecken. |
| `delimiters` | `Edm.String` | En sträng där varje tecken behandlas som en avgränsare när parametern tolkas `valueList` . Standardvärdet för den här parametern är `' ,'` vilket innebär att alla värden med blank steg och/eller kommatecken mellan dem kommer att separeras. Om du behöver använda andra avgränsare än blank steg och kommatecken eftersom värdena innehåller dessa tecken kan du ange alternativa avgränsare, som `'|'` i den här parametern. |

<a name="bkmk_performance"></a>

### <a name="performance-of-searchin"></a>Prestanda för `search.in`

Om du använder `search.in` kan du förväntar dig svars tid under andra när den andra parametern innehåller en lista över hundratals eller tusentals värden. Det finns ingen uttrycklig gräns för antalet objekt som du kan skicka till `search.in` , även om du fortfarande är begränsad till den maximala storleken för begäran. Svars tiden kommer dock att växa när antalet värden växer.

## <a name="examples"></a>Exempel

Hitta alla hotell med namn som motsvarar antingen "Sea View Motel" eller "budget hotell". Fraser innehåller blank steg, vilket är en standard avgränsare. Du kan ange en alternativ avgränsare i enkla citat tecken som den tredje sträng parametern:  

```odata-filter-expr
    search.in(HotelName, 'Sea View motel,Budget hotel', ',')
```

Hitta alla hotell med namn som är lika med "Sea View Motel" eller "budget hotellet", separerade med "|"):

```odata-filter-expr
    search.in(HotelName, 'Sea View motel|Budget hotel', '|')
```

Hitta alla hotell med rum som har taggen "WiFi" eller "tub":

```odata-filter-expr
    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'wifi, tub')))
```

Hitta en matchning på fraser i en samling, t. ex. "uppvärmd hand duks-rack" eller "Hairdryer ingår" i taggar.

```odata-filter-expr
    Rooms/any(room: room/Tags/any(tag: search.in(tag, 'heated towel racks,hairdryer included', ','))
```

Hitta alla hotell utan taggen ' Motel ' eller ' cabin':

```odata-filter-expr
    Tags/all(tag: not search.in(tag, 'motel, cabin'))
```

## <a name="next-steps"></a>Nästa steg  

- [Filter i Azure Kognitiv sökning](search-filters.md)
- [OData uttrycks språk översikt för Azure Kognitiv sökning](query-odata-filter-orderby-syntax.md)
- [Syntax-referens för OData-uttryck för Azure Kognitiv sökning](search-query-odata-syntax-reference.md)
- [Sök efter dokument &#40;Azure Kognitiv sökning REST API&#41;](/rest/api/searchservice/Search-Documents)