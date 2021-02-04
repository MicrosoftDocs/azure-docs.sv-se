---
title: Azure Service Bus slut punkt till slut punkt för spårning och diagnostik | Microsoft Docs
description: Översikt över Service Bus-klientautentisering och spårning från slut punkt till slut punkt (klient genom alla tjänster som ingår i bearbetningen.)
ms.topic: article
ms.date: 02/03/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 19b284aceb83fbbc2bcf662b2b58941e6a5b36f9
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539221"
---
# <a name="distributed-tracing-and-correlation-through-service-bus-messaging"></a>Distribuerad spårning och korrelation genom Service Bus meddelanden

Ett av de vanliga problemen vid Micro Services-utveckling är möjligheten att spåra åtgärder från en klient genom alla tjänster som ingår i bearbetningen. Det är användbart för fel sökning, prestanda analys, A/B-testning och andra typiska diagnos scenarier.
En del av det här problemet spårar logiska arbets delar. Det omfattar meddelande behandlings resultat och svars tider och externa beroende anrop. En annan del är en korrelation av dessa diagnostiska händelser utöver process gränser.

När en producent skickar ett meddelande via en kö, sker det vanligt vis i omfånget för en annan logisk åtgärd som initieras av en annan klient eller tjänst. Samma åtgärd fortsätter av konsumenten när den får ett meddelande. Både producent och konsument (och andra tjänster som bearbetar åtgärden) genererar sedan telemetri-händelser för att spåra åtgärds flödet och resultatet. För att kunna korrelera sådana händelser och spåra åtgärder från slut punkt till slut punkt måste varje tjänst som rapporterar telemetri stämpla varje händelse med en spårnings kontext.

Microsoft Azure Service Bus Messaging har definierat nytto Last egenskaper som producenter och konsumenter bör använda för att skicka sådan spårnings kontext.
Protokollet baseras på [http-korrelations protokollet](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md).

# <a name="azuremessagingservicebus-sdk-latest"></a>[Azure. Messaging. Service Bus SDK (senaste)](#tab/net-standard-sdk-2)
| Egenskapens namn        | Description                                                 |
|----------------------|-------------------------------------------------------------|
|  Diagnostic-Id       | Unikt ID för ett externt anrop från producent till kön. Se [begärande-ID i HTTP-protokollet](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) för att få rationella, överväganden och format |

## <a name="service-bus-net-client-autotracing"></a>Service Bus .NET-klient spårning
`ServiceBusProcessor`Klassen för [Azure Messaging Service Bus-klienten för .net](/dotnet/api/azure.messaging.servicebus.servicebusprocessor) innehåller spårnings instrument som kan kopplas till spårnings system eller del av klient kod. Med instrumentering kan du spåra alla anrop till tjänsten för Service Bus meddelande tjänsten från klient sidan. Om meddelande bearbetningen görs med hjälp [ `ProcessMessageAsync` av `ServiceBusProcessor` ](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processmessageasync) (meddelande hanterarens mönster) instrumenteras även meddelande bearbetningen.

### <a name="tracking-with-azure-application-insights"></a>Spåra med Azure Application insikter

[Microsoft Application Insights](https://azure.microsoft.com/services/application-insights/) ger omfattande prestanda övervaknings funktioner, inklusive automagic-begäran och beroende spårning.

Installera Application Insights SDK, beroende på din projekt typ:
- [ASP.net](../azure-monitor/app/asp-net.md) -installation version 2,5-beta2 eller högre
- [ASP.net Core](../azure-monitor/app/asp-net-core.md) -installera version 2.2.0-beta2 eller högre.
Dessa länkar innehåller information om hur du installerar SDK, skapar resurser och konfigurerar SDK (om det behövs). Information om non-ASP.NET-program finns i artikeln [Azure Application Insights för konsol program](../azure-monitor/app/console.md) .

Om du använder [ `ProcessMessageAsync` `ServiceBusProcessor` (mönster för meddelande](/dotnet/api/azure.messaging.servicebus.servicebusprocessor.processmessageasync) hanterare) för att bearbeta meddelanden, instrumenteras även meddelande bearbetningen. Alla Service Bus-anrop som utförs av tjänsten spåras automatiskt och korreleras med andra telemetri objekt. I annat fall refererar vi till följande exempel för manuell meddelande bearbetnings spårning.

#### <a name="trace-message-processing"></a>Behandling av spåra meddelanden

```csharp
async Task ProcessAsync(ProcessMessageEventArgs args)
{
    ServiceBusReceivedMessage message = args.Message;
    if (message.ApplicationProperties.TryGetValue("Diagnostic-Id", out var objectId) && objectId is string diagnosticId)
    {
        var activity = new Activity("ServiceBusProcessor.ProcessMessage");
        activity.SetParentId(diagnosticId);
        // If you're using Microsoft.ApplicationInsights package version 2.6-beta or higher, you should call StartOperation<RequestTelemetry>(activity) instead
        using (var operation = telemetryClient.StartOperation<RequestTelemetry>("Process", activity.RootId, activity.ParentId))
        {
            telemetryClient.TrackTrace("Received message");
            try 
            {
            // process message
            }
            catch (Exception ex)
            {
                telemetryClient.TrackException(ex);
                operation.Telemetry.Success = false;
                throw;
            }

            telemetryClient.TrackTrace("Done");
        }
    }
}
```

I det här exemplet rapporteras begäran om telemetri för varje bearbetat meddelande, med en tidsstämpel, varaktighet och resultat (lyckades). Telemetri har också en uppsättning korrelations egenskaper. Kapslade spår och undantag som rapporteras vid meddelande bearbetning stämplas också med korrelations egenskaper som representerar dem som underordnade till `RequestTelemetry` .

Om du gör anrop till stödda externa komponenter under meddelande bearbetningen spåras de också automatiskt och korreleras. Se [spåra anpassade åtgärder med Application Insights .NET SDK](../azure-monitor/app/custom-operations-tracking.md) för manuell spårning och korrelation.

Om du kör någon extern kod utöver Application Insights SDK, förväntar du dig att se längre **tid** när du visar Application Insights loggar. 

![Längre varaktighet i Application Insights loggen](./media/service-bus-end-to-end-tracing/longer-duration.png)

Det innebär inte att det tog lång tid att ta emot meddelandet. I det här scenariot har meddelandet redan mottagits sedan meddelandet skickades som en parameter till SDK-koden. Och **namn** -taggen i App Insights-loggarna (**processen**) anger att meddelandet nu bearbetas av den externa händelse bearbetnings koden. Det här problemet är inte Azure-relaterat. De här måtten avser i stället effektiviteten hos din externa kod, under förutsättning att meddelandet redan har tagits emot från Service Bus. 

### <a name="tracking-without-tracing-system"></a>Spåra utan spårnings system
Om spårnings systemet inte stöder automatisk Service Bus samtals spårning kan du behöva lägga till sådant stöd i ett spårnings system eller i ditt program. I det här avsnittet beskrivs diagnostiska händelser som skickas av Service Bus .NET-klienten.  

Service Bus .NET-klienten instrumenteras med hjälp av .NET tracing primitiver [system. Diagnostics. Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) och [system. Diagnostics. DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md).

`Activity` fungerar som en spårnings kontext medan `DiagnosticSource` är en meddelande funktion. 

Om det inte finns någon lyssnare för DiagnosticSource-händelserna, är instrumentering inaktiverat, och behåller inga instrument kostnader. DiagnosticSource ger all kontroll till lyssnaren:
- lyssnare styr vilka källor och händelser som ska bevakas
- lyssnare kontrollerar händelse frekvens och sampling
- händelser skickas med en nytto last som ger fullständig kontext så att du kan komma åt och ändra meddelande objekt under händelsen

Bekanta dig med [användar handboken för DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) innan du fortsätter med implementeringen.

Nu ska vi skapa en lyssnare för Service Bus händelser i ASP.NET Core app som skriver loggar med Microsoft. extension. Logga.
Den använder [system. Reactive. Core](https://www.nuget.org/packages/System.Reactive.Core) -biblioteket för att prenumerera på DiagnosticSource (det är också enkelt att prenumerera på DiagnosticSource utan IT)

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory factory, IApplicationLifetime applicationLifetime)
{
    // configuration...

    var serviceBusLogger = factory.CreateLogger("Azure.Messaging.ServiceBus");

    IDisposable innerSubscription = null;
    IDisposable outerSubscription = DiagnosticListener.AllListeners.Subscribe(delegate (DiagnosticListener listener)
    {
        // subscribe to the Service Bus DiagnosticSource
        if (listener.Name == "Azure.Messaging.ServiceBus")
        {
            // receive event from Service Bus DiagnosticSource
            innerSubscription = listener.Subscribe(delegate (KeyValuePair<string, object> evnt)
            {
                // Log operation details once it's done
                if (evnt.Key.EndsWith("Stop"))
                {
                    Activity currentActivity = Activity.Current;
                    serviceBusLogger.LogInformation($"Operation {currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}");
                }
            });
        }
    });

    applicationLifetime.ApplicationStopping.Register(() =>
    {
        outerSubscription?.Dispose();
        innerSubscription?.Dispose();
    });
}
```

I det här exemplet loggar Listener varaktighet, resultat, unik identifierare och start tid för varje Service Bus åtgärd.

### <a name="events"></a>Händelser
Alla händelser har följande egenskaper som stämmer med den öppna telemetri-specifikationen: https://github.com/open-telemetry/opentelemetry-specification/blob/master/specification/trace/api.md .

- `message_bus.destination` – kö/ämne/prenumerations Sök väg
- `peer.address` – fullständigt kvalificerat namn område
- `kind` – antingen producent, konsument eller klient. Producer används vid sändning av meddelanden, konsument vid mottagning och klient vid kvittning.
- `component` – `servicebus`

Alla händelser har även egenskaperna "entity" och "slut punkt", de utelämnas i tabellen nedan
  * `string Entity` --Namn på entiteten (kö, ämne osv.)
  * `Uri Endpoint` -Service Bus slut punkts-URL

### <a name="instrumented-operations"></a>Instrumenterade åtgärder
Här är en fullständig lista över instrumenterade åtgärder:

| Åtgärds namn | Spårat API |
| -------------- | ----------- | 
| ServiceBusSender. send | ServiceBusSender.SendMessageAsync<br/>ServiceBusSender.SendMessagesAsync |
| ServiceBusSender. Schedule | ServiceBusSender.ScheduleMessageAsync<br/>ServiceBusSender.ScheduleMessagesAsync | 
| ServiceBusSender. Cancel | ServiceBusSender.CancelScheduledMessageAsync<br/>ServiceBusSender.CancelScheduledMessagesAsync |
| ServiceBusReceiver. Receive | ServiceBusReceiver.ReceiveMessageAsync<br/>ServiceBusReceiver.ReceiveMessagesAsync |
| ServiceBusReceiver.ReceiveDeferred | ServiceBusReceiver.ReceiveDeferredMessagesAsync |
| ServiceBusReceiver. Peek | ServiceBusReceiver.PeekMessageAsync<br/>ServiceBusReceiver.PeekMessagesAsync |
| ServiceBusReceiver. överge | ServiceBusReceiver.AbandonMessagesAsync |
| ServiceBusReceiver. Complete | ServiceBusReceiver.CompleteMessagesAsync |
| ServiceBusReceiver. obeställbara meddelanden kön | ServiceBusReceiver.DeadLetterMessagesAsync |
| ServiceBusReceiver. Överlåt |  ServiceBusReceiver.DeferMessagesAsync |
| ServiceBusReceiver.RenewMessageLock | ServiceBusReceiver.RenewMessageLockAsync |
| ServiceBusSessionReceiver.RenewSessionLock | ServiceBusSessionReceiver.RenewSessionLockAsync |
| ServiceBusSessionReceiver.GetSessionState | ServiceBusSessionReceiver.GetSessionStateAsync |
| ServiceBusSessionReceiver.SetSessionState | ServiceBusSessionReceiver.SetSessionStateAsync |
| ServiceBusProcessor.ProcessMessage | Återanrop för processor angavs på ServiceBusProcessor. ProcessMessageAsync-egenskap |
| ServiceBusSessionProcessor.ProcessSessionMessage | Återanrop för processor angavs på ServiceBusSessionProcessor. ProcessMessageAsync-egenskap |

### <a name="filtering-and-sampling"></a>Filtrering och sampling

I vissa fall är det önskvärt att endast logga en del av händelserna för att minska prestanda och lagrings förbrukning. Du kan endast logga "stoppa"-händelser (som i föregående exempel) eller en procentuell sampling av händelserna. 
`DiagnosticSource` Ange ett sätt att åstadkomma det med `IsEnabled` predikatet. Mer information finns i [Sammanhangs beroende filtrering i DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering).

`IsEnabled` kan anropas flera gånger för en enskild åtgärd för att minimera prestanda påverkan.

`IsEnabled` anropas i följande ordning:

1. `IsEnabled(<OperationName>, string entity, null)` till exempel `IsEnabled("ServiceBusSender.Send", "MyQueue1")` . Observera att det inte finns någon start eller stopp i slutet. Använd den för att filtrera bort specifika åtgärder eller köer. Om callback-metoden returnerar `false` skickas inte händelser för åtgärden.

   * För åtgärderna "process" och "ProcessSession" får du också `IsEnabled(<OperationName>, string entity, Activity activity)` motringning. Använd den för att filtrera händelser baserat på `activity.Id` eller Taggar egenskaper.
  
2. `IsEnabled(<OperationName>.Start)` till exempel `IsEnabled("ServiceBusSender.Send.Start")` . Kontrollerar om start händelsen ska utlösas. Resultatet påverkar endast händelsen "starta", men ytterligare Instrumentation är inte beroende av det.

Det finns ingen `IsEnabled` "stopp"-händelse.

Om åtgärds resultatet är ett undantag `IsEnabled("ServiceBusSender.Send.Exception")` kallas det. Du kan bara prenumerera på "undantags händelser" och förhindra resten av Instrumentation. I så fall måste du fortfarande hantera sådana undantag. Eftersom annan Instrumentation är inaktive rad bör du inte förvänta spårnings kontexten för att flöda med meddelanden från konsument till producent.

Du kan `IsEnabled` även implementera provtagnings strategier. Sampling som baseras på `Activity.Id` eller `Activity.RootId` säkerställer konsekvent provtagning över alla däck (så länge som det sprids av spårnings systemet eller av din egen kod).

I närvaro av flera `DiagnosticSource` lyssnare för samma källa räcker det bara för en lyssnare att acceptera händelsen, så det finns ingen garanti som `IsEnabled` kallas.



# <a name="microsoftazureservicebus-sdk"></a>[Microsoft. Azure. Service Bus SDK](#tab/net-standard-sdk)

| Egenskapens namn        | Description                                                 |
|----------------------|-------------------------------------------------------------|
|  Diagnostic-Id       | Unikt ID för ett externt anrop från producent till kön. Se [begärande-ID i HTTP-protokollet](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#request-id) för att få rationella, överväganden och format |
|  Correlation-Context | Åtgärds kontext, som sprids över alla tjänster som ingår i åtgärds bearbetning. Mer information finns i [korrelations kontext i HTTP-protokoll](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md#correlation-context) |

## <a name="service-bus-net-client-autotracing"></a>Service Bus .NET-klient spårning

Från och med version 3.0.0 [Microsoft Azure Service Bus-klienten för .net](/dotnet/api/microsoft.azure.servicebus.queueclient) får du spårnings instrument som kan kopplas till spårnings system, eller del av klient kod.
Med instrumentering kan du spåra alla anrop till tjänsten för Service Bus meddelande tjänsten från klient sidan. Om meddelande bearbetning görs med [meddelande hanterarens mönster](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler), instrumenteras även meddelande bearbetning

### <a name="tracking-with-azure-application-insights"></a>Spåra med Azure Application insikter

[Microsoft Application Insights](https://azure.microsoft.com/services/application-insights/) ger omfattande prestanda övervaknings funktioner, inklusive automagic-begäran och beroende spårning.

Installera Application Insights SDK, beroende på din projekt typ:
- [ASP.net](../azure-monitor/app/asp-net.md) -installation version 2,5-beta2 eller högre
- [ASP.net Core](../azure-monitor/app/asp-net-core.md) -installera version 2.2.0-beta2 eller högre.
Dessa länkar innehåller information om hur du installerar SDK, skapar resurser och konfigurerar SDK (om det behövs). Information om non-ASP.NET-program finns i artikeln [Azure Application Insights för konsol program](../azure-monitor/app/console.md) .

Om du använder [meddelande hanteraren](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) för att bearbeta meddelanden är du klar: alla Service Bus samtal som utförs av tjänsten spåras automatiskt och korreleras med andra telemetridata. I annat fall refererar vi till följande exempel för manuell meddelande bearbetnings spårning.

#### <a name="trace-message-processing"></a>Behandling av spåra meddelanden

```csharp
private readonly TelemetryClient telemetryClient;

async Task ProcessAsync(Message message)
{
    var activity = message.ExtractActivity();
    
    // If you're using Microsoft.ApplicationInsights package version 2.6-beta or higher, you should call StartOperation<RequestTelemetry>(activity) instead
    using (var operation = telemetryClient.StartOperation<RequestTelemetry>("Process", activity.RootId, activity.ParentId))
    {
        telemetryClient.TrackTrace("Received message");
        try 
        {
           // process message
        }
        catch (Exception ex)
        {
             telemetryClient.TrackException(ex);
             operation.Telemetry.Success = false;
             throw;
        }

        telemetryClient.TrackTrace("Done");
   }
}
```

I det här exemplet `RequestTelemetry` rapporteras för varje bearbetat meddelande, med en tidsstämpel, varaktighet och resultat (lyckades). Telemetri har också en uppsättning korrelations egenskaper.
Kapslade spår och undantag som rapporteras vid meddelande bearbetning stämplas också med korrelations egenskaper som representerar dem som underordnade till `RequestTelemetry` .

Om du gör anrop till stödda externa komponenter under meddelande bearbetningen spåras de också automatiskt och korreleras. Se [spåra anpassade åtgärder med Application Insights .NET SDK](../azure-monitor/app/custom-operations-tracking.md) för manuell spårning och korrelation.

Om du kör någon extern kod utöver Application Insights SDK, förväntar du dig att se längre **tid** när du visar Application Insights loggar. 

![Längre varaktighet i Application Insights loggen](./media/service-bus-end-to-end-tracing/longer-duration.png)

Det innebär inte att det tog lång tid att ta emot meddelandet. I det här scenariot har meddelandet redan mottagits sedan meddelandet skickades som en parameter till SDK-koden. Och **namn** -taggen i App Insights-loggarna (**processen**) anger att meddelandet nu bearbetas av den externa händelse bearbetnings koden. Det här problemet är inte Azure-relaterat. De här måtten avser i stället effektiviteten hos din externa kod, under förutsättning att meddelandet redan har tagits emot från Service Bus. Se [den här filen på GitHub](https://github.com/Azure/azure-sdk-for-net/blob/4bab05144ce647cc9e704d46d3763de5f9681ee0/sdk/servicebus/Microsoft.Azure.ServiceBus/src/ServiceBusDiagnosticsSource.cs) för att se var **process** tag gen genereras och tilldelas när meddelandet har tagits emot från Service Bus. 

### <a name="tracking-without-tracing-system"></a>Spåra utan spårnings system
Om spårnings systemet inte stöder automatisk Service Bus samtals spårning kan du behöva lägga till sådant stöd i ett spårnings system eller i ditt program. I det här avsnittet beskrivs diagnostiska händelser som skickas av Service Bus .NET-klienten.  

Service Bus .NET-klienten instrumenteras med hjälp av .NET tracing primitiver [system. Diagnostics. Activity](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) och [system. Diagnostics. DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md).

`Activity` fungerar som en spårnings kontext medan `DiagnosticSource` är en meddelande funktion. 

Om det inte finns någon lyssnare för DiagnosticSource-händelserna, är instrumentering inaktiverat, och behåller inga instrument kostnader. DiagnosticSource ger all kontroll till lyssnaren:
- lyssnare styr vilka källor och händelser som ska bevakas
- lyssnare kontrollerar händelse frekvens och sampling
- händelser skickas med en nytto last som ger fullständig kontext så att du kan komma åt och ändra meddelande objekt under händelsen

Bekanta dig med [användar handboken för DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md) innan du fortsätter med implementeringen.

Nu ska vi skapa en lyssnare för Service Bus händelser i ASP.NET Core app som skriver loggar med Microsoft. extension. Logga.
Den använder [system. Reactive. Core](https://www.nuget.org/packages/System.Reactive.Core) -biblioteket för att prenumerera på DiagnosticSource (det är också enkelt att prenumerera på DiagnosticSource utan IT)

```csharp
public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory factory, IApplicationLifetime applicationLifetime)
{
    // configuration...

    var serviceBusLogger = factory.CreateLogger("Microsoft.Azure.ServiceBus");

    IDisposable innerSubscription = null;
    IDisposable outerSubscription = DiagnosticListener.AllListeners.Subscribe(delegate (DiagnosticListener listener)
    {
        // subscribe to the Service Bus DiagnosticSource
        if (listener.Name == "Microsoft.Azure.ServiceBus")
        {
            // receive event from Service Bus DiagnosticSource
            innerSubscription = listener.Subscribe(delegate (KeyValuePair<string, object> evnt)
            {
                // Log operation details once it's done
                if (evnt.Key.EndsWith("Stop"))
                {
                    Activity currentActivity = Activity.Current;
                    TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");
                    serviceBusLogger.LogInformation($"Operation {currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}");
                }
            });
        }
    });

    applicationLifetime.ApplicationStopping.Register(() =>
    {
        outerSubscription?.Dispose();
        innerSubscription?.Dispose();
    });
}
```

I det här exemplet loggar Listener varaktighet, resultat, unik identifierare och start tid för varje Service Bus åtgärd.

#### <a name="events"></a>Händelser

För varje åtgärd skickas två händelser: "starta" och "stoppa". Förmodligen är du bara intresse rad av "stopp"-händelser. De ger resultatet av åtgärden och start tid och varaktighet som aktivitets egenskaper.

Händelse nytto Last tillhandahåller en lyssnare med åtgärdens kontext, den replikerar API-inkommande parametrar och retur värde. Händelse nytto lasten "stoppa" har alla egenskaper för "starta" händelse nytto Last, så att du kan ignorera "Start"-händelsen fullständigt.

Alla händelser har även egenskaperna "entity" och "slut punkt", de utelämnas i tabellen nedan
  * `string Entity` --Namn på entiteten (kö, ämne osv.)
  * `Uri Endpoint` -Service Bus slut punkts-URL

Varje "stopp"-händelse har en `Status` egenskap med en `TaskStatus` asynkron åtgärd slutfördes med, vilket också utelämnas i följande tabell för enkelhetens skull.

Här är en fullständig lista över instrumenterade åtgärder:

| Åtgärds namn | Spårat API | Egenskaper för vissa nytto Last|
|----------------|-------------|---------|
| Microsoft. Azure. Service Bus. send | [MessageSender. SendAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.sendasync) | `IList<Message> Messages` – Lista över meddelanden som skickas |
| Microsoft. Azure. Service Bus. ScheduleMessage | [MessageSender. ScheduleMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.schedulemessageasync) | `Message Message` – Meddelande som bearbetas<br/>`DateTimeOffset ScheduleEnqueueTimeUtc` -Schemalagd meddelande förskjutning<br/>`long SequenceNumber` -Ordnings nummer för schemalagt meddelande ("stoppa" händelse nytto Last) |
| Microsoft. Azure. Service Bus. Cancel | [MessageSender. CancelScheduledMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagesender.cancelscheduledmessageasync) | `long SequenceNumber` – Sekvensnummer för te-meddelande som ska avbrytas | 
| Microsoft. Azure. Service Bus. Receive | [MessageReceiver.ReceiveAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receiveasync) | `int RequestedMessageCount` -Det maximala antalet meddelanden som kan tas emot.<br/>`IList<Message> Messages` – Lista över mottagna meddelanden ("stoppa" händelse nytto Last) |
| Microsoft. Azure. Service Bus. Peek | [MessageReceiver.PeekAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.peekasync) | `int FromSequenceNumber` – Start punkten som du kan använda för att bläddra i en batch med meddelanden.<br/>`int RequestedMessageCount` – Antalet meddelanden som ska hämtas.<br/>`IList<Message> Messages` – Lista över mottagna meddelanden ("stoppa" händelse nytto Last) |
| Microsoft. Azure. Service Bus. ReceiveDeferred | [MessageReceiver.ReceiveDeferredMessageAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.receivedeferredmessageasync) | `IEnumerable<long> SequenceNumbers` – Den lista som innehåller de ordnings nummer som ska tas emot.<br/>`IList<Message> Messages` – Lista över mottagna meddelanden ("stoppa" händelse nytto Last) |
| Microsoft. Azure. Service Bus. Complete | [MessageReceiver.CompleteAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.completeasync) | `IList<string> LockTokens` – Den lista som innehåller de lock-token för motsvarande meddelanden som ska slutföras.|
| Microsoft. Azure. Service Bus. Abandon | [MessageReceiver.AbandonAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.abandonasync) | `string LockToken` – Lås-token för motsvarande meddelande att överge. |
| Microsoft. Azure. Service Bus. Överlåt | [MessageReceiver.DeferAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deferasync) | `string LockToken` – Lås-token för motsvarande meddelande som ska skjutas upp. | 
| Microsoft. Azure. Service Bus. obeställbara meddelanden kön | [MessageReceiver.DeadLetterAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.deadletterasync) | `string LockToken` – Lås-token för motsvarande meddelande till obeställbara meddelanden. | 
| Microsoft. Azure. Service Bus. RenewLock | [MessageReceiver.RenewLockAsync](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync) | `string LockToken` – Lås-token för motsvarande meddelande för att förnya låset.<br/>`DateTime LockedUntilUtc` – Nytt datum och tid för att låsa token upphör att gälla i UTC-format. ("Stoppa" händelse nytto Last)|
| Microsoft. Azure. Service Bus. process | Meddelande hanteraren lambda-funktion som finns i [IReceiverClient. RegisterMessageHandler](/dotnet/api/microsoft.azure.servicebus.core.ireceiverclient.registermessagehandler) | `Message Message` – Meddelande som bearbetas. |
| Microsoft. Azure. Service Bus. ProcessSession | Lambda-funktionen för Message sessions handler finns i [IQueueClient. RegisterSessionHandler](/dotnet/api/microsoft.azure.servicebus.iqueueclient.registersessionhandler) | `Message Message` – Meddelande som bearbetas.<br/>`IMessageSession Session` -Sessionen som bearbetas |
| Microsoft. Azure. Service Bus. AddRule | [SubscriptionClient. AddRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.addruleasync) | `RuleDescription Rule` – Regel beskrivningen som innehåller den regel som ska läggas till. |
| Microsoft. Azure. Service Bus. RemoveRule | [SubscriptionClient. RemoveRuleAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.removeruleasync) | `string RuleName` -Namn på regeln som ska tas bort. |
| Microsoft. Azure. Service Bus. GetRules | [SubscriptionClient. GetRulesAsync](/dotnet/api/microsoft.azure.servicebus.subscriptionclient.getrulesasync) | `IEnumerable<RuleDescription> Rules` – Alla regler som är associerade med prenumerationen. (Stoppa endast nytto Last) |
| Microsoft. Azure. Service Bus. AcceptMessageSession | [ISessionClient.AcceptMessageSessionAsync](/dotnet/api/microsoft.azure.servicebus.isessionclient.acceptmessagesessionasync) | `string SessionId` – SessionId finns i meddelandena. |
| Microsoft. Azure. Service Bus. GetSessionState | [IMessageSession.GetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.getstateasync) | `string SessionId` – SessionId finns i meddelandena.<br/>`byte [] State` – Sessionstillstånd ("stoppa" händelse nytto Last) |
| Microsoft. Azure. Service Bus. SetSessionState | [IMessageSession.SetStateAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.setstateasync) | `string SessionId` – SessionId finns i meddelandena.<br/>`byte [] State` – Sessionstillstånd |
| Microsoft. Azure. Service Bus. RenewSessionLock | [IMessageSession.RenewSessionLockAsync](/dotnet/api/microsoft.azure.servicebus.imessagesession.renewsessionlockasync) | `string SessionId` – SessionId finns i meddelandena. |
| Microsoft. Azure. Service Bus. Exception | alla instrumenterade API: er| `Exception Exception` – Undantags instans |

I varje händelse kan du komma åt `Activity.Current` som innehåller aktuell åtgärds kontext.

#### <a name="logging-more-properties"></a>Logga fler egenskaper

`Activity.Current` innehåller en detaljerad kontext för den aktuella åtgärden och dess överordnade. Mer information finns i [aktivitets dokumentation](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md).
Service Bus Instrumentation innehåller mer information i `Activity.Current.Tags` – de är kvar `MessageId` och `SessionId` när de är tillgängliga.

Aktiviteter som spårar "Receive", "Peek" och "ReceiveDeferred"-händelsen kan också ha `RelatedTo` tagg. Den innehåller en distinkt lista över `Diagnostic-Id` meddelanden som tagits emot som ett resultat.
Sådan åtgärd kan leda till att flera orelaterade meddelanden tas emot. Det `Diagnostic-Id` är inte heller känt när åtgärden startar, så att Receive-åtgärder kan korreleras till process åtgärder med endast den här taggen. Det är användbart när du analyserar prestanda problem för att kontrol lera hur lång tid det tog att ta emot meddelandet.

Effektivt sätt att logga taggar är att iterera över dem, så att lägga till taggar i föregående exempel ser ut så här 

```csharp
Activity currentActivity = Activity.Current;
TaskStatus status = (TaskStatus)evnt.Value.GetProperty("Status");

var tagsList = new StringBuilder();
foreach (var tags in currentActivity.Tags)
{
    tagsList.Append($", {tags.Key}={tags.Value}");
}

serviceBusLogger.LogInformation($"{currentActivity.OperationName} is finished, Duration={currentActivity.Duration}, Status={status}, Id={currentActivity.Id}, StartTime={currentActivity.StartTimeUtc}{tagsList}");
```

#### <a name="filtering-and-sampling"></a>Filtrering och sampling

I vissa fall är det önskvärt att endast logga en del av händelserna för att minska prestanda och lagrings förbrukning. Du kan endast logga "stoppa"-händelser (som i föregående exempel) eller en procentuell sampling av händelserna. 
`DiagnosticSource` Ange ett sätt att åstadkomma det med `IsEnabled` predikatet. Mer information finns i [Sammanhangs beroende filtrering i DiagnosticSource](https://github.com/dotnet/corefx/blob/master/src/System.Diagnostics.DiagnosticSource/src/DiagnosticSourceUsersGuide.md#context-based-filtering).

`IsEnabled` kan anropas flera gånger för en enskild åtgärd för att minimera prestanda påverkan.

`IsEnabled` anropas i följande ordning:

1. `IsEnabled(<OperationName>, string entity, null)` till exempel `IsEnabled("Microsoft.Azure.ServiceBus.Send", "MyQueue1")` . Observera att det inte finns någon start eller stopp i slutet. Använd den för att filtrera bort specifika åtgärder eller köer. Om callback-metoden returnerar `false` skickas inte händelser för åtgärden

   * För åtgärderna "process" och "ProcessSession" får du också `IsEnabled(<OperationName>, string entity, Activity activity)` motringning. Använd den för att filtrera händelser baserat på `activity.Id` eller Taggar egenskaper.
  
2. `IsEnabled(<OperationName>.Start)` till exempel `IsEnabled("Microsoft.Azure.ServiceBus.Send.Start")` . Kontrollerar om start händelsen ska utlösas. Resultatet påverkar endast händelsen "starta", men ytterligare Instrumentation är inte beroende av det.

Det finns ingen `IsEnabled` "stopp"-händelse.

Om åtgärds resultatet är ett undantag `IsEnabled("Microsoft.Azure.ServiceBus.Exception")` kallas det. Du kan bara prenumerera på "undantags händelser" och förhindra resten av Instrumentation. I så fall måste du fortfarande hantera sådana undantag. Eftersom annan Instrumentation är inaktive rad bör du inte förvänta spårnings kontexten för att flöda med meddelanden från konsument till producent.

Du kan `IsEnabled` även implementera provtagnings strategier. Sampling som baseras på `Activity.Id` eller `Activity.RootId` säkerställer konsekvent provtagning över alla däck (så länge som det sprids av spårnings systemet eller av din egen kod).

I närvaro av flera `DiagnosticSource` lyssnare för samma källa räcker det bara för en lyssnare att acceptera händelsen, så det finns ingen garanti som `IsEnabled` kallas.

---

## <a name="next-steps"></a>Nästa steg

* [Application Insights korrelation](../azure-monitor/app/correlation.md)
* [Application Insights övervakar beroenden](../azure-monitor/app/asp-net-dependencies.md) för att se om rest, SQL eller andra externa resurser saktar ned dig.
* [Spåra anpassade åtgärder med Application Insights .NET SDK](../azure-monitor/app/custom-operations-tracking.md)

