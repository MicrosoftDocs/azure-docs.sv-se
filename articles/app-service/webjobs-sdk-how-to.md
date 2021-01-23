---
title: Använda WebJobs SDK
description: Läs mer om hur du skriver kod för WebJobs SDK. Skapa händelse drivna bakgrunds bearbetnings jobb som har åtkomst till data i Azure och tjänster från tredje part.
author: ggailey777
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.date: 02/18/2019
ms.author: glenga
ms.openlocfilehash: 5666a489cfd0f0d92120c9ec7e3c8d3c2d083b5e
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98737277"
---
# <a name="how-to-use-the-azure-webjobs-sdk-for-event-driven-background-processing"></a>Så använder du Azure WebJobs SDK för händelsedriven bakgrundsbearbetning

Den här artikeln innehåller rikt linjer för hur du arbetar med Azure WebJobs SDK. Om du vill komma igång med WebJobs direkt läser du [Kom igång med Azure WEBJOBS SDK för händelse driven bakgrunds bearbetning](webjobs-sdk-get-started.md). 

## <a name="webjobs-sdk-versions"></a>WebJobs SDK-versioner

Detta är de viktigaste skillnaderna mellan version 3. *x* och version 2. *x* av WebJobs-SDK:

* Version 3. *x* lägger till stöd för .net Core.
* I version 3. *x* måste du uttryckligen installera lagrings bindnings tillägget som krävs av WebJobs SDK. I version 2. *x* inkluderades lagrings bindningarna i SDK.
* Visual Studio-verktyg för .NET Core (3).*x*)-projekt skiljer sig från verktyg för .NET Framework (2.*x*)-projekt. Mer information finns i [utveckla och distribuera WebJobs med Visual Studio-Azure App Service](webjobs-dotnet-deploy-vs.md).

När det är möjligt finns exempel exempel för både version 3. *x* och version 2. *x*.

> [!NOTE]
> [Azure Functions](../azure-functions/functions-overview.md) bygger på WebJobs SDK och den här artikeln innehåller länkar till Azure Functions dokumentation för vissa ämnen. Observera skillnaderna mellan Functions och WebJobs SDK:
> * Azure Functions version 2. *x* motsvarar WebJobs SDK version 3. *x* och Azure Functions 1. *x* motsvarar WebJobs SDK 2. *x*. Käll kods databaser använder WebJobs SDK-numrering.
> * Exempel kod för Azure Functions C#-klass bibliotek är som WebJobs SDK-kod, förutom att du inte behöver ett `FunctionName` attribut i ett WebJobs SDK-projekt.
> * Vissa bindnings typer stöds bara i functions, t. ex. HTTP (Webhooks) och Event Grid (som baseras på HTTP).
>
> Mer information finns i [jämföra WebJobs SDK och Azure Functions](../azure-functions/functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

## <a name="webjobs-host"></a>WebJobs-värd

Värden är en runtime-behållare för functions.  Den lyssnar efter utlösare och anropar funktioner. I version 3. *x* är värden en implementering av `IHost` . I version 2. *x*, använder du `JobHost` objektet. Du skapar en värd instans i koden och skriver kod för att anpassa dess beteende.

Detta är en viktig skillnad mellan att använda WebJobs SDK direkt och använda den indirekt via Azure Functions. I Azure Functions kontrollerar tjänsten värden och du kan inte anpassa värden genom att skriva kod. Med Azure Functions kan du anpassa värd beteendet genom inställningarna i host.jspå filen. Dessa inställningar är strängar, inte kod, och detta begränsar de typer av anpassningar som du kan göra.

### <a name="host-connection-strings"></a>Värd anslutnings strängar

WebJobs-SDK: n söker efter Azure Storage och Azure Service Bus anslutnings strängar i local.settings.jsi filen när du kör lokalt, eller i webb jobbets miljö när du kör i Azure. Som standard krävs en lagrings anslutnings sträng inställning som heter `AzureWebJobsStorage` .  

Version 2. *x* i SDK kan du använda dina egna namn för dessa anslutnings strängar eller lagra dem på en annan plats. Du kan ange namn i kod med hjälp av [`JobHostConfiguration`] , som du ser här:

```cs
static void Main(string[] args)
{
    var _storageConn = ConfigurationManager
        .ConnectionStrings["MyStorageConnection"].ConnectionString;

    //// Dashboard logging is deprecated; use Application Insights.
    //var _dashboardConn = ConfigurationManager
    //    .ConnectionStrings["MyDashboardConnection"].ConnectionString;

    JobHostConfiguration config = new JobHostConfiguration();
    config.StorageConnectionString = _storageConn;
    //config.DashboardConnectionString = _dashboardConn;
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

> [!NOTE]
> Eftersom version 3. *x* använder standard Konfigurations-API: er för .net Core. det finns inget API för att ändra namn på anslutnings strängar. Se [utveckla och distribuera WebJobs med Visual Studio](webjobs-dotnet-deploy-vs.md)

### <a name="host-development-settings"></a>Inställningar för värd utveckling

Du kan köra värden i utvecklings läge för att göra den lokala utvecklingen mer effektiv. Här följer några av de inställningar som ändras när du kör i utvecklings läge:

| Egenskap | Utvecklings inställning |
| ------------- | ------------- |
| `Tracing.ConsoleLevel` | `TraceLevel.Verbose` för att maximera logg resultatet. |
| `Queues.MaxPollingInterval`  | Ett lågt värde för att se till att köa metoder utlöses omedelbart.  |
| `Singleton.ListenerLockPeriod` | 15 sekunder för att hjälpa till med snabb iterativ utveckling. |

Processen för att aktivera utvecklings läget beror på SDK-versionen. 

#### <a name="version-3x"></a>Version 3. *x*

Version 3. *x* använder standard-ASP.net Core-API: er. Anropa [`UseEnvironment`](/dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.useenvironment) metoden på [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) instansen. Skicka en sträng med namnet `development` , som i det här exemplet:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.UseEnvironment("development");
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Version 2. *x*

`JobHostConfiguration`Klassen har en `UseDevelopmentSettings` metod som aktiverar utvecklings läge.  I följande exempel visas hur du använder utvecklings inställningar. Om du vill göra `config.IsDevelopment` `true` en återgång när den körs lokalt anger du en lokal miljö variabel med namnet `AzureWebJobsEnv` med värdet `Development` .

```cs
static void Main()
{
    config = new JobHostConfiguration();

    if (config.IsDevelopment)
    {
        config.UseDevelopmentSettings();
    }

    var host = new JobHost(config);
    host.RunAndBlock();
}
```

### <a name="managing-concurrent-connections-version-2x"></a><a name="jobhost-servicepointmanager-settings"></a>Hantera samtidiga anslutningar (version 2.*x*)

I version 3. *x*, anslutnings gränsen som standard är oändlig anslutning. Om du av någon anledning behöver ändra den här gränsen kan du använda [`MaxConnectionsPerServer`](/dotnet/api/system.net.http.winhttphandler.maxconnectionsperserver) egenskapen för [`WinHttpHandler`](/dotnet/api/system.net.http.winhttphandler) klassen.

I version 2. *x* styr du antalet samtidiga anslutningar till en värd med hjälp av [ServicePointManager. DefaultConnectionLimit](/dotnet/api/system.net.servicepointmanager.defaultconnectionlimit#System_Net_ServicePointManager_DefaultConnectionLimit) -API: et. I 2. *x* ska du öka värdet från standardvärdet 2 innan du startar dina WebJobs-värden.

Alla utgående HTTP-begäranden som du gör från en funktion med hjälp av `HttpClient` Flow `ServicePointManager` . När du har nått värdet i `DefaultConnectionLimit` börjar du `ServicePointManager` köa förfrågningar innan du skickar dem. Anta `DefaultConnectionLimit` att du har angett till 2 och att din kod gör 1 000 HTTP-begäranden. Inlednings vis tillåts bara två begär anden till operativ systemet. Övriga 998 placeras i kö tills det finns utrymme för dem. Det innebär att din `HttpClient` tid kan ta slut eftersom det verkar som om begäran har gjorts, men begäran aldrig skickades av operativ systemet till mål servern. Det kan hända att du ser ett beteende som inte verkar vara meningsfullt: din lokala `HttpClient` tar 10 sekunder på sig att slutföra en begäran, men din tjänst returnerar varje begäran i 200 MS. 

Standardvärdet för ASP.NET-program är `Int32.MaxValue` , och det är sannolikt att fungerar bra för WebJobs som körs i en Basic-eller högre App Service-plan. WebJobs kräver vanligt vis inställningen Always on och stöds endast av Basic-och högre App Services planer.

Om ditt webb jobb körs i en kostnads fri eller delad App Service plan begränsas ditt program av App Service sandbox, som för närvarande har en [anslutnings gräns på 300](https://github.com/projectkudu/kudu/wiki/Azure-Web-App-sandbox#per-sandbox-per-appper-site-numerical-limits). Med en obunden anslutnings gräns i `ServicePointManager` är det troligt att tröskelvärdet för sand Box anslutning nås och att platsen stängs av. I så fall kan inställningen `DefaultConnectionLimit` för något lägre, till exempel 50 eller 100, förhindra att detta sker och fortfarande tillåta tillräckligt med data flöde.

Inställningen måste konfigureras innan en HTTP-begäran görs. Därför bör WebJobs-värden inte justera inställningen automatiskt. Det kan finnas HTTP-begäranden som inträffar innan värden startar, vilket kan leda till oväntat beteende. Den bästa metoden är att ange värdet direkt i din `Main` metod innan du initierar `JobHost` , som du ser här:

```csharp
static void Main(string[] args)
{
    // Set this immediately so that it's used by all requests.
    ServicePointManager.DefaultConnectionLimit = Int32.MaxValue;

    var host = new JobHost();
    host.RunAndBlock();
}
```

## <a name="triggers"></a>Utlösare

Functions måste vara offentliga metoder och måste ha ett trigger-attribut eller- [`NoAutomaticTrigger`](#manual-triggers) attribut.

### <a name="automatic-triggers"></a>Automatiska utlösare

Automatiska utlösare anropar en funktion som svar på en händelse. Överväg det här exemplet på en funktion som utlöses av ett meddelande som lagts till i Azure Queue Storage. Den svarar genom att läsa en BLOB från Azure Blob Storage:

```cs
public static void Run(
    [QueueTrigger("myqueue-items")] string myQueueItem,
    [Blob("samples-workitems/{queueTrigger}", FileAccess.Read)] Stream myBlob,
    ILogger log)
{
    log.LogInformation($"BlobInput processed blob\n Name:{myQueueItem} \n Size: {myBlob.Length} bytes");
}
```

`QueueTrigger`Attributet talar om för körningen att anropa funktionen när ett Queue-meddelande visas i `myqueue-items` kön. `Blob`Attributet instruerar körningen att använda Queue-meddelandet för att läsa en BLOB i *WorkItems-* behållaren. Namnet på BLOB-objektet i `samples-workitems` behållaren hämtas direkt från kön utlösare som ett bindnings uttryck ( `{queueTrigger}` ).

[!INCLUDE [webjobs-always-on-note](../../includes/webjobs-always-on-note.md)]

### <a name="manual-triggers"></a>Manuella utlösare

Om du vill utlösa en funktion manuellt använder du `NoAutomaticTrigger` attributet, som du ser här:

```cs
[NoAutomaticTrigger]
public static void CreateQueueMessage(
ILogger logger,
string value,
[Queue("outputqueue")] out string message)
{
    message = value;
    logger.LogInformation("Creating queue message: ", message);
}
```

Processen för att utlösa funktionen manuellt beror på SDK-versionen.

#### <a name="version-3x"></a>Version 3. *x*

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage();
    });
    var host = builder.Build();
    using (host)
    {
        var jobHost = host.Services.GetService(typeof(IJobHost)) as JobHost;
        var inputs = new Dictionary<string, object>
        {
            { "value", "Hello world!" }
        };

        await host.StartAsync();
        await jobHost.CallAsync("CreateQueueMessage", inputs);
        await host.StopAsync();
    }
}
```

#### <a name="version-2x"></a>Version 2. *x*

```cs
static void Main(string[] args)
{
    JobHost host = new JobHost();
    host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
}
```

## <a name="input-and-output-bindings"></a>Bindningar för indata och utdata

Med indata-bindningar får du ett deklarativ sätt att göra data från Azure eller tjänster från tredje part tillgängliga för din kod. Utgående bindningar ger ett sätt att uppdatera data. I artikeln [komma igång](webjobs-sdk-get-started.md) visas ett exempel på var och en.

Du kan använda ett metod retur värde för en utgående bindning genom att tillämpa attributet på metodens retur värde. Se exemplet i [använda Azure Function-returvärdet](../azure-functions/functions-bindings-return-value.md).

## <a name="binding-types"></a>Bindnings typer

Processen för att installera och hantera bindnings typer beror på om du använder version 3. *x* eller version 2. *x* i SDK. Du hittar paketet att installera för en viss bindnings typ i avsnittet "paket" i bindnings typens Azure Functions [referens artikel](#binding-reference-information). Ett undantag är filernas utlösare och bindning (för det lokala fil systemet), som inte stöds av Azure Functions.

#### <a name="version-3x"></a>Version 3. *x*

I version 3. *x*, ingår lagrings bindningarna i `Microsoft.Azure.WebJobs.Extensions.Storage` paketet. Anropa `AddAzureStorage` tilläggs metoden i `ConfigureWebJobs` metoden, som visas här:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddAzureStorage();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Om du vill använda andra typer av utlösare och bindningar installerar du NuGet-paketet som innehåller dem och anropar `Add<binding>` tilläggs metoden som implementeras i tillägget. Om du till exempel vill använda en Azure Cosmos DB-bindning, installerar `Microsoft.Azure.WebJobs.Extensions.CosmosDB` och anropar du så `AddCosmosDB` här:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddCosmosDB();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Om du vill använda timer-utlösaren eller fil bindningen, som är en del av kärn tjänsterna, anropar du respektive `AddTimers` `AddFiles` tilläggs metoder.

#### <a name="version-2x"></a>Version 2. *x*

Dessa utlösare och bindnings typer ingår i version 2. *x* i `Microsoft.Azure.WebJobs` paketet:

* Blob Storage
* Queue Storage
* Table Storage

Om du vill använda andra typer av utlösare och bindningar installerar du NuGet-paketet som innehåller dem och anropar en `Use<binding>` metod på `JobHostConfiguration` objektet. Om du till exempel vill använda en timer-utlösare, installerar `Microsoft.Azure.WebJobs.Extensions` och anropar du `UseTimers` `Main` metoden, som du ser här:

```cs
static void Main()
{
    config = new JobHostConfiguration();
    config.UseTimers();
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

Installera och anropa för att använda fil bindningen `Microsoft.Azure.WebJobs.Extensions` `UseFiles` .

### <a name="executioncontext"></a>ExecutionContext

Med WebJobs kan du binda till en [`ExecutionContext`] . Med den här bindningen får du åtkomst till [`ExecutionContext`] som en parameter i funktions under skriften. I följande kod används till exempel objektet Context för att få åtkomst till anrops-ID: t, som du kan använda för att korrelera alla loggar som skapats av ett angivet funktions anrop.  

```cs
public class Functions
{
    public static void ProcessQueueMessage([QueueTrigger("queue")] string message,
        ExecutionContext executionContext,
        ILogger logger)
    {
        logger.LogInformation($"{message}\n{executionContext.InvocationId}");
    }
}
```

Processen för bindning till [`ExecutionContext`] beror på din SDK-version.

#### <a name="version-3x"></a>Version 3. *x*

Anropa `AddExecutionContextBinding` tilläggs metoden i `ConfigureWebJobs` metoden, som visas här:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
            {
                b.AddAzureStorageCoreServices();
                b.AddExecutionContextBinding();
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Version 2. *x*

Det `Microsoft.Azure.WebJobs.Extensions` paket som nämns ovan tillhandahåller också en särskild bindnings typ som du kan registrera genom att anropa- `UseCore` metoden. Med den här bindningen kan du definiera en [`ExecutionContext`] parameter i funktions under skriften, som är aktive rad så här:

```cs
class Program
{
    static void Main()
    {
        config = new JobHostConfiguration();
        config.UseCore();
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="binding-configuration"></a>Bindnings konfiguration

Du kan konfigurera beteendet för vissa utlösare och bindningar. Processen för att konfigurera dem beror på SDK-versionen.

* **Version 3. *x*:** ange konfiguration när `Add<Binding>` metoden anropas i `ConfigureWebJobs` .
* **Version 2. *x*:** ange konfiguration genom att ange egenskaper i ett konfigurations objekt som du skickar till `JobHost` .

Dessa bindande inställningar motsvarar inställningarna i [host.jsi projekt filen](../azure-functions/functions-host-json.md) i Azure Functions.

Du kan konfigurera följande bindningar:

* [Azure CosmosDB-utlösare](#azure-cosmosdb-trigger-configuration-version-3x)
* [Event Hubs utlösare](#event-hubs-trigger-configuration-version-3x)
* [Kölagringsutlösare](#queue-storage-trigger-configuration)
* [SendGrid-bindning](#sendgrid-binding-configuration-version-3x)
* [Service Bus utlösare](#service-bus-trigger-configuration-version-3x)

### <a name="azure-cosmosdb-trigger-configuration-version-3x"></a>Konfiguration av Azure CosmosDB-utlösare (version 3.*x*)

Det här exemplet visar hur du konfigurerar Azure Cosmos DB-utlösaren:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddCosmosDB(a =>
        {
            a.ConnectionMode = ConnectionMode.Gateway;
            a.Protocol = Protocol.Https;
            a.LeaseOptions.LeasePrefix = "prefix1";

        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Mer information finns i artikeln om [bindning för Azure CosmosDB](../azure-functions/functions-bindings-cosmosdb-v2-output.md#hostjson-settings) .

### <a name="event-hubs-trigger-configuration-version-3x"></a>Event Hubs utlösarens konfiguration (version 3.*x*)

Det här exemplet visar hur du konfigurerar Event Hubs-utlösaren:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddEventHubs(a =>
        {
            a.BatchCheckpointFrequency = 5;
            a.EventProcessorOptions.MaxBatchSize = 256;
            a.EventProcessorOptions.PrefetchCount = 512;
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Mer information finns i artikeln [Event Hubs bindning](../azure-functions/functions-bindings-event-hubs-trigger.md#host-json) .

### <a name="queue-storage-trigger-configuration"></a>Konfiguration av kö Storage-utlösare

Följande exempel visar hur du konfigurerar utlösaren för kön Storage:

#### <a name="version-3x"></a>Version 3. *x*

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddAzureStorage(a => {
            a.BatchSize = 8;
            a.NewBatchThreshold = 4;
            a.MaxDequeueCount = 4;
            a.MaxPollingInterval = TimeSpan.FromSeconds(15);
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Mer information finns i artikeln om [kö Storage-bindning](../azure-functions/functions-bindings-storage-queue-trigger.md#hostjson-properties) .

#### <a name="version-2x"></a>Version 2. *x*

```cs
static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.Queues.BatchSize = 8;
    config.Queues.NewBatchThreshold = 4;
    config.Queues.MaxDequeueCount = 4;
    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Mer information finns i [ referensenhost.jspå v1. x](../azure-functions/functions-host-json-v1.md#queues).

### <a name="sendgrid-binding-configuration-version-3x"></a>SendGrid bindnings konfiguration (version 3.*x*)

Det här exemplet visar hur du konfigurerar SendGrid utgående bindning:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddSendGrid(a =>
        {
            a.FromAddress.Email = "samples@functions.com";
            a.FromAddress.Name = "Azure Functions";
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Mer information finns i artikeln om [SendGrid-bindning](../azure-functions/functions-bindings-sendgrid.md#hostjson-settings) .

### <a name="service-bus-trigger-configuration-version-3x"></a>Service Bus utlösarens konfiguration (version 3.*x*)

Det här exemplet visar hur du konfigurerar Service Bus-utlösaren:

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddServiceBus(sbOptions =>
        {
            sbOptions.MessageHandlerOptions.AutoComplete = true;
            sbOptions.MessageHandlerOptions.MaxConcurrentCalls = 16;
        });
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

Mer information finns i artikeln [Service Bus bindning](../azure-functions/functions-bindings-service-bus-output.md#hostjson-settings) .

### <a name="configuration-for-other-bindings"></a>Konfiguration för andra bindningar

Vissa typer av utlösare och bindningar definierar sina egna anpassade konfigurations typer. Med fil utlösaren kan du till exempel ange rot Sök vägen som ska övervakas, som i följande exempel:

#### <a name="version-3x"></a>Version 3. *x*

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
        b.AddFiles(a => a.RootPath = @"c:\data\import");
    });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Version 2. *x*

```cs
static void Main()
{
    config = new JobHostConfiguration();
    var filesConfig = new FilesConfiguration
    {
        RootPath = @"c:\data\import"
    };
    config.UseFiles(filesConfig);
    var host = new JobHost(config);
    host.RunAndBlock();
}
```

## <a name="binding-expressions"></a>Bindande uttryck

I parametrar för attributet konstruktor kan du använda uttryck som matchar värden från olika källor. I följande kod skapar exempelvis sökvägen för `BlobTrigger` attributet ett uttryck med namnet `filename` . När den används för utgående bindning, `filename` matchas namnet på den Utlös ande blobben.

```cs
public static void CreateThumbnail(
    [BlobTrigger("sample-images/{filename}")] Stream image,
    [Blob("sample-images-sm/{filename}", FileAccess.Write)] Stream imageSmall,
    string filename,
    ILogger logger)
{
    logger.Info($"Blob trigger processing: {filename}");
    // ...
}
```

Mer information om bindnings uttryck finns i avsnittet om [bindnings uttryck och mönster](../azure-functions/functions-bindings-expressions-patterns.md) i Azure Functions-dokumentationen.

### <a name="custom-binding-expressions"></a>Anpassade bindnings uttryck

Ibland vill du ange ett könamn, ett BLOB-namn eller behållare, eller ett tabell namn i kod i stället för att hårdkoda det. Du kanske till exempel vill ange köns namn för `QueueTrigger` attributet i en konfigurations fil eller en miljö variabel.

Du kan göra det genom att skicka ett `NameResolver` objekt till `JobHostConfiguration` objektet. Du inkluderar plats hållare i parametrarna trigger eller binding Attribute, och din `NameResolver` kod tillhandahåller de faktiska värdena som ska användas i stället för dessa plats hållare. Du identifierar plats hållarna genom att omge dem med procent (%) tecken, som du ser här:

```cs
public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
{
    Console.WriteLine(logMessage);
}
```

Med den här koden kan du använda en kö med namnet `logqueuetest` i test miljön och en som heter `logqueueprod` i produktion. I stället för ett hårdkodat könamn anger du namnet på en post i `appSettings` samlingen.

Det finns ett standardvärde `NameResolver` som börjar gälla om du inte anger någon anpassad. Standardvärdet hämtar värden från appinställningar eller miljövariabler.

`NameResolver`Klassen hämtar könamnet från `appSettings` , som visas här:

```cs
public class CustomNameResolver : INameResolver
{
    public string Resolve(string name)
    {
        return ConfigurationManager.AppSettings[name].ToString();
    }
}
```

#### <a name="version-3x"></a>Version 3. *x*

Du konfigurerar matcharen genom att använda beroende inmatning. Dessa exempel kräver följande `using` instruktion:

```cs
using Microsoft.Extensions.DependencyInjection;
```

Du lägger till matcharen genom att anropa [`ConfigureServices`] tilläggs metoden på  [`HostBuilder`](/dotnet/api/microsoft.extensions.hosting.hostbuilder) , som i det här exemplet:

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    var resolver = new CustomNameResolver();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureServices(s => s.AddSingleton<INameResolver>(resolver));
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Version 2. *x*

Skicka din `NameResolver` klass till `JobHost` objektet, som du ser här:

```cs
 static void Main(string[] args)
{
    JobHostConfiguration config = new JobHostConfiguration();
    config.NameResolver = new CustomNameResolver();
    JobHost host = new JobHost(config);
    host.RunAndBlock();
}
```

Azure Functions implementerar `INameResolver` för att hämta värden från appinställningar, som du ser i exemplet. När du använder WebJobs-SDK: n direkt kan du skriva en anpassad implementering som hämtar plats hållarens ersättnings värden från vilken källa du föredrar.

## <a name="binding-at-runtime"></a>Bindning vid körning

Om du behöver göra en del arbete i din funktion innan du använder ett binding-attribut som `Queue` , `Blob` eller, `Table` kan du använda `IBinder` gränssnittet.

I följande exempel visas ett meddelande om indatakö och ett nytt meddelande skapas med samma innehåll i en utgående kö. Namnet på utdataporten anges med kod i bröd texten i funktionen.

```cs
public static void CreateQueueMessage(
    [QueueTrigger("inputqueue")] string queueMessage,
    IBinder binder)
{
    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
    outputQueue.AddMessageAsync(new CloudQueueMessage(queueMessage));
}
```

Mer information finns i [bindning vid körning](../azure-functions/functions-dotnet-class-library.md#binding-at-runtime) i Azure Functions-dokumentationen.

## <a name="binding-reference-information"></a>Information om bindnings referensinformation

Azure Functions-dokumentationen innehåller referensinformation om varje bindnings typ. Du hittar följande information i varje bindnings referens artikel. (Det här exemplet baseras på lagrings kön.)

* [Paket](../azure-functions/functions-bindings-storage-queue.md). Paketet du måste installera för att inkludera stöd för bindningen i ett WebJobs SDK-projekt.
* [Exempel](../azure-functions/functions-bindings-storage-queue-trigger.md). Kod exempel. Exempel på C#-klass biblioteket gäller för WebJobs SDK. Utelämna bara `FunctionName` attributet.
* [Attribut](../azure-functions/functions-bindings-storage-queue-trigger.md#attributes-and-annotations). De attribut som ska användas för bindnings typen.
* [Konfiguration](../azure-functions/functions-bindings-storage-queue-trigger.md#configuration). Förklaringar av attributets egenskaper och parametrar för konstruktorn.
* [Användning](../azure-functions/functions-bindings-storage-queue-trigger.md#usage). Typerna som du kan binda till och information om hur bindningen fungerar. Exempel: avsöknings algoritm, bearbetning av Poison-köer.
  
En lista över bindnings referens artiklar finns i "bindningar som stöds" i artikeln [utlösare och bindningar](../azure-functions/functions-triggers-bindings.md#supported-bindings) för Azure Functions. I den här listan stöds HTTP-, webhook-och Event Grid-bindningar bara av Azure Functions, inte av WebJobs SDK.

## <a name="disable-attribute"></a>Inaktivera attribut 

Med [`Disable`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/DisableAttribute.cs) attributet kan du kontrol lera om en funktion kan utlösas. 

I följande exempel, om app-inställningen `Disable_TestJob` har värdet `1` eller `True` (inte Skift läges känsligt), körs inte funktionen. I så fall skapar körningen en logg meddelande *funktion "Functions. TestJob" som är inaktive rad*.

```cs
[Disable("Disable_TestJob")]
public static void TestJob([QueueTrigger("testqueue2")] string message)
{
    Console.WriteLine("Function with Disable attribute executed!");
}
```

När du ändrar inställnings värden för appen i Azure Portal startas Start programmet om för att hämta den nya inställningen.

Attributet kan deklareras på parameter-, metod-eller klass nivå. Inställnings namnet kan också innehålla bindnings uttryck.

## <a name="timeout-attribute"></a>Timeout-attribut

[`Timeout`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/TimeoutAttribute.cs)Attributet gör att en funktion avbryts om den inte slutförs inom en angiven tids period. I följande exempel körs funktionen en dag utan attributet timeout. Timeout gör att funktionen avbryts efter 15 sekunder.

```cs
[Timeout("00:00:15")]
public static async Task TimeoutJob(
    [QueueTrigger("testqueue2")] string message,
    CancellationToken token,
    TextWriter log)
{
    await log.WriteLineAsync("Job starting");
    await Task.Delay(TimeSpan.FromDays(1), token);
    await log.WriteLineAsync("Job completed");
}
```

Du kan använda attributet timeout på klass-eller metod nivå och du kan ange en global tids gräns med hjälp av `JobHostConfiguration.FunctionTimeout` . Tids gränser på klass-eller metod nivå åsidosätter globala tids gränser.

## <a name="singleton-attribute"></a>Singleton-attribut

[`Singleton`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonAttribute.cs)Attributet säkerställer att endast en instans av en funktion körs, även om det finns flera instanser av värd-webbappen. Detta görs med hjälp av [distribuerad låsning](#viewing-lease-blobs).

I det här exemplet körs bara en enda instans av `ProcessImage` funktionen vid en viss tidpunkt:

```cs
[Singleton]
public static async Task ProcessImage([BlobTrigger("images")] Stream image)
{
     // Process the image.
}
```

### <a name="singletonmodelistener"></a>SingletonMode. Listener

Vissa utlösare har inbyggt stöd för samtidig hantering:

* **QueueTrigger**. Ange `JobHostConfiguration.Queues.BatchSize` till `1` .
* **ServiceBusTrigger**. Ange `ServiceBusConfiguration.MessageOptions.MaxConcurrentCalls` till `1` .
* **FileTrigger**. Ange `FileProcessor.MaxDegreeOfParallelism` till `1` .

Du kan använda de här inställningarna för att se till att funktionen körs som en singleton-instans på en enda instans. För att se till att endast en enda instans av funktionen körs när webbappen skalar ut till flera instanser, ska du använda ett singleton-lås på lyssnar-nivå i funktionen ( `[Singleton(Mode = SingletonMode.Listener)]` ). Lyssnar lås förvärvas när JobHost startar. Om tre utskalade instanser startar samtidigt, kommer bara en av instanserna att förvärva låset och bara en lyssnare startar.

> [!NOTE]
> Se den här [GitHub-lagrings platsen](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/SingletonMode.cs) för mer information om hur SingletonMode. Function fungerar.

### <a name="scope-values"></a>Omfattnings värden

Du kan ange ett *omfattnings uttryck/-värde* på en singleton. Uttrycket/värdet säkerställer att alla körningar av funktionen i en speciell omfattning serialiseras. Genom att implementera mer detaljerad låsning på det här sättet kan du tillåta en viss nivå av parallellitet för din funktion samtidigt som du serialiserar andra anrop som styrs av dina krav. I följande kod binder till exempel omfångs uttrycket till `Region` värdet för det inkommande meddelandet. När kön innehåller tre meddelanden i regionerna öst, öst och väst, körs meddelanden som har region Öst i serie medan meddelandet med regionen Väst körs parallellt med dem i öst.

```csharp
[Singleton("{Region}")]
public static async Task ProcessWorkItem([QueueTrigger("workitems")] WorkItem workItem)
{
     // Process the work item.
}

public class WorkItem
{
     public int ID { get; set; }
     public string Region { get; set; }
     public int Category { get; set; }
     public string Description { get; set; }
}
```

### <a name="singletonscopehost"></a>SingletonScope. Host

Standard omfånget för ett lås är `SingletonScope.Function` , vilket innebär att Lås omfånget (BLOB-lånets sökväg) är kopplat till det fullständigt kvalificerade funktions namnet. Om du vill låsa mellan funktioner anger du `SingletonScope.Host` och använder ett scope-ID-namn som är samma för alla funktioner som du inte vill köra samtidigt. I följande exempel är endast en instans av `AddItem` eller `RemoveItem` körs i taget:

```csharp
[Singleton("ItemsLock", SingletonScope.Host)]
public static void AddItem([QueueTrigger("add-item")] string message)
{
     // Perform the add operation.
}

[Singleton("ItemsLock", SingletonScope.Host)]
public static void RemoveItem([QueueTrigger("remove-item")] string message)
{
     // Perform the remove operation.
}
```

### <a name="viewing-lease-blobs"></a>Visa leasing-blobar

WebJobs-SDK: n använder [Azure Blob-lån](../storage/blobs/concurrency-manage.md#pessimistic-concurrency-for-blobs) under försättsblad för att implementera distribuerad låsning. De leasing-blobbar som används av singleton finns i `azure-webjobs-host` behållaren i `AzureWebJobsStorage` lagrings kontot under sökvägen "lås". Till exempel kan låne-BLOB-sökvägen för det första `ProcessImage` exemplet som visas tidigare vara `locks/061851c758f04938a4426aa9ab3869c0/WebJobs.Functions.ProcessImage` . Alla sökvägar inkluderar JobHost-ID, i det här fallet 061851c758f04938a4426aa9ab3869c0.

## <a name="async-functions"></a>Asynkrona funktioner

Information om hur du kodar asynkrona funktioner finns i [Azure Functions-dokumentationen](../azure-functions/functions-dotnet-class-library.md#async).

## <a name="cancellation-tokens"></a>Token för avbrytande

Information om hur du hanterar inställda token finns i Azure Functions-dokumentationen om [avbrutna token och korrekt avstängning](../azure-functions/functions-dotnet-class-library.md#cancellation-tokens).

## <a name="multiple-instances"></a>Flera instanser

Om din webbapp körs på flera instanser körs ett kontinuerligt webbjobb på varje instans och lyssnar efter utlösare och anropar funktioner. De olika utlösnings bindningarna är utformade för att effektivt dela arbete som är kollektivt över flera instanser, så att du kan hantera mer belastning genom att skala ut till fler instanser.

Vissa utlösare kan leda till dubbel bearbetning, kö-och Blob Storage-utlösare förhindrar automatiskt en funktion från att bearbeta ett köat meddelande eller en BLOB mer än en gång. Mer information finns i [utforma för identiska indata](../azure-functions/functions-idempotent.md) i Azure Functions-dokumentationen.

Timer-utlösaren säkerställer automatiskt att endast en instans av timern körs, så att du inte får fler än en funktions instans som körs vid en specifik schemalagd tid.

Om du vill se till att endast en instans av en funktion körs även om det finns flera instanser av värd webb programmet kan du använda- [`Singleton`](#singleton-attribute) attributet.

## <a name="filters"></a>Filter

Funktions filter (för hands version) ger dig ett sätt att anpassa WebJobs-körningens pipeline med din egen logik. Filter liknar [ASP.net Core filter](/aspnet/core/mvc/controllers/filters). Du kan implementera dem som deklarativ attribut som tillämpas på dina funktioner eller klasser. Mer information finns i [funktions filter](https://github.com/Azure/azure-webjobs-sdk/wiki/Function-Filters).

## <a name="logging-and-monitoring"></a>Loggning och övervakning

Vi rekommenderar loggnings ramverket som har utvecklats för ASP.NET. Artikeln [Kom igång](webjobs-sdk-get-started.md) visar hur du använder den. 

### <a name="log-filtering"></a>Logg filtrering

Varje logg som skapats av en `ILogger` instans har en associerad `Category` och `Level` . [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel) är en uppräkning och heltals koden indikerar relativ prioritet:

|Loggnivå    |Kod|
|------------|---|
|Spårning       | 0 |
|Felsökning       | 1 |
|Information | 2 |
|Varning     | 3 |
|Fel       | 4 |
|Kritiskt    | 5 |
|Inget        | 6 |

Du kan filtrera varje kategori separat till en viss [`LogLevel`](/dotnet/api/microsoft.extensions.logging.loglevel) . Du kanske till exempel vill se alla loggar för bearbetning av BLOB-utlösare, men endast `Error` och högre för allt annat.

#### <a name="version-3x"></a>Version 3. *x*

Version 3. *x* av SDK är beroende av filtreringen som är inbyggd i .net Core. Med `LogCategories` klassen kan du definiera kategorier för vissa funktioner, utlösare eller användare. Den definierar också filter för vissa värd tillstånd, t `Startup` `Results` . ex. och. På så sätt kan du finjustera loggnings resultatet. Om ingen matchning hittas inom de definierade kategorierna går filtret tillbaka till `Default` värdet när du bestämmer om meddelandet ska filtreras.

`LogCategories` kräver följande användnings instruktion:

```cs
using Microsoft.Azure.WebJobs.Logging; 
```

I följande exempel skapas ett filter som som standard filtrerar alla loggar på `Warning` nivån. `Function`Kategorierna och `results` (motsvarar `Host.Results` i version 2.*x*) filtreras på `Error` nivån. Filtret jämför den aktuella kategorin med alla registrerade nivåer i `LogCategories` instansen och väljer den längsta matchningen. Det innebär att `Debug` nivån som registrerats för `Host.Triggers` matchningar `Host.Triggers.Queue` eller `Host.Triggers.Blob` . På så sätt kan du styra bredare kategorier utan att behöva lägga till var och en.

```cs
static async Task Main(string[] args)
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging(logging =>
            {
                logging.SetMinimumLevel(LogLevel.Warning);
                logging.AddFilter("Function", LogLevel.Error);
                logging.AddFilter(LogCategories.CreateFunctionCategory("MySpecificFunctionName"),
                    LogLevel.Debug);
                logging.AddFilter(LogCategories.Results, LogLevel.Error);
                logging.AddFilter("Host.Triggers", LogLevel.Debug);
            });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

#### <a name="version-2x"></a>Version 2. *x*

I version 2. *x* i SDK använder du `LogCategoryFilter` klassen för att styra filtreringen. `LogCategoryFilter`Har en `Default` egenskap med ett initialt värde `Information` , vilket innebär att alla meddelanden på `Information` nivåerna,, `Warning` `Error` eller `Critical` är loggade, men alla meddelanden på eller- `Debug` `Trace` nivåerna filtreras bort.

Som `LogCategories` i version 3.*x* kan `CategoryLevels` du med egenskapen ange logg nivåer för vissa kategorier så att du kan finjustera loggnings resultatet. Om ingen matchning hittas i `CategoryLevels` ord listan går filtret tillbaka till `Default` värdet när du bestämmer om meddelandet ska filtreras.

I följande exempel skapas ett filter som som standard filtrerar alla loggar på `Warning` nivån. `Function`Kategorierna och `Host.Results` filtreras på `Error` nivån. Den `LogCategoryFilter` aktuella kategorin jämförs med alla registrerade `CategoryLevels` och väljer den längsta matchningen. Den `Debug` nivå som registrerats för `Host.Triggers` kommer att matcha `Host.Triggers.Queue` eller `Host.Triggers.Blob` . På så sätt kan du styra bredare kategorier utan att behöva lägga till var och en.

```csharp
var filter = new LogCategoryFilter();
filter.DefaultLevel = LogLevel.Warning;
filter.CategoryLevels[LogCategories.Function] = LogLevel.Error;
filter.CategoryLevels[LogCategories.Results] = LogLevel.Error;
filter.CategoryLevels["Host.Triggers"] = LogLevel.Debug;

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(instrumentationKey, filter.Filter)
    .AddConsole(filter.Filter);
```

### <a name="custom-telemetry-for-application-insights"></a>Anpassad telemetri för Application Insights

Processen för att implementera anpassad telemetri för [Application Insights](../azure-monitor/app/app-insights-overview.md) beror på SDK-versionen. Information om hur du konfigurerar Application Insights finns i [Lägg till Application Insights loggning](webjobs-sdk-get-started.md#add-application-insights-logging).

#### <a name="version-3x"></a>Version 3. *x*

Eftersom version 3. *x* av WebJobs SDK är beroende av den generiska .net Core-värden, en anpassad telemetri fabrik tillhandahålls inte längre. Men du kan lägga till anpassad telemetri till pipelinen med hjälp av beroende inmatning. I exemplen i det här avsnittet krävs följande `using` instruktioner:

```cs
using Microsoft.ApplicationInsights.Extensibility;
using Microsoft.ApplicationInsights.Channel;
```

Med följande anpassade implementering av [`ITelemetryInitializer`] kan du lägga till egna [`ITelemetry`](/dotnet/api/microsoft.applicationinsights.channel.itelemetry) i standardvärdet [`TelemetryConfiguration`] .

```cs
internal class CustomTelemetryInitializer : ITelemetryInitializer
{
    public void Initialize(ITelemetry telemetry)
    {
        // Do something with telemetry.
    }
}
```

Anropa [`ConfigureServices`] i verktyget för att lägga till din anpassade [`ITelemetryInitializer`] i pipelinen.

```cs
static async Task Main()
{
    var builder = new HostBuilder();
    builder.ConfigureWebJobs(b =>
    {
        b.AddAzureStorageCoreServices();
    });
    builder.ConfigureLogging((context, b) =>
    {
        // Add logging providers.
        b.AddConsole();

        // If this key exists in any config, use it to enable Application Insights.
        string appInsightsKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
        if (!string.IsNullOrEmpty(appInsightsKey))
        {
            // This uses the options callback to explicitly set the instrumentation key.
            b.AddApplicationInsights(o => o.InstrumentationKey = appInsightsKey);
        }
    });
    builder.ConfigureServices(services =>
        {
            services.AddSingleton<ITelemetryInitializer, CustomTelemetryInitializer>();
        });
    var host = builder.Build();
    using (host)
    {
        await host.RunAsync();
    }
}
```

När [`TelemetryConfiguration`] är konstruerad inkluderas alla registrerade typer [`ITelemetryInitializer`] . Läs mer i [Application Insights API för anpassade händelser och mått](../azure-monitor/app/api-custom-events-metrics.md).

I version 3. *x* behöver du inte längre rensa [`TelemetryClient`] när värden stoppas. .NET Core-beroende inmatnings systemet tas automatiskt bort från den registrerade `ApplicationInsightsLoggerProvider` , vilket tömmer [`TelemetryClient`] .

#### <a name="version-2x"></a>Version 2. *x*

I version 2. *x*, som [`TelemetryClient`] skapats internt av Application Insights-providern för WebJobs SDK använder [`ServerTelemetryChannel`](https://github.com/microsoft/ApplicationInsights-dotnet/tree/develop/.publicApi/Microsoft.AI.ServerTelemetryChannel.dll) . När Application Insights-slutpunkten är otillgänglig eller begränsa inkommande begär Anden, sparar den här kanalen [begär anden i webbappens fil system och skickar dem igen senare](https://apmtips.com/posts/2015-09-03-more-telemetry-channels/).

[`TelemetryClient`]Skapas av en klass som implementerar `ITelemetryClientFactory` . Som standard är detta [`DefaultTelemetryClientFactory`](https://github.com/Azure/azure-webjobs-sdk/blob/dev/src/Microsoft.Azure.WebJobs.Logging.ApplicationInsights/) .

Om du vill ändra någon del av Application Insights pipeline kan du ange din egen `ITelemetryClientFactory` och värden använder klassen för att skapa en [`TelemetryClient`] . Den här koden åsidosätter exempelvis `DefaultTelemetryClientFactory` för att ändra en egenskap för `ServerTelemetryChannel` :

```csharp
private class CustomTelemetryClientFactory : DefaultTelemetryClientFactory
{
    public CustomTelemetryClientFactory(string instrumentationKey, Func<string, LogLevel, bool> filter)
        : base(instrumentationKey, new SamplingPercentageEstimatorSettings(), filter)
    {
    }

    protected override ITelemetryChannel CreateTelemetryChannel()
    {
        ServerTelemetryChannel channel = new ServerTelemetryChannel();

        // Change the default from 30 seconds to 15 seconds.
        channel.MaxTelemetryBufferDelay = TimeSpan.FromSeconds(15);

        return channel;
    }
}
```

`SamplingPercentageEstimatorSettings`Objektet konfigurerar [adaptiv sampling](../azure-monitor/app/sampling.md). Det innebär att program insikter skickar en vald delmängd av telemetridata till-servern i vissa hög volym scenarier.

När du har skapat telemetri fabriken skickar du den till Application Insights Logging-providern:

```csharp
var clientFactory = new CustomTelemetryClientFactory(instrumentationKey, filter.Filter);

config.LoggerFactory = new LoggerFactory()
    .AddApplicationInsights(clientFactory);
```

## <a name="next-steps"></a><a id="nextsteps"></a> Nästa steg

Den här artikeln innehåller kodfragment som visar hur du hanterar vanliga scenarier för att arbeta med WebJobs SDK. Fullständiga exempel finns i [Azure-WebJobs-SDK-samples](https://github.com/Azure/azure-webjobs-sdk/tree/dev/sample/SampleHost).

[ExecutionContext]: https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions/Extensions/Core/ExecutionContext.cs
[TelemetryClient]: /dotnet/api/microsoft.applicationinsights.telemetryclient
[ConfigureServices]: /dotnet/api/microsoft.extensions.hosting.hostinghostbuilderextensions.configureservices
['ITelemetryInitializer']: /dotnet/api/microsoft.applicationinsights.extensibility.itelemetryinitializer
['TelemetryConfiguration']: /dotnet/api/microsoft.applicationinsights.extensibility.telemetryconfiguration
['JobHostConfiguration']: https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.Host/JobHostConfiguration.cs