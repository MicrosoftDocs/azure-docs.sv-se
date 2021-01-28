---
title: Snabb start för Java-klient bibliotek Visuell sökning i Bing
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 03/26/2020
ms.custom: devx-track-java, devx-track-csharp
ms.author: aahi
ms.openlocfilehash: d1c9589265726bae01b86c152853c40f79825ceb
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98947818"
---
Använd den här snabb starten för att börja hämta Image Insights från tjänsten Visuell sökning i Bing, med Java-klientens bibliotek. Även om Visuell sökning i Bing har en REST API som är kompatibel med de flesta programmeringsspråk, är klient biblioteket ett enkelt sätt att integrera tjänsten i dina program. Du hittar käll koden för den här snabb starten på [GitHub](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingVisualSearch).

Använd Visuell sökning i Bing klient bibliotek för Java för att:

* Ladda upp en bild för att skicka en begäran om visuella sökningar.
* Hämta taggar för bild insikter och visuella Sök taggar.

[Referens dokumentation](/java/api/overview/azure/cognitiveservices/client/bingvisualsearch)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingVisualSearch)  |  [Artefakt (maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-visualsearch/)  |  [Exempel](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* Den aktuella versionen av [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle build-verktyget](https://gradle.org/install/)eller någon annan beroende hanterare

[!INCLUDE [cognitive-services-bing-visual-search-signup-requirements](~/includes/cognitive-services-bing-visual-search-signup-requirements.md)]

När du har fått en nyckel från resursen [skapar du en miljö variabel](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) för nyckeln, med namnet `BING_SEARCH_V7_SUBSCRIPTION_KEY` .

### <a name="create-a-new-gradle-project"></a>Skapa ett nytt Gradle-projekt

I ett konsol fönster (till exempel cmd, PowerShell eller bash) skapar du en ny katalog för din app och navigerar till den. 

```console
mkdir myapp && cd myapp
```

Kör `gradle init` kommandot från din arbets katalog. Med det här kommandot skapas viktiga build-filer för Gradle, inklusive *build. Gradle. KTS* som används vid körning för att skapa och konfigurera ditt program.

```console
gradle init --type basic
```

Välj en **DSL** när du uppmanas till det och välj **Kotlin**.

Leta upp *build. gradle. KTS* och öppna den med önskad IDE-eller text redigerare. Kopiera sedan i den här build-konfigurationen:

```kotlin
plugins {
    java
    application
}
application {
    mainClassName = "main.java.BingVisualSearchSample"
}
repositories {
    mavenCentral()
}
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-visualsearch:1.0.2-beta")
    compile("com.google.code.gson:gson:2.8.5")
}
```

Skapa en mapp för din exempel App. Kör följande kommando från arbets katalogen:

```console
mkdir -p src/main/java
```

Skapa en mapp för avbildningen som du vill överföra till API: et. Placera avbildningen i mappen **resurser** .

```console
mkdir -p src/main/resources
``` 

Navigera till den nya mappen och skapa en fil med namnet *BingVisualSearchSample. java*. Öppna det i önskat redigerings program eller IDE och Lägg till följande- `import` uttryck:

[!code-java[Import statements](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=imports)]

Skapa sedan en ny klass.

```java
public class BingVisualSearchSample {
}
```

I programmets `main` metod skapar du variabler för resursens Azure-slutpunkt och nyckel. Om du har skapat miljövariabeln efter att du har startat programmet måste du stänga och öppna redigerings programmet, IDE eller gränssnittet som kör det för att få åtkomst till variabeln. Skapa sedan en `byte[]` för avbildningen som ska laddas upp. Skapa ett `try` block för de metoder som du definierar senare och Läs in avbildningen och konvertera den till byte med `toByteArray()` .

[!code-java[Main method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=main)]


### <a name="install-the-client-library"></a>Installera klient biblioteket

I den här snabb starten används Gradle-beroende hanteraren. Du hittar klient biblioteket och information för andra beroende hanterare på den [centrala maven-lagringsplatsen](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-textanalytics/).

I projektets *build. gradle. KTS* -fil måste du ta med klient biblioteket som en `implementation` instruktion. 

```kotlin
dependencies {
    compile("org.slf4j:slf4j-simple:1.7.25")
    compile("com.microsoft.azure.cognitiveservices:azure-cognitiveservices-visualsearch:1.0.2-beta")
    compile("com.google.code.gson:gson:2.8.5")
}
```

## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du gör följande uppgifter med Visuell sökning i Bing klient bibliotek och java:

* [Autentisera klienten](#authenticate-the-client)
* [Skicka en begäran om visuella sökningar](#send-a-visual-search-request)
* [Skriv ut bild Insight-token och visuella Sök taggar](#print-the-image-insight-token-and-visual-search-tags)

## <a name="authenticate-the-client"></a>Autentisera klienten

> [!NOTE]
> Den här snabb starten förutsätter att du har [skapat en miljö variabel](../../../cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) för din visuell sökning i Bing nyckel, med namnet `BING_SEARCH_V7_SUBSCRIPTION_KEY` .


I din huvud metod måste du se till att använda din prenumerations nyckel för att instansiera ett [BingVisualSearchAPI](/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingvisualsearchapi) -objekt.

```csharp
BingVisualSearchAPI client = BingVisualSearchManager.authenticate(subscriptionKey);
```

## <a name="send-a-visual-search-request"></a>Skicka en begäran om visuella sökningar

I en ny metod skickar du bildens byte-matris (som skapades i `main()` -metoden) med hjälp av klientens [bingImages (). visualSearch ()](/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.bingimages.visualsearch#com_microsoft_azure_cognitiveservices_search_visualsearch_BingImages_visualSearch__) -metod. 

[!code-java[visualSearch() method](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=visualSearch)]

## <a name="print-the-image-insight-token-and-visual-search-tags"></a>Skriv ut bild Insight-token och visuella Sök taggar

Kontrol lera om [ImageKnowledge](/java/api/com.microsoft.azure.cognitiveservices.search.visualsearch.models.imageknowledge) -objektet är null. Om inte skriver du ut image Insights-token, antalet taggar, antalet åtgärder och den första åtgärds typen.

[!code-java[Print token and tags](~/cognitive-services-java-sdk-samples/Search/BingVisualSearch/src/main/java/BingVisualSearchSample.java?name=printVisualSearchResults)]

## <a name="run-the-application"></a>Kör programmet

Du kan bygga appen med:

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
> [Skapa en enkelsidig webbapp](../../tutorial-bing-visual-search-single-page-app.md)
