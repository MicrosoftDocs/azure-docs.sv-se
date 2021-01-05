---
title: Skapa en Java-funktion från kommando raden – Azure Functions
description: Lär dig hur du skapar en Java-funktion från kommando raden och sedan publicerar det lokala projektet till Server lös värd i Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-java
- devx-track-azurecli
ms.openlocfilehash: 504d3a834760605149c93c0beea84bbe1b59e263
ms.sourcegitcommit: 17e9cb8d05edaac9addcd6e0f2c230f71573422c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2020
ms.locfileid: "97707562"
---
# <a name="quickstart-create-a-java-function-in-azure-from-the-command-line"></a>Snabb start: skapa en Java-funktion i Azure från kommando raden

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

I den här artikeln använder du kommando rads verktyg för att skapa en Java-funktion som svarar på HTTP-begäranden. När du har testat koden lokalt distribuerar du den till den serverbaserade miljön för Azure Functions.

Att slutföra den här snabb starten innebär en låg kostnad av några USD cent eller mindre i ditt Azure-konto.

> [!NOTE]
> Om maven inte är det föredragna utvecklings verktyget kan du kolla in våra liknande själv studie kurser för Java-utvecklare som använder [Gradle](./functions-create-first-java-gradle.md), [IntelliJ-idén](/azure/developer/java/toolkit-for-intellij/quickstart-functions) och [Visual Studio Code](create-first-function-vs-code-java.md).

## <a name="configure-your-local-environment"></a>Konfigurera din lokala miljö

Innan du börjar måste du ha följande:

+ Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto kostnads fritt](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

+ [Azure Functions Core tools](functions-run-local.md#v2) version 3. x.

+ [Azure CLI](/cli/azure/install-azure-cli) version 2,4 eller senare.

+ [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), version 8 eller 11. `JAVA_HOME`Miljövariabeln måste anges till installations platsen för rätt version av JDK.     

+ [Apache maven](https://maven.apache.org), version 3,0 eller senare.

### <a name="prerequisite-check"></a>Krav kontroll

+ I ett terminalfönster eller kommando fönster, kör `func --version` för att kontrol lera att Azure Functions Core Tools är version 3. x.

+ Kör `az --version` för att kontrol lera att Azure CLI-versionen är 2,4 eller senare.

+ Kör `az login` för att logga in på Azure och verifiera en aktiv prenumeration.

## <a name="create-a-local-function-project"></a>Skapa ett lokalt funktions projekt

I Azure Functions är ett funktions projekt en behållare för en eller flera enskilda funktioner som varje svarar på en viss utlösare. Alla funktioner i ett projekt delar samma lokala och värdbaserade konfigurationer. I det här avsnittet skapar du ett funktions projekt som innehåller en enda funktion.

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

    > [!IMPORTANT]
    > + Använd `-DjavaVersion=11` om du vill att funktionerna ska köras på Java 11. Mer information finns i [Java-versioner](functions-reference-java.md#java-versions). 
    > + `JAVA_HOME`Miljövariabeln måste anges till installations platsen för rätt version av JDK för att slutföra den här artikeln.

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

    Den här mappen innehåller olika filer för projektet, inklusive konfigurationsfiler som heter [local.settings.jspå](functions-run-local.md#local-settings-file) och [host.js](functions-host-json.md). Eftersom *local.settings.jspå* kan innehålla hemligheter som hämtats från Azure, undantas filen från käll kontroll som standard i *. gitignore* -filen.

### <a name="optional-examine-the-file-contents"></a>Valfritt Granska fil innehållet

Om du vill kan du hoppa över att [köra funktionen lokalt](#run-the-function-locally) och undersöka fil innehållet senare.

#### <a name="functionjava"></a>Function. java
*Function. java* innehåller en `run` metod som tar emot begär ande data i `request` variabeln är en [HttpRequestMessage](/java/api/com.microsoft.azure.functions.httprequestmessage) som är dekorerad med [HttpTrigger](/java/api/com.microsoft.azure.functions.annotation.httptrigger) -anteckningen, som definierar utlösnings beteendet. 

:::code language="java" source="~/azure-functions-samples-java/src/main/java/com/functions/Function.java":::

Svarsmeddelandet genereras av [HttpResponseMessage. Builder](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder) -API: et.

#### <a name="pomxml"></a>pom.xml

Inställningarna för de Azure-resurser som skapats som **värd för din** app definieras i konfigurations elementet i plugin-programmet med ett **konfigurations** exempel `com.microsoft.azure` i den genererade pom.xmls filen. Konfigurations elementet nedan instruerar till exempel en maven-baserad distribution för att skapa en Function-app i `java-functions-group` resurs gruppen i `westus` regionen. Själva funktions programmet körs i Windows som finns i `java-functions-app-service-plan` planen, vilket som standard är en server lös förbruknings plan.

:::code language="java" source="~/azure-functions-samples-java/pom.xml" range="62-102":::

Du kan ändra de här inställningarna för att styra hur resurser skapas i Azure, till exempel genom `runtime.os` att ändra från `windows` till `linux` före den första distributionen. En fullständig lista över inställningar som stöds av maven-plugin-programmet finns i [konfigurations informationen](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details).

#### <a name="functiontestjava"></a>FunctionTest. java

Archetype genererar också ett enhets test för din funktion. När du ändrar din funktion för att lägga till bindningar eller lägger till nya funktioner i projektet, måste du också ändra testerna i *FunctionTest. java* -filen.

## <a name="run-the-function-locally"></a>Köra funktionen lokalt

1. Kör din funktion genom att starta den lokala Azure Functions runtime-värden från mappen *LocalFunctionProj* :

    ```console
    mvn clean package
    mvn azure-functions:run
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
    
    > [!NOTE]  
    > Om HttpExample inte visas som du ser ovan, startade du förmodligen värden från utsidan av projektets rotmapp. I så fall kan du använda **CTRL** + **C** för att stoppa värden, navigera till projektets rotmapp och köra föregående kommando igen.

1. Kopiera URL: en för din `HttpExample` funktion från utdata till en webbläsare och Lägg till frågesträngen och `?name=<YOUR_NAME>` gör den fullständiga URL: en som `http://localhost:7071/api/HttpExample?name=Functions` . Webbläsaren ska visa ett meddelande som `Hello Functions` :

    ![Resultatet av funktionen körs lokalt i webbläsaren](./media/functions-create-first-azure-function-azure-cli/function-test-local-browser.png)
    
    Terminalen där du startade projektet visar också logg data när du gör förfrågningar.

1. När du är klar använder du **CTRL** + **C** och väljer `y` att stoppa funktions värden.

## <a name="deploy-the-function-project-to-azure"></a>Distribuera funktions projektet till Azure

En Function-app och relaterade resurser skapas i Azure när du först distribuerar ditt Functions-projekt. Inställningarna för de Azure-resurser som skapats som värd för din app definieras i [pom.xml-filen](#pomxml). I den här artikeln godkänner du standardvärdena.

> [!TIP]
> Om du vill skapa en Function-app som körs på Linux i stället för Windows, ändrar du `runtime.os` elementet i pom.xml-filen från `windows` till `linux` . Att köra Linux i en förbruknings plan stöds i [dessa regioner](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions). Du kan inte ha appar som körs på Linux och appar som körs i Windows i samma resurs grupp.

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
    
    Detta skapar följande resurser i Azure:
    
    + Resurs grupp. Namngiven som _Java-Functions-grupp_.
    + Lagrings konto. Krävs av functions. Namnet genereras slumpmässigt baserat på lagrings kontots namn krav.
    + Värd plan. Server lös värd för din Function-app i regionen _väst_ . Namnet är _Java-Functions-App-Service-plan_.
    + Function-app. En Function-app är distributions-och körnings enheten för dina funktioner. Namnet genereras slumpmässigt baserat på din _artifactId_ och läggs till med ett slumpmässigt genererat nummer.
    
    -Distributionen paketerar projektfilerna och distribuerar dem till den nya Function-appen med hjälp av [zip-distribution](functions-deployment-technologies.md#zip-deploy). Koden körs från distributions paketet i Azure.

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

## <a name="clean-up-resources"></a>Rensa resurser

Om du fortsätter till [Nästa steg](#next-steps) och lägger till en Azure Storage utgående bindning för kö, bör du se till att alla resurser är på plats när du bygger på det du redan har gjort.

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

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Ansluta till en Azure Storage-kö](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-java)
