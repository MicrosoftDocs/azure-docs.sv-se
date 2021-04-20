---
title: Avvikelseidentifiering snabbstart för JavaScript-klientbibliotek med fleravarialer
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2021
ms.author: mbullwin
ms.openlocfilehash: 03fbd5e641c72a03a4a3cb19219678bc3d3fff51
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/19/2021
ms.locfileid: "107732601"
---
Kom igång med Avvikelseidentifiering flervariat klientbibliotek för JavaScript. Följ de här stegen för att installera paketet och börja använda de algoritmer som tillhandahålls av tjänsten. Med de nya API:erna för multivarierad avvikelseidentifiering kan utvecklare enkelt integrera avancerad AI för att identifiera avvikelser från grupper med mått, utan att behöva maskininlärning eller märkta data. Beroenden och interkorrelationer mellan olika signaler räknas automatiskt som viktiga faktorer. Detta hjälper dig att proaktivt skydda dina komplexa system mot fel.

Använd Avvikelseidentifiering flervariat klientbibliotek för JavaScript för att:

* Identifiera avvikelser på systemnivå från en grupp med tidsserier.
* När en enskild tidsserie inte berättar så mycket och du måste titta på alla signaler för att identifiera ett problem.
* Predikatiskt underhåll av dyra fysiska tillgångar med tiotals till hundratals olika typer av sensorer som mäter olika aspekter av systemhälsan.

[Bibliotekskällkod](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/anomalydetector/ai-anomaly-detector)  |  [Paket (npm)](https://www.npmjs.com/package/@azure/ai-anomaly-detector)

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration [– Skapa en utan kostnad](https://azure.microsoft.com/free/cognitive-services)
* Den aktuella versionen av [Node.js](https://nodejs.org/)
* När du har din Azure-prenumeration skapar Avvikelseidentifiering en Avvikelseidentifiering resurs i Azure Portal för att <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" hämta din nyckel och "  target="_blank"> </a> slutpunkt. Vänta tills den har distribuerats och klicka **på knappen Gå till** resurs.
    * Du behöver nyckeln och slutpunkten från den resurs som du skapar för att ansluta ditt program till Avvikelseidentifiering-API:et. Du klistrar in nyckeln och slutpunkten i koden nedan senare i snabbstarten.
    Du kan använda den kostnadsfria prisnivån ( `F0` ) för att prova tjänsten och uppgradera senare till en betald nivå för produktion.

## <a name="setting-up"></a>Inrätta

### <a name="create-a-new-nodejs-application"></a>Skapa ett nytt Node.js-program

I ett konsolfönster (till exempel cmd, PowerShell eller Bash) skapar du en ny katalog för din app och navigerar till den. 

```console
mkdir myapp && cd myapp
```

Kör kommandot `npm init` för att skapa ett nodprogram med en `package.json` -fil. 

```console
npm init
```

Skapa en fil med `index.js` namnet och importera följande bibliotek: '
```javascript
'use strict'

const fs = require('fs');
const parse = require("csv-parse/lib/sync");
const { AnomalyDetectorClient } = require('@azure/ai-anomaly-detector');
const { AzureKeyCredential } = require('@azure/core-auth');
```

Skapa variabler för resursens Azure-slutpunkt och nyckel. Skapa en annan variabel för exempeldatafilen.

```javascript
const apiKey = "YOUR_API_KEY";
const endpoint = "YOUR_ENDPOINT";
const data_source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
```

 Om du vill Avvikelseidentifiering flera api:er måste vi träna vår egen modell innan du använder identifiering. Data som används för träning är en batch med tidsserier. Varje tidsserie ska vara i CSV-format med två kolumner, tidsstämpel och värde. Alla tidsserier ska zippas upp i en zip-fil och laddas upp till [Azure Blob Storage.](../../../../storage/blobs/storage-blobs-introduction.md) Som standard används filnamnet för att representera variabeln för tidsserien. Alternativt kan en extra meta.jspå filen inkluderas i zip-filen om du vill att namnet på variabeln ska vara ett annat än ZIP-filnamnet. När vi har [genererat blob-SAS-URL :en (signaturer för delad åtkomst)](../../../../storage/common/storage-sas-overview.md)kan vi använda URL:en till ZIP-filen för träning.

### <a name="install-the-client-library"></a>Installera klientbiblioteket

Installera `ms-rest-azure` `azure-ai-anomalydetector` NPM-paketen och . Csv-parse-biblioteket används också i den här snabbstarten:

```console
npm install @azure/ai-anomaly-detector @azure/ms-rest-js csv-parse
```

Appens `package.json` fil uppdateras med beroendena.

## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du gör följande med Avvikelseidentifiering klientbibliotek för Node.js:

* [Autentisera klienten](#authenticate-the-client)
* [Träna en modell](#train-a-model)
* [Identifiera avvikelser](#detect-anomalies)
* [Exportera modell](#export-model)
* [Ta bort modell](#delete-model)

## <a name="authenticate-the-client"></a>Autentisera klienten

Instansiera ett `AnomalyDetectorClient` objekt med din slutpunkt och dina autentiseringsuppgifter.

```javascript
const client = new AnomalyDetectorClient(endpoint, new AzureKeyCredential(apiKey)).client;
```

## <a name="train-a-model"></a>Träna en modell

### <a name="construct-a-model-result"></a>Skapa ett modellresultat

Först måste vi skapa en modellbegäran. Se till att start- och sluttiden överensstämmer med din datakälla.

```javascript
const Modelrequest = {
      source: data_source,
      startTime: new Date(2021,0,1,0,0,0),
      endTime: new Date(2021,0,2,12,0,0),
      slidingWindow:200
    };    
```

### <a name="train-a-new-model"></a>Träna en ny modell

Du måste skicka din modellbegäran till den Avvikelseidentifiering `trainMultivariateModel` klientmetoden.

```javascript
console.log("Training a new model...")
var train_response = await client.trainMultivariateModel(Modelrequest)
var model_id = train_response.location.split("/").pop()
console.log("New model ID: " + model_id)
```

Om du vill kontrollera om träningen av din modell är klar kan du spåra modellens status:

```javascript
var model_response = await client.getMultivariateModel(model_id)
var model_status = model_response.modelInfo.status

while (model_status != 'READY'){
    await sleep(10000).then(() => {});
    var model_response = await client.getMultivariateModel(model_id)
    var model_status = model_response.modelInfo.status
}

console.log("TRAINING FINISHED.")
```

## <a name="detect-anomalies"></a>Identifiera avvikelser

Använd funktionerna `detectAnomaly` och för att avgöra om det finns några avvikelser i `getDectectionResult` datakällan.

```javascript
console.log("Start detecting...")
const detect_request = {
    source: data_source,
    startTime: new Date(2021,0,2,12,0,0),
    endTime: new Date(2021,0,3,0,0,0)
};
const result_header = await client.detectAnomaly(model_id, detect_request)
const result_id = result_header.location.split("/").pop()
var result = await client.getDetectionResult(result_id)
var result_status = result.summary.status

while (result_status != 'READY'){
    await sleep(2000).then(() => {});
    var result = await client.getDetectionResult(result_id)
    var result_status = result.summary.status
}
```

## <a name="export-model"></a>Exportera modell

Använd funktionen för att exportera den `exportModel` tränade modellen.

```javascript
const export_result = await client.exportModel(model_id)
const model_path = "model.zip"
const destination = fs.createWriteStream(model_path)
export_result.readableStreamBody.pipe(destination)
console.log("New model has been exported to "+model_path+".")
```

## <a name="delete-model"></a>Ta bort modell

Om du vill ta bort en befintlig modell som är tillgänglig för den aktuella resursen använder du `deleteMultivariateModel` funktionen .

```javascript
client.deleteMultivariateModel(model_id)
console.log("New model has been deleted.")
```

## <a name="run-the-application"></a>Kör programmet

Kör programmet med kommandot `node` i snabbstartsfilen.

```console
node index.js
```

## <a name="next-steps"></a>Nästa steg

* [Avvikelseidentifiering metodtips med flera](../../concepts/best-practices-multivariate.md)
