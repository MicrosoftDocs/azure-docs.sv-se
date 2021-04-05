---
title: Använd Java och Gradle för att publicera en funktion i Azure
description: Skapa och publicera en HTTP-utlöst funktion i Azure med Java och Gradle.
author: KarlErickson
ms.custom: devx-track-java
ms.author: karler
ms.topic: how-to
ms.date: 04/08/2020
ms.openlocfilehash: c9ecb670cea022988efda8ec690c0724310cdb4b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "97934857"
---
# <a name="use-java-and-gradle-to-create-and-publish-a-function-to-azure"></a>Använd Java och Gradle för att skapa och publicera en funktion i Azure

Den här artikeln visar hur du skapar och publicerar ett Java-Function-projekt för att Azure Functions med kommando rads verktyget Gradle. När du är klar körs funktions koden i Azure i en [värd plan utan server](consumption-plan.md) och utlöses av en http-begäran. 

> [!NOTE]
> Om Gradle inte är ditt fördelaktiga utvecklingsverktyg kan du ta en titt på våra liknande själv studie kurser för Java-utvecklare med [maven](./create-first-function-cli-java.md), [IntelliJ-idé](/azure/developer/java/toolkit-for-intellij/quickstart-functions) och vs- [kod](./create-first-function-vs-code-java.md).

## <a name="prerequisites"></a>Förutsättningar

För att kunna utveckla funktioner med hjälp av Java måste du ha följande installerat:

- [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support), version 8
- [Azure CLI]
- [Azure Functions Core tools](./functions-run-local.md#v2) version 2.6.666 eller senare
- [Gradle](https://gradle.org/), version 4,10 och senare

Du behöver också en aktiv Azure-prenumeration. [!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

> [!IMPORTANT]
> Miljövariabeln JAVA_HOME måste vara inställd på JDK-installationsplatsen för att snabbstarten ska gå att genomföra.

## <a name="prepare-a-functions-project"></a>Förbereda ett Functions-projekt

Använd följande kommando för att klona exempelprojektet:

```bash
git clone https://github.com/Azure-Samples/azure-functions-samples-java.git
cd azure-functions-samples-java/
```

Öppna `build.gradle` och ändra `appName` i följande avsnitt till ett unikt namn för att undvika domän namns konflikt vid distribution till Azure. 

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

Öppna den nya function. java-filen från sökvägen *src/main/Java* i en text redigerare och granska den genererade koden. Den här koden är en [http-utlöst](functions-bindings-http-webhook.md) funktion som upprepar bröd texten i begäran. 

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=generate-project)

## <a name="run-the-function-locally"></a>Köra funktionen lokalt

Kör följande kommando för att skapa och sedan köra-funktions projektet:

```bash
gradle jar --info
gradle azureFunctionsRun
```
Du ser utdata som följande från Azure Functions Core Tools när du kör projektet lokalt:

<pre>
...

Now listening on: http://0.0.0.0:7071
Application started. Press Ctrl+C to shut down.

Http Functions:

    HttpExample: [GET,POST] http://localhost:7071/api/HttpExample
...
</pre>

Utlös funktionen från kommando raden med hjälp av följande spiral kommando i ett nytt terminalfönster:

```bash
curl -w "\n" http://localhost:7071/api/HttpExample --data AzureFunctions
```

Förväntade utdata är följande:

<pre>
Hello, AzureFunctions
</pre>

> [!NOTE]
> Om du anger authLevel till `FUNCTION` eller `ADMIN` , krävs inte [funktions nyckeln](functions-bindings-http-webhook-trigger.md#authorization-keys) när du kör lokalt.  

Använd `Ctrl+C` i terminalen för att stoppa funktionskoden.

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=local-run)

## <a name="deploy-the-function-to-azure"></a>Distribuera funktionen till Azure

En Function-app och relaterade resurser skapas i Azure när du först distribuerar din Function-app. Innan du kan distribuera använder du kommandot [AZ login](/cli/azure/authenticate-azure-cli) Azure CLI för att logga in på din Azure-prenumeration. 

```azurecli
az login
```

> [!TIP]
> Om ditt konto har åtkomst till flera prenumerationer använder du [AZ-konto uppsättning](/cli/azure/account#az-account-set) för att ange standard prenumerationen för den här sessionen. 

Använd följande kommando för att distribuera projektet till en ny function-app. 

```bash
gradle azureFunctionsDeploy
```

Detta skapar följande resurser i Azure, baserat på värdena i filen build. gradle:

+ Resurs grupp. Med namnet med _resourceGroup_ du angav.
+ Lagrings konto. Krävs av functions. Namnet genereras slumpmässigt baserat på lagrings kontots namn krav.
+ App Service plan. Server lös förbruknings plan som är värd för din Function-app i den angivna _appRegion_. Namnet genereras slumpmässigt.
+ Function-app. En Function-app är distributions-och körnings enheten för dina funktioner. Namnet är ditt _APPNAME_ och läggs till med ett slumpmässigt genererat nummer. 

Distributionen paketerar också projektfilerna och distribuerar dem till den nya Function-appen med hjälp av [zip-distribution](functions-deployment-technologies.md#zip-deploy), där läget Kör-från-paket är aktiverat.

AuthLevel för HTTP-utlösare i exempel projekt är `ANONYMOUS` , vilket kommer att hoppa över autentiseringen. Men om du använder andra authLevel som `FUNCTION` eller `ADMIN` måste du hämta funktions nyckeln för att anropa funktions slut punkten över http. Det enklaste sättet att hämta funktions nyckeln är från [Azure Portal].

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=deploy)

## <a name="get-the-http-trigger-url"></a>Hämta URL: en för HTTP-utlösaren

Du kan hämta den URL som krävs för att utlösa din funktion med funktions tangenten från Azure Portal. 

1. Bläddra till [Azure Portal], logga in, ange _APPNAME_ för din Function-app i **Sök** överst på sidan och tryck på RETUR.
 
1. I din Function-app väljer du **funktioner**, väljer din funktion och klickar sedan på **</> Hämta funktions webb adress** längst upp till höger. 

    :::image type="content" source="./media/functions-create-first-java-gradle/get-function-url-portal.png" alt-text="Kopiera funktionswebbadressen från Azure Portal":::

1. Välj **Standard (funktions nyckel)** och välj **Kopiera**. 

Nu kan du använda den kopierade URL: en för att få åtkomst till din funktion.

## <a name="verify-the-function-in-azure"></a>Verifiera funktionen i Azure

För att kontrol lera att funktionen App körs på Azure med `cURL` , ersätter du URL: en från exemplet nedan med den URL som du kopierade från portalen.

```console
curl -w "\n" http://azure-functions-sample-demo.azurewebsites.net/api/HttpExample --data AzureFunctions
```

Detta skickar en POST-begäran till funktions slut punkten med `AzureFunctions` i bröd texten i begäran. Du ser följande svar.

<pre>
Hello, AzureFunctions
</pre>

> [!div class="nextstepaction"]
> [Jag stötte på ett problem](https://www.research.net/r/javae2e?tutorial=functions-create-first-java-gradle&step=verify-deployment)

## <a name="next-steps"></a>Nästa steg

Du har skapat ett Java Functions-projekt med en HTTP-utlöst funktion, kört den på din lokala dator och distribuerat den till Azure. Nu kan du utöka din funktion efter...

> [!div class="nextstepaction"]
> [Lägga till en Azure Storage utgående bindning för kö](functions-add-output-binding-storage-queue-java.md)


[Azure CLI]: /cli/azure
[Azure-portalen]: https://portal.azure.com