---
title: Översikt över virtuella datorer i HC-serien – Azure Virtual Machines | Microsoft Docs
description: Läs mer om för hands versions stöd för VM-storleken för HC-serien i Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: workloads
ms.topic: article
ms.date: 08/19/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: b9fe978da9accd28ea0e538f458325f10c9c5d8d
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/31/2020
ms.locfileid: "97831898"
---
# <a name="hc-series-virtual-machine-overview"></a>Översikt över virtuell dator i HC-serien

Att maximera HPC-programmets prestanda på Intel Xeon-skalbara processorer kräver en genomtänkt metod för att bearbeta placeringen på den nya arkitekturen. Här beskriver vi vår implementering av IT på virtuella datorer i Azure HC-serien för HPC-program. Vi kommer att använda termen "pNUMA" för att referera till en fysisk NUMA-domän och "vNUMA" för att referera till en virtualiserad NUMA-domän. På samma sätt kommer vi att använda termen "pCore" för att referera till fysiska processor kärnor och "vCore" för att referera till virtualiserade processor kärnor.

Fysisk, en [HC-seriens](../../hc-series.md) Server är 2 * 24 kärnor Intel Xeon platina 8168-processorer för totalt 48 fysiska kärnor. Varje CPU är en enskild pNUMA-domän och har enhetlig åtkomst till sex kanaler i DRAM. Intel Xeon platina-processorer har en 4x större L2-cache än i tidigare generationer (256 KB/Core-> 1 MB/Core), samtidigt som L3-cachen minskas jämfört med tidigare Intel-processorer (2,5 MB/Core-> 1,375 MB/Core).

Ovanstående topologi går även över till HC-seriens hypervisor-konfiguration. För att ge plats för Azure-hypervisorn att fungera utan att störa den virtuella datorn, reservera vi pCores 0-1 och 24-25 (det vill säga de första två pCores på varje socket). Vi tilldelar sedan pNUMA-domäner alla återstående kärnor till den virtuella datorn. Den virtuella datorn kommer därför att se:

`(2 vNUMA domains) * (22 cores/vNUMA) = 44` kärnor per virtuell dator

Den virtuella datorn har ingen kunskap som pCores 0-1 och 24-25 inte fick till den. Därför exponerar den varje vNUMA som om den ursprungligen hade 22 kärnor.

Intel Xeon platina-, guld-och silver-CPU: er introducerar också ett interaktivt 2D-nätnätverk för kommunikation inom och utanför CPU-socketen. Vi rekommenderar starkt att du ska fästa processen för optimala prestanda och konsekvens. Process fästning fungerar på virtuella datorer i HC-serien eftersom den underliggande kiseln exponeras för den virtuella gäst datorn.

Följande diagram visar ansvars fördelning av kärnor som är reserverade för Azure hypervisor och virtuell HC-serien.

![Uppdelning av kärnor som har reserver ATS för Azure hypervisor och HC-seriens virtuella datorer](./media/hc-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Maskinvaruspecifikationer

| Specifikationer för maskin vara          | Virtuell dator i HC-serien                     |
|----------------------------------|----------------------------------|
| Kärnor                            | 44 (HT-inaktive rad)                 |
| Processor                              | Intel Xeon platina 8168         |
| CPU-frekvens (icke-AVX)          | 3,7 GHz (enkel kärna), 2.7-3.4 GHz (alla kärnor) |
| Minne                           | 8 GB/kärna (totalt 352)            |
| Lokal disk                       | 700 GB SSD                       |
| InfiniBand                       | 100 GB EDR Mellanox ConnectX-5   |
| Nätverk                          | 50 GB Ethernet (40 GB kan användas) Azure andra gen SmartNIC    |

## <a name="software-specifications"></a>Programspecifikationer

| Programspecifikationer     |Virtuell dator i HC-serien           |
|-----------------------------|-----------------------|
| Maximal jobb storlek för MPI            | 13200 kärnor (300 virtuella datorer i en skalnings uppsättning för en virtuell dator med singlePlacementGroup = true)  |
| MPI-stöd                 | HPC-X, Intel MPI, OpenMPI, MVAPICH2, MPICH, Platform MPI  |
| Ytterligare ramverk       | Enhetlig kommunikation X, libfabric, PGAS |
| Azure Storage support       | Standard-och Premium-diskar (högst 4 diskar) |
| OS-stöd för IOV RDMA   | CentOS/RHEL 7.6 +, SLES 12 SP4 +, WinServer 2016 +  |
| Stöd för Orchestrator        | CycleCloud, batch  |

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Intel Xeon SP-arkitektur](https://software.intel.com/content/www/us/en/develop/articles/intel-xeon-processor-scalable-family-technical-overview.html).
- Läs om de senaste meddelandena och några HPC-exempel och resultat i [Azure Compute Tech community-Bloggar](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- En arkitektur för högre nivå för att köra HPC-arbetsbelastningar finns i [HPC (data behandling med höga prestanda) i Azure](/azure/architecture/topics/high-performance-computing/).
