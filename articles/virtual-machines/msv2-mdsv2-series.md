---
title: Msv2-serien (för hands version) – Azure Virtual Machines
description: Specifikationer för virtuella datorer i Msv2-serien.
author: ayshakeen
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: jushiman
ms.openlocfilehash: a7f4757467523837423d52998eb6b8204090e627
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "102562579"
---
# <a name="msv2-and-mdsv2-series-medium-memory-preview"></a>Msv2 och Mdsv2-seriens medium minne (för hands version)


> [!IMPORTANT]
> Delta i förhands granskningen genom att fylla i formuläret på **https://aka.ms/Mv2MedMemoryPreview** .  

Msv2 och Mdsv2 medels Tor VM-serien har Intel® Xeon-® Platinum 8280-processor (Cascade Lake) med en alla grund frekvens på 2,7 GHz och 4,0 GHz enkel kärna Turbo frekvens. Med de här virtuella datorerna ger kunderna ökad flexibilitet med lokala diskar och disk lösa alternativ. Kunder har också till gång till en uppsättning nya isolerade VM-storlekar med mer processor och minne som går upp till 192 vCPU med 4 TiB minne. 


Virtuella datorer med Msv2 och Mdsv2-serien är endast generation 2 och stöder en delmängd av avbildningar som stöds av generation 2. Nedan visas en fullständig lista över de avbildningar som stöds för Msv2 och Mdsv2-serien.  

- Windows Server 2019 eller senare
- SUSE Linux Enterprise Server 12 SP4 och senare eller SUSE Linux Enterprise Server 15 SP1 och senare
- Red Hat Enterprise Linux 7,6, 7,7, 8,1 eller senare 
- Oracle Enterprise Linux 7,7 eller senare

Mer information om virtuella datorer i generation 2 finns i [stöd för virtuella datorer i generation 2 på Azure](./generation-2.md).



[Premium Storage](premium-storage-performance.md): stöds<br>
[Premium Storage caching](premium-storage-performance.md): stöds<br>
[Direktmigrering](maintenance-and-updates.md): stöds inte<br>
[Minnes bebetjänings uppdateringar](maintenance-and-updates.md): stöds inte<br>
[Stöd för VM-generering](generation-2.md): generation 2<br>
[Skrivningsaccelerator](./how-to-enable-write-accelerator.md): stöds<br>
[Accelererat nätverk](../virtual-network/create-vm-accelerated-networking-cli.md): stöds<br>
[Tillfälliga OS-diskar](ephemeral-os-disks.md): stöds inte <br>
<br>
 
## <a name="msv2-medium-memory-diskless"></a>Msv2 medium minne disk utrymme 

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt antal cachelagrade diskar: IOPS/MBps | Maximalt antal nätverkskort | Förväntad nätverks bandbredd (Mbit/s) | 
|---|---|---|---|---|---|---|---|
| Standard_M32ms_v2 | 32 | 875 | 0 | 32 |  20000/500 | 8 | 8000 | 
| Standard_M64s_v2 | 64 | 1024 | 0 | 64 | 40000/1000 | 8 | 16000 | 
| Standard_M64ms_v2 | 64 | 1792 | 0 | 64 | 40000/1000 | 8 | 16000 | 
| Standard_M128s_v2 | 128 | 2048 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M128ms_v2 | 128 | 3892 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M192is_v2 | 192 | 2048 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M192ims_v2 | 192 | 4096 | 0 | 64 | 80000/2000 | 8 | 30000 | 

## <a name="mdsv2-medium-memory-with-disk"></a>Mdsv2 medel minne med disk  

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Högsta data disk | Maximalt cacheminne för cachelagrad och temporär lagring: IOPS/Mbit/s | Maximalt antal cachelagrade diskar: IOPS/MBps | Maximalt antal nätverkskort | Förväntad nätverks bandbredd (Mbit/s) | 
|---|---|---|---|---|---|---|---|---|
| Standard_M32dms_v2 | 32 | 875 | 1024 | 32 | 40000/400 | 20000/500 | 8 | 8000 | 
| Standard_M64ds_v2 | 64 | 1024 | 2048 | 64 | 80000/800 | 40000/1000 | 8 | 16000 | 
| Standard_M64dms_v2 | 64 | 1792 | 2048 | 64 | 80000/800 | 40000/1000 | 8 | 16000 | 
| Standard_M128ds_v2 | 128 | 2048 | 4096 | 64 |160000/1600 | 80000/2000 | 8 | 30000 | 
| Standard_M128dms_v2 | 128 | 3892 | 4096 | 64 | 160000/1600 | 80000/2000 | 8 | 30000 | 
| Standard_M192ids_v2 | 192 | 2048 | 4096 | 64 | 160000/1600 | 80000/2000 | 8 | 30000 | 
| Standard_M192idms_v2 | 192 | 4096 | 4096 | 64 | 160000/1600 | 80000/2000 | 8 | 30000 | 


[!INCLUDE [virtual-machines-common-sizes-table-defs](../../includes/virtual-machines-common-sizes-table-defs.md)]

## <a name="other-sizes-and-information"></a>Andra storlekar och information

- [Generell användning](sizes-general.md)
- [Minnesoptimerad](sizes-memory.md)
- [Lagringsoptimerad](sizes-storage.md)
- [GPU-optimerad](sizes-gpu.md)
- [Databehandling med höga prestanda](sizes-hpc.md)
- [Tidigare generationer](sizes-previous-gen.md)

Pris kalkylator: [pris kalkylator](https://azure.microsoft.com/pricing/calculator/)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur [Azure Compute Units (ACU)](acu.md) kan hjälpa dig att jämföra beräknings prestanda i Azure SKU: er.
