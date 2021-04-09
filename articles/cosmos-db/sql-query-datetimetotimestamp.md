---
title: DateTimeToTimestamp i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function DateTimeToTimestamp i Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 3b1cbd88b4cd6576b2c31fbeb2f3db86309c5ebf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104597631"
---
# <a name="datetimetotimestamp-azure-cosmos-db"></a>DateTimeToTimestamp (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Konverterar angivet DateTime-värde till en tidsstämpel.
  
## <a name="syntax"></a>Syntax
  
```sql
DateTimeToTimestamp (<DateTime>)
```

## <a name="arguments"></a>Argument

*DateTime*  
   UTC-datum och tid i ett ISO 8601-sträng värde i formatet `YYYY-MM-DDThh:mm:ss.fffffffZ` där:
  
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

## <a name="return-types"></a>Retur typer

Returnerar ett signerat numeriskt värde, det aktuella antalet millisekunder som har förflutit sedan UNIX-epoken, dvs. antalet millisekunder som har förflutit sedan 00:00:00 torsdag, 1 januari 1970.

## <a name="remarks"></a>Kommentarer

DateTimeToTimestamp kommer att returneras `undefined` om det datetime-värde som angetts är ogiltigt

## <a name="examples"></a>Exempel
  
I följande exempel konverteras datum/tid till en tidsstämpel:

```sql
SELECT DateTimeToTimestamp("2020-07-09T23:20:13.4575530Z") AS Timestamp
```

```json
[
    {
        "Timestamp": 1594336813457
    }
]
```  

Här är ett annat exempel:

```sql
SELECT DateTimeToTimestamp("2020-07-09") AS Timestamp
```

```json
[
    {
        "Timestamp": 1594252800000
    }
]
```  

## <a name="next-steps"></a>Nästa steg

- [Datum-och tids funktioner Azure Cosmos DB](sql-query-date-time-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
