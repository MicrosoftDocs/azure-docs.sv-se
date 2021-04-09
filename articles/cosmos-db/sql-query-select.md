---
title: SELECT-sats i Azure Cosmos DB
description: Läs om SQL SELECT-satsen för Azure Cosmos DB. Använd SQL som Azure Cosmos DB JSON-frågespråk.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: tisande
ms.openlocfilehash: 072e17b1c0ea312b4adfa1687e447fd2cadde233
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93335457"
---
# <a name="select-clause-in-azure-cosmos-db"></a>SELECT-sats i Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Varje fråga består av en `SELECT` sats och valfria [from](sql-query-from.md) -och [WHERE](sql-query-where.md) -satser, enligt ANSI SQL-standarder. Normalt räknas källan i `FROM` satsen och `WHERE` satsen använder ett filter på källan för att hämta en delmängd av JSON-objekt. `SELECT`Satsen projekterar sedan de begärda JSON-värdena i SELECT-listan.

## <a name="syntax"></a>Syntax

```sql
SELECT <select_specification>  

<select_specification> ::=
      '*'
      | [DISTINCT] <object_property_list>
      | [DISTINCT] VALUE <scalar_expression> [[ AS ] value_alias]  
  
<object_property_list> ::=
{ <scalar_expression> [ [ AS ] property_alias ] } [ ,...n ]  
```  
  
## <a name="arguments"></a>Argument
  
- `<select_specification>`  

  Egenskaper eller värde som ska väljas för resultat uppsättningen.  
  
- `'*'`  

  Anger att värdet ska hämtas utan att göra några ändringar. I synnerhet om det bearbetade värdet är ett objekt hämtas alla egenskaper.  
  
- `<object_property_list>`  
  
  Anger listan över egenskaper som ska hämtas. Varje returnerat värde är ett objekt med de angivna egenskaperna.  
  
- `VALUE`  

  Anger att JSON-värdet ska hämtas i stället för hela JSON-objektet. Detta sker till skillnad från `<property_list>` att inte omge det beräknade värdet i ett objekt.  

- `DISTINCT`
  
  Anger att dubbletter av projekterade egenskaper ska tas bort.  

- `<scalar_expression>`  

  Uttryck som representerar det värde som ska beräknas. Mer information finns i avsnittet om [skalära uttryck](sql-query-scalar-expressions.md) .  

## <a name="remarks"></a>Kommentarer

`SELECT *`Syntaxen är endast giltig om from-satsen har deklarerat exakt ett alias. `SELECT *` tillhandahåller en identitets projektion, som kan vara användbar om ingen projektion behövs. SELECT * är bara giltig om FROM-satsen anges och endast en enda indatakälla har introducerats.  
  
Både `SELECT <select_list>` och `SELECT *` är "syntaktisk socker" och kan uttryckas i enkla SELECT-uttryck som visas nedan.  
  
1. `SELECT * FROM ... AS from_alias ...`  
  
   motsvarar:  
  
   `SELECT from_alias FROM ... AS from_alias ...`  
  
2. `SELECT <expr1> AS p1, <expr2> AS p2,..., <exprN> AS pN [other clauses...]`  
  
   motsvarar:  
  
   `SELECT VALUE { p1: <expr1>, p2: <expr2>, ..., pN: <exprN> }[other clauses...]`  
  
## <a name="examples"></a>Exempel

Följande exempel på en URVALs fråga returnerar `address` från `Families` vars `id` matchningar `AndersenFamily` :

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

## <a name="next-steps"></a>Nästa steg

- [Komma igång](sql-query-getting-started.md)
- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [WHERE-satsen](sql-query-where.md)
