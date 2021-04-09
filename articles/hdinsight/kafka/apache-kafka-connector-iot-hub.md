---
title: Använda Apache Kafka på HDInsight med Azure IoT Hub
description: Lär dig hur du använder Apache Kafka i HDInsight med Azure IoT Hub. Kafka Connect Azure IoT Hub Project innehåller en källa och en mottagar anslutning för Kafka. Käll anslutningen kan läsa data från IoT Hub och mottagar kopplingen skriver till IoT Hub.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/26/2019
ms.openlocfilehash: feada70c7a461bb4a9cd621c76b5606a7f0e19d5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104865289"
---
# <a name="use-apache-kafka-on-hdinsight-with-azure-iot-hub"></a>Använda Apache Kafka på HDInsight med Azure IoT Hub

Lär dig hur du använder [Apache Kafka Connect Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) Connector för att flytta data mellan Apache Kafka på HDInsight och Azure IoT Hub. I det här dokumentet får du lära dig hur du kör IoT Hub Connector från en Edge-nod i klustret.

Med Kafka Connect API kan du implementera anslutningar som kontinuerligt hämtar data till Kafka eller skicka data från Kafka till ett annat system. [Apache Kafka Connect Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub) är en anslutning som hämtar data från Azure-IoT Hub till Kafka. Den kan också skicka data från Kafka till IoT Hub.

När du hämtar från IoT Hub använder du en __käll__ koppling. När du skickar till IoT Hub använder du en __mottagar__ anslutning. IoT Hub-anslutaren tillhandahåller både käll-och mottagar anslutningarna.

I följande diagram visas data flödet mellan Azure IoT Hub och Kafka i HDInsight när du använder anslutnings tjänsten.

:::image type="content" source="./media/apache-kafka-connector-iot-hub/iot-hub-kafka-connector-hdinsight.png" alt-text="Bild som visar data som flödar från IoT Hub till Kafka via kopplingen" border="false":::

Mer information om Connect API finns i [https://kafka.apache.org/documentation/#connect](https://kafka.apache.org/documentation/#connect) .

## <a name="prerequisites"></a>Förutsättningar

* Ett Apache Kafka kluster i HDInsight. Mer information finns i dokumentet [Snabbstart för Kafka på HDInsight](apache-kafka-get-started.md).

* En Edge-nod i Kafka-klustret. Mer information finns i [använda Edge-noder med HDInsight](../hdinsight-apps-use-edge-node.md) -dokument.

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md).

* En Azure-IoT Hub och-enhet. I den här artikeln bör du överväga att använda [Anslut Raspberry Pi online Simulator till Azure IoT Hub](../../iot-hub/iot-hub-raspberry-pi-web-simulator-get-started.md).

* [Scala build-verktyg](https://www.scala-sbt.org/).

## <a name="build-the-connector"></a>Bygg anslutningen

1. Hämta källan för anslutningen från [https://github.com/Azure/toketi-kafka-connect-iothub/](https://github.com/Azure/toketi-kafka-connect-iothub/) till din lokala miljö.

2. Öppna en kommando tolk och navigera till `toketi-kafka-connect-iothub-master` katalogen. Använd sedan följande kommando för att bygga och paketera projektet:

    ```cmd
    sbt assembly
    ```

    Det tar några minuter att slutföra versionen. Kommandot skapar en fil med namnet `kafka-connect-iothub-assembly_2.11-0.7.0.jar` i `toketi-kafka-connect-iothub-master\target\scala-2.11` katalogen för projektet.

## <a name="install-the-connector"></a>Installera anslutningen

1. Ladda upp. jar-filen till Edge-noden i din Kafka i HDInsight-klustret. Redigera kommandot nedan genom att ersätta `CLUSTERNAME` med det faktiska namnet på klustret. Standardvärdena för SSH-användarkontot och namnet på [Edge-noden](../hdinsight-apps-use-edge-node.md#access-an-edge-node) används nedan, ändra efter behov.

    ```cmd
    scp kafka-connect-iothub-assembly*.jar sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. När fil kopieringen är klar ansluter du till Edge-noden med SSH:

    ```bash
    ssh sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Om du vill installera anslutningen i Kafka `libs` -katalogen använder du följande kommando:

    ```bash
    sudo mv kafka-connect-iothub-assembly*.jar /usr/hdp/current/kafka-broker/libs/
    ```

Se till att SSH-anslutningen är aktiv i de återstående stegen.

## <a name="configure-apache-kafka"></a>Konfigurera Apache Kafka

Från SSH-anslutningen till Edge-noden använder du följande steg för att konfigurera Kafka för att köra anslutnings tjänsten i fristående läge:

1. Konfigurera lösen ords variabel. Ersätt lösen ordet med klustrets inloggnings lösen ord och ange sedan kommandot:

    ```bash
    export password='PASSWORD'
    ```

1. Installera [JQ](https://stedolan.github.io/jq/) -verktyget. JQ gör det enklare att bearbeta JSON-dokument som returneras från Ambari-frågor. Ange följande kommando:

    ```bash
    sudo apt -y install jq
    ```

1. Hämta adressen till Kafka-utlösarna. Det kan finnas många mäklare i klustret, men du behöver bara referera till en eller två. Använd följande kommando för att hämta adressen till två Broker-värdar:

    ```bash
    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')

    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    echo $KAFKABROKERS
    ```

    Kopiera värdena för senare användning. Det värde som genereras liknar följande text:

    `wn0-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092,wn1-kafka.w5ijyohcxt5uvdhhuaz5ra4u5f.ex.internal.cloudapp.net:9092`

1. Hämta adressen till Apache Zookeeper-noderna. Det finns flera Zookeeper-noder i klustret, men du behöver bara referera till en eller två. Använd följande kommando för att lagra adresserna i variabeln `KAFKAZKHOSTS` :

    ```bash
    export KAFKAZKHOSTS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/ZOOKEEPER/components/ZOOKEEPER_SERVER | jq -r '["\(.host_components[].HostRoles.host_name):2181"] | join(",")' | cut -d',' -f1,2`
    ```

1. När du kör anslutningen i fristående läge `/usr/hdp/current/kafka-broker/config/connect-standalone.properties` används filen för att kommunicera med Kafka-utjämnare. Om du vill redigera `connect-standalone.properties` filen använder du följande kommando:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-standalone.properties
    ```

1. Gör följande redigeringar:

    |Aktuellt värde |Nytt värde | Kommentar |
    |---|---|---|
    |`bootstrap.servers=localhost:9092`|Ersätt `localhost:9092` värdet med Service Broker-värdarna från föregående steg|Konfigurerar den fristående konfigurationen för Edge-noden för att hitta Kafka-utjämnare.|
    |`key.converter=org.apache.kafka.connect.json.JsonConverter`|`key.converter=org.apache.kafka.connect.storage.StringConverter`|Med den här ändringen kan du testa att använda konsol tillverkaren som ingår i Kafka. Du kan behöva olika konverterare för andra producenter och konsumenter. Information om hur du använder andra konverterings värden finns i [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) .|
    |`value.converter=org.apache.kafka.connect.json.JsonConverter`|`value.converter=org.apache.kafka.connect.storage.StringConverter`|Samma som ovan.|
    |Ej tillämpligt|`consumer.max.poll.records=10`|Lägg till i slutet av filen. Den här ändringen är att förhindra timeout i mottagar anslutningen genom att begränsa den till 10 poster i taget. Mer information finns i [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).|

1. Om du vill spara filen använder du __CTRL + X__, __Y__ och __anger__ sedan.

1. Använd följande kommandon för att skapa ämnen som används av kopplingen:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotin --zookeeper $KAFKAZKHOSTS

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic iotout --zookeeper $KAFKAZKHOSTS
    ```

    Om du vill kontrol lera att `iotin` och `iotout` ämnena finns använder du följande kommando:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --list --zookeeper $KAFKAZKHOSTS
    ```

    `iotin`Avsnittet används för att ta emot meddelanden från IoT Hub. `iotout`Avsnittet används för att skicka meddelanden till IoT Hub.

## <a name="get-iot-hub-connection-information"></a>Hämta IoT Hub anslutnings information

Använd följande steg för att hämta information om IoT Hub som används av anslutningen:

1. Hämta den Event Hub-kompatibla slut punkten och det Event Hub-kompatibla slut punkts namnet för din IoT Hub. Använd någon av följande metoder för att hämta den här informationen:

   * Använd följande steg __från [Azure Portal](https://portal.azure.com/)__:

     1. Navigera till IoT Hub och välj __slut punkter__.
     2. Välj __händelser__ från __inbyggda slut punkter__.
     3. Från __Egenskaper__ kopierar du värdet för följande fält:

         * __Event Hub-kompatibelt namn__
         * __Event Hub – kompatibel slut punkt__
         * __Partitioner__

        > [!IMPORTANT]  
        > Slut punkt svärdet från portalen kan innehålla extra text som inte behövs i det här exemplet. Extrahera texten som matchar det här mönstret `sb://<randomnamespace>.servicebus.windows.net/` .

   * Använd följande kommando __från [Azure CLI](/cli/azure/get-started-with-azure-cli)__:

       ```azurecli
       az iot hub show --name myhubname --query "{EventHubCompatibleName:properties.eventHubEndpoints.events.path,EventHubCompatibleEndpoint:properties.eventHubEndpoints.events.endpoint,Partitions:properties.eventHubEndpoints.events.partitionCount}"
       ```

       Ersätt `myhubname` med namnet på din IoT Hub. Svaret liknar följande text:

       ```json
       "EventHubCompatibleEndpoint": "sb://ihsuprodbnres006dednamespace.servicebus.windows.net/",
       "EventHubCompatibleName": "iothub-ehub-myhub08-207673-d44b2a856e",
       "Partitions": 2
       ```

2. Hämta __principen för delad åtkomst__ och __nyckel__. I det här exemplet använder du __tjänst__ nyckeln. Använd någon av följande metoder för att hämta den här informationen:

    * Använd följande steg __från [Azure Portal](https://portal.azure.com/)__:

        1. Välj __principer för delad åtkomst__ och välj sedan __tjänst__.
        2. Kopiera värdet för __primär nyckel__ .
        3. Kopiera __anslutnings strängen – primär nyckel__ värde.

    * Använd följande kommando __från [Azure CLI](/cli/azure/get-started-with-azure-cli)__:

        1. Använd följande kommando för att hämta värdet för primär nyckel:

            ```azurecli
            az iot hub policy show --hub-name myhubname --name service --query "primaryKey"
            ```

            Ersätt `myhubname` med namnet på din IoT Hub. Svaret är den primära nyckeln till `service` principen för den här hubben.

        2. Använd följande kommando för att hämta anslutnings strängen för `service` principen:

            ```azurecli
            az iot hub show-connection-string --name myhubname --policy-name service --query "connectionString"
            ```

            Ersätt `myhubname` med namnet på din IoT Hub. Svaret är anslutnings strängen för `service` principen.

## <a name="configure-the-source-connection"></a>Konfigurera käll anslutningen

Om du vill konfigurera källan så att den fungerar med din IoT Hub utför du följande åtgärder från en SSH-anslutning till Edge-noden:

1. Skapa en kopia av `connect-iot-source.properties` filen i `/usr/hdp/current/kafka-broker/config/` katalogen. Om du vill hämta filen från toketi-Kafka-Connect-iothub-projektet använder du följande kommando:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-source.properties
    ```

1. Om du vill redigera `connect-iot-source.properties` filen och lägga till information om IoT Hub, använder du följande kommando:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

1. Leta upp och ändra följande poster i redigeraren:

    |Aktuellt värde |Redigera|
    |---|---|
    |`Kafka.Topic=PLACEHOLDER`|Ersätt `PLACEHOLDER` med `iotin`. Meddelanden som tas emot från IoT Hub placeras i `iotin` avsnittet.|
    |`IotHub.EventHubCompatibleName=PLACEHOLDER`|Ersätt `PLACEHOLDER` med Event Hub-kompatibelt namn.|
    |`IotHub.EventHubCompatibleEndpoint=PLACEHOLDER`|Ersätt `PLACEHOLDER` med den Event Hub-kompatibla slut punkten.|
    |`IotHub.AccessKeyName=PLACEHOLDER`|Ersätt `PLACEHOLDER` med `service`.|
    |`IotHub.AccessKeyValue=PLACEHOLDER`|Ersätt `PLACEHOLDER` med `service` principens primär nyckel.|
    |`IotHub.Partitions=PLACEHOLDER`|Ersätt `PLACEHOLDER` med antalet partitioner från föregående steg.|
    |`IotHub.StartTime=PLACEHOLDER`|Ersätt `PLACEHOLDER` med ett UTC-datum. Det här datumet är när kopplingen börjar söka efter meddelanden. Datum formatet är `yyyy-mm-ddThh:mm:ssZ` .|
    |`BatchSize=100`|Ersätt `100` med `5`. Den här ändringen gör att anslutningen läser meddelanden i Kafka när det finns fem nya meddelanden i IoT Hub.|

    En exempel konfiguration finns i [Kafka Connect source Connector för Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md).

1. Om du vill spara ändringarna använder du __CTRL + X__, __Y__ och __anger__ sedan.

Mer information om hur du konfigurerar anslutnings källan finns i [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Source.md) .

## <a name="configure-the-sink-connection"></a>Konfigurera Sink-anslutningen

Om du vill konfigurera Sink-anslutningen så att den fungerar med din IoT Hub utför du följande åtgärder från en SSH-anslutning till Edge-noden:

1. Skapa en kopia av `connect-iothub-sink.properties` filen i `/usr/hdp/current/kafka-broker/config/` katalogen. Om du vill hämta filen från toketi-Kafka-Connect-iothub-projektet använder du följande kommando:

    ```bash
    sudo wget -P /usr/hdp/current/kafka-broker/config/ https://raw.githubusercontent.com/Azure/toketi-kafka-connect-iothub/master/connect-iothub-sink.properties
    ```

1. Om du vill redigera `connect-iothub-sink.properties` filen och lägga till information om IoT Hub, använder du följande kommando:

    ```bash
    sudo nano /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
    ```

1. Leta upp och ändra följande poster i redigeraren:

    |Aktuellt värde |Redigera|
    |---|---|
    |`topics=PLACEHOLDER`|Ersätt `PLACEHOLDER` med `iotout`. Meddelanden som skrivs till `iotout` avsnittet vidarebefordras till IoT Hub.|
    |`IotHub.ConnectionString=PLACEHOLDER`|Ersätt `PLACEHOLDER` med anslutnings strängen för `service` principen.|

    En exempel konfiguration finns i [Kafka Connect Sink Connector för Azure IoT Hub](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md).

1. Om du vill spara ändringarna använder du __CTRL + X__, __Y__ och __anger__ sedan.

Mer information om hur du konfigurerar anslutnings mottagaren finns i [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) .

## <a name="start-the-source-connector"></a>Starta käll anslutningen

1. Starta käll anslutningen genom att använda följande kommando från en SSH-anslutning till Edge-noden:

    ```bash
    /usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-source.properties
    ```

    När kopplingen startar skickar du meddelanden till IoT Hub från din enhet (er). När kopplingen läser meddelanden från IoT-hubben och lagrar dem i Kafka-avsnittet, loggas information till konsolen:

    ```output
    [2017-08-29 20:15:46,112] INFO Polling for data - Obtained 5 SourceRecords from IotHub (com.microsoft.azure.iot.kafka.connect.IotHubSourceTask:39)
    [2017-08-29 20:15:54,106] INFO Finished WorkerSourceTask{id=AzureIotHubConnector-0} commitOffsets successfully in 4 ms (org.apache.kafka.connect.runtime.WorkerSourceTask:356)
    ```

    > [!NOTE]  
    > Du kan se flera varningar när anslutningen startar. Dessa varningar orsakar inte problem med att ta emot meddelanden från IoT Hub.

1. Stoppa anslutningen efter några minuter med hjälp av **CTRL + C** två gånger. Det tar några minuter för anslutningen att stoppas.

## <a name="start-the-sink-connector"></a>Starta Sink-anslutningen

Från en SSH-anslutning till Edge-noden använder du följande kommando för att starta mottagar anslutningen i fristående läge:

```bash
/usr/hdp/current/kafka-broker/bin/connect-standalone.sh /usr/hdp/current/kafka-broker/config/connect-standalone.properties /usr/hdp/current/kafka-broker/config/connect-iothub-sink.properties
```

När kopplingen körs visas information som liknar följande text:

```output
[2017-08-30 17:49:16,150] INFO Started tasks to send 1 messages to devices. (com.microsoft.azure.iot.kafka.connect.sink.
IotHubSinkTask:47)
[2017-08-30 17:49:16,150] INFO WorkerSinkTask{id=AzureIotHubSinkConnector-0} Committing offsets (org.apache.kafka.connect.runtime.WorkerSinkTask:262)
```

> [!NOTE]  
> Du kan märka flera varningar när anslutningen startar. Du kan ignorera dem.

## <a name="send-messages"></a>Skicka meddelanden

Använd följande steg för att skicka meddelanden via anslutningen:

1. Öppna *en andra* SSH-session med Kafka-klustret:

    ```bash
    ssh sshuser@new-edgenode.CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Hämta adressen till Kafka-utlösarna för den nya SSH-sessionen. Ersätt lösen ordet med klustrets inloggnings lösen ord och ange sedan kommandot:

    ```bash
    export password='PASSWORD'

    export clusterName=$(curl -u admin:$password -sS -G "http://headnodehost:8080/api/v1/clusters" | jq -r '.items[].Clusters.cluster_name')

    export KAFKABROKERS=`curl -sS -u admin:$password -G http://headnodehost:8080/api/v1/clusters/$clusterName/services/KAFKA/components/KAFKA_BROKER | jq -r '["\(.host_components[].HostRoles.host_name):9092"] | join(",")' | cut -d',' -f1,2`
    ```

1. Använd följande kommando för att skicka meddelanden till `iotout` ämnet:

    ```bash
    /usr/hdp/current/kafka-broker/bin/kafka-console-producer.sh --broker-list $KAFKABROKERS --topic iotout
    ```

    Det här kommandot går inte tillbaka till den normala bash-prompten. I stället skickar den inmatade tangenter till `iotout` ämnet.

1. Om du vill skicka ett meddelande till din enhet klistrar du in ett JSON-dokument i SSH-sessionen för `kafka-console-producer` .

    > [!IMPORTANT]  
    > Du måste ange värdet för `"deviceId"` posten till ID för enheten. I följande exempel heter enheten `myDeviceId` :

    ```json
    {"messageId":"msg1","message":"Turn On","deviceId":"myDeviceId"}
    ```

    Schemat för JSON-dokumentet beskrivs mer detaljerat i [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) .

    Om du använder den simulerade Raspberry Pi-enheten och den körs, loggas följande meddelande av enheten:

    ```output
    Receive message: Turn On
    ```

    Skicka JSON-dokumentet igen, men ändra värdet för `"message"` posten. Det nya värdet loggas av enheten.

Mer information om hur du använder Sink-anslutningen finns i [https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md](https://github.com/Azure/toketi-kafka-connect-iothub/blob/master/README_Sink.md) .

## <a name="next-steps"></a>Nästa steg

I det här dokumentet har du lärt dig hur du använder Apache Kafka Connect API för att starta IoT Kafka-anslutaren på HDInsight. Använd följande länkar för att identifiera andra sätt att arbeta med Kafka:

* [Använda Apache Spark med Apache Kafka på HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Använda Apache Storm med Apache Kafka på HDInsight](../hdinsight-apache-storm-with-kafka.md)
