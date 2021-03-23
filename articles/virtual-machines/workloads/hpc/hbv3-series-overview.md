---
title: Översikt över virtuella datorer i HBv3-serien – Azure Virtual Machines | Microsoft Docs
description: Lär dig mer om storleken på virtuella datorer i HBv3-serien i Azure.
services: virtual-machines
author: vermagit
tags: azure-resource-manager
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/12/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: d1abd03f517f9e0b13a2994418cbae5cfbe22454
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801882"
---
# <a name="hbv3-series-virtual-machine-overview"></a>Översikt över virtuell dator i HBv3-serien 

En [HBv3-serie](../../hbv3-series.md) Server funktioner 2 * 64 kärnor EPYC 7V13-CPU: er för totalt 128 fysiska "Zen3"-kärnor. Multitrådning (multithreading) är inaktiverat på HBv3. Dessa 128 kärnor är indelade i 16 avsnitt (8 per socket), varje avsnitt som innehåller 8 processor kärnor med enhetlig åtkomst till en 32 MB L3-cache. Azure HBv3-servrar kör också följande inställningar för AMD BIOS:

```bash
Nodes per Socket =2
L3 as NUMA = Disabled
```

Därför startas servern med 4 NUMA-domäner (2 per socket) varje 32 kärnor i storlek. Varje NUMA har direkt åtkomst till 4 kanaler av fysiskt DRAM-operativsystem på 3200 MT/s.

För att ge plats för Azure-hypervisorn att fungera utan att störa den virtuella datorn, reservera vi 8 fysiska kärnor per server. 

Observera att begränsade kärnor i VM-storlekar bara minskar antalet fysiska kärnor som exponeras för den virtuella datorn. Alla globala delade till gångar (RAM, minnes bandbredd, L3-cache, GMI och xGMI-anslutning, InfiniBand, Azure Ethernet-nätverk, lokal SSD) förblir konstant. Detta gör det möjligt för en kund att välja en virtuell dator storlek som är bäst anpassad till en specifik uppsättning arbets belastnings-eller program varu licensierings behov.

Följande diagram visar ansvars fördelning av kärnor som är reserverade för Azure hypervisor (gult) och den virtuella HBv3-serien (grönt).
![Uppdelning av kärnor som är reserverade för Azure hypervisor och HBv3-serien VM](./media/architecture/hbv3-segregation-cores.png)

## <a name="infiniband-networking"></a>InfiniBand-nätverk
Virtuella HBv3-datorer fungerar också NVIDIA Mellanox HDR InfiniBand-nätverkskort (ConnectX-6) som körs på upp till 200 Gigabit/s. NÄTVERKSKORTet skickas till den virtuella datorn via IOV, vilket gör att nätverks trafiken kringgår hypervisor-modulen. Därför laddar kunderna standard Mellanox OFED-drivrutiner på HBv3-VM: ar som en Bare Metal-miljö.

HBv3-VM: ar stöder anpassningsbar routning, dynamisk ansluten transport (DCT, i tillägg till standard RC-och UD-transporter) och maskinvarubaserad avlastning av MPI Collectives till onboard-processorn för ConnectX-6-adaptern. Dessa funktioner förbättrar programmets prestanda, skalbarhet och konsekvens, och användningen av dem rekommenderas starkt.

## <a name="temporary-storage"></a>Tillfällig lagring
HBv3 VM funktion 3 fysiskt lokala SSD-enheter. En enhet är förformaterad för att fungera som en växlings fil och kommer att visas i den virtuella datorn som en generisk "SSD"-enhet.

Två andra, större SSD tillhandahålls som oformaterade block NVMe-enheter via NVMeDirect. När blocket NVMe-enheten kringgår hypervisor-programmet kommer den att ha högre bandbredd, högre IOPS och lägre latens per IOP.

Vid länkning i en stripe-matris ger NVMe SSD upp till 7 GB/s läsningar och 3 GB/s skrivningar, och upp till 186 000 IOPS (läsningar) och 201 000 IOPS (skrivningar) för djup ködjup.

## <a name="hardware-specifications"></a>Maskinvaruspecifikationer 

| Maskinvaruspecifikationer          | Virtuella datorer i HBv3-serien              |
|----------------------------------|----------------------------------|
| Kärnor                            | 120, 96, 64, 32 eller 16 (SMT-inaktive rad)               | 
| Processor                              | AMD EPYC-7V13                   | 
| CPU-frekvens (icke-AVX)          | 3,1 GHz (alla kärnor), 3,675 GHz (upp till 10 kärnor)    | 
| Minne                           | 448 GB (RAM per kärna beror på VM-storlek)         | 
| Lokal disk                       | 2 * 960 GB NVMe (block), 480 GB SSD (växlings fil) | 
| InfiniBand                       | 200 GB/s Mellanox-ConnectX – 6 HDR InfiniBand | 
| Nätverk                          | 50 GB/s Ethernet (40 GB/s användbar) Azure andra gen SmartNIC | 

## <a name="software-specifications"></a>Programspecifikationer 

| Programspecifikationer        | Virtuella datorer i HBv3-serien                                            | 
|--------------------------------|-----------------------------------------------------------|
| Maximal jobb storlek för MPI               | 36 000 kärnor (300 virtuella datorer i en skalnings uppsättning för en virtuell dator med singlePlacementGroup = true) |
| MPI-stöd                    | HPC-X, Intel MPI, OpenMPI, MVAPICH2, MPICH  |
| Ytterligare ramverk          | UCX, libfabric, PGAS                  |
| Azure Storage support          | Standard-och Premium-diskar (högst 32 diskar)              |
| OS-stöd för IOV RDMA      | CentOS/RHEL 7.6 +, Ubuntu 18.04 +, SLES 12 SP4 +, WinServer 2016 +           |
| Rekommenderat operativ system för prestanda | CentOS 8,1, Windows Server 2019 +
| Stöd för Orchestrator           | Azure CycleCloud, Azure Batch, AKS; [kluster konfigurations alternativ](../../sizes-hpc.md#cluster-configuration-options)                      | 

> [!NOTE] 
> Windows Server 2012 R2 stöds inte på HBv3 och andra virtuella datorer med mer än 64 (virtuella eller fysiska) kärnor. Mer information finns [här](https://docs.microsoft.com/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows).

## <a name="next-steps"></a>Nästa steg

- Läs om de senaste meddelandena, HPC-arbetsbelastnings exempel och prestanda resultat på [Azure Compute Tech-Webbgruppens Bloggar](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- En arkitektur för högre nivå för att köra HPC-arbetsbelastningar finns i [HPC (data behandling med höga prestanda) i Azure](/azure/architecture/topics/high-performance-computing/).