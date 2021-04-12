---
title: Översikt över virtuella datorer i HBv2-serien – Azure Virtual Machines | Microsoft Docs
description: Lär dig mer om storleken på virtuella datorer i HBv2-serien i Azure.
services: virtual-machines
author: vermagit
tags: azure-resource-manager
ms.service: virtual-machines
ms.subservice: hpc
ms.workload: infrastructure-services
ms.topic: article
ms.date: 09/28/2020
ms.author: amverma
ms.reviewer: cynthn
ms.openlocfilehash: 59dd953b2116bc1ec7bd0a581cc181df64fbf49e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104721155"
---
# <a name="hbv2-series-virtual-machine-overview"></a>Översikt över virtuell dator i HBv2-serien 

 
Att maximera prestanda för hög prestanda beräkning (HPC) i AMD EPYC kräver en metod för att lokalisera minnes utrymme och processer på ett genomtänkt sätt. Nedan ska vi disponera AMD EPYC-arkitekturen och vår implementering av IT på Azure för HPC-program. Vi kommer att använda termen **pNUMA** för att referera till en fysisk NUMA-domän och **vNUMA** för att referera till en virtualiserad NUMA-domän. 

Fysiskt, en [HBv2-serie](../../hbv2-series.md) Server är 2 * 64 kärnor EPYC 7742 CPU: er för totalt 128 fysiska kärnor. Dessa 128 kärnor är indelade i 32 pNUMA-domäner (16 per socket) som är 4 kärnor och betecknas av AMD som en **kärn komplex** (eller **CCX**). Varje CCX har sin egen L3-cache, vilket är hur ett operativ system kommer att se en pNUMA/vNUMA-gränsen. Fyra intilliggande CCXs resurs åtkomst till 2 kanaler av fysiskt DRAM. 

För att ge plats för Azure-hypervisorn att fungera utan att störa den virtuella datorn, reservera vi fysiska pNUMA-domäner 0 och 16 (d.v.s. det första CCX för varje CPU-socket). Alla återstående 30 pNUMA-domäner tilldelas den virtuella datorn vid den tidpunkt då de blir vNUMA. Den virtuella datorn kommer därför att se:

`(30 vNUMA domains) * (4 cores/vNUMA) = 120` kärnor per virtuell dator 

Den virtuella datorn har ingen medvetenhet om att pNUMA 0 och 16 är reserverade. Den räknar upp den vNUMA som den ser som 0-29, med 15 vNUMA per socket symmetriskt, vNUMA 0-14 på vSocket 0 och vNUMA 15-29 på vSocket 1. I nästa avsnitt finns instruktioner för hur du bäst kan köra MPI-program på den här asymmetriska NUMA-layouten. 

Process fästning fungerar på virtuella datorer i HBv2-serien eftersom vi exponerar den underliggande kiseln som-är till den virtuella gäst datorn. Vi rekommenderar starkt att du ska fästa processen för optimala prestanda och konsekvens. 


## <a name="hardware-specifications"></a>Maskinvaruspecifikationer 

| Specifikationer för maskin vara          | Virtuell dator i HBv2-serien                   | 
|----------------------------------|----------------------------------|
| Kärnor                            | 120 (SMT inaktive rad)               | 
| Processor                              | AMD EPYC 7742                    | 
| CPU-frekvens (icke-AVX)          | ~ 3,1 GHz (enkel + alla kärnor)    | 
| Minne                           | 4 GB/kärna (totalt 480 GB)         | 
| Lokal disk                       | 960 GB NVMe (block), 480 GB SSD (växlings fil) | 
| InfiniBand                       | 200 GB/s EDR Mellanox ConnectX-6 | 
| Nätverk                          | 50 GB/s Ethernet (40 GB/s användbar) Azure andra gen SmartNIC | 


## <a name="software-specifications"></a>Programspecifikationer 

| Programspecifikationer     | Virtuell dator i HBv2-serien                                            | 
|-----------------------------|-----------------------------------------------------------|
| Maximal jobb storlek för MPI            | 36000 kärnor (300 virtuella datorer i en skalnings uppsättning för en virtuell dator med singlePlacementGroup = true) |
| MPI-stöd                 | HPC-X, Intel MPI, OpenMPI, MVAPICH2, MPICH, Platform MPI  |
| Ytterligare ramverk       | UCX, libfabric, PGAS |
| Azure Storage support       | Standard-och Premium-diskar (högst 8 diskar) |
| OS-stöd för IOV RDMA   | CentOS/RHEL 7.6 +, Ubuntu 16.04 +, SLES 12 SP4 +, WinServer 2016 +  |
| Stöd för Orchestrator        | CycleCloud, batch, AKS; [kluster konfigurations alternativ](../../sizes-hpc.md#cluster-configuration-options)  |

> [!NOTE] 
> Windows Server 2012 R2 stöds inte på HBv2 och andra virtuella datorer med mer än 64 (virtuella eller fysiska) kärnor. Mer information finns [här](https://docs.microsoft.com/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [AMD EPYC-arkitekturen](https://bit.ly/2Epv3kC) och [multi-chip-arkitekturer](https://bit.ly/2GpQIMb). Mer detaljerad information finns i HPC- [justerings guiden för AMD EPYC-processorer](https://bit.ly/2T3AWZ9).
- Läs om de senaste meddelandena, HPC-arbetsbelastnings exempel och prestanda resultat på [Azure Compute Tech-Webbgruppens Bloggar](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- En arkitektur för högre nivå för att köra HPC-arbetsbelastningar finns i [HPC (data behandling med höga prestanda) i Azure](/azure/architecture/topics/high-performance-computing/).