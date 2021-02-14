---
title: Använda Azure Service Bus köer med Java (Azure-Messaging-Service Bus)
description: I den här självstudien får du lära dig hur du använder Java för att skicka meddelanden till och ta emot meddelanden från en Azure Service Bus kö. Du använder det nya paketet Azure-Messaging-Service Bus.
ms.devlang: Java
ms.topic: quickstart
ms.date: 02/13/2021
ms.custom: seo-java-july2019, seo-java-august2019, seo-java-september2019, devx-track-java
ms.openlocfilehash: bfe7835bea4415085279fb77eb85d67ed3f5f0f3
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100518614"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-java"></a>Skicka meddelanden till och ta emot meddelanden från Azure Service Bus köer (Java)
I den här snabb starten ska du skapa en Java-app för att skicka meddelanden till och ta emot meddelanden från en Azure Service Bus kö. 

> [!IMPORTANT]
> I den här snabb starten används det nya paketet Azure-Messaging-Service Bus. En snabb start som använder det gamla Azure-Service Bus-paketet finns i [skicka och ta emot meddelanden med Azure-Service Bus](service-bus-java-how-to-use-queues-legacy.md).


## <a name="prerequisites"></a>Förutsättningar
- En Azure-prenumeration. Du behöver ett Azure-konto för att genomföra kursen. Du kan aktivera dina [förmåner för MSDN-prenumeranter](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) eller registrera dig för ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Om du inte har en kö att arbeta med följer du stegen i artikeln [använd Azure Portal för att Service Bus skapa](service-bus-quickstart-portal.md) en kö. Anteckna **anslutnings strängen** för Service Bus namn området och namnet på **kön** som du skapade.
- Installera [Azure SDK för Java][Azure SDK for Java]. Om du använder Sol förmörkelse kan du installera [Azure Toolkit for Eclipse][Azure Toolkit for Eclipse] som innehåller Azure SDK för Java. Du kan sedan lägga till **Microsoft Azure biblioteken för Java** i projektet. Om du använder IntelliJ, se [installera Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation). 


## <a name="send-messages-to-a-queue"></a>Skicka meddelanden till en kö
I det här avsnittet ska du skapa ett Java-konsol-projekt och lägga till kod för att skicka meddelanden till kön som du skapade tidigare. 

### <a name="create-a-java-console-project"></a>Skapa ett Java-konsol projekt
Skapa ett Java-projekt med hjälp av Sol förmörkelse eller ett verktyg som du själv väljer. 

### <a name="configure-your-application-to-use-service-bus"></a>Konfigurera programmet så att det använder Service Bus
Lägg till referenser till Azure Core-och Azure Service Bus-bibliotek. 

Om du använder Sol förmörkelse och skapat ett Java-konsol program konverterar du ditt Java-projekt till en maven: Högerklicka på projektet i fönstret **Package Explorer** och välj **Konfigurera**  ->  **konvertera till Maven-projekt**. Lägg sedan till beroenden till dessa två bibliotek som visas i följande exempel.

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
1. Lägg till följande- `import` instruktioner i avsnittet i Java-filen. 

    ```java
    import com.azure.messaging.servicebus.*;
    
    import java.util.concurrent.CountDownLatch;
    import java.util.concurrent.TimeUnit;
    import java.util.Arrays;
    import java.util.List;
    ```    
5. I-klassen definierar du variabler som ska innehålla anslutnings strängen och könamnet enligt nedan: 

    ```java
    static String connectionString = "<NAMESPACE CONNECTION STRING>";
    static String queueName = "<QUEUE NAME>";    
    ```

    Ersätt `<NAMESPACE CONNECTION STRING>` med anslutnings strängen till Service Bus namn området. Och Ersätt `<QUEUE NAME>` med namnet på kön.
3. Lägg till en metod som heter `sendMessage` i klassen för att skicka ett meddelande till kön. 

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
1. Lägg till en metod som heter `createMessages` i klassen för att skapa en lista med meddelanden. Normalt får du dessa meddelanden från olika delar av programmet. Här skapar vi en lista över exempel meddelanden.

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
1. Lägg till en metod med namnet `sendMessageBatch` metod för att skicka meddelanden till kön som du skapade. Den här metoden skapar en `ServiceBusSenderClient` för kön, anropar `createMessages` metoden för att hämta listan över meddelanden, förbereder en eller flera batchar och skickar batcharna till kön. 

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
I det här avsnittet ska du lägga till kod för att hämta meddelanden från kön. 

1. Lägg till en metod `receiveMessages` som heter för att ta emot meddelanden från kön. Den här metoden skapar en `ServiceBusProcessorClient` för kön genom att ange en hanterare för bearbetning av meddelanden och en annan för hantering av fel. Sedan startar den processorn, väntar några sekunder, skriver ut de meddelanden som tas emot och stoppar och stänger sedan processorn.

    > [!IMPORTANT]
    > Ersätt `QueueTest` i `QueueTest::processMessage` koden med namnet på klassen. 

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
2. Lägg till `processMessage` metoden för att bearbeta ett meddelande som tagits emot från Service Bus prenumerationen. 

    ```java
    private static void processMessage(ServiceBusReceivedMessageContext context) {
        ServiceBusReceivedMessage message = context.getMessage();
        System.out.printf("Processing message. Session: %s, Sequence #: %s. Contents: %s%n", message.getMessageId(),
            message.getSequenceNumber(), message.getBody());
    }    
    ```
3. Lägg till- `processError` metoden för att hantera fel meddelanden.

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
2. Uppdatera `main` metoden för att anropa `sendMessage` -, `sendMessageBatch` -och- `receiveMessages` metoder och att utlösa `InterruptedException` .     

    ```java
    public static void main(String[] args) throws InterruptedException {        
        sendMessage();
        sendMessageBatch();
        receiveMessages();
    }   
    ```

## <a name="run-the-app"></a>Kör appen
När du kör programmet visas följande meddelanden i konsol fönstret. 

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

På sidan **Översikt** för Service Bus namn området i Azure Portal kan du se antalet **inkommande** och **utgående** meddelanden. Du kan behöva vänta en minut eller så och sedan uppdatera sidan för att se de senaste värdena. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Antal inkommande och utgående meddelanden" lightbox="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png":::

Välj kön på den här **översikts** sidan för att gå till sidan **Service Bus kö** . Du ser det **inkommande** och **utgående** meddelande antalet på den här sidan. Du kan också se annan information, till exempel köns **aktuella storlek** , **maximal storlek**, **Antal aktiva meddelanden** och så vidare. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/queue-details.png" alt-text="Information om kö" lightbox="./media/service-bus-java-how-to-use-queues/queue-details.png":::



## <a name="next-steps"></a>Nästa steg
Se följande dokumentation och exempel:

- [Azure Service Bus klient bibliotek för Java – viktigt](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/README.md)
- [Exempel på GitHub](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Referens för Java-API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-messaging-servicebus/7.0.0/index.html)

[Azure SDK for Java]: /azure/developer/java/sdk/java-sdk-azure-get-started
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage