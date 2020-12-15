---
title: SQL Server lagrad procedur aktivitet
description: Lär dig hur du kan använda den SQL Server lagrade procedur aktiviteten för att anropa en lagrad procedur i en Azure SQL Database eller Azure Synapse Analytics från en Data Factory pipeline.
services: data-factory
documentationcenter: ''
ms.assetid: 1c46ed69-4049-44ec-9b46-e90e964a4a8e
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/10/2018
author: nabhishek
ms.author: abnarain
manager: anandsub
robots: noindex
ms.openlocfilehash: f702633f6311d4ae4a79452e9b1c9176125c56f9
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97508412"
---
# <a name="sql-server-stored-procedure-activity"></a>SQL Server lagrad procedur aktivitet
> [!div class="op_single_selector" title1="Omvandlings aktiviteter"]
> * [Hive-aktivitet](data-factory-hive-activity.md)
> * [Aktivitet i gris](data-factory-pig-activity.md)
> * [MapReduce-aktivitet](data-factory-map-reduce.md)
> * [Hadoop streaming-aktivitet](data-factory-hadoop-streaming-activity.md)
> * [Spark-aktivitet](data-factory-spark.md)
> * [Batch-körningsaktivitet i Azure Machine Learning Studio (klassisk)](data-factory-azure-ml-batch-execution-activity.md)
> * [Uppdateringsresursaktivitet i Azure Machine Learning Studio (klassisk)](data-factory-azure-ml-update-resource-activity.md)
> * [Lagrad proceduraktivitet](data-factory-stored-proc-activity.md)
> * [Data Lake Analytics U-SQL-aktivitet](data-factory-usql-activity.md)
> * [Anpassad .NET-aktivitet](data-factory-use-custom-activities.md)

> [!NOTE]
> Den här artikeln gäller för version 1 av Azure Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [transformera data med hjälp av lagrade procedur aktiviteter i Data Factory](../transform-data-using-stored-procedure.md).

## <a name="overview"></a>Översikt
Du använder data omvandlings aktiviteter i en Data Factory [pipeline](data-factory-create-pipelines.md) för att transformera och bearbeta rå data i förutsägelser och insikter. Den lagrade procedur aktiviteten är en av de omvandlings aktiviteter som Data Factory stöder. Den här artikeln bygger på artikeln [data omvandlings aktiviteter](data-factory-data-transformation-activities.md) , som visar en allmän översikt över Datatransformeringen och de omvandlings aktiviteter som stöds i Data Factory.

Du kan använda den lagrade procedur aktiviteten för att anropa en lagrad procedur i något av följande data lager i företaget eller på en virtuell Azure-dator (VM):

- Azure SQL Database
- Azure Synapse Analytics
- SQL Server databas. Om du använder SQL Server installerar du Data Management Gateway på samma dator som är värd för databasen eller på en annan dator som har åtkomst till databasen. Data Management Gateway är en komponent som ansluter data källor lokalt/på virtuella Azure-datorer med moln tjänster på ett säkert och hanterat sätt. Mer information finns i [Data Management Gateway](data-factory-data-management-gateway.md) artikel.

> [!IMPORTANT]
> När du kopierar data till Azure SQL Database eller SQL Server kan du konfigurera **SqlSink** i kopierings aktiviteten så att en lagrad procedur anropas med hjälp av egenskapen **sqlWriterStoredProcedureName** . Mer information finns i [anropa lagrad procedur från kopierings aktivitet](data-factory-invoke-stored-procedure-from-copy-activity.md). Mer information om egenskapen finns i följande artiklar om koppling: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties) [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties). Det går inte att anropa en lagrad procedur medan data kopieras till Azure Synapse Analytics med hjälp av en kopierings aktivitet. Men du kan använda den lagrade procedur aktiviteten för att anropa en lagrad procedur i Azure Synapse Analytics.
>
> När du kopierar data från Azure SQL Database eller SQL Server eller Azure Synapse Analytics kan du konfigurera **SqlSource** i kopierings aktivitet för att anropa en lagrad procedur för att läsa data från käll databasen med hjälp av egenskapen **sqlReaderStoredProcedureName** . Mer information finns i följande artiklar om koppling: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties), [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

I följande genom gång används den lagrade procedur aktiviteten i en pipeline för att anropa en lagrad procedur i Azure SQL Database.

## <a name="walkthrough"></a>Genomgång
### <a name="sample-table-and-stored-procedure"></a>Exempel tabell och lagrad procedur
1. Skapa följande **tabell** i din Azure SQL Database med SQL Server Management Studio eller något annat verktyg som du är van vid. Kolumnen datumtidsstämpel är datum och tid då motsvarande ID genereras.

    ```SQL
    CREATE TABLE dbo.sampletable
    (
        Id uniqueidentifier,
        datetimestamp nvarchar(127)
    )
    GO

    CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable(Id);
    GO
    ```
    ID är unikt identifierat och kolumnen datumtidsstämpel är datum och tid då motsvarande ID genereras.
    
    ![Exempeldata](./media/data-factory-stored-proc-activity/sample-data.png)

    I det här exemplet är den lagrade proceduren i en Azure SQL Database. Om den lagrade proceduren är i Azure Synapse Analytics och SQL Server Database är metoden liknande. För en SQL Server databas måste du installera en [Data Management Gateway](data-factory-data-management-gateway.md).
2. Skapa följande **lagrade procedur** som infogar data i i **sampletable**.

    ```SQL
    CREATE PROCEDURE usp_sample @DateTime nvarchar(127)
    AS

    BEGIN
        INSERT INTO [sampletable]
        VALUES (newid(), @DateTime)
    END
    ```

   > [!IMPORTANT]
   > **Namn** och **SKIFT** läge för parametern (datetime i det här exemplet) måste matcha parametern för den parameter som anges i pipeline/Activity-JSON. I definitionen för den lagrade proceduren kontrollerar du att **\@** används som prefix för parametern.

### <a name="create-a-data-factory"></a>Skapa en datafabrik
1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Klicka på **ny** på den vänstra menyn, klicka på **information + analys** och klicka på **Data Factory**.

    ![Ny data fabrik 1](media/data-factory-stored-proc-activity/new-data-factory.png)
3. På bladet **ny data fabrik** anger du **SProcDF** som namn. Azure Data Factory namn är **globalt unika**. Du måste ge prefixet namnet på data fabriken med ditt namn för att kunna skapa fabriken.

   ![Ny data fabrik 2](media/data-factory-stored-proc-activity/new-data-factory-blade.png)
4. Välj din **Azure-prenumeration**.
5. För **resurs grupp** utför du något av följande steg:
   1. Klicka på **Skapa nytt** och ange ett namn för resurs gruppen.
   2. Klicka på **Använd befintlig** och välj en befintlig resurs grupp.
6. Välj **plats** för datafabriken.
7. Välj **Fäst på instrument panelen** så att du kan se data fabriken på instrument panelen nästa gången du loggar in.
8. Klicka på **Skapa** på bladet **Ny datafabrik**.
9. Du ser att data fabriken skapas på **instrument panelen** för Azure Portal. När datafabriken har skapats visas datafabrikssidan med innehållet i datafabriken.

   ![Data Factory start sida](media/data-factory-stored-proc-activity/data-factory-home-page.png)

### <a name="create-an-azure-sql-linked-service"></a>Skapa en länkad Azure SQL-tjänst
När du har skapat data fabriken skapar du en länkad Azure SQL-tjänst som länkar din databas i Azure SQL Database, som innehåller tabellen sampletable och usp_sample lagrade proceduren, till din data fabrik.

1. Klicka på **författare och distribuera** på bladet **Data Factory** för **SProcDF** för att starta Data Factory redigeraren.
2. Klicka på **nytt data lager** i kommando fältet och välj **Azure SQL Database**. Du bör se JSON-skriptet för att skapa en länkad Azure SQL-tjänst i redigeraren.

   ![Nytt data lager 1](media/data-factory-stored-proc-activity/new-data-store.png)
3. I JSON-skriptet gör du följande ändringar:

   1. Ersätt `<servername>` med namnet på servern.
   2. Ersätt `<databasename>` med databasen där du skapade tabellen och den lagrade proceduren.
   3. Ersätt `<username@servername>` med det användar konto som har åtkomst till databasen.
   4. Ersätt `<password>` med lösen ordet för användar kontot.

      ![Nytt data lager 2](media/data-factory-stored-proc-activity/azure-sql-linked-service.png)
4. Klicka på **distribuera** i kommando fältet för att distribuera den länkade tjänsten. Bekräfta att du ser AzureSqlLinkedService i trädvyn till vänster.

    ![trädvy med länkad tjänst 1](media/data-factory-stored-proc-activity/tree-view.png)

### <a name="create-an-output-dataset"></a>Skapa en datauppsättning för utdata
Du måste ange en data uppsättning för utdata för en lagrad procedur aktivitet även om den lagrade proceduren inte skapar några data. Det beror på att det är den utgående data uppsättningen som styr aktivitetens schema (hur ofta aktiviteten körs per timme, dagligen osv.). Data uppsättningen för utdata måste använda en **länkad tjänst** som refererar till en Azure SQL Database eller Azure Synapse Analytics eller en SQL Server databas där du vill att den lagrade proceduren ska köras. Data uppsättningen för utdata kan fungera som ett sätt att skicka resultatet av den lagrade proceduren för efterföljande bearbetning av en annan aktivitet ([länkning av aktiviteter](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) i pipelinen. Data Factory skriver dock inte automatiskt utdata från en lagrad procedur till den här data uppsättningen. Det är den lagrade proceduren som skriver till en SQL-tabell som den resulterande data uppsättningen pekar på. I vissa fall kan data uppsättningen för utdata vara en **dummy-datauppsättning** (en data uppsättning som pekar på en tabell som inte faktiskt innehåller utdata från den lagrade proceduren). Denna dummy-datauppsättning används endast för att ange schemat för körning av den lagrade procedur aktiviteten.

1. Klicka på **... Mer information** om verktygsfältet får du genom att klicka på **ny data uppsättning** och sedan på **Azure SQL**. **Ny data mängd** i kommando fältet och välj **Azure SQL**.

    ![trädvy med länkad tjänst 2](media/data-factory-stored-proc-activity/new-dataset.png)
2. Kopiera/klistra in följande JSON-skript i JSON-redigeraren.

    ```JSON
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": {
                "tableName": "sampletable"
            },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
3. Klicka på **distribuera** i kommando fältet för att distribuera data uppsättningen. Bekräfta att du ser data uppsättningen i trädvyn.

    ![trädvy med länkade tjänster](media/data-factory-stored-proc-activity/tree-view-2.png)

### <a name="create-a-pipeline-with-sqlserverstoredprocedure-activity"></a>Skapa en pipeline med SqlServerStoredProcedure-aktivitet
Nu ska vi skapa en pipeline med en lagrad procedur aktivitet.

Observera följande egenskaper:

- Egenskapen **Type** har angetts till **SqlServerStoredProcedure**.
- **StoredProcedureName** i typ egenskaper anges till **usp_sample** (namnet på den lagrade proceduren).
- Avsnittet **storedProcedureParameters** innehåller en parameter med namnet **datetime**. Namn och Skift läge för parametern i JSON måste matcha namn och Skift läge för parametern i definitionen för den lagrade proceduren. Om du behöver skicka null för en parameter använder du syntaxen: `"param1": null` (alla gemener).

1. Klicka på **... Mer** i kommando fältet och klicka på **ny pipeline**.
2. Kopiera/klistra in följande JSON-kodfragment:

    ```JSON
    {
        "name": "SprocActivitySamplePipeline",
        "properties": {
            "activities": [
                {
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {
                        "storedProcedureName": "usp_sample",
                        "storedProcedureParameters": {
                            "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)"
                        }
                    },
                    "outputs": [
                        {
                            "name": "sprocsampleout"
                        }
                    ],
                    "scheduler": {
                        "frequency": "Hour",
                        "interval": 1
                    },
                    "name": "SprocActivitySample"
                }
            ],
            "start": "2017-04-02T00:00:00Z",
            "end": "2017-04-02T05:00:00Z",
            "isPaused": false
        }
    }
    ```
3. Distribuera pipelinen genom att klicka på **distribuera** i verktygsfältet.

### <a name="monitor-the-pipeline"></a>Övervaka pipeline
1. Klicka på **X** för att stänga bladen i Data Factory-redigeraren och för att gå tillbaka till Data Factory-bladet och klicka på **Diagram**.

    ![diagram panel 1](media/data-factory-stored-proc-activity/data-factory-diagram-tile.png)
2. I **diagramvyn** visas en översikt över pipelines och data uppsättningar som används i den här självstudien.

    ![diagram panel 2](media/data-factory-stored-proc-activity/data-factory-diagram-view.png)
3. I diagramvyn dubbelklickar du på data uppsättningen `sprocsampleout` . Du ser sektorerna i klart läge. Det bör finnas fem segment eftersom en sektor skapas för varje timme mellan start tiden och slut tiden från JSON.

    ![diagram panel 3](media/data-factory-stored-proc-activity/data-factory-slices.png)
4. När en sektor har statusen **klar** kör du en `select * from sampletable` fråga mot databasen för att kontrol lera att data har infogats i tabellen med den lagrade proceduren.

   ![Utdata](./media/data-factory-stored-proc-activity/output.png)

   Mer information om övervakning Azure Data Factory pipelines finns i [övervaka pipelinen](data-factory-monitor-manage-pipelines.md) .

## <a name="specify-an-input-dataset"></a>Ange en indata-datauppsättning
I genom gången har den lagrade procedur aktiviteten inga indata-datauppsättningar. Om du anger en indata-datauppsättning körs inte den lagrade procedur aktiviteten förrän segmentet för indata-dataset är tillgängligt (i klart läge). Data uppsättningen kan vara en extern data uppsättning (som inte produceras av en annan aktivitet i samma pipeline) eller en intern data uppsättning som skapas av en överordnad aktivitet (den aktivitet som körs före den här aktiviteten). Du kan ange flera indata-datauppsättningar för aktiviteten lagrad procedur. Om du gör det körs den lagrade procedur aktiviteten bara när alla segment för indata-datauppsättning är tillgängliga (i klart läge). Det går inte att konsumera indata-dataset i den lagrade proceduren som en parameter. Den används endast för att kontrol lera beroendet innan den lagrade procedur aktiviteten startas.

## <a name="chaining-with-other-activities"></a>Länkning med andra aktiviteter
Om du vill länka en överordnad aktivitet med den här aktiviteten anger du utdata från den överordnade aktiviteten som indata för den här aktiviteten. När du gör det körs inte den lagrade procedur aktiviteten förrän den överordnade aktiviteten har slutförts och utdata-datauppsättningen för den överordnade aktiviteten är tillgänglig (i klar status). Du kan ange utdata från data uppsättningar för flera överordnade aktiviteter som indata-datauppsättningar för aktiviteten lagrad procedur. När du gör det körs den lagrade procedur aktiviteten bara när alla segment för indata-datauppsättning är tillgängliga.

I följande exempel är resultatet av kopierings aktiviteten: OutputDataset, som är en indata från den lagrade procedur aktiviteten. Den lagrade procedur aktiviteten körs därför inte förrän kopierings aktiviteten har slutförts och OutputDataset-sektorn är tillgänglig (i klart läge). Om du anger flera indata-datauppsättningar körs inte den lagrade procedur aktiviteten förrän alla segment för indata-dataset är tillgängliga (i klart läge). Det går inte att använda indata-datauppsättningar direkt som parametrar i den lagrade procedur aktiviteten.

Mer information om länkning av aktiviteter finns i [flera aktiviteter i en pipeline](data-factory-create-pipelines.md#multiple-activities-in-a-pipeline)

```json
{
    "name": "ADFTutorialPipeline",
    "properties": {
        "description": "Copy data from a blob to blob",
        "activities": [
            {
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "BlobSource"
                    },
                    "sink": {
                        "type": "BlobSink",
                        "writeBatchSize": 0,
                        "writeBatchTimeout": "00:00:00"
                    }
                },
                "inputs": [ { "name": "InputDataset" } ],
                "outputs": [ { "name": "OutputDataset" } ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "executionPriorityOrder": "NewestFirst"
                },
                "name": "CopyFromBlobToSQL"
            },
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "SPSproc"
                },
                "inputs": [ { "name": "OutputDataset" } ],
                "outputs": [ { "name": "SQLOutputDataset" } ],
                "policy": {
                    "timeout": "01:00:00",
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "RunStoredProcedure"
            }
        ],
        "start": "2017-04-12T00:00:00Z",
        "end": "2017-04-13T00:00:00Z",
        "isPaused": false,
    }
}
```

På samma sätt kan du länka aktiviteten lagra procedur med **underordnade aktiviteter** (aktiviteter som körs efter att den lagrade proceduren har slutförts) genom att ange utdata-datauppsättningen för den lagrade procedur aktiviteten som indata för den underordnade aktiviteten i pipelinen.

> [!IMPORTANT]
> När du kopierar data till Azure SQL Database eller SQL Server kan du konfigurera **SqlSink** i kopierings aktiviteten så att en lagrad procedur anropas med hjälp av egenskapen **sqlWriterStoredProcedureName** . Mer information finns i [anropa lagrad procedur från kopierings aktivitet](data-factory-invoke-stored-procedure-from-copy-activity.md). Mer information om egenskapen finns i följande artiklar om koppling: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties) [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties).
> 
> När du kopierar data från Azure SQL Database eller SQL Server eller Azure Synapse Analytics kan du konfigurera **SqlSource** i kopierings aktivitet för att anropa en lagrad procedur för att läsa data från käll databasen med hjälp av egenskapen **sqlReaderStoredProcedureName** . Mer information finns i följande artiklar om koppling: [Azure SQL Database](data-factory-azure-sql-connector.md#copy-activity-properties), [SQL Server](data-factory-sqlserver-connector.md#copy-activity-properties), [Azure Synapse Analytics](data-factory-azure-sql-data-warehouse-connector.md#copy-activity-properties)

## <a name="json-format"></a>JSON-format
Här är JSON-formatet för att definiera en lagrad procedur aktivitet:

```JSON
{
    "name": "SQLSPROCActivity",
    "description": "description",
    "type": "SqlServerStoredProcedure",
    "inputs": [ { "name": "inputtable" } ],
    "outputs": [ { "name": "outputtable" } ],
    "typeProperties":
    {
        "storedProcedureName": "<name of the stored procedure>",
        "storedProcedureParameters":
        {
            "param1": "param1Value"
            …
        }
    }
}
```

Följande tabell beskriver de här JSON-egenskaperna:

| Egenskap | Beskrivning | Krävs |
| --- | --- | --- |
| name | Namn på aktiviteten |Ja |
| description |Text som beskriver vad aktiviteten används för |Nej |
| typ | Måste vara inställt på: **SqlServerStoredProcedure** | Ja |
| tillför | Valfritt. Om du anger en indata-datauppsättning måste den vara tillgänglig (i klar status) för att den lagrade procedur aktiviteten ska kunna köras. Det går inte att konsumera indata-dataset i den lagrade proceduren som en parameter. Den används endast för att kontrol lera beroendet innan den lagrade procedur aktiviteten startas. |Nej |
| utdata | Du måste ange en data uppsättning för utdata för en lagrad procedur aktivitet. Data uppsättningen för utdata anger **schemat** för aktiviteten för lagrad procedur (varje timme, varje vecka, varje månad osv.). <br/><br/>Data uppsättningen för utdata måste använda en **länkad tjänst** som refererar till en Azure SQL Database eller Azure Synapse Analytics eller en SQL Server databas där du vill att den lagrade proceduren ska köras. <br/><br/>Data uppsättningen för utdata kan fungera som ett sätt att skicka resultatet av den lagrade proceduren för efterföljande bearbetning av en annan aktivitet ([länkning av aktiviteter](data-factory-scheduling-and-execution.md#multiple-activities-in-a-pipeline) i pipelinen. Data Factory skriver dock inte automatiskt utdata från en lagrad procedur till den här data uppsättningen. Det är den lagrade proceduren som skriver till en SQL-tabell som den resulterande data uppsättningen pekar på. <br/><br/>I vissa fall kan data uppsättningen för utdata vara en **dummy-datauppsättning**, som endast används för att ange schemat för körning av den lagrade procedur aktiviteten. |Ja |
| storedProcedureName |Ange namnet på den lagrade proceduren i Azure SQL Database, Azure Synapse Analytics eller SQL Server som representeras av den länkade tjänsten som används i utdatatabellen. |Ja |
| storedProcedureParameters |Ange värden för parametrar för lagrad procedur. Om du behöver skicka null för en parameter använder du syntaxen: "param1": null (alla gemener). I följande exempel hittar du information om hur du använder den här egenskapen. |Nej |

## <a name="passing-a-static-value"></a>Överför ett statiskt värde
Nu ska vi överväga att lägga till en annan kolumn med namnet "scenario" i tabellen som innehåller ett statiskt värde med namnet "Document Sample".

![Exempel data 2](./media/data-factory-stored-proc-activity/sample-data-2.png)

**Partitionstabell**

```SQL
CREATE TABLE dbo.sampletable2
(
    Id uniqueidentifier,
    datetimestamp nvarchar(127),
    scenario nvarchar(127)
)
GO

CREATE CLUSTERED INDEX ClusteredID ON dbo.sampletable2(Id);
```

**Lagrad procedur:**

```SQL
CREATE PROCEDURE usp_sample2 @DateTime nvarchar(127) , @Scenario nvarchar(127)

AS

BEGIN
    INSERT INTO [sampletable2]
    VALUES (newid(), @DateTime, @Scenario)
END
```

Nu ska du skicka **scenario** -parametern och värdet från den lagrade procedur aktiviteten. Avsnittet **typeProperties** i föregående exempel ser ut som i följande kodfragment:

```JSON
"typeProperties":
{
    "storedProcedureName": "usp_sample",
    "storedProcedureParameters":
    {
        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
        "Scenario": "Document sample"
    }
}
```

**Data Factory data uppsättning:**

```JSON
{
    "name": "sprocsampleout2",
    "properties": {
        "published": false,
        "type": "AzureSqlTable",
        "linkedServiceName": "AzureSqlLinkedService",
        "typeProperties": {
            "tableName": "sampletable2"
        },
        "availability": {
            "frequency": "Hour",
            "interval": 1
        }
    }
}
```

**Data Factory pipeline**

```JSON
{
    "name": "SprocActivitySamplePipeline2",
    "properties": {
        "activities": [
            {
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "usp_sample2",
                    "storedProcedureParameters": {
                        "DateTime": "$$Text.Format('{0:yyyy-MM-dd HH:mm:ss}', SliceStart)",
                        "Scenario": "Document sample"
                    }
                },
                "outputs": [
                    {
                        "name": "sprocsampleout2"
                    }
                ],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                },
                "name": "SprocActivitySample"
            }
        ],
        "start": "2016-10-02T00:00:00Z",
        "end": "2016-10-02T05:00:00Z"
    }
}
```
