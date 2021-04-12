---
title: Skalnings Server grupp – storskalig (citus)-Azure Database for PostgreSQL
description: Justera minne, disk och processor resurser i Server gruppen för att hantera ökad belastning
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 04/07/2021
ms.openlocfilehash: 905224119b9df4e4003b1736443406e9548a49e3
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012537"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Skala en citus-Server grupp

Azure Database for PostgreSQL-Scale (citus) tillhandahåller självbetjänings skalning för att hantera ökad belastning. Azure Portal gör det enkelt att lägga till nya arbetsnoder och öka virtuella kärnor för befintliga noder. Att lägga till noder medför ingen stillestånds tid, och till och med att flytta Shards till de nya noderna (kallas [Shard balansering](howto-hyperscale-scale-rebalance.md)) utan att avbryta frågor.

## <a name="add-worker-nodes"></a>Lägg till arbetsnoder

Om du vill lägga till noder går du till fliken **Compute + Storage** i Server gruppen för citus-servern.  Om du drar skjutreglaget för **antal arbets noder** ändras värdet.

> [!NOTE]
>
> En storskalig (citus)-Server grupp som skapats med [Basic-nivån (för hands version)](concepts-hyperscale-tiers.md) har inga arbetare. Om antalet arbets trådar ökar ökar server gruppen automatiskt till standard nivån.
> När du har graderat en Server grupp till standard nivån kan du inte nedgradera tillbaka till Basic-nivån.

:::image type="content" source="./media/howto-hyperscale-scaling/01-sliders-workers.png" alt-text="Resurs skjutreglage":::

Klicka på knappen **Spara** om du vill att det ändrade värdet ska börja gälla.

> [!NOTE]
> När du har ökat och sparat det går det inte att minska antalet arbetsnoder med skjutreglaget.

> [!NOTE]
> Om du vill dra nytta av nyligen tillagda noder måste du [balansera om Distributed Table Shards](howto-hyperscale-scale-rebalance.md), vilket innebär att vissa [Shards](concepts-hyperscale-distributed-data.md#shards) flyttas från befintliga noder till de nya.

## <a name="increase-or-decrease-vcores-on-nodes"></a>Öka eller minska virtuella kärnor på noder

Förutom att lägga till nya noder kan du öka funktionerna i befintliga noder. Det kan vara bra att justera beräknings kapaciteten för prestanda experiment, samt kortsiktiga eller långsiktiga ändringar av trafik kraven.

Om du vill ändra virtuella kärnor för alla arbetsnoder justerar du skjutreglaget **virtuella kärnor** under **konfiguration (per nod)**. Koordinator nodens virtuella kärnor kan justeras oberoende av varandra. Justera **virtuella kärnor** -skjutreglaget under  **konfiguration (koordinator nod)**.

## <a name="next-steps"></a>Nästa steg

- Läs mer om Server gruppens [prestanda alternativ](concepts-hyperscale-configuration-options.md).
- [Balansera om Distributed Table Shards](howto-hyperscale-scale-rebalance.md) så att alla arbetsnoder kan delta i parallella frågor
