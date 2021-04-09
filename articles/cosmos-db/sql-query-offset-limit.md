---
title: OFFSET LIMIT-sats i Azure Cosmos DB
description: Lär dig hur du använder OFFSET LIMIT-satsen för att hoppa över och ta vissa värden när du frågar i Azure Cosmos DB
author: timsander1
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/29/2020
ms.author: tisande
ms.openlocfilehash: 459bd8511577067766cf488f53df57c1dc33fad1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "93338310"
---
# <a name="offset-limit-clause-in-azure-cosmos-db"></a>OFFSET LIMIT-sats i Azure Cosmos DB
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

OFFSET LIMIT-satsen är en optional-sats att hoppa över och sedan ta ett visst antal värden från frågan. Antalet OFFSET och LIMIT-antalet krävs i OFFSET LIMIT-satsen.

När OFFSET-gränsen används tillsammans med en ORDER BY-sats skapas resultat uppsättningen genom att hoppa över och ta på de beställda värdena. Om ingen ORDER BY-sats används kommer den att resultera i en deterministisk ordning med värden.

## <a name="syntax"></a>Syntax
  
```sql  
OFFSET <offset_amount> LIMIT <limit_amount>
```  
  
## <a name="arguments"></a>Argument

- `<offset_amount>`

   Anger det heltals antal objekt som frågeresultaten ska hoppa över.

- `<limit_amount>`
  
   Anger det heltals antal objekt som frågeresultaten ska innehålla

## <a name="remarks"></a>Kommentarer
  
  Både `OFFSET` Count och `LIMIT` Count krävs i- `OFFSET LIMIT` satsen. Om en valfri `ORDER BY` sats används skapas resultat uppsättningen genom att hoppa över de beställda värdena. Annars returnerar frågan en fast ordning med värden.

  Avgiften för en fråga med `OFFSET LIMIT` ökar i takt med att antalet förskjutningar ökar. För frågor som har [flera resultat sidor](sql-query-pagination.md)rekommenderar vi vanligt vis att du använder [fortsättnings-token](sql-query-pagination.md#continuation-tokens). Fortsättnings-token är ett "bok märke" för platsen där frågan senare kan återupptas. `OFFSET LIMIT`Det finns inget bok märke om du använder det. Om du vill returnera nästa sida för frågan måste du starta från början.
  
  Du bör använda `OFFSET LIMIT` för fall när du vill hoppa över objekt helt och spara klient resurser. Du bör till exempel använda `OFFSET LIMIT` om du vill hoppa över till 1000th-frågeresultaten och inte behöver visa resultat 1 till 999. På Server delen `OFFSET LIMIT` laddar fortfarande varje objekt, inklusive de som hoppas över. Prestanda fördelarna är besparingar i klient resurser genom att undvika bearbetnings objekt som inte behövs.

## <a name="examples"></a>Exempel

Här är ett exempel på en fråga som hoppar över det första värdet och returnerar det andra värdet (i den inhemska ortens namn):

```sql
    SELECT f.id, f.address.city
    FROM Families f
    ORDER BY f.address.city
    OFFSET 1 LIMIT 1
```

Resultatet är:

```json
    [
      {
        "id": "AndersenFamily",
        "city": "Seattle"
      }
    ]
```

Här är en fråga som hoppar över det första värdet och returnerar det andra värdet (utan ordning):

```sql
    SELECT f.id, f.address.city
    FROM Families f
    OFFSET 1 LIMIT 1
```

Resultatet är:

```json
    [
      {
        "id": "WakefieldFamily",
        "city": "Seattle"
      }
    ]
```

## <a name="next-steps"></a>Nästa steg

- [Komma igång](sql-query-getting-started.md)
- [SELECT-sats](sql-query-select.md)
- [ORDER BY-sats](sql-query-order-by.md)
