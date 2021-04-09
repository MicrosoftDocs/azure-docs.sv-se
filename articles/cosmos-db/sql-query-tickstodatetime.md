---
title: TicksToDateTime i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function TicksToDateTime i Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 08/18/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 9adf525f4edb4104978bae32bbbef15ffb131f68
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104587261"
---
# <a name="tickstodatetime-azure-cosmos-db"></a>TicksToDateTime (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Konverterar angivet Ticket-värde till ett DateTime-värde.
  
## <a name="syntax"></a>Syntax
  
```sql
TicksToDateTime (<Ticks>)
```

## <a name="arguments"></a>Argument

*Ticks*  

Ett signerat numeriskt värde, det aktuella antalet 100 nanosekunder som har förflutit sedan UNIX-epoken. Med andra ord är det antalet 100 nanosekunder som har förflutit sedan 00:00:00 torsdag, 1 januari 1970.

## <a name="return-types"></a>Retur typer

Returnerar UTC-datum och tid i ett ISO 8601-sträng värde i formatet `YYYY-MM-DDThh:mm:ss.fffffffZ` där:
  
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

TicksToDateTime kommer att returneras `undefined` om det angivna Ticket-värdet är ogiltigt.

## <a name="examples"></a>Exempel
  
I följande exempel konverteras skalstrecken till en DateTime:

```sql
SELECT TicksToDateTime(15943368134575530) AS DateTime
```

```json
[
    {
        "DateTime": "2020-07-09T23:20:13.4575530Z"
    }
]
```  

## <a name="next-steps"></a>Nästa steg

- [Datum-och tids funktioner Azure Cosmos DB](sql-query-date-time-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
