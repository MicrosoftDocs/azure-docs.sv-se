---
title: ANTAL i Azure Cosmos DB frågespråk
description: Lär dig mer om funktionen Count (COUNT) SQL system i Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 5228558f4bcb146ec08ee5fff45fb1bdf4d56f01
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96553405"
---
# <a name="count-azure-cosmos-db"></a>ANTAL (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Den här system funktionen returnerar antalet värden i uttrycket.
  
## <a name="syntax"></a>Syntax
  
```sql
COUNT(<scalar_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*scalar_expr*  
   Är ett skalärt uttryck
  
## <a name="return-types"></a>Retur typer
  
Returnerar ett numeriskt uttryck.  
  
## <a name="examples"></a>Exempel
  
I följande exempel returneras det totala antalet objekt i en behållare:
  
```sql
SELECT COUNT(1)
FROM c
``` 
COUNT kan ta alla skalära uttryck som inmatade. I nedanstående fråga skapas ett motsvarande resultat:

```sql
SELECT COUNT(2)
FROM c
```

## <a name="remarks"></a>Kommentarer

Den här systemfunktionen kommer att ha nytta av ett [intervall index](index-policy.md#includeexclude-strategy) för egenskaper i frågans filter.

## <a name="next-steps"></a>Nästa steg

- [Matematiska funktioner i Azure Cosmos DB](sql-query-mathematical-functions.md)
- [System funktioner i Azure Cosmos DB](sql-query-system-functions.md)
- [Mängd funktioner i Azure Cosmos DB](sql-query-aggregate-functions.md)