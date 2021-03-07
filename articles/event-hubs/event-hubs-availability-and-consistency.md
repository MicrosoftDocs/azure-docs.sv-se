---
title: Tillgänglighet och konsekvens – Azure Event Hubs | Microsoft Docs
description: Hur du ger maximal tillgänglighet och konsekvens med Azure Event Hubs att använda partitioner.
ms.topic: article
ms.date: 01/25/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 325cc80daba2a44dedbd5e09ac4858ae2815c1cd
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102425931"
---
# <a name="availability-and-consistency-in-event-hubs"></a>Tillgänglighet och konsekvens i Event Hubs
Den här artikeln innehåller information om tillgänglighet och konsekvens som stöds av Azure Event Hubs. 

## <a name="availability"></a>Tillgänglighet
Azure Event Hubs sprider risken för oåterkalleliga fel på enskilda datorer eller till och med fullständiga rack i kluster som sträcker sig över flera fel domäner i ett Data Center. Den implementerar transparent fel identifiering och redundansväxling, så att tjänsten fortsätter att fungera inom säkra tjänste nivåer och normalt utan märkbart avbrott när sådana fel inträffar. 

Om ett Event Hubs-namnområde har skapats med aktiverade [tillgänglighets zoner](../availability-zones/az-overview.md) , sprids risken för störningar ytterligare över tre fysiskt avgränsade anläggningar, och tjänsten har tillräckligt med kapacitets reserver för att snabbt kunna hantera den fullständiga, oåterkalleliga förlusten av hela anläggningen. Mer information finns i [Azure Event Hubs-geo-Disaster Recovery](event-hubs-geo-dr.md).

När ett klient program skickar händelser till en händelsehubben utan att ange en partition, distribueras händelser automatiskt mellan partitioner i händelsehubben. Om en partition inte är tillgänglig av någon anledning distribueras händelser mellan återstående partitioner. Det här beteendet gör det möjligt för den största delen av tiden. För användnings fall som kräver den maximala tiden är den här modellen prioriterad i stället för att skicka händelser till en viss partition. 

### <a name="availability-considerations-when-using-a-partition-id-or-key"></a>Tillgänglighets överväganden vid användning av partitions-ID eller nyckel
Det är valfritt att använda ett partitions-ID eller en partitionsnyckel. Överväg noggrant om du vill använda en sådan. Om du inte anger ett partitions-ID/nyckel när du publicerar en händelse, balanserar Event Hubs belastningen mellan partitioner. När du använder ett partitions-ID/nyckel, kräver dessa partitioner tillgänglighet på en enda nod och avbrott kan ske över tid. Compute-noder kan till exempel behöva startas om eller korrigeras. Så om du anger ett partitions-ID/nyckel och den partitionen blir otillgänglig av någon anledning, kommer ett försök att komma åt data i den partitionen att Miss lyckas. Om hög tillgänglighet är viktigast, ska du inte ange ett partitions-ID/nyckel. I så fall skickas händelser till partitioner med hjälp av en intern algoritm för belastnings utjämning. I det här scenariot gör du ett explicit val mellan tillgänglighet (inget partitions-ID/nyckel) och konsekvens (fäster händelser till en specifik partition). Om du använder partitions-ID/nyckel nedgraderar du tillgängligheten för en Event Hub till partition-Level. 

### <a name="availability-considerations-when-handling-delays-in-processing-events"></a>Tillgänglighets överväganden vid hantering av fördröjningar vid bearbetning av händelser
Ett annat övervägande är om ett konsument program som hanterar fördröjningar vid bearbetning av händelser. I vissa fall kan det vara bättre för konsument programmet att släppa data och försöka igen i stället för att försöka fortsätta med bearbetningen, vilket kan leda till efterföljande bearbetnings fördröjningar. Till exempel är det bättre att vänta på att slutföra aktuella data, men i ett Live-chatt-eller VOIP-scenario kan du snabbt få data, även om de inte är slutförda.

Med hänsyn till dessa tillgänglighets överväganden kan konsument programmet välja någon av följande strategier för fel hantering:

- Stoppa (sluta att läsa från händelsehubben tills problem har åtgärd ATS)
- Släpp (meddelanden är inte viktiga, släpp dem)
- Försök igen (försök igen när du ser anpassningen)


## <a name="consistency"></a>Konsekvens
I vissa fall kan sortering av händelser vara viktigt. Till exempel kanske du vill att Server dels systemet ska bearbeta ett uppdaterings kommando före kommandot DELETE. I det här scenariot skickar ett klient program händelser till en specifik partition så att ordningen bevaras. När ett konsument program förbrukar dessa händelser från partitionen läses de i ordning. 

I den här konfigurationen ska du tänka på att om den partition som du skickar till inte är tillgänglig får du ett fel meddelande. Som jämförelse punkt, om du inte har en tillhörighet till en enda partition, skickar Event Hubs tjänsten din händelse till nästa tillgängliga partition.


## <a name="appendix"></a>Bilaga

### <a name="send-events-to-a-specific-partition"></a>Skicka händelser till en speciell partition
Det här avsnittet visar hur du skickar händelser till en speciell partition med C#, Java, python och Java Script. 

### <a name="net"></a>[.NET](#tab/dotnet)
Den fullständiga exempel kod som visar hur du skickar en händelse grupp till en Event Hub (utan att ange partitions-ID/nyckel) finns i [skicka händelser till och ta emot händelser från Azure Event Hubs-.net (Azure. Messaging. EventHubs)](event-hubs-dotnet-standard-getstarted-send.md).

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
För den fullständiga exempel kod som visar hur du skickar en händelse grupp till en Event Hub (utan att ange partitions-ID/nyckel), se [Använd Java för att skicka händelser till eller ta emot händelser från azure Event Hubs (Azure-Messaging-eventhubs)](event-hubs-java-get-started-send.md).

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
För den fullständiga exempel kod som visar hur du skickar en händelse grupp till en Event Hub (utan att ange partitions-ID/nyckel), se [skicka händelser till eller ta emot händelser från händelse hubbar med hjälp av python (Azure-eventhub)](event-hubs-python-get-started-send.md).

Om du vill skicka händelser till en speciell partition, när du skapar en batch med hjälp av [`EventHubProducerClient.create_batch`](/python/api/azure-eventhub/azure.eventhub.eventhubproducerclient#create-batch---kwargs-) metoden, anger du `partition_id` eller `partition_key` . Använd sedan [`EventHubProducerClient.send_batch`](/python/api/azure-eventhub/azure.eventhub.aio.eventhubproducerclient#send-batch-event-data-batch--typing-union-azure-eventhub--common-eventdatabatch--typing-list-azure-eventhub-) metoden för att skicka batchen till Event Hub-partitionen. 

```python
event_data_batch = await producer.create_batch(partition_key='cities')
```

Du kan också använda metoden [EventHubProducerClient.send_batch](/python/api/azure-eventhub/azure.eventhub.eventhubproducerclient#send-batch-event-data-batch----kwargs-) genom att ange värden för `partition_id` `partition_key` parametrarna eller.

```python
producer.send_batch(event_data_batch, partition_key="cities")
```


### <a name="javascript"></a>[JavaScript](#tab/javascript)
Den fullständiga exempel kod som visar hur du skickar en händelse grupp till en Event Hub (utan att ange partitions-ID/nyckel) finns i [skicka händelser till eller ta emot händelser från händelse hubbar med hjälp av Java Script (Azure/Event-Hubbs)](event-hubs-node-get-started-send.md).

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

* [Översikt över Event Hubs tjänsten](./event-hubs-about.md)
* [Skapa en händelsehubben](event-hubs-create.md)
