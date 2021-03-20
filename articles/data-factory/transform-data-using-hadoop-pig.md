---
title: Transformera data med Hadoop gris-aktivitet
description: Lär dig hur du kan använda gris-aktiviteten i en Azure Data Factory för att köra gris-skript på ett eget HDInsight-kluster på begäran.
ms.service: data-factory
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: seo-lt-2019
ms.date: 05/08/2020
ms.openlocfilehash: 1d0f5ce9dfbf09023b57accb761b5dd85b0f3599
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100375722"
---
# <a name="transform-data-using-hadoop-pig-activity-in-azure-data-factory"></a>Transformera data med Hadoop gris-aktivitet i Azure Data Factory

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-pig-activity.md)
> * [Aktuell version](transform-data-using-hadoop-pig.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

HDInsight gris-aktiviteten i en Data Factory [pipeline](concepts-pipelines-activities.md) kör gris-frågor på [ditt eget](compute-linked-services.md#azure-hdinsight-linked-service) eller [på begäran HDInsight-](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) kluster. Den här artikeln bygger på artikeln [data omvandlings aktiviteter](transform-data.md) , som visar en allmän översikt över Datatransformeringen och de omvandlings aktiviteter som stöds.

Om du är nybörjare på Azure Data Factory läser du [Introduktion till Azure Data Factory](introduction.md) och gör [självstudien: transformera data](tutorial-transform-data-spark-powershell.md) innan du läser den här artikeln. 

## <a name="syntax"></a>Syntax

```json
{
    "name": "Pig Activity",
    "description": "description",
    "type": "HDInsightPig",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "scriptLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "scriptPath": "MyAzureStorage\\PigScripts\\MyPigSript.pig",
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ],
        "defines": {
            "param1": "param1Value"
        }
    }   
}
```
## <a name="syntax-details"></a>Information om syntax

| Egenskap            | Beskrivning                              | Obligatorisk |
| ------------------- | ---------------------------------------- | -------- |
| name                | Namn på aktiviteten                     | Ja      |
| beskrivning         | Text som beskriver vad aktiviteten används för | Inga       |
| typ                | För Hive-aktivitet är aktivitets typen HDinsightPig | Ja      |
| linkedServiceName   | Referens till HDInsight-klustret som registrerats som en länkad tjänst i Data Factory. Mer information om den här länkade tjänsten finns i artikeln [Compute-länkade tjänster](compute-linked-services.md) . | Ja      |
| scriptLinkedService | Referens till en Azure Storage länkad tjänst som används för att lagra det gris-skript som ska köras. Endast **[Azure Blob Storage](./connector-azure-blob-storage.md)** -och **[ADLS Gen2](./connector-azure-data-lake-storage.md)** länkade tjänster stöds här. Om du inte anger den här länkade tjänsten används den Azure Storage länkade tjänsten som definierats i den länkade HDInsight-tjänsten. | Inga       |
| scriptPath          | Ange sökvägen till skript filen som lagras i Azure Storage som refereras av scriptLinkedService. Fil namnet är Skift läges känsligt. | Inga       |
| getDebugInfo        | Anger när loggfilerna kopieras till Azure Storage som används av HDInsight-kluster (eller) som anges av scriptLinkedService. Tillåtna värden: ingen, Always eller Failure. Standardvärde: ingen. | Inga       |
| ogiltiga           | Anger en matris med argument för ett Hadoop-jobb. Argumenten skickas som kommando rads argument till varje aktivitet. | Inga       |
| definierar             | Ange parametrar som nyckel/värde-par för referenser i gris-skriptet. | Nej       |

## <a name="next-steps"></a>Nästa steg
Se följande artiklar som förklarar hur du omformar data på andra sätt: 

* [U-SQL-aktivitet](transform-data-using-data-lake-analytics.md)
* [Hive-aktivitet](transform-data-using-hadoop-hive.md)
* [MapReduce-aktivitet](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streaming-aktivitet](transform-data-using-hadoop-streaming.md)
* [Spark-aktivitet](transform-data-using-spark.md)
* [.NET-anpassad aktivitet](transform-data-using-dotnet-custom-activity.md)
* [Azure Machine Learning Studio (klassisk) batch execution Activity](transform-data-using-machine-learning.md)
* [Lagrad procedur aktivitet](transform-data-using-stored-procedure.md)