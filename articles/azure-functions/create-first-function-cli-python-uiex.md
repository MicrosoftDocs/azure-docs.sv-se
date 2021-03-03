---
title: Skapa en python-funktion från kommando raden för Azure Functions
description: Lär dig hur du skapar en python-funktion från kommando raden och publicerar det lokala projektet till Server lös värd i Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-python
- devx-track-azurecli
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 76ec99e5f892438aaa637b15bb7382843d90f334
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101749040"
---
# <a name="quickstart-create-a-python-function-in-azure-from-the-command-line"></a>Snabb start: skapa en python-funktion i Azure från kommando raden

> [!div class="op_single_selector" title1="Välj ditt funktions språk: "]
> - [Python](create-first-function-cli-python.md)
> - [C#](create-first-function-cli-csharp.md)
> - [Java](create-first-function-cli-java.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [PowerShell](create-first-function-cli-powershell.md)
> - [TypeScript](create-first-function-cli-typescript.md)

I den här artikeln använder du kommando rads verktyg för att skapa en python-funktion som svarar på HTTP-begäranden. När du har testat koden lokalt distribuerar du den till <abbr title="En körnings miljö för körning där all information om servern är transparent för programutvecklare, vilket fören klar processen med att distribuera och hantera kod.">serverlöst</abbr> miljö i <abbr title="En Azure-tjänst som tillhandahåller en enklare data behandlings miljö utan kostnad för program.">Azure Functions</abbr>.

Att slutföra den här snabb starten innebär en låg kostnad av några USD cent eller mindre i ditt Azure-konto.

Det finns också en [Visual Studio Code-baserad version](create-first-function-vs-code-python.md) av den här artikeln.

## <a name="1-configure-your-environment"></a>1. Konfigurera din miljö

Innan du börjar måste du ha följande:

+ En Azure <abbr title="Den profil som hanterar fakturerings information för Azure-användning.">konto</abbr> med en aktiv <abbr title="Den grundläggande organisations strukturen i vilken du hanterar resurser i Azure, som vanligt vis är kopplade till en individ eller avdelning inom en organisation.">prenumeration</abbr>. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core tools](functions-run-local.md#v2) version 3. x. 
  
+ Antingen <abbr title="En uppsättning kommando rads verktyg för flera plattformar för att arbeta med Azure-resurser från din lokala utvecklings dator, som ett alternativ till att använda Azure Portal.">Azure CLI</abbr> eller <abbr title="En PowerShell-modul som innehåller kommandon för att arbeta med Azure-resurser från din lokala utvecklings dator, som ett alternativ till att använda Azure Portal.">Azure PowerShell</abbr> för att skapa Azure-resurser:

    + [Azure CLI](/cli/azure/install-azure-cli) version 2,4 eller senare.

    + [Azure PowerShell](/powershell/azure/install-az-ps) version 5,0 eller senare.

+ [Python 3,8 (64-bitars)](https://www.python.org/downloads/release/python-382/), [python 3,7 (64-bitars)](https://www.python.org/downloads/release/python-375/), [python 3,6 (64-bit)](https://www.python.org/downloads/release/python-368/), som stöds av version 3. x av Azure Functions.

### <a name="11-prerequisite-check"></a>1,1 krav kontroll

Verifiera dina krav, beroende på om du använder Azure CLI eller Azure PowerShell för att skapa Azure-resurser:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ I en terminal-eller kommando fönster, kör `func --version` för att kontrol lera att <abbr title="Uppsättningen kommando rads verktyg för att arbeta med Azure Functions på den lokala datorn.">Azure Functions Core Tools</abbr> är version 3. x.

+ Kör `az --version` för att kontrol lera att Azure CLI-versionen är 2,4 eller senare.

+ Kör `az login` för att logga in på Azure och verifiera en aktiv prenumeration.

+ Kör `python --version` (Linux/MacOS) eller `py --version` (Windows) för att kontrol lera dina python versions rapporter 3.8. x, 3.7. x eller 3.6. x.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+ I ett terminalfönster eller kommando fönster, kör `func --version` för att kontrol lera att Azure Functions Core Tools är version 3. x.

+ Kör `(Get-Module -ListAvailable Az).Version` och verifiera version 5,0 eller senare. 

+ Kör `Connect-AzAccount` för att logga in på Azure och verifiera en aktiv prenumeration.

+ Kör `python --version` (Linux/MacOS) eller `py --version` (Windows) för att kontrol lera dina python versions rapporter 3.8. x, 3.7. x eller 3.6. x.

---

<br/>

---

## <a name="2-create-and-activate-a-virtual-environment"></a>2. <a name="create-venv"></a> skapa och aktivera en virtuell miljö

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

<br/>

---

## <a name="3-create-a-local-function-project"></a>3. skapa ett lokalt funktions projekt

I det här avsnittet skapar du en lokal <abbr title="En logisk behållare för en eller flera enskilda funktioner som kan distribueras och hanteras tillsammans.">Azure Functions projekt</abbr> i python. Varje funktion i projektet svarar på ett särskilt <abbr title="Den typ av händelse som anropar funktionens kod, till exempel en HTTP-begäran, ett Queue-meddelande eller en angiven tid.">utlösare</abbr>.

1. Kör `func init` kommandot för att skapa ett Functions-projekt i en mapp med namnet *LocalFunctionProj* med den angivna körnings miljön:  

    ```console
    func init LocalFunctionProj --python
    ```

1. Navigera till projektmappen:

    ```console
    cd LocalFunctionProj
    ```
    
    <br/>
    <details>
    <summary><strong>Vad har skapats i mappen LocalFunctionProj?</strong></summary>
    
    Den här mappen innehåller olika filer för projektet, inklusive konfigurationsfiler som heter [local.settings.jspå](functions-run-local.md#local-settings-file) och [host.js](functions-host-json.md). Eftersom *local.settings.jspå* kan innehålla hemligheter som hämtats från Azure, undantas filen från käll kontroll som standard i *. gitignore* -filen.
    </details>

1. Lägg till en funktion i projektet med hjälp av följande kommando:

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ```   
    `--name`Argumentet är det unika namnet för din funktion (HttpExample).

    `--template`Argumentet anger funktionens utlösare (http).
    
    `func new`skapar en undermapp som matchar funktions namnet som innehåller en *\_ \_ init \_ \_ . py* -fil med funktionens kod och en konfigurations fil med namnet *function.jspå*.

    <br/>    
    <details>
    <summary><strong>Kod för __init__. py</strong></summary>
    
    *\_ \_ init \_ \_ . py* innehåller en `main()` python-funktion som utlöses enligt konfigurationen i *function.jspå*.
    
    :::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::
    
    För en HTTP-utlösare tar funktionen emot begär ande data i variabeln `req` enligt definitionen i *function.jspå*. `req` är en instans av [klassen Azure. functions. HttpRequest](/python/api/azure-functions/azure.functions.httprequest). Returvärdet, som definieras som `$return` i *function.jspå*, är en instans av [klassen Azure. functions. HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Läs mer i [Azure Functions HTTP-utlösare och bindningar](./functions-bindings-http-webhook.md?tabs=python).
    </details>

    <br/>
    <details>
    <summary><strong>Kod för function.jspå</strong></summary>

    *function.jspå* är en konfigurations fil som definierar <abbr title="Deklarativ anslutningar mellan en funktion och andra resurser. En indata-bindning tillhandahåller data till funktionen. en utgående bindning tillhandahåller data från funktionen till andra resurser.">bindningar för indata och utdata</abbr> för funktionen, inklusive utlösnings typen.
    
    Du kan ändra `scriptFile` för att anropa en annan python-fil om du vill.
    
    :::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::
    
    Varje bindning kräver en riktning, en typ och ett unikt namn. HTTP-utlösaren har en inkommande bindning av typen [`httpTrigger`](functions-bindings-http-webhook-trigger.md) och utgående bindningen av typen [`http`](functions-bindings-http-webhook-output.md) .    
    </details>

<br/>

---

## <a name="4-run-the-function-locally"></a>4. kör funktionen lokalt

1. Kör din funktion genom att starta den lokala Azure Functions runtime-värden från mappen *LocalFunctionProj* :

    ```
    func start
    ```

    I slutet av utdata bör följande rader visas: 
    
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

    Om HttpExample inte visas, startade du förmodligen värden från utanför rotmappen för projektet. I så fall kan du använda <kbd>CTRL + C</kbd> för att stoppa värden, navigera till projektets rotmapp och köra föregående kommando igen.
    </details>

1. Kopiera URL: en för din **HttpExample** -funktion från utdata till en webbläsare och Lägg till frågesträngen **? namn =<YOUR_NAME>**, vilket gör den fullständiga URL: en som **http://localhost:7071/api/HttpExample?name=Functions** . Webbläsaren ska visa ett meddelande som **Hello Functions**:

    ![Resultatet av funktionen körs lokalt i webbläsaren](../../includes/media/functions-run-function-test-local-cli/function-test-local-browser.png)

1. Terminalen där du startade projektet visar också logg data när du gör förfrågningar.

1. När du är klar använder du <kbd>CTRL + C</kbd> och väljer <kbd>y</kbd> för att stoppa funktions värden.

<br/>

---

## <a name="5-create-supporting-azure-resources-for-your-function"></a>5. skapa stöd för Azure-resurser för din funktion

Innan du kan distribuera funktions koden till Azure måste du skapa en <abbr title="En logisk behållare för relaterade Azure-resurser som du kan hantera som en enhet.">Resursgrupp</abbr>, en <abbr title="Ett konto som innehåller alla dina Azure Storage-dataobjekt. Lagrings kontot tillhandahåller ett unikt namn område för dina lagrings data.">storage account</abbr>, och en <abbr title="Moln resursen som är värd för funktioner utan server i Azure, vilket ger den underliggande beräknings miljön i vilken Functions körs.">function-app</abbr> med hjälp av följande kommandon:

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
 
    Kommandot [AZ Group Create](/cli/azure/group#az-group-create) skapar en resurs grupp. Du skapar vanligt vis resurs gruppen och resurserna i en <abbr title="En geografisk referens till ett särskilt Azure-datacenter där resurser allokeras.">region</abbr> nära dig, med en tillgänglig region som returneras från `az account list-locations` kommandot.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzResourceGroup -Name AzureFunctionsQuickstart-rg -Location westeurope
    ```

    Kommandot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) skapar en resurs grupp. Du skapar vanligt vis resurs gruppen och resurserna i en region nära dig, med hjälp av en tillgänglig region som returneras från cmdleten [Get-AzLocation](/powershell/module/az.resources/get-azlocation) .

    ---

    Du kan inte vara värd för Linux-och Windows-appar i samma resurs grupp. Om du har en befintlig resurs grupp med namnet `AzureFunctionsQuickstart-rg` med en Windows Function-app eller webbapp måste du använda en annan resurs grupp.

1. Skapa ett allmänt Azure Storage konto i din resurs grupp och region:

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

    Ersätt `<STORAGE_NAME>` med ett namn som passar dig och <abbr title="Namnet måste vara unikt för alla lagrings konton som används av alla Azure-kunder globalt. Du kan till exempel använda en kombination av ditt personliga eller företagets namn, program namn och en numerisk identifierare, som i contosobizappstorage20">unikt i Azure Storage</abbr>. Namn måste innehålla mellan 3 och 24 tecken och endast små bokstäver. `Standard_LRS` anger ett allmänt-syfte-konto, som [stöds av Functions](storage-considerations.md#storage-account-requirements).
    
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
    
    Ersätt `<STORAGE_NAME>` med namnet på det konto som du använde i föregående steg.

    Ersätt `<APP_NAME>` med en <abbr title="Ett namn som måste vara unikt för alla Azure-kunder globalt. Du kan till exempel använda en kombination av ditt personliga eller organisations namn, program namn och en numerisk identifierare, som i Contoso-bizapp-FUNC-20.">globalt unikt namn som passar dig</abbr>. `<APP_NAME>` är även DNS-standarddomänen för funktionsappen. 
    
    <br/>
    <details>
    <summary><strong>Vilken är kostnaden för resurserna som har allokerats i Azure?</strong></summary>

    Det här kommandot skapar en Function-app som körs på den angivna språk körningen under [Azure Functions förbruknings plan](functions-scale.md#overview-of-plans), vilket är kostnads fritt för den användnings mängd som du ådrar dig här. Kommandot etablerar även en associerad Azure Application insikts-instans i samma resurs grupp, som du kan använda för att övervaka din Function-app och Visa loggar. Mer information finns i [övervaka Azure Functions](functions-monitoring.md). Instansen debiteras inga kostnader förrän du aktiverar den.
    </details>

<br/>

---

## <a name="6-deploy-the-function-project-to-azure"></a>6. distribuera funktions projektet till Azure

När du har skapat din Function-app i Azure är du nu redo att **distribuera ditt lokala Functions-projekt** med hjälp av kommandot [FUNC Azure-functionapp Publish](functions-run-local.md#project-file-deployment) .  

I följande exempel ersätter du `<APP_NAME>` med namnet på din app.

```console
func azure functionapp publish <APP_NAME>
```

`publish`Kommandot visar resultat som liknar följande utdata (trunkerade för enkelhetens skull):

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

## <a name="7-invoke-the-function-on-azure"></a>7. anropa funktionen på Azure

Eftersom din funktion använder en HTTP-utlösare anropar du den genom att göra en HTTP-begäran till dess URL i webbläsaren eller med ett verktyg som <abbr title="Ett kommando rads verktyg för att generera HTTP-förfrågningar till en URL. Kontakta https://curl.se/">curl</abbr>. 

# <a name="browser"></a>[Webbläsare](#tab/browser)

Kopiera den fullständiga **anrops-URL: en** som visas i `publish` kommandots utdata till ett webbläsares adress fält, och lägg till Frågeparametern **&Name = Functions**. Webbläsaren bör visa liknande utdata som när du körde funktionen lokalt.

![Resultatet av funktionen körs på Azure i en webbläsare](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

Kör [`curl`](https://curl.haxx.se/) med **anrops-URL: en** och lägg till parametern **&Name = Functions**. Kommandots utdata ska vara texten, "Hello Functions".

![Resultatet av funktionen körs på Azure med hjälp av sväng](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---

### <a name="71-view-real-time-streaming-logs"></a>7,1 Visa strömnings loggar i real tid

Kör följande kommando för att Visa nästan real tids [strömnings loggar](functions-run-local.md#enable-streaming-logs) i Application Insights i Azure Portal:

```console
func azure functionapp logstream <APP_NAME> --browser
```

Ersätt `<APP_NAME>` med namnet på din Function-app.

I ett separat terminalfönster eller i webbläsaren, anropar du fjärrfunktionen igen. En utförlig logg över funktions körningen i Azure visas i terminalen. 

<br/>

---

## <a name="8-clean-up-resources"></a>8. Rensa resurser

Om du fortsätter till [Nästa steg](#next-steps) och lägger till ett <abbr title="Ett sätt att associera en funktion med en lagrings kö, så att den kan skapa meddelanden i kön. ">Utgående bindning för Azure Storage-kö</abbr>, se till att alla resurser finns på samma sätt som du bygger på vad du redan har gjort.

Annars kan du använda följande kommando för att ta bort resurs gruppen och alla resurser som finns i den för att undvika ytterligare kostnader.

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
> [Ansluta till en Azure Storage-kö](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-python)

[Har du problem? Berätta för oss.](https://aka.ms/python-functions-qs-survey)
