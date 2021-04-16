---
title: Använda privata slutpunkter för att skapa en Azure Data Factory pipeline
description: Den här självstudien innehåller stegvisa instruktioner för att skapa en datafabrik med en pipeline i Azure-portalen. Pipelinen använder kopieringsaktiviteten för att kopiera data från Azure Blob Storage till en Azure SQL databas.
author: linda33wj
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 04/14/2021
ms.author: jingwang
ms.openlocfilehash: 191ad61990e10fdb718eebf1a8f57d8edaadcf35
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515465"
---
# <a name="copy-data-securely-from-azure-blob-storage-to-a-sql-database-by-using-private-endpoints"></a>Kopiera data på ett säkert sätt från Azure Blob Storage till en SQL-databas med hjälp av privata slutpunkter

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här självstudiekursen skapar du en datafabrik med Azure Data Factory-användargränssnittet. *Pipelinen i den här datafabriken kopierar data säkert från Azure Blob Storage till en Azure SQL-databas (båda tillåter åtkomst till endast valda nätverk) med hjälp av privata slutpunkter [i Azure Data Factory Managed Virtual Network](managed-virtual-network-private-endpoint.md).* Konfigurationsmönstret i den här självstudien gäller kopiering av ett filbaserat datalager till ett relationsdatalager. En lista över datalager som stöds som källor och mottagare finns i tabellen [Datalager och](./copy-activity-overview.md) format som stöds.

> [!NOTE]
> Om du inte har använt datafabriken tidigare kan du läsa [Introduktion till Azure Data Factory](./introduction.md).

I den här självstudien gör du följande:

* Skapa en datafabrik.
* Skapa en pipeline med en kopieringsaktivitet.


## <a name="prerequisites"></a>Förutsättningar
* **Azure-prenumeration**. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* **Azure Storage-konto**. Du kan använda Blob Storage som *källa* för datalagringen. Om du inte har ett lagringskonto finns det anvisningar om hur du skapar ett i [Skapa ett Azure Storage-konto](../storage/common/storage-account-create.md?tabs=azure-portal). *Se till att lagringskontot endast tillåter åtkomst från valda nätverk.* 
* **Azure SQL Database**. Du använder databasen som *mottagare* för datalagringen. Om du inte har en Azure SQL finns anvisningar [för hur](../azure-sql/database/single-database-create-quickstart.md) du skapar en i Skapa en SQL-databas. *Kontrollera att SQL Database-kontot endast tillåter åtkomst från valda nätverk.* 

### <a name="create-a-blob-and-a-sql-table"></a>Skapa en blob och en SQL-tabell

Förbered nu bloblagringen och SQL-databasen för självstudien genom att utföra följande steg.

#### <a name="create-a-source-blob"></a>Skapa en källblob

1. Öppna Anteckningar. Kopiera följande text och spara den som en **emp.txt**-fil på din disk:

    ```
    FirstName,LastName
    John,Doe
    Jane,Doe
    ```

1. Skapa en container med **namnet adftutorial** i bloblagringen. Skapa en mapp som heter **input** i den här containern. Ladda sedan upp filen **emp.txt** till mappen **input**. Använd Azure-portalen eller verktyg som [Azure Storage Explorer](https://storageexplorer.com/) när du gör uppgifterna.

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

1. Öppna Microsoft Edge eller Google Chrome. För närvarande stöder Microsoft Edge webbläsare och Google Chrome Data Factory användargränssnitt.

1. På den vänstra menyn väljer du **Skapa en resurs**  >  **Analytics**  >  **Data Factory**.

1. I fönstret **Ny datafabrik**, under **Namn** anger du **ADFTutorialDataFactory**.

   Namnet på Azure-datafabriken måste *vara globalt unikt.* Om du får ett felmeddelande om namnvärdet anger du ett annat namn för datafabriken (till exempel dittnamnADFTutorialDataFactory). Se artikeln [Namnregler för Data Factory](./naming-rules.md) för namnregler för Data Factory-artefakter.

1. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i.

1. Gör något av följande för **Resursgrupp**:

    - Välj **Använd befintlig** och välj en befintlig resursgrupp i listrutan.
    - Välj **Skapa ny** och ange namnet på en resursgrupp. 
     
    Mer information om resursgrupper finns i [Använda resursgrupper för att hantera Azure-resurser](../azure-resource-manager/management/overview.md). 

1. Under **Version** väljer du **V2**.

1. Under **Plats** väljer du en plats för datafabriken. Endast platser som stöds visas i listrutan. Datalagren (t.ex. Azure Storage och SQL-databas) och beräkningarna (t.ex. Azure HDInsight) som används i datafabriken kan finnas i andra regioner.

1. Välj **Skapa**.

1. När skapandet är klart visas meddelandet i meddelandecentret. Välj **Gå till resurs** för att gå **Data Factory** sidan.

1. Klicka på **Författare och övervakare** för att starta användargränssnittet för datafabriken på en separat flik.

## <a name="create-an-azure-integration-runtime-in-data-factory-managed-virtual-network"></a>Skapa en Azure Integration Runtime i Data Factory Managed Virtual Network
I det här steget skapar du en Azure Integration Runtime och aktiverar Data Factory Managed Virtual Network.

1. I Data Factory portal går du till Hantera **och väljer** **Ny** för att skapa en ny Azure Integration Runtime.

   ![Skärmbild som visar hur du skapar en ny Azure Integration Runtime.](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
1. På **installationssidan för Integration Runtime** väljer du vilken Integration Runtime som ska skapas baserat på nödvändiga funktioner. I den här självstudien **väljer du Azure, Egen värd och** klickar sedan på **Fortsätt.** 
1. Välj **Azure** och klicka sedan på **Fortsätt för** att skapa en Azure Integration Runtime.

   ![Skärmbild som visar en ny Azure Integration Runtime.](./media/tutorial-copy-data-portal-private/azure-ir.png)
1. Under **Konfiguration av virtuellt nätverk (förhandsversion)** väljer du **Aktivera**.

   ![Skärmbild som visar aktivering av en ny Azure Integration Runtime.](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)
1. Välj **Skapa**.

## <a name="create-a-pipeline"></a>Skapa en pipeline
I det här steget skapar du en pipeline med en kopieringsaktivitet i datafabriken. Kopieringsaktiviteten kopierar data från Blob Storage till en SQL-databas. I [snabbstartssjälvstudien](./quickstart-create-data-factory-portal.md) skapade du en pipeline med följande steg:

1. Skapa den länkade tjänsten.
1. Skapa datauppsättningar för indata och utdata.
1. Skapa en pipeline.

I den här självstudien börjar du med att skapa en pipeline. Sedan skapar du länkade tjänster och datauppsättningar när du behöver dem för att konfigurera pipelinen.

1. På sidan **Nu sätter vi igång** väljer du **Skapa pipeline**.

   ![Skärmbild som visar hur du skapar en pipeline.](./media/doc-common-process/get-started-page.png)
1. I egenskapsfönstret för pipelinen anger du **CopyPipeline** som pipelinenamn.

1. I **verktygslådan** Aktiviteter  expanderar du kategorin Flytta och transformera och drar aktiviteten **Kopiera data** från verktygslådan till pipelinedesignerytan. Ange **CopyFromBlobToSql** som namn.

    ![Skärmbild som visar kopieringsaktiviteten.](./media/tutorial-copy-data-portal-private/drag-drop-copy-activity.png)

### <a name="configure-a-source"></a>Konfigurera en källa

>[!TIP]
>I den här självstudien **använder du kontonyckeln** som autentiseringstyp för ditt källdatalager. Du kan också välja andra autentiseringsmetoder som stöds, till exempel **SAS-URI,****tjänstens huvudnamn** och **hanterad identitet** om det behövs. Mer information finns i motsvarande avsnitt i Kopiera [och transformera data i Azure Blob Storage med hjälp av Azure Data Factory](./connector-azure-blob-storage.md#linked-service-properties).
>
>Om du vill lagra hemligheter för datalager på ett säkert sätt rekommenderar vi också att du använder Azure Key Vault. Mer information och bilder finns i Lagra [autentiseringsuppgifter i Azure Key Vault](./store-credentials-in-key-vault.md).

#### <a name="create-a-source-dataset-and-linked-service"></a>Skapa en källdatauppsättning och en länkad tjänst

1. Gå till **fliken** Källa. Välj **+ Ny för** att skapa en källdatauppsättning.

1. I dialogrutan **Ny datauppsättning** väljer du **Azure Blob Storage** och väljer sedan **Fortsätt.** Dina källdata finns i Blob Storage, så du väljer **Azure Blob Storage** för källdatauppsättningen.

1. I **dialogrutan Välj format** väljer du formattypen för dina data och väljer sedan **Fortsätt.**

1. I dialogrutan **Ange egenskaper** anger du **SourceBlobDataset** som **namn.** Markera kryssrutan för Första **raden som rubrik**. Under **textrutan Länkad** tjänst väljer du **+ Ny.**

1. I dialogrutan **New linked service (Azure Blob Storage) (Ny** länkad tjänst (Azure Blob Storage) anger du **AzureStorageLinkedService** som **Namn** och väljer ditt lagringskonto i **listan Lagringskontonamn.** 

1. Kontrollera att du aktiverar **interaktiv redigering.** Det kan ta ungefär en minut att aktiveras.

    ![Skärmbild som visar interaktiv redigering.](./media/tutorial-copy-data-portal-private/interactive-authoring.png)

1. Välj **Testanslutning**. Det bör misslyckas när lagringskontot endast tillåter åtkomst från **valda** nätverk och kräver Data Factory att skapa en privat slutpunkt till det som ska godkännas innan det används. I felmeddelandet bör du se en länk för att skapa en privat slutpunkt som du kan följa för att skapa en hanterad privat slutpunkt. Ett alternativ är att gå direkt till fliken **Hantera** och följa instruktionerna i nästa [avsnitt för att](#create-a-managed-private-endpoint) skapa en hanterad privat slutpunkt.

   > [!NOTE]
   > Fliken **Hantera** kanske inte är tillgänglig för alla datafabriksinstanser. Om du inte ser det kan du komma åt privata slutpunkter genom att välja **Author**  >  **Connections Private**  >  Endpoint (Skapa anslutningar privat **slutpunkt).**
1. Låt dialogrutan vara öppen och gå sedan till ditt lagringskonto.

1. Följ anvisningarna i [det här avsnittet](#approval-of-a-private-link-in-a-storage-account) för att godkänna den privata länken.

1. Gå tillbaka till dialogrutan. Välj **Testa anslutning** igen och välj Skapa för **att** distribuera den länkade tjänsten.

1. När den länkade tjänsten har skapats går den tillbaka till sidan **Ange** egenskaper. Vid **Filsökväg** väljer du **Bläddra**.

1. Gå till mappen **adftutorial/input,** välj **emp.txt** fil och välj sedan **OK.**

1. Välj **OK**. Den går automatiskt till pipelinesidan. På fliken **Källa** bekräftar du att **SourceBlobDataset** har valts. Om du vill förhandsgranska data på den här sidan väljer du **Förhandsgranska data**.

    ![Skärmbild som visar källdatauppsättningen.](./media/tutorial-copy-data-portal-private/source-dataset-selected.png)

#### <a name="create-a-managed-private-endpoint"></a>Skapa en hanterad privat slutpunkt

Om du inte har valt hyperlänken när du testade anslutningen följer du sökvägen. Nu måste du skapa en hanterad privat slutpunkt som du ska ansluta till den länkade tjänst som du skapade.

1. Gå till **fliken** Hantera.

   > [!NOTE]
   > Fliken **Hantera** kanske inte är tillgänglig för alla Data Factory instanser. Om du inte ser det kan du komma åt privata slutpunkter genom att välja **Author**  >  **Connections Private**  >  Endpoint (Skapa anslutningar privat **slutpunkt).**

1. Gå till avsnittet **Hanterade privata slutpunkter.**

1. Välj **+ Ny** under **Hanterade privata slutpunkter**.

    ![Skärmbild som visar knappen Hanterade privata slutpunkter Ny.](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. Välj **Azure Blob Storage** panelen i listan och välj **Fortsätt.**

1. Ange namnet på det lagringskonto som du skapade.

1. Välj **Skapa**.

1. Efter några sekunder bör du se att den privata länk som skapas behöver ett godkännande.

1. Välj den privata slutpunkt som du skapade. Du kan se en hyperlänk som leder till att du godkänner den privata slutpunkten på lagringskontonivå.

    ![Skärmbild som visar fönstret Hanterad privat slutpunkt.](./media/tutorial-copy-data-portal-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-a-storage-account"></a>Godkännande av en privat länk i ett lagringskonto
1. I lagringskontot går du till **Privata slutpunktsanslutningar** under **avsnittet** Inställningar.

1. Markera kryssrutan för den privata slutpunkt som du skapade och välj **Godkänn.**

    ![Skärmbild som visar knappen Godkänn för den privata slutpunkten.](./media/tutorial-copy-data-portal-private/approve-private-endpoint.png)

1. Lägg till en beskrivning och välj **ja.**
1. Gå tillbaka till avsnittet **Hanterade privata slutpunkter** på **fliken** Hantera i Data Factory.
1. Efter ungefär en eller två minuter bör du se godkännandet av din privata slutpunkt i Data Factory användargränssnitt.


### <a name="configure-a-sink"></a>Konfigurera en mottagare
>[!TIP]
>I den här självstudien använder **du SQL-autentisering** som autentiseringstyp för ditt datalager för mottagare. Du kan också välja andra autentiseringsmetoder som stöds, till exempel **tjänstens huvudnamn och** **hanterad identitet** om det behövs. Mer information finns i motsvarande avsnitt i Kopiera [och transformera data i Azure SQL Database med hjälp av Azure Data Factory](./connector-azure-sql-database.md#linked-service-properties).
>
>Om du vill lagra hemligheter för datalager på ett säkert sätt rekommenderar vi också att du använder Azure Key Vault. Mer information och bilder finns i Lagra [autentiseringsuppgifter i Azure Key Vault](./store-credentials-in-key-vault.md).

#### <a name="create-a-sink-dataset-and-linked-service"></a>Skapa en datauppsättning för mottagare och en länkad tjänst
1. Gå till fliken **Mottagare** och välj **+ Nytt** för att skapa en datauppsättning för mottagare.

1. I dialogrutan **Ny datauppsättning** anger du **SQL** i sökrutan för att filtrera anslutningsapparna. Välj **Azure SQL Database** och välj sedan **Fortsätt.** I dessa självstudier kopierar du data till en SQL-databas.

1. I dialogrutan **Ange egenskaper** anger du **OutputSqlDataset** som **Namn.** I **listrutan Länkad** tjänst väljer du **+ Ny.** En datauppsättning måste associeras med en länkad tjänst. De länkade tjänsterna har anslutningssträngen som datafabriken använder för att ansluta till SQL-databasen vid körning. Datauppsättningen anger den container, mapp och fil (valfritt) som data kopieras till.

1. I **dialogrutan New linked service (Azure SQL Database) (Ny länkad tjänst (Azure SQL Database)** gör du följande:

    1. Under **Namn** anger du **AzureSqlDatabaseLinkedService**.
    1. Under **Servernamn** väljer du din SQL Server-instans.
    1. Kontrollera att du aktiverar **interaktiv redigering.**
    1. Under **Databasnamn** väljer du din SQL-databas.
    1. Under **Användarnamn** anger du namnet på användaren.
    1. För **Lösenord** anger du användarens lösenord.
    1. Välj **Testanslutning**. Det bör misslyckas eftersom SQL-servern endast tillåter åtkomst från **valda** nätverk och kräver Data Factory att skapa en privat slutpunkt till den, som ska godkännas innan den används. I felmeddelandet bör du se en länk för att skapa en privat slutpunkt som du kan följa för att skapa en hanterad privat slutpunkt. Ett alternativ är att gå direkt till fliken **Hantera** och följa instruktionerna i nästa avsnitt för att skapa en hanterad privat slutpunkt.
    1. Låt dialogrutan vara öppen och gå sedan till den valda SQL-servern.
    1. Följ anvisningarna i [det här avsnittet](#approval-of-a-private-link-in-sql-server) för att godkänna den privata länken.
    1. Gå tillbaka till dialogrutan. Välj **Testa anslutning** igen och välj Skapa för **att** distribuera den länkade tjänsten.

1. Den går automatiskt till **dialogrutan Ange** egenskaper. Under **Tabell** väljer du **[dbo].[emp]**. Välj sedan **OK**.

1. Gå till fliken med pipelinen och i Sink dataset (Datauppsättning **för mottagare)** bekräftar du **att OutputSqlDataset** har valts.

    ![Skärmbild som visar fliken Pipeline.](./media/tutorial-copy-data-portal-private/pipeline-tab-2.png)

Om du vill kan du mappa schemat för källan till motsvarande schema för målet genom att följa [Schemamappning i kopieringsaktiviteten](./copy-activity-schema-and-type-mapping.md).

#### <a name="create-a-managed-private-endpoint"></a>Skapa en hanterad privat slutpunkt

Om du inte väljer hyperlänken när du testade anslutningen följer du sökvägen. Nu måste du skapa en hanterad privat slutpunkt som du ska ansluta till den länkade tjänst som du skapade.

1. Gå till **fliken** Hantera.
1. Gå till **avsnittet Hanterade privata slutpunkter.**
1. Välj **+ Ny** under **Hanterade privata slutpunkter.**

    ![Skärmbild som visar knappen Hanterade privata slutpunkter Ny.](./media/tutorial-copy-data-portal-private/new-managed-private-endpoint.png) 

1. Välj **Azure SQL Database** panelen i listan och välj **Fortsätt.**
1. Ange namnet på den SQL-server som du har valt.
1. Välj **Skapa**.
1. Efter några sekunder bör du se att den privata länk som skapas behöver ett godkännande.
1. Välj den privata slutpunkt som du skapade. Du kan se en hyperlänk som leder till att du godkänner den privata slutpunkten på SQL-servernivå.


#### <a name="approval-of-a-private-link-in-sql-server"></a>Godkännande av en privat länk i SQL Server
1. I SQL-servern går du till **Privata slutpunktsanslutningar** under **avsnittet** Inställningar.
1. Markera kryssrutan för den privata slutpunkt som du skapade och välj **Godkänn.**
1. Lägg till en beskrivning och välj **ja.**
1. Gå tillbaka till **avsnittet Hanterade privata slutpunkter** på **fliken** Hantera i Data Factory.
1. Det bör ta en eller två minuter innan godkännandet visas för din privata slutpunkt.

#### <a name="debug-and-publish-the-pipeline"></a>Felsöka och publicera en pipeline

Du kan felsöka en pipeline innan du publicerar artefakter (länkade tjänster, datauppsättningar och pipelines) till datafabriken eller din egen Azure Repos Git-lagringsplats.

1. Välj **Felsöka** i verktygsfält för att felsöka pipelinen. Du ser status för pipelinekörningen på fliken **Utdata** längst ned i fönstret.
1. När pipelinen har körts väljer du Publicera alla i det **översta verktygsfältet.** Den här åtgärden publicerar entiteter (datauppsättningar och pipeliner) som du har skapat för att Data Factory.
1. Vänta tills du ser meddelandet om att entiteterna **har publicerats**. Om du vill se meddelanden väljer **du Visa** meddelanden i det övre högra hörnet (klockknappen).


#### <a name="summary"></a>Sammanfattning
Pipelinen i det här exemplet kopierar data från Blob Storage till SQL Database med hjälp av privata slutpunkter i Data Factory Managed Virtual Network. Du har lärt dig att:

* Skapa en datafabrik.
* Skapa en pipeline med en kopieringsaktivitet.