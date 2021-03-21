---
title: 'Självstudie: träna en modell i python med automatisk maskin inlärning'
description: Självstudie om hur du tränar en Machine Learning-modell i python med hjälp av Apache Spark och automatiserad maskin inlärning.
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: machine-learning
ms.date: 06/30/2020
ms.author: midesa
ms.reviewer: jrasnick
ms.openlocfilehash: 89309cfe427183d594a5cc2f76332ae150d4f803
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102498684"
---
# <a name="tutorial-train-a-model-in-python-with-automated-machine-learning"></a>Självstudie: träna en modell i python med automatisk maskin inlärning

Azure Machine Learning är en molnbaserad miljö som gör det möjligt att träna, distribuera, automatisera, hantera och spåra maskin inlärnings modeller. 

I den här självstudien använder du [Automatisk maskin inlärning](../../machine-learning/concept-automated-ml.md) i Azure Machine Learning för att skapa en Regressions modell för att förutsäga priset för taxi priset. Den här processen kommer till den bästa modellen genom att acceptera inlärnings data och konfigurations inställningar och automatiskt söka igenom kombinationer av olika metoder, modeller och inställningar för en valfri parameter.

I den här guiden får du lära dig att:
- Hämta data med hjälp av Apache Spark och öppna data uppsättningar i Azure.
- Transformera och rensa data med Apache Spark DataFrames.
- Träna en Regressions modell i automatiserad maskin inlärning.
- Beräkna modell noggrannhet.

## <a name="before-you-begin"></a>Innan du börjar

- Skapa en server lös Apache Spark pool genom att följa snabb starten för att [skapa en server lös Apache Spark pool](../quickstart-create-apache-spark-pool-studio.md) .
- Slutför [installations guiden för Azure Machine Learning arbets yta](../../machine-learning/tutorial-1st-experiment-sdk-setup.md) om du inte har en befintlig Azure Machine Learning arbets yta. 

## <a name="understand-regression-models"></a>Förstå Regressions modeller

*Regressions modeller* förutsäger numeriska utmatnings värden baserat på oberoende förförutsägelser. Syftet med regressionen är att hjälpa till att upprätta relationen mellan de oberoende förutsägande variablerna genom att uppskatta hur en variabel påverkar de andra.  

### <a name="example-based-on-new-york-city-taxi-data"></a>Exempel baserat på New York taxi-data för Göteborg

I det här exemplet ska du använda Spark för att utföra en del analys av information om taxi resor från New York City (NYC). Data är tillgängliga via [Azure Open data uppsättningar](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Den här del mängden av data uppsättningen innehåller information om gula taxi resor, inklusive information om varje resa, start-och slut tid samt platser och kostnad.

> [!IMPORTANT]
> Det kan finnas ytterligare avgifter för att hämta dessa data från dess lagrings plats. I följande steg utvecklar du en modell för att förutsäga priserna för NYC Taxi pris. 

##  <a name="download-and-prepare-the-data"></a>Hämta och förbereda data

Gör så här:

1. Skapa en bärbar dator med hjälp av PySpark-kärnan. Instruktioner finns i [skapa en antecknings bok](../quickstart-apache-spark-notebook.md#create-a-notebook).
   
    > [!Note]
    > På grund av PySpark-kärnan behöver du inte skapa några kontexter explicit. Spark-kontexten skapas automatiskt åt dig när du kör den första kod cellen.
  
2. Eftersom rå data är i ett Parquet-format kan du använda Spark-kontexten för att hämta filen direkt till minnet som en DataFrame. Skapa en spark-DataFrame genom att hämta data via Open data uppsättnings-API: et. Här använder du egenskaper för Spark-DataFrame `schema on read` för att härleda data typerna och schemat. 
   
    ```python
    blob_account_name = "azureopendatastorage"
    blob_container_name = "nyctlc"
    blob_relative_path = "yellow"
    blob_sas_token = r""

    # Allow Spark to read from the blob remotely
    wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
    spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),blob_sas_token)

    # Spark read parquet; note that it won't load any data yet
    df = spark.read.parquet(wasbs_path)

    ```

3. Beroende på storleken på din spark-pool kan rå data vara för stora eller ta för lång tid att arbeta med. Du kan filtrera data nedåt till något mindre, till exempel en månad med data, med hjälp av- ```start_date``` och- ```end_date``` filtren. När du har filtrerat en DataFrame kör du även ```describe()``` funktionen på den nya DataFrame för att se sammanfattnings statistik för varje fält. 

   Utifrån sammanfattnings statistik kan du se att det finns några felaktigheter i data. Statistiken visar till exempel att minsta rese avstånd är mindre än 0. Du måste filtrera bort dessa oregelbundna data punkter.
   
   ```python
   # Create an ingestion filter
   start_date = '2015-01-01 00:00:00'
   end_date = '2015-12-31 00:00:00'

   filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime< "' + end_date + '"')

   filtered_df.describe().show()
   ```

4. Generera funktioner från data uppsättningen genom att välja en uppsättning kolumner och skapa olika tidsbaserade funktioner från upphämtnings `datetime` fältet. Filtrera bort extrem värden som identifierades från det tidigare steget och ta sedan bort de senaste kolumnerna eftersom de inte behövs för utbildning.
   
   ```python
   from datetime import datetime
   from pyspark.sql.functions import *

   # To make development easier, faster, and less expensive, downsample for now
   sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)

   taxi_df = sampled_taxi_df.select('vendorID', 'passengerCount', 'tripDistance',  'startLon', 'startLat', 'endLon' \
                                   , 'endLat', 'paymentType', 'fareAmount', 'tipAmount'\
                                   , column('puMonth').alias('month_num') \
                                   , date_format('tpepPickupDateTime', 'hh').alias('hour_of_day')\
                                   , date_format('tpepPickupDateTime', 'EEEE').alias('day_of_week')\
                                   , dayofmonth(col('tpepPickupDateTime')).alias('day_of_month')
                                   ,(unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('trip_time'))\
                           .filter((sampled_taxi_df.passengerCount > 0) & (sampled_taxi_df.passengerCount < 8)\
                                   & (sampled_taxi_df.tipAmount >= 0)\
                                   & (sampled_taxi_df.fareAmount >= 1) & (sampled_taxi_df.fareAmount <= 250)\
                                   & (sampled_taxi_df.tipAmount < sampled_taxi_df.fareAmount)\
                                   & (sampled_taxi_df.tripDistance > 0) & (sampled_taxi_df.tripDistance <= 200)\
                                   & (sampled_taxi_df.rateCodeId <= 5)\
                                   & (sampled_taxi_df.paymentType.isin({"1", "2"})))
   taxi_df.show(10)
   ```
   
   Som du kan se skapas en ny DataFrame med ytterligare kolumner för dagen i månaden, hämtnings tid, veckodag och total fördröjning. 

   ![Bild av taxi-DataFrame.](./media/azure-machine-learning-spark-notebook/dataset.png#lightbox)

## <a name="generate-test-and-validation-datasets"></a>Generera data uppsättningar för test och validering

När du har den slutliga data uppsättningen kan du dela upp data i utbildnings-och test uppsättningar genom att använda ```random_ split ``` funktionen i Spark. Genom att använda de angivna vikterna delar den här funktionen slumpvis data i data uppsättningen för modell utbildning och validerings data uppsättning för testning.

```python
# Random split dataset using Spark; convert Spark to pandas
training_data, validation_data = taxi_df.randomSplit([0.8,0.2], 223)

```
Det här steget säkerställer att data punkterna för att testa den färdiga modellen inte har använts för att träna modellen. 

## <a name="connect-to-an-azure-machine-learning-workspace"></a>Ansluta till en Azure Machine Learning-arbetsyta
I Azure Machine Learning är en arbets yta en klass som godkänner din Azure-prenumeration och resursinformation. Den skapar också en molnresurs för att övervaka och spåra dina körningar i modellen. I det här steget skapar du ett arbets ytans objekt från den befintliga Azure Machine Learning-arbetsytan.
   
```python
from azureml.core import Workspace

# Enter your workspace subscription, resource group, name, and region.
subscription_id = "<enter your subscription ID>" #you should be owner or contributor
resource_group = "<enter your resource group>" #you should be owner or contributor
workspace_name = "<enter your workspace name>" #your workspace name
workspace_region = "<enter workspace region>" #your region

ws = Workspace(workspace_name = workspace_name,
               subscription_id = subscription_id,
               resource_group = resource_group)

```

## <a name="convert-a-dataframe-to-an-azure-machine-learning-dataset"></a>Konvertera en DataFrame till en Azure Machine Learning data uppsättning
Om du vill skicka ett fjärrexperiment konverterar du data uppsättningen till en Azure Machine Learning- ```TabularDatset``` instans. [TabularDataset](/python/api/azureml-core/azureml.data.tabulardataset) representerar data i tabell format genom att parsa de angivna filerna.

Följande kod hämtar den befintliga arbets ytan och standard Azure Machine Learning data lagret. Den skickar sedan data lagret och fil Sök vägarna till parametern Path för att skapa en ny ```TabularDataset``` instans. 

```python
import pandas 
from azureml.core import Dataset

# Get the Azure Machine Learning default datastore
datastore = ws.get_default_datastore()
training_pd = training_data.toPandas().to_csv('training_pd.csv', index=False)

# Convert into an Azure Machine Learning tabular dataset
datastore.upload_files(files = ['training_pd.csv'],
                       target_path = 'train-dataset/tabular/',
                       overwrite = True,
                       show_progress = True)
dataset_training = Dataset.Tabular.from_delimited_files(path = [(datastore, 'train-dataset/tabular/training_pd.csv')])
```
![Bild av överförd data uppsättning.](./media/azure-machine-learning-spark-notebook/upload-dataset.png)

## <a name="submit-an-automated-experiment"></a>Skicka ett automatiserat experiment

I följande avsnitt får du stegvisa anvisningar genom processen för att skicka ett automatiserat maskin inlärnings experiment.

### <a name="define-training-settings"></a>Definiera utbildnings inställningar
1. Om du vill skicka ett experiment måste du definiera experiment parametern och modell inställningarna för träning. En fullständig lista över inställningarna finns i [Konfigurera automatiska Machine Learning-experiment i python](../../machine-learning/how-to-configure-auto-train.md).

   ```python
   import logging

   automl_settings = {
       "iteration_timeout_minutes": 10,
       "experiment_timeout_minutes": 30,
       "enable_early_stopping": True,
       "primary_metric": 'r2_score',
       "featurization": 'auto',
       "verbosity": logging.INFO,
       "n_cross_validations": 2}
   ```

1. Skicka de definierade utbildnings inställningarna som en `kwargs` parameter till ett `AutoMLConfig` objekt. Eftersom du använder Spark måste du också skicka Spark-kontexten, som automatiskt kan nås av ```sc``` variabeln. Dessutom kan du ange tränings data och typ av modell, som är regression i det här fallet.

   ```python
   from azureml.train.automl import AutoMLConfig

   automl_config = AutoMLConfig(task='regression',
                                debug_log='automated_ml_errors.log',
                                training_data = dataset_training,
                                spark_context = sc,
                                model_explainability = False, 
                                label_column_name ="fareAmount",**automl_settings)
   ```

> [!NOTE]
>Automatiserad bearbetning av Machine Learning-steg blir en del av den underliggande modellen. Dessa steg omfattar funktioner normalisering, hantering av data som saknas och konvertering av text till numeriska. När du använder modellen för förutsägelser tillämpas samma för bearbetnings steg som tillämpas på dina indata-data automatiskt.

### <a name="train-the-automatic-regression-model"></a>Träna den automatiska regressionsmodellen 
Därefter skapar du ett experiment objekt i din Azure Machine Learning-arbetsyta. Ett experiment fungerar som en behållare för dina enskilda körningar. 

```python
from azureml.core.experiment import Experiment

# Start an experiment in Azure Machine Learning
experiment = Experiment(ws, "aml-synapse-regression")
tags = {"Synapse": "regression"}
local_run = experiment.submit(automl_config, show_output=True, tags = tags)

# Use the get_details function to retrieve the detailed output for the run.
run_details = local_run.get_details()
```
När experimentet har slutförts returnerar utdata information om slutförda iterationer. För varje iteration ser du modelltypen, körningens varaktighet samt träningens noggrannhet. `BEST`Fältet spårar det bästa inlärnings resultatet baserat på mått typen.

![Skärm bild av modellens utdata.](./media/azure-machine-learning-spark-notebook/model-output.png)

> [!NOTE]
> När du har skickat in det automatiserade experimentet för maskin inlärning körs olika iterationer och modell typer. Den här körningen tar vanligt vis 60 till 90 minuter. 

### <a name="retrieve-the-best-model"></a>Hämta den bästa modellen
Om du vill välja den bästa modellen från dina iterationer använder du ```get_output``` funktionen för att returnera den bästa körningen och den monterade modellen. Följande kod hämtar den bästa körnings-och den monterade modellen för alla inloggade mått eller en viss iteration.

```python
# Get best model
best_run, fitted_model = local_run.get_output()
```

### <a name="test-model-accuracy"></a>Test modellens precision
1. Testa modell precisionen genom att använda den bästa modellen för att köra taxi pris förutsägelser på test data uppsättningen. ```predict```Funktionen använder den bästa modellen och förutsäger värdena för `y` (pris belopp) från verifierings data uppsättningen. 

   ```python
   # Test best model accuracy
   validation_data_pd = validation_data.toPandas()
   y_test = validation_data_pd.pop("fareAmount").to_frame()
   y_predict = fitted_model.predict(validation_data_pd)
   ```

1. Roten – medelvärde-kvadratiskt fel är ett ofta använda mått på skillnaderna mellan exempel värden som förutsägs av en modell och de värden som observeras. Du beräknar det bakomliggande medelvärdet för resultatet genom `y_test` att jämföra DataFrame med de värden som förväntas av modellen. 

   Funktionen ```mean_squared_error``` använder två matriser och beräknar det genomsnittliga kvadratvärdet för ett fel mellan dem. Sedan tar du kvadratroten ur resultatet. Det här måttet anger ungefär hur långt priset för taxi avgiften är från de faktiska biljett värdena.

   ```python
   from sklearn.metrics import mean_squared_error
   from math import sqrt

   # Calculate root-mean-square error
   y_actual = y_test.values.flatten().tolist()
   rmse = sqrt(mean_squared_error(y_actual, y_predict))

   print("Root Mean Square Error:")
   print(rmse)
   ```

   ```Output
   Root Mean Square Error:
   2.309997102577151
   ```
   Fel meddelandet rot-genomsnitts fyrkant är ett bra mått på hur bättre modellen förutsäger svaret. Från resultaten ser du att modellen är ganska lämplig vid förutsägelse av taxi-biljetter från data uppsättningens funktioner, vanligt vis inom $2,00.

1. Kör följande kod för att beräkna medelvärdes-absoluta-procent-felet. Mät värdet uttrycker noggrannhet som en procent andel av felet. Detta görs genom att beräkna en absolut skillnad mellan varje förutsägande och faktiskt värde och sedan summera alla skillnader. Sedan uttrycker den summan som en procent andel av summan av de faktiska värdena.

   ```python
   # Calculate mean-absolute-percent error and model accuracy 
   sum_actuals = sum_errors = 0

   for actual_val, predict_val in zip(y_actual, y_predict):
       abs_error = actual_val - predict_val
       if abs_error < 0:
           abs_error = abs_error * -1

       sum_errors = sum_errors + abs_error
       sum_actuals = sum_actuals + actual_val

   mean_abs_percent_error = sum_errors / sum_actuals

   print("Model MAPE:")
   print(mean_abs_percent_error)
   print()
   print("Model Accuracy:")
   print(1 - mean_abs_percent_error)
   ```

   ```Output
   Model MAPE:
   0.03655071038487368

   Model Accuracy:
   0.9634492896151263
   ```
   Från de två noggrannhets måtten för förutsägelse ser du att modellen är ganska lämplig vid förutsägelse av taxi-biljetter från data uppsättningens funktioner. 

1. När du har inpassat en linjär Regressions modell måste du nu fastställa hur väl modellen passar data. Det gör du genom att rita de faktiska biljett värdena mot förväntade utdata. Dessutom beräknar du R-kvadratvärde-måttet för att förstå hur nära data är till den monterade Regressions linjen.

   ```python
   import matplotlib.pyplot as plt
   import numpy as np
   from sklearn.metrics import mean_squared_error, r2_score

   # Calculate the R2 score by using the predicted and actual fare prices
   y_test_actual = y_test["fareAmount"]
   r2 = r2_score(y_test_actual, y_predict)

   # Plot the actual versus predicted fare amount values
   plt.style.use('ggplot')
   plt.figure(figsize=(10, 7))
   plt.scatter(y_test_actual,y_predict)
   plt.plot([np.min(y_test_actual), np.max(y_test_actual)], [np.min(y_test_actual), np.max(y_test_actual)], color='lightblue')
   plt.xlabel("Actual Fare Amount")
   plt.ylabel("Predicted Fare Amount")
   plt.title("Actual vs Predicted Fare Amount R^2={}".format(r2))
   plt.show()

   ```
   ![Skärm bild av en Regressions kurva.](./media/azure-machine-learning-spark-notebook/fare-amount.png)

   Från resultaten kan du se att R-kvadratvärde-måttets konton i 95 procent av avvikelsen. Detta val IDE ras också av det faktiska området jämfört med observations området. Den mer varians som Regressions modell kontona är för, desto närmare data punkterna kommer att falla till den monterade Regressions linjen.  

## <a name="register-the-model-to-azure-machine-learning"></a>Registrera modellen på Azure Machine Learning
När du har verifierat din bästa modell kan du registrera den till Azure Machine Learning. Sedan kan du hämta eller distribuera den registrerade modellen och ta emot alla filer som du har registrerat.

```python
description = 'My automated ML model'
model_path='outputs/model.pkl'
model = best_run.register_model(model_name = 'NYCGreenTaxiModel', model_path = model_path, description = description)
print(model.name, model.version)
```
```Output
NYCGreenTaxiModel 1
```
## <a name="view-results-in-azure-machine-learning"></a>Visa resultat i Azure Machine Learning
Du kan också få åtkomst till resultatet av iterationerna genom att gå till experimentet i Azure Machine Learning arbets ytan. Här kan du få mer information om status för dina körningar, försöks modeller och andra modell mått. 

![Skärm bild av en Azure Machine Learning-arbetsyta.](./media/azure-machine-learning-spark-notebook/azure-machine-learning-workspace.png)

## <a name="next-steps"></a>Nästa steg
- [Azure Synapse Analytics](../index.yml)
- [Självstudie: Bygg en Machine Learning-app med Apache Spark MLlib och Azure Synapse Analytics](./apache-spark-machine-learning-mllib-notebook.md)