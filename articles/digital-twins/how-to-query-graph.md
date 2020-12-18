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
ms.openlocfilehash: df7462cf047dd113c34669d9a5f68f2589cc50f4
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97673001"
---
# <a name="query-the-azure-digital-twins-twin-graph"></a>Skicka frågor till Azure Digitals dubbla grafer

Den här artikeln innehåller exempel på frågor och mer detaljerade instruktioner för att använda **Azure Digitals frågespråk** för att skicka frågor till den [dubbla grafen](concepts-twins-graph.md) . (För en introduktion till frågespråket och en fullständig lista över dess funktioner, se [*begrepp: frågespråk*](concepts-query-language.md).)

Den här artikeln börjar med exempel frågor som illustrerar frågans språk struktur och vanliga frågor för digitala enheter. Sedan beskrivs hur du kör dina frågor när du har skrivit dem med hjälp av Azure Digitals API för [frågor](/rest/api/digital-twins/dataplane/query) och svar eller ett [SDK](how-to-use-apis-sdks.md#overview-data-plane-apis).

> [!TIP]
> Om du kör exempel frågorna nedan med ett API eller SDK-anrop måste du komprimera frågetexten till en enda rad.

## <a name="show-all-digital-twins"></a>Visa alla digitala dubbla

Här är den grundläggande fråga som returnerar en lista över alla digitala enheter i instansen:

```sql
SELECT *
FROM DIGITALTWINS
```

## <a name="query-by-property"></a>Fråga efter egenskap

Hämta digitala dubbla med **Egenskaper** (inklusive ID och metadata):

```sql
SELECT  *
FROM DigitalTwins T  
WHERE T.firmwareVersion = '1.1'
AND T.$dtId in ['123', '456']
AND T.Temperature = 70
```

> [!TIP]
> ID: t för en digital delad frågas med hjälp av fältet metadata `$dtId` .

Du kan också skapa dubbla baserat på **om en viss egenskap har definierats**. Här är en fråga som hämtar dubbla med en definierad *plats* egenskap:

```sql
SELECT * FROM DIGITALTWINS WHERE IS_DEFINED(Location)
```

Detta kan hjälpa dig att få en upplösning med hjälp av taggarnas *egenskaper,* enligt beskrivningen i [lägga till taggar till digitala dubbla](how-to-use-tags.md). Här är en fråga som hämtar alla dubbla Taggar med *rött*:

```sql
SELECT * FROM DIGITALTWINS WHERE IS_DEFINED(tags.red)
```

Du kan också få dubbla baserat på **typen av egenskap**. Här är en fråga som sammanfaller vars *temperatur* egenskap är ett tal:

```sql
SELECT * FROM DIGITALTWINS T WHERE IS_NUMBER(T.Temperature)
```

## <a name="query-by-model"></a>Fråga efter modell

`IS_OF_MODEL`Operatorn kan användas för att filtrera baserat på den dubbla [**modellen**](concepts-models.md).

Den tar hänsyn till [arvs](concepts-models.md#model-inheritance) -och modell [versioner](how-to-manage-model.md#update-models)och utvärderas till **Sant** för en bestämd mitt om det dubbla uppfyller något av följande villkor:

* Den dubbla implementerar modellen direkt `IS_OF_MODEL()` och versions numret för modellen på den dubbla är *större än eller lika* med versions numret för den angivna modellen
* Den dubbla implementerar en modell som *utökar* modellen till `IS_OF_MODEL()` och den dubblans utökade modell versions nummer är *större än eller lika* med versions numret för den angivna modellen

Om du t. ex. frågar efter dubbla modeller `dtmi:example:widget;4` , returnerar frågan alla dubbla baserat på **version 4 eller senare** av **widgets** modellen, och kan också dela baserat på version **4 eller senare** av **modeller som ärver från widgeten**.

`IS_OF_MODEL` kan ta flera olika parametrar, och resten av det här avsnittet är avsedda för olika alternativ för överlagring.

Den enklaste användningen av `IS_OF_MODEL` tar bara en `twinTypeName` parameter: `IS_OF_MODEL(twinTypeName)` .
Här är ett exempel på en fråga som skickar ett värde i den här parametern:

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:example:thing;1')
```

Om du vill ange en dubbel samling att söka i när det finns mer än en (t. ex. När en `JOIN` används) lägger du till `twinCollection` parametern: `IS_OF_MODEL(twinCollection, twinTypeName)` .
Här är ett exempel på en fråga som lägger till ett värde för den här parametern:

```sql
SELECT * FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:example:thing;1')
```

Om du vill göra en exakt matchning lägger du till `exact` parametern: `IS_OF_MODEL(twinTypeName, exact)` .
Här är ett exempel på en fråga som lägger till ett värde för den här parametern:

```sql
SELECT * FROM DIGITALTWINS WHERE IS_OF_MODEL('dtmi:example:thing;1', exact)
```

Du kan också skicka alla tre argumenten tillsammans: `IS_OF_MODEL(twinCollection, twinTypeName, exact)` .
Här är ett exempel på en fråga som anger ett värde för alla tre parametrar:

```sql
SELECT ROOM FROM DIGITALTWINS DT WHERE IS_OF_MODEL(DT, 'dtmi:example:thing;1', exact)
```

## <a name="query-by-relationship"></a>Fråga efter relation

När du frågar baserat på digitala dubbla **relationer**, har Azures digitala dubbla frågespråket frågespråk en speciell syntax.

Relationerna hämtas till fråge omfånget i- `FROM` satsen. En viktig skillnad från "klassiska" SQL-typ språk är att varje uttryck i den här `FROM` satsen inte är en tabell. i stället `FROM` uttrycker satsen en relation mellan olika enheter och är skriven med en digital Azure-version av `JOIN` .

Kom ihåg att med Azure Digitals dubbla [modell](concepts-models.md) funktioner, finns det inga relationer oberoende av varandra. Det innebär att Azure Digitals-frågespråket `JOIN` är lite annorlunda än den allmänna SQL `JOIN` , eftersom relationer här inte kan frågas oberoende och måste vara knutna till ett dubbel.
För att ta med den här skillnaden `RELATED` används nyckelordet i- `JOIN` satsen för att referera till en grupp med dubbla relationer.

Följande avsnitt innehåller flera exempel på hur det ser ut.

> [!TIP]
> Den här funktionen imiterar den här funktionen de dokumentbaserade funktionerna i CosmosDB, där `JOIN` kan utföras på underordnade objekt i ett dokument. CosmosDB använder `IN` nyckelordet för att ange att `JOIN` är avsett att iterera över mat ris element i det aktuella Sammanhangs dokumentet.

### <a name="relationship-based-query-examples"></a>Exempel på Relations hip-baserade frågor

Om du vill hämta en data uppsättning som inkluderar relationer använder `FROM` du ett enda uttryck följt av N `JOIN` -instruktioner, där `JOIN` uttrycken uttrycker relationer för resultatet av en tidigare `FROM` or- `JOIN` instruktion.

Här är en exempel Relations hip-baserad fråga. Det här kodfragmentet väljer alla digitala, dubbla med *ID-* egenskapen för "ABC", och alla digitala garn som är relaterade till dessa Digitala flätar via en *contains* -relation.

```sql
SELECT T, CT
FROM DIGITALTWINS T
JOIN CT RELATED T.contains
WHERE T.$dtId = 'ABC'
```

> [!NOTE]
> Utvecklaren behöver inte korrelera detta `JOIN` med ett nyckel värde i `WHERE` -satsen (eller ange ett nyckel värde infogat med `JOIN` definitionen). Den här korrelationen beräknas automatiskt av systemet, eftersom Relations egenskaperna identifierar målentiteten.

### <a name="query-the-properties-of-a-relationship"></a>Fråga egenskaperna för en relation

På samma sätt som digitala dubbla har egenskaper som beskrivs via DTDL, kan relationer också ha egenskaper. Du kan fråga efter varandra **utifrån egenskaperna för deras relationer**.
Med Azures digitala Flätaa frågespråk kan du filtrera och projicera relationer genom att tilldela ett alias till relationen i- `JOIN` satsen.

Anta till exempel en *servicedBy* -relation som har en *reportedCondition* -egenskap. I nedanstående fråga får den här relationen ett alias för R för att referera till egenskapen.

```sql
SELECT T, SBT, R
FROM DIGITALTWINS T
JOIN SBT RELATED T.servicedBy R
WHERE T.$dtId = 'ABC'
AND R.reportedCondition = 'clean'
```

I exemplet ovan noterar du hur *reportedCondition* är en egenskap hos *servicedBy* -relationen (inte av någon digital, som har en *servicedBy* -relation).

### <a name="query-with-multiple-joins"></a>Fråga med flera kopplingar

Upp till fem `JOIN` s stöds i en enda fråga. På så sätt kan du förflytta flera nivåer av relationer samtidigt.

Här är ett exempel på en fråga med flera kopplingar, som hämtar alla lampor som finns på ljus panelerna i rum 1 och 2.

```sql
SELECT LightBulb
FROM DIGITALTWINS Room
JOIN LightPanel RELATED Room.contains
JOIN LightBulb RELATED LightPanel.contains
WHERE IS_OF_MODEL(LightPanel, 'dtmi:contoso:com:lightpanel;1')
AND IS_OF_MODEL(LightBulb, 'dtmi:contoso:com:lightbulb ;1')
AND Room.$dtId IN ['room1', 'room2']
```

## <a name="count-items"></a>Antal objekt

Du kan räkna antalet objekt i en resultat uppsättning med hjälp av- `Select COUNT` satsen:

```sql
SELECT COUNT()
FROM DIGITALTWINS
```

Lägg till en `WHERE` sats för att räkna antalet objekt som uppfyller ett visst kriterium. Här följer några exempel på hur du kan räkna med ett applicerat filter baserat på typen av dubbel modell (mer information om den här syntaxen finns i [*fråga efter modell*](#query-by-model) nedan):

```sql
SELECT COUNT()
FROM DIGITALTWINS
WHERE IS_OF_MODEL('dtmi:sample:Room;1')

SELECT COUNT()
FROM DIGITALTWINS c
WHERE IS_OF_MODEL('dtmi:sample:Room;1') AND c.Capacity > 20
```

Du kan också använda `COUNT` tillsammans med- `JOIN` satsen. Här är en fråga som räknar alla lampor som finns på de ljusa panelerna i rum 1 och 2:

```sql
SELECT COUNT()  
FROM DIGITALTWINS Room  
JOIN LightPanel RELATED Room.contains  
JOIN LightBulb RELATED LightPanel.contains  
WHERE IS_OF_MODEL(LightPanel, 'dtmi:contoso:com:lightpanel;1')  
AND IS_OF_MODEL(LightBulb, 'dtmi:contoso:com:lightbulb ;1')  
AND Room.$dtId IN ['room1', 'room2']
```

## <a name="filter-results-select-top-items"></a>Filter resultat: Markera de översta objekten

Du kan välja flera "Top"-objekt i en fråga med hjälp av- `Select TOP` satsen.

```sql
SELECT TOP (5)
FROM DIGITALTWINS
WHERE ...
```

## <a name="filter-results-specify-return-set-with-projections"></a>Filter resultat: Ange en retur uppsättning med projektioner

Genom att använda projektioner i `SELECT` instruktionen kan du välja vilka kolumner som en fråga ska returnera.

>[!NOTE]
>För tillfället stöds inte komplexa egenskaper. För att se till att projektions egenskaperna är giltiga kombinerar du projektionerna med en `IS_PRIMITIVE` bock.

Här är ett exempel på en fråga som använder projektion för att returnera dubbla och relationer. Följande fråga projekterar *konsumenten*, *fabriken* och *Edge* från ett scenario där en *fabrik* med ID: t *ABC* är relaterad till *konsumenten* via en relation av *fabriken. kunden* och relationen presenteras som en *gräns*.

```sql
SELECT Consumer, Factory, Edge
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
```

Du kan också använda projektion för att returnera en dubbels egenskap. I följande fråga visas *namn* egenskapen för de *konsumenter* som är relaterade till *fabriken* med ID: t *ABC* via en relation av *Factory. Custom*.

```sql
SELECT Consumer.name
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Consumer.name)
```

Du kan också använda projektion för att returnera en egenskap för en relation. Som i föregående exempel projekt i följande fråga projektets *namn* egenskap för de *konsumenter* som är relaterade till *fabriken* med ID: t *ABC* genom en relation mellan *fabrik. kund*; men nu returnerar den även två egenskaper för relationen, *prop1* och *prop2*. Det gör detta genom att namnge Relations *kanten* och samla in dess egenskaper.  

```sql
SELECT Consumer.name, Edge.prop1, Edge.prop2, Factory.area
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name) AND IS_PRIMITIVE(Edge.prop1) AND IS_PRIMITIVE(Edge.prop2)
```

Du kan också använda alias för att förenkla frågor med projektion.

Följande fråga utför samma åtgärder som i föregående exempel, men ger alias för egenskaps namnen till,, `consumerName` `first` `second` och `factoryArea` .

```sql
SELECT Consumer.name AS consumerName, Edge.prop1 AS first, Edge.prop2 AS second, Factory.area AS factoryArea
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name) AND IS_PRIMITIVE(Edge.prop1) AND IS_PRIMITIVE(Edge.prop2)
```

Här är en liknande fråga som frågar samma uppsättning som ovan, men projekterar endast egenskapen *Consumer.name* som `consumerName` , och projekterar hela *fabriken* som en dubbel.

```sql
SELECT Consumer.name AS consumerName, Factory
FROM DIGITALTWINS Factory
JOIN Consumer RELATED Factory.customer Edge
WHERE Factory.$dtId = 'ABC'
AND IS_PRIMITIVE(Factory.area) AND IS_PRIMITIVE(Consumer.name)
```

## <a name="build-efficient-queries-with-the-in-operator"></a>Bygg effektiva frågor med operatorn IN

Du kan avsevärt minska antalet frågor som du behöver genom att skapa en matris med dubbla och frågor med `IN` operatorn. 

Anta till exempel ett scenario där *byggnader* som innehåller *golv* och *golv* innehåller *rum*. Ett sätt att söka efter rum i en byggnad som är frekvent är att följa dessa steg.

1. Hitta golv i byggnaden baserat på `contains` relationen.

    ```sql
    SELECT Floor
    FROM DIGITALTWINS Building
    JOIN Floor RELATED Building.contains
    WHERE Building.$dtId = @buildingId
    ```

2. Om du vill hitta rum, i stället för att ta med golv en-i-ett och köra en `JOIN` fråga för att hitta rummen för var och en, kan du fråga med en samling våningar i byggnaden (med namnet *våning* i frågan nedan).

    I klient program:
    
    ```csharp
    var floors = "['floor1','floor2', ..'floorn']"; 
    ```
    
    I fråga:
    
    ```sql
    
    SELECT Room
    FROM DIGITALTWINS Floor
    JOIN Room RELATED Floor.contains
    WHERE Floor.$dtId IN ['floor1','floor2', ..'floorn']
    AND Room. Temperature > 72
    AND IS_OF_MODEL(Room, 'dtmi:com:contoso:Room;1')
    
    ```

## <a name="other-compound-query-examples"></a>Exempel på andra sammansatta frågor

Du kan **kombinera** någon av ovanstående typer av fråga med hjälp av kombinations operatorer för att inkludera mer information i en enskild fråga. Här följer några ytterligare exempel på sammansatta frågor som frågar efter fler än en typ av dubbel beskrivare på en gång.

| Beskrivning | Söka i data |
| --- | --- |
| Från de enheter som *Room 123* har kan du returnera de MxChip-enheter som hanterar rollen operatör | `SELECT device`<br>`FROM DigitalTwins space`<br>`JOIN device RELATED space.has`<br>`WHERE space.$dtid = 'Room 123'`<br>`AND device.$metadata.model = 'dtmi:contoso:com:DigitalTwins:MxChip:3'`<br>`AND has.role = 'Operator'` |
| Hämta dubbla som har en relation som heter *innehåller* med en annan som har ID: t *id1* | `SELECT Room`<br>`FROM DIGITALTWINS Room`<br>`JOIN Thermostat RELATED Room.Contains`<br>`WHERE Thermostat.$dtId = 'id1'` |
| Hämta alla rum för den här rums modellen som finns i *floor11* | `SELECT Room`<br>`FROM DIGITALTWINS Floor`<br>`JOIN Room RELATED Floor.Contains`<br>`WHERE Floor.$dtId = 'floor11'`<br>`AND IS_OF_MODEL(Room, 'dtmi:contoso:com:DigitalTwins:Room;1')` |

## <a name="run-queries-with-the-api"></a>Köra frågor med API: et

När du har bestämt dig för en frågesträng kör du den genom att anropa API: et för [**frågor**](/rest/api/digital-twins/dataplane/query).

Du kan anropa API: et direkt eller använda någon av [SDK: erna](how-to-use-apis-sdks.md#overview-data-plane-apis) som är tillgängliga för Azure Digitals dubbla.

Följande kodfragment illustrerar [.net (C#) SDK-](/dotnet/api/overview/azure/digitaltwins/client?view=azure-dotnet&preserve-view=true) anropet från en klient app:

```csharp
    string adtInstanceEndpoint = "https://<your-instance-hostname>";

    var credential = new DefaultAzureCredential();
    DigitalTwinsClient client = new DigitalTwinsClient(new Uri(adtInstanceEndpoint), credential);

    // Run a query for all twins   
    string query = "SELECT * FROM DIGITALTWINS";
    AsyncPageable<BasicDigitalTwin> result = client.QueryAsync<BasicDigitalTwin>(query);
```

Anropet returnerar frågeresultat i form av ett [BasicDigitalTwin](/dotnet/api/azure.digitaltwins.core.basicdigitaltwin?view=azure-dotnet&preserve-view=true) -objekt.

Fråge anrop stöder sid indelning. Här är ett fullständigt exempel som använder sig av `BasicDigitalTwin` typen frågeresultat med fel hantering och växling:

```csharp
try
{
    await foreach(BasicDigitalTwin twin in result)
        {
            // You can include your own logic to print the result
            // The logic below prints the twin's ID and contents
            Console.WriteLine($"Twin ID: {twin.Id} \nTwin data");
            IDictionary<string, object> contents = twin.Contents;
            foreach (KeyValuePair<string, object> kvp in contents)
            {
                Console.WriteLine($"{kvp.Key}  {kvp.Value}");
            }
        }
}
catch (RequestFailedException e)
{
    Console.WriteLine($"Error {e.Status}: {e.Message}");
    throw;
}
```

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [Azure Digitals dubbla API: er och SDK: er](how-to-use-apis-sdks.md), inklusive fråge-API som används för att köra frågorna från den här artikeln.
