---
title: Balansera om Shards-Scale (citus) – Azure Database for PostgreSQL
description: Distribuera Shards jämnt mellan servrar för bättre prestanda
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 04/09/2021
ms.openlocfilehash: 63322fac4c6ad5b705deedcd8a80466ddd803814
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305709"
---
# <a name="rebalance-shards-in-hyperscale-citus-server-group"></a>Balansera om Shards i citus ()-Server grupp

Om du vill dra nytta av nyligen tillagda noder måste du balansera om Distributed Table [Shards](concepts-hyperscale-distributed-data.md#shards), vilket innebär att vissa Shards flyttas från befintliga noder till de nya.

## <a name="determine-if-the-server-group-needs-a-rebalance"></a>Ta reda på om Server gruppen behöver en balansering

Azure Portal kan visa om data distribueras jämnt mellan arbetsnoder i en Server grupp. Om du vill se den går du till sidan för **Shard-ombalanser** på menyn **Server grupp hantering** . Om data är sneda mellan arbets tagarna, ser du att **ombalanseringen av meddelanden är rekommenderad**, tillsammans med en lista över storleken på varje nod.

Om data redan är balanserade, så kommer du inte att kunna se meddelande **ombalanseringen just nu**.

## <a name="run-the-shard-rebalancer"></a>Kör Shard-ombalanseringen

För att starta Shard-ombalanseringen måste du ansluta till koordinator-noden i Server gruppen och köra [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) SQL-funktionen på distribuerade tabeller. Funktionen balanserar alla tabeller i den [samplacerings](concepts-hyperscale-colocation.md) gruppen i tabellen med namnet i argumentet. Därför behöver du inte anropa funktionen för varje distribuerad tabell. anropa den bara på en representativ tabell från varje samplacerings grupp.

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

## <a name="monitor-rebalance-progress"></a>Övervaka ombalanserings förlopp

Om du vill titta på ombalanseringen när du har startat den går du tillbaka till Azure Portal. Öppna sidan **Shard rebalancer** i **Server grupp hantering**. Det visar att **ombalanseringen** av meddelanden är igång tillsammans med två tabeller.

Den första tabellen visar antalet Shards som flyttas till eller från en nod, till exempel "6 av 24 flyttade i". I den andra tabellen visas förloppet per databas tabell: namn, Shard antal påverkade, data storlek som påverkas och ombalansering av status.

Klicka på knappen **Uppdatera** för att uppdatera sidan. När ombalanseringen är klar, kommer den återigen att **ombalansera för** tillfället.

## <a name="next-steps"></a>Nästa steg

- Läs mer om Server gruppens [prestanda alternativ](concepts-hyperscale-configuration-options.md).
- Skala upp eller ut [en Server grupp](howto-hyperscale-scale-grow.md)
- Se [rebalance_table_shards](reference-hyperscale-functions.md#rebalance_table_shards) Reference material
