---
title: GROUP BY-sats i Azure Cosmos DB
description: Lär dig mer om GROUP BY-satsen för Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: tisande
ms.openlocfilehash: d9cafc100ddd4b553577c447e82334e6ee7d1b6d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96545459"
---
# <a name="group-by-clause-in-azure-cosmos-db"></a>GROUP BY-sats i Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

GROUP BY-satsen delar in frågans resultat enligt värdena för en eller flera angivna egenskaper.

## <a name="syntax"></a>Syntax

```sql  
<group_by_clause> ::= GROUP BY <scalar_expression_list>

<scalar_expression_list> ::=
          <scalar_expression>
        | <scalar_expression_list>, <scalar_expression>
```  

## <a name="arguments"></a>Argument

- `<scalar_expression_list>`

   Anger de uttryck som ska användas för att dividera frågeresultaten.

- `<scalar_expression>`
  
   Ett skalärt uttryck tillåts förutom skalära under frågor och skalära mängder. Varje skalärt uttryck måste innehålla minst en egenskaps referens. Det finns ingen gräns för antalet enskilda uttryck eller kardinalitet för varje uttryck.

## <a name="remarks"></a>Kommentarer
  
  När en fråga använder en GROUP BY-sats kan SELECT-satsen bara innehålla del mängden av de egenskaper och system funktioner som ingår i GROUP BY-satsen. Ett undantag är [mängd funktioner](sql-query-aggregate-functions.md)som kan visas i SELECT-satsen utan att ingå i Group by-satsen. Du kan också alltid inkludera litterala värden i SELECT-satsen.

  GROUP BY-satsen måste vara efter SELECT-, FROM-och WHERE-satsen och före OFFSET LIMIT-satsen. Du kan för närvarande inte använda GROUP BY med en ORDER BY-sats men detta är planerat.

  GROUP BY-satsen tillåter inte något av följande:
  
- Egenskaper för alias eller alias för system funktioner (alias tillåts fortfarande i SELECT-satsen)
- Underfrågor
- Sammanställda system funktioner (dessa är endast tillåtna i SELECT-satsen)

Frågor med en sammansatt systemfunktion och en under fråga med `GROUP BY` stöds inte. Följande fråga stöds exempelvis inte:

```sql
SELECT COUNT(UniqueLastNames)
FROM (
SELECT AVG(f.age)
FROM f
GROUP BY f.lastName
) AS UniqueLastNames
```

## <a name="examples"></a>Exempel

I de här exemplen används närings data uppsättningen som är tillgänglig via [Azure Cosmos DB testplats för databasfrågor](https://www.documentdb.com/sql/demo).

Här är ett exempel på en fråga som returnerar det totala antalet objekt i varje foodGroup:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup
FROM Food f
GROUP BY f.foodGroup
```

Vissa resultat är (det översta nyckelordet används för att begränsa resultaten):

```json
[
    {
        "foodGroupCount": 183,
        "foodGroup": "Cereal Grains and Pasta"
    },
    {
        "foodGroupCount": 133,
        "foodGroup": "Nut and Seed Products"
    },
    {
        "foodGroupCount": 113,
        "foodGroup": "Meals, Entrees, and Side Dishes"
    },
    {
        "foodGroupCount": 64,
        "foodGroup": "Spices and Herbs"
    }
]
```

Den här frågan har två uttryck som används för att dividera resultat:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, f.foodGroup, f.version
FROM Food f
GROUP BY f.foodGroup, f.version
```

Vissa resultat:

```json
[
    {
        "foodGroupCount": 183,
        "foodGroup": "Cereal Grains and Pasta",
        "version": 1
    },
    {
        "foodGroupCount": 133,
        "foodGroup": "Nut and Seed Products",
        "version": 1
    },
    {
        "foodGroupCount": 113,
        "foodGroup": "Meals, Entrees, and Side Dishes",
        "version": 1
    },
    {
        "foodGroupCount": 64,
        "foodGroup": "Spices and Herbs",
        "version": 1
    }
]
```

Den här frågan har en systemfunktion i GROUP BY-satsen:

```sql
SELECT TOP 4 COUNT(1) AS foodGroupCount, UPPER(f.foodGroup) AS upperFoodGroup
FROM Food f
GROUP BY UPPER(f.foodGroup)
```

Vissa resultat:

```json
[
    {
        "foodGroupCount": 183,
        "upperFoodGroup": "CEREAL GRAINS AND PASTA"
    },
    {
        "foodGroupCount": 133,
        "upperFoodGroup": "NUT AND SEED PRODUCTS"
    },
    {
        "foodGroupCount": 113,
        "upperFoodGroup": "MEALS, ENTREES, AND SIDE DISHES"
    },
    {
        "foodGroupCount": 64,
        "upperFoodGroup": "SPICES AND HERBS"
    }
]
```

Den här frågan använder både nyckelord och system funktioner i uttryck för objekt egenskaper:

```sql
SELECT COUNT(1) AS foodGroupCount, ARRAY_CONTAINS(f.tags, {name: 'orange'}) AS containsOrangeTag,  f.version BETWEEN 0 AND 2 AS correctVersion
FROM Food f
GROUP BY ARRAY_CONTAINS(f.tags, {name: 'orange'}), f.version BETWEEN 0 AND 2
```

Resultatet är:

```json
[
    {
        "foodGroupCount": 10,
        "containsOrangeTag": true,
        "correctVersion": true
    },
    {
        "foodGroupCount": 8608,
        "containsOrangeTag": false,
        "correctVersion": true
    }
]
```

## <a name="next-steps"></a>Nästa steg

- [Komma igång](sql-query-getting-started.md)
- [SELECT-sats](sql-query-select.md)
- [Mängdfunktioner](sql-query-aggregate-functions.md)
