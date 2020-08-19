---
title: Skapa en funktion i Azure som svarar på HTTP-begäranden
description: Lär dig hur du skapar en funktion från kommando raden och sedan publicerar det lokala projektet till Server lös värd i Azure Functions.
ms.date: 03/30/2020
ms.topic: quickstart
ms.custom: devx-track-csharp, devx-track-python, devx-track-azurecli
zone_pivot_groups: programming-languages-set-functions
ms.openlocfilehash: d63ae61dbaf969c021b0f1744e716068de88e4f8
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/18/2020
ms.locfileid: "88547383"
---
# <a name="quickstart-create-a-function-in-azure-that-responds-to-http-requests"></a>Snabb start: skapa en funktion i Azure som svarar på HTTP-begäranden

::: zone pivot="programming-language-csharp"  
I den här artikeln använder du kommando rads verktyg för att skapa en C#-baserad funktion i C#-klassen som svarar på HTTP-begäranden. När du har testat koden lokalt distribuerar du den till den serverbaserade miljön för Azure Functions. 
::: zone-end  
::: zone pivot="programming-language-javascript"
I den här artikeln använder du kommando rads verktyg för att skapa en JavaScript-funktion som svarar på HTTP-begäranden. När du har testat koden lokalt distribuerar du den till den serverbaserade miljön för Azure Functions. 
::: zone-end
::: zone pivot="programming-language-typescript"
I den här artikeln använder du kommando rads verktyg för att skapa en TypeScript-funktion som svarar på HTTP-begäranden. När du har testat koden lokalt distribuerar du den till den serverbaserade miljön för Azure Functions. 
::: zone-end   
::: zone pivot="programming-language-powershell"
I den här artikeln använder du kommando rads verktyg för att skapa en PowerShell-funktion som svarar på HTTP-begäranden. När du har testat koden lokalt distribuerar du den till den serverbaserade miljön för Azure Functions. 
::: zone-end  
::: zone pivot="programming-language-python" 
I den här artikeln använder du kommando rads verktyg för att skapa en python-funktion som svarar på HTTP-begäranden. När du har testat koden lokalt distribuerar du den till den serverbaserade miljön för Azure Functions. 
::: zone-end  
::: zone pivot="programming-language-java" 
I den här artikeln använder du kommando rads verktyg för att skapa en Java-funktion som svarar på HTTP-begäranden. När du har testat koden lokalt distribuerar du den till den serverbaserade miljön för Azure Functions. 
::: zone-end

Att slutföra den här snabb starten innebär en låg kostnad av några USD cent eller mindre i ditt Azure-konto.

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Det finns också en [Visual Studio Code-baserad version](functions-create-first-function-vs-code.md) av den här artikeln.
::: zone-end  
::: zone pivot="programming-language-java"  
> [!NOTE]
> Om maven inte är ditt fördelaktiga utvecklingsverktyg kan du ta en titt på våra liknande själv studie kurser för Java-utvecklare som använder [Gradle](./functions-create-first-java-gradle.md), [IntelliJ-idén](/azure/developer/java/toolkit-for-intellij/quickstart-functions) och [Visual Studio Code](./functions-create-first-function-vs-code.md?pivots=programming-language-java).
::: zone-end  

[!INCLUDE [functions-requirements-cli](../../includes/functions-requirements-cli.md)]

[!INCLUDE [functions-cli-verify-prereqs](../../includes/functions-cli-verify-prereqs.md)]

[!INCLUDE [functions-cli-create-venv](../../includes/functions-cli-create-venv.md)]

## <a name="create-a-local-function-project"></a>Skapa ett lokalt funktions projekt

I Azure Functions är ett funktions projekt en behållare för en eller flera enskilda funktioner som varje svarar på en viss utlösare. Alla funktioner i ett projekt delar samma lokala och värdbaserade konfigurationer. I det här avsnittet skapar du ett funktions projekt som innehåller en enda funktion.

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
Kör `func init` kommandot enligt följande för att skapa ett Functions-projekt i en mapp med namnet *LocalFunctionProj* med den angivna körnings miljön:  
::: zone-end  
::: zone pivot="programming-language-python"  
```
func init LocalFunctionProj --python
```
::: zone-end  
::: zone pivot="programming-language-csharp"  
```
func init LocalFunctionProj --dotnet
```
::: zone-end  
::: zone pivot="programming-language-javascript"  
```
func init LocalFunctionProj --javascript
```
::: zone-end  
::: zone pivot="programming-language-typescript"  
```
func init LocalFunctionProj --typescript
```
::: zone-end  
::: zone pivot="programming-language-powershell"  
```
func init LocalFunctionProj --powershell
```
::: zone-end    
::: zone pivot="programming-language-java"  
Kör följande kommando i en tom mapp för att skapa ett funktionsprojekt utifrån en [Maven-arketyp](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html). Använd `-DjavaVersion=11` om du vill distribuera en Java 11-funktion. Mer information finns i [Java-versioner](functions-reference-java.md#java-versions). 

# <a name="bash"></a>[bash](#tab/bash)
```bash
mvn archetype:generate -DarchetypeGroupId=com.microsoft.azure -DarchetypeArtifactId=azure-functions-archetype -DjavaVersion=8
```
# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8" 
```
# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate "-DarchetypeGroupId=com.microsoft.azure" "-DarchetypeArtifactId=azure-functions-archetype" "-DjavaVersion=8"
```
---

Maven ber dig om värden som behövs för att slutföra genereringen av projektet vid distribution.   
Ange följande värden när du uppmanas till det:

| Prompt | Värde | Beskrivning |
| ------ | ----- | ----------- |
| **groupId** | `com.fabrikam` | Ett värde som unikt identifierar ditt projekt i alla projekt, efter [paket namngivnings regler](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) för Java. |
| **artifactId** | `fabrikam-functions` | Ett värde som är jar-namn, utan versions nummer. |
| **2.0.1** | `1.0-SNAPSHOT` | Välj standardvärdet. |
| **paketfilerna** | `com.fabrikam` | Ett värde som är Java-paketet för den genererade funktions koden. Använd standardvärdet. |

Skriv `Y` eller tryck på RETUR för att bekräfta.

Maven skapar projektfilerna i en ny mapp med namnet _artifactId_, som i det här exemplet är `fabrikam-functions` . 

::: zone-end  
Navigera till projektmappen:

::: zone pivot="programming-language-csharp,programming-language-javascript,programming-language-typescript,programming-language-powershell,programming-language-python"  
```
cd LocalFunctionProj
```
::: zone-end  
::: zone pivot="programming-language-java"  
```
cd fabrikam-functions
```
::: zone-end  
Den här mappen innehåller olika filer för projektet, inklusive konfigurationsfiler som heter [local.settings.jspå](functions-run-local.md#local-settings-file) och [host.js](functions-host-json.md). Eftersom *local.settings.jspå* kan innehålla hemligheter som hämtats från Azure, undantas filen från käll kontroll som standard i *. gitignore* -filen.

[!INCLUDE [functions-cli-add-function](../../includes/functions-cli-add-function.md)]

### <a name="optional-examine-the-file-contents"></a>Valfritt Granska fil innehållet

Om du vill kan du hoppa över att [köra funktionen lokalt](#run-the-function-locally) och undersöka fil innehållet senare.

::: zone pivot="programming-language-csharp"
#### <a name="httpexamplecs"></a>HttpExample.cs

*HttpExample.cs* innehåller en `Run` metod som tar emot begär ande data i `req` variabeln är en [HttpRequest](/dotnet/api/microsoft.aspnetcore.http.httprequest) som är dekorerad med **HttpTriggerAttribute**, vilket definierar utlösnings beteendet. 

:::code language="csharp" source="~/functions-docs-csharp/http-trigger-template/HttpExample.cs":::

Returvärdet är en [ActionResult](/dotnet/api/microsoft.aspnetcore.mvc.actionresult) som returnerar ett svarsmeddelande som antingen en [OkObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.okobjectresult) (200) eller en [BadRequestObjectResult](/dotnet/api/microsoft.aspnetcore.mvc.badrequestobjectresult) (400). Läs mer i [Azure Functions HTTP-utlösare och bindningar](./functions-bindings-http-webhook.md?tabs=csharp).
::: zone-end

::: zone pivot="programming-language-java"
#### <a name="functionjava"></a>Function. java
*Function. java* innehåller en `run` metod som tar emot begär ande data i `request` variabeln är en [HttpRequestMessage](/java/api/com.microsoft.azure.functions.httprequestmessage) som är dekorerad med [HttpTrigger](/java/api/com.microsoft.azure.functions.annotation.httptrigger) -anteckningen, som definierar utlösnings beteendet. 

:::code language="java" source="~/azure-functions-samples-java/src/main/java/com/functions/Function.java":::

Svarsmeddelandet genereras av [HttpResponseMessage. Builder](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder) -API: et.

#### <a name="pomxml"></a>pom.xml

Inställningarna för de Azure-resurser som skapats som **värd för din** app definieras i konfigurations elementet i plugin-programmet med ett **konfigurations** exempel `com.microsoft.azure` i den genererade pom.xmls filen. Konfigurations elementet nedan instruerar till exempel en maven-baserad distribution för att skapa en Function-app i `java-functions-group` resurs gruppen i `westus` regionen. Själva funktions programmet körs i Windows som finns i `java-functions-app-service-plan` planen, vilket som standard är en server lös förbruknings plan.    

:::code language="java" source="~/azure-functions-samples-java/pom.xml" range="62-102":::

Du kan ändra de här inställningarna för att styra hur resurser skapas i Azure, till exempel genom `runtime.os` att ändra från `windows` till `linux` före den första distributionen. En fullständig lista över inställningar som stöds av maven-plugin-programmet finns i [konfigurations informationen](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details).

Om du vill köra din Function-app i Java 11 i stället för Java 8 måste du manuellt uppdatera pom.xml-filen med Java 11-värden. Mer information finns i [Java-versioner](functions-reference-java.md#java-versions). När du kör på Java 11 ser du till att  

#### <a name="functiontestjava"></a>FunctionTest. java

Archetype genererar också ett enhets test för din funktion. När du ändrar din funktion för att lägga till bindningar eller lägger till nya funktioner i projektet, måste du också ändra testerna i *FunctionTest. java* -filen.
::: zone-end  
::: zone pivot="programming-language-python"
#### <a name="__init__py"></a>\_\_init \_ \_ . py

* \_ \_ init \_ \_ . py* innehåller en `main()` python-funktion som utlöses enligt konfigurationen i *function.jspå*.

:::code language="python" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/__init__.py":::

För en HTTP-utlösare tar funktionen emot begär ande data i variabeln `req` enligt definitionen i *function.jspå*. `req` är en instans av [klassen Azure. functions. HttpRequest](/python/api/azure-functions/azure.functions.httprequest). Returvärdet, som definieras som `$return` i *function.jspå*, är en instans av [klassen Azure. functions. HttpResponse](/python/api/azure-functions/azure.functions.httpresponse). Läs mer i [Azure Functions HTTP-utlösare och bindningar](./functions-bindings-http-webhook.md?tabs=python).
::: zone-end

::: zone pivot="programming-language-javascript"
#### <a name="indexjs"></a>index.js

*index.js* exporterar en funktion som utlöses enligt konfigurationen i *function.jspå*.

:::code language="javascript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/index.js":::

För en HTTP-utlösare tar funktionen emot begär ande data i variabeln `req` enligt definitionen i *function.jspå*. Returvärdet, som definieras som `$return` i *function.jspå*, är svaret. Läs mer i [Azure Functions HTTP-utlösare och bindningar](./functions-bindings-http-webhook.md?tabs=javascript).
::: zone-end

::: zone pivot="programming-language-typescript"
#### <a name="indexts"></a>index. TS

*index. TS* exporterar en funktion som utlöses enligt konfigurationen i *function.jspå*.

:::code language="typescript" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-TypeScript/index.ts":::

För en HTTP-utlösare tar funktionen emot begär ande data i variabeln `req` av typen **HttpRequest** som definieras i *function.jspå*. Returvärdet, som definieras som `$return` i *function.jspå*, är svaret. 
::: zone-end

::: zone pivot="programming-language-powershell"
#### <a name="runps1"></a>run.ps1

*run.ps1* definierar ett funktions skript som utlöses enligt konfigurationen i *function.jspå*.

:::code language="powershell" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/run.ps1":::

För en HTTP-utlösare tar funktionen emot begär ande data som skickas till den `$Request` param som definierats i *function.js*. Returvärdet, som definieras som `Response` i *function.jspå*, skickas till `Push-OutputBinding` cmdleten som svar. 
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"
#### <a name="functionjson"></a>function.json

*function.jspå* är en konfigurations fil som definierar indata och utdata `bindings` för funktionen, inklusive utlösnings typen. 
::: zone-end

::: zone pivot="programming-language-python"
Du kan ändra `scriptFile` för att anropa en annan python-fil om du vill.

:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-Python/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-JavaScript/function.json":::
::: zone-end

::: zone pivot="programming-language-powershell"
:::code language="json" source="~/functions-quickstart-templates/Functions.Templates/Templates/HttpTrigger-PowerShell/function.json":::
::: zone-end

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell"  
Varje bindning kräver en riktning, en typ och ett unikt namn. HTTP-utlösaren har en inkommande bindning av typen [`httpTrigger`](functions-bindings-http-webhook-trigger.md) och utgående bindningen av typen [`http`](functions-bindings-http-webhook-output.md) .
::: zone-end  

[!INCLUDE [functions-run-function-test-local-cli](../../includes/functions-run-function-test-local-cli.md)]

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp"    
## <a name="create-supporting-azure-resources-for-your-function"></a>Skapa stöd för Azure-resurser för din funktion

Innan du kan distribuera funktions koden till Azure måste du skapa tre resurser:

- En resurs grupp, som är en logisk behållare för relaterade resurser.
- Ett lagrings konto som upprätthåller tillstånd och annan information om dina projekt.
- En Function-app som tillhandahåller miljön för att köra funktions koden. En Function-app mappar till ditt lokala Function-projekt och gör att du kan gruppera funktioner som en logisk enhet för enklare hantering, distribution och delning av resurser.

Använd följande Azure CLI-kommandon för att skapa dessa objekt. Varje kommando innehåller JSON-utdata när den har slutförts.

Om du inte redan har gjort det loggar du in på Azure med kommandot [AZ login](/cli/azure/reference-index#az-login) :

```azurecli
az login
```
    
Skapa en resursgrupp med kommandot [az group create](/cli/azure/group#az-group-create). I följande exempel skapas en resurs grupp med namnet `AzureFunctionsQuickstart-rg` i `westeurope` regionen. (Du skapar vanligt vis resurs gruppen och resurserna i en region nära dig, med hjälp av en tillgänglig region från `az account list-locations` kommandot.)

```azurecli
az group create --name AzureFunctionsQuickstart-rg --location westeurope
```

> [!NOTE]
> Du kan inte vara värd för Linux-och Windows-appar i samma resurs grupp. Om du har en befintlig resurs grupp med namnet `AzureFunctionsQuickstart-rg` med en Windows Function-app eller webbapp måste du använda en annan resurs grupp.
 
    
Skapa ett allmänt lagrings konto i din resurs grupp och region med hjälp av kommandot [AZ Storage Account Create](/cli/azure/storage/account#az-storage-account-create) . I följande exempel ersätter `<STORAGE_NAME>` du med ett globalt unikt namn som passar dig. Namn måste innehålla mellan 3 och 24 tecken och endast små bokstäver. `Standard_LRS` anger ett allmänt-syfte-konto, som [stöds av Functions](storage-considerations.md#storage-account-requirements).

```azurecli
az storage account create --name <STORAGE_NAME> --location westeurope --resource-group AzureFunctionsQuickstart-rg --sku Standard_LRS
```

Lagrings kontot förväntar sig bara några cent (USD) för den här snabb starten.
    
Skapa Function-appen med kommandot [AZ functionapp Create](/cli/azure/functionapp#az-functionapp-create) . I följande exempel ersätter `<STORAGE_NAME>` du med namnet på det konto som du använde i föregående steg och ersätter `<APP_NAME>` med ett globalt unikt namn som passar dig. `<APP_NAME>` är även DNS-standarddomänen för funktionsappen. 
::: zone-end  

::: zone pivot="programming-language-python"  
Om du använder python 3,8 ändrar du `--runtime-version` till `3.8` och `--functions_version` till `3` .

Om du använder python 3,6 ändrar `--runtime-version` du till `3.6` .

```azurecli
az functionapp create --resource-group AzureFunctionsQuickstart-rg --os-type Linux --consumption-plan-location westeurope --runtime python --runtime-version 3.7 --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
```
::: zone-end  

::: zone pivot="programming-language-javascript,programming-language-typescript"  
Om du använder Node.js 8 ändrar du också `--runtime-version` till `8` .


```azurecli
az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime node --runtime-version 10 --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
```
::: zone-end  

::: zone pivot="programming-language-csharp"  
```azurecli
az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime dotnet --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
```
::: zone-end  

::: zone pivot="programming-language-powershell"  
```azurecli
az functionapp create --resource-group AzureFunctionsQuickstart-rg --consumption-plan-location westeurope --runtime powershell --functions-version 2 --name <APP_NAME> --storage-account <STORAGE_NAME>
```
::: zone-end  

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp"  
Det här kommandot skapar en Function-app som körs på den angivna språk körningen under [Azure Functions förbruknings plan](functions-scale.md#consumption-plan), vilket är kostnads fritt för den användnings mängd som du ådrar dig här. Kommandot etablerar även en associerad Azure Application insikts-instans i samma resurs grupp, som du kan använda för att övervaka din Function-app och Visa loggar. Mer information finns i [övervaka Azure Functions](functions-monitoring.md). Instansen debiteras inga kostnader förrän du aktiverar den.
    
## <a name="deploy-the-function-project-to-azure"></a>Distribuera funktions projektet till Azure
::: zone-end  

::: zone pivot="programming-language-typescript"  
Innan du använder kärn verktyg för att distribuera ditt projekt till Azure skapar du en produktions klar version av JavaScript-filer från källfilerna för TypeScript.

Följande kommando förbereder ditt TypeScript-projekt för distribution:

```
npm run build:production 
```
::: zone-end  

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp"  
Med nödvändiga resurser på plats är du nu redo att distribuera ditt lokala Functions-projekt till Function-appen i Azure med hjälp av kommandot [FUNC Azure functionapp Publish](functions-run-local.md#project-file-deployment) . I följande exempel ersätter du `<APP_NAME>` med namnet på din app.

```
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

::: zone-end  
::: zone pivot="programming-language-java"  
## <a name="deploy-the-function-project-to-azure"></a>Distribuera funktions projektet till Azure

En Function-app och relaterade resurser skapas i Azure när du först distribuerar ditt Functions-projekt. Inställningarna för de Azure-resurser som skapats som värd för din app definieras i [pom.xml-filen](#pomxml). I den här artikeln godkänner du standardvärdena.

> [!TIP]
> Om du vill skapa en Function-app som körs på Linux i stället för Windows, ändrar du `runtime.os` elementet i pom.xml-filen från `windows` till `linux` . Att köra Linux i en förbruknings plan stöds i [dessa regioner](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions). Du kan inte ha appar som körs på Linux och appar som körs i Windows i samma resurs grupp.

Innan du kan distribuera använder du kommandot [AZ login](/cli/azure/authenticate-azure-cli) Azure CLI för att logga in på din Azure-prenumeration. 

```azurecli
az login
```

Använd följande kommando för att distribuera projektet till en ny function-app. 

```
mvn azure-functions:deploy
```

Detta skapar följande resurser i Azure:

+ Resurs grupp. Namngiven som _Java-Functions-grupp_.
+ Lagrings konto. Krävs av functions. Namnet genereras slumpmässigt baserat på lagrings kontots namn krav.
+ Värd plan. Server lös värd för din Function-app i regionen _väst_ . Namnet är _Java-Functions-App-Service-plan_.
+ Function-app. En Function-app är distributions-och körnings enheten för dina funktioner. Namnet genereras slumpmässigt baserat på din _artifactId_och läggs till med ett slumpmässigt genererat nummer. 

-Distributionen paketerar projektfilerna och distribuerar dem till den nya Function-appen med hjälp av [zip-distribution](functions-deployment-technologies.md#zip-deploy). Koden körs från distributions paketet i Azure.
::: zone-end

## <a name="invoke-the-function-on-azure"></a>Anropa funktionen på Azure

Eftersom din funktion använder en HTTP-utlösare anropar du den genom att göra en HTTP-begäran till dess URL i webbläsaren eller med ett verktyg som vändning. I båda instanserna `code` är URL-parametern din unika [funktions nyckel](functions-bindings-http-webhook-trigger.md#authorization-keys) som godkänner anropet av funktions slut punkten.

# <a name="browser"></a>[Webbläsare](#tab/browser)

Kopiera den fullständiga **anrops-URL: en** som visas i utdata från kommandot Publicera till ett webbläsarens Adress fält, som lägger till Frågeparametern `&name=Functions` . Webbläsaren bör visa liknande utdata som när du körde funktionen lokalt.

![Resultatet av funktionen körs på Azure i en webbläsare](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-browser.png)


# <a name="curl"></a>[klammerparentes](#tab/curl)

Kör [`curl`](https://curl.haxx.se/) med **anrops-URL: en**och Lägg till parametern `&name=Functions` . Kommandots utdata ska vara texten, "Hello Functions".

![Resultatet av funktionen körs på Azure med hjälp av sväng](./media/functions-create-first-azure-function-azure-cli/function-test-cloud-curl.png)

---

> [!TIP]
> Om du vill visa nära real tids loggar för en publicerad Function-app använder du [Application Insights Live Metrics Stream](functions-monitoring.md#streaming-logs).
>
> Kör följande kommando för att öppna Live Metrics-dataströmmen i en webbläsare.
>   ```
>   func azure functionapp logstream <APP_NAME> --browser
>   ```

## <a name="clean-up-resources"></a>Rensa resurser

Om du fortsätter till nästa steg lägger du [till en Azure Storage utgående bindning för kö](functions-add-output-binding-storage-queue-cli.md), behåller alla resurser på samma sätt som du skapar på det du redan har gjort.

Annars kan du använda följande kommando för att ta bort resurs gruppen och alla resurser som finns i den för att undvika ytterligare kostnader.

::: zone pivot="programming-language-javascript,programming-language-typescript,programming-language-python,programming-language-powershell,programming-language-csharp" 
```azurecli
az group delete --name AzureFunctionsQuickstart-rg
```
::: zone-end
::: zone pivot="programming-language-java"
```azurecli
az group delete --name java-functions-group
```
::: zone-end
::: zone pivot="programming-language-python"
Kör om du vill avsluta den virtuella miljön `deactivate` .
::: zone-end

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Ansluta till en Azure Storage-kö](functions-add-output-binding-storage-queue-cli.md)
 
