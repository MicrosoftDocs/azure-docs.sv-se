---
title: Felsöka Storm med Azure HDInsight
description: Få svar på vanliga frågor om att använda Apache Storm med Azure HDInsight.
keywords: Azure HDInsight, Storm, FAQ, fel söknings guide, vanliga problem
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 11/08/2019
ms.custom: seodec18
ms.openlocfilehash: c81084c77b355a5d60c72564c58a98e08da14312
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946316"
---
# <a name="troubleshoot-apache-storm-by-using-azure-hdinsight"></a>Felsöka Apache Storm med Azure HDInsight

Lär dig mer om de vanligaste problemen och deras resolutioner om hur du arbetar med [Apache Storm](https://storm.apache.org/) nytto laster i [Apache Ambari](https://ambari.apache.org/).

## <a name="how-do-i-access-the-storm-ui-on-a-cluster"></a>Hur gör jag för att åtkomst till storm-ANVÄNDARGRÄNSSNITTET i ett kluster?

Du har två alternativ för att komma åt Storm-ANVÄNDARGRÄNSSNITTET från en webbläsare:

### <a name="apache-ambari-ui"></a>Apache Ambari-användargränssnitt

1. Gå till Ambari-instrumentpanelen.
2. I listan över tjänster väljer du **Storm**.
3. I menyn **snabb länkar** väljer du **Storm UI**.

### <a name="direct-link"></a>Direkt länk

Du kan komma åt Storm-ANVÄNDARGRÄNSSNITTET på följande URL:

`https://<cluster DNS name>/stormui`

Exempel: `https://stormcluster.azurehdinsight.net/stormui`

## <a name="how-do-i-transfer-storm-event-hub-spout-checkpoint-information-from-one-topology-to-another"></a>Hur gör jag för att överföring av storm Event Hub-kanalen från en topologi till en annan?

När du utvecklar topologier som läses från Azure Event Hubs med hjälp av HDInsight Storm Event Hub kanalen. jar-filen måste du distribuera en topologi som har samma namn i ett nytt kluster. Du måste dock behålla kontroll punkts data som har allokerats för att [Apache ZooKeeper](https://zookeeper.apache.org/) i det gamla klustret.

### <a name="where-checkpoint-data-is-stored"></a>Var kontroll punkts data lagras

Kontroll punkts data för förskjutningar lagras av Event Hub-kanalen i ZooKeeper i två rot Sök vägar:

- Kanalen-kontrollpunkter lagras i `/eventhubspout` .

- Transaktions kontroll punkts data för kanalen lagras i `/transactional` .

### <a name="how-to-restore"></a>Så här återställer du

Om du vill hämta de skript och bibliotek som du använder för att exportera data från ZooKeeper och sedan importera data tillbaka till ZooKeeper med ett nytt namn, se [HDInsight Storm-exempel](https://github.com/hdinsight/hdinsight-storm-examples/tree/master/tools/zkdatatool-1.0).

Lib-mappen har. jar-filer som innehåller implementeringen för export/import-åtgärden. Mappen bash innehåller ett exempel skript som visar hur du exporterar data från ZooKeeper-servern på det gamla klustret och sedan importerar tillbaka den till ZooKeeper-servern på det nya klustret.

Kör [stormmeta.sh](https://github.com/hdinsight/hdinsight-storm-examples/blob/master/tools/zkdatatool-1.0/bash/stormmeta.sh) -skriptet från ZooKeeper-noderna för att exportera och sedan importera data. Uppdatera skriptet till rätt version av Hortonworks Data Platform (HDP). (Vi arbetar med att göra dessa skript generiska i HDInsight. Allmänna skript kan köras från alla noder i klustret utan ändringar av användaren.)

Export kommandot skriver metadata till en Apache Hadoop Distributed File System-sökväg (HDFS) (i Azure Blob Storage eller Azure Data Lake Storage) på en plats som du anger.

### <a name="examples"></a>Exempel

#### <a name="export-offset-metadata"></a>Exportera metadata för förskjutning

1. Använd SSH för att gå till ZooKeeper-klustret i det kluster som kontroll punktens förskjutning ska exporteras från.
2. Kör följande kommando (när du har uppdaterat HDP-versions strängen) för att exportera ZooKeeper förskjutnings data till `/stormmetadta/zkdata` HDFS-sökvägen:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter export /eventhubspout /stormmetadata/zkdata
    ```

#### <a name="import-offset-metadata"></a>Importera förskjutna metadata

1. Använd SSH för att gå till ZooKeeper-klustret i det kluster som kontroll punktens förskjutning ska importeras från.
2. Kör följande kommando (när du har uppdaterat HDP-versions strängen) för att importera ZooKeeper förskjutnings data från HDFS-sökvägen `/stormmetadata/zkdata` till ZooKeeper-servern i mål klustret:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter import /eventhubspout /home/sshadmin/zkdata
    ```

#### <a name="delete-offset-metadata-so-that-topologies-can-start-processing-data-from-the-beginning-or-from-a-timestamp-that-the-user-chooses"></a>Ta bort förskjutna metadata så att topologier kan börja bearbeta data från början eller från en tidsstämpel som användaren väljer

1. Använd SSH för att gå till ZooKeeper-klustret i det kluster som kontroll punktens förskjutning måste tas bort från.
2. Kör följande kommando (när du har uppdaterat HDP-versions strängen) för att ta bort alla ZooKeeper offset-data i det aktuella klustret:

    ```apache
    java -cp ./*:/etc/hadoop/conf/*:/usr/hdp/2.5.1.0-56/hadoop/*:/usr/hdp/2.5.1.0-56/hadoop/lib/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/*:/usr/hdp/2.5.1.0-56/hadoop-hdfs/lib/*:/etc/failover-controller/conf/*:/etc/hadoop/* com.microsoft.storm.zkdatatool.ZkdataImporter delete /eventhubspout
    ```

## <a name="how-do-i-locate-storm-binaries-on-a-cluster"></a>Hur gör jag för att hittar du Storm-binärfiler i ett kluster?

Storm-binärfiler för den aktuella HDP-stacken finns i `/usr/hdp/current/storm-client` . Platsen är samma både för huvudnoder och för arbetsnoder.

Det kan finnas flera binärfiler för vissa HDP-versioner i/usr/HDP (till exempel `/usr/hdp/2.5.0.1233/storm` ). `/usr/hdp/current/storm-client`Mappen är symlinked till den senaste versionen som körs i klustret.

Mer information finns i [ansluta till ett HDInsight-kluster med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) och [Apache Storm](https://storm.apache.org/).

## <a name="how-do-i-determine-the-deployment-topology-of-a-storm-cluster"></a>Hur gör jag för att du bestämma distributions sto pol Ogin för ett Storm-kluster?

Börja med att identifiera alla komponenter som är installerade med HDInsight storm. Ett Storm-kluster består av fyra Node-kategorier:

* Gateway-noder
* Huvudnoder
* ZooKeeper-noder
* Arbetsnoder

### <a name="gateway-nodes"></a>Gateway-noder

En gateway-nod är en gateway-och omvänd proxy-tjänst som möjliggör offentlig åtkomst till en aktiv hanterings tjänst för Ambari. Den hanterar också Ambari-ledar val.

### <a name="head-nodes"></a>Huvudnoder

Storm Head-noder kör följande tjänster:
* Nimbus
* Ambari-Server
* Ambari mått Server
* Ambari mått insamlare
 
### <a name="zookeeper-nodes"></a>ZooKeeper-noder

HDInsight innehåller ett ZooKeeper-kvorum med tre noder. Kvorumresursens storlek är fast och kan inte konfigureras om.

Storm-tjänster i klustret är konfigurerade för att automatiskt använda ZooKeeper-kvorumet.

### <a name="worker-nodes"></a>Arbetsnoder

Storm Worker-noder kör följande tjänster:
* Chef
* Worker Java Virtual Machines (JVMs) för topologier som körs
* Ambari-agent

## <a name="how-do-i-locate-storm-event-hub-spout-binaries-for-development"></a>Hur gör jag för att du hitta binärfiler för Storm Event Hub-kanalen för utveckling?

Mer information om hur du använder Storm Event Hub kanalen. jar-filer med din topologi finns i följande resurser.

### <a name="java-based-topology"></a>Java-baserad topologi

[Bearbeta händelser från Azure Event Hubs med Apache Storm på HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)

### <a name="c-based-topology-mono-on-hdinsight-34-linux-storm-clusters"></a>C#-baserad topologi (mono på HDInsight 3.4 + Linux Storm-kluster)

[Bearbeta händelser från Azure Event Hubs med Apache Storm på HDInsight (C#)](./apache-storm-develop-csharp-event-hub-topology.md)

### <a name="latest-apache-storm-event-hub-spout-binaries-for-hdinsight-35-linux-storm-clusters"></a>Senaste Apache Storm-binärfiler för Event Hub för HDInsight 3.5 + Linux Storm-kluster

Information om hur du använder den senaste Storm Event Hub-kanalen som fungerar med HDInsight 3.5 + Linux Storm-kluster finns i [Readme-filen mvn-lagrings platsen](https://github.com/hdinsight/mvn-repo/blob/master/README.md).

### <a name="source-code-examples"></a>Käll kod exempel

Se [exempel](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) på hur du läser och skriver från Azure Event Hub med en Apache Storm-topologi (skrivet i Java) i ett Azure HDInsight-kluster.

## <a name="how-do-i-locate-storm-log4j-2-configuration-files-on-clusters"></a>Hur gör jag för att hittar du Storm Log4J 2-konfigurationsfiler i kluster?

För att identifiera [Apache log4j 2](https://logging.apache.org/log4j/2.x/) -konfigurationsfiler för Storm-tjänster.

### <a name="on-head-nodes"></a>På Head-noder

Nimbus Log4J-konfigurationen läses från `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml` .

### <a name="on-worker-nodes"></a>På arbetsnoder

Konfigurationen av den överordnade Log4J läses från `/usr/hdp/\<HDP version>/storm/log4j2/cluster.xml` .

Konfigurations filen för Worker-Log4J läses från `/usr/hdp/\<HDP version>/storm/log4j2/worker.xml` .

Fler `/usr/hdp/2.6.0.2-76/storm/log4j2/cluster.xml`
`/usr/hdp/2.6.0.2-76/storm/log4j2/worker.xml`

---

## <a name="not-a-leader-exception"></a>Inget ledar undantag

När du skickar en topologi kan användaren få ett fel meddelande som liknar: `Topology submission exception, cause not a leader, the current leader is NimbusInfo` .

För att lösa problemet kan användaren behöva File a Ticket för att noderna ska starta om/starta om. Mer information finns i [https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html](https://community.hortonworks.com/content/supportkb/150287/error-ignoring-exception-while-trying-to-get-leade.html).

---

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

- Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

- Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Att ansluta Azure-communityn till rätt resurser: svar, support och experter.

- Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).