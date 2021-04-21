---
title: Skapa en Python-funktion från kommandoraden för Azure Functions
description: Lär dig hur du skapar en Python-funktion från kommandoraden och publicerar det lokala projektet till serverlös värd i Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-python
- devx-track-azurecli
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 626cff867a336880689373c289087e2332a816ee
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787457"
---
# <a name="quickstart-create-a-python-function-in-azure-from-the-command-line"></a>Snabbstart: Skapa en Python-funktion i Azure från kommandoraden

> [!div class="op_single_selector" title1="Välj funktionsspråk: "]
> - [Python](create-first-function-cli-python.md)
> - [C#](create-first-function-cli-csharp.md)
> - [Java](create-first-function-cli-java.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [PowerShell](create-first-function-cli-powershell.md)
> - [TypeScript](create-first-function-cli-typescript.md)

I den här artikeln använder du kommandoradsverktyg för att skapa en Python-funktion som svarar på HTTP-begäranden. När du har testat koden lokalt distribuerar du den till <abbr title="En körningsmiljö där all information om servern är transparent för programutvecklare, vilket förenklar distributionen och hanteringen av kod.">serverlöst</abbr> miljö för <abbr title="En Azure-tjänst som tillhandahåller en serverlös datormiljö med låg kostnad för program.">Azure Functions</abbr>.

När du slutför den här snabbstarten medför det en liten kostnad på några cent eller mindre för ditt Azure-konto.

Det finns även en [Visual Studio kodbaserad version av](create-first-function-vs-code-python.md) den här artikeln.

## <a name="1-configure-your-environment"></a>1. Konfigurera din miljö

Innan du börjar måste du ha följande:

+ Ett Azure-konto <abbr title="Profilen som underhåller faktureringsinformation för Azure-användning.">konto</abbr> med en aktiv <abbr title="Den grundläggande organisationsstrukturen där du hanterar resurser i Azure, vanligtvis associerad med en person eller avdelning inom en organisation.">prenumeration</abbr>. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ Den [Azure Functions Core Tools](functions-run-local.md#v2) version 3.x. 
  
+ Antingen <abbr title="En uppsättning plattformsoberoende kommandoradsverktyg för att arbeta med Azure-resurser från din lokala utvecklingsdator, som ett alternativ till att använda Azure Portal.">Azure CLI</abbr> eller <abbr title="En PowerShell-modul som innehåller kommandon för att arbeta med Azure-resurser från din lokala utvecklingsdator som ett alternativ till att använda Azure Portal.">Azure PowerShell</abbr> för att skapa Azure-resurser:

    + [Azure CLI](/cli/azure/install-azure-cli) version 2.4 eller senare.

    + [Azure PowerShell](/powershell/azure/install-az-ps) version 5.0 eller senare.

+ [Python 3.8 (64-bitars)](https://www.python.org/downloads/release/python-382/), [Python 3.7 (64-bitars)](https://www.python.org/downloads/release/python-375/), [Python 3.6 (64-bitars)](https://www.python.org/downloads/release/python-368/), som alla stöds av version 3.x av Azure Functions.

### <a name="11-prerequisite-check"></a>1.1 Kravkontroll

Kontrollera kraven, som beror på om du använder Azure CLI eller Azure PowerShell för att skapa Azure-resurser:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ I en terminal eller ett kommandofönster kör du `func --version` för att kontrollera att <abbr title="En uppsättning kommandoradsverktyg för att arbeta med Azure Functions på din lokala dator.">Azure Functions Core Tools</abbr> är version 3.x.

+ Kör `az --version` för att kontrollera att Azure CLI-versionen är 2.4 eller senare.

+ Kör `az login` för att logga in på Azure och verifiera en aktiv prenumeration.

+ Kör (Linux/macOS) eller (Windows) för att kontrollera `python --version` `py --version` python-versionsrapporterna 3.8.x, 3.7.x eller 3.6.x.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ I en terminal eller ett kommandofönster kör du `func --version` för att kontrollera att <abbr title="En uppsättning kommandoradsverktyg för att arbeta med Azure Functions på din lokala dator.">Azure Functions Core Tools</abbr> är version 3.x.

+ Kör `(Get-Module -ListAvailable Az).Version` och verifiera version 5.0 eller senare. 

+ Kör `Connect-AzAccount` för att logga in på Azure och verifiera en aktiv prenumeration.

+ Kör (Linux/macOS) eller (Windows) för att kontrollera `python --version` `py --version` python-versionsrapporterna 3.8.x, 3.7.x eller 3.6.x.

---

<br/>

---

## <a name="2-create-and-activate-a-virtual-environment"></a>2. <a name="create-venv"></a> Skapa och aktivera en virtuell miljö

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

<br/>

---

## <a name="3-create-a-local-function-project"></a>3. Skapa ett lokalt funktionsprojekt

I det här avsnittet skapar du en lokal <abbr title="En logisk container för en eller flera enskilda funktioner som kan distribueras och hanteras tillsammans.">Azure Functions projekt</abbr> i Python. Varje funktion i projektet svarar på en specifik <abbr title="Den typ av händelse som anropar funktionens kod, till exempel en HTTP-begäran, ett kömeddelande eller en viss tid.">Utlösa</abbr>.

1. Kör kommandot `func init` för att skapa ett funktionsprojekt i en mapp med namnet *LocalFunctionProj* med den angivna körningen:  

    ```console
    func init LocalFunctionProj --python
    ```

1. Navigera till projektmappen:

    ```console
    cd LocalFunctionProj
    ```
    
    <br/>
    <details>
    <summary><strong>Vad skapas i mappen LocalFunctionProj?</strong></summary>
    
    Den här mappen innehåller olika filer för projektet, inklusive konfigurationsfiler [ medlocal.settings.jspå](functions-run-local.md#local-settings-file) ochhost.js[ på](functions-host-json.md). Eftersom *local.settings.jspå* kan innehålla hemligheter som laddats ned från Azure undantas filen från källkontrollen som standard i *.gitignore-filen.*
    </details>

1. Lägg till en funktion i projektet med hjälp av följande kommando:

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```   
    Argumentet `--name` är det unika namnet på din funktion (HttpExample).

    Argumentet `--template` anger funktionens utlösare (HTTP).
    
    `func new`skapar en undermapp som matchar funktionsnamnet som innehåller en *\_ \_ \_ \_ init.py-fil* med funktionens kod och en konfigurationsfil *med namnetfunction.jspå*.

    <br/>    
    <details>
    <summary><strong>Kod för __init__.py</strong></summary>
    
    *\_ \_ init \_ \_ .py* innehåller en `main()` Python-funktion som utlöses enligt konfigurationen i *function.jspå*.
    
    :::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::
    
    För en HTTP-utlösare tar funktionen emot begärandedata i `req` variabeln enligt definitionen *ifunction.jspå*. `req`är en instans av [klassen azure.functions.HttpRequest.](/python/api/azure-functions/azure.functions.httprequest) Returobjektet, som definieras `$return` som ifunction.js *på*, är en instans av [klassen azure.functions.HttpResponse.](/python/api/azure-functions/azure.functions.httpresponse) Mer information finns i Azure Functions [HTTP-utlösare och bindningar](./functions-bindings-http-webhook.md?tabs=python).
    </details>

    <br/>
    <details>
    <summary><strong>Kod för function.jspå</strong></summary>

    *function.jspå* är en konfigurationsfil som definierar <abbr title="Deklarativa anslutningar mellan en funktion och andra resurser. En indatabindning tillhandahåller data till funktionen. en utdatabindning ger data från funktionen till andra resurser.">indata- och utdatabindningar</abbr> för funktionen, inklusive utlösartypen.
    
    Du kan ändra för `scriptFile` att anropa en annan Python-fil om du vill.
    
    :::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::
    
    Varje bindning kräver en riktning, en typ och ett unikt namn. HTTP-utlösaren har en indatabindning av typen [`httpTrigger`](functions-bindings-http-webhook-trigger.md) och utdatabindning av typen [`http`](functions-bindings-http-webhook-output.md) .    
    </details>

<br/>

---

## <a name="4-run-the-function-locally"></a>4. Kör funktionen lokalt

1. Kör funktionen genom att starta den lokala Azure Functions runtime-värden från *mappen LocalFunctionProj:*

    ```
    func start
    ```

    Mot slutet av utdata bör följande rader visas: 
    
    <pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    <br/>
    <details>
    <summary><strong>Jag ser inte HttpExample i utdata</strong></summary>

    Om HttpExample inte visas startade du förmodligen värden utanför rotmappen för projektet. I så fall använder du <kbd>Ctrl +C</kbd> för att stoppa värden, navigerar till projektets rotmapp och kör föregående kommando igen.
    </details>

1. Kopiera URL:en för **din HttpExample-funktion** från dessa utdata till en webbläsare och lägg till frågesträngen **?name=<YOUR_NAME>**, vilket gör den fullständiga URL:en som **http://localhost:7071/api/HttpExample?name=Functions** . Webbläsaren bör visa ett meddelande som **Hello Functions:**

    ![Resultatet av funktionen körs lokalt i webbläsaren](../../includes/media/functions-run-function-test-local-cli/function-test-local-browser.png)

1. Terminalen där du startade projektet visar även loggutdata när du gör begäranden.

1. När du är klar använder du <kbd>Ctrl+C och</kbd> väljer <kbd>y för</kbd> att stoppa functions-värden.

<br/>

---

## <a name="5-create-supporting-azure-resources-for-your-function"></a>5. Skapa azure-stödresurser för din funktion

Innan du kan distribuera funktionskoden till Azure måste du skapa en <abbr title="En logisk container för relaterade Azure-resurser som du kan hantera som en enhet.">Resursgrupp</abbr>A <abbr title="Ett konto som innehåller alla dina Azure Storage-dataobjekt. Lagringskontot tillhandahåller ett unikt namnområde för dina lagringsdata.">storage account</abbr>och en <abbr title="Den molnresurs som är värd för serverlösa funktioner i Azure, som tillhandahåller den underliggande beräkningsmiljö där funktioner körs.">funktionsapp</abbr> med hjälp av följande kommandon:

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
 
    Kommandot [az group create](/cli/azure/group#az_group_create) skapar en resursgrupp. Du skapar vanligtvis din resursgrupp och dina resurser i en <abbr title="En geografisk referens till ett specifikt Azure-datacenter där resurser allokeras.">region</abbr> nära dig, med hjälp av en tillgänglig region som returneras från `az account list-locations` kommandot .

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```

    Kommandot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) skapar en resursgrupp. Vanligtvis skapar du din resursgrupp och dina resurser i en region nära dig, med hjälp av en tillgänglig region som returneras från cmdleten [Get-AzLocation.](/powershell/module/az.resources/get-azlocation)

    ---

    Du kan inte vara värd för Linux- och Windows-appar i samma resursgrupp. Om du har en befintlig resursgrupp med `AzureFunctionsQuickstart-rg` namnet med en Windows-funktionsapp eller webbapp måste du använda en annan resursgrupp.

1. Skapa ett allmänt Azure Storage i din resursgrupp och region:

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

    Ersätt `<STORAGE_NAME>` med ett namn som är lämpligt för dig och <abbr title="Namnet måste vara unikt för alla lagringskonton som används av alla Azure-kunder globalt. Du kan till exempel använda en kombination av ditt personliga namn eller företagsnamn, programnamn och en numerisk identifierare, som i contosüzappstorage20.">unikt i Azure Storage</abbr>. Namn får endast innehålla tre till 24 tecken och gemener. `Standard_LRS` anger ett konto för generell användning som stöds [av Functions](storage-considerations.md#storage-account-requirements).
    
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
    
    Ersätt `<STORAGE_NAME>` med namnet på det konto som du använde i föregående steg.

    Ersätt `<APP_NAME>` med en <abbr title="Ett namn som måste vara unikt för alla Azure-kunder globalt. Du kan till exempel använda en kombination av ditt personliga namn eller organisationsnamn, programnamn och en numerisk identifierare, som i contoso-bizapp-func-20.">globalt unikt namn som är lämpligt för dig</abbr>. `<APP_NAME>` är även DNS-standarddomänen för funktionsappen. 
    
    <br/>
    <details>
    <summary><strong>Vad är kostnaden för de resurser som etableras i Azure?</strong></summary>

    Det här kommandot skapar en funktionsapp som körs i den angivna språkkörningen under [Azure Functions Consumption Plan](functions-scale.md#overview-of-plans), vilket är kostnadsfritt för den mängd användning som du ådrar dig här. Kommandot innehåller också en associerad Azure Application Insights-instans i samma resursgrupp, med vilken du kan övervaka funktionsappen och visa loggar. Mer information finns i [Övervaka Azure Functions](functions-monitoring.md). Instansen medför inga kostnader förrän du aktiverar den.
    </details>

<br/>

---

## <a name="6-deploy-the-function-project-to-azure"></a>6. Distribuera funktionsprojektet till Azure

När du har skapat funktionsappen i Azure är  du nu redo att distribuera ditt lokala funktionsprojekt med hjälp av [kommandot func azure functionapp publish.](functions-run-local.md#project-file-deployment)  

I följande exempel ersätter `<APP_NAME>` du med namnet på din app.

```console
func azure functionapp publish <APP_NAME>
```

Kommandot `publish` visar resultat som liknar följande utdata (trunkerade för enkelhetens skull):

<pre class="is-monospace is-size-small has-padding-medium has-background-tertiary has-text-tertiary-invert">
...

Getting site publishing info...
Creating archive for current directory...
Performing remote build for functions project.

...

Deployment successful.
Remote build succeeded!
Syncing triggers...
Functions in msdocs-azurefunctions-qs:
    HttpExample - [httpTrigger]
        Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample
</pre>

<br/>

---

## <a name="7-invoke-the-function-on-azure"></a>7. Anropa funktionen i Azure

Eftersom funktionen använder en HTTP-utlösare anropar du den genom att göra en HTTP-begäran till dess URL i webbläsaren eller med ett verktyg som <abbr title="Ett kommandoradsverktyg för att generera HTTP-begäranden till en URL. Se https://curl.se/">curl</abbr>. 

# <a name="browser"></a>[Webbläsare](#tab/browser)

Kopiera den fullständiga **Invoke URL som** visas i kommandots utdata till ett adressfält i webbläsaren och lägga till `publish` **frågeparametern&name=Functions**. Webbläsaren bör visa liknande utdata som när du körde funktionen lokalt.

![Utdata från funktionen körs på Azure i en webbläsare](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

Kör [`curl`](https://curl.haxx.se/) med Invoke URL **(Anropa URL)** och&**parametern name=Functions**. Kommandots utdata ska vara texten "Hello Functions".

![Utdata från funktionen körs på Azure med curl](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---

### <a name="71-view-real-time-streaming-logs"></a>7.1 Visa strömningsloggar i realtid

Kör följande kommando för att visa strömningsloggar [i nära](functions-run-local.md#enable-streaming-logs) realtid Application Insights i Azure Portal:

```console
func azure functionapp logstream <APP_NAME> --browser
```

Ersätt `<APP_NAME>` med namnet på din funktionsapp.

Anropa fjärrfunktionen igen i ett separat terminalfönster eller i webbläsaren. En utförlig logg över funktionskörningen i Azure visas i terminalen. 

<br/>

---

## <a name="8-clean-up-resources"></a>8. Rensa resurser

Om du fortsätter till [nästa steg och lägger](#next-steps) till en <abbr title="Ett sätt att associera en funktion med en lagringskö så att den kan skapa meddelanden i kön. ">Azure Storage köutdatabindning</abbr>håller du alla dina resurser på plats eftersom du bygger vidare på det du redan har gjort.

Annars använder du följande kommando för att ta bort resursgruppen och alla dess inneslutna resurser för att undvika ytterligare kostnader.

 # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name AzureFunctionsQuickstart-rg
```

<br/>

---

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Ansluta till en Azure Storage kö](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-python)

[Har du problem? Berätta för oss.](https://aka.ms/python-functions-qs-survey)
