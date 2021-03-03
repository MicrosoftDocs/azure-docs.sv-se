---
title: Använd fullständig Lucene-frågesyntax
titleSuffix: Azure Cognitive Search
description: Fråge exempel som demonstrerar en Lucene-frågesyntax för fuzzy search, närhets sökning, term förstärkning, sökning efter reguljära uttryck och jokertecken i ett Azure Kognitiv sökning index.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 6213efb6ba14052c6f957a6d999f48f55f65186c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101693568"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>Använd den fullständiga Söksyntaxen för Lucene (avancerade frågor i Azure Kognitiv sökning)

När du konstruerar frågor för Azure Kognitiv sökning kan du ersätta den [enkla standard fråge parsern](query-simple-syntax.md) med den mer kraftfulla [Lucene-frågedesignern](query-lucene-syntax.md) för att formulera specialiserade och avancerade frågeuttryck.

Lucene-parsern stöder komplexa frågeinställningar, till exempel frågor som kallas för fält, fuzzy search, infix och suffix med jokertecken, närhets sökning, term förstärkning och sökning efter reguljära uttryck. Den extra kraften ger ytterligare bearbetnings krav så att du kan förvänta dig en något längre körnings tid. I den här artikeln kan du gå igenom exempel som demonstrerar frågor som baseras på fullständig syntax.

> [!Note]
> Många av de specialiserade fråge konstruktioner som aktive ras med fullständig Lucene-frågesyntax är inte [text-analyserad](search-lucene-query-architecture.md#stage-2-lexical-analysis), vilket kan vara överraskande om du förväntar dig eller lemmatisering. Lexikalisk analys utförs endast på fullständiga villkor (en term fråga eller en fras fråga). Frågetyper med ofullständiga termer (prefixlängd, jokertecken, regex-fråga, fuzzy-fråga) läggs direkt till i frågans träd, vilket kringgår analys steget. Den enda omvandlingen som utförs på ofullständiga sökord är lowercasing. 
>

## <a name="hotels-sample-index"></a>Exempel index för hotell

Följande frågor baseras på hotell-exempel-indexet, som du kan skapa genom att följa anvisningarna i den här [snabb](search-get-started-portal.md)starten.

Exempel frågor har ledas med REST API-och POST-förfrågningar. Du kan klistra in och köra dem i [Postman](search-get-started-rest.md) eller i [Visual Studio Code med kognitiv sökning-tillägget](search-get-started-vs-code.md).

Begärandehuvuden måste ha följande värden:

| Tangent | Värde |
|-----|-------|
| Content-Type | application/json|
| API-nyckel  | `<your-search-service-api-key>`, antingen fråga eller administratörs nyckel |

URI-parametrarna måste innehålla din Sök tjänst slut punkt med index namn, dokument samlingar, sökkommando och API-version, som liknar följande exempel:

```http
https://{{service-name}}.search.windows.net/indexes/hotels-sample-index/docs/search?api-version=2020-06-30
```

Brödtext för begäran ska skapas som giltig JSON:

```json
{
    "search": "*",
    "queryType": "full",
    "select": "HotelId, HotelName, Category, Tags, Description",
    "count": true
}
```

+ "Sök" inställt på `*` är en ospecificerad fråga som motsvarar null eller tom sökning. Det är inte särskilt användbart, men det är den enklaste sökningen du kan göra, och det visar alla hämtnings bara fält i indexet, med alla värden.

+ med "queryType" inställt på "fullständig" anropas fullständig Lucene-fråga parser och det krävs för den här syntaxen.

+ Välj en kommaavgränsad lista med fält som används för Sök Resultat sammansättning, inklusive bara de fält som är användbara i Sök resultat.

+ "count" returnerar antalet dokument som matchar Sök kriterierna. I en tom Sök sträng är antalet dokument i indexet (50 om det rör sig om Hotels-Sample-index).

## <a name="example-1-fielded-search"></a>Exempel 1: Fältad sökning

Fält Sök omfång, enskilda, inbäddade Sök uttryck till ett visst fält. Det här exemplet söker efter hotell namn med termen "hotell" i dem, men inte "Motel". Du kan ange flera fält med hjälp av och. 

När du använder den här frågesyntaxen kan du utelämna parametern "searchFields" när fälten som du vill fråga är i själva Sök uttrycket. Om du inkluderar "searchFields" med fälts ökning har `fieldName:searchExpression` alltid företräde framför "searchFields".

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "HotelName:(hotel NOT motel) AND Category:'Resort and Spa'",
    "queryType": "full",
    "select": "HotelName, Category",
    "count": true
}
```

Svar för den här frågan bör se ut ungefär som i följande exempel, filtrerat på "utväg och Spa", som returnerar hotell som inkluderar "hotell" eller "Motel" i namnet.

```json
"@odata.count": 4,
"value": [
    {
        "@search.score": 4.481559,
        "HotelName": "Nova Hotel & Spa",
        "Category": "Resort and Spa"
    },
    {
        "@search.score": 2.4524608,
        "HotelName": "King's Palace Hotel",
        "Category": "Resort and Spa"
    },
    {
        "@search.score": 2.3970203,
        "HotelName": "Triple Landscape Hotel",
        "Category": "Resort and Spa"
    },
    {
        "@search.score": 2.2953436,
        "HotelName": "Peaceful Market Hotel & Spa",
        "Category": "Resort and Spa"
    }
]
```

Sök uttrycket kan vara en term eller en fras, eller ett mer komplext uttryck inom parentes, eventuellt med booleska operatorer. Några exempel är följande:

+ `HotelName:(hotel NOT motel)`
+ `Address/StateProvince:("WA" OR "CA")`
+ `Tags:("free wifi" NOT "free parking") AND "coffee in lobby"`

Se till att placera en fras inom citat tecken om du vill att båda strängarna ska utvärderas som en enda entitet, som i det här fallet söker efter två olika platser i fältet Adress/StateProvince. Beroende på klienten kan du behöva Escape ( `\` ) citat tecken.

Fältet som anges i `fieldName:searchExpression` måste vara ett sökbart fält. Mer information om hur fält definitioner är attribut finns i [skapa index (REST API)](/rest/api/searchservice/create-index) .

## <a name="example-2-fuzzy-search"></a>Exempel 2: oskarp sökning

Suddiga Sök matchningar på liknande villkor, inklusive felstavade ord. Om du vill göra en Fuzzy-sökning lägger du till Tilde- `~` symbolen i slutet av ett enstaka ord med en valfri parameter, ett värde mellan 0 och 2, som anger redigerings avståndet. Till exempel `blue~` eller `blue~1` skulle returnera blå, blå och limma.

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "Tags:conserge~",
    "queryType": "full",
    "select": "HotelName, Category, Tags",
    "searchFields": "HotelName, Category, Tags",
    "count": true
}
```

Svar för den här frågan matchar "Concierge" i matchande dokument, trimmade för det kortfattat:

```json
"@odata.count": 12,
"value": [
    {
        "@search.score": 1.1832147,
        "HotelName": "Secret Point Motel",
        "Category": "Boutique",
        "Tags": [
            "pool",
            "air conditioning",
            "concierge"
        ]
    },
    {
        "@search.score": 1.1819803,
        "HotelName": "Twin Dome Motel",
        "Category": "Boutique",
        "Tags": [
            "pool",
            "free wifi",
            "concierge"
        ]
    },
    {
        "@search.score": 1.1773309,
        "HotelName": "Smile Hotel",
        "Category": "Suite",
        "Tags": [
            "view",
            "concierge",
            "laundry service"
        ]
    },
```

Fraser stöds inte direkt, men du kan ange en Fuzzy-matchning på varje term i en sats med flera delar, till exempel `search=Tags:landy~ AND sevic~` .  Det här frågeuttrycket hittar 15 matchningar på "tvätt tjänst".

> [!Note]
> Fuzzy-frågor [analyseras](search-lucene-query-architecture.md#stage-2-lexical-analysis)inte. Frågetyper med ofullständiga termer (prefixlängd, jokertecken, regex-fråga, fuzzy-fråga) läggs direkt till i frågans träd, vilket kringgår analys steget. Den enda omvandlingen som utförs på partiella sökord är mindre Skift läge.
>

## <a name="example-3-proximity-search"></a>Exempel 3: närhets sökning

Närhets sökningen hittar termer som är nära varandra i ett dokument. Infoga en tilde ~-symbol i slutet av en fras följt av antalet ord som skapar närhets kanten.

Den här frågan söker efter termerna "hotell" och "flyg plats" inom 5 ord för varandra i ett dokument. Citat tecknen är undantagna ( `\"` ) för att bevara frasen:

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "Description: \"hotel airport\"~5",
    "queryType": "full",
    "select": "HotelName, Description",
    "searchFields": "HotelName, Description",
    "count": true
}
```

Svar för den här frågan bör se ut ungefär som i följande exempel:

```json
"@odata.count": 2,
"value": [
    {
        "@search.score": 0.6331726,
        "HotelName": "Trails End Motel",
        "Description": "Only 8 miles from Downtown.  On-site bar/restaurant, Free hot breakfast buffet, Free wireless internet, All non-smoking hotel. Only 15 miles from airport."
    },
    {
        "@search.score": 0.43032226,
        "HotelName": "Catfish Creek Fishing Cabins",
        "Description": "Brand new mattresses and pillows.  Free airport shuttle. Great hotel for your business needs. Comp WIFI, atrium lounge & restaurant, 1 mile from light rail."
    }
]
```

## <a name="example-4-term-boosting"></a>Exempel 4: term förstärkning

Term förstärkning syftar på att rangordna ett dokument högre om det innehåller den ökade perioden, i förhållande till dokument som inte innehåller termen. Om du vill förstärka en term använder du cirkumflex, `^` symbol med en förstärknings faktor (ett tal) i slutet av den period som du söker. Standardvärdet för förstärknings faktorn är 1 och det måste vara positivt, men det kan vara mindre än 1 (till exempel 0,2). Term förstärkning skiljer sig från bedömnings profiler i dessa bedömnings profiler ökar vissa fält i stället för specifika villkor.

I den här frågan söker du efter "strand Access" och ser att det finns sju dokument som matchar på en eller båda villkoren.

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "beach access",
    "queryType": "full",
    "select": "HotelName, Description, Tags",
    "searchFields": "HotelName, Description, Tags",
    "count": true
}
```

I själva verket finns det bara ett dokument som matchar "Access" och eftersom det är den enda matchningen är dess placering hög (andra placeringen) även om dokumentet saknar termen "strand".

```json
"@odata.count": 7,
"value": [
    {
        "@search.score": 2.2723424,
        "HotelName": "Nova Hotel & Spa",
        "Description": "1 Mile from the airport.  Free WiFi, Outdoor Pool, Complimentary Airport Shuttle, 6 miles from the beach & 10 miles from downtown."
    },
    {
        "@search.score": 1.5507699,
        "HotelName": "Old Carrabelle Hotel",
        "Description": "Spacious rooms, glamorous suites and residences, rooftop pool, walking access to shopping, dining, entertainment and the city center."
    },
    {
        "@search.score": 1.5358944,
        "HotelName": "Whitefish Lodge & Suites",
        "Description": "Located on in the heart of the forest. Enjoy Warm Weather, Beach Club Services, Natural Hot Springs, Airport Shuttle."
    },
    {
        "@search.score": 1.3433652,
        "HotelName": "Ocean Air Motel",
        "Description": "Oceanfront hotel overlooking the beach features rooms with a private balcony and 2 indoor and outdoor pools. Various shops and art entertainment are on the boardwalk, just steps away."
    },
```

I frågan "efter" upprepar du sökningen, den här tiden ökar resultatet med termen "strand" över termen "Access". En mänsklig läsbar version av frågan är `search=Description:beach^2 access` . Beroende på din klient kan du behöva uttrycka `^2` som `%5E2` .

Efter att ha förbättrat termen "strand" går matchningen på det gamla Carrabelle hotellet nedåt till den sjätte platsen.

<!-- Consider a scoring profile that boosts matches in a certain field, such as "genre" in a music app. Term boosting could be used to further boost certain search terms higher than others. For example, "rock^2 electronic" will boost documents that contain the search terms in the "genre" field higher than other searchable fields in the index. Furthermore, documents that contain the search term "rock" will be ranked higher than the other search term "electronic" as a result of the term boost value (2). -->

## <a name="example-5-regex"></a>Exempel 5: regex

En sökning efter reguljära uttryck hittar en matchning baserat på innehållet mellan snedstreck "/", enligt beskrivningen i [klassen RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "HotelName:/(Mo|Ho)tel/",
    "queryType": "full",
    "select": "HotelName",
    "count": true
}
```

Svar för den här frågan bör se ut ungefär som i följande exempel:

```json
    "@odata.count": 22,
    "value": [
        {
            "@search.score": 1.0,
            "HotelName": "Days Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Triple Landscape Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Smile Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Pelham Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Sublime Cliff Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Twin Dome Motel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Nova Hotel & Spa"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Scarlet Harbor Hotel"
        },
```

> [!Note]
> Regex-frågor [analyseras](./search-lucene-query-architecture.md#stage-2-lexical-analysis)inte. Den enda omvandlingen som utförs på partiella sökord är mindre Skift läge.
>

## <a name="example-6-wildcard-search"></a>Exempel 6: sökning med jokertecken

Du kan använda allmänt identifierad syntax för jokertecken för flera ( `*` ) eller enkla ( `?` )-Character-sökningar. Observera att funktionen Lucene Query parser stöder användningen av dessa symboler med en enda term och inte en fras.

I den här frågan söker du efter hotell namn som innehåller prefixet "SC". Du kan inte använda en `*` eller- `?` symbol som det första tecknet i en sökning.

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "HotelName:sc*",
    "queryType": "full",
    "select": "HotelName",
    "count": true
}
```

Svar för den här frågan bör se ut ungefär som i följande exempel:

```json
    "@odata.count": 2,
    "value": [
        {
            "@search.score": 1.0,
            "HotelName": "Scarlet Harbor Hotel"
        },
        {
            "@search.score": 1.0,
            "HotelName": "Scottish Inn"
        }
    ]
```

> [!Note]
> Jokertecken har inte [analyser](./search-lucene-query-architecture.md#stage-2-lexical-analysis)ATS. Den enda omvandlingen som utförs på partiella sökord är mindre Skift läge.
>

## <a name="next-steps"></a>Nästa steg

Försök att ange frågor i kod. Följande länkar förklarar hur du ställer in Sök frågor med hjälp av Azure SDK: er.

+ [Fråga ditt index med .NET SDK](search-get-started-dotnet.md)
+ [Fråga ditt index med python SDK](search-get-started-python.md)
+ [Fråga ditt index med hjälp av Java Script SDK](search-get-started-javascript.md)

Ytterligare syntax-referens, fråga arkitektur och exempel finns i följande länkar:

+ [Exempel på Lucene-syntax för att skapa avancerade frågor](search-query-lucene-examples.md)
+ [Så här fungerar fulltextsökning i Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Enkel frågesyntax](query-simple-syntax.md)
+ [Fullständig Lucene-frågesyntax](query-lucene-syntax.md)
+ [Filter-syntax](search-query-odata-filter.md)