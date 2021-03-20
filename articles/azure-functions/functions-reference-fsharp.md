---
title: 'Azure Functions F # referens för utvecklare'
description: 'Lär dig hur du utvecklar Azure Functions med hjälp av F #-skript.'
author: sylvanc
ms.assetid: e60226e5-2630-41d7-9e5b-9f9e5acc8e50
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: syclebsc
ms.openlocfilehash: f9b7b92fd21e12f1d86c5d5878e48c6ec6b0e748
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "87088027"
---
# <a name="azure-functions-f-developer-reference"></a>Azure Functions F # referens för utvecklare

F # för Azure Functions är en lösning för att enkelt köra små delar av kod eller "Functions" i molnet. Data flödar in i din F #-funktion via funktions argument. Argument namn anges i `function.json` , och det finns fördefinierade namn för att komma åt saker som funktions loggar och avbrutna token. 

>[!IMPORTANT]
>F #-skript (. FSX) stöds endast av [version 1. x](functions-versions.md#creating-1x-apps) av Azure Functions Runtime. Om du vill använda F # med version 2. x och senare versioner av körnings miljön måste du använda en förkompilerad F # klass biblioteks projekt (. FS). Du kan skapa, hantera och publicera ett F # klass biblioteks projekt med Visual Studio som ett [C#-klass biblioteks projekt](functions-dotnet-class-library.md). Mer information om funktions versioner finns i [Översikt över Azure Functions körnings versioner](functions-versions.md).

Den här artikeln förutsätter att du redan har läst [Azure Functions Developer-referensen](functions-reference.md).

## <a name="how-fsx-works"></a>How. FSX fungerar
En `.fsx` fil är ett F #-skript. Det kan ses som ett F #-projekt som finns i en enda fil. Filen innehåller både koden för ditt program (i det här fallet din Azure-funktion) och direktiv för att hantera beroenden.

När du använder en `.fsx` för en Azure-funktion inkluderas ofta nödvändiga sammansättningar automatiskt åt dig, så att du kan fokusera på funktionen i stället för "formaterad kod".

## <a name="folder-structure"></a>Mappstruktur

Mappstrukturen för ett F # script-projekt ser ut så här:

```
FunctionsProject
 | - MyFirstFunction
 | | - run.fsx
 | | - function.json
 | | - function.proj
 | - MySecondFunction
 | | - run.fsx
 | | - function.json
 | | - function.proj
 | - host.json
 | - extensions.csproj
 | - bin
```

Det finns ett delat [host.jspå](functions-host-json.md) en fil som kan användas för att konfigurera Function-appen. Varje funktion har sin egen kod fil (. FSX) och bindnings konfigurations fil (function.jspå).

De bindnings tillägg som krävs i [version 2. x och senare versioner](functions-versions.md) av Functions-körningen definieras i `extensions.csproj` filen med de faktiska biblioteksfilerna i `bin` mappen. När du utvecklar lokalt måste du [Registrera bindnings tillägg](./functions-bindings-register.md#extension-bundles). När du utvecklar funktioner i Azure Portal görs registreringen åt dig.

## <a name="binding-to-arguments"></a>Bindning till argument
Varje bindning stöder vissa argument, enligt beskrivningen i [Azure Functions utlösare och bindningar för Developer-referens](functions-triggers-bindings.md). Ett av argumenten som binder en BLOB-utlösare stöder till exempel en POCO, som kan uttryckas med en F #-post. Exempel:

```fsharp
type Item = { Id: string }

let Run(blob: string, output: byref<Item>) =
    let item = { Id = "Some ID" }
    output <- item
```

Din F # Azure-funktion tar ett eller flera argument. När vi pratar om Azure Functions argument, refererar vi till argument för *indata* och *utdata* . Ett indataargument är precis vad det låter som: indatamängden för din F # Azure-funktion. Ett argument för *utdata* är föränderligt data eller ett `byref<>` argument som fungerar som ett sätt att skicka tillbaka  data från din funktion.

I exemplet ovan `blob` är ett indataargumentet och `output` är ett argument för utdata. Observera att vi använde `byref<>` för `output` (du behöver inte lägga till `[<Out>]` anteckningen). Om du använder en `byref<>` typ kan din funktion ändra vilken post eller vilket objekt argumentet refererar till.

När en F #-post används som indatatyp måste post definitionen markeras med för `[<CLIMutable>]` att Azure Functions Framework ska kunna ställa in fälten korrekt innan posten skickas till din funktion. Under huven `[<CLIMutable>]` skapas setter för post egenskaperna. Exempel:

```fsharp
[<CLIMutable>]
type TestObject =
    { SenderName : string
      Greeting : string }

let Run(req: TestObject, log: ILogger) =
    { req with Greeting = sprintf "Hello, %s" req.SenderName }
```

En F #-klass kan också användas för både in-och out-argument. För en klass behöver egenskaper vanligt vis get-och Set-inställningar. Exempel:

```fsharp
type Item() =
    member val Id = "" with get,set
    member val Text = "" with get,set

let Run(input: string, item: byref<Item>) =
    let result = Item(Id = input, Text = "Hello from F#!")
    item <- result
```

## <a name="logging"></a>Loggning
Om du vill logga utdata till dina [strömmande loggar](../app-service/troubleshoot-diagnostic-logs.md) i F # ska funktionen ha ett argument av typen [ILogger](/dotnet/api/microsoft.extensions.logging.ilogger). För konsekvens rekommenderar vi att argumentet heter `log` . Exempel:

```fsharp
let Run(blob: string, output: byref<string>, log: ILogger) =
    log.LogInformation(sprintf "F# Azure Function processed a blob: %s" blob)
    output <- input
```

## <a name="async"></a>Async
`async`Arbets flödet kan användas, men resultatet måste returnera ett `Task` . Detta kan göras med `Async.StartAsTask` , till exempel:

```fsharp
let Run(req: HttpRequestMessage) =
    async {
        return new HttpResponseMessage(HttpStatusCode.OK)
    } |> Async.StartAsTask
```

## <a name="cancellation-token"></a>Token för avbrytande
Om din funktion behöver hantera avstängning smidigt kan du ge den ett [`CancellationToken`](/dotnet/api/system.threading.cancellationtoken) argument. Detta kan kombineras med `async` , till exempel:

```fsharp
let Run(req: HttpRequestMessage, token: CancellationToken)
    let f = async {
        do! Async.Sleep(10)
        return new HttpResponseMessage(HttpStatusCode.OK)
    }
    Async.StartAsTask(f, token)
```

## <a name="importing-namespaces"></a>Importerar namn områden
Namn områden kan öppnas på vanligt sätt:

```fsharp
open System.Net
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

Följande namn områden öppnas automatiskt:

* `System`
* `System.Collections.Generic`
* `System.IO`
* `System.Linq`
* `System.Net.Http`
* `System.Threading.Tasks`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`.

## <a name="referencing-external-assemblies"></a>Referera till externa sammansättningar
På samma sätt kan Ramverks sammansättnings referenser läggas till med `#r "AssemblyName"` direktivet.

```fsharp
#r "System.Web.Http"

open System.Net
open System.Net.Http
open System.Threading.Tasks
open Microsoft.Extensions.Logging

let Run(req: HttpRequestMessage, log: ILogger) =
    ...
```

Följande sammansättningar läggs automatiskt till av Azure Functions-värd miljön:

* `mscorlib`,
* `System`
* `System.Core`
* `System.Xml`
* `System.Net.Http`
* `Microsoft.Azure.WebJobs`
* `Microsoft.Azure.WebJobs.Host`
* `Microsoft.Azure.WebJobs.Extensions`
* `System.Web.Http`
* `System.Net.Http.Formatting`.

Dessutom är följande sammansättningar särskilda bokstäver och kan refereras av simplename (t. ex. `#r "AssemblyName"` ):

* `Newtonsoft.Json`
* `Microsoft.WindowsAzure.Storage`
* `Microsoft.ServiceBus`
* `Microsoft.AspNet.WebHooks.Receivers`
* `Microsoft.AspNEt.WebHooks.Common`.

Om du behöver referera till en privat sammansättning kan du överföra sammansättnings filen till en `bin` mapp i förhållande till din funktion och referera till den med hjälp av fil namnet (t. ex.  `#r "MyAssembly.dll"`). Information om hur du överför filer till mappen funktion finns i följande avsnitt om paket hantering.

## <a name="editor-prelude"></a>Redigeraren inledning
En redigerare som stöder F # compiler-tjänster kommer inte att vara medveten om de namn områden och sammansättningar som Azure Functions automatiskt inkluderar. Det kan vara användbart att inkludera en inledning som hjälper redigeraren att hitta de sammansättningar som du använder och för att explicit öppna namn områden. Exempel:

```fsharp
#if !COMPILED
#I "../../bin/Binaries/WebJobs.Script.Host"
#r "Microsoft.Azure.WebJobs.Host.dll"
#endif

open System
open Microsoft.Azure.WebJobs.Host
open Microsoft.Extensions.Logging

let Run(blob: string, output: byref<string>, log: ILogger) =
    ...
```

När Azure Functions kör din kod bearbetar den källan med en `COMPILED` definierad, så redigeraren inledning ignoreras.

<a name="package"></a>

## <a name="package-management"></a>Pakethantering
Om du vill använda NuGet-paket i en F #-funktion lägger du till en `project.json` fil i funktionens mapp i funktions appens fil system. Här är en exempel `project.json` fil som lägger till en NuGet-paket referens till `Microsoft.ProjectOxford.Face` version 1.1.0:

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

Endast .NET Framework 4,6 stöds, så se till att `project.json` filen anges `net46` som visas här.

När du laddar upp en `project.json` fil hämtar kör tiden paketen och lägger automatiskt till referenser till paket sammansättningarna. Du behöver inte lägga till `#r "AssemblyName"` direktiv. Lägg bara till de nödvändiga `open` instruktionerna i `.fsx` filen.

Du kanske vill ange referenser till sammansättningar automatiskt i din redigerings inledning för att förbättra redigerings programmets interaktion med F # compile-tjänster.

### <a name="how-to-add-a-projectjson-file-to-your-azure-function"></a>Så här lägger du till en `project.json` fil i Azure-funktionen
1. Börja med att se till att din Function-App körs, vilket du kan göra genom att öppna din funktion i Azure Portal. Detta ger även åtkomst till strömmande loggar där paket installations utdata visas.
2. Om du vill överföra en `project.json` fil använder du en av de metoder som beskrivs i [så här uppdaterar du Function-AppData](functions-reference.md#fileupdate). Om du använder [kontinuerlig distribution för Azure Functions](functions-continuous-deployment.md)kan du lägga till en `project.json` fil i mellanlagringsplatsen för att experimentera med den innan du lägger till den i distributions grenen.
3. När `project.json` filen har lagts till visas utdata som liknar följande exempel i funktionens strömnings logg:

```
2016-04-04T19:02:48.745 Restoring packages.
2016-04-04T19:02:48.745 Starting NuGet restore
2016-04-04T19:02:50.183 MSBuild auto-detection: using msbuild version '14.0' from 'D:\Program Files (x86)\MSBuild\14.0\bin'.
2016-04-04T19:02:50.261 Feeds used:
2016-04-04T19:02:50.261 C:\DWASFiles\Sites\facavalfunctest\LocalAppData\NuGet\Cache
2016-04-04T19:02:50.261 https://api.nuget.org/v3/index.json
2016-04-04T19:02:50.261
2016-04-04T19:02:50.511 Restoring packages for D:\home\site\wwwroot\HttpTriggerCSharp1\Project.json...
2016-04-04T19:02:52.800 Installing Newtonsoft.Json 6.0.8.
2016-04-04T19:02:52.800 Installing Microsoft.ProjectOxford.Face 1.1.0.
2016-04-04T19:02:57.095 All packages are compatible with .NETFramework,Version=v4.6.
2016-04-04T19:02:57.189
2016-04-04T19:02:57.189
2016-04-04T19:02:57.455 Packages restored.
```

## <a name="environment-variables"></a>Miljövariabler
Om du vill hämta en miljö variabel eller ett inställnings värde `System.Environment.GetEnvironmentVariable` för appen använder du till exempel:

```fsharp
open System.Environment
open Microsoft.Extensions.Logging

let Run(timer: TimerInfo, log: ILogger) =
    log.LogInformation("Storage = " + GetEnvironmentVariable("AzureWebJobsStorage"))
    log.LogInformation("Site = " + GetEnvironmentVariable("WEBSITE_SITE_NAME"))
```

## <a name="reusing-fsx-code"></a>Återanvänder. FSX-kod
Du kan använda kod från andra `.fsx` filer med hjälp av ett `#load` direktiv. Exempel:

`run.fsx`

```fsharp
#load "logger.fsx"

let Run(timer: TimerInfo, log: ILogger) =
    mylog log (sprintf "Timer: %s" DateTime.Now.ToString())
```

`logger.fsx`

```fsharp
let mylog(log: ILogger, text: string) =
    log.LogInformation(text);
```

Sökvägar till `#load` direktivet är relativa till `.fsx` filens plats.

* `#load "logger.fsx"` läser in en fil som finns i Function-mappen.
* `#load "package\logger.fsx"` läser in en fil som finns i `package` mappen i Function-mappen.
* `#load "..\shared\mylogger.fsx"` läser in en fil som finns i `shared` mappen på samma nivå som Function-mappen, det vill säga direkt under `wwwroot` .

`#load`Direktivet fungerar endast med `.fsx` (F # skript) filer och inte med `.fs` filer.

## <a name="next-steps"></a>Nästa steg
Mer information finns i följande resurser:

* [F#-guide](/dotnet/articles/fsharp/index)
* [Metod tips för Azure Functions](functions-best-practices.md)
* [Azure Functions, info för utvecklare](functions-reference.md)
* [Azure Functions utlösare och bindningar](functions-triggers-bindings.md)
* [Azure Functions testning](functions-test-a-function.md)
* [Azure Functions skalning](functions-scale.md)
