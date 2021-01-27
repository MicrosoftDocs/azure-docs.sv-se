---
title: Tillgänglighet och konsekvens – Azure Event Hubs | Microsoft Docs
description: Hur du ger maximal tillgänglighet och konsekvens med Azure Event Hubs att använda partitioner.
ms.topic: article
ms.date: 01/25/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 2fdb62e953230a38a26d22e136789fea52c8ee8c
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98882203"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Tillgänglighet och konsekvens i Event Hubs
Den här artikeln innehåller information om tillgänglighet och konsekvens som stöds av Azure Event Hubs. 

## <a name="availability"></a>Tillgänglighet
Azure Event Hubs sprider risken för oåterkalleliga fel på enskilda datorer eller till och med fullständiga rack i kluster som sträcker sig över flera fel domäner i ett Data Center. Den implementerar transparent fel identifiering och redundansväxling, så att tjänsten fortsätter att fungera inom säkra tjänste nivåer och normalt utan märkbart avbrott när sådana fel inträffar. Om ett Event Hubs-namnområde har skapats med det aktiverade alternativet för [tillgänglighets zoner](../availability-zones/az-overview.md), sprids risk risken ytterligare över tre fysiskt åtskilda anläggningar, och tjänsten har tillräckligt med kapacitets reserver för att snabbt kunna hantera hela den kompletta skadan. Mer information finns i [Azure Event Hubs-geo-Disaster Recovery](event-hubs-geo-dr.md).

När ett klient program skickar händelser till en händelsehubben, distribueras händelser automatiskt mellan partitioner i händelsehubben. Om en partition inte är tillgänglig av någon anledning distribueras händelser mellan återstående partitioner. Det här beteendet gör det möjligt för den största delen av tiden. För användnings fall som kräver den maximala tiden är den här modellen prioriterad i stället för att skicka händelser till en viss partition. Mer information finns i [partitioner](event-hubs-scalability.md#partitions).

## <a name="consistency"></a>Konsekvens
I vissa fall kan sortering av händelser vara viktigt. Till exempel kanske du vill att Server dels systemet ska bearbeta ett uppdaterings kommando före kommandot DELETE. I det här scenariot skickar ett klient program händelser till en specifik partition så att ordningen bevaras. När ett konsument program förbrukar dessa händelser från partitionen läses de i ordning. 

I den här konfigurationen ska du tänka på att om den partition som du skickar till inte är tillgänglig får du ett fel meddelande. Som jämförelse punkt, om du inte har en tillhörighet till en enda partition, skickar Event Hubs tjänsten din händelse till nästa tillgängliga partition.

En möjlig lösning för att säkerställa sortering, samtidigt som den maximerar tiden, är att aggregera händelser som en del av ditt händelse bearbetnings program. Det enklaste sättet att göra det är att stämpla din händelse med en anpassad sekvens nummer egenskap.

I det här scenariot skickar producent klienten händelser till en av de tillgängliga partitionerna i händelsehubben och anger motsvarande sekvensnummer från ditt program. Den här lösningen kräver tillstånd att behållas av ditt bearbetnings program, men ger dina avsändare en slut punkt som är mer sannolik tillgänglig.

## <a name="appendix"></a>Bilaga

### <a name="net-examples"></a>.NET-exempel

#### <a name="send-events-to-a-specific-partition"></a>Skicka händelser till en speciell partition
Ange antingen partitionsnyckel för en händelse eller Använd ett `PartitionSender` objekt (om du använder det gamla Microsoft. Azure. Messaging-biblioteket) för att endast skicka händelser till en viss partition. Detta säkerställer att när dessa händelser läses från partitionen, läses de in i ordning. 

Om du använder ett nyare **Azure. Messaging. EventHubs** -bibliotek, se [migrera kod från PartitionSender till EventHubProducerClient för publicering av händelser till en partition](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/eventhub/Azure.Messaging.EventHubs/MigrationGuide.md#migrating-code-from-partitionsender-to-eventhubproducerclient-for-publishing-events-to-a-partition).

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure. Messaging. EventHubs (5.0.0 eller senare)](#tab/latest)

```csharp
var connectionString = "<< CONNECTION STRING FOR THE EVENT HUBS NAMESPACE >>";
var eventHubName = "<< NAME OF THE EVENT HUB >>";

await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
{
    var batchOptions = new CreateBatchOptions() { PartitionId = "my-partition-id" };
    using EventDataBatch eventBatch = await producerClient.CreateBatchAsync(batchOptions);
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First")));
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second")));
    
    await producerClient.SendAsync(eventBatch);
}
```

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Microsoft. Azure. EventHubs (4.1.0 eller tidigare)](#tab/old)

```csharp
var connectionString = "<< CONNECTION STRING FOR THE EVENT HUBS NAMESPACE >>";
var eventHubName = "<< NAME OF THE EVENT HUB >>";

var connectionStringBuilder = new EventHubsConnectionStringBuilder(connectionString){ EntityPath = eventHubName }; 
var eventHubClient = EventHubClient.CreateFromConnectionString(connectionStringBuilder.ToString());
PartitionSender partitionSender = eventHubClient.CreatePartitionSender("my-partition-id");
try
{
    EventDataBatch eventBatch = partitionSender.CreateBatch();
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("First")));
    eventBatch.TryAdd(new EventData(Encoding.UTF8.GetBytes("Second")));

    await partitionSender.SendAsync(eventBatch);
}
finally
{
    await partitionSender.CloseAsync();
    await eventHubClient.CloseAsync();
}
```

---

### <a name="set-a-sequence-number"></a>Ange ett sekvensnummer
I följande exempel stämplas händelsen med en anpassad sekvens nummer egenskap. 

#### <a name="azuremessagingeventhubs-500-or-later"></a>[Azure. Messaging. EventHubs (5.0.0 eller senare)](#tab/latest)

```csharp
// create a producer client that you can use to send events to an event hub
await using (var producerClient = new EventHubProducerClient(connectionString, eventHubName))
{
    // get the latest sequence number from your application
    var sequenceNumber = GetNextSequenceNumber();

    // create a batch of events 
    using EventDataBatch eventBatch = await producerClient.CreateBatchAsync();

    // create a new EventData object by encoding a string as a byte array
    var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));

    // set a custom sequence number property
    data.Properties.Add("SequenceNumber", sequenceNumber);

    // add events to the batch. An event is a represented by a collection of bytes and metadata. 
    eventBatch.TryAdd(data);

    // use the producer client to send the batch of events to the event hub
    await producerClient.SendAsync(eventBatch);
}
```

#### <a name="microsoftazureeventhubs-410-or-earlier"></a>[Microsoft. Azure. EventHubs (4.1.0 eller tidigare)](#tab/old)
```csharp
// Create an Event Hubs client
var client = new EventHubClient(connectionString, eventHubName);

//Create a producer to produce events
EventHubProducer producer = client.CreateProducer();

// Get the latest sequence number from your application 
var sequenceNumber = GetNextSequenceNumber();

// Create a new EventData object by encoding a string as a byte array
var data = new EventData(Encoding.UTF8.GetBytes("This is my message..."));

// Set a custom sequence number property
data.Properties.Add("SequenceNumber", sequenceNumber);

// Send single message async
await producer.SendAsync(data);
```
---

Det här exemplet skickar din händelse till en av de tillgängliga partitionerna i händelsehubben och anger motsvarande ordnings nummer från ditt program. Den här lösningen kräver tillstånd att behållas av ditt bearbetnings program, men ger dina avsändare en slut punkt som är mer sannolik tillgänglig.

## <a name="next-steps"></a>Nästa steg
Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

* [Översikt över Event Hubs tjänsten](./event-hubs-about.md)
* [Skapa en händelsehubben](event-hubs-create.md)
