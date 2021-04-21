---
title: Skapa en Python-funktion från kommandoraden – Azure Functions
description: Lär dig hur du skapar en Python-funktion från kommandoraden och sedan publicerar det lokala projektet till serverlös värd i Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-python
- devx-track-azurecli
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-cli-python-uiex
ms.openlocfilehash: f5c51630d111bd68e311a93100abb8266e2a8e27
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787439"
---
# <a name="quickstart-create-a-python-function-in-azure-from-the-command-line"></a>Snabbstart: Skapa en Python-funktion i Azure från kommandoraden

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

I den här artikeln använder du kommandoradsverktyg för att skapa en Python-funktion som svarar på HTTP-begäranden. När du har testat koden lokalt distribuerar du den till den serverlösa Azure Functions.

Den här snabbstarten medför en liten kostnad på några cent eller mindre för ditt Azure-konto.

Det finns även en [Visual Studio kodbaserad version av](create-first-function-vs-code-python.md) den här artikeln.

## <a name="configure-your-local-environment"></a>Konfigurera din lokala miljö

Innan du börjar måste du ha följande:

+ Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ Den [Azure Functions Core Tools](functions-run-local.md#v2) version 3.x.
  
+ Ett av följande verktyg för att skapa Azure-resurser:

    + [Azure CLI](/cli/azure/install-azure-cli) version 2.4 eller senare.

    + [Azure PowerShell](/powershell/azure/install-az-ps) version 5.0 eller senare.

+ [Python-versioner som stöds av Azure Functions](supported-languages.md#languages-by-runtime-version)

### <a name="prerequisite-check"></a>Kravkontroll

Kontrollera kraven, som beror på om du använder Azure CLI eller Azure PowerShell för att skapa Azure-resurser:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ I en terminal eller ett kommandofönster kör `func --version` du för att kontrollera Azure Functions Core Tools version 3.x.

+ Kör `az --version` för att kontrollera att Azure CLI-versionen är 2.4 eller senare.

+ Kör `az login` för att logga in på Azure och verifiera en aktiv prenumeration.

+ Kör (Linux/macOS) eller (Windows) för att kontrollera `python --version` `py --version` python-versionsrapporterna 3.8.x, 3.7.x eller 3.6.x.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ I en terminal eller ett kommandofönster kör `func --version` du för att kontrollera Azure Functions Core Tools version 3.x.

+ Kör `(Get-Module -ListAvailable Az).Version` och verifiera version 5.0 eller senare. 

+ Kör `Connect-AzAccount` för att logga in på Azure och verifiera en aktiv prenumeration.

+ Kör (Linux/macOS) eller (Windows) för att kontrollera `python --version` `py --version` python-versionsrapporterna 3.8.x, 3.7.x eller 3.6.x.

---

## <a name="create-and-activate-a-virtual-environment"></a><a name="create-venv"></a>Skapa och aktivera en virtuell miljö

Kör följande kommandon i en lämplig mapp för att skapa och aktivera en virtuell miljö med namnet `.venv` . Se till att använda Python 3.8, 3.7 eller 3.6, som stöds av Azure Functions.

# <a name="bash"></a>[Bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Om Python inte har installerat venv-paketet på Linux-distributionen kör du följande kommando:

```bash
sudo apt-get install python3-venv
```

# <a name="powershell"></a>[PowerShell](#tab/powershell)

```powershell
py -m venv .venv
```

```powershell
.venv\scripts\activate
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
py -m venv .venv
```

```cmd
.venv\scripts\activate
```

---

Du kör alla efterföljande kommandon i den här aktiverade virtuella miljön. 

## <a name="create-a-local-function-project"></a>Skapa ett lokalt funktionsprojekt

I Azure Functions är ett funktionsprojekt en container för en eller flera enskilda funktioner som var och en svarar på en specifik utlösare. Alla funktioner i ett projekt delar samma lokala konfigurationer och värdkonfigurationer. I det här avsnittet skapar du ett funktionsprojekt som innehåller en enda funktion.

1. Kör kommandot på följande sätt för att skapa ett funktionsprojekt i en mapp med namnet `func init` *LocalFunctionProj* med den angivna körningen:  

    ```console
    func init LocalFunctionProj --python
    ```

1. Navigera till projektmappen:

    ```console
    cd LocalFunctionProj
    ```
    
    Den här mappen innehåller olika filer för projektet, inklusive konfigurationsfiler [ medlocal.settings.jspå](functions-run-local.md#local-settings-file) ochhost.js[ på](functions-host-json.md). Eftersom *local.settings.jspå* kan innehålla hemligheter som laddats ned från Azure undantas filen från källkontrollen som standard i *.gitignore-filen.*

1. Lägg till en funktion i projektet med hjälp av följande kommando, där argumentet är det unika namnet på din funktion `--name` (HttpExample) och `--template` argumentet anger funktionens utlösare (HTTP).

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```   
    
    `func new` skapar en undermapp som matchar funktionsnamnet som innehåller en kodfil som är lämplig för projektets valda språk och en konfigurationsfil med *namnetfunction.jspå*.

### <a name="optional-examine-the-file-contents"></a>(Valfritt) Granska filinnehållet

Om du vill kan du gå [vidare till Kör funktionen lokalt](#run-the-function-locally) och undersöka filinnehållet senare.

#### <a name="__init__py"></a>\_\_init \_ \_ .py

*\_ \_ init \_ \_ .py* innehåller en `main()` Python-funktion som utlöses enligt konfigurationen i *function.jspå*.

:::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

För en HTTP-utlösare tar funktionen emot begärandedata i `req` variabeln enligt definitionen *ifunction.jspå*. `req`är en instans av [klassen azure.functions.HttpRequest.](/python/api/azure-functions/azure.functions.httprequest) Returobjektet, som definieras `$return` som ifunction.js *på*, är en instans av [klassen azure.functions.HttpResponse.](/python/api/azure-functions/azure.functions.httpresponse) Mer information finns i Azure Functions [HTTP-utlösare och bindningar](./functions-bindings-http-webhook.md?tabs=python).

#### <a name="functionjson"></a>function.json

*function.jspå är* en konfigurationsfil som definierar indata och `bindings` utdata för funktionen, inklusive utlösartypen.

Du kan ändra för `scriptFile` att anropa en annan Python-fil om du vill.

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

Varje bindning kräver en riktning, en typ och ett unikt namn. HTTP-utlösaren har en indatabindning av typen [`httpTrigger`](functions-bindings-http-webhook-trigger.md) och utdatabindning av typen [`http`](functions-bindings-http-webhook-output.md) .

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

## <a name="create-supporting-azure-resources-for-your-function"></a>Skapa azure-stödresurser för din funktion

Innan du kan distribuera funktionskoden till Azure måste du skapa tre resurser:

- En resursgrupp, som är en logisk container för relaterade resurser.
- Ett lagringskonto som upprätthåller tillstånd och annan information om dina projekt.
- En funktionsapp som tillhandahåller miljön för att köra funktionskoden. En funktionsapp mappar till ditt lokala funktionsprojekt och gör att du kan gruppera funktioner som en logisk enhet för enklare hantering, distribution och delning av resurser.

Använd följande kommandon för att skapa dessa objekt. Både Azure CLI och PowerShell stöds.

1. Om du inte redan har gjort det loggar du in på Azure:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```

    Kommandot [az login](/cli/azure/reference-index#az_login) loggar in dig på ditt Azure-konto.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    Cmdleten [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) loggar in dig på ditt Azure-konto.

    ---

1. Skapa en resursgrupp med `AzureFunctionsQuickstart-rg` namnet i `westeurope` regionen . 

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    Kommandot [az group create](/cli/azure/group#az_group_create) skapar en resursgrupp. Vanligtvis skapar du din resursgrupp och dina resurser i en region nära dig, med hjälp av en tillgänglig region som returneras från `az account list-locations` kommandot .

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```

    Kommandot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) skapar en resursgrupp. Vanligtvis skapar du din resursgrupp och dina resurser i en region nära dig, med hjälp av en tillgänglig region som returneras från cmdleten [Get-AzLocation.](/powershell/module/az.resources/get-azlocation)

    ---

    > [!NOTE]
    > Du kan inte vara värd för Linux- och Windows-appar i samma resursgrupp. Om du har en befintlig resursgrupp med `AzureFunctionsQuickstart-rg` namnet med en Windows-funktionsapp eller webbapp måste du använda en annan resursgrupp.

1. Skapa ett allmänt lagringskonto i resursgruppen och regionen:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    Kommandot [az storage account create](/cli/azure/storage/account#az_storage_account_create) skapar lagringskontot. 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```

    Cmdleten [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) skapar lagringskontot.

    ---

    I föregående exempel ersätter du `<STORAGE_NAME>` med ett namn som är lämpligt för dig och unikt i Azure Storage. Namn får endast innehålla tre till 24 tecken och gemener. `Standard_LRS` anger ett konto för generell användning som stöds [av Functions](storage-considerations.md#storage-account-requirements).
    
    Lagringskontot ådrar sig bara några cent (USD) för den här snabbstarten.

1. Skapa funktionsappen i Azure:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime python --runtime-version 3.8 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME> --os-type linux
    ```
    
    Kommandot [az functionapp create](/cli/azure/functionapp#az_functionapp_create) skapar funktionsappen i Azure. Om du använder Python 3.7 eller 3.6 ändrar `--runtime-version` du till respektive eller `3.7` `3.6` .
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -FunctionsVersion 3 -RuntimeVersion 3.8 -Runtime python -Location 'West Europe'
    ```
    
    Cmdleten [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) skapar funktionsappen i Azure. Om du använder Python 3.7 eller 3.6 ändrar du `-RuntimeVersion` `3.7` till respektive eller `3.6` .

    ---
    
    I föregående exempel ersätter du med namnet på det konto som du använde i föregående steg och ersätter med ett `<STORAGE_NAME>` globalt unikt namn som passar `<APP_NAME>` dig.  `<APP_NAME>` är även DNS-standarddomänen för funktionsappen. 
    
    Det här kommandot skapar en funktionsapp som körs i den angivna språkkörningen under [Azure Functions Consumption Plan](consumption-plan.md), vilket är kostnadsfritt för den mängd användning som du ådrar dig här. Kommandot innehåller också en associerad Azure Application Insights-instans i samma resursgrupp, med vilken du kan övervaka funktionsappen och visa loggar. Mer information finns i [Övervaka Azure Functions](functions-monitoring.md). Instansen medför inga kostnader förrän du aktiverar den.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

Kör följande kommando för att visa strömmande loggar [i nära](functions-run-local.md#enable-streaming-logs) realtid Application Insights i Azure Portal:

```console
func azure functionapp logstream <APP_NAME> --browser
```

I ett separat terminalfönster eller i webbläsaren anropar du fjärrfunktionen igen. En utförlig logg över funktionskörningen i Azure visas i terminalen. 

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Ansluta till en Azure Storage kö](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-python)

[Har du problem? Berätta för oss.](https://aka.ms/python-functions-qs-survey)