---
title: Anropa SSIS-paket med hjälp av Azure Data Factory-lagrad procedur aktivitet
description: Den här artikeln beskriver hur du anropar ett SQL Server Integration Services-paket (SSIS) från en Azure Data Factory-pipeline med hjälp av den lagrade procedur aktiviteten.
author: linda33wj
ms.service: data-factory
ms.devlang: powershell
ms.topic: conceptual
ms.date: 01/19/2018
ms.author: jingwang
ms.openlocfilehash: 6c831fa1c6351840693f2b10a22f59c5cc424d0f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100392892"
---
# <a name="invoke-an-ssis-package-using-stored-procedure-activity-in-azure-data-factory"></a>Anropa ett SSIS-paket med hjälp av en lagrad procedur aktivitet i Azure Data Factory
I den här artikeln beskrivs hur du anropar ett SSIS-paket från en Azure Data Factory pipeline med hjälp av en lagrad procedur aktivitet. 

> [!NOTE]
> Den här artikeln gäller för version 1 av Data Factory. Om du använder den aktuella versionen av tjänsten Data Factory, se [anropa SSIS-paket med lagrad procedur aktivitet i](../how-to-invoke-ssis-package-stored-procedure-activity.md).

## <a name="prerequisites"></a>Förutsättningar

### <a name="azure-sql-database"></a>Azure SQL Database 
I genom gången i den här artikeln används Azure SQL Database. Du kan också använda en hanterad Azure SQL-instans.

### <a name="create-an-azure-ssis-integration-runtime"></a>Skapa en Azure-SSIS Integration Runtime
Skapa en Azure-SSIS integration Runtime om du inte har en genom att följa steg-för-steg-instruktionen i [självstudien: Distribuera SSIS-paket](../tutorial-deploy-ssis-packages-azure.md). Du kan inte använda Data Factory version 1 för att skapa en Azure-SSIS integration Runtime. 

## <a name="azure-powershell"></a>Azure PowerShell
I det här avsnittet använder du Azure PowerShell för att skapa en Data Factory-pipeline med en lagrad procedur aktivitet som anropar ett SSIS-paket.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Installera de senaste Azure PowerShell-modulerna enligt instruktionerna i [Installera och konfigurera Azure PowerShell](/powershell/azure/install-az-ps).

### <a name="create-a-data-factory"></a>Skapa en datafabrik
Följande procedur innehåller steg för att skapa en data fabrik. Du skapar en pipeline med en lagrad procedur aktivitet i den här data fabriken. Den lagrade procedur aktiviteten kör en lagrad procedur i SSISDB-databasen för att köra ditt SSIS-paket.

1. Definiera en variabel för resursgruppens namn som du kan använda senare i PowerShell-kommandon. Kopiera följande kommandotext till PowerShell, ange ett namn för [Azure-resursgruppen](../../azure-resource-manager/management/overview.md), sätt dubbla citattecken omkring namnet och kör sedan kommandot. Exempel: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Om resursgruppen redan finns behöver du kanske inte skriva över den. Ge variabeln `$ResourceGroupName` ett annat värde och kör kommandot igen
2. Kör följande kommando för att skapa en Azure-resursgrupp: 

    ```powershell
    $ResGrp = New-AzResourceGroup $resourceGroupName -location 'eastus'
    ``` 
    Om resursgruppen redan finns behöver du kanske inte skriva över den. Ge variabeln `$ResourceGroupName` ett annat värde och kör kommandot igen. 
3. Definiera en variabel för datafabrikens namn. 

    > [!IMPORTANT]
    >  Uppdateringen av datafabrikens namn måste vara unikt globalt. 

    ```powershell
    $DataFactoryName = "ADFTutorialFactory";
    ```

5. Skapa data fabriken genom att köra följande cmdlet **New-AzDataFactory** med hjälp av egenskapen location och ResourceGroupName från variabeln $ResGrp: 
    
    ```powershell       
    $df = New-AzDataFactory -ResourceGroupName $ResourceGroupName -Name $dataFactoryName -Location "East US"
    ```

Observera följande punkter:

* Namnet på Azure Data Factory måste vara globalt unikt. Om du får följande felmeddelande ändrar du namnet och försöker igen.

    ```
    The specified Data Factory name 'ADFTutorialFactory' is already in use. Data Factory names must be globally unique.
    ```
* Om du vill skapa Data Factory-instanser måste det användarkonto du använder för att logga in på Azure vara medlem av rollerna **deltagare** eller **ägare**, eller vara **administratör** för Azure-prenumerationen.

### <a name="create-an-azure-sql-database-linked-service"></a>Skapa en länkad Azure SQL Database-tjänst
Skapa en länkad tjänst för att länka databasen i Azure SQL Database som är värd för SSIS-katalogen till din data fabrik. Data Factory använder informationen i den här länkade tjänsten för att ansluta till SSISDB-databasen och köra en lagrad procedur för att köra ett SSIS-paket. 

1. Skapa en JSON-fil med namnet **AzureSqlDatabaseLinkedService.jspå** i **C:\ADF\RunSSISPackage** -mappen med följande innehåll: 

    > [!IMPORTANT]
    > Ersätt &lt; servername &gt; , &lt; användar namn &gt; @ &lt; Server namn &gt; och &lt; lösen ord &gt; med värdena för din Azure SQL Database innan du sparar filen.

    ```json
    {
        "name": "AzureSqlDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=SSISDB;User ID=<username>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
            }
        }
        }
    ```
2. I **Azure PowerShell** växlar du till mappen **C:\ADF\RunSSISPackage**
3. Kör cmdleten **New-AzDataFactoryLinkedService** för att skapa den länkade tjänsten: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    New-AzDataFactoryLinkedService $df -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-an-output-dataset"></a>Skapa en datauppsättning för utdata
Den här data uppsättningen är en dummy-datauppsättning som driver schemat för pipelinen. Observera att frekvensen är inställd på timme och att intervallet är inställt på 1. Därför körs pipelinen en gång i timmen i Start-och slut tiderna för pipelinen. 

1. Skapa en OutputDataset.jspå en fil med följande innehåll: 
    
    ```json
    {
        "name": "sprocsampleout",
        "properties": {
            "type": "AzureSqlTable",
            "linkedServiceName": "AzureSqlLinkedService",
            "typeProperties": { },
            "availability": {
                "frequency": "Hour",
                "interval": 1
            }
        }
    }
    ```
2. Kör cmdleten **New-AzDataFactoryDataset** för att skapa en data uppsättning. 

    ```powershell
    New-AzDataFactoryDataset $df -File ".\OutputDataset.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Skapa en pipeline med en lagrad procedur aktivitet 
I det här steget skapar du en pipeline med en lagrad procedur aktivitet. Aktiviteten anropar den sp_executesql lagrade proceduren för att köra ditt SSIS-paket. 

1. Skapa en JSON-fil med namnet **MyPipeline.js** i mappen **C:\ADF\RunSSISPackage** med följande innehåll:

    > [!IMPORTANT]
    > Ersätt &lt; mapp-namn &gt; , &lt; projekt &gt; namn, &lt; paket namn &gt; med namn på mapp, projekt och paket i SSIS-katalogen innan du sparar filen.

    ```json
    {
        "name": "MyPipeline",
        "properties": {
            "activities": [{
                "name": "SprocActivitySample",
                "type": "SqlServerStoredProcedure",
                "typeProperties": {
                    "storedProcedureName": "sp_executesql",
                    "storedProcedureParameters": {
                        "stmt": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<folder name>', @project_name=N'<project name>', @package_name=N'<package name>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                    }
                },
                "outputs": [{
                    "name": "sprocsampleout"
                }],
                "scheduler": {
                    "frequency": "Hour",
                    "interval": 1
                }
            }],
            "start": "2017-10-01T00:00:00Z",
            "end": "2017-10-01T05:00:00Z",
            "isPaused": false
        }
    }    
    ```

2. Om du vill skapa pipelinen: **RunSSISPackagePipeline** kör du cmdleten **New-AzDataFactoryPipeline** .

    ```powershell
    $DFPipeLine = New-AzDataFactoryPipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

### <a name="monitor-the-pipeline-run"></a>Övervaka pipelinekörningen

1. Kör **Get-AzDataFactorySlice** för att få information om alla segment i data uppsättningen för utdata * *, som är den utgående tabellen i pipelinen.

    ```powershell
    Get-AzDataFactorySlice $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```
    Observera att StartDateTime som du anger här är samma starttid som angavs i din pipeline-JSON. 
1. Kör **Get-AzDataFactoryRun** för att hämta information om aktivitets körningar för en speciell sektor.

    ```powershell
    Get-AzDataFactoryRun $df -DatasetName sprocsampleout -StartDateTime 2017-10-01T00:00:00Z
    ```

    Du kan köra denna cmdlet tills du ser att sektorn har statusen **Klar** eller **Misslyckades**. 

    Du kan köra följande fråga mot SSISDB-databasen på servern för att kontrol lera att paketet körs. 

    ```sql
    select * from catalog.executions
    ```

## <a name="next-steps"></a>Nästa steg
Mer information om den lagrade procedur aktiviteten finns i artikeln om [lagrade procedurer](data-factory-stored-proc-activity.md) .