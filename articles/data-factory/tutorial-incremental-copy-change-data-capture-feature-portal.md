---
title: Kopiera data stegvis med registrering av ändrings data
description: I den här självstudien skapar du en Azure Data Factory pipeline som kopierar delta data stegvis från en tabell i Azure SQL-hanterad instans databas till Azure Storage.
ms.author: nihurt
author: hurtn
ms.service: data-factory
ms.topic: tutorial
ms.date: 02/18/2021
ms.openlocfilehash: a00ec8698b188b8fa87935e498e8cfab3aeab5aa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101724990"
---
# <a name="incrementally-load-data-from-azure-sql-managed-instance-to-azure-storage-using-change-data-capture-cdc"></a>Läs in data stegvis från Azure SQL-hanterad instans till Azure Storage med hjälp av registrering av ändrings data (CDC)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här självstudien skapar du en Azure-datafabrik med en pipeline som läser in delta data baserat på information om **registrering av ändrings data (CDC)** i Azure SQL Managed instance-databasen till Azure Blob Storage.  

I den här självstudiekursen får du göra följande:

> [!div class="checklist"]
> * Förbereda källdatalagret
> * Skapa en datafabrik.
> * Skapa länkade tjänster.
> * Skapa datauppsättningar för källa och mottagare.
> * Skapa, Felsök och kör pipelinen för att söka efter ändrade data
> * Ändra data i käll tabellen
> * Slutför, kör och övervaka en pipeline för fullständig stegvis kopiering

## <a name="overview"></a>Översikt
Den teknik för registrering av ändrings data som stöds av data lager som Azure SQL Managed instances (MI) och SQL Server kan användas för att identifiera ändrade data.  Den här självstudien beskriver hur du använder Azure Data Factory med SQL Change data capture-teknik för att stegvis läsa in delta data från Azure SQL Managed Instance till Azure Blob Storage.  Mer konkret information om insamlings teknik för SQL-dataändrings data finns [i avsnittet Ändra data insamling i SQL Server](/sql/relational-databases/track-changes/about-change-data-capture-sql-server).

## <a name="end-to-end-workflow"></a>Arbetsflödet slutpunkt till slutpunkt
Här är de vanligaste arbets flödes stegen från slut punkt till slut punkt för att stegvis läsa in data med hjälp av insamling av ändrings data.

> [!NOTE]
> Både Azure SQL MI och SQL Server stöder insamling av ändrings data. I den här självstudien används Azure SQL-hanterad instans som käll data lager. Du kan också använda en lokal SQL Server.

## <a name="high-level-solution"></a>Lösning på hög nivå
I den här självstudien skapar du en pipeline som utför följande åtgärder:  

   1. Skapa en **uppslags aktivitet** för att räkna antalet ändrade poster i SQL Database CDC-tabellen och skicka det till en if-villkor-aktivitet.
   2. Skapa ett **if-villkor** för att kontrol lera om det finns ändrade poster och om så är fallet, anropa kopierings aktiviteten.
   3. Skapa en **kopierings aktivitet** för att kopiera infogade/uppdaterade/borttagna data mellan CDC-tabellen och Azure Blob Storage.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.

## <a name="prerequisites"></a>Förutsättningar
* **Azure SQL Database Hanterad instans**. Du använder databasen som **källa** för datalagringen. Om du inte har en Azure SQL Database Hanterad instans kan du läsa artikeln [skapa en Azure SQL Database Hanterad instans](../azure-sql/managed-instance/instance-create-quickstart.md) för steg för att skapa en.
* **Azure Storage konto**. Du kan använda blob-lagringen som **mottagare** för datalagringen. Om du inte har ett Azure Storage-konto kan du läsa artikeln [skapa ett lagrings konto](../storage/common/storage-account-create.md) för steg för att skapa ett. Skapa en behållare med namnet **RAW**. 

### <a name="create-a-data-source-table-in-azure-sql-database"></a>Skapa en data käll tabell i Azure SQL Database

1. Starta **SQL Server Management Studio** och Anslut till din Azure SQL Managed instances-Server.
2. I **Server Explorer** högerklickar du på **databasen** och väljer **Ny fråga**.
3. Kör följande SQL-kommando mot din Azure SQL Managed instances-databas för att skapa en tabell med namnet `customers` som data käll arkiv.  

    ```sql
    create table customers 
    (
    customer_id int, 
    first_name varchar(50), 
    last_name varchar(50), 
    email varchar(100), 
    city varchar(50), CONSTRAINT "PK_Customers" PRIMARY KEY CLUSTERED ("customer_id") 
     );
    ```
4. Aktivera funktionen för att **samla in ändringar av data** i databasen och käll tabellen (kunder) genom att köra följande SQL-fr åga:

    > [!NOTE]
    > - Ersätt &lt; ditt käll schema namn &gt; med schemat för din Azure SQL-mi som har tabellen kunder.
    > - Registrering av ändrings data gör ingenting som en del av de transaktioner som ändrar den tabell som spåras. I stället skrivs åtgärderna INSERT, Update och Delete till transaktions loggen. Data som deponeras i ändrings tabeller kommer att växa ohanterade om du inte regelbundet och rensar data. Mer information finns i [aktivera registrering av ändrings data för en databas](/sql/relational-databases/track-changes/enable-and-disable-change-data-capture-sql-server#enable-change-data-capture-for-a-database)

    ```sql
    EXEC sys.sp_cdc_enable_db 
    
    EXEC sys.sp_cdc_enable_table
    @source_schema = 'dbo',
    @source_name = 'customers', 
    @role_name = 'null',
    @supports_net_changes = 1
    ```
5. Infoga data i tabellen kunder genom att köra följande kommando:

    ```sql
     insert into customers 
         (customer_id, first_name, last_name, email, city) 
     values 
         (1, 'Chevy', 'Leward', 'cleward0@mapy.cz', 'Reading'),
         (2, 'Sayre', 'Ateggart', 'sateggart1@nih.gov', 'Portsmouth'),
        (3, 'Nathalia', 'Seckom', 'nseckom2@blogger.com', 'Portsmouth');
    ```

    > [!NOTE]
    > Inga historiska ändringar i tabellen fångas innan registrering av ändrings data aktive ras.

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Starta webbläsaren **Microsoft Edge** eller **Google Chrome**. Användargränssnittet för Data Factory stöds för närvarande bara i webbläsarna Microsoft Edge och Google Chrome.
1. På den vänstra menyn väljer du **skapa en resurs**  >  **data och analys**  >  **Data Factory**:

   ![Valet Data Factory i fönstret Nytt](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-azure-data-factory-menu.png)

2. På sidan **Ny datafabrik** anger du **ADFTutorialDataFactory** som **namn**.

     ![Sidan Ny datafabrik](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-azure-data-factory.png)

   Namnet på Azure Data Factory måste vara **globalt unikt**. Om följande fel returneras ändrar du namnet på datafabriken (till exempel dittnamnADFTutorialDataFactory) och provar att skapa fabriken igen. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.

    *Data fabriks namnet "ADFTutorialDataFactory" är inte tillgängligt.*
3. Välj **V2** för **versionen**.
4. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i.
5. För **resursgruppen** utför du något av följande steg:

   1. Välj **Använd befintlig** och välj en befintlig resurs grupp i den nedrullningsbara listan.
   2. Välj **Skapa ny** och ange namnet på en resurs grupp.   
         
    Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/management/overview.md).  
5. Välj **plats** för datafabriken. Endast platser som stöds visas i listrutan. Datalagren (Azure Storage, Azure SQL Database osv.) och beräkningarna (HDInsight osv.) som används i Data Factory kan finnas i andra regioner.
6. Avmarkera **Aktivera git**.     
7. Klicka på **Skapa**.
8. När distributionen är klar klickar **du på gå till resurs**

   ![Skärm bild som visar ett meddelande om att distributionen har slutförts och ett alternativ för att gå till resursen.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/data-factory-deploy-complete.png)
9. När datafabriken har skapats visas sidan **Datafabrik** som på bilden.

   ![Skärm bild visar den data fabrik som du har distribuerat.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/data-factory-home-page.png)
10. Klicka på rutan **Författare och övervakare** för att starta användargränssnittet för Azure Data Factory på en separat flik.
11. På sidan **kom igång** växlar du till fliken **Redigera** på den vänstra panelen som på följande bild:

    ![Knappen Skapa pipeline](./media/tutorial-incremental-copy-change-data-capture-feature-portal/get-started-page.png)

## <a name="create-linked-services"></a>Skapa länkade tjänster
Du kan skapa länkade tjänster i en datafabrik för att länka ditt datalager och beräkna datafabrik-tjänster. I det här avsnittet skapar du länkade tjänster till ditt Azure Storage konto och Azure SQL MI.

### <a name="create-azure-storage-linked-service"></a>Skapa en länkad Azure-lagringstjänst.
I det här steget länkar du ditt Azure-lagringskonto till datafabriken.

1. Klicka på **Anslutningar** och sedan på **+ Ny**.

   ![Knapp för ny anslutning](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-connection-button-storage.png)
2. I fönstret **New Linked Service** (Ny länkad tjänst) väljer du **Azure Blob Storage** och klickar på **Fortsätt**.

   ![Välj Azure Blob Storage](./media/tutorial-incremental-copy-change-data-capture-feature-portal/select-azure-storage.png)
3. Utför följande steg i fönstret **New Linked Service** (Ny länkad tjänst):

   1. Ange **AzureStorageLinkedService** som **namn**.
   2. Välj ditt Azure-lagringskonto som **Lagringskontonamn**.
   3. Klicka på **Spara**.

   ![Inställningar för Azure Storage-konto](./media/tutorial-incremental-copy-change-data-capture-feature-portal/azure-storage-linked-service-settings.png)


### <a name="create-azure-sql-mi-database-linked-service"></a>Skapa en länkad Azure SQL MI Database-tjänst.
I det här steget länkar du din Azure SQL MI-databas till data fabriken.

> [!NOTE]
> För de som använder SQL MI finns [här](./connector-azure-sql-managed-instance.md#prerequisites) för information om åtkomst via offentlig vs privat slut punkt. Om du använder privat slut punkt måste du köra den här pipelinen med hjälp av en lokal integration Runtime. Samma sak gäller för de som kör SQL Server lokal, i scenarier med virtuella datorer och virtuella nätverk.

1. Klicka på **Anslutningar** och sedan på **+ Ny**.
2. I fönstret **ny länkad tjänst** väljer du **Azure SQL Database Hanterad instans** och klickar på **Fortsätt**.
3. Utför följande steg i fönstret **New Linked Service** (Ny länkad tjänst):

   1. Ange **AzureSqlMI1** i fältet **namn** .
   2. Välj din SQL Server i fältet **Server namn** .
   4. Välj din SQL-databas i fältet **databas namn** .
   5. Ange användarens namn i fältet **Användarnamn**.
   6. Ange användarens lösenord i fältet **Lösenord**.
   7. Testa anslutningen genom att klicka på **Testa anslutning**.
   8. Klicka på **Spara** för att spara den länkade tjänsten.

   ![Inställningar för länkad Azure SQL MI-databas](./media/tutorial-incremental-copy-change-data-capture-feature-portal/azure-sql-managed-instance-database-linked-service-settings.png)

## <a name="create-datasets"></a>Skapa datauppsättningar
I det här steget skapar du data uppsättningar som representerar data källa och data mål.

### <a name="create-a-dataset-to-represent-source-data"></a>Skapa en datauppsättning för att representera källdata
I det här steget skapar du en datamängd för att representera källdata.

1. I trädvyn klickar du på **+ (plus)** och sedan på **Datauppsättning**.

   ![Menyn Ny datauppsättning](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-dataset-menu.png)
2. Välj **Azure SQL Database Hanterad instans** och klicka på **Fortsätt**.

   ![Typ av källdatauppsättning – Azure SQL Database](./media/tutorial-incremental-copy-change-data-capture-feature-portal/select-azure-sql-database.png)
   
3. På fliken **Ange egenskaper** anger du data uppsättningens namn och anslutnings information:
 
   1. Välj **AzureSqlMI1** för **länkad tjänst**.
   2. Välj **[dbo]. [ dbo_customers_CT]** för **tabell namn**.  OBS! den här tabellen skapades automatiskt när CDC aktiverades i tabellen kunder. Ändrade data frågas aldrig från den här tabellen direkt utan extraheras i stället via [CDC-funktionerna](/sql/relational-databases/system-functions/change-data-capture-functions-transact-sql).

   ![Källanslutning](./media/tutorial-incremental-copy-change-data-capture-feature-portal/source-dataset-configuration.png)

### <a name="create-a-dataset-to-represent-data-copied-to-sink-data-store"></a>Skapa en datauppsättning för att representera data som kopierats till datalagret för mottagare.
I det här steget skapar du en datamängd för att representera data som kopieras från källdatalagret. Du har skapat data Lake-behållaren i Azure Blob Storage som en del av förutsättningarna. Skapa containern om den inte finns (eller) ställ in den för namnet på en befintlig. I den här självstudien genereras namnet på utdatafilen dynamiskt med hjälp av utlösnings tiden, som kommer att konfigureras senare.

1. I trädvyn klickar du på **+ (plus)** och sedan på **Datauppsättning**.

   ![Menyn Ny datauppsättning](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-dataset-menu.png)
2. Välj **Azure Blob Storage** och klicka på **Fortsätt**.

   ![Typ av datauppsättning för mottagare – Azure Blob Storage](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-type.png)
3. Välj **DelimitedText** och klicka på **Fortsätt**.

   ![Data uppsättnings format för mottagare – DelimitedText](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-format.png)
4. På fliken **Ange egenskaper** anger du data uppsättningens namn och anslutnings information:

   1. Välj **AzureStorageLinkedService** för **Länkad tjänst**.
   2. Ange **RAW** som **container** del av **fil Sök vägen**.
   3. Aktivera **första raden som rubrik**
   4. Klicka på **OK**

   ![Datauppsättning för mottagare – anslutning](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration.png)

## <a name="create-a-pipeline-to-copy-the-changed-data"></a>Skapa en pipeline för att kopiera ändrade data
I det här steget skapar du en pipeline, som först kontrollerar antalet ändrade poster i ändrings tabellen med en **uppslags aktivitet**. En IF-condition-aktivitet kontrollerar om antalet ändrade poster är större än noll och kör en **kopierings aktivitet** för att kopiera infogade/uppdaterade/borttagna data från Azure SQL Database till Azure Blob Storage. Slutligen konfigureras en utlösare för rullande fönster och start-och slut tiderna skickas till aktiviteterna som start-och slut fönster parametrarna. 

1. I Data Factory användar gränssnitt växlar du till fliken **Redigera** . Klicka på **+ (plus)** i det vänstra fönstret och klicka på **pipeline**.

    ![Meny för ny pipeline](./media/tutorial-incremental-copy-change-data-capture-feature-portal/new-pipeline-menu.png)
2. En ny flik öppnas för inställningar för pipelinen. Du kan också se pipelinen i trädvyn. I fönstret **Egenskaper** ändrar du pipelinenamnet till **IncrementalCopyPipeline**.

    ![Namn på pipeline](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-name.png)
3. Visa verktygslådan **Allmänt** i verktygslådan **Aktiviteter** och dra och släpp **sökningen** på pipelinedesignytan. Ange namnet på aktiviteten till **GetChangeCount**. Den här aktiviteten hämtar antalet poster i ändrings tabellen för en bestämd tids period.

    ![Namn för sökningsaktivitet](./media/tutorial-incremental-copy-change-data-capture-feature-portal/first-lookup-activity-name.png)
4. Växla till **inställningarna** i fönstret **Egenskaper** :
   1. Ange SQL MI-datauppsättningens namn för fältet för **käll data uppsättningen** .
   2. Välj frågealternativet och ange följande i rutan fråga:
    ```sql
    DECLARE  @from_lsn binary(10), @to_lsn binary(10);  
    SET @from_lsn =sys.fn_cdc_get_min_lsn('dbo_customers');  
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn('largest less than or equal',  GETDATE());
    SELECT count(1) changecount FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, 'all')
    ```
   3. Aktivera **endast första raden**

    ![Inställningar för sökningsaktivitet](./media/tutorial-incremental-copy-change-data-capture-feature-portal/first-lookup-activity-settings.png)
5. Klicka på knappen **Förhandsgranska data** för att se till att giltiga utdata erhålls av Sök aktiviteten

    ![Söknings aktivitet – för hands version](./media/tutorial-incremental-copy-change-data-capture-feature-portal/first-lookup-activity-preview.png)
6. Expandera **iterationer & villkorliger** i **aktivitets** verktygs lådan och dra och släpp aktiviteten **if-villkor** till pipelinens designer-yta. Ange namnet på aktiviteten till **HasChangedRows**. 

    ![Om villkors aktivitet – namn](./media/tutorial-incremental-copy-change-data-capture-feature-portal/if-condition-activity-name.png)
7. Växla till **aktiviteterna** i fönstret **Egenskaper** :

   1. Ange följande **uttryck**
   
    ```adf
    @greater(int(activity('GetChangeCount').output.firstRow.changecount),0)
    ```

   2. Klicka på Penn ikonen för att redigera det sanna villkoret.

   ![Om villkors aktivitet – inställningar](./media/tutorial-incremental-copy-change-data-capture-feature-portal/if-condition-activity-setting.png)

   3. Expandera **Allmänt** i **aktivitets** verktygs lådan och dra och släpp en **vänta** -aktivitet till pipelinens designer-yta. Detta är en tillfällig aktivitet för att felsöka IF-villkoret och kommer att ändras senare i självstudien. 

   ![Om villkoret är sant – vänta](./media/tutorial-incremental-copy-change-data-capture-feature-portal/if-condition-activity-wait.png)

   4. Klicka på IncrementalCopyPipeline-spåret för att återgå till den huvudsakliga pipelinen.

8. Kör pipelinen i **fel söknings** läge för att verifiera att pipelinen har körts. 

   ![Pipeline – Felsök](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug.png)
9. Gå sedan tillbaka till steget sant villkor och ta bort aktiviteten **vänta** . I verktygs lådan **aktiviteter** expanderar du **Flytta & Transform** och drar och släpper en **kopierings** aktivitet till pipelinens design yta. Ange **IncrementalCopyActivity** som namn på aktiviteten. 

   ![Kopiera aktivitet – namn](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-source-name.png)
10. Växla till fliken **Källa** i fönstret **Egenskaper** och utför följande steg:

   1. Ange SQL MI-datauppsättningens namn för fältet för **käll data uppsättningen** . 
   2. Välj **Fråga** för **Använd fråga**.
   3. Ange följande för **fråga**.

      ```sql
      DECLARE @from_lsn binary(10), @to_lsn binary(10); 
      SET @from_lsn =sys.fn_cdc_get_min_lsn('dbo_customers'); 
      SET @to_lsn = sys.fn_cdc_map_time_to_lsn('largest less than or equal', GETDATE());
      SELECT * FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, 'all')
      ```

   ![Kopiera aktivitet – källinställningar](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-source-settings.png)

11. Klicka på Förhandsgranska för att kontrol lera att frågan returnerar de ändrade raderna korrekt.

    ![Skärm bild som visar förhands granskning för att verifiera frågan.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-source-preview.png)
12. Växla till fliken **mottagare** och ange Azure Storage data uppsättning för fältet Sink- **datauppsättning** .

    ![Skärm bild som visar fliken mottagare.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-sink-settings.png)
13. Klicka på tillbaka till den huvudsakliga pipeline-arbetsytan och Anslut **Sök** aktiviteten till aktiviteten **om villkor** en i taget. Dra den **gröna** knappen som är kopplad till **Lookup** -aktiviteten till aktiviteten **if-villkor** .

    ![Ansluta sökning- och kopieringsaktiviteter](./media/tutorial-incremental-copy-change-data-capture-feature-portal/connect-lookup-if.png)
14. Klicka på **Verifiera** i verktygsfältet. Kontrollera att det inte finns några verifieringsfel. Stäng fönstret med **verifieringsrapporten för pipeline** genom att klicka på **>>**.

    ![Verifieringsknapp](./media/tutorial-incremental-copy-change-data-capture-feature-portal/validate-button.png)
15. Klicka på Felsök för att testa pipelinen och kontrol lera att en fil genereras på lagrings platsen.

    ![Stegvis fel sökning för pipeline – 2](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug-2.png)
16. Publicera entiteter (länkade tjänster, data uppsättningar och pipeliner) till Data Factory tjänsten genom att klicka på knappen **publicera alla** . Vänta tills du ser meddelandet **Publiceringen är klar**.

    ![Knappen Publicera](./media/tutorial-incremental-copy-change-data-capture-feature-portal/publish-button-2.png)    

### <a name="configure-the-tumbling-window-trigger-and-cdc-window-parameters"></a>Konfigurera fönster utlösare för rullande fönster och parametrarna för CDC-fönstret 
I det här steget skapar du en utlösare för rullande fönster för att köra jobbet enligt ett frekvent schema. Du kommer att använda systemvariablerna WindowStart och WindowEnd i utlösaren rullande Window och skicka dem som parametrar till din pipeline som ska användas i CDC-frågan.

1. Gå till fliken **parametrar** i **IncrementalCopyPipeline** -pipeline och Använd knappen **+ ny** för att lägga till två parametrar (**triggerStartTime** och **triggerEndTime**) i pipelinen, som representerar start-och slut tid för rullande window. För fel söknings syfte lägger du till standardvärden i formatet **ÅÅÅÅ-MM-DD HH24: mi: SS. FFF** , men se till att triggerStartTime inte är i förväg än CDC som är aktive rad i tabellen, annars resulterar detta i ett fel.

    ![Menyn Utlös nu](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-parameters.png)
2. Klicka på fliken Inställningar i **Sök** aktiviteten och konfigurera frågan så att den använder start-och slut parametrarna. Kopiera följande till frågan:
    ```sql
    @concat('DECLARE @begin_time datetime, @end_time datetime, @from_lsn binary(10), @to_lsn binary(10); 
    SET @begin_time = ''',pipeline().parameters.triggerStartTime,''';
    SET @end_time = ''',pipeline().parameters.triggerEndTime,''';
    SET @from_lsn = sys.fn_cdc_map_time_to_lsn(''smallest greater than or equal'', @begin_time);
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn(''largest less than'', @end_time);
    SELECT count(1) changecount FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, ''all'')')
    ```

3. Gå till **kopierings** aktiviteten i det sanna fallet för aktiviteten **if-villkor** och klicka på fliken **källa** . Kopiera följande till frågan:
    ```sql
    @concat('DECLARE @begin_time datetime, @end_time datetime, @from_lsn binary(10), @to_lsn binary(10); 
    SET @begin_time = ''',pipeline().parameters.triggerStartTime,''';
    SET @end_time = ''',pipeline().parameters.triggerEndTime,''';
    SET @from_lsn = sys.fn_cdc_map_time_to_lsn(''smallest greater than or equal'', @begin_time);
    SET @to_lsn = sys.fn_cdc_map_time_to_lsn(''largest less than'', @end_time);
    SELECT * FROM cdc.fn_cdc_get_all_changes_dbo_customers(@from_lsn, @to_lsn, ''all'')')
    ```
4. Klicka på fliken **mottagare** i aktiviteten **Kopiera** och klicka på **Öppna** för att redigera egenskaperna för data uppsättningen. Klicka på fliken **parametrar** och Lägg till en ny parameter med namnet **triggerStart**    

    ![Skärm bild som visar hur du lägger till en ny parameter till fliken parametrar.](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration-2.png)
5. Konfigurera sedan egenskaperna för data uppsättningen för att lagra data i en **kund/stegvis** under katalog med datumbaserade partitioner.
   1. Klicka på fliken **anslutning** i egenskaperna för data uppsättningen och Lägg till dynamiskt innehåll för både **katalogen** och **fil** avsnitten. 
   2. Ange följande uttryck i avsnittet **katalog** genom att klicka på länken för dynamiskt innehåll under text rutan:
    
    ```sql
    @concat('customers/incremental/',formatDateTime(dataset().triggerStart,'yyyy/MM/dd'))
    ```
   3. Ange följande uttryck i avsnittet **fil** . Detta skapar fil namn baserat på utlösarens start datum och-tid med suffixet CSV-tillägg:
    
    ```sql
    @concat(formatDateTime(dataset().triggerStart,'yyyyMMddHHmmssfff'),'.csv')
    ```
    ![Konfiguration av Sink-datamängd-3](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration-3.png)

   4. Gå tillbaka till **mottagar** inställningarna i **kopierings** aktiviteten genom att klicka på fliken **IncrementalCopyPipeline** . 
   5. Expandera egenskaperna för data uppsättningen och ange dynamiskt innehåll i värdet för parametern triggerStart med följande uttryck:
     ```sql
     @pipeline().parameters.triggerStartTime
     ```
    ![Konfiguration av data uppsättning för mottagare – 4](./media/tutorial-incremental-copy-change-data-capture-feature-portal/sink-dataset-configuration-4.png)

6. Klicka på Felsök för att testa pipelinen och se till att mappstrukturen och utdatafilen genereras som förväntat. Hämta och öppna filen för att verifiera innehållet. 

    ![Fel sökning för stegvis kopiering-3](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug-3.png)
7. Se till att parametrarna matas in i frågan genom att granska indataparametrarna för pipeline-körningen.

    ![Fel sökning för stegvis kopiering-4](./media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-debug-4.png)
8. Publicera entiteter (länkade tjänster, data uppsättningar och pipeliner) till Data Factory tjänsten genom att klicka på knappen **publicera alla** . Vänta tills du ser meddelandet **Publiceringen är klar**.
9. Konfigurera slutligen en utlösare för rullande fönster för att köra pipelinen med ett regelbundet intervall och ange start-och slut tid-parametrar. 
   1. Klicka på knappen **Lägg till utlösare** och välj **nytt/redigera**

   ![Lägg till ny utlösare](./media/tutorial-incremental-copy-change-data-capture-feature-portal/add-trigger.png)

   2. Ange ett Utlösarens namn och ange en start tid som motsvarar slut tiden för fel söknings fönstret ovan.

   ![Utlösare för rullande fönster](./media/tutorial-incremental-copy-change-data-capture-feature-portal/tumbling-window-trigger.png)

   3. På nästa skärm anger du följande värden för start-och slut parametrarna.
    ```sql
    @formatDateTime(trigger().outputs.windowStartTime,'yyyy-MM-dd HH:mm:ss.fff')
    @formatDateTime(trigger().outputs.windowEndTime,'yyyy-MM-dd HH:mm:ss.fff')
    ```

   ![Utlösare för rullande fönster – 2](./media/tutorial-incremental-copy-change-data-capture-feature-portal/tumbling-window-trigger-2.png)

> [!NOTE]
> Observera att utlösaren bara körs när den har publicerats. Dessutom är det förväntade beteendet för rullande Window att köra alla historiska intervall från start datumet tills nu. Mer information om utlösare för rullande Window hittar du [här](./how-to-create-tumbling-window-trigger.md). 
  
10. Använd **SQL Server Management Studio** gör ytterligare ändringar i kund tabellen genom att köra följande SQL:
    ```sql
    insert into customers (customer_id, first_name, last_name, email, city) values (4, 'Farlie', 'Hadigate', 'fhadigate3@zdnet.com', 'Reading');
    insert into customers (customer_id, first_name, last_name, email, city) values (5, 'Anet', 'MacColm', 'amaccolm4@yellowbook.com', 'Portsmouth');
    insert into customers (customer_id, first_name, last_name, email, city) values (6, 'Elonore', 'Bearham', 'ebearham5@ebay.co.uk', 'Portsmouth');
    update customers set first_name='Elon' where customer_id=6;
    delete from customers where customer_id=5;
    ```
11. Klicka på knappen **publicera alla** . Vänta tills du ser meddelandet **Publiceringen är klar**.  
12. Efter några minuter kommer pipelinen att ha utlösts och en ny fil har lästs in i Azure Storage


### <a name="monitor-the-incremental-copy-pipeline"></a>Övervaka den inkrementella kopieringspipelinen
1. Klicka på fliken **Övervaka** till vänster. Du ser pipelinekörningen samt dess status i listan. Om du vill uppdatera listan klickar du på **Uppdatera**. Hovra nära namnet på pipelinen för att komma åt åtgärden kör om-åtgärd och användnings rapport.

    ![Pipelinekörningar](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-pipeline-runs.png)
2. Om du vill visa aktivitets körningar som är associerade med pipelinen klickar du på pipelinens namn. Om ändrade data upptäcktes finns det tre aktiviteter, inklusive kopierings aktiviteten, annars kommer det bara finnas två poster i listan. Om du vill växla tillbaka till vyn pipeline-körningar klickar du på länken **alla pipelines** överst.

    ![Aktivitetskörningar](./media/tutorial-incremental-copy-change-data-capture-feature-portal/copy-activity-runs.png)


### <a name="review-the-results"></a>Granska resultaten
Du ser den andra filen i mappen `customers/incremental/YYYY/MM/DD` i containern `raw`.

![Utdatafil från inkrementell säkerhetskopia](media/tutorial-incremental-copy-change-data-capture-feature-portal/incremental-copy-pipeline-run.png)
 

## <a name="next-steps"></a>Nästa steg
Fortsätt till följande självstudie och lär dig mer om hur du kopierar nya och ändrade filer endast baserat på deras LastModifiedDate:

> [!div class="nextstepaction"]
>[Kopiera nya filer efter lastmodifieddate](tutorial-incremental-copy-lastmodified-copy-data-tool.md)
