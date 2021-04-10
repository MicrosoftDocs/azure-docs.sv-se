---
title: Edv4- och Edsv4-serien
description: Specifikationer för virtuella datorer i Ev4, Edv4, Esv4 och Edsv4-serien.
author: brbell
ms.author: brbell
ms.reviewer: jushiman
ms.custom: mimckitt
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 02/04/2020
ms.openlocfilehash: d6edee1f349b37c274f71c9db7abac35f9c88c4a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102557122"
---
# <a name="edv4-and-edsv4-series"></a>Edv4- och Edsv4-serien

Edv4 och Edsv4-serien körs på Intel &reg; Xeon &reg; PLATINa 8272CL-processorer (Cascade Lake) i en Hyper-threadd konfiguration och är idealiska för olika minnes intensiva företags program och fungerar upp till 504 GIB RAM-minne, [Intel &reg; Turbo Boost technology 2,0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html), [Intel &reg; Hyper-Threading Technology](https://www.intel.com/content/www/us/en/architecture-and-technology/hyper-threading/hyper-threading-technology.html) och [Intel &reg; Advanced Vector Extensions 512 (Intel &reg; AVX-512)](https://www.intel.com/content/www/us/en/architecture-and-technology/avx-512-overview.html). De stöder också [Intel &reg; djup inlärnings förstärkning](https://software.intel.com/content/www/us/en/develop/topics/ai/deep-learning-boost.html). Dessa nya VM-storlekar kommer att ha 50% större lokal lagring, samt bättre IOPS för lokala diskar för både läsning och skrivning jämfört med [Ev3-/Esv3-](./ev3-esv3-series.md) storlekarna med [Gen2-VM](./generation-2.md): ar. Den har en central Turbo klock hastighet på 3,4 GHz. 

## <a name="edv4-series"></a>Edv4-serien

Edv4-seriens storlekar körs på Intel &reg; Xeon &reg; platina 8272CL-processorer (Cascade Lake). Edv4-funktionen för virtuella datorer är upp till 504 GiB RAM, förutom snabb och stor lokal SSD-lagring (upp till 2 400 GiB). De här virtuella datorerna är idealiska för minnes intensiva företags program och program som drar nytta av låg latens, lokal lagring med hög hastighet. Du kan koppla standard-SSD och standard hård disk lagring till de virtuella Edv4-datorerna. 

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): stöds inte<br>
[Premium Storage caching](premium-storage-performance.md): stöds inte<br>
[Direktmigrering](maintenance-and-updates.md): stöds<br>
[Minnes bebetjänings uppdateringar](maintenance-and-updates.md): stöds<br>
[Stöd för VM-generering](generation-2.md): generation 1 och 2<br>
[Accelererat nätverk](../virtual-network/create-vm-accelerated-networking-cli.md): stöds (*minst 4 vCPU krävs*)<br>
[Tillfälliga OS-diskar](ephemeral-os-disks.md): stöds inte <br>
<br>

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | <sup>**</sup> Maximalt cacheminne för cachelagrad och temporär lagring: IOPS/Mbit/s | Maximalt antal nätverkskort|Förväntad nätverks bandbredd (Mbit/s) |
|---|---|---|---|---|---|---|---|
| Standard_E2d_v4  | 2 | 16 | 75 | 4 | 19000/120 | 2|1000 |
| Standard_E4d_v4  | 4 | 32 | 150 | 8 | 38500/242 | 2|2000 |
| Standard_E8d_v4 | 8 | 64 | 300 | 16 | 77000/485 | 4|4000 |
| Standard_E16d_v4 | 16 | 128 | 600 | 32 | 154000/968 | 8|8000 |
| Standard_E20d_v4 | 20 | 160 | 750 | 32 | 193000/1211  | 8|10000 |
| Standard_E32d_v4 | 32 | 256 | 1200 | 32 | 308000/1936 | 8|16000 |
| Standard_E48d_v4 | 48 | 384 | 1800 | 32 | 462000/2904 | 8|24000 |
| Standard_E64d_v4 | 64 | 504 | 2400 | 32 | 615000/3872 | 8|30000 |


<sup>**</sup> Dessa IOPs-värden kan garanteras med hjälp av [virtuella Gen2-datorer](generation-2.md)

## <a name="edsv4-series"></a>Edsv4-serien

Edsv4-seriens storlekar körs på Intel &reg; Xeon &reg; platina 8272CL-processorer (Cascade Lake). Edsv4-funktionen för virtuella datorer är upp till 504 GiB RAM, förutom snabb och stor lokal SSD-lagring (upp till 2 400 GiB). De här virtuella datorerna är idealiska för minnes intensiva företags program och program som drar nytta av låg latens, lokal lagring med hög hastighet.

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
| Standard_E2ds_v4  | 2 | 16 | 75 | 4 | 19000/120 (50) | 3200/48 | 2|1000 |
| Standard_E4ds_v4  | 4 | 32 | 150 | 8 | 38500/242 (100) | 6400/96 | 2|2000 |
| Standard_E8ds_v4 | 8 | 64 | 300 | 16 | 77000/485 (200) | 12800/192 | 4|4000 |
| Standard_E16ds_v4 | 16 | 128 | 600 | 32 | 154000/968 (400) | 25600/384 | 8|8000 |
| Standard_E20ds_v4 | 20 | 160 | 750 | 32 | 193000/1211 (500)  | 32000/480  | 8|10000 |
| Standard_E32ds_v4 | 32 | 256 | 1200 | 32 | 308000/1936 (800) | 51200/768  | 8|16000 |
| Standard_E48ds_v4 | 48 | 384 | 1800 | 32 | 462000/2904 (1200) | 76800/1152 | 8|24000 |
| Standard_E64ds_v4 <sup>1</sup> | 64 | 504 | 2400 | 32 | 615000/3872 (1600) | 80000/1200 | 8|30000 |
| Standard_E80ids_v4 <sup>2</sup> | 80 | 504 | 2400 | 32 | 615000/3872 (1600) | 80000/1200 | 8|30000 |

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
