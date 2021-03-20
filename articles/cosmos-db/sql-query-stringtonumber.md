---
title: StringToNumber i Azure Cosmos DB frågespråk
description: Lär dig mer om SQL system Function StringToNumber i Azure Cosmos DB.
author: ginamr
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 03/03/2020
ms.author: girobins
ms.custom: query-reference
ms.openlocfilehash: f2622958a2315458ccd01da4aea7c6b75941e755
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339571"
---
# <a name="stringtonumber-azure-cosmos-db"></a>StringToNumber (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

 Returnerar uttryck översatt till ett tal. Om uttrycket inte kan översättas returneras undefined.  
  
## <a name="syntax"></a>Syntax
  
```sql
StringToNumber(<str_expr>)  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr*  
   Är ett sträng uttryck som ska tolkas som ett JSON Number-uttryck. Talen i JSON måste vara ett heltal eller en flyttal. Mer information om JSON-formatet finns i [JSON.org](https://json.org/)  
  
## <a name="return-types"></a>Retur typer
  
  Returnerar ett tal uttryck eller ett odefinierat värde.  
  
## <a name="examples"></a>Exempel
  
  I följande exempel visas hur `StringToNumber` beter sig mellan olika typer. 

Blank steg tillåts bara före eller efter talet.

```sql
SELECT 
    StringToNumber("1.000000") AS num1, 
    StringToNumber("3.14") AS num2,
    StringToNumber("   60   ") AS num3, 
    StringToNumber("-1.79769e+308") AS num4
```  
  
 Här är resultatuppsättningen.  
  
```json
{{"num1": 1, "num2": 3.14, "num3": 60, "num4": -1.79769e+308}}
```  

I JSON måste ett giltigt tal vara ett heltal eller ett flytt ALS nummer.

```sql
SELECT   
    StringToNumber("0xF")
```  
  
 Här är resultatuppsättningen.  
  
```json
{{}}
```  

Det överförda uttrycket kommer att tolkas som ett tal uttryck. dessa indata utvärderas inte till typ nummer och returneras därför inte. 

```sql
SELECT 
    StringToNumber("99     54"),   
    StringToNumber(undefined),
    StringToNumber("false"),
    StringToNumber(false),
    StringToNumber(" "),
    StringToNumber(NaN)
```  
  
 Här är resultatuppsättningen.  
  
```json
{{}}
```  

## <a name="remarks"></a>Kommentarer

Den här system funktionen kommer inte att använda indexet.

## <a name="next-steps"></a>Nästa steg

- [Sträng funktioner Azure Cosmos DB](sql-query-string-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
