---
title: Transformera data med hjälp av gris-aktivitet i Azure Data Factory
description: Lär dig hur du kan använda aktiviteten gris i Azure Data Factory v1 för att köra gris-skript på ett eget HDInsight-kluster på begäran.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 2f59734b5452b5a06b49583954f8851e84e8f84d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100387180"
---
# <a name="transform-data-using-pig-activity-in-azure-data-factory"></a>Transformera data med hjälp av gris-aktivitet i Azure Data Factory
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
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [transformera data med aktiviteten fax i Data Factory](../transform-data-using-hadoop-pig.md).


HDInsight gris-aktiviteten i en Data Factory [pipeline](data-factory-create-pipelines.md) kör gris-frågor på [ditt eget](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) eller Windows/Linux-baserade HDInsight-kluster [på begäran](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) . Den här artikeln bygger på artikeln [data omvandlings aktiviteter](data-factory-data-transformation-activities.md) , som visar en allmän översikt över Datatransformeringen och de omvandlings aktiviteter som stöds.

> [!NOTE] 
> Om du inte har använt Azure Data Factory läser du igenom [introduktionen till Azure Data Factory](data-factory-introduction.md) och gör självstudien: [skapa din första data pipeline innan du](data-factory-build-your-first-pipeline.md) läser den här artikeln. 

## <a name="syntax"></a>Syntax

```JSON
{
  "name": "HiveActivitySamplePipeline",
  "properties": {
    "activities": [
      {
        "name": "Pig Activity",
        "description": "description",
        "type": "HDInsightPig",
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
          "script": "Pig script",
          "scriptPath": "<pathtothePigscriptfileinAzureblobstorage>",
          "defines": {
            "param1": "param1Value"
          }
        },
        "scheduler": {
          "frequency": "Day",
          "interval": 1
        }
      }
    ]
  }
}
```

## <a name="syntax-details"></a>Information om syntax

| Egenskap | Beskrivning | Obligatorisk |
| --- | --- | --- |
| name |Namn på aktiviteten |Ja |
| beskrivning |Text som beskriver vad aktiviteten används för |Inga |
| typ |HDinsightPig |Ja |
| tillför |En eller flera indata som används av aktiviteten gris |Inga |
| utdata |En eller flera utdata som produceras av gris-aktiviteten |Ja |
| linkedServiceName |Referens till HDInsight-klustret som registrerats som en länkad tjänst i Data Factory |Ja |
| skript |Ange det infogade gris-skriptet |Inga |
| scriptPath |Lagra ditt gris-skript i en Azure Blob-lagring och ange sökvägen till filen. Använd script-eller scriptPath-egenskapen. Båda kan inte användas tillsammans. Fil namnet är Skift läges känsligt. |Inga |
| definierar |Ange parametrar som nyckel/värde-par för referenser i gris-skriptet |Inga |

## <a name="example"></a>Exempel
Nu ska vi titta på ett exempel på Game logs Analytics där du vill identifiera den tid som spelare kan spela för spel som lanseras av ditt företag.

Följande exempel på spel logg är en kommaavgränsad fil (,). Det innehåller följande fält – profil, SessionStart, duration, SrcIPAddress och GameType.

```
1809,2014-05-04 12:04:25.3470000,14,221.117.223.75,CaptureFlag
1703,2014-05-04 06:05:06.0090000,16,12.49.178.247,KingHill
1703,2014-05-04 10:21:57.3290000,10,199.118.18.179,CaptureFlag
1809,2014-05-04 05:24:22.2100000,23,192.84.66.141,KingHill
.....
```

**Gris-skriptet** för att bearbeta dessa data:

```
PigSampleIn = LOAD 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/samplein/' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);

GroupProfile = Group PigSampleIn all;

PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);

Store PigSampleOut into 'wasb://adfwalkthrough@anandsub14.blob.core.windows.net/sampleoutpig/' USING PigStorage (',');
```

Utför följande steg för att köra det här gris-skriptet i en Data Factory pipeline:

1. Skapa en länkad tjänst för att registrera [ditt eget HDInsight Compute-kluster](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) eller konfigurera [kluster för HDInsight-beräkning på begäran](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). Vi kallar den här länkade tjänsten **HDInsightLinkedService**.
2. Skapa en [länkad tjänst](data-factory-azure-blob-connector.md) för att konfigurera anslutningen till Azure Blob Storage som är värd för data. Vi kallar den här länkade tjänsten **StorageLinkedService**.
3. Skapa [data uppsättningar](data-factory-create-datasets.md) som pekar på indata och utdata. Låt oss anropa indata-datauppsättningen **PigSampleIn** och **PigSampleOut** för utgående data uppsättning.
4. Kopiera gris-frågan i en fil Azure-Blob Storage som kon figurer ATS i steg #2. Om Azure-lagringen som är värd för data skiljer sig från den som är värd för Frågeredigeraren skapar du en separat Azure Storage länkad tjänst. Referera till den länkade tjänsten i aktivitets konfigurationen. Använd **scriptPath** för att ange sökvägen till skript filen för gris och **scriptLinkedService**. 
   
   > [!NOTE]
   > Du kan också ange Fax skriptets infogade definition med hjälp av **skript** egenskapen. Vi rekommenderar dock inte den här metoden eftersom alla specialtecken i skriptet måste vara undantagna och kan orsaka fel söknings problem. Den bästa metoden är att följa steg #4.
   >
   >
5. Skapa pipelinen med HDInsightPig-aktiviteten. Den här aktiviteten bearbetar indata genom att köra gris-skript på HDInsight-kluster.

    ```JSON
    {
      "name": "PigActivitySamplePipeline",
      "properties": {
        "activities": [
          {
            "name": "PigActivitySample",
            "type": "HDInsightPig",
            "inputs": [
              {
                "name": "PigSampleIn"
              }
            ],
            "outputs": [
              {
                "name": "PigSampleOut"
              }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
              "scriptPath": "adfwalkthrough\\scripts\\enrichlogs.pig",
              "scriptLinkedService": "StorageLinkedService"
            },
            "scheduler": {
              "frequency": "Day",
              "interval": 1
            }
          }
        ]
      }
    }
    ```
6. Distribuera pipelinen. Mer information finns i artikeln om att [skapa pipeliner](data-factory-create-pipelines.md) . 
7. Övervaka pipelinen med hjälp av vyerna för övervakning och hantering av data fabrik. Mer information finns i artikeln [övervaka och hantera data Factory pipelines](data-factory-monitor-manage-pipelines.md) .

## <a name="specifying-parameters-for-a-pig-script"></a>Ange parametrar för ett gris-skript
Tänk på följande exempel: spel loggar matas in dagligen i Azure Blob Storage och lagras i en mapp som partitionerats baserat på datum och tid. Du vill Parameterisera i gris-skriptet och skicka platsen för indata-mappen dynamiskt under körningen och även skapa utdata som partitionerats med datum och tid.

Gör följande om du vill använda ett parameter Scripting gris-skript:

* Definiera parametrarna i **definierar**.

    ```JSON
    {
      "name": "PigActivitySamplePipeline",
      "properties": {
        "activities": [
          {
            "name": "PigActivitySample",
            "type": "HDInsightPig",
            "inputs": [
              {
                "name": "PigSampleIn"
              }
            ],
            "outputs": [
              {
                "name": "PigSampleOut"
              }
            ],
            "linkedServiceName": "HDInsightLinkedService",
            "typeproperties": {
              "scriptPath": "adfwalkthrough\\scripts\\samplepig.hql",
              "scriptLinkedService": "StorageLinkedService",
              "defines": {
                "Input": "$$Text.Format('wasb: //adfwalkthrough@<storageaccountname>.blob.core.windows.net/samplein/yearno={0: yyyy}/monthno={0:MM}/dayno={0: dd}/',SliceStart)",
                "Output": "$$Text.Format('wasb://adfwalkthrough@<storageaccountname>.blob.core.windows.net/sampleout/yearno={0:yyyy}/monthno={0:MM}/dayno={0:dd}/', SliceStart)"
              }
            },
            "scheduler": {
              "frequency": "Day",
              "interval": 1
            }
          }
        ]
      }
    }
    ```
* I gris-skriptet, referera till parametrarna med hjälp av **$parameterName** som visas i följande exempel:

    ```
    PigSampleIn = LOAD '$Input' USING PigStorage(',') AS (ProfileID:chararray, SessionStart:chararray, Duration:int, SrcIPAddress:chararray, GameType:chararray);
    GroupProfile = Group PigSampleIn all;
    PigSampleOut = Foreach GroupProfile Generate PigSampleIn.ProfileID, SUM(PigSampleIn.Duration);
    Store PigSampleOut into '$Output' USING PigStorage (','); 
    ```

## <a name="see-also"></a>Se även
* [Hive-aktivitet](data-factory-hive-activity.md)
* [MapReduce-aktivitet](data-factory-map-reduce.md)
* [Hadoop streaming-aktivitet](data-factory-hadoop-streaming-activity.md)
* [Anropa Spark-program](data-factory-spark.md)
* [Anropa R-skript](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample)
