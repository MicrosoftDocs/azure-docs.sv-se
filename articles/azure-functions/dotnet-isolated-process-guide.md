---
title: .NET-isolerad processguide för .NET 5.0 i Azure Functions
description: Lär dig hur du använder en .NET-isolerad process för att köra dina C#-funktioner på .NET 5.0 out-of-process i Azure.
ms.service: azure-functions
ms.topic: conceptual
ms.date: 03/01/2021
ms.custom: template-concept
ms.openlocfilehash: 53f3c79886d26b20a584d747759176ea842741cf
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739286"
---
# <a name="guide-for-running-functions-on-net-50-in-azure"></a>Guide för att köra funktioner på .NET 5.0 i Azure

Den här artikeln är en introduktion till att använda C# för att utveckla .NET-isolerade processfunktioner som tar slut i processen i Azure Functions. När processen tar slut kan du frikoppla funktionskoden från den Azure Functions körningen. Det är också ett sätt för dig att skapa och köra funktioner som riktar sig mot den aktuella .NET 5.0-versionen. 

| Komma igång | Begrepp| Exempel |
|--|--|--| 
| <ul><li>[Använda Visual Studio Code](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-vscode)</li><li>[Använda kommandoradsverktyg](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-cli)</li><li>[Använda Visual Studio](dotnet-isolated-process-developer-howtos.md?pivots=development-environment-vs)</li></ul> | <ul><li>[Värdalternativ](functions-scale.md)</li><li>[Övervakning](functions-monitoring.md)</li> | <ul><li>[Referensexempel](https://github.com/Azure/azure-functions-dotnet-worker/tree/main/samples)</li></ul> |

Om du inte behöver stödja .NET 5.0 eller köra dina funktioner utanför processen kan du i stället utveckla [C#-klassbiblioteksfunktioner](functions-dotnet-class-library.md).

## <a name="why-net-isolated-process"></a>Varför är .NET isolerad process?

Tidigare Azure Functions endast stöd för ett nära integrerat läge för [](functions-dotnet-class-library.md) .NET-funktioner, som körs som ett klassbibliotek i samma process som värden. Det här läget ger djupgående integrering mellan värdprocessen och funktionerna. Till exempel kan .NET-klassbiblioteksfunktioner dela bindnings-API:er och -typer. Den här integreringen kräver dock även en striktare koppling mellan värdprocessen och .NET-funktionen. Till exempel krävs .NET-funktioner som körs i processen för att köras på samma version av .NET som Functions-körningen. Om du vill köra utanför dessa begränsningar kan du nu välja att köra i en isolerad process. Med den här processisoleringen kan du även utveckla funktioner som använder aktuella .NET-versioner (till exempel .NET 5.0), som inte stöds av Functions-körningen.

Eftersom dessa funktioner körs i en [](#differences-with-net-class-library-functions) separat process finns det vissa funktionsskillnader mellan .NET-isolerade funktionsappar och .NET-klassbiblioteksfunktionsappar.

### <a name="benefits-of-running-out-of-process"></a>Fördelar med att processen tar slut

När processen tar slut kan dina .NET-funktioner dra nytta av följande fördelar: 

+ Färre konflikter: eftersom funktionerna körs i en separat process står sammansättningar som används i din app inte i konflikt med olika versioner av samma sammansättningar som används av värdprocessen.  
+ Fullständig kontroll över processen: du styr start av appen och kan styra de konfigurationer som används och mellanprogrammet startas.
+ Beroendeinjektion: Eftersom du har fullständig kontroll över processen kan du använda aktuella .NET-beteenden för beroendeinjektion och införliva mellanprogram i funktionsappen. 

## <a name="supported-versions"></a>Versioner som stöds

Den enda versionen av .NET som för närvarande stöds för att få slut på process är .NET 5.0.

## <a name="net-isolated-project"></a>.NET-isolerat projekt

Ett .NET isolerat funktionsprojekt är i princip ett .NET-konsolappprojekt som riktar sig mot .NET 5.0. Följande är de grundläggande filer som krävs i ett isolerat .NET-projekt:

+ [host.jspå](functions-host-json.md) filen.
+ [local.settings.jspå](functions-run-local.md#local-settings-file) filen.
+ C#-projektfil (.csproj) som definierar projektet och beroenden.
+ Program.cs-fil som är startpunkten för appen.

## <a name="package-references"></a>Paketreferenser

När processen tar slut använder .NET-projektet en unik uppsättning paket som implementerar både kärnfunktioner och bindningstillägg. 

### <a name="core-packages"></a>Kärnpaket 

Följande paket krävs för att köra dina .NET-funktioner i en isolerad process:

+ [Microsoft.Azure.Functions.Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)
+ [Microsoft.Azure.Functions.Worker.Sdk](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk/)

### <a name="extension-packages"></a>Tilläggspaket

Eftersom funktioner som körs i en .NET-isolerad process använder olika bindningstyper kräver de en unik uppsättning bindningstilläggspaket. 

Du hittar dessa tilläggspaket under [Microsoft.Azure.Functions.Worker.Extensions](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions).

## <a name="start-up-and-configuration"></a>Start och konfiguration 

När du använder isolerade .NET-funktioner har du åtkomst till start av funktionsappen, som vanligtvis finns i Program.cs. Du ansvarar för att skapa och starta en egen värdinstans. Därför har du också direkt åtkomst till konfigurationspipelinen för din app. När processen tar slut kan du mycket enklare lägga till konfigurationer, mata in beroenden och köra ditt eget mellanprogram. 

Följande kod visar ett exempel på en [HostBuilder-pipeline:]

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_startup":::

Den här koden kräver `using Microsoft.Extensions.DependencyInjection;` . 

En [HostBuilder används] för att skapa och returnera en fullständigt initierad [IHost-instans] som du kör asynkront för att starta funktionsappen. 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_host_run":::

### <a name="configuration"></a>Konfiguration

Metoden [ConfigureFunctionsWorkerDefaults] används för att lägga till de inställningar som krävs för att funktionsappen ska ta slut, vilket omfattar följande funktioner:

+ Standarduppsättning med konverterare.
+ Ange [JsonSerializerOptions som standard för att] ignorera höljet för egenskapsnamn.
+ Integrera med Azure Functions loggning.
+ Mellanprogram och funktioner för utdatabindning.
+ Mellanprogram för funktionskörning.
+ Standardstöd för gRPC. 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_configure_defaults" :::   

Om du har åtkomst till pipelinen för värdbyggaren kan du även ange appspecifika konfigurationer under initieringen. Du kan anropa [metoden ConfigureAppConfiguration] på [HostBuilder] en eller flera gånger för att lägga till de konfigurationer som krävs av funktionsappen. Mer information om appkonfiguration finns i [Konfiguration i ASP.NET Core](/aspnet/core/fundamentals/configuration/?view=aspnetcore-5.0&preserve-view=true). 

Dessa konfigurationer gäller för funktionsappen som körs i en separat process. Om du vill göra ändringar i functions-värden eller utlösaren och bindningskonfigurationen måste du fortfarande [ användahost.jspå filen](functions-host-json.md).   

### <a name="dependency-injection"></a>Beroendeinmatning

Beroendeinjektion är förenklad jämfört med .NET-klassbibliotek. I stället för att behöva skapa en startklass för att registrera tjänster behöver du bara anropa [ConfigureServices] på värdbyggaren och använda tilläggsmetoderna i [IServiceCollection] för att mata in specifika tjänster. 

I följande exempel matas ett singleton-tjänstberoende in:  
 
:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/FunctionApp/Program.cs" id="docsnippet_dependency_injection" :::

Den här koden kräver `using Microsoft.Extensions.DependencyInjection;` . Mer information finns i Dependency [injection in ASP.NET Core](/aspnet/core/fundamentals/dependency-injection?view=aspnetcore-5.0&preserve-view=true).

### <a name="middleware"></a>Mellanprogram

.NET isolerad stöder även registrering av mellanprogram, återigen genom att använda en modell som liknar den som finns i ASP.NET. Den här modellen ger dig möjlighet att mata in logik i anropspipelinen och före och efter att funktioner körs.

Tilläggsmetoden [ConfigureFunctionsWorkerDefaults] har en överlagring där du kan registrera ditt eget mellanprogram, som du kan se i följande exempel.  

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/CustomMiddleware/Program.cs" id="docsnippet_middleware_register" :::

Ett mer komplett exempel på hur du använder anpassade mellanprogram i funktionsappen finns i referensexempel [för anpassade mellanprogram.](https://github.com/Azure/azure-functions-dotnet-worker/blob/main/samples/CustomMiddleware)

## <a name="execution-context"></a>Körningskontext

.NET isolerad skickar ett [FunctionContext-objekt] till dina funktionsmetoder. Med det här objektet kan du få [en ILogger-instans] att skriva till loggarna genom att anropa [metoden GetLogger] och ange en `categoryName` sträng. Mer information finns i [Logga](#logging). 

## <a name="bindings"></a>Bindningar 

Bindningar definieras med hjälp av attribut för metoder, parametrar och returtyper. En funktionsmetod är en metod med ett `Function` attribut och ett utlösarattribut som tillämpas på en indataparameter, som du ser i följande exempel:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Queue/QueueFunction.cs" id="docsnippet_queue_trigger" :::

Utlösarattributet anger utlösartypen och binder indata till en metodparameter. Föregående exempelfunktion utlöses av ett kömeddelande och kömeddelandet skickas till metoden i `myQueueItem` parametern .

Attributet `Function` markerar metoden som en startpunkt för funktionen. Namnet måste vara unikt inom ett projekt, börja med en bokstav och får bara innehålla bokstäver, siffror, och , med en längd på upp till `_` `-` 127 tecken. Projektmallar skapar ofta en metod med namnet `Run` , men metodnamnet kan vara val annat giltigt C#-metodnamn.

Eftersom .NET-isolerade projekt körs i en separat arbetsprocess kan bindningar inte dra nytta av omfattande bindningsklasser, till exempel `ICollector<T>` , `IAsyncCollector<T>` och `CloudBlockBlob` . Det finns heller inget direkt stöd för typer som ärvts från underliggande tjänst-SDK:er, till exempel [DocumentClient] och [BrokeredMessage.] I stället förlitar sig bindningar på strängar, matriser och serialiserbara typer, till exempel vanliga gamla klassobjekt (POPO:er). 

För HTTP-utlösare måste du använda [HttpRequestData och] [HttpResponseData för] att få åtkomst till begärande- och svarsdata. Det beror på att du inte har åtkomst till de ursprungliga HTTP-begärande- och svarsobjekten när processen tar slut.

En fullständig uppsättning referensexempel för att använda utlösare och bindningar när processen tar slut finns i referensexempel [för bindningstillägg.](https://github.com/Azure/azure-functions-dotnet-worker/blob/main/samples/Extensions) 

### <a name="input-bindings"></a>Indatabindningar

En funktion kan ha noll eller flera indatabindningar som kan skicka data till en funktion. Precis som utlösare definieras indatabindningar genom att ett bindningsattribut tillämpas på en indataparameter. När funktionen körs försöker körningen hämta data som anges i bindningen. De data som begärs är ofta beroende av information som tillhandahålls av utlösaren med hjälp av bindningsparametrar.  

### <a name="output-bindings"></a>Utdatabindningar

Om du vill skriva till en utdatabindning måste du använda ett utdatabindningsattribut för funktionsmetoden, som definierade hur du skriver till den bundna tjänsten. Värdet som returneras av metoden skrivs till utdatabindningen. Följande exempel skriver till exempel ett strängvärde till en meddelandekö med namnet med `functiontesting2` hjälp av en utdatabindning:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Queue/QueueFunction.cs" id="docsnippet_queue_output_binding" :::

### <a name="multiple-output-bindings"></a>Flera utdatabindningar

Data som skrivs till en utdatabindning är alltid funktionens returvärde. Om du behöver skriva till mer än en utdatabindning måste du skapa en anpassad returtyp. Returtypen måste ha utdatabindningsattributet tillämpat på en eller flera egenskaper för klassen. Följande exempel från en HTTP-utlösare skriver till både HTTP-svaret och en köutdatabindning:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/MultiOutput/MultiOutput.cs" id="docsnippet_multiple_outputs":::

Svaret från en HTTP-utlösare betraktas alltid som utdata, så ett returvärdesattribut krävs inte.

### <a name="http-trigger"></a>HTTP-utlösare

HTTP-utlösare översätter det inkommande HTTP-begärandemeddelandet till ett [HttpRequestData-objekt] som skickas till funktionen. Det här objektet tillhandahåller data från begäran, inklusive `Headers` , , , och `Cookies` `Identities` `URL` valfritt ett meddelande `Body` . Det här objektet är en representation av HTTP-begärandeobjektet och inte själva begäran. 

På samma sätt returnerar funktionen ett [HttpResponseData-objekt] som tillhandahåller data som används för att skapa HTTP-svaret, inklusive meddelandet `StatusCode` , och `Headers` eventuellt ett meddelande `Body` .  

Följande kod är en HTTP-utlösare 

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Http/HttpFunction.cs" id="docsnippet_http_trigger" :::

## <a name="logging"></a>Loggning

I .NET isolerad kan du skriva till loggar med hjälp av [en ILogger-instans som] hämtas från ett [FunctionContext-objekt] som skickas till din funktion. Anropa [metoden GetLogger] och skicka ett strängvärde som är namnet på den kategori där loggarna skrivs. Kategorin är vanligtvis namnet på den specifika funktion som loggarna skrivs från. Mer information om kategorier finns i [övervakningsartikeln](functions-monitoring.md#log-levels-and-categories). 

I följande exempel visas hur du hämtar en [ILogger] och skriver loggar i en funktion:

:::code language="csharp" source="~/azure-functions-dotnet-worker/samples/Extensions/Http/HttpFunction.cs" id="docsnippet_logging" ::: 

Använd olika metoder för [ILogger för] att skriva olika loggnivåer, till exempel `LogWarning` eller `LogError` . Mer information om loggnivåer finns i [övervakningsartikeln](functions-monitoring.md#log-levels-and-categories).

En [ILogger tillhandahålls] också när du använder [beroendeinjektion](#dependency-injection).

## <a name="differences-with-net-class-library-functions"></a>Skillnader med .NET-klassbiblioteksfunktioner

I det här avsnittet beskrivs det aktuella tillståndet för funktionella och beteendemässiga skillnader som körs på .NET 5.0 utanför processen jämfört med .NET-klassbiblioteksfunktioner som körs i processen:

| Funktion/beteende |  Pågår (.NET Core 3.1) | Out-of-process (.NET 5.0) |
| ---- | ---- | ---- |
| .NET-versioner | LTS (.NET Core 3.1) | Aktuell (.NET 5.0) |
| Kärnpaket | [Microsoft .NET.Sdk.Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) | [Microsoft.Azure.Functions.Worker](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker/)<br/>[Microsoft.Azure.Functions.Worker.Sdk](https://www.nuget.org/packages/Microsoft.Azure.Functions.Worker.Sdk) | 
| Paket för bindningstillägg | [Microsoft.Azure.WebJobs.Extensions.*](https://www.nuget.org/packages?q=Microsoft.Azure.WebJobs.Extensions)  | Under [Microsoft.Azure.Functions.Worker.Extensions.*](https://www.nuget.org/packages?q=Microsoft.Azure.Functions.Worker.Extensions) | 
| Loggning | [ILogger] skickades till funktionen | [ILogger som] hämtas från [FunctionContext] |
| Annulleringstoken | [Stöds](functions-dotnet-class-library.md#cancellation-tokens) | Stöds inte |
| Utdatabindningar | Out-parametrar | Returvärden |
| Typer av utdatabindningar |  `IAsyncCollector`, [DocumentClient,] [BrokeredMessage]och andra klientspecifika typer | Enkla typer, JSON-serialiserbara typer och matriser. |
| Flera utdatabindningar | Stöds | [Stöds](#multiple-output-bindings) |
| HTTP-utlösare | [HttpRequest] / [ObjectResult] | [HttpRequestData] / [HttpResponseData] |
| Bestående funktioner | [Stöds](durable/durable-functions-overview.md) | Stöds inte | 
| Imperativ bindning | [Stöds](functions-dotnet-class-library.md#binding-at-runtime) | Stöds inte |
| function.jspå artefakt | Genereras | Genereras inte |
| Konfiguration | [host.jspå](functions-host-json.md) | [host.jspå](functions-host-json.md) och anpassad initiering |
| Beroendeinmatning | [Stöds](functions-dotnet-dependency-injection.md)  | [Stöds](#dependency-injection) |
| Mellanprogram | Stöds inte | Stöds |
| Kallstartstider | Typiska | Längre på grund av just-in-time-start. Kör i Linux i stället för Windows för att minska potentiella fördröjningar. |
| ReadyToRun | [Stöds](functions-dotnet-class-library.md#readytorun) | _Tbd_ |

## <a name="known-issues"></a>Kända problem

Information om lösningar för att få information om problem med att köra .NET-isolerade processfunktioner finns på [den här sidan med kända problem.](https://aka.ms/AAbh18e) Om du vill rapportera problem skapar [du ett problem på den här GitHub-lagringsplatsen.](https://github.com/Azure/azure-functions-dotnet-worker/issues/new/choose)  

## <a name="next-steps"></a>Nästa steg

+ [Läs mer om utlösare och bindningar](functions-triggers-bindings.md)
+ [Läs mer om metodtips för Azure Functions](functions-best-practices.md)


[HostBuilder]: /dotnet/api/microsoft.extensions.hosting.hostbuilder?view=dotnet-plat-ext-5.0&preserve-view=true
[IHost]: /dotnet/api/microsoft.extensions.hosting.ihost?view=dotnet-plat-ext-5.0&preserve-view=true
[ConfigureFunctionsWorkerDefaults]: /dotnet/api/microsoft.extensions.hosting.workerhostbuilderextensions.configurefunctionsworkerdefaults?view=azure-dotnet&preserve-view=true#Microsoft_Extensions_Hosting_WorkerHostBuilderExtensions_ConfigureFunctionsWorkerDefaults_Microsoft_Extensions_Hosting_IHostBuilder_
[KonfigureraAppConfiguration]: /dotnet/api/microsoft.extensions.hosting.hostbuilder.configureappconfiguration?view=dotnet-plat-ext-5.0&preserve-view=true
[IServiceCollection]: /dotnet/api/microsoft.extensions.dependencyinjection.iservicecollection?view=dotnet-plat-ext-5.0&preserve-view=true
[Konfigureratjänster]: /dotnet/api/microsoft.extensions.hosting.hostbuilder.configureservices?view=dotnet-plat-ext-5.0&preserve-view=true
[FunctionContext]: /dotnet/api/microsoft.azure.functions.worker.functioncontext?view=azure-dotnet&preserve-view=true
[ILogger]: /dotnet/api/microsoft.extensions.logging.ilogger?view=dotnet-plat-ext-5.0&preserve-view=true
[GetLogger]: /dotnet/api/microsoft.azure.functions.worker.functioncontextloggerextensions.getlogger?view=azure-dotnet&preserve-view=true
[DocumentClient]: /dotnet/api/microsoft.azure.documents.client.documentclient
[BrokeredMessage]: /dotnet/api/microsoft.servicebus.messaging.brokeredmessage
[HttpRequestData]: /dotnet/api/microsoft.azure.functions.worker.http.httprequestdata?view=azure-dotnet&preserve-view=true
[HttpResponseData]: /dotnet/api/microsoft.azure.functions.worker.http.httpresponsedata?view=azure-dotnet&preserve-view=true
[HttpRequest]: /dotnet/api/microsoft.aspnetcore.http.httprequest?view=aspnetcore-5.0&preserve-view=true
[ObjectResult]: /dotnet/api/microsoft.aspnetcore.mvc.objectresult?view=aspnetcore-5.0&preserve-view=true
[JsonSerializerOptions]: /dotnet/api/system.text.json.jsonserializeroptions?view=net-5.0&preserve-view=true
