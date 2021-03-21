---
title: Ddv4- och Ddsv4-serien
description: Specifikationer för virtuella datorer i DV4, Ddv4, Dsv4 och Ddsv4-serien.
author: brbell
ms.author: brbell
ms.reviewer: mimckitt
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 06/01/2020
ms.openlocfilehash: feabdcef9298c93f0cba93d3eeb9ebb0a32d6ef2
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560352"
---
# <a name="ddv4-and-ddsv4-series"></a>Ddv4- och Ddsv4-serien

Ddv4 och Ddsv4-serien körs på Intel &reg; Xeon &reg; platina 8272CL-processorer (Cascade Lake) i en Hyper-threadd konfiguration, vilket ger ett bättre värde för de flesta allmänna arbets belastningar. Den har en central Turbo klock hastighet på 3,4 GHz, [Intel &reg; Turbo Boost-teknik 2,0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Intel &reg; Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) och [Intel &reg; Advanced Vector Extensions 512 (Intel &reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). De stöder också [Intel &reg; djup inlärnings förstärkning](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html). Dessa nya VM-storlekar kommer att ha 50% större lokal lagring, samt bättre IOPS för lokala diskar för både läsning och skrivning jämfört med [dv3-/Dsv3-](./dv3-dsv3-series.md) storlekarna med [Gen2-VM](./generation-2.md): ar.

Användnings fall i D-serien omfattar program i företags klass, Relations databaser, minnes intern cachelagring och analys.

## <a name="ddv4-series"></a>Ddv4-serien

Ddv4-seriens storlekar körs på Intel &reg; Xeon &reg; platina-8272CL (överlappande sjö). Ddv4-serien erbjuder en kombination av vCPU, minne och temporär disk för de flesta produktions arbets belastningar.

De nya Ddv4 VM-storlekarna omfattar snabb, större lokal SSD-lagring (upp till 2 400 GiB) och är utformad för program som drar nytta av låg latens, lokal lagring med hög hastighet, till exempel program som kräver snabb läsning/skrivning till temporär lagring eller som kräver temporär lagring för cacheminnen eller temporära filer. Du kan ansluta standard-SSD och standard-HDD-lagring till de virtuella Ddv4-datorerna. Lagring av fjärrdata-datadisk faktureras separat från virtuella datorer.

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): stöds inte<br>
[Premium Storage caching](premium-storage-performance.md): stöds inte<br>
[Direktmigrering](maintenance-and-updates.md): stöds<br>
[Minnes bebetjänings uppdateringar](maintenance-and-updates.md): stöds<br>
[Stöd för VM-generering](generation-2.md): generation 1 och 2<br>
[Accelererat nätverk](../virtual-network/create-vm-accelerated-networking-cli.md): stöds (*minst 4 vCPU krävs*)<br>
[Tillfälliga OS-diskar](ephemeral-os-disks.md): stöds <br>
<br> 

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | <sup>**</sup> Maximalt cacheminne för cachelagrad och temporär lagring: IOPS/Mbit/s | Maximalt antal nätverkskort|Förväntad nätverks bandbredd (Mbit/s) |
|---|---|---|---|---|---|---|---|
| Standard_D2d_v4 | 2 | 8 | 75 | 4 | 19000/120 | 2|1000 |
| Standard_D4d_v4 | 4 | 16 | 150 | 8 | 38500/242 | 2|2000 |
| Standard_D8d_v4 | 8 | 32 | 300 | 16 | 77000/485 | 4|4000 |
| Standard_D16d_v4 | 16 | 64 | 600 | 32 | 154000/968 | 8|8000 |
| Standard_D32d_v4 | 32 | 128 | 1200 | 32 | 308000/1936 | 8|16000 |
| Standard_D48d_v4 | 48 | 192 | 1800 | 32 | 462000/2904 | 8|24000 |
| Standard_D64d_v4 | 64 | 256 | 2400 | 32 | 615000/3872 | 8|30000 |

<sup>**</sup> Dessa IOPs-värden kan garanteras med hjälp av [virtuella Gen2-datorer](generation-2.md)

## <a name="ddsv4-series"></a>Ddsv4-serien

Ddsv4-serien körs på Intel &reg; Xeon &reg; platina-8272CL (överlappande sjö). Ddsv4-serien erbjuder en kombination av vCPU, minne och temporär disk för de flesta produktions arbets belastningar.

De nya Ddsv4 VM-storlekarna omfattar snabb, större lokal SSD-lagring (upp till 2 400 GiB) och är utformad för program som drar nytta av låg latens, lokal lagring med hög hastighet, till exempel program som kräver snabb läsning/skrivning till temporär lagring eller som kräver temporär lagring för cacheminnen eller temporära filer. 

 > [!NOTE]
 >Pris-och debiterings mätare för Ddsv4-storlekar är samma som för Ddv4-serien.

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): stöds<br>
[Premium Storage caching](premium-storage-performance.md): stöds<br>
[Direktmigrering](maintenance-and-updates.md): stöds<br>
[Minnes bebetjänings uppdateringar](maintenance-and-updates.md): stöds<br>
[Stöd för VM-generering](generation-2.md): generation 1 och 2<br>
[Accelererat nätverk](../virtual-network/create-vm-accelerated-networking-cli.md): stöds (*minst 4 vCPU krävs*)<br>
[Tillfälliga OS-diskar](ephemeral-os-disks.md): stöds <br>
<br> 

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | <sup>**</sup> Högsta cachelagrade data flöde för cache och temporär lagring: IOPS/Mbit/s (cachestorlek i GiB) | Maximalt antal cachelagrade diskar: IOPS/MBps | Maximalt antal nätverkskort|Förväntad nätverks bandbredd (Mbit/s) |
|---|---|---|---|---|---|---|---|---|
| Standard_D2ds_v4 | 2 | 8 | 75 | 4 | 19000/120 (50) | 3200/48 | 2|1000 |
| Standard_D4ds_v4 | 4 | 16 | 150 | 8 | 38500/242 (100) | 6400/96 | 2|2000 |
| Standard_D8ds_v4 | 8 | 32 | 300 | 16 | 77000/485 (200) | 12800/192 | 4|4000 |
| Standard_D16ds_v4 | 16 | 64 | 600 | 32 | 154000/968 (400) | 25600/384 | 8|8000 |
| Standard_D32ds_v4 | 32 | 128 | 1200 | 32 | 308000/1936 (800) | 51200/768 | 8|16000 |
| Standard_D48ds_v4 | 48 | 192 | 1800 | 32 | 462000/2904 (1200) | 76800/1152 | 8|24000 |
| Standard_D64ds_v4 | 64 | 256 | 2400 | 32 | 615000/3872 (1600) | 80000/1200 | 8|30000 |

<sup>**</sup> Dessa IOPs-värden kan garanteras med hjälp av [virtuella Gen2-datorer](generation-2.md)

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Andra storlekar och information

- [Generell användning](sizes-general.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare generationer](sizes-previous-gen.md)

Pris kalkylator: [pris kalkylator](https://azure.microsoft.com/pricing/calculator/)

Mer information om disk typer: [disk typer](./disks-types.md#ultra-disk)


## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur [Azure Compute Units (ACU)](acu.md) kan hjälpa dig att jämföra beräknings prestanda i Azure SKU: er.
