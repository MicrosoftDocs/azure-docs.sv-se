---
title: Kopiera data från SQL Server till Blob Storage med Azure Portal
description: Lär dig hur du kopierar data från ett lokalt datalager till molnet med hjälp av en lokal Integration Runtime i Azure Data Factory.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 02/18/2021
ms.openlocfilehash: fa251b80f8e44f48de565685a0841f57f48620f1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101724973"
---
# <a name="copy-data-from-a-sql-server-database-to-azure-blob-storage"></a>Kopiera data från en SQL Server-databas till Azure Blob Storage

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här självstudien använder du Azure Data Factory användar gränssnitt (UI) för att skapa en Data Factory-pipeline som kopierar data från en SQL Server-databas till Azure Blob Storage. Du skapar och använder en lokal installation av Integration Runtime som flyttar data mellan lokala datalager och datalager i molnet.

> [!NOTE]
> Den här artikeln ger inte någon detaljerad introduktion till Data Factory. Mer information finns i [Introduktion till Data Factory](introduction.md).

I den här självstudien får du göra följande:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa Integration Runtime med egen värd.
> * Skapa länkade tjänster för SQL Server och Azure Storage.
> * Skapa datauppsättningar för SQL Server och Azure Blob.
> * Skapa en pipeline med en kopieringsaktivitet för att flytta data.
> * Starta en pipelinekörning.
> * Övervaka pipelinekörningen.

## <a name="prerequisites"></a>Förutsättningar
### <a name="azure-subscription"></a>Azure-prenumeration
Om du inte redan har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

### <a name="azure-roles"></a>Azure-roller
Om du vill skapa Data Factory-instanser måste det användar konto som du använder för att logga in på Azure vara tilldelat en *deltagar* -eller *ägar* roll eller måste vara *administratör* för Azure-prenumerationen.

Gå till Azure Portal om du vill se vilka behörigheter du har i prenumerationen. Välj användarnamnet längst upp till höger och välj sedan **Behörigheter**. Om du har åtkomst till flera prenumerationer väljer du rätt prenumeration. Exempel instruktioner om hur du lägger till en användare i en roll finns i [tilldela Azure-roller med hjälp av Azure Portal](../role-based-access-control/role-assignments-portal.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 och 2017
I den här självstudien använder du en SQL Server-databas som *käll* data lager. Pipelinen i data fabriken som du skapar i den här självstudien kopierar data från den här SQL Server databasen (källa) till Blob Storage (mottagare). Skapa sedan en tabell med namnet **emp** i SQL Server-databasen och infoga ett par exempelposter i tabellen.

1. Starta SQL Server Management Studio. Om det inte redan är installerat på datorn öppnar du [Ladda ner SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

1. Anslut till SQL Server-instansen med hjälp av dina autentiseringsuppgifter.

1. Skapa en exempeldatabas. I trädvyn högerklickar du på **Databaser** och sedan väljer du **Ny databas**.
1. I fönstret **Ny databas** anger du ett namn för databasen och sedan väljer du **OK**.

1. Skapa tabellen **emp** och infoga lite exempeldata i den genom att köra följande frågeskript mot databasen. I trädvyn högerklickar du på databasen du skapade och sedan väljer du **Ny fråga**.

   ```
    CREATE TABLE dbo.emp
    (
        ID int IDENTITY(1,1) NOT NULL,
        FirstName varchar(50),
        LastName varchar(50)
    )
    GO

    INSERT INTO emp (FirstName, LastName) VALUES ('John', 'Doe')
    INSERT INTO emp (FirstName, LastName) VALUES ('Jane', 'Doe')
    GO
   ```

### <a name="azure-storage-account"></a>Azure Storage-konto
I den här självstudien använder du ett allmänt Azure Storage-konto (Blob Storage, för att vara specifik) som datalager för destination eller mottagare. Om du inte har något allmänt Azure Storage-konto kan du läsa [Skapa ett lagringskonto](../storage/common/storage-account-create.md). Pipelinen i data fabriken som du skapar i den här självstudien kopierar data från SQL Server databas (källa) till Blob Storage (mottagare). 

#### <a name="get-the-storage-account-name-and-account-key"></a>Hämta lagringskontots namn och åtkomstnyckel
Du använder namnet och nyckeln för lagringskontot i den här självstudien. Gör så här för att hämta namnet och nyckeln till lagringskontot:

1. Logga in på [Azure Portal](https://portal.azure.com) med användarnamnet och lösenordet för Azure.

1. Välj **Alla tjänster** i rutan till vänster. Filtrera genom att använda nyckelordet **Lagring** och välj sedan **Lagringskonton**.

    ![Lagringskontosökning](media/doc-common-process/search-storage-account.png)

1. Filtrera efter ditt lagrings konto i listan över lagrings konton om det behövs. Välj sedan ditt lagringskonto.

1. I fönstret **Lagringskonto** väljer du **Åtkomstnycklar**.

1. I rutorna **Lagringskontonamn** och **key1** kopierar du värdena och klistrar sedan in dem i Anteckningar eller annat redigeringsprogram så att du har dem när du behöver dem senare i självstudien.

#### <a name="create-the-adftutorial-container"></a>Skapa containern adftutorial
I det här avsnittet skapar du en blobcontainer med namnet **adftutorial** i Blob Storage.

1. I fönstret **lagrings konto** går du till **Översikt** och väljer sedan **behållare**.

    ![Alternativet Välj blobar](media/tutorial-hybrid-copy-powershell/select-blobs.png)

1. I **behållare** -fönstret väljer du **+ container** för att skapa en ny.

1. I fönstret **Ny container**, under **Namn** anger du **adftutorial**. Välj sedan **Skapa**.

1. I listan över behållare väljer du **adftutorial** som du nyss skapade.

1. Låt **containerfönstret** för **adftutorial** vara öppet. Du använder den för att verifiera utdata i slutet av självstudien. Data Factory skapar automatiskt utdatamappen i den här containern, så du behöver inte skapa en.

## <a name="create-a-data-factory"></a>Skapa en datafabrik
I det här steget skapar du en datafabrik och startar sedan användargränssnittet för Data Factory för att skapa en pipeline i datafabriken.

1. Öppna webbläsaren **Microsoft Edge** eller **Google Chrome**. Användargränssnittet för Data Factory stöds för närvarande bara i webbläsarna Microsoft Edge och Google Chrome.
1. På den vänstra menyn väljer du **skapa en resurs**  >  **integrations**  >  **Data Factory**:

   ![Valet Data Factory i fönstret Nytt](./media/doc-common-process/new-azure-data-factory-menu.png)

1. I fönstret **Ny datafabrik**, under **Namn** anger du **ADFTutorialDataFactory**.

   Namnet på data fabriken måste vara *globalt unikt*. Om följande felmeddelande visas för namnfältet ändrar du namnet på datafabriken (t.ex. dittnamnADFTutorialDataFactory). Se artikeln [Namnregler för Data Factory](naming-rules.md) för namnregler för Data Factory-artefakter.

   ![Namn på ny datafabrik](./media/doc-common-process/name-not-available-error.png)

1. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i.
1. Gör något av följande för **Resursgrupp**:

   - Välj **Använd befintlig** och välj en befintlig resurs grupp i den nedrullningsbara listan.

   - Välj **Skapa ny** och ange namnet på en resurs grupp.
        
     Mer information om resursgrupper finns i [Använda resursgrupper för att hantera Azure-resurser](../azure-resource-manager/management/overview.md).
1. Under **Version** väljer du **V2**.
1. Under **plats** väljer du platsen för data fabriken. Endast platser som stöds visas i listrutan. Datalagren (t.ex. lagring och SQL-databas) och beräkningarna (t.ex. Azure HDInsight) som används i Data Factory kan finnas i andra regioner.
1. Välj **Skapa**.

1. När datafabriken har skapats visas sidan **Data Factory** som på bilden:

    ![Datafabrikens startsida](./media/doc-common-process/data-factory-home-page.png)
1. Välj panelen **Författare och övervakare** för att starta användargränssnittet för Data Factory på en separat flik.


## <a name="create-a-pipeline"></a>Skapa en pipeline

1. På sidan **Nu sätter vi igång** väljer du **Skapa pipeline**. En pipeline skapas automatiskt åt dig. Pipelinen visas i trädvyn och dess redigerare öppnas.

   ![Sidan Nu sätter vi igång](./media/doc-common-process/get-started-page.png)

1. I panelen Allmänt under **Egenskaper** anger du **SQLServerToBlobPipeline** som **namn**. Komprimera sedan panelen genom att klicka på egenskaps ikonen i det övre högra hörnet.

1. I rutan **aktiviteter** , expanderar du **Flytta & Transform**. Dra och släpp aktiviteten **Kopiera** på pipelinedesignytan. Ge aktiviteten namnet **CopySqlServerToAzureBlobActivity**.

1. I fönstret **Egenskaper** går du till fliken **Källa** och väljer **+ Ny**.

1. I dialog rutan **ny data uppsättning** söker du efter **SQL Server**. Välj **SQL Server** och välj sedan **Fortsätt**.
    ![Ny SqlServer-datauppsättning](./media/tutorial-hybrid-copy-portal/create-sqlserver-dataset.png)

1. I dialog rutan **Ange egenskaper** under **namn** anger du **SqlServerDataset**. Under **länkad tjänst** väljer du **+ ny**. I det här steget skapar du en anslutning till källdatalagret (SQL Server-databasen).

1. I dialog rutan **ny länkad tjänst** lägger du till **namn** som **SqlServerLinkedService**. Under **Anslut via integration runtime** väljer du **+ ny**.  I det här avsnittet kan du skapa en lokal Integration Runtime och koppla den till en lokal dator med SQL Server-databasen. En lokal Integration Runtime är den komponent som kopierar data från SQL Server-databasen på din dator till Blob Storage.

1. I dialog rutan **integration runtime installation** väljer du **egen värd** och väljer sedan **Fortsätt**.

1. Under namn anger du **TutorialIntegrationRuntime**. Välj sedan **Skapa**.

1. För inställningar väljer du **Klicka här för att starta Express installationen för den här datorn**. Med den här åtgärden installeras integreringskörningen på datorn och registreras med Data Factory. Alternativt kan du använda det manuella installationsalternativet för att ladda ned installationsfilen, köra den och använda nyckeln för att registrera integreringskörning.
    ![Installation av Integration Runtime](./media/tutorial-hybrid-copy-portal/intergration-runtime-setup.png)

1. I fönstret **integration Runtime (lokal installation) Express installation** väljer du **Stäng** när processen har slutförts.

    ![Snabbinstallation av Integration Runtime (lokal installation)](./media/tutorial-hybrid-copy-portal/integration-runtime-setup-successful.png)

1. I dialog rutan **ny länkad tjänst (SQL Server)** bekräftar du att **TutorialIntegrationRuntime** har valts under **Anslut via integration runtime**. Utför sedan följande steg:

    a. Under **Namn** anger du **SqlServerLinkedService**.

    b. Under **Servernamn** anger du namnet på SQL Server-instansen.

    c. Under **Databasnamn** anger du namnet på databasen med **emp**-tabellen.

    d. Under **Autentiseringstyp** väljer du den autentiseringstyp som Data Factory ska använda för att ansluta till SQL Server-databasen.

    e. Under **Användarnamn** och **Lösenord** anger du användarnamnet och lösenordet. Använd min *domän \\* som användar namn om det behövs.

    f. Välj **Testanslutning**. Det här steget är att bekräfta att Data Factory kan ansluta till SQL Server-databasen med hjälp av den integration runtime med egen värd som du har skapat.

    ex. Välj **skapa** om du vill spara den länkade tjänsten.
 
    ![Ny länkad tjänst (SQL Server)](./media/tutorial-hybrid-copy-portal/new-sqlserver-linked-service.png)

1. När den länkade tjänsten har skapats går du tillbaka till sidan **Ange egenskaper** för SqlServerDataset. Utför följande steg:

    a. Bekräfta att du ser **SqlServerLinkedService** i **Länkad tjänst**.

    b. Under **tabell namn** väljer du **[dbo]. [ EMP]**.
    
    c. Välj **OK**.

1. Gå till fliken med **SQLServerToBlobPipeline** eller välj **SQLServerToBlobPipeline** i trädvyn.

1. Gå till fliken **Mottagare** längst ned i fönstret **Egenskaper** och välj **+ Ny**.

1. I dialog rutan **ny data uppsättning** väljer du **Azure Blob Storage**. Välj sedan **Fortsätt**.

1. I dialog rutan **Välj format** väljer du format typ för dina data. Välj sedan **Fortsätt**.

    ![Val av data format](./media/doc-common-process/select-data-format.png)

1. I dialog rutan **Ange egenskaper** anger du **AzureBlobDataset** som namn. Vid textrutan **Länkad tjänst** väljer du **+ Nytt**.

1. I dialog rutan **ny länkad tjänst (Azure Blob Storage)** anger du **AzureStorageLinkedService** som namn, väljer ditt lagrings konto i listan **lagrings konto** namn. Testa anslutningen och välj sedan **skapa** för att distribuera den länkade tjänsten.

1. När den länkade tjänsten har skapats är du tillbaka till sidan **Ange egenskaper** . Välj **OK**.

1. Öppna Sink-datauppsättningen. Gör följande på fliken **Anslutning**:

    a. I **Länkad tjänst** bekräftar du att **AzureStorageLinkedService** är vald.

    b. I **fil Sök väg** anger du **adftutorial/Fromonprem** för delen **behållare/katalog** . Om utdatamappen inte finns i containern adftutorial skapas den automatiskt av Data Factory.

    c. I **fil** delen väljer du **Lägg till dynamiskt innehåll**.
    ![dynamiska uttryck för lösning av filnamn](./media/tutorial-hybrid-copy-portal/file-name.png)

    d. Lägg till `@CONCAT(pipeline().RunId, '.txt')` och välj sedan **Slutför**. Den här åtgärden kommer att byta namn på filen med PipelineRunID.txt.

1. Gå till fliken med pipelinen öppen eller välj pipelinen i trädvyn. I **Sink Dataset** (Datauppsättning för mottagare) bekräftar du att **AzureBlobDataset** är vald.

1. Verifiera pipelineinställningarna genom att välja **Verifiera** i verktygsfältet för pipelinen. Om du vill stänga **resultatet av pipe-verifiering** väljer du **>>** ikonen.
    ![verifiera pipeline](./media/tutorial-hybrid-copy-portal/validate-pipeline.png)
    

1. Om du vill publicera entiteter som du har skapat till Data Factory väljer du **publicera alla**.

1. Vänta tills du ser popup-fönstret för **publiceringen har slutförts** . Om du vill kontrol lera publicerings statusen väljer du länken **Visa meddelanden** överst i fönstret. Stäng meddelandefönstret genom att klicka på **Stäng**.


## <a name="trigger-a-pipeline-run"></a>Utlösa en pipelinekörning
Välj **Lägg till utlösare** i verktygsfältet för pipelinen och välj sedan **trigger Now (Utlös nu**).

## <a name="monitor-the-pipeline-run"></a>Övervaka pipelinekörningen

1. Gå till fliken **övervaka** . Du ser pipelinen som du utlöste manuellt i föregående steg.

1. Om du vill visa aktivitets körningar som är associerade med pipeline-körningen väljer du länken **SQLServerToBlobPipeline** under *pipeline-namn*. 
    ![Övervaka pipelinekörningar](./media/tutorial-hybrid-copy-portal/pipeline-runs.png)

1. På sidan **aktivitets körningar** väljer du länken information (glasögon image) för att se information om kopierings åtgärden. Om du vill gå tillbaka till vyn pipeline-körningar väljer du **alla pipelines körs** överst.

## <a name="verify-the-output"></a>Verifiera utdata
Pipelinen skapar automatiskt utdatamappen med namnet *fromonprem* i `adftutorial`-blobcontainern. Bekräfta att du ser filen *[pipeline().RunId].txt* i utdatamappen.


## <a name="next-steps"></a>Nästa steg
Pipelinen i det här exemplet kopierar data från en plats till en annan i Blob Storage. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa Integration Runtime med egen värd.
> * Skapa länkade tjänster för SQL Server och lagring.
> * Skapa datauppsättningar för SQL Server och Blob Storage.
> * Skapa en pipeline med en kopieringsaktivitet för att flytta data.
> * Starta en pipelinekörning.
> * Övervaka pipelinekörningen.

En lista över data lager som stöds av Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

Fortsätt till följande självstudiekurs för att lära dig att masskopiera data från en källa till ett mål:

> [!div class="nextstepaction"]
>[Masskopiera data](tutorial-bulk-copy-portal.md)