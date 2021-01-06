---
title: Skapa en C#-funktion från kommando raden – Azure Functions
description: Lär dig hur du skapar en C#-funktion från kommando raden och sedan publicerar det lokala projektet till Server lös värd i Azure Functions.
ms.date: 10/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-csharp
- devx-track-azurecli
ms.openlocfilehash: 19d38bb5933a6acf82c61f0a739bf2adb7f34eaf
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937271"
---
# <a name="quickstart-create-a-c-function-in-azure-from-the-command-line"></a>Snabb start: skapa en C#-funktion i Azure från kommando raden

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

I den här artikeln använder du kommando rads verktyg för att skapa en C#-baserad funktion i C#-klassen som svarar på HTTP-begäranden. När du har testat koden lokalt distribuerar du den till den serverbaserade miljön för Azure Functions.

Att slutföra den här snabb starten innebär en låg kostnad av några USD cent eller mindre i ditt Azure-konto.

Det finns också en [Visual Studio Code-baserad version](create-first-function-vs-code-csharp.md) av den här artikeln.

## <a name="configure-your-local-environment"></a>Konfigurera din lokala miljö

Innan du börjar måste du ha följande:

+ Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [.NET Core SDK 3,1](https://www.microsoft.com/net/download)

+ [Azure Functions Core tools](functions-run-local.md#v2) version 3. x.

+ Ett av följande verktyg för att skapa Azure-resurser:

    + [Azure CLI](/cli/azure/install-azure-cli) version 2,4 eller senare.

    + [Azure PowerShell](/powershell/azure/install-az-ps) version 5,0 eller senare.

### <a name="prerequisite-check"></a>Krav kontroll

Verifiera dina krav, beroende på om du använder Azure CLI eller Azure PowerShell för att skapa Azure-resurser:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ I ett terminalfönster eller kommando fönster, kör `func --version` för att kontrol lera att Azure Functions Core Tools är version 3. x.

+ Kör `az --version` för att kontrol lera att Azure CLI-versionen är 2,4 eller senare.

+ Kör `az login` för att logga in på Azure och verifiera en aktiv prenumeration.

+ Kör `dotnet --list-sdks` för att kontrol lera att .net Core SDK version 3.1. x har installerats

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ I ett terminalfönster eller kommando fönster, kör `func --version` för att kontrol lera att Azure Functions Core Tools är version 3. x.

+ Kör `(Get-Module -ListAvailable Az).Version` och verifiera version 5,0 eller senare. 

+ Kör `Connect-AzAccount` för att logga in på Azure och verifiera en aktiv prenumeration.

+ Kör `dotnet --list-sdks` för att kontrol lera att .net Core SDK version 3.1. x har installerats

---

## <a name="create-a-local-function-project"></a>Skapa ett lokalt funktions projekt

I Azure Functions är ett funktions projekt en behållare för en eller flera enskilda funktioner som varje svarar på en viss utlösare. Alla funktioner i ett projekt delar samma lokala och värdbaserade konfigurationer. I det här avsnittet skapar du ett funktions projekt som innehåller en enda funktion.

1. Kör `func init` kommandot enligt följande för att skapa ett Functions-projekt i en mapp med namnet *LocalFunctionProj* med den angivna körnings miljön:  

    ```csharp
    func init LocalFunctionProj --dotnet
    ```

1. Navigera till projektmappen:

    ```console
    cd LocalFunctionProj
    ```

    Den här mappen innehåller olika filer för projektet, inklusive konfigurationsfiler som heter [local.settings.jspå](functions-run-local.md#local-settings-file) och [host.js](functions-host-json.md). Eftersom *local.settings.jspå* kan innehålla hemligheter som hämtats från Azure, undantas filen från käll kontroll som standard i *. gitignore* -filen.

1. Lägg till en funktion i projektet med hjälp av följande kommando, där `--name` argumentet är det unika namnet för din funktion (HttpExample) och `--template` argumentet anger funktionens utlösare (http).

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 

    `func new` skapar en HttpExample.cs-kod fil.

### <a name="optional-examine-the-file-contents"></a>Valfritt Granska fil innehållet

Om du vill kan du hoppa över att [köra funktionen lokalt](#run-the-function-locally) och undersöka fil innehållet senare.

#### <a name="httpexamplecs"></a>HttpExample.cs

*HttpExample.cs* innehåller en `Run` metod som tar emot begär ande data i `req` variabeln är en [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest) som är dekorerad med **HttpTriggerAttribute**, vilket definierar utlösnings beteendet.

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::

Returvärdet är en [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) som returnerar ett svarsmeddelande som antingen en [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) eller en [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). Läs mer i [Azure Functions HTTP-utlösare och bindningar](./functions-bindings-http-webhook.md?tabs=csharp).

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-create-azure-resources-cli](../../includes/functions-create-azure-resources-cli.md)]

4. Skapa Function-appen i Azure:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    Kommandot [AZ functionapp Create](/cli/azure/functionapp#az_functionapp_create) skapar Function-appen i Azure. 
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime dotnet -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    Cmdlet: en [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) skapar Function-appen i Azure. 
    
    ---
    
    I föregående exempel ersätter `<STORAGE_NAME>` du med namnet på det konto som du använde i föregående steg och ersätter `<APP_NAME>` med ett globalt unikt namn som passar dig. `<APP_NAME>` är även DNS-standarddomänen för funktionsappen. 
    
    Det här kommandot skapar en Function-app som körs på den angivna språk körningen under [Azure Functions förbruknings plan](consumption-plan.md), vilket är kostnads fritt för den användnings mängd som du ådrar dig här. Kommandot etablerar även en associerad Azure Application insikts-instans i samma resurs grupp, som du kan använda för att övervaka din Function-app och Visa loggar. Mer information finns i [övervaka Azure Functions](functions-monitoring.md). Instansen debiteras inga kostnader förrän du aktiverar den.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Ansluta till en Azure Storage-kö]

[Ansluta till en Azure Storage-kö]: functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-csharp
