---
title: OutOfMemoryError-undantag för Apache Spark i Azure HDInsight
description: Olika OutOfMemoryError-undantag för Apache Spark kluster i Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.openlocfilehash: a15d79f2ae9c3d20a73ec557c57a5c189b18111b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946342"
---
# <a name="outofmemoryerror-exceptions-for-apache-spark-in-azure-hdinsight"></a>OutOfMemoryError-undantag för Apache Spark i Azure HDInsight

I den här artikeln beskrivs fel söknings steg och möjliga lösningar på problem när du använder Apache Spark-komponenter i Azure HDInsight-kluster.

## <a name="scenario-outofmemoryerror-exception-for-apache-spark"></a>Scenario: OutOfMemoryError-undantag för Apache Spark

### <a name="issue"></a>Problem

Ditt Apache Spark-program misslyckades med ett OutOfMemoryError ohanterat undantag. Du kan få ett fel meddelande som liknar:

```error
ERROR Executor: Exception in task 7.0 in stage 6.0 (TID 439)

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.grow(Unknown Source)
    at java.io.ByteArrayOutputStream.ensureCapacity(Unknown Source)
    at java.io.ByteArrayOutputStream.write(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.drain(Unknown Source)
    at java.io.ObjectOutputStream$BlockDataOutputStream.setBlockDataMode(Unknown Source)
    at java.io.ObjectOutputStream.writeObject0(Unknown Source)
    at java.io.ObjectOutputStream.writeObject(Unknown Source)
    at org.apache.spark.serializer.JavaSerializationStream.writeObject(JavaSerializer.scala:44)
    at org.apache.spark.serializer.JavaSerializerInstance.serialize(JavaSerializer.scala:101)
    at org.apache.spark.executor.Executor$TaskRunner.run(Executor.scala:239)
    at java.util.concurrent.ThreadPoolExecutor.runWorker(Unknown Source)
    at java.util.concurrent.ThreadPoolExecutor$Worker.run(Unknown Source)
    at java.lang.Thread.run(Unknown Source)
```

```error
ERROR SparkUncaughtExceptionHandler: Uncaught exception in thread Thread[Executor task launch worker-0,5,main]

java.lang.OutOfMemoryError
    at java.io.ByteArrayOutputStream.hugeCapacity(Unknown Source)
    ...
```

### <a name="cause"></a>Orsak

Den mest sannolika orsaken till det här undantaget är att inte tillräckligt med heap-minne allokeras till Java Virtual Machines (JVMs). Dessa JVMs startas som körnings program eller driv rutiner som en del av Apache Spark-programmet.

### <a name="resolution"></a>Lösning

1. Avgör maximal storlek för de data som Spark-programmet ska hantera. Gör en uppskattning av storleken baserat på den maximala storleken på indata, och de mellanliggande data som skapas genom att transformera indata och utdata som genererats ytterligare, omvandlas till mellanliggande data. Om den inledande uppskattningen inte räcker kan du öka storleken något och iterera fram till under sidan minnes fel.

1. Se till att HDInsight-klustret som ska användas har tillräckligt med resurser när det gäller minne och tillräckligt med kärnor för Spark-programmet. Detta kan bestämmas genom att visa avsnittet kluster mått i garn gränssnittet för klustret för värden för **använt minne** jämfört med **minne totalt** och **virtuella kärnor som används** jämfört med **virtuella kärnor totalt**.

    ![kärn minnes visning för garn](./media/apache-spark-ts-outofmemory/yarn-core-memory-view.png)

1. Ställ in följande Spark-konfigurationer på lämpliga värden. Balansera program kraven med de tillgängliga resurserna i klustret. Dessa värden får inte överstiga 90% av tillgängligt minne och kärnor som visas av garn, och det bör också uppfylla det lägsta minnes kravet för Spark-programmet:

    ```
    spark.executor.instances (Example: 8 for 8 executor count)
    spark.executor.memory (Example: 4g for 4 GB)
    spark.yarn.executor.memoryOverhead (Example: 384m for 384 MB)
    spark.executor.cores (Example: 2 for 2 cores per executor)
    spark.driver.memory (Example: 8g for 8GB)
    spark.driver.cores (Example: 4 for 4 cores)
    spark.yarn.driver.memoryOverhead (Example: 384m for 384MB)
    ```

    Totalt minne som används av alla körningar =

    ```
    spark.executor.instances * (spark.executor.memory + spark.yarn.executor.memoryOverhead) 
    ```

    Totalt minne som används av driv rutin =

    ```
    spark.driver.memory + spark.yarn.driver.memoryOverhead
    ```

---

## <a name="scenario-java-heap-space-error-when-trying-to-open-apache-spark-history-server"></a>Scenario: fel i Java heap vid försök att öppna Apache Spark historik Server

### <a name="issue"></a>Problem

Du får följande fel meddelande när du öppnar händelser i Spark historik Server:

```
scala.MatchError: java.lang.OutOfMemoryError: Java heap space (of class java.lang.OutOfMemoryError)
```

### <a name="cause"></a>Orsak

Det här problemet orsakas ofta av brist på resurser när du öppnar stora Spark-Event-filer. Storleken på Spark-heapen är inställd på 1 GB som standard, men stora Spark-händelseloggar kan kräva mer än så.

Om du vill kontrol lera storleken på de filer som du försöker läsa in kan du utföra följande kommandon:

```bash
hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0274_1/
**576.5 M**  wasb:///hdp/spark2-events/application_1503957839788_0274_1

hadoop fs -du -s -h wasb:///hdp/spark2-events/application_1503957839788_0264_1/
**2.1 G**  wasb:///hdp/spark2-events/application_1503957839788_0264_1
```

### <a name="resolution"></a>Lösning

Du kan öka Spark historik serverns minne genom att redigera `SPARK_DAEMON_MEMORY` egenskapen i Spark-konfigurationen och starta om alla tjänster.

Det kan du göra i Ambari webb läsar gränssnitt genom att välja avsnittet Spark2/config/Advanced Spark2-miljö.

![Avsnittet avancerad spark2-miljö](./media/apache-spark-ts-outofmemory-heap-space/apache-spark-image01.png)

Lägg till följande egenskap för att ändra server minnet för Spark-historik från 1G till 4G: `SPARK_DAEMON_MEMORY=4g` .

![Spark-egenskap](./media/apache-spark-ts-outofmemory-heap-space/apache-spark-image02.png)

Se till att starta om alla berörda tjänster från Ambari.

---

## <a name="scenario-livy-server-fails-to-start-on-apache-spark-cluster"></a>Scenario: livy-servern kan inte startas på Apache Spark kluster

### <a name="issue"></a>Problem

Det går inte att starta livy-servern på en Apache Spark [(Spark 2,1 på Linux (HDI 3,6)]. Försök att starta om resultat i följande felstack från Livy-loggarna:

```log
17/07/27 17:52:50 INFO CuratorFrameworkImpl: Starting
17/07/27 17:52:50 INFO ZooKeeper: Client environment:zookeeper.version=3.4.6-29--1, built on 05/15/2017 17:55 GMT
17/07/27 17:52:50 INFO ZooKeeper: Client environment:host.name=10.0.0.66
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.version=1.8.0_131
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.vendor=Oracle Corporation
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.home=/usr/lib/jvm/java-8-openjdk-amd64/jre
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.class.path= <DELETED>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.library.path= <DELETED>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.io.tmpdir=/tmp
17/07/27 17:52:50 INFO ZooKeeper: Client environment:java.compiler=<NA>
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.name=Linux
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.arch=amd64
17/07/27 17:52:50 INFO ZooKeeper: Client environment:os.version=4.4.0-81-generic
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.name=livy
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.home=/home/livy
17/07/27 17:52:50 INFO ZooKeeper: Client environment:user.dir=/home/livy
17/07/27 17:52:50 INFO ZooKeeper: Initiating client connection, connectString=zk2-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181,zk3-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181,zk6-kcspark.cxtzifsbseee1genzixf44zzga.gx.internal.cloudapp.net:2181 sessionTimeout=60000 watcher=org.apache.curator.ConnectionState@25fb8912
17/07/27 17:52:50 INFO StateStore$: Using ZooKeeperStateStore for recovery.
17/07/27 17:52:50 INFO ClientCnxn: Opening socket connection to server 10.0.0.61/10.0.0.61:2181. Will not attempt to authenticate using SASL (unknown error)
17/07/27 17:52:50 INFO ClientCnxn: Socket connection established to 10.0.0.61/10.0.0.61:2181, initiating session
17/07/27 17:52:50 INFO ClientCnxn: Session establishment complete on server 10.0.0.61/10.0.0.61:2181, sessionid = 0x25d666f311d00b3, negotiated timeout = 60000
17/07/27 17:52:50 INFO ConnectionStateManager: State change: CONNECTED
17/07/27 17:52:50 WARN NativeCodeLoader: Unable to load native-hadoop library for your platform... using builtin-java classes where applicable
17/07/27 17:52:50 INFO AHSProxy: Connecting to Application History server at headnodehost/10.0.0.67:10200
Exception in thread "main" java.lang.OutOfMemoryError: unable to create new native thread
  at java.lang.Thread.start0(Native Method)
  at java.lang.Thread.start(Thread.java:717)
  at com.cloudera.livy.Utils$.startDaemonThread(Utils.scala:98)
  at com.cloudera.livy.utils.SparkYarnApp.<init>(SparkYarnApp.scala:232)
  at com.cloudera.livy.utils.SparkApp$.create(SparkApp.scala:93)
  at com.cloudera.livy.server.batch.BatchSession$$anonfun$recover$2$$anonfun$apply$4.apply(BatchSession.scala:117)
  at com.cloudera.livy.server.batch.BatchSession$$anonfun$recover$2$$anonfun$apply$4.apply(BatchSession.scala:116)
  at com.cloudera.livy.server.batch.BatchSession.<init>(BatchSession.scala:137)
  at com.cloudera.livy.server.batch.BatchSession$.recover(BatchSession.scala:108)
  at com.cloudera.livy.sessions.BatchSessionManager$$anonfun$$init$$1.apply(SessionManager.scala:47)
  at com.cloudera.livy.sessions.BatchSessionManager$$anonfun$$init$$1.apply(SessionManager.scala:47)
  at scala.collection.TraversableLike$$anonfun$map$1.apply(TraversableLike.scala:244)
  at scala.collection.TraversableLike$$anonfun$map$1.apply(TraversableLike.scala:244)
  at scala.collection.mutable.ResizableArray$class.foreach(ResizableArray.scala:59)
  at scala.collection.mutable.ArrayBuffer.foreach(ArrayBuffer.scala:47)
  at scala.collection.TraversableLike$class.map(TraversableLike.scala:244)
  at scala.collection.AbstractTraversable.map(Traversable.scala:105)
  at com.cloudera.livy.sessions.SessionManager.com$cloudera$livy$sessions$SessionManager$$recover(SessionManager.scala:150)
  at com.cloudera.livy.sessions.SessionManager$$anonfun$1.apply(SessionManager.scala:82)
  at com.cloudera.livy.sessions.SessionManager$$anonfun$1.apply(SessionManager.scala:82)
  at scala.Option.getOrElse(Option.scala:120)
  at com.cloudera.livy.sessions.SessionManager.<init>(SessionManager.scala:82)
  at com.cloudera.livy.sessions.BatchSessionManager.<init>(SessionManager.scala:42)
  at com.cloudera.livy.server.LivyServer.start(LivyServer.scala:99)
  at com.cloudera.livy.server.LivyServer$.main(LivyServer.scala:302)
  at com.cloudera.livy.server.LivyServer.main(LivyServer.scala)
  
  ## using "vmstat" found  we had enough free memory
```

### <a name="cause"></a>Orsak

`java.lang.OutOfMemoryError: unable to create new native thread` visar att OS inte kan tilldela fler inbyggda trådar till JVMs. Bekräftat att detta undantag orsakas av överträdelsen av gränsen för antal trådar per process.

När livy-servern avslutas oväntad avslutas alla anslutningar till Spark-kluster också, vilket innebär att alla jobb och relaterade data går förlorade. I HDP 2,6-funktionen för att återställa sessionen lagrar livy sessionsinformation i Zookeeper för att återställas efter att livy-servern är tillbaka.

När ett stort antal jobb skickas via livy, som en del av hög tillgänglighet för livy-servern, lagrar dessa sessionstillstånd i ZK (i HDInsight-kluster) och återställer dessa sessioner när livy-tjänsten startas om. Vid omstart efter en oväntad avslutning skapar livy en tråd per session och detta ackumulerar ett visst antal att återställa sessioner som orsakar att för många trådar skapas.

### <a name="resolution"></a>Lösning

Ta bort alla poster med hjälp av stegen som beskrivs nedan.

1. Hämta IP-adressen för Zookeeper-noderna med hjälp av

    ```bash
    grep -R zk /etc/hadoop/conf  
    ```

1. Kommandot ovan listade alla Zookeepers för mitt kluster

    ```bash
    /etc/hadoop/conf/core-site.xml:      <value>zk1-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk2-      hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181,zk4-hwxspa.lnuwp5akw5ie1j2gi2amtuuimc.dx.internal.cloudapp.net:2181</value>
    ```

1. Hämta alla IP-adresser för Zookeeper-noderna med ping eller så kan du också ansluta till Zookeeper från huvudnoden med ZK-namn

    ```bash
    /usr/hdp/current/zookeeper-client/bin/zkCli.sh -server zk2-hwxspa:2181
    ```

1. När du är ansluten till Zookeeper kör du följande kommando för att visa en lista över alla sessioner som försöker starta om.

    1. De flesta fall kan vara en lista över 8000 sessioner ####

        ```bash
        ls /livy/v1/batch
        ```

    1. Följande kommando är för att ta bort alla sessioner som ska återställas. #####

        ```bash
        rmr /livy/v1/batch
        ```

1. Vänta tills kommandot ovan har slutförts och markören för att returnera prompten och starta sedan om livy-tjänsten från Ambari, som ska fungera.

> [!NOTE]
> `DELETE` livy-sessionen när den har körts. Livy batch-sessioner tas inte bort automatiskt så fort Spark-appen har slutförts, vilket är avsiktligt. En livy-session är en entitet som skapats av en POST-begäran mot livy rest-servern. Det `DELETE` krävs ett anrop för att ta bort entiteten. Eller så bör vi vänta tills den globala katalogen är igång.

---

## <a name="next-steps"></a>Nästa steg

Om du inte ser problemet eller inte kan lösa problemet kan du gå till någon av följande kanaler för mer support:

* [Översikt över Spark-minnes hantering](https://spark.apache.org/docs/latest/tuning.html#memory-management-overview).

* [Felsöka Spark-program i HDInsight-kluster](/archive/blogs/azuredatalake/spark-debugging-101).

* Få svar från Azure-experter via [Azure community support](https://azure.microsoft.com/support/community/).

* Anslut till [@AzureSupport](https://twitter.com/azuresupport) – det officiella Microsoft Azure kontot för att förbättra kund upplevelsen. Att ansluta Azure-communityn till rätt resurser: svar, support och experter.

* Om du behöver mer hjälp kan du skicka en support förfrågan från [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Välj **stöd** på Meny raden eller öppna **Hjälp + Support** Hub. Mer detaljerad information finns [i så här skapar du en support förfrågan för Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md). Åtkomst till prenumerations hantering och fakturerings support ingår i din Microsoft Azure prenumeration och teknisk support tillhandahålls via ett av support avtalen för [Azure](https://azure.microsoft.com/support/plans/).