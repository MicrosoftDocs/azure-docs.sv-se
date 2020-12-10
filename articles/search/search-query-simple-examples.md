---
title: Skapa en enkel fråga
titleSuffix: Azure Cognitive Search
description: Lär dig genom att köra frågor baserat på den enkla syntaxen för full texts ökning, filtrera sökning, geo-sökning och fasett-sökning mot ett Azure Kognitiv sökning-index.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: 027852216b3f2055a5a381d00aff94526953b898
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97007882"
---
# <a name="create-a-simple-query-in-azure-cognitive-search"></a>Skapa en enkel fråga i Azure Kognitiv sökning

I Azure Kognitiv sökning anropar den [enkla frågesyntaxen](query-simple-syntax.md) standardfrågans parser för att köra fullständiga texts öknings frågor mot ett index. Den här parsern är snabb och hanterar vanliga scenarier, inklusive full texts ökning, filtrerad och fasett-sökning och geo-sökning. 

I den här artikeln använder vi exempel för att illustrera den enkla syntaxen, fylla i `search=` parametern för en [Sök dokument](/rest/api/searchservice/search-documents) åtgärd.

En alternativ frågesyntax är [full Lucene](query-lucene-syntax.md)och stöder mer komplexa frågeuttryck, till exempel Fuzzy-och jokertecken, vilket kan ta ytterligare tid att bearbeta. Mer information och exempel som demonstrerar fullständig syntax finns i [använda fullständig Lucene-syntax](search-query-lucene-examples.md).

Välj bland följande verktyg och API: er för att skicka frågor.

| Metodik | Description |
|-------------|-------------|
| [Sökutforskaren (portal)](search-explorer.md) | Innehåller ett sökfält och alternativ för val av index och API-version. Resultat returneras som JSON-dokument. Rekommenderas för undersökning, testning och validering. <br/>[Läs mer.](search-get-started-portal.md#query-index) | 
| [Postman eller Visual Studio Code](search-get-started-rest.md) | Webb test verktyg är ett utmärkt alternativ för att utforma [Sök dokument](/rest/api/searchservice/search-documents) rest-anrop. REST API har stöd för varje program mässig åtgärd i Azure Kognitiv sökning, så att du kan skicka förfrågningar interaktivt för att fokusera på utforskningen för en speciell uppgift.  |
| [SearchClient (.NET)](/dotnet/api/azure.search.documents.searchclient) | Klient som kan användas för att skicka frågor till ett Azure Kognitiv sökning-index.  <br/>[Läs mer.](search-howto-dotnet-sdk.md)  |

## <a name="formulate-requests-in-postman"></a>Formulera förfrågningar i Postman

I följande exempel används ett sökindex för NYC-jobb som består av jobb som är tillgängliga baserat på en data uppsättning som tillhandahålls av Göteborg från [New York-data](https://nycopendata.socrata.com/) initiativ. Dessa data ska inte betraktas som aktuella eller fullständiga. Indexet finns på en sandbox-tjänst från Microsoft, vilket innebär att du inte behöver en Azure-prenumeration eller Azure-Kognitiv sökning för att testa dessa frågor.

Vad du behöver är Postman eller ett motsvarande verktyg för att skicka HTTP-begäran på GET. Mer information finns i [snabb start: utforska Azure Kognitiv sökning REST API](search-get-started-rest.md).

### <a name="set-the-request-header"></a>Ange rubriken för begäran

1. I begär ande huvudet anger du **Content-Type** till `application/json` .

2. Lägg till en **API-nyckel** och ange den som den här strängen: `252044BE3886FE4A8E3BAA4F595114BB` . Det här är en frågeparameter för tjänsten sandbox search som är värd för NYC-jobb indexet.

När du har angett rubriken för begäran kan du återanvända det för alla frågor i den här artikeln och bara växla ut **Sök =** strängen. 

  :::image type="content" source="media/search-query-lucene-examples/postman-header.png" alt-text="Parametrar för set-begär ande huvud för Postman" border="false":::

### <a name="set-the-request-url"></a>Ange URL för begäran

Begäran är ett GET-kommando som paras ihop med en URL som innehåller Azure Kognitiv sökning-slutpunkten och Sök strängen.

  :::image type="content" source="media/search-query-lucene-examples/postman-basic-url-request-elements.png" alt-text="Hämta rubrik för Postman-förfrågan" border="false":::

URL-kompositionen har följande element:

+ **`https://azs-playground.search.windows.net/`** är en Sök tjänst i begränsat läge som underhålls av Azure Kognitiv sökning Development-teamet. 
+ **`indexes/nycjobs/`** är NYC-jobb indexet i index samlingen för den tjänsten. Både tjänst namnet och indexet krävs på begäran.
+ **`docs`** är dokument samlingen som innehåller allt sökbart innehåll. Den API-nyckel för frågor som anges i huvudet för begäran fungerar endast med Läs åtgärder som riktar dokument samlingen.
+ **`api-version=2020-06-30`** anger API-versionen, som är en obligatorisk parameter på varje begäran.
+ **`search=*`** är frågesträngen som i den inledande frågan är null och returnerar de första 50 resultaten (som standard).

## <a name="send-your-first-query"></a>Skicka din första fråga

Som ett verifierings steg klistrar du in följande begäran i Hämta och klickar på **Skicka**. Resultat returneras som utförliga JSON-dokument. Hela dokument returneras, vilket gör att du kan se alla fält och alla värden.

Klistra in URL: en i en REST-klient som ett verifierings steg och Visa dokument strukturen.

  ```http
  https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=*
  ```

Frågesträngen, **`search=*`** , är en ospecificerad Sök motsvarighet till null eller tom sökning. Det är inte särskilt användbart, men det är den enklaste sökningen du kan göra.

Du kan också lägga till **`$count=true`** i URL: en för att returnera ett antal dokument som matchar Sök villkoren. I en tom Sök sträng är detta alla dokument i indexet (cirka 2800 när det gäller NYC-jobb).

## <a name="how-to-invoke-simple-query-parsing"></a>Så här anropar du enkel analys av frågor

För interaktiva frågor behöver du inte ange något: enkelt är standardvärdet. Om du tidigare har anropat **frågetyp = fullständig** för fullständig frågesyntax i kod kan du återställa standardvärdet med **queryType = Simple**.

## <a name="example-1-field-scoped-query"></a>Exempel 1: fält-omfattnings fråga

Det första exemplet är inte pars-Specific, men vi leder det till att introducera det första grundläggande frågeuttrycket: inne slutningen. Det här exemplet besvarar frågekörningen och svaret på bara några få fält. Att lära dig att strukturera ett läsbart JSON-svar är viktigt när ditt verktyg är Postman eller Sök Utforskaren. 

För det kortfattat är frågan bara den *business_title* fältet och anger bara affärs titlar som returneras. Syntaxen är **searchFields** för att begränsa frågekörningen till bara fältet business_title och **välja** att ange vilka fält som ska ingå i svaret.

### <a name="partial-query-string"></a>Partiell frågesträng

```http
searchFields=business_title&$select=business_title&search=*
```

Här är samma fråga med flera fält i en kommaavgränsad lista.

```http
search=*&searchFields=business_title, posting_type&$select=business_title, posting_type
```

### <a name="full-url"></a>Fullständig URL

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&searchFields=business_title&$select=business_title&search=*
```

Svar för den här frågan bör se ut ungefär som på följande skärm bild.

  :::image type="content" source="media/search-query-lucene-examples/postman-sample-results.png" alt-text="Exempel svar på Postman" border="false":::

Du kanske har lagt märke till Sök poängen i svaret. Enhetliga resultat på 1 inträffar när det inte finns någon rang, antingen på grund av att sökningen inte var full texts ökning eller att inga kriterier tillämpades. För null-sökning utan villkor kommer rader tillbaka i valfri ordning. När du inkluderar de faktiska kriterierna visas Sök resultat som utvecklas till meningsfulla värden.

## <a name="example-2-look-up-by-id"></a>Exempel 2: Sök efter ID

Det här exemplet är en bit ovanlig, men när du utvärderar Sök beteenden kanske du vill granska hela innehållet i ett enskilt dokument för att förstå varför det togs med eller uteslöts från resultatet. Om du vill returnera ett enskilt dokument i sin helhet använder du en [Lookup-åtgärd](/rest/api/searchservice/lookup-document) för att skicka in dokument-ID: t.

Alla dokument har en unik identifierare. Om du vill testa syntaxen för en uppslags fråga måste du först returnera en lista med dokument-ID: n så att du kan hitta en som ska användas. För NYC-jobb lagras identifierarna i `id` fältet.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&searchFields=id&$select=id&search=*
```

Nästa exempel är en uppslags fråga som returnerar ett särskilt dokument baserat på `id` "9E1E3AF9-0660-4E00-AF51-9B654925A2D5", som visades först i föregående svar. Följande fråga returnerar hela dokumentet, inte bara markerade fält. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs/9E1E3AF9-0660-4E00-AF51-9B654925A2D5?api-version=2020-06-30&$count=true&search=*
```

## <a name="example-3-filter-queries"></a>Exempel 3: filtrera frågor

[Filter-syntax](./search-query-odata-filter.md) är ett OData-uttryck som du kan använda med **Sök** eller av sig själv. Ett fristående filter, utan en Sök parameter, är användbart när filter uttrycket fullständigt kvalificerar dokument av intresse. Utan en frågesträng finns det ingen lexikalisk eller språklig analys, ingen poäng (alla poäng är 1) och ingen rangordning. Observera att Sök strängen är tom.

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "search": "",
      "filter": "salary_frequency eq 'Annual' and salary_range_from gt 90000",
      "select": "job_id, business_title, agency, salary_range_from",
      "count": "true"
    }
```

Används tillsammans tillämpas filtret först på hela indexet och sedan utförs sökningen på resultatet av filtret. Filter kan därför vara en användbar teknik för att förbättra frågeprestanda eftersom de begränsar uppsättningen dokument som sökfrågan behöver bearbeta.

  :::image type="content" source="media/search-query-simple-examples/filtered-query.png" alt-text="Filtrera fråge svar" border="false":::

Om du vill testa detta i Postman med GET kan du klistra in följande sträng:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,business_title,agency,salary_range_from&search=&$filter=salary_frequency eq 'Annual' and salary_range_from gt 90000
```

Ett annat kraftfullt sätt att kombinera filter och Sök är genom **`search.ismatch*()`** ett filter uttryck där du kan använda en Sök fråga i filtret. Det här filter uttrycket använder ett jokertecken i *planen* för att välja business_title inklusive termen plan, Planner, planering och så vidare.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,business_title,agency&search=&$filter=search.ismatch('plan*', 'business_title', 'full', 'any')
```

Mer information om funktionen finns [i Search. ismatch i "Filtrera exempel"](./search-query-odata-full-text-search-functions.md#examples).

## <a name="example-4-range-filters"></a>Exempel 4: intervall filter

Intervall filtrering stöds via **`$filter`** uttryck för vilken datatyp som helst. I följande exempel söker du igenom numeriska och sträng fält. 

Data typer är viktiga i intervall filter och fungerar bäst när numeriska data är i numeriska fält och sträng data i sträng fält. Numeriska data i sträng fält passar inte för intervall eftersom numeriska strängar inte är jämförbara i Azure Kognitiv sökning. 

Följande exempel är i formatet efter läsbarhet (numeriskt intervall, följt av text intervall):

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "search": "",
      "filter": "num_of_positions ge 5 and num_of_positions lt 10",
      "select": "job_id, business_title, num_of_positions, agency",
      "orderby": "agency",
      "count": "true"
    }
```
  :::image type="content" source="media/search-query-simple-examples/rangefilternumeric.png" alt-text="Intervall filter för numeriska intervall" border="false":::


```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "search": "",
      "filter": "business_title ge 'A*' and business_title lt 'C*'",
      "select": "job_id, business_title, agency",
      "orderby": "business_title",
      "count": "true"
    }
```

  :::image type="content" source="media/search-query-simple-examples/rangefiltertext.png" alt-text="Intervall filter för text intervall" border="false":::

Du kan också prova dessa i Postman med GET:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&search=&$filter=num_of_positions ge 5 and num_of_positions lt 10&$select=job_id, business_title, num_of_positions, agency&$orderby=agency&$count=true
```

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&search=&$filter=business_title ge 'A*' and business_title lt 'C*'&$select=job_id, business_title, agency&$orderby=business_title&$count=true
```

> [!NOTE]
> Fasettering över intervall med värden är ett vanligt Sök program krav. Mer information och exempel på hur du skapar filter för aspekt navigerings strukturer finns i ["filter baserat på ett intervall" i *så här implementerar du fasett-navigering*](search-faceted-navigation.md#filter-based-on-a-range).

## <a name="example-5-geo-search"></a>Exempel 5: Geo-search

Exempel indexet innehåller ett geo_location fält med latitud-och longitud-koordinater. I det här exemplet används [geo. Distance-funktionen](./search-query-odata-geo-spatial-functions.md#examples) som filtrerar dokument inom omkretsen för en start punkt, ut till ett godtyckligt avstånd (i kilo meter) som du anger. Du kan justera det sista värdet i frågan (4) för att minska eller förstora frågans yta.

Följande exempel är i formatet efter läsbarhet:

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
    {
      "search": "",
      "filter": "geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4",
      "select": "job_id, business_title, work_location",
      "count": "true"
    }
```
För mer läsbara resultat trimmas Sök resultaten för att inkludera ett jobb-ID, en befattning och arbets platsen. De start koordinaterna hämtades från ett slumpmässigt dokument i indexet (i det här fallet för en arbets plats på staten ön.

Du kan också prova detta i Postman med GET:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=&$select=job_id, business_title, work_location&$filter=geo.distance(geo_location, geography'POINT(-74.11734 40.634384)') le 4
```

## <a name="example-6-search-precision"></a>Exempel 6: Sök precision

Term frågor är enskilda termer, kanske många av dem, som utvärderas oberoende av varandra. Fras frågor omges av citat tecken och utvärderas som en orda Grant-sträng. Precisionen för matchningen styrs av operatörer och searchMode.

Exempel 1: **`&search=fire`**  returnerar 150 resultat, där alla matchningar innehåller ordet brand någonstans i dokumentet.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=fire
```

Exempel 2: **`&search=fire department`** returnerar 2002 resultat. Matchningar returneras för dokument som innehåller antingen Fire eller Department.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=fire department
```

Exempel 3: **`&search="fire department"`** returnerar 82 resultat. Att omsluta strängen inom citat tecken är en orda Grant-sökning på båda termerna och matchningar finns i token-termer i indexet, som består av kombinerade villkor. Detta förklarar varför en sökning som **`search=+fire +department`** inte är ekvivalent. Båda villkoren krävs, men genomsöks separat. 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search="fire department"
```

## <a name="example-7-booleans-with-searchmode"></a>Exempel 7: booleska värden med searchMode

Enkel syntax stöder booleska operatorer i form av tecken ( `+, -, |` ). SearchMode-parametern informerar kompromisser mellan precision och återkallande, med `searchMode=any` prioriterat återkallning (som matchar ett kriterium som kvalificerar ett dokument för resultat uppsättningen) och `searchMode=all` prioriterar precision (alla villkor måste matchas). Standardvärdet är `searchMode=any` , vilket kan vara förvirrande om du stackar en fråga med flera operatorer och får bredare i stället för smalare resultat. Detta är särskilt sant, där resultatet innehåller alla dokument som inte innehåller någon speciell term.

Med hjälp av standard searchMode (alla) returneras 2800-dokument: de som innehåller multi-del termen "brand Department", plus alla dokument som inte har termen "Metrotech Center".

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&searchMode=any&search="fire department"  -"Metrotech Center"
```

  :::image type="content" source="media/search-query-simple-examples/searchmodeany.png" alt-text="Sök läge, valfri" border="false":::

Ändra searchMode för att `all` verkställa en kumulativ inverkan på kriterier och returnerar en mindre resultat uppsättning – 21 dokument-bestående av dokument som innehåller hela frasen "brand Department", minus jobben på Metrotech Center-adressen.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&searchMode=all&search="fire department"  -"Metrotech Center"
```
  :::image type="content" source="media/search-query-simple-examples/searchmodeall.png" alt-text="Sök läge alla" border="false":::

## <a name="example-8-structuring-results"></a>Exempel 8: strukturera resultat

Flera parametrar styr vilka fält som finns i Sök resultaten, antalet dokument som returneras i varje batch och sorterings ordning. I det här exemplet finns några av de föregående exemplen som begränsar resultatet till vissa fält med hjälp av **$Select** -satsen och orda Grant Sök villkor, returnerar 82 matchningar 

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"
```
Läggs till i föregående exempel, du kan sortera efter rubrik. Den här sorteringen fungerar eftersom *civil_service_title kan* sorteras i indexet.

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title
```

Växlings resultatet implementeras med hjälp av **$Top** -parametern, i det här fallet returnerar de fem översta dokumenten:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=0
```

Om du vill hämta nästa 5 hoppar du över den första batchen:

```http
https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&$select=job_id,agency,business_title,civil_service_title,work_location,job_description&search="fire department"&$orderby=civil_service_title&$top=5&$skip=5
```

## <a name="next-steps"></a>Nästa steg
Försök att ange frågor i koden. Följande länkar förklarar hur du ställer in Sök frågor för både .NET och REST API med hjälp av den enkla standard syntaxen.

* [Fråga ditt index med .NET SDK](./search-get-started-dotnet.md)
* [Fråga ditt index med hjälp av REST API](./search-get-started-powershell.md)

Ytterligare syntax-referens, fråga arkitektur och exempel finns i följande länkar:

+ [Exempel på Lucene-syntax för att skapa avancerade frågor](search-query-lucene-examples.md)
+ [Så här fungerar fulltextsökning i Azure Cognitive Search](search-lucene-query-architecture.md)
+ [Enkel frågesyntax](/rest/api/searchservice/simple-query-syntax-in-azure-search)
+ [Fullständig Lucene-fråga](/rest/api/searchservice/lucene-query-syntax-in-azure-search)
+ [Filtrera och OrderBy-syntax](/rest/api/searchservice/odata-expression-syntax-for-azure-search)