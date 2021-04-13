---
title: Apache Kafka Developer Guide för Event Hubs
description: Den här artikeln innehåller länkar till artiklar som beskriver hur du integrerar dina Kafka-program med Azure Event Hubs.
ms.date: 06/23/2020
ms.topic: article
ms.openlocfilehash: 84fa40d7cc4b09f31d78665f901b49adacf3267f
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107303091"
---
# <a name="apache-kafka-developer-guide-for-azure-event-hubs"></a>Apache Kafka Developer Guide för Azure Event Hubs
Den här artikeln innehåller länkar till artiklar som beskriver hur du integrerar dina Apache Kafka-program med Azure Event Hubs. 

## <a name="overview"></a>Översikt
Event Hubs tillhandahåller en Kafka-slutpunkt som kan användas av dina befintliga Kafka-baserade program som ett alternativ till att köra ditt eget Kafka-kluster. Event Hubs fungerar med många av dina befintliga Kafka-program. Mer information finns i [Event Hubs för Apache Kafka](event-hubs-for-kafka-ecosystem-overview.md)

## <a name="quickstarts"></a>Snabbstarter
Du hittar snabb starter i GitHub och i den här innehålls uppsättningen som hjälper dig att snabbt öka rampen på Event Hubs för Kafka.

### <a name="quickstarts-in-github"></a>Snabb starter i GitHub
Se följande snabb starter i **Azure-Event-Hub-for-Kafka** lagrings platsen: 

| Klient språk/-ramverk | Description | 
| ------------------------- | ----------- | 
| [.NET](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/dotnet) | <p>I den här snabb starten visas hur du skapar och ansluter till en Event Hubs Kafka-slutpunkt med ett exempel på producent och konsument skriven i C# med hjälp av .NET Core 2,0.</p><p>Det här exemplet baseras på [samApache Kafka .net-klient](https://github.com/confluentinc/confluent-kafka-dotnet), som är modifierat för användning med Event Hubs för Kafka.</p> | 
| [Java](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/java) | Den här snabb starten visar hur du skapar och ansluter till en Event Hubs Kafka-slutpunkt med ett exempel på en producent och konsument som är skriven i Java. |
| [Node.js](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/node) | <p>I den här snabb starten visas hur du skapar och ansluter till en Event Hubs Kafka-slutpunkt med ett exempel på en producent och konsument som är skriven i nod.</p><p>I det här exemplet används [rdkafka-](https://github.com/Blizzard/node-rdkafka) biblioteket. </p>| 
| [Python](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/python) | <p>I den här snabb starten visas hur du skapar och ansluter till en Event Hubs Kafka-slutpunkt med ett exempel på en producent och konsument som skrivits i python.</p><p>Det här exemplet baseras på [samApache Kafka python-klienten](https://github.com/confluentinc/confluent-kafka-python), modifierad för användning med Event Hubs för Kafka.</p>|
| [Kör](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go) | <p>Den här snabb starten visar hur du skapar och ansluter till en Event Hubs Kafka-slutpunkt med ett exempel på en producent och konsument som är skriven i fart.</p><p>Det här exemplet baseras på [samApache Kafka Golang-klienten](https://github.com/confluentinc/confluent-kafka-go), som är modifierad för användning med Event Hubs för Kafka.</p>| 
| [Sarama Kafka go](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/go-sarama-client) | Den här snabb starten visar hur du skapar och ansluter till en Event Hubs Kafka-slutpunkt med hjälp av ett exempel på en producent och konsument som är skriven i go med hjälp av [klient biblioteket Sarama Kafka](https://github.com/Shopify/sarama) . |
| [Kafka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafka-cli) | I den här snabb starten visas hur du skapar och ansluter till en Event Hubs Kafka-slutpunkt med hjälp av CLI som ingår i paketet med Apache Kafka-distributionen.| 
| [Kafkacat](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/quickstart/kafkacat) | kafkacat är en icke-JVM konsument och producent som baseras på librdkafka, populärt på grund av dess hastighet och små storlek. Den här snabb starten innehåller en exempel konfiguration och flera enkla exempel på kafkacat-kommandon. | 
 
### <a name="quickstarts-in-docs"></a>Snabb starter i dokument
Se snabb start: [data strömning med Event Hubs att använda Kafka-protokollet](event-hubs-quickstart-kafka-enabled-event-hubs.md) i den här innehålls uppsättningen, som innehåller stegvisa instruktioner för hur du strömmar till Event Hubs. Du lär dig hur du använder dina producenter och användare för att prata med Event Hubs med bara en konfigurations ändring i dina program. 


## <a name="tutorials"></a>Självstudier 

### <a name="tutorials-in-github"></a>Självstudier i GitHub
Se följande självstudier på GitHub:

| Självstudie | Description | 
| ------------------------- | ----------- | 
| [Akka](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/akka/java) | Den här självstudien visar hur du ansluter Akka-strömmar till Kafka-aktiverade Event Hubs utan att ändra protokoll klienter eller köra egna kluster. Det finns två separata självstudier med hjälp av **Java** -och **Scala** -programmeringsspråk. | 
| [Anslut](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/connect) | Det här dokumentet beskriver hur du integrerar Kafka med Azure Event Hubs och distribuerar grundläggande FileStreamSource-och FileStreamSink-kopplingar. Även om dessa anslutningar inte är avsedda för användning i produktion, visar de ett Kafka-scenario från slut punkt till slut punkt där Azure Event Hubs imiteras som en Kafka-Broker.| 
| [Filebeat](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/filebeat) | Det här dokumentet beskriver hur du integrerar Filebeat och Event Hubs via Filebeat Kafka-utdata. | 
| [Flink](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/flink) | I den här självstudien visas hur du ansluter Apache Flink till Kafka-aktiverade Event Hubs utan att ändra protokoll klienter eller köra egna kluster. | 
| [Fluent](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/fluentd) | Det här dokumentet beskriver hur du integrerar det och Event Hubs med hjälp av `out_kafka` plugin-programmet för utdata. |
| [Interop](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/interop) | Den här självstudien visar hur du utbyter händelser mellan konsumenter och producenter som använder olika protokoll. |
| [Logstash](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/logstash) | Den här självstudien vägleder dig genom integrering av Logstash med Kafka-aktiverade Event Hubs med hjälp av Logstash-Kafka för in-och utdata. | 
| [MirrorMaker](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/mirror-maker) | Den här självstudien visar hur en Event Hub-och Kafka-MirrorMaker kan integrera en befintlig Kafka-pipeline i Azure genom att spegla Kafka-indataströmmen i Event Hubss tjänsten. |
| [NiFi](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/nifi) | I den här självstudien visas hur du ansluter Apache NiFi till ett Event Hubs-namnområde. | 
| [Arbeta](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/oauth) | Snabb starter visar hur du skapar och ansluter till en Event Hubs Kafka-slutpunkt med ett exempel på en producent och konsument som skrivits i go och Java-programmeringsspråk. |
| [Registrerings schema register](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/schema-registry) | I den här självstudien får du stegvisa anvisningar genom integrering av schema register och Event Hubs för Kafka. | 
| [Spark](https://github.com/Azure/azure-event-hubs-for-kafka/tree/master/tutorials/spark) | I den här självstudien visas hur du ansluter Spark-programmet till en Event Hub utan att ändra protokoll klienter eller köra egna Kafka-kluster. | 

### <a name="tutorials-in-docs"></a>Självstudier i dokument
Se även självstudien: [Process Apache Kafka för Event Hubs händelser som använder Stream Analytics](event-hubs-kafka-stream-analytics.md) i den här innehålls uppsättningen, som visar hur data strömmas till Event Hubs och bearbetas med Azure Stream Analytics.

## <a name="how-to-guides"></a>Anvisningar
Se följande instruktions guider i vår dokumentation:

| Artikel | Description | 
| ------- | ----------- | 
| [Spegla en Kafka-broker i en händelsehubb](event-hubs-kafka-mirror-maker-tutorial.md) | Visar hur du speglar en Kafka-Broker i en Event Hub med hjälp av Kafka MirrorMaker. |
| [Ansluta Apache Spark till en händelsehubb](event-hubs-kafka-spark-tutorial.md) | Vägleder dig genom att ansluta Spark-programmet till Event Hubs för strömning i real tid. |
| [Ansluta Apache Flink till en händelsehubb](event-hubs-kafka-flink-tutorial.md) | Visar hur du ansluter Apache Flink till en Event Hub utan att ändra protokoll klienter eller köra egna kluster. |
| [Integrera Apache Kafka ansluta med en Event Hub (för hands version)](event-hubs-kafka-connect-tutorial.md) | Vägleder dig genom integreringen av Kafka ansluter till en Event Hub och distribuerar grundläggande FileStreamSource-och FileStreamSink-anslutningar. |
| [Ansluta Akka-dataströmmar till en händelsehubb](event-hubs-kafka-akka-streams-tutorial.md) | Visar hur du ansluter Akka strömmar till en Event Hub utan att ändra protokoll klienter eller köra egna kluster. |
| [Använd start Start programmet våren för Apache Kafka med Azure Event Hubs](/azure/developer/java/spring-framework/configure-spring-cloud-stream-binder-java-app-kafka-azure-event-hub) | Visar hur du konfigurerar en Java-baserad våren Cloud Stream-bindning som skapats med våren Boot-initieraren för att använda Apache Kafka med Azure Event Hubs. |

## <a name="next-steps"></a>Nästa steg
Granska exempel i mapparna GitHub lagrings platsen [Azure-Event-Hub-for-Kafka](https://github.com/Azure/azure-event-hubs-for-kafka) under snabb start och självstudier.

Se även följande artiklar:

- [Apache Kafka fel söknings guide för Event Hubs](apache-kafka-troubleshooting-guide.md)
- [Vanliga frågor och svar – Event Hubs för Apache Kafka](apache-kafka-frequently-asked-questions.yml)
- [Apache Kafka migrations guide för Event Hubs](apache-kafka-migration-guide.md)



