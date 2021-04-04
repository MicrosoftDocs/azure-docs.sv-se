---
title: Väder prognoser med Azure Machine Learning Studio (klassisk) med IoT Hub data
description: Använd Azure Machine Learning Studio (klassisk) för att förutsäga risken för regn utifrån temperatur-och fuktighets data som din IoT Hub samlar in från en sensor.
author: robinsh
manager: philmea
keywords: Väder prognoser maskin inlärning
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.tgt_pltfrm: arduino
ms.date: 09/16/2020
ms.author: robinsh
ms.openlocfilehash: ab9e122ba0b2b50203a2d66ae14f03f3b6300f96
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96452345"
---
# <a name="weather-forecast-using-the-sensor-data-from-your-iot-hub-in-azure-machine-learning-studio-classic"></a>Väder prognoser med sensor data från din IoT Hub i Azure Machine Learning Studio (klassisk)

![Diagram från slut punkt till slut punkt](media/iot-hub-get-started-e2e-diagram/6.png)

[!INCLUDE [iot-hub-get-started-note](../../includes/iot-hub-get-started-note.md)]

Machine Learning är en teknik för data vetenskap som hjälper datorer att lära sig från befintliga data för att förutse framtida beteenden, resultat och trender. Azure Machine Learning Studio (klassisk) är en moln tjänst för förutsägelse analys som gör det möjligt att snabbt skapa och distribuera förutsägelse modeller som analys lösningar.

## <a name="what-you-learn"></a>Detta får du får lära dig

Du får lära dig hur du använder Azure Machine Learning Studio (klassisk) för att göra väder prognoser (en risk för regn) med hjälp av temperatur-och fuktighets data från din Azure IoT Hub. Risken för regn är resultatet av en beredd väder förutsägelse modell. Modellen bygger på historiska data och prognoserar risken för regn utifrån temperatur och fuktighet.

## <a name="what-you-do"></a>Vad du gör

- Distribuera väder förutsägelse modellen som en webb tjänst.
- Få din IoT Hub redo för data åtkomst genom att lägga till en konsument grupp.
- Skapa ett Stream Analytics jobb och konfigurera jobbet för att:
  - Läsa temperatur-och fuktighets data från din IoT Hub.
  - Ring webb tjänsten för att få en regn chans.
  - Spara resultatet i Azure Blob Storage.
- Använd Microsoft Azure Storage Explorer för att Visa väder prognosen.

## <a name="what-you-need"></a>Detta behöver du

- Slutför själv studie kursen om [Raspberry Pi online Simulator](iot-hub-raspberry-pi-web-simulator-get-started.md) eller någon av enhets självstudierna. till exempel [Raspberry Pi med node.js](iot-hub-raspberry-pi-kit-node-get-started.md). Detta beskriver följande krav:
  - En aktiv Azure-prenumeration.
  - En Azure IoT-hubb under din prenumeration.
  - Ett klient program som skickar meddelanden till Azure IoT Hub.
- Ett [Azure Machine Learning Studio (klassiskt)](https://studio.azureml.net/) konto.
- Ett [Azure Storage konto](../storage/common/storage-account-overview.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#types-of-storage-accounts)är ett **Allmänt-Purpose v2-** konto, men alla Azure Storage-konton som stöder Azure Blob Storage fungerar också.

> [!Note]
> Den här artikeln använder Azure Stream Analytics och flera andra betalda tjänster. Extra kostnader uppkommer i Azure Stream Analytics när data måste överföras i Azure-regioner. Av den anledningen är det bra att se till att din resurs grupp, IoT Hub och Azure Storage-konto – samt den Machine Learning Studio (klassiska) arbets ytan och Azure Stream Analytics jobb läggs till senare i den här självstudien – alla finns i samma Azure-region. Du kan kontrol lera regional support för Azure Machine Learning Studio (klassisk) och andra Azure-tjänster på [sidan för Azure produkt tillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/?products=machine-learning-studio&regions=all).

## <a name="deploy-the-weather-prediction-model-as-a-web-service"></a>Distribuera väder förutsägelse modellen som en webb tjänst

I det här avsnittet får du en väder förutsägelse modell från Azure AI-biblioteket. Sedan lägger du till en R-script-modul i modellen för att rensa temperatur-och fuktighets data. Slutligen distribuerar du modellen som en förutsägbar webb tjänst.

### <a name="get-the-weather-prediction-model"></a>Hämta väder förutsägelse modellen

I det här avsnittet får du en väder förutsägelse modell från Azure AI Gallery och öppnar den i Azure Machine Learning Studio (klassisk).

1. Gå till [sidan för väder förutsägelse modellen](https://gallery.cortanaintelligence.com/Experiment/Weather-prediction-model-1).

   ![Öppna sidan för väder förutsägelse modellen i Azure AI Gallery](media/iot-hub-weather-forecast-machine-learning/weather-prediction-model-in-azure-ai-gallery.png)

1. Välj **Öppna i Studio (klassisk)** för att öppna modellen i Microsoft Azure Machine Learning Studio (klassisk). Välj en region nära IoT-hubben och rätt arbets yta i popup-fönstret **Kopiera experiment från Galleri** .

   ![Öppna väder förutsägelse modellen i Azure Machine Learning Studio (klassisk)](media/iot-hub-weather-forecast-machine-learning/open-ml-studio.png)

### <a name="add-an-r-script-module-to-clean-temperature-and-humidity-data"></a>Lägg till en R-script-modul för ren temperatur och fuktighets data

För att modellen ska fungera korrekt måste temperatur-och fuktighets data konverteras till numeriska data. I det här avsnittet lägger du till en R-script-modul till den väder förutsägelse modell som tar bort alla rader som har data värden för temperatur eller fuktighet som inte kan konverteras till numeriska värden.

1. Klicka på pilen till vänster i fönstret Azure Machine Learning Studio (klassisk) för att expandera panelen verktyg. Ange "kör" i sökrutan. Välj modulen **Kör R-skript** .

   ![Välj Kör R-skript modul](media/iot-hub-weather-forecast-machine-learning/select-r-script-module.png)

1. Dra modulen **Kör r-skript** nära modulen **Rensa data som saknas** och den befintliga **execute r-skript** -modulen i diagrammet. Ta bort anslutningen mellan de **rensade data som saknas** och **Kör R-skriptets** moduler och Anslut sedan indata och utdata för den nya modulen som visas.

   ![Lägg till kör R-skriptkommando](media/iot-hub-weather-forecast-machine-learning/add-r-script-module.png)

1. Välj den nya **Kör R-skript** -modulen för att öppna dess egenskaps fönster. Kopiera och klistra in följande kod i rutan **R-skript** .

   ```r
   # Map 1-based optional input ports to variables
   data <- maml.mapInputPort(1) # class: data.frame

   data$temperature <- as.numeric(as.character(data$temperature))
   data$humidity <- as.numeric(as.character(data$humidity))

   completedata <- data[complete.cases(data), ]

   maml.mapOutputPort('completedata')

   ```

   När du är klar bör fönstret Egenskaper se ut ungefär så här:

   ![Lägg till kod för att köra R-skript-modulen](media/iot-hub-weather-forecast-machine-learning/add-code-to-module.png)

### <a name="deploy-predictive-web-service"></a>Distribuera förutsägbar webb tjänst

I det här avsnittet verifierar du modellen, konfigurerar en förutsägbar webb tjänst baserat på modellen och distribuerar sedan webb tjänsten.

1. Välj **Kör** för att validera stegen i modellen. Det här steget kan ta några minuter att slutföra.

   ![Kör experimentet för att verifiera stegen](media/iot-hub-weather-forecast-machine-learning/run-experiment.png)

1. Välj **Konfigurera** den  >  **förutsägbara webb tjänsten** för webb tjänsten. Det förutsägande experiment diagrammet öppnas.

   ![Distribuera väder förutsägelse modellen i Azure Machine Learning Studio (klassisk)](media/iot-hub-weather-forecast-machine-learning/predictive-experiment.png)

1. I det förutsägande experiment diagrammet tar du bort anslutningen mellan **webb tjänstens indata-** modul och **Välj kolumner i data uppsättningen** längst upp. Dra sedan modulen för **Indataporten för webb tjänsten** någonstans i modulen **Poäng modell** och Anslut den så att den visas:

   ![Anslut två moduler i Azure Machine Learning Studio (klassisk)](media/iot-hub-weather-forecast-machine-learning/connect-modules-azure-machine-learning-studio.png)

1. Välj **Kör** för att validera stegen i modellen.

1. Välj **distribuera webb tjänst** för att distribuera modellen som en webb tjänst.

1. På instrument panelen för modellen laddar du ned **Excel 2010 eller tidigare arbets bok** för **begäran/svar**.

   > [!Note]
   > Se till att du hämtar **excel 2010 eller tidigare arbets bok** även om du kör en senare version av Excel på datorn.

   ![Ladda ned Excel för slut punkten för begär ande svar](media/iot-hub-weather-forecast-machine-learning/download-workbook.png)

1. Öppna Excel-arbetsboken och anteckna **webb tjänstens URL** och **åtkomst nyckeln**.

[!INCLUDE [iot-hub-get-started-create-consumer-group](../../includes/iot-hub-get-started-create-consumer-group.md)]

## <a name="create-configure-and-run-a-stream-analytics-job"></a>Skapa, konfigurera och köra ett Stream Analytics jobb

### <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb

1. I [Azure Portal](https://portal.azure.com/)väljer du **skapa en resurs**. Skriv "Stream Analytics-jobb" i sökrutan och välj **Stream Analytics jobb** i list rutan resultat. När fönstret **Stream Analytics jobb** öppnas väljer du **skapa**.
1. Ange följande information för jobbet.

   **Jobbnamn**: Jobbets namn. Namnet måste vara globalt unikt.

   **Prenumeration**: Välj din prenumeration om den skiljer sig från standardvärdet.

   **Resurs grupp**: Använd samma resurs grupp som din IoT Hub använder.

   **Plats**: Använd samma plats som din resurs grupp.

   Lämna alla andra fält standardvärden.

   ![Skapa ett Stream Analytics jobb i Azure](media/iot-hub-weather-forecast-machine-learning/create-stream-analytics-job.png)

1. Välj **Skapa**.

### <a name="add-an-input-to-the-stream-analytics-job"></a>Lägga till indata till Stream Analytics-jobbet

1. Öppna Stream Analyticss jobbet.
1. Välj **Indata** under **Jobbtopologi**.
1. I rutan **indata** väljer du **Lägg till Stream-indata** och väljer sedan **IoT Hub** i list rutan. I fönstret **nytt indata** väljer du **Välj IoT Hub från dina prenumerationer** och anger följande information:

   **Indataområde**: det unika aliaset för indatamängden.

   **Prenumeration**: Välj din prenumeration om den skiljer sig från standardvärdet.

   **IoT Hub**: Välj IoT Hub från din prenumeration.

   **Princip namn för delad åtkomst**: Välj  **tjänst**. (Du kan också använda **iothubowner**.)

   **Konsument grupp**: Välj den konsument grupp som du skapade.

   Lämna alla andra fält standardvärden.

   ![Lägg till inmatade Stream Analytics jobb i Azure](media/iot-hub-weather-forecast-machine-learning/add-input-stream-analytics-job.png)

1. Välj **Spara**.

### <a name="add-an-output-to-the-stream-analytics-job"></a>Lägga till utdata till Stream Analytics-jobbet

1. Välj **Utdata** under **Jobbtopologi**.
1. I fönstret **utdata** väljer du **Lägg till** och väljer sedan **Blob Storage/data Lake Storage** i list rutan. I fönstret **nytt utdata** väljer du alternativet för att **välja lagring från dina prenumerationer** och anger följande information:

   **Utdataalias**: Utdatas unika alias.

   **Prenumeration**: Välj din prenumeration om den skiljer sig från standardvärdet.

   **Lagrings konto**: lagrings kontot för blob-lagringen. Du kan skapa ett lagrings konto eller använda ett befintligt.

   **Container**: den behållare där blobben sparas. Du kan skapa en behållare eller använda en befintlig.

   **Format för händelse serialisering**: Välj **CSV**.

   ![Lägga till utdata till Stream Analytics jobb i Azure](media/iot-hub-weather-forecast-machine-learning/add-output-stream-analytics-job.png)

1. Välj **Spara**.

### <a name="add-a-function-to-the-stream-analytics-job-to-call-the-web-service-you-deployed"></a>Lägg till en funktion i Stream Analytics jobbet för att anropa den distribuerade webb tjänsten

1. Under **jobb sto pol Ogin** väljer du **Functions**.
1. I rutan **funktioner** väljer du **Lägg till** och väljer sedan **Azure ml Studio** i list rutan. (Se till att du väljer **azure ml Studio**, inte **Azure ml-tjänsten**.) I fönstret **ny funktion** väljer du **ange inställningar för Azure Machine Learning funktion manuellt** och anger följande information:

   **Funktions Ali Aset**: ange `machinelearning` .

   **URL**: Ange webb tjänst-URL: en som du antecknade från Excel-arbetsboken.

   **Nyckel**: Ange den åtkomst nyckel som du antecknade från Excel-arbetsboken.

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

1. Välj **Spara fråga**.

> [!Note]
> Om du väljer **test fråga** visas följande meddelande: fråga testning med Machine Learning funktioner stöds inte. Ändra frågan och försök igen. Du kan ignorera detta meddelande på ett säkert sätt och välja **OK** för att stänga meddelande rutan. Se till att spara frågan innan du fortsätter till nästa avsnitt.

### <a name="run-the-stream-analytics-job"></a>Köra Stream Analytics-jobbet

I Stream Analytics jobb väljer du **Översikt** i det vänstra fönstret. Välj sedan **Starta**  >  **nu**  >  . När jobbet startar ändras jobbstatusen från **Stoppad** till **Körs**.

![Köra Stream Analytics-jobbet](media/iot-hub-weather-forecast-machine-learning/run-stream-analytics-job.png)

## <a name="use-microsoft-azure-storage-explorer-to-view-the-weather-forecast"></a>Använd Microsoft Azure Storage Explorer för att Visa väder prognosen

Kör klient programmet för att börja samla in och skicka temperatur-och fuktighets data till din IoT-hubb. För varje meddelande som IoT-hubben tar emot anropar Stream Analytics-jobbet väder prognosens webb tjänst för att skapa en chans för regn. Resultatet sparas sedan i Azure Blob Storage. Azure Storage Explorer är ett verktyg som du kan använda för att visa resultatet.

1. [Hämta och installera Microsoft Azure Storage Explorer](https://storageexplorer.com/).
1. Öppna Azure Storage Explorer.
1. Logga in på ditt Azure-konto.
1. Välj din prenumeration.
1. Välj din prenumeration > **lagrings konton** > ditt lagrings konto > **Blob-behållare** > din behållare.
1. Hämta en CSV-fil för att se resultatet. Den sista kolumnen registrerar risken för regn.

   ![Få resultat för väder prognoser med Azure Machine Learning Studio (klassisk)](media/iot-hub-weather-forecast-machine-learning/weather-forecast-result.png)

## <a name="summary"></a>Sammanfattning

Du har använt Azure Machine Learning Studio (klassisk) för att skapa en chans att ta regn baserat på den temperatur och de fuktighets data som din IoT hub tar emot.

[!INCLUDE [iot-hub-get-started-next-steps](../../includes/iot-hub-get-started-next-steps.md)]