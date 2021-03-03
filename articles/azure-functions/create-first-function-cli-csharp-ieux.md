---
title: Skapa en C#-funktion från kommando raden – Azure Functions
description: Lär dig hur du skapar en C#-funktion från kommando raden och sedan publicerar det lokala projektet till Server lös värd i Azure Functions.
ms.date: 10/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-csharp
- devx-track-azurecli
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: e5a3872cace0c2a691d64f4c00bd29dc3248f27f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101748827"
---
# <a name="quickstart-create-a-c-function-in-azure-from-the-command-line"></a>Snabb start: skapa en C#-funktion i Azure från kommando raden

> [!div class="op_single_selector" title1="Välj ditt funktions språk: "]
> - [C#](create-first-function-cli-csharp-uiex.md)
> - [Java](create-first-function-cli-java.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [PowerShell](create-first-function-cli-powershell.md)
> - [Python](create-first-function-cli-python.md)
> - [TypeScript](create-first-function-cli-typescript.md)

I den här artikeln använder du kommando rads verktyg för att skapa en C#-baserad funktion i C#-klassen som svarar på HTTP-begäranden. När du har testat koden lokalt distribuerar du den till <abbr title="En körnings miljö för körning där all information om servern är transparent för programutvecklare, vilket fören klar processen med att distribuera och hantera kod.">serverlöst</abbr> miljö i <abbr title="Azures tjänst som tillhandahåller en enklare data behandlings miljö utan kostnad för program.">Azure Functions</abbr>.

Att slutföra den här snabb starten innebär en låg kostnad av några USD cent eller mindre i ditt Azure-konto.

Det finns också en [Visual Studio Code-baserad version](create-first-function-vs-code-csharp.md) av den här artikeln.

## <a name="1-prepare-your-environment"></a>1. Förbered din miljö

+ Skaffa en Azure <abbr title="Den profil som hanterar fakturerings information för Azure-användning.">konto</abbr> med en aktiv <abbr title="Den grundläggande organisations strukturen i vilken du hanterar resurser i Azure, som vanligt vis är kopplade till en individ eller avdelning inom en organisation.">prenumeration</abbr>. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ Installera [.NET Core SDK 3,1](https://www.microsoft.com/net/download)

+ Installera [Azure Functions Core tools](functions-run-local.md#v2) version 3. x.

+ Antingen <abbr title="En uppsättning kommando rads verktyg för flera plattformar för att arbeta med Azure-resurser från din lokala utvecklings dator, som ett alternativ till att använda Azure Portal.">Azure CLI</abbr> eller <abbr title="En PowerShell-modul som innehåller kommandon för att arbeta med Azure-resurser från din lokala utvecklings dator, som ett alternativ till att använda Azure Portal.">Azure PowerShell</abbr> för att skapa Azure-resurser:

    + [Azure CLI](/cli/azure/install-azure-cli) version 2,4 eller senare.

    + [Azure PowerShell](/powershell/azure/install-az-ps) version 5,0 eller senare.

---

### <a name="2-verify-prerequisites"></a>2. kontrol lera krav

Verifiera dina krav, beroende på om du använder Azure CLI eller Azure PowerShell för att skapa Azure-resurser:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ I en terminal-eller kommando fönster, kör `func --version` för att kontrol lera att <abbr title="Uppsättningen kommando rads verktyg för att arbeta med Azure Functions på den lokala datorn.">Azure Functions Core Tools</abbr> är version 3. x.

+ **Kör** `az --version` för att kontrol lera att Azure CLI-versionen är 2,4 eller senare.

+ **Kör** `az login` för att logga in på Azure och verifiera en aktiv prenumeration.

+ **Kör** `dotnet --list-sdks` kontrol lera att .NET Core SDK version 3.1. x har installerats

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+**Kör** `func --version` för att kontrol lera att Azure Functions Core Tools är version 3. x.

+ **Kör** `(Get-Module -ListAvailable Az).Version` och kontrol lera version 5,0 eller senare. 

+ **Kör** `Connect-AzAccount` för att logga in på Azure och verifiera en aktiv prenumeration.

+ **Kör** `dotnet --list-sdks` kontrol lera att .NET Core SDK version 3.1. x har installerats

---

## <a name="3-create-a-local-function-project"></a>3. skapa ett lokalt funktions projekt

I det här avsnittet skapar du en lokal <abbr title="En logisk behållare för en eller flera enskilda funktioner som kan distribueras och hanteras tillsammans.">Azure Functions projekt</abbr> i C#. Varje funktion i projektet svarar på ett särskilt <abbr title="En händelse som anropar funktionens kod, till exempel en HTTP-begäran, ett Queue-meddelande eller en angiven tid.">utlösare</abbr>.

1. Kör `func init` kommandot för att skapa ett Functions-projekt i en mapp med namnet *LocalFunctionProj* med den angivna körnings miljön:  

    ```csharp
    func init LocalFunctionProj --dotnet
    ```

1. **Kör** "CD-LocalFunctionProj" för att navigera till <abbr title="Den här mappen innehåller olika filer för projektet, inklusive konfigurationsfiler som heter local.settings.jspå och host.js. Eftersom local.settings.jspå kan innehålla hemligheter som hämtats från Azure, undantas filen från käll kontroll som standard i. gitignore-filen.">Projektmapp</abbr>.

    ```console
    cd LocalFunctionProj
    ```
    <br/>

1. Lägg till en funktion i projektet med hjälp av följande kommando:
    
    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 
    `--name`Argumentet är det unika namnet för din funktion (HttpExample).

    `--template`Argumentet anger funktionens utlösare (http).

    
    <br/>   
    <details>  
    <summary><strong>Valfritt: kod för HttpExample.cs</strong></summary>  
    
    *HttpExample.cs* innehåller en `Run` metod som tar emot begär ande data i `req` variabeln är en [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest) som är dekorerad med **HttpTriggerAttribute**, vilket definierar utlösnings beteendet.

    :::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::
        
    Returvärdet är en [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) som returnerar ett svarsmeddelande som antingen en [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) eller en [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). Läs mer i [Azure Functions HTTP-utlösare och bindningar](./functions-bindings-http-webhook.md?tabs=csharp).  
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


1. Välj  <kbd>CTRL + C</kbd> och välj <kbd>y</kbd> för att stoppa funktions värden.

<br/>

---
    
## <a name="5-create-supporting-azure-resources-for-your-function"></a>5. skapa stöd för Azure-resurser för din funktion

Innan du kan distribuera funktions koden till Azure måste du skapa en <abbr title="En logisk behållare för relaterade Azure-resurser som du kan hantera som en enhet.">Resursgrupp</abbr>, en <abbr title="Ett konto som innehåller alla dina Azure Storage-dataobjekt. Lagrings kontot tillhandahåller ett unikt namn område för dina lagrings data.">storage account</abbr>, och en <abbr title="Moln resursen som är värd för funktioner utan server i Azure, vilket ger den underliggande beräknings miljön i vilken Functions körs.">function-app</abbr> med hjälp av följande kommandon:

1. Om du inte redan har gjort det loggar du in på Azure:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```


    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```


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


    ---

    Du kan inte vara värd för Linux-och Windows-appar i samma resurs grupp. Om du har en befintlig resurs grupp med namnet `AzureFunctionsQuickstart-rg` med en Windows Function-app eller webbapp måste du använda en annan resurs grupp.
    
1. Skapa ett allmänt Azure Storage konto i din resurs grupp och region:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```


    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```


    ---

    Ersätt `<STORAGE_NAME>` med ett namn som passar dig och <abbr title="Namnet måste vara unikt för alla lagrings konton som används av alla Azure-kunder globalt. Du kan till exempel använda en kombination av ditt personliga eller företagets namn, program namn och en numerisk identifierare, som i contosobizappstorage20">unikt i Azure Storage</abbr>. Namn måste innehålla mellan 3 och 24 tecken och endast små bokstäver. `Standard_LRS` anger ett allmänt-syfte-konto, som [stöds av Functions](storage-considerations.md#storage-account-requirements).


1. Skapa Function-appen i Azure.
**Ersätt** ' <STORAGE_NAME> * * med namnet i föregående steg.
**Ersätt** <APP_NAME> med ett globalt unikt namn.

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime dotnet -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    
    ---
    
    Ersätt `<STORAGE_NAME>` med namnet på det konto som du använde i föregående steg.

    Ersätt `<APP_NAME>` med en <abbr title="Ett namn som måste vara unikt för alla Azure-kunder globalt. Du kan till exempel använda en kombination av ditt personliga eller organisations namn, program namn och en numerisk identifierare, som i Contoso-bizapp-FUNC-20.">unikt namn</abbr>. `<APP_NAME>` är även DNS-standarddomänen för funktionsappen. 

    <br/>
    <details>
    <summary><strong>Vilken är kostnaden för resurserna som har allokerats i Azure?</strong></summary>

    Det här kommandot skapar en Function-app som körs på den angivna språk körningen under [Azure Functions förbruknings plan](consumption-plan.md), vilket är kostnads fritt för den användnings mängd som du ådrar dig här. Kommandot etablerar även en associerad Azure Application insikts-instans i samma resurs grupp, som du kan använda för att övervaka din Function-app och Visa loggar. Mer information finns i [övervaka Azure Functions](functions-monitoring.md). Instansen debiteras inga kostnader förrän du aktiverar den.
    </details>

<br/>

---

## <a name="6-deploy-the-function-project-to-azure"></a>6. distribuera funktions projektet till Azure


**Kopiera** "FUNC Azure funtionapp Publish <APP_NAME> i din terminal **Ersätt** `<APP_NAME>` med namnet på din app.
**Kör**

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

Kopiera den fullständiga **anrops-URL: en** som visas i `publish` kommandots utdata till ett webbläsares adress fält. **Lägg till** Frågeparametern **&Name = Functions**. 

![Resultatet av funktionen körs på Azure i en webbläsare](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

<br/>

---

## <a name="8-clean-up-resources"></a>8. Rensa resurser

Om du fortsätter till [Nästa steg](#next-steps) och lägger till en Azure Storage Queue-utdata <abbr title="En deklarativ anslutning mellan en funktion och andra resurser. En indata-bindning tillhandahåller data till funktionen. en utgående bindning tillhandahåller data från funktionen till andra resurser.">enheten</abbr>, se till att alla resurser finns på samma sätt som du bygger på vad du redan har gjort.

Annars kan du använda följande kommando för att ta bort resurs gruppen och alla resurser som finns i den för att undvika ytterligare kostnader.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name AzureFunctionsQuickstart-rg
```

---

<br/>

---

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Ansluta till en Azure Storage-kö](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-csharp)
