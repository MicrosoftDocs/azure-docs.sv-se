---
title: Delta Lake ETL med data flöden
description: Den här självstudien innehåller stegvisa instruktioner för att använda data flöden för att transformera och analysera data i delta Lake
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 02/09/2021
ms.openlocfilehash: cb8d44353e826df14ed3baab2c4ca66ffed4a569
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100418111"
---
# <a name="transform-data-in-delta-lake-using-mapping-data-flows"></a>Transformera data i delta Lake med hjälp av att mappa data flöden

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Om du inte har använt Azure Data Factory tidigare kan du läsa [Introduktion till Azure Data Factory](introduction.md).

I den här självstudien använder du data flödes arbets ytan för att skapa data flöden som gör att du kan analysera och transformera data i Azure Data Lake Storage (ADLS) Gen2 och lagra dem i delta Lake.

## <a name="prerequisites"></a>Förutsättningar
* **Azure-prenumeration**. Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* **Azure Storage-konto**. Du använder ADLS-lagring som *käll* -och *mottagar* data lager. Om du inte har ett lagringskonto finns det anvisningar om hur du skapar ett i [Skapa ett Azure Storage-konto](../storage/common/storage-account-create.md).

Filen som vi transformerar i den här självstudien är MoviesDB.csv, som du hittar [här](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/moviesDB2.csv). Om du vill hämta filen från GitHub kopierar du innehållet till en text redigerare som du väljer att spara lokalt som en CSV-fil. Om du vill överföra filen till ditt lagrings konto, se [Ladda upp blobar med Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md). Exemplen refererar till en behållare med namnet "Sample-data".

## <a name="create-a-data-factory"></a>Skapa en datafabrik

I det här steget skapar du en data fabrik och öppnar Data Factory UX för att skapa en pipeline i data fabriken.

1. Öppna **Microsoft Edge** eller **Google Chrome**. Data Factory-gränssnittet stöds för närvarande bara i webbläsarna Microsoft Edge och Google Chrome.
1. På den vänstra menyn väljer du **skapa en resurs**  >  **integration**  >  **Data Factory**
1. På sidan **ny data fabrik** , under **namn**, anger du **ADFTutorialDataFactory**
1. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i.
1. Gör något av följande för **Resursgrupp**:

    a. Välj **Använd befintlig** och välj en befintlig resurs grupp i den nedrullningsbara listan.

    b. Välj **Skapa ny** och ange namnet på en resurs grupp. 
         
    Mer information om resursgrupper finns i [Använda resursgrupper för att hantera Azure-resurser](../azure-resource-manager/management/overview.md). 
1. Under **Version** väljer du **V2**.
1. Under **Plats** väljer du en plats för datafabriken. Endast platser som stöds visas i listrutan. Data lager (till exempel Azure Storage och SQL Database) och beräkningarna (till exempel Azure HDInsight) som används av data fabriken kan finnas i andra regioner.
1. Välj **Skapa**.
1. När du har skapat meddelandet visas meddelandet i Notifications Center. Välj **gå till resurs** för att navigera till sidan data fabrik.
1. Klicka på **Författare och övervakare** för att starta användargränssnittet för datafabriken på en separat flik.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Skapa en pipeline med en data flödes aktivitet

I det här steget ska du skapa en pipeline som innehåller en data flödes aktivitet.

1. På sidan **Nu sätter vi igång** väljer du **Skapa pipeline**.

   ![Skapa pipeline](./media/doc-common-process/get-started-page.png)

1. På fliken **Allmänt** för pipelinen anger du **DeltaLake** som **namn** på pipelinen.
1. I det fabriksinstallerade övre fältet drar du skjutreglaget för **data flödes fel sökning** på. Fel söknings läge möjliggör interaktiv testning av omvandlings logik mot ett aktivt Spark-kluster. Data flödes kluster tar 5-7 minuter att värma upp och användare rekommenderas att aktivera fel sökning först om de planerar att utföra data flödes utveckling. Mer information finns i [fel söknings läge](concepts-data-flow-debug-mode.md).

    ![Data flödes aktivitet](media/tutorial-data-flow/dataflow1.png)
1. I fönstret **aktiviteter** expanderar du **förflyttnings-och omvandlings** draget. Dra och släpp **data flödes** aktiviteten från fönstret till pipeline-arbetsytan.

    ![Skärm bild som visar pipeline-arbetsytan där du kan släppa data flödes aktiviteten.](media/tutorial-data-flow/activity1.png)
1. I popup-fönstret för **att lägga till data flöde** väljer du **Skapa nytt data flöde** och namnger sedan **DeltaLake** för data flödet. Klicka på Slutför när du är klar.

    ![Skärm bild som visar var du namnger ditt data flöde när du skapar ett nytt data flöde.](media/tutorial-data-flow/activity2.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas&quot;></a>Bygg omvandlings logik i data flödets arbets yta

Du kommer att generera två data flöden i den här självstudien. Fist-dataflödet är en enkel källa som mottagare kan generera en ny delta Lake från CSV-filen för filmer från ovan. Slutligen skapar du den här flödes designen nedan för att uppdatera data i delta Lake.

![Slutligt flöde](media/data-flow/data-flow-tutorial-6.png &quot;Slutligt flöde")

### <a name="tutorial-objectives"></a>Kurs mål

1. Ta den MoviesCSV data uppsättnings källan från ovan, form a New Lake från IT
1. Bygg logiken till uppdaterade klassificeringar för 1988-filmer till ' 1 '
1. Ta bort alla filmer från 1950
1. Infoga nya filmer för 2021 genom att duplicera filmerna från 1960

### <a name="start-from-a-blank-data-flow-canvas"></a>Börja med en tom arbets yta för data flöde

1. Klicka på käll omvandlingen
1. Klicka på ny bredvid data uppsättning i den nedre panelen 1 Skapa en ny länkad tjänst för ADLS Gen2
1. Välj avgränsad text för data uppsättnings typen
1. Namnge data uppsättningen "MoviesCSV" 
1. Peka på den MoviesCSV-fil som du överförde till Storage ovan
1. Ange att den ska vara kommaavgränsad och Inkludera sidhuvud på första raden 
1. Gå till fliken käll projektion och klicka på "identifiera data typer"
1. När du har angett din projektion kan du fortsätta 
1. Lägg till en Sink-transformering
1. Delta är en infogad data uppsättnings typ. Du måste peka på ditt ADLS Gen2 lagrings konto.
   
   ![Infogad data uppsättning](media/data-flow/data-flow-tutorial-5.png "Infogad data uppsättning")

1. Välj ett mappnamn i lagrings behållaren där du vill att ADF ska skapa delta Lake
1. Gå tillbaka till pipeline-designern och klicka på Felsök för att köra pipelinen i fel söknings läge med bara denna data flödes aktivitet på arbets ytan. Detta genererar nya delta Lake i ADLS Gen2.
1. Från fabriks resurser klickar du på nytt > data flöde 
1. Använd MoviesCSV igen som källa och klicka på "identifiera data typer" igen
1. Lägg till en filter omvandling till din käll omvandling i grafen
1. Tillåt endast att film rader som matchar de tre åren som du kommer att arbeta med är 1950, 1988 och 1960
1. Uppdatera klassificeringar för varje 1988-film till ' 1 ' genom att nu lägga till en härledd kolumn omvandling till filter omvandlingen
1. I samma härledda kolumn skapar du filmer för 2021 genom att ta ett befintligt år och ändra året till 2021. Låt oss välja 1960.
1. Detta är vad dina tre härledda kolumner kommer att se ut

   ![Härledd kolumn](media/data-flow/data-flow-tutorial-2.png "Härledd kolumn")
   
1. ```Update, insert, delete, and upsert``` principer skapas i transformeringen Alter Row. Lägg till en Alter Row-omvandling efter den härledda kolumnen.
1. Dina Alter Row-principer bör se ut så här.

   ![Ändra rad](media/data-flow/data-flow-tutorial-3.png "Ändra rad")
   
1. Nu när du har angett rätt princip för varje Alter Row-typ kontrollerar du att rätt uppdaterings regler har angetts för Sink-omvandlingen

   ![Kanalmottagare](media/data-flow/data-flow-tutorial-4.png "Kanalmottagare")
   
1. Här använder vi delta Lake-Sink till ADLS Gen2 data Lake och tillåter infogning, uppdateringar och borttagningar. 
1. Observera att nyckel kolumnerna är en sammansatt nyckel som består av kolumnerna för primär nyckel kolumn och år. Det beror på att vi har skapat falska 2021-filmer genom att duplicera 1960-raderna. Detta förhindrar kollisioner när du söker efter befintliga rader genom att tillhandahålla unikhet.

### <a name="download-completed-sample"></a>Hämta slutfört exempel
[Här är en exempel lösning för delta-pipeline med ett data flöde för uppdatering/borttagning av rader i sjön:](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/DeltaPipeline.zip)

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [data flödets uttrycks språk](data-flow-expression-functions.md).
