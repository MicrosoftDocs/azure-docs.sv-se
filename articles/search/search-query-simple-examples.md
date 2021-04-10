---
title: Använd enkel Lucene-frågesyntax
titleSuffix: Azure Cognitive Search
description: Exempel på frågor som demonstrerar den enkla syntaxen för full texts ökning, filter sökning och geo-sökning mot ett Azure Kognitiv sökning-index.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 2abe19351c92bf9cea85c85dd55f47b5ee6d1625
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101694044"
---
# <a name="use-the-simple-search-syntax-in-azure-cognitive-search"></a>Använd den "enkla" Söksyntaxen i Azure Kognitiv sökning

I Azure Kognitiv sökning anropar den [enkla frågesyntaxen](query-simple-syntax.md) standard frågans parser för full texts ökning. Parsern är snabb och hanterar vanliga scenarier, inklusive full texts ökning, filtrerad och fasett sökning och sökning efter prefix. I den här artikeln används exempel för att illustrera användningen av enkel syntax i en [sökning efter dokument (REST API)](/rest/api/searchservice/search-documents) .

> [!NOTE]
> En alternativ frågesyntax är [full Lucene](query-lucene-syntax.md)och stöder mer komplexa frågeuttryck, till exempel Fuzzy-och jokertecken. Mer information och exempel finns i [använda fullständig Lucene-syntax](search-query-lucene-examples.md).

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
    "queryType": "simple",
    "select": "HotelId, HotelName, Category, Tags, Description",
    "count": true
}
```

+ "Sök" inställt på `*` är en ospecificerad fråga som motsvarar null eller tom sökning. Det är inte särskilt användbart, men det är den enklaste sökningen du kan göra, och det visar alla hämtnings bara fält i indexet, med alla värden.

+ "queryType" är inställd på "enkel" är standard och kan utelämnas, men det ingår för att ytterligare förstärka att fråge exemplen i den här artikeln uttrycks i den enkla syntaxen.

+ Välj en kommaavgränsad lista med fält som används för Sök Resultat sammansättning, inklusive bara de fält som är användbara i Sök resultat.

+ "count" returnerar antalet dokument som matchar Sök kriterierna. I en tom Sök sträng är antalet dokument i indexet (50 om det rör sig om Hotels-Sample-index).

## <a name="example-1-full-text-search"></a>Exempel 1: full texts ökning

Full texts ökning kan vara valfritt antal fristående termer eller citat tecken, med eller utan booleska operatorer. 

```http
POST /indexes/hotel-samples-index/docs/search?api-version=2020-06-30
{
    "search": "pool spa +airport",
    "searchMode": any,
    "queryType": "simple",
    "select": "HotelId, HotelName, Category, Description",
    "count": true
}
```

En nyckelords sökning som består av viktiga termer eller fraser tenderar att fungera bäst. Sträng fält genomgår text analyser vid indexering och frågor, vilket tar bort icke-grundläggande ord som "", "och", "IT". Om du vill se hur en frågesträng är token i indexet skickar du strängen i ett [analys text](/rest/api/searchservice/test-analyzer) anrop till indexet.

Parametern "searchMode" styr precision och återkallas. Om du vill ha mer information använder du Standard värdet "any", som returnerar ett resultat om någon del av frågesträngen matchas. Om du prioriterar precision, där alla delar av strängen måste matchas, ändrar du searchMode till "alla". Prova ovanstående fråga båda sätt för att se hur searchMode ändrar resultatet.

Svar för frågan "Pool-Spa + flyg plats" bör se ut ungefär som i följande exempel, trimmat för det kortfattat.

```json
"@odata.count": 6,
"value": [
    {
        "@search.score": 7.3617697,
        "HotelId": "21",
        "HotelName": "Nova Hotel & Spa",
        "Description": "1 Mile from the airport.  Free WiFi, Outdoor Pool, Complimentary Airport Shuttle, 6 miles from the beach & 10 miles from downtown.",
        "Category": "Resort and Spa",
        "Tags": [
            "pool",
            "continental breakfast",
            "free parking"
        ]
    },
    {
        "@search.score": 2.5560288,
        "HotelId": "25",
        "HotelName": "Scottish Inn",
        "Description": "Newly Redesigned Rooms & airport shuttle.  Minutes from the airport, enjoy lakeside amenities, a resort-style pool & stylish new guestrooms with Internet TVs.",
        "Category": "Luxury",
        "Tags": [
            "24-hour front desk service",
            "continental breakfast",
            "free wifi"
        ]
    },
    {
        "@search.score": 2.2988036,
        "HotelId": "35",
        "HotelName": "Suites At Bellevue Square",
        "Description": "Luxury at the mall.  Located across the street from the Light Rail to downtown.  Free shuttle to the mall and airport.",
        "Category": "Resort and Spa",
        "Tags": [
            "continental breakfast",
            "air conditioning",
            "24-hour front desk service"
        ]
    }
```

Observera Sök poängen i svaret. Detta är resultatet av matchningen. Som standard returnerar en Sök tjänst de översta 50-matchningarna baserat på den här poängen.

Enhetliga resultat för "1,0" inträffar när det inte finns någon rang, antingen på grund av att sökningen inte var full texts ökning eller på grund av att inga kriterier tillhandahölls. Till exempel i en tom sökning (search = `*` ), kommer rader tillbaka i valfri ordning. När du inkluderar de faktiska kriterierna visas Sök resultat som utvecklas till meningsfulla värden.

## <a name="example-2-look-up-by-id"></a>Exempel 2: Sök efter ID

När du returnerar Sök resultat i en fråga, är ett logiskt nästa steg att tillhandahålla en informations sida som innehåller fler fält från dokumentet. Det här exemplet visar hur du returnerar ett enda dokument med hjälp av [Sök dokument](/rest/api/searchservice/lookup-document) genom att skicka i dokument-ID: t.

```http
GET /indexes/hotels-sample-index/docs/41?api-version=2020-06-30
```

Alla dokument har en unik identifierare. Om du använder portalen väljer du fliken index från **index** och tittar sedan på fält definitionerna för att avgöra vilket fält som är nyckeln. Med hjälp av REST returnerar [Get index](/rest/api/searchservice/get-index) -anropet index definitionen i svars texten.

Svaret för ovanstående fråga består av det dokument vars nyckel är 41. Alla fält som har marker ATS som hämtnings bara i index definitionen kan returneras i Sök Resultat och återges i din app.

```json
{
    "HotelId": "41",
    "HotelName": "Ocean Air Motel",
    "Description": "Oceanfront hotel overlooking the beach features rooms with a private balcony and 2 indoor and outdoor pools. Various shops and art entertainment are on the boardwalk, just steps away.",
    "Description_fr": "L'hôtel front de mer surplombant la plage dispose de chambres avec balcon privé et 2 piscines intérieures et extérieures. Divers commerces et animations artistiques sont sur la promenade, à quelques pas.",
    "Category": "Budget",
    "Tags": [
        "pool",
        "air conditioning",
        "bar"
    ],
    "ParkingIncluded": true,
    "LastRenovationDate": "1951-05-10T00:00:00Z",
    "Rating": 3.5,
    "Location": {
        "type": "Point",
        "coordinates": [
            -157.846817,
            21.295841
        ],
        "crs": {
            "type": "name",
            "properties": {
                "name": "EPSG:4326"
            }
        }
    },
    "Address": {
        "StreetAddress": "1450 Ala Moana Blvd 2238 Ala Moana Ctr",
        "City": "Honolulu",
        "StateProvince": "HI",
        "PostalCode": "96814",
        "Country": "USA"
    },
```

## <a name="example-3-filter-on-text"></a>Exempel 3: filtrera på text

[Filter-syntax](search-query-odata-filter.md) är ett OData-uttryck som du kan använda själv eller med "search". Används tillsammans tillämpas "filter" först på hela indexet och sedan utförs sökningen på resultatet av filtret. Filter kan därför vara en användbar teknik för att förbättra frågeprestanda eftersom de begränsar uppsättningen dokument som sökfrågan behöver bearbeta.

Filter kan definieras för alla fält som marker ATS som "Filterable" i index definitionen. För hotell-exempel-index kan filter bara fält innehålla kategori, taggar, ParkingIncluded, betyg och de flesta adress fält.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "art tours",
    "queryType": "simple",
    "filter": "Category eq 'Resort and Spa'",
    "select": "HotelId,HotelName,Description,Category",
    "count": true
}
```

Svar för frågan ovan begränsas till de hotell som kategoriseras som "rapport och Spa", som innehåller termerna "konst" eller "visningar". I det här fallet finns det bara en matchning.

```json
{
    "@search.score": 2.8576312,
    "HotelId": "31",
    "HotelName": "Santa Fe Stay",
    "Description": "Nestled on six beautifully landscaped acres, located 2 blocks from the Plaza. Unwind at the spa and indulge in art tours on site.",
    "Category": "Resort and Spa"
}
```

## <a name="example-4-filter-functions"></a>Exempel 4: filtrera funktioner

Filter uttryck kan omfatta [funktionerna "search. ismatch" och "search. ismatchscoring"](search-query-odata-full-text-search-functions.md), så att du kan bygga en Sök fråga i filtret. Det här filter uttrycket använder jokertecken *gratis* för att välja bekvämligheterna, inklusive kostnads fri WiFi, fri parkering och så vidare.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
  {
    "search": "",
    "filter": "search.ismatch('free*', 'Tags', 'full', 'any')",
    "select": "HotelId, HotelName, Category, Description",
    "count": true
  }
```

Svar för ovanstående fråga matchar 19 hotell som erbjuder kostnads fria bekvämligheterna. Observera att Sök poängen är en enhetlig "1,0" i resultatet. Detta beror på att Sök uttrycket är null eller tomt, vilket resulterar i att orda Grant-filtret matchar, men inte full texts ökning. Relevans poängen returneras bara vid full texts ökning. Om du använder filter utan "Sök" ser du till att du har tillräckliga sorterbara fält så att du kan styra Sök rankningen.

```json
"@odata.count": 19,
"value": [
    {
        "@search.score": 1.0,
        "HotelId": "31",
        "HotelName": "Santa Fe Stay",
        "Tags": [
            "view",
            "restaurant",
            "free parking"
        ]
    },
    {
        "@search.score": 1.0,
        "HotelId": "27",
        "HotelName": "Super Deluxe Inn & Suites",
        "Tags": [
            "bar",
            "free wifi"
        ]
    },
    {
        "@search.score": 1.0,
        "HotelId": "39",
        "HotelName": "Whitefish Lodge & Suites",
        "Tags": [
            "continental breakfast",
            "free parking",
            "free wifi"
        ]
    },
    {
        "@search.score": 1.0,
        "HotelId": "11",
        "HotelName": "Regal Orb Resort & Spa",
        "Tags": [
            "free wifi",
            "restaurant",
            "24-hour front desk service"
        ]
    },
```

## <a name="example-5-range-filters"></a>Exempel 5: intervall filter

Intervall filtrering stöds via filter uttryck för alla data typer. I följande exempel visas numeriska och sträng områden. Data typer är viktiga i intervall filter och fungerar bäst när numeriska data är i numeriska fält och sträng data i sträng fält. Numeriska data i sträng fält passar inte för intervall eftersom numeriska strängar inte är jämförbara.

Följande fråga är ett numeriskt intervall. I hotell-exempel-index är det enda filter bara numeriska fältet omdömet.

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rating ge 2 and Rating lt 4",
    "select": "HotelId, HotelName, Rating",
    "orderby": "Rating desc",
    "count": true
}
```

Svar för den här frågan bör se ut ungefär som i följande exempel, trimmat för det kortfattat.

```json
"@odata.count": 27,
"value": [
    {
        "@search.score": 1.0,
        "HotelId": "22",
        "HotelName": "Stone Lion Inn",
        "Rating": 3.9
    },
    {
        "@search.score": 1.0,
        "HotelId": "25",
        "HotelName": "Scottish Inn",
        "Rating": 3.8
    },
    {
        "@search.score": 1.0,
        "HotelId": "2",
        "HotelName": "Twin Dome Motel",
        "Rating": 3.6
    }
```

Nästa fråga är ett intervall filter över ett sträng fält (adress/StateProvince):

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Address/StateProvince ge 'A*' and Address/StateProvince lt 'D*'",
    "select": "HotelId, HotelName, Address/StateProvince",
    "count": true
}
```

Svar för den här frågan bör se ut ungefär som i exemplet nedan, trimmat för det kortfattat. I det här exemplet går det inte att sortera efter StateProvince eftersom fältet inte har attributet "sorterbar" i index definitionen.

```json
"@odata.count": 9,
"value": [
    {
        "@search.score": 1.0,
        "HotelId": "9",
        "HotelName": "Smile Hotel",
        "Address": {
            "StateProvince": "CA "
        }
    },
    {
        "@search.score": 1.0,
        "HotelId": "39",
        "HotelName": "Whitefish Lodge & Suites",
        "Address": {
            "StateProvince": "CO"
        }
    },
    {
        "@search.score": 1.0,
        "HotelId": "7",
        "HotelName": "Countryside Resort",
        "Address": {
            "StateProvince": "CA "
        }
    },
```

## <a name="example-6-geo-search"></a>Exempel 6: Geo-search

Exempel indexet Hotels innehåller ett geo_location fält med latitud-och longitud-koordinater. I det här exemplet används [geo. Distance-funktionen](search-query-odata-geo-spatial-functions.md#examples) som filtrerar dokument inom omkretsen för en start punkt, ut till ett godtyckligt avstånd (i kilo meter) som du anger. Du kan justera det sista värdet i frågan (10) för att minska eller förstora frågans yta.

```http
POST /indexes/v/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "geo.distance(Location, geography'POINT(-122.335114 47.612839)') le 10",
    "select": "HotelId, HotelName, Address/City, Address/StateProvince",
    "count": true
}
```

Svar för den här frågan returnerar alla hotell inom ett 10 kilometer avstånd av de koordinater som anges:

```json
{
    "@odata.count": 3,
    "value": [
        {
            "@search.score": 1.0,
            "HotelId": "45",
            "HotelName": "Arcadia Resort & Restaurant",
            "Address": {
                "City": "Seattle",
                "StateProvince": "WA"
            }
        },
        {
            "@search.score": 1.0,
            "HotelId": "24",
            "HotelName": "Gacc Capital",
            "Address": {
                "City": "Seattle",
                "StateProvince": "WA"
            }
        },
        {
            "@search.score": 1.0,
            "HotelId": "16",
            "HotelName": "Double Sanctuary Resort",
            "Address": {
                "City": "Seattle",
                "StateProvince": "WA"
            }
        }
    ]
}
```

## <a name="example-7-booleans-with-searchmode"></a>Exempel 7: booleska värden med searchMode

Enkel syntax stöder booleska operatorer i form av tecken ( `+, -, |` ) för att stödja och, eller, och inte fråga om logik. Boolesk sökning fungerar som du kan förväntar dig, med några av de här undantagen. 

I föregående exempel introducerades parametern "searchMode" som en mekanism för att påverka precisionen och återkalla, med "searchMode = alla" prioriterat återkallning (ett dokument som uppfyller något av villkoren betraktas som en matchning) och "searchMode = all" prioriterad precision (alla villkor måste matchas i ett dokument). 

I kontexten för en boolesk sökning kan standardvärdet "searchMode = any" vara förvirrande om du stackar en fråga med flera operatorer och får bredare i stället för smalare resultat. Detta gäller särskilt om resultatet innehåller alla dokument som inte innehåller en speciell term eller fras.

I följande exempel visas en illustration. När du kör följande fråga med searchMode (alla) returneras 42-dokument: de som innehåller termen "restaurang", plus alla dokument som inte har frasen "luft konditionering". 

Observera att det inte finns något blank steg mellan den booleska operatorn ( `-` ) och frasen "luft konditionering".

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "restaurant -\"air conditioning\"",
    "searchMode": "any",
    "searchFields": "Tags",
    "select": "HotelId, HotelName, Tags",
    "count": true
}
```

Om du ändrar till "searchMode = all" tillämpas en kumulativ inverkan på villkor och returnerar en mindre resultat uppsättning (7 matchningar) bestående av dokument som innehåller termen "restaurang", minus de som innehåller frasen "luft konditionering".

Svar för den här frågan skulle nu se ut ungefär som i följande exempel, trimmat för det kortfattat.

```json
"@odata.count": 7,
"value": [
    {
        "@search.score": 2.5460577,
        "HotelId": "11",
        "HotelName": "Regal Orb Resort & Spa",
        "Tags": [
            "free wifi",
            "restaurant",
            "24-hour front desk service"
        ]
    },
    {
        "@search.score": 2.166792,
        "HotelId": "10",
        "HotelName": "Countryside Hotel",
        "Tags": [
            "24-hour front desk service",
            "coffee in lobby",
            "restaurant"
        ]
    },
```

## <a name="example-8-paging-results"></a>Exempel 8: växlings resultat

I föregående exempel har du lärt dig om parametrar som påverkar kompositionen av Sök resultat, inklusive "Välj" som avgör vilka fält som är i ett resultat, sorterings ordning och hur man inkluderar antalet matchningar. Det här exemplet är en fortsättning på Sök Resultat sammansättning i form av växlings parametrar som gör att du kan gruppera antalet resultat som visas på en specifik sida. 

Som standard returnerar en search-tjänst de översta 50-matchningarna. Om du vill kontrol lera antalet matchningar på varje sida använder du "Top" för att definiera storleken på batchen och använder sedan "hoppa över" för att hämta efterföljande batchar.

I följande exempel används ett filter och en sorterings ordning i fältet klassificering (klassificering är både Filterable och sorterbar) eftersom det är lättare att se effekterna av sid indelning i sorterade resultat. I en vanlig fullständig Sök fråga rangordnas de översta matchningarna och anges av " @search.score ".

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rating gt 4",
    "select": "HotelName, Rating",
    "orderby": "Rating desc",
    "top": "5",
    "count": true
}
```

Frågan hittar 21 matchande dokument, men eftersom du har angett "Top", returnerar svaret bara de fem främsta matchningarna, med klassificeringar som börjar på 4,9 och slutar på 4,7 med "fru av sjö-& B". 

Om du vill hämta nästa 5 hoppar du över den första batchen:

```http
POST /indexes/hotels-sample-index/docs/search?api-version=2020-06-30
{
    "search": "*",
    "filter": "Rating gt 4",
    "select": "HotelName, Rating",
    "orderby": "Rating desc",
    "top": "5",
    "skip": "5",
    "count": true
}
```

Svaret för den andra batchen hoppar över de fem första matchningarna, och returnerar nästa fem, som börjar med "Pull'r Inn Motel". Om du vill fortsätta med ytterligare batchar behåller du "överst" på 5 och ökar sedan "hoppa över" med 5 på varje ny begäran (hoppa över = 5, hoppa över = 10, hoppa över = 15 och så vidare).

```json
"value": [
    {
        "@search.score": 1.0,
        "HotelName": "Pull'r Inn Motel",
        "Rating": 4.7
    },
    {
        "@search.score": 1.0,
        "HotelName": "Sublime Cliff Hotel",
        "Rating": 4.6
    },
    {
        "@search.score": 1.0,
        "HotelName": "Antiquity Hotel",
        "Rating": 4.5
    },
    {
        "@search.score": 1.0,
        "HotelName": "Nordick's Motel",
        "Rating": 4.5
    },
    {
        "@search.score": 1.0,
        "HotelName": "Winter Panorama Resort",
        "Rating": 4.5
    }
]
```

## <a name="next-steps"></a>Nästa steg

Nu när du har en viss metod med den grundläggande frågesyntaxen kan du prova med att ange frågor i kod. Följande länkar förklarar hur du ställer in Sök frågor med hjälp av Azure SDK: er.

+ [Fråga ditt index med .NET SDK](search-get-started-dotnet.md)
+ [Fråga ditt index med python SDK](search-get-started-python.md)
+ [Fråga ditt index med hjälp av Java Script SDK](search-get-started-javascript.md)

Ytterligare syntax-referens, fråga arkitektur och exempel finns i följande länkar:

+ [Exempel på Lucene-syntax för att skapa avancerade frågor](search-query-lucene-examples.md)
+ [Så här fungerar fulltextsökning i Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Enkel frågesyntax](query-simple-syntax.md)
+ [Fullständig Lucene-frågesyntax](query-lucene-syntax.md)
+ [Filter-syntax](search-query-odata-filter.md)