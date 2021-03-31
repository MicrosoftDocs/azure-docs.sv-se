---
title: HÖGER i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL-systemfunktionen direkt i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: be950fed563d8e80339dc80181e7fe5a78092734
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93341592"
---
# <a name="right-azure-cosmos-db"></a>HÖGER (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Returnerar den högra delen av en sträng med det angivna antalet tecken.  
  
## <a name="syntax"></a>Syntax
  
```sql
RIGHT(<str_expr>, <num_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr*  
   Är sträng uttrycket som används för att extrahera tecken från.  
  
*num_expr*  
   Är ett numeriskt uttryck som anger antalet tecken.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett sträng uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel returneras rätt del av "ABC" för olika längd värden.  
  
```sql
SELECT RIGHT("abc", 1) AS r1, RIGHT("abc", 2) AS r2 
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"r1": "c", "r2": "bc"}]  
```  

## <a name="remarks"></a>Kommentarer

Den här system funktionen kommer inte att använda indexet.

## <a name="next-steps"></a>Nästa steg

- [Sträng funktioner Azure Cosmos DB](sql-query-string-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
