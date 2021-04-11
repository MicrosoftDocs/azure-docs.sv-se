---
title: Läsa in data i Azure Data Lake Storage Gen2
description: Använd Azure Data Factory för att kopiera data till Azure Data Lake Storage Gen2
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 075778e2650d3b1f67447078eb6cb130849bbb35
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104593789"
---
# <a name="load-data-into-azure-data-lake-storage-gen2-with-azure-data-factory"></a>Läs in data i Azure Data Lake Storage Gen2 med Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Lake Storage Gen2 är en uppsättning funktioner som är avsedda för stor data analys, som är inbyggda i [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md). Det gör att du kan använda gränssnittet med dina data med hjälp av både fil systemet och objekt lagrings paradigm.

Azure Data Factory (ADF) är en helt hanterad molnbaserad data integrerings tjänst. Du kan använda tjänsten för att fylla i Lake med data från en omfattande uppsättning lokala och molnbaserade data lager och spara tid när du skapar dina analys lösningar. En detaljerad lista över anslutningar som stöds finns i tabellen över [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory erbjuder en skalbar lösning för data förflyttning. På grund av den skalbara arkitekturen i ADF kan den mata in data i ett högt data flöde. Mer information finns i [Kopiera aktivitets prestanda](copy-activity-performance.md).

Den här artikeln visar hur du använder verktyget Data Factory Kopiera data för att läsa in data från _Amazon Web Services S3-tjänsten_ i _Azure Data Lake Storage Gen2_. Du kan följa liknande steg för att kopiera data från andra typer av data lager.

>[!TIP]
>Information om hur du kopierar data från Azure Data Lake Storage Gen1 till Gen2 finns i [den här detaljerade genom gången](load-azure-data-lake-storage-gen2-from-gen1.md).

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration: om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.
* Azure Storage konto med Data Lake Storage Gen2 aktiverat: om du inte har ett lagrings konto [skapar du ett konto](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).
* AWS-konto med en S3-Bucket som innehåller data: den här artikeln visar hur du kopierar data från Amazon S3. Du kan använda andra data lager genom att följa liknande steg.

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. På den vänstra menyn väljer du **skapa en resurs**  >  **integrations**  >  **Data Factory**:
   
   ![Valet Data Factory i fönstret Nytt](./media/doc-common-process/new-azure-data-factory-menu.png)

2. På sidan **ny data fabrik** anger du värden för följande fält:
 
    * **Namn**: Ange ett globalt unikt namn för din Azure Data Factory. Om du får felet "Data Factory name *YourDataFactoryName* är inte tillgängligt" anger du ett annat namn på data fabriken. Du kan till exempel använda namnet _**dittnamn**_**ADFTutorialDataFactory**. Försök att skapa data fabriken igen. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
    * **Prenumeration**: Välj din Azure-prenumeration där du vill skapa data fabriken. 
    * **Resurs grupp**: Välj en befintlig resurs grupp i list rutan eller Välj alternativet för att **Skapa nytt** och ange namnet på en resurs grupp. Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/management/overview.md).  
    * **Version**: Välj **V2**.
    * **Plats**: Välj plats för data fabriken. Endast platser som stöds visas i listrutan. De data lager som används av Data Factory kan finnas på andra platser och regioner. 

3. Välj **Skapa**.

4. När du har skapat filen går du till din data fabrik. Du ser **Data Factory** start sida så som visas i följande bild: 
   
   :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Start sidan för Azure Data Factory, med panel för författar & övervakning.":::

   Välj panelen **Författare och övervakare** för att starta dataintegreringsprogrammet på en separat flik.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Läsa in data i Azure Data Lake Storage Gen2

1. På sidan **Kom igång** väljer du panelen **Kopiera data** för att starta verktyget Kopiera data.

2. På sidan **Egenskaper** anger du **CopyFromAmazonS3ToADLS** för fältet **uppgifts namn** och väljer **Nästa**.

    ![Sidan Egenskaper](./media/load-azure-data-lake-storage-gen2/copy-data-tool-properties-page.png)
3. På sidan **Källdatalager** klickar du på **+ Skapa ny anslutning**. Välj **Amazon S3** i kopplings galleriet och välj **Fortsätt**.
    
    ![Käll data lager S3-sida](./media/load-azure-data-lake-storage-gen2/source-data-store-page-s3.png)
    
4. På sidan **ny länkad tjänst (Amazon S3)** gör du följande:

   1. Ange **åtkomst nyckelns ID-** värde.
   2. Ange nyckel värde för **hemlig åtkomst** .
   3. Verifiera inställningarna genom att klicka på **Testa anslutning** och välj sedan **skapa**.

      ![Ange Amazon S3-konto](./media/load-azure-data-lake-storage-gen2/specify-amazon-s3-account.png)
   4. Du ser att en ny AmazonS3-anslutning skapas. Välj **Nästa**. 

5. På sidan **Välj indatafil eller mapp** bläddrar du till den mapp och fil du vill kopiera över. Markera mappen/filen och välj sedan **Välj**.

    ![Välj indatafil eller mapp](./media/load-azure-data-lake-storage-gen2/choose-input-folder.png)

6. Ange kopierings beteendet genom att markera alternativen **rekursivt** och **binär kopia** . Välj **Nästa**.

    ![Skärm bild visar sidan Välj indatafilen eller mappen där du kan välja binär kopia och rekursivt.](./media/load-azure-data-lake-storage-gen2/specify-binary-copy.png)
    
7. På sidan **mål data lager** klickar du på **+ Skapa ny anslutning** och välj sedan **Azure Data Lake Storage Gen2** och välj **Fortsätt**.

    ![Sidan Måldatalager](./media/load-azure-data-lake-storage-gen2/destination-data-storage-page.png)

8. Utför följande steg på sidan **ny länkad tjänst (Azure Data Lake Storage Gen2)** :

   1. Välj ditt konto med funktioner för Data Lake Storage Gen2 i listrutan ”Namn på lagringskonto”.
   2. Välj **skapa** för att skapa anslutningen. Välj sedan **Nästa**.   

        ![Ange Azure Data Lake Storage Gen2 konto](./media/load-azure-data-lake-storage-gen2/specify-azure-data-lake-storage.png)

9. På sidan **Välj utdatafil eller mapp** anger du **copyfroms3** som namn på utdata-mappen och väljer **Nästa**. I ADF skapas motsvarande ADLS Gen2 fil system och undermappar under kopieringen om den inte finns.

    ![Skärm bild som visar den mappsökväg som du anger.](./media/load-azure-data-lake-storage-gen2/specify-adls-path.png)

10. På sidan **Inställningar** väljer du **Nästa** för att använda standardinställningarna.

    ![Sidan Inställningar](./media/load-azure-data-lake-storage-gen2/copy-settings.png)

11. På sidan **Sammanfattning** granskar du inställningarna och väljer **Nästa**.

    ![Sammanfattningssida](./media/load-azure-data-lake-storage-gen2/copy-summary.png)

12. Välj **Övervaka** på sidan **Distribution** för att övervaka pipelinen (aktiviteten). 
 
13. När pipeline-körningen har slutförts visas en pipeline-körning som utlöses av en manuell utlösare. Du kan använda länkar i kolumnen **pipeline-namn** om du vill visa aktivitets information och köra pipelinen igen.

    ![Övervaka pipelinekörningar](./media/load-azure-data-lake-storage-gen2/monitor-pipeline-runs.png)

14. Om du vill se aktivitets körningar som är associerade med pipeline-körningen väljer du länken **CopyFromAmazonS3ToADLS** i kolumnen pipeline-namn. Om du vill ha mer information om kopierings åtgärden väljer du länken **information** (glasögon ikonen) under kolumnen aktivitets namn. Du kan övervaka information om data volymen som kopieras från källan till mottagaren, data genom strömning, körnings steg med motsvarande varaktighet och Använd konfiguration.
 
    ![Övervaka aktivitetskörningar](./media/load-azure-data-lake-storage-gen2/monitor-activity-runs.png)
    
    ![Övervaka körnings information för aktivitet](./media/load-azure-data-lake-storage-gen2/monitor-activity-run-details.png)

15. Välj Uppdatera för att uppdatera vyn. Välj **alla pipelines** längst upp för att gå tillbaka till vyn pipelines-körningar.

16. Verifiera att data har kopierats till ditt Data Lake Storage Gen2-konto.

## <a name="next-steps"></a>Nästa steg

* [Översikt över kopierings aktivitet](copy-activity-overview.md)
* [Azure Data Lake Storage Gen2-anslutning](connector-azure-data-lake-storage.md)
