---
title: Referens för Azure Functions C#-skript utvecklare
description: Lär dig hur du utvecklar Azure Functions med C#-skript.
author: craigshoemaker
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 12/12/2017
ms.author: cshoe
ms.openlocfilehash: 48614640660da6d85face5ea416d267fa9f59515
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92164847"
---
# <a name="azure-functions-c-script-csx-developer-reference"></a>Referens för Azure Functions C#-skript (. CSX)

<!-- When updating this article, make corresponding changes to any duplicate content in functions-dotnet-class-library.md -->

Den här artikeln är en introduktion till att utveckla Azure Functions med hjälp av C#-skript (*. CSX*).

Azure Functions stöder C#-och C#-skript programmeringsspråk. Om du vill ha vägledning om hur du [använder C# i ett biblioteks projekt i Visual Studio-klass](functions-develop-vs.md), se [C#-referens för utvecklare](functions-dotnet-class-library.md).

Den här artikeln förutsätter att du redan har läst [guiden Azure Functions utvecklare](functions-reference.md).

## <a name="how-csx-works"></a>How. CSX fungerar

C#-skript upplevelsen för Azure Functions baseras på [Azure WEBJOBS SDK](https://github.com/Azure/azure-webjobs-sdk/wiki/Introduction). Data flödar in i C#-funktionen via metod argument. Argument namn anges i en `function.json` fil och det finns fördefinierade namn för att komma åt saker som funktions loggar och avbrutna token.

Med formatet *. CSX* kan du skriva mindre "standard" och fokusera på att bara skriva en C#-funktion. I stället för att figursätta allting i ett namn område och en klass definierar du bara en `Run` metod. Ta med alla sammansättnings referenser och namn rymder i början av filen som vanligt.

En Function Apps *. CSX* -filer kompileras när en instans initieras. Det här steget innebär att kall start kan ta längre tid för C#-skript funktioner jämfört med klass bibliotek i C#. Det här steget i kompileringen är också orsaken till att C#-skript funktioner kan redige ras i Azure Portal, medan C#-klass biblioteken inte är det.

## <a name="folder-structure"></a>Mappstruktur

Mappstrukturen för ett C#-skript projekt ser ut så här:

```
FunctionsProject
 | - MyFirstFunction
 | | - run.csx
 | | - function.json
 | | - function.proj
 | - MySecondFunction
 | | - run.csx
 | | - function.json
 | | - function.proj
 | - host.json
 | - extensions.csproj
 | - bin
```

Det finns ett delat [host.jspå](functions-host-json.md) en fil som kan användas för att konfigurera Function-appen. Varje funktion har sin egen kod fil (. CSX) och bindnings konfigurations fil (function.jspå).

De bindnings tillägg som krävs i [version 2. x och senare versioner](functions-versions.md) av Functions-körningen definieras i `extensions.csproj` filen med de faktiska biblioteksfilerna i `bin` mappen. När du utvecklar lokalt måste du [Registrera bindnings tillägg](./functions-bindings-register.md#extension-bundles). När du utvecklar funktioner i Azure Portal görs registreringen åt dig.

## <a name="binding-to-arguments"></a>Bindning till argument

Indata-eller utdata är kopplade till en C#-skript funktions parameter via `name` egenskapen i *function.jsi* konfigurations filen. I följande exempel visas en *function.jspå* filen och filen *Run. CSX* för en funktion som utlöses av en kö. Den parameter som tar emot data från Queue-meddelandet får namnet `myQueueItem` eftersom det är `name` egenskapens värde.

```json
{
    "disabled": false,
    "bindings": [
        {
            "type": "queueTrigger",
            "direction": "in",
            "name": "myQueueItem",
            "queueName": "myqueue-items",
            "connection":"MyStorageConnectionAppSetting"
        }
    ]
}
```

```csharp
#r "Microsoft.WindowsAzure.Storage"

using Microsoft.Extensions.Logging;
using Microsoft.WindowsAzure.Storage.Queue;
using System;

public static void Run(CloudQueueMessage myQueueItem, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed: {myQueueItem.AsString}");
}
```

`#r`Instruktionen beskrivs [senare i den här artikeln](#referencing-external-assemblies).

## <a name="supported-types-for-bindings"></a>Typer som stöds för bindningar

Varje bindning har sina egna typer som stöds. en BLOB-utlösare kan till exempel användas med en sträng parameter, en POCO-parameter, en `CloudBlockBlob` parameter eller någon av flera andra typer som stöds. [Bindnings referens artikeln för BLOB-bindningar](functions-bindings-storage-blob-trigger.md#usage) listar alla parameter typer som stöds för BLOB-utlösare. Mer information finns i [utlösare och bindningar](functions-triggers-bindings.md) och [bindnings referens dokument för varje bindnings typ](functions-triggers-bindings.md#next-steps).

[!INCLUDE [HTTP client best practices](../../includes/functions-http-client-best-practices.md)]

## <a name="referencing-custom-classes"></a>Referera till anpassade klasser

Om du behöver använda en anpassad POCO-klass (Plain CLR Object) kan du inkludera klass definitionen i samma fil eller lägga den i en separat fil.

I följande exempel visas ett *Run. CSX* -exempel som innehåller en definition av en Poco-klass.

```csharp
public static void Run(string myBlob, out MyClass myQueueItem)
{
    log.Verbose($"C# Blob trigger function processed: {myBlob}");
    myQueueItem = new MyClass() { Id = "myid" };
}

public class MyClass
{
    public string Id { get; set; }
}
```

En POCO-klass måste ha en get-och set-metod definierad för varje egenskap.

## <a name="reusing-csx-code"></a>Återanvänder. CSX-kod

Du kan använda klasser och metoder som definierats i andra *. CSX* -filer i filen *Run. CSX* . Det gör du genom att använda `#load` direktiv i filen *Run. CSX* . I följande exempel delas en loggnings rutin med namnet `MyLogger` i *loggen. CSX* och läses in i *Run. CSX* med hjälp av `#load` direktivet:

Exempel på *Kör. CSX*:

```csharp
#load "mylogger.csx"

using Microsoft.Extensions.Logging;

public static void Run(TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"Log by run.csx: {DateTime.Now}");
    MyLogger(log, $"Log by MyLogger: {DateTime.Now}");
}
```

Exempel på *CSX*:

```csharp
public static void MyLogger(ILogger log, string logtext)
{
    log.LogInformation(logtext);
}
```

Att använda en delad *. CSX* -fil är ett vanligt mönster när du vill skriva data som skickas mellan funktioner med hjälp av ett Poco-objekt. I följande förenklade exempel delar en HTTP-utlösare och en Queue-utlösare ett POCO-objekt med namnet `Order` stark typ av order data:

Exempel på *Kör. CSX* för http-utlösare:

```cs
#load "..\shared\order.csx"

using System.Net;
using Microsoft.Extensions.Logging;

public static async Task<HttpResponseMessage> Run(Order req, IAsyncCollector<Order> outputQueueItem, ILogger log)
{
    log.LogInformation("C# HTTP trigger function received an order.");
    log.LogInformation(req.ToString());
    log.LogInformation("Submitting to processing queue.");

    if (req.orderId == null)
    {
        return new HttpResponseMessage(HttpStatusCode.BadRequest);
    }
    else
    {
        await outputQueueItem.AddAsync(req);
        return new HttpResponseMessage(HttpStatusCode.OK);
    }
}
```

Exempel på *Kör. CSX* för Queue-utlösare:

```cs
#load "..\shared\order.csx"

using System;
using Microsoft.Extensions.Logging;

public static void Run(Order myQueueItem, out Order outputQueueItem, ILogger log)
{
    log.LogInformation($"C# Queue trigger function processed order...");
    log.LogInformation(myQueueItem.ToString());

    outputQueueItem = myQueueItem;
}
```

Exempel *order. CSX*:

```cs
public class Order
{
    public string orderId {get; set; }
    public string custName {get; set;}
    public string custAddress {get; set;}
    public string custEmail {get; set;}
    public string cartId {get; set; }

    public override String ToString()
    {
        return "\n{\n\torderId : " + orderId +
                  "\n\tcustName : " + custName +
                  "\n\tcustAddress : " + custAddress +
                  "\n\tcustEmail : " + custEmail +
                  "\n\tcartId : " + cartId + "\n}";
    }
}
```

Du kan använda en relativ sökväg med `#load` direktivet:

* `#load "mylogger.csx"` läser in en fil som finns i Function-mappen.
* `#load "loadedfiles\mylogger.csx"` läser in en fil som finns i en mapp i Function-mappen.
* `#load "..\shared\mylogger.csx"` läser in en fil som finns i en mapp på samma nivå som Function-mappen, det vill säga direkt under *wwwroot*.

`#load`Direktivet fungerar endast med *. CSX* -filer, inte med *. cs* -filer.

## <a name="binding-to-method-return-value"></a>Bindning till metod retur värde

Du kan använda ett metod retur värde för en utgående bindning genom att använda namnet `$return` i *function.jspå*. Exempel finns i [utlösare och bindningar](./functions-bindings-return-value.md).

Använd returvärdet endast om en lyckad funktions körning alltid resulterar i ett retur värde som skickas till utgående bindning. Annars använder `ICollector` eller `IAsyncCollector` , som du ser i följande avsnitt.

## <a name="writing-multiple-output-values"></a>Skriva flera värden för utdata

Om du vill skriva flera värden till en utgående bindning, eller om ett lyckat funktions anrop kanske inte leder till att något skickas till utgående bindning, använder [`ICollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) du [`IAsyncCollector`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) typerna eller. Dessa typer är skrivskyddade samlingar som skrivs till utgående bindning när metoden har slutförts.

I det här exemplet skrivs flera meddelanden i kön till samma kö med `ICollector` :

```csharp
public static void Run(ICollector<string> myQueue, ILogger log)
{
    myQueue.Add("Hello");
    myQueue.Add("World!");
}
```

## <a name="logging"></a>Loggning

Om du vill logga utdata till dina strömmande loggar i C# inkluderar du ett argument av typen [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger). Vi rekommenderar att du namnger det `log` . Undvik att använda `Console.Write` i Azure Functions.

```csharp
public static void Run(string myBlob, ILogger log)
{
    log.LogInformation($"C# Blob trigger function processed: {myBlob}");
}
```

> [!NOTE]
> Information om ett nyare loggnings ramverk som du kan använda i stället för finns i `TraceWriter` [ILogger](functions-dotnet-class-library.md#ilogger) -dokumentationen i .NET-klass bibliotekets utvecklings guide.

### <a name="custom-metrics-logging"></a>Anpassad mått loggning

Du kan använda `LogMetric` tilläggs metoden i om du `ILogger` vill skapa anpassade mått i Application Insights. Här är ett exempel på metod anrop:

```csharp
logger.LogMetric("TestMetric", 1234);
```

Den här koden är ett alternativ till att anropa `TrackMetric` med hjälp av Application Insights-API för .net.

## <a name="async"></a>Async

Om du vill göra en funktion [asynkron](/dotnet/csharp/programming-guide/concepts/async/)använder du `async` nyckelordet och returnerar ett `Task` objekt.

```csharp
public async static Task ProcessQueueMessageAsync(
        string blobName,
        Stream blobInput,
        Stream blobOutput)
{
    await blobInput.CopyToAsync(blobOutput, 4096);
}
```

Du kan inte använda `out` parametrar i async functions. För utgående bindningar använder du [funktionen returnera värde](#binding-to-method-return-value) eller ett [insamlat objekt](#writing-multiple-output-values) i stället.

## <a name="cancellation-tokens"></a>Token för avbrytande

En funktion kan ta emot en [CancellationToken](/dotnet/api/system.threading.cancellationtoken) -parameter som gör att operativ systemet kan meddela din kod när funktionen ska avslutas. Du kan använda det här meddelandet för att se till att funktionen inte avslutas plötsligt på ett sätt som lämnar data i ett inkonsekvent tillstånd.

I följande exempel visas hur du söker efter förestående funktions avslutning.

```csharp
using System;
using System.IO;
using System.Threading;

public static void Run(
    string inputText,
    TextWriter logger,
    CancellationToken token)
{
    for (int i = 0; i < 100; i++)
    {
        if (token.IsCancellationRequested)
        {
            logger.WriteLine("Function was cancelled at iteration {0}", i);
            break;
        }
        Thread.Sleep(5000);
        logger.WriteLine("Normal processing for queue message={0}", inputText);
    }
}
```

## <a name="importing-namespaces"></a>Importerar namn områden

Om du behöver importera namn områden kan du göra det som vanligt med- `using` satsen.

```csharp
using System.Net;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

Följande namn rymder importeras automatiskt och är därför valfria:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`

## <a name="referencing-external-assemblies"></a>Referera till externa sammansättningar

För Ramverks sammansättningar lägger du till referenser med hjälp av `#r "AssemblyName"` direktivet.

```csharp
#r "System.Web.Http"

using System.Net;
using System.Net.Http;
using System.Threading.Tasks;
using Microsoft.Extensions.Logging;

public static Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log)
```

Följande sammansättningar läggs automatiskt till av Azure Functions-värd miljön:

* `mscorlib`
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`

Följande sammansättningar kan refereras till av enkla namn (till exempel `#r "AssemblyName"` ):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNet.WebHooks.Common`
* `Microsoft.Azure.NotificationHubs`

## <a name="referencing-custom-assemblies"></a>Referera till anpassade sammansättningar

Om du vill referera till en anpassad sammansättning kan du antingen använda en *delad* sammansättning eller en *privat* sammansättning:

* Delade sammansättningar delas över alla funktioner i en Function-app. Om du vill referera till en anpassad sammansättning laddar du upp sammansättningen till en mapp som heter `bin` i mappen för [Function-rotmappen](functions-reference.md#folder-structure) (Wwwroot).

* Privata sammansättningar ingår i en specifik funktions kontext och stöder sid inläsning av olika versioner. Privata sammansättningar ska överföras i en `bin` mapp i funktions katalogen. Referera till sammansättningarna med hjälp av fil namnet, till exempel `#r "MyAssembly.dll"` .

Information om hur du överför filer till mappen funktion finns i avsnittet om [paket hantering](#using-nuget-packages).

### <a name="watched-directories"></a>Bevakade kataloger

Katalogen som innehåller funktions skript filen bevakas automatiskt för ändringar av sammansättningar. Om du vill se sammansättnings ändringar i andra kataloger lägger du till dem i `watchDirectories` listan i [host.jspå](functions-host-json.md).

## <a name="using-nuget-packages"></a>Använda NuGet-paket
Om du vill använda NuGet-paket i en 2. x-och senare C#-funktion laddar du upp en *Function. proj* -fil till funktionens mapp i funktions programmets fil system. Här är ett exempel på en *Function. proj* -fil som lägger till en referens till *Microsoft. ProjectOxford. Face* version *1.1.0*:

```xml
<Project Sdk="Microsoft.NET.Sdk">
    <PropertyGroup>
        <TargetFramework>netstandard2.0</TargetFramework>
    </PropertyGroup>

    <ItemGroup>
        <PackageReference Include="Microsoft.ProjectOxford.Face" Version="1.1.0" />
    </ItemGroup>
</Project>
```

Om du vill använda en anpassad NuGet-feed anger du flödet i en *Nuget.Config* -fil i Funktionsapp roten. Mer information finns i [Konfigurera NuGet-beteende](/nuget/consume-packages/configuring-nuget-behavior).

> [!NOTE]
> I 1. x C#-funktioner refereras NuGet-paket till med en *project.jspå* filen i stället för en *Function. proj* -fil.

Använd en *project.jspå* filen i stället för 1. x-funktioner. Här är ett exempel *project.jspå* en fil:

```json
{
  "frameworks": {
    "net46":{
      "dependencies": {
        "Microsoft.ProjectOxford.Face": "1.1.0"
      }
    }
   }
}
```

### <a name="using-a-functionproj-file"></a>Använda en function. PROJ-fil

1. Öppna funktionen i Azure Portal. På fliken loggar visas installations resultatet för paketet.
2. Om du vill ladda upp en *Function. proj* -fil använder du en av de metoder som beskrivs i avsnittet [så här uppdaterar du Function-appdata](functions-reference.md#fileupdate) i avsnittet Azure Functions Developer reference.
3. När *Function. proj* -filen har överförts visas utdata som i följande exempel i funktionens strömmande logg:

```
2018-12-14T22:00:48.658 [Information] Restoring packages.
2018-12-14T22:00:48.681 [Information] Starting packages restore
2018-12-14T22:00:57.064 [Information] Restoring packages for D:\local\Temp\9e814101-fe35-42aa-ada5-f8435253eb83\function.proj...
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\function.proj...
2018-12-14T22:01:00.844 [Information] Installing Newtonsoft.Json 10.0.2.
2018-12-14T22:01:01.041 [Information] Installing Microsoft.ProjectOxford.Common.DotNetStandard 1.0.0.
2018-12-14T22:01:01.140 [Information] Installing Microsoft.ProjectOxford.Face.DotNetStandard 1.0.0.
2018-12-14T22:01:09.799 [Information] Restore completed in 5.79 sec for D:\local\Temp\9e814101-fe35-42aa-ada5-f8435253eb83\function.proj.
2018-12-14T22:01:10.905 [Information] Packages restored.
```

## <a name="environment-variables"></a>Miljövariabler

Använd `System.Environment.GetEnvironmentVariable` , som du ser i följande kod exempel, för att hämta en miljö variabel eller ett inställnings värde för appar:

```csharp
public static void Run(TimerInfo myTimer, ILogger log)
{
    log.LogInformation($"C# Timer trigger function executed at: {DateTime.Now}");
    log.LogInformation(GetEnvironmentVariable("AzureWebJobsStorage"));
    log.LogInformation(GetEnvironmentVariable("WEBSITE_SITE_NAME"));
}

public static string GetEnvironmentVariable(string name)
{
    return name + ": " +
        System.Environment.GetEnvironmentVariable(name, EnvironmentVariableTarget.Process);
}
```

<a name="imperative-bindings"></a>

## <a name="binding-at-runtime"></a>Bindning vid körning

I C# och andra .NET-språk kan du använda ett [tvingande](https://en.wikipedia.org/wiki/Imperative_programming) bindnings mönster, till skillnad från [*deklarativ*](https://en.wikipedia.org/wiki/Declarative_programming) bindningar i *function.js*. Tvingande bindning är användbart när bindnings parametrar måste beräknas vid körning i stället för design tid. Med det här mönstret kan du binda till indata-och utgående bindningar som stöds i-farten i funktions koden.

Definiera en tvingande bindning enligt följande:

- Inkludera **inte** en post i *function.jspå* för önskade tvingande bindningar.
- Skicka in en indataparameter [`Binder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Host/Bindings/Runtime/Binder.cs) eller [`IBinder binder`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IBinder.cs) .
- Använd följande C#-mönster för att utföra data bindningen.

```cs
using (var output = await binder.BindAsync<T>(new BindingTypeAttribute(...)))
{
    ...
}
```

`BindingTypeAttribute` är .NET-attributet som definierar bindningen och `T` är en indata-eller utdatatyp som stöds av den bindnings typen. `T` kan inte vara en `out` parameter typ (till exempel `out JObject` ). Till exempel stöder Mobile Apps tabellens utgående bindning [sex typer av utdata](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs#L17-L22), men du kan bara använda [ICollector \<T> ](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/ICollector.cs) eller [`IAsyncCollector<T>`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/IAsyncCollector.cs) for `T` .

### <a name="single-attribute-example"></a>Exempel på ett attribut

Följande exempel kod skapar en [utgående bindning för Storage BLOB](functions-bindings-storage-blob-output.md) med en BLOB-sökväg som definieras vid körning och skriver sedan en sträng till blobben.

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    using (var writer = await binder.BindAsync<TextWriter>(new BlobAttribute("samples-output/path")))
    {
        writer.Write("Hello World!!");
    }
}
```

[BlobAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs) definierar [lagrings-BLOB](functions-bindings-storage-blob.md) -indata eller utdata-bindningen och [TextWriter](/dotnet/api/system.io.textwriter) är en typ av utgående bindning som stöds.

### <a name="multiple-attribute-example"></a>Exempel på flera attribut

I föregående exempel hämtas app-inställningen för funktions programmets huvud anslutnings sträng för lagrings konto (som är `AzureWebJobsStorage` ). Du kan ange en anpassad app-inställning som ska användas för lagrings kontot genom att lägga till [StorageAccountAttribute](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) och skicka attributhierarkin till `BindAsync<T>()` . Använd en `Binder` parameter, inte `IBinder` .  Exempel:

```cs
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host.Bindings.Runtime;

public static async Task Run(string input, Binder binder)
{
    var attributes = new Attribute[]
    {
        new BlobAttribute("samples-output/path"),
        new StorageAccountAttribute("MyStorageAccount")
    };

    using (var writer = await binder.BindAsync<TextWriter>(attributes))
    {
        writer.Write("Hello World!");
    }
}
```

I följande tabell visas .NET-attributen för varje bindnings typ och de paket som de är definierade i.

> [!div class="mx-codeBreakAll"]
> | Bindning | Attribut | Lägga till en referens |
> |------|------|------|
> | Cosmos DB | [`Microsoft.Azure.WebJobs.DocumentDBAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.CosmosDB/CosmosDBAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.CosmosDB"` |
> | Event Hubs | [`Microsoft.Azure.WebJobs.ServiceBus.EventHubAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/v2.x/src/Microsoft.Azure.WebJobs.ServiceBus/EventHubs/EventHubAttribute.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/b798412ad74ba97cf2d85487ae8479f277bdd85c/test/Microsoft.Azure.WebJobs.ServiceBus.UnitTests/ServiceBusAccountTests.cs) | `#r "Microsoft.Azure.Jobs.ServiceBus"` |
> | Mobile Apps | [`Microsoft.Azure.WebJobs.MobileTableAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.MobileApps/MobileTableAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.MobileApps"` |
> | Notification Hubs | [`Microsoft.Azure.WebJobs.NotificationHubAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/v2.x/src/WebJobs.Extensions.NotificationHubs/NotificationHubAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.NotificationHubs"` |
> | Service Bus | [`Microsoft.Azure.WebJobs.ServiceBusAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/b798412ad74ba97cf2d85487ae8479f277bdd85c/test/Microsoft.Azure.WebJobs.ServiceBus.UnitTests/ServiceBusAttributeTests.cs), [`Microsoft.Azure.WebJobs.ServiceBusAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/b798412ad74ba97cf2d85487ae8479f277bdd85c/test/Microsoft.Azure.WebJobs.ServiceBus.UnitTests/ServiceBusAccountTests.cs) | `#r "Microsoft.Azure.WebJobs.ServiceBus"` |
> | Lagringskö | [`Microsoft.Azure.WebJobs.QueueAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Lagringsblob | [`Microsoft.Azure.WebJobs.BlobAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs.Extensions.Storage/Blobs/BlobAttribute.cs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Lagrings tabell | [`Microsoft.Azure.WebJobs.TableAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs), [`Microsoft.Azure.WebJobs.StorageAccountAttribute`](https://github.com/Azure/azure-webjobs-sdk/blob/master/src/Microsoft.Azure.WebJobs/StorageAccountAttribute.cs) | |
> | Twilio | [`Microsoft.Azure.WebJobs.TwilioSmsAttribute`](https://github.com/Azure/azure-webjobs-sdk-extensions/blob/master/src/WebJobs.Extensions.Twilio/TwilioSMSAttribute.cs) | `#r "Microsoft.Azure.WebJobs.Extensions.Twilio"` |

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om utlösare och bindningar](functions-triggers-bindings.md)

> [!div class="nextstepaction"]
> [Läs mer om metod tips för Azure Functions](functions-best-practices.md)
