---
title: Skapa en Java-funktion från kommandoraden – Azure Functions
description: Lär dig hur du skapar en Java-funktion från kommandoraden och sedan publicerar det lokala projektet till serverlös värd i Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-java
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: b5bc453e2e0371ee0412824f01d99863b12d91e2
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375380"
---
# <a name="quickstart-create-a-java-function-in-azure-from-the-command-line"></a>Snabbstart: Skapa en Java-funktion i Azure från kommandoraden

> [!div class="op_single_selector" title1="Välj funktionsspråk: "]
> - [Java](create-first-function-cli-java.md)
> - [Python](create-first-function-cli-python.md)
> - [C#](create-first-function-cli-csharp.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [PowerShell](create-first-function-cli-powershell.md)
> - [TypeScript](create-first-function-cli-typescript.md)

Använd kommandoradsverktyg för att skapa en Java-funktion som svarar på HTTP-begäranden. Testa koden lokalt och distribuera den sedan till den serverlösa miljön för Azure Functions.

När du slutför den här snabbstarten medför det en liten kostnad på några cent eller mindre i din <abbr title="Profilen som underhåller faktureringsinformation för Azure-användning.">Azure-konto</abbr>.

Om Maven inte är det utvecklingsverktyg du föredrar kan du titta på våra liknande självstudier för Java-utvecklare som använder [Gradle,](./functions-create-first-java-gradle.md) [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions) [och Visual Studio Code](create-first-function-vs-code-java.md).

## <a name="1-prepare-your-environment"></a>1. Förbered din miljö

Innan du börjar måste du ha följande:

+ Ett Azure-konto med en aktiv <abbr title="Den grundläggande organisationsstrukturen där du hanterar resurser i Azure, vanligtvis associerad med en person eller avdelning inom en organisation.">prenumeration</abbr>. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ Den [Azure Functions Core Tools](functions-run-local.md#v2) version 3.x.

+ [Azure CLI](/cli/azure/install-azure-cli) version 2.4 eller senare.

+ [Java Developer Kit,](/azure/developer/java/fundamentals/java-jdk-long-term-support)version 8 eller 11. `JAVA_HOME`Miljövariabeln måste anges till installationsplatsen för rätt version av JDK.

+ [Apache Maven](https://maven.apache.org), version 3.0 eller senare.

### <a name="prerequisite-check"></a>Kravkontroll

+ I en terminal eller ett kommandofönster kör du `func --version` för att kontrollera att <abbr title="En uppsättning kommandoradsverktyg för att arbeta med Azure Functions på din lokala dator.">Azure Functions Core Tools</abbr> är version 3.x.

+ Kör `az --version` för att kontrollera att Azure CLI-versionen är 2.4 eller senare.

+ Kör `az login` för att logga in på Azure och verifiera en aktiv prenumeration.

<br>
<hr/>

## <a name="2-create-a-local-function-project"></a>2. Skapa ett lokalt funktionsprojekt

I Azure Functions är ett funktionsprojekt en container för en eller flera enskilda funktioner som var och en svarar på en specifik <abbr title="Den typ av händelse som anropar funktionens kod, till exempel en HTTP-begäran, ett kömeddelande eller en viss tid.">Utlösa</abbr>. Alla funktioner i ett projekt delar samma lokala konfigurationer och värdkonfigurationer. I det här avsnittet skapar du ett funktionsprojekt som innehåller en enda funktion.

1. Kör följande kommando i en tom mapp för att skapa ett funktionsprojekt utifrån en [Maven-arketyp](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html). 

    # <a name="bash"></a>[Bash](#tab/bash)

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

    <br/>
    <details>
    <summary><strong>Så här kör du funktioner i Java 11</strong></summary>

    Använd `-DjavaVersion=11` om du vill att funktionerna ska köras på Java 11. Mer information finns i [Java-versioner.](functions-reference-java.md#java-versions)
    </details>

1. Maven ber dig om de värden som behövs för att slutföra genereringen av projektet vid distributionen.
    Ange följande värden när du tillfrågas:

    | Prompt | Värde | Beskrivning |
    | ------ | ----- | ----------- |
    | **groupId** | `com.fabrikam` | Ett värde som unikt identifierar projektet i alla projekt och som följer [namngivningsreglerna för](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) Java. |
    | **artifactId** | `fabrikam-functions` | Ett värde som är namnet på jar-numret, utan versionsnummer. |
    | **Version** | `1.0-SNAPSHOT` | Välj standardvärdet. |
    | **Paket** | `com.fabrikam` | Ett värde som är Java-paketet för den genererade funktionskoden. Använd standardvärdet. |

1. Skriv `Y` eller tryck på Retur för att bekräfta.

    Maven skapar projektfilerna i en ny mapp med namnet _artifactId_, som i det här exemplet är `fabrikam-functions` . 

1. Navigera till projektmappen:

    ```console
    cd fabrikam-functions
    ```

<br/>
<details>
<summary><strong>Vad skapas i mappen LocalFunctionProj?</strong></summary>

Den här mappen innehåller olika filer för projektet, till exempel *Function.java,* *FunctionTest.java* och *pom.xml*. Det finns även konfigurationsfiler med [local.settings.jspå](functions-run-local.md#local-settings-file) och [host.jspå](functions-host-json.md). Eftersom *local.settings.jspå* kan innehålla hemligheter som laddats ned från Azure undantas filen från källkontrollen som standard i *.gitignore-filen.*
</details>

<br/>
<details>
<summary><strong>Kod för Function.java</strong></summary>

*Function.java* innehåller en metod som tar emot begärandedata i variabeln är ett HttpRequestMessage som är utsmyckat med `run` `request` [HttpTrigger-anteckningen,](/java/api/com.microsoft.azure.functions.annotation.httptrigger) som definierar utlösarbeteendet. [](/java/api/com.microsoft.azure.functions.httprequestmessage) 

:::code language="java" source="~/azure-functions-samples-java/src/main/java/com/functions/Function.java":::

Svarsmeddelandet genereras av [API:et HttpResponseMessage.Builder.](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder)

Arketypen genererar också ett enhetstest för din funktion. När du ändrar funktionen för att lägga till bindningar eller lägger till nya funktioner i projektet måste du också ändra testerna i *filen FunctionTest.java.*
</details>

<br/>
<details>
<summary><strong>Kod för pom.xml</strong></summary>

Inställningar för de Azure-resurser som skapats  som värd för din app definieras i konfigurationselementet i plugin-programmet med **ett groupId** för i den `com.microsoft.azure` *genereradepom.xmlfilen.* Konfigurationselementet nedan instruerar till exempel en Maven-baserad distribution att skapa en funktionsapp i `java-functions-group` resursgruppen i `westus` <abbr title="En geografisk referens till ett specifikt Azure-datacenter där resurser allokeras.">region</abbr>. Själva funktionsappen körs på Windows som finns i `java-functions-app-service-plan` planen, vilket som standard är en serverlös förbrukningsplan.

:::code language="java" source="~/azure-functions-samples-java/pom.xml" range="62-107":::

Du kan ändra de här inställningarna för att styra hur resurser skapas i Azure, till exempel genom att ändra `runtime.os` från till före den första `windows` `linux` distributionen. En fullständig lista över inställningar som stöds av Maven-plugin-programmet finns i [konfigurationsinformationen](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details).
</details>

<br>
<hr/>

## <a name="3-run-the-function-locally"></a>3. Kör funktionen lokalt

1. **Kör funktionen genom** att starta den lokala Azure Functions runtime-värden från *mappen LocalFunctionProj:*

    ```console
    mvn clean package
    mvn azure-functions:run
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

    Om HttpExample inte visas som det visas ovan har du förmodligen startat värden utanför projektets rotmapp. I så fall använder du <kbd>Ctrl +C</kbd> för att stoppa värden, navigerar till projektets rotmapp och kör föregående kommando igen.

1. **Kopiera URL:en** för funktionen från dessa utdata till en webbläsare och `HttpExample` lägg till frågesträngen `?name=<YOUR_NAME>` , vilket gör den fullständiga URL:en som `http://localhost:7071/api/HttpExample?name=Functions` . Webbläsaren bör visa ett meddelande som `Hello Functions` :

    ![Resultatet av funktionen körs lokalt i webbläsaren](./media/functions-create-first-azure-function-azure-cli/function-test-local-browser.png)

    Terminalen där du startade projektet visar även loggutdata när du gör begäranden.

1. När du är klar använder du <kbd>Ctrl+C och</kbd> väljer <kbd>y för</kbd> att stoppa functions-värden.

<br>
<hr/>

## <a name="4-deploy-the-function-project-to-azure"></a>4. Distribuera funktionsprojektet till Azure

En funktionsapp och relaterade resurser skapas i Azure när du först distribuerar funktionsprojektet. Inställningar för De Azure-resurser som skapas som värd för din app definieras i *pom.xml* fil. I den här artikeln accepterar du standardvärdena.

<br/>
<details>
<summary><strong>Så här skapar du en funktionsapp som körs i Linux</strong></summary>

Om du vill skapa en funktionsapp som körs på Linux i stället för Windows ändrar du `runtime.os` elementet *ipom.xml* från `windows` till `linux` . Att köra Linux i en förbrukningsplan stöds i [dessa regioner.](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions) Du kan inte ha appar som körs på Linux och appar som körs i Windows i samma resursgrupp.
</details>

1. Innan du kan distribuera loggar du in på din Azure-prenumeration med antingen Azure CLI eller Azure PowerShell. 

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```

    Kommandot [az login](/cli/azure/reference-index#az-login) loggar in dig på ditt Azure-konto.

    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell) 
    ```azurepowershell
    Connect-AzAccount
    ```

    Cmdleten [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) loggar in dig på ditt Azure-konto.

    ---

1. Använd följande kommando för att distribuera projektet till en ny funktionsapp.

    ```console
    mvn azure-functions:deploy
    ```

    Distributionen paketerar projektfilerna och distribuerar dem till den nya funktionsappen med [zip-distributionen](functions-deployment-technologies.md#zip-deploy). Koden körs från distributionspaketet i Azure.

<br/>
<details>
<summary><strong>Vad skapas i Azure?</strong></summary>

+ Resursgrupp. Namnges _som java-functions-group_.
+ Lagringskonto. Krävs av Functions. Namnet genereras slumpmässigt baserat på krav på lagringskontonamn.
+ Värdplan. Serverlös värdtjänst för funktionsappen i _regionen USA,_ västra. Namnet är _java-functions-app-service-plan_.
+ Funktionsapp. En funktionsapp är distributions- och körningsenheten för dina funktioner. Namnet genereras slumpmässigt baserat på ditt _artifactId_, och läggs till med ett slumpmässigt genererat tal.
</details>

<br>
<hr/>

## <a name="5-invoke-the-function-on-azure"></a>5. Anropa funktionen i Azure

Eftersom funktionen använder en HTTP-utlösare anropar du den genom att göra en HTTP-begäran till **dess URL** i webbläsaren eller med ett verktyg som <abbr title="Ett kommandoradsverktyg för att generera HTTP-begäranden till en URL. Se https://curl.se/">curl</abbr>.

# <a name="browser"></a>[Webbläsare](#tab/browser)

Kopiera den fullständiga **Invoke URL som** visas i kommandots utdata till ett `publish` adressfält i webbläsaren och lägga till frågeparametern `&name=Functions` . Webbläsaren bör visa liknande utdata som när du körde funktionen lokalt.

![Utdata från funktionen körs på Azure i en webbläsare](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

Kör [`curl`](https://curl.haxx.se/) med Invoke URL **(Anropa URL)** och lägga till parametern `&name=Functions` . Kommandots utdata ska vara texten "Hello Functions".

![Utdata från funktionen körs på Azure med curl](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

<br>
<hr/>

## <a name="6-clean-up-resources"></a>6. Rensa resurser

Om du fortsätter till nästa [steg och lägger](#next-steps) till en Azure Storage <abbr title="I Azure Storage kan du associera en funktion med en lagringskö så att den kan skapa meddelanden i kön.">köutdatabindning</abbr>håller du alla dina resurser på plats eftersom du bygger vidare på det du redan har gjort.

Annars använder du följande kommando för att ta bort resursgruppen och alla dess inneslutna resurser för att undvika ytterligare kostnader.

 # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli
az group delete --name java-functions-group
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azure-powershell)

```azurepowershell
Remove-AzResourceGroup -Name java-functions-group
```

---

<br>
<hr/>

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Ansluta till en Azure Storage kö](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-java)
