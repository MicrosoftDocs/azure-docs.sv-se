---
title: Förstå OData-samlings filter
titleSuffix: Azure Cognitive Search
description: Lär dig hur OData-Mechanics fungerar i Azure Kognitiv sökning-frågor, inklusive begränsningar och beteenden som är unika för samlingar.
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
ms.openlocfilehash: 6af0f2b5221a737687578e939c14cecf3be14509
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88932924"
---
# <a name="understanding-odata-collection-filters-in-azure-cognitive-search"></a>Förstå OData Collection-filter i Azure Kognitiv sökning

Om du vill [filtrera](query-odata-filter-orderby-syntax.md) på samlings fält i Azure kognitiv sökning kan du använda [ `any` `all` operatorerna och](search-query-odata-collection-operators.md) tillsammans med **lambda-uttryck**. Lambda-uttryck är booleska uttryck som refererar till en **intervall variabel**. `any` `all` Operatorerna och är likvärdiga med en `for` loop i de flesta programmeringsspråk, med variabeln Range som tar roll för loop-variabeln och lambda-uttrycket som bröd texten i slingan. Range-variabeln tar på det aktuella värdet för samlingen under iterationen av loopen.

Minst så fungerar det konceptuellt. I verkligheten implementerar Azure Kognitiv sökning filter på ett mycket annorlunda sätt för hur `for` slingor fungerar. Vi rekommenderar att skillnaden är osynlig för dig, men i vissa fall är det inte. Slut resultatet är att det finns regler som du måste följa när du skriver lambda-uttryck.

I den här artikeln förklaras varför reglerna för samlings filter finns genom att utforska hur Azure Kognitiv sökning kör dessa filter. Om du skriver avancerade filter med komplexa lambda-uttryck kan du hitta den här artikeln för att skapa din förståelse av vad som är möjligt med filter och varför.

Information om hur reglerna för samlings filter är, inklusive exempel, finns i [Felsöka OData Collection filter i Azure kognitiv sökning](search-query-troubleshoot-collection-filters.md).

## <a name="why-collection-filters-are-limited"></a>Varför samlings filter är begränsade

Det finns tre bakomliggande orsaker till varför inte alla filter funktioner stöds för alla typer av samlingar:

1. Endast vissa operatörer stöds för vissa data typer. Det är till exempel inte meningsfullt att jämföra de booleska värdena `true` och `false` använda `lt` , `gt` , och så vidare.
1. Azure Kognitiv sökning stöder inte **korrelerad sökning** i fält av typen `Collection(Edm.ComplexType)` .
1. Azure Kognitiv sökning använder inverterade index för att köra filter över alla typer av data, inklusive samlingar.

Den första orsaken är bara en följd av hur OData-språket och EDM-typ systemet definieras. De sista två förklaras i detalj i resten av den här artikeln.

## <a name="correlated-versus-uncorrelated-search"></a>Korrelerad kontra korrelerad sökning

När du tillämpar flera filter villkor för en samling komplexa objekt, **korreleras** villkoren eftersom de gäller för *varje objekt i samlingen*. Följande filter kommer till exempel att returnera hotell som har minst ett Deluxe-rum med en hastighet som är lägre än 100:

```odata-filter-expr
    Rooms/any(room: room/Type eq 'Deluxe Room' and room/BaseRate lt 100)
```

Om filtreringen var *korrelerad* kan filtret ovan returnera hotell där ett rum är Deluxe och ett annat rum har en bas taxa som är mindre än 100. Det skulle inte vara meningsfullt eftersom båda satserna i lambda-uttrycket gäller samma intervall variabel, nämligen `room` . Detta är anledningen till att sådana filter korreleras.

För full texts ökning finns det dock inget sätt att referera till en speciell intervall variabel. Om du använder fältet sökning för att utfärda en [fullständig Lucene-fråga](query-lucene-syntax.md) som den här:

```odata-filter-expr
    Rooms/Type:deluxe AND Rooms/Description:"city view"
```

Du kan få tillbaka hotell igen om ett rum är Deluxe och ett annat rum står "stads vy" i beskrivningen. Till exempel kan dokumentet nedan `Id` `1` matcha frågan:

```json
{
  "value": [
    {
      "Id": "1",
      "Rooms": [
        { "Type": "deluxe", "Description": "Large garden view suite" },
        { "Type": "standard", "Description": "Standard city view room" }
      ]
    },
    {
      "Id": "2",
      "Rooms": [
        { "Type": "deluxe", "Description": "Courtyard motel room" }
      ]
    }
  ]
}
```

Orsaken är att `Rooms/Type` alla analyserade villkor för `Rooms/Type` fältet i hela dokumentet och på samma sätt som `Rooms/Description` visas i tabellerna nedan.

Hur `Rooms/Type` lagras för full texts ökning:

| Villkor i `Rooms/Type` | Dokument-ID |
| --- | --- |
| modell | 1, 2 |
| standard | 1 |

Hur `Rooms/Description` lagras för full texts ökning:

| Villkor i `Rooms/Description` | Dokument-ID |
| --- | --- |
| courtyard | 2 |
| city | 1 |
| modernt | 1 |
| förstoring | 1 |
| Motel | 2 |
| rum | 1, 2 |
| standard | 1 |
| uppsättning | 1 |
| visa | 1 |

Till skillnad från filtret ovan, vilket i princip betyder att "matcha dokument där ett rum är lika med" `Type` Deluxe-rummet "och **att samma rum** har `BaseRate` mindre än 100", säger Sök frågan "matcha dokument där `Rooms/Type` termen" Deluxe "och `Rooms/Description` innehåller frasen" City ". Det finns inget begrepp för enskilda rum vars fält kan korreleras i det senare fallet.

> [!NOTE]
> Om du vill se stöd för korrelerad sökning som lagts till i Azure Kognitiv sökning, rösten för [det här röst alternativet för användare](https://feedback.azure.com/forums/263029-azure-search/suggestions/37735060-support-correlated-search-on-complex-collections).

## <a name="inverted-indexes-and-collections"></a>Inverterade index och samlingar

Du kanske har märkt att det finns mycket färre begränsningar för Lambda-uttryck i komplexa samlingar än för enkla samlingar som `Collection(Edm.Int32)` , `Collection(Edm.GeographyPoint)` och så vidare. Detta beror på att Azure Kognitiv sökning lagrar komplexa samlingar som faktiska samlingar av under dokument, medan enkla samlingar inte lagras som samlingar alls.

Du kan till exempel välja ett filter bara sträng samlings fält som `seasons` i ett index för en online-återförsäljare. Vissa dokument som överförs till det här indexet kan se ut så här:

```json
{
  "value": [
    {
      "id": "1",
      "name": "Hiking boots",
      "seasons": ["spring", "summer", "fall"]
    },
    {
      "id": "2",
      "name": "Rain jacket",
      "seasons": ["spring", "fall", "winter"]
    },
    {
      "id": "3",
      "name": "Parka",
      "seasons": ["winter"]
    }
  ]
}
```

Värdena i `seasons` fältet lagras i en struktur som kallas **inverterat index**, vilket ser ut ungefär så här:

| Period | Dokument-ID |
| --- | --- |
| upphängning | 1, 2 |
| Summer | 1 |
| kopplade | 1, 2 |
| vinter | 2, 3 |

Den här data strukturen är utformad för att svara på en fråga med bra hastighet: i vilka dokument visas en specifik term? Att besvara den här frågan fungerar mer som en vanlig likhets kontroll än en slinga över en samling. I själva verket är anledningen till varför för sträng samlingar, Azure Kognitiv sökning bara att tillåta `eq` som en jämförelse operator i ett lambda-uttryck för `any` .

Skapa upp från likhet, härnäst ska vi titta på hur det är möjligt att kombinera flera likhets kontroller i samma intervall variabel med `or` . Det fungerar tack vare algebra och [disfördelnings egenskapen för kvantifierare](https://en.wikipedia.org/wiki/Existential_quantification#Negation). Det här uttrycket:

```odata-filter-expr
    seasons/any(s: s eq 'winter' or s eq 'fall')
```

motsvarar:

```odata-filter-expr
    seasons/any(s: s eq 'winter') or seasons/any(s: s eq 'fall')
```

och var och en av de två `any` del uttrycken kan köras effektivt med det inverterade indexet. Detta uttryck är också tack vare [negations lagstiftningen för kvantifierare](https://en.wikipedia.org/wiki/Existential_quantification#Negation):

```odata-filter-expr
    seasons/all(s: s ne 'winter' and s ne 'fall')
```

motsvarar:

```odata-filter-expr
    not seasons/any(s: s eq 'winter' or s eq 'fall')
```

Därför är det möjligt att använda `all` med `ne` och `and` .

> [!NOTE]
> Även om informationen ligger utanför det här dokumentets omfattning, utökas samma principer till [avstånds-och skärnings test för samlingar med geo-spatiala punkter](search-query-odata-geo-spatial-functions.md) . Detta är varför, i `any` :
>
> - `geo.intersects` kan inte vara negationt
> - `geo.distance` måste jämföras med `lt` eller `le`
> - uttryck måste kombineras med `or` , inte `and`
>
> De omvända reglerna gäller för `all` .

En större mängd uttryck är tillåtna vid filtrering av samlingar med data typer som stöder `lt` `gt` operatorerna,, `le` och, `ge` till `Collection(Edm.Int32)` exempel. Mer specifikt kan du använda och `and` `or` i, förutsatt `any` att de underliggande jämförelse uttrycken kombineras till **intervall jämförelser** med `and` , som sedan kombineras med `or` . Den här strukturen av booleska uttryck kallas [Disjunctive normal form (DNF)](https://en.wikipedia.org/wiki/Disjunctive_normal_form), annars kallat "ORS of ANDs". Lambda-uttryck för `all` för dessa data typer måste däremot vara i [Conjunctive normal form (CNF)](https://en.wikipedia.org/wiki/Conjunctive_normal_form), annars kallat "ANDs of ORS". Med Azure Kognitiv sökning kan du jämföra dessa intervall eftersom de kan köra dem med inverterade index effektivt, precis som det kan göra snabba sökningar efter strängar.

Sammanfattnings vis är reglerna för tummen för vad som tillåts i ett lambda-uttryck:

- I `any` är *positiva kontroller* alltid tillåtna, som likhet, intervall jämförelser, `geo.intersects` eller `geo.distance` jämfört med `lt` eller `le` (Tänk på "nära", som likhet när det kommer att kontrol lera avståndet).
- Inuti `any` `or` tillåts alltid. Du kan `and` bara använda för data typer som kan uttrycka intervall kontroller och endast om du använder ORS av ANDs (DNF).
- I `all` är reglerna inverterade – endast *negativa kontroller* tillåts, du kan använda `and` Always och du kan `or` endast använda för intervall kontroller som uttryckts som ANDs av ORS (CNF).

I praktiken är dessa de typer av filter som du är mest troligt att använda ändå. Det är fortfarande bra att förstå gränserna för vad som är möjligt trots detta.

Ett exempel på vilka typer av filter som är tillåtna och vilka som inte är, finns i [så här skriver du giltiga samlings filter](search-query-troubleshoot-collection-filters.md#bkmk_examples).

## <a name="next-steps"></a>Nästa steg  

- [Felsöka OData Collection filter i Azure Kognitiv sökning](search-query-troubleshoot-collection-filters.md)
- [Filter i Azure Kognitiv sökning](search-filters.md)
- [OData uttrycks språk översikt för Azure Kognitiv sökning](query-odata-filter-orderby-syntax.md)
- [Syntax-referens för OData-uttryck för Azure Kognitiv sökning](search-query-odata-syntax-reference.md)
- [Sök efter dokument &#40;Azure Kognitiv sökning REST API&#41;](/rest/api/searchservice/Search-Documents)