---
title: Kluster storleks guide för interaktiv fråga i Azure HDInsight
description: Interaktiv storleks guide för frågor i Azure HDInsight
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: a7baa9340a1f0a99b94bfcbe535c73d0b502e2a0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98933054"
---
# <a name="interactive-query-cluster-sizing-guide-in-azure-hdinsight"></a>Kluster storleks guide för interaktiv fråga i Azure HDInsight

I det här dokumentet beskrivs storleken på LLAP (HDInsight Interactive Query Cluster) för en typisk arbets belastning för att uppnå rimlig prestanda. De rekommendationer som anges i det här dokumentet är generiska och vissa arbets belastningar kan behöva en speciell justering.

## <a name="default-vm-types-for-interactive-query"></a>Standard typer av virtuella datorer för interaktiv fråga

| Nodtyp | Instans | Storlek |
|---|---|---|
| Head | D13 v2 | 8 VIRTUELLA PROCESSORER, 56 GB RAM, 400 GB SSD |
| Företag | D14 v2 | 16-VIRTUELLA PROCESSORER, 112 GB RAM, 800 GB SSD |
| ZooKeeper | A4 V2 | 4 VIRTUELLA PROCESSORER, 8 GB RAM, 40 GB SSD |

## <a name="recommended-configurations"></a>Rekommenderade konfigurationer

De rekommenderade värdena för konfigurationer baseras på noden D14 v2 Type Worker.

| Tangent | Värde | Beskrivning |
|---|---|---|
| garn. nodemanager. Resource. Memory-MB | 102400 (MB) | Totalt minne angivet i MB för alla garn behållare på en nod. |
| garn. Scheduler. maximum-Allocation-MB | 102400 (MB) | Maximal allokering för varje container förfrågan i RM, i MB. Minnes förfrågningar som är högre än det här värdet börjar gälla. |
| garn. Scheduler. maximum-Allocation-virtuella kärnor | 12 |Det maximala antalet processor kärnor för varje container förfrågan i Resource Manager. Begär Anden som är högre än det här värdet börjar gälla. |
| garn. Scheduler. Capacity. root. LLAP. Capacity | 90 % | GARN kapacitets tilldelning för LLAP-kö.  |
| Hive. Server2. Tez. sessions. per. default. Queue | number_of_worker_nodes |Antalet sessioner för varje kö med namnet i Hive. Server2. Tez. default. queues. Det här numret motsvarar antalet frågeuttryck (Tez AMs). |
| Tez. am. Resource. Memory. MB | 4096 (MB) | Mängden minne i MB som ska användas av Tez-AppMaster. |
| Hive. Tez. container. size | 4096 (MB) | Angiven storlek för Tez-behållare i MB. |
| hive.llap.daemon.num.executors | 12 | Antal körningar per LLAP daemon. |
| Hive. LLAP. io. trådpool. size | 12 | Storlek på trådpoolen för körningar. |
| Hive. LLAP. daemon. garn. container. MB | 86016 (MB) | Totalt minne i MB som används av enskilda LLAP-daemon (minne per daemon).|
| Hive. LLAP. io. Memory. size | 409600 (MB) | Cachestorlek i MB per LLAP daemon-cache har Aktiver ATS. |
| Hive. Auto. convert. Join. noconditionaltask. size | 2048 (MB) | minnes storlek i MB för att mappa koppling. |

## <a name="llap-daemon-size-estimations"></a>Storleks uppskattningar för LLAP daemon  

### <a name="yarnnodemanagerresourcememory-mb"></a>garn. nodemanager. Resource. Memory-MB

Det här värdet anger en maximal mängd minne i MB som används av garn behållarna på varje nod. Det anger mängden minnes garn som kan användas på den här noden, så det här värdet ska vara mindre än det totala minnet på den noden.

Ange det här värdet = [Totalt fysiskt minne på nod] – [minne för OS + andra tjänster].

Vi rekommenderar att du anger det här värdet till ~ 90% av tillgängligt RAM-minne. För D14 v2 är det rekommenderade värdet **102400 MB**.

### <a name="yarnschedulermaximum-allocation-mb"></a>garn. Scheduler. maximum-Allocation-MB

Det här värdet anger den maximala allokeringen för varje container förfrågan i Resource Manager, i MB. Minnes förfrågningar som är större än det angivna värdet börjar gälla. Resurs hanteraren kan bara ge minne till behållare i steg om `yarn.scheduler.minimum-allocation-mb` och får inte överskrida den storlek som anges av `yarn.scheduler.maximum-allocation-mb` . Värdet får inte vara större än det totala minnet för noden som anges av `yarn.nodemanager.resource.memory-mb` .

För D14 v2 Worker-arbetsnoder är det rekommenderade värdet **102400 MB**

### <a name="yarnschedulermaximum-allocation-vcores"></a>garn. Scheduler. maximum-Allocation-virtuella kärnor

Den här konfigurationen visar det maximala antalet virtuella processor kärnor för varje container förfrågan i Resource Manager. Att begära ett högre värde än den här konfigurationen börjar inte gälla. Den här konfigurationen är en global egenskap hos garn Scheduler. För LLAP daemon-behållare kan det här värdet anges till 75% av de totala tillgängliga virtuella kärnorna (virtuella kärnor). Återstående 25% bör vara reserverad för NodeManager, DataNode och andra tjänster som körs på arbetsnoderna.  

För D14 v2 Worker-arbetsnoder finns det 16 virtuella kärnor och 75% av 16 virtuella kärnor kan anges. Det rekommenderade värdet för LLAP daemon-behållare är **12**.

### <a name="hiveserver2tezsessionsperdefaultqueue"></a>Hive. Server2. Tez. sessions. per. default. Queue

Det här konfiguration svärdet avgör antalet Tez-sessioner som ska startas parallellt för var och en av de köer som anges av `hive.server2.tez.default.queues` . Värdet motsvarar antalet Tez-AMs (fråga koordinatorer). Vi rekommenderar att du är samma som antalet arbetsnoder för att ha en Tez per nod. Antalet Tez-AMs kan vara högre än antalet LLAP daemon-noder. Deras primära ansvar är att koordinera frågekörningen och tilldela LLAP-daemonar för körning. Vi rekommenderar att du behåller det som Multiple av ett antal LLAP daemon-noder för att uppnå högre genomflöde.  

Standard-HDInsight-kluster har fyra LLAP-daemonar som körs på fyra arbetsnoder, så det rekommenderade värdet är **4**.  

### <a name="tezamresourcememorymb-hivetezcontainersize"></a>Tez. am. Resource. Memory. MB, Hive. Tez. container. size

`tez.am.resource.memory.mb` definierar Tez program huvud storlek.  
Det rekommenderade värdet är **4096 MB**.

`hive.tez.container.size` definierar mängden minne som anges för Tez-behållaren. Det här värdet måste anges mellan den minsta storleken för garn behållare ( `yarn.scheduler.minimum-allocation-mb` ) och den största garn storleken ( `yarn.scheduler.maximum-allocation-mb` ).  
Vi rekommenderar att du är inställd på **4096 MB**.  

En allmän regel är att hålla den mindre än mängden minne per processor som överväger en processor per behållare. `Rreserve` minne för antalet Tez-AMs på en nod innan du ger minnet för LLAP daemon. Om du till exempel använder två Tez-AMs (4 GB varje) per nod, ger du 82 GB av 90 GB för LLAP daemon reserverar 8 GB för två Tez AMs.

### <a name="yarnschedulercapacityrootllapcapacity"></a>garn. Scheduler. Capacity. root. LLAP. Capacity

Det här värdet anger en procent andel av kapaciteten som anges för LLAP-kön. Det HDInsights interaktiva fråge klustret ger 90% av den totala kapaciteten för LLAP-kön och de återstående 10% är inställda på standard kön för andra behållar allokeringar.  
För D14v2 Worker-arbetsnoder är det rekommenderade värdet **90** för LLAP-kön.

### <a name="hivellapdaemonyarncontainermb"></a>Hive. LLAP. daemon. garn. container. MB

Den totala minnes storleken för LLAP daemon är beroende av följande komponenter:

* Konfiguration av garn behållarens storlek ( `yarn.scheduler.maximum-allocation-mb` , `yarn.scheduler.maximum-allocation-mb` , `yarn.nodemanager.resource.memory-mb` )

* Heap-minne som används av exekverare (XMX)

    Mängden tillgängligt RAM-minne efter att ha tagit slut på utrymme.  
    För D14 v2, HDI 4,0 – det här värdet är (86 GB-6 GB) = 80 GB  
    För D14 v2, HDI 3,6 – det här värdet är (84 GB-6 GB) = 78 GB

* Off-heap cache-minne per daemon (Hive. LLAP. io. Memory. size)

* Både

    Det är en del av det minne av stor heap som används för att flytta Java VM (Metaspace, tråds tack, GC-datastrukturer osv.). Den här delen observeras vara cirka 6% av heap-storleken (XMX). För att vara på sidan säkrare kan den beräknas som 6% av den totala minnes storleken för LLAP daemon. Eftersom SSD-cachen är aktive rad kan LLAP daemon använda allt tillgängligt minnes utrymme som endast ska användas för heap.  
    För D14 v2 är det rekommenderade värdet ceil (86 GB x 0,06) ~ = **6 GB**.  

Minne per daemon = [minne i cacheminnet för minnes storlek] + [Heapstorlek] + [utrymme].

Det kan beräknas på följande sätt:

Tez AM minne per nod = [(antal Tez AMs/antal LLAP daemon-noder) * Tez AM storlek].
LLAP daemon container size = [90% av garn Max container Memory] – [Tez-minne per nod].

För D14 v2 Work Node, HDI 4,0 – det rekommenderade värdet är (90 – (1/1 * 4 GB)) = **86 GB**.
(För HDI 3,6 är det rekommenderade värdet **84 GB** eftersom du bör reservera ~ 2 GB för skjutreglaget.)  

### <a name="hivellapiomemorysize"></a>Hive. LLAP. io. Memory. size

Den här konfigurationen är mängden minne som är tillgängligt som cache för LLAP daemon. LLAP-daemonar kan använda SSD som cache. Inställningen `hive.llap.io.allocator.mmap` = True kommer att aktivera SSD-cachelagring. D14 v2 levereras med ~ 800 GB SSD och SSD-cachelagring är aktiverat som standard för Interactive Query Cluster (LLAP). Den har kon figurer ATS för att använda 50% av SSD-utrymmet för cache på annan heap.

För D14 v2 är det rekommenderade värdet **409600 MB**.  

För andra virtuella datorer, där ingen SSD-cachelagring har Aktiver ATS, är det fördelaktigt att ge en del av tillgängligt RAM-minne för cachelagring av LLAP för Justera den totala minnes storleken för LLAP daemon enligt följande:  

Totalt LLAP daemon-minne = [LLAP cache size] + [Heapstorlek] + [utrymme].

Vi rekommenderar att du justerar cachestorleken och storleken på den heap som passar din arbets belastning bäst.  

### <a name="hivellapdaemonnumexecutors"></a>hive.llap.daemon.num.executors

Den här konfigurationen styr antalet körningar som kan köra aktiviteter parallellt per LLAP daemon. Det här värdet är en balans mellan antalet tillgängliga virtuella kärnor, mängden minne som anges per utförar och totalt tillgängligt minne per LLAP daemon. Vanligt vis vill vi att det här värdet ska vara så nära som möjligt för antalet kärnor.

För D14 v2 finns 16 virtuella kärnor tillgängliga, men alla virtuella kärnor kan inte anges. Arbetsnoderna kör också andra tjänster som NodeManager, DataNode och Metrics Monitor som behöver en del av tillgängliga virtuella kärnor. Det här värdet kan konfigureras upp till 75% av den totala virtuella kärnor som är tillgänglig på den noden.

För D14 v2 är det rekommenderade värdet (. 75 X 16) = **12**

Vi rekommenderar att du reserverar ~ 6 GB heap-utrymme per utförar. Justera antalet körningar baserat på den tillgängliga LLAP daemon-storlek och antal tillgängliga virtuella kärnor per nod.  

### <a name="hivellapiothreadpoolsize"></a>Hive. LLAP. io. trådpool. size

Det här värdet anger storleken på trådpoolen för körningar. Eftersom körningar har åtgärd ATS som de har angetts kommer det att vara samma som antalet körningar per LLAP daemon.

För D14 v2 rekommenderar vi att du anger värdet till **12**.

Den här konfigurationen får inte överskrida `yarn.nodemanager.resource.cpu-vcores` värdet.

### <a name="hiveautoconvertjoinnoconditionaltasksize"></a>Hive. Auto. convert. Join. noconditionaltask. size

Kontrol lera att du har `hive.auto.convert.join.noconditionaltask` aktiverat den här parametern för att börja gälla. Med den här konfigurationen kan användaren ange storleken på de tabeller som får plats i minnet för att mappa koppling. Om summan av storleken på n-1 av n `tables/partitions` -Way-anslutningen är mindre än det konfigurerade värdet väljs kart kopplingen. Minnes storleken för LLAP-utförar ska användas för att beräkna tröskelvärdet för autoconvert för att mappa koppling.

För D14 v2 rekommenderar vi att du anger det här värdet till **2048 MB**.

Vi rekommenderar att du justerar det här värdet som är lämpligt för din arbets belastning eftersom inställningen för lågt värde inte kan använda funktionen autoconvert. Om du ställer in den för hög kan det leda till att GC pausar, vilket kan påverka frågans prestanda negativt.

## <a name="next-steps"></a>Nästa steg

* [Gateway-rikt linjer](gateway-best-practices.md)
* [Avmystifierar moln Apache Tez-minnes justering-steg för steg](https://community.cloudera.com/t5/Community-Articles/Demystify-Apache-Tez-Memory-Tuning-Step-by-Step/ta-p/245279)
* [Mappa kopplings minnes storlek för LLAP](https://community.cloudera.com/t5/Community-Articles/Map-Join-Memory-Sizing-For-LLAP/ta-p/247462)
* [LLAP – en arkitektur översikt med en sida](https://community.cloudera.com/t5/Community-Articles/LLAP-a-one-page-architecture-overview/ta-p/247439)
* [LLAP djup i Hive](https://community.cloudera.com/t5/Community-Articles/Hive-LLAP-deep-dive/ta-p/248893)
