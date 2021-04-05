---
title: RegexMatch i Azure Cosmos DB frågespråk
description: Lär dig mer om funktionen RegexMatch SQL system i Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 10/13/2020
ms.author: tisande
ms.custom: query-reference
ms.openlocfilehash: 7f5b2831f45e902b312636e4133557a16ee7ec95
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93341645"
---
# <a name="regexmatch-azure-cosmos-db"></a>REGEXMATCH (Azure Cosmos DB)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Tillhandahåller funktioner för reguljära uttryck. Reguljära uttryck är en kortfattad och flexibel notation för att hitta text mönster. Azure Cosmos DB använder [perl-kompatibla reguljära uttryck (pcre)](http://www.pcre.org/). 

## <a name="syntax"></a>Syntax
  
```sql
RegexMatch(<str_expr1>, <str_expr2>, [, <str_expr3>])  
```  
  
## <a name="arguments"></a>Argument
  
*str_expr1*  
   Är sträng uttrycket som ska genomsökas.  
  
*str_expr2*  
   Är det reguljära uttrycket.

*str_expr3*  
   Är strängen för markerade modifierare som ska användas med det reguljära uttrycket. Detta sträng värde är valfritt. Om du vill köra RegexMatch utan modifierare kan du antingen lägga till en tom sträng eller utelämna helt. 

Du kan lära dig mer om [syntax för att skapa reguljära uttryck i perl](https://perldoc.perl.org/perlre). 

Azure Cosmos DB stöder följande fyra modifierare:

| Modifieraren | Beskrivning |
| ------ | ----------- |
| `m` | Behandla sträng uttrycket så att det genomsöks som flera rader. Utan det här alternativet kommer "^" och "$" att matcha i början eller slutet av strängen och inte varje enskild rad. |
| `s` | Tillåt att "." matchar alla bokstäver, inklusive ett rad matnings tecknet. | 
| `i` | Ignorera Skift läge vid mönster matchning. |
| `x` | Ignorera alla blank stegs tecken. |

## <a name="return-types"></a>Retur typer
  
  Returnerar ett booleskt uttryck. Returnerar odefinierad om det sträng uttryck som ska genomsökas, det reguljära uttrycket eller de valda modifierarna är ogiltiga.
  
## <a name="examples"></a>Exempel
  
Följande enkla RegexMatch-exempel kontrollerar strängen "abcd" för reguljärt uttryck med hjälp av några olika modifierare.
  
```sql
SELECT RegexMatch ("abcd", "ABC", "") AS NoModifiers, 
RegexMatch ("abcd", "ABC", "i") AS CaseInsensitive, 
RegexMatch ("abcd", "ab.", "") AS WildcardCharacter,
RegexMatch ("abcd", "ab c", "x") AS IgnoreWhiteSpace, 
RegexMatch ("abcd", "aB c", "ix") AS CaseInsensitiveAndIgnoreWhiteSpace 
```  
  
 Här är resultatuppsättningen.  
  
```json
[
    {
        "NoModifiers": false,
        "CaseInsensitive": true,
        "WildcardCharacter": true,
        "IgnoreWhiteSpace": true,
        "CaseInsensitiveAndIgnoreWhiteSpace": true
    }
]
```

Med RegexMatch kan du använda metatecken för att göra mer komplexa Strängs ökningar som annars inte skulle vara möjligt med system funktionerna StartsWith, EndsWith, contains eller StringEquals. Här följer några ytterligare exempel, som du kan köra med hjälp av den näringsvärdes uppsättning som är tillgänglig via [Azure Cosmos DB testplats för databasfrågor](https://www.documentdb.com/sql/demo). 

> [!NOTE] 
> Om du behöver använda en metatecken i ett reguljärt uttryck och inte vill att det ska ha en särskild betydelse, bör du undanta metatecken med `\` .

**Kontrol lera objekt som innehåller en beskrivning som innehåller ordet "salt" exakt en gång:**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, "salt{1}","")
```

**Kontrol lera objekt med en beskrivning som innehåller ett tal mellan 0 och 99:**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, "[0-99]","")
```

**Kontrol lera objekt med en beskrivning som innehåller fyra bokstavs ord som börjar med "S" eller "s":**

```sql
SELECT * 
FROM c 
WHERE RegexMatch (c.description, " s... ","i")
```

## <a name="remarks"></a>Kommentarer

Den här systemfunktionen kommer att ha nytta av ett [intervall index](index-policy.md#includeexclude-strategy) om det reguljära uttrycket kan delas upp i antingen StartsWith, EndsWith, contains eller StringEquals system functions.

## <a name="next-steps"></a>Nästa steg

- [Sträng funktioner Azure Cosmos DB](sql-query-string-functions.md)
- [System funktioner Azure Cosmos DB](sql-query-system-functions.md)
- [Introduktion till Azure Cosmos DB](introduction.md)
