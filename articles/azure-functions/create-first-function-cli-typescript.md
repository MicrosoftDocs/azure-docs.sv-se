---
title: Skapa en TypeScript-funktion från kommando raden – Azure Functions
description: Lär dig hur du skapar en TypeScript-funktion från kommando raden och sedan publicerar det lokala projektet till Server lös värd i Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom: devx-track-azurecli
ms.openlocfilehash: 488ef9fa3fd5b6c09ed435483dbf8f6fa3eb5bef
ms.sourcegitcommit: 2aa52d30e7b733616d6d92633436e499fbe8b069
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/06/2021
ms.locfileid: "97937203"
---
# <a name="quickstart-create-a-typescript-function-in-azure-from-the-command-line"></a>Snabb start: skapa en TypeScript-funktion i Azure från kommando raden

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

I den här artikeln använder du kommando rads verktyg för att skapa en TypeScript-funktion som svarar på HTTP-begäranden. När du har testat koden lokalt distribuerar du den till den serverbaserade miljön för Azure Functions.

Att slutföra den här snabb starten innebär en låg kostnad av några USD cent eller mindre i ditt Azure-konto.

Det finns också en [Visual Studio Code-baserad version](create-first-function-vs-code-typescript.md) av den här artikeln.

## <a name="configure-your-local-environment"></a>Konfigurera din lokala miljö

Innan du börjar måste du ha följande:

+ Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core tools](functions-run-local.md#v2) version 3. x.

+ Ett av följande verktyg för att skapa Azure-resurser:

    + [Azure CLI](/cli/azure/install-azure-cli) version 2,4 eller senare.

    + [Azure PowerShell](/powershell/azure/install-az-ps) version 5,0 eller senare.

+ [Node.js](https://nodejs.org/), aktiva LTS-och UNDERHÅLLs LTS-versioner (8.11.1 och 10.14.1 rekommenderas).

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
    func init LocalFunctionProj --typescript
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

#### <a name="indexts"></a>index. TS

*index. TS* exporterar en funktion som utlöses enligt konfigurationen i *function.jspå*.

:::code language="typescript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-TypeScript/index.ts":::

För en HTTP-utlösare tar funktionen emot begär ande data i variabeln `req` av typen **HttpRequest** som definieras i *function.jspå*. Returvärdet, som definieras som `$return` i *function.jspå*, är svaret. 

#### <a name="functionjson"></a>function.json

*function.jspå* är en konfigurations fil som definierar indata och utdata `bindings` för funktionen, inklusive utlösnings typen. 

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json":::

Varje bindning kräver en riktning, en typ och ett unikt namn. HTTP-utlösaren har en inkommande bindning av typen [`httpTrigger`](functions-bindings-http-webhook-trigger.md) och utgående bindningen av typen [`http`](functions-bindings-http-webhook-output.md) .

## <a name="run-the-function-locally"></a>Köra funktionen lokalt

1. Kör din funktion genom att starta den lokala Azure Functions runtime-värden från mappen *LocalFunctionProj* :

    ```console
    npm install
    npm start
    ```
    
    I slutet av utdata bör följande rader visas:
    
    <pre>
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    >[!NOTE]  
    > Om HttpExample inte visas som visas nedan, kommer du förmodligen igång värden från utsidan av projektets rotmapp. I så fall kan du använda **CTRL** + **C** för att stoppa värden, navigera till projektets rotmapp och köra föregående kommando igen.

1. Kopiera URL: en för din `HttpExample` funktion från utdata till en webbläsare och Lägg till frågesträngen och `?name=<your-name>` gör den fullständiga URL: en som `http://localhost:7071/api/HttpExample?name=Functions` . Webbläsaren ska visa ett meddelande som `Hello Functions` :

    ![Resultatet av funktionen körs lokalt i webbläsaren](./media/functions-create-first-azure-function-azure-cli/function-test-local-browser.png)
    
    Terminalen där du startade projektet visar också logg data när du gör förfrågningar.

1. När du är klar kan du använda **CTRL** + **C** och välja `y` att stoppa funktions värden.

[!INCLUDE [functions-create-azure-resources-cli](../../includes/functions-create-azure-resources-cli.md)]

4. Skapa Function-appen i Azure:

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
        
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime node --runtime-version 12 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```
    
    Kommandot [AZ functionapp Create](/cli/azure/functionapp#az_functionapp_create) skapar Function-appen i Azure. Om du använder Node.js 10 ändrar du också `--runtime-version` till `10` .
    
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)
    
    ```azurepowershell
    New-AzFunctionApp -Name <APP_NAME> -ResourceGroupName AzureFunctionsQuickstart-rg -StorageAccount <STORAGE_NAME> -Runtime node -RuntimeVersion 12 -FunctionsVersion 3 -Location 'West Europe'
    ```
    
    Cmdlet: en [New-AzFunctionApp](/powershell/module/az.functions/new-azfunctionapp) skapar Function-appen i Azure. Ändra till om du använder Node.js 10 `-RuntimeVersion` `10` .

    ---
        
    I föregående exempel ersätter `<STORAGE_NAME>` du med namnet på det konto som du använde i föregående steg och ersätter `<APP_NAME>` med ett globalt unikt namn som passar dig. `<APP_NAME>` är även DNS-standarddomänen för funktionsappen. 
    
    Det här kommandot skapar en Function-app som körs på den angivna språk körningen under [Azure Functions förbruknings plan](consumption-plan.md), vilket är kostnads fritt för den användnings mängd som du ådrar dig här. Kommandot etablerar även en associerad Azure Application insikts-instans i samma resurs grupp, som du kan använda för att övervaka din Function-app och Visa loggar. Mer information finns i [övervaka Azure Functions](functions-monitoring.md). Instansen debiteras inga kostnader förrän du aktiverar den.

## <a name="deploy-the-function-project-to-azure"></a>Distribuera funktions projektet till Azure

Innan du använder kärn verktyg för att distribuera ditt projekt till Azure skapar du en produktions klar version av JavaScript-filer från källfilerna för TypeScript.

1. Använd följande kommando för att förbereda ditt TypeScript-projekt för distribution:

    ```console
    npm run build:production 
    ```

1. Med nödvändiga resurser på plats är du nu redo att distribuera ditt lokala Functions-projekt till Function-appen i Azure med hjälp av kommandot [FUNC Azure functionapp Publish](functions-run-local.md#project-file-deployment) . I följande exempel ersätter du `<APP_NAME>` med namnet på din app.

    ```console
    func azure functionapp publish <APP_NAME>
    ```
    
    Om du ser felet "det går inte att hitta appen med namnet..." väntar du några sekunder och försöker igen, eftersom Azure kanske inte har fullständigt initierat appen efter föregående `az functionapp create` kommando.
    
    Kommandot Publicera visar resultat som liknar följande utdata (trunkerade för enkelhetens skull):
    
    <pre>
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
            Invoke url: https://msdocs-azurefunctions-qs.azurewebsites.net/api/httpexample?code=KYHrydo4GFe9y0000000qRgRJ8NdLFKpkakGJQfC3izYVidzzDN4gQ==
    </pre>

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

[!INCLUDE [functions-cleanup-resources-cli](../../includes/functions-cleanup-resources-cli.md)]

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Ansluta till en Azure Storage-kö](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-typescript)
