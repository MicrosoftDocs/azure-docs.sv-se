---
title: 'Självstudie: Apache Storm med Apache Kafka – Azure HDInsight'
description: Lär dig att skapa en direktuppspelnings-pipeline med Apache Storm och Apache Kafka i HDInsight. I den här självstudien använder du KafkaBolt- och KafkaSpout-komponenter till att strömma data från Kafka.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: tutorial
ms.date: 06/25/2019
ms.openlocfilehash: 2078ba177d68cfaa0a91b79611d92e5c68e4e245
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104868429"
---
# <a name="tutorial-use-apache-storm-with-apache-kafka-on-hdinsight"></a>Självstudie: Använda Apache Storm med Apache Kafka i HDInsight

Den här självstudien visar hur du använder en [Apache Storm](https://storm.apache.org/)-topologi till att läsa och skriva data med [Apache Kafka](https://kafka.apache.org/) i HDInsight. Självstudien visar även hur du bevarar data i den [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html)-kompatibla lagringen på Storm-klustret.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Storm och Kafka
> * Förstå koden
> * Skapa Kafka- och Storm-kluster
> * Skapa topologin
> * Konfigurera topologin
> * Skapa Kafka-ämnet
> * Starta topologierna
> * Stoppa topologierna
> * Rensa resurser

## <a name="prerequisites"></a>Förutsättningar

* Om du vet hur man skapar Kafka-avsnitt. Mer information finns i dokumentet [Snabbstart för Kafka på HDInsight](./kafka/apache-kafka-get-started.md).

* Känna till hur man skapar och distribuerar Storm-lösningar (topologier). Mer specifikt gäller det topologier som använder [Apache Storm Flux](https://storm.apache.org/releases/current/flux.html)-ramverket. Mer information finns i dokumentet [Skapa en Apache Storm-topologi i Java](./storm/apache-storm-develop-java-topology.md).

* [Java JDK 1.8](https://www.oracle.com/technetwork/pt/java/javase/downloads/jdk8-downloads-2133151.html) eller högre. HDInsight 3.5 eller högre kräver Java 8.

* [Maven 3.x](https://maven.apache.org/download.cgi)

* En SSH-klient (du behöver kommandona `ssh` och `scp`) – Information finns i [Använda SSH med HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Följande miljövariabler kan konfigureras när du installerar Java och JDK på utvecklingsdatorn. Du bör dock kontrollera att de finns och att de innehåller rätt värden för ditt system.

* `JAVA_HOME` – ska peka på den katalog där JDK har installerats.
* `PATH` – ska innehålla följande sökvägar:
  
    * `JAVA_HOME` (eller motsvarande sökväg).
    * `JAVA_HOME\bin` (eller motsvarande sökväg).
    * Den katalog där Maven är installerat.

> [!IMPORTANT]  
> Stegen i det här dokumentet kräver en Azure-resursgrupp som både innehåller en Storm i HDInsight och en Kafka på HDInsight-klustret. Båda dessa kluster finns i ett virtuellt Azure-nätverk, vilket innebär att Storm-klustret kan kommunicera direkt med Kafka-klustret.
> 
> Dokumentet innehåller länkar till en mall som kan skapa alla nödvändiga Azure-resurser. 
>
> Mer information om hur du använder HDInsight i ett virtuellt nätverk finns i [planen ett virtuellt nätverk för HDInsight](hdinsight-plan-virtual-network-deployment.md) -dokument.

## <a name="storm-and-kafka"></a>Storm och Kafka

Apache Storm tillhandahåller flera komponenter för att arbeta med Apache Kafka. I den här självstudien används följande komponenter:

* `org.apache.storm.kafka.KafkaSpout`: Den här komponenten läser data från Kafka. Komponenten är beroende av följande komponenter:

    * `org.apache.storm.kafka.SpoutConfig`: Innehåller konfiguration för kanalkomponenten.

    * `org.apache.storm.spout.SchemeAsMultiScheme` och `org.apache.storm.kafka.StringScheme`: Hur data från Kafka omvandlas till en Storm-tuppel.

* `org.apache.storm.kafka.bolt.KafkaBolt`: Den här komponenten skriver data till Kafka. Komponenten är beroende av följande komponenter:

    * `org.apache.storm.kafka.bolt.selector.DefaultTopicSelector`: Beskriver ämnet som den skrivs till.

    * `org.apache.kafka.common.serialization.StringSerializer`: Konfigurerar bulten för att serialisera data som ett strängvärde.

    * `org.apache.storm.kafka.bolt.mapper.FieldNameBasedTupleToKafkaMapper`: Mappar från tuppeldatastrukturen som används inuti Storm-topologin till fält som lagras i Kafka.

Dessa komponenter är tillgängliga i `org.apache.storm : storm-kafka`-paketet. Använd den paketversion som matchar Storm-versionen. För HDInsight 3.6 är Storm-versionen 1.1.0.
Du måste också ha `org.apache.kafka : kafka_2.10`-paketet som innehåller ytterligare Kafka-komponenter. Använd den paketversion som matchar Kafka-versionen. För HDInsight 3,6 är Kafka-versionen 1.1.1.

Följande XML är beroendedeklarationen i `pom.xml` för ett [Apache Maven](https://maven.apache.org/)-projekt:

```xml
<!-- Storm components for talking to Kafka -->
<dependency>
    <groupId>org.apache.storm</groupId>
    <artifactId>storm-kafka</artifactId>
    <version>1.1.0</version>
</dependency>
<!-- needs to be the same Kafka version as used on your cluster -->
<dependency>
    <groupId>org.apache.kafka</groupId>
    <artifactId>kafka_2.10</artifactId>
    <version>1.1.1</version>
    <!-- Exclude components that are loaded from the Storm cluster at runtime -->
    <exclusions>
        <exclusion>
            <groupId>org.apache.zookeeper</groupId>
            <artifactId>zookeeper</artifactId>
        </exclusion>
        <exclusion>
            <groupId>log4j</groupId>
            <artifactId>log4j</artifactId>
        </exclusion>
        <exclusion>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-log4j12</artifactId>
        </exclusion>
    </exclusions>
</dependency>
```

## <a name="understanding-the-code"></a>Förstå koden

Koden som används i det här dokumentet finns på [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka) .

Två topologier medföljer den här självstudien:

* Kafka-skrivare: Genererar slumpmässiga meningar och lagrar dem till Kafka.

* Kafka-läsare: Läser data från Kafka och lagrar den i den HDFS-kompatibla filen för Storm-klustret.

    > [!WARNING]  
    > Om du vill aktivera att Storm ska fungera med den HDFS-kompatibla lagring som används av HDInsight, krävs en skriptåtgärd. Skriptet installerar flera jar-filer till sökvägen `extlib` för Storm. Mallen i den här självstudien använder automatiskt skriptet när klustret skapas.
    >
    > Om du inte använder mallen i det här dokumentet för att skapa Storm-kluster, måste du tillämpa skriptåtgärden i klustret manuellt.
    >
    > Skript åtgärden finns i [https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh](https://hdiconfigactions.blob.core.windows.net/linuxstormextlibv01/stormextlib.sh) och tillämpas på noderna ansvariga och Nimbus i Storm-klustret. Mer information om hur du använder skriptåtgärder finns i dokumentet [Anpassa HDInsight med hjälp av skriptåtgärder](hdinsight-hadoop-customize-cluster-linux.md).

Topologierna definieras med hjälp av [Flux](https://storm.apache.org/releases/current/flux.html). Flux introducerades i Storm-0.10.x för att du ska kunna avgränsa topologikonfigurationen från koden. För topologier som använder Flux-ramverket definieras topologin i en YAML-fil. YAML-filen kan ingå i topologin. Den kan också vara en fristående fil som används när du skickar topologin. Flux stöder också variabelersättning vid körning, vilket används i det här exemplet.

Följande parametrar anges vid körning för dessa topologier:

* `${kafka.topic}`: Namnet på Kafka-ämnet som topologierna läser/skriver till.

* `${kafka.broker.hosts}`: De värdar som Kafka-meddelandeköerna körs på. Informationen om meddelandeköer används av KafkaBolt vid skrivning till Kafka.

* `${kafka.zookeeper.hosts}`: De värdar som Zookeeper körs på i Kafka-klustret.

* `${hdfs.url}`: Filsystemets URL för HDFSBolt-komponenten. Anger huruvida data skrivs till ett Azure Storage-konto eller Azure Data Lake Storage.

* `${hdfs.write.dir}`: Den katalog som data skrivs till.

Mer information om flödes topologier finns i [https://storm.apache.org/releases/current/flux.html](https://storm.apache.org/releases/current/flux.html) .

### <a name="kafka-writer"></a>Kafka-skrivare

I topologin Kafka-skrivare tar Kafka-bulten två strängvärden som parametrar. Dessa parametrar anger vilka tuppelfält bulten skickar till Kafka som __nyckel__- och __meddelande__ värden. Nyckeln används för att partitionera data i Kafka. Meddelandet är den data som lagras.

I det här exemplet genererar `com.microsoft.example.SentenceSpout`-komponenten en tuppel som innehåller två fält, `key` och `message`. Kafka-bulten extraherar fälten och skickar datan i dem till Kafka.

Fälten får inte använda namnen `key` och `message`. Dessa namn används i projektet för att göra det lättare att förstå mappningen.

Följande YAML är definitionen för komponenten Kafka-skrivare:

```yaml
# kafka-writer
---

# topology definition
# name to be used when submitting
name: "kafka-writer"

# Components - constructors, property setters, and builder arguments.
# Currently, components must be declared in the order they are referenced
components:
  # Topic selector for KafkaBolt
  - id: "topicSelector"
    className: "org.apache.storm.kafka.bolt.selector.DefaultTopicSelector"
    constructorArgs:
      - "${kafka.topic}"

  # Mapper for KafkaBolt
  - id: "kafkaMapper"
    className: "org.apache.storm.kafka.bolt.mapper.FieldNameBasedTupleToKafkaMapper"
    constructorArgs:
      - "key"
      - "message"

  # Producer properties for KafkaBolt
  - id: "producerProperties"
    className: "java.util.Properties"
    configMethods:
      - name: "put"
        args:
          - "bootstrap.servers"
          - "${kafka.broker.hosts}"
      - name: "put"
        args:
          - "acks"
          - "1"
      - name: "put"
        args:
          - "key.serializer"
          - "org.apache.kafka.common.serialization.StringSerializer"
      - name: "put"
        args:
          - "value.serializer"
          - "org.apache.kafka.common.serialization.StringSerializer"
 

# Topology configuration
config:
  topology.workers: 2

# Spout definitions
spouts:
  - id: "sentence-spout"
    className: "com.microsoft.example.SentenceSpout"
    parallelism: 8

# Bolt definitions
bolts:
  - id: "kafka-bolt"
    className: "org.apache.storm.kafka.bolt.KafkaBolt"
    parallelism: 8
    configMethods:
    - name: "withProducerProperties"
      args: [ref: "producerProperties"]
    - name: "withTopicSelector"
      args: [ref: "topicSelector"]
    - name: "withTupleToKafkaMapper"
      args: [ref: "kafkaMapper"]

# Stream definitions

streams:
  - name: "spout --> kafka" # Streams data from the sentence spout to the Kafka bolt
    from: "sentence-spout"
    to: "kafka-bolt"
    grouping:
      type: SHUFFLE
```

### <a name="kafka-reader"></a>Kafka-läsare

I Kafka-läsartopologin läser kanalkomponenten data från Kafka som strängvärden. Datan skriver sedan Storm-loggen med loggningskomponenten och till det HDFS-kompatibla filsystemet för Storm-klustret med HDFS-bulten.

```yaml
# kafka-reader
---

# topology definition
# name to be used when submitting
name: "kafka-reader"

# Components - constructors, property setters, and builder arguments.
# Currently, components must be declared in the order they are referenced
components:
  # Convert data from Kafka into string tuples in storm
  - id: "stringScheme"
    className: "org.apache.storm.kafka.StringScheme"
  - id: "stringMultiScheme"
    className: "org.apache.storm.spout.SchemeAsMultiScheme"
    constructorArgs:
      - ref: "stringScheme"

  - id: "zkHosts"
    className: "org.apache.storm.kafka.ZkHosts"
    constructorArgs:
      - "${kafka.zookeeper.hosts}"

  # Spout configuration
  - id: "spoutConfig"
    className: "org.apache.storm.kafka.SpoutConfig"
    constructorArgs:
      # brokerHosts
      - ref: "zkHosts"
      # topic
      - "${kafka.topic}"
      # zkRoot
      - ""
      # id
      - "readerid"
    properties:
      - name: "scheme"
        ref: "stringMultiScheme"

    # How often to sync files to HDFS; every 1000 tuples.
  - id: "syncPolicy"
    className: "org.apache.storm.hdfs.bolt.sync.CountSyncPolicy"
    constructorArgs:
      - 1

  # Rotate files when they hit 5 MB
  - id: "rotationPolicy"
    className: "org.apache.storm.hdfs.bolt.rotation.FileSizeRotationPolicy"
    constructorArgs:
      - 5
      - "KB"

  # File format; read the directory from filters at run time, and use a .txt extension when writing.
  - id: "fileNameFormat"
    className: "org.apache.storm.hdfs.bolt.format.DefaultFileNameFormat"
    configMethods:
      - name: "withPath"
        args: ["${hdfs.write.dir}"]
      - name: "withExtension"
        args: [".txt"]

  # Internal file format; fields delimited by `|`.
  - id: "recordFormat"
    className: "org.apache.storm.hdfs.bolt.format.DelimitedRecordFormat"
    configMethods:
      - name: "withFieldDelimiter"
        args: ["|"]

# Topology configuration
config:
  topology.workers: 2

# Spout definitions
spouts:
  - id: "kafka-spout"
    className: "org.apache.storm.kafka.KafkaSpout"
    constructorArgs:
      - ref: "spoutConfig"
    # Set to the number of partitions for the topic
    parallelism: 8

# Bolt definitions
bolts:
  - id: "logger-bolt"
    className: "com.microsoft.example.LoggerBolt"
    parallelism: 1
  
  - id: "hdfs-bolt"
    className: "org.apache.storm.hdfs.bolt.HdfsBolt"
    configMethods:
      - name: "withConfigKey"
        args: ["hdfs.config"]
      - name: "withFsUrl"
        args: ["${hdfs.url}"]
      - name: "withFileNameFormat"
        args: [ref: "fileNameFormat"]
      - name: "withRecordFormat"
        args: [ref: "recordFormat"]
      - name: "withRotationPolicy"
        args: [ref: "rotationPolicy"]
      - name: "withSyncPolicy"
        args: [ref: "syncPolicy"]
    parallelism: 1

# Stream definitions

streams:
  # Stream data to log
  - name: "kafka --> log" # name isn't used (placeholder for logging, UI, etc.)
    from: "kafka-spout"
    to: "logger-bolt"
    grouping:
      type: SHUFFLE
  
  # stream data to file
  - name: "kafka --> hdfs"
    from: "kafka-spout"
    to: "hdfs-bolt"
    grouping:
      type: SHUFFLE
```

### <a name="property-substitutions"></a>Ersätta egenskaper

Projektet innehåller en fil med namnet `dev.properties` som används för att skicka parametrar som används av topologierna. Den definierar följande egenskaper:

| Filen dev.properties | Beskrivning |
| --- | --- |
| `kafka.zookeeper.hosts` | [Apache ZooKeeper](https://zookeeper.apache.org/)-värdarna för Kafka-klustret. |
| `kafka.broker.hosts` | Värdar för Kafka-meddelandeköer (arbetarnoder). |
| `kafka.topic` | Kafka-ämnet som topologierna använder. |
| `hdfs.write.dir` | Den katalog som Kafka-läsarens topologi skriver till. |
| `hdfs.url` | Det filsystem som används av Storm-klustret. För Azure Storage-konton använder du värdet `wasb://`. För Azure Data Lake Storage Gen2 använder du värdet `abfs://`. För Azure Data Lake Storage Gen1 använder du värdet `adl://`. |

## <a name="create-the-clusters"></a>Skapa kluster

Apache Kafka på HDInsight ger inte tillgång till asynkrona meddelandeköer i Kafka via offentligt Internet. Allt som använder Kafka måste finnas i samma virtuella Azure-nätverk. I den här självstudien finns både Kafka- och Storm-klustren i samma virtuella Azure-nätverk. 

Följande diagram visar hur kommunikationen flödar mellan Storm och Kafka:

:::image type="content" source="./media/hdinsight-apache-storm-with-kafka/apache-storm-kafka-vnet.png" alt-text="Diagram över Storm- och Kafka-kluster i ett virtuellt Azure-nätverk":::

> [!NOTE]  
> Andra tjänster på klustret, till exempel SSH och [Apache Ambari](https://ambari.apache.org/), kan nås via Internet. Mer information om de offentliga portar som är tillgängliga med HDInsight finns i [Portar och URI:er som används av HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Om du vill skapa ett Azure Virtual Network och sedan skapa Kafka- och Storm-kluster i det, använder du följande steg:

1. Använd följande knapp för att logga in på Azure och öppna mallen i Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-storm-java-kafka%2Fmaster%2Fcreate-kafka-storm-clusters-in-vnet.json" target="_blank"><img src="./media/hdinsight-apache-storm-with-kafka/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Mallen Azure Resource Manager finns på **https://github.com/Azure-Samples/hdinsight-storm-java-kafka/blob/master/create-kafka-storm-clusters-in-vnet.json** . Den skapar följande resurser:

    * Azure-resursgrupp
    * Azure Virtual Network
    * Azure-lagringskonto
    * Kafka i HDInsight version 3.6 (tre arbetarnoder)
    * Storm i HDInsight version 3.6 (tre arbetarnoder)

   > [!WARNING]  
   > Klustret måste innehålla minst tre arbetsnoder för att garantera tillgängligheten för Kafka i HDInsight. Den här mallen skapar ett Kafka-kluster som innehåller tre arbetarnoder.

2. Använd följande vägledning för att fylla i posterna i avsnittet **Anpassad distribution**:

   1. Använd följande information för att fylla i posterna i avsnittet **Anpassad mall**:

      | Inställning | Värde |
      | --- | --- |
      | Prenumeration | Din Azure-prenumeration |
      | Resursgrupp | Resursgruppen som innehåller resurserna. |
      | Location | Azure-regionen som resurserna skapas i. |
      | Kafka-klusternamn | Namnet på Kafka-klustret. |
      | Namn på Storm-kluster | Namnet på Storm-klustret. |
      | Användarnamn för klusterinloggning | Ett administratörsanvändarnamn för klustren. |
      | Lösenord för klusterinloggning | Ett administratörslösenord för klustren. |
      | SSH-användarnamn | SSH-användare som ska skapas för klustren. |
      | SSH-lösenord | Lösenord för SSH-användaren. |
   
      :::image type="content" source="./media/hdinsight-apache-storm-with-kafka/storm-kafka-template.png" alt-text="Bild av mallparametrarna":::

3. Granska **villkoren** och välj sedan **Jag godkänner villkoren ovan**.

4. Markera slutligen **Fäst på instrumentpanelen** och välj sedan **Inköp**.

> [!NOTE]  
> Det kan ta upp till 20 minuter att skapa klustren.

## <a name="build-the-topology"></a>Skapa topologin

1. I utvecklings miljön laddar du ned projektet från [https://github.com/Azure-Samples/hdinsight-storm-java-kafka](https://github.com/Azure-Samples/hdinsight-storm-java-kafka) , öppnar en kommando rad och byter katalog till den plats där du laddade ned projektet.

2. Från katalogen **hdinsight-storm-java-kafka** använder du följande kommando för att kompilera projektet och skapa ett paket för distributionen:

   ```bash
   mvn clean package
   ```

    Paketet skapar en fil med namnet `KafkaTopology-1.0-SNAPSHOT.jar` i katalogen `target`.

3. Använd följande kommandon för att kopiera paketet till Storm i ditt HDInsight-kluster. Ersätt `sshuser` med SSH-användarnamnet för klustret. Ersätt `stormclustername` med namnet på __Storm__-klustret.

   ```bash
   scp ./target/KafkaTopology-1.0-SNAPSHOT.jar sshuser@stormclustername-ssh.azurehdinsight.net:KafkaTopology-1.0-SNAPSHOT.jar
   ```

    När du uppmanas till det anger du det lösenord som du använde när du skapade klustren.

## <a name="configure-the-topology"></a>Konfigurera topologin

1. Använd någon av följande metoder till att identifiera värdarna för Kafka-meddelandeköer för **Kafka** på HDInsight-klustret:

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER" `
        -Credential $creds `
        -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $brokerHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($brokerHosts -join ":9092,") + ":9092"
    ```

    > [!IMPORTANT]  
    > I följande Bash-exempel förutsätts att `$CLUSTERNAME` innehåller namnet på __Kafka__-klustret. Det förutsätts även att [jq](https://stedolan.github.io/jq/) version 1.5 eller senare har installerats. När du blir ombedd anger du lösenordet till klusterinloggningskontot.

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/KAFKA/components/KAFKA_BROKER" | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2
    ```

    Det värde som genereras liknar följande text:

    ```output
    wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092
     ```

    > [!IMPORTANT]  
    > Även om det kan finnas fler än två meddelandekövärdar för klustret, behöver du inte ange en fullständig lista med alla värdar till klienterna. En eller två är tillräckligt.

2. Använd någon av följande metoder för att identifiera Zookeeper-värdar för __Kafka__ på HDInsight-klustret:

    ```powershell
    $creds = Get-Credential -UserName "admin" -Message "Enter the HDInsight login"
    $clusterName = Read-Host -Prompt "Enter the Kafka cluster name"
    $resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" `
        -Credential $creds `
        -UseBasicParsing
    $respObj = ConvertFrom-Json $resp.Content
    $zookeeperHosts = $respObj.host_components.HostRoles.host_name[0..1]
    ($zookeeperHosts -join ":2181,") + ":2181"
    ```

    > [!IMPORTANT]  
    > I följande Bash-exempel förutsätts att `$CLUSTERNAME` innehåller namnet på __Kafka__-klustret. Det förutsätts även att [jq](https://stedolan.github.io/jq/) är installerad. När du blir ombedd anger du lösenordet till klusterinloggningskontot.

    ```bash
    curl -su admin -G "https://$CLUSTERNAME.azurehdinsight.net/api/v1/clusters/$CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2
    ```

    Det värde som genereras liknar följande text:

    ```output
    zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181
    ```

    > [!IMPORTANT]  
    > Även om det kan finnas fler än två Zookeeper-noder, behöver du inte ange en fullständig lista med alla värdar till klienterna. En eller två är tillräckligt.

    Du bör spara det här värdet eftersom det används senare.

3. Redigera `dev.properties`-filen i projektroten. Lägg till information om meddelandeköer och Zookeeper-värdar för __Kafka__-klustret på de matchande raderna i filen. Följande exempel konfigureras med hjälp av exempelvärden från föregående steg:

    ```bash
    kafka.zookeeper.hosts: zk0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181,zk2-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:2181
    kafka.broker.hosts: wn0-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092,wn1-kafka.53qqkiavjsoeloiq3y1naf4hzc.ex.internal.cloudapp.net:9092
    kafka.topic: stormtopic
    ```

    > [!IMPORTANT]  
    > Posten `hdfs.url` är konfigurerad för ett kluster som använder ett Azure Storage-konto. Om du vill använda den här topologin med ett Storm-kluster som använder Data Lake Storage ändrar du värdet från `wasb` till `adl`.

4. Spara `dev.properties`-filen och använd sedan följande kommando till att ladda upp den till **Storm**-klustret:

     ```bash
    scp dev.properties USERNAME@BASENAME-ssh.azurehdinsight.net:dev.properties
    ```

    Ersätt **USERNAME** med SSH-användarnamnet för klustret. Ersätt **BASENAME** med basnamnet som du använde när du skapade klustret.

## <a name="create-the-kafka-topic"></a>Skapa Kafka-ämnet

Kafka lagrar data i ett _ämne_. Du måste skapa ämnet innan du startar Storm-topologierna. Använd följande steg för att skapa topologin:

1. Anslut till __Kafka__-klustret via SSH med hjälp av följande kommando. Ersätt `sshuser` med SSH-användarnamnet som användes när du skapade klustret. Ersätt `kafkaclustername` med namnet på Kafka-klustret:

    ```bash
    ssh sshuser@kafkaclustername-ssh.azurehdinsight.net
    ```

    När du uppmanas till det anger du det lösenord som du använde när du skapade klustren.
   
    Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

2. Skapa Kafka-ämnet med följande kommando. Ersätt `$KAFKAZKHOSTS` med Zookeepers värdinformation som du använde när du konfigurerade topologin:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic stormtopic --zookeeper $KAFKAZKHOSTS
    ```

    Det här kommandot ansluter till Zookeeper för Kafka-klustret och skapar ett nytt ämne som heter `stormtopic`. Ämnet används av Storm-topologierna.

## <a name="start-the-writer"></a>Starta skrivaren

1. Använd följande för att ansluta till **Storm**-klustret via SSH. Ersätt `sshuser` med SSH-användarnamnet som användes när du skapade klustret. Ersätt `stormclustername` med namnet på Storm-klustret:

    ```bash
    ssh sshuser@stormclustername-ssh.azurehdinsight.net
    ```

    När du uppmanas till det anger du det lösenord som du använde när du skapade klustren.
   
    Mer information finns i [Use SSH with HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) (Använda SSH med HDInsight).

2. Använd följande kommando från SSH-anslutningen till Storm-klustret för att starta skrivartopologin:

    ```bash
    storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /writer.yaml --filter dev.properties
    ```

    De parametrar som används i kommandot är:

    * `org.apache.storm.flux.Flux`: Använd Flux när du konfigurerar och kör den här topologin.

    * `--remote`: Skicka topologin till Nimbus. Topologin distribueras över arbetarnoderna i klustret.

    * `-R /writer.yaml`: Använd `writer.yaml`-filen för att konfigurera topologin. `-R` anger att den här resursen ingår i jar-filen. Den finns i jar-roten så den har sökvägen `/writer.yaml`.

    * `--filter`: Fyll i poster i `writer.yaml`-topologin med värdena i `dev.properties`-filen. Exempelvis används värdet för `kafka.topic`-posten i filen till att ersätta `${kafka.topic}`-posten i topologins definition.

## <a name="start-the-reader"></a>Starta läsaren

1. Använd följande kommando från SSH-sessionen till Storm-klustret för att starta läsartopologin:

   ```bash
   storm jar KafkaTopology-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux --remote -R /reader.yaml --filter dev.properties
   ```

2. Vänta en minut och använd sedan följande kommando för att se filerna som skapats av läsartopologin:

    ```bash
    hdfs dfs -ls /stormdata
    ```

    De utdata som genereras liknar följande text:

    ```output
    Found 173 items
      -rw-r--r--   1 storm supergroup       5137 2018-04-09 19:00 /stormdata/hdfs-bolt-4-0-1523300453088.txt
      -rw-r--r--   1 storm supergroup       5128 2018-04-09 19:00 /stormdata/hdfs-bolt-4-1-1523300453624.txt
      -rw-r--r--   1 storm supergroup       5131 2018-04-09 19:00 /stormdata/hdfs-bolt-4-10-1523300455170.txt
      ...
    ```

3. Använd följande kommando för att se innehållet i filen. Ersätt `filename.txt` med namnet på en fil:

    ```bash
    hdfs dfs -cat /stormdata/filename.txt
    ```

    Följande text är ett exempel på filinnehåll:

    > fyra poäng och sju år sedan
    >
    > snö vitt och sju Dwarfs
    >
    > Jag är två med karaktär
    >
    > snö vitt och sju Dwarfs
    >
    > Jag är två med karaktär
    >
    > fyra poäng och sju år sedan
    >
    > en Apple a-dag håller läkaren borta

## <a name="stop-the-topologies"></a>Stoppa topologierna

Använd följande kommando från en SSH-session till Storm-klustret för att stoppa Storm-topologierna:

  ```bash
  storm kill kafka-writer
  storm kill kafka-reader
  ```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa resurserna som har skapats med den här självstudien kan du ta bort resursgruppen. När du tar bort resursgruppen raderas även det kopplade HDInsight-klustret och eventuella andra resurser som är associerade med resursgruppen.

Ta bort en resursgrupp med Azure Portal:

1. I Azure Portal expanderar du menyn på vänster sida för att öppna tjänstemenyn och väljer sedan __Resursgrupper__ för att visa listan med dina resursgrupper.
2. Leta reda på den resursgrupp du vill ta bort och högerklicka på knappen __Mer__ (...) till höger om listan.
3. Välj __Ta bort resursgrupp__ och bekräfta.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig att använda en [Apache Storm](https://storm.apache.org/)-topologi för att skriva till och läsa från [Apache Kafka](https://kafka.apache.org/) i HDInsight. Du har även lärt dig hur du lagrar data till den [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html)-kompatibla lagring som används av HDInsight.

> [!div class="nextstepaction"]
> [Använda Apache Kafka-producenten och konsument-API:et](kafka/apache-kafka-producer-consumer-api.md)
