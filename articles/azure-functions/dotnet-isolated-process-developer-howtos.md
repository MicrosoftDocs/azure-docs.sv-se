---
title: Utveckla och publicera .NET 5-funktioner med hjälp av Azure Functions
description: Lär dig hur du skapar och felsöker C#-funktioner med hjälp av .NET 5,0 och sedan distribuerar det lokala projektet till Server lös värd i Azure Functions.
ms.date: 03/03/2021
ms.topic: how-to
zone_pivot_groups: development-environment-functions
ms.openlocfilehash: 9751d5d354d738a64d6b02e8153464491d78ec14
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "103201372"
---
# <a name="develop-and-publish-net-5-functions-using-azure-functions"></a>Utveckla och publicera .NET 5-funktioner med hjälp av Azure Functions 

Den här artikeln visar hur du arbetar med C#-funktioner med hjälp av .NET 5,0, som körs utanför processen från Azure Functions Runtime. Du får lära dig att skapa, felsöka lokalt och publicera de här .NET-isolerade process funktionerna i Azure. I Azure körs dessa funktioner i en isolerad process som stöder .NET 5,0. Mer information finns i [Guide för att köra funktioner i .net 5,0 i Azure](dotnet-isolated-process-guide.md).

Om du inte behöver stöd för .NET 5,0 eller köra funktionerna utanför processen, kanske du vill [skapa en C#-funktion för klass bibliotek](functions-create-your-first-function-visual-studio.md)i stället.

>[!NOTE]
>Utveckling av funktioner för isolerade .NET-processer i Azure Portal stöds inte för närvarande. Du måste använda antingen Azure CLI-eller Visual Studio Code Publishing för att skapa en Function-app i Azure som stöder körning av .NET 5,0-appar utanför processen.   

## <a name="prerequisites"></a>Förutsättningar

+ Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [.NET SDK 5.0](https://www.microsoft.com/net/download)

+ [Azure Functions Core tools](functions-run-local.md#v2) version 3.0.3381 eller en senare version.

+ [Azure CLI](/cli/azure/install-azure-cli) version 2,20 eller en senare version.  
::: zone pivot="development-environment-vscode"
+ [Visual Studio Code](https://code.visualstudio.com/) på någon av [plattformarna som stöds](https://code.visualstudio.com/docs/supporting/requirements#_platforms).  

+ [C#-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-dotnettools.csharp) för Visual Studio Code.  

+ [Azure Functions-tillägget](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) för Visual Studio Code, version 1.3.0 eller senare.
::: zone-end
::: zone pivot="development-environment-vs"
+ [Visual Studio 2019](https://azure.microsoft.com/downloads/), inklusive arbets belastningen **Azure Development** .  
Project-isolerade funktioner och publicering för de isolerade funktionerna är inte tillgängliga i Visual Studio.
::: zone-end

## <a name="create-a-local-function-project"></a>Skapa ett lokalt funktions projekt

I Azure Functions är ett funktions projekt en behållare för en eller flera enskilda funktioner som varje svarar på en viss utlösare. Alla funktioner i ett projekt delar samma lokala och värdbaserade konfigurationer. I det här avsnittet skapar du ett funktions projekt som innehåller en enda funktion.

::: zone pivot="development-environment-vs"

>[!NOTE]  
> För närvarande finns det inga Visual Studio-projektmallar som stöder generering av isolerade funktions projekt i .NET. Den här artikeln visar hur du använder kärn verktyg för att skapa ett C#-projekt, som du sedan kan köra lokalt och felsöka i Visual Studio.  

::: zone-end

::: zone pivot="development-environment-vscode"  
1. Välj Azure-ikonen i Aktivitetsfältet och gör sedan följande i **Azure: I området för funktioner** väljer du ikonen **Skapa nytt projekt...**.

    ![Välj Skapa ett nytt projekt](./media/functions-create-first-function-vs-code/create-new-project.png)

1. Välj en katalogplats för projektarbetsytan och välj **Välj**.

    > [!NOTE]
    > De här stegen har utformats för att slutföras utanför en arbets yta. Välj i det här fallet inte en projektmapp som ingår i en arbetsyta.

1. Ange följande information i meddelanderutorna:

    + **Välj ett språk för ditt funktionsprojekt**: Välj `C#`.

    + **Välj en .net-körnings miljö**: Välj `.NET 5 isolated` .

    + **Välj en mall för projektets första funktion**: Välj `HTTP trigger`.

    + **Ange ett funktionsnamn**: Skriv `HttpExample`.

    + **Ange ett namnområde**: Skriv `My.Functions`.

    + **Autentiseringsnivå**: Välj `Anonymous` , vilket gör att vem som helst kan anropa funktions slut punkten. Mer information om autentiseringsnivå finns i [Authorization Keys](functions-bindings-http-webhook-trigger.md#authorization-keys).

    + **Välj hur du vill öppna projektet**: Välj `Add to workspace`.

1. Med hjälp av den här informationen genererar Visual Studio Code ett Azure Functions-projekt med en HTTP-utlösare. Du kan visa de lokala projektfilerna i Utforskaren. Mer information om filer som skapas finns i [genererade projektfiler](functions-develop-vs-code.md#generated-project-files).
::: zone-end  
::: zone pivot="development-environment-cli,development-environment-vs"  

1. Kör `func init` kommandot enligt följande för att skapa ett Functions-projekt i en mapp med namnet *LocalFunctionProj*:  

    ```console
    func init LocalFunctionProj --worker-runtime dotnetisolated
    ```
    
    Om du anger `dotnetisolated` skapas ett projekt som körs på .net 5,0.


1. Navigera till projektmappen:

    ```console
    cd LocalFunctionProj
    ```

    Den här mappen innehåller olika filer för projektet, inklusive [local.settings.jspå](functions-run-local.md#local-settings-file) och [host.js](functions-host-json.md) filer för konfigurationer. Eftersom *local.settings.jspå* kan innehålla hemligheter som hämtats från Azure, undantas filen från käll kontroll som standard i *. gitignore* -filen.

1. Lägg till en funktion i projektet med hjälp av följande kommando, där `--name` argumentet är det unika namnet för din funktion (HttpExample) och `--template` argumentet anger funktionens utlösare (http).

    ```console
    func new --name HttpExample --template "HTTP trigger" --authlevel "anonymous"
    ``` 

    `func new` skapar en HttpExample. cs-kod fil.
::: zone-end  

::: zone pivot="development-environment-vscode"  

[!INCLUDE [functions-run-function-test-local-vs-code](../../includes/functions-run-function-test-local-vs-code.md)]

::: zone-end  
::: zone pivot="development-environment-cli" 

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

::: zone-end

::: zone pivot="development-environment-vs"

## <a name="run-the-function-locally"></a>Köra funktionen lokalt

Nu kan du köra `func start` kommandot från roten i projektmappen för att kompilera och köra det isolerade C#-funktions projektet. Om du för närvarande vill felsöka den färdiga funktions koden i Visual Studio måste du manuellt koppla en fel sökare till körnings processen för kör funktioner med hjälp av följande steg:  

1. Öppna projekt filen (. CSPROJ) i Visual Studio. Du kan granska och ändra projekt koden och ange önskade Bryt punkter i koden. 

1. Från mappen rotmapp använder du följande kommando från terminalen eller en kommando tolk för att starta kör tids värden:

    ```console
    func start --dotnet-isolated-debug
    ```

    `--dotnet-isolated-debug`Alternativet instruerar processen att vänta på att ett fel söknings program ska bifogas innan du fortsätter. I slutet av utdata bör du se något som liknar följande rader: 
    
    <pre>
    ...
    
    Functions:

        HttpExample: [GET,POST] http://localhost:7071/api/HttpExample

    For detailed output, run func with --verbose flag.
    [2021-03-09T08:41:41.904Z] Azure Functions .NET Worker (PID: 81720) initialized in debug mode. Waiting for debugger to attach...
    ...
    
    </pre> 

    `PID: XXXXXX`Anger process-ID (PID) för den dotnet.exe processen som är värd för körnings funktionen.
 
1. I Azure Functions runtime-utdata noterar du process-ID: t för den värd process som du kopplar en fel sökare till. Observera även URL: en för din lokala funktion.

1. Från **fel söknings** menyn i Visual Studio väljer du **Anslut till process...**, leta upp den process som matchar process-ID och välj **bifoga**. 
    
    :::image type="content" source="media/dotnet-isolated-process-developer-howtos/attach-to-process.png" alt-text="Koppla fel söknings programmet till funktionens värd process":::    

    Med fel sökaren ansluten kan du felsöka funktions koden som normalt.

1. I adress fältet i webbläsaren skriver du din lokala funktions webb adress, som ser ut ungefär så här och kör begäran. 

    <http://localhost:7071/api/HttpExample>

    Du bör se spåra utdata från begäran som skrivits till den aktiva terminalen. Kod körningen stoppas vid eventuella Bryt punkter som du anger i funktions koden.

1. När du är klar går du till terminalen och trycker på CTRL + C för att stoppa värd processen.
 
När du har kontrollerat att funktionen körs korrekt på den lokala datorn är det dags att publicera projektet på Azure.

> [!NOTE]  
> Visual Studio Publishing är för närvarande inte tillgängligt för program med .NET-isolerade processer. När du har skapat projektet i Visual Studio måste du använda Azure CLI för att skapa fjärran sluten Azure-resurser. Sedan kan du använda Azure Functions Core Tools från kommando raden för att publicera projektet till Azure.
::: zone-end

::: zone pivot="development-environment-cli,development-environment-vs" 
## <a name="create-supporting-azure-resources-for-your-function"></a>Skapa stöd för Azure-resurser för din funktion

Innan du kan distribuera funktions koden till Azure måste du skapa tre resurser:

- En [resurs grupp](../azure-resource-manager/management/overview.md), som är en logisk behållare för relaterade resurser.
- Ett [lagrings konto](../storage/common/storage-account-create.md)som används för att underhålla tillstånd och annan information om dina funktioner.
- En Function-app som tillhandahåller miljön för att köra funktions koden. En Function-app mappar till ditt lokala Function-projekt och gör att du kan gruppera funktioner som en logisk enhet för enklare hantering, distribution och delning av resurser.

Använd följande kommandon för att skapa dessa objekt.

1. Om du inte redan har gjort det loggar du in på Azure:

    ```azurecli
    az login
    ```

    Kommandot [AZ login](/cli/azure/reference-index#az_login) loggar in dig på ditt Azure-konto.

1. Skapa en resurs grupp med namnet `AzureFunctionsQuickstart-rg` i `westeurope` regionen:

    ```azurecli
    az group create --name AzureFunctionsQuickstart-rg --location westeurope
    ```
 
    Kommandot [AZ Group Create](/cli/azure/group#az_group_create) skapar en resurs grupp. Du skapar vanligt vis resurs gruppen och resurserna i en region nära dig, med hjälp av en tillgänglig region som returneras från `az account list-locations` kommandot.

1. Skapa ett allmänt lagrings konto i din resurs grupp och region:

    ```azurecli
    az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
    ```

    Kommandot [AZ Storage Account Create](/cli/azure/storage/account#az_storage_account_create) skapar lagrings kontot. 

    I föregående exempel ersätter `<STORAGE_NAME>` du med ett namn som passar dig och är unikt i Azure Storage. Namn måste innehålla mellan 3 och 24 tecken och endast små bokstäver. `Standard_LRS` anger ett allmänt-syfte-konto, som [stöds av Functions](storage-considerations.md#storage-account-requirements).

1. Skapa Function-appen i Azure:
   
    ```azurecli
    az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet-isolated --runtime-version 5.0 --functions-version 3 --name <APP_NAME> --storage-account <STORAGE_NAME>
    ```

    Kommandot [AZ functionapp Create](/cli/azure/functionapp#az_functionapp_create) skapar Function-appen i Azure. 
    
    I föregående exempel ersätter `<STORAGE_NAME>` du med namnet på det konto som du använde i föregående steg och ersätter `<APP_NAME>` med ett globalt unikt namn som passar dig. `<APP_NAME>` är även DNS-standarddomänen för funktionsappen. 
    
    Det här kommandot skapar en Function-app som kör .NET 5,0 under [Azure Functions förbruknings planen](consumption-plan.md). Den här planen bör vara kostnads fri för den mängd användning som du ådrar dig i den här artikeln. Kommandot etablerar även en associerad Azure Application insikts instans i samma resurs grupp. Använd den här instansen för att övervaka din Function-app och Visa loggar. Mer information finns i [övervaka Azure Functions](functions-monitoring.md). Instansen debiteras inga kostnader förrän du aktiverar den.

[!INCLUDE [functions-publish-project-cli](../../includes/functions-publish-project-cli.md)]

::: zone-end

::: zone pivot="development-environment-vscode"

[!INCLUDE [functions-sign-in-vs-code](../../includes/functions-sign-in-vs-code.md)]

## <a name="publish-the-project-to-azure"></a>Publicera projektet på Azure

I det här avsnittet ska du skapa en Function-app och relaterade resurser i din Azure-prenumeration och sedan distribuera din kod.

> [!IMPORTANT]
> Om du publicerar till en befintlig funktionsapp skrivs innehållet i den appen över i Azure.


1. Välj Azure-ikonen i aktivitets fältet och välj sedan knappen **distribuera till Function-appen** i avsnittet **Azure:** functions.

    ![Publicera projektet till Azure](../../includes/media/functions-publish-project-vscode/function-app-publish-project.png)

1. Ange följande information i meddelanderutorna:

    - **Välj mapp**: Välj en mapp från din arbets yta eller bläddra till en mapp som innehåller din Function-app. Den här frågan visas inte när du redan har en giltig Function-app öppen.

    - **Välj prenumeration**: Välj den prenumeration som ska användas. Du ser inte den här frågan när du bara har en prenumeration.

    - **Välj Funktionsapp i Azure**: Välj `- Create new Function App`. (Välj inte `Advanced` alternativet, som inte beskrivs i den här artikeln.)
      
    - **Ange ett globalt unikt namn för Function-appen**: Ange ett namn som är giltigt i en URL-sökväg. Namnet du skriver verifieras för att säkerställa att det är unikt i Azure Functions.
    
    - **Välj en körnings stack**: Välj `.NET 5 (non-LTS)` . 
    
    - **Välj en plats för nya resurser**: om du vill ha bättre prestanda väljer du en [region](https://azure.microsoft.com/regions/) nära dig. 
    
    I meddelande fältet visas statusen för enskilda resurser när de skapas i Azure.

    :::image type="content" source="../../includes/media/functions-publish-project-vscode/resource-notification.png" alt-text="Meddelande om skapande av Azure-resurs":::
    
1.  När det är slutfört skapas följande Azure-resurser i din prenumeration med hjälp av namn baserat på ditt funktions program namn:
    
    [!INCLUDE [functions-vs-code-created-resources](../../includes/functions-vs-code-created-resources.md)]

    Ett meddelande visas när funktionsappen har skapats och distributionspaketet har tillämpats. 

    [!INCLUDE [functions-vs-code-create-tip](../../includes/functions-vs-code-create-tip.md)]

4. Välj **Visa utdata** i det här meddelandet för att Visa skapande-och distributions resultaten, inklusive de Azure-resurser som du har skapat. Om du saknar meddelandet väljer du klock ikonen i det nedre högra hörnet för att se den igen.

    ![Skapa fullständig avisering](../../includes/media/functions-publish-project-vscode/function-create-notifications.png)

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
Använd följande kommando för att ta bort resurs gruppen och alla resurser som ingår för att undvika ytterligare kostnader.

```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```
::: zone-end  

::: zone pivot="development-environment-vscode"  
Använd följande steg för att ta bort Function-appen och dess relaterade resurser för att undvika kostnader.

[!INCLUDE [functions-cleanup-resources-vs-code-inner.md](../../includes/functions-cleanup-resources-vs-code-inner.md)]  
::: zone-end  
::: zone pivot="development-environment-vs"   
Använd följande steg för att ta bort Function-appen och dess relaterade resurser för att undvika kostnader.

1. I Cloud Explorer expanderar du prenumerationen > **app Services**, högerklickar på din Function-app och väljer **Öppna i portalen**. 

1. På sidan Function-app väljer du fliken **Översikt** och väljer sedan länken under **resurs grupp**.

   :::image type="content" source="media/functions-create-your-first-function-visual-studio/functions-app-delete-resource-group.png" alt-text="Välj den resurs grupp som ska tas bort från sidan funktions program":::

2. På sidan **resurs grupp** granskar du listan över resurser som ingår och kontrollerar att de är de som du vill ta bort.
 
3. Välj **Ta bort resursgrupp** och följ instruktionerna.

   Borttagningen kan ta några minuter. När du är färdig visas ett meddelande i några sekunder. Du kan även välja klockikonen längst upp på sidan för att se meddelandet.
::: zone-end

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Lär dig mer om de isolerade .NET-funktionerna](dotnet-isolated-process-guide.md)

