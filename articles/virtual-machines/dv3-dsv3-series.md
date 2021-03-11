---
title: Dv3- och Dsv3-serien
description: Specifikationer för virtuella datorer med Dv3 och Dsv3-serien.
author: joelpelley
ms.service: virtual-machines
ms.subservice: vm-sizes-general
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: jushiman
ms.openlocfilehash: 22400164ac82df3ec0e0e24daf6d823eae219837
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102566438"
---
# <a name="dv3-and-dsv3-series"></a>Dv3- och Dsv3-serien

Dv3-serien körs på Intel® Xeon® platina 8272CL (överlappar sjö), Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) eller Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell)-processorer i en Hyper-threadad konfiguration, vilket ger ett bättre värde för de flesta allmänna arbets belastningar Minnet har utökats (från ~ 3,5 GiB/vCPU till 4 GiB/vCPU) medan disk-och nätverks gränser har justerats per kärna för att justeras efter flytta till hyperthreading. Dv3-serien har inte längre hög minnes storlek för virtuella datorer i D/Dv2-serien, de har flyttats till den minnesoptimerade [Ev3 och Esv3-serien](ev3-esv3-series.md).

Exempel på användnings områden i D-serien är program i företags klass, Relations databaser, minnes intern cachelagring och analys.

## <a name="dv3-series"></a>Dv3-serien

Dv3-seriens storlekar körs på Intel® Xeon® platina 8272CL (Cascade Lake), Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) eller Intel® Xeon® E5-2673 v3 2,4 GHz (Haswell) med [Intel &reg; Turbo Boost Technology 2,0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html). Storlekarna i Dv3-serien erbjuder en kombination av virtuella processorer, minne och temporär lagring som passar de flesta produktionsarbetsbelastningar.

Datadisklagring faktureras separat från virtuella datorer. Om du vill använda premiumlagringsdiskar använder du Dsv3-storlekarna. Pris- och debiteringsmätarna för Dsv3-storlekar är samma som för Dv3-serien.

Virtuella datorer i dv3-serien Intel® Hyper-Threading-teknik.

[ACU](acu.md): 160-190<br>
[Premium Storage](premium-storage-performance.md): stöds inte<br>
[Premium Storage caching](premium-storage-performance.md): stöds inte<br>
[Direktmigrering](maintenance-and-updates.md): stöds<br>
[Minnes bebetjänings uppdateringar](maintenance-and-updates.md): stöds<br>
[Stöd för VM-generering](generation-2.md): generation 1<br>
[Accelererat nätverk](../virtual-network/create-vm-accelerated-networking-cli.md): stöds (*minst 4 vCPU krävs*)<br>
[Tillfälliga OS-diskar](ephemeral-os-disks.md): stöds inte <br>
<br>

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt data flöde för temporär lagring: IOPS/Read Mbit/s/Write Mbit/s | Högsta antal nätverkskort/nätverks bandbredd |
|---|---|---|---|---|---|---|
| Standard_D2_v3  | 2  | 8   | 50   | 4  | 3 000/46/23     | 2/1000  |
| Standard_D4_v3  | 4  | 16  | 100  | 8  | 6 000/93/46     | 2/2000  |
| Standard_D8_v3  | 8  | 32  | 200  | 16 | 12 000/187/93   | 4/4000  |
| Standard_D16_v3 | 16 | 64  | 400  | 32 | 24 000/375/187  | 8/8000  |
| Standard_D32_v3 | 32 | 128 | 800  | 32 | 48 000/750/375  | 8/16000 |
| Standard_D48_v3 | 48 | 192 | 1200 | 32 | 96 000/1 000/500 | 8/24000 |
| Standard_D64_v3 | 64 | 256 | 1600 | 32 | 96 000/1 000/500 | 8/30000 |

## <a name="dsv3-series"></a>Dsv3-serien

Dsv3-seriens storlekar körs på Intel® Xeon® platina 8272CL (överlappar sjö), Intel® Xeon® 8171M 2.1 GHz (Skylake), Intel® Xeon® E5-2673 v4 2,3 GHz (Broadwell) eller Intel® Xeon® E5-2673 v3 2,4 GHz-processorer (Haswell) med [Intel &reg; Turbo Boost Technology 2,0](https://www.intel.com/content/www/us/en/architecture-and-technology/turbo-boost/turbo-boost-technology.html) och Använd Premium Storage. Storlekarna i Dsv3-serien erbjuder en kombination av virtuella processorer, minne och temporär lagring som passar de flesta produktionsarbetsbelastningar.

Virtuella datorer i Dsv3-serien Intel® Hyper-Threading-teknik.

[ACU](acu.md): 160-190<br>
[Premium Storage](premium-storage-performance.md): stöds<br>
[Premium Storage caching](premium-storage-performance.md): stöds<br>
[Direktmigrering](maintenance-and-updates.md): stöds<br>
[Minnes bebetjänings uppdateringar](maintenance-and-updates.md): stöds<br>
[Stöd för VM-generering](generation-2.md): generation 1 och 2<br>
[Accelererat nätverk](../virtual-network/create-vm-accelerated-networking-cli.md): stöds (*minst 4 vCPU krävs*)<br>
[Tillfälliga OS-diskar](ephemeral-os-disks.md): stöds <br>
<br>

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Högsta cachelagrade data flöde för cache och temporär lagring: IOPS/Mbit/s (cachestorlek i GiB) | Maximalt burst-cachelagrat och temporärt lagrings utrymme: IOPS/MBps<sup>1</sup> | Maximalt antal cachelagrade diskar: IOPS/MBps | Högsta burst-överförda disk data flöde: IOPS/Mbit/s<sup>1</sup> | Högsta antal nätverkskort/förväntad nätverks bandbredd (Mbit/s) |
|---|---|---|---|---|---|---|---|---|---|
| Standard_D2s_v3  | 2  | 8   | 16  | 4  | 4000/32 (50)       | 4000/100    |3200/48    | 4000/100   | 2/1000  |
| Standard_D4s_v3  | 4  | 16  | 32  | 8  | 8000/64 (100)      | 8000/200    |6400/96    | 8000/200   | 2/2000  |
| Standard_D8s_v3  | 8  | 32  | 64  | 16 | 16000/128 (200)    | 16000/400   |12800/192  | 16000/400  | 4/4000  |
| Standard_D16s_v3 | 16 | 64  | 128 | 32 | 32000/256 (400)    | 32000/800   |25600/384  | 32000/800  | 8/8000  |
| Standard_D32s_v3 | 32 | 128 | 256 | 32 | 64000/512 (800)    | 64000/1600  |51200/768  | 64000/1600 | 8/16000 |
| Standard_D48s_v3 | 48 | 192 | 384 | 32 | 96000/768 (1200)   | 96000/2000  |76800/1152 | 80000/2000 | 8/24000 |
| Standard_D64s_v3 | 64 | 256 | 512 | 32 | 128000/1024 (1600) | 128000/2000 |80000/1200 | 80000/2000 | 8/30000 |

<sup>1</sup>  virtuella datorer i Dsv3- [serien kan överföra](./disk-bursting.md) disk prestanda och få upp till deras burst-Max i upp till 30 minuter i taget.

[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Andra storlekar och information

- [Generell användning](sizes-general.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare generationer](sizes-previous-gen.md)
- [Priskalkylator](https://azure.microsoft.com/pricing/calculator/)
- Mer information om disk typer finns i [vilka disk typer som är tillgängliga i Azure?](disks-types.md)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur [Azure Compute Units (ACU)](acu.md) kan hjälpa dig att jämföra beräknings prestanda i Azure SKU: er.