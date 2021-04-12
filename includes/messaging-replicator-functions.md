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
ms.openlocfilehash: 1ce983ee739a4a124a93c7913f092b23dfec3cbd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98901228"
---
## <a name="what-is-a-replication-task"></a>Vad är en replikering?

En replikeringslänk tar emot händelser från en källa och vidarebefordrar dem till ett mål.
De flesta replikerings aktiviteter vidarebefordrar händelser som inte har ändrats och utför mappningen mellan metadata strukturer om käll-och mål protokollen är olika. 

Replikeringsdata är i allmänhet tillstånds lösa, vilket innebär att de inte delar tillstånd eller andra sido effekter över sekventiella eller parallella körningar av en aktivitet. Detta gäller även för batching och länkning, som båda kan implementeras ovanpå det befintliga läget i en data ström. 

Detta gör att replikeringsdata skiljer sig från agg regerings aktiviteter, som är allmänt tillstånds känsliga och är domänen för analys ramverk och tjänster som [Azure Stream Analytics](../articles/stream-analytics/stream-analytics-introduction.md).

## <a name="replication-applications-and-tasks-in-azure-functions"></a>Replikering av program och uppgifter i Azure Functions

I Azure Functions implementeras en replikering med en [utlösare](../articles/azure-functions/functions-triggers-bindings.md) som skaffar ett eller flera indatameddelande från en konfigurerad källa och en [utgående bindning](../articles/azure-functions/functions-triggers-bindings.md#binding-direction) som vidarebefordrar meddelanden som kopieras från källan till ett konfigurerat mål. 

| Utlösare  | Resultat |
|----------|--------|
| [Azure Event Hubs-utlösare](../articles/azure-functions/functions-bindings-event-hubs-trigger.md?tabs=csharp) | [Utdata-bindning för Azure Event Hub](../articles/azure-functions/functions-bindings-event-hubs-output.md?tabs=csharp) |
| [Azure Service Bus utlösare](../articles/azure-functions/functions-bindings-service-bus-trigger.md?tabs=csharp) | [Azure Service Bus utgående bindning](../articles/azure-functions/functions-bindings-service-bus-output.md?tabs=csharp)
| [Azure IoT Hub-utlösare](../articles/azure-functions/functions-bindings-event-iot-trigger.md?tabs=csharp) | [Utgående bindning för Azure IoT Hub](../articles/azure-functions/functions-bindings-event-iot-output.md?tabs=csharp)
| [Azure Event Grid utlösare](../articles/azure-functions/functions-bindings-event-grid-trigger.md?tabs=csharp) | [Azure Event Grid utgående bindning](../articles/azure-functions/functions-bindings-event-grid-output.md?tabs=csharp)
| [Azure Queue Storage-utlösare](../articles/azure-functions/functions-bindings-storage-queue-trigger.md?tabs=csharp) | [Utgående bindning för Azure Queue Storage](../articles/azure-functions/functions-bindings-storage-queue-output.md?tabs=csharp)
| [Apache Kafka utlösare](https://github.com/azure/azure-functions-kafka-extension) | [Apache Kafka utgående bindning](https://github.com/azure/azure-functions-kafka-extension)
| [RabbitMQ-utlösare](https://github.com/azure/azure-functions-rabbitmq-extension) | [RabbitMQ utgående bindning](https://github.com/azure/azure-functions-rabbitmq-extension) 
| | [Utgående bindning för Azure Notification Hubs](../articles/azure-functions/functions-bindings-notification-hubs.md)
||[Utgående bindning för Azure SignalR service](../articles/azure-functions/functions-bindings-signalr-service-output.md?tabs=csharp)
||[Twilio SendGrid utgående bindning](../articles/azure-functions/functions-bindings-sendgrid.md?tabs=csharp)

Replikeringsdata distribueras som ett program för replikering via samma distributions metoder som andra Azure Functions program. Du kan konfigurera flera aktiviteter i samma program. 

Med Azure Functions Premium kan flera replikerade program dela samma underliggande resurspool, som kallas en App Service plan. Det innebär att du enkelt kan samordna för replikering som skrivits i .NET med replikeringsdata som är skrivna i Java, till exempel. Det kan vara bra om du vill dra nytta av specifika bibliotek som Apache kamel notation som endast är tillgängliga för Java och om de är det bästa alternativet för en viss integrations Sök väg, även om du vanligt vis föredrar ett annat språk och kör tid för andra replikerings aktiviteter. 

När det är tillgängligt bör du föredra de batch-orienterade utlösare som levererar enskilda händelser eller meddelanden och du bör alltid hämta hela händelse-eller meddelande strukturen i stället för att lita på Azure Functions [parameter bindnings uttryck](../articles/azure-functions/functions-bindings-expressions-patterns.md).

Namnet på funktionen ska återspegla det par av källa och mål som du ansluter, och du bör ange referenser till anslutnings strängar eller andra konfigurations element i programmets konfigurationsfiler med det namnet. 

### <a name="data-and-metadata-mapping"></a>Mappning av data och metadata

När du har bestämt dig för ett par med indata-utlösare och utgående bindning måste du utföra vissa mappningar mellan olika händelse-eller meddelande typer, såvida inte utlösaren och utdata är desamma.

För enkla replikeringar som kopierar meddelanden mellan Event Hubs och Service Bus behöver du inte skriva din egen kod, men du kan skapa ett [verktygs bibliotek som medföljer](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) replikerings exemplen.

### <a name="retry-policy"></a>Återförsöksprincip

För att undvika data förlust under tillgänglighets händelse på någon sida av en replikeringslänk måste du konfigurera principen för återförsök för att vara robust. Läs Azure Functions- [dokumentationen om försök](../articles/azure-functions/functions-bindings-error-pages.md) att konfigurera principen för återförsök. 

De princip inställningar som valts för exempel projekt i [exempel lagrings platsen](https://github.com/Azure-Samples/azure-messaging-replication-dotnet) konfigurerar en exponentiell backoff-strategi med återförsöksintervall mellan 5 och 15 minuter med oändliga återförsök för att undvika data förlust. 

För Service Bus kan du läsa avsnittet ["använda stöd för återförsök ovanpå utlösnings återhämtning"](../articles/azure-functions/functions-bindings-error-pages.md#using-retry-support-on-top-of-trigger-resilience) för att förstå interaktionen mellan utlösare och det maximala antalet leveranser som definierats för kön.

### <a name="setting-up-a-replication-application-host"></a>Konfigurera en värd för replikerings program

Ett program för replikering är en körnings värd för en eller flera replikerings aktiviteter. 

Det är ett Azure Functions program som har kon figurer ATS för att köras antingen i förbruknings planen eller (rekommenderas) i en Azure Functions Premium-plan. Alla program för replikering måste köras under en [system-eller användardefinierad hanterad identitet](../articles/app-service/overview-managed-identity.md). 

De länkade Azure Resource Manager (ARM) mallarna skapar och konfigurerar ett program för replikering med:

* ett Azure Storage konto för att spåra replikeringens förlopp och för loggar
* en systemtilldelad hanterad identitet och 
* Azure-övervakning och Application Insights-integrering för övervakning.

Replikeringsinställningar som måste komma åt Event Hubs bundna till ett virtuellt Azure-nätverk (VNet) måste använda Azure Functions Premium-planen och konfigureras för att ansluta till samma VNet, som också är ett av de tillgängliga alternativen.


|       | Distribuera | Visualisera  
|-------|------------------|--------------|---------------|
| **Azure Functions förbruknings plan** | [![Distribuera till Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2FAconsumption%2Fazuredeploy.json)|[![Visualisera](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fconsumption%2Fazuredeploy.json)
| **Azure Functions Premium-plan** |[![Distribuera till Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium%2Fazuredeploy.json) | [![Visualisera](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium%2Fazuredeploy.json)
| **Azure Functions Premium-plan med VNet** | [![Distribuera till Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium-vnet%2Fazuredeploy.json)|[![Visualisera](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fazure-messaging-replication-dotnet%2Fmain%2Ftemplates%2Fpremium-vnet%2Fazuredeploy.json)


### <a name="examples"></a>Exempel

[Exempel lagrings platsen](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/) innehåller flera exempel på replikerings aktiviteter som kopierar händelser mellan Event Hubs och/eller mellan Service Bus entiteter.

Om du vill kopiera en händelse mellan Event Hubs använder du en Event Hub-utlösare med en Event Hub-utgående bindning:

```csharp
[FunctionName("telemetry")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static Task Telemetry(
    [EventHubTrigger("telemetry", ConsumerGroup = "$USER_FUNCTIONS_APP_NAME.telemetry", Connection = "telemetry-source-connection")] EventData[] input,
    [EventHub("telemetry-copy", Connection = "telemetry-target-connection")] EventHubClient outputClient,
    ILogger log)
{
    return EventHubReplicationTasks.ForwardToEventHub(input, outputClient, log);
}
```

För att kopiera meddelanden mellan Service Bus entiteter använder du Service Bus utlösare och utgående bindning:

```csharp
[FunctionName("jobs-transfer")]
[ExponentialBackoffRetry(-1, "00:00:05", "00:05:00")]
public static Task JobsTransfer(
    [ServiceBusTrigger("jobs-transfer", Connection = "jobs-transfer-source-connection")] Message[] input,
    [ServiceBus("jobs", Connection = "jobs-target-connection")] IAsyncCollector<Message> output,
    ILogger log)
{
    return ServiceBusReplicationTasks.ForwardToServiceBus(input, output, log);
}
```

Med hjälp av metoderna kan du enkelt replikera mellan Event Hubs och Service Bus:

| Källa      | Mål      | Start punkt 
|-------------|-------------|------------------------------------------------------------------------
| Händelsehubb   | Händelsehubb   | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToEventHub`
| Händelsehubb   | Service Bus | `Azure.Messaging.Replication.EventHubReplicationTasks.ForwardToServiceBus`
| Service Bus | Händelsehubb   | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToEventHub`
| Service Bus | Service Bus | `Azure.Messaging.Replication.ServiceBusReplicationTasks.ForwardToServiceBus`


### <a name="monitoring"></a>Övervakning

Information om hur du övervakar din app för replikering finns i [avsnittet övervakning](../articles/azure-functions/configure-monitoring.md) i Azure Functions-dokumentationen.

Ett särskilt användbart visuellt verktyg som används för att övervaka replikering är Application Insights [program avbildningen](../articles/azure-monitor/app/app-map.md), som genereras automatiskt från insamlad övervaknings information och som gör det möjligt att utforska tillförlitligheten och prestandan för replikeringens aktivitets källa och mål överföringar.

För omedelbara diagnostiska insikter kan du arbeta med verktyget [Live Metrics](../articles/azure-monitor/app/live-stream.md) Portal, vilket ger låg latens visualisering av logg information.

## <a name="next-steps"></a>Nästa steg

* [Azure Functions distributioner](../articles/azure-functions/functions-deployment-technologies.md)
* [Azure Functions diagnostik](../articles/azure-functions/functions-diagnostics.md)
* [Azure Functions nätverks alternativ](../articles/azure-functions/functions-networking-options.md)
* [Azure Application insikter](../articles/azure-monitor/app/app-insights-overview.md)