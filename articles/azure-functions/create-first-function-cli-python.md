---
title: Skapa en python-funktion från kommando raden – Azure Functions
description: Lär dig hur du skapar en python-funktion från kommando raden och sedan publicerar det lokala projektet till Server lös värd i Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-python
- devx-track-azurecli
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-cli-python-uiex
ms.openlocfilehash: 1b6805434f8c697c1ea13925ee681fdc7b5f1a29
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101704760"
---
# <a name="quickstart-create-a-python-function-in-azure-from-the-command-line"></a>Snabb start: skapa en python-funktion i Azure från kommando raden

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

I den här artikeln använder du kommando rads verktyg för att skapa en python-funktion som svarar på HTTP-begäranden. När du har testat koden lokalt distribuerar du den till den serverbaserade miljön för Azure Functions.

Att slutföra den här snabb starten innebär en låg kostnad av några USD cent eller mindre i ditt Azure-konto.

Det finns också en [Visual Studio Code-baserad version](create-first-function-vs-code-python.md) av den här artikeln.

## <a name="configure-your-local-environment"></a>Konfigurera din lokala miljö

Innan du börjar måste du ha följande:

+ Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core tools](functions-run-local.md#v2) version 3. x.
  
+ Ett av följande verktyg för att skapa Azure-resurser:

    + [Azure CLI](/cli/azure/install-azure-cli) version 2,4 eller senare.

    + [Azure PowerShell](/powershell/azure/install-az-ps) version 5,0 eller senare.

+ [Python-versioner som stöds av Azure Functions](supported-languages.md#languages-by-runtime-version)

### <a name="prerequisite-check"></a>Krav kontroll

Verifiera dina krav, beroende på om du använder Azure CLI eller Azure PowerShell för att skapa Azure-resurser:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ I ett terminalfönster eller kommando fönster, kör `func --version` för att kontrol lera att Azure Functions Core Tools är version 3. x.

+ Kör `az --version` för att kontrol lera att Azure CLI-versionen är 2,4 eller senare.

+ Kör `az login` för att logga in på Azure och verifiera en aktiv prenumeration.

+ Kör `python --version` (Linux/MacOS) eller `py --version` (Windows) för att kontrol lera dina python versions rapporter 3.8. x, 3.7. x eller 3.6. x.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ I ett terminalfönster eller kommando fönster, kör `func --version` för att kontrol lera att Azure Functions Core Tools är version 3. x.

+ Kör `(Get-Module -ListAvailable Az).Version` och verifiera version 5,0 eller senare. 

+ Kör `Connect-AzAccount` för att logga in på Azure och verifiera en aktiv prenumeration.

+ Kör `python --version` (Linux/MacOS) eller `py --version` (Windows) för att kontrol lera dina python versions rapporter 3.8. x, 3.7. x eller 3.6. x.

---

## <a name="create-and-activate-a-virtual-environment"></a><a name="create-venv"></a>Skapa och aktivera en virtuell miljö

I en lämplig mapp kör du följande kommandon för att skapa och aktivera en virtuell miljö med namnet `.venv` . Se till att använda python 3,8, 3,7 eller 3,6, som stöds av Azure Functions.

# <a name="bash"></a>[bash](#tab/bash)

```bash
python -m venv .venv
```

```bash
source .venv/bin/activate
```

Om python inte installerade venv-paketet på din Linux-distribution kör du följande kommando:

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

## <a name="create-a-local-function-project"></a>Skapa ett lokalt funktions projekt

I Azure Functions är ett funktions projekt en behållare för en eller flera enskilda funktioner som varje svarar på en viss utlösare. Alla funktioner i ett projekt delar samma lokala och värdbaserade konfigurationer. I det här avsnittet skapar du ett funktions projekt som innehåller en enda funktion.

1. Kör `func init` kommandot enligt följande för att skapa ett Functions-projekt i en mapp med namnet *LocalFunctionProj* med den angivna körnings miljön:  

    ```console
    func init LocalFunctionProj --python
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

#### <a name="__init__py"></a>\_\_init \_ \_ . py

*\_ \_ init \_ \_ . py* innehåller en `main()` python-funktion som utlöses enligt konfigurationen i *function.jspå*.

:::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

För en HTTP-utlösare tar funktionen emot begär ande data i variabeln `req` enligt definitionen i *function.jspå*. `req` är en instans av [klassen Azure. functions. HttpRequest](/python/api/azure-functions/azure.functions.httprequest). Returvärdet, som definieras som `$return` i *function.jspå*, är en instans av [klassen Azure. functions. HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Läs mer i [Azure Functions HTTP-utlösare och bindningar](./functions-bindings-http-webhook.md?tabs=python).

#### <a name="functionjson"></a>function.json

*function.jspå* är en konfigurations fil som definierar indata och utdata `bindings` för funktionen, inklusive utlösnings typen.

Du kan ändra `scriptFile` för att anropa en annan python-fil om du vill.

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::

Varje bindning kräver en riktning, en typ och ett unikt namn. HTTP-utlösaren har en inkommande bindning av typen [`httpTrigger`](functions-bindings-http-webhook-trigger.md) och utgående bindningen av typen [`http`](functions-bindings-http-webhook-output.md) .

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

## <a name="create-supporting-azure-resources-for-your-function"></a>Skapa stöd för Azure-resurser för din funktion

Innan du kan distribuera funktions koden till Azure måste du skapa tre resurser:

- En resurs grupp, som är en logisk behållare för relaterade resurser.
- Ett lagrings konto som upprätthåller tillstånd och annan information om dina projekt.
- En Function-app som tillhandahåller miljön för att köra funktions koden. En Function-app mappar till ditt lokala Function-projekt och gör att du kan gruppera funktioner som en logisk enhet för enklare hantering, distribution och delning av resurser.

Använd följande kommandon för att skapa dessa objekt. Både Azure CLI och PowerShell stöds.

1. Om du inte redan har gjort det loggar du in på Azure:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```

    Kommandot [AZ login](/cli/azure/reference-index#az-login) loggar in dig på ditt Azure-konto.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    Cmdleten [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) loggar in dig på ditt Azure-konto.

    ---

1. Skapa en resurs grupp med namnet `AzureFunctionsQuickstart-rg` i `westeurope` regionen. 

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    
    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    Kommandot [AZ Group Create](/cli/azure/group#az-group-create) skapar en resurs grupp. Du skapar vanligt vis resurs gruppen och resurserna i en region nära dig, med hjälp av en tillgänglig region som returneras från `az account list-locations` kommandot.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```

    Kommandot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) skapar en resurs grupp. Du skapar vanligt vis resurs gruppen och resurserna i en region nära dig, med hjälp av en tillgänglig region som returneras från cmdleten [Get-AzLocation](/powershell/module/az.resources/get-azlocation) .

    ---

    > [!NOTE]
    > Du kan inte vara värd för Linux-och Windows-appar i samma resurs grupp. Om du har en befintlig resurs grupp med namnet `AzureFunctionsQuickstart-rg` med en Windows Function-app eller webbapp måste du använda en annan resurs grupp.

1. Skapa ett allmänt lagrings konto i din resurs grupp och region:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    Kommandot [AZ Storage Account Create](/cli/azure/storage/account#az-storage-account-create) skapar lagrings kontot. 

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```

    Cmdlet: en [New-AzStorageAccount](/powershell/module/az.storage/new-azstorageaccount) skapar lagrings kontot.

    ---

    I föregående exempel ersätter `<STORAGE_NAME>` du med ett namn som passar dig och är unikt i Azure Storage. Namn måste innehålla mellan 3 och 24 tecken och endast små bokstäver. `Standard_LRS` anger ett allmänt-syfte-konto, som [stöds av Functions](storage-considerations.md#storage-account-requirements).
    
    Lagrings kontot förväntar sig bara några cent (USD) för den här snabb starten.

1. Skapa Function-appen i Azure:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime python --runtime-version 3.8 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME> --os-type linux
    ```
    
    Kommandot [AZ functionapp Create](/cli/azure/functionapp#az_functionapp_create) skapar Function-appen i Azure. Om du använder python 3,7 eller 3,6 ändrar `--runtime-version` du till `3.7` `3.6` respektive.
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -FunctionsVersion 3 -RuntimeVersion 3.8 -Runtime python -Location 'West Europe'
    ```
    
    Cmdlet: en [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) skapar Function-appen i Azure. Om du använder python 3,7 eller 3,6 ändrar `-RuntimeVersion` du till `3.7` `3.6` respektive.

    ---
    
    I föregående exempel ersätter `<STORAGE_NAME>` du med namnet på det konto som du använde i föregående steg och ersätter `<APP_NAME>` med ett globalt unikt namn som passar dig.  `<APP_NAME>` är även DNS-standarddomänen för funktionsappen. 
    
    Det här kommandot skapar en Function-app som körs på den angivna språk körningen under [Azure Functions förbruknings plan](consumption-plan.md), vilket är kostnads fritt för den användnings mängd som du ådrar dig här. Kommandot etablerar även en associerad Azure Application insikts-instans i samma resurs grupp, som du kan använda för att övervaka din Function-app och Visa loggar. Mer information finns i [övervaka Azure Functions](functions-monitoring.md). Instansen debiteras inga kostnader förrän du aktiverar den.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

Kör följande kommando för att Visa nästan real tids [strömnings loggar](functions-run-local.md#enable-streaming-logs) i Application Insights i Azure Portal:

```console
func azure functionapp logstream <APP_NAME> --browser
```

I ett separat terminalfönster eller i webbläsaren, anropar du fjärrfunktionen igen. En utförlig logg över funktions körningen i Azure visas i terminalen. 

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Ansluta till en Azure Storage-kö](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-python)

[Har du problem? Berätta för oss.](https://aka.ms/python-functions-qs-survey)