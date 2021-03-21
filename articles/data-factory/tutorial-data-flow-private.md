---
title: Transformera data med ett Azure Data Factory hanterat data flöde för mappning av virtuellt nätverk
description: Den här självstudien innehåller stegvisa instruktioner för hur du använder Azure Data Factory för att transformera data med data flöden för mappning.
author: dcstwh
ms.author: weetok
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/15/2021
ms.openlocfilehash: a5c93244862d72f9c8ea2928c41e699302b1752b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98249451"
---
# <a name="transform-data-securely-by-using-mapping-data-flow"></a>Transformera data på ett säkert sätt med hjälp av data flödet för mappning

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Om du inte har använt Azure Data Factory tidigare kan du läsa [Introduktion till Azure Data Factory](./introduction.md).

I den här självstudien använder du Data Factory användar gränssnitt för att skapa en pipeline som kopierar och transformerar data *från en Azure Data Lake Storage Gen2 källa till en data Lake Storage Gen2 mottagare (båda tillåter endast åtkomst till valda nätverk) genom att* använda mappa data flöde i [Data Factory hanterade Virtual Network](managed-virtual-network-private-endpoint.md). Du kan expandera konfigurations mönstret i den här självstudien när du transformerar data med hjälp av mappnings data flödet.

I den här självstudien gör du följande:

> [!div class="checklist"]
>
> * Skapa en datafabrik.
> * Skapa en pipeline med en data flödes aktivitet.
> * Skapa ett data flöde för mappning med fyra transformeringar.
> * Testkör pipelinen.
> * Övervaka en data flödes aktivitet.

## <a name="prerequisites"></a>Förutsättningar

* **Azure-prenumeration**. Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* **Azure Storage-konto**. Du använder Data Lake Storage som *käll* -och *mottagar* data lager. Om du inte har ett lagringskonto finns det anvisningar om hur du skapar ett i [Skapa ett Azure Storage-konto](../storage/common/storage-account-create.md?tabs=azure-portal). *Se till att lagrings kontot bara tillåter åtkomst från valda nätverk.* 

Filen som vi ska transformera i den här självstudien är moviesDB.csv, som finns på den här [GitHub innehålls webbplatsen](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv). Om du vill hämta filen från GitHub kopierar du innehållet till en text redigerare som du väljer för att spara den lokalt som en CSV-fil. Om du vill överföra filen till ditt lagrings konto, se [Ladda upp blobar med Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md). Exemplen refererar till en behållare med namnet **Sample-data**.

## <a name="create-a-data-factory"></a>Skapa en datafabrik

I det här steget skapar du en data fabrik och öppnar Data Factory gränssnittet för att skapa en pipeline i data fabriken.

1. Öppna Microsoft Edge eller Google Chrome. För närvarande stöder endast Microsoft Edge-och Google Chrome-webbläsare Data Factory gränssnittet.
1. På den vänstra menyn väljer du **skapa en resurs**  >  **analys**  >  **Data Factory**.
1. I fönstret **Ny datafabrik**, under **Namn** anger du **ADFTutorialDataFactory**.

   Namnet på data fabriken måste vara *globalt unikt*. Om du får ett fel meddelande om name-värdet anger du ett annat namn på data fabriken (till exempel Dittnamnadftutorialdatafactory). Se artikeln [Namnregler för Data Factory](naming-rules.md) för namnregler för Data Factory-artefakter.

1. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i.
1. Gör något av följande för **Resursgrupp**:

    * Välj **Använd befintlig** och välj en befintlig resurs grupp i den nedrullningsbara listan.
    * Välj **Skapa ny** och ange namnet på en resurs grupp. 
         
    Mer information om resursgrupper finns i [Använda resursgrupper för att hantera Azure-resurser](../azure-resource-manager/management/overview.md). 
1. Under **Version** väljer du **V2**.
1. Under **Plats** väljer du en plats för datafabriken. Endast platser som stöds visas i den nedrullningsbara listan. Data lager (till exempel Azure Storage och Azure SQL Database) och beräkningarna (till exempel Azure HDInsight) som används av data fabriken kan finnas i andra regioner.

1. Välj **Skapa**.
1. När du har skapat meddelandet visas meddelandet i meddelande centret. Välj **gå till resurs** för att gå till sidan **Data Factory** .
1. Klicka på **Författare och övervakare** för att starta användargränssnittet för datafabriken på en separat flik.

## <a name="create-an-azure-ir-in-data-factory-managed-virtual-network"></a>Skapa en Azure IR i Data Factory hanterade Virtual Network

I det här steget skapar du en Azure IR och aktiverar Data Factory hanterade Virtual Network.

1. I Data Factory portal går du till **Hantera** och väljer **nytt** för att skapa en ny Azure IR.

   ![Skärm bild som visar hur du skapar en ny Azure IR.](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
1. På sidan **installation av integration runtime** väljer du vilken integrerings körning som ska skapas baserat på de funktioner som krävs. I den här självstudien väljer du **Azure, egen värd** och klickar sedan på **Fortsätt**. 
1. Välj **Azure** och klicka sedan på **Fortsätt** för att skapa en Azure integration Runtime.

   ![Skärm bild som visar en ny Azure IR.](./media/tutorial-copy-data-portal-private/azure-ir.png)

1. Under **konfiguration av virtuellt nätverk (för hands version)** väljer du **Aktivera**.

   ![Skärm bild som visar aktivering av en ny Azure IR.](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)

1. Välj **Skapa**.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Skapa en pipeline med en data flödes aktivitet

I det här steget ska du skapa en pipeline som innehåller en data flödes aktivitet.

1. På sidan **Nu sätter vi igång** väljer du **Skapa pipeline**.

   ![Skärm bild som visar hur du skapar en pipeline.](./media/doc-common-process/get-started-page.png)

1. I rutan Egenskaper för pipelinen anger du **TransformMovies** som namn på pipelinen.
1. I det fabriksinstallerade övre fältet drar du skjutreglaget för **data flödes fel sökning** på. Fel söknings läge möjliggör interaktiv testning av omvandlings logik mot ett aktivt Spark-kluster. Data flödes kluster tar fem till sju minuter att värma upp. Aktivera **fel sökning av data flöde** först om du planerar att göra data flödes utveckling. Mer information finns i [fel söknings läge](./concepts-data-flow-debug-mode.md).

    ![Skärm bild som visar skjutreglaget för fel sökning av data flöde.](media/tutorial-data-flow-private/dataflow-debug.png)
1. I fönstret **aktiviteter** expanderar du **flytta och transformera**. Dra **data flödes** aktiviteten från fönstret till pipeline-arbetsytan.

1. I popup-fönstret för **att lägga till data flöde** väljer du **Skapa nytt data flöde** och väljer sedan **mappa data flöde**. Välj **OK** när du är klar.

    ![Skärm bild som visar data flöde för mappning.](media/tutorial-data-flow-private/mapping-dataflow.png)

1. Namnge ditt data flöde **TransformMovies** i rutan Egenskaper.

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Bygg omvandlings logik i data flödets arbets yta

När du har skapat ditt data flöde skickas det automatiskt till data flödets arbets yta. I det här steget ska du bygga ett data flöde som tar moviesDB.csv-filen i Data Lake Storage och aggregerar genomsnitts betyget för Comedies från 1910 till 2000. Sedan skriver du tillbaka filen till Data Lake Storage.

### <a name="add-the-source-transformation"></a>Lägg till käll omvandlingen

I det här steget konfigurerar du Data Lake Storage Gen2 som källa.

1. I data flödets arbets yta lägger du till en källa genom att markera rutan **Lägg till källa** .

1. Namnge din käll **MoviesDB**. Välj **ny** för att skapa en ny käll data uppsättning.

1. Välj **Azure Data Lake Storage Gen2** och välj sedan **Fortsätt**.

1. Välj **DelimitedText** och välj sedan **Fortsätt**.

1. Namnge din data uppsättnings **MoviesDB**. I list rutan länkad tjänst väljer du **nytt**.

1. I fönstret Skapa länkad tjänst namnger du Data Lake Storage Gen2 länkade tjänstens **ADLSGen2** och anger din autentiseringsmetod. Ange sedan dina autentiseringsuppgifter för anslutningen. I den här självstudien använder vi **konto nyckeln** för att ansluta till vårt lagrings konto. 

1. Se till att aktivera **interaktiv redigering**. Det kan ta en minut att aktive ras.

    ![Skärm bild som visar interaktiv redigering.](./media/tutorial-data-flow-private/interactive-authoring.png)

1. Välj **Testanslutning**. Det bör inte utföras eftersom lagrings kontot inte tillåter åtkomst till det utan att en privat slut punkt skapas och godkänns. I fel meddelandet bör du se en länk för att skapa en privat slut punkt som du kan följa för att skapa en hanterad privat slut punkt. Ett alternativ är att gå direkt till fliken **Hantera** och följa instruktionerna i [det här avsnittet](#create-a-managed-private-endpoint) för att skapa en hanterad privat slut punkt.

1. Håll dialog rutan öppen och gå sedan till ditt lagrings konto.

1. Följ anvisningarna i [det här avsnittet](#approval-of-a-private-link-in-a-storage-account) om du vill godkänna den privata länken.

1. Gå tillbaka till dialog rutan. Välj **Testa anslutning** igen och välj **skapa** för att distribuera den länkade tjänsten.

1. På skärmen skapa data uppsättning anger du var filen finns under fältet **fil Sök väg** . I den här självstudien finns filen moviesDB.csv i container **exempel-data**. Eftersom filen innehåller rubriker markerar du kryss rutan **första raden som rubrik** . Välj **från anslutning/Arkiv** om du vill importera huvud schemat direkt från filen i lagrings utrymmet. Välj **OK** när du är klar.

    ![Skärm bild som visar käll Sök vägen.](media/tutorial-data-flow-private/source-file-path.png)

1. Om ditt fel söknings kluster har startats går du till fliken **data förhands granskning** i käll omvandlingen och väljer **Uppdatera** för att hämta en ögonblicks bild av data. Du kan använda för hands versionen av data för att kontrol lera att din omvandling är korrekt konfigurerad.

    ![Skärm bild som visar fliken Data förhands granskning.](media/tutorial-data-flow-private/data-preview.png)

#### <a name="create-a-managed-private-endpoint"></a>Skapa en hanterad privat slut punkt

Om du inte använde hyperlänken när du testade den föregående anslutningen följer du sökvägen. Nu måste du skapa en hanterad privat slut punkt som du ansluter till den länkade tjänst som du har skapat.

1. Gå till fliken **Hantera** .

   > [!NOTE]
   > Fliken **Hantera** kanske inte är tillgänglig för alla data Factory-instanser. Om du inte ser det kan du komma åt privata slut punkter genom att välja **författar**  >  **anslutningar**  >  **privat slut punkt**.

1. Gå till avsnittet **hanterade privata slut punkter** .
1. Välj **+ ny** under **hanterade privata slut punkter**.

    ![Skärm bild som visar knappen Ny hanterad privat slut punkt ny.](./media/tutorial-data-flow-private/new-managed-private-endpoint.png) 

1. Välj panelen **Azure Data Lake Storage Gen2** i listan och välj **Fortsätt**.
1. Ange namnet på det lagrings konto som du har skapat.
1. Välj **Skapa**.
1. Efter några sekunder bör du se att den privata länken som skapas kräver ett godkännande.
1. Välj den privata slut punkt som du skapade. Du kan se en hyperlänk som kommer att leda till att du godkänner den privata slut punkten på lagrings konto nivån.

    ![Skärm bild som visar fönstret Hantera privat slut punkt.](./media/tutorial-data-flow-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-a-storage-account"></a>Godkännande av en privat länk i ett lagrings konto

1. I lagrings kontot går du till **anslutningar för privata slut punkter** under avsnittet **Inställningar** .

1. Markera kryss rutan efter den privata slut punkt som du skapade och välj **Godkänn**.

    ![Skärm bild som visar knappen Godkänn privat slut punkt.](./media/tutorial-data-flow-private/approve-private-endpoint.png)

1. Lägg till en beskrivning och välj **Ja**.
1. Gå tillbaka till avsnittet **hanterade privata slut punkter** på fliken **Hantera** i Data Factory.
1. Efter ungefär en minut bör du se att godkännandet visas för din privata slut punkt.

### <a name="add-the-filter-transformation"></a>Lägg till filter omvandlingen

1. Bredvid Källnoden på data flödets arbets yta väljer du plus ikonen för att lägga till en ny omvandling. Den första omvandlingen som du lägger till är ett **filter**.

    ![Skärm bild som visar hur du lägger till ett filter.](media/tutorial-data-flow-private/add-filter.png)
1. Namnge filter omvandlingen **FilterYears**. Välj uttrycks rutan bredvid **filtrera på** för att öppna uttrycks verktyget. Här anger du filtrerings villkoret.

    ![Skärm bild som visar FilterYears.](media/tutorial-data-flow-private/filter-years.png)
1. Med uttrycks verktyget Data Flow kan du interaktivt skapa uttryck som ska användas i olika transformationer. Uttryck kan innehålla inbyggda funktioner, kolumner från schemat för indata och användardefinierade parametrar. Mer information om hur du skapar uttryck finns i [uttrycks verktyg för data flöde](./concepts-data-flow-expression-builder.md).

    * I den här självstudien vill du filtrera filmer i komedi-genren som kom mellan åren 1910 och 2000. Eftersom året för närvarande är en sträng måste du konvertera det till ett heltal med hjälp av ```toInteger()``` funktionen. Använd operatorerna större än eller lika med (>=) och mindre än eller lika med (<=) för att jämföra med de litterala årets värden 1910 och 2000. Union dessa uttryck tillsammans med operatorn och (&&). Uttrycket visas som:

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    * Om du vill ta reda på vilka filmer som är Comedies kan du använda ```rlike()``` funktionen för att hitta mönstret "komedi" i kolumn genrerna. Union rlike-uttrycket med jämförelse året för att hämta:

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    * Om du har ett aktivt fel söknings kluster kan du verifiera din logik genom att välja **Uppdatera** för att se uttryckets utdata jämfört med de använda inmatningarna. Det finns mer än ett rätt svar på hur du kan utföra den här logiken med hjälp av Expression-språket för data flödet.

        ![Skärm bild som visar filter uttrycket.](media/tutorial-data-flow-private/filter-expression.png)

    * Välj **Spara och slutför** när du är klar med ditt uttryck.

1. Hämta en **data förhands granskning** för att kontrol lera att filtret fungerar korrekt.

    ![Skärm bild som visar den filtrerade data förhands granskningen.](media/tutorial-data-flow-private/filter-data.png)

### <a name="add-the-aggregate-transformation"></a>Lägg till aggregerad transformering

1. Nästa omvandling du lägger till är en **sammanställd** omvandling under **schema modifieraren**.

    ![Skärm bild som visar hur du lägger till aggregatet.](media/tutorial-data-flow-private/add-aggregate.png)
1. Namnge den sammanställda transformeringen **AggregateComedyRating**. På fliken **Gruppera efter** väljer du **år** i list rutan för att gruppera agg regeringar efter året som filmen kommer ut.

    ![Skärm bild som visar samlings gruppen.](media/tutorial-data-flow-private/group-by-year.png)
1. Gå till fliken **agg regeringar** . I den vänstra text rutan namnger du den sammanställda kolumnen **AverageComedyRating**. Välj rutan till höger uttryck för att ange det sammanställda uttrycket via uttrycks verktyget.

    ![Skärm bild som visar sammanslaget kolumn namn.](media/tutorial-data-flow-private/name-column.png)
1. Om du vill få medelvärdet för kolumn **klassificeringen** använder du ```avg()``` mängd funktionen. Eftersom **klassificeringen** är en sträng och ```avg()``` tar med numeriska värden måste vi konvertera värdet till ett tal via ```toInteger()``` funktionen. Detta uttryck ser ut så här:

    ```avg(toInteger(Rating))```

1. Välj **Spara och slutför** när du är klar.

    ![Skärm bild som visar hur du sparar mängden.](media/tutorial-data-flow-private/save-aggregate.png)
1. Gå till fliken **data förhands granskning** för att Visa transformationens utdata. Observera att endast två kolumner finns där, **år** och **AverageComedyRating**.

### <a name="add-the-sink-transformation"></a>Lägg till omvandlingen av mottagare

1. Sedan vill du lägga till en **Sink** -omvandling under **mål**.

    ![Skärm bild som visar hur du lägger till en mottagare.](media/tutorial-data-flow-private/add-sink.png)
1. Namnge din Sink- **mottagare**. Välj **ny** för att skapa din data uppsättning för mottagare.

    ![Skärm bild som visar hur du skapar en mottagare.](media/tutorial-data-flow-private/create-sink.png)
1. På sidan **ny data uppsättning** väljer du **Azure Data Lake Storage Gen2** och väljer sedan **Fortsätt**.

1. På sidan **Välj format** väljer du **DelimitedText** och väljer sedan **Fortsätt**.

1. Namnge din data uppsättning **MoviesSink**. För länkad tjänst väljer du samma **ADLSGen2** -länkade tjänst som du skapade för käll omvandling. Ange en mapp för utdata för att skriva dina data till. I den här självstudien ska vi skriva till mappens **utdata** i **exempel på container-data**. Mappen behöver inte finnas i förväg och kan skapas dynamiskt. Markera kryss rutan **första raden som rubrik** och välj **ingen** för **import schema**. Välj **OK**.

    ![Skärm bild som visar mottagarens sökväg.](media/tutorial-data-flow-private/sink-file-path.png)

Nu har du slutfört skapandet av ditt data flöde. Du är redo att köra den i din pipeline.

## <a name="run-and-monitor-the-data-flow"></a>Köra och övervaka data flödet

Du kan felsöka en pipeline innan du publicerar den. I det här steget utlöser du en fel söknings körning av data flödets pipeline. Medan data förhands granskningen inte skriver data skriver en fel söknings körning data till mottagar målet.

1. Gå till pipeline-arbetsytan. Välj **Felsök** för att utlösa en debug-körning.

1. Pipeline-felsökning av data flödes aktiviteter använder det aktiva fel söknings klustret men tar fortfarande minst en minut att initiera. Du kan följa förloppet via fliken **utdata** . När körningen är klar väljer du ikonen glasögon för körnings information.

1. På sidan information kan du se antalet rader och hur lång tid som har använts för varje omformnings steg.

    ![Skärm bild som visar en övervaknings körning.](media/tutorial-data-flow-private/run-details.png)
1. Välj en omvandling för att få detaljerad information om kolumner och partitionering av data.

Om du har följt den här självstudien korrekt bör du ha skrivit 83 rader och 2 kolumner i mottagar mappen. Du kan kontrol lera att informationen är korrekt genom att kontrol lera Blob Storage.

## <a name="summary"></a>Sammanfattning

I den här självstudien använde du Data Factory gränssnittet för att skapa en pipeline som kopierar och transformerar data från en Data Lake Storage Gen2 källa till en Data Lake Storage Gen2 mottagare (båda tillåter endast åtkomst till valda nätverk) genom att använda mappa data flöde i [Data Factory hanterade Virtual Network](managed-virtual-network-private-endpoint.md).