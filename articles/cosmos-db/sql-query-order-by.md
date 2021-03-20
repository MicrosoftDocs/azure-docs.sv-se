---
title: ORDER BY-sats i Azure Cosmos DB
description: Läs mer om SQL ORDER BY-satsen för Azure Cosmos DB. Använd SQL som Azure Cosmos DB JSON-frågespråk.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 06/06/2020
ms.author: tisande
ms.openlocfilehash: 887dc13eb5e351688718d2a221e69499557b23e5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93338313"
---
# <a name="order-by-clause-in-azure-cosmos-db"></a>ORDER BY-sats i Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Den valfria `ORDER BY` satsen anger sorterings ordningen för resultat som returneras av frågan.

## <a name="syntax"></a>Syntax
  
```sql  
ORDER BY <sort_specification>  
<sort_specification> ::= <sort_expression> [, <sort_expression>]  
<sort_expression> ::= {<scalar_expression> [ASC | DESC]} [ ,...n ]  
```  

## <a name="arguments"></a>Argument
  
- `<sort_specification>`  
  
   Anger en egenskap eller ett uttryck som resultatet av frågeresultatet ska sorteras efter. En sorterings kolumn kan anges som ett namn eller ett egenskaps-alias.  
  
   Flera egenskaper kan anges. Egenskaps namn måste vara unika. Ordningen på sorterings egenskaperna i- `ORDER BY` satsen definierar organisationen för den sorterade resultat uppsättningen. Det vill säga resultat uppsättningen sorteras efter den första egenskapen och sedan sorteras den sorterade listan efter den andra egenskapen och så vidare.  
  
   De egenskaps namn som refereras i- `ORDER BY` satsen måste motsvara antingen en egenskap i SELECT-listan eller till en egenskap som har definierats i mängden som anges i `FROM` satsen utan tvetydigheter.  
  
- `<sort_expression>`  
  
   Anger en eller flera egenskaper eller uttryck som du kan använda för att sortera frågeresultatet.  
  
- `<scalar_expression>`  
  
   Mer information finns i avsnittet [skalära uttryck](sql-query-scalar-expressions.md) .  
  
- `ASC | DESC`  
  
   Anger att värdena i den angivna kolumnen ska sorteras i stigande eller fallande ordning. `ASC` sorterar från det lägsta värdet till det högsta värdet. `DESC` sorterar från det högsta värdet till det lägsta värdet. `ASC` är standard sorterings ordningen. Null-värden behandlas som lägsta möjliga värden.  
  
## <a name="remarks"></a>Kommentarer  
  
   För- `ORDER BY` satsen krävs att indexerings principen inkluderar ett index för fälten som sorteras. Azure Cosmos DB Query runtime stöder sortering mot ett egenskaps namn och inte mot beräknade egenskaper. Azure Cosmos DB stöder flera `ORDER BY` Egenskaper. För att kunna köra en fråga med flera ORDER BY-egenskaper bör du definiera ett [sammansatt index](index-policy.md#composite-indexes) för fälten som sorteras.

> [!Note]
> Om egenskaperna som sorteras kan vara odefinierade för vissa dokument och du vill hämta dem i en ORDER BY-fråga, måste du uttryckligen ta med den här sökvägen i indexet. Standard indexerings principen tillåter inte hämtning av dokument där sorterings egenskapen är odefinierad. [Granska exempel frågor i dokument med några fält som saknas](#documents-with-missing-fields).

## <a name="examples"></a>Exempel

Här är ett exempel på en fråga som hämtar familjer i stigande ordning efter den inhemska ortens namn:

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
```

Resultatet är:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "NY"
      },
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

Följande fråga hämtar Family `id` s i ordning efter deras objekts skapande datum. Objektet `creationDate` är ett tal som representerar den *Epoka tiden*, eller förfluten tid sedan Jan. 1, 1970 på några sekunder.

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.creationDate DESC
```

Resultatet är:

```json
    [
      {
        "id": "WakefieldFamily",
        "creationDate": 1431620462
      },
      {
        "id": "AndersenFamily",
        "creationDate": 1431620472
      }
    ]
```

Dessutom kan du sortera efter flera egenskaper. En fråga som sorteras efter flera egenskaper kräver ett [sammansatt index](index-policy.md#composite-indexes). Överväg följande fråga:

```sql
    SELECT f.id, f.creationDate
    FROM Families f
    ORDER BY f.address.city ASC, f.creationDate DESC
```

Den här frågan hämtar familjen `id` i stigande ordning efter Orts namnet. Om flera objekt har samma Orts namn, sorteras frågan efter `creationDate` i fallande ordning.

## <a name="documents-with-missing-fields"></a>Dokument med fält som saknas

Frågor med `ORDER BY` som körs mot behållare med standard indexerings principen returnerar inte dokument där sorterings egenskapen är odefinierad. Om du vill inkludera dokument där sorterings egenskapen är odefinierad bör du uttryckligen ta med den här egenskapen i indexerings principen.

Här är ett exempel på en behållare med en indexerings princip som inte uttryckligen innehåller några sökvägar `"/*"` , förutom:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

Om du kör en fråga som inkluderar `lastName` i `Order By` -satsen innehåller resultatet endast dokument som har en `lastName` definierad egenskap. Vi har inte definierat någon uttrycklig sökväg för `lastName` så alla dokument utan någon `lastName` visas inte i frågeresultatet.

Här är en fråga som sorteras efter `lastName` i två dokument, en av som inte har någon `lastName` definierad:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

Resultaten innehåller bara det dokument som har en definierad `lastName` :

```json
    [
        {
            "id": "AndersenFamily",
            "lastName": "Andersen"
        }
    ]
```

Om vi uppdaterar behållarens indexerings princip för att explicit inkludera en sökväg för `lastName` , kommer vi att inkludera dokument med en odefinierad sorterings egenskap i frågeresultatet. Du måste uttryckligen definiera sökvägen för att leda till det här skalära värdet (och inte bortom det). Du bör använda `?` specialtecknet i Sök vägs definitionen i indexerings principen för att se till att du explicit indexerar egenskapen `lastName` och inga ytterligare inkapslade sökvägar. Om `Order By` frågan använder ett [sammansatt index](index-policy.md#composite-indexes), kommer resultatet alltid att innehålla dokument med en odefinierad sorterings egenskap i frågeresultatet.

Här är ett exempel på en indexerings princip som gör att du kan låta dokument med en odefinierad `lastName` visas i frågeresultaten:

```json
{
    "indexingMode": "consistent",
    "automatic": true,
    "includedPaths": [
        {
            "path": "/lastName/?"
        },
        {
            "path": "/*"
        }
    ],
    "excludedPaths": []
}
```

Om du kör samma fråga igen visas dokument som saknas `lastName` först i frågeresultatet:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName
```

Resultatet är:

```json
[
    {
        "id": "WakefieldFamily"
    },
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    }
]
```

Om du ändrar sorterings ordningen till `DESC` visas dokument som saknas `lastName` sist i frågeresultatet:

```sql
    SELECT f.id, f.lastName
    FROM Families f
    ORDER BY f.lastName DESC
```

Resultatet är:

```json
[
    {
        "id": "AndersenFamily",
        "lastName": "Andersen"
    },
    {
        "id": "WakefieldFamily"
    }
]
```

> [!Note]
> Endast .NET SDK-versionen 3.4.0 eller senare stöder ORDER BY med blandade typer. Om du vill sortera efter en kombination av odefinierade och definierade värden bör du därför använda den här versionen (eller senare).

Du kan inte styra ordningen som olika typer visas i resultatet. I ovanstående exempel visade vi hur odefinierade värden sorterades före sträng värden. Om du till exempel vill ha mer kontroll över sorterings ordningen för odefinierade värden kan du tilldela alla odefinierade egenskaper ett sträng värde på "aaaaaaaaa" eller "zzzzzzzz" för att säkerställa att de var antingen först eller sist.

## <a name="next-steps"></a>Nästa steg

- [Komma igång](sql-query-getting-started.md)
- [Indexeringsprinciper i Azure Cosmos DB](index-policy.md)
- [OFFSET LIMIT-sats](sql-query-offset-limit.md)
