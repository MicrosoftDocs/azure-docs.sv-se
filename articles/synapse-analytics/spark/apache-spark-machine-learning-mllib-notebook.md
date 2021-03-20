---
title: 'Självstudie: Bygg en Machine Learning-app med Apache Spark MLlib'
description: En själv studie kurs om hur du använder Apache Spark MLlib för att skapa en app Learning-app som analyserar en data uppsättning med hjälp av klassificering via logistik regression.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.reviewer: jrasnick
ms.topic: tutorial
ms.subservice: machine-learning
ms.date: 04/15/2020
ms.author: euang
ms.openlocfilehash: 5caa41b852bf55a11489db6c0bab871b20720e05
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101670665"
---
# <a name="tutorial-build-a-machine-learning-app-with-apache-spark-mllib-and-azure-synapse-analytics"></a>Självstudie: Bygg en Machine Learning-app med Apache Spark MLlib och Azure Synapse Analytics

I den här artikeln får du lära dig hur du använder Apache Spark [MLlib](https://spark.apache.org/mllib/) för att skapa ett maskin inlärnings program som gör enkel förutsägelse analys på en öppen Azure Open-datauppsättning. Spark innehåller inbyggda Machine Learning-bibliotek. I det här exemplet används *klassificering* via logistik regression.

SparkML och MLlib är Core Spark-bibliotek som tillhandahåller många verktyg som är användbara för Machine Learning-uppgifter, inklusive verktyg som är lämpliga för:

- Klassificering
- Regression
- Klustring
- Ämnes modeller
- Sammanställning av sammansatta värden (SVD) och huvud komponent analys (PCA)
- Hypotes testning och beräkning av exempel statistik

## <a name="understand-classification-and-logistic-regression"></a>Förstå klassificering och logistik regression

*Klassificering*, en populär maskin inlärnings uppgift, är processen att sortera indata i kategorier. Det är ett jobb för en klassificerings algoritm som visar hur du tilldelar *Etiketter* till indata som du anger. Du kan till exempel tänka på en Machine Learning-algoritm som tar emot aktie information som indata och delar upp lagret i två kategorier: aktier som du bör sälja och de bestånd som du bör behålla.

*Logistisk regression* är en algoritm som du kan använda för klassificering. Spark: s logistik Regressions-API är användbart för *binär klassificering* eller klassificerar indata till en av två grupper. Mer information om Logistisk regression finns i [Wikipedia](https://en.wikipedia.org/wiki/Logistic_regression).

I sammanfattning skapar processen för Logistisk regression en logistisk- *funktion* som du kan använda för att förutsäga sannolikheten att en indatamängds-Vector tillhör en grupp eller en annan.

## <a name="predictive-analysis-example-on-nyc-taxi-data"></a>Exempel på förutsägelse analys på NYC taxi-data

I det här exemplet ska du använda Spark för att utföra en förutsägelse analys av data för taxi resan från New York. Data är tillgängliga via [Azure Open data uppsättningar](https://azure.microsoft.com/services/open-datasets/catalog/nyc-taxi-limousine-commission-yellow-taxi-trip-records/). Denna del av data uppsättningen innehåller information om gula taxi resor, inklusive information om varje resa, start-och slut tid samt platser, kostnad och andra intressanta attribut.

> [!IMPORTANT]
> Det kan finnas ytterligare avgifter för att hämta dessa data från dess lagrings plats.

I följande steg utvecklar du en modell för att förutsäga om en viss resa innehåller ett tips eller inte.

## <a name="create-an-apache-spark-machine-learning-model"></a>Skapa en Apache Spark Machine Learning-modell

1. Skapa en bärbar dator med hjälp av PySpark-kärnan. Instruktioner finns i [skapa en antecknings bok](../quickstart-apache-spark-notebook.md#create-a-notebook).
2. Importera de typer som krävs för det här programmet. Kopiera och klistra in följande kod i en tom cell och tryck sedan på SKIFT + RETUR. Eller kör cellen med hjälp av den blå uppspelnings ikonen till vänster om koden.

    ```python
    import matplotlib.pyplot as plt
    from datetime import datetime
    from dateutil import parser
    from pyspark.sql.functions import unix_timestamp, date_format, col, when
    from pyspark.ml import Pipeline
    from pyspark.ml import PipelineModel
    from pyspark.ml.feature import RFormula
    from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorIndexer
    from pyspark.ml.classification import LogisticRegression
    from pyspark.mllib.evaluation import BinaryClassificationMetrics
    from pyspark.ml.evaluation import BinaryClassificationEvaluator
    ```

    På grund av PySpark-kärnan behöver du inte skapa några kontexter explicit. Spark-kontexten skapas automatiskt åt dig när du kör den första kod cellen.

## <a name="construct-the-input-dataframe"></a>Konstruera DataFrame för indatamängd

Eftersom rå data är i ett Parquet-format kan du använda Spark-kontexten för att hämta filen till minnet som en DataFrame direkt. Även om koden i följande steg använder standard alternativen, är det möjligt att tvinga mappning av data typer och andra schemaattribut om det behövs.

1. Kör följande rader för att skapa en spark-DataFrame genom att klistra in koden i en ny cell. I det här steget hämtas data via det öppna data uppsättnings-API: et. Att hämta alla dessa data genererar cirka 1 500 000 000 rader. 

   Beroende på storleken på den serverbaserade Apache Spark-poolen kan rå data vara för stora eller ta för lång tid att arbeta med. Du kan filtrera bort dessa data till något mindre. I följande kod exempel används `start_date` och `end_date` för att tillämpa ett filter som returnerar en enda månad med data.

    ```python
    from azureml.opendatasets import NycTlcYellow

    end_date = parser.parse('2018-06-06')
    start_date = parser.parse('2018-05-01')
    nyc_tlc = NycTlcYellow(start_date=start_date, end_date=end_date)
    filtered_df = nyc_tlc.to_spark_dataframe()
    ```

2. Nack delen med enkel filtrering är att från ett statistiskt perspektiv kan det leda till en förskjutning i data. En annan metod är att använda den inbyggda samplingen i Spark. 

   Följande kod minskar data uppsättningen till cirka 2 000 rader, om den används efter föregående kod. Du kan använda det här samplings steget i stället för det enkla filtret eller tillsammans med det enkla filtret.

    ```python
    # To make development easier, faster, and less expensive, downsample for now
    sampled_taxi_df = filtered_df.sample(True, 0.001, seed=1234)
    ```

3. Nu går det att titta på data för att se vad som lästs. Det är normalt bättre att granska data med en delmängd i stället för den fullständiga uppsättningen, beroende på data uppsättningens storlek. 

   Följande kod erbjuder två sätt att visa data. Det första sättet är Basic. Det andra sättet ger en mycket rikare rutnäts upplevelse, tillsammans med möjligheten att visualisera data grafiskt.

    ```python
    #sampled_taxi_df.show(5)
    display(sampled_taxi_df)
    ```

4. Beroende på storleken på den genererade data uppsättningen och du behöver experimentera eller köra antecknings boken flera gånger kanske du vill cachelagra data uppsättningen lokalt i arbets ytan. Det finns tre sätt att utföra explicit cachelagring:

   - Spara DataFrame lokalt som en fil.
   - Spara DataFrame som en temporär tabell eller vy.
   - Spara DataFrame som en permanent tabell.

De första två här metoderna ingår i följande kod exempel.

När du skapar en temporär tabell eller vy får du olika åtkomst vägar till data, men det gäller endast för varaktigheten för Spark-serverinstansen.

```Python
sampled_taxi_df.createOrReplaceTempView("nytaxi")
```

## <a name="prepare-the-data"></a>Förbereda data

Data i RAW-form är ofta inte lämpliga för att skickas direkt till en modell. Du måste utföra en serie åtgärder för data för att få dem i ett tillstånd där modellen kan använda den.

I följande kod kan du utföra fyra typer av åtgärder:

- Borttagning av avvikande eller felaktiga värden genom filtrering.
- Borttagning av kolumner, som inte behövs.
- Skapandet av nya kolumner härleds från rå data för att modellen ska fungera mer effektivt. Den här åtgärden kallas ibland för funktionalisering.
- Märkning. Eftersom du är tilldelad binära klassificering (det finns ett tips eller inte på en bestämd resa) behöver du konvertera beskrivningen till ett 0-eller 1-värde.

```python
taxi_df = sampled_taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'rateCodeId', 'passengerCount'\
                                , 'tripDistance', 'tpepPickupDateTime', 'tpepDropoffDateTime'\
                                , date_format('tpepPickupDateTime', 'hh').alias('pickupHour')\
                                , date_format('tpepPickupDateTime', 'EEEE').alias('weekdayString')\
                                , (unix_timestamp(col('tpepDropoffDateTime')) - unix_timestamp(col('tpepPickupDateTime'))).alias('tripTimeSecs')\
                                , (when(col('tipAmount') > 0, 1).otherwise(0)).alias('tipped')
                                )\
                        .filter((sampled_taxi_df.passengerCount > 0) & (sampled_taxi_df.passengerCount < 8)\
                                & (sampled_taxi_df.tipAmount >= 0) & (sampled_taxi_df.tipAmount <= 25)\
                                & (sampled_taxi_df.fareAmount >= 1) & (sampled_taxi_df.fareAmount <= 250)\
                                & (sampled_taxi_df.tipAmount < sampled_taxi_df.fareAmount)\
                                & (sampled_taxi_df.tripDistance > 0) & (sampled_taxi_df.tripDistance <= 100)\
                                & (sampled_taxi_df.rateCodeId <= 5)
                                & (sampled_taxi_df.paymentType.isin({"1", "2"}))
                                )
```

Sedan gör du ett andra steg över data för att lägga till de slutliga funktionerna.

```Python
taxi_featurised_df = taxi_df.select('totalAmount', 'fareAmount', 'tipAmount', 'paymentType', 'passengerCount'\
                                                , 'tripDistance', 'weekdayString', 'pickupHour','tripTimeSecs','tipped'\
                                                , when((taxi_df.pickupHour <= 6) | (taxi_df.pickupHour >= 20),"Night")\
                                                .when((taxi_df.pickupHour >= 7) & (taxi_df.pickupHour <= 10), "AMRush")\
                                                .when((taxi_df.pickupHour >= 11) & (taxi_df.pickupHour <= 15), "Afternoon")\
                                                .when((taxi_df.pickupHour >= 16) & (taxi_df.pickupHour <= 19), "PMRush")\
                                                .otherwise(0).alias('trafficTimeBins')
                                              )\
                                       .filter((taxi_df.tripTimeSecs >= 30) & (taxi_df.tripTimeSecs <= 7200))
```

## <a name="create-a-logistic-regression-model"></a>Skapa en logistik Regressions modell

Den sista uppgiften är att konvertera etiketterade data till ett format som kan analyseras via Logistisk regression. Indatamängden till en logistik Regressions algoritm måste vara en uppsättning *etikett-/funktions vektor par*, där *funktions vektorn* är en Vector med tal som representerar ingångs punkten. 

Så du måste konvertera kategoriska-kolumnerna till siffror. Mer specifikt måste du konvertera och- `trafficTimeBins` `weekdayString` kolumnerna till heltals representationer. Det finns flera metoder för att utföra konverteringen. I följande exempel `OneHotEncoder` används metoden, som är vanlig.

```python
# Because the sample uses an algorithm that works only with numeric features, convert them so they can be consumed
sI1 = StringIndexer(inputCol="trafficTimeBins", outputCol="trafficTimeBinsIndex")
en1 = OneHotEncoder(dropLast=False, inputCol="trafficTimeBinsIndex", outputCol="trafficTimeBinsVec")
sI2 = StringIndexer(inputCol="weekdayString", outputCol="weekdayIndex")
en2 = OneHotEncoder(dropLast=False, inputCol="weekdayIndex", outputCol="weekdayVec")

# Create a new DataFrame that has had the encodings applied
encoded_final_df = Pipeline(stages=[sI1, en1, sI2, en2]).fit(taxi_featurised_df).transform(taxi_featurised_df)
```

Den här åtgärden resulterar i en ny DataFrame med alla kolumner i rätt format för att träna en modell.

## <a name="train-a-logistic-regression-model"></a>Träna en logistik Regressions modell

Den första aktiviteten är att dela upp data uppsättningen i en tränings uppsättning och en testnings-eller validerings uppsättning. Delningen här är godtycklig. Experimentera med olika delnings inställningar för att se om de påverkar modellen.

```python
# Decide on the split between training and testing data from the DataFrame
trainingFraction = 0.7
testingFraction = (1-trainingFraction)
seed = 1234

# Split the DataFrame into test and training DataFrames
train_data_df, test_data_df = encoded_final_df.randomSplit([trainingFraction, testingFraction], seed=seed)
```

Nu när det finns två DataFrames är nästa uppgift att skapa modell formeln och köra den mot Training-DataFrame. Sedan kan du validera mot test-DataFrame. Experimentera med olika versioner av modell formeln för att se effekten av olika kombinationer.

> [!Note]
> Om du vill spara modellen behöver du Azure-rollen *Storage BLOB data Contributor* . Under ditt lagrings konto går du till **Access Control (IAM)** och väljer **Lägg till roll tilldelning**. Tilldela rollen Storage BLOB data Contributor till din Azure SQL Database-Server. Endast medlemmar med ägar behörighet kan utföra det här steget. 
>
>Information om olika inbyggda Azure-roller finns i [den här guiden](../../role-based-access-control/built-in-roles.md).

```python
## Create a new logistic regression object for the model
logReg = LogisticRegression(maxIter=10, regParam=0.3, labelCol = 'tipped')

## The formula for the model
classFormula = RFormula(formula="tipped ~ pickupHour + weekdayVec + passengerCount + tripTimeSecs + tripDistance + fareAmount + paymentType+ trafficTimeBinsVec")

## Undertake training and create a logistic regression model
lrModel = Pipeline(stages=[classFormula, logReg]).fit(train_data_df)

## Saving the model is optional, but it's another form of inter-session cache
datestamp = datetime.now().strftime('%m-%d-%Y-%s')
fileName = "lrModel_" + datestamp
logRegDirfilename = fileName
lrModel.save(logRegDirfilename)

## Predict tip 1/0 (yes/no) on the test dataset; evaluation using area under ROC
predictions = lrModel.transform(test_data_df)
predictionAndLabels = predictions.select("label","prediction").rdd
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)
```

Utdata från den här cellen är:

```shell
Area under ROC = 0.9779470729751403
```

## <a name="create-a-visual-representation-of-the-prediction"></a>Skapa en visuell representation av förutsägelsen

Nu kan du skapa en slutgiltig visualisering som hjälper dig att få en anledning till resultatet av testet. En [Roc kurva](https://en.wikipedia.org/wiki/Receiver_operating_characteristic) är ett sätt att granska resultatet.

```python
## Plot the ROC curve; no need for pandas, because this uses the modelSummary object
modelSummary = lrModel.stages[-1].summary

plt.plot([0, 1], [0, 1], 'r--')
plt.plot(modelSummary.roc.select('FPR').collect(),
         modelSummary.roc.select('TPR').collect())
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.show()
```

![Diagram som visar ROC-kurvan för Logistisk regression i tips modellen.](./media/apache-spark-machine-learning-mllib-notebook/nyc-taxi-roc.png)

## <a name="shut-down-the-spark-instance"></a>Stäng av Spark-instansen

När du har kört programmet stänger du antecknings boken för att frigöra resurserna genom att stänga fliken. Eller Välj **avsluta sessionen** från panelen status längst ned i antecknings boken.

## <a name="see-also"></a>Se även

- [Översikt: Apache Spark på Azure Synapse Analytics](apache-spark-overview.md)

## <a name="next-steps"></a>Nästa steg

- [Dokumentation om .NET för Apache Spark](/dotnet/spark)
- [Azure Synapse Analytics](../index.yml)
- [Apache Spark officiell dokumentation](https://spark.apache.org/docs/2.4.5/)

>[!NOTE]
> En del av den officiella Apache Spark dokumentationen använder Spark-konsolen, som inte är tillgänglig på Apache Spark i Azure Synapse Analytics. Använd [antecknings boken](../quickstart-apache-spark-notebook.md) eller [IntelliJ](../spark/intellij-tool-synapse.md) -upplevelser i stället.