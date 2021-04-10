---
title: Ev4 och Esv4-serien – Azure Virtual Machines
description: Specifikationer för Ev4 och Esv4-seriens virtuella datorer.
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 6/8/2020
ms.openlocfilehash: 40ca03114d5a27ad3e5be54d75cb3bbe0221a501
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102560301"
---
# <a name="ev4-and-esv4-series"></a>Ev4- och Esv4-serien

Ev4 och Esv4-serien körs på Intel &reg; Xeon &reg; platina-8272CL (Cascade Lake) i en Hyper-threadd konfiguration, är idealiska för olika minnes intensiva företags program och funktioner upp till 504GiB RAM-minne. Den har en central Turbo klock hastighet på 3,4 GHz.

> [!NOTE]
> För vanliga frågor, se  [Azure VM-storlekar utan en lokal temporär disk](azure-vms-no-temp-disk.md).

## <a name="ev4-series"></a>Ev4-serien

Ev4-seriens storlekar körs på Intel Xeon &reg; platina-8272CL (överlappande sjö). Instanserna i Ev4-serien är idealiska för minnes intensiva företags program. Virtuella datorer i Ev4-serien har Intel &reg; Hyper-Threading Technology.

Lagring av fjärrdata-datadisk faktureras separat från virtuella datorer. Använd Esv4-storlekarna om du vill använda Premium Storage-diskar. Pris-och debiterings mätare för Esv4-storlekar är samma som för Ev4-serien.

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): stöds inte<br>
[Premium Storage caching](premium-storage-performance.md): stöds inte<br>
[Direktmigrering](maintenance-and-updates.md): stöds<br>
[Minnes bebetjänings uppdateringar](maintenance-and-updates.md): stöds<br>
[Stöd för VM-generering](generation-2.md): generation 1<br>
[Accelererat nätverk](../virtual-network/create-vm-accelerated-networking-cli.md): stöds (*minst 4 vCPU krävs*)<br>
[Tillfälliga OS-diskar](ephemeral-os-disks.md): stöds inte <br>
<br>

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt antal nätverkskort|Förväntad nätverks bandbredd (Mbit/s) |
|---|---|---|---|---|---|---|
| Standard_E2_v4  | 2 | 16   | Endast Fjärrlagring | 4 | 2|1000  |
| Standard_E4_v4  | 4 | 32  | Endast Fjärrlagring | 8 | 2|2000  |
| Standard_E8_v4  | 8 | 64 | Endast Fjärrlagring | 16 | 4|4000 |
| Standard_E16_v4 | 16 | 128 | Endast Fjärrlagring | 32 | 8|8000 |
| Standard_E20_v4 | 20 | 160 | Endast Fjärrlagring | 32 | 8|10000 |
| Standard_E32_v4 | 32 | 256 | Endast Fjärrlagring | 32 | 8|16000 |
| Standard_E48_v4 | 48 | 384 | Endast Fjärrlagring | 32 | 8|24000 |
| Standard_E64_v4 | 64 | 504 | Endast Fjärrlagring | 32| 8|30000 |


## <a name="esv4-series"></a>Esv4-serien

Esv4-seriens storlekar körs på Intel &reg; Xeon &reg; platina-8272CL (överlappande sjö). Instanserna i Esv4-serien är idealiska för minnes intensiva företags program. Virtuella datorer i Evs4-serien har Intel &reg; Hyper-Threading Technology. Lagring av fjärrdata-datadisk faktureras separat från virtuella datorer.

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): stöds<br>
[Premium Storage caching](premium-storage-performance.md): stöds<br>
[Direktmigrering](maintenance-and-updates.md): stöds<br>
[Minnes bebetjänings uppdateringar](maintenance-and-updates.md): stöds<br>
[Stöd för VM-generering](generation-2.md): generation 1 och 2<br>
[Accelererat nätverk](../virtual-network/create-vm-accelerated-networking-cli.md): stöds (*minst 4 vCPU krävs*)<br>
[Tillfälliga OS-diskar](ephemeral-os-disks.md): stöds inte <br>
<br>

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt antal cachelagrade diskar: IOPS/MBps | Maximalt antal nätverkskort|Förväntad nätverks bandbredd (Mbit/s) |
|---|---|---|---|---|---|---|---|
| Standard_E2s_v4  | 2 | 16  | Endast Fjärrlagring | 4 | 3200/48 | 2|1000  |
| Standard_E4s_v4  | 4 | 32  | Endast Fjärrlagring | 8 | 6400/96 | 2|2000  |
| Standard_E8s_v4  | 8 | 64  | Endast Fjärrlagring | 16 | 12800/192 | 4|4000 |
| Standard_E16s_v4 | 16 | 128 | Endast Fjärrlagring | 32 | 25600/384 | 8|8000 |
| Standard_E20s_v4 | 20 | 160 | Endast Fjärrlagring | 32 | 32000/480  | 8|10000 |
| Standard_E32s_v4 | 32 | 256 | Endast Fjärrlagring | 32 | 51200/768  | 8|16000 |
| Standard_E48s_v4 | 48 | 384 | Endast Fjärrlagring | 32 | 76800/1152 | 8|24000 |
| Standard_E64s_v4 <sup>1</sup> | 64 | 504| Endast Fjärrlagring | 32 | 80000/1200 | 8|30000 |
| Standard_E80is_v4 <sup>2</sup> | 80 | 504 | Endast Fjärrlagring | 32 | 80000/1200 | 8|30000 |

<sup>1</sup> [begränsade kärn storlekar är tillgängliga)](./constrained-vcpu.md).

<sup>2</sup> -instansen är isolerad till maskin vara som är dedikerad till en enda kund.

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
