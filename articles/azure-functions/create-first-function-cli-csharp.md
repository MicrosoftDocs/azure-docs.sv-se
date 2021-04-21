---
title: Skapa en C#-funktion från kommandoraden – Azure Functions
description: Lär dig hur du skapar en C#-funktion från kommandoraden och sedan publicerar det lokala projektet till serverlös värd i Azure Functions.
ms.date: 10/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-csharp
- devx-track-azurecli
- devx-track-azurepowershell
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-cli-csharp-ieux
ms.openlocfilehash: e5a8a0a32196d4f4b988083f22930829418fa1b8
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832022"
---
# <a name="quickstart-create-a-c-function-in-azure-from-the-command-line"></a>Snabbstart: Skapa en C#-funktion i Azure från kommandoraden

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

I den här artikeln använder du kommandoradsverktyg för att skapa en biblioteksbaserad C#-klassfunktion som svarar på HTTP-begäranden. När du har testat koden lokalt distribuerar du den till den serverlösa Azure Functions.

Den här snabbstarten medför en liten kostnad på några cent eller mindre för ditt Azure-konto.

Det finns även en [Visual Studio kodbaserad version av](create-first-function-vs-code-csharp.md) den här artikeln.

## <a name="configure-your-local-environment"></a>Konfigurera din lokala miljö

Innan du börjar måste du ha följande:

+ Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ Följande [.NET Core SDK 3.1](https://www.microsoft.com/net/download)

+ Den [Azure Functions Core Tools](functions-run-local.md#v2) version 3.x.

+ Ett av följande verktyg för att skapa Azure-resurser:

    + [Azure CLI](/cli/azure/install-azure-cli) version 2.4 eller senare.

    + [Azure PowerShell](/powershell/azure/install-az-ps) version 5.0 eller senare.

### <a name="prerequisite-check"></a>Kravkontroll

Kontrollera kraven, som beror på om du använder Azure CLI eller Azure PowerShell för att skapa Azure-resurser:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ I en terminal eller ett kommandofönster kör `func --version` du för att kontrollera Azure Functions Core Tools version 3.x.

+ Kör `az --version` för att kontrollera att Azure CLI-versionen är 2.4 eller senare.

+ Kör `az login` för att logga in på Azure och verifiera en aktiv prenumeration.

+ Kör `dotnet --list-sdks` för att kontrollera .NET Core SDK version 3.1.x är installerad

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ I en terminal eller ett kommandofönster kör `func --version` du för att kontrollera Azure Functions Core Tools version 3.x.

+ Kör `(Get-Module -ListAvailable Az).Version` och verifiera version 5.0 eller senare. 

+ Kör `Connect-AzAccount` för att logga in på Azure och verifiera en aktiv prenumeration.

+ Kör `dotnet --list-sdks` för att kontrollera .NET Core SDK version 3.1.x är installerad

---

## <a name="create-a-local-function-project"></a>Skapa ett lokalt funktionsprojekt

I Azure Functions är ett funktionsprojekt en container för en eller flera enskilda funktioner som var och en svarar på en specifik utlösare. Alla funktioner i ett projekt delar samma lokala konfigurationer och värdkonfigurationer. I det här avsnittet skapar du ett funktionsprojekt som innehåller en enda funktion.

1. Kör kommandot på följande sätt för att skapa ett functions-projekt i en mapp med namnet `func init` *LocalFunctionProj* med den angivna körningen:  

    ```csharp
    func init LocalFunctionProj --dotnet
    ```

1. Navigera till projektmappen:

    ```console
    cd LocalFunctionProj
    ```

    Den här mappen innehåller olika filer för projektet, inklusive konfigurationsfiler med [local.settings.jspå](functions-run-local.md#local-settings-file) och [host.jspå](functions-host-json.md). Eftersom *local.settings.jspå* kan innehålla hemligheter som laddats ned från Azure undantas filen från källkontrollen som standard i *.gitignore-filen.*

1. Lägg till en funktion i projektet med hjälp av följande kommando, där argumentet är det unika namnet på funktionen `--name` (HttpExample) och `--template` argumentet anger funktionens utlösare (HTTP).

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 

    `func new` skapar en HttpExample.cs-kodfil.

### <a name="optional-examine-the-file-contents"></a>(Valfritt) Granska filinnehållet

Om du vill kan du gå [vidare till Kör funktionen lokalt](#run-the-function-locally) och undersöka filinnehållet senare.

#### <a name="httpexamplecs"></a>HttpExample.cs

*HttpExample.cs* innehåller en metod som tar emot begärandedata i variabeln är en HttpRequest som är prydlig med `Run` `req` **HttpTriggerAttribute**, som definierar utlösarbeteendet. [](/dotnet/api/microsoft.aspnetcore.http.httprequest)

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::

Returobjektet är ett [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) som returnerar ett svarsmeddelande som antingen [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) eller [Ett BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). Mer information finns i Azure Functions [HTTP-utlösare och bindningar](./functions-bindings-http-webhook.md?tabs=csharp).

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-create-azure-resources-cli](../../includes/functions-create-azure-resources-cli.md)]

4. Skapa funktionsappen i Azure:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    Kommandot [az functionapp create](/cli/azure/functionapp#az_functionapp_create) skapar funktionsappen i Azure. 
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime dotnet -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    Cmdleten [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) skapar funktionsappen i Azure. 
    
    ---
    
    I föregående exempel ersätter du med namnet på det konto som du använde i föregående steg och ersätter med ett `<STORAGE_NAME>` globalt unikt namn som passar `<APP_NAME>` dig. `<APP_NAME>` är även DNS-standarddomänen för funktionsappen. 
    
    Det här kommandot skapar en funktionsapp som körs i din angivna språkkörning under [Azure Functions Consumption Plan](consumption-plan.md), vilket är kostnadsfritt för den användning du ådrar dig här. Kommandot tiller även en associerad Azure Application Insights-instans i samma resursgrupp som du kan använda för att övervaka funktionsappen och visa loggar. Mer information finns i [Övervaka Azure Functions](functions-monitoring.md). Instansen medför inga kostnader förrän du aktiverar den.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Ansluta till en Azure Storage kö]

[Ansluta till en Azure Storage kö]: functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-csharp
