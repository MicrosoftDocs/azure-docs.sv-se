---
title: Transformera data med Spark-aktivitet
description: Lär dig hur du omvandlar data genom att köra Spark-program från en Azure Data Factory pipeline med Spark-aktiviteten.
ms.service: data-factory
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
ms.custom: seo-lt-2019
ms.date: 05/08/2020
ms.openlocfilehash: e5c50d2cbd16ad2808dab485ad2b2870d6f3d350
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100392365"
---
# <a name="transform-data-using-spark-activity-in-azure-data-factory"></a>Transformera data med Spark-aktivitet i Azure Data Factory
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-spark.md)
> * [Aktuell version](transform-data-using-spark.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Spark-aktiviteten i en Data Factory [pipeline](concepts-pipelines-activities.md) kör ett Spark-program på [ditt eget](compute-linked-services.md#azure-hdinsight-linked-service) eller [på begäran HDInsight-](compute-linked-services.md#azure-hdinsight-on-demand-linked-service)  kluster. Den här artikeln bygger på artikeln [data omvandlings aktiviteter](transform-data.md) , som visar en allmän översikt över Datatransformeringen och de omvandlings aktiviteter som stöds. När du använder en spark-länkad Spark på begäran skapar Data Factory automatiskt ett Spark-kluster för dig just-in-Time för att bearbeta data och tar sedan bort klustret när bearbetningen är klar. 


## <a name="spark-activity-properties"></a>Egenskaper för Spark-aktivitet
Här är exempel-JSON-definitionen för en spark-aktivitet:    

```json
{
    "name": "Spark Activity",
    "description": "Description",
    "type": "HDInsightSpark",
    "linkedServiceName": {
        "referenceName": "MyHDInsightLinkedService",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "sparkJobLinkedService": {
            "referenceName": "MyAzureStorageLinkedService",
            "type": "LinkedServiceReference"
        },
        "rootPath": "adfspark",
        "entryFilePath": "test.py",
        "sparkConfig": {
            "ConfigItem1": "Value"
        },
        "getDebugInfo": "Failure",
        "arguments": [
            "SampleHadoopJobArgument1"
        ]
    }
}
```

I följande tabell beskrivs de JSON-egenskaper som används i JSON-definitionen:

| Egenskap              | Beskrivning                              | Obligatorisk |
| --------------------- | ---------------------------------------- | -------- |
| name                  | Namnet på aktiviteten i pipelinen.    | Ja      |
| beskrivning           | Text som beskriver vad aktiviteten gör.  | Inga       |
| typ                  | För Spark-aktivitet är aktivitets typen HDInsightSpark. | Ja      |
| linkedServiceName     | Namnet på den länkade HDInsight Spark-tjänsten som Spark-programmet körs på. Mer information om den här länkade tjänsten finns i artikeln [Compute-länkade tjänster](compute-linked-services.md) . | Ja      |
| SparkJobLinkedService | Den länkade tjänsten Azure Storage som innehåller Spark-jobbets fil, beroenden och loggar. Endast **[Azure Blob Storage](./connector-azure-blob-storage.md)** -och **[ADLS Gen2](./connector-azure-data-lake-storage.md)** länkade tjänster stöds här. Om du inte anger något värde för den här egenskapen används det lagrings utrymme som är associerat med HDInsight-kluster. Värdet för den här egenskapen kan bara vara en Azure Storage länkad tjänst. | Inga       |
| rootPath              | Azure Blob-behållaren och-mappen som innehåller Spark-filen. Fil namnet är Skift läges känsligt. Se avsnittet mappstruktur (nästa avsnitt) för information om den här mappens struktur. | Ja      |
| entryFilePath         | Relativ sökväg till rotmappen för Spark-koden/-paketet. Post filen måste vara antingen en python-fil eller en. jar-fil. | Ja      |
| className             | Programmets Java/Spark-huvud klass      | Inga       |
| ogiltiga             | En lista med kommando rads argument för Spark-programmet. | Inga       |
| proxyUser             | Användar kontot som ska personifieras för att köra Spark-programmet | Inga       |
| sparkConfig           | Ange värden för konfigurations egenskaper för Spark som anges i avsnittet: [Spark-konfiguration – program egenskaper](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Inga       |
| getDebugInfo          | Anger när Spark-loggfilerna ska kopieras till Azure-lagringen som används av HDInsight-kluster (eller) som anges av sparkJobLinkedService. Tillåtna värden: ingen, Always eller Failure. Standardvärde: ingen. | Inga       |

## <a name="folder-structure"></a>Mappstruktur
Spark-jobb är mer utöknings bara av jobben i gris/Hive. För Spark-jobb kan du tillhandahålla flera beroenden, till exempel jar-paket (placerade i Java-CLASSPATH), python-filer (placeras på PYTHONPATH) och andra filer.

Skapa följande mappstruktur i Azure blob-lagringen som refereras av den länkade HDInsight-tjänsten. Ladda sedan upp beroende filer till lämpliga undermappar i rotmappen som representeras av **entryFilePath**. Du kan till exempel Ladda upp python-filer till undermappen pyFiles och jar-filer till undermappen jar v7 i rotmappen. Vid körning förväntar Data Factory tjänsten följande mappstruktur i Azure Blob Storage:     

| Sökväg                  | Beskrivning                              | Krävs | Typ   |
| --------------------- | ---------------------------------------- | -------- | ------ |
| `.` skogen            | Spark-jobbets rot Sök väg i den länkade lagrings tjänsten | Ja      | Mapp |
| &lt;användardefinierad &gt; | Sökvägen som pekar på post filen för Spark-jobbet | Ja      | Fil   |
| ./jars                | Alla filer i den här mappen laddas upp och placeras i Java-classpath för klustret | Inga       | Mapp |
| ./pyFiles             | Alla filer i den här mappen överförs och placeras i PYTHONPATH i klustret | Inga       | Mapp |
| ./files               | Alla filer i den här mappen överförs och placeras i utförar arbets katalog | Inga       | Mapp |
| ./archives            | Alla filer i den här mappen är okomprimerade | Inga       | Mapp |
| ./logs                | Den mapp som innehåller loggar från Spark-klustret. | Inga       | Mapp |

Här är ett exempel på ett lagrings utrymme som innehåller två Spark-jobbmallar i Azure-Blob Storage som refereras till av den länkade HDInsight-tjänsten.

```
SparkJob1
    main.jar
    files
        input1.txt
        input2.txt
    jars
        package1.jar
        package2.jar
    logs

SparkJob2
    main.py
    pyFiles
        scrip1.py
        script2.py
    logs
```
## <a name="next-steps"></a>Nästa steg
Se följande artiklar som förklarar hur du omformar data på andra sätt: 

* [U-SQL-aktivitet](transform-data-using-data-lake-analytics.md)
* [Hive-aktivitet](transform-data-using-hadoop-hive.md)
* [Aktivitet i gris](transform-data-using-hadoop-pig.md)
* [MapReduce-aktivitet](transform-data-using-hadoop-map-reduce.md)
* [Hadoop streaming-aktivitet](transform-data-using-hadoop-streaming.md)
* [Spark-aktivitet](transform-data-using-spark.md)
* [.NET-anpassad aktivitet](transform-data-using-dotnet-custom-activity.md)
* [Azure Machine Learning Studio (klassisk) batch execution Activity](transform-data-using-machine-learning.md)
* [Lagrad procedur aktivitet](transform-data-using-stored-procedure.md)
