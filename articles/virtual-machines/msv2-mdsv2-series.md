---
title: Msv2/Mdsv2 Medium Memory Series – Azure Virtual Machines
description: Specifikationer för de virtuella datorerna i Msv2-serien.
author: ayshakeen
ms.service: virtual-machines
ms.subservice: vm-sizes-memory
ms.topic: conceptual
ms.date: 04/07/2020
ms.author: jushiman
ms.openlocfilehash: d85623184ad52fb0d4acd4c49d08badfaf886b30
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107728238"
---
# <a name="msv2-and-mdsv2-series-medium-memory"></a>Medelminne i Msv2- och Mdsv2-serien

VM-serien Msv2 och Mdsv2 Medium Memory har Intel® Xeon® Flash 8280-processorn (Cascade Lake) med en basfrekvens på 2,7 GHz och 4,0 GHz enkärnig turbofrekvens. Med dessa virtuella datorer får kunderna ökad flexibilitet med lokala diskalternativ och disklösa alternativ. Kunder har också tillgång till en uppsättning nya isolerade VM-storlekar med mer cpu och minne som går upp till 192 vCPU med 4 TiB minne. 


Virtuella datorer i Msv2- och Mdsv2-serien är endast i generation 2 och stöder en delmängd av avbildningar som stöds av generation 2. Nedan finns en fullständig lista över avbildningar som stöds för Msv2- och Mdsv2-serien.  

- Windows Server 2019 eller senare
- SUSE Linux Enterprise Server 12 SP4 och senare eller SUSE Linux Enterprise Server 15 SP1 och senare
- Red Hat Enterprise Linux 7.6, 7.7, 8.1 eller senare 
- Oracle Enterprise Linux 7.7 eller senare

Mer information om virtuella datorer av andra generationen finns i [Support for generation 2 VMs on Azure (Stöd för virtuella datorer i generation 2 på Azure).](./generation-2.md)



[Premium Storage:](premium-storage-performance.md)Stöds<br>
[Premium Storage cachelagring:](premium-storage-performance.md)Stöds<br>
[direktmigrering:](maintenance-and-updates.md)Stöds inte<br>
[Minnesbevarande uppdateringar:](maintenance-and-updates.md)stöds inte<br>
[Stöd för VM-generering:](generation-2.md)Generation 2<br>
[Skrivningsaccelerator:](./how-to-enable-write-accelerator.md)Stöds<br>
[Accelererat nätverk:](../virtual-network/create-vm-accelerated-networking-cli.md)Stöds<br>
[Tillfälliga OS-diskar:](ephemeral-os-disks.md)Stöds inte <br>
<br>
 
## <a name="msv2-medium-memory-diskless"></a>Msv2 Medium Memory Diskless 

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt dataflöde för okorkopplad disk: IOPS/Mops | Maximalt antal nätverkskort | Förväntad nätverksbandbredd (Mbit/s) | 
|---|---|---|---|---|---|---|---|
| Standard_M32ms_v2 | 32 | 875 | 0 | 32 |  20000/500 | 8 | 8000 | 
| Standard_M64s_v2 | 64 | 1024 | 0 | 64 | 40000/1000 | 8 | 16000 | 
| Standard_M64ms_v2 | 64 | 1792 | 0 | 64 | 40000/1000 | 8 | 16000 | 
| Standard_M128s_v2 | 128 | 2048 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M128ms_v2 | 128 | 3892 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M192is_v2 | 192 | 2048 | 0 | 64 | 80000/2000 | 8 | 30000 | 
| Standard_M192ims_v2 | 192 | 4096 | 0 | 64 | 80000/2000 | 8 | 30000 | 

## <a name="mdsv2-medium-memory-with-disk"></a>Mdsv2 medium minne med disk  

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt cachelagrat och temporärt lagringsgenomflöde: IOPS/MBps | Maximalt dataflöde för obekopplad disk: IOPS/MBps | Maximalt antal nätverkskort | Förväntad nätverksbandbredd (Mbit/s) | 
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

Priskalkylator: [Priskalkylator](https://azure.microsoft.com/pricing/calculator/)

## <a name="next-steps"></a>Nästa steg

Läs mer om hur [Azure-beräkningsenheter (ACU) kan hjälpa](acu.md) dig att jämföra beräkningsprestanda mellan Azure-SKU:er.
