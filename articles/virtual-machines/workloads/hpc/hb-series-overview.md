---
title: Översikt över virtuella datorer i HB-serien – Azure Virtual Machines | Microsoft Docs
description: Läs mer om för hands versionen av storleken på virtuella datorer i HB-serien i Azure.
author: vermagit
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: article
ms.date: 08/19/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: b0e8e2e0ee7ce730f6bf00d7e5ef4bd4eae65ce7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "101666935"
---
# <a name="hb-series-virtual-machines-overview"></a>Översikt över virtuella datorer i HB-serien

Att maximera prestanda för hög prestanda beräkning (HPC) i AMD EPYC kräver en metod för att lokalisera minnes utrymme och processer på ett genomtänkt sätt. Nedan ska vi disponera AMD EPYC-arkitekturen och vår implementering av IT på Azure för HPC-program. Vi kommer att använda termen "pNUMA" för att referera till en fysisk NUMA-domän och "vNUMA" för att referera till en virtualiserad NUMA-domän.

Fysiskt, en [HB-serie](../../hb-series.md) Server är 2 * 32 kärnor EPYC 7551 CPU: er för totalt 64 fysiska kärnor. Dessa 64 kärnor är indelade i 16 pNUMA-domäner (8 per socket) som är fyra kärnor och kallas "processor komplex" (eller "CCX"). Varje CCX har sin egen L3-cache, vilket är hur ett operativ system kommer att se en pNUMA/vNUMA-gränsen. Ett par intilliggande CCXs delar åtkomst till två kanaler av fysiskt DRAM (32 GB RAM-minnen i HB-serie servrar).

För att ge plats för Azure-hypervisorn att fungera utan att störa den virtuella datorn, reservera vi fysisk pNUMA-domän 0 (den första CCX-datorn). Sedan tilldelar vi pNUMA-domäner 1-15 (återstående CCX-enheter) för den virtuella datorn. Den virtuella datorn visas:

`(15 vNUMA domains) * (4 cores/vNUMA) = 60` kärnor per virtuell dator

Den virtuella datorn vet inte att pNUMA 0 har tilldelats den. Den virtuella datorn förstår pNUMA 1-15 som vNUMA 0-14, med 7 vNUMA på vSocket 0 och 8 vNUMA på vSocket 1. Även om detta är asymmetriskt bör operativ systemet starta och fungerar normalt. Senare i den här hand boken beskriver vi hur du bäst kör MPI-program på den här asymmetriska NUMA-layouten.

Process fästning fungerar på virtuella datorer i HB-serien eftersom vi exponerar den underliggande kiseln som-är till den virtuella gäst datorn. Vi rekommenderar starkt att du ska fästa processen för optimala prestanda och konsekvens.

Följande diagram visar ansvars fördelning av kärnor som är reserverade för Azure hypervisor och den virtuella HB-serien.

![Uppdelning av kärnor som är reserverade för Azure hypervisor och HB-serien VM](./media/hb-series-overview/segregation-cores.png)

## <a name="hardware-specifications"></a>Maskinvaruspecifikationer

| Specifikationer för maskin vara                | Virtuell dator i HB-serien                     |
|----------------------------------|----------------------------------|
| Kärnor                            | 60 (SMT inaktive rad)                |
| Processor                              | AMD EPYC 7551                    |
| CPU-frekvens (icke-AVX)          | ~ 2,55 GHz (enkel + alla kärnor)   |
| Minne                           | 4 GB/kärna (totalt 240 GB)         |
| Lokal disk                       | 700 GB SSD                       |
| InfiniBand                       | 100 GB EDR Mellanox ConnectX-5 |
| Nätverk                          | 50 GB Ethernet (40 GB kan användas) Azure andra gen SmartNIC |

## <a name="software-specifications"></a>Programspecifikationer

| Programspecifikationer           |Virtuell dator i HB-serien           |
|-----------------------------|-----------------------|
| Maximal jobb storlek för MPI            | 18000 kärnor (300 virtuella datorer i en skalnings uppsättning för en virtuell dator med singlePlacementGroup = true)  |
| MPI-stöd                 | HPC-X, Intel MPI, OpenMPI, MVAPICH2, MPICH, Platform MPI  |
| Ytterligare ramverk       | Enhetlig kommunikation X, libfabric, PGAS |
| Azure Storage support       | Standard-och Premium-diskar (högst 4 diskar) |
| OS-stöd för IOV RDMA   | CentOS/RHEL 7.6 +, SLES 12 SP4 +, WinServer 2016 +  |
| Stöd för Orchestrator        | CycleCloud, batch  |

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [AMD EPYC-arkitekturen](https://bit.ly/2Epv3kC) och [multi-chip-arkitekturer](https://bit.ly/2GpQIMb). Mer detaljerad information finns i HPC- [justerings guiden för AMD EPYC-processorer](https://bit.ly/2T3AWZ9).
- Läs om de senaste meddelandena och några HPC-exempel och resultat i [Azure Compute Tech community-Bloggar](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- En arkitektur för högre nivå för att köra HPC-arbetsbelastningar finns i [HPC (data behandling med höga prestanda) i Azure](/azure/architecture/topics/high-performance-computing/).
