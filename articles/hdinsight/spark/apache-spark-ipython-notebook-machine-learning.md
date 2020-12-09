---
title: 'Självstudie: bygga Spark Machine Learning-app – Azure HDInsight'
description: Själv studie kurs – stegvisa anvisningar om hur du skapar Apache Spark Machine Learning-program i HDInsight Spark-kluster med hjälp av Jupyter Notebook.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc, devx-track-python
ms.date: 04/07/2020
ms.openlocfilehash: 6af50bae075e88ac3bbba39cba9c9042eee2000e
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2020
ms.locfileid: "96932253"
---
# <a name="tutorial-build-an-apache-spark-machine-learning-application-in-azure-hdinsight"></a>Självstudie: utveckla ett Apache Spark Machine Learning-program i Azure HDInsight

I den här självstudien lär du dig att använda [Jupyter Notebook](https://jupyter.org/) för att skapa ett [Apache Spark](./apache-spark-overview.md)-maskininlärningsprogram för Azure HDInsight.

[MLlib](https://spark.apache.org/docs/latest/ml-guide.html) är Spark: s anpassningsbara maskin inlärnings bibliotek som består av vanliga inlärnings-algoritmer och-verktyg. (Klassificering, regression, klustring, gemensam filtrering och dimensionalitet. Även underliggande optimerings primitiver.)

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Utveckla ett Apache Spark-maskininlärningsprogram

## <a name="prerequisites"></a>Krav

* Ett Apache Spark-kluster i HDInsight. Se [skapa ett Apache Spark-kluster](./apache-spark-jupyter-spark-sql-use-portal.md).

* Kunskaper om Jupyter Notebooks med Spark på HDInsight. Mer information finns i [läsa in data och köra frågor med Apache Spark på HDInsight](./apache-spark-load-data-run-query.md).

## <a name="understand-the-data-set"></a>Förstå datauppsättningen

Programmet använder exempel **HVAC.csv** data som är tillgängliga i alla kluster som standard. Filen finns på `\HdiSamples\HdiSamples\SensorSampleData\hvac` . Data visar måltemperaturen och den faktiska temperaturen för några byggnader som har installerade HVAC-system. Kolumnen **System** representerar system-ID:t, och kolumnen **SystemAge** representerar antalet år som HVAC-systemet har funnits i byggnaden. Du kan förutsäga om en byggnad ska vara Hotter eller kallare baserat på mål temperatur, angivet system-ID och systemets ålder.

![Ögonblicks bild av data som används för Spark Machine Learning-exempel](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-understand-data.png "Ögonblicks bild av data som används för Spark Machine Learning-exempel")

## <a name="develop-a-spark-machine-learning-application-using-spark-mllib"></a>Utveckla ett Spark-maskininlärningsprogram med Spark MLlib

Det här programmet använder en spark [ml-pipeline](https://spark.apache.org/docs/2.2.0/ml-pipeline.html) för att göra en dokument klassificering. ML pipelines tillhandahåller en enhetlig uppsättning högnivå-API: er som bygger på DataFrames. DataFrames hjälper användarna att skapa och finjustera praktiska pipeliner för Machine Learning. I pipelinen delar du upp dokumentet i ord, konverterar orden till en numerisk funktionsvektor och slutligen skapa en förutsägelsemodell med funktionsvektorer och etiketter. Utför följande steg för att skapa programmet.

1. Skapa en Jupyter Notebook med PySpark-kärnan. Instruktioner finns i [skapa en Jupyter Notebook-fil](./apache-spark-jupyter-spark-sql.md#create-a-jupyter-notebook-file).

1. Importera de typer som krävs för det här scenariot. Klistra in följande kodfragment i en tom cell och tryck sedan på **SKIFT+RETUR**.

    ```PySpark
    from pyspark.ml import Pipeline
    from pyspark.ml.classification import LogisticRegression
    from pyspark.ml.feature import HashingTF, Tokenizer
    from pyspark.sql import Row

    import os
    import sys
    from pyspark.sql.types import *

    from pyspark.mllib.classification import LogisticRegressionWithSGD
    from pyspark.mllib.regression import LabeledPoint
    from numpy import array
    ```

1. Läs in data (hvac.csv), parsa dem och använd dem för att träna modellen.

    ```PySpark
    # Define a type called LabelDocument
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
    data = sc.textFile("/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")

    documents = data.filter(lambda s: "Date" not in s).map(parseDocument)
    training = documents.toDF()
    ```

    I kodfragmentet definierar du en funktion som jämför den faktiska temperaturen med måltemperaturen. Om den faktiska temperaturen är högre är byggnaden varm, vilket markeras med värdet **1.0**. Annars är byggnaden kall, vilket visas av värdet **0.0**.

1. Konfigurera Spark machine learning-pipelinen som består av tre steg: tokenizer, hashingTF och lr.

    ```PySpark
    tokenizer = Tokenizer(inputCol="SystemInfo", outputCol="words")
    hashingTF = HashingTF(inputCol=tokenizer.getOutputCol(), outputCol="features")
    lr = LogisticRegression(maxIter=10, regParam=0.01)
    pipeline = Pipeline(stages=[tokenizer, hashingTF, lr])
    ```

    Mer information om pipeline och hur det fungerar finns i avsnittet om [Apache Spark-maskininlärnings-pipeline](https://spark.apache.org/docs/latest/ml-pipeline.html).

1. Anpassa pipelinen till utbildningsdokumentet.

    ```PySpark
    model = pipeline.fit(training)
    ```

1. Verifiera utbildningsdokumentet för att kontrollera din utveckling med programmet.

    ```PySpark
    training.show()
    ```

    Utdatan liknar följande:

    ```output
    +----------+----------+-----+
    |BuildingID|SystemInfo|label|
    +----------+----------+-----+
    |         4|     13 20|  0.0|
    |        17|      3 20|  0.0|
    |        18|     17 20|  1.0|
    |        15|      2 23|  0.0|
    |         3|      16 9|  1.0|
    |         4|     13 28|  0.0|
    |         2|     12 24|  0.0|
    |        16|     20 26|  1.0|
    |         9|      16 9|  1.0|
    |        12|       6 5|  0.0|
    |        15|     10 17|  1.0|
    |         7|      2 11|  0.0|
    |        15|      14 2|  1.0|
    |         6|       3 2|  0.0|
    |        20|     19 22|  0.0|
    |         8|     19 11|  0.0|
    |         6|      15 7|  0.0|
    |        13|      12 5|  0.0|
    |         4|      8 22|  0.0|
    |         7|      17 5|  0.0|
    +----------+----------+-----+
    ```

    Jämföra utdata med CSV-råfilen. Den första raden i CSV-filen har dessa data till exempel:

    ![Ögonblicks bild av utdata för Spark Machine Learning-exempel](./media/apache-spark-ipython-notebook-machine-learning/spark-machine-learning-output-data.png "Ögonblicks bild av utdata för Spark Machine Learning-exempel")

    Lägg märke till att den faktiska temperaturen är lägre än måltemperaturen, vilket indikerar att byggnaden är kall. Värdet för **Label** på den första raden är **0,0**, vilket innebär att byggnaden inte är aktiv.

1. Förbered en datauppsättning att köra träningsmodellen mot. Det gör du genom att skicka ett system-ID och en system ålder (anges som **SYSTEMINFO** i övnings resultatet). Modellen förutsäger om byggnaden med detta system-ID och systemets ålder kommer att vara Hotter (betecknad med 1,0) eller kylare (betecknas med 0,0).

    ```PySpark
    # SystemInfo here is a combination of system ID followed by system age
    Document = Row("id", "SystemInfo")
    test = sc.parallelize([(1L, "20 25"),
                    (2L, "4 15"),
                    (3L, "16 9"),
                    (4L, "9 22"),
                    (5L, "17 10"),
                    (6L, "7 22")]) \
        .map(lambda x: Document(*x)).toDF()
    ```

1. Gör slutligen förutsägelser på testdata.

    ```PySpark
    # Make predictions on test documents and print columns of interest
    prediction = model.transform(test)
    selected = prediction.select("SystemInfo", "prediction", "probability")
    for row in selected.collect():
        print row
    ```

    Utdatan liknar följande:

    ```output  
    Row(SystemInfo=u'20 25', prediction=1.0, probability=DenseVector([0.4999, 0.5001]))
    Row(SystemInfo=u'4 15', prediction=0.0, probability=DenseVector([0.5016, 0.4984]))
    Row(SystemInfo=u'16 9', prediction=1.0, probability=DenseVector([0.4785, 0.5215]))
    Row(SystemInfo=u'9 22', prediction=1.0, probability=DenseVector([0.4549, 0.5451]))
    Row(SystemInfo=u'17 10', prediction=1.0, probability=DenseVector([0.4925, 0.5075]))
    Row(SystemInfo=u'7 22', prediction=0.0, probability=DenseVector([0.5015, 0.4985]))
    ```

   Observera den första raden i förutsägelsen. För ett HVAC-system med ID 20 och systemets ålder 25 år är byggnaden hett (**förutsägelse = 1.0**). Det första värdet för DenseVector (0,49999) motsvarar förutsägelsen 0.0 och det andra värdet (0,5001) motsvarar förutsägelsen 1.0. I utdata, trots att det andra värdet bara är marginellt högre, visar modellen **förutsägelse=1.0**.

1. Stäng anteckningsboken för att frigöra resurser. Du gör det genom att välja **Stäng och stoppa** i anteckningsbokens **Fil**-meny. Åtgärden stänger anteckningsboken.

## <a name="use-anaconda-scikit-learn-library-for-spark-machine-learning"></a>Använda Anaconda scikit-bibliotek för Spark machine learning

Apache Spark-kluster i HDInsight innehåller Anaconda-bibliotek. Det innehåller också **scikit-learn**-bibliotek för machine learning. Biblioteket innehåller också olika data uppsättningar som du kan använda för att bygga exempel program direkt från en Jupyter Notebook. Exempel på hur du använder scikit – lära-biblioteket finns i [https://scikit-learn.org/stable/auto_examples/index.html](https://scikit-learn.org/stable/auto_examples/index.html) .

## <a name="clean-up-resources"></a>Rensa resurser

Om du inte kommer att fortsätta att använda det här programmet, tar du bort det kluster som du skapade med följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. I rutan **Sök** längst upp skriver du **HDInsight**.

1. Välj **HDInsight-kluster** under **Tjänster**.

1. I listan med HDInsight-kluster som visas väljer du **...** bredvid det kluster som du skapade för den här självstudien.

1. Välj **Ta bort**. Välj **Ja**.

![Azure Portal tar bort ett HDInsight-kluster](./media/apache-spark-ipython-notebook-machine-learning/hdinsight-azure-portal-delete-cluster.png "Ta bort HDInsight-kluster")

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du använder Jupyter Notebook för att bygga ett Apache Spark Machine Learning-program för Azure HDInsight. Gå vidare till nästa självstudie om du vill lära dig hur du använder IntelliJ IDEA för Spark-jobb.

> [!div class="nextstepaction"]
> [Skapa ett Scala maven-program med hjälp av IntelliJ](./apache-spark-create-standalone-application.md)
