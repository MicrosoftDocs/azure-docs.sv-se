---
title: Transformera data med ett Azure Data Factory dataflöde för mappning av hanterade virtuella nätverk
description: Den här självstudien innehåller stegvisa instruktioner för hur du använder Azure Data Factory för att transformera data med mappning av dataflöden.
author: dcstwh
ms.author: weetok
ms.reviewer: makromer
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/14/2021
ms.openlocfilehash: ad101bee84256662d1436ba8d8a49304aecb9129
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518290"
---
# <a name="transform-data-securely-by-using-mapping-data-flow"></a>Transformera data på ett säkert sätt med hjälp av mappning av dataflöde

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Om du inte har använt Azure Data Factory tidigare kan du läsa [Introduktion till Azure Data Factory](./introduction.md).

I den här självstudien använder du användargränssnittet för Data Factory för att skapa en pipeline som kopierar och transformerar data från en Azure Data Lake Storage Gen2-källa till en *Data Lake Storage Gen2-mottagare (båda* tillåter åtkomst till endast valda nätverk) med hjälp av mappningsdataflödet i [Data Factory Managed Virtual Network](managed-virtual-network-private-endpoint.md). Du kan utöka konfigurationsmönstret i den här självstudien när du transformerar data med hjälp av mappning av dataflöde.

I den här självstudien gör du följande:

> [!div class="checklist"]
>
> * Skapa en datafabrik.
> * Skapa en pipeline med en dataflödesaktivitet.
> * Skapa ett mappningsdataflöde med fyra transformningar.
> * Testkör pipelinen.
> * Övervaka en dataflödesaktivitet.

## <a name="prerequisites"></a>Förutsättningar

* **Azure-prenumeration**. Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt Azure-konto](https://azure.microsoft.com/free/) innan du börjar.
* **Azure Storage-konto**. Du använder Data Lake Storage som *käll- och* *mottagare för* datalager. Om du inte har ett lagringskonto finns det anvisningar om hur du skapar ett i [Skapa ett Azure Storage-konto](../storage/common/storage-account-create.md?tabs=azure-portal). *Se till att lagringskontot endast tillåter åtkomst från valda nätverk.* 

Filen som vi transformerar i den här självstudien är moviesDB.csv som finns på den här [GitHub-innehållswebbplatsen](https://raw.githubusercontent.com/djpmsft/adf-ready-demo/master/moviesDB.csv). Om du vill hämta filen från GitHub kopierar du innehållet till en valfri textredigerare för att spara den lokalt som en CSV-fil. Information om hur du laddar upp filen till ditt [lagringskonto finns i Ladda upp blobar med Azure Portal](../storage/blobs/storage-quickstart-blobs-portal.md). Exemplen refererar till en container med **namnet sample-data**.

## <a name="create-a-data-factory"></a>Skapa en datafabrik

I det här steget skapar du en datafabrik och öppnar Data Factory användargränssnittet för att skapa en pipeline i datafabriken.

1. Öppna Microsoft Edge eller Google Chrome. För närvarande stöder Microsoft Edge webbläsare och Google Chrome Data Factory användargränssnitt.
1. På den vänstra menyn väljer du **Skapa en resurs Analytics**  >    >  **Data Factory**.
1. I fönstret **Ny datafabrik**, under **Namn** anger du **ADFTutorialDataFactory**.

   Namnet på datafabriken måste *vara globalt unikt.* Om du får ett felmeddelande om namnvärdet anger du ett annat namn för datafabriken (till exempel dittnamnADFTutorialDataFactory). Se artikeln [Namnregler för Data Factory](naming-rules.md) för namnregler för Data Factory-artefakter.

1. Välj den Azure-**prenumeration** som du vill skapa den nya datafabriken i.
1. Gör något av följande för **Resursgrupp**:

    * Välj **Använd befintlig** och välj en befintlig resursgrupp i listrutan.
    * Välj **Skapa ny** och ange namnet på en resursgrupp. 
         
    Mer information om resursgrupper finns i [Använda resursgrupper för att hantera Azure-resurser](../azure-resource-manager/management/overview.md). 
1. Under **Version** väljer du **V2**.
1. Under **Plats** väljer du en plats för datafabriken. Endast platser som stöds visas i listrutan. Datalager (till exempel Azure Storage och Azure SQL Database) och beräkningar (till exempel Azure HDInsight) som används av datafabriken kan finnas i andra regioner.

1. Välj **Skapa**.
1. När skapandet är klart visas meddelandet i meddelandecentret. Välj **Gå till resurs** för att gå **Data Factory** sidan.
1. Klicka på **Författare och övervakare** för att starta användargränssnittet för datafabriken på en separat flik.

## <a name="create-an-azure-ir-in-data-factory-managed-virtual-network"></a>Skapa en Azure IR i Data Factory Managed Virtual Network

I det här steget skapar du en Azure IR och aktiverar Data Factory Managed Virtual Network.

1. I Data Factory portal går du till **Hantera och** väljer Ny **för** att skapa en ny Azure IR.

   ![Skärmbild som visar hur du skapar en ny Azure IR.](./media/tutorial-copy-data-portal-private/create-new-azure-ir.png)
1. På **installationssidan för Integration Runtime** väljer du vilken Integration Runtime som ska skapas baserat på nödvändiga funktioner. I den här självstudien **väljer du Azure, Egen värd och** klickar sedan på **Fortsätt.** 
1. Välj **Azure** och klicka sedan på **Fortsätt för** att skapa en Azure Integration Runtime.

   ![Skärmbild som visar en ny Azure IR.](./media/tutorial-copy-data-portal-private/azure-ir.png)

1. Under **Konfiguration av virtuellt nätverk (förhandsversion)** väljer du **Aktivera**.

   ![Skärmbild som visar aktivering av en ny Azure IR.](./media/tutorial-copy-data-portal-private/enable-managed-vnet.png)

1. Välj **Skapa**.

## <a name="create-a-pipeline-with-a-data-flow-activity"></a>Skapa en pipeline med en dataflödesaktivitet

I det här steget skapar du en pipeline som innehåller en dataflödesaktivitet.

1. På sidan **Nu sätter vi igång** väljer du **Skapa pipeline**.

   ![Skärmbild som visar hur du skapar en pipeline.](./media/doc-common-process/get-started-page.png)

1. I egenskapsfönstret för pipelinen anger du **TransformMovies** som pipelinenamn.
1. Expandera Flytta **och** transformera i **fönstret Aktiviteter.** Dra **Dataflöde** aktiviteten från fönstret till pipelinearbetsytan.

1. I **popup-menyn Lägga till** dataflöde väljer **du Skapa nytt dataflöde** och väljer **sedan Mappa Dataflöde**. Välj **OK** när du är klar.

    ![Skärmbild som visar Dataflöde.](media/tutorial-data-flow-private/mapping-dataflow.png)

1. Ge dataflödet namnet **TransformMovies** i egenskapsfönstret.
1. I det översta fältet på pipelinearbetsytan drar du Dataflöde **skjutreglaget** för felsökning. Felsökningsläge möjliggör interaktiv testning av transformeringslogik mot ett spark-livekluster. Dataflöde tar 5–7 minuter att värma upp och användarna rekommenderas att aktivera felsökning först om de planerar att Dataflöde utveckling. Mer information finns i [Felsökningsläge.](concepts-data-flow-debug-mode.md)

    ![Skärmbild som visar skjutreglaget Dataflödesfelsökning.](media/tutorial-data-flow-private/dataflow-debug.png)

## <a name="build-transformation-logic-in-the-data-flow-canvas"></a>Skapa transformeringslogik på dataflödesarbetsytan

När du har skapat ditt dataflöde skickas du automatiskt till dataflödesarbetsytan. I det här steget skapar du ett dataflöde som tar moviesDB.csv-filen i Data Lake Storage och aggregerar det genomsnittliga omdömet för comedies från 1910 till 2000. Sedan skriver du tillbaka den här filen till Data Lake Storage.

### <a name="add-the-source-transformation"></a>Lägga till källtransformering

I det här steget ska du konfigurera Data Lake Storage Gen2 som källa.

1. På dataflödesarbetsytan lägger du till en källa genom att **välja rutan Lägg till** källa.

1. Ge källan namnet **MoviesDB**. Välj **Ny för** att skapa en ny källdatauppsättning.

1. Välj **Azure Data Lake Storage Gen2** och välj sedan **Fortsätt.**

1. Välj **DelimitedText** och välj sedan **Fortsätt.**

1. Ge datauppsättningen **namnet MoviesDB**. I listrutan länkad tjänst väljer du **Nytt.**

1. På skärmen för skapande av länkad tjänst ger du Data Lake Storage Gen2 länkade **tjänsten ADLSGen2** och anger din autentiseringsmetod. Ange sedan autentiseringsuppgifterna för anslutningen. I den här självstudien använder vi **kontonyckeln för** att ansluta till vårt lagringskonto. 

1. Kontrollera att du aktiverar **interaktiv redigering.** Det kan ta någon minut att aktiveras.

    ![Skärmbild som visar interaktiv redigering.](./media/tutorial-data-flow-private/interactive-authoring.png)

1. Välj **Testanslutning**. Det bör misslyckas eftersom lagringskontot inte aktiverar åtkomst till det utan att skapa och godkänna en privat slutpunkt. I felmeddelandet bör du se en länk för att skapa en privat slutpunkt som du kan följa för att skapa en hanterad privat slutpunkt. Ett alternativ är att gå direkt till fliken **Hantera och** följa anvisningarna i det här avsnittet [för](#create-a-managed-private-endpoint) att skapa en hanterad privat slutpunkt.

1. Låt dialogrutan vara öppen och gå sedan till ditt lagringskonto.

1. Följ anvisningarna i [det här avsnittet](#approval-of-a-private-link-in-a-storage-account) för att godkänna den privata länken.

1. Gå tillbaka till dialogrutan. Välj **Testa anslutning** igen och välj Skapa för **att** distribuera den länkade tjänsten.

1. På skärmen för att skapa datauppsättning anger du var filen finns under **fältet Filsökväg.** I den här självstudien finns moviesDB.csv filen i **containern sample-data**. Eftersom filen har rubriker markerar du kryssrutan **Första raden som** rubrik. Välj **Från anslutning/arkiv för** att importera huvudschemat direkt från filen i lagringen. Välj **OK** när du är klar.

    ![Skärmbild som visar källsökvägen.](media/tutorial-data-flow-private/source-file-path.png)

1. Om felsökningsklustret har startat går du till **fliken Dataförhandsvisning** för källtransformeringen och väljer **Uppdatera** för att hämta en ögonblicksbild av data. Du kan använda dataförhandsvisningen för att verifiera att omvandlingen är korrekt konfigurerad.

    ![Skärmbild som visar fliken Dataförhandsgranskning.](media/tutorial-data-flow-private/data-preview.png)

#### <a name="create-a-managed-private-endpoint"></a>Skapa en hanterad privat slutpunkt

Om du inte använder hyperlänken när du testade föregående anslutning följer du sökvägen. Nu måste du skapa en hanterad privat slutpunkt som du ska ansluta till den länkade tjänst som du skapade.

1. Gå till **fliken** Hantera.

   > [!NOTE]
   > Fliken **Hantera** kanske inte är tillgänglig för alla Data Factory instanser. Om du inte ser den kan du komma åt privata slutpunkter genom att välja **Skapa**  >  **anslutningar privat**  >  **slutpunkt.**

1. Gå till **avsnittet Hanterade privata slutpunkter.**
1. Välj **+ Ny** under **Hanterade privata slutpunkter.**

    ![Skärmbild som visar knappen Hanterade privata slutpunkter Ny.](./media/tutorial-data-flow-private/new-managed-private-endpoint.png) 

1. Välj **Azure Data Lake Storage Gen2** panelen i listan och välj **Fortsätt.**
1. Ange namnet på det lagringskonto som du skapade.
1. Välj **Skapa**.
1. Efter några sekunder bör du se att den privata länk som skapas behöver ett godkännande.
1. Välj den privata slutpunkt som du skapade. Du kan se en hyperlänk som leder till att du godkänner den privata slutpunkten på lagringskontonivå.

    ![Skärmbild som visar fönstret Hantera privat slutpunkt.](./media/tutorial-data-flow-private/manage-private-endpoint.png) 

#### <a name="approval-of-a-private-link-in-a-storage-account"></a>Godkännande av en privat länk i ett lagringskonto

1. I lagringskontot går du till **Privata slutpunktsanslutningar** under **avsnittet** Inställningar.

1. Markera kryssrutan för den privata slutpunkt som du skapade och välj **Godkänn.**

    ![Skärmbild som visar knappen Godkänn för privat slutpunkt.](./media/tutorial-data-flow-private/approve-private-endpoint.png)

1. Lägg till en beskrivning och välj **ja.**
1. Gå tillbaka till **avsnittet Hanterade privata slutpunkter** på **fliken** Hantera i Data Factory.
1. Efter ungefär en minut bör du se godkännande för din privata slutpunkt.

### <a name="add-the-filter-transformation"></a>Lägga till filtertransformering

1. Bredvid källnoden på dataflödesarbetsytan väljer du plusikonen för att lägga till en ny transformering. Den första transformeringen som du lägger till är ett **Filter**.

    ![Skärmbild som visar tillägg av ett filter.](media/tutorial-data-flow-private/add-filter.png)
1. Ge filtertransformeringen **namnet FilterYears**. Välj uttrycksrutan bredvid Filtrera på **för att** öppna uttrycksverktyget. Här anger du filtreringsvillkoret.

    ![Skärmbild som visar FilterYears.](media/tutorial-data-flow-private/filter-years.png)
1. Med uttrycksverktyget för dataflöden kan du interaktivt skapa uttryck som ska användas i olika transformationer. Uttryck kan innehålla inbyggda funktioner, kolumner från indataschemat och användardefinierade parametrar. Mer information om hur du skapar uttryck finns i [Dataflödesuttrycksbyggare.](./concepts-data-flow-expression-builder.md)

    * I den här självstudien vill du filtrera filmer i genren som utspelade sig mellan åren 1910 och 2000. Eftersom året för närvarande är en sträng måste du konvertera det till ett heltal med hjälp av ```toInteger()``` funktionen . Använd operatorerna större än eller lika med (>=) och mindre än eller lika med (<=) för att jämföra med literalårsvärdena 1910 och 2000. Sammanför dessa uttryck med operatorn och (&&). Uttrycket kommer ut som:

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000```

    * Om du vill ta reda på vilka filmer som är komiker kan du använda funktionen för att ```rlike()``` hitta mönstret "Gör" i kolumngenreerna. Union the rlike expression with the year comparison to get:

        ```toInteger(year) >= 1910 && toInteger(year) <= 2000 && rlike(genres, 'Comedy')```

    * Om du har ett aktivt felsökningskluster kan  du verifiera logiken genom att välja Uppdatera för att se utdata för uttrycket jämfört med de indata som används. Det finns fler än ett rätt svar på hur du kan åstadkomma den här logiken med hjälp av språket för dataflödesuttryck.

        ![Skärmbild som visar filteruttrycket.](media/tutorial-data-flow-private/filter-expression.png)

    * Välj **Spara och slutför** när du är klar med uttrycket.

1. Hämta en **dataförhandsgranskning** för att verifiera att filtret fungerar korrekt.

    ![Skärmbild som visar den filtrerade dataförhandsvisningen.](media/tutorial-data-flow-private/filter-data.png)

### <a name="add-the-aggregate-transformation"></a>Lägga till aggregeringstransformeringen

1. Nästa transformering som du lägger till är en **aggregeringsomvandling** under **Schemamodifierare**.

    ![Skärmbild som visar hur du lägger till aggregeringen.](media/tutorial-data-flow-private/add-aggregate.png)
1. Ge **aggregeringsomvandlingen namnet AggregateComedyRating**. På fliken **Gruppera efter** väljer du **år** i listrutan för att gruppera sammansättningarna efter det år som filmen kom ut.

    ![Skärmbild som visar aggregeringsgruppen.](media/tutorial-data-flow-private/group-by-year.png)
1. Gå till **fliken Aggregat.** I den vänstra textrutan ger du aggregeringskolumnen **namnet AverageComedyRating**. Välj rätt uttrycksruta för att ange mängduttrycket via uttrycksverktyget.

    ![Skärmbild som visar samlingskolumnens namn.](media/tutorial-data-flow-private/name-column.png)
1. Använd mängdfunktionen för **att få medelvärdet** av ```avg()``` kolumnklassificering. Eftersom **Omdöme** är en sträng ```avg()``` och tar in numeriska indata måste vi konvertera värdet till ett tal via funktionen ```toInteger()``` . Det här uttrycket ser ut så här:

    ```avg(toInteger(Rating))```

1. Välj **Spara och slutför** när du är klar.

    ![Skärmbild som visar hur du sparar aggregeringen.](media/tutorial-data-flow-private/save-aggregate.png)
1. Gå till fliken **Dataförhandsgranskning** för att visa transformeringsutdata. Observera att endast två kolumner finns där, **year** och **AverageComedyRating**.

### <a name="add-the-sink-transformation"></a>Lägg till omvandlingen av mottagaren

1. Därefter vill du lägga till en **Sink-transformering** under **Mål**.

    ![Skärmbild som visar tillägg av en mottagare.](media/tutorial-data-flow-private/add-sink.png)
1. Ge mottagaren namnet **Sink**. Välj **Ny för** att skapa datauppsättningen för mottagare.

    ![Skärmbild som visar hur du skapar en mottagare.](media/tutorial-data-flow-private/create-sink.png)
1. På sidan **Ny datauppsättning** väljer du **Azure Data Lake Storage Gen2** och väljer sedan **Fortsätt.**

1. På sidan **Välj format** väljer du **DelimitedText och** sedan **Fortsätt.**

1. Namnge din datauppsättning för **mottagare FilmerSink**. För länkad tjänst väljer du samma **ADLSGen2-länkade** tjänst som du skapade för källtransformering. Ange en utdatamapp att skriva dina data till. I den här självstudien skriver vi till **mapputdata** i **containern sample-data**. Mappen behöver inte finnas i förväg och kan skapas dynamiskt. Markera kryssrutan **Första raden som rubrik** och välj Ingen **för** **Importera schema**. Välj **OK**.

    ![Skärmbild som visar sökvägen till mottagaren.](media/tutorial-data-flow-private/sink-file-path.png)

Nu har du skapat ditt dataflöde. Du är redo att köra den i din pipeline.

## <a name="run-and-monitor-the-data-flow"></a>Köra och övervaka dataflödet

Du kan felsöka en pipeline innan du publicerar den. I det här steget utlöser du en felsökningskörning av dataflödespipelinen. Även om dataförhandsvisningen inte skriver data skriver en felsökningskörning data till mottagarmålet.

1. Gå till pipelinearbetsytan. Välj **Felsök för** att utlösa en felsökningskörning.

1. Pipeline-felsökning av dataflödesaktiviteter använder det aktiva felsökningsklustret, men det tar ändå minst en minut att initiera. Du kan följa förloppet via **fliken Utdata.** När körningen är lyckad väljer du glasögonikonen för körningsinformation.

1. På informationssidan kan du se antalet rader och den tid som ägnats åt varje transformeringssteg.

    ![Skärmbild som visar en övervakningskörning.](media/tutorial-data-flow-private/run-details.png)
1. Välj en transformering för att få detaljerad information om kolumnerna och partitionering av data.

Om du har följt den här självstudien korrekt bör du ha skrivit 83 rader och 2 kolumner i mappen för mottagare. Du kan kontrollera att data är korrekta genom att kontrollera bloblagringen.

## <a name="summary"></a>Sammanfattning

I den här självstudien använde du användargränssnittet för Data Factory för att skapa en pipeline som kopierar och transformerar data från en Data Lake Storage Gen2-källa till en Data Lake Storage Gen2-mottagare (båda ger åtkomst till endast valda nätverk) med hjälp av mappningsdataflödet i [Data Factory Managed Virtual Network](managed-virtual-network-private-endpoint.md).