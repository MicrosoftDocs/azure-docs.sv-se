---
title: Översikt över virtuella datorer i HBv3-serien, arkitektur, topologi – Azure Virtual Machines | Microsoft Docs
description: Lär dig mer om storleken på virtuella datorer i HBv3-serien i Azure.
services: virtual-machines
author: vermagit
tags: azure-resource-manager
ms.service: virtual-machines
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 03/25/2021
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: f78420a65cd9c2402266eb9ba973eabe758d7ee5
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608292"
---
# <a name="hbv3-series-virtual-machine-overview"></a>Översikt över virtuell dator i HBv3-serien 

En [HBv3-serie](../../hbv3-series.md) Server funktioner 2 * 64 kärnor EPYC 7V13-CPU: er för totalt 128 fysiska "Zen3"-kärnor. Multitrådning (multithreading) är inaktiverat på HBv3. Dessa 128 kärnor är indelade i 16 avsnitt (8 per socket), varje avsnitt som innehåller 8 processor kärnor med enhetlig åtkomst till en 32 MB L3-cache. Azure HBv3-servrar kör också följande inställningar för AMD BIOS:

```bash
Nodes per Socket (NPS) = 2
L3 as NUMA = Disabled
NUMA domains within VM OS = 4
C-states = Enabled
```

Därför startas servern med 4 NUMA-domäner (2 per socket) varje 32 kärnor i storlek. Varje NUMA har direkt åtkomst till 4 kanaler av fysiskt DRAM-operativsystem på 3200 MT/s.

För att ge plats för Azure-hypervisorn att fungera utan att störa den virtuella datorn, reservera vi 8 fysiska kärnor per server.

## <a name="vm-topology"></a>VM-topologi

Följande diagram visar topologin för-servern. Vi förbehåller oss de här 8 hypervisor-värd kärnorna (gult) symmetriskt över båda processor uttagen och tar de första 2 kärnorna från en speciell, komplex Övertagning (CCDs) på varje NUMA-domän, med återstående kärnor för den virtuella HBv3-serien (grönt).

![Topologi för HBv3-seriens Server](./media/architecture/hbv3/hbv3-topology-server.png)

Observera att CCD-gräns inte motsvarar en NUMA-gräns. I HBv3 är en grupp med fyra i följd (4) CCDs konfigurerad som en NUMA-domän, både på nivån för värd servern och i en virtuell gäst dator. Därför exponeras alla HBv3 VM-storlekar 4 NUMA-domäner som kommer att visas för ett operativ system och program som visas nedan, 4 enhetliga NUMA-domäner, var och en med olika antal kärnor beroende på den specifika [HBV3 VM-storleken](../../hbv3-series.md).

![Topologi för virtuell dator i HBv3-serien](./media/architecture/hbv3/hbv3-topology-vm.png)

Varje HBv3 VM-storlek liknar den fysiska layouten, funktionerna och prestandan hos en annan processor än AMD EPYC 7003-serien, enligt följande:

| VM-storlek för HBv3-serien             | NUMA-domäner | Kärnor per NUMA-domän  | Likhet med AMD EPYC         |
|---------------------------------|--------------|------------------------|----------------------------------|
Standard_HB120rs_v3               | 4            | 30                     | Dual-socket EPYC 7713            |
Standard_HB120r-96s_v3            | 4            | 24                     | Dual-socket EPYC 7643            |
Standard_HB120r-64s_v3            | 4            | 16                     | Dual-socket EPYC 7543            |
Standard_HB120r-32s_v3            | 4            | 8                      | Dual-socket EPYC 7313            |
Standard_HB120r-16s_v3            | 4            | 4                      | EPYC-72F3 med dubbla socklar            |

> [!NOTE]
> De begränsade kärnornas storlekar för virtuella datorer minskar bara antalet fysiska kärnor som exponeras för den virtuella datorn. Alla globala delade till gångar (RAM, minnes bandbredd, L3-cache, GMI och xGMI-anslutning, InfiniBand, Azure Ethernet-nätverk, lokal SSD) förblir konstant. Detta gör det möjligt för en kund att välja en virtuell dator storlek som är bäst anpassad till en specifik uppsättning arbets belastnings-eller program varu licensierings behov.

Den virtuella NUMA-mappningen för varje HBv3 VM-storlek mappas till den underliggande fysiska NUMA-topologin. Det finns ingen potentiell missvisande abstraktion av maskin varu miljön. 

Den exakta topologin för de olika [HBV3 VM-storleken](../../hbv3-series.md) visas på följande sätt med utdata från [lstopo](https://linux.die.net/man/1/lstopo):
```bash
lstopo-no-graphics --no-io --no-legend --of txt
```
<br>
<details>
<summary>Klicka om du vill visa lstopo-utdata för Standard_HB120rs_v3</summary>

![lstopo-utdata för HBv3 – 120 VM](./media/architecture/hbv3/hbv3-120-lstopo.png)
</details>

<details>
<summary>Klicka om du vill visa lstopo-utdata för Standard_HB120rs-96_v3</summary>

![lstopo-utdata för HBv3 – 96 VM](./media/architecture/hbv3/hbv3-96-lstopo.png)
</details>

<details>
<summary>Klicka om du vill visa lstopo-utdata för Standard_HB120rs-64_v3</summary>

![lstopo-utdata för HBv3-64-VM](./media/architecture/hbv3/hbv3-64-lstopo.png)
</details>

<details>
<summary>Klicka om du vill visa lstopo-utdata för Standard_HB120rs-32_v3</summary>

![lstopo-utdata för HBv3 – 32 VM](./media/architecture/hbv3/hbv3-32-lstopo.png)
</details>

<details>
<summary>Klicka om du vill visa lstopo-utdata för Standard_HB120rs-16_v3</summary>

![lstopo-utdata för HBv3-16 VM](./media/architecture/hbv3/hbv3-16-lstopo.png)
</details>

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
