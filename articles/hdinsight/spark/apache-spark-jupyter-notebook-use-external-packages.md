---
title: Använda anpassade maven-paket med Jupyter i Spark – Azure HDInsight
description: Stegvisa instruktioner för hur du konfigurerar Jupyter-anteckningsböcker som är tillgängliga med HDInsight Spark-kluster för att använda anpassade maven-paket.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/22/2019
ms.openlocfilehash: a3d787059345b5971d99ebd7a1e26a9135b61bed
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98930347"
---
# <a name="use-external-packages-with-jupyter-notebooks-in-apache-spark-clusters-on-hdinsight"></a>Använda externa paket med Jupyter-anteckningsböcker i Apache Spark kluster i HDInsight

Lär dig hur du konfigurerar en [Jupyter Notebook](https://jupyter.org/) i Apache Spark-kluster på HDInsight för att använda externa community-bidrogiga Apache **maven** -paket som inte ingår i klustret.

Du kan söka i [maven-lagringsplatsen](https://search.maven.org/) efter den fullständiga listan med tillgängliga paket. Du kan också hämta en lista över tillgängliga paket från andra källor. Till exempel finns en fullständig lista över community-paket som har bidragit till i [Spark-paket](https://spark-packages.org/).

I den här artikeln får du lära dig hur du använder [Spark-CSV-](https://search.maven.org/#artifactdetails%7Ccom.databricks%7Cspark-csv_2.10%7C1.4.0%7Cjar) paketet med Jupyter Notebook.

## <a name="prerequisites"></a>Förutsättningar

* Ett Apache Spark-kluster i HDInsight. Anvisningar finns i [Skapa Apache Spark-kluster i Azure HDInsight](apache-spark-jupyter-spark-sql.md).

* Kunskaper om Jupyter Notebooks med Spark på HDInsight. Mer information finns i [läsa in data och köra frågor med Apache Spark på HDInsight](./apache-spark-load-data-run-query.md).

* [URI-schemat](../hdinsight-hadoop-linux-information.md#URI-and-scheme) för klustrets primära lagring. Detta `wasb://` gäller Azure Storage, `abfs://` för Azure Data Lake Storage Gen2 eller `adl://` för Azure Data Lake Storage gen1. Om säker överföring har Aktiver ATS för Azure Storage eller Data Lake Storage Gen2, skulle URI: n vara `wasbs://` eller `abfss://` , se även [säker överföring](../../storage/common/storage-require-secure-transfer.md).

## <a name="use-external-packages-with-jupyter-notebooks"></a>Använda externa paket med Jupyter-anteckningsböcker

1. Navigera till `https://CLUSTERNAME.azurehdinsight.net/jupyter` där `CLUSTERNAME` är namnet på ditt Spark-kluster.

1. Skapa en ny anteckningsbok. Välj **nytt** och välj sedan **Spark**.

    ![Skapa en ny Spark-Jupyter Notebook](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-create-notebook.png "Skapa en ny Jupyter Notebook")

1. En ny anteckningsbok skapas och öppnas med namnet Untitled.pynb. Välj antecknings bokens namn högst upp och ange ett eget namn.

    ![Ange ett namn för anteckningsboken](./media/apache-spark-jupyter-notebook-use-external-packages/hdinsight-spark-name-notebook.png "Ange ett namn för anteckningsboken")

1. Du använder `%%configure` Magic för att konfigurera antecknings boken för att använda ett externt paket. I antecknings böcker som använder externa paket, se till att du anropar `%%configure` Magic i den första kod cellen. Detta säkerställer att kerneln har kon figurer ATS för att använda paketet innan sessionen startar.

    >[!IMPORTANT]  
    >Om du glömmer att konfigurera kärnan i den första cellen kan du använda `%%configure` med `-f` -parametern, men den startar om sessionen och all förloppet går förlorad.

    | HDInsight-version | Kommando |
    |-------------------|---------|
    | För HDInsight 3,5 och HDInsight 3,6 | `%%configure`<br>`{ "conf": {"spark.jars.packages": "com.databricks:spark-csv_2.11:1.5.0" }}`|
    |För HDInsight 3,3 och HDInsight 3,4 | `%%configure` <br>`{ "packages":["com.databricks:spark-csv_2.10:1.4.0"] }`|

1. Kodfragmentet ovan förväntar sig maven-koordinaterna för det externa paketet i maven Central-lagringsplatsen. I det här kodfragmentet `com.databricks:spark-csv_2.11:1.5.0` är maven-koordinaten för **Spark-CSV-** paketet. Så här skapar du koordinaterna för ett paket.

    a. Leta upp paketet i maven-lagringsplatsen. I den här artikeln använder vi [Spark-CSV](https://mvnrepository.com/artifact/com.databricks/spark-csv).

    b. Samla in **värdena för** **ArtifactId** och **version** från databasen. Kontrol lera att värdena som du samlar in matchar klustret. I det här fallet använder vi ett Scala 2,11-och Spark 1.5.0-paket, men du kan behöva välja olika versioner för lämplig Scala-eller Spark-version i klustret. Du kan ta reda på Scala-versionen på klustret genom `scala.util.Properties.versionString` att köra i Spark Jupyter-kärnan eller vid Spark-sändning. Du kan ta reda på Spark-versionen i klustret genom att köra `sc.version` på Jupyter Notebooks.

    ![Använda externa paket med Jupyter Notebook](./media/apache-spark-jupyter-notebook-use-external-packages/use-external-packages-with-jupyter.png "Använda externa paket med Jupyter Notebook")

    c. Sammanfoga de tre värdena, avgränsade med kolon (**:**).

    ```scala
    com.databricks:spark-csv_2.11:1.5.0
    ```

1. Kör Code-cellen med `%%configure` Magic. Detta konfigurerar den underliggande livy-sessionen att använda det paket som du har angett. I de efterföljande cellerna i antecknings boken kan du nu använda paketet, som du ser nedan.

    ```scala
    val df = spark.read.format("com.databricks.spark.csv").
    option("header", "true").
    option("inferSchema", "true").
    load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    ```

    För HDInsight 3,4 och tidigare bör du använda följande kodfragment.

    ```scala
    val df = sqlContext.read.format("com.databricks.spark.csv").
    option("header", "true").
    option("inferSchema", "true").
    load("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
    ```

1. Sedan kan du köra kodfragmenten som visas nedan för att visa data från dataframe som du skapade i föregående steg.

    ```scala
    df.show()
   
    df.select("Time").count()
    ```

## <a name="see-also"></a><a name="seealso"></a>Se även

* [Översikt: Apache Spark i Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenarier

* [Apache Spark med BI: utföra interaktiv data analys med hjälp av spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Apache Spark med Machine Learning: använda spark i HDInsight för analys av byggnads temperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark med Machine Learning: använda spark i HDInsight för att förutsäga resultatet av livsmedels inspektionen](apache-spark-machine-learning-mllib-ipython.md)
* [Webbplats logg analys med Apache Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Skapa och köra program

* [Skapa ett fristående program med hjälp av Scala](apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Apache Spark-kluster med hjälp av Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Verktyg och tillägg

* [Använda externa python-paket med Jupyter-anteckningsböcker i Apache Spark kluster i HDInsight Linux](apache-spark-python-package-installation.md)
* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att skapa och skicka Spark Scala-appar](apache-spark-intellij-tool-plugin.md)
* [Använd HDInsight tools-plugin för IntelliJ-idé för att felsöka Apache Spark program via fjärr anslutning](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använda Apache Zeppelin-anteckningsböcker med ett Apache Spark-kluster i HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels som är tillgängliga för Jupyter Notebook i Apache Spark-kluster för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Hantera resurser

* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)
