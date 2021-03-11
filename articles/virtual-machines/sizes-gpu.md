---
title: Storlekar för virtuella Azure-datorer – GPU | Microsoft Docs
description: Visar en lista över de olika GPU-optimerade storlekar som är tillgängliga för virtuella datorer i Azure. Visar information om antalet virtuella processorer, data diskar och nätverkskort samt lagrings data flöde och nätverks bandbredd för storlekar i den här serien.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: a63a468301a5b0d54abd474fd9bb62212b890f76
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102554423"
---
# <a name="gpu-optimized-virtual-machine-sizes"></a>Storlekar för GPU-optimerade virtuella datorer

GPU-optimerade VM-storlekar är specialiserade virtuella datorer som är tillgängliga med enkla, flera eller bråktal-GPU: er. De här storlekarna är utformade för beräknings intensiva, grafik intensiva och visualiserings arbets belastningar. Den här artikeln innehåller information om antalet och typen av GPU, virtuella processorer, data diskar och nätverkskort. Lagrings data flöde och nätverks bandbredd ingår också för varje storlek i grupperingen.

- Storlekarna för [NCv3-serien](ncv3-series.md) och [NC T4_v3-serien](nct4-v3-series.md) är optimerade för beräknings intensiva GPU-accelererade program. Några exempel är CUDA-och OpenCL-baserade program och simuleringar, AI och djup inlärning. NC-T4 v3-serien fokuserar på moln arbets belastningar med NVIDIA: s Tesla T4 GPU-och AMD EPYC2 Rom-processor. NCv3-serien fokuserar på data behandling med höga prestanda och AI-arbetsbelastningar med NVIDIA: s Tesla V100-GPU.

- [NDv2-seriens](ndv2-series.md) storlek fokuserar på skalbara och skalbara program för djup inlärning. NDv2-serien använder NVIDIA Volta-V100 och Intel Xeon Platin 8168-processorn (Skylake).

- Storlekarna på [NV-](nv-series.md) och [NVv3-serien](nvv3-series.md) är optimerade och utformade för fjärrvisualiseringar, strömnings-, spel-, kodnings-och VDI-scenarier med ramverk som OpenGL och DirectX. De här virtuella datorerna backas upp av NVIDIA Tesla M60-GPU: n.

- [NVv4-serien](nvv4-series.md) VM-storlekar som är optimerade för VDI och fjärrvisualisering. Med partitionerade GPU: er ger NVv4 rätt storlek för arbets belastningar som kräver mindre GPU-resurser. De här virtuella datorerna backas upp av AMD Radeon Instinct MI25-GPU: n. Virtuella NVv4-datorer stöder för närvarande endast Windows gäst operativ system.

## <a name="supported-operating-systems-and-drivers"></a>Operativsystem och drivrutiner som stöds

För att kunna dra nytta av GPU-funktionerna i virtuella datorer i Azure N-serien måste du installera NVIDIA-och AMD GPU-drivrutiner.

- För virtuella datorer som backas upp av NVIDIA GPU: er installeras lämpliga NVIDIA-CUDA eller RUTNÄTs driv rutiner i [nVidia GPU-drivrutinen](./extensions/hpccompute-gpu-windows.md) . Installera eller hantera tillägget med hjälp av Azure Portal eller verktyg som Azure PowerShell eller Azure Resource Manager mallar. Mer information om vilka operativ system och distributions steg som stöds finns i [dokumentationen för NVIDIA GPU-drivrutins tillägget](./extensions/hpccompute-gpu-windows.md) . Allmän information om VM-tillägg finns i [tillägg och funktioner för virtuella Azure-datorer](./extensions/overview.md).   

   Alternativt kan du installera NVIDIA GPU-drivrutiner manuellt. Se [Installera nVidia GPU-drivrutiner för virtuella datorer i n-serien som kör Windows](./windows/n-series-driver-setup.md) eller [Installera nVidia GPU-drivrutiner på virtuella datorer i n-serien som kör Linux](./linux/n-series-driver-setup.md) för operativ system, driv rutiner, installation och verifierings steg som stöds.

- För virtuella datorer som backas upp av AMD GPU: er, se [Installera AMD GPU-drivrutiner för virtuella datorer i N-serien som kör Windows](./windows/n-series-amd-driver-setup.md) för operativ system, driv rutiner, installation och verifierings steg som stöds.

## <a name="deployment-considerations"></a>Distributionsöverväganden

- Tillgänglighet för virtuella datorer i N-serien finns i [produkt tillgänglighet per region](https://azure.microsoft.com/regions/services/).

- Virtuella datorer i N-serien kan bara distribueras i distributions modellen för Resource Manager.

- Virtuella datorer i N-serien skiljer sig åt i den typ av Azure Storage de stöder för diskarna. NC och NV-virtuella datorer stöder bara virtuella hård diskar som backas upp av standard Disklagring (HDD). Alla andra GPU-VM: ar stöder VM-diskar som backas upp av standard Disklagring och Premium Disklagring (SSD).

- Om du vill distribuera fler än några få virtuella datorer i N-serien bör du överväga att betala per användning-prenumeration eller andra köp alternativ. Om du använder ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) kan du bara använda ett begränsat antal Azure Compute-kärnor.

- Du kan behöva öka processor kvoten (per region) i din Azure-prenumeration och öka den separata kvoten för NC-, NCv2-, NCv3-, ND-, NDv2-, NV-eller NVv2-kärnor. Om du vill begära en kvot ökning [öppnar du en support förfrågan online](../azure-portal/supportability/how-to-create-azure-support-request.md) utan kostnad. Standard gränserna kan variera beroende på din prenumerations kategori.

## <a name="other-sizes"></a>Andra storlekar

- [Generell användning](sizes-general.md)
- [Beräkningsoptimerad](sizes-compute.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [Tidigare generationer](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur [Azure Compute Units (ACU)](acu.md) kan hjälpa dig att jämföra beräknings prestanda i Azure SKU: er.
