---
title: Läs in data i Azure Data Lake Storage Gen1
description: Använd Azure Data Factory för att kopiera data till Azure Data Lake Storage Gen1
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 1e75fc65b4afea9dc895580e4711e0050cb7c64f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101716422"
---
# <a name="load-data-into-azure-data-lake-storage-gen1-by-using-azure-data-factory"></a>Läs in data i Azure Data Lake Storage Gen1 med Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

[Azure Data Lake Storage gen1](../data-lake-store/data-lake-store-overview.md) (tidigare kallat Azure Data Lake Store) är en företagsomfattande storskalig lagrings plats för stor data analys arbets belastningar. Med Data Lake Storage Gen1 kan du samla in data om storlek, typ och inmatnings hastighet. Data samlas in på ett och samma ställe för operativa och undersökande analyser.

Azure Data Factory är en helt hanterad molnbaserad data integrerings tjänst. Du kan använda tjänsten för att fylla i Lake med data från ditt befintliga system och spara tid när du skapar dina analys lösningar.

Azure Data Factory erbjuder följande fördelar för att läsa in data i Data Lake Storage Gen1:

* **Enkelt att konfigurera**: en intuitiv 5-stegs guide utan skript krävs.
* **Stöd för omfattande data lager**: inbyggt stöd för en omfattande uppsättning lokala och molnbaserade data lager. En detaljerad lista finns i tabellen över [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).
* **Säkert och kompatibelt**: data överförs via https eller ExpressRoute. Med den globala tjänstens närvaro ser du till att dina data aldrig lämnar den geografiska gräns.
* **Hög prestanda**: upp till 1 GB/s data inläsnings hastighet i data Lake Storage gen1. Mer information finns i [Kopiera aktivitets prestanda](copy-activity-performance.md).

Den här artikeln visar hur du använder verktyget Data Factory Kopiera data för att _läsa in data från Amazon S3 till data Lake Storage gen1_. Du kan följa liknande steg för att kopiera data från andra typer av data lager.

> [!NOTE]
> Mer information finns i [Kopiera data till eller från data Lake Storage gen1 med Azure Data Factory](connector-azure-data-lake-store.md).

## <a name="prerequisites"></a>Förutsättningar

* Azure-prenumeration: om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt konto](https://azure.microsoft.com/free/) innan du börjar.
* Data Lake Storage Gen1 konto: om du inte har ett Data Lake Storage Gen1-konto kan du läsa anvisningarna i [skapa ett data Lake Storage gen1 konto](../data-lake-store/data-lake-store-get-started-portal.md#create-a-data-lake-storage-gen1-account).
* Amazon S3: den här artikeln visar hur du kopierar data från Amazon S3. Du kan använda andra data lager genom att följa liknande steg.

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. På den vänstra menyn väljer du **skapa en resurs**  >  **analys**  >  **Data Factory**:
   
   ![Valet Data Factory i fönstret Nytt](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. På sidan **ny data fabrik** anger du värden för de fält som visas i följande bild: 
      
   ![Sidan Ny datafabrik](./media/load-data-into-azure-data-lake-store//new-azure-data-factory.png)
 
    * **Namn**: Ange ett globalt unikt namn för din Azure Data Factory. Om du får felet "Data Factory name \" LoadADLSG1Demo \" är inte tillgängligt" anger du ett annat namn för data fabriken. Du kan till exempel använda namnet _**dittnamn**_**ADFTutorialDataFactory**. Försök att skapa data fabriken igen. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
    * **Prenumeration**: Välj din Azure-prenumeration där du vill skapa data fabriken. 
    * **Resurs grupp**: Välj en befintlig resurs grupp i list rutan eller Välj alternativet för att **Skapa nytt** och ange namnet på en resurs grupp. Mer information om resursgrupper finns i [Använda resursgrupper till att hantera Azure-resurser](../azure-resource-manager/management/overview.md).  
    * **Version**: Välj **V2**.
    * **Plats**: Välj plats för data fabriken. Endast platser som stöds visas i listrutan. De data lager som används av Data Factory kan finnas på andra platser och regioner. Dessa data lager omfattar Azure Data Lake Storage Gen1, Azure Storage, Azure SQL Database och så vidare.

3. Välj **Skapa**.
4. När du har skapat filen går du till din data fabrik. Du ser **Data Factory** start sida så som visas i följande bild: 
   
   ![Datafabrikens startsida](./media/load-data-into-azure-data-lake-store/data-factory-home-page.png)

   Välj panelen **Författare och övervakare** för att starta dataintegreringsprogrammet på en separat flik.

## <a name="load-data-into-data-lake-storage-gen1"></a>Läs in data i Data Lake Storage Gen1

1. På sidan **Kom igång** väljer du panelen **Kopiera data** för att starta kopiera data-verktyget: 

   ![Panel för verktyget Kopiera data](./media/load-data-into-azure-data-lake-store/copy-data-tool-tile.png)
2. På sidan **Egenskaper** anger du **CopyFromAmazonS3ToADLS** för fältet **uppgifts namn** och väljer **Nästa**:

    ![Sidan Egenskaper](./media/load-data-into-azure-data-lake-store/copy-data-tool-properties-page.png)
3. På sidan **käll data lager** klickar du på **+ Skapa ny anslutning**:

    ![Sidan Källdatalager](./media/load-data-into-azure-data-lake-store/source-data-store-page.png)
    
    Välj **Amazon S3** och välj **Fortsätt**
    
    ![Käll data lager S3-sida](./media/load-data-into-azure-data-lake-store/source-data-store-page-s3.png)
    
4. På sidan **Ange Amazon S3-anslutning** utför du följande steg: 
   1. Ange **åtkomst nyckelns ID-** värde.
   2. Ange nyckel värde för **hemlig åtkomst** .
   3. Välj **Slutför**.
   
      ![Skärm bild som visar fönstret ny länkad tjänst där du kan ange värden.](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account.png)
   
   4. En ny anslutning visas. Välj **Nästa**.
   
   ![Skärm bild som visar din nya anslutning.](./media/load-data-into-azure-data-lake-store/specify-amazon-s3-account-created.png)
   
5. På sidan **Välj indatafil eller mapp** bläddrar du till den mapp och fil du vill kopiera över. Välj mappen/filen, Välj **Välj** och välj sedan **Nästa**:

    ![Välj indatafil eller mapp](./media/load-data-into-azure-data-lake-store/choose-input-folder.png)

6. Välj kopierings beteende genom att välja alternativen **Kopiera filer rekursivt** och **binär kopia** (kopiera filer som är). Välj **Nästa**:

    ![Skärm bild visar sidan Välj indatafilen eller mappen där du kan välja Kopiera fil rekursivt och binär kopia.](./media/load-data-into-azure-data-lake-store/specify-binary-copy.png)
    
7. På sidan **mål data lager** klickar du på **+ Skapa ny anslutning** och välj sedan **Azure Data Lake Storage gen1** och välj **Fortsätt**:

    ![Sidan Måldatalager](./media/load-data-into-azure-data-lake-store/destination-data-storage-page.png)

8. Utför följande steg på sidan **ny länkad tjänst (Azure Data Lake Storage gen1)** : 

   1. Välj ditt Data Lake Storage Gen1 konto för **namnet på data Lake Stores kontot**.
   2. Ange **klienten** och välj Slutför.
   3. Välj **Nästa**.
   
   > [!IMPORTANT]
   > I den här genom gången använder du en hanterad identitet för Azure-resurser för att autentisera ditt Data Lake Storage Gen1-konto. Se till att ge MSI rätt behörigheter i Data Lake Storage Gen1 genom att följa [dessa anvisningar](connector-azure-data-lake-store.md#managed-identity).
   
   ![Ange Data Lake Storage Gen1 konto](./media/load-data-into-azure-data-lake-store/specify-adls.png)
9. På sidan **Välj utdatafil eller mapp** anger du **copyfroms3** som namn på utdata-mappen och väljer **Nästa**: 

    ![Skärm bild som visar den mappsökväg som du anger.](./media/load-data-into-azure-data-lake-store/specify-adls-path.png)

10. På sidan **Inställningar** väljer du **Nästa**:

    ![Sidan Inställningar](./media/load-data-into-azure-data-lake-store/copy-settings.png)
11. På sidan **Sammanfattning** granskar du inställningarna och väljer **Nästa**:

    ![Sammanfattningssida](./media/load-data-into-azure-data-lake-store/copy-summary.png)
12. På **sidan distribution** väljer du **övervakare** för att övervaka pipelinen (aktiviteten):

    ![Distributionssida](./media/load-data-into-azure-data-lake-store/deployment-page.png)
13. Observera att fliken **Övervaka** till vänster väljs automatiskt. I kolumnen **åtgärder** finns länkar för att Visa aktivitets körnings information och köra pipelinen igen:

    ![Övervaka pipelinekörningar](./media/load-data-into-azure-data-lake-store/monitor-pipeline-runs.png)
14. Om du vill visa aktivitets körningar som är associerade med pipeline-körningen väljer du länken **Visa aktivitet kör** i kolumnen **åtgärder** . Det finns bara en aktivitet (kopieringsaktiviteten) i pipelinen. Därför visas bara en post. Om du vill växla tillbaka till vyn pipeline-körningar väljer du länken **pipelines** överst. Om du vill uppdatera listan väljer du **Refresh** (Uppdatera). 

    ![Övervaka aktivitetskörningar](./media/load-data-into-azure-data-lake-store/monitor-activity-runs.png)

15. Om du vill övervaka körnings informationen för varje kopierings aktivitet väljer du länken **information** under **åtgärder** i vyn aktivitets övervakning. Du kan övervaka information om data volymen som kopieras från källan till mottagaren, data genom strömning, körnings steg med motsvarande varaktighet och använda konfigurationer:

    ![Övervaka körnings information för aktivitet](./media/load-data-into-azure-data-lake-store/monitor-activity-run-details.png)

16. Verifiera att data har kopierats till ditt Data Lake Storage Gen1-konto: 

    ![Verifiera Data Lake Storage Gen1 utdata](./media/load-data-into-azure-data-lake-store/adls-copy-result.png)

## <a name="next-steps"></a>Nästa steg

Gå vidare till följande artikel om du vill lära dig mer om Data Lake Storage Gen1-support: 

> [!div class="nextstepaction"]
>[Azure Data Lake Storage Gen1 koppling](connector-azure-data-lake-store.md)
