---
title: Data utforskning och modellering med process för Spark-team data science
description: Visar data utforsknings-och modellerings funktionerna i Spark MLlib Toolkit på HDInsight Spark.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: sample
ms.date: 06/03/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath, contperf-fy20q4
ms.openlocfilehash: 1ef6c678110037a4aed24ef40631b73a04ca6a9d
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97028095"
---
# <a name="data-exploration-and-modeling-with-spark"></a>Datagranskning och modellering med Spark

Lär dig hur du använder HDInsight Spark för att träna maskin inlärnings modeller för taxi pris förutsägelse med Spark MLlib.

Det här exemplet demonstrerar de olika stegen i [team data science-processen](./index.yml). En delmängd av NYC taxi-resan och biljett 2013-datauppsättningen används för att läsa in, utforska och förbereda data. Sedan kan du använda Spark-MLlib, binära klassificerings-och Regressions modeller för att förutsäga om ett tips kommer att betalas för resan och uppskatta Tip-mängden.

## <a name="prerequisites"></a>Förutsättningar

Du behöver ett Azure-konto och ett Spark 1,6-kluster (eller Spark 2,0) för att slutföra den här genom gången. Mer information om hur du uppfyller dessa krav finns i [Översikt över data vetenskap med Spark på Azure HDInsight](spark-overview.md) . Avsnittet innehåller också en beskrivning av NYC 2013 taxi-data som används här och instruktioner om hur man kör kod från en Jupyter-anteckningsbok i Spark-klustret. 

### <a name="spark-clusters-and-notebooks"></a>Spark-kluster och notebook-datorer

Installations steg och kod beskrivs i den här genom gången av en HDInsight Spark 1,6. Men Jupyter Notebooks finns för både HDInsight Spark 1,6-kluster och Spark 2,0-kluster. En beskrivning av antecknings böckerna och länkar till dem finns i [Readme.MD](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) för GitHub-databasen som innehåller dem. Dessutom är koden här och i de länkade antecknings böckerna generisk och bör fungera på alla Spark-kluster. Om du inte använder HDInsight Spark kan konfigurations-och hanterings stegen för klustret skilja sig något från vad som visas här. För enkelhetens skull är här länkarna till Jupyter-anteckningsböcker för Spark 1,6 (som ska köras i pySpark-kärnan i Jupyter Notebook-servern) och Spark 2,0 (som ska köras i pySpark3-kärnan i Jupyter Notebook-servern):

- [Spark 1,6-Notebook](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/): ger information om hur du utför data utforskning, modellering och poängsättning med flera olika algoritmer.
- [Spark 2,0-Notebook](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/): ger information om hur du utför Regressions-och klassificerings uppgifter. Data uppsättningar kan variera, men stegen och begreppen gäller för olika data uppsättningar.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

> [!NOTE]
> Beskrivningarna nedan är relaterade till användningen av Spark 1,6. För Spark 2,0-versioner ska du använda de antecknings böcker som beskrivs och länkas ovan. 

## <a name="setup"></a>Installation

Spark kan läsa och skriva till Azure Storage Blob (kallas även WASB). Så alla dina befintliga data som lagras kan bearbetas med Spark och resultaten lagras igen i WASB.

Om du vill spara modeller eller filer i WASB måste sökvägen anges korrekt. Standard behållaren som är kopplad till Spark-klustret kan refereras med hjälp av en sökväg som börjar med: "wasb:///". Andra platser refereras till av "wasb://".

### <a name="set-directory-paths-for-storage-locations-in-wasb"></a>Ange katalog Sök vägar för lagrings platser i WASB

I följande kod exempel anges platsen för de data som ska läsas och sökvägen för den modell lagrings katalog som modellens utdata sparas i:

```python
# SET PATHS TO FILE LOCATIONS: DATA AND MODEL STORAGE

# LOCATION OF TRAINING DATA
taxi_train_file_loc = "wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv";

# SET THE MODEL STORAGE DIRECTORY PATH 
# NOTE THAT THE FINAL BACKSLASH IN THE PATH IS NEEDED.
modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/" 
```

### <a name="import-libraries"></a>Importera bibliotek

Konfigurations krav kräver också att du importerar nödvändiga bibliotek. Ange Spark-kontext och importera nödvändiga bibliotek med följande kod:

```python
# IMPORT LIBRARIES
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

PySpark-kernelerna som medföljer Jupyter notebook-datorer har en förinställd kontext. Du behöver inte ange Spark-eller Hive-kontexterna explicit innan du börjar arbeta med programmet som du utvecklar. Dessa kontexter är tillgängliga som standard. Dessa kontexter är:

* SC – för Spark 
* sqlContext-för Hive

PySpark-kärnan innehåller fördefinierade "MAGICS", som är särskilda kommandon som du kan anropa med%%. Det finns två sådana kommandon som används i dessa kod exempel.

* **%% lokal** Anger att koden i efterföljande rader ska köras lokalt. Koden måste vara en giltig python-kod.
* **%% SQL-o \<variable name>** Kör en Hive-fråga mot sqlContext. Om-o-parametern skickas sparas resultatet av frågan i den lokala python-kontexten%% som en Pandas-DataFrame.

Mer information om Jupyter Notebook-kärnor och de fördefinierade "MAGICS" finns i [kernels som är tillgängliga för Jupyter-anteckningsböcker med HDInsight Spark Linux-kluster i HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="load-the-data"></a>Läsa in data

Det första steget i data vetenskaps processen är att mata in data som ska analyseras från källor där finns i data utforsknings-och modell miljö. Miljön är spark i den här genom gången. Det här avsnittet innehåller koden för att slutföra en serie aktiviteter:

* Mata in data exemplet som ska modelleras
* Läs i data uppsättningen för indata (lagras som en. TSV-fil)
* formatera och rensa data
* Skapa och cachelagra objekt (RDD eller data ramar) i minnet
* registrera den som en temporär tabell i SQL-kontext.

Här är koden för data inmatning.

```python
# INGEST DATA

# RECORD START TIME
timestart = datetime.datetime.now()

# IMPORT FILE FROM PUBLIC BLOB
taxi_train_file = sc.textFile(taxi_train_file_loc)

# GET SCHEMA OF THE FILE FROM HEADER
schema_string = taxi_train_file.first()
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

# PARSE FIELDS AND CONVERT DATA TYPE FOR SOME FIELDS
taxi_header = taxi_train_file.filter(lambda l: "medallion" in l)
taxi_temp = taxi_train_file.subtract(taxi_header).map(lambda k: k.split("\t"))\
        .map(lambda p: (p[0],p[1],p[2],p[3],p[4],p[5],p[6],int(p[7]),int(p[8]),int(p[9]),int(p[10]),
                        float(p[11]),float(p[12]),p[13],p[14],p[15],p[16],p[17],p[18],float(p[19]),
                        float(p[20]),float(p[21]),float(p[22]),float(p[23]),float(p[24]),int(p[25]),int(p[26])))


# CREATE DATA FRAME
taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)

# CREATE A CLEANED DATA-FRAME BY DROPPING SOME UN-NECESSARY COLUMNS & FILTERING FOR UNDESIRED VALUES OR OUTLIERS
taxi_df_train_cleaned = taxi_train_df.drop('medallion').drop('hack_license').drop('store_and_fwd_flag').drop('pickup_datetime')\
    .drop('dropoff_datetime').drop('pickup_longitude').drop('pickup_latitude').drop('dropoff_latitude')\
    .drop('dropoff_longitude').drop('tip_class').drop('total_amount').drop('tolls_amount').drop('mta_tax')\
    .drop('direct_distance').drop('surcharge')\
    .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200" )


# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
taxi_df_train_cleaned.cache()
taxi_df_train_cleaned.count()

# REGISTER DATA-FRAME AS A TEMP-TABLE IN SQL-CONTEXT
taxi_df_train_cleaned.registerTempTable("taxi_train")

# PRINT HOW MUCH TIME IT TOOK TO RUN THE CELL
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
```

**UTDATAPARAMETRAR**

Tids åtgång för att köra ovanför cell: 51,72 sekunder

## <a name="explore-the-data"></a>Utforska data

När data har förts in i Spark, är nästa steg i data vetenskaps processen att få djupare förståelse för data genom utforskning och visualisering. I det här avsnittet undersöker vi taxi-data med hjälp av SQL-frågor och ritar upp målvärdena och potentiella funktioner för visuell inspektion. Mer specifikt plottar vi frekvensen av antalet passagerare i taxi resor, frekvensen för Tip-mängder och hur tips varierar efter betalnings belopp och typ.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Rita ett histogram över frekvensen för antalet passagerare i provet i taxi resor

Den här koden och efterföljande kodfragment använder SQL Magic för att fråga exemplet och det lokala Magic för att rita data.

* **SQL Magic ( `%%sql` )** HDInsight PySpark-kärnan stöder enkla infogade HiveQL-frågor mot sqlContext. Argumentet (-o VARIABLE_NAME) behåller SQL-frågans utdata som en Pandas-DataFrame på Jupyter-servern. Den här inställningen gör utdata tillgängligt i lokalt läge.
* **`%%local` Magic** används för att köra kod lokalt på Jupyter-servern, som är huvudnoden för HDInsight-klustret. Normalt använder du `%%local` Magic tillsammans med `%%sql` parametern Magic with-o. Parametern-o behåller utdata från SQL-frågan lokalt och sedan%% Local Magic utlöser nästa uppsättning kodfragment för att köras lokalt mot utdata från SQL-frågorna som är bestående lokalt

Utdata visualiseras automatiskt när du har kört koden.

Den här frågan hämtar antalet resor per passagerare. 

```sql
# PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

# HIVEQL QUERY AGAINST THE sqlContext
%%sql -q -o sqlResults
SELECT passenger_count, COUNT(*) as trip_counts 
FROM taxi_train 
WHERE passenger_count > 0 and passenger_count < 7 
GROUP BY passenger_count 
```

Den här koden skapar en lokal data ram från frågans utdata och ritar data. `%%local`Magic skapar en lokal data ram, `sqlResults` som kan användas för att rita med matplotlib. 

> [!NOTE]
> Detta PySpark Magic används flera gånger i den här genom gången. Om mängden data är stor, bör du prova att skapa en data ram som kan passa i det lokala minnet.

```python
#CREATE LOCAL DATA-FRAME AND USE FOR MATPLOTLIB PLOTTING

# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
# CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
sqlResults
```

Här är koden för att rita upp resor efter antal passagerare

```python
# PLOT PASSENGER NUMBER VS. TRIP COUNTS
%%local
import matplotlib.pyplot as plt
%matplotlib inline

x_labels = sqlResults['passenger_count'].values
fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
fig.set_xticklabels(x_labels)
fig.set_title('Counts of trips by passenger count')
fig.set_xlabel('Passenger count in trips')
fig.set_ylabel('Trip counts')
plt.show()
```

**UTDATAPARAMETRAR**

![Rese frekvens per antal passagerare](./media/spark-data-exploration-modeling/trip-freqency-by-passenger-count.png)

Du kan välja bland flera olika typer av visualiseringar (tabell, cirkel, linje, yta eller stapel) med hjälp av meny knapparna **typ** i antecknings boken. Stolp området visas här.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Rita ett histogram med Tip-mängder och hur Tip-mängden varierar beroende på antalet passagerare och avgifts belopp.

Använd en SQL-fråga för att sampla data.

```sql
# PLOT HISTOGRAM OF TIP AMOUNTS AND VARIATION BY PASSENGER COUNT AND PAYMENT TYPE

# HIVEQL QUERY AGAINST THE sqlContext
%%sql -q -o sqlResults
SELECT fare_amount, passenger_count, tip_amount, tipped 
FROM taxi_train 
WHERE passenger_count > 0 
AND passenger_count < 7 
AND fare_amount > 0 
AND fare_amount < 200 
AND payment_type in ('CSH', 'CRD') 
AND tip_amount > 0 
AND tip_amount < 25
```

Den här kod cellen använder SQL-frågan för att skapa tre data områden.

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# HISTOGRAM OF TIP AMOUNTS AND PASSENGER COUNT
ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()

# TIP BY PASSENGER COUNT
ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
ax2.set_title('Tip amount by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
plt.suptitle('')
plt.show()

# TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
ax.set_title('Tip amount by Fare amount')
ax.set_xlabel('Fare Amount ($)')
ax.set_ylabel('Tip Amount ($)')
plt.axis([-2, 100, -2, 20])
plt.show()
```

**UTDATAPARAMETRAR** 

![Distribution av Tip-belopp](./media/spark-data-exploration-modeling/tip-amount-distribution.png)

![Tips belopp efter antal passagerare](./media/spark-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Tips belopp per avgifts belopp](./media/spark-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="prepare-the-data"></a>Förbereda data

Det här avsnittet beskriver och innehåller koden för procedurer som används för att förbereda data för användning i ML-modellering. Det visar hur du utför följande uppgifter:

* Skapa en ny funktion genom diskretisering timmar i tid-buckets
* Indexera och koda kategoriska-funktioner
* Skapa märkta punkt objekt för inmatade i ML-funktioner
* Skapa en slumpmässig under sampling av data och dela upp den i utbildning och testnings uppsättningar
* Funktionsskalning
* Cachelagra objekt i minnet

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Skapa en ny funktion genom diskretisering timmar i tid-buckets

Den här koden visar hur du skapar en ny funktion genom att diskretisering timmar till trafik tids Bucken och sedan cachelagra den resulterande data ramen i minnet. Om elastiska distribuerade data uppsättningar (RDD) och data ramar används flera gånger, leder cachelagring till förbättrade körnings tider. Därför cachelagrar vi RDD och data ramar i flera steg i genom gången. 

```python
# CREATE FOUR BUCKETS FOR TRAFFIC TIMES
sqlStatement = """
    SELECT *,
    CASE
     WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night" 
     WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush" 
     WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
     WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
    END as TrafficTimeBins
    FROM taxi_train 
"""
taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)

# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
# THE .COUNT() GOES THROUGH THE ENTIRE DATA-FRAME,
# MATERIALIZES IT IN MEMORY, AND GIVES THE COUNT OF ROWS.
taxi_df_train_with_newFeatures.cache()
taxi_df_train_with_newFeatures.count()
```

**UTDATAPARAMETRAR**

126050

### <a name="index-and-encode-categorical-features-for-input-into-modeling-functions"></a>Indexera och koda kategoriska-funktioner för ineffekt i modellerings funktioner

Det här avsnittet visar hur du kan indexera eller koda kategoriska-funktioner för inmatade funktioner i modellerings funktionerna. Modellerings-och förutsägelse funktionerna i MLlib kräver funktioner med kategoriska indata som ska indexeras eller kodas innan de används. Beroende på modellen måste du indexera eller koda dem på olika sätt:  

* **Tree-baserad modellering** kräver att kategorier kodas som numeriska värden (till exempel kan en funktion med tre kategorier kodas med 0, 1, 2). Den här algoritmen tillhandahålls av MLlib [StringIndexer](https://spark.apache.org/docs/latest/ml-features.html#stringindexer) -funktionen. Den här funktionen kodar en sträng kolumn med etiketter till en kolumn med etikett index som sorteras efter etikett frekvenser. Även om det är indexerat med numeriska värden för indata och data hantering kan de trädbaserade algoritmerna anges för att behandla dem korrekt som kategorier. 
* **Logistik-och linjära Regressions modeller** kräver en-frekvent kodning, där till exempel en funktion med tre kategorier kan utökas till tre funktions kolumner, där var och en innehåller 0 eller 1 beroende på en observations kategori. MLlib tillhandahåller funktionen [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) för att göra en snabb kodning. Denna kodare mappar en kolumn med etikett index till en kolumn med binära vektorer, med högst ett enda värde. Den här kodningen tillåter algoritmer som förväntar sig numeriska värde funktioner, till exempel Logistisk regression, som ska användas för kategoriska-funktioner.

Här är koden för att indexera och koda kategoriska-funktioner:

```python
# INDEX AND ENCODE CATEGORICAL FEATURES

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES    
from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, VectorIndexer

# INDEX AND ENCODE VENDOR_ID
stringIndexer = StringIndexer(inputCol="vendor_id", outputCol="vendorIndex")
model = stringIndexer.fit(taxi_df_train_with_newFeatures) # Input data-frame is the cleaned one from above
indexed = model.transform(taxi_df_train_with_newFeatures)
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

# INDEX AND TRAFFIC TIME BINS
stringIndexer = StringIndexer(inputCol="TrafficTimeBins", outputCol="TrafficTimeBinsIndex")
model = stringIndexer.fit(encoded3)
indexed = model.transform(encoded3)
encoder = OneHotEncoder(dropLast=False, inputCol="TrafficTimeBinsIndex", outputCol="TrafficTimeBinsVec")
encodedFinal = encoder.transform(indexed)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**UTDATAPARAMETRAR**

Tids åtgång för att köra ovanför cell: 1,28 sekunder

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Skapa märkta punkt objekt för inmatade i ML-funktioner

Det här avsnittet innehåller kod som visar hur du kan indexera kategoriska text data som en etikettad punkt data typ och koda den så att den kan användas för att träna och testa MLlib logistik regression och andra klassificerings modeller. Märkta punkt objekt är elastiska distribuerade data uppsättningar (RDD) formaterade på ett sätt som indata för de flesta av ML-algoritmer används i MLlib. En [märkt punkt](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) är en lokal vektor, antingen kompakt eller sparse, som är associerad med en etikett/ett svar.  

Det här avsnittet innehåller kod som visar hur du kan indexera kategoriska text data som en [etikettad punkt](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) data typ och koda den så att den kan användas för att träna och testa MLlib logistik regression och andra klassificerings modeller. Märkta punkt objekt är elastiska distribuerade data uppsättningar (RDD) bestående av en etikett (mål/svar-variabel) och funktions vektor. Det här formatet krävs som inmatat av många ML-algoritmer i MLlib.

Här är koden för att indexera och koda text funktioner för binär klassificering.

```python
# FUNCTIONS FOR BINARY CLASSIFICATION

# LOAD LIBRARIES
from pyspark.mllib.regression import LabeledPoint
from numpy import array

# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
def parseRowIndexingBinary(line):
    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex,
                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                         line.trip_distance, line.fare_amount])
    labPt = LabeledPoint(line.tipped, features)
    return  labPt

# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC REGRESSION MODELS
def parseRowOneHotBinary(line):
    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
    labPt = LabeledPoint(line.tipped, features)
    return  labPt
```

Här är koden för att koda och indexera kategoriska text funktioner för linjär Regressions analys.

```python
# FUNCTIONS FOR REGRESSION WITH TIP AMOUNT AS TARGET VARIABLE

# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
def parseRowIndexingRegression(line):
    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.TrafficTimeBinsIndex, 
                         line.pickup_hour, line.weekday, line.passenger_count, line.trip_time_in_secs, 
                         line.trip_distance, line.fare_amount])

    labPt = LabeledPoint(line.tip_amount, features)
    return  labPt

# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO LINEAR REGRESSION MODELS
def parseRowOneHotRegression(line):
    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                                        line.vendorVec.toArray(), line.rateVec.toArray(), 
                                        line.paymentVec.toArray(), line.TrafficTimeBinsVec.toArray()), axis=0)
    labPt = LabeledPoint(line.tip_amount, features)
    return  labPt
```

### <a name="create-a-random-subsampling-of-the-data-and-split-it-into-training-and-testing-sets"></a>Skapa en slumpmässig under sampling av data och dela upp den i utbildning och testnings uppsättningar

Den här koden skapar en slumpmässig sampling av data (25% används här). Även om det inte krävs för det här exemplet på grund av storleken på data uppsättningen, visar vi hur du kan prova här så att du vet hur du använder det för ditt eget problem när det behövs. När exempel är stora kan samplingen Spara avsevärd tid och utbildnings modeller. Nästa steg är att dela exemplet i en utbildnings del (75% här) och en test del (25% här) för användning i klassificerings-och Regressions modellering.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.sql.functions import rand

# SPECIFY SAMPLING AND SPLITTING FRACTIONS
samplingFraction = 0.25;
trainingFraction = 0.75; testingFraction = (1-trainingFraction);
seed = 1234;
encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)

# SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST
# INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS (FOR USE LATER IN AN ADVANCED TOPIC)
dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);

# FOR BINARY CLASSIFICATION TRAINING AND TESTING
indexedTRAINbinary = trainData.map(parseRowIndexingBinary)
indexedTESTbinary = testData.map(parseRowIndexingBinary)
oneHotTRAINbinary = trainData.map(parseRowOneHotBinary)
oneHotTESTbinary = testData.map(parseRowOneHotBinary)

# FOR REGRESSION TRAINING AND TESTING
indexedTRAINreg = trainData.map(parseRowIndexingRegression)
indexedTESTreg = testData.map(parseRowIndexingRegression)
oneHotTRAINreg = trainData.map(parseRowOneHotRegression)
oneHotTESTreg = testData.map(parseRowOneHotRegression)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**UTDATAPARAMETRAR**

Tids åtgång för att köra ovanför cell: 0,24 sekund

### <a name="feature-scaling"></a>Funktionsskalning

Funktions skalning, även kallat data normalisering, säkerställer att funktioner med de vanligaste värdena för uppfyllande värden inte får orimlig väg i mål funktionen. I koden för funktions skalning används [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) för att skala funktionerna till enhets avvikelse. Det tillhandahålls av MLlib för användning i linjär regression med Stochastic gradient brantaste (SGD), en populär algoritm för utbildning av ett brett utbud av andra maskin inlärnings modeller, till exempel regelbundna regressioner eller support vektor maskiner (SVM).

> [!NOTE]
> Vi har hittat LinearRegressionWithSGD-algoritmen för att vara känslig för skalning av funktioner.

Här är koden för att skala variabler för användning med den vanliga linjära SGD-algoritmen.

```python
# FEATURE SCALING

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.regression import LabeledPoint
from pyspark.mllib.linalg import Vectors
from pyspark.mllib.feature import StandardScaler, StandardScalerModel
from pyspark.mllib.util import MLUtils

# SCALE VARIABLES FOR REGULARIZED LINEAR SGD ALGORITHM
label = oneHotTRAINreg.map(lambda x: x.label)
features = oneHotTRAINreg.map(lambda x: x.features)
scaler = StandardScaler(withMean=False, withStd=True).fit(features)
dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
oneHotTRAINregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))

label = oneHotTESTreg.map(lambda x: x.label)
features = oneHotTESTreg.map(lambda x: x.features)
scaler = StandardScaler(withMean=False, withStd=True).fit(features)
dataTMP = label.zip(scaler.transform(features.map(lambda x: Vectors.dense(x.toArray()))))
oneHotTESTregScaled = dataTMP.map(lambda x: LabeledPoint(x[0], x[1]))

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**UTDATAPARAMETRAR**

Tids åtgång för att köra ovanför cell: 13,17 sekunder

### <a name="cache-objects-in-memory"></a>Cachelagra objekt i minnet

Den tid det tar för utbildning och testning av ML-algoritmer kan minskas genom att cachelagra de indata från ram-objekten som används för klassificering, regression och skalade funktioner.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# FOR BINARY CLASSIFICATION TRAINING AND TESTING
indexedTRAINbinary.cache()
indexedTESTbinary.cache()
oneHotTRAINbinary.cache()
oneHotTESTbinary.cache()

# FOR REGRESSION TRAINING AND TESTING
indexedTRAINreg.cache()
indexedTESTreg.cache()
oneHotTRAINreg.cache()
oneHotTESTreg.cache()

# SCALED FEATURES
oneHotTRAINregScaled.cache()
oneHotTESTregScaled.cache()

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**UTDATAPARAMETRAR** 

Tids åtgång för att köra ovanför cell: 0,15 sekund

## <a name="train-a-binary-classification-model"></a>Träna en binär klassificerings modell

I det här avsnittet visas hur du använder tre modeller för den binära klassificerings uppgiften för att förutsäga om ett tips är betalt för en taxi resa. De modeller som visas är:

* Vanlig logistik regression 
* Slumpmässig skogs modell
* Tonings förstärknings träd

Varje modell för modell skapande kod är uppdelad i steg: 

1. **Modellera tränings** data med en parameter uppsättning
2. **Modell utvärdering** av en test data uppsättning med mått
3. **Spara modell** i BLOB för framtida användning

### <a name="classification-using-logistic-regression"></a>Klassificering med Logistisk regression

Koden i det här avsnittet visar hur du tränar, utvärderar och sparar en logistik Regressions modell med [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) som förutsäger om ett tips är betalt för en resa i NYC taxi-resan och pris data uppsättningen.

**Träna logistik Regressions modellen med hjälp av ka och dess parameter Svep**

```python
# LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

# GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD LIBRARIES
from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
from sklearn.metrics import roc_curve,auc
from pyspark.mllib.evaluation import BinaryClassificationMetrics
from pyspark.mllib.evaluation import MulticlassMetrics


# CREATE MODEL WITH ONE SET OF PARAMETERS
logitModel = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, iterations=20, initialWeights=None, 
                                               regParam=0.01, regType='l2', intercept=True, corrections=10, 
                                               tolerance=0.0001, validateData=True, numClasses=2)

# PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
# NOTE: There are 20 coefficient terms for the 10 features, 
#       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
print("Coefficients: " + str(logitModel.weights))
print("Intercept: " + str(logitModel.intercept))

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**UTDATAPARAMETRAR** 

Koefficienter: [0.0082065285375,-0.0223675576104,-0.0183812028036,-3.48124578069 e-05,-0.00247646947233,-0.00165897881503, 0.0675394837328,-0.111823113101,-0.324609912762,-0.204549780032,-1.36499216354, 0.591088507921,-0.664263411392,-1,00439726852, 3.46567827545,-3.51025855172,-0.0471341112232,-0.043521833294, 0.000243375810385, 0.054518719222]

Spärr:-0.0111216486893

Tids åtgång för att köra ovanför cell: 14,43 sekunder

**Utvärdera den binära klassificerings modellen med standard mått**

```python
#EVALUATE LOGISTIC REGRESSION MODEL WITH LBFGS

# RECORD START TIME
timestart = datetime.datetime.now()

# PREDICT ON TEST DATA WITH MODEL
predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitModel.predict(lp.features)), lp.label))

# INSTANTIATE METRICS OBJECT
metrics = BinaryClassificationMetrics(predictionAndLabels)

# AREA UNDER PRECISION-RECALL CURVE
print("Area under PR = %s" % metrics.areaUnderPR)

# AREA UNDER ROC CURVE
print("Area under ROC = %s" % metrics.areaUnderROC)
metrics = MulticlassMetrics(predictionAndLabels)

# OVERALL STATISTICS
precision = metrics.precision()
recall = metrics.recall()
f1Score = metrics.fMeasure()
print("Summary Stats")
print("Precision = %s" % precision)
print("Recall = %s" % recall)
print("F1 Score = %s" % f1Score)


## SAVE MODEL WITH DATE-STAMP
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
dirfilename = modelDir + logisticregressionfilename;
logitModel.save(sc, dirfilename);

# OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
logitModel.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
predictionAndLabelsDF = predictionAndLabels.toDF()
predictionAndLabelsDF.registerTempTable("tmp_results");

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
```

**UTDATAPARAMETRAR** 

Area under PR = 0.985297691373

Area under ROC = 0.983714670256

Sammanfattnings statistik

Precision = 0.984304060189

Återkalla = 0.984304060189

F1 score = 0.984304060189

Tids åtgång för att köra ovanför cell: 57,61 sekunder

**Rita upp ROC-kurvan.**

*PredictionAndLabelsDF* är registrerad som en tabell *tmp_results* i föregående cell. *tmp_results* kan användas för att utföra frågor och utmatnings resultat i sqlResults data-ram för att rita. Här är koden.

```python
# QUERY RESULTS                              
%%sql -q -o sqlResults
SELECT * from tmp_results
```

Här är koden för att göra förutsägelser och rita ROC-kurvan.

```python
# MAKE PREDICTIONS AND PLOT ROC-CURVE

# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
%matplotlib inline
from sklearn.metrics import roc_curve,auc

# MAKE PREDICTIONS
predictions_pddf = test_predictions.rename(columns={'_1': 'probability', '_2': 'label'})
prob = predictions_pddf["probability"] 
fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
roc_auc = auc(fpr, tpr)

# PLOT ROC CURVE
plt.figure(figsize=(5,5))
plt.plot(fpr, tpr, label='ROC curve (area = %0.2f)' % roc_auc)
plt.plot([0, 1], [0, 1], 'k--')
plt.xlim([0.0, 1.0])
plt.ylim([0.0, 1.05])
plt.xlabel('False Positive Rate')
plt.ylabel('True Positive Rate')
plt.title('ROC Curve')
plt.legend(loc="lower right")
plt.show()
```

**UTDATAPARAMETRAR**

![Logistisk regression ROC-curve.png](./media/spark-data-exploration-modeling/logistic-regression-roc-curve.png)

### <a name="random-forest-classification"></a>Slumpmässig skogs klassificering

Koden i det här avsnittet visar hur du tränar, utvärderar och sparar en slumpmässig skogs modell som förutsäger om ett tips är betalt för en resa i NYC taxi-resan och pris data uppsättningen.

```python
#PREDICT WHETHER A TIP IS PAID OR NOT USING RANDOM FOREST

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import RandomForest, RandomForestModel
from pyspark.mllib.util import MLUtils
from pyspark.mllib.evaluation import BinaryClassificationMetrics
from pyspark.mllib.evaluation import MulticlassMetrics

# SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

# TRAIN RANDOMFOREST MODEL
rfModel = RandomForest.trainClassifier(indexedTRAINbinary, numClasses=2, 
                                       categoricalFeaturesInfo=categoricalFeaturesInfo,
                                       numTrees=25, featureSubsetStrategy="auto",
                                       impurity='gini', maxDepth=5, maxBins=32)
## UN-COMMENT IF YOU WANT TO PRINT TREES
#print('Learned classification forest model:')
#print(rfModel.toDebugString())

# PREDICT ON TEST DATA AND EVALUATE
predictions = rfModel.predict(indexedTESTbinary.map(lambda x: x.features))
predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

# AREA UNDER ROC CURVE
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)

# PERSIST MODEL IN BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
rfclassificationfilename = "RandomForestClassification_" + datestamp;
dirfilename = modelDir + rfclassificationfilename;

rfModel.save(sc, dirfilename);

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**UTDATAPARAMETRAR**

Area under ROC = 0.985297691373

Tids åtgång för att köra ovanför cell: 31,09 sekunder

### <a name="gradient-boosting-trees-classification"></a>Klassificering av tonings förstärknings träd

Koden i det här avsnittet visar hur du tränar, utvärderar och sparar en modell modell för tonings förstärkning som förutsäger om ett tips är betalt för en resa i NYC taxi-resan och pris data uppsättningen.

```python
#PREDICT WHETHER A TIP IS PAID OR NOT USING GRADIENT BOOSTING TREES

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

# SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo, numIterations=5)
## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
#print('Learned classification GBT model:')
#print(bgtModel.toDebugString())

# PREDICT ON TEST DATA AND EVALUATE
predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

# AREA UNDER ROC CURVE
metrics = BinaryClassificationMetrics(predictionAndLabels)
print("Area under ROC = %s" % metrics.areaUnderROC)

# PERSIST MODEL IN A BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
btclassificationfilename = "GradientBoostingTreeClassification_" + datestamp;
dirfilename = modelDir + btclassificationfilename;

gbtModel.save(sc, dirfilename)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**UTDATAPARAMETRAR**

Area under ROC = 0.985297691373

Tids åtgång för att köra ovanför cell: 19,76 sekunder

## <a name="train-a-regression-model"></a>Träna en regressionsmodell

Det här avsnittet visar hur du använder tre modeller för Regressions uppgiften för att förutsäga hur mycket av tipset som betalas för en taxi resa baserat på andra Tip-funktioner. De modeller som visas är:

* Vanlig linjär regression
* Slumpmässig skog
* Tonings förstärknings träd

Dessa modeller beskrivs i introduktionen. Varje modell för modell skapande kod är uppdelad i steg: 

1. **Modellera tränings** data med en parameter uppsättning
2. **Modell utvärdering** av en test data uppsättning med mått
3. **Spara modell** i BLOB för framtida användning

### <a name="linear-regression-with-sgd"></a>Linjär regression med SGD

Koden i det här avsnittet visar hur du använder skalade funktioner för att träna en linjär regression som använder Stochastic gradient brantaste (SGD) för optimering, samt hur du poängs ätter, utvärderar och sparar modellen i Azure Blob Storage (WASB).

> [!TIP]
> I vår miljö kan det finnas problem med konvergensen av LinearRegressionWithSGD-modeller, och parametrarna måste ändras/optimeras noggrant för att få en giltig modell. Skalning av variabler bidrar avsevärt till konvergens.

```python
#PREDICT TIP AMOUNTS USING LINEAR REGRESSION WITH SGD

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD LIBRARIES
from pyspark.mllib.regression import LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel
from pyspark.mllib.evaluation import RegressionMetrics
from scipy import stats

# USE SCALED FEATURES TO TRAIN MODEL
linearModel = LinearRegressionWithSGD.train(oneHotTRAINregScaled, iterations=100, step = 0.1, regType='l2', regParam=0.1, intercept = True)

# PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
# NOTE: There are 20 coefficient terms for the 10 features, 
#       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
print("Coefficients: " + str(linearModel.weights))
print("Intercept: " + str(linearModel.intercept))

# SCORE ON SCALED TEST DATA-SET & EVALUATE
predictionAndLabels = oneHotTESTregScaled.map(lambda lp: (float(linearModel.predict(lp.features)), lp.label))
testMetrics = RegressionMetrics(predictionAndLabels)

# PRINT TEST METRICS
print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# SAVE MODEL WITH DATE-STAMP IN THE DEFAULT BLOB FOR THE CLUSTER
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
linearregressionfilename = "LinearRegressionWithSGD_" + datestamp;
dirfilename = modelDir + linearregressionfilename;

linearModel.save(sc, dirfilename)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**UTDATAPARAMETRAR**

Koefficienter: [0.00457675809917,-0.0226314167349,-0.0191910355236, 0.246793409578, 0.312047890459, 0.359634405999, 0.00928692253981,-0.000987181489428,-0.0888306617845, 0.0569376211553, 0.115519551711, 0.149250164995,-0.00990211159703,-0.00637410344522, 0.545083566179,-0.536756072402, 0.0105762393099,-0.0130117577055, 0.0129304737772,-0.00171065945959]

Skärning: 0.853872718283

RMSE = 1.24190115863

R-SQR = 0.608017146081

Tids åtgång för att köra ovanför cell: 58,42 sekunder

### <a name="random-forest-regression"></a>Slumpmässig skogs regression

Koden i det här avsnittet visar hur du tränar, utvärderar och sparar en slumpmässig skogs regression som förutsäger Tip-mängden för NYC taxi-resan.

```python
#PREDICT TIP AMOUNTS USING RANDOM FOREST

# RECORD START TIME
timestart= datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import RandomForest, RandomForestModel
from pyspark.mllib.util import MLUtils
from pyspark.mllib.evaluation import RegressionMetrics


## TRAIN MODEL
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                    numTrees=25, featureSubsetStrategy="auto",
                                    impurity='variance', maxDepth=10, maxBins=32)
## UN-COMMENT IF YOU WANT TO PRING TREES
#print('Learned classification forest model:')
#print(rfModel.toDebugString())

## PREDICT AND EVALUATE ON TEST DATA-SET
predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)

# TEST METRICS
testMetrics = RegressionMetrics(predictionAndLabels)
print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# SAVE MODEL IN BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
rfregressionfilename = "RandomForestRegression_" + datestamp;
dirfilename = modelDir + rfregressionfilename;

rfModel.save(sc, dirfilename);

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**UTDATAPARAMETRAR**

RMSE = 0.891209218139

R-SQR = 0.759661334921

Tids åtgång för att köra ovanför cell: 49,21 sekunder

### <a name="gradient-boosting-trees-regression"></a>Tonings förstärkning av träd regression

Koden i det här avsnittet visar hur du tränar, utvärderar och sparar en modell modell för tonings förstärkning som förutsäger Tip-data för NYC taxi-resan.

**Träna och utvärdera**

```python
#PREDICT TIP AMOUNTS USING GRADIENT BOOSTING TREES

# RECORD START TIME
timestart= datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel
from pyspark.mllib.util import MLUtils

## TRAIN MODEL
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
                                                numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)

## EVALUATE A TEST DATA-SET
predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

# TEST METRICS
testMetrics = RegressionMetrics(predictionAndLabels)
print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# SAVE MODEL IN BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
dirfilename = modelDir + btregressionfilename;
gbtModel.save(sc, dirfilename)

# CONVERT RESULTS TO DF AND REGISTER TEMP TABLE
test_predictions = sqlContext.createDataFrame(predictionAndLabels)
test_predictions.registerTempTable("tmp_results");

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**UTDATAPARAMETRAR**

RMSE = 0.908473148639

R-SQR = 0.753835096681

Tids åtgång för att köra ovanför cell: 34,52 sekunder

**Basera**

*tmp_results* registreras som en Hive-tabell i föregående cell. Resultat från tabellen är utdata till *sqlResults* data-ram för att rita. Här är koden

```python
# PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES

# SELECT RESULTS
%%sql -q -o sqlResults
SELECT * from tmp_results
```

Här är koden för att rita data med hjälp av Jupyter-servern.

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
%matplotlib inline
import numpy as np

# PLOT 
ax = test_predictions_pddf.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
fit = np.polyfit(test_predictions_pddf['_1'], test_predictions_pddf['_2'], deg=1)
ax.set_title('Actual vs. Predicted Tip Amounts ($)')
ax.set_xlabel("Actual")
ax.set_ylabel("Predicted")
ax.plot(test_predictions_pddf['_1'], fit[0] * test_predictions_pddf['_1'] + fit[1], color='magenta')
plt.axis([-1, 20, -1, 20])
plt.show(ax)
```

**UTDATAPARAMETRAR**

![Utfall – vs-förutsägt – tips-belopp](./media/spark-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="clean-up-objects-from-memory"></a>Rensa objekt från minnet

Används `unpersist()` för att ta bort cachelagrade objekt i minnet.

```python
# REMOVE ORIGINAL DFs
taxi_df_train_cleaned.unpersist()
taxi_df_train_with_newFeatures.unpersist()

# FOR BINARY CLASSIFICATION TRAINING AND TESTING
indexedTRAINbinary.unpersist()
indexedTESTbinary.unpersist()
oneHotTRAINbinary.unpersist()
oneHotTESTbinary.unpersist()

# FOR REGRESSION TRAINING AND TESTING
indexedTRAINreg.unpersist()
indexedTESTreg.unpersist()
oneHotTRAINreg.unpersist()
oneHotTESTreg.unpersist()

# SCALED FEATURES
oneHotTRAINregScaled.unpersist()
oneHotTESTregScaled.unpersist()
```

## <a name="save-the-models"></a>Spara modellerna

Om du vill förbruka en oberoende data uppsättning som beskrivs i avsnittet [Poäng och utvärdera Spark-skapade maskin inlärnings modeller](spark-model-consumption.md) måste du kopiera och klistra in dessa fil namn som innehåller de sparade modeller som skapats här i den Jupyter Notebook. Här är koden för att skriva ut Sök vägarna för att modellera filer som du behöver där.

```python
# MODEL FILE LOCATIONS FOR CONSUMPTION
print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";
```

**UTDATAPARAMETRAR**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05 -0317 _03_ 23.516568"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05 -0317 _05_ 21.577773"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05 -0317 _04_ 11.950206"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05 -0317 _06_ 08.723736"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05 -0317 _04_ 36.346583"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05 -0317 _06_ 51.737282"

## <a name="whats-next"></a>Nästa steg

Nu när du har skapat Regressions-och klassificerings modeller med Spark-MlLib är du redo att lära dig hur du utvärderar och utvärderar dessa modeller. Avancerad data utforsknings-och modellerings dykningar är djupare i att inkludera kors validering, rensning av Hyper-parameter och modell utvärdering. 

**Modell förbrukning:** Information om hur du utvärderar och utvärderar klassificerings-och Regressions modeller som skapats i det här avsnittet finns i [Poäng och utvärdera Spark-skapade maskin inlärnings modeller](spark-model-consumption.md).

**Rensning av kors validering och Cross-parameter**: se [Avancerad data granskning och modellering med Spark](spark-advanced-data-exploration-modeling.md) om hur modeller kan tränas med kors validering och rensning av Hyper-parameter