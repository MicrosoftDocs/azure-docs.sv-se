---
title: Kolumn tabell Storage Preview – storskalig (citus)-Azure Database for PostgreSQL
description: Komprimera data med hjälp av Column Storage (för hands version)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: c60c398d49a802dbe051ca37c4aea2092ab5144b
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107024004"
---
# <a name="columnar-table-storage-preview"></a>Kolumn tabells lagring (för hands version)

> [!IMPORTANT]
> Kolumn tabells lagring i storskalig (citus) är för närvarande en för hands version. Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
>
> Du kan se en fullständig lista över andra nya funktioner i [för hands versions funktioner för skalning (citus)](hyperscale-preview-features.md).

Azure Database for PostgreSQL-storskalig (citus) stöder tillägg för kolumner med enbart kolumn lagring för analys-och data lager arbets belastningar. När kolumner (i stället för rader) lagras sammanhängande på disk blir data mer komprimerbara, och frågor kan begära en delmängd av kolumnerna snabbare.

Om du vill använda kolumn lagring anger du `USING columnar` när du skapar en tabell:

```postgresql
CREATE TABLE contestant (
    handle TEXT,
    birthdate DATE,
    rating INT,
    percentile FLOAT,
    country CHAR(3),
    achievements TEXT[]
) USING columnar;
```

Citus (storskalig) konverterar rader till kolumn lagring i "ränder" under infogning. Varje rand innehåller en transaktions värde, eller 150000 rader, beroende på vilket som är mindre.  (Stripe-storlek och andra parametrar för en kolumn tabell kan ändras med funktionen [alter_columnar_table_set](reference-hyperscale-functions.md#alter_columnar_table_set) .)

Följande uttryck placerar till exempel alla fem raderna i samma rand, eftersom alla värden infogas i en enda transaktion:

```postgresql
-- insert these values into a single columnar stripe

INSERT INTO contestant VALUES
  ('a','1990-01-10',2090,97.1,'XA','{a}'),
  ('b','1990-11-01',2203,98.1,'XA','{a,b}'),
  ('c','1988-11-01',2907,99.4,'XB','{w,y}'),
  ('d','1985-05-05',2314,98.3,'XB','{}'),
  ('e','1995-05-05',2236,98.2,'XC','{a}');
```

Det är bäst att göra stora ränder när det är möjligt, eftersom citus (storskalig) komprimerar kolumn data separat per rand. Vi kan se fakta om vår tabell tabell som komprimerings frekvens, antal ränder och genomsnitts rader per rand genom att använda `VACUUM VERBOSE` :

```postgresql
VACUUM VERBOSE contestant;
```
```
INFO:  statistics for "contestant":
storage id: 10000000000
total file size: 24576, total data size: 248
compression rate: 1.31x
total row count: 5, stripe count: 1, average rows per stripe: 5
chunk count: 6, containing data for dropped columns: 0, zstd compressed: 6
```

Utdata visar att citus (zstd Compression) använder Compression-algoritmen för att hämta 1.31 x-datakomprimering. Komprimerings frekvensen jämför a) storleken på infogade data som den mellanlagrades i minnet mot b) storleken på data som komprimerats i den eventuella randen.

På grund av hur den mäts kan komprimerings takten eller kanske inte matcha storleks skillnaden mellan rad-och kolumn lagring för en tabell. Det enda sättet att verkligen hitta skillnaden är att skapa en rad-och kolumn tabell som innehåller samma data och jämföra:

```postgresql
CREATE TABLE contestant_row AS
    SELECT * FROM contestant;

SELECT pg_total_relation_size('contestant_row') as row_size,
       pg_total_relation_size('contestant') as columnar_size;
```
```
 row_size | columnar_size
----------+---------------
    16384 |         24576
```

För vår små tabell använder kolumn lagringen mer utrymme, men när data växer kommer komprimeringen att vinna.

## <a name="example"></a>Exempel

Kolumn lagring fungerar bra med tabell partitionering. Ett exempel finns i Community-dokumentationen för citus Engine, [arkivera med kolumn lagring](https://docs.citusdata.com/en/stable/use_cases/timeseries.html#archiving-with-columnar-storage).

## <a name="gotchas"></a>Information

* Kolumn lagringen komprimeras per rand. Stripes skapas per transaktion, så infoga en rad per transaktion för att placera enskilda rader i sina egna ränder. Komprimering och prestanda för enstaka rad ränder blir sämre än en rad tabell. Infoga alltid i bulk i en kolumn tabell.
* Om du tar upp och columnarize en massa små ränder är du fastnad.
  Den enda korrigeringen är att skapa en ny kolumn tabell och kopiera data från originalet i en transaktion:
  ```postgresql
  BEGIN;
  CREATE TABLE foo_compacted (LIKE foo) USING columnar;
  INSERT INTO foo_compacted SELECT * FROM foo;
  DROP TABLE foo;
  ALTER TABLE foo_compacted RENAME TO foo;
  COMMIT;
  ```
* Grundläggande icke-komprimerbara data kan vara ett problem, men kolumn lagring är fortfarande användbart när du väljer vissa kolumner. De andra kolumnerna behöver inte läsas in i minnet.
* På en partitionerad tabell med en blandning av rad-och kolumn-partitioner måste uppdateringar vara noggrant riktade. Filtrera dem så att de bara träffar på raddata.
   * Om åtgärden är riktad mot en specifik rad partition (t. ex. `UPDATE p2 SET i = i + 1` ) lyckas den, om den är riktad mot en angiven kolumn partition (till exempel `UPDATE p1 SET i = i + 1` ), kommer den att Miss lyckas.
   * Om åtgärden är riktad mot den partitionerade tabellen och har en WHERE-sats som undantar alla kolumn partitioner (till exempel `UPDATE parent SET i = i + 1 WHERE timestamp = '2020-03-15'` ), kommer den att lyckas.
   * Om åtgärden är riktad mot den partitionerade tabellen, men inte filtreras i kolumnerna för partitionsnyckel, kommer den att Miss förväntas. Även om det finns WHERE-satser som matchar rader i endast kolumn partitioner, är det inte tillräckligt –-partitionsnyckel måste också filtreras.

## <a name="limitations"></a>Begränsningar

Den här funktionen har fortfarande ett antal betydande begränsningar. Se [gränser och begränsningar för storskalighet (citus)](concepts-hyperscale-limits.md#columnar-storage).

## <a name="next-steps"></a>Nästa steg

* Se ett exempel på kolumn lagring i en citus [timeseries-självstudie](https://docs.citusdata.com/en/stable/use_cases/timeseries.html#archiving-with-columnar-storage) (extern länk).
