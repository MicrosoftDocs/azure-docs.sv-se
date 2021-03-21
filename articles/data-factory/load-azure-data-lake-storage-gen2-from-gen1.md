---
title: Kopiera data från Azure Data Lake Storage Gen1 till Gen2
description: Använd Azure Data Factory för att kopiera data från Azure Data Lake Storage Gen1 till Gen2
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 4a4bc0b55928fcaa40cd1cb03ca0522e611df476
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "101727727"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>Kopiera data från Azure Data Lake Storage Gen1 till Gen2 med Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Lake Storage Gen2 är en uppsättning funktioner som är avsedda för stor data analys som är inbyggd i [Azure Blob Storage](../storage/blobs/storage-blobs-introduction.md). Med den här lösningen kan du interagera med data med hjälp av både filsystem- och objektlagringsparadigm.

Om du för närvarande använder Azure Data Lake Storage Gen1 kan du utvärdera Azure Data Lake Storage Gen2 genom att kopiera data från Data Lake Storage Gen1 till Gen2 med hjälp av Azure Data Factory.

Azure Data Factory är en helt hanterad molnbaserad data integrerings tjänst. Du kan använda tjänsten för att fylla i Lake med data från en omfattande uppsättning lokala och molnbaserade data lager och spara tid när du bygger analys lösningarna. En lista över anslutningar som stöds finns i tabellen över [data lager som stöds](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory erbjuder en skalbar lösning för data förflyttning. På grund av den skalbara arkitekturen i Data Factory kan den mata in data i ett högt data flöde. Mer information finns i [Kopiera aktivitets prestanda](copy-activity-performance.md).

Den här artikeln visar hur du använder verktyget Data Factory kopiera data för att kopiera data från Azure Data Lake Storage Gen1 till Azure Data Lake Storage Gen2. Du kan följa liknande steg för att kopiera data från andra typer av data lager.

## <a name="prerequisites"></a>Förutsättningar

* En Azure-prenumeration. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* Azure Data Lake Storage Gen1 konto med data i det.
* Azure Storage konto med Data Lake Storage Gen2 aktiverat. [Skapa ett konto](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM)om du inte har ett lagrings konto.

## <a name="create-a-data-factory"></a>Skapa en datafabrik

1. På den vänstra menyn väljer du **skapa en resurs**  >  **data och analys**  >  **Data Factory**.
   
   ![Data Factory valet i det nya fönstret](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. På sidan **ny data fabrik** anger du värden för de fält som visas i följande bild: 
      
   ![Sidan ny data fabrik](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **Namn**: Ange ett globalt unikt namn för din Azure Data Factory. Om du får felet "Data Factory name \" LoadADLSDemo \" är inte tillgängligt" anger du ett annat namn för data fabriken. Använd till exempel namnet _**dittnamn**_**ADFTutorialDataFactory**. Skapa data fabriken igen. Se artikeln [Data Factory – namnregler](naming-rules.md) för namnregler för Data Factory-artefakter.
    * **Prenumeration**: Välj din Azure-prenumeration där du vill skapa data fabriken. 
    * **Resurs grupp**: Välj en befintlig resurs grupp i den nedrullningsbara listan. Du kan också välja alternativet **Skapa nytt** och ange namnet på en resurs grupp. Mer information om resursgrupper finns i [Använda resursgrupper för att hantera Azure-resurser](../azure-resource-manager/management/overview.md). 
    * **Version**: Välj **V2**.
    * **Plats**: Välj plats för data fabriken. Endast platser som stöds visas i listrutan. Datalagren som används av datafabriken kan finnas på andra platser och i andra regioner. 

3. Välj **Skapa**.
4. När du har skapat den går du till din data fabrik. Du ser **Data Factory** start sida så som visas i följande bild: 
   
   ![Datafabrikens startsida](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

5. Välj panelen **författare & Monitor** för att starta programmet för data integrering på en separat flik.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Läsa in data i Azure Data Lake Storage Gen2

1. På sidan **Kom igång** väljer du panelen **Kopiera data** för att starta verktyget kopiera data. 

   ![Verktygs panel för att kopiera data](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. På sidan **Egenskaper** anger du **CopyFromADLSGen1ToGen2** för fältet **uppgifts namn** . Välj **Nästa**.

    ![Sidan Egenskaper](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. På sidan **käll data lager** väljer du **+ Skapa ny anslutning**.

    ![Sidan Källdatalager](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
    
4. Välj **Azure Data Lake Storage Gen1** i galleriet med anslutningsappar och välj **Fortsätt**.
    
    ![Sidan käll data lager Azure Data Lake Storage Gen1](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
5. Följ dessa steg på sidan **ange Azure Data Lake Storage gen1 anslutning** :

   a. Välj Data Lake Storage Gen1 för konto namnet och ange eller verifiera **klienten**.
  
   b. Verifiera inställningarna genom att välja **Testa anslutning** . Välj sedan **Slutför**.
  
   c. Du ser att en ny anslutning har skapats. Välj **Nästa**.
   
   > [!IMPORTANT]
   > I den här genom gången använder du en hanterad identitet för Azure-resurser för att autentisera din Azure Data Lake Storage Gen1. Följ [dessa instruktioner](connector-azure-data-lake-store.md#managed-identity)om du vill ge den hanterade identiteten rätt behörigheter i Azure Data Lake Storage gen1.
   
   ![Ange Azure Data Lake Storage Gen1 konto](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
6. På sidan **Välj indatafil eller mapp** bläddrar du till mappen och filen som du vill kopiera. Markera mappen eller filen och välj **Välj**.

    ![Välj indatafil eller mapp](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

7. Ange kopierings beteendet genom att välja alternativen **Kopiera filer rekursivt** och **binär kopia** . Välj **Nästa**.

    ![Skärm bild visar sidan Välj indatafilen eller mappen där du kan välja Kopiera fil rekursivt och binär kopia.](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
8. På sidan **mål data lager** väljer du **+ Skapa ny anslutning**  >  **Azure Data Lake Storage Gen2**  >  **Fortsätt**.

    ![Sidan Måldatalager](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

9. Följ dessa steg på sidan **ange Azure Data Lake Storage Gen2 anslutning** :

   a. Välj ditt Data Lake Storage Gen2-kapabla konto i list rutan **lagrings konto namn** .
   
   b. Välj **Slutför** för att skapa anslutningen. Välj sedan **Nästa**.
   
   ![Ange Azure Data Lake Storage Gen2 konto](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

10. På sidan **Välj utdatafil eller mapp** anger du **copyfromadlsgen1** som namn på utdata-mappen och väljer **Nästa**. Data Factory skapar motsvarande Azure Data Lake Storage Gen2 fil system och undermappar under kopieringen om de inte redan finns.

    ![Skärm bild som visar den mappsökväg som du anger.](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

11. På sidan **Inställningar** väljer du **Nästa** för att använda standardinställningarna.

12. På sidan **Sammanfattning** granskar du inställningarna och väljer **Nästa**.

    ![Sammanfattningssida](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
13. På **sidan distribution** väljer du **övervakare** för att övervaka pipelinen.

    ![Distributionssida](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
14. Observera att fliken **Övervaka** till vänster väljs automatiskt. I kolumnen **Åtgärder** finns länkar som visar information om aktivitetskörningen och för att köra pipelinen igen.

    ![Övervaka pipelinekörningar](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

15. Om du vill visa aktivitets körningar som är associerade med pipeline-körningen väljer du länken **Visa aktivitet kör** i kolumnen **åtgärder** . Det finns bara en aktivitet (kopieringsaktiviteten) i pipelinen. Därför visas bara en post. Om du vill växla tillbaka till vyn pipeline-körningar väljer du länken **pipelines** överst. Om du vill uppdatera listan väljer du **Refresh** (Uppdatera). 

    ![Övervaka aktivitetskörningar](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

16. Om du vill övervaka körnings informationen för varje kopierings aktivitet väljer du länken **information** (glasögon-avbildning) under **åtgärder** i vyn aktivitets övervakning. Du kan övervaka information om data volymen som kopieras från källan till mottagaren, data genom strömning, körnings steg med motsvarande varaktighet och använda konfigurationer.

    ![Övervaka körnings information för aktivitet](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

17. Verifiera att data har kopierats till ditt Azure Data Lake Storage Gen2-konto.

## <a name="best-practices"></a>Bästa praxis

Information om hur du bedömer uppgradering från Azure Data Lake Storage Gen1 till Azure Data Lake Storage Gen2 i allmänhet finns i [uppgradera dina Big data Analytics-lösningar från Azure Data Lake Storage gen1 till Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md). I följande avsnitt beskrivs metod tips för hur du använder Data Factory för att uppgradera data från Data Lake Storage Gen1 till Data Lake Storage Gen2.

### <a name="data-partition-for-historical-data-copy"></a>Datapartition för historisk data kopia

- Om den totala data storleken i Data Lake Storage Gen1 är mindre än 30 TB och antalet filer är mindre än 1 000 000 kan du kopiera alla data i en enda kopierings aktivitet.
- Om du har en större mängd data som ska kopieras, eller om du vill ha flexibiliteten att hantera datamigrering i batchar och göra var och en av dem fullständigt inom en viss tidsram, partitionerar du data. Partitionering minskar också risken för oväntade problem.

Använd ett koncept bevis för att kontrol lera lösningen från slut punkt till slut punkt och testa kopierings data flödet i din miljö. Viktiga koncept för koncept bevis: 

1. Skapa en Data Factory pipeline med en enda kopierings aktivitet för att kopiera flera TBs av data från Data Lake Storage Gen1 till Data Lake Storage Gen2 för att få en bas linje för kopierings prestanda. Börja med [data integrerings enheter (DIUs)](copy-activity-performance-features.md#data-integration-units) som 128. 
2. Baserat på kopierings data flödet som du fick i steg 1, beräknar du den beräknade tiden som krävs för hela datamigreringen. 
3. Valfritt Skapa en kontroll tabell och definiera fil filtret för att partitionera de filer som ska migreras. Sättet att partitionera filerna är att: 

    - Partitionera efter mappnamn eller mappnamn med ett Wildcard-filter. Vi rekommenderar den här metoden.
    - Partition med en fils senaste ändrings tid.

### <a name="network-bandwidth-and-storage-io"></a>Nätverks bandbredd och lagring I/O 

Du kan styra samtidigheten för Data Factory kopierings jobb som läser data från Data Lake Storage Gen1 och skriver data till Data Lake Storage Gen2. På så sätt kan du hantera användningen på det lagrings-I/O för att undvika att det normala affärs arbetet på Data Lake Storage Gen1 under migreringen.

### <a name="permissions"></a>Behörigheter 

I Data Factory har [data Lake Storage gen1 Connector](connector-azure-data-lake-store.md) stöd för tjänstens huvud namn och hanterad identitet för Azure-resurs-autentiseringar. [Data Lake Storage Gen2-anslutningen](connector-azure-data-lake-storage.md) stöder konto nyckel, tjänstens huvud namn och hanterad identitet för Azure-resurs-autentiseringar. Om du vill att Data Factory kan navigera och kopiera alla filer eller åtkomst kontrol listor (ACL: er) du behöver ger du tillräckligt många behörigheter för det konto du ger åtkomst till, läser eller skriver alla filer och anger ACL: er om du väljer. Ge den en super-user-eller Owner-roll under migrerings perioden. 

### <a name="preserve-acls-from-data-lake-storage-gen1"></a>Behåll ACL: er från Data Lake Storage Gen1

Om du vill replikera ACL: er tillsammans med datafiler när du uppgraderar från Data Lake Storage Gen1 till Data Lake Storage Gen2, se [bevara ACL: er från data Lake Storage gen1](connector-azure-data-lake-storage.md#preserve-acls). 

### <a name="incremental-copy"></a>Stegvis kopia 

Du kan använda flera metoder för att läsa in endast nya eller uppdaterade filer från Data Lake Storage Gen1:

- Läs in nya eller uppdaterade filer med en tidspartitionad mapp eller ett fil namn. Ett exempel är/2019/05/13/*.
- Läs in nya eller uppdaterade filer från LastModifiedDate.
- Identifiera nya eller uppdaterade filer med valfritt verktyg eller någon annan lösning från tredje part. Skicka sedan fil-eller mappnamnet till Data Factory pipelinen via parametern eller en tabell eller fil. 

Den korrekta frekvensen för att utföra stegvis inläsning beror på det totala antalet filer i Azure Data Lake Storage Gen1 och volymen nya eller uppdaterade filer som ska läsas in varje gång. 

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Översikt över](copy-activity-overview.md) 
>  kopierings aktivitet [Azure Data Lake Storage gen1 koppling](connector-azure-data-lake-store.md) 
>  [Azure Data Lake Storage Gen2 koppling](connector-azure-data-lake-storage.md)