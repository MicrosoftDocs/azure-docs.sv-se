---
title: NC-serien – Azure Virtual Machines
description: Specifikationer för virtuella datorer i NC-serien.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 97b5d6bc6f19e07ea7c980b98c1e7986c59dc546
ms.sourcegitcommit: d2d1c90ec5218b93abb80b8f3ed49dcf4327f7f4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97585419"
---
# <a name="nc-series"></a>NC-serien

Virtuella datorer i NC-serien drivs av [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) -kortet och Intel Xeon E5-2690 v3-processorn (Haswell). Användare kan gå igenom data snabbare genom att använda CUDA för energi gransknings program, krasch simulering, Ray-spårad åter givning, djup inlärning med mera. NC24r-konfigurationen ger ett nätverks gränssnitt med låg latens och hög genom strömning som är optimerat för tätt sammansatta parallella dator arbets belastningar.

[Premium Storage](premium-storage-performance.md): stöds inte<br>
[Premium Storage caching](premium-storage-performance.md): stöds inte<br>
[Direktmigrering](maintenance-and-updates.md): stöds inte<br>
[Minnes bebetjänings uppdateringar](maintenance-and-updates.md): stöds inte<br>
[Stöd för VM-generering](generation-2.md): generation 1<br>
NVIDIA NVLink-Interconnect: stöds inte<br>
<br>

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | GPU-minne: GiB | Maximalt antal datadiskar | Maximalt antal nätverkskort |
|---|---|---|---|---|---|---|---|
| Standard_NC6    | 6  | 56  | 340  | 1 | 12 | 24 | 1 |
| Standard_NC12   | 12 | 112 | 680  | 2 | 24 | 48 | 2 |
| Standard_NC24   | 24 | 224 | 1440 | 4 | 48 | 64 | 4 |
| Standard_NC24r* | 24 | 224 | 1440 | 4 | 48 | 64 | 4 |

1 GPU = ett halvt K80-kort.

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
