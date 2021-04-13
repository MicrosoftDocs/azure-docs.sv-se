---
title: Multivarierad för avvikelse detektor i Java Script klient bibliotek snabb start
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/06/2021
ms.author: mbullwin
ms.openlocfilehash: 4e0f2d1bae07f0814b4f096d8be315bd92cd42fe
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107316047"
---
Kom igång med multivarierad klient bibliotek för avvikelse detektor för Java Script. Följ de här stegen för att installera paketet och börja använda de algoritmer som tillhandahålls av tjänsten. De nya API: erna för avvikelse identifiering i multivarierad gör det möjligt för utvecklare att enkelt integrera avancerad AI för att identifiera avvikelser från grupper av mått, utan att det behövs några kunskaper om Machine Learning eller märkta data. Beroenden och Inter-korrelationer mellan olika signaler räknas automatiskt som viktiga faktorer. Detta hjälper dig att proaktivt skydda dina komplexa system från haverier.

Använd klient biblioteket för avvikelse detektor multivarierad för Java Script för att:

* Identifiera avvikelser på system nivå från en grupp tids serier.
* När en enskild tid serie inte meddelar dig mycket och du måste titta på alla signaler för att identifiera ett problem.
* Predicative-underhåll av dyra fysiska till gångar med flera till hundratals olika typer av sensorer som mäter olika aspekter av system hälsan.

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services)
* Den aktuella versionen av [Node.js](https://nodejs.org/)
* När du har en Azure-prenumeration <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" skapar du en avvikelse detektor resurs "  target="_blank"> skapa en avvikelse </a> i den Azure Portal för att hämta nyckel och slut punkt. Vänta tills den har distribuerats och klicka på knappen **gå till resurs** .
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till API: t för avvikelse identifiering. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.

## <a name="setting-up"></a>Konfigurera

### <a name="create-a-new-nodejs-application"></a>Skapa ett nytt Node.js-program

I ett konsol fönster (till exempel cmd, PowerShell eller bash) skapar du en ny katalog för din app och navigerar till den. 

```console
mkdir myapp && cd myapp
```

Kör `npm init` kommandot för att skapa ett Node-program med en `package.json` fil. 

```console
npm init
```

Skapa en fil med namnet `index.js` och importera följande bibliotek:
```javascript
'use strict'

const fs = require('fs');
const parse = require("csv-parse/lib/sync");
const { AnomalyDetectorClient } = require('@azure/ai-anomaly-detector');
const { AzureKeyCredential } = require('@azure/core-auth');
```

Skapa variabler för resursens Azure-slutpunkt och nyckel. Skapa en annan variabel för exempel data filen.

```javascript
const apiKey = "YOUR_API_KEY";
const endpoint = "YOUR_ENDPOINT";
const data_source = "YOUR_SAMPLE_ZIP_FILE_LOCATED_IN_AZURE_BLOB_STORAGE_WITH_SAS";
```

 För att kunna använda multivarierad-API: erna för avvikelse detektor måste vi träna vår egen modell innan identifieringen används. Data som används för utbildning är en batch över tids serier, varje gång serien ska vara i CSV-format med två kolumner, tidsstämpel och värde. Alla tids serier ska vara zippade till en zip-fil och överföras till [Azure Blob Storage](../../../../storage/blobs/storage-blobs-introduction.md). Som standard används fil namnet för att representera variabeln för tids serien. Alternativt kan en extra meta.jsi filen inkluderas i zip-filen om du vill att namnet på variabeln ska skilja sig från. zip-filens namn. När vi genererar [URL: en BLOB SAS (Shared Access Signatures)](../../../../storage/common/storage-sas-overview.md)kan vi använda URL: en till zip-filen för utbildning.

### <a name="install-the-client-library"></a>Installera klient biblioteket

Installera `ms-rest-azure` och `azure-ai-anomalydetector` NPM-paketen. Det CSV-parsande biblioteket används också i den här snabb starten:

```console
npm install @azure/ai-anomaly-detector @azure/ms-rest-js csv-parse
```

Appens `package.json` fil kommer att uppdateras med beroenden.

## <a name="code-examples"></a>Kodexempel

De här kodfragmenten visar hur du gör följande med klient biblioteket för avvikelse detektor för Node.js:

* [Autentisera klienten](#authenticate-the-client)
* [Träna en modell](#train-a-model)
* [Identifiera avvikelser](#detect-anomalies)
* [Exportera modell](#export-model)
* [Ta bort modell](#delete-model)

## <a name="authenticate-the-client"></a>Autentisera klienten

Instansiera ett `AnomalyDetectorClient` objekt med din slut punkt och dina autentiseringsuppgifter.

```javascript
const client = new AnomalyDetectorClient(endpoint, new AzureKeyCredential(apiKey)).client;
```

## <a name="train-a-model"></a>Träna en modell

### <a name="construct-a-model-result"></a>Skapa ett modell resultat

Först måste vi konstruera en modell förfrågan. Kontrol lera att start-och slut tid stämmer med din data källa.

```javascript
const Modelrequest = {
      source: data_source,
      startTime: new Date(2021,0,1,0,0,0),
      endTime: new Date(2021,0,2,12,0,0),
      slidingWindow:200
    };    
```

### <a name="train-a-new-model"></a>Träna en ny modell

Du måste skicka din modell förfrågan till klient metoden för avvikelse detektor `trainMultivariateModel` .

```javascript
console.log("Training a new model...")
var train_response = await client.trainMultivariateModel(Modelrequest)
var model_id = train_response.location.split("/").pop()
console.log("New model ID: " + model_id)
```

För att kontrol lera om din modell är slutförd kan du spåra modellens status:

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

Använd- `detectAnomaly` och- `getDectectionResult` funktionerna för att avgöra om det finns några avvikelser i data källan.

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

Använd-funktionen för att exportera din tränade modell `exportModel` .

```javascript
const export_result = await client.exportModel(model_id)
const model_path = "model.zip"
const destination = fs.createWriteStream(model_path)
export_result.readableStreamBody.pipe(destination)
console.log("New model has been exported to "+model_path+".")
```

## <a name="delete-model"></a>Ta bort modell

Använd funktionen för att ta bort en befintlig modell som är tillgänglig för den aktuella resursen `deleteMultivariateModel` .

```javascript
client.deleteMultivariateModel(model_id)
console.log("New model has been deleted.")
```

## <a name="run-the-application"></a>Kör programmet

Kör programmet med `node` kommandot på snabb starts filen.

```console
node index.js
```

## <a name="next-steps"></a>Nästa steg

* [Metod tips för multivarierad av avvikelse detektor](../../concepts/best-practices-multivariate.md)
