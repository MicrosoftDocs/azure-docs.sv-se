---
title: Skicka och ta emot händelser från Azure Event Hubs med .NET (gammal)
description: Den här artikeln innehåller en genom gång av hur du skapar en .NET Core-app som skickar/tar emot händelser till/från Azure Event Hubs med hjälp av det gamla Microsoft. Azure. EventHubs-paketet.
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: f289b920b036629e5c518ce3c9e8ec521b1497c3
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310112"
---
# <a name="send-events-to-or-receive-events-from-azure-event-hubs-using-net-core-microsoftazureeventhubs"></a>Skicka händelser till eller ta emot händelser från Azure Event Hubs med .NET Core (Microsoft. Azure. EventHubs)
Den här snabb starten visar hur du skickar händelser till och tar emot händelser från en händelsehubben med hjälp av **Microsoft. Azure. EventHubs** .net Core Library.

> [!WARNING]
> Den här snabb starten använder det gamla **Microsoft. Azure. EventHubs** -paketet. En snabb start som använder det senaste  **Azure. Messaging. EventHubs** -biblioteket finns i [skicka och ta emot händelser med hjälp av Azure. Messaging. EventHubs-biblioteket](event-hubs-dotnet-standard-getstarted-send.md). Information om hur du flyttar ditt program från att använda det gamla biblioteket till ett nytt finns i [guiden för att migrera från Microsoft. Azure. EventHubs till Azure. Messaging. EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md).

## <a name="prerequisites"></a>Förutsättningar
Om du är nybörjare på Azure Event Hubs, se [Event Hubs översikt](event-hubs-about.md) innan du gör den här snabb starten. 

För att slutföra den här snabbstarten, behöver du följande förhandskrav:

- **Microsoft Azure prenumeration**. Om du vill använda Azure-tjänster, inklusive Azure Event Hubs, behöver du en prenumeration.  Om du inte har ett befintligt Azure-konto kan du registrera dig för en [kostnads fri utvärderings version](https://azure.microsoft.com/free/) eller använda dina förmåner för MSDN-prenumeranter när du [skapar ett konto](https://azure.microsoft.com).
- [Microsoft Visual Studio 2019](https://www.visualstudio.com).
- [.NET Core Visual Studio 2015- eller 2017-verktyg](https://www.microsoft.com/net/core). 
- **Skapa ett Event Hubs-namnområde och en Event Hub**. Det första steget är att använda [Azure Portal](https://portal.azure.com) för att skapa ett namn område av typen Event Hubs och hämta de autentiseringsuppgifter som programmet behöver för att kommunicera med händelsehubben. Om du behöver skapa ett namnområde och en händelsehubb följer du anvisningarna i [den här artikeln](event-hubs-create.md). Hämta sedan **anslutnings strängen för Event Hub-namnområdet** genom att följa anvisningarna i artikeln: [Hämta anslutnings sträng](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Du använder anslutnings strängen senare i den här snabb starten.

## <a name="send-events"></a>Skicka händelser 
Det här avsnittet visar hur du skapar ett .NET Core-konsolprogram för att skicka händelser till en händelsehubben. 

> [!NOTE]
> Du kan ladda ned den här snabbstarten som ett exempel från [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleSender). Ersätt strängarna `EventHubConnectionString` och `EventHubName` med värdena för din händelsehubb och kör den. Du kan också följa stegen i den här snabb starten för att skapa en egen.


### <a name="create-a-console-application"></a>Skapa ett konsolprogram

Starta Visual Studio. Klicka på **Nytt** i **Arkiv**-menyn och klicka sedan på **Projekt**. Skapa ett .NET Core-konsolprogram.

![Nytt projekt](./media/event-hubs-dotnet-standard-getstarted-send/netcoresnd.png)

### <a name="add-the-event-hubs-nuget-package"></a>Lägga till Event Hubs NuGet-paketet

Lägg till [`Microsoft.Azure.EventHubs`](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) .net Core Library NuGet-paketet i projektet genom att följa dessa steg: 

1. Högerklicka på det nyskapade projektet och välj **Hantera Nuget-paket**.
2. Klicka på fliken **Bläddra**, sök efter ”Microsoft.Azure.EventHubs” och markera paketet **Microsoft.Azure.EventHubs**. Klicka på **Installera** för att slutföra installationen och stäng sedan den här dialogrutan.

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Skriva kod för att skicka meddelanden till händelsehubben

1. Lägg till följande `using`-instruktioner överst i Program.cs-filen:

    ```csharp
    using Microsoft.Azure.EventHubs;
    using System.Text;
    using System.Threading.Tasks;
    ```

2. Lägg till konstanter för `Program`-klassen för händelsehubbens anslutningssträng och entitetssökväg (enskilt händelsehubbnamn). Ersätt platshållare inom hakparentes med rätt värden som erhölls när du skapade händelsehubben. Kontrollera att `{Event Hubs connection string}` är anslutningssträngen på namnrymdsnivå och inte händelsehubbens sträng. 

    ```csharp
    private static EventHubClient eventHubClient;
    private const string EventHubConnectionString = "{Event Hubs connection string}";
    private const string EventHubName = "{Event Hub path/name}";
    ```

3. Lägg till en ny metod som heter `MainAsync` till `Program`-klassen enligt följande:

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
        // Typically, the connection string should have the entity path in it, but this simple scenario
        // uses the connection string from the namespace.
        var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
        {
            EntityPath = EventHubName
        };

        eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

        await SendMessagesToEventHub(100);

        await eventHubClient.CloseAsync();

        Console.WriteLine("Press ENTER to exit.");
        Console.ReadLine();
    }
    ```

4. Lägg till en ny metod som heter `SendMessagesToEventHub` till `Program`-klassen enligt följande:

    ```csharp
    // Uses the event hub client to send 100 messages to the event hub.
    private static async Task SendMessagesToEventHub(int numMessagesToSend)
    {
        for (var i = 0; i < numMessagesToSend; i++)
        {
            try
            {
                var message = $"Message {i}";
                Console.WriteLine($"Sending message: {message}");
                await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
            }
            catch (Exception exception)
            {
                Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
            }

            await Task.Delay(10);
        }

        Console.WriteLine($"{numMessagesToSend} messages sent.");
    }
    ```

5. Lägg till följande kod till `Main`-metoden i `Program`-klassen:

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

   Så här bör din Program.cs se ut.

    ```csharp
    namespace SampleSender
    {
        using System;
        using System.Text;
        using System.Threading.Tasks;
        using Microsoft.Azure.EventHubs;

        public class Program
        {
            private static EventHubClient eventHubClient;
            private const string EventHubConnectionString = "{Event Hubs connection string}";
            private const string EventHubName = "{Event Hub path/name}";

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                // Creates an EventHubsConnectionStringBuilder object from the connection string, and sets the EntityPath.
                // Typically, the connection string should have the entity path in it, but for the sake of this simple scenario
                // we are using the connection string from the namespace.
                var connectionStringBuilder = new EventHubsConnectionStringBuilder(EventHubConnectionString)
                {
                    EntityPath = EventHubName
                };

                eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());

                await SendMessagesToEventHub(100);

                await eventHubClient.CloseAsync();

                Console.WriteLine("Press ENTER to exit.");
                Console.ReadLine();
            }

            // Uses the event hub client to send 100 messages to the event hub.
            private static async Task SendMessagesToEventHub(int numMessagesToSend)
            {
                for (var i = 0; i < numMessagesToSend; i++)
                {
                    try
                    {
                        var message = $"Message {i}";
                        Console.WriteLine($"Sending message: {message}");
                        await eventHubClient.SendAsync(new EventData(Encoding.UTF8.GetBytes(message)));
                    }
                    catch (Exception exception)
                    {
                        Console.WriteLine($"{DateTime.Now} > Exception: {exception.Message}");
                    }

                    await Task.Delay(10);
                }

                Console.WriteLine($"{numMessagesToSend} messages sent.");
            }
        }
    }
    ```

6. Kör programmet och kontrollera att det inte finns några fel.

## <a name="receive-events"></a>Ta emot händelser
Det här avsnittet visar hur du skriver ett .NET Core-konsolprogram som tar emot meddelanden från en Event Hub med hjälp av [händelse bearbetnings värden](event-hubs-event-processor-host.md). [Värden för händelsebearbetning](event-hubs-event-processor-host.md) är en .NET-klass som förenklar mottagandet av händelser från händelsehubbar genom att hantera permanenta kontrollpunkter och parallella mottaganden från händelsehubbar. Med hjälp av värden för händelsebearbetning kan du dela upp händelser över flera olika mottagare, även när de ligger på olika noder. Det här exemplet visas hur man använder värden för händelsebearbetning för en enda mottagare.
> [!NOTE]
> Du kan hämta den här snabb starten som ett exempel från [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/SampleEphReceiver), ersätta `EventHubConnectionString` och,,, `EventHubName` `StorageAccountName` `StorageAccountKey` och `StorageContainerName` strängar med Event Hub-värdena och köra den. Alternativt kan du följa stegen i den här självstudiekursen och skapa ett eget.

[!INCLUDE [event-hubs-create-storage](../../includes/event-hubs-create-storage.md)]

### <a name="create-a-console-application"></a>Skapa ett konsolprogram

Starta Visual Studio. Klicka på **Nytt** i **Arkiv**-menyn och klicka sedan på **Projekt**. Skapa ett .NET Core-konsolprogram.

![Nytt projekt för mottagning](./media/event-hubs-dotnet-standard-getstarted-receive-eph/netcorercv.png)

### <a name="add-the-event-hubs-nuget-package"></a>Lägga till Event Hubs NuGet-paketet

Lägg till [**Microsoft.Azure.EventHubs**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs/) och [**Microsoft.Azure.EventHubs.Processor**](https://www.nuget.org/packages/Microsoft.Azure.EventHubs.Processor/) .NET Standard-bibliotekets NuGet-paket till projektet genom att följa dessa steg: 

1. Högerklicka på det nyskapade projektet och välj **Hantera Nuget-paket**.
2. Klicka på fliken **Bläddra**, sök efter **Microsoft.Azure.EventHubs** och markera paketet **Microsoft.Azure.EventHubs**. Klicka på **Installera** för att slutföra installationen och stäng sedan den här dialogrutan.
3. Upprepa steg 1 och 2 och installera paketet **Microsoft.Azure.EventHubs.Processor**.

### <a name="implement-the-ieventprocessor-interface"></a>Implementera gränssnittet IEventProcessor

1. Högerklicka på projektet i Solution Explorer, klicka på **Lägg till** och sedan på **Klass**. Ge den nya klassen namnet **SimpleEventProcessor**.

2. Öppna filen SimpleEventProcessor.cs och lägg till följande `using`-uttryck överst i filen.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

3. Implementera gränssnittet `IEventProcessor`. Ersätt hela innehållet i klassen `SimpleEventProcessor` med följande kod:

    ```csharp
    public class SimpleEventProcessor : IEventProcessor
    {
        public Task CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine($"Processor Shutting Down. Partition '{context.PartitionId}', Reason: '{reason}'.");
            return Task.CompletedTask;
        }

        public Task OpenAsync(PartitionContext context)
        {
            Console.WriteLine($"SimpleEventProcessor initialized. Partition: '{context.PartitionId}'");
            return Task.CompletedTask;
        }

        public Task ProcessErrorAsync(PartitionContext context, Exception error)
        {
            Console.WriteLine($"Error on Partition: {context.PartitionId}, Error: {error.Message}");
            return Task.CompletedTask;
        }

        public Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (var eventData in messages)
            {
                var data = Encoding.UTF8.GetString(eventData.Body.Array, eventData.Body.Offset, eventData.Body.Count);
                Console.WriteLine($"Message received. Partition: '{context.PartitionId}', Data: '{data}'");
            }

            return context.CheckpointAsync();
        }
    }
    ```

### <a name="update-the-main-method-to-use-simpleeventprocessor"></a>Uppdatera Main-metoden till att använda SimpleEventProcessor

1. Lägg till följande `using`-instruktioner överst i Program.cs-filen.

    ```csharp
    using Microsoft.Azure.EventHubs;
    using Microsoft.Azure.EventHubs.Processor;
    using System.Threading.Tasks;
    ```

2. Lägg till konstanter till `Program`-klassen för händelsehubbens anslutningssträng, händelsehubbens namn, namn på lagringskontocontainern, lagringskontots namn och lagringskontonyckeln. Lägg till följande kod och ersätt platshållarna med motsvarande värden:

    ```csharp
    private const string EventHubConnectionString = "{Event Hubs connection string}";
    private const string EventHubName = "{Event Hub path/name}";
    private const string StorageContainerName = "{Storage account container name}";
    private const string StorageAccountName = "{Storage account name}";
    private const string StorageAccountKey = "{Storage account key}";

    private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);
    ```   

3. Lägg till en ny metod som heter `MainAsync` till `Program`-klassen enligt följande:

    ```csharp
    private static async Task MainAsync(string[] args)
    {
        Console.WriteLine("Registering EventProcessor...");

        var eventProcessorHost = new EventProcessorHost(
            EventHubName,
            PartitionReceiver.DefaultConsumerGroupName,
            EventHubConnectionString,
            StorageConnectionString,
            StorageContainerName);

        // Registers the Event Processor Host and starts receiving messages
        await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

        Console.WriteLine("Receiving. Press ENTER to stop worker.");
        Console.ReadLine();

        // Disposes of the Event Processor Host
        await eventProcessorHost.UnregisterEventProcessorAsync();
    }
    ```

3. Lägg till följande kodrad till `Main`-metoden:

    ```csharp
    MainAsync(args).GetAwaiter().GetResult();
    ```

    Så här bör din Program.cs-fil se ut:

    ```csharp
    namespace SampleEphReceiver
    {

        public class Program
        {
            private const string EventHubConnectionString = "{Event Hubs connection string}";
            private const string EventHubName = "{Event Hub path/name}";
            private const string StorageContainerName = "{Storage account container name}";
            private const string StorageAccountName = "{Storage account name}";
            private const string StorageAccountKey = "{Storage account key}";

            private static readonly string StorageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", StorageAccountName, StorageAccountKey);

            public static void Main(string[] args)
            {
                MainAsync(args).GetAwaiter().GetResult();
            }

            private static async Task MainAsync(string[] args)
            {
                Console.WriteLine("Registering EventProcessor...");

                var eventProcessorHost = new EventProcessorHost(
                    EventHubName,
                    PartitionReceiver.DefaultConsumerGroupName,
                    EventHubConnectionString,
                    StorageConnectionString,
                    StorageContainerName);

                // Registers the Event Processor Host and starts receiving messages
                await eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>();

                Console.WriteLine("Receiving. Press ENTER to stop worker.");
                Console.ReadLine();

                // Disposes of the Event Processor Host
                await eventProcessorHost.UnregisterEventProcessorAsync();
            }
        }
    }
    ```

4. Kör programmet och kontrollera att det inte finns några fel.


## <a name="next-steps"></a>Nästa steg
Läs följande artiklar:

- [Azure-rollbaserad åtkomst kontroll (Azure RBAC) exempel](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Microsoft.Azure.EventHubs/Rbac). 
    
    I de här exemplen används det gamla biblioteket **Microsoft. Azure. EventHubs** , men du kan enkelt uppdatera det så att det använder det senaste **Azure. Messaging. EventHubs** -biblioteket. Om du vill flytta exemplet från att använda det gamla biblioteket till ett nytt, se [guiden för att migrera från Microsoft. Azure. EventHubs till Azure. Messaging. EventHubs](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md).
- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funktioner och terminologi i Azure Event Hubs](event-hubs-features.md)
- [Vanliga frågor och svar om Event Hubs](event-hubs-faq.yml)


