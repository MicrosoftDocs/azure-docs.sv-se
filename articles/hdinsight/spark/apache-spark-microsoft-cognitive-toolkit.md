---
title: Microsoft Cognitive Toolkit med Apache Spark – Azure HDInsight
description: Lär dig hur en utbildad Microsoft Cognitive Toolkit djup inlärnings modell kan användas för en data uppsättning med Spark python API i ett Azure HDInsight Spark-kluster.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 01/14/2020
ms.openlocfilehash: cddbc4b6a5c7a2c787c8305fdf703e34543746f8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98929977"
---
# <a name="use-microsoft-cognitive-toolkit-deep-learning-model-with-azure-hdinsight-spark-cluster"></a>Använd Microsoft Cognitive Toolkit djup inlärnings modell med Azure HDInsight Spark kluster

I den här artikeln utför du följande steg.

1. Kör ett anpassat skript för att installera [Microsoft Cognitive Toolkit](/cognitive-toolkit/) på ett Azure HDInsight Spark-kluster.

2. Ladda upp en [Jupyter Notebook](https://jupyter.org/) till [Apache Spark](https://spark.apache.org/) -klustret för att se hur du använder en utbildad Microsoft Cognitive Toolkit djup inlärnings modell för filer i ett Azure Blob Storage [-konto med Spark python API (PySpark)](https://spark.apache.org/docs/latest/api/python/index.html)

## <a name="prerequisites"></a>Förutsättningar

* Ett Apache Spark-kluster i HDInsight. Se [skapa ett Apache Spark-kluster](./apache-spark-jupyter-spark-sql-use-portal.md).

* Kunskaper om Jupyter Notebooks med Spark på HDInsight. Mer information finns i [läsa in data och köra frågor med Apache Spark på HDInsight](./apache-spark-load-data-run-query.md).

## <a name="how-does-this-solution-flow"></a>Hur flödar den här lösningen?

Den här lösningen är uppdelad mellan den här artikeln och en Jupyter Notebook som du laddar upp som en del av den här artikeln. I den här artikeln slutför du följande steg:

* Kör en skript åtgärd på ett HDInsight Spark-kluster för att installera Microsoft Cognitive Toolkit-och python-paket.
* Överför Jupyter Notebook som kör lösningen till HDInsight Spark-klustret.

Följande återstående steg beskrivs i Jupyter Notebook.

* Läs in exempel bilder i en spark elastisk distribuerad data uppsättning eller RDD.
  * Läs in moduler och definiera för inställningar.
  * Hämta data uppsättningen lokalt i Spark-klustret.
  * Konvertera data uppsättningen till en RDD.
* Räkna bilder med hjälp av en utbildad Cognitive Toolkit modell.
  * Ladda ned den tränade Cognitive Toolkits modellen till Spark-klustret.
  * Definiera funktioner som ska användas av arbetsnoder.
  * Räkna bilder på arbetsnoder.
  * Utvärdera modell noggrannhet.

## <a name="install-microsoft-cognitive-toolkit"></a>Installera Microsoft Cognitive Toolkit

Du kan installera Microsoft Cognitive Toolkit på ett Spark-kluster med skript åtgärd. Skript åtgärden använder anpassade skript för att installera komponenter i klustret som inte är tillgängliga som standard. Du kan använda det anpassade skriptet från Azure Portal, genom att använda HDInsight .NET SDK eller med Azure PowerShell. Du kan också använda skriptet för att installera verktyget, antingen som en del av klustret, eller när klustret är igång.

I den här artikeln använder vi portalen för att installera verktyget när klustret har skapats. Andra sätt att köra det anpassade skriptet finns i [Anpassa HDInsight-kluster med skript åtgärd](../hdinsight-hadoop-customize-cluster-linux.md).

### <a name="using-the-azure-portal"></a>Använda Azure Portal

Instruktioner för hur du använder Azure Portal för att köra skript åtgärder finns i [Anpassa HDInsight-kluster med skript åtgärd](../hdinsight-hadoop-customize-cluster-linux.md#script-action-during-cluster-creation). Se till att du anger följande indata för att installera Microsoft Cognitive Toolkit. Använd följande värden för din skript åtgärd:

|Egenskap |Värde |
|---|---|
|Skript typ|– Anpassad|
|Name| Installera MCT|
|Bash-skript-URI|`https://raw.githubusercontent.com/Azure-Samples/hdinsight-pyspark-cntk-integration/master/cntk-install.sh`|
|Node-typ (er):|Head, Worker|
|Parametrar|Ingen|

## <a name="upload-the-jupyter-notebook-to-azure-hdinsight-spark-cluster"></a>Ladda upp Jupyter Notebook till Azure HDInsight Spark-kluster

Om du vill använda Microsoft Cognitive Toolkit med Azure HDInsight Spark-klustret måste du läsa in Jupyter Notebook **CNTK_model_scoring_on_Spark_walkthrough. ipynb** till Azure HDInsight Spark-klustret. Den här antecknings boken finns på GitHub på [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration) .

1. Ladda ned och zippa upp [https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration](https://github.com/Azure-Samples/hdinsight-pyspark-cntk-integration) .

1. I en webbläsare går du till `https://CLUSTERNAME.azurehdinsight.net/jupyter` , där `CLUSTERNAME` är namnet på klustret.

1. Från Jupyter Notebook väljer du **Ladda upp** i det övre högra hörnet och navigera sedan till Ladda ned och välj fil `CNTK_model_scoring_on_Spark_walkthrough.ipynb` .

    ![Ladda upp Jupyter Notebook till Azure HDInsight Spark-kluster](./media/apache-spark-microsoft-cognitive-toolkit/hdinsight-microsoft-cognitive-toolkit-load-jupyter-notebook.png "Ladda upp Jupyter Notebook till Azure HDInsight Spark-kluster")

1. Välj **Ladda upp** igen.

1. När antecknings boken har laddats upp klickar du på namnet på antecknings boken och följer sedan anvisningarna i själva antecknings boken för att läsa in data uppsättningen och utföra artikeln.

## <a name="see-also"></a>Se även

* [Översikt: Apache Spark i Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scenarier

* [Apache Spark med BI: utföra interaktiv data analys med hjälp av spark i HDInsight med BI-verktyg](apache-spark-use-bi-tools.md)
* [Apache Spark med Machine Learning: använda spark i HDInsight för analys av byggnads temperatur med HVAC-data](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark med Machine Learning: använda spark i HDInsight för att förutsäga resultatet av livsmedels inspektionen](apache-spark-machine-learning-mllib-ipython.md)
* [Webbplats logg analys med Apache Spark i HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Program insiktering telemetri data analys med Apache Spark i HDInsight](apache-spark-analyze-application-insight-logs.md)

### <a name="create-and-run-applications"></a>Skapa och köra program

* [Skapa ett fristående program med hjälp av Scala](apache-spark-create-standalone-application.md)
* [Köra jobb via fjärranslutning på ett Apache Spark-kluster med hjälp av Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Verktyg och tillägg

* [Använda HDInsight Tools-plugin för IntelliJ IDEA till att skapa och skicka Spark Scala-appar](apache-spark-intellij-tool-plugin.md)
* [Använd HDInsight tools-plugin för IntelliJ-idé för att felsöka Apache Spark program via fjärr anslutning](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Använda Apache Zeppelin-anteckningsböcker med ett Apache Spark-kluster i HDInsight](apache-spark-zeppelin-notebook.md)
* [Kernels som är tillgängliga för Jupyter Notebook i Apache Spark-kluster för HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Använda externa paket med Jupyter-anteckningsböcker](apache-spark-jupyter-notebook-use-external-packages.md)
* [Installera Jupyter på datorn och ansluta till ett HDInsight Spark-kluster](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Hantera resurser

* [Hantera resurser för Apache Spark-klustret i Azure HDInsight](apache-spark-resource-manager.md)
* [Följa och felsöka jobb som körs i ett Apache Spark-kluster i HDInsight](apache-spark-job-debugging.md)
