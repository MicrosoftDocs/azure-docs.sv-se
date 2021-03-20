---
title: LÄGRE Azure Cosmos DB frågespråk
description: Lär dig mer om den lägre SQL system-funktionen i Azure Cosmos DB att returnera ett sträng uttryck efter konvertering av versaler till gemener
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: dd1d9ddefd67cadb92632fd6db7a1fbd5a34f35a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93338466"
---
# <a name="lower-azure-cosmos-db"></a>LÄGRE (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Returnerar ett stränguttryck efter att teckendata med versaler har konverterats till gemener.  

Den lägre systemfunktionen använder inte indexet. Om du planerar att göra frekventa Skift läges känsliga jämförelser kan den lägre systemfunktionen förbruka en betydande mängd RU. I så fall, i stället för att använda funktionen för lägre system för att normalisera data varje gång för jämförelser, kan du normalisera höljet vid infogning. Sedan väljer du en fråga som SELECT * från c där lägre (c. Name) = "Robert" bara väljer * från c där c.name = ' Bob '.

## <a name="syntax"></a>Syntax
  
```sql
LOWER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr*  
   Är ett sträng uttryck.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett sträng uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel visas hur du använder `LOWER` i en fråga.  
  
```sql
SELECT LOWER("Abc") AS lower
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"lower": "abc"}]  
  
```  

## <a name="remarks"></a>Kommentarer

Den här system funktionen kommer inte att använda indexet.

## <a name="next-steps"></a>Nästa steg

- [Sträng funktioner Azure Cosmos DB](sql-query-string-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
