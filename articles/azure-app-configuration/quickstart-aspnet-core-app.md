---
title: Snabbstart för Azure App Configuration med ASP.NET Core | Microsoft Docs
description: Skapa en ASP.NET Core-app med Azure App konfiguration för att centralisera lagring och hantering av program inställningar för ett ASP.NET Core program.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.devlang: csharp
ms.custom: devx-track-csharp, contperfq1
ms.topic: quickstart
ms.date: 09/25/2020
ms.author: alkemper
ms.openlocfilehash: fc2b218621fcf91a7d82b0554da79a8b4582c3a2
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932174"
---
# <a name="quickstart-create-an-aspnet-core-app-with-azure-app-configuration"></a>Snabb start: skapa en ASP.NET Core-app med Azure App konfiguration

I den här snabb starten använder du Azure App konfiguration för att centralisera lagring och hantering av program inställningar för en ASP.NET Core-app. ASP.NET Core skapar ett enda, nyckel-värdebaserade konfigurations objekt med inställningar från en eller flera data källor som anges av en app. Dessa data källor kallas för *konfigurations leverantörer*. Eftersom app Configurations .NET Core-klient implementeras som en Konfigurationsprovider, visas tjänsten som en annan data källa.

## <a name="prerequisites"></a>Krav

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/dotnet)
* [.NET Core SDK](https://dotnet.microsoft.com/download)

> [!TIP]
> Azure Cloud Shell är ett kostnads fritt, interaktivt gränssnitt som du kan använda för att köra kommando rads instruktionerna i den här artikeln. Den har vanliga Azure-verktyg förinstallerade, inklusive .NET Core SDK. Om du är inloggad på din Azure-prenumeration startar du [Azure Cloud Shell](https://shell.azure.com) från Shell.Azure.com. Du kan lära dig mer om Azure Cloud Shell genom att [läsa vår dokumentation](../cloud-shell/overview.md)

## <a name="create-an-app-configuration-store"></a>Skapa ett konfigurations Arkiv för appen

[!INCLUDE[Azure App Configuration resource creation steps](../../includes/azure-app-configuration-create.md)]

7. Välj **Operations**  >  **Configuration Explorer**  >  **skapa**  >  **nyckel-värde** om du vill lägga till följande nyckel/värde-par:

    | Tangent                                | Värde                               |
    |------------------------------------|-------------------------------------|
    | `TestApp:Settings:BackgroundColor` | *#FFF*                              |
    | `TestApp:Settings:FontColor`       | *#000*                              |
    | `TestApp:Settings:FontSize`        | *24.1*                                |
    | `TestApp:Settings:Message`         | *Data från Azure App Configuration* |

    Lämna **etiketten** och **innehålls typen** tom för tillfället. Välj **Använd**.

## <a name="create-an-aspnet-core-web-app"></a>Skapa en ASP.NET Core-webbapp

Använd [.net Core kommando rads gränssnitt (CLI)](/dotnet/core/tools) för att skapa ett nytt ASP.net Core MVC-projekt. [Azure Cloud Shell](https://shell.azure.com) tillhandahåller dessa verktyg. De är också tillgängliga på Windows-, macOS-och Linux-plattformarna.

Kör följande kommando för att skapa ett ASP.NET Core MVC-projekt i en ny *TestAppConfig* -mapp:

```dotnetcli
dotnet new mvc --no-https --output TestAppConfig
```

[!INCLUDE[Add Secret Manager support to an ASP.NET Core project](../../includes/azure-app-configuration-add-secret-manager.md)]

## <a name="connect-to-the-app-configuration-store"></a>Anslut till appens konfigurations Arkiv

1. Kör följande kommando för att lägga till en [Microsoft. Azure. AppConfiguration. AspNetCore](https://www.nuget.org/packages/Microsoft.Azure.AppConfiguration.AspNetCore) NuGet-paket referens:

    ```dotnetcli
    dotnet add package Microsoft.Azure.AppConfiguration.AspNetCore
    ```

1. Kör följande kommando i samma katalog som *. CSPROJ* -filen. Kommandot använder Secret Manager för att lagra en hemlighet med namnet `ConnectionStrings:AppConfig` , som lagrar anslutnings strängen för din app Configuration Store. Ersätt `<your_connection_string>` plats hållaren med appens konfigurations arkivets anslutnings sträng. Du kan hitta anslutnings strängen under **åtkomst nycklar** i Azure Portal.

    ```dotnetcli
    dotnet user-secrets set ConnectionStrings:AppConfig "<your_connection_string>"
    ```

    > [!IMPORTANT]
    > Vissa gränssnitt kommer att trunkera anslutnings strängen om den inte omges av citat tecken. Se till att `dotnet user-secrets` kommandots utdata visar hela anslutnings strängen. Om den inte gör det kör du kommandot på nytt, så att anslutnings strängen stängs av i citat tecken.

    Secret Manager används bara för att testa webbappen lokalt. När appen distribueras till [Azure App Service](https://azure.microsoft.com/services/app-service/web)använder du inställningen **anslutnings strängar** i App Service i stället för Secret Manager för att lagra anslutnings strängen.

    Få åtkomst till den här hemligheten med API för .NET Core-konfiguration. Ett kolon ( `:` ) fungerar i konfigurations namnet med Konfigurations-API: et på alla plattformar som stöds. Mer information finns i [konfigurations nycklar och värden](/aspnet/core/fundamentals/configuration#configuration-keys-and-values).

1. I *program.cs* lägger du till en referens till API-namnrymden för .net Core Configuration:

    ```csharp
    using Microsoft.Extensions.Configuration;
    ```

1. Uppdatera `CreateWebHostBuilder` metoden för att använda app-konfiguration genom att anropa- `AddAzureAppConfiguration` metoden.

    > [!IMPORTANT]
    > `CreateHostBuilder` ersätter `CreateWebHostBuilder` i .net Core 3. x. Välj rätt syntax baserat på din miljö.

    #### <a name="net-core-3x"></a>[.NET Core 3. x](#tab/core3x)

    ```csharp
    public static IHostBuilder CreateHostBuilder(string[] args) =>
        Host.CreateDefaultBuilder(args)
            .ConfigureWebHostDefaults(webBuilder =>
                webBuilder.ConfigureAppConfiguration(config =>
                {
                    var settings = config.Build();
                    var connection = settings.GetConnectionString("AppConfig");
                    config.AddAzureAppConfiguration(connection);
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
                config.AddAzureAppConfiguration(connection);
            })
            .UseStartup<Startup>();
    ```

    ---

    Med den föregående ändringen har [konfigurations leverantören för app-konfigurationen](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration) registrerats med API för .net Core-konfiguration.

## <a name="read-from-the-app-configuration-store"></a>Läsa från appens konfigurations Arkiv

Slutför följande steg för att läsa och visa värden som lagras i appens konfigurations arkiv. Konfigurations-API: t för .NET Core kommer att användas för att komma åt butiken. Kniv-syntax kommer att användas för att Visa Nycklarnas värden.

Öppna *\<app root> /views/Home/index.cshtml* och ersätt innehållet med följande kod:

```cshtml
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

I föregående kod används appens konfigurations arkivets nycklar på följande sätt:

* `TestApp:Settings:BackgroundColor`Nyckelns värde tilldelas CSS- `background-color` egenskapen.
* `TestApp:Settings:FontColor`Nyckelns värde tilldelas CSS- `color` egenskapen.
* `TestApp:Settings:FontSize`Nyckelns värde tilldelas CSS- `font-size` egenskapen.
* `TestApp:Settings:Message`Nyckelns värde visas som en rubrik.

## <a name="build-and-run-the-app-locally"></a>Skapa och köra appen lokalt

1. Om du vill bygga appen med hjälp av .NET Core CLI navigerar du till rot katalogen för projektet. Kör följande kommando i kommandogränssnittet:

    ```dotnetcli
    dotnet build
    ```

1. När skapandet har slutförts kör du följande kommando för att köra webbappen lokalt:

    ```dotnetcli
    dotnet run
    ```

1. Om du arbetar på den lokala datorn kan du använda en webbläsare för att navigera till `http://localhost:5000` . Den här adressen är standard-URL: en för den lokalt värdbaserade webbappen. Om du arbetar i Azure Cloud Shell väljer du knappen för **förhands granskning** följt av **Konfigurera**.

    ![Leta upp knappen Förhandsgranska för webben](./media/quickstarts/cloud-shell-web-preview.png)

    När du uppmanas att konfigurera porten för förhands granskning anger du *5000* och väljer **öppna och bläddra**. Webb sidan kommer att läsa "data från Azure App-konfiguration."

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE[Azure App Configuration cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten kommer du att göra följande:

* Etablerade ett nytt konfigurations Arkiv för appar.
* Registrerade konfigurations leverantören för konfigurations arkivets .NET-kärna.
* Läs program konfigurations arkivets nycklar med konfigurationsprovidern.
* Visar appens konfigurations arkivs nyckel värden med hjälp av kniv-syntax.

Fortsätt till nästa självstudie om du vill veta hur du konfigurerar din ASP.NET Core-app för att dynamiskt uppdatera konfigurations inställningar.

> [!div class="nextstepaction"]
> [Aktivera dynamisk konfiguration](./enable-dynamic-configuration-aspnet-core.md)