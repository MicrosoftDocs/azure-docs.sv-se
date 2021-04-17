---
title: Publicera meddelanden till och bearbeta meddelanden från Atlas Kafka-ämnena i Azure Purview via Event Hubs med hjälp av .NET
description: Den här artikeln innehåller en genomgång för att skapa ett .NET Core-program som skickar/tar emot händelser till/från Purviews Apache Atlas Kafka-ämnen med hjälp av det senaste Azure.Messaging.EventHubs-paketet.
ms.topic: quickstart
author: nayenama
ms.author: nayenama
ms.service: purview
ms.subservice: purview-data-catalog
ms.devlang: dotnet
ms.date: 04/15/2021
ms.openlocfilehash: 60c177c913c78dbcfcbfe1d465044b69b0e6dd2e
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590439"
---
# <a name="publish-messages-to-and-process-messages-from-azure-purviews-atlas-kafka-topics-via-event-hubs-using-net"></a>Publicera meddelanden till och bearbeta meddelanden från Atlas Kafka-ämnena i Azure Purview via Event Hubs med hjälp av .NET 
Den här snabbstarten visar hur du skickar händelser till och tar emot händelser från Atlas Kafka-ämnena i Azure Purview via händelsehubben med hjälp av **Azure.Messaging.EventHubs** .NET-biblioteket. 

> [!IMPORTANT]
> En hanterad händelsehubb skapas som en del av skapandet av Purview-kontot. Se [Skapa Purview-konto.](create-catalog-portal.md) Du kan publicera meddelanden till Kafka-ämnet för händelsehubben ATLAS_HOOK Purview kommer att använda och bearbeta det. Purview meddelar entitetsändringar i Kafka-ämnet för ATLAS_ENTITIES och användaren kan använda och bearbeta den. I den här snabbstarten används **det nya biblioteket Azure.Messaging.EventHubs.** 


## <a name="prerequisites"></a>Förutsättningar
Om du inte har börjat använda Azure Event Hubs kan du gå [Event Hubs översikt innan](../event-hubs/event-hubs-about.md) du gör den här snabbstarten. 

För att slutföra den här snabbstarten, behöver du följande förhandskrav:

- **Microsoft Azure prenumeration**. Om du vill använda Azure-Azure Event Hubs måste du ha en prenumeration.  Om du inte har ett befintligt Azure-konto kan [](https://azure.microsoft.com/free/) du registrera dig för en kostnadsfri utvärderingsversion eller använda dina MSDN-prenumerantförmåner när [du skapar ett konto.](https://azure.microsoft.com)
- **Microsoft Visual Studio 2019**. I Azure Event Hubs-klientbiblioteket används nya funktioner som introducerades i C# 8.0.  Du kan fortfarande använda biblioteket med tidigare C#-språkversioner, men den nya syntaxen är inte tillgänglig. Om du vill använda den fullständiga syntaxen rekommenderar vi att du kompilerar [med .NET Core SDK](https://dotnet.microsoft.com/download) 3.0 eller senare och [språkversionen](/dotnet/csharp/language-reference/configure-language-version#override-a-default) är inställd på `latest` . Om du använder Visual Studio är versioner före Visual Studio 2019 inte kompatibla med de verktyg som behövs för att skapa C# 8.0-projekt. Visual Studio 2019, inklusive den kostnadsfria Community Edition, kan laddas ned [här.](https://visualstudio.microsoft.com/vs/)

## <a name="publish-messages-to-purview"></a>Publicera meddelanden till Purview 
Det här avsnittet visar hur du skapar ett .NET Core-konsolprogram för att skicka händelser till ett Purview via kafka-ämne för **händelsehubben ATLAS_HOOK**. 

## <a name="create-a-visual-studio-project"></a>Skapa ett Visual Studio-projekt

Skapa sedan ett C# .NET-konsolprogram i Visual Studio:

1. Starta **Visual Studio**.
2. I startfönstret väljer du **Skapa ett nytt projekt**  >  **konsolapp (.NET Framework).** .NET version 4.5.2 eller senare krävs.
3. I **Projektnamn** anger du **PurviewKafkaPurr**.
4. Välj **Skapa** för att skapa projektet.

### <a name="create-a-console-application"></a>Skapa ett konsolprogram

1. Starta Visual Studio 2019. 
1. Välj **Skapa ett nytt projekt.** 
1. I **dialogrutan Skapa** ett nytt projekt gör du följande: Om du inte  ser den här dialogrutan väljer du Arkiv på menyn, väljer **Nytt** och sedan **Projekt.** 
    1. Välj **C#** som programmeringsspråk.
    1. Välj **Konsol** som typ av program. 
    1. Välj **Konsolapp (.NET Core)** i resultatlistan. 
    1. Välj **Nästa**. 


### <a name="add-the-event-hubs-nuget-package"></a>Lägga till Event Hubs NuGet-paketet

1. Välj **Verktyg**  >  **NuGet Package Manager** Package Manager  >  **Console** på menyn. 
1. Kör följande kommando för att installera **NuGet-paketet Azure.Messaging.EventHubs** och NuGet-paketet **Azure.Messaging.EventHubs.Producer:**

    ```cmd
    Install-Package Azure.Messaging.EventHubs
    ```
    
    ```cmd
    Install-Package Azure.Messaging.EventHubs.Producer
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

2. Lägg till konstanter i `Program` klassen för Event Hubs anslutningssträngen och event hub-namnet. 

    ```csharp
    private const string connectionString = "<EVENT HUBS NAMESPACE - CONNECTION STRING>";
    private const string eventHubName = "<EVENT HUB NAME>";
    ```

    Du kan hämta händelsehubbens namnområde som är associerat med Purview-kontot genom att titta på Atlas Kafka-slutpunktens primära/sekundära anslutningssträngar på egenskapsfliken för Purview-kontot.

    :::image type="content" source="media/manage-eventhub-kafka-dotnet/properties.png" alt-text="Namnområde för händelsehubb":::

    Händelsehubbens namn ska vara **ATLAS_HOOK** för att skicka meddelanden till Purview.

3. Ersätt metoden `Main` med följande metod och lägg till en för att skicka meddelanden till `async Main` `async ProduceMessage` Purview. Mer information finns i kodkommentarerna. 

    ```csharp
        static async Task Main()
        {
            // Read from the default consumer group: $Default
            string consumerGroup = EventHubConsumerClient.DefaultConsumerGroupName;
            
            / Create an event producer client to add events in the event hub
            EventHubProducerClient producer = new EventHubProducerClient(ehubNamespaceConnectionString, eventHubName);
            
            await ProduceMessage(producer);
        }
        
        static async Task ProduceMessage(EventHubProducerClient producer)
     
        {
            // Create a batch of events 
            using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

            // Add events to the batch. An event is a represented by a collection of bytes and metadata. 
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("<First event>")));
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("<Second event>")));
            eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("<Third event>")));

            // Use the producer client to send the batch of events to the event hub
            await producerClient.SendAsync(eventBatch);
            Console.WriteLine("A batch of 3 events has been published.");
             
        }
    ```
5. Skapa projektet och se till att det inte finns några fel.
6. Kör programmet och vänta på bekräftelsemeddelandet. 

    > [!NOTE]
    > Den fullständiga källkoden med fler informationskommentarer finns [i den här filen på GitHub](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/samples/Sample04_PublishingEvents.md)

### <a name="sample-create-entity-json-message-to-create-a-sql-table-with-two-columns"></a>Exempel på JSON-meddelande för att skapa en sql-tabell med två kolumner.

```json
    
    {
    "msgCreatedBy": "nayenama",
    "message": {
    "entities": {
    "referredEntities": {
        "-1102395743156037": {
            "typeName": "azure_sql_column",
            "attributes": {
                "owner": null,
                "userTypeId": 61,
                "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderID",
                "precision": 23,
                "length": 8,
                "description": "Sales Order ID",
                "scale": 3,
                "name": "OrderID",
                "data_type": "int",
                "table": {
                    "guid": "-1102395743156036",
                    "typeName": "azure_sql_table",
                    "entityStatus": "ACTIVE",
                    "displayText": "SalesOrderTable",
                    "uniqueAttributes": {
                                "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable"
                    }
            }
            },
            "guid": "-1102395743156037",
            "version": 2
        },
        "-1102395743156038": {
         "typeName": "azure_sql_column",
            "attributes": {
                "owner": null,
                "userTypeId": 61,
                "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderDate",
                "description": "Sales Order Date",
                "scale": 3,
                "name": "OrderDate",
                "data_type": "datetime",
                "table": {
                    "guid": "-1102395743156036",
                    "typeName": "azure_sql_table",
                    "entityStatus": "ACTIVE",
                    "displayText": "SalesOrderTable",
                    "uniqueAttributes": {
                                "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable"
                    }
            }
            },
            "guid": "-1102395743156038",
            "status": "ACTIVE",
            "createdBy": "ServiceAdmin",
            "version": 0
        }
        },
        "entity": 
                {
                    "typeName": "azure_sql_table",
                    "attributes": {
                        "owner": "admin",
                        "temporary": false,
                        "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable",
                        "name" : "SalesOrderTable",
                        "description": "Sales Order Table added via Kafka",
                        "columns": [
                            {
                                "guid": "-1102395743156037",
                                "typeName": "azure_sql_column",
                                "uniqueAttributes": {
                                    "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderID"
                                }
                            },
                            {
                                "guid": "-1102395743156038",
                                "typeName": "azure_sql_column",
                                "uniqueAttributes": {
                                    "qualifiedName": "mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable#OrderDate"
                                }
                            }
                        ]
                        },
                        "guid": "-1102395743156036",
                    "version": 0                
                    }
                },
        "type": "ENTITY_CREATE_V2",
        "user": "admin"
    },
    "version": {
        "version": "1.0.0"
    },
    "msgCompressionKind": "NONE",
    "msgSplitIdx": 1,
    "msgSplitCount": 1
}

``` 

## <a name="consume-messages-from-purview"></a>Använda meddelanden från Purview
Det här avsnittet visar hur du skriver ett .NET Core-konsolprogram som tar emot meddelanden från en händelsehubb med hjälp av en händelseprocessor. Du måste använda ATLAS_ENTITIES händelsehubb för att ta emot meddelanden från Purview.Händelseprocessorn gör det enklare att ta emot händelser från händelsehubbbar genom att hantera permanenta kontrollpunkter och parallella mottaganden från dessa händelsehubbbar. 

> [!WARNING]
> Om du kör den här koden på Azure Stack Hub får du körningsfel om du inte riktar in dig på en specifik Storage API-version. Det beror på att Event Hubs SDK använder det senaste tillgängliga AZURE STORAGE-API:et i Azure som kanske inte är tillgängligt på din Azure Stack Hub plattform. Azure Stack Hub kan ha stöd för en annan version av Storage Blob SDK än de som vanligtvis är tillgängliga i Azure. Om du använder Azure Blob Storage som kontrollpunktslager kontrollerar du vilken [API Azure Storage version](/azure-stack/user/azure-stack-acs-differences?#api-version) som stöds för ditt Azure Stack Hub och riktar in dig på den versionen i koden. 
>
> Om du till exempel kör på Azure Stack Hub version 2005 är den högsta tillgängliga versionen för Storage-tjänsten version 2019-02-02. Som standard använder Event Hubs SDK-klientbiblioteket den högsta tillgängliga versionen i Azure (2019-07-07 vid tidpunkten för SDK-versionen). I det här fallet måste du, förutom att följa stegen i det här avsnittet, även lägga till kod för att rikta in dig på STORAGE-tjänstens API-version 2019-02-02. Ett exempel på hur du riktar en specifik Storage API-version finns i [det här exemplet på GitHub.](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/) 
 

### <a name="create-an-azure-storage-and-a-blob-container"></a>Skapa en Azure Storage och en blobcontainer
I den här snabbstarten använder du Azure Storage som kontrollpunktslager. Följ de här stegen för att skapa Azure Storage konto. 

1. [Skapa ett Azure Storage-konto](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Skapa en blobcontainer](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Hämta anslutningssträngen till lagringskontot](../storage/common/storage-configure-connection-string.md)

    Anteckna anslutningssträngen och containernamnet. Du använder dem i mottagningskoden. 


### <a name="create-a-project-for-the-receiver"></a>Skapa ett projekt för mottagaren

1. I fönstret Solution Explorer högerklickar du på **EventHubQuickStart-lösningen,** pekar på **Lägg till** och väljer Nytt **projekt.** 
1. Välj **Konsolapp (.NET Core)** och välj **Nästa.** 
1. Ange **PurviewKafkaConsumer** som **projektnamn** och välj **Skapa.** 

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
    
    Du kan hämta händelsehubbens namnområde som är associerat med Purview-kontot genom att titta på Atlas Kafka-slutpunktens primära/sekundära anslutningssträngar på egenskapsfliken för Purview-kontot.

    :::image type="content" source="media/manage-eventhub-kafka-dotnet/properties.png" alt-text="Namnområde för händelsehubb":::

    Namnet på händelsehubben ska vara **ATLAS_ENTITIES** för att skicka meddelanden till Purview.

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

            // Wait for 10 seconds for the events to be processed
            await Task.Delay(TimeSpan.FromSeconds(10));

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

### <a name="sample-message-received-from-purview"></a>Exempelmeddelande som tagits emot från Purview

```json
{
    "version":
        {"version":"1.0.0",
         "versionParts":[1]
        },
         "msgCompressionKind":"NONE",
         "msgSplitIdx":1,
         "msgSplitCount":1,
         "msgSourceIP":"10.244.155.5",
         "msgCreatedBy":
         "",
         "msgCreationTime":1618588940869,
         "message":{
            "type":"ENTITY_NOTIFICATION_V2",
            "entity":{
                "typeName":"azure_sql_table",
                    "attributes":{
                        "owner":"admin",
                        "createTime":0,
                        "qualifiedName":"mssql://nayenamakafka.eventhub.sql.net/salespool/dbo/SalesOrderTable",
                        "name":"SalesOrderTable",
                        "description":"Sales Order Table"
                        },
                        "guid":"ead5abc7-00a4-4d81-8432-d5f6f6f60000",
                        "status":"ACTIVE",
                        "displayText":"SalesOrderTable"
                    },
                    "operationType":"ENTITY_UPDATE",
                    "eventTime":1618588940567
                }
}
```

> [!IMPORTANT]
> Atlas stöder för närvarande följande **åtgärdstyper: ENTITY_CREATE_V2**, **ENTITY_PARTIAL_UPDATE_V2**, **ENTITY_FULL_UPDATE_V2**, **ENTITY_DELETE_V2**. Push-meddelanden till Purview är för närvarande aktiverat som standard. Om scenariot omfattar läsning från Purview kontaktar du oss eftersom det måste vara tillåtet. (ange prenumerations-ID och namn på Purview-konto).


## <a name="next-steps"></a>Nästa steg
Kolla in exemplen på GitHub. 

- [Event Hubs på GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs/samples)
- [Exempel på händelseprocessorer på GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples)
- [Atlas-introduktion till meddelanden](https://atlas.apache.org/2.0.0/Notifications.html)