---
title: Dv2- och DSv2-serien – Azure Virtual Machines
description: Specifikationer för de virtuella datorerna i Dv2- och Dsv2-serien.
author: joelpelley
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 71a4cebcc11657566f65f53508df18efe822c409
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107514792"
---
# <a name="dv2-and-dsv2-series"></a>Dv2- och DSv2-serien

Dv2- och DSv2-serien, som är en uppföljning till den ursprungliga D-serien, har en kraftfullare processor och optimal processor-till-minne-konfiguration, vilket gör dem lämpliga för de flesta produktionsarbetsbelastningar. Dv2-serien är ungefär 35 % snabbare än D-serien. Dv2-serien körs på Intel® Xeon® Dvd 8272CL (Cascade Lake), Intel® Xeon® 8171M 2.1GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) eller Intel® Xeon® E5-2673 v3 2,4 GHz-processorer (Haswell) med Intel Turbo Boost Technology 2.0. Dv2-serien har samma minnes- och diskkonfigurationer som D-serien.

## <a name="dv2-series"></a>Dv2-serien

Storlekarna i Dv2-serien körs på Intel® Xeon®Hz 8272CL (Cascade Lake), Intel® Xeon® 8171M 2.1GHz (Skylake) eller Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) eller Intel® Xeon® E5-2673 v3 2,4 GHz-processorer (Haswell) med Intel Turbo Boost Technology 2.0.

[ACU:](acu.md)210-250<br>
[Premium Storage:](premium-storage-performance.md)Stöds inte<br>
[Premium Storage cachelagring:](premium-storage-performance.md)Stöds inte<br>
[direktmigrering](maintenance-and-updates.md): Stöds<br>
[Minnesbevarande uppdateringar:](maintenance-and-updates.md)Stöds<br>
[Stöd för VM-generering:](generation-2.md)Generation 1<br>
[Accelererat nätverk:](../virtual-network/create-vm-accelerated-networking-cli.md)Stöds (*Kräver minst 2 virtuella processor)*<br>
[Tillfälliga OS-diskar:](ephemeral-os-disks.md)Stöds inte <br>
<br>

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt dataflöde för temporär lagring: IOPS/läs-MBps/skriv-MBps | Maximalt antal datadiskar | Dataflöde: IOPS | Maximalt antal nätverkskort | Förväntad nätverksbandbredd (Mbit/s) |
|---|---|---|---|---|---|---|---|---|
| Standard_D1_v2 | 1  | 3.5 | 50  | 3 000/46/23    | 4  | 4 × 500  | 2|750   |
| Standard_D2_v2 | 2  | 7   | 100 | 6 000/93/46    | 8  | 8 × 500  | 2|1500  |
| Standard_D3_v2 | 4  | 14  | 200 | 12 000/187/93  | 16 | 16 × 500 | 4|3000  |
| Standard_D4_v2 | 8  | 28  | 400 | 24 000/375/187 | 32 | 32 × 500 | 8|6000  |
| Standard_D5_v2 | 16 | 56  | 800 | 48 000/750/375 | 64 | 64x500 | 8|12000 |

## <a name="dsv2-series"></a>DSv2-serien

Storlekar i DSv2-serien körs på Intel® Xeon® Xi 8272CL (Cascade Lake), Intel® Xeon® 8171M 2.1GHz (Skylake) eller Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) eller Intel® Xeon® E5-2673 v3 2,4 GHz-processorer (Haswell) med Intel Turbo Boost Technology 2.0 och använda premiumlagring.

[ACU:](acu.md)210-250<br>
[Premium Storage:](premium-storage-performance.md)Stöds<br>
[Premium Storage cachelagring:](premium-storage-performance.md)Stöds<br>
[direktmigrering:](maintenance-and-updates.md)Stöds<br>
[Minnesbevarande uppdateringar:](maintenance-and-updates.md)Stöds<br>
[Stöd för VM-generering:](generation-2.md)Generation 1 och 2<br>
[Accelererat nätverk:](../virtual-network/create-vm-accelerated-networking-cli.md)Stöds (*Kräver minst 2 vCPU)*<br>
[Tillfälliga OS-diskar: Stöds](ephemeral-os-disks.md) <br>
<br>

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt dataflöde för cachelagrad och temporär lagring: IOPS/MBps (cachestorlek i GiB) | Maximalt dataflöde för okorkopplad disk: IOPS/Mops | Maximalt antal nätverkskort|Förväntad nätverksbandbredd (Mbit/s) |
|---|---|---|---|---|---|---|---|---|
| Standard_DS1_v2 | 1  | 3.5 | 7   | 4  | 4000/32 (43)    | 3200/48   | 2|750   |
| Standard_DS2_v2 | 2  | 7   | 14  | 8  | 8000/64 (86)    | 6400/96   | 2|1500  |
| Standard_DS3_v2 | 4  | 14  | 28  | 16 | 16000/128 (172) | 12800/192 | 4|3000  |
| Standard_DS4_v2 | 8  | 28  | 56  | 32 | 32000/256 (344) | 25600/384 | 8|6000  |
| Standard_DS5_v2 | 16 | 56  | 112 | 64 | 64000/512 (688) | 51200/768 | 8|12000 |

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Andra storlekar och information

- [Generell användning](sizes-general.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare generationer](sizes-previous-gen.md)

Priskalkylator: [Priskalkylator](https://azure.microsoft.com/pricing/calculator/)

Mer information om disktyper: [Disktyper](./disks-types.md#ultra-disk)

## <a name="next-steps"></a>Nästa steg

Läs mer om hur [Azure-beräkningsenheter (ACU) kan](acu.md) hjälpa dig att jämföra beräkningsprestanda mellan Azure-SKU:er.
