---
title: Kopiera en tabell stegvis med PowerShell
description: I den här självstudien skapar du en Azure Data Factory pipeline som stegvis kopierar data från en Azure SQL-databas till Azure Blob Storage.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-dt-2019
ms.date: 02/18/2021
ms.openlocfilehash: f4cb4807e6f2620bb76649fc7c7dcce7363cf4a4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101740995"
---
# <a name="incrementally-load-data-from-azure-sql-database-to-azure-blob-storage-using-powershell"></a>Läs in data stegvis från Azure SQL Database till Azure Blob Storage med hjälp av PowerShell

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här självstudien använder du Azure Data Factory för att skapa en pipeline som läser in delta data från en tabell i Azure SQL Database till Azure Blob Storage.

I den här självstudiekursen får du göra följande:

> [!div class="checklist"]
> * Förbered datalagringen för att lagra värdet för vattenstämpeln.
> * Skapa en datafabrik.
> * Skapa länkade tjänster.
> * Skapa datauppsättningar för källa, mottagare och vattenstämpel.
> * Skapa en pipeline.
> * Köra en pipeline.
> * Övervaka pipelinekörningen.

## <a name="overview"></a>Översikt
Här är det avancerade diagrammet:

![Läsa in data stegvis](media/tutorial-Incrementally-copy-powershell/incrementally-load.png)

Här är några viktiga steg för att skapa den här lösningen:

1. **Markera vattenstämpelkolumnen**.
    Markera en kolumn i källdatalagringen som går att använda för att dela upp de nya eller uppdaterade posterna för varje körning. Vanligtvis ökar data i den markerade kolumnen (till exempel last_modify_time elle ID) när rader skapas eller uppdateras. Det maximala värdet i den här kolumnen används som vattenstämpel.

2. **Förbered datalagringen för att lagra värdet för vattenstämpeln**.   
    I den här självstudien lagrar du storleksgränsen i en SQL-databas.

3. **Skapa en pipeline med följande arbets flöde**:

    Pipelinen i den här lösningen har följande aktiviteter:

    * Skapa två sökningsaktiviteter. Använd den första sökningsaktiviteten för att hämta det sista vattenstämpelvärdet. Använd den andra sökningsaktiviteten för att hämta det nya vattenstämpelvärdet. Vattenstämpelvärdena skickas till kopieringsaktiviteten.
    * Skapa en {0}kopieringsaktivitet{0} som kopierar raderna från källdatalagringen med värdet för vattenstämpelkolumnen som är större än det gamla värdet och mindre än det nya. Sedan kopieras deltadata från källdatalagringen till Blob-lagring som en ny fil.
    * Skapa en StoredProcedure-aktivitet som uppdaterar vattenstämpelvärdet för den pipeline som körs nästa gång.


Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Azure SQL Database**. Du använder databasen som källa för datalagringen. Om du inte har en databas i Azure SQL Database, se [skapa en databas i Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md) för att skapa en.
* **Azure Storage**. Du kan använda blob-lagringen som mottagare för datalagringen. Om du inte har ett lagringskonto finns det anvisningar om hur du skapar ett i [Skapa ett lagringskonto](../storage/common/storage-account-create.md). Skapa en container med namnet adftutorial. 
* **Azure PowerShell**. Följ instruktionerna i [Installera och konfigurera Azure PowerShell](/powershell/azure/install-Az-ps).

### <a name="create-a-data-source-table-in-your-sql-database"></a>Skapa en datatabell i din SQL-databas
1. Öppna SQL Server Management Studio. I **Server Explorer** högerklickar du på databasen och väljer **Ny fråga**.

2. Kör följande SQL-kommando mot din SQL-databas för att skapa en tabell med namnet `data_source_table` som datakällagringen:

    ```sql
    create table data_source_table
    (
        PersonID int,
        Name varchar(255),
        LastModifytime datetime
    );

    INSERT INTO data_source_table
    (PersonID, Name, LastModifytime)
    VALUES
    (1, 'aaaa','9/1/2017 12:56:00 AM'),
    (2, 'bbbb','9/2/2017 5:23:00 AM'),
    (3, 'cccc','9/3/2017 2:36:00 AM'),
    (4, 'dddd','9/4/2017 3:21:00 AM'),
    (5, 'eeee','9/5/2017 8:06:00 AM');
    ```
    I den här självstudien ska du använda LastModifytime som vattenstämpelkolumn. Data i datakällagringen visas i följande tabell:

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    ```

### <a name="create-another-table-in-your-sql-database-to-store-the-high-watermark-value"></a>Skapa en annan tabell i SQL-databasen för att lagra värdet för högvattenmärket
1. Kör följande SQL-kommando mot din SQL-databas för att skapa en tabell med namnet `watermarktable` för att lagra värdet för högvattenmärket:  

    ```sql
    create table watermarktable
    (

    TableName varchar(255),
    WatermarkValue datetime,
    );
    ```
2. Ställ in standardvärdet för högvattenmärket med tabellnamnet på källdatalagret. I den här självstudien är tabellnamnet: data_source_table.

    ```sql
    INSERT INTO watermarktable
    VALUES ('data_source_table','1/1/2010 12:00:00 AM')    
    ```
3. Granska informationen i tabellen `watermarktable`.

    ```sql
    Select * from watermarktable
    ```
    Utdata:

    ```
    TableName  | WatermarkValue
    ----------  | --------------
    data_source_table | 2010-01-01 00:00:00.000
    ```

### <a name="create-a-stored-procedure-in-your-sql-database"></a>Skapa en lagrad procedur i din SQL-databas

Kör följande kommando för att skapa en lagrad procedur i din SQL-databas:

```sql
CREATE PROCEDURE usp_write_watermark @LastModifiedtime datetime, @TableName varchar(50)
AS

BEGIN

UPDATE watermarktable
SET [WatermarkValue] = @LastModifiedtime
WHERE [TableName] = @TableName

END
```

## <a name="create-a-data-factory"></a>Skapa en datafabrik
1. Definiera en variabel för resursgruppens namn som du kan använda senare i PowerShell-kommandon. Kopiera följande kommandotext till PowerShell, ange ett namn för [Azure-resursgruppen](../azure-resource-manager/management/overview.md), sätt dubbla citattecken omkring namnet och kör sedan kommandot. Ett exempel är `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Om resursgruppen redan finns behöver du kanske inte skriva över den. Ge variabeln `$resourceGroupName` ett annat värde och kör kommandot igen.

2. Definiera en variabel för datafabrikens plats.

    ```powershell
    $location = "East US"
    ```
3. Kör följande kommando för att skapa en Azure-resursgrupp:

    ```powershell
    New-AzResourceGroup $resourceGroupName $location
    ```
    Om resursgruppen redan finns behöver du kanske inte skriva över den. Ge variabeln `$resourceGroupName` ett annat värde och kör kommandot igen.

4. Definiera en variabel för datafabrikens namn.

    > [!IMPORTANT]
    >  Uppdateringen av datafabrikens namn för att göra det unikt globalt. Ett exempel är ADFTutorialFactorySP1127.

    ```powershell
    $dataFactoryName = "ADFIncCopyTutorialFactory";
    ```
5. Skapa data fabriken genom att köra följande **set-AzDataFactoryV2-** cmdlet:

    ```powershell       
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName
    ```

Observera följande punkter:

* Namnet på datafabriken måste vara globalt unikt. Om du får följande felmeddelande ändrar du namnet och försöker igen:

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```

* Om du vill skapa Data Factory-instanser måste det användarkonto du använder för att logga in på Azure vara medlem av rollerna deltagare eller ägare, eller vara administratör för Azure-prenumerationen.
* Om du vill se en lista med Azure-regioner där Data Factory är tillgängligt för närvarande markerar du de regioner du är intresserad av på följande sida. Expandera sedan **Analytics** och leta rätt på **Data Factory**: [Tillgängliga produkter per region](https://azure.microsoft.com/global-infrastructure/services/). Data lag ren (lagring, SQL Database, Azure SQL-hanterad instans osv.) och beräkningarna (Azure HDInsight osv.) som används av data fabriken kan finnas i andra regioner.


## <a name="create-linked-services"></a>Skapa länkade tjänster
Du kan skapa länkade tjänster i en datafabrik för att länka ditt datalager och beräkna datafabrik-tjänster. I det här avsnittet skapar du länkade tjänster till ditt lagrings konto och SQL Database.

### <a name="create-a-storage-linked-service"></a>Skapa en länkad lagringstjänst
1. Skapa en JSON-fil med namnet AzureStorageLinkedService.json i mappen C:\ADF med följande innehåll. (Skapa mappen ADF om den inte redan finns.) Ersätt `<accountName>` och `<accountKey>` med namnet och nyckeln för ditt lagrings konto innan du sparar filen.

    ```json
    {
        "name": "AzureStorageLinkedService",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountName>;AccountKey=<accountKey>"
            }
        }
    }
    ```
2. Växla till mappen ADF i PowerShell.

3. Kör cmdleten **set-AzDataFactoryV2LinkedService** för att skapa den länkade tjänsten AzureStorageLinkedService. I följande exempel skickar du värden för parametrarna *ResourceGroupName* och *DataFactoryName* :

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
    ```

    Här är exempel på utdata:

    ```console
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

### <a name="create-a-sql-database-linked-service"></a>Skapa en länkad tjänst till SQL-databas
1. Skapa en JSON-fil med namnet AzureSQLDatabaseLinkedService.json i mappen C:\ADF med följande innehåll. (Skapa mappen ADF om den inte redan finns.) Ersätt &lt; Server &gt; , &lt; databas &gt; , &lt; användar-ID &gt; och &lt; lösen ord &gt; med namnet på din server, databas, användar-ID och lösen ord innan du sparar filen.

    ```json
    {
        "name": "AzureSQLDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server = tcp:<server>.database.windows.net,1433;Initial Catalog=<database>; Persist Security Info=False; User ID=<user> ; Password=<password>; MultipleActiveResultSets = False; Encrypt = True; TrustServerCertificate = False; Connection Timeout = 30;"
            }
        }
    }
    ```
2. Växla till mappen ADF i PowerShell.

3. Kör cmdleten **set-AzDataFactoryV2LinkedService** för att skapa den länkade tjänsten AzureSQLDatabaseLinkedService.

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    Här är exempel på utdata:

    ```console
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ProvisioningState :
    ```

## <a name="create-datasets"></a>Skapa datauppsättningar
I det här steget skapar du databaser för att representera käll- och mottagardata.

### <a name="create-a-source-dataset"></a>Skapa en källdatauppsättning

1. Skapa en JSON-fil med namnet SourceDataset.json i samma mapp med följande innehåll:

    ```json
    {
        "name": "SourceDataset",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "data_source_table"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }

    ```
    I den här självstudien använder du tabellnamnet data_source_table. Ersätt det om du använder en tabell med ett annan namn.

2. Kör cmdleten **set-AzDataFactoryV2Dataset** för att skapa data uppsättningen SourceDataset.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    Här är exempel på utdata från cmdleten:

    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-sink-dataset"></a>Skapa en källdatauppsättning

1. Skapa en JSON-fil med namnet SinkDataset.json i samma mapp med följande innehåll:

    ```json
    {
        "name": "SinkDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/incrementalcopy",
                "fileName": "@CONCAT('Incremental-', pipeline().RunId, '.txt')",
                "format": {
                    "type": "TextFormat"
                }
            },
            "linkedServiceName": {
                "referenceName": "AzureStorageLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }   
    ```

    > [!IMPORTANT]
    > Det här kodfragmentet förutsätter att du har en BLOB-behållare med namnet `adftutorial` i Blob Storage. Skapa containern om den inte finns, eller ställ in den för namnet på en befintlig. Utdatamappen `incrementalcopy` skapas automatiskt om den inte finns i containern. I den här självstudien genereras filnamnet dynamiskt med uttrycket `@CONCAT('Incremental-', pipeline().RunId, '.txt')`.

2. Kör cmdleten **set-AzDataFactoryV2Dataset** för att skapa data uppsättningen SinkDataset.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    Här är exempel på utdata från cmdleten:

    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset    
    ```

## <a name="create-a-dataset-for-a-watermark"></a>Skapa en datauppsättning för en vattenstämpel
I det här steget skapar du en datauppsättning för att lagra ett värde för ett högvattenmärke.

1. Skapa en JSON-fil med namnet WatermarkDataset.json i samma mapp med följande innehåll:

    ```json
    {
        "name": " WatermarkDataset ",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "watermarktable"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }    
    ```
2.  Kör cmdleten **set-AzDataFactoryV2Dataset** för att skapa data uppsättningen WatermarkDataset.

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "WatermarkDataset" -File ".\WatermarkDataset.json"
    ```

    Här är exempel på utdata från cmdleten:

    ```json
    DatasetName       : WatermarkDataset
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset    
    ```

## <a name="create-a-pipeline"></a>Skapa en pipeline
I den här självstudien skapar du en pipeline med två sökningsaktiviteter, en kopieringsaktivitet och en aktivitet för lagrad procedur i en länkad pipeline.


1. Skapa en JSON-fil med namnet IncrementalCopyPipeline.json i samma mapp med följande innehåll:

    ```json
    {
        "name": "IncrementalCopyPipeline",
        "properties": {
            "activities": [
                {
                    "name": "LookupOldWaterMarkActivity",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                        "type": "SqlSource",
                        "sqlReaderQuery": "select * from watermarktable"
                        },

                        "dataset": {
                        "referenceName": "WatermarkDataset",
                        "type": "DatasetReference"
                        }
                    }
                },
                {
                    "name": "LookupNewWaterMarkActivity",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select MAX(LastModifytime) as NewWatermarkvalue from data_source_table"
                        },

                        "dataset": {
                        "referenceName": "SourceDataset",
                        "type": "DatasetReference"
                        }
                    }
                },

                {
                    "name": "IncrementalCopyActivity",
                    "type": "Copy",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select * from data_source_table where LastModifytime > '@{activity('LookupOldWaterMarkActivity').output.firstRow.WatermarkValue}' and LastModifytime <= '@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}'"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "dependsOn": [
                        {
                            "activity": "LookupNewWaterMarkActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        },
                        {
                            "activity": "LookupOldWaterMarkActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        }
                    ],

                    "inputs": [
                        {
                            "referenceName": "SourceDataset",
                            "type": "DatasetReference"
                        }
                    ],
                    "outputs": [
                        {
                            "referenceName": "SinkDataset",
                            "type": "DatasetReference"
                        }
                    ]
                },

                {
                    "name": "StoredProceduretoWriteWatermarkActivity",
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {

                        "storedProcedureName": "usp_write_watermark",
                        "storedProcedureParameters": {
                            "LastModifiedtime": {"value": "@{activity('LookupNewWaterMarkActivity').output.firstRow.NewWatermarkvalue}", "type": "datetime" },
                            "TableName":  { "value":"@{activity('LookupOldWaterMarkActivity').output.firstRow.TableName}", "type":"String"}
                        }
                    },

                    "linkedServiceName": {
                        "referenceName": "AzureSQLDatabaseLinkedService",
                        "type": "LinkedServiceReference"
                    },

                    "dependsOn": [
                        {
                            "activity": "IncrementalCopyActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        }
                    ]
                }
            ]

        }
    }
    ```


2. Kör cmdleten **set-AzDataFactoryV2Pipeline** för att skapa pipelinen IncrementalCopyPipeline.

   ```powershell
   Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ```

   Här är exempel på utdata:

   ```console
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    Activities        : {LookupOldWaterMarkActivity, LookupNewWaterMarkActivity, IncrementalCopyActivity, StoredProceduretoWriteWatermarkActivity}
    Parameters        :
   ```

## <a name="run-the-pipeline"></a>Köra en pipeline

1. Kör pipeline-IncrementalCopyPipeline med cmdleten **Invoke-AzDataFactoryV2Pipeline** . Ersätt platshållarna med din egen grupp och datafabrikens namn.

    ```powershell
    $RunId = Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroupName $resourceGroupName -dataFactoryName $dataFactoryName
    ```
2. Kontrol lera status för pipelinen genom att köra cmdleten **Get-AzDataFactoryV2ActivityRun** tills du ser att alla aktiviteter körs. Ersätt platshållarna med din egen lämpliga tid för parametern *RunStartedAfter* och *RunStartedBefore*. I den här självstudien använder du *-RunStartedAfter "2017/09/14"* och *-RunStartedBefore "2017/09/15"*.

    ```powershell
    Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $RunId -RunStartedAfter "<start time>" -RunStartedBefore "<end time>"
    ```

    Här är exempel på utdata:

    ```console
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupNewWaterMarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {NewWatermarkvalue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:42:42 AM
    ActivityRunEnd    : 9/14/2017 7:42:50 AM
    DurationInMs      : 7777
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupOldWaterMarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {TableName, WatermarkValue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:42:42 AM
    ActivityRunEnd    : 9/14/2017 7:43:07 AM
    DurationInMs      : 25437
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : IncrementalCopyActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, rowsCopied, copyDuration...}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:43:10 AM
    ActivityRunEnd    : 9/14/2017 7:43:29 AM
    DurationInMs      : 19769
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : StoredProceduretoWriteWatermarkActivity
    PipelineRunId     : d4bf3ce2-5d60-43f3-9318-923155f61037
    PipelineName      : IncrementalCopyPipeline
    Input             : {storedProcedureName, storedProcedureParameters}
    Output            : {}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 7:43:32 AM
    ActivityRunEnd    : 9/14/2017 7:43:47 AM
    DurationInMs      : 14467
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ```

## <a name="review-the-results"></a>Granska resultaten

1. I blob-lagringen (sink store) ser du att data har kopierats till filen som definierats i SinkDataset. I den aktuella självstudien är filnamnet `Incremental- d4bf3ce2-5d60-43f3-9318-923155f61037.txt`. Öppna filen så ser du poster i filen som är samma som de data som finns i SQL-databasen.

    ```
    1,aaaa,2017-09-01 00:56:00.0000000
    2,bbbb,2017-09-02 05:23:00.0000000
    3,cccc,2017-09-03 02:36:00.0000000
    4,dddd,2017-09-04 03:21:00.0000000
    5,eeee,2017-09-05 08:06:00.0000000
    ```
2. Kontrollera det senaste värdet från `watermarktable`. Du kan se att vattenstämpeln har uppdaterats.

    ```sql
    Select * from watermarktable
    ```

    Här är exempel på utdata:

    TableName | WatermarkValue
    --------- | --------------
    data_source_table | 2017-09-05 8:06:00.000

### <a name="insert-data-into-the-data-source-store-to-verify-delta-data-loading"></a>Infoga data i datakällagret för att verifiera deltadatainläsning

1. Infoga nya data i SQL-databasen (datakällagring).

    ```sql
    INSERT INTO data_source_table
    VALUES (6, 'newdata','9/6/2017 2:23:00 AM')

    INSERT INTO data_source_table
    VALUES (7, 'newdata','9/7/2017 9:01:00 AM')
    ```

    Uppdaterade data i SQL-databasen är:

    ```
    PersonID | Name | LastModifytime
    -------- | ---- | --------------
    1 | aaaa | 2017-09-01 00:56:00.000
    2 | bbbb | 2017-09-02 05:23:00.000
    3 | cccc | 2017-09-03 02:36:00.000
    4 | dddd | 2017-09-04 03:21:00.000
    5 | eeee | 2017-09-05 08:06:00.000
    6 | newdata | 2017-09-06 02:23:00.000
    7 | newdata | 2017-09-07 09:01:00.000
    ```
2. Kör pipelinen IncrementalCopyPipeline igen genom att använda cmdleten **Invoke-AzDataFactoryV2Pipeline** . Ersätt platshållarna med din egen grupp och datafabrikens namn.

    ```powershell
    $RunId = Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroupName $resourceGroupName -dataFactoryName $dataFactoryName
    ```
3. Kontrol lera status för pipelinen genom att köra cmdleten **Get-AzDataFactoryV2ActivityRun** tills du ser att alla aktiviteter körs. Ersätt platshållarna med din egen lämpliga tid för parametern *RunStartedAfter* och *RunStartedBefore*. I den här självstudien använder du *-RunStartedAfter "2017/09/14"* och *-RunStartedBefore "2017/09/15"*.

    ```powershell
    Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $RunId -RunStartedAfter "<start time>" -RunStartedBefore "<end time>"
    ```

    Här är exempel på utdata:

    ```console
    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupNewWaterMarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {NewWatermarkvalue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:52:26 AM
    ActivityRunEnd    : 9/14/2017 8:52:58 AM
    DurationInMs      : 31758
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : LookupOldWaterMarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, dataset}
    Output            : {TableName, WatermarkValue}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:52:26 AM
    ActivityRunEnd    : 9/14/2017 8:52:52 AM
    DurationInMs      : 25497
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : IncrementalCopyActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {source, sink}
    Output            : {dataRead, dataWritten, rowsCopied, copyDuration...}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:53:00 AM
    ActivityRunEnd    : 9/14/2017 8:53:20 AM
    DurationInMs      : 20194
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : ADF
    DataFactoryName   : incrementalloadingADF
    ActivityName      : StoredProceduretoWriteWatermarkActivity
    PipelineRunId     : 2fc90ab8-d42c-4583-aa64-755dba9925d7
    PipelineName      : IncrementalCopyPipeline
    Input             : {storedProcedureName, storedProcedureParameters}
    Output            : {}
    LinkedServiceName :
    ActivityRunStart  : 9/14/2017 8:53:23 AM
    ActivityRunEnd    : 9/14/2017 8:53:41 AM
    DurationInMs      : 18502
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ```
4. I blob-lagringen ser du att en annan fil har skapats. I den här självstudien är det nya filnamnet `Incremental-2fc90ab8-d42c-4583-aa64-755dba9925d7.txt`. När du öppnar filen ser du två rader med poster i den.

5. Kontrollera det senaste värdet från `watermarktable`. Du kan se att vattenstämpeln har uppdaterats igen.

    ```sql
    Select * from watermarktable
    ```
    exempel på utdata:

    TableName | WatermarkValue
    --------- | ---------------
    data_source_table | 2017-09-07 09:01:00.000


## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen fick du:

> [!div class="checklist"]
> * Förbered datalagringen för att lagra värdet för vattenstämpeln.
> * Skapa en datafabrik.
> * Skapa länkade tjänster.
> * Skapa datauppsättningar för källa, mottagare och vattenstämpel.
> * Skapa en pipeline.
> * Köra en pipeline.
> * Övervaka pipelinekörningen.

I den här självstudien kopierade pipelinen data från en enskild tabell i Azure SQL Database till Blob Storage. Gå vidare till följande självstudie för att lära dig hur du kopierar data från flera tabeller i en SQL Server databas till SQL Database.

> [!div class="nextstepaction"]
>[Läs in data stegvist från flera tabeller i SQL Server till Azure SQL Database](tutorial-incremental-copy-multiple-tables-powershell.md)
