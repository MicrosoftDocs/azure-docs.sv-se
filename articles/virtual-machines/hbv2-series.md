---
title: HBv2-serien – Azure Virtual Machines
description: Specifikationer för virtuella datorer i HBv2-serien.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 03/08/2021
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: 25f0933c2d0b8b3c8ec227ce52c974a50a671043
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309738"
---
# <a name="hbv2-series"></a>HBv2-serien

Virtuella datorer i HBv2-serien är optimerade för program som drivs av minnes bandbredd, till exempel flytande dynamik, analys av ändliga element och lagrings simulering. HBv2 VM Feature 120 AMD EPYC 7742 processor kärnor, 4 GB RAM per CPU-kärna och ingen samtidig multitrådning. Varje HBv2 VM ger upp till 340 GB/s minnes bandbredd och upp till 4 teraFLOPS FP64-beräkning.

HBv2-serie VM-funktioner 200 GB/SEK Mellanox HDR InfiniBand. De här virtuella datorerna är anslutna i ett icke-blockerande fat-träd för optimerad och konsekvent RDMA-prestanda. De här virtuella datorerna har stöd för anpassningsbar Routning och dynamisk ansluten transport (DCT, förutom standard RC-och UD-transporter). Dessa funktioner förbättrar programmets prestanda, skalbarhet och konsekvens, och användningen rekommenderas.

[Premium Storage](premium-storage-performance.md): stöds<br>
[Premium Storage caching](premium-storage-performance.md): stöds<br>
[Ultra disks](disks-types.md#ultra-disk): stöds ([Lär dig mer](https://techcommunity.microsoft.com/t5/azure-compute/ultra-disk-storage-for-hpc-and-gpu-vms/ba-p/2189312) om tillgänglighet, användning och prestanda) <br>
[Direktmigrering](maintenance-and-updates.md): stöds inte<br>
[Minnes bebetjänings uppdateringar](maintenance-and-updates.md): stöds inte<br>
[Stöd för VM-generering](generation-2.md): generation 1 och 2<br>
[Accelererat nätverk](../virtual-network/create-vm-accelerated-networking-cli.md): stöds ([Läs mer](https://techcommunity.microsoft.com/t5/azure-compute/accelerated-networking-on-hb-hc-hbv2-and-ndv2/ba-p/2067965) om prestanda och potentiella problem) <br>
[Tillfälliga OS-diskar](ephemeral-os-disks.md): stöds inte <br>
<br>

| Storlek | Virtuell processor | Processor | Minne (GiB) | Minnes bandbredd GB/s | Bas processor frekvens (GHz) | Frekvens för alla kärnor (GHz, högsta) | Frekvens för enkla kärnor (GHz, hög) | RDMA-prestanda (GB/s) | MPI-stöd | Temp-lagring (GiB) | Maximalt antal datadiskar | Högsta Ethernet-virtuella nätverkskort |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_HB120rs_v2 | 120 | AMD EPYC-7V12 | 456 | 350 | 2.45 | 3.1 | 3.3 | 200 | Alla | 480 + 960 | 8 | 8 |

Läs mer om:
- [Arkitektur och topologi för virtuella datorer](./workloads/hpc/hbv2-series-overview.md)
- [Program varu stack](./workloads/hpc/hbv2-series-overview.md#software-specifications) som stöds, inklusive operativ system som stöds
- Förväntad [prestanda](./workloads/hpc/hbv2-performance.md) för den virtuella datorn med HBv2-serien

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

- Läs om de senaste meddelandena, HPC-arbetsbelastnings exempel och prestanda resultat på [Azure Compute Tech-Webbgruppens Bloggar](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- En arkitektur för högre nivå för att köra HPC-arbetsbelastningar finns i [HPC (data behandling med höga prestanda) i Azure](/azure/architecture/topics/high-performance-computing/).
- Lär dig mer om hur [Azure Compute Units (ACU)](acu.md) kan hjälpa dig att jämföra beräknings prestanda i Azure SKU: er.
