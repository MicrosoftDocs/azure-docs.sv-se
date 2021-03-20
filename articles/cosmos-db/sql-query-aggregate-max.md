---
title: MAX i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system-funktionen Max (MAX) i Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: a3d8e3f2687a492461bdbbdd761e26cdb58f9e96
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96553408"
---
# <a name="max-azure-cosmos-db"></a>MAX (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Den här mängd funktionen returnerar Max värdet för värdena i uttrycket.
  
## <a name="syntax"></a>Syntax
  
```sql
MAX(<scalar_expr>)  
```  
  
## <a name="arguments"></a>Argument

*scalar_expr*  
   Är ett skalärt uttryck. 
  
## <a name="return-types"></a>Retur typer
  
Returnerar ett skalärt uttryck.  
  
## <a name="examples"></a>Exempel
  
I följande exempel returneras Max värdet `propertyA` :
  
```sql
SELECT MAX(c.propertyA)
FROM c
```  

## <a name="remarks"></a>Kommentarer

Den här systemfunktionen kommer att ha nytta av ett [intervall index](index-policy.md#includeexclude-strategy). Argumenten i `MAX` kan vara Number, String, Boolean eller null. Alla odefinierade värden kommer att ignoreras.

När olika typer av data jämförs används följande prioritetsordning (i fallande ordning):

- sträng
- nummer
- boolean
- null

## <a name="next-steps"></a>Nästa steg

- [Matematiska funktioner i Azure Cosmos DB](sql-query-mathematical-functions.md)
- [System funktioner i Azure Cosmos DB](sql-query-system-functions.md)
- [Mängd funktioner i Azure Cosmos DB](sql-query-aggregate-functions.md)