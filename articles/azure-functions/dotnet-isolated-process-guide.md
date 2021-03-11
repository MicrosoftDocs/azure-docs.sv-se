---
title: .NET-isolerad process guide för .NET 5,0 i Azure Functions
description: Lär dig hur du använder en .NET-isolerad process för att köra dina C#-funktioner på .NET 5,0 i Azure.
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/01/2021
ms.custom: template-concept
ms.openlocfilehash: ffdb146b26e83e1973c1d1bfee130eabfa09ea6a
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/10/2021
ms.locfileid: "102613960"
---
# <a name="guide-for-running-functions-on-net-50-in-azure"></a>Guide för att köra funktioner på .NET 5,0 i Azure

Den här artikeln är en introduktion till att använda C# för att utveckla isolerade process funktioner i .NET som körs utanför processen i Azure Functions. Genom att köra out-of-process kan du ta bort funktions koden från Azure Functions Runtime. Det gör det också möjligt för dig att skapa och köra funktioner som riktar sig mot den aktuella .NET 5,0-versionen. 

| Komma igång | Begrepp| Exempel |
|--|--|--| 
| <ul><li>[Använda Visual Studio Code](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-vscode)</li><li>[Använda kommando rads verktyg](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-cli)</li><li>[Använda Visual Studio](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-vs)</li></ul> | <ul><li>[Värdalternativ](functions-scale.md)</li><li>[Övervakning](functions-monitoring.md)</li> | <ul><li>[Referens exempel](https://github.com/Azure/azure-functions-dotnet-worker/tree/main/samples)</li></ul> |

Om du inte behöver stöd för .NET 5,0 eller köra funktionerna utanför processen kanske du vill [utveckla funktioner i C#-klass bibliotek](functions-dotnet-class-library.md).

## <a name="why-net-isolated-process"></a>Varför .NET-isolerad process?

Tidigare Azure Functions har endast stöd för ett tätt integrerat läge för .NET-funktioner som körs [som ett klass bibliotek](functions-dotnet-class-library.md) i samma process som värden. Det här läget ger en djup integrering mellan värd processen och funktionerna. Till exempel kan .NET-klass biblioteks funktioner dela bindnings-API: er och typer. Den här integrationen kräver dock också en tätt fungerande koppling mellan värd processen och .NET-funktionen. Exempelvis krävs .NET-funktioner som körs i processen för att köras på samma version av .NET som Functions-körningen. För att du ska kunna köra utanför dessa begränsningar kan du nu välja att köra i en isolerad process. Den här process isoleringen gör det också möjligt att utveckla funktioner som använder aktuella .NET-versioner (till exempel .NET 5,0), som inte stöds av Functions-körningen internt.

Eftersom dessa funktioner körs i en separat process finns det vissa [skillnader i funktionen och skillnaderna](#differences-with-net-class-library-functions) mellan funktionerna i .net, isolerade funktions appar och .NET-klass biblioteks funktioner i appar.

### <a name="benefits-of-running-out-of-process"></a>Fördelar med att köra utanför processen

När processen körs utanför processen kan dina .NET-funktioner dra nytta av följande fördelar: 

+ Färre konflikter: eftersom funktionerna körs i en separat process, kommer sammansättningar som används i din app inte att stå i konflikt med olika versioner av samma sammansättningar som används av värd processen.  
+ Fullständig kontroll av processen: du styr starten av appen och kan styra de konfigurationer som används och mellanprogram startades.
+ Beroende inmatning: eftersom du har full kontroll över processen kan du använda aktuella .NET-beteenden för beroende inmatning och införliva mellanprogram i din Function-app. 

## <a name="supported-versions"></a>Versioner som stöds

Den enda versionen av .NET som för närvarande stöds för att köra out-of-process är .NET 5,0.

## <a name="net-isolated-project"></a>.NET-isolerat projekt

Ett projekt med en isolerad .NET-funktion är i princip ett .NET-konsol program projekt som är mål för .NET 5,0. Följande är de grundläggande filerna som krävs i alla isolerade .NET-projekt:

+ [host.jspå](functions-host-json.md) fil.
+ [local.settings.jspå](functions-run-local.md#local-settings-file) fil.
+ C#-projekt fil (. CSPROJ) som definierar projektet och beroenden.
+ Program.cs-filen är start punkten för appen.

## <a name="package-references"></a>Paket referenser

När .NET-projektet körs utanför processen används en unik uppsättning paket som implementerar både Core-funktioner och bindnings tillägg. 

### <a name="core-packages"></a>Kärn paket 

Följande paket krävs för att köra dina .NET-funktioner i en isolerad process:

+ [Microsoft. Azure. functions. Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)
+ [Microsoft. Azure. functions. Worker. SDK](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk/)

### <a name="extension-packages"></a>Tilläggs paket

Eftersom funktioner som körs i en separat .NET-process använder olika bindnings typer, kräver de en unik uppsättning bindnings tilläggs paket. 

Du hittar dessa tilläggs paket under [Microsoft. Azure. functions. Worker. Extensions](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions).

## <a name="start-up-and-configuration"></a>Start och konfiguration 

När du använder de isolerade .NET-funktionerna har du åtkomst till start av din Function-app, som vanligt vis finns i Program.cs. Du är ansvarig för att skapa och starta en egen värd instans. Därför har du också direkt åtkomst till konfigurations pipelinen för din app. Du kan enkelt mata in beroenden och köra mellanprogram när du kör processen utanför processen. 

Följande kod visar ett exempel på en `HostBuilder` pipeline:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_startup":::

En `HostBuilder` används för att skapa och returnera en fullständigt initierad `IHost` instans, som du kör asynkront för att starta din Function-app. 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_host_run":::

### <a name="configuration"></a>Konfiguration

När du har åtkomst till Host Builder-pipeline innebär det att du kan ställa in alla appar som är speciella för initiering under initieringen. Dessa konfigurationer gäller för din Function-app som körs i en separat process. Om du vill göra ändringar i functions-värden eller Utlösar-och bindnings konfigurationen måste du fortfarande använda [host.jspå filen](functions-host-json.md).      

<!--The following example shows how to add configuration `args`, which are read as command-line arguments: 
 
:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_configure_app" :::

The `ConfigureAppConfiguration` method is used to configure the rest of the build process and application. This example also uses an [IConfigurationBuilder](/dotnet/api/microsoft.extensions.configuration.iconfigurationbuilder?view=dotnet-plat-ext-5.0&preserve-view=true), which makes it easier to add multiple configuration items. Because `ConfigureAppConfiguration` returns the same instance of [`IConfiguration`](/dotnet/api/microsoft.extensions.configuration.iconfiguration?view=dotnet-plat-ext-5.0&preserve-view=true), you can also just call it multiple times to add multiple configuration items.-->  
Du kan komma åt hela uppsättningen konfigurationer från både [`HostBuilderContext.Configuration`](/dotnet/api/microsoft.extensions.hosting.hostbuildercontext.configuration?view=dotnet-plat-ext-5.0&preserve-view=true) och [`IHost.Services`](/dotnet/api/microsoft.extensions.hosting.ihost.services?view=dotnet-plat-ext-5.0&preserve-view=true) .

Mer information om konfiguration finns [i konfiguration i ASP.net Core](/aspnet/core/fundamentals/configuration/?view=aspnetcore-5.0&preserve-view=true). 

### <a name="dependency-injection"></a>Beroendeinmatning

Beroende inmatning är förenklad, jämfört med .NET-klass bibliotek. I stället för att skapa en start klass för att registrera tjänster, behöver du bara anropa `ConfigureServices` verktyget Host Builder och använda tilläggs metoderna för [`IServiceCollection`](/dotnet/api/microsoft.extensions.dependencyinjection.iservicecollection?view=dotnet-plat-ext-5.0&preserve-view=true) att injicera vissa tjänster. 

I följande exempel matas ett singleton-tjänst beroende:  
 
:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_dependency_injection" :::

Läs mer i [beroende inmatning i ASP.net Core](/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-5.0&preserve-view=true).

<!--### Middleware

.NET isolated also supports middleware registration, again by using a model similar to what exists in ASP.NET. This model gives you the ability to inject logic into the invocation pipeline, and before and after functions execute.

While the full middleware registration set of APIs is not yet exposed, we do support middleware registration and have added an example to the sample application under the Middleware folder.

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_middleware" :::-->

## <a name="execution-context"></a>Körningskontext

.NET isolerat skickar ett `FunctionContext` objekt till funktions metoderna. Med det här objektet kan du få en [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) instans att skriva till loggarna genom att anropa- `GetLogger` metoden och ange en `categoryName` sträng. Mer information finns i [Logga](#logging). 

## <a name="bindings"></a>Bindningar 

Bindningar definieras genom att använda attribut för metoder, parametrar och retur typer. En funktions metod är en metod med `Function` attributet och ett utlösare som tillämpas på en indataparameter, som du ser i följande exempel:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/SampleApp/Queue/QueueFunction.cs" id="docsnippet_queue_trigger" :::

Attributet trigger anger utlösarens typ och binder indata till en metod parameter. Föregående exempel funktion utlöses av ett köat meddelande och Queue-meddelandet skickas till-metoden i- `myQueueItem` parametern.

`Function`Attributet markerar metoden som en funktions start punkt. Namnet måste vara unikt inom ett projekt, börja med en bokstav och får bara innehålla bokstäver, siffror, `_` och `-` upp till 127 tecken. I Project-mallar skapas ofta en metod med namnet `Run` , men metod namnet kan vara ett giltigt C#-metod namn.

Eftersom de isolerade .NET-projekten körs i en separat arbets process kan bindningar inte dra nytta av omfattande bindnings klasser som `ICollector<T>` , `IAsyncCollector<T>` och `CloudBlockBlob` . Det finns inget direkt stöd för typer som ärvts från underliggande tjänst-SDK: er, till exempel [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient) och [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage). I stället förlitar sig bindningar på strängar, matriser och serialiserbara typer, till exempel rena gamla klass objekt (POCOs). 

För HTTP-utlösare måste du använda `HttpRequestData` och `HttpResponseData` för att komma åt begär ande-och svars data. Detta beror på att du inte har åtkomst till de ursprungliga HTTP-begärandena och svars objekt när processen körs utanför processen. 

### <a name="input-bindings"></a>Indatabindningar

En funktion kan ha noll eller flera indata-bindningar som kan skicka data till en funktion. Som utlösare definieras indatatyper genom att använda ett binding-attribut för en indataparameter. När funktionen körs försöker körningen hämta data som anges i bindningen. De begärda data är ofta beroende av information som tillhandahålls av utlösaren med hjälp av bindnings parametrar.  

### <a name="output-bindings"></a>Utdatabindningar

Om du vill skriva till en utgående bindning måste du använda attributet utgående bindning till funktions metoden, som definierats för skrivning till den kopplade tjänsten. Värdet som returneras av metoden skrivs till utgående bindning. Följande exempel skriver till exempel ett sträng värde till en meddelandekö med namnet `functiontesting2` med hjälp av en utgående bindning:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/SampleApp/Queue/QueueFunction.cs" id="docsnippet_queue_output_binding" :::

### <a name="multiple-output-bindings"></a>Flera utgående bindningar

Data som skrivs till en utgående bindning är alltid returvärdet för funktionen. Om du behöver skriva till fler än en utgående bindning måste du skapa en anpassad returtyp. Den här retur typen måste ha attributet utgående bindning tillämpat på en eller flera egenskaper för klassen. I följande exempel skrivs både ett HTTP-svar och en utgående bindning för kö:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Function1/Function1.cs" id="docsnippet_multiple_outputs":::

### <a name="http-trigger"></a>HTTP-utlösare

HTTP-utlösare översätter meddelandet inkommande HTTP-begäran till ett `HttpRequestData` objekt som skickas till funktionen. Det här objektet tillhandahåller data från begäran, inklusive `Headers` ,,, `Cookies` `Identities` `URL` och valfritt meddelande `Body` . Det här objektet är en representation av HTTP Request-objektet och inte själva begäran. 

På samma sätt returnerar funktionen ett `HttpReponseData` objekt, som innehåller data som används för att skapa HTTP-svaret, inklusive meddelande `StatusCode` , `Headers` och eventuellt ett meddelande `Body` .  

Följande kod är en HTTP-utlösare 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/SampleApp/Http/HttpFunction.cs" id="docsnippet_http_trigger" :::

## <a name="logging"></a>Loggning

I .NET som är isolerade kan du skriva till loggar genom att använda en [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) instans som hämtats från ett `FunctionContext` objekt som skickats till din funktion. Anropa `GetLogger` metoden och skicka ett sträng värde som är namnet på kategorin där loggarna skrivs. Kategorin är vanligt vis namnet på den speciella funktion som loggarna skrivs ifrån. Mer information om kategorier finns i [övervaknings artikeln](functions-monitoring.md#log-levels-and-categories). 

I följande exempel visas hur du hämtar `ILogger` och skriver loggar i en funktion:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/SampleApp/Http/HttpFunction.cs" id="docsnippet_logging" ::: 

Använd olika metoder för `ILogger` att skriva olika logg nivåer, till exempel `LogWarning` eller `LogError` . Mer information om loggnings nivåer finns i [övervaknings artikeln](functions-monitoring.md#log-levels-and-categories).

En [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) ingår också när du använder [beroende inmatning](#dependency-injection).

## <a name="differences-with-net-class-library-functions"></a>Skillnader med funktioner i .NET-klass bibliotek

I det här avsnittet beskrivs det aktuella läget för funktionella och beteende skillnader som körs på .NET 5,0-processen utanför processen jämfört med .NET-klass biblioteks funktioner som körs i processen:

| Funktion/beteende |  I processen (.NET Core 3,1) | Utanför processen (.NET 5,0) |
| ---- | ---- | ---- |
| .NET-versioner | LTS (.NET Core 3,1) | Aktuell (.NET 5,0) |
| Kärn paket | [Microsoft. NET. SDK. Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) | [Microsoft. Azure. functions. Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)<br/>[Microsoft. Azure. functions. Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk) | 
| Paket för bindnings tillägg | [`Microsoft.Azure.WebJobs.Extensions.*`](https://www.nuget.org/packages?q=Microsoft.Azure.WebJobs.Extensions)  | Ramen [`Microsoft.Azure.Functions.Worker.Extensions.*`](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions) | 
| Loggning | [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) skickades till funktionen | [`ILogger`](/dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true) hämtas från `FunctionContext` |
| Token för avbrytande | [Stöds](functions-dotnet-class-library.md#cancellation-tokens) | Stöds inte |
| Utdatabindningar | Out-parametrar | Returvärden |
| Typer av utdatabindningar |  `IAsyncCollector`, [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet&preserve-view=true), [BrokeredMessage](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage?view=azure-dotnet&preserve-view=true)och andra klient-/regionsspecifika typer | Enkla typer, JSON-serialiserbara typer och matriser. |
| Flera utgående bindningar | Stöds | [Stöds](#multiple-output-bindings) |
| HTTP-utlösare | [`HttpRequest`](/dotnet/api/microsoft.aspnetcore.http.httprequest?view=aspnetcore-5.0&preserve-view=true)/[`ObjectResult`](/dotnet/api/microsoft.aspnetcore.mvc.objectresult?view=aspnetcore-5.0&preserve-view=true) | `HttpRequestData`/`HttpResponseData` |
| Bestående funktioner | [Stöds](durable/durable-functions-overview.md) | Stöds inte | 
| Tvingande bindningar | [Stöds](functions-dotnet-class-library.md#binding-at-runtime) | Stöds inte |
| function.jspå artefakt | Ges | Inte genererad |
| Konfiguration | [host.jspå](functions-host-json.md) | [host.jspå](functions-host-json.md) och anpassad initiering |
| Beroendeinmatning | [Stöds](functions-dotnet-dependency-injection.md)  | [Stöds](#dependency-injection) |
| Mellanprogram | Stöds inte | Stöds |
| Kall start tider | Vanligt | Längre, på grund av just-in-Time-start. Kör på Linux i stället för Windows för att minska eventuella fördröjningar. |
| ReadyToRun | [Stöds](functions-dotnet-class-library.md#readytorun) | _TBD_ |

## <a name="known-issues"></a>Kända problem

Information om hur du löser problem med att köra process funktioner som kör .NET, finns på [sidan kända problem](https://aka.ms/AAbh18e). [Skapa ett problem i den här GitHub-lagringsplatsen](https://github.com/Azure/azure-functions-dotnet-worker/issues/new/choose)för att rapportera problem.  

## <a name="next-steps"></a>Nästa steg

+ [Läs mer om utlösare och bindningar](functions-triggers-bindings.md)
+ [Läs mer om metod tips för Azure Functions](functions-best-practices.md)
