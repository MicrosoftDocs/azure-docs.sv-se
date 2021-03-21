---
title: Skicka eller ta emot händelser från Azure Event Hubs med .NET (senaste)
description: Den här artikeln innehåller en genom gång av hur du skapar ett .NET Core-program som skickar/tar emot händelser till/från Azure Event Hubs med hjälp av det senaste Azure. Messaging. EventHubs-paketet.
ms.topic: quickstart
ms.date: 09/25/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 36551a52bc095a37aa5176ab945dd5d27c640954
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97504618"
---
# <a name="send-events-to-and-receive-events-from-azure-event-hubs---net-azuremessagingeventhubs"></a>Skicka händelser till och ta emot händelser från Azure Event Hubs-.NET (Azure. Messaging. EventHubs) 
Den här snabb starten visar hur du skickar händelser till och tar emot händelser från en händelsehubben med hjälp av **Azure. Messaging. EventHubs** .net-biblioteket. 

> [!IMPORTANT]
> Den här snabb starten använder det nya **Azure. Messaging. EventHubs** -biblioteket. En snabb start som använder det gamla **Microsoft. Azure. EventHubs** -biblioteket finns i [skicka och ta emot händelser med hjälp av Microsoft. Azure. EventHubs-biblioteket](event-hubs-dotnet-standard-get-started-send-legacy.md). 



## <a name="prerequisites"></a>Förutsättningar
Om du inte har använt Azure Event Hubs tidigare, se [Event Hubs översikt](event-hubs-about.md) innan du gör den här snabb starten. 

För att slutföra den här snabbstarten, behöver du följande förhandskrav:

- **Microsoft Azure prenumeration**. Om du vill använda Azure-tjänster, inklusive Azure Event Hubs, behöver du en prenumeration.  Om du inte har ett befintligt Azure-konto kan du registrera dig för en [kostnads fri utvärderings version](https://azure.microsoft.com/free/) eller använda dina förmåner för MSDN-prenumeranter när du [skapar ett konto](https://azure.microsoft.com).
- **Microsoft Visual Studio 2019**. Klient biblioteket för Azure Event Hubs använder nya funktioner som introducerades i C# 8,0.  Du kan fortfarande använda biblioteket med tidigare C#-språk versioner, men den nya syntaxen är inte tillgänglig. Om du vill använda fullständig syntax rekommenderar vi att du kompilerar med [.NET Core SDK](https://dotnet.microsoft.com/download) 3,0 eller högre och [språk versionen](/dotnet/csharp/language-reference/configure-language-version#override-a-default) har angetts till `latest` . Om du använder Visual Studio kan du inte använda de verktyg som krävs för att bygga C# 8,0-projekt i Visual Studio 2019. Visual Studio 2019, inklusive den kostnads fria community-versionen, kan hämtas [här](https://visualstudio.microsoft.com/vs/).
- **Skapa ett Event Hubs-namnområde och en Event Hub**. Det första steget är att använda [Azure Portal](https://portal.azure.com) för att skapa ett namn område av typen Event Hubs och hämta de autentiseringsuppgifter som programmet behöver för att kommunicera med händelsehubben. Om du behöver skapa ett namnområde och en händelsehubb följer du anvisningarna i [den här artikeln](event-hubs-create.md). Hämta sedan **anslutnings strängen för Event Hubs namn området genom att** följa anvisningarna i artikeln: [Hämta anslutnings sträng](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Du använder anslutnings strängen senare i den här snabb starten.

## <a name="send-events"></a>Skicka händelser 
Det här avsnittet visar hur du skapar ett .NET Core-konsolprogram för att skicka händelser till en händelsehubben. 

### <a name="create-a-console-application"></a>Skapa ett konsolprogram

1. Starta Visual Studio 2019. 
1. Välj **skapa ett nytt projekt**. 
1. I dialog rutan **skapa ett nytt projekt** gör du följande: om du inte ser den här dialog rutan väljer du **fil** på menyn, väljer **ny** och väljer sedan **projekt**. 
    1. Välj **C#** som programmeringsspråk.
    1. Välj **konsol** för typen av program. 
    1. Välj **konsol program (.net Core)** i resultat listan. 
    1. Välj **Nästa**. 

        ![Dialogrutan Nytt projekt](./media/getstarted-dotnet-standard-send-v2/new-send-project.png)    
1. Ange **EventHubsSender** som projekt namn, **EventHubsQuickStart** för lösningens namn och välj sedan **OK** för att skapa projektet. 

    ![C# >-konsol program](./media/getstarted-dotnet-standard-send-v2/project-solution-names.png)

### <a name="add-the-event-hubs-nuget-package"></a>Lägga till Event Hubs NuGet-paketet

1. Välj **verktyg**  >  **NuGet Package Manager**  >  **Package Manager-konsolen** på menyn. 
1. Kör följande kommando för att installera paketet **Azure. Messaging. EventHubs** NuGet:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```


### <a name="write-code-to-send-messages-to-the-event-hub"></a>Skriva kod för att skicka meddelanden till händelsehubben

1. Lägg till följande- `using` instruktioner överst i filen **program. cs** :

    ```csharp
    using System;
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Producer;
    ```

2. Lägg till konstanter i `Program` klassen för Event Hubs anslutnings sträng och händelsehubben-namn. Ersätt plats hållare inom hak paren tes med rätt värden som du fick när du skapade händelsehubben. Kontrollera att `{Event Hubs namespace connection string}` är anslutningssträngen på namnrymdsnivå och inte händelsehubbens sträng. 

    ```csharp
    private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
    private const string eventHubName = "<EVENT HUB NAME>";
    ```

3. Ersätt `Main` metoden med följande `async Main` metod. Se kod kommentarer för mer information. 

    ```csharp
        static async Task Main()
        {
            // Create a producer client that you can use to send events to an event hub
            await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
            {
                // Create a batch of events 
                using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

                // Add events to the batch. An event is a represented by a collection of bytes and metadata. 
                eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First event")));
                eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second event")));
                eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Third event")));

                // Use the producer client to send the batch of events to the event hub
                await producerClient.SendAsync(eventBatch);
                Console.WriteLine("A batch of 3 events has been published.");
            }
        }
    ```
5. Bygg projektet och se till att det inte finns några fel.
6. Kör programmet och vänta på bekräftelse meddelandet. 
7. I Azure Portal kan du kontrol lera att händelsehubben har tagit emot meddelandena. Växla till vyn **meddelanden** i avsnittet **mått** . Uppdatera sidan för att uppdatera diagrammet. Det kan ta några sekunder innan det visar att meddelandena har mottagits. 

    [![Verifiera att händelsehubben tog emot meddelanden](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > Den fullständiga käll koden med mer informations kommentarer finns i [den här filen på GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample04_PublishingEvents.md)

## <a name="receive-events"></a>Ta emot händelser
Det här avsnittet visar hur du skriver ett .NET Core-konsolprogram som tar emot meddelanden från en Event Hub med en händelse processor. Händelse processorn fören klar mottagandet av händelser från Event Hub genom att hantera permanenta kontroll punkter och parallella mottagningar från dessa händelse nav. En händelse processor är associerad med en speciell händelsehubben och en konsument grupp. Den tar emot händelser från flera partitioner i händelsehubben och skickar dem till en hanterare som delegeras för bearbetning med hjälp av kod som du anger. 


> [!WARNING]
> Om du kör den här koden på Azure Stack hubb får du körnings fel om du inte riktar in dig på en viss lagrings-API-version. Det beror på att Event Hubs SDK använder det senaste tillgängliga Azure Storage API som är tillgängligt i Azure och som kanske inte är tillgängligt på din Azure Stack Hub-plattform. Azure Stack Hub kan stödja en annan version av Storage BLOB SDK än vad som normalt är tillgängligt på Azure. Om du använder Azure Blob Storage som kontroll punkts lager, kontrollerar du [Azure Storage API-versionen som stöds för din Azure Stack Hub](/azure-stack/user/azure-stack-acs-differences?#api-version) -version och aktiverar den versionen i din kod. 
>
> Om du till exempel kör på Azure Stack Hub version 2005 är den högsta tillgängliga versionen för lagrings tjänsten version 2019-02-02. Som standard använder klient biblioteket för Event Hubs SDK den senaste tillgängliga versionen på Azure (2019-07-07 vid tidpunkten för lanseringen av SDK: n). I det här fallet, förutom följande steg i det här avsnittet, måste du också lägga till kod som mål för Storage Service API-versionen 2019-02-02. Ett exempel på hur du riktar in en speciell Storage API-version finns i [det här exemplet på GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/). 
 

### <a name="create-an-azure-storage-and-a-blob-container"></a>Skapa en Azure Storage och en BLOB-behållare
I den här snabb starten använder du Azure Storage som kontroll punkts arkiv. Följ dessa steg om du vill skapa ett Azure Storage-konto. 

1. [Skapa ett Azure Storage-konto](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Skapa en blobcontainer](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Hämta anslutnings strängen till lagrings kontot](../storage/common/storage-configure-connection-string.md)

    Anteckna anslutnings strängen och behållar namnet. Du ska använda dem i Receive-koden. 


### <a name="create-a-project-for-the-receiver"></a>Skapa ett projekt för mottagaren

1. I fönstret Solution Explorer högerklickar du på lösningen **EventHubQuickStart** , pekar på **Lägg till** och väljer **nytt projekt**. 
1. Välj **konsol program (.net Core)** och välj **Nästa**. 
1. Ange **EventHubsReceiver** som **projekt namn** och välj **skapa**. 

### <a name="add-the-event-hubs-nuget-package"></a>Lägga till Event Hubs NuGet-paketet

1. Välj **verktyg**  >  **NuGet Package Manager**  >  **Package Manager-konsolen** på menyn. 
1. Kör följande kommando för att installera paketet **Azure. Messaging. EventHubs** NuGet:

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```
1. Kör följande kommando för att installera paketet **Azure. Messaging. EventHubs. processor** NuGet:

    ```cmd
    Install-Package Azure.Messaging.EventHubs.Processor
    ```    

### <a name="update-the-main-method"></a>Uppdatera main-metoden 

1. Lägg till följande- `using` instruktioner överst i filen **program. cs** .

    ```csharp
    using System;
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Storage.Blobs;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Consumer;
    using Azure.Messaging.EventHubs.Processor;
    ```
1. Lägg till konstanter i `Program` klassen för Event Hubs anslutnings sträng och händelsehubben-namn. Ersätt plats hållare inom hak paren tes med rätt värden som du fick när du skapade händelsehubben. Ersätt plats hållare inom hak paren tes med rätt värden som du fick när du skapade händelsehubben och lagrings kontot (åtkomst nycklar – primär anslutnings sträng). Kontrollera att `{Event Hubs namespace connection string}` är anslutningssträngen på namnrymdsnivå och inte händelsehubbens sträng.

    ```csharp
        private const string ehubNamespaceConnectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
        private const string eventHubName = "<EVENT HUB NAME>";
        private const string blobStorageConnectionString = "<AZURE STORAGE CONNECTION STRING>";
        private const string blobContainerName = "<BLOB CONTAINER NAME>";
    ```
3. Ersätt `Main` metoden med följande `async Main` metod. Se kod kommentarer för mer information. 

    ```csharp
        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;

            // Create a blob container client that the event processor will use 
            BlobContainerClient storageClient = new BlobContainerClient(blobStorageConnectionString, blobContainerName);

            // Create an event processor client to process events in the event hub
            EventProcessorClient processor = new EventProcessorClient(storageClient, consumerGroup, ehubNamespaceConnectionString, eventHubName);

            // Register handlers for processing events and handling errors
            processor.ProcessEventAsync += ProcessEventHandler;
            processor.ProcessErrorAsync += ProcessErrorHandler;

            // Start the processing
            await processor.StartProcessingAsync();

            // Wait for 10 seconds for the events to be processed
            await Task.Delay(TimeSpan.FromSeconds(10));

            // Stop the processing
            await processor.StopProcessingAsync();
        }    
    ```
1. Lägg nu till följande händelse-och fel hanterings metoder i klassen. 

    ```csharp
        static async Task ProcessEventHandler(ProcessEventArgs eventArgs)
        {
            // Write the body of the event to the console window
            Console.WriteLine("\tReceived event: {0}", Encoding.UTF8.GetString(eventArgs.Data.Body.ToArray()));

            // Update checkpoint in the blob storage so that the app receives only new events the next time it's run
            await eventArgs.UpdateCheckpointAsync(eventArgs.CancellationToken);
        }

        static Task ProcessErrorHandler(ProcessErrorEventArgs eventArgs)
        {
            // Write details about the error to the console window
            Console.WriteLine($"\tPartition '{ eventArgs.PartitionId}': an unhandled exception was encountered. This was not expected to happen.");
            Console.WriteLine(eventArgs.Exception.Message);
            return Task.CompletedTask;
        }    
    ```
1. Bygg projektet och se till att det inte finns några fel.

    > [!NOTE]
    > Den fullständiga käll koden med mer informations kommentarer finns i [den här filen på GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample01_HelloWorld.md).
6. Kör mottagarens program. 
1. Du bör se ett meddelande om att händelsen har mottagits. 

    ![Händelse mottagen](./media/getstarted-dotnet-standard-send-v2/event-received.png)

    Dessa händelser är de tre händelser som du skickade till händelsehubben tidigare genom att köra avsändar programmet. 


## <a name="next-steps"></a>Nästa steg
Kolla in exemplen på GitHub. 

- [Event Hubs exempel på GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [Event processor-exempel på GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples)
- [Azure-rollbaserad åtkomst kontroll (Azure RBAC)-exempel](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
