---
title: Köra Durable Functions som webbjobb – Azure
description: Lär dig hur du kodar och konfigurerar Durable Functions att köras i WebJobs med hjälp av WebJobs SDK.
ms.topic: conceptual
ms.date: 04/25/2018
ms.author: azfuncdf
ms.openlocfilehash: 826e475eb71563b52d687903aeac4ec936e267f6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96009508"
---
# <a name="how-to-run-durable-functions-as-webjobs"></a>Köra Durable Functions som WebJobs

Som standard använder Durable Functions Azure Functions körning till värdbaserade dirigeringar. Det kan dock finnas vissa scenarier där du behöver mer kontroll över koden som lyssnar efter händelser. Den här artikeln visar hur du implementerar ditt Orchestration med WebJobs SDK. Om du vill se en mer detaljerad jämförelse mellan Functions och WebJobs, se [jämföra funktioner och WebJobs](../functions-compare-logic-apps-ms-flow-webjobs.md#compare-functions-and-webjobs).

[Azure Functions](../functions-overview.md) och [Durable Functions](durable-functions-overview.md) tillägget bygger på [WebJobs SDK](../../app-service/webjobs-sdk-how-to.md). Jobb värden i WebJobs SDK är körning i Azure Functions. Om du behöver styra beteendet på sätt som inte är möjligt i Azure Functions kan du utveckla och köra Durable Functions genom att använda WebJobs SDK själv.

I version 3. x av WebJobs SDK är värden en implementering av `IHost` och i version 2. x använder du `JobHost` objektet.

Länknings Durable Functionss exemplet är tillgängligt i en WebJobs SDK 2. x-version: Hämta eller klona [Durable Functions-lagringsplatsen](https://github.com/azure/azure-functions-durable-extension/)och check *v1* -grenen och gå till mappen *samples \\ webjobssdk \\ Chaining* .

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du är bekant med grunderna i WebJobs SDK, klass biblioteks utveckling i C# för Azure Functions och Durable Functions. Om du behöver en introduktion till dessa ämnen, se följande resurser:

* [Kom igång med WebJobs SDK](../../app-service/webjobs-sdk-get-started.md)
* [Skapa din första funktion med Visual Studio](../functions-create-your-first-function-visual-studio.md)
* [Durable Functions](durable-functions-sequence.md)

För att slutföra stegen i den här artikeln:

* [Installera Visual Studio 2019](/visualstudio/install/) med arbets belastningen **Azure Development** .

  Om du redan har Visual Studio, men inte har den arbets belastningen, lägger du till arbets belastningen genom att välja **verktyg**  >  **Hämta verktyg och funktioner**.

  (Du kan använda [Visual Studio Code](https://code.visualstudio.com/) i stället, men vissa instruktioner är bara för Visual Studio.)

* Installera och kör [Azure Storage emulator](../../storage/common/storage-use-emulator.md) version 5,2 eller senare. Ett alternativ är att uppdatera *App.config* -filen med en Azure Storage anslutnings sträng.

## <a name="webjobs-sdk-versions"></a>WebJobs SDK-versioner

Den här artikeln förklarar hur du utvecklar ett WebJobs SDK 2. x-projekt (motsvarar Azure Functions version 1. x). Information om version 3. x finns i [WebJobs SDK 3. x](#webjobs-sdk-3x) längre fram i den här artikeln.

## <a name="create-a-console-app"></a>Skapa en konsolapp

Om du vill köra Durable Functions som WebJobs måste du först skapa en-konsol program. Ett WebJobs SDK-projekt är bara ett konsol program projekt med lämpliga NuGet-paket installerade.

I dialog rutan **nytt projekt** i Visual Studio väljer du **Windows klassisk Desktop**  >  **console-app (.NET Framework)**. I projekt filen `TargetFrameworkVersion` bör vara `v4.6.1` .

Visual Studio har också en projektmall för webbjobb, som du kan använda genom att välja **Cloud**  >  **Azure-webbjobb (.NET Framework)**. Den här mallen installerar många paket som du kanske inte behöver.

## <a name="install-nuget-packages"></a>Installera NuGet-paket

Du behöver NuGet-paket för WebJobs SDK, Core-bindningar, loggnings ramverk och det varaktiga aktivitets tillägget. Här är kommandon i **Package Manager-konsolen** för dessa paket, med de senaste stabila versions numren från och med datumet då artikeln skrevs:

```powershell
Install-Package Microsoft.Azure.WebJobs.Extensions -version 2.2.0
Install-Package Microsoft.Extensions.Logging -version 2.0.1
Install-Package Microsoft.Azure.WebJobs.Extensions.DurableTask -version 1.8.3
```

Du behöver också loggnings leverantörer. Följande kommandon installerar Azure Application Insights-providern och `ConfigurationManager` . Med `ConfigurationManager` kan du hämta Application Insights Instrumentation-nyckeln från appinställningar.

```powershell
Install-Package Microsoft.Azure.WebJobs.Logging.ApplicationInsights -version 2.2.0
Install-Package System.Configuration.ConfigurationManager -version 4.4.1
```

Följande kommando installerar-konsol leverantören:

```powershell
Install-Package Microsoft.Extensions.Logging.Console -version 2.0.1
```

## <a name="jobhost-code"></a>JobHost-kod

När du har skapat-konsol programmet och installerat de NuGet-paket som du behöver, är du redo att använda Durable Functions. Du gör det genom att använda JobHost-kod.

Om du vill använda Durable Functions-tillägget kan du anropa `UseDurableTask` `JobHostConfiguration` objektet i din `Main` metod:

```cs
var config = new JobHostConfiguration();
config.UseDurableTask(new DurableTaskExtension
{
    HubName = "MyTaskHub",
};
```

En lista över egenskaper som du kan ange i `DurableTaskExtension` objektet finns i [host.jspå](../functions-host-json.md#durabletask).

`Main`Metoden är också platsen där du ställer in loggnings leverantörer. I följande exempel konfigureras konsolen och Application Insights providrar.

```cs
static void Main(string[] args)
{
    using (var loggerFactory = new LoggerFactory())
    {
        var config = new JobHostConfiguration();

        config.DashboardConnectionString = "";

        var instrumentationKey =
            ConfigurationManager.AppSettings["APPINSIGHTS_INSTRUMENTATIONKEY"];

        config.LoggerFactory = loggerFactory
            .AddApplicationInsights(instrumentationKey, null)
            .AddConsole();

        config.UseTimers();
        config.UseDurableTask(new DurableTaskExtension
        {
            HubName = "MyTaskHub",
        });
        var host = new JobHost(config);
        host.RunAndBlock();
    }
}
```

## <a name="functions"></a>Functions

Durable Functions i samband med WebJobs skiljer sig något från Durable Functions i samband med Azure Functions. Det är viktigt att vara medveten om skillnaderna när du skriver koden.

WebJobs-SDK: n stöder inte följande Azure Functions funktioner:

* [FunctionName-attribut](#functionname-attribute)
* [HTTP-utlösare](#http-trigger)
* [API för Durable Functions HTTP-hantering](#http-management-api)

### <a name="functionname-attribute"></a>FunctionName-attribut

I ett WebJobs SDK-projekt är metod namnet för en funktion funktions namnet. `FunctionName`Attributet används endast i Azure Functions.

### <a name="http-trigger"></a>HTTP-utlösare

WebJobs-SDK: n har ingen HTTP-utlösare. Exempel projektets Orchestration-klient använder en timer-utlösare:

```cs
public static async Task CronJob(
    [TimerTrigger("0 */2 * * * *")] TimerInfo timer,
    [OrchestrationClient] DurableOrchestrationClient client,
    ILogger logger)
{
  ...
}
```

### <a name="http-management-api"></a>API för HTTP-hantering

Eftersom den inte har någon HTTP-utlösare har WebJobs [-SDK: n ingen http-hanterings-API](durable-functions-http-api.md).

I ett WebJobs SDK-projekt kan du anropa metoder för Orchestration-klientens objekt, i stället för genom att skicka HTTP-begäranden. Följande metoder motsvarar de tre uppgifter som du kan utföra med HTTP Management API:

* `GetStatusAsync`
* `RaiseEventAsync`
* `TerminateAsync`

Orchestration-klientens funktion i exempelprojektet startar Orchestrator-funktionen och hamnar sedan i en loop som anropar `GetStatusAsync` var 2: e sekund:

```cs
string instanceId = await client.StartNewAsync(nameof(HelloSequence), input: null);
logger.LogInformation($"Started new instance with ID = {instanceId}.");

DurableOrchestrationStatus status;
while (true)
{
    status = await client.GetStatusAsync(instanceId);
    logger.LogInformation($"Status: {status.RuntimeStatus}, Last update: {status.LastUpdatedTime}.");

    if (status.RuntimeStatus == OrchestrationRuntimeStatus.Completed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Failed ||
        status.RuntimeStatus == OrchestrationRuntimeStatus.Terminated)
    {
        break;
    }

    await Task.Delay(TimeSpan.FromSeconds(2));
}
```

## <a name="run-the-sample"></a>Kör exemplet

Du har Durable Functions konfigurerat för att köras som ett webb jobb, och du har nu en förståelse för hur detta skiljer sig från att köra Durable Functions som fristående Azure Functions. I det här läget kan det vara bra att se hur det fungerar i ett exempel.

Det här avsnittet innehåller en översikt över hur du kör [exempelprojektet](https://github.com/Azure/azure-functions-durable-extension/tree/v1/samples/webjobssdk/chaining). Detaljerade instruktioner som förklarar hur du kör ett WebJobs SDK-projekt lokalt och distribuerar det till ett Azure-webbjobb finns i [Kom igång med WebJobs SDK](../../app-service/webjobs-sdk-get-started.md#deploy-as-a-webjob).

### <a name="run-locally"></a>Lokal körning

1. Kontrol lera att Storage-emulatorn körs (se [krav](#prerequisites)).

1. Om du vill se loggar i Application Insights när du kör projektet lokalt:

    a. Skapa en Application Insights resurs och Använd den **allmänna** app-typen för den.

    b. Spara Instrumentation-nyckeln i *App.config* -filen.

1. Kör projektet.

### <a name="run-in-azure"></a>Kör i Azure

1. Skapa en webbapp och ett lagrings konto.

1. I webbappen sparar du anslutnings strängen för lagring i en app-inställning med namnet `AzureWebJobsStorage` .

1. Skapa en Application Insights resurs och Använd den **allmänna** app-typen för den.

1. Spara Instrumentation-nyckeln i en app-inställning med namnet `APPINSIGHTS_INSTRUMENTATIONKEY` .

1. Distribuera som ett webb jobb.

## <a name="webjobs-sdk-3x"></a>WebJobs SDK 3. x

Den här artikeln förklarar hur du utvecklar ett WebJobs SDK 2. x-projekt. Om du utvecklar ett [WebJobs SDK 3. x](../../app-service/webjobs-sdk-get-started.md) -projekt hjälper det här avsnittet dig att förstå skillnaderna.

Den huvudsakliga ändringen som introduceras är att använda .NET core i stället för .NET Framework. Om du vill skapa ett WebJobs SDK 3. x-projekt är instruktionerna samma, med följande undantag:

1. Skapa en .NET Core-konsolprogram. I dialog rutan **nytt projekt** i Visual Studio väljer du **.net Core**  >  **console app (.net Core)**. Projekt filen anger att `TargetFramework` är `netcoreapp2.x` .

1. Välj publicerings versionen WebJobs SDK 3. x av följande paket:

    * `Microsoft.Azure.WebJobs.Extensions`
    * `Microsoft.Azure.WebJobs.Extensions.Storage`
    * `Microsoft.Azure.WebJobs.Logging.ApplicationInsights`

1. Ange lagrings anslutnings strängen och Application Insights Instrumentation-nyckeln i en *appsettings.jspå* filen genom att använda .net Core Configuration Framework. Här är ett exempel:

    ```json
        {
            "AzureWebJobsStorage": "<replace with storage connection string>",
            "APPINSIGHTS_INSTRUMENTATIONKEY": "<replace with Application Insights instrumentation key>"
        }
    ```

1. Ändra `Main` metod koden för att göra detta. Här är ett exempel:

   ```cs
   static void Main(string[] args)
   {
        var hostBuilder = new HostBuilder()
            .ConfigureWebJobs(config =>
            {
                config.AddAzureStorageCoreServices();
                config.AddAzureStorage();
                config.AddTimers();
                config.AddDurableTask(options =>
                {
                    options.HubName = "MyTaskHub";
                    options.AzureStorageConnectionStringName = "AzureWebJobsStorage";
                });
            })
            .ConfigureLogging((context, logging) =>
            {
                logging.AddConsole();
                logging.AddApplicationInsights(config =>
                {
                    config.InstrumentationKey = context.Configuration["APPINSIGHTS_INSTRUMENTATIONKEY"];
                });
            })
            .UseConsoleLifetime();

        var host = hostBuilder.Build();

        using (host)
        {
            host.Run();
        }
   }
   ```

## <a name="next-steps"></a>Nästa steg

Mer information om WebJobs SDK finns i [så här använder du WebJobs SDK](../../app-service/webjobs-sdk-how-to.md).
