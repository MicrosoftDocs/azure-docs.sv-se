---
title: Utforska data i ett Hadoop-kluster – team data science process
description: Använd Team data science-processen för ett scenario från slut punkt till slut punkt som använder ett HDInsight Hadoop-kluster för att bygga och distribuera en modell.
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
ms.openlocfilehash: 53f50e98bcec4b8ace342808f0bcfd96770834b0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "96002229"
---
# <a name="the-team-data-science-process-in-action-use-azure-hdinsight-hadoop-clusters"></a>Team data science-processen i praktiken: Använd Azure HDInsight Hadoop kluster
I den här genom gången använder vi [team data science-processen (TDSP)](overview.md) i ett scenario från slut punkt till slut punkt. Vi använder ett [Azure HDInsight Hadoop-kluster](https://azure.microsoft.com/services/hdinsight/) för att lagra, utforska och tillhandahålla data från den allmänt tillgängliga [NYC taxi TRIPs](https://www.andresmh.com/nyctaxitrips/) -datauppsättningen och för att få fram exempel på data. För att hantera binära och Regressions aktiviteter med multiklasser, bygger vi modeller av data med Azure Machine Learning. 

En genom gång som visar hur du hanterar en större data uppsättning finns i [team data science process-använda Azure HDInsight Hadoop kluster på en data uppsättning på 1 TB](hive-criteo-walkthrough.md).

Du kan också använda en IPython Notebook för att utföra de uppgifter som visas i genom gången som använder data uppsättningen 1 – TB. Mer information finns i [Criteo-genom gång med en Hive ODBC-anslutning](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb).

## <a name="nyc-taxi-trips-dataset-description"></a><a name="dataset"></a>Beskrivning av NYC taxi TRIPs-datauppsättning
NYC taxi-resan är ungefär 20 GB komprimerade kommaavgränsade värden (CSV) (~ 48 GB okomprimerat). Det har fler än 173 000 000 enskilda resor och omfattar de priser som betalas för varje resa. Varje rese post omfattar hämtnings-och DropOff plats och tid, anonymiserats Hack (driv rutins licens nummer) och Medallion nummer (Taxins unika ID). Data omfattar alla resor under året 2013 och tillhandahålls i följande två data uppsättningar för varje månad:

- Trip_data CSV-filer innehåller information om resan: antalet passagerare, hämtning och DropOff punkter, varaktighet för resan och rese längd. Här följer några exempel poster:

  `medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude`

  `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171`

  `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066`

  `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002`

  `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388`

  `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868`

- Trip_fare CSV-filer innehåller information om avgiften som betalas för varje resa: betalnings typ, avgifts belopp, tilläggs avgift, tips och avgifter och totalt betalat belopp. Här följer några exempel poster:

  `medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount`

  `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7`

  `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7`

  `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7`

  `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6`

  `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5`

Den unika nyckeln för att ansluta till rese \_ data och rese \_ pris består av fälten: Medallion, Hack \_ License och upphämtnings \_ datum. För att få all information som är relevant för en viss resa räcker det att ansluta till dessa tre nycklar.

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>Exempel på förutsägelse aktiviteter
Bestäm vilken typ av förutsägelser du vill göra baserat på data analys för att klargör nödvändiga process uppgifter. Här följer tre exempel på förutsägelse problem som vi åtgärdar i den här genom gången, allt baserat på *tips \_ mängden*:

- **Binära klassificering**: förutsäga huruvida ett tips har betalats för en resa. Det vill säga ett *Tip- \_ belopp* som är större än $0 är ett positivt exempel, medan ett *tips \_ på* $0 är ett negativt exempel.

  - Klass 0: tip_amount = $0
  - Klass 1: tip_amount > $0

- **Klassificering** av flera klasser: förutsäga antalet Tip-mängder som betalas för resan. Vi delar upp *tipset \_* i fem klasser:

  - Klass 0: tip_amount = $0
  - Klass 1: tip_amount > $0 och tip_amount <= $5
  - Klass 2: tip_amount > $5 och tip_amount <= $10
  - Klass 3: tip_amount > $10 och tip_amount <= $20
  - Klass 4: tip_amount > $20

- **Regressions uppgift**: förutsäga hur mycket av tipset som betalats för en resa.  

## <a name="set-up-an-hdinsight-hadoop-cluster-for-advanced-analytics"></a><a name="setup"></a>Konfigurera ett HDInsight Hadoop-kluster för avancerad analys
> [!NOTE]
> Detta är vanligt vis en administratörs uppgift.
> 
> 

Du kan konfigurera en Azure-miljö för avancerad analys som använder ett HDInsight-kluster i tre steg:

1. [Skapa ett lagrings konto](../../storage/common/storage-account-create.md): det här lagrings kontot används för att lagra data i Azure Blob Storage. Data som används i HDInsight-kluster finns också här.
2. [Anpassa Azure HDInsight Hadoop kluster för avancerad analys process och teknik](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Det här steget skapar ett HDInsight Hadoop-kluster med 64-bitars Anaconda python 2,7 installerat på alla noder. Det finns två viktiga steg att komma ihåg när du anpassar ditt HDInsight-kluster.
   
   * Kom ihåg att länka lagrings kontot som skapades i steg 1 med ditt HDInsight-kluster när du skapar det. Det här lagrings kontot har åtkomst till data som bearbetas i klustret.
   * När du har skapat klustret aktiverar du fjärråtkomst till Head-noden i klustret. Bläddra till fliken **konfiguration** och välj **Aktivera fjärran sluten**. Det här steget anger de användarautentiseringsuppgifter som används för fjärrinloggning.
3. [Skapa en Azure Machine Learning arbets yta](../classic/create-workspace.md): du använder den här arbets ytan för att skapa maskin inlärnings modeller. Den här uppgiften åtgärdas när du har slutfört en första data utforskning och-sampling, med hjälp av HDInsight-klustret.

## <a name="get-the-data-from-a-public-source"></a><a name="getdata"></a>Hämta data från en offentlig källa
> [!NOTE]
> Detta är vanligt vis en administratörs uppgift.
> 
> 

Om du vill kopiera data uppsättningen [NYC taxi TRIPs](https://www.andresmh.com/nyctaxitrips/) till din dator från dess offentliga plats använder du någon av de metoder som beskrivs i [Flytta data till och från Azure Blob Storage](move-azure-blob.md).

Här beskrivs hur du använder AzCopy för att överföra filer som innehåller data. Hämta och installera AzCopy genom att följa anvisningarna i [komma igång med kommando rads verktyget AzCopy](../../storage/common/storage-use-azcopy-v10.md).

1. Kör följande AzCopy-kommandon från ett kommando tolks fönster och Ersätt *\<path_to_data_folder>* med det önskade målet:

    ```console
    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S
    ```

1. När kopieringen är klar visas totalt 24 zippade filer i mappen data som valts. Zippa upp de hämtade filerna till samma katalog på den lokala datorn. Anteckna mappen där de okomprimerade filerna finns. Den här mappen kallas i det som *\<path\_to\_unzipped_data\_files\>* följer.

## <a name="upload-the-data-to-the-default-container-of-the-hdinsight-hadoop-cluster"></a><a name="upload"></a>Ladda upp data till standard behållaren för HDInsight Hadoop-klustret
> [!NOTE]
> Detta är vanligt vis en administratörs uppgift.
> 
> 

I följande AzCopy-kommandon ersätter du följande parametrar med de faktiska värden som du angav när du skapade Hadoop-klustret och avzippar datafilerna.

* ***\<path_to_data_folder>*** Katalogen (tillsammans med sökvägen) på datorn som innehåller de zippade datafilerna.  
* ***\<storage account name of Hadoop cluster>*** Det lagrings konto som är associerat med ditt HDInsight-kluster.
* ***\<default container of Hadoop cluster>*** Standard behållaren som används av klustret. Namnet på standard behållaren är vanligt vis samma namn som själva klustret. Om klustret till exempel kallas "abc123.azurehdinsight.net" är standard behållaren vi abc123.
* ***\<storage account key>*** Nyckeln för det lagrings konto som används av klustret.

Kör följande två AzCopy-kommandon från en kommando tolk eller ett Windows PowerShell-fönster.

Det här kommandot överför rese data till ***nyctaxitripraw*** -katalogen i standard behållaren för Hadoop-klustret.

```console
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv
```

Det här kommandot överför pris data till ***nyctaxifareraw*** -katalogen i standard behållaren för Hadoop-klustret.

```console
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv
```

Data bör nu finnas i Blob Storage och vara redo att användas i HDInsight-klustret.

## <a name="sign-in-to-the-head-node-of-hadoop-cluster-and-prepare-for-exploratory-data-analysis"></a><a name="#download-hql-files"></a>Logga in på Head-noden i Hadoop-kluster och Förbered för analys av undersöknings data
> [!NOTE]
> Detta är vanligt vis en administratörs uppgift.
> 
> 

Om du vill få åtkomst till Head-noden i klustret för analys av exempel data och data insamlingen följer du proceduren som beskrivs i [åtkomst till Head-noden i Hadoop-kluster](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md).

I den här genom gången använder vi i första hand frågor som skrivits i [Hive](https://hive.apache.org/), ett SQL-liknande frågespråk, för att utföra preliminära data utforskningar. Hive-frågorna lagras i ". HQL"-filer. Vi kan sedan stänga av data som ska användas i Machine Learning för att skapa modeller.

För att förbereda klustret för analys av exempel data kan du hämta filerna ". HQL" som innehåller relevanta Hive-skript från [GitHub](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) till en lokal katalog (C:\Temp) på Head-noden. Öppna kommando tolken inifrån noden Head i klustret och kör följande två kommandon:

```console
set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'

@powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"
```

De här två kommandona laddar ned alla '. HQL '-filer som behövs i den här genom gången till den lokala katalogen ***C:\temp&#92;*** i head-noden.

## <a name="create-hive-database-and-tables-partitioned-by-month"></a><a name="#hive-db-tables"></a>Skapa Hive-databas och tabeller partitionerade efter månad
> [!NOTE]
> Den här uppgiften är normalt för en administratör.
> 
> 

Du är nu redo att skapa Hive-tabeller för NYC taxi-datauppsättningen.
I noden Head i Hadoop-klustret öppnar du kommando raden för Hadoop på Skriv bordet för Head-noden. Ange Hive-katalogen genom att köra följande kommando:

```console
cd %hive_home%\bin
```

> [!NOTE]
> Kör alla Hive-kommandon i den här genom gången från registrerings data filen för Hive bin/katalog. Detta hanterar eventuella Sök vägs problem automatiskt. Vi använder termerna "Hive-katalog fråga", "Hive-bin/katalog fråga" och "Hadoop-kommandoraden" i den här genom gången.
> 
> 

Från Hive-katalogen, kör du följande kommando på Hadoop-kommandoraden i head-noden som skapar Hive-databasen och-tabellerna:

```console
hive -f "C:\temp\sample_hive_create_db_and_tables.hql"
```

Här är innehållet i Hive- **C:\temp\sample \_ \_ skapa \_ db- \_ och \_ Tables. HQL** -fil som skapar Hive-databasen **nyctaxidb** och tabellerna **resa** och **pris**.

```hiveql
create database if not exists nyctaxidb;

create external table if not exists nyctaxidb.trip
(
    medallion string,
    hack_license string,
    vendor_id string,
    rate_code string,
    store_and_fwd_flag string,
    pickup_datetime string,
    dropoff_datetime string,
    passenger_count int,
    trip_time_in_secs double,
    trip_distance double,
    pickup_longitude double,
    pickup_latitude double,
    dropoff_longitude double,
    dropoff_latitude double)  
PARTITIONED BY (month int)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');

create external table if not exists nyctaxidb.fare
(
    medallion string,
    hack_license string,
    vendor_id string,
    pickup_datetime string,
    payment_type string,
    fare_amount double,
    surcharge double,
    mta_tax double,
    tip_amount double,
    tolls_amount double,
    total_amount double)
PARTITIONED BY (month int)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');
```

Detta Hive-skript skapar två tabeller:

* **Rese** tabellen innehåller information om resan för varje åsidosättning (driv rutins information, hämtnings tid, rese avstånd och tider).
* **Avgifts** tabellen innehåller information om biljetten (pris belopp, Tip-belopp, avgifter och tillägg).

Om du behöver ytterligare hjälp med dessa procedurer, eller om du vill undersöka alternativ, kan du läsa avsnittet [Skicka Hive-frågor direkt från Hadoop-kommandoraden](move-hive-tables.md#submit).

## <a name="load-data-to-hive-tables-by-partitions"></a><a name="#load-data"></a>Läs in data till Hive-tabeller efter partitioner
> [!NOTE]
> Den här uppgiften är normalt för en administratör.
> 
> 

NYC taxi-datauppsättningen har en naturlig partitionering efter månad, som vi använder för att aktivera snabbare bearbetning och fråge tider. Följande PowerShell-kommandon (som utfärdats från Hive-katalogen med hjälp av Hadoop-kommandoraden) läser in data till Hive-tabellerna för rese-och registrerings nivå, partitionerade efter månad.

```powershell
for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")
```

**\_ Registrerings data filen \_ \_ för exempel data \_ by \_ partitions. HQL** innehåller följande **inläsnings** kommandon:

```hiveql
LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});
```

Ett antal Hive-frågor som används här i utforsknings processen innebär bara att titta på en eller två partitioner. Men dessa frågor kan köras över hela data uppsättningen.

### <a name="show-databases-in-the-hdinsight-hadoop-cluster"></a><a name="#show-db"></a>Visa databaser i HDInsight Hadoop-klustret
Om du vill visa databaserna som skapats i HDInsight Hadoop-kluster i kommando rads fönstret Hadoop kör du följande kommando i Hadoop-kommando raden:

```console
hive -e "show databases;"
```

### <a name="show-the-hive-tables-in-the-nyctaxidb-database"></a><a name="#show-tables"></a>Visa Hive-tabellerna i **nyctaxidb** -databasen
Om du vill visa tabellerna i **nyctaxidb** -databasen kör du följande kommando i Hadoop-kommando raden:

```console
hive -e "show tables in nyctaxidb;"
```

Vi kan bekräfta att tabellerna är partitionerade genom att köra följande kommando:

```console
hive -e "show partitions nyctaxidb.trip;"
```

Här är förväntad utdata:

```output
month=1
month=10
month=11
month=12
month=2
month=3
month=4
month=5
month=6
month=7
month=8
month=9
Time taken: 2.075 seconds, Fetched: 12 row(s)
```

På samma sätt kan vi se till att tabellen avgift är partitionerad genom att köra följande kommando:

```console
hive -e "show partitions nyctaxidb.fare;"
```

Här är förväntad utdata:

```output
month=1
month=10
month=11
month=12
month=2
month=3
month=4
month=5
month=6
month=7
month=8
month=9
Time taken: 1.887 seconds, Fetched: 12 row(s)
```

## <a name="data-exploration-and-feature-engineering-in-hive"></a><a name="#explore-hive"></a>Data utforskning och funktions teknik i Hive
> [!NOTE]
> Detta är vanligt vis en data expert-uppgift.
> 
> 

Du kan använda Hive-frågor för att utföra data utforsknings-och funktions teknik uppgifter för data som läses in i Hive-tabellerna. Här följer några exempel på sådana uppgifter:

* Visa de 10 viktigaste posterna i båda tabellerna.
* Utforska data distributioner av några fält i varierande tidsfönster.
* Undersök data kvaliteten på fälten longitud och Latitude.
* Generera binära och multiklassens klassificerings etiketter baserat på tips mängden.
* Generera funktioner genom att beräkna avstånd för direkt resan.

### <a name="exploration-view-the-top-10-records-in-table-trip"></a>Utforskning: Visa de 10 översta posterna i tabell resan
> [!NOTE]
> Detta är vanligt vis en data expert-uppgift.
> 
> 

Granska 10 poster från varje tabell för att se vilka data som ser ut. Om du vill granska posterna kör du följande två frågor separat från Hive-katalogen i kommando rads konsolen för Hadoop.

För att hämta de 10 viktigaste posterna i rese tabellen från den första månaden:

```console
hive -e "select * from nyctaxidb.trip where month=1 limit 10;"
```

För att hämta de 10 viktigaste posterna i tabellen avgift från den första månaden:

```console
hive -e "select * from nyctaxidb.fare where month=1 limit 10;"
```

Du kan spara posterna i en fil för bekväm visning med en liten ändring av föregående fråga:

```console
hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput
```

### <a name="exploration-view-the-number-of-records-in-each-of-the-12-partitions"></a>Undersökning: Visa antalet poster i var och en av de 12 partitionerna
> [!NOTE]
> Detta är vanligt vis en data expert-uppgift.
> 
> 

Intresse rad är hur antalet resor varierar under kalender året. Gruppering per månad visar distributionen av resor.

```console
hive -e "select month, count(*) from nyctaxidb.trip group by month;"
```

Det här kommandot ger följande utdata:

```output
1       14776615
2       13990176
3       15749228
4       15100468
5       15285049
6       14385456
7       13823840
8       12597109
9       14107693
10      15004556
11      14388451
12      13971118
Time taken: 283.406 seconds, Fetched: 12 row(s)
```

Här är den första kolumnen månaden, och den andra är antalet resor för den månaden.

Vi kan också räkna antalet poster i vår rese data uppsättning genom att köra följande kommando i Hive-katalogens kommando tolk:

```console
hive -e "select count(*) from nyctaxidb.trip;"
```

Det här kommandot ger:

```output
173179759
Time taken: 284.017 seconds, Fetched: 1 row(s)
```

Genom att använda kommandon som liknar dem som visas för rese data uppsättningen kan vi utfärda Hive-frågor från Hive-katalogen fråga efter pris data uppsättningen för att verifiera antalet poster.

```console
hive -e "select month, count(*) from nyctaxidb.fare group by month;"
```

Det här kommandot genererar följande utdata:

```output
1       14776615
2       13990176
3       15749228
4       15100468
5       15285049
6       14385456
7       13823840
8       12597109
9       14107693
10      15004556
11      14388451
12      13971118
Time taken: 253.955 seconds, Fetched: 12 row(s)
```

Exakt samma antal resor per månad returneras för båda data uppsättningarna, vilket ger den första verifieringen att data har lästs in på rätt sätt.

Du kan räkna det totala antalet poster i pris data uppsättningen genom att använda följande kommando från Hive-katalogens kommando tolk:

```console
hive -e "select count(*) from nyctaxidb.fare;"
```

Det här kommandot ger:

```output
173179759
Time taken: 186.683 seconds, Fetched: 1 row(s)
```

Det totala antalet poster i båda tabellerna är också detsamma, vilket ger en andra verifiering att data har lästs in på rätt sätt.

### <a name="exploration-trip-distribution-by-medallion"></a>Undersökning: rese distribution per Medallion
> [!NOTE]
> Den här analysen är vanligt vis en data expert-uppgift.
> 
> 

I det här exemplet identifieras medallions (taxi-nummer) med fler än 100 resor inom en viss tids period. Frågan fördelar från den partitionerade tabell åtkomsten, eftersom den är ett villkor för en partitions variabel **månad**. Frågeresultatet skrivs till en lokal fil, **queryoutput. tsv**, i `C:\temp` på Head-noden.

```console
hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv
```

Här följer innehållet i exemplet på **\_ Hive- \_ \_ antal \_ med \_ Medallion. HQL** -filen för granskning.

```hiveql
SELECT medallion, COUNT(*) as med_count
FROM nyctaxidb.fare
WHERE month<=3
GROUP BY medallion
HAVING med_count > 100
ORDER BY med_count desc;
```

Medallion i NYC taxi-datauppsättningen identifierar en unik cab. Du kan identifiera vilka hytter som är parallellt med varandra genom att fråga vilka som har gjort fler än ett visst antal resor under en viss tids period. I följande exempel identifieras hytter som gjorde mer än hundratals resor under de första tre månaderna och sparar frågeresultaten till en lokal fil, **C:\temp\queryoutput.tsv**.

Här följer innehållet i exemplet på **\_ Hive- \_ \_ antal \_ med \_ Medallion. HQL** -filen för granskning.

```hiveql
SELECT medallion, COUNT(*) as med_count
FROM nyctaxidb.fare
WHERE month<=3
GROUP BY medallion
HAVING med_count > 100
ORDER BY med_count desc;
```

Kör följande kommando från Hive-katalogens prompt:

```console
hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv
```

### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Undersökning: rese distribution av Medallion och hacka licens
> [!NOTE]
> Den här uppgiften är normalt för en data expert.
> 
> 

När du utforskar en data uppsättning vill vi ofta undersöka distributionerna av grupper med värden. Det här avsnittet innehåller ett exempel på hur du utför den här analysen för hytter och driv rutiner.

**Exempel på \_ registrerings data för Hive \_ \_ \_ per \_ Medallion \_ License. HQL** -fil grupperar pris data uppsättningen på **Medallion** och **hack_license** och returnerar antalet av varje kombination. Här följer dess innehåll:

```hiveql
SELECT medallion, hack_license, COUNT(*) as trip_count
FROM nyctaxidb.fare
WHERE month=1
GROUP BY medallion, hack_license
HAVING trip_count > 100
ORDER BY trip_count desc;
```

Den här frågan returnerar kombinationer av CAB och driv rutiner, sorterade efter fallande antal resor.

Kör följande från Hive-katalogens prompt:

```console
hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv
```

Frågeresultatet skrivs till en lokal fil, **C:\temp\queryoutput.tsv**.

### <a name="exploration-assessing-data-quality-by-checking-for-invalid-longitude-or-latitude-records"></a>Undersökning: utvärdera data kvalitet genom att söka efter ogiltiga longitud-eller Latitude-poster
> [!NOTE]
> Detta är vanligt vis en data expert-uppgift.
> 
> 

Ett gemensamt syfte att analysera undersöknings data är att ta bort felaktiga eller dåliga poster. Exemplet i det här avsnittet avgör om fälten longitud eller latitud innehåller ett värde som ligger utanför området NYC. Eftersom det är troligt att sådana poster har ett felaktigt longitud-latitud-värde vill vi ta bort dem från data som ska användas för modellering.

Här är innehållet i **exempel filen \_ Hive \_ Quality \_ Assessment. HQL** för granskning.

```hiveql
    SELECT COUNT(*) FROM nyctaxidb.trip
    WHERE month=1
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND -30
    OR    CAST(pickup_latitude AS float) NOT BETWEEN 30 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND -30
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN 30 AND 90);
```

Kör följande från Hive-katalogens prompt:

```console
hive -S -f "C:\temp\sample_hive_quality_assessment.hql"
```

Argumentet *-S-* argumentet som ingår i det här kommandot undertrycker status raster utskriften av Hive-avbildningen/minska jobben. Det här kommandot är användbart eftersom det gör att skärmen skrivs ut i Hive-frågans utdata kan läsas.

### <a name="exploration-binary-class-distributions-of-trip-tips"></a>Undersökning: binära klass distributioner av rese tips
> [!NOTE]
> Detta är vanligt vis en data expert-uppgift.
> 
> 

För det binära klassificerings problemet som beskrivs i avsnittet [exempel på förutsägelse aktiviteter](hive-walkthrough.md#mltasks) , är det användbart att veta om ett tips har angetts eller inte. Den här fördelningen av tips är binär:

* Tips angivet (klass 1, Tip- \_ belopp > $0)  
* inget tips (klass 0, Tip- \_ belopp = $0)

Följande **exempel på \_ den \_ lutade filen \_ frekvenss. HQL** visar det kommando som ska köras:

```hiveql
SELECT tipped, COUNT(*) AS tip_freq
FROM
(
    SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
    FROM nyctaxidb.fare
)tc
GROUP BY tipped;
```

Kör följande från Hive-katalogens prompt:

```console
hive -f "C:\temp\sample_hive_tipped_frequencies.hql"
```


### <a name="exploration-class-distributions-in-the-multiclass-setting"></a>Undersökning: klass distributioner i inställningen multiklass
> [!NOTE]
> Detta är vanligt vis en data expert-uppgift.
> 
> 

För klassificerings problemet med flera klasser som beskrivs i avsnittet [exempel på förutsägelse aktiviteter](hive-walkthrough.md#mltasks) , lånar den här data uppsättningen också sig själv till en naturlig klassificering för att förutsäga hur mycket av tipsen som ges. Vi kan använda lager platser för att definiera Tip-intervall i frågan. Om du vill hämta klass distributioner för de olika Tip-intervallen använder du **exempel filen \_ \_ \_ \_ frekvenser för Hive-tipset HQL** . Här är dess innehåll.

```hiveql
SELECT tip_class, COUNT(*) AS tip_freq
FROM
(
    SELECT if(tip_amount=0, 0,
        if(tip_amount>0 and tip_amount<=5, 1,
        if(tip_amount>5 and tip_amount<=10, 2,
        if(tip_amount>10 and tip_amount<=20, 3, 4)))) as tip_class, tip_amount
    FROM nyctaxidb.fare
)tc
GROUP BY tip_class;
```

Kör följande kommando från kommando rads konsolen för Hadoop:

```console
hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"
```

### <a name="exploration-compute-the-direct-distance-between-two-longitude-latitude-locations"></a>Utforskning: beräkna det direkta avståndet mellan två longitud-latitud-platser
> [!NOTE]
> Detta är vanligt vis en data expert-uppgift.
> 
> 

Du kanske vill veta om det finns en skillnad mellan två platser och det faktiska rese avståndet för taxin. En passagerare kan vara mindre sannolik för att få tips om att driv rutinen har avsiktligt tagit dem genom en längre väg.

Om du vill se jämförelsen mellan det faktiska rese avståndet och [Haversine avståndet](https://en.wikipedia.org/wiki/Haversine_formula) mellan två longitud-latitud-platser ("fantastiska cirkel"-avståndet) kan du använda de trigonometriska funktionerna i Hive:

```hiveql
set R=3959;
set pi=radians(180);

insert overwrite directory 'wasb:///queryoutputdir'

select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
 *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
 *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
 /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
 +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
 pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
from nyctaxidb.trip
where month=1
and pickup_longitude between -90 and -30
and pickup_latitude between 30 and 90
and dropoff_longitude between -90 and -30
and dropoff_latitude between 30 and 90;
```

I föregående fråga är R radien för jorden i miles och PI konverteras till radianer. Punkterna longitud-latitud filtreras för att ta bort värden som är långt från NYC-ytan.

I det här fallet skriver vi resultaten till en katalog med namnet **queryoutputdir**. Sekvensen av följande kommandon skapar först denna utdataparameter och kör sedan Hive-kommandot.

Kör följande från Hive-katalogens prompt:

```hiveql
hdfs dfs -mkdir wasb:///queryoutputdir

hive -f "C:\temp\sample_hive_trip_direct_distance.hql"
```

Frågeresultatet skrivs till nio Azure-blobbar (**queryoutputdir/000000 \_ 0** till  **queryoutputdir/000008 \_ 0**) under standard behållaren för Hadoop-klustret.

Om du vill se storleken på de enskilda Blobbarna kör du följande kommando från Hive-katalogens kommando tolk:

```hiveql
hdfs dfs -ls wasb:///queryoutputdir
```

Om du vill se innehållet i en specifik fil, t. ex. **000000 \_ 0**, använder du Hadoop- `copyToLocal` kommandot.

```hiveql
hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile
```

> [!WARNING]
> `copyToLocal` kan vara mycket långsamt för stora filer och rekommenderas inte för användning med dem.  
> 
> 

En viktig fördel med att ha dessa data finns i en Azure-Blob är att vi kan utforska data i Machine Learning med hjälp av modulen [Importera data][import-data] .

## <a name="down-sample-data-and-build-models-in-machine-learning"></a><a name="#downsample"></a>Nedåt – sampla data och bygg modeller i Machine Learning
> [!NOTE]
> Detta är vanligt vis en data expert-uppgift.
> 
> 

Efter analys fasen för analys av undersöknings data är vi nu redo att stänga av data för att skapa modeller i Machine Learning. I det här avsnittet visar vi hur du använder en Hive-fråga för att visa exempel på data. Machine Learning öppnas sedan från modulen [Importera data][import-data] .

### <a name="down-sampling-the-data"></a>Nedåt – sampling av data
Det finns två steg i den här proceduren. Först ansluter vi tabellerna **nyctaxidb. rese** och **nyctaxidb. pris** på tre nycklar som finns i alla poster: **Medallion**, **Hack \_ License** och **upphämtnings \_ datum**. Sedan genererar vi en binära klassificerings etikett, **lutad** och en klassificerings etikett för multiklass, **Tip- \_ klass**.

Om du vill kunna använda de nedsamplade data direkt från modulen [Importera data][import-data] i Machine Learning bör du lagra resultatet från föregående fråga till en intern Hive-tabell. I det här exemplet skapar vi en intern Hive-tabell och fyller i dess innehåll med de kopplade och indata.

Frågan använder vanliga Hive-funktioner direkt för att generera följande tids parametrar från fältet **upphämtnings \_ datum/** tid:
- timme på dagen
- vecka på år
- veckodag (1 står för måndag och 7 står för söndag)

Frågan genererar också det direkta avståndet mellan plocknings-och DropOff-platserna. En fullständig lista över sådana funktioner finns i [LANGUAGEMANUAL UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF).

Frågan sedan nedåt – samplar data så att frågeresultaten får plats i Azure Machine Learning Studio. Bara cirka 1 procent av den ursprungliga data uppsättningen importeras till Studio.

Här följer innehållet i **exempel \_ registrerings data filen \_ Förbered \_ for \_ AML \_ full. HQL** som förbereder data för modell utveckling i Machine Learning:

```hiveql
set R = 3959;
set pi=radians(180);

create table if not exists nyctaxidb.nyctaxi_downsampled_dataset (

medallion string,
hack_license string,
vendor_id string,
rate_code string,
store_and_fwd_flag string,
pickup_datetime string,
dropoff_datetime string,
pickup_hour string,
pickup_week string,
weekday string,
passenger_count int,
trip_time_in_secs double,
trip_distance double,
pickup_longitude double,
pickup_latitude double,
dropoff_longitude double,
dropoff_latitude double,
direct_distance double,
payment_type string,
fare_amount double,
surcharge double,
mta_tax double,
tip_amount double,
tolls_amount double,
total_amount double,
tipped string,
tip_class string
)
row format delimited fields terminated by ','
lines terminated by '\n'
stored as textfile;

--- now insert contents of the join into the above internal table

insert overwrite table nyctaxidb.nyctaxi_downsampled_dataset
select
t.medallion,
t.hack_license,
t.vendor_id,
t.rate_code,
t.store_and_fwd_flag,
t.pickup_datetime,
t.dropoff_datetime,
hour(t.pickup_datetime) as pickup_hour,
weekofyear(t.pickup_datetime) as pickup_week,
from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
t.passenger_count,
t.trip_time_in_secs,
t.trip_distance,
t.pickup_longitude,
t.pickup_latitude,
t.dropoff_longitude,
t.dropoff_latitude,
t.direct_distance,
f.payment_type,
f.fare_amount,
f.surcharge,
f.mta_tax,
f.tip_amount,
f.tolls_amount,
f.total_amount,
if(tip_amount>0,1,0) as tipped,
if(tip_amount=0,0,
if(tip_amount>0 and tip_amount<=5,1,
if(tip_amount>5 and tip_amount<=10,2,
if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class

from
(
select
medallion,
hack_license,
vendor_id,
rate_code,
store_and_fwd_flag,
pickup_datetime,
dropoff_datetime,
passenger_count,
trip_time_in_secs,
trip_distance,
pickup_longitude,
pickup_latitude,
dropoff_longitude,
dropoff_latitude,
${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
*${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
/sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
+cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance,
rand() as sample_key

from nyctaxidb.trip
where pickup_latitude between 30 and 90
    and pickup_longitude between -90 and -30
    and dropoff_latitude between 30 and 90
    and dropoff_longitude between -90 and -30
)t
join
(
select
medallion,
hack_license,
vendor_id,
pickup_datetime,
payment_type,
fare_amount,
surcharge,
mta_tax,
tip_amount,
tolls_amount,
total_amount
from nyctaxidb.fare
)f
on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
where t.sample_key<=0.01
```

Så här kör du den här frågan från Hive-katalogen:

```console
hive -f "C:\temp\sample_hive_prepare_for_aml_full.hql"
```

Nu har vi en intern tabell **nyctaxidb.nyctaxi_downsampled_dataset** som kan nås med hjälp av modulen [importera data][import-data] från Machine Learning. Dessutom kan vi använda den här data uppsättningen för att skapa Machine Learning modeller.  

### <a name="use-the-import-data-module-in-machine-learning-to-access-the-down-sampled-data"></a>Använd modulen importera data i Machine Learning för att få åtkomst till de nedsamplade data
Om du vill utfärda Hive-frågor i modulen [Importera data][import-data] i Machine Learning måste du ha åtkomst till en Machine Learning arbets yta. Du måste också ha åtkomst till klustrets autentiseringsuppgifter och det associerade lagrings kontot.

Här följer information om modulen [Importera data][import-data] och parametrarna för indata:

**HCatalog Server-URI**: om kluster namnet är **vi abc123** använder du: https: \/ /abc123.azurehdinsight.net.

**Hadoop-användar konto namn**: det användar namn som valts för klustret (inte användar namnet för fjärråtkomst).

**Konto lösen ord för Hadoop-användare**: lösen ordet som valts för klustret (inte lösen ordet för fjärråtkomst).

**Plats för utgående data**: väljs som Azure.

**Azure Storage konto namn**: namnet på det standard lagrings konto som är associerat med klustret.

**Namn på Azure-behållare**: standard behållar namnet för klustret och är vanligt vis samma som kluster namnet. För ett kluster med namnet **vi abc123** är namnet vi abc123.

> [!IMPORTANT]
> Alla tabeller som vi vill fråga med hjälp av modulen [Importera data][import-data] i Machine Learning måste vara en intern tabell.
> 
> 

Så här avgör du om en tabell **T** i en databas **D. db** är en intern tabell. Kör följande kommando från Hive-katalogens prompt:

```hiveql
hdfs dfs -ls wasb:///D.db/T
```

Om tabellen är en intern tabell och den är ifylld måste dess innehåll visas här.

Ett annat sätt att avgöra om en tabell är en intern tabell är att använda Azure Storage Explorer. Använd den för att navigera till standard behållar namnet för klustret och filtrera sedan efter tabell namnet. Om tabellen och dess innehåll visas, bekräftar detta att det är en intern tabell.

Här är en skärm bild av Hive-frågan och modulen [Importera data][import-data] :

![Skärm bild av Hive-fråga för modulen importera data](./media/hive-walkthrough/1eTYf52.png)

Eftersom våra insamplade data finns i standard behållaren är den resulterande Hive-frågan från Machine Learning enkel. Det är bara ett **Select *-nyctaxidb. nyctaxi \_ nedsamplade \_ data**.

Data uppsättningen kan nu användas som start punkt för att skapa Machine Learning modeller.

### <a name="build-models-in-machine-learning"></a><a name="mlmodel"></a>Bygg modeller i Machine Learning
Nu kan du fortsätta med modell skapande och modell distribution i [Machine Learning](https://studio.azureml.net). Data är redo för oss att använda för att hantera de förutsägelse problem som identifierats tidigare:

- **Binära klassificering**: för att förutsäga om ett tips har betalats för en resa.

  **Elev som används:** Logistik regression med två klasser

  a. För det här problemet **lutas** mål-eller klass etiketten. Den ursprungliga data uppsättningen i det här exemplet har några kolumner som är mål läckor för det här klassificerings experimentet. I synnerhet **Tip- \_ klass**, **Tip- \_ belopp** och **total \_ mängd** för att visa information om mål etiketten som inte är tillgänglig vid test tillfället. Vi tar bort dessa kolumner från överväganden med hjälp av modulen [Välj kolumner i data uppsättning][select-columns] .

  I följande diagram visas vårt experiment för att förutse om ett tips har betalats för en bestämd resa:

  ![Diagram över experiment för att förutse om Tip betalades](./media/hive-walkthrough/QGxRz5A.png)

  b. I det här experimentet var våra mål etikett fördelningar ungefär 1:1.

   Följande diagram visar fördelningen av tips klass etiketter för problem med den binära klassificeringen:

  ![Diagram över fördelningen av tips klass etiketter](./media/hive-walkthrough/9mM4jlD.png)

    Därför får vi ett utrymme under kurvan (AUC) av 0,987, som du ser i följande figur:

  ![Diagram över AUC-värde](./media/hive-walkthrough/8JDT0F8.png)

- **Klassificering** av flera klasser: för att förutsäga antalet Tip-mängder som betalas för resan, genom att använda de tidigare definierade klasserna.

  **Elev som används:** Logistik regression med multiklass

  a. För det här problemet är vår mål sättnings etikett (eller klass) en **Tip- \_ klass**, som kan ha ett av fem värden (0, 1, 2, 3, 4). Som i det binära klassificerings fallet har vi några kolumner som är mål läckor för det här experimentet. I synnerhet överliggande, **Tip \_** **-** belopp och **total \_ mängd** visar information om mål etiketten som inte är tillgänglig vid testnings tillfället. Vi tar bort de här kolumnerna med hjälp av modulen [Välj kolumner i data uppsättning][select-columns] .

  I följande diagram visas experimentet för att förutsäga på vilken lager plats ett tips sannolikt kommer att falla. Lager platserna är: klass 0: tips = $0, klass 1: tips > $0 och tips <= $5, klass 2: tips > $5 och tips <= $10, klass 3: Tip > $10 och Tip <= $20 och klass 4: Tip > $20.

  ![Diagram över experiment till förutse binge för tips](./media/hive-walkthrough/5ztv0n0.png)

  Nu visar vi hur den faktiska test klass fördelningen ser ut. Klass 0 och klass 1 är vanliga och de andra klasserna är ovanliga.

  ![Diagram över distribution av test klass](./media/hive-walkthrough/Vy1FUKa.png)

  b. För det här experimentet använder vi en Förväxlings mat ris för att titta på de förutsägelser som visas här:

  ![Förvirringsmatris](./media/hive-walkthrough/cxFmErM.png)

  Även om klassen visar att de vanligaste klasserna är effektiva, gör modellen inte något lämpligt jobb av "inlärning" i rarer-klasserna.

- **Regressions uppgift**: för att förutsäga hur mycket tips du betalar för en resa.

  **Elev som används:** Utökat besluts träd

  a. För det här problemet är mål etiketten (eller klassen) **Tip- \_ storlek**. Mål läckor i det här fallet är: **nedlutad**, **Tip- \_ klass** och **total \_ mängd**. Alla dessa variabler visar information om det tips belopp som normalt inte är tillgängligt vid test tillfället. Vi tar bort de här kolumnerna med hjälp av modulen [Välj kolumner i data uppsättning][select-columns] .

  I följande diagram visas experimentet för att förutsäga det aktuella tipset:

  ![Diagram över experiment för att förutsäga mängden tips](./media/hive-walkthrough/11TZWgV.png)

  b. För Regressions problem mäter vi resultatet av förutsägelsen genom att titta på fel meddelandet i förutsägelserna och koefficienten för bestämningen:

  ![Skärm bild av förutsägelse statistik](./media/hive-walkthrough/Jat9mrz.png)

  Här är koefficienten för bestämningen 0,709, vilket innebär att cirka 71 procent av avvikelsen förklaras av modellens koefficienter.

> [!IMPORTANT]
> Mer information om Machine Learning och hur du kommer åt och använder den finns i [Machine Learning](../classic/index.yml). Dessutom täcker [Azure AI Gallery](https://gallery.cortanaintelligence.com/) en omfattning av experiment och ger en grundlig introduktion till de olika funktionerna i Machine Learning.
> 
> 

## <a name="license-information"></a>Licens information
Den här exempel genom gången och dess tillhör ande skript delas av Microsoft under MIT-licensen. Mer information finns i **LICENSE.txt** -filen i katalogen i exempel koden på GitHub.

## <a name="references"></a>Referenser
•    [Andrés MONROY NYC taxi TRIPs Download Page](https://www.andresmh.com/nyctaxitrips/)  
•    [Folier av NYCs taxi uppgifter från Christer Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)   
•    [NYC taxi och limousine kommissionens forskning och statistik](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[2]: ./media/hive-walkthrough/output-hive-results-3.png
[11]: ./media/hive-walkthrough/hive-reader-properties.png
[12]: ./media/hive-walkthrough/binary-classification-training.png
[13]: ./media/hive-walkthrough/create-scoring-experiment.png
[14]: ./media/hive-walkthrough/binary-classification-scoring.png
[15]: ./media/hive-walkthrough/amlreader.png

<!-- Module References -->
[select-columns]: /azure/machine-learning/studio-module-reference/select-columns-in-dataset
[import-data]: /azure/machine-learning/studio-module-reference/import-data