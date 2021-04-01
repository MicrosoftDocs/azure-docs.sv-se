---
title: Storlekar för virtuella Azure-datorer – tidigare generationer | Microsoft Docs
description: Visar en lista över tidigare generationer av storlekar som är tillgängliga för virtuella datorer i Azure. Visar information om antalet virtuella processorer, data diskar och nätverkskort samt lagrings data flöde och nätverks bandbredd för storlekar i den här serien.
services: virtual-machines
ms.subservice: sizes
author: mimckitt
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 11/01/2020
ms.author: mimckitt
ms.openlocfilehash: 90bc98d63b45e43c9325eed4fe019b18f52d0de8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96500299"
---
# <a name="previous-generations-of-virtual-machine-sizes"></a>Tidigare generationer av virtuella dator storlekar

Det här avsnittet innehåller information om tidigare generationer av virtuella dator storlekar. De här storlekarna kan fortfarande användas, men det finns nya generationer som är tillgängliga.

## <a name="f-series"></a>F-serien

F-serien bygger på 2,4 GHz-processorn Intel Xeon® E5-2673 v3 (Haswell), som kan uppnå klockhastigheter på så mycket som 3,1 GHz med Intel Turbo Boost Technology 2.0. Det här är samma processorprestanda som Dv2-serien för virtuella datorer.  

De virtuella datorerna i F-serien är ett utmärkt alternativ för arbetsbelastningar som kräver snabbare processorer, men som inte behöver så mycket minne eller temporär lagring per virtuell processor.  Arbetsbelastningar som analyser, spelservrar, webbservrar och batchbearbetning kan dra nytta av F-serien.

ACU: 210–250

Premium Storage: stöds inte

Premium Storage caching: stöds inte

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt data flöde för temporär lagring: IOPS/Read Mbit/s/Write Mbit/s | Maximalt antal data diskar/data flöde: IOPS | Högsta antal nätverkskort/förväntad nätverks bandbredd (Mbit/s) |
|---|---|---|---|---|---|---|
| Standard_F1  | 1  | 2  | 16  | 3 000/46/23    | 4/4x500   | 2/750   |
| Standard_F2  | 2  | 4  | 32  | 6 000/93/46    | 8/8x500   | 2/1500  |
| Standard_F4  | 4  | 8  | 64  | 12 000/187/93  | 16/16x500 | 4/3000  |
| Standard_F8  | 8  | 16 | 128 | 24 000/375/187 | 32/32x500 | 8/6000  |
| Standard_F16 | 16 | 32 | 256 | 48 000/750/375 | 64/64x500 | 8/12000 |

## <a name="fs-series-sup1sup"></a>FS-serie <sup>1</sup>

Fs-serien ger alla fördelar med F-serien, och Premium-lagring.

ACU: 210–250

Premium Storage: stöds

Premium Storage caching: stöds

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Högsta cachelagrade data flöde för cache och temporär lagring: IOPS/Mbit/s (cachestorlek i GiB) | Maximalt antal cachelagrade diskar: IOPS/MBps | Högsta antal nätverkskort/förväntad nätverks bandbredd (Mbit/s) |
|---|---|---|---|---|---|---|---|
| Standard_F1s  | 1  | 2  | 4  | 4  | 4000/32 (12)    | 3200/48   | 2/750   |
| Standard_F2s  | 2  | 4  | 8  | 8  | 8000/64 (24)    | 6400/96   | 2/1500  |
| Standard_F4s  | 4  | 8  | 16 | 16 | 16000/128 (48)  | 12800/192 | 4/3000  |
| Standard_F8s  | 8  | 16 | 32 | 32 | 32000/256 (96)  | 25600/384 | 8/6000  |
| Standard_F16s | 16 | 32 | 64 | 64 | 64000/512 (192) | 51200/768 | 8/12000 |

Mbit/s = 10^6 byte per sekund och GiB = 1 024^3 byte.

<sup>1</sup> det högsta antalet disk data flöde (IOPS eller Mbit/s) som är möjligt med en virtuell FS-serienhet kan begränsas av antal, storlek och striping av de anslutna diskarna.  Mer information finns i [design för hög prestanda](premium-storage-performance.md).


## <a name="nvv2-series"></a>NVv2-serien

**Nyare storleks rekommendation**: [NVv3-serien](nvv3-series.md)

De virtuella datorerna i NVv2-serien drivs av [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU-och NVIDIA grid-teknik med Intel Broadwell-processorer. Dessa virtuella datorer är avsedda för GPU-accelererade grafik program och virtuella skriv bord där kunder vill visualisera sina data, simulera resultat för att visa, arbeta med CAD eller återge och strömma innehåll. Dessutom kan de virtuella datorerna köra enskilda precisions arbets belastningar som kodning och åter givning. NVv2 Virtual Machines stöder Premium Storage och levereras med två gånger system minnet (RAM) jämfört med dess föregående NV-serie.  

Varje GPU i NVv2-instanser levereras med en GRID-licens. Den här licensen ger dig möjlighet att använda en NV-instans som virtuell arbets station för en enskild användare, eller att 25 samtidiga användare kan ansluta till den virtuella datorn för ett virtuellt program scenario.

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | GPU-minne: GiB | Maximalt antal datadiskar | Maximalt antal nätverkskort | Virtuella arbets stationer | Virtuella program |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NV6s_v2  | 6  | 112 | 320  | 1 | 8  | 12 | 4 | 1 | 25  |
| Standard_NV12s_v2 | 12 | 224 | 640  | 2 | 16 | 24 | 8 | 2 | 50  |
| Standard_NV24s_v2 | 24 | 448 | 1280 | 4 | 32 | 32 | 8 | 4 | 100 |

## <a name="older-generations-of-virtual-machine-sizes"></a>Äldre generationer av virtuella dator storlekar

Det här avsnittet innehåller information om äldre generationer av virtuella dator storlekar. Dessa storlekar stöds fortfarande men får ingen ytterligare kapacitet. Det finns nyare eller alternativa storlekar som är allmänt tillgängliga. Se storlekar på [virtuella datorer i Azure](./sizes.md) för att välja de VM-storlekar som passar bäst för dina behov.  

Mer information om hur du ändrar storlek på en virtuell Linux-dator finns i [ändra storlek på en virtuell Linux-dator](linux/change-vm-size.md).  

<br>

### <a name="basic-a"></a>Basic A  

**Nyare storleks rekommendation**: [AV2-serien](av2-series.md)

Premium Storage: stöds inte

Premium Storage caching: stöds inte

Storlekarna på den grundläggande nivån är främst avsedda för utvecklingsarbetsbelastningar och andra program som inte kräver belastningsutjämning, automatisk skalning eller minnesintensiva virtuella datorer.

| Storlek – Storlek\namn | Virtuell processor | Minne|Nätverkskort (max.)| Högsta temporär diskstorlek | Max. data diskar (1023 GB vardera)| Max. IOPS (300 per disk) |
|---|---|---|---|---|---|---|
| A0\Basic_A0 | 1 | 768 MB  | 2 | 20 GB  | 1  | 1 × 300  |
| A1\Basic_A1 | 1 | 1,75 GB | 2 | 40 GB  | 2  | 2 × 300  |
| A2\Basic_A2 | 2 | 3,5 GB  | 2 | 60 GB  | 4  | 4 × 300  |
| A3\Basic_A3 | 4 | 7 GB    | 2 | 120 GB | 8  | 8 × 300  |
| A4\Basic_A4 | 8 | 14 GB   | 2 | 240 GB | 16 | 16 × 300 |

<br>

### <a name="standard-a0---a4-using-cli-and-powershell"></a>Standard A0–A4 med CLI och PowerShell

I den klassiska distributionsmodellen skiljer sig vissa namn på VM-storlekarna i CLI och PowerShell:

* Standard_A0 är ExtraSmall
* Standard_A1 är Small
* Standard_A2 är Medium
* Standard_A3 är Large
* Standard_A4 är ExtraLarge

### <a name="a-series"></a>A-serien  

**Nyare storleks rekommendation**: [AV2-serien](av2-series.md)

ACU: 50–100

Premium Storage: stöds inte

Premium Storage caching: stöds inte

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (HDD): GiB | Maximalt antal datadiskar | Maximalt diskgenomflöde: IOPS | Högsta antal nätverkskort/förväntad nätverks bandbredd (Mbit/s) |
| --- | --- | --- | --- | --- | --- | --- |
| Standard_A0 &nbsp; <sup>1</sup> | 1 | 0.768 | 20 | 1 | 1 × 500 | 2/100 |
| Standard_A1 | 1 | 1,75 | 70  | 2  | 2 × 500  | 2/500  |
| Standard_A2 | 2 | 3.5  | 135 | 4  | 4 × 500  | 2/500  |
| Standard_A3 | 4 | 7    | 285 | 8  | 8 × 500  | 2/1000 |
| Standard_A4 | 8 | 14   | 605 | 16 | 16 × 500 | 4/2000 |
| Standard_A5 | 2 | 14   | 135 | 4  | 4 × 500  | 2/500  |
| Standard_A6 | 4 | 28   | 285 | 8  | 8 × 500  | 2/1000 |
| Standard_A7 | 8 | 56   | 605 | 16 | 16 × 500 | 4/2000 |

<sup>1</sup> a0-storleken är överdrived för den fysiska maskin varan. För just den här storleken kan andra kunddistributioner påverka prestanda för arbetsbelastningen som körs. Nedan beskrivs relativa prestanda som den förväntade baslinjen, som har en ungefärlig variation på 15 procent.

<br>

### <a name="a-series---compute-intensive-instances"></a>A-serien – beräkningsintensiva instanser  

**Nyare storleks rekommendation**: [AV2-serien](av2-series.md)

ACU: 225

Premium Storage: stöds inte

Premium Storage caching: stöds inte

Storlekarna i A8–A11- och H-serien kallas även för *beräkningsintensiva instanser*. Maskinvaran som kör dessa storlekar är utformad och optimerad för beräkningsintensiva och nätverksintensiva program, inklusive HPC-klustertillämpningar (databehandling med höga prestanda), modellering och simuleringar. A8–A11-serien använder Intel Xeon E5-2670 @ 2,6 GHZ och H-serien använder Intel Xeon E5-2667 v3 @ 3,2 GHz.  

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (HDD): GiB | Maximalt antal datadiskar | Maximalt diskgenomflöde: IOPS | Maximalt antal nätverkskort|
|---|---|---|---|---|---|---|
| Standard_A8 &nbsp; <sup>1</sup> | 8  | 56  | 382 | 32 | 32 × 500 | 2 |
| Standard_A9 &nbsp; <sup>1</sup> | 16 | 112 | 382 | 64 | 64x500 | 4 |
| Standard_A10 | 8  | 56  | 382 | 32 | 32 × 500 | 2 |
| Standard_A11 | 16 | 112 | 382 | 64 | 64x500 | 4 |

<sup>1</sup> För MPI-program aktive ras det dedikerade RDMA-backend-nätverket av FDR InfiniBand-nätverket, vilket ger mycket låg latens och hög bandbredd.  

> [!NOTE]
> De [virtuella datorerna A8 – A11 planeras för att dra på 3/2021](https://azure.microsoft.com/updates/a8-a11-azure-virtual-machine-sizes-will-be-retired-on-march-1-2021/). Vi rekommenderar starkt att du inte skapar några nya A8-A11 virtuella datorer. Migrera befintliga virtuella A8-A11-datorer till nyare och kraftfulla VM-storlekar med höga prestanda, till exempel H, HB, HC, HBv2, samt generella beräknings storlekar för virtuella datorer som D, E och F för bättre pris-prestanda. Mer information finns i [Guide för HPC-migrering](https://azure.microsoft.com/resources/hpc-migration-guide/).

<br>

### <a name="d-series"></a>D-serien  

**Nyare storleks rekommendation**: [Dav4-serien](dav4-dasv4-series.md), [DV4-serien](dv4-dsv4-series.md) och [Ddv4-serien](ddv4-ddsv4-series.md)

ACU: 160-250 <sup>1</sup>

Premium Storage: stöds inte

Premium Storage caching: stöds inte

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt data flöde för temporär lagring: IOPS/Read Mbit/s/Write Mbit/s | Maximalt antal data diskar/data flöde: IOPS | Högsta antal nätverkskort/förväntad nätverks bandbredd (Mbit/s) |
|---|---|---|---|---|---|---|
| Standard_D1  | 1 | 3.5 | 50  | 3 000/46/23    | 4/4x500   | 2/500  |
| Standard_D2  | 2 | 7   | 100 | 6 000/93/46    | 8/8x500   | 2/1000 |
| Standard_D3  | 4 | 14  | 200 | 12 000/187/93  | 16/16x500 | 4/2000 |
| Standard_D4  | 8 | 28  | 400 | 24 000/375/187 | 32/32x500 | 8/4000 |

<sup>1</sup> VM-serien kan köras på någon av följande CPU: 2,2 GHz intel Xeon® E5-2660 v2, 2,4 GHz intel Xeon® E5-2673 v3 (Haswell) eller 2,3 GHz intel Xeon® E5-2673 v4 (Broadwell)  

<br>

### <a name="d-series---memory-optimized"></a>D-serien-minnesoptimerade  

**Nyare storleks rekommendation**: [Dav4-serien](dav4-dasv4-series.md), [DV4-serien](dv4-dsv4-series.md) och [Ddv4-serien](ddv4-ddsv4-series.md)

ACU: 160-250 <sup>1</sup>

Premium Storage: stöds inte

Premium Storage caching: stöds inte

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt data flöde för temporär lagring: IOPS/Read Mbit/s/Write Mbit/s | Maximalt antal data diskar/data flöde: IOPS | Högsta antal nätverkskort/förväntad nätverks bandbredd (Mbit/s) |
|---|---|---|---|---|---|---|
| Standard_D11 | 2  | 14  | 100 | 6 000/93/46    | 8/8x500   | 2/1000 |
| Standard_D12 | 4  | 28  | 200 | 12 000/187/93  | 16/16x500 | 4/2000 |
| Standard_D13 | 8  | 56  | 400 | 24 000/375/187 | 32/32x500 | 8/4000 |
| Standard_D14 | 16 | 112 | 800 | 48 000/750/375 | 64/64x500 | 8/8000 |

<sup>1</sup> VM-serien kan köras på någon av följande CPU: 2,2 GHz intel Xeon® E5-2660 v2, 2,4 GHz intel Xeon® E5-2673 v3 (Haswell) eller 2,3 GHz intel Xeon® E5-2673 v4 (Broadwell)  

<br>

### <a name="preview-dc-series"></a>För hands version: DC-serien

**Nyare storleks rekommendation**: [DCsv2-serien](dcv2-series.md)

Premium Storage: stöds

Premium Storage caching: stöds

DC-serien använder den senaste generationen av 3,7 GHz Intel XEON E-2176G-processor med SGX-teknik och med Intel Turbo Boost-teknik kan gå upp till 4,7 GHz. 

| Storlek          | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för cachelagring och temporär lagring: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt icke cachelagrat diskgenomflöde: IOPS / Mbit/s | Högsta antal nätverkskort/förväntad nätverks bandbredd (Mbit/s) |
|---------------|------|-------------|------------------------|----------------|-------------------------------------------------------------------------|-------------------------------------------|----------------------------------------------|
| Standard_DC2s | 2    | 8           | 100                    | 2              | 4000/32 (43)                                                          | 3200/48                                  | 2/1 500                                     |
| Standard_DC4s | 4    | 16          | 200                    | 4              | 8000/64 (86)                                                          | 6400/96                                  | 2 / 3000                                     |

> [!IMPORTANT]
>
> Virtuella datorer i VM-serien är [generation 2 virtuella datorer](./generation-2.md#creating-a-generation-2-vm) och stöder bara `Gen2` avbildningar.


### <a name="ds-series"></a>DS-serien  

**Nyare storleks rekommendation**: [Dasv4-serien](dav4-dasv4-series.md), [Dsv4-serien](dv4-dsv4-series.md) och [Ddsv4-serien](ddv4-ddsv4-series.md)

ACU: 160-250 <sup>1</sup>

Premium Storage: stöds

Premium Storage caching: stöds

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Högsta cachelagrade data flöde för cache och temporär lagring: IOPS/Mbit/s (cachestorlek i GiB) | Maximalt antal cachelagrade diskar: IOPS/MBps | Högsta antal nätverkskort/förväntad nätverks bandbredd (Mbit/s) |
|---|---|---|---|---|---|---|---|
| Standard_DS1 | 1 | 3.5 | 7  | 4  | 4000/32 (43)    | 3200/32   | 2/500  |
| Standard_DS2 | 2 | 7   | 14 | 8  | 8000/64 (86)    | 6400/64   | 2/1000 |
| Standard_DS3 | 4 | 14  | 28 | 16 | 16000/128 (172) | 12800/128 | 4/2000 |
| Standard_DS4 | 8 | 28  | 56 | 32 | 32000/256 (344) | 25600/256 | 8/4000 |

<sup>1</sup> VM-serien kan köras på någon av följande CPU: 2,2 GHz intel Xeon® E5-2660 v2, 2,4 GHz intel Xeon® E5-2673 v3 (Haswell) eller 2,3 GHz intel Xeon® E5-2673 v4 (Broadwell)  

<br>

### <a name="ds-series---memory-optimized"></a>DS-seriens minnesoptimerade  

**Nyare storleks rekommendation**: [Dasv4-serien](dav4-dasv4-series.md), [Dsv4-serien](dv4-dsv4-series.md) och [Ddsv4-serien](ddv4-ddsv4-series.md)

ACU: 160-250 <sup>1, 2</sup>

Premium Storage: stöds

Premium Storage caching: stöds

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Högsta cachelagrade data flöde för cache och temporär lagring: IOPS/Mbit/s (cachestorlek i GiB) | Maximalt antal cachelagrade diskar: IOPS/MBps | Högsta antal nätverkskort/förväntad nätverks bandbredd (Mbit/s) |
|---|---|---|---|---|---|---|---|
| Standard_DS11 | 2  | 14  | 28  | 8  | 8000/64 (72)    | 6400/64   | 2/1000 |
| Standard_DS12 | 4  | 28  | 56  | 16 | 16000/128 (144) | 12800/128 | 4/2000 |
| Standard_DS13 | 8  | 56  | 112 | 32 | 32000/256 (288) | 25600/256 | 8/4000 |
| Standard_DS14 | 16 | 112 | 224 | 64 | 64000/512 (576) | 51200/512 | 8/8000 |

<sup>1</sup> det högsta antalet disk data flöde (IOPS eller Mbit/s) som möjligt med en virtuell dator i DS-serien kan begränsas av antalet, storlek och striping av de anslutna diskarna.  Mer information finns i [design för hög prestanda](premium-storage-performance.md).
<sup>2</sup> VM-serien kan köras på någon av följande CPU: 2,2 GHz intel Xeon® E5-2660 v2, 2,4 GHz intel Xeon® E5-2673 v3 (Haswell) eller 2,3 GHz intel Xeon® E5-2673 v4 (Broadwell)  

<br>

### <a name="ls-series"></a>Ls-serien

**Nyare storleks rekommendation**: [Lsv2-serien](lsv2-series.md)

Ls-serien stöder upp till 32 virtuella processorer i [E5 v3-familjen med Intel® Xeon®-processorn](https://www.intel.com/content/www/us/en/processors/xeon/xeon-e5-solutions.html). Ls-serien uppnår samma processorprestanda som G/GS-serien och levereras med 8 GiB minne per virtuell processor.

LS-serien stöder inte att en lokal cache skapas för att öka IOPS som kan nås av varaktiga data diskar. Med det höga genom strömningen och IOPS av den lokala disken kan virtuella datorer i LS-serien vara idealiska för NoSQL-butiker som Apache Cassandra och MongoDB som replikerar data över flera virtuella datorer för att få beständighet i händelse av att en enskild virtuell dator Miss lyckas.

ACU: 180–240

Premium Storage: stöds

Premium Storage caching: stöds inte

| Storlek | Virtuell processor | Minne (GiB) | Temp-lagring (GiB) | Maximalt antal datadiskar | Maximalt data flöde för temporärt lagrings utrymme (IOPS/Mbit/s) | Maximalt antal cachelagrade diskar (IOPS/Mbit/s) | Högsta antal nätverkskort/förväntad nätverks bandbredd (Mbit/s) |
|---|---|---|---|---|---|---|---|
| Standard_L4s   | 4  | 32  | 678  | 16 | 20000/200 | 5000/125  | 2/4000  |
| Standard_L8s   | 8  | 64  | 1388 | 32 | 40000/400 | 10000/250 | 4/8000  |
| Standard_L16s  | 16 | 128 | 2807 | 64 | 80000/800 | 20000/500 | 8/16000 |
| Standard_L32s &nbsp; <sup>1</sup> | 32 | 256 | 5630 | 64 | 160000/1600 | 40000/1000 | 8/20000 |

Det maximala disk data flödet som är möjligt med virtuella datorer i LS-serien kan begränsas av antal, storlek och striping för anslutna diskar. Mer information finns i [design för hög prestanda](premium-storage-performance.md).

<sup>1</sup> instans är isolerad till maskin vara som är dedikerad till en enda kund.

### <a name="gs-series"></a>GS-serien

**Nyare storleks rekommendation**: [Easv4-serien](eav4-easv4-series.md), [Esv4-serien](ev4-esv4-series.md), [Edsv4-serien](edv4-edsv4-series.md) och [M-serien](m-series.md)

ACU: 180-240 <sup>1</sup>

Premium Storage: stöds

Premium Storage caching: stöds

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt antal datadiskar | Maximalt genomflöde för cachelagring och temporär lagring: IOPS / Mbit/s (cachestorlek i GiB) | Maximalt antal cachelagrade diskar: IOPS/MBps | Högsta antal nätverkskort/förväntad nätverks bandbredd (Mbit/s) |
|---|---|---|---|---|---|---|---|
| Standard_GS1 | 2 | 28  | 56  | 8  | 10000/100 (264)  | 5000/ 125  | 2/2000 |
| Standard_GS2 | 4 | 56  | 112 | 16 | 20000/200 (528)  | 10000/ 250 | 2/4000 |
| Standard_GS3 | 8 | 112 | 224 | 32 | 40000/400 (1056) | 20000/ 500 | 4/8000 |
| Standard_GS4 &nbsp; <sup>3</sup> | 16 | 224 | 448 | 64 | 80000/800 (2112) | 40000/1000 | 8/16000 |
| Standard_GS5 &nbsp; <sup>2, &nbsp; 3</sup> | 32 | 448 |896 | 64 |160000/1600 (4224) | 80000/2000 | 8/20000 |

<sup>1</sup> det högsta antalet disk data flöde (IOPS eller Mbit/s) som är möjligt med en GS-seriens virtuella dator kan begränsas av antalet, storlek och striping av de anslutna diskarna. Mer information finns i [design för hög prestanda](premium-storage-performance.md).

<sup>2</sup> -instansen är isolerad till maskin vara som är dedikerad till en enda kund.

<sup>3</sup> begränsade kärn storlekar är tillgängliga.

<br>

### <a name="g-series"></a>G-serien

**Nyare storleks rekommendation**: [Eav4-serien](eav4-easv4-series.md), [Ev4-serien](ev4-esv4-series.md) och [Edv4-serien](edv4-edsv4-series.md) och [M-serien](m-series.md)

ACU: 180–240

Premium Storage: stöds inte

Premium Storage caching: stöds inte

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | Maximalt data flöde för temporär lagring: IOPS/Read Mbit/s/Write Mbit/s | Maximalt antal data diskar/data flöde: IOPS | Högsta antal nätverkskort/förväntad nätverks bandbredd (Mbit/s) |
|---|---|---|---|---|---|---|
| Standard_G1  | 2  | 28  | 384  | 6 000/93/46    | 8/8x500   | 2/2000  |
| Standard_G2  | 4  | 56  | 768  | 12 000/187/93  | 16/16x500 | 2/4000  |
| Standard_G3  | 8  | 112 | 1536 | 24 000/375/187 | 32/32x500 | 4/8000  |
| Standard_G4  | 16 | 224 | 3072 | 48 000/750/375 | 64/64x500 | 8/16000 |
| Standard_G5 &nbsp; <sup>1</sup> | 32 | 448 | 6144 | 96000/1500/750| 64/64x500 | 8/20000 |

<sup>1</sup> instans är isolerad till maskin vara som är dedikerad till en enda kund.
<br>

### <a name="nv-series"></a>NV-serien
**Nyare storleks rekommendation**: [NVv3-serien](nvv3-series.md) och [NVv4-serien](nvv4-series.md)

De virtuella datorerna med NV-serien drivs av [NVIDIA Tesla M60](https://images.nvidia.com/content/tesla/pdf/188417-Tesla-M60-DS-A4-fnl-Web.pdf) GPU: er och NVIDIA grid-teknik för snabbare program och virtuella skriv bord där kunderna kan visualisera data eller simuleringar. Användare kan visualisera sina grafik intensiva arbets flöden på NV-instanserna för att få överlägsen grafik kapacitet och dessutom köra enskilda precisions arbets belastningar som kodning och åter givning. Virtuella datorer i NV-serien drivs också av Intel Xeon E5-2690 v3-processorer (Haswell).

Varje GPU i NV-instanser levereras med en GRID-licens. Den här licensen ger dig möjlighet att använda en NV-instans som virtuell arbets station för en enskild användare, eller att 25 samtidiga användare kan ansluta till den virtuella datorn för ett virtuellt program scenario.

Premium Storage: stöds inte

Premium Storage caching: stöds inte

Direktmigrering: stöds inte

Minnes bebetjänings uppdateringar: stöds inte

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | GPU-minne: GiB | Maximalt antal datadiskar | Maximalt antal nätverkskort | Virtuella arbets stationer | Virtuella program |
|---|---|---|---|---|---|---|---|---|---|
| Standard_NV6  | 6  | 56  | 340  | 1 | 8  | 24 | 1 | 1 | 25  |
| Standard_NV12 | 12 | 112 | 680  | 2 | 16 | 48 | 2 | 2 | 50  |
| Standard_NV24 | 24 | 224 | 1440 | 4 | 32 | 64 | 4 | 4 | 100 |

1 GPU = ett halvt M60-kort.
<br>

### <a name="nc-series"></a>NC-serien
**Nyare storleks rekommendation**: [NC T4 v3-serien](nct4-v3-series.md)

Virtuella datorer i NC-serien drivs av [NVIDIA Tesla K80](https://www.nvidia.com/content/dam/en-zz/Solutions/Data-Center/tesla-product-literature/Tesla-K80-BoardSpec-07317-001-v05.pdf) -kortet och Intel Xeon E5-2690 v3-processorn (Haswell). Användare kan gå igenom data snabbare genom att använda CUDA för energi gransknings program, krasch simulering, Ray-spårad åter givning, djup inlärning med mera. NC24r-konfigurationen ger ett nätverks gränssnitt med låg latens och hög genom strömning som är optimerat för tätt sammansatta parallella dator arbets belastningar.

[Premium Storage](premium-storage-performance.md): stöds inte<br>
[Premium Storage caching](premium-storage-performance.md): stöds inte<br>
[Direktmigrering](maintenance-and-updates.md): stöds inte<br>
[Minnes bebetjänings uppdateringar](maintenance-and-updates.md): stöds inte<br>
[Stöd för VM-generering](generation-2.md): generation 1<br>
<br>

| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | GPU-minne: GiB | Maximalt antal datadiskar | Maximalt antal nätverkskort |
|---|---|---|---|---|---|---|---|
| Standard_NC6    | 6  | 56  | 340  | 1 | 12 | 24 | 1 |
| Standard_NC12   | 12 | 112 | 680  | 2 | 24 | 48 | 2 |
| Standard_NC24   | 24 | 224 | 1440 | 4 | 48 | 64 | 4 |
| Standard_NC24r* | 24 | 224 | 1440 | 4 | 48 | 64 | 4 |

1 GPU = ett halvt K80-kort.

*RDMA-stöd


<br>


### <a name="ncv2-series"></a>NCv2-serien
**Nyare storleks rekommendation**: [NC T4 v3-serien](nct4-v3-series.md) och [NC-V100 v3-serien](ncv3-series.md)

Virtuella datorer i NCv2-serien drivs av NVIDIA Tesla P100-GPU: er. Dessa GPU: er kan ge mer än dubbelt så många beräknings prestanda som NC-serien. Kunderna kan dra nytta av dessa uppdaterade GPU: er för traditionella HPC-arbetsbelastningar som till exempel behållar modellering, DNA-sekvensering, protein analys, Monte Carlo-simuleringar och andra. Förutom GPU: er är virtuella datorer i NCv2-serien också baserade på Broadwell-processorer (Intel Xeon E5-2690 v4).

NC24rs v2-konfigurationen ger ett nätverks gränssnitt med låg fördröjning och hög genom strömning som är optimerat för tätt sammansatta parallella dator arbets belastningar.

[Premium Storage](premium-storage-performance.md): stöds<br>
[Premium Storage caching](premium-storage-performance.md): stöds<br>
[Direktmigrering](maintenance-and-updates.md): stöds inte<br>
[Minnes bebetjänings uppdateringar](maintenance-and-updates.md): stöds inte<br>
[Stöd för VM-generering](generation-2.md): generation 1 och 2<br>

> För den här VM-serien ställs vCPU-kvoten (kärnor) i din prenumeration från början till 0 i varje region. [Begär en vCPU-kvot ökning](../azure-portal/supportability/resource-manager-core-quotas-request.md) för den här serien i en [tillgänglig region](https://azure.microsoft.com/regions/services/).
>
| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | GPU-minne: GiB | Maximalt antal datadiskar | Maximalt antal cachelagrade diskar: IOPS/MBps | Maximalt antal nätverkskort |
|---|---|---|---|---|---|---|---|---|
| Standard_NC6s_v2    | 6  | 112 | 736  | 1 | 16 | 12 | 20000/200 | 4 |
| Standard_NC12s_v2   | 12 | 224 | 1474 | 2 | 32 | 24 | 40000/400 | 8 |
| Standard_NC24s_v2   | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |
| Standard_NC24rs_v2 * | 24 | 448 | 2948 | 4 | 64 | 32 | 80000/800 | 8 |

1 GPU = ett P100-kort.

*RDMA-stöd

<br>

### <a name="nd-series"></a>ND-serien
**Nyare storleks rekommendation**: [NDv2-serien](ndv2-series.md) och [NC-V100 v3-serien](ncv3-series.md)

De virtuella datorerna i ND-serien är ett nytt tillägg till GPU-familjen som är utformad för AI-och djup inlärnings arbets belastningar. De erbjuder utmärkt prestanda för utbildning och härledning. ND-instanser drivs av [NVIDIA Tesla P40](https://images.nvidia.com/content/pdf/tesla/184427-Tesla-P40-Datasheet-NV-Final-Letter-Web.pdf) -GPU: er och Intel Xeon E5-2690 v4-processorer (Broadwell). Dessa instanser ger utmärkta prestanda för flytt ALS åtgärder med enkel precision, för AI-arbetsbelastningar som använder Microsoft Cognitive Toolkit, TensorFlow, caffe och andra ramverk. ND-serien erbjuder även en mycket större GPU-minnes storlek (24 GB), vilket gör det möjligt att få plats med mycket större neurala NET-modeller. I likhet med NC-serien erbjuder ND-serien en konfiguration med ett sekundärt nätverk med låg latens, högt data flöde via RDMA och InfiniBand-anslutning så att du kan köra storskaliga utbildnings jobb över flera GPU: er.

[Premium Storage](premium-storage-performance.md): stöds<br>
[Premium Storage caching](premium-storage-performance.md): stöds<br>
[Direktmigrering](maintenance-and-updates.md): stöds inte<br>
[Minnes bebetjänings uppdateringar](maintenance-and-updates.md): stöds inte<br>
[Stöd för VM-generering](generation-2.md): generation 1 och 2<br>

> För den här VM-serien anges vCPU (Core)-kvoten per region i din prenumeration till 0. [Begär en vCPU-kvot ökning](../azure-portal/supportability/resource-manager-core-quotas-request.md) för den här serien i en [tillgänglig region](https://azure.microsoft.com/regions/services/).
>
| Storlek | Virtuell processor | Minne: GiB | Temporär lagring (SSD) GiB | GPU | GPU-minne: GiB | Maximalt antal datadiskar | Maximalt antal cachelagrade diskar: IOPS/MBps | Maximalt antal nätverkskort |
|---|---|---|---|---|---|---|---|---|
| Standard_ND6s    | 6  | 112 | 736  | 1 | 24 | 12 | 20000/200 | 4 |
| Standard_ND12s   | 12 | 224 | 1474 | 2 | 48 | 24 | 40000/400 | 8 |
| Standard_ND24s   | 24 | 448 | 2948 | 4 | 24 | 32 | 80000/800 | 8 |
| Standard_ND24rs * | 24 | 448 | 2948 | 4 | 96 | 32 | 80000/800 | 8 |

1 GPU = ett P40-kort.

*RDMA-stöd

<br>

## <a name="next-steps"></a>Nästa steg

Lär dig mer om hur [Azure Compute Units (ACU)](acu.md) kan hjälpa dig att jämföra beräknings prestanda i Azure SKU: er.