---
title: Skicka meddelanden till Azure Service Bus ämnen med Azure-Messaging – Service Bus
description: Den här snabb starten visar hur du skickar meddelanden till Azure Service Bus ämnen med hjälp av Azure-Messaging-Service Bus-paketet.
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 03/16/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 7b313caf6709429de9e0dcac219a4180c7391cf7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104607624"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-net"></a>Skicka meddelanden till ett Azure Service Bus ämne och ta emot meddelanden från prenumerationer på ämnet (.NET)
I den här självstudien skapar du ett C#-program för att utföra följande uppgifter:

1. Skicka meddelanden till ett Service Bus ämne. 

    Ett Service Bus ämne innehåller en slut punkt för avsändarens program för att skicka meddelanden. Ett ämne kan ha en eller flera prenumerationer. Varje prenumeration på ett ämne får en kopia av meddelandet som skickas till ämnet. Mer information om Service Bus ämnen finns i [Azure Service Bus?](service-bus-messaging-overview.md). 
1. Ta emot meddelanden från en prenumeration på ämnet. 

    :::image type="content" source="./media/service-bus-messaging-overview/about-service-bus-topic.png" alt-text="Service Bus-ämnen och prenumerationer":::

    > [!Important]
    > Den här snabb starten använder det nya **Azure. Messaging. Service Bus** -paketet. Om du använder det gamla Microsoft. Azure. Service Bus-paketet kan du läsa [skicka och ta emot meddelanden med hjälp av Microsoft. Azure. Service Bus-paketet](service-bus-dotnet-how-to-use-topics-subscriptions-legacy.md).

## <a name="prerequisites"></a>Förutsättningar

- En Azure-prenumeration. Du behöver ett Azure-konto för att genomföra kursen. Du kan aktivera dina [förmåner för Visual Studio eller MSDN-prenumeranter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) eller registrera dig för ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Följ stegen i den här [snabb](service-bus-quickstart-topics-subscriptions-portal.md) starten för att skapa ett Service Bus ämne och prenumerationer på avsnittet. 

    > [!NOTE]
    > Du kommer att använda anslutnings strängen för namn området, ämnes namnet och namnet på en av prenumerationerna på ämnet i den här självstudien.  
- [Visual Studio 2019](https://www.visualstudio.com/vs). 
 
## <a name="send-messages-to-a-topic"></a>Skicka meddelanden till ett ämne
I det här avsnittet ska du skapa ett .NET Core-konsolprogram i Visual Studio, lägga till kod för att skicka meddelanden till den Service Bus avsnittet som du skapade. 

### <a name="create-a-console-application"></a>Skapa ett konsolprogram
Skapa ett .NET Core-konsolprogram med Visual Studio. 

1. Starta Visual Studio.  
1. Om du ser sidan **Kom igång** väljer du **skapa ett nytt projekt**. 
1. Följ dessa steg på sidan **skapa ett nytt projekt** : 
    1. För programmeringsspråk väljer du **C#**. 
    1. För projekt typen väljer du **konsol**. 
    1. Välj **konsol program (.net Core)** i listan med mallar. 
    1. Välj **Nästa**. 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-console-project.png" alt-text="Skapa ett konsol program projekt":::
1. Följ dessa steg på sidan **Konfigurera ditt nya projekt** : 
    1. Ange ett namn för projektet för **projekt namn**. 
    1. För **plats** väljer du en plats för projekt-och lösningsfiler. 
    1. Ange ett namn för lösningen för **lösningens namn**. En Visual Studio-lösning kan ha ett eller flera projekt. I den här snabb starten kommer lösningen bara att ha ett projekt. 
    1. Välj **Skapa** för att skapa projektet. 
            
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/create-console-project-2.png" alt-text="Ange namn och plats för projektet och lösningen":::    


### <a name="add-the-service-bus-nuget-package"></a>Lägga till Service Bus-NuGet-paketet
1. Högerklicka på det nyskapade projektet och välj **Hantera Nuget-paket**.

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/manage-nuget-packages-menu.png" alt-text="Menyn Hantera NuGet-paket":::        
1. Växla till fliken **Bläddra** .
1. Sök efter och välj **[Azure. Messaging. Service Bus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/)**.
1. Välj **Installera** för att slutföra installationen.

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/select-service-bus-package.png" alt-text="Välj Service Bus NuGet-paket.":::
5. Om du ser dialog rutan för **förhands gransknings ändringar** väljer du **OK** för att fortsätta. 
1. Om sidan **licens godkännande** visas väljer du **Jag accepterar** för att fortsätta. 
    

### <a name="add-code-to-send-messages-to-the-topic"></a>Lägg till kod för att skicka meddelanden till ämnet 

1. I fönstret **Solution Explorer** dubbelklickar du på **program. cs** för att öppna filen i redigeraren. 
1. Lägg till följande `using` -instruktioner överst i namn områdes definitionen, före klass deklarationen:
   
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using Azure.Messaging.ServiceBus;
    ```
1. I- `Program` klassen, ovanför `Main` funktionen, deklarerar du följande variabler:

    ```csharp
        static string connectionString = "<NAMESPACE CONNECTION STRING>";
        static string topicName = "<SERVICE BUS TOPIC NAME>";
        static string subscriptionName = "<SERVICE BUS - TOPIC SUBSCRIPTION NAME>";
    ```

    Ersätt följande värden:
    - `<NAMESPACE CONNECTION STRING>` med anslutnings strängen till Service Bus namn området
    - `<TOPIC NAME>` med namnet på ämnet
    - `<SUBSCRIPTION NAME>` med namnet på prenumerationen

### <a name="send-a-single-message-to-the-topic"></a>Skicka ett enskilt meddelande till ämnet
Lägg till en metod `SendMessageToTopicAsync` som heter i `Program` klassen ovan eller under `Main` metoden. Den här metoden skickar ett enskilt meddelande till ämnet.

```csharp
    static async Task SendMessageToTopicAsync()
    {
        // create a Service Bus client 
        await using (ServiceBusClient client = new ServiceBusClient(connectionString))
        {
            // create a sender for the topic
            ServiceBusSender sender = client.CreateSender(topicName);
            await sender.SendMessageAsync(new ServiceBusMessage("Hello, World!"));
            Console.WriteLine($"Sent a single message to the topic: {topicName}");
        }
    }
```

Den här metoden utför följande steg: 
1. Skapar ett [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) -objekt med hjälp av anslutnings strängen till namn området. 
1. Använder `ServiceBusClient` objektet för att skapa ett [ServiceBusSender](/dotnet/api/azure.messaging.servicebus.servicebussender) -objekt för det angivna Service Bus ämnet. I det här steget används metoden [ServiceBusClient. CreateSender](/dotnet/api/azure.messaging.servicebus.servicebusclient.createsender) .
1. Sedan skickar metoden ett enda test meddelande till Service Bus avsnittet med hjälp av metoden [ServiceBusSender. SendMessageAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.sendmessageasync) . 

### <a name="send-a-batch-of-messages-to-the-topic"></a>Skicka en batch med meddelanden till ämnet
1. Lägg till en metod med namnet `CreateMessages` för att skapa en kö (.net-kö, inte Service Bus kö) för meddelanden till `Program` klassen. Normalt får du dessa meddelanden från olika delar av programmet. Här skapar vi en kö med exempel meddelanden. Om du inte är bekant med .NET-köer, se [Queue. Queue](/dotnet/api/system.collections.queue.enqueue).

    ```csharp
        static Queue<ServiceBusMessage> CreateMessages()
        {
            // create a queue containing the messages and return it to the caller
            Queue<ServiceBusMessage> messages = new Queue<ServiceBusMessage>();
            messages.Enqueue(new ServiceBusMessage("First message"));
            messages.Enqueue(new ServiceBusMessage("Second message"));
            messages.Enqueue(new ServiceBusMessage("Third message"));
            return messages;
        }
    ```
1. Lägg till en metod med namnet `SendMessageBatchAsync` `Program` klass och Lägg till följande kod. Den här metoden tar en kö med meddelanden och förbereder en eller flera batchar som ska skickas till Service Bus ämnet. 

    ```csharp
        static async Task SendMessageBatchToTopicAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {

                // create a sender for the topic 
                ServiceBusSender sender = client.CreateSender(topicName);

                // get the messages to be sent to the Service Bus topic
                Queue<ServiceBusMessage> messages = CreateMessages();

                // total number of messages to be sent to the Service Bus topic
                int messageCount = messages.Count;

                // while all messages are not sent to the Service Bus topic
                while (messages.Count > 0)
                {
                    // start a new batch 
                    using ServiceBusMessageBatch messageBatch = await sender.CreateMessageBatchAsync();

                    // add the first message to the batch
                    if (messageBatch.TryAddMessage(messages.Peek()))
                    {
                        // dequeue the message from the .NET queue once the message is added to the batch
                        messages.Dequeue();
                    }
                    else
                    {
                        // if the first message can't fit, then it is too large for the batch
                        throw new Exception($"Message {messageCount - messages.Count} is too large and cannot be sent.");
                    }

                    // add as many messages as possible to the current batch
                    while (messages.Count > 0 && messageBatch.TryAddMessage(messages.Peek()))
                    {
                        // dequeue the message from the .NET queue as it has been added to the batch
                        messages.Dequeue();
                    }

                    // now, send the batch
                    await sender.SendMessagesAsync(messageBatch);

                    // if there are any remaining messages in the .NET queue, the while loop repeats 
                }

                Console.WriteLine($"Sent a batch of {messageCount} messages to the topic: {topicName}");
            }
        }
    ```    

    Här följer de viktiga stegen från koden:
    1. Skapar ett [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) -objekt med hjälp av anslutnings strängen till namn området. 
    1. Anropar [CreateSender](/dotnet/api/azure.messaging.servicebus.servicebusclient.createsender) -metoden på `ServiceBusClient` objektet för att skapa ett [ServiceBusSender](/dotnet/api/azure.messaging.servicebus.servicebussender) -objekt för det angivna Service Bus ämnet. 
    1. Anropar hjälp metoden `GetMessages` för att hämta en kö med meddelanden som ska skickas till Service Bus avsnittet. 
    1. Skapar en [ServiceBusMessageBatch](/dotnet/api/azure.messaging.servicebus.servicebusmessagebatch) med hjälp av [ServiceBusSender. CreateMessageBatchAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.createmessagebatchasync).
    1. Lägg till meddelanden i batchen med hjälp av [ServiceBusMessageBatch. TryAddMessage](/dotnet/api/azure.messaging.servicebus.servicebusmessagebatch.tryaddmessage). När meddelandena läggs till i batchen tas de bort från .NET-kön. 
    1. Skickar en batch med meddelanden till Service Bus avsnittet med hjälp av metoden [ServiceBusSender. SendMessagesAsync](/dotnet/api/azure.messaging.servicebus.servicebussender.sendmessagesasync) .

### <a name="update-the-main-method"></a>Uppdatera main-metoden
Ersätt `Main()` metoden med följande async-  `Main` metod. Den anropar både sändnings metoder för att skicka ett enda meddelande och en batch med meddelanden till ämnet.  

```csharp
    static async Task Main()
    {
        // send a single message to the topic
        await SendMessageToTopicAsync();

        // send a batch of messages to the topic
        await SendMessageBatchToTopicAsync();
    }
```

### <a name="test-the-app-to-send-messages-to-the-topic"></a>Testa appen för att skicka meddelanden till ämnet
1. Kör appen. Du bör se följande utdata:

    ```console
    Sent a single message to the topic: mytopic
    Sent a batch of 3 messages to the topic: mytopic
    ```
1. Följ de här stegen i Azure Portal:
    1. Navigera till din Service Bus namnrum. 
    1. På sidan **Översikt** , i det nedre fönstret i mitten, växlar du till fliken **ämnen** och väljer avsnittet Service Bus. I följande exempel är det `mytopic` .
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/select-topic.png" alt-text="Välja ämna":::
    1. På sidan **Service Bus ämne** i **meddelande** diagrammet i avsnittet nedre **mått** kan du se att det finns fyra inkommande meddelanden för ämnet. Om du inte ser värdet väntar du några minuter och uppdaterar sidan för att se det uppdaterade diagrammet. 

        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png" alt-text="Meddelanden som skickas till ämnet" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/sent-messages-essentials.png":::
    4. Välj prenumerationen i det nedre fönstret. I följande exempel är det **S1**. På sidan **Service Bus prenumeration** visas **antalet aktiva meddelanden** som **4**. Prenumerationen har tagit emot fyra meddelanden som du skickade till ämnet, men ingen mottagare har valt dem ännu. 
    
        :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png" alt-text="Meddelanden som tagits emot vid prenumerationen" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page.png":::
    

## <a name="receive-messages-from-a-subscription"></a>Ta emot meddelanden från en prenumeration

1. Lägg till följande metoder i den `Program` klass som hanterar meddelanden och eventuella fel. 

    ```csharp
        static async Task MessageHandler(ProcessMessageEventArgs args)
        {
            string body = args.Message.Body.ToString();
            Console.WriteLine($"Received: {body} from subscription: {subscriptionName}");

            // complete the message. messages is deleted from the queue. 
            await args.CompleteMessageAsync(args.Message);
        }

        static Task ErrorHandler(ProcessErrorEventArgs args)
        {
            Console.WriteLine(args.Exception.ToString());
            return Task.CompletedTask;
        }
    ```
1. Lägg till följande metod `ReceiveMessagesFromSubscriptionAsync` i- `Program` klassen.

    ```csharp
        static async Task ReceiveMessagesFromSubscriptionAsync()
        {
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a processor that we can use to process the messages
                ServiceBusProcessor processor = client.CreateProcessor(topicName, subscriptionName, new ServiceBusProcessorOptions());

                // add handler to process messages
                processor.ProcessMessageAsync += MessageHandler;

                // add handler to process any errors
                processor.ProcessErrorAsync += ErrorHandler;

                // start processing 
                await processor.StartProcessingAsync();

                Console.WriteLine("Wait for a minute and then press any key to end the processing");
                Console.ReadKey();

                // stop processing 
                Console.WriteLine("\nStopping the receiver...");
                await processor.StopProcessingAsync();
                Console.WriteLine("Stopped receiving messages");
            }
        }
    ```

    Här följer de viktiga stegen från koden:
    1. Skapar ett [ServiceBusClient](/dotnet/api/azure.messaging.servicebus.servicebusclient) -objekt med hjälp av anslutnings strängen till namn området. 
    1. Anropar metoden [CreateProcessor](/dotnet/api/azure.messaging.servicebus.servicebusclient.createprocessor) för objektet för `ServiceBusClient` att skapa ett [ServiceBusProcessor](/dotnet/api/azure.messaging.servicebus.servicebusprocessor) -objekt för det angivna Service Bus ämnet och prenumerations kombinationen. 
    1. Anger hanterare för [ProcessMessageAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processmessageasync) -och [ProcessErrorAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processerrorasync) -händelser för `ServiceBusProcessor` objektet. 
    1. Startar bearbetning av meddelanden genom att anropa [StartProcessingAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.startprocessingasync) på `ServiceBusProcessor` objektet. 
    1. När användaren trycker på en tangent för att avsluta bearbetningen anropas [StopProcessingAsync](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.stopprocessingasync) för `ServiceBusProcessor` objektet. 
1. Lägg till ett anrop till `ReceiveMessagesFromSubscriptionAsync` metoden i- `Main` metoden. Kommentera ut- `SendMessagesToTopicAsync` metoden om du bara vill testa mottagning av meddelanden. Om du inte gör det visas ytterligare fyra meddelanden som skickas till ämnet. 

    ```csharp
        static async Task Main()
        {
            // send a message to the topic
            await SendMessageToTopicAsync();

            // send a batch of messages to the topic
            await SendMessageBatchToTopicAsync();

            // receive messages from the subscription
            await ReceiveMessagesFromSubscriptionAsync();
        }
    ```
## <a name="run-the-app"></a>Kör appen
Kör appen. Vänta en minut och tryck sedan på valfri tangent för att sluta ta emot meddelanden. Du bör se följande utdata (blank steg för nyckeln). 

```console
Sent a single message to the topic: mytopic
Sent a batch of 3 messages to the topic: mytopic
Wait for a minute and then press any key to end the processing
Received: Hello, World! from subscription: mysub
Received: First message from subscription: mysub
Received: Second message from subscription: mysub
Received: Third message from subscription: mysub
Received: Hello, World! from subscription: mysub
Received: First message from subscription: mysub
Received: Second message from subscription: mysub
Received: Third message from subscription: mysub

Stopping the receiver...
Stopped receiving messages
```

Kontrol lera portalen igen. 

- På sidan **Service Bus ämne** i **meddelande** diagrammet ser du åtta inkommande meddelanden och åtta utgående meddelanden. Om du inte ser de här talen väntar du några minuter och uppdaterar sidan för att se det uppdaterade diagrammet. 

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png" alt-text="Skickade och mottagna meddelanden" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/messages-size-final.png":::
- På sidan **Service Bus prenumeration** visas **antalet aktiva meddelanden** som noll. Det beror på att en mottagare har tagit emot meddelanden från den här prenumerationen och slutfört meddelandena. 

    :::image type="content" source="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png" alt-text="Antal aktiva meddelanden vid prenumerationen i slutet" lightbox="./media/service-bus-dotnet-how-to-use-topics-subscriptions/subscription-page-final.png":::
    


## <a name="next-steps"></a>Nästa steg
Se följande dokumentation och exempel:

- [Azure Service Bus klient bibliotek för .NET – viktigt](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus)
- [.NET-exempel för Azure Service Bus på GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [.NET API-referens](/dotnet/api/azure.messaging.servicebus?preserve-view=true)