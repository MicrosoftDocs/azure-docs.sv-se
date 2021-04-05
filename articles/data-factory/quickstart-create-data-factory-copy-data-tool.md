---
title: Kopiera data med hjälp av Azure Kopiera data-verktyget
description: Skapa en Azure Data Factory och Använd sedan Kopiera data-verktyget för att kopiera data från en plats i Azure Blob Storage till en annan plats.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: quickstart
ms.date: 11/09/2020
ms.openlocfilehash: 78bc815aff195124d3e79a83445b8d12506fc70d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104613098"
---
# <a name="quickstart-use-the-copy-data-tool-to-copy-data"></a>Snabb start: Använd Kopiera data-verktyget för att kopiera data

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuell version](quickstart-create-data-factory-copy-data-tool.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

I den här snabbstarten använder du Azure-portalen för att skapa en datafabrik. Sedan använder du verktyget Kopiera data för att skapa en pipeline som kopierar data från en mapp i en Azure Blob Storage till en annan mapp. 

> [!NOTE]
> Om du inte har använt Azure Data Factory tidigare kan du läsa [Introduktion till Azure Data Factory](introduction.md). 

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Starta webbläsaren **Microsoft Edge** eller **Google Chrome**. Användargränssnittet för Data Factory stöds för närvarande bara i webbläsarna Microsoft Edge och Google Chrome.
1. Gå till [Azure-portalen](https://portal.azure.com). 
1. Från Azure Portal-menyn väljer du **skapa en resurs**  >  **integration**  >  **Data Factory**:

    ![Skapa ny datafabrik](./media/doc-common-process/new-azure-data-factory-menu.png)

1. På sidan **Ny datafabrik** anger du **ADFTutorialDataFactory** som **Namn**. 
 
   Namnet på Azure Data Factory måste vara *globalt unikt*. Om du ser följande fel ändrar du namnet på data fabriken (till exempel **&lt; dittnamn &gt; ADFTutorialDataFactory**) och försöker skapa igen. Namngivningsregler för Data Factory-artefakter finns i artikeln [Data Factory – namnregler](naming-rules.md).
  
   ![Fel när ett namn inte är tillgängligt](./media/doc-common-process/name-not-available-error.png)
1. Välj den Azure-prenumeration där du vill skapa den nya datafabriken för **Prenumeration**. 
1. För **Resursgrupp** utför du något av följande steg:
     
   - Välj **Använd befintlig** och välj en befintlig resursgrupp från listan. 
   - Välj **Skapa ny** och ange namnet på en resurs grupp.   
         
   Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/management/overview.md).  
1. För **Version** väljer du **V2**.
1. För **Plats** väljer du en plats för datafabriken.

   I listan visas endast platser som Data Factory har stöd för och var dina Azure Data Factory-metadata kommer att lagras. De associerade data lag ren (t. ex. Azure Storage och Azure SQL Database) och beräkningarna (som Azure HDInsight) som Data Factory använder kan köras i andra regioner.

1. Välj **Skapa**.

1. När datafabriken har skapats visas sidan **Datafabrik**. Välj panelen **Författare och övervakare** för att starta användargränssnittet för Azure Data Factory på en separat flik.
   
:::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Start sidan för Azure Data Factory, med panel för författar & övervakning.":::

## <a name="start-the-copy-data-tool"></a>Starta verktyget Kopiera data

1. Sidan **Nu sätter vi igång** visas. Välj panelen **Kopiera data** för att starta verktyget kopieringsdata. 

   ![Panelen Kopiera data](./media/doc-common-process/get-started-page.png)

1. På sidan **Egenskaper** sida i verktyget Kopiera data kan du ange ett namn för din pipeline och dess beskrivning och sedan välja **Nästa**. 

   ![Sidan Egenskaper](./media/quickstart-create-data-factory-copy-data-tool/copy-data-tool-properties-page.png)
1. Gör följande på sidan **Källdatalager**:

    a. Klicka på **+ Skapa ny anslutning** för att lägga till en anslutning.

    b. Välj den länkade tjänst typ som du vill skapa för käll anslutningen. I den här självstudien använder vi **Azure Blob Storage**. Välj den från galleriet och välj sedan **Fortsätt**.
    
    ![Välj BLOB](./media/quickstart-create-data-factory-copy-data-tool/select-blob-source.png)

    c. På sidan **ny länkad tjänst (Azure Blob Storage)** anger du ett namn för den länkade tjänsten. Välj ditt lagrings konto i listan **lagrings konto namn** , testa anslutning och välj sedan **skapa**. 

    ![Konfigurera Azure Blob Storage-konto](./media/quickstart-create-data-factory-copy-data-tool/configure-blob-storage.png)

    d. Välj den nyligen skapade länkade tjänsten som källa och klicka sedan på **Nästa**.


1. Gör följande på sidan för att **välja indatafil eller -mapp**:

   a. Klicka på **Bläddra** för att navigera till mappen **adftutorial/indata** , Välj **emp.txt** -filen och klicka sedan på **Välj**. 

   d. Markera kryss rutan **binär kopia** om du vill kopiera filen som den är och välj sedan **Nästa**. 

   ![Sidan för att välja indatafil eller -mapp](./media/quickstart-create-data-factory-copy-data-tool/select-binary-copy.png)


1. På sidan **mål data lager** väljer du den länkade tjänsten **Azure Blob Storage** som du skapade och väljer sedan **Nästa**. 

1. På sidan **Välj utdatafil eller mapp** anger du **adftutorial/output** som mappsökväg och väljer sedan **Nästa**. 

   ![Sidan för att välja utdatafil eller -mapp](./media/quickstart-create-data-factory-copy-data-tool/configure-sink-path.png) 

1. På sidan **Inställningar** väljer du **Nästa** för att använda standardkonfigurationerna. 

1. Granska alla inställningar på sidan **Summary** (Sammanfattning) och klicka på **Nästa**. 

1. Sidan om att **distributionen har slutförts** visas. Välj **Övervaka** om du vill övervaka den pipeline du har skapat. 

    ![Sidan om att distributionen har slutförts](./media/quickstart-create-data-factory-copy-data-tool/deployment-page.png)

1. Programmet växlar till fliken **övervakare** . Du ser status för pipelinen på den här fliken. Välj **Uppdatera** för att uppdatera listan. Klicka på länken under **pipeline-namn** om du vill visa aktivitets körnings information eller kör pipelinen igen. 
   
    ![Uppdatera pipeline](./media/quickstart-create-data-factory-copy-data-tool/refresh-pipeline.png)

1. På sidan aktivitets körningar väljer du länken **information** (glasögon ikonen) under kolumnen **aktivitets namn** för mer information om kopierings åtgärden. Mer information om egenskaperna finns i [Copy Activity overview](copy-activity-overview.md) (Översikt över kopieringsaktivitet). 

1. Om du vill gå tillbaka till vyn pipeline-körningar väljer du länken **alla pipeline-körningar** på menyn för dynamiska länkar. Välj **Uppdatera** för att uppdatera vyn. 

1. Verifiera att filen **emp.txt** har skapats i mappen **output** för containern **adftutorial**. Om mappen utdata inte finns skapas den automatiskt av Data Factory tjänsten. 

1. Växla till fliken **Fönster** över fliken **Övervakare** på den vänstra panelen så att du kan redigera länkade tjänster, datauppsättningar och piplines. Mer information om hur du redigerar dessa i användargränssnittet för Data Factory finns i [Skapa en datafabrik med hjälp av Azure-portalen](quickstart-create-data-factory-portal.md).

    ![Fliken Välj författare](./media/quickstart-create-data-factory-copy-data-tool/select-author.png)

## <a name="next-steps"></a>Nästa steg
Pipelinen i det här exemplet kopierar data från en plats till en annan i Azure Blob Storage. Gå igenom [självstudiekurserna](tutorial-copy-data-portal.md) om du vill lära dig hur du använder Data Factory i fler scenarier. 
