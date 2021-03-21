---
title: Transformera data med Databricks Notebook
description: Lär dig hur du bearbetar eller transformerar data genom att köra en Databricks Notebook i Azure Data Factory.
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: fea572c2e75f62b5e7e7b4634e37da348bdcdaf1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102183496"
---
# <a name="transform-data-by-running-a-databricks-notebook"></a>Transformera data genom att köra en Databricks Notebook
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Databricks Notebook-aktiviteten i en [Data Factory-pipeline](concepts-pipelines-activities.md) kör en Databricks-anteckningsbok i din Azure Databricks-arbetsyta. Den här artikeln bygger på artikeln [data omvandlings aktiviteter](transform-data.md) , som visar en allmän översikt över Datatransformeringen och de omvandlings aktiviteter som stöds. Azure Databricks är en hanterad plattform för att köra Apache Spark.

## <a name="databricks-notebook-activity-definition"></a>Aktivitets definition för Databricks Notebook

Här är exempel-JSON-definitionen för en Databricks Notebook-aktivitet:

```json
{
    "activity": {
        "name": "MyActivity",
        "description": "MyActivity description",
        "type": "DatabricksNotebook",
        "linkedServiceName": {
            "referenceName": "MyDatabricksLinkedservice",
            "type": "LinkedServiceReference"
        },
        "typeProperties": {
            "notebookPath": "/Users/user@example.com/ScalaExampleNotebook",
            "baseParameters": {
                "inputpath": "input/folder1/",
                "outputpath": "output/"
            },
            "libraries": [
                {
                "jar": "dbfs:/docs/library.jar"
                }
            ]
        }
    }
}
```

## <a name="databricks-notebook-activity-properties"></a>Egenskaper för Databricks Notebook-aktivitet

I följande tabell beskrivs de JSON-egenskaper som används i JSON-definitionen:

|Egenskap|Beskrivning|Obligatorisk|
|---|---|---|
|name|Namnet på aktiviteten i pipelinen.|Ja|
|beskrivning|Text som beskriver vad aktiviteten gör.|Inga|
|typ|Aktivitets typen är DatabricksNotebook för Databricks Notebook-aktivitet.|Ja|
|linkedServiceName|Namnet på den länkade Databricks-tjänsten som Databricks-anteckningsboken körs på. Mer information om den här länkade tjänsten finns i artikeln [Compute-länkade tjänster](compute-linked-services.md) .|Ja|
|notebookPath|Den absoluta sökvägen till antecknings boken som ska köras i Databricks-arbetsytan. Sökvägen måste börja med ett snedstreck.|Ja|
|baseParameters|En matris med Key-Value par. Du kan använda bas parametrar för varje aktivitets körning. Om antecknings boken använder en parameter som inte har angetts används standardvärdet från antecknings boken. Hitta mer information om parametrar i [Databricks Notebooks](https://docs.databricks.com/api/latest/jobs.html#jobsparampair).|Inga|
|bibliotek|En lista med bibliotek som ska installeras i klustret som ska köra jobbet. Det kan vara en matris med \<string, object> .|Inga|

## <a name="supported-libraries-for-databricks-activities"></a>Bibliotek som stöds för Databricks-aktiviteter

I Databricks-aktivitets definitionen anger du dessa biblioteks typer: *jar*, *ägg*, *WHL*, *maven*, *pypi*, *cran*.

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
            "whl": "dbfs:/mnt/libraries/mlflow-0.0.1.dev0-py2-none-any.whl"
        },
        {
            "whl": "dbfs:/mnt/libraries/wheel-libraries.wheelhouse.zip"
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

## <a name="passing-parameters-between-notebooks-and-data-factory"></a>Skicka parametrar mellan antecknings böcker och Data Factory

Du kan skicka data Factory-parametrar till antecknings böcker med egenskapen *baseParameters* i databricks-aktivitet.

I vissa fall kan du behöva skicka tillbaka vissa värden från antecknings boken till Data Factory, som kan användas för kontroll flöde (villkorliga kontroller) i Data Factory eller användas av underordnade aktiviteter (storleks gränsen är 2 MB).

1. I din antecknings bok kan du anropa [dbutils. Notebook. Exit ("returnValue")](/azure/databricks/notebooks/notebook-workflows#notebook-workflows-exit) och motsvarande "returnValue" kommer att returneras till Data Factory.

2. Du kan använda utdata i Data Factory genom att använda uttryck som `@{activity('databricks notebook activity name').output.runOutput}` . 

   > [!IMPORTANT]
   > Om du skickar JSON-objekt kan du hämta värden genom att lägga till egenskaps namn. Exempel: `@{activity('databricks notebook activity name').output.runOutput.PropertyName}`

## <a name="how-to-upload-a-library-in-databricks"></a>Ladda upp ett bibliotek i Databricks

### <a name="you-can-use-the-workspace-ui"></a>Du kan använda arbets ytans användar gränssnitt:

1. [Använda användar gränssnittet för arbets ytan Databricks](/azure/databricks/libraries/#create-a-library)

2. Du kan hämta dBFS-sökvägen till biblioteket som lagts till med hjälp av användar gränssnittet genom att använda [DATABRICKS CLI](/azure/databricks/dev-tools/cli/#install-the-cli).

   Vanligt vis lagras jar-biblioteken under dBFS:/FileStore/jar v7 när du använder användar gränssnittet. Du kan visa alla via CLI: *databricks FS LS dBFS:/FileStore/Job-Jars*

### <a name="or-you-can-use-the-databricks-cli"></a>Du kan också använda Databricks CLI:

1. Följ [Kopiera biblioteket med DATABRICKS CLI](/azure/databricks/dev-tools/cli/#copy-a-file-to-dbfs)

2. Använda Databricks CLI [(installations steg)](/azure/databricks/dev-tools/cli/#install-the-cli)

   Till exempel för att kopiera en JAR-till-dBFS: `dbfs cp SparkPi-assembly-0.1.jar dbfs:/docs/sparkpi.jar`
