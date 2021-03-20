---
title: Utveckla och distribuera en modell i en SQL Server VM-team data vetenskaps process
description: Bygg och distribuera en maskin inlärnings modell med SQL Server på en virtuell Azure-dator med en offentligt tillgänglig data uppsättning.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 580181aaaea975ee07bcec8108297079c5373b92
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96007417"
---
# <a name="the-team-data-science-process-in-action-using-sql-server"></a>Team data vetenskaps processen i praktiken: använda SQL Server
I den här självstudien går vi igenom processen för att skapa och distribuera en maskin inlärnings modell med SQL Server och en offentligt tillgänglig data uppsättning – [NYC taxi TRIPs](https://www.andresmh.com/nyctaxitrips/) -datauppsättningen. I proceduren följer ett standard arbets flöde för data vetenskap: mata in och utforska data, ingenjörs funktioner för att under lätta inlärningen och sedan bygga och distribuera en modell.

## <a name="nyc-taxi-trips-dataset-description"></a><a name="dataset"></a>Beskrivning av NYC taxi TRIPs-datauppsättning
NYC taxi-resan är cirka 20 GB komprimerade CSV-filer (~ 48 GB okomprimerat), vilket omfattar mer än 173 000 000 enskilda resor och de priser som betalas för varje resa. Varje rese post omfattar hämtnings-och DropOff plats och tid, anonymiserats Hack (driv Rutinens) licens nummer och Medallion (Taxins unika ID). Data omfattar alla resor under året 2013 och tillhandahålls i följande två data uppsättningar för varje månad:

1. CSV-trip_data innehåller information om resan, till exempel antal passagerare, upphämtnings-och DropOff punkter, varaktighet för resan och rese längd. Här följer några exempel poster:
   
    `medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude`

    `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171`

    `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066`

    `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002`

    `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388`

    `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868`

2. "Trip_fare" CSV innehåller information om den avgift som betalas för varje resa, till exempel betalnings typ, pris belopp, tilläggs avgift, tips och avgifter, samt totalt betalat belopp. Här följer några exempel poster:
   
    `medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount`

    `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7`

    `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7`

    `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7`

    `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6`

    `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5`

Den unika nyckeln för att ansluta till rese \_ data och rese \_ pris består av fälten: Medallion, Hacke \_ License och upphämtnings \_ tid.

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>Exempel på förutsägelse aktiviteter
Vi kommer att formulera tre förutsägelse problem baserat på *Tip- \_ mängden*, nämligen:

* Binära klassificering: förutsäga huruvida ett tips har betalats för en resa, det vill säga ett *Tip- \_ belopp* som är större än $0 är ett positivt exempel, medan ett *tip- \_ värde* på $0 är ett negativt exempel.
* Klassificering av flera klasser: för att förutsäga det tips som du betalar för resan. Vi delar upp *Tip- \_ beloppet* i fem lager platser eller klasser:

   `Class 0 : tip_amount = $0`

   `Class 1 : tip_amount > $0 and tip_amount <= $5`

   `Class 2 : tip_amount > $5 and tip_amount <= $10`

   `Class 3 : tip_amount > $10 and tip_amount <= $20`

   `Class 4 : tip_amount > $20`

* Regressions uppgift: för att förutsäga hur mycket tips du betalar för en resa.  

## <a name="setting-up-the-azure-data-science-environment-for-advanced-analytics"></a><a name="setup"></a>Konfigurera Azure Data Science-miljön för avancerad analys
Som du kan se i guiden [Planera din miljö](plan-your-environment.md) finns det flera alternativ för att arbeta med NYC taxi TRIPs-datauppsättningen i Azure:

* Arbeta med data i Azure-blobbar sedan modell i Azure Machine Learning
* Läs in data i en SQL Server Database och sedan modell i Azure Machine Learning

I den här självstudien demonstrerar vi parallell Mass import av data till en SQL Server, data utforskning, funktions teknik och insamlings sampling med hjälp av SQL Server Management Studio samt användning av IPython Notebook. [Exempel skript](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) och [ipython-anteckningsböcker](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks) delas i GitHub. Ett exempel på en IPython Notebook som fungerar med data i Azure-blobbar finns också på samma plats.

Så här konfigurerar du din Azure Data Science-miljö:

1. [Skapa ett lagringskonto](../../storage/common/storage-account-create.md)
2. [Skapa en Azure Machine Learning-arbetsyta](../classic/create-workspace.md)
3. [Etablera en data science Virtual Machine](../data-science-virtual-machine/overview.md)som tillhandahåller en SQL Server och en IPython Notebook-Server.
   
   > [!NOTE]
   > Exempel skripten och IPython antecknings böcker laddas ned till den virtuella datorn för data vetenskap under installationen. När skriptet för VM-installationen har slutförts kommer exemplen att finnas i den virtuella datorns dokument bibliotek:  
   > 
   > * Exempel skript: `C:\Users\<user_name>\Documents\Data Science Scripts`  
   > * Exempel på IPython Notebooks: `C:\Users\<user_name>\Documents\IPython Notebooks\DataScienceSamples`  
   >   där `<user_name>` är den virtuella datorns Windows-inloggningsnamn. Vi kommer att referera till exempel-mapparna som **exempel skript** och **IPython Notebook-exempel**.
   > 
   > 

Beroende på data uppsättningens storlek, data källans plats och den valda Azure mål miljön liknar scenariot [ \# 5: stor data uppsättning i lokala filer, mål SQL Server i Azure VM](plan-sample-scenarios.md#largelocaltodb).

## <a name="get-the-data-from-public-source"></a><a name="getdata"></a>Hämta data från offentlig källa
För att hämta data uppsättningen [NYC taxi TRIPs](https://www.andresmh.com/nyctaxitrips/) från dess offentliga plats kan du använda någon av de metoder som beskrivs i [Flytta data till och från Azure Blob Storage](move-azure-blob.md) för att kopiera data till den nya virtuella datorn.

Så här kopierar du data med AzCopy:

1. Logga in på den virtuella datorn (VM)
2. Skapa en ny katalog i den virtuella datorns data disk (Obs: Använd inte den tillfälliga disken som medföljer den virtuella datorn som en data disk).
3. I ett kommando tolks fönster kör du följande AzCopy-kommando rad och ersätter <path_to_data_folder> med din datamapp skapad i (2):

    ```console
    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S
    ```

    När AzCopy är klar bör det finnas 24 zippade CSV-filer (12 för rese \_ data och 12 för rese \_ pris) i mappen data.
4. Zippa upp de hämtade filerna. Observera mappen där de okomprimerade filerna finns. Den här mappen kallas för <sökvägen \_ till \_ \_ datafiler \> .

## <a name="bulk-import-data-into-sql-server-database"></a><a name="dbload"></a>Mass import av data till SQL Server databas
Prestanda vid inläsning/överföring av stora mängder data till en SQL Database och efterföljande frågor kan förbättras med hjälp av *partitionerade tabeller och vyer*. I det här avsnittet följer vi instruktionerna som beskrivs i [parallell Mass data import med hjälp av SQL partition tables](parallel-load-sql-partitioned-tables.md) för att skapa en ny databas och läsa in data i partitionerade tabeller parallellt.

1. Starta **SQL Server Management Studio** när du är inloggad på den virtuella datorn.
2. Anslut med Windows-autentisering.
   
    ![SSMS ansluta][12]
3. Om du ännu inte har ändrat SQL Server autentiseringsläget och skapat en ny SQL-inloggning öppnar du skript filen med namnet **change \_ auth. SQL** i mappen **exempel skript** . Ändra standard användar namn och lösen ord. Klicka på **Kör** i verktygsfältet för att köra skriptet.
   
    ![Kör skript][13]
4. Verifiera och/eller ändra SQL Server standard databas och loggfiler för att säkerställa att nyligen skapade databaser lagras på en datadisk. SQL Server VM-avbildningen som är optimerad för data lager inläsningar är förkonfigurerad med data-och logg diskar. Om den virtuella datorn inte innehåller någon datadisk och du har lagt till nya virtuella hård diskar under installationen av den virtuella datorn, ändrar du standardmapparna enligt följande:
   
   * Högerklicka på SQL Server namnet i den vänstra panelen och klicka på **Egenskaper**.
     
       ![SQL Server egenskaper][14]
   * Välj **databas inställningar** från listan **Välj en sida** till vänster.
   * Verifiera och/eller ändra **databasens standard platser** till de **data disk** platser som du väljer. Den här platsen är den nya databas som finns om den skapas med standardinställningarna.
     
       ![SQL Database standardvärden][15]  
5. Om du vill skapa en ny databas och en uppsättning fil grupper som ska innehålla de partitionerade tabellerna öppnar du exempel skriptet **create \_ db \_ . SQL**. Skriptet skapar en ny databas med namnet **TaxiNYC** och 12 fil grupper på standard data platsen. Varje fil grupp innehåller en månad med rese \_ data och rese \_ pris data. Ändra databas namnet om du vill. Klicka på **Kör** för att köra skriptet.
6. Skapa sedan två partitionstabell, en för rese \_ data och en annan för rese \_ avgiften. Öppna exempel skriptet **skapa \_ partitionerad \_ tabell. SQL**, som kommer att:
   
   * Skapa en partitions funktion för att dela data efter månad.
   * Skapa ett partition schema för att mappa varje månads data till en annan filgrupp.
   * Skapa två partitionerade tabeller som är mappade till partitionsfunktionen: **nyctaxi- \_ resan** kommer att innehålla rese \_ data och **nyctaxi- \_ priset** kommer att rymma rese \_ pris data.
     
     Klicka på **Kör** för att köra skriptet och skapa partitionerade tabeller.
7. I mappen **exempel skript** finns det två exempel på PowerShell-skript som visar parallell Mass import av data till SQL Server tabeller.
   
   * **BCP \_ Parallel \_generic.ps1** är ett allmänt skript för att importera data till parallell Mass import till en tabell. Ändra det här skriptet för att ange in-och mål-variabler som anges i kommentar raderna i skriptet.
   * **BCP \_ Parallel \_nyctaxi.ps1** är en förkonfigurerad version av det generiska skriptet och kan användas för att läsa in båda tabellerna för NYC taxi-TRIPs-data.  
8. Högerklicka på namnet på **den \_ parallella \_nyctaxi.ps1** skriptet och klicka på **Redigera** för att öppna det i PowerShell. Granska de förinställda variablerna och ändra enligt det valda databas namnet, i mappen indata, i målmappen och sökvägar till exempel formatet filer **nyctaxi_trip.xml** och **nyctaxi \_fare.xml** (finns i mappen **exempel skript** ).
   
    ![Mass import av data][16]
   
    Du kan också välja autentiseringsläget, standardvärdet är Windows-autentisering. Klicka på den gröna pilen i verktygsfältet för att köra. Skriptet startar 24 Mass import åtgärder parallellt, 12 för varje partitionerad tabell. Du kan övervaka statusen för data import genom att öppna SQL Server standardmappen för data som anges ovan.
9. PowerShell-skriptet rapporterar start-och slut tider. När alla Mass importer har slutförts rapporteras slut tiden. Kontrol lera målmappen för att kontrol lera att Mass importen lyckades, det vill säga inga fel som rapporteras i mål loggens mapp.
10. Din databas är nu klar för utforskning, funktions teknik och andra åtgärder som önskas. Eftersom tabellerna är partitionerade enligt fältet Hämta **\_ datum/tid** , kommer frågor som innehåller villkor för **upphämtnings \_ datum** i **WHERE** -satsen att dra nytta av partitionsfunktionen.
11. I **SQL Server Management Studio** kan du utforska exempel frågorna om exempel skriptet **\_ . SQL**. Om du vill köra någon av exempel frågorna markerar du raderna och klickar sedan på **Kör** i verktygsfältet.
12. NYC taxi-TRIPs-data läses in i två separata tabeller. För att förbättra kopplings åtgärderna rekommenderas det starkt att indexera tabellerna. Exempel skriptet **skapar \_ partitionerat \_ index. SQL** skapar partitionerade index för den sammansatta kopplings nyckeln **Medallion, hacka \_ licens och upphämtnings \_ datum**.

## <a name="data-exploration-and-feature-engineering-in-sql-server"></a><a name="dbexplore"></a>Data utforskning och funktions teknik i SQL Server
I det här avsnittet utför vi data utforsknings-och funktions skapande genom att köra SQL-frågor direkt i **SQL Server Management Studio** med hjälp av SQL Server-databasen som skapades tidigare. Ett exempel skript som heter **exempel \_ frågor. SQL** finns i mappen **exempel skript** . Ändra skriptet för att ändra databas namnet, om det skiljer sig från standardvärdet: **TaxiNYC**.

I den här övningen kommer vi att:

* Anslut till **SQL Server Management Studio** med hjälp av Windows-autentisering eller med SQL-autentisering och SQL-inloggningsnamn och lösen ord.
* Utforska data distributioner av några fält i varierande tidsfönster.
* Undersök data kvaliteten på fälten longitud och Latitude.
* Generera binära och multiklassens klassificerings etiketter baserat på **tips \_ mängden**.
* Generera funktioner och beräkna/jämför rese avstånd.
* Gå med i de två tabellerna och extrahera ett slumpmässigt exempel som ska användas för att bygga modeller.

När du är redo att fortsätta till Azure Machine Learning kan du antingen:  

1. Spara den sista SQL-frågan för att extrahera och sampla data och kopiera – klistra in frågan direkt i en modul för att [Importera data][import-data] i Azure Machine Learning, eller
2. Behåll de insamlade och tillverkade data som du planerar att använda för modell utveckling i en ny databas tabell och Använd den nya tabellen i modulen [Importera data][import-data] i Azure Machine Learning.

I det här avsnittet ska vi spara den slutliga frågan för att extrahera och sampla data. Den andra metoden visas i avsnittet [data utforskning och funktions teknik i IPython Notebook](#ipnb) .

En snabb kontroll av antalet rader och kolumner i tabellerna som är ifyllda tidigare med hjälp av parallell Mass import,

- Rapport antal rader i tabell nyctaxi_trip utan tabells ökning: `SELECT SUM(rows) FROM sys.partitions WHERE object_id = OBJECT_ID('nyctaxi_trip')`
- Rapport antal kolumner i tabell nyctaxi_trip: `SELECT COUNT(*) FROM information_schema.columns WHERE table_name = 'nyctaxi_trip'`

#### <a name="exploration-trip-distribution-by-medallion"></a>Undersökning: rese distribution per Medallion
I det här exemplet identifieras Medallion (taxi-nummer) med mer än 100 resor inom en viss tids period. Frågan skulle dra nytta av den partitionerade tabell åtkomsten eftersom den har ett villkor för det partition schema som användes vid **upphämtnings \_ datum**. Vid frågor till den fullständiga data uppsättningen används även den partitionerade tabellen och/eller index genomsökningen.

```sql
SELECT medallion, COUNT(*)
FROM nyctaxi_fare
WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
GROUP BY medallion
HAVING COUNT(*) > 100
```

#### <a name="exploration-trip-distribution-by-medallion-and-hack_license"></a>Undersökning: rese distribution av Medallion och hack_license

```sql
SELECT medallion, hack_license, COUNT(*)
FROM nyctaxi_fare
WHERE pickup_datetime BETWEEN '20130101' AND '20130131'
GROUP BY medallion, hack_license
HAVING COUNT(*) > 100
```

#### <a name="data-quality-assessment-verify-records-with-incorrect-longitude-andor-latitude"></a>Utvärdering av data kvalitet: verifiera poster med felaktig longitud och/eller latitud
Det här exemplet undersöker om något av fälten longitud och/eller latitud innehåller ett ogiltigt värde (radian grader måste vara mellan-90 och 90), eller ha koordinaterna (0, 0).

```sql
SELECT COUNT(*) FROM nyctaxi_trip
WHERE pickup_datetime BETWEEN '20130101' AND '20130331'
AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(pickup_latitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND 90
OR    CAST(dropoff_latitude AS float) NOT BETWEEN -90 AND 90
OR    (pickup_longitude = '0' AND pickup_latitude = '0')
OR    (dropoff_longitude = '0' AND dropoff_latitude = '0'))
```

#### <a name="exploration-tipped-vs-not-tipped-trips-distribution"></a>Undersökning: lutad eller överlutad TRIPs-distribution
I det här exemplet hittar du antalet resor som har lutats jämfört med en viss tids period (eller i den fullständiga data uppsättningen om det omfattar hela året). Den här distributionen visar den binära etikett fördelningen som ska användas senare för binära klassificerings modellering.

```sql
SELECT tipped, COUNT(*) AS tip_freq FROM (
  SELECT CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped, tip_amount
  FROM nyctaxi_fare
  WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
GROUP BY tipped
```

#### <a name="exploration-tip-classrange-distribution"></a>Undersökning: Tip-klass/intervall-distribution
Det här exemplet beräknar fördelningen av Tip-intervall under en viss tids period (eller i den fullständiga data uppsättningen om den täcker hela året). Den här fördelningen av etikett klasser kommer att användas senare för klassificerings modellering i multiklass.

```sql
SELECT tip_class, COUNT(*) AS tip_freq FROM (
    SELECT CASE
        WHEN (tip_amount = 0) THEN 0
        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
        ELSE 4
    END AS tip_class
FROM nyctaxi_fare
WHERE pickup_datetime BETWEEN '20130101' AND '20131231') tc
GROUP BY tip_class
```

#### <a name="exploration-compute-and-compare-trip-distance"></a>Undersökning: beräkna och jämför rese avstånd
I det här exemplet konverteras upphämtnings-och DropOff longitud och latitud till SQL-geografiska punkter, vilket beräknar rese avståndet med hjälp av SQL geografi-punkter skillnad och returnerar ett slumpmässigt exempel av resultaten för jämförelse. Exemplet begränsar resultatet till giltiga koordinater endast med frågan om utvärdering av data som tidigare beskrivits.

```sql
SELECT
pickup_location=geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326)
,dropoff_location=geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326)
,trip_distance
,computedist=round(geography::STPointFromText('POINT(' + pickup_longitude + ' ' + pickup_latitude + ')', 4326).STDistance(geography::STPointFromText('POINT(' + dropoff_longitude + ' ' + dropoff_latitude + ')', 4326))/1000, 2)
FROM nyctaxi_trip
tablesample(0.01 percent)
WHERE CAST(pickup_latitude AS float) BETWEEN -90 AND 90
AND   CAST(dropoff_latitude AS float) BETWEEN -90 AND 90
AND   pickup_longitude != '0' AND dropoff_longitude != '0'
```

#### <a name="feature-engineering-in-sql-queries"></a>Funktions teknik i SQL-frågor
Utforsknings frågor för etikett generering och geografi konvertering kan också användas för att generera etiketter/funktioner genom att ta bort inventerings delen. Ytterligare funktioner i SQL-exempel finns i avsnittet [data utforskning och funktions teknik i IPython Notebook](#ipnb) . Det är mer effektivt att köra frågor som genereras av funktionen på den fullständiga data uppsättningen eller en stor del av den med hjälp av SQL-frågor som körs direkt på SQL Server databas instansen. Frågorna kan utföras i **SQL Server Management Studio**, IPython Notebook eller ett utvecklingsverktyg eller en miljö som kan komma åt databasen lokalt eller via fjärr anslutning.

#### <a name="preparing-data-for-model-building"></a>Förbereda data för modell skapande
Följande fråga ansluter tabellerna **nyctaxi \_ rese** -och **nyctaxi- \_ pris** , genererar en binära klassificerings **etikett, en** **\_ klass** för klassificerings etiketter i flera klasser och extraherar ett slumpmässigt exempel i 1% från den fullständiga sammanfogade data uppsättningen. Den här frågan kan kopieras och klistras in direkt i modulen [Azure Machine Learning Studio](https://studio.azureml.net) [Importera data][import-data] för direkt data inmatning från SQL Server databas instansen i Azure. Frågan utesluter poster med felaktiga koordinater (0, 0).

```sql
SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,     f.total_amount, f.tip_amount,
    CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END AS tipped,
    CASE WHEN (tip_amount = 0) THEN 0
        WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
        WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
        WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
        ELSE 4
    END AS tip_class
FROM nyctaxi_trip t, nyctaxi_fare f
TABLESAMPLE (1 percent)
WHERE t.medallion = f.medallion
AND   t.hack_license = f.hack_license
AND   t.pickup_datetime = f.pickup_datetime
AND   pickup_longitude != '0' AND dropoff_longitude != '0'
```

## <a name="data-exploration-and-feature-engineering-in-ipython-notebook"></a><a name="ipnb"></a>Data utforskning och funktions teknik i IPython Notebook
I det här avsnittet ska vi utföra data utforskning och skapa funktioner med hjälp av både python-och SQL-frågor mot SQL Server databas som skapades tidigare. Ett exempel på en IPython Notebook med namnet **Machine-Learning-data-science-process-SQL-berättelse. ipynb** finns i mappen **Sample ipython Notebooks** . Den här antecknings boken finns också på [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/iPythonNotebooks).

När du arbetar med Big data följer du den här rekommenderade ordningen:

* Läs i ett litet exempel på data till en minnes intern data ram.
* Utför vissa visualiseringar och utforskningar med hjälp av exempel data.
* Experimentera med funktions teknik med hjälp av exempel data.
* För större data granskning, data manipulation och funktions teknik använder du python för att utfärda SQL-frågor direkt mot SQL Server-databasen i den virtuella Azure-datorn.
* Bestäm vilken exempel storlek som ska användas för Azure Machine Learning modell skapande.

När du är redo att fortsätta till Azure Machine Learning kan du antingen:  

1. Spara den sista SQL-frågan för att extrahera och sampla data och kopiera – klistra in frågan direkt i en modul för att [Importera data][import-data] i Azure Machine Learning. Den här metoden visas i avsnittet [skapa modeller i Azure Machine Learning](#mlmodel) .    
2. Behåll de insamlade data som du planerar att använda för modell utveckling i en ny databas tabell och Använd sedan den nya tabellen i modulen [Importera data][import-data] .

Här följer några exempel på data visualisering, data visualisering och funktions teknik. Fler exempel finns i exempel på SQL-IPython Notebook i mappen **exempel ipython Notebooks** .

#### <a name="initialize-database-credentials"></a>Initiera autentiseringsuppgifter för databas
Initiera inställningarna för databas anslutningen i följande variabler:

```sql
SERVER_NAME=<server name>
DATABASE_NAME=<database name>
USERID=<user name>
PASSWORD=<password>
DB_DRIVER = <database server>
```

#### <a name="create-database-connection"></a>Skapa databas anslutning

```sql
CONNECTION_STRING = 'DRIVER={'+DRIVER+'};SERVER='+SERVER_NAME+';DATABASE='+DATABASE_NAME+';UID='+USERID+';PWD='+PASSWORD
conn = pyodbc.connect(CONNECTION_STRING)
```

#### <a name="report-number-of-rows-and-columns-in-table-nyctaxi_trip"></a>Rapport antal rader och kolumner i tabellen nyctaxi_trip

```sql
nrows = pd.read_sql('''
    SELECT SUM(rows) FROM sys.partitions
    WHERE object_id = OBJECT_ID('nyctaxi_trip')
''', conn)

print 'Total number of rows = %d' % nrows.iloc[0,0]

ncols = pd.read_sql('''
    SELECT COUNT(*) FROM information_schema.columns
    WHERE table_name = ('nyctaxi_trip')
''', conn)

print 'Total number of columns = %d' % ncols.iloc[0,0]
```

* Totalt antal rader = 173179759  
* Totalt antal kolumner = 14

#### <a name="read-in-a-small-data-sample-from-the-sql-server-database"></a>Läs ett litet data exempel från SQL Server-databasen

```sql
t0 = time.time()

query = '''
    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax,
        f.tolls_amount, f.total_amount, f.tip_amount
    FROM nyctaxi_trip t, nyctaxi_fare f
    TABLESAMPLE (0.05 PERCENT)
    WHERE t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
'''

df1 = pd.read_sql(query, conn)

t1 = time.time()
print 'Time to read the sample table is %f seconds' % (t1-t0)

print 'Number of rows and columns retrieved = (%d, %d)' % (df1.shape[0], df1.shape[1])
```

Tiden för att läsa exempel tabellen är 6,492000 sekunder  
Antal rader och kolumner som hämtats = (84952, 21)

#### <a name="descriptive-statistics"></a>Beskrivande statistik
Nu är du redo att utforska exempel data. Vi börjar med att titta på beskrivande statistik för **rese \_ avståndet** (eller något annat) fält:

```sql
df1['trip_distance'].describe()
```

#### <a name="visualization-box-plot-example"></a>Visualisering: låddiagram: exempel
Härnäst ska vi titta på rutan för resan för att visualisera quantiles

```sql
df1.boxplot(column='trip_distance',return_type='dict')
```

![Rita #1][1]

#### <a name="visualization-distribution-plot-example"></a>Visualisering: exempel på distributions ritning

```sql
fig = plt.figure()
ax1 = fig.add_subplot(1,2,1)
ax2 = fig.add_subplot(1,2,2)
df1['trip_distance'].plot(ax=ax1,kind='kde', style='b-')
df1['trip_distance'].hist(ax=ax2, bins=100, color='k')
```

![Rita #2][2]

#### <a name="visualization-bar-and-line-plots"></a>Visualisering: stapel-och linje diagram
I det här exemplet Binar vi rese avståndet till fem lager platser och visualiserar diskretisering-resultaten.

```sql
trip_dist_bins = [0, 1, 2, 4, 10, 1000]
df1['trip_distance']
trip_dist_bin_id = pd.cut(df1['trip_distance'], trip_dist_bins)
trip_dist_bin_id
```

Vi kan rita över lager plats fördelningen ovan i ett stapel-eller linje diagram enligt nedan

```sql
pd.Series(trip_dist_bin_id).value_counts().plot(kind='bar')
```

![Rita #3][3]

```sql
pd.Series(trip_dist_bin_id).value_counts().plot(kind='line')
```
![Rita #4][4]

#### <a name="visualization-scatterplot-example"></a>Visualisering: scatterplot-exempel
Vi visar punkt diagram mellan **rese \_ tiden \_ i \_ sekunder** och **rese \_ avstånd** för att se om det finns någon korrelation

```sql
plt.scatter(df1['trip_time_in_secs'], df1['trip_distance'])
```

![Rita #6][6]

På samma sätt kan vi kontrol lera förhållandet mellan **pris \_ kod** och **rese \_ avstånd**.

```sql
plt.scatter(df1['passenger_count'], df1['trip_distance'])
```

![Rita #8][8]

### <a name="sub-sampling-the-data-in-sql"></a>Sub-Sampling data i SQL
När du förbereder data för modell utveckling i [Azure Machine Learning Studio](https://studio.azureml.net)kan du antingen bestämma om **SQL-frågan ska användas direkt i modulen importera data** eller spara de utformade och samplade data i en ny tabell, som du kan använda i modulen [Importera data][import-data] med ett enkelt **Select * från <det \_ nya \_ tabell \_ namnet>**.

I det här avsnittet ska vi skapa en ny tabell för att lagra de insamlade och tillverkade data. Ett exempel på en direkt SQL-fråga för modell utveckling finns i avsnittet [data utforskning och funktions teknik i SQL Server](#dbexplore) avsnittet.

#### <a name="create-a-sample-table-and-populate-with-1-of-the-joined-tables-drop-table-first-if-it-exists"></a>Skapa en exempel tabell och fyll i med 1% av de kopplade tabellerna. Ta bort tabellen först om den finns.
I det här avsnittet ska vi gå med i tabellerna **nyctaxi \_ rese** -och **nyctaxi- \_ biljett**, extrahera ett slumpmässigt exempel för 1% och bevara exempel data i ett nytt tabell namn **nyctaxi \_ en \_ procents** ATS:

```sql
cursor = conn.cursor()

drop_table_if_exists = '''
    IF OBJECT_ID('nyctaxi_one_percent', 'U') IS NOT NULL DROP TABLE nyctaxi_one_percent
'''

nyctaxi_one_percent_insert = '''
    SELECT t.*, f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount, f.total_amount, f.tip_amount
    INTO nyctaxi_one_percent
    FROM nyctaxi_trip t, nyctaxi_fare f
    TABLESAMPLE (1 PERCENT)
    WHERE t.medallion = f.medallion
    AND   t.hack_license = f.hack_license
    AND   t.pickup_datetime = f.pickup_datetime
    AND   pickup_longitude <> '0' AND dropoff_longitude <> '0'
'''

cursor.execute(drop_table_if_exists)
cursor.execute(nyctaxi_one_percent_insert)
cursor.commit()
```

### <a name="data-exploration-using-sql-queries-in-ipython-notebook"></a>Data utforskning med SQL-frågor i IPython Notebook
I det här avsnittet ska vi utforska data distributioner med hjälp av 1% samplings data som finns kvar i den nya tabellen som vi skapade ovan. Liknande utforskningar kan utföras med hjälp av de ursprungliga tabellerna, om du använder **TABLESAMPLE** för att begränsa utforsknings exemplet eller genom att begränsa resultaten till en viss tids period med hjälp av **upphämtnings- \_ datetime** -partitionerna enligt [data utforsknings-och funktions teknikerna i SQL Server](#dbexplore) avsnittet.

#### <a name="exploration-daily-distribution-of-trips"></a>Undersökning: daglig distribution av resor

```sql
query = '''
    SELECT CONVERT(date, dropoff_datetime) AS date, COUNT(*) AS c
    FROM nyctaxi_one_percent
    GROUP BY CONVERT(date, dropoff_datetime)
'''

pd.read_sql(query,conn)
```

#### <a name="exploration-trip-distribution-per-medallion"></a>Undersökning: rese distribution per Medallion

```sql
query = '''
    SELECT medallion,count(*) AS c
    FROM nyctaxi_one_percent
    GROUP BY medallion
'''

pd.read_sql(query,conn)
```

### <a name="feature-generation-using-sql-queries-in-ipython-notebook"></a>Skapa funktioner med hjälp av SQL-frågor i IPython Notebook
I det här avsnittet kommer vi att generera nya etiketter och funktioner direkt med hjälp av SQL-frågor, som körs på tabellen 1%-exempel som vi skapade i föregående avsnitt.

#### <a name="label-generation-generate-class-labels"></a>Generering av etiketter: generera klass etiketter
I följande exempel genererar vi två uppsättningar etiketter som ska användas för modellering:

1. Binära klass etiketter **lutade** (förutsäger om ett tips visas)
2. **Tips \_ klass** för etiketter i flera klasser (förutsäger tipsets fack eller intervall)

```sql   
    nyctaxi_one_percent_add_col = '''
        ALTER TABLE nyctaxi_one_percent ADD tipped bit, tip_class int
    '''
   
    cursor.execute(nyctaxi_one_percent_add_col)
    cursor.commit()
   
    nyctaxi_one_percent_update_col = '''
        UPDATE nyctaxi_one_percent
        SET
           tipped = CASE WHEN (tip_amount > 0) THEN 1 ELSE 0 END,
           tip_class = CASE WHEN (tip_amount = 0) THEN 0
                            WHEN (tip_amount > 0 AND tip_amount <= 5) THEN 1
                            WHEN (tip_amount > 5 AND tip_amount <= 10) THEN 2
                            WHEN (tip_amount > 10 AND tip_amount <= 20) THEN 3
                            ELSE 4
                        END
    '''
   
    cursor.execute(nyctaxi_one_percent_update_col)
    cursor.commit()
```

#### <a name="feature-engineering-count-features-for-categorical-columns"></a>Funktions teknik: antal funktioner för kategoriska-kolumner
I det här exemplet omvandlas ett kategoriska-fält till ett numeriskt fält genom att varje kategori ersätts med antalet förekomster i data.

```sql
nyctaxi_one_percent_insert_col = '''
    ALTER TABLE nyctaxi_one_percent ADD cmt_count int, vts_count int
'''

cursor.execute(nyctaxi_one_percent_insert_col)
cursor.commit()

nyctaxi_one_percent_update_col = '''
    WITH B AS
    (
        SELECT medallion, hack_license,
            SUM(CASE WHEN vendor_id = 'cmt' THEN 1 ELSE 0 END) AS cmt_count,
            SUM(CASE WHEN vendor_id = 'vts' THEN 1 ELSE 0 END) AS vts_count
        FROM nyctaxi_one_percent
        GROUP BY medallion, hack_license
    )

    UPDATE nyctaxi_one_percent
    SET nyctaxi_one_percent.cmt_count = B.cmt_count,
        nyctaxi_one_percent.vts_count = B.vts_count
    FROM nyctaxi_one_percent A INNER JOIN B
    ON A.medallion = B.medallion AND A.hack_license = B.hack_license
'''

cursor.execute(nyctaxi_one_percent_update_col)
cursor.commit()
```

#### <a name="feature-engineering-bin-features-for-numerical-columns"></a>Funktions teknik: lager plats funktioner för numeriska kolumner
I det här exemplet omvandlas ett kontinuerligt numeriskt fält till förinställda kategori intervall, det vill säga ett numeriskt fält i ett kategoriska-fält.

```sql
nyctaxi_one_percent_insert_col = '''
    ALTER TABLE nyctaxi_one_percent ADD trip_time_bin int
'''

cursor.execute(nyctaxi_one_percent_insert_col)
cursor.commit()

nyctaxi_one_percent_update_col = '''
    WITH B(medallion,hack_license,pickup_datetime,trip_time_in_secs, BinNumber ) AS
    (
        SELECT medallion,hack_license,pickup_datetime,trip_time_in_secs,
        NTILE(5) OVER (ORDER BY trip_time_in_secs) AS BinNumber from nyctaxi_one_percent
    )

    UPDATE nyctaxi_one_percent
    SET trip_time_bin = B.BinNumber
    FROM nyctaxi_one_percent A INNER JOIN B
    ON A.medallion = B.medallion
    AND A.hack_license = B.hack_license
    AND A.pickup_datetime = B.pickup_datetime
'''

cursor.execute(nyctaxi_one_percent_update_col)
cursor.commit()
```

#### <a name="feature-engineering-extract-location-features-from-decimal-latitudelongitude"></a>Funktions teknik: extrahera plats funktioner från decimal latitud/longitud
I det här exemplet bryts decimal representationen av ett latitud-och/eller longitud-fält i flera region fält med olika granularitet, till exempel land/region, stad, stad, block osv. De nya geo-fälten mappas inte till faktiska platser. Information om att mappa koder för olika platser finns i [Bing Maps rest Services](/bingmaps/rest-services/locations/find-a-location-by-point).

```sql
nyctaxi_one_percent_insert_col = '''
    ALTER TABLE nyctaxi_one_percent
    ADD l1 varchar(6), l2 varchar(3), l3 varchar(3), l4 varchar(3),
        l5 varchar(3), l6 varchar(3), l7 varchar(3)
'''

cursor.execute(nyctaxi_one_percent_insert_col)
cursor.commit()

nyctaxi_one_percent_update_col = '''
    UPDATE nyctaxi_one_percent
    SET l1=round(pickup_longitude,0)
        , l2 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 1 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),1,1) ELSE '0' END     
        , l3 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 2 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),2,1) ELSE '0' END     
        , l4 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 3 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),3,1) ELSE '0' END     
        , l5 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 4 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),4,1) ELSE '0' END     
        , l6 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 5 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),5,1) ELSE '0' END     
        , l7 = CASE WHEN LEN (PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1)) >= 6 THEN SUBSTRING(PARSENAME(ROUND(ABS(pickup_longitude) - FLOOR(ABS(pickup_longitude)),6),1),6,1) ELSE '0' END
'''

cursor.execute(nyctaxi_one_percent_update_col)
cursor.commit()
```

#### <a name="verify-the-final-form-of-the-featurized-table"></a>Verifiera den slutliga formen för bearbetas-tabellen

```sql
query = '''SELECT TOP 100 * FROM nyctaxi_one_percent'''
pd.read_sql(query,conn)
```

Vi är nu redo att gå vidare till modell utveckling och modell distribution i [Azure Machine Learning](https://studio.azureml.net). Data är klara för något av de förutsägelse problem som identifierats tidigare, nämligen:

1. Binära klassificering: för att förutsäga om ett tips har betalats för en resa.
2. Klassificering av flera klasser: för att förutsäga det tips som betalas, enligt de tidigare definierade klasserna.
3. Regressions uppgift: för att förutsäga hur mycket tips du betalar för en resa.  

## <a name="building-models-in-azure-machine-learning"></a><a name="mlmodel"></a>Skapa modeller i Azure Machine Learning
Börja modelleringen genom att logga in på din Azure Machine Learning-arbetsyta. Om du ännu inte har skapat en Machine Learning-arbetsyta, se [skapa en Azure Machine Learning arbets yta](../classic/create-workspace.md).

1. För att komma igång med Azure Machine Learning, se [Vad är Azure Machine Learning Studio?](../overview-what-is-machine-learning-studio.md#ml-studio-classic-vs-azure-machine-learning-studio)
2. Logga in på [Azure Machine Learning Studio](https://studio.azureml.net).
3. På Start sidan för Studio får du en mängd information, videor, självstudier, länkar till modulerna referens och andra resurser. Mer information om Azure Machine Learning finns i [Azure Machine Learning dokumentations Center](https://azure.microsoft.com/documentation/services/machine-learning/).

Ett typiskt utbildnings experiment består av följande steg:

1. Skapa ett **+ nytt** experiment.
2. Hämta data till Azure Machine Learning.
3. I förväg bearbeta, transformera och manipulera data efter behov.
4. Generera funktioner efter behov.
5. Dela data i utbildning/validering/testning av data uppsättningar (eller ha separata data uppsättningar för var och en).
6. Välj en eller flera Machine Learning-algoritmer beroende på inlärnings problemet för att lösa problemet. Till exempel binära klassificering, klassificering av multiklass, regression.
7. Träna en eller flera modeller med hjälp av inlärnings data uppsättningen.
8. Räkna in verifierings data uppsättningen med hjälp av de utbildade modellerna.
9. Utvärdera modell (er) för att beräkna relevanta mått för inlärnings problemet.
10. Justera modell (er) och välj den bästa modellen som ska distribueras.

I den här övningen har vi redan utforskat och bearbetat data i SQL Server, och beslut om urvals storlek att mata in i Azure Machine Learning. Vi beslutade att bygga en eller flera av förutsägelse modellerna:

1. Hämta data till Azure Machine Learning med modulen [Importera data][import-data] som finns i avsnittet **data indata och utdata** . Mer information finns på referens sidan [Importera data][import-data] modul.
   
    ![Azure Machine Learning importera data][17]
2. Välj **Azure SQL Database** som **data källa** i panelen **Egenskaper** .
3. Ange databasens DNS-namn i fältet **databas server namn** . Formatering `tcp:<your_virtual_machine_DNS_name>,1433`
4. Ange **databas namnet** i motsvarande fält.
5. Ange **SQL-användarnamnet** i **serverns användar konto namn** och **lösen** ordet i **serverns användar konto lösen ord**.
7. I redigerings text områden för **databas fråga** klistrar du in frågan som extraherar de nödvändiga databas fälten (inklusive alla beräknade fält som etiketterna) och nedåt exempel data till önskad exempel storlek.

Ett exempel på ett binära klassificerings experiment som läser data direkt från SQL Server-databasen visas i bilden nedan. Liknande experiment kan konstrueras för klassificerings-och Regressions problem med multiklasser.

![Azure Machine Learning träna][10]

> [!IMPORTANT]
> I exemplen för att extrahera data och samplings frågor i föregående avsnitt, **ingår alla etiketter för de tre modell övningarna i frågan**. Ett viktigt (obligatoriskt) steg i varje modell övning är att **utesluta** onödiga etiketter för de andra två problemen och andra **mål läckor**. Om du t. ex. använder binära klassificering använder du etiketten **lutad** och utelämnar fält **Tip- \_ klassen**, **Tip- \_ beloppet** och **total \_ beloppet**. De sistnämnda är mål läckor eftersom de innebär att tipset betalas.
> 
> Om du vill utesluta onödiga kolumner och/eller mål läckor kan du använda modulen [Välj kolumner i data uppsättning][select-columns] eller [Redigera metadata][edit-metadata]. Mer information finns i avsnittet [Välj kolumner i data uppsättning][select-columns] och [Redigera metadata][edit-metadata] referens sidor.
> 
> 

## <a name="deploying-models-in-azure-machine-learning"></a><a name="mldeploy"></a>Distribuera modeller i Azure Machine Learning
När modellen är klar kan du enkelt distribuera den som en webb tjänst direkt från experimentet. Mer information om hur du distribuerar Azure Machine Learning-webbtjänster finns i [distribuera en Azure Machine Learning-webbtjänst](../classic/deploy-a-machine-learning-web-service.md).

Om du vill distribuera en ny webb tjänst måste du:

1. Skapa ett poängsättnings experiment.
2. Distribuera webb tjänsten.

Om du vill skapa ett Poäng experiment från ett **färdigt** utbildnings experiment klickar du på **skapa Poäng experiment** i det nedre åtgärds fältet.

![Azure-Poäng][18]

Azure Machine Learning försöker skapa ett bedömnings experiment baserat på komponenterna i utbildnings experimentet. I synnerhet kommer det att:

1. Spara den tränade modellen och ta bort modulen modell utbildning.
2. Identifiera en logisk **indataport** som representerar det förväntade schemat för indata.
3. Identifiera en logisk **utdataport** som representerar det förväntade datautdata-schemat för webb tjänsten.

När Poäng experimentet har skapats granskar du det och justerar efter behov. En typisk justering är att ersätta indata-datauppsättningen och/eller frågan med en som utesluter etikett fält, eftersom dessa etiketter inte är tillgängliga i schemat när tjänsten anropas. Det är också en bra idé att minska storleken på data uppsättningen och/eller frågan till några få poster, tillräckligt för att ange schemat för indata. För utdataporten är det vanligt att undanta alla indatafält och bara ta med de **resultat etiketter** och **resultat** som visas i resultatet med hjälp av modulen [Välj kolumner i data uppsättning][select-columns] .

Ett exempel på bedömnings experiment visas i bilden nedan. När du är redo att distribuera klickar du på knappen **publicera webb tjänst** i det nedre åtgärds fältet.

![Azure Machine Learning publicera][11]

I den här själv studie kursen har du skapat en Azure Data Science-miljö som arbetar med en stor offentlig data uppsättning från data förvärv till modell utbildning och distribution av en Azure Machine Learning-webbtjänst.

### <a name="license-information"></a>Licens information
Den här exempel genom gången och dess tillhör ande skript och IPython-anteckningsböcker delas av Microsoft under MIT-licensen. Se LICENSE.txt-filen i katalogen i exempel koden på GitHub för mer information.

### <a name="references"></a>Referenser
•    [Andrés MONROY NYC taxi TRIPs Download Page](https://www.andresmh.com/nyctaxitrips/)  
•    [Folier av NYCs taxi uppgifter från Christer Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)   
•    [NYC taxi och limousine kommissionens forskning och statistik](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[1]: ./media/sql-walkthrough/sql-walkthrough_26_1.png
[2]: ./media/sql-walkthrough/sql-walkthrough_28_1.png
[3]: ./media/sql-walkthrough/sql-walkthrough_35_1.png
[4]: ./media/sql-walkthrough/sql-walkthrough_36_1.png
[5]: ./media/sql-walkthrough/sql-walkthrough_39_1.png
[6]: ./media/sql-walkthrough/sql-walkthrough_42_1.png
[7]: ./media/sql-walkthrough/sql-walkthrough_44_1.png
[8]: ./media/sql-walkthrough/sql-walkthrough_46_1.png
[9]: ./media/sql-walkthrough/sql-walkthrough_71_1.png
[10]: ./media/sql-walkthrough/azuremltrain.png
[11]: ./media/sql-walkthrough/azuremlpublish.png
[12]: ./media/sql-walkthrough/ssmsconnect.png
[13]: ./media/sql-walkthrough/executescript.png
[14]: ./media/sql-walkthrough/sqlserverproperties.png
[15]: ./media/sql-walkthrough/sqldefaultdirs.png
[16]: ./media/sql-walkthrough/bulkimport.png
[17]: ./media/sql-walkthrough/amlreader.png
[18]: ./media/sql-walkthrough/amlscoring.png


<!-- Module References -->
[edit-metadata]: /azure/machine-learning/studio-module-reference/edit-metadata
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
[import-data]: /azure/machine-learning/studio-module-reference/import-data