---
title: Apache ZooKeeper servern kan inte bilda ett kvorum i Azure HDInsight
description: Apache ZooKeeper servern kan inte bilda ett kvorum i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 05/20/2020
ms.openlocfilehash: 3301d00dce6feb00edcb70ba9edfedcce2e31ec9
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98929213"
---
# <a name="apache-zookeeper-server-fails-to-form-a-quorum-in-azure-hdinsight"></a>Apache ZooKeeper servern kan inte bilda ett kvorum i Azure HDInsight

I den här artikeln beskrivs fel söknings steg och möjliga lösningar på problem som rör Zookeepers i Azure HDInsight-kluster.

## <a name="symptoms"></a>Symtom

* Båda resurs hanterarna går in i vänte läge
* Namenodes är både i vänte läge
* Spark-, Hive-och garn-jobb eller Hive-frågor Miss lyckas på grund av Zookeeper-anslutningsfel
* LLAP-daemonen kan inte startas på säkra Spark eller säkra interaktiva Hive-kluster

## <a name="sample-log"></a>Exempel logg

Du kan se ett fel meddelande som liknar följande i garn loggar (/var/log/Hadoop-yarn/yarn/yarn-yarn *. log på huvudnoderna):

```output
2020-05-05 03:17:18.3916720|Lost contact with Zookeeper. Transitioning to standby in 10000 ms if connection is not reestablished.
Message
2020-05-05 03:17:07.7924490|Received RMFatalEvent of type STATE_STORE_FENCED, caused by org.apache.zookeeper.KeeperException$NoAuthException: KeeperErrorCode = NoAuth
...
2020-05-05 03:17:08.3890350|State store operation failed 
2020-05-05 03:17:08.3890350|Transitioning to standby state
```

## <a name="related-issues"></a>Relaterade problem

* Tjänster med hög tillgänglighet, t. ex. garn, NameNode och livy, kan gå ned av många olika orsaker.
* Bekräfta från de loggar som den är relaterad till Zookeeper-anslutningar
* Kontrol lera att problemet inträffar upprepade gånger (Använd inte dessa lösningar för ett eller flera fall)
* Jobb kan tillfälligt stängas på grund av problem med Zookeeper-anslutningen

## <a name="common-causes-for-zookeeper-failure"></a>Vanliga orsaker till Zookeeper-fel

* Hög CPU-användning på Zookeeper-servrarna
  * Om du ser nära 100% varaktig CPU-användning på Zookeeper-servrarna i Ambari-ANVÄNDARGRÄNSSNITTET kan Zookeeper-sessionerna öppnas under den tiden och tids gränsen överskrids
* Zookeeper-klienter rapporterar frekventa tids gränser
  * I loggarna för Resource Manager, Namenode och andra, kommer du att se frekventa tids gränser för klient anslutning
  * Detta kan leda till förlust av kvorum, frekventa växlingar och andra problem

## <a name="check-for-zookeeper-status"></a>Kontrol lera status för Zookeeper

* Hitta Zookeeper-servrarna från/etc/hosts-filen eller från Ambari-ANVÄNDARGRÄNSSNITTET
* Kör följande kommando
  * `echo stat | nc <ZOOKEEPER_HOST_IP> 2181` (eller 2182)  
  * Port 2181 är Apache Zookeeper-instansen
  * Port 2182 används av HDInsight-Zookeeper (för att tillhandahålla hektar för tjänster som inte har något inbyggt)
  * Om kommandot inte visar några utdata innebär det att Zookeeper-servrarna inte körs
  * Om servrarna är igång inkluderar resultatet statics av klient anslutningar och annan statistik

```output
Zookeeper version: 3.4.6-8--1, built on 12/05/2019 12:55 GMT
Clients:
 /10.2.0.57:50988[1](queued=0,recved=715,sent=715)
 /10.2.0.57:46632[1](queued=0,recved=138340,sent=138347)
 /10.2.0.34:14688[1](queued=0,recved=264653,sent=353420)
 /10.2.0.52:49680[1](queued=0,recved=134812,sent=134814)
 /10.2.0.57:50614[1](queued=0,recved=19812,sent=19812)
 /10.2.0.56:35034[1](queued=0,recved=2586,sent=2586)
 /10.2.0.52:63982[1](queued=0,recved=72215,sent=72217)
 /10.2.0.57:53024[1](queued=0,recved=19805,sent=19805)
 /10.2.0.57:45126[1](queued=0,recved=19621,sent=19621)
 /10.2.0.56:41270[1](queued=0,recved=1348743,sent=1348788)
 /10.2.0.53:59097[1](queued=0,recved=72215,sent=72217)
 /10.2.0.56:41088[1](queued=0,recved=788,sent=802)
 /10.2.0.34:10246[1](queued=0,recved=19575,sent=19575)
 /10.2.0.56:40944[1](queued=0,recved=717,sent=717)
 /10.2.0.57:45466[1](queued=0,recved=19861,sent=19861)
 /10.2.0.57:59634[0](queued=0,recved=1,sent=0)
 /10.2.0.34:14704[1](queued=0,recved=264622,sent=353355)
 /10.2.0.57:42244[1](queued=0,recved=49245,sent=49248)

Latency min/avg/max: 0/3/14865
Received: 238606078
Sent: 239139381
Connections: 18
Outstanding: 0
Zxid: 0x1004f99be
Mode: follower
Node count: 133212
```

## <a name="cpu-load-peaks-up-every-hour"></a>CPU-belastningen går upp varje timme

* Logga in på Zookeeper-servern och kontrol lera/etc/crontab
* Om det finns några Tim jobb som körs just nu, slumpar du Start tiden över olika Zookeeper-servrar.
  
## <a name="purging-old-snapshots"></a>Rensa gamla ögonblicks bilder

* Zookeepers har kon figurer ATS för att rensa gamla ögonblicks bilder automatiskt
* De sista 30 ögonblicks bilderna bevaras som standard
* Antalet ögonblicks bilder som kvarhålls styrs av konfigurations nyckeln `autopurge.snapRetainCount` . Du hittar den här egenskapen i följande filer:
  * `/etc/zookeeper/conf/zoo.cfg` för Hadoop-Zookeeper
  * `/etc/hdinsight-zookeeper/conf/zoo.cfg` för HDInsight-Zookeeper
* Ange `autopurge.snapRetainCount` värdet 3 och starta om Zookeeper-servrarna
  * Hadoop Zookeeper-konfigurationen kan uppdateras och tjänsten kan startas om via Ambari
  * Stoppa och starta om HDInsight-Zookeeper manuellt
    * `sudo lsof -i :2182` ger dig det process-ID som ska stoppas
    * `sudo python /opt/startup_scripts/startup_hdinsight_zookeeper.py`
* Rensa inte ögonblicks bilder manuellt – borttagning av ögonblicks bilder manuellt kan leda till data förlust

## <a name="cancelledkeyexception-in-the-zookeeper-server-log-doesnt-require-snapshot-cleanup"></a>CancelledKeyException i Zookeeper-serverloggen kräver inte ögonblicks bild rensning

* Detta undantag visas på Zookeeper-servrarna (/var/log/Zookeeper/Zookeeper-Zookeeper-* eller/var/log/HDInsight-Zookeeper/Zookeeper * Files)
* Detta undantag innebär vanligt vis att klienten inte längre är aktiv och att servern inte kan skicka ett meddelande
* Detta undantag indikerar också att Zookeeper-klienten avslutar sessioner för tidigt
* Leta efter de andra problemen som beskrivs i det här dokumentet

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

- Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).
- Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Att ansluta Azure-communityn till rätt resurser: svar, support och experter.
- Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).