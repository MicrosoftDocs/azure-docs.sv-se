---
title: Skapa en PowerShell-funktion från kommando raden – Azure Functions
description: Lär dig hur du skapar en PowerShell-funktion från kommando raden och sedan publicerar det lokala projektet till Server lös värd i Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-powershell
- devx-track-azurecli
ms.openlocfilehash: abbe3b9ed4d9a8c9bf30c6be3e6980228d319090
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "97937237"
---
# <a name="quickstart-create-a-powershell-function-in-azure-from-the-command-line"></a>Snabb start: skapa en PowerShell-funktion i Azure från kommando raden

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

I den här artikeln använder du kommando rads verktyg för att skapa en PowerShell-funktion som svarar på HTTP-begäranden. När du har testat koden lokalt distribuerar du den till den serverbaserade miljön för Azure Functions.

Att slutföra den här snabb starten innebär en låg kostnad av några USD cent eller mindre i ditt Azure-konto.

Det finns också en [Visual Studio Code-baserad version](create-first-function-vs-code-powershell.md) av den här artikeln.

## <a name="configure-your-local-environment"></a>Konfigurera din lokala miljö

Innan du börjar måste du ha följande:

+ Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core tools](functions-run-local.md#v2) version 3. x.

+ Ett av följande verktyg för att skapa Azure-resurser:

    + [Azure PowerShell](/powershell/azure/install-az-ps) version 5,0 eller senare.

    + [Azure CLI](/cli/azure/install-azure-cli) version 2,4 eller senare.

+ [.NET Core SDK 3,1](https://www.microsoft.com/net/download)

### <a name="prerequisite-check"></a>Krav kontroll

Verifiera dina krav, beroende på om du använder Azure CLI eller Azure PowerShell för att skapa Azure-resurser:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ I ett terminalfönster eller kommando fönster, kör `func --version` för att kontrol lera att Azure Functions Core Tools är version 3. x.

+ Kör `az --version` för att kontrol lera att Azure CLI-versionen är 2,4 eller senare.

+ Kör `az login` för att logga in på Azure och verifiera en aktiv prenumeration.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ I ett terminalfönster eller kommando fönster, kör `func --version` för att kontrol lera att Azure Functions Core Tools är version 3. x.

+ Kör `(Get-Module -ListAvailable Az).Version` och verifiera version 5,0 eller senare. 

+ Kör `Connect-AzAccount` för att logga in på Azure och verifiera en aktiv prenumeration.

---

## <a name="create-a-local-function-project"></a>Skapa ett lokalt funktions projekt

I Azure Functions är ett funktions projekt en behållare för en eller flera enskilda funktioner som varje svarar på en viss utlösare. Alla funktioner i ett projekt delar samma lokala och värdbaserade konfigurationer. I det här avsnittet skapar du ett funktions projekt som innehåller en enda funktion.

1. Kör `func init` kommandot enligt följande för att skapa ett Functions-projekt i en mapp med namnet *LocalFunctionProj* med den angivna körnings miljön:  

    ```console
    func init LocalFunctionProj --powershell
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
    
    `func new` skapar en undermapp som matchar funktions namnet som innehåller en kod fil som är lämplig för projektets valda språk och en konfigurations fil med namnet *function.jspå*.

### <a name="optional-examine-the-file-contents"></a>Valfritt Granska fil innehållet

Om du vill kan du hoppa över att [köra funktionen lokalt](#run-the-function-locally) och undersöka fil innehållet senare.

#### <a name="runps1"></a>run.ps1

*run.ps1* definierar ett funktions skript som utlöses enligt konfigurationen i *function.jspå*.

:::code language="powershell" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/run.ps1":::

För en HTTP-utlösare tar funktionen emot begär ande data som skickas till den `$Request` param som definierats i *function.js*. Returvärdet, som definieras som `Response` i *function.jspå*, skickas till `Push-OutputBinding` cmdleten som svar. 

#### <a name="functionjson"></a>function.json

*function.jspå* är en konfigurations fil som definierar indata och utdata `bindings` för funktionen, inklusive utlösnings typen. 

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json":::

Varje bindning kräver en riktning, en typ och ett unikt namn. HTTP-utlösaren har en inkommande bindning av typen [`httpTrigger`](functions-bindings-http-webhook-trigger.md) och utgående bindningen av typen [`http`](functions-bindings-http-webhook-output.md) .

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

[!INCLUDE [functions-create-azure-resources-cli](../../includes/functions-create-azure-resources-cli.md)]

4. Skapa Function-appen i Azure:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime powershell --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    Kommandot [AZ functionapp Create](/cli/azure/functionapp#az_functionapp_create) skapar Function-appen i Azure. 
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime PowerShell -FunctionsVersion 3 -Location 'West Europe'
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

[Ansluta till en Azure Storage-kö]: functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-powershell
