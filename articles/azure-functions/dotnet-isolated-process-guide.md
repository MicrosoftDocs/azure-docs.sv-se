---
title: .NET-isolerad process guide för .NET 5,0 i Azure Functions
description: Lär dig hur du använder en .NET-isolerad process för att köra dina C#-funktioner på .NET 5,0 i Azure.
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/01/2021
ms.custom: template-concept
ms.openlocfilehash: 4da685c247427e78297df1753779ee9b5c7866b8
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105023205"
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
+ Filen program. cs som är start punkten för appen.

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

När du använder de isolerade .NET-funktionerna har du åtkomst till start av din Function-app, som vanligt vis finns i program. cs. Du är ansvarig för att skapa och starta en egen värd instans. Därför har du också direkt åtkomst till konfigurations pipelinen för din app. När processen körs utanför processen kan du enkelt lägga till konfigurationer, mata in beroenden och köra egna mellanprogram. 

Följande kod visar ett exempel på en [HostBuilder] -pipeline:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_startup":::

En [HostBuilder] används för att skapa och returnera en fullständigt initierad [IHost] -instans, som du kör asynkront för att starta din Function-app. 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_host_run":::

### <a name="configuration"></a>Konfiguration

Metoden [ConfigureFunctionsWorkerDefaults] används för att lägga till de inställningar som krävs för att Function-appen ska köras utanför processen, vilket omfattar följande funktioner:

+ Standard uppsättning konverterare.
+ Ange standard [JsonSerializerOptions] för att ignorera Skift läge för egenskaps namn.
+ Integrera med Azure Functions loggning.
+ Utgående bindning mellanprogram och funktioner.
+ Mellanprogramvara för funktions körning.
+ Standard stöd för gRPC. 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_configure_defaults" :::   

När du har åtkomst till Host Builder-pipeline innebär det att du även kan ställa in appar-/regionsspecifika konfigurationer under initieringen. Du kan anropa metoden [ConfigureAppConfiguration] på [HostBuilder] en eller flera gånger för att lägga till de konfigurationer som krävs av din Function-app. Mer information om konfiguration av appar finns [i konfiguration i ASP.net Core](/aspnet/core/fundamentals/configuration/?view=aspnetcore-5.0&preserve-view=true). 

Dessa konfigurationer gäller för din Function-app som körs i en separat process. Om du vill göra ändringar i functions-värden eller Utlösar-och bindnings konfigurationen måste du fortfarande använda [host.jspå filen](functions-host-json.md).   

### <a name="dependency-injection"></a>Beroendeinmatning

Beroende inmatning är förenklad, jämfört med .NET-klass bibliotek. I stället för att skapa en start klass för att registrera tjänster, behöver du bara anropa [ConfigureServices] på värd Builder och använda tilläggs metoderna på [IServiceCollection] för att mata in vissa tjänster. 

I följande exempel matas ett singleton-tjänst beroende:  
 
:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_dependency_injection" :::

Läs mer i [beroende inmatning i ASP.net Core](/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-5.0&preserve-view=true).

### <a name="middleware"></a>Mellanprogram

.NET, isolerat stöder även mellanprogram registrering, igen med hjälp av en modell som liknar den som finns i ASP.NET. Den här modellen ger dig möjlighet att mata in logik i pipelinen för anrop, och innan och efter att funktionerna körs.

[ConfigureFunctionsWorkerDefaults] -tilläggs metoden har en överlagring som gör att du kan registrera dina egna mellanprogram, som du ser i följande exempel.  

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/CustomMiddleware/Program.cs" id="docsnippet_middleware_register" :::

Ett mer komplett exempel på hur du använder anpassade mellanprogram i din Function-app finns i det [anpassade exemplet för mellanprogram](https://github.com/Azure/azure-functions-dotnet-worker/blob/main/samples/CustomMiddleware).

## <a name="execution-context"></a>Körningskontext

.NET isolerat skickar ett [FunctionContext] -objekt till dina funktions metoder. Med det här objektet kan du få en [ILogger] -instans att skriva till loggarna genom att anropa metoden [GetLogger] och tillhandahålla en `categoryName` sträng. Mer information finns i [Logga](#logging). 

## <a name="bindings"></a>Bindningar 

Bindningar definieras genom att använda attribut för metoder, parametrar och retur typer. En funktions metod är en metod med ett `Function` attribut och ett Utlös ande attribut som tillämpas på en indataparameter, som visas i följande exempel:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Queue/QueueFunction.cs" id="docsnippet_queue_trigger" :::

Attributet trigger anger utlösarens typ och binder indata till en metod parameter. Föregående exempel funktion utlöses av ett köat meddelande och Queue-meddelandet skickas till-metoden i- `myQueueItem` parametern.

`Function`Attributet markerar metoden som en funktions start punkt. Namnet måste vara unikt inom ett projekt, börja med en bokstav och får bara innehålla bokstäver, siffror, `_` och `-` upp till 127 tecken. I Project-mallar skapas ofta en metod med namnet `Run` , men metod namnet kan vara ett giltigt C#-metod namn.

Eftersom de isolerade .NET-projekten körs i en separat arbets process kan bindningar inte dra nytta av omfattande bindnings klasser som `ICollector<T>` , `IAsyncCollector<T>` och `CloudBlockBlob` . Det finns inget direkt stöd för typer som ärvts från underliggande tjänst-SDK: er, till exempel [DocumentClient] och [BrokeredMessage]. I stället förlitar sig bindningar på strängar, matriser och serialiserbara typer, till exempel rena gamla klass objekt (POCOs). 

För HTTP-utlösare måste du använda [HttpRequestData] och [HttpResponseData] för att få åtkomst till begär ande-och svars data. Detta beror på att du inte har åtkomst till de ursprungliga HTTP-begärandena och svars objekt när processen körs utanför processen.

En fullständig uppsättning referens exempel för att använda utlösare och bindningar när processen körs utanför processen finns i [referens exemplet bindnings tillägg](https://github.com/Azure/azure-functions-dotnet-worker/blob/main/samples/Extensions). 

### <a name="input-bindings"></a>Indatabindningar

En funktion kan ha noll eller flera indata-bindningar som kan skicka data till en funktion. Som utlösare definieras indatatyper genom att använda ett binding-attribut för en indataparameter. När funktionen körs försöker körningen hämta data som anges i bindningen. De begärda data är ofta beroende av information som tillhandahålls av utlösaren med hjälp av bindnings parametrar.  

### <a name="output-bindings"></a>Utdatabindningar

Om du vill skriva till en utgående bindning måste du använda attributet utgående bindning till funktions metoden, som definierats för skrivning till den kopplade tjänsten. Värdet som returneras av metoden skrivs till utgående bindning. Följande exempel skriver till exempel ett sträng värde till en meddelandekö med namnet `functiontesting2` med hjälp av en utgående bindning:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Queue/QueueFunction.cs" id="docsnippet_queue_output_binding" :::

### <a name="multiple-output-bindings"></a>Flera utgående bindningar

Data som skrivs till en utgående bindning är alltid returvärdet för funktionen. Om du behöver skriva till fler än en utgående bindning måste du skapa en anpassad returtyp. Den här retur typen måste ha attributet utgående bindning tillämpat på en eller flera egenskaper för klassen. Följande exempel från en HTTP-utlösare skriver till både HTTP-svar och en kö-utgående bindning:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/MultiOutput/MultiOutput.cs" id="docsnippet_multiple_outputs":::

Svaret från en HTTP-utlösare betraktas alltid som utdata, så ett attribut för retur värde är inte obligatoriskt.

### <a name="http-trigger"></a>HTTP-utlösare

HTTP-utlösare översätter meddelandet inkommande HTTP-begäran till ett [HttpRequestData] -objekt som skickas till funktionen. Det här objektet tillhandahåller data från begäran, inklusive `Headers` ,,, `Cookies` `Identities` `URL` och valfritt meddelande `Body` . Det här objektet är en representation av HTTP Request-objektet och inte själva begäran. 

På samma sätt returnerar funktionen ett [HttpResponseData] -objekt, som tillhandahåller data som används för att skapa http-svaret, inklusive meddelande `StatusCode` , `Headers` och eventuellt ett meddelande `Body` .  

Följande kod är en HTTP-utlösare 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Http/HttpFunction.cs" id="docsnippet_http_trigger" :::

## <a name="logging"></a>Loggning

I .NET, kan du skriva till loggar med hjälp av en [ILogger] -instans som hämtats från ett [FunctionContext] -objekt som skickats till din funktion. Anropa metoden [GetLogger] och skicka ett sträng värde som är namnet på kategorin där loggarna skrivs. Kategorin är vanligt vis namnet på den speciella funktion som loggarna skrivs ifrån. Mer information om kategorier finns i [övervaknings artikeln](functions-monitoring.md#log-levels-and-categories). 

I följande exempel visas hur du hämtar en [ILogger] och skriver loggar i en funktion:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Http/HttpFunction.cs" id="docsnippet_logging" ::: 

Använd olika metoder för [ILogger] för att skriva olika logg nivåer, till exempel `LogWarning` eller `LogError` . Mer information om loggnings nivåer finns i [övervaknings artikeln](functions-monitoring.md#log-levels-and-categories).

En [ILogger] anges också när du använder [beroende inmatning](#dependency-injection).

## <a name="differences-with-net-class-library-functions"></a>Skillnader med funktioner i .NET-klass bibliotek

I det här avsnittet beskrivs det aktuella läget för funktionella och beteende skillnader som körs på .NET 5,0-processen utanför processen jämfört med .NET-klass biblioteks funktioner som körs i processen:

| Funktion/beteende |  I processen (.NET Core 3,1) | Utanför processen (.NET 5,0) |
| ---- | ---- | ---- |
| .NET-versioner | LTS (.NET Core 3,1) | Aktuell (.NET 5,0) |
| Kärn paket | [Microsoft. NET. SDK. Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) | [Microsoft. Azure. functions. Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)<br/>[Microsoft. Azure. functions. Worker. SDK](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk) | 
| Paket för bindnings tillägg | [Microsoft. Azure. WebJobs. Extensions. *](https://www.nuget.org/packages?q=Microsoft.Azure.WebJobs.Extensions)  | Under [Microsoft. Azure. functions. Worker. Extensions. *](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions) | 
| Loggning | [ILogger] skickades till funktionen | [ILogger] hämtades från [FunctionContext] |
| Token för avbrytande | [Stöds](functions-dotnet-class-library.md#cancellation-tokens) | Stöds inte |
| Utdatabindningar | Out-parametrar | Returvärden |
| Typer av utdatabindningar |  `IAsyncCollector`, [DocumentClient], [BrokeredMessage]och andra klient-/regionsspecifika typer | Enkla typer, JSON-serialiserbara typer och matriser. |
| Flera utgående bindningar | Stöds | [Stöds](#multiple-output-bindings) |
| HTTP-utlösare | [HttpRequest] / [ObjectResult] | [HttpRequestData] / [HttpResponseData] |
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


[HostBuilder]: /dotnet/api/microsoft.extensions.hosting.hostbuilder?view=dotnet-plat-ext-5.0&preserve-view=true
[IHost]: /dotnet/api/microsoft.extensions.hosting.ihost?view=dotnet-plat-ext-5.0&preserve-view=true
[ConfigureFunctionsWorkerDefaults]: /dotnet/api/microsoft.extensions.hosting.workerhostbuilderextensions.configurefunctionsworkerdefaults?view=azure-dotnet&preserve-view=true#Microsoft_Extensions_Hosting_WorkerHostBuilderExtensions_ConfigureFunctionsWorkerDefaults_Microsoft_Extensions_Hosting_IHostBuilder_
[ConfigureAppConfiguration]: /dotnet/api/microsoft.extensions.hosting.hostbuilder.configureappconfiguration?view=dotnet-plat-ext-5.0&preserve-view=true
[IServiceCollection]: /dotnet/api/microsoft.extensions.dependencyinjection.iservicecollection?view=dotnet-plat-ext-5.0&preserve-view=true
[ConfigureServices]: /dotnet/api/microsoft.extensions.hosting.hostbuilder.configureservices?view=dotnet-plat-ext-5.0&preserve-view=true
[FunctionContext]: /dotnet/api/microsoft.azure.functions.worker.functioncontext?view=azure-dotnet&preserve-view=true
[ILogger]: /dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true
[GetLogger]: /dotnet/api/microsoft.azure.functions.worker.functioncontextloggerextensions.getlogger?view=azure-dotnet&preserve-view=true
[DocumentClient]: /dotnet/api/microsoft.azure.documents.client.documentclient
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[HttpRequestData]: /dotnet/api/microsoft.azure.functions.worker.http.httprequestdata?view=azure-dotnet&preserve-view=true
[HttpResponseData]: /dotnet/api/microsoft.azure.functions.worker.http.httpresponsedata?view=azure-dotnet&preserve-view=true
[HttpRequest]: /dotnet/api/microsoft.aspnetcore.http.httprequest?view=aspnetcore-5.0&preserve-view=true
[ObjectResult]: /dotnet/api/microsoft.aspnetcore.mvc.objectresult?view=aspnetcore-5.0&preserve-view=true
[JsonSerializerOptions]: /api/system.text.json.jsonserializeroptions?view=net-5.0&preserve-view=true
