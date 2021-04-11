---
title: NV-serien – Azure Virtual Machines
description: Specifikationer för virtuella datorer med NV-serien.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 4aec4ab90e430569ae2e771e32b613ddd0dee89a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102551104"
---
# <a name="nv-series"></a>NV-serien

De virtuella datorerna med NV-serien drivs av [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU: er och NVIDIA grid-teknik för snabbare program och virtuella skriv bord där kunderna kan visualisera data eller simuleringar. Användare kan visualisera sina grafik intensiva arbets flöden på NV-instanserna för att få överlägsen grafik kapacitet och dessutom köra enskilda precisions arbets belastningar som kodning och åter givning. Virtuella datorer i NV-serien drivs också av Intel Xeon E5-2690 v3-processorer (Haswell).

Varje GPU i NV-instanser levereras med en GRID-licens. Den här licensen ger dig möjlighet att använda en NV-instans som virtuell arbets station för en enskild användare, eller att 25 samtidiga användare kan ansluta till den virtuella datorn för ett virtuellt program scenario.

[Premium Storage](premium-storage-performance.md): stöds inte<br>
[Premium Storage caching](premium-storage-performance.md): stöds inte<br>
[Direktmigrering](maintenance-and-updates.md): stöds inte<br>
[Minnes bebetjänings uppdateringar](maintenance-and-updates.md): stöds inte<br>
[Stöd för VM-generering](generation-2.md): generation 1<br>
[Accelererat nätverk](../virtual-network/create-vm-accelerated-networking-cli.md): stöds inte<br>
[Tillfälliga OS-diskar](ephemeral-os-disks.md): stöds inte <br>
<br>

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | GPU-minne: GiB | Maximalt antal datadiskar | Maximalt antal nätverkskort | Virtuella arbets stationer | Virtuella program |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NV6  | 6  | 56  | 340  | 1 | 8  | 24 | 1 | 1 | 25  |
| Standard_NV12 | 12 | 112 | 680  | 2 | 16 | 48 | 2 | 2 | 50  |
| Standard_NV24 | 24 | 224 | 1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = ett halvt M60-kort.

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
