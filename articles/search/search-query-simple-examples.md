---
title: Använd enkel Lucene-frågesyntax
titleSuffix: Azure Cognitive Search
description: Lär dig genom att köra frågor baserat på den enkla syntaxen för full texts ökning, filtrera sökning, geo-sökning och fasett-sökning mot ett Azure Kognitiv sökning-index.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/12/2020
ms.openlocfilehash: 51d36211c7ffa0507a186c9a1e1f2b52d478fe4e
ms.sourcegitcommit: 1bdcaca5978c3a4929cccbc8dc42fc0c93ca7b30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2020
ms.locfileid: "97369110"
---
# <a name="use-the-simple-search-syntax-in-azure-cognitive-search"></a>Använd den "enkla" Söksyntaxen i Azure Kognitiv sökning

I Azure Kognitiv sökning anropar den [enkla frågesyntaxen](query-simple-syntax.md) standard frågans parser för full texts ökning. Parsern är snabb och hanterar vanliga scenarier, inklusive full texts ökning, filtrerad och fasett sökning och sökning efter prefix. I den här artikeln används exempel för att illustrera användningen av enkel syntax i en [sökning efter dokument (REST API)](/rest/api/searchservice/search-documents) .

> [!NOTE]
> En alternativ frågesyntax är [full Lucene](query-lucene-syntax.md)och stöder mer komplexa frågeuttryck, till exempel Fuzzy-och jokertecken. Mer information och exempel finns i [använda fullständig Lucene-syntax](search-query-lucene-examples.md).

## <a name="nyc-jobs-examples"></a>Exempel på NYC-jobb

I följande exempel används [Sök indexet för NYC-jobb](https://azjobsdemo.azurewebsites.net/) som består av jobb som är tillgängliga baserat på en data uppsättning som tillhandahålls av Göteborg från [New York-data initiativ](https://nycopendata.socrata.com/). Dessa data ska inte betraktas som aktuella eller fullständiga. Indexet finns på en sandbox-tjänst från Microsoft, vilket innebär att du inte behöver en Azure-prenumeration eller Azure-Kognitiv sökning för att testa dessa frågor.

Vad du behöver är Postman eller ett motsvarande verktyg för att skicka HTTP-begäran på GET-eller POST. Om du inte känner till dessa verktyg, se [snabb start: utforska Azure Kognitiv sökning REST API](search-get-started-rest.md).

## <a name="set-up-the-request"></a>Konfigurera begäran

1. Begärandehuvuden måste ha följande värden:

   | Tangent | Värde |
   |-----|-------|
   | Content-Type | `application/json`|
   | API-nyckel  | `252044BE3886FE4A8E3BAA4F595114BB` </br> (det här är den faktiska fråge-API-nyckeln för tjänsten sandbox search som är värd för NYC-jobb index) |

1. Ange verbet till **`GET`** .

1. Ange URL: en till **`https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&search=*&$count=true`** . 

   + Dokument samlingen i indexet innehåller allt sökbart innehåll. Fråge-API-nyckeln i huvudet för begäran fungerar bara för Läs åtgärder som riktar in dokument samlingen.

   + **`$count=true`** Returnerar ett antal dokument som matchar Sök kriterierna. I en tom Sök sträng är antalet dokument i indexet (cirka 2558 om det gäller NYC-jobb).

   + **`search=*`** är en ospecificerad fråga som motsvarar null eller tom sökning. Det är inte särskilt användbart, men det är den enklaste sökningen du kan göra, och det visar alla hämtnings bara fält i indexet, med alla värden.

1. Som ett verifierings steg klistrar du in följande begäran i Hämta och klickar på **Skicka**. Resultat returneras som utförliga JSON-dokument.

   ```http
   https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=*&queryType=full
   ```

### <a name="how-to-invoke-simple-query-parsing"></a>Så här anropar du enkel analys av frågor

För interaktiva frågor behöver du inte ange något: enkelt är standardvärdet. I kod, om du tidigare har anropat **`queryType=full`** , kan du återställa standardvärdet med **`queryType=simple`** .

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "*"
}
```

## <a name="example-1-full-text-search-on-specific-fields"></a>Exempel 1: full texts ökning på vissa fält

Det första exemplet är inte pars-Specific, men vi leder det till att introducera det första grundläggande frågeuttrycket: inne slutningen. I det här exemplet begränsas både frågekörningen och svaret på bara några få angivna fält. Att lära dig att strukturera ett läsbart JSON-svar är viktigt när ditt verktyg är Postman eller Sök Utforskaren. 

Den här frågan är bara avsedd för *business_title* i **`searchFields`** och anger genom **`select`** parametern i samma fält i svaret.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
{
    "count": true,
    "queryType": "simple",
    "search": "*",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Svar för den här frågan bör se ut ungefär som på följande skärm bild.

  :::image type="content" source="media/search-query-lucene-examples/postman-sample-results.png" alt-text="Exempel svar på Postman" border="false":::

Du kanske har lagt märke till Sök poängen i svaret. Enhetliga resultat på **1** inträffar när det inte finns någon rang, antingen på grund av att sökningen inte var full texts ökning eller att inga kriterier tillhandahölls. För en tom sökning kommer rader tillbaka i valfri ordning. När du inkluderar de faktiska kriterierna visas Sök resultat som utvecklas till meningsfulla värden.

## <a name="example-2-look-up-by-id"></a>Exempel 2: Sök efter ID

När du returnerar Sök resultat i en fråga, är ett logiskt efterföljande steg att tillhandahålla en informations sida som innehåller fler fält från dokumentet. Det här exemplet visar hur du returnerar ett enskilt dokument med en [Lookup-åtgärd](/rest/api/searchservice/lookup-document) för att skicka in dokument-ID: t.

Alla dokument har en unik identifierare. Om du vill testa syntaxen för en uppslags fråga måste du först returnera en lista med dokument-ID: n så att du kan hitta en som ska användas. För NYC-jobb lagras identifierarna i `id` fältet.

```http
GET /indexes/nycjobs/docs?api-version=2020-06-30&search=*&$select=id&$count=true
```

Hämta sedan ett dokument från samlingen baserat på `id` "9E1E3AF9-0660-4E00-AF51-9B654925A2D5", som visades först i föregående svar. Följande fråga returnerar alla fält som går att hämta för hela dokumentet.

```http
GET /indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2020-06-30
```

## <a name="example-3-filter-queries"></a>Exempel 3: filtrera frågor

[Filter-syntax](./search-query-odata-filter.md) är ett OData-uttryck som du kan använda själv eller med **`search`** . Ett fristående filter, utan en Sök parameter, är användbart när filter uttrycket fullständigt kvalificerar dokument av intresse. Utan en frågesträng finns det ingen lexikalisk eller språklig analys, ingen poäng (alla poäng är 1) och ingen rangordning. Observera att Sök strängen är tom.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "select": "job_id, business_title, agency, salary_range_from"
    }
```

Används tillsammans tillämpas filtret först på hela indexet och sedan utförs sökningen på resultatet av filtret. Filter kan därför vara en användbar teknik för att förbättra frågeprestanda eftersom de begränsar uppsättningen dokument som sökfrågan behöver bearbeta.

  :::image type="content" source="media/search-query-simple-examples/filtered-query.png" alt-text="Filtrera fråge svar" border="false":::

Ett annat kraftfullt sätt att kombinera filter och Sök är genom **`search.ismatch*()`** ett filter uttryck där du kan använda en Sök fråga i filtret. Det här filter uttrycket använder ett jokertecken i *planen* för att välja business_title inklusive termen plan, Planner, planering och så vidare.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "search.ismatch('plan*', 'business_title', 'full', 'any')",
      "select": "job_id, business_title, agency, salary_range_from"
    }
```

Mer information om funktionen finns [i Search. ismatch i "Filtrera exempel"](./search-query-odata-full-text-search-functions.md#examples).

## <a name="example-4-range-filters"></a>Exempel 4: intervall filter

Intervall filtrering stöds via **`$filter`** uttryck för vilken datatyp som helst. I följande exempel söker du igenom numeriska och sträng fält. 

Data typer är viktiga i intervall filter och fungerar bäst när numeriska data är i numeriska fält och sträng data i sträng fält. Numeriska data i sträng fält passar inte för intervall eftersom numeriska strängar inte är jämförbara i Azure Kognitiv sökning.

Följande fråga är ett numeriskt intervall:

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "num_of_positions ge 5 and num_of_positions lt 10",
      "select": "job_id, business_title, num_of_positions, agency",
      "orderby": "agency"
    }
```
Svar för den här frågan bör se ut ungefär som på följande skärm bild.

  :::image type="content" source="media/search-query-simple-examples/rangefilternumeric.png" alt-text="Intervall filter för numeriska intervall" border="false":::

I den här frågan är intervallet över ett sträng fält (business_title):

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "business_title ge 'A*' and business_title lt 'C*'",
      "select": "job_id, business_title, agency",
      "orderby": "business_title"
    }
```

Svar för den här frågan bör se ut ungefär som på följande skärm bild.

  :::image type="content" source="media/search-query-simple-examples/rangefiltertext.png" alt-text="Intervall filter för text intervall" border="false":::

> [!NOTE]
> Fasettering över intervall med värden är ett vanligt Sök program krav. Mer information och exempel finns i [så här skapar du ett aspekt filter](search-filters-facets.md).

## <a name="example-5-geo-search"></a>Exempel 5: Geo-search

Exempel indexet innehåller ett geo_location fält med latitud-och longitud-koordinater. I det här exemplet används [geo. Distance-funktionen](search-query-odata-geo-spatial-functions.md#examples) som filtrerar dokument inom omkretsen för en start punkt, ut till ett godtyckligt avstånd (i kilo meter) som du anger. Du kan justera det sista värdet i frågan (4) för att minska eller förstora frågans yta.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "",
      "filter": "geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4",
      "select": "business_title, work_location"
    }
```

För mer läsbara resultat trimmas Sök resultaten för att inkludera befattning och arbets plats. De start koordinaterna hämtades från ett slumpmässigt dokument i indexet (i det här fallet för en arbets plats på staten ön.

  :::image type="content" source="media/search-query-simple-examples/geo-search.png" alt-text="Karta över staten-ön" border="false":::

## <a name="example-6-search-precision"></a>Exempel 6: Sök precision

Term frågor är enskilda termer, kanske många av dem, som utvärderas oberoende av varandra. Fras frågor omges av citat tecken och utvärderas som en orda Grant-sträng. Precisionen för matchningen styrs av operatörer och searchMode.

Exempel 1: `search=fire`  matchar på 140 resultat, där alla matchningar innehåller ordet brand någonstans i dokumentet.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "fire"
    }
```

Exempel 2: `search=fire department` returnerar 2002 resultat. Matchningar returneras för dokument som innehåller antingen Fire eller Department.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "fire department"
    }
```

Exempel 3: `search="fire department"` returnerar 77 resultat. Om du omsluter strängen inom citat tecken skapas en fras sökning bestående av båda termerna, och matchningar påträffas i de uttryck som består av de kombinerade termerna. Detta förklarar varför en sökning som `search=+fire +department` inte är ekvivalent. Båda villkoren krävs, men genomsöks separat. 

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
    "count": true,
    "search": "\"fire department\""
    }
```

> [!Note]
> Eftersom en fras fråga anges genom citat tecken lägger det här exemplet till ett escape-tecken ( `\` ) för att bevara syntaxen.

## <a name="example-7-booleans-with-searchmode"></a>Exempel 7: booleska värden med searchMode

Enkel syntax stöder booleska operatorer i form av tecken ( `+, -, |` ). SearchMode-parametern informerar kompromisser mellan precision och återkallande, med **`searchMode=any`** prioriterat återkallning (som matchar ett kriterium som kvalificerar ett dokument för resultat uppsättningen) och **`searchMode=all`** prioriterar precision (alla villkor måste matchas). 

Standardvärdet är **`searchMode=any`** , vilket kan vara förvirrande om du stackar en fråga med flera operatorer och får bredare i stället för smalare resultat. Detta är särskilt sant, där resultatet innehåller alla dokument som inte innehåller någon speciell term.

Med hjälp av standard searchMode (valfritt) returneras 2800-dokument: de som innehåller frasen "brand avdelning" samt alla dokument som inte har frasen "Metrotech Center".

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"-\"Metrotech Center\"",
      "searchMode": "any"
    }
```

Svar för den här frågan bör se ut ungefär som på följande skärm bild.

  :::image type="content" source="media/search-query-simple-examples/searchmodeany.png" alt-text="Sök läge, valfri" border="false":::

Om du ändrar till att **`searchMode=all`** tillämpa en kumulativ inverkan på villkor och returnerar en mindre resultat uppsättning – 21 dokument – består av dokument som innehåller hela frasen "brand Department", minus jobben på Metrotech Center-adressen.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"-\"Metrotech Center\"",
      "searchMode": "all"
    }
```

  :::image type="content" source="media/search-query-simple-examples/searchmodeall.png" alt-text="Sök läge alla" border="false":::

## <a name="example-8-structuring-results"></a>Exempel 8: strukturera resultat

Flera parametrar styr vilka fält som finns i Sök resultaten, antalet dokument som returneras i varje batch och sorterings ordning. I det här exemplet finns några av de föregående exemplen som begränsar resultatet till vissa fält med hjälp av **`$select`** instruktionen instruktion och orda Grant, och returnerar 82 matchningar.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description"
    }
```

Läggs till i föregående exempel, du kan sortera efter rubrik. Den här sorteringen fungerar eftersom *civil_service_title kan* sorteras i indexet.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description",
      "orderby": "civil_service_title"
    }
```

Växlings resultatet implementeras med hjälp av **`$top`** -parametern, i det här fallet returnerar de fem översta dokumenten:

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description",
      "orderby": "civil_service_title",
      "top": "5"
    }
```

Om du vill hämta nästa 5 hoppar du över den första batchen:

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "count": true,
      "search": "\"fire department\"",
      "searchMode": "any",
      "select": "job_id,agency,business_title,civil_service_title,work_location,job_description",
      "orderby": "civil_service_title",
      "top": "5",
      "skip": "5"
    }
```

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