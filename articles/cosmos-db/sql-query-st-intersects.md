---
title: ST_INTERSECTS i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system-funktionen ST_INTERSECTS i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c123446c7d7f654f0e3ace6c9d92983558509c75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100559948"
---
# <a name="st_intersects-azure-cosmos-db"></a>ST_INTERSECTS (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Returnerar ett booleskt uttryck som anger om det interjson-objekt (punkt, polygon, multipolygon eller lin Est ring) som anges i det första argumentet korsar den interjson (punkt, polygon, multipolygon eller lin Est ring) i det andra argumentet.  
  
## <a name="syntax"></a>Syntax
  
```sql
ST_INTERSECTS (<spatial_expr>, <spatial_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*spatial_expr*  
   Är ett uttryck för en interjson-punkt, polygon eller lin Est ring-objekt.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett booleskt värde.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel visas hur du hittar alla områden som överlappar med den aktuella polygonen.  
  
```sql
SELECT a.id
FROM Areas a
WHERE ST_INTERSECTS(a.location, {  
    'type':'Polygon',
    'coordinates': [[[31.8, -5], [32, -5], [32, -4.7], [31.8, -4.7], [31.8, -5]]]  
})  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{ "id": "IntersectingPolygon" }]  
```  

## <a name="remarks"></a>Kommentarer

Den här systemfunktionen kommer att ha nytta av ett [geospatialt index](index-policy.md#spatial-indexes) , förutom i frågor med agg regeringar.

## <a name="next-steps"></a>Nästa steg

- [Spatiala funktioner Azure Cosmos DB](sql-query-spatial-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
