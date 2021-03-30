---
title: Ansluta med en Apache Spark-app – Azure Event Hubs | Microsoft Docs
description: Den här artikeln innehåller information om hur du använder Apache Spark med Azure Event Hubs för Kafka.
ms.topic: how-to
ms.date: 06/23/2020
ms.openlocfilehash: 84184ed3dffee97863b93c592d1cd577df313605
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92913746"
---
# <a name="connect-your-apache-spark-application-with-azure-event-hubs"></a>Anslut ditt Apache Spark program med Azure Event Hubs
Den här självstudien vägleder dig genom att ansluta Spark-programmet till Event Hubs för strömning i real tid. Den här integreringen möjliggör direkt uppspelning utan att behöva ändra protokoll klienter eller köra egna Kafka-eller Zookeeper-kluster. Den här självstudien kräver Apache Spark v 2.4 + och Apache Kafka v 2.0 +.

> [!NOTE]
> Det här exemplet finns på [GitHub](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark/)

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Skapa ett Event Hubs-namnområde
> * Klona exempelprojektet
> * Köra Spark
> * Läsa från Event Hubs för Kafka
> * Skriva till Event Hubs för Kafka

## <a name="prerequisites"></a>Förutsättningar

Kontrollera att du har följande innan du börjar den här självstudien:
-   En Azure-prenumeration. Om du inte har en [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/).
-   [Apache Spark v2.4](https://spark.apache.org/downloads.html)
-   [Apache Kafka v2.0]( https://kafka.apache.org/20/documentation.html)
-   [Git](https://www.git-scm.com/downloads)

> [!NOTE]
> Spark-Kafka-adaptern har uppdaterats för att stödja Kafka v2.0 från och med Spark v2.4. I tidigare versioner av Spark hade adaptern stöd för Kafka v0.10 och senare men förlitade sig tidigare särskilt på Kafka v0.10-API:er. Eftersom Event Hubs för Kafka inte har stöd för Kafka v0.10 stöds inte Spark-Kafka-adaptrar från versioner av Spark före v2.4 av Event Hubs för Kafka-ekosystem.


## <a name="create-an-event-hubs-namespace"></a>Skapa ett Event Hubs-namnområde
En Event Hubs-namnrymd krävs för att skicka och ta emot från Event Hubs-tjänster. Instruktioner för att skapa ett namn område och en Event Hub finns i [skapa en Event Hub](event-hubs-create.md) . Hämta Event Hubs-anslutningssträngen och fullständigt domännamn (FQDN) för senare användning. Anvisningar finns i avsnittet om att [hämta en Event Hubs-anslutningssträng](event-hubs-get-connection-string.md). 

## <a name="clone-the-example-project"></a>Klona exempelprojektet
Klona Azure Event Hubs-lagringsplatsen och navigera till undermappen `tutorials/spark`:

```bash
git clone https://github.com/Azure/azure-event-hubs-for-kafka.git
cd azure-event-hubs-for-kafka/tutorials/spark
```

## <a name="read-from-event-hubs-for-kafka"></a>Läsa från Event Hubs för Kafka
Med några få konfigurationsändringar kan du börja läsa från Event Hubs för Kafka. Uppdatera **BOOTSTRAP_SERVERS** och **EH_SASL** med information från din namnrymd så kan du starta strömning med Event Hubs på samma sätt som med Kafka. Den fullständiga exempelkoden finns i filen sparkConsumer.scala på GitHub. 

```scala
//Read from your Event Hub!
val df = spark.readStream
    .format("kafka")
    .option("subscribe", TOPIC)
    .option("kafka.bootstrap.servers", BOOTSTRAP_SERVERS)
    .option("kafka.sasl.mechanism", "PLAIN")
    .option("kafka.security.protocol", "SASL_SSL")
    .option("kafka.sasl.jaas.config", EH_SASL)
    .option("kafka.request.timeout.ms", "60000")
    .option("kafka.session.timeout.ms", "30000")
    .option("kafka.group.id", GROUP_ID)
    .option("failOnDataLoss", "true")
    .load()

//Use dataframe like normal (in this example, write to console)
val df_write = df.writeStream
    .outputMode("append")
    .format("console")
    .start()
```

## <a name="write-to-event-hubs-for-kafka"></a>Skriva till Event Hubs för Kafka
Du kan även skriva till Event Hubs på samma sätt som du skriver till Kafka. Glöm inte att uppdatera din konfiguration genom att ändra **BOOTSTRAP_SERVERS** och **EH_SASL** med information från din Event Hubs-namnrymd.  Den fullständiga exempelkoden finns i filen sparkProducer.scala på GitHub. 

```scala
df = /**Dataframe**/

//Write to your Event Hub!
df.writeStream
    .format("kafka")
    .option("topic", TOPIC)
    .option("kafka.bootstrap.servers", BOOTSTRAP_SERVERS)
    .option("kafka.sasl.mechanism", "PLAIN")
    .option("kafka.security.protocol", "SASL_SSL")
    .option("kafka.sasl.jaas.config", EH_SASL)
    .option("checkpointLocation", "./checkpoint")
    .start()
```



## <a name="next-steps"></a>Nästa steg
Mer information om Event Hubs och Event Hubs för Kafka finns i följande artiklar:  

- [Spegla en Kafka-broker i en händelsehubb](event-hubs-kafka-mirror-maker-tutorial.md)
- [Ansluta Apache Flink till en händelsehubb](event-hubs-kafka-flink-tutorial.md)
- [Integrera Kafka ansluta med en Event Hub](event-hubs-kafka-connect-tutorial.md)
- [Utforska exempel på vår GitHub](https://github.com/Azure/azure-event-hubs-for-kafka)
- [Ansluta Akka-dataströmmar till en händelsehubb](event-hubs-kafka-akka-streams-tutorial.md)
- [Apache Kafka Developer Guide för Azure Event Hubs](apache-kafka-developer-guide.md)

