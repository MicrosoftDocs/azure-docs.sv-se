---
title: Tillgänglighet och konsekvens – Azure Event Hubs | Microsoft Docs
description: Hur du ger maximal tillgänglighet och konsekvens med Azure Event Hubs att använda partitioner.
ms.topic: article
ms.date: 03/15/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 62249357f8c6aa8521924dceef26a6f2c1e9e296
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2021
ms.locfileid: "103600853"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Tillgänglighet och konsekvens i Event Hubs
Den här artikeln innehåller information om tillgänglighet och konsekvens som stöds av Azure Event Hubs. 

## <a name="availability"></a>Tillgänglighet
Azure Event Hubs sprider risken för oåterkalleliga fel på enskilda datorer eller till och med fullständiga rack i kluster som sträcker sig över flera fel domäner i ett Data Center. Den implementerar transparent fel identifiering och redundansväxling, så att tjänsten fortsätter att fungera inom säkra tjänste nivåer och normalt utan märkbart avbrott när sådana fel inträffar. 

Om ett Event Hubs-namnområde har skapats med aktiverade [tillgänglighets zoner](../availability-zones/az-overview.md) , sprids risken för störningar ytterligare över tre fysiskt avgränsade anläggningar, och tjänsten har tillräckligt med kapacitets reserver för att snabbt kunna hantera den fullständiga, oåterkalleliga förlusten av hela anläggningen. Mer information finns i [Azure Event Hubs-geo-Disaster Recovery](event-hubs-geo-dr.md).

När ett klient program skickar händelser till en händelsehubben utan att ange en partition, distribueras händelser automatiskt mellan partitioner i händelsehubben. Om en partition inte är tillgänglig av någon anledning distribueras händelser mellan återstående partitioner. Det här beteendet gör det möjligt för den största delen av tiden. För användnings fall som kräver den maximala tiden är den här modellen prioriterad i stället för att skicka händelser till en viss partition. 

## <a name="consistency"></a>Konsekvens
I vissa fall kan sortering av händelser vara viktigt. Till exempel kanske du vill att Server dels systemet ska bearbeta ett uppdaterings kommando före kommandot DELETE. I det här scenariot skickar ett klient program händelser till en specifik partition så att ordningen bevaras. När ett konsument program förbrukar dessa händelser från partitionen läses de i ordning. 

I den här konfigurationen ska du tänka på att om den partition som du skickar till inte är tillgänglig får du ett fel meddelande. Som jämförelse punkt, om du inte har en tillhörighet till en enda partition, skickar Event Hubs tjänsten din händelse till nästa tillgängliga partition.

Därför, om hög tillgänglighet är viktigast, ska du inte rikta en speciell partition (med partitions-ID/nyckel). Om du använder partitions-ID/nyckel nedgraderar du tillgängligheten för en Event Hub till partition-Level. I det här scenariot gör du ett explicit val mellan tillgänglighet (inget partitions-ID/nyckel) och konsekvens (fäster händelser till en specifik partition). Detaljerad information om partitioner i Event Hubs finns i [partitioner](event-hubs-features.md#partitions).

## <a name="appendix"></a>Bilaga

### <a name="send-events-without-specifying-a-partition"></a>Skicka händelser utan att ange en partition
Vi rekommenderar att du skickar händelser till en händelsehubben utan att ange partitionsinformation så att Event Hubss tjänsten kan utjämna belastningen mellan partitioner. Se följande snabb starter för att lära dig hur du gör det i olika programmeringsspråk. 

- [Skicka händelser med .NET](event-hubs-dotnet-standard-getstarted-send.md)
- [Skicka händelser med Java](event-hubs-java-get-started-send.md)
- [Skicka händelser med Java Script](event-hubs-python-get-started-send.md)
- [Skicka händelser med Python](event-hubs-python-get-started-send.md)


### <a name="send-events-to-a-specific-partition"></a>Skicka händelser till en speciell partition
I det här avsnittet får du lära dig hur du skickar händelser till en särskild partition med olika programmeringsspråk. 

### <a name="net"></a>[.NET](#tab/dotnet)
Om du vill skicka händelser till en särskild partition skapar du batchen med hjälp av metoden [EventHubProducerClient. CreateBatchAsync](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.createbatchasync#Azure_Messaging_EventHubs_Producer_EventHubProducerClient_CreateBatchAsync_Azure_Messaging_EventHubs_Producer_CreateBatchOptions_System_Threading_CancellationToken_) genom att ange antingen `PartitionId` eller `PartitionKey` i [CreateBatchOptions](//dotnet/api/azure.messaging.eventhubs.producer.createbatchoptions). Följande kod skickar en batch med händelser till en särskild partition genom att ange en partitionsnyckel. 

```csharp
var batchOptions = new CreateBatchOptions { PartitionKey = "cities" };
using var eventBatch = await producer.CreateBatchAsync(batchOptions);
```

Du kan också använda metoden [EventHubProducerClient. SendAsync](/dotnet/api/azure.messaging.eventhubs.producer.eventhubproducerclient.sendasync#Azure_Messaging_EventHubs_Producer_EventHubProducerClient_SendAsync_System_Collections_Generic_IEnumerable_Azure_Messaging_EventHubs_EventData__Azure_Messaging_EventHubs_Producer_SendEventOptions_System_Threading_CancellationToken_) genom att ange antingen **PartitionID** eller **PartitionKey** i [SendEventOptions](/dotnet/api/azure.messaging.eventhubs.producer.sendeventoptions).

```csharp
var sendEventOptions  = new SendEventOptions { PartitionKey = "cities" };
// create the events array
producer.SendAsync(events, sendOptions)
```


### <a name="java"></a>[Java](#tab/java)
Om du vill skicka händelser till en särskild partition skapar du batchen med hjälp av metoden [createBatch](/java/api/com.azure.messaging.eventhubs.eventhubproducerclient.createbatch) genom att ange antingen **partitions-ID** eller **partitionsnyckel** i [createBatchOptions](/java/api/com.azure.messaging.eventhubs.models.createbatchoptions). Följande kod skickar en batch med händelser till en särskild partition genom att ange en partitionsnyckel. 

```java
CreateBatchOptions batchOptions = new CreateBatchOptions();
batchOptions.setPartitionKey("cities");
```

Du kan också använda metoden [EventHubProducerClient. send](/java/api/com.azure.messaging.eventhubs.eventhubproducerclient.send#com_azure_messaging_eventhubs_EventHubProducerClient_send_java_lang_Iterable_com_azure_messaging_eventhubs_EventData__com_azure_messaging_eventhubs_models_SendOptions_) genom att ange antingen **partitions-ID** eller **partitionsnyckel** i [SendOptions](/java/api/com.azure.messaging.eventhubs.models.sendoptions).

```java
List<EventData> events = Arrays.asList(new EventData("Melbourne"), new EventData("London"), new EventData("New York"));
SendOptions sendOptions = new SendOptions();
sendOptions.setPartitionKey("cities");
producer.send(events, sendOptions);
```


### <a name="python"></a>[Python](#tab/python) 
Om du vill skicka händelser till en speciell partition, när du skapar en batch med hjälp av [`EventHubProducerClient.create_batch`](/python/api/azure-eventhub/azure.eventhub.eventhubproducerclient#create-batch---kwargs-) metoden, anger du `partition_id` eller `partition_key` . Använd sedan [`EventHubProducerClient.send_batch`](/python/api/azure-eventhub/azure.eventhub.aio.eventhubproducerclient#send-batch-event-data-batch--typing-union-azure-eventhub--common-eventdatabatch--typing-list-azure-eventhub-) metoden för att skicka batchen till Event Hub-partitionen. 

```python
event_data_batch = await producer.create_batch(partition_key='cities')
```

Du kan också använda metoden [EventHubProducerClient.send_batch](/python/api/azure-eventhub/azure.eventhub.eventhubproducerclient#send-batch-event-data-batch----kwargs-) genom att ange värden för `partition_id` `partition_key` parametrarna eller.

```python
producer.send_batch(event_data_batch, partition_key="cities")
```

### <a name="javascript"></a>[JavaScript](#tab/javascript)
Om du vill skicka händelser till en särskild partition [skapar du en batch](/javascript/api/@azure/event-hubs/eventhubproducerclient#createBatch_CreateBatchOptions_) med hjälp av objektet [EventHubProducerClient. CreateBatchOptions](/javascript/api/@azure/event-hubs/eventhubproducerclient#createBatch_CreateBatchOptions_) genom att ange `partitionId` eller `partitionKey` . Skicka sedan batchen till Event Hub med hjälp av metoden [EventHubProducerClient. SendBatch](/javascript/api/@azure/event-hubs/eventhubproducerclient#sendBatch_EventDataBatch__OperationOptions_) . 

Se följande exempel.

```javascript
const batchOptions = { partitionKey = "cities"; };
const batch = await producer.createBatch(batchOptions);
```

Du kan också använda metoden [EventHubProducerClient. sendBatch](/javascript/api/@azure/event-hubs/eventhubproducerclient#sendBatch_EventData____SendBatchOptions_) genom att ange antingen **partitions-ID** eller **partitionsnyckel** i [SendBatchOptions](/javascript/api/@azure/event-hubs/sendbatchoptions).

```javascript
const sendBatchOptions = { partitionKey = "cities"; };
// prepare events
producer.sendBatch(events, sendBatchOptions);
```

---



## <a name="next-steps"></a>Nästa steg
Du kan lära dig mer om Event Hubs genom att gå till följande länkar:

- [Översikt över Event Hubs tjänsten](./event-hubs-about.md)
- [Event Hubs-terminologi](event-hubs-features.md)
