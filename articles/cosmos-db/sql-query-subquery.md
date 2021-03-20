---
title: SQL-underfrågor för Azure Cosmos DB
description: Lär dig om SQL-underfrågaer och deras vanliga användnings fall och olika typer av under frågor i Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: tisande
ms.openlocfilehash: f5f209229d17a2587258d21ee90e7560e629d082
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93340863"
---
# <a name="sql-subquery-examples-for-azure-cosmos-db"></a>Exempel på SQL-underfrågan för Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

En under fråga är en fråga som är kapslad i en annan fråga. En under fråga kallas även för en inre fråga eller inre Select. Den instruktion som innehåller en under fråga kallas vanligt vis för en yttre fråga.

I den här artikeln beskrivs SQL-underfrågor och deras vanliga användnings fall i Azure Cosmos DB. Alla exempel frågor i det här dokumentet kan köras mot en näringsvärdes data uppsättning som är förinstallerad på [Azure Cosmos DB testplats för databasfrågor](https://www.documentdb.com/sql/demo).

## <a name="types-of-subqueries"></a>Typer av under frågor

Det finns två huvud typer av under frågor:

* **Korrelerade**: en under fråga som refererar till värden från den yttre frågan. Under frågan utvärderas en gång för varje rad som den yttre frågan bearbetar.
* **Icke-korrelerad**: en under fråga som är oberoende av den yttre frågan. Den kan köras på egen hand utan att det förlitar sig på den yttre frågan.

> [!NOTE]
> Azure Cosmos DB stöder endast korrelerade under frågor.

Under frågor kan klassificeras ytterligare baserat på antalet rader och kolumner som de returnerar. Det finns tre typer:
* **Tabell**: returnerar flera rader och flera kolumner.
* **Flera värden**: returnerar flera rader och en kolumn.
* **Skalär**: returnerar en enskild rad och en enskild kolumn.

SQL-frågor i Azure Cosmos DB returnerar alltid en enda kolumn (antingen ett enkelt värde eller ett komplext dokument). Därför kan bara flera värden och skalära under frågor användas i Azure Cosmos DB. Du kan bara använda en under fråga med flera värden i FROM-satsen som ett Relations uttryck. Du kan använda en skalär under fråga som ett skalärt uttryck i SELECT-eller WHERE-satsen eller som ett Relations uttryck i from-satsen.

## <a name="multi-value-subqueries"></a>Under frågor med flera värden

Under frågor med flera värden returnerar en uppsättning dokument och används alltid i FROM-satsen. De används för:

* Optimerar KOPPLINGs uttryck. 
* Utvärderar dyra uttryck en gång och refererar flera gånger.

## <a name="optimize-join-expressions"></a>Optimera KOPPLINGs uttryck

Under frågor med flera värden kan optimera KOPPLINGs uttryck genom att push-överföra predikat efter varje Select-many-uttryck i stället för efter alla kors kopplingar i WHERE-satsen.

Överväg följande fråga:

```sql
SELECT Count(1) AS Count
FROM c
JOIN t IN c.tags
JOIN n IN c.nutrients
JOIN s IN c.servings
WHERE t.name = 'infant formula' AND (n.nutritionValue > 0 
AND n.nutritionValue < 10) AND s.amount > 1
```

I den här frågan matchar indexet alla dokument som har en tagg med namnet "mode/mode-formel". Det är ett näringsämnes objekt med ett värde mellan 0 och 10, och ett betjänande objekt med ett större belopp än 1. KOPPLINGs uttrycket här utför kors produkten av alla objekt i taggar, näringsämnen och betjänar matriser för varje matchande dokument innan något filter tillämpas. 

WHERE-satsen använder sedan filtrets predikat på varje <c, t, n, s> tupel. Om ett matchande dokument exempelvis hade 10 objekt i var och en av de tre matriserna, expanderas det till 1 x 10 x 10 x 10 (d.v.s. 1 000) tupler. Om du använder under frågor här kan du filtrera ut sammanfogade mat ris objekt innan du ansluter till nästa uttryck.

Den här frågan motsvarar den tidigare en men använder under frågor:

```sql
SELECT Count(1) AS Count
FROM c
JOIN (SELECT VALUE t FROM t IN c.tags WHERE t.name = 'infant formula')
JOIN (SELECT VALUE n FROM n IN c.nutrients WHERE n.nutritionValue > 0 AND n.nutritionValue < 10)
JOIN (SELECT VALUE s FROM s IN c.servings WHERE s.amount > 1)
```

Anta att endast ett objekt i matrisen taggar matchar filtret och det finns fem objekt för båda näringsämnena och som betjänar matriser. KOPPLINGs uttrycken utökas sedan till 1 x 1 x 5 x 5 = 25 objekt, i stället för 1 000 objekt i den första frågan.

## <a name="evaluate-once-and-reference-many-times"></a>Utvärdera en gång och referera flera gånger

Under frågor kan hjälpa till att optimera frågor med dyra uttryck, till exempel användardefinierade funktioner (UDF: er), komplexa strängar eller aritmetiska uttryck. Du kan använda en under fråga tillsammans med ett JOIN-uttryck för att utvärdera uttrycket en gång, men referera till det flera gånger.

Följande fråga kör UDF `GetMaxNutritionValue` två gånger:

```sql
SELECT c.id, udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue
FROM c
WHERE udf.GetMaxNutritionValue(c.nutrients) > 100
```

Här är en motsvarande fråga som bara kör UDF-filen en gång:

```sql
SELECT TOP 1000 c.id, MaxNutritionValue
FROM c
JOIN (SELECT VALUE udf.GetMaxNutritionValue(c.nutrients)) MaxNutritionValue
WHERE MaxNutritionValue > 100
``` 

> [!NOTE] 
> Tänk på kors produkt beteendet för KOPPLINGs uttryck. Om UDF-uttrycket kan utvärderas som odefinierat, bör du se till att JOIN-uttrycket alltid genererar en enda rad genom att returnera ett objekt från under frågan i stället för värdet direkt.
>

Här är ett liknande exempel som returnerar ett objekt i stället för ett värde:

```sql
SELECT TOP 1000 c.id, m.MaxNutritionValue
FROM c
JOIN (SELECT udf.GetMaxNutritionValue(c.nutrients) AS MaxNutritionValue) m
WHERE m.MaxNutritionValue > 100
```

Metoden är inte begränsad till UDF: er. Den gäller för ett potentiellt kostsamt uttryck. Du kan till exempel ta samma metod med den matematiska funktionen `avg` :

```sql
SELECT TOP 1000 c.id, AvgNutritionValue
FROM c
JOIN (SELECT VALUE avg(n.nutritionValue) FROM n IN c.nutrients) AvgNutritionValue
WHERE AvgNutritionValue > 80
```

## <a name="mimic-join-with-external-reference-data"></a>Härma koppling med externa referens data

Du kanske ofta behöver referera till statiska data som sällan ändras, till exempel mått enheter eller lands koder. Det är bättre att inte duplicera sådana data för varje dokument. Att undvika den här dupliceringen sparar på lagring och förbättrar Skriv prestandan genom att minska storleken på dokumentet. Du kan använda en under fråga för att efterlikna inre kopplings-semantik med en samling referens data.

Överväg till exempel den här uppsättningen referens data:

| **Enhet** | **Namn**            | **Multiplikatorn** | **Bas enhet** |
| -------- | ------------------- | -------------- | ------------- |
| naturgas       | Nanogram            | 1,00 e-09       | Grammatikkontroll          |
| ìg       | Microgram           | 1,00 e-06       | Grammatikkontroll          |
| MB       | Mg           | 1,00 e-03       | Grammatikkontroll          |
| g        | Grammatikkontroll                | 1,00 e + 00       | Grammatikkontroll          |
| kg       | Kilo            | 1,00 e + 03       | Grammatikkontroll          |
| MB       | Megagram            | 1,00 e + 06       | Grammatikkontroll          |
| Gg       | Gigagram            | 1,00 e + 09       | Grammatikkontroll          |
| nJ       | Nanojoule           | 1,00 e-09       | Joule         |
| µJ       | Microjoule          | 1,00 e-06       | Joule         |
| mJ       | Millijoule          | 1,00 e-03       | Joule         |
| J        | Joule               | 1,00 e + 00       | Joule         |
| kJ       | Kilojoule           | 1,00 e + 03       | Joule         |
| MJ       | Megajoule           | 1,00 e + 06       | Joule         |
| GJ       | Gigajoule           | 1,00 e + 09       | Joule         |
| installera      | Calorie             | 1,00 e + 00       | calorie       |
| kcal     | Calorie             | 1,00 e + 03       | calorie       |
| AGGLUTINATIONSENHETER       | Internationella enheter |                |               |


Följande fråga imiterar anslutning till dessa data så att du lägger till namnet på enheten i utdata:

```sql
SELECT TOP 10 n.id, n.description, n.nutritionValue, n.units, r.name
FROM food
JOIN n IN food.nutrients
JOIN r IN (
    SELECT VALUE [
        {unit: 'ng', name: 'nanogram', multiplier: 0.000000001, baseUnit: 'gram'},
        {unit: 'µg', name: 'microgram', multiplier: 0.000001, baseUnit: 'gram'},
        {unit: 'mg', name: 'milligram', multiplier: 0.001, baseUnit: 'gram'},
        {unit: 'g', name: 'gram', multiplier: 1, baseUnit: 'gram'},
        {unit: 'kg', name: 'kilogram', multiplier: 1000, baseUnit: 'gram'},
        {unit: 'Mg', name: 'megagram', multiplier: 1000000, baseUnit: 'gram'},
        {unit: 'Gg', name: 'gigagram', multiplier: 1000000000, baseUnit: 'gram'},
        {unit: 'nJ', name: 'nanojoule', multiplier: 0.000000001, baseUnit: 'joule'},
        {unit: 'µJ', name: 'microjoule', multiplier: 0.000001, baseUnit: 'joule'},
        {unit: 'mJ', name: 'millijoule', multiplier: 0.001, baseUnit: 'joule'},
        {unit: 'J', name: 'joule', multiplier: 1, baseUnit: 'joule'},
        {unit: 'kJ', name: 'kilojoule', multiplier: 1000, baseUnit: 'joule'},
        {unit: 'MJ', name: 'megajoule', multiplier: 1000000, baseUnit: 'joule'},
        {unit: 'GJ', name: 'gigajoule', multiplier: 1000000000, baseUnit: 'joule'},
        {unit: 'cal', name: 'calorie', multiplier: 1, baseUnit: 'calorie'},
        {unit: 'kcal', name: 'Calorie', multiplier: 1000, baseUnit: 'calorie'},
        {unit: 'IU', name: 'International units'}
    ]
)
WHERE n.units = r.unit
```

## <a name="scalar-subqueries"></a>Skalära under frågor

Ett skalärt under frågeuttryck är en under fråga som utvärderar till ett enda värde. Värdet för det skalära under frågans uttryck är värdet för under frågans projektion (SELECT-sats).  Du kan använda ett uttryck för en skalär under fråga på många platser där ett skalärt uttryck är giltigt. Du kan till exempel använda en skalär under fråga i alla uttryck i både SELECT-och WHERE-satserna.

Att använda en skalär under fråga bidrar inte alltid till att optimera, trots. Om du till exempel skickar en skalär under fråga som ett argument till antingen en systemdefinierad funktion eller en användardefinierad funktion ger du ingen nytta av förbrukningen eller svars tiden för resurs enheten (RU).

Skalära under frågor kan klassificeras ytterligare som:
* Skalära under frågor med enkla uttryck
* Aggregerade skalära under frågor

## <a name="simple-expression-scalar-subqueries"></a>Skalära under frågor med enkla uttryck

En skalär under fråga med enkla uttryck är en korrelerad under fråga som har en SELECT-sats som inte innehåller några mängd uttryck. Dessa under frågor ger inga optimerings fördelar eftersom kompilatorn konverterar dem till ett större enkelt uttryck. Det finns ingen korrelerad kontext mellan inre och yttre frågor.

Här följer några exempel:

**Exempel 1**

```sql
SELECT 1 AS a, 2 AS b
```

Du kan skriva om den här frågan genom att använda en skalär, enkel uttryck, för att:

```sql
SELECT (SELECT VALUE 1) AS a, (SELECT VALUE 2) AS b
```

Båda frågorna ger följande utdata:

```json
[
  { "a": 1, "b": 2 }
]
```

**Exempel 2**

```sql
SELECT TOP 5 Concat('id_', f.id) AS id
FROM food f
```

Du kan skriva om den här frågan genom att använda en skalär, enkel uttryck, för att:

```sql
SELECT TOP 5 (SELECT VALUE Concat('id_', f.id)) AS id
FROM food f
```

Frågans utdata:

```json
[
  { "id": "id_03226" },
  { "id": "id_03227" },
  { "id": "id_03228" },
  { "id": "id_03229" },
  { "id": "id_03230" }
]
```

**Exempel 3**

```sql
SELECT TOP 5 f.id, Contains(f.description, 'fruit') = true ? f.description : undefined
FROM food f
```

Du kan skriva om den här frågan genom att använda en skalär, enkel uttryck, för att:

```sql
SELECT TOP 10 f.id, (SELECT f.description WHERE Contains(f.description, 'fruit')).description
FROM food f
```

Frågans utdata:

```json
[
  { "id": "03230" },
  { "id": "03238", "description":"Babyfood, dessert, tropical fruit, junior" },
  { "id": "03229" },
  { "id": "03226", "description":"Babyfood, dessert, fruit pudding, orange, strained" },
  { "id": "03227" }
]
```

### <a name="aggregate-scalar-subqueries"></a>Aggregerade skalära under frågor

En sammansatt skalär under fråga är en under fråga som har en mängd funktion i projektionen eller filtret som utvärderas till ett enda värde.

**Exempel 1:**

Här är en under fråga med ett enda mängd funktions uttryck i projektionen:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg'
) AS count_mg
FROM food f
```

Frågans utdata:

```json
[
  { "id": "03230", "count_mg": 13 },
  { "id": "03238", "count_mg": 14 },
  { "id": "03229", "count_mg": 13 },
  { "id": "03226", "count_mg": 15 },
  { "id": "03227", "count_mg": 19 }
]
```

**Exempel 2**

Här är en under fråga med flera mängd funktions uttryck:

```sql
SELECT TOP 5 f.id, (
    SELECT Count(1) AS count, Sum(n.nutritionValue) AS sum 
    FROM n IN f.nutrients 
    WHERE n.units = 'mg'
) AS unit_mg
FROM food f
```

Frågans utdata:

```json
[
  { "id": "03230","unit_mg": { "count": 13,"sum": 147.072 } },
  { "id": "03238","unit_mg": { "count": 14,"sum": 107.385 } },
  { "id": "03229","unit_mg": { "count": 13,"sum": 141.579 } },
  { "id": "03226","unit_mg": { "count": 15,"sum": 183.91399999999996 } },
  { "id": "03227","unit_mg": { "count": 19,"sum": 94.788999999999987 } }
]
```

**Exempel 3**

Här är en fråga med en sammanställd under fråga i både projektion och filter:

```sql
SELECT TOP 5 
    f.id, 
    (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
FROM food f
WHERE (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') > 20
```

Frågans utdata:

```json
[
  { "id": "03235", "count_mg": 27 },
  { "id": "03246", "count_mg": 21 },
  { "id": "03267", "count_mg": 21 },
  { "id": "03269", "count_mg": 21 },
  { "id": "03274", "count_mg": 21 }
]
```

Ett mer optimalt sätt att skriva den här frågan är att ansluta till under frågan och referera till under frågans alias i både SELECT-och WHERE-satserna. Den här frågan är mer effektiv eftersom du bara behöver köra under frågan i Join-instruktionen och inte i både projektion och filter.

```sql
SELECT TOP 5 f.id, count_mg
FROM food f
JOIN (SELECT VALUE Count(1) FROM n IN f.nutrients WHERE n.units = 'mg') AS count_mg
WHERE count_mg > 20
```

## <a name="exists-expression"></a>EXISTS-uttryck

Azure Cosmos DB stöder EXISTS-uttryck. Det här är en sammanställd skalär under fråga som är inbyggd i Azure Cosmos DB SQL API. EXISTS är ett booleskt uttryck som använder uttryck för under frågor och returnerar true om under frågan returnerar några rader. Annars returnerar den false.

Eftersom Azure Cosmos DB SQL API skiljer sig mellan booleska uttryck och andra skalära uttryck, kan du använda finns i både SELECT-och WHERE-satser. Detta skiljer sig från T-SQL, där ett booleskt uttryck (till exempel finns, mellan och IN) är begränsat till filtret.

Om under frågan EXISTS returnerar ett enskilt värde som är odefinierat, utvärderas värdet FALSKT. Överväg till exempel följande fråga som utvärderar till falskt:
```sql
SELECT EXISTS (SELECT VALUE undefined)
```   


Om nyckelordet VALUE i föregående under fråga utelämnas, kommer frågan att utvärderas till sant:
```sql
SELECT EXISTS (SELECT undefined) 
```

Under frågan omger listan med värden i den markerade listan i ett objekt. Om den markerade listan inte har några värden returnerar under frågan det enskilda värdet {} . Det här värdet är definierat, så det finns utvärderas till sant.

### <a name="example-rewriting-array_contains-and-join-as-exists"></a>Exempel: skriva om ARRAY_CONTAINS och delta som det finns

Ett vanligt användnings fall av ARRAY_CONTAINS är att filtrera ett dokument genom att finnas ett objekt i en matris. I det här fallet kontrollerar vi om taggarna innehåller ett objekt med namnet "orange".

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE ARRAY_CONTAINS(f.tags, {name: 'orange'})
```

Du kan skriva om samma fråga som ska användas:

```sql
SELECT TOP 5 f.id, f.tags
FROM food f
WHERE EXISTS(SELECT VALUE t FROM t IN f.tags WHERE t.name = 'orange')
```

Dessutom kan ARRAY_CONTAINS bara kontrol lera om ett värde är lika med ett element i en matris. Om du behöver mer komplexa filter på mat ris egenskaper använder du JOIN.

Tänk på följande fråga som filtrerar baserat på enheter och `nutritionValue` Egenskaper i matrisen: 

```sql
SELECT VALUE c.description
FROM c
JOIN n IN c.nutrients
WHERE n.units= "mg" AND n.nutritionValue > 0
```

För varje dokument i samlingen utförs en kors produkt med dess mat ris element. Den här KOPPLINGs åtgärden gör det möjligt att filtrera efter egenskaper i matrisen. Den här frågans RU-förbrukning är dock viktig. Om till exempel 1 000 dokument hade 100 objekt i varje matris, expanderas det till 1 000 x 100 (dvs. 100 000) tupler.

Att använda finns kan hjälpa till att undvika den dyra kors produkten:

```sql
SELECT VALUE c.description
FROM c
WHERE EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0
)
```

I det här fallet filtrerar du på mat ris element i under frågan som finns. Om ett mat ris element matchar filtret, så projicera det och det finns utvärderas till sant.

Du kan också ha ett alias som finns och referera till det i projektionen:

```sql
SELECT TOP 1 c.description, EXISTS(
    SELECT VALUE n
    FROM n IN c.nutrients
    WHERE n.units = "mg" AND n.nutritionValue > 0) as a
FROM c
```

Frågans utdata:

```json
[
    {
        "description": "Babyfood, dessert, fruit pudding, orange, strained",
        "a": true
    }
]
```

## <a name="array-expression"></a>MAT ris uttryck

Du kan använda mat ris uttrycket för att projicera resultatet av en fråga som en matris. Du kan bara använda det här uttrycket inom frågans SELECT-sats.

```sql
SELECT TOP 1   f.id, ARRAY(SELECT VALUE t.name FROM t in f.tags) AS tagNames
FROM  food f
```

Frågans utdata:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

Precis som med andra under frågor är filter med mat ris uttryck möjliga.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t FROM t in c.tags WHERE t.name != 'infant formula') AS tagNames
FROM c
```

Frågans utdata:

```json
[
    {
        "id": "03226",
        "tagNames": [
            {
                "name": "babyfood"
            },
            {
                "name": "dessert"
            },
            {
                "name": "fruit pudding"
            },
            {
                "name": "orange"
            },
            {
                "name": "strained"
            }
        ]
    }
]
```

Mat ris uttryck kan också komma efter FROM-satsen i under frågor.

```sql
SELECT TOP 1 c.id, ARRAY(SELECT VALUE t.name FROM t in c.tags) as tagNames
FROM c
JOIN n IN (SELECT VALUE ARRAY(SELECT t FROM t in c.tags WHERE t.name != 'infant formula'))
```

Frågans utdata:

```json
[
    {
        "id": "03238",
        "tagNames": [
            "babyfood",
            "dessert",
            "tropical fruit",
            "junior"
        ]
    }
]
```

## <a name="next-steps"></a>Nästa steg

- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Modelldokumentdata](modeling-data.md)
