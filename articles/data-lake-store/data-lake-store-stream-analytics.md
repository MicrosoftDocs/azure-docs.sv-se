---
title: Strömma data från Stream Analytics till Data Lake Storage Gen1 – Azure
description: Lär dig hur du använder Azure Data Lake Storage Gen1 som utdata för ett Azure Stream Analytics jobb, med ett enkelt scenario som läser data från en Azure Storage-blob.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/30/2018
ms.author: twooley
ms.openlocfilehash: 4c289ecb1d8471a7b99f1d4c85a0163de4d0c593
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "91576225"
---
# <a name="stream-data-from-azure-storage-blob-into-azure-data-lake-storage-gen1-using-azure-stream-analytics"></a>Strömma data från Azure Storage Blob till Azure Data Lake Storage Gen1 med Azure Stream Analytics
I den här artikeln får du lära dig hur du använder Azure Data Lake Storage Gen1 som utdata för ett Azure Stream Analytics jobb. Den här artikeln visar ett enkelt scenario som läser data från en Azure Storage BLOB (indata) och skriver data till Data Lake Storage Gen1 (utdata).

## <a name="prerequisites"></a>Förutsättningar
Innan du påbörjar de här självstudierna måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Azure Storage konto**. Du kommer att använda en BLOB-behållare från det här kontot för att mata in data för ett Stream Analytics jobb. I den här självstudien förutsätter vi att du har ett lagrings konto med namnet **storageforasa** och en behållare i kontot som heter **storageforasacontainer**. När du har skapat behållaren laddar du upp en exempel data fil till den. 
  
* **Ett data Lake Storage gen1 konto**. Följ anvisningarna i [Kom igång med Azure Data Lake Storage gen1 med hjälp av Azure Portal](data-lake-store-get-started-portal.md). Vi antar att du har ett Data Lake Storage Gen1-konto som heter **myadlsg1**. 

## <a name="create-a-stream-analytics-job"></a>Skapa ett Stream Analytics-jobb
Du börjar med att skapa ett Stream Analytics jobb som innehåller en indatakälla och ett utgående mål. I den här självstudien är källan en Azure Blob-behållare och målet är Data Lake Storage Gen1.

1. Logga in på [Azure Portal](https://portal.azure.com).

2. Klicka på **Stream Analytics jobb** i den vänstra rutan och klicka sedan på **Lägg till**.

    ![Skapa ett Stream Analytics jobb](./media/data-lake-store-stream-analytics/create.job.png "Skapa ett Stream Analytics-jobb")

    > [!NOTE]
    > Se till att du skapar jobbet i samma region som lagrings kontot, eller debiteras ytterligare kostnad för att flytta data mellan regioner.
    >

## <a name="create-a-blob-input-for-the-job"></a>Skapa en BLOB-ineffekt för jobbet

1. Öppna sidan för Stream Analytics jobbet, klicka på fliken **indata** i den vänstra rutan och klicka sedan på **Lägg till**.

    ![Skärm bild av bladet Stream Analytics jobb med alternativet indata och alternativet Lägg till ström indata som kallas för.](./media/data-lake-store-stream-analytics/create.input.1.png "Lägg till inmatade jobb")

2. Ange följande värden på det **nya indatamängd** -bladet.

    ![Skärm bild av bladet Blob Storage – nytt indata.](./media/data-lake-store-stream-analytics/create.input.2.png "Lägg till inmatade jobb")

   * Ange ett unikt namn för indatamängden för **inmatat alias**.
   * I **typ av källa** väljer du **data ström**.
   * I **källa** väljer du **Blob Storage**.
   * För **prenumeration** väljer du **Använd Blob Storage från aktuell prenumeration**.
   * För **lagrings konto** väljer du det lagrings konto som du skapade som en del av förutsättningarna. 
   * För **behållare** väljer du den behållare som du skapade i det valda lagrings kontot.
   * I **format för händelse serialisering** väljer du **CSV**.
   * För **avgränsare** väljer du **flik**.
   * För **kodning** väljer du **UTF-8**.

     Klicka på **Skapa**. Portalen lägger nu till inmatade och testar anslutningen till den.


## <a name="create-a-data-lake-storage-gen1-output-for-the-job"></a>Skapa en Data Lake Storage Gen1 utdata för jobbet

1. Öppna sidan för Stream Analytics jobb, klicka på fliken **utdata** , klicka på **Lägg till** och välj **data Lake Storage gen1**.

    ![Skärm bild av bladet Stream Analytics jobb med alternativet utdata, Lägg till alternativ och Data Lake Storage gen 1-alternativ som kallas.](./media/data-lake-store-stream-analytics/create.output.1.png "Lägga till utdata i jobbet")

2. Ange följande värden på bladet **ny utdata** .

    ![Skärm bild av bladet Data Lake Storage gen 1 – nytt utdata med alternativet auktorisera som kallas för.](./media/data-lake-store-stream-analytics/create.output.2.png "Lägga till utdata i jobbet")

    * Ange ett unikt namn för jobbets utdata i **alias**. Detta är ett eget namn som används i frågor för att dirigera frågeresultatet till det här Data Lake Storage Gen1 kontot.
    * Du uppmanas att godkänna åtkomst till Data Lake Storage Gen1 kontot. Klicka på **auktorisera**.

3. På bladet **ny utdata** fortsätter du att ange följande värden.

    ![Skärm bild av bladet Data Lake Storage gen 1-nya utdata.](./media/data-lake-store-stream-analytics/create.output.3.png "Lägga till utdata i jobbet")

   * För **konto namn** väljer du det data Lake Storage gen1 konto som du redan har skapat där du vill att jobbets utdata ska skickas till.
   * För **mönster för sökvägar** anger du en fil Sök väg som används för att skriva filer inom det angivna data Lake Storage gen1 kontot.
   * Om du använder ett datum-token i prefixets sökväg i **datum format** kan du välja det datum format som filerna är ordnade i.
   * Om du har använt en tidstoken i prefixets sökväg anger du tids formatet som dina filer är ordnade i **tids format**.
   * I **format för händelse serialisering** väljer du **CSV**.
   * För **avgränsare** väljer du **flik**.
   * För **kodning** väljer du **UTF-8**.
    
     Klicka på **Skapa**. Portalen lägger nu till utdata och testar anslutningen till den.
    
## <a name="run-the-stream-analytics-job"></a>Köra Stream Analytics-jobbet

1. Om du vill köra ett Stream Analytics jobb måste du köra en fråga från fliken **fråga** . I den här självstudien kan du köra exempel frågan genom att ersätta plats hållarna med jobb indata och utdata för utdata, som du ser i skärmdumpen nedan.

    ![Kör fråga](./media/data-lake-store-stream-analytics/run.query.png "Kör frågan")

2. Klicka på **Spara** längst upp på skärmen och klicka sedan på **Start** på fliken **Översikt** . I dialog rutan väljer du **anpassad tid** och anger sedan aktuellt datum och tid.

    ![Ange jobb tid](./media/data-lake-store-stream-analytics/run.query.2.png "Ange jobb tid")

    Starta jobbet genom att klicka på **Starta** . Det kan ta upp till några minuter att starta jobbet.

3. Om du vill utlösa jobbet för att välja data från blobben kopierar du en exempel data fil till BLOB-behållaren. Du kan hämta en exempel data fil från [Azure Data Lake git-lagringsplatsen](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt). I den här kursen ska vi kopiera filen **vehicle1_09142014.csv**. Du kan använda olika klienter, till exempel [Azure Storage Explorer](https://storageexplorer.com/), för att ladda upp data till en BLOB-behållare.

4. På fliken **Översikt** , under **övervakning**, se hur data bearbetades.

    ![Övervaka jobb](./media/data-lake-store-stream-analytics/run.query.3.png "Övervaka jobb")

5. Slutligen kan du kontrol lera att jobbets utgående data är tillgängliga i Data Lake Storage Gen1-kontot. 

    ![Verifiera utdata](./media/data-lake-store-stream-analytics/run.query.4.png "Verifiera utdata")

    I rutan Datautforskaren ser du att utdata skrivs till en mappsökväg som anges i inställningarna för Data Lake Storage Gen1 utdata ( `streamanalytics/job/output/{date}/{time}` ).  

## <a name="see-also"></a>Se även
* [Skapa ett HDInsight-kluster för att använda Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
