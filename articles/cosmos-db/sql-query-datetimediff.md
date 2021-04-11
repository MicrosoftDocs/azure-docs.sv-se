---
title: DateTimeDiff i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function DateTimeDiff i Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: aeea2905b6bae094c92bd8b5d46523225c745494
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104595657"
---
# <a name="datetimediff-azure-cosmos-db"></a>DateTimeDiff (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
Returnerar antalet (som ett signerat heltal) för angivna DateTimePart gränser korsade mellan angivet *StartDate* och *EndDate*.
  
## <a name="syntax"></a>Syntax
  
```sql
DateTimeDiff (<DateTimePart> , <StartDate> , <EndDate>)
```

## <a name="arguments"></a>Argument
  
*DateTimePart*  
   Den del av datumet som DateTimeAdd lägger till ett heltals nummer. I den här tabellen visas alla giltiga DateTimePart-argument:

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

*/SD*  
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

*EndDate*  
   UTC-datum och tid i ett ISO 8601-sträng värde i formatet `YYYY-MM-DDThh:mm:ss.fffffffZ`

## <a name="return-types"></a>Retur typer

Returnerar ett signerat heltals värde.

## <a name="remarks"></a>Kommentarer

DateTimeDiff kommer att returneras `undefined` av följande orsaker:

- Det angivna DateTimePart-värdet är ogiltigt
- StartDate eller EndDate är inte en giltig ISO 8601 DateTime

DateTimeDiff returnerar alltid ett signerat heltals värde och är ett mått på antalet DateTimePart gränser som är korsade, inte mätning av tidsintervallet.

## <a name="examples"></a>Exempel
  
I följande exempel beräknas antalet dag gränser korsade mellan `2020-01-01T01:02:03.1234527Z` och `2020-01-03T01:02:03.1234567Z` .

```sql
SELECT DateTimeDiff("day", "2020-01-01T01:02:03.1234527Z", "2020-01-03T01:02:03.1234567Z") AS DifferenceInDays
```

```json
[
    {
        "DifferenceInDays": 2
    }
]
```  

I följande exempel beräknas antalet år gränser korsade mellan `2028-01-01T01:02:03.1234527Z` och `2020-01-03T01:02:03.1234567Z` .

```sql
SELECT DateTimeDiff("yyyy", "2028-01-01T01:02:03.1234527Z", "2020-01-03T01:02:03.1234567Z") AS DifferenceInYears
```

```json
[
    {
        "DifferenceInYears": -8
    }
]
```

I följande exempel beräknas antalet Tim gränser korsade mellan `2020-01-01T01:00:00.1234527Z` och `2020-01-01T01:59:59.1234567Z` . Även om dessa DateTime-värden är över 0,99 timmar, `DateTimeDiff` returnerar 0 eftersom inga Tim gränser överskreds.

```sql
SELECT DateTimeDiff("hh", "2020-01-01T01:00:00.1234527Z", "2020-01-01T01:59:59.1234567Z") AS DifferenceInHours
```

```json
[
    {
        "DifferenceInHours": 0
    }
]
```

## <a name="next-steps"></a>Nästa steg

- [Datum-och tids funktioner Azure Cosmos DB](sql-query-date-time-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
