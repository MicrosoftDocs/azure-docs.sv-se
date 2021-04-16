---
title: 'Snabbstart: Formigenkänning klientbibliotek för JavaScript'
description: Använd Formigenkänning-klientbiblioteket för JavaScript för att skapa en formulärbearbetningsapp som extraherar nyckel/värde-par och tabelldata från dina anpassade dokument.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: include
ms.date: 04/14/2021
ms.author: lajanuar
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 7098cfbc2fbe2236687eb7d621a0e587497fcebc
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107516454"
---
<!-- markdownlint-disable MD001 -->
<!-- markdownlint-disable MD024 -->
<!-- markdownlint-disable MD033 -->
<!-- markdownlint-disable MD034 -->
> [!IMPORTANT]
>
> * Koden i den här artikeln använder synkrona metoder och lagring av o säkra autentiseringsuppgifter för enkelhetens skull. Se referensdokumentationen nedan.

[Referensdokumentation](../../index.yml)  |  [Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-js/blob/master/sdk/formrecognizer/ai-form-recognizer/)  |  [Paket (npm)](https://www.npmjs.com/package/@azure/ai-form-recognizer)  |  [Exempel](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples)

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration [– Skapa en kostnadsfritt](https://azure.microsoft.com/free/cognitive-services)
* Den aktuella versionen av [Node.js](https://nodejs.org/)
* En Azure Storage blob som innehåller en uppsättning träningsdata. Se [Skapa en träningsdatauppsättning för en anpassad modell för](../../build-training-data-set.md) tips och alternativ för att sätta ihop din träningsdatauppsättning. I den här snabbstarten kan du använda filerna under mappen **Train** (Träna) i [exempeldatauppsättningen](https://go.microsoft.com/fwlink/?linkid=2090451) (ladda ned och extrahera *sample_data.zip*).
* När du har din Azure-prenumeration skapar Formigenkänning en Formigenkänning resurs i Azure Portal för att <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer"  title=" hämta din nyckel och "  target="_blank"> </a> slutpunkt. När den har distribuerats väljer du **Gå till resurs**.
  * Du behöver nyckeln och slutpunkten från den resurs som du skapar för att ansluta ditt program till Formigenkänning-API:et. Du klistrar in nyckeln och slutpunkten i koden nedan senare i snabbstarten.
  * Du kan använda den kostnadsfria prisnivån ( `F0` ) för att prova tjänsten och senare uppgradera till en betald nivå för produktion.

## <a name="setting-up"></a>Inrätta

### <a name="create-a-new-nodejs-application"></a>Skapa ett nytt Node.js-program

I ett konsolfönster (till exempel cmd, PowerShell eller Bash) skapar du en ny katalog för din app och navigerar till den.

```console
mkdir myapp && cd myapp
```

Kör kommandot `npm init` för att skapa ett nodprogram med en `package.json` fil.

```console
npm init
```

### <a name="install-the-client-library"></a>Installera klientbiblioteket

Installera `ai-form-recognizer` NPM-paketet:

```console
npm install @azure/ai-form-recognizer
```

Appens `package.json` fil uppdateras med beroendena.

Skapa en fil med `index.js` namnet , öppna den och importera följande bibliotek:

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_imports)]

> [!TIP]
> Vill du visa hela snabbstartskodfilen samtidigt? Du hittar den på [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/FormRecognizer/FormRecognizerQuickstart.js), som innehåller kodexe exemplen i den här snabbstarten.

Skapa variabler för resursens Azure-slutpunkt och nyckel.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_creds)]

> [!IMPORTANT]
> Gå till Azure-portalen. Om den Formigenkänning som du skapade i **avsnittet Förutsättningar** har distribuerats klickar du på knappen **Gå till** resurs under **Nästa steg.** Du hittar din nyckel och slutpunkt på resursens nyckel- **och slutpunktssida** under **resurshantering .**
>
> Kom ihåg att ta bort nyckeln från koden när du är klar och aldrig publicera den offentligt. Överväg att använda ett säkert sätt att lagra och komma åt dina autentiseringsuppgifter för produktion. Mer information Cognitive Services artikeln om Cognitive Services [säkerhet.](../../../cognitive-services-security.md)

## <a name="object-model"></a>Objektmodell

Med Formigenkänning kan du skapa två olika klienttyper. Den första används `FormRecognizerClient` för att fråga tjänsten efter identifierade formulärfält och innehåll. Den andra används `FormTrainingClient` för att skapa och hantera anpassade modeller som du kan använda för att förbättra igenkänningen.

### <a name="formrecognizerclient"></a>FormRecognizerClient

`FormRecognizerClient` tillhandahåller åtgärder för:

* Identifiera formulärfält och innehåll med hjälp av anpassade modeller som tränats för att analysera dina anpassade formulär. Dessa värden returneras i en samling `RecognizedForm` objekt.
* Känna igen formulärinnehåll, inklusive tabeller, linjer och ord, utan att behöva träna en modell. Formulärinnehåll returneras i en samling `FormPage` objekt.
* Känna igen vanliga fält från amerikanska kvitton, visitkort, fakturor och identitetsdokument med hjälp av en förtränad modell på Formigenkänning tjänsten.

### <a name="formtrainingclient"></a>FormTrainingClient

`FormTrainingClient` tillhandahåller åtgärder för:

* Träna anpassade modeller för att analysera alla fält och värden som finns i dina anpassade formulär. En `CustomFormModel` returneras som anger de formulärtyper som modellen kommer att analysera och de fält som den extraherar för varje formulärtyp. _Mer_ [information finns i tjänstens dokumentation om omärkt modellträning.](#train-a-model-without-labels)
* Träna anpassade modeller för att analysera specifika fält och värden som du anger genom att märka dina anpassade formulär. En `CustomFormModel` returneras som anger de fält som modellen ska extrahera, samt den uppskattade noggrannheten för varje fält. En mer [detaljerad förklaring av hur du tillämpar etiketter på en träningsdatauppsättning](#train-a-model-with-labels) finns i tjänstens dokumentation om etiketterad modellträning.
* Hantera modeller som skapats i ditt konto.
* Kopiera en anpassad modell från en Formigenkänning resurs till en annan.

> [!NOTE]
> Modeller kan också tränas med ett grafiskt användargränssnitt, till [exempel Formigenkänning Labeling Tool](../../quickstarts/label-tool.md).

## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du utför följande uppgifter med Formigenkänning klientbibliotek för JavaScript:

* [Autentisera klienten](#authenticate-the-client)
* [Analysera layout](#analyze-layout)
* [Analysera kvitton](#analyze-receipts)
* [Analysera visitkort](#analyze-business-cards)
* [Analysera fakturor](#analyze-invoices)
* [Analysera identitetsdokument](#analyze-identity-documents)
* [Träna en anpassad modell](#train-a-custom-model)
* [Analysera formulär med en anpassad modell](#analyze-forms-with-a-custom-model)
* [Hantera dina anpassade modeller](#manage-your-custom-models)

## <a name="authenticate-the-client"></a>Autentisera klienten

Autentisera ett klientobjekt med hjälp av de prenumerationsvariabler som du har definierat. Du använder ett `AzureKeyCredential` -objekt, så att du vid behov kan uppdatera API-nyckeln utan att skapa nya klientobjekt. Du skapar även ett träningsklientobjekt.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_auth)]

## <a name="get-assets-for-testing"></a>Hämta tillgångar för testning

Du måste också lägga till referenser till URL:erna för dina tränings- och testdata.

* [!INCLUDE [get SAS URL](../../includes/sas-instructions.md)]

   :::image type="content" source="../../media/quickstarts/get-sas-url.png" alt-text="HÄMTNING AV SAS-URL":::
* Använd exemplet från och kvittobilderna som ingår i exemplen nedan (finns även på [GitHub)](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/test-assets)eller så kan du använda stegen ovan för att hämta SAS-URL:en för ett enskilt dokument i Blob Storage.

## <a name="analyze-layout"></a>Analysera layout

Du kan använda Formigenkänning för att analysera tabeller, linjer och ord i dokument, utan att behöva träna en modell. Mer information om extrahering av layout finns i [layoutkonceptsguiden](../../concept-layout.md). Om du vill analysera innehållet i en fil vid en viss URI använder du `beginRecognizeContentFromUrl` metoden .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_getcontent)]

> [!TIP]
> Du kan också hämta innehåll från en lokal fil. Se [FormRecognizerClient-metoderna,](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient) till exempel **beginRecognizeContent**. Eller så kan du se exempelkoden på [GitHub för](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) scenarier som involverar lokala bilder.

### <a name="output"></a>Utdata

```console
Page 1: width 8.5 and height 11 with unit inch
cell [0,0] has text Invoice Number
cell [0,1] has text Invoice Date
cell [0,2] has text Invoice Due Date
cell [0,3] has text Charges
cell [0,5] has text VAT ID
cell [1,0] has text 34278587
cell [1,1] has text 6/18/2017
cell [1,2] has text 6/24/2017
cell [1,3] has text $56,651.49
cell [1,5] has text PT
```

## <a name="analyze-receipts"></a>Analysera kvitton

Det här avsnittet visar hur du analyserar och extraherar vanliga fält från amerikanska kvitton med hjälp av en förtränad kvittomodell. Mer information om kvittoanalys finns i [konceptuella kvittoguiden](../../concept-receipts.md).

Om du vill analysera kvitton från en URI använder du `beginRecognizeReceiptsFromUrl` metoden . Följande kod bearbetar ett kvitto på den angivna URI:en och skriver ut de större fälten och värdena till konsolen.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_receipts)]

> [!TIP]
> Du kan också analysera lokala kvittobilder. Se [FormRecognizerClient-metoderna,](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient) till exempel **beginRecognizeReceipts**. Eller så kan du se exempelkoden på [GitHub för](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) scenarier som rör lokala avbildningar.

### <a name="output"></a>Utdata

```console
status: notStarted
status: running
status: succeeded
First receipt:
  Receipt Type: 'Itemized', with confidence of 0.659
  Merchant Name: 'Contoso Contoso', with confidence of 0.516
  Transaction Date: 'Sun Jun 09 2019 17:00:00 GMT-0700 (Pacific Daylight Time)', with confidence of 0.985
    Item Name: '8GB RAM (Black)', with confidence of 0.916
    Item Name: 'SurfacePen', with confidence of 0.858
  Total: '1203.39', with confidence of 0.774
```

## <a name="analyze-business-cards"></a>Analysera visitkort

Det här avsnittet visar hur du analyserar och extraherar vanliga fält från visitkort på engelska med hjälp av en förtränad modell. Mer information om visitkortsanalys finns i den [konceptuella handboken för visitkort.](../../concept-business-cards.md)

Om du vill analysera visitkort från en URL använder du `beginRecognizeBusinessCardsFromURL` metoden .

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js" id="snippet_bc":::

> [!TIP]
> Du kan också analysera lokala visitkortsbilder. Se [FormRecognizerClient-metoderna,](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient) till exempel **beginRecognizeBusinessCards**. Eller så kan du se exempelkoden på [GitHub för](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) scenarier som rör lokala avbildningar.

## <a name="analyze-invoices"></a>Analysera fakturor

Det här avsnittet visar hur du analyserar och extraherar vanliga fält från försäljningsfakturor med hjälp av en förtränad modell. Mer information om fakturaanalys finns i den [konceptuella guiden Faktura.](../../concept-invoices.md)

Om du vill analysera fakturor från en URL använder du `beginRecognizeInvoicesFromUrl` metoden .

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js" id="snippet_invoice":::

> [!TIP]
> Du kan också analysera lokala visitkortsbilder. Se [FormRecognizerClient-metoderna,](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient) till exempel **beginRecognizeInvoices**. Eller så kan du se exempelkoden på [GitHub för](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) scenarier som rör lokala avbildningar.

## <a name="analyze-identity-documents"></a>Analysera identitetsdokument

Det här avsnittet visar hur du analyserar och extraherar viktig information från myndighetsutgivna identifieringsdokument – världsomfattande pass och amerikanska drivrutinslicenser – med hjälp av Formigenkänning fördefinierade ID-modell. Mer information om fakturaanalys finns i vår [fördefinierade begreppsguide för identifieringsmodellen.](../../concept-identification-cards.md)

Om du vill analysera identitetsdokument från en URL använder du `beginRecognizeIdDocumentsFromUrl` metoden .

:::code language="javascript" source="~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js" id="snippet_id":::

## <a name="train-a-custom-model"></a>Träna en anpassad modell

Det här avsnittet visar hur du tränar en modell med dina egna data. En tränad modell kan mata ut strukturerade data som innehåller nyckel/värde-relationerna i det ursprungliga formulärdokumentet. När du har tränat modellen kan du testa och träna om den och så småningom använda den för att extrahera data från flera formulär på ett tillförlitligt sätt utifrån dina behov.

> [!NOTE]
> Du kan också träna modeller med ett grafiskt användargränssnitt, till exempel [Formigenkänning exempeletikettverktyget](../../quickstarts/label-tool.md).

### <a name="train-a-model-without-labels"></a>Träna en modell utan etiketter

Träna anpassade modeller för att analysera alla fält och värden som finns i dina anpassade formulär utan att manuellt märka träningsdokumenten.

Följande funktion tränar en modell på en viss uppsättning dokument och skriver ut modellens status till konsolen.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_train)]

### <a name="output"></a>Utdata

Det här är utdata för en modell som tränats med träningsdata som är tillgängliga från [JavaScript SDK.](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer) Dessa exempelutdata har trunkerats för läsbarhet.

```console
training status: creating
training status: ready
Model ID: 9d893595-1690-4cf2-a4b1-fbac0fb11909
Status: ready
Training started on: Thu Aug 20 2020 20:27:26 GMT-0700 (Pacific Daylight Time)
Training completed on: Thu Aug 20 2020 20:27:37 GMT-0700 (Pacific Daylight Time)
We have recognized the following fields
The model found field 'field-0'
The model found field 'field-1'
The model found field 'field-2'
The model found field 'field-3'
The model found field 'field-4'
The model found field 'field-5'
The model found field 'field-6'
The model found field 'field-7'
...
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors:
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors:
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors:
...
```

### <a name="train-a-model-with-labels"></a>Träna en modell med etiketter

Du kan också träna anpassade modeller genom att manuellt märka träningsdokumenten. Träning med etiketter leder till bättre prestanda i vissa scenarier. Om du vill träna med etiketter måste du ha särskilda etikettinformationsfiler ( `\<filename\>.pdf.labels.json` ) i bloblagringscontainern tillsammans med träningsdokumenten. [Exempeletikettverktyget Formigenkänning ett användargränssnitt](../../quickstarts/label-tool.md) som hjälper dig att skapa etikettfilerna. När du har dem kan du anropa metoden `beginTraining` med `uselabels` parametern inställd på `true` .

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_trainlabels)]

### <a name="output"></a>Utdata

Det här är utdata för en modell som tränats med träningsdata som är tillgängliga från [JavaScript SDK.](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) Dessa exempelutdata har trunkerats för läsbarhet.

```console
training status: creating
training status: ready
Model ID: 789b1b37-4cc3-4e36-8665-9dde68618072
Status: ready
Training started on: Thu Aug 20 2020 20:30:37 GMT-0700 (Pacific Daylight Time)
Training completed on: Thu Aug 20 2020 20:30:43 GMT-0700 (Pacific Daylight Time)
We have recognized the following fields
The model found field 'CompanyAddress'
The model found field 'CompanyName'
The model found field 'CompanyPhoneNumber'
The model found field 'DatedAs'
...
Document name: Form_1.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
Document name: Form_2.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
Document name: Form_3.jpg
Document status: succeeded
Document page count: 1
Document errors: undefined
...
```

## <a name="analyze-forms-with-a-custom-model"></a>Analysera formulär med en anpassad modell

Det här avsnittet visar hur du extraherar nyckel-/värdeinformation och annat innehåll från dina anpassade formulärtyper med hjälp av modeller som du har tränat med dina egna formulär.

> [!IMPORTANT]
> För att kunna implementera det här scenariot måste du redan ha tränat en modell så att du kan skicka dess ID till metoden nedan. Se [avsnittet Träna en](#train-a-model-without-labels) modell.

Du använder `beginRecognizeCustomFormsFromUrl` metoden . Det returnerade värdet är en samling `RecognizedForm` objekt: ett för varje sida i det skickade dokumentet.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_analyze)]

> [!TIP]
> Du kan också analysera lokala filer. Se [FormRecognizerClient-metoderna,](/javascript/api/@azure/ai-form-recognizer/formrecognizerclient) till exempel **beginRecognizeCustomForms**. Eller så kan du se exempelkoden på [GitHub för](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer/samples) scenarier som rör lokala avbildningar.

### <a name="output"></a>Utdata

```console
status: notStarted
status: succeeded
Forms:
custom:form, page range: [object Object]
Pages:
Page number: 1
Tables
cell (0,0) Invoice Number
cell (0,1) Invoice Date
cell (0,2) Invoice Due Date
cell (0,3) Charges
cell (0,5) VAT ID
cell (1,0) 34278587
cell (1,1) 6/18/2017
cell (1,2) 6/24/2017
cell (1,3) $56,651.49
cell (1,5) PT
Fields:
Field Merchant has value 'Invoice For:' with a confidence score of 0.116
Field CompanyPhoneNumber has value '$56,651.49' with a confidence score of 0.249
Field VendorName has value 'Charges' with a confidence score of 0.145
Field CompanyAddress has value '1 Redmond way Suite 6000 Redmond, WA' with a confidence score of 0.258
Field CompanyName has value 'PT' with a confidence score of 0.245
Field Website has value '99243' with a confidence score of 0.114
Field DatedAs has value 'undefined' with a confidence score of undefined
Field Email has value 'undefined' with a confidence score of undefined
Field PhoneNumber has value 'undefined' with a confidence score of undefined
Field PurchaseOrderNumber has value 'undefined' with a confidence score of undefined
Field Quantity has value 'undefined' with a confidence score of undefined
Field Signature has value 'undefined' with a confidence score of undefined
Field Subtotal has value 'undefined' with a confidence score of undefined
Field Tax has value 'undefined' with a confidence score of undefined
Field Total has value 'undefined' with a confidence score of undefined
```

## <a name="manage-your-custom-models"></a>Hantera dina anpassade modeller

Det här avsnittet visar hur du hanterar de anpassade modeller som lagras i ditt konto. Följande kod utför alla modellhanteringsuppgifter i en enda funktion, som exempel.

### <a name="get-number-of-models"></a>Hämta antal modeller

Följande kodblock hämtar antalet modeller som för närvarande finns i ditt konto.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_count)]

### <a name="get-list-of-models-in-account"></a>Hämta lista över modeller i kontot

Följande kodblock innehåller en fullständig lista över tillgängliga modeller i ditt konto, inklusive information om när modellen skapades och dess aktuella status.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_list)]

### <a name="output"></a>Utdata

```console
model 0:
{
  modelId: '453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e',
  status: 'invalid',
  trainingStartedOn: 2020-08-20T22:28:52.000Z,
  trainingCompletedOn: 2020-08-20T22:28:53.000Z
}
model 1:
{
  modelId: '628739de-779c-473d-8214-d35c72d3d4f7',
  status: 'ready',
  trainingStartedOn: 2020-08-20T23:16:51.000Z,
  trainingCompletedOn: 2020-08-20T23:16:59.000Z
}
model 2:
{
  modelId: '789b1b37-4cc3-4e36-8665-9dde68618072',
  status: 'ready',
  trainingStartedOn: 2020-08-21T03:30:37.000Z,
  trainingCompletedOn: 2020-08-21T03:30:43.000Z
}
model 3:
{
  modelId: '9d893595-1690-4cf2-a4b1-fbac0fb11909',
  status: 'ready',
  trainingStartedOn: 2020-08-21T03:27:26.000Z,
  trainingCompletedOn: 2020-08-21T03:27:37.000Z
}
```

### <a name="get-list-of-model-ids-by-page"></a>Hämta lista över modell-ID:er efter sida

Det här kodblocket innehåller en sidnumrerad lista över modeller och modell-ID:er.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_listpages)]

### <a name="output"></a>Utdata

```console
model 1: 453cc2e6-e3eb-4e9f-aab6-e1ac7b87e09e
model 2: 628739de-779c-473d-8214-d35c72d3d4f7
model 3: 789b1b37-4cc3-4e36-8665-9dde68618072
```

### <a name="get-model-by-id"></a>Hämta modell efter ID

Följande funktion tar ett modell-ID och hämtar det matchande modellobjektet. Den här funktionen anropas inte som standard.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_getmodel)]

### <a name="delete-a-model-from-the-resource-account"></a>Ta bort en modell från resurskontot

Du kan också ta bort en modell från ditt konto genom att referera till dess ID. Den här funktionen tar bort modellen med det angivna ID:t. Den här funktionen anropas inte som standard.

[!code-javascript[](~/cognitive-services-quickstart-code/javascript/FormRecognizer/FormRecognizerQuickstart.js?name=snippet_manage_delete)]

### <a name="output"></a>Utdata

```console
Model with id 789b1b37-4cc3-4e36-8665-9dde68618072 has been deleted
```

## <a name="run-the-application"></a>Kör programmet

Kör programmet med kommandot `node` i snabbstartsfilen.

```console
node index.js
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa och ta bort en Cognitive Services prenumeration kan du ta bort resursen eller resursgruppen. Om du tar bort resursgruppen tas även alla andra resurser som är associerade med den bort.

* [Portal](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="troubleshooting"></a>Felsökning

### <a name="enable-logs"></a>Aktivera loggar

Du kan ange följande miljövariabel för att se felsökningsloggar när du använder det här biblioteket.

```console
export DEBUG=azure*
```

Mer detaljerade anvisningar om hur du aktiverar loggar finns i [ @azure/logger paketdokumenten](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/core/logger).

## <a name="next-steps"></a>Nästa steg

I den här snabbstarten använde du Formigenkänning JavaScript-klientbiblioteket för att träna modeller och analysera formulär på olika sätt. Härnäst får du tips om hur du skapar en bättre datauppsättning för träning och skapar mer exakta modeller.

> [!div class="nextstepaction"]
> [Skapa en träningsdatauppsättning](../../build-training-data-set.md)

## <a name="see-also"></a>Se även

* [Vad är formigenkänning?](../../overview.md)
* Exempelkoden från den här guiden finns på [GitHub.](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/FormRecognizer/FormRecognizerQuickstart.js)