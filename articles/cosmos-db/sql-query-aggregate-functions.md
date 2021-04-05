---
title: Mängd funktioner i Azure Cosmos DB
description: Lär dig mer om SQL agg regering Function-syntax, typer av mängd funktioner som stöds av Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.openlocfilehash: c0d953c8d99582f63744d51b505852b5c44bc409
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96555414"
---
# <a name="aggregate-functions-in-azure-cosmos-db"></a>Mängd funktioner i Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Mängd funktioner utför en beräkning av en uppsättning värden i `SELECT` satsen och returnerar ett enda värde. Följande fråga returnerar till exempel antalet objekt i en behållare:

```sql
    SELECT COUNT(1)
    FROM c
```

## <a name="types-of-aggregate-functions"></a>Typer av mängd funktioner

SQL-API: et stöder följande mängd funktioner. `SUM` och `AVG` använder numeriska värden, och `COUNT` , `MIN` , och `MAX` arbetar med siffror, strängar, booleska värden och null-värden.

| Funktion | Beskrivning |
|-------|-------------|
| [GMSN](sql-query-aggregate-avg.md) | Returnerar medelvärdet av värdena i uttrycket. |
| [COUNT](sql-query-aggregate-count.md) | Returnerar antalet objekt i uttrycket. |
| [MAX](sql-query-aggregate-max.md) | Returnerar maxvärdet i uttrycket. |
| [MIN](sql-query-aggregate-min.md) | Returnerar minimivärdet i uttrycket. |
| [SUM](sql-query-aggregate-sum.md) | Returnerar summan av alla värden i uttrycket. |


Du kan också returnera det skalära värdet för agg regeringen genom att använda nyckelordet VALUE. Till exempel returnerar följande fråga antalet värden som ett enskilt tal:

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
```

Resultatet är:

```json
    [ 2 ]
```

Du kan också kombinera agg regeringar med filter. Följande fråga returnerar till exempel antalet objekt med adress statusen för `WA` .

```sql
    SELECT VALUE COUNT(1)
    FROM Families f
    WHERE f.address.state = "WA"
```

Resultatet är:

```json
    [ 1 ]
```

## <a name="remarks"></a>Kommentarer

Dessa sammanställda system funktioner kommer att ha nytta av ett [intervall index](index-policy.md#includeexclude-strategy). Om du förväntar dig att göra ett,,, `AVG` `COUNT` `MAX` `MIN` eller `SUM` på en egenskap, bör du [ta med relevant sökväg i indexerings principen](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Nästa steg

- [Introduktion till Azure Cosmos DB](introduction.md)
- [Systemfunktioner](sql-query-system-functions.md)
- [Användardefinierade funktioner](sql-query-udfs.md)