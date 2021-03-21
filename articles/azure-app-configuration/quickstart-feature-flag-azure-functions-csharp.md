---
title: Snabb start för att lägga till funktions flaggor i Azure Functions | Microsoft Docs
description: I den här snabb starten ska du använda Azure Functions med funktions flaggor från Azure App konfiguration och testa funktionen lokalt.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 8/26/2020
ms.author: alkemper
ms.openlocfilehash: 96efc0ea6300e482ddeeda8fa177847f02b7e126
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98724262"
---
# <a name="quickstart-add-feature-flags-to-an-azure-functions-app"></a>Snabb start: Lägg till funktions flaggor i en Azure Functions app

I den här snabb starten skapar du en Azure Functions app och använder funktions flaggor i den. Du kan använda funktions hantering från Azure App konfiguration för att centralt lagra alla dina funktions flaggor och kontrol lera deras tillstånd.

Biblioteken för .NET-funktions hantering utökar ramverket med stöd för funktions flagga. Dessa bibliotek skapas ovanpå .NET-konfigurations systemet. De integreras med app-konfiguration via dess .NET-Konfigurationsprovider.

## <a name="prerequisites"></a>Förutsättningar

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) med arbets belastningen **Azure Development** .
- [Azure Functions verktyg](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>Skapa ett konfigurations Arkiv för appen

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Välj **funktions hanteraren**  >  **+ Lägg** till för att lägga till en funktions flagga som kallas `Beta` .

    > [!div class="mx-imgBorder"]
    > ![Aktivera funktions flagga med namnet beta](media/add-beta-feature-flag.png)

    Lämna `label` och gör `Description` odefinierat för tillfället.

8. Välj **Använd** för att spara den nya funktions flaggan.

## <a name="create-a-functions-app"></a>Skapa en Functions-app

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>Anslut till ett konfigurations Arkiv för appen

Det här projektet kommer att använda [beroende inmatning i .net Azure Functions](../azure-functions/functions-dotnet-dependency-injection.md). Den lägger till Azure App konfiguration som en extra konfigurations källa där dina funktions flaggor lagras.

1. Högerklicka på projektet och välj **Hantera NuGet-paket**. På fliken **Bläddra** söker du efter och lägger till följande NuGet-paket i projektet.
   - [Microsoft.Extensions.Configuration. AzureAppConfiguration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureAppConfiguration/) version 4.1.0 eller senare
   - [Microsoft. FeatureManagement](https://www.nuget.org/packages/Microsoft.FeatureManagement/) version 2.2.0 eller senare
   - [Microsoft. Azure. functions. Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/) version 1.1.0 eller senare 

2. Lägg till en ny fil, *startup. cs*, med följande kod. Den definierar en klass med namnet `Startup` som implementerar den `FunctionsStartup` abstrakta klassen. Ett Assembly-attribut används för att ange det typ namn som används vid Azure Functions start.

    ```csharp
    using System;
    using Microsoft.Azure.Functions.Extensions.DependencyInjection;
    using Microsoft.Extensions.Configuration;
    using Microsoft.FeatureManagement;

    [assembly: FunctionsStartup(typeof(FunctionApp.Startup))]

    namespace FunctionApp
    {
        class Startup : FunctionsStartup
        {
            public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
            {
            }

            public override void Configure(IFunctionsHostBuilder builder)
            {
            }
        }
    }
    ```


3. Uppdatera `ConfigureAppConfiguration` metoden och Lägg till Azure App Konfigurationsprovider som en extra konfigurations källa genom att anropa `AddAzureAppConfiguration()` . 

   `UseFeatureFlags()`Metoden instruerar providern att läsa in funktions flaggor. Alla funktions flaggor har en förfallo tid på 30 sekunder för cachen innan ändringar görs om efter ändringar. Du kan uppdatera förfallo intervallet genom att ange `FeatureFlagsOptions.CacheExpirationInterval` egenskapen som skickas till `UseFeatureFlags` metoden. 

    ```csharp
    public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
    {
        builder.ConfigurationBuilder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                   .Select("_")
                   .UseFeatureFlags();
        });
    }
    ```
   > [!TIP]
   > Om du inte vill att någon annan konfiguration än funktions flaggor ska läsas in i programmet kan du anropa `Select("_")` att bara läsa in en befintlig dummy-nyckel "_". Som standard kommer alla konfigurations nyckel värden i appens konfigurations Arkiv att läsas in om ingen `Select` metod anropas.

4. Uppdatera `Configure` metoden för att göra Azure App konfigurations tjänster och funktions hanteraren tillgängliga via beroende inmatning.

    ```csharp
    public override void Configure(IFunctionsHostBuilder builder)
    {
        builder.Services.AddAzureAppConfiguration();
        builder.Services.AddFeatureManagement();
    }
    ```

5. Öppna *Function1. cs* och Lägg till följande namn rymder.

    ```csharp
    using System.Linq;
    using Microsoft.FeatureManagement;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

   Lägg till en konstruktor som används för att hämta instanser av `_featureManagerSnapshot` och `IConfigurationRefresherProvider` genom beroende inmatning. Från kan `IConfigurationRefresherProvider` du hämta instansen av `IConfigurationRefresher` .

    ```csharp
    private readonly IFeatureManagerSnapshot _featureManagerSnapshot;
    private readonly IConfigurationRefresher _configurationRefresher;

    public Function1(IFeatureManagerSnapshot featureManagerSnapshot, IConfigurationRefresherProvider refresherProvider)
    {
        _featureManagerSnapshot = featureManagerSnapshot;
        _configurationRefresher = refresherProvider.Refreshers.First();
    }
    ```

6. Uppdatera `Run` metoden för att ändra värdet för det visade meddelandet beroende på funktions flaggans tillstånd.

   `TryRefreshAsync`Metoden anropas i början av funktions anropet för att uppdatera funktions flaggor. Det kommer inte att vara något-op om perioden för förfallo tid för cache inte har uppnåtts. Ta bort `await` operatorn om du vill att funktions flaggorna ska uppdateras utan att blockera aktuella funktions anrop. I så fall kommer senare Functions-anrop att få ett uppdaterat värde.

    ```csharp
    [FunctionName("Function1")]
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req,
        ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        await _configurationRefresher.TryRefreshAsync();

        string message = await _featureManagerSnapshot.IsEnabledAsync("Beta")
                ? "The Feature Flag 'Beta' is turned ON"
                : "The Feature Flag 'Beta' is turned OFF";

        return (ActionResult)new OkObjectResult(message);
    }
    ```

## <a name="test-the-function-locally"></a>Testa funktionen lokalt

1. Ange en miljö variabel med namnet **ConnectionString**, där värdet är den anslutnings sträng som du hämtade tidigare i konfigurations arkivet för appen under **åtkomst nycklar**. Om du använder kommando tolken i Windows kör du följande kommando och startar om kommando tolken för att ändringarna ska börja gälla:

    ```cmd
        setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Om du använder Windows PowerShell kör du följande kommando:

    ```azurepowershell
        $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Om du använder macOS eller Linux kör du följande kommando:

    ```bash
        export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

1. Tryck på F5 för att testa funktionen. Om du uppmanas att göra det accepterar du begäran från Visual Studio för att ladda ned och installera **Azure Functions Core-verktyg (CLI)** . Du kan också behöva aktivera ett brand Väggs undantag så att verktygen kan hantera HTTP-begäranden.

1. Kopiera URL:en för funktionen från dina Azure Functions-utdata.

    ![Snabbstart för funktionsfelsökning i VS](./media/quickstarts/function-visual-studio-debugging.png)

1. Klistra in webbadressen för HTTP-begäran i webbläsarens adressfält. Följande bild visar svaret som indikerar att funktions flaggan `Beta` är inaktive rad. 

    ![Funktions flagga för snabb starts funktion inaktiverat](./media/quickstarts/functions-launch-ff-disabled.png)

1. Logga in på [Azure-portalen](https://portal.azure.com). Välj **alla resurser** och välj det app konfigurations lager som du har skapat.

1. Välj **funktions hanteraren** och ändra statusen för **beta** nyckeln till **på**.

1. Uppdatera webbläsaren några gånger. När den cachelagrade funktions flaggan upphör att gälla efter 30 sekunder, bör sidan ha ändrats för att visa att funktions flaggan `Beta` är aktive rad, som du ser i bilden nedan.
 
    ![Funktions flagga för snabb starts funktion aktive rad](./media/quickstarts/functions-launch-ff-enabled.png)

> [!NOTE]
> Exempel koden som används i den här självstudien kan hämtas från [Azure App Configuration GitHub lagrings platsen](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction).

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du en funktions flagga och använde den med en Azure Functions-app via [Microsoft. FeatureManagement](/dotnet/api/microsoft.featuremanagement) -biblioteket.

- Läs mer om [funktions hantering](./concept-feature-management.md)
- [Hantera funktionsflaggor](./manage-feature-flags.md)
- [Använda villkorsstyrda funktionsflaggor](./howto-feature-filters-aspnet-core.md)
- [Aktivera stegvis distribution av funktioner för mål grupper](./howto-targetingfilter-aspnet-core.md)
- [Använd dynamisk konfiguration i en Azure Functions app](./enable-dynamic-configuration-azure-functions-csharp.md)