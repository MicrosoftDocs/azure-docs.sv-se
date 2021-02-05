---
title: ta med fil
description: ta med fil
services: virtual-machines
author: jonbeck7
ms.service: virtual-machines
ms.topic: include
ms.date: 03/09/2018
ms.author: azcspmt;jonbeck;cynthn
ms.custom: include file
ms.openlocfilehash: d610193783d44b86d48cd5ceaa91377b32b7061b
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/05/2021
ms.locfileid: "99580399"
---
<!-- Not used for Ls-series -->

## <a name="size-table-definitions"></a>Definitioner för storlekstabellen

- Lagringskapaciteten visas i GiB, eller 1 024^3 byte. När du jämför diskar som mäts i GB (1000 ^ 3 byte) till diskar som mäts i GiB (1024 ^ 3) kom ihåg att de kapacitets nummer som anges i GiB kan vara mindre. Till exempel 1023 GiB = 1098,4 GB.
- Diskgenomflödet mäts i indata-/utdataåtgärder per sekund (IOPS) och Mbit/s där Mbit/s = 10^6 byte/sek.
- Datadiskar kan köras i cachelagrat eller icke cachelagrat läge. För diskåtgärder med cachelagrade data anges cacheläget till **ReadOnly** eller **ReadWrite**.  För diskåtgärder med icke cachelagrade data anges cacheläget till **Inget**.
- Information om hur du får bästa möjliga lagrings prestanda för dina virtuella datorer finns i [virtuell dator och disk prestanda](../articles/virtual-machines/disks-performance.md).
- **Förväntad nätverks bandbredd** är den högsta sammanlagda allokerade bandbredden per VM-typ i alla nätverkskort, för alla mål. Mer information finns i [bandbredd för virtuella dator nätverk](../articles/virtual-network/virtual-machine-network-throughput.md).

  Övre gränser är inte garanterade. Begränsningar ger vägledning för att välja rätt typ av virtuell dator för det avsedda programmet. Faktiska nätverks prestanda beror på flera faktorer, inklusive överbelastning av nätverket, program belastningar och nätverks inställningar. Information om hur du optimerar nätverks data flödet finns i [optimera nätverks data flöde för virtuella Azure-datorer](../articles/virtual-network/virtual-network-optimize-network-bandwidth.md). För att uppnå den förväntade nätverks prestandan på Linux eller Windows kan du behöva välja en enskild version eller optimera din virtuella dator. Mer information finns i [test för bandbredd/data flöde (NTTTCP)](../articles/virtual-network/virtual-network-bandwidth-testing.md).



