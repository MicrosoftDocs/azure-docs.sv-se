---
title: 'Snabb start: ta emot händelser med Apache Storm-Azure Event Hubs'
description: 'Snabb start: den här artikeln innehåller information om hur du tar emot händelser från Azure Event Hubs att använda Apache Storm.'
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 099a3ff1ad57616de0d7929d787b82e9d55c9e00
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87087585"
---
# <a name="quickstart-receive-events-from-event-hubs-using-apache-storm"></a>Snabb start: ta emot händelser från Event Hubs med Apache Storm

[Apache Storm](https://storm.incubator.apache.org) är ett distribuerat real tids beräknings system som fören klar tillförlitlig bearbetning av obundna data strömmar. Det här avsnittet visar hur du använder en Azure Event Hubs Storm-kanalen för att ta emot händelser från Event Hubs. Med hjälp av Apache Storm kan du dela upp händelser över flera processer som finns på olika noder. Event Hubs-integrering med storm fören klar händelse förbrukningen genom att överlappa den förhands granskningen med Storms Zookeeper-installation, hantera beständiga kontroll punkter och parallella inhämtningar från Event Hubs.

Mer information om Event Hubs ta emot mönster finns i [Event Hubs översikt][Event Hubs overview].

## <a name="prerequisites"></a>Förutsättningar
Innan du börjar med snabb starten **skapar du ett Event Hubs-namnområde och en Event Hub**. Använd [Azure Portal](https://portal.azure.com) för att skapa ett namn område av typen Event Hubs och hämta de autentiseringsuppgifter som programmet behöver för att kommunicera med händelsehubben. Om du behöver skapa ett namnområde och en händelsehubb följer du anvisningarna i [den här artikeln](event-hubs-create.md). 

## <a name="create-project-and-add-code"></a>Skapa projekt och Lägg till kod

I den här självstudien används en [HDInsight Storm][HDInsight Storm] -installation som medföljer Event Hubs kanalen redan tillgängligt.

1. Följ proceduren för [HDInsight Storm – kom igång](../hdinsight/storm/apache-storm-overview.md) för att skapa ett nytt HDInsight-kluster och Anslut till det via fjärr skrivbord.
2. Kopiera `%STORM_HOME%\examples\eventhubspout\eventhubs-storm-spout-0.9-jar-with-dependencies.jar` filen till din lokala utvecklings miljö. Detta innehåller händelserna-Storm-kanalen.
3. Använd följande kommando för att installera paketet i den lokala maven-butiken. På så sätt kan du lägga till den som en referens i storm-projektet i ett senare steg.

    ```shell
    mvn install:install-file -Dfile=target\eventhubs-storm-spout-0.9-jar-with-dependencies.jar -DgroupId=com.microsoft.eventhubs -DartifactId=eventhubs-storm-spout -Dversion=0.9 -Dpackaging=jar
    ```
4. Skapa ett nytt Maven-projekt i Sol förmörkelse (klicka på **Arkiv**, sedan på **nytt** och sedan på **projekt**).
   
    ![Fil-> projekt med ny >][12]
5. Välj **Använd standard plats för arbets yta** och klicka sedan på **Nästa**
6. Välj **maven-archetype-snabb start** archetype och klicka sedan på **Nästa**
7. Infoga ett  **ArtifactId** och klicka sedan på **Slutför**
8. I **pom.xml** lägger du till följande beroenden i `<dependency>` noden.

    ```xml  
    <dependency>
        <groupId>org.apache.storm</groupId>
        <artifactId>storm-core</artifactId>
        <version>0.9.2-incubating</version>
        <scope>provided</scope>
    </dependency>
    <dependency>
        <groupId>com.microsoft.eventhubs</groupId>
        <artifactId>eventhubs-storm-spout</artifactId>
        <version>0.9</version>
    </dependency>
    <dependency>
        <groupId>com.netflix.curator</groupId>
        <artifactId>curator-framework</artifactId>
        <version>1.3.3</version>
        <exclusions>
            <exclusion>
                <groupId>log4j</groupId>
                <artifactId>log4j</artifactId>
            </exclusion>
            <exclusion>
                <groupId>org.slf4j</groupId>
                <artifactId>slf4j-log4j12</artifactId>
            </exclusion>
        </exclusions>
        <scope>provided</scope>
    </dependency>
    ```

9. I mappen **src** skapar du en fil med namnet **config. Properties** och kopierar följande innehåll och ersätter `receive rule key` `event hub name` värdena och.

    ```java
    eventhubspout.username = ReceiveRule
    eventhubspout.password = {receive rule key}
    eventhubspout.namespace = ioteventhub-ns
    eventhubspout.entitypath = {event hub name}
    eventhubspout.partitions.count = 16
       
    # if not provided, will use storm's zookeeper settings
    # zookeeper.connectionstring=localhost:2181
       
    eventhubspout.checkpoint.interval = 10
    eventhub.receiver.credits = 10
    ```
    Värdet för **eventhub. Receive. krediter** avgör hur många händelser som grupperas innan de släpps i storm-pipeline. För enkelhetens skull anger det här värdet till 10. I produktion bör den normalt anges till högre värden. till exempel 1024.
10. Skapa en ny klass med namnet **LoggerBolt** med följande kod:
    
    ```java
    import java.util.Map;
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import backtype.storm.task.OutputCollector;
    import backtype.storm.task.TopologyContext;
    import backtype.storm.topology.OutputFieldsDeclarer;
    import backtype.storm.topology.base.BaseRichBolt;
    import backtype.storm.tuple.Tuple;
    
    public class LoggerBolt extends BaseRichBolt {
        private OutputCollector collector;
        private static final Logger logger = LoggerFactory
                  .getLogger(LoggerBolt.class);
    
        @Override
        public void execute(Tuple tuple) {
            String value = tuple.getString(0);
            logger.info("Tuple value: " + value);
   
            collector.ack(tuple);
        }
   
        @Override
        public void prepare(Map map, TopologyContext context, OutputCollector collector) {
            this.collector = collector;
            this.count = 0;
        }
        
        @Override
        public void declareOutputFields(OutputFieldsDeclarer declarer) {
            // no output fields
        }
    
    }
    ```
    
    Den här Storm-bulten loggar innehållet i de mottagna händelserna. Detta kan enkelt utökas för att lagra tupler i en lagrings tjänst. [HDInsight Storm med Event Hub-exemplet] använder samma metod för att lagra data i Azure Storage och Power BI.
11. Skapa en klass med namnet **LogTopology** med följande kod:
    
    ```java
    import java.io.FileReader;
    import java.util.Properties;
    import backtype.storm.Config;
    import backtype.storm.LocalCluster;
    import backtype.storm.StormSubmitter;
    import backtype.storm.generated.StormTopology;
    import backtype.storm.topology.TopologyBuilder;
    import com.microsoft.eventhubs.samples.EventCount;
    import com.microsoft.eventhubs.spout.EventHubSpout;
    import com.microsoft.eventhubs.spout.EventHubSpoutConfig;
        
    public class LogTopology {
        protected EventHubSpoutConfig spoutConfig;
        protected int numWorkers;
        
        protected void readEHConfig(String[] args) throws Exception {
            Properties properties = new Properties();
            if (args.length > 1) {
                properties.load(new FileReader(args[1]));
            } else {
                properties.load(EventCount.class.getClassLoader()
                        .getResourceAsStream("Config.properties"));
            }
        
            String username = properties.getProperty("eventhubspout.username");
            String password = properties.getProperty("eventhubspout.password");
            String namespaceName = properties
                    .getProperty("eventhubspout.namespace");
            String entityPath = properties.getProperty("eventhubspout.entitypath");
            String zkEndpointAddress = properties
                    .getProperty("zookeeper.connectionstring"); // opt
            int partitionCount = Integer.parseInt(properties
                    .getProperty("eventhubspout.partitions.count"));
            int checkpointIntervalInSeconds = Integer.parseInt(properties
                    .getProperty("eventhubspout.checkpoint.interval"));
            int receiverCredits = Integer.parseInt(properties
                    .getProperty("eventhub.receiver.credits")); // prefetch count
                                                                // (opt)
            System.out.println("Eventhub spout config: ");
            System.out.println("  partition count: " + partitionCount);
            System.out.println("  checkpoint interval: "
                    + checkpointIntervalInSeconds);
            System.out.println("  receiver credits: " + receiverCredits);
     
            spoutConfig = new EventHubSpoutConfig(username, password,
                    namespaceName, entityPath, partitionCount, zkEndpointAddress,
                    checkpointIntervalInSeconds, receiverCredits);
        
            // set the number of workers to be the same as partition number.
            // the idea is to have a spout and a logger bolt co-exist in one
            // worker to avoid shuffling messages across workers in storm cluster.
            numWorkers = spoutConfig.getPartitionCount();
        
            if (args.length > 0) {
                // set topology name so that sample Trident topology can use it as
                // stream name.
                spoutConfig.setTopologyName(args[0]);
            }
        }
        
        protected StormTopology buildTopology() {
            TopologyBuilder topologyBuilder = new TopologyBuilder();
       
            EventHubSpout eventHubSpout = new EventHubSpout(spoutConfig);
            topologyBuilder.setSpout("EventHubsSpout", eventHubSpout,
                    spoutConfig.getPartitionCount()).setNumTasks(
                    spoutConfig.getPartitionCount());
            topologyBuilder
                    .setBolt("LoggerBolt", new LoggerBolt(),
                            spoutConfig.getPartitionCount())
                    .localOrShuffleGrouping("EventHubsSpout")
                    .setNumTasks(spoutConfig.getPartitionCount());
            return topologyBuilder.createTopology();
        }
        
        protected void runScenario(String[] args) throws Exception {
            boolean runLocal = true;
            readEHConfig(args);
            StormTopology topology = buildTopology();
            Config config = new Config();
            config.setDebug(false);
        
            if (runLocal) {
                config.setMaxTaskParallelism(2);
                LocalCluster localCluster = new LocalCluster();
                localCluster.submitTopology("test", config, topology);
                Thread.sleep(5000000);
                localCluster.shutdown();
            } else {
                config.setNumWorkers(numWorkers);
                StormSubmitter.submitTopology(args[0], config, topology);
            }
        }
        
        public static void main(String[] args) throws Exception {
            LogTopology topology = new LogTopology();
            topology.runScenario(args);
        }
    }
    ```

    Den här klassen skapar en ny Event Hubs kanalen, med hjälp av egenskaperna i konfigurations filen för att instansiera den. Det är viktigt att Observera att det här exemplet skapar så många kanaler uppgifter som antalet partitioner i händelsehubben, för att kunna använda den maximala parallellitet som tillåts av den händelsehubben.

## <a name="next-steps"></a>Nästa steg
Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Översikt över Event Hubs][Event Hubs overview]
* [Skapa en händelsehubben](event-hubs-create.md)
* [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)

<!-- Links -->
[Event Hubs overview]: ./event-hubs-about.md
[HDInsight Storm]: ../hdinsight/storm/apache-storm-overview.md
[HDInsight Storm med Event Hub-exempel]: https://github.com/Azure-Samples/hdinsight-java-storm-eventhub

<!-- Images -->

[12]: ./media/event-hubs-get-started-receive-storm/create-storm1.png
