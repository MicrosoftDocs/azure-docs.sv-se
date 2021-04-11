---
title: Självstudie för att använda funktions flaggor i en .NET Core-app | Microsoft Docs
description: I den här självstudien får du lära dig hur du implementerar funktions flaggor i .NET Core-appar.
services: azure-app-configuration
documentationcenter: ''
author: AlexandraKemperMS
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 09/17/2020
ms.author: alkemper
ms.custom: devx-track-csharp, mvc
ms.openlocfilehash: 4d54e1ff07b250b5595d2f8aee5f022bd2359721
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105729515"
---
# <a name="tutorial-use-feature-flags-in-an-aspnet-core-app"></a>Självstudie: använda funktions flaggor i en ASP.NET Core app

Hanterings biblioteken för .NET Core-funktioner ger idiomatiskt stöd för att implementera funktions flaggor i ett .NET-eller ASP.NET Core-program. Med de här biblioteken kan du lägga till funktions flaggor i koden så att du inte behöver skriva kod manuellt för att aktivera eller inaktivera funktioner med- `if` instruktioner.

Biblioteken för funktions hantering hanterar även livscykler i funktions flaggor i bakgrunden. Till exempel, biblioteks uppdaterings-och cache-flaggan eller garanterar att ett flagg tillstånd kan inte ändras under ett begär ande anrop. Dessutom erbjuder ASP.NET Core biblioteket färdiga integreringar, inklusive åtgärder för MVC-kontrollant, vyer, vägar och mellanprogram.

Med [funktionen Lägg till funktions flaggor till en ASP.net Core app-snabb start](./quickstart-feature-flag-aspnet-core.md) visas ett enkelt exempel på hur du använder funktions flaggor i ett ASP.net Core program. I den här självstudien visas ytterligare installations alternativ och funktioner i bibliotek för funktions hantering. Du kan använda exempel appen som skapades i snabb starten för att testa exempel koden som visas i den här självstudien. 

Dokumentation om API-referens för ASP.NET Core Feature Management finns i [namn området Microsoft. FeatureManagement](/dotnet/api/microsoft.featuremanagement).

I den här självstudien får du lära dig hur man:

> [!div class="checklist"]
> * Lägg till funktions flaggor i huvud delar av ditt program för att kontrol lera funktions tillgänglighet.
> * Integrera med app-konfiguration när du använder den för att hantera funktions flaggor.

## <a name="set-up-feature-management"></a>Konfigurera funktions hantering

För att få åtkomst till .NET Core Feature Manager måste din app ha referenser till `Microsoft.FeatureManagement.AspNetCore` NuGet-paketet.

.NET Core Feature Manager har kon figurer ATS från ramverkets inbyggda konfigurations system. Därför kan du definiera programmets funktions flagga inställningar genom att använda en konfigurations källa som .NET Core stöder, inklusive den lokala *appsettings.jspå* fil-eller miljövariabler.

Som standard hämtar funktions hanteraren konfiguration av funktions flagga från `"FeatureManagement"` avsnittet i konfigurations data för .net Core. Om du vill använda standard konfigurations platsen anropar du [AddFeatureManagement](/dotnet/api/microsoft.featuremanagement.servicecollectionextensions.addfeaturemanagement) -metoden för **IServiceCollection** som skickas till **ConfigureServices** -metoden i **Start** klassen.


```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement();
    }
}
```

Du kan ange att konfiguration av funktions hantering ska hämtas från ett annat konfigurations avsnitt genom att anropa [Configuration. GetSection](/dotnet/api/microsoft.web.administration.configuration.getsection) och skicka i namnet på det önskade avsnittet. I följande exempel instrueras funktions hanteraren att läsa från ett annat avsnitt som kallas `"MyFeatureFlags"` i stället:

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement(Configuration.GetSection("MyFeatureFlags"));
    }
}
```


Om du använder filter i dina funktions flaggor måste du inkludera namn området [Microsoft. FeatureManagement. FeatureFilters](/dotnet/api/microsoft.featuremanagement.featurefilters) och lägga till ett anrop till [AddFeatureFilters](/dotnet/api/microsoft.featuremanagement.ifeaturemanagementbuilder.addfeaturefilter) som anger typ namnet för filtret som du vill använda som generisk typ för metoden. Mer information om hur du använder funktions filter för att aktivera och inaktivera funktioner dynamiskt finns i [Aktivera stegvis distribution av funktioner för mål grupper](./howto-targetingfilter-aspnet-core.md).

I följande exempel visas hur du använder ett inbyggt funktions filter som heter `PercentageFilter` :



```csharp
using Microsoft.FeatureManagement;
using Microsoft.FeatureManagement.FeatureFilters;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        ...
        services.AddFeatureManagement()
                .AddFeatureFilter<PercentageFilter>();
    }
}
```

I stället för att hårdkoda dina funktions flaggor i ditt program, rekommenderar vi att du behåller funktions flaggor utanför programmet och hanterar dem separat. Om du gör det kan du när som helst ändra flaggans tillstånd och ändringarna börjar gälla i programmet direkt. Azure App konfigurations tjänsten innehåller ett dedikerat Portal gränssnitt för att hantera alla dina funktions flaggor. Azure App konfigurations tjänsten levererar också funktions flaggorna till ditt program direkt via klient biblioteken för .NET Core.

Det enklaste sättet att ansluta ASP.NET Core program till app-konfigurationen är via den Konfigurationsprovider som ingår i `Microsoft.Azure.AppConfiguration.AspNetCore` NuGet-paketet. När du har inkluderat en referens till paketet följer du dessa steg för att använda det här NuGet-paketet.

1. Öppna filen *program. cs* och Lägg till följande kod.
    > [!IMPORTANT]
    > `CreateHostBuilder` ersätter `CreateWebHostBuilder` i .net Core 3. x. Välj rätt syntax baserat på din miljö.

    ### <a name="net-5x"></a>[.NET 5. x](#tab/core5x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration(config =>
                {
                    var settings = config.Build();
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
                }).UseStartup<Startup>());
    ```

    ### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IHostBuilder CreateHostBuilder(string[] args) =>
    Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
            webBuilder.ConfigureAppConfiguration(config =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(options =>
                    options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
            }).UseStartup<Startup>());
    ```
        
    ### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)
    
    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;

    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
            WebHost.CreateDefaultBuilder(args)
                    .ConfigureAppConfiguration(config =>
                    {
                        var settings = config.Build();
                        config.AddAzureAppConfiguration(options =>
                            options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags());
                    }).UseStartup<Startup>();
    ```
    ---

2. Öppna *Start. cs* och uppdatera `Configure` och `ConfigureServices` -metoden för att lägga till inbyggda mellanprogram som kallas `UseAzureAppConfiguration` . Det här mellanprogramet tillåter att funktions flagg värden uppdateras vid ett återkommande intervall medan ASP.NET Core webbappen fortsätter att ta emot begär Anden.



    ```csharp
    public void Configure(IApplicationBuilder app, IWebHostEnvironment env)
    {
        app.UseAzureAppConfiguration();
    }
    ```

   ```csharp
   public void ConfigureServices(IServiceCollection services)
   {
       services.AddAzureAppConfiguration();
   }
   ```
   
I ett typiskt scenario kommer du att uppdatera dina funktions flagg värden regelbundet när du distribuerar och aktiverar och olika funktioner i programmet. Som standard cachelagras funktions flagg värden under en period på 30 sekunder, så en uppdaterings åtgärd utlöses när mellanprogram tar emot en begäran att inte uppdatera värdet förrän det cachelagrade värdet upphör att gälla. Följande kod visar hur du ändrar förfallo tiden för cachen eller avsöknings intervallet till 5 minuter genom att ange [CacheExpirationInterval](/dotnet/api/microsoft.extensions.configuration.azureappconfiguration.featuremanagement.featureflagoptions.cacheexpirationinterval) i anropet till **UseFeatureFlags**.


    
```csharp
config.AddAzureAppConfiguration(options =>
    options.Connect(settings["ConnectionStrings:AppConfig"]).UseFeatureFlags(featureFlagOptions => {
        featureFlagOptions.CacheExpirationInterval = TimeSpan.FromMinutes(5);
    }));
});
```


## <a name="feature-flag-declaration"></a>Deklaration för funktions flagga

Varje funktions flagga deklaration har två delar: ett namn och en lista med ett eller flera filter som används för att utvärdera om en funktions status är *på* (det vill säga när dess värde är `True` ). Ett filter definierar ett villkor för när en funktion ska aktive ras.

När en funktions flagga har flera filter, så genomsöks filter listan i ordning tills ett av filtren bestämmer att funktionen ska aktive ras. Nu är funktions flaggan *på* och eventuella kvarvarande filter resultat hoppas över. Om inget filter anger att funktionen ska aktive ras, är funktions flaggan *avstängd*.

Funktions hanteraren stöder *appsettings.jspå* som en konfigurations källa för funktions flaggor. I följande exempel visas hur du ställer in funktions flaggor i en JSON-fil:

```JSON
"FeatureManagement": {
    "FeatureA": true, // Feature flag set to on
    "FeatureB": false, // Feature flag set to off
    "FeatureC": {
        "EnabledFor": [
            {
                "Name": "Percentage",
                "Parameters": {
                    "Value": 50
                }
            }
        ]
    }
}
```

Efter konvention `FeatureManagement` används avsnittet i det här JSON-dokumentet för inställningar för funktions flagga. I föregående exempel visas tre funktions flaggor med de filter som definierats i `EnabledFor` egenskapen:

* `FeatureA` är *på*.
* `FeatureB` är *avstängd*.
* `FeatureC` anger ett filter med namnet `Percentage` med en `Parameters` egenskap. `Percentage` är ett konfigurerbart filter. I det här exemplet `Percentage` anger en sannolikhet på 50 procent för att `FeatureC` flaggan ska vara *på*. En instruktions guide om hur du använder funktions filter finns i [använda funktions filter för att aktivera villkorliga funktions flaggor](./howto-feature-filters-aspnet-core.md).




## <a name="use-dependency-injection-to-access-ifeaturemanager"></a>Använd beroende insprutning för att få åtkomst till IFeatureManager 

För vissa åtgärder, t. ex. kontroll av funktions flagg värden manuellt, måste du hämta en instans av [IFeatureManager](/dotnet/api/microsoft.featuremanagement.ifeaturemanager?preserve-view=true&view=azure-dotnet-preview). I ASP.NET Core MVC kan du komma åt funktions hanteraren `IFeatureManager` via beroende inmatning. I följande exempel läggs ett argument av typen `IFeatureManager` till i signaturen för konstruktorn för en kontrollant. Körningen löser automatiskt referensen och tillhandahåller ett av gränssnittet när du anropar konstruktorn. Om du använder en Programmall där kontrollanten redan har ett eller flera argument för beroende inmatning i konstruktorn, till exempel `ILogger` , kan du bara lägga till `IFeatureManager` som ett ytterligare argument:

### <a name="net-5x"></a>[.NET 5. x](#tab/core5x)
    
```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(ILogger<HomeController> logger, IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(ILogger<HomeController> logger, IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```
    
### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

```csharp
using Microsoft.FeatureManagement;

public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

---

## <a name="feature-flag-references"></a>Funktions flagga referenser

Definiera funktions flaggor som String-variabler för att referera till dem från kod:

```csharp
public static class MyFeatureFlags
{
    public const string FeatureA = "FeatureA";
    public const string FeatureB = "FeatureB";
    public const string FeatureC = "FeatureC";
}
```

## <a name="feature-flag-checks"></a>Funktions flagga kontrollerar

Ett gemensamt mönster för funktions hantering är att kontrol lera om en funktions flagga är inställd på på och, *i* så fall, köra ett avsnitt av kod. Exempel:

```csharp
IFeatureManager featureManager;
...
if (await featureManager.IsEnabledAsync(MyFeatureFlags.FeatureA))
{
    // Run the following code
}
```

## <a name="controller-actions"></a>Åtgärder för styrenhet

Med MVC-styrenheter kan du använda `FeatureGate` attributet för att kontrol lera om en hel kontroll enhets klass eller en speciell åtgärd är aktive rad. Följande `HomeController` styrenhet måste `FeatureA` vara *aktive* ras innan det går att utföra några åtgärder som klassen Controller innehåller kan utföra:

```csharp
using Microsoft.FeatureManagement.Mvc;

[FeatureGate(MyFeatureFlags.FeatureA)]
public class HomeController : Controller
{
    ...
}
```

Följande `Index` åtgärd måste `FeatureA` finnas innan den  kan köras:

```csharp
using Microsoft.FeatureManagement.Mvc;

[FeatureGate(MyFeatureFlags.FeatureA)]
public IActionResult Index()
{
    return View();
}
```

När en MVC-styrenhet eller-åtgärd blockeras eftersom kontroll funktions flaggan är *inaktive rad*, anropas ett registrerat [IDisabledFeaturesHandler](/dotnet/api/microsoft.featuremanagement.mvc.idisabledfeatureshandler?preserve-view=true&view=azure-dotnet-preview) -gränssnitt. Standard `IDisabledFeaturesHandler` gränssnittet returnerar en 404 status kod till klienten utan svars text.

## <a name="mvc-views"></a>MVC-vyer

Öppna *_ViewImports. cshtml* i katalogen *vyer* och Lägg till hjälp för Feature Manager-tagg:

```html
@addTagHelper *, Microsoft.FeatureManagement.AspNetCore
```

I MVC-vyer kan du använda en `<feature>` tagg för att återge innehåll baserat på om en funktions flagga är aktive rad:

```html
<feature name="FeatureA">
    <p>This can only be seen if 'FeatureA' is enabled.</p>
</feature>
```

Om du vill visa alternativt innehåll när kraven inte uppfylls `negate` kan attributet användas.

```html
<feature name="FeatureA" negate="true">
    <p>This will be shown if 'FeatureA' is disabled.</p>
</feature>
```

Funktions `<feature>` tag gen kan också användas för att visa innehåll om någon eller alla funktioner i en lista är aktiverade.

```html
<feature name="FeatureA, FeatureB" requirement="All">
    <p>This can only be seen if 'FeatureA' and 'FeatureB' are enabled.</p>
</feature>
<feature name="FeatureA, FeatureB" requirement="Any">
    <p>This can be seen if 'FeatureA', 'FeatureB', or both are enabled.</p>
</feature>
```

## <a name="mvc-filters"></a>MVC-filter

Du kan konfigurera MVC-filter så att de aktive ras baserat på status för en funktions flagga. Den här funktionen är begränsad till filter som implementerar [IAsyncActionFilter](/dotnet/api/microsoft.aspnetcore.mvc.filters.iasyncactionfilter). Följande kod lägger till ett MVC-filter med namnet `ThirdPartyActionFilter` . Det här filtret utlöses endast i MVC-pipeline om `FeatureA` är aktiverat.  

```csharp
using Microsoft.FeatureManagement.FeatureFilters;

IConfiguration Configuration { get; set;}

public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options => {
        options.Filters.AddForFeature<ThirdPartyActionFilter>(MyFeatureFlags.FeatureA);
    });
}
```

## <a name="middleware"></a>Mellanprogram

Du kan också använda funktions flaggor för att villkorligt lägga till program grenar och mellanprogram. Följande kod infogar en mellanprogram komponent i pipelinen för begär Anden endast när `FeatureA` är aktive rad:

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(MyFeatureFlags.FeatureA);
```

Den här koden skapar en mer allmän funktion för att förgrena hela programmet baserat på en funktions flagga:

```csharp
app.UseForFeature(featureName, appBuilder => {
    appBuilder.UseMiddleware<T>();
});
```

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du implementerar funktions flaggor i ditt ASP.NET Core program med hjälp av `Microsoft.FeatureManagement` biblioteken. Mer information om stöd för funktions hantering i ASP.NET Core-och app-konfiguration finns i följande resurser:

* [Exempel kod för ASP.NET Core funktions flagga](./quickstart-feature-flag-aspnet-core.md)
* [Dokumentation om Microsoft. FeatureManagement](/dotnet/api/microsoft.featuremanagement)
* [Hantera funktionsflaggor](./manage-feature-flags.md)