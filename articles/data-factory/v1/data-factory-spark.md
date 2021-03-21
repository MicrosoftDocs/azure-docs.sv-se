---
title: Anropa Spark-program från Azure Data Factory
description: Lär dig hur du anropar Spark-program från en Azure Data Factory med hjälp av MapReduce-aktiviteten.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.openlocfilehash: a7fcccad039ff6d737e10cfc57ba97f914645a9d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100382675"
---
# <a name="invoke-spark-programs-from-azure-data-factory-pipelines"></a>Anropa Spark-program från Azure Data Factory pipelines

> [!div class="op_single_selector" title1="Omvandlings aktiviteter"]
> * [Hive-aktivitet](data-factory-hive-activity.md)
> * [Aktivitet i gris](data-factory-pig-activity.md)
> * [MapReduce-aktivitet](data-factory-map-reduce.md)
> * [Hadoop streaming-aktivitet](data-factory-hadoop-streaming-activity.md)
> * [Spark-aktivitet](data-factory-spark.md)
> * [Azure Machine Learning Studio (klassisk) batch execution Activity](data-factory-azure-ml-batch-execution-activity.md)
> * [Azure Machine Learning Studio (klassisk) uppdatera resurs aktivitet](data-factory-azure-ml-update-resource-activity.md)
> * [Lagrad procedur aktivitet](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-aktivitet](data-factory-usql-activity.md)
> * [.NET-anpassad aktivitet](data-factory-use-custom-activities.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Azure Data Factory, som är allmänt tillgänglig. Om du använder den aktuella versionen av tjänsten Data Factory, se [transformera data med hjälp av Apache Spark-aktiviteten i Data Factory](../transform-data-using-spark.md).

## <a name="introduction"></a>Introduktion
Spark-aktiviteten är en av de [data omvandlings aktiviteter](data-factory-data-transformation-activities.md) som stöds av Data Factory. Den här aktiviteten kör det angivna Spark-programmet på ditt Spark-kluster i Azure HDInsight.

> [!IMPORTANT]
> - Spark-aktiviteten stöder inte HDInsight Spark-kluster som använder Azure Data Lake Store som primär lagring.
> - Spark-aktiviteten stöder bara befintliga (dina egna) HDInsight Spark-kluster. Den har inte stöd för en länkad HDInsight-tjänst på begäran.

## <a name="walkthrough-create-a-pipeline-with-a-spark-activity"></a>Genom gång: skapa en pipeline med en spark-aktivitet
Här är de vanligaste stegen för att skapa en Data Factory-pipeline med en spark-aktivitet:

* Skapa en datafabrik.
* Skapa en Azure Storage länkad tjänst för att länka lagringen som är kopplad till ditt HDInsight Spark-kluster till data fabriken.
* Skapa en länkad HDInsight-tjänst för att länka ditt Spark-kluster i HDInsight till data fabriken.
* Skapa en data uppsättning som refererar till den länkade lagrings tjänsten. För närvarande måste du ange en data uppsättning för utdata för en aktivitet även om det inte finns några utdata som skapas.
* Skapa en pipeline med Spark-aktivitet som refererar till den länkade HDInsight-tjänsten som du skapade. Aktiviteten konfigureras med den data uppsättning som du skapade i föregående steg som en data uppsättning för utdata. Data uppsättningen för utdata är det som driver schemat (varje timme, varje dag). Därför måste du ange data uppsättningen för utdata även om aktiviteten inte faktiskt skapar utdata.

### <a name="prerequisites"></a>Förutsättningar
1. Skapa ett allmänt lagrings konto genom att följa instruktionerna i [skapa ett lagrings konto](../../storage/common/storage-account-create.md).

1. Skapa ett Spark-kluster i HDInsight genom att följa anvisningarna i självstudien [skapa ett Spark-kluster i HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Koppla lagrings kontot som du skapade i steg 1 med det här klustret.

1. Hämta och granska python-skriptfilen **test.py** finns på [https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py](https://adftutorialfiles.blob.core.windows.net/sparktutorial/test.py) .

1. Ladda upp **test.py** till mappen **pyFiles** i behållaren **adfspark** i Blob Storage. Skapa behållaren och mappen om de inte finns.

### <a name="create-a-data-factory"></a>Skapa en datafabrik
Gör så här för att skapa en datafabrik:

1. Logga in på [Azure-portalen](https://portal.azure.com/).

1. Välj **ny**  >  **data och analys**  >  **Data Factory**.

1. På bladet **ny data fabrik** , under **namn**, anger du **SparkDF**.

   > [!IMPORTANT]
   > Namnet på Azure Data Factory måste vara globalt unikt. Om du ser felet "Data Factory Name SparkDF är inte tillgängligt" ändrar du namnet på data fabriken. Använd till exempel yournameSparkDFdate och skapa data fabriken igen. Mer information om namngivningsregler finns i [Data Factory: namngivningsregler](data-factory-naming-rules.md).

1. Under **Prenumeration** väljer du den Azure-prenumeration där du vill att datafabriken ska skapas.

1. Välj en befintlig resurs grupp eller skapa en Azure-resurs grupp.

1. Markera kryssrutan **Fäst på instrumentpanelen**.

1. Välj **Skapa**.

   > [!IMPORTANT]
   > Om du vill skapa Data Factory instanser måste du vara medlem i rollen [Data Factory Contributor](../../role-based-access-control/built-in-roles.md#data-factory-contributor) på prenumerations-eller resurs grupps nivån.

1. Du ser data fabriken när den skapas på instrument panelen för Azure Portal.

1. När datafabriken har skapats visas sidan **Datafabrik** med innehållet i datafabriken. Om du inte ser **data fabriks** sidan väljer du panelen för din data fabrik på instrument panelen.

    ![Bladet Datafabrik](./media/data-factory-spark/data-factory-blade.png)

### <a name="create-linked-services"></a>Skapa länkade tjänster
I det här steget skapar du två länkade tjänster. En tjänst länkar ditt Spark-kluster till din data fabrik och den andra tjänsten länkar din lagring till din data fabrik.

#### <a name="create-a-storage-linked-service"></a>Skapa en länkad lagringstjänst
I det här steget länkar du ditt lagringskonto till datafabriken. En data uppsättning som du skapar i ett steg senare i den här genom gången refererar till den här länkade tjänsten. Den länkade HDInsight-tjänsten som du definierar i nästa steg avser även den här länkade tjänsten.

1. På bladet **data fabrik** väljer du **författare och distribution**. Data Factory redigeraren visas.

1. Välj **Nytt datalager** och välj **Azure Storage**.

   ![Nytt data lager](./media/data-factory-spark/new-data-store-azure-storage-menu.png)

1. JSON-skriptet som du använder för att skapa en länkad lagrings tjänst visas i redigeraren.

   ![AzureStorageLinkedService](./media/data-factory-build-your-first-pipeline-using-editor/azure-storage-linked-service.png)

1. Ersätt **konto namnet** och **konto nyckeln** med namnet och åtkomst nyckeln för ditt lagrings konto. Information om hur du hämtar din lagrings åtkomst nyckel finns i [Hantera åtkomst nycklar för lagrings konton](../../storage/common/storage-account-keys-manage.md).

1. Om du vill distribuera den länkade tjänsten väljer du **distribuera** i kommando fältet. Fönstret Draft-1 stängs när den länkade tjänsten har distribuerats. Du ser **AzureStorageLinkedService** i trädvyn till vänster.

#### <a name="create-an-hdinsight-linked-service"></a>Skapa en länkad HDInsight-tjänst
I det här steget skapar du en länkad HDInsight-tjänst för att länka ditt HDInsight Spark-kluster till data fabriken. HDInsight-klustret används för att köra Spark-programmet som anges i Spark-aktiviteten för pipelinen i det här exemplet.

1. I Data Factory redigeraren väljer du **fler**  >  **nya beräkning**  >  **HDInsight-kluster**.

    ![Skapa länkad HDInsight-tjänst](media/data-factory-spark/new-hdinsight-linked-service.png)

1. Kopiera och klistra in följande kodfragment till fönstret Draft-1. Utför följande steg i JSON-redigeraren:

    1. Ange URI: n för HDInsight Spark-klustret. Exempel: `https://<sparkclustername>.azurehdinsight.net/`.

    1. Ange namnet på den användare som har åtkomst till Spark-klustret.

    1. Ange lösenordet för användaren.

    1. Ange den länkade lagrings tjänsten som är associerad med HDInsight Spark-klustret. I det här exemplet är det AzureStorageLinkedService.

    ```json
    {
        "name": "HDInsightLinkedService",
        "properties": {
            "type": "HDInsight",
            "typeProperties": {
                "clusterUri": "https://<sparkclustername>.azurehdinsight.net/",
                "userName": "admin",
                "password": "**********",
                "linkedServiceName": "AzureStorageLinkedService"
            }
        }
    }
    ```

    > [!IMPORTANT]
    > - Spark-aktiviteten stöder inte HDInsight Spark-kluster som använder Azure Data Lake Store som primär lagring.
    > - Spark-aktiviteten stöder bara befintliga (dina egna) HDInsight Spark-kluster. Den har inte stöd för en länkad HDInsight-tjänst på begäran.

    Mer information om den länkade HDInsight-tjänsten finns i [HDInsight-länkad tjänst](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).

1. Om du vill distribuera den länkade tjänsten väljer du **distribuera** i kommando fältet.

### <a name="create-the-output-dataset"></a>Skapa datauppsättningen för utdata
Data uppsättningen för utdata är det som driver schemat (varje timme, varje dag). Därför måste du ange en data uppsättning för Spark-aktiviteten i pipelinen även om aktiviteten inte genererar några utdata. Det är valfritt att ange en indata-datauppsättning för aktiviteten.

1. I Data Factory redigeraren väljer du **mer**  >  **ny data uppsättning**  >  **Azure Blob Storage**.

1. Kopiera och klistra in följande kodfragment till fönstret Draft-1. JSON-kodfragmentet definierar en data uppsättning som kallas **OutputDataset**. Dessutom kan du ange att resultaten lagras i BLOB-behållaren med namnet **adfspark** och mappen med namnet **pyFiles/output**. Som tidigare nämnts är den här data uppsättningen en dummy-datauppsättning. Spark-programmet i det här exemplet genererar inga utdata. Avsnittet **tillgänglighet** anger att data uppsättningen för utdata skapas dagligen.

    ```json
    {
        "name": "OutputDataset",
        "properties": {
            "type": "AzureBlob",
            "linkedServiceName": "AzureStorageLinkedService",
            "typeProperties": {
                "fileName": "sparkoutput.txt",
                "folderPath": "adfspark/pyFiles/output",
                "format": {
                    "type": "TextFormat",
                    "columnDelimiter": "\t"
                }
            },
            "availability": {
                "frequency": "Day",
                "interval": 1
            }
        }
    }
    ```
1. Om du vill distribuera data uppsättningen väljer du **distribuera** i kommando fältet.


### <a name="create-a-pipeline"></a>Skapa en pipeline
I det här steget skapar du en pipeline med en HDInsightSpark-aktivitet. För närvarande är det utdatauppsättningen som skapar schemat. Därför måste du skapa en utdatauppsättning även om aktiviteten inte genererar några utdata. Om aktiviteten inte får några indata, kan du hoppa över att skapa indatauppsättningen. Därför anges ingen indata-datamängd i det här exemplet.

1. I Data Factory redigeraren väljer du **mer**  >  **ny pipeline**.

1. Ersätt skriptet i fönstret Draft-1 med följande skript:

    ```json
    {
        "name": "SparkPipeline",
        "properties": {
            "activities": [
                {
                    "type": "HDInsightSpark",
                    "typeProperties": {
                        "rootPath": "adfspark\\pyFiles",
                        "entryFilePath": "test.py",
                        "getDebugInfo": "Always"
                    },
                    "outputs": [
                        {
                            "name": "OutputDataset"
                        }
                    ],
                    "name": "MySparkActivity",
                    "linkedServiceName": "HDInsightLinkedService"
                }
            ],
            "start": "2017-02-05T00:00:00Z",
            "end": "2017-02-06T00:00:00Z"
        }
    }
    ```

    Observera följande punkter:

    1. Egenskapen **Type** har angetts till **HDInsightSpark**.

    1. Egenskapen **rootPath** anges till **adfspark \\ pyFiles** där adfspark är BLOB-behållaren och pyFiles är filmapp i den behållaren. I det här exemplet är Blob Storage det som är associerat med Spark-klustret. Du kan överföra filen till ett annat lagrings konto. Om du gör det skapar du en länkad lagrings tjänst för att länka lagrings kontot till data fabriken. Ange sedan namnet på den länkade tjänsten som ett värde för egenskapen **sparkJobLinkedService** . Mer information om den här egenskapen och andra egenskaper som stöds av Spark-aktiviteten finns i [Egenskaper för Spark-aktivitet](#spark-activity-properties).

    1. Egenskapen **entryFilePath** är inställd på **test.py**, vilket är python-filen.

    1. **GetDebugInfo** -egenskapen är inställd på **Always**, vilket innebär att loggfilerna alltid genereras (lyckade eller misslyckade).

       > [!IMPORTANT]
       > Vi rekommenderar att du inte anger den här egenskapen till `Always` i en produktions miljö om du inte felsöker ett problem.

    1. Avsnittet **utdata** har en data uppsättning för utdata. Du måste ange en data uppsättning för utdata även om Spark-programmet inte producerar några utdata. Den utgående data uppsättningen driver schemat för pipelinen (varje timme, varje dag).

    Mer information om vilka egenskaper som stöds av Spark-aktiviteten finns i avsnittet om [aktivitets egenskaper](#spark-activity-properties)för section Spark.

1. Om du vill distribuera pipelinen väljer du **distribuera** i kommando fältet.

### <a name="monitor-a-pipeline"></a>Övervaka en pipeline
1. På bladet **data fabrik** väljer du **övervaka & hantera** för att starta övervakningsprogrammet på en annan flik.

    ![Ikonen Övervaka och hantera](media/data-factory-spark/monitor-and-manage-tile.png)

1. Ändra **Start tids** filtret överst till **2/1/2017** och välj **Använd**.

1. Endast ett aktivitets fönster visas eftersom det bara finns en dag mellan start (2017-02-01) och slut tid (2017-02-02) för pipelinen. Bekräfta att data sektorn har statusen **klar** .

    ![Övervaka pipeline](media/data-factory-spark/monitor-and-manage-app.png)

1. I listan **aktivitets fönster** väljer du en aktivitets körning för att se information om den. Om det uppstår ett fel visas information om det i den högra rutan.

### <a name="verify-the-results"></a>Verifiera resultaten

1. Starta Jupyter Notebook för ditt HDInsight Spark-kluster genom att gå till `https://CLUSTERNAME.azurehdinsight.net/jupyter` . Du kan också öppna ett kluster instrument panel för ditt HDInsight Spark-kluster och sedan starta Jupyter Notebook.

1. Välj **ny**  >  **PySpark** för att starta en ny antecknings bok.

    ![Jupyter ny Notebook](media/data-factory-spark/jupyter-new-book.png)

1. Kör följande kommando genom att kopiera och klistra in texten och trycka på SKIFT + RETUR i slutet av den andra instruktionen:

    ```sql
    %%sql

    SELECT buildingID, (targettemp - actualtemp) AS temp_diff, date FROM hvac WHERE date = \"6/1/13\"
    ```
1. Bekräfta att du ser data från HVAC-tabellen.

    ![Frågeresultat för Jupyter](media/data-factory-spark/jupyter-notebook-results.png)

<!-- Removed bookmark #run-a-hive-query-using-spark-sql since it doesn't exist in the target article -->
Detaljerade anvisningar finns i avsnittet [köra en Spark SQL-fråga](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md).

### <a name="troubleshooting"></a>Felsökning
Eftersom du anger getDebugInfo till **Always** visas en logg-undermapp i mappen pyFiles i BLOB-behållaren. Logg filen i loggmappen innehåller ytterligare information. Den här logg filen är särskilt användbar när det uppstår ett fel. I en produktions miljö kanske du vill ställa in den på att **Miss lyckas**.

Gör så här för ytterligare fel sökning:


1. Gå till `https://<CLUSTERNAME>.azurehdinsight.net/yarnui/hn/cluster`.

    ![GRÄNSSNITTs program för garn](media/data-factory-spark/yarnui-application.png)

1. Välj **loggar** för ett av körnings försöken.

    ![Program sida](media/data-factory-spark/yarn-applications.png)

1. Följande ytterligare fel information visas på logg sidan:

    ![Logg fel](media/data-factory-spark/yarnui-application-error.png)

I följande avsnitt finns information om Data Factory-entiteter för att använda Spark-kluster och Spark-aktivitet i din data fabrik.

## <a name="spark-activity-properties"></a>Egenskaper för Spark-aktivitet
Här är exempel-JSON-definitionen för en pipeline med en spark-aktivitet:

```json
{
    "name": "SparkPipeline",
    "properties": {
        "activities": [
            {
                "type": "HDInsightSpark",
                "typeProperties": {
                    "rootPath": "adfspark\\pyFiles",
                    "entryFilePath": "test.py",
                    "arguments": [ "arg1", "arg2" ],
                    "sparkConfig": {
                        "spark.python.worker.memory": "512m"
                    },
                    "getDebugInfo": "Always"
                },
                "outputs": [
                    {
                        "name": "OutputDataset"
                    }
                ],
                "name": "MySparkActivity",
                "description": "This activity invokes the Spark program",
                "linkedServiceName": "HDInsightLinkedService"
            }
        ],
        "start": "2017-02-01T00:00:00Z",
        "end": "2017-02-02T00:00:00Z"
    }
}
```

I följande tabell beskrivs de JSON-egenskaper som används i JSON-definitionen.

| Egenskap | Beskrivning | Obligatorisk |
| -------- | ----------- | -------- |
| name | Namnet på aktiviteten i pipelinen. | Ja |
| beskrivning | Text som beskriver vad aktiviteten gör. | Inga |
| typ | Den här egenskapen måste anges till HDInsightSpark. | Ja |
| linkedServiceName | Namnet på den länkade HDInsight-tjänsten som Spark-programmet körs på. | Ja |
| rootPath | BLOB-behållaren och mappen som innehåller Spark-filen. Fil namnet är Skift läges känsligt. | Ja |
| entryFilePath | Relativ sökväg till rotmappen för Spark-koden/-paketet. | Ja |
| className | Programmets Java/Spark-huvud klass. | Inga |
| ogiltiga | En lista med kommando rads argument för Spark-programmet. | Inga |
| proxyUser | Användar kontot som ska personifieras för att köra Spark-programmet. | Inga |
| sparkConfig | Ange värden för konfigurations egenskaperna för Spark som anges i [Spark-konfigurationen: program egenskaper](https://spark.apache.org/docs/latest/configuration.html#available-properties). | Inga |
| getDebugInfo | Anger när Spark-loggfilerna kopieras till det lagrings utrymme som används av HDInsight-klustret (eller) som anges av sparkJobLinkedService. Tillåtna värden är none, Always eller Failure. Standardvärdet är none. | Inga |
| sparkJobLinkedService | Den länkade lagrings tjänsten som innehåller Spark-jobbets fil, beroenden och loggar. Om du inte anger något värde för den här egenskapen används det lagrings utrymme som är associerat med HDInsight-klustret. | Inga |

## <a name="folder-structure"></a>Mappstruktur
Spark-aktiviteten stöder inte ett infogat skript som gris-och Hive-aktiviteter. Spark-jobb är också mer utöknings bara än gris-/Hive-jobb. För Spark-jobb kan du tillhandahålla flera beroenden, till exempel jar-paket (placerade i Java-CLASSPATH), python-filer (placeras på PYTHONPATH) och andra filer.

Skapa följande mappstruktur i blob-lagringen som refereras av den länkade HDInsight-tjänsten. Ladda sedan upp beroende filer till lämpliga undermappar i rotmappen som representeras av **entryFilePath**. Du kan till exempel Ladda upp python-filer till undermappen pyFiles och jar-filer till undermappen jar v7 i rotmappen. Vid körning förväntar tjänsten Data Factory tjänsten följande mappstruktur i blob-lagringen:

| Sökväg | Beskrivning | Krävs | Typ |
| ---- | ----------- | -------- | ---- |
| . | Spark-jobbets rot Sök väg i den länkade lagrings tjänsten. | Ja | Mapp |
| &lt;användardefinierad &gt; | Den sökväg som pekar på post filen för Spark-jobbet. | Ja | Fil |
| ./jars | Alla filer i den här mappen överförs och placeras i Java-classpath för klustret. | Inga | Mapp |
| ./pyFiles | Alla filer i den här mappen överförs och placeras i PYTHONPATH i klustret. | Inga | Mapp |
| ./files | Alla filer i den här mappen överförs och placeras i den utförar arbets katalogen. | Inga | Mapp |
| ./archives | Alla filer i den här mappen är okomprimerade. | Inga | Mapp |
| ./logs | Mappen där loggar från Spark-klustret lagras.| Inga | Mapp |

Här är ett exempel på lagring som innehåller två Spark-jobbmallar i blob-lagringen som refereras av den länkade HDInsight-tjänsten:

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
