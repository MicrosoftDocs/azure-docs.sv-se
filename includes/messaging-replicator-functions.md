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
ms.openlocfilehash: 279a00a6146d756e6a518dbf86b88f471d170b3a
ms.sourcegitcommit: 7e97ae405c1c6c8ac63850e1b88cf9c9c82372da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/29/2020
ms.locfileid: "97805619"
---
## <a name="what-is-a-replication-task"></a>Vad är en replikering?

En replikeringslänk tar emot händelser från en källa och vidarebefordrar dem till ett mål.
De flesta replikerings aktiviteter vidarebefordrar händelser som inte har ändrats och utför mappningen mellan metadata strukturer om käll-och mål protokollen är olika. 

Replikeringsdata är i allmänhet tillstånds lösa, vilket innebär att de inte delar tillstånd eller andra sido effekter över sekventiella eller parallella körningar av en aktivitet. Detta gäller även för batching och länkning, som båda kan implementeras ovanpå det befintliga läget i en data ström. 

Detta gör att replikeringsdata skiljer sig från agg regerings aktiviteter, som är allmänt tillstånds känsliga och är domänen för analys ramverk och tjänster som [Azure Stream Analytics](/azure/stream-analytics/stream-analytics-introduction).

## <a name="replication-applications-and-tasks-in-azure-functions"></a>Replikering av program och uppgifter i Azure Functions

I Azure Functions implementeras en replikering med en [utlösare](/azure/azure-functions/functions-triggers-bindings) som skaffar ett eller flera indatameddelande från en konfigurerad källa och en [utgående bindning](/azure/azure-functions/functions-triggers-bindings#binding-direction) som vidarebefordrar meddelanden som kopieras från källan till ett konfigurerat mål. 

| Utlösare  | Resultat |
|----------|--------|
| [Azure Event Hubs-utlösare](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-hubs-trigger?tabs=csharp) | [Utdata-bindning för Azure Event Hub](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-hubs-output?tabs=csharp) |
| [Azure Service Bus utlösare](https://docs.microsoft.com/azure/azure-functions/functions-bindings-service-bus-trigger?tabs=csharp) | [Azure Service Bus utgående bindning](https://docs.microsoft.com/azure/azure-functions/functions-bindings-service-bus-output?tabs=csharp)
| [Azure IoT Hub-utlösare](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-iot-trigger?tabs=csharp) | [Utgående bindning för Azure IoT Hub](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-iot-output?tabs=csharp)
| [Azure Event Grid utlösare](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-grid-trigger?tabs=csharp) | [Azure Event Grid utgående bindning](https://docs.microsoft.com/azure/azure-functions/functions-bindings-event-grid-output?tabs=csharp)
| [Azure Queue Storage-utlösare](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-queue-trigger?tabs=csharp) | [Utgående bindning för Azure Queue Storage](https://docs.microsoft.com/azure/azure-functions/functions-bindings-storage-queue-output?tabs=csharp)
| [Apache Kafka utlösare](https://github.com/azure/azure-functions-kafka-extension) | [Apache Kafka utgående bindning](https://github.com/azure/azure-functions-kafka-extension)
| [RabbitMQ-utlösare](https://github.com/azure/azure-functions-rabbitmq-extension) | [RabbitMQ utgående bindning](https://github.com/azure/azure-functions-rabbitmq-extension) 
| | [Utgående bindning för Azure Notification Hubs](https://docs.microsoft.com/azure/azure-functions/functions-bindings-notification-hubs)
||[Utgående bindning för Azure SignalR service](https://docs.microsoft.com/azure/azure-functions/functions-bindings-signalr-service-output?tabs=csharp)
||[Twilio SendGrid utgående bindning](https://docs.microsoft.com/azure/azure-functions/functions-bindings-sendgrid?tabs=csharp)

Replikeringsdata distribueras som ett program för replikering via samma distributions metoder som andra Azure Functions program. Du kan konfigurera flera aktiviteter i samma program. 

Med Azure Functions Premium kan flera replikerade program dela samma underliggande resurspool, som kallas en App Service plan. Det innebär att du enkelt kan samordna för replikering som skrivits i .NET med replikeringsdata som är skrivna i Java, till exempel. Det kan vara bra om du vill dra nytta av specifika bibliotek som Apache kamel notation som endast är tillgängliga för Java och om de är det bästa alternativet för en viss integrations Sök väg, även om du vanligt vis föredrar ett annat språk och kör tid för andra replikerings aktiviteter. 

När det är tillgängligt bör du föredra de batch-orienterade utlösare som levererar enskilda händelser eller meddelanden och du bör alltid hämta hela händelse-eller meddelande strukturen i stället för att lita på Azure Functions [parameter bindnings uttryck](https://docs.microsoft.com/azure/azure-functions/functions-bindings-expressions-patterns).

Namnet på funktionen ska återspegla det par av källa och mål som du ansluter, och du bör ange referenser till anslutnings strängar eller andra konfigurations element i programmets konfigurationsfiler med det namnet. 

### <a name="data-and-metadata-mapping"></a>Mappning av data och metadata

När du har bestämt dig för ett par med indata-utlösare och utgående bindning måste du utföra vissa mappningar mellan olika händelse-eller meddelande typer, såvida inte utlösaren och utdata är desamma.

För enkla replikeringar som kopierar meddelanden mellan Event Hubs och Service Bus behöver du inte skriva din egen kod, men du kan skapa ett [verktygs bibliotek som medföljer](https://github.com/Azure-Samples/azure-messaging-replication-dotnet/tree/main/src/Azure.Messaging.Replication) replikerings exemplen.

### <a name="retry-policy"></a>Återförsöksprincip

För att undvika data förlust under tillgänglighets händelse på någon sida av en replikeringslänk måste du konfigurera principen för återförsök för att vara robust. Läs Azure Functions- [dokumentationen om försök](/azure/azure-functions/functions-bindings-error-pages) att konfigurera principen för återförsök. 

De princip inställningar som valts för exempel projekt i [exempel lagrings platsen](https://github.com/Azure-Samples/azure-messaging-replication-dotnet) konfigurerar en exponentiell backoff-strategi med återförsöksintervall mellan 5 och 15 minuter med oändliga återförsök för att undvika data förlust. 

För Service Bus kan du läsa avsnittet ["använda stöd för återförsök ovanpå utlösnings återhämtning"](/azure/azure-functions/functions-bindings-error-pages#using-retry-support-on-top-of-trigger-resilience) för att förstå interaktionen mellan utlösare och det maximala antalet leveranser som definierats för kön.

### <a name="setting-up-a-replication-application-host"></a>Konfigurera en värd för replikerings program

Ett program för replikering är en körnings värd för en eller flera replikerings aktiviteter. 

Det är ett Azure Functions program som har kon figurer ATS för att köras antingen i förbruknings planen eller (rekommenderas) i en Azure Functions Premium-plan. Alla program för replikering måste köras under en [system-eller användardefinierad hanterad identitet](/azure/app-service/overview-managed-identity). 

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

Information om hur du övervakar din app för replikering finns i [avsnittet övervakning](https://docs.microsoft.com/azure/azure-functions/configure-monitoring) i Azure Functions-dokumentationen.

Ett särskilt användbart visuellt verktyg som används för att övervaka replikering är Application Insights [program avbildningen](https://docs.microsoft.com/azure/azure-monitor/app/app-map), som genereras automatiskt från insamlad övervaknings information och som gör det möjligt att utforska tillförlitligheten och prestandan för replikeringens aktivitets källa och mål överföringar.

För omedelbara diagnostiska insikter kan du arbeta med verktyget [Live Metrics](https://docs.microsoft.com/azure/azure-monitor/app/live-stream) Portal, vilket ger låg latens visualisering av logg information.

## <a name="next-steps"></a>Nästa steg

* [Azure Functions distributioner](/azure/azure-functions/functions-deployment-technologies)
* [Azure Functions diagnostik](/azure/azure-functions/functions-diagnostics)
* [Azure Functions nätverks alternativ](/azure/azure-functions/functions-networking-options)
* [Azure Application Insights](/azure/azure-monitor/app/app-insights-overview)