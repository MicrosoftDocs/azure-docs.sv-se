---
title: Skalbar data vetenskap med Azure Data Lake-team data science process
description: Använda Azure Data Lake för att utföra data utforsknings-och binära klassificerings aktiviteter på en data uppsättning.
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
ms.openlocfilehash: e6eb0be4d9946907dc5bb2f22b27530a27a37aec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "96021460"
---
# <a name="scalable-data-science-with-azure-data-lake-an-end-to-end-walkthrough"></a>Skalbar data vetenskap med Azure Data Lake: en slut punkt till slut punkts genom gång
Den här genom gången visar hur du använder Azure Data Lake för att utföra data utforsknings-och binära klassificerings uppgifter på ett exempel av NYC taxi-resan och pris data uppsättningen för att förutsäga huruvida ett tips betalas av en avgift. Det vägleder dig genom stegen i [team data science-processen](./index.yml), från slut punkt till slut punkt från data förvärv till modell träning, och sedan till distributionen av en webb tjänst som publicerar modellen.

## <a name="technologies"></a>Tekniker

Dessa tekniker används i den här genom gången.
* Azure Data Lake Analytics
* U-SQL och Visual Studio
* Python
* Azure Machine Learning
* Skript


### <a name="azure-data-lake-analytics"></a>Azure Data Lake Analytics
[Microsoft Azure Data Lake](https://azure.microsoft.com/solutions/data-lake/) har alla funktioner som krävs för att göra det enkelt för data forskare att lagra data i alla storlekar, former och hastigheter, samt för att utföra data bearbetning, avancerad analys och maskin inlärning med hög skalbarhet på ett kostnads effektivt sätt.   Du betalar per jobb, endast när data faktiskt bearbetas. Azure Data Lake Analytics innehåller U-SQL, ett språk som blandar den deklarativ beskaffenheten hos SQL med lättfattliga programspecifika-kraften i C# för att tillhandahålla skalbar funktion för distribuerad fråga. Det gör att du kan bearbeta ostrukturerade data genom att använda schema vid läsning, infoga anpassad logik och användardefinierade funktioner (UDF: er) och inkluderar utöknings barhet för att möjliggöra detaljerad kontroll över hur du kör i skala. Mer information om design filosofin bakom U-SQL finns i [Visual Studio blogg inlägg](https://blogs.msdn.microsoft.com/visualstudio/2015/09/28/introducing-u-sql-a-language-that-makes-big-data-processing-easy/).

Data Lake Analytics är också en viktig del av Cortana Analytics Suite och fungerar med Azure Synapse Analytics, Power BI och Data Factory. Den här kombinationen ger dig en komplett moln stor data-och avancerad analys plattform.

Den här genom gången börjar med att beskriva hur du installerar de nödvändiga förutsättningarna och resurserna som krävs för att utföra uppgifter för data vetenskaps processen. Sedan beskriver den data bearbetnings stegen med hjälp av U-SQL och avslutas genom att visa hur du använder python och Hive med Azure Machine Learning Studio (klassisk) för att bygga och distribuera förutsägande modeller.

### <a name="u-sql-and-visual-studio"></a>U-SQL och Visual Studio
Den här genom gången rekommenderar att du använder Visual Studio för att redigera U-SQL-skript för att bearbeta data uppsättningen. U-SQL-skripten beskrivs här och tillhandahålls i en separat fil. Processen omfattar inmatning, utforska och sampling av data. Det visar också hur du kör ett U-SQL-skript jobb från Azure Portal. Hive-tabeller skapas för data i ett associerat HDInsight-kluster för att under lätta skapandet och distributionen av en binär klassificerings modell i Azure Machine Learning Studio.

### <a name="python"></a>Python
Den här genom gången innehåller också ett avsnitt som visar hur du skapar och distribuerar en förutsägelse modell med hjälp av python med Azure Machine Learning Studio. Det ger en Jupyter Notebook med python-skript för stegen i den här processen. Antecknings boken innehåller kod för vissa ytterligare funktions tekniks steg och modeller, till exempel multiklass-klassificering och Regressions modellering utöver den binära klassificerings modellen som beskrivs här. Regressions uppgiften är att förutsäga hur mycket av tipset som baseras på andra Tip-funktioner.

### <a name="azure-machine-learning"></a>Azure Machine Learning 
Azure Machine Learning Studio (klassisk) används för att bygga och distribuera förutsägande modeller med två metoder: först med python-skript och sedan med Hive-tabeller i ett HDInsight-kluster (Hadoop).

### <a name="scripts"></a>Skript
Endast huvud stegen beskrivs i den här genom gången. Du kan ladda ned det fullständiga **U-SQL-skriptet** och **Jupyter Notebook** från [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

## <a name="prerequisites"></a>Förutsättningar
Innan du påbörjar de här ämnena måste du ha följande:

* En Azure-prenumeration. Om du inte redan har ett kan du läsa [få en kostnads fri utvärderings version av Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Rekommenderas Visual Studio 2013 eller senare. Om du inte redan har en av dessa versioner installerade kan du hämta en kostnads fri community-version från [Visual Studio Community](https://www.visualstudio.com/vs/community/).

> [!NOTE]
> I stället för Visual Studio kan du också använda Azure Portal för att skicka Azure Data Lake frågor. Instruktioner finns i så här gör du både med Visual Studio och på portalen i avsnittet **process data med U-SQL**.
>
>


## <a name="prepare-data-science-environment-for-azure-data-lake"></a>Förbered data vetenskaps miljö för Azure Data Lake
Skapa följande resurser för att förbereda data vetenskaps miljön för den här genom gången:

* Azure Data Lake Storage (ADLS)
* Azure Data Lake Analytics (ADLA)
* Azure Blob Storage-konto
* Azure Machine Learning Studio (klassiskt) konto
* Azure Data Lake verktyg för Visual Studio (rekommenderas)

Det här avsnittet innehåller anvisningar om hur du skapar var och en av dessa resurser. Om du väljer att använda Hive-tabeller med Azure Machine Learning, i stället för python, för att bygga en modell, måste du också etablera ett HDInsight-kluster (Hadoop). Den här alternativa proceduren beskrivs i avsnittet Alternativ 2.


> [!NOTE]
> **Azure Data Lake Store** kan skapas separat eller när du skapar **Azure Data Lake Analytics** som standard lagring. Instruktioner för att skapa var och en av dessa resurser separat, men Data Lake lagrings kontot behöver inte skapas separat.
>
>

### <a name="create-an-azure-data-lake-storage"></a>Skapa en Azure Data Lake Storage


Skapa en ADLS från [Azure Portal](https://portal.azure.com). Mer information finns i [skapa ett HDInsight-kluster med data Lake Store som använder Azure Portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md). Se till att konfigurera AAD-identiteten för klustret på bladet **DataSource** på bladet för **valfri konfiguration** som beskrivs där.

 ![3](./media/data-lake-walkthrough/3-create-ADLS.PNG)

### <a name="create-an-azure-data-lake-analytics-account"></a>Skapa ett Azure Data Lake Analytics konto
Skapa ett ADLA-konto från [Azure Portal](https://portal.azure.com). Mer information finns i [Självstudier: komma igång med Azure Data Lake Analytics att använda Azure Portal](../../data-lake-analytics/data-lake-analytics-get-started-portal.md).

 ![4](./media/data-lake-walkthrough/4-create-ADLA-new.PNG)

### <a name="create-an-azure-blob-storage-account"></a>Skapa ett Azure Blob Storage-konto
Skapa ett Azure Blob Storage-konto från [Azure Portal](https://portal.azure.com). Mer information finns i avsnittet Skapa ett lagrings konto i [om Azure Storage-konton](../../storage/common/storage-account-create.md).

 ![5](./media/data-lake-walkthrough/5-Create-Azure-Blob.PNG)

### <a name="set-up-an-azure-machine-learning-studio-classic-account"></a>Konfigurera ett Azure Machine Learning Studio (klassiskt) konto
Registrera dig eller i Azure Machine Learning Studio (klassisk) från sidan för [Azure Machine Learning Studio](https://azure.microsoft.com/services/machine-learning/) . Klicka på knappen **Kom igång nu** och välj sedan en "kostnads fri arbets yta" eller "standard arbets yta". Nu är det dags att skapa experiment i Azure Machine Learning Studio.

### <a name="install-azure-data-lake-tools-recommended"></a>Installera Azure Data Lake verktyg [Recommended]
Installera Azure Data Lake verktyg för din version av Visual Studio från [Azure Data Lake verktyg för Visual Studio](https://www.microsoft.com/download/details.aspx?id=49504).

 ![6](./media/data-lake-walkthrough/6-install-ADL-tools-VS.PNG)

När installationen är klar öppnar du Visual Studio. Du bör se fliken Data Lake överst i menyn. Dina Azure-resurser bör visas i den vänstra panelen när du loggar in på ditt Azure-konto.

 ![7](./media/data-lake-walkthrough/7-install-ADL-tools-VS-done.PNG)

## <a name="the-nyc-taxi-trips-dataset"></a>NYC taxi TRIPs-datauppsättning
Data uppsättningen som används här är en offentligt tillgänglig data uppsättning – [NYC taxi TRIPs](https://www.andresmh.com/nyctaxitrips/)-datauppsättningen. NYC taxi-resan består av cirka 20 GB komprimerade CSV-filer (~ 48 GB okomprimerat), och registrerar mer än 173 000 000 enskilda resor och de priser som betalas för varje resa. Varje rese post omfattar hämtnings-och DropOff platser och tidpunkter, anonymiserats Hack (driv rutins licens nummer) och numret på Medallion (Taxins unika ID). Data omfattar alla resor under året 2013 och tillhandahålls i följande två data uppsättningar för varje månad:

CSV-trip_data innehåller information om resan, till exempel antal passagerare, upphämtnings-och DropOff punkter, varaktighet för resan och rese längd. Här följer några exempel poster:

`medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude`
`89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171`
`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066`
`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002`
`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388`
`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868`

"Trip_fare" CSV innehåller information om den avgift som betalas för varje resa, till exempel betalnings typ, pris belopp, tilläggs avgift, tips och avgifter, samt totalt betalat belopp. Här följer några exempel poster:

`medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount`
`89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7`
`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7`
`0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7`
`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6`
`DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5`

Den unika nyckeln för att ansluta till rese \_ data och rese \_ pris består av följande tre fält: Medallion, Hack- \_ licens och upphämtnings \_ tid. RAW CSV-filer kan nås från en Azure Storage-blob. U-SQL-skriptet för den här kopplingen finns i avsnittet [koppla rese-och avgifts tabeller](#join) .

## <a name="process-data-with-u-sql"></a>Bearbeta data med U-SQL
Data bearbetnings aktiviteterna som illustreras i det här avsnittet omfattar inmatning, kontroll av kvalitet, utforska och sampling av data. Hur du ansluter till rese-och avgifts tabeller visas också. I det sista avsnittet visas hur du kör ett U-SQL-skript jobb från Azure Portal. Här följer länkar till varje underavsnitt:

* [Data inmatning: läsa in data från offentlig BLOB](#ingest)
* [Data kvalitets kontroller](#quality)
* [Data utforskning](#explore)
* [Gå med i rese-och avgifts tabeller](#join)
* [Data sampling](#sample)
* [Kör U-SQL-jobb](#run)

U-SQL-skripten beskrivs här och tillhandahålls i en separat fil. Du kan ladda ned fullständiga **U-SQL-skript** från [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough).

Om du vill köra U-SQL öppnar du Visual Studio, klickar på **Arkiv--> nytt – > projekt**, väljer **U-SQL-projekt**, namn och sparar det i en mapp.

![8](./media/data-lake-walkthrough/8-create-USQL-project.PNG)

> [!NOTE]
> Det går att använda Azure-portalen för att köra U-SQL i stället för Visual Studio. Du kan navigera till Azure Data Lake Analytics resursen på portalen och skicka frågor direkt som illustreras i följande figur:
>
>

![9](./media/data-lake-walkthrough/9-portal-submit-job.PNG)

### <a name="data-ingestion-read-in-data-from-public-blob"></a><a name="ingest"></a>Data inmatning: läsa in data från offentlig BLOB

Platsen för data i Azure-blobben refereras till som wasb://container- **\_ namn \@ Blob storage \_ - \_ konto \_ Name.blob.Core.Windows.net/blob_name** och kan extraheras med hjälp av **Extractors.Csv ()**. Ersätt namnet på din behållare och lagrings kontot i följande skript för container \_ namn \@ BLOB \_ Storage \_ \_ -konto namn i wasb-adressen. Eftersom fil namnen är i samma format är det möjligt att använda **rese \_ data \_ \{ \* \} . csv** för att läsa i alla 12-testfiler.

```sql
///Read in Trip data
@trip0 =
    EXTRACT
    medallion string,
    hack_license string,
    vendor_id string,
    rate_code string,
    store_and_fwd_flag string,
    pickup_datetime string,
    dropoff_datetime string,
    passenger_count string,
    trip_time_in_secs string,
    trip_distance string,
    pickup_longitude string,
    pickup_latitude string,
    dropoff_longitude string,
    dropoff_latitude string
// This is reading 12 trip data from blob
FROM "wasb://container_name@blob_storage_account_name.blob.core.windows.net/nyctaxitrip/trip_data_{*}.csv"
USING Extractors.Csv();
```

Eftersom det finns rubriker på den första raden måste du ta bort rubrikerna och ändra kolumn typerna till lämpliga. Du kan antingen spara bearbetade data till Azure Data Lake Storage med hjälp av **swebhdfs://data_lake_storage_name. azuredatalakestorage. net/folder_name/file_name** _ eller till Azure Blob Storage-konto med  **wasb://container_name \@ blob_storage_account_name. blob. Core. windows. net/blob_name**.

```sql
// change data types
@trip =
    SELECT
    medallion,
    hack_license,
    vendor_id,
    rate_code,
    store_and_fwd_flag,
    DateTime.Parse(pickup_datetime) AS pickup_datetime,
    DateTime.Parse(dropoff_datetime) AS dropoff_datetime,
    Int32.Parse(passenger_count) AS passenger_count,
    Double.Parse(trip_time_in_secs) AS trip_time_in_secs,
    Double.Parse(trip_distance) AS trip_distance,
    (pickup_longitude==string.Empty ? 0: float.Parse(pickup_longitude)) AS pickup_longitude,
    (pickup_latitude==string.Empty ? 0: float.Parse(pickup_latitude)) AS pickup_latitude,
    (dropoff_longitude==string.Empty ? 0: float.Parse(dropoff_longitude)) AS dropoff_longitude,
    (dropoff_latitude==string.Empty ? 0: float.Parse(dropoff_latitude)) AS dropoff_latitude
FROM @trip0
WHERE medallion != "medallion";

////output data to ADL
OUTPUT @trip
TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_trip.csv"
USING Outputters.Csv();

////Output data to blob
OUTPUT @trip
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_trip.csv"
USING Outputters.Csv();
```

På samma sätt kan du läsa i pris data uppsättningarna. Högerklicka på Azure Data Lake Storage, du kan välja att titta på dina data i **Azure Portal-> datautforskaren** eller **Utforskaren** i Visual Studio.

 ![10](./media/data-lake-walkthrough/10-data-in-ADL-VS.PNG)

 ![11](./media/data-lake-walkthrough/11-data-in-ADL.PNG)

### <a name="data-quality-checks"></a><a name="quality"></a>Data kvalitets kontroller
Efter att rese-och färd pris tabellerna har lästs in kan data kvalitets kontroller göras på följande sätt. De resulterande CSV-filerna kan vara utdata till Azure Blob Storage eller Azure Data Lake Storage.

Hitta antalet Medallions och det unika antalet Medallions:

```sql
///check the number of medallions and unique number of medallions
@trip2 =
    SELECT
    medallion,
    vendor_id,
    pickup_datetime.Month AS pickup_month
    FROM @trip;

@ex_1 =
    SELECT
    pickup_month,
    COUNT(medallion) AS cnt_medallion,
    COUNT(DISTINCT(medallion)) AS unique_medallion
    FROM @trip2
    GROUP BY pickup_month;
    OUTPUT @ex_1
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_1.csv"
USING Outputters.Csv();
```

Hitta de Medallions som har fler än 100 resor:

```sql
///find those medallions that had more than 100 trips
@ex_2 =
    SELECT medallion,
           COUNT(medallion) AS cnt_medallion
    FROM @trip2
    //where pickup_datetime >= "2013-01-01t00:00:00.0000000" and pickup_datetime <= "2013-04-01t00:00:00.0000000"
    GROUP BY medallion
    HAVING COUNT(medallion) > 100;
    OUTPUT @ex_2
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_2.csv"
USING Outputters.Csv();
```

Hitta dessa ogiltiga poster med avseende på pickup_longitude:

```sql
///find those invalid records in terms of pickup_longitude
@ex_3 =
    SELECT COUNT(medallion) AS cnt_invalid_pickup_longitude
    FROM @trip
    WHERE
    pickup_longitude <- 90 OR pickup_longitude > 90;
    OUTPUT @ex_3
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_3.csv"
USING Outputters.Csv();
```

Hitta saknade värden för vissa variabler:

```sql
//check missing values
@res =
    SELECT *,
           (medallion == null? 1 : 0) AS missing_medallion
    FROM @trip;

@trip_summary6 =
    SELECT
        vendor_id,
    SUM(missing_medallion) AS medallion_empty,
    COUNT(medallion) AS medallion_total,
    COUNT(DISTINCT(medallion)) AS medallion_total_unique
    FROM @res
    GROUP BY vendor_id;
OUTPUT @trip_summary6
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_16.csv"
USING Outputters.Csv();
```

### <a name="data-exploration"></a><a name="explore"></a>Data utforskning
Gör vissa data utforskningar med följande skript för att få en bättre förståelse för data.

Hitta fördelningen av lutade och icke-lutade resor:

```sql
///tipped vs. not tipped distribution
@tip_or_not =
    SELECT *,
           (tip_amount > 0 ? 1: 0) AS tipped
    FROM @fare;

@ex_4 =
    SELECT tipped,
           COUNT(*) AS tip_freq
    FROM @tip_or_not
    GROUP BY tipped;
    OUTPUT @ex_4
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_4.csv"
USING Outputters.Csv();
```

Hitta fördelningen av Tip-mängd med skärnings värden: 0, 5, 10 och 20 dollar.

```sql
//tip class/range distribution
@tip_class =
    SELECT *,
           (tip_amount >20? 4: (tip_amount >10? 3:(tip_amount >5 ? 2:(tip_amount > 0 ? 1: 0)))) AS tip_class
    FROM @fare;
@ex_5 =
    SELECT tip_class,
           COUNT(*) AS tip_freq
    FROM @tip_class
    GROUP BY tip_class;
    OUTPUT @ex_5
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_5.csv"
USING Outputters.Csv();
```

Hitta grundläggande statistik för rese avståndet:

```sql
// find basic statistics for trip_distance
@trip_summary4 =
    SELECT
        vendor_id,
        COUNT(*) AS cnt_row,
        MIN(trip_distance) AS min_trip_distance,
        MAX(trip_distance) AS max_trip_distance,
        AVG(trip_distance) AS avg_trip_distance
    FROM @trip
    GROUP BY vendor_id;
OUTPUT @trip_summary4
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_14.csv"
USING Outputters.Csv();
```

Hitta percentiler av rese avstånd:

```sql
// find percentiles of trip_distance
@trip_summary3 =
    SELECT DISTINCT vendor_id AS vendor,
                    PERCENTILE_DISC(0.25) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
                    PERCENTILE_DISC(0.5) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc,
                    PERCENTILE_DISC(0.75) WITHIN GROUP(ORDER BY trip_distance) OVER(PARTITION BY vendor_id) AS median_trip_distance_disc
    FROM @trip;
   // group by vendor_id;
OUTPUT @trip_summary3
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_13.csv"
USING Outputters.Csv();
```

### <a name="join-trip-and-fare-tables"></a><a name="join"></a>Gå med i rese-och avgifts tabeller
Rese-och avgifts tabeller kan anslutas av Medallion, hack_license och pickup_time.

```sql
//join trip and fare table

@model_data_full =
SELECT t.*,
f.payment_type, f.fare_amount, f.surcharge, f.mta_tax, f.tolls_amount,  f.total_amount, f.tip_amount,
(f.tip_amount > 0 ? 1: 0) AS tipped,
(f.tip_amount >20? 4: (f.tip_amount >10? 3:(f.tip_amount >5 ? 2:(f.tip_amount > 0 ? 1: 0)))) AS tip_class
FROM @trip AS t JOIN  @fare AS f
ON   (t.medallion == f.medallion AND t.hack_license == f.hack_license AND t.pickup_datetime == f.pickup_datetime)
WHERE   (pickup_longitude != 0 AND dropoff_longitude != 0 );

//// output to blob
OUTPUT @model_data_full
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_full_data.csv"
USING Outputters.Csv();

////output data to ADL
OUTPUT @model_data_full
TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_7_full_data.csv"
USING Outputters.Csv();
```

För varje nivå av antal passagerare beräknar du antalet poster, Genomsnittligt Tip-belopp, varians för Tip-mängd, procent av lutade resor.

```sql
// contingency table
@trip_summary8 =
    SELECT passenger_count,
           COUNT(*) AS cnt,
           AVG(tip_amount) AS avg_tip_amount,
           VAR(tip_amount) AS var_tip_amount,
           SUM(tipped) AS cnt_tipped,
           (float)SUM(tipped)/COUNT(*) AS pct_tipped
    FROM @model_data_full
    GROUP BY passenger_count;
    OUTPUT @trip_summary8
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_17.csv"
USING Outputters.Csv();
```

### <a name="data-sampling"></a><a name="sample"></a>Data sampling
Först väljer du 0,1% av data från den kopplade tabellen:

```sql
//random select 1/1000 data for modeling purpose
@addrownumberres_randomsample =
SELECT *,
        ROW_NUMBER() OVER() AS rownum
FROM @model_data_full;

@model_data_random_sample_1_1000 =
SELECT *
FROM @addrownumberres_randomsample
WHERE rownum % 1000 == 0;

OUTPUT @model_data_random_sample_1_1000
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_7_random_1_1000.csv"
USING Outputters.Csv();
```

Gör sedan Stratified-sampling av binär variabel tip_class:

```sql
//stratified random select 1/1000 data for modeling purpose
@addrownumberres_stratifiedsample =
SELECT *,
        ROW_NUMBER() OVER(PARTITION BY tip_class) AS rownum
FROM @model_data_full;

@model_data_stratified_sample_1_1000 =
SELECT *
FROM @addrownumberres_stratifiedsample
WHERE rownum % 1000 == 0;
//// output to blob
OUTPUT @model_data_stratified_sample_1_1000
TO "wasb://container_name@blob_storage_account_name.blob.core.windows.net/demo_ex_9_stratified_1_1000.csv"
USING Outputters.Csv();
////output data to ADL
OUTPUT @model_data_stratified_sample_1_1000
TO "swebhdfs://data_lake_storage_name.azuredatalakestore.net/nyctaxi_folder/demo_ex_9_stratified_1_1000.csv"
USING Outputters.Csv();
```

### <a name="run-u-sql-jobs"></a><a name="run"></a>Kör U-SQL-jobb
När du har redigerat U-SQL-skript kan du skicka dem till servern med hjälp av ditt Azure Data Lake Analytics-konto. Klicka på **data Lake**, **Skicka jobb**, Välj ditt **analys konto**, Välj **parallellitet** och klicka på **Skicka** .

 ![12](./media/data-lake-walkthrough/12-submit-USQL.PNG)

När jobbet har slutförts visas jobbets status i Visual Studio för övervakning. När jobbet har slutförts kan du till och med upprepa jobb körnings processen och ta reda på Flask hals stegen för att förbättra jobb effektiviteten. Du kan också gå till Azure Portal för att kontrol lera status för dina U-SQL-jobb.

 ![13](./media/data-lake-walkthrough/13-USQL-running-v2.PNG)

 ![14](./media/data-lake-walkthrough/14-USQL-jobs-portal.PNG)

Nu kan du kontrol lera utdatafilerna antingen i Azure Blob Storage eller Azure Portal. Använd Stratified exempel data för vår modell i nästa steg.

 ![15](./media/data-lake-walkthrough/15-U-SQL-output-csv.PNG)

 ![16](./media/data-lake-walkthrough/16-U-SQL-output-csv-portal.PNG)

## <a name="build-and-deploy-models-in-azure-machine-learning"></a>Bygga och distribuera modeller i Azure Machine Learning
Det finns två alternativ för att hämta data till Azure Machine Learning för att bygga och

* I det första alternativet använder du de exempel data som har skrivits till en Azure-Blob (i stegen för **data sampling** ovan) och använder python för att bygga och distribuera modeller från Azure Machine Learning.
* I det andra alternativet frågar du efter data i Azure Data Lake direkt med en Hive-fråga. Det här alternativet kräver att du skapar ett nytt HDInsight-kluster eller använder ett befintligt HDInsight-kluster där Hive-tabellerna pekar på nya taxi-data i Azure Data Lake Storage.  Båda alternativen beskrivs i följande avsnitt.

## <a name="option-1-use-python-to-build-and-deploy-machine-learning-models"></a>Alternativ 1: Använd python för att bygga och distribuera Machine Learning-modeller
Skapa och distribuera Machine Learning-modeller med python genom att skapa en Jupyter Notebook på den lokala datorn eller i Azure Machine Learning Studio. Jupyter Notebook som finns på [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/AzureDataLakeWalkthrough) innehåller den fullständiga koden för att utforska, visualisera data, funktioner, modellering och distribution. I den här artikeln omfattas bara modellering och distribution.

### <a name="import-python-libraries"></a>Importera python-bibliotek
Följande python-paket krävs för att köra exempel Jupyter Notebook eller python-skriptfilen. Om du använder tjänsten Azure Machine Learning Notebook har dessa paket förinstallerats.

```python
import pandas as pd
from pandas import Series, DataFrame
import numpy as np
import matplotlib.pyplot as plt
from time import time
import pyodbc
import os
from azure.storage.blob import BlobService
import tables
import time
import zipfile
import random
import sklearn
from sklearn.linear_model import LogisticRegression
from sklearn.cross_validation import train_test_split
from sklearn import metrics
from __future__ import division
from sklearn import linear_model
from azureml import services
```

### <a name="read-in-the-data-from-blob"></a>Läs i data från BLOB
* Connection String (Anslutningssträng)

  ```text
  CONTAINERNAME = 'test1'
  STORAGEACCOUNTNAME = 'XXXXXXXXX'
  STORAGEACCOUNTKEY = 'YYYYYYYYYYYYYYYYYYYYYYYYYYYY'
  BLOBNAME = 'demo_ex_9_stratified_1_1000_copy.csv'
  blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
  ```

* Läs in som text

  ```text
  t1 = time.time()
  data = blob_service.get_blob_to_text(CONTAINERNAME,BLOBNAME).split("\n")
  t2 = time.time()
  print(("It takes %s seconds to read in "+BLOBNAME) % (t2 - t1))
  ```

  ![17](./media/data-lake-walkthrough/17-python_readin_csv.PNG)

* Lägg till kolumn namn och separata kolumner

  ```text
  colnames = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime',
  'passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
  'payment_type', 'fare_amount', 'surcharge', 'mta_tax', 'tolls_amount',  'total_amount', 'tip_amount', 'tipped', 'tip_class', 'rownum']
  df1 = pd.DataFrame([sub.split(",") for sub in data], columns = colnames)
  ```

* Ändra vissa kolumner till numeriska

  ```text
  cols_2_float = ['trip_time_in_secs','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude',
  'fare_amount', 'surcharge','mta_tax','tolls_amount','total_amount','tip_amount', 'passenger_count','trip_distance'
  ,'tipped','tip_class','rownum']
  for col in cols_2_float:
      df1[col] = df1[col].astype(float)
  ```

### <a name="build-machine-learning-models"></a>Bygg maskin inlärnings modeller
Här skapar du en binär klassificerings modell för att förutsäga om en resa är lutad eller inte. I Jupyter Notebook kan du hitta andra två modeller: klassificering av flera klasser och Regressions modeller.

* Först måste du skapa dummy-variabler som kan användas i scikit – lära modeller

  ```python
  df1_payment_type_dummy = pd.get_dummies(df1['payment_type'], prefix='payment_type_dummy')
  df1_vendor_id_dummy = pd.get_dummies(df1['vendor_id'], prefix='vendor_id_dummy')
  ```

* Skapa data ram för modellering

  ```python
  cols_to_keep = ['tipped', 'trip_distance', 'passenger_count']
  data = df1[cols_to_keep].join([df1_payment_type_dummy,df1_vendor_id_dummy])

  X = data.iloc[:,1:]
  Y = data.tipped
    ```

* Utbildning och testning 60-40 Split

  ```python
  X_train, X_test, Y_train, Y_test = train_test_split(X, Y, test_size=0.4, random_state=0)
  ```

* Logistisk regression i tränings uppsättning

  ```python
  model = LogisticRegression()
  logit_fit = model.fit(X_train, Y_train)
  print ('Coefficients: \n', logit_fit.coef_)
  Y_train_pred = logit_fit.predict(X_train)
  ```

    ![c1](./media/data-lake-walkthrough/c1-py-logit-coefficient.PNG)

* Data uppsättning för Poäng test

  ```python
  Y_test_pred = logit_fit.predict(X_test)
  ```

* Beräkna utvärderings mått

  ```python
  fpr_train, tpr_train, thresholds_train = metrics.roc_curve(Y_train, Y_train_pred)
  print fpr_train, tpr_train, thresholds_train

  fpr_test, tpr_test, thresholds_test = metrics.roc_curve(Y_test, Y_test_pred)
  print fpr_test, tpr_test, thresholds_test

  #AUC
  print metrics.auc(fpr_train,tpr_train)
  print metrics.auc(fpr_test,tpr_test)

  #Confusion Matrix
  print metrics.confusion_matrix(Y_train,Y_train_pred)
  print metrics.confusion_matrix(Y_test,Y_test_pred)
  ```

    ![c2](./media/data-lake-walkthrough/c2-py-logit-evaluation.PNG)

### <a name="build-web-service-api-and-consume-it-in-python"></a>Bygg webb tjänst-API och Använd det i python
Du vill operationalisera Machine Learning-modellen när den har skapats. Den binära logistik modellen används här som exempel. Kontrol lera att scikit-versionen på den lokala datorn är 0.15.1 (Azure Machine Learning Studio redan har minst den här versionen).

* Hitta dina autentiseringsuppgifter för arbets ytan från Azure Machine Learning Studio (klassiska) inställningar. I Azure Machine Learning Studio klickar du på **Inställningar**  -->  **namn**  -->  **auktorisering tokens**.

    ![c3](./media/data-lake-walkthrough/c3-workspace-id.PNG)

  ```output
  workspaceid = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'
  auth_token = 'xxxxxxxxxxxxxxxxxxxxxxxxxxx'
  ```

* Skapa webb tjänst

  ```python
  @services.publish(workspaceid, auth_token)
  @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float, payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
  @services.returns(int) #0, or 1
  def predictNYCTAXI(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
      inputArray = [trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH, payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS]
      return logit_fit.predict(inputArray)
  ```

* Hämta autentiseringsuppgifter för webb tjänst

  ```python
  url = predictNYCTAXI.service.url
  api_key =  predictNYCTAXI.service.api_key

  print url
  print api_key

  @services.service(url, api_key)
  @services.types(trip_distance = float, passenger_count = float, payment_type_dummy_CRD = float, payment_type_dummy_CSH=float,payment_type_dummy_DIS = float, payment_type_dummy_NOC = float, payment_type_dummy_UNK = float, vendor_id_dummy_CMT = float, vendor_id_dummy_VTS = float)
  @services.returns(float)
  def NYCTAXIPredictor(trip_distance, passenger_count, payment_type_dummy_CRD, payment_type_dummy_CSH,payment_type_dummy_DIS, payment_type_dummy_NOC, payment_type_dummy_UNK, vendor_id_dummy_CMT, vendor_id_dummy_VTS ):
      pass
  ```

* Anropa webb tjänst-API. Vänta vanligt vis 5-10 sekunder efter föregående steg.

  ```python
  NYCTAXIPredictor(1,2,1,0,0,0,0,0,1)
  ```

    ![c4](./media/data-lake-walkthrough/c4-call-API.PNG)

## <a name="option-2-create-and-deploy-models-directly-in-azure-machine-learning"></a>Alternativ 2: skapa och distribuera modeller direkt i Azure Machine Learning
Azure Machine Learning Studio (klassisk) kan läsa data direkt från Azure Data Lake Storage och sedan användas för att skapa och distribuera modeller. Den här metoden använder en Hive-tabell som pekar på Azure Data Lake Storage. Ett separat Azure HDInsight-kluster måste tillhandahållas för Hive-tabellen. 

### <a name="create-an-hdinsight-linux-cluster"></a>Skapa ett HDInsight Linux-kluster
Skapa ett HDInsight-kluster (Linux) från [Azure Portal](https://portal.azure.com). Mer information finns i avsnittet **skapa ett HDInsight-kluster med åtkomst till Azure Data Lake Storage** i [skapa ett HDInsight-kluster med data Lake Store med hjälp av Azure Portal](../../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md).

 ![18](./media/data-lake-walkthrough/18-create_HDI_cluster.PNG)

### <a name="create-hive-table-in-hdinsight"></a>Skapa Hive-tabell i HDInsight
Nu skapar du Hive-tabeller som ska användas i Azure Machine Learning Studio (klassisk) i HDInsight-klustret med hjälp av de data som lagras i Azure Data Lake Storage i föregående steg. Gå till HDInsight-klustret har skapats. Klicka på **Inställningar**  -->  **Egenskaper**  -->  **kluster AAD Identity**  -->  **ADLS Access**, kontrol lera att ditt Azure Data Lake Storage-konto har lagts till i listan med Läs-, skriv-och körnings rättigheter.

 ![19](./media/data-lake-walkthrough/19-HDI-cluster-add-ADLS.PNG)

Klicka sedan på **instrument panel** bredvid knappen **Inställningar** så öppnas ett fönster. Klicka på **Hive-vy** i det övre högra hörnet på sidan och se **Frågeredigeraren**.

 ![20](./media/data-lake-walkthrough/20-HDI-dashboard.PNG)

 ![21](./media/data-lake-walkthrough/21-Hive-Query-Editor-v2.PNG)

Klistra in följande Hive-skript för att skapa en tabell. Platsen för data källan finns Azure Data Lake Storage referens på det här sättet: **ADL://data_lake_store_name. azuredatalakestore. net: 443/folder_name/file_name**.

```hiveql
CREATE EXTERNAL TABLE nyc_stratified_sample
(
    medallion string,
    hack_license string,
    vendor_id string,
    rate_code string,
    store_and_fwd_flag string,
    pickup_datetime string,
    dropoff_datetime string,
    passenger_count string,
    trip_time_in_secs string,
    trip_distance string,
    pickup_longitude string,
    pickup_latitude string,
    dropoff_longitude string,
    dropoff_latitude string,
  payment_type string,
  fare_amount string,
  surcharge string,
  mta_tax string,
  tolls_amount string,
  total_amount string,
  tip_amount string,
  tipped string,
  tip_class string,
  rownum string
  )
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
LOCATION 'adl://data_lake_storage_name.azuredatalakestore.net:443/nyctaxi_folder/demo_ex_9_stratified_1_1000_copy.csv';
```

När frågan har slutförts bör du se resultatet så här:

 ![22](./media/data-lake-walkthrough/22-Hive-Query-results.PNG)

### <a name="build-and-deploy-models-in-azure-machine-learning-studio"></a>Bygga och distribuera modeller i Azure Machine Learning Studio
Nu är du redo att bygga och distribuera en modell som förutsäger om ett tips betalas med Azure Machine Learning. Stratified exempel data är redo att användas i den här binära klassificeringen (Tip eller not). Förutsägande modeller som använder multiklass-klassificering (tip_class) och regression (tip_amount) kan också skapas och distribueras med Azure Machine Learning Studio, men här visas bara hur du hanterar fallet med den binära klassificerings modellen.

1. Hämta data till Azure Machine Learning Studio (klassisk) med modulen **Importera data** som finns i avsnittet **data indata och utdata** . Mer information finns på referens sidan [Importera data modul](/azure/machine-learning/studio-module-reference/import-data) .
2. Välj **Hive-fråga** som **data källa** i panelen **Egenskaper** .
3. Klistra in följande Hive-skript i Registereditorns **Hive-databas Frågeredigeraren**

    ```hiveql
    select * from nyc_stratified_sample;
    ```

4. Ange URI för HDInsight-kluster (denna URI finns i Azure Portal), Hadoop-autentiseringsuppgifter, plats för utdata och Azure Storage konto namn/nyckel/behållar namn.

   ![23](./media/data-lake-walkthrough/23-reader-module-v3.PNG)

Ett exempel på ett binära klassificerings experiment som läser data från Hive-tabellen visas i följande figur:

 ![24](./media/data-lake-walkthrough/24-AML-exp.PNG)

När experimentet har skapats klickar du på **Konfigurera** webb tjänst för  -->  **förutsägelse** webb tjänst

 ![25](./media/data-lake-walkthrough/25-AML-exp-deploy.PNG)

Kör det automatiskt skapade bedömnings experimentet när det är klart och klicka på **distribuera webb tjänst**

 ![26](./media/data-lake-walkthrough/26-AML-exp-deploy-web.PNG)

Instrument panelen för webb tjänster visas kort:

 ![27](./media/data-lake-walkthrough/27-AML-web-api.PNG)

## <a name="summary"></a>Sammanfattning
Genom att slutföra den här genom gången har du skapat en data vetenskaps miljö för att skapa skalbara lösningar från slut punkt till slut punkt i Azure Data Lake. Den här miljön användes för att analysera en stor offentlig data uppsättning, som tar den genom de kanoniska stegen i data vetenskaps processen, från data förvärv via modell träning och sedan till distributionen av modellen som en webb tjänst. U-SQL användes för att bearbeta, utforska och sampla data. Python och Hive användes med Azure Machine Learning Studio (klassisk) för att bygga och distribuera förutsägande modeller.

## <a name="whats-next"></a>Nästa steg
Utbildnings vägen för [team data science-processen (TDSP)](./index.yml) innehåller länkar till avsnitt som beskriver varje steg i avancerad analys process. Det finns en serie med genom gångar som är indelade på sidan [genom gång av team data vetenskaps process](walkthroughs.md) som visar hur du använder resurser och tjänster i olika förutsägelse analys scenarier:

* [Team data science-processen i praktiken: använda Azure Synapse Analytics](sqldw-walkthrough.md)
* [Team data vetenskaps processen i praktiken: använda HDInsight Hadoop-kluster](hive-walkthrough.md)
* [Team data science-processen: använda SQL Server](sql-walkthrough.md)
* [Översikt över data vetenskaps processen med Spark på Azure HDInsight](spark-overview.md)