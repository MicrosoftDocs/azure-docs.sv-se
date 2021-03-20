---
title: GENOMSNITTLIGt i Azure Cosmos DB frågespråk
description: Lär dig mer om funktionen genomsnittlig (genomsnittlig) SQL-system i Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 12/02/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 2cfbca798a7b404e4ee12b93396b2a5b08d7d5bb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96555423"
---
# <a name="avg-azure-cosmos-db"></a>MEDEL (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Den här mängd funktionen returnerar medelvärdet av värdena i uttrycket.
  
## <a name="syntax"></a>Syntax
  
```sql
AVG(<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*numeric_expr*  
   Är ett numeriskt uttryck.  
  
## <a name="return-types"></a>Retur typer
  
Returnerar ett numeriskt uttryck.  
  
## <a name="examples"></a>Exempel
  
I följande exempel returneras genomsnitts värdet för `propertyA` :
  
```sql
SELECT AVG(c.propertyA)
FROM c
```  

## <a name="remarks"></a>Kommentarer

Den här systemfunktionen kommer att ha nytta av ett [intervall index](index-policy.md#includeexclude-strategy). Om några argument i `AVG` är String, Boolean eller null, kommer hela funktionen Aggregate system att returneras `undefined` . Om ett argument har ett `undefined` värde påverkar det inte `AVG` beräkningen.

## <a name="next-steps"></a>Nästa steg

- [Matematiska funktioner i Azure Cosmos DB](sql-query-mathematical-functions.md)
- [System funktioner i Azure Cosmos DB](sql-query-system-functions.md)
- [Mängd funktioner i Azure Cosmos DB](sql-query-aggregate-functions.md)