---
title: Prestanda för virtuell dator i HBv2-serien
description: Lär dig mer om prestanda testnings resultat för VM-storlekar i HBv2 i Azure.
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/28/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 28e7066c166a4c61c2f6436e9bd126f712f89f78
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101670075"
---
# <a name="hbv2-series-virtual-machine-sizes"></a>HBv2-seriens storlekar på virtuella datorer

Flera prestandatester har körts på virtuella datorer i [HBv2-serien](../../hbv2-series.md) . Följande är några av resultaten av den här prestanda testningen.


| Arbetsbelastning                                        | HBv2                                                              |
|-------------------------------------------------|-------------------------------------------------------------------|
| STREAM-Triad                                    | 350 GB/s (21-23 GB/s per CCX)                                     |
| High-Performance Linpack (HPL)                  | 4 TeraFLOPS (Rpeak, FP64), 8 TeraFLOPS (Rmax, FP32)               |
| RDMA-latens & bandbredd                        | 1,2 mikrosekunder, 190 GB/s                                        |
| FIO på lokala NVMe SSD                           | 2,7 GB/s läsningar, 1,1 GB/s skrivningar; 102k IOPS-läsningar, 115 IOPS-skrivningar |
| IOR på 8 * Azure Premium SSD (P40 Managed Disks, RAID0) * *  | 1,3 GB/s läsningar, 2,5 GB/skrivningar; 101k IOPS-läsningar, 105k IOPS-skrivningar |


## <a name="mpi-latency"></a>MPI-svars tid

MPI latens-test från OSU mikrobenchmark Suite körs. Exempel skript finns på [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh).


```bash 
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency
``` 
 
:::image type="content" source="./media/latency-hbv2.png" alt-text="MPI svars tid på Azure HB.":::


## <a name="mpi-bandwidth"></a>MPI-bandbredd

MPI för bandbredds test från OSU-mikrobenchmark Suite körs. Exempel skript finns på [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh).


```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
``` 

:::image type="content" source="./media/bandwidth-hbv2.png" alt-text="MPI bandbredd på Azure HB.":::


## <a name="mellanox-perftest"></a>Mellanox perftest

[Mellanox perftest-paketet](https://community.mellanox.com/s/article/perftest-package) har många InfiniBand-test, till exempel latens (ib_send_lat) och bandbredd (ib_send_bw). Ett exempel kommando är nedan. 


```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```


## <a name="next-steps"></a>Nästa steg

- Läs om de senaste meddelandena och vissa HPC-exempel (data behandling med höga prestanda) och resultat i [Azure Compute Tech community-Bloggar](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- En mer övergripande arkitektur för att köra HPC-arbetsbelastningar finns i [HPC (data behandling med höga prestanda) i Azure](/azure/architecture/topics/high-performance-computing/).