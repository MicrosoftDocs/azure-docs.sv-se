---
title: 'Snabb start: formulär tolkens klient bibliotek för python'
description: Använd formulär tolkens klient bibliotek för python för att skapa en app för bearbetning av formulär som extraherar nyckel/värde-par och tabell data från dina anpassade dokument.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 10/26/2020
ms.author: pafarley
ms.openlocfilehash: 9762ca79f73b3333045d1c11376ab315aac2d55e
ms.sourcegitcommit: 02ed9acd4390b86c8432cad29075e2204f6b1bc3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/29/2020
ms.locfileid: "97808665"
---
> [!IMPORTANT]
> * Koden i den här artikeln använder synkrona metoder och icke-säkrade inloggnings uppgifter för att förenkla orsaker. Se referens dokumentationen nedan. 

[Referens dokumentation](/python/api/azure-ai-formrecognizer)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/azure/ai/formrecognizer)  |  [Paket (PyPi)](https://pypi.org/project/azure-ai-formrecognizer/)  |  [Exempel](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples)

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services)
* [Python 3.x](https://www.python.org/)
* En Azure Storage-blob som innehåller en uppsättning tränings data. Se [skapa en tränings data uppsättning för en anpassad modell](../../build-training-data-set.md) för tips och alternativ för att sätta samman din tränings data uppsättning. I den här snabb starten kan du använda filerna under mappen **träna** i [exempel data uppsättningen](https://go.microsoft.com/fwlink/?linkid=2090451) (Hämta och extrahera *sample_data.zip*).
* När du har en Azure-prenumeration kan du <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" skapa en formulär igenkännings resurs "  target="_blank"> skapa en formulär igenkännings resurs <span class="docon docon-navigate-external x-hidden-focus"></span> </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till Forms igenkännings-API: et. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.

## <a name="setting-up"></a>Konfigurera

### <a name="install-the-client-library"></a>Installera klient biblioteket

När du har installerat python kan du installera den senaste versionen av formulär identifierarens klient bibliotek med:

#### <a name="version-20"></a>[version 2,0](#tab/ga)

```console
pip install azure-ai-formrecognizer
```

> [!NOTE]
> Den senaste formulär identifieraren visar API-version 2,0

#### <a name="version-21-preview"></a>[version 2,1 Preview](#tab/preview)

```console
pip install azure-ai-formrecognizer --pre
```

> [!NOTE]
> Formulär tolken för hands version SDK visar API version 2,1 Preview

---

### <a name="create-a-new-python-application"></a>Skapa ett nytt python-program

Skapa ett nytt python-program i önskat redigerings program eller IDE. Importera sedan följande bibliotek.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_imports)]

> [!TIP]
> Vill du Visa hela snabb starts kod filen samtidigt? Du kan hitta den på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/FormRecognizerQuickstart.py), som innehåller kod exemplen i den här snabb starten.


Skapa variabler för resursens Azure-slutpunkt och nyckel. 

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_creds)]


## <a name="object-model"></a>Objekt modell 

Med formulär tolken kan du skapa två olika klient typer. Det första `form_recognizer_client` används för att fråga tjänsten om identifierade formulär fält och innehåll. Den andra används `form_training_client` för att skapa och hantera anpassade modeller som du kan använda för att förbättra igenkänningen. 

### <a name="formrecognizerclient"></a>FormRecognizerClient
`form_recognizer_client` tillhandahåller åtgärder för:

 * Igenkänning av formulär fält och innehåll med anpassade modeller utbildade för att identifiera dina anpassade formulär. 
 * Igenkänning av formulär innehåll, inklusive tabeller, rader och ord, utan att behöva träna en modell. 
 * Att känna igen vanliga fält från inleveranser, med en förtränad kvitto modell på formulär igenkännings tjänsten.

### <a name="formtrainingclient"></a>FormTrainingClient
`form_training_client` tillhandahåller åtgärder för:

* Utbilda anpassade modeller för att identifiera alla fält och värden som finns i dina anpassade formulär. Mer detaljerad information om hur du skapar en tränings data uppsättning finns i [tjänstens dokumentation om etiketterad modell utbildning](#train-a-model-without-labels) .
* Utbilda anpassade modeller för att identifiera vissa fält och värden som du anger genom att namnge dina anpassade formulär. Se [tjänst dokumentationen om etiketterad modell utbildning](#train-a-model-with-labels) för en mer detaljerad förklaring av hur du använder etiketter i en tränings data uppsättning.
* Hantera modeller som skapats i ditt konto.
* Kopiera en anpassad modell från en formulär igenkännings resurs till en annan.

> [!NOTE]
> Modeller kan också tränas med hjälp av ett grafiskt användar gränssnitt, till exempel [etikett verktyget för formulär igenkänning](../../quickstarts/label-tool.md).

## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du utför följande uppgifter med formulär tolkens klient bibliotek för python:

#### <a name="version-20"></a>[version 2,0](#tab/ga)

* [Autentisera klienten](#authenticate-the-client)
* [Identifiera formulär innehåll](#recognize-form-content)
* [Identifiera kvitton](#recognize-receipts)
* [Träna en anpassad modell](#train-a-custom-model)
* [Analysera formulär med en anpassad modell](#analyze-forms-with-a-custom-model)
* [Hantera dina anpassade modeller](#manage-your-custom-models)

#### <a name="version-21-preview"></a>[version 2,1 Preview](#tab/preview)

* [Autentisera klienten](#authenticate-the-client)
* [Identifiera formulär innehåll](#recognize-form-content)
* [Identifiera kvitton](#recognize-receipts)
* [Identifiera visitkort](#recognize-business-cards)
* [Identifiera fakturor](#recognize-invoices)
* [Träna en anpassad modell](#train-a-custom-model)
* [Analysera formulär med en anpassad modell](#analyze-forms-with-a-custom-model)
* [Hantera dina anpassade modeller](#manage-your-custom-models)

---

## <a name="authenticate-the-client"></a>Autentisera klienten

Här ska du autentisera två klient objekt med de prenumerationsfiler som du definierade ovan. Du använder ett **AzureKeyCredential** -objekt, så om det behövs kan du uppdatera API-nyckeln utan att skapa nya klient objekt.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_auth)]


## <a name="get-assets-for-testing"></a>Få till gångar för testning

Du måste lägga till referenser till URL: erna för din utbildning och testa data.
* [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]
  
   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="Hämtning av SAS-URL":::
* Använd exemplen från och kvitto bilder som ingår i exemplen nedan (även tillgängligt på [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms) eller så kan du använda ovanstående steg för att hämta SAS-URL: en för ett enskilt dokument i Blob Storage. 

> [!NOTE]
> Kodfragmenten i den här guiden använder fjärrformulär som används av URL: er. Om du vill bearbeta lokala formulär dokument i stället, se de relaterade metoderna i [referens dokumentation](/python/api/azure-ai-formrecognizer) och [exempel](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples).

## <a name="recognize-form-content"></a>Identifiera formulär innehåll

Du kan använda formulär igenkänning för att identifiera tabeller, rader och ord i dokument, utan att behöva träna en modell.

Använd metoden för att identifiera innehållet i en fil på en viss URL `begin_recognize_content_from_url` . Det returnerade värdet är en samling `FormPage` objekt: ett för varje sida i det dokument som skickas. Följande kod itererar igenom dessa objekt och skriver ut de extraherade nyckel-och värdeparen och tabell data.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_getcontent)]

> [!TIP]
> Du kan också hämta innehåll från lokala avbildningar. Se [FormRecognizerClient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python) -metoderna, till exempel `begin_recognize_content` . Eller, se exempel koden på [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) för scenarier som involverar lokala avbildningar.

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

## <a name="recognize-receipts"></a>Identifiera kvitton

Det här avsnittet visar hur du identifierar och extraherar vanliga fält från amerikanska kvitton med hjälp av en förtränad kvitto modell. Om du vill känna igen kvitton från en URL använder du- `begin_recognize_receipts_from_url` metoden. 

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_receipts)]

> [!TIP]
> Du kan också identifiera lokala kvitto avbildningar. Se [FormRecognizerClient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python) -metoderna, till exempel `begin_recognize_receipts` . Eller, se exempel koden på [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) för scenarier som involverar lokala avbildningar.

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


## <a name="recognize-business-cards"></a>Identifiera visitkort

#### <a name="version-20"></a>[version 2,0](#tab/ga)

> [!IMPORTANT]
> Den här funktionen är inte tillgänglig i den valda API-versionen.

#### <a name="version-21-preview"></a>[version 2,1 Preview](#tab/preview)

Det här avsnittet visar hur du känner igen och extraherar vanliga fält från engelska visitkort med en förtränad modell. Använd metoden för att identifiera visitkort från en URL `begin_recognize_business_cards_from_url` . 

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart-preview.py?name=snippet_bc)]

> [!TIP]
> Du kan också identifiera lokala visitkorts avbildningar. Se [FormRecognizerClient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python) -metoderna, till exempel `begin_recognize_business_cards` . Eller, se exempel koden på [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) för scenarier som involverar lokala avbildningar.

---

## <a name="recognize-invoices"></a>Identifiera fakturor

#### <a name="version-20"></a>[version 2,0](#tab/ga)

> [!IMPORTANT]
> Den här funktionen är inte tillgänglig i den valda API-versionen.

#### <a name="version-21-preview"></a>[version 2,1 Preview](#tab/preview)

Det här avsnittet visar hur du identifierar och extraherar gemensamma fält från försäljnings fakturor med hjälp av en förtränad modell. Använd-metoden för att identifiera fakturor från en URL `begin_recognize_invoices_from_url` . 

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart-preview.py?name=snippet_invoice)]

> [!TIP]
> Du kan också identifiera lokala faktura avbildningar. Se [FormRecognizerClient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python) -metoderna, till exempel `begin_recognize_invoices` . Eller, se exempel koden på [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) för scenarier som involverar lokala avbildningar.

---

## <a name="train-a-custom-model"></a>Träna en anpassad modell

Det här avsnittet visar hur du tränar en modell med dina egna data. En utbildad modell kan spara strukturerade data som innehåller nyckel-/värde relationerna i det ursprungliga formulär dokumentet. När du har tränat modellen kan du testa och träna den och sedan använda den för att på ett tillförlitligt sätt extrahera data från fler formulär utifrån dina behov.

> [!NOTE]
> Du kan också träna modeller med ett grafiskt användar gränssnitt, t. ex. [formulär tolkens exempel etikett verktyg](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Träna en modell utan etiketter

Träna anpassade modeller för att identifiera alla fält och värden som finns i dina anpassade formulär utan att manuellt märka utbildnings dokumenten.

I följande kod används träna klienten med `begin_training` funktionen för att träna en modell på en specifik uppsättning dokument. Det returnerade `CustomFormModel` objektet innehåller information om formulär typerna som modellen kan identifiera och vilka fält som kan extraheras från varje formulär typ. Följande kod block skriver ut den här informationen till-konsolen.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_train)]


### <a name="output"></a>Utdata

Detta är utdata för en modell som är utbildad med de utbildnings data som är tillgängliga från [python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training).

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

Du kan också träna anpassade modeller genom att manuellt märka utbildnings dokumenten. Utbildning med etiketter leder till bättre prestanda i vissa scenarier. Returnerade `CustomFormModel` anger vilka fält som modellen kan extrahera, tillsammans med dess beräknade noggrannhet i varje fält. Följande kod block skriver ut den här informationen till-konsolen.

> [!IMPORTANT]
> För att träna med etiketter måste du ha särskilda etikett informations filer ( `\<filename\>.pdf.labels.json` ) i din Blob Storage-behållare tillsammans med utbildnings dokumenten. [Formulär tolkens exempel etikett verktyg](../../quickstarts/label-tool.md) innehåller ett användar gränssnitt som hjälper dig att skapa etikettfiler. När du har gjort det kan du anropa- `begin_training` funktionen med parametern *use_training_labels* som har angetts till `true` .

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_trainlabels)]

### <a name="output"></a>Utdata

Detta är utdata för en modell som är utbildad med de utbildnings data som är tillgängliga från [python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms/training).

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

Det här avsnittet visar hur du extraherar nyckel/värde-information och annat innehåll från dina anpassade formulär typer med hjälp av modeller som du har tränat med dina egna formulär.

> [!IMPORTANT]
> För att implementera det här scenariot måste du redan ha tränat en modell så att du kan skicka dess ID till metoden nedan. Se avsnittet [träna en modell](#train-a-model-without-labels) .

Du använder- `begin_recognize_custom_forms_from_url` metoden. Det returnerade värdet är en samling `RecognizedForm` objekt: ett för varje sida i det dokument som skickas. Följande kod skriver ut analys resultaten till-konsolen. Det skriver ut varje identifierat fält och motsvarande värde, tillsammans med en förtroende poäng.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_analyze)]

> [!TIP]
> Du kan också analysera lokala avbildningar. Se [FormRecognizerClient](/python/api/azure-ai-formrecognizer/azure.ai.formrecognizer.formrecognizerclient?view=azure-python) -metoderna, till exempel `begin_recognize_custom_forms` . Eller, se exempel koden på [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples) för scenarier som involverar lokala avbildningar.


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

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Kontrol lera antalet modeller i resurs kontot för FormRecognizer

Följande kod block kontrollerar hur många modeller som du har sparat i ditt formulärs igenkännings konto och jämför dem med konto gränsen.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_count)]


### <a name="output"></a>Utdata

```console
Our account has 5 custom models, and we can have at most 5000 custom models
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Lista de modeller som för närvarande lagras i resurs kontot

Följande kodblock visar de aktuella modellerna i ditt konto och skriver ut information till-konsolen. Den sparar också en referens till den första modellen.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_list)]


### <a name="output"></a>Utdata

Detta är ett exempel på utdata för test kontot.

```console
We have models with the following ids:
453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e
628739de-779c-473d-8214-d35c72d3d4f7
ae636292-0b14-4e26-81a7-a0bfcbaf7c91
b4b5df77-8538-4ffb-a996-f67158ecd305
c6309148-6b64-4fef-aea0-d39521452699
```

### <a name="get-a-specific-model-using-the-models-id"></a>Hämta en speciell modell med modellens ID

Följande kod block använder det modell-ID som sparades från föregående avsnitt och använder det för att hämta information om modellen.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_getmodel)]


### <a name="output"></a>Utdata

Detta är exempel på utdata för den anpassade modell som skapades i föregående exempel.

```console
Model ID: ae636292-0b14-4e26-81a7-a0bfcbaf7c91
Status: ready
Training started on: 2020-08-20 23:20:56+00:00
Training completed on: 2020-08-20 23:20:57+00:00
```

### <a name="delete-a-model-from-the-resource-account"></a>Ta bort en modell från resurs kontot

Du kan också ta bort en modell från ditt konto genom att referera till dess ID. Den här koden tar bort modellen som används i föregående avsnitt.

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerQuickstart.py?name=snippet_manage_delete)]


## <a name="run-the-application"></a>Kör programmet

Kör programmet med `python` kommandot på snabb starts filen.

```console
python quickstart-file.py
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Felsökning

### <a name="general"></a>Allmänt

Formulär identifierarens klient bibliotek kommer att generera undantag som definierats i [Azure Core](https://aka.ms/azsdk-python-azure-core).

### <a name="logging"></a>Loggning

I det här biblioteket används [standard loggnings biblioteket](https://docs.python.org/3/library/logging.html) för loggning. Grundläggande information om HTTP-sessioner (URL: er, rubriker och så vidare) är inloggad på informations nivå.

Detaljerad loggning av fel SÖKNINGs nivå, inklusive instans begär Anden/svar och inte redigerade huvuden, kan aktive ras på en klient med `logging_enable` nyckelords argumentet:

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerLogging.py?name=snippet_logging)]


På samma sätt `logging_enable` kan utförlig loggning aktive ras för en enskild åtgärd, även om den inte är aktive rad för klienten:

[!code-python[](~/cognitive-services-quickstart-code/python/FormRecognizer/FormRecognizerLogging.py?name=snippet_example)]

## <a name="next-steps"></a>Nästa steg

I den här snabb starten har du använt formulär tolkens python-klient bibliotek för att träna modeller och analysera formulär på olika sätt. Nu ska du lära dig hur du skapar en bättre tränings data uppsättning och ger mer exakta modeller.

> [!div class="nextstepaction"]
> [Skapa en träningsdatauppsättning](../../build-training-data-set.md)

* [Vad är formigenkänning?](../../overview.md)
* Exempel koden från den här guiden hittar du på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/python/FormRecognizer/FormRecognizerQuickstart.py).