---
title: Självstudie för att använda Azure App dynamisk konfiguration dynamisk konfiguration i en Azure Functions app | Microsoft Docs
description: I den här självstudien får du lära dig att dynamiskt uppdatera konfigurations data för Azure Functions appar
services: azure-app-configuration
documentationcenter: ''
author: zhenlan
manager: qingye
editor: ''
ms.assetid: ''
ms.service: azure-app-configuration
ms.workload: tbd
ms.devlang: csharp
ms.topic: tutorial
ms.date: 11/17/2019
ms.author: zhenlwa
ms.custom: devx-track-csharp, azure-functions
ms.tgt_pltfrm: Azure Functions
ms.openlocfilehash: add4b54adb02db09536f4e56a7f039c46245c182
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97963572"
---
# <a name="tutorial-use-dynamic-configuration-in-an-azure-functions-app"></a>Självstudie: Använd dynamisk konfiguration i en Azure Functions app

Konfigurations leverantören för app Configuration .NET har stöd för cachelagring och uppdatering av konfigurationen dynamiskt av program aktiviteten. Den här självstudien visar hur du kan implementera dynamiska konfigurationsuppdateringar i koden. Den bygger på den Azure Functions app som introducerades i snabb starterna. Innan du fortsätter måste du [skapa en Azure Functions-app med Azure App-konfigurationen](./quickstart-azure-functions-csharp.md) först.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera din Azure Functions-app för att uppdatera konfigurationen som svar på ändringar i ett konfigurations lager för appar.
> * Mata in den senaste konfigurationen till dina Azure Functions-anrop.

## <a name="prerequisites"></a>Förutsättningar

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) med arbets belastningen **Azure Development**
- [Azure Functions verktyg](../azure-functions/functions-develop-vs.md#check-your-tools-version)
- Slutför snabb start [skapa en Azure Functions-app med Azure App konfiguration](./quickstart-azure-functions-csharp.md)

## <a name="reload-data-from-app-configuration"></a>Läsa in data på nytt från App Configuration

1. Öppna *startup. cs* och uppdatera `ConfigureAppConfiguration` metoden. 

   `ConfigureRefresh`Metoden registrerar en inställning för att kontrol lera ändringar varje gång en uppdatering utlöses i programmet, vilket du gör i det senare steget när du lägger till `_configurationRefresher.TryRefreshAsync()` . `refreshAll`Parametern instruerar appens konfigurations leverantör att läsa in hela konfigurationen igen varje gång en ändring identifieras i den registrerade inställningen.

    Alla inställningar som har registrerats för uppdatering har ett förfallo datum för cachen på 30 sekunder. Den kan uppdateras genom att anropa- `AzureAppConfigurationRefreshOptions.SetCacheExpiration` metoden.

    ```csharp
    public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
    {
        builder.ConfigurationBuilder.AddAzureAppConfiguration(options =>
        {
            options.Connect(Environment.GetEnvironmentVariable("ConnectionString"))
                   // Load all keys that start with `TestApp:`
                   .Select("TestApp:*")
                   // Configure to reload configuration if the registered 'Sentinel' key is modified
                   .ConfigureRefresh(refreshOptions =>
                      refreshOptions.Register("TestApp:Settings:Sentinel", refreshAll: true));
        });
    }
    ```

   > [!TIP]
   > När du uppdaterar flera nyckel värden i app-konfigurationen vill du normalt inte att ditt program ska läsa in konfigurationen igen innan alla ändringar görs. Du kan registrera en **kontroll** nyckel och bara uppdatera den när alla andra konfigurations ändringar har slutförts. Detta hjälper till att säkerställa att konfigurationen är konsekvent i ditt program.

2. Uppdatera `Configure` metoden för att göra Azure App konfigurations tjänster tillgängliga via beroende inmatning.

    ```csharp
    public override void Configure(IFunctionsHostBuilder builder)
    {
        builder.Services.AddAzureAppConfiguration();
    }
    ```

3. Öppna *Function1. cs* och Lägg till följande namn rymder.

    ```csharp
    using System.Linq;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

   Uppdatera konstruktorn för att hämta instansen för `IConfigurationRefresherProvider` genom beroende insprutning, från vilken du kan hämta instansen av `IConfigurationRefresher` .

    ```csharp
    private readonly IConfiguration _configuration;
    private readonly IConfigurationRefresher _configurationRefresher;

    public Function1(IConfiguration configuration, IConfigurationRefresherProvider refresherProvider)
    {
        _configuration = configuration;
        _configurationRefresher = refresherProvider.Refreshers.First();
    }
    ```

4. Uppdatera `Run` metoden och signalen för att uppdatera konfigurationen med `TryRefreshAsync` metoden i början av funktions anropet. Det kommer inte att vara något-op om perioden för förfallo tid för cache inte har uppnåtts. Ta bort `await` operatorn om du vill att konfigurationen ska uppdateras utan att blockera det aktuella funktions anropet. I så fall kommer senare Functions-anrop att få ett uppdaterat värde.

    ```csharp
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        await _configurationRefresher.TryRefreshAsync(); 

        string keyName = "TestApp:Settings:Message";
        string message = _configuration[keyName];
            
        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>Testa funktionen lokalt

1. Ange en miljö variabel med namnet **ConnectionString** och ange den till åtkomst nyckeln till appens konfigurations arkiv. Om du använder kommando tolken i Windows kör du följande kommando och startar om kommando tolken för att ändringarna ska börja gälla:

    ```console
    setx ConnectionString "connection-string-of-your-app-configuration-store"
    ```

    Om du använder Windows PowerShell kör du följande kommando:

    ```powershell
    $Env:ConnectionString = "connection-string-of-your-app-configuration-store"
    ```

    Om du använder macOS eller Linux kör du följande kommando:

    ```console
    export ConnectionString='connection-string-of-your-app-configuration-store'
    ```

2. Tryck på F5 för att testa funktionen. Om du uppmanas att göra det accepterar du begäran från Visual Studio för att ladda ned och installera **Azure Functions Core-verktyg (CLI)** . Du kan också behöva aktivera ett brand Väggs undantag så att verktygen kan hantera HTTP-begäranden.

3. Kopiera URL:en för funktionen från dina Azure Functions-utdata.

    ![Snabbstart för funktionsfelsökning i VS](./media/quickstarts/function-visual-studio-debugging.png)

4. Klistra in webbadressen för HTTP-begäran i webbläsarens adressfält. Följande bild visar svaret i webbläsaren till den lokala GET-begäran som returnerades av funktionen.

    ![Snabbstart för lokal funktionsstart](./media/quickstarts/dotnet-core-function-launch-local.png)

5. Logga in på [Azure-portalen](https://portal.azure.com). Välj **alla resurser** och välj det app konfigurations lager som du skapade i snabb starten.

6. Välj **Configuration Explorer** och uppdatera värdet för följande nyckel:

    | Tangent | Värde |
    |---|---|
    | TestApp:Settings:Message | Data från Azure App konfiguration – uppdaterad |

   Skapa sedan kontroll nyckeln eller ändra dess värde om det redan finns, till exempel

    | Tangent | Värde |
    |---|---|
    | TestApp: inställningar: Sentinel | v1 |


7. Uppdatera webbläsaren några gånger. När den cachelagrade inställningen upphör att gälla efter 30 sekunder, visar sidan svaret på funktions anropet med det uppdaterade värdet.

    ![Snabb starts funktion uppdatera lokal](./media/quickstarts/dotnet-core-function-refresh-local.png)

> [!NOTE]
> Exempel koden som används i den här självstudien kan hämtas från [app Configuration GitHub lagrings platsen](https://github.com/Azure/AppConfiguration/tree/master/examples/DotNetCore/AzureFunction).

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du aktiverat Azure Functions-appen för dynamisk uppdatering av konfigurations inställningar från App-konfigurationen. Fortsätt till nästa självstudie om du vill lära dig hur du använder en Azure-hanterad identitet för att effektivisera åtkomsten till app-konfigurationen.

> [!div class="nextstepaction"]
> [Hanterad identitets integrering](./howto-integrate-azure-managed-service-identity.md)
