---
title: Azure Event Hubs – skicka/ta emot händelser med hjälp av .NET Framework
description: 'Snabb start: den här artikeln innehåller en genom gång av hur du skapar ett .NET Framework program som skickar händelser till Azure Event Hubs.'
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 3c2ce58f86fa27544641c0917e96fa3172017232
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309364"
---
# <a name="quickstart-send-events-to-or-receive-events-from-azure-event-hubs-using-net-framework"></a>Snabb start: skicka händelser till eller ta emot händelser från Azure Event Hubs med .NET Framework
Azure Event Hubs är en strömningstjänst för stordata och händelseinmatningstjänst som kan ta emot och bearbeta flera miljoner händelser per sekund. Event Hubs kan bearbeta och lagra händelser, data eller telemetri som producerats av distribuerade program och enheter. Data som skickas till en händelsehubb kan omvandlas och lagras med valfri provider för realtidsanalys eller batchbearbetnings-/lagringsadapter. En detaljerad översikt över Event Hubs finns i [Översikt över Event Hubs](event-hubs-about.md) och [Event Hubs-funktioner](event-hubs-features.md).

I den här självstudien visas hur du skapar .NET Framework-konsol program i C# för att skicka händelser till eller ta emot händelser från en eventhub. 

## <a name="prerequisites"></a>Förutsättningar
För att slutföra den här självstudien, finns följande förhandskrav:

- [Microsoft Visual Studio 2019](https://visualstudio.com).
- **Skapa ett Event Hubs-namnområde och en Event Hub**. Det första steget är att använda [Azure Portal](https://portal.azure.com) för att skapa ett namn område av typen Event Hubs och hämta de autentiseringsuppgifter som programmet behöver för att kommunicera med händelsehubben. Om du behöver skapa ett namnområde och en händelsehubb följer du anvisningarna i [den här artikeln](event-hubs-create.md). Hämta sedan **anslutnings strängen för Event Hub-namnområdet** genom att följa anvisningarna i artikeln: [Hämta anslutnings sträng](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Du kan använda anslutningssträngen senare i den här självstudien.

## <a name="send-events"></a>Skicka händelser 
Det här avsnittet visar hur du skapar ett .NET Framework-konsolprogram för att skicka händelser till en Event Hub. 

### <a name="create-a-console-application"></a>Skapa ett konsolprogram

I Visual Studio skapar du ett nytt Visual C# Desktop-projekt med hjälp av projekt mal len **konsol program** . Namnge projektet **Avsändare**.
   
![Skärm bild som visar fönstret nytt projekt med "konsol program (.NET Framework)" markerat och knappen för projekt namnet "Sender" och "OK" markerat.](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp1.png)

### <a name="add-the-event-hubs-nuget-package"></a>Lägga till Event Hubs NuGet-paketet

1. Högerklicka på projektet **Avsändare** i Solution Explorer och klicka sedan på **Hantera NuGet-paket för lösningen**. 
2. Klicka på **Bläddra**-fliken och sök sedan efter `WindowsAzure.ServiceBus`. Klicka på **Installera** och godkänn användningsvillkoren. 
   
    ![Installera Service Bus NuGet-paket](./media/event-hubs-dotnet-framework-getstarted-send/create-sender-csharp2.png)
   
    Visual Studio laddar ned, installerar och lägger till en referens till [Azure Service Bus-bibliotekets NuGet-paket](https://www.nuget.org/packages/WindowsAzure.ServiceBus).

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Skriva kod för att skicka meddelanden till händelsehubben

1. Lägg till följande- `using` instruktioner överst i filen **program. cs** :
   
    ```csharp
    using System.Threading;
    using Microsoft.ServiceBus.Messaging;
    ```
2. Lägg till följande fält i klassen **Program**, och ersätt platshållarvärdena med namnet på den händelsehubb du skapade i föregående avsnitt samt anslutningssträngen på namnområdesnivå som du sparat tidigare. Du kan kopiera anslutnings strängen för händelsehubben från **anslutnings strängen – primär** nyckel under **RootManageSharedAccessKey** på sidan Event Hub i Azure Portal. Detaljerade anvisningar finns i [Hämta anslutnings sträng](event-hubs-get-connection-string.md#get-connection-string-from-the-portal).
   
    ```csharp
    static string eventHubName = "Your Event Hub name";
    static string connectionString = "namespace connection string";
    ```
3. Lägg till följande metod i **program** -klassen:
   
      ```csharp
      static void SendingRandomMessages()
      {
          var eventHubClient = EventHubClient.CreateFromConnectionString(connectionString, eventHubName);
          while (true)
          {
              try
              {
                  var message = Guid.NewGuid().ToString();
                  Console.WriteLine("{0} > Sending message: {1}", DateTime.Now, message);
                  eventHubClient.Send(new EventData(Encoding.UTF8.GetBytes(message)));
              }
              catch (Exception exception)
              {
                  Console.ForegroundColor = ConsoleColor.Red;
                  Console.WriteLine("{0} > Exception: {1}", DateTime.Now, exception.Message);
                  Console.ResetColor();
              }
       
              Thread.Sleep(200);
          }
      }
      ```
   
      Med den här metoden skickas kontinuerligt händelser till din händelsehubb med en fördröjning på 200 ms.
4. Slutligen lägger du till följande rader till **Main**-metoden:
   
      ```csharp
      Console.WriteLine("Press Ctrl-C to stop the sender process");
      Console.WriteLine("Press Enter to start now");
      Console.ReadLine();
      SendingRandomMessages();
      ```
5. Kör programmet och kontrollera att det inte finns några fel.
  
## <a name="receive-events"></a>Ta emot händelser
I det här avsnittet skriver du ett .NET Framework-konsol program som tar emot meddelanden från en händelsehubben med hjälp av [händelse bearbetnings värden](event-hubs-event-processor-host.md). [Värden för händelsebearbetning](event-hubs-event-processor-host.md) är en .NET-klass som förenklar mottagandet av händelser från händelsehubbar genom att hantera permanenta kontrollpunkter och parallella mottaganden från händelsehubbar. Med hjälp av värden för händelsebearbetning kan du dela upp händelser över flera olika mottagare, även när de ligger på olika noder. 

[!INCLUDE [event-hubs-create-storage](../../includes/event-hubs-create-storage.md)]

### <a name="create-a-console-application"></a>Skapa ett konsolprogram

I Visual Studio skapar du ett nytt Visual C#-skrivbordsapprojekt med hjälp av projektmallen **Konsolprogram**. Namnge projektet **Mottagare**.
   
![Skapa konsolprogram](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp1.png)

### <a name="add-the-event-hubs-nuget-package"></a>Lägga till Event Hubs NuGet-paketet

1. Högerklicka på projektet **Mottagare** i Solution Explorer och klicka sedan på **Hantera NuGet-paket för lösningen**.
2. Klicka på **Bläddra**-fliken och sök sedan efter `Microsoft Azure Service Bus Event Hub - EventProcessorHost`. Klicka på **Installera** och godkänn användningsvillkoren.
   
    ![Sök efter event processors värd NuGet-paket](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-eph-csharp1.png)
   
    Visual Studio laddar ned, installerar och lägger till en referens till [Azure Service Bus Event Hub –EventProcessorHost NuGet-paket ](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost), med alla sina beroenden.

### <a name="implement-the-ieventprocessor-interface"></a>Implementera gränssnittet IEventProcessor

1. Högerklicka på **Mottagare**-projektet, klicka på **Lägg till** och klicka sedan på **Klass**. Kalla den nya klassen för **SimpleEventProcessor** och klicka sedan på **Lägg till** för att skapa klassen.
   
    ![Lägg till SimpleEventProcessor-klass](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-receiver-csharp2.png)
2. Lägg till följande uttryck överst i filen SimpleEventProcessor.cs:
    
      ```csharp
      using Microsoft.ServiceBus.Messaging;
      using System.Diagnostics;
      ```
    
3. Ersätt följande kod för bröd texten i klassen:
    
      ```csharp
      class SimpleEventProcessor : IEventProcessor
      {
        Stopwatch checkpointStopWatch;
        
        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
        {
            Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
            if (reason == CloseReason.Shutdown)
            {
                await context.CheckpointAsync();
            }
        }
        
        Task IEventProcessor.OpenAsync(PartitionContext context)
        {
            Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
            this.checkpointStopWatch = new Stopwatch();
            this.checkpointStopWatch.Start();
            return Task.FromResult<object>(null);
        }
        
        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
        {
            foreach (EventData eventData in messages)
            {
                string data = Encoding.UTF8.GetString(eventData.GetBytes());
        
                Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                    context.Lease.PartitionId, data));
            }
        
            //Call checkpoint every 5 minutes, so that worker can resume processing from 5 minutes back if it restarts.
            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5))
            {
                await context.CheckpointAsync();
                this.checkpointStopWatch.Restart();
            }
        }
      }
      ```
    
      Den här klassen kommer att anropas av **EventProcessorHost** för att bearbeta händelser som tagits emot från händelsehubben. Observera att `SimpleEventProcessor`-klassen använder sig av ett stoppur för att regelbundet anropa kontrollpunktsmetoden i **EventProcessorHost**-kontexten. Detta garanterar att högst fem minuters bearbetningsarbete försvinner om mottagaren startas om.

### <a name="update-the-main-method-to-use-simpleeventprocessor"></a>Uppdatera Main-metoden till att använda SimpleEventProcessor

1. I **Program**-klassen lägger du till följande `using`-uttryck överst i filen:
    
      ```csharp
      using Microsoft.ServiceBus.Messaging;
      ```
    
2. Ersätt `Main` -metoden i- `Program` klassen med följande kod och ersätt namnet på händelsehubben och anslutnings strängen på namn områdes nivå som du sparade tidigare, samt lagrings kontot och nyckeln som du kopierade i föregående avsnitt. 
    
      ```csharp
      static void Main(string[] args)
      {
        string eventHubConnectionString = "{Event Hubs namespace connection string}";
        string eventHubName = "{Event Hub name}";
        string storageAccountName = "{storage account name}";
        string storageAccountKey = "{storage account key}";
        string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);
        
        string eventProcessorHostName = Guid.NewGuid().ToString();
        EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
        Console.WriteLine("Registering EventProcessor...");
        var options = new EventProcessorOptions();
        options.ExceptionReceived += (sender, e) => { Console.WriteLine(e.Exception); };
        eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>(options).Wait();
        
        Console.WriteLine("Receiving. Press enter key to stop worker.");
        Console.ReadLine();
        eventProcessorHost.UnregisterEventProcessorAsync().Wait();
      }
      ```
    
3. Kör programmet och kontrollera att det inte finns några fel.
  
## <a name="next-steps"></a>Nästa steg
Läs följande artiklar: 

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funktioner och terminologi i Azure Event Hubs](event-hubs-features.md).
- [Vanliga frågor och svar om Event Hubs](event-hubs-faq.yml)


<!-- Links -->
[EventProcessorHost]: /dotnet/api/microsoft.servicebus.messaging.eventprocessorhost
[Event Hubs overview]: event-hubs-about.md
[Event Hubs Programming Guide]: event-hubs-programming-guide.md
[Azure Storage account]:../storage/common/storage-create-storage-account.md
[Event Processor Host]: event-hubs-event-processor-host.md
[Azure portal]: https://portal.azure.com
