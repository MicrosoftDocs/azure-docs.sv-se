---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: 19927fd274cbb7337248fb2ae8cf9d882612d570
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "92369256"
---
Azure Data Factory stöder följande transformeringsaktiviteter som kan läggas till enskilt, eller kopplade till en annan aktivitet, i pipelines.

| Datatransformeringsaktivitet | Compute-miljö |
|:--- |:--- |
| [Hive](../articles/data-factory/v1/data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](../articles/data-factory/v1/data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](../articles/data-factory/v1/data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Hadoop Streaming](../articles/data-factory/v1/data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Spark](../articles/data-factory/v1/data-factory-spark.md) | HDInsight [Hadoop] |
| [Azure Machine Learning Studio (klassisk) aktiviteter: batch-körning och uppdaterings resurs](../articles/data-factory/v1/data-factory-azure-ml-batch-execution-activity.md) |Azure VM |
| [Lagrad procedur](../articles/data-factory/v1/data-factory-stored-proc-activity.md) |Azure SQL, Azure Synapse Analytics eller SQL Server |
| [Data Lake Analytics U-SQL](../articles/data-factory/v1/data-factory-usql-activity.md) |Azure Data Lake Analytics |
| [DotNet](../articles/data-factory/v1/data-factory-use-custom-activities.md) |HDInsight [Hadoop] eller Azure Batch |

> [!NOTE]
> Du kan använda MapReduce-aktiviteten för att köra Spark-program i ditt HDInsight Spark-kluster. Mer information finns i [Invoke Spark programs from Azure Data Factory](../articles/data-factory/v1/data-factory-spark.md) (Anropa Spark-program från Azure Data Factory).
> Du kan skapa en anpassad aktivitet som kör R-skript i ditt HDInsight-kluster med R installerat. Se [Run R Script using Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample) (Köra R-skript med Azure Data Factory).
> 
> 

