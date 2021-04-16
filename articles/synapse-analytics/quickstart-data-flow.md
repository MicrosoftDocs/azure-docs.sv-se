---
title: 'Snabbstart: Transformera data med hjälp av ett mappningsdataflöde'
description: Den här självstudien innehåller stegvisa instruktioner för att använda Azure Synapse Analytics för att transformera data med mappning av dataflöde.
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/03/2020
ms.openlocfilehash: 37696d2f4054e46125b39f3d5efa794ce54f94b5
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2021
ms.locfileid: "107567731"
---
# <a name="quickstart-transform-data-using-mapping-data-flows"></a>Snabbstart: Transformera data med mappning av dataflöden

I den här snabbstarten använder du Azure Synapse Analytics för att skapa en pipeline som transformerar data från en Azure Data Lake Storage Gen2-källa (ADLS Gen2) till en ADLS Gen2 mottagare med hjälp av mappning av dataflöde. Konfigurationsmönstret i den här snabbstarten kan utökas när du transformerar data med hjälp av mappning av dataflöde

I den här snabbstarten gör du följande:

> [!div class="checklist"]
> * Skapa en pipeline med en Dataflöde aktivitet i Azure Synapse Analytics.
> * Skapa ett mappningsdataflöde med fyra transformningar.
> * Testkör pipelinen.
> * Övervaka en Dataflöde aktivitet

## <a name="prerequisites"></a>Förutsättningar

* **Azure-prenumeration**: Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* **Azure Synapse arbetsyta:** Skapa en Synapse-arbetsyta med hjälp Azure Portal följa anvisningarna i [Snabbstart: Skapa en Synapse-arbetsyta](quickstart-create-workspace.md).
* **Azure Storage-konto:** Du använder ADLS-lagring som *källa och* *mottagare* för datalager. Om du inte har ett lagringskonto finns det anvisningar om hur du skapar ett i [Skapa ett Azure Storage-konto](../storage/common/storage-account-create.md).

    Filen som vi transformerar i den här självstudien MoviesDB.csv, som du hittar [här](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv). Om du vill hämta filen från GitHub kopierar du innehållet till en valfri textredigerare för att spara lokalt som en CSV-fil. Information om hur du laddar upp filen till ditt [lagringskonto finns i Ladda upp blobar med Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md). Exemplen refererar till en container med namnet "sample-data".

### <a name="navigate-to-the-synapse-studio"></a>Gå till Synapse Studio

När din Azure Synapse har skapats har du två sätt att öppna Synapse Studio:

* Öppna Synapse-arbetsytan i [Azure Portal](https://ms.portal.azure.com/#home). Välj **Öppna** på kortet Öppna Synapse Studio under Komma igång.
* Öppna [Azure Synapse Analytics](https://web.azuresynapse.net/) och logga in på din arbetsyta.

I den här snabbstarten använder vi arbetsytan med namnet "adftest2020" som exempel. Du kommer automatiskt till Synapse Studio startsidan.

![Synapse Studio startsida](media/doc-common-process/synapse-studio-home.png)

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Skapa en pipeline med en Dataflöde aktivitet

En pipeline innehåller det logiska flödet för en körning av en uppsättning aktiviteter. I det här avsnittet skapar du en pipeline som innehåller en Dataflöde aktivitet.

1. Gå till **fliken Integrera.** Välj på plusikonen bredvid rubriken för pipelines och välj Pipeline.

   ![Skapa en ny pipeline](media/doc-common-process/new-pipeline.png)

1. På sidan **Egenskaperinställningar** i pipelinen anger du **TransformMovies** som **Namn.**

1. Dra *Dataflöde till* *pipelinearbetsytan* under **Flytta och transformera** i fönstret Aktiviteter.

1. På **popup-sidan Lägga till** dataflöde väljer du **Skapa nytt dataflöde**  ->  **Dataflöde.** Klicka **på OK** när du är klar.

   ![Skapa ett dataflöde](media/quickstart-data-flow/new-data-flow.png)

1. Ge dataflödet namnet **TransformMovies** på **sidan** Egenskaper.

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Skapa transformeringslogik på dataflödesarbetsytan

När du Dataflöde dataflödesarbetsytan skickas du automatiskt till dataflödets arbetsyta. I det här steget skapar du ett dataflöde som tar MoviesDB.csv i ADLS-lagring och aggregerar genomsnittsklassificeringen för komiker från 1910 till 2000. Sedan skriver du tillbaka den här filen till ADLS-lagringen.

1. Ovanför dataflödets arbetsyta drar du **skjutreglaget Dataflödesfelsökning** på. Felsökningsläge möjliggör interaktiv testning av transformeringslogik mot ett spark-livekluster. Dataflöde tar 5–7 minuter att värma upp och användarna rekommenderas att aktivera felsökning först om de planerar att Dataflöde utveckling. Mer information finns i [Felsökningsläge.](../data-factory/concepts-data-flow-debug-mode.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)

    ![Dra i felsökningen](media/quickstart-data-flow/debug-on.png)

1. På dataflödesarbetsytan lägger du till en källa genom att klicka på **rutan Lägg till** källa.

1. Ge källan namnet **MoviesDB.** Klicka på **Ny för** att skapa en ny källdatauppsättning.

    ![Skapa en ny källdatauppsättning](media/quickstart-data-flow/new-source-dataset.png)

1. Välj **Azure Data Lake Storage Gen2**. Klicka på Fortsätt.

    ![Välj Azure Data Lake Storage Gen2](media/quickstart-data-flow/select-source-dataset.png)

1. Välj **DelimitedText**. Klicka på Fortsätt.

1. Ge datauppsättningen **namnet MoviesDB**. I listrutan länkad tjänst väljer du **Nytt.**

1. På skärmen för skapande av länkad tjänst ger du ADLS Gen2 länkade tjänsten **ADLSGen2** och anger din autentiseringsmetod. Ange sedan autentiseringsuppgifterna för anslutningen. I den här snabbstarten använder vi kontonyckeln för att ansluta till vårt lagringskonto. Du kan klicka på **Testa anslutning för** att verifiera att dina autentiseringsuppgifter har angetts korrekt. Klicka **på Skapa** när du är klar.

    ![Skapa en länkad källtjänst](media/quickstart-data-flow/adls-gen2-linked-service.png)

1. När du är tillbaka på skärmen för att skapa datauppsättningen går du till **fältet** Filsökväg och anger var filen finns. I den här snabbstarten finns filen "MoviesDB.csv" i containern "sample-data". Eftersom filen har rubriker markerar du Första **raden som rubrik**. Välj **Från anslutning/lagring för** att importera rubrikschemat direkt från filen i lagringen. Klicka **på OK** när du är klar.

    ![Inställningar för källdatauppsättningen](media/quickstart-data-flow/source-dataset-properties.png)

1. Om felsökningsklustret har startat går du till fliken **Dataförhandsvisning** för källtransformering och klickar på Uppdatera för att få en ögonblicksbild av data.  Du kan använda dataförhandsvisning för att verifiera att din transformering är korrekt konfigurerad.

    ![Förhandsgranskning](media/quickstart-data-flow/data-preview.png)

1. Bredvid källnoden på dataflödesarbetsytan klickar du på plusikonen för att lägga till en ny transformering. Den första transformeringen som du lägger till är ett **Filter**.

    ![Lägga till ett filter](media/quickstart-data-flow/add-filter.png)

1. Ge filtertransformeringen **namnet FilterYears**. Klicka på uttrycksrutan bredvid Filtrera **på för att** öppna uttrycksverktyget. Här anger du filtreringsvillkoret.

1. Med uttrycksverktyget för dataflöden kan du interaktivt skapa uttryck som ska användas i olika transformationer. Uttryck kan innehålla inbyggda funktioner, kolumner från indataschemat och användardefinierade parametrar. Mer information om hur du skapar uttryck finns i [Dataflöde expression builder](../data-factory/concepts-data-flow-expression-builder.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json).

    I den här snabbstarten vill du filtrera filmer med genrer som gick ut mellan åren 1910 och 2000. Eftersom år för närvarande är en sträng måste du konvertera det till ett heltal med hjälp av ```toInteger()``` funktionen . Använd operatorerna större än eller lika med (>=) och mindre än eller lika med (<=) för att jämföra med literalårsvärdena 1910 och 200-. Sammanse dessa uttryck med operatorn och (&&). Uttrycket kommer ut som:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    För att ta reda på vilka filmer som är comedies kan du använda funktionen för att hitta mönstret ```rlike()``` "Aret" i kolumngenreerna. Union the rlike expression with the year comparison to get:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    ![Ange filtreringsvillkor](media/quickstart-data-flow/visual-expression-builder.png)

    Om du har ett aktivt felsökningskluster kan  du verifiera logiken genom att klicka på Uppdatera för att se utdata för uttryck jämfört med de indata som används. Det finns fler än ett rätt svar på hur du kan åstadkomma den här logiken med hjälp av språket för dataflödesuttryck.

    Klicka **på Spara och** slutför när du är klar med uttrycket.

1. Hämta en **dataförhandsgranskning** för att verifiera att filtret fungerar korrekt.

1. Nästa transformering som du lägger till är en **aggregeringsomvandling** under **Schemamodifierare**.

    ![Lägga till en aggregering](media/quickstart-data-flow/add-aggregate.png)

1. Ge **aggregeringsomvandlingen namnet AggregateComedyRatings**. På fliken **Gruppera efter** väljer du **år i** listrutan för att gruppera sammansättningarna efter det år som filmen kom ut.

    ![Mängdinställningar 1](media/quickstart-data-flow/aggregate-settings.png)

1. Gå till **fliken Aggregeringar.** I den vänstra textrutan ger du aggregeringskolumnen **namnet AverageComedyRating**. Klicka på rutan för det högra uttrycket för att ange mängduttrycket via uttrycksverktyget.

    ![Sammanställ inställningar 2](media/quickstart-data-flow/aggregate-settings-2.png)

1. Använd mängdfunktionen för **att få medelvärdet** av ```avg()``` kolumnklassificering. Eftersom **Klassificering** är en sträng ```avg()``` och tar in numeriska indata måste vi konvertera värdet till ett tal via funktionen ```toInteger()``` . Det här uttrycket ser ut så här:

    ```avg(toInteger(Rating))```

    Klicka **på Spara och slutför när** du är klar.

    ![Genomsnittligt betyg](media/quickstart-data-flow/average-comedy-rating.png)

1. Gå till fliken **Dataförhandsgranskning** för att visa transformeringsutdata. Observera att endast två kolumner finns där, **year** och **AverageComedyRating**.

    ![Förhandsversion av aggregerade data](media/quickstart-data-flow/transformation-output.png)

1. Därefter vill du lägga till en **Sink-transformering** under **Mål**.

    ![Lägg till en mottagare](media/quickstart-data-flow/add-sink.png)

1. Ge mottagaren namnet **Sink**. Klicka **på Ny** för att skapa din datauppsättning för mottagare.

1. Välj **Azure Data Lake Storage Gen2**. Klicka på Fortsätt.

1. Välj **DelimitedText**. Klicka på Fortsätt.

1. Namnge din datauppsättning för **mottagare FilmerSink**. För länkad tjänst väljer du den ADLS Gen2 tjänst som du skapade i steg 7. Ange en utdatamapp att skriva dina data till. I den här snabbstarten skriver vi till mappen "output" i containern "sample-data". Mappen behöver inte finnas i förväg och kan skapas dynamiskt. Ange **Första raden som rubrik som** true och välj **Ingen** för Importera **schema**. Klicka **på OK** när du är klar.

    ![Egenskaper för datauppsättning för mottagare](media/quickstart-data-flow/sink-dataset-properties.png)

Nu har du skapat ditt dataflöde. Du är redo att köra den i din pipeline.

## <a name="running-and-monitoring-the-data-flow"></a>Köra och övervaka Dataflöde

Du kan felsöka en pipeline innan du publicerar den. I det här steget ska du utlösa en felsökningskörning av dataflödespipelinen. Medan dataförhandsgranskningen inte skriver data skriver en felsökningskörning data till mottagarmålet.

1. Gå till pipelinearbetsytan. Klicka **på Felsök** för att utlösa en felsökningskörning.

    ![Felsökningspipeline](media/quickstart-data-flow/debug-pipeline.png)

1. Pipelinefelsökning av Dataflöde-aktiviteter använder det aktiva felsökningsklustret, men det tar ändå minst en minut att initiera. Du kan följa förloppet via **fliken Utdata.** När körningen är lyckad klickar du på glasögonikonen för att öppna övervakningsfönstret.

    ![Felsökning av utdata](media/quickstart-data-flow/debugging-output.png)

1. I övervakningsfönstret kan du se antalet rader och den tid som spenderas i varje transformeringssteg.

    ![Transformeringsövervakning](media/quickstart-data-flow/4-transformations.png)

1. Klicka på en transformering för att få detaljerad information om kolumner och partitionering av data.

    ![Transformeringsinformation](media/quickstart-data-flow/transformation-details.png)

Om du har följt den här snabbstarten korrekt bör du ha skrivit 83 rader och 2 kolumner i mappen för mottagare. Du kan verifiera data genom att kontrollera bloblagringen.


## <a name="next-steps"></a>Nästa steg

Gå vidare till följande artiklar om du vill veta Azure Synapse Analytics support:

> [!div class="nextstepaction"]
> [Pipeline och aktiviteter](../data-factory/concepts-pipelines-activities.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) 
>  [Mappa dataflöde – översikt](../data-factory/concepts-data-flow-overview.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) 
>  [Språk för dataflödesuttryck](../data-factory/data-flow-expression-functions.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)