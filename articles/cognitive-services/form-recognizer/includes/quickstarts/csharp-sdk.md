---
title: 'Snabb start: formulär tolkens klient bibliotek för .NET'
description: Använd formulär tolkens klient bibliotek för .NET för att skapa en app för bearbetning av formulär som extraherar nyckel/värde-par och tabell data från dina anpassade dokument.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 10/06/2020
ms.author: pafarley
ms.openlocfilehash: c67a859177695b71c6607c53b3ae9c268aee7797
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/16/2020
ms.locfileid: "97582594"
---
> [!IMPORTANT]
> Koden i den här artikeln använder synkrona metoder och icke-säkrade inloggnings uppgifter för att förenkla orsaker.

[Referens dokumentation](/dotnet/api/overview/azure/ai.formrecognizer-readme)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/src)  |  [Paket (NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer)  |  [Exempel](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)

## <a name="prerequisites"></a>Krav

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services/)
* [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) eller aktuell version av [.net Core](https://dotnet.microsoft.com/download/dotnet-core).
* En Azure Storage-blob som innehåller en uppsättning tränings data. Se [skapa en tränings data uppsättning för en anpassad modell](../../build-training-data-set.md) för tips och alternativ för att sätta samman din tränings data uppsättning. I den här snabb starten kan du använda filerna under mappen **träna** i [exempel data uppsättningen](https://go.microsoft.com/fwlink/?linkid=2090451) (Hämta och extrahera *sample_data.zip*).
* När du har en Azure-prenumeration kan du <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" skapa en formulär igenkännings resurs "  target="_blank"> skapa en formulär igenkännings resurs <span class="docon docon-navigate-external x-hidden-focus"></span> </a> i Azure Portal för att hämta din nyckel och slut punkt. När den har distribuerats klickar **du på gå till resurs**.
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till Forms igenkännings-API: et. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    * Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.

## <a name="setting-up"></a>Konfigurera

I ett konsol fönster (till exempel cmd, PowerShell eller bash) använder du `dotnet new` kommandot för att skapa en ny konsol app med namnet `formrecognizer-quickstart` . Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda käll fil: *program.cs*. 

```console
dotnet new console -n formrecognizer-quickstart
```

Ändra katalogen till mappen nyligen skapade appar. Du kan bygga programmet med:

```console
dotnet build
```

Build-utdata får inte innehålla varningar eller fel. 

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Installera klient biblioteket 

I program katalogen installerar du formulär tolkens klient bibliotek för .NET med följande kommando:

#### <a name="version-20"></a>[version 2,0](#tab/ga)

```console
dotnet add package Azure.AI.FormRecognizer --version 3.0.0
```

> [!NOTE]
> Formulär tolken 3.0.0 SDK reflekterar API-version 2,0

#### <a name="version-21-preview"></a>[version 2,1 Preview](#tab/preview)

```console
dotnet add package Azure.AI.FormRecognizer --version 3.1.0-beta.1
```

> [!NOTE]
> Formulär tolken 3.1.0 SDK visar API version 2,1 Preview

---

> [!TIP]
> Vill du Visa hela snabb starts kod filen samtidigt? Du kan hitta den på [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md), som innehåller kod exemplen i den här snabb starten.

Från projekt katalogen öppnar du *program.cs* -filen i önskat redigerings program eller IDE. Lägg till följande `using` direktiv:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_using)]

I programmets **program** klass skapar du variabler för resursens nyckel och slut punkt.

> [!IMPORTANT]
> Gå till Azure-portalen. Om formulär igenkännings resursen som du skapade i avsnittet **krav** har distribuerats, klickar du på knappen **gå till resurs** under **Nästa steg**. Du hittar din nyckel och slut punkt i resursens **nyckel och slut punkts** sida under **resurs hantering**. 
>
> Kom ihåg att ta bort nyckeln från koden när du är klar och publicera den aldrig offentligt. För produktion bör du överväga att använda ett säkert sätt att lagra och komma åt dina autentiseringsuppgifter. Mer information finns i [säkerhets](../../../cognitive-services-security.md) artikeln Cognitive Services.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_creds)]

I programmets **main** -metod lägger du till ett anrop till de asynkrona uppgifter som används i den här snabb starten. Du kommer att implementera dem senare.

#### <a name="version-20"></a>[version 2,0](#tab/ga)
[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_main)]
#### <a name="version-21-preview"></a>[version 2,1 Preview](#tab/preview)
[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_main)]

---


## <a name="object-model"></a>Objekt modell 

Med formulär tolken kan du skapa två olika klient typer. Det första `FormRecognizerClient` används för att fråga tjänsten om identifierade formulär fält och innehåll. Den andra används `FormTrainingClient` för att skapa och hantera anpassade modeller som du kan använda för att förbättra igenkänningen. 

### <a name="formrecognizerclient"></a>FormRecognizerClient

`FormRecognizerClient` tillhandahåller åtgärder för:

 - Igenkänning av formulär fält och innehåll, med anpassade modeller utbildade för att identifiera dina anpassade formulär.  Dessa värden returneras i en `RecognizedForm` objekt samling. Se exempel [analysera anpassade formulär](#analyze-forms-with-a-custom-model).
 - Igenkänning av formulär innehåll, inklusive tabeller, rader och ord, utan att behöva träna en modell.  Formulär innehåll returneras i en `FormPage` objekt samling. Se exempel på att [identifiera formulär innehåll](#recognize-form-content).
 - Identifiera vanliga fält från USA-kvitton med en förtränad kvitto modell på formulär igenkännings tjänsten. Dessa fält och meta-data returneras i en `RecognizedForm` objekt samling. Se exempel som [identifierar kvitton](#recognize-receipts).

### <a name="formtrainingclient"></a>FormTrainingClient

`FormTrainingClient` tillhandahåller åtgärder för:

- Utbilda anpassade modeller för att identifiera alla fält och värden som finns i dina anpassade formulär.  En `CustomFormModel` returneras som anger vilka formulär som modellen ska identifiera och vilka fält som ska extraheras för varje formulär typ.
- Utbilda anpassade modeller för att identifiera vissa fält och värden som du anger genom att namnge dina anpassade formulär.  En `CustomFormModel` returneras som anger de fält som modellen extraherar, samt den uppskattade noggrannheten för varje fält.
- Hantera modeller som skapats i ditt konto.
- Kopiera en anpassad modell från en formulär igenkännings resurs till en annan.

Se exempel för att [träna en modell](#train-a-custom-model) och [Hantera anpassade modeller](#manage-custom-models).

> [!NOTE]
> Modeller kan också tränas med hjälp av ett grafiskt användar gränssnitt, till exempel [etikett verktyget för formulär igenkänning](../../quickstarts/label-tool.md).

## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du utför följande uppgifter med formulär tolkens klient bibliotek för .NET:

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

Under **main**, skapar du en ny metod med namnet `AuthenticateClient` . Du ska använda detta i andra uppgifter för att autentisera dina begär anden till formulär igenkännings tjänsten. Den här metoden använder `AzureKeyCredential` objektet, så om det behövs kan du uppdatera API-nyckeln utan att skapa nya klient objekt.

> [!IMPORTANT]
> Hämta din nyckel och slut punkt från Azure Portal. Om formulär igenkännings resursen som du skapade i avsnittet **krav** har distribuerats, klickar du på knappen **gå till resurs** under **Nästa steg**. Du hittar din nyckel och slut punkt i resursens **nyckel och slut punkts** sida under **resurs hantering**. 
>
> Kom ihåg att ta bort nyckeln från koden när du är klar och publicera den aldrig offentligt. För produktion bör du överväga att använda ett säkert sätt att lagra och komma åt dina autentiseringsuppgifter. Till exempel [Azure Key Vault](../../../../key-vault/general/overview.md).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_auth)]

Upprepa stegen ovan för en ny metod som autentiserar en utbildnings klient.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_auth_training)]

## <a name="get-assets-for-testing"></a>Få till gångar för testning 

Du måste också lägga till referenser till URL: erna för din utbildning och testa data. Lägg till dessa i roten för **program** klassen.

* [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]
* Upprepa sedan stegen ovan för att hämta SAS-URL: en för ett enskilt dokument i Blob Storage-behållare. Spara den på en tillfällig plats även.
* Spara slutligen URL: en för de exempel bilder som ingår nedan (finns också på [GitHub](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms)). 

#### <a name="version-20"></a>[version 2,0](#tab/ga)
[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_urls)]
#### <a name="version-21-preview"></a>[version 2,1 Preview](#tab/preview)
[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_urls)]

---


## <a name="recognize-form-content"></a>Identifiera formulär innehåll

Du kan använda formulär igenkänning för att identifiera tabeller, rader och ord i dokument, utan att behöva träna en modell. Det returnerade värdet är en samling **FormPage** -objekt: en för varje sida i det dokument som skickas. 

Använd metoden för att identifiera innehållet i en fil på en viss URL `StartRecognizeContentFromUri` .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_getcontent_call)]

> [!TIP]
> Du kan också hämta innehåll från en lokal fil. Se [FormRecognizerClient](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient?view=azure-dotnet) -metoderna, till exempel **StartRecognizeContent**. Eller, se exempel koden på [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) för scenarier som involverar lokala avbildningar.

Resten av aktiviteten skriver ut innehålls informationen till-konsolen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_getcontent_print)]

### <a name="output"></a>Utdata

```console
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    Line 5 has 3 words, and text: '6000 Redmond, WA'.
    Line 6 has 3 words, and text: 'Sunnayvale, CA 87659'.
    Line 7 has 1 word, and text: '99243'.
    Line 8 has 2 words, and text: 'Invoice Number'.
    Line 9 has 2 words, and text: 'Invoice Date'.
    Line 10 has 3 words, and text: 'Invoice Due Date'.
    Line 11 has 1 word, and text: 'Charges'.
    Line 12 has 2 words, and text: 'VAT ID'.
    Line 13 has 1 word, and text: '34278587'.
    Line 14 has 1 word, and text: '6/18/2017'.
    Line 15 has 1 word, and text: '6/24/2017'.
    Line 16 has 1 word, and text: '$56,651.49'.
    Line 17 has 1 word, and text: 'PT'.
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    Cell (0, 3) contains text: 'Charges'.
    Cell (0, 5) contains text: 'VAT ID'.
    Cell (1, 0) contains text: '34278587'.
    Cell (1, 1) contains text: '6/18/2017'.
    Cell (1, 2) contains text: '6/24/2017'.
    Cell (1, 3) contains text: '$56,651.49'.
    Cell (1, 5) contains text: 'PT'.
```

## <a name="recognize-receipts"></a>Identifiera kvitton

Det här avsnittet visar hur du identifierar och extraherar vanliga fält från amerikanska kvitton med hjälp av en förtränad kvitto modell.

Om du vill känna igen kvitton från en URL använder du- `StartRecognizeReceiptsFromUri` metoden. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_receipt_call)]

> [!TIP]
> Du kan också identifiera lokala kvitto avbildningar. Se [FormRecognizerClient](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient?view=azure-dotnet) -metoderna, till exempel **StartRecognizeReceipts**. Eller, se exempel koden på [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) för scenarier som involverar lokala avbildningar.

Det returnerade värdet är en samling `RecognizedReceipt` objekt: ett för varje sida i det dokument som skickas. Följande kod bearbetar kvittot vid den aktuella URI: n och skriver ut de viktigaste fälten och värdena till-konsolen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_receipt_print)]

### <a name="output"></a>Utdata 

```console
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    Line 5 has 3 words, and text: '6000 Redmond, WA'.
    Line 6 has 3 words, and text: 'Sunnayvale, CA 87659'.
    Line 7 has 1 word, and text: '99243'.
    Line 8 has 2 words, and text: 'Invoice Number'.
    Line 9 has 2 words, and text: 'Invoice Date'.
    Line 10 has 3 words, and text: 'Invoice Due Date'.
    Line 11 has 1 word, and text: 'Charges'.
    Line 12 has 2 words, and text: 'VAT ID'.
    Line 13 has 1 word, and text: '34278587'.
    Line 14 has 1 word, and text: '6/18/2017'.
    Line 15 has 1 word, and text: '6/24/2017'.
    Line 16 has 1 word, and text: '$56,651.49'.
    Line 17 has 1 word, and text: 'PT'.
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    Cell (0, 3) contains text: 'Charges'.
    Cell (0, 5) contains text: 'VAT ID'.
    Cell (1, 0) contains text: '34278587'.
    Cell (1, 1) contains text: '6/18/2017'.
    Cell (1, 2) contains text: '6/24/2017'.
    Cell (1, 3) contains text: '$56,651.49'.
    Cell (1, 5) contains text: 'PT'.
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
```

## <a name="recognize-business-cards"></a>Identifiera visitkort

#### <a name="version-20"></a>[version 2,0](#tab/ga)

> [!IMPORTANT]
> Den här funktionen är inte tillgänglig i den valda API-versionen.

#### <a name="version-21-preview"></a>[version 2,1 Preview](#tab/preview)


Det här avsnittet visar hur du känner igen och extraherar vanliga fält från engelska visitkort med en förtränad modell.

Använd metoden för att identifiera visitkort från en URL `StartRecognizeBusinessCardsFromUriAsync` . 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_bc_call)]

> [!TIP]
> Du kan också identifiera lokala kvitto avbildningar. Se [FormRecognizerClient](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient?view=azure-dotnet) -metoderna, till exempel **StartRecognizeBusinessCards**. Eller, se exempel koden på [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) för scenarier som involverar lokala avbildningar.

Det returnerade värdet är en samling `RecognizedForm` objekt: ett för varje kort i dokumentet. Följande kod bearbetar visitkortet vid den aktuella URI: n och skriver ut de viktigaste fälten och värdena till-konsolen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_bc_print)]

---

## <a name="recognize-invoices"></a>Identifiera fakturor

#### <a name="version-20"></a>[version 2,0](#tab/ga)

> [!IMPORTANT]
> Den här funktionen är inte tillgänglig i den valda API-versionen.

#### <a name="version-21-preview"></a>[version 2,1 Preview](#tab/preview)

Det här avsnittet visar hur du identifierar och extraherar gemensamma fält från försäljnings fakturor med hjälp av en förtränad modell.

Använd-metoden för att identifiera fakturor från en URL `StartRecognizeInvoicesFromUriAsync` . 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_invoice_call)]

> [!TIP]
> Du kan också identifiera lokala faktura avbildningar. Se [FormRecognizerClient](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient?view=azure-dotnet) -metoderna, till exempel **StartRecognizeInvoices**. Eller, se exempel koden på [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) för scenarier som involverar lokala avbildningar.

Det returnerade värdet är en samling `RecognizedForm` objekt: ett för varje faktura i det dokument som skickas. Följande kod bearbetar fakturan vid den aktuella URI: n och skriver ut de viktigaste fälten och värdena till-konsolen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_invoice_print)]

---


## <a name="train-a-custom-model"></a>Träna en anpassad modell

Det här avsnittet visar hur du tränar en modell med dina egna data. En utbildad modell kan spara strukturerade data som innehåller nyckel-/värde relationerna i det ursprungliga formulär dokumentet. När du har tränat modellen kan du testa och träna den och sedan använda den för att på ett tillförlitligt sätt extrahera data från fler formulär utifrån dina behov.

> [!NOTE]
> Du kan också träna modeller med ett grafiskt användar gränssnitt, t. ex. [formulär tolkens exempel etikett verktyg](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Träna en modell utan etiketter

Träna anpassade modeller för att identifiera alla fält och värden som finns i dina anpassade formulär utan att manuellt märka utbildnings dokumenten. Följande metod tränar en modell på en specifik uppsättning dokument och skriver ut modellens status till-konsolen. 


[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train)]


Det returnerade `CustomFormModel` objektet innehåller information om formulär typerna som modellen kan identifiera och vilka fält som kan extraheras från varje formulär typ. Följande kod block skriver ut den här informationen till-konsolen.


[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train_response)]

Slutligen returnerar du det utbildade modell-ID: t som används i senare steg.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train_return)]

### <a name="output"></a>Utdata

Det här svaret har trunkerats för läsbarhet.

```console
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    ...
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    Cell (0, 3) contains text: 'Charges'.
    ... 
Custom Model Info:
    Model Id: 95035721-f19d-40eb-8820-0c806b42798b
    Model Status: Ready
    Training model started on: 8/24/2020 6:36:44 PM +00:00
    Training model completed on: 8/24/2020 6:36:50 PM +00:00
Submodel Form Type: form-95035721-f19d-40eb-8820-0c806b42798b
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    ... 
Custom Model Info:
    Model Id: e7a1181b-1fb7-40be-bfbe-1ee154183633
    Model Status: Ready
    Training model started on: 8/24/2020 6:36:44 PM +00:00
    Training model completed on: 8/24/2020 6:36:52 PM +00:00
Submodel Form Type: form-0
    FieldName: field-0, FieldLabel: Additional Notes:
    FieldName: field-1, FieldLabel: Address:
    FieldName: field-2, FieldLabel: Company Name:
    FieldName: field-3, FieldLabel: Company Phone:
    FieldName: field-4, FieldLabel: Dated As:
    FieldName: field-5, FieldLabel: Details
    FieldName: field-6, FieldLabel: Email:
    FieldName: field-7, FieldLabel: Hero Limited
    FieldName: field-8, FieldLabel: Name:
    FieldName: field-9, FieldLabel: Phone:
    ...
```

### <a name="train-a-model-with-labels"></a>Träna en modell med etiketter

Du kan också träna anpassade modeller genom att manuellt märka utbildnings dokumenten. Utbildning med etiketter leder till bättre prestanda i vissa scenarier. För att träna med etiketter måste du ha särskilda etikett informations filer ( `\<filename\>.pdf.labels.json` ) i din Blob Storage-behållare tillsammans med utbildnings dokumenten. [Formulär tolkens exempel etikett verktyg](../../quickstarts/label-tool.md) innehåller ett användar gränssnitt som hjälper dig att skapa etikettfiler. När du har gjort det kan du anropa- `StartTrainingAsync` metoden med- `uselabels` parametern inställd på `true` . 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_trainlabels)]

Returnerade `CustomFormModel` anger vilka fält som modellen kan extrahera, tillsammans med dess beräknade noggrannhet i varje fält. Följande kod block skriver ut den här informationen till-konsolen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_trainlabels_response)]


### <a name="output"></a>Utdata

Det här svaret har trunkerats för läsbarhet.

```console
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    Line 4 has 3 words, and text: '1020 Enterprise Way'.
    Line 5 has 3 words, and text: '6000 Redmond, WA'.
    ...
Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    ... 
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
Custom Model Info:
    Model Id: 63c013e3-1cab-43eb-84b0-f4b20cb9214c
    Model Status: Ready
    Training model started on: 8/24/2020 6:42:54 PM +00:00
    Training model completed on: 8/24/2020 6:43:01 PM +00:00
Submodel Form Type: form-63c013e3-1cab-43eb-84b0-f4b20cb9214c
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    FieldName: DatedAs
    FieldName: Email
    FieldName: Merchant
    ... 
```

## <a name="analyze-forms-with-a-custom-model"></a>Analysera formulär med en anpassad modell

Det här avsnittet visar hur du extraherar nyckel/värde-information och annat innehåll från dina anpassade formulär typer med hjälp av modeller som du har tränat med dina egna formulär.

> [!IMPORTANT]
> För att implementera det här scenariot måste du redan ha tränat en modell så att du kan skicka dess ID till metoden nedan.

Du använder- `StartRecognizeCustomFormsFromUri` metoden. 

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_analyze)]

> [!TIP]
> Du kan också analysera en lokal fil. Se [FormRecognizerClient](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient?view=azure-dotnet) -metoderna, till exempel **StartRecognizeCustomForms**. Eller, se exempel koden på [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) för scenarier som involverar lokala avbildningar.

Det returnerade värdet är en samling `RecognizedForm` objekt: ett för varje sida i det dokument som skickas. Följande kod skriver ut analys resultaten till-konsolen. Det skriver ut varje identifierat fält och motsvarande värde, tillsammans med en förtroende poäng.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_analyze_response)]


### <a name="output"></a>Utdata

Det här svaret har trunkerats för läsbarhet.

```console
Custom Model Info:
    Model Id: 9b0108ee-65c8-450e-b527-bb309d054fc4
    Model Status: Ready
    Training model started on: 8/24/2020 7:00:31 PM +00:00
    Training model completed on: 8/24/2020 7:00:32 PM +00:00
Submodel Form Type: form-9b0108ee-65c8-450e-b527-bb309d054fc4
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    ...
Form Page 1 has 18 lines.
    Line 0 has 1 word, and text: 'Contoso'.
    Line 1 has 1 word, and text: 'Address:'.
    Line 2 has 3 words, and text: 'Invoice For: Microsoft'.
    Line 3 has 4 words, and text: '1 Redmond way Suite'.
    ...

Table 0 has 2 rows and 6 columns.
    Cell (0, 0) contains text: 'Invoice Number'.
    Cell (0, 1) contains text: 'Invoice Date'.
    Cell (0, 2) contains text: 'Invoice Due Date'.
    ...
Merchant Name: 'Contoso Contoso', with confidence 0.516
Transaction Date: '6/10/2019 12:00:00 AM', with confidence 0.985
Item:
    Name: '8GB RAM (Black)', with confidence 0.916
    Total Price: '999', with confidence 0.559
Item:
    Name: 'SurfacePen', with confidence 0.858
    Total Price: '99.99', with confidence 0.386
Total: '1203.39', with confidence '0.774'
Custom Model Info:
    Model Id: dc115156-ce0e-4202-bbe4-7426e7bee756
    Model Status: Ready
    Training model started on: 8/24/2020 7:00:31 PM +00:00
    Training model completed on: 8/24/2020 7:00:41 PM +00:00
Submodel Form Type: form-0
    FieldName: field-0, FieldLabel: Additional Notes:
    FieldName: field-1, FieldLabel: Address:
    FieldName: field-2, FieldLabel: Company Name:
    FieldName: field-3, FieldLabel: Company Phone:
    FieldName: field-4, FieldLabel: Dated As:
    ... 
Form of type: custom:form
Field 'Azure.AI.FormRecognizer.Models.FieldValue:
    Value: '$56,651.49
    Confidence: '0.249
Field 'Azure.AI.FormRecognizer.Models.FieldValue:
    Value: 'PT
    Confidence: '0.245
Field 'Azure.AI.FormRecognizer.Models.FieldValue:
    Value: '99243
    Confidence: '0.114
   ...
```

## <a name="manage-custom-models"></a>Hantera anpassade modeller

Det här avsnittet visar hur du hanterar de anpassade modeller som lagras i ditt konto. Du utför flera åtgärder i följande metod:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage)]


### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Kontrol lera antalet modeller i resurs kontot för FormRecognizer

Följande kod block kontrollerar hur många modeller som du har sparat i ditt formulärs igenkännings konto och jämför dem med konto gränsen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_count)]

### <a name="output"></a>Utdata 

```console
Account has 20 models.
It can have at most 5000 models.
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Lista de modeller som för närvarande lagras i resurs kontot

Följande kodblock visar de aktuella modellerna i ditt konto och skriver ut information till-konsolen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_list)]


### <a name="output"></a>Utdata 

Det här svaret har trunkerats för läsbarhet.

```console
Custom Model Info:
    Model Id: 05932d5a-a2f8-4030-a2ef-4e5ed7112515
    Model Status: Creating
    Training model started on: 8/24/2020 7:35:02 PM +00:00
    Training model completed on: 8/24/2020 7:35:02 PM +00:00
Custom Model Info:
    Model Id: 150828c4-2eb2-487e-a728-60d5d504bd16
    Model Status: Ready
    Training model started on: 8/24/2020 7:33:25 PM +00:00
    Training model completed on: 8/24/2020 7:33:27 PM +00:00
Custom Model Info:
    Model Id: 3303e9de-6cec-4dfb-9e68-36510a6ecbb2
    Model Status: Ready
    Training model started on: 8/24/2020 7:29:27 PM +00:00
    Training model completed on: 8/24/2020 7:29:36 PM +00:00
```

### <a name="get-a-specific-model-using-the-models-id"></a>Hämta en speciell modell med modellens ID

Följande kod blockerar tågen en ny modell (precis som i avsnittet [träna en modell](#train-a-model-without-labels) ) och hämtar sedan en andra referens till den med hjälp av dess ID.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_get)]

### <a name="output"></a>Utdata 

Det här svaret har trunkerats för läsbarhet.

```console
Custom Model Info:
    Model Id: 150828c4-2eb2-487e-a728-60d5d504bd16
    Model Status: Ready
    Training model started on: 8/24/2020 7:33:25 PM +00:00
    Training model completed on: 8/24/2020 7:33:27 PM +00:00
Submodel Form Type: form-150828c4-2eb2-487e-a728-60d5d504bd16
    FieldName: CompanyAddress
    FieldName: CompanyName
    FieldName: CompanyPhoneNumber
    FieldName: DatedAs
    FieldName: Email
    FieldName: Merchant
    FieldName: PhoneNumber
    FieldName: PurchaseOrderNumber
    FieldName: Quantity
    FieldName: Signature
    FieldName: Subtotal
    FieldName: Tax
    FieldName: Total
    FieldName: VendorName
    FieldName: Website
...
```

### <a name="delete-a-model-from-the-resource-account"></a>Ta bort en modell från resurs kontot

Du kan också ta bort en modell från ditt konto genom att referera till dess ID. Det här steget avslutar också metoden.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_delete)]


## <a name="run-the-application"></a>Köra appen

Kör programmet från program katalogen med `dotnet run` kommandot.

```dotnet
dotnet run
```


## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resurs gruppen. Om du tar bort resurs gruppen raderas även andra resurser som är kopplade till den.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Felsökning

När du interagerar med Cognitive Services formulär tolkens klient bibliotek med hjälp av .NET SDK, resulterar felen som returneras av tjänsten i en `RequestFailedException` . De innehåller samma HTTP-statuskod som skulle ha returnerats av en REST API begäran.

Om du till exempel skickar en kvitto avbildning med en ogiltig URI `400` returneras ett fel som anger "felaktig begäran".

```csharp
try
{
    RecognizedReceiptCollection receipts = await client.StartRecognizeReceiptsFromUri(new Uri(receiptUri)).WaitForCompletionAsync();
}
catch (RequestFailedException e)
{
    Console.WriteLine(e.ToString());
}
```

Du ser att ytterligare information, t. ex. klient begär ande-ID för åtgärden, loggas.

```
Message:
    Azure.RequestFailedException: Service request failed.
    Status: 400 (Bad Request)

Content:
    {"error":{"code":"FailedToDownloadImage","innerError":
    {"requestId":"8ca04feb-86db-4552-857c-fde903251518"},
    "message":"Failed to download image from input URL."}}

Headers:
    Transfer-Encoding: chunked
    x-envoy-upstream-service-time: REDACTED
    apim-request-id: REDACTED
    Strict-Transport-Security: REDACTED
    X-Content-Type-Options: REDACTED
    Date: Mon, 20 Apr 2020 22:48:35 GMT
    Content-Type: application/json; charset=utf-8
```

## <a name="next-steps"></a>Nästa steg

I den här snabb starten använde du formulär tolken .NET-klient bibliotek för att träna modeller och analysera formulär på olika sätt. Nu ska du lära dig hur du skapar en bättre tränings data uppsättning och ger mer exakta modeller.

> [!div class="nextstepaction"]
> [Skapa en träningsdatauppsättning](../../build-training-data-set.md)

* [Vad är formigenkänning?](../../overview.md)
* Exempel koden från den här guiden (och mer) finns på [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md).