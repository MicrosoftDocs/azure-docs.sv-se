---
title: OData-sökning. score-funktions referens
titleSuffix: Azure Cognitive Search
description: Syntax och referens dokumentation för att använda funktionen search. score i Azure Kognitiv sökning frågor.
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
ms.openlocfilehash: 0a84d0310573a1210e21157102a445fff9244782
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88923984"
---
# <a name="odata-searchscore-function-in-azure-cognitive-search"></a>OData- `search.score` funktion i Azure kognitiv sökning

När du skickar en fråga till Azure Kognitiv sökning utan parametern [ **$OrderBy**](search-query-odata-orderby.md), sorteras resultaten som kommer tillbaka i fallande ordning efter relevans-poäng. Även om du använder **$OrderBy**, kommer relevans poängen att användas för att avbryta band som standard. Men ibland är det praktiskt att använda relevans poängen som ett inledande sorterings villkor och andra villkor som slip-Break. `search.score`Funktionen gör det möjligt att göra detta.

## <a name="syntax"></a>Syntax

Syntaxen för `search.score` i **$OrderBy** är `search.score()` . Funktionen `search.score` tar inga parametrar. Den kan användas med `asc` `desc` specifikationen eller sorterings ordningen, precis som andra satser i **$OrderBy** parameter. Den kan visas var som helst i listan med sorterings villkor.

## <a name="example"></a>Exempel

Sortera hotell i fallande ordning efter `search.score` och och `rating` sedan i stigande ordning efter avstånd från givna koordinater, så att de två hotellen med identiska klassificeringar visas först:

```odata-filter-expr
    search.score() desc,rating desc,geo.distance(location, geography'POINT(-122.131577 47.678581)') asc
```

## <a name="next-steps"></a>Nästa steg  

- [OData uttrycks språk översikt för Azure Kognitiv sökning](query-odata-filter-orderby-syntax.md)
- [Syntax-referens för OData-uttryck för Azure Kognitiv sökning](search-query-odata-syntax-reference.md)
- [Sök efter dokument &#40;Azure Kognitiv sökning EST API&#41;](/rest/api/searchservice/Search-Documents)