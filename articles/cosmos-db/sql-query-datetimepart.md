---
title: DateTimePart i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function DateTimePart i Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/14/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 97eaff405086190b60279ac7d5cf8bf441c8f840
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93336375"
---
# <a name="datetimepart-azure-cosmos-db"></a>DateTimePart (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Returnerar värdet för det angivna DateTimePart mellan angivet DateTime-värde.
  
## <a name="syntax"></a>Syntax
  
```sql
DateTimePart (<DateTimePart> , <DateTime>)
```

## <a name="arguments"></a>Argument
  
*DateTimePart*  
   Den del av datumet då DateTimePart kommer att returnera värdet. I den här tabellen visas alla giltiga DateTimePart-argument:

| DateTimePart | förkortningar        |
| ------------ | -------------------- |
| Year         | "Year", "åååå", "åå" |
| Månad        | "månad", "mm", "m"   |
| Dag          | "dag", "DD", "d"     |
| Tid         | "timme", "hh"         |
| Minut       | "minut", "mi", "n"  |
| Second       | "sekund", "ss", "s"  |
| Tiden  | "millisekund", "MS"  |
| Latens  | "mikrosekunder", "mcs" |
| Nanosekund   | "nanosekund", "ns"   |

*DateTime*  
   UTC-datum och tid i ett ISO 8601-sträng värde i formatet `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>Retur typer

Returnerar ett positivt heltals värde.

## <a name="remarks"></a>Kommentarer

DateTimePart kommer att returneras `undefined` av följande orsaker:

- Det angivna DateTimePart-värdet är ogiltigt
- DateTime är inte en giltig ISO 8601 DateTime

Den här system funktionen kommer inte att använda indexet.

## <a name="examples"></a>Exempel

Här är ett exempel som returnerar värdet för heltal i månaden:

```sql
SELECT DateTimePart("m", "2020-01-02T03:04:05.6789123Z") AS MonthValue
```

```json
[
    {
        "MonthValue": 1
    }
]
```

Här är ett exempel som returnerar antalet mikrosekunder:

```sql
SELECT DateTimePart("mcs", "2020-01-02T03:04:05.6789123Z") AS MicrosecondsValue
```

```json
[
    {
        "MicrosecondsValue": 678912
    }
]
```

## <a name="next-steps"></a>Nästa steg

- [Datum-och tids funktioner Azure Cosmos DB](sql-query-date-time-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
