---
title: 'Snabbstart: Formigenkänning klientbibliotek för Java'
description: Använd Formigenkänning-klientbiblioteket för Java för att skapa en formulärbearbetningsapp som extraherar nyckel/värde-par och tabelldata från dina anpassade dokument.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 04/14/2021
ms.custom: devx-track-java
ms.author: lajanuar
ms.openlocfilehash: cd5e6383e71e3f37a26b866156b64c86302f6990
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516445"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
> [!IMPORTANT]
> Koden i den här artikeln använder synkrona metoder och lagring av o säkra autentiseringsuppgifter för enkelhetens skull.

[Referensdokumentation](/java/api/overview/azure/ai-formrecognizer-readme)  |  [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src)  |  [Paket (Maven)](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer)  |  [Exempel](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md)

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration [– Skapa en kostnadsfritt](https://azure.microsoft.com/free/cognitive-services)
* Den aktuella versionen av [Java Development Kit (JDK)](https://www.oracle.com/technetwork/java/javase/downloads/index.html)
* [Gradle-byggverktyget](https://gradle.org/install/), eller någon annan beroendehanterare.
* När du har din Azure-prenumeration skapar Formigenkänning en Formigenkänning resurs i Azure Portal för att <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" hämta din nyckel och "  target="_blank"> </a> slutpunkt. När den har distribuerats klickar du **på Gå till resurs**.
  * Du behöver nyckeln och slutpunkten från den resurs som du skapar för att ansluta ditt program till Formigenkänning-API:et. Du klistrar in din nyckel och slutpunkt i koden nedan senare i snabbstarten.
  * Du kan använda den kostnadsfria prisnivån ( `F0` ) för att prova tjänsten och senare uppgradera till en betald nivå för produktion.
* En Azure Storage blob som innehåller en uppsättning träningsdata. Se [Skapa en träningsdatauppsättning för en anpassad modell för](../../build-training-data-set.md) tips och alternativ för att sätta ihop din träningsdatauppsättning. I den här snabbstarten kan du använda filerna under mappen **Train** (Träna) i [exempeldatauppsättningen](https://go.microsoft.com/fwlink/?linkid=2090451) (ladda ned och extrahera *sample_data.zip*).

## <a name="setting-up"></a>Inrätta

### <a name="create-a-new-gradle-project"></a>Skapa ett nytt Gradle-projekt

I ett konsolfönster (till exempel cmd, PowerShell eller Bash) skapar du en ny katalog för din app och navigerar till den.

```console
mkdir myapp && cd myapp
```

Kör kommandot `gradle init` från arbetskatalogen. Det här kommandot skapar viktiga byggfiler för Gradle, inklusive *build.gradle.kts* som används vid körning för att skapa och konfigurera ditt program.

```console
gradle init --type basic
```

Välj en **DSL** när du uppmanas till det och välj **Kotlin**.

### <a name="install-the-client-library"></a>Installera klientbiblioteket

I den här snabbstarten används Gradle-beroendehanteraren. Du hittar klientbiblioteket och information för andra beroendehanterare på den centrala [Maven-lagringsplatsen](https://mvnrepository.com/artifact/com.azure/azure-ai-formrecognizer).

I projektets *build.gradle.kts-fil* inkluderar du klientbiblioteket som en instruktion, tillsammans med nödvändiga `implementation` plugin-program och inställningar.

#### <a name="v21-preview"></a>[v2.1 förhandsversion](#tab/preview)

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
    implementation(group = "com.azure", name = "azure-ai-formrecognizer", version = "3.1.0-beta.3")
}
```

> [!NOTE]
> I Formigenkänning 3.1.0-beta.3 SDK återspeglas _API-version 2.1-preview.3._

#### <a name="v20"></a>[v2.0](#tab/ga)

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
    implementation(group = "com.azure", name = "azure-ai-formrecognizer", version = "3.1.0-beta.3")
}
```

> [!NOTE]
> I Formigenkänning 3.0.0 SDK återspeglas API v2.1-preview.3

---

### <a name="create-a-java-file"></a>Skapa en Java-fil


Kör följande kommando från arbetskatalogen:

```console
mkdir -p src/main/java
```

Navigera till den nya mappen och skapa en fil med namnet *FormRecognizer.java.* Öppna den i önskat redigeringsprogram eller IDE och lägg till följande `import` -instruktioner:

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_imports)]

> [!TIP]
> Vill du visa hela snabbstartskodfilen samtidigt? Du hittar den på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/java/FormRecognizer/FormRecognizer.java), som innehåller kodexe exemplen i den här snabbstarten.


I programmets **FormRecognizer-klass** skapar du variabler för resursens nyckel och slutpunkt.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_creds)]

> [!IMPORTANT]
> Gå till Azure-portalen. Om den Formigenkänning som du skapade i avsnittet Förutsättningar har **distribuerats** klickar du på **knappen Gå till** resurs under Nästa **steg.** Du hittar din nyckel och slutpunkt på resursens nyckel- **och slutpunktssida** under **resurshantering .**
>
> Kom ihåg att ta bort nyckeln från koden när du är klar och aldrig publicera den offentligt. Överväg att använda ett säkert sätt att lagra och komma åt dina autentiseringsuppgifter för produktion. Se artikeln Cognitive Services [säkerhet](../../../cognitive-services-security.md) för mer information.

I programmets **main-metod lägger** du till anrop för de metoder som används i den här snabbstarten. Du definierar dessa senare. Du måste också lägga till referenser till URL:erna för dina tränings- och testdata.

* [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="HÄMTNING AV SAS-URL":::
* Om du vill hämta en URL för ett formulär att testa kan du använda stegen ovan för att hämta SAS-URL:en för ett enskilt dokument i Blob Storage. Eller ta URL:en för ett dokument som finns någon annanstans.
* Använd metoden ovan för att även hämta URL:en för en kvittobild.
<!-- markdownlint-disable MD024 -->
#### <a name="v21-preview"></a>[v2.1-förhandsgranskning](#tab/preview)

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_mainvars)]

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_maincalls)]

#### <a name="v20"></a>[v2.0](#tab/ga)

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_mainvars)]

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_maincalls)]

---

## <a name="object-model"></a>Objektmodell

Med Formigenkänning kan du skapa två olika klienttyper. Den första används `FormRecognizerClient` för att fråga tjänsten efter identifierade formulärfält och innehåll. Den andra är `FormTrainingClient` att använda för att skapa och hantera anpassade modeller som du kan använda för att förbättra igenkänningen.

### <a name="formrecognizerclient"></a>FormRecognizerClient

`FormRecognizerClient` tillhandahåller åtgärder för:

* Känna igen formulärfält och innehåll med hjälp av anpassade modeller som tränats för att analysera dina anpassade formulär.  Dessa värden returneras i en samling `RecognizedForm` objekt. Se exemplet [Analysera anpassade formulär.](#analyze-forms-with-a-custom-model)
* Känna igen formulärinnehåll, inklusive tabeller, linjer och ord, utan att behöva träna en modell.  Formulärinnehåll returneras i en samling `FormPage` objekt. Se exemplet [Analysera layout.](#analyze-layout)
* Känna igen vanliga fält från amerikanska kvitton, visitkort, fakturor och identitetsdokument med hjälp av en förtränad modell på Formigenkänning tjänsten.

### <a name="formtrainingclient"></a>FormTrainingClient

`FormTrainingClient` tillhandahåller åtgärder för:

* Träna anpassade modeller för att analysera alla fält och värden som finns i dina anpassade formulär.  En `CustomFormModel` returneras som anger de formulärtyper som modellen kommer att analysera och de fält som den extraherar för varje formulärtyp.
* Träna anpassade modeller för att analysera specifika fält och värden som du anger genom att märka dina anpassade formulär.  En `CustomFormModel` returneras som anger de fält som modellen ska extrahera, samt den uppskattade noggrannheten för varje fält.
* Hantera modeller som skapats i ditt konto.
* Kopiera en anpassad modell från en Formigenkänning resurs till en annan.

> [!NOTE]
> Modeller kan också tränas med ett grafiskt användargränssnitt, till exempel [Formigenkänning Labeling Tool](../../quickstarts/label-tool.md).

## <a name="code-examples"></a>Kodexempel

Dessa kodfragment visar hur du utför följande uppgifter med Formigenkänning klientbibliotek för Java:
<!-- markdownlint-disable MD001 -->
#### <a name="v21-preview"></a>[v2.1 förhandsversion](#tab/preview)

* [Autentisera klienten](#authenticate-the-client)
* [Analysera layout](#analyze-layout)
* [Analysera kvitton](#analyze-receipts)
* [Analysera visitkort](#analyze-business-cards)
* [Analysera fakturor](#analyze-invoices)
* [Analysera identitetsdokument](#analyze-identity-documents)
* [Träna en anpassad modell](#train-a-custom-model)
* [Analysera formulär med en anpassad modell](#analyze-forms-with-a-custom-model)
* [Hantera dina anpassade modeller](#manage-your-custom-models)

#### <a name="v20"></a>[v2.0](#tab/ga)

* [Autentisera klienten](#authenticate-the-client)
* [Analysera layout](#analyze-layout)
* [Analysera kvitton](#analyze-receipts)
* [Träna en anpassad modell](#train-a-custom-model)
* [Analysera formulär med en anpassad modell](#analyze-forms-with-a-custom-model)
* [Hantera dina anpassade modeller](#manage-your-custom-models)

---

## <a name="authenticate-the-client"></a>Autentisera klienten

Längst upp i **huvudmetoden** lägger du till följande kod. Här autentiserar du två klientobjekt med hjälp av de prenumerationsvariabler som du definierade ovan. Du använder ett **AzureKeyCredential-objekt,** så att du vid behov kan uppdatera API-nyckeln utan att skapa nya klientobjekt.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_auth)]

## <a name="analyze-layout"></a>Analysera layout

Du kan använda Formigenkänning för att analysera tabeller, linjer och ord i dokument, utan att behöva träna en modell. Mer information om extrahering av layout finns i [layoutkonceptuell guide.](../../concept-layout.md)

Om du vill analysera innehållet i en fil på en viss URL använder du metoden **beginRecognizeContentFromUrl.**

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_getcontent_call)]

> [!TIP]
> Du kan också hämta innehåll från en lokal fil. Se [FormRecognizerClient-metoderna,](/java/api/com.azure.ai.formrecognizer.formrecognizerclient) till exempel **beginRecognizeContent**. Eller så kan du se exempelkoden på [GitHub för](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) scenarier som rör lokala avbildningar.

Det returnerade värdet är en samling **FormPage-objekt:** en för varje sida i det skickade dokumentet. Följande kod itererar genom dessa objekt och skriver ut de extraherade nyckel/värde-paren och tabelldata.

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

Det här avsnittet visar hur du analyserar och extraherar vanliga fält från amerikanska kvitton med hjälp av en förtränad kvittomodell. Mer information om kvittoanalys finns i [konceptuella kvittoguiden](../../concept-receipts.md).

Om du vill analysera kvitton från en URI använder du **metoden beginRecognizeReceiptsFromUrl.**

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_call)]

> [!TIP]
> Du kan också analysera lokala kvittobilder. Se [FormRecognizerClient-metoderna,](/java/api/com.azure.ai.formrecognizer.formrecognizerclient) till exempel **beginRecognizeReceipts**. Eller så kan du se exempelkoden på [GitHub för](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) scenarier som involverar lokala bilder.

Det returnerade värdet är en samling **recognizedReceipt-objekt:** ett för varje sida i det skickade dokumentet. Nästa kodblock itererar genom kvittona och skriver ut information till konsolen.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_receipts_print)]

Nästa kodblock itererar genom de enskilda objekt som identifierats på kvittot och skriver ut information till konsolen.

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

#### <a name="v21-preview"></a>[v2.1 förhandsversion](#tab/preview)

Det här avsnittet visar hur du analyserar och extraherar vanliga fält från engelska visitkort med hjälp av en förtränad modell. Mer information om visitkortsanalys finns i [konceptguiden för visitkort.](../../concept-business-cards.md)

Om du vill analysera visitkort från en URL använder du `beginRecognizeBusinessCardsFromUrl` metoden .

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_bc_call)]

> [!TIP]
> Du kan också analysera lokala visitkortsbilder. Se [FormRecognizerClient-metoderna,](/java/api/com.azure.ai.formrecognizer.formrecognizerclient) till exempel **beginRecognizeBusinessCards**. Eller så kan du se exempelkoden på [GitHub för](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) scenarier som involverar lokala bilder.

Det returnerade värdet är en samling **RecognizedForm-objekt:** ett för varje kort i dokumentet. Följande kod bearbetar visitkortet på den angivna URI:en och skriver ut de större fälten och värdena till konsolen.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_bc_print)]

#### <a name="v20"></a>[v2.0](#tab/ga)

> [!IMPORTANT]
> Den här funktionen är inte tillgänglig i den valda API-versionen.

---

## <a name="analyze-invoices"></a>Analysera fakturor

#### <a name="v21-preview"></a>[v2.1 förhandsversion](#tab/preview)

Det här avsnittet visar hur du analyserar och extraherar vanliga fält från försäljningsfakturor med hjälp av en förtränad modell. Mer information om fakturaanalys finns i den [konceptuella guiden Faktura.](../../concept-invoices.md)

Om du vill analysera fakturor från en URL använder du `beginRecognizeInvoicesFromUrl` metoden .

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_invoice_call)]

> [!TIP]
> Du kan också analysera lokala fakturor. Se [FormRecognizerClient-metoderna,](/java/api/com.azure.ai.formrecognizer.formrecognizerclient) till exempel **beginRecognizeInvoices**. Eller så kan du se exempelkoden på [GitHub för](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) scenarier som rör lokala avbildningar.

Det returnerade värdet är en samling **RecognizedForm-objekt:** en för varje faktura i dokumentet. Följande kod bearbetar fakturan på den angivna URI:en och skriver ut de större fälten och värdena till konsolen.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java?name=snippet_invoice_print)]

#### <a name="v20"></a>[v2.0](#tab/ga)

> [!IMPORTANT]
> Den här funktionen är inte tillgänglig i den valda API-versionen.

---

## <a name="analyze-identity-documents"></a>Analysera identitetsdokument

#### <a name="v21-preview"></a>[v2.1-förhandsgranskning](#tab/preview)

Det här avsnittet visar hur du analyserar och extraherar viktig information från myndighetsutgivna identifieringsdokument – världsomfattande pass och amerikanska drivrutinslicenser – med hjälp av Formigenkänning fördefinierade ID-modell. Mer information om identitetsdokumentanalys finns i vår [fördefinierade begreppsguide för identifieringsmodellen.](../../concept-identification-cards.md)

Om du vill analysera identitetsdokument från en URI använder du `beginRecognizeIdDocumentsFromUrl` metoden .

:::code language="java" source="~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java" id="snippet_id_call":::

> [!TIP]
> Du kan också analysera bilder av lokala identitetsdokument. Se [FormRecognizerClient-metoderna,](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient) till exempel **beginRecognizeIdDocuments**. Se även exempelkoden på [GitHub för scenarier](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) som rör lokala avbildningar.

Följande kod bearbetar identitetsdokumentet vid den angivna URI:en och skriver ut de större fälten och värdena till konsolen.

:::code language="java" source="~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer-preview.java" id="snippet_id_print":::

#### <a name="v20"></a>[v2.0](#tab/ga)

> [!IMPORTANT]
> Den här funktionen är inte tillgänglig i den valda API-versionen.

---

## <a name="train-a-custom-model"></a>Träna en anpassad modell

Det här avsnittet visar hur du tränar en modell med dina egna data. En tränad modell kan mata ut strukturerade data som innehåller nyckel/värde-relationerna i det ursprungliga formulärdokumentet. När du har tränat modellen kan du testa och träna om den och så småningom använda den för att extrahera data från flera formulär på ett tillförlitligt sätt utifrån dina behov.

> [!NOTE]
> Du kan också träna modeller med ett grafiskt användargränssnitt, till exempel [Formigenkänning exempeletikettverktyget](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Träna en modell utan etiketter

Träna anpassade modeller för att analysera alla fält och värden som finns i dina anpassade formulär utan att manuellt märka träningsdokumenten.

Följande metod tränar en modell på en viss uppsättning dokument och skriver ut modellens status till konsolen.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_call)]

Det **returnerade CustomFormModel-objektet** innehåller information om de formulärtyper som modellen kan analysera och de fält som den kan extrahera från varje formulärtyp. Följande kodblock skriver ut den här informationen till konsolen.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_train_print)]

Slutligen returnerar den här metoden modellens unika ID.

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

Du kan också träna anpassade modeller genom att manuellt märka träningsdokumenten. Träning med etiketter leder till bättre prestanda i vissa scenarier. Om du vill träna med etiketter måste du ha särskilda etikettinformationsfiler *\<filename\> (.pdf.labels.jspå*) i bloblagringscontainern tillsammans med träningsdokumenten. [Exempeletikettverktyget Formigenkänning ett användargränssnitt](../../quickstarts/label-tool.md) som hjälper dig att skapa etikettfilerna. När du har dem kan du anropa **metoden beginTraining** med *parametern useTrainingLabels* inställd på `true` .

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_trainlabels_call)]


Den **returnerade CustomFormModel** anger de fält som modellen kan extrahera, tillsammans med dess uppskattade noggrannhet i varje fält. Följande kodblock skriver ut den här informationen till konsolen.

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

Det här avsnittet visar hur du extraherar nyckel-/värdeinformation och annat innehåll från dina anpassade formulärtyper med hjälp av modeller som du har tränat med dina egna formulär.

> [!IMPORTANT]
> För att kunna implementera det här scenariot måste du redan ha tränat en modell så att du kan skicka dess ID till metoden nedan. Se [avsnittet Träna en](#train-a-model-without-labels) modell.

Du använder metoden **beginRecognizeCustomFormsFromUrl.**

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_analyze_call)]

> [!TIP]
> Du kan också analysera en lokal fil. Se [FormRecognizerClient-metoderna,](/java/api/com.azure.ai.formrecognizer.formrecognizerclient) till exempel **beginRecognizeCustomForms**. Eller så kan du se exempelkoden på [GitHub för](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples/README.md) scenarier som rör lokala avbildningar.

Det returnerade värdet är en samling **RecognizedForm-objekt:** en för varje sida i det skickade dokumentet. Följande kod skriver ut analysresultaten till konsolen. Den skriver ut varje identifierat fält och motsvarande värde, tillsammans med en förtroendepoäng.

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

Det här avsnittet visar hur du hanterar de anpassade modeller som lagras i ditt konto. Följande kod utför alla modellhanteringsuppgifter i en enda metod, som exempel. Börja med att kopiera metodsignaturen nedan:

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage)]


### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Kontrollera antalet modeller i FormRecognizer-resurskontot

Följande kodblock kontrollerar hur många modeller du har sparat i ditt Formigenkänning konto och jämför det med kontogränsen.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_count)]


#### <a name="output"></a>Utdata

```console
The account has 12 custom models, and we can have at most 250 custom models
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Visa en lista över de modeller som för närvarande lagras i resurskontot

Följande kodblock visar en lista över aktuella modeller i ditt konto och skriver ut deras information till konsolen.

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

### <a name="delete-a-model-from-the-resource-account"></a>Ta bort en modell från resurskontot

Du kan också ta bort en modell från ditt konto genom att referera till dess ID.

[!code-java[](~/cognitive-services-quickstart-code/java/FormRecognizer/FormRecognizer.java?name=snippet_manage_delete)]

## <a name="run-the-application"></a>Kör programmet

Gå tillbaka till huvudprojektkatalogen. Skapa sedan appen med följande kommando:

```console
gradle build
```

Kör programmet med `run` målet:

```console
gradle run
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resursgruppen. Om du tar bort resursgruppen tas även alla andra resurser som är associerade med den bort.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Felsökning

Skapa undantag från `ErrorResponseException` Recognizer-klienter. Om du till exempel försöker ange en ogiltig filkälls-URL utlöses en med `ErrorResponseException` ett fel som anger felorsaken. I följande kodfragment hanteras felet korrekt genom att fånga undantaget och visa ytterligare information om felet.

```java Snippet:FormRecognizerBadRequest
try {
    formRecognizerClient.beginRecognizeContentFromUrl("invalidSourceUrl");
} catch (ErrorResponseException e) {
    System.out.println(e.getMessage());
}
```

### <a name="enable-client-logging"></a>Aktivera klientloggning

Azure-SDK:er för Java erbjuder en konsekvent loggningsberättelse som hjälper dig att felsöka programfel och påskynda deras lösning. Loggarna som skapas samlar in flödet för ett program innan de når terminaltillståndet för att hitta rotproblemet. Se [loggnings-wikin](https://github.com/Azure/azure-sdk-for-java/wiki/Logging-with-Azure-SDK) för vägledning om hur du aktiverar loggning.

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten använde du Formigenkänning Java-klientbiblioteket för att träna modeller och analysera formulär på olika sätt. Härnäst får du tips om hur du skapar en bättre datauppsättning för träning och skapar mer exakta modeller.

> [!div class="nextstepaction"]
> [Skapa en träningsdatauppsättning](../../build-training-data-set.md)

* [Vad är formigenkänning?](../../overview.md)
* Exempelkoden från den här guiden (med mera) finns på [GitHub.](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/src/samples)
