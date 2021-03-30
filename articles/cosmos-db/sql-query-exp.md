---
title: EXP i Azure Cosmos DB frågespråk
description: Lär dig mer om funktionen exponent (EXP) SQL system i Azure Cosmos DB att returnera exponent svärdet för det angivna numeriska uttrycket
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 09/13/2019
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: adf80d36edbe8f9a5535e8fa04501918a62ea7ca
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93335661"
---
# <a name="exp-azure-cosmos-db"></a>EXP (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Returnerar exponent svärdet för det angivna numeriska uttrycket.  
  
## <a name="syntax"></a>Syntax
  
```sql
EXP (<numeric_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*numeric_expr*  
   Är ett numeriskt uttryck.  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett numeriskt uttryck.  
  
## <a name="remarks"></a>Kommentarer
  
  Konstanten **e** (2,718281...) är basen för naturliga logaritmer.  
  
  Exponenten för ett tal är konstanten **e** upphöjt till talet. Till exempel EXP (1.0) = e ^ 1.0 = 2.71828182845905 och EXP (10) = e ^ 10 = 22026.4657948067.  
  
  Exponenten för den naturliga logaritmen för ett tal är själva siffran: EXP (LOG (n)) = n. Och den naturliga logaritmen av exponenten för ett tal är själva siffran: LOG (EXP (n)) = n.  
  
## <a name="examples"></a>Exempel
  
  Följande exempel deklarerar en variabel och returnerar exponent svärdet för den angivna variabeln (10).  
  
```sql
SELECT EXP(10) AS exp  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{exp: 22026.465794806718}]  
```  
  
 I följande exempel returneras exponent svärdet för den naturliga logaritmen på 20 och den naturliga logaritmen för exponenten 20. Eftersom dessa funktioner är inversa funktioner i varandra är returvärdet med avrundning för flytt ALS matematik i båda fallen 20.  
  
```sql
SELECT EXP(LOG(20)) AS exp1, LOG(EXP(20)) AS exp2  
```  
  
 Här är resultatuppsättningen.  
  
```json
[{exp1: 19.999999999999996, exp2: 20}]  
```  

## <a name="next-steps"></a>Nästa steg

- [Matematiska funktioner Azure Cosmos DB](sql-query-mathematical-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
