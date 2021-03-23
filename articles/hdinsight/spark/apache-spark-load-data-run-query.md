---
title: 'Självstudie: läsa in data & köra frågor med Apache Spark – Azure HDInsight'
description: Självstudie – lär dig hur du läser in data och kör interaktiva frågor i Spark-kluster i Azure HDInsight.
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,mvc
ms.date: 02/12/2020
ms.openlocfilehash: de17bf02392f0bb05820fabba3f9057e067391cf
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104865918"
---
# <a name="tutorial-load-data-and-run-queries-on-an-apache-spark-cluster-in-azure-hdinsight"></a>Självstudie: Läsa in data och köra frågor i ett Apache Spark-kluster i Azure HDInsight

I den här självstudien får du lära dig hur du skapar en dataframe från en CSV-fil och hur du kör interaktiva Spark SQL-frågor mot ett [Apache Spark](https://spark.apache.org/) kluster i Azure HDInsight. I Spark är en dataram en distribuerad datasamling som har ordnats i namngivna kolumner. Begreppsmässigt motsvarar dataramen en tabell i en relationsdatabas eller en dataram i R/Python.

I den här guiden får du lära dig att:
> [!div class="checklist"]
> * Skapa en dataram från en csv-fil
> * Köra frågor i dataramen

## <a name="prerequisites"></a>Förutsättningar

Ett Apache Spark-kluster i HDInsight. Se [skapa ett Apache Spark-kluster](./apache-spark-jupyter-spark-sql-use-portal.md).

## <a name="create-a-jupyter-notebook"></a>Skapa en Jupyter Notebook

Jupyter Notebook är en interaktiv anteckningsboksmiljö som stöder flera olika datorspråk. Du kan använda anteckningsboken för att interagera med dina data, kombinera kod med markdown-text och utföra enkla visualiseringar.

1. Redigera URL: en `https://SPARKCLUSTER.azurehdinsight.net/jupyter` genom `SPARKCLUSTER` att ersätta med namnet på ditt Spark-kluster. Ange sedan den redigerade URL-adressen i en webbläsare. Ange autentiseringsuppgifterna för klustret om du uppmanas att göra det.

2. På webb sidan Jupyter väljer du **ny**  >  **PySpark** för att skapa en antecknings bok.

   :::image type="content" source="./media/apache-spark-load-data-run-query/hdinsight-spark-create-jupyter-interactive-spark-sql-query.png " alt-text="Skapa en Jupyter Notebook för att köra interaktiv Spark SQL-fråga" border="true":::

   En ny antecknings bok skapas och öppnas med namnet namnlös ( `Untitled.ipynb` ).

    > [!NOTE]  
    > Genom att använda PySpark-kärnan för att skapa en anteckningsbok skapas automatiskt sessionen `spark` för dig när du kör den första kodcellen. Du behöver inte uttryckligen skapa sessionen.

## <a name="create-a-dataframe-from-a-csv-file"></a>Skapa en dataram från en csv-fil

Program kan skapa dataframes direkt från filer eller mappar på fjärrlagringen, till exempel Azure Storage eller Azure Data Lake Storage. från en Hive-tabell; eller från andra data källor som stöds av Spark, till exempel Cosmos DB, Azure SQL DB, DW och så vidare. Följande skärmbild visar en ögonblicksbild av den HVAC.csv-fil som används i självstudien. Csv-filen finns i alla HDInsight Spark-kluster. Datan visar temperaturvariationer i vissa byggnader.

:::image type="content" source="./media/apache-spark-load-data-run-query/hdinsight-spark-sample-data-interactive-spark-sql-query.png " alt-text="Ögonblicks bild av data för interaktiv Spark SQL-fråga" border="true":::

1. Klistra in följande kod i en tom cell i Jupyter Notebook och tryck sedan på **SKIFT + RETUR** för att köra koden. Koden importerar de typer som krävs för det här scenariot:

    ```python
    from pyspark.sql import *
    from pyspark.sql.types import *
    ```

    När du kör en interaktiv fråga i Jupyter visar fönstret i webbläsaren eller fliktiteln statusen **(Upptagen)** tillsammans med anteckningsbokens titel. Du ser även en fylld cirkel bredvid **PySpark**-texten i det övre högra hörnet. När jobbet har slutförts ändras detta till en tom cirkel.

    :::image type="content" source="./media/apache-spark-load-data-run-query/hdinsight-spark-interactive-spark-query-status.png " alt-text="Status för interaktiv Spark SQL-fråga" border="true":::

1. Observera att sessions-ID: t returnerades. Från bilden ovan är sessions-ID: t 0. Om du vill kan du hämta sessionsinformation genom att gå till `https://CLUSTERNAME.azurehdinsight.net/livy/sessions/ID/statements` där kluster namn är namnet på ditt Spark-kluster och ID-numret för din sessions-ID.

1. Kör följande kod för att skapa en dataram och en tillfällig tabell (**hvac**).

    ```python
    # Create a dataframe and table from sample data
    csvFile = spark.read.csv('/HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv', header=True, inferSchema=True)
    csvFile.write.saveAsTable("hvac")
    ```

## <a name="run-queries-on-the-dataframe"></a>Köra frågor i dataramen

När tabellen har skapats kan du köra en interaktiv fråga på datan.

1. Kör följande kod i en tom cell i anteckningsboken:

    ```sql
    %%sql
    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```

   Följande tabellutdata visas.

     :::image type="content" source="./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result.png " alt-text="Tabell resultatet av interaktivt Spark-frågeresultat" border="true":::

2. Du kan också visa resultaten i andra visualiseringar. Om du vill se ett ytdiagram för samma utdata väljer du **Yta** och anger sedan de andra värden som visas.

    :::image type="content" source="./media/apache-spark-load-data-run-query/hdinsight-interactive-spark-query-result-area-chart.png " alt-text="Ytdiagram i interaktivt Spark-frågeresultat" border="true":::

3. Från meny raden i anteckningsbokslayout navigerar du till **filen**  >  **Save och Checkpoint**.

4. Om du ska starta [nästa självstudie](apache-spark-use-bi-tools.md) direkt kan du lämna anteckningsboken öppen. Annars stänger du antecknings boken för att frigöra kluster resurserna: från meny raden i anteckningsbok-menyn navigerar du till **filen**  >   **Stäng och stoppa**.

## <a name="clean-up-resources"></a>Rensa resurser

Med HDInsight lagras dina data och Jupyter antecknings böcker i Azure Storage eller Azure Data Lake Storage, så att du kan ta bort ett kluster på ett säkert sätt när det inte används. Du debiteras också för ett HDInsight-kluster, även när det inte används. Eftersom avgifterna för klustret är flera gånger mer än avgifterna för lagring, är det ekonomiskt klokt att ta bort kluster när de inte används. Om du tänker arbeta med nästa självstudie direkt kan du behålla klustret.

Öppna klustret i Azure Portal och välj **Ta bort**.

:::image type="content" source="./media/apache-spark-load-data-run-query/hdinsight-azure-portal-delete-cluster.png " alt-text="Ta bort HDInsight-kluster" border="true":::

Du kan också välja resursgruppnamnet för att öppna resursgruppsidan. Välj sedan **Ta bort resursgrupp**. När resursgruppen tas bort, tas även HDInsight Spark-klustret och standardkontot för lagring bort.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du skapar en dataframe från en CSV-fil och hur du kör interaktiva Spark SQL-frågor mot ett Apache Spark kluster i Azure HDInsight. Gå vidare till nästa artikel för att se hur de data som du har registrerat i Apache Spark kan hämtas till ett BI-analysverktyg såsom Power BI.

> [!div class="nextstepaction"]
> [Analysera data med BI-verktyg](apache-spark-use-bi-tools.md)
