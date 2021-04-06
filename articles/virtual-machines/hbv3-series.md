---
title: HBv3-serien – Azure Virtual Machines
description: Specifikationer för virtuella datorer i HBv3-serien.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.date: 03/12/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: a03eac3969e8918c8da20b90d0dcf8b60b39b8ee
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104800847"
---
# <a name="hbv3-series"></a>HBv3-serien

Virtuella datorer i HBv3-serien är optimerade för HPC-program, till exempel flytande dynamik, explicita och implicita element analyser, väder modellering, seismisk-bearbetning, simulering av behållare och hö-simulering. HBv3 VM-funktionen har upp till 120 AMD EPYC™ 7003-seriens (Milano) processor kärnor, 448 GB RAM och ingen hyperthreading. Virtuella datorer i HBv3-serien ger också 350 GB/s minnes bandbredd, upp till 32 MB L3-cache per kärna, upp till 7 GB/s av block enhet SSD-prestanda och klock frekvens upp till 3,675 GHz. 

Alla VM-funktioner i HBv3 200 GB/SEK HDR InfiniBand från NVIDIA-nätverk för att aktivera superdator MPI-arbetsbelastningar. De här virtuella datorerna är anslutna i ett icke-blockerande fat-träd för optimerad och konsekvent RDMA-prestanda. HDR InfiniBand-infrastrukturen har även stöd för anpassningsbar Routning och dynamisk ansluten transport (DCT, i tillägg till standard RC och UD transporter). Dessa funktioner förbättrar programmets prestanda, skalbarhet och konsekvens, och användningen rekommenderas starkt.

[Premium Storage](premium-storage-performance.md): stöds<br>
[Premium Storage caching](premium-storage-performance.md): stöds<br>
[Direktmigrering](maintenance-and-updates.md): stöds inte<br>
[Minnes bebetjänings uppdateringar](maintenance-and-updates.md): stöds inte<br>
[Stöd för VM-generering](generation-2.md): generation 1 och 2<br>
[Accelererat nätverk](../virtual-network/create-vm-accelerated-networking-cli.md): kommer snart<br>
[Tillfälliga OS-diskar](ephemeral-os-disks.md): stöds inte <br>
<br>

|Storlek |Virtuell processor |Processor |Minne (GiB) |Minnes bandbredd GB/s |Bas processor frekvens (GHz) |Frekvens för alla kärnor (GHz, högsta) |Frekvens för enkla kärnor (GHz, hög) |RDMA-prestanda (GB/s) |MPI-stöd |Temp-lagring (GiB) |Maximalt antal datadiskar |Högsta Ethernet-virtuella nätverkskort |
|----|----|----|----|----|----|----|----|----|----|----|----|----|
|Standard_HB120rs_v3    |120 |AMD EPYC-7V13 |448 |350 |2.45 |3.1 |3,675 |200 |Alla |2 * 960 |32 |8 |
|Standard_HB120-96rs_v3 |96  |AMD EPYC-7V13 |448 |350 |2.45 |3.1 |3,675 |200 |Alla |2 * 960 |32 |8 |
|Standard_HB120-64rs_v3 |64  |AMD EPYC-7V13 |448 |350 |2.45 |3.1 |3,675 |200 |Alla |2 * 960 |32 |8 |
|Standard_HB120-32rs_v3 |32  |AMD EPYC-7V13 |448 |350 |2.45 |3.1 |3,675 |200 |Alla |2 * 960 |32 |8 |
|Standard_HB120-16rs_v3 |16  |AMD EPYC-7V13 |448 |350 |2.45 |3.1 |3,675 |200 |Alla |2 * 960 |32 |8 |

Läs mer om:
- [Arkitektur och topologi för virtuella datorer](./workloads/hpc/hbv3-series-overview.md)
- [Program varu stack](./workloads/hpc/hbv3-series-overview.md#software-specifications) som stöds, inklusive operativ system som stöds
- Förväntad [prestanda](./workloads/hpc/hbv3-performance.md) för den virtuella datorn med HBv3-serien

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
