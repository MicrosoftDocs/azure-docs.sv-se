---
title: ND-serien – Azure Virtual Machines
description: Specifikationer för de virtuella datorerna i ND-serien.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 6428f3eafc9d19060fb065082ea3cbe06bb74d81
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98917022"
---
# <a name="nd-series"></a>ND-serien

De virtuella datorerna i ND-serien är ett nytt tillägg till GPU-familjen som är utformad för AI-och djup inlärnings arbets belastningar. De erbjuder utmärkt prestanda för utbildning och härledning. ND-instanser drivs av [NVIDIA Tesla P40](https://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) -GPU: er och Intel Xeon E5-2690 v4-processorer (Broadwell). Dessa instanser ger utmärkta prestanda för flytt ALS åtgärder med enkel precision, för AI-arbetsbelastningar som använder Microsoft Cognitive Toolkit, TensorFlow, caffe och andra ramverk. ND-serien erbjuder även en mycket större GPU-minnes storlek (24 GB), vilket gör det möjligt att få plats med mycket större neurala NET-modeller. I likhet med NC-serien erbjuder ND-serien en konfiguration med ett sekundärt nätverk med låg latens, högt data flöde via RDMA och InfiniBand-anslutning så att du kan köra storskaliga utbildnings jobb över flera GPU: er.

[Premium Storage](premium-storage-performance.md): stöds<br>
[Premium Storage caching](premium-storage-performance.md): stöds<br>
[Direktmigrering](maintenance-and-updates.md): stöds inte<br>
[Minnes bebetjänings uppdateringar](maintenance-and-updates.md): stöds inte<br>
[Stöd för VM-generering](generation-2.md): generation 1 och 2<br>
[Accelererat nätverk](../virtual-network/create-vm-accelerated-networking-cli.md): stöds inte<br>
NVIDIA NVLink-Interconnect: stöds inte<br>

> [!IMPORTANT]
> För den här VM-serien anges vCPU (Core)-kvoten per region i din prenumeration till 0. [Begär en vCPU-kvot ökning](../azure-portal/supportability/resource-manager-core-quotas-request.md) för den här serien i en [tillgänglig region](https://azure.microsoft.com/regions/services/).
>
| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | GPU-minne: GiB | Maximalt antal datadiskar | Maximalt antal cachelagrade diskar: IOPS/MBps | Maximalt antal nätverkskort |
|---|---|---|---|---|---|---|---|---|
| Standard_ND6s    | 6  | 112 | 736  | 1 | 24 | 12 | 20000/200 | 4 |
| Standard_ND12s   | 12 | 224 | 1474 | 2 | 48 | 24 | 40000/400 | 8 |
| Standard_ND24s   | 24 | 448 | 2948 | 4 | 24 | 32 | 80000/800 | 8 |
| Standard_ND24rs * | 24 | 448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |

1 GPU = ett P40-kort.

*RDMA-stöd

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Operativsystem och drivrutiner som stöds

För att kunna dra nytta av GPU-funktionerna i virtuella datorer i Azure N-serien måste du installera NVIDIA GPU-drivrutiner.

[NVidia GPU-drivrutinen](./extensions/hpccompute-gpu-windows.md) installerar lämpliga NVIDIA-CUDA eller rutnäts driv rutiner på en virtuell dator i N-serien. Installera eller hantera tillägget med hjälp av Azure Portal eller verktyg som Azure PowerShell eller Azure Resource Manager mallar. Mer information om vilka operativ system och distributions steg som stöds finns i [dokumentationen för NVIDIA GPU-drivrutins tillägget](./extensions/hpccompute-gpu-windows.md) . Allmän information om VM-tillägg finns i [tillägg och funktioner för virtuella Azure-datorer](./extensions/overview.md).

Om du väljer att installera NVIDIA GPU-drivrutiner manuellt, se [N-seriens installations program för GPU-drivrutiner för Windows](./windows/n-series-driver-setup.md) eller [N-serien GPU-drivrutin installation för Linux](./linux/n-series-driver-setup.md) för operativ system, driv rutiner, installation och verifierings steg som stöds.

## <a name="other-sizes"></a>Andra storlekar

- [Generell användning](sizes-general.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare generationer](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur [Azure Compute Units (ACU)](acu.md) kan hjälpa dig att jämföra beräknings prestanda i Azure SKU: er.
