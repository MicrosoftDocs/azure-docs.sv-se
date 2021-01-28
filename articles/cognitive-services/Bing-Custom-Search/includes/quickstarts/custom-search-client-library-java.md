---
title: Snabb start för Java-klient bibliotek Anpassad Bing-sökning
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 02/27/2020
ms.custom: devx-track-java
ms.author: aahi
ms.openlocfilehash: 300c602a62b0d6b3ba579931b2222d2cd8667656
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947268"
---
Kom igång med Anpassad Bing-sökning klient bibliotek för Java. Följ de här stegen för att installera paketet och prova exempel koden för grundläggande uppgifter. Med API för anpassad Bing-sökning kan du skapa skräddarsydda, annons fria Sök upplevelser för ämnen som du bryr dig om. Du hittar käll koden för det här exemplet på [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingCustomSearch)

Använd Anpassad Bing-sökning klient bibliotek för Java för att:

* Hitta Sök resultat på webben från Anpassad Bing-sökning-instansen.

[Referens dokumentation](/java/api/overview/azure/cognitiveservices/client/bingcustomsearch)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingCustomSearch)  |  [Artefakt (maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-customsearch/)  |  [Exempel](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/).
* Den aktuella versionen av [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html).
* [Gradle build-verktyget](https://gradle.org/install/)eller någon annan beroende hanterare.
* En instans av anpassad Bing-sökning. Se [snabb start: skapa din första anpassad Bing-sökning-instans](../../quick-start.md) för mer information.

[!INCLUDE [cognitive-services-bing-custom-search-prerequisites](~/includes/cognitive-services-bing-custom-search-signup-requirements.md)]

När du har fått en nyckel från resursen [skapar du en miljö variabel](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) för nyckeln, med namnet `AZURE_BING_CUSTOM_SEARCH_API_KEY` .

### <a name="create-a-new-gradle-project"></a>Skapa ett nytt Gradle-projekt

> [!TIP]
> Om du inte använder Gradle kan du hitta klient biblioteks informationen för andra beroende hanterare på [maven Central-lagringsplatsen](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

I ett konsol fönster (till exempel cmd, PowerShell eller bash) skapar du en ny katalog för din app och navigerar till den.

```console
mkdir myapp && cd myapp
```

Kör `gradle init` kommandot från din arbets katalog. Det här kommandot skapar viktiga build-filer för Gradle, inklusive en *build. Gradle. KTS* -fil som används vid körning för att konfigurera ditt program.

```console
gradle init --type basic
```

Välj en **DSL** när du uppmanas till det och välj **Kotlin**.

## <a name="install-the-client-library"></a>Installera klient biblioteket

Leta upp *build. gradle. KTS* och öppna den med önskad IDE-eller text redigerare. Kopiera sedan i den här build-konfigurationen. Se till att inkludera klient biblioteket Under `dependencies` :

```kotlin
plugins {
    java
    application
}
application {
    mainClassName = "main.java.BingCustomSearchSample"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-customsearch:1.0.2")
}
```

Skapa en mapp för din exempel App. Kör följande kommando från arbets katalogen:

```console
mkdir src/main/java
```

Navigera till den nya mappen och skapa en fil med namnet *BingCustomSearchSample. java*. Öppna den och Lägg till följande- `import` uttryck:


[!code-java[import statements](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=imports)]

Skapa en klass med namnet `BingCustomSearchSample`

```java
public class BingCustomSearchSample {
}
```

I-klassen skapar du en- `main` metod och en variabel för resurs nyckeln. Om du har skapat miljövariabeln när du har startat programmet stänger du och öppnar redigerings programmet, IDE eller gränssnittet som kör det för att få åtkomst till variabeln. Du definierar metoderna senare.

[!code-java[main method](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=main)]

## <a name="object-model"></a>Objekt modell

Anpassad Bing-sökning-klienten är ett [BingCustomSearchAPI](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi) -objekt som skapas från [BingCustomSearchManager](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager) -objektets metod för [autentisering ()](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchmanager.authenticate) . Du kan skicka en Sök förfrågan med hjälp av klientens [BingCustomInstances. search ()](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) -metod.

API-svaret är ett [SearchResponse](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.models.searchresponse) -objekt som innehåller information om Sök frågan och Sök resultat.

## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du gör följande uppgifter med Anpassad Bing-sökning klient bibliotek för java:

* [Autentisera klienten](#authenticate-the-client)
* [Få Sök Resultat från din anpassade Sök instans](#get-search-results-from-your-custom-search-instance)

## <a name="authenticate-the-client"></a>Autentisera klienten

Huvud metoden bör innehålla ett [BingCustomSearchManager](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustomsearchapi) -objekt som tar din nyckel och anropar dess `authenticate()` .

```java
BingCustomSearchAPI client = BingCustomSearchManager.authenticate(subscriptionKey);
```

## <a name="get-search-results-from-your-custom-search-instance"></a>Få Sök Resultat från din anpassade Sök instans

Använd klientens [BingCustomInstances. search ()](/java/api/com.microsoft.azure.cognitiveservices.search.customsearch.bingcustominstances.search#com_microsoft_azure_cognitiveservices_search_customsearch_BingCustomInstances_search__) -funktion för att skicka en Sök fråga till din anpassade instans. Ange `withCustomConfig` som anpassat konfigurations-ID eller Använd som standard `1` . När du har skaffat ett svar från API: et kontrollerar du om det finns några Sök resultat. I så fall får du det första Sök resultatet genom att anropa svarets `webPages().value().get()` funktion och skriva ut resultatets namn och URL.

[!code-java[call the custom search API](~/cognitive-services-java-sdk-samples/Search/BingCustomSearch/src/main/java/BingCustomSearchSample.java?name=runSample)]

## <a name="run-the-application"></a>Kör programmet

Bygg appen med följande kommando från projektets huvud katalog:

```console
gradle build
```

Kör programmet med `run` målet:

```console
gradle run
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Skapa en webbapp för anpassad sökning](../../tutorials/custom-search-web-page.md)
