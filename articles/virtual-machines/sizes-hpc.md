---
title: Storlekar för virtuella Azure-datorer – HPC | Microsoft Docs
description: Visar en lista över de olika storlekar som är tillgängliga för virtuella datorer med hög prestanda beräkning i Azure. Visar information om antalet virtuella processorer, data diskar och nätverkskort samt lagrings data flöde och nätverks bandbredd för storlekar i den här serien.
author: vermagit
ms.service: virtual-machines
ms.subservice: vm-sizes-hpc
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/19/2021
ms.author: amverma
ms.reviewer: jushiman
ms.openlocfilehash: a41dce28427db40dfd19879e4ada95add64009c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104772441"
---
# <a name="high-performance-computing-vm-sizes"></a>Datorer med hög prestanda beräknings storlek

Virtuella datorer i Azure H-serien (VM) har utformats för att leverera prestanda, skalbarhet och kostnads effektivitet i ledande klass för olika verkliga HPC-arbetsbelastningar i verkligheten.

[HBv3-serien](hbv3-series.md) Virtuella datorer är optimerade för HPC-program, till exempel flytande dynamik, explicit och implicit begränsad element analys, väder modellering, seismisk-bearbetning, simulering av behållare och hö-simulering. HBv3 VM-funktionen har upp till 120 AMD EPYC™ 7003-seriens (Milano) processor kärnor, 448 GB RAM och ingen hyperthreading. Virtuella datorer i HBv3-serien ger också 350 GB/s minnes bandbredd, upp till 32 MB L3-cache per kärna, upp till 7 GB/s av block enhet SSD-prestanda och klock frekvens upp till 3,675 GHz. 

Alla VM-funktioner i HBv3 200 GB/SEK HDR InfiniBand från NVIDIA-nätverk för att aktivera superdator MPI-arbetsbelastningar. De här virtuella datorerna är anslutna i ett icke-blockerande fat-träd för optimerad och konsekvent RDMA-prestanda. HDR InfiniBand-infrastrukturen stöder också anpassningsbar Routning och dynamisk ansluten transport (DCT, förutom standard RC-och UD-transporter). Dessa funktioner förbättrar programmets prestanda, skalbarhet och konsekvens, och användningen rekommenderas starkt.

[HBv2-serien](hbv2-series.md) Virtuella datorer är optimerade för program som drivs av minnes bandbredd, till exempel flytande dynamik, analys av ändliga element och behållare för lagring. HBv2 VM Feature 120 AMD EPYC 7742 processor kärnor, 4 GB RAM per CPU-kärna och ingen samtidig multitrådning. Varje HBv2 VM ger upp till 340 GB/s minnes bandbredd och upp till 4 teraFLOPS FP64-beräkning.

HBv2 VM-funktionen 200 GB/SEK Mellanox HDR InfiniBand, medan både HB-och HC-seriens VM-funktioner 100 GB/s Mellanox EDR InfiniBand. Var och en av dessa VM-typer är ansluten i ett icke-blockerande fat-träd för optimerad och konsekvent RDMA-prestanda. HBv2-VM: ar stöder anpassningsbar Routning och dynamisk ansluten transport (DCT, förutom standard RC-och UD-transporter). Dessa funktioner förbättrar programmets prestanda, skalbarhet och konsekvens, och användningen rekommenderas starkt.

[HB-serien](hb-series.md) Virtuella datorer är optimerade för program som drivs av minnes bandbredd, till exempel flytande dynamik, explicit ändliga element analyser och väder modellering. HB VM Feature 60 AMD EPYC 7551 processor kärnor, 4 GB RAM-minne per CPU-kärna och ingen hyperthreading. AMD EPYC-plattformen ger över 260 GB/SEK minnes bandbredd.

[HC-serien](hc-series.md) Virtuella datorer är optimerade för program som drivs av kompakt beräkning, till exempel implicit ändlig analys av element, molekyl Dynamics och beräknings kemi. Virtuella HC-funktioner 44 Intel Xeon platina 8168 processor kärnor, 8 GB RAM-minne per CPU-kärna och ingen hyperthreading. Intel Xeon platina-plattformen stöder Intels omfattande eko system med program varu verktyg som Intel Math kernel-biblioteket.

[H-serien](h-series.md) Virtuella datorer är optimerade för program som drivs av höga CPU-frekvenser eller stora minnen per kärn krav. Virtuella datorer i H-serien 8 eller 16 Intel Xeon E5 2667 v3 processor kärnor, 7 eller 14 GB RAM-minne per CPU-kärna och ingen hyperthreading-funktion. H-seriens funktioner 56 GB/s Mellanox FDR InfiniBand i en icke-blockerande fett träds konfiguration för konsekvent RDMA-prestanda. Virtuella datorer i H-serien har stöd för Intel MPI 5. x och MS-MPI.

> [!NOTE]
> Alla virtuella datorer i HBv3, HBv2, HB och HC-serien har exklusiv åtkomst till de fysiska servrarna. Det finns bara en virtuell dator per fysisk server och det finns inga delade flera innehavare med andra virtuella datorer för de här VM-storlekarna.

> [!NOTE]
> De [virtuella datorerna A8 – A11](./sizes-previous-gen.md#a-series---compute-intensive-instances) tas bort från och med 3/2021. Det går nu inte att distribuera nya VM-distributioner av dessa storlekar. Om du har befintliga virtuella datorer kan du läsa om e-postmeddelanden för nästa steg, inklusive migrering till andra VM-storlekar i [guiden för HPC-migrering](https://azure.microsoft.com/resources/hpc-migration-guide/).

## <a name="rdma-capable-instances"></a>RDMA-kompatibla instanser

De flesta av de virtuella datorernas HPC-storlekar är ett nätverks gränssnitt för RDMA-anslutning (Remote Direct Memory Access). De valda [N-serie](./nc-series.md) storlekarna som anges med r är också RDMA-kompatibla. Det här gränssnittet är förutom standard gränssnittet för Azure Ethernet-nätverket som är tillgängligt i de andra VM-storlekarna.

Det här sekundära gränssnittet tillåter RDMA-kompatibla instanser att kommunicera över ett InfiniBand-nätverk (IB) till HDR-priser för HBv3, HBv2, EDR rates för HB, HC, NDv2 och FDR priser för H16r, H16mr och andra RDMA-kompatibla N-seriens virtuella datorer. Dessa RDMA-funktioner kan öka skalbarheten och prestandan för MPI-baserade (Message Passing Interface) program.

> [!NOTE]
> **SR-IOV-stöd**: i Azure HPC finns det för närvarande två typer av virtuella datorer beroende på om de är SR-IOV-aktiverade för InfiniBand. För närvarande är nästan alla nyare generationerna RDMA-kompatibla eller InfiniBand-aktiverade virtuella datorer i Azure SR-IOV-aktiverade, förutom H16r, H16mr och NC24r.
> RDMA är bara aktiverat över InfiniBand-nätverket (IB) och stöds för alla RDMA-kompatibla virtuella datorer.
> IP över IB stöds endast på de virtuella SR-IOV-datorer som är aktiverade.
> RDMA är inte aktiverat över Ethernet-nätverket.

- **Operativ system** – Linux-distributioner som CENTOS, RHEL, Ubuntu och SUSE används ofta. Windows Server 2016 och senare versioner stöds på alla virtuella datorer med HPC-serien. Windows Server 2012 R2 och Windows Server 2012 stöds också på virtuella datorer som inte är SR-IOV. Observera att [Windows Server 2012 R2 inte stöds på HBv2 och senare som VM-storlekar med mer än 64 (virtuella eller fysiska) kärnor](/windows-server/virtualization/hyper-v/supported-windows-guest-operating-systems-for-hyper-v-on-windows). Se [VM-avbildningar](./workloads/hpc/configure.md) för en lista över virtuella dator avbildningar som stöds på Marketplace och hur de kan konfigureras på rätt sätt. På sidorna för VM-storlek visas även stöd för program varu stack.

- **InfiniBand och driv rutiner** – på InfiniBand-aktiverade virtuella datorer krävs lämpliga driv rutiner för att aktivera RDMA. Se [VM-avbildningar](./workloads/hpc/configure.md) för en lista över virtuella dator avbildningar som stöds på Marketplace och hur de kan konfigureras på rätt sätt. Se även [Aktivera InfiniBand](./workloads/hpc/enable-infiniband.md) för att lära dig om VM-tillägg eller manuell installation av InfiniBand-drivrutiner.

- **MPI** – SR-IOV-aktiverade VM-storlekar i Azure tillåter nästan vilken smak av MPI som ska användas med Mellanox ofed. På virtuella datorer som inte är SR-IOV-aktiverade använder MPI-implementeringar som stöds av Microsoft Network Direct (ND)-gränssnittet för att kommunicera mellan virtuella datorer. Därför stöds endast Intel MPI 5. x-och Microsoft MPI (MS-MPI) 2012 R2 eller senare versioner. Senare versioner av Intel MPI runtime-biblioteket kanske inte är kompatibla med Azure RDMA-drivrutinerna. Se [installations MPI för HPC](./workloads/hpc/setup-mpi.md) för mer information om hur du konfigurerar MPI på virtuella HPC-datorer i Azure.

  > [!NOTE]
  > **RDMA-nätverkets adress utrymme**: RDMA-nätverket i Azure reserverar adress utrymmet 172.16.0.0/16. Om du vill köra MPI-program på instanser som har distribuerats i ett virtuellt Azure-nätverk kontrollerar du att det virtuella nätverkets adress utrymme inte överlappar RDMA-nätverket.

## <a name="cluster-configuration-options"></a>Kluster konfigurations alternativ

Azure innehåller flera alternativ för att skapa kluster med virtuella HPC-datorer som kan kommunicera med RDMA-nätverket, inklusive: 

- **Virtuella datorer**  – distribuera RDMA-kompatibla HPC-datorer i samma skalnings uppsättning eller tillgänglighets uppsättning (när du använder Azure Resource Manager distributions modell). Om du använder den klassiska distributions modellen distribuerar du de virtuella datorerna i samma moln tjänst.

- **Skalnings uppsättningar för virtuella datorer** – i en skalnings uppsättning för virtuella datorer, se till att du begränsar distributionen till en enda placerings grupp för InfiniBand-kommunikation i skalnings uppsättningen. I en Resource Manager-mall anger du till exempel `singlePlacementGroup` egenskapen till `true` . Observera att den maximala skalnings uppsättnings storleken som kan anpassas med `singlePlacementGroup=true` är på 100 virtuella datorer som standard. Om HPC-jobbets skalning måste vara högre än 100 virtuella datorer i en enda klient kan du begära en ökning, [öppna en kund support förfrågan](../azure-portal/supportability/how-to-create-azure-support-request.md) utan kostnad. Gränsen för antalet virtuella datorer i en enda skalnings uppsättning kan höjas till 300. Observera att när du distribuerar virtuella datorer med tillgänglighets uppsättningar är max gränsen på 200 VM: ar per tillgänglighets uppsättning.

  > [!NOTE]
  > **MPI mellan virtuella datorer**: om RDMA (t. ex. använder MPI-kommunikation) krävs mellan virtuella datorer, kontrollerar du att de virtuella datorerna finns i samma skalnings uppsättning eller tillgänglighets uppsättning för virtuella datorer.

- **Azure-CycleCloud** – skapa ett HPC-kluster med [Azure CycleCloud](/azure/cyclecloud/) för att köra MPI-jobb.

- **Azure Batch** – skapa en [Azure Batch](../batch/index.yml) -pool för att köra MPI-arbetsbelastningar. Om du vill använda beräknings intensiva instanser när du kör MPI-program med Azure Batch, se [Använd aktiviteter med flera instanser för att köra MPI-program (Message Passing Interface) i Azure Batch](../batch/batch-mpi.md).

- **Microsoft HPC Pack**  -  [HPC Pack](/powershell/high-performance-computing/overview) innehåller en körnings miljö för MS-MPI som använder Azure RDMA-nätverket när det distribueras på RDMA-kompatibla virtuella Linux-datorer. Exempel på distributioner finns i [Konfigurera ett Linux RDMA-kluster med HPC Pack för att köra MPI-program](/powershell/high-performance-computing/hpcpack-linux-openfoam).

## <a name="deployment-considerations"></a>Distributionsöverväganden

- **Azure-prenumeration** – om du vill distribuera fler än några få beräknings intensiva instanser bör du överväga att betala per användning-prenumeration eller andra köp alternativ. Om du använder ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) kan du bara använda ett begränsat antal Azure Compute-kärnor.

- **Priser och tillgänglighet** – kontrol lera priser och [tillgänglighet](https://azure.microsoft.com/global-infrastructure/services/) för [virtuella datorer](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) med Azure-regioner.

- **Kärnor-kvot** – du kan behöva öka kvoten för kärnor i din Azure-prenumeration från standardvärdet. Din prenumeration kan också begränsa antalet kärnor som du kan distribuera i vissa storlekar på virtuella dator grupper, inklusive H-serien. Om du vill begära en kvot ökning [öppnar du en support förfrågan online](../azure-portal/supportability/how-to-create-azure-support-request.md) utan kostnad. (Standard gränserna kan variera beroende på din prenumerations kategori.)

  > [!NOTE]
  > Kontakta Azure-supporten om du har storskaliga kapacitets behov. Azure-kvoter är kredit gränser, inte kapacitets garantier. Oavsett din kvot debiteras du bara för kärnor som du använder.
  
- **Virtuellt nätverk** – ett virtuellt Azure- [nätverk](https://azure.microsoft.com/documentation/services/virtual-network/) krävs inte för att använda beräknings intensiva instanser. För många distributioner behöver du dock minst ett molnbaserad virtuellt Azure-nätverk eller en plats-till-plats-anslutning om du behöver åtkomst till lokala resurser. När det behövs skapar du ett nytt virtuellt nätverk för att distribuera instanserna. Det går inte att lägga till beräknings intensiva virtuella datorer i ett virtuellt nätverk i en tillhörighets grupp.

- **Ändra storlek** – på grund av deras specialiserade maskin vara kan du bara ändra storlek på beräknings intensiva instanser inom samma storleks familj (H-serien eller N-serien). Du kan till exempel bara ändra storlek på en virtuell dator i H-serien från en storlek på H-serien till en annan. Ytterligare överväganden kring InfiniBand-driv rutins stöd och NVMe-diskar kan behöva beaktas för vissa virtuella datorer.


## <a name="other-sizes"></a>Andra storlekar

- [Generell användning](sizes-general.md)
- [Beräkningsoptimerad](sizes-compute.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Tidigare generationer](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

- Lär dig mer om [att konfigurera virtuella datorer](./workloads/hpc/configure.md), [Aktivera INFINIBAND](./workloads/hpc/enable-infiniband.md), konfigurera [MPI](./workloads/hpc/setup-mpi.md) och optimera HPC-program för Azure i [HPC-arbetsbelastningar](./workloads/hpc/overview.md).
- Läs översikt över [HBv3-serien](./workloads/hpc/hbv3-series-overview.md) och [HC-serien](./workloads/hpc/hc-series-overview.md).
- Läs om de senaste meddelandena, HPC-arbetsbelastnings exempel och prestanda resultat på [Azure Compute Tech-Webbgruppens Bloggar](https://techcommunity.microsoft.com/t5/azure-compute/bg-p/AzureCompute).
- En arkitektur för högre nivå för att köra HPC-arbetsbelastningar finns i [HPC (data behandling med höga prestanda) i Azure](/azure/architecture/topics/high-performance-computing/).
