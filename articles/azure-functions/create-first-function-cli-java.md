---
title: Skapa en Java-funktion från kommandoraden – Azure Functions
description: Lär dig hur du skapar en Java-funktion från kommandoraden och sedan publicerar det lokala projektet till serverlös värd i Azure Functions.
ms.date: 11/03/2020
ms.topic: quickstart
ms.custom:
- devx-track-java
- devx-track-azurecli
adobe-target: true
adobe-target-activity: DocsExp–386541–A/B–Enhanced-Readability-Quickstarts–2.19.2021
adobe-target-experience: Experience B
adobe-target-content: ./create-first-function-cli-java-uiex
ms.openlocfilehash: 1a1571e83e33ee0aec40df4cf68b3d01b6ef5f04
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107787511"
---
# <a name="quickstart-create-a-java-function-in-azure-from-the-command-line"></a>Snabbstart: Skapa en Java-funktion i Azure från kommandoraden

[!INCLUDE [functions-language-selector-quickstart-cli](../../includes/functions-language-selector-quickstart-cli.md)]

I den här artikeln använder du kommandoradsverktyg för att skapa en Java-funktion som svarar på HTTP-begäranden. När du har testat koden lokalt distribuerar du den till den serverlösa Azure Functions.

Den här snabbstarten medför en liten kostnad på några cent eller mindre för ditt Azure-konto.

> [!NOTE]
> Om Maven inte är det utvecklingsverktyg du föredrar kan du kolla in våra liknande självstudier för Java-utvecklare som använder [Gradle](./functions-create-first-java-gradle.md), [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions) [och Visual Studio Code](create-first-function-vs-code-java.md).

## <a name="configure-your-local-environment"></a>Konfigurera din lokala miljö

Innan du börjar måste du ha följande:

+ Ett Azure-konto med en aktiv prenumeration. [Skapa ett konto utan kostnad.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

+ Den [Azure Functions Core Tools](functions-run-local.md#v2) version 3.x..

+ [Azure CLI](/cli/azure/install-azure-cli) version 2.4 eller senare.

+ [Java Developer Kit,](/azure/developer/java/fundamentals/java-jdk-long-term-support)version 8 eller 11. `JAVA_HOME`Miljövariabeln måste anges till installationsplatsen för rätt version av JDK.     

+ [Apache Maven](https://maven.apache.org), version 3.0 eller senare.

### <a name="prerequisite-check"></a>Kravkontroll

+ I en terminal eller ett kommandofönster kör `func --version` du för att kontrollera Azure Functions Core Tools version 3.x.

+ Kör `az --version` för att kontrollera att Azure CLI-versionen är 2.4 eller senare.

+ Kör `az login` för att logga in på Azure och verifiera en aktiv prenumeration.

## <a name="create-a-local-function-project"></a>Skapa ett lokalt funktionsprojekt

I Azure Functions är ett funktionsprojekt en container för en eller flera enskilda funktioner som var och en svarar på en specifik utlösare. Alla funktioner i ett projekt delar samma lokala konfigurationer och värdkonfigurationer. I det här avsnittet skapar du ett funktionsprojekt som innehåller en enda funktion.

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
    > + Använd `-DjavaVersion=11` om du vill att funktionerna ska köras på Java 11. Mer information finns i [Java-versioner.](functions-reference-java.md#java-versions) 
    > + `JAVA_HOME`Miljövariabeln måste anges till installationsplatsen för rätt version av JDK för att slutföra den här artikeln.

1. Maven ber dig om de värden som behövs för att slutföra genereringen av projektet vid distributionen.   
    Ange följande värden när du uppmanas att göra det:

    | Prompt | Värde | Beskrivning |
    | ------ | ----- | ----------- |
    | **groupId** | `com.fabrikam` | Ett värde som unikt identifierar projektet i alla projekt genom att följa [paketnamnreglerna för](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7) Java. |
    | **artifactId** | `fabrikam-functions` | Ett värde som är namnet på jar-et, utan versionsnumret. |
    | **Version** | `1.0-SNAPSHOT` | Välj standardvärdet. |
    | **Paket** | `com.fabrikam` | Ett värde som är Java-paketet för den genererade funktionskoden. Använd standardvärdet. |

1. Skriv `Y` eller tryck på Retur för att bekräfta.

    Maven skapar projektfilerna i en ny mapp med namnet _artifactId_, som i det här exemplet är `fabrikam-functions` . 

1. Navigera till projektmappen:

    ```console
    cd fabrikam-functions
    ```

    Den här mappen innehåller olika filer för projektet, inklusive konfigurationsfiler med [local.settings.jspå](functions-run-local.md#local-settings-file) och [host.jspå](functions-host-json.md). Eftersom *local.settings.jspå* kan innehålla hemligheter som laddats ned från Azure undantas filen från källkontrollen som standard i *.gitignore-filen.*

### <a name="optional-examine-the-file-contents"></a>(Valfritt) Granska filinnehållet

Om du vill kan du gå [vidare till Kör funktionen lokalt](#run-the-function-locally) och undersöka filinnehållet senare.

#### <a name="functionjava"></a>Function.java
*Function.java* innehåller en metod som tar emot begärandedata i variabeln är ett HttpRequestMessage som är utsmyckat med `run` `request` [HttpTrigger-anteckningen,](/java/api/com.microsoft.azure.functions.annotation.httptrigger) som definierar utlösarbeteendet. [](/java/api/com.microsoft.azure.functions.httprequestmessage) 

:::code language="java" source="~/azure-functions-samples-java/src/main/java/com/functions/Function.java":::

Svarsmeddelandet genereras av [API:et HttpResponseMessage.Builder.](/java/api/com.microsoft.azure.functions.httpresponsemessage.builder)

#### <a name="pomxml"></a>pom.xml

Inställningar för de Azure-resurser som skapats  som värd för din app definieras i konfigurationselementet för plugin-programmet med **ett groupId** för i den `com.microsoft.azure` genererade pom.xml filen. Konfigurationselementet nedan instruerar till exempel en Maven-baserad distribution att skapa en funktionsapp `java-functions-group` i resursgruppen i `westus` regionen. Själva funktionsappen körs på Windows som finns i `java-functions-app-service-plan` planen, vilket som standard är en serverlös förbrukningsplan.

:::code language="java" source="~/azure-functions-samples-java/pom.xml" range="62-107":::

Du kan ändra de här inställningarna för att styra hur resurser skapas i Azure, till exempel genom att ändra `runtime.os` från till före den första `windows` `linux` distributionen. En fullständig lista över inställningar som stöds av Maven-plugin-programmet finns i [konfigurationsinformationen](https://github.com/microsoft/azure-maven-plugins/wiki/Azure-Functions:-Configuration-Details).

#### <a name="functiontestjava"></a>FunctionTest.java

Arketypen genererar också ett enhetstest för din funktion. När du ändrar funktionen för att lägga till bindningar eller lägger till nya funktioner i projektet måste du också ändra testerna i *filen FunctionTest.java.*

## <a name="run-the-function-locally"></a>Köra funktionen lokalt

1. Kör funktionen genom att starta den lokala Azure Functions runtime-värden från mappen *LocalFunctionProj:*

    ```console
    mvn clean package
    mvn azure-functions:run
    ```
    
    Mot slutet av utdata bör följande rader visas:
    
    <pre>
    ...
    
    Now listening on: http://0.0.0.0:7071
    Application started. Press Ctrl+C to shut down.
    
    Http Functions:
    
            HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
    ...
    
    </pre>
    
    > [!NOTE]  
    > Om HttpExample inte visas som det visas ovan startade du förmodligen värden utanför projektets rotmapp. I så fall använder du **Ctrl** C för att stoppa värden, navigerar till projektets +  rotmapp och kör föregående kommando igen.

1. Kopiera URL:en för funktionen från dessa utdata till en webbläsare och lägg till `HttpExample` frågesträngen `?name=<YOUR_NAME>` , vilket gör den fullständiga URL:en som `http://localhost:7071/api/HttpExample?name=Functions` . Webbläsaren bör visa ett meddelande som returnerar frågesträngens värde. Terminalen där du startade projektet visar även loggutdata när du gör begäranden.

1. När du är klar använder du **Ctrl** + **C** och väljer `y` att stoppa functions-värden.

## <a name="deploy-the-function-project-to-azure"></a>Distribuera funktionsprojektet till Azure

En funktionsapp och relaterade resurser skapas i Azure när du först distribuerar functions-projektet. Inställningar för de Azure-resurser som skapats som värd för din app definieras i [pom.xml filen](#pomxml). I den här artikeln accepterar du standardvärdena.

> [!TIP]
> Om du vill skapa en funktionsapp som körs i Linux i stället för Windows ändrar du `runtime.os` elementet i pom.xml från till `windows` `linux` . Det finns stöd för att köra Linux i en förbrukningsplan i [dessa regioner.](https://github.com/Azure/azure-functions-host/wiki/Linux-Consumption-Regions) Du kan inte ha appar som körs i Linux och appar som körs i Windows i samma resursgrupp.

1. Innan du kan distribuera loggar du in på din Azure-prenumeration med antingen Azure CLI eller Azure PowerShell. 

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli
    az login
    ```

    Kommandot [az login](/cli/azure/reference-index#az_login) loggar in dig på ditt Azure-konto.

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
    
    Detta skapar följande resurser i Azure:
    
    + Resursgrupp. Namnges _som java-functions-group_.
    + Lagringskonto. Krävs av Functions. Namnet genereras slumpmässigt baserat på krav på lagringskontonamn.
    + Värdplan. Serverlös värdtjänst för funktionsappen i _regionen USA,_ västra. Namnet är _java-functions-app-service-plan_.
    + Funktionsapp. En funktionsapp är distributions- och körningsenheten för dina funktioner. Namnet genereras slumpmässigt baserat på ditt _artifactId_, och läggs till med ett slumpmässigt genererat tal.
    
    Distributionen paketerar projektfilerna och distribuerar dem till den nya funktionsappen med [zip-distributionen](functions-deployment-technologies.md#zip-deploy). Koden körs från distributionspaketet i Azure.

[!INCLUDE [functions-run-remote-azure-cli](../../includes/functions-run-remote-azure-cli.md)]

[!INCLUDE [functions-streaming-logs-cli-qs](../../includes/functions-streaming-logs-cli-qs.md)]

## <a name="clean-up-resources"></a>Rensa resurser

Om du fortsätter till [nästa](#next-steps) steg och lägger till en Azure Storage-köutdatabindning behåller du alla dina resurser på plats eftersom du bygger vidare på det du redan har gjort.

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

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Ansluta till en Azure Storage kö](functions-add-output-binding-storage-queue-cli.md?pivots=programming-language-java)
