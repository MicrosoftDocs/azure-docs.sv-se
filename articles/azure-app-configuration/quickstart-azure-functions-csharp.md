---
title: Snabbstart för Azure App Configuration med Azure Functions | Microsoft Docs
description: I den här snabb starten ska du göra en Azure Functions-app med Azure App konfiguration och C#. Skapa och Anslut till ett konfigurations Arkiv för appar. Testa funktionen lokalt.
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.custom: devx-track-csharp
ms.topic: quickstart
ms.date: 1/9/2019
ms.author: lcozzens
ms.openlocfilehash: e15c83504ee0eebb925c122d0efd4896e7b55916
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590293"
---
# <a name="quickstart-create-an-azure-functions-app-with-azure-app-configuration"></a>Snabb start: skapa en Azure Functions-app med Azure App konfiguration

I den här snabb starten införlivar du Azure App konfigurations tjänsten i en Azure Functions-app för att centralisera lagring och hantering av alla dina program inställningar separat från din kod.

## <a name="prerequisites"></a>Förutsättningar

- Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/)
- [Visual Studio 2019](https://visualstudio.microsoft.com/vs) med arbets belastningen **Azure Development** .
- [Azure Functions verktyg](../azure-functions/functions-develop-vs.md#check-your-tools-version)

## <a name="create-an-app-configuration-store"></a>Skapa ett konfigurations Arkiv för appen

[!INCLUDE [azure-app-configuration-create](../../includes/azure-app-configuration-create.md)]

6. Välj **Configuration Explorer**  >  **+ skapa**  >  **nyckel värde** om du vill lägga till följande nyckel/värde-par:

    | Tangent | Värde |
    |---|---|
    | TestApp:Settings:Message | Data från Azure App Configuration |

    Lämna **etiketten** och **innehålls typen** tom för tillfället.

7. Välj **Tillämpa**.

## <a name="create-a-functions-app"></a>Skapa en Functions-app

[!INCLUDE [Create a project using the Azure Functions template](../../includes/functions-vstools-create.md)]

## <a name="connect-to-an-app-configuration-store"></a>Anslut till ett konfigurations Arkiv för appen

1. Högerklicka på projektet och välj **Hantera NuGet-paket**. På fliken **Bläddra** söker du efter och lägger till `Microsoft.Extensions.Configuration.AzureAppConfiguration` NuGet-paketet i projektet. Om du inte hittar det markerar du kryss rutan **Inkludera för hands version** .

2. Öppna *Function1.cs*och Lägg till namn områdena för .net Core-konfigurationen och konfigurations leverantören för app Configuration.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using Microsoft.Extensions.Configuration.AzureAppConfiguration;
    ```

3. Lägg till en `static` egenskap med namnet `Configuration` för att skapa en singleton-instans av `IConfiguration` . Lägg sedan till en `static` konstruktor för att ansluta till app-konfigurationen genom att anropa `AddAzureAppConfiguration()` . Konfigurationen läses in en gång när programmet startas. Samma konfigurations instans kommer att användas för alla funktions anrop senare.

    ```csharp
    private static IConfiguration Configuration { set; get; }

    static Function1()
    {
        var builder = new ConfigurationBuilder();
        builder.AddAzureAppConfiguration(Environment.GetEnvironmentVariable("ConnectionString"));
        Configuration = builder.Build();
    }
    ```

4. Uppdatera `Run` metoden för att läsa värden från konfigurationen.

    ```csharp
    public static async Task<IActionResult> Run(
        [HttpTrigger(AuthorizationLevel.Anonymous, "get", "post", Route = null)] HttpRequest req, ILogger log)
    {
        log.LogInformation("C# HTTP trigger function processed a request.");

        string keyName = "TestApp:Settings:Message";
        string message = Configuration[keyName];

        return message != null
            ? (ActionResult)new OkObjectResult(message)
            : new BadRequestObjectResult($"Please create a key-value with the key '{keyName}' in App Configuration.");
    }
    ```

## <a name="test-the-function-locally"></a>Testa funktionen lokalt

1. Ange en miljö variabel med namnet **ConnectionString**och ange den till åtkomst nyckeln till appens konfigurations arkiv. Om du använder kommando tolken i Windows kör du följande kommando och startar om kommando tolken för att ändringarna ska börja gälla:

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

I den här snabb starten skapade du ett nytt konfigurations Arkiv för appar och använde det med en Azure Functions app via [appens Konfigurationsprovider](https://go.microsoft.com/fwlink/?linkid=2074664). Fortsätt till nästa självstudie om du vill veta hur du konfigurerar din Azure Functions-app för att dynamiskt uppdatera konfigurations inställningar.

> [!div class="nextstepaction"]
> [Aktivera dynamisk konfiguration](./enable-dynamic-configuration-azure-functions-csharp.md)
