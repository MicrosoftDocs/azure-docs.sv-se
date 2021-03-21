---
title: HC-serien – Azure Virtual Machines
description: Specifikationer för virtuella datorer i HC-serien.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 03/05/2021
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 6ec629629fc774ddb5423db91fe0d71a49305ca1
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102566047"
---
# <a name="hc-series"></a>HC-serien

Virtuella datorer i HC-serien är optimerade för program som drivs av kompakt beräkning, till exempel implicit ändlig analys av element, molekyl Dynamics och beräknings kemi. Virtuella HC-funktioner 44 Intel Xeon platina 8168 processor kärnor, 8 GB RAM-minne per CPU-kärna och ingen hyperthreading. Intel Xeon platina-plattformen stöder Intels omfattande eko system med program varu verktyg som Intel Math kernel-bibliotek och avancerade funktioner för vektor bearbetning, till exempel AVX-512.

Virtuella datorer i HC-serien 100 GB/SEK Mellanox EDR InfiniBand. De här virtuella datorerna är anslutna i ett icke-blockerande fat-träd för optimerad och konsekvent RDMA-prestanda. De här virtuella datorerna har stöd för anpassningsbar Routning och dynamisk ansluten transport (DCT, i tillägg till standard RC och UD transporter). Dessa funktioner förbättrar programmets prestanda, skalbarhet och konsekvens, och användningen rekommenderas. 

[ACU](acu.md): 297-315<br>
[Premium Storage](premium-storage-performance.md): stöds<br>
[Premium Storage caching](premium-storage-performance.md): stöds<br>
[Direktmigrering](maintenance-and-updates.md): stöds inte<br>
[Minnes bebetjänings uppdateringar](maintenance-and-updates.md): stöds inte<br>
[Stöd för VM-generering](generation-2.md): generation 1 och 2<br>
[Accelererat nätverk](../virtual-network/create-vm-accelerated-networking-cli.md): stöds ([Läs mer](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-hbv2-and-ndv2/ba-p/2067965) om prestanda och potentiella problem)<br>
[Tillfälliga OS-diskar](ephemeral-os-disks.md): stöds <br>

<br>

| Storlek | Virtuell processor | Processor | Minne (GiB) | Minnes bandbredd GB/s | Bas processor frekvens (GHz) | Frekvens för alla kärnor (GHz, högsta) | Frekvens för enkla kärnor (GHz, hög) | RDMA-prestanda (GB/s) | MPI-stöd | Temp-lagring (GiB) | Maximalt antal datadiskar | Högsta Ethernet-virtuella nätverkskort |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HC44rs | 44 | Intel Xeon platina 8168 | 352 | 191 | 2.7 | 3.4 | 3.7 | 100 | Alla | 700 | 4 | 8 |

Läs mer om den underliggande [arkitekturen, VM-topologin](./workloads/hpc/hc-series-overview.md) och förväntade [prestanda](./workloads/hpc/hc-series-performance.md) för den virtuella HC-serien.

[!INCLUDE [hpc-include](./workloads/hpc/includes/hpc-include.md)]

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes"></a>Andra storlekar

- [Generell användning](sizes-general.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare generationer](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

- Läs om de senaste meddelandena, HPC-arbetsbelastnings exempel och prestanda resultat på [Azure Compute Tech-community Bloggar](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- En övergripande arkitektur för att köra HPC-arbetsbelastningar finns i [HPC (data behandling med höga prestanda) i Azure](/azure/architecture/topics/high-performance-computing/).
- Lär dig mer om hur [Azure Compute Units (ACU)](acu.md) kan hjälpa dig att jämföra beräknings prestanda i Azure SKU: er.
