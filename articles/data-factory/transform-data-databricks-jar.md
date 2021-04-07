---
title: Transformera data med Databricks jar
description: Lär dig hur du bearbetar eller transformerar data genom att köra en Databricks-jar i en Azure Data Factory pipeline.
ms.service: data-factory
ms.topic: conceptual
ms.author: abnarain
author: nabhishek
ms.date: 02/10/2021
ms.openlocfilehash: ccfe8fbf330e1c7f6f415b64a1f18d93a084a0ba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100374022"
---
# <a name="transform-data-by-running-a-jar-activity-in-azure-databricks"></a>Transformera data genom att köra en jar-aktivitet i Azure Databricks

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Databricks jar-aktivitet i en [Data Factory-pipeline](concepts-pipelines-activities.md) kör en spark-jar i ditt Azure Databricks-kluster. Den här artikeln bygger på artikeln [data omvandlings aktiviteter](transform-data.md) , som visar en allmän översikt över Datatransformeringen och de omvandlings aktiviteter som stöds. Azure Databricks är en hanterad plattform för att köra Apache Spark.

Om du vill se en introduktion och demonstration av den här funktionen rekommenderar vi följande videoklipp (11 minuter):

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-jar-activity-definition"></a>Definition av Databricks jar-aktivitet

Här är exempel-JSON-definitionen för en Databricks jar-aktivitet:

```json
{
    "name": "SparkJarActivity",
    "type": "DatabricksSparkJar",
    "linkedServiceName": {
        "referenceName": "AzureDatabricks",
        "type": "LinkedServiceReference"
    },
    "typeProperties": {
        "mainClassName": "org.apache.spark.examples.SparkPi",
        "parameters": [ "10" ],
        "libraries": [
            {
                "jar": "dbfs:/docs/sparkpi.jar"
            }
        ]
    }
}

```

## <a name="databricks-jar-activity-properties"></a>Egenskaper för Databricks jar-aktivitet

I följande tabell beskrivs de JSON-egenskaper som används i JSON-definitionen:

|Egenskap|Beskrivning|Obligatorisk|
|:--|---|:-:|
|name|Namnet på aktiviteten i pipelinen.|Ja|
|beskrivning|Text som beskriver vad aktiviteten gör.|Inga|
|typ|För Databricks jar-aktivitet är aktivitets typen DatabricksSparkJar.|Ja|
|linkedServiceName|Namnet på den länkade Databricks-tjänst som jar-aktiviteten körs på. Mer information om den här länkade tjänsten finns i artikeln [Compute-länkade tjänster](compute-linked-services.md) .|Ja|
|mainClassName|Det fullständiga namnet på klassen som innehåller den huvudsakliga metoden som ska köras. Den här klassen måste finnas i en JAR-form som är ett bibliotek. En JAR-fil kan innehålla flera klasser. Varje klass kan innehålla en main-metod.|Ja|
|parametrar|Parametrar som skickas till main-metoden. Den här egenskapen är en sträng mat ris.|Inga|
|bibliotek|En lista med bibliotek som ska installeras i klustret som ska köra jobbet. Det kan vara en matris med <sträng, objekt>|Ja (minst en som innehåller mainClassName-metoden)|

> [!NOTE]
> **Känt problem** – när du använder samma [interaktiva kluster](compute-linked-services.md#example---using-existing-interactive-cluster-in-databricks) för att köra samtidiga Databricks jar-aktiviteter (utan kluster omstart), finns det ett känt problem i Databricks där i parametrarna för den första aktiviteten kommer att användas av följande aktiviteter. Därför leder det till att felaktiga parametrar skickas till efterföljande jobb. För att minimera detta använder du i stället ett [jobb kluster](compute-linked-services.md#example---using-new-job-cluster-in-databricks) .

## <a name="supported-libraries-for-databricks-activities"></a>Bibliotek som stöds för databricks-aktiviteter

I den tidigare Databricks-aktivitets definitionen angav du följande biblioteks typer: `jar` ,,,, `egg` `maven` `pypi` `cran` .

```json
{
    "libraries": [
        {
            "jar": "dbfs:/mnt/libraries/library.jar"
        },
        {
            "egg": "dbfs:/mnt/libraries/library.egg"
        },
        {
            "maven": {
                "coordinates": "org.jsoup:jsoup:1.7.2",
                "exclusions": [ "slf4j:slf4j" ]
            }
        },
        {
            "pypi": {
                "package": "simplejson",
                "repo": "http://my-pypi-mirror.com"
            }
        },
        {
            "cran": {
                "package": "ada",
                "repo": "https://cran.us.r-project.org"
            }
        }
    ]
}

```

Mer information finns i Databricks- [dokumentationen](/azure/databricks/dev-tools/api/latest/libraries#managedlibrarieslibrary) för biblioteks typer.

## <a name="how-to-upload-a-library-in-databricks"></a>Ladda upp ett bibliotek i Databricks

### <a name="you-can-use-the-workspace-ui"></a>Du kan använda arbets ytans användar gränssnitt:

1. [Använda användar gränssnittet för arbets ytan Databricks](/azure/databricks/libraries/#create-a-library)

2. Du kan hämta dBFS-sökvägen till biblioteket som lagts till med hjälp av användar gränssnittet genom att använda [DATABRICKS CLI](/azure/databricks/dev-tools/cli/#install-the-cli).

   Vanligt vis lagras jar-biblioteken under dBFS:/FileStore/jar v7 när du använder användar gränssnittet. Du kan visa alla via CLI: *databricks FS LS dBFS:/FileStore/Job-Jars*

### <a name="or-you-can-use-the-databricks-cli"></a>Du kan också använda Databricks CLI:

1. Följ [Kopiera biblioteket med DATABRICKS CLI](/azure/databricks/dev-tools/cli/#copy-a-file-to-dbfs)

2. Använda Databricks CLI [(installations steg)](/azure/databricks/dev-tools/cli/#install-the-cli)

   Till exempel för att kopiera en JAR-till-dBFS: `dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar`

## <a name="next-steps"></a>Nästa steg

Titta på [videon](https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player)om du vill visa en elva minuters introduktion och demonstration av den här funktionen.
