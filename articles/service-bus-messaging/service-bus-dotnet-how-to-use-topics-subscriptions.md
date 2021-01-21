---
title: Skicka meddelanden till Azure Service Bus ämnen med Azure-Messaging – Service Bus
description: Den här snabb starten visar hur du skickar meddelanden till Azure Service Bus ämnen med hjälp av Azure-Messaging-Service Bus-paketet.
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 11/13/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 60504bcf9e2c3f9460eee9a2e72d18767c0cfa71
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98631682"
---
# <a name="send-messages-to-an-azure-service-bus-topic-and-receive-messages-from-subscriptions-to-the-topic-net"></a>Skicka meddelanden till ett Azure Service Bus ämne och ta emot meddelanden från prenumerationer på ämnet (.NET)
Den här självstudien visar hur du skapar en .NET Core-konsol som skickar meddelanden till ett Service Bus ämne och tar emot meddelanden från en prenumeration av artikeln. 

> [!Important]
> Den här snabb starten använder det nya **Azure. Messaging. Service Bus** -paketet. En snabb start som använder det gamla Microsoft. Azure. Service Bus-paketet finns i [skicka och ta emot meddelanden med hjälp av Microsoft. Azure. Service Bus-paketet](service-bus-dotnet-how-to-use-topics-subscriptions-legacy.md).

## <a name="prerequisites"></a>Förutsättningar

- [Visual Studio 2019](https://www.visualstudio.com/vs)
- En Azure-prenumeration. Du behöver ett Azure-konto för att genomföra kursen. Du kan aktivera dina [förmåner för Visual Studio eller MSDN-prenumeranter](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A85619ABF) eller registrera dig för ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Följ stegen i [snabb starten: använd Azure Portal för att skapa ett Service Bus ämne och prenumerationer på avsnittet](service-bus-quickstart-topics-subscriptions-portal.md). Anteckna anslutnings strängen, ämnes namnet och ett prenumerations namn. Du kommer bara att använda en prenumeration för den här snabb starten. 

## <a name="send-messages-to-a-topic"></a>Skicka meddelanden till ett ämne
I det här avsnittet ska du skapa ett .NET Core-konsolprogram i Visual Studio, lägga till kod för att skicka meddelanden till ämnet som du har skapat. 

### <a name="create-a-console-application"></a>Skapa ett konsolprogram
Starta Visual Studio och skapa ett nytt **konsol program (.net Core)-** projekt för C#. 

### <a name="add-the-service-bus-nuget-package"></a>Lägga till Service Bus-NuGet-paketet

1. Högerklicka på det nyskapade projektet och välj **Hantera Nuget-paket**.
1. Välj **Bläddra**. Sök efter och välj **[Azure. Messaging. Service Bus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/)**.
1. Välj **Installera** för att slutföra installationen och stäng sedan NuGet Package Manager.

### <a name="add-code-to-send-messages-to-the-topic"></a>Lägg till kod för att skicka meddelanden till ämnet 

1. I Program.cs lägger du till följande `using`-instruktioner överst i definitionen för namnområdet, före klassdeklarationen:
   
    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using Azure.Messaging.ServiceBus;
    ```
1. I `Program` -klassen deklarerar du följande variabler:

    ```csharp
        static string connectionString = "<NAMESPACE CONNECTION STRING>";
        static string topicName = "<TOPIC NAME>";
        static string subscriptionName = "<SUBSCRIPTION NAME>";
    ```

    Ersätt följande värden:
    - `<NAMESPACE CONNECTION STRING>` med anslutnings strängen till Service Bus namn området
    - `<TOPIC NAME>` med namnet på ämnet
    - `<SUBSCRIPTION NAME>` med namnet på prenumerationen
2. Lägg till en metod med namnet `SendMessageToTopicAsync` som skickar ett meddelande till ämnet. 

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
1. Lägg till en metod med namnet `CreateMessages` för att skapa en kö (.net-kö) med meddelanden till- `Program` klassen. Normalt får du dessa meddelanden från olika delar av programmet. Här skapar vi en kö med exempel meddelanden.

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
1. Lägg till en metod `SendMessageBatchAsync` som heter i `Program` klassen och Lägg till följande kod. Den här metoden tar en kö med meddelanden och förbereder en eller flera batchar som ska skickas till Service Bus ämnet. 

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
1. Ersätt `Main()` metoden med följande async-  `Main` metod. Den anropar både sändnings metoder för att skicka ett enda meddelande och en batch med meddelanden till ämnet.  

    ```csharp
        static async Task Main()
        {
            // send a message to the topic
            await SendMessageToTopicAsync();

            // send a batch of messages to the topic
            await SendMessageBatchToTopicAsync();
        }
    ```
5. Kör appen. Du bör se följande utdata:

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
- [Exempel på GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [.NET API-referens](/dotnet/api/azure.messaging.servicebus?preserve-view=true)