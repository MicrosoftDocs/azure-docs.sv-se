---
title: Kopiera nya filer stegvis baserat på tidspartitionerat fil namn
description: Skapa en Azure-datafabrik och Använd sedan Kopiera data-verktyget för att stegvis läsa in nya filer baserat på tidspartitionerat fil namn.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: aa6c0d9035f172cc7fb520ff58f233a82fca228a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101741080"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>Kopiera nya filer stegvis baserat på partitionerat fil namn med hjälp av Kopiera data-verktyget

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

I den här självstudien skapar du en datafabrik i Azure Portal. Sedan använder du Kopiera data-verktyget för att skapa en pipeline som stegvis kopierar nya filer baserat på tidspartitionerat fil namn från Azure Blob Storage till Azure Blob Storage.

> [!NOTE]
> Om du inte har använt Azure Data Factory tidigare kan du läsa [Introduktion till Azure Data Factory](introduction.md).

I den här självstudien får du göra följande:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Använd verktyget Kopiera data för att skapa en pipeline.
> * Övervaka pipelinen och aktivitetskörningarna.

## <a name="prerequisites"></a>Förutsättningar

* **Azure-prenumeration**: Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* **Azure Storage-konto**: Använd Blob Storage som data lager för _källa_  och _mottagare_ . Om du inte har något Azure-lagringskonto finns det anvisningar i [Skapa ett lagringskonto](../storage/common/storage-account-create.md).

### <a name="create-two-containers-in-blob-storage"></a>Skapa två behållare i Blob Storage

Förbered blob-lagringen för självstudien genom att utföra dessa steg.

1. Skapa en behållare med namnet **Source**.  Skapa en mappsökväg som **2020/03/17/03** i din behållare. Skapa en tom textfil och ge den namnet **file1.txt**. Ladda upp file1.txt till sökvägen **källa/2020/03/17/03** i ditt lagrings konto.  Du kan använda olika verktyg för att utföra dessa uppgifter, exempelvis [Azure Storage Explorer](https://storageexplorer.com/).

    ![Ladda upp filer](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png)

    > [!NOTE]
    > Justera mappnamnet med din UTC-tid.  Om den aktuella UTC-tiden till exempel är 3:38 AM den 17 mars 2020, kan du skapa mappsökvägen som **källa/2020/03/17/03/** av regeln för **källa/{year}/{month}/{Day}/{timme}/**.

2. Skapa en behållare med namnet **mål**. Du kan använda olika verktyg för att utföra dessa uppgifter, exempelvis [Azure Storage Explorer](https://storageexplorer.com/).

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. På den vänstra menyn väljer du **skapa en resurs**  >  **integrations**  >  **Data Factory**:

   ![Valet Data Factory i fönstret Nytt](./media/doc-common-process/new-azure-data-factory-menu.png)

2. I fönstret **Ny datafabrik**, under **Namn** anger du **ADFTutorialDataFactory**.

    Namnet på datafabriken måste vara _globalt unikt_. Du kan få följande felmeddelande:

   ![Felmeddelande för ny datafabrik](./media/doc-common-process/name-not-available-error.png)

   Ange ett annat namn för datafabriken om du får ett felmeddelande om namnvärdet. Använd till exempel namnet _**dittnamn**_**ADFTutorialDataFactory**. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
3. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i.
4. Gör något av följande för **Resursgrupp**:

    a. Välj **Använd befintlig** och välj en befintlig resurs grupp i den nedrullningsbara listan.

    b. Välj **Skapa ny** och ange namnet på en resurs grupp. 
         
    Mer information om resursgrupper finns i [Använda resursgrupper för att hantera Azure-resurser](../azure-resource-manager/management/overview.md).

5. För **version** väljer du **V2**.
6. Under **plats** väljer du en plats för datafabriken. Endast platser som stöds visas i listrutan. Datalagren (t.ex. Azure Storage och SQL Database) och beräkningarna (t.ex. Azure HDInsight) som används i datafabriken kan finnas på andra platser och i andra regioner.
7. Välj **Skapa**.
8. När den har skapats visas startsidan för **Data Factory**.
9. Klicka på panelen **Författare och övervakare** för att starta användargränssnittet för Azure Data Factory i en separat flik.

    ![Datafabrikens startsida](./media/doc-common-process/data-factory-home-page.png)


## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Använd verktyget Kopiera data för att skapa en pipeline

1. På sidan **nu sätter vi igång** väljer du **Kopiera data** titeln för att starta verktyget kopiera data.

   ![Panel för verktyget Kopiera data](./media/doc-common-process/get-started-page.png)

2. Utför följande steg på sidan **Egenskaper** :

    a. Under **uppgifts namn**, anger du **DeltaCopyFromBlobPipeline**.

    b. Under **aktivitets takt eller aktivitets schema** väljer du **kör regelbundet enligt schema**.

    c. Under **utlösnings typ** väljer du **rullande-fönster**.

    d. Under **upprepning** anger du **1 timme (ar)**.

    e. Välj **Nästa**.

    Med användargränssnittet för Data Factory skapas en pipeline med angivet aktivitetsnamn.

    ![Sidan Egenskaper](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png)
3. Gör följande på sidan **Källdatalager**:

    a. Klicka på **+ Skapa ny anslutning** för att lägga till en anslutning
    
    b. Välj Azure Blob Storage från galleriet och välj sedan Fortsätt.
    
    c. På sidan **ny länkad tjänst (Azure Blob Storage)** anger du ett namn för den länkade tjänsten. Välj din Azure-prenumeration och välj ditt lagrings konto i listan **lagrings konto namn** . Testa anslutning och välj sedan **skapa**.

    ![Sidan Källdatalager](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-linkedservice.png)

    d. Välj den nyligen skapade länkade tjänsten på sidan **käll data lager** och klicka sedan på **Nästa**.

4. Gör följande på sidan **Välj indatafil eller mapp**:

    a. Bläddra och välj **käll** behållaren och välj sedan **Välj**.

    ![Skärm bild som visar dialog rutan Välj indatafil eller mapp.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-input-file-folder.png)

    b. Under **fil inläsnings beteende** väljer du **stegvis inläsning: tidspartitionade mappar/fil namn**.

    c. Skriv sökvägen till den dynamiska mappen som **källa/{year}/{month}/{Day}/{Hour}/** och ändra formatet så som visas i följande skärm bild. Kontrol lera **binär kopia** och klicka på **Nästa**.

    ![Skärm bild som visar dialog rutan Välj indatafil eller mapp med en mapp vald.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/check-binary-copy.png)     

5. På sidan **mål data lager** väljer du **AzureBlobStorage**, som är samma lagrings konto som data källans lager och klickar sedan på **Nästa**.

    ![Sidan Måldatalager](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store-page-select-linkedservice.png)
6. Utför följande steg på sidan **Välj utdatafil eller mapp** :

    a. Bläddra och välj **målmappen** och klicka sedan på **Välj**.

    b. Skriv sökvägen till den dynamiska mappen som **mål/{year}/{month}/{Day}/{Hour}/** och ändra formatet enligt följande:

    ![Skärm bild som visar dialog rutan Välj utdatafil eller mapp.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/output-file-name.png)

    c. Klicka på **Nästa**.

    ![Skärm bild som visar dialog rutan Välj utdatafil eller mapp med nästa valt.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/click-next-after-output-folder.png)
7. Sidan **Settings** (Inställningar) visas. Välj **Nästa**.

8. Granska inställningarna på sidan **Sammanfattning** och klicka på **Nästa**.

    ![Sammanfattningssida](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png)

9. Välj **Övervaka** på sidan **Distribution** för att övervaka pipelinen (aktiviteten).
    ![Distributionssida](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png)

10. Observera att fliken **Övervaka** till vänster väljs automatiskt.  Du måste vänta på att pipelinen ska köras när den utlöses automatiskt (ungefär en timme). När den körs klickar du på länken pipeline-namn **DeltaCopyFromBlobPipeline** för att visa information om aktivitets körningen eller kör pipelinen igen. Om du vill uppdatera listan väljer du **Refresh** (Uppdatera).

    ![Skärm bild som visar fönstret pipeline-körningar.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs-1.png)
11. Det finns bara en aktivitet (kopieringsaktiviteten) i pipelinen. Därför visas bara en post. Justera kolumn bredden för **käll** -och **mål** kolumnerna (om det behövs) om du vill visa mer information, kan du se käll filen (file1.txt) har kopierats från  *källan/2020/03/17/03/* till *destinationen/2020/03/17* /03/med samma fil namn. 

    ![Skärm bild som visar information om pipeline-körningar.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png)

    Du kan också kontrol lera att du använder Azure Storage Explorer ( https://storageexplorer.com/) om du vill genomsöka filerna.

    ![Skärm bild som visar information om pipeline-körningar för målet.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png)

12. Skapa en annan tom textfil med det nya namnet som **file2.txt**. Ladda upp file2.txt-filen till sökvägen **källa/2020/03/17/04** i ditt lagrings konto. Du kan använda olika verktyg för att utföra dessa uppgifter, exempelvis [Azure Storage Explorer](https://storageexplorer.com/).

    > [!NOTE]
    > Du kan vara medveten om att en ny mappsökväg krävs för att kunna skapas. Justera mappnamnet med din UTC-tid.  Om den aktuella UTC-tiden till exempel är 4:20 AM på Mar. 17, 2020, kan du skapa mappsökvägen som **källa/2020/03/17/04/** av regeln **{year}/{month}/{Day}/{Hour}/**.

13. Gå tillbaka till vyn **pipeline-körningar** genom att välja **alla pipelines körs** och vänta tills samma pipeline utlöses igen automatiskt efter en halvtimme.  

    ![Skärm bild som visar länken alla pipeline-körningar som går tillbaka till sidan.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png)

14. Välj den nya **DeltaCopyFromBlobPipeline** -länken för den andra pipelinen som körs när den kommer och gör samma sak för att granska information. Du kommer att se käll filen (file2.txt) har kopierats från  **källa/2020/03/17/04/**  till **destinationen/2020/03/17/04/** med samma fil namn. Du kan också kontrol lera att du använder Azure Storage Explorer ( https://storageexplorer.com/) om du vill genomsöka filerna i **mål** behållaren.


## <a name="next-steps"></a>Nästa steg
Fortsätt till följande självstudie och lär dig att transformera data med ett Spark-kluster på Azure:

> [!div class="nextstepaction"]
>[Transformera data med Apache Spark i molnet](tutorial-transform-data-spark-portal.md)
