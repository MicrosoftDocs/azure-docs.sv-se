---
title: Avancerad data granskning och modellering med process för Spark-team data science
description: Använd HDInsight Spark för att utföra data utforskning och träna binära klassificerings-och Regressions modeller med optimering av kors validering och globala parametrar.
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
ms.openlocfilehash: b84b7387411e7df8e092a2e810591697319f9554
ms.sourcegitcommit: 2c1b93301174fccea00798df08e08872f53f669c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104774685"
---
# <a name="advanced-data-exploration-and-modeling-with-spark"></a>Avancerad datagranskning och modellering med Spark

Den här genom gången använder HDInsight Spark för att utföra data utforskning och träna binära klassificerings-och Regressions modeller med optimering av kors validering och globala parametrar i ett exempel på NYC taxi-resan och biljett 2013-datamängden. Det vägleder dig genom stegen i [data vetenskaps processen](./index.yml), från slut punkt till slut punkt, med ett HDInsight Spark-kluster för bearbetning och Azure-blobbar för att lagra data och modeller. Processen utforskar och visualiserar data som förts in från en Azure Storage Blob och förbereder sedan data för att skapa förutsägande modeller. Python har använts för att koda lösningen och visa relevanta områden. Dessa modeller bygger på Spark MLlib Toolkit för att utföra binära klassificerings-och Regressions modellerings uppgifter. 

* Den **binära klassificerings** uppgiften är att förutsäga om ett tips betalas ut för resan. 
* **Regressions** uppgiften är att förutsäga hur mycket av tipset som baseras på andra Tip-funktioner. 

Modellerings stegen innehåller också kod som visar hur du tränar, utvärderar och sparar varje typ av modell. Avsnittet beskriver en del av samma mark som [data utforskning och modellering med Spark](spark-data-exploration-modeling.md) -ämne. Men det är mer "Avancerat" i så att det även använder kors validering med en valfri parameter för att träna optimalt exakta klassificerings-och Regressions modeller. 

**Kors validering (ka)** är en teknik som utvärderar hur väl en modell tränas på en känd uppsättning datageneraliseringar för att förutsäga funktionerna i data uppsättningar som inte har tränats.  En vanlig implementering som används här är att dela upp en data uppsättning i n-vik och sedan träna modellen i ett Round-Robin på alla utom en av vikningarna. Modellens förmåga att förutsäga korrekt när den testas mot den oberoende data uppsättningen i den här vikningen används inte för att träna modellen att utvärderas.

**Optimering** av dess parameter är ett problem med att välja en uppsättning med Grundparametrar för en Learning-algoritm, vanligt vis med målet att optimera ett mått på algoritmens prestanda på en oberoende data uppsättning. **Disponeringsparametrarna** är värden som måste anges utanför modell inlärnings proceduren. Antaganden om dessa värden kan påverka modellens flexibilitet och noggrannhet. Besluts träd har grundparametrar, till exempel det önskade djupet och antalet löv i trädet. Support Vector Machines (SVMs) kräver att du anger en sanktions period för felklassificering. 

Ett vanligt sätt att utföra optimering av en vanlig parameter är att använda en rutnäts sökning eller en **parameter rensning**. Den här sökningen går igenom en delmängd av områdets parameter utrymme för en Learning-algoritm. Kors validering kan tillhandahålla ett prestanda mått för att sortera ut optimala resultat som genereras av algoritmen för rutnäts sökning. KA som används med en vanlig parameter, hjälper till att begränsa problem som översätter en modell för att träna data, så att modellen behåller den kapacitet som ska gälla för den allmänna data uppsättning som inlärnings data extraheras från.

De modeller som vi använder är logistik och linjär regression, slumpmässiga skogar och tonings utökat träd:

* [Linjär regression med SGD](https://spark.apache.org/docs/latest/api/python/reference/api/pyspark.mllib.regression.LinearRegressionWithSGD.html#pyspark.mllib.regression.LinearRegressionWithSGD
) är en linjär Regressions modell som använder sig av en Stochastic gradient BRANTASTE (SGD)-metod och för optimering och funktions skalning för att förutsäga Tip-beloppen som betalas. 
* [Logistisk regression med LBFGS](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.classification.LogisticRegressionWithLBFGS) eller "Logit" regression är en Regressions modell som kan användas när den beroende variabeln är kategoriska för att utföra data klassificering. LBFGS är en Delegerings algoritm för en halv Newton som uppskattar algoritmen Broyden – Fletcher – Goldfarb – Shanno (BFGS) med en begränsad mängd dator minne och som används ofta i Machine Learning.
* [Slumpmässiga skogar](https://spark.apache.org/docs/latest/mllib-ensembles.html#Random-Forests) är ensembler för besluts träd.  De kombinerar många besluts träd för att minska risken för överanpassning. Slumpmässiga skogar används för regression och klassificering och kan hantera kategoriska-funktioner och kan utökas till klassificerings inställningen multiklass. De kräver inte funktions skalning och kan fånga icke-linjära och funktions interaktioner. Slumpmässiga skogar är en av de mest fungerande maskin inlärnings modellerna för klassificering och regression.
* [Tonings utökat träd](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) (GBTS) är ensembler i besluts träd. GBTS träna besluts träd iterativt för att minimera en förlust funktion. GBTS används för regression och klassificering och kan hantera kategoriska-funktioner, kräver inte funktions skalning och kan fånga icke-linjära och funktions interaktioner. De kan också användas i en inställning för multiklass-klassificering.

Modellerings exempel med ka och dess parameter svep visas för problem med binära klassificeringar. Enklare exempel (utan parameter rensningar) visas i huvud avsnittet för Regressions aktiviteter. Men i bilagan presenteras även en validering med elastiskt netto för linjär regression och ka med parameter svep som använder för slumpmässig skogs regression. **Elastiskt nät** är en regelbunden Regressions metod för att passa in linjära Regressions modeller som linjärt kombinerar L1-och L2-mått som sanktioner för [lasso](https://en.wikipedia.org/wiki/Lasso%20%28statistics%29) -och [Ridge](https://en.wikipedia.org/wiki/Tikhonov_regularization) -metoderna.   

<!-- -->

> [!NOTE]
> Även om Spark MLlib Toolkit är utformat för att fungera på stora data mängder, används ett relativt litet exempel (~ 30 MB med 170K-rader, ungefär 0,1% av den ursprungliga NYC-datauppsättningen). Den här övningen här körs effektivt (om 10 minuter) i ett HDInsight-kluster med 2 arbetsnoder. Samma kod, med mindre ändringar, kan användas för att bearbeta större data uppsättningar, med lämpliga ändringar för cachelagring av data i minnet och ändring av kluster storleken.

<!-- -->

## <a name="setup-spark-clusters-and-notebooks"></a>Installation: Spark-kluster och bärbara datorer
Installations steg och kod beskrivs i den här genom gången av en HDInsight Spark 1,6. Men Jupyter Notebooks finns för både HDInsight Spark 1,6-kluster och Spark 2,0-kluster. En beskrivning av antecknings böckerna och länkar till dem finns i [Readme.MD](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) för GitHub-databasen som innehåller dem. Dessutom är koden här och i de länkade antecknings böckerna generisk och bör fungera på alla Spark-kluster. Om du inte använder HDInsight Spark kan konfigurations-och hanterings stegen för klustret skilja sig något från vad som visas här. För enkelhetens skull är här länkarna till Jupyter-anteckningsbokerna för Spark 1,6 och 2,0 för att köras i pyspark-kärnan i Jupyter Notebook-servern:

### <a name="spark-16-notebooks"></a>Spark 1,6-anteckningsböcker

[pySpark-Machine-Learning-data-science-Spark-Advanced-data-prospektering-Modeling. ipynb](https://github.com/Azure-Samples/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): innehåller avsnitt i notebook-#1 och modell utveckling med hjälp av widgeten för att justera och kors validering.

### <a name="spark-20-notebooks"></a>Spark 2,0-anteckningsböcker

[Spark 2.0-pySpark3-Machine-Learning-data-science-Spark-Advanced-data-utforskning-modellering. ipynb](https://github.com/Azure-Samples/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): den här filen innehåller information om hur du utför data granskning, modellering och poäng i Spark 2,0-kluster.

[!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="setup-storage-locations-libraries-and-the-preset-spark-context"></a>Installation: lagrings platser, bibliotek och den förinställda Spark-kontexten
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
modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";

# PRINT START TIME
import datetime
datetime.datetime.now()
```

**UTDATAPARAMETRAR**

DateTime. DateTime (2016, 4, 18, 17, 36, 27, 832799)

### <a name="import-libraries"></a>Importera bibliotek
Importera nödvändiga bibliotek med följande kod:

```python
# LOAD PYSPARK LIBRARIES
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

Mer information om kerneler för Jupyter-anteckningsböcker och de fördefinierade "magiska" som de tillhandahåller finns i [kernels som är tillgängliga för Jupyter-anteckningsböcker med HDInsight Spark Linux-kluster i HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

## <a name="data-ingestion-from-public-blob"></a>Data inmatning från offentlig BLOB:
Det första steget i data vetenskaps processen är att mata in data som ska analyseras från källor där de finns i data utforsknings-och modell miljö. Den här miljön är spark i den här genom gången. Det här avsnittet innehåller koden för att slutföra en serie aktiviteter:

* Mata in data exemplet som ska modelleras
* Läs i data uppsättningen för indata (lagras som en. TSV-fil)
* formatera och rensa data
* Skapa och cachelagra objekt (RDD eller data ramar) i minnet
* registrera den som en temporär tabell i SQL-kontext.

Här är koden för data inmatning.

```python
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

# CACHE & MATERIALIZE DATA-FRAME IN MEMORY. GOING THROUGH AND COUNTING NUMBER OF ROWS MATERIALIZES THE DATA-FRAME IN MEMORY
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

Tids åtgång för att köra ovanför cell: 276,62 sekunder

## <a name="data-exploration--visualization"></a>Data utforskning & visualisering
När data har förts in i Spark, är nästa steg i data vetenskaps processen att få djupare förståelse för data genom utforskning och visualisering. I det här avsnittet undersöker vi taxi-data med hjälp av SQL-frågor och ritar upp målvärdena och potentiella funktioner för visuell inspektion. Mer specifikt plottar vi frekvensen av antalet passagerare i taxi resor, frekvensen för Tip-mängder och hur tips varierar efter betalnings belopp och typ.

### <a name="plot-a-histogram-of-passenger-count-frequencies-in-the-sample-of-taxi-trips"></a>Rita ett histogram över frekvensen för antalet passagerare i provet i taxi resor
Den här koden och efterföljande kodfragment använder SQL Magic för att fråga exemplet och det lokala Magic för att rita data.

* **SQL Magic ( `%%sql` )** HDInsight PySpark-kärnan stöder enkla infogade HiveQL-frågor mot sqlContext. Argumentet (-o VARIABLE_NAME) behåller SQL-frågans utdata som en Pandas-DataFrame på Jupyter-servern. Det innebär att den är tillgänglig i det lokala läget.
* **`%%local` Magic** används för att köra kod lokalt på Jupyter-servern, som är huvudnoden för HDInsight-klustret. Normalt använder du `%%local` Magic när `%%sql -o` Magic används för att köra en fråga. Parametern-o behåller utdata från SQL-frågan lokalt. Sedan `%%local` utlöser Magic nästa uppsättning kodfragment för att köras lokalt mot utdata från de SQL-frågor som har sparats lokalt. Utdata visualiseras automatiskt när du har kört koden.

Den här frågan hämtar antalet resor per passagerare. 

```sql
# PLOT FREQUENCY OF PASSENGER COUNTS IN TAXI TRIPS

# SQL QUERY
%%sql -q -o sqlResults
SELECT passenger_count, COUNT(*) as trip_counts FROM taxi_train WHERE passenger_count > 0 and passenger_count < 7 GROUP BY passenger_count
```

Den här koden skapar en lokal data ram från frågans utdata och ritar data. `%%local`Magic skapar en lokal data ram, `sqlResults` som kan användas för att rita med matplotlib. 

<!-- -->

> [!NOTE]
> Detta PySpark Magic används flera gånger i den här genom gången. Om mängden data är stor, bör du prova att skapa en data ram som kan passa i det lokala minnet.

<!-- -->

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES. 
# CLICK ON THE TYPE OF PLOT TO BE GENERATED (E.G. LINE, AREA, BAR ETC.)
sqlResults
```

Här är koden för att rita upp resor efter antal passagerare

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
import matplotlib.pyplot as plt
%matplotlib inline

# PLOT PASSENGER NUMBER VS TRIP COUNTS
x_labels = sqlResults['passenger_count'].values
fig = sqlResults[['trip_counts']].plot(kind='bar', facecolor='lightblue')
fig.set_xticklabels(x_labels)
fig.set_title('Counts of trips by passenger count')
fig.set_xlabel('Passenger count in trips')
fig.set_ylabel('Trip counts')
plt.show()
```

**UTDATAPARAMETRAR**

![Frekvens för resor per antal passagerare](./media/spark-advanced-data-exploration-modeling/frequency-of-trips-by-passenger-count.png)

Du kan välja bland flera olika typer av visualiseringar (tabell, cirkel, linje, yta eller stapel) med hjälp av meny knapparna **typ** i antecknings boken. Stolp området visas här.

### <a name="plot-a-histogram-of-tip-amounts-and-how-tip-amount-varies-by-passenger-count-and-fare-amounts"></a>Rita ett histogram med Tip-mängder och hur Tip-mängden varierar beroende på antalet passagerare och avgifts belopp.
Använd en SQL-fråga för att sampla data...

```sql
# SQL SQUERY
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
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
%matplotlib inline

# TIP BY PAYMENT TYPE AND PASSENGER COUNT
ax1 = resultsPDDF[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()

# TIP BY PASSENGER COUNT
ax2 = resultsPDDF.boxplot(column=['tip_amount'], by=['passenger_count'])
ax2.set_title('Tip amount ($) by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
plt.suptitle('')
plt.show()

# TIP AMOUNT BY FARE AMOUNT, POINTS ARE SCALED BY PASSENGER COUNT
ax = resultsPDDF.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(resultsPDDF.passenger_count))
ax.set_title('Tip amount by Fare amount ($)')
ax.set_xlabel('Fare Amount')
ax.set_ylabel('Tip Amount')
plt.axis([-2, 120, -2, 30])
plt.show()
```

**UTDATAPARAMETRAR** 

![Distribution av Tip-belopp](./media/spark-advanced-data-exploration-modeling/tip-amount-distribution.png)

![Tips belopp efter antal passagerare](./media/spark-advanced-data-exploration-modeling/tip-amount-by-passenger-count.png)

![Tips belopp per avgifts belopp](./media/spark-advanced-data-exploration-modeling/tip-amount-by-fare-amount.png)

## <a name="feature-engineering-transformation-and-data-preparation-for-modeling"></a>Funktions teknik, transformering och data förberedelse för modellering
Det här avsnittet beskriver och innehåller koden för procedurer som används för att förbereda data för användning i ML-modellering. Det visar hur du utför följande uppgifter:

* Skapa en ny funktion genom att partitionera timmar i arbets lager för trafik tid
* Index och frekventa kategoriska funktioner
* Skapa märkta punkt objekt för inmatade i ML-funktioner
* Skapa en slumpmässig under sampling av data och dela upp den i utbildning och testnings uppsättningar
* Funktionsskalning
* Cachelagra objekt i minnet

### <a name="create-a-new-feature-by-partitioning-traffic-times-into-bins"></a>Skapa en ny funktion genom att partitionera trafik tider i lager platser
Den här koden visar hur du skapar en ny funktion genom att partitionera trafik tider i lager platser och sedan cachelagra den resulterande data ramen i minnet. Cachelagring leder till förbättrad körnings tid där elastiska distribuerade data uppsättningar (RDD) och data ramar används flera gånger. Därför cachelagrar vi RDD och data ramar i flera steg i den här genom gången.

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
```

```python
# CACHE DATA-FRAME IN MEMORY & MATERIALIZE DF IN MEMORY
# THE .COUNT() GOES THROUGH THE ENTIRE DATA-FRAME,
# MATERIALIZES IT IN MEMORY, AND GIVES THE COUNT OF ROWS.
taxi_df_train_with_newFeatures.cache()
taxi_df_train_with_newFeatures.count()
```

**UTDATAPARAMETRAR**

126050

### <a name="index-and-one-hot-encode-categorical-features"></a>Index och en-frekvent kodning av kategoriska-funktioner
Det här avsnittet visar hur du kan indexera eller koda kategoriska-funktioner för inmatade funktioner i modellerings funktionerna. Modellerings-och förutsägelse funktionerna i MLlib kräver att funktioner med kategoriska indata indexeras eller kodas innan de används. 

Beroende på modellen måste du indexera eller koda dem på olika sätt. Till exempel kräver logistik-och linjära Regressions modeller en-frekvent kodning, där till exempel en funktion med tre kategorier kan utökas till tre funktions kolumner, där var och en innehåller 0 eller 1 beroende på en observations kategori. MLlib tillhandahåller funktionen [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) för att göra en snabb kodning. Denna kodare mappar en kolumn med etikett index till en kolumn med binära vektorer, med högst ett enda värde. Den här kodningen tillåter algoritmer som förväntar sig numeriska värde funktioner, till exempel Logistisk regression, som ska användas för kategoriska-funktioner.

Här är koden för att indexera och koda kategoriska-funktioner:

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.ml.feature import OneHotEncoder, StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer

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

Tids åtgång för att köra ovanför cell: 3,14 sekunder

### <a name="create-labeled-point-objects-for-input-into-ml-functions"></a>Skapa märkta punkt objekt för inmatade i ML-funktioner
Det här avsnittet innehåller kod som visar hur du kan indexera kategoriska text data som en etikettad punkt data typ och hur du kodar den. Den här omvandlingen förbereder text data som ska användas för att träna och testa MLlib Logistisk regression och andra klassificerings modeller. Märkta punkt objekt är elastiska distribuerade data uppsättningar (RDD) formaterade på ett sätt som indata för de flesta av ML-algoritmer används i MLlib. En [märkt punkt](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) är en lokal vektor, antingen kompakt eller sparse, som är associerad med en etikett/ett svar.

Här är koden för att indexera och koda text funktioner för binär klassificering.

```python
# FUNCTIONS FOR BINARY CLASSIFICATION

# LOAD LIBRARIES
from pyspark.mllib.regression import LabeledPoint
from numpy import array

# INDEXING CATEGORICAL TEXT FEATURES FOR INPUT INTO TREE-BASED MODELS
def parseRowIndexingBinary(line):
    features = np.array([line.paymentIndex, line.vendorIndex, line.rateIndex, line.pickup_hour, line.weekday,
                         line.passenger_count, line.trip_time_in_secs, line.trip_distance, line.fare_amount])
    labPt = LabeledPoint(line.tipped, features)
    return  labPt

# ONE-HOT ENCODING OF CATEGORICAL TEXT FEATURES FOR INPUT INTO LOGISTIC REGRESSION MODELS
def parseRowOneHotBinary(line):
    features = np.concatenate((np.array([line.pickup_hour, line.weekday, line.passenger_count,
                                        line.trip_time_in_secs, line.trip_distance, line.fare_amount]), 
                               line.vendorVec.toArray(), line.rateVec.toArray(), line.paymentVec.toArray()), axis=0)
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
Den här koden skapar en slumpmässig sampling av data (25% används här). Även om det inte krävs för det här exemplet på grund av storleken på data uppsättningen, visar vi hur du kan sampla data här. Sedan vet du hur du använder den för dina egna problem om det behövs. När exempel är stora kan samplingen Spara avsevärd tid och utbildnings modeller. Nästa steg är att dela exemplet i en utbildnings del (75% här) och en test del (25% här) för användning i klassificerings-och Regressions modellering.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# SPECIFY SAMPLING AND SPLITTING FRACTIONS
from pyspark.sql.functions import rand

samplingFraction = 0.25;
trainingFraction = 0.75; testingFraction = (1-trainingFraction);
seed = 1234;
encodedFinalSampled = encodedFinal.sample(False, samplingFraction, seed=seed)

# SPLIT SAMPLED DATA-FRAME INTO TRAIN/TEST, WITH A RANDOM COLUMN ADDED FOR DOING CV (SHOWN LATER)
# INCLUDE RAND COLUMN FOR CREATING CROSS-VALIDATION FOLDS
dfTmpRand = encodedFinalSampled.select("*", rand(0).alias("rand"));
trainData, testData = dfTmpRand.randomSplit([trainingFraction, testingFraction], seed=seed);

# CACHE TRAIN AND TEST DATA
trainData.cache()
testData.cache()

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

Tids åtgång för att köra ovanför cell: 0,31 sekund

### <a name="feature-scaling"></a>Funktionsskalning
Funktions skalning, även kallat data normalisering, säkerställer att funktioner med de vanligaste värdena för uppfyllande värden inte får orimlig väg i mål funktionen. I koden för funktions skalning används [StandardScaler](https://spark.apache.org/docs/latest/api/python/pyspark.mllib.html#pyspark.mllib.feature.StandardScaler) för att skala funktionerna till enhets avvikelse. Det tillhandahålls av MLlib för användning i linjär regression med Stochastic gradient brantaste (SGD). SGD är en populär algoritm för utbildning av ett brett utbud av andra maskin inlärnings modeller, till exempel vanliga regressioner eller support vektor datorer (SVM).   

> [!TIP]
> Vi har hittat LinearRegressionWithSGD-algoritmen för att vara känslig för skalning av funktioner.   
> 
> 

Här är koden för att skala variabler för användning med den vanliga linjära SGD-algoritmen.

```python
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

Tids åtgång för att köra ovanför cell: 11,67 sekunder

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

Tids åtgång för att köra ovanför cell: 0,13 sekund

## <a name="predict-whether-or-not-a-tip-is-paid-with-binary-classification-models"></a>Förutsäg om ett tips betalas med binära klassificerings modeller
I det här avsnittet visas hur du använder tre modeller för den binära klassificerings uppgiften för att förutsäga om ett tips är betalt för en taxi resa. De modeller som visas är:

* Logistisk regression 
* Slumpmässig skog
* Tonings förstärknings träd

Varje modell för modell skapande kod är uppdelad i steg: 

1. **Modellera tränings** data med en parameter uppsättning
2. **Modell utvärdering** av en test data uppsättning med mått
3. **Spara modell** i BLOB för framtida användning

Vi visar hur du utför kors validering (ka) med parameter rensning på två sätt:

1. Använda **generisk** anpassad kod som kan tillämpas på alla algoritmer i MLlib och till alla parameter uppsättningar i en algoritm. 
2. Använda **pipeline-funktionen PySpark CrossValidator**. CrossValidator har några begränsningar för Spark-1.5.0: 
   
   * Det går inte att spara eller spara pipeline-modeller för framtida konsumtion.
   * Kan inte användas för varje parameter i en modell.
   * Kan inte användas för varje MLlib-algoritm.

### <a name="generic-cross-validation-and-hyperparameter-sweeping-used-with-the-logistic-regression-algorithm-for-binary-classification"></a>Generisk kors validering och Cross-parameter sveper som används med Logistisk regression-algoritmen för binär klassificering
Koden i det här avsnittet visar hur du tränar, utvärderar och sparar en logistik Regressions modell med [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) som förutsäger om ett tips är betalt för en resa i NYC taxi-resan och pris data uppsättningen. Modellen tränas med hjälp av kors validering (ka) och en cross-parameter som implementeras med anpassad kod som kan tillämpas på någon av inlärnings algoritmerna i MLlib.   

<!-- -->

> [!NOTE]
> Det kan ta flera minuter att köra den här anpassade ka-koden.

<!-- -->

**Träna logistik Regressions modellen med hjälp av ka och dess parameter Svep**

```python
# LOGISTIC REGRESSION CLASSIFICATION WITH CV AND HYPERPARAMETER SWEEPING

# GET ACCURACY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD LIBRARIES
from pyspark.mllib.classification import LogisticRegressionWithLBFGS 
from pyspark.mllib.evaluation import BinaryClassificationMetrics

# CREATE PARAMETER GRID FOR LOGISTIC REGRESSION PARAMETER SWEEP
from sklearn.grid_search import ParameterGrid
grid = [{'regParam': [0.01, 0.1], 'iterations': [5, 10], 'regType': ["l1", "l2"], 'tolerance': [1e-3, 1e-4]}]
paramGrid = list(ParameterGrid(grid))
numModels = len(paramGrid)

# SET NUM FOLDS AND NUM PARAMETER SETS TO SWEEP ON
nFolds = 3;
h = 1.0 / nFolds;
metricSum = np.zeros(numModels);

# BEGIN CV WITH PARAMETER SWEEP
for i in range(nFolds):
    # Create training and x-validation sets
    validateLB = i * h
    validateUB = (i + 1) * h
    condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
    validation = trainData.filter(condition)
    # Create LabeledPoints from data-frames
    if i > 0:
        trainCVLabPt.unpersist()
        validationLabPt.unpersist()
    trainCV = trainData.filter(~condition)
    trainCVLabPt = trainCV.map(parseRowOneHotBinary)
    trainCVLabPt.cache()
    validationLabPt = validation.map(parseRowOneHotBinary)
    validationLabPt.cache()
    # For parameter sets compute metrics from x-validation
    for j in range(numModels):
        regt = paramGrid[j]['regType']
        regp = paramGrid[j]['regParam']
        iters = paramGrid[j]['iterations']
        tol = paramGrid[j]['tolerance']
        # Train logistic regression model with hypermarameter set
        model = LogisticRegressionWithLBFGS.train(trainCVLabPt, regType=regt, iterations=iters,  
                                                  regParam=regp, tolerance = tol, intercept=True)
        predictionAndLabels = validationLabPt.map(lambda lp: (float(model.predict(lp.features)), lp.label))
        # Use ROC-AUC as accuracy metrics
        validMetrics = BinaryClassificationMetrics(predictionAndLabels)
        metric = validMetrics.areaUnderROC
        metricSum[j] += metric

avgAcc = metricSum / nFolds;
bestParam = paramGrid[np.argmax(avgAcc)];

# UNPERSIST OBJECTS
trainCVLabPt.unpersist()
validationLabPt.unpersist()

# TRAIN ON FULL TRAIING SET USING BEST PARAMETERS FROM CV/PARAMETER SWEEP
logitBest = LogisticRegressionWithLBFGS.train(oneHotTRAINbinary, regType=bestParam['regType'], 
                                              iterations=bestParam['iterations'], 
                                              regParam=bestParam['regParam'], tolerance = bestParam['tolerance'], 
                                              intercept=True)


# PRINT COEFFICIENTS AND INTERCEPT OF THE MODEL
# NOTE: There are 20 coefficient terms for the 10 features, 
#       and the different categories for features: vendorVec (2), rateVec, paymentVec (6), TrafficTimeBinsVec (4)
print("Coefficients: " + str(logitBest.weights))
print("Intercept: " + str(logitBest.intercept))

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

Koden i det här avsnittet visar hur du utvärderar en logistik Regressions modell mot en test data uppsättning, inklusive ett observations område i ROC-kurvan.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

#IMPORT LIBRARIES
from sklearn.metrics import roc_curve,auc
from pyspark.mllib.evaluation import BinaryClassificationMetrics
from pyspark.mllib.evaluation import MulticlassMetrics

# PREDICT ON TEST DATA WITH BEST/FINAL MODEL
predictionAndLabels = oneHotTESTbinary.map(lambda lp: (float(logitBest.predict(lp.features)), lp.label))

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

# OUTPUT PROBABILITIES AND REGISTER TEMP TABLE
logitBest.clearThreshold(); # This clears threshold for classification (0.5) and outputs probabilities
predictionAndLabelsDF = predictionAndLabels.toDF()
predictionAndLabelsDF.registerTempTable("tmp_results");

# PRINT ELAPSED TIME    
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**UTDATAPARAMETRAR**

Area under PR = 0.985336538462

Area under ROC = 0.983383274312

Sammanfattnings statistik

Precision = 0.984174341679

Återkalla = 0.984174341679

F1 score = 0.984174341679

Tids åtgång för att köra ovanför cell: 2,67 sekunder

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

#PREDICTIONS
predictions_pddf = sqlResults.rename(columns={'_1': 'probability', '_2': 'label'})
prob = predictions_pddf["probability"] 
fpr, tpr, thresholds = roc_curve(predictions_pddf['label'], prob, pos_label=1);
roc_auc = auc(fpr, tpr)

# PLOT ROC CURVES
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

![Logistisk regression ROC-kurva för allmän metod](./media/spark-advanced-data-exploration-modeling/logistic-regression-roc-curve.png)

**Bevara modell i en BLOB för framtida konsumtion**

Koden i det här avsnittet visar hur du sparar logistik Regressions modellen för förbrukning.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.classification import LogisticRegressionModel

# PERSIST MODEL
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
logisticregressionfilename = "LogisticRegressionWithLBFGS_" + datestamp;
dirfilename = modelDir + logisticregressionfilename;

logitBest.save(sc, dirfilename);

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
```

**UTDATAPARAMETRAR**

Tids åtgång för att köra ovanför cell: 34,57 sekunder

### <a name="use-mllibs-crossvalidator-pipeline-function-with-logistic-regression-elastic-regression-model"></a>Använda MLlib CrossValidator-pipeline med logistisk Regressions modell (elastisk regression)
Koden i det här avsnittet visar hur du tränar, utvärderar och sparar en logistik Regressions modell med [LBFGS](https://en.wikipedia.org/wiki/Broyden%E2%80%93Fletcher%E2%80%93Goldfarb%E2%80%93Shanno_algorithm) som förutsäger om ett tips är betalt för en resa i NYC taxi-resan och pris data uppsättningen. Modellen tränas med kors validering (ka) och en hel parameter som implementeras med funktionen MLlib CrossValidator pipeline för ka med parameter svep.   

<!-- -->

> [!NOTE]
> Det kan ta flera minuter att köra den här MLlib ka-koden.

<!-- -->

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.ml.classification import LogisticRegression
from pyspark.ml import Pipeline
from pyspark.ml.evaluation import BinaryClassificationEvaluator
from pyspark.ml.tuning import CrossValidator, ParamGridBuilder
from sklearn.metrics import roc_curve,auc

# DEFINE ALGORITHM / MODEL
lr = LogisticRegression()

# DEFINE GRID PARAMETERS
paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
                              .addGrid(lr.maxIter, (5, 10))\
                              .addGrid(lr.tol, (1e-4, 1e-5))\
                              .addGrid(lr.elasticNetParam, (0.25,0.75))\
                              .build()

# DEFINE CV WITH PARAMETER SWEEP
cv = CrossValidator(estimator= lr,
                    estimatorParamMaps=paramGrid,
                    evaluator=BinaryClassificationEvaluator(),
                    numFolds=3)

# CONVERT TO DATA-FRAME: THIS DOES NOT RUN ON RDDs
trainDataFrame = sqlContext.createDataFrame(oneHotTRAINbinary, ["features", "label"])

# TRAIN WITH CROSS-VALIDATION
cv_model = cv.fit(trainDataFrame)


## PREDICT AND EVALUATE ON TEST DATA-SET

# USE TEST DATASET FOR PREDICTION
testDataFrame = sqlContext.createDataFrame(oneHotTESTbinary, ["features", "label"])
test_predictions = cv_model.transform(testDataFrame)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds";
``` 

**UTDATAPARAMETRAR**

Tids åtgång för att köra ovanför cell: 107,98 sekunder

**Rita upp ROC-kurvan.**

*PredictionAndLabelsDF* är registrerad som en tabell *tmp_results* i föregående cell. *tmp_results* kan användas för att utföra frågor och utmatnings resultat i sqlResults data-ram för att rita. Här är koden.

```python
# QUERY RESULTS
%%sql -q -o sqlResults
SELECT label, prediction, probability from tmp_results
```

Här är koden för att rita ROC-kurvan.

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES 
%%local
from sklearn.metrics import roc_curve,auc

# ROC CURVE
prob = [x["values"][1] for x in sqlResults["probability"]]
fpr, tpr, thresholds = roc_curve(sqlResults['label'], prob, pos_label=1);
roc_auc = auc(fpr, tpr)

#PLOT
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

![Logistisk regression ROC-kurva med MLlib-CrossValidator](./media/spark-advanced-data-exploration-modeling/mllib-crossvalidator-roc-curve.png)

### <a name="random-forest-classification"></a>Slumpmässig skogs klassificering
Koden i det här avsnittet visar hur du tränar, utvärderar och sparar en slumpmässig skogs regression som förutsäger om ett tips är betalt för en resa i NYC taxi-resan och pris data uppsättningen.

```python
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
## UN-COMMENT IF YOU WANT TO PRING TREES
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

Area under ROC = 0.985336538462

Tids åtgång för att köra ovanför cell: 26,72 sekunder

### <a name="gradient-boosting-trees-classification"></a>Klassificering av tonings förstärknings träd
Koden i det här avsnittet visar hur du tränar, utvärderar och sparar en modell modell för tonings förstärkning som förutsäger om ett tips är betalt för en resa i NYC taxi-resan och pris data uppsättningen.

```python
# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import GradientBoostedTrees, GradientBoostedTreesModel

# SPECIFY NUMBER OF CATEGORIES FOR CATEGORICAL FEATURES. FEATURE #0 HAS 2 CATEGORIES, FEATURE #2 HAS 2 CATEGORIES, AND SO ON
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

gbtModel = GradientBoostedTrees.trainClassifier(indexedTRAINbinary, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                numIterations=10)
## UNCOMMENT IF YOU WANT TO PRINT TREE DETAILS
#print('Learned classification GBT model:')
#print(bgtModel.toDebugString())

# PREDICT ON TEST DATA AND EVALUATE
predictions = gbtModel.predict(indexedTESTbinary.map(lambda x: x.features))
predictionAndLabels = indexedTESTbinary.map(lambda lp: lp.label).zip(predictions)

# Area under ROC curve
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

Area under ROC = 0.985336538462

Tids åtgång för att köra ovanför cell: 28,13 sekunder

## <a name="predict-tip-amount-with-regression-models-not-using-cv"></a>Förutsäg Tip-mängd med Regressions modeller (inte med CV)
Det här avsnittet visar hur du använder tre modeller för Regressions uppgiften: förutsäga det tips belopp som betalats för en taxi resa baserat på andra Tip-funktioner. De modeller som visas är:

* Vanlig linjär regression
* Slumpmässig skog
* Tonings förstärknings träd

Dessa modeller beskrivs i introduktionen. Varje modell för modell skapande kod är uppdelad i steg: 

1. **Modellera tränings** data med en parameter uppsättning
2. **Modell utvärdering** av en test data uppsättning med mått
3. **Spara modell** i BLOB för framtida användning   

<!-- -->

> [!NOTE] 
> Kors validering används inte med de tre Regressions modellerna i det här avsnittet eftersom detta visades i detalj för logistik Regressions modellerna. Ett exempel som visar hur man använder ka med elastiskt netto för linjär regression finns i bilagan till det här avsnittet.

<!-- -->

<!-- -->

> [!NOTE] 
> I vår miljö kan det finnas problem med konvergens av LinearRegressionWithSGD-modeller, och parametrarna måste ändras/optimeras noggrant för att få en giltig modell. Skalning av variabler bidrar avsevärt till konvergens. Elastisk netto regression, som visas i bilagan till det här avsnittet, kan också användas i stället för LinearRegressionWithSGD.

<!-- -->

### <a name="linear-regression-with-sgd"></a>Linjär regression med SGD
Koden i det här avsnittet visar hur du använder skalade funktioner för att träna en linjär regression som använder Stochastic gradient brantaste (SGD) för optimering, samt hur du poängs ätter, utvärderar och sparar modellen i Azure Blob Storage (WASB).

> [!TIP]
> I vår miljö kan det finnas problem med konvergensen av LinearRegressionWithSGD-modeller, och parametrarna måste ändras/optimeras noggrant för att få en giltig modell. Skalning av variabler bidrar avsevärt till konvergens.
> 
> 

```python
# LINEAR REGRESSION WITH SGD 

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

print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# SAVE MODEL IN BLOB
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

Koefficienter: [0.0141707753435,-0.0252930927087,-0.0231442517137, 0.247070902996, 0.312544147152, 0.360296120645, 0.0122079566092,-0.00456498588241,-0.0898228505177, 0.0714046248793, 0.102171263868, 0.100022455632,-0.00289545676449,-0.00791124681938, 0.54396316518,-0.536293513569, 0.0119076553369,-0.0173039244582, 0.0119632796147, 0.00146764882502]

Skärning: 0.854507624459

RMSE = 1.23485131376

R-SQR = 0.597963951127

Tids åtgång för att köra ovanför cell: 38,62 sekunder

### <a name="random-forest-regression"></a>Slumpmässig skogs regression
Koden i det här avsnittet visar hur du tränar, utvärderar och sparar en slumpmässig skogs modell som förutsäger Tip-mängd för NYC taxi-resan.   

<!-- -->

> [!NOTE]
> Kors validering med parameter svepning med anpassad kod anges i bilagan.

<!-- -->

```python
#PREDICT TIP AMOUNTS USING RANDOM FOREST

# RECORD START TIME
timestart= datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.mllib.tree import RandomForest, RandomForestModel
from pyspark.mllib.util import MLUtils
from pyspark.mllib.evaluation import RegressionMetrics


# TRAIN MODEL
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                    numTrees=25, featureSubsetStrategy="auto",
                                    impurity='variance', maxDepth=10, maxBins=32)
# UN-COMMENT IF YOU WANT TO PRING TREES
#print('Learned classification forest model:')
#print(rfModel.toDebugString())

# PREDICT AND EVALUATE ON TEST DATA-SET
predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
predictionAndLabels = oneHotTESTreg.map(lambda lp: lp.label).zip(predictions)

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

RMSE = 0.931981967875

R-SQR = 0.733445485802

Tids åtgång för att köra ovanför cell: 25,98 sekunder

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

# TRAIN MODEL
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}
gbtModel = GradientBoostedTrees.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo, 
                                                numIterations=10, maxBins=32, maxDepth = 4, learningRate=0.1)

# EVALUATE A TEST DATA-SET
predictions = gbtModel.predict(indexedTESTreg.map(lambda x: x.features))
predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

testMetrics = RegressionMetrics(predictionAndLabels)
print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# PLOT SCATTER-PLOT BETWEEN ACTUAL AND PREDICTED TIP VALUES
test_predictions= sqlContext.createDataFrame(predictionAndLabels)
test_predictions_pddf = test_predictions.toPandas()

# SAVE MODEL IN BLOB
datestamp = unicode(datetime.datetime.now()).replace(' ','').replace(':','_');
btregressionfilename = "GradientBoostingTreeRegression_" + datestamp;
dirfilename = modelDir + btregressionfilename;
gbtModel.save(sc, dirfilename)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**UTDATAPARAMETRAR**

RMSE = 0.928172197114

R-SQR = 0.732680354389

Tids åtgång för att köra ovanför cell: 20,9 sekunder

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
import numpy as np

# PLOT
ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='_1', y='_2', color='blue', alpha = 0.25, label='Actual vs. predicted');
fit = np.polyfit(sqlResults['_1'], sqlResults['_2'], deg=1)
ax.set_title('Actual vs. Predicted Tip Amounts ($)')
ax.set_xlabel("Actual")
ax.set_ylabel("Predicted")
ax.plot(sqlResults['_1'], fit[0] * sqlResults['_1'] + fit[1], color='magenta')
plt.axis([-1, 15, -1, 15])
plt.show(ax)
```

![Utfall – vs-förutsägt – tips-belopp](./media/spark-advanced-data-exploration-modeling/actual-vs-predicted-tips.png)

## <a name="appendix-additional-regression-tasks-using-cross-validation-with-parameter-sweeps"></a>Bilaga: ytterligare Regressions aktiviteter med kors validering med parameter Svep
Den här bilagan innehåller kod som visar hur du gör CV med elastiskt nät för linjär regression och hur du gör ka med parameter svep med anpassad kod för slumpmässig skogs regression.

### <a name="cross-validation-using-elastic-net-for-linear-regression"></a>Kors validering med elastiskt nät för linjär regression
Koden i det här avsnittet visar hur du utför kors validering med elastiskt nät för linjär regression och hur du utvärderar modellen mot test data.

```python
###  CV USING ELASTIC NET FOR LINEAR REGRESSION

# RECORD START TIME
timestart = datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
from pyspark.ml.regression import LinearRegression
from pyspark.ml import Pipeline
from pyspark.ml.evaluation import RegressionEvaluator
from pyspark.ml.tuning import CrossValidator, ParamGridBuilder

# DEFINE ALGORITHM/MODEL
lr = LinearRegression()

# DEFINE GRID PARAMETERS
paramGrid = ParamGridBuilder().addGrid(lr.regParam, (0.01, 0.1))\
                              .addGrid(lr.maxIter, (5, 10))\
                              .addGrid(lr.tol, (1e-4, 1e-5))\
                              .addGrid(lr.elasticNetParam, (0.25,0.75))\
                              .build() 

# DEFINE PIPELINE 
# SIMPLY THE MODEL HERE, WITHOUT TRANSFORMATIONS
pipeline = Pipeline(stages=[lr])

# DEFINE CV WITH PARAMETER SWEEP
cv = CrossValidator(estimator= lr,
                    estimatorParamMaps=paramGrid,
                    evaluator=RegressionEvaluator(),
                    numFolds=3)

# CONVERT TO DATA FRAME, AS CROSSVALIDATOR WON'T RUN ON RDDS
trainDataFrame = sqlContext.createDataFrame(oneHotTRAINreg, ["features", "label"])

# TRAIN WITH CROSS-VALIDATION
cv_model = cv.fit(trainDataFrame)


# EVALUATE MODEL ON TEST SET
testDataFrame = sqlContext.createDataFrame(oneHotTESTreg, ["features", "label"])

# MAKE PREDICTIONS ON TEST DOCUMENTS
# cvModel uses the best model found (lrModel).
predictionAndLabels = cv_model.transform(testDataFrame)

# CONVERT TO DF AND SAVE REGISTER DF AS TABLE
predictionAndLabels.registerTempTable("tmp_results");

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**UTDATAPARAMETRAR**

Tids åtgång för att köra ovanför cell: 161,21 sekunder

**Utvärdera med R-SQR mått**

*tmp_results* registreras som en Hive-tabell i föregående cell. Resultat från tabellen är utdata till *sqlResults* data-ram för att rita. Här är koden

```python
# SELECT RESULTS
%%sql -q -o sqlResults
SELECT label,prediction from tmp_results
```

Här är koden för att beräkna R-SQR.

```python
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
from scipy import stats

#R-SQR TEST METRIC
corstats = stats.linregress(sqlResults['label'],sqlResults['prediction'])
r2 = (corstats[2]*corstats[2])
print("R-sqr = %s" % r2)
```

**UTDATAPARAMETRAR**

R-SQR = 0.619184907088

### <a name="cross-validation-with-parameter-sweep-using-custom-code-for-random-forest-regression"></a>Kors validering med parameter svep med anpassad kod för slumpmässig skogs regression
Koden i det här avsnittet visar hur du utför kors validering med parameter svep med anpassad kod för slumpmässig skogs regression och hur du utvärderar modellen mot test data.

```python
# RECORD START TIME
timestart= datetime.datetime.now()

# LOAD PYSPARK LIBRARIES
# GET ACCURARY FOR HYPERPARAMETERS BASED ON CROSS-VALIDATION IN TRAINING DATA-SET
from pyspark.mllib.tree import RandomForest, RandomForestModel
from pyspark.mllib.util import MLUtils
from pyspark.mllib.evaluation import RegressionMetrics
from sklearn.grid_search import ParameterGrid

## CREATE PARAMETER GRID
grid = [{'maxDepth': [5,10], 'numTrees': [25,50]}]
paramGrid = list(ParameterGrid(grid))

## SPECIFY LEVELS OF CATEGORICAL VARIBLES
categoricalFeaturesInfo={0:2, 1:2, 2:6, 3:4}

# SPECIFY NUMFOLDS AND ARRAY TO HOLD METRICS
nFolds = 3;
numModels = len(paramGrid)
h = 1.0 / nFolds;
metricSum = np.zeros(numModels);

for i in range(nFolds):
    # Create training and x-validation sets
    validateLB = i * h
    validateUB = (i + 1) * h
    condition = (trainData["rand"] >= validateLB) & (trainData["rand"] < validateUB)
    validation = trainData.filter(condition)
    # Create labeled points from data-frames
    if i > 0:
        trainCVLabPt.unpersist()
        validationLabPt.unpersist()
    trainCV = trainData.filter(~condition)
    trainCVLabPt = trainCV.map(parseRowIndexingRegression)
    trainCVLabPt.cache()
    validationLabPt = validation.map(parseRowIndexingRegression)
    validationLabPt.cache()
    # For parameter sets compute metrics from x-validation
    for j in range(numModels):
        maxD = paramGrid[j]['maxDepth']
        numT = paramGrid[j]['numTrees']
        # Train logistic regression model with hypermarameter set
        rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                    numTrees=numT, featureSubsetStrategy="auto",
                                    impurity='variance', maxDepth=maxD, maxBins=32)
        predictions = rfModel.predict(validationLabPt.map(lambda x: x.features))
        predictionAndLabels = validationLabPt.map(lambda lp: lp.label).zip(predictions)
        # Use ROC-AUC as accuracy metrics
        validMetrics = RegressionMetrics(predictionAndLabels)
        metric = validMetrics.rootMeanSquaredError
        metricSum[j] += metric

avgAcc = metricSum/nFolds;
bestParam = paramGrid[np.argmin(avgAcc)];

# UNPERSIST OBJECTS
trainCVLabPt.unpersist()
validationLabPt.unpersist()

## TRAIN FINAL MODL WIHT BEST PARAMETERS
rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                    numTrees=bestParam['numTrees'], featureSubsetStrategy="auto",
                                    impurity='variance', maxDepth=bestParam['maxDepth'], maxBins=32)

# EVALUATE MODEL ON TEST DATA
predictions = rfModel.predict(indexedTESTreg.map(lambda x: x.features))
predictionAndLabels = indexedTESTreg.map(lambda lp: lp.label).zip(predictions)

#PRINT TEST METRICS
testMetrics = RegressionMetrics(predictionAndLabels)
print("RMSE = %s" % testMetrics.rootMeanSquaredError)
print("R-sqr = %s" % testMetrics.r2)

# PRINT ELAPSED TIME
timeend = datetime.datetime.now()
timedelta = round((timeend-timestart).total_seconds(), 2) 
print "Time taken to execute above cell: " + str(timedelta) + " seconds"; 
```

**UTDATAPARAMETRAR**

RMSE = 0.906972198262

R-SQR = 0.740751197012

Tids åtgång för att köra ovanför cell: 69,17 sekunder

### <a name="clean-up-objects-from-memory-and-print-model-locations"></a>Rensa objekt från minnes-och utskrifts modell platser
Används `unpersist()` för att ta bort cachelagrade objekt i minnet.

```python
# UNPERSIST OBJECTS CACHED IN MEMORY

# REMOVE ORIGINAL DFs
taxi_df_train_cleaned.unpersist()
taxi_df_train_with_newFeatures.unpersist()
trainData.unpersist()
trainData.unpersist()

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

**UTDATAPARAMETRAR**

PythonRDD [122] vid RDD vid PythonRDD. Scala: 43

* * Sökväg för utdata till modell filer som ska användas i konsumtions antecknings boken. * * Om du vill använda och räkna upp en oberoende data uppsättning måste du kopiera och klistra in dessa fil namn i "förbruknings antecknings boken".

```python
# PRINT MODEL FILE LOCATIONS FOR CONSUMPTION
print "logisticRegFileLoc = modelDir + \"" + logisticregressionfilename + "\"";
print "linearRegFileLoc = modelDir + \"" + linearregressionfilename + "\"";
print "randomForestClassificationFileLoc = modelDir + \"" + rfclassificationfilename + "\"";
print "randomForestRegFileLoc = modelDir + \"" + rfregressionfilename + "\"";
print "BoostedTreeClassificationFileLoc = modelDir + \"" + btclassificationfilename + "\"";
print "BoostedTreeRegressionFileLoc = modelDir + \"" + btregressionfilename + "\"";
```

**UTDATAPARAMETRAR**

logisticRegFileLoc = modelDir + "LogisticRegressionWithLBFGS_2016-05 -0316 _47_ 30.096528"

linearRegFileLoc = modelDir + "LinearRegressionWithSGD_2016-05 -0316 _51_ 28.433670"

randomForestClassificationFileLoc = modelDir + "RandomForestClassification_2016-05 -0316 _50_ 17.454440"

randomForestRegFileLoc = modelDir + "RandomForestRegression_2016-05 -0316 _51_ 57.331730"

BoostedTreeClassificationFileLoc = modelDir + "GradientBoostingTreeClassification_2016-05 -0316 _50_ 40.138809"

BoostedTreeRegressionFileLoc = modelDir + "GradientBoostingTreeRegression_2016-05 -0316 _52_ 18.827237"

## <a name="whats-next"></a>Nästa steg
Nu när du har skapat Regressions-och klassificerings modeller med Spark-MlLib är du redo att lära dig hur du utvärderar och utvärderar dessa modeller.

**Modell förbrukning:** Information om hur du utvärderar och utvärderar klassificerings-och Regressions modeller som skapats i det här avsnittet finns i [Poäng och utvärdera Spark-skapade maskin inlärnings modeller](spark-model-consumption.md).
