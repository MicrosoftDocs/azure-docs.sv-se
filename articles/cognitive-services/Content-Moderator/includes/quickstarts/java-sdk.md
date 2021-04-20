---
title: Content Moderator snabbstart för Java-klientbibliotek
titleSuffix: Azure Cognitive Services
description: I den här snabbstarten lär du dig hur du kommer igång med Azure Content Moderator klientbibliotek för Java. Skapa programvara för innehållsfiltrering i din app för att följa regler eller underhålla den avsedda miljön för dina användare.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 10/16/2020
ms.custom: devx-track-java, cog-serv-seo-aug-2020
ms.author: pafarley
ms.openlocfilehash: 76d62806541e0a3186cd17712b7af3930d9ccf9f
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726168"
---
Kom igång med Azure Content Moderator klientbibliotek för Java. Följ de här stegen för att installera Maven-paketet och prova exempelkoden för grundläggande uppgifter. 

Content Moderator är en AI-tjänst som gör att du kan hantera innehåll som är potentiellt stötande, riskabelt eller på annat sätt oönskat. Använd den AI-baserade innehållsmodereringstjänsten för att söka igenom text, bilder och videor och tillämpa innehållsflaggor automatiskt. Skapa programvara för innehållsfiltrering i din app för att följa regler eller underhålla den avsedda miljön för dina användare.

Använd Content Moderator för Java för att:

* Moderera text
* Moderera bilder

[Referensdokumentation](/java/api/overview/azure/cognitiveservices/client/contentmoderator)  |  [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-contentmoderator)  | [Artefakt (Maven)](https://mvnrepository.com/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-contentmoderator)  |  [Exempel](/samples/browse/?products=azure&term=content-moderator)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration [– Skapa en utan kostnad](https://azure.microsoft.com/free/cognitive-services/)
* Den aktuella versionen av [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle-byggverktyget](https://gradle.org/install/), eller någon annan beroendehanterare.
* När du har din Azure-prenumeration skapar du Content Moderator resurs för att skapa Content Moderator resurs i Azure Portal för att hämta <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title=" "  target="_blank"> din nyckel och </a> slutpunkt. Vänta tills den har distribuerats och klicka **på knappen Gå till** resurs.
    * Du behöver nyckeln och slutpunkten från den resurs som du skapar för att ansluta ditt program till Content Moderator. Du klistrar in nyckeln och slutpunkten i koden nedan senare i snabbstarten.
    * Du kan använda den kostnadsfria prisnivån ( `F0` ) för att prova tjänsten och uppgradera senare till en betald nivå för produktion.

## <a name="setting-up"></a>Inrätta

### <a name="create-a-new-gradle-project"></a>Skapa ett nytt Gradle-projekt

I ett konsolfönster (till exempel cmd, PowerShell eller Bash) skapar du en ny katalog för din app och navigerar till den. 

```console
mkdir myapp && cd myapp
```

Kör kommandot `gradle init` från arbetskatalogen. Det här kommandot skapar viktiga byggfiler för Gradle, inklusive *build.gradle.kts*, som används vid körning för att skapa och konfigurera ditt program.

```console
gradle init --type basic
```

Välj en **DSL** när du uppmanas till det och välj **Kotlin**.

## <a name="install-the-client-library"></a>Installera klientbiblioteket

Hitta *build.gradle.kts och* öppna det med önskad IDE eller textredigerare. Kopiera sedan följande byggkonfiguration. Den här konfigurationen definierar projektet som ett Java-program vars startpunkt är klassen **ContentModeratorQuickstart**. Den importerar Content Moderator klientbiblioteket och GSON SDK för JSON-serialisering.

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


Kör följande kommando från arbetskatalogen för att skapa en projektkällmapp:

```console
mkdir -p src/main/java
```

Navigera till den nya mappen och skapa en fil med namnet *ContentModeratorQuickstart.java*. Öppna den i önskad redigerare eller IDE och lägg till följande `import` -instruktioner:

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imports)]

> [!TIP]
> Vill du visa hela snabbstartskodfilen samtidigt? Du hittar den på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java), som innehåller kodexe exemplen i den här snabbstarten.

I programmets **ContentModeratorQuickstart-klass** skapar du variabler för resursens nyckel och slutpunkt.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_creds)]

> [!IMPORTANT]
> Gå till Azure-portalen. Om den Content Moderator som du  skapade i avsnittet Förutsättningar har distribuerats klickar du på **knappen Gå till** resurs under **Nästa steg.** Du hittar din nyckel och slutpunkt på resursens nyckel- **och slutpunktssida** under **resurshantering .** 
>
> Kom ihåg att ta bort nyckeln från koden när du är klar och aldrig publicera den offentligt. Överväg att använda ett säkert sätt att lagra och komma åt dina autentiseringsuppgifter för produktion. Mer information finns i artikeln Cognitive Services [säkerhet.](../../../cognitive-services-security.md)

I programmets **main-metod lägger** du till anrop för de metoder som används i den här snabbstarten. Du definierar dessa metoder senare.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_maincalls)]


## <a name="object-model"></a>Objektmodell

Följande klasser hanterar några av de viktigaste funktionerna i Content Moderator Java-klientbiblioteket.

|Name|Beskrivning|
|---|---|
|[ContentModeratorClient](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient)|Den här klassen behövs för alla Content Moderator funktioner. Du instansierar den med din prenumerationsinformation och använder den för att skapa instanser av andra klasser.|
|[ImageModeration](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.imagemoderations)|Den här klassen innehåller funktioner för att analysera bilder för vuxet innehåll, personlig information eller mänskliga ansikten.|
|[TextModerations](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.textmoderations)|Den här klassen innehåller funktioner för att analysera text för språk, svordomar, fel och personlig information.|
|[Omdömen](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.reviews)|Den här klassen innehåller funktionerna i gransknings-API:erna, inklusive metoder för att skapa jobb, anpassade arbetsflöden och mänskliga granskningar.|


## <a name="code-examples"></a>Kodexempel

Dessa kodfragment visar hur du utför följande uppgifter med Content Moderator klientbibliotek för Java:

* [Autentisera klienten](#authenticate-the-client)
* [Moderera text](#moderate-text)
* [Moderera bilder](#moderate-images)


## <a name="authenticate-the-client"></a>Autentisera klienten

I programmets -metod `main` skapar du ett [ContentModeratorClient-objekt med](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.contentmoderatorclient) ditt prenumerationsslutpunktsvärde och prenumerationsnyckel.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_client)]



## <a name="moderate-text"></a>Moderera text

### <a name="set-up-sample-text"></a>Konfigurera exempeltext

Längst upp i klassen **ContentModeratorQuickstart** definierar du en referens till en lokal textfil. Lägg till en .txt-fil i projektkatalogen och ange den text som du vill analysera.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_var)]

### <a name="analyze-text"></a>Analysera text

Skapa en ny metod som läser .txt-filen och anropar **metoden screenText** på varje rad.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod)]

### <a name="print-text-moderation-results"></a>Resultat för textmoderering

Lägg till följande kod för att skriva ut modereringsresultatet till en .json-fil i projektkatalogen.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_print)]

Stäng - och `try` `catch` -instruktionen för att slutföra -metoden.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_textmod_catch)]


## <a name="moderate-images"></a>Moderera bilder

### <a name="set-up-sample-image"></a>Konfigurera exempelbild

I en ny metod skapar du ett **[BodyModelModel-objekt](/java/api/com.microsoft.azure.cognitiveservices.vision.contentmoderator.models.bodymodelmodel)** med en viss URL-sträng som pekar på en bild.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod)]


### <a name="define-helper-class"></a>Definiera hjälpklass

I filen *ContentModeratorQuickstart.java* lägger du sedan till följande klassdefinition i **klassen ContentModeratorQuickstart.** Den här inre klassen används i bildmodereringsprocessen.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_evaluationdata)]


### <a name="analyze-content"></a>Analysera innehåll
Den här kodraden kontrollerar om bilden på den angivna URL:en innehåller innehåll som är vuxet eller inte. Se konceptuell guide för bildmoderering för information om dessa termer.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_ar)]

### <a name="check-for-text"></a>Sök efter text
Den här kodraden söker efter synlig text i bilden.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_text)]

### <a name="check-for-faces"></a>Söka efter ansikten
Den här kodraden kontrollerar bilden efter ansikten.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_faces)]

Lagra slutligen den returnerade informationen i `EvaluationData` listan.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_storedata)]

### <a name="print-results"></a>Skriv ut resultat

Efter loopen lägger du till följande kod, som skriver ut resultatet till konsolen och till en `while` *utdatafil, src/main/resources/ModerationOutput.jspå*.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_printdata)]

Stäng `try` -instruktionen och lägg till en `catch` -instruktion för att slutföra metoden.

[!code-java[](~/cognitive-services-quickstart-code/java/ContentModerator/src/main/java/ContentModeratorQuickstart.java?name=snippet_imagemod_catch)]

## <a name="run-the-application"></a>Kör programmet

Du kan skapa appen med:

```console
gradle build
```

Kör programmet med `gradle run` kommandot :

```console
gradle run
```

Gå sedan till *filen src/main/resources/ModerationOutput.js* och visa resultatet av innehållsmodereringen.

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resursgruppen. När du tar bort resursgruppen tas även alla andra resurser som är associerade med den bort.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten har du lärt dig hur du använder Content Moderator Java-biblioteket för att utföra modereringsuppgifter. Härnäst kan du läsa mer om moderering av bilder eller andra media genom att läsa en konceptuell guide.

> [!div class="nextstepaction"]
> [Begrepp inom bildmoderering](../../image-moderation-api.md)
