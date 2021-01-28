---
title: 'Självstudie: bedömnings guide för Machine Learning-modell för dedikerade SQL-pooler'
description: Själv studie kurs om hur du använder bedömnings guiden för Machine Learning-modeller för att utöka data i dedikerade SQL-pooler.
services: synapse-analytics
ms.service: synapse-analytics
ms.subservice: machine-learning
ms.topic: tutorial
ms.reviewer: jrasnick, garye
ms.date: 09/25/2020
author: nelgson
ms.author: negust
ms.openlocfilehash: d8db9257ad6eed98b39cd2c9a52351f013453365
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98935236"
---
# <a name="tutorial-machine-learning-model-scoring-wizard-preview-for-dedicated-sql-pools"></a>Självstudie: guiden bedömnings modell för Machine Learning (för hands version) för dedikerade SQL-pooler

Lär dig hur du enkelt kan utöka dina data i dedikerade SQL-pooler med förutsägande Machine Learning-modeller. De modeller som dina data experter skapar är nu lättillgängliga för data experter för förutsägelse analys. En data Professional i Azure Synapse Analytics kan helt enkelt välja en modell från Azure Machine Learning Model-registret för distribution i Azure Synapse SQL-pooler och starta förutsägelser för att utöka data.

I den här självstudien får du lära dig att:

> [!div class="checklist"]
> - Träna en förutsägelse maskin inlärnings modell och registrera modellen i Azure Machine Learning Model-registret.
> - Använd guiden SQL-poängsättning för att starta förutsägelser i en dedikerad SQL-pool.

Om du inte har en Azure-prenumeration kan du [skapa ett kostnads fritt konto innan du börjar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Förutsättningar

- [Azure Synapse Analytics-arbetsyta](../get-started-create-workspace.md) med ett Azure Data Lake Storage Gen2 lagrings konto konfigurerat som standard lagring. Du måste vara *data deltagare i Storage BLOB* för det data Lake Storage Gen2 fil system som du arbetar med.
- Dedikerad SQL-pool i din Azure Synapse Analytics-arbetsyta. Mer information finns i [skapa en dedikerad SQL-pool](../quickstart-create-sql-pool-studio.md).
- Azure Machine Learning länkad tjänst i din Azure Synapse Analytics-arbetsyta. Mer information finns i [skapa en Azure Machine Learning länkad tjänst i Azure DataSynapses](quickstart-integrate-azure-machine-learning.md).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="train-a-model-in-azure-machine-learning"></a>Träna en modell i Azure Machine Learning

Innan du börjar ska du kontrol lera att din version av sklearn är 0.20.3.

Innan du kör alla celler i antecknings boken kontrollerar du att beräknings instansen körs.

![Skärm bild som visar verifiering av Azure Machine Learning Compute.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00b.png)

1. Gå till din Azure Machine Learning-arbetsyta.

1. Ladda ned [predict NYC taxi-tips. ipynb](https://go.microsoft.com/fwlink/?linkid=2144301).

1. Öppna arbets ytan Azure Machine Learning i [Azure Machine Learning Studio](https://ml.azure.com).

1. Gå till **antecknings böcker**  >  **Ladda upp filer**. Välj sedan filen **predict NYC taxi-tips. ipynb** som du laddade ned och ladda upp den.
   ![Skärm bild av knappen för att ladda upp en fil.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00a.png)

1. När antecknings boken har laddats upp och öppnats väljer du **Kör alla celler**.

   En av cellerna kan Miss varnas och be dig att autentisera till Azure. Titta på det här i cellens utdata och autentisera i webbläsaren genom att följa länken och ange koden. Kör sedan antecknings boken igen.

1. Den bärbara datorn kommer att träna en ONNX-modell och registrera den med MLflow. Gå till **modeller** för att kontrol lera att den nya modellen är korrekt registrerad.
   ![Skärm bild som visar modellen i registret.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00c.png)

1. Genom att köra antecknings boken exporteras även test data till en CSV-fil. Ladda ned CSV-filen till det lokala systemet. Senare importerar du CSV-filen till en dedikerad SQL-pool och använder data för att testa modellen.

   CSV-filen skapas i samma mapp som din Notebook-fil. Välj **Uppdatera** i Utforskaren om du inte vill se det direkt.

   ![Skärm bild som visar C S V-filen.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-train-00d.png)

## <a name="launch-predictions-with-the-sql-scoring-wizard"></a>Starta förutsägelser med guiden SQL-beräkning

1. Öppna Azure dataSynapses-arbetsytan med Synapse Studio.

1. Gå till **data**  >  **länkade**  >  **lagrings konton**. Överför `test_data.csv` till standard lagrings kontot.

   ![Skärm bild som visar markeringar för att ladda upp data.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00a.png)

1. Gå till **utveckla**  >  **SQL-skript**. Skapa ett nytt SQL-skript som ska läsas in `test_data.csv` i den dedikerade SQL-poolen.

   > [!NOTE]
   > Uppdatera fil-URL: en i det här skriptet innan du kör den.

   ```SQL
   IF NOT EXISTS (SELECT * FROM sys.objects WHERE NAME = 'nyc_taxi' AND TYPE = 'U')
   CREATE TABLE dbo.nyc_taxi
   (
       tipped int,
       fareAmount float,
       paymentType int,
       passengerCount int,
       tripDistance float,
       tripTimeSecs bigint,
       pickupTimeBin nvarchar(30)
   )
   WITH
   (
       DISTRIBUTION = ROUND_ROBIN,
       CLUSTERED COLUMNSTORE INDEX
   )
   GO
   
   COPY INTO dbo.nyc_taxi
   (tipped 1, fareAmount 2, paymentType 3, passengerCount 4, tripDistance 5, tripTimeSecs 6, pickupTimeBin 7)
   FROM '<URL to linked storage account>/test_data.csv'
   WITH
   (
       FILE_TYPE = 'CSV',
       ROWTERMINATOR='0x0A',
       FIELDQUOTE = '"',
       FIELDTERMINATOR = ',',
       FIRSTROW = 2
   )
   GO
   
   SELECT TOP 100 * FROM nyc_taxi
   GO
   ```

   ![Läs in data till dedikerad SQL-pool](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00b.png)

1. Gå till **data**  >  **arbets ytan**. Öppna guiden SQL-poängsättning genom att högerklicka på den dedikerade tabellen för SQL-pool. Välj **Machine Learning**  >  **utöka med befintlig modell**.

   > [!NOTE]
   > Alternativet Machine Learning visas inte om du inte har skapat någon länkad tjänst för Azure Machine Learning. (Se [krav](#prerequisites) i början av den här självstudien.)

   ![Skärm bild som visar alternativet Machine Learning.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00c.png)

1. Välj en länkad Azure Machine Learning arbets yta i list rutan. Det här steget läser in en lista över Machine Learning-modeller från modell registret på den valda Azure Machine Learning-arbetsytan. För närvarande stöds endast ONNX-modeller, så det här steget visar bara ONNX-modeller.

1. Välj den modell som du precis har tränat och välj sedan **Fortsätt**.

   ![Skärm bild som visar hur du väljer Azure Machine Learning modellen.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00d.png)

1. Mappa tabell kolumnerna till modellens indata och ange modellens utdata. Om modellen sparas i MLflow-format och Model-signaturen är ifylld görs mappningen automatiskt åt dig genom att använda en logik baserat på liknande namn. Gränssnittet stöder även manuell mappning.

   Välj **Fortsätt**.

   ![Skärm bild som visar mappning mellan tabeller och modeller.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00e.png)

1. Den genererade T-SQL-koden omsluts i en lagrad procedur. Därför måste du ange ett namn på en lagrad procedur. Den binära modellen, inklusive metadata (version, beskrivning och annan information) kopieras fysiskt från Azure Machine Learning till en dedikerad SQL-adresspool. Du måste ange vilken tabell som modellen ska sparas i. 

   Du kan välja antingen **befintlig tabell** eller **Skapa ny**. När du är klar väljer du **distribuera modell + öppna skript** för att distribuera modellen och generera ett T-SQL förutsägelse skript.

   ![Skärm bild som visar markeringar för att skapa en lagrad procedur.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00f.png)

1. När skriptet har skapats väljer du **Kör** för att köra poängsättningen och hämta förutsägelser.

   ![Skärm bild som visar poängsättning och förutsägelser.](media/tutorial-sql-pool-model-scoring-wizard/tutorial-sql-scoring-wizard-00g.png)

## <a name="next-steps"></a>Nästa steg

- [Snabb start: skapa en ny Azure Machine Learning länkad tjänst i Azure Synapse](quickstart-integrate-azure-machine-learning.md)
- [Machine Learning funktioner i Azure Synapse Analytics](what-is-machine-learning.md)
