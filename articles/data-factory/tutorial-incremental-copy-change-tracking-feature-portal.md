---
title: Kopiera data stegvis med Ändringsspårning med hjälp av Azure Portal
description: I den här självstudien skapar du en Azure Data Factory med en pipeline som läser in delta data baserat på ändrings spårnings information i käll databasen i Azure SQL Database till en Azure Blob Storage.
ms.author: yexu
author: dearandyxu
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/18/2021
ms.openlocfilehash: c79d96e016459732ce71019511fa429d62d91f9d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740145"
---
# <a name="incrementally-load-data-from-azure-sql-database-to-azure-blob-storage-using-change-tracking-information-using-the-azure-portal"></a>Läs in data stegvis från Azure SQL Database till Azure Blob Storage med hjälp av ändrings spårnings information med hjälp av Azure Portal

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här självstudien skapar du en Azure Data Factory med en pipeline som läser in delta data baserat på **ändrings spårnings** information i käll databasen i Azure SQL Database till en Azure Blob Storage.  

I den här självstudiekursen får du göra följande:

> [!div class="checklist"]
> * Förbereda källdatalagret
> * Skapa en datafabrik.
> * Skapa länkade tjänster.
> * Skapa datamängder för källa, mottagare och spårning av ändringar.
> * Skapa, kör och övervaka den fullständiga kopieringspipelinen
> * Lägga till eller uppdatera data i källtabellen
> * Skapa, kör och övervaka den inkrementella kopieringspipelinen

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
    3. Läs in deltadata genom att ansluta till primärnycklarna för de ändrade raderna (mellan två SYS_CHANGE_VERSION-värden) från **sys.change_tracking_tables** med data i **källtabellen** och flytta sedan deltadata till målet.
    4. Uppdatera SYS_CHANGE_VERSION för deltainläsning nästa gång.

## <a name="high-level-solution"></a>Lösning på hög nivå
I den här självstudien skapar du två pipelines som utför följande två åtgärder:  

1. **Inledande inläsning:** du skapar en pipeline med en kopieringsaktivitet som kopierar alla data från källdatalagret (Azure SQL Database) till måldatalagret (Azure Blob Storage).

    ![Fullständig inläsning av data](media/tutorial-incremental-copy-change-tracking-feature-portal/full-load-flow-diagram.png)
1.  **Stegvis inläsning:** du skapar en pipeline med följande aktiviteter och kör den med jämna mellanrum.
    1. Skapa **två lookupaktiviteter** för att hämta den nya och gamla SYS_CHANGE_VERSION från Azure SQL Database och skicka den till kopieringsaktiviteten.
    2. Skapa **en kopieringsaktivitet** för att kopiera de infogade/uppdaterade/borttagna data mellan de två SYS_CHANGE_VERSION-värdena från Azure SQL Database till Azure Blob Storage.
    3. Skapa **en lagrad proceduraktivitet** för att uppdatera värdet för SYS_CHANGE_VERSION för nästa pipelinekörning.

    ![Steg för att läsa in flödesdiagram](media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-load-flow-diagram.png)


Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="prerequisites"></a>Förutsättningar
* **Azure SQL Database**. Du använder databasen som **källa** för datalagringen. Om du inte har en databas i Azure SQL Database går du till artikeln [skapa en databas i Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md) för steg för att skapa en.
* **Azure Storage konto**. Du kan använda blob-lagringen som **mottagare** för datalagringen. Om du inte har ett Azure Storage-konto kan du läsa artikeln [skapa ett lagrings konto](../storage/common/storage-account-create.md) för steg för att skapa ett. Skapa en container med namnet **adftutorial**. 

### <a name="create-a-data-source-table-in-azure-sql-database"></a>Skapa en data käll tabell i Azure SQL Database

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
    > - Ersätt &lt; databas namnet &gt; med namnet på databasen i Azure SQL Database som har data_source_table.
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

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Installera de senaste Azure PowerShell-modulerna genom att följa anvisningarna i [så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/install-Az-ps).

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Starta webbläsaren **Microsoft Edge** eller **Google Chrome**. Användargränssnittet för Data Factory stöds för närvarande bara i webbläsarna Microsoft Edge och Google Chrome.
1. På den vänstra menyn väljer du **skapa en resurs**  >  **data och analys**  >  **Data Factory**:

   ![Valet Data Factory i fönstret Nytt](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. På sidan **Ny datafabrik** anger du **ADFTutorialDataFactory** som **namn**.

     ![Sidan Ny datafabrik](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-azure-data-factory.png)

   Namnet på Azure Data Factory måste vara **globalt unikt**. Om följande fel returneras ändrar du namnet på datafabriken (till exempel dittnamnADFTutorialDataFactory) och provar att skapa fabriken igen. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.

   *Data fabriks namnet "ADFTutorialDataFactory" är inte tillgängligt*
3. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i.
4. För **resursgruppen** utför du något av följande steg:

      - Välj **Använd befintlig** och välj en befintlig resurs grupp i den nedrullningsbara listan.
      - Välj **Skapa ny** och ange namnet på en resurs grupp.   
         
        Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/management/overview.md).  
4. Välj **V2 (förhandsgranskning)** för **versionen**.
5. Välj **plats** för datafabriken. Endast platser som stöds visas i listrutan. Datalagren (Azure Storage, Azure SQL Database osv.) och beräkningarna (HDInsight osv.) som används i Data Factory kan finnas i andra regioner.
6. Välj **fäst till instrumentpanelen**.     
7. Klicka på **Skapa**.      
8. På instrument panelen visas följande panel med status: **distribuerar Data Factory**.

    ![panelen distribuerar datafabrik](media/tutorial-incremental-copy-change-tracking-feature-portal/deploying-data-factory.png)
9. När datafabriken har skapats visas sidan **Datafabrik** som på bilden.

   ![Datafabrikens startsida](./media/tutorial-incremental-copy-change-tracking-feature-portal/data-factory-home-page.png)
10. Klicka på rutan **Författare och övervakare** för att starta användargränssnittet för Azure Data Factory på en separat flik.
11. På sidan **kom igång** växlar du till fliken **Redigera** på den vänstra panelen som på följande bild:

    ![Knappen Skapa pipeline](./media/tutorial-incremental-copy-change-tracking-feature-portal/get-started-page.png)

## <a name="create-linked-services"></a>Skapa länkade tjänster
Du kan skapa länkade tjänster i en datafabrik för att länka ditt datalager och beräkna datafabrik-tjänster. I det här avsnittet skapar du länkade tjänster till ditt Azure Storage-konto och din databas i Azure SQL Database.

### <a name="create-azure-storage-linked-service"></a>Skapa en länkad Azure-lagringstjänst.
I det här steget länkar du ditt Azure-lagringskonto till datafabriken.

1. Klicka på **Anslutningar** och sedan på **+ Ny**.

   ![Knapp för ny anslutning](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-connection-button-storage.png)
2. I fönstret **New Linked Service** (Ny länkad tjänst) väljer du **Azure Blob Storage** och klickar på **Fortsätt**.

   ![Välj Azure Blob Storage](./media/tutorial-incremental-copy-change-tracking-feature-portal/select-azure-storage.png)
3. Utför följande steg i fönstret **New Linked Service** (Ny länkad tjänst):

    1. Ange **AzureStorageLinkedService** som **namn**.
    2. Välj ditt Azure-lagringskonto som **Lagringskontonamn**.
    3. Klicka på **Spara**.

   ![Inställningar för Azure Storage-konto](./media/tutorial-incremental-copy-change-tracking-feature-portal/azure-storage-linked-service-settings.png)


### <a name="create-azure-sql-database-linked-service"></a>Skapa länkad Azure SQL Database-tjänst.
I det här steget länkar du databasen till data fabriken.

1. Klicka på **Anslutningar** och sedan på **+ Ny**.
2. I fönstret **New Linked Service** (Ny länkad tjänst) väljer du **Azure SQL Database** och klickar på **Fortsätt**.
3. Utför följande steg i fönstret **New Linked Service** (Ny länkad tjänst):

    1. Ange **AzureSqlDatabaseLinkedService** i fältet **Namn**.
    2. Välj servern för fältet **Server namn** .
    3. Välj din databas för fältet **databas namn** .
    4. Ange användarens namn i fältet **Användarnamn**.
    5. Ange användarens lösenord i fältet **Lösenord**.
    6. Testa anslutningen genom att klicka på **Testa anslutning**.
    7. Klicka på **Spara** för att spara den länkade tjänsten.

       ![Inställningar för länkad Azure SQL Database-tjänst](./media/tutorial-incremental-copy-change-tracking-feature-portal/azure-sql-database-linked-service-settings.png)

## <a name="create-datasets"></a>Skapa datauppsättningar
I det här steget skapar du databaser för att representera datakälla, datamål. och plats för att lagra SYS_CHANGE_VERSION.

### <a name="create-a-dataset-to-represent-source-data"></a>Skapa en datauppsättning för att representera källdata
I det här steget skapar du en datamängd för att representera källdata.

1. I trädvyn klickar du på **+ (plus)** och sedan på **Datauppsättning**.

   ![Menyn Ny datauppsättning](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-dataset-menu.png)
2. Välj **Azure SQL Database** och klicka på **Slutför**.

   ![Typ av källdatauppsättning – Azure SQL Database](./media/tutorial-incremental-copy-change-tracking-feature-portal/select-azure-sql-database.png)
3. En ny flik öppnas för inställningar för datauppsättning. Du kan också se datauppsättningen i trädvyn. Ändra namnet på datauppsättningen till **SourceBlobDataset** i fönstret **Egenskaper**.

   ![Namn på källdatauppsättning](./media/tutorial-incremental-copy-change-tracking-feature-portal/source-dataset-name.png)    
4. Välj fliken **Anslutning** och gör följande:

    1. Välj **AzureSqlDatabaseLinkedService** som **Länkad tjänst**.
    2. Välj **[dbo].[data_source_table]** som **Tabell**.

   ![Källanslutning](./media/tutorial-incremental-copy-change-tracking-feature-portal/source-dataset-connection.png)

### <a name="create-a-dataset-to-represent-data-copied-to-sink-data-store"></a>Skapa en datauppsättning för att representera data som kopierats till datalagret för mottagare.
I det här steget skapar du en datamängd för att representera data som kopieras från källdatalagret. Du skapade containern adftutorial i din Azure Blob Storage som en del av förutsättningarna. Skapa containern om den inte finns (eller) ställ in den för namnet på en befintlig. I den här självstudien genereras namnet på utdatafilen dynamiskt med uttrycket: `@CONCAT('Incremental-', pipeline().RunId, '.txt')`.

1. I trädvyn klickar du på **+ (plus)** och sedan på **Datauppsättning**.

   ![Menyn Ny datauppsättning](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-dataset-menu.png)
2. Välj **Azure Blob Storage** och klicka på **Slutför**.

   ![Typ av datauppsättning för mottagare – Azure Blob Storage](./media/tutorial-incremental-copy-change-tracking-feature-portal/source-dataset-type.png)
3. En ny flik öppnas för inställningar för datauppsättning. Du kan också se datauppsättningen i trädvyn. Ändra namnet på datauppsättningen till **SinkDataset** i fönstret **Egenskaper**.

   ![Namn på datauppsättning för mottagare](./media/tutorial-incremental-copy-change-tracking-feature-portal/sink-dataset-name.png)
4. Växla till fliken **Anslutning** i fönstret Egenskaper och utför följande steg:

    1. Välj **AzureStorageLinkedService** för **Länkad tjänst**.
    2. Ange **adftutorial/incchgtracking** för **mappdelen** i **filePath**.
    3. Ange **\@ concat ("stegvis", pipeline (). RunId, '. txt ')** för en fil del av **fil** **Sök vägen**.  

       ![Datauppsättning för mottagare – anslutning](./media/tutorial-incremental-copy-change-tracking-feature-portal/sink-dataset-connection.png)

### <a name="create-a-dataset-to-represent-change-tracking-data"></a>Skapa en datauppsättning för att representera ändrad spårningsdata
I det här steget skapar du en datauppsättning för att lagra ändringsspårningsversionen.  Du skapade tabellen table_store_ChangeTracking_version som en del av förutsättningarna.

1. I trädvyn klickar du på **+ (plus)** och sedan på **Datauppsättning**.
2. Välj **Azure SQL Database** och klicka på **Slutför**.
3. En ny flik öppnas för inställningar för datauppsättning. Du kan också se datauppsättningen i trädvyn. Ändra namnet på datauppsättningen till **ChangeTrackingDataset** i fönstret **Egenskaper**.
4. Välj fliken **Anslutning** och gör följande:

    1. Välj **AzureSqlDatabaseLinkedService** som **Länkad tjänst**.
    2. Välj **[dbo].[table_store_ChangeTracking_version]** för **Tabell**.

## <a name="create-a-pipeline-for-the-full-copy"></a>Skapa en pipeline för hela kopian
I det här steget skapar du en pipeline med en kopieringsaktivitet som kopierar alla data från källdatalagret (Azure SQL Database) till måldatalagret (Azure Blob Storage).

1. Klicka på **+ (plus)** i det vänstra fönstret och klicka på **Pipeline**.

    ![Skärm bild som visar alternativet pipelines för en data fabrik.](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-pipeline-menu.png)
2. En ny flik öppnas för inställningar för pipelinen. Du kan också se pipelinen i trädvyn. I fönstret **Egenskaper** ändrar du pipelinenamnet till **FullCopyPipeline**.

    ![Skärm bild som visar en pipeline med ett namn angivet.](./media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-pipeline-name.png)
3. I verktygslådan **Aktiviteter** visar du **Dataflöde** och drar och släpper aktiviteten **Kopiera** till pipelinedesignytan samt anger namnet **FullCopyActivity**.

    ![Aktivitetsnamn för fullständig kopiering](./media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-activity-name.png)
4. Växla till fliken **Källa** och markera **SourceDataset** för fältet för **källdatauppsättning**.

    ![Kopiera aktivitet – källa](./media/tutorial-incremental-copy-change-tracking-feature-portal/copy-activity-source.png)
5. Växla till fliken **Mottagare** och markera **SinkDataset** för fältet för **datauppsättning för mottagare**.

    ![Kopiera aktivitet – mottagare](./media/tutorial-incremental-copy-change-tracking-feature-portal/copy-activity-sink.png)
6. Verifiera pipeline-definitionen genom att klicka på **Verifiera** i verktygsfältet. Kontrollera att det inte finns några verifieringsfel. Stäng **verifieringsrapporten för pipeline** genom att klicka på **>>**.

    ![Verifiera pipeline](./media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-pipeline-validate.png)
7. Klicka på **Publicera** om du vill publicera entiteter (länkade tjänster, datauppsättningar och pipeliner). Vänta tills publiceringen har lyckats.

    ![Skärm bild som visar data Factory med knappen publicera alla som heter ut.](./media/tutorial-incremental-copy-change-tracking-feature-portal/publish-button.png)
8. Vänta tills du ser meddelandet om att entiteterna **har publicerats**.

    ![Publiceringen lyckades](./media/tutorial-incremental-copy-change-tracking-feature-portal/publishing-succeeded.png)
9. Du kan även se meddelanden genom att klicka på knappen **Visa meddelanden** till vänster. Stäng meddelandefönstret genom att klicka på **X**.

    ![Visa meddelanden](./media/tutorial-incremental-copy-change-tracking-feature-portal/show-notifications.png)


### <a name="run-the-full-copy-pipeline"></a>Kör den fullständiga kopieringspipelinen
Klicka på **Utlösare** i verktygsfältet för pipelinen och klicka på **Trigger Now** (Utlös nu).

![Skärm bild som visar alternativet Utlös nu valt på menyn utlösare.](./media/tutorial-incremental-copy-change-tracking-feature-portal/trigger-now-menu.png)

### <a name="monitor-the-full-copy-pipeline"></a>Övervaka den fullständiga kopieringspipelinen

1. Klicka på fliken **Övervaka** till vänster. Du ser pipelinekörningen samt dess status i listan. Om du vill uppdatera listan klickar du på **Uppdatera**. Med länkarna i åtgärdskolumnen kan du visa de aktivitetskörningar som är associerade med pipelinekörningar och köra pipelinen på nytt.

    ![Skärm bild som visar pipeline-körningar för en data fabrik.](./media/tutorial-incremental-copy-change-tracking-feature-portal/monitor-full-copy-pipeline-run.png)
2. Om du vill visa de aktivitetskörningar som är associerade med pipelinekörningarna klickar du på länken **View activity runs** (Visa aktivitetskörningar) i kolumnen **Action** (Åtgärd). Det finns bara en aktivitet i pipelinen. Därför visas bara en post i listan. Om du vill växla tillbaka till vyn med pipelinekörningar klickar du på länken **Pipeliner** högst upp.

    ![Skärm bild som visar aktivitets körningar för en data fabrik med pipeline-länken som kallas.](./media/tutorial-incremental-copy-change-tracking-feature-portal/activity-runs-full-copy.png)

### <a name="review-the-results"></a>Granska resultaten
Du ser en fil som heter `incremental-<GUID>.txt` i mappen `incchgtracking` i containern `adftutorial`.

![Utdatafil från fullständig kopia](media/tutorial-incremental-copy-change-tracking-feature-portal/full-copy-output-file.png)

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

1. I Data Factory användar gränssnitt växlar du till fliken **Redigera** . Klicka på **+ (plus)** i det vänstra fönstret och klicka på **pipeline**.

    ![Skärm bild som visar hur du skapar en pipeline i en data fabrik.](./media/tutorial-incremental-copy-change-tracking-feature-portal/new-pipeline-menu-2.png)
2. En ny flik öppnas för inställningar för pipelinen. Du kan också se pipelinen i trädvyn. I fönstret **Egenskaper** ändrar du pipelinenamnet till **IncrementalCopyPipeline**.

    ![Namn på pipeline](./media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-copy-pipeline-name.png)
3. Visa verktygslådan **Allmänt** i verktygslådan **Aktiviteter** och dra och släpp **sökningen** på pipelinedesignytan. Ange aktivitetens namn som **LookupLastChangeTrackingVersionActivity**. Den här aktiviteten hämtar ändringsspårningsversionen som användes i den sista kopieringsåtgärden, som lagras i tabellen **table_store_ChangeTracking_version**.

    ![Skärm bild som visar en pipeline med en uppslags aktivitet.](./media/tutorial-incremental-copy-change-tracking-feature-portal/first-lookup-activity-name.png)
4. Växla till **Inställningar** i fönstret **Egenskaper** och välj **ChangeTrackingDataset** för fältet för **källdatauppsättning**.

    ![Skärm bild som visar fliken Inställningar i Fönstret Egenskaper.](./media/tutorial-incremental-copy-change-tracking-feature-portal/first-lookup-activity-settings.png)
5. Dra och släpp aktiviteten **Sökning** från verktygslådan **Aktiviteter** till pipelinedesignytan. Ange aktivitetens namn som **LookupCurrentChangeTrackingVersionActivity**. Den här aktiviteten hämtar den aktuella versionen för ändringsspårning.

    ![Skärm bild som visar en pipeline med två lookup-aktiviteter.](./media/tutorial-incremental-copy-change-tracking-feature-portal/second-lookup-activity-name.png)
6. Växla till **Inställningar** i fönstret **Egenskaper** och utför följande steg:

   1. Markera **SourceDataset** för fältet för **källdatauppsättning**.
   2. Välj **Fråga** för **Använd fråga**.
   3. Ange följande SQL-fråga för **Fråga**.

       ```sql
       SELECT CHANGE_TRACKING_CURRENT_VERSION() as CurrentChangeTrackingVersion
       ```

      ![Skärm bild som visar en fråga som har lagts till på fliken Inställningar i Fönstret Egenskaper.](./media/tutorial-incremental-copy-change-tracking-feature-portal/second-lookup-activity-settings.png)
7. I verktygslådan **Aktiviteter** visar du **Dataflöde** och drar och släpper aktiviteten **Kopiera** till pipelinedesignytan. Ange **IncrementalCopyActivity** som namn på aktiviteten. Den här aktiviteten kopierar data mellan den senaste versionen för ändringsspårning och den aktuella versionen för ändringsspårning till måldatalagret.

    ![Kopiera aktivitet – namn](./media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-copy-activity-name.png)
8. Växla till fliken **Källa** i fönstret **Egenskaper** och utför följande steg:

   1. Markera **SourceDataset** för **Källdatauppsättning**.
   2. Välj **Fråga** för **Använd fråga**.
   3. Ange följande SQL-fråga för **Fråga**.

       ```sql
       select data_source_table.PersonID,data_source_table.Name,data_source_table.Age, CT.SYS_CHANGE_VERSION, SYS_CHANGE_OPERATION from data_source_table RIGHT OUTER JOIN CHANGETABLE(CHANGES data_source_table, @{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.SYS_CHANGE_VERSION}) as CT on data_source_table.PersonID = CT.PersonID where CT.SYS_CHANGE_VERSION <= @{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion}
       ```

      ![Kopiera aktivitet – källinställningar](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-source-settings.png)
9. Växla till fliken **Mottagare** och markera **SinkDataset** för fältet för **datauppsättning för mottagare**.

    ![Kopiera aktivitet – inställningar för mottagare](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-sink-settings.png)
10. **Ansluta båda sökningsaktiviteterna till aktiviteten Kopiera**, en i taget. Dra den **gröna** knappen som är kopplad till aktiviteten **Sökning** till aktiviteten **Kopiera**.

    ![Ansluta sökning- och kopieringsaktiviteter](./media/tutorial-incremental-copy-change-tracking-feature-portal/connect-lookup-and-copy.png)
11. Dra och släpp aktiviteten **Lagrad procedur** från verktygslådan **Aktiviteter** till pipelinedesignytan. Ange aktivitetens namn som **StoredProceduretoUpdateChangeTrackingActivity**. Den här aktiviteten uppdaterar ändringsspårningsversionen i tabellen **table_store_ChangeTracking_version**.

    ![Lagrad proceduraktivitet – namn](./media/tutorial-incremental-copy-change-tracking-feature-portal/stored-procedure-activity-name.png)
12. Växla till fliken *SQL-konto** och välj **AzureSqlDatabaseLinkedService** som **Länkad tjänst**.

    ![Lagrad proceduraktivitet – SQL-konto](./media/tutorial-incremental-copy-change-tracking-feature-portal/sql-account-tab.png)
13. Växla till fliken **Lagrad procedur** och gör följande:

    1. Som **Namn på lagrad procedur** väljer du **Update_ChangeTracking_Version**.  
    2. Välj **Importera parameter**.
    3. I avsnittet **Parametrar för lagrad procedur** anger du följande värden för parametrarna:

        | Namn | Typ | Värde |
        | ---- | ---- | ----- |
        | CurrentTrackingVersion | Int64 | @{activity('LookupCurrentChangeTrackingVersionActivity').output.firstRow.CurrentChangeTrackingVersion} |
        | TableName | Sträng | @{activity('LookupLastChangeTrackingVersionActivity').output.firstRow.TableName} |

        ![Lagrad proceduraktivitet – Parametrar](./media/tutorial-incremental-copy-change-tracking-feature-portal/stored-procedure-parameters.png)
14. **Anslut kopierings aktiviteten till aktiviteten lagrad procedur**. Dra och släpp den **gröna** knappen som är kopplad till aktiviteten Kopiera till aktiviteten Lagrad procedur.

    ![Ansluta Kopierings- och Lagrade proceduraktiviteter](./media/tutorial-incremental-copy-change-tracking-feature-portal/connect-copy-stored-procedure.png)
15. Klicka på **Verifiera** i verktygsfältet. Kontrollera att det inte finns några verifieringsfel. Stäng fönstret med **verifieringsrapporten för pipeline** genom att klicka på **>>**.

    ![Verifieringsknapp](./media/tutorial-incremental-copy-change-tracking-feature-portal/validate-button.png)
16. Publicera entiteter (länkade tjänster, datauppsättningar och pipeliner) till Data Factory-tjänsten genom att klicka på knappen **Publicera alla**. Vänta tills du ser meddelandet **Publiceringen är klar**.

       ![Skärm bild som visar knappen publicera alla för en data fabrik.](./media/tutorial-incremental-copy-change-tracking-feature-portal/publish-button-2.png)    

### <a name="run-the-incremental-copy-pipeline"></a>Kör den inkrementella kopieringspipelinen
1. Klicka på **Utlösare** i verktygsfältet för pipelinen och klicka på **Trigger Now** (Utlös nu).

    ![Skärm bild som visar en pipeline med aktiviteter och alternativet Utlös nu valt från menyn utlösare.](./media/tutorial-incremental-copy-change-tracking-feature-portal/trigger-now-menu-2.png)
2. I fönstret **Pipeline Run** (Pipelinekörning) väljer du **Slutför**.

### <a name="monitor-the-incremental-copy-pipeline"></a>Övervaka den inkrementella kopieringspipelinen
1. Klicka på fliken **Övervaka** till vänster. Du ser pipelinekörningen samt dess status i listan. Om du vill uppdatera listan klickar du på **Uppdatera**. Med länkarna i **åtgärdskolumnen** kan du visa de aktivitetskörningar som är associerade med pipelinekörningar och köra pipelinen på nytt.

    ![Skärm bild som visar pipeline-körningar för en data fabrik, inklusive din pipeline.](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-pipeline-runs.png)
2. Om du vill visa de aktivitetskörningar som är associerade med pipelinekörningarna klickar du på länken **View activity runs** (Visa aktivitetskörningar) i kolumnen **Action** (Åtgärd). Det finns bara en aktivitet i pipelinen. Därför visas bara en post i listan. Om du vill växla tillbaka till vyn med pipelinekörningar klickar du på länken **Pipeliner** högst upp.

    ![Skärm bild som visar pipeline-körningar för en data fabrik med flera markerade som slutförda.](./media/tutorial-incremental-copy-change-tracking-feature-portal/inc-copy-activity-runs.png)


### <a name="review-the-results"></a>Granska resultaten
Du ser den andra filen i mappen `incchgtracking` i containern `adftutorial`.

![Utdatafil från inkrementell säkerhetskopia](media/tutorial-incremental-copy-change-tracking-feature-portal/incremental-copy-output-file.png)

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
> [Kopiera nya filer efter lastmodifieddate](tutorial-incremental-copy-lastmodified-copy-data-tool.md)
