---
title: Kopiera lokala data med Azure Kopiera data-verktyget
description: Skapa en Azure Data Factory och Använd sedan Kopiera data-verktyget för att kopiera data från en SQL Server-databas till Azure Blob Storage.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 11/09/2020
ms.openlocfilehash: 33dd1a1ca203ee57d3ef263bc5e8686841f03c1f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100391260"
---
# <a name="copy-data-from-a-sql-server-database-to-azure-blob-storage-by-using-the-copy-data-tool"></a>Kopiera data från en SQL Server-databas till Azure Blob Storage med hjälp av Kopiera data-verktyget
> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuell version](tutorial-hybrid-copy-data-tool.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

I den här självstudien skapar du en datafabrik i Azure Portal. Sedan använder du verktyget Kopiera data för att skapa en pipeline som kopierar data från en SQL Server-databas till Azure Blob Storage.

> [!NOTE]
> - Om du inte har använt Azure Data Factory tidigare kan du läsa [Introduktion till Data Factory](introduction.md).

I den här självstudien får du göra följande:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Använd verktyget Kopiera data för att skapa en pipeline.
> * Övervaka pipelinen och aktivitetskörningarna.

## <a name="prerequisites"></a>Förutsättningar
### <a name="azure-subscription"></a>Azure-prenumeration
Om du inte redan har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

### <a name="azure-roles"></a>Azure-roller
Om du vill skapa Data Factory-instanser måste det användar konto som du använder för att logga in på Azure vara tilldelat en *deltagar* -eller *ägar* roll eller måste vara *administratör* för Azure-prenumerationen.

Gå till Azure Portal om du vill se vilka behörigheter du har i prenumerationen. Välj användarnamnet längst upp till höger och välj sedan **Behörigheter**. Om du har åtkomst till flera prenumerationer väljer du rätt prenumeration. Exempel instruktioner för hur du lägger till en användare till en roll finns i [lägga till eller ta bort Azure Role-tilldelningar med hjälp av Azure Portal](../role-based-access-control/role-assignments-portal.md).

### <a name="sql-server-2014-2016-and-2017"></a>SQL Server 2014, 2016 och 2017
I den här självstudien använder du en SQL Server-databas som *käll* data lager. Pipelinen i data fabriken som du skapar i den här självstudien kopierar data från den här SQL Server databasen (källa) till Blob Storage (mottagare). Skapa sedan en tabell med namnet **emp** i SQL Server-databasen och infoga ett par exempelposter i tabellen.

1. Starta SQL Server Management Studio. Om det inte redan är installerat på datorn öppnar du [Ladda ner SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).

1. Anslut till SQL Server-instansen med hjälp av dina autentiseringsuppgifter.

1. Skapa en exempeldatabas. I trädvyn högerklickar du på **Databaser** och sedan väljer du **Ny databas**.

1. I fönstret **Ny databas** anger du ett namn för databasen och sedan väljer du **OK**.

1. Skapa tabellen **emp** och infoga lite exempeldata i den genom att köra följande frågeskript mot databasen. I trädvyn högerklickar du på databasen du skapade och sedan väljer du **Ny fråga**.

    ```sql
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
I den här självstudien använder du ett allmänt Azure Storage-konto (Blob Storage, för att vara specifik) som datalager för destination eller mottagare. Om du inte har något allmänt lagringskonto finns det anvisningar om hur du skapar ett i artikeln [Skapa ett lagringskonto](../storage/common/storage-account-create.md). Pipelinen i data fabriken som du skapar i den här självstudien kopierar data från den SQL Server databasen (källa) till Blob Storage (mottagare). 

#### <a name="get-the-storage-account-name-and-account-key"></a>Hämta lagringskontots namn och åtkomstnyckel
Du använder namnet och nyckeln för lagringskontot i den här självstudien. Gör så här för att hämta namnet och nyckeln till lagringskontot:

1. Logga in på [Azure Portal](https://portal.azure.com) med användarnamnet och lösenordet för Azure.

1. Välj **Alla tjänster** i rutan till vänster. Filtrera genom att använda nyckelordet **Lagring** och välj sedan **Lagringskonton**.

    ![Lagringskontosökning](media/doc-common-process/search-storage-account.png)

1. Filtrera på ditt lagringskonto (om det behövs) i listan med lagringskonton. Välj sedan ditt lagringskonto.

1. I fönstret **Lagringskonto** väljer du **Åtkomstnycklar**.


1. I rutorna **Lagringskontonamn** och **key1** kopierar du värdena och klistrar sedan in dem i Anteckningar eller annat redigeringsprogram så att du har dem när du behöver dem senare i självstudien.

#### <a name="create-the-adftutorial-container"></a>Skapa containern adftutorial
I det här avsnittet skapar du en blobcontainer med namnet **adftutorial** i Blob Storage.

1. I fönstret **Lagringskonto** växlar du till **Översikt** och klickar sedan på **Blobar**.

1. I fönstret **blobbar** väljer du **+ container**.

1. I fönstret **ny behållare** , under **namn**, anger du **adftutorial** och väljer sedan **OK**.

1. Välj **adftutorial** i listan över containrar.


1. Behåll fönstret **container** för **adftutorial** Open. Du använder den för att verifiera utdata i slutet av självstudien. Data Factory skapar automatiskt utdatamappen i den här containern, så du behöver inte skapa en.


## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Välj **skapa en resurs**  >  **integrations**  >  **Data Factory** på menyn till vänster.

   ![Skapa ny datafabrik](./media/doc-common-process/new-azure-data-factory-menu.png)

1. I fönstret **Ny datafabrik**, under **Namn** anger du **ADFTutorialDataFactory**.

   Namnet på data fabriken måste vara *globalt unikt*. Om följande felmeddelande visas för namnfältet ändrar du namnet på datafabriken (t.ex. dittnamnADFTutorialDataFactory). Se artikeln [Namnregler för Data Factory](naming-rules.md) för namnregler för Data Factory-artefakter.

   ![Namn på ny datafabrik](./media/doc-common-process/name-not-available-error.png)
1. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i.
1. Gör något av följande för **Resursgrupp**:

   - Välj **Använd befintlig** och välj en befintlig resurs grupp i den nedrullningsbara listan.

   - Välj **Skapa ny** och ange namnet på en resurs grupp. 
        
     Mer information om resursgrupper finns i [Använda resursgrupper för att hantera Azure-resurser](../azure-resource-manager/management/overview.md).
1. Under **Version** väljer du **V2**.
1. Under **plats** väljer du platsen för data fabriken. Endast platser som stöds visas i listrutan. Datalagren (t.ex. Azure Storage och SQL-databas) och beräkningarna (t.ex. Azure HDInsight) som används i Data Factory kan finnas på andra platser/i andra regioner.
1. Välj **Skapa**.

1. När den har skapats visas sidan **Data Factory** som visas i bilden.

     ![Datafabrikens startsida](./media/doc-common-process/data-factory-home-page.png)
1. Välj **Författare och övervakare** för att starta användargränssnittet för Data Factory på en separat flik.

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Använd verktyget Kopiera data för att skapa en pipeline

1. Sidan **Nu sätter vi igång** visas. Välj **Kopiera data** för att starta verktyget Kopiera data.

   ![Sidan Kom igång](./media/doc-common-process/get-started-page.png)

1. På sidan **Egenskaper** för verktyget Kopiera data går du till **Uppgiftsnamn** och anger **CopyFromOnPremSqlToAzureBlobPipeline**. Välj sedan **Nästa**. Verktyget Kopiera data skapar en pipeline med det namn som du anger i det här fältet.
  ![Uppgiftsnamn](./media/tutorial-hybrid-copy-data-tool/properties-page.png)

1. På sidan **Källdatalager** klickar du på **Skapa ny anslutning**.

1. Sök efter **SQL Server** under **ny länkad tjänst** och välj sedan **Fortsätt**.

1. I dialog rutan **ny länkad tjänst (SQL Server)** under **namn**, anger du **SqlServerLinkedService**. Välj **+Ny** under **Connect via integration runtime** (Anslut via Integration Runtime). Du måste skapa en lokal integreringskörning, ladda ned den på din dator och registrera den med Data Factory. Den lokala integreringskörningen kopierar data mellan din lokala miljö och molnet.

1. I dialog rutan **integration runtime installation** väljer du **egen värd**. Välj sedan **Fortsätt**.

   ![Skapa integration runtime](./media/tutorial-hybrid-copy-data-tool/create-self-hosted-integration-runtime.png)

1. I dialog rutan **integration runtime-konfiguration** , under **namn**, anger du **TutorialIntegrationRuntime**. Välj sedan **Skapa**.

1. I dialog rutan **integration runtime installation** väljer **du klicka här för att starta Express installationen för den här datorn**. Med den här åtgärden installeras integreringskörningen på datorn och registreras med Data Factory. Alternativt kan du använda det manuella installationsalternativet för att ladda ned installationsfilen, köra den och använda nyckeln för att registrera integreringskörning.

1. Kör programmet som laddats ned. I fönstret visas status för expressinstallationen.

    ![Status för expressinstallation](./media/tutorial-hybrid-copy-data-tool/express-setup-status.png)

1. I dialog rutan **ny länkad tjänst (SQL Server)** bekräftar du att **TutorialIntegrationRuntime** har valts för fältet integration Runtime. Utför sedan följande steg:

    a. Under **Namn** anger du **SqlServerLinkedService**.

    b. Under **Servernamn** anger du namnet på SQL Server-instansen.

    c. Under **Databasnamn** anger du namnet på din lokala databas.

    d. Under **Autentiseringstyp** väljer du lämplig autentisering.

    e. Under **användar namn** anger du namnet på användaren med åtkomst till SQL Server.

    f. Ange **lösenordet** för användaren.

    ex. Testa anslutning och välj **Slutför**.

      ![Vald integreringskörning](./media/tutorial-hybrid-copy-data-tool/integration-runtime-selected.png)

1. På sidan **käll data lager** väljer du **Nästa**.

1. På sidan **Välj tabellen som data ska kopieras från eller använd en anpassad fråga**, välj tabellen **[dbo].[emp]** i listan och välj **Nästa**. Du kan välja andra tabeller baserade på din databas.

1. På sidan **Måldatalager** väljer du **Skapa ny anslutning**


1. I **ny länkad tjänst** söker du och väljer **Azure Blob** och väljer sedan **Fortsätt**.

   ![Val av Blob Storage](./media/tutorial-hybrid-copy-data-tool/select-destination-data-store.png)

1. Utför följande steg i dialogrutan **New Linked Service (Azure Blob Storage)** (Ny länkad tjänst):

   a. Under **namn** anger du **AzureStorageLinkedService**.

   b. Under **Connect via integration runtime** (Anslut via Integration Runtime) väljer du **TutorialIntegrationRuntime**

   c. Under **Lagringskontonamn** väljer du ditt lagringskonto i listrutan.

   d. Välj **Slutför**.

1. I dialog rutan **mål för data lager** kontrollerar du att **Azure Blob Storage** är markerat. Välj sedan **Nästa**.

1. I dialogrutan **Välj utdatafil eller mapp** under **Mappsökväg** anger du **adftutorial/fromonprem**. Du skapade containern **adftutorial** som en del av förutsättningarna. Om utdatamappen inte finns (i det här fallet **fromonprem**) skapas den automatiskt av Data Factory. Du kan också använda knappen **Bläddra** för att bläddra i Blob Storage och dess behållare/mappar. Om du inte anger något värde under **Filnamn** används som standard namnet från källan (i det här fallet **dbo.emp**).

   ![Välj utdatafil eller mapp](./media/tutorial-hybrid-copy-data-tool/choose-output-file-folder.png)

1. I dialogrutan **Filformatsinställningar** väljer du **Nästa**.

1. I dialogrutan **Inställningar** väljer du **Nästa**.

1. Granska värdena för alla inställningar i dialogrutan **Sammanfattning** och välj **Nästa**.

1. Välj **Övervaka** på sidan **Distribution** för att övervaka pipelinen (aktiviteten). 

1. När pipeline-körningen är klar kan du Visa status för den pipeline som du skapade. 

1. På sidan pipeliner körs väljer du **Uppdatera** för att uppdatera listan. Klicka på länken under **pipeline-namn** om du vill visa aktivitets körnings information eller kör pipelinen igen. 

1. På sidan aktivitets körningar väljer du länken **information** (glasögon ikonen) under kolumnen **aktivitets namn** för mer information om kopierings åtgärden. Om du vill gå tillbaka till vyn pipeline-körningar väljer du länken **alla pipeline-körningar** på menyn för dynamiska länkar. Välj **Uppdatera** för att uppdatera vyn.

1. Bekräfta att utdatafilen visas i mappen **fromonprem** för containern **adftutorial**.

1. Klicka på fliken **Redigera** till vänster för att växla till redigeringsläget. Du kan uppdatera de länkade tjänster, datauppsättningar och pipeliner som skapats med verktyget med hjälp av redigeraren. Klicka på **Kod** för att visa JSON-koden som är associerad med den entitet som har öppnats i redigeraren. Mer information om hur du redigerar dessa entiteter i användargränssnittet för Data Factory finns i [Azure Portal-versionen av den här självstudiekursen](tutorial-copy-data-portal.md).


## <a name="next-steps"></a>Nästa steg
Pipelinen i det här exemplet kopierar data från en SQL Server-databas till Blob Storage. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Använd verktyget Kopiera data för att skapa en pipeline.
> * Övervaka pipelinen och aktivitetskörningarna.

En lista över data lager som stöds av Data Factory finns i [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

Fortsätt till följande självstudie för att lära dig att masskopiera data från en källa till ett mål:

> [!div class="nextstepaction"]
>[Masskopiera data](tutorial-bulk-copy-portal.md)
