---
title: Data verktyg för att kopiera nya och uppdaterade filer stegvis
description: Skapa en Azure-datafabrik och Använd sedan Kopiera data-verktyget för att stegvis läsa in nya filer baserat på LastModifiedDate.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/09/2020
ms.openlocfilehash: f2f0b3e452b39cb81f435dbee4a3b0f524b0213d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100361153"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Kopiera nya och ändrade filer stegvis baserat på LastModifiedDate med hjälp av verktyget Kopiera data

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

I den här självstudien använder du Azure Portal för att skapa en data fabrik. Sedan använder du Kopiera data-verktyget för att skapa en pipeline som stegvis kopierar nya och ändrade filer, från Azure Blob Storage till Azure Blob Storage. Den används `LastModifiedDate` för att avgöra vilka filer som ska kopieras.

När du har slutfört stegen här kommer Azure Data Factory att söka igenom alla filer i käll arkivet, använda fil filtret av `LastModifiedDate` och kopiera till mål arkivet endast filer som är nya eller som har uppdaterats sedan senaste gången. Observera att om Data Factory skannar ett stort antal filer, bör du fortfarande förvänta dig långa varaktigheter. Det tar lång tid att genomsöka filer, även när mängden data som kopieras minskar.

> [!NOTE]
> Om du inte har använt datafabriken tidigare kan du läsa [Introduktion till Azure Data Factory](introduction.md).

I den här självstudien får du utföra följande uppgifter:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Använd verktyget Kopiera data för att skapa en pipeline.
> * Övervaka pipelinen och aktivitetskörningarna.

## <a name="prerequisites"></a>Förutsättningar

* **Azure-prenumeration**: Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* **Azure Storage konto**: Använd Blob Storage för käll-och mottagar data lager. Om du inte har ett Azure Storage konto följer du instruktionerna i [skapa ett lagrings konto](../storage/common/storage-account-create.md).

## <a name="create-two-containers-in-blob-storage"></a>Skapa två behållare i Blob Storage

Förbered blob-lagringen för självstudien genom att utföra de här stegen:

1. Skapa en behållare med namnet **Source**. Du kan använda olika verktyg för att utföra den här uppgiften, t. ex. [Azure Storage Explorer](https://storageexplorer.com/).

2. Skapa en behållare med namnet **mål**.

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. Välj **Skapa en resurs** i fönstret till vänster. Välj **integrations**  >  **Data Factory**:

   ![Välj Data Factory](./media/doc-common-process/new-azure-data-factory-menu.png)

2. I fönstret **Ny datafabrik**, under **Namn** anger du **ADFTutorialDataFactory**.

   Namnet på datafabriken måste vara globalt unikt. Du kan få det här fel meddelandet:

   ![Namnet är inte tillgängligt, fel meddelande](./media/doc-common-process/name-not-available-error.png)

   Ange ett annat namn för datafabriken om du får ett felmeddelande om namnvärdet. Använd till exempel namnet _**dittnamn**_**ADFTutorialDataFactory**. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
3. Under **prenumeration** väljer du den Azure-prenumeration där du vill skapa den nya data fabriken.
4. Gör något av följande under **resurs grupp**:

    * Välj **Använd befintlig** och välj sedan en befintlig resurs grupp i listan.

    * Välj **Skapa nytt** och ange ett namn för resurs gruppen.
         
    Mer information om resursgrupper finns i [Använda resursgrupper för att hantera Azure-resurser](../azure-resource-manager/management/overview.md).

5. Under **Version** väljer du **V2**.
6. Under **plats** väljer du platsen för data fabriken. Endast platser som stöds visas i listan. Data lag ren (till exempel Azure Storage och Azure SQL Database) och beräkningarna (till exempel Azure HDInsight) som din Data Factory använder kan finnas på andra platser och regioner.
8. Välj **Skapa**.
9. När data fabriken har skapats visas start sidan för Data Factory.
10. Om du vill öppna användar gränssnittet för Azure Data Factory på en separat flik väljer du panelen **skapare & övervakare** :

    ![Datafabrikens startsida](./media/doc-common-process/data-factory-home-page.png)

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Använd verktyget Kopiera data för att skapa en pipeline

1. På sidan **nu sätter vi igång** väljer du panelen **Kopiera data** för att öppna kopiera datas verktyget:

   ![Kopiera data panel](./media/doc-common-process/get-started-page.png)

2. Utför följande steg på sidan **Egenskaper** :

    a. Under **uppgifts namn**, anger du **DeltaCopyFromBlobPipeline**.

    b. Under **aktivitets takt eller aktivitets schema** väljer du **kör regelbundet enligt schema**.

    c. Under **utlösnings typ** väljer du **rullande-fönster**.

    d. Under **upprepning** anger du **15 minuter**.

    e. Välj **Nästa**.

    Data Factory skapar en pipeline med angivet uppgifts namn.

    ![Sidan kopiera data egenskaper](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)

3. Utför följande steg på sidan **käll data lager** :

    a. Välj  **Skapa ny anslutning** för att lägga till en anslutning.

    b. Välj **Azure Blob Storage** från galleriet och välj sedan **Fortsätt**:

    ![Välj Azure blogg lagring](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. På sidan **ny länkad tjänst (Azure Blob Storage)** väljer du ditt lagrings konto i listan **lagrings konto namn** . Testa anslutningen och välj sedan **skapa**.

    d. Välj den nya länkade tjänsten och välj sedan **Nästa**:

   ![Välj den nya länkade tjänsten](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. Gör följande på sidan för att **välja indatafil eller -mapp**:

    a. Bläddra efter och välj **källmappen** och välj sedan **Välj**.

    ![Välj indatafil eller mapp](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)

    b. Under **fil inläsnings beteende** väljer du **stegvis belastning: LastModifiedDate**.

    c. Välj **binär kopia** och välj sedan **Nästa**:

     ![Välj sidan indatafil eller mapp](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)

5. På sidan **mål data lager** väljer du den **AzureBlobStorage** -tjänst som du har skapat. Det här är samma lagrings konto som käll data lagret. Välj sedan **Nästa**.

6. Gör följande på sidan **Choose the output file or folder** (Välj utdatafil eller -mapp):

    a. Bläddra efter och välj **målmappen** och välj sedan **Välj**:

    ![Välj sidan utdatafil eller mapp](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)

    b. Välj **Nästa**.

7. Sidan **Settings** (Inställningar) visas. Välj **Nästa**.

8. På sidan **Sammanfattning** granskar du inställningarna och väljer sedan **Nästa**.

    ![Sammanfattningssida](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)

9. Välj **Övervaka** på sidan **Distribution** för att övervaka pipelinen (aktiviteten).

    ![Distributionssida](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)

10. Observera att fliken **Övervaka** till vänster väljs automatiskt. Programmet växlar till fliken **övervakare** . Du ser status för pipelinen. Om du vill uppdatera listan väljer du **Refresh** (Uppdatera). Välj länken under **pipeline-namn** om du vill visa aktivitets körnings information eller köra pipelinen igen.

    ![Uppdatera listan och Visa aktivitets körnings information](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs-1.png)

11. Det finns bara en aktivitet (kopierings aktiviteten) i pipelinen, så att du bara ser en post. Om du vill ha mer information om kopierings åtgärden väljer du länken **information** (glasögon ikonen) i kolumnen **aktivitets namn** . Mer information om egenskaperna finns i [Översikt över kopierings aktivitet](copy-activity-overview.md).

    ![Kopierings aktivitet i pipelinen](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)

    Eftersom det inte finns några filer i käll behållaren i ditt Blob Storage-konto ser du inga filer som har kopierats till mål behållaren i kontot:

    ![Inga filer i käll containern eller mål behållaren](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)

12. Skapa en tom textfil och ge den namnet **file1.txt**. Ladda upp text filen till käll behållaren i ditt lagrings konto. Du kan använda olika verktyg för att utföra dessa uppgifter, t. ex. [Azure Storage Explorer](https://storageexplorer.com/).

    ![Skapa file1.txt och ladda upp den till käll behållaren](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)

13. Gå tillbaka till vyn **pipeline-körningar** genom att välja **alla pipelines-körningar** och vänta tills samma pipelines automatiskt utlöses igen.  

14. När den andra pipeline-körningen är klar följer du samma steg som tidigare för att granska aktivitets körnings informationen.  

    Du ser att en fil (file1.txt) har kopierats från käll behållaren till mål behållaren för ditt Blob Storage-konto:

    ![file1.txt har kopierats från käll behållaren till mål behållaren](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)

15. Skapa en annan tom textfil och ge den namnet **file2.txt**. Ladda upp text filen till käll behållaren i ditt Blob Storage-konto.

16. Upprepa steg 13 och 14 för den andra text filen. Du ser att endast den nya filen (file2.txt) har kopierats från käll behållaren till mål behållaren för ditt lagrings konto under den här pipeline-körningen.  

    Du kan också kontrol lera att endast en fil har kopierats med hjälp av [Azure Storage Explorer](https://storageexplorer.com/) för att genomsöka filerna:

    ![Genomsök filer med hjälp av Azure Storage Explorer](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)


## <a name="next-steps"></a>Nästa steg
Gå till följande självstudie för att lära dig hur du omvandlar data med hjälp av ett Apache Spark-kluster på Azure:

> [!div class="nextstepaction"]
>[Transformera data i molnet med hjälp av ett Apache Spark kluster](tutorial-transform-data-spark-portal.md)
