---
title: Frågetyper
titleSuffix: Azure Cognitive Search
description: Lär dig mer om de typer av frågor som stöds i Kognitiv sökning, inklusive fritext, filter, Autoavsluta och förslag, geo-sökning, system frågor och dokuments ökning.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: d1ea2d0ba8ed5850e5d4cd9c06a0b016c4059ca7
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007865"
---
# <a name="query-types-in-azure-cognitive-search"></a>Frågetyper i Azure Kognitiv sökning

I Azure Kognitiv sökning är en fråga en fullständig specifikation av en tur och retur-åtgärd, med parametrar som styr frågekörningen och parametrar som formar det svar som kommer tillbaka.

## <a name="elements-of-a-request"></a>Element i en begäran

Följande exempel är en representativ fråga som konstrueras med hjälp av [Sök dokument REST API](/rest/api/searchservice/search-documents). Det här exemplet riktar sig till [hotell demonstrations indexet](search-get-started-portal.md) och inkluderar vanliga parametrar så att du kan få en uppfattning om hur en fråga ser ut.

```http
POST https://[service name].search.windows.net/indexes/[index name]/docs/search?api-version=[api-version]
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

Frågor dirigeras alltid i dokument samlingen för ett enda index. Du kan inte ansluta index eller skapa anpassade eller tillfälliga data strukturer som mål för frågor.

+ **`queryType`** ställer in parsern, som är antingen [standardvärdet för enkel fråga](search-query-simple-examples.md) (optimal för full texts ökning) eller den [fullständiga Lucene-frågeuttrycket](search-query-lucene-examples.md) som används för avancerade fråge konstruktioner som reguljära uttryck, närhets sökning, fuzzy och jokertecken, för att ge några.

+ **`search`** innehåller matchnings villkor, vanligt vis hela termer eller fraser, men ofta åtföljs av booleska operatorer. Enstaka fristående villkor är *term* frågor. Quote-inneslutna frågor med flera delar är *fras* frågor. Sökningen kan vara odefinierad, som i **`search=*`** , men utan villkor att matcha på består resultat uppsättningen av godtyckligt valda dokument.

+ **`searchFields`** begränsar frågans körning till vissa fält. Alla fält som har attribut som *sökbara* i index schemat är en kandidat för den här parametern.

Svar är också formade av de parametrar som du inkluderar i frågan:

+ **`select`** anger vilka fält som ska returneras i svaret. Endast fält som har marker ATS som *hämtnings* bara i indexet kan användas i en SELECT-instruktion.

+ **`top`** Returnerar det angivna antalet dokument med optimal matchning. I det här exemplet returneras bara 10 träffar. Du kan använda Top och SKIP (visas inte) för att visa resultatet.

+ **`count`** visar hur många dokument i hela indexet som matchar övergripande, vilket kan vara mer än vad som returneras. 

+ **`orderby`** används om du vill sortera resultaten efter ett värde, till exempel en klassificering eller plats. Annars är standardvärdet att använda relevans poängen för att rangordna resultat.

> [!Tip]
> Innan du skriver någon kod kan du använda specialverktyg för att lära dig syntaxen och experimentera med olika parametrar. Den snabbaste metoden är det inbyggda Portal verktyget, [Sök Utforskaren](search-explorer.md).
>
> Om du har följt den här [snabb starten för att skapa hotell demonstrations indexet](search-get-started-portal.md)kan du klistra in frågesträngen i Explorers sökfält för att köra din första fråga: `search=+"New York" +restaurant&searchFields=Description, Address/City, Tags&$select=HotelId, HotelName, Description, Rating, Address/City, Tags&$top=10&$orderby=Rating desc&$count=true`

### <a name="how-field-attributes-in-an-index-determine-query-behaviors"></a>Hur fältattribut i ett index bestämmer fråge beteenden

Index design och fråge design är nära kopplade i Azure Kognitiv sökning. Ett viktigt faktum att känna sig fram är att *index schemat*, med attribut på varje fält, bestämmer vilken typ av fråga som du kan skapa. 

Indexattribut i ett fält ange tillåtna åtgärder – om ett fält är *sökbart* i indexet, kan *hämtas* i resultat, *sorterbart*, *filtrerat* och så vidare. I exempel frågans sträng `"$orderby": "Rating"` fungerar endast eftersom fältet klassificering är markerat som *sorterbart* i index schemat. 

![Index definition för hotellet-exemplet](./media/search-query-overview/hotel-sample-index-definition.png "Index definition för hotellet-exemplet")

Skärm bilden ovan är en ofullständig lista över indexattribut för Hotels-exemplet. Du kan visa hela index schemat i portalen. Mer information om indexattribut finns i [skapa index REST API](/rest/api/searchservice/create-index).

> [!Note]
> Vissa fråge funktioner har Aktiver ATS för index i stället för per fält. Dessa funktioner omfattar: [synonym Maps](search-synonyms.md), [anpassade analys](index-add-custom-analyzers.md)verktyg, [förslags konstruktioner (för Autoavsluta och föreslagna frågor)](index-add-suggesters.md), [bedömnings logik för ranknings resultat](index-add-scoring-profiles.md).

## <a name="choose-a-parser-simple--full"></a>Välj en parser: enkel | fullständig

Med Azure Kognitiv sökning kan du välja mellan två fråge tolkare för hantering av typiska och specialiserade frågor. Begär Anden som använder den enkla parsern formuleras med hjälp av den [enkla frågesyntaxen](query-simple-syntax.md), valt som standard för dess hastighet och effektivitet i text frågor med fri form. Den här syntaxen stöder ett antal vanliga Sök operatorer som operatörerna AND, OR, NOT, fras, suffix och prioritet.

Den [fullständiga Lucene-frågesyntaxen](query-Lucene-syntax.md#bkmk_syntax), som aktive ras när du lägger till `queryType=full` begäran, exponerar det vanligaste och lättfattliga programspecifika-frågespråket som utvecklats som en del av [Apache Lucene](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html). Fullständig syntax utökar den enkla syntaxen. Alla frågor som du skriver för den enkla syntaxen körs under hela Lucene-parsern. 

I följande exempel visas punkten: samma fråga, men med olika inställningar för queryType ger det olika resultat. I den första frågan `^3` `historic` behandlas efter som en del av Sök termen. Det översta resultatet för den här frågan är "Marquis Plaza & Suitess", *som har 10* i beskrivningen.

```http
queryType=simple&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

Samma fråga som använder den fullständiga Lucene-parsern tolkar `^3` som en term förstärkning i fält. När du växlar parser ändras rangordningen, med resultat som innehåller den *historiska* termen flytta högst upp.

```http
queryType=full&search=ocean historic^3&searchFields=Description, Tags&$select=HotelId, HotelName, Tags, Description&$count=true
```

<a name="types-of-queries"></a>

## <a name="types-of-queries"></a>Typer av frågor

Azure Kognitiv sökning stöder ett brett utbud av frågetyper. 

| Frågetyp | Användning | Exempel och mer information |
|------------|--------|-------------------------------|
| Texts ökning med fritext | Sök parameter och antingen parser| Fullständig texts ökning söker efter en eller flera termer i alla *sökbara* fält i ditt index och fungerar på samma sätt som du förväntar dig att en sökmotor som Google eller Bing fungerar. Exemplet i introduktionen är full texts ökning.<br/><br/>Full texts ökning underbörjar lexikal analys med hjälp av standard Lucene Analyzer (som standard) för att sänka alla villkor, ta bort stoppord som "The". Du kan åsidosätta standardvärdet med [icke-engelska analyserare](index-add-language-analyzers.md#language-analyzer-list) eller [specialiserade oberoende-analyser](index-add-custom-analyzers.md#AnalyzerTable) som ändrar lexikalisk analys. Ett exempel är ett [nyckelord](https://lucene.apache.org/core/6_6_1/analyzers-common/org/apache/lucene/analysis/core/KeywordAnalyzer.html) som behandlar hela innehållet i ett fält som en enskild token. Detta är användbart för data som post nummer, ID: n och vissa produkt namn. | 
| Filtrerad sökning | [OData filter-uttryck](query-odata-filter-orderby-syntax.md) och antingen parser | Filter frågor utvärderar ett booleskt uttryck över alla *filter* bara fält i ett index. Till skillnad från sökningen matchar en filter fråga det exakta innehållet i ett fält, inklusive Skift läges känslighet i sträng fält. En annan skillnad är att filter frågor uttrycks i OData-syntax. <br/>[Exempel på filter uttryck](search-query-simple-examples.md#example-3-filter-queries) |
| Geo-sökning | [EDM. GeographyPoint-typ](/rest/api/searchservice/supported-data-types) i fältet, filter uttrycket och antingen parsern | Koordinater som lagras i ett fält med en EDM. GeographyPoint används för "hitta nära mig" eller mappbaserade Sök kontroller. <br/>[Exempel på Geo-sökning](search-query-simple-examples.md#example-5-geo-search)|
| Intervalls ökning | filter uttryck och enkel parser | I Azure Kognitiv sökning skapas intervall frågor med hjälp av filter parametern. <br/>[Exempel på intervall filter](search-query-simple-examples.md#example-4-range-filters) | 
| [Sökning efter fält](query-lucene-syntax.md#bkmk_fields) | Sök parameter och fullständig parser | Bygg ett sammansatt frågeuttryck riktade mot ett enskilt fält. <br/>[Exempel på fält som söks](search-query-lucene-examples.md#example-2-fielded-search) |
| [Autoavsluta eller föreslagna resultat](search-autocomplete-tutorial.md) | Parameter för Komplettera automatiskt eller förslag | Ett alternativt fråge formulär som körs baserat på partiella strängar i en sökning efter typ. Du kan använda Autoavsluta och förslag tillsammans eller separat. |
| [Fuzzy-sökning](query-lucene-syntax.md#bkmk_fuzzy) | Sök parameter och fullständig parser | Matchningar på villkor som har en liknande konstruktion eller stavning. <br/>[Exempel på fuzzy-sökning](search-query-lucene-examples.md#example-3-fuzzy-search) |
| [närhets sökning](query-lucene-syntax.md#bkmk_proximity) | Sök parameter och fullständig parser | Söker efter termer som är nära varandra i ett dokument. <br/>[Exempel på närhet](search-query-lucene-examples.md#example-4-proximity-search) |
| [term förstärkning](query-lucene-syntax.md#bkmk_termboost) | Sök parameter och fullständig parser | Rangordna ett dokument högre om det innehåller den ökade termen, i förhållande till andra som inte är det. <br/>[Exempel på term förstärkning](search-query-lucene-examples.md#example-5-term-boosting) |
| [sökning efter reguljära uttryck](query-lucene-syntax.md#bkmk_regex) | Sök parameter och fullständig parser | Matchningar baserat på innehållet i ett reguljärt uttryck. <br/>[Exempel på reguljära uttryck](search-query-lucene-examples.md#example-6-regex) |
|  [sökning med jokertecken eller prefix](query-lucene-syntax.md#bkmk_wildcard) | Sök parameter och fullständig parser | Matchar baserat på ett prefix och tilde ( `~` ) eller ett enskilt tecken ( `?` ). <br/>[Sök exempel i jokertecken](search-query-lucene-examples.md#example-7-wildcard-search) |

## <a name="next-steps"></a>Nästa steg

Använd portalen eller ett annat verktyg som Postman eller Visual Studio Code, eller en av SDK: erna för att utforska frågor i mer djup. Följande länkar hjälper dig att komma igång.

+ [Sök Utforskaren](search-explorer.md)
+ [Så här frågar du i .NET](./search-get-started-dotnet.md)
+ [Fråga i REST](./search-get-started-powershell.md)