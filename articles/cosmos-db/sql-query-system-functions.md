---
title: System funktioner i Azure Cosmos DB frågespråk
description: Lär dig mer om inbyggda och användardefinierade SQL system-funktioner i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/03/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 36ebe80671c77cc83ddba770e3259f6542472e58
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99526847"
---
# <a name="system-functions-azure-cosmos-db"></a>System funktioner (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Cosmos DB innehåller många inbyggda SQL-funktioner. Kategorierna med inbyggda funktioner visas nedan.  
  
|Funktionsgrupp|Beskrivning|Operations|  
|--------------|-----------------|-----------------| 
|[Matrisfunktioner](sql-query-array-functions.md)|Mat ris funktionerna utför en åtgärd på ett värde för mat ris värden och returnerar numeriska värden, booleska värden eller mat ris värden. | [ARRAY_CONCAT](sql-query-array-concat.md), [ARRAY_CONTAINS](sql-query-array-contains.md), [ARRAY_LENGTH](sql-query-array-length.md), [ARRAY_SLICE](sql-query-array-slice.md) |
|[Datum-och tids funktioner](sql-query-date-time-functions.md)|Med funktionerna för datum och tid kan du hämta aktuellt UTC-datum och tid i två formulär; en numerisk tidstämpel vars värde är UNIX-epoken i millisekunder eller som en sträng som följer ISO 8601-formatet. | [GetCurrentDateTime](sql-query-getcurrentdatetime.md), [GetCurrentTimestamp](sql-query-getcurrenttimestamp.md), [GetCurrentTicks](sql-query-getcurrentticks.md) |
|[Matematiska funktioner](sql-query-mathematical-functions.md)|De matematiska funktionerna utför en beräkning, vanligt vis baserat på indatavärden som anges som argument, och returnerar ett numeriskt värde. | [ABS](sql-query-abs.md), [arccos](sql-query-acos.md), [ARCSIN](sql-query-asin.md), [ARCTAN](sql-query-atan.md), [ATN2](sql-query-atn2.md), [tak](sql-query-ceiling.md), [cos](sql-query-cos.md), [COT](sql-query-cot.md), [grader](sql-query-degrees.md), [exp](sql-query-exp.md), [våning](sql-query-floor.md), [log](sql-query-log.md), [log10](sql-query-log10.md), [PI](sql-query-pi.md), [Exponent](sql-query-power.md), [radianer](sql-query-radians.md), [rand](sql-query-rand.md), [Round](sql-query-round.md), [Sign](sql-query-sign.md), [sin](sql-query-sin.md), [sqrt](sql-query-sqrt.md), [Square](sql-query-square.md), [Tan](sql-query-tan.md), [TRUNC](sql-query-trunc.md) |
|[Spatiella funktioner](sql-query-spatial-functions.md)|De spatiala funktionerna utför en åtgärd på ett värde för spatialdata och returnerar ett numeriskt eller booleskt värde. | [ST_DISTANCE](sql-query-st-distance.md), [ST_INTERSECTS](sql-query-st-intersects.md), [ST_ISVALID](sql-query-st-isvalid.md), [ST_ISVALIDDETAILED](sql-query-st-isvaliddetailed.md), [ST_WITHIN](sql-query-st-within.md) |
|[Strängfunktioner](sql-query-string-functions.md)|Strängen Functions utför en åtgärd på ett sträng indatavärde och returnerar ett sträng värde, ett numeriskt eller booleskt värde. | [Concat](sql-query-concat.md), [innehåller](sql-query-contains.md), [ENDSWITH](sql-query-endswith.md), [INDEX_OF](sql-query-index-of.md), [vänster](sql-query-left.md), [length](sql-query-length.md), [lägre](sql-query-lower.md), [LTRIM](sql-query-ltrim.md), [REGEXMATCH](sql-query-regexmatch.md)[Ersätt](sql-query-replace.md), [REPLIKERA](sql-query-replicate.md), [Omvänd](sql-query-reverse.md), [höger](sql-query-right.md), [RTRIM](sql-query-rtrim.md), [STARTSWITH](sql-query-startswith.md), [StringToArray](sql-query-stringtoarray.md), StringToBoolean [, StringToNull](sql-query-stringtoboolean.md), StringToNumber [, StringToObject](sql-query-stringtonumber.md), [substräng](sql-query-substring.md), [toString](sql-query-tostring.md), [trim](sql-query-trim.md), [versaler](sql-query-upper.md) [](sql-query-stringtonull.md) [](sql-query-stringtoobject.md) |
|[Funktioner för typkontroll](sql-query-type-checking-functions.md)|Med funktionerna för typkontroll kan du kontrollera typen av ett uttryck i SQL-frågor. | [IS_ARRAY](sql-query-is-array.md), [IS_BOOL](sql-query-is-bool.md), [IS_DEFINED](sql-query-is-defined.md), [IS_NULL](sql-query-is-null.md), [IS_NUMBER](sql-query-is-number.md), [IS_OBJECT](sql-query-is-object.md), [IS_PRIMITIVE](sql-query-is-primitive.md), [IS_STRING](sql-query-is-string.md) |

## <a name="built-in-versus-user-defined-functions-udfs"></a>Inbyggda kontra användardefinierade funktioner (UDF: er)

Om du för närvarande använder en användardefinierad funktion (UDF) för vilken en inbyggd funktion nu är tillgänglig, kan motsvarande inbyggda funktion gå snabbare att köra och effektivare.

## <a name="built-in-versus-ansi-sql-functions"></a>Inbyggda jämfört med ANSI SQL Functions

Den största skillnaden mellan Cosmos DB Functions och ANSI SQL Functions är att Cosmos DB funktioner är utformade för att fungera bra med schema lösa och blandade schema data. Om t. ex. en egenskap saknas eller har ett icke-numeriskt värde `undefined` , hoppas objektet över i stället för att returnera ett fel.

## <a name="next-steps"></a>Nästa steg

- [Introduktion till Azure Cosmos DB](introduction.md)
- [Matrisfunktioner](sql-query-array-functions.md)
- [Datum- och tidsfunktioner](sql-query-date-time-functions.md)
- [Matematiska funktioner](sql-query-mathematical-functions.md)
- [Spatiella funktioner](sql-query-spatial-functions.md)
- [Strängfunktioner](sql-query-string-functions.md)
- [Funktioner för typkontroll](sql-query-type-checking-functions.md)
- [Användardefinierade funktioner](sql-query-udfs.md)
- [Aggregeringar](sql-query-aggregate-functions.md)
