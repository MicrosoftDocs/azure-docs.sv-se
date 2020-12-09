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
ms.openlocfilehash: 8c0dd9713c673ad676058acc7dbbb3cb5a65362e
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929199"
---
# <a name="tutorial-use-feature-flags-in-an-aspnet-core-app"></a>Självstudie: använda funktions flaggor i en ASP.NET Core app

Hanterings biblioteken för .NET Core-funktioner ger idiomatiskt stöd för att implementera funktions flaggor i ett .NET-eller ASP.NET Core-program. Med de här biblioteken kan du lägga till funktions flaggor i koden så att du inte behöver skriva alla `if` uttryck manuellt.

Biblioteken för funktions hantering hanterar även livscykler i funktions flaggor i bakgrunden. Till exempel, biblioteks uppdaterings-och cache-flaggan eller garanterar att ett flagg tillstånd kan inte ändras under ett begär ande anrop. Dessutom erbjuder ASP.NET Core biblioteket färdiga integreringar, inklusive åtgärder för MVC-kontrollant, vyer, vägar och mellanprogram.

Med [funktionen Lägg till funktions flaggor till en ASP.net Core app-snabb start](./quickstart-feature-flag-aspnet-core.md) visas flera sätt att lägga till funktions flaggor i ett ASP.net Core program. I den här självstudien beskrivs dessa metoder i detalj. En fullständig referens finns i dokumentationen för [ASP.net Core Feature Management](/dotnet/api/microsoft.featuremanagement).

I den här självstudien får du lära dig hur man:

> [!div class="checklist"]
> * Lägg till funktions flaggor i huvud delar av ditt program för att kontrol lera funktions tillgänglighet.
> * Integrera med app-konfiguration när du använder den för att hantera funktions flaggor.

## <a name="set-up-feature-management"></a>Konfigurera funktions hantering

Lägg till en referens till `Microsoft.FeatureManagement.AspNetCore` `Microsoft.FeatureManagement` NuGet-paketen och Använd .net Core Feature Manager.
    
.NET Core Feature Manager `IFeatureManager` hämtar funktions flaggor från ramverkets inbyggda konfigurations system. Därför kan du definiera programmets funktions flaggor genom att använda en konfigurations källa som .NET Core stöder, inklusive den lokala *appsettings.jspå* fil-eller miljövariabler. `IFeatureManager` förlitar sig på .NET Core-beroende inmatning. Du kan registrera funktions hanterings tjänsterna genom att använda standard konventioner:

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement();
    }
}
```

Som standard hämtar funktions hanteraren funktions flaggor från `"FeatureManagement"` avsnittet i konfigurations data för .net Core. I följande exempel instrueras funktions hanteraren att läsa från ett annat avsnitt som kallas `"MyFeatureFlags"` i stället:

```csharp
using Microsoft.FeatureManagement;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement(options =>
        {
                options.UseConfiguration(Configuration.GetSection("MyFeatureFlags"));
        });
    }
}
```

Om du använder filter i dina funktions flaggor måste du inkludera ett ytterligare bibliotek och registrera det. I följande exempel visas hur du använder ett inbyggt funktions filter som heter `PercentageFilter` :

```csharp
using Microsoft.FeatureManagement;
using Microsoft.FeatureManagement.FeatureFilters;

public class Startup
{
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddFeatureManagement()
                .AddFeatureFilter<PercentageFilter>();
    }
}
```

Vi rekommenderar att du behåller funktions flaggor utanför programmet och hanterar dem separat. Om du gör det kan du när som helst ändra flaggans tillstånd och ändringarna börjar gälla i programmet direkt. Med app Configuration får du en central plats för att ordna och kontrol lera alla funktions flaggor via ett dedikerat Portal gränssnitt. App-konfigurationen skickar också flaggorna till ditt program direkt via dess .NET Core-klient bibliotek.

Det enklaste sättet att ansluta din ASP.NET Core program till app-konfigurationen är via konfigurationsprovidern `Microsoft.Azure.AppConfiguration.AspNetCore` . Följ dessa steg om du vill använda det här NuGet-paketet.

1. Öppna *program.cs* -filen och Lägg till följande kod.

   ```csharp
   using Microsoft.Extensions.Configuration.AzureAppConfiguration;

   public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
       WebHost.CreateDefaultBuilder(args)
              .ConfigureAppConfiguration((hostingContext, config) => {
                  var settings = config.Build();
                  config.AddAzureAppConfiguration(options => {
                      options.Connect(settings["ConnectionStrings:AppConfig"])
                             .UseFeatureFlags();
                   });
              })
              .UseStartup<Startup>();
   ```

2. Öppna *startup.cs* och uppdatera `Configure` metoden för att lägga till det inbyggda mellanprogram som kallas `UseAzureAppConfiguration` . Det här mellanprogramet tillåter att funktions flagg värden uppdateras vid ett återkommande intervall medan ASP.NET Core webbappen fortsätter att ta emot begär Anden.

   ```csharp
   public void Configure(IApplicationBuilder app, IHostingEnvironment env)
   {
       app.UseAzureAppConfiguration();
       app.UseMvc();
   }
   ```

Funktions flagg värden förväntas ändras med tiden. Som standard cachelagras funktions flagg värden under en period på 30 sekunder, så en uppdaterings åtgärd utlöses när mellanprogram tar emot en begäran att inte uppdatera värdet förrän det cachelagrade värdet upphör att gälla. Följande kod visar hur du ändrar förfallo tiden för cachen eller avsöknings intervallet till 5 minuter i `options.UseFeatureFlags()` anropet.

```csharp
config.AddAzureAppConfiguration(options => {
    options.Connect(settings["ConnectionStrings:AppConfig"])
           .UseFeatureFlags(featureFlagOptions => {
                featureFlagOptions.CacheExpirationTime = TimeSpan.FromMinutes(5);
           });
});
```

## <a name="feature-flag-declaration"></a>Deklaration för funktions flagga

Varje funktions flagga har två delar: ett namn och en lista med ett eller flera filter som används för att utvärdera om en funktions status är *på* (det vill säga när dess värde är `True` ). Ett filter definierar ett användnings fall för när en funktion ska aktive ras.

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
* `FeatureC` anger ett filter med namnet `Percentage` med en `Parameters` egenskap. `Percentage` är ett konfigurerbart filter. I det här exemplet `Percentage` anger en sannolikhet på 50 procent för att `FeatureC` flaggan ska vara *på*.

## <a name="feature-flag-references"></a>Funktions flagga referenser

Du kan enkelt referera till funktions flaggor i kod genom att definiera dem som `enum` variabler:

```csharp
public enum MyFeatureFlags
{
    FeatureA,
    FeatureB,
    FeatureC
}
```

## <a name="feature-flag-checks"></a>Funktions flagga kontrollerar

Det grundläggande mönstret för funktions hantering är att först kontrol lera om en funktions flagga har angetts till *på*. I så fall kör funktions hanteraren de åtgärder som funktionen innehåller. Exempel:

```csharp
IFeatureManager featureManager;
...
if (await featureManager.IsEnabledAsync(nameof(MyFeatureFlags.FeatureA)))
{
    // Run the following code
}
```

## <a name="dependency-injection"></a>Beroendeinmatning

I ASP.NET Core MVC kan du komma åt funktions hanteraren `IFeatureManager` via beroende inmatning:

```csharp
public class HomeController : Controller
{
    private readonly IFeatureManager _featureManager;

    public HomeController(IFeatureManager featureManager)
    {
        _featureManager = featureManager;
    }
}
```

## <a name="controller-actions"></a>Åtgärder för styrenhet

I MVC-styrenheter använder du `FeatureGate` attributet för att kontrol lera om en hel kontroll enhets klass eller en speciell åtgärd är aktive rad. Följande `HomeController` styrenhet måste `FeatureA` vara *aktive* ras innan det går att utföra några åtgärder som klassen Controller innehåller kan utföra:

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

När en MVC-styrenhet eller-åtgärd blockeras eftersom kontroll funktions flaggan är *inaktive rad*, `IDisabledFeaturesHandler` anropas ett registrerat gränssnitt. Standard `IDisabledFeaturesHandler` gränssnittet returnerar en 404 status kod till klienten utan svars text.

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

Du kan konfigurera MVC-filter så att de aktive ras baserat på status för en funktions flagga. Följande kod lägger till ett MVC-filter med namnet `SomeMvcFilter` . Det här filtret utlöses endast i MVC-pipeline om `FeatureA` är aktiverat. Den här funktionen är begränsad till `IAsyncActionFilter` . 

```csharp
using Microsoft.FeatureManagement.FeatureFilters;

IConfiguration Configuration { get; set;}

public void ConfigureServices(IServiceCollection services)
{
    services.AddMvc(options => {
        options.Filters.AddForFeature<SomeMvcFilter>(nameof(MyFeatureFlags.FeatureA));
    });
}
```

## <a name="middleware"></a>Mellanprogram

Du kan också använda funktions flaggor för att villkorligt lägga till program grenar och mellanprogram. Följande kod infogar en mellanprogram komponent i pipelinen för begär Anden endast när `FeatureA` är aktive rad:

```csharp
app.UseMiddlewareForFeature<ThirdPartyMiddleware>(nameof(MyFeatureFlags.FeatureA));
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