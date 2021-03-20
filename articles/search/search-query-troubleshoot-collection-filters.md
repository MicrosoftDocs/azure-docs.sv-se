---
title: Felsöka OData-samlings filter
titleSuffix: Azure Cognitive Search
description: Lär dig mer om metoder för att lösa OData Collection filter-fel i Azure Kognitiv sökning-frågor.
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
ms.openlocfilehash: 3050f701c11773207aa6054d4d08d908d87b2ce7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "88932074"
---
# <a name="troubleshooting-odata-collection-filters-in-azure-cognitive-search"></a>Felsöka OData Collection filter i Azure Kognitiv sökning

Om du vill [filtrera](query-odata-filter-orderby-syntax.md) på samlings fält i Azure kognitiv sökning kan du använda [ `any` `all` operatorerna och](search-query-odata-collection-operators.md) tillsammans med **lambda-uttryck**. Ett lambda-uttryck är ett del filter som används för varje element i en samling.

Alla funktioner i filter uttryck är inte tillgängliga i ett lambda-uttryck. Vilka funktioner som är tillgängliga varierar beroende på data typen för det samlings fält som du vill filtrera. Detta kan resultera i ett fel om du försöker använda en funktion i ett lambda-uttryck som inte stöds i den kontexten. Om du stöter på sådana fel och försöker skriva ett komplext filter över samlings fält kan du felsöka problemet med hjälp av den här artikeln.

## <a name="common-collection-filter-errors"></a>Filter fel för vanliga samlingar

I följande tabell visas fel meddelanden som du kan stöta på när du försöker köra ett samlings filter. Felen inträffar när du använder en funktion i filter uttryck som inte stöds i ett lambda-uttryck. Varje fel ger viss vägledning om hur du kan skriva om filtret för att undvika felet. Tabellen innehåller också en länk till relevant avsnitt i den här artikeln som innehåller mer information om hur du undviker det felet.

| Felmeddelande | Tillståndet | Mer information finns i |
| --- | --- | --- |
| Funktionen ismatch har inga parametrar som är kopplade till intervall variabeln. Endast obundna fält referenser stöds i lambda-uttryck (any eller all). Ändra filtret så att funktionen "ismatch" är utanför lambda-uttrycket och försök igen. | Använda `search.ismatch` eller `search.ismatchscoring` inuti ett lambda-uttryck | [Regler för filtrering av komplexa samlingar](#bkmk_complex) |
| Ogiltigt lambda-uttryck. Ett test har hittats för likhet eller olikhet där motsatt förväntades i ett lambda-uttryck som itererar över ett fält av typen Collection (EDM. String). För "any" använder du uttryck i formatet "x EQ y" eller "search.in (...)". För "alla" använder du uttryck i formatet "x Ne y", "not (x EQ y)" eller "not search.in (...)". | Filtrering i ett fält av typen `Collection(Edm.String)` | [Regler för filtrering av sträng samlingar](#bkmk_strings) |
| Ogiltigt lambda-uttryck. En form av ett komplext boolean-uttryck som inte stöds hittades. För "any" använder du uttryck som är "ORs of ANDs", även kallat Disjunctive normal form. Till exempel: "(a och b) eller (c och d)" där a, b, c, och d är jämförelse-eller likhets under uttryck. För all, använder du uttryck som är "ANDs of ORs", även kallat Conjunctive normal form. Exempel: "(a eller b) och (c eller d)" där a, b, c och d är jämförelse-och olikhets under uttryck. Exempel på jämförelse uttryck: "x gt 5", "x Le 2". Exempel på ett likhets uttryck: x EQ 5. Exempel på ett likhets uttryck: x Ne 5. | Filtrering av fält av typen `Collection(Edm.DateTimeOffset)` , `Collection(Edm.Double)` , `Collection(Edm.Int32)` , eller `Collection(Edm.Int64)` | [Regler för filtrering av jämförbara samlingar](#bkmk_comparables) |
| Ogiltigt lambda-uttryck. En användning av Geo. Distance () eller Geo () som inte stöds har påträffats i ett lambda-uttryck som itererar över ett fält av typen Collection (EDM. GeographyPoint). För "any", se till att du jämför geo. Distance () med operatorerna "lt" eller "Le" och se till att all användning av Geo. intersects () inte är negerad. För all, se till att du jämför geo. Distance () med operatorerna "gt" eller "ge" och se till att all användning av Geo. intersects () är negationd. | Filtrering i ett fält av typen `Collection(Edm.GeographyPoint)` | [Regler för filtrering av GeographyPoint-samlingar](#bkmk_geopoints) |
| Ogiltigt lambda-uttryck. Komplexa booleska uttryck stöds inte i lambda-uttryck som itererar över fält av typen Collection (EDM. GeographyPoint). För "any", koppla under uttryck med "eller"; "och" stöds inte. För all kan du gå med under uttryck med "och"; eller stöds inte. | Filtrering på fält av typen `Collection(Edm.String)` eller `Collection(Edm.GeographyPoint)` | [Regler för filtrering av sträng samlingar](#bkmk_strings) <br/><br/> [Regler för filtrering av GeographyPoint-samlingar](#bkmk_geopoints) |
| Ogiltigt lambda-uttryck. En jämförelse operator (en av "lt", "Le", "gt" eller "ge") hittades. Endast likhets operatorer tillåts i lambda-uttryck som itererar över fält av typen Collection (EDM. String). För "any" använder du uttryck i formatet ' x EQ '. För "alla" använder du uttryck i formatet "x Ne y" eller "not (x EQ y)". | Filtrering i ett fält av typen `Collection(Edm.String)` | [Regler för filtrering av sträng samlingar](#bkmk_strings) |

<a name="bkmk_examples"></a>

## <a name="how-to-write-valid-collection-filters"></a>Så här skriver du giltiga samlings filter

Reglerna för att skriva giltiga samlings filter skiljer sig åt för varje datatyp. I följande avsnitt beskrivs reglerna genom att visa exempel på vilka filter funktioner som stöds och vilka som inte är:

- [Regler för filtrering av sträng samlingar](#bkmk_strings)
- [Regler för filtrering av booleska samlingar](#bkmk_bools)
- [Regler för filtrering av GeographyPoint-samlingar](#bkmk_geopoints)
- [Regler för filtrering av jämförbara samlingar](#bkmk_comparables)
- [Regler för filtrering av komplexa samlingar](#bkmk_complex)

<a name="bkmk_strings"></a>

## <a name="rules-for-filtering-string-collections"></a>Regler för filtrering av sträng samlingar

I lambda-uttryck för sträng samlingar är de enda jämförelse operatorer som kan användas `eq` och `ne` .

> [!NOTE]
> Azure kognitiv sökning stöder inte `lt` / `le` / `gt` / `ge` operatorer för strängar, vare sig inom eller utanför ett lambda-uttryck.

Bröd texten i en `any` kan bara testa för jämställdhet medan bröd texten i en `all` bara kan testa för olikheter.

Det är också möjligt att kombinera flera uttryck via `or` i bröd texten för en `any` , och via `and` i bröd texten i en `all` . Eftersom `search.in` funktionen motsvarar att kombinera likhets kontroller med `or` , är det också tillåtet i bröd texten i en `any` . Omvänt `not search.in` tillåts i bröd texten i en `all` .

Dessa uttryck är till exempel tillåtna:

- `tags/any(t: t eq 'books')`
- `tags/any(t: search.in(t, 'books, games, toys'))`
- `tags/all(t: t ne 'books')`
- `tags/all(t: not (t eq 'books'))`
- `tags/all(t: not search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' or t eq 'games')`
- `tags/all(t: t ne 'books' and not (t eq 'games'))`

dessa uttryck är inte tillåtna:

- `tags/any(t: t ne 'books')`
- `tags/any(t: not search.in(t, 'books, games, toys'))`
- `tags/all(t: t eq 'books')`
- `tags/all(t: search.in(t, 'books, games, toys'))`
- `tags/any(t: t eq 'books' and t ne 'games')`
- `tags/all(t: t ne 'books' or not (t eq 'games'))`

<a name="bkmk_bools"></a>

## <a name="rules-for-filtering-boolean-collections"></a>Regler för filtrering av booleska samlingar

Typen `Edm.Boolean` stöder endast `eq` `ne` operatorerna och. Det gör att det inte är mycket viktigt att tillåta att kombinera sådana satser som kontrollerar samma intervall variabel med `and` / `or` eftersom det alltid skulle leda till tautologies eller motstridiga värden.

Här följer några exempel på filter för booleska samlingar som tillåts:

- `flags/any(f: f)`
- `flags/all(f: f)`
- `flags/any(f: f eq true)`
- `flags/any(f: f ne true)`
- `flags/all(f: not f)`
- `flags/all(f: not (f eq true))`

Till skillnad från sträng samlingar har booleska samlingar inga gränser för vilken operator som kan användas i vilken typ av lambda-uttryck. Både `eq` och `ne` kan användas i bröd texten i `any` eller `all` .

Uttryck som nedan är inte tillåtna för booleska samlingar:

- `flags/any(f: f or not f)`
- `flags/any(f: f or f)`
- `flags/all(f: f and not f)`
- `flags/all(f: f and f eq true)`

<a name="bkmk_geopoints"></a>

## <a name="rules-for-filtering-geographypoint-collections"></a>Regler för filtrering av GeographyPoint-samlingar

Värden av typen `Edm.GeographyPoint` i en samling kan inte jämföras direkt med varandra. De måste i stället användas som parametrar till- `geo.distance` och- `geo.intersects` funktionerna. `geo.distance`Funktionen i sin tur måste jämföras med ett avstånds värde med hjälp av en av jämförelse operatorerna,, `lt` `le` `gt` eller `ge` . Dessa regler gäller även för icke-Collection-EDM. GeographyPoint fält.

Precis som med sträng samlingar `Edm.GeographyPoint` har samlingar vissa regler för hur geo-spatial-funktionerna kan användas och kombineras i olika typer av lambda-uttryck:

- Vilka jämförelse operatorer du kan använda med `geo.distance` funktionen beror på typen av lambda-uttryck. För `any` kan du bara använda `lt` eller `le` . För `all` kan du bara använda `gt` eller `ge` . Du kan negera uttryck som rör `geo.distance` , men du måste ändra jämförelse operatorn ( `geo.distance(...) lt x` blir `not (geo.distance(...) ge x)` och `geo.distance(...) le x` blir `not (geo.distance(...) gt x)` ).
- `all` `geo.intersects` Funktionen måste vara negationd i bröd texten i en. `any` `geo.intersects` Funktionen får däremot inte vara negationd i bröd texten i en.
- I bröd texten i ett `any` geo-spatial uttryck kan kombineras med hjälp av `or` . I bröd texten i ett kan `all` sådana uttryck kombineras med `and` .

Ovanstående begränsningar finns av liknande orsaker som begränsningen likhet/olikhet i sträng samlingar. Se [förstå OData Collection filter i Azure kognitiv sökning](search-query-understand-collection-filters.md) för en djupare titt på dessa orsaker.

Här följer några exempel på filter för `Edm.GeographyPoint` samlingar som tillåts:

- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: not (geo.distance(l, geography'POINT(-122 49)') ge 10) or geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') ge 10 and not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

Uttryck som nedan är inte tillåtna för `Edm.GeographyPoint` samlingar:

- `locations/any(l: l eq geography'POINT(-122 49)')`
- `locations/any(l: not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') gt 10)`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') lt 10)`
- `locations/any(l: geo.distance(l, geography'POINT(-122 49)') lt 10 and geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`
- `locations/all(l: geo.distance(l, geography'POINT(-122 49)') le 10 or not geo.intersects(l, geography'POLYGON((-122.031577 47.578581, -122.031577 47.678581, -122.131577 47.678581, -122.031577 47.578581))'))`

<a name="bkmk_comparables"></a>

## <a name="rules-for-filtering-comparable-collections"></a>Regler för filtrering av jämförbara samlingar

Det här avsnittet gäller för alla följande data typer:

- `Collection(Edm.DateTimeOffset)`
- `Collection(Edm.Double)`
- `Collection(Edm.Int32)`
- `Collection(Edm.Int64)`

Typer som `Edm.Int32` och `Edm.DateTimeOffset` stöder alla sex av jämförelse operatorerna:,,,, `eq` `ne` `lt` `le` `gt` och `ge` . Lambda-uttryck över samlingar av dessa typer kan innehålla enkla uttryck med någon av dessa operatorer. Detta gäller både `any` och `all` . Dessa filter är till exempel tillåtna:

- `ratings/any(r: r ne 5)`
- `dates/any(d: d gt 2017-08-24T00:00:00Z)`
- `not margins/all(m: m eq 3.5)`

Det finns dock begränsningar för hur jämförelse uttryck kan kombineras i mer komplexa uttryck i ett lambda-uttryck:

- Regler för `any` :
  - Enkla likhets uttryck kan inte kombineras med andra uttryck. Detta uttryck är till exempel tillåtet:
    - `ratings/any(r: r ne 5)`

    men det här uttrycket är inte:
    - `ratings/any(r: r ne 5 and r gt 2)`

    och även om det här uttrycket är tillåtet är det inte användbart eftersom villkoren överlappar varandra:
    - `ratings/any(r: r ne 5 or r gt 7)`
  - Enkla jämförelse uttryck som involverar,,, `eq` `lt` `le` `gt` eller `ge` kan kombineras med `and` / `or` . Exempel:
    - `ratings/any(r: r gt 2 and r le 5)`
    - `ratings/any(r: r le 5 or r gt 7)`
  - Jämförelse uttryck som kombineras med `and` (samverkar) kan kombineras ytterligare med `or` . Det här formuläret är känt i boolesk logik som "[Disjunctive normal form](https://en.wikipedia.org/wiki/Disjunctive_normal_form)" (DNF). Exempel:
    - `ratings/any(r: (r gt 2 and r le 5) or (r gt 7 and r lt 10))`
- Regler för `all` :
  - Enkla likhets uttryck kan inte kombineras med andra uttryck. Detta uttryck är till exempel tillåtet:
    - `ratings/all(r: r eq 5)`

    men det här uttrycket är inte:
    - `ratings/all(r: r eq 5 or r le 2)`

    och även om det här uttrycket är tillåtet är det inte användbart eftersom villkoren överlappar varandra:
    - `ratings/all(r: r eq 5 and r le 7)`
  - Enkla jämförelse uttryck som involverar,,, `ne` `lt` `le` `gt` eller `ge` kan kombineras med `and` / `or` . Exempel:
    - `ratings/all(r: r gt 2 and r le 5)`
    - `ratings/all(r: r le 5 or r gt 7)`
  - Jämförelse uttryck i kombination med `or` (disknutna) kan kombineras ytterligare med `and` . Det här formuläret är känt i boolesk logik som "[Conjunctive normal form](https://en.wikipedia.org/wiki/Conjunctive_normal_form)" (CNF). Exempel:
    - `ratings/all(r: (r le 2 or gt 5) and (r lt 7 or r ge 10))`

<a name="bkmk_complex"></a>

## <a name="rules-for-filtering-complex-collections"></a>Regler för filtrering av komplexa samlingar

Lambda-uttryck i komplexa samlingar stöder mycket mer flexibel syntax än lambda-uttryck över samlingar med primitiva typer. Du kan använda valfri filter konstruktion inuti ett lambda-uttryck som du kan använda utanför ett, med endast två undantag.

Först, funktionerna `search.ismatch` och `search.ismatchscoring` inte stöds i lambda-uttryck. Mer information finns i [förstå OData Collection filter i Azure kognitiv sökning](search-query-understand-collection-filters.md).

För det andra är det inte tillåtet att referera till fält som inte är *bundna* till variabeln Range (so-kallas *lediga variabler*). Anta till exempel följande två motsvarande OData filter-uttryck:

1. `stores/any(s: s/amenities/any(a: a eq 'parking')) and details/margin gt 0.5`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and details/margin gt 0.5))`

Det första uttrycket är tillåtet, medan det andra formuläret avvisas eftersom det `details/margin` inte är kopplat till intervall variabeln `s` .

Den här regeln utökar även uttryck som har variabler som är kopplade till en yttre omfattning. Sådana variabler är kostnads fria i förhållande till omfattningen där de förekommer. Till exempel tillåts det första uttrycket, medan det andra motsvarande uttrycket inte är tillåtet eftersom det `s/name` är kostnads fritt med avseende på intervall variabelns omfång `a` :

1. `stores/any(s: s/amenities/any(a: a eq 'parking') and s/name ne 'Flagship')`
1. `stores/any(s: s/amenities/any(a: a eq 'parking' and s/name ne 'Flagship'))`

Den här begränsningen bör inte vara ett problem i övningen eftersom det alltid är möjligt att konstruera filter så att lambda-uttryck bara innehåller kopplade variabler.

## <a name="cheat-sheet-for-collection-filter-rules"></a>Lathund-blad för samlings filter regler

I följande tabell sammanfattas reglerna för att skapa giltiga filter för varje samlings data typ.

[!INCLUDE [Limitations on OData lambda expressions in Azure Cognitive Search](../../includes/search-query-odata-lambda-limitations.md)]

Exempel på hur du skapar giltiga filter för varje ärende finns i [så här skriver du giltiga samlings filter](#bkmk_examples).

Om du ofta skriver filter och förstår reglerna från de första principerna, kan du läsa mer än att bara memorera dem genom att [förstå OData Collection filter i Azure kognitiv sökning](search-query-understand-collection-filters.md).

## <a name="next-steps"></a>Nästa steg  

- [Förstå OData Collection-filter i Azure Kognitiv sökning](search-query-understand-collection-filters.md)
- [Filter i Azure Kognitiv sökning](search-filters.md)
- [OData uttrycks språk översikt för Azure Kognitiv sökning](query-odata-filter-orderby-syntax.md)
- [Syntax-referens för OData-uttryck för Azure Kognitiv sökning](search-query-odata-syntax-reference.md)
- [Sök efter dokument &#40;Azure Kognitiv sökning REST API&#41;](/rest/api/searchservice/Search-Documents)