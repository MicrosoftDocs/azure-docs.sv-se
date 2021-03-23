---
title: Filtrera efter Sök Resultat
titleSuffix: Azure Cognitive Search
description: Filtrera efter användarens säkerhets identitet, språk, Geo-Location eller numeriska värden för att minska Sök resultaten för frågor i Azure Kognitiv sökning, en värdbaserad Sök tjänst i molnet på Microsoft Azure.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/02/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: ba538f4753c2365406bd88286b6d54cff1a9e9ea
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104800830"
---
# <a name="filters-in-azure-cognitive-search"></a>Filter i Azure Kognitiv sökning 

Ett *filter* ger värdebaserade kriterier för att välja dokument som används i en fråga. Ett filter kan vara ett enskilt värde eller ett OData [filter-uttryck](search-query-odata-filter.md). Till skillnad från full texts ökning returnerar ett filter värde eller uttryck bara en strikt matchning.

Vissa Sök upplevelser, till exempel [fasettisk navigering](search-filters-facets.md), är beroende av filter som en del av implementeringen, men du kan använda filter när du vill begränsa en fråga till vissa värden. Om målet i stället är att begränsa en fråga till vissa fält, finns det alternativa metoder, som beskrivs nedan.

## <a name="when-to-use-a-filter"></a>När du ska använda ett filter

Filter är grundläggande för flera Sök upplevelser, inklusive "hitta nära mig", fasett-navigering och säkerhets filter som endast visar de dokument som en användare får se. Om du implementerar något av dessa upplevelser krävs ett filter. Det är filtret som är kopplat till Sök frågan som tillhandahåller koordinaterna för geolokalisering, den aspekt kategori som användaren har valt eller säkerhets-ID: t för beställaren.

Vanliga scenarier är följande:

+ Segment Sök resultat baserat på innehåll i indexet. Med ett schema med hotell plats, kategorier och bekvämligheterna kan du skapa ett filter för att explicit matcha villkor (i Seattle, på vattnet, med en vy). 

+ Implementera en Sök upplevelse innehåller ett filter krav:

  + [Fasettisk navigering](search-faceted-navigation.md) använder ett filter för att skicka tillbaka den aspekt kategori som valts av användaren.
  + Geo-search använder ett filter för att skicka koordinater för den aktuella platsen i "hitta nära mig"-appar. 
  + [Säkerhets filter](search-security-trimming-for-azure-search.md) skickar säkerhets identifierare som filter villkor, där en matchning i indexet fungerar som en proxy för åtkomst behörighet till dokumentet.

+ Gör en "nummers ökning". Numeriska fält kan hämtas och visas i Sök resultat, men de är inte sökbara (undersöknings texts ökning) individuellt. Om du behöver urvals villkor baserat på numeriska data använder du ett filter.

### <a name="alternative-methods-for-reducing-scope"></a>Alternativa metoder för att minska omfattningen

Om du vill ha en begränsad inverkan i dina Sök resultat kan du inte välja filter. De här alternativen kan vara en bättre anpassning, beroende på ditt mål:

+ `searchFields` Frågeparametern begränsar sökningen till vissa fält. Om ditt index t. ex. innehåller separata fält för engelska och spanska beskrivningar, kan du använda searchFields för att ange vilka fält som ska användas för full texts ökning. 

+ `$select` parameter används för att ange vilka fält som ska ingå i en resultat uppsättning, vilket effektivt kan rensa svaret innan det skickas till det anropande programmet. Den här parametern kan inte förfina frågan eller minska dokument samlingen, men om ett mindre svar är ditt mål är den här parametern ett alternativ att tänka på. 

Mer information om någon av parametrarna finns i [Sök efter dokument > begäran > frågeparametrar](/rest/api/searchservice/search-documents#query-parameters).

## <a name="how-filters-are-executed"></a>Hur filter körs

Vid tidpunkten i frågan accepterar en filter tolkare villkor som inmatade, konverterar uttrycket till atomiska booleska uttryck som visas som ett träd och utvärderar filter trädet över filter bara fält i ett index.

Filtrering sker i tandem med sökning och kvalificerar vilka dokument som ska inkluderas i underordnad bearbetning för dokument hämtning och relevans-poäng. När de kombineras med en Sök sträng minskar filtret åter kallelse uppsättningen för efterföljande Sök åtgärder. När den används enskilt (till exempel när frågesträngen är tom där `search=*` ) är filter villkoret den enda ingången. 

## <a name="defining-filters"></a>Definiera filter

Filter är OData-uttryck som har ledats i att [filter-syntaxen](search-query-odata-filter.md) stöds av kognitiv sökning.

Du kan ange ett filter för varje **Sök** åtgärd, men själva filtret kan innehålla flera fält, flera kriterier och om du använder en **ismatch** -funktion, flera full texts öknings uttryck. I ett filter uttryck med flera delar kan du ange predikat i valfri ordning (enligt reglerna för Operator prioritet). Det finns ingen märkbar vinst i prestanda om du försöker arrangera om predikat i en viss sekvens.

En av gränserna för ett filter uttryck är den maximala storleks gränsen för begäran. Hela förfrågan, inklusive filtret, kan vara högst 16 MB för POST eller 8 KB för GET. Det finns också en gräns för antalet satser i filter uttrycket. En lämplig tumregel är att om du har hundratals satser kan du vara utsatt för att kunna köra gränsen. Vi rekommenderar att du utformar ditt program på ett sådant sätt att det inte genererar filter av obegränsad storlek.

Följande exempel representerar prototyp filter definitioner i flera API: er.

```http
# Option 1:  Use $filter for GET
GET https://[service name].search.windows.net/indexes/hotels/docs?api-version=2020-06-30&search=*&$filter=Rooms/any(room: room/BaseRate lt 150.0)&$select=HotelId, HotelName, Rooms/Description, Rooms/BaseRate

# Option 2: Use filter for POST and pass it in the request body
POST https://[service name].search.windows.net/indexes/hotels/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rooms/any(room: room/BaseRate lt 150.0)",
    "select": "HotelId, HotelName, Rooms/Description, Rooms/BaseRate"
}
```

```csharp
    parameters =
        new SearchParameters()
        {
            Filter = "Rooms/any(room: room/BaseRate lt 150.0)",
            Select = new[] { "HotelId", "HotelName", "Rooms/Description" ,"Rooms/BaseRate"}
        };

    var results = searchIndexClient.Documents.Search("*", parameters);
```

## <a name="filter-usage-patterns"></a>Filtrera användnings mönster

I följande exempel visas flera användnings mönster för filter scenarier. Fler idéer finns i [syntax för OData-uttryck > exempel](./search-query-odata-filter.md#examples).

+ Fristående **$filter**, utan en frågesträng, användbart när filter uttrycket fullständigt kvalificerar dokument av intresse. Utan en frågesträng finns det ingen lexikalisk eller språklig analys, ingen poäng och ingen rangordning. Observera att Sök strängen bara är en asterisk, vilket innebär "matcha alla dokument".

  ```http
  {
    "search": "*",
    "filter": "Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Honolulu"
  }
  ```

+ En kombination av frågesträng och **$filter**, där filtret skapar delmängd och frågesträngen innehåller term inmatningar för full texts ökning över den filtrerade del mängden. Genom att lägga till villkor (promenads avstånds biograf) införs Sök resultat i resultaten, där dokument som bäst matchar villkoren rangordnas högre. Att använda ett filter med en frågesträng är det vanligaste användnings mönstret.

  ```http
  {
    "search": "walking distance theaters",
    "filter": "Rooms/any(room: room/BaseRate ge 60 and room/BaseRate lt 300) and Address/City eq 'Seattle'"
  }

+ Compound queries, separated by "or", each with its own filter criteria (for example, 'beagles' in 'dog' or 'siamese' in 'cat'). Expressions combined with `or` are evaluated individually, with the union of documents matching each expression sent back in the response. This usage pattern is achieved through the `search.ismatchscoring` function. You can also use the non-scoring version, `search.ismatch`.

   ```
   # <a name="match-on-hostels-rated-higher-than-4-or-5-star-motels"></a>Matcha på Hostels som klassats högre än 4 eller 5 stjärnor Motels.
   $filter = search. ismatchscoring (' Hostel ') och betyget ge 4 eller search. ismatchscoring (' Motel ') och betyget EQ 5

   # <a name="match-on-luxury-or-high-end-in-the-description-field-or-on-category-exactly-equal-to-luxury"></a>Matcha "lyxen" eller "high-end" i fältet Beskrivning eller på en kategori exakt lika med "lyxen".
   $filter = search. ismatchscoring (' lyxen | High-end ', ' Description ') eller Category EQ ' lyxen ' &$count = True
   ```

  It is also possible to combine full-text search via `search.ismatchscoring` with filters using `and` instead of `or`, but this is functionally equivalent to using the `search` and `$filter` parameters in a search request. For example, the following two queries produce the same result:

  ```
  $filter = search. ismatchscoring (' pool ') och betyget ge 4

  search = pool&$filter = klassificering ge 4
  ```

Follow up with these articles for comprehensive guidance on specific use cases:

+ [Facet filters](search-filters-facets.md)
+ [Security trimming](search-security-trimming-for-azure-search.md) 

## Field requirements for filtering

In the REST API, filterable is *on* by default for simple fields. Filterable fields increase index size; be sure to set `"filterable": false` for fields that you don't plan to actually use in a filter. For more information about settings for field definitions, see [Create Index](/rest/api/searchservice/create-index).

In the .NET SDK, the filterable is *off* by default. You can make a field filterable by setting the [IsFilterable property](/dotnet/api/azure.search.documents.indexes.models.searchfield.isfilterable) of the corresponding [SearchField](/dotnet/api/azure.search.documents.indexes.models.searchfield) object to `true`. In the example below, the attribute is set on the `BaseRate` property of a model class that maps to the index definition.

```csharp
[IsFilterable, IsSortable, IsFacetable]
public double? BaseRate { get; set; }
```

### <a name="making-an-existing-field-filterable"></a>Göra ett befintligt fält filter bara

Du kan inte ändra befintliga fält så att de filtreras. I stället måste du lägga till ett nytt fält eller återskapa indexet. Mer information om hur du återskapar ett index eller fyller i fält finns i [så här återskapar du ett Azure kognitiv sökning-index](search-howto-reindex.md).

## <a name="text-filter-fundamentals"></a>Grundläggande text filter

Text filter matchar sträng fält mot literala strängar som du anger i filtret: `$filter=Category eq 'Resort and Spa'`

Till skillnad från full texts ökning finns det ingen lexikal analys eller ord brytning för text filter, så jämförelser är enbart för exakta matchningar. Anta till exempel att ett fält med *f* innehåller "solig Day", `$filter=f eq 'Sunny'` inte matchar, men `$filter=f eq 'sunny day'` kommer att. 

Text strängar är Skift läges känsliga. Det finns inget lägre Skift läge för de övre bokstäver orden: `$filter=f eq 'Sunny day'` "solig Day" hittas inte.

### <a name="approaches-for-filtering-on-text"></a>Metoder för filtrering av text

| Metod | Beskrivning | När du ska använda detta |
|----------|-------------|-------------|
| [`search.in`](search-query-odata-search-in-function.md) | En funktion som matchar ett fält mot en avgränsad lista med strängar. | Rekommenderas för [säkerhets filter](search-security-trimming-for-azure-search.md) och för alla filter där många rå text värden måste matchas med ett sträng fält. Funktionen **search.in** är utformad för snabbhet och är mycket snabbare än att explicit jämföra fältet mot varje sträng med `eq` och `or` . | 
| [`search.ismatch`](search-query-odata-full-text-search-functions.md) | En funktion som gör att du kan blanda full texts öknings åtgärder med strikta booleska filter åtgärder i samma filter uttryck. | Använd **search. ismatch** (eller dess bedömnings motsvarighet, **search. ismatchscoring**) när du vill ha flera Sök filter kombinationer i en begäran. Du kan också använda det för a *innehåller* filter för att filtrera på en delvis sträng inom en större sträng. |
| [`$filter=field operator string`](search-query-odata-comparison-operators.md) | Ett användardefinierat uttryck bestående av fält, operatorer och värden. | Använd det här när du vill hitta exakta matchningar mellan ett sträng fält och ett sträng värde. |

## <a name="numeric-filter-fundamentals"></a>Numeriska filter grunderna

Numeriska fält ingår inte `searchable` i kontexten för full texts ökning. Endast strängar omfattas av full texts ökning. Om du till exempel anger 99,99 som Sök villkor får du inte tillbaka dina objekt pris till $99,99. I stället visas objekt med nummer 99 i sträng fält i dokumentet. Om du däremot har numeriska data, är det att du kommer att använda dem för filter, inklusive intervall, ansikte, grupper och så vidare. 

Dokument som innehåller numeriska fält (pris, storlek, SKU, ID) tillhandahåller dessa värden i Sök resultat om fältet är markerat `retrievable` . Punkten här är att ingen fullständig texts ökning är tillämplig på numeriska fält typer.

## <a name="next-steps"></a>Nästa steg

Prova först att **söka i Utforskaren** i portalen för att skicka frågor med **$filter** parametrar. [Indexet för fastighets-och-exempel](search-get-started-portal.md) ger intressanta resultat för följande filtrerade frågor när du klistrar in dem i Sök fältet:

```
# Geo-filter returning documents within 5 kilometers of Redmond, Washington state
# Use $count=true to get a number of hits returned by the query
# Use $select to trim results, showing values for named fields only
# Use search=* for an empty query string. The filter is the sole input

search=*&$count=true&$select=description,city,postCode&$filter=geo.distance(location,geography'POINT(-122.121513 47.673988)') le 5

# Numeric filters use comparison like greater than (gt), less than (lt), not equal (ne)
# Include "and" to filter on multiple fields (baths and bed)
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=baths gt 3 and beds gt 4

# Text filters can also use comparison operators
# Wrap text in single or double quotes and use the correct case
# Full text search is on John Leclerc, matching on John or Leclerc

search=John Leclerc&$count=true&$select=source,city,postCode,baths,beds&$filter=city gt 'Seattle'
```

Mer information om hur du arbetar med fler exempel finns i [syntax för OData filter Expression > exempel](./search-query-odata-filter.md#examples).

## <a name="see-also"></a>Se även

+ [Så här fungerar fulltextsökning i Azure Cognitive Search](search-lucene-query-architecture.md)
+ [REST API för dokumentsökning](/rest/api/searchservice/search-documents)
+ [Enkel frågesyntax](/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Lucene-frågesyntax](/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Datatyper som stöds](/rest/api/searchservice/supported-data-types)