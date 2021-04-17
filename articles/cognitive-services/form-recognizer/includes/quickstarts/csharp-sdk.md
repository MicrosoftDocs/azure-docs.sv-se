---
title: 'Snabbstart: Formigenkänning klientbibliotek för .NET'
description: Använd Formigenkänning för .NET för att skapa en formulärbearbetningsapp som extraherar nyckel/värde-par och tabelldata från dina anpassade dokument.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 04/14/2021
ms.author: lajanuar
ms.openlocfilehash: b7a35046a7f170365974c50a5be99f35cb4a868f
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516471"
---
<!-- markdownlint-disable MD024 -->
> [!IMPORTANT]
> Koden i den här artikeln använder synkrona metoder och oskadlig lagring av autentiseringsuppgifter för enkelhetens skull.

[Referensdokumentation](/dotnet/api/overview/azure/ai.formrecognizer-readme)  |  [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/src)  |  [Paket (NuGet)](https://www.nuget.org/packages/Azure.AI.FormRecognizer)  |  [Exempel](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md)

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration [– Skapa en kostnadsfritt](https://azure.microsoft.com/free/cognitive-services/)
* Den [Visual Studio IDE](https://visualstudio.microsoft.com/vs/) eller den aktuella versionen av [.NET Core](https://dotnet.microsoft.com/download/dotnet-core).
* En Azure Storage blob som innehåller en uppsättning träningsdata. Se [Skapa en träningsdatauppsättning för en anpassad modell för](../../build-training-data-set.md) tips och alternativ för att sätta ihop din träningsdatauppsättning. I den här snabbstarten kan du använda filerna under mappen **Train** (Träna) i [exempeldatauppsättningen](https://go.microsoft.com/fwlink/?linkid=2090451) (ladda ned och extrahera *sample_data.zip*).
* När du har din Azure-prenumeration skapar Formigenkänning resurs en Formigenkänning resurs i Azure Portal för att <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" hämta din nyckel och "  target="_blank"> </a> slutpunkt. När den har distribuerats klickar du **på Gå till resurs**.
  * Du behöver nyckeln och slutpunkten från den resurs som du skapar för att ansluta ditt program till Formigenkänning-API:et. Du klistrar in nyckeln och slutpunkten i koden nedan senare i snabbstarten.
  * Du kan använda den kostnadsfria prisnivån ( `F0` ) för att prova tjänsten och senare uppgradera till en betald nivå för produktion.

## <a name="setting-up"></a>Inrätta

I ett konsolfönster (till exempel cmd, PowerShell eller Bash) använder du kommandot för att skapa en `dotnet new` ny konsolapp med namnet `formrecognizer-quickstart` . Det här kommandot skapar ett enkelt "Hello World" C#-projekt med en enda källfil: *program.cs*.

```console
dotnet new console -n formrecognizer-quickstart
```

Ändra katalogen till den nyligen skapade appmappen. Du kan skapa programmet med:

```console
dotnet build
```

Build-utdata får inte innehålla några varningar eller fel.

```console
...
Build succeeded.
 0 Warning(s)
 0 Error(s)
...
```

### <a name="install-the-client-library"></a>Installera klientbiblioteket

I programkatalogen installerar du Formigenkänning för .NET med följande kommando:

#### <a name="v21-preview"></a>[v2.1 förhandsversion](#tab/preview)

```console
dotnet add package Azure.AI.FormRecognizer --version 3.1.0-beta.4
```

> [!NOTE]
> Sdk Formigenkänning 3.1.0-beta.4 återspeglar _API version 2.1-preview.3.

#### <a name="v20"></a>[v2.0](#tab/ga)

```console
dotnet add package Azure.AI.FormRecognizer --version 3.0.0
```

> [!NOTE]
> I Formigenkänning 3.0.0 SDK återspeglas API v2.0

---

> [!TIP]
> Vill du visa hela snabbstartskodfilen samtidigt? Du hittar den på [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md), som innehåller kodexe exemplen i den här snabbstarten.

Från projektkatalogen öppnar du filen *Program.cs* i önskad redigerare eller IDE. Lägg till följande `using` -direktiv:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_using)]

I programmets **Program-klass** skapar du variabler för resursens nyckel och slutpunkt.

> [!IMPORTANT]
> Gå till Azure-portalen. Om den Formigenkänning som du skapade i avsnittet Förutsättningar har **distribuerats** klickar du på **knappen Gå till** resurs under Nästa **steg.** Du hittar din nyckel och slutpunkt på resursens nyckel- **och slutpunktssida** under **resurshantering .**
>
> Kom ihåg att ta bort nyckeln från koden när du är klar och aldrig publicera den offentligt. Överväg att använda ett säkert sätt att lagra och komma åt dina autentiseringsuppgifter för produktion. Se artikeln Cognitive Services [säkerhet](../../../cognitive-services-security.md) för mer information.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_creds)]

I programmets Main-metod **lägger** du till ett anrop till de asynkrona uppgifter som används i den här snabbstarten. Du kommer att implementera dem senare.

#### <a name="v21-preview"></a>[v2.1 förhandsversion](#tab/preview)

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_main)]

#### <a name="v20"></a>[v2.0](#tab/ga)

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_main)]

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

Se exempel för [Träna en modell](#train-a-custom-model) och Hantera anpassade [modeller.](#manage-custom-models)

> [!NOTE]
> Modeller kan också tränas med ett grafiskt användargränssnitt, till exempel [Formigenkänning Labeling Tool](../../quickstarts/label-tool.md).

## <a name="code-examples"></a>Kodexempel

Dessa kodfragment visar hur du utför följande uppgifter med Formigenkänning klientbibliotek för .NET:
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

Under **Main** skapar du en ny metod med namnet `AuthenticateClient` . Du använder detta i andra uppgifter för att autentisera dina begäranden till Formigenkänning tjänsten. Den här metoden använder `AzureKeyCredential` -objektet, så att du vid behov kan uppdatera API-nyckeln utan att skapa nya klientobjekt.

> [!IMPORTANT]
> Hämta din nyckel och slutpunkt från Azure Portal. Om den Formigenkänning som du skapade i **avsnittet** Förutsättningar har distribuerats klickar du på **knappen Gå till** resurs under Nästa **steg.** Du hittar din nyckel och slutpunkt på resursens nyckel- **och slutpunktssida** under **resurshantering .**
>
> Kom ihåg att ta bort nyckeln från koden när du är klar och aldrig publicera den offentligt. Överväg att använda ett säkert sätt att lagra och komma åt dina autentiseringsuppgifter för produktion. Till exempel [Azure-nyckelvalvet](../../../../key-vault/general/overview.md).

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_auth)]

Upprepa stegen ovan för en ny metod som autentiserar en träningsklient.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_auth_training)]

## <a name="get-assets-for-testing"></a>Hämta tillgångar för testning

Du måste också lägga till referenser till URL:erna för dina tränings- och testdata. Lägg till dessa i roten för **din Program-klass.**

* [!INCLUDE [get SAS URL](../sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="HÄMTNING AV SAS-URL":::
* Upprepa sedan stegen ovan för att hämta SAS-URL:en för ett enskilt dokument i bloblagringscontainern. Spara den även på en tillfällig plats.
* Slutligen sparar du URL:en för exempelbilderna som ingår nedan (finns även på [GitHub).](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer/samples/sample_forms)

#### <a name="v21-preview"></a>[v2.1-förhandsgranskning](#tab/preview)

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_urls)]

#### <a name="v20"></a>[v2.0](#tab/ga)

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_urls)]

---

## <a name="analyze-layout"></a>Analysera layout

Du kan använda Formigenkänning för att analysera tabeller, linjer och ord i dokument, utan att behöva träna en modell. Det returnerade värdet är en samling **FormPage-objekt:** en för varje sida i det skickade dokumentet. Mer information om extrahering av layout finns i [layoutkonceptuell guide.](../../concept-layout.md)

Om du vill analysera innehållet i en fil på en viss URL använder du `StartRecognizeContentFromUri` metoden .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_getcontent_call)]

> [!TIP]
> Du kan också hämta innehåll från en lokal fil. Se [FormRecognizerClient-metoderna,](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient) till exempel **StartRecognizeContent.** Eller så kan du se exempelkoden på [GitHub för](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) scenarier som involverar lokala bilder.

Resten av den här uppgiften skriver ut innehållsinformationen till konsolen.

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

## <a name="analyze-receipts"></a>Analysera kvitton

Det här avsnittet visar hur du analyserar och extraherar vanliga fält från amerikanska kvitton med hjälp av en förtränad kvittomodell. Mer information om kvittoanalys finns i [konceptuella kvittoguiden](../../concept-receipts.md).

Om du vill analysera kvitton från en URL använder du `StartRecognizeReceiptsFromUri` metoden .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_receipt_call)]

> [!TIP]
> Du kan också analysera lokala kvittobilder. Se [FormRecognizerClient-metoderna,](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient) till exempel **StartRecognizeReceipts**. Eller så kan du se exempelkoden på [GitHub för](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) scenarier som involverar lokala bilder.

Det returnerade värdet är en samling `RecognizedForm` objekt: ett för varje sida i det skickade dokumentet. Följande kod bearbetar kvittot på den angivna URI:en och skriver ut de större fälten och värdena till konsolen.

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

## <a name="analyze-business-cards"></a>Analysera visitkort

####  <a name="v21-preview"></a>[v2.1 förhandsversion](#tab/preview)

Det här avsnittet visar hur du analyserar och extraherar vanliga fält från engelska visitkort med hjälp av en förtränad modell. Mer information om visitkortsanalys finns i [konceptguiden för visitkort.](../../concept-business-cards.md)

Om du vill analysera visitkort från en URL använder du `StartRecognizeBusinessCardsFromUriAsync` metoden .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_bc_call)]

> [!TIP]
> Du kan också analysera lokala kvittobilder. Se [FormRecognizerClient-metoderna,](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient) till exempel **StartRecognizeBusinessCards.** Eller så kan du se exempelkoden på [GitHub för](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) scenarier som rör lokala avbildningar.

Följande kod bearbetar visitkortet på den angivna URI:en och skriver ut de större fälten och värdena till konsolen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_bc_print)]

#### <a name="v20"></a>[v2.0](#tab/ga)

> [!IMPORTANT]
> Den här funktionen är inte tillgänglig i den valda API-versionen.

---

## <a name="analyze-invoices"></a>Analysera fakturor

#### <a name="v21-preview"></a>[v2.1-förhandsgranskning](#tab/preview)

Det här avsnittet visar hur du analyserar och extraherar gemensamma fält från försäljningsfakturor med hjälp av en förtränad modell. Mer information om fakturaanalys finns i den [konceptuella guiden Faktura.](../../concept-invoices.md)

Om du vill analysera fakturor från en URL använder du `StartRecognizeInvoicesFromUriAsync` metoden .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_invoice_call)]

> [!TIP]
> Du kan också analysera lokala fakturabilder. Se [FormRecognizerClient-metoderna,](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient) till exempel **StartRecognizeInvoices**. Eller så kan du se exempelkoden på [GitHub för](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) scenarier som rör lokala avbildningar.

Följande kod bearbetar fakturan på den angivna URI:en och skriver ut de större fälten och värdena till konsolen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs?name=snippet_invoice_print)]

#### <a name="v20"></a>[v2.0](#tab/ga)

> [!IMPORTANT]
> Den här funktionen är inte tillgänglig i den valda API-versionen.

---

## <a name="analyze-identity-documents"></a>Analysera identitetsdokument

#### <a name="v21-preview"></a>[v2.1-förhandsgranskning](#tab/preview)

Det här avsnittet visar hur du analyserar och extraherar viktig information från myndighetsutgivna identifieringsdokument – världsomfattande pass och amerikanska drivrutinslicenser – med hjälp av Formigenkänning fördefinierade ID-modell. Mer information om identitetsdokumentanalys finns i vår [fördefinierade begreppsguide för identifieringsmodellen.](../../concept-identification-cards.md)

Om du vill analysera identitetsdokument från en URI använder du `StartRecognizeIdDocumentsFromUriAsync` metoden .

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs" id="snippet_id_call":::

> [!TIP]
> Du kan också analysera bilder av lokala identitetsdokument. Se [FormRecognizerClient-metoder,](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient) till exempel **StartRecognizeIdDocumentsAsync.** Se även exempelkoden på [GitHub för scenarier](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) som involverar lokala bilder.

Följande kod bearbetar identitetsdokumentet vid den angivna URI:en och skriver ut huvudfälten och värdena till konsolen.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart-preview.cs" id="snippet_id_print":::

#### <a name="v20"></a>[v2.0](#tab/ga)

> [!IMPORTANT]
> Den här funktionen är inte tillgänglig i den valda API-versionen.

---

## <a name="train-a-custom-model"></a>Träna en anpassad modell

Det här avsnittet visar hur du tränar en modell med dina egna data. En tränad modell kan mata ut strukturerade data som innehåller nyckel/värde-relationerna i det ursprungliga formulärdokumentet. När du har tränat modellen kan du testa och träna om den och så småningom använda den för att extrahera data från flera formulär på ett tillförlitligt sätt utifrån dina behov.

> [!NOTE]
> Du kan också träna modeller med ett grafiskt användargränssnitt, till exempel [Formigenkänning exempeletikettverktyget](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Träna en modell utan etiketter

Träna anpassade modeller för att analysera alla fält och värden som finns i dina anpassade formulär utan att manuellt märka träningsdokumenten. Följande metod tränar en modell på en viss uppsättning dokument och skriver ut modellens status till konsolen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train)]

Det `CustomFormModel` returnerade objektet innehåller information om de formulärtyper som modellen kan analysera och de fält som den kan extrahera från varje formulärtyp. Följande kodblock skriver ut den här informationen till konsolen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_train_response)]

Slutligen returnerar du det tränade modell-ID:t för användning i senare steg.

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

Du kan också träna anpassade modeller genom att manuellt märka träningsdokumenten. Träning med etiketter leder till bättre prestanda i vissa scenarier. Om du vill träna med etiketter måste du ha särskilda etikettinformationsfiler ( `\<filename\>.pdf.labels.json` ) i bloblagringscontainern tillsammans med träningsdokumenten. [Exempeletikettverktyget Formigenkänning ett användargränssnitt](../../quickstarts/label-tool.md) som hjälper dig att skapa etikettfilerna. När du har dem kan du anropa metoden `StartTrainingAsync` med `uselabels` parametern inställd på `true` .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_trainlabels)]

Returnerade `CustomFormModel` anger de fält som modellen kan extrahera, tillsammans med den uppskattade noggrannheten i varje fält. Följande kodblock skriver ut den här informationen till konsolen.

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

Det här avsnittet visar hur du extraherar nyckel-/värdeinformation och annat innehåll från dina anpassade formulärtyper med hjälp av modeller som du har tränat med dina egna formulär.

> [!IMPORTANT]
> För att kunna implementera det här scenariot måste du redan ha tränat en modell så att du kan skicka dess ID till metoden nedan.

Du använder `StartRecognizeCustomFormsFromUri` metoden .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_analyze)]

> [!TIP]
> Du kan också analysera en lokal fil. Se [FormRecognizerClient-metoderna,](/dotnet/api/azure.ai.formrecognizer.formrecognizerclient) till exempel **StartRecognizeCustomForms.** Eller så kan du se exempelkoden på [GitHub för](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md) scenarier som rör lokala avbildningar.

Det returnerade värdet är en samling `RecognizedForm` objekt: ett för varje sida i det skickade dokumentet. Följande kod skriver ut analysresultaten till konsolen. Den skriver ut varje identifierat fält och motsvarande värde, tillsammans med en förtroendepoäng.

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

Det här avsnittet visar hur du hanterar de anpassade modeller som lagras i ditt konto. Du kommer att göra flera åtgärder i följande metod:

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage)]

### <a name="check-the-number-of-models-in-the-formrecognizer-resource-account"></a>Kontrollera antalet modeller i FormRecognizer-resurskontot

Följande kodblock kontrollerar hur många modeller du har sparat i ditt Formigenkänning konto och jämför det med kontogränsen.

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_count)]

### <a name="output"></a>Utdata

```console
Account has 20 models.
It can have at most 5000 models.
```

### <a name="list-the-models-currently-stored-in-the-resource-account"></a>Visa en lista över de modeller som för närvarande lagras i resurskontot

Följande kodblock visar en lista över aktuella modeller i ditt konto och skriver ut deras information till konsolen.

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

### <a name="get-a-specific-model-using-the-models-id"></a>Hämta en specifik modell med hjälp av modellens ID

Följande kodblock tränar en ny modell (precis som i avsnittet [Träna](#train-a-model-without-labels) en modell) och hämtar sedan en andra referens till den med hjälp av dess ID.

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

### <a name="delete-a-model-from-the-resource-account"></a>Ta bort en modell från resurskontot

Du kan också ta bort en modell från ditt konto genom att referera till dess ID. Det här steget stänger även metoden .

[!code-csharp[](~/cognitive-services-quickstart-code/dotnet/FormRecognizer/FormRecognizerQuickstart.cs?name=snippet_manage_model_delete)]

## <a name="run-the-application"></a>Kör programmet

Kör programmet från programkatalogen med `dotnet run` kommandot .

```dotnet
dotnet run
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resursgruppen. Om du tar bort resursgruppen tas även alla andra resurser som är associerade med den bort.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Felsökning

När du interagerar Cognitive Services Formigenkänning klientbiblioteket med hjälp av .NET SDK resulterar fel som returneras av tjänsten i `RequestFailedException` en . De innehåller samma HTTP-statuskod som skulle ha returnerats av en REST API begäran.

Om du till exempel skickar en kvittobild med en ogiltig URI returneras ett fel `400` som anger "Felaktig begäran".

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

Du ser att ytterligare information, t.ex. klientens begärande-ID för åtgärden, loggas.

``

Meddelande: Azure.RequestFailedException: Tjänstbegäran misslyckades.
Status: 400 (felaktig begäran)

Innehåll: {"error":{"code":"FailedToDownloadImage","innerError": {"requestId":"8ca04feb-86db-4552-857c-fde903251518"}, "message":"Failed to download image from input URL."}}

Huvuden: Transfer-Encoding: chunked x-envoy-upstream-service-time: REDACTED apim-request-id: REDACTED Strict-Transport-Security: REDACTED X-Content-Type-Options: REDACTED Date: Mon, 20 Apr 2020 22:48:35 GMT Content-Type: application/json; charset=utf-8 ''

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten använde du Formigenkänning .NET-klientbiblioteket för att träna modeller och analysera formulär på olika sätt. Härnäst får du tips om hur du skapar en bättre datauppsättning för träning och skapar mer exakta modeller.

> [!div class="nextstepaction"]
> [Skapa en träningsdatauppsättning](../../build-training-data-set.md)

* [Vad är formigenkänning?](../../overview.md)
* Exempelkoden från den här guiden (med mera) finns på [GitHub](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer/samples/README.md).
