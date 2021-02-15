---
title: Transformera data med Databricks python
description: Lär dig hur du bearbetar eller transformerar data genom att köra en Databricks python-aktivitet i en Azure Data Factory pipeline.
ms.service: data-factory
ms.topic: conceptual
ms.date: 03/15/2018
author: dcstwh
ms.author: weetok
ms.custom: devx-track-python
ms.openlocfilehash: 49dfe11ceb01471e3b5afadd30259dcd63e7b82a
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373954"
---
# <a name="transform-data-by-running-a-python-activity-in-azure-databricks"></a>Transformera data genom att köra en python-aktivitet i Azure Databricks
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Databricks python-aktivitet i en [Data Factory pipeline](concepts-pipelines-activities.md) kör en python-fil i Azure Databricks klustret. Den här artikeln bygger på artikeln [data omvandlings aktiviteter](transform-data.md) , som visar en allmän översikt över Datatransformeringen och de omvandlings aktiviteter som stöds. Azure Databricks är en hanterad plattform för att köra Apache Spark.

Om du vill se en introduktion och demonstration av den här funktionen rekommenderar vi följande videoklipp (11 minuter):

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Execute-Jars-and-Python-scripts-on-Azure-Databricks-using-Data-Factory/player]

## <a name="databricks-python-activity-definition"></a>Databricks python-aktivitets definition

Här är exempel-JSON-definitionen för en Databricks python-aktivitet:

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksSparkPython",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "pythonFile": "dbfs:/docs/pi.py",
            "parameters": [
                "10"
            ],
            "libraries": [
                {
                    "pypi": {
                        "package": "tensorflow"
                    }
                }
            ]
        }
    }
}
```

## <a name="databricks-python-activity-properties"></a>Egenskaper för Databricks python-aktivitet

I följande tabell beskrivs de JSON-egenskaper som används i JSON-definitionen:

|Egenskap|Beskrivning|Obligatorisk|
|---|---|---|
|name|Namnet på aktiviteten i pipelinen.|Ja|
|beskrivning|Text som beskriver vad aktiviteten gör.|Inga|
|typ|Aktivitets typen är DatabricksSparkPython för Databricks python-aktivitet.|Ja|
|linkedServiceName|Namnet på den länkade Databricks-tjänst som python-aktiviteten körs på. Mer information om den här länkade tjänsten finns i artikeln [Compute-länkade tjänster](compute-linked-services.md) .|Ja|
|pythonFile|URI för python-filen som ska köras. Endast DBFS-sökvägar stöds.|Ja|
|parametrar|Kommando rads parametrar som ska skickas till python-filen. Detta är en sträng mat ris.|Inga|
|bibliotek|En lista med bibliotek som ska installeras i klustret som ska köra jobbet. Det kan vara en matris med <sträng, objekt>|Inga|

## <a name="supported-libraries-for-databricks-activities"></a>Bibliotek som stöds för databricks-aktiviteter

I ovanstående Databricks-aktivitets definition anger du dessa biblioteks typer: *jar*, *ägg*, *maven*, *pypi*, *cran*.

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

Mer information finns i [Databricks-dokumentationen](/azure/databricks/dev-tools/api/latest/libraries#managedlibrarieslibrary) för biblioteks typer.

## <a name="how-to-upload-a-library-in-databricks"></a>Ladda upp ett bibliotek i Databricks

### <a name="you-can-use-the-workspace-ui"></a>Du kan använda arbets ytans användar gränssnitt:

1. [Använda användar gränssnittet för arbets ytan Databricks](/azure/databricks/libraries/#create-a-library)

2. Du kan hämta dBFS-sökvägen till biblioteket som lagts till med hjälp av användar gränssnittet genom att använda [DATABRICKS CLI](/azure/databricks/dev-tools/cli/#install-the-cli).

   Vanligt vis lagras jar-biblioteken under dBFS:/FileStore/jar v7 när du använder användar gränssnittet. Du kan visa alla via CLI: *databricks FS LS dBFS:/FileStore/Job-Jars*

### <a name="or-you-can-use-the-databricks-cli"></a>Du kan också använda Databricks CLI:

1. Följ [Kopiera biblioteket med DATABRICKS CLI](/azure/databricks/dev-tools/cli/#copy-a-file-to-dbfs)

2. Använda Databricks CLI [(installations steg)](/azure/databricks/dev-tools/cli/#install-the-cli)

   Till exempel för att kopiera en JAR-till-dBFS: `dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar`
