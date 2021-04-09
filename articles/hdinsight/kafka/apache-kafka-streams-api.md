---
title: 'Självstudie: Använda Apache Kafka Streams-API:er – Azure HDInsight '
description: 'Självstudie – lär dig hur du använder API: erna Apache Kafka Stream med Kafka på HDInsight. Detta API kan du utföra för att strömma bearbetningen mellan ämnen i Kafka.'
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive
ms.date: 03/20/2020
ms.openlocfilehash: 5a1548cdf1d05a1f9d42f5c64b7fdc18f514518e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98939200"
---
# <a name="tutorial-use-apache-kafka-streams-api-in-azure-hdinsight"></a>Självstudie: använda Apache Kafka Streams-API i Azure HDInsight

Lär dig hur du skapar ett program som använder Apache Kafka Streams-API:et och kör det med Kafka i HDInsight.

Programmet som används i den här självstudien är en strömmande ordräkning. Den läser textdata från ett Kafka-ämne, extraherar enskilda ord och lagrar sedan ordet och antalet till ett annat Kafka-ämne.

Kafka-strömningsbearbetning görs ofta med Apache Spark eller Apache Storm. Kafka version 1.1.0 (i HDInsight 3,5 och 3,6) introducerade API: t för Kafka Streams. Med detta API kan du transformera dataströmmar mellan indata- och utdata-ämnen. I vissa fall kan detta vara ett alternativ till att skapa en strömmande Spark- eller Storm-lösning.

Mer information om Kafka Streams finns i dokumentationen [Intro to Streams](https://kafka.apache.org/10/documentation/streams/) (Introduktion till Streams) på Apache.org.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Förstå koden
> * Skapa och distribuera programmet
> * Konfigurera Kafka-ämnen
> * Kör koden

## <a name="prerequisites"></a>Förutsättningar

* En Kafka på HDInsight 3.6-klustret. Information om hur du skapar en Kafka på ett HDInsight-kluster finns i dokumentet [Starta med Apache Kafka i HDInsight](apache-kafka-get-started.md).

* Utför stegen i dokumentet [Apache Kafka-konsument- och producent-API](apache-kafka-producer-consumer-api.md). Stegen i det här dokumentet använder exempelprogram och avsnitt som skapats i den här kursen.

* [Java Developer Kit (JDK) version 8](/azure/developer/java/fundamentals/java-jdk-long-term-support) eller motsvarande, till exempel openjdk.

* [Apache maven](https://maven.apache.org/download.cgi) korrekt [installerat](https://maven.apache.org/install.html) enligt Apache.  Maven är ett projekt versions system för Java-projekt.

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

## <a name="understand-the-code"></a>Förstå koden

Exempel programmet finns [https://github.com/Azure-Samples/hdinsight-kafka-java-get-started](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started) i i under `Streaming` katalogen. Programmet består av två filer:

* `pom.xml`: Den här filen definierar projektberoenden, Java-version och paketeringsmetoder.
* `Stream.java`: Den här filen implementerar strömningslogiken.

### <a name="pomxml"></a>Pom.xml

Viktiga saker att förstå i `pom.xml`-filen är:

* Beroenden: Det här projektet använder Kafka Streams-API:er, som tillhandahålls av `kafka-clients`-paketet. Följande XML-kod definierar detta beroende:

    ```xml
    <!-- Kafka client for producer/consumer operations -->
    <dependency>
            <groupId>org.apache.kafka</groupId>
            <artifactId>kafka-clients</artifactId>
            <version>${kafka.version}</version>
    </dependency>
    ```

    `${kafka.version}`-posten har deklarerats i `<properties>..</properties>`-avsnittet i `pom.xml` och är konfigurerad till Kafka-versionen av HDInsight-klustret.

* Plugin-program: Plugin-programmet Maven innehåller olika funktioner. I det här projektet används följande plugin-program:

    * `maven-compiler-plugin`: Används för att ange den Java-version som används av projektet till 8. Java 8 krävs av HDInsight 3.6.
    * `maven-shade-plugin`: Används för att generera en Uber-jar som innehåller det här programmet och eventuella beroenden. Den används också för att ange start punkten för programmet, så att du kan köra jar-filen direkt utan att behöva ange huvud klassen.

### <a name="streamjava"></a>Stream.java

Filen [Stream.java](https://github.com/Azure-Samples/hdinsight-kafka-java-get-started/blob/master/Streaming/src/main/java/com/microsoft/example/Stream.java) använder Streams-API för att implementera ett ordantalprogram. Den läser data från ett Kafka-ämne som heter `test` och skriver ordräkningen till ett ämne som heter `wordcounts`.

Följande kod definierar ordräkningsprogrammet:

```java
package com.microsoft.example;

import org.apache.kafka.common.serialization.Serde;
import org.apache.kafka.common.serialization.Serdes;
import org.apache.kafka.streams.KafkaStreams;
import org.apache.kafka.streams.KeyValue;
import org.apache.kafka.streams.StreamsConfig;
import org.apache.kafka.streams.kstream.KStream;
import org.apache.kafka.streams.kstream.KStreamBuilder;

import java.util.Arrays;
import java.util.Properties;

public class Stream
{
    public static void main( String[] args ) {
        Properties streamsConfig = new Properties();
        // The name must be unique on the Kafka cluster
        streamsConfig.put(StreamsConfig.APPLICATION_ID_CONFIG, "wordcount-example");
        // Brokers
        streamsConfig.put(StreamsConfig.BOOTSTRAP_SERVERS_CONFIG, args[0]);
        // SerDes for key and values
        streamsConfig.put(StreamsConfig.KEY_SERDE_CLASS_CONFIG, Serdes.String().getClass().getName());
        streamsConfig.put(StreamsConfig.VALUE_SERDE_CLASS_CONFIG, Serdes.String().getClass().getName());

        // Serdes for the word and count
        Serde<String> stringSerde = Serdes.String();
        Serde<Long> longSerde = Serdes.Long();

        KStreamBuilder builder = new KStreamBuilder();
        KStream<String, String> sentences = builder.stream(stringSerde, stringSerde, "test");
        KStream<String, Long> wordCounts = sentences
                .flatMapValues(value -> Arrays.asList(value.toLowerCase().split("\\W+")))
                .map((key, word) -> new KeyValue<>(word, word))
                .countByKey("Counts")
                .toStream();
        wordCounts.to(stringSerde, longSerde, "wordcounts");

        KafkaStreams streams = new KafkaStreams(builder, streamsConfig);
        streams.start();

        Runtime.getRuntime().addShutdownHook(new Thread(streams::close));
    }
}
```

## <a name="build-and-deploy-the-example"></a>Skapa och distribuera exemplet

För att skapa och distribuera projektet till Kafka p HDInsight-klustret utför du följande steg:

1. Ange den aktuella katalogen till platsen för `hdinsight-kafka-java-get-started-master\Streaming` katalogen och Använd sedan följande kommando för att skapa ett jar-paket:

    ```cmd
    mvn clean package
    ```

    Det här kommandot skapar paketet på `target/kafka-streaming-1.0-SNAPSHOT.jar`.

2. Ersätt `sshuser` med SSH-användare för klustret och ersätt `clustername` med namnet på klustret. Använd följande kommando för att kopiera `kafka-streaming-1.0-SNAPSHOT.jar` filen till HDInsight-klustret. Ange lösenordet för SSH-användarkontot om du uppmanas till det.

    ```cmd
    scp ./target/kafka-streaming-1.0-SNAPSHOT.jar sshuser@clustername-ssh.azurehdinsight.net:kafka-streaming.jar
    ```

## <a name="create-apache-kafka-topics"></a>Skapa Apache Kafka-ämnen

1. Ersätt `sshuser` med SSH-användare för klustret och ersätt `CLUSTERNAME` med namnet på klustret. Öppna en SSH-anslutning till klustret genom att ange följande kommando. Ange lösenordet för SSH-användarkontot om du uppmanas till det.

    ```bash
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Installera [JQ](https://stedolan.github.io/jq/), en JSON-processor med kommando rad. I den öppna SSH-anslutningen anger du följande kommando för att installera `jq` :

    ```bash
    sudo apt -y install jq
    ```

3. Konfigurera lösen ords variabel. Ersätt `PASSWORD` med klustrets inloggnings lösen ord och ange sedan kommandot:

    ```bash
    export password='PASSWORD'
    ```

4. Extrahera korrekt bokstäver-kluster namn. Det faktiska Skift läget i kluster namnet kan skilja sig från förväntat, beroende på hur klustret skapades. Det här kommandot kommer att hämta det faktiska Skift läget och sedan lagra det i en variabel. Ange följande kommando:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')
    ```

    > [!Note]  
    > Om du utför den här processen utanför klustret finns det en annan procedur för att lagra kluster namnet. Hämta kluster namnet i gemener från Azure Portal. Ersätt sedan kluster namnet för `<clustername>` i följande kommando och kör det: `export clusterName='<clustername>'` .  

5. Använd följande kommandon för att hämta värdar för Apache Kafka-meddelandeköer och Apache Zookeeper. När du blir ombedd anger du lösenordet till klusterinloggningskontot (admin).

    ```bash
    export KAFKAZKHOSTS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2);

    export KAFKABROKERS=$(curl -sS -u admin:$password -G https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2);
    ```

    > [!Note]  
    > De här kommandona kräver Ambari-åtkomst. Om klustret ligger bakom en NSG kör du dessa kommandon från en dator som har åtkomst till Ambari.

6. Skapa ämnen som används av strömningen med följande kommandon:

    > [!NOTE]  
    > Du kan få ett felmeddelande om att ämnet `test` redan finns. Det är OK eftersom det kan ha skapats i självstudien Producent- och konsument-API.

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic test --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcounts --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic RekeyedIntermediateTopic --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic wordcount-example-Counts-changelog --zookeeper $KAFKAZKHOSTS
    ```

    Ämnena använda för följande ändamål:

   * `test`: I det här ämnet tas poster emot. Strömningsprogrammet läses härifrån.
   * `wordcounts`: I det här ämnet lagrar strömningsprogrammet sina utdata.
   * `RekeyedIntermediateTopic`: Det här ämnet  används för att partitionera om data när antalet uppdateras av operatorn `countByKey`.
   * `wordcount-example-Counts-changelog`: Det är ämnet är ett tillståndslager som används av åtgärden `countByKey`

    Kafka på HDInsight kan också konfigureras för att automatiskt skapa ämnen. Mer information finns i dokumentet [Configure automatic topic creation](apache-kafka-auto-create-topics.md) (Konfigurera automatiskt skapande av ämne).

## <a name="run-the-code"></a>Kör koden

1. Om du vill starta strömningsprogrammet som en bakgrundsprocess använder du följande kommando:

    ```bash
    java -jar kafka-streaming.jar $KAFKABROKERS $KAFKAZKHOSTS &
    ```

    Du får kanske en varning om Apache log4j. Du kan ignorera den.

2. För att skicka poster till ämnet `test` använder du följande kommando för att starta producentprogrammet:

    ```bash
    java -jar kafka-producer-consumer.jar producer test $KAFKABROKERS
    ```

3. När producenten är klar använder du följande kommando för att visa informationen som är lagrad i ämnet `wordcounts`:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-consumer.sh --bootstrap-server $KAFKABROKERS --topic wordcounts --formatter kafka.tools.DefaultMessageFormatter --property print.key=true --property key.deserializer=org.apache.kafka.common.serialization.StringDeserializer --property value.deserializer=org.apache.kafka.common.serialization.LongDeserializer --from-beginning
    ```

    Parametrarna `--property` uppmanar konsolkonsumenten att skriva ut nyckeln (ordet) tillsammans med antalet (värdet). Den här parametern konfigurerar även funktionen för avserialisering som ska användas vid läsning av dessa värden från Kafka.

    De utdata som genereras liknar följande text:

    ```output
    dwarfs  13635
    ago     13664
    snow    13636
    dwarfs  13636
    ago     13665
    a       13803
    ago     13666
    a       13804
    ago     13667
    ago     13668
    jumped  13640
    jumped  13641
    ```

    Parametern `--from-beginning` konfigurerar konsumenten för att börja vid början av posterna i ämnet. Antalet ökar varje gång ett ord påträffas, så det här ämnet innehåller flera poster för varje ord med ett ökande antal.

4. Använd __Ctrl + C__ om du vill avsluta producenten. Fortsätt att använda __Ctrl + C__ för att avsluta programmet och konsumenten.

5. Om du vill ta bort de ämnen som används av streaming-åtgärden använder du följande kommandon:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic test --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic wordcounts --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic RekeyedIntermediateTopic --zookeeper $KAFKAZKHOSTS
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --delete --topic wordcount-example-Counts-changelog --zookeeper $KAFKAZKHOSTS
    ```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa resurserna som har skapats med den här självstudien kan du ta bort resursgruppen. När du tar bort resursgruppen raderas även det kopplade HDInsight-klustret och eventuella andra resurser som är associerade med resursgruppen.

Ta bort en resursgrupp med Azure Portal:

1. I Azure Portal expanderar du menyn på vänster sida för att öppna tjänstemenyn och väljer sedan __Resursgrupper__ för att visa listan med dina resursgrupper.
2. Leta reda på den resursgrupp du vill ta bort och högerklicka på knappen __Mer__ (...) till höger om listan.
3. Välj __Ta bort resursgrupp__ och bekräfta.

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig använda Apache Kafka Streams-API med Kafka i HDInsight. Använd följande för att lära dig mer om att arbeta med Kafka.

> [!div class="nextstepaction"]
> [Analysera Apache Kafka-loggar](apache-kafka-log-analytics-operations-management.md)