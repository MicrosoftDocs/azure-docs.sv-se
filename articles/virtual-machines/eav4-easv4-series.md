---
title: Eav4-serien och Easv4-serien
description: Specifikationer för virtuella datorer med Eav4 och Easv4-serien.
author: migerdes
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: ec320d7eeebbe5eab14061cd5c194f15acf5638a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102557938"
---
# <a name="eav4-and-easv4-series"></a>Eav4- och Easv4-serien

Eav4-serien och Easv4-serien använder AMD: s 2.35 GHz EPYC<sup>TM</sup> 7452-processor i en multi-threadd konfiguration med upp till 256 MB L3-cache, vilket ökar alternativen för att köra de flesta minnesoptimerade arbets belastningar. Eav4-serien och Easv4-serien har samma minnes-och diskkonfigurationer som Ev3 & Esv3-serien.

## <a name="eav4-series"></a>Eav4-serien

[ACU](acu.md): 230-260<br>
[Premium Storage](premium-storage-performance.md): stöds inte<br>
[Premium Storage caching](premium-storage-performance.md): stöds inte<br>
[Direktmigrering](maintenance-and-updates.md): stöds<br>
[Minnes bebetjänings uppdateringar](maintenance-and-updates.md): stöds<br>
[Stöd för VM-generering](generation-2.md): generation 1 och 2<br>
[Accelererat nätverk](../virtual-network/create-vm-accelerated-networking-cli.md): stöds (*minst 4 vCPU krävs*)<br>
[Tillfälliga OS-diskar](ephemeral-os-disks.md): stöds <br>
<br>

Eav4-seriens storlekar baseras på 2.35 GHz AMD EPYC<sup>TM</sup> 7452-processorn som kan uppnå en högre högsta frekvens på 3.35 GHz. Eav4-seriens storlek är idealiska för minnes intensiva företags program. Datadisklagring faktureras separat från virtuella datorer. Om du vill använda Premium SSD använder du Easv4-seriens storlekar. Pris-och debiterings mätare för Easv4-storlekar är samma som för Eav3-serien.

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för temporär lagring: IOPS / Mbit/s för läsning / M/bit/s för skrivning | Maximalt antal nätverkskort | Förväntad nätverks bandbredd (Mbit/s) |
| -----|-----|-----|-----|-----|-----|-----|-----|
| Standard \_ E2a \_ v4|2|16|50|4|3 000 / 46 / 23|2 | 800 |
| Standard \_ e4a \_ v4|4|32|100|8|6 000 / 93 / 46|2 | 1600 |
| Standard \_ E8a \_ v4|8|64|200|16|12 000 / 187 / 93|4 | 3200 |
| Standard \_ E16a \_ v4|16|128|400|32|24 000 / 375 / 187|8 | 6400 |
| Standard \_ E20a \_ v4|20|160|500|32|30000/468/234|8 | 8000 |
| Standard \_ E32a \_ v4|32|256|800|32|48 000 / 750 / 375|8 | 12800 |
| Standard \_ E48a \_ v4|48|384|1200|32|96000/1000 (500)|8 | 19200 |
| Standard \_ E64a \_ v4|64|512|1600|32|96000/1000 (500)|8 | 25600 |
| Standard \_ E96a \_ v4|96|672|2400|32|96000/1000 (500)|8 | 32000 |

## <a name="easv4-series"></a>Easv4-serien

[ACU](acu.md): 230-260<br>
[Premium Storage](premium-storage-performance.md): stöds<br>
[Premium Storage caching](premium-storage-performance.md): stöds<br>
[Direktmigrering](maintenance-and-updates.md): stöds<br>
[Minnes bebetjänings uppdateringar](maintenance-and-updates.md): stöds<br>
[Stöd för VM-generering](generation-2.md): generation 1 och 2<br>
[Accelererat nätverk](../virtual-network/create-vm-accelerated-networking-cli.md): stöds (*minst 4 vCPU krävs*)<br>
[Tillfälliga OS-diskar](ephemeral-os-disks.md): stöds <br>
<br>

Easv4-seriens storlekar baseras på 2.35 GHz AMD EPYC<sup>TM</sup> 7452-processorn som kan uppnå en högre högsta frekvens på 3.35 GHz och använder Premium SSD. Easv4-seriens storlek är idealiska för minnes intensiva företags program.

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för cachelagring och temporär lagring: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Maximalt antal nätverkskort | Förväntad nätverks bandbredd (Mbit/s) |
|-----|-----|-----|-----|-----|-----|-----|-----|-----|
| Standard_E2as_v4|2|16|32|4|4000/32 (50)|3200/48|2 | 800 |
| Standard_E4as_v4|4|32|64|8|8000/64 (100)|6400/96|2 | 1600 |
| Standard_E8as_v4|8|64|128|16|16000/128 (200)|12800/192|4 | 3200 |
| Standard_E16as_v4|16|128|256|32|32000/255 (400)|25600/384|8 | 6400 |
| Standard_E20as_v4|20|160|320|32|40000/320 (500)|32000/480|8 | 8000 |
| Standard_E32as_v4|32|256|512|32|64000/510 (800)|51200/768|8 | 12800 |
| Standard_E48as_v4|48|384|768|32|96000/1020 (1200)|76800/1148|8 | 19200 |
| Standard_E64as_v4|64|512|1024|32|128000/1020 (1600)|80000/1200|8 | 25600 |
| Standard_E96as_v4 <sup>1</sup>|96|672|1344|32|192000/1020 (2400)|80000/1200|8 | 32000 |

<sup>1</sup> [begränsad kärn storlek är tillgänglig](./constrained-vcpu.md).

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
