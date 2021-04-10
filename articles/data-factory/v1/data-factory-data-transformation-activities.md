---
title: 'Data omvandling: bearbeta & transformera data '
description: Lär dig hur du omformar data eller bearbetar data i Azure Data Factory att använda Hadoop, Azure Machine Learning Studio (klassisk) eller Azure Data Lake Analytics.
author: dcstwh
ms.author: weetok
ms.reviewer: jburchel
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: 422acbaab097182b800e3bad35b0121284db9cd8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104782936"
---
# <a name="transform-data-in-azure-data-factory-version-1"></a>Transformera data i Azure Data Factory version 1
> [!div class="op_single_selector"]
> * [Hive](data-factory-hive-activity.md)  
> * [Pig](data-factory-pig-activity.md)  
> * [MapReduce](data-factory-map-reduce.md)  
> * [Hadoop Streaming](data-factory-hadoop-streaming-activity.md)
> * [Azure Machine Learning Studio (klassisk)](data-factory-azure-ml-batch-execution-activity.md) 
> * [Lagrad procedur](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL](data-factory-usql-activity.md)
> * [Anpassad .NET-](data-factory-use-custom-activities.md)

## <a name="overview"></a>Översikt
> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [data omvandlings aktiviteter i Data Factory](../transform-data.md).

Den här artikeln beskriver datatransformerings aktiviteter i Azure Data Factory som du kan använda för att omvandla och bearbeta dina rå data till förutsägelser och insikter. En omvandlings aktivitet körs i en dator miljö, till exempel Azure HDInsight-kluster eller en Azure Batch. Den innehåller länkar till artiklar med detaljerad information om varje omvandlings aktivitet.

Data Factory stöder följande data omvandlings aktiviteter som kan läggas till i [pipelines](data-factory-create-pipelines.md) som är individuellt eller länkade till en annan aktivitet.

> [!NOTE]
> En genom gång med stegvisa instruktioner finns i [skapa en pipeline med Hive-omvandlings](data-factory-build-your-first-pipeline.md) artikel.  
> 
> 

## <a name="hdinsight-hive-activity"></a>HDInsight Hive-aktivitet
HDInsight Hive-aktiviteten i en Data Factory pipelinen kör Hive-frågor på ditt eget eller Windows/Linux-baserade HDInsight-kluster på begäran. Se [Hive-aktivitets](data-factory-hive-activity.md) artikeln för information om den här aktiviteten. 

## <a name="hdinsight-pig-activity"></a>HDInsight gris-aktivitet
HDInsight gris-aktiviteten i en Data Factory pipeline kör gris-frågor på ditt eget eller Windows/Linux-baserade HDInsight-kluster på begäran. Mer information om den här aktiviteten finns i artikeln om [aktivitet i gris](data-factory-pig-activity.md) . 

## <a name="hdinsight-mapreduce-activity"></a>HDInsight MapReduce-aktivitet
HDInsight MapReduce-aktiviteten i en Data Factory pipeline kör MapReduce-program på ditt eget eller Windows/Linux-baserade HDInsight-kluster på begäran. Mer information om den här aktiviteten finns i artikeln om [MapReduce-aktiviteter](data-factory-map-reduce.md) .

## <a name="hdinsight-streaming-activity"></a>Aktivitet för HDInsight-direktuppspelning
HDInsight streaming-aktiviteten i en Data Factory pipeline kör Hadoop streaming-program på ditt eget eller Windows/Linux-baserade HDInsight-kluster på begäran. Mer information om den här aktiviteten finns i [aktivitet för HDInsight-direktuppspelning](data-factory-hadoop-streaming-activity.md) .

## <a name="hdinsight-spark-activity"></a>HDInsight Apache Spark-aktivitet
HDInsight Spark-aktiviteten i en Data Factory pipeline kör Spark-program i ditt eget HDInsight-kluster. Mer information finns i [anropa Spark-program från Azure Data Factory](data-factory-spark.md). 

## <a name="azure-machine-learning-studio-classic-activities"></a>Azure Machine Learning Studio (klassiska) aktiviteter
Med Azure Data Factory kan du enkelt skapa pipelines som använder en publicerad Azure Machine Learning Studio (klassisk) webb tjänst för förutsägelse analys. Med hjälp av [aktiviteten kör batch-körning](data-factory-azure-ml-batch-execution-activity.md#invoking-a-web-service-using-batch-execution-activity) i en Azure Data Factory pipeline kan du anropa en Studio (klassisk) webb tjänst för att göra förutsägelser för data i batch.

Med tiden måste förutsägande modeller i de Studio (klassiska) bedömnings experimenten omtränas med nya data uppsättningar för indata. När du är färdig med omträningen vill du uppdatera bedömnings-webbtjänsten med den omarbetade Machine Learning-modellen. Du kan använda [aktiviteten uppdatera resurs](data-factory-azure-ml-batch-execution-activity.md#updating-models-using-update-resource-activity) för att uppdatera webb tjänsten med den nyligen utbildade modellen.  

Se [använda Azure Machine Learning Studio (klassiska) aktiviteter](data-factory-azure-ml-batch-execution-activity.md) för information om de här Studio-aktiviteterna (klassiska). 

## <a name="stored-procedure-activity"></a>Lagrad procedur aktivitet
Du kan använda aktiviteten SQL Server lagrad procedur i en Data Factory pipeline för att anropa en lagrad procedur i något av följande data lager: Azure SQL Database, Azure Synapse Analytics, SQL Server databas i ditt företag eller en virtuell Azure-dator. Mer information finns i artikeln om [lagrade procedur aktiviteter](data-factory-stored-proc-activity.md) .  

## <a name="data-lake-analytics-u-sql-activity"></a>Data Lake Analytics U-SQL-aktivitet
Data Lake Analytics U-SQL-aktivitet kör ett U-SQL-skript i ett Azure Data Lake Analytics-kluster. Mer information finns i artikeln [data analys U-SQL-aktivitet](data-factory-usql-activity.md) . 

## <a name="net-custom-activity"></a>.NET-anpassad aktivitet
Om du behöver transformera data på ett sätt som inte stöds av Data Factory, kan du skapa en anpassad aktivitet med din egen data bearbetnings logik och använda aktiviteten i pipelinen. Du kan konfigurera den anpassade .NET-aktiviteten att köras med hjälp av antingen en Azure Batch tjänst eller ett Azure HDInsight-kluster. Mer information finns i artikeln om att [använda anpassade aktiviteter](data-factory-use-custom-activities.md) . 

Du kan skapa en anpassad aktivitet som kör R-skript i ditt HDInsight-kluster med R installerat. Se [Run R Script using Azure Data Factory](https://github.com/Azure/Azure-DataFactory/tree/master/SamplesV1/RunRScriptUsingADFSample) (Köra R-skript med Azure Data Factory). 

## <a name="compute-environments"></a>Beräknings miljöer
Du skapar en länkad tjänst för beräknings miljön och använder sedan den länkade tjänsten när du definierar en Transformations aktivitet. Det finns två typer av beräknings miljöer som stöds av Data Factory. 

1. **På begäran**: i det här fallet hanteras dator miljön fullständigt av Data Factory. Den skapas automatiskt av Data Factory tjänsten innan ett jobb skickas för att bearbeta data och tas bort när jobbet har slutförts. Du kan konfigurera och kontrol lera detaljerade inställningar för beräknings miljön på begäran för jobb körning, kluster hantering och start åtgärder. 
2. **Ta** med dig: i det här fallet kan du registrera din egen dator miljö (till exempel HDInsight-kluster) som en länkad tjänst i Data Factory. Dator miljön hanteras av dig och den Data Factory tjänsten använder den för att köra aktiviteterna. 

Se artikeln om att beräkna [länkade tjänster](data-factory-compute-linked-services.md) för att lära dig mer om beräknings tjänster som stöds av Data Factory. 

## <a name="summary"></a>Sammanfattning
Azure Data Factory stöder följande data omvandlings aktiviteter och beräknings miljöerna för aktiviteterna. Transformations aktiviteterna kan läggas till i pipelines som är individuellt eller länkade till en annan aktivitet.

| Datatransformeringsaktivitet | Compute-miljö |
|:--- |:--- |
| [Hive](data-factory-hive-activity.md) |HDInsight [Hadoop] |
| [Pig](data-factory-pig-activity.md) |HDInsight [Hadoop] |
| [MapReduce](data-factory-map-reduce.md) |HDInsight [Hadoop] |
| [Hadoop Streaming](data-factory-hadoop-streaming-activity.md) |HDInsight [Hadoop] |
| [Azure Machine Learning Studio (klassisk) aktiviteter: batch-körning och uppdaterings resurs](data-factory-azure-ml-batch-execution-activity.md) |Azure VM |
| [Lagrad procedur](data-factory-stored-proc-activity.md) |Azure SQL, Azure Synapse Analytics eller SQL Server |
| [Data Lake Analytics U-SQL](data-factory-usql-activity.md) |Azure Data Lake Analytics |
| [DotNet](data-factory-use-custom-activities.md) |HDInsight [Hadoop] eller Azure Batch |

