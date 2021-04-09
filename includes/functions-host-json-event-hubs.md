---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/04/2018
ms.author: glenga
ms.openlocfilehash: fe80a71125d43220e408eab7b07aeedcafa0a526
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "102473886"
---
### <a name="functions-2x-and-higher"></a>Functions 2.x och senare

```json
{
    "version": "2.0",
    "extensions": {
        "eventHubs": {
            "batchCheckpointFrequency": 5,
            "eventProcessorOptions": {
                "maxBatchSize": 256,
                "prefetchCount": 512
            },
            "initialOffsetOptions": {
                "type": "fromStart",
                "enqueuedTimeUtc": ""
            }
        }
    }
}  
```

|Egenskap  |Standardvärde | Beskrivning |
|---------|---------|---------|
|batchCheckpointFrequency|1|Antalet händelse grupper som ska bearbetas innan en kontroll punkt för EventHub-markören skapas.|
|eventProcessorOptions/maxBatchSize|10|Maximalt antal händelser som tas emot per Receive-slinga.|
|eventProcessorOptions/prefetchCount|300|Standard antalet för hämtningar som används av den underliggande `EventProcessorHost` . Det minsta tillåtna värdet är 10.|
|initialOffsetOptions/typ<sup>1</sup>|fromStart|Den plats i händelse strömmen som bearbetningen ska startas från när en kontroll punkt inte finns i lagrings platsen. Alternativen är `fromStart` `fromEnd` eller `fromEnqueuedTime` . `fromEnd` bearbetar nya händelser som har placerats i kö efter att Function-appen började köras. Gäller för alla partitioner.  Mer information finns i EventProcessorOptions- [dokumentationen](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider).|
|initialOffsetOptions/enqueuedTimeUtc<sup>1</sup>|Ej tillämpligt| Anger den köade tiden för händelsen i strömmen som bearbetningen ska startas från. `initialOffsetOptions/type` `fromEnqueuedTime` Den här inställningen är obligatorisk när konfigureras som. Stöder tid i alla format som stöds av [DateTime. parse ()](/dotnet/standard/base-types/parsing-datetime), till exempel  `2020-10-26T20:31Z` . För tydlighetens skull bör du även ange en tidszon. När ingen tidszon anges förutsätter funktionen den lokala tids zonen för den dator som kör Function-appen, som är UTC när den körs på Azure. Mer information finns i EventProcessorOptions- [dokumentationen](/dotnet/api/microsoft.azure.eventhubs.processor.eventprocessoroptions.initialoffsetprovider).|

<sup>1</sup> stöd för `intitialOffsetOptions` börjar med [EventHubs v-4.2.0](https://github.com/Azure/azure-functions-eventhubs-extension/releases/tag/v4.2.0).

> [!NOTE]
> En referens för host.jspå i Azure Functions 2. x och senare finns i [host.jsför referens för Azure Functions](../articles/azure-functions/functions-host-json.md).

### <a name="functions-1x"></a>Functions 1.x

```json
{
    "eventHub": {
      "maxBatchSize": 64,
      "prefetchCount": 256,
      "batchCheckpointFrequency": 1
    }
}
```

|Egenskap  |Standardvärde | Beskrivning |
|---------|---------|---------| 
|maxBatchSize|64|Maximalt antal händelser som tas emot per Receive-slinga.|
|prefetchCount|saknas|Standard för hämtning som ska användas av den underliggande `EventProcessorHost` .| 
|batchCheckpointFrequency|1|Antalet händelse grupper som ska bearbetas innan en kontroll punkt för EventHub-markören skapas.| 

> [!NOTE]
> En referens för host.jspå i Azure Functions 1. x finns i [host.jsför referens för Azure Functions 1. x](../articles/azure-functions/functions-host-json-v1.md).
