---
title: ÖVRE i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system-funktionen i övre Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: 06f85d97266e78b343ad8da233b77e369da5ee65
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93334913"
---
# <a name="upper-azure-cosmos-db"></a>ÖVRE (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Returnerar ett stränguttryck efter att teckendata med gemener har konverterats till versaler.  

Den övre system funktionen använder inte indexet. Om du planerar att göra frekventa Skift läges känsliga jämförelser kan den övre systemfunktionen använda en betydande mängd RU. I så fall, i stället för att använda funktionen VERSALer för att normalisera data varje gång för jämförelser, kan du normalisera höljet vid infogning. Sedan väljer du en fråga som SELECT * FROM c där (c. Name) = ' BOB ' bara väljer * från c WHERE c.name = ' BOB '.

## <a name="syntax"></a>Syntax
  
```sql
UPPER(<str_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr*  
   Är ett sträng uttryck.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett sträng uttryck.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel visas hur du använder `UPPER` i en fråga  
  
```sql
SELECT UPPER("Abc") AS upper  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{"upper": "ABC"}]  
```

## <a name="remarks"></a>Kommentarer

Den här system funktionen kommer inte att använda indexet.

## <a name="next-steps"></a>Nästa steg

- [Sträng funktioner Azure Cosmos DB](sql-query-string-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
