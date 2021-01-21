---
title: Omvandla data
description: Transformera data eller bearbeta data i Azure Data Factory att använda Hadoop, Azure Machine Learning Studio (klassisk) eller Azure Data Lake Analytics.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: shwang
ms.custom: seo-lt-2019
ms.date: 07/31/2018
ms.openlocfilehash: 43a035662cc76dc6de1de3fa990e06f2e00cfd66
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/21/2021
ms.locfileid: "98632333"
---
# <a name="transform-data-in-azure-data-factory"></a>Transformera data i Azure Data Factory

> [!div class="op_single_selector"]
> * [Mappa data flöde](data-flow-create.md)
> * [Hive](transform-data-using-hadoop-hive.md)  
> * [Pig](transform-data-using-hadoop-pig.md)  
> * [MapReduce](transform-data-using-hadoop-map-reduce.md)  
> * [HDInsight-direktuppspelning](transform-data-using-hadoop-streaming.md)
> * [HDInsight Spark](transform-data-using-spark.md)
> * [Azure Machine Learning Studio (klassisk)](transform-data-using-machine-learning.md) 
> * [Lagrad procedur](transform-data-using-stored-procedure.md)
> * [Data Lake Analytics U-SQL](transform-data-using-data-lake-analytics.md)
> * [Databricks Notebook](transform-data-databricks-notebook.md)
> * [Databricks jar](transform-data-databricks-jar.md)
> * [Databricks python](transform-data-databricks-python.md)
> * [Anpassad .NET-](transform-data-using-dotnet-custom-activity.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="overview"></a>Översikt
Den här artikeln beskriver datatransformerings aktiviteter i Azure Data Factory som du kan använda för att transformera och bearbeta dina rå data till förutsägelser och insikter i stor skala. En omvandlings aktivitet körs i en dator miljö som Azure Databricks eller Azure HDInsight. Den innehåller länkar till artiklar med detaljerad information om varje omvandlings aktivitet.

Data Factory stöder följande data omvandlings aktiviteter som kan läggas till i [pipelines](concepts-pipelines-activities.md) som är individuellt eller länkade till en annan aktivitet.

## <a name="transform-natively-in-azure-data-factory-with-data-flows"></a>Transformera internt i Azure Data Factory med data flöden

### <a name="mapping-data-flows"></a>Mappa dataflöden

Mappning av data flöden är visuellt utformad med data transformationer i Azure Data Factory. Data flöden gör det möjligt för data tekniker att utveckla grafisk data omvandlings logik utan att skriva kod. De resulterande data flödena körs som aktiviteter i Azure Data Factory pipelines som använder uppskalade Spark-kluster. Data flödes aktiviteter kan användas via befintliga Data Factory schemaläggnings-, kontroll-, flödes-och övervaknings funktioner. Mer information finns i [mappa data flöden](concepts-data-flow-overview.md).

### <a name="data-wrangling"></a>Data datatransformering

Power Query i Azure Data Factory möjliggör data datatransformering i moln skala, vilket gör att du kan göra kod fria data förberedelser i moln skala upprepade gånger. Data datatransformering integreras med [Power Query online](/power-query/) och gör Power Query M-funktioner tillgängliga för data datatransformering i moln skala via Spark-körning. Mer information finns i [data datatransformering i ADF](wrangling-overview.md).

## <a name="external-transformations"></a>Externa omvandlingar

Alternativt kan du manuellt koda omvandlingar och hantera den externa beräknings miljön.

### <a name="hdinsight-hive-activity"></a>HDInsight Hive-aktivitet
HDInsight Hive-aktiviteten i en Data Factory pipelinen kör Hive-frågor på ditt eget eller Windows/Linux-baserade HDInsight-kluster på begäran. Se [Hive-aktivitets](transform-data-using-hadoop-hive.md) artikeln för information om den här aktiviteten. 

### <a name="hdinsight-pig-activity"></a>HDInsight gris-aktivitet
HDInsight gris-aktiviteten i en Data Factory pipeline kör gris-frågor på ditt eget eller Windows/Linux-baserade HDInsight-kluster på begäran. Mer information om den här aktiviteten finns i artikeln om [aktivitet i gris](transform-data-using-hadoop-pig.md) . 

### <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce-aktivitet
HDInsight MapReduce-aktiviteten i en Data Factory pipeline kör MapReduce-program på ditt eget eller Windows/Linux-baserade HDInsight-kluster på begäran. Mer information om den här aktiviteten finns i artikeln om [MapReduce-aktiviteter](transform-data-using-hadoop-map-reduce.md) .

### <a name="hdinsight-streaming-activity"></a>Aktivitet för HDInsight-direktuppspelning
HDInsight streaming-aktiviteten i en Data Factory pipeline kör Hadoop streaming-program på ditt eget eller Windows/Linux-baserade HDInsight-kluster på begäran. Mer information om den här aktiviteten finns i [aktivitet för HDInsight-direktuppspelning](transform-data-using-hadoop-streaming.md) .

### <a name="hdinsight-spark-activity"></a>HDInsight Spark-aktivitet
HDInsight Spark-aktiviteten i en Data Factory pipeline kör Spark-program i ditt eget HDInsight-kluster. Mer information finns i [anropa Spark-program från Azure Data Factory](transform-data-using-spark.md). 

### <a name="azure-machine-learning-studio-classic-activities"></a>Azure Machine Learning Studio (klassiska) aktiviteter
Med Azure Data Factory kan du enkelt skapa pipelines som använder en publicerad Azure Machine Learning Studio (klassisk) webb tjänst för förutsägelse analys. Med hjälp av [aktiviteten kör batch-körning](transform-data-using-machine-learning.md) i en Azure Data Factory pipeline kan du anropa en Studio (klassisk) webb tjänst för att göra förutsägelser för data i batch.

Med tiden måste förutsägande modeller i de Studio (klassiska) bedömnings experimenten omtränas med nya data uppsättningar för indata. När du är färdig med omträningen vill du uppdatera bedömnings-webbtjänsten med den omarbetade Machine Learning-modellen. Du kan använda [aktiviteten uppdatera resurs](update-machine-learning-models.md) för att uppdatera webb tjänsten med den nyligen utbildade modellen.  

Se [använda Azure Machine Learning Studio (klassiska) aktiviteter](transform-data-using-machine-learning.md) för information om de här Studio-aktiviteterna (klassiska). 

### <a name="stored-procedure-activity"></a>Lagrad procedur aktivitet
Du kan använda aktiviteten SQL Server lagrad procedur i en Data Factory pipeline för att anropa en lagrad procedur i något av följande data lager: Azure SQL Database, Azure Synapse Analytics, SQL Server databas i ditt företag eller en virtuell Azure-dator. Mer information finns i artikeln om [lagrade procedur aktiviteter](transform-data-using-stored-procedure.md) .  

### <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL-aktivitet
Data Lake Analytics U-SQL-aktivitet kör ett U-SQL-skript i ett Azure Data Lake Analytics-kluster. Mer information finns i artikeln [data analys U-SQL-aktivitet](transform-data-using-data-lake-analytics.md) . 

### <a name="databricks-notebook-activity"></a>Databricks Notebook-aktivitet

Azure Databricks Notebook-aktiviteten i en Data Factory-pipeline kör en Databricks-anteckningsbok i din Azure Databricks-arbetsyta. Azure Databricks är en hanterad plattform för att köra Apache Spark. Se [transformera data genom att köra en Databricks Notebook](transform-data-databricks-notebook.md).

### <a name="databricks-jar-activity"></a>Databricks jar-aktivitet

Azure Databricks jar-aktivitet i en Data Factory-pipeline kör en spark-jar i ditt Azure Databricks-kluster. Azure Databricks är en hanterad plattform för att köra Apache Spark. Se [transformera data genom att köra en jar-aktivitet i Azure Databricks](transform-data-databricks-jar.md).

### <a name="databricks-python-activity"></a>Databricks python-aktivitet

Azure Databricks python-aktivitet i en Data Factory pipeline kör en python-fil i Azure Databricks klustret. Azure Databricks är en hanterad plattform för att köra Apache Spark. Se [transformera data genom att köra en python-aktivitet i Azure Databricks](transform-data-databricks-python.md).

### <a name="custom-activity"></a>Anpassad aktivitet
Om du behöver transformera data på ett sätt som inte stöds av Data Factory, kan du skapa en anpassad aktivitet med din egen data bearbetnings logik och använda aktiviteten i pipelinen. Du kan konfigurera den anpassade .NET-aktiviteten att köras med hjälp av antingen en Azure Batch tjänst eller ett Azure HDInsight-kluster. Mer information finns i artikeln om att [använda anpassade aktiviteter](transform-data-using-dotnet-custom-activity.md) . 

Du kan skapa en anpassad aktivitet som kör R-skript i ditt HDInsight-kluster med R installerat. Se [Run R Script using Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample) (Köra R-skript med Azure Data Factory). 

### <a name="compute-environments"></a>Beräknings miljöer
Du skapar en länkad tjänst för beräknings miljön och använder sedan den länkade tjänsten när du definierar en Transformations aktivitet. Det finns två typer av beräknings miljöer som stöds av Data Factory. 

- **På begäran**: i det här fallet hanteras dator miljön fullständigt av Data Factory. Den skapas automatiskt av Data Factory tjänsten innan ett jobb skickas för att bearbeta data och tas bort när jobbet har slutförts. Du kan konfigurera och kontrol lera detaljerade inställningar för beräknings miljön på begäran för jobb körning, kluster hantering och start åtgärder. 
- **Ta** med dig: i det här fallet kan du registrera din egen dator miljö (till exempel HDInsight-kluster) som en länkad tjänst i Data Factory. Dator miljön hanteras av dig och den Data Factory tjänsten använder den för att köra aktiviteterna. 

Se artikeln om att beräkna [länkade tjänster](compute-linked-services.md) för att lära dig mer om beräknings tjänster som stöds av Data Factory. 

## <a name="next-steps"></a>Nästa steg
I följande självstudie finns ett exempel på hur du använder en Transformations aktivitet: [Självstudier: transformera data med Spark](tutorial-transform-data-spark-powershell.md)
