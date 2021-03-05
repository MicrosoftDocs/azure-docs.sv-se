---
title: Skicka eller ta emot händelser från Azure Event Hubs med Java (senaste)
description: Den här artikeln innehåller en genom gång av hur du skapar ett Java-program som skickar/tar emot händelser till/från Azure Event Hubs med det senaste paketet Azure-Messaging-eventhubs.
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: 640f6c4dcb223e55e10f7cb5d7daaa44dbd41578
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102172031"
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
    <version>5.0.1</version>
</dependency>
```

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Skriva kod för att skicka meddelanden till händelsehubben

För följande exempel skapar du först ett nytt Maven-projekt för ett konsol-/gränssnittsprogram i din favorit Java Development Environment. Lägg till en klass med namnet `Sender` och Lägg till följande kod i klassen:

```java
import com.azure.messaging.eventhubs.*;
import static java.nio.charset.StandardCharsets.UTF_8;

public class Sender {
       public static void main(String[] args) {
    }
}
```

### <a name="connection-string-and-event-hub"></a>Anslutnings sträng och händelsehubben
Den här koden använder anslutnings strängen för Event Hubs namn området och namnet på händelsehubben för att bygga en Event Hubs-klient. 

```java
String connectionString = "<CONNECTION STRING to EVENT HUBS NAMESPACE>";
String eventHubName = "<EVENT HUB NAME>";
```

### <a name="create-an-event-hubs-producer-client"></a>Skapa en Event Hubs Producer-klient 
Den här koden skapar ett producent klient objekt som används för att skapa/skicka händelser till händelsehubben. 

```java
EventHubProducerClient producer = new EventHubClientBuilder()
    .connectionString(connectionString, eventHubName)
    .buildProducerClient();
```

### <a name="prepare-a-batch-of-events"></a>Förbereda en grupp händelser
Den här koden förbereder en batch med händelser. 

```java
EventDataBatch batch = producer.createBatch();
batch.tryAdd(new EventData("First event"));
batch.tryAdd(new EventData("Second event"));
batch.tryAdd(new EventData("Third event"));
batch.tryAdd(new EventData("Fourth event"));
batch.tryAdd(new EventData("Fifth event"));
```

### <a name="send-the-batch-of-events-to-the-event-hub"></a>Skicka en händelse grupp till händelsehubben
Den här koden skickar den grupp med händelser som du för beredde i föregående steg till Event Hub. Följande kodblock i sändnings åtgärden. 

```java
producer.send(batch);
```

### <a name="close-and-cleanup"></a>Stäng och rensa
Den här koden stänger producenten. 

```java
producer.close();
```
### <a name="complete-code-to-send-events"></a>Slutför kod för att skicka händelser
Här är den fullständiga koden för att skicka händelser till händelsehubben. 

```java
import com.azure.messaging.eventhubs.*;

public class Sender {
    public static void main(String[] args) {
        final String connectionString = "EVENT HUBS NAMESPACE CONNECTION STRING";
        final String eventHubName = "EVENT HUB NAME";

        // create a producer using the namespace connection string and event hub name
        EventHubProducerClient producer = new EventHubClientBuilder()
            .connectionString(connectionString, eventHubName)
            .buildProducerClient();

        // prepare a batch of events to send to the event hub    
        EventDataBatch batch = producer.createBatch();
        batch.tryAdd(new EventData("First event"));
        batch.tryAdd(new EventData("Second event"));
        batch.tryAdd(new EventData("Third event"));
        batch.tryAdd(new EventData("Fourth event"));
        batch.tryAdd(new EventData("Fifth event"));

        // send the batch of events to the event hub
        producer.send(batch);

        // close the producer
        producer.close();
    }
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
        <version>5.1.1</version>
    </dependency>
    <dependency>
        <groupId>com.azure</groupId>
        <artifactId>azure-messaging-eventhubs-checkpointstore-blob</artifactId>
        <version>1.5.0</version>
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
    import java.util.concurrent.TimeUnit;
    ```
2. Skapa en klass med namnet `Receiver` och Lägg till följande String-variabler i klassen. Ersätt plats hållarna med rätt värden. 
    ```java
    private static final String EH_NAMESPACE_CONNECTION_STRING = "<EVENT HUBS NAMESPACE CONNECTION STRING>";
    private static final String eventHubName = "<EVENT HUB NAME>";
    private static final String STORAGE_CONNECTION_STRING = "<AZURE STORAGE CONNECTION STRING>";
    private static final String STORAGE_CONTAINER_NAME = "<AZURE STORAGE CONTAINER NAME>";
    ```
3. Lägg till följande `main` metod i-klassen. 

    ```java
    public static void main(String[] args) throws Exception {
        // Create a blob container client that you use later to build an event processor client to receive and process events
        BlobContainerAsyncClient blobContainerAsyncClient = new BlobContainerClientBuilder()
            .connectionString(STORAGE_CONNECTION_STRING) 
            .containerName(STORAGE_CONTAINER_NAME) 
            .buildAsyncClient();
    
        // Create a builder object that you will use later to build an event processor client to receive and process events and errors.
        EventProcessorClientBuilder eventProcessorClientBuilder = new EventProcessorClientBuilder()
            .connectionString(EH_NAMESPACE_CONNECTION_STRING, eventHubName) 
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
3. Den fullständiga koden bör se ut så här: 

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
    import java.util.concurrent.TimeUnit;
    
    public class Receiver {
        
        private static final String EH_NAMESPACE_CONNECTION_STRING = "<EVENT HUBS NAMESPACE CONNECTION STRING>";
        private static final String eventHubName = "<EVENT HUB NAME>";
        private static final String STORAGE_CONNECTION_STRING = "<AZURE STORAGE CONNECTION STRING>";
        private static final String STORAGE_CONTAINER_NAME = "<AZURE STORAGE CONTAINER NAME>";
    
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
        
        public static void main(String[] args) throws Exception {
            BlobContainerAsyncClient blobContainerAsyncClient = new BlobContainerClientBuilder()
                .connectionString(STORAGE_CONNECTION_STRING)
                .containerName(STORAGE_CONTAINER_NAME)
                .buildAsyncClient();
    
            EventProcessorClientBuilder eventProcessorClientBuilder = new EventProcessorClientBuilder()
                .connectionString(EH_NAMESPACE_CONNECTION_STRING, eventHubName)
                .consumerGroup(EventHubClientBuilder.DEFAULT_CONSUMER_GROUP_NAME)
                .processEvent(PARTITION_PROCESSOR)
                .processError(ERROR_HANDLER)
                .checkpointStore(new BlobCheckpointStore(blobContainerAsyncClient));
    
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
        
    }
    ```
3. Bygg programmet och se till att det inte finns några fel. 

## <a name="run-the-applications"></a>Köra programmen
1. Kör **mottagar** programmet först.
1. Kör sedan **avsändar** programmet. 
1. I fönstret för **mottagar** programmet bekräftar du att du ser de händelser som publicerades av avsändar programmet.
1. Tryck på **RETUR** i fönstret för mottagar program för att stoppa programmet. 

## <a name="next-steps"></a>Nästa steg
Se följande exempel på GitHub:

- [Azure-Messaging – eventhubs exempel](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs/src/samples/java/com/azure/messaging/eventhubs)
- [Azure-Messaging-eventhubs-checkpointstore-BLOB-exempel](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob).  
