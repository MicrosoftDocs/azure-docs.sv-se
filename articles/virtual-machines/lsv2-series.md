---
title: Lsv2-serien – Azure Virtual Machines
description: Specifikationer för virtuella datorer i Lsv2-serien.
author: sasha-melamed
ms.service: virtual-machines
ms.subservice: sizes
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: jushiman
ms.openlocfilehash: 2416c0b4002f560e272393c95a86c543cb5f1009
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2021
ms.locfileid: "99097850"
---
# <a name="lsv2-series"></a>Lsv2-serien

Lsv2-serien har högt data flöde, låg latens, direkt mappad lokal NVMe-lagring som körs på [AMD EPYC<sup>TM</sup> 7551-processorn](https://www.amd.com/en/products/epyc-7000-series) med en större ökning på 2.55 GHz och en maximal förstärkning på 3,0 GHz. De virtuella datorerna i Lsv2-serien har storlekar från 8 till 80 vCPU i en samtidig multi-threading-konfiguration.  Det finns 8 GiB minne per vCPU och en 1.92 TB NVMe SSD M. 2-enhet per 8 virtuella processorer, med upp till 19,2 TB (10X 1.92 TB) tillgängligt på L80s v2.

> [!NOTE]
> De virtuella datorerna i Lsv2-serien är optimerade för att använda den lokala disken på noden som är ansluten direkt till den virtuella datorn i stället för att använda varaktiga data diskar Detta möjliggör större IOPs/data flöde för dina arbets belastningar. Lsv2 och LS-serien stöder inte att en lokal cache skapas för att öka IOPs som kan nås av varaktiga data diskar.
>
> Det höga genom strömningen och IOPs av den lokala disken gör Lsv2-seriens virtuella datorer perfekt för NoSQL-butiker som Apache Cassandra och MongoDB som replikerar data över flera virtuella datorer för att få beständighet i händelse av att en enskild virtuell dator Miss lyckas.
>
> Läs mer i optimera prestanda på virtuella datorer i Lsv2-serien för [Windows](../virtual-machines/windows/storage-performance.md) eller [Linux](../virtual-machines/linux/storage-performance.md).  

[ACU](acu.md): 150-175<br>
[Premium Storage](premium-storage-performance.md): stöds<br>
[Premium Storage caching](premium-storage-performance.md): stöds inte<br>
[Direktmigrering](maintenance-and-updates.md): stöds inte<br>
[Minnes bebetjänings uppdateringar](maintenance-and-updates.md): stöds inte<br>
[Stöd för VM-generering](generation-2.md): generation 1 och 2<br>
Bursting: stöds<br>
[Accelererat nätverk](../virtual-network/create-vm-accelerated-networking-cli.md): stöds<br>
[Tillfälliga OS-diskar](ephemeral-os-disks.md): stöds inte <br>
<br>

| Storlek | Virtuell processor | Minne (GiB) | Temporär disk<sup>1</sup> (GIB) | NVMe-diskar<sup>2</sup> | NVMe-disk data flöde<sup>3</sup> (läsa IOPS/Mbit/s) | Data flöde för datadisk i cacheminnet (IOPs/Mbit/s)<sup>4</sup> | Högsta burst-genomflöde (IOPs/Mbit/s) för burst-överföring<sup>5</sup>| Maximalt antal data diskar | Maximalt antal nätverkskort | Förväntad nätverks bandbredd (Mbit/s) |
|---|---|---|---|---|---|---|---|---|---|---|
| Standard_L8s_v2   |  8 |  64 |  80 |  1x 1.92 TB  | 400000/2000  | 8000/160   | 8000/1280 | 16 | 2 | 3200   |
| Standard_L16s_v2  | 16 | 128 | 160 |  2x 1.92 TB  | 800000/4000  | 16000/320  | 16000/1280 | 32 | 4 | 6400   |
| Standard_L32s_v2  | 32 | 256 | 320 |  4x 1.92 TB  | 1,5 meter per 8000    | 32000/640  | 32000/1280 | 32 | 8 | 12800  |
| Standard_L48s_v2  | 48 | 384 | 480 |  6x 1.92 TB  | 2,2 m/14000   | 48000/960  | 48000/2000 | 32 | 8 | 16000 + |
| Standard_L64s_v2  | 64 | 512 | 640 |  8x 1.92 TB  | 2.9 m/16000   | 64000/1280 | 64000/2000 | 32 | 8 | 16000 + |
| Standard_L80s_v2<sup>6</sup> | 80 | 640 | 800 | 10X 1.92 TB | 3.8 m/20000 | 80000/1400 | 80000/2000 | 32 | 8 | 16000 + |

<sup>1</sup> virtuella datorer i Lsv2-serien har en SCSI-baserad temporär resurs disk för växlings-/växlings fil för operativ system (D: i Windows,/dev/SDB på Linux). Den här disken ger 80 GiB lagring, 4 000 IOPS och 80 MBps-överförings takt för varje 8-virtuella processorer (t. ex. Standard_L80s_v2 tillhandahåller 800 GiB vid 40 000 IOPS och 800 MBIT/s). Detta säkerställer att NVMe-enheter kan användas fullt ut för användning av program. Den här disken är tillfällig och alla data går förlorade vid stopp/frigörning.

<sup>2</sup> lokala NVMe-diskar är tillfälliga. data kommer att gå förlorade på diskarna om du stoppar/frigör den virtuella datorn.

<sup>3</sup> Hyper-V NVMe Direct-teknik ger obegränsad åtkomst till lokala NVMe-enheter som mappats säkert till det virtuella gäst dator utrymmet.  För att uppnå högsta prestanda måste du använda antingen den senaste WS2019-versionen eller Ubuntu 18,04 eller 16,04 från Azure Marketplace.  Skriv prestanda varierar beroende på IO-storlek, enhets belastning och kapacitets användning.

<sup>4</sup> virtuella datorer i Lsv2-serien tillhandahåller inte värd-cache för datadisk eftersom den inte drar nytta av Lsv2-arbetsbelastningar.

Virtuella datorer med <sup>5</sup> Lsv2- [serien kan överföra](./disk-bursting.md) disk prestanda i upp till 30 minuter i taget. 

<sup>6</sup> virtuella datorer med mer än 64 virtuella processorer kräver ett av de gäst operativ system som stöds:

- Windows Server 2016 eller senare
- Ubuntu 16,04 LTS eller senare med Azures justerade kernel (4,15 kernel eller senare)
- SLES 12 SP2 eller senare
- RHEL eller CentOS version 6,7 till 6,10, med Microsoft-medföljande LIS-paket 4.3.1 (eller senare) installerat
- RHEL eller CentOS version 7,3, med Microsoft-tillhandahållen LIS-paket 4.2.1 (eller senare) installerat
- RHEL eller CentOS version 7,6 eller senare
- Oracle Linux med UEK4 eller senare
- Debian 9 med backports-kärnan, Debian 10 eller senare
- Core-attribut med en 4,14-kernel eller senare

## <a name="size-table-definitions"></a>Definitioner för storlekstabellen

- Lagringskapaciteten visas i GiB, eller 1 024^3 byte. När du jämför diskar som mäts i GB (1 000^3 byte) med diskar som mäts i GiB (1 024^3) är det viktigt att veta att kapaciteten som anges i GiB kan vara mindre. Exempel: 1 023 GiB = 1 098,4 GB
- Diskgenomflödet mäts i indata-/utdataåtgärder per sekund (IOPS) och Mbit/s där Mbit/s = 10^6 byte/sek.
- Om du vill få bästa möjliga prestanda för dina virtuella datorer bör du begränsa antalet data diskar till 2 diskar per vCPU.
- **Förväntad nätverks bandbredd** är den högsta sammanlagda [allokerade bandbredden per VM-typ](../virtual-network/virtual-machine-network-throughput.md) i alla nätverkskort, för alla mål. Övre gränser garanteras inte, men är avsedda att ge vägledning vid valet av VM-typ för det avsedda programmet. Faktiska nätverksprestanda beror på flera faktorer, t.ex. nätverksbelastning, programinläsningar och nätverksinställningar. Information om hur du optimerar dataflödet i nätverket finns i [Optimizing network throughput for Windows and Linux](../virtual-network/virtual-network-optimize-network-bandwidth.md) (Optimera nätverksgenomflödet för Windows och Linux). För att uppnå förväntade nätverksprestanda i Linux eller Windows kan det vara nödvändigt att välja en specifik version eller att optimera den virtuella datorn. Mer information finns i [How to reliably test for virtual machine throughput](../virtual-network/virtual-network-bandwidth-testing.md) (Tillförlitlig testning av genomflödet för en virtuell dator).


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