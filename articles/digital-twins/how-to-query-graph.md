---
title: Fråga tvillingdiagrammet
titleSuffix: Azure Digital Twins
description: Mer information finns i så här frågar du efter information i Azure Digitals dubbla diagram.
author: baanders
ms.author: baanders
ms.date: 11/19/2020
ms.topic: how-to
ms.service: digital-twins
ms.custom: contperf-fy21q2
ms.openlocfilehash: 3fd504ec36abae3f00cd2a7eb4e1f7b639be0cea
ms.sourcegitcommit: afb9e9d0b0c7e37166b9d1de6b71cd0e2fb9abf5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/14/2021
ms.locfileid: "103462685"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>Skicka frågor till Azure Digitals dubbla grafer

Den här artikeln innehåller exempel på frågor och mer detaljerade instruktioner för att använda **Azure Digitals frågespråk** för att skicka frågor till den [dubbla grafen](concepts-twins-graph.md) . (För en introduktion till frågespråket och en fullständig lista över dess funktioner, se [*begrepp: frågespråk*](concepts-query-language.md).)

Den här artikeln börjar med exempel frågor som illustrerar frågans språk struktur och vanliga frågor för digitala enheter. Sedan beskrivs hur du kör dina frågor när du har skrivit dem med hjälp av Azure Digitals API för [frågor](/rest/api/digital-twins/dataplane/query) och svar eller ett [SDK](how-to-use-apis-sdks.md#overview-data-plane-apis).

> [!NOTE]
> Om du kör exempel frågorna nedan med ett API eller SDK-anrop måste du komprimera frågetexten till en enda rad.

## <a name="show-all-digital-twins"></a>Visa alla digitala dubbla

Här är den grundläggande fråga som returnerar en lista över alla digitala enheter i instansen:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="GetAllTwins":::

## <a name="query-by-property"></a>Fråga efter egenskap

Hämta digitala dubbla med **Egenskaper** (inklusive ID och metadata):

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByProperty1":::

Som du ser i frågan ovan, frågas ID: t för en Digitals, med hjälp av fältet metadata `$dtId` .

>[!TIP]
> Om du använder Cloud Shell för att köra en fråga med metadatafält som börjar med `$` , bör du kringgå `$` med ett baktick för att göra det möjligt för Cloud Shell veta att det inte är en variabel och bör användas som en literal i frågetexten.

Du kan också skapa dubbla baserat på **om en viss egenskap har definierats**. Här är en fråga som hämtar dubbla med en definierad *plats* egenskap:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByProperty2":::

Detta kan hjälpa dig att få en upplösning med hjälp av taggarnas *egenskaper,* enligt beskrivningen i [lägga till taggar till digitala dubbla](how-to-use-tags.md). Här är en fråga som hämtar alla dubbla Taggar med *rött*:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryMarkerTags1":::

Du kan också få dubbla baserat på **typen av egenskap**. Här är en fråga som sammanfaller vars *temperatur* egenskap är ett tal:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByProperty3":::

>[!TIP]
> Om en egenskap är av typen `Map` kan du använda mappa nycklar och värden direkt i frågan, så här:
> :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByProperty4":::

## <a name="query-by-model"></a>Fråga efter modell

`IS_OF_MODEL`Operatorn kan användas för att filtrera baserat på den dubbla [**modellen**](concepts-models.md).

Den tar hänsyn till [arvs](concepts-models.md#model-inheritance) -och modell [versioner](how-to-manage-model.md#update-models)och utvärderas till **Sant** för en bestämd mitt om det dubbla uppfyller något av följande villkor:

* Den dubbla implementerar modellen direkt `IS_OF_MODEL()` och versions numret för modellen på den dubbla är *större än eller lika* med versions numret för den angivna modellen
* Den dubbla implementerar en modell som *utökar* modellen till `IS_OF_MODEL()` och den dubblans utökade modell versions nummer är *större än eller lika* med versions numret för den angivna modellen

Om du t. ex. frågar efter dubbla modeller `dtmi:example:widget;4` , returnerar frågan alla dubbla baserat på **version 4 eller senare** av **widgets** modellen, och kan också dela baserat på version **4 eller senare** av **modeller som ärver från widgeten**.

`IS_OF_MODEL` kan ta flera olika parametrar, och resten av det här avsnittet är avsedda för olika alternativ för överlagring.

Den enklaste användningen av `IS_OF_MODEL` tar bara en `twinTypeName` parameter: `IS_OF_MODEL(twinTypeName)` .
Här är ett exempel på en fråga som skickar ett värde i den här parametern:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByModel1":::

Om du vill ange en dubbel samling att söka i när det finns mer än en (t. ex. När en `JOIN` används) lägger du till `twinCollection` parametern: `IS_OF_MODEL(twinCollection, twinTypeName)` .
Här är ett exempel på en fråga som lägger till ett värde för den här parametern:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByModel2":::

Om du vill göra en exakt matchning lägger du till `exact` parametern: `IS_OF_MODEL(twinTypeName, exact)` .
Här är ett exempel på en fråga som lägger till ett värde för den här parametern:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByModel3":::

Du kan också skicka alla tre argumenten tillsammans: `IS_OF_MODEL(twinCollection, twinTypeName, exact)` .
Här är ett exempel på en fråga som anger ett värde för alla tre parametrar:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByModel4":::

## <a name="query-by-relationship"></a>Fråga efter relation

När du frågar baserat på digitala dubbla **relationer**, har Azures digitala dubbla frågespråket frågespråk en speciell syntax.

Relationer hämtas in i frågeomfånget i `FROM`-satsen. En viktig skillnad från "klassiska" SQL-typ språk är att varje uttryck i den här `FROM` satsen inte är en tabell. i stället `FROM` uttrycker satsen en relation mellan olika enheter och är skriven med en digital Azure-version av `JOIN` .

Kom ihåg att med Azure Digitals dubbla [modell](concepts-models.md) funktioner, finns det inga relationer oberoende av varandra. Det innebär att `JOIN` i Azure Digital Twins-frågespråket skiljer sig lite från den allmänna SQL-versionen av `JOIN`eftersom relationer här inte kan frågas separat och måste vara knutna till en tvilling.
För att ta med den här skillnaden används nyckelordet `RELATED` i `JOIN`-satsen för att referera till en tvillings uppsättning av relationer.

Följande avsnitt innehåller flera exempel på hur det ser ut.

> [!TIP]
> Den här funktionen imiterar den här funktionen de dokumentbaserade funktionerna i CosmosDB, där `JOIN` kan utföras på underordnade objekt i ett dokument. CosmosDB använder `IN` nyckelordet för att ange att `JOIN` är avsett att iterera över mat ris element i det aktuella Sammanhangs dokumentet.

### <a name="relationship-based-query-examples"></a>Exempel på Relations hip-baserade frågor

Om du vill hämta en data uppsättning som inkluderar relationer använder `FROM` du ett enda uttryck följt av N `JOIN` -instruktioner, där `JOIN` uttrycken uttrycker relationer för resultatet av en tidigare `FROM` or- `JOIN` instruktion.

Här är en exempel Relations hip-baserad fråga. Det här kodfragmentet väljer alla digitala, dubbla med *ID-* egenskapen för "ABC", och alla digitala garn som är relaterade till dessa Digitala flätar via en *contains* -relation.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByRelationship1":::

> [!NOTE]
> Utvecklaren behöver inte korrelera detta `JOIN` med ett nyckel värde i `WHERE` -satsen (eller ange ett nyckel värde infogat med `JOIN` definitionen). Den här korrelationen beräknas automatiskt av systemet, eftersom relationsegenskaperna själva identifierar målentiteten.

### <a name="query-the-properties-of-a-relationship"></a>Ställ frågor till en relations egenskaper

På liknande sätt som digitala tvillingar har egenskaper som beskrivs via DTDL så kan även relationer ha egenskaper. Du kan fråga efter varandra **utifrån egenskaperna för deras relationer**.
Med Azures digitala Flätaa frågespråk kan du filtrera och projicera relationer genom att tilldela ett alias till relationen i- `JOIN` satsen.

Anta till exempel en *servicedBy* -relation som har en *reportedCondition* -egenskap. I nedanstående fråga får den här relationen ett alias för R för att referera till egenskapen.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByRelationship2":::

I exemplet ovan noterar du hur *reportedCondition* är en egenskap hos *servicedBy* -relationen (inte av någon digital, som har en *servicedBy* -relation).

### <a name="query-with-multiple-joins"></a>Fråga med flera kopplingar

Upp till fem `JOIN` s stöds i en enda fråga. På så sätt kan du förflytta flera nivåer av relationer samtidigt.

Här är ett exempel på en fråga med flera kopplingar, som hämtar alla lampor som finns på ljus panelerna i rum 1 och 2.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="QueryByRelationship3":::

## <a name="count-items"></a>Antal objekt

Du kan räkna antalet objekt i en resultat uppsättning med hjälp av- `Select COUNT` satsen:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="SelectCount1":::

Lägg till en `WHERE` sats för att räkna antalet objekt som uppfyller ett visst kriterium. Här följer några exempel på hur du kan räkna med ett applicerat filter baserat på typen av dubbel modell (mer information om den här syntaxen finns i [*fråga efter modell*](#query-by-model) nedan):

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="SelectCount2":::

Du kan också använda `COUNT` tillsammans med- `JOIN` satsen. Här är en fråga som räknar alla lampor som finns på de ljusa panelerna i rum 1 och 2:

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="SelectCount3":::

## <a name="filter-results-select-top-items"></a>Filter resultat: Markera de översta objekten

Du kan välja flera "Top"-objekt i en fråga med hjälp av- `Select TOP` satsen.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="SelectTop":::

## <a name="filter-results-specify-return-set-with-projections"></a>Filter resultat: Ange en retur uppsättning med projektioner

Genom att använda projektioner i `SELECT` instruktionen kan du välja vilka kolumner som en fråga ska returnera.

>[!NOTE]
>För tillfället stöds inte komplexa egenskaper. För att se till att projektions egenskaperna är giltiga kombinerar du projektionerna med en `IS_PRIMITIVE` bock.

Här är ett exempel på en fråga som använder projektion för att returnera dubbla och relationer. Följande fråga projekterar *konsumenten*, *fabriken* och *Edge* från ett scenario där en *fabrik* med ID: t *ABC* är relaterad till *konsumenten* via en relation av *fabriken. kunden* och relationen presenteras som en *gräns*.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections1":::

Du kan också använda projektion för att returnera en dubbels egenskap. I följande fråga visas *namn* egenskapen för de *konsumenter* som är relaterade till *fabriken* med ID: t *ABC* via en relation av *Factory. Custom*.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections2":::

Du kan också använda projektion för att returnera en egenskap för en relation. Som i föregående exempel projekt i följande fråga projektets *namn* egenskap för de *konsumenter* som är relaterade till *fabriken* med ID: t *ABC* genom en relation mellan *fabrik. kund*; men nu returnerar den även två egenskaper för relationen, *prop1* och *prop2*. Det gör detta genom att namnge Relations *kanten* och samla in dess egenskaper.  

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections3":::

Du kan också använda alias för att förenkla frågor med projektion.

Följande fråga utför samma åtgärder som i föregående exempel, men ger alias för egenskaps namnen till,, `consumerName` `first` `second` och `factoryArea` .

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections4":::

Här är en liknande fråga som frågar samma uppsättning som ovan, men projekterar endast egenskapen *Consumer.name* som `consumerName` , och projekterar hela *fabriken* som en dubbel.

:::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="Projections5":::

## <a name="build-efficient-queries-with-the-in-operator"></a>Bygg effektiva frågor med operatorn IN

Du kan avsevärt minska antalet frågor som du behöver genom att skapa en matris med dubbla och frågor med `IN` operatorn. 

Anta till exempel ett scenario där *byggnader* som innehåller *golv* och *golv* innehåller *rum*. Ett sätt att söka efter rum i en byggnad som är frekvent är att följa dessa steg.

1. Hitta golv i byggnaden baserat på `contains` relationen.

    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="INOperatorWithout":::

2. Om du vill hitta rum, i stället för att ta med golv en-i-ett och köra en `JOIN` fråga för att hitta rummen för var och en, kan du fråga med en samling våningar i byggnaden (med namnet *våning* i frågan nedan).

    I klient program:
    
    ```csharp
    var floors = "['floor1','floor2', ..'floorn']"; 
    ```
    
    I fråga:
    
    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="INOperatorWith":::

## <a name="other-compound-query-examples"></a>Exempel på andra sammansatta frågor

Du kan **kombinera** någon av ovanstående typer av fråga med hjälp av kombinations operatorer för att inkludera mer information i en enskild fråga. Här följer några ytterligare exempel på sammansatta frågor som frågar efter fler än en typ av dubbel beskrivare på en gång.

* Från de enheter som *Room 123* har kan du returnera de MxChip-enheter som hanterar rollen operatör
    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="OtherExamples1":::
* Hämta dubbla som har en relation som heter *innehåller* med en annan som har ID: t *id1*
    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="OtherExamples2":::
* Hämta alla rum för den här rums modellen som finns i *floor11*
    :::code language="sql" source="~/digital-twins-docs-samples/queries/queries.sql" id="OtherExamples3":::

## <a name="run-queries-with-the-api"></a>Köra frågor med API: et

När du har bestämt dig för en frågesträng kör du den genom att anropa API: et för [**frågor**](/rest/api/digital-twins/dataplane/query).

Du kan anropa API: et direkt eller använda någon av [SDK: erna](how-to-use-apis-sdks.md#overview-data-plane-apis) som är tillgängliga för Azure Digitals dubbla.

Följande kodfragment illustrerar [.net (C#) SDK-](/dotnet/api/overview/azure/digitaltwins/client) anropet från en klient app:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="RunQuery":::

Frågan som används i det här anropet returnerar en lista över digitala dubbla, som exemplet ovan representerar med [BasicDigitalTwin](/dotnet/api/azure.digitaltwins.core.basicdigitaltwin) -objekt. Retur typen för dina data för varje fråga beror på vilka villkor som du anger med `SELECT` instruktionen:
* Frågor som börjar med `SELECT * FROM ...` returnerar en lista över digitala dubbla objekt (som kan serialiseras som `BasicDigitalTwin` objekt eller andra anpassade digitala, dubbla typer som du kan ha skapat).
* Frågor som börjar i formatet `SELECT <A>, <B>, <C> FROM ...` returnerar en ord lista med nycklar `<A>` , `<B>` och `<C>` .
* Andra `SELECT` uttrycks format kan användas för att returnera anpassade data. Du kan överväga att skapa egna klasser för att hantera mycket anpassade resultat uppsättningar. 

### <a name="query-with-paging"></a>Fråga med sid indelning

Fråge anrop stöder sid indelning. Här är ett fullständigt exempel som använder sig av `BasicDigitalTwin` typen frågeresultat med fel hantering och växling:

:::code language="csharp" source="~/digital-twins-docs-samples/sdks/csharp/queries.cs" id="FullQuerySample":::

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [Azure Digitals dubbla API: er och SDK: er](how-to-use-apis-sdks.md), inklusive fråge-API som används för att köra frågorna från den här artikeln.
