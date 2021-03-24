---
title: 'Snabb start: Azure-hanterings klient bibliotek för Java'
description: I den här snabb starten kommer du igång med Azures hanterings klient bibliotek för Java.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 3/22/2021
ms.author: pafarley
ms.openlocfilehash: 4c0d4dd1a834e42a75da5199b7aaed0e123f8e63
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104879692"
---
[Referens dokumentation](/java/api/com.microsoft.azure.management.cognitiveservices)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cognitiveservices/mgmt-v2017_04_18/src/main/java/com/microsoft/azure/management/cognitiveservices/v2017_04_18)  |  [Paket (maven)](https://mvnrepository.com/artifact/com.microsoft.azure/azure-mgmt-cognitiveservices)

## <a name="java-prerequisites"></a>Java-krav

* En giltig Azure-prenumeration – [skapa en kostnads fri](https://azure.microsoft.com/free/).
* Den aktuella versionen av [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle build-verktyget](https://gradle.org/install/)eller någon annan beroende hanterare.


[!INCLUDE [Create a service principal](./create-service-principal.md)]

[!INCLUDE [Create a resource group](./create-resource-group.md)]

## <a name="create-a-new-java-application"></a>Skapa ett nytt Java-program

I ett konsol fönster (till exempel cmd, PowerShell eller bash) skapar du en ny katalog för din app och navigerar till den. 

```console
mkdir myapp && cd myapp
```

Kör `gradle init` kommandot från din arbets katalog. Med det här kommandot skapas viktiga build-filer för Gradle, inklusive *build. Gradle. KTS* som används vid körning för att skapa och konfigurera ditt program.

```console
gradle init --type basic
```

Välj en **DSL** när du uppmanas till det och välj **Kotlin**.

Kör följande kommando från arbets katalogen:

```console
mkdir -p src/main/java
```

### <a name="install-the-client-library"></a>Installera klient biblioteket

I den här snabb starten används Gradle-beroende hanteraren. Du hittar klient biblioteket och information för andra beroende hanterare på den [centrala maven-lagringsplatsen](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer).

I projektets *build. gradle. KTS* -fil inkluderar du klient biblioteket som en `implementation` instruktion, tillsammans med nödvändiga plugin-program och inställningar.

```kotlin
plugins {
    java
    application
}
application {
    mainClass.set("FormRecognizer")
}
repositories {
    mavenCentral()
}
dependencies {
    implementation(group = "com.microsoft.azure", name = "azure-mgmt-cognitiveservices", version = "1.10.0-beta")
}
```

### <a name="import-libraries"></a>Importera bibliotek

Navigera till den nya **src/main/Java-** mappen och skapa en fil med namnet *Management. java*. Öppna det i önskat redigerings program eller IDE och Lägg till följande- `import` uttryck:

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_imports)]

## <a name="authenticate-the-client"></a>Autentisera klienten

Lägg till en klass i *Management. java* och Lägg sedan till följande fält och deras värden i den. Fyll i sina värden med hjälp av tjänstens huvud namn som du skapade och annan information om Azure-kontot.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_constants)]

Använd sedan de här värdena i **main** -metoden för att skapa ett **CognitiveServicesManager** -objekt. Det här objektet krävs för alla dina Azure-hanterings åtgärder.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_auth)]

## <a name="call-management-methods"></a>Anrops hanterings metoder

Lägg till följande kod i **main** -metoden för att lista tillgängliga resurser, skapa en exempel resurs, lista dina ägda resurser och ta sedan bort exempel resursen. Du definierar dessa metoder i nästa steg.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_calls)]

## <a name="create-a-cognitive-services-resource-java"></a>Skapa en Cognitive Services resurs (Java)

Använd **create** -metoden för att skapa och prenumerera på en ny Cognitive Services-resurs. Med den här metoden läggs en ny fakturerbar resurs till i resurs gruppen som du skickar. När du skapar en ny resurs måste du veta vilken typ av tjänst du vill använda, tillsammans med dess pris nivå (eller SKU) och en Azure-plats. Följande metod tar alla dessa som argument och skapar en resurs.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_create)]

### <a name="choose-a-service-and-pricing-tier"></a>Välj en tjänst och en pris nivå

När du skapar en ny resurs måste du veta vilken typ av tjänst du vill använda, tillsammans med den [pris nivå](https://azure.microsoft.com/pricing/details/cognitive-services/) (eller SKU) som du vill använda. Du använder denna och annan information som parametrar när du skapar resursen. Du kan hitta en lista över tillgängliga kognitiva tjänst typer genom att anropa följande metod:

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_list_avail)]

[!INCLUDE [cognitive-services-subscription-types](../../../../includes/cognitive-services-subscription-types.md)]

[!INCLUDE [SKUs and pricing](./sku-pricing.md)]

## <a name="view-your-resources"></a>Visa dina resurser

Använd följande metod om du vill visa alla resurser under ditt Azure-konto (i alla resurs grupper):

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_list)]

## <a name="delete-a-resource"></a>Ta bort en resurs

Följande metod tar bort den angivna resursen från den angivna resurs gruppen.

[!code-java[](~/cognitive-services-quickstart-code/java/azure_management_service/quickstart.java?name=snippet_delete)]

## <a name="see-also"></a>Se även

* Se **[autentisera begär anden till Azure Cognitive Services](../../authentication.md)** om hur du säkert arbetar med Cognitive Services.
* Se **[Vad är Azure Cognitive Services?](../../what-are-cognitive-services.md)** för att hämta en lista över olika kategorier i Cognitive Services.
* Se den **[naturliga språk supporten](../../language-support.md)** om du vill se en lista över naturliga språk som Cognitive Services stöder.
* Se **[använda Cognitive Services som behållare](../../cognitive-services-container-support.md)** för att förstå hur du använder Cognitive Services lokal.
* Se **[planera och hantera kostnader för Cognitive Services](../../plan-manage-costs.md)** för att beräkna kostnaden för att använda Cognitive Services.
* Mer information om hanterings-SDK finns i **[referens dokumentationen för Azure Management SDK](/java/api/com.microsoft.azure.management.cognitiveservices)** .
