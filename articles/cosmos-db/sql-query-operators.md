---
title: SQL-fråga operatörer för Azure Cosmos DB
description: Lär dig om SQL-operatorer som likheter, jämförelse och logiska operatörer som stöds av Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: c1409bd7f098c24efbb4196d78c6dffb6048119b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93335455"
---
# <a name="operators-in-azure-cosmos-db"></a>Operatorer i Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Den här artikeln beskriver de olika operatörer som stöds av Azure Cosmos DB.

## <a name="equality-and-comparison-operators"></a>Likhets-och jämförelse operatorer

I följande tabell visas resultatet av likhetsjämförelser i SQL API mellan två godtyckliga JSON-typer.

| **Op** | **Odefinierad** | **Null** | **Boolesk** | **Nummer** | **Sträng** | **Objekt** | **Lagringsmatriser** |
|---|---|---|---|---|---|---|---|
| **Odefinierad** | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) |
| **Null** | Undefined (Odefinierad) | **Okej** | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) |
| **Boolesk** | Undefined (Odefinierad) | Undefined (Odefinierad) | **Okej** | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) |
| **Nummer** | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | **Okej** | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) |
| **Sträng** | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | **Okej** | Undefined (Odefinierad) | Undefined (Odefinierad) |
| **Objekt** | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | **Okej** | Undefined (Odefinierad) |
| **Lagringsmatriser** | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | Undefined (Odefinierad) | **Okej** |

Jämförelse operatorer som `>` ,, `>=` , `!=` `<` och `<=` , jämförelse mellan typer eller mellan två objekt eller matriser skapar `Undefined` .  

Om resultatet av det skalära uttrycket är är `Undefined` objektet inte inkluderat i resultatet, eftersom det `Undefined` inte är lika med `true` .

Följande frågans jämförelse mellan ett tal och ett sträng värde genererar till exempel `Undefined` . Filtret innehåller därför inte några resultat.

```sql
SELECT *
FROM c
WHERE 7 = 'a'
```

## <a name="logical-and-or-and-not-operators"></a>Logiska operatorer (AND, OR och NOT) (och, eller samt inte)

Logiska operatorer arbetar med booleska värden. I följande tabeller visas de logiska sanningen-tabellerna för dessa operatörer:

**Operatorn OR** (ELLER)

Returnerar `true` när något av villkoren är `true` .

|  | **True** | **False** | **Odefinierad** |
| --- | --- | --- | --- |
| **True** |Sant |Sant |Sant |
| **False** |Sant |Falskt |Undefined (Odefinierad) |
| **Odefinierad** |Sant |Undefined (Odefinierad) |Undefined (Odefinierad) |

**Operatorn AND** (OCH)

Returnerar `true` när båda uttrycken är `true` .

|  | **True** | **False** | **Odefinierad** |
| --- | --- | --- | --- |
| **True** |Sant |Falskt |Undefined (Odefinierad) |
| **False** |Falskt |Falskt |Falskt |
| **Odefinierad** |Undefined (Odefinierad) |Falskt |Undefined (Odefinierad) |

**Operatorn NOT** (INTE)

Kastar om värdet för booleska uttryck.

|  | **NOT** |
| --- | --- |
| **True** |Falskt |
| **False** |Sant |
| **Odefinierad** |Undefined (Odefinierad) |

**Prioritet för Operator**

De logiska operatörerna `OR` , `AND` och `NOT` har prioritets nivån som visas nedan:

| **Operator** | **Priority** |
| --- | --- |
| **NOT** |1 |
| **AND** |2 |
| **OR** |3 |

## <a name="-operator"></a>* Operator

Den speciella operatorn * projekterar hela objektet som det är. När den används måste den vara det enda projicerade fältet. En fråga som `SELECT * FROM Families f` är giltig, men `SELECT VALUE * FROM Families f` som  `SELECT *, f.id FROM Families f` inte är giltig.

## <a name="-and--operators"></a>? och?? operatorer

Du kan använda operatorerna ternär (?) och sammanslagning (??) för att bygga villkors uttryck, precis som C# och Java Script.

Du kan använda den ? operatör för att skapa nya JSON-egenskaper i farten. Följande fråga klassificerar till exempel betygs nivåer i `elementary` eller `other` :

```sql
     SELECT (c.grade < 5)? "elementary": "other" AS gradeLevel
     FROM Families.children[0] c
```

Kan du även kapsla anrop till? Operator, som i följande fråga: 

```sql
    SELECT (c.grade < 5)? "elementary": ((c.grade < 9)? "junior": "high") AS gradeLevel
    FROM Families.children[0] c
```

Precis som med andra fråga-operatörer är? operatorn utesluter objekt om de refererade egenskaperna saknas eller om typerna som jämförs är olika.

Använd?? operatör för att effektivt söka efter en egenskap i ett objekt vid frågor mot halv strukturer eller blandade data. Till exempel returnerar följande fråga om den `lastName` finns, eller `surname` om den `lastName` inte finns.

```sql
    SELECT f.lastName ?? f.surname AS familyName
    FROM Families f
```

## <a name="next-steps"></a>Nästa steg

- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmos-dotnet-v3)
- [Nyckelord](sql-query-keywords.md)
- [SELECT-sats](sql-query-select.md)
