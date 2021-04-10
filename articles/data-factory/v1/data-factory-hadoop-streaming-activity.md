---
title: Transformera data med Hadoop streaming-aktivitet – Azure
description: Lär dig hur du kan använda Hadoop streaming-aktiviteten i en Azure Data Factory för att transformera data genom att köra Hadoop-strömmande program på begäran/ditt eget HDInsight-kluster.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 964af993fdcf17bca2caa812bf39ab63e650e807
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104782630"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Transformera data med hjälp av Hadoop streaming-aktivitet i Azure Data Factory
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
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [transformera data med hjälp av Hadoop streaming-aktivitet i Data Factory](../transform-data-using-hadoop-streaming.md).


Du kan använda HDInsightStreamingActivity-aktiviteten för att anropa ett Hadoop streaming-jobb från en Azure Data Factory pipeline. Följande JSON-kodfragment visar syntaxen för att använda HDInsightStreamingActivity i en pipeline JSON-fil. 

HDInsight streaming-aktiviteten i en Data Factory [pipeline](data-factory-create-pipelines.md) kör Hadoop streaming-program på [ditt eget](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) eller Windows/Linux-baserade HDInsight-kluster [på begäran](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . Den här artikeln bygger på artikeln [data omvandlings aktiviteter](data-factory-data-transformation-activities.md) , som visar en allmän översikt över Datatransformeringen och de omvandlings aktiviteter som stöds.

> [!NOTE] 
> Om du inte har använt Azure Data Factory läser du igenom [introduktionen till Azure Data Factory](data-factory-introduction.md) och gör självstudien: [skapa din första data pipeline innan du](data-factory-build-your-first-pipeline.md) läser den här artikeln. 

## <a name="json-sample"></a>JSON-exempel
HDInsight-klustret fylls i automatiskt med exempel program (wc.exe och cat.exe) och data (davinci.txt). Som standard är namnet på den behållare som används av HDInsight-klustret namnet på själva klustret. Om ditt kluster namn till exempel är myhdicluster, är namnet på BLOB-behållaren som är kopplad till myhdicluster. 

```JSON
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": [
                        "<nameofthecluster>/example/apps/wc.exe",
                        "<nameofthecluster>/example/apps/cat.exe"
                    ],
                    "fileLinkedService": "AzureStorageLinkedService",
                    "getDebugInfo": "Failure"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2014-01-04T00:00:00Z",
        "end": "2014-01-05T00:00:00Z"
    }
}
```

Observera följande punkter:

1. Ange **linkedServiceName** till namnet på den länkade tjänsten som pekar på ditt HDInsight-kluster där strömmande MapReduce-jobbet körs.
2. Ange typen av aktivitet till **HDInsightStreaming**.
3. För egenskapen **Mapper** anger du namnet på den körbara filen för mappning. I exemplet är cat.exe filen mapper.
4. Ange namnet på den Defiler som ska minskas för egenskapen **reduce** . I exemplet är wc.exe den körbara filen för minskning.
5. För egenskapen **Indatatyp** anger du indatafilen (inklusive platsen) för mapper. I exemplet: `wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt` : adfsample är BLOB-behållaren, exempel/data/Gutenberg är mappen och davinci.txt är blobben.
6. För egenskapen **Utdatatyp anger** du utdatafilen (inklusive platsen) för minskningen. Utdata från Hadoop streaming-jobbet skrivs till den plats som angetts för den här egenskapen.
7. I avsnittet **fil Sök** vägar anger du Sök vägarna för mapparna mapper och reducere. I exemplet: "adfsample/example/Apps/wc.exe", är adfsample BLOB-behållaren, exempel/Apps är mappen och wc.exe är den körbara filen.
8. För egenskapen **fileLinkedService** anger du Azure Storage länkade tjänsten som representerar Azure Storage som innehåller filerna som anges i avsnittet fil Sök vägar.
9. Ange argumenten för direkt uppspelnings jobbet för egenskapen **arguments** .
10. Egenskapen **getDebugInfo** är ett valfritt element. När det är inställt på att Miss lyckas, hämtas loggarna bara vid ett haveri. När det är inställt på Always, hämtas alltid loggar oavsett körnings status.

> [!NOTE]
> Som du ser i exemplet anger du en utdata-datauppsättning för Hadoop streaming-aktiviteten för egenskapen **utdata** . Den här data uppsättningen är bara en dummy-datauppsättning som krävs för att driva pipeline-schemat. Du behöver inte ange någon indata-datauppsättning för aktiviteten för egenskapen **Inputs** .  
> 
> 

## <a name="example"></a>Exempel
Pipelinen i den här genom gången kör kommandot räkna direkt uppspelning/minska program i ditt Azure HDInsight-kluster. 

### <a name="linked-services"></a>Länkade tjänster
#### <a name="azure-storage-linked-service"></a>Länkad Azure-lagringstjänst
Först skapar du en länkad tjänst för att länka Azure Storage som används av Azure HDInsight-klustret till Azure Data Factory. Om du kopierar eller klistrar in följande kod ska du inte glömma att ersätta konto namnet och konto nyckeln med namnet och nyckeln för din Azure Storage. 

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
Därefter skapar du en länkad tjänst för att länka ditt Azure HDInsight-kluster till Azure Data Factory. Om du kopierar eller klistrar in följande kod ersätter du HDInsight-klustrets namn med namnet på ditt HDInsight-kluster och ändrar värdena för användar namn och lösen ord. 

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
Pipelinen i det här exemplet tar inga indata. Du anger en utdata-datauppsättning för aktiviteten för HDInsight-direktuppspelning. Den här data uppsättningen är bara en dummy-datauppsättning som krävs för att driva pipeline-schemat. 

```JSON
{
    "name": "StreamingOutputDataset",
    "properties": {
        "published": false,
        "type": "AzureBlob",
        "linkedServiceName": "StorageLinkedService",
        "typeProperties": {
            "folderPath": "adftutorial/streamingdata/",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            },
        },
        "availability": {
            "frequency": "Day",
            "interval": 1
        }
    }
}
```

### <a name="pipeline"></a>Pipeline
Pipelinen i det här exemplet har endast en aktivitet av typen: **HDInsightStreaming**. 

HDInsight-klustret fylls i automatiskt med exempel program (wc.exe och cat.exe) och data (davinci.txt). Som standard är namnet på den behållare som används av HDInsight-klustret namnet på själva klustret. Om ditt kluster namn till exempel är myhdicluster, är namnet på BLOB-behållaren som är kopplad till myhdicluster.  

```JSON
{
    "name": "HadoopStreamingPipeline",
    "properties": {
        "description": "Hadoop Streaming Demo",
        "activities": [
            {
                "type": "HDInsightStreaming",
                "typeProperties": {
                    "mapper": "cat.exe",
                    "reducer": "wc.exe",
                    "input": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
                    "output": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
                    "filePaths": [
                        "<blobcontainer>/example/apps/wc.exe",
                        "<blobcontainer>/example/apps/cat.exe"
                    ],
                    "fileLinkedService": "StorageLinkedService"
                },
                "outputs": [
                    {
                        "name": "StreamingOutputDataset"
                    }
                ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst",
                    "retry": 1
                },
                "scheduler": {
                    "frequency": "Day",
                    "interval": 1
                },
                "name": "RunHadoopStreamingJob",
                "description": "Run a Hadoop streaming job",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-01-03T00:00:00Z",
        "end": "2017-01-04T00:00:00Z"
    }
}
```
## <a name="see-also"></a>Se även
* [Hive-aktivitet](data-factory-hive-activity.md)
* [Aktivitet i gris](data-factory-pig-activity.md)
* [MapReduce-aktivitet](data-factory-map-reduce.md)
* [Anropa Spark-program](data-factory-spark.md)
* [Anropa R-skript](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)

