---
title: Skicka eller ta emot händelser från Azure Event Hubs med Java (senaste)
description: Den här artikeln innehåller en genom gång av hur du skapar ett Java-program som skickar/tar emot händelser till/från Azure Event Hubs med det senaste paketet Azure-Messaging-eventhubs.
ms.topic: quickstart
ms.date: 04/05/2021
ms.custom: devx-track-java
ms.openlocfilehash: bae0d4147fddf57398d494ca7f13c347f892e45e
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106448448"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-messaging-eventhubs"></a>Använda Java för att skicka händelser till eller ta emot händelser från Azure Event Hubs (Azure-Messaging-eventhubs)
Den här snabb starten visar hur du skickar händelser till och tar emot händelser från en händelsehubben med hjälp av Java **-paketet Azure-Messaging-eventhubs** .

> [!IMPORTANT]
> I den här snabb starten används det nya paketet **Azure-Messaging-eventhubs** . En snabb start som använder gamla **Azure-eventhubs** -och **Azure-eventhubs-EPH-** paket finns i [skicka och ta emot händelser med Azure-eventhubs och Azure-eventhubs-EPH](event-hubs-java-get-started-send-legacy.md). 


## <a name="prerequisites"></a>Förutsättningar
Om du inte har använt Azure Event Hubs tidigare, se [Event Hubs översikt](event-hubs-about.md) innan du gör den här snabb starten. 

För att slutföra den här snabbstarten, behöver du följande förhandskrav:

- **Microsoft Azure prenumeration**. Om du vill använda Azure-tjänster, inklusive Azure Event Hubs, behöver du en prenumeration.  Om du inte har ett befintligt Azure-konto kan du registrera dig för en [kostnads fri utvärderings version](https://azure.microsoft.com/free/) eller använda dina förmåner för MSDN-prenumeranter när du [skapar ett konto](https://azure.microsoft.com).
- En Java Development Environment. Den här snabb starten använder [Sol förmörkelse](https://www.eclipse.org/). Java Development Kit (JDK) med version 8 eller senare krävs. 
- **Skapa ett Event Hubs-namnområde och en Event Hub**. Det första steget är att använda [Azure Portal](https://portal.azure.com) för att skapa ett namn område av typen Event Hubs och hämta de autentiseringsuppgifter som programmet behöver för att kommunicera med händelsehubben. Om du behöver skapa ett namnområde och en händelsehubb följer du anvisningarna i [den här artikeln](event-hubs-create.md). Hämta sedan **anslutnings strängen för Event Hubs namn området genom att** följa anvisningarna i artikeln: [Hämta anslutnings sträng](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Du använder anslutnings strängen senare i den här snabb starten.

## <a name="send-events"></a>Skicka händelser 
Det här avsnittet visar hur du skapar ett Java-program för att skicka händelser till en Event Hub. 

### <a name="add-reference-to-azure-event-hubs-library"></a>Lägg till en referens till Azure Event Hubs-biblioteket

Java-klientens bibliotek för Event Hubs är tillgängligt i [maven Central-lagringsplatsen](https://search.maven.org/search?q=a:azure-messaging-eventhubs). Du kan referera till det här biblioteket med hjälp av följande beroende deklaration i Maven-projekt filen:

```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-messaging-eventhubs</artifactId>
    <version>5.6.0</version>
</dependency>
```

> [!NOTE]
> Uppdatera versionen till den senaste versionen som publicerats på maven-lagringsplatsen. 

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Skriva kod för att skicka meddelanden till händelsehubben

För följande exempel skapar du först ett nytt Maven-projekt för ett konsol-/gränssnittsprogram i din favorit Java Development Environment. Lägg till en klass med namnet `Sender` och Lägg till följande kod i klassen:

> [!IMPORTANT]
> Uppdatera `<Event Hubs namespace connection string>` med anslutnings strängen till Event Hubs namn området. Uppdatera `<Event hub name>` med namnet på händelsehubben i namn området. 

```java
import com.azure.messaging.eventhubs.*;
import java.util.Arrays;
import java.util.List;

public class Sender {
    private static final String connectionString = "<Event Hubs namespace connection string>";
    private static final String eventHubName = "<Event hub name>";

    public static void main(String[] args) {
        publishEvents();
    }
}
```
### <a name="add-code-to-publish-events-to-the-event-hub"></a>Lägg till kod för att publicera händelser till händelsehubben
Lägg till en metod `publishEvents` som heter i `Sender` klassen enligt vad som visas nedan. 

```java
    /**
     * Code sample for publishing events.
     * @throws IllegalArgumentException if the event data is bigger than max batch size.
     */
    public static void publishEvents() {
        // create a producer client
        EventHubProducerClient producer = new EventHubClientBuilder()
            .connectionString(connectionString, eventHubName)
            .buildProducerClient();

        // sample events in an array
        List<EventData> allEvents = Arrays.asList(new EventData("Foo"), new EventData("Bar"));
        
        // create a batch
        EventDataBatch eventDataBatch = producer.createBatch();

        for (EventData eventData : allEvents) {
            // try to add the event from the array to the batch
            if (!eventDataBatch.tryAdd(eventData)) {
                // if the batch is full, send it and then create a new batch
                producer.send(eventDataBatch);
                eventDataBatch = producer.createBatch();

                // Try to add that event that couldn't fit before.
                if (!eventDataBatch.tryAdd(eventData)) {
                    throw new IllegalArgumentException("Event is too large for an empty batch. Max size: "
                        + eventDataBatch.getMaxSizeInBytes());
                }
            }
        }
        // send the last batch of remaining events
        if (eventDataBatch.getCount() > 0) {
            producer.send(eventDataBatch);
        }
        producer.close();
    }
```

Bygg programmet och se till att det inte finns några fel. Du kör det här programmet när du har kört mottagar programmet. 

## <a name="receive-events"></a>Ta emot händelser
Koden i den här självstudien baseras på [EventProcessorClient-exemplet på GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorBlobCheckpointStoreSample.java), som du kan granska för att se hela det fungerande programmet.

> [!WARNING]
> Om du kör den här koden på Azure Stack hubb får du körnings fel om du inte riktar in dig på en viss lagrings-API-version. Det beror på att Event Hubs SDK använder det senaste tillgängliga Azure Storage API som är tillgängligt i Azure och som kanske inte är tillgängligt på din Azure Stack Hub-plattform. Azure Stack Hub kan stödja en annan version av Storage BLOB SDK än vad som normalt är tillgängligt på Azure. Om du använder Azure blogg Storage som kontroll punkts Arkiv, kontrollerar du [Azure Storage API-versionen som stöds för din Azure Stack Hub](/azure-stack/user/azure-stack-acs-differences?#api-version) -version och aktiverar den versionen i din kod. 
>
> Om du till exempel kör på Azure Stack Hub version 2005 är den högsta tillgängliga versionen för lagrings tjänsten version 2019-02-02. Som standard använder klient biblioteket för Event Hubs SDK den senaste tillgängliga versionen på Azure (2019-07-07 vid tidpunkten för lanseringen av SDK: n). I det här fallet, förutom följande steg i det här avsnittet, måste du också lägga till kod som mål för Storage Service API-versionen 2019-02-02. Ett exempel på hur du riktar in en speciell Storage API-version finns i [det här exemplet på GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java). 


### <a name="create-an-azure-storage-and-a-blob-container"></a>Skapa en Azure Storage och en BLOB-behållare
I den här snabb starten använder du Azure Storage (särskilt Blob Storage) som kontroll punkts arkivet. Kontroll punkter är en process där en händelse processor markerar eller genomför positionen för den senaste bearbetade händelsen i en partition. Att markera en kontroll punkt utförs vanligt vis i den funktion som bearbetar händelserna. Mer information om kontroll punkter finns i [händelse processor](event-processor-balance-partition-load.md).

Följ dessa steg om du vill skapa ett Azure Storage-konto. 

1. [Skapa ett Azure Storage-konto](../storage/common/storage-account-create.md?tabs=azure-portal)
2. [Skapa en blobcontainer](../storage/blobs/storage-quickstart-blobs-portal.md#create-a-container)
3. [Hämta anslutnings strängen till lagrings kontot](../storage/common/storage-configure-connection-string.md)

    Anteckna **anslutnings strängen** och **behållar namnet**. Du ska använda dem i Receive-koden. 

### <a name="add-event-hubs-libraries-to-your-java-project"></a>Lägga till Event Hubs-bibliotek i ditt Java-projekt
Lägg till följande beroenden i pom.xml-filen. 

- [Azure-Messaging – eventhubs](https://search.maven.org/search?q=a:azure-messaging-eventhubs)
- [Azure-Messaging-eventhubs-checkpointstore-BLOB](https://search.maven.org/search?q=a:azure-messaging-eventhubs-checkpointstore-blob)

```xml
<dependencies>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs</artifactId>
        <version>5.6.0</version>
    </dependency>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs-checkpointstore-blob</artifactId>
        <version>1.5.1</version>
    </dependency>
</dependencies>
```

1. Lägg till följande **import** -instruktioner överst i Java-filen. 

    ```java
    import com.azure.messaging.eventhubs.EventHubClientBuilder;
    import com.azure.messaging.eventhubs.EventProcessorClient;
    import com.azure.messaging.eventhubs.EventProcessorClientBuilder;
    import com.azure.messaging.eventhubs.checkpointstore.blob.BlobCheckpointStore;
    import com.azure.messaging.eventhubs.models.ErrorContext;
    import com.azure.messaging.eventhubs.models.EventContext;
    import com.azure.storage.blob.BlobContainerAsyncClient;
    import com.azure.storage.blob.BlobContainerClientBuilder;
    import java.util.function.Consumer;
    ```
2. Skapa en klass med namnet `Receiver` och Lägg till följande String-variabler i klassen. Ersätt plats hållarna med rätt värden. 

    > [!IMPORTANT]
    > Ersätt plats hållarna med rätt värden.
    > - `<Event Hubs namespace connection string>` med anslutnings strängen till Event Hubs namn området. Uppdatera 
    > - `<Event hub name>` med namnet på händelsehubben i namn området. 
    > - `<Storage connection string>` med anslutnings strängen till ditt Azure Storage-konto. 
    > - `<Storage container name>` med namnet på din behållare i Azure Blob Storage. 

    ```java
    private static final String connectionString = "<Event Hubs namespace connection string>";
    private static final String eventHubName = "<Event hub name>";
    private static final String storageConnectionString = "<Storage connection string>";
    private static final String storageContainerName = "<Storage container name>";
    ```
1. Lägg till följande `main` metod i-klassen. 

    ```java
    public static void main(String[] args) throws Exception {
        // Create a blob container client that you use later to build an event processor client to receive and process events
        BlobContainerAsyncClient blobContainerAsyncClient = new BlobContainerClientBuilder()
            .connectionString(storageConnectionString) 
            .containerName(storageContainerName) 
            .buildAsyncClient();
    
        // Create a builder object that you will use later to build an event processor client to receive and process events and errors.
        EventProcessorClientBuilder eventProcessorClientBuilder = new EventProcessorClientBuilder()
            .connectionString(connectionString, eventHubName) 
            .consumerGroup(EventHubClientBuilder.DEFAULT_CONSUMER_GROUP_NAME)
            .processEvent(PARTITION_PROCESSOR) 
            .processError(ERROR_HANDLER) 
            .checkpointStore(new BlobCheckpointStore(blobContainerAsyncClient)); 

        // Use the builder object to create an event processor client 
        EventProcessorClient eventProcessorClient = eventProcessorClientBuilder.buildEventProcessorClient();

        System.out.println("Starting event processor");
        eventProcessorClient.start();

        System.out.println("Press enter to stop.");
        System.in.read();

        System.out.println("Stopping event processor");
        eventProcessorClient.stop();
        System.out.println("Event processor stopped.");

        System.out.println("Exiting process");
    }    
    ```
4. Lägg till de två hjälp metoderna ( `PARTITION_PROCESSOR` och `ERROR_HANDLER` ) som bearbetar händelser och fel i `Receiver` klassen. 

    ```java
    public static final Consumer<EventContext> PARTITION_PROCESSOR = eventContext -> {
        System.out.printf("Processing event from partition %s with sequence number %d with body: %s %n", 
                eventContext.getPartitionContext().getPartitionId(), eventContext.getEventData().getSequenceNumber(), eventContext.getEventData().getBodyAsString());

        if (eventContext.getEventData().getSequenceNumber() % 10 == 0) {
            eventContext.updateCheckpoint();
        }
    };
    
    public static final Consumer<ErrorContext> ERROR_HANDLER = errorContext -> {
        System.out.printf("Error occurred in partition processor for partition %s, %s.%n",
            errorContext.getPartitionContext().getPartitionId(),
            errorContext.getThrowable());
    };    
    ```
3. Bygg programmet och se till att det inte finns några fel. 

## <a name="run-the-applications"></a>Köra programmen
1. Kör **mottagar** programmet först.
1. Kör sedan **avsändar** programmet. 
1. I fönstret för **mottagar** programmet bekräftar du att du ser de händelser som publicerades av avsändar programmet.

    ```cmd
    Starting event processor
    Press enter to stop.
    Processing event from partition 0 with sequence number 331 with body: Foo 
    Processing event from partition 0 with sequence number 332 with body: Bar 
    ```
1. Tryck på **RETUR** i fönstret för mottagar program för att stoppa programmet. 

    ```cmd
    Starting event processor
    Press enter to stop.
    Processing event from partition 0 with sequence number 331 with body: Foo 
    Processing event from partition 0 with sequence number 332 with body: Bar 
    
    Stopping event processor
    Event processor stopped.
    Exiting process
    ```

## <a name="next-steps"></a>Nästa steg
Se följande exempel på GitHub:

- [Azure-Messaging – eventhubs exempel](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [Azure-Messaging-eventhubs-checkpointstore-BLOB-exempel](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob).  
