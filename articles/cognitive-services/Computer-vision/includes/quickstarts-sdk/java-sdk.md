---
title: 'Snabb start: klient bibliotek för optiskt teckensnitts igenkänning för Java'
description: I den här snabb starten ska du komma igång med klient biblioteket för optiskt teckensnitts igenkänning för Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 12/15/2020
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: 84ac8e8309d9f1d0536d0f7a16ab9cd9f3c10a2c
ms.sourcegitcommit: b8995b7dafe6ee4b8c3c2b0c759b874dff74d96f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2021
ms.locfileid: "106284813"
---
<a name="HOLTop"></a>

Använd det optiska tecken igenkännings klient biblioteket för att läsa tryckt och handskriven text i bilder.

[Referens dokumentation](/java/api/overview/azure/cognitiveservices/client/computervision)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/ms-azure-cs-computervision)  | [Artefakt (maven)](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-computervision)  |  [Exempel](https://azure.microsoft.com/resources/samples/?service=cognitive-services&term=vision&sort=0)

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* Den aktuella versionen av [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle build-verktyget](https://gradle.org/install/)eller någon annan beroende hanterare.
* När du har en Azure-prenumeration <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title=" skapar du en visuellt innehåll resurs "  target="_blank"> skapa en visuellt innehåll resurs </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till Visuellt innehåll-tjänsten. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
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

### <a name="install-the-client-library"></a>Installera klient biblioteket

I den här snabb starten används Gradle-beroende hanteraren. Du hittar klient biblioteket och information för andra beroende hanterare på den [centrala maven-lagringsplatsen](https://search.maven.org/artifact/com.microsoft.azure.cognitiveservices/azure-cognitiveservices-computervision).

Leta upp *build. gradle. KTS* och öppna den med önskad IDE-eller text redigerare. Kopiera sedan i följande build-konfiguration. Den här konfigurationen definierar projektet som ett Java-program vars start punkt är klassen **ComputerVisionQuickstarts**. Visuellt innehålls biblioteket importeras.

```kotlin
plugins {
    java
    application
}
application { 
    mainClassName = "ComputerVisionQuickstarts"
}
repositories {
    mavenCentral()
}
dependencies {
    compile(group = "com.microsoft.azure.cognitiveservices", name = "azure-cognitiveservices-computervision", version = "1.0.4-beta")
}
```

### <a name="create-a-java-file"></a>Skapa en Java-fil

Kör följande kommando från din arbets katalog för att skapa en mapp för projekt Källa:

```console
mkdir -p src/main/java
```

> [!TIP]
> Vill du Visa hela snabb starts kod filen samtidigt? Du kan hitta den på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java), som innehåller kod exemplen i den här snabb starten.

Navigera till den nya mappen och skapa en fil med namnet *ComputerVisionQuickstarts. java*. Öppna det i önskat redigerings program eller IDE.

### <a name="find-the-subscription-key-and-endpoint"></a>Hitta prenumerations nyckeln och slut punkten

Gå till Azure-portalen. Om Visuellt innehåll resursen som du skapade i avsnittet **krav** har distribuerats, klickar du på knappen **gå till resurs** under **Nästa steg**. Du hittar din prenumerations nyckel och slut punkt i resursens **nyckel och slut punkts** sida under **resurs hantering**. 

Definiera klassen **ComputerVisionQuickstarts**. Skapa variabler för din Visuellt innehåll prenumerations nyckel och slut punkt. Klistra in prenumerations nyckeln och slut punkten i följande kod där det anges. Din Visuellt innehåll-slutpunkt har formuläret `https://<your_computer_vision_resource_name>.cognitiveservices.azure.com/` .

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_imports_and_vars)]

> [!IMPORTANT]
> Kom ihåg att ta bort prenumerations nyckeln från koden när du är klar och publicera den aldrig offentligt. För produktion bör du överväga att använda ett säkert sätt att lagra och komma åt dina autentiseringsuppgifter. Till exempel [Azure Key Vault](../../../../key-vault/general/overview.md).

I programmets **main** -metod lägger du till anrop för de metoder som används i den här snabb starten. Du definierar dessa senare.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_beginmain)]

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_authinmain)]

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_readinmain)]

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_endmain)]
> [!div class="nextstepaction"]
> [Jag har konfigurerat klienten](?success=set-up-client#object-model) som [Jag stötte på ett problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=set-up-client)

## <a name="object-model"></a>Objekt modell

Följande klasser och gränssnitt hanterar några av de viktigaste funktionerna i OCR Java SDK.

|Name|Beskrivning|
|---|---|
| [ComputerVisionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) | Den här klassen krävs för alla Visuellt innehåll-funktioner. Du instansierar det med din prenumerations information och använder den för att skapa instanser av andra klasser.|

## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du gör följande uppgifter med OCR-klientcertifikatet för java:

* [Autentisera klienten](#authenticate-the-client)
* [Skriv ut och handskriven text](#read-printed-and-handwritten-text)

## <a name="authenticate-the-client"></a>Autentisera klienten

I en ny metod instansierar du ett [ComputerVisionClient](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervisionclient) -objekt med din slut punkt och nyckel.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_auth)]

> [!div class="nextstepaction"]
> [Jag autentiserade klienten att](?success=authenticate-client#read-printed-and-handwritten-text) [Jag stötte på ett problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=authenticate-client)



## <a name="read-printed-and-handwritten-text"></a>Skriv ut och handskriven text

OCR-tjänsten kan läsa synlig text i en bild och konvertera den till en tecken ström. I det här avsnittet definieras en metod, `ReadFromFile` som tar en lokal fil Sök väg och skriver ut bildens text till-konsolen.

> [!TIP]
> Du kan också läsa text i en fjärravbildning som refereras till av URL. Se [ComputerVision](/java/api/com.microsoft.azure.cognitiveservices.vision.computervision.computervision) -metoderna, t. ex. **läsa**. Eller, se exempel koden på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java) för scenarier som rör fjärranslutna avbildningar.

### <a name="set-up-test-image"></a>Konfigurera test avbildning

Skapa en **resurs/** mapp i ditt projekts **src/main/-** mapp och Lägg till en bild som du vill läsa text från. Du kan ladda ned en [exempel bild](https://raw.githubusercontent.com/MicrosoftDocs/azure-docs/master/articles/cognitive-services/Computer-vision/Images/readsample.jpg) som du kan använda här.

Lägg sedan till följande metod definition i **ComputerVisionQuickstarts** -klassen. Ändra värdet för `localFilePath` att matcha bild filen. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_setup)]

### <a name="call-the-read-api"></a>Anropa Read API

Lägg sedan till följande kod för att anropa **readInStreamWithServiceResponseAsync** -metoden för den aktuella avbildningen.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_call)]

Följande kodblock extraherar åtgärds-ID: t från svaret från Read-anropet. Den använder det här ID: t med en hjälp metod för att skriva ut texten läsa resultat till-konsolen. 

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_response)]

Stäng try/catch-blocket och metod definitionen.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_catch)]

### <a name="get-read-results"></a>Hämta Läs resultat

Lägg sedan till en definition för hjälp metoden. Den här metoden använder åtgärds-ID: t från föregående steg för att fråga Läs åtgärden och få OCR-resultat när de är tillgängliga.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_result_helper_call)]

Resten av metoden tolkar OCR-resultaten och skriver ut dem till-konsolen.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_read_result_helper_print)]

Slutligen lägger du till den andra hjälp metoden som används ovan, som extraherar åtgärds-ID: t från det första svaret.

[!code-java[](~/cognitive-services-quickstart-code/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java?name=snippet_opid_extract)]

> [!div class="nextstepaction"]
> [Jag](?success=read-printed-handwritten-text#run-the-application) [fick ett problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=read-printed-handwritten-text) med att läsa texten

## <a name="run-the-application"></a>Kör programmet

Du kan bygga appen med:

```console
gradle build
```

Kör programmet med `gradle run` kommandot:

```console
gradle run
```

> [!div class="nextstepaction"]
> [Jag körde det program](?success=run-the-application#clean-up-resources) [Jag stötte på ett problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=run-the-application)

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

> [!div class="nextstepaction"]
> [Jag har rensat resurser](?success=clean-up-resources#next-steps) som [jag stött på ett problem](https://microsoft.qualtrics.com/jfe/form/SV_0Cl5zkG3CnDjq6O?PLanguage=Java&Section=clean-up-resources)

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du lärt dig hur du använder OCR Java-biblioteket för att utföra grundläggande uppgifter. Sedan kan du utforska referens dokumentationen och läsa mer om biblioteket.

> [!div class="nextstepaction"]
>[OCR SDK Reference (Java)](/java/api/overview/azure/cognitiveservices/client/computervision)


* [Översikt över OCR](../../overview-ocr.md)
* Källkoden för det här exemplet finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/ComputerVision/src/main/java/ComputerVisionQuickstart.java).
