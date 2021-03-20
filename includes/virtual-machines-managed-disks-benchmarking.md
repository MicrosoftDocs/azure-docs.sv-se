---
title: ta med fil
description: ta med fil
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 01/29/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 25404837d5bc66ff415be8d8670eb6650475c30f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99094670"
---
## <a name="warm-up-the-cache"></a>Värm upp cacheminnet

Disken med ReadOnly-cachelagring kan ge högre IOPS än disk gränsen. För att få den här maximala Läs prestandan från värd-cachen måste du först värma upp cacheminnet för den här disken. Detta säkerställer att den Läs IOs som benchmarking-verktyget kommer att köra på CacheReads volym, träffar i själva cacheminnet och inte på disken direkt. Cacheträffar resulterar i mer IOPS från den enkla cache-aktiverade disken.

> [!IMPORTANT]
> Du måste värma upp cacheminnet innan du kör benchmarks varje gång den virtuella datorn startas om.

## <a name="diskspd"></a>DISKSPD

[Hämta verktyget DISKSP](https://github.com/Microsoft/diskspd) på den virtuella datorn. DISKSPD är ett verktyg som du kan anpassa för att skapa dina egna syntetiska arbets belastningar. Vi kommer att använda samma inställningar som beskrivs ovan för att köra benchmarking-tester. Du kan ändra specifikationerna för att testa olika arbets belastningar.

I det här exemplet använder vi följande uppsättning bas linje parametrar:

- -c200G: skapar (eller återskapar) exempel filen som används i testet. Den kan anges i byte, KiB, MiB, GiB eller block. I det här fallet används en stor fil med 200-GiB som mål fil för att minimera cachelagring av minne.
- -W100: anger procent andelen åtgärder som är Write-begäranden (-W0 motsvarar 100% Read).
- -b4K: anger block storleken i byte, KiB, MiB eller GiB. I det här fallet används storleken på 4K-block för att simulera ett slumpmässigt I/O-test.
- -F4: anger totalt fyra trådar.
- -r: anger slumpmässigt I/O-test (åsidosätter parametern-s).
- -o128: anger antalet utestående I/O-begäranden per mål per tråd. Detta kallas även ködjup. I det här fallet används 128 för att stressa processorn.
- -W7200: anger tiden för den varmaste tiden innan mätningarna börjar.
- -D30: anger varaktigheten för testet, inte inklusive uppvärmning.
- -SH: inaktivera cachelagring av program vara och maskin vara (motsvarar-SUW).

En fullständig lista över parametrar finns i GitHub- [lagringsplatsen](https://github.com/Microsoft/diskspd/wiki/Command-line-and-parameters).

### <a name="maximum-write-iops"></a>Maximalt Skriv-IOPS
Vi använder ett högt ködjup på 128, en liten block storlek på 8 KB och fyra arbets trådar för att köra Skriv åtgärder. Skriv personalen driver trafik på "NoCacheWrites"-volymen, som har tre diskar med cache satt till "ingen".

Kör följande kommando för 30 sekunders uppvärmning och 30 sekunders mått:

`diskspd -c200G -w100 -b8K -F4 -r -o128 -W30 -d30 -Sh testfile.dat`

Resultaten visar att den Standard_D8ds_v4 virtuella datorn levereras med den maximala Skriv-IOPS-gränsen på 12 800.

:::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/disks-benchmarks-diskspd-max-write-io-per-second.png" alt-text="För 3208642560 totalt antal byte, Max totalt I/o för 391680, med totalt 101,97 MiB/s, och totalt 13052,65 I/O per sekund.":::

### <a name="maximum-read-iops"></a>Högsta Läs-IOPS

Vi använder ett högt ködjup på 128, en liten block storlek på fyra KB och fyra arbets trådar för att köra Läs åtgärder. Läs arbetare driver trafik på "CacheReads"-volymen, som har en disk med cache satt till "ReadOnly".

Kör följande kommando för två timmar med varma och 30 sekunders mått:

`diskspd -c200G -b4K -F4 -r -o128 -W7200 -d30 -Sh testfile.dat`

Resultaten visar att den Standard_D8ds_v4 virtuella datorn levererar Max gränsen för läsnings-IOPS på 77 000.

:::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/disks-benchmarks-diskspd-max-read-io-per-second.png" alt-text="För totalt antal byte i 9652785152 fanns det 2356637 totalt I/o vid 306,72 totalt MiB/s, och totalt 78521,23 I/o per sekund.":::

### <a name="maximum-throughput"></a>Maximalt dataflöde

Om du vill hämta det maximala Skriv-och Skriv data flödet kan du ändra till en större block storlek på 64 KB.
## <a name="fio"></a>FIO

FIO är ett populärt verktyg för benchmark-lagring på virtuella Linux-datorer. Det har flexibiliteten att välja olika IO-storlekar, sekventiella eller slumpmässiga läsningar och skrivningar. Den skapar arbets trådar eller processer för att utföra angivna I/O-åtgärder. Du kan ange vilken typ av I/O-åtgärder som varje arbets tråd måste utföra med hjälp av projektfiler. Vi har skapat en jobb fil per scenario som illustreras i exemplen nedan. Du kan ändra specifikationerna i dessa projektfiler till att mäta olika arbets belastningar som körs på Premium Storage. I exemplen använder vi en Standard_D8ds_v4 som kör **Ubuntu**. Använd samma installations program som beskrivs i början av benchmark-avsnittet och Värm upp cacheminnet innan du kör benchmark-testerna.

Innan du börjar kan du [Hämta FIO](https://github.com/axboe/fio) och installera det på den virtuella datorn.

Kör följande kommando för Ubuntu,

```
apt-get install fio
```

Vi använder fyra arbets trådar för att köra Skriv åtgärder och fyra arbets trådar för att köra Läs åtgärder på diskarna. Skriv personalen driver trafik på volymen "NoCache", som har tre diskar med cache inställd på "ingen". Läs arbetare driver trafik på "readcache"-volymen, som har en disk med cache satt till "ReadOnly".

### <a name="maximum-write-iops"></a>Maximalt Skriv-IOPS

Skapa jobb filen med följande specifikationer för att få maximal Skriv-IOPS. Ge den namnet "fiowrite.ini".

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=4k
numjobs=4

[writer1]
rw=randwrite
directory=/mnt/nocache
```

Tänk på följande viktiga saker som är i linje med design rikt linjerna som beskrivs i föregående avsnitt. Dessa specifikationer är nödvändiga för att driva högsta IOPS.  

* Ett högt ködjup på 256.  
* En liten block storlek på 4 KB.  
* Flera trådar utför slumpmässiga skrivningar.

Kör följande kommando för att starta FIO-testet i 30 sekunder  

```
sudo fio --runtime 30 fiowrite.ini
```

Medan testet körs kan du se antalet skrivnings-IOPS som den virtuella datorn och Premium-diskarna levererar. Som du ser i exemplet nedan levererar den Standard_D8ds_v4 virtuella datorn sin maximala Skriv-IOPS-gräns på 12 800 IOPS.  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-uncached-writes-1.jpg" alt-text="Antalet skriv IOPS VM och Premium-SSD levereras, visar att skrivningar är 13.1 k IOPS.":::

### <a name="maximum-read-iops"></a>Högsta Läs-IOPS

Skapa jobb filen med följande specifikationer för att få maximal Läs-IOPS. Ge den namnet "fioread.ini".

```ini
[global]
size=30g
direct=1
iodepth=256
ioengine=libaio
bs=4k
numjobs=4

[reader1]
rw=randread
directory=/mnt/readcache
```

Tänk på följande viktiga saker som är i linje med design rikt linjerna som beskrivs i föregående avsnitt. Dessa specifikationer är nödvändiga för att driva högsta IOPS.

* Ett högt ködjup på 256.  
* En liten block storlek på 4 KB.  
* Flera trådar utför slumpmässiga skrivningar.

Kör följande kommando för att starta FIO-testet i 30 sekunder

```
sudo fio --runtime 30 fioread.ini
```

Medan testet körs kan du se antalet Läs-IOPS som den virtuella datorn och Premium-diskarna levererar. Som du ser i exemplet nedan levererar Standard_D8ds_v4 VM över 77 000 Läs-IOPS. Detta är en kombination av disk-och cache-prestanda.  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-cached-reads-1.jpg" alt-text="Skärm bild av antalet skriv IOPS VM och Premium-SSD levereras, visar att läsningarna är 78.6 k.":::

### <a name="maximum-read-and-write-iops"></a>Maximal Läs-och skriv-IOPS

Skapa jobb filen med följande specifikationer för att få maximalt kombinerat Läs-och skriv-IOPS. Ge den namnet "fioreadwrite.ini".

```ini
[global]
size=30g
direct=1
iodepth=128
ioengine=libaio
bs=4k
numjobs=4

[reader1]
rw=randread
directory=/mnt/readcache

[writer1]
rw=randwrite
directory=/mnt/nocache
rate_iops=3200
```

Tänk på följande viktiga saker som är i linje med design rikt linjerna som beskrivs i föregående avsnitt. Dessa specifikationer är nödvändiga för att driva högsta IOPS.

* Ett högt ködjup på 128.  
* En liten block storlek på 4 KB.  
* Flera trådar utför slumpmässiga läsningar och skrivningar.

Kör följande kommando för att starta FIO-testet i 30 sekunder

```
sudo fio --runtime 30 fioreadwrite.ini
```

Medan testet körs kan du se antalet kombinerade Läs-och skriv-IOPS som den virtuella datorn och Premium-diskarna levererar. Som du ser i exemplet nedan levererar Standard_D8ds_v4 VM över 90 000 kombinerat Läs-och skriv-IOPS. Detta är en kombination av disk-och cache-prestanda.  
    :::image type="content" source="../articles/virtual-machines/linux/media/premium-storage-performance/fio-both-1.jpg" alt-text="Kombinerad läsning och skrivning av IOPS visar att läsningarna är 78.3 k och skrivningar är 12,6 k IOPS.":::

### <a name="maximum-combined-throughput"></a>Maximalt kombinerat data flöde

Om du vill hämta maximalt kombinerat Läs-och Skriv data flöde använder du en större block storlek och ett stort ködjup med flera trådar som utför läsningar och skrivningar. Du kan använda en block storlek på 64 KB och ködjup på 128.