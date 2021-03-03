---
title: Kopiera data stegvis med Ändringsspårning med PowerShell
description: I den här självstudien skapar du en Azure Data Factory pipeline som kopierar delta data stegvis från flera tabeller i en SQL Server databas till Azure SQL Database.
ms.author: yexu
author: dearandyxu
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019, devx-track-azurepowershell
ms.date: 02/18/2021
ms.openlocfilehash: a31f8ce227175e65f7119c25dcc575dc6fafdcd4
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101727761"
---
# <a name="incrementally-load-data-from-azure-sql-database-to-azure-blob-storage-using-change-tracking-information-using-powershell"></a>Läs in data stegvis från Azure SQL Database till Azure Blob Storage använda ändrings spårnings information med hjälp av PowerShell

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här självstudien skapar du en Azure-datafabrik med en pipeline som läser in delta data baserat på **ändrings spårnings** information i käll databasen i Azure SQL Database till en Azure Blob Storage.  

I den här självstudiekursen får du göra följande:

> [!div class="checklist"]
> * Förbereda källdatalagret
> * Skapa en datafabrik.
> * Skapa länkade tjänster.
> * Skapa datamängder för källa, mottagare och spårning av ändringar.
> * Skapa, kör och övervaka den fullständiga kopieringspipelinen
> * Lägga till eller uppdatera data i källtabellen
> * Skapa, kör och övervaka den inkrementella kopieringspipelinen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="overview"></a>Översikt
I en dataintegrationslösning är stegvis inläsning av data efter den första datainläsningen ett vanligt scenario. I vissa fall kan ändrade data inom en period i ditt källdatalager enkelt delas upp (till exempel LastModifyTime, CreationTime). I vissa fall finns det inget uttryckligt sätt att identifiera deltadata från förra gången du bearbetade data. Tekniken för Ändringsspårning som stöds av datakällor som Azure SQL Database och SQL Server kan användas för att identifiera deltadata.  I den här självstudien beskrivs hur du använder Azure Data Factory med SQL-ändringsspårningstekniken för att inkrementellt läsa in deltadata från Azure SQL Database till Azure Blob Storage.  Mer konkret information om SQL-ändringsspårningstekniken finns i [Change tracking in SQL Server](/sql/relational-databases/track-changes/about-change-tracking-sql-server) (Ändringsspårning i SQL Server).

## <a name="end-to-end-workflow"></a>Arbetsflödet slutpunkt till slutpunkt
Här följer de typiska arbetsflödesstegen från slutpunkt till slutpunkt för att stegvis läsa in data med ändringsspårningstekniken.

> [!NOTE]
> Både Azure SQL Database och SQL Server stöder ändringsspårningsteknik. I den här självstudien används Azure SQL Database som källdatalager. Du kan också använda en SQL Server instans.

1. **Inledande inläsning av historiska data** (kör en gång):
    1. Aktivera Ändringsspårning teknik i käll databasen i Azure SQL Database.
    2. Hämta det ursprungliga värdet för SYS_CHANGE_VERSION i databasen som bas linje för att samla in ändrade data.
    3. Läs in fullständiga data från käll databasen i Azure Blob Storage.
2. **Inkrementell inläsning av deltadata enligt ett schema** (kör regelbundet efter den första datainläsningen):
    1. Hämta de gamla och nya SYS_CHANGE_VERSION-värdena.
    2. Läs in deltadata genom att ansluta till primärnycklarna för de ändrade raderna (mellan två SYS_CHANGE_VERSION-värden) från **sys.change_tracking_tables** med data i **källtabellen** och flytta sedan deltadata till målet.
    3. Uppdatera SYS_CHANGE_VERSION för deltainläsning nästa gång.

## <a name="high-level-solution"></a>Lösning på hög nivå
I den här självstudien skapar du två pipelines som utför följande två åtgärder:  

1. **Inledande inläsning:** du skapar en pipeline med en kopieringsaktivitet som kopierar alla data från källdatalagret (Azure SQL Database) till måldatalagret (Azure Blob Storage).

    ![Fullständig inläsning av data](media/tutorial-incremental-copy-change-tracking-feature-powershell/full-load-flow-diagram.png)
1.  **Stegvis inläsning:** du skapar en pipeline med följande aktiviteter och kör den med jämna mellanrum.
    1. Skapa **två lookupaktiviteter** för att hämta den nya och gamla SYS_CHANGE_VERSION från Azure SQL Database och skicka den till kopieringsaktiviteten.
    2. Skapa **en kopieringsaktivitet** för att kopiera de infogade/uppdaterade/borttagna data mellan de två SYS_CHANGE_VERSION-värdena från Azure SQL Database till Azure Blob Storage.
    3. Skapa **en lagrad proceduraktivitet** för att uppdatera värdet för SYS_CHANGE_VERSION för nästa pipelinekörning.

    ![Steg för att läsa in flödesdiagram](media/tutorial-incremental-copy-change-tracking-feature-powershell/incremental-load-flow-diagram.png)


Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* Azure PowerShell. Installera de senaste Azure PowerShell-modulerna enligt instruktionerna i [Installera och konfigurera Azure PowerShell](/powershell/azure/install-Az-ps).
* **Azure SQL Database**. Du använder databasen som **källa** för datalagringen. Om du inte har en databas i Azure SQL Database går du till artikeln [skapa en databas i Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md) för steg för att skapa en.
* **Azure Storage konto**. Du kan använda blob-lagringen som **mottagare** för datalagringen. Om du inte har ett Azure Storage-konto kan du läsa artikeln [skapa ett lagrings konto](../storage/common/storage-account-create.md) för steg för att skapa ett. Skapa en container med namnet **adftutorial**. 

### <a name="create-a-data-source-table-in-your-database"></a>Skapa en data käll tabell i databasen

1. Starta **SQL Server Management Studio** och anslut till SQL Database.
2. I **Server Explorer** högerklickar du på **databasen** och väljer **Ny fråga**.
3. Kör följande SQL-kommando mot databasen för att skapa en tabell med namnet `data_source_table` som data käll arkiv.  

    ```sql
    create table data_source_table
    (
        PersonID int NOT NULL,
        Name varchar(255),
        Age int
        PRIMARY KEY (PersonID)
    );

    INSERT INTO data_source_table
        (PersonID, Name, Age)
    VALUES
        (1, 'aaaa', 21),
        (2, 'bbbb', 24),
        (3, 'cccc', 20),
        (4, 'dddd', 26),
        (5, 'eeee', 22);

    ```
4. Aktivera mekanismen för **ändringsspårning** på din databas och källtabellen (data_source_table) genom att köra följande SQL-fråga:

    > [!NOTE]
    > - Ersätt &lt; ditt databas namn &gt; med namnet på databasen som har data_source_table.
    > - Ändrade data sparas i två dagar i det aktuella exemplet. Om du läser in ändrade data ungefär var tredje dag eller oftare inkluderas inte en del ändrade data.  Du måste antingen ändra värdet för CHANGE_RETENTION till ett större antal. Du kan också se till att din period för att läsa in ändrade data är inom två dagar. Mer information finns i [Enable change tracking for a database](/sql/relational-databases/track-changes/enable-and-disable-change-tracking-sql-server#enable-change-tracking-for-a-database) (Aktivera ändringsspårning för en databas)

    ```sql
    ALTER DATABASE <your database name>
    SET CHANGE_TRACKING = ON  
    (CHANGE_RETENTION = 2 DAYS, AUTO_CLEANUP = ON)  

    ALTER TABLE data_source_table
    ENABLE CHANGE_TRACKING  
    WITH (TRACK_COLUMNS_UPDATED = ON)
    ```
5. Skapa en ny tabell och lagra ChangeTracking_version med ett standardvärde genom att köra följande fråga:

    ```sql
    create table table_store_ChangeTracking_version
    (
        TableName varchar(255),
        SYS_CHANGE_VERSION BIGINT,
    );

    DECLARE @ChangeTracking_version BIGINT
    SET @ChangeTracking_version = CHANGE_TRACKING_CURRENT_VERSION();  

    INSERT INTO table_store_ChangeTracking_version
    VALUES ('data_source_table', @ChangeTracking_version)
    ```

    > [!NOTE]
    > Om data inte ändras när du har aktiverat spårning för SQL-databasen är värdet för ändringsspårningsversionen 0.
6. Kör följande fråga för att skapa en lagrad procedur i databasen. Pipelinen anropar den här lagrade proceduren för att uppdatera ändringsspårningsversionen i tabellen som du skapade i föregående steg.

    ```sql
    CREATE PROCEDURE Update_ChangeTracking_Version @CurrentTrackingVersion BIGINT, @TableName varchar(50)
    AS

    BEGIN

    UPDATE table_store_ChangeTracking_version
    SET [SYS_CHANGE_VERSION] = @CurrentTrackingVersion
    WHERE [TableName] = @TableName

    END    
    ```

### <a name="azure-powershell"></a>Azure PowerShell
Installera de senaste Azure PowerShell-modulerna genom att följa anvisningarna i [så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/install-Az-ps).

## <a name="create-a-data-factory"></a>Skapa en datafabrik
1. Definiera en variabel för resursgruppens namn som du kan använda senare i PowerShell-kommandon. Kopiera följande kommandotext till PowerShell, ange ett namn för [Azure-resursgruppen](../azure-resource-manager/management/overview.md), sätt dubbla citattecken omkring namnet och kör sedan kommandot. Exempel: `"adfrg"`. 
   
     ```powershell
    $resourceGroupName = "ADFTutorialResourceGroup";
    ```

    Om resursgruppen redan finns behöver du kanske inte skriva över den. Ge variabeln `$resourceGroupName` ett annat värde och kör kommandot igen
2. Definiera en variabel för datafabrikens plats:

    ```powershell
    $location = "East US"
    ```
3. Kör följande kommando för att skapa en Azure-resursgrupp:

    ```powershell
    New-AzResourceGroup $resourceGroupName $location
    ```
    Om resursgruppen redan finns behöver du kanske inte skriva över den. Ge variabeln `$resourceGroupName` ett annat värde och kör kommandot igen.
3. Definiera en variabel för datafabrikens namn.

    > [!IMPORTANT]
    >  Uppdateringen av datafabrikens namn måste vara unikt globalt.  

    ```powershell
    $dataFactoryName = "IncCopyChgTrackingDF";
    ```
5. Skapa data fabriken genom att köra följande **set-AzDataFactoryV2-** cmdlet:

    ```powershell       
    Set-AzDataFactoryV2 -ResourceGroupName $resourceGroupName -Location $location -Name $dataFactoryName
    ```

Observera följande punkter:

* Namnet på Azure Data Factory måste vara globalt unikt. Om du får följande felmeddelande ändrar du namnet och försöker igen.

    ```
    The specified Data Factory name 'ADFIncCopyChangeTrackingTestFactory' is already in use. Data Factory names must be globally unique.
    ```
* Om du vill skapa Data Factory-instanser måste det användarkonto du använder för att logga in på Azure vara medlem av rollerna **deltagare** eller **ägare**, eller vara **administratör** för Azure-prenumerationen.
* Om du vill se en lista med Azure-regioner där Data Factory är tillgängligt för närvarande markerar du de regioner du är intresserad av på följande sida. Expandera sedan **Analytics** och leta rätt på **Data Factory**: [Tillgängliga produkter per region](https://azure.microsoft.com/global-infrastructure/services/). Datalagren (Azure Storage, Azure SQL Database osv.) och beräkningarna (HDInsight osv.) som används i Data Factory kan finnas i andra regioner.


## <a name="create-linked-services"></a>Skapa länkade tjänster
Du kan skapa länkade tjänster i en datafabrik för att länka ditt datalager och beräkna datafabrik-tjänster. I det här avsnittet skapar du länkade tjänster till ditt Azure Storage-konto och din databas i Azure SQL Database.

### <a name="create-azure-storage-linked-service"></a>Skapa en länkad Azure-lagringstjänst.
I det här steget länkar du ditt Azure-lagringskonto till datafabriken.

1. Skapa en JSON-fil med namnet **AzureStorageLinkedService.json** i mappen **C:\ADFTutorials\IncCopyChangeTrackingTutorial** med följande innehåll: (Skapa mappen om den inte redan finns). Ersätt `<accountName>`, `<accountKey>` med namnet och nyckeln för ditt Azure-lagringskonto innan du sparar filen.

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
2. I **Azure PowerShell** växlar du till mappen **C:\ADFTutorials\IncCopyChangeTrackingTutorial**
3. Kör cmdleten **set-AzDataFactoryV2LinkedService** för att skapa den länkade tjänsten: **AzureStorageLinkedService**. I följande exempel skickar du värden för parametrarna **ResourceGroupName** och **DataFactoryName**.

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureStorageLinkedService" -File ".\AzureStorageLinkedService.json"
    ```

    Här är exempel på utdata:

    ```console
    LinkedServiceName : AzureStorageLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureStorageLinkedService
    ```

### <a name="create-azure-sql-database-linked-service"></a>Skapa länkad Azure SQL Database-tjänst.
I det här steget länkar du databasen till data fabriken.

1. Skapa en JSON-fil med namnet **AzureSQLDatabaseLinkedService.jspå** i **C:\ADFTutorials\IncCopyChangeTrackingTutorial** -mappen med följande innehåll: Ersätt **&lt; serverns &gt; &lt; databas namn &gt; , &lt; användar-ID &gt; och &lt; lösen ord &gt;** med namnet på servern, namnet på din databas, ditt användar-ID och lösen ord innan du sparar filen.

    ```json
    {
        "name": "AzureSQLDatabaseLinkedService",
        "properties": {
            "type": "AzureSqlDatabase",
            "typeProperties": {
                "connectionString": "Server = tcp:<server>.database.windows.net,1433;Initial Catalog=<database name>; Persist Security Info=False; User ID=<user name>; Password=<password>; MultipleActiveResultSets = False; Encrypt = True; TrustServerCertificate = False; Connection Timeout = 30;"
            }
        }
    }
    ```
2. I **Azure PowerShell** kör du cmdleten **set-AzDataFactoryV2LinkedService** för att skapa den länkade tjänsten: **AzureSQLDatabaseLinkedService**.

    ```powershell
    Set-AzDataFactoryV2LinkedService -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "AzureSQLDatabaseLinkedService" -File ".\AzureSQLDatabaseLinkedService.json"
    ```

    Här är exempel på utdata:

    ```console
    LinkedServiceName : AzureSQLDatabaseLinkedService
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlDatabaseLinkedService
    ```

## <a name="create-datasets"></a>Skapa datauppsättningar
I det här steget skapar du databaser för att representera datakälla, datamål. och plats för att lagra SYS_CHANGE_VERSION.

### <a name="create-a-source-dataset"></a>Skapa en källdatauppsättning
I det här steget skapar du en datamängd för att representera källdata.

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

2.  Kör cmdleten Set-AzDataFactoryV2Dataset för att skapa data uppsättningen: SourceDataset

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SourceDataset" -File ".\SourceDataset.json"
    ```

    Här är exempel på utdata från cmdleten:

    ```json
    DatasetName       : SourceDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

### <a name="create-a-sink-dataset"></a>Skapa en källdatauppsättning
I det här steget skapar du en datamängd för att representera data som kopieras från källdatalagret.

1. Skapa en JSON-fil med namnet SinkDataset.json i samma mapp med följande innehåll:

    ```json
    {
        "name": "SinkDataset",
        "properties": {
            "type": "AzureBlob",
            "typeProperties": {
                "folderPath": "adftutorial/incchgtracking",
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

    Du skapar containern adftutorial i din Azure Blob Storage som en del av förutsättningarna. Skapa containern om den inte finns (eller) ställ in den för namnet på en befintlig. I den här självstudien genereras filnamnet dynamiskt med uttrycket: @CONCAT('Incremental-', pipeline().RunId, '.txt').
2.  Kör cmdleten Set-AzDataFactoryV2Dataset för att skapa data uppsättningen: SinkDataset

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "SinkDataset" -File ".\SinkDataset.json"
    ```

    Här är exempel på utdata från cmdleten:

    ```json
    DatasetName       : SinkDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureBlobDataset
    ```

### <a name="create-a-change-tracking-dataset"></a>Skapa en datamängd för ändringsspårning
I det här steget skapar du en datauppsättning för att lagra ändringsspårningsversionen.  

1. Skapa en JSON-fil med namnet ChangeTrackingDataset.json i samma mapp med följande innehåll:

    ```json
    {
        "name": " ChangeTrackingDataset",
        "properties": {
            "type": "AzureSqlTable",
            "typeProperties": {
                "tableName": "table_store_ChangeTracking_version"
            },
            "linkedServiceName": {
                "referenceName": "AzureSQLDatabaseLinkedService",
                "type": "LinkedServiceReference"
            }
        }
    }
    ```

    Du kan skapa tabellen table_store_ChangeTracking_version som en del av förutsättningarna.
2.  Kör cmdleten Set-AzDataFactoryV2Dataset för att skapa data uppsättningen: ChangeTrackingDataset

    ```powershell
    Set-AzDataFactoryV2Dataset -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "ChangeTrackingDataset" -File ".\ChangeTrackingDataset.json"
    ```

    Här är exempel på utdata från cmdleten:

    ```json
    DatasetName       : ChangeTrackingDataset
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Structure         :
    Properties        : Microsoft.Azure.Management.DataFactory.Models.AzureSqlTableDataset
    ```

## <a name="create-a-pipeline-for-the-full-copy"></a>Skapa en pipeline för hela kopian
I det här steget skapar du en pipeline med en kopieringsaktivitet som kopierar alla data från källdatalagret (Azure SQL Database) till måldatalagret (Azure Blob Storage).

1. Skapa en JSON-fil med namnet FullCopyPipeline.json i samma mapp med följande innehåll:

    ```json
    {
        "name": "FullCopyPipeline",
        "properties": {
            "activities": [{
                "name": "FullCopyActivity",
                "type": "Copy",
                "typeProperties": {
                    "source": {
                        "type": "SqlSource"
                    },
                    "sink": {
                        "type": "BlobSink"
                    }
                },

                "inputs": [{
                    "referenceName": "SourceDataset",
                    "type": "DatasetReference"
                }],
                "outputs": [{
                    "referenceName": "SinkDataset",
                    "type": "DatasetReference"
                }]
            }]
        }
    }
    ```
2. Kör cmdleten Set-AzDataFactoryV2Pipeline för att skapa pipelinen: FullCopyPipeline.

   ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "FullCopyPipeline" -File ".\FullCopyPipeline.json"
   ```

   Här är exempel på utdata:

   ```console
    PipelineName      : FullCopyPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Activities        : {FullCopyActivity}
    Parameters        :
   ```

### <a name="run-the-full-copy-pipeline"></a>Kör den fullständiga kopieringspipelinen
Kör pipelinen: **FullCopyPipeline** med cmdleten **Invoke-AzDataFactoryV2Pipeline** .

```powershell
Invoke-AzDataFactoryV2Pipeline -PipelineName "FullCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName        
```

### <a name="monitor-the-full-copy-pipeline"></a>Övervaka den fullständiga kopieringspipelinen

1. Logga in på [Azure Portal](https://portal.azure.com).
2. Klicka på **Alla tjänster**, sök med nyckelordet `data factories` och välj **Datafabriker**.

    ![Menyn Datafabriker](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-data-factories-menu-1.png)
3. Sök efter **din datafabrik** i listan med datafabriker och välj den så att du öppnar sidan Datafabrik.

    ![Sök efter din datafabrik](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-search-data-factory-2.png)
4. På sidan Datafabrik klickar du på panelen **Övervaka och hantera**.

    ![Ikonen Övervaka och hantera](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-monitor-manage-tile-3.png)    
5. **Data integrerings programmet** startar på en separat flik. Du kan se alla **pipeline-körningar** och deras status. Lägg i följande exempel märke till att statusen för pipelinekörningen är **Lyckades**. Du kan kontrollera parametrarna som skickats till pipelinen genom att klicka på länken i kolumnen **Parametrar**. Om det uppstod ett fel ser du en länk i kolumnen **Fel**. Klicka på länken i kolumnen **Åtgärder**.

    ![Skärm bild som visar pipeline-körningar för en data fabrik.](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-pipeline-runs-4.png)    
6. När du klickar på länken i kolumnen **Åtgärder** ser du följande sida som visar alla **aktivitetskörningar** för pipelinen.

    ![Skärm bild som visar aktivitets körningar för en data fabrik med pipeline-länken som kallas.](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-activity-runs-5.png)
7. Om du vill växla tillbaka till vyn med **pipelinekörningar** klickar du på **Pipelines** enligt bilden.


### <a name="review-the-results"></a>Granska resultaten
Du ser en fil som heter `incremental-<GUID>.txt` i mappen `incchgtracking` i containern `adftutorial`.

![Utdatafil från fullständig kopia](media/tutorial-incremental-copy-change-tracking-feature-powershell/full-copy-output-file.png)

Filen ska ha data från databasen:

```
1,aaaa,21
2,bbbb,24
3,cccc,20
4,dddd,26
5,eeee,22
```

## <a name="add-more-data-to-the-source-table"></a>Lägga till mer data i källtabellen

Kör följande fråga mot databasen för att lägga till en rad och uppdatera en rad.

```sql
INSERT INTO data_source_table
(PersonID, Name, Age)
VALUES
(6, 'new','50');


UPDATE data_source_table
SET [Age] = '10', [name]='update' where [PersonID] = 1

```

## <a name="create-a-pipeline-for-the-delta-copy"></a>Skapa en pipeline för deltakopian
I det här steget skapar du en pipeline med följande aktiviteter och kör den med jämna mellanrum. **Lookupaktiviteterna** hämtar den nya och gamla SYS_CHANGE_VERSION från Azure SQL Database och skickar den till kopieringsaktiviteten. **Kopieringsaktiviteten** kopierar infogade/uppdaterade/borttagna data mellan de två SYS_CHANGE_VERSION-värdena från Azure SQL Database till Azure Blob Storage. Den **lagrade proceduraktivitetsuppdateringarna** uppdaterar värdet för SYS_CHANGE_VERSION för nästa pipelinekörning.

1. Skapa en JSON-fil med namnet IncrementalCopyPipeline.json i samma mapp med följande innehåll:

    ```json
    {
        "name": "IncrementalCopyPipeline",
        "properties": {
            "activities": [
                {
                    "name": "LookupLastChangeTrackingVersionActivity",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "select * from table_store_ChangeTracking_version"
                        },
                        "dataset": {
                            "referenceName": "ChangeTrackingDataset",
                            "type": "DatasetReference"
                        }
                    }
                },
                {
                    "name": "LookupCurrentChangeTrackingVersionActivity",
                    "type": "Lookup",
                    "typeProperties": {
                        "source": {
                            "type": "SqlSource",
                            "sqlReaderQuery": "SELECT CHANGE_TRACKING_CURRENT_VERSION() as CurrentChangeTrackingVersion"
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
                            "sqlReaderQuery": "select data_source_table.PersonID,data_source_table.Name,data_source_table.Age, CT.SYS_CHANGE_VERSION, SYS_CHANGE_OPERATION from data_source_table RIGHT OUTER JOIN CHANGETABLE(CHANGES data_source_table, @{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.SYS_CHANGE_VERSION}) as CT on data_source_table.PersonID = CT.PersonID where CT.SYS_CHANGE_VERSION <= @{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion}"
                        },
                        "sink": {
                            "type": "BlobSink"
                        }
                    },
                    "dependsOn": [
                        {
                            "activity": "LookupLastChangeTrackingVersionActivity",
                            "dependencyConditions": [
                                "Succeeded"
                            ]
                        },
                        {
                            "activity": "LookupCurrentChangeTrackingVersionActivity",
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
                    "name": "StoredProceduretoUpdateChangeTrackingActivity",
                    "type": "SqlServerStoredProcedure",
                    "typeProperties": {
                        "storedProcedureName": "Update_ChangeTracking_Version",
                        "storedProcedureParameters": {
                            "CurrentTrackingVersion": {
                                "value": "@{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion}",
                                "type": "INT64"
                            },
                            "TableName": {
                                "value": "@{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.TableName}",
                                "type": "String"
                            }
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

2. Kör cmdleten Set-AzDataFactoryV2Pipeline för att skapa pipelinen: FullCopyPipeline.

   ```powershell
    Set-AzDataFactoryV2Pipeline -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName -Name "IncrementalCopyPipeline" -File ".\IncrementalCopyPipeline.json"
   ```

   Här är exempel på utdata:

   ```console
    PipelineName      : IncrementalCopyPipeline
    ResourceGroupName : ADFTutorialResourceGroup
    DataFactoryName   : IncCopyChgTrackingDF
    Activities        : {LookupLastChangeTrackingVersionActivity, LookupCurrentChangeTrackingVersionActivity, IncrementalCopyActivity, StoredProceduretoUpdateChangeTrackingActivity}
    Parameters        :
   ```

### <a name="run-the-incremental-copy-pipeline"></a>Kör den inkrementella kopieringspipelinen
Kör pipelinen: **IncrementalCopyPipeline** med cmdleten **Invoke-AzDataFactoryV2Pipeline** .

```powershell
Invoke-AzDataFactoryV2Pipeline -PipelineName "IncrementalCopyPipeline" -ResourceGroup $resourceGroupName -dataFactoryName $dataFactoryName     
```


### <a name="monitor-the-incremental-copy-pipeline"></a>Övervaka den inkrementella kopieringspipelinen
1. I **dataintegrationsprogrammet** uppdaterar du vyn för **pipelinekörningar**. Kontrollera att du ser IncrementalCopyPipeline i listan. Klicka på länken i kolumnen **Åtgärder**.  

    ![Skärm bild som visar pipeline-körningar för en data fabrik, inklusive din pipeline.](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-pipeline-runs-6.png)    
2. När du klickar på länken i kolumnen **Åtgärder** ser du följande sida som visar alla **aktivitetskörningar** för pipelinen.

    ![Skärm bild som visar pipeline-körningar för en data fabrik med flera markerade som slutförda.](media/tutorial-incremental-copy-change-tracking-feature-powershell/monitor-activity-runs-7.png)
3. Om du vill växla tillbaka till vyn med **pipelinekörningar** klickar du på **Pipelines** enligt bilden.

### <a name="review-the-results"></a>Granska resultaten
Du ser den andra filen i mappen `incchgtracking` i containern `adftutorial`.

![Utdatafil från inkrementell säkerhetskopia](media/tutorial-incremental-copy-change-tracking-feature-powershell/incremental-copy-output-file.png)

Filen bör bara ha delta data från databasen. Posten med `U` är den uppdaterade raden i databasen och `I` är den tillagda raden.

```
1,update,10,2,U
6,new,50,1,I
```
De första tre kolumnerna är ändrade data från data_source_table. De sista två kolumnerna är metadata från tabellen med ändringsspårningssystem. Den fjärde kolumnen är SYS_CHANGE_VERSION för varje ändrad rad. Den femte kolumnen är åtgärden:  U = uppdatera, I = infoga.  Mer information om ändringsspårningsinformation finns i [CHANGETABLE](/sql/relational-databases/system-functions/changetable-transact-sql).

```
==================================================================
PersonID Name    Age    SYS_CHANGE_VERSION    SYS_CHANGE_OPERATION
==================================================================
1        update  10            2                                 U
6        new     50            1                                 I
```


## <a name="next-steps"></a>Nästa steg
Fortsätt till följande självstudie och lär dig mer om hur du kopierar nya och ändrade filer endast baserat på deras LastModifiedDate:

> [!div class="nextstepaction"]
>[Kopiera nya filer efter lastmodifieddate](tutorial-incremental-copy-lastmodified-copy-data-tool.md)
