---
title: Skapa Apache Spark Machine Learning pipeline – Azure HDInsight
description: Använd Apache Spark Machine Learning-bibliotek för att skapa data pipeliner i Azure HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 07/22/2019
ms.openlocfilehash: e9897c7ba14e6190698cf10792a94acc759699d4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98940188"
---
# <a name="create-an-apache-spark-machine-learning-pipeline"></a>Skapa en Apache Spark-maskininlärningspipeline

Apache Sparks skalbara Machine Learning Library (MLlib) ger modellerings funktioner till en distribuerad miljö. Spark-paketet [`spark.ml`](https://spark.apache.org/docs/latest/ml-pipeline.html) är en uppsättning API: er på hög nivå som bygger på DataFrames. Dessa API: er hjälper dig att skapa och finjustera praktiska pipeliner för maskin inlärning.  *Spark Machine Learning*  refererar till detta MLlib DataFrame-baserade API, inte det äldre RDD-baserade pipeline-API: et.

En pipeline för Machine Learning (ML) är ett komplett arbets flöde som kombinerar flera Machine Learning-algoritmer tillsammans. Det kan finnas många steg som krävs för att bearbeta och lära sig från data, vilket kräver en sekvens med algoritmer. Pipelines definierar stadierna och ordningen för en Machine Learning-process. I MLlib representeras stadierna i en pipeline av en speciell sekvens av PipelineStages, där en transformerare och en uppskattare utför uppgifter.

En transformator är en algoritm som transformerar en DataFrame till en annan med hjälp av `transform()` metoden. Till exempel kan en funktions transformator läsa en kolumn i en DataFrame, mappa den till en annan kolumn och skapa en ny DataFrame med den mappade kolumnen som lagts till.

En uppskattning är en abstraktion av inlärnings algoritmer och ansvarar för anpassning eller utbildning på en data uppsättning för att skapa en transformator. En uppskattning implementerar en metod med namnet `fit()` , som godkänner en DataFrame och skapar en DataFrame, som är en transformator.

Varje tillstånds lös instans av en transformator eller en uppskattare har sin egen unika identifierare, som används när du anger parametrar. Båda använder ett enhetligt API för att ange dessa parametrar.

## <a name="pipeline-example"></a>Pipeline-exempel

För att demonstrera en praktisk användning av en ML-pipeline använder det här exemplet exempel `HVAC.csv` data filen som är förinstallerad på standard lagringen för ditt HDInsight-kluster, antingen Azure Storage eller data Lake Storage. Om du vill visa innehållet i filen navigerar du till `/HdiSamples/HdiSamples/SensorSampleData/hvac` katalogen. `HVAC.csv` innehåller en uppsättning gånger med både mål-och faktiska temperaturer för HVAC-system (*uppvärmning, ventilation och luft konditionering*) i olika byggnader. Målet är att träna modellen på data och skapa en prognos temperatur för en specifik byggnad.

Följande kod:

1. Definierar en `LabeledDocument` , som lagrar `BuildingID` , `SystemInfo` (ett Systems identifierare och ålder) och en `label` (1,0 om byggnaden är för het, 0,0 annars).
2. Skapar en anpassad parser `parseDocument` -funktion som tar en rad (rad) med data och avgör om byggnaden är "frekvent" genom att jämföra mål temperaturen med den faktiska temperaturen.
3. Använder parsern när data extraheras.
4. Skapar tränings data.

```python
from pyspark.ml import Pipeline
from pyspark.ml.classification import LogisticRegression
from pyspark.ml.feature import HashingTF, Tokenizer
from pyspark.sql import Row

# The data structure (column meanings) of the data array:
# 0 Date
# 1 Time
# 2 TargetTemp
# 3 ActualTemp
# 4 System
# 5 SystemAge
# 6 BuildingID

LabeledDocument = Row("BuildingID", "SystemInfo", "label")

# Define a function that parses the raw CSV file and returns an object of type LabeledDocument


def parseDocument(line):
    values = [str(x) for x in line.split(',')]
    if (values[3] > values[2]):
        hot = 1.0
    else:
        hot = 0.0

    textValue = str(values[4]) + " " + str(values[5])

    return LabeledDocument((values[6]), textValue, hot)


# Load the raw HVAC.csv file, parse it using the function
data = sc.textFile(
    "wasbs:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
training = documents.toDF()
```

Det här exemplet på pipelinen har tre steg: `Tokenizer` och `HashingTF` (båda transformatorerna) och `Logistic Regression` (en uppskattning).  Extraherade och parsade data i `training` DataFrame flödar genom pipelinen när `pipeline.fit(training)` anropas.

1. Det första steget, `Tokenizer` , delar upp `SystemInfo` inmatnings kolumnen (som består av system-ID och ålders värden) i en `words` utgående kolumn. Den här nya `words` kolumnen läggs till i DataFrame. 
2. Det andra steget, `HashingTF` konverterar den nya `words` kolumnen till funktions vektorer. Den här nya  `features` kolumnen läggs till i DataFrame. De första två stegen är transformatorer. 
3. Det tredje steget, `LogisticRegression` , är en uppskattning och så att pipelinen anropar `LogisticRegression.fit()` metoden för att skapa en `LogisticRegressionModel` . 

```python
tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
lr = LogisticRegression(maxIter=10, regParam=0.01)

# Build the pipeline with our tokenizer, hashingTF, and logistic regression stages
pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])

model = pipeline.fit(training)
```

Om du vill se de nya `words` och `features` kolumnerna som har lagts till av `Tokenizer` och `HashingTF` -transformatorerna, och ett exempel på `LogisticRegression` uppskattningen, kör du en `PipelineModel.transform()` metod på den ursprungliga DataFrame. I produktions koden är nästa steg att skicka in ett test-DataFrame för att validera utbildningen.

```python
peek = model.transform(training)
peek.show()

# Outputs the following:
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+
|BuildingID|SystemInfo|label|   words|            features|       rawPrediction|         probability|prediction|
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+
|         4|     13 20|  0.0|[13, 20]|(262144,[250802,2...|[0.11943986671420...|[0.52982451901740...|       0.0|
|        17|      3 20|  0.0| [3, 20]|(262144,[89074,25...|[0.17511205617446...|[0.54366648775222...|       0.0|
|        18|     17 20|  1.0|[17, 20]|(262144,[64358,25...|[0.14620993833623...|[0.53648750722548...|       0.0|
|        15|      2 23|  0.0| [2, 23]|(262144,[31351,21...|[-0.0361327091023...|[0.49096780538523...|       1.0|
|         3|      16 9|  1.0| [16, 9]|(262144,[153779,1...|[-0.0853679939336...|[0.47867095324139...|       1.0|
|         4|     13 28|  0.0|[13, 28]|(262144,[69821,25...|[0.14630166986618...|[0.53651031790592...|       0.0|
|         2|     12 24|  0.0|[12, 24]|(262144,[187043,2...|[-0.0509556393066...|[0.48726384581522...|       1.0|
|        16|     20 26|  1.0|[20, 26]|(262144,[128319,2...|[0.33829638728900...|[0.58377663577684...|       0.0|
|         9|      16 9|  1.0| [16, 9]|(262144,[153779,1...|[-0.0853679939336...|[0.47867095324139...|       1.0|
|        12|       6 5|  0.0|  [6, 5]|(262144,[18659,89...|[0.07513008136562...|[0.51877369045183...|       0.0|
|        15|     10 17|  1.0|[10, 17]|(262144,[64358,25...|[-0.0291988646553...|[0.49270080242078...|       1.0|
|         7|      2 11|  0.0| [2, 11]|(262144,[212053,2...|[0.03678030020834...|[0.50919403860812...|       0.0|
|        15|      14 2|  1.0| [14, 2]|(262144,[109681,2...|[0.06216423725633...|[0.51553605651806...|       0.0|
|         6|       3 2|  0.0|  [3, 2]|(262144,[89074,21...|[0.00565582077537...|[0.50141395142468...|       0.0|
|        20|     19 22|  0.0|[19, 22]|(262144,[139093,2...|[-0.0769288695989...|[0.48077726176073...|       1.0|
|         8|     19 11|  0.0|[19, 11]|(262144,[139093,2...|[0.04988910033929...|[0.51246968885151...|       0.0|
|         6|      15 7|  0.0| [15, 7]|(262144,[77099,20...|[0.14854929135994...|[0.53706918109610...|       0.0|
|        13|      12 5|  0.0| [12, 5]|(262144,[89689,25...|[-0.0519932532562...|[0.48700461408785...|       1.0|
|         4|      8 22|  0.0| [8, 22]|(262144,[98962,21...|[-0.0120753606650...|[0.49698119651572...|       1.0|
|         7|      17 5|  0.0| [17, 5]|(262144,[64358,89...|[-0.0721054054871...|[0.48198145477106...|       1.0|
+----------+----------+-----+--------+--------------------+--------------------+--------------------+----------+

only showing top 20 rows
```

`model`Objektet kan nu användas för att göra förutsägelser. Det fullständiga exemplet på det här Machine Learning-programmet och stegvisa anvisningar för hur du kör det finns i [utveckla Apache Spark Machine Learning-program i Azure HDInsight](apache-spark-ipython-notebook-machine-learning.md).

## <a name="see-also"></a>Se även

* [Data vetenskap med Scala och Apache Spark på Azure](../../machine-learning/team-data-science-process/scala-walkthrough.md)
