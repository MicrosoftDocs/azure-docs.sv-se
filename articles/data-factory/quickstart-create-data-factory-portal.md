---
title: Skapa en Azure-datafabrik med hjälp av Azure Data Factory gränssnittet
description: Skapa en datafabrik med en pipeline som en kopierar data från en plats till en annan i Azure Blob Storage.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: quickstart
ms.date: 12/14/2020
ms.author: jingwang
ms.openlocfilehash: 76a490ed28f940620db7835c4cec145740f48503
ms.sourcegitcommit: 63d0621404375d4ac64055f1df4177dfad3d6de6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2020
ms.locfileid: "97509027"
---
# <a name="quickstart-create-a-data-factory-by-using-the-azure-data-factory-ui"></a>Snabbstart: Skapa en datafabrik via gränssnittet i Azure Data Factory 

> [!div class="op_single_selector" title1="Välj den version av Data Factory-tjänsten som du använder:"]
> * [Version 1](v1/data-factory-copy-data-from-azure-blob-storage-to-sql-database.md)
> * [Aktuell version](quickstart-create-data-factory-portal.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Den här snabbstarten beskriver hur du använder Azure Data Factory-användargränssnittet till att skapa och övervaka en datafabrik. Den pipeline du skapar i den här datafabriken *kopierar* data från en mapp till en annan mapp i Azure Blob Storage. Information om hur du *omvandlar* data med hjälp av Azure Data Factory finns i [mappa data flöde](concepts-data-flow-overview.md) och [Datatransformering data flöde (för hands version)](wrangling-data-flow-overview.md).

> [!NOTE]
> Om du inte har använt Azure Data Factory tidigare kan du läsa [Introduktion till Azure Data Factory](introduction.md). 

[!INCLUDE [data-factory-quickstart-prerequisites](../../includes/data-factory-quickstart-prerequisites.md)] 

### <a name="video"></a>Video 
Om du tittar på den här videon får du hjälp med att förstå Data Factory-användargränssnittet: 
>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Visually-build-pipelines-for-Azure-Data-Factory-v2/Player]

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Starta webbläsaren **Microsoft Edge** eller **Google Chrome**. Användargränssnittet för Data Factory stöds för närvarande bara i webbläsarna Microsoft Edge och Google Chrome.
1. Öppna [Azure-portalen](https://portal.azure.com). 
1. I menyn i Azure-portalen väljer du **Skapa en resurs**.
1. Välj **integration** och välj sedan **Data Factory**. 
   
   ![Valet Data Factory i fönstret Nytt](./media/doc-common-process/new-azure-data-factory-menu.png)
1. På sidan **skapa Data Factory** under fliken **grundläggande** väljer du din Azure- **prenumeration** där du vill skapa data fabriken.
1. Gör något av följande för **Resursgrupp**:

    a. Välj en befintlig resurs grupp i den nedrullningsbara listan.

    b. Välj **Skapa ny** och ange namnet på en ny resurs grupp.
    
    Mer information om resursgrupper finns i [Använda resursgrupper för att hantera Azure-resurser](../azure-resource-manager/management/overview.md). 

1. För **region** väljer du platsen för data fabriken.

   I listan visas endast platser som Data Factory har stöd för och var dina Azure Data Factory-metadata kommer att lagras. De associerade data lag ren (t. ex. Azure Storage och Azure SQL Database) och beräkningarna (som Azure HDInsight) som Data Factory använder kan köras i andra regioner.
 
1. Som **namn** anger du **ADFTutorialDataFactory**.
   Namnet på Azure Data Factory måste vara *globalt unikt*. Om du ser följande fel ändrar du namnet på data fabriken (till exempel **&lt; dittnamn &gt; ADFTutorialDataFactory**) och försöker skapa igen. Namngivningsregler för Data Factory-artefakter finns i artikeln [Data Factory – namnregler](naming-rules.md).
  
   ![Fel när ett namn inte är tillgängligt](./media/doc-common-process/name-not-available-error.png)

1. För **Version** väljer du **V2**.

1. Välj **Nästa: git-konfiguration** och välj sedan kryss rutan **Konfigurera git senare** .

1. Välj **Granska + skapa** och välj **skapa** när verifieringen har slutförts. När du har skapat filen väljer du **gå till resurs** för att gå till sidan **Data Factory** . 

1. Välj panelen **författare & Monitor** för att starta programmet Azure Data Factory användar gränssnitt (UI) på en separat flik i webbläsaren.
   
   ![Startsidan för datafabriken med panelen Författare och övervakare](./media/doc-common-process/data-factory-home-page.png)
   
   > [!NOTE]
   > Om du ser att webbläsaren har fastnat vid "auktorisering" avmarkerar du kryss rutan **blockera cookies från tredje part och plats data** . Eller behåll det markerat, skapa ett undantag för **login.microsoftonline.com** och försök sedan att öppna appen igen.
   

## <a name="create-a-linked-service"></a>Skapa en länkad tjänst
I den här proceduren skapar du en länkad tjänst för att länka ditt Azure Storage-konto till data fabriken. Den länkade tjänsten har anslutningsinformationen som Data Factory-tjänsten använder vid körning för att ansluta till den.

1. Öppna fliken [**Hantera**](./author-management-hub.md) i det vänstra fönstret på sidan Azure Data Factory UI.

1. På sidan länkade tjänster väljer du **+ ny** för att skapa en ny länkad tjänst.

   ![Ny länkad tjänst](./media/doc-common-process/new-linked-service.png)
   
1. På sidan **New Linked Service** (Ny länkad tjänst) väljer du **Azure Blob Storage** och klickar på **Fortsätt**. 

1. Utför följande steg på sidan ny länkad tjänst (Azure Blob Storage): 

   a. Som **Namn** anger du **AzureStorageLinkedService**.

   b. För **lagrings konto namn** väljer du namnet på ditt Azure Storage konto.

   c. Välj **Testanslutning** och bekräfta att Data Factory-tjänsten kan ansluta till lagringskontot. 

   d. Välj **skapa** för att spara den länkade tjänsten. 

      ![Länkad tjänst](./media/quickstart-create-data-factory-portal/linked-service.png)


## <a name="create-datasets"></a>Skapa datauppsättningar
I den här proceduren skapar du två datauppsättningar: **InputDataset** och **OutputDataset**. Dessa datauppsättningar är av typen **AzureBlob**. De refererar till den länkade Azure Storage-tjänst du skapade i föregående avsnitt. 

Datauppsättningen för indata representerar källdata i indatamappen. I definitionen av datauppsättningen för indata anger du blobcontainern (**adftutorial**), mappen (**input**) och filen (**emp.txt**) som innehåller källdata. 

Datauppsättningen för utdata representerar de data som kopieras till målet. I definitionen av datauppsättningen för utdata anger du blobcontainern (**adftutorial**), mappen (**output**) och filen som data ska kopieras till. Varje pipelinekörning har ett unikt ID tilldelat. Du kan komma åt detta ID via systemvariabeln **RunId**. Namnet på utdatafilen utvärderas dynamiskt baserat på pipelinens körnings-ID.   

I de länkade tjänst inställningarna angav du det Azure Storage konto som innehåller källdata. I inställningarna för källdatauppsättningen anger du exakt var källdata finns (blobcontainer, mapp och fil). I inställningarna för mottagaruppsättningen anger du var du vill kopiera data (blobcontainer, mapp och fil). 

1. Välj fliken **författare** i det vänstra fönstret.

1. Välj **+** knappen (plus) och välj sedan **data uppsättning**.

   ![Meny för att skapa en datauppsättning](./media/quickstart-create-data-factory-portal/new-dataset-menu.png)

1. På sidan **ny data uppsättning** väljer du **Azure Blob Storage** och väljer sedan **Fortsätt**. 

1. På sidan **Välj format** väljer du format typ för dina data och väljer sedan **Fortsätt**. I det här fallet väljer du **binär** när du kopierar filer som-är utan att parsa innehållet.

   ![Välj format](./media/quickstart-create-data-factory-portal/select-format.png)
   
1. På sidan **Ange egenskaper** slutför du följande steg:

    a. Under **namn** anger du **InputDataset**. 

    b. För **Länkad tjänst** väljer du **AzureStorageLinkedService**.

    c. För **Filsökväg** väljer du knappen **Bläddra**.

    d. I fönstret **Välj en fil eller mapp** bläddrar du till mappen **indata** i behållaren **adftutorial** , väljer filen **emp.txt** och väljer sedan **OK**.
    
    e. Välj **OK**.   

    ![Ange egenskaper för InputDataset](./media/quickstart-create-data-factory-portal/set-properties-for-inputdataset.png)
1. Upprepa stegen för att skapa datauppsättningen för utdata:  

    a. Välj **+** knappen (plus) och välj sedan **data uppsättning**.

    b. På sidan **ny data uppsättning** väljer du **Azure Blob Storage** och väljer sedan **Fortsätt**.

    c. På sidan **Välj format** väljer du format typ för dina data och väljer sedan **Fortsätt**.

    d. På sidan **Ange egenskaper** anger du **OutputDataset** som namn. Välj **AzureStorageLinkedService** som länkad tjänst.

    e. Under **fil Sök väg** anger du **adftutorial/output**. Om mappen **utdata** inte finns skapar kopierings aktiviteten den vid körning.

    f. Välj **OK**.   

    ![Ange egenskaper för OutputDataset](./media/quickstart-create-data-factory-portal/set-properties-for-outputdataset.png)

## <a name="create-a-pipeline"></a>Skapa en pipeline 
I den här proceduren skapar och verifierar du en pipeline med en kopieringsaktivitet som använder uppsättningar för indata och utdata. Kopieringsaktiviteten kopierar data från filen som anges i inställningarna för datauppsättningen för indata till filen som anges i inställningarna för datauppsättningen för utdata. Om datauppsättningen för indata endast anger en mapp (inte filnamnet) kopierar kopieringsaktiviteten alla filer i källmappen till målet. 

1. Välj **+** knappen (plus) och välj sedan **pipeline**. 

1. I panelen Allmänt under **Egenskaper** anger du **CopyPipeline** som **namn**. Komprimera sedan panelen genom att klicka på egenskaps ikonen i det övre högra hörnet.

1. Gå till verktygsfältet **Aktiviteter** och expandera **Flytta och transformera**. Dra aktiviteten **Kopiera data** från **aktivitets verktygs lådan** till pipelinens designer-yta. Du kan också söka efter aktiviteter i verktygslådan **Aktiviteter**. Ange **CopyFromBlobToBlob** som **Namn**.

   ![Skapa en kopierings data aktivitet](./media/quickstart-create-data-factory-portal/copy-activity.png)

1. Växla till fliken **Källa** i inställningarna för kopieringsaktiviteten och välj **InputDataset** som **Källdatauppsättning**.

1. Växla till fliken **Mottagare** i inställningarna för kopieringsaktiviteten och välj **OutputDataset** som **Datauppsättning för mottagare**.

1. Verifiera pipelineinställningarna genom att klicka på **Verifiera** i verktygsfältet för pipelinen. Bekräfta att pipelinen har verifierats. Om du vill stänga verifierings resultatet väljer du verifierings knappen i det övre högra hörnet. 

   ![Verifiera en pipeline](./media/quickstart-create-data-factory-portal/pipeline-validate.png)

## <a name="debug-the-pipeline"></a>Felsöka pipeline
I det här steget felsöker du pipelinen innan du distribuerar den till Data Factory. 

1. Klicka på **Felsök** i Pipeline-verktygsfältet över arbetsytan för att starta en testkörning. 
    
1. Bekräfta att du ser status för pipelinekörningen på fliken **Utdata** i pipelineinställningarna längst ner. 
 
    ![Kör utdata för pipeline](./media/quickstart-create-data-factory-portal/pipeline-output.png)

1. Bekräfta att du ser en utdatafil i **outputfolder** för containern **adftutorial**. Om mappen utdata inte finns skapas den automatiskt av Data Factory tjänsten. 

## <a name="trigger-the-pipeline-manually"></a>Utlös pipelinen manuellt
I den här proceduren distribuerar du entiteter (länkade tjänster, datauppsättningar, pipeliner) till Azure Data Factory. Sedan utlöser du en pipelinekörning manuellt. 

1. Innan du utlöser en pipeline måste du publicera entiteter i Data Factory. Om du vill publicera väljer du **publicera alla** överst. 

    ![Publicera alla](./media/quickstart-create-data-factory-portal/publish-all.png)

1. Om du vill utlösa pipelinen manuellt väljer du **Lägg till utlösare** i pipeline-verktygsfältet och väljer sedan **Utlös nu**. På sidan för **pipeline-körning** väljer du **OK**.

## <a name="monitor-the-pipeline"></a>Övervaka pipeline

1. Växla till fliken **Övervaka** till vänster. Du kan uppdatera listan med knappen **Uppdatera**.

   ![Fliken för övervakning av pipeline-körningar](./media/quickstart-create-data-factory-portal/monitor-trigger-now-pipeline.png)
1. Välj **CopyPipeline** -länken så ser du status för kopierings aktiviteten som körs på den här sidan. 

1. Om du vill visa information om kopierings åtgärden väljer du länken **information** (glasögon image). Mer information om egenskaperna finns i [Copy Activity overview](copy-activity-overview.md) (Översikt över kopieringsaktivitet). 

   ![Detaljerad information om kopieringsåtgärden](./media/quickstart-create-data-factory-portal/copy-operation-details.png)
1. Bekräfta att du ser en ny fil i **utdatamappen**. 
1. Du kan gå tillbaka till **pipeline-körningar** från vyn **aktivitets körningar** genom att välja länken **alla pipeline-körningar** . 

## <a name="trigger-the-pipeline-on-a-schedule"></a>Utlös pipelinen enligt ett schema
Den här proceduren är valfri i den här självstudien. Du kan skapa en *schemautlösare* för att schemalägga pipelinen så att den körs regelbundet (varje timme, varje dag och så vidare). I den här proceduren skapar du en utlösare som ska köras varje minut tills det slutdatum och den sluttid du anger. 

1. Växla till fliken **Författare**. 

1. Gå till din pipeline, Välj **Lägg till utlösare** i pipeline-verktygsfältet och välj sedan **ny/redigera**. 

1. På sidan **Add Triggers** (Lägg till utlösare) väljer du **Choose trigger** (Välj utlösare) och sedan **Ny**. 

1. På sidan **Ny utlösare** under **slut** väljer du **på datum**, anger en slut tid några minuter efter aktuell tid och väljer sedan **OK**. 

   Den tillkommer en kostnad för varje pipelinekörning, så ange sluttiden bara några minuter efter starttiden. Kontrollera att det är samma dag. Se dock till att det finns tillräckligt med tid för pipelinen att köras mellan publicerings tiden och slut tiden. Utlösaren träder endast i kraft när du har publicerat lösningen till Data Factory, och inte när du sparar utlösaren i användargränssnittet. 

1. På sidan **Ny utlösare** markerar du kryss rutan **aktive rad** och väljer sedan **OK**. 

   ![Ny Utlösar inställning](./media/quickstart-create-data-factory-portal/trigger-settings-next.png)
1. Läs varnings meddelandet och välj **OK**.

1. Välj **publicera allt** för att publicera ändringar i Data Factory. 

1. Växla till fliken **Övervaka** till vänster. Om du vill uppdatera listan väljer du **Refresh** (Uppdatera). Du ser att pipelinen körs varje minut från publiceringstiden till sluttiden. 

   Observera värdena i kolumnen **utlöst av** . Den manuella körningen av utlösaren var från steget (**Trigger Now**) (Utlös nu) du gjorde tidigare. 

1. Växla till vyn **Utlös ande körningar** . 

1. Bekräfta att en utdatafil har skapats för varje pipelinekörning fram till det angivet slutdatum och angiven sluttid i **utdatamappen**. 

## <a name="next-steps"></a>Nästa steg
Pipelinen i det här exemplet kopierar data från en plats till en annan i Azure Blob Storage. Gå igenom [självstudiekurserna](tutorial-copy-data-portal.md) om du vill lära dig hur du använder Data Factory i fler scenarier.
