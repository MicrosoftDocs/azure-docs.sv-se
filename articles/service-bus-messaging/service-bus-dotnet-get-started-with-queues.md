---
title: Kom igång med Azure Service Bus köer (Azure. Messaging. Service Bus)
description: I den här självstudien skapar du ett .NET Core C#-program för att skicka meddelanden till och ta emot meddelanden från en Service Bus kö.
ms.topic: quickstart
ms.tgt_pltfrm: dotnet
ms.date: 11/13/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: ec3f53e6f69614028c013efa5f0e6852cbc3f8ae
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98631648"
---
# <a name="send-messages-to-and-receive-messages-from-azure-service-bus-queues-net"></a>Skicka meddelanden till och ta emot meddelanden från Azure Service Bus köer (.NET)
I den här självstudien skapar du ett .NET Core-konsolprogram för att skicka meddelanden till och ta emot meddelanden från en Service Bus kö med hjälp av **Azure. Messaging. Service Bus** -paketet. 

> [!Important]
> Den här snabb starten använder det nya Azure. Messaging. Service Bus-paketet. En snabb start som använder det gamla Microsoft. Azure. Service Bus-paketet finns i [skicka och ta emot händelser med hjälp av Microsoft. Azure. Service Bus-paketet](service-bus-dotnet-get-started-with-queues-legacy.md).

## <a name="prerequisites"></a>Förutsättningar

- [Visual Studio 2019](https://www.visualstudio.com/vs)
- En Azure-prenumeration. Du behöver ett Azure-konto för att genomföra kursen. Du kan aktivera dina [förmåner för MSDN-prenumeranter](https://azure.microsoft.com/pricing/member-offers/credit-for-visual-studio-subscribers/?WT.mc_id=A85619ABF) eller registrera dig för ett [kostnads fritt konto](https://azure.microsoft.com/free/?WT.mc_id=A85619ABF).
- Om du inte har en kö att arbeta med följer du stegen i artikeln [använd Azure Portal för att Service Bus skapa](service-bus-quickstart-portal.md) en kö. Anteckna **anslutnings strängen** för Service Bus namn området och namnet på **kön** som du skapade.

## <a name="send-messages-to-a-queue"></a>Skicka meddelanden till en kö
I den här snabb starten skapar du ett C# .NET Core-konsolprogram för att skicka meddelanden till kön.

### <a name="create-a-console-application"></a>Skapa ett konsolprogram
Starta Visual Studio och skapa ett nytt **konsol program (.net Core)-** projekt för C#. 

### <a name="add-the-service-bus-nuget-package"></a>Lägga till Service Bus-NuGet-paketet

1. Högerklicka på det nyskapade projektet och välj **Hantera Nuget-paket**.
1. Välj **Bläddra**. Sök efter och välj **[Azure. Messaging. Service Bus](https://www.nuget.org/packages/Azure.Messaging.ServiceBus/)**.
1. Välj **Installera** för att slutföra installationen och stäng sedan NuGet Package Manager.

### <a name="add-code-to-send-messages-to-the-queue"></a>Lägga till kod för att skicka meddelanden till kön

1. I *program. cs* lägger du till följande- `using` instruktioner överst i namn områdes definitionen, före klass deklarationen:

    ```csharp
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    
    using Azure.Messaging.ServiceBus;
    ```

1. I `Program` -klassen deklarerar du följande variabler:

    ```csharp
        static string connectionString = "<NAMESPACE CONNECTION STRING>";
        static string queueName = "<QUEUE NAME>";
    ```

    Ange din anslutnings sträng för namn området som `connectionString` variabel. Ange namnet på kön.

1. Direkt efter `Main()` -metoden lägger du till följande `SendMessagesAsync()` metod som utför arbetet med att skicka ett meddelande:

    ```csharp
        static async Task SendMessageAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a sender for the queue 
                ServiceBusSender sender = client.CreateSender(queueName);

                // create a message that we can send
                ServiceBusMessage message = new ServiceBusMessage("Hello world!");

                // send the message
                await sender.SendMessageAsync(message);
                Console.WriteLine($"Sent a single message to the queue: {queueName}");
            }
        }
    ```
1. Lägg till en metod med namnet `CreateMessages` för att skapa en kö (.net-kö) med meddelanden till- `Program` klassen. Normalt får du dessa meddelanden från olika delar av programmet. Här skapar vi en kö med exempel meddelanden.

    ```csharp
        static Queue<ServiceBusMessage> CreateMessages()
        {
            // create a queue containing the messages and return it to the caller
            Queue<ServiceBusMessage> messages = new Queue<ServiceBusMessage>();
            messages.Enqueue(new ServiceBusMessage("First message in the batch"));
            messages.Enqueue(new ServiceBusMessage("Second message in the batch"));
            messages.Enqueue(new ServiceBusMessage("Third message in the batch"));
            return messages;
        }
    ```
1. Lägg till en metod `SendMessageBatchAsync` som heter i `Program` klassen och Lägg till följande kod. Den här metoden tar en kö med meddelanden och förbereder en eller flera batchar som ska skickas till Service Bus kön. 

    ```csharp
        static async Task SendMessageBatchAsync()
        {
            // create a Service Bus client 
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a sender for the queue 
                ServiceBusSender sender = client.CreateSender(queueName);

                // get the messages to be sent to the Service Bus queue
                Queue<ServiceBusMessage> messages = CreateMessages();

                // total number of messages to be sent to the Service Bus queue
                int messageCount = messages.Count;

                // while all messages are not sent to the Service Bus queue
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

                Console.WriteLine($"Sent a batch of {messageCount} messages to the topic: {queueName}");
            }
        }
    ```
1. Ersätt `Main()` metoden med följande async-  `Main` metod. Den anropar både sändnings metoder för att skicka ett enda meddelande och en batch med meddelanden till kön. 

    ```csharp
        static async Task Main()
        {
            // send a message to the queue
            await SendMessageAsync();

            // send a batch of messages to the queue
            await SendMessageBatchAsync();
        }
    ```
5. Kör appen. Du bör se följande meddelanden. 

    ```console
    Sent a single message to the queue: myqueue
    Sent a batch of messages to the queue: myqueue
    ```       
1. Följ de här stegen i Azure Portal:
    1. Navigera till din Service Bus namnrum. 
    1. På sidan **Översikt** väljer du kön i det nedre fönstret i mitten. 
    1. Observera värdena i avsnittet **Essentials** .

    :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/sent-messages-essentials.png" alt-text="Mottagna meddelanden med antal och storlek" lightbox="./media/service-bus-dotnet-get-started-with-queues/sent-messages-essentials.png":::

    Observera följande värden:
    - Värdet för **Antal aktiva meddelanden** för kön är nu **4**. Varje gången du kör den här avsändar appen utan att hämta meddelandena ökar värdet med 4.
    - Köns aktuella storlek ökar det **aktuella** värdet i **grunderna**  varje gången appen lägger till meddelanden i kön.
    - I **meddelande** diagrammet i avsnittet nedre **mått** kan du se att det finns fyra inkommande meddelanden för kön. 

## <a name="receive-messages-from-a-queue"></a>Ta emot meddelanden från en kö
I det här avsnittet ska du lägga till kod för att hämta meddelanden från kön.

1. Lägg till följande metoder i den `Program` klass som hanterar meddelanden och eventuella fel. 

    ```csharp
        // handle received messages
        static async Task MessageHandler(ProcessMessageEventArgs args)
        {
            string body = args.Message.Body.ToString();
            Console.WriteLine($"Received: {body}");

            // complete the message. messages is deleted from the queue. 
            await args.CompleteMessageAsync(args.Message);
        }

        // handle any errors when receiving messages
        static Task ErrorHandler(ProcessErrorEventArgs args)
        {
            Console.WriteLine(args.Exception.ToString());
            return Task.CompletedTask;
        }
    ```
1. Lägg till en metod som heter `ReceiveMessagesAsync` i `Program` klassen och Lägg till följande kod för att ta emot meddelanden. 

    ```csharp
        static async Task ReceiveMessagesAsync()
        {
            await using (ServiceBusClient client = new ServiceBusClient(connectionString))
            {
                // create a processor that we can use to process the messages
                ServiceBusProcessor processor = client.CreateProcessor(queueName, new ServiceBusProcessorOptions());

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
1. Lägg till ett anrop till- `ReceiveMessagesAsync` metoden från- `Main` metoden. Kommentera ut- `SendMessagesAsync` metoden om du bara vill testa mottagning av meddelanden. Om du inte gör det visas ytterligare fyra meddelanden som skickas till kön. 

    ```csharp
        static async Task Main()
        {
            // send a message to the queue
            await SendMessageAsync();

            // send a batch of messages to the queue
            await SendMessageBatchAsync();

            // receive message from the queue
            await ReceiveMessagesAsync();
        }
    ```

## <a name="run-the-app"></a>Kör appen
Kör appen. Vänta en minut och tryck sedan på valfri tangent för att sluta ta emot meddelanden. Du bör se följande utdata (blank steg för nyckeln). 

```console
Sent a single message to the queue: myqueue
Sent a batch of messages to the queue: myqueue
Wait for a minute and then press any key to end the processing
Received: Hello world!
Received: First message in the batch
Received: Second message in the batch
Received: Third message in the batch
Received: Hello world!
Received: First message in the batch
Received: Second message in the batch
Received: Third message in the batch

Stopping the receiver...
Stopped receiving messages
```

Kontrol lera portalen igen. 

- **Antalet aktiva meddelanden** och **aktuella** värden är nu **0**.
- I **meddelande** diagrammet i avsnittet nedre **mått** kan du se att det finns åtta inkommande meddelanden och åtta utgående meddelanden för kön. 

    :::image type="content" source="./media/service-bus-dotnet-get-started-with-queues/queue-messages-size-final.png" alt-text="Aktiva meddelanden och storlek efter mottagning" lightbox="./media/service-bus-dotnet-get-started-with-queues/queue-messages-size-final.png":::

## <a name="next-steps"></a>Nästa steg
Se följande dokumentation och exempel:

- [Azure Service Bus klient bibliotek för .NET – viktigt](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus)
- [Exempel på GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/servicebus/Azure.Messaging.ServiceBus/samples)
- [.NET API-referens](/dotnet/api/azure.messaging.servicebus?preserve-view=true)