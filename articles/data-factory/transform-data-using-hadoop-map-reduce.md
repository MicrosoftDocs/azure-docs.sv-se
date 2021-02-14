---
title: Transformera data med Hadoop MapReduce-aktivitet
description: Lär dig hur du bearbetar data genom att köra Hadoop MapReduce-program i ett Azure HDInsight-kluster från en Azure Data Factory.
ms.service: data-factory
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: seo-lt-2019
ms.date: 05/08/2020
ms.openlocfilehash: f03906586d6226c92cfa69e1a139d4c876cbf723
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100375892"
---
# <a name="transform-data-using-hadoop-mapreduce-activity-in-azure-data-factory"></a>Transformera data med Hadoop MapReduce-aktivitet i Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-map-reduce.md)
> * [Aktuell version](transform-data-using-hadoop-map-reduce.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

HDInsight MapReduce-aktiviteten i en Data Factory [pipeline](concepts-pipelines-activities.md) anropar MapReduce-program på [ditt eget](compute-linked-services.md#azure-hdinsight-linked-service) eller [på begäran HDInsight-](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) kluster. Den här artikeln bygger på artikeln [data omvandlings aktiviteter](transform-data.md) , som visar en allmän översikt över Datatransformeringen och de omvandlings aktiviteter som stöds.

Om du är nybörjare på Azure Data Factory läser du [introduktionen till Azure Data Factory](introduction.md) och gör självstudien: [Självstudier: transformera data](tutorial-transform-data-spark-powershell.md) innan du läser den här artikeln.

Se [gris](transform-data-using-hadoop-pig.md) och [Hive](transform-data-using-hadoop-hive.md) för mer information om att köra gris/Hive-skript på ett HDInsight-kluster från en pipeline med hjälp av HDInsight-aktiviteter av gris och Hive.

## <a name="syntax"></a>Syntax

```json
{
    "name": "Map Reduce Activity",
    "description": "Description",
    "type": "HDInsightMapReduce",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "className": "org.myorg.SampleClass",
        "jarLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "jarFilePath": "MyAzureStorage/jars/sample.jar",
        "getDebugInfo": "Failure",
        "arguments": [
            "-SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }
}
```

## <a name="syntax-details"></a>Information om syntax

| Egenskap          | Beskrivning                              | Obligatorisk |
| ----------------- | ---------------------------------------- | -------- |
| name              | Namn på aktiviteten                     | Ja      |
| beskrivning       | Text som beskriver vad aktiviteten används för | Inga       |
| typ              | Aktivitets typen är HDinsightMapReduce för MapReduce-aktivitet | Ja      |
| linkedServiceName | Referens till HDInsight-klustret som registrerats som en länkad tjänst i Data Factory. Mer information om den här länkade tjänsten finns i artikeln [Compute-länkade tjänster](compute-linked-services.md) . | Ja      |
| className         | Namnet på klassen som ska köras         | Ja      |
| jarLinkedService  | Referens till en Azure Storage länkad tjänst som används för att lagra jar-filerna. Endast **[Azure Blob Storage](./connector-azure-blob-storage.md)** -och **[ADLS Gen2](./connector-azure-data-lake-storage.md)** länkade tjänster stöds här. Om du inte anger den här länkade tjänsten används den Azure Storage länkade tjänsten som definierats i den länkade HDInsight-tjänsten. | Inga       |
| jarFilePath       | Ange sökvägen till jar-filerna som lagras i Azure Storage som refereras av jarLinkedService. Fil namnet är Skift läges känsligt. | Ja      |
| jarlibs           | Sträng mat ris för sökvägen till jar-biblioteksfilerna som refereras av jobbet som lagras i Azure Storage som definierats i jarLinkedService. Fil namnet är Skift läges känsligt. | Inga       |
| getDebugInfo      | Anger när loggfilerna kopieras till Azure Storage som används av HDInsight-kluster (eller) som anges av jarLinkedService. Tillåtna värden: ingen, Always eller Failure. Standardvärde: ingen. | Inga       |
| ogiltiga         | Anger en matris med argument för ett Hadoop-jobb. Argumenten skickas som kommando rads argument till varje aktivitet. | Inga       |
| definierar           | Ange parametrar som nyckel/värde-par för referenser i Hive-skriptet. | Inga       |



## <a name="example"></a>Exempel
Du kan använda HDInsight-MapReduce-aktiviteten för att köra en MapReduce jar-fil på ett HDInsight-kluster. I följande exempel på JSON-definition för en pipeline är HDInsight-aktiviteten konfigurerad att köra en Mahout JAR-fil.

```json
{
    "name": "MapReduce Activity for Mahout",
    "description": "Custom MapReduce to generate Mahout result",
    "type": "HDInsightMapReduce",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
        "jarLinkedService": {
            "referenceName": "MyStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
        "arguments": [
            "-s",
            "SIMILARITY_LOGLIKELIHOOD",
            "--input",
            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
            "--output",
            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
            "--maxSimilaritiesPerItem",
            "500",
            "--tempDir",
            "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
        ]
    }
}
```
Du kan ange argument för MapReduce-programmet i avsnittet **argument** . Vid körning ser du några extra argument (till exempel: MapReduce. job. Tags) från MapReduce-ramverket. Om du vill särskilja argumenten med MapReduce-argumenten kan du använda båda alternativen och värdet som argument, som du ser i följande exempel (-s,--indata,--utdata osv.), följt av värdena.

## <a name="next-steps"></a>Nästa steg
Se följande artiklar som förklarar hur du omformar data på andra sätt:

* [U-SQL-aktivitet](transform-data-using-data-lake-analytics.md)
* [Hive-aktivitet](transform-data-using-hadoop-hive.md)
* [Aktivitet i gris](transform-data-using-hadoop-pig.md)
* [Hadoop streaming-aktivitet](transform-data-using-hadoop-streaming.md)
* [Spark-aktivitet](transform-data-using-spark.md)
* [.NET-anpassad aktivitet](transform-data-using-dotnet-custom-activity.md)
* [Azure Machine Learning Studio (klassisk) batch execution Activity](transform-data-using-machine-learning.md)
* [Lagrad procedur aktivitet](transform-data-using-stored-procedure.md)