---
title: Skicka eller ta emot händelser Azure Event Hubs med .NET (senaste)
description: Den här artikeln innehåller en genomgång för att skapa ett .NET Core-program som skickar/tar emot händelser till/från Azure Event Hubs med hjälp av det senaste Azure.Messaging.EventHubs-paketet.
ms.topic: quickstart
ms.date: 09/25/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 58da331336481614cf0f85bdf6c1136c8bdc8db7
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536504"
---
# <a name="send-events-to-and-receive-events-from-azure-event-hubs---net-azuremessagingeventhubs"></a>Skicka händelser till och ta emot händelser Azure Event Hubs .NET (Azure.Messaging.EventHubs) 
Den här snabbstarten visar hur du skickar händelser till och tar emot händelser från en händelsehubb med hjälp av .NET-biblioteket **Azure.Messaging.EventHubs.** 

> [!IMPORTANT]
> I den här snabbstarten används **det nya Biblioteket Azure.Messaging.EventHubs.** En snabbstart som använder det gamla **Microsoft.Azure.EventHubs-biblioteket** finns i Skicka och ta emot händelser med hjälp av [Microsoft.Azure.EventHubs-biblioteket](event-hubs-dotnet-standard-get-started-send-legacy.md). 



## <a name="prerequisites"></a>Förutsättningar
Om du inte har börjat använda Azure Event Hubs bör du Event Hubs [översikt innan](event-hubs-about.md) du gör den här snabbstarten. 

För att slutföra den här snabbstarten, behöver du följande förhandskrav:

- **Microsoft Azure prenumeration**. Om du vill använda Azure-Azure Event Hubs måste du ha en prenumeration.  Om du inte har ett befintligt Azure-konto kan [](https://azure.microsoft.com/free/) du registrera dig för en kostnadsfri utvärderingsversion eller använda dina MSDN-prenumerantförmåner när [du skapar ett konto.](https://azure.microsoft.com)
- **Microsoft Visual Studio 2019**. I Azure Event Hubs-klientbiblioteket används nya funktioner som introducerades i C# 8.0.  Du kan fortfarande använda biblioteket med tidigare C#-språkversioner, men den nya syntaxen är inte tillgänglig. Om du vill använda den fullständiga syntaxen rekommenderar vi att du kompilerar [med .NET Core SDK](https://dotnet.microsoft.com/download) 3.0 eller senare och [språkversionen](/dotnet/csharp/language-reference/configure-language-version#override-a-default) inställd på `latest` . Om du använder Visual Studio är versioner före Visual Studio 2019 inte kompatibla med de verktyg som behövs för att skapa C# 8.0-projekt. Visual Studio 2019, inklusive den kostnadsfria Community Edition, kan laddas ned [här.](https://visualstudio.microsoft.com/vs/)
- **Skapa en Event Hubs namnrymd och en händelsehubb**. Det första steget är att använda [Azure Portal](https://portal.azure.com) för att skapa ett namnområde av typen Event Hubs och hämta de autentiseringsuppgifter för hantering som programmet behöver för att kommunicera med händelsehubben. Om du behöver skapa ett namnområde och en händelsehubb följer du anvisningarna i [den här artikeln](event-hubs-create.md). Hämta sedan **anslutningssträngen för Event Hubs genom** att följa anvisningarna i artikeln: [Hämta anslutningssträngen](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Du använder anslutningssträngen senare i den här snabbstarten.

## <a name="send-events"></a>Skicka händelser 
Det här avsnittet visar hur du skapar ett .NET Core-konsolprogram för att skicka händelser till en händelsehubb. 

### <a name="create-a-console-application"></a>Skapa ett konsolprogram

1. Starta Visual Studio 2019. 
1. Välj **Skapa ett nytt projekt.** 
1. I **dialogrutan Skapa** ett nytt projekt gör du följande: Om du inte  ser den här dialogrutan väljer du Arkiv på menyn, väljer **Nytt** och sedan **Projekt.** 
    1. Välj **C#** som programmeringsspråk.
    1. Välj **Konsol** som typ av program. 
    1. Välj **Konsolapp (.NET Core)** i resultatlistan. 
    1. Välj **Nästa**. 

        ![Dialogrutan Nytt projekt](./media/getstarted-dotnet-standard-send-v2/new-send-project.png)    
1. Ange **EventHubsSender** som projektnamn, **EventHubsQuickStart** som lösningsnamn och välj **sedan OK** för att skapa projektet. 

    ![C# > Console-app](./media/getstarted-dotnet-standard-send-v2/project-solution-names.png)

### <a name="add-the-event-hubs-nuget-package"></a>Lägga till Event Hubs NuGet-paketet

1. Välj **Verktyg**  >  **NuGet Package Manager** Package Manager  >  **Console** på menyn. 
1. Kör följande kommando för att installera **NuGet-paketet Azure.Messaging.EventHubs:**

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```


### <a name="write-code-to-send-messages-to-the-event-hub"></a>Skriva kod för att skicka meddelanden till händelsehubben

1. Lägg till följande `using` -instruktioner överst i **filen Program.cs:**

    ```csharp
    using System;
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Producer;
    ```

2. Lägg till konstanter i `Program` klassen för Event Hubs anslutningssträngen och händelsehubbens namn. Ersätt platshållarna inom hakparentes med rätt värden som du fick när du skapade händelsehubben. Kontrollera att `{Event Hubs namespace connection string}` är anslutningssträngen på namnrymdsnivå och inte händelsehubbens sträng. 

    ```csharp
    private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
    private const string eventHubName = "<EVENT HUB NAME>";
    ```

3. Ersätt `Main` metoden med följande `async Main` metod. Mer information finns i kodkommentarerna. 

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
5. Skapa projektet och se till att det inte finns några fel.
6. Kör programmet och vänta på bekräftelsemeddelandet. 
7. I Azure Portal kan du kontrollera att händelsehubben har tagit emot meddelandena. Växla till **vyn** Meddelanden i **avsnittet** Mått. Uppdatera sidan för att uppdatera diagrammet. Det kan ta några sekunder för den att visa att meddelandena har tagits emot. 

    [![Kontrollera att händelsehubben tog emot meddelandena](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png)](./media/getstarted-dotnet-standard-send-v2/verify-messages-portal.png#lightbox)

    > [!NOTE]
    > Den fullständiga källkoden med fler informationskommentarer finns [i den här filen på GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample04_PublishingEvents.md)

## <a name="receive-events"></a>Ta emot händelser
Det här avsnittet visar hur du skriver ett .NET Core-konsolprogram som tar emot meddelanden från en händelsehubb med hjälp av en händelseprocessor. Händelseprocessorn förenklar mottagandet av händelser från händelsehubbbar genom att hantera beständiga kontrollpunkter och parallella mottagningar från dessa händelsehubbbar. En händelseprocessor är associerad med en specifik händelsehubb och en konsumentgrupp. Den tar emot händelser från flera partitioner i händelsehubben och skicka dem till en hanterardelegat för bearbetning med hjälp av kod som du anger. 


> [!WARNING]
> Om du kör den här koden Azure Stack Hub får du körningsfel om du inte har en specifik Storage API-version som mål. Det beror på att Event Hubs SDK använder det senaste tillgängliga AZURE STORAGE-API:et som är tillgängligt i Azure och som kanske inte är tillgängligt på din Azure Stack Hub plattform. Azure Stack Hub kan ha stöd för en annan version av Storage Blob SDK än de som vanligtvis är tillgängliga i Azure. Om du använder Azure Blob Storage som ett kontrollpunktslager kontrollerar du vilken API Azure Storage version som stöds för ditt [Azure Stack Hub](/azure-stack/user/azure-stack-acs-differences?#api-version) och riktar in dig på den versionen i koden. 
>
> Om du till exempel kör på Azure Stack Hub version 2005 är den högsta tillgängliga versionen för lagringstjänsten version 2019-02-02. Som standard använder Event Hubs SDK-klientbiblioteket den högsta tillgängliga versionen i Azure (2019-07-07 vid tidpunkten för SDK-versionen). I det här fallet måste du, förutom att följa stegen i det här avsnittet, även lägga till kod som mål för STORAGE-tjänstens API-version 2019-02-02. Ett exempel på hur du riktar en specifik Storage API-version finns i [det här exemplet på GitHub.](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/) 
 

### <a name="create-an-azure-storage-and-a-blob-container"></a>Skapa en Azure Storage och en blobcontainer
I den här snabbstarten använder du Azure Storage som kontrollpunktslager. Följ dessa steg för att skapa ett Azure Storage konto. 

1. [Skapa ett Azure Storage-konto](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Skapa en blobcontainer](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Hämta anslutningssträngen till lagringskontot](../storage/common/storage-configure-connection-string.md)

    Anteckna anslutningssträngen och containernamnet. Du använder dem i mottagningskoden. 


### <a name="create-a-project-for-the-receiver"></a>Skapa ett projekt för mottagaren

1. I fönstret Solution Explorer högerklickar du på **EventHubQuickStart-lösningen,** pekar på **Lägg till** och väljer Nytt **projekt.** 
1. Välj **Konsolapp (.NET Core)** och sedan **Nästa.** 
1. Ange **EventHubsReceiver** som **projektnamn** och välj **Skapa.** 

### <a name="add-the-event-hubs-nuget-package"></a>Lägga till Event Hubs NuGet-paketet

1. Välj **Verktyg**  >  **NuGet Package Manager** Package Manager  >  **Console** på menyn. 
1. Kör följande kommando för att installera **NuGet-paketet Azure.Messaging.EventHubs:**

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```
1. Kör följande kommando för att installera **NuGet-paketet Azure.Messaging.EventHubs.Processor:**

    ```cmd
    Install-Package Azure.Messaging.EventHubs.Processor
    ```    

### <a name="update-the-main-method"></a>Uppdatera Main-metoden 

1. Lägg till följande `using` -instruktioner överst i **filen Program.cs.**

    ```csharp
    using System;
    using System.Text;
    using System.Threading.Tasks;
    using Azure.Storage.Blobs;
    using Azure.Messaging.EventHubs;
    using Azure.Messaging.EventHubs.Consumer;
    using Azure.Messaging.EventHubs.Processor;
    ```
1. Lägg till konstanter i `Program` klassen för Event Hubs anslutningssträngen och händelsehubbens namn. Ersätt platshållarna inom hakparentes med rätt värden som du fick när du skapade händelsehubben. Ersätt platshållarna inom hakparentes med rätt värden som du fick när du skapade händelsehubben och lagringskontot (åtkomstnycklar – primär anslutningssträng). Kontrollera att `{Event Hubs namespace connection string}` är anslutningssträngen på namnrymdsnivå och inte händelsehubbens sträng.

    ```csharp
        private const string ehubNamespaceConnectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
        private const string eventHubName = "<EVENT HUB NAME>";
        private const string blobStorageConnectionString = "<AZURE STORAGE CONNECTION STRING>";
        private const string blobContainerName = "<BLOB CONTAINER NAME>";
    ```
3. Ersätt `Main` metoden med följande `async Main` metod. Mer information finns i kodkommentarerna. 

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

            // Wait for 30 seconds for the events to be processed
            await Task.Delay(TimeSpan.FromSeconds(30));

            // Stop the processing
            await processor.StopProcessingAsync();
        }    
    ```
1. Lägg nu till följande metoder för händelse- och felhanterare i klassen . 

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
1. Skapa projektet och se till att det inte finns några fel.

    > [!NOTE]
    > Den fullständiga källkoden med mer informationskommentarer finns i [den här filen på GitHub.](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/Sample01_HelloWorld.md)
6. Kör mottagarprogrammet. 
1. Du bör se ett meddelande om att händelsen har tagits emot. 

    ![Mottagen händelse](./media/getstarted-dotnet-standard-send-v2/event-received.png)

    Dessa händelser är de tre händelser som du tidigare skickade till händelsehubben genom att köra avsändarprogrammet. 


## <a name="next-steps"></a>Nästa steg
Kolla in exemplen på GitHub. 

- [Event Hubs på GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [Exempel på händelseprocessorer på GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples)
- [Exempel på rollbaserad åtkomstkontroll i Azure (Azure RBAC)](https://github.com/Azure/azure-event-hubs/tree/master/samples/DotNet/Azure.Messaging.EventHubs/ManagedIdentityWebApp)
