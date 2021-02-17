---
title: Fsv2-serien
description: Specifikationer för virtuella datorer i Fsv2-serien.
author: brbell
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: e738a3e18d4ee16b2bbfd7edb6cc32152bb82b48
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100557803"
---
# <a name="fsv2-series"></a>Fsv2-serien

Fsv2-serien körs på Intel® Xeon® platina 8272CL-processorer (Cascade Lake) och Intel® Xeon® platina 8168-processorer (Skylake). Den har en hållbar alla kärnor för Turbo Turbo klock hastighet på 3,4 GHz och en maximal Turbo frekvens på 3,7 GHz. Intel® AVX-512-instruktioner är nya på Intel Scalable processors. Dessa instruktioner ger till gång till en dubbel prestanda ökning till vektor bearbetning av arbets belastningar i både enkla och dubbla precisions åtgärder. Med andra ord är de verkligen snabba för alla beräknings arbets belastningar.

Virtuella datorer i Fsv2-serien Intel® Hyper-Threading-teknik.

[ACU](acu.md): 195-210<br>
[Premium Storage](premium-storage-performance.md): stöds<br>
[Premium Storage caching](premium-storage-performance.md): stöds<br>
[Direktmigrering](maintenance-and-updates.md): stöds<br>
[Minnes bebetjänings uppdateringar](maintenance-and-updates.md): stöds<br>
[Stöd för VM-generering](generation-2.md): generation 1 och 2<br>
[Accelererat nätverk](../virtual-network/create-vm-accelerated-networking-cli.md): stöds (*minst 4 vCPU krävs*)<br>
[Tillfälliga OS-diskar](ephemeral-os-disks.md): stöds <br>
<br>

| Storlek | vCPU | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Högsta cachelagrade data flöde för cache och temporär lagring: IOPS/Mbit/s (cachestorlek i GiB) | Maximalt antal cachelagrade diskar: IOPS/MBps | Maximalt antal nätverkskort|Förväntad nätverks bandbredd (Mbit/s) |
|---|---|---|---|---|---|---|---|---|
| Standard_F2s_v2  | 2  | 4   | 16  | 4  | 4000/31 (32)       | 3200/47    | 2|875   |
| Standard_F4s_v2  | 4  | 8   | 32  | 8  | 8000/63 (64)       | 6400/95    | 2|1750  |
| Standard_F8s_v2  | 8  | 16  | 64  | 16 | 16000/127 (128)    | 12800/190  | 4|3500  |
| Standard_F16s_v2 | 16 | 32  | 128 | 32 | 32000/255 (256)    | 25600/380  | 4|7000  |
| Standard_F32s_v2 | 32 | 64  | 256 | 32 | 64000/512 (512)    | 51200/750  | 8|14000 |
| Standard_F48s_v2 | 48 | 96  | 384 | 32 | 96000/768 (768)    | 76800/1100 | 8|21000 |
| Standard_F64s_v2 | 64 | 128 | 512 | 32 | 128000/1024 (1024) | 80000/1100 | 8|28000 |
| Standard_F72s_v2<sup>1, 2</sup> | 72 | 144 | 576 | 32 | 144000/1152 (1520) | 80000/1100 | 8|30000 |

<sup>1</sup> användningen av mer än 64 vCPU kräver ett av dessa gäst operativ system som stöds:

- Windows Server 2016 eller senare
- Ubuntu 16,04 LTS eller senare med Azures justerade kernel (4,15 kernel eller senare)
- SLES 12 SP2 eller senare
- RHEL eller CentOS version 6,7 till 6,10, med Microsoft-medföljande LIS-paket 4.3.1 (eller senare) installerat
- RHEL eller CentOS version 7,3, med Microsoft-tillhandahållen LIS-paket 4.2.1 (eller senare) installerat
- RHEL eller CentOS version 7,6 eller senare
- Oracle Linux med UEK4 eller senare
- Debian 9 med backports-kärnan, Debian 10 eller senare
- Core-attribut med en 4,14-kernel eller senare

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