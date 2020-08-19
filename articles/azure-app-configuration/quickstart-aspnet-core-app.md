---
title: Snabbstart för Azure App Configuration med ASP.NET Core | Microsoft Docs
description: Skapa en ASP.NET Core-app med Azure App konfiguration för att centralisera lagring och hantering av program inställningar för ett ASP.NET Core program.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 02/19/2020
ms.author: lcozzens
ms.openlocfilehash: 41675eb1911eede750b5a9cdc19cfe49e4699bac
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590310"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Snabb start: skapa en ASP.NET Core-app med Azure App konfiguration

I den här snabb starten ska du använda Azure App konfiguration för att centralisera lagring och hantering av program inställningar för ett ASP.NET Core program. ASP.NET Core skapar ett enda nyckelbaserade konfigurations objekt med hjälp av inställningar från en eller flera data källor som anges av ett program. Dessa data källor kallas för *konfigurations leverantörer*. Eftersom app Configurations .NET Core-klient implementeras som en Konfigurationsprovider, visas tjänsten som en annan data källa.

## <a name="prerequisites"></a>Förutsättningar

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
- [.NET Core SDK](https://dotnet.microsoft.com/download)

>[!TIP]
> Azure Cloud Shell är ett kostnads fritt interaktivt gränssnitt som du kan använda för att köra kommando rads instruktionerna i den här artikeln.  Den har vanliga Azure-verktyg förinstallerade, inklusive .NET Core SDK. Om du är inloggad på din Azure-prenumeration startar du [Azure Cloud Shell](https://shell.azure.com) från Shell.Azure.com.  Du kan lära dig mer om Azure Cloud Shell genom att [läsa vår dokumentation](../cloud-shell/overview.md)

## <a name="create-an-app-configuration-store"></a>Skapa ett konfigurations Arkiv för appen

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Välj **konfigurations Utforskaren**  >  **skapa**  >  **nyckel-värde** om du vill lägga till följande nyckel/värde-par:

    | Tangent | Värde |
    |---|---|
    | TestApp:Settings:BackgroundColor | Vit |
    | TestApp:Settings:FontSize | 24 |
    | TestApp:Settings:FontColor | Svarta |
    | TestApp:Settings:Message | Data från Azure App Configuration |

    Lämna **etiketten** och **innehålls typen** tom för tillfället. Välj **Tillämpa**.

## <a name="create-an-aspnet-core-web-app"></a>Skapa en ASP.NET Core-webbapp

Använd [.net Core kommando rads gränssnitt (CLI)](https://docs.microsoft.com/dotnet/core/tools/) för att skapa ett nytt ASP.net Core MVC-webbprogram. [Azure Cloud Shell](https://shell.azure.com) tillhandahåller dessa verktyg.  De är också tillgängliga på Windows-, macOS-och Linux-plattformarna.

1. Skapa en ny mapp för ditt projekt. I den här snabb starten ska du ge den namnet *TestAppConfig*.

1. I den nya mappen kör du följande kommando för att skapa ett nytt ASP.NET Core MVC-webbprogram-projekt:

```dotnetcli
dotnet new mvc --no-https
```

## <a name="add-secret-manager"></a>Lägga till Secret Manager

Om du vill använda Secret Manager lägger du till ett- `UserSecretsId` element i *. CSPROJ* -filen.

1. Öppna *. CSPROJ* -filen.

1.  Lägg till ett- `UserSecretsId` element som det visas här. Du kan använda samma GUID, eller så kan du ersätta det här värdet med ditt eget.

    > [!IMPORTANT]
    > `CreateHostBuilder` ersätter `CreateWebHostBuilder` i .net Core 3,0.  Välj rätt syntax baserat på din miljö.
    
    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)
    
    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
    
        <PropertyGroup>
            <TargetFramework>netcoreapp2.1</TargetFramework>
            <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
        </PropertyGroup>
    
        <ItemGroup>
            <PackageReference Include="Microsoft.AspNetCore.App" />
            <PackageReference Include="Microsoft.AspNetCore.Razor.Design" Version="2.1.2" PrivateAssets="All" />
        </ItemGroup>
    
    </Project>
    ```
    
    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)
    
    ```xml
    <Project Sdk="Microsoft.NET.Sdk.Web">
        
        <PropertyGroup>
            <TargetFramework>netcoreapp3.1</TargetFramework>
            <UserSecretsId>79a3edd0-2092-40a2-a04d-dcb46d5ca9ed</UserSecretsId>
        </PropertyGroup>
    
    </Project>
    ```
    ---

1. Spara *. CSPROJ* -filen.

Verktyget Secret Manager lagrar känsliga uppgifter för utvecklingsarbete utanför projektträdet. Den här metoden hjälper till att förhindra oavsiktlig delning av apphemligheter i källkoden.

> [!TIP]
> Om du vill veta mer om Secret Manager kan du läsa mer i [säker lagring av program hemligheter i utvecklingen i ASP.net Core](https://docs.microsoft.com/aspnet/core/security/app-secrets)

## <a name="connect-to-an-app-configuration-store"></a>Anslut till ett konfigurations Arkiv för appen

1. Lägg till en referens till `Microsoft.Azure.AppConfiguration.AspNetCore` NuGet-paketet genom att köra följande kommando:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. Kör följande kommando för att återställa paket för ditt projekt:

    ```dotnetcli
    dotnet restore
    ```

1. Lägg till en hemlighet med namnet *ConnectionStrings:AppConfig* i Secret Manager.

    Den här hemligheten innehåller anslutnings strängen för att komma åt appens konfigurations arkiv. Ersätt värdet i följande kommando med anslutnings strängen för appens konfigurations arkiv. Du kan hitta anslutnings strängen under **åtkomst nycklar** i Azure Portal.

    Det här kommandot måste köras i samma katalog som *.csproj*-filen.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig <your_connection_string>
    ```

    > [!IMPORTANT]
    > Vissa gränssnitt kommer att trunkera anslutnings strängen om den inte omges av citat tecken. Se till att `dotnet user-secrets` kommandots utdata visar hela anslutnings strängen. Om den inte gör det kör du kommandot på nytt, så att anslutnings strängen stängs av i citat tecken.

    Secret Manager används bara för att testa webbappen lokalt. När appen distribueras till [Azure App Service](https://azure.microsoft.com/services/app-service/web)kan du till exempel använda inställningen **anslutnings strängar**  i App Service i stället för hemliga hanterare för att lagra anslutnings strängen.

    Få åtkomst till den här hemligheten med Konfigurations-API: t Ett kolon (:) fungerar i konfigurations namnet med Konfigurations-API: et på alla plattformar som stöds. Se [konfiguration efter miljö](https://docs.microsoft.com/aspnet/core/fundamentals/configuration/index?tabs=basicconfiguration&view=aspnetcore-2.0).

1. Öppna *program.cs*och Lägg till en referens till .net Core app Configuration-providern.

    ```csharp
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

1. Uppdatera `CreateWebHostBuilder` metoden för att använda app-konfiguration genom att anropa- `config.AddAzureAppConfiguration()` metoden.

    > [!IMPORTANT]
    > `CreateHostBuilder` ersätter `CreateWebHostBuilder` i .net Core 3,0.  Välj rätt syntax baserat på din miljö.

    #### <a name="net-core-2x"></a>[.NET Core 2. x](#tab/core2x)

    ```csharp
    public static IWebHostBuilder CreateWebHostBuilder(string[] args) =>
        WebHost.CreateDefaultBuilder(args)
            .ConfigureAppConfiguration((hostingContext, config) =>
            {
                var settings = config.Build();
                config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
            })
            .UseStartup<Startup>();
    ```

    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
        .ConfigureWebHostDefaults(webBuilder =>
        webBuilder.ConfigureAppConfiguration((hostingContext, config) =>
        {
            var settings = config.Build();
            config.AddAzureAppConfiguration(settings["ConnectionStrings:AppConfig"]);
        })
        .UseStartup<Startup>());
    ```

    ---

1. Gå till * <app root> /views/Home* och öppna *index. cshtml*. Ersätt dess innehåll med följande kod:

    ```HTML
    @using Microsoft.Extensions.Configuration
    @inject IConfiguration Configuration

    <style>
        body {
            background-color: @Configuration["TestApp:Settings:BackgroundColor"]
        }
        h1 {
            color: @Configuration["TestApp:Settings:FontColor"];
            font-size: @Configuration["TestApp:Settings:FontSize"]px;
        }
    </style>

    <h1>@Configuration["TestApp:Settings:Message"]</h1>
    ```

1. Gå till * <app root> /views/Shared* och öppna *_Layout. cshtml*. Ersätt dess innehåll med följande kod:

    ```HTML
    <!DOCTYPE html>
    <html>
    <head>
        <meta charset="utf-8" />
        <meta name="viewport" content="width=device-width, initial-scale=1.0" />
        <title>@ViewData["Title"] - hello_world</title>

        <link rel="stylesheet" href="~/lib/bootstrap/dist/css/bootstrap.css" />
        <link rel="stylesheet" href="~/css/site.css" />
    </head>
    <body>
        <div class="container body-content">
            @RenderBody()
        </div>

        <script src="~/lib/jquery/dist/jquery.js"></script>
        <script src="~/lib/bootstrap/dist/js/bootstrap.js"></script>
        <script src="~/js/site.js" asp-append-version="true"></script>

        @RenderSection("Scripts", required: false)
    </body>
    </html>
    ```

## <a name="build-and-run-the-app-locally"></a>Skapa och köra appen lokalt

1. Om du vill skapa appen med hjälp av .NET Core CLI navigerar du till rot katalogen för programmet och kör följande kommando i kommando gränssnittet:

    ```dotnetcli
    dotnet build
    ```

1. När skapandet har slutförts kör du följande kommando för att köra webbappen lokalt:

    ```dotnetcli
    dotnet run
    ```

1. Om du arbetar på den lokala datorn kan du använda en webbläsare för att navigera till `http://localhost:5000` . Det här är standard-URL: en för webbappen som finns lokalt.  

Om du arbetar i Azure Cloud Shell väljer du knappen för *förhands granskning* följt av *Konfigurera*.  

![Leta upp knappen Förhandsgranska för webben](./media/quickstarts/cloud-shell-web-preview.png)

När du uppmanas att konfigurera porten för förhands granskning anger du "5000" och väljer *öppna och bläddra*.  Webb sidan kommer att läsa "data från Azure App-konfiguration."

![Startar snabb starts appen](./media/quickstarts/aspnet-core-app-launch-local-before.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du skapat ett nytt konfigurations Arkiv för appar och använt det med en ASP.NET Core-webbapp via [appens Konfigurationsprovider](https://go.microsoft.com/fwlink/?linkid=2074664). Fortsätt till nästa självstudie om du vill veta hur du konfigurerar din ASP.NET Core-app för att dynamiskt uppdatera konfigurations inställningar.

> [!div class="nextstepaction"]
> [Aktivera dynamisk konfiguration](./enable-dynamic-configuration-aspnet-core.md)
