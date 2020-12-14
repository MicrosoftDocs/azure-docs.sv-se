---
title: Använd fullständig Lucene-frågesyntax
titleSuffix: Azure Cognitive Search
description: Lucene-frågesyntax för fuzzy search, närhets sökning, term förstärkning, sökning efter reguljära uttryck och jokertecken i en Azure Kognitiv sökning-tjänst.
manager: nitinme
author: HeidiSteen
ms.author: heidist
tags: Lucene query analyzer syntax
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/05/2020
ms.openlocfilehash: df26cfc3b220f40a7e73ff1c750d2b2ae37e7625
ms.sourcegitcommit: cc13f3fc9b8d309986409276b48ffb77953f4458
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/14/2020
ms.locfileid: "97401465"
---
# <a name="use-the-full-lucene-search-syntax-advanced-queries-in-azure-cognitive-search"></a>Använd den fullständiga Söksyntaxen för Lucene (avancerade frågor i Azure Kognitiv sökning)

När du konstruerar frågor för Azure Kognitiv sökning kan du ersätta den [enkla standard fråge parsern](query-simple-syntax.md) med den mer kraftfulla [Lucene-frågedesignern i Azure kognitiv sökning](query-lucene-syntax.md) för att formulera specialiserade och avancerade fråge definitioner. 

Lucene-parsern stöder komplexa fråge konstruktioner, t. ex. frågor med fält omfång, fuzzy search, infix och suffix med jokertecken, närhets sökning, term förstärkning och sökning efter reguljära uttryck. Den extra kraften ger ytterligare bearbetnings krav så att du kan förvänta dig en något längre körnings tid. I den här artikeln kan du gå igenom exempel som demonstrerar frågor som baseras på fullständig syntax.

> [!Note]
> Många av de specialiserade fråge konstruktioner som aktive ras med fullständig Lucene-frågesyntax är inte [text-analyserad](search-lucene-query-architecture.md#stage-2-lexical-analysis), vilket kan vara överraskande om du förväntar dig eller lemmatisering. Lexikalisk analys utförs endast på fullständiga villkor (en term fråga eller en fras fråga). Frågetyper med ofullständiga termer (prefixlängd, jokertecken, regex-fråga, fuzzy-fråga) läggs direkt till i frågans träd, vilket kringgår analys steget. Den enda omvandlingen som utförs på ofullständiga sökord är lowercasing. 
>

## <a name="nyc-jobs-examples"></a>Exempel på NYC-jobb

I följande exempel används [Sök indexet för NYC-jobb](https://azjobsdemo.azurewebsites.net/)  som består av jobb som är tillgängliga baserat på en data uppsättning som tillhandahålls av Göteborg från [New York-data initiativ](https://nycopendata.socrata.com/). Dessa data ska inte betraktas som aktuella eller fullständiga. Indexet finns på en sandbox-tjänst från Microsoft, vilket innebär att du inte behöver en Azure-prenumeration eller Azure-Kognitiv sökning för att testa dessa frågor.

Vad du behöver är Postman eller ett motsvarande verktyg för att skicka HTTP-begäran på GET-eller POST. Om du inte känner till dessa verktyg, se [snabb start: utforska Azure Kognitiv sökning REST API](search-get-started-rest.md).

## <a name="set-up-the-request"></a>Konfigurera begäran

1. Begärandehuvuden måste ha följande värden:

   | Tangent | Värde |
   |-----|-------|
   | Content-Type | `application/json`|
   | API-nyckel  | `252044BE3886FE4A8E3BAA4F595114BB` </br> (det här är den faktiska fråge-API-nyckeln för tjänsten sandbox search som är värd för NYC-jobb index) |

1. Ange verbet till **`GET`** .

1. Ange URL: en till **`https://azs-playground.search.windows.net/indexes/nycjobs/docs/search=*&api-version=2020-06-30&queryType=full`**

   + Dokument samlingen i indexet innehåller allt sökbart innehåll. En API-nyckel för frågor som anges i huvudet för begäran fungerar bara för Läs åtgärder som riktar in dokument samlingen.

   + **`$count=true`** Returnerar ett antal dokument som matchar Sök kriterierna. I en tom Sök sträng är antalet dokument i indexet (cirka 2558 om det gäller NYC-jobb).

   + **`search=*`** är en ospecificerad fråga som motsvarar null eller tom sökning. Det är inte särskilt användbart, men det är den enklaste sökningen du kan göra, och det visar alla hämtnings bara fält i indexet, med alla värden.

   + **`queryType=full`** anropar fullständig Lucene Analyzer.

1. Som ett verifierings steg klistrar du in följande begäran i Hämta och klickar på **Skicka**. Resultat returneras som utförliga JSON-dokument.

   ```http
   https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30&$count=true&search=*&queryType=full
   ```

### <a name="how-to-invoke-full-lucene-parsing"></a>Så här anropar du fullständig Lucene-parsning

Lägg till **`queryType=full`** för att anropa fullständig frågesyntax och åsidosätta standardvärdet för enkel frågesyntax. Alla exempel i den här artikeln anger **`queryType=full`** Sök parametern som anger att den fullständiga syntaxen hanteras av Lucene-frågeuttrycket. 

```http
POST /indexes/nycjobs/docs/search?api-version=2020-06-30
{
    "queryType": "full"
}
```

## <a name="example-1-query-scoped-to-a-list-of-fields"></a>Exempel 1: frågan är begränsad till en lista med fält

Det första exemplet är inte pars-Specific, men vi leder det till att introducera det första grundläggande frågeuttrycket: inne slutningen. I det här exemplet begränsas både frågekörningen och svaret på bara några få angivna fält. Att lära dig att strukturera ett läsbart JSON-svar är viktigt när ditt verktyg är Postman eller Sök Utforskaren. 

Den här frågan är bara avsedd för *business_title* i **`searchFields`** och anger genom **`select`** parametern i samma fält i svaret.

```http
POST https://azs-playground.search.windows.net/indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "*",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Svar för den här frågan bör se ut ungefär som på följande skärm bild.

  ![Postman-exempel svar med Poäng](media/search-query-lucene-examples/postman-sample-results.png)

Du kanske har lagt märke till Sök poängen i svaret. Enhetliga resultat på **1** inträffar när det inte finns någon rang, antingen på grund av att sökningen inte var full texts ökning eller att inga kriterier tillhandahölls. För en tom sökning kommer rader tillbaka i valfri ordning. När du inkluderar de faktiska kriterierna visas Sök resultat som utvecklas till meningsfulla värden.

## <a name="example-2-fielded-search"></a>Exempel 2: Fältad sökning

Fullständig Lucene-syntax har stöd för omfångs enskilda Sök uttryck till ett visst fält. Det här exemplet söker efter affärs titlar med termen Senior, men inte Junior. Du kan ange flera fält med hjälp av och.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:(senior NOT junior) AND posting_type:external",
    "searchFields": "business_title, posting_type",
    "select": "business_title, posting_type"
}
```

Svar för den här frågan bör se ut ungefär som på följande skärm bild (posting_type visas inte).

  :::image type="content" source="media/search-query-lucene-examples/intrafieldfilter.png" alt-text="Sök uttryck för exempel svar på Postman" border="false":::

Sök uttrycket kan vara ett enstaka ord eller en fras, eller ett mer komplext uttryck inom parentes, eventuellt med booleska operatorer. Några exempel är följande:

+ `business_title:(senior NOT junior)`
+ `state:("New York" OR "New Jersey")`
+ `business_title:(senior NOT junior) AND posting_type:external`

Se till att placera flera strängar inom citat tecken om du vill att båda strängarna ska utvärderas som en enda entitet, som i det här fallet söker efter två olika platser i `state` fältet. Beroende på verktyget kan du behöva Escape ( `\` ) citat tecken. 

Fältet som anges i **FieldName: searchExpression** måste vara ett sökbart fält. Mer information om hur indexera attribut används i fält definitioner finns i [skapa index (Azure Kognitiv sökning REST API)](/rest/api/searchservice/create-index) .

> [!NOTE]
> I exemplet ovan **`searchFields`** utelämnas parametern eftersom varje del av frågan har ett angett fält namn explicit. Du kan dock fortfarande använda **`searchFields`** om frågan har flera delar (med och-instruktioner). Frågan skulle till exempel `search=business_title:(senior NOT junior) AND external&searchFields=posting_type` matcha `senior NOT junior` enbart till `business_title` fältet, medan den matchar "extern" med `posting_type` fältet. Det fält namn som har angetts i `fieldName:searchExpression` har alltid företräde framför **`searchFields`** , vilket är anledningen till att vi kan utelämna från i det här exemplet `business_title` **`searchFields`** .

## <a name="example-3-fuzzy-search"></a>Exempel 3: suddig sökning

Fullständig Lucene-syntax stöder också fuzzy search, som matchar på villkor som har en liknande konstruktion. Om du vill göra en Fuzzy-sökning lägger du till Tilde- `~` symbolen i slutet av ett enstaka ord med en valfri parameter, ett värde mellan 0 och 2, som anger redigerings avståndet. Till exempel `blue~` eller `blue~1` skulle returnera blå, blå och limma.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:asosiate~",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Fraser stöds inte direkt, men du kan ange en Fuzzy-matchning på varje term i en sats med flera delar, till exempel `search=business_title:asosiate~ AND comm~` .  I skärm bilden nedan innehåller svaret en matchning på *grupp kopplingen*.

  :::image type="content" source="media/search-query-lucene-examples/fuzzysearch.png" alt-text="Fuzzy search-svar" border="false":::

> [!Note]
> Fuzzy-frågor [analyseras](search-lucene-query-architecture.md#stage-2-lexical-analysis)inte. Frågetyper med ofullständiga termer (prefixlängd, jokertecken, regex-fråga, fuzzy-fråga) läggs direkt till i frågans träd, vilket kringgår analys steget. Den enda omvandlingen som utförs på ofullständiga sökord är lowercasing.
>

## <a name="example-4-proximity-search"></a>Exempel 4: närhets sökning

Närhets sökningar används för att hitta termer som ligger nära varandra i ett dokument. Infoga en tilde ~-symbol i slutet av en fras följt av antalet ord som skapar närhets kanten. Till exempel "hotell flyg plats" ~ 5 hittar du villkoret hotell och flyg plats inom 5 ord för varandra i ett dokument.

Den här frågan söker efter termerna "senior" och "analytiker", där varje term skiljs åt av fler än ett ord och citat tecknen är undantagna ( `\"` ) för att bevara frasen:

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:\"senior analyst\"~1",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Svar för den här frågan bör se ut ungefär som på följande skärm bild 

  :::image type="content" source="media/search-query-lucene-examples/proximity-before.png" alt-text="Närhets fråga" border="false":::

Prova igen, vilket eliminerar avståndet ( `~0` ) mellan begreppen "senior analytiker". Observera att åtta dokument returneras för den här frågan i stället för 10 för föregående fråga.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:\"senior analyst\"~0",
    "searchFields": "business_title",
    "select": "business_title"
}
```

## <a name="example-5-term-boosting"></a>Exempel 5: term förstärkning

Term förstärkning syftar på att rangordna ett dokument högre om det innehåller den ökade perioden, i förhållande till dokument som inte innehåller termen. Om du vill förstärka en term använder du cirkumflex, `^` symbol med en förstärknings faktor (ett tal) i slutet av den period som du söker.

I den här frågan kan du söka efter jobb med termen *dator analytiker* och Observera att det inte finns några resultat med *både ord och* *analytiker*, men *dator* jobb är högst upp i resultaten.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:computer analyst",
    "searchFields": "business_title",
    "select": "business_title"
}
```

I frågan "efter" upprepar du sökningen, den här tiden ökar resultatet med termen *analytiker* över termen *dator* om båda orden inte finns. En mänsklig läsbar version av frågan är `search=business_title:computer analyst^2` . För en fungerande fråga i Postman `^2` kodas det som `%5E2` .

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:computer analyst%5e2",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Svar för den här frågan bör se ut ungefär som på följande skärm bild.

  :::image type="content" source="media/search-query-lucene-examples/termboostingafter.png" alt-text="Term förstärkning efter" border="false":::

Term förstärkning skiljer sig från bedömnings profiler i dessa bedömnings profiler ökar vissa fält i stället för specifika villkor. I följande exempel kan du illustrera skillnaderna.

Överväg en bedömnings profil som höjer träffar i ett visst fält, till exempel **Genre** i musicstoreindex-exemplet. Termen förstärkning kan användas för att ytterligare öka vissa Sök villkor som är större än andra. Exempelvis ökar "Rock ^ 2 Electronic" dokument som innehåller Sök villkoren i fältet **Genre** högre än andra sökbara fält i indexet. Dessutom rangordnas dokument som innehåller Sök termen "Rock" högre än den andra Sök termen "elektronisk" som ett resultat av termen förstärknings värde (2).

När du anger faktor nivå, desto högre grad av öknings faktorn, är det mer relevant att ange villkoret i förhållande till andra Sök villkor. Som standard är förstärknings faktorn 1. Förstärknings faktorn måste vara positiv, men den kan vara mindre än 1 (till exempel 0,2).

## <a name="example-6-regex"></a>Exempel 6: regex

En sökning efter reguljära uttryck hittar en matchning baserat på innehållet mellan snedstreck "/", enligt beskrivningen i [klassen RegExp](https://lucene.apache.org/core/6_6_1/core/org/apache/lucene/util/automaton/RegExp.html).

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:/(Sen|Jun)ior/",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Svar för den här frågan bör se ut ungefär som på följande skärm bild.

  :::image type="content" source="media/search-query-lucene-examples/regex.png" alt-text="Regex-fråga" border="false":::

> [!Note]
> Regex-frågor [analyseras](./search-lucene-query-architecture.md#stage-2-lexical-analysis)inte. Den enda omvandlingen som utförs på ofullständiga sökord är lowercasing.
>

## <a name="example-7-wildcard-search"></a>Exempel 7: sökning med jokertecken

Du kan använda allmänt identifierad syntax för jokertecken för flera ( \* ) eller enkla (?)-tecknen. Observera att funktionen Lucene Query parser stöder användningen av dessa symboler med en enda term och inte en fras.

I den här frågan kan du söka efter jobb som innehåller prefixet "PROG" som innehåller affärs titlar med termerna programmering och programmerare. Du kan inte använda en `*` eller- `?` symbol som det första tecknet i en sökning.

```http
POST /indexes/nycjobs/docs?api-version=2020-06-30
{
    "count": true,
    "queryType": "full",
    "search": "business_title:prog*",
    "searchFields": "business_title",
    "select": "business_title"
}
```

Svar för den här frågan bör se ut ungefär som på följande skärm bild.

  :::image type="content" source="media/search-query-lucene-examples/wildcard.png" alt-text="Fråga med jokertecken" border="false":::

> [!Note]
> Jokertecken har inte [analyser](./search-lucene-query-architecture.md#stage-2-lexical-analysis)ATS. Den enda omvandlingen som utförs på ofullständiga sökord är lowercasing.
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
+ [Fullständigt Lucene-frågesyntax](query-lucene-syntax.md)
+ [Filter-syntax](search-query-odata-filter.md)