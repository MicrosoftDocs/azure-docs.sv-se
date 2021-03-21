---
title: Använd API för Java Message Service 2,0 med Azure Service Bus Premium
description: Så här använder du JMS (Java Message Service) med Azure Service Bus
ms.topic: article
ms.date: 07/17/2020
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019
ms.openlocfilehash: 894821444f74248b73578595df943cb3a0025360
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101698407"
---
# <a name="use-java-message-service-20-api-with-azure-service-bus-premium"></a>Använd API för Java Message Service 2,0 med Azure Service Bus Premium

Den här artikeln beskriver hur du använder JMS-API: t **(populära Java Message Service) 2,0** för att interagera med Azure Service Bus över protokollet Advanced Message Queueing Protocol (AMQP 1,0).

> [!NOTE]
> Stöd för Java Message Service (JMS) 2,0 API finns bara på **Azure Service Bus Premium-nivån**.
>

## <a name="pre-requisites"></a>Förutsättningar

### <a name="get-started-with-service-bus"></a>Kom igång med Service Bus

Den här guiden förutsätter att du redan har ett Service Bus-namnområde. Om du inte gör det kan du [skapa namn området och kön](service-bus-create-namespace-portal.md) med hjälp av [Azure Portal](https://portal.azure.com). 

Mer information om hur du skapar Service Bus namnrymder och köer finns i [komma igång med Service Bus köer via Azure Portal](service-bus-quickstart-portal.md).

### <a name="set-up-a-java-development-environment"></a>Konfigurera en Java-utvecklings miljö

För att utveckla Java-program måste du konfigurera lämplig utvecklings miljö – 
   * Antingen JDK (Java Development Kit) eller JRE (Java Runtime Environment) har installerats.
   * JDK eller JRE läggs till i build-sökvägen och lämpliga systemvariabler.
   * En Java IDE installeras för att använda JDK eller JRE. Till exempel Sol förmörkelse eller IntelliJ.

Om du vill veta mer om hur du förbereder din utvecklings miljö för Java på Azure kan du använda [den här guiden](/azure/developer/java/fundamentals/).

## <a name="what-jms-features-are-supported"></a>Vilka JMS-funktioner stöds?

[!INCLUDE [service-bus-jms-features-list](../../includes/service-bus-jms-feature-list.md)]

## <a name="downloading-the-java-message-service-jms-client-library"></a>Hämta klient biblioteket Java Message Service (JMS)

Om du vill använda alla funktioner som är tillgängliga på Azure Service Bus Premium-nivån måste du lägga till följande bibliotek i projektets build-sökväg.

[Azure-Service Bus – JMS](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms)

> [!NOTE]
> Om du vill lägga till [Azure-Service Bus-JMS](https://search.maven.org/artifact/com.microsoft.azure/azure-servicebus-jms) till build-sökvägen använder du det prioriterade beroende hanterings verktyget för ditt projekt som [maven](https://maven.apache.org/) eller [Gradle](https://gradle.org/).
>

## <a name="coding-java-applications"></a>Koda Java-program

När beroenden har importer ATS kan Java-programmen skrivas i en JMS-Provider oberoende-metod.

### <a name="connecting-to-azure-service-bus-using-jms"></a>Ansluta till Azure Service Bus med JMS

Om du vill ansluta med Azure Service Bus med JMS-klienter behöver du **anslutnings strängen** som är tillgänglig i "Shared Access policies" i [Azure Portal](https://portal.azure.com) under **primär anslutnings sträng**.

1. Instansiera `ServiceBusJmsConnectionFactorySettings`

    ```java
    ServiceBusJmsConnectionFactorySettings connFactorySettings = new ServiceBusJmsConnectionFactorySettings();
    connFactorySettings.setConnectionIdleTimeoutMS(20000);
    ```
2. Instansiera `ServiceBusJmsConnectionFactory` med lämplig `ServiceBusConnectionString` .

    ```java
    String ServiceBusConnectionString = "<SERVICE_BUS_CONNECTION_STRING_WITH_MANAGE_PERMISSIONS>";
    ConnectionFactory factory = new ServiceBusJmsConnectionFactory(ServiceBusConnectionString, connFactorySettings);
    ```

3. Använd `ConnectionFactory` för att antingen skapa en `Connection` och sedan en `Session` 

    ```java
    Connection connection = factory.createConnection();
    Session session = connection.createSession();
    ```
    eller en `JMSContext` (för JMS 2,0-klienter)

    ```java
    JMSContext jmsContext = factory.createContext();
    ```

    >[!IMPORTANT]
    > Även om det finns på samma sätt som Named, är JMS session och Service Bus session helt oberoende av varandra.
    >
    > I JMS 1,1 är session ett viktigt Bygg block av API: et som gör det möjligt att skapa MessageProducer, MessageConsumer och själva meddelandet. Mer information finns i [programmerings modellen för API för JMS](https://docs.oracle.com/javaee/6/tutorial/doc/bnceh.html)
    >
    > I Service Bus är [sessioner](message-sessions.md) tjänst-och klient sidans konstruktion för att aktivera FIFO-bearbetning på köer och prenumerationer.
    >

### <a name="write-the-jms-application"></a>Skriv JMS-programmet

När `Session` eller `JMSContext` har instansierats kan programmet använda välbekanta JMS-API: er för att utföra både hanterings-och data åtgärder.

Se listan över [JMS funktioner som stöds](how-to-use-java-message-service-20.md#what-jms-features-are-supported) för att se vilka API: er som stöds.

Nedan visas några exempel kod avsnitt som hjälper dig att komma igång med JMS-

#### <a name="sending-messages-to-a-queue-and-topic"></a>Skicka meddelanden till en kö och ämne

```java
// Create the queue and topic
Queue queue = jmsContext.createQueue("basicQueue");
Topic topic = jmsContext.createTopic("basicTopic");
// Create the message
Message msg = jmsContext.createMessage();

// Create the JMS message producer
JMSProducer producer = jmsContext.createProducer();

// send the message to the queue
producer.send(queue, msg);
// send the message to the topic
producer.send(topic, msg);
```

#### <a name="receiving-messages-from-a-queue"></a>Ta emot meddelanden från en kö

```java
// Create the queue
Queue queue = jmsContext.createQueue("basicQueue");

// Create the message consumer
JMSConsumer consumer = jmsContext.createConsumer(queue);

// Receive the message
Message msg = (Message) consumer.receive();
```

#### <a name="receiving-messages-from-a-shared-durable-subscription-on-a-topic"></a>Ta emot meddelanden från en delad varaktig prenumeration på ett ämne

```java
// Create the topic
Topic topic = jmsContext.createTopic("basicTopic");

// Create a shared durable subscriber on the topic
JMSConsumer sharedDurableConsumer = jmsContext.createSharedDurableConsumer(topic, "sharedDurableConsumer");

// Receive the message
Message msg = (Message) sharedDurableConsumer.receive();
```

## <a name="summary"></a>Sammanfattning

Den här guiden demonstrerade hur Java-klientprogram som använder Java Message Service (JMS) över AMQP 1,0 kan interagera med Azure Service Bus.

Du kan också använda Service Bus AMQP 1,0 från andra språk, inklusive .NET, C, python och PHP. Komponenter som skapats med dessa olika språk kan utbyta meddelanden på ett tillförlitligt sätt och med fullständig åter givning med AMQP 1,0-stödet i Service Bus.

## <a name="next-steps"></a>Nästa steg

Mer information om Azure Service Bus och information om JMS-enheter (Java Message Service) finns i länkarna nedan – 
* [Service Bus-köer, ämnen och prenumerationer](service-bus-queues-topics-subscriptions.md)
* [Service Bus-Java Message Service-entiteter](service-bus-queues-topics-subscriptions.md#java-message-service-jms-20-entities)
* [AMQP 1,0-stöd i Azure Service Bus](service-bus-amqp-overview.md)
* [Service Bus AMQP 1,0 Developer ' s guide](service-bus-amqp-dotnet.md)
* [Komma igång med Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* [API för Java Message Service (externt Oracle-dokument)](https://docs.oracle.com/javaee/7/api/javax/jms/package-summary.html)
* [Lär dig hur du migrerar från ActiveMQ till Service Bus](migrate-jms-activemq-to-servicebus.md)