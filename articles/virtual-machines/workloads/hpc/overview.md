---
title: Data behandling med höga prestanda på InfiniBand-aktiverade H-och N-seriens virtuella datorer – Azure Virtual Machines
description: Lär dig mer om funktionerna i InfiniBand-aktiverade H-och N-seriens virtuella datorer som är optimerade för HPC.
author: vermagit
ms.author: amverma
ms.service: virtual-machines
ms.subservice: hpc
ms.topic: overview
ms.date: 07/29/2020
ms.reviewer: cynthn
ms.openlocfilehash: 15d05632e5ebf6e45a61adebbbbf647efca2a1a1
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2021
ms.locfileid: "101666895"
---
# <a name="high-performance-computing-on-infiniband-enabled-h-series-and-n-series-vms"></a>Data behandling med höga prestanda på InfiniBand-aktiverade H-och N-seriens virtuella datorer

Azures InfiniBand-aktiverade H-och N-seriens virtuella datorer är utformade för att leverera MPI-skalbarhet (ledarskaps klass prestanda, Message Passing Interface) och kostnads effektivitet för en mängd olika HPC-och AI-arbetsbelastningar i Real världen. Dessa optimerade virtuella datorer med höga prestanda (HPC) används för att lösa några av de mest beräknings intensiva problemen med vetenskap och teknik, till exempel: Fluid Dynamics, jordnings modellering, väder simuleringar osv.

De här artiklarna beskriver hur du kommer igång med den InfiniBand-aktiverade H-seriens och N-seriens virtuella datorer på Azure samt optimal konfiguration av HPC-och AI-arbetsbelastningar på virtuella datorer för skalbarhet.

## <a name="features-and-capabilities"></a>Funktioner

De virtuella datorer i H-serien och N-serien är utformade för att ge bästa HPC-prestanda, skalbarhet för MPI och kostnads effektivitet för HPC-arbetsbelastningar. Se virtuella datorer i [H-serien](../../sizes-hpc.md) och [N-serien](../../sizes-gpu.md) för att lära dig mer om de virtuella datorernas funktioner och funktioner.

### <a name="rdma-and-infiniband"></a>RDMA och InfiniBand

[RDMA-kompatibla](../../sizes-hpc.md#rdma-capable-instances) [H-](../../sizes-hpc.md) och [N-seriens](../../sizes-gpu.md) virtuella datorer kommunicerar via ett InfiniBand-nätverk med låg latens och hög bandbredd. RDMA-funktionen över en sådan samman koppling är nödvändig för att öka skalbarheten och prestandan för HPC-och AI-arbetsbelastningar med distribuerad nod. De InfiniBand-baserade virtuella datorerna i H-serien och N-serien är anslutna i ett icke-blockerande fat-träd med en design med låg diameter för optimerad och konsekvent RDMA-prestanda.
Se [Aktivera InfiniBand](enable-infiniband.md) för att lära dig mer om hur du konfigurerar InfiniBand på de InfiniBand-aktiverade virtuella datorerna.

### <a name="message-passing-interface"></a>Gränssnitt för meddelande överföring

SR-IOV-funktionen i H-serien och N-serien stöder nästan alla MPI-bibliotek och-versioner. Några av de vanligaste MPI-biblioteken som stöds är: Intel MPI, OpenMPI, MPICH, MVAPICH2, Platform MPI och alla RDMA-verb (Remote Direct Memory Access).
Mer information om hur du installerar olika MPI-bibliotek och deras optimala konfiguration finns i [Konfigurera MPI](setup-mpi.md) .

## <a name="get-started"></a>Kom igång

Det första steget är att välja den VM-typ ( [H-serien](../../sizes-hpc.md) ) och [N-serien](../../sizes-gpu.md) som är optimal för arbets BELASTNINGen baserat på VM-specifikationerna och [RDMA-funktionen](../../sizes-hpc.md#rdma-capable-instances).
Sedan konfigurerar du den virtuella datorn genom att aktivera InfiniBand. Det finns olika metoder för att göra detta, inklusive att använda optimerade VM-avbildningar med driv rutiner bakade-in; Se [optimering för Linux](configure.md) och [Aktivera InfiniBand](enable-infiniband.md) för mer information.
För den distribuerade nodens arbets belastningar är det till exempel viktigt att välja och konfigurera MPI. Mer information finns i [Konfigurera MPI](setup-mpi.md) .
För prestanda och skalbarhet kan du konfigurera arbets belastningarna på ett optimalt sätt genom att följa anvisningarna för VM-serien, till exempel översikt över [HB-serien](hb-series-overview.md) och [HC-serien](hc-series-overview.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om att [Konfigurera och optimera](configure.md) InfiniBand-aktiverade virtuella datorer i [H-serien](../../sizes-hpc.md) och [N-serien](../../sizes-gpu.md) .
- Läs översikten över [HB-serien](hb-series-overview.md) och [HC-serien](hc-series-overview.md) för att lära dig mer om att konfigurera arbets belastningar optimalt för prestanda och skalbarhet.
- Läs om de senaste meddelandena och några HPC-exempel och resultat i [Azure Compute Tech community-Bloggar](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- En arkitektur för högre nivå för att köra HPC-arbetsbelastningar finns i [HPC (data behandling med höga prestanda) i Azure](/azure/architecture/topics/high-performance-computing/).
