---
title: Snabb start för Java-klient bibliotek Content Moderator
titleSuffix: Azure Cognitive Services
description: I den här snabb starten får du lära dig hur du kommer igång med Azures Content Moderator klient bibliotek för Java. Bygg program vara för innehålls filtrering i din app för att följa regler eller underhålla användarnas avsedda miljö.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 10/16/2020
ms.custom: devx-track-java, cog-serv-seo-aug-2020
ms.author: pafarley
ms.openlocfilehash: 646b4378c887bb30bfbd945693aacc4e50c2f02c
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105027950"
---
Kom igång med Azures Content Moderator klient bibliotek för Java. Följ dessa steg om du vill installera maven-paketet och prova exempel koden för grundläggande uppgifter. 

Content Moderator är en AI-tjänst som låter dig hantera innehåll som är potentiellt stötande, riskfylld eller på annat sätt olämpligt. Använd den AI-drivna Content moderatoring-tjänsten för att söka igenom text, bild och videor och tillämpa innehålls flaggor automatiskt. Bygg program vara för innehålls filtrering i din app för att följa regler eller underhålla användarnas avsedda miljö.

Använd Content Moderator klient bibliotek för Java för att:

* Måttlig text
* Måttliga bilder

[Referens dokumentation](/java/api/overview/azure/cognitiveservices/client/contentmoderator)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-contentmoderator)  | [Artefakt (maven)](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-contentmoderator)  |  [Exempel](/samples/browse/?products=azure&term=content-moderator)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* Den aktuella versionen av [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle build-verktyget](https://gradle.org/install/)eller någon annan beroende hanterare.
* När du har en Azure-prenumeration <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title=" skapar du en Content moderator resurs "  target="_blank"> skapa en Content moderator resurs </a> i Azure Portal för att hämta din nyckel och slut punkt. Vänta tills den har distribuerats och klicka på knappen **gå till resurs** .
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till Content Moderator. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-gradle-project"></a>Skapa ett nytt Gradle-projekt

I ett konsol fönster (till exempel cmd, PowerShell eller bash) skapar du en ny katalog för din app och navigerar till den. 

```console
mkdir myapp && cd myapp
```

Kör `gradle init` kommandot från din arbets katalog. Med det här kommandot skapas viktiga build-filer för Gradle, inklusive *build. Gradle. KTS*, som används vid körning för att skapa och konfigurera ditt program.

```console
gradle init --type basic
```

Välj en **DSL** när du uppmanas till det och välj **Kotlin**.

## <a name="install-the-client-library"></a>Installera klient biblioteket

Hitta *build. gradle. KTS* och öppna den med önskad IDE-eller text redigerare. Kopiera sedan i följande build-konfiguration. Den här konfigurationen definierar projektet som ett Java-program vars start punkt är klassen **ContentModeratorQuickstart**. Den importerar Content Moderator klient biblioteket samt GSON SDK för JSON-serialisering.

```kotlin
plugins {
    java
    application
}

application{ 
    mainClassName = "ContentModeratorQuickstart"
}

repositories{
    mavenCentral()
}

dependencies{
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-contentmoderator", version = "1.0.2-beta")
    compile(group = "com.google.code.gson", name = "gson", version = "2.8.5")
}
```

### <a name="create-a-java-file"></a>Skapa en Java-fil


Kör följande kommando från din arbets katalog för att skapa en mapp för projekt Källa:

```console
mkdir -p src/main/java
```

Navigera till den nya mappen och skapa en fil med namnet *ContentModeratorQuickstart. java*. Öppna det i önskat redigerings program eller IDE och Lägg till följande- `import` uttryck:

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imports)]

> [!TIP]
> Vill du Visa hela snabb starts kod filen samtidigt? Du kan hitta den på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java), som innehåller kod exemplen i den här snabb starten.

I programmets **ContentModeratorQuickstart** -klass skapar du variabler för resursens nyckel och slut punkt.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_creds)]

> [!IMPORTANT]
> Gå till Azure-portalen. Om Content Moderator resursen som du skapade i avsnittet **krav** har distribuerats, klickar du på knappen **gå till resurs** under **Nästa steg**. Du hittar din nyckel och slut punkt i resursens **nyckel och slut punkts** sida under **resurs hantering**. 
>
> Kom ihåg att ta bort nyckeln från koden när du är klar och publicera den aldrig offentligt. För produktion bör du överväga att använda ett säkert sätt att lagra och komma åt dina autentiseringsuppgifter. Mer information finns i [säkerhets](../../../cognitive-services-security.md) artikeln Cognitive Services.

I programmets **main** -metod lägger du till anrop för de metoder som används i den här snabb starten. Du definierar dessa senare.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_maincalls)]


## <a name="object-model"></a>Objekt modell

Följande klasser hanterar några av de viktigaste funktionerna i Content Moderator Java-klient biblioteket.

|Name|Beskrivning|
|---|---|
|[ContentModeratorClient](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient)|Den här klassen krävs för alla Content Moderator-funktioner. Du instansierar det med din prenumerations information och använder den för att skapa instanser av andra klasser.|
|[ImageModeration](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.imagemoderations)|Den här klassen innehåller funktioner för att analysera bilder för innehåll som är olämpligt för barn, personlig information eller mänskliga ansikten.|
|[TextModerations](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.textmoderations)|Den här klassen innehåller funktioner för att analysera text för språk, svordomar, fel och personlig information.|
|[Omdömen](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.reviews)|Den här klassen innehåller funktionerna i gransknings-API: erna, inklusive metoder för att skapa jobb, anpassade arbets flöden och mänsklig granskning.|


## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du gör följande uppgifter med Content Moderator klient bibliotek för java:

* [Autentisera klienten](#authenticate-the-client)
* [Måttlig text](#moderate-text)
* [Måttliga bilder](#moderate-images)


## <a name="authenticate-the-client"></a>Autentisera klienten

I programmets `main` metod skapar du ett [ContentModeratorClient](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient) -objekt med hjälp av prenumerationens slut punkts värde och prenumerations nyckel.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_client)]



## <a name="moderate-text"></a>Måttlig text

### <a name="set-up-sample-text"></a>Konfigurera exempel text

Definiera en referens till en lokal textfil överst i **ContentModeratorQuickstart** -klassen. Lägg till en. txt-fil i projekt katalogen och ange den text som du vill analysera.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_var)]

### <a name="analyze-text"></a>Analysera text

Skapa en ny metod som läser txt-filen och anropar metoden **screenText** på varje rad.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod)]

### <a name="print-text-moderation-results"></a>Skriv ut text redigerings resultat

Lägg till följande kod för att skriva ut kontroll resultaten till en. JSON-fil i projekt katalogen.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_print)]

Avsluta- `try` `catch` instruktionen och slutför-metoden.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_catch)]


## <a name="moderate-images"></a>Måttliga bilder

### <a name="set-up-sample-image"></a>Konfigurera exempel avbildning

Skapa ett **[BodyModelModel](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.models.bodymodelmodel)** -objekt med en viss URL-sträng som pekar på en bild i en ny metod.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod)]


### <a name="define-helper-class"></a>Definiera hjälp klass

Lägg sedan till följande klass definition i **ContentModeratorQuickstart** -klassen i *ContentModeratorQuickstart. java* -filen. Den här inre klassen används i avbildnings redigerings processen.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_evaluationdata)]


### <a name="analyze-content"></a>Analysera innehåll
Den här kodraden kontrollerar avbildningen på den angivna URL: en för vuxen eller vågat innehåll. Mer information om de här villkoren finns i översikts hand boken för bild moderator.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_ar)]

### <a name="check-for-text"></a>Sök efter text
Den här kodraden kontrollerar bilden för synlig text.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Sök efter ansikten
Den här kodraden kontrollerar bilden för mänskliga ansikten.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_faces)]

Slutligen lagrar du den information som returnerades i `EvaluationData` listan.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_storedata)]

### <a name="print-results"></a>Utskrifts resultat

Efter `while` loopen lägger du till följande kod, som skriver ut resultatet till-konsolen och till en utdatafil, *src/main/Resources/ModerationOutput.jspå*.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_printdata)]

Avsluta `try` instruktionen och Lägg till en `catch` instruktion för att slutföra metoden.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_catch)]

## <a name="run-the-application"></a>Kör programmet

Du kan bygga appen med:

```console
gradle build
```

Kör programmet med `gradle run` kommandot:

```console
gradle run
```

Gå sedan till filen *src/main/Resources/ModerationOutput.jspå* filen och visa resultatet av din innehålls moderator.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="source-code"></a>Källkod

* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java).

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du använder Content Moderator Java-biblioteket för att utföra moderator uppgifter. Läs sedan mer om hur du använder avbildningen eller andra media genom att läsa en konceptuell guide.

> [!div class="nextstepaction"]
> [Bild moderator koncept](../../image-moderation-api.md)
