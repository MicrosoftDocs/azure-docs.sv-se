---
title: Data integrering med Azure Data Factory och Azure-Dataresurs
description: Kopiera, transformera och dela data med Azure Data Factory och Azure-dataresursen
author: dcstwh
ms.author: weetok
ms.service: data-factory
ms.topic: tutorial
ms.custom: seo-lt-2019
ms.date: 12/09/2020
ms.openlocfilehash: fa424f7e1f5e1f885dd433b8abc8aae1dc1bc206
ms.sourcegitcommit: 273c04022b0145aeab68eb6695b99944ac923465
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2020
ms.locfileid: "97006205"
---
# <a name="data-integration-using-azure-data-factory-and-azure-data-share"></a>Data integrering med Azure Data Factory och Azure-Dataresurs

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

När kunderna går in på sina moderna informations lager och analys projekt, behöver de inte bara fler data, utan också bättre insyn i deras data över sin datafastighet. Den här workshopen dykningar i hur förbättringar av Azure Data Factory och Azure Data Share fören klar data integrering och hantering i Azure. 

Genom att aktivera kod fri ETL/ELT för att skapa en omfattande vy över dina data, kan förbättringar i Azure Data Factory hjälpa dina data tekniker att på ett säkert sätt få mer data, och därmed mer värde, till ditt företag. Med Azure Data Share kan du göra affärer till företags delning på ett styrt sätt.

I den här workshopen använder du Azure Data Factory (ADF) för att mata in data från Azure SQL Database till Azure Data Lake Storage Gen2 (ADLS Gen2). När du har landat data i sjön kommer du att omvandla det genom att mappa data flöden, Data Factory: s inbyggda omvandlings tjänst och dela in dem i Azure Synapse Analytics. Sedan delar du tabellen med transformerade data tillsammans med ytterligare data med hjälp av Azure Data Share. 

De data som används i det här labbet är New York taxi-data. Om du vill importera den till databasen i SQL Database laddar du ned [taxi-data BACPAC-filen](https://github.com/djpmsft/ADF_Labs/blob/master/sample-data/taxi-data.bacpac).

## <a name="prerequisites"></a>Förutsättningar

* **Azure-prenumeration**: Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.

* **Azure SQL Database**: om du inte har en SQL-databas kan du läsa om hur du [skapar ett SQL DB-konto](../azure-sql/database/single-database-create-quickstart.md?tabs=azure-portal)

* **Azure Data Lake Storage Gen2 lagrings konto**: om du inte har ett ADLS Gen2 lagrings konto kan du läsa om hur du [skapar ett ADLS Gen2 lagrings konto](../storage/common/storage-account-create.md).

* **Azure Synapse Analytics**: om du inte har en Azure Synapse-analys kan du läsa om hur du [skapar en Azure Synapse Analytics-instans](../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md).

* **Azure Data Factory**: om du inte har skapat en data fabrik, se så här [skapar du en data fabrik](./quickstart-create-data-factory-portal.md).

* **Azure-Data resurs**: om du inte har skapat en data resurs, se så här [skapar du en data resurs](../data-share/share-your-data.md#create-a-data-share-account).

## <a name="set-up-your-azure-data-factory-environment"></a>Konfigurera din Azure Data Factorys miljö

I det här avsnittet får du lära dig hur du får åtkomst till Azure Data Factory användar upplevelse (ADF UX) från Azure Portal. När du har använt ADF-UX konfigurerar du tre länkade tjänster för varje data lager som vi använder: Azure SQL DB, ADLS Gen2 och Azure Synapse Analytics.

I Azure Data Factory länkade tjänster definierar du anslutnings informationen för externa resurser. Azure Data Factory stöder för närvarande över 85-kopplingar.

### <a name="open-the-azure-data-factory-ux"></a>Öppna Azure Data Factory UX

1. Öppna [Azure Portal](https://portal.azure.com) i antingen Microsoft Edge eller Google Chrome.
1. Använd Sök fältet högst upp på sidan och Sök efter "data fabriker"

    ![Portal 1](media/lab-data-flow-data-share/portal1.png)
1. Öppna resurs bladet genom att klicka på Data Factory-resursen.

    ![Portal 2](media/lab-data-flow-data-share/portal2.png)
1. Klicka på **författare och övervakare** för att öppna ADF-UX. Du kan också använda ADF-UX på adf.azure.com.

    ![Portal 3](media/lab-data-flow-data-share/portal3.png)
1. Du omdirigeras till start sidan för ADF-UX. Den här sidan innehåller snabb starter, instruktions videoklipp och länkar till självstudier för att lära dig Data Factory-koncept. Om du vill börja redigera klickar du på Penn ikonen i det vänstra sid fältet.

    ![Konfigurera Portal](media/lab-data-flow-data-share/configure1.png)

### <a name="create-an-azure-sql-database-linked-service"></a>Skapa en länkad Azure SQL Database-tjänst

1. På sidan redigering kan du skapa Data Factory-resurser som pipelines, data uppsättningar, data flöden, utlösare och länkade tjänster. Om du vill skapa en länkad tjänst klickar du på knappen **anslutningar** i det nedre högra hörnet.

    ![Portal konfigurera 2](media/lab-data-flow-data-share/configure2.png)
1. På fliken anslutningar klickar du på **ny** för att lägga till en ny länkad tjänst.

    ![Portal konfigurera 3](media/lab-data-flow-data-share/configure3.png)
1. Den första länkade tjänsten du konfigurerar är en Azure SQL-databas. Du kan använda Sök fältet för att filtrera data lager listan. Klicka på panelen **Azure SQL Database** och klicka på Fortsätt.

    ![Portal konfigurera 4](media/lab-data-flow-data-share/configure-4.png)
1. I fönstret SQL DB-konfiguration anger du "SQLDB" som namnet på den länkade tjänsten. Ange dina autentiseringsuppgifter så att Data Factory kan ansluta till databasen. Om du använder SQL-autentisering anger du namnet på servern, databasen, ditt användar namn och lösen ord. Du kan kontrol lera att anslutnings informationen är korrekt genom att klicka på **Testa anslutning**. Klicka på **skapa** när du är färdig.

    ![Portal konfigurera 5](media/lab-data-flow-data-share/configure5.png)

### <a name="create-an-azure-synapse-analytics-linked-service"></a>Skapa en länkad Azure Synapse Analytics-tjänst

1. Upprepa samma process för att lägga till en länkad Azure Synapse Analytics-tjänst. På fliken anslutningar klickar du på **ny**. Välj panelen **Azure Synapse Analytics** och klicka på Fortsätt.

    ![Portal konfigurera 6](media/lab-data-flow-data-share/configure-6.png)
1. I fönstret konfiguration av länkad tjänst anger du "SQLDW" som namnet på den länkade tjänsten. Ange dina autentiseringsuppgifter så att Data Factory kan ansluta till databasen. Om du använder SQL-autentisering anger du namnet på servern, databasen, ditt användar namn och lösen ord. Du kan kontrol lera att anslutnings informationen är korrekt genom att klicka på **Testa anslutning**. Klicka på **skapa** när du är färdig.

    ![Portal konfigurera 7](media/lab-data-flow-data-share/configure-7.png)

### <a name="create-an-azure-data-lake-storage-gen2-linked-service"></a>Skapa en Azure Data Lake Storage Gen2 länkad tjänst

1. Den senaste länkade tjänsten som krävs för den här övningen är en Azure Data Lake Storage Gen2.  På fliken anslutningar klickar du på **ny**. Välj panelen **Azure Data Lake Storage Gen2** och klicka på Fortsätt.

    ![Portal konfigurera 8](media/lab-data-flow-data-share/configure8.png)
1. I fönstret konfiguration av länkad tjänst anger du "ADLSGen2" som namnet på den länkade tjänsten. Om du använder autentisering med konto nycklar väljer du ADLS Gen2 lagrings konto i list rutan **lagrings konto namn** . Du kan kontrol lera att anslutnings informationen är korrekt genom att klicka på **Testa anslutning**. Klicka på **skapa** när du är färdig.

    ![Portal konfigurera 9](media/lab-data-flow-data-share/configure9.png)

### <a name="turn-on-data-flow-debug-mode"></a>Aktivera fel söknings läge för data flöde

I avsnittet *transformera data med hjälp av data flöde för mappning* kommer du att skapa mappnings data flöden. Vi rekommenderar att du aktiverar fel söknings läge innan du skapar mappnings data flöden, vilket gör att du kan testa omvandlings logiken på några sekunder i ett aktivt Spark-kluster.

Aktivera fel sökning genom att klicka på skjutreglaget för **fel sökning av data flöde** i det övre fältet i fabriken. Klicka på OK när popup-fönster för bekräftelse dialog rutan visas. Det tar ungefär 5-7 minuter att starta klustret. Fortsätt med att hämta *data från Azure SQL DB till ADLS Gen2 att använda kopierings aktiviteten* medan den initieras.

![Portal konfigurera 10](media/lab-data-flow-data-share/configure10.png)

## <a name="ingest-data-using-the-copy-activity"></a>Mata in data med kopierings aktiviteten

I det här avsnittet ska du skapa en pipeline med en kopierings aktivitet som matar in en tabell från en Azure SQL-databas till ett ADLS Gen2 lagrings konto. Du lär dig hur du lägger till en pipeline, konfigurerar en data uppsättning och felsöker en pipeline via ADF-UX. Det konfigurations mönster som används i det här avsnittet kan användas för att kopiera från ett Relations data lager till ett filbaserat data lager.

I Azure Data Factory är en pipeline en logisk gruppering av aktiviteter som tillsammans utför en aktivitet. En aktivitet definierar en åtgärd som ska utföras på dina data. En data uppsättning pekar på de data som du vill använda i en länkad tjänst.

### <a name="create-a-pipeline-with-a-copy-activity"></a>Skapa en pipeline med en kopierings aktivitet

1. I fönstret fabriks resurser klickar du på plus ikonen för att öppna den nya resurs menyn. Välj **pipeline**.

    ![Portal kopia 1](media/lab-data-flow-data-share/copy1.png)
1. På fliken **Allmänt** i pipeline-arbetsytan kan du ge pipelinen ett beskrivande som "IngestAndTransformTaxiData".

    ![Portal kopia 2](media/lab-data-flow-data-share/copy2.png)
1. I fönstret aktiviteter på pipeline-arbetsytan öppnar du drag **och transformering** och drar aktiviteten **Kopiera data** till arbets ytan. Ge kopierings aktiviteten ett beskrivande namn, till exempel "IngestIntoADLS".

    ![Portal kopia 3](media/lab-data-flow-data-share/copy3.png)

### <a name="configure-azure-sql-db-source-dataset"></a>Konfigurera käll data uppsättning för Azure SQL DB

1. Klicka på fliken **källa** i kopierings aktiviteten. Klicka på **ny** för att skapa en ny data uppsättning. Källan blir tabellen ' dbo. TripData "finns i den länkade tjänsten" SQLDB "som kon figurer ATS tidigare.

    ![Portal kopia 4](media/lab-data-flow-data-share/copy4.png)
1. Sök efter **Azure SQL Database** och klicka på Fortsätt.

    ![Portal kopia 5](media/lab-data-flow-data-share/copy-5.png)
1. Anropa din data uppsättning "TripData". Välj "SQLDB" som länkad tjänst. Markera tabell namnet ' dbo. TripData ' från List rutan tabell namn. Importera schemat **från anslutning/lagring**. Klicka på OK när du är färdig.

    ![Portal kopia 6](media/lab-data-flow-data-share/copy6.png)

Du har skapat käll data uppsättningen. Se till att käll inställningarna är standard **tabellen** i fältet Använd fråga.

### <a name="configure-adls-gen2-sink-dataset"></a>Konfigurera ADLS Gen2 data uppsättning för mottagare

1. Klicka på fliken **mottagare** i kopierings aktiviteten. Klicka på **ny** för att skapa en ny data uppsättning.

    ![Portal kopia 7](media/lab-data-flow-data-share/copy7.png)
1. Sök efter **Azure Data Lake Storage Gen2** och klicka på Fortsätt.

    ![Portal kopia 8](media/lab-data-flow-data-share/copy8.png)
1. I fönstret Välj format väljer du **DelimitedText** när du skriver till en CSV-fil. Klicka på Fortsätt.

    ![Portal kopia 9](media/lab-data-flow-data-share/copy9.png)
1. Namnge din Sink-datauppsättning TripDataCSV. Välj "ADLSGen2" som länkad tjänst. Ange var du vill skriva CSV-filen. Du kan till exempel skriva data till en fil `trip-data.csv` i behållaren `staging-container` . Ange **första raden som rubrik** till True eftersom du vill att dina utdata ska ha rubriker. Eftersom det inte finns någon fil på målet ännu, anger du **Importera schema** till **ingen**. Klicka på OK när du är färdig.

    ![Portal kopia 10](media/lab-data-flow-data-share/copy10.png)

### <a name="test-the-copy-activity-with-a-pipeline-debug-run"></a>Testa kopierings aktiviteten med en pipeline för fel sökning

1. Verifiera att kopierings aktiviteten fungerar som den ska genom att klicka på **Felsök** överst i pipeline-arbetsytan för att köra en fel sökning. Med en fel söknings körning kan du testa din pipeline antingen från slut punkt till slut punkt eller till en Bryt punkt innan du publicerar den till Data Factory-tjänsten.

    ![Portal kopia 11](media/lab-data-flow-data-share/copy11.png)
1. Om du vill övervaka din fel söknings körning går du till fliken **utdata** i pipeline-arbetsytan. Övervaknings skärmen uppdateras automatiskt var 20: e sekund eller när du klickar på knappen Uppdatera manuellt. Kopierings aktiviteten har en särskild övervaknings vy, som du kan komma åt genom att klicka på ögon ikonen i kolumnen **åtgärder** .

    ![Portal kopia 12](media/lab-data-flow-data-share/copy12.png)
1. Vyn kopiera övervakning ger aktivitetens körnings information och prestanda egenskaper. Du kan se information, till exempel data läsa/skriva, rader, lästa/skrivna, lästa/skrivna filer och data flöde. Om du har konfigurerat allting korrekt bör du se 49 999 rader skrivna i en fil i din ADLS-mottagare.

    ![Portal kopia 13](media/lab-data-flow-data-share/copy13.png)
1. Innan du går vidare till nästa avsnitt, rekommenderar vi att du publicerar ändringarna till Data Factory-tjänsten genom att klicka på **publicera alla** i det översta fältet i fabriken. Även om det inte beskrivs i det här labbet, Azure Data Factory stöder fullständig git-integrering. Git-integrering möjliggör versions kontroll, upprepnings sparande i en lagrings plats och samarbete på en data fabrik. Mer information finns i [käll kontroll i Azure Data Factory](./source-control.md#troubleshooting-git-integration).

    ![Portal publicering 1](media/lab-data-flow-data-share/publish1.png)

## <a name="transform-data-using-mapping-data-flow"></a>Omvandla data med Mappa dataflöden

Nu när du har kopierat data till Azure Data Lake Storage är det dags att ansluta och aggregera dessa data till ett data lager. Vi använder data flödet för att mappa Azure Data Factorys visuellt utformade transformerings tjänst. Genom att mappa data flöden kan användarna utveckla omvandlings logik kod utan kostnad och köra dem i Spark-kluster som hanteras av ADF-tjänsten.

Det data flöde som skapats i det här steget inre kopplar samman data uppsättningen ' TripDataCSV ' som skapades i föregående avsnitt med en tabell ' dbo. TripFares lagras i SQLDB baserat på fyra nyckel kolumner. Sedan sammanställs data baserat på kolumn `payment_type` för att beräkna medelvärdet för vissa fält och skrivna i en Azure Synapse Analytics-tabell.

### <a name="add-a-data-flow-activity-to-your-pipeline"></a>Lägg till en data flödes aktivitet i din pipeline

1. I fönstret aktiviteter på pipeline-arbetsytan öppnar du drag **och transformering** och drar **data flödes** aktiviteten till arbets ytan.

    ![Portal data flöde 1](media/lab-data-flow-data-share/dataflow1.png)
1. I sidofönstret som öppnas väljer du **Skapa nytt data flöde** och väljer **mappa data flöde**. Klicka på **OK**.

    ![Portal data flöde 2](media/lab-data-flow-data-share/dataflow2.png)
1. Du dirigeras till data flödes arbets ytan där du skapar din omvandlings logik. På fliken Allmänt namnger du ditt data flöde "JoinAndAggregateData".

    ![Portal data flöde 3](media/lab-data-flow-data-share/dataflow3.png)

### <a name="configure-your-trip-data-csv-source"></a>Konfigurera din rese data CSV-källa

1. Det första du vill göra är att konfigurera de två käll omvandlingarna. Den första källan pekar på DelimitedText-datauppsättningen ' TripDataCSV '. Om du vill lägga till en käll omvandling klickar du på rutan **Lägg till källa** på arbets ytan.

    ![Portal data flöde 4](media/lab-data-flow-data-share/dataflow4.png)
1. Namnge källan "TripDataCSV" och välj data uppsättningen TripDataCSV från List rutan Källa. Om du kommer ihåg att du inte importerade ett schema från början när du skapade den här data uppsättningen eftersom det inte fanns några data där. Sedan `trip-data.csv` finns nu klickar du på **Redigera** för att gå till fliken Data uppsättnings inställningar.

    ![Portal data flöde 5](media/lab-data-flow-data-share/dataflow5.png)
1. Gå till fliken **schema** och klicka på **Importera schema**. Välj **från anslutning/Arkiv** för att importera direkt från fil arkivet. 14 kolumner av typen sträng ska visas.

    ![Portal data flöde 6](media/lab-data-flow-data-share/dataflow6.png)
1. Gå tillbaka till data flödet ' JoinAndAggregateData '. Om ditt fel söknings kluster har startats (visas med en grön cirkel bredvid skjutreglaget för fel sökning) kan du hämta en ögonblicks bild av data på fliken **data förhands granskning** . Klicka på **Uppdatera** för att hämta en data förhands granskning.

    ![Portal data flöde 7](media/lab-data-flow-data-share/dataflow7.png)

> [!Note]
> Data förhands granskning skriver inte data.

### <a name="configure-your-trip-fares-sql-db-source"></a>Konfigurera din rese biljett SQL DB-källa

1. Den andra källan som du lägger till pekar på SQL DB-tabellen ' dbo. TripFares'. Under din "TripDataCSV"-källa finns det en annan **Lägg till källa** -ruta. Klicka på den för att lägga till en ny käll omvandling.

    ![Portal data flöde 8](media/lab-data-flow-data-share/dataflow8.png)
1. Namnge källan ' TripFaresSQL '. Klicka på **ny** bredvid fältet käll data uppsättning för att skapa en ny SQL DB-datauppsättning.

    ![Portal data flöde 9](media/lab-data-flow-data-share/dataflow9.png)
1. Välj panelen **Azure SQL Database** och klicka på Fortsätt. *Obs! Du kanske märker att många av kopplingarna i Data Factory inte stöds i data flödet för mappning. Om du vill transformera data från någon av dessa källor kan du mata in dem i en källa som stöds med hjälp av kopierings aktiviteten*.

    ![Portal data flöde 10](media/lab-data-flow-data-share/dataflow-10.png)
1. Anropa din data uppsättning "TripFares". Välj "SQLDB" som länkad tjänst. Markera tabell namnet ' dbo. TripFares ' från List rutan tabell namn. Importera schemat **från anslutning/lagring**. Klicka på OK när du är färdig.

    ![Portal data flöde 11](media/lab-data-flow-data-share/dataflow11.png)
1. För att verifiera dina data, hämta en data förhands granskning på fliken **data förhands granskning** .

    ![Portal data flöde 12](media/lab-data-flow-data-share/dataflow12.png)

### <a name="inner-join-tripdatacsv-and-tripfaressql"></a>Inre koppling TripDataCSV och TripFaresSQL

1. Om du vill lägga till en ny omvandling klickar du på plus ikonen i det nedre högra hörnet av ' TripDataCSV '. Under **flera indata/utdata** väljer du **Anslut**.

    ![Portal koppling 1](media/lab-data-flow-data-share/join1.png)
1. Ge kopplings omvandlingen värdet "InnerJoinWithTripFares". Välj ' TripFaresSQL ' i list rutan höger Stream. Välj **inre** som kopplings typ. Om du vill veta mer om de olika kopplings typerna i mappnings data flödet, se [kopplings typer](./data-flow-join.md#join-types).

    Välj vilka kolumner du vill matcha från varje data ström via List rutan **kopplings villkor** . Om du vill lägga till ett ytterligare kopplings villkor klickar du på plus ikonen bredvid ett befintligt villkor. Som standard kombineras alla kopplings villkor med operatorn och, vilket innebär att alla villkor måste uppfyllas för en matchning. I det här labbet vill vi matcha på kolumner `medallion` , `hack_license` , `vendor_id` och `pickup_datetime`

    ![Portal koppling 2](media/lab-data-flow-data-share/join2.png)
1. Kontrol lera att du har anslutit 25 kolumner tillsammans med en data förhands granskning.

    ![Portal koppling 3](media/lab-data-flow-data-share/join3.png)

### <a name="aggregate-by-payment_type"></a>Sammanställt av payment_type

1. När du har slutfört din kopplings omvandling lägger du till en aggregerad omvandling genom att klicka på plus ikonen bredvid "InnerJoinWithTripFares. Välj **aggregera** under **schema modifierare**.

    ![Portal-aggregering 1](media/lab-data-flow-data-share/agg1.png)
1. Namnge den sammanställda omvandlingen ' AggregateByPaymentType '. Välj `payment_type` som Group by-kolumn.

    ![Portal aggity 2](media/lab-data-flow-data-share/agg2.png)
1. Gå till fliken **agg regeringar** . Här anger du två agg regeringar:
    * Den genomsnittliga pris gruppen grupperat efter betalnings typ
    * Det totala rese avståndet grupperat efter betalnings typ

    Först skapar du ett uttryck för genomsnittlig biljett. Skriv "average_fare" i text rutan med etiketten **Lägg till eller Välj en kolumn**.

    ![Portal aggare 3](media/lab-data-flow-data-share/agg3.png)
1. Om du vill ange ett agg regerings uttryck klickar du på den blå rutan med namnet **RETUR**. Detta öppnar uttrycks verktyget för data flöde, ett verktyg som används för att visuellt skapa data flödes uttryck med hjälp av inmatnings schema, inbyggda funktioner och åtgärder och användardefinierade parametrar. Mer information om funktionerna i uttrycks verktyget finns i dokumentationen till [uttrycks verktyget](./concepts-data-flow-expression-builder.md).

    För att få genomsnitts avgiften använder du `avg()` agg regerings funktionen för att sammanställa `total_amount` kolumn omvandlingen till ett heltal med `toInteger()` . I data flödets uttrycks språk definieras det som `avg(toInteger(total_amount))` . Klicka på **Spara och slutför** när du är klar.

    ![Portal aggity 4](media/lab-data-flow-data-share/agg4.png)
1. Om du vill lägga till ett extra agg regerings uttryck klickar du på plus ikonen bredvid `average_fare` . Välj **Lägg till kolumn**.

    ![Portal agg, 5](media/lab-data-flow-data-share/agg5.png)
1. Skriv "total_trip_distance" i text rutan med etiketten **Lägg till eller Välj en kolumn**. Som i det sista steget öppnar du uttrycks verktyget för att ange i uttrycket.

    Om du vill hämta det totala rese avståndet använder du `sum()` agg regerings funktionen för att aggregera `trip_distance` kolumn omvandlingen till ett heltal med `toInteger()` . I data flödets uttrycks språk definieras det som `sum(toInteger(trip_distance))` . Klicka på **Spara och slutför** när du är klar.

    ![Portal aggare 6](media/lab-data-flow-data-share/agg6.png)
1. Testa din omvandlings logik på fliken **data förhands granskning** . Som du kan se finns det betydligt färre rader och kolumner än tidigare. Endast de tre grupperna by och agg regerings kolumnerna som definierats i den här omvandlingen fortsätter att vara underordnade. Eftersom det bara finns fem grupper av betalnings typer i exemplet returneras bara fem rader.

    ![Portal aggare 7](media/lab-data-flow-data-share/agg7.png)

### <a name="configure-you-azure-synapse-analytics-sink"></a>Konfigurera Azure Synapse Analytics-mottagare

1. Nu när vi har slutfört vår omvandlings logik är vi redo att presentera våra data i en Azure Synapse Analytics-tabell. Lägg till en Sink-omvandling i avsnittet **mål** .

    ![Portal mottagare 1](media/lab-data-flow-data-share/sink1.png)
1. Namnge din Sink ' SQLDWSink '. Skapa en ny Azure Synapse Analytics-datauppsättning genom att klicka på **ny** bredvid fältet Sink-datauppsättning.

    ![Portal mottagare 2](media/lab-data-flow-data-share/sink2.png)

1. Välj panelen **Azure Synapse Analytics** och klicka på Fortsätt.

    ![Portal mottagare 3](media/lab-data-flow-data-share/sink-3.png)
1. Anropa din data uppsättning "AggregatedTaxiData". Välj "SQLDW" som länkad tjänst. Välj **Skapa ny tabell** och namnge den nya tabellen dbo. AggregateTaxiData. Klicka på OK när du är färdig

    ![Portal mottagare 4](media/lab-data-flow-data-share/sink4.png)
1. Gå till fliken **Inställningar** för mottagaren. Eftersom vi skapar en ny tabell måste vi välja **Återskapa tabell** under tabell åtgärd. Avmarkera **Aktivera mellanlagring**, som växlar om vi infogar rad för rad eller i batch.

    ![Portal mottagare 5](media/lab-data-flow-data-share/sink5.png)

Du har skapat ditt data flöde. Nu är det dags att köra det i en pipeline-aktivitet.

### <a name="debug-your-pipeline-end-to-end"></a>Felsöka din pipeline från slut punkt till slut punkt

1. Gå tillbaka till fliken för **IngestAndTransformData** -pipeline. Lägg märke till den gröna rutan på kopierings aktiviteten ' IngestIntoADLS '. Dra den till data flödes aktiviteten "JoinAndAggregateData". Detta skapar en på gång, vilket gör att data flödes aktiviteten endast körs om kopieringen lyckas.

    ![Portal-pipeline 1](media/lab-data-flow-data-share/pipeline1.png)
1. Klicka på **Felsök** för att köra en fel söknings körning som vi gjorde för kopierings aktiviteten. Vid fel söknings körningar använder data flödes aktiviteten det aktiva fel söknings klustret i stället för att skapa ett nytt kluster. Den här pipelinen tar lite mer än en minut att köra.

    ![Portal pipeline 2](media/lab-data-flow-data-share/pipeline2.png)
1. Precis som kopierings aktiviteten har data flödet en särskild övervaknings vy som nås av glasögon-ikonen när aktiviteten har slutförts.

    ![Portal pipeline 3](media/lab-data-flow-data-share/pipeline3.png)
1. I vyn övervakning kan du se ett förenklat data flödes diagram tillsammans med körnings tiderna och raderna i varje körnings steg. Om du har gjort det bör du ha sammanställda 49 999 rader i fem rader i den här aktiviteten.

    ![Portal pipelinen 4](media/lab-data-flow-data-share/pipeline4.png)
1. Du kan klicka på en omvandling för att få mer information om körningen, till exempel partitionerings information och nya/uppdaterade/avbrutna kolumner.

    ![Portal-pipeline 5](media/lab-data-flow-data-share/pipeline5.png)

Du har nu slutfört data fabriks delen av det här labbet. Publicera dina resurser om du vill operationalisera dem med utlösare. Du har kört en pipeline som inhämtade data från Azure SQL Database till Azure Data Lake Storage att använda kopierings aktiviteten och sedan aggregerade dessa data i en Azure Synapse-analys. Du kan kontrol lera att data har skrivits genom att titta på själva SQL Server.

## <a name="share-data-using-azure-data-share"></a>Dela data med Azure Data Share

I det här avsnittet får du lära dig hur du konfigurerar en ny data resurs med hjälp av Azure Portal. Detta innebär att du kan skapa en ny data resurs som innehåller data uppsättningar från Azure Data Lake Store Gen2 och Azure Synapse Analytics. Sedan kan du konfigurera ett ögonblicks bild schema, vilket ger data förbrukare möjlighet att automatiskt uppdatera de data som delas med dem. Sedan bjuder du in mottagare till data resursen. 

När du har skapat en data resurs kan du växla hatt och bli *data konsument*. Som data konsument går du igenom flödet för att acceptera en inbjudan om data delning, konfigurerar var du vill att data ska tas emot och mappa data uppsättningar till olika lagrings platser. Sedan kommer du att utlösa en ögonblicks bild, som kopierar de data som delas med dig till det angivna målet. 

### <a name="sharing-data-data-provider-flow"></a>Dela data (data leverantörs flöde)

1. Öppna Azure Portal i antingen Microsoft Edge eller Google Chrome.

1. Använd Sök fältet högst upp på sidan och Sök efter **data resurser**

    ![Portal annonser](media/lab-data-flow-data-share/portal-ads.png)

1. Välj data resurs kontot med providern i namnet. Till exempel **DataProvider0102**. 

1. Välj **börja dela dina data**

    ![Börja dela](media/lab-data-flow-data-share/ads-start-sharing.png)

1. Välj **+ skapa** för att börja konfigurera din nya data resurs. 

1. Under **resurs namn** anger du ett namn som du väljer. Detta är resurs namnet som visas av din data konsument, så se till att ge det ett beskrivande namn, till exempel TaxiData.

1. Under **Beskrivning**, sorteras en mening som beskriver innehållet i data resursen. Data resursen kommer att innehålla världs omfattande taxi rese data som lagras i ett antal butiker, inklusive Azure Synapse Analytics och Azure Data Lake Store. 

1. Under **användningsvillkor** anger du en uppsättning villkor som du vill att din data konsument ska följa. Några exempel är "Distribuera inte dessa data utanför organisationen" eller "se juridiskt avtal". 

    ![Dela information](media/lab-data-flow-data-share/ads-details.png)

1. Välj **Fortsätt**. 

1. Välj **Lägg till data uppsättningar** 

    ![Lägg till data uppsättning 1](media/lab-data-flow-data-share/add-dataset.png)

1. Välj **Azure Synapse Analytics** om du vill välja en tabell från Azure Synapse Analytics att dina ADF-omvandlingar landats i.

    ![Lägg till SQL för data uppsättning](media/lab-data-flow-data-share/add-dataset-sql.png)


1. Du kommer att få ett skript att köra innan du kan fortsätta. Det angivna skriptet skapar en användare i SQL-databasen så att Azure Data Share-MSI kan autentiseras för dess räkning. 

> [!IMPORTANT]
> Innan du kör skriptet måste du ange dig själv som Active Directory administratör för SQL Server. 

1. Öppna en ny flik och navigera till Azure Portal. Kopiera skriptet som tillhandahålls för att skapa en användare i databasen som du vill dela data från. Det gör du genom att logga in på ANALYSLÖSNINGAR-databasen med Query Explorer (för hands version) med AAD-autentisering. 

    Du måste ändra skriptet så att den skapade användaren finns inom hakparenteser. Exempel
    
    Skapa användare [dataprovider-xxxx] från extern inloggning;  exec sp_addrolemember db_owner, [dataprovider-xxxx];
    
1. Växla tillbaka till Azure-dataresursen där du lade till data uppsättningar till data resursen. 

1. Välj **analyslösningar** och välj sedan **AggregatedTaxiData** för tabellen. 

1. Välj **Lägg till data uppsättning**

    Nu har vi en SQL-tabell som är en del av vår data uppsättning. Nu ska vi lägga till ytterligare data uppsättningar från Azure Data Lake Store. 

1. Välj **Lägg till data uppsättning** och välj **Azure Data Lake Store Gen2**

    ![Lägg till data uppsättning ADLS](media/lab-data-flow-data-share/add-dataset-adls.png)

1. Välj **Nästa**

1. Expandera *wwtaxidata*. Expandera *data för Boston taxi*. Observera att du kan dela på filnivå. 

1. Välj mappen *Boston taxi data* för att lägga till hela mappen till data resursen. 

1. Välj **Lägg till data uppsättningar**

1. Granska de data uppsättningar som har lagts till. Du bör ha en SQL-tabell och en ADLS Gen2-mapp som har lagts till i din data resurs. 

1. Välj **Fortsätt**

1. På den här skärmen kan du lägga till mottagare till din data resurs. De mottagare som du lägger till får inbjudningar till din data resurs. I det här labbet måste du lägga till i 2 e-post adresser:

    1. E-postadressen till den Azure-prenumeration du befinner dig i. 

        ![Lägga till mottagare](media/lab-data-flow-data-share/add-recipients.png)

    1. Lägg till i den fiktiva data konsumenten med namnet *janedoe@fabrikam.com* .

1. På den här skärmen kan du konfigurera en ögonblicks bild inställning för din data konsument. Detta gör att de kan ta emot regelbundna uppdateringar av dina data enligt ett intervall som definierats av dig. 

1. Kontrol lera **ögonblicks bilds schema** och konfigurera en Tim uppdatering av dina data med hjälp av list rutan *upprepning* .  

1. Välj **Skapa**.

    Nu har du en aktiv data resurs. Gör det möjligt att granska vad du kan se som en data leverantör när du skapar en data resurs. 

1. Välj den data resurs som du har skapat, med rubriken **dataprovider**. Du kan navigera till den genom att välja **skickade resurser** i **data resursen**. 

1. Klicka på schema för ögonblicks bild. Du kan inaktivera schemat för ögonblicks bilder om du väljer. 

1. Välj sedan fliken **data uppsättningar** . Du kan lägga till ytterligare data uppsättningar i den här data resursen när den har skapats. 

1. Välj fliken **dela prenumerationer** . Det finns inga delnings prenumerationer än eftersom din data konsument ännu inte har accepterat din inbjudan.

1. Gå till fliken **inbjudningar** . Här visas en lista över väntande inbjudningar. 

    ![Väntande inbjudningar](media/lab-data-flow-data-share/pending-invites.png)

1. Välj inbjudan till *janedoe@fabrikam.com* . Välj Ta bort. Om mottagaren ännu inte har accepterat inbjudan kommer de inte längre att kunna göra det. 

1. Välj fliken **Historik** . Inget visas eftersom din data konsument ännu inte har accepterat din inbjudan och utlöst en ögonblicks bild. 

### <a name="receiving-data-data-consumer-flow"></a>Ta emot data (data konsument flöde)

Nu när vi har granskat vår data resurs är vi redo att byta kontext och använda vår data Consumer hat. 

Du bör nu ha en Azure Data Share-inbjudan i din inkorg från Microsoft Azure. Starta Outlook Web Access (outlook.com) och logga in med de autentiseringsuppgifter som angetts för din Azure-prenumeration.

Klicka på "Visa inbjudan >" i e-postmeddelandet som du har tagit emot. Nu ska du simulera data konsument upplevelsen när du accepterar en dataproviders-inbjudan till data resursen. 

![Inbjudan till e-post](media/lab-data-flow-data-share/email-invite.png)

Du kan uppmanas att välja en prenumeration. Se till att du väljer den prenumeration som du har arbetat med i den här övningen. 

1. Klicka på inbjudan med titeln *dataprovider*. 

1. I den här inbjudan visar du olika uppgifter om den data resurs som du konfigurerade tidigare som en data leverantör. Granska informationen och godkänn användnings villkoren om de anges.

1. Välj den prenumeration och resurs grupp som redan finns för ditt labb. 

1. För **data resurs konto** väljer du **DataConsumer**. Du kan också skapa ett nytt data resurs konto. 

1. Bredvid **mottaget resurs namn**, ser du att standard resurs namnet är det namn som angavs av data leverantören. Ge delningen ett eget namn som beskriver de data som du ska ta emot, t. ex. **TaxiDataShare**.

    ![Inbjudan accepterar](media/lab-data-flow-data-share/consumer-accept.png)

1. Du kan välja att **godkänna och konfigurera nu** eller **godkänna och konfigurera senare**. Om du väljer att godkänna och konfigurera nu ska du ange ett lagrings konto där alla data ska kopieras. Om du väljer att godkänna och konfigurera senare kommer data uppsättningarna i resursen att omappas och du måste mappa dem manuellt. Vi ska välja det senare. 

1. Välj **Godkänn och konfigurera senare**. 

    När du konfigurerar det här alternativet skapas en resurs prenumeration, men det finns inget som krävs för data till land eftersom inget mål har mappats. 

    Nu ska vi konfigurera data uppsättnings mappningar för data resursen. 

1. Välj den mottagna resursen (namnet du angav i steg 5).

    **Utlös ögonblicks bild** är nedtonad men resursen är aktiv. 

1. Välj fliken **data uppsättningar** . Observera att varje data uppsättning är omappad, vilket innebär att den inte har något mål för att kopiera data till. 

    ![omappade data uppsättningar](media/lab-data-flow-data-share/unmapped.png)

1. Välj Azure Synapse Analytics-tabellen och välj sedan **+ Mappa till mål**.

1. På den högra sidan av skärmen väljer du List rutan **mål data typ** . 

    Du kan mappa SQL-data till en mängd olika data lager. I det här fallet ska vi mappa till en Azure SQL Database.

    ![mapping](media/lab-data-flow-data-share/mapping-options.png)
    
    Valfritt Välj **Azure Data Lake Store Gen2** som mål data typ. 
    
    Valfritt Välj den prenumeration, resurs grupp och det lagrings konto som du har arbetat med. 
    
    Valfritt Du kan välja att ta emot data i data Lake i antingen CSV-eller Parquet-format. 

1. Välj Azure SQL Database bredvid **mål data typ**. 

1. Välj den prenumeration, resurs grupp och det lagrings konto som du har arbetat med. 

    ![mappa till SQL](media/lab-data-flow-data-share/map-to-sqldb.png)

1. Innan du kan fortsätta måste du skapa en ny användare i SQL Server genom att köra det angivna skriptet. Kopiera först skriptet som medföljer Urklipp. 

1. Öppna en ny Azure Portal-flik. Stäng inte din befintliga flik eftersom du måste komma tillbaka till den om en stund. 

1. Gå till **SQL-databaser** på den nya fliken som du öppnade.

1. Välj SQL-databas (det får bara finnas en i din prenumeration). Var noga med att inte välja data lagret. 

1. Välj **Frågeredigeraren (förhands granskning)**

1. Använd AAD-autentisering för att logga in i Frågeredigeraren. 

1. Kör frågan som tillhandahölls i din data resurs (kopieras till Urklipp i steg 14). 

    Med det här kommandot kan Azure Data Share-tjänsten använda hanterade identiteter för Azure-tjänster för att autentisera till SQL Server att kunna kopiera data till den. 

1. Gå tillbaka till den ursprungliga fliken och välj **Mappa till mål**.

1. Välj sedan den Azure Data Lake Gen2-mapp som är en del av data uppsättningen och mappa den till ett Azure Blob Storage-konto. 

    ![storage](media/lab-data-flow-data-share/storage-map.png)

    När alla data uppsättningar har mappats är du nu redo att börja ta emot data från data leverantören. 

    ![mappar](media/lab-data-flow-data-share/all-mapped.png)
    
1. Välj **information**. 

    Observera att **Utlös ögonblicks bild** inte längre är nedtonad, eftersom data resursen nu har destinationer att kopiera till.

1. Välj Utlös ögonblicks bild – > fullständig kopia. 

    ![utlösare](media/lab-data-flow-data-share/trigger-full.png)

    Detta kommer att börja kopiera data till det nya data resurs kontot. I ett verkligt världs scenario skulle dessa data komma från en tredje part. 

    Det tar ungefär 3-5 minuter att komma åt data. Du kan övervaka förloppet genom att klicka på fliken **Historik** . 

    Medan du väntar går du till den ursprungliga data resursen (dataprovider) och visar status för fliken **dela prenumerationer** och **Historik** . Observera att det nu finns en aktiv prenumeration och som en data leverantör kan du övervaka när data konsumenten har börjat ta emot de data som delas med dem. 

1. Gå tillbaka till data konsumentens data resurs. När statusen för utlösaren har genomförts går du till mål-SQL-databasen och data Lake för att se att data har landats i respektive butiker. 

Grattis, du har slutfört labbet!