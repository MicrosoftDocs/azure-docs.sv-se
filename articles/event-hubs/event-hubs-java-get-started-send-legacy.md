---
title: Skicka eller ta emot händelser från Azure Event Hubs med Java (bakåtkompatibelt)
description: Den här artikeln innehåller en genom gång av hur du skapar ett Java-program som skickar/tar emot händelser till/från Azure Event Hubs med hjälp av det gamla Azure-eventhubs-paketet.
ms.topic: quickstart
ms.date: 06/23/2020
ms.custom: devx-track-java
ms.openlocfilehash: e86ca76f3eb661e1407a02b58e60b62b391f5702
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97607991"
---
# <a name="use-java-to-send-events-to-or-receive-events-from-azure-event-hubs-azure-eventhubs"></a>Använda Java för att skicka händelser till eller ta emot händelser från Azure Event Hubs (Azure-eventhubs)

Den här snabb starten visar hur du skickar händelser till och tar emot händelser från en händelsehubben med hjälp av **Azure-eventhubs Java-** paketet.

> [!WARNING]
> Den här snabb starten använder de gamla **Azure-eventhubs** **-och Azure-eventhubs-EPH-** paketen. En snabb start som använder det senaste paketet  **Azure-Messaging-eventhubs** finns i [skicka och ta emot händelser med Azure-Messaging-eventhubs](event-hubs-java-get-started-send.md). Information om hur du flyttar ditt program från att använda det gamla paketet till ett nytt finns i [hand boken för att migrera från Azure-eventhubs till Azure-Messaging-eventhubs](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs/migration-guide.md). 

## <a name="prerequisites"></a>Krav

Om du är nybörjare på Azure Event Hubs, se [Event Hubs översikt](event-hubs-about.md) innan du gör den här snabb starten. 

För att slutföra den här snabbstarten, behöver du följande förhandskrav:

- **Microsoft Azure prenumeration**. Om du vill använda Azure-tjänster, inklusive Azure Event Hubs, behöver du en prenumeration.  Om du inte har ett befintligt Azure-konto kan du registrera dig för en [kostnads fri utvärderings version](https://azure.microsoft.com/free/) eller använda dina förmåner för MSDN-prenumeranter när du [skapar ett konto](https://azure.microsoft.com).
- En Java Development Environment. Den här snabb starten använder [Sol förmörkelse](https://www.eclipse.org/).
- **Skapa ett Event Hubs-namnområde och en Event Hub**. Det första steget är att använda [Azure Portal](https://portal.azure.com) för att skapa ett namn område av typen Event Hubs och hämta de autentiseringsuppgifter som programmet behöver för att kommunicera med händelsehubben. Om du behöver skapa ett namnområde och en händelsehubb följer du anvisningarna i [den här artikeln](event-hubs-create.md). Hämta sedan värdet för åtkomst nyckeln för händelsehubben genom att följa anvisningarna i artikeln: [Hämta anslutnings sträng](event-hubs-get-connection-string.md#get-connection-string-from-the-portal). Du använder åtkomst nyckeln i koden som du skriver senare i den här snabb starten. Standard nyckel namnet är: **RootManageSharedAccessKey**.

## <a name="send-events"></a>Skicka händelser 
Det här avsnittet visar hur du skapar ett Java-program för att skicka händelser till en Event Hub. 

> [!NOTE]
> Du kan ladda ned den här snabbstarten som ett exempel från [GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/SimpleSend). Ersätt strängarna `EventHubConnectionString` och `EventHubName` med värdena för din händelsehubb och kör den. Du kan också följa stegen i den här snabb starten för att skapa en egen.

### <a name="add-reference-to-azure-event-hubs-library"></a>Lägg till en referens till Azure Event Hubs-biblioteket

Java-klientens bibliotek för Event Hubs är tillgängligt för användning i Maven-projekt från [maven Central-lagringsplatsen](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22). Du kan referera till det här biblioteket med hjälp av följande beroende deklaration i Maven-projekt filen:

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
```

För olika typer av build-miljöer kan du uttryckligen hämta de senaste utgivna JAR-filerna från [maven Central-lagringsplatsen](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs%22).  

För en enkel händelse utgivare importerar du *com. Microsoft. Azure. eventhubs-* paketet för Event Hubs-klient klasserna och paketet *com. Microsoft. Azure. Service Bus* för verktygs klasser som vanliga undantag som delas med Azure Service Bus meddelande klienten. 

### <a name="write-code-to-send-messages-to-the-event-hub"></a>Skriva kod för att skicka meddelanden till händelsehubben

För följande exempel skapar du först ett nytt Maven-projekt för ett konsol-/gränssnittsprogram i din favorit Java Development Environment. Lägg till en klass med namnet `SimpleSend` och Lägg till följande kod i klassen:

```java
import com.google.gson.Gson;
import com.google.gson.GsonBuilder;
import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
import com.microsoft.azure.eventhubs.EventData;
import com.microsoft.azure.eventhubs.EventHubClient;
import com.microsoft.azure.eventhubs.EventHubException;

import java.io.IOException;
import java.nio.charset.Charset;
import java.time.Instant;
import java.util.concurrent.ExecutionException;
import java.util.concurrent.Executors;
import java.util.concurrent.ScheduledExecutorService;

public class SimpleSend {

    public static void main(String[] args)
            throws EventHubException, ExecutionException, InterruptedException, IOException {

    }
 }
```

### <a name="construct-connection-string"></a>Skapa anslutnings sträng

Använd klassen ConnectionStringBuilder för att skapa ett anslutnings sträng värde som skickas till den Event Hubs klient instansen. Ersätt plats hållarna med de värden som du fick när du skapade namn området och händelsehubben:

```java
        final ConnectionStringBuilder connStr = new ConnectionStringBuilder()
                .setNamespaceName("<EVENTHUB NAMESPACE") 
                .setEventHubName("EVENT HUB")
                .setSasKeyName("RootManageSharedAccessKey")
                .setSasKey("SHARED ACCESS KEY");
```

### <a name="write-code-to-send-events"></a>Skriva kod för att skicka händelser

Skapa en singularisk händelse genom att omvandla en sträng till sin byte i UTF-8-format. Skapa sedan en ny Event Hubs klient instans från anslutnings strängen och skicka meddelandet:   

```java 
        final Gson gson = new GsonBuilder().create();

        // The Executor handles all asynchronous tasks and this is passed to the EventHubClient instance.
        // This enables the user to segregate their thread pool based on the work load.
        // This pool can then be shared across multiple EventHubClient instances.
        // The following sample uses a single thread executor, as there is only one EventHubClient instance,
        // handling different flavors of ingestion to Event Hubs here.
        final ScheduledExecutorService executorService = Executors.newScheduledThreadPool(4);

        // Each EventHubClient instance spins up a new TCP/TLS connection, which is expensive.
        // It is always a best practice to reuse these instances. The following sample shows this.
        final EventHubClient ehClient = EventHubClient.createSync(connStr.toString(), executorService);

        try {
            for (int i = 0; i < 10; i++) {

                String payload = "Message " + Integer.toString(i);
                byte[] payloadBytes = gson.toJson(payload).getBytes(Charset.defaultCharset());
                EventData sendEvent = EventData.create(payloadBytes);

                // Send - not tied to any partition
                // Event Hubs service will round-robin the events across all Event Hubs partitions.
                // This is the recommended & most reliable way to send to Event Hubs.
                ehClient.sendSync(sendEvent);
            }

            System.out.println(Instant.now() + ": Send Complete...");
            System.out.println("Press Enter to stop.");
            System.in.read();
        } finally {
            ehClient.closeSync();
            executorService.shutdown();
        }

``` 

Skapa och kör programmet och se till att det inte finns några fel.

Grattis! Du har nu skickat meddelanden till en händelsehubb.

### <a name="appendix-how-messages-are-routed-to-eventhub-partitions"></a>Bilaga: hur meddelanden dirigeras till EventHub-partitioner

Innan meddelanden hämtas av konsumenter måste de publiceras till partitionerna först av utgivaren. När meddelanden publiceras till Event Hub synkront med metoden sendSync () i objektet com. Microsoft. Azure. eventhubs. EventHubClient, kan meddelandet skickas till en specifik partition eller distribueras till alla tillgängliga partitioner i ett resursallokerings sätt beroende på om partitionsnyckel har angetts eller inte.

När en sträng som representerar partitionsnyckel anges kommer nyckeln att hashas för att avgöra vilken partition som händelsen ska skickas till.

När partitionsnyckel inte har angetts, kommer meddelanden att rundas av till alla tillgängliga partitioner

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

// close the client at the end of your program
eventHubClient.closeSync();

```

## <a name="receive-events"></a>Ta emot händelser
Koden i den här självstudien baseras på [EventProcessorSample-koden på GitHub](https://github.com/Azure/azure-event-hubs/tree/master/samples/Java/Basic/EventProcessorSample), som du kan granska för att se hela det fungerande programmet.

### <a name="receive-messages-with-eventprocessorhost-in-java"></a>Ta emot meddelanden med EventProcessorHost i Java

**EventProcessorHost** är en Java-klass som fören klar mottagandet av händelser från Event Hubs genom att hantera permanenta kontroll punkter och parallella mottagningar från dessa Event Hubs. Med EventProcessorHost kan du dela upp händelser över flera olika mottagare, även när de finns på olika noder. Det här exemplet visas hur man använder EventProcessorHost för en enda mottagare.

### <a name="create-a-storage-account"></a>Skapa ett lagringskonto

Om du vill använda EventProcessorHost måste du ha ett [Azure Storage konto] [Azure Storage konto]:

1. Logga in på [Azure Portal](https://portal.azure.com)och välj **skapa en resurs** till vänster på skärmen.
2. Välj **lagring** och välj sedan **lagrings konto**. I fönstret **skapa lagrings konto** anger du ett namn för lagrings kontot. Fyll i resten av fälten, Välj önskad region och välj sedan **skapa**.

    ![Skapa ett lagrings konto i Azure Portal](./media/event-hubs-dotnet-framework-getstarted-receive-eph/create-azure-storage-account.png)

3. Välj det nyligen skapade lagrings kontot och välj sedan **åtkomst nycklar**:

    ![Hämta dina åtkomst nycklar i Azure Portal](./media/event-hubs-dotnet-framework-getstarted-receive-eph/select-azure-storage-access-keys.png)

    Kopiera värdet KEY1 till en tillfällig plats. Du använder det senare i den här självstudien.

### <a name="create-a-java-project-using-the-eventprocessor-host"></a>Skapa ett Java-projekt med EventProcessor-värden

Java-klientens bibliotek för Event Hubs är tillgängligt för användning i Maven-projekt från [maven Central-lagringsplatsen](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22)och kan refereras till med hjälp av följande beroende deklaration i din Maven-projekt fil: 

```xml
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs</artifactId>
    <version>2.2.0</version>
</dependency>
<dependency>
    <groupId>com.microsoft.azure</groupId>
    <artifactId>azure-eventhubs-eph</artifactId>
    <version>2.4.0</version>
</dependency>
```

För olika typer av build-miljöer kan du uttryckligen hämta de senaste utgivna JAR-filerna från [maven Central-lagringsplatsen](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-eventhubs-eph%22).

1. För följande exempel skapar du först ett nytt Maven-projekt för ett konsol-/gränssnittsprogram i din favorit Java Development Environment. Klassen anropas `ErrorNotificationHandler` .     

    ```java
    import java.util.function.Consumer;
    import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;

    public class ErrorNotificationHandler implements Consumer<ExceptionReceivedEventArgs>
    {
        @Override
        public void accept(ExceptionReceivedEventArgs t)
        {
            System.out.println("SAMPLE: Host " + t.getHostname() + " received general error notification during " + t.getAction() + ": " + t.getException().toString());
        }
    }
    ```
2. Använd följande kod för att skapa en ny klass som kallas `EventProcessorSample`. Ersätt plats hållarna med de värden som används när du skapade händelsehubben och lagrings kontot:

   ```java
   package com.microsoft.azure.eventhubs.samples.eventprocessorsample;

   import com.microsoft.azure.eventhubs.ConnectionStringBuilder;
   import com.microsoft.azure.eventhubs.EventData;
   import com.microsoft.azure.eventprocessorhost.CloseReason;
   import com.microsoft.azure.eventprocessorhost.EventProcessorHost;
   import com.microsoft.azure.eventprocessorhost.EventProcessorOptions;
   import com.microsoft.azure.eventprocessorhost.ExceptionReceivedEventArgs;
   import com.microsoft.azure.eventprocessorhost.IEventProcessor;
   import com.microsoft.azure.eventprocessorhost.PartitionContext;

   import java.util.concurrent.ExecutionException;
   import java.util.function.Consumer;

   public class EventProcessorSample
   {
       public static void main(String args[]) throws InterruptedException, ExecutionException
       {
           String consumerGroupName = "$Default";
           String namespaceName = "----NamespaceName----";
           String eventHubName = "----EventHubName----";
           String sasKeyName = "----SharedAccessSignatureKeyName----";
           String sasKey = "----SharedAccessSignatureKey----";
           String storageConnectionString = "----AzureStorageConnectionString----";
           String storageContainerName = "----StorageContainerName----";
           String hostNamePrefix = "----HostNamePrefix----";

           ConnectionStringBuilder eventHubConnectionString = new ConnectionStringBuilder()
                .setNamespaceName(namespaceName)
                .setEventHubName(eventHubName)
                .setSasKeyName(sasKeyName)
                .setSasKey(sasKey);

           EventProcessorHost host = new EventProcessorHost(
                EventProcessorHost.createHostName(hostNamePrefix),
                eventHubName,
                consumerGroupName,
                eventHubConnectionString.toString(),
                storageConnectionString,
                storageContainerName);

           System.out.println("Registering host named " + host.getHostName());
           EventProcessorOptions options = new EventProcessorOptions();
           options.setExceptionNotification(new ErrorNotificationHandler());

           host.registerEventProcessor(EventProcessor.class, options)
           .whenComplete((unused, e) ->
           {
               if (e != null)
               {
                   System.out.println("Failure while registering: " + e.toString());
                   if (e.getCause() != null)
                   {
                       System.out.println("Inner exception: " + e.getCause().toString());
                   }
               }
           })
           .thenAccept((unused) ->
           {
               System.out.println("Press enter to stop.");
                 try 
               {
                   System.in.read();
               }
               catch (Exception e)
               {
                   System.out.println("Keyboard read failed: " + e.toString());
               }
           })
           .thenCompose((unused) ->
            {
                return host.unregisterEventProcessor();
           })
           .exceptionally((e) ->
           {
               System.out.println("Failure while unregistering: " + e.toString());
               if (e.getCause() != null)
               {
                   System.out.println("Inner exception: " + e.getCause().toString());
               }
               return null;
           })
           .get(); // Wait for everything to finish before exiting main!

           System.out.println("End of sample");
       }
   }
   ```
3. Skapa ytterligare en klass `EventProcessor` med namnet med hjälp av följande kod:

    ```java
    public static class EventProcessor implements IEventProcessor
    {
        private int checkpointBatchingCount = 0;

        // OnOpen is called when a new event processor instance is created by the host. 
        @Override
        public void onOpen(PartitionContext context) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is opening");
        }

        // OnClose is called when an event processor instance is being shut down. 
        @Override
        public void onClose(PartitionContext context, CloseReason reason) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " is closing for reason " + reason.toString());
        }

        // onError is called when an error occurs in EventProcessorHost code that is tied to this partition, such as a receiver failure.
        @Override
        public void onError(PartitionContext context, Throwable error)
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " onError: " + error.toString());
        }

        // onEvents is called when events are received on this partition of the Event Hub. 
        @Override
        public void onEvents(PartitionContext context, Iterable<EventData> events) throws Exception
        {
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " got event batch");
            int eventCount = 0;
            for (EventData data : events)
            {
                try
                {
                    System.out.println("SAMPLE (" + context.getPartitionId() + "," + data.getSystemProperties().getOffset() + "," +
                            data.getSystemProperties().getSequenceNumber() + "): " + new String(data.getBytes(), "UTF8"));
                    eventCount++;

                    // Checkpointing persists the current position in the event stream for this partition and means that the next
                    // time any host opens an event processor on this event hub+consumer group+partition combination, it will start
                    // receiving at the event after this one. 
                    this.checkpointBatchingCount++;
                    if ((checkpointBatchingCount % 5) == 0)
                    {
                        System.out.println("SAMPLE: Partition " + context.getPartitionId() + " checkpointing at " +
                               data.getSystemProperties().getOffset() + "," + data.getSystemProperties().getSequenceNumber());
                        // Checkpoints are created asynchronously. It is important to wait for the result of checkpointing
                        // before exiting onEvents or before creating the next checkpoint, to detect errors and to ensure proper ordering.
                        context.checkpoint(data).get();
                    }
                }
                catch (Exception e)
                {
                    System.out.println("Processing failed for an event: " + e.toString());
                }
            }
            System.out.println("SAMPLE: Partition " + context.getPartitionId() + " batch size was " + eventCount + " for host " + context.getOwner());
        }
    }
    ```

Den här guiden använder en enda instans av EventProcessorHost. För att öka data flödet rekommenderar vi att du kör flera instanser av EventProcessorHost, helst på separata datorer.  Den ger även redundans. I de fallen koordineras de olika instanserna automatiskt sinsemellan för att kunna belastningsutjämna de mottagna händelserna. Om du vill att flera mottagare bearbetar *alla* händelser, måste du använda konceptet **ConsumerGroup**. När du tar emot händelser från olika datorer, kan det vara praktiskt att ange namn för EventProcessorHost-instanser baserat på de datorer (eller roller) som de har distribuerats i.

### <a name="publishing-messages-to-eventhub"></a>Publicera meddelanden till EventHub

Innan meddelanden hämtas av konsumenter måste de publiceras till partitionerna först av utgivaren. Det är värt att Observera att när meddelanden publiceras till Event Hub synkront med metoden sendSync () i objektet com. Microsoft. Azure. eventhubs. EventHubClient, kan meddelandet skickas till en specifik partition eller distribueras till alla tillgängliga partitioner i ett resursallokerings sätt beroende på om partitionsnyckel har angetts eller inte.

När en sträng som representerar partitionsnyckel anges, hashas nyckeln för att avgöra vilken partition som händelsen ska skickas till.

När partitionsnyckel inte har angetts, så är meddelanden rundade av till alla tillgängliga partitioner

```java
// Serialize the event into bytes
byte[] payloadBytes = gson.toJson(messagePayload).getBytes(Charset.defaultCharset());

// Use the bytes to construct an {@link EventData} object
EventData sendEvent = EventData.create(payloadBytes);

// Transmits the event to event hub without a partition key
// If a partition key is not set, then we will round-robin to all topic partitions
eventHubClient.sendSync(sendEvent);

//  the partitionKey will be hash'ed to determine the partitionId to send the eventData to.
eventHubClient.sendSync(sendEvent, partitionKey);

```

### <a name="implementing-a-custom-checkpointmanager-for-eventprocessorhost-eph"></a>Implementera en anpassad CheckpointManager för EventProcessorHost (EPH)

API: et tillhandahåller en mekanism för att implementera din anpassade kontroll punkts hanterare för scenarier där standard implementeringen inte är kompatibel med ditt användnings fall.

Standard kontroll punkts hanteraren använder Blob Storage, men om du åsidosätter kontroll punkts hanteraren som används av EPH med din egen implementering, kan du använda alla butiker som du vill återställa till implementeringen av Checkpoint Manager.

Skapa en klass som implementerar gränssnittet com. Microsoft. Azure. eventprocessorhost. ICheckpointManager

Använd din anpassade implementering av Checkpoint Manager (com. Microsoft. Azure. eventprocessorhost. ICheckpointManager)

I din implementering kan du åsidosätta standard kontroll punkts mekanismen och implementera egna kontroll punkter baserat på ditt eget data lager (t. ex. SQL Server, CosmosDB och Azure cache för Redis). Vi rekommenderar att butiken som används för att säkerhetskopiera kontroll punkts hanteraren är tillgänglig för alla EPH-instanser som bearbetar händelser för konsument gruppen.

Du kan använda alla data lager som är tillgängliga i din miljö.

Klassen com. Microsoft. Azure. eventprocessorhost. EventProcessorHost innehåller två konstruktorer som gör att du kan åsidosätta Checkpoint Manager för din EventProcessorHost.

## <a name="next-steps"></a>Nästa steg
Läs följande artiklar: 

- [EventProcessorHost](event-hubs-event-processor-host.md)
- [Funktioner och terminologi i Azure Event Hubs](event-hubs-features.md)
- [Vanliga frågor och svar om Event Hubs](event-hubs-faq.md)
