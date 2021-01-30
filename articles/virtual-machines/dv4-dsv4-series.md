---
title: DV4 och Dsv4-serien – Azure Virtual Machines
description: Specifikationer för virtuella datorer med DV4 och Dsv4-serien.
author: brbell
ms.author: brbell
ms.reviewer: cynthn
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: aea0a79d1ae27d987a6a30cfefe91e875f9a8382
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99097514"
---
# <a name="dv4-and-dsv4-series"></a>DV4- och Dsv4-serien

DV4 och Dsv4-serien körs på Intel &reg; Xeon &reg; platina 8272CL-processorer (Cascade Lake) i en Hyper-threadd konfiguration, vilket ger ett bättre värde för de flesta allmänna arbets belastningar. Den har en central Turbo klock hastighet på 3,4 GHz. 

> [!NOTE]
> För vanliga frågor, se  [Azure VM-storlekar utan en lokal temporär disk](azure-vms-no-temp-disk.md).
## <a name="dv4-series"></a>DV4-serien

DV4-seriens storlekar körs på Intel &reg; Xeon &reg; platina-8272CL (överlappande sjö). Storlekarna i DV4-serien erbjuder en kombination av alternativ för vCPU, minne och Fjärrlagring för de flesta produktions arbets belastningar. Virtuella datorer i DV4-serien har [Intel &reg; Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html).

Lagring av fjärrdata-datadisk faktureras separat från virtuella datorer. Använd Dsv4-storlekarna om du vill använda Premium Storage-diskar. Pris-och debiterings mätare för Dsv4-storlekar är samma som för DV4-serien.

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): stöds inte<br>
[Premium Storage caching](premium-storage-performance.md): stöds inte<br>
[Direktmigrering](maintenance-and-updates.md): stöds<br>
[Minnes bebetjänings uppdateringar](maintenance-and-updates.md): stöds<br>
[Stöd för VM-generering](generation-2.md): generation 1<br>
[Accelererat nätverk](../virtual-network/create-vm-accelerated-networking-cli.md): stöds<br>
[Tillfälliga OS-diskar](ephemeral-os-disks.md): stöds inte <br>
<br>

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt antal nätverkskort|Förväntad nätverks bandbredd (Mbit/s) |
|---|---|---|---|---|---|---|
| Standard_D2_v4 | 2 | 8 | Endast Fjärrlagring | 4 | 2|1000 |
| Standard_D4_v4 | 4 | 16  | Endast Fjärrlagring | 8 | 2|2000 |
| Standard_D8_v4 | 8 | 32 | Endast Fjärrlagring | 16 | 4|4000 |
| Standard_D16_v4 | 16 | 64 | Endast Fjärrlagring | 32 | 8|8000 |
| Standard_D32_v4 | 32 | 128 | Endast Fjärrlagring | 32 | 8|16000 |
| Standard_D48_v4 | 48 | 192 | Endast Fjärrlagring | 32 | 8|24000 |
| Standard_D64_v4 | 64 | 256 | Endast Fjärrlagring | 32 | 8|30000 |

## <a name="dsv4-series"></a>Dsv4-serien

Dsv4-seriens storlekar körs på Intel &reg; Xeon &reg; platina-8272CL (överlappande sjö). Storlekarna i DV4-serien erbjuder en kombination av alternativ för vCPU, minne och Fjärrlagring för de flesta produktions arbets belastningar. Virtuella datorer i Dsv4-serien har [Intel &reg; Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html). Lagring av fjärrdata-datadisk faktureras separat från virtuella datorer.

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): stöds<br>
[Premium Storage caching](premium-storage-performance.md): stöds<br>
[Direktmigrering](maintenance-and-updates.md): stöds<br>
[Minnes bebetjänings uppdateringar](maintenance-and-updates.md): stöds<br>
[Stöd för VM-generering](generation-2.md): generation 1 och 2<br>
[Accelererat nätverk](../virtual-network/create-vm-accelerated-networking-cli.md): stöds<br>
[Tillfälliga OS-diskar](ephemeral-os-disks.md): stöds inte <br>
<br>

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt antal cachelagrade diskar: IOPS/MBps | Maximalt antal nätverkskort|Förväntad nätverks bandbredd (Mbit/s) |
|---|---|---|---|---|---|---|---|
| Standard_D2s_v4 | 2 | 8  | Endast Fjärrlagring | 4 | 3200/48 | 2|1000 |
| Standard_D4s_v4 | 4 | 16 | Endast Fjärrlagring | 8 | 6400/96 | 2|2000 |
| Standard_D8s_v4 | 8 | 32 | Endast Fjärrlagring | 16 | 12800/192 | 4|4000 |
| Standard_D16s_v4 | 16 | 64  | Endast Fjärrlagring | 32 | 25600/384 | 8|8000 |
| Standard_D32s_v4 | 32 | 128 | Endast Fjärrlagring | 32 | 51200/768 | 8|16000 |
| Standard_D48s_v4 | 48 | 192 | Endast Fjärrlagring | 32 | 76800/1152 | 8|24000 |
| Standard_D64s_v4 | 64 | 256 | Endast Fjärrlagring | 32 | 80000/1200 | 8|30000 |
