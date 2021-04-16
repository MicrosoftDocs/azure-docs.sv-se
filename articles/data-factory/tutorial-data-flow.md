---
title: Transformera data med hjälp av ett mappningsdataflöde
description: Den här självstudien innehåller stegvisa instruktioner för att använda Azure Data Factory för att transformera data med mappning av dataflöde
author: dcstwh
ms.author: weetok
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2021
ms.openlocfilehash: 0842dad0e0ea6f9987727e8abf3d0eaf8a59e821
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517526"
---
# <a name="transform-data-using-mapping-data-flows"></a>Omvandla data med Mappa dataflöden

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Om du inte har använt Azure Data Factory tidigare kan du läsa [Introduktion till Azure Data Factory](introduction.md).

I den här självstudien använder du Azure Data Factory-användargränssnittet (UX) för att skapa en pipeline som kopierar och transformerar data från en Azure Data Lake Storage(ADLS) Gen2-källa till en ADLS Gen2-mottagare med mappning av dataflöde. Konfigurationsmönstret i den här självstudien kan utökas när du transformerar data med dataflödesmappning

 >[!NOTE]
   >Den här självstudien är avsedd för att mappa dataflöden i allmänhet. Dataflöden är tillgängliga både i Azure Data Factory Synapse-pipelines. Om du inte har använt dataflöden i Azure Synapse pipelines är du välkommen att [Dataflöde med Azure Synapse pipelines](../synapse-analytics/concepts-data-flow-overview.md) 
   
I den här självstudien gör du följande:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa en pipeline med en Dataflöde aktivitet.
> * Skapa ett mappningsdataflöde med fyra transformningar.
> * Testkör pipelinen.
> * Övervaka en Dataflöde aktivitet

## <a name="prerequisites"></a>Förutsättningar
* **Azure-prenumeration**. Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* **Azure Storage-konto**. Du använder ADLS-lagring som källa *och* *mottagare för* datalager. Om du inte har ett lagringskonto finns det anvisningar om hur du skapar ett i [Skapa ett Azure Storage-konto](../storage/common/storage-account-create.md).

Filen som vi transformerar i den här självstudien MoviesDB.csv, som du hittar [här.](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv) Om du vill hämta filen från GitHub kopierar du innehållet till en valfri textredigerare för att spara lokalt som en CSV-fil. Information om hur du laddar upp filen till ditt [lagringskonto finns](../storage/blobs/storage-quickstart-blobs-portal.md)i Ladda upp blobar med Azure Portal . Exemplen refererar till en container med namnet "sample-data".

## <a name="create-a-data-factory"></a>Skapa en datafabrik

I det här steget skapar du en datafabrik och öppnar Data Factory UX för att skapa en pipeline i datafabriken.

1. Öppna **Microsoft Edge** eller **Google Chrome**. För närvarande Data Factory användargränssnittet endast i webbläsarna Microsoft Edge och Google Chrome.
2. På den vänstra menyn väljer du **Skapa en resursintegrering**  >    >  **Data Factory**:

   ![Valet Data Factory i fönstret Nytt](./media/doc-common-process/new-azure-data-factory-menu.png)

3. I fönstret **Ny datafabrik**, under **Namn** anger du **ADFTutorialDataFactory**.

   Namnet på Azure-datafabriken måste *vara globalt unikt.* Ange ett annat namn för datafabriken om du får ett felmeddelande om namnvärdet. (till exempel dittnamnADFTutorialDataFactory). Se artikeln [Namnregler för Data Factory](naming-rules.md) för namnregler för Data Factory-artefakter.

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Nytt datafabriksfelmeddelande för dubblettnamn.":::
4. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i.
5. Gör något av följande för **Resursgrupp**:

    a. Välj **Använd befintlig** och välj en befintlig resursgrupp i listrutan.

    b. Välj **Skapa ny** och ange namnet på en resursgrupp. 
         
    Mer information om resursgrupper finns i [Använda resursgrupper för att hantera Azure-resurser](../azure-resource-manager/management/overview.md). 
6. Under **Version** väljer du **V2**.
7. Under **Plats** väljer du en plats för datafabriken. Endast platser som stöds visas i listrutan. Datalager (till exempel Azure Storage och SQL Database) och beräkningar (till exempel Azure HDInsight) som används av datafabriken kan finnas i andra regioner.
8. Välj **Skapa**.
9. När skapandet är klart visas meddelandet i Meddelandecenter. Välj **Gå till resurs** för att gå till sidan Datafabrik.
10. Klicka på **Författare och övervakare** för att starta användargränssnittet för datafabriken på en separat flik.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Skapa en pipeline med en Dataflöde aktivitet

I det här steget skapar du en pipeline som innehåller en Dataflöde aktivitet.

1. På sidan **Nu sätter vi igång** väljer du **Skapa pipeline**.

   ![Skapa pipeline](./media/doc-common-process/get-started-page.png)

1. På fliken **Allmänt** för pipelinen anger du **TransformMovies** **som Namn** på pipelinen.
1. I fönstret **Aktiviteter** expanderar du **draget Flytta och** Transformera. Dra och släpp **Dataflöde** aktiviteten från fönstret till pipelinearbetsytan.

    ![Skärmbild som visar pipelinearbetsytan där du kan släppa Dataflöde aktivitet.](media/tutorial-data-flow/activity1.png)
1. I **popup-Dataflöde** väljer du **Skapa** ny Dataflöde namn på dataflödet **TransformMovies**. Klicka på Slutför när du är klar.

    ![Skärmbild som visar var du ger ditt dataflöde ett namn när du skapar ett nytt dataflöde.](media/tutorial-data-flow/activity2.png)
1. I det översta fältet på pipelinearbetsytan drar du Dataflöde **skjutreglaget** för felsökning. Felsökningsläge möjliggör interaktiv testning av transformeringslogik mot ett spark-livekluster. Dataflöde tar 5–7 minuter att värma upp och användarna rekommenderas att aktivera felsökning först om de planerar att Dataflöde utvecklingen. Mer information finns i [Felsökningsläge.](concepts-data-flow-debug-mode.md)

    ![Dataflöde aktivitet](media/tutorial-data-flow/dataflow1.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Skapa transformeringslogik på dataflödesarbetsytan

När du Dataflöde dataflödesarbetsytan skickas du automatiskt till dataflödets arbetsyta. I det här steget skapar du ett dataflöde som tar moviesDB.csv i ADLS-lagring och aggregerar genomsnittsklassificeringen för komiker från 1910 till 2000. Sedan skriver du tillbaka den här filen till ADLS-lagringen.

1. Lägg till en källa på dataflödesarbetsytan genom att klicka på **rutan Lägg till** källa.

    ![Skärmbild som visar rutan Lägg till källa.](media/tutorial-data-flow/dataflow2.png)
1. Ge källan namnet **MoviesDB**. Klicka på **Ny för** att skapa en ny källdatauppsättning.

    ![Skärmbild som visar var du väljer Ny när du har namnnamnet för din källa.](media/tutorial-data-flow/dataflow3.png)
1. Välj **Azure Data Lake Storage Gen2**. Klicka på Fortsätt.

    ![Skärmbild som visar Azure Data Lake Storage Gen2 panelen.](media/tutorial-data-flow/dataset1.png)
1. Välj **DelimitedText**. Klicka på Fortsätt.

    ![Skärmbild som visar panelen DelimitedText.](media/tutorial-data-flow/dataset2.png)
1. Ge datauppsättningen **namnet MoviesDB**. I listrutan länkad tjänst väljer du **Nytt.**

    ![Skärmbild som visar listrutan Länkad tjänst.](media/tutorial-data-flow/dataset3.png)
1. På skärmen för skapande av länkad tjänst ger du den länkade ADLS Gen2-tjänsten **namnet ADLSGen2** och anger din autentiseringsmetod. Ange sedan autentiseringsuppgifterna för anslutningen. I den här självstudien använder vi kontonyckeln för att ansluta till vårt lagringskonto. Du kan klicka på **Testa anslutning för** att verifiera att dina autentiseringsuppgifter har angetts korrekt. Klicka på Skapa när du är klar.

    ![Länkad tjänst](media/tutorial-data-flow/ls1.png)
1. När du är tillbaka på skärmen för att skapa datauppsättningen anger du var filen finns under **fältet Filsökväg.** I den här självstudien finns moviesDB.csv filen i containern sample-data. Eftersom filen har rubriker markerar du **Första raden som rubrik.** Välj **Från anslutning/arkiv för** att importera huvudschemat direkt från filen i lagringen. Klicka på OK när du är klar.

    ![Datauppsättningar](media/tutorial-data-flow/dataset4.png)
1. Om felsökningsklustret har startat går du till **fliken Dataförhandsgranskning** i källtransformeringen och klickar på **Uppdatera** för att hämta en ögonblicksbild av data. Du kan använda dataförhandsvisning för att verifiera att din transformering är korrekt konfigurerad.

    ![Skärmbild som visar var du kan förhandsgranska dina data för att verifiera att omvandlingen är korrekt konfigurerad.](media/tutorial-data-flow/dataflow4.png)
1. Bredvid källnoden på dataflödesarbetsytan klickar du på plusikonen för att lägga till en ny transformering. Den första omvandlingen du lägger till är ett **Filter**.

    ![Dataflöde Canvas](media/tutorial-data-flow/dataflow5.png)
1. Ge filtertransformeringen **namnet FilterYears**. Klicka på uttrycksrutan bredvid Filtrera **på för att** öppna uttrycksverktyget. Här anger du filtreringsvillkoret.

    ![Skärmbild som visar rutan Filtrera efter uttryck.](media/tutorial-data-flow/filter1.png)
1. Med uttrycksverktyget för dataflöden kan du interaktivt skapa uttryck som ska användas i olika transformationer. Uttryck kan innehålla inbyggda funktioner, kolumner från indataschemat och användardefinierade parametrar. Mer information om hur du skapar uttryck finns i [Dataflöde expression builder](concepts-data-flow-expression-builder.md).

    I den här självstudien vill du filtrera filmer med genrer som kom ut mellan åren 1910 och 2000. Eftersom year för närvarande är en sträng måste du konvertera den till ett heltal med hjälp av ```toInteger()``` funktionen . Använd operatorerna större än eller lika med (>=) och mindre än eller lika med (<=) för att jämföra med literalårsvärdena 1910 och 200-. Sammanför dessa uttryck med operatorn och (&&). Uttrycket kommer ut som:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    Om du vill ta reda på vilka filmer som är comedies kan du använda funktionen för att hitta mönstret ```rlike()``` "Gör" i kolumngenreerna. Union the rlike expression with the year comparison to get:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    Om du har ett aktivt felsökningskluster kan  du verifiera logiken genom att klicka på Uppdatera för att se utdata för uttryck jämfört med de indata som används. Det finns fler än ett rätt svar på hur du kan åstadkomma den här logiken med hjälp av språket för dataflödesuttryck.

    ![Filtrera](media/tutorial-data-flow/filter2.png)

    Klicka **på Spara och** slutför när du är klar med uttrycket.

1. Hämta en **dataförhandsgranskning** för att verifiera att filtret fungerar korrekt.

    ![Skärmbild som visar den dataförhandsvisning som du hämtade.](media/tutorial-data-flow/filter3.png)
1. Nästa transformering som du lägger till är en **aggregeringsomvandling** under **Schemamodifierare**.

    ![Skärmbild som visar aggregatschemamodifieraren.](media/tutorial-data-flow/agg1.png)
1. Ge **aggregeringsomvandlingen namnet AggregateComedyRatings.** På fliken **Gruppera efter** väljer du **år i** listrutan för att gruppera sammansättningarna efter det år som filmen kom ut.

    ![Skärmbild som visar alternativet år på fliken Gruppera efter under Aggregera inställningar.](media/tutorial-data-flow/agg2.png)
1. Gå till **fliken Aggregeringar.** I den vänstra textrutan ger du aggregeringskolumnen **namnet AverageComedyRating**. Klicka på rutan för det högra uttrycket för att ange mängduttrycket via uttrycksverktyget.

    ![Skärmbild som visar alternativet år på fliken Aggregeringar under Aggregera inställningar.](media/tutorial-data-flow/agg3.png)
1. Använd mängdfunktionen för **att få medelvärdet** av ```avg()``` kolumnklassificering. Eftersom **Klassificering** är en sträng ```avg()``` och tar in numeriska indata måste vi konvertera värdet till ett tal via funktionen ```toInteger()``` . Det här uttrycket ser ut så här:

    ```avg(toInteger(Rating))```

    Klicka **på Spara och slutför när** du är klar.

    ![Skärmbild som visar det sparade uttrycket.](media/tutorial-data-flow/agg4.png)
1. Gå till fliken **Dataförhandsgranskning** för att visa transformeringsutdata. Observera att endast två kolumner finns där, **year** och **AverageComedyRating**.

    ![Aggregera](media/tutorial-data-flow/agg3.png)
1. Därefter vill du lägga till en **sink-transformering** under **Mål**.

    ![Skärmbild som visar var du lägger till en mottagartransformering under Mål.](media/tutorial-data-flow/sink1.png)
1. Ge mottagaren namnet **Sink**. Klicka **på Ny** för att skapa din datauppsättning för mottagare.

    ![Skärmbild som visar var du kan namnge mottagaren och skapa en ny datauppsättning för mottagare.](media/tutorial-data-flow/sink2.png)
1. Välj **Azure Data Lake Storage Gen2**. Klicka på Fortsätt.

    ![Skärmbild som visar Azure Data Lake Storage Gen2 som du kan välja.](media/tutorial-data-flow/dataset1.png)
1. Välj **DelimitedText**. Klicka på Fortsätt.

    ![Datamängd](media/tutorial-data-flow/dataset2.png)
1. Namnge din datauppsättning för **mottagare FilmerSink**. För länkad tjänst väljer du den länkade ADLS gen2-tjänst som du skapade i steg 6. Ange en utdatamapp att skriva dina data till. I den här självstudien skriver vi till mappen "output" i containern "sample-data". Mappen behöver inte finnas i förväg och kan skapas dynamiskt. Ange **Första raden som rubrik som** true och välj **Ingen** för Importera **schema**. Klicka på Slutför.

    ![Kanalmottagare](media/tutorial-data-flow/sink3.png)

Nu har du skapat ditt dataflöde. Du är redo att köra den i din pipeline.

## <a name="running-and-monitoring-the-data-flow"></a>Köra och övervaka Dataflöde

Du kan felsöka en pipeline innan du publicerar den. I det här steget ska du utlösa en felsökningskörning av dataflödespipelinen. Även om dataförhandsvisningen inte skriver data skriver en felsökningskörning data till mottagarmålet.

1. Gå till pipelinearbetsytan. Klicka **på Felsök** för att utlösa en felsökningskörning.

    ![Skärmbild som visar pipelinearbetsytan med Felsökning markerat.](media/tutorial-data-flow/pipeline1.png)
1. Pipeline-felsökning av Dataflöde aktiviteter använder det aktiva felsökningsklustret, men det tar ändå minst en minut att initiera. Du kan följa förloppet via **fliken Utdata.** När körningen är lyckad klickar du på glasögonikonen för att öppna övervakningsfönstret.

    ![Pipeline](media/tutorial-data-flow/pipeline2.png)
1. I övervakningsfönstret kan du se antalet rader och den tid som använts i varje transformeringssteg.

    ![Skärmbild som visar övervakningsfönstret där du kan se antalet rader och den tid som spenderas i varje transformeringssteg.](media/tutorial-data-flow/pipeline3.png)
1. Klicka på en transformering för att få detaljerad information om kolumnerna och partitionering av data.

    ![Övervakning](media/tutorial-data-flow/pipeline4.png)

Om du har följt den här självstudien korrekt bör du ha skrivit 83 rader och 2 kolumner i mappen för mottagare. Du kan kontrollera att data är korrekta genom att kontrollera bloblagringen.

## <a name="next-steps"></a>Nästa steg

Pipelinen i den här självstudien kör ett dataflöde som aggregerar det genomsnittliga omdömet för comedies från 1910 till 2000 och skriver data till ADLS. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa en pipeline med en Dataflöde aktivitet.
> * Skapa ett mappningsdataflöde med fyra transformationer.
> * Testkör pipelinen.
> * Övervaka en Dataflöde aktivitet

Läs mer om [dataflödesuttrycksspråket](data-flow-expression-functions.md).