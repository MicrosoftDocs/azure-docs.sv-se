---
title: Balansera om Shards-Scale (citus) – Azure Database for PostgreSQL
description: Distribuera Shards jämnt mellan servrar för bättre prestanda
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: b69c4fc3ff16cf30181a3529f61af7126b92950b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "95026394"
---
# <a name="rebalance-shards-in-hyperscale-citus-server-group"></a>Balansera om Shards i citus ()-Server grupp

Om du vill dra nytta av nyligen tillagda noder måste du balansera om Distributed Table [Shards](concepts-hyperscale-distributed-data.md#shards), vilket innebär att vissa Shards flyttas från befintliga noder till de nya. Kontrol lera först att de nya arbets tagarna har slutfört etableringen. Starta sedan Shard-ombalanseringen genom att ansluta till noden kluster koordinatorer med psql och köra:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

Funktionen [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) balanserar om alla tabeller i den [samplacerings](concepts-hyperscale-colocation.md) gruppen i tabellen med namnet i argumentet. Därför behöver du inte anropa funktionen för varje distribuerad tabell. anropa den bara på en representativ tabell från varje samplacerings grupp.

**Nästa steg**


- Läs mer om Server gruppens [prestanda alternativ](concepts-hyperscale-configuration-options.md).
- Skala upp eller ut [en Server grupp](howto-hyperscale-scale-grow.md)
- Se [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) Reference material
