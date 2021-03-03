---
title: SQL-nyckelord för Azure Cosmos DB
description: Läs om SQL-nyckelord för Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 01/20/2021
ms.author: tisande
ms.openlocfilehash: 4711401172c4a2b224c231db4a773e0c4219d3a7
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101659842"
---
# <a name="keywords-in-azure-cosmos-db"></a>Nyckelord i Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Den här artikeln innehåller information om nyckelord som kan användas i Azure Cosmos DB SQL-frågor.

## <a name="between"></a>BETWEEN

Du kan använda `BETWEEN` nyckelordet för att uttrycka frågor mot intervall av sträng eller numeriska värden. Följande fråga returnerar till exempel alla objekt där de första underordnade klasserna är 1-5.

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade BETWEEN 1 AND 5
```

Du kan också använda `BETWEEN` nyckelordet i `SELECT` -satsen, som i följande exempel.

```sql
    SELECT (c.grade BETWEEN 0 AND 10)
    FROM Families.children[0] c
```

I SQL API, till skillnad från ANSI SQL, kan du uttrycka intervall frågor mot egenskaper av blandade typer. Kan till exempel `grade` vara ett tal som `5` i vissa objekt och en sträng som `grade4` i andra. I dessa fall, som i Java Script, resulterar jämförelsen mellan de två olika typerna i `Undefined` , så objektet hoppas över.

## <a name="distinct"></a>DISTINKTA

`DISTINCT`Nyckelordet eliminerar dubbletter i frågans projektion.

I det här exemplet är fråga projekt värden för varje efter namn:

```sql
SELECT DISTINCT VALUE f.lastName
FROM Families f
```

Resultatet är:

```json
[
    "Andersen"
]
```

Du kan också projicera unika objekt. I det här fallet finns inte fältet lastName i något av de två dokumenten, så frågan returnerar ett tomt objekt.

```sql
SELECT DISTINCT f.lastName
FROM Families f
```

Resultatet är:

```json
[
    {
        "lastName": "Andersen"
    },
    {}
]
```

`DISTINCT` kan också användas i projektionen inom en under fråga:

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

Den här frågan projekterar en matris som innehåller varje barns givenName och dubbletter tas bort. Matrisen har ett alias som ChildNames och projiceras i den yttre frågan.

Resultatet är:

```json
[
    {
        "id": "AndersenFamily",
        "ChildNames": []
    },
    {
        "id": "WakefieldFamily",
        "ChildNames": [
            "Jesse",
            "Lisa"
        ]
    }
]
```

Frågor med en sammansatt systemfunktion och en under fråga med `DISTINCT` stöds inte. Följande fråga stöds exempelvis inte:

```sql
SELECT COUNT(1) FROM (SELECT DISTINCT f.lastName FROM f)
```

## <a name="like"></a>LIKADAN

Returnerar ett booleskt värde beroende på om en specifik tecken sträng matchar ett angivet mönster. Ett mönster kan innehålla vanliga tecken och jokertecken. Du kan skriva logiskt motsvarande frågor med hjälp av antingen `LIKE` nyckelordet eller funktionen [RegexMatch](sql-query-regexmatch.md) system. Du kommer att observera samma index användning oavsett vilken du väljer. Därför bör du använda `LIKE` om du föredrar syntaxen mer än reguljära uttryck.

> [!NOTE]
> Eftersom `LIKE` kan använda ett index, bör du [skapa ett intervall index](./index-policy.md) för egenskaper som du jämför med `LIKE` .

Du kan använda följande jokertecken som:

| Jokertecken | Beskrivning                                                  | Exempel                                     |
| -------------------- | ------------------------------------------------------------ | ------------------------------------------- |
| %                    | En sträng på noll eller flera tecken                      | DÄR c. Description som "% SO% PS%"      |
| _ (under streck)     | Valfritt enskilt Character                                       | DÄR c. Description som "% SO_PS%"      |
| [ ]                  | Ett enskilt Character inom det angivna intervallet ([a-f]) eller set ([ABCDEF]). | DÄR c. Description som "% SO [t-z] PS%"  |
| [^]                  | Ett enskilt Character som inte ligger inom det angivna intervallet ([^ a-f]) eller set ([^ ABCDEF]). | DÄR c. Description som "% SO [^ abc] PS%" |


### <a name="using-like-with-the--wildcard-character"></a>Använda LIKE med jokertecknet%

`%`Tecknet matchar valfri sträng med noll eller flera tecken. Genom att t. ex. Placera en `%` i början och slutet av mönstret returnerar följande fråga alla objekt med en beskrivning som innehåller `fruit` :

```sql
SELECT *
FROM c
WHERE c.description LIKE "%fruit%"
```

Om du bara har använt ett `%` tecken i början av mönstret returnerar du bara objekt med en beskrivning som börjar med `fruit` :

```sql
SELECT *
FROM c
WHERE c.description LIKE "fruit%"
```


### <a name="using-not-like"></a>Använd inte som

I exemplet nedan returneras alla objekt med en beskrivning som inte innehåller `fruit` :

```sql
SELECT *
FROM c
WHERE c.description NOT LIKE "%fruit%"
```

### <a name="using-the-escape-clause"></a>Använda Escape-satsen

Du kan söka efter mönster som innehåller ett eller flera jokertecken med ESCAPE-satsen. Om du till exempel vill söka efter beskrivningar som innehåller strängen `20-30%` vill du inte tolka `%` som ett jokertecken.

```sql
SELECT *
FROM c
WHERE c.description LIKE '%20-30!%%' ESCAPE '!'
```

### <a name="using-wildcard-characters-as-literals"></a>Använda jokertecken som litteraler

Du kan omge jokertecken med hakparenteser för att behandla dem som litterala tecken. När du omsluter ett jokertecken i hakparenteser tar du bort alla särskilda attribut. Här är några exempel:

| Mönster           | Innebörd |
| ----------------- | ------- |
| SOM "20-30 [%]" | 20-30%  |
| SOM "[_] n"     | _n      |
| SOM "[[]"    | [       |
| SOM "]"        | ]       |

## <a name="in"></a>IN

Använd nyckelordet IN för att kontrol lera om ett angivet värde matchar ett värde i en lista. Följande fråga returnerar till exempel alla familje objekt där `id` är `WakefieldFamily` eller `AndersenFamily` .

```sql
    SELECT *
    FROM Families
    WHERE Families.id IN ('AndersenFamily', 'WakefieldFamily')
```

I följande exempel returneras alla objekt där status är något av de angivna värdena:

```sql
    SELECT *
    FROM Families
    WHERE Families.address.state IN ("NY", "WA", "CA", "PA", "OH", "OR", "MI", "WI", "MN", "FL")
```

SQL API ger stöd för att [iterera över JSON-matriser](sql-query-object-array.md#Iteration), med en ny konstruktion som lagts till via nyckelordet i från-källan.

Om du inkluderar din partitionsnyckel i `IN` filtret filtreras frågan automatiskt till de relevanta partitionerna.

## <a name="top"></a>ÖVERSTA

Nyckelordet TOP returnerar det första `N` antalet frågeresultat i en odefinierad ordning. Bästa praxis är att använda TOP med- `ORDER BY` satsen för att begränsa resultaten till det första `N` antalet beställda värden. Att kombinera dessa två satser är det enda sättet att förutsäga vilka rader som påverkar.

Du kan använda TOP med ett konstant värde, som i följande exempel, eller med ett variabel värde med hjälp av parametriserade frågor.

```sql
    SELECT TOP 1 *
    FROM Families f
```

Resultatet är:

```json
    [{
        "id": "AndersenFamily",
        "lastName": "Andersen",
        "parents": [
           { "firstName": "Thomas" },
           { "firstName": "Mary Kay"}
        ],
        "children": [
           {
               "firstName": "Henriette Thaulow", "gender": "female", "grade": 5,
               "pets": [{ "givenName": "Fluffy" }]
           }
        ],
        "address": { "state": "WA", "county": "King", "city": "Seattle" },
        "creationDate": 1431620472,
        "isRegistered": true
    }]
```

## <a name="next-steps"></a>Nästa steg

- [Komma igång](sql-query-getting-started.md)
- [Kopplingar](sql-query-join.md)
- [Underfrågor](sql-query-subquery.md)