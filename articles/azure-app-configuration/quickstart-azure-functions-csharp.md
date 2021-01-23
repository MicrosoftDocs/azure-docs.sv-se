---
title: Snabbstart för Azure App Configuration med Azure Functions | Microsoft Docs
description: I den här snabb starten ska du göra en Azure Functions-app med Azure App konfiguration och C#. Skapa och Anslut till ett konfigurations Arkiv för appar. Testa funktionen lokalt.
services: azure-app-configuration
author: AlexandraKemperMS
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 09/28/2020
ms.author: alkemper
ms.openlocfilehash: 9d378b21132e6646329c459401255ef9a3ed9426
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2021
ms.locfileid: "98724254"
---
# <a name="quickstart-create-an-azure-functions-app-with-azure-app-configuration"></a>Snabb start: skapa en Azure Functions-app med Azure App konfiguration

I den här snabb starten införlivar du Azure App konfigurations tjänsten i en Azure Functions-app för att centralisera lagring och hantering av alla dina program inställningar separat från din kod.

## <a name="prerequisites"></a>Krav

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/dotnet)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) med arbets belastningen **Azure Development** .
- [Azure Functions verktyg](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>Skapa ett konfigurations Arkiv för appen

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

7. Välj **Configuration Explorer**  >  **+ skapa**  >  **nyckel värde** om du vill lägga till följande nyckel/värde-par:

    | Tangent | Värde |
    |---|---|
    | TestApp:Settings:Message | Data från Azure App Configuration |

    Lämna **etiketten** och **innehålls typen** tom för tillfället.

8. Välj **Använd**.

## <a name="create-a-functions-app"></a>Skapa en Functions-app

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>Anslut till ett konfigurations Arkiv för appen
Det här projektet kommer att använda [beroende inmatning i .net Azure Functions](../azure-functions/functions-dotnet-dependency-injection.md) och lägga till Azure App konfiguration som en extra konfigurations källa.

1. Högerklicka på projektet och välj **Hantera NuGet-paket**. På fliken **Bläddra** söker du efter och lägger till följande NuGet-paket i projektet.
   - [Microsoft.Extensions.Configuration. AzureAppConfiguration](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.AzureAppConfiguration/) version 4.1.0 eller senare
   - [Microsoft. Azure. functions. Extensions](https://www.nuget.org/packages/Microsoft.Azure.Functions.Extensions/) version 1.1.0 eller senare 

2. Lägg till en ny fil, *startup.cs*, med följande kod. Den definierar en klass med namnet `Startup` som implementerar den `FunctionsStartup` abstrakta klassen. Ett Assembly-attribut används för att ange det typ namn som används vid Azure Functions start.

    `ConfigureAppConfiguration`Metoden åsidosätts och Azure App Konfigurationsprovider läggs till som en extra konfigurations källa genom att anropa `AddAzureAppConfiguration()` . `Configure`Metoden lämnas tom eftersom du inte behöver registrera några tjänster i det här läget.
    
    ```csharp
    using System;
    using Microsoft.Azure.Functions.Extensions.DependencyInjection;
    using Microsoft.Extensions.Configuration;

    [assembly: FunctionsStartup(typeof(FunctionApp.Startup))]

    namespace FunctionApp
    {
        class Startup : FunctionsStartup
        {
            public override void ConfigureAppConfiguration(IFunctionsConfigurationBuilder builder)
            {
                string cs = Environment.GetEnvironmentVariable("ConnectionString");
                builder.ConfigurationBuilder.AddAzureAppConfiguration(cs);
            }

            public override void Configure(IFunctionsHostBuilder builder)
            {
            }
        }
    }
    ```

3. Öppna *Function1.cs* och Lägg till följande namnrymd.

    ```csharp
    using Microsoft.Extensions.Configuration;
    ```

   Lägg till en konstruktor som används för att hämta en instans av `IConfiguration` genom beroende inmatning.

    ```csharp
    private readonly IConfiguration _configuration;

    public Function1(IConfiguration configuration)
    {
        _configuration = configuration;
    }
    ```

4. Uppdatera `Run` metoden för att läsa värden från konfigurationen.

    ```csharp
    public async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        string keyName = "TestApp:Settings:Message";
        string message = _configuration[keyName];

        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

   `Function1`Klassen och `Run` metoden får inte vara statiska. Ta bort `static` modifieraren om den skapades automatiskt.

## <a name="test-the-function-locally"></a>Testa funktionen lokalt

1. Ange en miljö variabel med namnet **ConnectionString** och ange den till åtkomst nyckeln till appens konfigurations arkiv. Om du använder kommando tolken i Windows kör du följande kommando och startar om kommando tolken för att ändringarna ska börja gälla:

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

2. Tryck på F5 för att testa funktionen. Om du uppmanas att göra det accepterar du begäran från Visual Studio för att ladda ned och installera **Azure Functions Core-verktyg (CLI)** . Du kan också behöva aktivera ett brand Väggs undantag så att verktygen kan hantera HTTP-begäranden.

3. Kopiera URL:en för funktionen från dina Azure Functions-utdata.

    ![Snabbstart för funktionsfelsökning i VS](./media/quickstarts/function-visual-studio-debugging.png)

4. Klistra in webbadressen för HTTP-begäran i webbläsarens adressfält. Följande bild visar svaret i webbläsaren till den lokala GET-begäran som returnerades av funktionen.

    ![Snabbstart för lokal funktionsstart](./media/quickstarts/dotnet-core-function-launch-local.png)

## <a name="clean-up-resources"></a>Rensa resurser

[!INCLUDE [azure-app-configuration-cleanup](../../includes/azure-app-configuration-cleanup.md)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten skapade du ett nytt konfigurations Arkiv för appar och använde det med en Azure Functions app via [appens Konfigurationsprovider](/dotnet/api/Microsoft.Extensions.Configuration.AzureAppConfiguration). Fortsätt till nästa självstudie om du vill veta hur du uppdaterar din Azure Functions-app för att dynamiskt uppdatera konfigurationen.

> [!div class="nextstepaction"]
> [Aktivera dynamisk konfiguration i Azure Functions](./enable-dynamic-configuration-azure-functions-csharp.md)