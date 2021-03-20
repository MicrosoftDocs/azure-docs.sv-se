---
title: Skalära uttryck i Azure Cosmos DB SQL-frågor
description: Läs om SQL-syntaxen för skalära uttryck för Azure Cosmos DB. Den här artikeln beskriver också hur du kombinerar skalära uttryck i komplexa uttryck med hjälp av operatorer.
author: markjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 05/17/2019
ms.author: mjbrown
ms.openlocfilehash: 34df2d2f4a2209cca93c3f7ac12dcd203bf4a089
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93339639"
---
# <a name="scalar-expressions-in-azure-cosmos-db-sql-queries"></a>Skalära uttryck i Azure Cosmos DB SQL-frågor
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

[SELECT-satsen](sql-query-select.md) stöder skalära uttryck. Ett skalärt uttryck är en kombination av symboler och operatorer som kan utvärderas för att hämta ett enda värde. Exempel på skalära uttryck är: konstanter, egenskaps referenser, mat ris element referenser, alias referenser eller funktions anrop. Skalära uttryck kan kombineras i komplexa uttryck med hjälp av operatorer.

## <a name="syntax"></a>Syntax
  
```sql  
<scalar_expression> ::=  
       <constant>
     | input_alias
     | parameter_name  
     | <scalar_expression>.property_name  
     | <scalar_expression>'['"property_name"|array_index']'  
     | unary_operator <scalar_expression>  
     | <scalar_expression> binary_operator <scalar_expression>    
     | <scalar_expression> ? <scalar_expression> : <scalar_expression>  
     | <scalar_function_expression>  
     | <create_object_expression>
     | <create_array_expression>  
     | (<scalar_expression>)
  
<scalar_function_expression> ::=  
        'udf.' Udf_scalar_function([<scalar_expression>][,…n])  
        | builtin_scalar_function([<scalar_expression>][,…n])  
  
<create_object_expression> ::=  
   '{' [{property_name | "property_name"} : <scalar_expression>][,…n] '}'  
  
<create_array_expression> ::=  
   '[' [<scalar_expression>][,…n] ']'  
  
```

## <a name="arguments"></a>Argument
  
- `<constant>`  
  
   Representerar ett konstant värde. Mer information finns i avsnittet [konstanter](sql-query-constants.md) .  
  
- `input_alias`  
  
   Representerar ett värde som definieras av `input_alias` introduceraren i- `FROM` satsen.  
  Det här värdet är inte **odefinierat** –**odefinierade** värden i inaktuella inaktuella värden hoppas över.  
  
- `<scalar_expression>.property_name`  
  
   Representerar ett värde för egenskapen för ett objekt. Om egenskapen inte finns eller om det refereras till en egenskap för ett värde, som inte är ett objekt, utvärderas uttrycket till **odefinierat** värde.  
  
- `<scalar_expression>'['"property_name"|array_index']'`  
  
   Representerar ett värde för egenskapen med namnet `property_name` eller mat ris elementet med index `array_index` för en matris. Om egenskapen/mat ris indexet inte finns eller om egenskapen/mat ris indexet refereras till ett värde som inte är ett objekt/en matris, utvärderas uttrycket till odefinierat värde.  
  
- `unary_operator <scalar_expression>`  
  
   Representerar en operator som tillämpas på ett enda värde. Mer information finns i avsnittet om [operatörer](sql-query-operators.md) .  
  
- `<scalar_expression> binary_operator <scalar_expression>`  
  
   Representerar en operator som tillämpas på två värden. Mer information finns i avsnittet om [operatörer](sql-query-operators.md) .  
  
- `<scalar_function_expression>`  
  
   Representerar ett värde som definieras av ett resultat av ett funktions anrop.  
  
- `udf_scalar_function`  
  
   Namnet på den användardefinierade skalära funktionen.  
  
- `builtin_scalar_function`  
  
   Namnet på den inbyggda skalära funktionen.  
  
- `<create_object_expression>`  
  
   Representerar ett värde som erhålls genom att skapa ett nytt objekt med angivna egenskaper och deras värden.  
  
- `<create_array_expression>`  
  
   Representerar ett värde som erhålls genom att skapa en ny matris med angivna värden som element  
  
- `parameter_name`  
  
   Representerar ett värde för det angivna parameter namnet. Parameter namn måste ha ett enda \@ som första Character.  
  
## <a name="remarks"></a>Kommentarer
  
  När du anropar en inbyggd eller användardefinierad skalär funktion måste alla argument definieras. Om något av argumenten är odefinierat kommer funktionen inte att anropas och resultatet blir odefinierat.  
  
  När du skapar ett objekt hoppas alla egenskaper som tilldelats odefinierat värde över och tas inte med i det skapade objektet.  
  
  När du skapar en matris hoppas alla element värden som tilldelats det **odefinierade** värdet över och tas inte med i det skapade objektet. Detta gör att nästa definierade element kan ta sitt ställe på ett sådant sätt att den skapade matrisen inte kommer att ha överhoppade index.  

## <a name="examples"></a>Exempel

```sql
    SELECT ((2 + 11 % 7)-2)/3
```

Resultatet är:

```json
    [{
      "$1": 1.33333
    }]
```

I följande fråga är resultatet av det skalära uttrycket boolesk:

```sql
    SELECT f.address.city = f.address.state AS AreFromSameCityState
    FROM Families f
```

Resultatet är:

```json
    [
      {
        "AreFromSameCityState": false
      },
      {
        "AreFromSameCityState": true
      }
    ]
```

## <a name="next-steps"></a>Nästa steg

- [Introduktion till Azure Cosmos DB](introduction.md)
- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Underfrågor](sql-query-subquery.md)