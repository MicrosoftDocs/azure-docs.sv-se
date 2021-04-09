---
title: Arbeta med matriser och objekt i Azure Cosmos DB
description: Lär dig SQL-syntaxen för att skapa matriser och objekt i Azure Cosmos DB. Den här artikeln innehåller också några exempel på hur du utför åtgärder på mat ris objekt
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/02/2021
ms.author: tisande
ms.openlocfilehash: 1dccb8e51fbc578f8f218fe1582f95f7bcaf42d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99493800"
---
# <a name="working-with-arrays-and-objects-in-azure-cosmos-db"></a>Arbeta med matriser och objekt i Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

En viktig funktion i Azure Cosmos DB SQL API är matris-och objekt skapande. Det här dokumentet använder exempel som kan återskapas med hjälp av [Family-datauppsättningen](sql-query-getting-started.md#upload-sample-data).

Här är ett exempel objekt i den här data uppsättningen:

```json
{
  "id": "AndersenFamily",
  "lastName": "Andersen",
  "parents": [
     { "firstName": "Thomas" },
     { "firstName": "Mary Kay"}
  ],
  "children": [
     {
         "firstName": "Henriette Thaulow",
         "gender": "female",
         "grade": 5,
         "pets": [{ "givenName": "Fluffy" }]
     }
  ],
  "address": { "state": "WA", "county": "King", "city": "Seattle" },
  "creationDate": 1431620472,
  "isRegistered": true
}
```

## <a name="arrays"></a>Matriser

Du kan skapa matriser, som du ser i följande exempel:

```sql
SELECT [f.address.city, f.address.state] AS CityState
FROM Families f
```

Resultatet är:

```json
[
  {
    "CityState": [
      "Seattle",
      "WA"
    ]
  },
  {
    "CityState": [
      "NY", 
      "NY"
    ]
  }
]
```

Du kan också använda [mat ris uttrycket](sql-query-subquery.md#array-expression) för att skapa en matris från under [frågans](sql-query-subquery.md) resultat. Den här frågan hämtar alla distinkta namn på underordnade objekt i en matris.

```sql
SELECT f.id, ARRAY(SELECT DISTINCT VALUE c.givenName FROM c IN f.children) as ChildNames
FROM f
```

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

## <a name="iteration"></a><a id="Iteration"></a>Iteration

SQL API ger stöd för att iterera över JSON-matriser med [nyckelordet in](sql-query-keywords.md#in) i från-källan. Se följande exempel:

```sql
SELECT *
FROM Families.children
```

Resultatet är:

```json
[
  [
    {
      "firstName": "Henriette Thaulow",
      "gender": "female",
      "grade": 5,
      "pets": [{ "givenName": "Fluffy"}]
    }
  ], 
  [
    {
        "familyName": "Merriam",
        "givenName": "Jesse",
        "gender": "female",
        "grade": 1
    }, 
    {
        "familyName": "Miller",
        "givenName": "Lisa",
        "gender": "female",
        "grade": 8
    }
  ]
]
```

Nästa fråga utför iteration över `children` i `Families` behållaren. Den utgående matrisen skiljer sig från föregående fråga. Det här exemplet delar upp `children` och fören klar resultatet till en enda matris:  

```sql
SELECT *
FROM c IN Families.children
```

Resultatet är:

```json
[
  {
      "firstName": "Henriette Thaulow",
      "gender": "female",
      "grade": 5,
      "pets": [{ "givenName": "Fluffy" }]
  },
  {
      "familyName": "Merriam",
      "givenName": "Jesse",
      "gender": "female",
      "grade": 1
  },
  {
      "familyName": "Miller",
      "givenName": "Lisa",
      "gender": "female",
      "grade": 8
  }
]
```

Du kan filtrera efter varje enskild post i matrisen, som du ser i följande exempel:

```sql
SELECT c.givenName
FROM c IN Families.children
WHERE c.grade = 8
```

Resultatet är:

```json
[{
  "givenName": "Lisa"
}]
```

Du kan också aggregera över resultatet av en mat ris iteration. Följande fråga räknar till exempel antalet underordnade objekt bland alla familjer:

```sql
SELECT COUNT(1) AS Count
FROM child IN Families.children
```

Resultatet är:

```json
[
  {
    "Count": 3
  }
]
```

> [!NOTE]
> När du använder i nyckelordet for iteration kan du inte filtrera eller projicera egenskaper utanför matrisen. I stället bör du använda [kopplingar](sql-query-join.md).

Fler exempel finns [i vårt blogg inlägg om att arbeta med matriser i Azure Cosmos DB](https://devblogs.microsoft.com/cosmosdb/understanding-how-to-query-arrays-in-azure-cosmos-db/).

## <a name="next-steps"></a>Nästa steg

- [Komma igång](sql-query-getting-started.md)
- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Kopplingar](sql-query-join.md)
