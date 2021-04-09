---
title: GetCurrentTicks i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function GetCurrentTicks i Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 16004e6e471094c99229c32a63396ac3b0490905
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "99524335"
---
# <a name="getcurrentticks-azure-cosmos-db"></a>GetCurrentTicks (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Returnerar antalet 100-nanosekunder som har förflutit sedan 00:00:00 torsdag, 1 januari 1970.
  
## <a name="syntax"></a>Syntax
  
```sql
GetCurrentTicks ()
```

## <a name="return-types"></a>Retur typer

Returnerar ett signerat numeriskt värde, det aktuella antalet 100-nanosekunder som har förflutit sedan UNIX-epoken. Med andra ord returnerar GetCurrentTicks antalet 100 nanosekunder som har förflutit sedan 00:00:00 torsdag 1 januari 1970.

## <a name="remarks"></a>Kommentarer

GetCurrentTicks () är en icke-deterministisk funktion. Resultatet som returneras är UTC (Coordinated Universal Time).

> [!NOTE]
> Den här system funktionen kommer inte att använda indexet. Om du behöver jämföra värden med den aktuella tiden hämtar du den aktuella tiden innan du kör frågekörningen och använder det konstanta sträng värdet i- `WHERE` satsen.

## <a name="examples"></a>Exempel

Här är ett exempel som returnerar den aktuella tiden, mätt i Tick:

```sql
SELECT GetCurrentTicks() AS CurrentTimeInTicks
```

```json
[
    {
        "CurrentTimeInTicks": 15973607943002652
    }
]
```

## <a name="next-steps"></a>Nästa steg

- [Datum-och tids funktioner Azure Cosmos DB](sql-query-date-time-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
