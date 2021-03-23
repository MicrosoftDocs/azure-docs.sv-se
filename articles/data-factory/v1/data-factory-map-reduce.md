---
title: Anropa MapReduce-program från Azure Data Factory
description: Lär dig hur du bearbetar data genom att köra MapReduce-program på ett Azure HDInsight-kluster från en Azure Data Factory.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: f1f54e972d59d3de3b0f93b3150ee1150eb6f612
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2021
ms.locfileid: "104786404"
---
# <a name="invoke-mapreduce-programs-from-data-factory"></a>Anropa MapReduce-program från Data Factory
> [!div class="op_single_selector" title1="Omvandlings aktiviteter"]
> * [Hive-aktivitet](data-factory-hive-activity.md) 
> * [Aktivitet i gris](data-factory-pig-activity.md)
> * [MapReduce-aktivitet](data-factory-map-reduce.md)
> * [Hadoop streaming-aktivitet](data-factory-hadoop-streaming-activity.md)
> * [Spark-aktivitet](data-factory-spark.md)
> * [Batch-körningsaktivitet i Azure Machine Learning Studio (klassisk)](data-factory-azure-ml-batch-execution-activity.md)
> * [Uppdateringsresursaktivitet i Azure Machine Learning Studio (klassisk)](data-factory-azure-ml-update-resource-activity.md)
> * [Lagrad proceduraktivitet](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-aktivitet](data-factory-usql-activity.md)
> * [Anpassad .NET-aktivitet](data-factory-use-custom-activities.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [transformera data med MapReduce-aktivitet i Data Factory](../transform-data-using-hadoop-map-reduce.md).


HDInsight MapReduce-aktiviteten i en Data Factory [pipeline](data-factory-create-pipelines.md) kör MapReduce-program på [ditt eget](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) eller Windows/Linux-baserade HDInsight-kluster [på begäran](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . Den här artikeln bygger på artikeln [data omvandlings aktiviteter](data-factory-data-transformation-activities.md) , som visar en allmän översikt över Datatransformeringen och de omvandlings aktiviteter som stöds.

> [!NOTE] 
> Om du inte har använt Azure Data Factory läser du igenom [introduktionen till Azure Data Factory](data-factory-introduction.md) och gör självstudien: [skapa din första data pipeline innan du](data-factory-build-your-first-pipeline.md) läser den här artikeln.  

## <a name="introduction"></a>Introduktion
En pipeline i en Azure Data Factory bearbetar data i länkade lagrings tjänster med hjälp av länkade Compute-tjänster. Den innehåller en sekvens med aktiviteter där varje aktivitet utför en speciell bearbetnings åtgärd. I den här artikeln beskrivs hur du använder HDInsight MapReduce-aktiviteten.

Se [gris](data-factory-pig-activity.md) och [Hive](data-factory-hive-activity.md) om du vill ha mer information om att köra gris/Hive-skript på ett Windows/Linux-baserat HDInsight-kluster från en pipeline med hjälp av HDInsight-gris och Hive-aktiviteter. 

## <a name="json-for-hdinsight-mapreduce-activity"></a>JSON för HDInsight MapReduce-aktivitet
I JSON-definitionen för HDInsight-aktiviteten: 

1. Ange **typen** av **aktivitet** till **HDInsight**.
2. Ange namnet på klassen för egenskapen **className** .
3. Ange sökvägen till JAR-filen inklusive fil namnet för egenskapen **jarFilePath** .
4. Ange den länkade tjänst som refererar till den Azure-Blob Storage som innehåller JAR-filen för egenskapen **jarLinkedService** .   
5. Ange argument för MapReduce-programmet i avsnittet **argument** . Vid körning ser du några extra argument (till exempel: MapReduce. job. Tags) från MapReduce-ramverket. Om du vill särskilja argumenten med MapReduce-argumenten kan du använda båda alternativen och värdet som argument, som du ser i följande exempel (-s,--output,--output osv., är alternativ omedelbart följt av deras värden).

    ```JSON   
    {
        "name": "MahoutMapReduceSamplePipeline",
        "properties": {
            "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
            "activities": [
                {
                    "type": "HDInsightMapReduce",
                    "typeProperties": {
                        "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
                        "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
                        "jarLinkedService": "StorageLinkedService",
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
                    },
                    "inputs": [
                        {
                            "name": "MahoutInput"
                        }
                    ],
                    "outputs": [
                        {
                            "name": "MahoutOutput"
                        }
                    ],
                    "policy": {
                        "timeout": "01:00:00",
                        "concurrency": 1,
                        "retry": 3
                    },
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "MahoutActivity",
                    "description": "Custom Map Reduce to generate Mahout result",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2017-01-03T00:00:00Z",
            "end": "2017-01-04T00:00:00Z"
        }
    }
    ```
   Du kan använda HDInsight-MapReduce-aktiviteten för att köra en MapReduce jar-fil på ett HDInsight-kluster. I följande exempel på JSON-definition för en pipeline är HDInsight-aktiviteten konfigurerad att köra en Mahout JAR-fil.

## <a name="sample-on-github"></a>Exempel på GitHub
Du kan hämta ett exempel för att använda HDInsight-MapReduce-aktiviteten från: [Data Factory exempel på GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/JSON/MapReduce_Activity_Sample).  

## <a name="running-the-word-count-program"></a>Köra Word Count-programmet
Pipelinen i det här exemplet kör kommandot räkna ord för att mappa/minska programmet på ditt Azure HDInsight-kluster.   

### <a name="linked-services"></a>Länkade tjänster
Först skapar du en länkad tjänst för att länka Azure Storage som används av Azure HDInsight-klustret till Azure Data Factory. Om du kopierar eller klistrar in följande kod ska du inte glömma att ersätta **konto namnet** och **konto nyckeln** med namnet och nyckeln för din Azure Storage. 

#### <a name="azure-storage-linked-service"></a>Länkad Azure-lagringstjänst

```JSON
{
    "name": "StorageLinkedService",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
        }
    }
}
```

#### <a name="azure-hdinsight-linked-service"></a>Länkad Azure HDInsight-tjänst
Därefter skapar du en länkad tjänst för att länka ditt Azure HDInsight-kluster till Azure Data Factory. Om du kopierar eller klistrar in följande kod ersätter du **HDInsight-klustrets namn** med namnet på ditt HDInsight-kluster och ändrar värdena för användar namn och lösen ord.   

```JSON
{
    "name": "HDInsightLinkedService",
    "properties": {
        "type": "HDInsight",
        "typeProperties": {
            "clusterUri": "https://<HDInsight cluster name>.azurehdinsight.net",
            "userName": "admin",
            "password": "**********",
            "linkedServiceName": "StorageLinkedService"
        }
    }
}
```

### <a name="datasets"></a>Datauppsättningar
#### <a name="output-dataset"></a>Data uppsättning för utdata
Pipelinen i det här exemplet tar inga indata. Du anger en utdata-datauppsättning för HDInsight-MapReduce-aktiviteten. Den här data uppsättningen är bara en dummy-datauppsättning som krävs för att driva pipeline-schemat.  

```JSON
{
    "name": "MROutput",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "fileName": "WordCountOutput1.txt",
            "folderPath": "example/data/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="pipeline"></a>Pipeline
Pipelinen i det här exemplet har endast en aktivitet av typen: HDInsightMapReduce. Några av de viktiga egenskaperna i JSON är: 

| Egenskap | Kommentarer |
|:--- |:--- |
| typ |Typen måste anges till **HDInsightMapReduce**. |
| className |Namnet på klassen är: **WORDCOUNT** |
| jarFilePath |Sökväg till jar-filen som innehåller klassen. Om du kopierar eller klistrar in följande kod ska du inte glömma att ändra namnet på klustret. |
| jarLinkedService |Azure Storage länkad tjänst som innehåller jar-filen. Den här länkade tjänsten refererar till det lagrings utrymme som är associerat med HDInsight-klustret. |
| ogiltiga |WORDCOUNT-programmet tar två argument, in-och utdata. Indatafilen är den davinci.txt filen. |
| frekvens/intervall |Värdena för dessa egenskaper matchar utdata-datauppsättningen. |
| linkedServiceName |refererar till den länkade HDInsight-tjänsten som du skapade tidigare. |

```JSON
{
    "name": "MRSamplePipeline",
    "properties": {
        "description": "Sample Pipeline to Run the Word Count Program",
        "activities": [
            {
                "type": "HDInsightMapReduce",
                "typeProperties": {
                    "className": "wordcount",
                    "jarFilePath": "<HDInsight cluster name>/example/jars/hadoop-examples.jar",
                    "jarLinkedService": "StorageLinkedService",
                    "arguments": [
                        "/example/data/gutenberg/davinci.txt",
                        "/example/data/WordCountOutput1"
                    ]
                },
                "outputs": [
                    {
                        "name": "MROutput"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "MRActivity",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-03T00:00:00Z",
        "end": "2014-01-04T00:00:00Z"
    }
}
```

## <a name="run-spark-programs"></a>Köra Spark-program
Du kan använda MapReduce-aktiviteten för att köra Spark-program i ditt HDInsight Spark-kluster. Mer information finns i [Invoke Spark programs from Azure Data Factory](data-factory-spark.md) (Anropa Spark-program från Azure Data Factory).  

[developer-reference]: /previous-versions/azure/dn834987(v=azure.100)
[cmdlet-reference]: https://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-copy-data-from-azure-blob-storage-to-sql-database.md
[adfgetstartedmonitoring]:data-factory-copy-data-from-azure-blob-storage-to-sql-database.md#monitor-pipelines 

[Developer Reference]: /previous-versions/azure/dn834987(v=azure.100)
[Azure Portal]: https://portal.azure.com

## <a name="see-also"></a>Se även
* [Hive-aktivitet](data-factory-hive-activity.md)
* [Aktivitet i gris](data-factory-pig-activity.md)
* [Hadoop streaming-aktivitet](data-factory-hadoop-streaming-activity.md)
* [Anropa Spark-program](data-factory-spark.md)
* [Anropa R-skript](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)