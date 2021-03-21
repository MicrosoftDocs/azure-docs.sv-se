---
title: Skapa en Kotlin-funktion i Azure Functions med maven
description: Skapa och publicera en HTTP-utlöst funktions app för att Azure Functions med Kotlin och Maven.
author: dglover
ms.service: azure-functions
ms.topic: quickstart
ms.date: 03/25/2020
ms.author: dglover
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 721fbaf1a3b2418677d07e8ac39fa733164c1459
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102179569"
---
# <a name="quickstart-create-your-first-function-with-kotlin-and-maven"></a>Snabb start: skapa din första funktion med Kotlin och maven

Den här artikeln vägleder dig genom att använda kommando rads verktyget Maven för att bygga och publicera ett Kotlin-funktions projekt för att Azure Functions. När du är klar körs funktionskoden på [förbrukningsplanen](consumption-plan.md) i Azure och kan utlösas med hjälp av en HTTP-begäran.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Förutsättningar

Om du vill utveckla funktioner med Kotlin måste du ha följande installerat:

- [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), version 8
- [Apache Maven](https://maven.apache.org), version 3.0 eller senare
- [Azure CLI](/cli/azure)
- [Azure Functions Core tools](./functions-run-local.md#v2) version 2.6.666 eller senare

> [!IMPORTANT]
> Miljövariabeln JAVA_HOME måste vara inställd på JDK-installationsplatsen för att snabbstarten ska gå att genomföra.

## <a name="generate-a-new-azure-functions-project"></a>Generera ett nytt Azure Functions-projekt

I en tom mapp kör du följande kommando för att generera Azure Functions-projektet från en [maven-archetype](https://maven.apache.org/guides/introduction/introduction-to-archetypes.html).

# <a name="bash"></a>[bash](#tab/bash)
```bash
mvn archetype:generate \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-kotlin-archetype
```

> [!NOTE]
> Om du har problem med att köra kommandot bör du ta en titt på vilken `maven-archetype-plugin` version som används. Eftersom du kör kommandot i en tom katalog utan `.pom` fil, kan det försöka använda ett plugin-program av den äldre versionen från `~/.m2/repository/org/apache/maven/plugins/maven-archetype-plugin` om du har uppgraderat maven från en äldre version. I så fall kan du försöka ta bort `maven-archetype-plugin` katalogen och köra kommandot igen.

# <a name="powershell"></a>[PowerShell](#tab/powershell)
```powershell
mvn archetype:generate `
    "-DarchetypeGroupId=com.microsoft.azure" `
    "-DarchetypeArtifactId=azure-functions-kotlin-archetype"
```

# <a name="cmd"></a>[Cmd](#tab/cmd)
```cmd
mvn archetype:generate ^
    -DarchetypeGroupId=com.microsoft.azure ^
    -DarchetypeArtifactId=azure-functions-kotlin-archetype
```
---

Maven ber dig om värden som behövs för att slutföra genereringen av projektet. Mer information om värdena _groupId_, _artifactId_ och _version_ finns i referensmaterialet om [namngivningskonventioner i Maven](https://maven.apache.org/guides/mini/guide-naming-conventions.html). Värdet _appName_ måste vara unikt i Azure, så Maven genererar som standard ett appnamn baserat på ett tidigare angivet _artifactId_. _PackageName_ -värdet avgör Kotlin-paketet för den genererade funktions koden.

Identifierarna `com.fabrikam.functions` och `fabrikam-functions` nedan används som exempel och för att göra senare steg i den här snabbstarten lättare att läsa. Du uppmanas att ange dina egna värden för maven i det här steget.

<pre>
[INFO] Parameter: groupId, Value: com.fabrikam.function
[INFO] Parameter: artifactId, Value: fabrikam-function
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: package, Value: com.fabrikam.function
[INFO] Parameter: packageInPathFormat, Value: com/fabrikam/function
[INFO] Parameter: appName, Value: fabrikam-function-20190524171507291
[INFO] Parameter: resourceGroup, Value: java-functions-group
[INFO] Parameter: package, Value: com.fabrikam.function
[INFO] Parameter: version, Value: 1.0-SNAPSHOT
[INFO] Parameter: groupId, Value: com.fabrikam.function
[INFO] Parameter: appRegion, Value: westus
[INFO] Parameter: artifactId, Value: fabrikam-function
</pre>

Maven skapar projektfilerna i en ny mapp med namnet _artifactId_, `fabrikam-functions` i det här exemplet. Den körklara genererade koden i projektet utgörs av en enkel [HTTP-utlöst](./functions-bindings-http-webhook.md) funktion som återspeglar en begäran:

```kotlin
class Function {

    /**
     * This function listens at endpoint "/api/HttpTrigger-Java". Two ways to invoke it using "curl" command in bash:
     * 1. curl -d "HTTP Body" {your host}/api/HttpTrigger-Java&code={your function key}
     * 2. curl "{your host}/api/HttpTrigger-Java?name=HTTP%20Query&code={your function key}"
     * Function Key is not needed when running locally, it is used to invoke function deployed to Azure.
     * More details: https://aka.ms/functions_authorization_keys
     */
    @FunctionName("HttpTrigger-Java")
    fun run(
            @HttpTrigger(
                    name = "req",
                    methods = [HttpMethod.GET, HttpMethod.POST],
                    authLevel = AuthorizationLevel.FUNCTION) request: HttpRequestMessage<Optional<String>>,
            context: ExecutionContext): HttpResponseMessage {

        context.logger.info("HTTP trigger processed a ${request.httpMethod.name} request.")

        val query = request.queryParameters["name"]
        val name = request.body.orElse(query)

        name?.let {
            return request
                    .createResponseBuilder(HttpStatus.OK)
                    .body("Hello, $name!")
                    .build()
        }

        return request
                .createResponseBuilder(HttpStatus.BAD_REQUEST)
                .body("Please pass a name on the query string or in the request body")
                .build()
    }
}
```

## <a name="run-the-function-locally"></a>Köra funktionen lokalt

Byt katalog till den nyskapade projektmappen. Skapa och kör sedan funktionen med Maven:

```
cd fabrikam-function
mvn clean package
mvn azure-functions:run
```

> [!NOTE]
> Om du får det här undantaget: `javax.xml.bind.JAXBException` med Java 9 så hittar du en lösning på [GitHub](https://github.com/jOOQ/jOOQ/issues/6477).

Du kan se dessa utdata när funktionen körs lokalt på datorn och är redo att svara på HTTP-begäranden:

<pre>
Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

        HttpTrigger-Java: [GET,POST] http://localhost:7071/api/HttpTrigger-Java
</pre>

Utlös funktionen från kommandoraden med curl i ett nytt terminalfönster:

```
curl -w '\n' -d LocalFunction http://localhost:7071/api/HttpTrigger-Java
```

<pre>
Hello LocalFunction!
</pre>

Använd `Ctrl-C` i terminalen för att stoppa funktionskoden.

## <a name="deploy-the-function-to-azure"></a>Distribuera funktionen till Azure

Under distribueringen till Azure Functions används autentiseringsuppgifter från Azure CLI. [Logga in med Azure CLI](/cli/azure/authenticate-azure-cli) innan du fortsätter.

```azurecli
az login
```

Distribuera din kod till en ny function-app med `azure-functions:deploy` maven-målet.

> [!NOTE]
> När du använder Visual Studio Code för att distribuera din Function-app måste du komma ihåg att välja en icke-kostnads fri prenumeration eller så får du ett fel meddelande. Du kan titta på din prenumeration på vänster sida av IDE-nätverket.

```
mvn azure-functions:deploy
```

När distributionen är klar visas den URL som du kan använda för att få åtkomst till din Function-app:

<pre>
[INFO] Successfully deployed Function App with package.
[INFO] Deleting deployment package from Azure Storage...
[INFO] Successfully deleted deployment package fabrikam-function-20170920120101928.20170920143621915.zip
[INFO] Successfully deployed Function App at https://fabrikam-function-20170920120101928.azurewebsites.net
[INFO] ------------------------------------------------------------------------
</pre>

Testa funktionsappen som körs på Azure med hjälp av `cURL`. Du måste ändra URL-adressen från exemplet till den distribuerade URL-adressen för din egen funktionsapp från föregående steg.

> [!NOTE]
> Se till att du ställer in **åtkomst behörighet** till `Anonymous` . När du väljer standard nivån för `Function` , måste du presentera [funktions nyckeln](functions-bindings-http-webhook-trigger.md#authorization-keys) i begär Anden för att få åtkomst till funktions slut punkten.

```
curl -w '\n' https://fabrikam-function-20170920120101928.azurewebsites.net/api/HttpTrigger-Java -d AzureFunctions
```

```Output
Hello AzureFunctions!
```

## <a name="make-changes-and-redeploy"></a>Göra ändringar och omdistribuera

Redigera `src/main.../Function.java` käll filen i det genererade projektet för att ändra den text som returneras av din Function-app. Ändra den här raden:

```kotlin
return request
        .createResponseBuilder(HttpStatus.OK)
        .body("Hello, $name!")
        .build()
```

Till följande kod:

```kotlin
return request
        .createResponseBuilder(HttpStatus.OK)
        .body("Hi, $name!")
        .build()
```

Spara ändringarna och distribuera om genom att köra `azure-functions:deploy` från terminalen som tidigare. Funktionsappen uppdateras och den här begäran:

```
curl -w '\n' -d AzureFunctionsTest https://fabrikam-functions-20170920120101928.azurewebsites.net/api/HttpTrigger-Java
```

Du ser de uppdaterade utdata:

<pre>
Hi, AzureFunctionsTest
</pre>


## <a name="reference-bindings"></a>Referens bindningar

Om du vill arbeta med [Azure Functions utlösare och andra bindningar](functions-triggers-bindings.md) än http-utlösare och timer-utlösare måste du installera bindnings tillägg. Även om detta inte krävs i den här artikeln, måste du veta hur du gör detta för att aktivera tillägg när du arbetar med andra typer av bindningar.

[!INCLUDE [functions-extension-bundles](../../includes/functions-extension-bundles.md)]

## <a name="next-steps"></a>Nästa steg

Du har skapat en Kotlin Function-app med en enkel HTTP-utlösare och distribuerat den till Azure Functions.

- Läs [Azure Functions Java Developer Guide](functions-reference-java.md) för mer information om hur du utvecklar Java-och Kotlin-funktioner.
- Lägg till fler funktioner med olika utlösare i projektet med Maven-målet `azure-functions:add`.
- Skriv och felsök funktioner lokalt med [Visual Studio Code](https://code.visualstudio.com/docs/java/java-azurefunctions), [IntelliJ](functions-create-maven-intellij.md) och [Eclipse](functions-create-maven-eclipse.md). 
- Felsök funktioner distribuerade i Azure med Visual Studio Code. Instruktioner finns i dokumentationen för [serverlösa Java-program](https://code.visualstudio.com/docs/java/java-serverless#_remote-debug-functions-running-in-the-cloud) för Visual Studio Code.
