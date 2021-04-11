---
title: Kopiera data i bulk med Azure Portal
description: Använd Azure Data Factory-och kopierings aktivitet för att kopiera data från ett käll data lager till ett mål data lager i bulk.
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.workload: data-services
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/29/2021
ms.openlocfilehash: bca2158f448f74ba596114fce5d1631249124a92
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104606743"
---
# <a name="copy-multiple-tables-in-bulk-by-using-azure-data-factory-in-the-azure-portal"></a>Kopiera flera tabeller i bulk genom att använda Azure Data Factory i Azure Portal

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Den här självstudien visar hur **du kopierar ett antal tabeller från Azure SQL Database till Azure Synapse Analytics**. Du kan även använda samma mönster i andra kopieringssituationer. Du kan till exempel kopiera tabeller från SQL Server/Oracle till Azure SQL Database/Azure Synapse Analytics/Azure blob och kopiera olika sökvägar från blob till Azure SQL Database tabeller.

> [!NOTE]
> Om du inte har använt Azure Data Factory tidigare kan du läsa [Introduktion till Azure Data Factory](introduction.md).

Sett på en hög nivå ingår följande steg i självstudierna:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa Azure SQL Database, Azure Synapse Analytics och Azure Storage länkade tjänster.
> * Skapa Azure SQL Database och Azure Synapse Analytics-datauppsättningar.
> * Skapa en pipeline för att leta upp de tabeller som ska kopieras och en annan pipeline för att utföra den faktiska kopierings åtgärden. 
> * Starta en pipelinekörning.
> * Övervaka pipelinen och aktivitetskörningarna.

I den här självstudien används Azure-portalen. Läs mer om att använda andra verktyg/SDK:er för att skapa en datafabrik i [Snabbstarter](quickstart-create-data-factory-dot-net.md). 

## <a name="end-to-end-workflow"></a>Arbetsflödet slutpunkt till slutpunkt
I det här scenariot har du ett antal tabeller i Azure SQL Database som du vill kopiera till Azure Synapse Analytics. Här är den logiska ordningsföljden i arbetsflödet som sker i våra pipelines:

![Arbetsflöde](media/tutorial-bulk-copy-portal/tutorial-copy-multiple-tables.png)

* Den första pipelinen letar rätt på listan med tabeller som ska kopieras till de mottagande datalagren.  Du kan istället underhålla en metadatatabell som innehåller alla tabeller som ska kopieras till de mottagande datalagren. Sedan utlöser pipelinen en annan pipeline, som itererar över varje tabell i databasen och utför själva datakopieringen.
* Den andra pipelinen utför den faktiska kopieringen. Den tar listan med tabeller som en parameter. För varje tabell i listan kopierar du den speciella tabellen i Azure SQL Database till motsvarande tabell i Azure Synapse Analytics med hjälp av [mellanlagrad kopia via Blob Storage och PolyBase](connector-azure-sql-data-warehouse.md#use-polybase-to-load-data-into-azure-synapse-analytics) för bästa prestanda. I det här exemplet skickar den första pipelinen listan med tabeller som värde för parametern. 

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar
* **Azure Storage konto**. Azure Storage-kontot används för mellanlagring för Blob Storage i masskopieringsåtgärden. 
* **Azure SQL Database**. Den här databasen innehåller källdata. Skapa en databas i SQL Database med Adventure Works LT-exempel data efter [skapa en databas i Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md) artikeln. Den här självstudien kopierar alla tabeller från den här exempel databasen till en Azure Synapse-analys.
* **Azure Synapse-analys**. Det här datalagret innehåller de data som kopieras från SQL Database. Om du inte har en Azure Synapse Analytics-arbetsyta läser du artikeln [Kom igång med Azure Synapse Analytics](..\synapse-analytics\get-started.md) för att skapa en.

## <a name="azure-services-to-access-sql-server"></a>Azure-tjänster för åtkomst till SQL-servern

Ge Azure-tjänster åtkomst till SQL Server för både SQL Database-och Azure Synapse-analys. Se till att **Tillåt att Azure-tjänster och-resurser har åtkomst till den här server** inställningen är aktive **rad för** servern. Med den här inställningen kan Data Factory tjänsten läsa data från din Azure SQL Database och skriva data till din Azure Synapse-analys. 

Om du vill kontrol lera och aktivera den här inställningen går du till Server > säkerhets > brand väggar och virtuella nätverk > ställer in **Tillåt Azure-tjänster och-resurser för att få åtkomst till den här servern** **på**.

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Starta webbläsaren **Microsoft Edge** eller **Google Chrome**. Användargränssnittet för Data Factory stöds för närvarande bara i webbläsarna Microsoft Edge och Google Chrome.
1. Gå till [Azure-portalen](https://portal.azure.com). 
1. Till vänster på Azure Portal-menyn väljer du **skapa en resurs**  >  **integration**  >  **Data Factory**. 

   ![Valet Data Factory i fönstret Nytt](./media/doc-common-process/new-azure-data-factory-menu.png)
1. På sidan **ny data fabrik** anger du **ADFTutorialBulkCopyDF** som **namn**. 
 
   Namnet på Azure Data Factory måste vara **globalt unikt**. Om följande fel visas för namnfältet ändrar du namnet på datafabriken (till exempel dittnamnADFTutorialBulkCopyDF). Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
  
    ```text
    Data factory name "ADFTutorialBulkCopyDF" is not available
    ```
1. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i. 
1. För **resursgruppen** utför du något av följande steg:
     
   - Välj **Använd befintlig** och välj en befintlig resurs grupp i den nedrullningsbara listan. 
   - Välj **Skapa ny** och ange namnet på en resurs grupp.   
         
     Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/management/overview.md).  
1. Välj **V2** för **versionen**.
1. Välj **plats** för datafabriken. Om du vill se en lista med Azure-regioner där Data Factory är tillgängligt för närvarande markerar du de regioner du är intresserad av på följande sida. Expandera sedan **Analytics** och leta rätt på **Data Factory**: [Tillgängliga produkter per region](https://azure.microsoft.com/global-infrastructure/services/). Datalagren (Azure Storage, Azure SQL Database osv.) och beräkningarna (HDInsight osv.) som används i Data Factory kan finnas i andra regioner.
1. Klicka på **Skapa**.
1. När du har skapat filen väljer du **gå till resurs** för att gå till sidan **Data Factory** . 
   
1. Klicka på panelen **Författare och övervakare** för att starta användargränssnittet för Data Factory på en separat flik.


## <a name="create-linked-services"></a>Skapa länkade tjänster
Du kan skapa länkade tjänster för att länka dina datalager och beräkna till en datafabrik. En länkad tjänst har anslutningsinformationen som Data Factory-tjänsten använder för att ansluta till datalagret i körningsfasen. 

I den här självstudien länkar du Azure SQL Database, Azure Synapse Analytics och Azure Blob Storage data lager till din data fabrik. Azure SQL Database är källdatalagret. Azure Synapse Analytics är data lagret för mottagare/mål. Azure-Blob Storage är att mellanlagra data innan data läses in i Azure Synapse Analytics genom att använda PolyBase. 

### <a name="create-the-source-azure-sql-database-linked-service"></a>Skapa den länkade tjänsten för Azure SQL Database-källan
I det här steget skapar du en länkad tjänst för att länka databasen i Azure SQL Database till data fabriken. 

1. Öppna [fliken Hantera](./author-management-hub.md) i det vänstra fönstret.

1. På sidan länkade tjänster väljer du **+ ny** för att skapa en ny länkad tjänst.

   :::image type="content" source="./media/doc-common-process/new-linked-service.png" alt-text="Ny länkad tjänst.":::
1. I fönstret **New Linked Service** (Ny länkad tjänst) väljer du **Azure SQL Database** och klickar på **Fortsätt**. 
1. Utför följande steg i fönstret **ny länkad tjänst (Azure SQL Database)** : 

    a. Ange **AzureSqlDatabaseLinkedService** som **namn**.

    b. Välj servern som **Server namn**
    
    c. Välj din databas som **databas namn**. 
    
    d. Ange **namnet på den användare** som ska anslutas till databasen. 
    
    e. Ange **lösenord** för användaren. 

    f. Om du vill testa anslutningen till databasen med den angivna informationen klickar du på **Testa anslutning**.
  
    ex. Klicka på **skapa** för att spara den länkade tjänsten.


### <a name="create-the-sink-azure-synapse-analytics-linked-service"></a>Skapa en länkad Azure Synapse Analytics-tjänst för mottagare

1. På fliken **Anslutningar** klickar du på **+ Ny** på verktygsfältet igen. 
1. I fönstret **ny länkad tjänst** väljer du **Azure Synapse Analytics** och klickar på **Fortsätt**. 
1. Utför följande steg i fönstret **ny länkad tjänst (Azure Synapse Analytics)** : 
   
    a. Ange **AzureSqlDWLinkedService** som **namn**.
     
    b. Välj servern som **Server namn**
     
    c. Välj din databas som **databas namn**. 
     
    d. Ange **användar namn** för att ansluta till databasen. 
     
    e. Ange **lösen ordet** för användaren. 
     
    f. Om du vill testa anslutningen till databasen med den angivna informationen klickar du på **Testa anslutning**.
     
    ex. Klicka på **Skapa**.

### <a name="create-the-staging-azure-storage-linked-service"></a>Skapa den länkade tjänsten för Azure Storage-mellanlagringen
I den här självstudien använder du Azure Blob Storage som ett mellanlagringsutrymme så att PolyBase aktiveras och ger kopieringen bättre prestanda.

1. På fliken **Anslutningar** klickar du på **+ Ny** på verktygsfältet igen. 
1. I fönstret **New Linked Service** (Ny länkad tjänst) väljer du **Azure Blob Storage** och klickar på **Fortsätt**. 
1. Utför följande steg i fönstret **ny länkad tjänst (Azure Blob Storage)** : 

    a. Ange **AzureStorageLinkedService** som **namn**.                                                 
    b. Välj ditt **Azure-lagringskonto** som **Lagringskontonamn**.
    
    c. Klicka på **Skapa**.

## <a name="create-datasets"></a>Skapa datauppsättningar
I den här självstudien skapar du datauppsättningar för källa och mottagare som anger var data lagras. 

Datauppsättningen för indata **AzureSqlDatabaseDataset** refererar till **AzureSqlDatabaseLinkedService**. Den länkade tjänsten anger anslutningssträngen för att ansluta till databasen. Datauppsättningen anger namnet på databasen och tabellen som innehåller källdata. 

Datauppsättningen för utdata **AzureSqlDWDataset** refererar till **AzureSqlDWLinkedService**. Den länkade tjänsten anger anslutnings strängen för att ansluta till Azure Synapse Analytics. Datauppsättningen anger databasen och tabellen som data kopieras till. 

I den här självstudien är käll- och måltabellerna i SQL inte hårdkodade i definitionen för datauppsättningen. Istället skickar ForEach-aktiviteten tabellens namn under körningsfasen till kopieringsaktiviteten. 

### <a name="create-a-dataset-for-source-sql-database"></a>Skapa en datauppsättning för SQL Database-källan

1. Välj fliken **författare** i det vänstra fönstret.

1. Välj **+** (plus) i den vänstra rutan och välj sedan **data uppsättning**. 

    ![Menyn Ny datauppsättning](./media/tutorial-bulk-copy-portal/new-dataset-menu.png)
1. I fönstret **ny data uppsättning** väljer du **Azure SQL Database** och klickar sedan på **Fortsätt**. 
    
1. I fönstret **Ange egenskaper** , under **namn**, anger du **AzureSqlDatabaseDataset**. Under **länkad tjänst** väljer du **AzureSqlDatabaseLinkedService**. Klicka sedan på **OK**.

1. Växla till fliken **anslutning** , Välj valfri tabell för **tabell**. Den här tabellen är en dummytabell. Du anger en fråga för källdatauppsättningen när du skapar en pipeline. Frågan används för att extrahera data från databasen. Alternativt kan du klicka på kryss rutan **Redigera** och ange **dbo. dummyName** som tabell namn. 
 

### <a name="create-a-dataset-for-sink-azure-synapse-analytics"></a>Skapa en data uppsättning för Sink Azure Synapse Analytics 

1. Klicka på **+ (plus)** i den vänstra rutan och sedan på **Datauppsättning**. 
1. I fönstret **ny data uppsättning** väljer du **Azure Synapse Analytics** och klickar sedan på **Fortsätt**.
1. I fönstret **Ange egenskaper** , under **namn**, anger du **AzureSqlDWDataset**. Under **länkad tjänst** väljer du **AzureSqlDWLinkedService**. Klicka sedan på **OK**.
1. Byt till fliken **Parametrar**, klicka på **+ Ny** och ange **DWTableName** som parameternamn. Klicka på **+ ny** igen och ange **DWSchema** som parameter namn. Om du kopierar eller klistrar in det här namnet från sidan kontrollerar du att det inte finns något **avslutande blank stegs tecken** i slutet av *DWTableName* och *DWSchema*. 
1. Växla till fliken **Anslutningar**. 

    1. För **tabell** kontrollerar du alternativet **Redigera** . Välj i den första inmatade rutan och klicka på länken **Lägg till dynamiskt innehåll** nedan. På sidan **Lägg till dynamiskt innehåll** klickar du på **DWSchema** under **parametrar**, som fyller automatiskt i text rutan för det översta uttrycket `@dataset().DWSchema` och klickar sedan på **Slutför**.  
    
        ![Data uppsättnings anslutningens tabell namn](./media/tutorial-bulk-copy-portal/dataset-connection-tablename.png)

    1. Välj i den andra inmatade rutan och klicka på länken **Lägg till dynamiskt innehåll** nedan. På sidan **Lägg till dynamiskt innehåll** klickar du på **DWTAbleName** under **parametrar**, som fyller automatiskt i text rutan för det översta uttrycket `@dataset().DWTableName` och klickar sedan på **Slutför**. 
    
    1. Egenskapen **TableName** för data mängden anges till de värden som skickas som argument för parametrarna **DWSchema** och **DWTableName** . ForEach-aktiviteten itereras via en lista över tabeller och skickar en i taget till kopieringsaktiviteten. 
    

## <a name="create-pipelines"></a>Skapa pipelines
I den här självstudien skapar du två pipeliner: **IterateAndCopySQLTables** och **GetTableListAndTriggerCopyData**. 

**GetTableListAndTriggerCopyData** -pipeline utför två åtgärder:

* Den söker i Azure SQL Database-systemtabellen för att få fram listan med tabeller som ska kopieras
* Utlöser pipeline- **IterateAndCopySQLTables** för att utföra den faktiska data kopieringen.

**IterateAndCopySQLTables** -pipeline tar en lista med tabeller som en parameter. För varje tabell i listan kopieras data från tabellen i Azure SQL Database till Azure Synapse Analytics med hjälp av mellanlagrad kopiering och PolyBase.

### <a name="create-the-pipeline-iterateandcopysqltables"></a>Skapa pipelinen IterateAndCopySQLTables

1. I den vänstra rutan klickar du på **+ (plus)** och sedan på **Pipeline**.

    ![Meny för ny pipeline](./media/tutorial-bulk-copy-portal/new-pipeline-menu.png)
 
1. I panelen Allmänt under **Egenskaper** anger du **IterateAndCopySQLTables** som **namn**. Komprimera sedan panelen genom att klicka på egenskaps ikonen i det övre högra hörnet.

1. Växla till fliken **Parametrar** och gör följande: 

    a. Klicka på **+ Ny**. 
    
    b. Ange **tablelist** som parameter **namn**.
    
    c. Välj **Matris** för **Typ**.

1. I verktygslådan **Aktiviteter** expanderar du **Iteration & Conditions** (Iteration och villkor) och drar och släpper aktiviteten **ForEach** till pipelinedesignytan. Du kan också söka efter aktiviteter i verktygslådan **Aktiviteter**. 

    a. I fliken **Allmänt** längst ned skriver du **IterateSQLTables** för **Namn**. 

    b. Växla till fliken **Inställningar** , klicka på kryss rutan för **objekt** och klicka sedan på länken **Lägg till dynamiskt innehåll** nedan. 

    c. På sidan **Lägg till dynamiskt innehåll** döljer du avsnittet **Systemvariabler** och **funktioner** , klickar på **tablelist** under **parametrar**, som automatiskt fyller i text rutan överst uttryck som `@pipeline().parameter.tableList` . Klicka sedan på **Slutför**. 

    ![Byggare för parametern Foreach](./media/tutorial-bulk-copy-portal/for-each-parameter-builder.png)
    
    d. Växla till fliken **aktiviteter** och klicka på **Penn ikonen** för att lägga till en underordnad aktivitet i den **förgrunds** aktiviteten.
    
    ![För-aktivitets byggare](./media/tutorial-bulk-copy-portal/for-each-activity-builder.png)

1. I verktygs lådan **aktiviteter** expanderar du **Flytta & överföring** och drar och släpper **Kopiera data** aktivitet i pipelinens designer-yta. Lägg märke till adressfältmenyn längst upp. **IterateAndCopySQLTable** är pipelinens namn och **IterateSQLTables** är förgrunds aktivitetens namn. Designern är i aktivitetsomfånget. Om du vill växla tillbaka till pipeline-redigeraren från förgrunds redigeraren kan du klicka på länken i menyn för dynamiska länkar. 

    ![Kopiera i ForEach](./media/tutorial-bulk-copy-portal/copy-in-for-each.png)

1. Växla till fliken **Källa** och gör följande:

    1. Välj **AzureSqlDatabaseDataset** för **källdatauppsättning**. 
    1. Välj **frågealternativet** för **Använd fråga**. 
    1. Klicka på textrutan **Fråga** -> välj **Lägg till dynamiskt innehåll** nedan -> ange följande uttryck för **Fråga** -> välj **Slutför**.

        ```sql
        SELECT * FROM [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ``` 

1. Växla till fliken **Mottagare** och gör följande: 

    1. Välj **AzureSqlDWDataset** för **Sink Dataset** (Datauppsättning för mottagare).
    1. Klicka på indatamängden för värdet för parametern DWTableName-> Markera kryss rutan **Lägg till dynamiskt innehåll** nedan, ange `@item().TABLE_NAME` uttrycket som skript, > Välj **Slutför**.
    1. Klicka på indatamängden för värdet för parametern DWSchema-> Markera kryss rutan **Lägg till dynamiskt innehåll** nedan, ange `@item().TABLE_SCHEMA` uttrycket som skript, > Välj **Slutför**.
    1. För kopierings metod väljer du **PolyBase**. 
    1. Avmarkera alternativet **Använd typ som standard** . 
    1. För tabell alternativet är standardinställningen "ingen". Om du inte har skapat tabeller i tabellen Sink Azure Synapse Analytics, aktiverar du alternativet för **Automatisk skapande av tabell** . kopierings aktiviteten skapar sedan automatiskt tabeller åt dig baserat på källdata. Mer information finns i [skapa mottagar tabeller automatiskt](copy-activity-overview.md#auto-create-sink-tables). 
    1. Klicka på textrutan **Pre-copy Script** (Förkopieringsskript) -> välj **Lägg till dynamiskt innehåll** nedan -> ange följande uttryck för Fråga -> välj **Slutför**. 

        ```sql
        TRUNCATE TABLE [@{item().TABLE_SCHEMA}].[@{item().TABLE_NAME}]
        ```

        ![Inställningar för att kopiera mottagare](./media/tutorial-bulk-copy-portal/copy-sink-settings.png)

1. Växla till fliken **Inställningar** och gör följande: 

    1. Markera kryss rutan för **att aktivera mellanlagring**.
    1. Välj **AzureStorageLinkedService** för **Store Account Linked Service** (Länkad tjänst för lagringskonto).

1. Verifiera pipelineinställningarna genom att klicka på **Verifiera** i verktygsfältet för pipelinen. Kontrol lera att det inte finns något validerings fel. Om du vill stänga **verifierings rapporten för pipeline** klickar du på vinkelparenteser med dubbla vinklar **>>** .

### <a name="create-the-pipeline-gettablelistandtriggercopydata"></a>Skapa pipelinen GetTableListAndTriggerCopyData

Den här pipelinen gör två åtgärder:

* Den söker i Azure SQL Database-systemtabellen för att få fram listan med tabeller som ska kopieras
* den utlöser pipelinen ”IterateAndCopySQLTables” för att utföra den faktiska kopieringen.

Här följer stegen för att skapa pipelinen:

1. I den vänstra rutan klickar du på **+ (plus)** och sedan på **Pipeline**.
1. I panelen Allmänt under **Egenskaper** ändrar du namnet på pipelinen till **GetTableListAndTriggerCopyData**. 

1. I verktygs lådan **aktiviteter** expanderar du **Allmänt** och drar och släpper **söknings** aktivitet till pipelinen för pipelinen och utför följande steg:

    1. Skriv **LookupTableList** som **namn**. 
    1. Ange **Hämta tabell listan från databasen** för **Beskrivning**.

1. Växla till fliken **Inställningar** och gör följande:

    1. Välj **AzureSqlDatabaseDataset** för **källdatauppsättning**. 
    1. Välj **fråga** för **Använd fråga**. 
    1. Ange följande SQL-fråga för **Fråga**.

        ```sql
        SELECT TABLE_SCHEMA, TABLE_NAME FROM information_schema.TABLES WHERE TABLE_TYPE = 'BASE TABLE' and TABLE_SCHEMA = 'SalesLT' and TABLE_NAME <> 'ProductModel'
        ```
    1. Avmarkera kryssrutan för fältet **First row only** (Endast första raden).

        ![Sökningsaktivitet – inställningssida](./media/tutorial-bulk-copy-portal/lookup-settings-page.png)
1. Dra och släpp **pipeline** -aktivitet från aktivitets lådan till pipelinen och ange namnet till **TriggerCopy**.

1. Om du vill **ansluta** **söknings** aktiviteten till aktiviteten **Kör pipeliner** drar du den **gröna rutan** som är kopplad till Sök aktiviteten till vänster om aktiviteten kör pipeline.

    ![Ansluta kopierings- och Kör pipeline-aktiviteter](./media/tutorial-bulk-copy-portal/connect-lookup-execute-pipeline.png)

1. Växla till fliken **Inställningar** för aktiviteten **Kör pipeline** och utför följande steg: 

    1. Välj **IterateAndCopySQLTables** som **Invoked pipeline** (Anropad pipeline). 
    1. Avmarkera kryss rutan för **vänta på slut för ande**.
    1. I avsnittet **parametrar** klickar du på rutan inmatare under värde-> väljer värdet **Lägg till dynamiskt innehåll** nedan – > ange `@activity('LookupTableList').output.value` som tabell namn värde – > väljer du **Slutför**. Du ställer in resultat listan från söknings aktiviteten som inmatad till den andra pipelinen. Resultatlistan innehåller listan över tabeller vars data ska kopieras till målet. 

        ![Kör pipeline-aktivitet – sidan inställningar](./media/tutorial-bulk-copy-portal/execute-pipeline-settings-page.png)

1. Verifiera pipelinen genom att klicka på **Verifiera** i verktygsfältet. Kontrollera att det inte finns några verifieringsfel. Om du vill stänga **verifieringsrapporten för pipeline** klickar du på **>>**.

1. Om du vill publicera entiteter (data uppsättningar, pipeliner osv.) till den Data Factory tjänsten klickar du på **publicera allt** ovanpå fönstret. Vänta tills publiceringen har lyckats. 

## <a name="trigger-a-pipeline-run"></a>Utlösa en pipelinekörning

1. Gå till pipeline- **GetTableListAndTriggerCopyData**, klicka på **Lägg till utlösare** i det övre pipeline-verktygsfältet och klicka sedan på **Utlös nu**. 

1. Bekräfta körningen på sidan **Kör pipelinen** och välj sedan **Slutför**.

## <a name="monitor-the-pipeline-run"></a>Övervaka pipelinekörningen

1. Växla till fliken **övervaka** . Klicka på **Uppdatera** tills du ser körningar för båda pipelinen i din lösning. Fortsätt att uppdatera listan tills du ser statusen **Lyckades**. 

1. Om du vill visa aktivitets körningar som är associerade med **GetTableListAndTriggerCopyData** -pipeline klickar du på länken pipeline-namn för pipelinen. Du ska se två aktivitetskörningar för den här pipelinekörningen. 
    ![Övervaka pipeline-körning](./media/tutorial-bulk-copy-portal/monitor-pipeline.png)
1. Om du vill visa resultatet av **uppslags** aktiviteten klickar du på länken **utdata** bredvid AKTIVITETEN under kolumnen **aktivitets namn** . Du kan maximera och återställa fönstret **Utdata**. Stäng fönstret **Utdata** när du har sett klart genom att klicka på **X**.

    ```json
    {
        "count": 9,
        "value": [
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Customer"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Product"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductModelProductDescription"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "ProductCategory"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "Address"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "CustomerAddress"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderDetail"
            },
            {
                "TABLE_SCHEMA": "SalesLT",
                "TABLE_NAME": "SalesOrderHeader"
            }
        ],
        "effectiveIntegrationRuntime": "DefaultIntegrationRuntime (East US)",
        "effectiveIntegrationRuntimes": [
            {
                "name": "DefaultIntegrationRuntime",
                "type": "Managed",
                "location": "East US",
                "billedDuration": 0,
                "nodes": null
            }
        ]
    }
    ```    
1. Om du vill växla tillbaka till vyn **pipelines körs** klickar du på **alla pipeline-körningar** överst i menyn för dynamiska länkar. Klicka på **IterateAndCopySQLTables** länk (under **pipeline Name** -kolumnen) om du vill visa aktivitets körningar för pipelinen. Observera att det finns en **kopierings** aktivitet som körs för varje tabell i utdata för **söknings** aktiviteten. 

1. Bekräfta att data har kopierats till den Azure Synapse-analys som du använde i den här självstudien. 

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
>[Kopiera data stegvis](tutorial-incremental-copy-portal.md)
