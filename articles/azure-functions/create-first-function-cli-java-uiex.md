---
title: Skapa en Java-funktion från kommando raden – Azure Functions
description: Lär dig hur du skapar en Java-funktion från kommando raden och sedan publicerar det lokala projektet till Server lös värd i Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-java
- devx-track-azurecli
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 32d91f01fcebec3e1ae0d19b1bff29a71f41c0d1
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050434"
---
# <a name="quickstart-create-a-java-function-in-azure-from-the-command-line"></a>Snabb start: skapa en Java-funktion i Azure från kommando raden

> [!div class="op_single_selector" title1="Välj ditt funktions språk: "]
> - [Java](create-first-function-cli-java.md)
> - [Python](create-first-function-cli-python.md)
> - [C#](create-first-function-cli-csharp.md)
> - [JavaScript](create-first-function-cli-node.md)
> - [PowerShell](create-first-function-cli-powershell.md)
> - [TypeScript](create-first-function-cli-typescript.md)

Använd kommando rads verktyg för att skapa en Java-funktion som svarar på HTTP-begäranden. Testa koden lokalt och distribuera den sedan till den serverbaserade miljön för Azure Functions.

Att slutföra den här snabb starten innebär en låg kostnad av några USD cent eller mindre i din <abbr title="Den profil som hanterar fakturerings information för Azure-användning.">Azure-konto</abbr>.

Om maven inte är det föredragna utvecklings verktyget kan du kolla in våra liknande själv studie kurser för Java-utvecklare som använder [Gradle](./functions-create-first-java-gradle.md), [IntelliJ-idén](/azure/developer/java/toolkit-for-intellij/quickstart-functions) och [Visual Studio Code](create-first-function-vs-code-java.md).

## <a name="1-prepare-your-environment"></a>1. Förbered din miljö

Innan du börjar måste du ha följande:

+ Ett Azure-konto med en aktiv <abbr title="Den grundläggande organisations strukturen i vilken du hanterar resurser i Azure, som vanligt vis är kopplade till en individ eller avdelning inom en organisation.">prenumeration</abbr>. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core tools](functions-run-local.md#v2) version 3. x.

+ [Azure CLI](/cli/azure/install-azure-cli) version 2,4 eller senare.

+ [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), version 8 eller 11. `JAVA_HOME`Miljövariabeln måste anges till installations platsen för rätt version av JDK.

+ [Apache maven](https://maven.apache.org), version 3,0 eller senare.

### <a name="prerequisite-check"></a>Krav kontroll

+ I en terminal-eller kommando fönster, kör `func --version` för att kontrol lera att <abbr title="Uppsättningen kommando rads verktyg för att arbeta med Azure Functions på den lokala datorn.">Azure Functions Core Tools</abbr> är version 3. x.

+ Kör `az --version` för att kontrol lera att Azure CLI-versionen är 2,4 eller senare.

+ Kör `az login` för att logga in på Azure och verifiera en aktiv prenumeration.

<br>
<hr/>

## <a name="2-create-a-local-function-project"></a>2. skapa ett lokalt funktions projekt

I Azure Functions är ett funktions projekt en behållare för en eller flera enskilda funktioner som varje svarar på en viss <abbr title="Den typ av händelse som anropar funktionens kod, till exempel en HTTP-begäran, ett Queue-meddelande eller en angiven tid.">utlösare</abbr>. Alla funktioner i ett projekt delar samma lokala och värdbaserade konfigurationer. I det här avsnittet skapar du ett funktions projekt som innehåller en enda funktion.

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
    <summary><strong>Köra funktioner på Java 11</strong></summary>

    Använd `-DjavaVersion=11` om du vill att funktionerna ska köras på Java 11. Mer information finns i [Java-versioner](functions-reference-java.md#java-versions).
    </details>

1. Maven ber dig om värden som behövs för att slutföra genereringen av projektet vid distribution.
    Ange följande värden när du uppmanas till det:

    | Prompt | Värde | Beskrivning |
    | ------ | ----- | ----------- |
    | **groupId** | `com.fabrikam` | Ett värde som unikt identifierar ditt projekt i alla projekt, efter [paket namngivnings regler](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) för Java. |
    | **artifactId** | `fabrikam-functions` | Ett värde som är jar-namn, utan versions nummer. |
    | **2.0.1** | `1.0-SNAPSHOT` | Välj standardvärdet. |
    | **paketfilerna** | `com.fabrikam` | Ett värde som är Java-paketet för den genererade funktions koden. Använd standardvärdet. |

1. Skriv `Y` eller tryck på RETUR för att bekräfta.

    Maven skapar projektfilerna i en ny mapp med namnet _artifactId_, som i det här exemplet är `fabrikam-functions` . 

1. Navigera till projektmappen:

    ```console
    cd fabrikam-functions
    ```

<br/>
<details>
<summary><strong>Vad har skapats i mappen LocalFunctionProj?</strong></summary>

Den här mappen innehåller olika filer för projektet, till exempel *Function. java*, *FunctionTest. java* och *pom.xml*. Det finns också filer med namnet [local.settings.jspå](functions-run-local.md#local-settings-file) och [host.jspå](functions-host-json.md). Eftersom *local.settings.jspå* kan innehålla hemligheter som hämtats från Azure, undantas filen från käll kontroll som standard i *. gitignore* -filen.
</details>

<br/>
<details>
<summary><strong>Kod för function. java</strong></summary>

*Function. java* innehåller en `run` metod som tar emot begär ande data i `request` variabeln är en [HttpRequestMessage](/java/api/com.microsoft.azure.functions.httprequestmessage) som är dekorerad med [HttpTrigger](/java/api/com.microsoft.azure.functions.annotation.httptrigger) -anteckningen, som definierar utlösnings beteendet. 

:::code language="java" source="~/azure-functions-samples-java/src/main/java/com/functions/Function.java":::

Svarsmeddelandet genereras av [HttpResponseMessage. Builder](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder) -API: et.

Archetype genererar också ett enhets test för din funktion. När du ändrar din funktion för att lägga till bindningar eller lägger till nya funktioner i projektet, måste du också ändra testerna i *FunctionTest. java* -filen.
</details>

<br/>
<details>
<summary><strong>Kod för pom.xml</strong></summary>

Inställningarna för de Azure-resurser som skapats som **värd för din** app definieras i konfigurations elementet i plugin-programmet med ett **konfigurations** exempel `com.microsoft.azure` i den genererade *pom.xmls* filen. Konfigurations elementet nedan instruerar till exempel en maven-baserad distribution att skapa en Function-app i `java-functions-group` resurs gruppen i `westus` <abbr title="En geografisk referens till ett särskilt Azure-datacenter där resurser allokeras.">region</abbr>. Själva funktions programmet körs i Windows som finns i `java-functions-app-service-plan` planen, vilket som standard är en server lös förbruknings plan.

:::code language="java" source="~/azure-functions-samples-java/pom.xml" range="62-107":::

Du kan ändra de här inställningarna för att styra hur resurser skapas i Azure, till exempel genom `runtime.os` att ändra från `windows` till `linux` före den första distributionen. En fullständig lista över inställningar som stöds av maven-plugin-programmet finns i [konfigurations informationen](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details).
</details>

<br>
<hr/>

## <a name="3-run-the-function-locally"></a>3. kör funktionen lokalt

1. **Kör din funktion** genom att starta den lokala Azure Functions runtime-värden från mappen *LocalFunctionProj* :

    ```console
    mvn clean package
    mvn azure-functions:run
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

    Om HttpExample inte visas som du ser ovan, startade du förmodligen värden från utsidan av projektets rotmapp. I så fall kan du använda <kbd>CTRL + C</kbd> för att stoppa värden, navigera till projektets rotmapp och köra föregående kommando igen.

1. **Kopiera URL:** en för din `HttpExample` funktion från utdata till en webbläsare och Lägg till frågesträngen och `?name=<YOUR_NAME>` gör den fullständiga URL: en som `http://localhost:7071/api/HttpExample?name=Functions` . Webbläsaren ska visa ett meddelande som `Hello Functions` :

    ![Resultatet av funktionen körs lokalt i webbläsaren](./media/functions-create-first-azure-function-azure-cli/function-test-local-browser.png)

    Terminalen där du startade projektet visar också logg data när du gör förfrågningar.

1. När du är klar använder du <kbd>CTRL + C</kbd> och väljer <kbd>y</kbd> för att stoppa funktions värden.

<br>
<hr/>

## <a name="4-deploy-the-function-project-to-azure"></a>4. distribuera funktions projektet till Azure

En Function-app och relaterade resurser skapas i Azure när du först distribuerar ditt Functions-projekt. Inställningarna för de Azure-resurser som skapats som värd för din app definieras i *pom.xml* -filen. I den här artikeln godkänner du standardvärdena.

<br/>
<details>
<summary><strong>Så här skapar du en Function-app som körs på Linux</strong></summary>

Om du vill skapa en Function-app som körs på Linux i stället för Windows, ändrar du `runtime.os` elementet i *pom.xml* -filen från `windows` till `linux` . Att köra Linux i en förbruknings plan stöds i [dessa regioner](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions). Du kan inte ha appar som körs på Linux och appar som körs i Windows i samma resurs grupp.
</details>

1. Innan du kan distribuera loggar du in på Azure-prenumerationen med hjälp av antingen Azure CLI eller Azure PowerShell. 

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

1. Använd följande kommando för att distribuera projektet till en ny function-app.

    ```console
    mvn azure-functions:deploy
    ```

    -Distributionen paketerar projektfilerna och distribuerar dem till den nya Function-appen med hjälp av [zip-distribution](functions-deployment-technologies.md#zip-deploy). Koden körs från distributions paketet i Azure.

<br/>
<details>
<summary><strong>Vad har skapats i Azure?</strong></summary>

+ Resurs grupp. Namngiven som _Java-Functions-grupp_.
+ Lagrings konto. Krävs av functions. Namnet genereras slumpmässigt baserat på lagrings kontots namn krav.
+ Värd plan. Server lös värd för din Function-app i regionen _väst_ . Namnet är _Java-Functions-App-Service-plan_.
+ Function-app. En Function-app är distributions-och körnings enheten för dina funktioner. Namnet genereras slumpmässigt baserat på din _artifactId_ och läggs till med ett slumpmässigt genererat nummer.
</details>

<br>
<hr/>

## <a name="5-invoke-the-function-on-azure"></a>5. anropa funktionen på Azure

Eftersom din funktion använder en HTTP-utlösare **anropar du den genom att göra en HTTP-begäran till dess URL** i webbläsaren eller med ett verktyg som <abbr title="Ett kommando rads verktyg för att generera HTTP-förfrågningar till en URL. Kontakta https://curl.se/">curl</abbr>.

# <a name="browser"></a>[Webbläsare](#tab/browser)

Kopiera den fullständiga **anrops-URL: en** som visas i `publish` kommandots utdata till ett webbläsares adress fält och lägger till Frågeparametern `&name=Functions` . Webbläsaren bör visa liknande utdata som när du körde funktionen lokalt.

![Resultatet av funktionen körs på Azure i en webbläsare](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-browser.png)

# <a name="curl"></a>[curl](#tab/curl)

Kör [`curl`](https://curl.haxx.se/) med **anrops-URL: en** och Lägg till parametern `&name=Functions` . Kommandots utdata ska vara texten, "Hello Functions".

![Resultatet av funktionen körs på Azure med hjälp av sväng](../../includes/media/functions-run-remote-azure-cli/function-test-cloud-curl.png)

---

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

<br>
<hr/>

## <a name="6-clean-up-resources"></a>6. Rensa resurser

Om du fortsätter till [Nästa steg](#next-steps) och lägger till ett Azure Storage <abbr title="I Azure Storage innebär ett sätt att associera en funktion med en lagrings kö, så att den kan skapa meddelanden i kön.">utgående bindning för kö</abbr>, se till att alla resurser finns på samma sätt som du bygger på vad du redan har gjort.

Annars kan du använda följande kommando för att ta bort resurs gruppen och alla resurser som finns i den för att undvika ytterligare kostnader.

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
> [Ansluta till en Azure Storage-kö](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-java)
