---
title: Snabb start för att lägga till funktions flaggor i ASP.NET Core
description: Lägg till funktions flaggor i ASP.NET Core appar och hantera dem med Azure App konfiguration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 09/28/2020
ms.author: alkemper
ms.openlocfilehash: 8ca4a6e78ab3c6ccf492869bb68c5296ae91de21
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99806078"
---
# <a name="quickstart-add-feature-flags-to-an-aspnet-core-app"></a>Snabb start: Lägg till funktions flaggor i en ASP.NET Core app

I den här snabb starten skapar du en end-to-end-implementering av funktions hantering i en ASP.NET Core-app med hjälp av Azure App konfiguration. Du använder app Configuration service för att centralt lagra alla dina funktions flaggor och kontrol lera deras tillstånd. 

Biblioteken för .NET Core Feature Management utökar ramverket med omfattande stöd för funktions flaggor. Dessa bibliotek skapas ovanpå konfigurations systemet för .NET Core. De integreras sömlöst med app-konfigurationen via sin .NET Core-Konfigurationsprovider.

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/dotnet)
* [.NET Core SDK](https://dotnet.microsoft.com/download)

## <a name="create-an-app-configuration-store"></a>Skapa ett konfigurations Arkiv för appen

[!INCLUDE[Azure App Configuration resource creation steps](../../includes/azure-app-configuration-create.md)]

8. Välj **Operations**  >  **Feature Manager**  >  **Lägg till** för att lägga till en funktions flagga som heter *beta*.

    > [!div class="mx-imgBorder"]
    > ![Aktivera funktions flagga med namnet beta](media/add-beta-feature-flag.png)

    Lämna **etiketten** tom för tillfället. Välj **Använd** för att spara den nya funktions flaggan.

## <a name="create-an-aspnet-core-web-app"></a>Skapa en ASP.NET Core-webbapp

Använd [.net Core kommando rads gränssnitt (CLI)](/dotnet/core/tools) för att skapa ett nytt ASP.net Core MVC-projekt. Fördelen med att använda .NET Core CLI i stället för Visual Studio är att .NET Core CLI är tillgängligt på Windows-, macOS-och Linux-plattformarna.

Kör följande kommando för att skapa ett ASP.NET Core MVC-projekt i en ny *TestFeatureFlags* -mapp:

```dotnetcli
dotnet new mvc --no-https --output TestFeatureFlags
```

[!INCLUDE[Add Secret Manager support to an ASP.NET Core project](../../includes/azure-app-configuration-add-secret-manager.md)]

## <a name="connect-to-an-app-configuration-store"></a>Anslut till ett konfigurations Arkiv för appen

1. Installera paketet [Microsoft. Azure. AppConfiguration. AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore) och [Microsoft. FeatureManagement. AspNetCore](https://www.nuget.org/packages/Microsoft.FeatureManagement.AspNetCore) NuGet genom att köra följande kommandon:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

    ```dotnetcli
    dotnet add package Microsoft.FeatureManagement.AspNetCore
    ```

1. Kör följande kommando i samma katalog som *. CSPROJ* -filen. Kommandot använder Secret Manager för att lagra en hemlighet med namnet `ConnectionStrings:AppConfig` , som lagrar anslutnings strängen för din app Configuration Store. Ersätt `<your_connection_string>` plats hållaren med appens konfigurations arkivets anslutnings sträng. Du kan hitta anslutnings strängen under **åtkomst nycklar** i Azure Portal.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig "<your_connection_string>"
    ```

    Secret Manager används bara för att testa webbappen lokalt. När appen distribueras till [Azure App Service](https://azure.microsoft.com/services/app-service/web)använder du inställningen **anslutnings strängar** i App Service i stället för Secret Manager för att lagra anslutnings strängen.

    Få åtkomst till den här hemligheten med API för .NET Core-konfiguration. Ett kolon ( `:` ) fungerar i konfigurations namnet med Konfigurations-API: et på alla plattformar som stöds. Mer information finns i [konfigurations nycklar och värden](/aspnet/core/fundamentals/configuration#configuration-keys-and-values).

1. I *program. cs* uppdaterar du `CreateWebHostBuilder` metoden för att använda app-konfiguration genom att anropa `AddAzureAppConfiguration` metoden.

    > [!IMPORTANT]
    > `CreateHostBuilder` ersätter `CreateWebHostBuilder` i .net Core 3. x. Välj rätt syntax baserat på din miljö.

     #### <a name="net-5x"></a>[.NET 5. x](#tab/core5x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration(config =>
                {
                    var settings = config.Build();
                    var connection = settings.GetConnectionString("AppConfig");
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(connection).UseFeatureFlags());
                }).UseStartup<Startup>());
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration(config =>
                {
                    var settings = config.Build();
                    var connection = settings.GetConnectionString("AppConfig");
                    config.AddAzureAppConfiguration(options =>
                        options.Connect(connection).UseFeatureFlags());
                }).UseStartup<Startup>());
    ```

    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
               .ConfigureAppConfiguration(config =>
               {
                   var settings = config.Build();
                   var connection = settings.GetConnectionString("AppConfig");
                   config.AddAzureAppConfiguration(options =>
                       options.Connect(connection).UseFeatureFlags());
               }).UseStartup<Startup>();
    ```

    ---

    Med den föregående ändringen har [konfigurations leverantören för app-konfigurationen](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration) registrerats med API för .net Core-konfiguration.

1. I *startup. cs* lägger du till en referens till .net Core Feature Manager:

    ```csharp
    using Microsoft.FeatureManagement;
    ```

1. Uppdatera `Startup.ConfigureServices` metoden för att lägga till stöd för funktions flaggor genom att anropa `AddFeatureManagement` metoden. Du kan också ta med alla filter som ska användas med funktions flaggor genom att anropa `AddFeatureFilter<FilterType>()` :

     #### <a name="net-5x"></a>[.NET 5. x](#tab/core5x)

    ```csharp    
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllersWithViews();
        services.AddFeatureManagement();
    }
    ```
    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp    
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddControllersWithViews();
        services.AddFeatureManagement();
    }
    ```

    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
    public void ConfigureServices(IServiceCollection services)
    {
        services.AddMvc()
            .SetCompatibilityVersion(CompatibilityVersion.Version_2_2);
        services.AddFeatureManagement();
    }
    ```

    ---

1. Lägg till en *MyFeatureFlags. cs* -fil i rot projekt katalogen med följande kod:

    ```csharp
    namespace TestFeatureFlags
    {
        public enum MyFeatureFlags
        {
            Beta
        }
    }
    ```

1. Lägg till en *BetaController. cs* -fil i katalogen *controllers* med följande kod:

    ```csharp
    using Microsoft.AspNetCore.Mvc;
    using Microsoft.FeatureManagement;
    using Microsoft.FeatureManagement.Mvc;

    namespace TestFeatureFlags.Controllers
    {
        public class BetaController: Controller
        {
            private readonly IFeatureManager _featureManager;

            public BetaController(IFeatureManagerSnapshot featureManager) =>
                _featureManager = featureManager;

            [FeatureGate(MyFeatureFlags.Beta)]
            public IActionResult Index() => View();
        }
    }
    ```

1. I *vyer/_ViewImports. cshtml*, registrera Feature Manager-taggnings hjälpen med ett `@addTagHelper` direktiv:

    ```cshtml
    @addTagHelper *, Microsoft.FeatureManagement.AspNetCore
    ```

    Med föregående kod kan du `<feature>` använda etikett hjälpen i projektets *. cshtml* -filer.

1. Öppna *_Layout. cshtml* i den  \\ *delade* katalogen för vyer. Leta upp `<nav>` streckkoden under `<body>`  >  `<header>` . Infoga en ny `<feature>` tagg mellan *Start* -och *Sekretess* navigerings objekt, som du ser i de markerade raderna nedan.

    :::code language="html" source="../../includes/azure-app-configuration-navbar.md" range="15-38" highlight="14-18":::

1. Skapa en *vy/beta-* katalog och en *index. cshtml* -fil som innehåller följande markering:

    ```cshtml
    @{
        ViewData["Title"] = "Beta Home Page";
    }

    <h1>This is the beta website.</h1>
    ```

## <a name="build-and-run-the-app-locally"></a>Skapa och köra appen lokalt

1. Om du vill skapa appen med hjälp av .NET Core CLI kör du följande kommando i kommando gränssnittet:

    ```dotnetcli
    dotnet build
    ```

1. När skapandet har slutförts kör du följande kommando för att köra webbappen lokalt:

    ```dotnetcli
    dotnet run
    ```

1. Öppna ett webbläsarfönster och gå till `http://localhost:5000` , vilket är standard-URL: en för webbappen som finns lokalt. Om du arbetar i Azure Cloud Shell väljer du knappen för **förhands granskning** följt av **Konfigurera**. När du uppmanas väljer du port 5000.

    ![Leta upp knappen Förhandsgranska för webben](./media/quickstarts/cloud-shell-web-preview.png)

    Webbläsaren ska visa en sida som liknar bilden nedan.

    :::image type="content" source="media/quickstarts/aspnet-core-feature-flag-local-before.png" alt-text="Lokal snabb starts app före ändring" border="true":::

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **alla resurser** och välj den instans av app Configuration Store som du skapade i snabb starten.

1. Välj **funktions hanterare**. 

1. Aktivera *beta* -flaggan genom att markera kryss rutan under **aktive rad**.

1. Återgå till kommando gränssnittet. Avbryt den pågående `dotnet` processen genom att trycka på <kbd>CTRL + C</kbd>. Starta om din app med `dotnet run` .

1. Uppdatera webbläsarsidan för att visa de nya konfigurationsinställningarna.

    :::image type="content" source="media/quickstarts/aspnet-core-feature-flag-local-after.png" alt-text="Lokal snabb starts app efter ändring" border="true":::

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE[Azure App Configuration cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du skapat ett nytt konfigurations Arkiv för appar och använt det för att hantera funktioner i en ASP.NET Core-webbapp via [biblioteken för funktions hantering](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration).

* Läs mer om [funktions hantering](./concept-feature-management.md).
* [Hantera funktions flaggor](./manage-feature-flags.md).
* [Använd funktions flaggor i en ASP.net Core app](./use-feature-flags-dotnet-core.md).
* [Använda dynamisk konfiguration i en ASP.NET Core-app](./enable-dynamic-configuration-aspnet-core.md)