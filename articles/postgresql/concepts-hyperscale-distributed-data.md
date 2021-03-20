---
title: Distribuerade data – storskalig (citus) – Azure Database for PostgreSQL
description: Lär dig mer om distribuerade tabeller, referens tabeller, lokala tabeller och Shards i Azure Database for PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 7757fdb4953640597a805c3d74a9e1ef08ef2c07
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86114507"
---
# <a name="distributed-data-in-azure-database-for-postgresql--hyperscale-citus"></a>Distribuerade data i Azure Database for PostgreSQL – storskalig (citus)

Den här artikeln beskriver de tre tabell typerna i Azure Database for PostgreSQL – storskalig (citus).
Det visar hur distribuerade tabeller lagras som Shards och hur Shards placeras på noder.

## <a name="table-types"></a>Tabelltyper

Det finns tre typer av tabeller i en storskalig (citus) Server grupp som används för olika syfte.

### <a name="type-1-distributed-tables"></a>Typ 1: distribuerade tabeller

Den första typen och vanligaste är distribuerade tabeller. De verkar vara normala tabeller i SQL-uttryck, men de är vågrätt partitionerade mellan arbetsnoder. Det innebär att raderna i tabellen lagras på olika noder i fragment tabeller som kallas Shards.

Storskalig (citus) kör inte bara SQL-och DDL-instruktioner i ett kluster.
Ändra schemat för en distribuerad tabell sammanhängande för att uppdatera alla tabellens Shards över arbets tagarna.

#### <a name="distribution-column"></a>Distributions kolumn

Storskalig (citus) använder algoritmisk horisontell partitionering för att tilldela rader till Shards. Tilldelningen görs deterministiskt baserat på värdet för en tabell kolumn som kallas för distributions kolumnen. Kluster administratören måste ange den här kolumnen när du distribuerar en tabell.
Att välja rätt är viktigt för prestanda och funktionalitet.

### <a name="type-2-reference-tables"></a>Typ 2: referens tabeller

En referens tabell är en typ av distribuerad tabell vars hela innehåll är koncentrerat till en enda Shard. Shard replikeras på varje arbets tagare. Frågor för alla anställda kan komma åt referensinformationen lokalt utan att det går att begära rader från en annan nod. Referens tabeller har ingen distributions kolumn eftersom det inte finns något behov av att skilja separata Shards per rad.

Referens tabeller är vanligt vis små och används för att lagra data som är relevanta för frågor som körs på alla Worker-noder. Ett exempel är uppräknade värden som order status eller produkt kategorier.

### <a name="type-3-local-tables"></a>Typ 3: lokala tabeller

När du använder citus (storskalig) är koordinator-noden som du ansluter till en vanlig PostgreSQL-databas. Du kan skapa vanliga tabeller på koordinatorn och välja att inte Shard dem.

En bra kandidat för lokala tabeller är små administrativa tabeller som inte ingår i kopplings frågor. Ett exempel är en användar tabell för program inloggning och autentisering.

## <a name="shards"></a>Shards

I föregående avsnitt beskrivs hur distribuerade tabeller lagras som Shards på arbetsnoder. I det här avsnittet beskrivs mer teknisk information.

`pg_dist_shard`Tabellen metadata i koordinatorn innehåller en rad för varje Shard för varje distribuerad tabell i systemet. Raden matchar ett Shard-ID med ett heltals intervall i ett hash-utrymme (shardminvalue, shardmaxvalue).

```sql
SELECT * from pg_dist_shard;
 logicalrelid  | shardid | shardstorage | shardminvalue | shardmaxvalue
---------------+---------+--------------+---------------+---------------
 github_events |  102026 | t            | 268435456     | 402653183
 github_events |  102027 | t            | 402653184     | 536870911
 github_events |  102028 | t            | 536870912     | 671088639
 github_events |  102029 | t            | 671088640     | 805306367
 (4 rows)
```

Om koordinator-noden vill avgöra vilken Shard som innehåller en rad med `github_events` hash-värden, hashas värdet för distributions kolumnen på raden. Noden kontrollerar sedan vilket Shard \' s-intervall som innehåller det hashade värdet. Intervallen definieras så att avbildningen av hash-funktionen är sin osammanhängande Union.

### <a name="shard-placements"></a>Shard placeringar

Anta att Shard 102027 är associerat med raden i fråga. Raden läses eller skrivs i en tabell som kallas för `github_events_102027` en av arbets tagarna. Vilken arbetare? Detta fastställs helt av metadata-tabellerna. Mappningen av Shard till Worker kallas för Shard-placering.

Koordinator-noden skriver om frågor till fragment som refererar till de olika tabellerna som `github_events_102027` och kör fragmenten på lämpliga arbetare. Här är ett exempel på en fråga som körs bakom kulisserna för att hitta noden som innehåller Shard-ID 102027.

```sql
SELECT
    shardid,
    node.nodename,
    node.nodeport
FROM pg_dist_placement placement
JOIN pg_dist_node node
  ON placement.groupid = node.groupid
 AND node.noderole = 'primary'::noderole
WHERE shardid = 102027;
```

```output
┌─────────┬───────────┬──────────┐
│ shardid │ nodename  │ nodeport │
├─────────┼───────────┼──────────┤
│  102027 │ localhost │     5433 │
└─────────┴───────────┴──────────┘
```

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [väljer en distributions kolumn](concepts-hyperscale-choose-distribution-column.md) för distribuerade tabeller.
