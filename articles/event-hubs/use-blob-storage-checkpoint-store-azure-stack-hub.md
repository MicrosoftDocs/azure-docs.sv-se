---
title: Använda Blob Storage som kontrollpunktslager på Azure Stack Hub
description: Den här artikeln beskriver hur du använder Blob Storage som en kontroll punkts lagring i Event Hubs på Azure Stack hubben.
ms.topic: how-to
ms.date: 12/09/2020
ms.openlocfilehash: 07d7cf844480a9a88468c17cecc7ca38cca5d176
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97007831"
---
# <a name="use-blob-storage-as-checkpoint-store---event-hubs-on-azure-stack-hub"></a>Använd Blob Storage som kontroll punkts lager-Event Hubs på Azure Stack hubben
Om du använder Azure Blob Storage som kontroll punkts Arkiv i en miljö som har stöd för en annan version av Storage BLOB SDK än de som vanligt vis är tillgängliga på Azure, måste du använda kod för att ändra Storage Service API-versionen till den version som stöds av den aktuella miljön. Om du till exempel kör [Event Hubs på en Azure Stack hubb version 2002](/azure-stack/user/event-hubs-overview)är den högsta tillgängliga versionen för lagrings tjänsten version 2017-11-09. I så fall måste du använda kod för att rikta Storage Service API-versionen till 2017-11-09. Ett exempel på hur du riktar in en speciell Storage API-version finns i följande exempel på GitHub: 

- [.NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/eventhub/Azure.Messaging.EventHubs.Processor/samples/)
- [Java](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/eventhubs/azure-messaging-eventhubs-checkpointstore-blob/src/samples/java/com/azure/messaging/eventhubs/checkpointstore/blob/EventProcessorWithCustomStorageVersion.java). 
- [Java Script](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/javascript/receiveEventsWithApiSpecificStorage.js) eller  [typescript](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/eventhub/eventhubs-checkpointstore-blob/samples/typescript/src/receiveEventsWithApiSpecificStorage.ts) 
- Python – [synkron](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob/samples/receive_events_using_checkpoint_store_storage_api_version.py), [asynkron](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/eventhub/azure-eventhub-checkpointstoreblob-aio/samples/receive_events_using_checkpoint_store_storage_api_version_async.py)

Om du kör Event Hubs mottagare som använder Blob Storage som kontroll punkts arkiv utan att behöva rikta den version som Azure Stack Hub har stöd för, får du följande fel meddelande:

```
The value for one of the HTTP headers is not in the correct format
```


## <a name="sample-error-message-in-python"></a>Exempel fel meddelande i python
För python skickas ett fel `azure.core.exceptions.HttpResponseError` till fel hanteraren `on_error(partition_context, error)` för `EventHubConsumerClient.receive()` . Men metoden `receive()` utlöser inte ett undantag. `print(error)` följande undantags information skrivs ut:

```bash
The value for one of the HTTP headers is not in the correct format.

RequestId:f048aee8-a90c-08ba-4ce1-e69dba759297
Time:2020-03-17T22:04:13.3559296Z
ErrorCode:InvalidHeaderValue
Error:None
HeaderName:x-ms-version
HeaderValue:2019-07-07
```

Loggaren kommer att logga två varningar som följande:

```bash
WARNING:azure.eventhub.extensions.checkpointstoreblobaio._blobstoragecsaio: 
An exception occurred during list_ownership for namespace '<namespace-name>.eventhub.<region>.azurestack.corp.microsoft.com' eventhub 'python-eh-test' consumer group '$Default'. 

Exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07')

WARNING:azure.eventhub.aio._eventprocessor.event_processor:EventProcessor instance '26d84102-45b2-48a9-b7f4-da8916f68214' of eventhub 'python-eh-test' consumer group '$Default'. An error occurred while load-balancing and claiming ownership. 

The exception is HttpResponseError('The value for one of the HTTP headers is not in the correct format.\nRequestId:f048aee8-a90c-08ba-4ce1-e69dba759297\nTime:2020-03-17T22:04:13.3559296Z\nErrorCode:InvalidHeaderValue\nError:None\nHeaderName:x-ms-version\nHeaderValue:2019-07-07'). Retrying after 71.45254944090853 seconds
```



## <a name="next-steps"></a>Nästa steg

Mer information om partitionering och kontroll punkter finns i följande artikel: [utjämna partition belastning över flera instanser av programmet](event-processor-balance-partition-load.md)
