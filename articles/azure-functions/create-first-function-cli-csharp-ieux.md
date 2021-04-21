---
title: Skapa en C#-funktion från kommandoraden – Azure Functions
description: Lär dig hur du skapar en C#-funktion från kommandoraden och sedan publicerar det lokala projektet till serverlös värd i Azure Functions.
ms.date: 10/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-csharp
- devx-track-azurecli
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 2d03f8c820e0a8b6a19394649db66f8028b62781
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107768803"
---
# <a name="quickstart-create-a-c-function-in-azure-from-the-command-line"></a>Snabbstart: Skapa en C#-funktion i Azure från kommandoraden

> [!div class="op_single_selector" title1="Välj funktionsspråk: "]
> - [C#](create-first-function-cli-csharp-ieux.md)
> - [Java](create-first-function-cli-java.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [PowerShell](create-first-function-cli-powershell.md)
> - [Python](create-first-function-cli-python.md)
> - [TypeScript](create-first-function-cli-typescript.md)

I den här artikeln använder du kommandoradsverktyg för att skapa en biblioteksbaserad C#-klassfunktion som svarar på HTTP-begäranden. När du har testat koden lokalt distribuerar du den till <abbr title="En körningsmiljö där all information om servern är transparent för programutvecklare, vilket förenklar processen med att distribuera och hantera kod.">serverlöst</abbr> miljö för <abbr title="Azures tjänst som tillhandahåller en serverlös datormiljö med låg kostnad för program.">Azure Functions</abbr>.

När du slutför den här snabbstarten medför det en liten kostnad på några cent eller mindre för ditt Azure-konto.

Det finns även en [Visual Studio kodbaserad version av](create-first-function-vs-code-csharp.md) den här artikeln.

## <a name="1-prepare-your-environment"></a>1. Förbered din miljö

+ Skaffa ett Azure <abbr title="Profilen som underhåller faktureringsinformation för Azure-användning.">konto</abbr> med en aktiv <abbr title="Den grundläggande organisationsstrukturen där du hanterar resurser i Azure, vanligtvis associerad med en person eller avdelning inom en organisation.">prenumeration</abbr>. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ Installera [.NET Core SDK 3.1](https://www.microsoft.com/net/download)

+ Installera [Azure Functions Core Tools](functions-run-local.md#v2) version 3.x.

+ Antingen <abbr title="En uppsättning plattformsoberoende kommandoradsverktyg för att arbeta med Azure-resurser från din lokala utvecklingsdator, som ett alternativ till att använda Azure Portal.">Azure CLI</abbr> eller <abbr title="En PowerShell-modul som innehåller kommandon för att arbeta med Azure-resurser från din lokala utvecklingsdator som ett alternativ till att använda Azure Portal.">Azure PowerShell</abbr> för att skapa Azure-resurser:

    + [Azure CLI](/cli/azure/install-azure-cli) version 2.4 eller senare.

    + [Azure PowerShell](/powershell/azure/install-az-ps) version 5.0 eller senare.

---

### <a name="2-verify-prerequisites"></a>2. Kontrollera förhandskrav

Kontrollera kraven, som beror på om du använder Azure CLI eller Azure PowerShell för att skapa Azure-resurser:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

+ I en terminal eller ett kommandofönster kör du `func --version` för att kontrollera att <abbr title="En uppsättning kommandoradsverktyg för att arbeta med Azure Functions på din lokala dator.">Azure Functions Core Tools</abbr> är version 3.x.

+ **Kör** `az --version` för att kontrollera att Azure CLI-versionen är 2.4 eller senare.

+ **Kör** `az login` för att logga in på Azure och verifiera en aktiv prenumeration.

+ **Kör** `dotnet --list-sdks` för att kontrollera .NET Core SDK version 3.1.x är installerad

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

+**Kör** `func --version` för att kontrollera att Azure Functions Core Tools version 3.x.

+ **Kör** `(Get-Module -ListAvailable Az).Version` och verifiera version 5.0 eller senare. 

+ **Kör** `Connect-AzAccount` för att logga in på Azure och verifiera en aktiv prenumeration.

+ **Kör** `dotnet --list-sdks` för att kontrollera .NET Core SDK version 3.1.x är installerad

---

## <a name="3-create-a-local-function-project"></a>3. Skapa ett lokalt funktionsprojekt

I det här avsnittet skapar du en lokal <abbr title="En logisk container för en eller flera enskilda funktioner som kan distribueras och hanteras tillsammans.">Azure Functions projekt</abbr> i C#. Varje funktion i projektet svarar på en specifik <abbr title="En händelse som anropar funktionens kod, till exempel en HTTP-begäran, ett kömeddelande eller en viss tid.">Utlösa</abbr>.

1. Kör kommandot `func init` för att skapa ett functions-projekt i en mapp med namnet *LocalFunctionProj* med den angivna körningen:  

    ```csharp
    func init LocalFunctionProj --dotnet
    ```

1. **Kör** "cd LocalFunctionProj" för att navigera till <abbr title="Den här mappen innehåller olika filer för projektet, inklusive konfigurationsfiler med local.settings.jspå och host.jspå. Eftersom local.settings.jspå kan innehålla hemligheter som laddats ned från Azure undantas filen från källkontrollen som standard i .gitignore-filen.">Projektmapp</abbr>.

    ```console
    cd LocalFunctionProj
    ```
    <br/>

1. Lägg till en funktion i projektet med hjälp av följande kommando:
    
    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 
    Argumentet `--name` är det unika namnet på din funktion (HttpExample).

    Argumentet `--template` anger funktionens utlösare (HTTP).

    
    <br/>   
    <details>  
    <summary><strong>Valfritt: Kod för HttpExample.cs</strong></summary>  
    
    *HttpExample.cs* innehåller en metod som tar emot begärandedata i variabeln är en HttpRequest som är prydlig med `Run` `req` **HttpTriggerAttribute**, som definierar utlösarbeteendet. [](/dotnet/api/microsoft.aspnetcore.http.httprequest)

    :::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::
        
    Det returnerade objektet är [ett ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) som returnerar ett svarsmeddelande som antingen [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) eller [ett BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). Mer information finns i Azure Functions [HTTP-utlösare och bindningar.](./functions-bindings-http-webhook.md?tabs=csharp)  
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


1. Välj  <kbd>Ctrl+C</kbd> och <kbd>välj y för</kbd> att stoppa functions-värden.

<br/>

---
    
## <a name="5-create-supporting-azure-resources-for-your-function"></a>5. Skapa azure-stödresurser för din funktion

Innan du kan distribuera funktionskoden till Azure måste du skapa en <abbr title="En logisk container för relaterade Azure-resurser som du kan hantera som en enhet.">Resursgrupp</abbr>A <abbr title="Ett konto som innehåller alla dina Azure Storage-dataobjekt. Lagringskontot tillhandahåller ett unikt namnområde för dina lagringsdata.">storage account</abbr>och en <abbr title="Den molnresurs som är värd för serverlösa funktioner i Azure, som tillhandahåller den underliggande beräkningsmiljö där funktioner körs.">funktionsapp</abbr> med hjälp av följande kommandon:

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


    ---

    Du kan inte vara värd för Linux- och Windows-appar i samma resursgrupp. Om du har en befintlig resursgrupp med `AzureFunctionsQuickstart-rg` namnet med en Windows-funktionsapp eller webbapp måste du använda en annan resursgrupp.
    
1. Skapa ett allmänt Azure Storage i din resursgrupp och region:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```


    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

    ```azurepowershell
    New-AzStorageAccount -ResourceGroupName AzureFunctionsQuickstart-rg -Name <STORAGE_NAME> -SkuName Standard_LRS -Location westeurope
    ```


    ---

    Ersätt `<STORAGE_NAME>` med ett namn som är lämpligt för dig och <abbr title="Namnet måste vara unikt för alla lagringskonton som används av alla Azure-kunder globalt. Du kan till exempel använda en kombination av ditt personliga namn eller företagsnamn, programnamn och en numerisk identifierare, som i contoszenzappstorage20">unikt i Azure Storage</abbr>. Namn får endast innehålla tre till 24 tecken och gemener. `Standard_LRS` anger ett konto för generell användning som stöds [av Functions](storage-considerations.md#storage-account-requirements).


1. Skapa funktionsappen i Azure.
**Ersätt** "<STORAGE_NAME>** med namnet i föregående steg.
**Ersätt** "<APP_NAME>" med ett globalt unikt namn.

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

    Ersätt `<APP_NAME>` med en <abbr title="Ett namn som måste vara unikt för alla Azure-kunder globalt. Du kan till exempel använda en kombination av ditt personliga namn eller organisationsnamn, programnamn och en numerisk identifierare, som i contoso-bizapp-func-20.">unikt namn</abbr>. `<APP_NAME>` är även DNS-standarddomänen för funktionsappen. 

    <br/>
    <details>
    <summary><strong>Vad kostar det att etablera resurser i Azure?</strong></summary>

    Det här kommandot skapar en funktionsapp som körs i din angivna språkkörning under [Azure Functions Consumption-planen](consumption-plan.md), vilket är kostnadsfritt för den mängd användning som du ådrar dig här. Kommandot tiller även en associerad Azure Application Insights-instans i samma resursgrupp som du kan använda för att övervaka funktionsappen och visa loggar. Mer information finns i [Övervaka Azure Functions](functions-monitoring.md). Instansen medför inga kostnader förrän du aktiverar den.
    </details>

<br/>

---

## <a name="6-deploy-the-function-project-to-azure"></a>6. Distribuera funktionsprojektet till Azure


**Kopiera** 'func azure funtionapp publish <APP_NAME> into your terminal **Replace** `<APP_NAME>` with the name of your app.
**Kör**

```console
func azure functionapp publish <APP_NAME>
```

Kommandot `publish` visar resultat som liknar följande utdata (trunkerat för enkelhetens skull):

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

Kopiera den fullständiga **Invoke URL (Anropa URL)** som visas i `publish` kommandots utdata till ett adressfält i webbläsaren. **Lägg till** frågeparametern **&name=Functions**. 

![Utdata från funktionen körs på Azure i en webbläsare](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

<br/>

---

## <a name="8-clean-up-resources"></a>8. Rensa resurser

Om du fortsätter till nästa [steg och lägger till](#next-steps) en Azure Storage köutdata <abbr title="En deklarativ anslutning mellan en funktion och andra resurser. En indatabindning tillhandahåller data till funktionen. en utdatabindning ger data från funktionen till andra resurser.">Bindande</abbr>håller du alla dina resurser på plats eftersom du bygger vidare på det du redan har gjort.

Annars använder du följande kommando för att ta bort resursgruppen och alla dess inneslutna resurser för att undvika ytterligare kostnader.

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
> [Ansluta till en Azure Storage kö](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-csharp)
