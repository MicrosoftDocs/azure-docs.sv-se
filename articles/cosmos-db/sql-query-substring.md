---
title: Under sträng i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function-understräng i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 17888ccd8fc51ed96f7fc92a0f9275d2c8cb56f8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93340846"
---
# <a name="substring-azure-cosmos-db"></a>Under sträng (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Returnerar en del av ett sträng uttryck som börjar vid den angivna tecken noll-baserade positionen och fortsätter till den angivna längden, eller till slutet av strängen.  
  
## <a name="syntax"></a>Syntax
  
```sql
SUBSTRING(<str_expr>, <num_expr1>, <num_expr2>)  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr*  
   Är ett sträng uttryck.
  
*num_expr1*  
   Är ett numeriskt uttryck som anger start tecken. Värdet 0 är det första *str_exprets* första bokstav.
  
*num_expr2*  
   Är ett numeriskt uttryck som anger det maximala antalet tecken i *str_expr* som ska returneras. Värdet 0 eller mindre resulterar i en tom sträng.

## <a name="return-types"></a>Retur typer
  
  Returnerar ett sträng uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel returneras del strängen "ABC" från 1 till och med en längd på 1 tecken.  
  
```sql
SELECT SUBSTRING("abc", 1, 1) AS substring  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"substring": "b"}]  
```

## <a name="remarks"></a>Kommentarer

Den här systemfunktionen kommer att ha nytta av ett [intervall index](index-policy.md#includeexclude-strategy) om start positionen är `0` .

## <a name="next-steps"></a>Nästa steg

- [Sträng funktioner Azure Cosmos DB](sql-query-string-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
