---
title: EndsWith i Azure Cosmos DB frågespråk
description: Lär dig mer om funktionen ENDSWITH SQL system i Azure Cosmos DB att returnera ett booleskt värde som anger om det första sträng uttrycket slutar med det andra
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/02/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: c0cc93fee8aacc711a797925cb2e2808b73cafd1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93338840"
---
# <a name="endswith-azure-cosmos-db"></a>ENDSWITH (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Returnerar ett booleskt värde som anger om det första sträng uttrycket slutar med det andra.  
  
## <a name="syntax"></a>Syntax
  
```sql
ENDSWITH(<str_expr1>, <str_expr2> [, <bool_expr>])
```  
  
## <a name="arguments"></a>Argument
  
*str_expr1*  
   Är ett sträng uttryck.  
  
*str_expr2*  
   Är ett sträng uttryck som ska jämföras med slutet av *str_expr1*.

*bool_expr* Valfritt värde för att ignorera Skift läge. När värdet är true kommer ENDSWITH att göra en Skift läges känslig sökning. Värdet är false när det har angetts.
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett booleskt uttryck.  
  
## <a name="examples"></a>Exempel
  
I följande exempel kontrol leras om strängen "ABC" slutar med "b" och "bC".  
  
```sql
SELECT ENDSWITH("abc", "b", false) AS e1, ENDSWITH("abc", "bC", false) AS e2, ENDSWITH("abc", "bC", true) AS e3
```  
  
 Här är resultatuppsättningen.  
  
```json
[
    {
        "e1": false,
        "e2": false,
        "e3": true
    }
]
```  

## <a name="remarks"></a>Kommentarer

Den här systemfunktionen kommer att ha nytta av ett [intervall index](index-policy.md#includeexclude-strategy).

RU-förbrukningen för EndsWith ökar när egenskapens kardinalitet i systemfunktionen ökar. Om du däremot kontrollerar om ett egenskaps värde slutar med en viss sträng, kommer frågan RU-avgiften att vara beroende av antalet möjliga värden för den egenskapen.

Överväg till exempel två egenskaper: stad och land. Stadens kardinalitet är 5 000 och landets kardinalitet är 200. Här följer två exempel frågor:

```sql
    SELECT * FROM c WHERE ENDSWITH(c.town, "York", false)
```

```sql
    SELECT * FROM c WHERE ENDSWITH(c.country, "States", false)
```

Den första frågan kommer förmodligen att använda mer ru: er än den andra frågan eftersom stadens kardinalitet är högre än landet.

Om egenskaps storleken i EndsWith är större än 1 KB för vissa dokument måste du läsa in dessa dokument med frågespråket. I det här fallet kan inte frågespråket helt utvärdera EndsWith med ett index. Avgiften för EndsWith är hög om du har ett stort antal dokument med egenskaps storlekar som är större än 1 KB.

## <a name="next-steps"></a>Nästa steg

- [Sträng funktioner Azure Cosmos DB](sql-query-string-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
