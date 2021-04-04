---
title: CONCAt i Azure Cosmos DB frågespråk
description: Lär dig mer om hur funktionen CONCAt SQL system i Azure Cosmos DB Returnerar en sträng som är resultatet av sammanfogningen av två eller fler sträng värden
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f5fbbbdd9d29199f5b9bd173b6ab12d3d615943c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93339207"
---
# <a name="concat-azure-cosmos-db"></a>CONCAt (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Returnerar en sträng som är resultatet av en sammanfogning av två eller fler strängvärden.  
  
## <a name="syntax"></a>Syntax
  
```sql
CONCAT(<str_expr1>, <str_expr2> [, <str_exprN>])  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr*  
   Är ett sträng uttryck som sammanfogar till de andra värdena. `CONCAT`Funktionen kräver minst två *str_expr* argument.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett sträng uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel returneras den sammanfogade strängen för de angivna värdena.  
  
```sql
SELECT CONCAT("abc", "def") AS concat  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"concat": "abcdef"}]  
```  
  
## <a name="remarks"></a>Kommentarer

Den här system funktionen kommer inte att använda indexet.

## <a name="next-steps"></a>Nästa steg

- [Sträng funktioner Azure Cosmos DB](sql-query-string-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
