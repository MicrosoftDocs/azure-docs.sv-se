---
title: Fastställa tabell storlek – citus) – Azure Database for PostgreSQL
description: Så här hittar du den sanna storleken på distribuerade tabeller i en citus-Server (för skalning)
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 1/5/2021
ms.openlocfilehash: 6ebdbe250862ccd462259900ba56d007f002a52f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97937678"
---
# <a name="determine-table-and-relation-size"></a>Fastställa tabell-och Relations storlek

Det vanligaste sättet att hitta tabell storlekar i PostgreSQL, `pg_total_relation_size` vilket drastiskt under rapporterar storleken på distribuerade tabeller på skala (citus).
All den här funktionen i en citus-Server () är att visa storleken på tabeller på koordinator-noden.  I verkligheten finns data i distribuerade tabeller på arbetsnoderna (i Shards), inte på koordinatorn. Ett sant mått på den distribuerade tabell storleken erhålls som en summa av Shard-storlekar. Citus (storskalig) tillhandahåller hjälp funktioner för att fråga den här informationen.

<table>
<colgroup>
<col style="width: 40%" />
<col style="width: 59%" />
</colgroup>
<thead>
<tr class="header">
<th>Funktion</th>
<th>Returer</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td>citus_relation_size (relation_name)</td>
<td><ul>
<li>Storleken på faktiska data i tabellen ("<a href="https://www.postgresql.org/docs/current/static/storage-file-layout.html">huvud grenen</a>").</li>
<li>En relation kan vara namnet på en tabell eller ett index.</li>
</ul></td>
</tr>
<tr class="even">
<td>citus_table_size (relation_name)</td>
<td><ul>
<li><p>citus_relation_size plus:</p>
<blockquote>
<ul>
<li>storlek på <a href="https://www.postgresql.org/docs/current/static/storage-fsm.html">karta över ledigt utrymme</a></li>
<li>storlek på <a href="https://www.postgresql.org/docs/current/static/storage-vm.html">Synlighets karta</a></li>
</ul>
</blockquote></li>
</ul></td>
</tr>
<tr class="odd">
<td>citus_total_relation_size (relation_name)</td>
<td><ul>
<li><p>citus_table_size plus:</p>
<blockquote>
<ul>
<li>storlek på index</li>
</ul>
</blockquote></li>
</ul></td>
</tr>
</tbody>
</table>

Dessa funktioner är likvärdiga med tre av standard funktionerna för PostgreSQL [objekt storlek](https://www.postgresql.org/docs/current/static/functions-admin.html#FUNCTIONS-ADMIN-DBSIZE), förutom om de inte kan ansluta till en nod, så fel uppstår.

## <a name="example"></a>Exempel

Så här visar du storlekarna för alla distribuerade tabeller:

``` postgresql
SELECT logicalrelid AS name,
       pg_size_pretty(citus_table_size(logicalrelid)) AS size
  FROM pg_dist_partition;
```

Utdata:

```
┌───────────────┬───────┐
│     name      │ size  │
├───────────────┼───────┤
│ github_users  │ 39 MB │
│ github_events │ 37 MB │
└───────────────┴───────┘
```

## <a name="next-steps"></a>Nästa steg

* Lär dig hur du [skalar en Server grupp](howto-hyperscale-scale-grow.md) för att lagra mer data.
* Särskilja [tabell typer](concepts-hyperscale-nodes.md) i en citus-servergrupp (disscales).
