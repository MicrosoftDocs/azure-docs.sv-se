---
title: Operationalisera Spark – inbyggda Machine Learning-modeller – team data science process
description: Så här läser du in och betygs ätt inlärnings modeller lagrade i Azure Blob Storage (WASB) med python.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: bb38a76de41885b6f39a1c6dce7c44bcb52a4d60
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "86027451"
---
# <a name="operationalize-spark-built-machine-learning-models"></a>Operationalisera Spark – inbyggda Machine Learning-modeller

Det här avsnittet visar hur du operationalisera en sparad Machine Learning-modell (ML) med python på HDInsight Spark-kluster. Den beskriver hur du läser in maskin inlärnings modeller som har skapats med Spark-MLlib och lagrats i Azure Blob Storage (WASB) och hur du visar dem med data uppsättningar som också har lagrats i WASB. Det visar hur du förbearbetar indata, transformerar funktioner med hjälp av indexerings-och kodnings funktionerna i MLlib Toolkit och hur du skapar ett etikettat punkt data objekt som kan användas som indata för poängsättning med ML-modeller. De modeller som används för poängsättning är linjär regression, Logistisk regression, slumpmässiga skogs modeller och träd modeller för tonings förstärkning.

## <a name="spark-clusters-and-jupyter-notebooks"></a>Spark-kluster och Jupyter-anteckningsböcker
Installations stegen och koden för att operationalisera en ML-modell finns i den här genom gången för att använda ett HDInsight Spark 1,6-kluster samt ett Spark 2,0-kluster. Koden för de här procedurerna finns också i Jupyter Notebooks.

### <a name="notebook-for-spark-16"></a>Notebook för Spark 1,6
[PySpark-Machine-Learning-data-science-Spark-Model-förbrukning. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) Jupyter Notebook visar hur du operationalisera en sparad modell med python i HDInsight-kluster. 

### <a name="notebook-for-spark-20"></a>Notebook för Spark 2,0
Om du vill ändra den Jupyter Notebook för Spark 1,6 att använda med ett HDInsight Spark 2,0-kluster ersätter du python-kodfragmentet med [den här filen](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py). Den här koden visar hur du använder de modeller som skapats i Spark 2,0.


## <a name="prerequisites"></a>Förutsättningar

1. Du behöver ett Azure-konto och ett Spark 1,6-kluster (eller Spark 2,0) för att slutföra den här genom gången. Mer information om hur du uppfyller dessa krav finns i [Översikt över data vetenskap med Spark på Azure HDInsight](spark-overview.md) . Avsnittet innehåller också en beskrivning av NYC 2013 taxi-data som används här och instruktioner om hur man kör kod från en Jupyter-anteckningsbok i Spark-klustret. 
2. Skapa maskin inlärnings modeller som ska poängas här genom att gå igenom [data utforskningen och modelleringen med Spark](spark-data-exploration-modeling.md) -avsnittet för Spark 1,6-klustret eller Spark 2,0-anteckningsboken. 
3. De bärbara Spark 2,0-datorerna använder ytterligare en data uppsättning för klassificerings uppgiften, den välkända flyg data uppsättningen från 2011 och 2012. En beskrivning av antecknings böckerna och länkar till dem finns i [Readme.MD](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) för GitHub-databasen som innehåller dem. Dessutom är koden här och i de länkade antecknings böckerna generisk och bör fungera på alla Spark-kluster. Om du inte använder HDInsight Spark kan konfigurations-och hanterings stegen för klustret skilja sig något från vad som visas här. 

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Installation: lagrings platser, bibliotek och den förinställda Spark-kontexten
Spark kan läsa och skriva till en Azure Storage Blob (WASB). Så alla dina befintliga data som lagras kan bearbetas med Spark och resultaten lagras igen i WASB.

Om du vill spara modeller eller filer i WASB måste sökvägen anges korrekt. Standard behållaren som är kopplad till Spark-klustret kan refereras med hjälp av en sökväg som börjar med: *"wasb///"*. I följande kod exempel anges platsen för de data som ska läsas och sökvägen för den modell lagrings katalog som modellens utdata sparas i. 

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Ange katalog Sök vägar för lagrings platser i WASB
Modeller sparas i: "wasb:///user/remoteuser/NYCTaxi/Models". Om den här sökvägen inte anges korrekt, läses inte modeller in för poängsättning.

De resultat resultat som har sparats i: "wasb:///user/remoteuser/NYCTaxi/ScoredResults". Om sökvägen till mappen är felaktig sparas inte resultaten i den mappen.   

> [!NOTE]
> Sökvägen för fil Sök vägar kan kopieras och klistras in i plats hållarna i den här koden från utdatan från den sista cellen i **datorn-inlärning-data-science-Spark-data-utforskning-modellering. ipynb** Notebook.   
> 
> 

Här är koden för att ange katalog Sök vägar: 

```python
# LOCATION OF DATA TO BE SCORED (TEST DATA)
taxi_test_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Test.tsv";

# SET THE MODEL STORAGE DIRECTORY PATH 
# NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 

# SET SCORDED RESULT DIRECTORY PATH
# NOTE THE LAST BACKSLASH IN THIS PATH IS NEEDED
scoredResultDir = "wasb:///user/remoteuser/NYCTaxi/ScoredResults/"; 

# FILE LOCATIONS FOR THE MODELS TO BE SCORED
logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-04-1817_40_35.796789"
linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-04-1817_44_00.993832"
randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-04-1817_42_58.899412"
randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-04-1817_44_27.204734"
BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-04-1817_43_16.354770"
BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-04-1817_44_46.206262"

# RECORD START TIME
import datetime
datetime.datetime.now()
```

**UTDATAPARAMETRAR**

DateTime. DateTime (2016, 4, 25, 23, 56, 19, 229403)

### <a name="import-libraries"></a>Importera bibliotek
Ange Spark-kontext och importera nödvändiga bibliotek med följande kod

```python
#IMPORT LIBRARIES
import pyspark
from pyspark import SparkConf
from pyspark import SparkContext
from pyspark.sql import SQLContext
import matplotlib
import matplotlib.pyplot as plt
from pyspark.sql import Row
from pyspark.sql.functions import UserDefinedFunction
from pyspark.sql.types import *
import atexit
from numpy import array
import numpy as np
import datetime
```

### <a name="preset-spark-context-and-pyspark-magics"></a>Förinställt Spark-kontext och PySpark MAGICS
PySpark-kernelerna som medföljer Jupyter notebook-datorer har en förinställd kontext. Därför behöver du inte ange Spark-eller Hive-kontexterna explicit innan du börjar arbeta med programmet som du utvecklar. Dessa kontexter är tillgängliga som standard:

* SC – för Spark 
* sqlContext-för Hive

PySpark-kärnan innehåller fördefinierade "MAGICS", som är särskilda kommandon som du kan anropa med%%. Det finns två sådana kommandon som används i dessa kod exempel.

* **%% lokal** Angav att koden i efterföljande rader körs lokalt. Koden måste vara en giltig python-kod.
* **%% SQL-o \<variable name>** 
* Kör en Hive-fråga mot sqlContext. Om-o-parametern skickas sparas resultatet av frågan i den lokala python-kontexten%% som en Pandas-dataframe.

Mer information om kerneler för Jupyter-anteckningsböcker och de fördefinierade "magiska" som de tillhandahåller finns i [kernels som är tillgängliga för Jupyter-anteckningsböcker med HDInsight Spark Linux-kluster i HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="ingest-data-and-create-a-cleaned-data-frame"></a>Mata in data och skapa en rensad data ram
Det här avsnittet innehåller koden för en serie uppgifter som krävs för att mata in data som ska poängas. Läs i ett kopplat 0,1%-exempel på taxi resan och avgifts filen (lagras som en. TSV-fil), formatera data och skapa sedan en ren data-ram.

Taxi-och biljett-filerna anslöts utifrån proceduren i avsnittet: [team data science-processen i praktiken: använda HDInsight Hadoop-kluster](hive-walkthrough.md) .

```python
# INGEST DATA AND CREATE A CLEANED DATA FRAME

# RECORD START TIME
timestart = datetime.datetime.now()

# IMPORT FILE FROM PUBLIC BLOB
taxi_test_file = sc.textFile(taxi_test_file_loc)

# GET SCHEMA OF THE FILE FROM HEADER
taxi_header = taxi_test_file.filter(lambda l: "medallion" in l)

# PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
taxi_temp = taxi_test_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
        .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                        float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                        float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))

# GET SCHEMA OF THE FILE FROM HEADER
schema_string = taxi_test_file.first()
fields = [StructField(field_name, StringType(), True) for field_name in schema_string.split('\t')]
fields[7].dataType = IntegerType() #Pickup hour
fields[8].dataType = IntegerType() # Pickup week
fields[9].dataType = IntegerType() # Weekday
fields[10].dataType = IntegerType() # Passenger count
fields[11].dataType = FloatType() # Trip time in secs
fields[12].dataType = FloatType() # Trip distance
fields[19].dataType = FloatType() # Fare amount
fields[20].dataType = FloatType() # Surcharge
fields[21].dataType = FloatType() # Mta_tax
fields[22].dataType = FloatType() # Tip amount
fields[23].dataType = FloatType() # Tolls amount
fields[24].dataType = FloatType() # Total amount
fields[25].dataType = IntegerType() # Tipped or not
fields[26].dataType = IntegerType() # Tip class
taxi_schema = StructType(fields)

# CREATE DATA FRAME
taxi_df_test = sqlContext.createDataFrame(taxi_temp, taxi_schema)

# CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
taxi_df_test_cleaned = taxi_df_test.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
    .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
    .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
    .drop('direct_distance').drop('surcharge')\
    .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )

# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
taxi_df_test_cleaned.cache()
taxi_df_test_cleaned.count()

# REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
taxi_df_test_cleaned.registerTempTable("taxi_test")

# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**UTDATAPARAMETRAR**

Tids åtgång för att köra ovanför cell: 46,37 sekunder

## <a name="prepare-data-for-scoring-in-spark"></a>Förbereda data för poängsättning i Spark
Det här avsnittet visar hur du kan indexera, koda och skala kategoriska-funktioner för att förbereda dem för användning i MLlib övervakade inlärnings algoritmer för klassificering och regression.

### <a name="feature-transformation-index-and-encode-categorical-features-for-input-into-models-for-scoring"></a>Funktions omvandling: indexera och koda kategoriska-funktioner för inmatade modeller för poängsättning
I det här avsnittet visas hur du indexerar kategoriska-data med hjälp av `StringIndexer` och kodar funktioner med `OneHotEncoder` indata till modeller.

[StringIndexer](https://spark.apache.org/docs/latest/ml-features.html#stringindexer) kodar en sträng kolumn med etiketter till en kolumn med etikett index. Indexen ordnas efter etikett frekvenser. 

[OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) mappar en kolumn med etikett index till en kolumn med binära vektorer, med högst ett enda värde. Den här kodningen tillåter algoritmer som förväntar sig kontinuerliga värde funktioner, till exempel Logistisk regression, som ska användas för kategoriska-funktioner.

```python
#INDEX AND ONE-HOT ENCODE CATEGORICAL FEATURES

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer

# CREATE FOUR BUCKETS FOR TRAFFIC TIMES
sqlStatement = """
    SELECT *,
    CASE
     WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
     WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
     WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
     WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
    END as TrafficTimeBins
    FROM taxi_test 
"""
taxi_df_test_with_newFeatures = sqlContext.sql(sqlStatement)

# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
taxi_df_test_with_newFeatures.cache()
taxi_df_test_with_newFeatures.count()

# INDEX AND ONE-HOT ENCODING
stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
model = stringIndexer.fit(taxi_df_test_with_newFeatures) # Input data-frame is the cleaned one from above
indexed = model.transform(taxi_df_test_with_newFeatures)
encoder = OneHotEncoder(dropLast=False, inputCol="vendorIndex", outputCol="vendorVec")
encoded1 = encoder.transform(indexed)

# INDEX AND ENCODE RATE_CODE
stringIndexer = StringIndexer(inputCol="rate_code", outputCol="rateIndex")
model = stringIndexer.fit(encoded1)
indexed = model.transform(encoded1)
encoder = OneHotEncoder(dropLast=False, inputCol="rateIndex", outputCol="rateVec")
encoded2 = encoder.transform(indexed)

# INDEX AND ENCODE PAYMENT_TYPE
stringIndexer = StringIndexer(inputCol="payment_type", outputCol="paymentIndex")
model = stringIndexer.fit(encoded2)
indexed = model.transform(encoded2)
encoder = OneHotEncoder(dropLast=False, inputCol="paymentIndex", outputCol="paymentVec")
encoded3 = encoder.transform(indexed)

# INDEX AND ENCODE TRAFFIC TIME BINS
stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
model = stringIndexer.fit(encoded3)
indexed = model.transform(encoded3)
encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
encodedFinal = encoder.transform(indexed)

# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**UTDATAPARAMETRAR**

Tids åtgång för att köra ovanför cell: 5,37 sekunder

### <a name="create-rdd-objects-with-feature-arrays-for-input-into-models"></a>Skapa RDD-objekt med funktions mat ris för inmatade modeller
Det här avsnittet innehåller kod som visar hur du kan indexera kategoriska text data som ett RDD-objekt och en-frekvent kod så att det kan användas för att träna och testa MLlib logistik regression och trädbaserade modeller. Indexerade data lagras i [elastiska data mängds objekt (RDD)](https://spark.apache.org/docs/latest/api/java/org/apache/spark/rdd/RDD.html) . RDD är den grundläggande abstraktionen i Spark. Ett RDD-objekt representerar en oföränderlig, partitionerad samling element som kan köras parallellt med Spark.

Den innehåller också kod som visar hur du skalar data med `StandardScalar` MLlib för användning i linjär regression med Stochastic gradient brantaste (SGD), en populär algoritm för att träna en mängd olika Machine Learning-modeller. [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) används för att skala funktionerna till enhets avvikelse. Funktions skalning, även kallat data normalisering, säkerställer att funktioner med de vanligaste värdena för uppfyllande värden inte får orimlig väg i mål funktionen. 

```python
# CREATE RDD OBJECTS WITH FEATURE ARRAYS FOR INPUT INTO MODELS

# RECORD START TIME
timestart = datetime.datetime.now()

# IMPORT LIBRARIES
from pyspark.mllib.linalg import Vectors
from pyspark.mllib.feature import StandardScaler, StandardScalerModel
from pyspark.mllib.util import MLUtils
from numpy import array

# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
def parseRowIndexingBinary(line):
    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                         line.trip_distance, line.fare_amount])
    return  features

# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC REGRESSION MODELS
def parseRowOneHotBinary(line):
    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
    return  features

# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
def parseRowIndexingRegression(line):
    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                         line.trip_distance, line.fare_amount])
    return  features

# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
def parseRowOneHotRegression(line):
    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
    return  features

# FOR BINARY CLASSIFICATION TRAINING AND TESTING
indexedTESTbinary = encodedFinal.map(parseRowIndexingBinary)
oneHotTESTbinary = encodedFinal.map(parseRowOneHotBinary)

# FOR REGRESSION CLASSIFICATION TRAINING AND TESTING
indexedTESTreg = encodedFinal.map(parseRowIndexingRegression)
oneHotTESTreg = encodedFinal.map(parseRowOneHotRegression)

# SCALING FEATURES FOR LINEARREGRESSIONWITHSGD MODEL
scaler = StandardScaler(withMean=False, withStd=True).fit(oneHotTESTreg)
oneHotTESTregScaled = scaler.transform(oneHotTESTreg)

# CACHE RDDS IN MEMORY
indexedTESTbinary.cache();
oneHotTESTbinary.cache();
indexedTESTreg.cache();
oneHotTESTreg.cache();
oneHotTESTregScaled.cache();

# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**UTDATAPARAMETRAR**

Tids åtgång för att köra ovanför cell: 11,72 sekunder

## <a name="score-with-the-logistic-regression-model-and-save-output-to-blob"></a>Poängen med logistik Regressions modellen och spara utdata till BLOB
Koden i det här avsnittet visar hur du läser in en logistik Regressions modell som har sparats i Azure Blob Storage och använder den för att förutsäga om ett tips har betalats på en taxi resa, visar det med standard klassificerings mått och sparar och ritar sedan resultaten till Blob Storage. De resultat som visas lagras i RDD-objekt. 

```python
# SCORE AND EVALUATE LOGISTIC REGRESSION MODEL

# RECORD START TIME
timestart = datetime.datetime.now()

# IMPORT LIBRARIES
from pyspark.mllib.classification import LogisticRegressionModel

## LOAD SAVED MODEL
savedModel = LogisticRegressionModel.load(sc, logisticRegFileLoc)
predictions = oneHotTESTbinary.map(lambda features: (float(savedModel.predict(features))))

## SAVE SCORED RESULTS (RDD) TO BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp + ".txt";
dirfilename = scoredResultDir + logisticregressionfilename;
predictions.saveAsTextFile(dirfilename)


# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
```

**UTDATAPARAMETRAR**

Tids åtgång för att köra ovanför cell: 19,22 sekunder

## <a name="score-a-linear-regression-model"></a>Score en linjär Regressions modell
Vi använde [LinearRegressionWithSGD](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.regression.LinearRegressionWithSGD) för att träna en linjär Regressions modell med Stochastic gradient BRANTASTE (SGD) för optimering för att förutsäga den mängd Tip som du betalar. 

Koden i det här avsnittet visar hur du läser in en linjär Regressions modell från Azure Blob Storage, poängen med skalade variabler och sedan sparar tillbaka resultatet till blobben.

```python
#SCORE LINEAR REGRESSION MODEL

# RECORD START TIME
timestart = datetime.datetime.now()

#LOAD LIBRARIES
from pyspark.mllib.regression import LinearRegressionWithSGD, LinearRegressionModel

# LOAD MODEL AND SCORE USING ** SCALED VARIABLES **
savedModel = LinearRegressionModel.load(sc, linearRegFileLoc)
predictions = oneHotTESTregScaled.map(lambda features: (float(savedModel.predict(features))))

# SAVE RESULTS
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
dirfilename = scoredResultDir + linearregressionfilename;
predictions.saveAsTextFile(dirfilename)

# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**UTDATAPARAMETRAR**

Tids åtgång för att köra ovanför cell: 16,63 sekunder

## <a name="score-classification-and-regression-random-forest-models"></a>Resultat klassificering och Regressions slumpmässiga skogs modeller
Koden i det här avsnittet visar hur du läser in de sparade klassificerings-och Regressions modellerna som är sparade i Azure Blob Storage, visar prestanda med standardklassificeraren och Regressions mått och sparar sedan resultatet till Blob Storage.

[Slumpmässiga skogar](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) är ensembler för besluts träd.  De kombinerar många besluts träd för att minska risken för överanpassning. Slumpmässiga skogar kan hantera kategoriska-funktioner, utöka till klassificerings inställningen multiklass, behöver inte funktion skalning och kan fånga icke-linjära och funktions interaktioner. Slumpmässiga skogar är en av de mest fungerande maskin inlärnings modellerna för klassificering och regression.

[Spark. mllib](https://spark.apache.org/mllib/) stöder slumpmässiga skogar för binär och multiklass-klassificering och för regression med både kontinuerliga och kategoriska funktioner. 

```python
# SCORE RANDOM FOREST MODELS FOR CLASSIFICATION AND REGRESSION

# RECORD START TIME
timestart = datetime.datetime.now()

#IMPORT MLLIB LIBRARIES    
from pyspark.mllib.tree import RandomForest, RandomForestModel


# CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
savedModel = RandomForestModel.load(sc, randomForestClassificationFileLoc)
predictions = savedModel.predict(indexedTESTbinary)

# SAVE RESULTS
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
rfclassificationfilename = "RandomForestClassification_" + datestamp + ".txt";
dirfilename = scoredResultDir + rfclassificationfilename;
predictions.saveAsTextFile(dirfilename)


# REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB
savedModel = RandomForestModel.load(sc, randomForestRegFileLoc)
predictions = savedModel.predict(indexedTESTreg)

# SAVE RESULTS
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
rfregressionfilename = "RandomForestRegression_" + datestamp + ".txt";
dirfilename = scoredResultDir + rfregressionfilename;
predictions.saveAsTextFile(dirfilename)

# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
```

**UTDATAPARAMETRAR**

Tids åtgång för att köra ovanför cell: 31,07 sekunder

## <a name="score-classification-and-regression-gradient-boosting-tree-models"></a>Poäng klassificering och modell modeller för Regressions tonings förstärkning
Koden i det här avsnittet visar hur du läser in klassificerings-och Regressions tonings träd modeller från Azure Blob Storage, visar prestanda med standardklassificeraren och Regressions mått och sparar sedan resultatet till Blob Storage igen. 

**Spark. mllib** stöder GBTS för binära klassificering och för regression med både kontinuerliga och kategoriska funktioner. 

GBTS ( [gradient Boosting trees](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) ) är ensembler i besluts träd. GBTS träna besluts träd iterativt för att minimera en förlust funktion. GBTS kan hantera kategoriska-funktioner, kräver inte funktions skalning och kan fånga icke-linjära och funktions interaktioner. Den här algoritmen kan också användas i en inställning för multiklass-klassificering.

```python
# SCORE GRADIENT BOOSTING TREE MODELS FOR CLASSIFICATION AND REGRESSION

# RECORD START TIME
timestart = datetime.datetime.now()

#IMPORT MLLIB LIBRARIES
from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

# CLASSIFICATION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

#LOAD AND SCORE THE MODEL
savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeClassificationFileLoc)
predictions = savedModel.predict(indexedTESTbinary)

# SAVE RESULTS
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp + ".txt";
dirfilename = scoredResultDir + btclassificationfilename;
predictions.saveAsTextFile(dirfilename)


# REGRESSION: LOAD SAVED MODEL, SCORE AND SAVE RESULTS BACK TO BLOB

# LOAD AND SCORE MODEL 
savedModel = GradientBoostedTreesModel.load(sc, BoostedTreeRegressionFileLoc)
predictions = savedModel.predict(indexedTESTreg)

# SAVE RESULTS
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
btregressionfilename = "GradientBoostingTreeRegression_" + datestamp + ".txt";
dirfilename = scoredResultDir + btregressionfilename;
predictions.saveAsTextFile(dirfilename)


# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**UTDATAPARAMETRAR**

Tids åtgång för att köra ovanför cell: 14,6 sekunder

## <a name="clean-up-objects-from-memory-and-print-scored-file-locations"></a>Rensa objekt från minnes-och utskrifts platser med Poäng

```python
# UNPERSIST OBJECTS CACHED IN MEMORY
taxi_df_test_cleaned.unpersist()
indexedTESTbinary.unpersist();
oneHotTESTbinary.unpersist();
indexedTESTreg.unpersist();
oneHotTESTreg.unpersist();
oneHotTESTregScaled.unpersist();


# PRINT OUT PATH TO SCORED OUTPUT FILES
print "logisticRegFileLoc: " + logisticregressionfilename;
print "linearRegFileLoc: " + linearregressionfilename;
print "randomForestClassificationFileLoc: " + rfclassificationfilename;
print "randomForestRegFileLoc: " + rfregressionfilename;
print "BoostedTreeClassificationFileLoc: " + btclassificationfilename;
print "BoostedTreeRegressionFileLoc: " + btregressionfilename;
```

**UTDATAPARAMETRAR**

logisticRegFileLoc: LogisticRegressionWithLBFGS_2016-05-0317_22_38.953814.txt

linearRegFileLoc: LinearRegressionWithSGD_2016 05 -0317 _22_ 58.878949

randomForestClassificationFileLoc: RandomForestClassification_2016-05-0317_23_15.939247.txt

randomForestRegFileLoc: RandomForestRegression_2016-05-0317_23_31.459140.txt

BoostedTreeClassificationFileLoc: GradientBoostingTreeClassification_2016-05-0317_23_49.648334.txt

BoostedTreeRegressionFileLoc: GradientBoostingTreeRegression_2016-05-0317_23_56.860740.txt

## <a name="consume-spark-models-through-a-web-interface"></a>Använda Spark-modeller via ett webb gränssnitt
Spark tillhandahåller en mekanism för att fjärrskicka batchjobb eller interaktiva frågor via ett REST-gränssnitt med en komponent som heter livy. Livy är aktiverat som standard i ditt HDInsight Spark-kluster. Mer information om livy finns i: [Skicka Spark-jobb via fjärr anslutning med livy](../../hdinsight/spark/apache-spark-livy-rest-interface.md). 

Du kan använda livy för att skicka ett jobb som batchen skickar en fil som lagras i en Azure-blob och sedan skriver resultatet till en annan blob. Det gör du genom att överföra python-skriptet från  
[GitHub](https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/Spark/Python/ConsumeGBNYCReg.py) till bloben för Spark-klustret. Du kan använda ett verktyg som **Microsoft Azure Storage Explorer** eller **AzCopy** för att kopiera skriptet till kluster-bloben. I vårt fall överförde vi skriptet till **_wasb:///example/python/ConsumeGBNYCReg.py_**.   

> [!NOTE]
> De åtkomst nycklar som du behöver finns på portalen för det lagrings konto som är kopplat till Spark-klustret. 
> 
> 

När det här skriptet har laddats upp till den här platsen körs det i Spark-klustret i en distribuerad kontext. Den läser in modellen och kör förutsägelser på indatafiler baserat på modellen.  

Du kan anropa det här skriptet via fjärr anslutning genom att göra en enkel HTTPS/REST-begäran på livy.  Här är ett spiral kommando för att skapa HTTP-begäran för att anropa python-skriptet via fjärr anslutning. Ersätt CLUSTERLOGIN, CLUSTERPASSWORD, kluster namn med lämpliga värden för ditt Spark-kluster.

```console
# CURL COMMAND TO INVOKE PYTHON SCRIPT WITH HTTP REQUEST

curl -k --user "CLUSTERLOGIN:CLUSTERPASSWORD" -X POST --data "{\"file\": \"wasb:///example/python/ConsumeGBNYCReg.py\"}" -H "Content-Type: application/json" https://CLUSTERNAME.azurehdinsight.net/livy/batches
```

Du kan använda valfritt språk på fjärrdatorn för att anropa Spark-jobbet via livy genom att göra ett enkelt HTTPS-anrop med grundläggande autentisering.   

> [!NOTE]
> Det är praktiskt att använda python-begärandena-biblioteket när du gör detta HTTP-anrop, men det är för närvarande inte installerat som standard i Azure Functions. Därför används äldre HTTP-bibliotek i stället.   
> 
> 

Här är python-koden för HTTP-anropet:

```python
#MAKE AN HTTPS CALL ON LIVY. 

import os

# OLDER HTTP LIBRARIES USED HERE INSTEAD OF THE REQUEST LIBRARY AS THEY ARE AVAILABLE BY DEFAULT
import httplib, urllib, base64

# REPLACE VALUE WITH ONES FOR YOUR SPARK CLUSTER
host = '<spark cluster name>.azurehdinsight.net:443'
username='<username>'
password='<password>'

#AUTHORIZATION
conn = httplib.HTTPSConnection(host)
auth = base64.encodestring('%s:%s' % (username, password)).replace('\n', '')
headers = {'Content-Type': 'application/json', 'Authorization': 'Basic %s' % auth}

# SPECIFY THE PYTHON SCRIPT TO RUN ON THE SPARK CLUSTER
# IN THE FILE PARAMETER OF THE JSON POST REQUEST BODY
r=conn.request("POST", '/livy/batches', '{"file": "wasb:///example/python/ConsumeGBNYCReg.py"}', headers )
response = conn.getresponse().read()
print(response)
conn.close()
```

Du kan också lägga till den här python-koden till [Azure Functions](https://azure.microsoft.com/documentation/services/functions/) för att utlösa ett Spark-jobb som tar en BLOB baserat på olika händelser som en timer, skapande eller uppdatering av en blob. 

Om du föredrar en kod kostnads fri klient upplevelse använder du [Azure Logic Apps](https://azure.microsoft.com/documentation/services/app-service/logic/) för att anropa Spark-batch-poängen genom att definiera en HTTP-åtgärd i **Logic Apps designer** och ange dess parametrar. 

* Skapa en ny Logic-app från Azure Portal genom att välja **+ ny**  ->  **webb och mobilt**  ->  **Logic app**. 
* För att öppna **Logic Apps designer**, ange namnet på Logic App och App Service plan.
* Välj en HTTP-åtgärd och ange de parametrar som visas i följande figur:

![Logikappdesigner](./media/spark-model-consumption/spark-logica-app-client.png)

## <a name="whats-next"></a>Nästa steg
**Cross-Validation och Cross-parameter svep**: se [Avancerad data granskning och modellering med Spark](spark-advanced-data-exploration-modeling.md) om hur modeller kan tränas med kors validering och rensning av Hyper-parameter.

