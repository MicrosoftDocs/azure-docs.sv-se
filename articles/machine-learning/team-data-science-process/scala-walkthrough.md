---
title: Data vetenskap med Scala och Spark på Azure-team data science process
description: Använda Scala för övervakade Machine Learning-uppgifter med Spark Scalable MLlib-och Spark ML-paket i ett Azure HDInsight Spark-kluster.
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
ms.openlocfilehash: 9ae4549fe343422bbf60275a97768ca407f2dc7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "93321380"
---
# <a name="data-science-using-scala-and-spark-on-azure"></a>Datavetenskap med Scala och Spark på Azure
Den här artikeln visar hur du använder Scala för övervakade Machine Learning-uppgifter med Spark Scalable MLlib-och Spark ML-paket i ett Azure HDInsight Spark-kluster. Den vägleder dig genom de uppgifter som utgör [data vetenskaps processen](./index.yml): data inmatning och utforskning, visualisering, funktions teknik, modellering och modell användning. Modellerna i artikeln är logistik och linjär regression, slumpmässiga skogar och GBTs (gradient-Boosted trees), förutom två vanliga övervakade Machine Learning-uppgifter:

* Regressions problem: förutsägelse av Tip-mängd ($) för en taxi resa
* Binära klassificering: förutsägelse av tips eller inget tips (1/0) för en taxi resa

Modellerings processen kräver utbildning och utvärdering av en test data uppsättning och relevanta noggrannhets mått. I den här artikeln får du lära dig hur du lagrar dessa modeller i Azure Blob Storage och hur du utvärderar och utvärderar deras förutsägande prestanda. Den här artikeln beskriver också mer avancerade avsnitt om hur du optimerar modeller med hjälp av kors validering och rensning av Hyper-parameter. De data som används är ett exempel på 2013 NYC taxi-resa och pris uppgifter som är tillgängliga på GitHub.

[Scala](https://www.scala-lang.org/), ett språk baserat på den virtuella Java-datorn, integrerar objektorienterade och funktionella språk koncept. Det är ett skalbart språk som lämpar sig väl för distribuerad bearbetning i molnet och körs på Azure Spark-kluster.

[Spark](https://spark.apache.org/) är ett ramverk för parallell bearbetning med öppen källkod som stöder bearbetning i minnet för att öka prestandan hos big data Analytics-program. Motorn för Spark-bearbetning är byggd för hastighet, enkel användning och avancerad analys. Spark: s InMemory-distribuerade beräknings funktioner gör det ett bra val för iterativa algoritmer i maskin inlärnings-och diagram beräkningar. [Spark.ml](https://spark.apache.org/docs/latest/ml-guide.html) -paketet innehåller en enhetlig uppsättning högnivå-API: er som bygger på data ramar som kan hjälpa dig att skapa och finjustera praktiska pipeliner för maskin inlärning. [MLlib](https://spark.apache.org/mllib/) är Spark: s skalbara Machine Learning-bibliotek, som ger modellerings funktioner till den här distribuerade miljön.

[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) är ett Azure-värd erbjudande med Spark med öppen källkod. Den innehåller också stöd för Jupyter Scala-anteckningsböcker i Spark-klustret och kan köra Spark SQL-interaktiva frågor för att transformera, filtrera och visualisera data som lagras i Azure Blob Storage. Scala kodfragment i den här artikeln som innehåller lösningarna och visar relevanta områden för att visualisera data som körs i Jupyter-anteckningsböcker som är installerade på Spark-klustren. Modellerings stegen i dessa avsnitt har kod som visar hur du tränar, utvärderar, sparar och använder varje typ av modell.

Installations stegen och koden i den här artikeln är för Azure HDInsight 3,4 spark 1,6. Koden i den här artikeln och i [Scala-Jupyter Notebook](https://github.com/Azure-Samples/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb) är dock generisk och bör fungera i alla Spark-kluster. Kluster konfigurations-och hanterings stegen kan skilja sig något från vad som visas i den här artikeln om du inte använder HDInsight Spark.

> [!NOTE]
> Ett avsnitt som visar hur du använder python snarare än Scala för att slutföra uppgifter för en process från slut punkt till slut punkt finns i [data vetenskap med Spark på Azure HDInsight](spark-overview.md).
> 
> 

## <a name="prerequisites"></a>Förutsättningar
* Du måste ha en Azure-prenumeration. Om du inte redan har ett kan du [Skaffa en kostnads fri utvärderings version av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Du behöver ett Azure HDInsight 3,4 spark 1,6-kluster för att utföra följande procedurer. Information om hur du skapar ett kluster finns i anvisningarna i [Kom igång: skapa Apache Spark på Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Ange kluster typ och version på menyn **Välj kluster typ** .

![Konfiguration av HDInsight-kluster typ](./media/scala-walkthrough/spark-cluster-on-portal.png)

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
> 
> 

En beskrivning av NYC taxi-resan och anvisningar om hur du kör kod från en Jupyter-anteckningsbok i Spark-klustret finns i relevanta avsnitt i [Översikt över data vetenskap med Spark på Azure HDInsight](spark-overview.md).  

## <a name="execute-scala-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Köra Scala-kod från en Jupyter-anteckningsbok i Spark-klustret
Du kan starta en Jupyter-anteckningsbok från Azure Portal. Leta upp Spark-klustret på instrument panelen och klicka sedan på det för att ange hanterings sidan för klustret. Klicka sedan på **kluster instrument paneler** och klicka sedan på **Jupyter Notebook** för att öppna den antecknings bok som är kopplad till Spark-klustret.

![Kluster instrument panel och Jupyter-anteckningsböcker](./media/scala-walkthrough/spark-jupyter-on-portal.png)

Du kan också komma åt Jupyter-anteckningsböcker på https:// &lt; kluster namn &gt; . azurehdinsight.net/Jupyter. Ersätt *kluster* namn med namnet på klustret. Du behöver lösen ordet för ditt administratörs konto för att få åtkomst till Jupyter-anteckningsböcker.

![Gå till Jupyter-anteckningsböcker med kluster namnet](./media/scala-walkthrough/spark-jupyter-notebook.png)

Välj **Scala** om du vill se en katalog med några exempel på förpaketerade antecknings böcker som använder PySpark-API: et. Utforsknings modellen och poängsättningen med hjälp av Scala. ipynb Notebook som innehåller kod exemplen för den här serien av Spark-ämnen finns på [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/Scala).

Du kan ladda upp antecknings boken direkt från GitHub till Jupyter Notebook-servern i Spark-klustret. På din Jupyter start sida klickar du på knappen **överför** . I Utforskaren klistrar du in URL: en för GitHub (RAW content) i Scala-anteckningsboken och klickar sedan på **Öppna**. Antecknings boken för Scala finns på följande URL:

[Undersökning-modellering-och-poängsättning-using-Scala. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Scala/Exploration-Modeling-and-Scoring-using-Scala.ipynb)

## <a name="setup-preset-spark-and-hive-contexts-spark-magics-and-spark-libraries"></a>Installation: förinställda Spark-och Hive-kontexter, Spark-Magic och Spark-bibliotek
### <a name="preset-spark-and-hive-contexts"></a>Förinställt Spark-och Hive-kontexter

```scala
# SET THE START TIME
import java.util.Calendar
val beginningTime = Calendar.getInstance().getTime()
```

De Spark-kerneler som ingår i Jupyter Notebooks har förinställda kontexter. Du behöver inte uttryckligen ange Spark-eller Hive-kontexterna innan du börjar arbeta med programmet som du utvecklar. De förinställda kontexterna är:

* `sc` för SparkContext
* `sqlContext` för HiveContext

### <a name="spark-magics"></a>Spark-Magic
Spark-kärnan innehåller några fördefinierade "MAGICS", som är särskilda kommandon som du kan anropa med `%%` . Två av dessa kommandon används i följande kod exempel.

* `%%local` anger att koden i efterföljande rader ska köras lokalt. Koden måste vara en giltig Scala-kod.
* `%%sql -o <variable name>` kör en Hive-fråga mot `sqlContext` . Om `-o` parametern skickas, sparas resultatet av frågan i `%%local` Scala-kontexten som en spark data-ram.

Mer information om kernelerna för Jupyter-anteckningsböcker och deras fördefinierade "magiska" som du anropar med `%%` (till exempel `%%local` ) finns i [kernels som är tillgängliga för Jupyter-anteckningsböcker med HDInsight Spark Linux-kluster i HDInsight](../../hdinsight/spark/apache-spark-jupyter-notebook-kernels.md).

### <a name="import-libraries"></a>Importera bibliotek
Importera Spark, MLlib och andra bibliotek som du behöver med hjälp av följande kod.

```scala
# IMPORT SPARK AND JAVA LIBRARIES
import org.apache.spark.sql.SQLContext
import org.apache.spark.sql.functions._
import java.text.SimpleDateFormat
import java.util.Calendar
import sqlContext.implicits._
import org.apache.spark.sql.Row

# IMPORT SPARK SQL FUNCTIONS
import org.apache.spark.sql.types.{StructType, StructField, StringType, IntegerType, FloatType, DoubleType}
import org.apache.spark.sql.functions.rand

# IMPORT SPARK ML FUNCTIONS
import org.apache.spark.ml.Pipeline
import org.apache.spark.ml.feature.{StringIndexer, VectorAssembler, OneHotEncoder, VectorIndexer, Binarizer}
import org.apache.spark.ml.tuning.{ParamGridBuilder, TrainValidationSplit, CrossValidator}
import org.apache.spark.ml.regression.{LinearRegression, LinearRegressionModel, RandomForestRegressor, RandomForestRegressionModel, GBTRegressor, GBTRegressionModel}
import org.apache.spark.ml.classification.{LogisticRegression, LogisticRegressionModel, RandomForestClassifier, RandomForestClassificationModel, GBTClassifier, GBTClassificationModel}
import org.apache.spark.ml.evaluation.{BinaryClassificationEvaluator, RegressionEvaluator, MulticlassClassificationEvaluator}

# IMPORT SPARK MLLIB FUNCTIONS
import org.apache.spark.mllib.linalg.{Vector, Vectors}
import org.apache.spark.mllib.util.MLUtils
import org.apache.spark.mllib.classification.{LogisticRegressionWithLBFGS, LogisticRegressionModel}
import org.apache.spark.mllib.regression.{LabeledPoint, LinearRegressionWithSGD, LinearRegressionModel}
import org.apache.spark.mllib.tree.{GradientBoostedTrees, RandomForest}
import org.apache.spark.mllib.tree.configuration.BoostingStrategy
import org.apache.spark.mllib.tree.model.{GradientBoostedTreesModel, RandomForestModel, Predict}
import org.apache.spark.mllib.evaluation.{BinaryClassificationMetrics, MulticlassMetrics, RegressionMetrics}

# SPECIFY SQLCONTEXT
val sqlContext = new SQLContext(sc)
```

## <a name="data-ingestion"></a>Datainhämtning
Det första steget i data vetenskaps processen är att mata in de data som du vill analysera. Du hämtar data från externa källor eller system där de finns i data utforsknings-och modell miljö. I den här artikeln är de data du matar in ett kopplat 0,1%-exempel på taxi resan och pris filen (lagras som en. TSV-fil). Data utforsknings-och modellerings miljön är Spark. Det här avsnittet innehåller koden för att slutföra följande serie aktiviteter:

1. Ange katalog Sök vägar för data och modell lagring.
2. Läs i indata-uppsättningen (lagras som en. TSV-fil).
3. Definiera ett schema för data och rensa data.
4. Skapa en rensad data ram och cachelagra den i minnet.
5. Registrera data som en temporär tabell i SQLContext.
6. Fråga tabellen och importera resultatet till en data ram.

### <a name="set-directory-paths-for-storage-locations-in-azure-blob-storage"></a>Ange katalog Sök vägar för lagrings platser i Azure Blob Storage
Spark kan läsa och skriva till Azure Blob Storage. Du kan använda Spark för att bearbeta alla befintliga data och sedan lagra resultaten igen i Blob Storage.

Om du vill spara modeller eller filer i Blob Storage måste du ange sökvägen korrekt. Referera till standard behållaren som är kopplad till Spark-klustret med hjälp av en sökväg som börjar med `wasb:///` . Referera till andra platser med `wasb://` .

Följande kod exempel anger platsen för de indata som ska läsas och sökvägen till Blob Storage som är kopplat till Spark-klustret där modellen ska sparas.

```scala
# SET PATHS TO DATA AND MODEL FILE LOCATIONS
# INGEST DATA AND SPECIFY HEADERS FOR COLUMNS
val taxi_train_file = sc.textFile("wasb://mllibwalkthroughs@cdspsparksamples.blob.core.windows.net/Data/NYCTaxi/JoinedTaxiTripFare.Point1Pct.Train.tsv")
val header = taxi_train_file.first;

# SET THE MODEL STORAGE DIRECTORY PATH
# NOTE THAT THE FINAL BACKSLASH IN THE PATH IS REQUIRED.
val modelDir = "wasb:///user/remoteuser/NYCTaxi/Models/";
```

### <a name="import-data-create-an-rdd-and-define-a-data-frame-according-to-the-schema"></a>Importera data, skapa en RDD och definiera en data ram enligt schemat

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# DEFINE THE SCHEMA BASED ON THE HEADER OF THE FILE
val sqlContext = new SQLContext(sc)
val taxi_schema = StructType(
    Array(
        StructField("medallion", StringType, true),
        StructField("hack_license", StringType, true),
        StructField("vendor_id", StringType, true),
        StructField("rate_code", DoubleType, true),
        StructField("store_and_fwd_flag", StringType, true),
        StructField("pickup_datetime", StringType, true),
        StructField("dropoff_datetime", StringType, true),
        StructField("pickup_hour", DoubleType, true),
        StructField("pickup_week", DoubleType, true),
        StructField("weekday", DoubleType, true),
        StructField("passenger_count", DoubleType, true),
        StructField("trip_time_in_secs", DoubleType, true),
        StructField("trip_distance", DoubleType, true),
        StructField("pickup_longitude", DoubleType, true),
        StructField("pickup_latitude", DoubleType, true),
        StructField("dropoff_longitude", DoubleType, true),
        StructField("dropoff_latitude", DoubleType, true),
        StructField("direct_distance", StringType, true),
        StructField("payment_type", StringType, true),
        StructField("fare_amount", DoubleType, true),
        StructField("surcharge", DoubleType, true),
        StructField("mta_tax", DoubleType, true),
        StructField("tip_amount", DoubleType, true),
        StructField("tolls_amount", DoubleType, true),
        StructField("total_amount", DoubleType, true),
        StructField("tipped", DoubleType, true),
        StructField("tip_class", DoubleType, true)
        )
    )

# CAST VARIABLES ACCORDING TO THE SCHEMA
val taxi_temp = (taxi_train_file.map(_.split("\t"))
                        .filter((r) => r(0) != "medallion")
                         .map(p => Row(p(0), p(1), p(2),
                            p(3).toDouble, p(4), p(5), p(6), p(7).toDouble, p(8).toDouble, p(9).toDouble, p(10).toDouble,
                            p(11).toDouble, p(12).toDouble, p(13).toDouble, p(14).toDouble, p(15).toDouble, p(16).toDouble,
                            p(17), p(18), p(19).toDouble, p(20).toDouble, p(21).toDouble, p(22).toDouble,
                            p(23).toDouble, p(24).toDouble, p(25).toDouble, p(26).toDouble)))


# CREATE AN INITIAL DATA FRAME AND DROP COLUMNS, AND THEN CREATE A CLEANED DATA FRAME BY FILTERING FOR UNWANTED VALUES OR OUTLIERS
val taxi_train_df = sqlContext.createDataFrame(taxi_temp, taxi_schema)

val taxi_df_train_cleaned = (taxi_train_df.drop(taxi_train_df.col("medallion"))
        .drop(taxi_train_df.col("hack_license")).drop(taxi_train_df.col("store_and_fwd_flag"))
        .drop(taxi_train_df.col("pickup_datetime")).drop(taxi_train_df.col("dropoff_datetime"))
        .drop(taxi_train_df.col("pickup_longitude")).drop(taxi_train_df.col("pickup_latitude"))
        .drop(taxi_train_df.col("dropoff_longitude")).drop(taxi_train_df.col("dropoff_latitude"))
        .drop(taxi_train_df.col("surcharge")).drop(taxi_train_df.col("mta_tax"))
        .drop(taxi_train_df.col("direct_distance")).drop(taxi_train_df.col("tolls_amount"))
        .drop(taxi_train_df.col("total_amount")).drop(taxi_train_df.col("tip_class"))
        .filter("passenger_count > 0 and passenger_count < 8 AND payment_type in ('CSH', 'CRD') AND tip_amount >= 0 AND tip_amount < 30 AND fare_amount >= 1 AND fare_amount < 150 AND trip_distance > 0 AND trip_distance < 100 AND trip_time_in_secs > 30 AND trip_time_in_secs < 7200"));

# CACHE AND MATERIALIZE THE CLEANED DATA FRAME IN MEMORY
taxi_df_train_cleaned.cache()
taxi_df_train_cleaned.count()

# REGISTER THE DATA FRAME AS A TEMPORARY TABLE IN SQLCONTEXT
taxi_df_train_cleaned.registerTempTable("taxi_train")

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```

**Utdataparametrar**

Tid att köra cellen: 8 sekunder.

### <a name="query-the-table-and-import-results-in-a-data-frame"></a>Fråga tabellen och importera resultat i en data ram
Sedan frågar du tabellen efter uppgifter om pris, passagerare och Tip. filtrera bort skadade och yttre data; och skriva ut flera rader.

```scala
# QUERY THE DATA
val sqlStatement = """
    SELECT fare_amount, passenger_count, tip_amount, tipped
    FROM taxi_train
    WHERE passenger_count > 0 AND passenger_count < 7
    AND fare_amount > 0 AND fare_amount < 200
    AND payment_type in ('CSH', 'CRD')
    AND tip_amount > 0 AND tip_amount < 25
"""
val sqlResultsDF = sqlContext.sql(sqlStatement)

# SHOW ONLY THE TOP THREE ROWS
sqlResultsDF.show(3)
```

**Utdataparametrar**

| fare_amount | passenger_count | tip_amount | lutad |
| --- | --- | --- | --- |
|        13,5 |1.0 |2.9 |1.0 |
|        16,0 |2.0 |3.4 |1.0 |
|        10.5 |2.0 |1.0 |1.0 |

## <a name="data-exploration-and-visualization"></a>Data utforskning och visualisering
När du hämtar data till Spark är nästa steg i data vetenskaps processen att få en djupare förståelse för data genom utforskning och visualisering. I det här avsnittet undersöker du taxi-data med hjälp av SQL-frågor. Importera sedan resultaten till en data ram för att rita upp målvärdena och potentiella funktioner för visuell granskning med hjälp av funktionen för automatisk visualiserings Jupyter.

### <a name="use-local-and-sql-magic-to-plot-data"></a>Använd lokala och SQL Magic för att rita data
Som standard är utdata från alla kodfragment som du kör från en Jupyter-anteckningsbok tillgänglig i kontexten för den session som behålls på arbetsnoderna. Om du vill spara en resa till arbetsnoderna för varje beräkning, och om alla data som behövs för din beräkning är tillgängliga lokalt på Jupyter (vilket är Head-noden), kan du använda `%%local` Magic för att köra kodfragmentet på Jupyter-servern.

* **SQL Magic** ( `%%sql` ). HDInsight Spark-kärnan stöder enkla infogade HiveQL-frågor mot SQLContext. Argumentet ( `-o VARIABLE_NAME` ) behåller utdata från SQL-frågan som ett Pandas data fält på Jupyter-servern. Den här inställningen innebär att utdata är tillgängliga i det lokala läget.
* `%%local`**Magic**. `%%local`Magic kör koden lokalt på Jupyter-servern, som är Head-noden i HDInsight-klustret. Normalt använder du `%%local` Magic tillsammans med `%%sql` Magic med `-o` parametern. `-o`Parametern behåller SQL-frågans utdata lokalt och sedan `%%local` utlöser Magic nästa uppsättning kodfragment för att köras lokalt mot utdata från SQL-frågorna som är bestående lokalt.

### <a name="query-the-data-by-using-sql"></a>Fråga data med hjälp av SQL
Den här frågan hämtar taxi resor per pris, antal passagerare och Tip-belopp.

```scala
# RUN THE SQL QUERY
%%sql -q -o sqlResults
SELECT fare_amount, passenger_count, tip_amount, tipped FROM taxi_train WHERE passenger_count > 0 AND passenger_count < 7 AND fare_amount > 0 AND fare_amount < 200 AND payment_type in ('CSH', 'CRD') AND tip_amount > 0 AND tip_amount < 25
```

I följande kod `%%local` skapar Magic en lokal data ram, sqlResults. Du kan använda sqlResults för att rita med matplotlib.

> [!TIP]
> Local Magic används flera gånger i den här artikeln. Om din data uppsättning är stor kan du prova att skapa en data ram som får plats i lokalt minne.
> 
> 

### <a name="plot-the-data"></a>Rita data
Du kan rita genom att använda python-kod när data ramen är i ett lokalt sammanhang som Pandas data ram.

```scala
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES.
# CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, ETC.)
sqlResults
```

 Spark-kärnan visualiserar automatiskt utdata från SQL-frågor (HiveQL) när du har kört koden. Du kan välja mellan flera olika typer av visualiseringar:

* Tabell
* Cirkel
* Linje
* Område
* Stapel

Här är koden för att rita data:

```scala
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
import matplotlib.pyplot as plt
%matplotlib inline

# PLOT TIP BY PAYMENT TYPE AND PASSENGER COUNT
ax1 = sqlResults[['tip_amount']].plot(kind='hist', bins=25, facecolor='lightblue')
ax1.set_title('Tip amount distribution')
ax1.set_xlabel('Tip Amount ($)')
ax1.set_ylabel('Counts')
plt.suptitle('')
plt.show()

# PLOT TIP BY PASSENGER COUNT
ax2 = sqlResults.boxplot(column=['tip_amount'], by=['passenger_count'])
ax2.set_title('Tip amount by Passenger count')
ax2.set_xlabel('Passenger count')
ax2.set_ylabel('Tip Amount ($)')
plt.suptitle('')
plt.show()

# PLOT TIP AMOUNT BY FARE AMOUNT; SCALE POINTS BY PASSENGER COUNT
ax = sqlResults.plot(kind='scatter', x= 'fare_amount', y = 'tip_amount', c='blue', alpha = 0.10, s=5*(sqlResults.passenger_count))
ax.set_title('Tip amount by Fare amount')
ax.set_xlabel('Fare Amount ($)')
ax.set_ylabel('Tip Amount ($)')
plt.axis([-2, 80, -2, 20])
plt.show()
```

**Utdataparametrar**

![Histogram för spets belopp](./media/scala-walkthrough/plot-tip-amount-histogram.png)

![Tips belopp efter antal passagerare](./media/scala-walkthrough/plot-tip-amount-by-passenger-count.png)

![Tips belopp per avgifts belopp](./media/scala-walkthrough/plot-tip-amount-by-fare-amount.png)

## <a name="create-features-and-transform-features-and-then-prep-data-for-input-into-modeling-functions"></a>Skapa funktioner och transformera funktioner och Förbered sedan data för indata till modellerings funktioner
För Tree-baserade modellerings funktioner från Spark ML och MLlib måste du förbereda mål och funktioner med hjälp av en mängd olika metoder, till exempel diskretisering, indexering, en snabb kodning och vectorization. Här följer de procedurer som beskrivs i det här avsnittet:

1. Skapa en ny funktion genom att **diskretisering** timmar i tid-buckets.
2. Använd **indexering och en snabb kodning** för kategoriska-funktioner.
3. **Sampla och dela data uppsättningen** i utbildning och test-bråk.
4. **Ange inlärnings variabel och funktioner** och skapa sedan indexerad eller en enkel, kodad utbildning och testa indata med märkta punkt elastiska data uppsättningar (RDD) eller data ramar.
5. **Kategorisera och vectorize automatiskt funktioner och mål** som ska användas som indata för Machine Learning-modeller.

### <a name="create-a-new-feature-by-binning-hours-into-traffic-time-buckets"></a>Skapa en ny funktion genom diskretisering timmar i tid-buckets
Den här koden visar hur du skapar en ny funktion genom diskretisering timmar i tid-buckets för trafik och hur du cachelagrar den resulterande data ramen i minnet. När RDD och data ramar används flera gånger, leder cachelagring till förbättrade körnings tider. Därför kommer du att cachelagra RDD och data ramar i flera steg i följande procedurer.

```scala
# CREATE FOUR BUCKETS FOR TRAFFIC TIMES
val sqlStatement = """
    SELECT *,
    CASE
     WHEN (pickup_hour <= 6 OR pickup_hour >= 20) THEN "Night"
     WHEN (pickup_hour >= 7 AND pickup_hour <= 10) THEN "AMRush"
     WHEN (pickup_hour >= 11 AND pickup_hour <= 15) THEN "Afternoon"
     WHEN (pickup_hour >= 16 AND pickup_hour <= 19) THEN "PMRush"
    END as TrafficTimeBins
    FROM taxi_train
"""
val taxi_df_train_with_newFeatures = sqlContext.sql(sqlStatement)

# CACHE THE DATA FRAME IN MEMORY AND MATERIALIZE THE DATA FRAME IN MEMORY
taxi_df_train_with_newFeatures.cache()
taxi_df_train_with_newFeatures.count()
```

### <a name="indexing-and-one-hot-encoding-of-categorical-features"></a>Indexering och en snabb kodning av kategoriska-funktioner
Modellerings-och förutsägelse funktionerna i MLlib kräver funktioner med kategoriska indata som ska indexeras eller kodas innan de används. Det här avsnittet visar hur du kan indexera eller koda kategoriska-funktioner för inmatade funktioner i modellerings funktionerna.

Du måste indexera eller koda dina modeller på olika sätt beroende på modell. Till exempel kräver logistik-och linjära Regressions modeller en-frekvent kodning. En funktion med tre kategorier kan till exempel expanderas i tre funktions kolumner. Varje kolumn skulle innehålla 0 eller 1 beroende på kategori för en observation. MLlib tillhandahåller funktionen [OneHotEncoder](https://scikit-learn.org/stable/modules/generated/sklearn.preprocessing.OneHotEncoder.html#sklearn.preprocessing.OneHotEncoder) för enkel kodning. Denna kodare mappar en kolumn med etikett index till en kolumn med binära vektorer med högst ett enda värde. Med den här kodningen kan algoritmer som förväntar sig numeriska värde funktioner, till exempel Logistisk regression, användas för kategoriska-funktioner.

Här transformerar du bara fyra variabler för att visa exempel, som är text strängar. Du kan också indexera andra variabler, till exempel veckodag, som representeras av numeriska värden som kategoriska variabler.

Använd Functions från MLlib för att indexera, använda `StringIndexer()` och för en snabb kodning `OneHotEncoder()` . Här är koden för att indexera och koda kategoriska-funktioner:

```scala
# CREATE INDEXES AND ONE-HOT ENCODED VECTORS FOR SEVERAL CATEGORICAL FEATURES

# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# INDEX AND ENCODE VENDOR_ID
val stringIndexer = new StringIndexer().setInputCol("vendor_id").setOutputCol("vendorIndex").fit(taxi_df_train_with_newFeatures)
val indexed = stringIndexer.transform(taxi_df_train_with_newFeatures)
val encoder = new OneHotEncoder().setInputCol("vendorIndex").setOutputCol("vendorVec")
val encoded1 = encoder.transform(indexed)

# INDEX AND ENCODE RATE_CODE
val stringIndexer = new StringIndexer().setInputCol("rate_code").setOutputCol("rateIndex").fit(encoded1)
val indexed = stringIndexer.transform(encoded1)
val encoder = new OneHotEncoder().setInputCol("rateIndex").setOutputCol("rateVec")
val encoded2 = encoder.transform(indexed)

# INDEX AND ENCODE PAYMENT_TYPE
val stringIndexer = new StringIndexer().setInputCol("payment_type").setOutputCol("paymentIndex").fit(encoded2)
val indexed = stringIndexer.transform(encoded2)
val encoder = new OneHotEncoder().setInputCol("paymentIndex").setOutputCol("paymentVec")
val encoded3 = encoder.transform(indexed)

# INDEX AND TRAFFIC TIME BINS
val stringIndexer = new StringIndexer().setInputCol("TrafficTimeBins").setOutputCol("TrafficTimeBinsIndex").fit(encoded3)
val indexed = stringIndexer.transform(encoded3)
val encoder = new OneHotEncoder().setInputCol("TrafficTimeBinsIndex").setOutputCol("TrafficTimeBinsVec")
val encodedFinal = encoder.transform(indexed)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```

**Utdataparametrar**

Tid för att köra cellen: 4 sekunder.

### <a name="sample-and-split-the-data-set-into-training-and-test-fractions"></a>Exempel och dela data uppsättningen i utbildning och test-bråk
Den här koden skapar en slumpmässig sampling av data (25% i det här exemplet). Även om sampling inte krävs för det här exemplet på grund av storleken på data uppsättningen, visar artikeln hur du kan prova så att du vet hur du kan använda den för dina egna problem när det behövs. När exempel är stora kan detta spara mycket tid medan du tränar modeller. Sedan delar vi upp exemplet i en utbildnings del (75% i det här exemplet) och en test del (25% i det här exemplet) för användning i klassificerings-och Regressions modellering.

Lägg till ett slumptal (mellan 0 och 1) på varje rad (i en "slump"-kolumn) som kan användas för att välja kors validerings vikning under träning.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# SPECIFY SAMPLING AND SPLITTING FRACTIONS
val samplingFraction = 0.25;
val trainingFraction = 0.75;
val testingFraction = (1-trainingFraction);
val seed = 1234;
val encodedFinalSampledTmp = encodedFinal.sample(withReplacement = false, fraction = samplingFraction, seed = seed)
val sampledDFcount = encodedFinalSampledTmp.count().toInt

val generateRandomDouble = udf(() => {
    scala.util.Random.nextDouble
})

# ADD A RANDOM NUMBER FOR CROSS-VALIDATION
val encodedFinalSampled = encodedFinalSampledTmp.withColumn("rand", generateRandomDouble());

# SPLIT THE SAMPLED DATA FRAME INTO TRAIN AND TEST, WITH A RANDOM COLUMN ADDED FOR DOING CROSS-VALIDATION (SHOWN LATER)
# INCLUDE A RANDOM COLUMN FOR CREATING CROSS-VALIDATION FOLDS
val splits = encodedFinalSampled.randomSplit(Array(trainingFraction, testingFraction), seed = seed)
val trainData = splits(0)
val testData = splits(1)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```

**Utdataparametrar**

Tid att köra cellen: 2 sekunder.

### <a name="specify-training-variable-and-features-and-then-create-indexed-or-one-hot-encoded-training-and-testing-input-labeled-point-rdds-or-data-frames"></a>Ange inlärnings variabel och funktioner och skapa sedan indexerad eller en-frekvent kodad utbildning och testa indata med etiketten peka RDD eller data ramar
Det här avsnittet innehåller kod som visar hur du indexerar kategoriska text data som en etikettad punkt data typ och kodar den så att du kan använda den för att träna och testa MLlib logistik regression och andra klassificerings modeller. Märkta punkt objekt är RDD som är formaterade på ett sätt som är indata med de flesta Machine Learning-algoritmer i MLlib. En [märkt punkt](https://spark.apache.org/docs/latest/mllib-data-types.html#labeled-point) är en lokal vektor, antingen kompakt eller sparse, som är associerad med en etikett/ett svar.

I den här koden anger du Target-variabeln (Dependent) och de funktioner som ska användas för att träna modeller. Sedan kan du skapa indexerad eller en-frekvent kodad utbildning och testa indata med etiketten punkt RDD eller data ramar.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# MAP NAMES OF FEATURES AND TARGETS FOR CLASSIFICATION AND REGRESSION PROBLEMS
val featuresIndOneHot = List("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))
val featuresIndIndex = List("paymentIndex", "vendorIndex", "rateIndex", "TrafficTimeBinsIndex", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount").map(encodedFinalSampled.columns.indexOf(_))

# SPECIFY THE TARGET FOR CLASSIFICATION ('tipped') AND REGRESSION ('tip_amount') PROBLEMS
val targetIndBinary = List("tipped").map(encodedFinalSampled.columns.indexOf(_))
val targetIndRegression = List("tip_amount").map(encodedFinalSampled.columns.indexOf(_))

# CREATE INDEXED LABELED POINT RDD OBJECTS
val indexedTRAINbinary = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
val indexedTESTbinary = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndBinary(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
val indexedTRAINreg = trainData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))
val indexedTESTreg = testData.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)))

# CREATE INDEXED DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
val indexedTRAINbinaryDF = indexedTRAINbinary.toDF()
val indexedTESTbinaryDF = indexedTESTbinary.toDF()
val indexedTRAINregDF = indexedTRAINreg.toDF()
val indexedTESTregDF = indexedTESTreg.toDF()

# CREATE ONE-HOT ENCODED (VECTORIZED) DATA FRAMES THAT YOU CAN USE TO TRAIN BY USING SPARK ML FUNCTIONS
val assemblerOneHot = new VectorAssembler().setInputCols(Array("paymentVec", "vendorVec", "rateVec", "TrafficTimeBinsVec", "pickup_hour", "weekday", "passenger_count", "trip_time_in_secs", "trip_distance", "fare_amount")).setOutputCol("features")
val OneHotTRAIN = assemblerOneHot.transform(trainData)
val OneHotTEST = assemblerOneHot.transform(testData)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```

**Utdataparametrar**

Tid för att köra cellen: 4 sekunder.

### <a name="automatically-categorize-and-vectorize-features-and-targets-to-use-as-inputs-for-machine-learning-models"></a>Kategorisera och vectorize automatiskt funktioner och mål som ska användas som indata för Machine Learning-modeller
Använd Spark ML för att kategorisera mål och funktioner som ska användas i trädbaserade modellerings funktioner. Koden slutför två uppgifter:

* Skapar ett binärt mål för klassificering genom att tilldela värdet 0 eller 1 till varje data punkt mellan 0 och 1 genom att använda ett tröskelvärde på 0,5.
* Kategoriserar funktioner automatiskt. Om antalet distinkta numeriska värden för en funktion är mindre än 32, kategoriseras funktionen.

Här är koden för de här två uppgifterna.

```scala
# CATEGORIZE FEATURES AND BINARIZE THE TARGET FOR THE BINARY CLASSIFICATION PROBLEM

# TRAIN DATA
val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
val indexerModel = indexer.fit(indexedTRAINbinaryDF)
val indexedTrainwithCatFeat = indexerModel.transform(indexedTRAINbinaryDF)
val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
val indexedTRAINwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

# TEST DATA
val indexerModel = indexer.fit(indexedTESTbinaryDF)
val indexedTrainwithCatFeat = indexerModel.transform(indexedTESTbinaryDF)
val binarizer: Binarizer = new Binarizer().setInputCol("label").setOutputCol("labelBin").setThreshold(0.5)
val indexedTESTwithCatFeatBinTarget = binarizer.transform(indexedTrainwithCatFeat)

# CATEGORIZE FEATURES FOR THE REGRESSION PROBLEM
# CREATE PROPERLY INDEXED AND CATEGORIZED DATA FRAMES FOR TREE-BASED MODELS

# TRAIN DATA
val indexer = new VectorIndexer().setInputCol("features").setOutputCol("featuresCat").setMaxCategories(32)
val indexerModel = indexer.fit(indexedTRAINregDF)
val indexedTRAINwithCatFeat = indexerModel.transform(indexedTRAINregDF)

# TEST DATA
val indexerModel = indexer.fit(indexedTESTbinaryDF)
val indexedTESTwithCatFeat = indexerModel.transform(indexedTESTregDF)
```


## <a name="binary-classification-model-predict-whether-a-tip-should-be-paid"></a>Binär klassificerings modell: Förutsäg om ett tips ska betalas
I det här avsnittet skapar du tre typer av binära klassificerings modeller för att förutsäga om ett tips ska betalas eller inte:

* En **logistik Regressions modell** med Spark ml- `LogisticRegression()` funktionen
* En **slumpmässig skogs klassificerings modell** med Spark ml- `RandomForestClassifier()` funktionen
* En **träd klassificerings modell med tonings förstärkning** med hjälp av `GradientBoostedTrees()` funktionen MLlib

### <a name="create-a-logistic-regression-model"></a>Skapa en logistik Regressions modell
Skapa sedan en logistik Regressions modell med hjälp av Spark ML- `LogisticRegression()` funktionen. Du skapar modell skapande koden i en serie steg:

1. **Träna modell** data med en parameter uppsättning.
2. **Utvärdera modellen** på en test data uppsättning med mått.
3. **Spara modellen** i Blob Storage för framtida konsumtion.
4. **Betygs ätt modellen** mot test data.
5. **Rita resultatet** med Roc-kurvor (Receive Operational enkarakteristik).

Här är koden för de här procedurerna:

```scala
# CREATE A LOGISTIC REGRESSION MODEL
val lr = new LogisticRegression().setLabelCol("tipped").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)
val lrModel = lr.fit(OneHotTRAIN)

# PREDICT ON THE TEST DATA SET
val predictions = lrModel.transform(OneHotTEST)

# SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
val ROC = evaluator.evaluate(predictions)
println("ROC on test data = " + ROC)

# SAVE THE MODEL
val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
val modelName = "LogisticRegression__"
val filename = modelDir.concat(modelName).concat(datestamp)
lrModel.save(filename);
```

Läs in, score och spara resultatet.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# LOAD THE SAVED MODEL AND SCORE THE TEST DATA SET
val savedModel = org.apache.spark.ml.classification.LogisticRegressionModel.load(filename)
println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

# SCORE THE MODEL ON THE TEST DATA
val predictions = savedModel.transform(OneHotTEST).select("tipped","probability","rawPrediction")
predictions.registerTempTable("testResults")

# SELECT `BinaryClassificationEvaluator()` TO COMPUTE THE TEST ERROR
val evaluator = new BinaryClassificationEvaluator().setLabelCol("tipped").setRawPredictionCol("probability").setMetricName("areaUnderROC")
val ROC = evaluator.evaluate(predictions)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

# PRINT THE ROC RESULTS
println("ROC on test data = " + ROC)
```

**Utdataparametrar**

ROC för test data = 0.9827381497557599

Använd python på lokala Pandas data ramar för att rita ROC-kurvan.

```scala
# QUERY THE RESULTS
%%sql -q -o sqlResults
SELECT tipped, probability from testResults


# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
%matplotlib inline
from sklearn.metrics import roc_curve,auc

sqlResults['probFloat'] = sqlResults.apply(lambda row: row['probability'].values()[0][1], axis=1)
predictions_pddf = sqlResults[["tipped","probFloat"]]

# PREDICT THE ROC CURVE
# predictions_pddf = sqlResults.rename(columns={'_1': 'probability', 'tipped': 'label'})
prob = predictions_pddf["probFloat"]
fpr, tpr, thresholds = roc_curve(predictions_pddf['tipped'], prob, pos_label=1);
roc_auc = auc(fpr, tpr)

# PLOT THE ROC CURVE
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

**Utdataparametrar**

![Tips eller ingen spets ROC kurva](./media/scala-walkthrough/plot-roc-curve-tip-or-not.png)

### <a name="create-a-random-forest-classification-model"></a>Skapa en slumpmässig skogs klassificerings modell
Skapa sedan en slumpmässig skogs klassificerings modell med hjälp av Spark ML `RandomForestClassifier()` -funktionen och utvärdera sedan modellen på test data.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# CREATE THE RANDOM FOREST CLASSIFIER MODEL
val rf = new RandomForestClassifier().setLabelCol("labelBin").setFeaturesCol("featuresCat").setNumTrees(10).setSeed(1234)

# FIT THE MODEL
val rfModel = rf.fit(indexedTRAINwithCatFeatBinTarget)
val predictions = rfModel.transform(indexedTESTwithCatFeatBinTarget)

# EVALUATE THE MODEL
val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
val Test_f1Score = evaluator.evaluate(predictions)
println("F1 score on test data: " + Test_f1Score);

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

# CALCULATE BINARY CLASSIFICATION EVALUATION METRICS
val evaluator = new BinaryClassificationEvaluator().setLabelCol("label").setRawPredictionCol("probability").setMetricName("areaUnderROC")
val ROC = evaluator.evaluate(predictions)
println("ROC on test data = " + ROC)
```

**Utdataparametrar**

ROC för test data = 0.9847103571552683

### <a name="create-a-gbt-classification-model"></a>Skapa en GBT klassificerings modell
Skapa sedan en GBT klassificerings modell med hjälp av MLlib `GradientBoostedTrees()` -funktionen och utvärdera sedan modellen på test data.

```scala
# TRAIN A GBT CLASSIFICATION MODEL BY USING MLLIB AND A LABELED POINT

# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# DEFINE THE GBT CLASSIFICATION MODEL
val boostingStrategy = BoostingStrategy.defaultParams("Classification")
boostingStrategy.numIterations = 20
boostingStrategy.treeStrategy.numClasses = 2
boostingStrategy.treeStrategy.maxDepth = 5
boostingStrategy.treeStrategy.categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

# TRAIN THE MODEL
val gbtModel = GradientBoostedTrees.train(indexedTRAINbinary, boostingStrategy)

# SAVE THE MODEL IN BLOB STORAGE
val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
val modelName = "GBT_Classification__"
val filename = modelDir.concat(modelName).concat(datestamp)
gbtModel.save(sc, filename);

# EVALUATE THE MODEL ON TEST INSTANCES AND THE COMPUTE TEST ERROR
val labelAndPreds = indexedTESTbinary.map { point =>
  val prediction = gbtModel.predict(point.features)
  (point.label, prediction)
}
val testErr = labelAndPreds.filter(r => r._1 != r._2).count.toDouble / indexedTRAINbinary.count()
//println("Learned classification GBT model:\n" + gbtModel.toDebugString)
println("Test Error = " + testErr)

# USE BINARY AND MULTICLASS METRICS TO EVALUATE THE MODEL ON THE TEST DATA
val metrics = new MulticlassMetrics(labelAndPreds)
println(s"Precision: ${metrics.precision}")
println(s"Recall: ${metrics.recall}")
println(s"F1 Score: ${metrics.fMeasure}")

val metrics = new BinaryClassificationMetrics(labelAndPreds)
println(s"Area under PR curve: ${metrics.areaUnderPR}")
println(s"Area under ROC curve: ${metrics.areaUnderROC}")

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

# PRINT THE ROC METRIC
println(s"Area under ROC curve: ${metrics.areaUnderROC}")
```

**Utdataparametrar**

Area under ROC kurva: 0.9846895479241554

## <a name="regression-model-predict-tip-amount"></a>Regressions modell: Förutsäg Tip-mängd
I det här avsnittet skapar du två typer av Regressions modeller för att förutsäga Tip-mängden:

* En **regelbunden linjär Regressions modell** med hjälp av Spark ml- `LinearRegression()` funktionen. Du sparar modellen och utvärderar modellen på test data.
* En **Regressions modell för tonings förstärkning** med Spark ml- `GBTRegressor()` funktionen.

### <a name="create-a-regularized-linear-regression-model"></a>Skapa en reguljär linjär Regressions modell

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# CREATE A REGULARIZED LINEAR REGRESSION MODEL BY USING THE SPARK ML FUNCTION AND DATA FRAMES
val lr = new LinearRegression().setLabelCol("tip_amount").setFeaturesCol("features").setMaxIter(10).setRegParam(0.3).setElasticNetParam(0.8)

# FIT THE MODEL BY USING DATA FRAMES
val lrModel = lr.fit(OneHotTRAIN)
println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

# SUMMARIZE THE MODEL OVER THE TRAINING SET AND PRINT METRICS
val trainingSummary = lrModel.summary
println(s"numIterations: ${trainingSummary.totalIterations}")
println(s"objectiveHistory: ${trainingSummary.objectiveHistory.toList}")
trainingSummary.residuals.show()
println(s"RMSE: ${trainingSummary.rootMeanSquaredError}")
println(s"r2: ${trainingSummary.r2}")

# SAVE THE MODEL IN AZURE BLOB STORAGE
val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
val modelName = "LinearRegression__"
val filename = modelDir.concat(modelName).concat(datestamp)
lrModel.save(filename);

# PRINT THE COEFFICIENTS
println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

# SCORE THE MODEL ON TEST DATA
val predictions = lrModel.transform(OneHotTEST)

# EVALUATE THE MODEL ON TEST DATA
val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
val r2 = evaluator.evaluate(predictions)
println("R-sqr on test data = " + r2)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```


**Utdataparametrar**

Tid för att köra cellen: 13 sekunder.

```scala
# LOAD A SAVED LINEAR REGRESSION MODEL FROM BLOB STORAGE AND SCORE A TEST DATA SET

# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# LOAD A SAVED LINEAR REGRESSION MODEL FROM AZURE BLOB STORAGE
val savedModel = org.apache.spark.ml.regression.LinearRegressionModel.load(filename)
println(s"Coefficients: ${savedModel.coefficients} Intercept: ${savedModel.intercept}")

# SCORE THE MODEL ON TEST DATA
val predictions = savedModel.transform(OneHotTEST).select("tip_amount","prediction")
predictions.registerTempTable("testResults")

# EVALUATE THE MODEL ON TEST DATA
val evaluator = new RegressionEvaluator().setLabelCol("tip_amount").setPredictionCol("prediction").setMetricName("r2")
val r2 = evaluator.evaluate(predictions)
println("R-sqr on test data = " + r2)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

# PRINT THE RESULTS
println("R-sqr on test data = " + r2)
```

**Utdataparametrar**

R-SQR för test data = 0.5960320470835743

Fråga sedan test resultatet som en data ram och Använd AutoVizWidget och matplotlib för att visualisera det.

```sql
# RUN A SQL QUERY
%%sql -q -o sqlResults
select * from testResults

# RUN THE CODE LOCALLY ON THE JUPYTER SERVER
%%local

# USE THE JUPYTER AUTO-PLOTTING FEATURE TO CREATE INTERACTIVE FIGURES
# CLICK THE TYPE OF PLOT TO GENERATE (LINE, AREA, BAR, AND SO ON)
sqlResults
```

Koden skapar en lokal data ram från frågans utdata och ritar data. `%%local`Magic skapar en lokal data ram, `sqlResults` som du kan använda för att rita med matplotlib.

> [!NOTE]
> Detta Spark Magic används flera gånger i den här artikeln. Om mängden data är stor, bör du prova att skapa en data ram som kan passa i det lokala minnet.
> 
> 

Skapa ritytor med python-matplotlib.

```scala
# RUN THE CODE LOCALLY ON THE JUPYTER SERVER AND IMPORT LIBRARIES
%%local
sqlResults
%matplotlib inline
import numpy as np

# PLOT THE RESULTS
ax = sqlResults.plot(kind='scatter', figsize = (6,6), x='tip_amount', y='prediction', color='blue', alpha = 0.25, label='Actual vs. predicted');
fit = np.polyfit(sqlResults['tip_amount'], sqlResults['prediction'], deg=1)
ax.set_title('Actual vs. Predicted Tip Amounts ($)')
ax.set_xlabel("Actual")
ax.set_ylabel("Predicted")
#ax.plot(sqlResults['tip_amount'], fit[0] * sqlResults['prediction'] + fit[1], color='magenta')
plt.axis([-1, 15, -1, 8])
plt.show(ax)
```

**Utdataparametrar**

![Tips belopp: faktiskt vs. förutsägande](./media/scala-walkthrough/plot-actual-vs-predicted-tip-amount.png)

### <a name="create-a-gbt-regression-model"></a>Skapa en GBT regression-modell
Skapa en GBT regression-modell med Spark ML `GBTRegressor()` -funktionen och utvärdera sedan modellen på test data.

GBTS ( [gradient-Boosted trees](https://spark.apache.org/docs/latest/ml-classification-regression.html#gradient-boosted-trees-gbts) ) är ensembler i besluts träd. Besluts träd för GBTS-tågen upprepas för att minimera en förlust funktion. Du kan använda GBTS för regression och klassificering. De kan hantera kategoriska-funktioner, kräver inte funktions skalning och kan fånga icke-linjära och funktions interaktioner. Du kan också använda dem i en inställning för multiklass-klassificering.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# TRAIN A GBT REGRESSION MODEL
val gbt = new GBTRegressor().setLabelCol("label").setFeaturesCol("featuresCat").setMaxIter(10)
val gbtModel = gbt.fit(indexedTRAINwithCatFeat)

# MAKE PREDICTIONS
val predictions = gbtModel.transform(indexedTESTwithCatFeat)

# COMPUTE TEST SET R2
val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
val Test_R2 = evaluator.evaluate(predictions)


# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

# PRINT THE RESULTS
println("Test R-sqr is: " + Test_R2);
```

**Utdataparametrar**

Test R-SQR är: 0.7655383534596654

## <a name="advanced-modeling-utilities-for-optimization"></a>Avancerade modellerings verktyg för optimering
I det här avsnittet ska du använda Machine Learning-verktyg som utvecklare ofta använder för modell optimering. Mer specifikt kan du optimera Machine Learning-modeller på tre olika sätt genom att använda parameter rensning och kors validering:

* Dela data i tåg-och validerings uppsättningar, optimera modellen med hjälp av rensning av Hyper-parameter på en tränings uppsättning och utvärdera på en verifierings uppsättning (linjär regression)
* Optimera modellen med hjälp av Cross-Validation och rensning av Hyper-parameter med Spark ML CrossValidator-funktion (binära klassificering)
* Optimera modellen med hjälp av anpassad kod för kors validering och parameter rensning för att använda valfri maskin inlärnings funktion och parameter uppsättning (linjär regression)

**Kors validering** är en teknik som utvärderar hur väl en modell som är utbildad på en känd uppsättning data generaliseras för att förutsäga funktionerna i data uppsättningar som inte har tränats. Den allmänna idén bakom den här metoden är att en modell har tränats på en data uppsättning kända data och att dess noggrannhet testas mot en oberoende data uppsättning. En vanlig implementering är att dela upp en data uppsättning i *k*-vikning och sedan träna modellen i en Round-Robin på alla utom en av vikningarna.

**Optimering av Hyper-parameter** är ett problem med att välja en uppsättning Hyper-Parameters för en Learning-algoritm, vanligt vis med målet att optimera ett mått på algoritmens prestanda på en oberoende data uppsättning. En Hyper-parameter är ett värde som du måste ange utanför modell inlärnings proceduren. Antaganden om Hyper-parameter-värden kan påverka modellens flexibilitet och noggrannhet. Besluts träd har Hyper-Parameters, till exempel, till exempel det önskade djupet och antalet löv i trädet. Du måste ange en straff period för en felklassificering för en support vektor dator (SVM).

Ett vanligt sätt att utföra optimering av Hyper-parameter är att använda en rutnäts sökning, även kallat **parameter svep**. I en rutnäts sökning utförs en uttömmande sökning genom värdena för en angiven delmängd av Hyper-parameter-utrymmet för en Learning-algoritm. Kors validering kan tillhandahålla ett prestanda mått för att sortera ut optimala resultat som genereras av algoritmen för rutnäts sökning. Om du använder kors validering av en Hyper-parameter-rensning kan du begränsa problem som översätter en modell till tränings data. På så sätt behåller modellen kapaciteten att tillämpa på den allmänna data uppsättningen från vilken inlärnings data extraherades.

### <a name="optimize-a-linear-regression-model-with-hyper-parameter-sweeping"></a>Optimera en linjär Regressions modell med rensning av Hyper-parameter
Dela sedan upp data i träna och verifierings uppsättningar, Använd Hyper-parameter-rensning på en utbildning för att optimera modellen och utvärdera på en verifierings uppsättning (linjär regression).

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# RENAME `tip_amount` AS A LABEL
val OneHotTRAINLabeled = OneHotTRAIN.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
val OneHotTESTLabeled = OneHotTEST.select("tip_amount","features").withColumnRenamed(existingName="tip_amount",newName="label")
OneHotTRAINLabeled.cache()
OneHotTESTLabeled.cache()

# DEFINE THE ESTIMATOR FUNCTION: `THE LinearRegression()` FUNCTION
val lr = new LinearRegression().setLabelCol("label").setFeaturesCol("features").setMaxIter(10)

# DEFINE THE PARAMETER GRID
val paramGrid = new ParamGridBuilder().addGrid(lr.regParam, Array(0.1, 0.01, 0.001)).addGrid(lr.fitIntercept).addGrid(lr.elasticNetParam, Array(0.1, 0.5, 0.9)).build()

# DEFINE THE PIPELINE WITH A TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET), AND THEN THE SPECIFY ESTIMATOR, EVALUATOR, AND PARAMETER GRID
val trainPct = 0.75
val trainValidationSplit = new TrainValidationSplit().setEstimator(lr).setEvaluator(new RegressionEvaluator).setEstimatorParamMaps(paramGrid).setTrainRatio(trainPct)

# RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
val model = trainValidationSplit.fit(OneHotTRAINLabeled)

# MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
val testResults = model.transform(OneHotTESTLabeled).select("label", "prediction")

# COMPUTE TEST SET R2
val evaluator = new RegressionEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("r2")
val Test_R2 = evaluator.evaluate(testResults)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");

println("Test R-sqr is: " + Test_R2);
```

**Utdataparametrar**

Test R-SQR är: 0.6226484708501209

### <a name="optimize-the-binary-classification-model-by-using-cross-validation-and-hyper-parameter-sweeping"></a>Optimera den binära klassificerings modellen med hjälp av kors validering och rensning av Hyper-parameter
I det här avsnittet visas hur du optimerar en binär klassificerings modell med hjälp av kors validering och rensning av Hyper-parameter. Detta använder Spark ML- `CrossValidator` funktionen.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# CREATE DATA FRAMES WITH PROPERLY LABELED COLUMNS TO USE WITH THE TRAIN AND TEST SPLIT
val indexedTRAINwithCatFeatBinTargetRF = indexedTRAINwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
val indexedTESTwithCatFeatBinTargetRF = indexedTESTwithCatFeatBinTarget.select("labelBin","featuresCat").withColumnRenamed(existingName="labelBin",newName="label").withColumnRenamed(existingName="featuresCat",newName="features")
indexedTRAINwithCatFeatBinTargetRF.cache()
indexedTESTwithCatFeatBinTargetRF.cache()

# DEFINE THE ESTIMATOR FUNCTION
val rf = new RandomForestClassifier().setLabelCol("label").setFeaturesCol("features").setImpurity("gini").setSeed(1234).setFeatureSubsetStrategy("auto").setMaxBins(32)

# DEFINE THE PARAMETER GRID
val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(4,8)).addGrid(rf.numTrees, Array(5,10)).addGrid(rf.minInstancesPerNode, Array(100,300)).build()

# SPECIFY THE NUMBER OF FOLDS
val numFolds = 3

# DEFINE THE TRAIN/TEST VALIDATION SPLIT (75% IN THE TRAINING SET)
val CrossValidator = new CrossValidator().setEstimator(rf).setEvaluator(new BinaryClassificationEvaluator).setEstimatorParamMaps(paramGrid).setNumFolds(numFolds)

# RUN THE TRAIN VALIDATION SPLIT AND CHOOSE THE BEST SET OF PARAMETERS
val model = CrossValidator.fit(indexedTRAINwithCatFeatBinTargetRF)

# MAKE PREDICTIONS ON THE TEST DATA BY USING THE MODEL WITH THE COMBINATION OF PARAMETERS THAT PERFORMS THE BEST
val testResults = model.transform(indexedTESTwithCatFeatBinTargetRF).select("label", "prediction")

# COMPUTE THE TEST F1 SCORE
val evaluator = new MulticlassClassificationEvaluator().setLabelCol("label").setPredictionCol("prediction").setMetricName("f1")
val Test_f1Score = evaluator.evaluate(testResults)

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");
```

**Utdataparametrar**

Tid för att köra cellen: 33 sekunder.

### <a name="optimize-the-linear-regression-model-by-using-custom-cross-validation-and-parameter-sweeping-code"></a>Optimera den linjära Regressions modellen med hjälp av anpassad kod för kors validering och parameter rensning
Optimera sedan modellen med hjälp av anpassad kod och identifiera de bästa modell parametrarna med kriteriet högsta noggrannhet. Skapa sedan den slutliga modellen, utvärdera modellen på test data och Spara modellen i Blob Storage. Läs slutligen in modellen, resultat test data och utvärdera noggrannhet.

```scala
# RECORD THE START TIME
val starttime = Calendar.getInstance().getTime()

# DEFINE THE PARAMETER GRID AND THE NUMBER OF FOLDS
val paramGrid = new ParamGridBuilder().addGrid(rf.maxDepth, Array(5,10)).addGrid(rf.numTrees, Array(10,25,50)).build()

val nFolds = 3
val numModels = paramGrid.size
val numParamsinGrid = 2

# SPECIFY THE NUMBER OF CATEGORIES FOR CATEGORICAL VARIABLES
val categoricalFeaturesInfo = Map[Int, Int]((0,2),(1,2),(2,6),(3,4))

var maxDepth = -1
var numTrees = -1
var param = ""
var paramval = -1
var validateLB = -1.0
var validateUB = -1.0
val h = 1.0 / nFolds;
val RMSE  = Array.fill(numModels)(0.0)

# CREATE K-FOLDS
val splits = MLUtils.kFold(indexedTRAINbinary, numFolds = nFolds, seed=1234)


# LOOP THROUGH K-FOLDS AND THE PARAMETER GRID TO GET AND IDENTIFY THE BEST PARAMETER SET BY LEVEL OF ACCURACY
for (i <- 0 to (nFolds-1)) {
    validateLB = i * h
    validateUB = (i + 1) * h
    val validationCV = trainData.filter($"rand" >= validateLB  && $"rand" < validateUB)
    val trainCV = trainData.filter($"rand" < validateLB  || $"rand" >= validateUB)
    val validationLabPt = validationCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
    val trainCVLabPt = trainCV.rdd.map(r => LabeledPoint(r.getDouble(targetIndRegression(0).toInt), Vectors.dense(featuresIndIndex.map(r.getDouble(_)).toArray)));
    validationLabPt.cache()
    trainCVLabPt.cache()

    for (nParamSets <- 0 to (numModels-1)) {
        for (nParams <- 0 to (numParamsinGrid-1)) {
            param = paramGrid(nParamSets).toSeq(nParams).param.toString.split("__")(1)
            paramval = paramGrid(nParamSets).toSeq(nParams).value.toString.toInt
            if (param == "maxDepth") {maxDepth = paramval}
            if (param == "numTrees") {numTrees = paramval}
        }
        val rfModel = RandomForest.trainRegressor(trainCVLabPt, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                  numTrees=numTrees, maxDepth=maxDepth,
                                                  featureSubsetStrategy="auto",impurity="variance", maxBins=32)
        val labelAndPreds = validationLabPt.map { point =>
                                                 val prediction = rfModel.predict(point.features)
                                                  ( prediction, point.label )
                                                }
        val validMetrics = new RegressionMetrics(labelAndPreds)
        val rmse = validMetrics.rootMeanSquaredError
        RMSE(nParamSets) += rmse
    }
    validationLabPt.unpersist();
    trainCVLabPt.unpersist();
}
val minRMSEindex = RMSE.indexOf(RMSE.min)

# GET THE BEST PARAMETERS FROM A CROSS-VALIDATION AND PARAMETER SWEEP
var best_maxDepth = -1
var best_numTrees = -1
for (nParams <- 0 to (numParamsinGrid-1)) {
    param = paramGrid(minRMSEindex).toSeq(nParams).param.toString.split("__")(1)
    paramval = paramGrid(minRMSEindex).toSeq(nParams).value.toString.toInt
    if (param == "maxDepth") {best_maxDepth = paramval}
    if (param == "numTrees") {best_numTrees = paramval}
}

# CREATE THE BEST MODEL WITH THE BEST PARAMETERS AND A FULL TRAINING DATA SET
val best_rfModel = RandomForest.trainRegressor(indexedTRAINreg, categoricalFeaturesInfo=categoricalFeaturesInfo,
                                                  numTrees=best_numTrees, maxDepth=best_maxDepth,
                                                  featureSubsetStrategy="auto",impurity="variance", maxBins=32)

# SAVE THE BEST RANDOM FOREST MODEL IN BLOB STORAGE
val datestamp = Calendar.getInstance().getTime().toString.replaceAll(" ", ".").replaceAll(":", "_");
val modelName = "BestCV_RF_Regression__"
val filename = modelDir.concat(modelName).concat(datestamp)
best_rfModel.save(sc, filename);

# PREDICT ON THE TRAINING SET WITH THE BEST MODEL AND THEN EVALUATE
val labelAndPreds = indexedTESTreg.map { point =>
                                        val prediction = best_rfModel.predict(point.features)
                                        ( prediction, point.label )
                                       }

val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
val test_rsqr = new RegressionMetrics(labelAndPreds).r2

# GET THE TIME TO RUN THE CELL
val endtime = Calendar.getInstance().getTime()
val elapsedtime =  ((endtime.getTime() - starttime.getTime())/1000).toString;
println("Time taken to run the above cell: " + elapsedtime + " seconds.");


# LOAD THE MODEL
val savedRFModel = RandomForestModel.load(sc, filename)

val labelAndPreds = indexedTESTreg.map { point =>
                                        val prediction = savedRFModel.predict(point.features)
                                        ( prediction, point.label )
                                       }
# TEST THE MODEL
val test_rmse = new RegressionMetrics(labelAndPreds).rootMeanSquaredError
val test_rsqr = new RegressionMetrics(labelAndPreds).r2
```

**Utdataparametrar**

Tid för att köra cellen: 61 sekunder.

## <a name="consume-spark-built-machine-learning-models-automatically-with-scala"></a>Använda Spark-integrerade maskin inlärnings modeller automatiskt med Scala
En översikt över avsnitt som vägleder dig genom de uppgifter som ingår i data vetenskaps processen i Azure finns i [team data science process](./index.yml).

[Genom gång av team data science process](walkthroughs.md) beskrivs andra slut punkt till slut punkts guider som demonstrerar stegen i team data science-processen för särskilda scenarier. Genom gången illustreras också hur du kombinerar molnet och lokala verktyg och tjänster i ett arbets flöde eller en pipeline för att skapa ett intelligent program.

[Poängen Spark-inbyggda Machine Learning-modeller](spark-model-consumption.md) visar hur du använder Scala-kod för att automatiskt läsa in och Visa nya data uppsättningar med Machine Learning-modeller som byggts i Spark och sparats i Azure Blob Storage. Du kan följa anvisningarna och bara ersätta python-koden med Scala-koden i den här artikeln för automatisk konsumtion.