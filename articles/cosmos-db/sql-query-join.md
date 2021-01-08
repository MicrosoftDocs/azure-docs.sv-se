---
title: SQL JOIN-frågor för Azure Cosmos DB
description: Lär dig hur du kopplar ihop flera tabeller i Azure Cosmos DB för att fråga data
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 01/07/2021
ms.author: tisande
ms.openlocfilehash: cb7b2e62a9fabeeca675edb8e6aa356213e0999e
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2021
ms.locfileid: "98011415"
---
# <a name="joins-in-azure-cosmos-db"></a>Kopplingar i Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

I en Relations databas är sammanfogningar över tabeller den logiska överrullningen för att utforma normaliserade scheman. SQL-API: t använder däremot den avnormaliserade data modellen för schema fria objekt, vilket är den logiska motsvarigheten till en *själv koppling*.

Kopplingar resulterar i en fullständig kors produkt av uppsättningarna som ingår i kopplingen. Resultatet av en N-Way-koppling är en uppsättning av N-element-tupler, där varje värde i tuppeln är associerat med den aliasad uppsättning som ingår i kopplingen och kan nås genom att referera till aliaset i andra satser.

## <a name="syntax"></a>Syntax

Språket stöder syntaxen `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>` . Den här frågan returnerar en uppsättning tupler med `N` värden. Varje tuppel har värden som skapas när alla containeralias itereras över sina respektive uppsättningar. 

Nu ska vi titta på följande FROM-sats: `<from_source1> JOIN <from_source2> JOIN ... JOIN <from_sourceN>`  
  
 Låt varje källa definiera `input_alias1, input_alias2, …, input_aliasN` . Denna FROM-sats returnerar en uppsättning av N-tupler (tupel med N värden). Varje tuppel har värden som skapas när alla containeralias itereras över sina respektive uppsättningar.  
  
**Exempel 1** – 2 Källor  
  
- Tillåt `<from_source1>` container-scope och representerar set {A, B, C}.  
  
- Gör `<from_source2>` det möjligt att referera till dokument med begränsad input_alias1 och representerar uppsättningar:  
  
    {1, 2} för `input_alias1 = A,`  
  
    {3} söker `input_alias1 = B,`  
  
    {4, 5} för `input_alias1 = C,`  
  
- FROM-satsen `<from_source1> JOIN <from_source2>` leder till följande tupler:  
  
    (`input_alias1, input_alias2`):  
  
    `(A, 1), (A, 2), (B, 3), (C, 4), (C, 5)`  
  
**Exempel 2** – 3 Källor  
  
- Tillåt `<from_source1>` container-scope och representerar set {A, B, C}.  
  
- Gör `<from_source2>` det möjligt att referera till dokument omfattning `input_alias1` och representera uppsättningar:  
  
    {1, 2} för `input_alias1 = A,`  
  
    {3} söker `input_alias1 = B,`  
  
    {4, 5} för `input_alias1 = C,`  
  
- Gör `<from_source3>` det möjligt att referera till dokument omfattning `input_alias2` och representera uppsättningar:  
  
    {100, 200} för `input_alias2 = 1,`  
  
    {300} söker `input_alias2 = 3,`  
  
- FROM-satsen `<from_source1> JOIN <from_source2> JOIN <from_source3>` leder till följande tupler:  
  
    (input_alias1, input_alias2, input_alias3):  
  
    (A, 1, 100), (A, 1, 200), (B, 3, 300)  
  
  > [!NOTE]
  > Avsaknad av tupler för andra värden för `input_alias1` , `input_alias2` , för vilka inga `<from_source3>` värden returnerades.  
  
**Exempel 3** – 3 Källor  
  
- Låt <from_source1> vara container-scope och representera set {A, B, C}.  
  
- Tillåt `<from_source1>` container-scope och representerar set {A, B, C}.  
  
- Låt <from_source2> vara dokumenterad referens input_alias1 och representera uppsättningar:  
  
    {1, 2} för `input_alias1 = A,`  
  
    {3} söker `input_alias1 = B,`  
  
    {4, 5} för `input_alias1 = C,`  
  
- Ge `<from_source3>` omfång till `input_alias1` och representerar uppsättningar:  
  
    {100, 200} för `input_alias2 = A,`  
  
    {300} söker `input_alias2 = C,`  
  
- FROM-satsen `<from_source1> JOIN <from_source2> JOIN <from_source3>` leder till följande tupler:  
  
    (`input_alias1, input_alias2, input_alias3`):  
  
    (A, 1, 100), (A, 1, 200), (A, 2, 100), (A, 2, 200), (C, 4, 300), (C, 5, 300)  
  
  > [!NOTE]
  > Detta resulterade i mellanprodukter mellan `<from_source2>` och `<from_source3>` eftersom båda är begränsade till samma `<from_source1>` .  Detta resulterade i 4 (2x2) tupler med värdet A, 0 tupler med värde B (1x0) och 2 (2x1) tupler med värdet C.  
  
## <a name="examples"></a>Exempel

I följande exempel visas hur JOIN-satsen fungerar. Innan du kör de här exemplen laddar du upp exempel [familjens data](sql-query-getting-started.md#upload-sample-data). I följande exempel är resultatet tomt, eftersom kryss produkten för varje objekt från källa och en tom uppsättning är tom:

```sql
    SELECT f.id
    FROM Families f
    JOIN f.NonExistent
```

Resultatet är:

```json
    [{
    }]
```

I följande exempel är kopplingen en mellan produkt mellan två JSON-objekt, objekt roten `id` och under `children` roten. Det faktum att `children` en matris inte är effektiv i kopplingen, eftersom den behandlar en enda rot som är `children` matrisen. Resultatet innehåller bara två resultat eftersom kryss produkten för varje objekt med matrisen ger exakt bara ett objekt.

```sql
    SELECT f.id
    FROM Families f
    JOIN f.children
```

Resultatet är:

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

I följande exempel visas en mer konventionell koppling:

```sql
    SELECT f.id
    FROM Families f
    JOIN c IN f.children
```

Resultatet är:

```json
    [
      {
        "id": "AndersenFamily"
      },
      {
        "id": "WakefieldFamily"
      },
      {
        "id": "WakefieldFamily"
      }
    ]
```

FROM-källa för JOIN-satsen är en iterator. Därför är flödet i föregående exempel:  

1. Expandera varje underordnat element `c` i matrisen.
2. Använd en kors produkt med roten för objektet `f` med varje underordnat element `c` som det första steget är utplattat.
3. Till sist projicerar du rot objekts `f` `id` egenskapen endast.

Det första objektet, `AndersenFamily` , innehåller endast ett `children` -element, så resultat uppsättningen innehåller bara ett enda objekt. Det andra objektet, `WakefieldFamily` , som innehåller två `children` , så att kors produkten genererar två objekt, ett för varje `children` element. Rotfälten i båda dessa objekt är detsamma, vilket är det som förväntas i en kryssprodukt.

Det verkliga verktyget i JOIN-satsen är att forma tupler från kors produkten i en form som annars är svår att projicera. I exemplet nedan filtreras kombinationen av en tupel som gör det möjligt för användaren att välja ett villkor som är uppfyllt av tupelarna generellt.

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
```

Resultatet är:

```json
    [
      {
        "familyName": "AndersenFamily",
        "childFirstName": "Henriette Thaulow",
        "petName": "Fluffy"
      },
      {
        "familyName": "WakefieldFamily",
        "childGivenName": "Jesse",
        "petName": "Goofy"
      }, 
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

Följande tillägg i föregående exempel utför en dubbel koppling. Du kan visa kors produkten som följande pseudo-kod:

```
    for-each(Family f in Families)
    {
        for-each(Child c in f.children)
        {
            for-each(Pet p in c.pets)
            {
                return (Tuple(f.id AS familyName,
                  c.givenName AS childGivenName,
                  c.firstName AS childFirstName,
                  p.givenName AS petName));
            }
        }
    }
```

`AndersenFamily` har en underordnad som har ett hus djur, så att kors produkten ger en rad (1 1 \* \* ) från den här familjen. `WakefieldFamily` har två underordnade, endast en av som har hus djur, men att barnet har två hus djur. Den här seriens kors produkt ger 1 \* 1 \* 2 = 2 rader.

I nästa exempel finns ett ytterligare filter på `pet` , som utesluter alla tupler där PET-namnet inte är det `Shadow` . Du kan bygga tupler från matriser, filtrera efter alla element i tuppeln och projicera en kombination av elementen.

```sql
    SELECT 
        f.id AS familyName,
        c.givenName AS childGivenName,
        c.firstName AS childFirstName,
        p.givenName AS petName
    FROM Families f
    JOIN c IN f.children
    JOIN p IN c.pets
    WHERE p.givenName = "Shadow"
```

Resultatet är:

```json
    [
      {
       "familyName": "WakefieldFamily",
       "childGivenName": "Jesse",
       "petName": "Shadow"
      }
    ]
```

Om frågan har en koppling och filter kan du skriva om en del av frågan som en under [fråga](sql-query-subquery.md#optimize-join-expressions) för att förbättra prestandan.

## <a name="next-steps"></a>Nästa steg

- [Komma igång](sql-query-getting-started.md)
- [Azure Cosmos DB .NET-exempel](https://github.com/Azure/azure-cosmosdb-dotnet)
- [Underfrågor](sql-query-subquery.md)