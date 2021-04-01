---
title: 'Användardefinierade funktioner (UDF: er) i Azure Cosmos DB'
description: Lär dig mer om användardefinierade funktioner i Azure Cosmos DB.
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: tisande
ms.custom: devx-track-js
ms.openlocfilehash: 98698264f0beb25a8b4f74861f1150ae889d7115
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96546349"
---
# <a name="user-defined-functions-udfs-in-azure-cosmos-db"></a>Användardefinierade funktioner (UDF: er) i Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

SQL API ger stöd för användardefinierade funktioner (UDF: er). Med skalär UDF: er kan du skicka med noll eller många argument och returnera ett enskilt argument resultat. API: et kontrollerar varje argument för att vara juridiska JSON-värden.  

## <a name="udf-use-cases"></a>UDF användnings fall

API: et utökar SQL-syntaxen för att stödja anpassad program logik med UDF: er. Du kan registrera UDF: er med SQL-API: et och referera dem i SQL-frågor. Till skillnad från lagrade procedurer och utlösare är UDF: er skrivskyddade.

Med UDF: er kan du utöka Azure Cosmos DBens frågespråk. UDF: er är ett bra sätt att uttrycka komplexa affärs logik i en frågas projektion.

Vi rekommenderar dock att du undviker UDF: er när:

- En ekvivalent [system funktion](sql-query-system-functions.md) finns redan i Azure Cosmos dB. System funktioner använder alltid färre RU-objekt än motsvarande UDF.
- UDF är det enda filtret i- `WHERE` satsen i din fråga. UDF-filen använder inte indexet så att utvärdering av UDF kräver inläsning av dokument. Om du kombinerar ytterligare filter-predikat som använder indexet, i kombination med UDF, `WHERE` minskas antalet dokument som bearbetas av UDF-filen i-satsen.

Om du måste använda samma UDF flera gånger i en fråga bör du referera till UDF i en under [fråga](sql-query-subquery.md#evaluate-once-and-reference-many-times), så att du kan använda ett JOIN-uttryck för att utvärdera UDF-filen en gång, men referera till den flera gånger.

## <a name="examples"></a>Exempel

I följande exempel registreras en UDF under en objekt behållare i Cosmos-databasen. I exemplet skapas en UDF vars namn är `REGEX_MATCH` . Det accepterar två JSON-sträng värden `input` och `pattern` kontrollerar om det första matchar mönstret som anges i den andra med hjälp av JavaScript- `string.match()` funktionen.

```javascript
       UserDefinedFunction regexMatchUdf = new UserDefinedFunction
       {
           Id = "REGEX_MATCH",
           Body = @"function (input, pattern) {
                      return input.match(pattern) !== null;
                   };",
       };

       UserDefinedFunction createdUdf = client.CreateUserDefinedFunctionAsync(
           UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
           regexMatchUdf).Result;  
```

Använd nu denna UDF i en fråga-projektion. Du måste kvalificera UDF: er med Skift läges känsligt prefix `udf.` när du anropar dem inifrån frågor.

```sql
    SELECT udf.REGEX_MATCH(Families.address.city, ".*eattle")
    FROM Families
```

Resultatet är:

```json
    [
      {
        "$1": true
      },
      {
        "$1": false
      }
    ]
```

Du kan använda UDF-filen som är kvalificerad med `udf.` prefixet inuti ett filter, som i följande exempel:

```sql
    SELECT Families.id, Families.address.city
    FROM Families
    WHERE udf.REGEX_MATCH(Families.address.city, ".*eattle")
```

Resultatet är:

```json
    [{
        "id": "AndersenFamily",
        "city": "Seattle"
    }]
```

I själva verket är UDF: er giltiga skalära uttryck som du kan använda i både projektioner och filter.

Om du vill utöka kraften i UDF: er kan du titta på ett annat exempel med villkorlig logik:

```javascript
       UserDefinedFunction seaLevelUdf = new UserDefinedFunction()
       {
           Id = "SEALEVEL",
           Body = @"function(city) {
                   switch (city) {
                       case 'Seattle':
                           return 520;
                       case 'NY':
                           return 410;
                       case 'Chicago':
                           return 673;
                       default:
                           return -1;
                    }"
            };

            UserDefinedFunction createdUdf = await client.CreateUserDefinedFunctionAsync(
                UriFactory.CreateDocumentCollectionUri("myDatabase", "families"),
                seaLevelUdf);
```

I följande exempel övningaras UDF:

```sql
    SELECT f.address.city, udf.SEALEVEL(f.address.city) AS seaLevel
    FROM Families f
```

Resultatet är:

```json
     [
      {
        "city": "Seattle",
        "seaLevel": 520
      },
      {
        "city": "NY",
        "seaLevel": 410
      }
    ]
```

Om egenskaperna som anges av UDF-parametrarna inte är tillgängliga i JSON-värdet betraktas parametern som odefinierad och UDF-anropet hoppas över. Om resultatet av UDF-filen är odefinierat inkluderas det inte i resultatet.

Som föregående exempel visar UDF: er integrerar du kraften hos JavaScript-språket med SQL-API: et. UDF: er tillhandahåller ett omfattande programmerbart gränssnitt för att göra komplexa procedurer, villkorlig logik med hjälp av inbyggda funktioner för JavaScript-körning. SQL-API: et tillhandahåller argumenten för UDF: er för varje käll objekt med det aktuella WHERE-eller SELECT-sats stadiet bearbetning. Resultatet är sömlöst införlivat i den övergripande körnings pipelinen. I sammanfattning är UDF: er fantastiska verktyg för att utföra komplex affärs logik som en del av frågorna.

## <a name="next-steps"></a>Nästa steg

- [Introduktion till Azure Cosmos DB](introduction.md)
- [Systemfunktioner](sql-query-system-functions.md)
- [Aggregeringar](sql-query-aggregate-functions.md)