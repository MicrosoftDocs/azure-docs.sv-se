---
title: DateTimeToTicks i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function DateTimeToTicks i Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: ab81e2b6ef19e7a5dacb80186c5364a5848077f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93336341"
---
# <a name="datetimetoticks-azure-cosmos-db"></a>DateTimeToTicks (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Konverterar angiven DateTime till tickning. Ett enda streck visar 100 nanosekunder eller 1 10-millionth av en sekund. 

## <a name="syntax"></a>Syntax
  
```sql
DateTimeToTicks (<DateTime>)
```

## <a name="arguments"></a>Argument
  
*DateTime*  
   UTC-datum och tid i ett ISO 8601-sträng värde i formatet `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>Retur typer

Returnerar ett signerat numeriskt värde, det aktuella antalet 100-nanosekunder som har förflutit sedan UNIX-epoken. Med andra ord returnerar DateTimeToTicks antalet 100-nanosekunder som har förflutit sedan 00:00:00 torsdag 1 januari 1970.

## <a name="remarks"></a>Kommentarer

DateTimeDateTimeToTicks kommer att returneras `undefined` om datetime inte är en giltig ISO 8601 datetime

Den här system funktionen kommer inte att använda indexet.

## <a name="examples"></a>Exempel

Här är ett exempel som returnerar antalet Tick:

```sql
SELECT DateTimeToTicks("2020-01-02T03:04:05.6789123Z") AS Ticks
```

```json
[
    {
        "Ticks": 15779342456789124
    }
]
```

Här är ett exempel som returnerar antalet Tick utan att ange antalet decimal sekunder:

```sql
SELECT DateTimeToTicks("2020-01-02T03:04:05Z") AS Ticks
```

```json
[
    {
        "Ticks": 15779342450000000
    }
]
```

## <a name="next-steps"></a>Nästa steg

- [Datum-och tids funktioner Azure Cosmos DB](sql-query-date-time-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
