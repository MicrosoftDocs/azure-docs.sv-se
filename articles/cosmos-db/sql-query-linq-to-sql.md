---
title: LINQ to SQL översättning i Azure Cosmos DB
description: Läs om de LINQ-operatörer som stöds och hur LINQ-frågorna mappas till SQL-frågor i Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: tisande
ms.openlocfilehash: 122c95fe9ac017ad7a6957dcdb8323837be34f21
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96545391"
---
# <a name="linq-to-sql-translation"></a>LINQ to SQL-översättning
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB Query-providern utför en mappning från en LINQ-fråga till en Cosmos DB SQL-fråga. Om du vill hämta SQL-frågan som har översatts från LINQ använder du `ToString()` metoden på det genererade `IQueryable` objektet. Följande beskrivning förutsätter en grundläggande kunskap om [LINQ](/dotnet/csharp/programming-guide/concepts/linq/introduction-to-linq-queries). Förutom LINQ stöder Azure Cosmos DB också [Entity Framework Core](/ef/core/providers/cosmos/?tabs=dotnet-core-cli) som fungerar med SQL API.

Typ systemet för fråged provider stöder endast JSON primitiva typer: numeric, Boolean, String och null.

Frågans provider stöder följande skalära uttryck:

- Konstanta värden, inklusive konstanta värden för primitiva data typer vid frågans utvärderings tid.
  
- Index uttryck för egenskap/mat ris som refererar till egenskapen för ett objekt eller ett mat ris element. Exempel:
  
  ```
    family.Id;
    family.children[0].familyName;
    family.children[0].grade;
    family.children[n].grade; //n is an int variable
  ```
  
- Aritmetiska uttryck, inklusive vanliga aritmetiska uttryck för numeriska och booleska värden. En fullständig lista finns i [Azure Cosmos DB SQL-specifikationen](sql-query-aggregate-functions.md).
  
  ```
    2 * family.children[0].grade;
    x + y;
  ```
  
- Sträng jämförelse uttryck, som inkluderar jämförelse av ett sträng värde till ett konstant sträng värde.  
  
  ```
    mother.familyName == "Wakefield";
    child.givenName == s; //s is a string variable
  ```
  
- Uttryck för att skapa objekt/matris, som returnerar ett objekt av sammansatt värde typ eller anonym typ, eller en matris med sådana objekt. Du kan kapsla dessa värden.
  
  ```
    new Parent { familyName = "Wakefield", givenName = "Robin" };
    new { first = 1, second = 2 }; //an anonymous type with two fields  
    new int[] { 3, child.grade, 5 };
  ```

## <a name="using-linq"></a>Använda LINQ

Du kan skapa en LINQ-fråga med `GetItemLinqQueryable` . Det här exemplet visar generering av LINQ-frågor och asynkron körning med en `FeedIterator` :

```csharp
using (FeedIterator<Book> setIterator = container.GetItemLinqQueryable<Book>()
                      .Where(b => b.Title == "War and Peace")
                      .ToFeedIterator<Book>())
 {
     //Asynchronous query execution
     while (setIterator.HasMoreResults)
     {
         foreach(var item in await setIterator.ReadNextAsync()){
         {
             Console.WriteLine(item.cost);
         }
       }
     }
 }
```

## <a name="supported-linq-operators"></a><a id="SupportedLinqOperators"></a>LINQ-operatorer som stöds

LINQ-providern som ingår i SQL .NET SDK stöder följande operatorer:

- **Välj**: projektioner översätts till [Välj](sql-query-select.md), inklusive objekt konstruktion.
- **Där**: filter översätter till [WHERE](sql-query-where.md), och stöder översättning mellan `&&` , `||` , och `!` till SQL-operatörerna
- **SelectMany**: tillåter avlindning av matriser till [Join](sql-query-join.md) -satsen. Används för att kedja eller kapsla uttryck för att filtrera på mat ris element.
- **OrderBy** och **OrderByDescending**: Översätt till [order by](sql-query-order-by.md) med ASC eller DESC.
- **Count**-, **Sum**-, **min**-, **Max**-och **genomsnitts** operatorer för [agg regering](sql-query-aggregate-functions.md)och deras async-motsvarigheter **CountAsync**, **SumAsync**, **MinAsync**, **MaxAsync** och **AverageAsync**.
- **CompareTo**: Översätts till intervalljämförelser. Används ofta för strängar, eftersom de inte är jämförbara i .NET.
- **Hoppa över** och **vidta**: översätts till [förskjutning och gräns](sql-query-offset-limit.md) för begränsning av resultat från en fråga och sid brytning.
- **Matematiska funktioner**: stöder översättning från .net,,,,,, `Abs` `Acos` `Asin` `Atan` `Ceiling` `Cos` `Exp` , `Floor` , `Log` , `Log10` , `Pow` , `Round` , `Sign` , `Sin` , `Sqrt` , `Tan` , och `Truncate` motsvarande [inbyggda matematiska funktioner](sql-query-mathematical-functions.md).
- **Sträng funktioner**: stöder översättning från .net `Concat` , `Contains` ,,,,, `Count` `EndsWith` `IndexOf` `Replace` `Reverse` , `StartsWith` , `SubString` , `ToLower` , `ToUpper` , `TrimEnd` , och `TrimStart` till motsvarande [inbyggda sträng funktioner](sql-query-string-functions.md).
- **Mat ris funktioner**: stöder översättning från .net `Concat` , `Contains` och `Count` till motsvarande [inbyggda mat ris funktioner](sql-query-array-functions.md).
- **Geospatiala utöknings funktioner**: stöder översättning från stub `Distance` -metoder, `IsValid` ,, `IsValidDetailed` och `Within` till motsvarande [inbyggda geospatiala funktioner](sql-query-geospatial-query.md).
- **Användardefinierad funktions utöknings funktion**: stöder översättning från stub-metoden `UserDefinedFunctionProvider.Invoke` till motsvarande [användardefinierade funktion](sql-query-udfs.md).
- **Diverse**: stöder översättning av `Coalesce` och villkorliga [operatorer](sql-query-operators.md). Kan översättas `Contains` till sträng innehåller, ARRAY_CONTAINS eller i, beroende på kontext.

## <a name="examples"></a>Exempel

I följande exempel visas hur några av de standardiserade LINQ Query-operatörerna översätts till frågor i Azure Cosmos DB.

### <a name="select-operator"></a>Välj operator

Syntaxen är `input.Select(x => f(x))`, där `f` är ett skalärt uttryck. `input`I det här fallet är det ett `IQueryable` objekt.

**Välj operatör, exempel 1:**

- **LINQ-lambdauttryck**
  
  ```csharp
      input.Select(family => family.parents[0].familyName);
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
    ```
  
**Välj Operator, exempel 2:**

- **LINQ-lambdauttryck**
  
  ```csharp
      input.Select(family => family.children[0].grade + c); // c is an int variable
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE f.children[0].grade + c
      FROM Families f
  ```
  
**Välj Operator, exempel 3:**

- **LINQ-lambdauttryck**
  
  ```csharp
    input.Select(family => new
    {
        name = family.children[0].familyName,
        grade = family.children[0].grade + 3
    });
  ```
  
- **SQL**
  
  ```sql
      SELECT VALUE {"name":f.children[0].familyName,
                    "grade": f.children[0].grade + 3 }
      FROM Families f
  ```

### <a name="selectmany-operator"></a>Operatorn SelectMany

Syntaxen är `input.SelectMany(x => f(x))`, där `f` är ett skalärt uttryck som returnerar en containertyp.

- **LINQ-lambdauttryck**
  
  ```csharp
      input.SelectMany(family => family.children);
  ```
  
- **SQL**

  ```sql
      SELECT VALUE child
      FROM child IN Families.children
  ```

### <a name="where-operator"></a>Operatorn Where

Syntaxen är `input.Where(x => f(x))`, där `f` är ett skalärt uttryck som returnerar ett booleskt värde.

**Where-Operator, exempel 1:**

- **LINQ-lambdauttryck**
  
  ```csharp
      input.Where(family=> family.parents[0].familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```
  
**Where-Operator, exempel 2:**

- **LINQ-lambdauttryck**
  
  ```csharp
      input.Where(
          family => family.parents[0].familyName == "Wakefield" &&
          family.children[0].grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
      AND f.children[0].grade < 3
  ```

## <a name="composite-sql-queries"></a>Sammansatta SQL-frågor

Du kan skapa föregående operatorer för att bilda mer kraftfulla frågor. Eftersom Cosmos DB stöder kapslade behållare kan du sammanfoga eller kapsla sammansättningen.

### <a name="concatenation"></a>Sammanlänkning

Syntax: `input(.|.SelectMany())(.Select()|.Where())*`. En sammanfogad fråga kan börja med en valfri `SelectMany` fråga, följt av flera `Select` eller- `Where` operatorer.

**Sammanfogning, exempel 1:**

- **LINQ-lambdauttryck**
  
  ```csharp
      input.Select(family => family.parents[0])
          .Where(parent => parent.familyName == "Wakefield");
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE f.parents[0].familyName = "Wakefield"
  ```

**Sammanfogning, exempel 2:**

- **LINQ-lambdauttryck**
  
  ```csharp
      input.Where(family => family.children[0].grade > 3)
          .Select(family => family.parents[0].familyName);
  ```

- **SQL**
  
  ```sql
      SELECT VALUE f.parents[0].familyName
      FROM Families f
      WHERE f.children[0].grade > 3
  ```

**Sammanfogning, exempel 3:**

- **LINQ-lambdauttryck**
  
  ```csharp
      input.Select(family => new { grade=family.children[0].grade}).
          Where(anon=> anon.grade < 3);
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      WHERE ({grade: f.children[0].grade}.grade > 3)
  ```

**Sammanfogning, exempel 4:**

- **LINQ-lambdauttryck**
  
  ```csharp
      input.SelectMany(family => family.parents)
          .Where(parent => parents.familyName == "Wakefield");
  ```
  
- **SQL**
  
  ```sql
      SELECT *
      FROM p IN Families.parents
      WHERE p.familyName = "Wakefield"
  ```

### <a name="nesting"></a>Kapsling

Syntaxen är `input.SelectMany(x=>x.Q())` där `Q` är en `Select` , `SelectMany` -eller- `Where` operator.

En kapslad fråga använder den inre frågan för varje element i den yttre behållaren. En viktig funktion är att den inre frågan kan referera till fälten för elementen i den yttre behållaren, t. ex. en själv koppling.

**Kapsling, exempel 1:**

- **LINQ-lambdauttryck**
  
  ```csharp
      input.SelectMany(family=>
          family.parents.Select(p => p.familyName));
  ```

- **SQL**
  
  ```sql
      SELECT VALUE p.familyName
      FROM Families f
      JOIN p IN f.parents
  ```

**Kapsling, exempel 2:**

- **LINQ-lambdauttryck**
  
  ```csharp
      input.SelectMany(family =>
          family.children.Where(child => child.familyName == "Jeff"));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = "Jeff"
  ```

**Kapsling, exempel 3:**

- **LINQ-lambdauttryck**
  
  ```csharp
      input.SelectMany(family => family.children.Where(
          child => child.familyName == family.parents[0].familyName));
  ```

- **SQL**
  
  ```sql
      SELECT *
      FROM Families f
      JOIN c IN f.children
      WHERE c.familyName = f.parents[0].familyName
  ```

## <a name="next-steps"></a>Nästa steg

- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Modelldokumentdata](modeling-data.md)