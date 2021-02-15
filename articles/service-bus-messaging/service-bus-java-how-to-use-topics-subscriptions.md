---
title: Använda Azure Service Bus ämnen och prenumerationer med Java (Azure-Messaging-Service Bus)
description: I den här snabb starten skriver du Java-kod med Azure-Messaging-Service Bus-paketet för att skicka meddelanden till ett Azure Service Bus ämne och sedan ta emot meddelanden från prenumerationer till det avsnittet.
ms.devlang: Java
ms.topic: quickstart
ms.date: 02/13/2021
ms.openlocfilehash: c5b930fb2c87a09a1f4801365936c62a7cf79f1d
ms.sourcegitcommit: e972837797dbad9dbaa01df93abd745cb357cde1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100516183"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-java"></a>Skicka meddelanden till ett Azure Service Bus ämne och ta emot meddelanden från prenumerationer till ämnet (Java)
I den här snabb starten skriver du Java-kod med Azure-Messaging-Service Bus-paketet för att skicka meddelanden till ett Azure Service Bus ämne och sedan ta emot meddelanden från prenumerationer till det avsnittet.

> [!IMPORTANT]
> I den här snabb starten används det nya paketet Azure-Messaging-Service Bus. En snabb start som använder det gamla Azure-Service Bus-paketet finns i [skicka och ta emot meddelanden med Azure-Service Bus](service-bus-java-how-to-use-topics-subscriptions-legacy.md).

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Du behöver ett Azure-konto för att genomföra kursen. Du kan aktivera dina [förmåner för Visual Studio eller MSDN-prenumeranter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) eller registrera dig för ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Följ stegen i [snabb starten: använd Azure Portal för att skapa ett Service Bus ämne och prenumerationer på avsnittet](service-bus-quickstart-topics-subscriptions-portal.md). Anteckna anslutnings strängen, ämnes namnet och ett prenumerations namn. Du kommer bara att använda en prenumeration för den här snabb starten. 
- Installera [Azure SDK för Java][Azure SDK for Java]. Om du använder Sol förmörkelse kan du installera [Azure Toolkit for Eclipse][Azure Toolkit for Eclipse] som innehåller Azure SDK för Java. Du kan sedan lägga till **Microsoft Azure biblioteken för Java** i projektet. Om du använder IntelliJ, se [installera Azure Toolkit for IntelliJ](/azure/developer/java/toolkit-for-intellij/installation). 


## <a name="send-messages-to-a-topic"></a>Skicka meddelanden till ett ämne
I det här avsnittet ska du skapa ett Java-konsol-projekt och lägga till kod för att skicka meddelanden till ämnet som du har skapat. 

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

### <a name="add-code-to-send-messages-to-the-topic"></a>Lägg till kod för att skicka meddelanden till ämnet
1. Lägg till följande- `import` instruktioner i avsnittet i Java-filen. 

    ```java
    import com.azure.messaging.servicebus.*;
    
    import java.util.concurrent.CountDownLatch;
    import java.util.concurrent.TimeUnit;
    import java.util.Arrays;
    import java.util.List;
    ```    
5. I-klassen definierar du variabler som ska innehålla anslutnings strängen och ämnes namnet enligt nedan: 

    ```java
    static String connectionString = "<NAMESPACE CONNECTION STRING>";
    static String topicName = "<TOPIC NAME>";    
    static String subName = "<SUBSCRIPTION NAME>";
    ```

    Ersätt `<NAMESPACE CONNECTION STRING>` med anslutnings strängen till Service Bus namn området. Och Ersätt `<TOPIC NAME>` med namnet på ämnet.
3. Lägg till en metod som heter `sendMessage` i klassen för att skicka ett meddelande till ämnet. 

    ```java
    static void sendMessage()
    {
        // create a Service Bus Sender client for the queue 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .topicName(topicName)
                .buildClient();
        
        // send one message to the topic
        senderClient.sendMessage(new ServiceBusMessage("Hello, World!"));
        System.out.println("Sent a single message to the topic: " + topicName);        
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
1. Lägg till en metod med namnet `sendMessageBatch` metod för att skicka meddelanden till ämnet som du skapade. Den här metoden skapar en `ServiceBusSenderClient` för avsnittet och anropar `createMessages` metoden för att hämta listan över meddelanden, förbereder en eller flera batchar och skickar batcharna till ämnet. 

    ```java
    static void sendMessageBatch()
    {
        // create a Service Bus Sender client for the topic 
        ServiceBusSenderClient senderClient = new ServiceBusClientBuilder()
                .connectionString(connectionString)
                .sender()
                .topicName(topicName)
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
            System.out.println("Sent a batch of messages to the topic: " + topicName);
            
            // create a new batch
            messageBatch = senderClient.createMessageBatch();

            // Add that message that we couldn't before.
            if (!messageBatch.tryAddMessage(message)) {
                System.err.printf("Message is too large for an empty batch. Skipping. Max size: %s.", messageBatch.getMaxSizeInBytes());
            }
        }

        if (messageBatch.getCount() > 0) {
            senderClient.sendMessages(messageBatch);
            System.out.println("Sent a batch of messages to the topic: " + topicName);
        }

        //close the client
        senderClient.close();
    }
    ```

## <a name="receive-messages-from-a-subscription"></a>Ta emot meddelanden från en prenumeration
I det här avsnittet ska du lägga till kod för att hämta meddelanden från en prenumeration till ämnet. 

1. Lägg till en metod `receiveMessages` som heter för att ta emot meddelanden från prenumerationen. Den här metoden skapar en `ServiceBusProcessorClient` för prenumerationen genom att ange en hanterare för bearbetning av meddelanden och en annan för hantering av fel. Sedan startar den processorn, väntar några sekunder, skriver ut de meddelanden som tas emot och stoppar och stänger sedan processorn.

    > [!IMPORTANT]
    > Ersätt `ServiceBusTopicTest` i `ServiceBusTopicTest::processMessage` koden med namnet på klassen. 

    ```java
    // handles received messages
    static void receiveMessages() throws InterruptedException
    {
        CountDownLatch countdownLatch = new CountDownLatch(1);

        // Create an instance of the processor through the ServiceBusClientBuilder
        ServiceBusProcessorClient processorClient = new ServiceBusClientBuilder()
            .connectionString(connectionString)
            .processor()
            .topicName(topicName)
            .subscriptionName(subName)
            .processMessage(ServiceBusTopicTest::processMessage)
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
1. Uppdatera `main` metoden för att anropa `sendMessage` -, `sendMessageBatch` -och- `receiveMessages` metoder och att utlösa `InterruptedException` .     

    ```java
    public static void main(String[] args) throws InterruptedException {        
        sendMessage();
        sendMessageBatch();
        receiveMessages();
    }   
    ```

## <a name="run-the-app"></a>Kör appen
Kör programmet för att se utdata som liknar följande utdata:

```console
Sent a single message to the topic: mytopic
Sent a batch of messages to the topic: mytopic
Starting the processor
Processing message. Session: e0102f5fbaf646988a2f4b65f7d32385, Sequence #: 1. Contents: Hello, World!
Processing message. Session: 3e991e232ca248f2bc332caa8034bed9, Sequence #: 2. Contents: First message
Processing message. Session: 56d3a9ea7df446f8a2944ee72cca4ea0, Sequence #: 3. Contents: Second message
Processing message. Session: 7bd3bd3e966a40ebbc9b29b082da14bb, Sequence #: 4. Contents: Third message
```

På sidan **Översikt** för Service Bus namn området i Azure Portal kan du se antalet **inkommande** och **utgående** meddelanden. Du kan behöva vänta en minut eller så och sedan uppdatera sidan för att se de senaste värdena. 

:::image type="content" source="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png" alt-text="Antal inkommande och utgående meddelanden" lightbox="./media/service-bus-java-how-to-use-queues/overview-incoming-outgoing-messages.png":::

Växla till fliken **ämnen** i det mittersta fönstret och markera avsnittet om du vill se sidan **Service Bus ämne** för ditt ämne. På den här sidan bör du se fyra inkommande och fyra utgående meddelanden i **meddelande** diagrammet. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png" alt-text="Inkommande och utgående meddelanden" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/topic-page-portal.png":::

Om du kommenterar ut `receiveMessages` anropet i- `main` metoden och kör appen igen på sidan **Service Bus ämne** visas 8 inkommande meddelanden (4 nya), men fyra utgående meddelanden. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png" alt-text="Uppdaterad ämnes sida" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/updated-topic-page.png":::

Om du väljer en prenumeration på den här sidan kommer du till sidan **Service Bus prenumeration** . Du kan se antalet aktiva meddelanden, antal meddelanden om obeställbara meddelanden och mer på den här sidan. I det här exemplet finns det fyra aktiva meddelanden som inte har tagits emot av en mottagare än. 

:::image type="content" source="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png" alt-text="Antal aktiva meddelanden" lightbox="./media/service-bus-java-how-to-use-topics-subscriptions/active-message-count.png":::

## <a name="next-steps"></a>Nästa steg
Se följande dokumentation och exempel:

- [Azure Service Bus klient bibliotek för Java – viktigt](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/servicebus/azure-messaging-servicebus/README.md)
- [Exempel på GitHub](/samples/azure/azure-sdk-for-java/servicebus-samples/)
- [Referens för Java-API](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-messaging-servicebus/7.0.0/index.html)


[Azure SDK for Java]: /java/api/overview/azure/
[Azure Toolkit for Eclipse]: /azure/developer/java/toolkit-for-eclipse/installation
[Service Bus queues, topics, and subscriptions]: service-bus-queues-topics-subscriptions.md
[SqlFilter]: /dotnet/api/microsoft.azure.servicebus.sqlfilter
[SqlFilter.SqlExpression]: /dotnet/api/microsoft.azure.servicebus.sqlfilter.sqlexpression
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage