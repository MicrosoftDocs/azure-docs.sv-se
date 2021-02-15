---
title: Transformera data med Hadoop streaming-aktivitet
description: Förklarar hur du använder Hadoop streaming-aktivitet i Azure Data Factory för att transformera data genom att köra Hadoop streaming-program i ett Hadoop-kluster.
author: nabhishek
ms.author: abnarain
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/08/2020
ms.openlocfilehash: e2a9bc9d664ba15da3cdefa5cf28519ab703d6ce
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361445"
---
# <a name="transform-data-using-hadoop-streaming-activity-in-azure-data-factory"></a>Transformera data med hjälp av Hadoop streaming-aktivitet i Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-hadoop-streaming-activity.md)
> * [Aktuell version](transform-data-using-hadoop-streaming.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

HDInsight streaming-aktiviteten i en Data Factory [pipeline](concepts-pipelines-activities.md) kör Hadoop streaming-program på [ditt eget](compute-linked-services.md#azure-hdinsight-linked-service) eller [på begäran HDInsight-](compute-linked-services.md#azure-hdinsight-on-demand-linked-service) kluster. Den här artikeln bygger på artikeln [data omvandlings aktiviteter](transform-data.md) , som visar en allmän översikt över Datatransformeringen och de omvandlings aktiviteter som stöds.

Om du är nybörjare på Azure Data Factory läser du [Introduktion till Azure Data Factory](introduction.md) och gör [självstudien: transformera data](tutorial-transform-data-spark-powershell.md) innan du läser den här artikeln. 

## <a name="json-sample"></a>JSON-exempel
```json
{
    "name": "Streaming Activity",
    "description": "Description",
    "type": "HDInsightStreaming",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mapper": "MyMapper.exe",
        "reducer": "MyReducer.exe",
        "combiner": "MyCombiner.exe",
        "fileLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "filePaths": [
            "<containername>/example/apps/MyMapper.exe",
            "<containername>/example/apps/MyReducer.exe",
            "<containername>/example/apps/MyCombiner.exe"
        ],
        "input": "wasb://<containername>@<accountname>.blob.core.windows.net/example/input/MapperInput.txt",
        "output": "wasb://<containername>@<accountname>.blob.core.windows.net/example/output/ReducerOutput.txt",
        "commandEnvironment": [
            "CmdEnvVarName=CmdEnvVarValue"
        ],
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

| Egenskap          | Beskrivning                              | Obligatorisk |
| ----------------- | ---------------------------------------- | -------- |
| name              | Namn på aktiviteten                     | Ja      |
| beskrivning       | Text som beskriver vad aktiviteten används för | Inga       |
| typ              | För Hadoop streaming-aktivitet är aktivitets typen HDInsightStreaming | Ja      |
| linkedServiceName | Referens till HDInsight-klustret som registrerats som en länkad tjänst i Data Factory. Mer information om den här länkade tjänsten finns i artikeln [Compute-länkade tjänster](compute-linked-services.md) . | Ja      |
| mappning            | Anger namnet på den körbara filen för mapper | Ja      |
| minskning           | Anger namnet på den programbegränsande körbara filen | Ja      |
| kombinations          | Anger namnet på den kombinerade program filen | Inga       |
| fileLinkedService | Referens till en Azure Storage länkad tjänst som används för att lagra mapparna Mapper, kombinerare och minska program som ska köras. Endast **[Azure Blob Storage](./connector-azure-blob-storage.md)** -och **[ADLS Gen2](./connector-azure-data-lake-storage.md)** länkade tjänster stöds här. Om du inte anger den här länkade tjänsten används den Azure Storage länkade tjänsten som definierats i den länkade HDInsight-tjänsten. | Inga       |
| filePath          | Ange en matris med sökvägen till mapparna Mapper, kombinerare och Minskare som lagras i Azure Storage som refereras av fileLinkedService. Sökvägen är skiftlägeskänslig. | Ja      |
| indata             | Anger WASB-sökvägen till indatafilen för mapper. | Ja      |
| utdata            | Anger WASB-sökvägen till utdatafilen för minsknings filen. | Ja      |
| getDebugInfo      | Anger när loggfilerna kopieras till Azure Storage som används av HDInsight-kluster (eller) som anges av scriptLinkedService. Tillåtna värden: ingen, Always eller Failure. Standardvärde: ingen. | Inga       |
| ogiltiga         | Anger en matris med argument för ett Hadoop-jobb. Argumenten skickas som kommando rads argument till varje aktivitet. | Inga       |
| definierar           | Ange parametrar som nyckel/värde-par för referenser i Hive-skriptet. | Nej       | 

## <a name="next-steps"></a>Nästa steg
Se följande artiklar som förklarar hur du omformar data på andra sätt: 

* [U-SQL-aktivitet](transform-data-using-data-lake-analytics.md)
* [Hive-aktivitet](transform-data-using-hadoop-hive.md)
* [Aktivitet i gris](transform-data-using-hadoop-pig.md)
* [MapReduce-aktivitet](transform-data-using-hadoop-map-reduce.md)
* [Spark-aktivitet](transform-data-using-spark.md)
* [.NET-anpassad aktivitet](transform-data-using-dotnet-custom-activity.md)
* [Azure Machine Learning Studio (klassisk) batch execution Activity](transform-data-using-machine-learning.md)
* [Lagrad procedur aktivitet](transform-data-using-stored-procedure.md)