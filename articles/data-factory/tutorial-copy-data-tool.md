---
title: Kopiera data från Azure Blob Storage till SQL med Kopiera data-verktyget
description: Skapa en Azure Data Factory och Använd sedan Kopiera data-verktyget för att kopiera data från Azure Blob Storage till en SQL Database.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 20db863f1e2ae66acada928687b0bcd572f729f9
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103546"
---
# <a name="copy-data-from-azure-blob-storage-to-a-sql-database-by-using-the-copy-data-tool"></a>Kopiera data från Azure Blob Storage till en SQL Database med hjälp av Kopiera data-verktyget

> [!div class="op_single_selector" title1="Välj den version av tjänsten Data Factory som du använder:"]
> * [Version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuell version](tutorial-copy-data-tool.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här självstudien skapar du en datafabrik i Azure Portal. Sedan använder du verktyget Kopiera data för att skapa en pipeline som kopierar data från Azure Blob Storage till en SQL Database.

> [!NOTE]
> Om du inte har använt Azure Data Factory tidigare kan du läsa [Introduktion till Azure Data Factory](introduction.md).

I den här självstudien får du göra följande:
> [!div class="checklist"]
> * Skapa en datafabrik.
> * Använd verktyget Kopiera data för att skapa en pipeline.
> * Övervaka pipelinen och aktivitetskörningarna.

## <a name="prerequisites"></a>Förutsättningar

* **Azure-prenumeration**: Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* **Azure Storage konto**: Använd Blob Storage som _käll_ data lager. Om du inte har ett Azure Storage konto kan du läsa instruktionerna i [skapa ett lagrings konto](../storage/common/storage-account-create.md).
* **Azure SQL Database**: använd en SQL Database som data lager för _mottagare_ . Om du inte har en SQL Database kan du läsa anvisningarna i [skapa en SQL Database](../azure-sql/database/single-database-create-quickstart.md).

### <a name="create-a-blob-and-a-sql-table"></a>Skapa en blob och en SQL-tabell

Förbered din Blob Storage och dina SQL Database för självstudien genom att utföra dessa steg.

#### <a name="create-a-source-blob"></a>Skapa en källblob

1. Starta **anteckningar**. Kopiera följande text och spara den i en fil med namnet **inputEmp.txt** på din disk:

   ```text
   FirstName|LastName
   John|Doe
   Jane|Doe
   ```

1. Skapa en container med namnet **adfv2tutorial** och ladda upp filen inputEmp.txt till containern. Du kan använda Azure Portal eller olika verktyg som [Azure Storage Explorer](https://storageexplorer.com/) för att utföra dessa uppgifter.

#### <a name="create-a-sink-sql-table"></a>Skapa en SQL-mottagartabell

1. Använd följande SQL-skript för att skapa en tabell med namnet **dbo. EMP** i SQL Database:

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

2. Ge Azure-tjänster åtkomst till SQL Server. Kontrol lera att inställningen **Tillåt att Azure-tjänster och resurser får åtkomst till den här servern** är aktive rad för den server som kör SQL Database. Med den här inställningen kan Data Factory skriva data till din databasinstans. Om du vill kontrol lera och aktivera den här inställningen går du till logiska SQL Server > säkerhets > brand väggar och virtuella nätverk > anger alternativet **Tillåt att Azure-tjänster och-resurser får åtkomst till det här server** alternativet till **på**.

   > [!NOTE]
   > Alternativet att **tillåta att Azure-tjänster och resurser får åtkomst till den här servern** ger nätverks åtkomst till din SQL Server från en Azure-resurs, inte bara de i din prenumeration. Mer information finns i [regler för Azure SQL Server-brandvägg](../azure-sql/database/firewall-configure.md). I stället kan du använda [privata slut punkter](../private-link/private-endpoint-overview.md) för att ansluta till Azure PaaS Services utan att använda offentliga IP-adresser.

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. På den vänstra menyn väljer du **skapa en resurs**  >  **integrations**  >  **Data Factory**:

   ![Skapa ny datafabrik](./media/doc-common-process/new-azure-data-factory-menu.png)

1. I fönstret **Ny datafabrik**, under **Namn** anger du **ADFTutorialDataFactory**.

   Namnet på datafabriken måste vara _globalt unikt_. Du kan få följande felmeddelande:

   :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Fel meddelande för ny data fabrik för dubblettnamn.":::

   Ange ett annat namn för datafabriken om du får ett felmeddelande om namnvärdet. Använd till exempel namnet _**dittnamn**_**ADFTutorialDataFactory**. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.

1. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i.

1. Gör något av följande för **Resursgrupp**:

   a. Välj **Använd befintlig** och välj en befintlig resurs grupp i den nedrullningsbara listan.

   b. Välj **Skapa ny** och ange namnet på en resurs grupp.

   Mer information om resursgrupper finns i [Använda resursgrupper för att hantera Azure-resurser](../azure-resource-manager/management/overview.md).

1. För **version** väljer du **V2**.

1. Under **plats** väljer du en plats för datafabriken. Endast platser som stöds visas i listrutan. Datalagren (t.ex. Azure Storage och SQL Database) och beräkningarna (t.ex. Azure HDInsight) som används i datafabriken kan finnas på andra platser och i andra regioner.

1. Välj **Skapa**.

1. När den har skapats visas startsidan för **Data Factory**.

   :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Start sidan för Azure Data Factory, med panel för författar & övervakning.":::

1. Klicka på panelen **Författare och övervakare** för att starta användargränssnittet för Azure Data Factory i en separat flik.

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Använd verktyget Kopiera data för att skapa en pipeline

1. Sidan **Nu sätter vi igång** visas. Välj panelen **Kopiera data** för att starta verktyget Kopiera data.

   ![Panel för verktyget Kopiera data](./media/doc-common-process/get-started-page.png)

1. På sidan **Egenskaper** under **Aktivitetsnamn**, anger du **CopyFromBlobToSqlPipeline**. Välj sedan **Nästa**. Med användargränssnittet för Data Factory skapas en pipeline med angivet aktivitetsnamn.

   ![Skapa en pipeline](./media/tutorial-copy-data-tool/create-pipeline.png)

1. Gör följande på sidan **Källdatalager**:

   a. Välj **+ Skapa ny anslutning** för att lägga till en anslutning

   b. Välj **Azure Blob Storage** från galleriet och välj sedan **Fortsätt**.

   c. På sidan **ny länkad tjänst** väljer du din Azure-prenumeration och väljer ditt lagrings konto i listan **lagrings konto namn** . Testa anslutning och välj sedan **skapa**.

   d. Välj den nyligen skapade länkade tjänsten som källa och välj sedan **Nästa**.

   ![Välj källa för den länkade tjänsten](./media/tutorial-copy-data-tool/select-source-linked-service.png)

1. Gör följande på sidan för att **välja indatafil eller -mapp**:

   a. Välj **Bläddra** för att navigera till mappen **adfv2tutorial/indata** , Välj **inputEmp.txt** filen och välj sedan **Välj**.

   b. Välj **Nästa** för att gå vidare till nästa steg.

1. På sidan **fil formats inställningar** aktiverar du kryss rutan för *första raden som rubrik*. Observera att verktyget automatiskt identifierar kolumn-och rad avgränsare. Välj **Nästa**. Du kan också förhandsgranska data och Visa schemat för indata på den här sidan.

   ![Filformatinställningar](./media/tutorial-copy-data-tool/file-format-settings-page.png)

1. Gör följande på sidan **Måldatalager**:

   a. Välj **+ Skapa ny anslutning** för att lägga till en anslutning

   b. Välj **Azure SQL Database** från galleriet och välj sedan **Fortsätt**.

   c. På sidan **ny länkad tjänst** väljer du Server namnet och databas namnet i list rutan. Ange användar namn och lösen ord och välj sedan **skapa**.

      ![Konfigurera Azure SQL DB](./media/tutorial-copy-data-tool/config-azure-sql-db.png)

   d. Välj den nyligen skapade länkade tjänsten som mottagare och välj sedan **Nästa**.

1. På sidan **Tabellmappning** väljer du tabellen **[dbo].[emp]** och sedan **Nästa**.

1. På sidan **kolumn mappning** ser du att den andra och de tredje kolumnerna i indatafilen mappas till kolumnerna **FirstName** och **LastName** för tabellen **EMP** . Ändra mappningen för att kontrol lera att det inte finns något fel och välj sedan **Nästa**.

   ![Kolumn mappnings sida](./media/tutorial-copy-data-tool/column-mapping.png)

1. Sidan **Settings** (Inställningar) visas. Välj **Nästa**.

1. Granska inställningarna på sidan **Sammanfattning** och klicka på **Nästa**.

1. Välj **Övervaka** på sidan **Distribution** för att övervaka pipelinen (aktiviteten).

   ![Övervaka pipeline](./media/tutorial-copy-data-tool/monitor-pipeline.png)

1. På sidan pipeliner körs väljer du **Uppdatera** för att uppdatera listan. Välj länken under **pipeline-namn** för att Visa aktivitets körnings information eller kör pipelinen igen. 

   ![Pipeline-körning](./media/tutorial-copy-data-tool/pipeline-run.png)

1. På sidan aktivitets körningar väljer du länken **information** (glasögon ikonen) under kolumnen **aktivitets namn** för mer information om kopierings åtgärden. Om du vill gå tillbaka till vyn pipeline-körningar väljer du länken **alla pipeline-körningar** på menyn för dynamiska länkar. Välj **Uppdatera** för att uppdatera vyn.

   ![Övervaka aktivitetskörningar](./media/tutorial-copy-data-tool/activity-monitoring.png)

1. Kontrol lera att data infogas i tabellen **dbo. EMP** i din SQL Database.

1. Klicka på fliken **Författare** till vänster för att växla till redigeringsläget. Du kan uppdatera de länkade tjänster, datauppsättningar och pipeliner som skapats med verktyget med hjälp av redigeraren. Mer information om hur du redigerar dessa entiteter i användargränssnittet för Data Factory finns i [Azure Portal-versionen av den här självstudiekursen](tutorial-copy-data-portal.md).

   ![Fliken Välj författare](./media/tutorial-copy-data-tool/author-tab.png)

## <a name="next-steps"></a>Nästa steg
Pipelinen i det här exemplet kopierar data från Blob Storage till en SQL Database. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Använd verktyget Kopiera data för att skapa en pipeline.
> * Övervaka pipelinen och aktivitetskörningarna.

Fortsätt till nästa självstudie om du vill lära dig att kopiera data från en lokal plats till molnet:

>[!div class="nextstepaction"]
>[Kopiera data från en lokal plats till molnet](tutorial-hybrid-copy-data-tool.md)
