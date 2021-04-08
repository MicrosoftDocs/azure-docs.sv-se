---
title: StartsWith i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function STARTSWITH i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/20/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 7217a8416354de260f958bfb5b980a8fdc391618
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93334930"
---
# <a name="startswith-azure-cosmos-db"></a>STARTSWITH (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Returnerar ett booleskt värde som anger om det första sträng uttrycket börjar med det andra.  
  
## <a name="syntax"></a>Syntax
  
```sql
STARTSWITH(<str_expr1>, <str_expr2> [, <bool_expr>])  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr1*  
   Är ett sträng uttryck.
  
*str_expr2*  
   Är ett sträng uttryck som ska jämföras med början av *str_expr1*.

*bool_expr* Valfritt värde för att ignorera Skift läge. När värdet är true kommer STARTSWITH att göra en Skift läges känslig sökning. Värdet är false när det har angetts.

## <a name="return-types"></a>Retur typer
  
  Returnerar ett booleskt uttryck.  
  
## <a name="examples"></a>Exempel
  
I följande exempel kontrol leras om strängen "ABC" börjar med "b" och "A".  
  
```sql
SELECT STARTSWITH("abc", "b", false) AS s1, STARTSWITH("abc", "A", false) AS s2, STARTSWITH("abc", "A", true) AS s3
```  
  
 Här är resultatuppsättningen.  
  
```json
[
    {
        "s1": false,
        "s2": false,
        "s3": true
    }
]
```  

## <a name="remarks"></a>Kommentarer

Den här systemfunktionen kommer att ha nytta av ett [intervall index](index-policy.md#includeexclude-strategy).

## <a name="next-steps"></a>Nästa steg

- [Sträng funktioner Azure Cosmos DB](sql-query-string-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
