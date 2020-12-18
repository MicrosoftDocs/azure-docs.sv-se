---
title: Storleks guide för HDInsight-LLAP (Interactive Query Cluster)
description: LLAP storleks guide
ms.service: hdinsight
ms.topic: troubleshooting
author: aniket-ms
ms.author: aadnaik
ms.reviewer: HDI HiveLLAP Team
ms.date: 05/05/2020
ms.openlocfilehash: 626b061cc237f7238d47863a3e1ed88961d2f742
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/18/2020
ms.locfileid: "97680514"
---
# <a name="azure-hdinsight-interactive-query-cluster-hive-llap-sizing-guide"></a>Storleks guide för Azure HDInsight-interaktiv fråga kluster (Hive LLAP)

Det här dokumentet beskriver storleken på det interaktiva LLAP-klustret för HDInsight (Hive-kluster) för en typisk arbets belastning för att uppnå rimlig prestanda. Observera att rekommendationerna i det här dokumentet är allmänna rikt linjer och att vissa arbets belastningar kan kräva en speciell justering.

### <a name="azure-default-vm-types-for-hdinsight-interactive-query-clusterllap"></a>**Azure standard VM-typer för HDInsight-Interactive Query-kluster (LLAP)**

| Nodtyp      | Instans | Storlek     |
| :---        |    :----:   | :---     |
| Head      | D13 v2       | 8 virtuella processorer, 56 GB RAM, 400 GB SSD   |
| Företag   | **D14 v2**        | **16 virtuella processorer, 112 GB RAM, 800 GB SSD**       |
| ZooKeeper   | A4 V2        | 4 virtuella processorer, 8 GB RAM, 40 GB SSD       |

**_Obs! alla rekommenderade konfigurationer baseras på D14 v2-typ Worker Node_* _  

### <a name="_configuration"></a>_ *Konfiguration:**    
| Konfigurationsnyckel      | Rekommenderat värde  | Beskrivning |
| :---        |    :----:   | :---     |
| garn. nodemanager. Resource. Memory-MB | 102400 (MB) | Totalt minne angivet i MB för alla garn behållare på en nod | 
| garn. Scheduler. maximum-Allocation-MB | 102400 (MB) | Maximal allokering för varje container förfrågan i RM, i MB. Minnes förfrågningar som är högre än det här värdet börjar gälla |
| garn. Scheduler. maximum-Allocation-virtuella kärnor | 12 |Det maximala antalet processor kärnor för varje container förfrågan i Resource Manager. Begär Anden som är högre än det här värdet börjar gälla. |
| garn. nodemanager. Resource. CPU-virtuella kärnor | 12 | Antal processor kärnor per NodeManager som kan tilldelas för behållare. |
| garn. Scheduler. Capacity. root. LLAP. Capacity | 85 (%) | LLAP-kö för garn kapacitets tilldelning  |
| Tez. am. Resource. Memory. MB | 4096 (MB) | Mängden minne i MB som ska användas av Tez-AppMaster |
| Hive. Server2. Tez. sessions. per. default. Queue | <number_of_worker_nodes> |Antalet sessioner för varje kö med namnet i Hive. Server2. Tez. default. queues. Det här talet motsvarar antalet frågans koordinatorer (Tez AMs) |
| Hive. Tez. container. size | 4096 (MB) | Angiven storlek på Tez-behållare i MB |
| hive.llap.daemon.num.executors | 19 | Antal körningar per LLAP daemon | 
| Hive. LLAP. io. trådpool. size | 19 | Storlek på trådpoolen för körningar |
| Hive. LLAP. daemon. garn. container. MB | 81920 (MB) | Totalt minne i MB som används av enskilda LLAP-daemonar (minne per daemon)
| Hive. LLAP. io. Memory. size | 242688 (MB) | Cachestorlek i MB per LLAP daemon tillhandahöll cache-minne för SSD är aktiverat |
| Hive. Auto. convert. Join. noconditionaltask. size | 2048 (MB) | minnes storlek i MB för kart koppling |

### <a name="llap-architecturecomponents"></a>**LLAP-arkitektur/-komponenter:**  

![LLAP-arkitektur/-komponenter](./media/hive-llap-sizing-guide/LLAP_architecture_sizing_guide.png "LLAP-arkitektur/-komponenter")

### <a name="llap-daemon-size-estimations"></a>**Storleks uppskattningar för LLAP daemon:** 

#### <a name="1-determining-total-yarn-memory-allocation-for-all-containers-on-a-node"></a>**1. bestämmande av total GARNs minnesallokering för alla behållare på en nod**    
Konfiguration: **_garn. nodemanager. Resource. Memory-MB_* _  

Det här värdet anger en högsta mängd minne i MB som kan användas av garn behållare på varje nod. Det angivna värdet måste vara mindre än den totala mängden fysiskt minne på den noden.   
Totalt minne för alla garn behållare på en Node = (Totalt fysiskt minne – minne för OS + andra tjänster)  
Ange det här värdet till ~ 90% av den tillgängliga RAM-storleken.  
För D14 v2 är det rekommenderade värdet _ * 102400 MB * *. 

#### <a name="2-determining-maximum-amount-of-memory-per-yarn-container-request"></a>**2. fastställer den maximala mängden minne per garn container förfrågan**  
Konfiguration: **_garn. Scheduler. maximum-Allocation-MB_* _

Det här värdet anger den maximala allokeringen för varje container förfrågan i Resource Manager, i MB. Minnes förfrågningar som är större än det angivna värdet börjar gälla. Resurs hanteraren kan ge minne till behållare i steg om _yarn. Scheduler. minimum-Allocation-MB * och får inte överskrida den storlek som anges av *garn. Scheduler. maximum-Allocation-MB*. Det angivna värdet får inte vara större än det totala antalet angivna minne för alla behållare på den nod som anges av *garn. nodemanager. Resource. Memory-MB*.    
För D14 v2 Worker-arbetsnoder är det rekommenderade värdet **102400 MB**

#### <a name="3-determining-maximum-amount-of-vcores-per-yarn-container-request"></a>**3. fastställer maximal mängd virtuella kärnor per garn container-begäran**  
Konfiguration: **_garn. Scheduler. maximum-Allocation-virtuella kärnor_* _  

Det här värdet anger det maximala antalet virtuella processor kärnor för varje container förfrågan i Resource Manager. Att begära ett högre antal virtuella kärnor än det här värdet börjar inte gälla. Det är en global egenskap hos garn Scheduler. För LLAP daemon-behållare kan det här värdet anges till 75% av det totala antalet tillgängliga virtuella kärnor. Återstående 25% bör vara reserverad för NodeManager, DataNode och andra tjänster som körs på arbetsnoderna.  
Det finns 16 virtuella kärnor på D14 v2-VM: ar och 75% av totalt 16 virtuella kärnor kan användas av LLAP daemon-behållare.  
För D14 v2 är det rekommenderade värdet _ * 12 * *.  

#### <a name="4-number-of-concurrent-queries"></a>**4. antal samtidiga frågor**  
Konfiguration: **_Hive. Server2. Tez. sessions. per. default. Queue_* _

Det här konfiguration svärdet avgör antalet Tez-sessioner som kan startas parallellt. Dessa Tez-sessioner startas för var och en av de köer som anges av "Hive. Server2. Tez. default. Queues". Det motsvarar antalet Tez-AMs (fråga koordinatorer). Vi rekommenderar att du är samma som antalet arbetsnoder. Antalet Tez-AMs kan vara högre än antalet LLAP daemon-noder. Tez är ett primärt ansvar för att samordna frågekörningen och tilldela till motsvarande LLAP-daemon för körning. Behåll det här värdet i multipler av ett antal LLAP daemon-noder för att uppnå högre genomflöde.  

Standard-HDInsight-kluster har fyra LLAP-daemonar som körs på fyra arbetsnoder, så det rekommenderade värdet är _ * 4 * *.  

**Ambari UI-skjutreglage för Hive-konfigurations variabel `hive.server2.tez.sessions.per.default.queue` :**

![' LLAP maximalt antal samtidiga frågor '](./media/hive-llap-sizing-guide/LLAP_sizing_guide_max_concurrent_queries.png "LLAP maximalt antal samtidiga frågor")

#### <a name="5-tez-container-and-tez-application-master-size"></a>**5. Tez-behållare och Tez program huvud storlek**    
Konfiguration: **_Tez. am. Resource. Memory. MB, Hive. Tez. container. size_* _  

_tez. am. Resource. Memory. MB *-definierar programmets huvud storlek för Tez.  
Det rekommenderade värdet är **4096 MB**.
   
*Hive. Tez. container. size* -definierar mängden minne som anges för Tez container. Det här värdet måste anges mellan den minsta storleken för garn behållare (*garn. Scheduler. minimum-Allocation-MB*) och maximal container storlek för garn (*garn. Scheduler. maximum-Allocation-MB*). LLAP daemon-körningar använder det här värdet för att begränsa minnes användning per utförar.  
Det rekommenderade värdet är **4096 MB**.  

#### <a name="6-llap-queue-capacity-allocation"></a>**6. LLAP för kös kap.**   
Konfiguration: **_garn. Scheduler. Capacity. root. LLAP. Capacity_* _  

Det här värdet anger en procent andel av kapaciteten som har tilldelats LLAP-kön. Kapacitets tilldelningarna kan ha olika värden för olika arbets belastningar beroende på hur garn köerna har kon figurer ATS. Om arbets belastningen är skrivskyddad kan du ställa in den så hög som 90% av kapaciteten. Men om arbets belastningen är blandning av uppdaterings-/borttagnings-/sammanfognings åtgärder med hjälp av hanterade tabeller, rekommenderar vi att du ger 85% av kapaciteten för LLAP-kön. Den återstående 15% kapaciteten kan användas av andra aktiviteter, till exempel komprimering osv. för att allokera behållare från standard kön. På så sätt kan uppgifter i standard kön inte berövas garn resurser.    

För D14v2 Worker-arbetsnoder är det rekommenderade värdet för LLAP-kön _ * 85 * *.     
(För skrivskyddade arbets belastningar kan det ökas upp till 90 på lämpligt sätt.)  

#### <a name="7-llap-daemon-container-size"></a>**7. LLAP daemon container-storlek**    
Konfiguration: **_Hive. LLAP. daemon. garn. container. MB_* _  
   
LLAP Daemon körs som en garn behållare på varje arbetsnod. Den totala minnes storleken för LLAP daemon-behållaren beror på följande faktorer,    
_ Konfigurationer av garn behållarens storlek (garn. Scheduler. minimum-Allocation-MB, garn. Scheduler. maximum-Allocation-MB, garn. nodemanager. Resource. Memory-MB)
*  Antalet Tez-AMs på en nod
*  Totalt minne som kon figurer ATS för alla behållare i en nod och LLAP-kös Kap kap.  

Det minne som krävs av Tez program hanterare (Tez) kan beräknas på följande sätt.  
Tez fungerar som en frågeplan och antalet Tez-AMs bör konfigureras baserat på ett antal samtidiga frågor som ska hanteras. Teoretiskt sett kan vi betrakta en Tez per arbets nod. Men det är möjligt att du kan se fler än en Tez på en arbetsnod. I beräknings syfte antar vi en enhetlig distribution av Tez-AMs över alla LLAP daemon-noder/arbetsnoder.
Vi rekommenderar att du har 4 GB minne per Tez.  

Antalet Tez-AMS = värdet som anges av Hive config ***Hive. Server2. Tez. sessions. per. default. Queue** _.  
Antalet LLAP daemon-noder som anges av miljövariabeln _*_num_llap_nodes_for_llap_daemons_*_ i AMBARI-användargränssnittet.  
Tez AM container storlek = värdet som anges av Tez config _*_Tez. am. Resource. Memory. MB_*_.  

Tez am minne per nod = _ *(** ceil **(** antal Tez AMS **/** antalet LLAP daemon **-noder)** **x** Tez am container storlek **)**  
För D14 v2 har standard konfigurationen fyra Tez-AMs och fyra LLAP daemon-noder.  
Tez AM minne per nod = (ceil (4/4) x 4 GB) = 4 GB

Det totala tillgängliga minnet för LLAP kö per arbets nod kan beräknas på följande sätt:  
Det här värdet beror på den totala mängden minne som är tillgängligt för alla garn behållare på en nod (*garn. nodemanager. Resource. Memory-MB*) och procent andelen av kapaciteten som kon figurer ATS för LLAP Queue (*garn. Scheduler. Capacity. root. LLAP. Capacity*).  
Totalt minne för LLAP-kön på arbetsnoden = totalt tillgängligt minne för alla garn behållare på en nod x procent andel av kapaciteten för LLAP-kön.  
För D14 v2 är det här värdet (100 GB x 0,85) = 85 GB.

Storleken på LLAP daemon-behållare beräknas på följande sätt:

**LLAP daemon container size = (totalt minne för LLAP-kö på en workernode) – (Tez AM minne per nod)-(storlek på tjänstens huvud behållare)**  
Det finns bara en tjänst huvud (program huvud för LLAP-tjänsten) i klustret som har skapats på en av arbetsnoderna. För beräknings syfte betraktar vi en tjänst huvud per arbetsnod.  
För D14 v2 Work Node, HDI 4,0 – det rekommenderade värdet är (85 GB-4 GB-1 GB)) = **80 GB**   
(För HDI 3,6 är det rekommenderade värdet **79 GB** eftersom du bör reservera ytterligare ~ 2 GB för skjutreglaget.)  

#### <a name="8-determining-number-of-executors-per-llap-daemon"></a>**8. fastställer antalet körningar per LLAP daemon**  
Konfiguration: **_hive.llap.daemon.num.executors_* _, _*_Hive. LLAP. io. trådpool. size_*_

_*_hive.llap.daemon.num.executors_*_:   
Den här konfigurationen styr antalet körningar som kan köra aktiviteter parallellt per LLAP daemon. Det här värdet beror på antalet virtuella kärnor, mängden använt minne per utförar och mängden totalt minne som är tillgängligt för LLAP daemon-behållare.    Antalet körningar kan överföras till 120% av de tillgängliga virtuella kärnor per arbets nod. Det bör dock justeras om det inte uppfyller minnes kraven baserat på det minne som krävs per utförar och storleken på LLAP daemon-behållare.

Varje utförar motsvarar en Tez-behållare och kan förbruka 4 GB (Tez container storlek) för minnet. Alla körningar i LLAP daemon delar samma heap-minne. Med antagandet att inte alla körningar kör minnes intensiva åtgärder samtidigt kan du överväga 75% av storleken på Tez container (4 GB) per utförar. På så sätt kan du öka antalet körningar genom att ge varje utförar mindre minne (t. ex. 3 GB) för ökad parallellitet. Vi rekommenderar dock att du justerar den här inställningen för mål arbets belastningen.

Det finns 16 virtuella kärnor på D14 v2-virtuella datorer.
För D14 v2 är det rekommenderade värdet för antal körningar (16 virtuella kärnor x 120%) ~ = _ *19** på varje arbetsnoden som överväger 3 GB per utförar.

**_Hive. LLAP. io. trådpool. size_*_: det här värdet anger Poolens storlek för körningar. Eftersom körningar har åtgärd ATS som de har angetts kommer det att vara samma som antalet körningar per LLAP daemon. För D14 v2 är det rekommenderade värdet _* 19**.

#### <a name="9-determining-llap-daemon-cache-size"></a>**9. avgör storleken på LLAP daemon-cachen**  
Konfiguration: **_Hive. LLAP. io. Memory. size_* _

LLAP daemon container Memory består av följande komponenter: _ Huvud rum
*  Heap-minne som används av exekverare (XMX)
*  Minnes intern cache per daemon (minnes storlek för ledigt minne, ej tillämpbar när SSD-cachen är aktive rad)
*  Metadata för minnes intern cache (gäller endast när SSD-cachen är aktive rad)

**Utrymmes storlek**: den här storleken anger en del av ett minnes minne som används för Java VM-overhead (Metaspace, tråds tack, GC-datastrukturer osv.). I allmänhet är den här overheadkostnaden cirka 6% av heap-storleken (XMX). För att vara på sidan säkrare kan det här värdet beräknas som 6% av den totala minnes storleken för LLAP daemon.  
För D14 v2 är det rekommenderade värdet ceil (80 GB x 0,06) ~ = **4 GB**.  

**Heap-storlek (XMX)**: det är mängden heap-minne som är tillgängligt för alla körningar.
Total Heap-storlek = antal körningar x 3 GB  
För D14 v2 är det här värdet 19 x 3 GB = **57 GB**  

`Ambari environment variable for LLAP heap size:`

![LLAP Heap-storlek](./media/hive-llap-sizing-guide/LLAP_sizing_guide_llap_heap_size.png "LLAP Heap-storlek")

När SSD-cachen är inaktive rad är minnet i minnet den mängd minne som är kvar efter att ha tagit slut på utrymme och Heap-storlek från LLAP daemon-behållarens storlek.

Storleks beräkningen för cachen varierar när SSD-cachen är aktive rad.
Om du ställer in *Hive. LLAP. io. allokerare. mmap* = True aktive ras SSD-cachelagring.
När SSD-cachen är aktive rad kommer en del av minnet att användas för att lagra metadata för SSD-cachen. Metadata lagras i minnet och förväntas vara ~ 8% av storleken på SSD-cachen.   
Storlek på SSD-cache i minnet = LLAP daemon container size-(huvud rum + Heap-storlek)  
För D14 v2 med HDI 4,0 är SSD cache i minnes storlek = 80 GB-(4 GB + 57 GB) = **19 GB**  
För D14 v2 med HDI 3,6 är SSD cache i minnes storlek = 79 GB-(4 GB + 57 GB) = **18 GB**

Om storleken på tillgängligt minne för lagring av SSD cache-metadata kan du beräkna storleken på SSD-cachen som kan användas.  
Storlek på minnes intern metadata för SSD cache = LLAP daemon container size-(huvud rummet + Heap-storlek) = 19 GB     
Storlek på SSD cache = storleken på minnesbaserade metadata för SSD-cache (19 GB)/0,08 (8 procent)  

För D14 v2 och HDI 4,0 är den rekommenderade storleken på SSD-cache = 19 GB/0,08 ~ = **237 GB**  
För D14 v2 och HDI 3,6 är den rekommenderade storleken på SSD-cache = 18 GB/0,08 ~ = **225 GB**

#### <a name="10-adjusting-map-join-memory"></a>**10. Justera kart kopplings minne**   
Konfiguration: **_Hive. Auto. convert. Join. noconditionaltask. size_* _

Kontrol lera att du har _hive. Auto. convert. Join. noconditionaltask * Enabled för att den här parametern ska börja gälla.
Den här konfigurationen fastställer tröskelvärdet för MapJoin-val av Hive-optimering som tar hänsyn till övermontering av minne från andra körningar för att få mer utrymme för hash-tabeller i minnet för att tillåta fler kart kopplings konverteringar. Med avseende på 3 GB per utförar kan den här storleken överföras till 3 GB, men vissa heap-minnen kan också användas för sorterings-buffertar, blanda buffertar osv. av andra åtgärder.   
För D14 v2, med 3 GB minne per utförar, rekommenderar vi att du anger det här värdet till **2048 MB**.  

(Obs! det här värdet kan kräva justeringar som är lämpliga för din arbets belastning. Om värdet anges för lågt får inte funktionen konvertera in. Om du ställer in den för hög kan det medföra att minnes undantagen eller GC pausas, vilket kan leda till försämrade prestanda.)  

#### <a name="11-number-of-llap-daemons"></a>**11. antal LLAP-daemonar**
Ambari för miljövariabler: **_num_llap_nodes num_llap_nodes_for_llap_daemons_* _  

_ *num_llap_nodes**-anger antalet noder som används av Hive LLAP-tjänsten, inklusive noder som kör LLAP daemon, LLAP Service Master och Tez program Master (Tez).  

![' Antal noder för LLAP-tjänsten '](./media/hive-llap-sizing-guide/LLAP_sizing_guide_num_llap_nodes.png "Antal noder för LLAP-tjänsten")  

**num_llap_nodes_for_llap_daemons** -angivet antal noder som endast används för LLAP-daemonar. Storlekarna på LLAP daemon-behållare anges till max-noden, så det leder till en LLAP daemon på varje nod.

![' Antal noder för LLAP daemon '](./media/hive-llap-sizing-guide/LLAP_sizing_guide_num_llap_nodes_for_llap_daemons.png "Antal noder för LLAP-daemon")

Vi rekommenderar att du behåller båda värdena på samma sätt som antalet arbetsnoder i det interaktiva fråga klustret.

### <a name="considerations-for-workload-management"></a>**Överväganden för hantering av arbets belastning**  
Om du vill aktivera arbets belastnings hantering för LLAP kontrollerar du att du har tillräckligt med kapacitet för att arbets belastnings hanteringen ska fungera som förväntat. Arbets belastnings hanteringen kräver konfiguration av en anpassad garn-kö, som är utöver `llap` kö. Se till att dela upp den totala resurs kapaciteten för klustret mellan LLAP kön och arbets belastnings hanterings kön i enlighet med dina arbets belastnings krav.
Arbets belastnings hantering skapar Tez program hanterare (Tez AMs) när en resurs plan aktive ras.
Observera följande:

* Tez AMs som har skapats genom att aktivera en resurs plan förbrukar resurser från kön för hantering av arbets belastning som anges i `hive.server2.tez.interactive.queue` .  
* Antalet Tez-AMs skulle vara beroende av värdet för `QUERY_PARALLELISM` angivet i resurs planen.  
* När arbets belastnings hanteringen är aktiv kommer Tez AMs i LLAP-kön inte att användas. Endast Tez-AMs från arbets belastnings hanterings kön används för frågor om samordning. Tez AMs i `llap` kön används när arbets belastnings hanteringen är inaktive rad.
 
Till exempel: total kluster kapacitet = 100 GB minne, uppdelat mellan LLAP, hantering av arbets belastning och standard köer enligt följande:
 - LLAP-kös kapacitet = 70 GB
 - Kapacitet för arbets belastnings hanterings köer = 20 GB
 - Standard kapacitet för kö = 10 GB

Med 20 GB i kapaciteten för hantering av arbets belastnings hantering kan en resurs plan ange `QUERY_PARALLELISM` värde som fem, vilket innebär att arbets belastnings hanteringen kan starta fem Tez-AMS med 4 GB behållarens storlek. Om `QUERY_PARALLELISM` är högre än kapaciteten kan du se att vissa Tez-AMS har stannat i `ACCEPTED` status. Hiveserver2 interaktiva kan inte skicka frågefiler till Tez-AMs som inte är i ett `RUNNING` tillstånd.


#### <a name="next-steps"></a>**Nästa steg**
Om du inte kan lösa problemet med dessa värden kan du gå till något av följande...

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen genom att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).  

* ##### <a name="other-references"></a>**Andra referenser:**
  * [Konfigurera andra egenskaper för LLAP](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_setup_llap.html)  
  * [Konfigurera heap-serverns Heap-storlek](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_hiveserver_heap_sizing.html)  
  * [Mappa kopplings minnes storlek för LLAP](https://community.cloudera.com/t5/Community-Articles/Map-Join-Memory-Sizing-For-LLAP/ta-p/247462)  
  * [Tez körnings motor egenskaper](https://docs.cloudera.com/HDPDocuments/HDP3/HDP-3.1.5/performance-tuning/content/hive_tez_engine_properties.html)  
  * [LLAP djup i Hive](https://community.cloudera.com/t5/Community-Articles/Hive-LLAP-deep-dive/ta-p/248893)