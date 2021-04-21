---
title: Skapa en JavaScript-funktion från kommandoraden – Azure Functions
description: Lär dig hur du skapar en JavaScript-funktion från kommandoraden och sedan publicerar det lokala Node.js-projektet till serverlös värd i Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: eb162bcff647e9e1dbced19ddea33a8f3c54b58e
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831950"
---
# <a name="quickstart-create-a-javascript-function-in-azure-from-the-command-line"></a>Snabbstart: Skapa en JavaScript-funktion i Azure från kommandoraden


[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

I den här artikeln använder du kommandoradsverktyg för att skapa en JavaScript-funktion som svarar på HTTP-begäranden. När du har testat koden lokalt distribuerar du den till den serverlösa Azure Functions. 

Den här snabbstarten medför en liten kostnad på några cent eller mindre för ditt Azure-konto.

Det finns även en [Visual Studio kodbaserad version av](create-first-function-vs-code-node.md) den här artikeln.

## <a name="configure-your-local-environment"></a>Konfigurera din lokala miljö

Innan du börjar måste du ha följande:

+ Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ Den [Azure Functions Core Tools](./functions-run-local.md#v2) version 3.x.

+ Ett av följande verktyg för att skapa Azure-resurser:

    + [Azure CLI](/cli/azure/install-azure-cli) version 2.4 eller senare.

    + [Azure PowerShell](/powershell/azure/install-az-ps) version 5.0 eller senare.

+ [Node.js](https://nodejs.org/) version 12. Node.js version 10 stöds också.

### <a name="prerequisite-check"></a>Kravkontroll

Kontrollera kraven, som beror på om du använder Azure CLI eller Azure PowerShell för att skapa Azure-resurser:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ I en terminal eller ett kommandofönster kör `func --version` du för att kontrollera Azure Functions Core Tools version 3.x.

+ Kör `az --version` för att kontrollera att Azure CLI-versionen är 2.4 eller senare.

+ Kör `az login` för att logga in på Azure och verifiera en aktiv prenumeration.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ I en terminal eller ett kommandofönster kör `func --version` du för att kontrollera Azure Functions Core Tools version 3.x.

+ Kör `(Get-Module -ListAvailable Az).Version` och verifiera version 5.0 eller senare. 

+ Kör `Connect-AzAccount` för att logga in på Azure och verifiera en aktiv prenumeration.

---

## <a name="create-a-local-function-project"></a>Skapa ett lokalt funktionsprojekt

I Azure Functions är ett funktionsprojekt en container för en eller flera enskilda funktioner som var och en svarar på en specifik utlösare. Alla funktioner i ett projekt delar samma lokala konfigurationer och värdkonfigurationer. I det här avsnittet skapar du ett funktionsprojekt som innehåller en enda funktion.

1. Kör kommandot på följande sätt för att skapa ett functions-projekt i en mapp med namnet `func init` *LocalFunctionProj* med den angivna körningen:  

    ```console
    func init LocalFunctionProj --javascript
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
    
    `func new` skapar en undermapp som matchar funktionsnamnet som innehåller en kodfil som är lämplig för projektets valda språk och en konfigurationsfil med *namnetfunction.jspå*.
    
### <a name="optional-examine-the-file-contents"></a>(Valfritt) Granska filinnehållet

Om du vill kan du gå [vidare till Kör funktionen lokalt](#run-the-function-locally) och undersöka filinnehållet senare.

#### <a name="indexjs"></a>index.js

*index.js* exporterar en funktion som utlöses enligt konfigurationen i *function.jspå*.

:::code language="javascript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/index.js":::

För en HTTP-utlösare tar funktionen emot begärandedata i `req` variabeln enligt definitionen *ifunction.jspå*. Svaret definieras som `res` i *function.jspå* och kan nås med hjälp av `context.res` . Mer information finns i Azure Functions [HTTP-utlösare och bindningar](./functions-bindings-http-webhook.md?tabs=javascript).

#### <a name="functionjson"></a>function.json

*function.jspå är* en konfigurationsfil som definierar indata och `bindings` utdata för funktionen, inklusive utlösartypen. 

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json":::

Varje bindning kräver en riktning, en typ och ett unikt namn. HTTP-utlösaren har en indatabindning av typen [`httpTrigger`](functions-bindings-http-webhook-trigger.md) och utdatabindning av typen [`http`](functions-bindings-http-webhook-output.md) .

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-create-azure-resources-cli](../../includes/functions-create-azure-resources-cli.md)]

4. Skapa funktionsappen i Azure:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime node --runtime-version 12 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    Kommandot [az functionapp create](/cli/azure/functionapp#az_functionapp_create) skapar funktionsappen i Azure. Om du använder Node.js 10 ändrar du även `--runtime-version` till `10` .
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime node -RuntimeVersion 12 -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    Cmdleten [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) skapar funktionsappen i Azure. Om du använder Node.js 10 ändrar du `-RuntimeVersion` till `10` .
    
    ---
    
    I föregående exempel ersätter du med namnet på det konto som du använde i föregående steg och ersätter med ett `<STORAGE_NAME>` globalt unikt namn som passar `<APP_NAME>` dig. `<APP_NAME>` är även DNS-standarddomänen för funktionsappen. 
    
    Det här kommandot skapar en funktionsapp som körs i din angivna språkkörning under [Azure Functions Consumption Plan](consumption-plan.md), vilket är kostnadsfritt för den användning du ådrar dig här. Kommandot tiller även en associerad Azure Application Insights-instans i samma resursgrupp som du kan använda för att övervaka funktionsappen och visa loggar. Mer information finns i [Övervaka Azure Functions](functions-monitoring.md). Instansen medför inga kostnader förrän du aktiverar den.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Ansluta till en Azure Storage kö](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-javascript)
