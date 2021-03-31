---
title: Fuzzy-sökning
titleSuffix: Azure Cognitive Search
description: Implementera en "menade du" Sök funktion för att automatiskt korrigera en felstavad term eller skrivfel.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: b2f2c8497d5365104a5ffc361b791450925d0c19
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101694792"
---
# <a name="fuzzy-search-to-correct-misspellings-and-typos"></a>Fuzzy-sökning för att korrigera fel stavningar och skrivfel

Azure Kognitiv sökning stöder fuzzy search, en typ av fråga som kompenserar för skrivfel och felstavade termer i Indatasträngen. Det gör det genom att söka efter villkor med liknande sammansättning. Om du expanderar sökning för att lägga till nära matchningar har du möjlighet att automatiskt korrigera ett skrivfel när det är bara några fel placerade tecken. 

## <a name="what-is-fuzzy-search"></a>Vad är en suddig sökning?

Det är en expansions övning som ger en matchning på villkor som har en liknande sammansättning. När en Fuzzy-sökning anges skapar motorn ett diagram (baserat på [deterministiska begränsade kontoresurserna teori](https://en.wikipedia.org/wiki/Deterministic_finite_automaton)) av likartade villkor för alla hela termer i frågan. Om din fråga till exempel innehåller tre villkor "University of Washington", skapas en graf för varje term i frågan `search=university~ of~ washington~` (det finns ingen borttagning av stopp ord i en suddig sökning, så "av" hämtar en graf).

Grafen består av upp till 50 expansionar, eller permutationer, av varje term, och fångar både korrekta och felaktiga varianter i processen. Motorn returnerar sedan de översta relevanta matchningarna i svaret. 

För en term som "universitet" kan grafen ha "unversty, Universty, University, universum, invertering". Alla dokument som matchar i diagrammet ingår i resultaten. Jämfört med andra frågor som analyserar texten för att hantera olika former av samma ord ("möss" och "Mouse"), tas jämförelserna i en Fuzzy-fråga till sitt värde utan någon språklig analys på texten. "Universum" och "Invertera", som är semantiskt olika, matchar eftersom syntaktiska skillnader är små.

En matchning fungerar om avvikelserna är begränsade till två eller färre ändringar, där en redigering är ett infogat, Borttaget, ersatt eller transponerat tecken. Algoritmen för sträng korrigering som anger differentialen är [Damerau-levenshtein avstånds](https://en.wikipedia.org/wiki/Damerau%E2%80%93Levenshtein_distance) måttet, som beskrivs som "minsta antal åtgärder (infogningar, borttagningar, ersättningar eller placeringar av två intilliggande tecken) som krävs för att ändra ett ord till ett annat". 

I Azure Kognitiv sökning:

+ En Fuzzy-fråga gäller hela villkor, men du kan ge stöd för fraser genom och konstruktioner. Exempel: "Unviersty ~ ~" Wshington ~ "skulle matcha" University of Washington ".

+ Standard avståndet för en redigering är 2. Värdet `~0` är ingen expansion (endast den exakta termen betraktas som en matchning), men du kan ange `~1` en skillnad i en viss skillnad eller en redigering. 

+ En Fuzzy-fråga kan expandera en term upp till 50 ytterligare permutationer. Den här gränsen kan inte konfigureras, men du kan minska antalet utökningar effektivt genom att minska redigerings avståndet till 1.

+ Svar består av dokument som innehåller en relevant matchning (upp till 50).

Tillsammans skickas graferna som matchnings villkor mot token i indexet. Som du kan föreställa dig är den suddiga sökningen långsammare än andra fråge formulär. Indexets storlek och komplexitet kan avgöra om fördelarna är tillräckligt stora för att kompensera svarets svars tid.

> [!NOTE]
> Eftersom en Fuzzy-sökning tenderar att bli långsam, kan det vara ett alternativ för att undersöka alternativ som t. ex. n-gram-indexering, med förloppet av korta teckensekvens (två och tre teckensekvens för dubbel gram och trigram-token). Beroende på språk och fråge yta kan n-gram ge dig bättre prestanda. Det är mycket lagrings utrymme att indexera n-gram och genererar mycket större index.
>
> Ett annat alternativ, som du kan överväga om du vill hantera enbart de mest egregiousa fallen, är en [synonym karta](search-synonyms.md). Du kan till exempel mappa "search" till "sökning, serch, Sarch" eller "Hämta" till "Hämta".

## <a name="indexing-for-fuzzy-search"></a>Indexering för fuzzy-sökning

Analys verktyg används inte vid frågekörning för att skapa ett expansions diagram, men det är inte medelvärde för analys verktyg som inte ska ignoreras i fuzzy-sökscenarier. Efter alla används analys verktyg under indexeringen för att skapa tokens som matchar, om frågan är kostnads fri, filtrerad sökning eller en Fuzzy-sökning med en graf som indatamängden. 

I allmänhet är beslutet att finjustera analys kedjan baserat på det primära användnings fallet (ett filter eller fullständig texts ökning) i stället för särskilda fråge formulär som fuzzy search när du tilldelar analys verktyg per fält. Därför finns det inte någon speciell analys rekommendation för fuzzy search. 

Men om test frågorna inte producerar de matchningar du förväntar dig kan du prova att variera indexerings analysen, ställa in den på en [språk analys](index-add-language-analyzers.md), för att se om du får bättre resultat. Vissa språk, särskilt de med vokal mutationer, kan dra nytta av Bryt och oregelbundna ord former som genereras av Microsofts naturliga språk processorer. I vissa fall kan du använda rätt språk analys för att göra en skillnad i om en term är token på ett sätt som är kompatibelt med det värde som anges av användaren.

## <a name="how-to-use-fuzzy-search"></a>Använda FUZZY search

Fuzzy-frågor konstrueras med fullständig Lucene-frågesyntax och anropar sedan [Lucene-frågeuttrycket](https://lucene.apache.org/core/6_6_1/queryparser/org/apache/lucene/queryparser/classic/package-summary.html).

1. Ange fullständig Lucene-parser på frågan ( `queryType=full` ).

1. Du kan också begränsa begäran till vissa fält med hjälp av den här parametern ( `searchFields=<field1,field2>` ). 

1. Lägg till tilde ( `~` )-operatorn i slutet av hela termen ( `search=<string>~` ).

   Ta med en valfri parameter, ett tal mellan 0 och 2 (standard) om du vill ange redigerings avståndet ( `~1` ). Till exempel "blå ~" eller "blå ~ 1" returnerar "blått", "blått" och "lim".

I Azure Kognitiv sökning, förutom termen och avståndet (högst 2), finns det inga ytterligare parametrar att ange för frågan.

> [!NOTE]
> Under bearbetningen av frågan genomgår fuzzy-frågor inte [lexikal analys](search-lucene-query-architecture.md#stage-2-lexical-analysis). Frågeresultatet läggs in direkt i frågefönstret och expanderas för att skapa en diagram med villkor. Den enda omvandlingen som utförs är mindre Skift läge.

## <a name="testing-fuzzy-search"></a>Testa fuzzy-sökning

För enkel testning rekommenderar vi [Sök Utforskaren](search-explorer.md) eller [Postman](search-get-started-rest.md) för att iterera över ett frågeuttryck. Båda verktygen är interaktiva, vilket innebär att du snabbt kan stega igenom flera varianter av en term och utvärdera svaren som kommer tillbaka.

När resultatet är tvetydigt kan [träff markerings](search-pagination-page-layout.md#hit-highlighting) funktionen hjälpa dig att identifiera matchningen i svaret. 

> [!Note]
> Användningen av träff markering för att identifiera fuzzy-matchningar har begränsningar och fungerar bara för en grundläggande fuzzy-sökning. Om ditt index har bedömnings profiler, eller om du skiktar frågan med ytterligare syntax, kan träff markering inte identifiera matchningen. 

### <a name="example-1-fuzzy-search-with-the-exact-term"></a>Exempel 1: suddig sökning med den exakta termen

Anta att följande sträng finns i ett `"Description"` fält i ett Sök dokument: `"Test queries with special characters, plus strings for MSFT, SQL and Java."`

Börja med en Fuzzy-sökning på "Special" och Lägg till träff markeringar i fältet Beskrivning:

```console
search=special~&highlight=Description
```

Eftersom du har lagt till träff markering i svaret används formateringen till "Special" som matchnings villkor.

```output
"@search.highlights": {
    "Description": [
        "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
    ]
```

Försök igen, stava stavfel "Special" genom att ta bort flera bokstäver ("PE"):

```console
search=scial~&highlight=Description
```

Hittills, ingen ändring av svaret. Om du använder standardvärdet på 2 grader, tar du bort två tecken "PE" från "Special", vilket gör det möjligt för en lyckad matchning på den termen.

```output
"@search.highlights": {
    "Description": [
        "Test queries with <em>special</em> characters, plus strings for MSFT, SQL and Java."
    ]
```

Om du försöker med en mer begäran kan du ändra Sök termen ytterligare genom att ta bort ett sista bokstavs antal tre borttagningar (från "Special" till "skal"):

```console
search=scal~&highlight=Description
```

Observera att samma svar returneras, men nu i stället för att matcha "Special", är den suddiga matchningen på "SQL".

```output
        "@search.score": 0.4232868,
        "@search.highlights": {
            "Description": [
                "Mix of special characters, plus strings for MSFT, <em>SQL</em>, 2019, Linux, Java."
            ]
```

Punkten för det här expanderade exemplet är att illustrera den klarhet som träff markeringar kan få till tvetydiga resultat. I samtliga fall returneras samma dokument. Var du tvungen att använda dokument-ID: n för att verifiera en matchning. du kan ha missat skiftet från "Special" till "SQL".

## <a name="see-also"></a>Se även

+ [Hur full texts ökning fungerar i Azure Kognitiv sökning (arkitektur för frågekörning)](search-lucene-query-architecture.md)
+ [Sök Utforskaren](search-explorer.md)
+ [Så här frågar du i .NET](./search-get-started-dotnet.md)
+ [Fråga i REST](./search-get-started-powershell.md)