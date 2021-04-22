---
title: Utveckla och publicera .NET 5-funktioner med hjälp av Azure Functions
description: Lär dig hur du skapar och felsöker C#-funktioner med hjälp av .NET 5.0 och sedan distribuerar det lokala projektet till en serverlös värd i Azure Functions.
ms.date: 03/03/2021
ms.topic: how-to
zone_pivot_groups: development-environment-functions
ms.openlocfilehash: c76fde9a61ca60171ac094ef541e8c5841846aab
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866279"
---
# <a name="develop-and-publish-net-5-functions-using-azure-functions"></a>Utveckla och publicera .NET 5-funktioner med hjälp av Azure Functions 

Den här artikeln visar hur du arbetar med C#-funktioner med hjälp av .NET 5.0, som körs utanför processen från Azure Functions-körningen. Du får lära dig hur du skapar, felsöker lokalt och publicerar dessa .NET-isolerade processfunktioner till Azure. I Azure körs dessa funktioner i en isolerad process som stöder .NET 5.0. Mer information finns i Guide for running functions on .NET 5.0 in Azure (Guide för att köra funktioner på [.NET 5.0 i Azure).](dotnet-isolated-process-guide.md)

Om du inte behöver stödja .NET 5.0 eller köra dina funktioner utanför processen kan du i stället skapa en [C#-klassbiblioteksfunktion](functions-create-your-first-function-visual-studio.md).

>[!NOTE]
>Utveckling av isolerade .NET-processfunktioner i Azure Portal stöds inte för närvarande. Du måste använda antingen Azure CLI eller Visual Studio Code-publicering för att skapa en funktionsapp i Azure som har stöd för att köra .NET 5.0-appar out-of-process.   

## <a name="prerequisites"></a>Förutsättningar

+ Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ [.NET 5.0 SDK](https://dotnet.microsoft.com/download)

+ [Azure Functions Core Tools](functions-run-local.md#v2) version 3.0.3381 eller en senare version.

+ [Azure CLI](/cli/azure/install-azure-cli) version 2.20 eller en senare version.  
::: zone pivot="development-environment-vscode"
+ [Visual Studio Code](https://code.visualstudio.com/) på någon av [plattformarna som stöds](https://code.visualstudio.com/docs/supporting/requirements#_platforms).  

+ [C#-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) för Visual Studio Code.  

+ Tillägget [Azure Functions för](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) Visual Studio Code, version 1.3.0 eller senare.
::: zone-end
::: zone pivot="development-environment-vs"
+ [Visual Studio 2019](https://azure.microsoft.com/downloads/), inklusive **arbetsbelastningen Azure Development.**  
Projektmallar och publicering av isolerade funktioner i .NET är för närvarande inte tillgängliga i Visual Studio.
::: zone-end

## <a name="create-a-local-function-project"></a>Skapa ett lokalt funktionsprojekt

I Azure Functions är ett funktionsprojekt en container för en eller flera enskilda funktioner som var och en svarar på en specifik utlösare. Alla funktioner i ett projekt delar samma lokala konfigurationer och värdkonfigurationer. I det här avsnittet skapar du ett funktionsprojekt som innehåller en enda funktion.

::: zone pivot="development-environment-vs"

>[!NOTE]  
> Just nu finns det inga mallar Visual Studio som stöder skapandet av .NET-isolerade funktionsprojekt. Den här artikeln visar hur du använder Core Tools för att skapa ett C#-projekt som du sedan kan köra lokalt och felsöka i Visual Studio.  

::: zone-end

::: zone pivot="development-environment-vscode"  
1. Välj Azure-ikonen i Aktivitetsfältet och gör sedan följande i **Azure: I området för funktioner** väljer du ikonen **Skapa nytt projekt...**.

    ![Välj Skapa ett nytt projekt](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Välj en katalogplats för projektarbetsytan och välj **Välj**.

    > [!NOTE]
    > De här stegen har utformats för att slutföras utanför en arbetsyta. Välj i det här fallet inte en projektmapp som ingår i en arbetsyta.

1. Ange följande information i meddelanderutorna:

    + **Välj ett språk för ditt funktionsprojekt**: Välj `C#`.

    + **Välj en .NET-körning:** Välj `.NET 5 isolated` .

    + **Välj en mall för projektets första funktion**: Välj `HTTP trigger`.

    + **Ange ett funktionsnamn**: Skriv `HttpExample`.

    + **Ange ett namnområde**: Skriv `My.Functions`.

    + **Auktoriseringsnivå:** `Anonymous` Välj , som gör att alla kan anropa din funktionsslutpunkt. Mer information om auktoriseringsnivå finns i [Auktoriseringsnycklar.](functions-bindings-http-webhook-trigger.md#authorization-keys)

    + **Välj hur du vill öppna projektet**: Välj `Add to workspace`.

1. Med den här informationen Visual Studio Code ett Azure Functions projekt med en HTTP-utlösare. Du kan visa de lokala projektfilerna i Utforskaren. Mer information om filer som skapas finns i [Genererade projektfiler.](functions-develop-vs-code.md#generated-project-files)
::: zone-end  
::: zone pivot="development-environment-cli,development-environment-vs"  

1. Kör kommandot `func init` på följande sätt för att skapa ett funktionsprojekt i en mapp med namnet *LocalFunctionProj:*  

    ```console
    func init LocalFunctionProj --worker-runtime dotnetisolated
    ```
    
    Om `dotnetisolated` du anger skapas ett projekt som körs på .NET 5.0.


1. Navigera till projektmappen:

    ```console
    cd LocalFunctionProj
    ```

    Den här mappen innehåller olika filer för projektet, inklusive [local.settings.jspå](functions-run-local.md#local-settings-file) och [host.jskonfigurationsfiler.](functions-host-json.md) Eftersom *local.settings.jspå* kan innehålla hemligheter som laddats ned från Azure undantas filen från källkontrollen som standard i *.gitignore-filen.*

1. Lägg till en funktion i projektet med hjälp av följande kommando, där argumentet är det unika namnet på funktionen `--name` (HttpExample) och `--template` argumentet anger funktionens utlösare (HTTP).

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 

    `func new` skapar en HttpExample.cs-kodfil.
::: zone-end  

::: zone pivot="development-environment-vscode"  

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end  
::: zone pivot="development-environment-cli" 

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

::: zone-end

::: zone pivot="development-environment-vs"

## <a name="run-the-function-locally"></a>Köra funktionen lokalt

Nu kan du köra kommandot från roten i projektmappen för att kompilera `func start` och köra C#-projektet för isolerade funktioner. Om du för närvarande vill felsöka din out-of-process-funktionskod i Visual Studio måste du manuellt koppla en felsökare till körningsprocessen för Functions med hjälp av följande steg:  

1. Öppna projektfilen (.csproj) i Visual Studio. Du kan granska och ändra projektkoden och ange önskade brytpunkter i koden. 

1. Från rotprojektmappen använder du följande kommando från terminalen eller en kommandotolk för att starta körningsvärden:

    ```console
    func start --dotnet-isolated-debug
    ```

    Alternativet `--dotnet-isolated-debug` anger att processen ska vänta tills en felsökare har bifogas innan du fortsätter. Mot slutet av utdata bör du se något som liknar följande rader: 
    
    <pre>
    ...
    
    Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample

    For detailed output, run func with --verbose flag.
    [2021-03-09T08:41:41.904Z] Azure Functions .NET Worker (PID: 81720) initialized in debug mode. Waiting for debugger to attach...
    ...
    
    </pre> 

    `PID: XXXXXX`anger process-ID (PID) för dotnet.exe process som är den Functions-värd som körs.
 
1. I Azure Functions körtidsutdata anteckningen av process-ID för värdprocessen som du ska koppla ett felsökningsprogrammet till. Observera även URL:en för din lokala funktion.

1. På menyn **Felsök** i Visual Studio väljer du Koppla till **process...**, letar upp den process som matchar process-ID:t och väljer **Bifoga**. 
    
    :::image type="content" source="media/dotnet-isolated-process-developer-howtos/attach-to-process.png" alt-text="Koppla felsökaren till Functions-värdprocessen":::    

    Med felsökningsprogrammet bifogat kan du felsöka funktionskoden som vanligt.

1. I webbläsarens adressfält anger du url:en för den lokala funktionen, som ser ut så här, och kör begäran. 

    `http://localhost:7071/api/HttpExample`

    Du bör se spårningsutdata från begäran som skrivits till terminalen som körs. Kodkörningen stoppas vid de brytpunkter som du anger i funktionskoden.

1. När du är klar går du till terminalen och trycker på Ctrl + C för att stoppa värdprocessen.
 
När du har kontrollerat att funktionen körs korrekt på den lokala datorn är det dags att publicera projektet på Azure.

> [!NOTE]  
> Visual Studio publicering är för närvarande inte tillgängligt för .NET-isolerade processappar. När du har utvecklat ditt projekt i Visual Studio måste du använda Azure CLI för att skapa Azure-fjärrresurserna. Sedan kan du återigen använda Azure Functions Core Tools från kommandoraden för att publicera projektet till Azure.
::: zone-end

::: zone pivot="development-environment-cli,development-environment-vs" 
## <a name="create-supporting-azure-resources-for-your-function"></a>Skapa azure-stödresurser för din funktion

Innan du kan distribuera funktionskoden till Azure måste du skapa tre resurser:

- En [resursgrupp](../azure-resource-manager/management/overview.md), som är en logisk container för relaterade resurser.
- Ett [lagringskonto](../storage/common/storage-account-create.md), som används för att underhålla tillstånd och annan information om dina funktioner.
- En funktionsapp som tillhandahåller miljön för att köra funktionskoden. En funktionsapp mappar till ditt lokala funktionsprojekt och gör att du kan gruppera funktioner som en logisk enhet för enklare hantering, distribution och delning av resurser.

Använd följande kommandon för att skapa dessa objekt.

1. Om du inte redan har gjort det loggar du in på Azure:

    ```azurecli
    az login
    ```

    Kommandot [az login](/cli/azure/reference-index#az_login) loggar in dig på ditt Azure-konto.

1. Skapa en resursgrupp med `AzureFunctionsQuickstart-rg` namnet i `westeurope` regionen:

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    Kommandot [az group create](/cli/azure/group#az_group_create) skapar en resursgrupp. Vanligtvis skapar du din resursgrupp och dina resurser i en region nära dig, med hjälp av en tillgänglig region som returneras från `az account list-locations` kommandot .

1. Skapa ett allmänt lagringskonto i resursgruppen och regionen:

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    Kommandot [az storage account create](/cli/azure/storage/account#az_storage_account_create) skapar lagringskontot. 

    I föregående exempel ersätter du `<STORAGE_NAME>` med ett namn som är lämpligt för dig och unikt i Azure Storage. Namn får endast innehålla tre till 24 tecken och gemener. `Standard_LRS` anger ett konto för generell användning som stöds [av Functions](storage-considerations.md#storage-account-requirements).

1. Skapa funktionsappen i Azure:
   
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet-isolated --runtime-version 5.0 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```

    Kommandot [az functionapp create](/cli/azure/functionapp#az_functionapp_create) skapar funktionsappen i Azure. 
    
    I föregående exempel ersätter du med namnet på det konto som du använde i föregående steg och ersätter med ett `<STORAGE_NAME>` globalt unikt namn som passar `<APP_NAME>` dig. `<APP_NAME>` är även DNS-standarddomänen för funktionsappen. 
    
    Det här kommandot skapar en funktionsapp som kör .NET 5.0 under [Azure Functions Förbrukningsplan](consumption-plan.md). Den här planen bör vara kostnadsfri för den användning som du ådrar dig i den här artikeln. Kommandot tillser även en associerad Azure Application Insights-instans i samma resursgrupp. Använd den här instansen för att övervaka funktionsappen och visa loggar. Mer information finns i [Övervaka Azure Functions](functions-monitoring.md). Instansen medför inga kostnader förrän du aktiverar den.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

::: zone-end

::: zone pivot="development-environment-vscode"

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-the-project-to-azure"></a>Publicera projektet på Azure

I det här avsnittet skapar du en funktionsapp och relaterade resurser i din Azure-prenumeration och distribuerar sedan koden.

> [!IMPORTANT]
> Om du publicerar till en befintlig funktionsapp skrivs innehållet i den appen över i Azure.


1. Välj Azure-ikonen i aktivitetsfältet. I området **Azure: Funktioner** väljer du sedan **knappen Distribuera till funktionsapp....**

    ![Publicera projektet till Azure](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Ange följande information i meddelanderutorna:

    - **Välj mapp:** Välj en mapp från din arbetsyta eller bläddra till en som innehåller din funktionsapp. Du ser inte den här uppmaningen när du redan har öppnat en giltig funktionsapp.

    - **Välj prenumeration:** Välj den prenumeration som ska användas. Du ser inte den här uppmaningen när du bara har en prenumeration.

    - **Välj Funktionsapp i Azure**: Välj `- Create new Function App`. (Välj inte det alternativ `Advanced` som inte beskrivs i den här artikeln.)
      
    - **Ange ett globalt unikt namn för funktionsappen:** Ange ett namn som är giltigt i en URL-sökväg. Namnet du skriver verifieras för att se till att det är unikt i Azure Functions.
    
    - **Välj en körningsstack:** Välj `.NET 5 (non-LTS)` . 
    
    - **Välj en plats för nya resurser:** Om du vill ha bättre prestanda väljer du [en region](https://azure.microsoft.com/regions/) nära dig. 
    
    I meddelandefältet visas status för enskilda resurser när de skapas i Azure.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Meddelande om skapande av Azure-resurser":::
    
1.  När du är klar skapas följande Azure-resurser i din prenumeration med namn som baseras på funktionsappens namn:
    
    [!INCLUDE [functions-vs-code-created-resources](../../includes/functions-vs-code-created-resources.md)]

    Ett meddelande visas när funktionsappen har skapats och distributionspaketet har tillämpats. 

    [!INCLUDE [functions-vs-code-create-tip](../../includes/functions-vs-code-create-tip.md)]

4. Välj **Visa utdata** i det här meddelandet för att visa skapande- och distributionsresultat, inklusive de Azure-resurser som du skapade. Om du missar meddelandet väljer du klockikonen i det nedre högra hörnet för att se det igen.

    ![Skapa ett fullständigt meddelande](../../includes/media/functions-publish-project-vscode/function-create-notifications.png)

::: zone-end

::: zone pivot="development-environment-cli"  
[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]  
::: zone-end  

::: zone pivot="development-environment-vscode"  
[!INCLUDE [functions-vs-code-run-remote](../../includes/functions-vs-code-run-remote.md)]  
::: zone-end  

## <a name="clean-up-resources"></a>Rensa resurser

Du har skapat resurser för att slutföra den här artikeln. Det är möjligt att du debiteras för de här resurserna beroende på din [kontostatus](https://azure.microsoft.com/account/) och dina [servicepriser](https://azure.microsoft.com/pricing/). 

::: zone pivot="development-environment-cli"  
Använd följande kommando för att ta bort resursgruppen och alla dess inneslutna resurser för att undvika ytterligare kostnader.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```
::: zone-end  

::: zone pivot="development-environment-vscode"  
Använd följande steg för att ta bort funktionsappen och dess relaterade resurser för att undvika ytterligare kostnader.

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]  
::: zone-end  
::: zone pivot="development-environment-vs"   
Använd följande steg för att ta bort funktionsappen och dess relaterade resurser för att undvika ytterligare kostnader.

1. I Cloud Explorer expanderar du prenumerationsappen **> App Services,** högerklickar på funktionsappen och väljer **Öppna i portalen.** 

1. På funktionsappsidan väljer du **fliken Översikt** och sedan länken under **Resursgrupp.**

   :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-app-delete-resource-group.png" alt-text="Välj den resursgrupp som ska tas bort från funktionsappens sida":::

2. På sidan **Resursgrupp** granskar du listan över inkluderade resurser och kontrollerar att det är dessa som du vill ta bort.
 
3. Välj **Ta bort resursgrupp** och följ instruktionerna.

   Borttagningen kan ta några minuter. När du är färdig visas ett meddelande i några sekunder. Du kan även välja klockikonen längst upp på sidan för att se meddelandet.
::: zone-end

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Läs mer om isolerade .NET-funktioner](dotnet-isolated-process-guide.md)

