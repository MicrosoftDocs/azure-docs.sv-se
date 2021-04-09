---
title: Komponenter med hög tillgänglighet i Azure HDInsight
description: Översikt över de olika komponenter med hög tillgänglighet som används av HDInsight-kluster.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: 93d2317c85f93ce8a22f2d434fbc081a88265a74
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863725"
---
# <a name="high-availability-services-supported-by-azure-hdinsight"></a>Tjänster med hög tillgänglighet som stöds av Azure HDInsight

För att ge dig optimala tillgänglighets nivåer för dina analys komponenter utvecklades HDInsight med en unik arkitektur för att säkerställa hög tillgänglighet för kritiska tjänster. Vissa komponenter i den här arkitekturen har utvecklats av Microsoft för att tillhandahålla automatisk redundans. Andra komponenter är standard Apache-komponenter som distribueras för att stödja vissa tjänster. I den här artikeln beskrivs arkitekturen hos HA-tjänst modellen i HDInsight, hur HDInsight stöder redundans för HA-tjänster och bästa praxis för att återställa från andra tjänst avbrott.

> [!NOTE]
> Den här artikeln innehåller referenser till termen *slav*, en term som Microsoft inte längre använder. När termen tas bort från program varan tar vi bort det från den här artikeln.

## <a name="high-availability-infrastructure"></a>Infrastruktur med hög tillgänglighet

HDInsight tillhandahåller anpassad infrastruktur för att säkerställa att fyra primära tjänster är hög tillgängliga med automatiska funktioner för redundans:

- Apache Ambari-Server
- Program tids linje server för Apache-garn
- Jobb historik Server för Hadoop-MapReduce
- Apache Livy

Den här infrastrukturen består av ett antal tjänster och program varu komponenter, varav vissa är utformade av Microsoft. Följande komponenter är unika för HDInsight-plattformen:

- Slav redundansväxling
- Styrenhet för huvud fel
- Slav tjänst för hög tillgänglighet
- Huvud tjänst för hög tillgänglighet

:::image type="content" source="./media/hdinsight-high-availability-components/high-availability-architecture.png" alt-text="infrastruktur med hög tillgänglighet" border="false":::

Det finns även andra tjänster med hög tillgänglighet som stöds av Apache-pålitliga komponenter med öppen källkod. Dessa komponenter finns också i HDInsight-kluster:

- HDFS (Hadoop File System) NameNode
- GARN resurs hanteraren
- HBase Master

I följande avsnitt får du mer information om hur dessa tjänster fungerar tillsammans.

## <a name="hdinsight-high-availability-services"></a>Tjänster för hög tillgänglighet för HDInsight

Microsoft tillhandahåller support för de fyra Apache-tjänsterna i följande tabell i HDInsight-kluster. För att skilja dem från tjänster med hög tillgänglighet som stöds av komponenter från Apache kallas de för *HDInsight ha-tjänster*.

| Tjänst | Klusternoder | Kluster typer | Syfte |
|---|---|---|---|
| Apache Ambari-Server| Aktiva huvudnoden | Alla | Övervakar och hanterar klustret.|
| Program tids linje server för Apache-garn | Aktiva huvudnoden | Alla utom Kafka | Upprätthåller felsöknings information om garn jobb som körs i klustret.|
| Jobb historik Server för Hadoop-MapReduce | Aktiva huvudnoden | Alla utom Kafka | Upprätthåller felsöknings data för MapReduce-jobb.|
| Apache Livy | Aktiva huvudnoden | Spark | Möjliggör enkel interaktion med ett Spark-kluster över ett REST-gränssnitt |

>[!Note]
> HDInsight Enterprise Security Package-kluster (ESP) tillhandahåller för närvarande endast Ambari-servern med hög tillgänglighet. Program tids linje server, jobb historik Server och livy körs bara på headnode0 och de redundansväxlas inte till headnode1 när Ambari failsover. Programmets tids linje databas finns också på headnode0 och inte på Ambari SQL Server.

### <a name="architecture"></a>Arkitektur

Varje HDInsight-kluster har två huvudnoderna i aktiva och vänte läge. HDInsight HA-tjänsterna körs endast på huvudnoderna. Dessa tjänster bör alltid köras på den aktiva huvudnoden och stoppas och placeras i underhålls läge på huvudnoden vänte läge.

För att upprätthålla rätt tillstånd för HA-tjänster och tillhandahålla snabb redundans, använder HDInsight Apache ZooKeeper, som är en koordinerings tjänst för distribuerade program, för att genomföra aktiv huvudnoden val. HDInsight tillhandahåller också några Java-processer i bakgrunden som koordinerar redundansväxlingen för HDInsight-tjänster. Dessa tjänster är följande: huvud fel kontroll panelen, den sekundära redundansväxlingen, *tjänsten master-ha* och den *slav-ha-tjänsten*.

### <a name="apache-zookeeper"></a>Apache ZooKeeper

Apache ZooKeeper är en högpresterande koordinerings tjänst för distribuerade program. I produktion körs ZooKeeper vanligt vis i replikerat läge där en replikerad grupp ZooKeeper-servrar bildar ett kvorum. Varje HDInsight-kluster har tre ZooKeeper-noder som gör att tre ZooKeeper-servrar kan bilda ett kvorum. HDInsight har två ZooKeeper-kvorum som körs parallellt med varandra. Ett kvorum bestämmer aktiva huvudnoden i ett kluster där HDInsight-tjänster ska köras. Ett annat kvorum används för att koordinera tjänster som tillhandahålls av Apache, enligt beskrivningen i senare avsnitt.

### <a name="slave-failover-controller"></a>Slav redundansväxling

Den sekundära redundansväxlingen körs på varje nod i ett HDInsight-kluster. Den här styrenheten ansvarar för att starta Ambari-agenten och *slav-ha-tjänsten* på varje nod. Den skickar regelbundet det första ZooKeeper-kvorumet om den aktiva huvudnoden. När den aktiva och vänte läges huvudnoderna ändras utför den sekundära redundansväxlingen följande:

1. Uppdaterar värd konfigurations filen.
1. Startar om Ambari-agenten.

*Slaven-ha-tjänsten* ansvarar för att stoppa HDInsight-tjänster (utom Ambari-Server) på huvudnoden för vänte läge.

### <a name="master-failover-controller"></a>Styrenhet för huvud fel

En styrenhet för huvud fel körning på båda huvudnoderna. Båda styrenheterna för huvud-redundansväxling kommunicerar med det första ZooKeeper-kvorumet för att nominera de huvudnoden som de kör som den aktiva huvudnoden.

Om t. ex. huvud fel kontrol Lanterna på huvudnoden 0 vinner valet, sker följande ändringar:

1. Huvudnoden 0 aktive ras.
1. Huvud fel kontroll enheten startar Ambari Server och *Master-ha-tjänsten* på huvudnoden 0.
1. Den andra huvud fel kontroll enheten stoppar Ambari-servern och *Master-ha-tjänsten* på huvudnoden 1.

Master-ha-tjänsten körs bara på den aktiva huvudnoden, den stoppar HDInsight HA-tjänsterna (utom Ambari-Server) på standby-huvudnoden och startar dem på Active huvudnoden.

### <a name="the-failover-process"></a>Redundansväxlingen

:::image type="content" source="./media/hdinsight-high-availability-components/failover-steps.png" alt-text="redundansväxling" border="false":::

En hälso övervakning körs på varje huvudnoden tillsammans med huvud fel kontrollen för huvud servern för att skicka pulsslags meddelanden till Zookeeper-kvorumet. Huvudnoden betraktas som en HA-tjänst i det här scenariot. Hälso övervakaren kontrollerar om varje tjänst för hög tillgänglighet är felfri och om den är redo att ansluta till det ledarskapde valet. Om ja, kommer den här huvudnoden att konkurrera i valet. Om inte, stängs valet tills det blir klart igen.

Om vänte läges huvudnoden någonsin uppnår sitt ledarskap och blir aktivt (till exempel vid fel med den tidigare aktiva noden), kommer dess huvud fel hanterare att starta alla HDInsight-tjänster på den. Huvud-failover-styrenheten kommer också att stoppa dessa tjänster på de andra huvudnoden.

För fel i HDInsight-tjänsten, t. ex. om en tjänst är avstängd eller inte felfri, bör huvud fel kontroll enheten automatiskt starta om eller stoppa tjänsterna enligt huvudnoden-status. Användare bör inte starta HDInsight-tjänster manuellt på båda Head-noderna. Tillåt i stället automatisk eller manuell redundans för att hjälpa tjänsten att återställa.

### <a name="inadvertent-manual-intervention"></a>Oavsiktlig manuell åtgärd

HDInsight HA-tjänster ska bara köras på den aktiva huvudnoden och startas om automatiskt när det behövs. Eftersom enskilda HA-tjänster inte har sin egen hälso övervakare kan inte redundans utlösas på nivån för den enskilda tjänsten. Redundansväxlingen säkerställs på nodnivå och inte på service nivå.

### <a name="some-known-issues"></a>Kända problem

- När du startar en HA-tjänst manuellt på vänte läges huvudnoden stoppas den inte förrän nästa redundansväxling sker. När HA-tjänster körs på båda huvudnoderna kan vissa möjliga problem vara: Ambari-gränssnittet är inte tillgängligt, Ambari genererar fel, garn, Spark och Oozie jobb kan fastna.

- När en HA-tjänst på den aktiva huvudnoden stoppas startar den inte förrän nästa redundansväxling inträffar eller så startar huvud enheten för huvud fel styrning/Master-ha-tjänsten om. När en eller flera HA-tjänster stoppas på den aktiva huvudnoden, särskilt när Ambari-servern stannar, är Ambari-gränssnittet inte tillgängligt, andra potentiella problem är fel i samband med garn, Spark och Oozie.

## <a name="apache-high-availability-services"></a>Tjänster med hög tillgänglighet för Apache

Apache ger hög tillgänglighet för HDFS NameNode, garn-ResourceManager och HBase Master, som också är tillgängliga i HDInsight-kluster. Till skillnad från HDInsight-HA-tjänster stöds de i ESP-kluster. Apache HA-tjänster kommunicerar med det andra ZooKeeper-kvorumet (beskrivs i avsnittet ovan) för att välja aktiva/vänte läge och utföra automatisk redundans. Följande avsnitt innehåller information om hur dessa tjänster fungerar.

### <a name="hadoop-distributed-file-system-hdfs-namenode"></a>Hadoop Distributed File System (HDFS) NameNode

HDInsight-kluster baserade på Apache Hadoop 2,0 eller högre ger NameNode hög tillgänglighet. Det finns två NameNodes som körs på huvudnoderna, som har kon figurer ATS för automatisk redundans. NameNodes använder *ZKFailoverController* för att kommunicera med Zookeeper för att välja för status aktiv/vänte läge. *ZKFailoverController* körs på båda huvudnoderna och fungerar på samma sätt som huvud styrenheten för redundansväxling ovan.

Det andra Zookeeper-kvorumet är oberoende av det första kvorumet, så den aktiva NameNode får inte köras på den aktiva huvudnoden. När den aktiva NameNode är i död läge eller ej felfri är vänte läge NameNode WINS och aktiv.

### <a name="yarn-resourcemanager"></a>GARN resurs hanteraren

HDInsight-kluster baserade på Apache Hadoop 2,4 eller högre, stöder garn-ResourceManager med hög tillgänglighet. Det finns två ResourceManagers, RM1 och RM2, som körs på huvudnoden 0 respektive huvudnoden 1. Som NameNode konfigureras garn ResourceManager också för automatisk redundans. En annan resurs hanteraren väljs automatiskt som aktiv när den aktuella aktiva ResourceManager stängs av eller slutar fungera.

GARN resurs hanteraren använder sin inbäddade *ActiveStandbyElector* som en feldetektor och en val av ledare. Till skillnad från HDFS NameNode behöver inte garn resurs hanteraren en separat ZKFC-daemon. Den aktiva ResourceManager skriver sina tillstånd till Apache Zookeeper.

Den höga tillgängligheten för garn resurs hanteraren är oberoende av NameNode och andra HDInsight-tjänster. Den aktiva ResourceManager kanske inte körs på den aktiva huvudnoden eller huvudnoden där den aktiva NameNode körs. Mer information om garn resurs hanteraren med hög tillgänglighet finns i [resurs hanteraren hög tillgänglighet](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/ResourceManagerHA.html).

### <a name="hbase-master"></a>HBase Master

HDInsight HBase-kluster har stöd för HBase Master hög tillgänglighet. Till skillnad från andra HA-tjänster, som körs på huvudnoderna, körs HBase-huvudmasters på de tre Zookeeper-noderna, där en av dem är den aktiva huvud servern och de andra två är vänte läge. Som NameNode kan HBase Master koordineras med Apache Zookeeper för val av ledare och gör automatisk redundans när den aktuella aktiva huvud servern har problem. Det finns bara en aktiv HBase Master när som helst.

## <a name="next-steps"></a>Nästa steg

- [Tillgänglighet och tillförlitlighet för Apache Hadoop kluster i HDInsight](./hdinsight-business-continuity.md)
- [Azure HDInsight Virtual Network-arkitektur](hdinsight-virtual-network-architecture.md)