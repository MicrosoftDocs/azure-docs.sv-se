---
title: Minnesoptimerade Dv2-och DSv2-serien VM – Azure Virtual Machines
description: Specifikationer för virtuella datorer med Dv2 och DSv2-serien.
author: joelpelley
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 1a8cab2529632316c86ab5a0a86c6699182811ba
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98917023"
---
# <a name="memory-optimized-dv2-and-dsv2-series"></a>Minnesoptimerade Dv2 och Dsv2-serien

Dv2 och Dsv2-serien, som är en uppföljning av den ursprungliga D-serien, har en mer kraftfull processor. DSv2-seriens storlekar körs på Intel® Xeon® platina 8272CL (överlappar sjö), Intel® Xeon® 8171M 2,1 GHz (Skylake) eller Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) eller Intel® Xeon® E5-2673 v3 2,4 GHz-processorer (Haswell). Dv2-serien har samma minnes- och diskkonfigurationer som D-serien.

## <a name="dv2-series-11-15"></a>Dv2-serien 11-15

Dv2-seriens storlekar körs på Intel® Xeon® platina 8272CL (överlappar sjö), Intel® Xeon® 8171M 2,1 GHz (Skylake) eller Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) eller Intel® Xeon® E5-2673 v3 2,4 GHz-processorer (Haswell).

[ACU](acu.md): 210-250<br>
[Premium Storage](premium-storage-performance.md): stöds inte<br>
[Premium Storage caching](premium-storage-performance.md): stöds inte<br>
[Direktmigrering](maintenance-and-updates.md): stöds<br>
[Minnes bebetjänings uppdateringar](maintenance-and-updates.md): stöds<br>
[Stöd för VM-generering](generation-2.md): generation 1<br>
[Accelererat nätverk](../virtual-network/create-vm-accelerated-networking-cli.md): stöds<br>
<br> 

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt data flöde för temporär lagring: IOPS/Read Mbit/s/Write Mbit/s | Maximalt antal data diskar/data flöde: IOPS | Maximalt antal nätverkskort|Förväntad nätverks bandbredd (Mbit/s) |
|---|---|---|---|---|---|---|---|
| Standard_D11_v2 | 2  | 14  | 100 | 6 000/93/46    | 8/8x500   | 2|1500  |
| Standard_D12_v2 | 4  | 28  | 200 | 12 000/187/93  | 16/16x500 | 4|3000  |
| Standard_D13_v2 | 8  | 56  | 400 | 24 000/375/187 | 32/32x500 | 8|6000  |
| Standard_D14_v2 | 16 | 112 | 800 | 48 000/750/375 | 64/64x500 | 8|12000 |
| Standard_D15_v2 <sup>1</sup> | 20 | 140 | 1000 | 60000/937/468 | 64/64x500 | 8|25000 <sup>2</sup> |

<sup>1</sup> instans är isolerad till maskin vara som är dedikerad till en enda kund.
<sup>2</sup> 25000 Mbit/s med accelererat nätverk.

## <a name="dsv2-series-11-15"></a>DSv2-serien 11-15

DSv2-seriens storlekar körs på Intel® Xeon® platina 8272CL (överlappar sjö), Intel® Xeon® 8171M 2,1 GHz (Skylake) eller Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) eller Intel® Xeon® E5-2673 v3 2,4 GHz-processorer (Haswell).

[ACU](acu.md): 210-250 <sup>1</sup><br>
[Premium Storage](premium-storage-performance.md): stöds<br>
[Premium Storage caching](premium-storage-performance.md): stöds<br>
[Direktmigrering](maintenance-and-updates.md): stöds<br>
[Minnes bebetjänings uppdateringar](maintenance-and-updates.md): stöds<br>
[Stöd för VM-generering](generation-2.md): generation 1 och 2<br>
[Accelererat nätverk](../virtual-network/create-vm-accelerated-networking-cli.md): stöds<br>
<br> 

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Högsta cachelagrade data flöde för cache och temporär lagring: IOPS/Mbit/s (cachestorlek i GiB) | Maximalt antal cachelagrade diskar: IOPS/MBps | Maximalt antal nätverkskort|Förväntad nätverks bandbredd (Mbit/s) |
| --- | --- | --- | --- | --- | --- | --- | --- |---|
| Standard_DS11_v2 <sup>3</sup> | 2  | 14  | 28  | 8  | 8000/64 (72)    | 6400/96   | 2|1500  |
| Standard_DS12_v2 <sup>3</sup> | 4  | 28  | 56  | 16 | 16000/128 (144) | 12800/192 | 4|3000  |
| Standard_DS13_v2 <sup>3</sup> | 8  | 56  | 112 | 32 | 32000/256 (288) | 25600/384 | 8|6000  |
| Standard_DS14_v2 <sup>3</sup> | 16 | 112 | 224 | 64 | 64000/512 (576) | 51200/768 | 8|12000 |
| Standard_DS15_v2 <sup>2</sup> | 20 | 140 | 280 | 64 | 80000/640 (720) | 64000/960 | 8|25000 <sup>4</sup> |

<sup>1</sup> det maximala disk data flödet (IOPS eller Mbit/s) möjligt med en virtuell dator i DSv2-serien kan begränsas av antalet, storlek och striping av de anslutna diskarna.  Mer information finns i [utforma för hög prestanda](./premium-storage-performance.md).
<sup>2</sup>  -instansen är isolerad till den Intel Haswell-baserade maskin varan och är dedikerad till en enda kund.  
<sup>3</sup> begränsade kärn storlekar är tillgängliga.  
<sup>4</sup> 25000 Mbit/s med accelererat nätverk.

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