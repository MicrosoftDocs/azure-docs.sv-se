---
title: Transformera data med Hive-aktivitet – Azure
description: Lär dig hur du kan använda Hive-aktiviteten i Azure Data Factory v1 för att köra Hive-frågor på ett eget HDInsight-kluster på begäran.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 9d14ddb172546e062b62a5a8dd98b49a0a6e1c6f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383032"
---
# <a name="transform-data-using-hive-activity-in-azure-data-factory"></a>Transformera data med Hive-aktivitet i Azure Data Factory 
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
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [transformera data med Hive-aktivitet i Data Factory](../transform-data-using-hadoop-hive.md).

HDInsight Hive-aktiviteten i en Data Factory [pipelinen](data-factory-create-pipelines.md) kör Hive-frågor på [ditt eget](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) eller Windows/Linux-baserade HDInsight-kluster [på begäran](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . Den här artikeln bygger på artikeln [data omvandlings aktiviteter](data-factory-data-transformation-activities.md) , som visar en allmän översikt över Datatransformeringen och de omvandlings aktiviteter som stöds.

> [!NOTE] 
> Om du inte har använt Azure Data Factory läser du igenom [introduktionen till Azure Data Factory](data-factory-introduction.md) och gör självstudien: [skapa din första data pipeline innan du](data-factory-build-your-first-pipeline.md) läser den här artikeln. 

## <a name="syntax"></a>Syntax

```JSON
{
    "name": "Hive Activity",
    "description": "description",
    "type": "HDInsightHive",
    "inputs": [
      {
        "name": "input tables"
      }
    ],
    "outputs": [
      {
        "name": "output tables"
      }
    ],
    "linkedServiceName": "MyHDInsightLinkedService",
    "typeProperties": {
      "script": "Hive script",
      "scriptPath": "<pathtotheHivescriptfileinAzureblobstorage>",
      "defines": {
        "param1": "param1Value"
      }
    },
   "scheduler": {
      "frequency": "Day",
      "interval": 1
    }
}
```
## <a name="syntax-details"></a>Information om syntax
| Egenskap | Beskrivning | Obligatorisk |
| --- | --- | --- |
| name |Namn på aktiviteten |Ja |
| beskrivning |Text som beskriver vad aktiviteten används för |Inga |
| typ |HDinsightHive |Ja |
| tillför |Indata som används av Hive-aktiviteten |Inga |
| utdata |Utdata som produceras av Hive-aktiviteten |Ja |
| linkedServiceName |Referens till HDInsight-klustret som registrerats som en länkad tjänst i Data Factory |Ja |
| skript |Ange Hive-skriptet infogat |Inga |
| scriptPath |Lagra Hive-skriptet i en Azure Blob-lagring och ange sökvägen till filen. Använd script-eller scriptPath-egenskapen. Båda kan inte användas tillsammans. Fil namnet är Skift läges känsligt. |Inga |
| definierar |Ange parametrar som nyckel/värde-par för referenser i Hive-skriptet med hjälp av "hiveconf" |Inga |

## <a name="example"></a>Exempel
Nu ska vi titta på ett exempel på spel loggar för att identifiera hur lång tid det tar för användare att spela spel som lanserats av ditt företag. 

Följande logg är en exempel spel logg, som är komma ( `,` ) separerad och innehåller följande fält – profil, SessionStart, duration, SrcIPAddress och gametype.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

**Hive-skriptet** för att bearbeta dessa data:

```
DROP TABLE IF EXISTS HiveSampleIn; 
CREATE EXTERNAL TABLE HiveSampleIn 
(
    ProfileID        string, 
    SessionStart     string, 
    Duration         int, 
    SrcIPAddress     string, 
    GameType         string
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/samplein/'; 

DROP TABLE IF EXISTS HiveSampleOut; 
CREATE EXTERNAL TABLE HiveSampleOut 
(    
    ProfileID     string, 
    Duration     int
) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION 'wasb://adfwalkthrough@<storageaccount>.blob.core.windows.net/sampleout/';

INSERT OVERWRITE TABLE HiveSampleOut
Select 
    ProfileID,
    SUM(Duration)
FROM HiveSampleIn Group by ProfileID
```

Om du vill köra det här Hive-skriptet i en Data Factory pipeline måste du göra följande

1. Skapa en länkad tjänst för att registrera [ditt eget HDInsight Compute-kluster](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) eller konfigurera [kluster för HDInsight-beräkning på begäran](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Vi kallar den här länkade tjänsten "HDInsightLinkedService".
2. Skapa en [länkad tjänst](data-factory-azure-blob-connector.md) för att konfigurera anslutningen till Azure Blob Storage som är värd för data. Vi kallar den här länkade tjänsten "StorageLinkedService"
3. Skapa [data uppsättningar](data-factory-create-datasets.md) som pekar på indata och utdata. Låt oss anropa indata-datauppsättningen "HiveSampleIn" och utdata-datauppsättningen "HiveSampleOut"
4. Kopiera Hive-frågan som en fil till Azure Blob Storage som konfigurerades i steg #2. om lagringen för att vara värd för data skiljer sig från den som är värd för den här frågan skapar du en separat Azure Storage länkad tjänst och refererar till den i aktiviteten. Använd **scriptPath** för att ange sökvägen till Hive-frågefiler och **scriptLinkedService** för att ange den Azure-lagring som innehåller skript filen. 
   
   > [!NOTE]
   > Du kan också ange Hive-skriptet infogat i aktivitets definitionen med hjälp av **skript** egenskapen. Vi rekommenderar inte den här metoden eftersom alla specialtecken i skriptet i JSON-dokumentet måste vara undantagna och kan orsaka fel söknings problem. Den bästa metoden är att följa steg #4.
   > 
   > 
5. Skapa en pipeline med HDInsightHive-aktiviteten. Aktivitets processerna/transformerar data.

  ```json
  {
    "name": "HiveActivitySamplePipeline",
       "properties": {
    "activities": [
      {
        "name": "HiveActivitySample",
        "type": "HDInsightHive",
        "inputs": [
        {
          "name": "HiveSampleIn"
        }
        ],
             "outputs": [
               {
                "name": "HiveSampleOut"
               }
             ],
             "linkedServiceName": "HDInsightLinkedService",
             "typeproperties": {
                 "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                 "scriptLinkedService": "StorageLinkedService"
             },
              "scheduler": {
          "frequency": "Hour",
                   "interval": 1
             }
           }
      ]
    }
  }
  ```

6. Distribuera pipelinen. Mer information finns i artikeln om att [skapa pipeliner](data-factory-create-pipelines.md) . 
7. Övervaka pipelinen med hjälp av vyerna för övervakning och hantering av data fabrik. Mer information finns i artikeln [övervaka och hantera data Factory pipelines](data-factory-monitor-manage-pipelines.md) . 

## <a name="specifying-parameters-for-a-hive-script"></a>Ange parametrar för ett Hive-skript
I det här exemplet matas spel loggar in dagligen i Azure Blob Storage och lagras i en mapp som partitionerats med datum och tid. Du vill Parameterisera Hive-skriptet och skicka platsen för indata-mappen dynamiskt under körning och även skapa utdata som partitionerats med datum och tid.

Gör följande om du vill använda parametriserade Hive-skript

* Definiera parametrarna i **definierar**.

  ```JSON  
    {
        "name": "HiveActivitySamplePipeline",
          "properties": {
        "activities": [
             {
                "name": "HiveActivitySample",
                "type": "HDInsightHive",
                "inputs": [
                      {
                        "name": "HiveSampleIn"
                      }
                ],
                "outputs": [
                      {
                        "name": "HiveSampleOut"
                    }
                ],
                "linkedServiceName": "HDInsightLinkedService",
                "typeproperties": {
                      "scriptPath": "adfwalkthrough\\scripts\\samplehive.hql",
                      "scriptLinkedService": "StorageLinkedService",
                      "defines": {
                        "Input": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)",
                        "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
                      },
                       "scheduler": {
                          "frequency": "Hour",
                          "interval": 1
                    }
                }
              }
        ]
      }
    }
    ```
* I Hive-skriptet, referera till-parametern med **$ {hiveconf: parameterName}**. 
  
    ```
    DROP TABLE IF EXISTS HiveSampleIn; 
    CREATE EXTERNAL TABLE HiveSampleIn 
    (
        ProfileID     string, 
        SessionStart     string, 
        Duration     int, 
        SrcIPAddress     string, 
        GameType     string
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Input}'; 

    DROP TABLE IF EXISTS HiveSampleOut; 
    CREATE EXTERNAL TABLE HiveSampleOut 
    (
        ProfileID     string, 
        Duration     int
    ) ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' LINES TERMINATED BY '10' STORED AS TEXTFILE LOCATION '${hiveconf:Output}';

    INSERT OVERWRITE TABLE HiveSampleOut
    Select 
        ProfileID,
        SUM(Duration)
    FROM HiveSampleIn Group by ProfileID
    ```
  ## <a name="see-also"></a>Se även
* [Aktivitet i gris](data-factory-pig-activity.md)
* [MapReduce-aktivitet](data-factory-map-reduce.md)
* [Hadoop streaming-aktivitet](data-factory-hadoop-streaming-activity.md)
* [Anropa Spark-program](data-factory-spark.md)
* [Anropa R-skript](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)

