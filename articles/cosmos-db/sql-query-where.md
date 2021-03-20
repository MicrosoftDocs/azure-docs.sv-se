---
title: WHERE-sats i Azure Cosmos DB
description: Läs om SQL WHERE-satsen för Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/06/2020
ms.author: tisande
ms.openlocfilehash: 5620a9fb95fb52a487095afd75d5f30c82a8bce1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93341478"
---
# <a name="where-clause-in-azure-cosmos-db"></a>WHERE-sats i Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Den valfria WHERE-satsen ( `WHERE <filter_condition>` ) anger villkor som käll-JSON-objekten måste uppfylla för att frågan ska inkluderas i resultaten. Ett JSON-objekt måste utvärdera de angivna villkoren för `true` att kunna beaktas för resultatet. Index skiktet använder WHERE-satsen för att fastställa den minsta delmängd av käll objekt som kan ingå i resultatet.
  
## <a name="syntax"></a>Syntax
  
```sql  
WHERE <filter_condition>  
<filter_condition> ::= <scalar_expression>  
  
```  
  
## <a name="arguments"></a>Argument

- `<filter_condition>`  
  
   Anger det villkor som ska uppfyllas för de dokument som ska returneras.  
  
- `<scalar_expression>`  
  
   Uttryck som representerar det värde som ska beräknas. Mer information finns i [skalära uttryck](sql-query-scalar-expressions.md) .  
  
## <a name="remarks"></a>Kommentarer
  
  För att det ska gå att returnera ett uttryck som har angetts som filter villkor måste det utvärderas till sant. Endast booleskt värde `true` uppfyller villkoret, vilket ger något annat värde: odefinierat, null, falskt, tal, matris eller objekt uppfyller inte villkoret.

  Om du inkluderar din partitionsnyckel i- `WHERE` satsen som en del av ett likhets filter så filtreras frågan automatiskt till de relevanta partitionerna.

## <a name="examples"></a>Exempel

Följande fråga begär objekt som innehåller en `id` egenskap vars värde är `AndersenFamily` . Det utesluter alla objekt som inte har någon `id` egenskap eller vars värde inte matchar `AndersenFamily` .

```sql
    SELECT f.address
    FROM Families f
    WHERE f.id = "AndersenFamily"
```

Resultatet är:

```json
    [{
      "address": {
        "state": "WA",
        "county": "King",
        "city": "Seattle"
      }
    }]
```

### <a name="scalar-expressions-in-the-where-clause"></a>Skalära uttryck i WHERE-satsen

I föregående exempel visades en enkel likhetsfråga. SQL-API: et stöder också olika [skalära uttryck](sql-query-scalar-expressions.md). De mest använda är binära och unära uttryck. Egenskapsreferenser från JSON-källobjektet är också giltiga uttryck.

Du kan använda följande binära operatorer som stöds:  

|**Operatortyp**  | **Värden** |
|---------|---------|
|Aritmetisk | +,-,*,/,% |
|Binär    | \|, &, ^, <<, >>, >>> (högerskiftning med nollfyllning) |
|Logiskt    | AND, OR, NOT (och, eller, inte)      |
|Jämförelse | =, !=, &lt;, &gt;, &lt;=, &gt;=, <> |
|Sträng     |  \|\| (sammanfoga) |

Följande frågor använder binära operatorer:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE c.grade % 2 = 1     -- matching grades == 5, 1

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade ^ 4 = 1    -- matching grades == 5

    SELECT *
    FROM Families.children[0] c
    WHERE c.grade >= 5    -- matching grades == 5
```

Du kan också använda unära operatorer +,-, ~ och inte i frågor, som du ser i följande exempel:

```sql
    SELECT *
    FROM Families.children[0] c
    WHERE NOT(c.grade = 5)  -- matching grades == 1

    SELECT *
    FROM Families.children[0] c
    WHERE (-c.grade = -5)  -- matching grades == 5
```

Du kan också använda egenskaps referenser i frågor. Returnerar till exempel `SELECT * FROM Families f WHERE f.isRegistered` JSON-objektet som innehåller egenskapen `isRegistered` med värdet lika med `true` . Alla andra värden, till exempel,,,,, `false` `null` `Undefined` `<number>` `<string>` `<object>` eller `<array>` , utesluter objektet från resultatet. Du kan också använda `IS_DEFINED` funktionen typ kontroll för att fråga efter förekomsten eller frånvaron av en specifik JSON-egenskap. Returnerar till exempel `SELECT * FROM Families f WHERE NOT IS_DEFINED(f.isRegistered)` alla JSON-objekt som inte har något värde för `isRegistered` .

## <a name="next-steps"></a>Nästa steg

- [Komma igång](sql-query-getting-started.md)
- [Nyckelordet IN (I)](sql-query-keywords.md#in)
- [FROM-sats](sql-query-from.md)
