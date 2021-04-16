---
title: Väderprognos med Azure Machine Learning Studio (klassisk) med IoT Hub data
description: Använd Azure Machine Learning Studio (klassisk) för att förutsäga risken för regn baserat på temperatur- och luftfuktighetsdata som din IoT-hubb samlar in från en sensor.
author: robinsh
manager: philmea
keywords: maskininlärning för väderprognoser
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 09/16/2020
ms.author: robinsh
ms.openlocfilehash: 455d78ed21403952046448dd4447b5ec54f77c00
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107566987"
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning-studio-classic"></a>Väderprognos med sensordata från din IoT-hubb i Azure Machine Learning Studio (klassisk)

![Diagram från slutet till slut](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Maskininlärning är en teknik inom datavetenskap som hjälper datorer att lära sig från befintliga data för att göra prognoser om framtida beteenden, resultat och trender. Azure Machine Learning Studio (klassisk) är en molnbaserad tjänst för förutsägelseanalys som gör det möjligt att snabbt skapa och distribuera förutsägelsemodeller som analyslösningar. I den här artikeln får du lära dig hur du använder Azure Machine Learning Studio (klassisk) för att göra väderprognoser (regnchans) med hjälp av temperatur- och luftfuktighetsdata från Din Azure IoT-hubb. Risken för regn är resultatet av en förberedd modell för väderförutsägelse. Modellen bygger på historiska data för att göra prognoser för regn baserat på temperatur och luftfuktighet.

## <a name="prerequisites"></a>Förutsättningar

- Slutför [självstudien om Raspberry Pi-onlinesimulatorn](iot-hub-raspberry-pi-web-simulator-get-started.md) eller någon av självstudierna om enheten. Du kan till exempel gå till [Raspberry Pi med node.js](iot-hub-raspberry-pi-kit-node-get-started.md) eller till någon av snabbstarterna för att skicka [telemetri.](quickstart-send-telemetry-dotnet.md) De här artiklarna omfattar följande krav:
  - En aktiv Azure-prenumeration.
  - En Azure IoT-hubb under din prenumeration.
  - Ett klientprogram som skickar meddelanden till din Azure IoT-hubb.
- Ett [Azure Machine Learning Studio-konto (klassisk).](https://studio.azureml.net/)
- Ett [Azure Storage konto](../storage/common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-storage-accounts)är ett allmänt **v2-konto** att föredra, men alla Azure Storage som stöder Azure Blob Storage fungerar också.

> [!Note]
> Den här artikeln använder Azure Stream Analytics och flera andra betaltjänster. Extra avgifter debiteras i Azure Stream Analytics data måste överföras mellan Azure-regioner. Därför skulle det vara bra att se till att ditt resursgrupps-, IoT Hub- och Azure Storage-konto , samt arbetsytan Machine Learning Studio (klassisk) och Azure Stream Analytics-jobbet som lades till senare i den här självstudien – finns i samma Azure-region. Du kan kontrollera regional support för Azure Machine Learning Studio (klassisk) och andra Azure-tjänster på [sidan Produkttillgänglighet för Azure per region.](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-studio&regions=all)

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>Distribuera väderförutsägelsemodellen som en webbtjänst

I det här avsnittet får du väderförutsägelsemodellen från Azure AI biblioteket. Sedan lägger du till en R-skriptmodul i modellen för att rensa temperatur- och luftfuktighetsdata. Slutligen distribuerar du modellen som en förutsägelsewebbtjänst.

### <a name="get-the-weather-prediction-model"></a>Hämta väderförutsägelsemodellen

I det här avsnittet får du väderförutsägelsemodellen från Azure AI Gallery och öppnar den i Azure Machine Learning Studio (klassisk).

1. Gå till sidan för [väderförutsägelsemodellen](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1).

   ![Öppna sidan för väderförutsägelsemodellen i Azure AI Gallery](media/iot-hub-weather-forecast-machine-learning/weather-prediction-model-in-azure-ai-gallery.png)

1. Välj **Öppna i Studio (klassisk)** för att öppna modellen i Microsoft Azure Machine Learning Studio (klassisk). Välj en region nära din IoT-hubb och rätt arbetsyta i **popup-menyn Kopiera experiment** från galleri.

   ![Öppna väderförutsägelsemodellen i Azure Machine Learning Studio (klassisk)](media/iot-hub-weather-forecast-machine-learning/open-ml-studio.png)

### <a name="add-an-r-script-module-to-clean-temperature-and-humidity-data"></a>Lägga till en R-skriptmodul för att rensa temperatur- och fuktighetsdata

För att modellen ska fungera korrekt måste temperatur- och fuktighetsdata omvandlas till numeriska data. I det här avsnittet lägger du till en R-skriptmodul i väderförutsägelsemodellen som tar bort alla rader som har datavärden för temperatur eller luftfuktighet som inte kan konverteras till numeriska värden.

1. På vänster sida av fönstret Azure Machine Learning Studio (klassisk) väljer du pilen för att expandera verktygspanelen. Ange "Execute" (Kör) i sökrutan. Välj modulen **Kör R-skript.**

   ![Välj modulen Execute R Script (Kör R-skript)](media/iot-hub-weather-forecast-machine-learning/select-r-script-module.png)

1. Dra modulen **Execute R Script (Kör** R-skript) nära modulen **Clean Missing Data** (Rensa data som saknas) och den befintliga Execute R Script-modulen (Kör **R-skript)** i diagrammet. Ta bort anslutningen mellan **modulerna Clean Missing Data** (Rensa saknade data) och Execute R Script (Kör **R-skript)** och anslut sedan indata och utdata för den nya modulen enligt bilden.

   ![Lägga till modulen Kör R-skript](media/iot-hub-weather-forecast-machine-learning/add-r-script-module.png)

1. Välj den nya **Execute R Script-modulen** (Kör R-skript) för att öppna egenskapsfönstret. Kopiera och klistra in följande kod i **rutan R-skript.**

   ```r
   # Map 1-based optional input ports to variables
   data <- maml.mapInputPort(1) # class: data.frame

   data$temperature <- as.numeric(as.character(data$temperature))
   data$humidity <- as.numeric(as.character(data$humidity))

   completedata <- data[complete.cases(data), ]

   maml.mapOutputPort('completedata')

   ```

   När du är klar bör egenskapsfönstret se ut ungefär så här:

   ![Lägga till kod i modulen Execute R Script (Kör R-skript)](media/iot-hub-weather-forecast-machine-learning/add-code-to-module.png)

### <a name="deploy-predictive-web-service"></a>Distribuera en förutsägelsewebbtjänst

I det här avsnittet ska du verifiera modellen, konfigurera en förutsägelsewebbtjänst baserat på modellen och sedan distribuera webbtjänsten.

1. Välj **Kör** för att verifiera stegen i modellen. Det här steget kan ta några minuter att slutföra.

   ![Kör experimentet för att verifiera stegen](media/iot-hub-weather-forecast-machine-learning/run-experiment.png)

1. Välj **Konfigurera**  >  **webbtjänsten Predictive Web Service**. Diagrammet för förutsägelseexperiment öppnas.

   ![Distribuera väderförutsägelsemodellen i Azure Machine Learning Studio (klassisk)](media/iot-hub-weather-forecast-machine-learning/predictive-experiment.png)

1. I förutsägelseexperimentdiagrammet tar  du bort anslutningen mellan modulen Webbtjänstindata och **Välj kolumner i datauppsättning** längst upp. Dra sedan **indatamodulen webbtjänst** någonstans nära **modulen Poängmodell** och anslut den enligt bilden:

   ![Ansluta två moduler i Azure Machine Learning Studio (klassisk)](media/iot-hub-weather-forecast-machine-learning/connect-modules-azure-machine-learning-studio.png)

1. Välj **KÖR** för att verifiera stegen i modellen.

1. Välj **DISTRIBUERA WEBBTJÄNST för** att distribuera modellen som en webbtjänst.

1. På instrumentpanelen för modellen laddar du ned **Excel 2010** eller tidigare arbetsbok för **BEGÄRAN/SVAR.**

   > [!Note]
   > Se till att du laddar ned **Excel 2010** eller tidigare arbetsbok även om du kör en senare version av Excel på datorn.

   ![Ladda ned Excel för slutpunkten FÖR BEGÄRANDESVAR](media/iot-hub-weather-forecast-machine-learning/download-workbook.png)

1. Öppna Excel-arbetsboken och anteckna **WEBBTJÄNSTENS URL och** **ÅTKOMSTNYCKEL.**

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Skapa, konfigurera och köra ett Stream Analytics jobb

### <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb

1. I [Azure Portal](https://portal.azure.com/)väljer du **Skapa en resurs**. Skriv "stream analytics job" i rutan Sök och välj **Stream Analytics jobb** i resultat-listrutan. När Stream Analytics **öppnas** väljer du **Skapa.**
1. Ange följande information för jobbet.

   **Jobbnamn**: Jobbets namn. Namnet måste vara globalt unikt.

   **Prenumeration:** Välj din prenumeration om den skiljer sig från standardprenumerationen.

   **Resursgrupp:** Använd samma resursgrupp som din IoT Hub använder.

   **Plats:** Använd samma plats som resursgruppen.

   Lämna standardvärdena för alla andra fält.

   ![Skapa ett Stream Analytics jobb i Azure](media/iot-hub-weather-forecast-machine-learning/create-stream-analytics-job.png)

1. Välj **Skapa**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Lägga till indata till Stream Analytics-jobbet

1. Öppna Stream Analytics jobbet.
1. Välj **Indata** under **Jobbtopologi**.
1. I fönstret **Indata** väljer du **Lägg till strömindata** och **IoT Hub** i listrutan. I fönstret **Nya indata** väljer du **Välj IoT Hub från dina prenumerationer** och anger följande information:

   **Indataalias:** Det unika aliaset för indata.

   **Prenumeration:** Välj din prenumeration om den skiljer sig från standardprenumerationen.

   **IoT Hub:** Välj IoT-hubben från din prenumeration.

   **Namn på princip för delad åtkomst:** Välj  **tjänst**. (Du kan också använda **iothubowner**.)

   **Konsumentgrupp:** Välj den konsumentgrupp som du skapade.

   Lämna standardvärdet för alla andra fält.

   ![Lägga till indata i Stream Analytics jobbet i Azure](media/iot-hub-weather-forecast-machine-learning/add-input-stream-analytics-job.png)

1. Välj **Spara**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Lägga till utdata till Stream Analytics-jobbet

1. Välj **Utdata** under **Jobbtopologi**.
1. I fönstret **Utdata** väljer du **Lägg** till och sedan **Blob Storage/Data Lake Storage** i listrutan. I fönstret **Nya utdata** väljer du **Välj lagring från dina prenumerationer** och anger följande information:

   **Utdataalias**: Utdatas unika alias.

   **Prenumeration:** Välj din prenumeration om den skiljer sig från standardprenumerationen.

   **Lagringskonto:** Lagringskontot för din bloblagring. Du kan skapa ett lagringskonto eller använda ett befintligt.

   **Container:** Containern där bloben sparas. Du kan skapa en container eller använda en befintlig.

   **Händelseserialiseringsformat:** Välj **CSV**.

   ![Lägga till utdata till Stream Analytics jobbet i Azure](media/iot-hub-weather-forecast-machine-learning/add-output-stream-analytics-job.png)

1. Välj **Spara**.

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Lägg till en funktion i Stream Analytics för att anropa webbtjänsten som du distribuerade

1. Under **Jobbtopologi** väljer du **Funktioner.**
1. I fönstret **Funktioner** väljer du **Lägg** till och sedan **Azure ML Studio** i listrutan. (Se till att du **väljer Azure ML Studio,** inte **Azure ML Service**.) I fönstret **Ny funktion** väljer du **funktionsinställningarna Ange Azure Machine Learning manuellt** och anger följande information:

   **Funktionsalias:** Ange `machinelearning` .

   **URL:** Ange WEBBTJÄNST-URL:en som du antecknade från Excel-arbetsboken.

   **Nyckel:** Ange den ÅTKOMSTNYCKEL som du antecknade från Excel-arbetsboken.

   ![Lägga till en funktion i Stream Analytics jobbet i Azure](media/iot-hub-weather-forecast-machine-learning/add-function-stream-analytics-job.png)

1. Välj **Spara**.

### <a name="configure-the-query-of-the-stream-analytics-job"></a>Konfigurera frågan för Stream Analytics-jobbet

1. Välj **Fråga** under **Jobbtopologi**.
1. Ersätt den befintliga koden med följande kod:

   ```sql
   WITH machinelearning AS (
      SELECT EventEnqueuedUtcTime, temperature, humidity, machinelearning(temperature, humidity) as result from [YourInputAlias]
   )
   Select System.Timestamp time, CAST (result.[temperature] AS FLOAT) AS temperature, CAST (result.[humidity] AS FLOAT) AS humidity, CAST (result.[scored probabilities] AS FLOAT ) AS 'probabalities of rain'
   Into [YourOutputAlias]
   From machinelearning
   ```

   Ersätt `[YourInputAlias]` med inmatat alias för jobbet.

   Ersätt `[YourOutputAlias]` med utdataalias för jobbet.

1. Välj **Spara fråga.**

> [!Note]
> Om du **väljer Testfråga** visas följande meddelande: Frågetestning med Machine Learning funktioner stöds inte. Ändra frågan och försök igen. Du kan ignorera det här meddelandet och välja **OK** för att stänga meddelanderutan. Se till att spara frågan innan du fortsätter till nästa avsnitt.

### <a name="run-the-stream-analytics-job"></a>Köra Stream Analytics-jobbet

I Stream Analytics väljer du **Översikt** i den vänstra rutan. Välj sedan **Starta**  >  **nu**  >  **Starta.** När jobbet startar ändras jobbstatusen från **Stoppad** till **Körs**.

![Köra Stream Analytics-jobbet](media/iot-hub-weather-forecast-machine-learning/run-stream-analytics-job.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>Använd Microsoft Azure Storage Explorer för att visa väderprognosen

Kör klientprogrammet för att börja samla in och skicka temperatur- och fuktighetsdata till din IoT-hubb. För varje meddelande som din IoT-hubb tar emot anropar Stream Analytics webbtjänsten för väderprognoser för att skapa risk för regn. Resultatet sparas sedan i Azure Blob Storage. Azure Storage Explorer är ett verktyg som du kan använda för att visa resultatet.

1. [Ladda ned och installera Microsoft Azure Storage Explorer](https://storageexplorer.com/).
1. Öppna Azure Storage Explorer.
1. Logga in på ditt Azure-konto.
1. Välj din prenumeration.
1. Välj din prenumeration > **Storage->** ditt lagringskonto > **blobcontainrar** > din container.
1. Ladda ned en CSV-fil för att se resultatet. Den sista kolumnen registrerar risken för regn.

   ![Hämta väderprognosresultat med Azure Machine Learning Studio (klassisk)](media/iot-hub-weather-forecast-machine-learning/weather-forecast-result.png)

## <a name="summary"></a>Sammanfattning

Du har använt Azure Machine Learning Studio (klassisk) för att skapa regnchans baserat på temperatur- och luftfuktighetsdata som din IoT-hubb tar emot.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]