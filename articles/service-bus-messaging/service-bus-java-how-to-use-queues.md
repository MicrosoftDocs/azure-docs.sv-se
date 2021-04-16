---
title: Använda Azure Service Bus köer med Java (azure-messaging-servicebus)
description: I den här självstudien får du lära dig hur du använder Java för att skicka meddelanden till och ta emot meddelanden från en Azure Service Bus kö. Du använder det nya paketet azure-messaging-servicebus.
ms.date: 02/13/2021
ms.topic: quickstart
ms.devlang: Java
ms.custom:
- seo-java-july2019
- seo-java-august2019
- seo-java-september2019
- devx-track-java
- mode-api
ms.openlocfilehash: e3998e812bb921ff8bea5e83199bc4e142ed2e83
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107533425"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-java"></a>Skicka meddelanden till och ta emot meddelanden Azure Service Bus köer (Java)
I den här snabbstarten skapar du en Java-app för att skicka meddelanden till och ta emot meddelanden från en Azure Service Bus kö. 

> [!IMPORTANT]
> I den här snabbstarten används det nya paketet azure-messaging-servicebus. En snabbstart som använder det gamla azure-servicebus-paketet finns i [Skicka och ta emot meddelanden med azure-servicebus](service-bus-java-how-to-use-queues-legacy.md).


## <a name="prerequisites"></a>Förutsättningar
- En Azure-prenumeration. Du behöver ett Azure-konto för att genomföra kursen. Du kan aktivera dina [MSDN-prenumerantförmåner](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) eller registrera dig för ett [kostnadsfritt konto.](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF)
- Om du inte har någon kö att arbeta med följer du stegen i artikeln Använda Azure Portal för att skapa [en Service Bus för](service-bus-quickstart-portal.md) att skapa en kö. Anteckna **anslutningssträngen** för Service Bus namnområdet och namnet på den **kö som** du skapade.
- Installera [Azure SDK för Java][Azure SDK for Java]. Om du använder Eclipse kan du installera den [Azure Toolkit for Eclipse][Azure Toolkit for Eclipse] som innehåller Azure SDK för Java. Du kan sedan lägga till **Microsoft Azure-bibliotek för Java** i projektet. Om du använder IntelliJ kan du se [Installera Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation). 


## <a name="send-messages-to-a-queue"></a>Skicka meddelanden till en kö
I det här avsnittet skapar du ett Java-konsolprojekt och lägger till kod för att skicka meddelanden till kön som du skapade tidigare. 

### <a name="create-a-java-console-project"></a>Skapa ett Java-konsolprojekt
Skapa ett Java-projekt med Eclipse eller ett val av verktyg. 

### <a name="configure-your-application-to-use-service-bus"></a>Konfigurera ditt program att använda Service Bus
Lägg till referenser till Azure Core och Azure Service Bus bibliotek. 

Om du använder Eclipse och har skapat ett Java-konsolprogram konverterar du Ditt Java-projekt till maven: högerklicka på projektet i **fönstret Package Explorer** och välj **Konfigurera** Konvertera  ->  **till Maven-projekt**. Lägg sedan till beroenden till dessa två bibliotek enligt följande exempel.

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 https://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>
    <groupId>org.myorg.sbusquickstarts</groupId>
    <artifactId>sbustopicqs</artifactId>
    <version>0.0.1-SNAPSHOT</version>
    <build>
        <sourceDirectory>src</sourceDirectory>
        <plugins>
            <plugin>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>3.8.1</version>
                <configuration>
                    <release>15</release>
                </configuration>
            </plugin>
        </plugins>
    </build>
    <dependencies>
        <dependency>
            <groupId>com.azure</groupId>
            <artifactId>azure-core</artifactId>
            <version>1.13.0</version>
        </dependency>
        <dependency>
            <groupId>com.azure</groupId>
            <artifactId>azure-messaging-servicebus</artifactId>
            <version>7.0.2</version>
        </dependency>
    </dependencies>
</project>
```

### <a name="add-code-to-send-messages-to-the-queue"></a>Lägga till kod för att skicka meddelanden till kön
1. Lägg till följande `import` -instruktioner i java-filens ämne. 

    ```java
    import com.azure.messaging.servicebus.*;
    
    import java.util.concurrent.CountDownLatch;
    import java.util.concurrent.TimeUnit;
    import java.util.Arrays;
    import java.util.List;
    ```    
5. I klassen definierar du variabler som ska innehålla anslutningssträngen och könamnet enligt nedan: 

    ```java
    static String connectionString = "<NAMESPACE CONNECTION STRING>";
    static String queueName = "<QUEUE NAME>";    
    ```

    Ersätt `<NAMESPACE CONNECTION STRING>` med anslutningssträngen till Service Bus namnområdet. Och ersätt `<QUEUE NAME>` med namnet på kön.
3. Lägg till en metod `sendMessage` med namnet i klassen för att skicka ett meddelande till kön. 

    ```java
    static void sendMessage()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .queueName(queueName)
                .buildClient();
        
        // send one message to the queue
        senderClient.sendMessage(new ServiceBusMessage("Hello, World!"));
        System.out.println("Sent a single message to the queue: " + queueName);        
    }
    ```
1. Lägg till en metod `createMessages` med namnet i klassen för att skapa en lista med meddelanden. Vanligtvis får du dessa meddelanden från olika delar av ditt program. Här skapar vi en lista över exempelmeddelanden.

    ```java
    static List<ServiceBusMessage> createMessages()
    {
        // create a list of messages and return it to the caller
        ServiceBusMessage[] messages = {
                new ServiceBusMessage("First message"),
                new ServiceBusMessage("Second message"),
                new ServiceBusMessage("Third message")
        };
        return Arrays.asList(messages);
    }
    ```
1. Lägg till en metod med `sendMessageBatch` namnet method för att skicka meddelanden till kön som du skapade. Den här metoden skapar en för kön, anropar metoden för att hämta listan över meddelanden, förbereder en eller flera batchar och skickar `ServiceBusSenderClient` `createMessages` batcharna till kön. 

    ```java
    static void sendMessageBatch()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .queueName(queueName)
                .buildClient();

        // Creates an ServiceBusMessageBatch where the ServiceBus.
        ServiceBusMessageBatch messageBatch = senderClient.createMessageBatch();        
        
        // create a list of messages
        List<ServiceBusMessage> listOfMessages = createMessages();
        
        // We try to add as many messages as a batch can fit based on the maximum size and send to Service Bus when
        // the batch can hold no more messages. Create a new batch for next set of messages and repeat until all
        // messages are sent.        
        for (ServiceBusMessage message : listOfMessages) {
            if (messageBatch.tryAddMessage(message)) {
                continue;
            }

            // The batch is full, so we create a new batch and send the batch.
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the queue: " + queueName);
            
            // create a new batch
            messageBatch = senderClient.createMessageBatch();

            // Add that message that we couldn't before.
            if (!messageBatch.tryAddMessage(message)) {
                System.err.printf("Message is too large for an empty batch. Skipping. Max size: %s.", messageBatch.getMaxSizeInBytes());
            }
        }
        
        if (messageBatch.getCount() > 0) {
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the queue: " + queueName);
        }

        //close the client
        senderClient.close();
    }
    ```

## <a name="receive-messages-from-a-queue"></a>Ta emot meddelanden från en kö
I det här avsnittet lägger du till kod för att hämta meddelanden från kön. 

1. Lägg till en metod med `receiveMessages` namnet för att ta emot meddelanden från kön. Den här metoden skapar `ServiceBusProcessorClient` en för kön genom att ange en hanterare för bearbetning av meddelanden och en annan för hantering av fel. Sedan startar den processorn, väntar i några sekunder, skriver ut de meddelanden som tas emot och stoppar och stänger sedan processorn.

    > [!IMPORTANT]
    > Ersätt `QueueTest` `QueueTest::processMessage` i i koden med namnet på din klass. 

    ```java
    // handles received messages
    static void receiveMessages() throws InterruptedException
    {
        CountDownLatch countdownLatch = new CountDownLatch(1);

        // Create an instance of the processor through the ServiceBusClientBuilder
        ServiceBusProcessorClient processorClient = new ServiceBusClientBuilder()
            .connectionString(connectionString)
            .processor()
            .queueName(queueName)
            .processMessage(QueueTest::processMessage)
            .processError(context -> processError(context, countdownLatch))
            .buildProcessorClient();

        System.out.println("Starting the processor");
        processorClient.start();

        TimeUnit.SECONDS.sleep(10);
        System.out.println("Stopping and closing the processor");
        processorClient.close();        
    }   
    ```
2. Lägg till `processMessage` metoden för att bearbeta ett meddelande som tas emot från Service Bus prenumerationen. 

    ```java
    private static void processMessage(ServiceBusReceivedMessageContext context) {
        ServiceBusReceivedMessage message = context.getMessage();
        System.out.printf("Processing message. Session: %s, Sequence #: %s. Contents: %s%n", message.getMessageId(),
            message.getSequenceNumber(), message.getBody());
    }    
    ```
3. Lägg till `processError` metoden för att hantera felmeddelanden.

    ```java
    private static void processError(ServiceBusErrorContext context, CountDownLatch countdownLatch) {
        System.out.printf("Error when receiving messages from namespace: '%s'. Entity: '%s'%n",
            context.getFullyQualifiedNamespace(), context.getEntityPath());

        if (!(context.getException() instanceof ServiceBusException)) {
            System.out.printf("Non-ServiceBusException occurred: %s%n", context.getException());
            return;
        }

        ServiceBusException exception = (ServiceBusException) context.getException();
        ServiceBusFailureReason reason = exception.getReason();

        if (reason == ServiceBusFailureReason.MESSAGING_ENTITY_DISABLED
            || reason == ServiceBusFailureReason.MESSAGING_ENTITY_NOT_FOUND
            || reason == ServiceBusFailureReason.UNAUTHORIZED) {
            System.out.printf("An unrecoverable error occurred. Stopping processing with reason %s: %s%n",
                reason, exception.getMessage());

            countdownLatch.countDown();
        } else if (reason == ServiceBusFailureReason.MESSAGE_LOCK_LOST) {
            System.out.printf("Message lock lost for message: %s%n", context.getException());
        } else if (reason == ServiceBusFailureReason.SERVICE_BUSY) {
            try {
                // Choosing an arbitrary amount of time to wait until trying again.
                TimeUnit.SECONDS.sleep(1);
            } catch (InterruptedException e) {
                System.err.println("Unable to sleep for period of time");
            }
        } else {
            System.out.printf("Error source %s, reason %s, message: %s%n", context.getErrorSource(),
                reason, context.getException());
        }
    }  
    ```
2. Uppdatera metoden `main` för att anropa metoderna , och och för att skapa `sendMessage` `sendMessageBatch` `receiveMessages` `InterruptedException` .     

    ```java
    public static void main(String[] args) throws InterruptedException {        
        sendMessage();
        sendMessageBatch();
        receiveMessages();
    }   
    ```

## <a name="run-the-app"></a>Kör appen
När du kör programmet visas följande meddelanden i konsolfönstret. 

```console
Sent a single message to the queue: myqueue
Sent a batch of messages to the queue: myqueue
Starting the processor
Processing message. Session: 88d961dd801f449e9c3e0f8a5393a527, Sequence #: 1. Contents: Hello, World!
Processing message. Session: e90c8d9039ce403bbe1d0ec7038033a0, Sequence #: 2. Contents: First message
Processing message. Session: 311a216a560c47d184f9831984e6ac1d, Sequence #: 3. Contents: Second message
Processing message. Session: f9a871be07414baf9505f2c3d466c4ab, Sequence #: 4. Contents: Third message
Stopping and closing the processor
```

På sidan **Översikt** för den Service Bus namnrymden i Azure Portal kan du se antal **inkommande** och **utgående** meddelanden. Du kan behöva vänta i någon minut och sedan uppdatera sidan för att se de senaste värdena. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Antal inkommande och utgående meddelanden" lightbox="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png":::

Välj kön på den här **översiktssidan** för att gå Service Bus **sidan** Kö. Du ser även **antalet inkommande** **och** utgående meddelanden på den här sidan. Du kan också se annan information, till **exempel den aktuella** storleken på kön, maximal **storlek,** **antal aktiva** meddelanden och så vidare. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/queue-details.png" alt-text="Köinformation" lightbox="./media/service-bus-java-how-to-use-queues/queue-details.png":::



## <a name="next-steps"></a>Nästa steg
Se följande dokumentation och exempel:

- [Azure Service Bus klientbibliotek för Java – Viktigt](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/README.md)
- [Exempel på GitHub](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Referens för Java-API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-messaging-servicebus/7.0.0/index.html)

[Azure SDK for Java]: /azure/developer/java/sdk/get-started
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
