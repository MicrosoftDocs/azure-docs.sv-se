---
title: Använda Java och Gradle för att publicera en funktion till Azure
description: Skapa och publicera en HTTP-utlöst funktion till Azure med Java och Gradle.
author: KarlErickson
ms.custom: devx-track-java
ms.author: karler
ms.topic: how-to
ms.date: 04/08/2020
ms.openlocfilehash: d7f8aa990f5a5e64d2d5c59b52457149187acddd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773989"
---
# <a name="use-java-and-gradle-to-create-and-publish-a-function-to-azure"></a>Använda Java och Gradle för att skapa och publicera en funktion till Azure

Den här artikeln visar hur du skapar och publicerar ett Java-funktionsprojekt för Azure Functions med gradle-kommandoradsverktyget. När du är klar körs funktionskoden i Azure i en [serverlös värdplan](consumption-plan.md) och utlöses av en HTTP-begäran. 

> [!NOTE]
> Om Gradle inte är det utvecklingsverktyg du föredrar kan du kolla in våra liknande självstudier för Java-utvecklare som använder [Maven,](./create-first-function-cli-java.md) [IntelliJ IDEA](/azure/developer/java/toolkit-for-intellij/quickstart-functions) och [VS Code.](./create-first-function-vs-code-java.md)

## <a name="prerequisites"></a>Förutsättningar

För att kunna utveckla funktioner med hjälp av Java måste du ha följande installerat:

- [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), version 8
- [Azure CLI]
- [Azure Functions Core Tools](./functions-run-local.md#v2) version 2.6.666 eller senare
- [Gradle](https://gradle.org/), version 4.10 och senare

Du behöver också en aktiv Azure-prenumeration. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Miljövariabeln JAVA_HOME måste vara inställd på JDK-installationsplatsen för att snabbstarten ska gå att genomföra.

## <a name="prepare-a-functions-project"></a>Förbereda ett Functions-projekt

Använd följande kommando för att klona exempelprojektet:

```bash
git clone https://github.com/Azure-Samples/azure-functions-samples-java.git
cd azure-functions-samples-java/
```

Öppna `build.gradle` och ändra i följande avsnitt till ett unikt namn för att undvika konflikter med domännamn när du `appName` distribuerar till Azure. 

```gradle
azurefunctions {
    resourceGroup = 'java-functions-group'
    appName = 'azure-functions-sample-demo'
    pricingTier = 'Consumption'
    region = 'westus'
    runtime {
      os = 'windows'
    }
    localDebug = "transport=dt_socket,server=y,suspend=n,address=5005"
}
```

Öppna den nya Function.java-filen från *sökvägen src/main/java* i en textredigerare och granska den genererade koden. Den här koden är [en HTTP-utlöst](functions-bindings-http-webhook.md) funktion som returnerar brödtexten i begäran. 

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=generate-project)

## <a name="run-the-function-locally"></a>Köra funktionen lokalt

Kör följande kommando för att skapa och kör sedan funktionsprojektet:

```bash
gradle jar --info
gradle azureFunctionsRun
```
Du ser utdata som liknar följande Azure Functions Core Tools när du kör projektet lokalt:

<pre>
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...
</pre>

Utlöst funktionen från kommandoraden med hjälp av följande cURL-kommando i ett nytt terminalfönster:

```bash
curl -w "\n" http://localhost:7071/api/HttpExample --data AzureFunctions
```

Förväntade utdata är följande:

<pre>
Hello, AzureFunctions
</pre>

> [!NOTE]
> Om du anger authLevel `FUNCTION` till eller krävs inte `ADMIN` [funktionsnyckeln](functions-bindings-http-webhook-trigger.md#authorization-keys) när du kör lokalt.  

Använd `Ctrl+C` i terminalen för att stoppa funktionskoden.

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=local-run)

## <a name="deploy-the-function-to-azure"></a>Distribuera funktionen till Azure

En funktionsapp och relaterade resurser skapas i Azure när du först distribuerar funktionsappen. Innan du kan distribuera använder du [Azure CLI-kommandot az login](/cli/azure/authenticate-azure-cli) för att logga in på din Azure-prenumeration. 

```azurecli
az login
```

> [!TIP]
> Om ditt konto har åtkomst till flera prenumerationer använder du [az account set för](/cli/azure/account#az_account_set) att ange standardprenumerationen för den här sessionen. 

Använd följande kommando för att distribuera projektet till en ny funktionsapp. 

```bash
gradle azureFunctionsDeploy
```

Detta skapar följande resurser i Azure, baserat på värdena i filen build.gradle:

+ Resursgrupp. Namnges med _den resourceGroup som_ du angav.
+ Lagringskonto. Krävs av Functions. Namnet genereras slumpmässigt baserat på krav på lagringskontonamn.
+ App Service plan. Serverlös förbrukningsplan som värd för funktionsappen i den angivna _appRegion_. Namnet genereras slumpmässigt.
+ Funktionsapp. En funktionsapp är distributions- och körningsenheten för dina funktioner. Namnet är ditt _appName_, som läggs till med ett slumpmässigt genererat tal. 

Distributionen paketerar även projektfilerna och distribuerar dem till den nya funktionsappen med [zip-distribution](functions-deployment-technologies.md#zip-deploy), med läget run-from-package aktiverat.

AuthLevel för HTTP-utlösaren i exempelprojektet är `ANONYMOUS` , vilket hoppar över autentiseringen. Men om du använder en annan authLevel som eller måste du hämta `FUNCTION` `ADMIN` funktionsnyckeln för att anropa funktionsslutpunkten via HTTP. Det enklaste sättet att hämta funktionsnyckeln är från [Azure Portal].

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=deploy)

## <a name="get-the-http-trigger-url"></a>Hämta HTTP-utlösar-URL:en

Du kan hämta den URL som krävs för att utlösa funktionen, med funktionsnyckeln, från Azure Portal. 

1. Bläddra till [Azure Portal,]logga in, skriv _appName_ för funktionsappen i **Sök** överst på sidan och tryck på Retur.
 
1. I funktionsappen väljer du **Funktioner,** väljer din funktion och klickar sedan **på</> Hämta funktions-URL** längst upp till höger. 

    :::image type="content" source="./media/functions-create-first-java-gradle/get-function-url-portal.png" alt-text="Kopiera funktionswebbadressen från Azure Portal":::

1. Välj **standard (funktionsnyckel)** och välj **Kopiera**. 

Du kan nu använda den kopierade URL:en för att få åtkomst till funktionen.

## <a name="verify-the-function-in-azure"></a>Verifiera funktionen i Azure

Om du vill verifiera funktionsappen som körs i Azure med hjälp av ersätter du URL:en från exemplet nedan med `cURL` den URL som du kopierade från portalen.

```console
curl -w "\n" http://azure-functions-sample-demo.azurewebsites.net/api/HttpExample --data AzureFunctions
```

Detta skickar en POST-begäran till funktionsslutpunkten `AzureFunctions` med i brödtexten i begäran. Du ser följande svar.

<pre>
Hello, AzureFunctions
</pre>

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=verify-deployment)

## <a name="next-steps"></a>Nästa steg

Du har skapat ett Java Functions-projekt med en HTTP-utlöst funktion, kört det på din lokala dator och distribuerat det till Azure. Utöka nu din funktion genom att...

> [!div class="nextstepaction"]
> [Lägga till en Azure Storage köutdatabindning](functions-add-output-binding-storage-queue-java.md)


[Azure CLI]: /cli/azure
[Azure-portalen]: https://portal.azure.com