---
title: Frågetyper
titleSuffix: Azure Cognitive Search
description: Lär dig mer om de typer av frågor som stöds i Kognitiv sökning, inklusive fritext, filter, Autoavsluta och förslag, geo-sökning, system frågor och dokuments ökning.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/14/2020
ms.openlocfilehash: 7277ad060c57b44d633054c4fc4d29d151bd7192
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97400819"
---
# <a name="querying-in-azure-cognitive-search"></a>Fråga i Azure Kognitiv sökning

Azure Kognitiv sökning erbjuder ett omfattande frågespråk för att stödja ett brett utbud av scenarier, från fritext sökning, till mönster med hög angiven fråga. Den här artikeln beskriver fråge förfrågningar och vilka typer av frågor som du kan skapa.

I Kognitiv sökning är en fråga en fullständig specifikation av en tur och retur- **`search`** åtgärd, med parametrar som både informerar frågekörningen och formar svaret som kommer tillbaka. Parametrar och parser bestämmer vilken typ av fråga som begärs. Följande exempel är en text fråga med en boolesk operator med hjälp av [Sök dokumenten (REST API)](/rest/api/searchservice/search-documents)som riktar in samlingen [hotell-exempel-index](search-get-started-portal.md) dokument.

```http
POST https://[service name].search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "queryType": "simple"
    "search": "`New York` +restaurant",
    "searchFields": "Description, Address/City, Tags",
    "select": "HotelId, HotelName, Description, Rating, Address/City, Tags",
    "top": "10",
    "count": "true",
    "orderby": "Rating desc"
}
```

Parametrar som används vid frågekörningen är:

+ **`queryType`** ställer in parsern, som är antingen [standardvärdet för enkel fråga](search-query-simple-examples.md) (optimal för full texts ökning) eller den [fullständiga Lucene-frågeuttrycket](search-query-lucene-examples.md) som används för avancerade fråge konstruktioner som reguljära uttryck, närhets sökning, fuzzy och jokertecken, för att ge några.

+ **`search`** innehåller matchnings kriterier, vanligt vis hela termer eller fraser, med eller utan operatorer. Alla fält som har attribut som *sökbara* i index schemat är en kandidat för den här parametern.

+ **`searchFields`** begränsar frågans körning till vissa sökbara fält.

Parametrar som används för att forma svaret:

+ **`select`** anger vilka fält som ska returneras i svaret. Endast fält som har marker ATS som *hämtnings* bara i indexet kan användas i en SELECT-instruktion.

+ **`top`** Returnerar det angivna antalet dokument med optimal matchning. I det här exemplet returneras bara 10 träffar. Du kan använda Top och SKIP (visas inte) för att visa resultatet.

+ **`count`** visar hur många dokument i hela indexet som matchar övergripande, vilket kan vara mer än vad som returneras. 

+ **`orderby`** används om du vill sortera resultaten efter ett värde, till exempel en klassificering eller plats. Annars är standardvärdet att använda relevans poängen för att rangordna resultat. Ett fält måste ha attributet *sorterbart* för att vara en kandidat för den här parametern.

Listan ovan är representativ men inte fullständig. En fullständig lista över parametrar för en fråge förfrågan finns i [Sök efter dokument (REST API)](/rest/api/searchservice/search-documents).

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>Typer av frågor

Med några viktiga undantag itererar en förfrågan över inverterade index som är strukturerade för snabba sökningar, där en matchning kan hittas i ett potentiellt fält i ett valfritt antal sökdokument. I Kognitiv sökning är den primära metoden för att söka efter matchningar antingen full texts ökning eller filter, men du kan också implementera andra välkända Sök upplevelser som komplettera automatiskt eller Geo-Location-sökning. Resten av den här artikeln sammanfattar frågor i Kognitiv sökning och innehåller länkar till mer information och exempel.

## <a name="full-text-search"></a>Fulltextsökning

Om din Sök-App innehåller en sökruta som samlar in term indata, är full texts ökning förmodligen den fråga som fungerar. Full texts ökning accepterar villkor eller fraser som skickas i en **`search`** parameter i alla *sökbara* fält i ditt index. Valfria booleska operatorer i frågesträngen kan ange inkluderings-eller uteslutnings kriterier. Både den enkla parsern och fullständig parser har stöd för full texts ökning.

I Kognitiv sökning skapas full texts ökning på Apache Lucene-frågespråket. Frågesträngar i full texts ökning genomgår lexikal analys för att göra genomsökningar mer effektiva. Analysen innehåller gemener och versaler, och tar bort stopp ord som "The" och minskar termerna till primitiva rot formulär. Standard Analyzer är standard Lucene.

När matchnings termer påträffas utgör frågespråket ett sökdokument som innehåller matchningen med hjälp av dokument nyckeln eller ID: t för att sätta samman fält värden, rangordna dokumenten efter relevans och returnerar de översta 50 (som standard) i svaret eller ett annat nummer om du har angett **`top`** .

Om du implementerar fullständig texts ökning kan du förstå hur ditt innehåll är uppfyllt för att felsöka eventuella avvikelser i frågan. Frågor över avstavade strängar eller specialtecken kan krävas med hjälp av en annan analys än standard-Lucene för att säkerställa att indexet innehåller rätt tokens. Du kan åsidosätta standardvärdet med [språk analys](index-add-language-analyzers.md#language-analyzer-list) verktyg eller [särskilda analyser](index-add-custom-analyzers.md#AnalyzerTable) som ändrar lexikalisk analys. Ett exempel är ett [nyckelord](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) som behandlar hela innehållet i ett fält som en enskild token. Detta är användbart för data som post nummer, ID: n och vissa produkt namn. Mer information finns i [partiell terms ökning och mönster med specialtecken](search-query-partial-matching.md).

Om du förväntar dig kraftig användning av booleska operatorer, vilket är mer sannolik i index som innehåller stora textblock (ett innehålls fält eller långa beskrivningar), måste du testa frågor med **`searchMode=Any|All`** parametern för att utvärdera effekten av den här inställningen vid boolesk sökning.

## <a name="autocomplete-and-suggested-queries"></a>Autoavsluta och föreslagna frågor

[Autoavsluta eller föreslagna resultat](search-autocomplete-tutorial.md) är alternativ för **`search`** att söka efter en lyckad fråga som baseras på partiella sträng inmatningar (efter varje tecken) i en sökning efter typ. Du kan använda **`autocomplete`** och **`suggestions`** parameter tillsammans eller separat, enligt beskrivningen i [den här självstudien](tutorial-csharp-type-ahead-and-suggestions.md), men du kan inte använda dem med **`search`** . Både slutförda villkor och föreslagna frågor härleds från index innehåll. Motorn returnerar aldrig en sträng eller ett förslag som inte finns i indexet. Mer information finns i [Autoavsluta (REST API)](/rest/api/searchservice/autocomplete) och [förslag (REST API)](/rest/api/searchservice/suggestions).

## <a name="filter-search"></a>Filtrera sökning

Filter används ofta i appar som innehåller Kognitiv sökning. På program sidor visualiseras ofta filter som FACET i länk navigerings strukturer för användar riktad filtrering. Filter används också internt för att exponera segment med indexerat innehåll. Du kan till exempel initiera en Sök sida med ett filter i en produkt kategori, eller ett språk om ett index innehåller fält på både engelska och franska.

Du kan också behöva filter för att anropa ett specialiserat fråge formulär, enligt beskrivningen i följande tabell. Du kan använda ett filter med en ospecificerad sökning ( **`search=*`** ) eller med en frågesträng som innehåller termer, fraser, operatorer och mönster.

| Filter scenario | Beskrivning |
|-----------------|-------------|
| Intervall filter | I Azure Kognitiv sökning skapas intervall frågor med hjälp av filter parametern. Mer information och exempel finns i [exempel på Range filter](search-query-simple-examples.md#example-4-range-filters). |
| Sökning på Geo-platser | Om ett sökbart fält är av [typen EDM. GeographyPoint](/rest/api/searchservice/supported-data-types)kan du skapa ett filter uttryck för "hitta nära mig" eller mappnings-baserade Sök kontroller. Fält som driver geo-search innehåller koordinater. Mer information och ett exempel finns i [exempel på Geo-sökning](search-query-simple-examples.md#example-5-geo-search). |
| Aspektbaserad navigering | En aspekt navigerings struktur blir instrumentell i användar riktad navigering när du anropar ett filter som svar på en `onclick` händelse på en aspekt. Det gör att ansikte och filter går hand i hand. Om du lägger till aspekt navigering måste du ha filter för att slutföra upplevelsen. Mer information finns i [så här skapar du ett aspekt filter](search-filters-facets.md). |

> [!NOTE]
> Text som används i ett filter uttryck analyseras inte under frågans bearbetning. Text indatatypen förmodas vara ett orda Grant Skift läges känsligt tecken mönster som antingen lyckas eller Miss lyckas med matchningen. Filter uttryck konstrueras med [OData-syntax](query-odata-filter-orderby-syntax.md) och skickas i en **`filter`** parameter i alla *filter* bara fält i indexet. Mer information finns i [filter i Azure kognitiv sökning](search-filters.md).

## <a name="document-look-up"></a>Dokumentets sökning

I motsats till de tidigare beskrivna fråge formulären hämtar den här ett enda [sökdokument med ID](/rest/api/searchservice/lookup-document), utan motsvarande index sökning eller sökning. Endast ett dokument begärs och returneras. När en användare väljer ett objekt i Sök resultaten, hämtar dokumentet och fyller i en informations sida med fält är ett typiskt svar och ett dokuments ökning är den åtgärd som stöder det.

## <a name="advanced-search-fuzzy-wildcard-proximity-regex"></a>Avancerad sökning: fuzzy, jokertecken, närhet, regex

Ett avancerat fråge formulär är beroende av en fullständig Lucene-parser och operatörer som utlöser ett särskilt fråge beteende.

| Frågetyp | Användning | Exempel och mer information |
|------------|--------|------------------------------|
| [Sökning efter fält](query-lucene-syntax.md#bkmk_fields) | **`search`**  ProfileServiceApplicationProxy **`queryType=full`**  | Bygg ett sammansatt frågeuttryck riktade mot ett enskilt fält. <br/>[Exempel på fält som söks](search-query-lucene-examples.md#example-2-fielded-search) |
| [Fuzzy-sökning](query-lucene-syntax.md#bkmk_fuzzy) | **`search`** ProfileServiceApplicationProxy **`queryType=full`** | Matchningar på villkor som har en liknande konstruktion eller stavning. <br/>[Exempel på fuzzy-sökning](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [närhets sökning](query-lucene-syntax.md#bkmk_proximity) | **`search`** ProfileServiceApplicationProxy **`queryType=full`** | Söker efter termer som är nära varandra i ett dokument. <br/>[Exempel på närhet](search-query-lucene-examples.md#example-4-proximity-search) |
| [term förstärkning](query-lucene-syntax.md#bkmk_termboost) | **`search`** ProfileServiceApplicationProxy **`queryType=full`** | Rangordna ett dokument högre om det innehåller den ökade termen, i förhållande till andra som inte är det. <br/>[Exempel på term förstärkning](search-query-lucene-examples.md#example-5-term-boosting) |
| [sökning efter reguljära uttryck](query-lucene-syntax.md#bkmk_regex) | **`search`** ProfileServiceApplicationProxy **`queryType=full`** | Matchningar baserat på innehållet i ett reguljärt uttryck. <br/>[Exempel på reguljära uttryck](search-query-lucene-examples.md#example-6-regex) |
|  [sökning med jokertecken eller prefix](query-lucene-syntax.md#bkmk_wildcard) | **`search`** parameter med * *_`~`_* eller **`?`** , **`queryType=full`**| Matchar baserat på ett prefix och tilde ( `~` ) eller ett enskilt tecken ( `?` ). <br/>[Sök exempel i jokertecken](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="next-steps"></a>Nästa steg

Granska exemplen för varje syntax för en närmare titt på frågekörningen. Om du är nybörjare på full texts ökning kan du titta närmare på vad frågemotor kan vara ett lika bra val.

+ [Enkla exempelfrågor](search-query-simple-examples.md)
+ [Exempel på Lucene-syntax för att skapa avancerade frågor](search-query-lucene-examples.md)
+ [Så här fungerar fulltextsökning i Azure Cognitive Search](search-lucene-query-architecture.md)