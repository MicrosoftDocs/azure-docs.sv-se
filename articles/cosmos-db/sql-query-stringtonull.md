---
title: StringToNull i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function StringToNull i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: ee91ca3a32bacbf6590877f49bf74499fc00a8f9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339599"
---
# <a name="stringtonull-azure-cosmos-db"></a>StringToNull (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Returnerar uttryck översatt till null. Om uttrycket inte kan översättas returneras undefined.  
  
## <a name="syntax"></a>Syntax
  
```sql
StringToNull(<str_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr*  
   Är ett sträng uttryck som ska tolkas som ett null-uttryck.
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett null-uttryck eller ett odefinierat värde.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel visas hur `StringToNull` beter sig mellan olika typer. 

Följande är exempel på giltiga indatatyper.

 Blank steg tillåts bara före eller efter "null".

```sql
SELECT 
    StringToNull("null") AS n1, 
    StringToNull("  null ") AS n2,
    IS_NULL(StringToNull("null   ")) AS n3
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"n1": null, "n2": null, "n3": true}]
```  

Följande är exempel på ogiltiga indatatyper.

Null är Skift läges känsligt och måste skrivas med alla gemener, t. ex. "null".

```sql
SELECT    
    StringToNull("NULL"),
    StringToNull("Null")
```  
  
 Här är resultatuppsättningen.  
  
```json
[{}]
```  

Det överförda uttrycket kommer att parsas som ett null-uttryck. dessa indata kan inte utvärderas till typen null och returneras därför inte.

```sql
SELECT    
    StringToNull("true"), 
    StringToNull(false), 
    StringToNull(undefined),
    StringToNull(NaN) 
```  
  
 Här är resultatuppsättningen.  
  
```json
[{}]
```  

## <a name="remarks"></a>Kommentarer

Den här system funktionen kommer inte att använda indexet.

## <a name="next-steps"></a>Nästa steg

- [Sträng funktioner Azure Cosmos DB](sql-query-string-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
