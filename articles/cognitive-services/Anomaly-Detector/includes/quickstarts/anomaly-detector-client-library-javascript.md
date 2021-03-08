---
title: Snabb start för klient bibliotek för avvikelse detektor
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 09/22/2020
ms.author: mbullwin
ms.custom: devx-track-js
ms.openlocfilehash: ca842a3d235cd58cb12bf9d8d80419e42ddf8441
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2021
ms.locfileid: "102444894"
---
Kom igång med klient biblioteket för avvikelse detektor för Java Script. Följ de här stegen för att installera paket starten med hjälp av algoritmerna som tillhandahålls av tjänsten. Med tjänsten avvikelse detektor kan du hitta avvikelser i dina Time Series-data genom att automatiskt använda de bästa passnings modellerna, oavsett bransch, scenario eller data volym.

Använd klient biblioteket för avvikelse detektor för Java Script för att:

* Identifiera avvikelser i din tids serie data uppsättning som en batch-begäran
* Identifiera avvikelse status för den senaste data punkten i din tids serie
* Identifiera trend ändrings punkter i din data uppsättning.

Dokumentation om biblioteks [referens](https://go.microsoft.com/fwlink/?linkid=2090788)  |  [Biblioteks käll kod](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/AnomalyDetector)  |  [Paket (NPM)](https://www.npmjs.com/package/%40azure/ai-anomaly-detector)  |  [Hitta koden på GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/AnomalyDetector)

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration – [skapa en kostnads fritt](https://azure.microsoft.com/free/cognitive-services)
* Den aktuella versionen av [Node.js](https://nodejs.org/)
* När du har en Azure-prenumeration <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector"  title=" skapar du en avvikelse detektor resurs "  target="_blank"> skapa en avvikelse </a> i den Azure Portal för att hämta nyckel och slut punkt. Vänta tills den har distribuerats och klicka på knappen **gå till resurs** .
    * Du behöver nyckeln och slut punkten från den resurs som du skapar för att ansluta ditt program till API: t för avvikelse identifiering. Du klistrar in nyckeln och slut punkten i koden nedan i snabb starten.
    Du kan använda den kostnads fria pris nivån ( `F0` ) för att testa tjänsten och senare uppgradera till en betald nivå för produktion.

## <a name="setting-up"></a>Konfigurera

[!INCLUDE [anomaly-detector-environment-variables](../environment-variables.md)]

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

[!code-javascript[Import statements](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=imports)]

Skapa variabler för resursens Azure-slutpunkt och nyckel. Om du har skapat miljövariabeln efter att du har startat programmet måste du stänga och öppna redigerings programmet, IDE eller gränssnittet som kör det för att få åtkomst till variabeln. Skapa en annan variabel för exempel data filen som du ska ladda ned i ett senare steg och en tom lista för data punkterna. Skapa sedan ett `ApiKeyCredentials` objekt som innehåller nyckeln.

[!code-javascript[Initial endpoint and key variables](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=vars)]

### <a name="install-the-client-library"></a>Installera klient biblioteket

Installera `ms-rest-azure` och `azure-cognitiveservices-anomalydetector` NPM-paketen. Det CSV-parsande biblioteket används också i den här snabb starten:

```console
npm install @azure/ai-anomaly-detector @azure/ms-rest-js csv-parse
```

Appens `package.json` fil kommer att uppdateras med beroenden.

## <a name="object-model"></a>Objekt modell

Klienten för avvikelse detektor är ett [AnomalyDetectorClient](/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient) -objekt som autentiserar till Azure med hjälp av din nyckel. Klienten kan utföra avvikelse identifiering på en hel data uppsättning med hjälp av [entireDetect ()](/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient#entiredetect-request--servicecallback-entiredetectresponse--)eller den senaste data punkten med [LastDetect ()](/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient#lastdetect-request--msrest-requestoptionsbase-). Metoden [ChangePointDetectAsync](https://go.microsoft.com/fwlink/?linkid=2090788) identifierar punkter som markerar ändringar i en trend. 

Time Series-data skickas som en serie med [punkter](/javascript/api/@azure/cognitiveservices-anomalydetector/point) i ett [Request](/javascript/api/@azure/cognitiveservices-anomalydetector/request) -objekt. `Request`Objektet innehåller egenskaper för att beskriva data (till exempel[granularitet](/javascript/api/@azure/cognitiveservices-anomalydetector/request#granularity) ) och parametrar för avvikelse identifiering. 

Avvikelse detektorns svar är ett [LastDetectResponse](/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse)-, [EntireDetectResponse](/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse)-eller [ChangePointDetectResponse](https://go.microsoft.com/fwlink/?linkid=2090788) -objekt beroende på vilken metod som används. 

## <a name="code-examples"></a>Kodexempel 

De här kodfragmenten visar hur du gör följande med klient biblioteket för avvikelse detektor för Node.js:

* [Autentisera klienten](#authenticate-the-client)
* [Läs in en tids serie data uppsättning från en fil](#load-time-series-data-from-a-file)
* [Identifiera avvikelser i hela data uppsättningen](#detect-anomalies-in-the-entire-data-set) 
* [Identifiera avvikelse statusen för den senaste data punkten](#detect-the-anomaly-status-of-the-latest-data-point)
* [Identifiera ändrings punkterna i data uppsättningen](#detect-change-points-in-the-data-set)

## <a name="authenticate-the-client"></a>Autentisera klienten

Instansiera ett [AnomalyDetectorClient](/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient) -objekt med din slut punkt och dina autentiseringsuppgifter.

[!code-javascript[Authentication](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=authentication)]

## <a name="load-time-series-data-from-a-file"></a>Läsa in tids serie data från en fil

Hämta exempel data för den här snabb starten från [GitHub](https://github.com/Azure-Samples/cognitive-services-quickstart-code/blob/master/javascript/AnomalyDetector/request-data.csv):
1. Högerklicka på **RAW** i webbläsaren.
2. Klicka på **Spara länk som**.
3. Spara filen i program katalogen som en CSV-fil.

Denna tids serie data formateras som en. csv-fil och skickas till API: t för avvikelse identifiering.

Läs data filen med CSV-parsing-bibliotekets `readFileSync()` metod och parsa filen med `parse()` . För varje rad push-överför du ett [punkt](/javascript/api/@azure/cognitiveservices-anomalydetector/point) objekt som innehåller tidsstämpeln och det numeriska värdet.

[!code-javascript[Read the data file](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=readFile)]

## <a name="detect-anomalies-in-the-entire-data-set"></a>Identifiera avvikelser i hela data uppsättningen 

Anropa API: et för att identifiera avvikelser genom hela tids serien som en batch med klientens [entireDetect ()-](/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient#entiredetect-request--msrest-requestoptionsbase-) metod. Lagra det returnerade [EntireDetectResponse](/javascript/api/@azure/cognitiveservices-anomalydetector/entiredetectresponse) -objektet. Upprepa i svars `isAnomaly` listan och skriv ut indexet för alla `true` värden. Dessa värden motsvarar indexet för avvikande data punkter, om sådana hittades.

[!code-javascript[Batch detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=batchCall)]

## <a name="detect-the-anomaly-status-of-the-latest-data-point"></a>Identifiera avvikelse statusen för den senaste data punkten

Anropa API: t för avvikelse detektor för att avgöra om den senaste data punkten är en avvikelse med klientens [lastDetect ()](/javascript/api/@azure/cognitiveservices-anomalydetector/anomalydetectorclient#lastdetect-request--msrest-requestoptionsbase-) -Metod och lagra det returnerade [LastDetectResponse](/javascript/api/@azure/cognitiveservices-anomalydetector/lastdetectresponse) -objektet. Svarets `isAnomaly` värde är ett booleskt värde som anger den punktens avvikelse status.  

[!code-javascript[Last point detection function](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=lastDetection)]

## <a name="detect-change-points-in-the-data-set"></a>Identifiera ändrings punkter i data uppsättningen

Anropa API: et för att identifiera ändrings punkter i tids serien med klientens [detectChangePoint ()-](https://go.microsoft.com/fwlink/?linkid=2090788) metod. Lagra det returnerade [ChangePointDetectResponse](https://go.microsoft.com/fwlink/?linkid=2090788) -objektet. Upprepa i svars `isChangePoint` listan och skriv ut indexet för alla `true` värden. Dessa värden motsvarar indexen för trend ändrings punkter, om de påträffades.

[!code-javascript[detect change points](~/cognitive-services-quickstart-code/javascript/AnomalyDetector/anomaly_detector_quickstart.js?name=changePointDetection)]

## <a name="run-the-application"></a>Kör programmet

Kör programmet med `node` kommandot på snabb starts filen.

```console
node index.js
```

[!INCLUDE [anomaly-detector-next-steps](../quickstart-cleanup-next-steps.md)]
