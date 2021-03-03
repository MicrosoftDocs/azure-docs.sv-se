---
title: Kopiera data i bulk med PowerShell
description: Använd Azure Data Factory med kopierings aktivitet för att kopiera data från ett käll data lager till ett mål data lager i bulk.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: fc539ababf4cb240fbe78de0d87b1f127807f604
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740444"
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory-using-powershell"></a>Kopiera flera tabeller i bulk genom att använda Azure Data Factory med hjälp av PowerShell

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här självstudien visar hur **du kopierar ett antal tabeller från Azure SQL Database till Azure Synapse Analytics**. Du kan även använda samma mönster i andra kopieringssituationer. Till exempel kan du kopiera tabeller från SQL Server/Oracle till Azure SQL Database/Data Warehouse/Azure Blob eller kopiera olika sökvägar från Blob till Azure SQL Database-tabeller.

Sett på en hög nivå ingår följande steg i självstudierna:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa Azure SQL Database, Azure Synapse Analytics och Azure Storage länkade tjänster.
> * Skapa Azure SQL Database och Azure Synapse Analytics-datauppsättningar.
> * Skapa en pipeline för sökning av de tabeller som ska kopieras och en annan pipeline för den faktiska kopieringsåtgärden. 
> * Starta en pipelinekörning.
> * Övervaka pipelinen och aktivitetskörningarna.

I den här kursen används Azure PowerShell. Läs mer om att använda andra verktyg/SDK:er för att skapa en datafabrik i [Snabbstarter](quickstart-create-data-factory-dot-net.md). 

## <a name="end-to-end-workflow"></a>Arbetsflödet slutpunkt till slutpunkt
I det här scenariot har vi ett antal tabeller i Azure SQL Database som vi vill kopiera till Azure Synapse Analytics. Här är den logiska ordningsföljden i arbetsflödet som sker i våra pipelines:

![Arbetsflöde](media/tutorial-bulk-copy/tutorial-copy-multiple-tables.png)

* Den första pipelinen letar rätt på listan med tabeller som ska kopieras till de mottagande datalagren.  Du kan istället underhålla en metadatatabell som innehåller alla tabeller som ska kopieras till de mottagande datalagren. Sedan utlöser pipelinen en annan pipeline, som itererar över varje tabell i databasen och utför själva datakopieringen.
* Den andra pipelinen utför den faktiska kopieringen. Den tar listan med tabeller som en parameter. För varje tabell i listan kopierar du den speciella tabellen i Azure SQL Database till motsvarande tabell i Azure Synapse Analytics med hjälp av [mellanlagrad kopia via Blob Storage och PolyBase](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-synapse-analytics) för bästa prestanda. I det här exemplet skickar den första pipelinen listan med tabeller som värde för parametern. 

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

* **Azure PowerShell**. Följ instruktionerna i [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/install-Az-ps).
* **Azure Storage konto**. Azure Storage-kontot används för mellanlagring för Blob Storage i masskopieringsåtgärden. 
* **Azure SQL Database**. Den här databasen innehåller källdata. 
* **Azure Synapse-analys**. Det här datalagret innehåller de data som kopieras från SQL Database. 

### <a name="prepare-sql-database-and-azure-synapse-analytics"></a>Förbered SQL Database-och Azure Synapse-analys

**Förbered Azure SQL Database-källan**:

Skapa en databas med Adventure Works LT-exempel data i SQL Database genom att följa artikeln [skapa en databas i Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md) . Den här självstudien kopierar alla tabeller från den här exempel databasen till Azure Synapse Analytics.

**Förbered intagningen av Azure Synapse Analytics**:

1. Om du inte har en Azure Synapse Analytics-arbetsyta läser du artikeln [Kom igång med Azure Synapse Analytics](..\synapse-analytics\get-started.md) för att skapa en.

2. Skapa motsvarande tabell scheman i Azure Synapse Analytics. Du kommer att använda Azure Data Factory till att migrera/kopiera data i ett senare steg.

## <a name="azure-services-to-access-sql-server"></a>Azure-tjänster för åtkomst till SQL-servern

Ge Azure-tjänster åtkomst till SQL Server för både SQL Database-och Azure Synapse-analys. Kontrol lera att inställningen **Tillåt åtkomst till Azure** -tjänster **är aktive** rad för servern. Med den här inställningen kan Data Factory tjänsten läsa data från din Azure SQL Database och skriva data till Azure Synapse Analytics. Gör så här för att kontrollera och aktivera den här inställningen:

1. Klicka på **Alla tjänster** till vänster och klicka på **SQL-servrar**.
2. Välj din server och klicka på **Brandvägg** under **INSTÄLLNINGAR**.
3. På sidan **Brandväggsinställningar** klickar du på **På** för **Tillåt åtkomst till Azure-tjänster**.

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Starta **PowerShell**. Låt Azure PowerShell vara öppet tills du är klar med självstudien. Om du stänger och öppnar det igen måste du köra kommandona en gång till.

    Kör följande kommando och ange det användarnamn och lösenord som du använder för att logga in i Azure Portal:
        
    ```powershell
    Connect-AzAccount
    ```
    Kör följande kommando för att visa alla prenumerationer för det här kontot:

    ```powershell
    Get-AzSubscription
    ```
    Kör följande kommando för att välja den prenumeration som du vill arbeta med. Ersätt **SubscriptionId** med ID:t för din Azure-prenumeration:

    ```powershell
    Select-AzSubscription -SubscriptionId "<SubscriptionId>"
    ```
2. Kör cmdleten **set-AzDataFactoryV2** för att skapa en data fabrik. Ersätt platshållare med egna värden innan kommandot körs. 

    ```powershell
    $resourceGroupName = "<your resource group to create the factory>"
    $dataFactoryName = "<specify the name of data factory to create. It must be globally unique.>"
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location "East US" -Name $dataFactoryName
    ```

    Observera följande punkter:

    * Namnet på Azure Data Factory måste vara globalt unikt. Om du får följande felmeddelande ändrar du namnet och försöker igen.

        ```
        The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
        ```

    * När du ska skapa Data Factory-instanser måste du vara deltagare/administratör för Azure-prenumerationen.
    * Om du vill se en lista med Azure-regioner där Data Factory är tillgängligt för närvarande markerar du de regioner du är intresserad av på följande sida. Expandera sedan **Analytics** och leta rätt på **Data Factory**: [Tillgängliga produkter per region](https://azure.microsoft.com/global-infrastructure/services/). Datalagren (Azure Storage, Azure SQL Database osv.) och beräkningarna (HDInsight osv.) som används i Data Factory kan finnas i andra regioner.

## <a name="create-linked-services"></a>Skapa länkade tjänster

I den här självstudien skapar du tre länkade tjänster för käll-, mottagar- och mellanlagringsbloben, som innehåller anslutningar till dina datalager:

### <a name="create-the-source-azure-sql-database-linked-service"></a>Skapa den länkade tjänsten för Azure SQL Database-källan

1. Skapa en JSON-fil med namnet **AzureSqlDatabaseLinkedService.json** i mappen **C:\ADFv2TutorialBulkCopy** med följande innehåll: (skapa mappen ADFv2TutorialBulkCopy om den inte redan finns.)

    > [!IMPORTANT]
    > Ersätt &lt;servername&gt;, &lt;databasename&gt;, &lt;username&gt;@&lt;servername&gt; och &lt;password&gt; med värdena för din Azure SQL-databas innan du sparar filen.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
    }
    ```

2. I **Azure PowerShell** växlar du till mappen **ADFv2TutorialBulkCopy**.

3. Kör cmdleten **set-AzDataFactoryV2LinkedService** för att skapa den länkade tjänsten: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

    Här är exempel på utdata:

    ```console
    LinkedServiceName : AzureSqlDatabaseLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

### <a name="create-the-sink-azure-synapse-analytics-linked-service"></a>Skapa en länkad Azure Synapse Analytics-tjänst för mottagare

1. Skapa en JSON-fil med namnet **AzureSqlDWLinkedService.json** i mappen **C:\ADFv2TutorialBulkCopy** med följande innehåll:

    > [!IMPORTANT]
    > Ersätt &lt;servername&gt;, &lt;databasename&gt;, &lt;username&gt;@&lt;servername&gt; och &lt;password&gt; med värdena för din Azure SQL-databas innan du sparar filen.

    ```json
    {
        "name": "AzureSqlDWLinkedService",
        "properties": {
            "type": "AzureSqlDW",
            "typeProperties": {
                "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
    }
    ```

2. Skapa den länkade tjänsten: **AzureSqlDWLinkedService** genom att köra cmdleten **set-AzDataFactoryV2LinkedService** .

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDWLinkedService" -File ".\AzureSqlDWLinkedService.json"
    ```

    Här är exempel på utdata:

    ```console
    LinkedServiceName : AzureSqlDWLinkedService
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDWLinkedService
    ```

### <a name="create-the-staging-azure-storage-linked-service"></a>Skapa den länkade tjänsten för Azure Storage-mellanlagringen

I den här självstudien använder du Azure Blob Storage som ett mellanlagringsutrymme så att PolyBase aktiveras och ger kopieringen bättre prestanda.

1. Skapa en JSON-fil med namnet **AzureStorageLinkedService.json** i mappen **C:\ADFv2TutorialBulkCopy** med följande innehåll:

    > [!IMPORTANT]
    > Ersätt &lt;accountName&gt; och &lt;accountKey&gt; med namnet och nyckeln för ditt Azure Storage-konto innan du sparar filen.

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

2. Skapa den länkade tjänsten: **AzureStorageLinkedService** genom att köra cmdleten **set-AzDataFactoryV2LinkedService** .

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

## <a name="create-datasets"></a>Skapa datauppsättningar

I den här självstudien skapar du datauppsättningar för källa och mottagare som anger var data lagras:

### <a name="create-a-dataset-for-source-sql-database"></a>Skapa en datauppsättning för SQL Database-källan

1. Skapa en JSON-fil med namnet **AzureSqlDatabaseDataset.json** i mappen **C:\ADFv2TutorialBulkCopy** med följande innehåll: ”tableName” är en platshållare eftersom du senare använder SQL-frågan i kopieringsaktiviteten till att hämta data.

    ```json
    {
        "name": "AzureSqlDatabaseDataset",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": {
                "referenceName": "AzureSqlDatabaseLinkedService",
                "type": "LinkedServiceReference"
            },
            "typeProperties": {
                "tableName": "dummy"
            }
        }
    }
    ```

2. Om du vill skapa data uppsättningen: **AzureSqlDatabaseDataset** kör du cmdleten **set-AzDataFactoryV2Dataset** .

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDatabaseDataset" -File ".\AzureSqlDatabaseDataset.json"
    ```

    Här är exempel på utdata:

    ```console
    DatasetName       : AzureSqlDatabaseDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-dataset-for-sink-azure-synapse-analytics"></a>Skapa en data uppsättning för Sink Azure Synapse Analytics

1. Skapa en JSON-fil med namnet **AzureSqlDWDataset.json** i mappen **C:\ADFv2TutorialBulkCopy** med följande innehåll: ”tableName” anges som en parameter, senare skickar kopieringsaktiviteten som refererar till den här datauppsättningen det faktiska värdet till datauppsättningen.

    ```json
    {
        "name": "AzureSqlDWDataset",
        "properties": {
            "type": "AzureSqlDWTable",
            "linkedServiceName": {
                "referenceName": "AzureSqlDWLinkedService",
                "type": "LinkedServiceReference"
            },
            "typeProperties": {
                "tableName": {
                    "value": "@{dataset().DWTableName}",
                    "type": "Expression"
                }
            },
            "parameters":{
                "DWTableName":{
                    "type":"String"
                }
            }
        }
    }
    ```

2. Om du vill skapa data uppsättningen: **AzureSqlDWDataset** kör du cmdleten **set-AzDataFactoryV2Dataset** .

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSqlDWDataset" -File ".\AzureSqlDWDataset.json"
    ```

    Här är exempel på utdata:

    ```console
    DatasetName       : AzureSqlDWDataset
    ResourceGroupName : <resourceGroupname>
    DataFactoryName   : <dataFactoryName>
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDwTableDataset
    ```

## <a name="create-pipelines"></a>Skapa pipelines

I den här självstudien skapar du två pipelines:

### <a name="create-the-pipeline-iterateandcopysqltables"></a>Skapa pipelinen ”IterateAndCopySQLTables”

Den här pipelinen tar en lista med tabeller som en parameter. För varje tabell i listan kopieras data från tabellen i Azure SQL Database till Azure Synapse Analytics med hjälp av mellanlagrad kopiering och PolyBase.

1. Skapa en JSON-fil med namnet **IterateAndCopySQLTables.json** i mappen **C:\ADFv2TutorialBulkCopy** med följande innehåll:

    ```json
    {
        "name": "IterateAndCopySQLTables",
        "properties": {
            "activities": [
                {
                    "name": "IterateSQLTables",
                    "type": "ForEach",
                    "typeProperties": {
                        "isSequential": "false",
                        "items": {
                            "value": "@pipeline().parameters.tableList",
                            "type": "Expression"
                        },
                        "activities": [
                            {
                                "name": "CopyData",
                                "description": "Copy data from Azure SQL Database to Azure Synapse Analytics",
                                "type": "Copy",
                                "inputs": [
                                    {
                                        "referenceName": "AzureSqlDatabaseDataset",
                                        "type": "DatasetReference"
                                    }
                                ],
                                "outputs": [
                                    {
                                        "referenceName": "AzureSqlDWDataset",
                                        "type": "DatasetReference",
                                        "parameters": {
                                            "DWTableName": "[@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]"
                                        }
                                    }
                                ],
                                "typeProperties": {
                                    "source": {
                                        "type": "SqlSource",
                                        "sqlReaderQuery": "SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]"
                                    },
                                    "sink": {
                                        "type": "SqlDWSink",
                                        "preCopyScript": "TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]",
                                        "allowPolyBase": true
                                    },
                                    "enableStaging": true,
                                    "stagingSettings": {
                                        "linkedServiceName": {
                                            "referenceName": "AzureStorageLinkedService",
                                            "type": "LinkedServiceReference"
                                        }
                                    }
                                }
                            }
                        ]
                    }
                }
            ],
            "parameters": {
                "tableList": {
                    "type": "Object"
                }
            }
        }
    }
    ```

2. Om du vill skapa pipelinen: **IterateAndCopySQLTables** kör du cmdleten **set-AzDataFactoryV2Pipeline** .

    ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IterateAndCopySQLTables" -File ".\IterateAndCopySQLTables.json"
    ```

    Här är exempel på utdata:

    ```console
    PipelineName      : IterateAndCopySQLTables
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {IterateSQLTables}
    Parameters        : {[tableList, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>Skapa pipelinen ”GetTableListAndTriggerCopyData”

Den här pipelinen utför två steg:

* Den söker i Azure SQL Database-systemtabellen för att få fram listan med tabeller som ska kopieras
* den utlöser pipelinen ”IterateAndCopySQLTables” för att utföra den faktiska kopieringen.

1. Skapa en JSON-fil med namnet **GetTableListAndTriggerCopyData.json** i mappen **C:\ADFv2TutorialBulkCopy** med följande innehåll:

    ```json
    {
        "name":"GetTableListAndTriggerCopyData",
        "properties":{
            "activities":[
                { 
                    "name": "LookupTableList",
                    "description": "Retrieve the table list from Azure SQL dataabse",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'"
                        },
                        "dataset": {
                            "referenceName": "AzureSqlDatabaseDataset",
                            "type": "DatasetReference"
                        },
                        "firstRowOnly": false
                    }
                },
                {
                    "name": "TriggerCopy",
                    "type": "ExecutePipeline",
                    "typeProperties": {
                        "parameters": {
                            "tableList": {
                                "value": "@activity('LookupTableList').output.value",
                                "type": "Expression"
                            }
                        },
                        "pipeline": {
                            "referenceName": "IterateAndCopySQLTables",
                            "type": "PipelineReference"
                        },
                        "waitOnCompletion": true
                    },
                    "dependsOn": [
                        {
                            "activity": "LookupTableList",
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

2. Om du vill skapa pipelinen: **GetTableListAndTriggerCopyData** kör du cmdleten **set-AzDataFactoryV2Pipeline** .

    ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "GetTableListAndTriggerCopyData" -File ".\GetTableListAndTriggerCopyData.json"
    ```

    Här är exempel på utdata:

    ```console
    PipelineName      : GetTableListAndTriggerCopyData
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {LookupTableList, TriggerCopy}
    Parameters        :
    ```

## <a name="start-and-monitor-pipeline-run"></a>Starta och övervaka en pipelinekörning

1. Starta en pipelinekörning för huvudpipelinen ”GetTableListAndTriggerCopyData” och spara pipelinekörningens ID för framtida övervakning. Därefter utlöses körningen av pipelinen ”IterateAndCopySQLTables” som anges i aktiviteten ExecutePipeline.

    ```powershell
    $runId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineName 'GetTableListAndTriggerCopyData'
    ```

2.  Kör följande skript som kontinuerligt kontrollerar körningsstatus för pipelinen **GetTableListAndTriggerCopyData** och skriver ut slutresultatet för pipelinekörningen och aktiviteten.

    ```powershell
    while ($True) {
        $run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $resourceGroupName -DataFactoryName $DataFactoryName -PipelineRunId $runId

        if ($run) {
            if ($run.Status -ne 'InProgress') {
                Write-Host "Pipeline run finished. The status is: " $run.Status -foregroundcolor "Yellow"
                Write-Host "Pipeline run details:" -foregroundcolor "Yellow"
                $run
                break
            }
            Write-Host  "Pipeline is running...status: InProgress" -foregroundcolor "Yellow"
        }

        Start-Sleep -Seconds 15
    }

    $result = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId $runId -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    Write-Host "Activity run details:" -foregroundcolor "Yellow"
    $result
    ```

    Här är utdata för exempelkörningen:

    ```console
    Pipeline run details:
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    RunId             : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    LastUpdated       : 9/18/2017 4:08:15 PM
    Parameters        : {}
    RunStart          : 9/18/2017 4:06:44 PM
    RunEnd            : 9/18/2017 4:08:15 PM
    DurationInMs      : 90637
    Status            : Succeeded
    Message           : 

    Activity run details:
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : LookupTableList
    PipelineRunId     : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    Input             : {source, dataset, firstRowOnly}
    Output            : {count, value, effectiveIntegrationRuntime}
    LinkedServiceName : 
    ActivityRunStart  : 9/18/2017 4:06:46 PM
    ActivityRunEnd    : 9/18/2017 4:07:09 PM
    DurationInMs      : 22995
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}

    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    ActivityName      : TriggerCopy
    PipelineRunId     : 0000000000-00000-0000-0000-000000000000
    PipelineName      : GetTableListAndTriggerCopyData
    Input             : {pipeline, parameters, waitOnCompletion}
    Output            : {pipelineRunId}
    LinkedServiceName : 
    ActivityRunStart  : 9/18/2017 4:07:11 PM
    ActivityRunEnd    : 9/18/2017 4:08:14 PM
    DurationInMs      : 62581
    Status            : Succeeded
    Error             : {errorCode, message, failureType, target}
    ```

3. Du kan få fram körnings-ID:t för pipelinen ”**IterateAndCopySQLTables**” och se detaljerade resultat från aktivitetskörningen genom att köra följande:

    ```powershell
    Write-Host "Pipeline 'IterateAndCopySQLTables' run result:" -foregroundcolor "Yellow"
    ($result | Where-Object {$_.ActivityName -eq "TriggerCopy"}).Output.ToString()
    ```

    Här är utdata för exempelkörningen:

    ```json
    {
        "pipelineRunId": "7514d165-14bf-41fb-b5fb-789bea6c9e58"
    }
    ```

    ```powershell
    $result2 = Get-AzDataFactoryV2ActivityRun -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -PipelineRunId <copy above run ID> -RunStartedAfter (Get-Date).AddMinutes(-30) -RunStartedBefore (Get-Date).AddMinutes(30)
    $result2
    ```

3. Anslut till din Sink Azure Synapse Analytics och bekräfta att data har kopierats från Azure SQL Database korrekt.

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen fick du: 

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa Azure SQL Database, Azure Synapse Analytics och Azure Storage länkade tjänster.
> * Skapa Azure SQL Database och Azure Synapse Analytics-datauppsättningar.
> * Skapa en pipeline för sökning av de tabeller som ska kopieras och en annan pipeline för den faktiska kopieringsåtgärden. 
> * Starta en pipelinekörning.
> * Övervaka pipelinen och aktivitetskörningarna.

Fortsätt till följande självstudiekurs om du vill lära dig att kopiera data stegvis från en källa till ett mål:
> [!div class="nextstepaction"]
>[Kopiera data stegvis](tutorial-incremental-copy-powershell.md)
