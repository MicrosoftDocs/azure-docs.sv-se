---
title: 'Snabb start: transformera data med hjälp av ett mappnings data flöde'
description: Den här självstudien innehåller stegvisa instruktioner för hur du använder Azure Synapse Analytics för att omvandla data med data flöde för mappning.
author: djpmsft
ms.author: daperlov
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 11/03/2020
ms.openlocfilehash: cb0fd5464f7025d71dd384c56233aefa6a6cd364
ms.sourcegitcommit: d59abc5bfad604909a107d05c5dc1b9a193214a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/14/2021
ms.locfileid: "98218730"
---
# <a name="quickstart-transform-data-using-mapping-data-flows"></a>Snabb start: transformera data med hjälp av mappnings data flöden

I den här snabb starten använder du Azure Synapse Analytics för att skapa en pipeline som transformerar data från en Azure Data Lake Storage Gen2-källa (ADLS Gen2) till en ADLS Gen2 mottagare med hjälp av data flödet för mappning. Konfigurations mönstret i den här snabb starten kan utökas vid omvandling av data med hjälp av data flöde för mappning

I den här snabb starten utför du följande steg:

> [!div class="checklist"]
> * Skapa en pipeline med en data flödes aktivitet i Azure Synapse Analytics.
> * Skapa ett data flöde för mappning med fyra transformeringar.
> * Testkör pipelinen.
> * Övervaka en data flödes aktivitet

## <a name="prerequisites"></a>Förutsättningar

* **Azure-prenumeration**: Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* **Azure Synapse-arbetsyta**: skapa en Synapse-arbetsyta med hjälp av Azure Portal följa anvisningarna i [snabb start: skapa en Synapse-arbetsyta](quickstart-create-workspace.md).
* **Azure Storage-konto**: du använder ADLS-lagring som *käll* -och *mottagar* data lager. Om du inte har ett lagringskonto finns det anvisningar om hur du skapar ett i [Skapa ett Azure Storage-konto](../storage/common/storage-account-create.md).

    Filen som vi transformerar i den här självstudien är MoviesDB.csv, som du hittar [här](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv). Om du vill hämta filen från GitHub kopierar du innehållet till en text redigerare som du väljer att spara lokalt som en CSV-fil. Om du vill överföra filen till ditt lagrings konto, se [Ladda upp blobar med Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md). Exemplen refererar till en behållare med namnet "Sample-data".

### <a name="navigate-to-the-synapse-studio"></a>Navigera till Synapse Studio

När din Azure Synapse-arbetsyta har skapats kan du öppna Synapse Studio på två sätt:

* Öppna din Synapse-arbetsyta i [Azure Portal](https://ms.portal.azure.com/#home). Välj **Öppna** på kortet Open Synapse Studio under komma igång.
* Öppna [Azure Synapse Analytics](https://web.azuresynapse.net/) och logga in på din arbets yta.

I den här snabb starten använder vi arbets ytan med namnet "adftest2020" som exempel. Den kommer automatiskt att gå till start sidan för Synapse Studio.

![Start sida för Synapse Studio](media/doc-common-process/synapse-studio-home.png)

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Skapa en pipeline med en data flödes aktivitet

En pipeline innehåller det logiska flödet för en körning av en uppsättning aktiviteter. I det här avsnittet ska du skapa en pipeline som innehåller en data flödes aktivitet.

1. Gå till fliken **integrera** . Välj på plus ikonen bredvid pipelinens huvud och välj pipeline.

   ![Skapa en ny pipeline](media/doc-common-process/new-pipeline.png)

1. På sidan **Egenskaper** inställningar i pipelinen anger du **TransformMovies** som **namn**.

1. Under *flytta och transformera* i fönstret *aktiviteter* drar du **data flödet** till pipeline-arbetsytan.

1. I popup-fönstret för **att lägga till data flödes** sidan väljer du **Skapa nytt data** flöde  ->  **data flöde**. Klicka på **OK** när du är färdig.

   ![Skapa ett data flöde](media/quickstart-data-flow/new-data-flow.png)

1. Namnge din data flödes **TransformMovies** på sidan **Egenskaper** .

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Bygg omvandlings logik i data flödets arbets yta

När du har skapat ditt data flöde skickas det automatiskt till data flödets arbets yta. I det här steget ska du bygga ett data flöde som tar MoviesDB.csv i ADLS-lagring och sammanställer genomsnitts betyget för Comedies från 1910 till 2000. Sedan skriver du filen tillbaka till ADLS-lagringen.

1. Ovanför data flödes arbets ytan drar du skjutreglaget för **data flödes fel sökning** på. Fel söknings läge möjliggör interaktiv testning av omvandlings logik mot ett aktivt Spark-kluster. Data flödes kluster tar 5-7 minuter att värma upp och användare rekommenderas att aktivera fel sökning först om de planerar att utföra data flödes utveckling. Mer information finns i [fel söknings läge](../data-factory/concepts-data-flow-debug-mode.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json).

    ![Skjut in fel sökningen på](media/quickstart-data-flow/debug-on.png)

1. I data flödets arbets yta lägger du till en källa genom att klicka på rutan **Lägg till källa** .

1. Namnge din käll **MoviesDB**. Klicka på **ny** för att skapa en ny käll data uppsättning.

    ![Skapa en ny käll data uppsättning](media/quickstart-data-flow/new-source-dataset.png)

1. Välj **Azure Data Lake Storage Gen2**. Klicka på Fortsätt.

    ![Välj Azure Data Lake Storage Gen2](media/quickstart-data-flow/select-source-dataset.png)

1. Välj **DelimitedText**. Klicka på Fortsätt.

1. Namnge din data uppsättnings **MoviesDB**. I list rutan länkad tjänst väljer du **nytt**.

1. I fönstret Skapa länkad tjänst namnger du ADLS Gen2 länkade tjänstens **ADLSGen2** och anger din autentiseringsmetod. Ange sedan dina autentiseringsuppgifter för anslutningen. I den här snabb starten använder vi konto nyckeln för att ansluta till vårt lagrings konto. Du kan klicka på **Testa anslutning** för att kontrol lera att dina autentiseringsuppgifter har angetts korrekt. Klicka på **skapa** när du är färdig.

    ![Skapa en länkad käll tjänst](media/quickstart-data-flow/adls-gen2-linked-service.png)

1. När du är tillbaka på skärmen för att skapa data uppsättning går du till fältet **fil Sök väg** och anger var filen finns. I den här snabb starten finns filen "MoviesDB.csv" i behållaren "Sample-data". När filen har rubriker kontrollerar du **första raden som rubrik**. Välj **från anslutning/Arkiv** om du vill importera huvud schemat direkt från filen i lagrings utrymmet. Klicka på **OK** när du är färdig.

    ![Inställningar för källdatauppsättningen](media/quickstart-data-flow/source-dataset-properties.png)

1. Om ditt fel söknings kluster har startats går du till fliken **data förhands granskning** i käll omvandlingen och klickar på **Uppdatera** för att hämta en ögonblicks bild av data. Du kan använda för hands versionen av data för att kontrol lera att din omvandling är korrekt konfigurerad.

    ![Förhandsgranskning](media/quickstart-data-flow/data-preview.png)

1. Klicka på plus ikonen bredvid käll-noden på data flödets arbets yta för att lägga till en ny omvandling. Den första omvandlingen som du lägger till är ett **filter**.

    ![Lägga till ett filter](media/quickstart-data-flow/add-filter.png)

1. Namnge filter omvandlingen **FilterYears**. Klicka på uttrycks rutan bredvid **filtrera på** för att öppna uttrycks verktyget. Här anger du filtrerings villkoret.

1. Med uttrycks verktyget Data Flow kan du interaktivt skapa uttryck som ska användas i olika transformationer. Uttryck kan innehålla inbyggda funktioner, kolumner från schemat för indata och användardefinierade parametrar. Mer information om hur du skapar uttryck finns i [uttrycks verktyg för data flöde](../data-factory/concepts-data-flow-expression-builder.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json).

    I den här snabb starten vill du filtrera filmer för Genre-komedi som kommer ut mellan åren 1910 och 2000. Om ett år är för närvarande en sträng måste du konvertera det till ett heltal med hjälp av ```toInteger()``` funktionen. Använd den större än eller lika med (>=) och mindre än eller lika med (<=) operatörer för att jämföra med de exakta värdena 1910 och 200-. Union dessa uttryck tillsammans med operatorn och (&&). Uttrycket visas som:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    Om du vill ta reda på vilka filmer som är Comedies kan du använda ```rlike()``` funktionen för att hitta mönstret "komedi" i kolumn genrerna. Union rlike-uttrycket med jämförelse året för att hämta:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    ![Ange filtrerings villkor](media/quickstart-data-flow/visual-expression-builder.png)

    Om du har ett fel söknings kluster aktivt kan du verifiera din logik genom att klicka på **Uppdatera** för att se uttryckets utdata jämfört med de indata som används. Det finns mer än ett rätt svar på hur du kan utföra den här logiken med hjälp av Expression-språket för data flödet.

    Klicka på **Spara och slutför** när du är klar med ditt uttryck.

1. Hämta en **data förhands granskning** för att kontrol lera att filtret fungerar korrekt.

1. Nästa omvandling du lägger till är en **sammanställd** omvandling under **schema modifieraren**.

    ![Lägg till en mängd](media/quickstart-data-flow/add-aggregate.png)

1. Namnge den sammanställda transformeringen **AggregateComedyRatings**. På fliken **Gruppera efter** väljer du **år** i list rutan för att gruppera agg regeringar efter året som filmen kommer ut.

    ![Samlings inställningar 1](media/quickstart-data-flow/aggregate-settings.png)

1. Gå till fliken **agg regeringar** . I den vänstra text rutan namnger du den sammanställda kolumnen **AverageComedyRating**. Klicka i rutan till höger uttryck för att ange det sammanställda uttrycket via uttrycks verktyget.

    ![Samlings inställningar 2](media/quickstart-data-flow/aggregate-settings-2.png)

1. Om du vill få medelvärdet för kolumn **klassificeringen** använder du ```avg()``` mängd funktionen. Eftersom **klassificering** är en sträng och ```avg()``` tar med numeriska värden måste vi konvertera värdet till ett tal via ```toInteger()``` funktionen. Detta uttryck ser ut så här:

    ```avg(toInteger(Rating))```

    Klicka på **Spara och slutför** när du är klar.

    ![Genomsnittlig komedi-klassificering](media/quickstart-data-flow/average-comedy-rating.png)

1. Gå till fliken **data förhands granskning** för att Visa transformationens utdata. Observera att endast två kolumner finns där, **år** och **AverageComedyRating**.

    ![Förhands granskning av mängd data](media/quickstart-data-flow/transformation-output.png)

1. Sedan vill du lägga till en **Sink** -omvandling under **mål**.

    ![Lägg till en mottagare](media/quickstart-data-flow/add-sink.png)

1. Namnge din Sink- **mottagare**. Klicka på **ny** för att skapa din data uppsättning för mottagare.

1. Välj **Azure Data Lake Storage Gen2**. Klicka på Fortsätt.

1. Välj **DelimitedText**. Klicka på Fortsätt.

1. Namnge din data uppsättning **MoviesSink**. För länkad tjänst väljer du den ADLS Gen2 länkade tjänst som du skapade i steg 7. Ange en mapp för utdata för att skriva dina data till. I den här snabb starten ska vi skriva till mappen "output" i container ' Sample-data '. Mappen behöver inte finnas i förväg och kan skapas dynamiskt. Ange **första raden som rubrik** som sann och välj **ingen** för **import schema**. Klicka på **OK** när du är färdig.

    ![Egenskaper för data uppsättning för mottagare](media/quickstart-data-flow/sink-dataset-properties.png)

Nu har du slutfört skapandet av ditt data flöde. Du är redo att köra den i din pipeline.

## <a name="running-and-monitoring-the-data-flow"></a>Köra och övervaka data flödet

Du kan felsöka en pipeline innan du publicerar den. I det här steget ska du utlösa en fel söknings körning av data flödets pipeline. Medan data förhands granskning skriver inte data, skriver en fel söknings körning data till mottagar målet.

1. Gå till pipeline-arbetsytan. Klicka på **Felsök** för att utlösa en debug-körning.

    ![Fel söknings pipeline](media/quickstart-data-flow/debug-pipeline.png)

1. Fel sökning av data flödes aktiviteter i pipeline använder det aktiva fel söknings klustret men tar fortfarande minst en minut att initiera. Du kan följa förloppet via fliken **utdata** . När körningen är klar klickar du på glasögon-ikonen för att öppna fönstret övervakning.

    ![Fel sökning av utdata](media/quickstart-data-flow/debugging-output.png)

1. I fönstret övervakning kan du se antalet rader och tid som lagts till i varje omvandlings steg.

    ![Omvandlings övervakning](media/quickstart-data-flow/4-transformations.png)

1. Klicka på en omvandling för att få detaljerad information om kolumnerna och partitionering av data.

    ![Omvandlings information](media/quickstart-data-flow/transformation-details.png)

Om du har följt den här snabb starten korrekt bör du ha skrivit 83 rader och 2 kolumner i mottagar mappen. Du kan verifiera data genom att kontrol lera Blob Storage.


## <a name="next-steps"></a>Nästa steg

Gå vidare till följande artiklar om du vill lära dig mer om Azure Synapse Analytics-support:

> [!div class="nextstepaction"]
> [Pipeline och aktiviteter](../data-factory/concepts-pipelines-activities.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) 
>  [Översikt över](../data-factory/concepts-data-flow-overview.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) 
>  kart data flöde [Data flöde uttrycks språk](../data-factory/data-flow-expression-functions.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json)