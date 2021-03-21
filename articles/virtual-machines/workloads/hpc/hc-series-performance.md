---
title: Prestanda för virtuell dator i HC-serien
description: Lär dig mer om prestanda testnings resultat för VM-storlekar i HC-serien i Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 09/10/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 177a58090303a70491d9a9226eca40d0bb371764
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "104722267"
---
# <a name="hc-series-virtual-machine-sizes"></a>Storlekar på virtuella datorer i HC-serien

Flera prestandatester har körts i storlekarna för HC-serien. Följande är några av resultaten av den här prestanda testningen.

| Arbetsbelastning                                        | HB                    |
|-------------------------------------------------|-----------------------|
| STREAM-Triad                                    | 190 GB/s (Intel MLC AVX-512)  |
| High-Performance Linpack (HPL)                  | 3520 GigaFLOPS (Rpeak), 2970 GigaFLOPS (Rmax) |
| RDMA-latens & bandbredd                        | 1,05 mikrosekunder, 96,8 GB/s   |
| FIO på lokala NVMe SSD                           | 1,3 GB/s läsningar, 900 MB/s skrivningar |  
| IOR på 4 Azure Premium SSD (P30 Managed Disks, RAID0) * *  | 780 MB/s läsningar, 780 MB/skrivningar |

## <a name="mpi-latency"></a>MPI-svars tid

MPI latens-test från OSU mikrobenchmark Suite körs. Exempel skript finns på [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)

```bash
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency 
```

:::image type="content" source="./media/latency-hc.png" alt-text="MPI svars tid på Azure HC.":::

## <a name="mpi-bandwidth"></a>MPI-bandbredd

MPI för bandbredds test från OSU-mikrobenchmark Suite körs. Exempel skript finns på [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh)

```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```

:::image type="content" source="./media/bandwidth-hc.png" alt-text="MPI bandbredd på Azure HC.":::


## <a name="mellanox-perftest"></a>Mellanox perftest

[Mellanox perftest-paketet](https://community.mellanox.com/s/article/perftest-package) har många InfiniBand-test, till exempel latens (ib_send_lat) och bandbredd (ib_send_bw). Ett exempel kommando är nedan.

```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```

## <a name="next-steps"></a>Nästa steg

- Läs om de senaste meddelandena, HPC-arbetsbelastnings exempel och prestanda resultat på [Azure Compute Tech-Webbgruppens Bloggar](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- En mer övergripande arkitektur för att köra HPC-arbetsbelastningar finns i [HPC (data behandling med höga prestanda) i Azure](/azure/architecture/topics/high-performance-computing/).
