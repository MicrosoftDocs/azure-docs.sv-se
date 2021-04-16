---
title: Delta lake ETL med dataflöden
description: Den här självstudien innehåller stegvisa instruktioner för hur du använder dataflöden för att transformera och analysera data i Delta Lake
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2021
ms.date: 04/14/2021
ms.openlocfilehash: a0fb3488aec6761764e30b4cf8556b71e5edda15
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515418"
---
# <a name="transform-data-in-delta-lake-using-mapping-data-flows"></a>Transformera data i Delta Lake med mappning av dataflöden

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Om du inte har använt Azure Data Factory tidigare kan du läsa [Introduktion till Azure Data Factory](introduction.md).

I den här självstudien använder du dataflödesarbetsytan för att skapa dataflöden som gör att du kan analysera och transformera data i Azure Data Lake Storage (ADLS) Gen2 och lagra dem i Delta Lake.

## <a name="prerequisites"></a>Förutsättningar
* **Azure-prenumeration**. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* **Azure Storage-konto**. Du använder ADLS-lagring som källa *och* *mottagare för* datalager. Om du inte har ett lagringskonto finns det anvisningar om hur du skapar ett i [Skapa ett Azure Storage-konto](../storage/common/storage-account-create.md).

Filen som vi transformerar i den här självstudien MoviesDB.csv, som du hittar [här](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/moviesDB2.csv). Om du vill hämta filen från GitHub kopierar du innehållet till en valfri textredigerare för att spara lokalt som en CSV-fil. Information om hur du laddar upp filen till ditt [lagringskonto finns i Ladda upp blobar med Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md). Exemplen refererar till en container med namnet "sample-data".

## <a name="create-a-data-factory"></a>Skapa en datafabrik

I det här steget skapar du en datafabrik och öppnar Data Factory UX för att skapa en pipeline i datafabriken.

1. Öppna **Microsoft Edge** eller **Google Chrome**. För närvarande Data Factory användargränssnittet endast i webbläsarna Microsoft Edge och Google Chrome.
1. På den vänstra menyn väljer du **Skapa en**  >    >  **resursintegrerings-Data Factory**
1. På sidan **Ny datafabrik** går du till **Namn** och anger **ADFTutorialDataFactory**
1. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i.
1. Gör något av följande för **Resursgrupp**:

    a. Välj **Använd befintlig** och välj en befintlig resursgrupp i listrutan.

    b. Välj **Skapa ny** och ange namnet på en resursgrupp. 
         
    Mer information om resursgrupper finns i [Använda resursgrupper för att hantera Azure-resurser](../azure-resource-manager/management/overview.md). 
1. Under **Version** väljer du **V2**.
1. Under **Plats** väljer du en plats för datafabriken. Endast platser som stöds visas i listrutan. Datalager (till exempel Azure Storage och SQL Database) och beräkningar (till exempel Azure HDInsight) som används av datafabriken kan finnas i andra regioner.
1. Välj **Skapa**.
1. När skapandet är klart visas meddelandet i Meddelandecenter. Välj **Gå till resurs** för att gå till sidan Datafabrik.
1. Klicka på **Författare och övervakare** för att starta användargränssnittet för datafabriken på en separat flik.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Skapa en pipeline med en dataflödesaktivitet

I det här steget skapar du en pipeline som innehåller en dataflödesaktivitet.

1. På sidan **Nu sätter vi igång** väljer du **Skapa pipeline**.

   ![Skapa pipeline](./media/doc-common-process/get-started-page.png)

1. På fliken **Allmänt** för pipelinen anger du **DeltaLake** **som namn** på pipelinen.
1. I fönstret **Aktiviteter** expanderar du **flyttnings- och transformeringsspelionen.** Dra och släpp **Dataflöde** aktiviteten från fönstret till pipelinearbetsytan.

    ![Skärmbild som visar pipelinearbetsytan där du kan släppa Dataflöde aktiviteten.](media/tutorial-data-flow/activity1.png)
1. I **popup-Dataflöde** klickar du på **Skapa ny Dataflöde** namn på dataflödet **DeltaLake.** Klicka på Slutför när du är klar.

    ![Skärmbild som visar var du ger ditt dataflöde ett namn när du skapar ett nytt dataflöde.](media/tutorial-data-flow/activity2.png)
1. Dra skjutreglaget Dataflöde **felsökningsreglaget i det översta fältet på pipelinearbetsytan.** Felsökningsläge möjliggör interaktiv testning av transformeringslogik mot ett spark-livekluster. Dataflöde tar 5–7 minuter att värma upp och användarna rekommenderas att aktivera felsökning först om de planerar att Dataflöde utvecklingen. Mer information finns i [Felsökningsläge.](concepts-data-flow-debug-mode.md)

    ![Dataflöde aktivitet](media/tutorial-data-flow/dataflow1.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas&quot;></a>Skapa transformeringslogik på dataflödesarbetsytan

Du kommer att generera två dataflöden i den här självstudien. Det första dataflödet är en enkel källa till mottagare för att generera en ny Delta Lake från filmer CSV-fil från ovan. Slutligen skapar du flödesdesignen nedan för att uppdatera data i Delta Lake.

![Slutligt flöde](media/data-flow/data-flow-tutorial-6.png &quot;Slutligt flöde")

### <a name="tutorial-objectives"></a>Självstudiemål

1. Ta datauppsättningskällan MoviesCSV från ovan och skapa en ny Delta Lake från den
1. Skapa logiken för att uppdatera klassificeringar för 1988-filmer till "1"
1. Ta bort alla filmer från 1950
1. Infoga nya filmer för 2021 genom att duplicera filmer från 1960

### <a name="start-from-a-blank-data-flow-canvas"></a>Starta från en tom dataflödesarbetsyta

1. Klicka på källtransformering
1. Klicka på Ny bredvid datauppsättning i den nedre panelen 1 Skapa en ny länkad tjänst för ADLS Gen2
1. Välj Avgränsad text som datauppsättningstyp
1. Ge datauppsättningen namnet "MoviesCSV" 
1. Peka på MoviesCSV-filen som du laddade upp till lagringen ovan
1. Ange att den ska avgränsas med kommatecken och inkludera rubrik på den första raden 
1. Gå till fliken källprojektion och klicka på "Identifiera datatyper"
1. När du har angett din projektion kan du fortsätta 
1. Lägga till en sink-transformering
1. Delta är en infogade datauppsättningstyp. Du måste peka på ditt ADLS Gen2 lagringskonto.
   
   ![Infogade datauppsättningar](media/data-flow/data-flow-tutorial-5.png "Infogade datauppsättningar")

1. Välj ett mappnamn i lagringscontainern där du vill att ADF ska skapa Delta Lake
1. Gå tillbaka pipelinedesignern och klicka på Felsök för att köra pipelinen i felsökningsläge med bara den här dataflödesaktiviteten på arbetsytan. Detta genererar din nya Delta Lake i ADLS Gen2.
1. Från Fabriksresurser klickar du på nytt > dataflöde 
1. Använd MoviesCSV igen som källa och klicka på "Identifiera datatyper" igen
1. Lägga till en filtertransformering i källtransformeringen i diagrammet
1. Tillåt endast filmrader som matchar de tre år som du kommer att arbeta med, vilket blir 1950, 1988 och 1960
1. Uppdatera klassificeringarna för varje film från 1988 till "1" genom att nu lägga till en härledd kolumnomvandling i filtertransformeringen
1. I samma härledda kolumn skapar du filmer för 2021 genom att ta ett befintligt år och ändra året till 2021. Vi väljer 1960.
1. Så här ser dina tre härledda kolumner ut

   ![Härledd kolumn](media/data-flow/data-flow-tutorial-2.png "Härledd kolumn")
   
1. ```Update, insert, delete, and upsert``` -principer skapas i transformeringen Ändra rad. Lägg till en ändra radtransformering efter din härledda kolumn.
1. Dina ändringsradsprinciper bör se ut så här.

   ![Ändra rad](media/data-flow/data-flow-tutorial-3.png "Ändra rad")
   
1. Nu när du har angett rätt princip för varje ändringsradtyp kontrollerar du att rätt uppdateringsregler har angetts för omvandlingen av mottagaren

   ![Kanalmottagare](media/data-flow/data-flow-tutorial-4.png "Kanalmottagare")
   
1. Här använder vi Delta Lake-mottagaren till din ADLS Gen2 datasjö och tillåter infogningar, uppdateringar och borttagningar. 
1. Observera att nyckelkolumnerna är en sammansatt nyckel som består av kolumnen Film primärnyckel och år. Det beror på att vi har skapat falska 2021-filmer genom att duplicera de 1960 raderna. På så sätt undviker du kollisioner när du söker efter befintliga rader genom att tillhandahålla unikhet.

### <a name="download-completed-sample"></a>Ladda ned slutfört exempel
[Här är en exempellösning för Delta-pipelinen med ett dataflöde för att uppdatera/ta bort rader i lake:](https://github.com/kromerm/adfdataflowdocs/blob/master/sampledata/DeltaPipeline.zip)

## <a name="next-steps"></a>Nästa steg

Läs mer om [dataflödesuttrycksspråket](data-flow-expression-functions.md).
