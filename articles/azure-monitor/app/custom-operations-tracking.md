---
title: Spåra anpassade åtgärder med Azure Application Insights .NET SDK
description: Spåra anpassade åtgärder med Azure Application Insights .NET SDK
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 11/26/2019
ms.reviewer: sergkanz
ms.openlocfilehash: 42a5318325f9961483465357403089755feb130d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "88933315"
---
# <a name="track-custom-operations-with-application-insights-net-sdk"></a>Spåra anpassade åtgärder med Application Insights .NET SDK

Azure Application Insights SDK: er automatiskt spårar inkommande HTTP-begäranden och anrop till beroende tjänster, till exempel HTTP-begäranden och SQL-frågor. Spårning och korrelation av förfrågningar och beroenden ger dig insyn i hela programmets svars tid och tillförlitlighet i alla mikrotjänster som kombinerar det här programmet. 

Det finns en klass med program mönster som inte stöds allmänt. En korrekt övervakning av sådana mönster kräver manuell kod instrumentering. Den här artikeln beskriver några mönster som kan kräva manuell instrumentering, till exempel bearbetning av anpassade köer och körning av tids krävande bakgrunds aktiviteter.

Det här dokumentet ger vägledning om hur du spårar anpassade åtgärder med Application Insights SDK. Den här dokumentationen är relevant för:

- Application Insights för .NET (även kallat Base SDK) version 2.4 +.
- Application Insights för webb program (som kör ASP.NET) version 2.4 +.
- Application Insights för ASP.NET Core version 2.1 +.

## <a name="overview"></a>Översikt
En åtgärd är en logisk typ av arbete som körs av ett program. Det har ett namn, start tid, varaktighet, resultat och en kontext för körning som användar namn, egenskaper och resultat. Om åtgärd A initierades av åtgärd B, anges operation B som överordnad för en. En åtgärd kan bara ha en överordnad, men den kan ha många underordnade åtgärder. Mer information om åtgärder och telemetri finns i [Azure Application Insights-korrelation](correlation.md).

I Application Insights .NET SDK beskrivs åtgärden av abstrakt klass [OperationTelemetry](https://github.com/microsoft/ApplicationInsights-dotnet/blob/7633ae849edc826a8547745b6bf9f3174715d4bd/BASE/src/Microsoft.ApplicationInsights/Extensibility/Implementation/OperationTelemetry.cs) och dess underordnade [RequestTelemetry](https://github.com/microsoft/ApplicationInsights-dotnet/blob/7633ae849edc826a8547745b6bf9f3174715d4bd/BASE/src/Microsoft.ApplicationInsights/DataContracts/RequestTelemetry.cs) och [DependencyTelemetry](https://github.com/microsoft/ApplicationInsights-dotnet/blob/7633ae849edc826a8547745b6bf9f3174715d4bd/BASE/src/Microsoft.ApplicationInsights/DataContracts/DependencyTelemetry.cs).

## <a name="incoming-operations-tracking"></a>Inkommande åtgärds spårning 
Application Insights Web SDK samlar automatiskt in HTTP-begäranden för ASP.NET-program som körs i en IIS-pipeline och alla ASP.NET Core-program. Det finns community-lösningar som stöds för andra plattformar och ramverk. Men om programmet inte stöds av någon av lösningarna standard eller community-support kan du instrumentera det manuellt.

Ett annat exempel som kräver anpassad spårning är den arbets tagare som tar emot objekt från kön. För vissa köer spåras anropet till att lägga till ett meddelande i den här kön som ett beroende. Den övergripande åtgärden som beskriver meddelande bearbetningen samlas dock inte in automatiskt.

Nu ska vi se hur sådana åtgärder kan spåras.

På en hög nivå är uppgiften att skapa `RequestTelemetry` och ange kända egenskaper. När åtgärden har slutförts spårar du Telemetrin. Följande exempel visar den här uppgiften.

### <a name="http-request-in-owin-self-hosted-app"></a>HTTP-begäran i OWIN app med egen värd
I det här exemplet sprids spårnings kontexten enligt [http-protokollet för korrelation](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md). Du bör förvänta dig att ta emot rubriker som beskrivs där.

```csharp
public class ApplicationInsightsMiddleware : OwinMiddleware
{
    // you may create a new TelemetryConfiguration instance, reuse one you already have
    // or fetch the instance created by Application Insights SDK.
    private readonly TelemetryConfiguration telemetryConfiguration = TelemetryConfiguration.CreateDefault();
    private readonly TelemetryClient telemetryClient = new TelemetryClient(telemetryConfiguration);
    
    public ApplicationInsightsMiddleware(OwinMiddleware next) : base(next) {}

    public override async Task Invoke(IOwinContext context)
    {
        // Let's create and start RequestTelemetry.
        var requestTelemetry = new RequestTelemetry
        {
            Name = $"{context.Request.Method} {context.Request.Uri.GetLeftPart(UriPartial.Path)}"
        };

        // If there is a Request-Id received from the upstream service, set the telemetry context accordingly.
        if (context.Request.Headers.ContainsKey("Request-Id"))
        {
            var requestId = context.Request.Headers.Get("Request-Id");
            // Get the operation ID from the Request-Id (if you follow the HTTP Protocol for Correlation).
            requestTelemetry.Context.Operation.Id = GetOperationId(requestId);
            requestTelemetry.Context.Operation.ParentId = requestId;
        }

        // StartOperation is a helper method that allows correlation of 
        // current operations with nested operations/telemetry
        // and initializes start time and duration on telemetry items.
        var operation = telemetryClient.StartOperation(requestTelemetry);

        // Process the request.
        try
        {
            await Next.Invoke(context);
        }
        catch (Exception e)
        {
            requestTelemetry.Success = false;
            telemetryClient.TrackException(e);
            throw;
        }
        finally
        {
            // Update status code and success as appropriate.
            if (context.Response != null)
            {
                requestTelemetry.ResponseCode = context.Response.StatusCode.ToString();
                requestTelemetry.Success = context.Response.StatusCode >= 200 && context.Response.StatusCode <= 299;
            }
            else
            {
                requestTelemetry.Success = false;
            }

            // Now it's time to stop the operation (and track telemetry).
            telemetryClient.StopOperation(operation);
        }
    }
    
    public static string GetOperationId(string id)
    {
        // Returns the root ID from the '|' to the first '.' if any.
        int rootEnd = id.IndexOf('.');
        if (rootEnd < 0)
            rootEnd = id.Length;

        int rootStart = id[0] == '|' ? 1 : 0;
        return id.Substring(rootStart, rootEnd - rootStart);
    }
}
```

HTTP-protokollet för korrelation deklarerar också `Correlation-Context` rubriken. Detta är dock utelämnat här för enkelhetens skull.

## <a name="queue-instrumentation"></a>Queue Instruments
Även om det finns [W3C-spårnings kontext](https://www.w3.org/TR/trace-context/) och [http-protokoll för korrelation](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/HttpCorrelationProtocol.md) för att skicka KORRELATIONS information med http-begäran, måste varje Queue-protokoll definiera hur samma information skickas ihop i Queue-meddelandet. Vissa Queue-protokoll (t. ex. AMQP) tillåter att ytterligare metadata skickas och andra (Azure Storage kö) kräver att kontexten kodas i meddelande nytto lasten.

> [!NOTE]
> * **Spårning mellan komponenter stöds inte för köer än** Med HTTP, om din producent och konsument skickar telemetri till olika Application Insightss resurser, visar transaktions diagnos och program karta transaktioner och mappar från slut punkt till slut punkt. Om köer inte stöds ännu. 

### <a name="service-bus-queue"></a>Service Bus-kö
Application Insights spårar Service Bus meddelande anrop med den nya [Microsoft Azure Service Bus-klienten för .net](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus/) version 3.0.0 och högre.
Om du använder [meddelande hanteraren](/dotnet/api/microsoft.azure.servicebus.queueclient.registermessagehandler) för att bearbeta meddelanden gör du följande: alla Service Bus samtal som utförs av tjänsten spåras automatiskt och korreleras med andra telemetridata. Se [Service Bus klient spårning med Microsoft Application Insights](../../service-bus-messaging/service-bus-end-to-end-tracing.md) om du bearbetar meddelanden manuellt.

Om du använder [windowsazure. Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) -paketet kan du läsa mer i följande exempel visar hur du spårar (och korrelerar) anrop till Service Bus som Service Bus kö använder AMQP-protokoll och Application Insights inte automatiskt spårar köa åtgärder.
Korrelations identifierare skickas i meddelande egenskaperna.

#### <a name="enqueue"></a>Placera

```csharp
public async Task Enqueue(string payload)
{
    // StartOperation is a helper method that initializes the telemetry item
    // and allows correlation of this operation with its parent and children.
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queueName);
    
    operation.Telemetry.Type = "Azure Service Bus";
    operation.Telemetry.Data = "Enqueue " + queueName;

    var message = new BrokeredMessage(payload);
    // Service Bus queue allows the property bag to pass along with the message.
    // We will use them to pass our correlation identifiers (and other context)
    // to the consumer.
    message.Properties.Add("ParentId", operation.Telemetry.Id);
    message.Properties.Add("RootId", operation.Telemetry.Context.Operation.Id);

    try
    {
        await queue.SendAsync(message);
        
        // Set operation.Telemetry Success and ResponseCode here.
        operation.Telemetry.Success = true;
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // Set operation.Telemetry Success and ResponseCode here.
        operation.Telemetry.Success = false;
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

#### <a name="process"></a>Process
```csharp
public async Task Process(BrokeredMessage message)
{
    // After the message is taken from the queue, create RequestTelemetry to track its processing.
    // It might also make sense to get the name from the message.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "process " + queueName };

    var rootId = message.Properties["RootId"].ToString();
    var parentId = message.Properties["ParentId"].ToString();
    // Get the operation ID from the Request-Id (if you follow the HTTP Protocol for Correlation).
    requestTelemetry.Context.Operation.Id = rootId;
    requestTelemetry.Context.Operation.ParentId = parentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}
```

### <a name="azure-storage-queue"></a>Azure Storage kö
I följande exempel visas hur du spårar [Azure Storage Queue](../../storage/queues/storage-dotnet-how-to-use-queues.md) -åtgärder och korrelerar telemetri mellan tillverkare, konsument och Azure Storage. 

Lagrings kön har ett HTTP-API. Alla anrop till kön spåras av den Application Insights beroende insamlaren för HTTP-begäranden.
Den konfigureras som standard på ASP.NET och ASP.NET Core program, med andra typer av program, kan du läsa [dokumentationen om konsol program](./console.md)

Du kanske också vill korrelera Application Insights åtgärds-ID med ID för begäran. Information om hur du ställer in och skaffar en begäran om lagrings begär ande klient och ett ID för serverbegäran finns i [övervaka, diagnostisera och felsöka Azure Storage](../../storage/common/storage-monitoring-diagnosing-troubleshooting.md#end-to-end-tracing).

#### <a name="enqueue"></a>Placera
Eftersom lagrings köer stöder HTTP API spåras alla åtgärder med kön automatiskt av Application Insights. I många fall bör denna Instrumentation räcka. Men om du vill korrelera spår på konsument sidan med producent spår måste du skicka vissa korrelations sammanhang på samma sätt som i HTTP-protokollet för korrelation. 

Det här exemplet visar hur du spårar `Enqueue` åtgärden. Du kan:

 - **Korrelera nya försök (om det finns några)**: alla har en gemensam överordnad `Enqueue` åtgärd. Annars spåras de som underordnade till den inkommande begäran. Om det finns flera logiska begär anden till kön kan det vara svårt att hitta vilket anrop som resulterade i återförsök.
 - **Korrelera lagrings loggar (om och när det behövs)**: de är korrelerade med Application Insights telemetri.

`Enqueue`Åtgärden är underordnad en överordnad åtgärd (till exempel en inkommande HTTP-begäran). HTTP-beroende anropet är underordnat `Enqueue` åtgärden och grandchild för den inkommande begäran:

```csharp
public async Task Enqueue(CloudQueue queue, string message)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("enqueue " + queue.Name);
    operation.Telemetry.Type = "Azure queue";
    operation.Telemetry.Data = "Enqueue " + queue.Name;

    // MessagePayload represents your custom message and also serializes correlation identifiers into payload.
    // For example, if you choose to pass payload serialized to JSON, it might look like
    // {'RootId' : 'some-id', 'ParentId' : '|some-id.1.2.3.', 'message' : 'your message to process'}
    var jsonPayload = JsonConvert.SerializeObject(new MessagePayload
    {
        RootId = operation.Telemetry.Context.Operation.Id,
        ParentId = operation.Telemetry.Id,
        Payload = message
    });
    
    CloudQueueMessage queueMessage = new CloudQueueMessage(jsonPayload);

    // Add operation.Telemetry.Id to the OperationContext to correlate Storage logs and Application Insights telemetry.
    OperationContext context = new OperationContext { ClientRequestID = operation.Telemetry.Id};

    try
    {
        await queue.AddMessageAsync(queueMessage, null, null, new QueueRequestOptions(), context);
    }
    catch (StorageException e)
    {
        operation.Telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        operation.Telemetry.Success = false;
        operation.Telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}  
```

Om du vill minska mängden telemetri för dina program rapporter eller om du inte vill spåra `Enqueue` åtgärden av andra orsaker använder du `Activity` API: et direkt:

- Skapa (och starta) en ny `Activity` i stället för att starta Application Insights-åtgärden. Du behöver *inte* tilldela några egenskaper förutom åtgärds namnet.
- Serialisera `yourActivity.Id` i meddelande nytto lasten i stället för `operation.Telemetry.Id` . Du kan också använda `Activity.Current.Id` .


#### <a name="dequeue"></a>Ta bort
På samma sätt som `Enqueue` en faktisk HTTP-begäran till lagrings kön spåras automatiskt med Application Insights. `Enqueue`Åtgärden sker dock i det överordnade sammanhanget, till exempel en inkommande kontext för begäran. Application Insights SDK: er automatiskt korrelera en sådan åtgärd (och dess HTTP-del) med den överordnade begäran och annan telemetri som rapporteras i samma omfång.

`Dequeue`Åtgärden är knepig. Application Insights SDK spårar automatiskt HTTP-begäranden. Den känner dock inte till korrelations kontexten förrän meddelandet har tolkats. Det går inte att korrelera HTTP-begäran för att hämta meddelandet med resten av Telemetrin, särskilt när fler än ett meddelande tas emot.

```csharp
public async Task<MessagePayload> Dequeue(CloudQueue queue)
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>("dequeue " + queue.Name);
    operation.Telemetry.Type = "Azure queue";
    operation.Telemetry.Data = "Dequeue " + queue.Name;
    
    try
    {
        var message = await queue.GetMessageAsync();
    }
    catch (StorageException e)
    {
        operation.telemetry.Properties.Add("AzureServiceRequestID", e.RequestInformation.ServiceRequestID);
        operation.telemetry.Success = false;
        operation.telemetry.ResultCode = e.RequestInformation.HttpStatusCode.ToString();
        telemetryClient.TrackException(e);
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }

    return null;
}
```

#### <a name="process"></a>Process

I följande exempel spåras ett inkommande meddelande på ett sätt som liknar inkommande HTTP-begäran:

```csharp
public async Task Process(MessagePayload message)
{
    // After the message is dequeued from the queue, create RequestTelemetry to track its processing.
    RequestTelemetry requestTelemetry = new RequestTelemetry { Name = "process " + queueName };
    
    // It might also make sense to get the name from the message.
    requestTelemetry.Context.Operation.Id = message.RootId;
    requestTelemetry.Context.Operation.ParentId = message.ParentId;

    var operation = telemetryClient.StartOperation(requestTelemetry);

    try
    {
        await ProcessMessage();
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        throw;
    }
    finally
    {
        // Update status code and success as appropriate.
        telemetryClient.StopOperation(operation);
    }
}
```

På samma sätt kan andra köa åtgärder instrumenteras. En gransknings åtgärd bör instrumenteras på samma sätt som en åtgärd för att köa. Hanterings åtgärder för instrumenterade köer är inte nödvändiga. Application Insights spårar åtgärder som HTTP, och i de flesta fall är det tillräckligt.

Se till att du ställer in operation (korrelation)-identifierarna när du tar bort ett meddelande. Alternativt kan du använda API: `Activity` et. Sedan behöver du inte ange åtgärds identifierare för telemetri-objekten eftersom Application Insights SDK gör det åt dig:

- Skapa en ny `Activity` när du har ett objekt från kön.
- Används `Activity.SetParentId(message.ParentId)` för att korrelera konsument-och producent loggar.
- Starta `Activity` .
- Spåra åtgärder i kö-, process-och borttagnings åtgärder med `Start/StopOperation` hjälp av hjälp program. Gör det från samma asynkrona kontroll flöde (körnings kontext). På så sätt är de korrelerade korrekt.
- Stoppa `Activity` .
- Använd `Start/StopOperation` eller anropa `Track` telemetri manuellt.

### <a name="dependency-types"></a>Beroende typer

Application Insights använder beroende typen för att anpassa GRÄNSSNITTs upplevelser. För köer identifieras följande typer av `DependencyTelemetry` som förbättrar [upplevelsen för transaktions diagnostik](./transaction-diagnostics.md):
- `Azure queue` för Azure Storage köer
- `Azure Event Hubs` för Azure Event Hubs
- `Azure Service Bus` för Azure Service Bus

### <a name="batch-processing"></a>Batchbearbetning
Med vissa köer kan du ta bort flera meddelanden med en begäran i kö. Att bearbeta sådana meddelanden är förmodligen oberoende och tillhör olika logiska åtgärder. Det går inte att korrelera `Dequeue` åtgärden till ett visst meddelande som bearbetas.

Varje meddelande ska bearbetas i ett eget asynkront kontroll flöde. Mer information finns i avsnittet om [spårning av utgående beroenden](#outgoing-dependencies-tracking) .

## <a name="long-running-background-tasks"></a>Tids krävande bakgrunds aktiviteter

Vissa program startar långvariga åtgärder som kan ha orsakats av användar förfrågningar. Från spårnings-/instrument planen skiljer sig det inte från begär ande eller beroende Instrumentation: 

```csharp
async Task BackgroundTask()
{
    var operation = telemetryClient.StartOperation<DependencyTelemetry>(taskName);
    operation.Telemetry.Type = "Background";
    try
    {
        int progress = 0;
        while (progress < 100)
        {
            // Process the task.
            telemetryClient.TrackTrace($"done {progress++}%");
        }
        // Update status code and success as appropriate.
    }
    catch (Exception e)
    {
        telemetryClient.TrackException(e);
        // Update status code and success as appropriate.
        throw;
    }
    finally
    {
        telemetryClient.StopOperation(operation);
    }
}
```

I det här exemplet `telemetryClient.StartOperation` skapar `DependencyTelemetry` och fyller korrelations kontexten. Anta att du har en överordnad åtgärd som har skapats av inkommande begär Anden som schemalagt åtgärden. Så länge som `BackgroundTask` startar i samma asynkrona kontroll flöde som en inkommande begäran korreleras det med den överordnade åtgärden. `BackgroundTask` och alla kapslade telemetri-objekt korreleras automatiskt med den begäran som orsakade det, även efter att begäran har slutförts.

När aktiviteten startar från den bakgrunds tråd som inte har någon åtgärd ( `Activity` ) kopplad till den, `BackgroundTask` har inte någon överordnad. Det kan dock ha kapslade åtgärder. Alla telemetridata som har rapporter ATS från uppgiften korreleras till den `DependencyTelemetry` skapade i `BackgroundTask` .

## <a name="outgoing-dependencies-tracking"></a>Spårning av utgående beroenden
Du kan spåra din egen beroende typ eller en åtgärd som inte stöds av Application Insights.

`Enqueue`Metoden i Service Bus kön eller lagrings kön kan fungera som exempel för sådan anpassad spårning.

Den allmänna metoden för anpassad beroende spårning är att:

- Anropa `TelemetryClient.StartOperation` metoden (Extension) som fyller de `DependencyTelemetry` egenskaper som behövs för korrelationen och andra egenskaper (start tid, varaktighet).
- Ange andra anpassade egenskaper för `DependencyTelemetry` , till exempel namn och annan kontext som du behöver.
- Gör ett beroende anrop och vänta på det.
- Stoppa åtgärden med `StopOperation` när den är slutförd.
- Hantera undantag.

```csharp
public async Task RunMyTaskAsync()
{
    using (var operation = telemetryClient.StartOperation<DependencyTelemetry>("task 1"))
    {
        try 
        {
            var myTask = await StartMyTaskAsync();
            // Update status code and success as appropriate.
        }
        catch(...) 
        {
            // Update status code and success as appropriate.
        }
    }
}
```

Åtgärden stoppas om åtgärden stoppas, så du kan göra det i stället för att anropa `StopOperation` .

*Varning*: ett undantag som inte är behållet kan [förhindras](/dotnet/csharp/language-reference/keywords/try-finally) att `finally` anropas så att åtgärder inte kan spåras.

### <a name="parallel-operations-processing-and-tracking"></a>Bearbetning och spårning av parallella åtgärder

`StopOperation` stoppar bara den åtgärd som startades. Om den aktuella aktiva åtgärden inte matchar den som du vill stoppa, `StopOperation` gör ingenting. Den här situationen kan inträffa om du startar flera åtgärder parallellt i samma körnings kontext:

```csharp
var firstOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 1");
var firstTask = RunMyTaskAsync();

var secondOperation = telemetryClient.StartOperation<DependencyTelemetry>("task 2");
var secondTask = RunMyTaskAsync();

await firstTask;

// FAILURE!!! This will do nothing and will not report telemetry for the first operation
// as currently secondOperation is active.
telemetryClient.StopOperation(firstOperation); 

await secondTask;
```

Se till att du alltid anropar `StartOperation` och bearbetar åtgärder i samma **asynkrona** metod för att isolera åtgärder som körs parallellt. Om åtgärden är synkron (eller inte asynkron), Radbryt processen och spåra med `Task.Run` :

```csharp
public void RunMyTask(string name)
{
    using (var operation = telemetryClient.StartOperation<DependencyTelemetry>(name))
    {
        Process();
        // Update status code and success as appropriate.
    }
}

public async Task RunAllTasks()
{
    var task1 = Task.Run(() => RunMyTask("task 1"));
    var task2 = Task.Run(() => RunMyTask("task 2"));
    
    await Task.WhenAll(task1, task2);
}
```

## <a name="applicationinsights-operations-vs-systemdiagnosticsactivity"></a>ApplicationInsights-åtgärder vs system. Diagnostics. Activity
`System.Diagnostics.Activity` visar den distribuerade spårnings kontexten och används av ramverk och bibliotek för att skapa och sprida kontext inuti och utanför processen och korrelera telemetri-objekt. Aktiviteten fungerar tillsammans med `System.Diagnostics.DiagnosticSource` -meddelande mekanismen mellan ramverket/biblioteket för att meddela om intressanta händelser (inkommande eller utgående begär Anden, undantag osv.).

Aktiviteter är de första klasserna i Application Insights och automatiskt beroende och begär ande samling är mycket beroende av dem tillsammans med `DiagnosticSource` händelser. Om du skapar en aktivitet i ditt program innebär det inte att Application Insights telemetri skapas. Application Insights måste ta emot DiagnosticSource-händelser och känna till händelse namn och nytto laster för att översätta aktivitet till telemetri.

Varje Application Insights-åtgärd (Request eller Dependency) omfattar `Activity` -när `StartOperation` anropas skapas aktiviteten under. `StartOperation` är det rekommenderade sättet att spåra begäran eller beroende telemetrivärden manuellt och se till att allt korreleras.

## <a name="next-steps"></a>Nästa steg

- Lär dig grunderna för [telemetri-korrelation](correlation.md) i Application Insights.
- Ta reda på hur korrelerade data potenser för [transaktions diagnostik](./transaction-diagnostics.md) och [program översikt](./app-map.md).
- Se [data modellen](./data-model.md) för Application Insights typer och data modeller.
- Rapportera anpassade [händelser och mått](./api-custom-events-metrics.md) till Application Insights.
- Ta en titt på standard [konfigurationen](configuration-with-applicationinsights-config.md#telemetry-initializers-aspnet) för samling med kontext egenskaper.
- Kontrol lera [användar handboken för system. Diagnostics. Activity](https://github.com/dotnet/runtime/blob/master/src/libraries/System.Diagnostics.DiagnosticSource/src/ActivityUserGuide.md) för att se hur vi korrelerar telemetri.

