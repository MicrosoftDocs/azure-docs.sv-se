---
title: Använda beroendeinmatning i .NET Azure Functions
description: Lär dig hur du använder beroende inmatning för att registrera och använda tjänster i .NET Functions
author: ggailey777
ms.topic: conceptual
ms.custom: devx-track-csharp
ms.date: 08/15/2020
ms.author: glenga
ms.reviewer: jehollan
ms.openlocfilehash: 70ec9248db002823e969fa5f4fba8bf1074a9af7
ms.sourcegitcommit: 0830e02635d2f240aae2667b947487db01f5fdef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2020
ms.locfileid: "97706940"
---
# <a name="use-dependency-injection-in-net-azure-functions"></a>Använda beroendeinmatning i .NET Azure Functions

Azure Functions stöder design mönstret för program beroende insprutning (DI), vilket är en teknik för att uppnå [inversion av kontrollen (IoC)](/dotnet/standard/modern-web-apps-azure-architecture/architectural-principles#dependency-inversion) mellan klasser och deras beroenden.

- Beroende inmatning i Azure Functions bygger på funktionerna för .NET Core-beroende inmatning. Det rekommenderas att du är bekant med [.net Core-beroende inmatning](/aspnet/core/fundamentals/dependency-injection) . Det finns skillnader i hur du åsidosätter beroenden och hur konfigurations värden läses med Azure Functions i förbruknings planen.

- Stöd för beroende inmatning börjar med Azure Functions 2. x.

## <a name="prerequisites"></a>Förutsättningar

Innan du kan använda beroende inmatning måste du installera följande NuGet-paket:

- [Microsoft. Azure. functions. Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/)

- [Microsoft. net. SDK. Functions](https://www.nuget.org/packages/Microsoft.NET.Sdk.Functions/) -paket version 1.0.28 eller senare

- [Microsoft. Extensions. DependencyInjection](https://www.nuget.org/packages/Microsoft.Extensions.DependencyInjection/) (för närvarande version 3. x och tidigare stöds)

## <a name="register-services"></a>Registrera tjänster

Registrera tjänster genom att skapa en metod för att konfigurera och lägga till komponenter i en `IFunctionsHostBuilder` instans.  Azure Functions-värden skapar en instans av `IFunctionsHostBuilder` och skickar den direkt till din metod.

Registrera-metoden genom att lägga till `FunctionsStartup` attributet Assembly som anger typ namnet som används vid start.

```csharp
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.DependencyInjection;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void Configure(IFunctionsHostBuilder builder)
        {
            builder.Services.AddHttpClient();

            builder.Services.AddSingleton<IMyService>((s) => {
                return new MyService();
            });

            builder.Services.AddSingleton<ILoggerProvider, MyLoggerProvider>();
        }
    }
}
```

I det här exemplet används paketet [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) som krävs för att registrera ett `HttpClient` vid start.

### <a name="caveats"></a>Varningar

En serie registrerings steg som körs före och efter körningen bearbetar start klassen. Tänk därför på följande saker:

- *Start klassen är endast avsedd för installation och registrering.* Undvik att använda tjänster som registrerats vid start under start processen. Försök till exempel inte att logga ett meddelande i en loggad logg som registreras under start. Den här tidpunkten för registrerings processen är för tidig för att tjänsterna ska kunna användas. När `Configure` metoden har körts fortsätter funktions körningen att registrera ytterligare beroenden, vilket kan påverka hur dina tjänster fungerar.

- *Den beroende injektions behållaren innehåller bara explicit registrerade typer*. De enda tjänster som är tillgängliga som inmatnings bara typer är vad som ställts in i- `Configure` metoden. Därför är funktions typerna som `BindingContext` och `ExecutionContext` inte tillgängliga under installationen eller som inmatnings bara typer.

## <a name="use-injected-dependencies"></a>Använda inmatade beroenden

Konstruktorn för konstruktorn används för att göra beroenden tillgängliga i en funktion. Användningen av konstruktorn för konstruktorn kräver att du inte använder statiska klasser för inmatade tjänster eller för dina funktions klasser.

Följande exempel visar hur `IMyService` och `HttpClient` beroenden matas in i en http-utlöst funktion.

```csharp
using Microsoft.AspNetCore.Http;
using Microsoft.AspNetCore.Mvc;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Extensions.Http;
using Microsoft.Extensions.Logging;
using System.Net.Http;
using System.Threading.Tasks;

namespace MyNamespace
{
    public class MyHttpTrigger
    {
        private readonly HttpClient _client;
        private readonly IMyService _service;

        public MyHttpTrigger(HttpClient httpClient, IMyService service)
        {
            this._client = httpClient;
            this._service = service;
        }

        [FunctionName("MyHttpTrigger")]
        public async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Function, "get", "post", Route = null)] HttpRequest req,
            ILogger log)
        {
            var response = await _client.GetAsync("https://microsoft.com");
            var message = _service.GetMessage();

            return new OkObjectResult("Response from function with injected dependencies.");
        }
    }
}
```

I det här exemplet används paketet [Microsoft. Extensions. http](https://www.nuget.org/packages/Microsoft.Extensions.Http/) som krävs för att registrera ett `HttpClient` vid start.

## <a name="service-lifetimes"></a>Livs längd för tjänsten

Azure Functions appar ger samma tjänste livstid som ASP.NET- [beroende inmatning](/aspnet/core/fundamentals/dependency-injection#service-lifetimes). För en Functions-app beter sig de olika livs längderna för tjänsten enligt följande:

- **Tillfälliga**: tillfälliga tjänster skapas vid varje lösning av tjänsten.
- **Omfattning**: livs längden för omfattnings tjänsten matchar en livs längd för funktions körning. Omfångs tjänster skapas en gång per funktion-körning. Senare begär Anden för tjänsten under körningen återanvänder den befintliga tjänst instansen.
- **Singleton**: singleton-tjänstens livs längd motsvarar värdens livstid och återanvänds över funktions körningar på den instansen. Singleton livstids tjänster rekommenderas för anslutningar och klienter, till exempel `DocumentClient` eller `HttpClient` instanser.

Visa eller hämta ett [exempel på olika livs längder för tjänsten](https://github.com/Azure/azure-functions-dotnet-extensions/tree/main/src/samples/DependencyInjection/Scopes) på GitHub.

## <a name="logging-services"></a>Loggningstjänster

Om du behöver en egen Logging-Provider registrerar du en anpassad typ som en instans av [`ILoggerProvider`](/dotnet/api/microsoft.extensions.logging.iloggerfactory) , som är tillgänglig via NuGet-paketet [Microsoft. Extensions. logging. Abstraction](https://www.nuget.org/packages/Microsoft.Extensions.Logging.Abstractions/) .

Application Insights läggs till av Azure Functions automatiskt.

> [!WARNING]
> - Lägg inte `AddApplicationInsightsTelemetry()` till i samlingen tjänster, som registrerar tjänster som står i konflikt med tjänster som tillhandahålls av miljön.
> - Registrera inte din egen `TelemetryConfiguration` eller `TelemetryClient` om du använder de inbyggda Application Insights funktionerna. Om du behöver konfigurera en egen `TelemetryClient` instans skapar du en via den inmatade `TelemetryConfiguration` som du ser i [Logga anpassad telemetri i C#-funktioner](functions-dotnet-class-library.md?tabs=v2%2Ccmd#log-custom-telemetry-in-c-functions).

### <a name="iloggert-and-iloggerfactory"></a>ILogger <T> och ILoggerFactory

Värden injiceras `ILogger<T>` och `ILoggerFactory` tjänster i konstruktörer.  Dessa nya loggnings filter filtreras dock inte som standard av funktions loggarna.  Du måste ändra `host.json` filen om du vill välja ytterligare filter och kategorier.

Följande exempel visar hur du lägger till en `ILogger<HttpTrigger>` med-loggar som exponeras för värden.

```csharp
namespace MyNamespace
{
    public class HttpTrigger
    {
        private readonly ILogger<HttpTrigger> _log;

        public HttpTrigger(ILogger<HttpTrigger> log)
        {
            _log = log;
        }

        [FunctionName("HttpTrigger")]
        public async Task<IActionResult> Run(
            [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req)
        {
            _log.LogInformation("C# HTTP trigger function processed a request.");

            // ...
    }
}
```

Följande exempel `host.json` fil lägger till logg filtret.

```json
{
    "version": "2.0",
    "logging": {
        "applicationInsights": {
            "samplingSettings": {
                "isEnabled": true,
                "excludedTypes": "Request"
            }
        },
        "logLevel": {
            "MyNamespace.HttpTrigger": "Information"
        }
    }
}
```

Mer information om loggnings nivåer finns i [Konfigurera logg nivåer](configure-monitoring.md#configure-log-levels).

## <a name="function-app-provided-services"></a>Function-appen tillhandahåller tjänster

Funktions värden registrerar många tjänster. Följande tjänster är säkra att ta med i ditt program:

|Typ av tjänst|Giltighet|Beskrivning|
|--|--|--|
|`Microsoft.Extensions.Configuration.IConfiguration`|Singleton|Körnings konfiguration|
|`Microsoft.Azure.WebJobs.Host.Executors.IHostIdProvider`|Singleton|Ansvarar för att tillhandahålla ID för värd instansen|

Om det finns andra tjänster som du vill ta ett beroende på, [skapar du ett ärende och föreslår dem på GitHub](https://github.com/azure/azure-functions-host).

### <a name="overriding-host-services"></a>Åsidosätta värd tjänster

Det finns för närvarande inte stöd för att åsidosätta tjänster som tillhandahålls av värden.  Om det finns tjänster som du vill åsidosätta [skapar du ett ärende och föreslår dem på GitHub](https://github.com/azure/azure-functions-host).

## <a name="working-with-options-and-settings"></a>Arbeta med alternativ och inställningar

Värden som definieras i [appinställningar](./functions-how-to-use-azure-function-app-settings.md#settings) är tillgängliga i en `IConfiguration` instans, vilket gör att du kan läsa inställningarna för appens inställningar i Start klassen.

Du kan extrahera värden från `IConfiguration` instansen till en anpassad typ. Genom att kopiera inställningarna för appen till en anpassad typ gör det enkelt att testa dina tjänster genom att göra dessa värden inmatnings bara. Inställningar som läses in i konfigurations instansen måste vara enkla nyckel/värde-par.

Överväg följande klass som innehåller en egenskap som heter konsekvent med en app-inställning:

```csharp
public class MyOptions
{
    public string MyCustomSetting { get; set; }
}
```

Och en `local.settings.json` fil som kan strukturera den anpassade inställningen enligt följande:
```json
{
  "IsEncrypted": false,
  "Values": {
    "MyOptions:MyCustomSetting": "Foobar"
  }
}
```

Inifrån `Startup.Configure` -metoden kan du extrahera värden från `IConfiguration` instansen till din anpassade typ med hjälp av följande kod:

```csharp
builder.Services.AddOptions<MyOptions>()
    .Configure<IConfiguration>((settings, configuration) =>
    {
        configuration.GetSection("MyOptions").Bind(settings);
    });
```

Anropar `Bind` kopierings värden som har matchande egenskaps namn från konfigurationen till den anpassade instansen. Alternativ instansen är nu tillgänglig i IoC-behållaren för inmatning i en funktion.

Options-objektet matas in i funktionen som en instans av det generiska `IOptions` gränssnittet. Använd `Value` egenskapen för att få åtkomst till de värden som finns i konfigurationen.

```csharp
using System;
using Microsoft.Extensions.Options;

public class HttpTrigger
{
    private readonly MyOptions _settings;

    public HttpTrigger(IOptions<MyOptions> options)
    {
        _settings = options.Value;
    }
}
```

Mer information om hur du arbetar med alternativ finns [i alternativ mönster i ASP.net Core](/aspnet/core/fundamentals/configuration/options) .

## <a name="customizing-configuration-sources"></a>Anpassa konfigurations källor

> [!NOTE]
> Anpassning av konfigurations källan är tillgänglig från Azure Functions värd versioner 2.0.14192.0 och 3.0.14191.0.

Om du vill ange ytterligare konfigurations källor åsidosätter du `ConfigureAppConfiguration` metoden i din Function-Apps- `StartUp` klass.

Följande exempel lägger till konfigurations värden från en bas och en valfri miljö bestämd app Setting-filer.

```csharp
using System.IO;
using Microsoft.Azure.Functions.Extensions.DependencyInjection;
using Microsoft.Extensions.Configuration;
using Microsoft.Extensions.DependencyInjection;

[assembly: FunctionsStartup(typeof(MyNamespace.Startup))]

namespace MyNamespace
{
    public class Startup : FunctionsStartup
    {
        public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
        {
            FunctionsHostBuilderContext context = builder.GetContext();

            builder.ConfigurationBuilder
                .AddJsonFile(Path.Combine(context.ApplicationRootPath, "appsettings.json"), optional: true, reloadOnChange: false)
                .AddJsonFile(Path.Combine(context.ApplicationRootPath, $"appsettings.{context.EnvironmentName}.json"), optional: true, reloadOnChange: false)
                .AddEnvironmentVariables();
        }
    }
}
```

Lägg till konfigurations leverantörer i `ConfigurationBuilder` egenskapen för `IFunctionsConfigurationBuilder` . Mer information om hur du använder konfigurations leverantörer finns [i konfiguration i ASP.net Core](/aspnet/core/fundamentals/configuration/#configuration-providers).

En `FunctionsHostBuilderContext` hämtas från `IFunctionsConfigurationBuilder.GetContext()` . Använd den här kontexten för att hämta det aktuella miljö namnet och lös platsen för konfigurationsfiler i mappen Function app.

Som standard kopieras konfigurationsfiler som *appsettings.jspå* inte automatiskt till mappen utdata i Function-appen. Uppdatera din *. CSPROJ* -fil så att den matchar följande exempel för att se till att filerna kopieras.

```xml
<None Update="appsettings.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>      
</None>
<None Update="appsettings.Development.json">
    <CopyToOutputDirectory>PreserveNewest</CopyToOutputDirectory>
    <CopyToPublishDirectory>Never</CopyToPublishDirectory>
</None>
```

> [!IMPORTANT]
> För Function-appar som körs i förbruknings-eller Premium-planerna kan ändringar av konfigurations värden som används i utlösare orsaka skalnings fel. Eventuella ändringar av dessa egenskaper av `FunctionsStartup` klassen resulterar i ett start fel i Function-appen.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande resurser:

- [Så här övervakar du din Function-app](functions-monitoring.md)
- [Metod tips för functions](functions-best-practices.md)
