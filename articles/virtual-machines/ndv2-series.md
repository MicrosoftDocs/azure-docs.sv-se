---
title: NDv2-serien
description: Specifikationer för virtuella datorer i NDv2-serien.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: f4031893ae5d45902f9d0ab2ccb1cf984724a69e
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102562256"
---
# <a name="updated-ndv2-series"></a>Uppdaterad NDv2-serien

Den virtuella datorn i NDv2-serien är ett nytt tillägg till GPU-familjen som är utformad för att behöva de mest krävande GPU: n för GPU-accelererad GPU, maskin inlärning, simulering och HPC-arbetsbelastningar.

NDv2 drivs av 8 NVIDIA Tesla V100 NVLINK-anslutna GPU: er, var och en med 32 GB GPU-minne. Varje NDv2 VM har också 40-kärnor som inte är trådade Intel Xeon Platin 8168 (Skylake) och 672 GiB system minne.

NDv2-instanser ger utmärkta prestanda för HPC-och AI-arbetsbelastningar som använder CUDA GPU-optimerade beräknings kärnor och de många AI-, ML-och Analytics-verktyg som har stöd för GPU-acceleration "out-of-box", till exempel TensorFlow, Pytorch, caffe, RAPIDS och andra ramverk.

I stor skala är NDv2 byggd för både beräknings intensiva data (med 8 GPU-processorer per virtuell dator) och skalbarhet (vilket innebär att flera virtuella datorer fungerar tillsammans) arbets belastningar. NDv2-serien stöder nu 100-Gigabit InfiniBand EDR-nätverk, som liknar det som finns på HB-serien med HPC-VM, för att tillåta kluster med hög prestanda för parallella scenarier, inklusive distribuerad utbildning för AI och ML. Detta Server dels nätverk har stöd för alla större InfiniBand-protokoll, inklusive de som används av NVIDIA: s NCCL2-bibliotek, vilket möjliggör sömlös klustring av GPU: er.

> [!IMPORTANT]
> När du [aktiverar InfiniBand](./workloads/hpc/enable-infiniband.md) på den virtuella datorn ND40rs_v2 ska du använda Mellanox ofed-drivrutinen (4,7-1.0.0.1).
>
> På grund av ett ökat GPU-minne kräver den nya ND40rs_v2 virtuella datorn användningen av [virtuella datorer i generation 2](./generation-2.md) och Marketplace-avbildningar. 
>
> OBS! den ND40s_v2 med 16 GB minne per GPU är inte längre tillgänglig för för hands versionen och har ersatts av den uppdaterade ND40rs_v2.

<br>

[Premium Storage](premium-storage-performance.md): stöds<br>
[Premium Storage caching](premium-storage-performance.md): stöds<br>
[Direktmigrering](maintenance-and-updates.md): stöds inte<br>
[Minnes bebetjänings uppdateringar](maintenance-and-updates.md): stöds inte<br>
[Stöd för VM-generering](generation-2.md): generation 2<br>
[Accelererat nätverk](../virtual-network/create-vm-accelerated-networking-cli.md): stöds<br>
[Tillfälliga OS-diskar](ephemeral-os-disks.md): stöds inte <br>
InfiniBand: stöds<br>
NVIDIA NVLink-Interconnect: stöds<br>
<br>

| Storlek | Virtuell processor | Minne: GiB | Temp-lagring (SSD): GiB | GPU | GPU-minne: GiB | Maximalt antal datadiskar | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Maximal nätverks bandbredd | Maximalt antal nätverkskort |
|---|---|---|---|---|---|---|---|---|---|
| Standard_ND40rs_v2 | 40 | 672 | 2948 | 8 V100 32 GB (NVLink) | 32 | 32 | 80000/800 | 24000 Mbit/s | 8 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Operativsystem och drivrutiner som stöds

För att kunna dra nytta av GPU-funktionerna i virtuella datorer i Azure N-serien måste du installera NVIDIA GPU-drivrutiner.

[NVidia GPU-drivrutinen](./extensions/hpccompute-gpu-linux.md) installerar lämpliga NVIDIA-CUDA eller rutnäts driv rutiner på en virtuell dator i N-serien. Installera eller hantera tillägget med hjälp av Azure Portal eller verktyg som Azure PowerShell eller Azure Resource Manager mallar. Allmän information om VM-tillägg finns i [tillägg och funktioner för virtuella Azure-datorer](./extensions/overview.md).

Om du väljer att installera NVIDIA GPU-drivrutiner manuellt, se [konfiguration av GPU-drivrutiner för Linux i N-serien](./linux/n-series-driver-setup.md).

## <a name="other-sizes"></a>Andra storlekar

- [Generell användning](sizes-general.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare generationer](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur [Azure Compute Units (ACU)](acu.md) kan hjälpa dig att jämföra beräknings prestanda i Azure SKU: er.
