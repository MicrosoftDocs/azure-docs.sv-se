---
title: Dataintegrering med Azure Data Factory och Azure Data Share
description: Kopiera, transformera och dela data med Azure Data Factory och Azure Data Share
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 04/14/2021
ms.openlocfilehash: d3924c38f760a9698735a2757bdad2af5beb0e24
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518816"
---
# <a name="data-integration-using-azure-data-factory-and-azure-data-share"></a>Dataintegrering med Azure Data Factory och Azure Data Share

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

När kunderna går vidare med sina moderna informationslager- och analysprojekt behöver de inte bara mer data, utan även mer insyn i sina data i sin datae egendom. Den här workshoppen går in på hur förbättringar av Azure Data Factory och Azure Data Share förenklar dataintegrering och hantering i Azure. 

Från att aktivera kodfri ETL/ELT till att skapa en heltäckande vy över dina data, ger förbättringar i Azure Data Factory dina datatekniker möjlighet att på ett säkert sätt få in mer data och därmed mer värde för ditt företag. Azure Data Share gör att du kan dela företag till företag på ett reglerat sätt.

I den här workshoppen använder du Azure Data Factory (ADF) för att mata in data från Azure SQL Database till Azure Data Lake Storage Gen2 (ADLS Gen2). När du har landat data i laken transformerar du dem via mappning av dataflöden, datafabrikens interna transformeringstjänst och skickar dem till Azure Synapse Analytics. Sedan delar du tabellen med transformerade data tillsammans med ytterligare data med hjälp av Azure Data Share. 

De data som används i den här labblabbet är New York City-taxidata. Om du vill importera den till databasen i SQL Database laddar du ned [bacpac-filen taxi-data.](https://github.com/djpmsft/ADF_Labs/blob/master/sample-data/taxi-data.bacpac)

## <a name="prerequisites"></a>Förutsättningar

* **Azure-prenumeration**: Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

* **Azure SQL Database:** Om du inte har en SQL DB kan du lära dig hur du skapar [ett SQL DB-konto](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal)

* **Azure Data Lake Storage Gen2 lagringskonto:** Om du inte har ett ADLS Gen2 storage-konto kan du lära dig hur du skapar [ett ADLS Gen2-lagringskonto](../storage/common/storage-account-create.md).

* **Azure Synapse Analytics:** Om du inte har en Azure Synapse Analytics kan du lära dig hur du [skapar en Azure Synapse Analytics instans](../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md).

* **Azure Data Factory:** Om du inte har skapat en datafabrik kan du se hur du [skapar en datafabrik](./quickstart-create-data-factory-portal.md).

* **Azure Data Share:** Om du inte har skapat en dataresurs kan du se hur du [skapar en dataresurs](../data-share/share-your-data.md#create-a-data-share-account).

## <a name="set-up-your-azure-data-factory-environment"></a>Konfigurera din Azure Data Factory miljö

I det här avsnittet lär du dig att komma åt Azure Data Factory användarupplevelse (ADF UX) från Azure Portal. I ADF UX konfigurerar du tre länkade tjänster för vart och ett av de datalager som vi använder: Azure SQL DB, ADLS Gen2 och Azure Synapse Analytics.

I Azure Data Factory definierar länkade tjänster anslutningsinformationen till externa resurser. Azure Data Factory stöder för närvarande över 85 anslutningsappar.

### <a name="open-the-azure-data-factory-ux"></a>Öppna Azure Data Factory UX

1. Öppna [Azure Portal](https://portal.azure.com) antingen i Microsoft Edge eller Google Chrome.
1. Använd sökfältet längst upp på sidan och sök efter "Datafabriker"

    ![Portal 1](media/lab-data-flow-data-share/portal1.png)
1. Klicka på datafabriksresursen för att öppna dess resursblad.

    ![Portal 2](media/lab-data-flow-data-share/portal2.png)
1. Klicka på **Författare och övervakare** för att öppna ADF UX. ADF UX kan också nås på adf.azure.com.

    ![Portal 3](media/lab-data-flow-data-share/portal3.png)
1. Du omdirigeras till startsidan för ADF UX. Den här sidan innehåller snabbstarter, instruktionsvideor och länkar till självstudier för att lära dig datafabriksbegrepp. Börja redigera genom att klicka på pennikonen i det vänstra sidofältet.

    ![Konfigurera portalen](media/lab-data-flow-data-share/configure1.png)

### <a name="create-an-azure-sql-database-linked-service"></a>Skapa en länkad Azure SQL Database-tjänst

1. Om du vill skapa  en länkad tjänst väljer du  Hantera hubb i det vänstra sidofältet. I fönstret Anslutningar väljer du **Länkade** tjänster och sedan **Ny** för att lägga till en ny länkad tjänst.

    ![Portalkonfigurering 2](media/lab-data-flow-data-share/configure2.png)
1. Den första länkade tjänsten som du konfigurerar är en Azure SQL DB. Du kan använda sökfältet för att filtrera datalagerlistan. Klicka på panelen **Azure SQL Database** och klicka på Fortsätt.

    ![Portalkonfigurering 4](media/lab-data-flow-data-share/configure-4.png)
1. I konfigurationsfönstret för SQL DB anger du SQLDB som namn på den länkade tjänsten. Ange dina autentiseringsuppgifter för att tillåta att Data Factory ansluter till databasen. Om du använder SQL-autentisering anger du servernamnet, databasen, ditt användarnamn och lösenord. Du kan kontrollera att anslutningsinformationen är korrekt genom att klicka på **Testa anslutning.** Klicka **på Skapa** när du är klar.

    ![Konfigurera portalen 5](media/lab-data-flow-data-share/configure5.png)

### <a name="create-an-azure-synapse-analytics-linked-service"></a>Skapa en Azure Synapse Analytics länkad tjänst

1. Upprepa samma process för att lägga till en Azure Synapse Analytics länkad tjänst. På fliken anslutningar klickar du på **Ny.** Välj panelen **Azure Synapse Analytics** och klicka på Fortsätt.

    ![Portalkonfigurering 6](media/lab-data-flow-data-share/configure-6.png)
1. I konfigurationsfönstret för länkad tjänst anger du SQLDW som namn på den länkade tjänsten. Ange dina autentiseringsuppgifter för att tillåta att Data Factory ansluter till databasen. Om du använder SQL-autentisering anger du servernamnet, databasen, ditt användarnamn och lösenord. Du kan kontrollera att anslutningsinformationen är korrekt genom att klicka på **Testa anslutning.** Klicka **på Skapa** när du är klar.

    ![Portalkonfigurering 7](media/lab-data-flow-data-share/configure-7.png)

### <a name="create-an-azure-data-lake-storage-gen2-linked-service"></a>Skapa en Azure Data Lake Storage Gen2 länkad tjänst

1. Den senaste länkade tjänsten som behövs för den här labblabbet är Azure Data Lake Storage gen2.  På fliken anslutningar klickar du på **Ny.** Välj panelen **Azure Data Lake Storage Gen2** och klicka på Fortsätt.

    ![Portalkonfigurering 8](media/lab-data-flow-data-share/configure8.png)
1. I konfigurationsfönstret för länkad tjänst anger du "ADLSGen2" som namn på den länkade tjänsten. Om du använder autentisering med kontonyckel väljer du ADLS Gen2 lagringskonto i listrutan **Lagringskontonamn.** Du kan kontrollera att anslutningsinformationen är korrekt genom att klicka på **Testa anslutning.** Klicka **på Skapa** när du är klar.

    ![Portalkonfigurering 9](media/lab-data-flow-data-share/configure9.png)

### <a name="turn-on-data-flow-debug-mode"></a>Aktivera felsökningsläge för dataflöde

I avsnittet *Transformera data med mappningsdataflöde* skapar du mappningsdataflöden. En bra metod innan du skapar mappningsdataflöden är att aktivera felsökningsläge, vilket gör att du kan testa transformeringslogiken på några sekunder i ett aktivt Spark-kluster.

Om du vill aktivera felsökning klickar du på skjutreglaget **Dataflödesfelsökning** i det översta fältet på dataflödesarbetsytan eller pipelinearbetsytan när du har **Dataflödesaktiviteter.** Klicka på OK när bekräftelsedialogrutan visas. Det tar cirka 5–7 minuter att starta klustret. Fortsätt med att *mata in data från Azure SQL DB ADLS Gen2 att använda kopieringsaktiviteten* medan den initieras.

![Portalkonfigurering 10](media/lab-data-flow-data-share/configure10.png)

![Portal konfigurera 11](media/lab-data-flow-data-share/configure11.png)

## <a name="ingest-data-using-the-copy-activity"></a>Mata in data med kopieringsaktiviteten

I det här avsnittet skapar du en pipeline med en kopieringsaktivitet som matar in en tabell från en Azure SQL DB till ett ADLS Gen2-lagringskonto. Du lär dig hur du lägger till en pipeline, konfigurerar en datauppsättning och felsöker en pipeline via ADF UX. Konfigurationsmönstret som används i det här avsnittet kan användas för att kopiera från ett relationsdatalager till ett filbaserat datalager.

I Azure Data Factory är en pipeline en logisk gruppering av aktiviteter som tillsammans utför en uppgift. En aktivitet definierar en åtgärd som ska utföras på dina data. En datauppsättning pekar på de data som du vill använda i en länkad tjänst.

### <a name="create-a-pipeline-with-a-copy-activity"></a>Skapa en pipeline med en kopieringsaktivitet

1. I fönstret Fabriksresurser klickar du på plusikonen för att öppna den nya resursmenyn. Välj **Pipeline**.

    ![Portalkopia 1](media/lab-data-flow-data-share/copy1.png)
1. På fliken **Allmänt på** pipelinearbetsytan ger du pipelinen ett beskrivande namn, till exempel "IngestAndTransformData".

    ![Portalkopia 2](media/lab-data-flow-data-share/copy2.png)
1. I aktivitetsfönstret på pipelinearbetsytan  öppnar du flyttnings- och transformeringskopplingen och drar **aktiviteten Kopiera data** till arbetsytan. Ge kopieringsaktiviteten ett beskrivande namn, till exempel "IngestIntoADLS".

    ![Portalkopia 3](media/lab-data-flow-data-share/copy3.png)

### <a name="configure-azure-sql-db-source-dataset"></a>Konfigurera Azure SQL DB-källdatauppsättning

1. Klicka på **fliken Källa** för kopieringsaktiviteten. Om du vill skapa en ny datauppsättning klickar du **på Ny.** Källan blir tabellen "dbo". TripData finns i den länkade tjänsten "SQLDB" som konfigurerades tidigare.

    ![Portalkopia 4](media/lab-data-flow-data-share/copy4.png)
1. Sök efter **Azure SQL Database** och klicka på Fortsätt.

    ![Portalkopia 5](media/lab-data-flow-data-share/copy-5.png)
1. Anropa din datauppsättning "TripData". Välj SQLDB som länkad tjänst. Välj tabellnamnet 'dbo. TripData' från listrutan tabellnamn. Importera schemat från **anslutning/lagra**. Klicka på OK när du är klar.

    ![Portalkopia 6](media/lab-data-flow-data-share/copy6.png)

Du har skapat din källdatauppsättning. Se till att standardvärdet Tabell är markerat **i** fältet Använd fråga i källinställningarna.

### <a name="configure-adls-gen2-sink-dataset"></a>Konfigurera ADLS Gen2 datauppsättning för mottagare

1. Klicka på fliken **Mottagare** för kopieringsaktiviteten. Om du vill skapa en ny datauppsättning klickar du **på Ny.**

    ![Portalkopia 7](media/lab-data-flow-data-share/copy7.png)
1. Sök efter **Azure Data Lake Storage Gen2** och klicka på Fortsätt.

    ![Portalkopia 8](media/lab-data-flow-data-share/copy8.png)
1. I fönstret Välj format väljer du **DelimitedText** när du skriver till en csv-fil. Klicka på Fortsätt.

    ![Portalkopia 9](media/lab-data-flow-data-share/copy9.png)
1. Ge mottagaren namnet TripDataCSV. Välj ADLSGen2 som länkad tjänst. Ange var du vill skriva csv-filen. Du kan till exempel skriva dina data till filen `trip-data.csv` i containern `staging-container` . Ange **Första raden som rubrik till** sant eftersom du vill att dina utdata ska ha rubriker. Eftersom det inte finns någon fil i målet ännu anger du **Importera schema** till **Ingen.** Klicka på OK när du är klar.

    ![Portalkopia 10](media/lab-data-flow-data-share/copy10.png)

### <a name="test-the-copy-activity-with-a-pipeline-debug-run"></a>Testa kopieringsaktiviteten med en pipelinefelsökningskörning

1. Kontrollera att kopieringsaktiviteten fungerar korrekt genom att klicka på **Felsök** längst upp på pipelinearbetsytan för att köra en felsökningskörning. Med en felsökningskörning kan du testa pipelinen från ena änden till slutet eller tills en brytpunkt innan du publicerar den till datafabrikstjänsten.

    ![Portalkopia 11](media/lab-data-flow-data-share/copy11.png)
1. Om du vill övervaka felsökningskörningen går du **till fliken Utdata** på pipelinearbetsytan. Övervakningsskärmen uppdateras automatiskt var 20:e sekund eller när du klickar på uppdateringsknappen manuellt. Kopieringsaktiviteten har en särskild övervakningsvy som du kan komma åt genom att klicka på glasögonikonen i **kolumnen Åtgärder.**

    ![Portalkopia 12](media/lab-data-flow-data-share/copy12.png)
1. Kopieringsövervakningsvyn ger aktivitetens körningsinformation och prestandaegenskaper. Du kan se information som lästa/skrivna data, lästa/skrivna rader, lästa/skrivna filer och dataflöde. Om du har konfigurerat allt korrekt bör du se 49 999 rader skrivna i en fil i ADLS-mottagaren.

    ![Portalkopia 13](media/lab-data-flow-data-share/copy13.png)
1. Innan du går vidare till nästa avsnitt föreslår vi att du publicerar  ändringarna i datafabrikstjänsten genom att klicka på Publicera alla i fabriksfältet. Även om det inte tas upp i den här labb Azure Data Factory stöd för fullständig Git-integrering. Git-integrering möjliggör versionskontroll, iterativ besparing i en lagringsplats och samarbete på en datafabrik. Mer information finns i [källkontroll i Azure Data Factory](./source-control.md#troubleshooting-git-integration).

    ![Publicera på portalen 1](media/lab-data-flow-data-share/publish1.png)

## <a name="transform-data-using-mapping-data-flow"></a>Omvandla data med Mappa dataflöden

Nu när du har kopierat data till Azure Data Lake Storage är det dags att ansluta och aggregera dessa data till ett informationslager. Vi kommer att använda mappningsdataflöde Azure Data Factory är en visuellt utformad transformeringstjänst. Genom att mappa dataflöden kan användarna utveckla transformeringslogik utan kod och köra dem i Spark-kluster som hanteras av ADF-tjänsten.

Dataflödet som skapas i det här steget ansluter till datauppsättningen "TripDataCSV" som skapades i föregående avsnitt med en tabell "dbo. TripFares lagras i SQLDB baserat på fyra nyckelkolumner. Sedan aggregeras data baserat på kolumn för `payment_type` att beräkna medelvärdet för vissa fält och skrivas i en Azure Synapse Analytics tabell.

### <a name="add-a-data-flow-activity-to-your-pipeline"></a>Lägga till en dataflödesaktivitet i din pipeline

1. I aktivitetsfönstret på pipelinearbetsytan  öppnar du flyttnings- och transformeringskopplingen och drar **aktiviteten Dataflöde** till arbetsytan.

    ![Portaldataflöde 1](media/lab-data-flow-data-share/dataflow1.png)
1. I sidofönstret som öppnas väljer du **Skapa nytt dataflöde och** sedan **Mappa dataflöde.** Klicka på **OK**.

    ![Portaldataflöde 2](media/lab-data-flow-data-share/dataflow2.png)
1. Du dirigeras till dataflödesarbetsytan där du kommer att skapa din transformeringslogik. På fliken Allmänt ger du dataflödet namnet "JoinAndAggregateData".

    ![Portaldataflöde 3](media/lab-data-flow-data-share/dataflow3.png)

### <a name="configure-your-trip-data-csv-source"></a>Konfigurera din csv-källa för resedata

1. Det första du vill göra är att konfigurera dina två källtransformationer. Den första källan pekar på datauppsättningen TripDataCSV DelimitedText. Om du vill lägga till en källtransformering klickar **du på rutan Lägg** till källa på arbetsytan.

    ![Portaldataflöde 4](media/lab-data-flow-data-share/dataflow4.png)
1. Ge källan namnet TripDataCSV och välj datauppsättningen TripDataCSV från listrutan källa. Som du kanske kommer ihåg importerade du inte ett schema från början när du skapade den här datauppsättningen eftersom det inte fanns några data där. Eftersom `trip-data.csv` finns nu klickar du på Redigera **för** att gå till fliken datauppsättningsinställningar.

    ![Portaldataflöde 5](media/lab-data-flow-data-share/dataflow5.png)
1. Gå till fliken **Schema och** klicka på **Importera schema**. Välj **Från anslutning/lagring för** att importera direkt från filarkivet. 14 kolumner av typen sträng bör visas.

    ![Portaldataflöde 6](media/lab-data-flow-data-share/dataflow6.png)
1. Gå tillbaka till dataflödet JoinAndAggregateData. Om felsökningsklustret har startat (visas med en grön cirkel bredvid skjutreglaget för felsökning) kan du hämta en ögonblicksbild av data på **fliken Förhandsgranskning av** data. Klicka **på Uppdatera** för att hämta en dataförhandsgranskning.

    ![Portaldataflöde 7](media/lab-data-flow-data-share/dataflow7.png)

> [!Note]
> Dataförhandsgranskningen skriver inte data.

### <a name="configure-your-trip-fares-sql-db-source"></a>Konfigurera SQL DB-källan för dina reseresor

1. Den andra källan som du lägger till pekar på SQL DB-tabellen 'dbo. TripFares. Under din TripDataCSV-källa finns det en annan rutan **Lägg till** källa. Klicka på den för att lägga till en ny källtransformering.

    ![Portaldataflöde 8](media/lab-data-flow-data-share/dataflow8.png)
1. Ge den här källan namnet TripFaresSQL. Klicka **på Ny** bredvid fältet för källdatauppsättning för att skapa en ny SQL DB-datauppsättning.

    ![Portaldataflöde 9](media/lab-data-flow-data-share/dataflow9.png)
1. Välj panelen **Azure SQL Database** och klicka på Fortsätt. *Obs! Du kanske märker att många av anslutningarna i datafabriken inte stöds i mappning av dataflöden. Om du vill transformera data från någon av dessa källor matar du in dem till en källa som stöds med hjälp av kopieringsaktiviteten*.

    ![Portaldataflöde 10](media/lab-data-flow-data-share/dataflow-10.png)
1. Anropa datauppsättningen "TripFares". Välj SQLDB som länkad tjänst. Välj tabellnamnet 'dbo. TripFares' från listrutan tabellnamn. Importera schemat från **anslutning/lagra**. Klicka på OK när du är klar.

    ![Portaldataflöde 11](media/lab-data-flow-data-share/dataflow11.png)
1. Om du vill verifiera dina data hämtar du en dataförhandsgranskning på **fliken Dataförhandsgranskning.**

    ![Portaldataflöde 12](media/lab-data-flow-data-share/dataflow12.png)

### <a name="inner-join-tripdatacsv-and-tripfaressql"></a>Inre koppling TripDataCSV och TripFaresSQL

1. Om du vill lägga till en ny transformering klickar du på plusikonen i det nedre högra hörnet av TripDataCSV. Under **Flera indata/utdata väljer** du **Anslut**.

    ![Portalkoppling 1](media/lab-data-flow-data-share/join1.png)
1. Ge kopplingsomvandlingen namnet InnerJoinWithTripFares. Välj TripFaresSQL från den högra dataströms listrutan. Välj **Inre** som kopplingstyp. Mer information om de olika kopplingstyperna i mappningsdataflödet finns i [kopplingstyper.](./data-flow-join.md#join-types)

    Välj vilka kolumner som du vill matcha mot från varje dataström via **listrutan Kopplingsvillkor.** Om du vill lägga till ytterligare ett kopplingsvillkor klickar du på plusikonen bredvid ett befintligt villkor. Som standard kombineras alla kopplingsvillkor med en AND-operator, vilket innebär att alla villkor måste uppfyllas för en matchning. I den här labb labben vill vi matcha `medallion` kolumnerna `hack_license` , , `vendor_id` och `pickup_datetime`

    ![Portalkoppling 2](media/lab-data-flow-data-share/join2.png)
1. Kontrollera att du har anslutit 25 kolumner tillsammans med en dataförhandsgranskning.

    ![Portalkoppling 3](media/lab-data-flow-data-share/join3.png)

### <a name="aggregate-by-payment_type"></a>Aggregera efter payment_type

1. När du har slutfört din kopplingsomvandling lägger du till en aggregeringsomvandling genom att klicka på plusikonen bredvid InnerJoinWithTripFares. Välj **Aggregera** under **Schemamodifierare**.

    ![Portalagg 1](media/lab-data-flow-data-share/agg1.png)
1. Ge den sammanställda omvandlingen namnet "AggregateByPaymentType". Välj `payment_type` som gruppera efter kolumn.

    ![Portalagg 2](media/lab-data-flow-data-share/agg2.png)
1. Gå till **fliken Aggregat.** Här anger du två sammansättningar:
    * Genomsnittligt pris grupperat efter betalningstyp
    * Totalt reseavstånd grupperat efter betalningstyp

    Först skapar du det genomsnittliga prisuttrycket. I textrutan med etiketten Lägg **till eller välj en kolumn** anger du "average_fare".

    ![Portal agg 3](media/lab-data-flow-data-share/agg3.png)
1. Om du vill ange ett sammansättningsuttryck klickar du på den blå rutan med etiketten **Retur-uttryck.** Då öppnas dataflödesuttrycksverktyget, ett verktyg som används för att visuellt skapa dataflödesuttryck med hjälp av indataschema, inbyggda funktioner och åtgärder samt användardefinierade parametrar. Mer information om funktionerna i uttrycksverktyget finns i dokumentationen [för uttrycksverktyget.](./concepts-data-flow-expression-builder.md)

    Om du vill hämta genomsnittspriset använder du `avg()` sammansättningsfunktionen för att aggregera `total_amount` kolumnen som har ett heltal med `toInteger()` . I dataflödesuttrycksspråket definieras detta som `avg(toInteger(total_amount))` . Klicka **på Spara och** slutför när du är klar.

    ![Portalagg 4](media/lab-data-flow-data-share/agg4.png)
1. Om du vill lägga till ytterligare ett aggregeringsuttryck klickar du på plusikonen bredvid `average_fare` . Välj **Lägg till kolumn**.

    ![Portalagg 5](media/lab-data-flow-data-share/agg5.png)
1. I textrutan med etiketten Lägg **till eller välj en kolumn** anger du "total_trip_distance". Precis som i det sista steget öppnar du uttrycksverktyget för att ange i uttrycket.

    Om du vill hämta det totala reseavståndet använder du `sum()` sammansättningsfunktionen för att aggregera `trip_distance` kolumnen som kastar till ett heltal med `toInteger()` . I dataflödesuttrycksspråket definieras detta som `sum(toInteger(trip_distance))` . Klicka **på Spara och** slutför när du är klar.

    ![Portal agg 6](media/lab-data-flow-data-share/agg6.png)
1. Testa din transformeringslogik på **fliken Dataförhandsgranskning.** Som du ser finns det betydligt färre rader och kolumner än tidigare. Endast de tre grupperna efter och aggregeringskolumnerna som definierats i den här omvandlingen fortsätter nedströms. Eftersom det bara finns fem betalningstypgrupper i exemplet matas endast fem rader ut.

    ![Portalagg 7](media/lab-data-flow-data-share/agg7.png)

### <a name="configure-you-azure-synapse-analytics-sink"></a>Konfigurera Azure Synapse Analytics mottagare

1. Nu när vi har slutfört transformeringslogiken är vi redo att ta data till en Azure Synapse Analytics tabell. Lägg till en mottagartransformering under **avsnittet** Mål.

    ![Portal mottagare 1](media/lab-data-flow-data-share/sink1.png)
1. Ge mottagaren namnet "SQLDWSink". Klicka **på** Ny bredvid fältet för datauppsättningen för mottagare för att skapa en Azure Synapse Analytics datauppsättning.

    ![Portal mottagare 2](media/lab-data-flow-data-share/sink2.png)

1. Välj panelen **Azure Synapse Analytics** och klicka på Fortsätt.

    ![Portal mottagare 3](media/lab-data-flow-data-share/sink-3.png)
1. Anropa din datauppsättning "Aggregated DatasetData". Välj SQLDW som länkad tjänst. Välj **Skapa ny tabell** och ge den nya tabellen namnet dbo. AggregateRegeringData. Klicka på OK när du är klar

    ![Portal mottagare 4](media/lab-data-flow-data-share/sink4.png)
1. Gå till **fliken Inställningar** för mottagaren. Eftersom vi skapar en ny tabell måste vi välja **Återskapa tabell** under tabellåtgärden. Avmarkera **Aktivera mellanlagring,** vilket växlar om vi infogar rad för rad eller i batch.

    ![Portal mottagare 5](media/lab-data-flow-data-share/sink5.png)

Du har skapat ditt dataflöde. Nu är det dags att köra den i en pipeline-aktivitet.

### <a name="debug-your-pipeline-end-to-end"></a>Felsöka din pipeline från end-to-end

1. Gå tillbaka till fliken för **Pipelinen IngestAndTransformData.** Lägg märke till den gröna rutan för kopieringsaktiviteten "IngestIntoADLS". Dra den till dataflödesaktiviteten JoinAndAggregateData. Detta skapar ett "om åtgärden lyckades", vilket gör att dataflödesaktiviteten endast körs om kopieringen lyckas.

    ![Portalpipeline 1](media/lab-data-flow-data-share/pipeline1.png)
1. Som vi gjorde för kopieringsaktiviteten klickar du **på Felsök** för att köra en felsökningskörning. För felsökningskörningar använder dataflödesaktiviteten det aktiva felsökningsklustret i stället för att skapa ett nytt kluster. Den här pipelinen tar lite över en minut att köra.

    ![Portalpipeline 2](media/lab-data-flow-data-share/pipeline2.png)
1. Precis som kopieringsaktiviteten har dataflödet en särskild övervakningsvy som visas av glasögonikonen när aktiviteten har slutförts.

    ![Portalpipeline 3](media/lab-data-flow-data-share/pipeline3.png)
1. I övervakningsvyn visas ett förenklat dataflödesdiagram tillsammans med körningstider och rader i varje körningssteg. Om det görs på rätt sätt bör du ha sammanställt 49 999 rader i fem rader i den här aktiviteten.

    ![Portalpipeline 4](media/lab-data-flow-data-share/pipeline4.png)
1. Du kan klicka på en transformering för att få ytterligare information om dess körning, till exempel partitioneringsinformation och nya/uppdaterade/bort ignorerade kolumner.

    ![Portalpipeline 5](media/lab-data-flow-data-share/pipeline5.png)

Nu har du slutfört datafabriksdelen av den här övningen. Publicera dina resurser om du vill operationalisera dem med utlösare. Du har kört en pipeline som matade in data från Azure SQL Database till Azure Data Lake Storage med hjälp av kopieringsaktiviteten och aggregerade sedan dessa data till en Azure Synapse Analytics. Du kan kontrollera att data har skrivits genom att titta på SQL Server själv.

## <a name="share-data-using-azure-data-share"></a>Dela data med Azure Data Share

I det här avsnittet lär du dig att konfigurera en ny dataresurs med hjälp av Azure Portal. Detta innebär att du skapar en ny dataresurs som innehåller datauppsättningar från Azure Data Lake Store Gen2 och Azure Synapse Analytics. Sedan konfigurerar du ett schema för ögonblicksbilder, vilket ger datakonsumenterna ett alternativ för att automatiskt uppdatera data som delas med dem. Sedan bjuder du in mottagare till din dataresurs. 

När du har skapat en dataresurs växlar du sedan huvudena och blir *datakonsumenten*. Som datakonsument går du igenom flödet för att acceptera en dataresursinbjudan och konfigurerar var du vill att data ska tas emot och mappar datauppsättningar till olika lagringsplatser. Sedan utlöser du en ögonblicksbild som kopierar de data som delas med dig till det angivna målet. 

### <a name="sharing-data-data-provider-flow"></a>Dela data (dataleverantörsflöde)

1. Öppna Azure Portal antingen i Microsoft Edge eller Google Chrome.

1. Använd sökfältet längst upp på sidan och sök efter **dataresurser**

    ![Portalannonser](media/lab-data-flow-data-share/portal-ads.png)

1. Välj dataresurskontot med "Provider" i namnet. Till exempel **DataProvider0102**. 

1. Välj **Börja dela dina data**

    ![Börja dela](media/lab-data-flow-data-share/ads-start-sharing.png)

1. Välj **+ Skapa för** att börja konfigurera din nya dataresurs. 

1. Under **Resursnamn** anger du ett val av namn. Det här är resursnamnet som datakonsumenten kommer att se, så se till att ge den ett beskrivande namn, till exempel TaxiData.

1. Under **Beskrivning** lägger du till en mening som beskriver innehållet i dataresursen. Dataresursen innehåller data om taxiresor i hela världen som lagras i ett antal butiker, inklusive Azure Synapse Analytics och Azure Data Lake Store. 

1. Under **Användningsvillkor** anger du en uppsättning villkor som du vill att datakonsumenten ska följa. Några exempel är "Distribuera inte dessa data utanför din organisation" eller "Referera till ett juridiskt avtal". 

    ![Dela information](media/lab-data-flow-data-share/ads-details.png)

1. Välj **Fortsätt**. 

1. Välj **Lägg till datauppsättningar** 

    ![Lägg till datauppsättning 1](media/lab-data-flow-data-share/add-dataset.png)

1. Välj **Azure Synapse Analytics** att välja en tabell från Azure Synapse Analytics som dina ADF-transformationer landade i.

    ![Lägga till datauppsättnings-sql](media/lab-data-flow-data-share/add-dataset-sql.png)


1. Du får ett skript att köra innan du kan fortsätta. Skriptet som tillhandahålls skapar en användare i SQL-databasen så att Azure Data Share MSI kan autentisera för sin räkning. 

> [!IMPORTANT]
> Innan du kör skriptet måste du ange dig själv som Active Directory-administratör för SQL Server. 

1. Öppna en ny flik och gå till Azure Portal. Kopiera skriptet som tillhandahålls för att skapa en användare i databasen som du vill dela data från. Gör detta genom att logga in på EDW-databasen med Frågeutforskaren (förhandsversion) med AAD-autentisering. 

    Du måste ändra skriptet så att användaren som skapas finns inom hakparenteser. T.ex:
    
    skapa användare [dataprovider-xxxx] från extern inloggning;  exec sp_addrolemember db_owner, [dataprovider-xxxx];
    
1. Växla tillbaka till Azure Data Share där du lägger till datauppsättningar till din dataresurs. 

1. Välj **EDW** och sedan **AggregatedData** för tabellen. 

1. Välj **Lägg till datauppsättning**

    Nu har vi en SQL-tabell som ingår i vår datauppsättning. Nu ska vi lägga till ytterligare datauppsättningar från Azure Data Lake Store. 

1. Välj **Lägg till datauppsättning** och **välj Azure Data Lake Store Gen2**

    ![Lägga till adls för datauppsättning](media/lab-data-flow-data-share/add-dataset-adls.png)

1. Välj **Nästa**

1. Expandera *ww ww wwdata*. Expandera *Boston Taxi Data*. Observera att du kan dela ned till filnivån. 

1. Välj mappen *Boston Taxi Data (Boston Taxi-data)* för att lägga till hela mappen i dataresursen. 

1. Välj **Lägg till datauppsättningar**

1. Granska de datauppsättningar som har lagts till. Du bör ha en SQL-tabell och en ADLS Gen2-mapp som lagts till i dataresursen. 

1. Välj **Fortsätt**

1. På den här skärmen kan du lägga till mottagare i din dataresurs. Mottagarna som du lägger till får inbjudningar till din dataresurs. I den här övningen måste du lägga till två e-postadresser:

    1. E-postadressen till den Azure-prenumeration som du använder. 

        ![Lägga till mottagare](media/lab-data-flow-data-share/add-recipients.png)

    1. Lägg till i den fiktiva datakonsumenten med namnet *janedoe@fabrikam.com* .

1. På den här skärmen kan du konfigurera en ögonblicksbildsinställning för datakonsumenten. Detta gör att de kan ta emot regelbundna uppdateringar av dina data med ett intervall som definieras av dig. 

1. Kontrollera **Schemat för ögonblicksbilder** och konfigurera en uppdatering per timme av dina data med hjälp av *listrutan* Upprepning.  

1. Välj **Skapa**.

    Nu har du en aktiv dataresurs. Vi går igenom vad du kan se som dataleverantör när du skapar en dataresurs. 

1. Välj den dataresurs som du skapade, med namnet **DataProvider**. Du kan navigera till den genom att **välja Skickade resurser** i **Data Share**. 

1. Klicka på Schema för ögonblicksbild. Du kan inaktivera schemat för ögonblicksbilder om du väljer det. 

1. Välj sedan fliken **Datauppsättningar.** Du kan lägga till ytterligare datauppsättningar till den här dataresursen när den har skapats. 

1. Välj fliken **Dela** prenumerationer. Det finns ännu inga resursprenumerationer eftersom datakonsumenten ännu inte har accepterat din inbjudan.

1. Gå till **fliken Inbjudningar.** Här visas en lista över väntande inbjudningar. 

    ![Väntande inbjudningar](media/lab-data-flow-data-share/pending-invites.png)

1. Välj inbjudan till *janedoe@fabrikam.com* . Välj Ta bort. Om mottagaren inte har accepterat inbjudan än kommer han eller hon inte längre att kunna göra det. 

1. Välj **fliken Historik.** Inget visas ännu eftersom datakonsumenten ännu inte har accepterat din inbjudan och utlöst en ögonblicksbild. 

### <a name="receiving-data-data-consumer-flow"></a>Ta emot data (datakonsumentflöde)

Nu när vi har granskat vår dataresurs är vi redo att byta kontext och ha på oss vår datakonsumenthat. 

Nu bör du ha en Azure Data Share inbjudan i inkorgen från Microsoft Azure. Starta Outlook Web Access (outlook.com) och logga in med de autentiseringsuppgifter som angetts för din Azure-prenumeration.

I e-postmeddelandet som du borde ha fått klickar du på "Visa >". I det här läget kommer du att simulera datakonsumentupplevelsen när du godkänner en dataproviders inbjudan till deras dataresurs. 

![E-postinbjudan](media/lab-data-flow-data-share/email-invite.png)

Du kan uppmanas att välja en prenumeration. Se till att du väljer den prenumeration som du har arbetat i för den här övningen. 

1. Klicka på inbjudan *DataProvider*. 

1. På den här inbjudningsskärmen ser du olika detaljer om dataresursen som du konfigurerade tidigare som dataleverantör. Granska informationen och godkänn användningsvillkoren om de anges.

1. Välj den prenumeration och resursgrupp som redan finns för ditt labb. 

1. För **Dataresurskonto** väljer du **DataConsumer**. Du kan också skapa ett nytt dataresurskonto. 

1. Bredvid **Mottaget resursnamn** ser du att standardresursnamnet är det namn som angavs av dataleverantören. Ge resursen ett eget namn som beskriver de data som du kommer att ta emot, t.ex. **TaxiDataShare**.

    ![Inbjudan accepterar](media/lab-data-flow-data-share/consumer-accept.png)

1. Du kan välja att **Acceptera och konfigurera nu eller** Acceptera och konfigurera **senare.** Om du väljer att acceptera och konfigurera nu anger du ett lagringskonto där alla data ska kopieras. Om du väljer att godkänna och konfigurera senare kommer datauppsättningarna i resursen att ommappas och du måste mappa dem manuellt. Vi kommer att välja det senare. 

1. Välj **Acceptera och konfigurera senare.** 

    När du konfigurerar det här alternativet skapas en resursprenumeration, men det finns en möjlighet att data hamnar eftersom inget mål har mappats. 

    Nu ska vi konfigurera datauppsättningsmappningar för dataresursen. 

1. Välj Mottagen resurs (det namn som du angav i steg 5).

    **Utlösarögonblicksbild** är nedtonad men resursen är aktiv. 

1. Välj **fliken Datauppsättningar.** Observera att varje datauppsättning är Omappad, vilket innebär att den inte har något mål att kopiera data till. 

    ![omappade datauppsättningar](media/lab-data-flow-data-share/unmapped.png)

1. Välj Azure Synapse Analytics tabell och välj sedan **+ Mappa till mål.**

1. Välj listrutan Måldatatyp till höger  på skärmen. 

    Du kan mappa SQL-data till en mängd olika datalager. I det här fallet ska vi mappa till en Azure SQL Database.

    ![mapping](media/lab-data-flow-data-share/mapping-options.png)
    
    (Valfritt) Välj **Azure Data Lake Store Gen2** som måldatatyp. 
    
    (Valfritt) Välj den prenumeration, resursgrupp och det lagringskonto som du har arbetat med. 
    
    (Valfritt) Du kan välja att ta emot data till din datasjö i csv- eller parquet-format. 

1. Bredvid **Måldatatyp väljer** du Azure SQL Database. 

1. Välj den prenumeration, resursgrupp och det lagringskonto som du har arbetat med. 

    ![mappa till sql](media/lab-data-flow-data-share/map-to-sqldb.png)

1. Innan du kan fortsätta måste du skapa en ny användare i SQL Server genom att köra det angivna skriptet. Kopiera först skriptet till Urklipp. 

1. Öppna en ny Azure Portal fliken. Stäng inte den befintliga fliken eftersom du behöver gå tillbaka till den om en stund. 

1. På den nya fliken som du öppnade navigerar du till **SQL-databaser**.

1. Välj SQL-databasen (det bör bara finnas en i din prenumeration). Var noga med att inte välja informationslagret. 

1. Välj **Frågeredigeraren (förhandsversion)**

1. Använd AAD-autentisering för att logga in i frågeredigeraren. 

1. Kör frågan i dataresursen (kopierades till Urklipp i steg 14). 

    Med det här kommandot Azure Data Share-tjänsten använda hanterade identiteter för Azure-tjänster för att autentisera till SQL Server att kunna kopiera data till den. 

1. Gå tillbaka till den ursprungliga fliken och välj **Mappa för att ange som mål.**

1. Välj sedan mappen Azure Data Lake Gen2 som ingår i datauppsättningen och mappa den till ett Azure Blob Storage konto. 

    ![storage](media/lab-data-flow-data-share/storage-map.png)

    Nu när alla datauppsättningar har mappats är du redo att börja ta emot data från dataleverantören. 

    ![Mappade](media/lab-data-flow-data-share/all-mapped.png)
    
1. Välj **Information.** 

    Observera att **utlösarögonblicksbild** inte längre är nedtonad, eftersom dataresursen nu har mål att kopiera till.

1. Välj Utlösarögonblicksbild > fullständig kopia. 

    ![Utlösa](media/lab-data-flow-data-share/trigger-full.png)

    Detta börjar kopiera data till ditt nya dataresurskonto. I ett verkligt scenario skulle dessa data komma från en tredje part. 

    Det tar cirka 3–5 minuter för data att komma fram. Du kan övervaka förloppet genom att klicka på **fliken Historik.** 

    Medan du väntar går du till den ursprungliga dataresursen (DataProvider) och visar status för fliken **Dela prenumerationer** **och historik.** Observera att det nu finns en aktiv prenumeration och som dataleverantör kan du även övervaka när datakonsumenten har börjat ta emot data som delas med dem. 

1. Gå tillbaka till datakonsumentens dataresurs. När utlösaren har status navigerar du till SQL-måldatabasen och datasjön för att se att data har landat i respektive lager. 

Grattis, du har slutfört labbet!