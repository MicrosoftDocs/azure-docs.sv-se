---
title: Transformera data med ett data flöde för mappning
description: Den här självstudien innehåller stegvisa instruktioner för hur du använder Azure Data Factory för att omvandla data med data flöde för mappning
author: dcstwh
ms.author: weetok
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 03/11/2021
ms.openlocfilehash: 5deaef04330e21c8d9c580bf2a16814d4e902f6a
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2021
ms.locfileid: "103199873"
---
# <a name="transform-data-using-mapping-data-flows"></a>Omvandla data med Mappa dataflöden

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Om du inte har använt Azure Data Factory tidigare kan du läsa [Introduktion till Azure Data Factory](introduction.md).

I den här självstudien använder du gränssnittet i Azure Data Factory användar gränssnitt (UX) för att skapa en pipeline som kopierar och transformerar data från en Azure Data Lake Storage (ADLS) Gen2-källa till en ADLS Gen2 mottagare med hjälp av mappnings data flödet. Konfigurations mönstret i den här självstudien kan utökas vid omvandling av data med hjälp av data flöde för mappning

 >[!NOTE]
   >Den här självstudien är avsedd för att mappa data flöden i allmänhet. Data flöden är tillgängliga både i Azure Data Factory-och Synapse-pipeliner. Om du är nybörjare på data flöden i Azure Synapse-pipelines följer du [data flödet med Azure Synapse-pipeliner](https://docs.microsoft.com/azure/synapse-analytics/concepts-data-flow-overview) 
   
I den här självstudien gör du följande:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa en pipeline med en data flödes aktivitet.
> * Skapa ett data flöde för mappning med fyra transformeringar.
> * Testkör pipelinen.
> * Övervaka en data flödes aktivitet

## <a name="prerequisites"></a>Förutsättningar
* **Azure-prenumeration**. Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* **Azure Storage-konto**. Du använder ADLS-lagring som *käll* -och *mottagar* data lager. Om du inte har ett lagringskonto finns det anvisningar om hur du skapar ett i [Skapa ett Azure Storage-konto](../storage/common/storage-account-create.md).

Filen som vi transformerar i den här självstudien är MoviesDB.csv, som du hittar [här](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv). Om du vill hämta filen från GitHub kopierar du innehållet till en text redigerare som du väljer att spara lokalt som en CSV-fil. Om du vill överföra filen till ditt lagrings konto, se [Ladda upp blobar med Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md). Exemplen refererar till en behållare med namnet "Sample-data".

## <a name="create-a-data-factory"></a>Skapa en datafabrik

I det här steget skapar du en data fabrik och öppnar Data Factory UX för att skapa en pipeline i data fabriken.

1. Öppna **Microsoft Edge** eller **Google Chrome**. Data Factory-gränssnittet stöds för närvarande bara i webbläsarna Microsoft Edge och Google Chrome.
2. På den vänstra menyn väljer du **skapa en resurs**  >  **integrations**  >  **Data Factory**:

   ![Valet Data Factory i fönstret Nytt](./media/doc-common-process/new-azure-data-factory-menu.png)

3. I fönstret **Ny datafabrik**, under **Namn** anger du **ADFTutorialDataFactory**.

   Namnet på Azure Data Factory måste vara *globalt unikt*. Ange ett annat namn för datafabriken om du får ett felmeddelande om namnvärdet. (till exempel Dittnamnadftutorialdatafactory). Se artikeln [Namnregler för Data Factory](naming-rules.md) för namnregler för Data Factory-artefakter.

     ![Ny datafabrik](./media/doc-common-process/name-not-available-error.png)
4. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i.
5. Gör något av följande för **Resursgrupp**:

    a. Välj **Använd befintlig** och välj en befintlig resurs grupp i den nedrullningsbara listan.

    b. Välj **Skapa ny** och ange namnet på en resurs grupp. 
         
    Mer information om resursgrupper finns i [Använda resursgrupper för att hantera Azure-resurser](../azure-resource-manager/management/overview.md). 
6. Under **Version** väljer du **V2**.
7. Under **Plats** väljer du en plats för datafabriken. Endast platser som stöds visas i listrutan. Data lager (till exempel Azure Storage och SQL Database) och beräkningarna (till exempel Azure HDInsight) som används av data fabriken kan finnas i andra regioner.
8. Välj **Skapa**.
9. När du har skapat meddelandet visas meddelandet i Notifications Center. Välj **gå till resurs** för att navigera till sidan data fabrik.
10. Klicka på **Författare och övervakare** för att starta användargränssnittet för datafabriken på en separat flik.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Skapa en pipeline med en data flödes aktivitet

I det här steget ska du skapa en pipeline som innehåller en data flödes aktivitet.

1. På sidan **Nu sätter vi igång** väljer du **Skapa pipeline**.

   ![Skapa pipeline](./media/doc-common-process/get-started-page.png)

1. På fliken **Allmänt** för pipelinen anger du **TransformMovies** som **namn** på pipelinen.
1. I det fabriksinstallerade övre fältet drar du skjutreglaget för **data flödes fel sökning** på. Fel söknings läge möjliggör interaktiv testning av omvandlings logik mot ett aktivt Spark-kluster. Data flödes kluster tar 5-7 minuter att värma upp och användare rekommenderas att aktivera fel sökning först om de planerar att utföra data flödes utveckling. Mer information finns i [fel söknings läge](concepts-data-flow-debug-mode.md).

    ![Data flödes aktivitet](media/tutorial-data-flow/dataflow1.png)
1. I fönstret **aktiviteter** expanderar du **förflyttnings-och omvandlings** draget. Dra och släpp **data flödes** aktiviteten från fönstret till pipeline-arbetsytan.

    ![Skärm bild som visar pipeline-arbetsytan där du kan släppa data flödes aktiviteten.](media/tutorial-data-flow/activity1.png)
1. I popup-fönstret för **att lägga till data flöde** väljer du **Skapa nytt data flöde** och namnger sedan **TransformMovies** för data flödet. Klicka på Slutför när du är klar.

    ![Skärm bild som visar var du namnger ditt data flöde när du skapar ett nytt data flöde.](media/tutorial-data-flow/activity2.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Bygg omvandlings logik i data flödets arbets yta

När du har skapat ditt data flöde skickas det automatiskt till data flödets arbets yta. I det här steget ska du bygga ett data flöde som tar moviesDB.csv i ADLS-lagring och sammanställer genomsnitts betyget för Comedies från 1910 till 2000. Sedan skriver du filen tillbaka till ADLS-lagringen.

1. I data flödets arbets yta lägger du till en källa genom att klicka på rutan **Lägg till källa** .

    ![Skärm bild som visar rutan Lägg till källa.](media/tutorial-data-flow/dataflow2.png)
1. Namnge din käll **MoviesDB**. Klicka på **ny** för att skapa en ny käll data uppsättning.

    ![Skärm bild som visar var du väljer nytt när du har bytt namn på källan.](media/tutorial-data-flow/dataflow3.png)
1. Välj **Azure Data Lake Storage Gen2**. Klicka på Fortsätt.

    ![Skärm bild som visar panelen Azure Data Lake Storage Gen2.](media/tutorial-data-flow/dataset1.png)
1. Välj **DelimitedText**. Klicka på Fortsätt.

    ![Skärm bild som visar panelen DelimitedText.](media/tutorial-data-flow/dataset2.png)
1. Namnge din data uppsättnings **MoviesDB**. I list rutan länkad tjänst väljer du **nytt**.

    ![Skärm bild som visar List rutan länkad tjänst.](media/tutorial-data-flow/dataset3.png)
1. På skärmen Skapa länkad tjänst namnger du ADLS Gen2 länkade tjänst **ADLSGen2** och anger autentiseringsmetoden. Ange sedan dina autentiseringsuppgifter för anslutningen. I den här självstudien använder vi konto nyckeln för att ansluta till vårt lagrings konto. Du kan klicka på **Testa anslutning** för att kontrol lera att dina autentiseringsuppgifter har angetts korrekt. Klicka på skapa när du är färdig.

    ![Länkad tjänst](media/tutorial-data-flow/ls1.png)
1. När du är tillbaka på skärmen skapa data uppsättning anger du var filen finns under fältet **fil Sök väg** . I den här självstudien finns filen moviesDB.csv i container exempel-data. När filen har rubriker kontrollerar du **första raden som rubrik**. Välj **från anslutning/Arkiv** om du vill importera huvud schemat direkt från filen i lagrings utrymmet. Klicka på OK när du är färdig.

    ![Datauppsättningar](media/tutorial-data-flow/dataset4.png)
1. Om ditt fel söknings kluster har startats går du till fliken **data förhands granskning** i käll omvandlingen och klickar på **Uppdatera** för att hämta en ögonblicks bild av data. Du kan använda för hands versionen av data för att kontrol lera att din omvandling är korrekt konfigurerad.

    ![Skärm bild som visar var du kan förhandsgranska dina data för att kontrol lera att din omvandling är korrekt konfigurerad.](media/tutorial-data-flow/dataflow4.png)
1. Klicka på plus ikonen bredvid käll-noden på data flödets arbets yta för att lägga till en ny omvandling. Den första omvandlingen som du lägger till är ett **filter**.

    ![Data flödes arbets yta](media/tutorial-data-flow/dataflow5.png)
1. Namnge filter omvandlingen **FilterYears**. Klicka på uttrycks rutan bredvid **filtrera på** för att öppna uttrycks verktyget. Här anger du filtrerings villkoret.

    ![Skärm bild som visar rutan filtrera efter uttryck.](media/tutorial-data-flow/filter1.png)
1. Med uttrycks verktyget Data Flow kan du interaktivt skapa uttryck som ska användas i olika transformationer. Uttryck kan innehålla inbyggda funktioner, kolumner från schemat för indata och användardefinierade parametrar. Mer information om hur du skapar uttryck finns i [uttrycks verktyg för data flöde](concepts-data-flow-expression-builder.md).

    I den här självstudien vill du filtrera filmer för Genre-komedi som kommer ut mellan åren 1910 och 2000. Om ett år är för närvarande en sträng måste du konvertera det till ett heltal med hjälp av ```toInteger()``` funktionen. Använd den större än eller lika med (>=) och mindre än eller lika med (<=) operatörer för att jämföra med de exakta värdena 1910 och 200-. Union dessa uttryck tillsammans med operatorn och (&&). Uttrycket visas som:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    Om du vill ta reda på vilka filmer som är Comedies kan du använda ```rlike()``` funktionen för att hitta mönstret "komedi" i kolumn genrerna. Union rlike-uttrycket med jämförelse året för att hämta:

    ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    Om du har ett fel söknings kluster aktivt kan du verifiera din logik genom att klicka på **Uppdatera** för att se uttryckets utdata jämfört med de indata som används. Det finns mer än ett rätt svar på hur du kan utföra den här logiken med hjälp av Expression-språket för data flödet.

    ![Filtrera](media/tutorial-data-flow/filter2.png)

    Klicka på **Spara och slutför** när du är klar med ditt uttryck.

1. Hämta en **data förhands granskning** för att kontrol lera att filtret fungerar korrekt.

    ![Skärm bild som visar data förhands granskningen som du hämtade.](media/tutorial-data-flow/filter3.png)
1. Nästa omvandling du lägger till är en **sammanställd** omvandling under **schema modifieraren**.

    ![Skärm bild som visar den aggregerade schema modifieraren.](media/tutorial-data-flow/agg1.png)
1. Namnge den sammanställda transformeringen **AggregateComedyRatings**. På fliken **Gruppera efter** väljer du **år** i list rutan för att gruppera agg regeringar efter året som filmen kommer ut.

    ![Skärm bild som visar alternativet år på fliken Gruppera efter under aggregerade inställningar.](media/tutorial-data-flow/agg2.png)
1. Gå till fliken **agg regeringar** . I den vänstra text rutan namnger du den sammanställda kolumnen **AverageComedyRating**. Klicka i rutan till höger uttryck för att ange det sammanställda uttrycket via uttrycks verktyget.

    ![Skärm bild som visar alternativet år på fliken Aggregator under aggregerade inställningar.](media/tutorial-data-flow/agg3.png)
1. Om du vill få medelvärdet för kolumn **klassificeringen** använder du ```avg()``` mängd funktionen. Eftersom **klassificering** är en sträng och ```avg()``` tar med numeriska värden måste vi konvertera värdet till ett tal via ```toInteger()``` funktionen. Detta är ett uttryck som ser ut så här:

    ```avg(toInteger(Rating))```

    Klicka på **Spara och slutför** när du är klar.

    ![Skärm bild som visar det sparade uttrycket.](media/tutorial-data-flow/agg4.png)
1. Gå till fliken **data förhands granskning** för att Visa transformationens utdata. Observera att endast två kolumner finns där, **år** och **AverageComedyRating**.

    ![Aggregera](media/tutorial-data-flow/agg3.png)
1. Sedan vill du lägga till en **Sink** -omvandling under **mål**.

    ![Skärm bild som visar var du kan lägga till en Sink-omvandling under mål.](media/tutorial-data-flow/sink1.png)
1. Namnge din Sink- **mottagare**. Klicka på **ny** för att skapa din data uppsättning för mottagare.

    ![Skärm bild som visar var du kan namnge din mottagare och skapa en ny Sink-datauppsättning.](media/tutorial-data-flow/sink2.png)
1. Välj **Azure Data Lake Storage Gen2**. Klicka på Fortsätt.

    ![Skärm bild som visar Azure Data Lake Storage Gen2 panel som du kan välja.](media/tutorial-data-flow/dataset1.png)
1. Välj **DelimitedText**. Klicka på Fortsätt.

    ![Datamängd](media/tutorial-data-flow/dataset2.png)
1. Namnge din data uppsättning **MoviesSink**. För länkad tjänst väljer du den länkade tjänsten ADLS Gen2 som du skapade i steg 6. Ange en mapp för utdata för att skriva dina data till. I den här självstudien ska vi skriva till mappen "output" i container ' Sample-data '. Mappen behöver inte finnas i förväg och kan skapas dynamiskt. Ange **första raden som rubrik** som sann och välj **ingen** för **import schema**. Klicka på Slutför.

    ![Kanalmottagare](media/tutorial-data-flow/sink3.png)

Nu har du slutfört skapandet av ditt data flöde. Du är redo att köra den i din pipeline.

## <a name="running-and-monitoring-the-data-flow"></a>Köra och övervaka data flödet

Du kan felsöka en pipeline innan du publicerar den. I det här steget ska du utlösa en fel söknings körning av data flödets pipeline. Medan data förhands granskning skriver inte data, skriver en fel söknings körning data till mottagar målet.

1. Gå till pipeline-arbetsytan. Klicka på **Felsök** för att utlösa en debug-körning.

    ![Skärm bild som visar pipeline-arbetsytan med fel sökning markerat.](media/tutorial-data-flow/pipeline1.png)
1. Fel sökning av data flödes aktiviteter i pipeline använder det aktiva fel söknings klustret men tar fortfarande minst en minut att initiera. Du kan följa förloppet via fliken **utdata** . När körningen är klar klickar du på glasögon-ikonen för att öppna fönstret övervakning.

    ![Pipeline](media/tutorial-data-flow/pipeline2.png)
1. I fönstret övervakning kan du se antalet rader och tid som lagts till i varje omvandlings steg.

    ![Skärm bild som visar fönstret övervakning där du kan se antalet rader och tid som lagts till i varje omformnings steg.](media/tutorial-data-flow/pipeline3.png)
1. Klicka på en omvandling för att få detaljerad information om kolumnerna och partitionering av data.

    ![Övervakning](media/tutorial-data-flow/pipeline4.png)

Om du har följt den här självstudien korrekt bör du ha skrivit 83 rader och 2 kolumner i mottagar mappen. Du kan kontrol lera att informationen är korrekt genom att kontrol lera Blob Storage.

## <a name="next-steps"></a>Nästa steg

Pipelinen i den här självstudien kör ett data flöde som sammanställer genomsnitts betyget för Comedies från 1910 till 2000 och skriver data till ADLS. Du har lärt dig att:

> [!div class="checklist"]
> * Skapa en datafabrik.
> * Skapa en pipeline med en data flödes aktivitet.
> * Skapa ett data flöde för mappning med fyra transformeringar.
> * Testkör pipelinen.
> * Övervaka en data flödes aktivitet

Lär dig mer om [data flödets uttrycks språk](data-flow-expression-functions.md).
