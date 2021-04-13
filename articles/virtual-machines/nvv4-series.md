---
title: NVv4-serien
description: Specifikationer för virtuella datorer i NVv4-serien.
author: vikancha-MSFT
ms.service: virtual-machines
ms.subservice: vm-sizes-gpu
ms.topic: conceptual
ms.date: 01/12/2020
ms.author: vikancha
ms.openlocfilehash: 2d0f82565ec4010d03003ed902e9aba819e93bc3
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303193"
---
# <a name="nvv4-series"></a>NVv4-serien 

De virtuella datorerna i NVv4-serien drivs av [AMD Radeon Instinct MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) GPU-processorer och AMD EPYC 7V12 (Rom). Med NVv4-serien Azure introducerar virtuella datorer med delar av GPU: er. Välj den högra virtuella datorn för GPU-accelererade grafik program och virtuella skriv bord som börjar på 1/8 av en GPU med 2 GiB-bildruta-buffert till en fullständig GPU med 16 GiB-bildruteproportioner. Virtuella NVv4-datorer stöder för närvarande endast Windows gäst operativ system.

<br>

[ACU](acu.md): 230-260<br>
[Premium Storage](premium-storage-performance.md): stöds<br>
[Premium Storage caching](premium-storage-performance.md): stöds<br>
[Ultra disks](disks-types.md#ultra-disk): stöds ([Lär dig mer](https://techcommunity.microsoft.com/t5/azure-compute/ultra-disk-storage-for-hpc-and-gpu-vms/ba-p/2189312) om tillgänglighet, användning och prestanda) <br>
[Direktmigrering](maintenance-and-updates.md): stöds inte<br>
[Minnes bebetjänings uppdateringar](maintenance-and-updates.md): stöds inte<br>
[Stöd för VM-generering](generation-2.md): generation 1 och 2<br>
[Accelererat nätverk](../virtual-network/create-vm-accelerated-networking-cli.md): stöds<br>
[Tillfälliga OS-diskar](ephemeral-os-disks.md): stöds <br>
<br>

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | GPU-minne: GiB | Maximalt antal datadiskar | Högsta antal nätverkskort/förväntad nätverks bandbredd (Mbit/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2/1 000 |
| Standard_NV8as_v4 |8 |28 |176 | 1/4 | 4 | 8 | 4/2 000 |
| Standard_NV16as_v4 |16 |56 |352 | 1/2 | 8 | 16 | 8/4 000 |
| Standard_NV32as_v4 |32 |112 |704 | 1 | 16 | 32 | 8 / 8000 |

<sup>1</sup> virtuella datorer i NVv4-serien använder AMD samtidiga multitråds teknik

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="supported-operating-systems-and-drivers"></a>Operativsystem och drivrutiner som stöds

För att kunna dra nytta av GPU-funktionerna i virtuella datorer med Azure NVv4-serien som kör Windows måste du installera AMD GPU-drivrutinerna.

Om du vill installera AMD GPU-drivrutiner manuellt, se [N-seriens installation av AMD GPU-drivrutiner för Windows](./windows/n-series-amd-driver-setup.md) för operativ system, driv rutiner, installation och verifierings steg som stöds.

## <a name="other-sizes"></a>Andra storlekar

- [Generell användning](sizes-general.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare generationer](sizes-previous-gen.md)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur [Azure Compute Units (ACU)](acu.md) kan hjälpa dig att jämföra beräknings prestanda i Azure SKU: er.
