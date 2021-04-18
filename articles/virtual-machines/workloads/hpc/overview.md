---
title: Databehandling med höga prestanda på InfiniBand-aktiverade virtuella datorer i H-serien och N-serien – Azure Virtual Machines
description: Lär dig mer om funktionerna i InfiniBand-aktiverade virtuella datorer i H-serien och N-serien som är optimerade för HPC.
author: vermagit
ms.author: amverma
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: overview
ms.date: 04/09/2021
ms.reviewer: cynthn
ms.openlocfilehash: 554764b89e5da4cd6777ec89fcb2f2d5ad104ebf
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2021
ms.locfileid: "107600275"
---
# <a name="high-performance-computing-on-infiniband-enabled-h-series-and-n-series-vms"></a>Databehandling med höga prestanda på InfiniBand-aktiverade virtuella datorer i H-serien och N-serien

Azures InfiniBand-aktiverade virtuella datorer i H-serien och N-serien är utformade för att leverera högpresterande prestanda, Message Passing Interface (MPI) skalbarhet och kostnadseffektivitet för en mängd verkliga HPC- och AI-arbetsbelastningar. Dessa hpc-optimerade virtuella datorer (databehandling med höga prestanda) används för att lösa några av de mest beräkningsintensiva problemen inom vetenskap och teknik, till exempel strömningsdynamik, jordmodellering, vädersimuleringar osv.

De här artiklarna beskriver hur du kommer igång med de infiniBand-aktiverade virtuella datorerna i H-serien och N-serien i Azure samt optimal konfiguration av HPC- och AI-arbetsbelastningar på de virtuella datorerna för skalbarhet.

## <a name="features-and-capabilities"></a>Funktioner

De infiniBand-aktiverade virtuella datorerna i H-serien och N-serien är utformade för att ge bästa möjliga HPC-prestanda, MPI-skalbarhet och kostnadseffektivitet för HPC-arbetsbelastningar. Mer information om de virtuella datorernas funktioner finns i [H-serien](../../sizes-hpc.md) och [N-serien.](../../sizes-gpu.md)

### <a name="rdma-and-infiniband"></a>RDMA och InfiniBand

[RDMA-kompatibla](../../sizes-hpc.md#rdma-capable-instances) [virtuella datorer i H-serien](../../sizes-hpc.md) och N-serien kommunicerar via InfiniBand-nätverket med låg latens och hög bandbredd. [](../../sizes-gpu.md) RDMA-funktionen över en sådan sammankoppling är viktig för att öka skalbarheten och prestandan för distribuerade noders HPC- och AI-arbetsbelastningar. De infiniBand-aktiverade virtuella datorerna i H-serien och N-serien är anslutna i ett icke-blockerande fet träd med en design med låg överensstämmelse för optimerad och konsekvent RDMA-prestanda.
Se [Aktivera InfiniBand om du](enable-infiniband.md) vill veta mer om att konfigurera InfiniBand på De InfiniBand-aktiverade virtuella datorerna.

### <a name="message-passing-interface"></a>Meddelandemeddelandegränssnitt

SR-IOV-aktiverade H-serien och N-serien stöder nästan alla MPI-bibliotek och -versioner. Några av de vanligaste MPI-biblioteken är: Intel MPI, OpenMPI, HPC-X, MVAPICH2, MPICH, Platform MPI. Alla RDMA-verb (direktåtkomst till fjärrminne) stöds.
Mer information om hur du installerar olika MPI-bibliotek som stöds och deras optimala konfiguration finns i Konfigurera [MPI.](setup-mpi.md)

## <a name="get-started"></a>Kom igång

Det första steget är att välja den typ av virtuell dator i [H-serien och N-serien](../../sizes-gpu.md) som är optimal för arbetsbelastningen baserat på [VM-specifikationerna](../../sizes-hpc.md) och [RDMA-kapaciteten.](../../sizes-hpc.md#rdma-capable-instances)
Konfigurera sedan den virtuella datorn genom att aktivera InfiniBand. Det finns olika metoder för att göra detta, inklusive att använda optimerade VM-avbildningar med drivrutiner som är in baked-in. Mer [information finns i Optimering](configure.md) för Linux och Aktivera [InfiniBand.](enable-infiniband.md)
För distribuerade nodarbetsbelastningar är det viktigt att välja och konfigurera MPI på rätt sätt. Mer [information finns i Konfigurera MPI.](setup-mpi.md)
För det fjärde konfigurerar du arbetsbelastningarna optimalt för prestanda och skalbarhet genom att följa vägledningen som är specifik för VM-serien, till exempel för [HBv3-seriens](hbv3-series-overview.md) översikt och översikt över [HC-serien.](hc-series-overview.md)

## <a name="next-steps"></a>Nästa steg

- Lär dig [mer om att konfigurera och optimera](configure.md) infiniBand-aktiverade virtuella datorer i [H-serien](../../sizes-hpc.md) [och N-serien.](../../sizes-gpu.md)
- Läs [översikten över HBv3-serien](hb-series-overview.md) och översikten över [HC-serien](hc-series-overview.md) för att lära dig hur du konfigurerar arbetsbelastningar optimalt för prestanda och skalbarhet.
- Läs om de senaste meddelandena, HPC-arbetsbelastningsexempel och prestandaresultat på [Azure Compute Tech Community Blogs](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- Testa dina kunskaper med en [utbildningsmodul om hur du optimerar HPC-program i Azure](https://docs.microsoft.com/learn/modules/optimize-tightly-coupled-hpc-apps/).
- En arkitekturvy på högre nivå av att köra HPC-arbetsbelastningar finns [i Databehandling med höga prestanda (HPC) på Azure.](/azure/architecture/topics/high-performance-computing/)
