---
title: Summa i Azure Cosmos DB frågespråk
description: Lär dig mer om funktionen Sum (SUM) SQL-system i Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: d5a86cd5af504072480e0cd749caa6c0532d0bc1
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96553429"
---
# <a name="sum-azure-cosmos-db"></a>SUM (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Den här mängd funktionen returnerar summan av värdena i uttrycket.
  
## <a name="syntax"></a>Syntax
  
```sql
SUM(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*numeric_expr*  
   Är ett numeriskt uttryck.  
  
## <a name="return-types"></a>Retur typer
  
Returnerar ett numeriskt uttryck.  
  
## <a name="examples"></a>Exempel
  
I följande exempel returneras summan av `propertyA` :
  
```sql
SELECT SUM(c.propertyA)
FROM c
```  

## <a name="remarks"></a>Kommentarer

Den här systemfunktionen kommer att ha nytta av ett [intervall index](index-policy.md#includeexclude-strategy). Om några argument i `SUM` är String, Boolean eller null, kommer hela funktionen Aggregate system att returneras `undefined` . Om ett argument har ett `undefined` värde påverkar det inte `SUM` beräkningen.

## <a name="next-steps"></a>Nästa steg

- [Matematiska funktioner i Azure Cosmos DB](sql-query-mathematical-functions.md)
- [System funktioner i Azure Cosmos DB](sql-query-system-functions.md)
- [Mängd funktioner i Azure Cosmos DB](sql-query-aggregate-functions.md)