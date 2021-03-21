---
title: ST_ISVALID i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system-funktionen ST_ISVALID i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 11/23/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 1e7c124da91a947a0ac8426ce8c92347396236c4
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96004440"
---
# <a name="st_isvalid-azure-cosmos-db"></a>ST_ISVALID (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Returnerar ett booleskt värde som anger om den angivna typen av polyjson-punkt, polygon, multipolygon eller lin Est ring är giltig.  
  
## <a name="syntax"></a>Syntax
  
```sql
ST_ISVALID(<spatial_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*spatial_expr*  
   Är ett interjson-, polygon-eller lin Est ring-uttryck.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett booleskt uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel visas hur du kontrollerar om en punkt är giltig med ST_VALID.  
  
  Till exempel har den här punkten ett latitud-värde som inte finns i det giltiga värde intervallet [-90, 90], så frågan returnerar false.  
  
  För polygoner kräver polyjson-specifikationen att det sista koordinat paret som anges ska vara detsamma som det första, för att skapa en sluten form. Punkter inom en polygon måste anges i en motsols ordning. En polygon som anges i medsols ordning representerar inversen för regionen i den.  
  
```sql
SELECT ST_ISVALID({ "type": "Point", "coordinates": [31.9, -132.8] }) AS b 
```  
  
 Här är resultatuppsättningen.  
  
```json
[{ "b": false }]  
```  

## <a name="next-steps"></a>Nästa steg

- [Spatiala funktioner Azure Cosmos DB](sql-query-spatial-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
