---
title: Köra SSIS-paket med lagrad procedur aktivitet – Azure
description: Den här artikeln beskriver hur du kör ett SQL Server Integration Services-paket (SSIS) i en Azure Data Factory-pipeline med hjälp av den lagrade procedur aktiviteten.
author: swinarko
ms.service: data-factory
ms.devlang: powershell
ms.topic: conceptual
ms.date: 07/09/2020
ms.author: sawinark
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 6260606fe56d4dfc6bac93e04e726b5fd3298777
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100391515"
---
# <a name="run-an-ssis-package-with-the-stored-procedure-activity-in-azure-data-factory"></a>Köra ett SSIS-paket med aktiviteten Lagrad procedur i Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Den här artikeln beskriver hur du kör ett SSIS-paket i en Azure Data Factory pipeline med hjälp av en lagrad procedur aktivitet. 

## <a name="prerequisites"></a>Förutsättningar

### <a name="azure-sql-database"></a>Azure SQL Database 
I genom gången i den här artikeln används Azure SQL Database som värd för SSIS-katalogen. Du kan också använda en hanterad Azure SQL-instans.

## <a name="create-an-azure-ssis-integration-runtime"></a>Skapa en Azure-SSIS Integration Runtime
Skapa en Azure-SSIS integration Runtime om du inte har en genom att följa steg-för-steg-instruktionen i [självstudien: Distribuera SSIS-paket](./tutorial-deploy-ssis-packages-azure.md).

## <a name="data-factory-ui-azure-portal"></a>Data Factory användar gränssnitt (Azure Portal)
I det här avsnittet ska du använda Data Factory användar gränssnitt för att skapa en Data Factory-pipeline med en lagrad procedur aktivitet som anropar ett SSIS-paket.

### <a name="create-a-data-factory"></a>Skapa en datafabrik
Det första steget är att skapa en data fabrik med hjälp av Azure Portal. 

1. Starta webbläsaren **Microsoft Edge** eller **Google Chrome**. Användargränssnittet för Data Factory stöds för närvarande bara i webbläsarna Microsoft Edge och Google Chrome.
2. Navigera till [Azure Portal](https://portal.azure.com). 
3. Klicka på **Ny** på den vänstra menyn, klicka på **Data + Analys**, och klicka på **Data Factory**. 
   
   ![Nytt->DataFactory](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory-menu.png)
2. På sidan **Ny datafabrik** anger du **ADFTutorialDataFactory** som **namn**. 
      
     ![Sidan Ny datafabrik](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-azure-data-factory.png)
 
   Namnet på Azure Data Factory måste vara **globalt unikt**. Om följande fel visas för namnfältet ändrar du namnet på datafabriken (till exempel dittnamnADFTutorialDataFactory). Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
  
     ![Namnet är inte tillgängligt – fel](./media/how-to-invoke-ssis-package-stored-procedure-activity/name-not-available-error.png)
3. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i. 
4. För **resursgruppen** utför du något av följande steg:
     
   - Välj **Använd befintlig** och välj en befintlig resurs grupp i den nedrullningsbara listan. 
   - Välj **Skapa ny** och ange namnet på en resurs grupp.   
         
     Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/management/overview.md).  
4. Välj **V2** för **versionen**.
5. Välj **plats** för datafabriken. Endast platser som stöds av Data Factory visas i listrutan. Datalagren (Azure Storage, Azure SQL Database osv.) och beräkningarna (HDInsight osv.) som används i Data Factory kan finnas på andra platser.
6. Välj **fäst till instrumentpanelen**.     
7. Klicka på **Skapa**.
8. På instrument panelen visas följande panel med status: **distribuerar Data Factory**. 

     ![panelen distribuerar datafabrik](media//how-to-invoke-ssis-package-stored-procedure-activity/deploying-data-factory.png)
9. När datafabriken har skapats visas sidan **Datafabrik** som på bilden.
   
     ![Datafabrikens startsida](./media/how-to-invoke-ssis-package-stored-procedure-activity/data-factory-home-page.png)
10. Klicka på rutan **Författare och övervakare** för att starta användargränssnittet för Azure Data Factory på en separat flik. 

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Skapa en pipeline med en lagrad procedur aktivitet
I det här steget använder du Data Factory gränssnittet för att skapa en pipeline. Du lägger till en lagrad procedur aktivitet i pipelinen och konfigurerar den att köra SSIS-paketet med hjälp av den sp_executesql lagrade proceduren. 

1. På sidan kom igång klickar du på **skapa pipeline**: 

    ![Sidan Kom igång](./media/how-to-invoke-ssis-package-stored-procedure-activity/get-started-page.png)
2. I verktygs lådan **aktiviteter** expanderar du **Allmänt** och drar och släpper **lagrad procedur** aktivitet till pipelinens design yta. 

    ![Dra-och-släpp-lagrad procedur aktivitet](./media/how-to-invoke-ssis-package-stored-procedure-activity/drag-drop-sproc-activity.png)
3. I fönstret Egenskaper för aktiviteten lagrad procedur växlar du till fliken **SQL-konto** och klickar på **+ ny**. Du skapar en anslutning till databasen i Azure SQL Database som är värd för SSIS-katalogen (SSIDB-databasen). 
   
    ![Knapp för ny länkad tjänst](./media/how-to-invoke-ssis-package-stored-procedure-activity/new-linked-service-button.png)
4. Utför följande steg i fönstret **New Linked Service** (Ny länkad tjänst): 

    1. Välj **Azure SQL Database** för **typ**.
    2. Välj **standard** Azure integration runtime för att ansluta till den Azure SQL Database som är värd för `SSISDB` databasen.
    3. Välj den Azure SQL Database som är värd för SSISDB-databasen för fältet **Server namn** .
    4. Välj **SSISDB** som **databas namn**.
    5. För **användar namn** anger du namnet på den användare som har åtkomst till databasen.
    6. För **lösen ord** anger du användarens lösen ord. 
    7. Testa anslutningen till databasen genom att klicka på knappen **Testa anslutning** .
    8. Spara den länkade tjänsten genom att klicka på knappen **Spara** . 

        ![Skärm bild som visar processen för att lägga till en ny länkad tjänst.](./media/how-to-invoke-ssis-package-stored-procedure-activity/azure-sql-database-linked-service-settings.png)
5. I fönstret Egenskaper växlar du till fliken **lagrad procedur** från fliken **SQL-konto** och utför följande steg: 

    1. Välj **Redigera**. 
    2. I fältet **namn på lagrad procedur** anger du `sp_executesql` . 
    3. Klicka på **+ ny** i avsnittet **Parametrar för lagrad procedur** . 
    4. För parameterns **namn** anger du **stmt**. 
    5. Ange **sträng** för parameterns **typ** . 
    6. Ange följande SQL-fråga för parameterns **värde** :

        I SQL-frågan anger du rätt värden för parametrarna **folder_name**, **project_name** och **package_name** . 

        ```sql
        DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER name in SSIS Catalog>', @project_name=N'<PROJECT name in SSIS Catalog>', @package_name=N'<PACKAGE name>.dtsx', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END
        ```

        ![Länkad Azure SQL Database-tjänst](./media/how-to-invoke-ssis-package-stored-procedure-activity/stored-procedure-settings.png)
6. Verifiera pipeline-konfigurationen genom att klicka på **Verifiera** i verktygsfältet. Om du vill stänga **verifieringsrapporten för pipeline** klickar du på **>>**.

    ![Verifiera pipeline](./media/how-to-invoke-ssis-package-stored-procedure-activity/validate-pipeline.png)
7. Publicera pipelinen till Data Factory genom att klicka på knappen **publicera alla** . 

    ![Publicera](./media/how-to-invoke-ssis-package-stored-procedure-activity/publish-all-button.png)    

### <a name="run-and-monitor-the-pipeline"></a>Köra och övervaka pipelinen
I det här avsnittet aktiverar du en pipeline-körning och övervakar den sedan. 

1. Om du vill utlösa en pipeline-körning klickar du på **Utlös** i verktygsfältet och klickar sedan på **Utlös nu**. 

    ![Utlös nu](media/how-to-invoke-ssis-package-stored-procedure-activity/trigger-now.png)

2. I fönstret **Pipeline Run** (Pipelinekörning) väljer du **Slutför**. 
3. Växla till fliken **Övervaka** till vänster. Du ser pipeline-körningen och dess status tillsammans med annan information (till exempel kör Start tid). Om du vill uppdatera vyn klickar du på **Uppdatera**.

    ![Pipelinekörningar](./media/how-to-invoke-ssis-package-stored-procedure-activity/pipeline-runs.png)

3. Klicka på länken **View Activity Runs** (Visa aktivitetskörningar) i kolumnen **Åtgärder**. Du ser bara en aktivitets körning eftersom pipelinen bara har en aktivitet (lagrad procedur aktivitet).

    ![Aktivitetskörningar](./media/how-to-invoke-ssis-package-stored-procedure-activity/activity-runs.png)

4. Du kan köra följande **fråga** mot SSISDB-databasen i SQL Database för att kontrol lera att paketet körs. 

    ```sql
    select * from catalog.executions
    ```

    ![Verifiera paket körningar](./media/how-to-invoke-ssis-package-stored-procedure-activity/verify-package-executions.png)


> [!NOTE]
> Du kan också skapa en schemalagd utlösare för din pipeline så att pipelinen körs enligt ett schema (varje timme, varje dag osv.). Ett exempel finns i [skapa en data fabrik – Data Factory användar gränssnitt](quickstart-create-data-factory-portal.md#trigger-the-pipeline-on-a-schedule).

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

I det här avsnittet använder du Azure PowerShell för att skapa en Data Factory-pipeline med en lagrad procedur aktivitet som anropar ett SSIS-paket. 

Installera de senaste Azure PowerShell-modulerna enligt instruktionerna i [Installera och konfigurera Azure PowerShell](/powershell/azure/install-az-ps). 

### <a name="create-a-data-factory"></a>Skapa en datafabrik
Du kan antingen använda samma data fabrik som Azure-SSIS IR eller skapa en separat data fabrik. Följande procedur innehåller steg för att skapa en data fabrik. Du skapar en pipeline med en lagrad procedur aktivitet i den här data fabriken. Den lagrade procedur aktiviteten kör en lagrad procedur i SSISDB-databasen för att köra ditt SSIS-paket. 

1. Definiera en variabel för resursgruppens namn som du kan använda senare i PowerShell-kommandon. Kopiera följande kommandotext till PowerShell, ange ett namn för [Azure-resursgruppen](../azure-resource-manager/management/overview.md), sätt dubbla citattecken omkring namnet och kör sedan kommandot. Exempel: `"adfrg"`. 
   
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

5. Skapa data fabriken genom att köra följande **set-AzDataFactoryV2-** cmdlet med hjälp av egenskapen location och ResourceGroupName från variabeln $ResGrp: 
    
    ```powershell       
    $DataFactory = Set-AzDataFactoryV2 -ResourceGroupName $ResGrp.ResourceGroupName -Location $ResGrp.Location -Name $dataFactoryName 
    ```

Observera följande punkter:

* Namnet på Azure Data Factory måste vara globalt unikt. Om du får följande felmeddelande ändrar du namnet och försöker igen.

    ```
    The specified Data Factory name 'ADFv2QuickStartDataFactory' is already in use. Data Factory names must be globally unique.
    ```
* Om du vill skapa Data Factory-instanser måste det användarkonto du använder för att logga in på Azure vara medlem av rollerna **deltagare** eller **ägare**, eller vara **administratör** för Azure-prenumerationen.
* Om du vill se en lista med Azure-regioner där Data Factory är tillgängligt för närvarande markerar du de regioner du är intresserad av på följande sida. Expandera sedan **Analytics** och leta rätt på **Data Factory**: [Tillgängliga produkter per region](https://azure.microsoft.com/global-infrastructure/services/). Datalagren (Azure Storage, Azure SQL Database osv.) och beräkningarna (HDInsight osv.) som används i Data Factory kan finnas i andra regioner.

### <a name="create-an-azure-sql-database-linked-service"></a>Skapa en länkad Azure SQL Database-tjänst
Skapa en länkad tjänst för att länka databasen som är värd för SSIS-katalogen till din data fabrik. Data Factory använder informationen i den här länkade tjänsten för att ansluta till SSISDB-databasen och köra en lagrad procedur för att köra ett SSIS-paket. 

1. Skapa en JSON-fil med namnet **AzureSqlDatabaseLinkedService.jspå** i **C:\ADF\RunSSISPackage** -mappen med följande innehåll: 

    > [!IMPORTANT]
    > Ersätt &lt; servername &gt; , &lt; username &gt; och &lt; password &gt; med värdena för din Azure SQL Database innan du sparar filen.

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

3. Kör cmdleten **set-AzDataFactoryV2LinkedService** för att skapa den länkade tjänsten: **AzureSqlDatabaseLinkedService**. 

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "AzureSqlDatabaseLinkedService" -File ".\AzureSqlDatabaseLinkedService.json"
    ```

### <a name="create-a-pipeline-with-stored-procedure-activity"></a>Skapa en pipeline med en lagrad procedur aktivitet 
I det här steget skapar du en pipeline med en lagrad procedur aktivitet. Aktiviteten anropar den sp_executesql lagrade proceduren för att köra ditt SSIS-paket. 

1. Skapa en JSON-fil med namnet **RunSSISPackagePipeline.js** i mappen **C:\ADF\RunSSISPackage** med följande innehåll:

    > [!IMPORTANT]
    > Ersätt &lt; mapp-namn &gt; , &lt; projekt &gt; namn, &lt; paket namn &gt; med namn på mapp, projekt och paket i SSIS-katalogen innan du sparar filen. 

    ```json
    {
        "name": "RunSSISPackagePipeline",
        "properties": {
            "activities": [
                {
                    "name": "My SProc Activity",
                    "description":"Runs an SSIS package",
                    "type": "SqlServerStoredProcedure",
                    "linkedServiceName": {
                        "referenceName": "AzureSqlDatabaseLinkedService",
                        "type": "LinkedServiceReference"
                    },
                    "typeProperties": {
                        "storedProcedureName": "sp_executesql",
                        "storedProcedureParameters": {
                            "stmt": {
                                "value": "DECLARE @return_value INT, @exe_id BIGINT, @err_msg NVARCHAR(150)    EXEC @return_value=[SSISDB].[catalog].[create_execution] @folder_name=N'<FOLDER NAME>', @project_name=N'<PROJECT NAME>', @package_name=N'<PACKAGE NAME>', @use32bitruntime=0, @runinscaleout=1, @useanyworker=1, @execution_id=@exe_id OUTPUT    EXEC [SSISDB].[catalog].[set_execution_parameter_value] @exe_id, @object_type=50, @parameter_name=N'SYNCHRONIZED', @parameter_value=1    EXEC [SSISDB].[catalog].[start_execution] @execution_id=@exe_id, @retry_count=0    IF(SELECT [status] FROM [SSISDB].[catalog].[executions] WHERE execution_id=@exe_id)<>7 BEGIN SET @err_msg=N'Your package execution did not succeed for execution ID: ' + CAST(@exe_id AS NVARCHAR(20)) RAISERROR(@err_msg,15,1) END"
                            }
                        }
                    }
                }
            ]
        }
    }
    ```

2. Om du vill skapa pipelinen: **RunSSISPackagePipeline** kör du cmdleten **set-AzDataFactoryV2Pipeline** .

    ```powershell
    $DFPipeLine = Set-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -Name "RunSSISPackagePipeline" -DefinitionFile ".\RunSSISPackagePipeline.json"
    ```

    Här är exempel på utdata:

    ```
    PipelineName      : Adfv2QuickStartPipeline
    ResourceGroupName : <resourceGroupName>
    DataFactoryName   : <dataFactoryName>
    Activities        : {CopyFromBlobToBlob}
    Parameters        : {[inputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification], [outputPath, Microsoft.Azure.Management.DataFactory.Models.ParameterSpecification]}
    ```

### <a name="create-a-pipeline-run"></a>Skapa en pipelinekörning
Använd cmdleten **Invoke-AzDataFactoryV2Pipeline** för att köra pipelinen. Cmdleten samlar även in pipelinekörningens ID för kommande övervakning.

```powershell
$RunId = Invoke-AzDataFactoryV2Pipeline -DataFactoryName $DataFactory.DataFactoryName -ResourceGroupName $ResGrp.ResourceGroupName -PipelineName $DFPipeLine.Name
```

### <a name="monitor-the-pipeline-run"></a>Övervaka pipelinekörningen

Kör följande PowerShell-skript för att kontinuerligt kontrollera pipelinekörningens status tills kopieringen av data är klar. Kopiera/klistra in följande skript i PowerShell-fönstret och tryck på RETUR. 

```powershell
while ($True) {
    $Run = Get-AzDataFactoryV2PipelineRun -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -PipelineRunId $RunId

    if ($Run) {
        if ($run.Status -ne 'InProgress') {
            Write-Output ("Pipeline run finished. The status is: " +  $Run.Status)
            $Run
            break
        }
        Write-Output  "Pipeline is running...status: InProgress"
    }

    Start-Sleep -Seconds 10
}   
```

### <a name="create-a-trigger"></a>Skapa en utlösare
I föregående steg anropade du pipelinen på begäran. Du kan också skapa en schema utlösare för att köra pipelinen enligt ett schema (varje timme, varje dag osv.).

1. Skapa en JSON-fil med namnet **MyTrigger.jspå** i **C:\ADF\RunSSISPackage** -mappen med följande innehåll: 

    ```json
    {
        "properties": {
            "name": "MyTrigger",
            "type": "ScheduleTrigger",
            "typeProperties": {
                "recurrence": {
                    "frequency": "Hour",
                    "interval": 1,
                    "startTime": "2017-12-07T00:00:00-08:00",
                    "endTime": "2017-12-08T00:00:00-08:00"
                }
            },
            "pipelines": [{
                    "pipelineReference": {
                        "type": "PipelineReference",
                        "referenceName": "RunSSISPackagePipeline"
                    },
                    "parameters": {}
                }
            ]
        }
    }    
    ```
2. I **Azure PowerShell** växlar du till mappen **C:\ADF\RunSSISPackage**
3. Kör cmdleten **set-AzDataFactoryV2Trigger** som skapar utlösaren. 

    ```powershell
    Set-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" -DefinitionFile ".\MyTrigger.json"
    ```
4. Som standard är utlösaren i stoppat läge. Starta utlösaren genom att köra cmdleten **Start-AzDataFactoryV2Trigger** . 

    ```powershell
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResGrp.ResourceGroupName -DataFactoryName $DataFactory.DataFactoryName -Name "MyTrigger" 
    ```
5. Bekräfta att utlösaren har startats genom att köra cmdleten **Get-AzDataFactoryV2Trigger** . 

    ```powershell
    Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name "MyTrigger"     
    ```    
6. Kör följande kommando efter nästa timma. Om den aktuella tiden till exempel är 3:25 PM, kör du kommandot på 4 PM UTC. 
    
    ```powershell
    Get-AzDataFactoryV2TriggerRun -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -TriggerName "MyTrigger" -TriggerRunStartedAfter "2017-12-06" -TriggerRunStartedBefore "2017-12-09"
    ```

    Du kan köra följande fråga mot SSISDB-databasen i SQL Database för att kontrol lera att paketet körs. 

    ```sql
    select * from catalog.executions
    ```


## <a name="next-steps"></a>Nästa steg
Du kan också övervaka pipelinen med hjälp av Azure Portal. Stegvisa instruktioner finns i [övervaka pipelinen](quickstart-create-data-factory-resource-manager-template.md#monitor-the-pipeline).