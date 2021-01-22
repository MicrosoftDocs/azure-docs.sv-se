---
title: 'Snabb start: formulär tolkens klient bibliotek för Java'
description: Använd formulär tolkens klient bibliotek för Java för att skapa en app för bearbetning av formulär som extraherar nyckel/värde-par och tabell data från dina anpassade dokument.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 09/21/2020
ms.custom: devx-track-java
ms.author: pafarley
ms.openlocfilehash: 01ac7c01d449cfe794fcdb6c26a8162d3cffd695
ms.sourcegitcommit: 77afc94755db65a3ec107640069067172f55da67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98698109"
---
> [!IMPORTANT]
> Koden i den här artikeln använder synkrona metoder och icke-säkrade inloggnings uppgifter för att förenkla orsaker.

[Referens dokumentation](/java/api/overview/azure/ai-formrecognizer-readme?view=azure-java-preview)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src)  |  [Paket (maven)](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer)  |  [Exempel](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md)

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services)
* Den aktuella versionen av [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle build-verktyget](https://gradle.org/install/)eller någon annan beroende hanterare.
* När du har en Azure-prenumeration kan du <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" skapa en formulär igenkännings resurs "  target="_blank"> skapa en formulär igenkännings resurs <span class="docon docon-navigate-external x-hidden-focus"></span> </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till Forms igenkännings-API: et. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.
* En Azure Storage-blob som innehåller en uppsättning tränings data. Se [skapa en tränings data uppsättning för en anpassad modell](../../build-training-data-set.md) för tips och alternativ för att sätta samman din tränings data uppsättning. I den här snabb starten kan du använda filerna under mappen **träna** i [exempel data uppsättningen](https://go.microsoft.com/fwlink/?linkid=2090451) (Hämta och extrahera *sample_data.zip*).


## <a name="setting-up"></a>Konfigurera

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


### <a name="install-the-client-library"></a>Installera klient biblioteket

I den här snabb starten används Gradle-beroende hanteraren. Du hittar klient biblioteket och information för andra beroende hanterare på den [centrala maven-lagringsplatsen](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer).

I projektets *build. gradle. KTS* -fil inkluderar du klient biblioteket som en `implementation` instruktion, tillsammans med nödvändiga plugin-program och inställningar.

#### <a name="version-20"></a>[version 2,0](#tab/ga)
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
    implementation(group = "com.azure", name = "azure-ai-formrecognizer", version = "3.0.0")
}
```

> [!NOTE]
> Formulär tolken 3.0.0 SDK reflekterar API-version 2,0

#### <a name="version-31-preview"></a>[version 3,1 Preview](#tab/preview)
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
    implementation(group = "com.azure", name = "azure-ai-formrecognizer", version = "3.1.0-beta.1")
}
```

> [!NOTE]
> Formulär tolken 3.1.0 SDK visar API version 2,1 Preview

---

### <a name="create-a-java-file"></a>Skapa en Java-fil


Kör följande kommando från arbets katalogen:

```console
mkdir -p src/main/java
```

Navigera till den nya mappen och skapa en fil med namnet *FormRecognizer. java*. Öppna det i önskat redigerings program eller IDE och Lägg till följande- `import` uttryck:

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_imports)]

> [!TIP]
> Vill du Visa hela snabb starts kod filen samtidigt? Du kan hitta den på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/FormRecognizer/FormRecognizer.java), som innehåller kod exemplen i den här snabb starten.


I programmets **FormRecognizer** -klass skapar du variabler för resursens nyckel och slut punkt.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_creds)]

> [!IMPORTANT]
> Gå till Azure-portalen. Om formulär igenkännings resursen som du skapade i avsnittet **krav** har distribuerats, klickar du på knappen **gå till resurs** under **Nästa steg**. Du hittar din nyckel och slut punkt i resursens **nyckel och slut punkts** sida under **resurs hantering**. 
>
> Kom ihåg att ta bort nyckeln från koden när du är klar och publicera den aldrig offentligt. För produktion bör du överväga att använda ett säkert sätt att lagra och komma åt dina autentiseringsuppgifter. Mer information finns i [säkerhets](../../../cognitive-services-security.md) artikeln Cognitive Services.

I programmets **main** -metod lägger du till anrop för de metoder som används i den här snabb starten. Du definierar dessa senare. Du måste också lägga till referenser till URL: erna för din utbildning och testa data.

* [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]
  
   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="Hämtning av SAS-URL":::
* Om du vill få en URL för ett formulär att testa kan du använda ovanstående steg för att hämta SAS-URL: en för ett enskilt dokument i Blob Storage. Eller ta med URL: en för ett dokument som finns på annan plats.
* Använd metoden ovan för att hämta URL: en för en kvitto bild.

#### <a name="version-20"></a>[version 2,0](#tab/ga)
[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_mainvars)]

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_maincalls)]
#### <a name="version-21-preview"></a>[version 2,1 Preview](#tab/preview)
[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_mainvars)]

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_maincalls)]

---



## <a name="object-model"></a>Objekt modell 

Med formulär tolken kan du skapa två olika klient typer. Det första `FormRecognizerClient` används för att fråga tjänsten om identifierade formulär fält och innehåll. Den andra används `FormTrainingClient` för att skapa och hantera anpassade modeller som du kan använda för att förbättra igenkänningen. 

### <a name="formrecognizerclient"></a>FormRecognizerClient

`FormRecognizerClient` tillhandahåller åtgärder för:

- Igenkänning av formulär fält och innehåll, med anpassade modeller utbildade för att identifiera dina anpassade formulär.  Dessa värden returneras i en `RecognizedForm` objekt samling. Se exempel [analysera anpassade formulär](#analyze-forms-with-a-custom-model).
- Igenkänning av formulär innehåll, inklusive tabeller, rader och ord, utan att behöva träna en modell.  Formulär innehåll returneras i en `FormPage` objekt samling. Se exempel [analys av layout](#analyze-layout).
- Identifiera vanliga fält från USA-kvitton med en förtränad kvitto modell på formulär igenkännings tjänsten.  Dessa fält och meta-data returneras i en `RecognizedForm` objekt samling. Se exempel på att [analysera inleveranser](#analyze-receipts).

### <a name="formtrainingclient"></a>FormTrainingClient

`FormTrainingClient` tillhandahåller åtgärder för:

- Utbilda anpassade modeller för att identifiera alla fält och värden som finns i dina anpassade formulär.  En `CustomFormModel` returneras som anger vilka formulär som modellen ska identifiera och vilka fält som ska extraheras för varje formulär typ.
- Utbilda anpassade modeller för att identifiera vissa fält och värden som du anger genom att namnge dina anpassade formulär.  En `CustomFormModel` returneras som anger de fält som modellen extraherar, samt den uppskattade noggrannheten för varje fält.
- Hantera modeller som skapats i ditt konto.
- Kopiera en anpassad modell från en formulär igenkännings resurs till en annan.

> [!NOTE]
> Modeller kan också tränas med hjälp av ett grafiskt användar gränssnitt, till exempel [etikett verktyget för formulär igenkänning](../../quickstarts/label-tool.md).

## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du utför följande uppgifter med formulär tolkens klient bibliotek för java:

#### <a name="version-20"></a>[version 2,0](#tab/ga)
* [Autentisera klienten](#authenticate-the-client)
* [Analysera layout](#analyze-layout)
* [Analysera kvitton](#analyze-receipts)
* [Träna en anpassad modell](#train-a-custom-model)
* [Analysera formulär med en anpassad modell](#analyze-forms-with-a-custom-model)
* [Hantera dina anpassade modeller](#manage-your-custom-models)
#### <a name="version-21-preview"></a>[version 2,1 Preview](#tab/preview)
* [Autentisera klienten](#authenticate-the-client)
* [Analysera layout](#analyze-layout)
* [Analysera kvitton](#analyze-receipts)
* [Analysera visitkort](#analyze-business-cards)
* [Analysera fakturor](#analyze-invoices)
* [Träna en anpassad modell](#train-a-custom-model)
* [Analysera formulär med en anpassad modell](#analyze-forms-with-a-custom-model)
* [Hantera dina anpassade modeller](#manage-your-custom-models)

---

## <a name="authenticate-the-client"></a>Autentisera klienten

Lägg till följande kod högst upp i **huvud** metoden. Här ska du autentisera två klient objekt med de prenumerationsfiler som du definierade ovan. Du använder ett **AzureKeyCredential** -objekt, så om det behövs kan du uppdatera API-nyckeln utan att skapa nya klient objekt.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_auth)]

## <a name="analyze-layout"></a>Analysera layout

Du kan använda formulär igenkänning för att identifiera tabeller, rader och ord i dokument, utan att behöva träna en modell.

Om du vill identifiera innehållet i en fil på en viss URL använder du metoden **beginRecognizeContentFromUrl** .

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_getcontent_call)]

> [!TIP]
> Du kan också hämta innehåll från en lokal fil. Se [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient?view=azure-java-stable) -metoderna, till exempel **beginRecognizeContent**. Eller, se exempel koden på [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) för scenarier som involverar lokala avbildningar.

Det returnerade värdet är en samling **FormPage** -objekt: en för varje sida i det dokument som skickas. Följande kod itererar igenom dessa objekt och skriver ut de extraherade nyckel-och värdeparen och tabell data.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_getcontent_print)]
### <a name="output"></a>Utdata

```console
Get form content...
----Recognizing content ----
Has width: 8.500000 and height: 11.000000, measured with unit: inch.
Table has 2 rows and 6 columns.
Cell has text Invoice Number.
Cell has text Invoice Date.
Cell has text Invoice Due Date.
Cell has text Charges.
Cell has text VAT ID.
Cell has text 458176.
Cell has text 3/28/2018.
Cell has text 4/16/2018.
Cell has text $89,024.34.
Cell has text ET.
```

## <a name="analyze-receipts"></a>Analysera kvitton

Det här avsnittet visar hur du identifierar och extraherar vanliga fält från amerikanska kvitton med hjälp av en förtränad kvitto modell.

Om du vill känna igen kvitton från en URI använder du metoden **beginRecognizeReceiptsFromUrl** . 

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_call)]

> [!TIP]
> Du kan också identifiera lokala kvitto avbildningar. Se [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient?view=azure-java-stable) -metoderna, till exempel **beginRecognizeReceipts**. Eller, se exempel koden på [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) för scenarier som involverar lokala avbildningar.

Det returnerade värdet är en samling **RecognizedReceipt** -objekt: en för varje sida i det dokument som skickas. Nästa kodblock upprepas genom kvittona och skriver ut information till-konsolen.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_print)]

Nästa kodblock upprepas genom de enskilda objekt som identifierats vid inleveransen och skriver ut information till-konsolen.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_print_items)]

### <a name="output"></a>Utdata 

```console
Analyze receipt...
----------- Recognized Receipt page 0 -----------
Merchant Name: Contoso Contoso, confidence: 0.62
Merchant Address: 123 Main Street Redmond, WA 98052, confidence: 0.99
Transaction Date: 2020-06-10, confidence: 0.90
Receipt Items:
Name: Cappuccino, confidence: 0.96s
Quantity: null, confidence: 0.957s]
Total Price: 2.200000, confidence: 0.95
Name: BACON & EGGS, confidence: 0.94s
Quantity: null, confidence: 0.927s]
Total Price: null, confidence: 0.93
```

## <a name="analyze-business-cards"></a>Analysera visitkort

#### <a name="version-20"></a>[version 2,0](#tab/ga)

> [!IMPORTANT]
> Den här funktionen är inte tillgänglig i den valda API-versionen.

#### <a name="version-21-preview"></a>[version 2,1 Preview](#tab/preview)

Det här avsnittet visar hur du känner igen och extraherar vanliga fält från engelska visitkort med en förtränad modell.

Använd metoden för att identifiera visitkort från en URL `beginRecognizeBusinessCardsFromUrl` . 

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_bc_call)]

> [!TIP]
> Du kan också identifiera lokala visitkorts avbildningar. Se [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient?view=azure-java-stable) -metoderna, till exempel **beginRecognizeBusinessCards**. Eller, se exempel koden på [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) för scenarier som involverar lokala avbildningar.

Det returnerade värdet är en samling **RecognizedForm** -objekt: ett för varje kort i dokumentet. Följande kod bearbetar visitkortet vid den aktuella URI: n och skriver ut de viktigaste fälten och värdena till-konsolen.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_bc_print)]

---

## <a name="analyze-invoices"></a>Analysera fakturor

#### <a name="version-20"></a>[version 2,0](#tab/ga)

> [!IMPORTANT]
> Den här funktionen är inte tillgänglig i den valda API-versionen.

#### <a name="version-21-preview"></a>[version 2,1 Preview](#tab/preview)

Det här avsnittet visar hur du identifierar och extraherar gemensamma fält från försäljnings fakturor med hjälp av en förtränad modell.

Använd metoden för att identifiera visitkort från en URL `beginRecognizeInvoicesFromUrl` . 

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_invoice_call)]

> [!TIP]
> Du kan också identifiera lokala fakturor. Se [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient?view=azure-java-stable) -metoderna, till exempel **beginRecognizeInvoices**. Eller, se exempel koden på [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) för scenarier som involverar lokala avbildningar.

Det returnerade värdet är en samling **RecognizedForm** -objekt: ett för varje faktura i dokumentet. Följande kod bearbetar visitkortet vid den aktuella URI: n och skriver ut de viktigaste fälten och värdena till-konsolen.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_invoice_print)]

---

## <a name="train-a-custom-model"></a>Träna en anpassad modell

Det här avsnittet visar hur du tränar en modell med dina egna data. En utbildad modell kan spara strukturerade data som innehåller nyckel-/värde relationerna i det ursprungliga formulär dokumentet. När du har tränat modellen kan du testa och träna den och sedan använda den för att på ett tillförlitligt sätt extrahera data från fler formulär utifrån dina behov.

> [!NOTE]
> Du kan också träna modeller med ett grafiskt användar gränssnitt, t. ex. [formulär tolkens exempel etikett verktyg](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Träna en modell utan etiketter

Träna anpassade modeller för att identifiera alla fält och värden som finns i dina anpassade formulär utan att manuellt märka utbildnings dokumenten.

Följande metod tränar en modell på en specifik uppsättning dokument och skriver ut modellens status till-konsolen. 


[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_call)]

Det returnerade **CustomFormModel** -objektet innehåller information om formulär typerna som modellen kan identifiera och vilka fält som kan extraheras från varje formulär typ. Följande kod block skriver ut den här informationen till-konsolen.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_print)]

Slutligen returnerar den här metoden det unika ID: t för modellen.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_return)]


### <a name="output"></a>Utdata

```console
Train Model with training data...
Model Id: 20c3544d-97b4-49d9-b39b-dc32d85f1358
Model Status: ready
Training started on: 2020-08-31T16:52:09Z
Training completed on: 2020-08-31T16:52:23Z

Recognized Fields:
The subModel has form type form-0
The model found field 'field-0' with label: Address:
The model found field 'field-1' with label: Charges
The model found field 'field-2' with label: Invoice Date
The model found field 'field-3' with label: Invoice Due Date
The model found field 'field-4' with label: Invoice For:
The model found field 'field-5' with label: Invoice Number
The model found field 'field-6' with label: VAT ID
```

### <a name="train-a-model-with-labels"></a>Träna en modell med etiketter

Du kan också träna anpassade modeller genom att manuellt märka utbildnings dokumenten. Utbildning med etiketter leder till bättre prestanda i vissa scenarier. För att träna med etiketter måste du ha särskilda etikett informations filer (*\<filename\>.pdf.labels.jspå*) i din Blob Storage-behållare tillsammans med utbildnings dokumenten. [Formulär tolkens exempel etikett verktyg](../../quickstarts/label-tool.md) innehåller ett användar gränssnitt som hjälper dig att skapa etikettfiler. När du har gjort det kan du anropa metoden **beginTraining** med parametern *useTrainingLabels* inställd på `true` .

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_trainlabels_call)]


Den returnerade **CustomFormModel** anger de fält som modellen kan extrahera, tillsammans med dess beräknade noggrannhet i varje fält. Följande kod block skriver ut den här informationen till-konsolen.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_trainlabels_print)]


### <a name="output"></a>Utdata

```console
Train Model with training data...
Model Id: 20c3544d-97b4-49d9-b39b-dc32d85f1358
Model Status: ready
Training started on: 2020-08-31T16:52:09Z
Training completed on: 2020-08-31T16:52:23Z

Recognized Fields:
The subModel has form type form-0
The model found field 'field-0' with label: Address:
The model found field 'field-1' with label: Charges
The model found field 'field-2' with label: Invoice Date
The model found field 'field-3' with label: Invoice Due Date
The model found field 'field-4' with label: Invoice For:
The model found field 'field-5' with label: Invoice Number
The model found field 'field-6' with label: VAT ID
```

## <a name="analyze-forms-with-a-custom-model"></a>Analysera formulär med en anpassad modell

Det här avsnittet visar hur du extraherar nyckel/värde-information och annat innehåll från dina anpassade formulär typer med hjälp av modeller som du har tränat med dina egna formulär.

> [!IMPORTANT]
> För att implementera det här scenariot måste du redan ha tränat en modell så att du kan skicka dess ID till metoden nedan. Se avsnittet [träna en modell](#train-a-model-without-labels) .

Du använder metoden **beginRecognizeCustomFormsFromUrl** . 

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_analyze_call)]

> [!TIP]
> Du kan också analysera en lokal fil. Se [FormRecognizerClient](/java/api/com.azure.ai.formrecognizer.formrecognizerclient?view=azure-java-stable) -metoderna, till exempel **beginRecognizeCustomForms**. Eller, se exempel koden på [GitHub](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) för scenarier som involverar lokala avbildningar.

Det returnerade värdet är en samling **RecognizedForm** -objekt: en för varje sida i det dokument som skickas. Följande kod skriver ut analys resultaten till-konsolen. Det skriver ut varje identifierat fält och motsvarande värde, tillsammans med en förtroende poäng.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_analyze_print)]


### <a name="output"></a>Utdata

```console
Analyze PDF form...
----------- Recognized custom form info for page 0 -----------
Form type: form-0
Field 'field-0' has label 'Address:' with a confidence score of 0.91.
Field 'field-1' has label 'Invoice For:' with a confidence score of 1.00.
Field 'field-2' has label 'Invoice Number' with a confidence score of 1.00.
Field 'field-3' has label 'Invoice Date' with a confidence score of 1.00.
Field 'field-4' has label 'Invoice Due Date' with a confidence score of 1.00.
Field 'field-5' has label 'Charges' with a confidence score of 1.00.
Field 'field-6' has label 'VAT ID' with a confidence score of 1.00.
```


## <a name="manage-custom-models"></a>Hantera anpassade modeller

Det här avsnittet visar hur du hanterar de anpassade modeller som lagras i ditt konto. Följande kod utför alla modell hanterings aktiviteter i en enda metod, som exempel. Börja med att kopiera metod signaturen nedan:

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage)]


### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Kontrol lera antalet modeller i resurs kontot för FormRecognizer

Följande kod block kontrollerar hur många modeller som du har sparat i ditt formulärs igenkännings konto och jämför dem med konto gränsen.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_count)]


#### <a name="output"></a>Utdata 

```console
The account has 12 custom models, and we can have at most 250 custom models
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Lista de modeller som för närvarande lagras i resurs kontot

Följande kodblock visar de aktuella modellerna i ditt konto och skriver ut information till-konsolen.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_list)]


#### <a name="output"></a>Utdata 

Det här svaret har trunkerats för läsbarhet.

```console
We have following models in the account:
Model Id: 0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Model Id: 0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Model Status: ready
Training started on: 2020-06-04T18:33:08Z
Training completed on: 2020-06-04T18:33:10Z
Custom Model Form type: form-0b048b60-86cc-47ec-9782-ad0ffaf7a5ce
Custom Model Accuracy: 1.00
Field Text: invoice date
Field Accuracy: 1.00
Field Text: invoice number
Field Accuracy: 1.00
...
```

### <a name="delete-a-model-from-the-resource-account"></a>Ta bort en modell från resurs kontot

Du kan också ta bort en modell från ditt konto genom att referera till dess ID.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_delete)]



## <a name="run-the-application"></a>Kör programmet

Gå tillbaka till huvud projekt katalogen. Bygg sedan appen med följande kommando:

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

## <a name="troubleshooting"></a>Felsökning

Från igenkännings klienterna utlöser `ErrorResponseException` undantag. Om du till exempel försöker ange en ogiltig URL till en filkälla `ErrorResponseException` skulle det uppstå ett fel som indikerar orsaken till felet. I följande kodfragment hanteras felet smidigt genom att undantags felet och ytterligare information om felet visas.

```java Snippet:FormRecognizerBadRequest
try {
    formRecognizerClient.beginRecognizeContentFromUrl("invalidSourceUrl");
} catch (ErrorResponseException e) {
    System.out.println(e.getMessage());
}
```

### <a name="enable-client-logging"></a>Aktivera klient loggning

Azure SDK: er för Java erbjuder en enhetlig loggnings berättelse för att hjälpa till att felsöka program fel och påskynda sin lösning. Loggarna som skapas avbildar flödet av ett program innan det når Terminal-tillstånd för att hjälpa till att hitta rot problemet. Se [loggning av wiki](https://github.com/Azure/azure-sdk-for-java/wiki/Logging-with-Azure-SDK) för att få hjälp med att aktivera loggning.

## <a name="next-steps"></a>Nästa steg

I den här snabb starten använde du formulär tolken Java-klient bibliotek för att träna modeller och analysera formulär på olika sätt. Nu ska du lära dig hur du skapar en bättre tränings data uppsättning och ger mer exakta modeller.

> [!div class="nextstepaction"]
> [Skapa en träningsdatauppsättning](../../build-training-data-set.md)

* [Vad är formigenkänning?](../../overview.md)
* Exempel koden från den här guiden (och mer) finns på [GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples).