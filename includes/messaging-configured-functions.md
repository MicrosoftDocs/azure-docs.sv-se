---
title: ta med fil
description: ta med fil
services: service-bus-messaging, event-hubs
author: spelluru
ms.service: service-bus-messaging, event-hubs
ms.topic: include
ms.date: 12/12/2020
ms.author: spelluru
ms.custom: include file
ms.openlocfilehash: 9bc641d680d927c44814f6814ebf6a6dde958c9e
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/27/2021
ms.locfileid: "98901227"
---
Azure Functions möjliggör skapande av konfigurations aktiviteter som är Lean-baserade på en fördefinierad start punkt. De [konfigurations exemplen för replikering för Azure Functions](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config) illustrerar hur du kan använda [färdiga hjälp program](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) i din egen kod eller undvika att hantera kod helt och bara använda konfigurationen.

## <a name="create-a-replication-task"></a>Skapa en replikerings aktivitet
Skapa en sådan aktivitet genom att först skapa en ny mapp under projektmappen. Namnet på den nya mappen är namnet på funktionen, till exempel `europeToAsia` eller `telemetry` . Namnet har ingen direkt korrelation med de meddelande enheter som används och fungerar bara för att identifiera dem. Du kan skapa dussin tals funktioner i samma projekt.

Skapa sedan en `function.json` fil i mappen. Filen konfigurerar funktionen. Börja med följande innehåll:

``` JSON
{
    "configurationSource": "config",
    "bindings" : [
        {
            "direction": "in",
            "name": "input" 
        },
        {
            "direction": "out",
            "name": "output"
        }
    ],
    "retry": {
        "strategy": "exponentialBackoff",
        "maxRetryCount": -1,
        "minimumInterval": "00:00:05",
        "maximumInterval": "00:05:00"
    },
    "disabled": false,
    "scriptFile": "../bin/Azure.Messaging.Replication.dll",
    "entryPoint": "Azure.Messaging.Replication.*"
}
```

I den filen måste du slutföra tre konfigurations steg som är beroende av vilka entiteter du vill ansluta till:

1. [Konfigurera ingångs riktningen](#configure-the-input-direction)
2. [Konfigurera utdata riktningen](#configure-the-output-direction)
3. [Konfigurera Start punkten](#configure-the-entry-point)

### <a name="configure-the-input-direction"></a>Konfigurera ingångs riktningen

#### <a name="event-hub-input"></a>Inmatade Event Hub

Om du vill ta emot händelser från en Händelsehubben lägger du till konfigurations information i det övre avsnittet i "bindningar" som anger

* **Skriv** "eventHubTrigger"-typ.
* **anslutning** – namnet på appens inställnings värde för anslutnings strängen för Event Hub. 
* **eventHubName** – namnet på händelsehubben i namn området som identifieras av anslutnings strängen.
* **consumerGroup** – namnet på konsument gruppen. Observera att namnet omges av procent tecken och refererar därför också till ett inställnings värde för appar.

```JSON
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "eventHubTrigger",
            "connection": "functionname-source-connection",
            "eventHubName": "eventHubA",
            "consumerGroup" : "%functionname-source-consumergroup%",
            "name": "input" 
        }
    ...
```

#### <a name="service-bus-queue-input"></a>Service Bus Queue-indatamängd

Om du vill ta emot händelser från en Service Bus kö lägger du till konfigurations information i det övre avsnittet i "bindningar" som anger

* **Skriv** "serviceBusTrigger"-typ.
* **anslutning** – namnet på App Settings-värdet för Service Bus anslutnings strängen.
* **queueName** – namnet på Service Bus kön i namn området som identifieras av anslutnings strängen.

```JSON
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "serviceBusTrigger",
            "connection": "functionname-source-connection",
            "queueName": "queue-a",
            "name": "input" 
        }
    ...
```

#### <a name="service-bus-topic-input"></a>Service Bus ämnes information

Om du vill ta emot händelser från ett Service Bus ämne lägger du till konfigurations information i det övre avsnittet i "bindningar" som anger

* **Skriv** "serviceBusTrigger"-typ.
* **anslutning** – namnet på App Settings-värdet för Service Bus anslutnings strängen. Det här värdet måste vara `{FunctionName}-source-connection` om du vill använda de angivna skripten.
* **topicName** – namnet på Service Bus ämnet i namn området som identifieras av anslutnings strängen.
* **subscriptionName** – namnet på Service Bus prenumerationen på den angivna artikeln i namn rymden som identifieras av anslutnings strängen.

```JSON
    ...
    "bindings" : [
        {
            "direction": "in",
            "type": "serviceBusTrigger",
            "connection": "functionname-source-connection",
            "topicName": "topic-x",
            "subscriptionName" : "sub-y",
            "name": "input" 
        }
    ...
```

### <a name="configure-the-output-direction"></a>Konfigurera utdata riktningen

#### <a name="event-hub-output"></a>Utdata för Event Hub

Om du vill vidarebefordra händelser till en Händelsehubben lägger du till konfigurations information i det nedre avsnittet i "bindningar" som anger

* **Skriv** typen "eventHub".
* **anslutning** – namnet på appens inställnings värde för anslutnings strängen för Event Hub. 
* **eventHubName** – namnet på händelsehubben i namn området som identifieras av anslutnings strängen.

```JSON
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "eventHub",
            "connection": "functionname-target-connection",
            "eventHubName": "eventHubB",
            "name": "output" 
        }
    ...
```

#### <a name="service-bus-queue-output"></a>Service Bus Queue-utdata

Om du vill vidarebefordra händelser till en Service Bus kö lägger du till konfigurations information i det nedre avsnittet i "bindningar" som anger

* **Skriv** "Service Bus"-typ.
* **anslutning** – namnet på App Settings-värdet för Service Bus anslutnings strängen. 
* **queueName** – namnet på Service Bus kön i namn området som identifieras av anslutnings strängen.

```JSON
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "serviceBus",
            "connection": "functionname-target-connection",
            "queueName": "queue-b",
            "name": "output" 
        }
    ...
```

#### <a name="service-bus-topic-output"></a>Utdata för Service Bus ämnet

Om du vill vidarebefordra händelser till ett Service Bus ämne lägger du till konfigurations information i det nedre avsnittet i "bindningar" som anger

* **Skriv** "Service Bus"-typ.
* **anslutning** – namnet på App Settings-värdet för Service Bus anslutnings strängen. 
* **topicName** – namnet på Service Bus ämnet i namn området som identifieras av anslutnings strängen.

```JSON
    ...
    "bindings" : [
        {
            ...
        },
        {
            "direction": "out",
            "type": "serviceBus",
            "connection": "functionname-target-connection",
            "topicName": "topic-b",
            "name": "output" 
        }
    ...
```

### <a name="configure-the-entry-point"></a>Konfigurera Start punkten

Start punkts konfigurationen väljer en av aktiviteterna för standard replikering. Om du ändrar `Azure.Messaging.Replication` projektet kan du också lägga till uppgifter och referera till dem här. Till exempel:

```JSON
    ...
    "scriptFile": "../dotnet/bin/Azure.Messaging.Replication.dll",
    "entryPoint": "Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub"
    ...
```

I följande tabell får du korrekta värden för kombinationer av källor och mål:

| Källa      | Mål      | Start punkt 
|-------------|-------------|------------------------------------------------------------------------
| Händelsehubb   | Händelsehubb   | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub`
| Händelsehubb   | Service Bus | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToServiceBus`
| Service Bus | Händelsehubb   | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToEventHub`
| Service Bus | Service Bus | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToServiceBus`

### <a name="retry-policy"></a>Återförsöksprincip

Läs Azure Functions- [dokumentationen om försök](../articles/azure-functions/functions-bindings-error-pages.md) att konfigurera principen för återförsök. Princip inställningarna som väljs i alla projekt i den här lagrings platsen konfigurerar en exponentiell backoff-strategi med återförsöksintervall mellan 5 sekunder och 5 minuter med oändliga återförsök för att undvika data förlust.

För Service Bus kan du läsa avsnittet ["använda stöd för återförsök ovanpå utlösnings återhämtning"](../articles/azure-functions/functions-bindings-error-pages.md#using-retry-support-on-top-of-trigger-resilience) för att förstå interaktionen mellan utlösare och det maximala antalet leveranser som definierats för kön.

### <a name="build-deploy-and-configure"></a>Bygg, distribuera och konfigurera

Medan du fokuserar på konfigurationen behöver aktiviteterna fortfarande skapa ett distributions bara program och konfigurera Azure Functions-värdar så att den har all information som krävs för att ansluta till de specifika slut punkterna. 

Detta illustreras tillsammans med återanvändbara skript i [exemplen för konfigurations-baserade replikeringar för Azure Functions](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/functions/config).