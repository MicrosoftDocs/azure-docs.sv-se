---
title: Prestanda för virtuell dator i HBv3-serien
description: Lär dig mer om prestanda testnings resultat för VM-storlekar i HBv3 i Azure.
services: virtual-machines
author: vermagit
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/12/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 87c3e4e9b509589624a228ea2e1f4b68e86e3fa8
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "104721136"
---
# <a name="hbv3-series-virtual-machine-performance"></a>Prestanda för virtuell dator i HBv3-serien

Tidig åtkomst användare av virtuella HBv3-datorer kan förväntas följa följande prestanda siffror på vanliga HPC-prestandatester

| Arbetsbelastning                                        | HBv3                                                              |
|-------------------------------------------------|-------------------------------------------------------------------|
| STREAM-Triad                                    | 330-350 GB/s (~ 82-86 GB/s per NUMA)                                     |
| High-Performance Linpack (HPL)                  | 4 TF (Rpeak, FP64), 8 TF (Rpeak, FP32) för virtuell dator storlek på 120 kärnor               |
| RDMA-latens & bandbredd                        | 1,2 mikrosekunder (1 byte), 192 GB/s (envägs)                                        |
| FIO på lokala NVMe-SSD (RAID0)                  | 7 GB/s-läsningar, 3 GB/s skrivningar; 186k IOPS-läsningar, 201k IOPS-skrivningar |

## <a name="process-pinning"></a>Process fäste

Process fäst fungerar bra på virtuella datorer i HBv3-serien eftersom vi exponerar den underliggande kiseln som-är till den virtuella gäst datorn. Vi rekommenderar starkt att du ska fästa processen för optimala prestanda och konsekvens.

## <a name="mpi-latency"></a>MPI-svars tid

MPI latens-testet från OSU mikrobenchmark Suite kan utföras enligt nedan. Exempel skript finns på [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh).

```bash 
./bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./osu_latency
``` 
## <a name="mpi-bandwidth"></a>MPI-bandbredd
MPI bandbredds test från OSU-mikrobenchmark Suite kan utföras enligt nedan. Exempel skript finns på [GitHub](https://github.com/Azure/azhpc-images/blob/04ddb645314a6b2b02e9edb1ea52f079241f1297/tests/run-tests.sh).
```bash
./mvapich2-2.3.install/bin/mpirun_rsh -np 2 -hostfile ~/hostfile MV2_CPU_MAPPING=[INSERT CORE #] ./mvapich2-2.3/osu_benchmarks/mpi/pt2pt/osu_bw
```
## <a name="mellanox-perftest"></a>Mellanox perftest
[Mellanox perftest-paketet](https://community.mellanox.com/s/article/perftest-package) har många InfiniBand-test, till exempel latens (ib_send_lat) och bandbredd (ib_send_bw). Ett exempel kommando är nedan. 
```console
numactl --physcpubind=[INSERT CORE #]  ib_send_lat -a
```
## <a name="next-steps"></a>Nästa steg
- Lär dig mer om [skalning av MPI-program](compiling-scaling-applications.md).
- Läs om de senaste meddelandena, HPC-arbetsbelastnings exempel och prestanda resultat på [Azure Compute Tech-Webbgruppens Bloggar](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- En mer övergripande arkitektur för att köra HPC-arbetsbelastningar finns i [HPC (data behandling med höga prestanda) i Azure](/azure/architecture/topics/high-performance-computing/).