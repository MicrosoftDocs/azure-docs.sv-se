---
title: Branchning och länkning av aktiviteter i en pipeline med hjälp av Azure Portal
description: Lär dig hur du styr flödet av data i Azure Data Factory pipelinen med hjälp av Azure Portal.
author: dcstwh
ms.author: weetok
ms.reviewer: maghan
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019; seo-dt-2019
ms.date: 01/11/2018
ms.openlocfilehash: 9d8d9af4892001ff1f8f60a92f28aedad14c17cf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100378612"
---
# <a name="branching-and-chaining-activities-in-an-azure-data-factory-pipeline-using-the-azure-portal"></a>Branchning och länkning av aktiviteter i en Azure Data Factory pipelinen med hjälp av Azure Portal

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

I den här självstudiekursen skapar du en Data Factory-pipeline som visar några av funktionerna för att styra flödet. Den här pipelinen skapar en enkel kopia från en container i Azure Blob Storage till en annan container i samma lagringskonto. Om kopieringen lyckas skickar pipelinen information om den lyckade kopieringsåtgärden (till exempel hur mycket data som har skrivits) i ett e-postmeddelande. Om kopieringen misslyckas skickar pipelinen information om att kopieringen misslyckades (till exempel ett felmeddelande) i ett e-postmeddelande. I självstudiekursen visas olika exempel på hur du skickar parametrar.

En översikt över scenariot: ![ diagrammet visar Azure-Blob Storage, som är målet för en kopia, som på lyckas skickar ett e-postmeddelande med information eller, vid fel, skickar ett e-postmeddelande med fel information.](media/tutorial-control-flow-portal/overview.png)

I den här självstudiekursen får du göra följande:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa en länkad Azure Storage-tjänst.
> * Skapa en Azure Blob-datauppsättning
> * Skapa en pipeline som innehåller en kopieringsaktivitet och en webbaktivitet
> * Skicka utdata för aktiviteter till efterföljande aktiviteter
> * Skicka parametrar och använda systemvariabler
> * Starta en pipelinekörning
> * Övervaka pipelinen och aktivitetskörningar

I den här självstudien används Azure-portalen. Du kan använda andra metoder för att interagera med Azure Data Factory (se Snabbstarter i innehållsförteckningen).

## <a name="prerequisites"></a>Förutsättningar

* **Azure-prenumeration**. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.
* **Azure Storage konto**. Du kan använda blob-lagringen som **källa** för datalagringen. Om du inte har ett Azure Storage-konto kan du läsa artikeln [skapa ett lagrings konto](../storage/common/storage-account-create.md) för steg för att skapa ett.
* **Azure SQL Database**. Du använder databasen som **mottagare** för datalagringen. Om du inte har en databas i Azure SQL Database går du till artikeln [skapa en databas i Azure SQL Database](../azure-sql/database/single-database-create-quickstart.md) för steg för att skapa en.

### <a name="create-blob-table"></a>Skapa blob-tabell

1. Öppna Anteckningar. Kopiera följande text och spara den som **input.txt** på disken.

    ```
    John,Doe
    Jane,Doe
    ```
2. Gör följande med verktyg som [Azure Storage Explorer](https://storageexplorer.com/):
    1. Skapa containern **adfv2branch**.
    2. Skapa mappen **input** i containern **adfv2branch**.
    3. Överför filen **input.txt** till containern.

## <a name="create-email-workflow-endpoints"></a>Skapa slutpunkter för e-postarbetsflödet
För att utlösa utskicket av ett e-postmeddelande från pipelinen använder du [Logic Apps](../logic-apps/logic-apps-overview.md) för att definiera arbetsflödet. Mer information om hur du skapar ett Logic App-arbetsflöde finns i [Skapa en logikapp](../logic-apps/quickstart-create-first-logic-app-workflow.md).

### <a name="success-email-workflow"></a>Lyckat e-postarbetsflöde
Skapa ett Logic App-arbetsflöde med namnet `CopySuccessEmail`. Definiera arbetsflödesutlösaren som `When an HTTP request is received`, och lägg till en `Office 365 Outlook – Send an email`-åtgärd.

![Lyckat e-postarbetsflöde](media/tutorial-control-flow-portal/success-email-workflow.png)

För begärandeutlösaren fyller du i `Request Body JSON Schema` med följande JSON:

```json
{
    "properties": {
        "dataFactoryName": {
            "type": "string"
        },
        "message": {
            "type": "string"
        },
        "pipelineName": {
            "type": "string"
        },
        "receiver": {
            "type": "string"
        }
    },
    "type": "object"
}
```

Din begäran i Logic App Designer ska se ut som på följande bild:

![Logic App Designer – begäran](media/tutorial-control-flow-portal/logic-app-designer-request.png)

För åtgärden **Skicka e-post** anger du hur du vill formatera e-postmeddelandet med hjälp av egenskaperna som skickas i begärandetexten i JSON-schemat. Här är ett exempel:

![Logic App Designer – åtgärd för att skicka e-post](media/tutorial-control-flow-portal/send-email-action-2.png)

Spara arbetsflödet. Notera URL:en för HTTP Post-begäran för arbetsflödet för e-postmeddelandet om lyckad kopiering:

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

### <a name="fail-email-workflow"></a>Arbetsflöde för e-postmeddelande om misslyckad kopiering
Följ samma steg för att skapa ytterligare ett Logic Apps-arbetsflöde för **CopyFailEmail**. `Request Body JSON schema` är samma i begärandeutlösaren. Ändra formatet för ditt e-postmeddelande som `Subject` för att skapa ett e-postmeddelande om att kopieringen misslyckats. Här är ett exempel:

![Logic App Designer – arbetsflöde för e-postmeddelande om misslyckad kopiering](media/tutorial-control-flow-portal/fail-email-workflow-2.png)

Spara arbetsflödet. Notera URL:en för HTTP Post-begäran för arbetsflödet för e-postmeddelandet om misslyckad kopiering:

```
//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

Du bör nu ha två arbetsflödes-URL:er:

```
//Success Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000

//Fail Request Url
https://prodxxx.eastus.logic.azure.com:443/workflows/000000/triggers/manual/paths/invoke?api-version=2016-10-01&sp=%2Ftriggers%2Fmanual%2Frun&sv=1.0&sig=000000
```

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Starta webbläsaren **Microsoft Edge** eller **Google Chrome**. Användargränssnittet för Data Factory stöds för närvarande bara i webbläsarna Microsoft Edge och Google Chrome.
1. På den vänstra menyn väljer du **skapa en resurs**  >  **data och analys**  >  **Data Factory**:

   ![Valet Data Factory i fönstret Nytt](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. På sidan **Ny datafabrik** anger du **ADFTutorialDataFactory** som **namn**.

     ![Sidan Ny datafabrik](./media/tutorial-control-flow-portal/new-azure-data-factory.png)

   Namnet på Azure Data Factory måste vara **globalt unikt**. Om följande fel returneras ändrar du namnet på datafabriken (till exempel dittnamnADFTutorialDataFactory) och provar att skapa fabriken igen. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.

   *Data fabriks namnet "ADFTutorialDataFactory" är inte tillgängligt.*

3. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i.
4. För **resursgruppen** utför du något av följande steg:

      - Välj **Använd befintlig** och välj en befintlig resurs grupp i den nedrullningsbara listan.
      - Välj **Skapa ny** och ange namnet på en resurs grupp.   
         
        Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/management/overview.md).  
4. Välj **V2** för **versionen**.
5. Välj **plats** för datafabriken. Endast platser som stöds visas i listrutan. Datalagren (Azure Storage, Azure SQL Database osv.) och beräkningarna (HDInsight osv.) som används i Data Factory kan finnas i andra regioner.
6. Välj **fäst till instrumentpanelen**.     
7. Klicka på **Skapa**.      
8. På instrument panelen visas följande panel med status: **distribuerar Data Factory**.

    ![panelen distribuerar datafabrik](media/tutorial-control-flow-portal/deploying-data-factory.png)
9. När datafabriken har skapats visas sidan **Datafabrik** som på bilden.

   ![Datafabrikens startsida](./media/tutorial-control-flow-portal/data-factory-home-page.png)
10. Klicka på rutan **Författare och övervakare** för att starta användargränssnittet för Azure Data Factory på en separat flik.


## <a name="create-a-pipeline"></a>Skapa en pipeline
I det här steget kan du skapa en pipeline med en kopieringsaktivitet och två webbaktiviteter. Du använder följande funktioner för att skapa pipelinen:

- Parametrar för pipelinen som har åtkomst via datauppsättningar.
- Webbaktivitet för att anropa logic apps-arbetsflöden för att skicka e-postmeddelanden (lyckades/misslyckades).
- Ansluta en aktivitet med en annan aktivitet (vid lyckat och misslyckat resultat)
- Använda utdata från en aktivitet som indata i efterföljande aktivitet

1. På sidan **Kom igång** i användargränssnittet för Data Factory klickar du på panelen **Skapa pipeline**.  

   ![Sidan Kom igång](./media/tutorial-control-flow-portal/get-started-page.png)
3. I egenskapsfönstret för pipelinen väljer du fliken **Parametrar** och använder knappen **Ny** för att lägga till tre parametrar av typen String: sourceBlobContainer, sinkBlobContainer och receiver.

    - **sourceBlobContainer** – parameter i pipelinen som används av blob-datauppsättningen för källan.
    - **sinkBlobContainer** – parameter i pipelinen som används av blob-datauppsättningen för mottagaren
    - **mottagare** – den här parametern används av de två webb aktiviteterna i pipelinen som skickar lyckade eller misslyckade e-postmeddelanden till mottagaren vars e-postadress anges av den här parametern.

   ![Meny för ny pipeline](./media/tutorial-control-flow-portal/pipeline-parameters.png)
4. I verktygslådan **Aktiviteter** visar du **Dataflöde** och drar och släpper aktiviteten **Kopiera** till pipelinedesignytan.

   ![Dra och släpp kopieringsaktivitet](./media/tutorial-control-flow-portal/drag-drop-copy-activity.png)
5. I fönstret **Egenskaper** för aktiviteten **Kopiera** längst ned väljer du fliken **Källa** och klickar på **+ Ny**. I det här steget skapar du en källdatauppsättning för kopieringsaktiviteten.

   ![Skärm bild som visar hur du skapar en käll data uppsättning för kopierings aktiviteten.](./media/tutorial-control-flow-portal/new-source-dataset-button.png)
6. Välj **Azure Blob Storage** i fönstret **Ny datauppsättning** och klicka på **Slutför**.

   ![Välj Azure Blob Storage](./media/tutorial-control-flow-portal/select-azure-blob-storage.png)
7. En ny **flik** med namnet **AzureBlob1** visas. Ändra namnet på datauppsättningen till **SourceBlobDataset**.

   ![Allmänna inställningar för datauppsättning](./media/tutorial-control-flow-portal/dataset-general-page.png)
8. Välj fliken **Anslutning** i fönstret **Egenskaper** och klicka på Ny för **Länkad tjänst**. I det här steget skapar du en länkad tjänst för att länka ditt Azure Storage-konto till datafabriken.

   ![Datauppsättningsanslutning – ny länkad tjänst](./media/tutorial-control-flow-portal/dataset-connection-new-button.png)
9. Utför följande steg i fönstret **New Linked Service** (Ny länkad tjänst):

    1. Ange **AzureStorageLinkedService** som **namn**.
    2. Välj ditt Azure Storage-konto i **Lagringskontonamn**.
    3. Klicka på **Spara**.

   ![Ny länkad Azure Storage-tjänst](./media/tutorial-control-flow-portal/new-azure-storage-linked-service.png)
12. Ange `@pipeline().parameters.sourceBlobContainer` för mappen och `emp.txt` för filnamnet. Du kan använda pipelineparametern sourceBlobContainer för att ange sökvägen till mappen för datauppsättningen.

    ![Inställningar för källdatauppsättningen](./media/tutorial-control-flow-portal/source-dataset-settings.png)

13. Välj fliken **Pipeline**, eller klicka på pipelinen i trädvyn. Bekräfta att **SourceBlobDataset** har valts för **Source Dataset** (Källdatauppsättning).
      
   ![Källdatauppsättning](./media/tutorial-control-flow-portal/pipeline-source-dataset-selected.png)

13. I fönstret Egenskaper väljer du fliken **Mottagare** och klickar på **+ Ny** för **Sink Dataset** (Datauppsättning för mottagare). I det här steget skapar du en datauppsättning för mottagare för kopieringsaktiviteten på liknande sätt som när du skapade källdatauppsättningen.

    ![Knapp för ny datauppsättning för mottagare](./media/tutorial-control-flow-portal/new-sink-dataset-button.png)
14. Välj **Azure Blob Storage** i fönstret **Ny datauppsättning** och klicka på **Slutför**.
15. På sidan **Allmänna inställningar** för datauppsättningen anger du **SinkBlobDataset** som **namn**.
16. Välj fliken **Anslutning** och gör följande:

    1. Välj **AzureStorageLinkedService** för **LinkedService**.
    2. Ange `@pipeline().parameters.sinkBlobContainer` för mappen.
    1. Ange `@CONCAT(pipeline().RunId, '.txt')` för filnamnet. Uttrycket använder ID:t för den aktuella pipelinekörningen som filnamn. En lista över systemvariabler och uttryck som stöds finns i [Systemvariabler](control-flow-system-variables.md) och [Uttrycksspråk](control-flow-expression-language-functions.md).

        ![Inställningar för datauppsättning för mottagare](./media/tutorial-control-flow-portal/sink-dataset-settings.png)
17. Välj fliken **Pipeline** högst upp. Visa **Allmänt** i verktygslådan **Aktiviteter** och dra och släpp en **webbaktivitet** till pipelinedesignytan. Ange **SendSuccessEmailActivity** som namn på aktiviteten. Webbaktiviteten tillåter anrop till valfri REST-slutpunkt. Mer information om aktiviteten finns i [Webbaktivitet](control-flow-web-activity.md). Denna pipeline använder en webbaktivitet för att anropa Logic Apps arbetsflöde för e-post.

    ![Dra och släpp den första webbaktiviteten](./media/tutorial-control-flow-portal/success-web-activity-general.png)
18. Välj fliken **Inställningar** på fliken **Allmänt** och gör följande:
    1. För **URL** anger du URL:en för logic apps-arbetsflödet som skickar e-postmeddelandet om att det lyckades.  
    2. Välj **POST** som **metod**.
    3. Klicka på länken **+ Add header** (Lägg till sidhuvud) i avsnittet **Headers** (Sidhuvud).
    4. Lägg till **Content-Type** (Innehållstyp) och ge den värdet **application/json**.
    5. Ange följande JSON för **Body** (meddelandetext).

        ```json
        {
            "message": "@{activity('Copy1').output.dataWritten}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
        }
        ```
        Meddelandetexten innehåller följande egenskaper:

       - Meddelande – skicka värdet `@{activity('Copy1').output.dataWritten`. Hämtar en egenskap för den tidigare kopieringsaktiviteten och skickar värdet för dataWritten. Vid ett fel skickas felutdata i stället för `@{activity('CopyBlobtoBlob').error.message`.
       - Datafabriksnamn – skicka värdet för `@{pipeline().DataFactory}`. Detta är en systemvariabel som gör att du kan komma åt motsvarande datafabriksnamn. En lista över systemvariabler finns i artikeln om [systemvariabler](control-flow-system-variables.md).
       - Pipelinenamn – skicka värdet för `@{pipeline().Pipeline}`. Detta är också en systemvariabel som gör att du kan komma åt motsvarande pipelinenamn.
       - Mottagare – skicka värdet för "\@pipeline().parameters.receiver"). Kommer åt pipelineparametrar.

         ![Inställningar för den första webbaktiviteten](./media/tutorial-control-flow-portal/web-activity1-settings.png)         
19. Anslut **kopieringsaktiviteten** till **webbaktiviteten** genom att dra den gröna knappen bredvid kopieringsaktiviteten och släppa den på webbaktiviteten.

    ![Anslut kopieringsaktiviteten till den första webbaktiviteten](./media/tutorial-control-flow-portal/connect-copy-web-activity1.png)
20. Dra och släpp en till **webbaktivitet** från verktygslådan Aktiviteter till pipelinedesignytan och ange **SendFailureEmailActivity** som **namn**.

    ![Namn på den andra webbaktiviteten](./media/tutorial-control-flow-portal/web-activity2-name.png)
21. Växla till fliken **Inställningar** och gör följande:

    1. För **URL** anger du URL:en för logic apps-arbetsflödet som skickar e-postmeddelandet om att det misslyckades.  
    2. Välj **POST** som **metod**.
    3. Klicka på länken **+ Add header** (Lägg till sidhuvud) i avsnittet **Headers** (Sidhuvud).
    4. Lägg till **Content-Type** (Innehållstyp) och ge den värdet **application/json**.
    5. Ange följande JSON för **Body** (meddelandetext).

        ```json
        {
            "message": "@{activity('Copy1').error.message}",
            "dataFactoryName": "@{pipeline().DataFactory}",
            "pipelineName": "@{pipeline().Pipeline}",
            "receiver": "@pipeline().parameters.receiver"
        }
        ```

        ![Inställningar för den andra webbaktiviteten](./media/tutorial-control-flow-portal/web-activity2-settings.png)         
22. Välj **kopieringsaktiviteten** i pipelinedesignen och klicka på **+->** och välj **Fel**.  

    ![Skärm bild som visar hur du väljer fel på kopierings aktiviteten i pipelinens designer.](./media/tutorial-control-flow-portal/select-copy-failure-link.png)
23. Dra den **röda** knappen bredvid kopieringsaktiviteten till den andra webbaktiviteten, **SendFailureEmailActivity**. Du kan flytta aktiviteterna så att pipelinen ser ut som på följande bild:

    ![Fullständig pipeline med alla aktiviteter](./media/tutorial-control-flow-portal/full-pipeline.png)
24. Verifiera pipelinen genom att klicka på **Verifiera** i verktygsfältet. Stäng fönstret **Pipeline Validation Output** (Resultat av pipelineverifiering) genom att klicka på **>>**.

    ![Verifiera pipeline](./media/tutorial-control-flow-portal/validate-pipeline.png)
24. Om du vill publicera entiteter (datauppsättningar, pipeliner osv.) till Data Factory-tjänsten väljer du **Publicera alla**. Vänta tills du ser meddelandet om att entiteterna **har publicerats**.

    ![Publicera](./media/tutorial-control-flow-portal/publish-button.png)

## <a name="trigger-a-pipeline-run-that-succeeds"></a>Utlös en lyckad pipelinekörning
1. Om du vill **utlösa** en pipelinekörning klickar du på **Utlösare** i verktygsfältet och klickar på **Trigger Now** (Utlös nu).

    ![Utlösa en pipelinekörning](./media/tutorial-control-flow-portal/trigger-now-menu.png)
2. Gör följande i fönstret **Pipeline Run** (Pipelinekörning):

    1. Ange **adftutorial/adfv2branch/input** för parametern **sourceBlobContainer**.
    2. Ange **adftutorial/adfv2branch/output** för parametern **sinkBlobContainer**.
    3. Ange en **e-postadress** för **mottagaren**.
    4. Klicka på **Slutför**

        ![Parametrar för pipelinekörning](./media/tutorial-control-flow-portal/pipeline-run-parameters.png)

## <a name="monitor-the-successful-pipeline-run"></a>Övervaka den lyckade pipelinekörningen

1. Om du vill övervaka pipelinekörningen väljer du fliken **Övervaka** till vänster. Du kan se pipelinekörningen som du utlöste manuellt. Du kan uppdatera listan med knappen **Uppdatera**.

    ![Lyckad pipelinekörning](./media/tutorial-control-flow-portal/monitor-success-pipeline-run.png)
2. Om du vill **visa aktivitetskörningar** som är associerade med pipelinekörningarna klickar du på den första länken i kolumnen **Actions** (Åtgärder). Du kan gå tillbaka till föregående vy genom att klicka på **Pipeliner** högst upp. Du kan uppdatera listan med knappen **Uppdatera**.

    ![Skärm bild som visar hur du visar listan över aktivitets körningar.](./media/tutorial-control-flow-portal/activity-runs-success.png)

## <a name="trigger-a-pipeline-run-that-fails"></a>Utlös en misslyckad pipelinekörning
1. Välj fliken **Redigera** till vänster.
2. Om du vill **utlösa** en pipelinekörning klickar du på **Utlösare** i verktygsfältet och klickar på **Trigger Now** (Utlös nu).
3. Gör följande i fönstret **Pipeline Run** (Pipelinekörning):

    1. Ange **adftutorial/dummy/input** för parametern **sourceBlobContainer**. Se till att mappen dummy inte finns i containern adftutorial.
    2. Ange **adftutorial/dummy/output** för parametern **sinkBlobContainer**.
    3. Ange en **e-postadress** för **mottagaren**.
    4. Klicka på **Finish**.

## <a name="monitor-the-failed-pipeline-run"></a>Övervaka den misslyckade pipelinekörningen

1. Om du vill övervaka pipelinekörningen väljer du fliken **Övervaka** till vänster. Du kan se pipelinekörningen som du utlöste manuellt. Du kan uppdatera listan med knappen **Uppdatera**.

    ![Misslyckad pipelinekörning](./media/tutorial-control-flow-portal/monitor-failure-pipeline-run.png)
2. Klicka på **fellänken** för pipelinen om du vill visa mer information om felet.

    ![Pipelinefel](./media/tutorial-control-flow-portal/pipeline-error-message.png)
2. Om du vill **visa aktivitetskörningar** som är associerade med pipelinekörningarna klickar du på den första länken i kolumnen **Actions** (Åtgärder). Du kan uppdatera listan med knappen **Uppdatera**. Observera att kopieringsaktiviteten i pipelinen misslyckades. Webbaktiviteten lyckades skicka ett felmeddelande via e-post till angiven mottagare.

    ![Aktivitetskörningar](./media/tutorial-control-flow-portal/activity-runs-failure.png)
4. Klicka på **fellänken** i kolumnen **Actions** (Åtgärder) om du vill visa mer information om felet.

    ![Aktivitetskörningsfel](./media/tutorial-control-flow-portal/activity-run-error.png)

## <a name="next-steps"></a>Nästa steg
I den här självstudiekursen fick du:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa en länkad Azure Storage-tjänst.
> * Skapa en Azure Blob-datauppsättning
> * Skapa en pipeline som innehåller en kopieringsaktivitet och en webbaktivitet
> * Skicka utdata för aktiviteter till efterföljande aktiviteter
> * Skicka parametrar och använda systemvariabler
> * Starta en pipelinekörning
> * Övervaka pipelinen och aktivitetskörningar

Nu kan du gå vidare till begreppsavsnittet för mer information om Azure Data Factory.
> [!div class="nextstepaction"]
>[Pipelines och aktiviteter](concepts-pipelines-activities.md)
