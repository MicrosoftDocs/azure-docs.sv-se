---
title: Använda Apache Flink för Apache Kafka-Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller information om hur du ansluter Apache Flink till en Azure Event Hub
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 1d382270248e95b1b973f57f67ebf81160f03a16
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92369513"
---
# <a name="use-apache-flink-with-azure-event-hubs-for-apache-kafka"></a>Använda Apache Flink med Azure Event Hubs för Apache Kafka
Den här självstudien visar hur du ansluter Apache Flink till en Event Hub utan att ändra protokoll klienter eller köra egna kluster. Mer information om Event Hubs support för Apache Kafka-konsument protokollet finns i [Event Hubs för Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md).


I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Skapa ett Event Hubs-namnområde
> * Klona exempelprojektet
> * Kör Flink-tillverkare 
> * Kör Flink-konsument

> [!NOTE]
> Det här exemplet finns på [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink)

## <a name="prerequisites"></a>Förutsättningar

Kontrol lera att du har följande krav för att slutföra den här självstudien:

* Läs artikeln [Event Hubs för Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md). 
* En Azure-prenumeration. Om du inte har ett konto kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) innan du börjar.
* [Java Development Kit (JDK) 1.7 +](/azure/developer/java/fundamentals/java-jdk-long-term-support)
    * I Ubuntu kör du `apt-get install default-jdk` för att installera JDK-paketet.
    * Tänk på att ställa in miljövariabeln JAVA_HOME så att den pekar på den mapp där JDK-paketet är installerat.
* [Hämta](https://maven.apache.org/download.cgi) och [Installera](https://maven.apache.org/install.html) ett maven-binärt Arkiv
    * I Ubuntu kan du köra `apt-get install maven` för att installera Maven.
* [Git](https://www.git-scm.com/downloads)
    * I Ubuntu kan du köra `sudo apt-get install git` för att installera Git.

## <a name="create-an-event-hubs-namespace"></a>Skapa ett Event Hubs-namnområde

Ett Event Hubs-namnområde krävs för att skicka eller ta emot från en Event Hubs-tjänst. Instruktioner för att skapa ett namn område och en Event Hub finns i [skapa en Event Hub](event-hubs-create.md) . Se till att kopiera Event Hubs anslutnings strängen för senare användning.

## <a name="clone-the-example-project"></a>Klona exempelprojektet

Nu när du har Event Hubs anslutnings strängen klonar du Azure-Event Hubs för Kafka-lagringsplatsen och navigerar till `flink` undermappen:

```shell
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/flink
```

## <a name="run-flink-producer"></a>Kör Flink-tillverkare

Med hjälp av det angivna Flink Producer-exemplet skickar du meddelanden till tjänsten Event Hubs.

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Ange en Event Hubs Kafka-slutpunkt

#### <a name="producerconfig"></a>producer.config

Uppdatera `bootstrap.servers` värdena och för `sasl.jaas.config` `producer/src/main/resources/producer.config` att dirigera producenten till den Event Hubs Kafka-slutpunkten med rätt autentisering.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
client.id=FlinkExampleProducer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!IMPORTANT]
> Ersätt `{YOUR.EVENTHUBS.CONNECTION.STRING}` med anslutnings strängen för din Event Hubs-namnrymd. Anvisningar om hur du hämtar anslutnings strängen finns i [Hämta en Event Hubs anslutnings sträng](event-hubs-get-connection-string.md). Här är ett exempel på en konfiguration: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`

### <a name="run-producer-from-the-command-line"></a>Kör Producer från kommando raden

Om du vill köra producenten från kommando raden genererar du BURKen och kör sedan inifrån maven (eller genererar JAR med Maven och kör sedan i Java genom att lägga till de nödvändiga Kafka-JAR: erna i classpath):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestProducer"
```

Nu börjar producenten att skicka händelser till händelsehubben i avsnittet `test` och skriva ut händelserna till stdout.

## <a name="run-flink-consumer"></a>Kör Flink-konsument

Med hjälp av det tillhandahållna konsument exemplet tar du emot meddelanden från händelsehubben. 

### <a name="provide-an-event-hubs-kafka-endpoint"></a>Ange en Event Hubs Kafka-slutpunkt

#### <a name="consumerconfig"></a>consumer.config

Uppdatera `bootstrap.servers` värdena och för `sasl.jaas.config` `consumer/src/main/resources/consumer.config` att dirigera konsumenten till den Event Hubs Kafka-slutpunkten med rätt autentisering.

```xml
bootstrap.servers={YOUR.EVENTHUBS.FQDN}:9093
group.id=FlinkExampleConsumer
sasl.mechanism=PLAIN
security.protocol=SASL_SSL
sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required \
   username="$ConnectionString" \
   password="{YOUR.EVENTHUBS.CONNECTION.STRING}";
```

> [!IMPORTANT]
> Ersätt `{YOUR.EVENTHUBS.CONNECTION.STRING}` med anslutnings strängen för din Event Hubs-namnrymd. Anvisningar om hur du hämtar anslutnings strängen finns i [Hämta en Event Hubs anslutnings sträng](event-hubs-get-connection-string.md). Här är ett exempel på en konfiguration: `sasl.jaas.config=org.apache.kafka.common.security.plain.PlainLoginModule required username="$ConnectionString" password="Endpoint=sb://mynamespace.servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=XXXXXXXXXXXXXXXX";`


### <a name="run-consumer-from-the-command-line"></a>Kör konsument från kommando raden

Om du vill köra konsumenten från kommando raden genererar du BURKen och kör sedan inifrån maven (eller genererar JAR med Maven och kör sedan i Java genom att lägga till de nödvändiga Kafka-JAR: erna i classpath):

```shell
mvn clean package
mvn exec:java -Dexec.mainClass="FlinkTestConsumer"
```

Om händelsehubben har händelser (till exempel om din producent också körs) börjar konsumenten nu ta emot händelser från ämnet `test` .

Mer detaljerad information om hur du ansluter Flink till Kafka finns i [Flink Kafka Connector guide](https://ci.apache.org/projects/flink/flink-docs-stable/dev/connectors/kafka.html) .

## <a name="next-steps"></a>Nästa steg
Mer information om Event Hubs för Kafka finns i följande artiklar:  

- [Spegla en Kafka-broker i en händelsehubb](event-hubs-kafka-mirror-maker-tutorial.md)
- [Ansluta Apache Spark till en händelsehubb](event-hubs-kafka-spark-tutorial.md)
- [Integrera Kafka ansluta med en Event Hub](event-hubs-kafka-connect-tutorial.md)
- [Utforska exempel på vår GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Ansluta Akka-dataströmmar till en händelsehubb](event-hubs-kafka-akka-streams-tutorial.md)
- [Apache Kafka Developer Guide för Azure Event Hubs](apache-kafka-developer-guide.md)