---
title: Rekommenderade prestandatester för prestandatest – Azure NetApp Files
description: Lär dig mer om benchmark test-rekommendationer för volym prestanda och mått med hjälp av Azure NetApp Files.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 08/07/2019
ms.openlocfilehash: f73091552a78760024189b173897913edca724bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100593400"
---
# <a name="performance-benchmark-test-recommendations-for-azure-netapp-files"></a>Rekommendationer från benchmark-prestandatest för Azure NetApp Files

Den här artikeln innehåller test rekommendationer för prestandatest för volym prestanda och mått med hjälp av Azure NetApp Files.

## <a name="overview"></a>Översikt

Om du vill förstå prestanda egenskaperna för en Azure NetApp Files volym kan du använda verktyget med öppen källkod för [att köra](https://github.com/axboe/fio) en serie benchmarks för att simulera en rad olika arbets belastningar. FIO kan installeras på både Linux-och Windows-baserade operativ system.  Det är ett utmärkt verktyg för att få en snabb ögonblicks bild av både IOPS och data flöde för en volym.

### <a name="vm-instance-sizing"></a>Storlek på virtuell dator instans

För bästa resultat bör du kontrol lera att du använder en virtuell dator instans (VM) som har rätt storlek för att utföra testerna. I följande exempel används en Standard_D32s_v3-instans. Mer information om storlekar för virtuella dator instanser finns i [storlekar för virtuella Windows-datorer i Azure](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) för Windows-baserade virtuella datorer och [storlekar för virtuella Linux-datorer i Azure](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) för Linux-baserade virtuella datorer.

### <a name="azure-netapp-files-volume-sizing"></a>Azure NetApp Files volym storlek

Se till att du väljer rätt service nivå och volym kvot storlek för den förväntade prestanda nivån. Mer information finns i [tjänste nivåer för Azure NetApp Files](azure-netapp-files-service-levels.md) .

### <a name="virtual-network-vnet-recommendations"></a>Rekommendationer för virtuella nätverk (VNet)

Du bör utföra benchmark-testning i samma VNet som Azure NetApp Files. Exemplet nedan visar rekommendationen:

![VNet-rekommendationer](../media/azure-netapp-files/azure-netapp-files-benchmark-testing-vnet.png)

## <a name="installation-of-fio"></a>Installation av FIO

FIO är tillgängligt i binärt format för både Linux och Windows. Följ avsnittet binära paket i [FIO](https://github.com/axboe/fio) för att installera för valfri plattform.

## <a name="fio-examples-for-iops"></a>FIO-exempel för IOPS 

FIO-exemplen i det här avsnittet använder följande installation:
* Storlek på virtuell dator instans: D32s_v3
* Service nivå och storlek för kapacitets bassäng: Premium/50 TiB
* Volym kvots storlek: 48 TiB

I följande exempel visas FIO slumpmässiga läsningar och skrivningar.

### <a name="fio-8k-block-size-100-random-reads"></a>FIO: 8k block storlek 100% slumpmässiga läsningar

`fio --name=8krandomreads --rw=randread --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128 --size=4G --runtime=600 --group_reporting`

### <a name="output-68k-read-iops-displayed"></a>Utdata: 68k Read IOPS visas

`Starting 4 processes`  
`Jobs: 4 (f=4): [r(4)][84.4%][r=537MiB/s,w=0KiB/s][r=68.8k,w=0 IOPS][eta 00m:05s]`

### <a name="fio-8k-block-size-100-random-writes"></a>FIO: 8k block storlek 100% slumpmässiga skrivningar

`fio --name=8krandomwrites --rw=randwrite --direct=1 --ioengine=libaio --bs=8k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-73k-write-iops-displayed"></a>Utdata: 73k Skriv IOPS som visas

`Starting 4 processes`  
`Jobs: 4 (f=4): [w(4)][26.7%][r=0KiB/s,w=571MiB/s][r=0,w=73.0k IOPS][eta 00m:22s]`

## <a name="fio-examples-for-bandwidth"></a>FIO-exempel för bandbredd

I exemplen i det här avsnittet visas FIO sekventiella läsningar och skrivningar.

### <a name="fio-64k-block-size-100-sequential-reads"></a>FIO: 64 kB block storlek 100% sekventiella läsningar

`fio --name=64kseqreads --rw=read --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-118-gbits-throughput-displayed"></a>Utdata: 11,8 Gbit/s-dataflöde visas

`Starting 4 processes`  
`Jobs: 4 (f=4): [R(4)][40.0%][r=1313MiB/s,w=0KiB/s][r=21.0k,w=0 IOPS][eta 00m:09s]`

### <a name="fio-64k-block-size-100-sequential-writes"></a>FIO: 64 kB block storlek 100% sekventiella skrivningar

`fio --name=64kseqwrites --rw=write --direct=1 --ioengine=libaio --bs=64k --numjobs=4 --iodepth=128  --size=4G --runtime=600 --group_reporting`

### <a name="output-122-gbits-throughput-displayed"></a>Utdata: 12,2 Gbit/s-dataflöde visas

`Starting 4 processes`  
`Jobs: 4 (f=4): [W(4)][85.7%][r=0KiB/s,w=1356MiB/s][r=0,w=21.7k IOPS][eta 00m:02s]`

## <a name="volume-metrics"></a>Volym mått

Azure NetApp Files prestanda data är tillgängliga via Azure Monitor räknare. Räknarna är tillgängliga via Azure Portal och REST API Hämta förfrågningar. 

Du kan visa historiska data för följande information:
* Genomsnittlig Läs fördröjning 
* Genomsnittlig Skriv fördröjning 
* Läs IOPS (genomsnitt)
* Skriv IOPS (genomsnitt)
* Logisk volym storlek (genomsnitt)
* Storlek på volym ögonblicks bild (genomsnitt)

### <a name="using-azure-monitor"></a>Använda Azure Monitor 

Du kan komma åt Azure NetApp Files räknare per volym på sidan mått, som du ser nedan:

![Azure Monitor mått](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-metrics.png)

Du kan också skapa en instrument panel i Azure Monitor för Azure NetApp Files genom att gå till sidan mått, filtrera på NetApp och ange de olika volym räknarna: 

![Azure Monitor-instrumentpanel](../media/azure-netapp-files/azure-netapp-files-benchmark-monitor-dashboard.png)

### <a name="azure-monitor-api-access"></a>Azure Monitor API-åtkomst

Du kan komma åt Azure NetApp Files räknare genom att använda REST API-anrop. Se [mått som stöds med Azure Monitor: Microsoft. NetApp/netAppAccounts/capacityPools/Volumes](../azure-monitor/essentials/metrics-supported.md#microsoftnetappnetappaccountscapacitypoolsvolumes) för räknare för kapacitets grupper och volymer.

I följande exempel visas en Hämta URL för visning av logisk volym storlek:

`#get ANF volume usage`  
`curl -X GET -H "Authorization: Bearer TOKENGOESHERE" -H "Content-Type: application/json" https://management.azure.com/subscriptions/SUBIDGOESHERE/resourceGroups/RESOURCEGROUPGOESHERE/providers/Microsoft.NetApp/netAppAccounts/ANFACCOUNTGOESHERE/capacityPools/ANFPOOLGOESHERE/Volumes/ANFVOLUMEGOESHERE/providers/microsoft.insights/metrics?api-version=2018-01-01&metricnames=VolumeLogicalSize`


## <a name="next-steps"></a>Nästa steg

- [Tjänstnivåer för Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Prestandamått för Linux](performance-benchmarks-linux.md)