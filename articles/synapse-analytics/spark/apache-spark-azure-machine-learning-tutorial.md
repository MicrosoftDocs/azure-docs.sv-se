---
title: Köra experiment med Azures automatiserade ML
description: Kör Machine Learning-experiment med Apache Spark och Azures automatiserad ML
services: synapse-analytics
author: midesa
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 06/30/2020
ms.author: midesa
ms.reviewer: jrasnick,
ms.openlocfilehash: 57830ae14c5d6653f6a2225924160fcb75646c12
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590767"
---
# <a name="run-experiments-using-azure-automated-ml-and-apache-spark"></a>Köra experiment med Azures automatiserade ML och Apache Spark

Azure Machine Learning är en molnbaserad miljö som gör det möjligt att träna, distribuera, automatisera, hantera och spåra maskin inlärnings modeller. 

I den här självstudien använder du [Automatisk maskin inlärning](https://docs.microsoft.com/azure/machine-learning/concept-automated-ml) i Azure Machine Learning för att skapa en Regressions modell för att förutsäga priserna för NYC Taxi pris. Den här processen godkänner inlärnings data och konfigurations inställningar och upprepas automatiskt genom kombinationer av olika metoder, modeller och inställningar för en funktion för funktioner, modeller och kompatibilitetsinställningar för att komma till den bästa modellen.

I den här självstudien får du lära dig följande uppgifter:
- Hämta data med hjälp av Apache Spark och Azure Open-datauppsättningar
- Transformera och rensa data med Apache Spark dataframes
- Träna en automatisk maskin inlärnings Regressions modell
- Beräkna modell precision

### <a name="before-you-begin"></a>Innan du börjar
- Skapa en Apache Spark pool genom att följa [själv studie kursen skapa en Apache Spark pool](../quickstart-create-apache-spark-pool-studio.md).
- Slutför [installations guiden för Azure Machine Learning arbets yta](https://docs.microsoft.com/azure/machine-learning/tutorial-1st-experiment-sdk-setup) om du inte har en befintlig Azure Machine Learning arbets yta. 

### <a name="understand-regression-models"></a>Förstå Regressions modeller
*Regressions modeller* förutsäger numeriska utmatnings värden baserat på oberoende förförutsägelser. Syftet med regressionen är att hjälpa till att upprätta relationen mellan de oberoende förutsägande variablerna genom att uppskatta hur en variabel påverkar de andra.  

### <a name="regression-analysis-example-on-the-nyc-taxi-data"></a>Regressions analys exempel på NYC taxi-data
I det här exemplet ska du använda Spark för att utföra vissa analyser av information om taxi resor från New York. Data är tillgängliga via [Azure Open data uppsättningar](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Denna del av data uppsättningen innehåller information om gula taxi resor, inklusive information om varje resa, start-och slut tid samt platser, kostnad och andra intressanta attribut.

> [!IMPORTANT]
> 
> Det kan finnas ytterligare avgifter för att hämta dessa data från dess lagrings plats. I följande steg ska du utveckla en modell för att förutsäga priserna för NYC Taxi pris. 
> 

##  <a name="download-and-prepare-the-data"></a>Hämta och förbereda data

1. Skapa en bärbar dator med PySpark-kärnan. Instruktioner finns i [skapa en antecknings bok](https://docs.microsoft.com/azure/synapse-analytics/quickstart-apache-spark-notebook#create-a-notebook.)
   
   > [!Note]
   > 
   > På grund av PySpark-kärnan behöver du inte skapa några kontexter explicit. Spark-kontexten skapas automatiskt åt dig när du kör den första kod cellen.
   >

2. Eftersom rå data är i ett Parquet-format kan du använda Spark-kontexten för att hämta filen till minnet som en dataframe direkt. Skapa en spark-dataframe genom att hämta data via Open data uppsättnings-API: et. Här använder vi Spark dataframe- *schemat på Läs* egenskaper för att härleda data typerna och schemat. 
   
   ```python
   blob_account_name = "azureopendatastorage"
   blob_container_name = "nyctlc"
   blob_relative_path = "yellow"
   blob_sas_token = r""

   # Allow Spark to read from Blob remotely
   wasbs_path = 'wasbs://%s@%s.blob.core.windows.net/%s' % (blob_container_name, blob_account_name, blob_relative_path)
   spark.conf.set('fs.azure.sas.%s.%s.blob.core.windows.net' % (blob_container_name, blob_account_name),blob_sas_token)

   # Spark read parquet, note that it won't load any data yet by now
   df = spark.read.parquet(wasbs_path)
   ```

3. Beroende på storleken på din spark-pool (för hands version) kan rå data vara för stora eller ta för lång tid att arbeta med. Du kan filtrera data nedåt till något mindre genom att använda- ```start_date``` och- ```end_date``` filtren. Detta använder ett filter som returnerar en månad med data. När vi har filtrerat dataframe kommer vi också att köra ```describe()``` funktionen på den nya dataframe för att se sammanfattnings statistik för varje fält. 

   Utifrån sammanfattnings statistik kan vi se att det finns några felaktigheter och avvikande extrem värden i data. Statistiken visar till exempel att det minsta rese avståndet är mindre än 0. Vi kommer att behöva filtrera bort dessa oregelbundna data punkter.
   
   ```python
   # Create an ingestion filter
   start_date = '2015-01-01 00:00:00'
   end_date = '2015-12-31 00:00:00'

   filtered_df = df.filter('tpepPickupDateTime > "' + start_date + '" and tpepPickupDateTime < "' + end_date + '"')

   filtered_df.describe().show()
   ```

4. Nu ska vi generera funktioner från data uppsättningen genom att välja en uppsättning kolumner och skapa olika tidsbaserade funktioner från fältet upphämtnings datum/tid. Vi filtrerar också ut extrem värden som har identifierats från föregående steg och sedan tar bort de senaste kolumnerna som inte behövs för utbildning.
   
   ```python
   from datetime import datetime
   from pyspark.sql.functions import *

   # To make development easier, faster and less expensive down sample for now
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
   
Som du kan se skapas en ny dataframe med ytterligare kolumner för dagen i månaden, hämtnings tid, veckodag och total fördröjning. 

![Bild av taxi-dataframe.](./media/apache-spark-machine-learning-aml-notebook/aml-dataset.png)

## <a name="generate-test-and-validation-datasets"></a>Generera data uppsättningar för test och validering

När vi har vår slutliga data uppsättning kan vi dela upp data i utbildnings-och test uppsättningar med hjälp av Spark- ```random_ split ``` funktionen. Med hjälp av de angivna vikterna delar den här funktionen slumpvis data i data uppsättningen för modell utbildning och validerings data uppsättning för testning.

```python
# Random split dataset using spark, convert Spark to Pandas
training_data, validation_data = taxi_df.randomSplit([0.8,0.2], 223)

```

Det här steget ser till att data punkter för att testa den färdiga modellen som inte har använts för att träna modellen. 

## <a name="connect-to-an-azure-machine-learning-workspace"></a>Ansluta till en Azure Machine Learning-arbetsyta
I Azure Machine Learning är en  **arbets yta** en klass som godkänner din Azure-prenumeration och resursinformation. Den skapar också en molnresurs för att övervaka och spåra dina körningar i modellen. I det här steget ska vi skapa ett objekt för arbets ytan från den befintliga Azure Machine Learning-arbetsytan.
   
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

## <a name="convert-a-dataframe-to-an-azure-machine-learning-dataset"></a>Konvertera en dataframe till en Azure Machine Learning data uppsättning
För att kunna skicka ett fjärrexperiment måste vi konvertera vår data uppsättning till en Azure Machine Learning ```TabularDatset``` . En [TabularDataset](https://docs.microsoft.com/python/api/azureml-core/azureml.data.tabulardataset?view=azure-ml-py) representerar data i tabell format genom att parsa de angivna filerna.

Följande kod hämtar den befintliga arbets ytan och förvalda Azure Machine Learning standard data lager. Den skickar sedan data lagret och fil Sök vägarna till parametern Path för att skapa en ny ```TabularDataset``` . 

```python
import pandas 
from azureml.core import Dataset

# Get the AML Default Datastore
datastore = ws.get_default_datastore()
training_pd = training_data.toPandas().to_csv('training_pd.csv', index=False)

# Convert into AML Tabular Dataset
datastore.upload_files(files = ['training_pd.csv'],
                       target_path = 'train-dataset/tabular/',
                       overwrite = True,
                       show_progress = True)
dataset_training = Dataset.Tabular.from_delimited_files(path = [(datastore, 'train-dataset/tabular/training_pd.csv')])
```

![Bild av överförd data uppsättning.](./media/apache-spark-machine-learning-aml-notebook/upload-dataset.png)

## <a name="submit-an-auto-ml-experiment"></a>Skicka ett automatiskt ML-experiment

#### <a name="define-training-settings"></a>Definiera utbildnings inställningar

1. För att skicka ett experiment måste du definiera experiment parametern och modell inställningarna för träning. Du kan visa en fullständig lista med inställningar [här](https://docs.microsoft.com/azure/machine-learning/how-to-configure-auto-train).

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

2. Nu ska vi skicka de definierade utbildnings inställningarna som en \* \* kwargs-parameter till ett AutoMLConfig-objekt. Eftersom vi är utbildning i Spark måste vi också skicka en spark-kontext som automatiskt kan nås av ```sc``` variabeln. Dessutom anger vi utbildnings data och typ av modell, som är regression i det här fallet.

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
> Automatiserad bearbetning av Machine Learning för bearbetning (funktions normalisering, hantering av saknade data, konvertering av text till tal osv.) blir en del av den underliggande modellen. När du använder modellen för förutsägelser tillämpas samma för bearbetnings steg som tillämpas på dina indata-data automatiskt.

#### <a name="train-the-automatic-regression-model"></a>Träna den automatiska regressionsmodellen 
Nu ska vi skapa ett experiment objekt i din Azure Machine Learning-arbetsyta. Ett experiment fungerar som en behållare för dina enskilda körningar. 

```python
from azureml.core.experiment import Experiment

# Start an experiment in Azure Machine Learning
experiment = Experiment(ws, "aml-synapse-regression")
tags = {"Synapse": "regression"}
local_run = experiment.submit(automl_config, show_output=True, tags = tags)

# Use the get_details function to retrieve the detailed output for the run.
run_details = local_run.get_details()
```

När experimentet har slutförts returnerar utdata information om slutförda iterationer. För varje iteration ser du modelltypen, körningens varaktighet samt träningens noggrannhet. Fältet visar bästa möjliga inlärnings poäng baserat på mått typen.

![Skärm bild av modellens utdata.](./media/apache-spark-machine-learning-aml-notebook/aml-model-output.png)

> [!NOTE]
> När det har skickats körs olika iterationer och modell typer av AutoML-experimentet. Den här körningen tar vanligt vis 1 – 1,5 timmar. 

#### <a name="retrieve-the-best-model"></a>Hämta den bästa modellen
För att välja den bästa modellen från dina iterationer använder vi ```get_output``` funktionen för att returnera den bästa körningen och den monterade modellen. Koden nedan hämtar den bästa körnings-och den monterade modellen för alla inloggade mått eller en viss iteration.

```python
# Get best model
best_run, fitted_model = local_run.get_output()
```

#### <a name="test-model-accuracy"></a>Test modellens precision

1. För att testa modell precisionen kommer vi att använda den bästa modellen för att köra taxi pris förutsägelser på test data uppsättningen. ```predict```Funktionen använder den bästa modellen och förutsäger värdena för y (pris belopp) från verifierings data uppsättningen. 

   ```python
   # Test best model accuracy
   validation_data_pd = validation_data.toPandas()
   y_test = validation_data_pd.pop("fareAmount").to_frame()
   y_predict = fitted_model.predict(validation_data_pd)
   ```

2. RMSE (root-mean-Square) är ett ofta använda mått på skillnaderna mellan exempel värden som förutsägs av en modell och de värden som observeras. Vi beräknar rotens medelvärde för resultatet genom att jämföra y_test-dataframe med de värden som förväntas av modellen. 

   Funktionen ```mean_squared_error``` använder två matriser och beräknar det genomsnittliga kvadratvärdet för ett fel mellan dem. Därefter tar vi kvadratroten ur resultatet. Det här måttet anger ungefär hur långt priset för taxi avgiften är från de faktiska biljett priserna.

   ```python
   from sklearn.metrics import mean_squared_error
   from math import sqrt

   # Calculate Root Mean Square Error
   y_actual = y_test.values.flatten().tolist()
   rmse = sqrt(mean_squared_error(y_actual, y_predict))

   print("Root Mean Square Error:")
   print(rmse)
   ```

   ```Output
   Root Mean Square Error:
   2.309997102577151
   ```
   
   Fel meddelandet rot-genomsnitts fyrkant är ett bra mått på hur bättre modellen förutsäger svaret. Från resultaten ser du att modellen är ganska lämplig vid förutsägelse av taxi-biljetter från data uppsättningens funktioner, vanligt vis inom +-$2,00

3. Kör följande kod för att beräkna medelvärde för absoluta procent fel (MAPE). Mät värdet uttrycker noggrannhet som en procent andel av felet. Detta görs genom att beräkna en absolut skillnad mellan varje förutsägande och faktiskt värde och sedan summera alla skillnader. Sedan uttrycker den summan som en procent andel av summan av de faktiska värdena.

   ```python
   # Calculate MAPE and Model Accuracy 
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
   Från de två noggrannhets måtten för förutsägelser ser du att modellen är ganska stor för att förutsäga Taxis biljetter från data uppsättningens funktioner. 

4. När du har tilldelat en linjär Regressions modell måste vi nu fastställa hur väl modellen passar dina data. För att göra detta kommer vi att rita de faktiska biljett värdena mot förväntade utdata. Dessutom beräknar vi även R-kvadratvärde-måttet för att förstå hur nära data är till den monterade Regressions linjen.

   ```python
   import matplotlib.pyplot as plt
   import numpy as np
   from sklearn.metrics import mean_squared_error, r2_score

   # Calculate the R2 score using the predicted and actual fare prices
   y_test_actual = y_test["fareAmount"]
   r2 = r2_score(y_test_actual, y_predict)

   # Plot the Actual vs Predicted Fare Amount Values
   plt.style.use('ggplot')
   plt.figure(figsize=(10, 7))
   plt.scatter(y_test_actual,y_predict)
   plt.plot([np.min(y_test_actual), np.max(y_test_actual)], [np.min(y_test_actual), np.max(y_test_actual)], color='lightblue')
   plt.xlabel("Actual Fare Amount")
   plt.ylabel("Predicted Fare Amount")
   plt.title("Actual vs Predicted Fare Amont R^2={}".format(r2))
   plt.show()
   ```
   
   ![Skärm bild av Regressions kurva.](./media/apache-spark-machine-learning-aml-notebook/aml-fare-amount.png)

   Från resultaten kan vi se att konton för R-kvadratvärdet-mått för 95% av vår varians. Detta val IDE ras också av de faktiska fraserna i observations området. Den mer varians som redovisas av Regressions modellen desto närmare data punkterna kommer att falla till den monterade Regressions linjen.  

## <a name="register-model-to-azure-machine-learning"></a>Registrera modell för Azure Machine Learning
När vi har verifierat vår bästa modell kan vi registrera modellen till Azure Machine Learning. När du har registrerat modellen kan du ladda ned eller distribuera den registrerade modellen och ta emot alla filer som du har registrerat.

```python
description = 'My AutoML Model'
model_path='outputs/model.pkl'
model = best_run.register_model(model_name = 'NYCGreenTaxiModel', model_path = model_path, description = description)
print(model.name, model.version)
```

```Output
NYCGreenTaxiModel 1
```

## <a name="view-results-in-azure-machine-learning"></a>Visa resultat i Azure Machine Learning
Du kan också få åtkomst till resultatet av iterationerna genom att gå till experimentet i Azure Machine Learning-arbetsyta. Här kommer du att kunna ta del av ytterligare information om status för dina körningar, försöks modeller och andra modell mått. 

![Skärm bild av AML-arbetsytan.](./media/apache-spark-machine-learning-mllib-notebook/apache-spark-aml-workspace.png)

## <a name="next-steps"></a>Nästa steg
- [Azure Synapse Analytics](https://docs.microsoft.com/azure/synapse-analytics)
- [Själv studie kurs om Apache Spark MLlib](./apache-spark-machine-learning-mllib-notebook.md)
