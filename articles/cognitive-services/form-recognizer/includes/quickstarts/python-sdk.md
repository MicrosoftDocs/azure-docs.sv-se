---
title: 'Snabbstart: Formigenkänning klientbibliotek för Python'
description: Använd Formigenkänning-klientbiblioteket för Python för att skapa en formulärbearbetningsapp som extraherar nyckel/värde-par och tabelldata från dina anpassade dokument.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 04/09/2021
ms.author: lajanuar
ms.openlocfilehash: 606755333856f6dd97ab6c5158ac67f122a1237d
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516468"
---
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
> [!IMPORTANT]
>
> * Koden i den här artikeln använder synkrona metoder och oskadlig lagring av autentiseringsuppgifter för enkelhetens skull. Se referensdokumentationen nedan.

[Referensdokumentation](/python/api/azure-ai-formrecognizer)  |  [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/azure/ai/formrecognizer)  |  [Paket (PyPi)](https://pypi.org/project/azure-ai-formrecognizer/)  |  [Exempel](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples)

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration [– Skapa en kostnadsfritt](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
  * Python-installationen bör innehålla [pip](https://pip.pypa.io/en/stable/). Du kan kontrollera om pip har installerats genom `pip --version` att köra på kommandoraden. Hämta pip genom att installera den senaste versionen av Python.
* En Azure Storage blob som innehåller en uppsättning träningsdata. Se [Skapa en träningsdatauppsättning för en anpassad modell för](../../build-training-data-set.md) tips och alternativ för att sätta ihop din träningsdatauppsättning. I den här snabbstarten kan du använda filerna under mappen **Train** (Träna) i [exempeldatauppsättningen](https://go.microsoft.com/fwlink/?linkid=2090451) (ladda ned och extrahera *sample_data.zip*).
* När du har din Azure-prenumeration skapar Formigenkänning resurs en Formigenkänning resurs i Azure Portal för att <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" hämta din nyckel och "  target="_blank"> </a> slutpunkt. När den har distribuerats klickar du **på Gå till resurs**.
  * Du behöver nyckeln och slutpunkten från den resurs som du skapar för att ansluta ditt program till Formigenkänning-API:et. Du klistrar in nyckeln och slutpunkten i koden nedan senare i snabbstarten.
  * Du kan använda den kostnadsfria prisnivån ( `F0` ) för att prova tjänsten och senare uppgradera till en betald nivå för produktion.

## <a name="setting-up"></a>Inrätta

### <a name="install-the-client-library"></a>Installera klientbiblioteket

När du har installerat Python kan du installera den senaste versionen Formigenkänning klientbiblioteket med:

#### <a name="v21-preview"></a>[v2.1 förhandsversion](#tab/preview)

```console
pip install azure-ai-formrecognizer --pre
```

> [!NOTE]
> Versionen Formigenkänning 3.1.0b4 är den senaste SDK-förhandsversionen och återspeglar _API-version 2.1 preview.3._

#### <a name="v20"></a>[v2.0](#tab/ga)

```console
pip install azure-ai-formrecognizer
```

> [!NOTE]
> I Formigenkänning 3.0.0 SDK återspeglas API v2.0

---

### <a name="create-a-new-python-application"></a>Skapa ett nytt Python-program

Skapa ett nytt Python-program i önskat redigeringsprogram eller IDE. Importera sedan följande bibliotek.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_imports)]

> [!TIP]
> Vill du visa hela snabbstartskodfilen samtidigt? Du hittar den på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/FormRecognizerQuickstart.py), som innehåller kodexe exemplen i den här snabbstarten.

Skapa variabler för resursens Azure-slutpunkt och nyckel.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_creds)]

## <a name="object-model"></a>Objektmodell

Med Formigenkänning kan du skapa två olika klienttyper. Den första används `form_recognizer_client` för att fråga tjänsten för att identifiera formulärfält och innehåll. Den andra används `form_training_client` för att skapa och hantera anpassade modeller som du kan använda för att förbättra igenkänningen. 

### <a name="formrecognizerclient"></a>FormRecognizerClient

`form_recognizer_client` tillhandahåller åtgärder för:

* Identifiera formulärfält och innehåll med hjälp av anpassade modeller som tränats för att analysera dina anpassade formulär.
* Känna igen formulärinnehåll, inklusive tabeller, linjer och ord, utan att behöva träna en modell.
* Identifiera vanliga fält från kvitton med hjälp av en förtränad kvittomodell på Formigenkänning tjänsten.

### <a name="formtrainingclient"></a>FormTrainingClient

`form_training_client` tillhandahåller åtgärder för:

* Träna anpassade modeller för att analysera alla fält och värden som finns i dina anpassade formulär. En mer [detaljerad förklaring av hur du skapar en datauppsättning för](#train-a-model-without-labels) träning finns i tjänstens dokumentation om omärkt modellträning.
* Träna anpassade modeller för att analysera specifika fält och värden som du anger genom att märka dina anpassade formulär. En mer [detaljerad förklaring av hur du tillämpar etiketter på en träningsdatauppsättning](#train-a-model-with-labels) finns i tjänstens dokumentation om etiketterad modellträning.
* Hantera modeller som skapats i ditt konto.
* Kopiera en anpassad modell från en Formigenkänning resurs till en annan.

> [!NOTE]
> Modeller kan också tränas med ett grafiskt användargränssnitt, till [exempel Formigenkänning Labeling Tool](../../quickstarts/label-tool.md).

## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du utför följande uppgifter Formigenkänning klientbiblioteket för Python:
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
#### <a name="v21-preview"></a>[v2.1-förhandsgranskning](#tab/preview)

* [Autentisera klienten](#authenticate-the-client)
* [Analysera layout](#analyze-layout)
* [Analysera kvitton](#analyze-receipts)
* [Analysera visitkort](#analyze-business-cards)
* [Analysera fakturor](#analyze-invoices)
* [Analysera identitetsdokument](#analyze-identity-documents)
* [Analysera formulär med en anpassad modell](#analyze-forms-with-a-custom-model)
* [Träna en anpassad modell](#train-a-custom-model)
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

Här autentiserar du två klientobjekt med hjälp av de prenumerationsvariabler som du definierade ovan. Du använder ett **AzureKeyCredential-objekt,** så att du vid behov kan uppdatera API-nyckeln utan att skapa nya klientobjekt.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_auth)]

## <a name="get-assets-for-testing"></a>Hämta tillgångar för testning

Du måste lägga till referenser till URL:erna för dina tränings- och testdata.

* [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="HÄMTNING AV SAS-URL":::

* Använd exempelformuläret och kvittobilderna som ingår i exemplen nedan (finns även på [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms) eller så kan du använda stegen ovan för att hämta SAS-URL:en för ett enskilt dokument i Blob Storage. 

> [!NOTE]
> Kodfragmenten i den här guiden använder fjärrformulär som nås av URL:er. Om du vill bearbeta lokala formulärdokument i stället kan du läsa de relaterade metoderna i [referensdokumentationen](/python/api/azure-ai-formrecognizer) och [exemplen](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples).

## <a name="analyze-layout"></a>Analysera layout

Du kan använda Formigenkänning för att analysera tabeller, linjer och ord i dokument, utan att behöva träna en modell. Mer information om layout-extrahering finns i [layoutkonceptuell guide](../../concept-layout.md).

Om du vill analysera innehållet i en fil på en viss URL använder du `begin_recognize_content_from_url` metoden . Det returnerade värdet är en samling `FormPage` objekt: ett för varje sida i det skickade dokumentet. Följande kod itererar genom dessa objekt och skriver ut de extraherade nyckel/värde-paren och tabelldata.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_getcontent)]

> [!TIP]
> Du kan också hämta innehåll från lokala bilder. Se [FormRecognizerClient-metoderna,](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient) till exempel `begin_recognize_content` . Eller så kan du se exempelkoden på [GitHub för](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) scenarier som involverar lokala bilder.

### <a name="output"></a>Utdata

```console
Table found on page 1:
Cell text: Invoice Number
Location: [Point(x=0.5075, y=2.8088), Point(x=1.9061, y=2.8088), Point(x=1.9061, y=3.3219), Point(x=0.5075, y=3.3219)]
Confidence score: 1.0

Cell text: Invoice Date
Location: [Point(x=1.9061, y=2.8088), Point(x=3.3074, y=2.8088), Point(x=3.3074, y=3.3219), Point(x=1.9061, y=3.3219)]
Confidence score: 1.0

Cell text: Invoice Due Date
Location: [Point(x=3.3074, y=2.8088), Point(x=4.7074, y=2.8088), Point(x=4.7074, y=3.3219), Point(x=3.3074, y=3.3219)]
Confidence score: 1.0

Cell text: Charges
Location: [Point(x=4.7074, y=2.8088), Point(x=5.386, y=2.8088), Point(x=5.386, y=3.3219), Point(x=4.7074, y=3.3219)]
Confidence score: 1.0
...

```

## <a name="analyze-receipts"></a>Analysera kvitton

Det här avsnittet visar hur du analyserar och extraherar vanliga fält från amerikanska kvitton med hjälp av en förtränad kvittomodell. Mer information om kvittoanalys finns i [konceptuella kvittoguiden](../../concept-receipts.md). Om du vill analysera kvitton från en URL använder du `begin_recognize_receipts_from_url` metoden .

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_receipts)]

> [!TIP]
> Du kan också analysera lokala kvittobilder. Se [FormRecognizerClient-metoder,](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient) till exempel `begin_recognize_receipts` . Eller så kan du se exempelkoden på [GitHub för](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) scenarier som rör lokala avbildningar.

### <a name="output"></a>Utdata

```console
ReceiptType: Itemized has confidence 0.659
MerchantName: Contoso Contoso has confidence 0.516
MerchantAddress: 123 Main Street Redmond, WA 98052 has confidence 0.986
MerchantPhoneNumber: None has confidence 0.99
TransactionDate: 2019-06-10 has confidence 0.985
TransactionTime: 13:59:00 has confidence 0.968
Receipt Items:
...Item #1
......Name: 8GB RAM (Black) has confidence 0.916
......TotalPrice: 999.0 has confidence 0.559
...Item #2
......Quantity: None has confidence 0.858
......Name: SurfacePen has confidence 0.858
......TotalPrice: 99.99 has confidence 0.386
Subtotal: 1098.99 has confidence 0.964
Tax: 104.4 has confidence 0.713
Total: 1203.39 has confidence 0.774
```

## <a name="analyze-business-cards"></a>Analysera visitkort

#### <a name="v21-preview"></a>[v2.1-förhandsgranskning](#tab/preview)

Det här avsnittet visar hur du analyserar och extraherar vanliga fält från engelska visitkort med hjälp av en förtränad modell. Mer information om visitkortsanalys finns i den [konceptuella handboken för visitkort.](../../concept-business-cards.md) 

Om du vill analysera visitkort från en URL använder du `begin_recognize_business_cards_from_url` metoden .

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart-preview.py?name=snippet_bc)]

> [!TIP]
> Du kan också analysera lokala visitkortsbilder. Se [FormRecognizerClient-metoder,](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient) till exempel `begin_recognize_business_cards` . Eller så kan du se exempelkoden på [GitHub för](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) scenarier som rör lokala avbildningar.

#### <a name="v20"></a>[v2.0](#tab/ga)

> [!IMPORTANT]
> Den här funktionen är inte tillgänglig i den valda API-versionen.

---

## <a name="analyze-invoices"></a>Analysera fakturor

#### <a name="v21-preview"></a>[v2.1-förhandsgranskning](#tab/preview)

Det här avsnittet visar hur du analyserar och extraherar vanliga fält från försäljningsfakturor med hjälp av en förtränad modell. Mer information om fakturaanalys finns i den [konceptuella guiden Faktura.](../../concept-invoices.md) 

Om du vill analysera fakturor från en URL använder du `begin_recognize_invoices_from_url` metoden .

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart-preview.py?name=snippet_invoice)]

> [!TIP]
> Du kan också analysera lokala fakturabilder. Se [FormRecognizerClient-metoderna,](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient) till exempel `begin_recognize_invoices` . Eller så kan du se exempelkoden på [GitHub för](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) scenarier som involverar lokala bilder.

#### <a name="v20"></a>[v2.0](#tab/ga)

> [!IMPORTANT]
> Den här funktionen är inte tillgänglig i den valda API-versionen.

---

## <a name="analyze-identity-documents"></a>Analysera identitetsdokument

#### <a name="v21-preview"></a>[v2.1 förhandsversion](#tab/preview)

Det här avsnittet visar hur du analyserar och extraherar viktig information från myndighetsutgivna identifieringsdokument – världsomfattande pass och amerikanska drivrutinslicenser – med hjälp av Formigenkänning fördefinierade ID-modellen. Mer information om fakturaanalys finns i vår [fördefinierade begreppsguide för identifieringsmodellen.](../../concept-identification-cards.md)

Om du vill analysera identitetsdokument från en URL använder du `begin_recognize_id_documents_from_url` metoden .

:::code language="python" source="~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart-preview.py" id="snippet_id":::

> [!TIP]
> Du kan också analysera bilder av identitetsdokument. _Se_ [FormRecognizerClient-metoderna,](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python&preserve-view=true#methods) till exempel `begin_recognize_id_documents` . _Se även_ exempelkoden på [GitHub för scenarier](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) som involverar lokala bilder.

#### <a name="v20"></a>[v2.0](#tab/ga)

> [!IMPORTANT]
> Den här funktionen är inte tillgänglig i den valda API-versionen.

---

## <a name="train-a-custom-model"></a>Träna en anpassad modell

Det här avsnittet visar hur du tränar en modell med dina egna data. En tränad modell kan mata ut strukturerade data som innehåller nyckel/värde-relationerna i det ursprungliga formulärdokumentet. När du har tränat modellen kan du testa och träna om den och så småningom använda den för att extrahera data från flera formulär på ett tillförlitligt sätt utifrån dina behov.

> [!NOTE]
> Du kan också träna modeller med ett grafiskt användargränssnitt, till [exempel Formigenkänning exempeletikettverktyget](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Träna en modell utan etiketter

Träna anpassade modeller för att analysera alla fält och värden som finns i dina anpassade formulär utan att manuellt märka träningsdokumenten.

Följande kod använder träningsklienten med funktionen `begin_training` för att träna en modell på en viss uppsättning dokument. Det `CustomFormModel` returnerade objektet innehåller information om de formulärtyper som modellen kan analysera och de fält som den kan extrahera från varje formulärtyp. Följande kodblock skriver ut den här informationen till konsolen.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_train)]

### <a name="output"></a>Utdata

Det här är utdata för en modell som tränats med träningsdata som är tillgängliga från [Python SDK.](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training)

```console
Model ID: 628739de-779c-473d-8214-d35c72d3d4f7
Status: ready
Training started on: 2020-08-20 23:16:51+00:00
Training completed on: 2020-08-20 23:16:59+00:00

Recognized fields:
The submodel with form type 'form-0' has recognized the following fields: Additional Notes:, Address:, Company Name:, Company Phone:, Dated As:, Details, Email:, Hero Limited, Name:, Phone:, Purchase Order, Purchase Order #:, Quantity, SUBTOTAL, Seattle, WA 93849 Phone:, Shipped From, Shipped To, TAX, TOTAL, Total, Unit Price, Vendor Name:, Website:
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_4.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_5.jpg
Document status: succeeded
Document page count: 1
Document errors: []
```

### <a name="train-a-model-with-labels"></a>Träna en modell med etiketter

Du kan också träna anpassade modeller genom att manuellt märka träningsdokumenten. Träning med etiketter leder till bättre prestanda i vissa scenarier. Returnerade `CustomFormModel` anger de fält som modellen kan extrahera, tillsammans med den uppskattade noggrannheten i varje fält. Följande kodblock skriver ut den här informationen till konsolen.

> [!IMPORTANT]
> Om du vill träna med etiketter måste du ha särskilda etikettinformationsfiler ( `\<filename\>.pdf.labels.json` ) i bloblagringscontainern tillsammans med utbildningsdokumenten. [Exempeletikettverktyget Formigenkänning ett användargränssnitt](../../quickstarts/label-tool.md) som hjälper dig att skapa dessa etikettfiler. När du har dem kan du anropa funktionen `begin_training` med parametern *use_training_labels* inställd på `true` .

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_trainlabels)]

### <a name="output"></a>Utdata

Det här är utdata för en modell som tränats med träningsdata som är tillgängliga från [Python SDK.](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training)

```console
Model ID: ae636292-0b14-4e26-81a7-a0bfcbaf7c91

Status: ready
Training started on: 2020-08-20 23:20:56+00:00
Training completed on: 2020-08-20 23:20:57+00:00

Recognized fields:
The submodel with form type 'form-ae636292-0b14-4e26-81a7-a0bfcbaf7c91' has recognized the following fields: CompanyAddress, CompanyName, CompanyPhoneNumber, DatedAs, Email, Merchant, PhoneNumber, PurchaseOrderNumber, Quantity, Signature, Subtotal, Tax, Total, VendorName, Website
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_4.jpg
Document status: succeeded
Document page count: 1
Document errors: []
Document name: Form_5.jpg
Document status: succeeded
Document page count: 1
Document errors: []
```

## <a name="analyze-forms-with-a-custom-model"></a>Analysera formulär med en anpassad modell

Det här avsnittet visar hur du extraherar nyckel-/värdeinformation och annat innehåll från dina anpassade formulärtyper med hjälp av modeller som du har tränat med dina egna formulär.

> [!IMPORTANT]
> För att kunna implementera det här scenariot måste du redan ha tränat en modell så att du kan skicka dess ID till metoden nedan. Se [avsnittet Träna en](#train-a-model-without-labels) modell.

Du använder `begin_recognize_custom_forms_from_url` metoden . Det returnerade värdet är en samling `RecognizedForm` objekt: ett för varje sida i det skickade dokumentet. Följande kod skriver ut analysresultaten till konsolen. Den skriver ut varje identifierat fält och motsvarande värde, tillsammans med en förtroendepoäng.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_analyze)]

> [!TIP]
> Du kan också analysera lokala bilder. Se [FormRecognizerClient-metoderna,](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient) till exempel `begin_recognize_custom_forms` . Eller så kan du se exempelkoden på [GitHub för](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) scenarier som involverar lokala bilder.

### <a name="output"></a>Utdata

Med hjälp av modellen från föregående exempel tillhandahålls följande utdata.

```console
Form type: form-ae636292-0b14-4e26-81a7-a0bfcbaf7c91
Field 'Merchant' has label 'Merchant' with value 'Invoice For:' and a confidence score of 0.116
Field 'CompanyAddress' has label 'CompanyAddress' with value '1 Redmond way Suite 6000 Redmond, WA' and a confidence score of 0.258
Field 'Website' has label 'Website' with value '99243' and a confidence score of 0.114
Field 'VendorName' has label 'VendorName' with value 'Charges' and a confidence score of 0.145
Field 'CompanyPhoneNumber' has label 'CompanyPhoneNumber' with value '$56,651.49' and a confidence score of 0.249
Field 'CompanyName' has label 'CompanyName' with value 'PT' and a confidence score of 0.245
Field 'DatedAs' has label 'DatedAs' with value 'None' and a confidence score of None
Field 'Email' has label 'Email' with value 'None' and a confidence score of None
Field 'PhoneNumber' has label 'PhoneNumber' with value 'None' and a confidence score of None
Field 'PurchaseOrderNumber' has label 'PurchaseOrderNumber' with value 'None' and a confidence score of None
Field 'Quantity' has label 'Quantity' with value 'None' and a confidence score of None
Field 'Signature' has label 'Signature' with value 'None' and a confidence score of None
Field 'Subtotal' has label 'Subtotal' with value 'None' and a confidence score of None
Field 'Tax' has label 'Tax' with value 'None' and a confidence score of None
Field 'Total' has label 'Total' with value 'None' and a confidence score of None
```


## <a name="manage-your-custom-models"></a>Hantera dina anpassade modeller

Det här avsnittet visar hur du hanterar de anpassade modeller som lagras i ditt konto.

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Kontrollera antalet modeller i FormRecognizer-resurskontot

Följande kodblock kontrollerar hur många modeller du har sparat i ditt Formigenkänning konto och jämför det med kontogränsen.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_count)]


### <a name="output"></a>Utdata

```console
Our account has 5 custom models, and we can have at most 5000 custom models
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Lista de modeller som för närvarande lagras i resurskontot

Följande kodblock visar en lista över aktuella modeller i ditt konto och skriver ut information om dem till konsolen. Det sparar också en referens till den första modellen.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_list)]


### <a name="output"></a>Utdata

Det här är exempel på utdata för testkontot.

```console
We have models with the following ids:
453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e
628739de-779c-473d-8214-d35c72d3d4f7
ae636292-0b14-4e26-81a7-a0bfcbaf7c91
b4b5df77-8538-4ffb-a996-f67158ecd305
c6309148-6b64-4fef-aea0-d39521452699
```

### <a name="get-a-specific-model-using-the-models-id"></a>Hämta en specifik modell med hjälp av modellens ID

Följande kodblock använder det modell-ID som sparades från föregående avsnitt och använder det för att hämta information om modellen.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_getmodel)]


### <a name="output"></a>Utdata

Det här är exempel på utdata för den anpassade modellen som skapades i föregående exempel.

```console
Model ID: ae636292-0b14-4e26-81a7-a0bfcbaf7c91
Status: ready
Training started on: 2020-08-20 23:20:56+00:00
Training completed on: 2020-08-20 23:20:57+00:00
```

### <a name="delete-a-model-from-the-resource-account"></a>Ta bort en modell från resurskontot

Du kan också ta bort en modell från ditt konto genom att referera till dess ID. Den här koden tar bort modellen som användes i föregående avsnitt.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_delete)]


## <a name="run-the-application"></a>Kör programmet

Kör programmet med kommandot `python` i snabbstartsfilen.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resursgruppen. Om du tar bort resursgruppen tas även alla andra resurser som är associerade med den bort.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Felsökning

### <a name="general"></a>Allmänt

Klientbiblioteket Formigenkänning undantag som definierats i [Azure Core](https://aka.ms/azsdk-python-azure-core).

### <a name="logging"></a>Loggning

Det här biblioteket använder [standardloggningsbiblioteket för](https://docs.python.org/3/library/logging.html) loggning. Grundläggande information om HTTP-sessioner (URL:er, rubriker och så vidare) loggas på INFO-nivå.

Detaljerad loggning på FELSÖKNINGsnivå, inklusive begäran/svarskroppar och oredigerade huvuden, kan aktiveras på en klient med `logging_enable` nyckelordsargumentet:

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerLogging.py?name=snippet_logging)]


På samma sätt `logging_enable` kan aktivera detaljerad loggning för en enda åtgärd, även om den inte är aktiverad för klienten:

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerLogging.py?name=snippet_example)]

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten använde du Formigenkänning Python-klientbiblioteket för att träna modeller och analysera formulär på olika sätt. Lär dig sedan tips för att skapa en bättre datauppsättning för träning och skapa mer exakta modeller.

> [!div class="nextstepaction"]
> [Skapa en träningsdatauppsättning](../../build-training-data-set.md)

* [Vad är formigenkänning?](../../overview.md)
* Exempelkoden från den här guiden finns på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/FormRecognizerQuickstart.py).
