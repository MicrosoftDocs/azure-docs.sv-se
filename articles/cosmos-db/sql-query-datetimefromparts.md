---
title: DateTimeFromParts i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function DateTimeFromParts i Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: b0e7996b71e68db371201da1f0f5c93486ae4e29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104592106"
---
# <a name="datetimefromparts-azure-cosmos-db"></a>DateTimeFromParts (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Returnerar ett sträng-DateTime-värde som skapats från indatavärden.
  
## <a name="syntax"></a>Syntax
  
```sql
DateTimeFromParts(<numberYear>, <numberMonth>, <numberDay> [, numberHour]  [, numberMinute]  [, numberSecond] [, numberOfFractionsOfSecond])
```

## <a name="arguments"></a>Argument
  
*numberYear* Ett heltals värde för året i formatet `YYYY`

*numberMonth*  
   Heltals värde för månaden i formatet `MM`

*numberDay*  
   Heltals värde för dagen i formatet `DD`

*numberHour* (valfritt) heltals värde för timmen i formatet `hh`

*numberMinute* (valfritt) heltals värde för minuten i formatet `mm`

*numberSecond* (valfritt) heltals värde för det andra i formatet `ss`

*numberOfFractionsOfSecond* (valfritt) heltals värde för bråk delen av en sekund i formatet `.fffffff`

## <a name="return-types"></a>Retur typer

Returnerar ett UTC-datum och klock slags ISO 8601-sträng värde i formatet `YYYY-MM-DDThh:mm:ss.fffffffZ` där:
  
|Format|Beskrivning|
|-|-|
|MMMM|fyrsiffrigt år|
|MM|tvåsiffrig månad (01 = januari osv.)|
|FORMATET|fyrsiffrigt dag i månad (01 till 31)|
|T|indikerare för start av tids element|
|hh|Dubbels siffrig timme (00 – 23)|
|mm|två-siffriga minuter (00 till 59)|
|ss|tvåsiffriga sekunder (00 till 59)|
|.fffffff|sju siffrors decimal sekunder|
|Z|UTC-beteckning (Coordinated Universal Time)|
  
 Mer information om ISO 8601-formatet finns i [ISO_8601](https://en.wikipedia.org/wiki/ISO_8601)

## <a name="remarks"></a>Kommentarer

Om de angivna heltalen skulle skapa ett ogiltigt DateTime-värde kommer DateTimeFromParts att returneras `undefined` .

Om ett valfritt argument inte anges kommer värdet 0.

## <a name="examples"></a>Exempel

Här är ett exempel som bara innehåller argument som krävs för att skapa en DateTime:

```sql
SELECT DateTimeFromParts(2020, 9, 4) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T00:00:00.0000000Z"
    }
]
```

Här är ett annat exempel som också använder några valfria argument för att skapa en DateTime:

```sql
SELECT DateTimeFromParts(2020, 9, 4, 10, 52) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T10:52:00.0000000Z"
    }
]
```

Här är ett annat exempel som också använder alla valfria argument för att skapa en DateTime:

```sql
SELECT DateTimeFromParts(2020, 9, 4, 10, 52, 12, 3456789) AS DateTime
```

```json
[
    {
        "DateTime": "2020-09-04T10:52:12.3456789Z"
    }
]
```

## <a name="next-steps"></a>Nästa steg

- [Datum-och tids funktioner Azure Cosmos DB](sql-query-date-time-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
