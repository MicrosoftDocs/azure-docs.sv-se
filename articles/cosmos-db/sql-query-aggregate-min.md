---
title: MIN i Azure Cosmos DB frågespråk
description: Läs mer om den minsta SQL-systemfunktionen (MIN) i Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 679814822cca5a72bd261d3c8944863715e31f70
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96555411"
---
# <a name="min-azure-cosmos-db"></a>MIN (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Den här mängd funktionen returnerar minimivärdet för värdena i uttrycket.
  
## <a name="syntax"></a>Syntax
  
```sql
MIN(<scalar_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*scalar_expr*  
   Är ett skalärt uttryck. 
  
## <a name="return-types"></a>Retur typer
  
Returnerar ett skalärt uttryck.  
  
## <a name="examples"></a>Exempel
  
I följande exempel returneras det lägsta värdet `propertyA` :
  
```sql
SELECT MIN(c.propertyA)
FROM c
```  

## <a name="remarks"></a>Kommentarer

Den här systemfunktionen kommer att ha nytta av ett [intervall index](index-policy.md#includeexclude-strategy). Argumenten i `MIN` kan vara Number, String, Boolean eller null. Alla odefinierade värden kommer att ignoreras.

När olika typer av data jämförs används följande prioritetsordning (i stigande ordning):

- null
- boolean
- antal
- sträng

## <a name="next-steps"></a>Nästa steg

- [Matematiska funktioner i Azure Cosmos DB](sql-query-mathematical-functions.md)
- [System funktioner i Azure Cosmos DB](sql-query-system-functions.md)
- [Mängd funktioner i Azure Cosmos DB](sql-query-aggregate-functions.md)