---
title: Använd privata slut punkter för att skapa en Azure Data Factory pipeline
description: Den här självstudien innehåller stegvisa instruktioner för att skapa en datafabrik med en pipeline i Azure-portalen. Pipelinen använder kopierings aktiviteten för att kopiera data från Azure Blob Storage till en Azure SQL-databas.
author: linda33wj
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.author: jingwang
ms.openlocfilehash: a3cc3686d4b256fc7b8702f34cdf393dc1b0e25f
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740400"
---
# <a name="copy-data-securely-from-azure-blob-storage-to-a-sql-database-by-using-private-endpoints"></a>Kopiera data säkert från Azure Blob Storage till en SQL-databas med hjälp av privata slut punkter

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här självstudiekursen skapar du en datafabrik med Azure Data Factory-användargränssnittet. *Pipelinen i den här data fabriken kopierar data säkert från Azure Blob Storage till en Azure SQL-databas (som både tillåter åtkomst till valda nätverk) genom att använda privata slut punkter i [Azure Data Factory hanterade Virtual Network](managed-virtual-network-private-endpoint.md).* Konfigurationsmönstret i den här självstudien gäller kopiering av ett filbaserat datalager till ett relationsdatalager. En lista över data lager som stöds som källor och mottagare finns i tabellen [data lager och format som stöds](./copy-activity-overview.md) .

> [!NOTE]
> Om du inte har använt datafabriken tidigare kan du läsa [Introduktion till Azure Data Factory](./introduction.md).

I den här självstudien gör du följande:

* Skapa en datafabrik.
* Skapa en pipeline med en kopieringsaktivitet.


## <a name="prerequisites"></a>Förutsättningar
* **Azure-prenumeration**. Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* **Azure Storage-konto**. Du kan använda Blob Storage som *källa* för datalagringen. Om du inte har ett lagringskonto finns det anvisningar om hur du skapar ett i [Skapa ett Azure Storage-konto](../storage/common/storage-account-create.md?tabs=azure-portal). *Se till att lagrings kontot bara tillåter åtkomst från valda nätverk.* 
* **Azure SQL Database**. Du använder databasen som *mottagare* för datalagringen. Om du inte har en Azure SQL-databas, se [skapa en SQL-databas](../azure-sql/database/single-database-create-quickstart.md) för att skapa en. *Se till att SQL Database-kontot endast tillåter åtkomst från valda nätverk.* 

### <a name="create-a-blob-and-a-sql-table"></a>Skapa en blob och en SQL-tabell

Förbered nu Blob Storage och SQL Database för självstudien genom att utföra följande steg.

#### <a name="create-a-source-blob"></a>Skapa en källblob

1. Öppna Anteckningar. Kopiera följande text och spara den som en **emp.txt**-fil på din disk:

    ```
    FirstName,LastName
    John,Doe
    Jane,Doe
    ```

1. Skapa en behållare med namnet **adftutorial** i Blob Storage. Skapa en mapp som heter **input** i den här containern. Ladda sedan upp filen **emp.txt** till mappen **input**. Använd Azure-portalen eller verktyg som [Azure Storage Explorer](https://storageexplorer.com/) när du gör uppgifterna.

#### <a name="create-a-sink-sql-table"></a>Skapa en SQL-mottagartabell

Använd följande SQL-skript för att skapa tabellen **dbo.emp** i din SQL-databas:

```sql
CREATE TABLE dbo.emp
(
    ID int IDENTITY(1,1) NOT NULL,
    FirstName varchar(50),
    LastName varchar(50)
)
GO

CREATE CLUSTERED INDEX IX_emp_ID ON dbo.emp (ID);
```

## <a name="create-a-data-factory"></a>Skapa en datafabrik
I det här steget skapar du en datafabrik och startar sedan användargränssnittet för Data Factory för att skapa en pipeline i datafabriken.

1. Öppna Microsoft Edge eller Google Chrome. För närvarande stöder endast Microsoft Edge-och Google Chrome-webbläsare Data Factory gränssnittet.

1. På den vänstra menyn väljer du **skapa en resurs**  >  **analys**  >  **Data Factory**.

1. I fönstret **Ny datafabrik**, under **Namn** anger du **ADFTutorialDataFactory**.

   Namnet på Azure Data Factory måste vara *globalt unikt*. Om du får ett fel meddelande om name-värdet anger du ett annat namn på data fabriken (till exempel Dittnamnadftutorialdatafactory). Se artikeln [Namnregler för Data Factory](./naming-rules.md) för namnregler för Data Factory-artefakter.

1. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i.

1. Gör något av följande för **Resursgrupp**:

    - Välj **Använd befintlig** och välj en befintlig resurs grupp i den nedrullningsbara listan.
    - Välj **Skapa ny** och ange namnet på en resurs grupp. 
     
    Mer information om resursgrupper finns i [Använda resursgrupper för att hantera Azure-resurser](../azure-resource-manager/management/overview.md). 

1. Under **Version** väljer du **V2**.

1. Under **Plats** väljer du en plats för datafabriken. Endast platser som stöds visas i den nedrullningsbara listan. Datalagren (t.ex. Azure Storage och SQL-databas) och beräkningarna (t.ex. Azure HDInsight) som används i datafabriken kan finnas i andra regioner.

1. Välj **Skapa**.

1. När du har skapat meddelandet visas meddelandet i meddelande centret. Välj **gå till resurs** för att gå till sidan **Data Factory** .

1. Klicka på **Författare och övervakare** för att starta användargränssnittet för datafabriken på en separat flik.

## <a name="create-an-azure-integration-runtime-in-data-factory-managed-virtual-network"></a>Skapa en Azure integration runtime i Data Factory hanterade Virtual Network
I det här steget skapar du en Azure integration Runtime och aktiverar Data Factory hanterade Virtual Network.

1. I Data Factory portal går du till **Hantera** och väljer **ny** för att skapa en ny Azure integration Runtime.

   ![Skärm bild som visar hur du skapar en ny Azure integration Runtime.](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
1. På sidan **installation av integration runtime** väljer du vilken integrerings körning som ska skapas baserat på de funktioner som krävs. I den här självstudien väljer du **Azure, egen värd** och klickar sedan på **Fortsätt**. 
1. Välj **Azure** och klicka sedan på **Fortsätt** för att skapa en Azure integration Runtime.

   ![Skärm bild som visar en ny Azure integration Runtime.](./media/tutorial-copy-data-portal-private/azure-ir.png)
1. Under **konfiguration av virtuellt nätverk (för hands version)** väljer du **Aktivera**.

   ![Skärm bild som visar aktivering av en ny Azure integration Runtime.](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)
1. Välj **Skapa**.

## <a name="create-a-pipeline"></a>Skapa en pipeline
I det här steget skapar du en pipeline med en kopieringsaktivitet i datafabriken. Kopieringsaktiviteten kopierar data från Blob Storage till en SQL-databas. I [snabbstartssjälvstudien](./quickstart-create-data-factory-portal.md) skapade du en pipeline med följande steg:

1. Skapa den länkade tjänsten.
1. Skapa datauppsättningar för indata och utdata.
1. Skapa en pipeline.

I den här självstudien börjar du med att skapa en pipeline. Sedan skapar du länkade tjänster och datauppsättningar när du behöver dem för att konfigurera pipelinen.

1. På sidan **Nu sätter vi igång** väljer du **Skapa pipeline**.

   ![Skärm bild som visar hur du skapar en pipeline.](./media/doc-common-process/get-started-page.png)
1. I rutan Egenskaper för pipelinen anger du **CopyPipeline** som namn på pipelinen.

1. I rutan **aktiviteter** , expanderar du kategorin **flytta och transformera** och drar aktiviteten **Kopiera data** från verktygs lådan till pipelinens design yta. Ange **CopyFromBlobToSql** som namn.

    ![Skärm bild som visar kopierings aktiviteten.](./media/tutorial-copy-data-portal-private/drag-drop-copy-activity.png)

### <a name="configure-a-source"></a>Konfigurera en källa

>[!TIP]
>I den här självstudien använder du **konto nyckel** som autentiseringstyp för ditt käll data lager. Du kan också välja andra autentiseringsmetoder som stöds, till exempel **SAS-URI**,**tjänstens huvud namn** och **hanterad identitet** om det behövs. Mer information finns i motsvarande avsnitt i [Kopiera och transformera data i Azure Blob Storage med hjälp av Azure Data Factory](./connector-azure-blob-storage.md#linked-service-properties).
>
>För att lagra hemligheter för data lager på ett säkert sätt rekommenderar vi också att du använder Azure Key Vault. Mer information och illustrationer finns [i lagra autentiseringsuppgifter i Azure Key Vault](./store-credentials-in-key-vault.md).

#### <a name="create-a-source-dataset-and-linked-service"></a>Skapa en käll data uppsättning och länkad tjänst

1. Gå till fliken **källa** . Välj **+ ny** för att skapa en käll data uppsättning.

1. I dialog rutan **ny data uppsättning** väljer du **Azure Blob Storage** och väljer sedan **Fortsätt**. Dina källdata finns i Blob Storage, så du väljer **Azure Blob Storage** för källdatauppsättningen.

1. I dialog rutan **Välj format** väljer du format typ för dina data och väljer sedan **Fortsätt**.

1. I dialog rutan **Ange egenskaper** anger du **SourceBlobDataset** som **namn**. Markera kryss rutan för **första raden som rubrik**. Under text rutan **länkad tjänst** väljer du **+ ny**.

1. I dialog rutan **ny länkad tjänst (Azure Blob Storage)** anger du **AzureStorageLinkedService** som **namn** och väljer ditt lagrings konto i listan **lagrings konto namn** . 

1. Se till att aktivera **interaktiv redigering**. Det kan ta ungefär en minut att aktive ras.

    ![Skärm bild som visar interaktiv redigering.](./media/tutorial-copy-data-portal-private/interactive-authoring.png)

1. Välj **Testanslutning**. Den bör inte fungera när lagrings kontot bara tillåter åtkomst från **valda nätverk** och kräver Data Factory för att skapa en privat slut punkt till det som ska godkännas innan det används. I fel meddelandet bör du se en länk för att skapa en privat slut punkt som du kan följa för att skapa en hanterad privat slut punkt. Ett alternativ är att gå direkt till fliken **Hantera** och följa anvisningarna i [Nästa avsnitt](#create-a-managed-private-endpoint) för att skapa en hanterad privat slut punkt.

   > [!NOTE]
   > Fliken **Hantera** kanske inte är tillgänglig för alla data Factory-instanser. Om du inte ser det kan du komma åt privata slut punkter genom att välja **författar**  >  **anslutningar**  >  **privat slut punkt**.
1. Håll dialog rutan öppen och gå sedan till ditt lagrings konto.

1. Följ anvisningarna i [det här avsnittet](#approval-of-a-private-link-in-a-storage-account) om du vill godkänna den privata länken.

1. Gå tillbaka till dialog rutan. Välj **Testa anslutning** igen och välj **skapa** för att distribuera den länkade tjänsten.

1. När den länkade tjänsten har skapats går den tillbaka till sidan **Ange egenskaper** . Vid **Filsökväg** väljer du **Bläddra**.

1. Gå till mappen **adftutorial/indata** , Välj **emp.txt** -filen och välj sedan **OK**.

1. Välj **OK**. Den går automatiskt till sidan pipelines. På fliken **källa** bekräftar du att **SourceBlobDataset** har valts. Om du vill förhandsgranska data på den här sidan väljer du **Förhandsgranska data**.

    ![Skärm bild som visar käll data uppsättningen.](./media/tutorial-copy-data-portal-private/source-dataset-selected.png)

#### <a name="create-a-managed-private-endpoint"></a>Skapa en hanterad privat slut punkt

Om du inte har valt hyperlänken när du testade anslutningen följer du sökvägen. Nu måste du skapa en hanterad privat slut punkt som du ansluter till den länkade tjänst som du har skapat.

1. Gå till fliken **Hantera** .

   > [!NOTE]
   > Fliken **Hantera** kanske inte är tillgänglig för alla data Factory-instanser. Om du inte ser det kan du komma åt privata slut punkter genom att välja **författar**  >  **anslutningar**  >  **privat slut punkt**.

1. Gå till avsnittet **hanterade privata slut punkter** .

1. Välj **+ ny** under **hanterade privata slut punkter**.

    ![Skärm bild som visar knappen Ny hanterad privat slut punkt ny.](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. Välj panelen **Azure-Blob Storage** i listan och välj **Fortsätt**.

1. Ange namnet på det lagrings konto som du har skapat.

1. Välj **Skapa**.

1. Efter några sekunder bör du se att den privata länken som skapas kräver ett godkännande.

1. Välj den privata slut punkt som du skapade. Du kan se en hyperlänk som kommer att leda till att du godkänner den privata slut punkten på lagrings konto nivån.

    ![Skärm bild som visar fönstret hanterad privat slut punkt.](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-a-storage-account"></a>Godkännande av en privat länk i ett lagrings konto
1. I lagrings kontot går du till **anslutningar för privata slut punkter** under avsnittet **Inställningar** .

1. Markera kryss rutan för den privata slut punkten som du skapade och välj **Godkänn**.

    ![Skärm bild som visar knappen Godkänn för den privata slut punkten.](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

1. Lägg till en beskrivning och välj **Ja**.
1. Gå tillbaka till avsnittet **hanterade privata slut punkter** på fliken **Hantera** i Data Factory.
1. Efter ungefär en eller två minuter bör du se att godkännandet för din privata slut punkt visas i Data Factory användar gränssnittet.


### <a name="configure-a-sink"></a>Konfigurera en mottagare
>[!TIP]
>I den här självstudien använder du **SQL-autentisering** som autentiseringstyp för ditt data lager för mottagare. Du kan också välja andra autentiseringsmetoder som stöds, till exempel **tjänstens huvud namn** och **hanterad identitet** om det behövs. Mer information finns i motsvarande avsnitt i [Kopiera och transformera data i Azure SQL Database med hjälp av Azure Data Factory](./connector-azure-sql-database.md#linked-service-properties).
>
>För att lagra hemligheter för data lager på ett säkert sätt rekommenderar vi också att du använder Azure Key Vault. Mer information och illustrationer finns [i lagra autentiseringsuppgifter i Azure Key Vault](./store-credentials-in-key-vault.md).

#### <a name="create-a-sink-dataset-and-linked-service"></a>Skapa en data uppsättning för mottagare och länkad tjänst
1. Gå till fliken **Mottagare** och välj **+ Nytt** för att skapa en datauppsättning för mottagare.

1. I dialog rutan **ny data uppsättning** anger du **SQL** i sökrutan för att filtrera kopplingarna. Välj **Azure SQL Database** och välj sedan **Fortsätt**. I dessa självstudier kopierar du data till en SQL-databas.

1. I dialog rutan **Ange egenskaper** anger du **OutputSqlDataset** som **namn**. I list rutan **länkad tjänst** väljer du **+ ny**. En datauppsättning måste associeras med en länkad tjänst. De länkade tjänsterna har anslutningssträngen som datafabriken använder för att ansluta till SQL-databasen vid körning. Datauppsättningen anger den container, mapp och fil (valfritt) som data kopieras till.

1. Utför följande steg i dialog rutan **ny länkad tjänst (Azure SQL Database)** :

    1. Under **Namn** anger du **AzureSqlDatabaseLinkedService**.
    1. Under **Servernamn** väljer du din SQL Server-instans.
    1. Se till att aktivera **interaktiv redigering**.
    1. Under **Databasnamn** väljer du din SQL-databas.
    1. Under **Användarnamn** anger du namnet på användaren.
    1. För **Lösenord** anger du användarens lösenord.
    1. Välj **Testanslutning**. Den bör inte utföras eftersom SQL Server endast tillåter åtkomst från **valda nätverk** och kräver Data Factory för att skapa en privat slut punkt till den, vilket bör godkännas innan den används. I fel meddelandet bör du se en länk för att skapa en privat slut punkt som du kan följa för att skapa en hanterad privat slut punkt. Ett alternativ är att gå direkt till fliken **Hantera** och följa anvisningarna i nästa avsnitt för att skapa en hanterad privat slut punkt.
    1. Håll dialog rutan öppen och gå sedan till den valda SQL-servern.
    1. Följ anvisningarna i [det här avsnittet](#approval-of-a-private-link-in-sql-server) om du vill godkänna den privata länken.
    1. Gå tillbaka till dialog rutan. Välj **Testa anslutning** igen och välj **skapa** för att distribuera den länkade tjänsten.

1. Den hamnar automatiskt i dialog rutan **Ange egenskaper** . Under **Tabell** väljer du **[dbo].[emp]**. Välj sedan **OK**.

1. Gå till fliken med pipelinen och i **mottagar data uppsättningen** bekräftar du att **OutputSqlDataset** har valts.

    ![Skärm bild som visar fliken pipelines.](./media/tutorial-copy-data-portal-private/pipeline-tab-2.png)

Du kan också mappa schemat för källan till motsvarande schema för målet genom att följa [schema mappningen i kopierings aktiviteten](./copy-activity-schema-and-type-mapping.md).

#### <a name="create-a-managed-private-endpoint"></a>Skapa en hanterad privat slut punkt

Om du inte har valt hyperlänken när du testade anslutningen följer du sökvägen. Nu måste du skapa en hanterad privat slut punkt som du ansluter till den länkade tjänst som du har skapat.

1. Gå till fliken **Hantera** .
1. Gå till avsnittet **hanterade privata slut punkter** .
1. Välj **+ ny** under **hanterade privata slut punkter**.

    ![Skärm bild som visar knappen Ny hanterad privat slut punkt ny.](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. Välj panelen **Azure SQL Database** i listan och välj **Fortsätt**.
1. Ange namnet på den SQL Server som du har valt.
1. Välj **Skapa**.
1. Efter några sekunder bör du se att den privata länken som skapas kräver ett godkännande.
1. Välj den privata slut punkt som du skapade. Du kan se en hyperlänk som kommer att leda till att du godkänner den privata slut punkten på SQL Server-nivån.


#### <a name="approval-of-a-private-link-in-sql-server"></a>Godkännande av en privat länk i SQL Server
1. I SQL Server går du till **anslutningar för privata slut punkter** under avsnittet **Inställningar** .
1. Markera kryss rutan för den privata slut punkten som du skapade och välj **Godkänn**.
1. Lägg till en beskrivning och välj **Ja**.
1. Gå tillbaka till avsnittet **hanterade privata slut punkter** på fliken **Hantera** i Data Factory.
1. Det bör ta en eller två minuter innan godkännandet visas för din privata slut punkt.

#### <a name="debug-and-publish-the-pipeline"></a>Felsöka och publicera en pipeline

Du kan felsöka en pipeline innan du publicerar artefakter (länkade tjänster, datauppsättningar och pipelines) till datafabriken eller din egen Azure Repos Git-lagringsplats.

1. Välj **Felsöka** i verktygsfält för att felsöka pipelinen. Du ser status för pipelinekörningen på fliken **Utdata** längst ned i fönstret.
1. När pipelinen har körts kan du gå till det övre verktygsfältet och välja **publicera alla**. Den här åtgärden publicerar entiteter (data uppsättningar och pipeliner) som du skapade till Data Factory.
1. Vänta tills du ser meddelandet om att entiteterna **har publicerats**. Om du vill se meddelanden väljer du **Visa meddelanden** i det övre högra hörnet (klock knappen).


#### <a name="summary"></a>Sammanfattning
Pipelinen i det här exemplet kopierar data från Blob Storage till SQL Database med hjälp av privata slut punkter i Data Factory hanterade Virtual Network. Du har lärt dig att:

* Skapa en datafabrik.
* Skapa en pipeline med en kopieringsaktivitet.