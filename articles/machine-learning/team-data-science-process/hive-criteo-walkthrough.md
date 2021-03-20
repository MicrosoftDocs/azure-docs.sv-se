---
title: Använd Azure HDInsight Hadoop kluster på 1 – TB data uppsättning – team data science process
description: Med hjälp av team data science-processen för ett scenario från slut punkt till slut punkt som använder ett HDInsight Hadoop-kluster för att bygga och distribuera en modell med hjälp av en stor (1 TB) offentligt tillgänglig data uppsättning
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
ms.openlocfilehash: e66bd0a4e56f63185d8361355d6cf8e0e29bc30b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "93305944"
---
# <a name="the-team-data-science-process-in-action---using-an-azure-hdinsight-hadoop-cluster-on-a-1-tb-dataset"></a>Team data science-processen i praktiken – använda ett Azure HDInsight Hadoop-kluster på en data uppsättning på 1 TB

Den här genom gången visar hur du använder team data science-processen i ett scenario med ett slut punkt till slut punkt med ett [Azure HDInsight Hadoop-kluster](https://azure.microsoft.com/services/hdinsight/) för att lagra, utforska, funktions tekniker och nedåt exempel data från någon av de offentligt tillgängliga [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) -datauppsättningarna. Den använder Azure Machine Learning för att bygga en binär klassificerings modell för dessa data. Den visar också hur du publicerar en av dessa modeller som en webb tjänst.

Det är också möjligt att använda en IPython Notebook för att utföra de uppgifter som presenteras i den här genom gången. Användare som vill prova den här metoden bör läsa igenom [Criteo genom att använda en HIVE ODBC-anslutning](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) .

## <a name="criteo-dataset-description"></a><a name="dataset"></a>Beskrivning av Criteo-datauppsättning
Criteo-data är en förutsägelse data uppsättning som är 370 GB gzip-komprimerade TSV filer (~ 1,3 TB okomprimerad), som består av mer än 4 300 000 000 poster. Det tas från 24 dagar från klicknings data som gjorts tillgängliga av [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/). För att få till gång till data experter är de data som är tillgängliga för oss att experimentera med vara zippade.

Varje post i den här data uppsättningen innehåller 40 kolumner:

* den första kolumnen är en etikett kolumn som visar om en användare klickar på ett **Lägg till** (värde 1) eller inte klickar på ett (värde 0)
* efterföljande 13 kolumner är numeriska och
* de senaste 26 är kategoriska kolumner

Kolumnerna är anonymiserats och använder en serie med uppräknade namn: "Col1" (för kolumnen Label) till "Col40" (för den sista kategoriska-kolumnen).

Här är ett utdrag av de första 20 kolumnerna i två observationer (rader) från den här data uppsättningen:

> `Col1    Col2    Col3    Col4    Col5    Col6    Col7    Col8    Col9    Col10    Col11    Col12    Col13    Col14    Col15            Col16            Col17            Col18            Col19        Col20`
>
> `0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb`
>
> `0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb`

Det finns saknade värden i båda kolumnerna numeric och kategoriska i den här data uppsättningen. En enkel metod för att hantera de saknade värdena beskrivs. Ytterligare information om data visas när du lagrar dem i Hive-tabeller.

**Definition:** *klicknings frekvens (/maskin):* det här måttet är procent andelen klickningar i data. I den här Criteo-datauppsättningen är/maskin cirka 3,3% eller 0,033.

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>Exempel på förutsägelse aktiviteter
I den här genom gången är två exempel på förutsägelse problem:

1. **Binära klassificering**: förutsäger om en användare har klickat på Lägg till:

   * Klass 0: inget klick
   * Klass 1: Klicka på
2. **Regression**: förutsäger sannolikheten för att en annons klickar på användar funktioner.

## <a name="set-up-an-hdinsight-hadoop-cluster-for-data-science"></a><a name="setup"></a>Konfigurera ett HDInsight Hadoop-kluster för data vetenskap
> [!NOTE]
> Det här steget är vanligt vis en **Administratörs** uppgift.

Konfigurera din Azure Data Science-miljö för att skapa förutsägelse analys lösningar med HDInsight-kluster i tre steg:

1. [Skapa ett lagrings konto](../../storage/common/storage-account-create.md): det här lagrings kontot används för att lagra data i Azure Blob Storage. Data som används i HDInsight-kluster lagras här.
2. [Anpassa Azure HDInsight Hadoop kluster för data vetenskap](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md): det här steget skapar ett Azure HDInsight Hadoop-kluster med 64-bitars Anaconda python 2,7 installerat på alla noder. Det finns två viktiga steg (beskrivs i det här avsnittet) att slutföra när du anpassar HDInsight-klustret.

   * Länka lagrings kontot som skapades i steg 1 med ditt HDInsight-kluster när det skapas. Det här lagrings kontot används för att komma åt data som kan bearbetas i klustret.
   * Aktivera fjärråtkomst till Head-noden i klustret när den har skapats. Kom ihåg de autentiseringsuppgifter för fjärråtkomst som du anger här (skiljer sig från de autentiseringsuppgifter som angavs när klustret skapas): utför följande procedurer.
3. [Skapa en Azure Machine Learning Studio-arbetsyta (klassisk)](../classic/create-workspace.md): den här Azure Machine Learning arbets ytan används för att skapa maskin inlärnings modeller efter en inledande data utforskning och inaktive insampling i HDInsight-klustret.

## <a name="get-and-consume-data-from-a-public-source"></a><a name="getdata"></a>Hämta och använda data från en offentlig källa
[Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) -datauppsättningen kan nås genom att klicka på länken, godkänna användnings villkoren och ange ett namn. Här visas en ögonblicks bild:

![Godkänn Criteo-villkor](./media/hive-criteo-walkthrough/hLxfI2E.png)

Klicka på **Fortsätt för att ladda ned** för att läsa mer om data uppsättningen och dess tillgänglighet.

Data finns på en [Azure Blob Storage](../../storage/blobs/storage-quickstart-blobs-dotnet.md) -plats: wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/ . "Wasb" syftar på Azure Blob Storage-platsen.

1. Data i Azure Blob Storage består av tre undermappar med zippade data.

   1. Undermappen *RAW/Count/* innehåller de första 21 dagarna data – från dag \_ 00 till dag \_ 20
   2. Undermappen *RAW/Train/* består av en enda dag med data, dag \_ 21
   3. Undermappen *RAW/test/* består av två dagars data, dag \_ 22 och dag \_ 23
2. Rå gzip-data är också tillgängliga i huvudmappen *RAW/* som day_NN. gz, där NN går från 00 till 23.

En annan metod för att komma åt, utforska och modellera dessa data som inte kräver lokala hämtningar beskrivs senare i den här genom gången när vi skapar Hive-tabeller.

## <a name="log-in-to-the-cluster-headnode"></a><a name="login"></a>Logga in på klustrets huvudnoden
Logga in på huvudnoden i klustret genom att använda [Azure Portal](https://ms.portal.azure.com) för att hitta klustret. Klicka på HDInsight elefant-ikonen till vänster och dubbelklicka sedan på namnet på klustret. Gå till fliken **konfiguration** , dubbelklicka på ikonen Anslut längst ned på sidan och ange autentiseringsuppgifter för fjärråtkomst när du uppmanas att ta dig till huvudnoden i klustret.

Så här ser en typisk första inloggning till klustrets huvudnoden ut:

![Logga in på kluster](./media/hive-criteo-walkthrough/Yys9Vvm.png)

Till vänster visas "Hadoop kommando rad", som är vår WorkHorse för data utforskningen. Observera två användbara URL-adresser – "Hadoop-Garnets status" och "Hadoop Name Node". URL: en för garn status visar jobb förlopp och URL: en för noden namn innehåller information om kluster konfigurationen.

Nu är du redo att börja första delen av genom gången: data utforskning med Hive och hämtning av data som är redo för Azure Machine Learning.

## <a name="create-hive-database-and-tables"></a><a name="hive-db-tables"></a> Skapa Hive-databas och tabeller
Om du vill skapa Hive-tabeller för vår Criteo-datauppsättning öppnar du ***kommando raden för Hadoop*** på Skriv bordet för Head-noden och anger Hive-katalogen genom att ange kommandot

```console
cd %hive_home%\bin
```

> [!NOTE]
> Kör alla Hive-kommandon i den här genom gången från registrerings data filen för Hive bin/katalog. Detta tar hand om eventuella Sök vägs problem automatiskt. Du kan använda termerna "Hive-katalog fråga", "Hive-bin/katalog fråga" och "Hadoop-kommandoraden".
>
> [!NOTE]
> Om du vill köra en Hive-fråga kan du alltid använda följande kommandon:
>
> `cd %hive_home%\bin`
> `hive`

När Hive-REPL visas med ett "Hive >"-tecken kan du helt enkelt klippa ut och klistra in frågan för att köra den.

Följande kod skapar en databas "Criteo" och genererar sedan fyra tabeller:

* en *tabell för generering av antal* som skapats på dagar \_ till dag \_ 20,
* en *tabell som används som tågets data uppsättning* byggd dag \_ 21 och
* två *tabeller som ska användas som test data uppsättningar* byggda dag \_ 22 respektive dag \_ 23.

Dela upp test data uppsättningen i två olika tabeller eftersom en av dagarna är en helgdag. Målet är att avgöra om modellen kan identifiera skillnader mellan en helgdag och en annan än en helgdag från klicknings takten.

Skript [exemplet&#95;hive&#95;skapa&#95;criteo&#95;database&#95;och&#95;tables. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql) visas här för enkelhetens skull:

```hiveql
CREATE DATABASE IF NOT EXISTS criteo;
DROP TABLE IF EXISTS criteo.criteo_count;
CREATE TABLE criteo.criteo_count (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/count';

DROP TABLE IF EXISTS criteo.criteo_train;
CREATE TABLE criteo.criteo_train (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/train';

DROP TABLE IF EXISTS criteo.criteo_test_day_22;
CREATE TABLE criteo.criteo_test_day_22 (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_22';

DROP TABLE IF EXISTS criteo.criteo_test_day_23;
CREATE TABLE criteo.criteo_test_day_23 (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_23';
```

Alla dessa tabeller är externa så att du kan peka på deras Azure Blob Storage-platser (wasb).

**Det finns två sätt att köra en Hive-fråga:**

* **Med hjälp av kommando raden för Hive-repl**: det första är att utfärda ett Hive-kommando och kopiera och klistra in en fråga på HIVE-repl kommando rad:

  ```console
  cd %hive_home%\bin
  hive
  ```

     Nu körs den här frågan på kommando raden för REPL och inklistringen klistras in.
* **Spara frågor till en fil och köra kommandot**: det andra är att spara frågorna till en. HQL-fil ([exempel&#95;hive&#95;skapa&#95;criteo&#95;database&#95;och&#95;tables. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)) och utfärda sedan följande kommando för att köra frågan:

  ```console
  hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql
  ```

### <a name="confirm-database-and-table-creation"></a>Bekräfta skapande av databas och tabell
Sedan bekräftar du skapandet av databasen med följande kommando från registrerings data filen för Hive-bin/katalogen:

```console
hive -e "show databases;"
```

Detta ger:

```output
criteo
default
Time taken: 1.25 seconds, Fetched: 2 row(s)
```

Detta bekräftar skapandet av den nya databasen, "Criteo".

Om du vill se vilka tabeller som har skapats kan du bara utfärda kommandot här från registrerings data filen för Hive-bin/-katalogen:

```output
hive -e "show tables in criteo;"
```

Du bör sedan se följande utdata:

```ouput
criteo_count
criteo_test_day_22
criteo_test_day_23
criteo_train
Time taken: 1.437 seconds, Fetched: 4 row(s)
```

## <a name="data-exploration-in-hive"></a><a name="exploration"></a> Data utforskning i Hive
Nu är du redo att utföra vissa grundläggande data utforskningar i Hive. Du börjar med att räkna antalet exempel i tabellerna träna och testa data.

### <a name="number-of-train-examples"></a>Antal träna-exempel
Innehållet i [exemplet&#95;hive&#95;räkna&#95;träna&#95;tabell&#95;exempel. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql) visas här:

```hiveql
SELECT COUNT(*) FROM criteo.criteo_train;
```

Detta ger:

```output
192215183
Time taken: 264.154 seconds, Fetched: 1 row(s)
```

Ett annat kan också utfärda följande kommando från registrerings data filen för Hive-bin/-katalogen:

```console
hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql
```

### <a name="number-of-test-examples-in-the-two-test-datasets"></a>Antal test exempel i de två test data uppsättningarna
Räkna nu antalet exempel i de två test data uppsättningarna. Innehållet i [exemplet&#95;hive&#95;count&#95;criteo&#95;test&#95;dag&#95;22&#95;table&#95;exempel. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql) finns här:

```hiveql
SELECT COUNT(*) FROM criteo.criteo_test_day_22;
```

Detta ger:

```output
189747893
Time taken: 267.968 seconds, Fetched: 1 row(s)
```

Som vanligt kan du också anropa skriptet från registrerings data filen för Hive-bin/katalog genom att utfärda kommandot:

```console
hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql
```

Slutligen undersöker du antalet test exempel i test data uppsättningen baserat på dag \_ 23.

Kommandot för att göra detta liknar det som visas (se [exempel&#95;hive&#95;count&#95;criteo&#95;test&#95;dag&#95;23&#95;exempel. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql)):

```hiveql
SELECT COUNT(*) FROM criteo.criteo_test_day_23;
```

Detta ger:

```output
178274637
Time taken: 253.089 seconds, Fetched: 1 row(s)
```

### <a name="label-distribution-in-the-train-dataset"></a>Etikett fördelning i tåg data uppsättningen
Etikett fördelningen i tågets data uppsättning är av intresse. För att se detta, Visa innehåll i [exempel&#95;hive&#95;criteo&#95;etikett&#95;distribution&#95;träna&#95;Table. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql):

```hiveql
SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;
```

Detta ger etiketten distribution:

```output
1       6292903
0       185922280
Time taken: 459.435 seconds, Fetched: 2 row(s)
```

Procent andelen positiva etiketter är cirka 3,3% (konsekvent med den ursprungliga data uppsättningen).

### <a name="histogram-distributions-of-some-numeric-variables-in-the-train-dataset"></a>Histogram distributioner av vissa numeriska variabler i tågets data uppsättning
Du kan använda Hive: s inbyggda "histogram"- \_ funktion för att ta reda på hur den här fördelningen av numeriska variabler ser ut. Här är innehållet i [exemplet&#95;hive&#95;criteo&#95;histogram&#95;numeriska. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql):

```hiveql
SELECT CAST(hist.x as int) as bin_center, CAST(hist.y as bigint) as bin_height FROM
    (SELECT
    histogram_numeric(col2, 20) as col2_hist
    FROM
    criteo.criteo_train
    ) a
    LATERAL VIEW explode(col2_hist) exploded_table as hist;
```

Detta ger följande:

```output
26      155878415
2606    92753
6755    22086
11202   6922
14432   4163
17815   2488
21072   1901
24113   1283
27429   1225
30818   906
34512   723
38026   387
41007   290
43417   312
45797   571
49819   428
53505   328
56853   527
61004   160
65510   3446
Time taken: 317.851 seconds, Fetched: 20 row(s)
```

Kombinationen av LATERAL vy-explodera i Hive används för att skapa en SQL-liknande utdata i stället för den vanliga listan. Den första kolumnen i den här tabellen motsvarar lager plats Center och den andra till lager plats frekvensen.

### <a name="approximate-percentiles-of-some-numeric-variables-in-the-train-dataset"></a>Ungefärliga percentiler av vissa numeriska variabler i tågets data uppsättning
Även om det är intressant med numeriska variabler är beräkningen av ungefärliga percentiler. Hive: s interna "percentils \_ uppskattat" gör detta för oss. Innehållet i [exemplet&#95;hive&#95;criteo&#95;ungefärligt&#95;percentiler. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql) :

```hiveql
SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;
```

Detta ger:

```output
1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
Time taken: 564.953 seconds, Fetched: 1 row(s)
```

Fördelningen av percentiler är nära kopplad till histogrammets distribution av en numerisk variabel vanligt vis.

### <a name="find-number-of-unique-values-for-some-categorical-columns-in-the-train-dataset"></a>Hitta antalet unika värden för vissa kategoriska-kolumner i träna-datauppsättningen
Du kan fortsätta med data utforskningen, hitta, för vissa kategoriska-kolumner, antalet unika värden som de tar. Det gör du genom att visa innehåll i [exempel&#95;hive&#95;criteo&#95;unika&#95;värden&#95;categoricals. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql):

```hiveql
SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;
```

Detta ger:

```output
19011825
Time taken: 448.116 seconds, Fetched: 1 row(s)
```

Col15 har 19M unika värden! Det är inte möjligt att använda naïve-tekniker som "en-frekvent kodning" för att koda sådana avancerade kategoriska-variabler. I synnerhet förklaras och visas en kraftfull, robust teknik som kallas [inlärning](/archive/blogs/machinelearning/big-learning-made-easy-with-counts) för att lösa problemet effektivt.

Titta slutligen på antalet unika värden för några andra kategoriska-kolumner. Innehållet i [exemplet&#95;hive&#95;criteo&#95;unika&#95;värden&#95;flera&#95;categoricals. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql) :

```hiveql
SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)),
COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
FROM criteo.criteo_train;
```

Detta ger:

```output
30935   15200   7349    20067   3
Time taken: 1933.883 seconds, Fetched: 1 row(s)
```

Observera att förutom för Col20 har alla andra kolumner många unika värden.

### <a name="co-occurrence-counts-of-pairs-of-categorical-variables-in-the-train-dataset"></a>Antal förekomster av par med kategoriska-variabler i tåg data uppsättningen

Count-fördelningen för par av kategoriska-variabler är också av intresse. Detta kan fastställas med hjälp av koden i [exemplet&#95;hive&#95;criteo&#95;kopplade&#95;kategoriska&#95;Counts. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql):

```hiveql
SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;
```

Omvänd ordning efter antalet förekomster och titta på de översta 15 i det här fallet. Detta ger oss följande:

```output
ad98e872        cea68cd3        8964458
ad98e872        3dbb483e        8444762
ad98e872        43ced263        3082503
ad98e872        420acc05        2694489
ad98e872        ac4c5591        2559535
ad98e872        fb1e95da        2227216
ad98e872        8af1edc8        1794955
ad98e872        e56937ee        1643550
ad98e872        d1fade1c        1348719
ad98e872        977b4431        1115528
e5f3fd8d        a15d1051        959252
ad98e872        dd86c04a        872975
349b3fec        a52ef97d        821062
e5f3fd8d        a0aaffa6        792250
265366bf        6f5c7c41        782142
Time taken: 560.22 seconds, Fetched: 15 row(s)
```

## <a name="down-sample-the-datasets-for-azure-machine-learning"></a><a name="downsample"></a> Exempel på data uppsättningar för Azure Machine Learning
Ha utforskat data uppsättningarna och visat hur du utför den här typen av utforskning för variabler (inklusive kombinationer) och nedåt, så att modeller i Azure Machine Learning kan skapas. Kom ihåg att fokus på problemet är: Ange en uppsättning exempel-attribut (funktions värden från Col2-Col40), förutse om Col1 är 0 (inget klick) eller 1 (klicka).

Om du vill se ett exempel på att data uppsättningarna tränas och testas till 1% av den ursprungliga storleken använder du Hive: s inbyggda RAND ()-funktion. Nästa skript, [exempel&#95;hive&#95;criteo&#95;nedsampling&#95;träna&#95;data uppsättning. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql) gör detta för tågets data uppsättning:

```hiveql
CREATE TABLE criteo.criteo_train_downsample_1perc (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE;

---Now downsample and store in this table

INSERT OVERWRITE TABLE criteo.criteo_train_downsample_1perc SELECT * FROM criteo.criteo_train WHERE RAND() <= 0.01;
```

Detta ger:

```output
Time taken: 12.22 seconds
Time taken: 298.98 seconds
```

Skript [exemplet&#95;hive&#95;criteo&#95;nedsampling&#95;test&#95;day&#95;22&#95;data uppsättning. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) gör det till test data, dag \_ 22:

```hiveql
--- Now for test data (day_22)

CREATE TABLE criteo.criteo_test_day_22_downsample_1perc (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE;

INSERT OVERWRITE TABLE criteo.criteo_test_day_22_downsample_1perc SELECT * FROM criteo.criteo_test_day_22 WHERE RAND() <= 0.01;
```

Detta ger:

```output
Time taken: 1.22 seconds
Time taken: 317.66 seconds
```

Slutligen, skript [exemplet&#95;hive&#95;criteo&#95;nedsampling&#95;test&#95;dag&#95;23&#95;data uppsättning. HQL](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) gör det till test data, dag \_ 23:

```hiveql
--- Finally test data day_23
CREATE TABLE criteo.criteo_test_day_23_downsample_1perc (
col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 srical feature; tring)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
LINES TERMINATED BY '\n'
STORED AS TEXTFILE;

INSERT OVERWRITE TABLE criteo.criteo_test_day_23_downsample_1perc SELECT * FROM criteo.criteo_test_day_23 WHERE RAND() <= 0.01;
```

Detta ger:

```output
Time taken: 1.86 seconds
Time taken: 300.02 seconds
```

Med det här alternativet är du redo att använda vårt insamlade tränat tåg och testa data uppsättningar för att skapa modeller i Azure Machine Learning.

Det finns en slutlig viktig komponent innan du går vidare till Azure Machine Learning, vilket avser Count-tabellen. I nästa avsnitt beskrivs tabellen Count i viss detalj.

## <a name="a-brief-discussion-on-the-count-table"></a><a name="count"></a> En kort diskussion om Count-tabellen
Som du såg har flera kategoriska-variabler en hög dimensionalitet. I genom gången är en kraftfull teknik som kallas [inlärning med antal](/archive/blogs/machinelearning/big-learning-made-easy-with-counts) för att koda dessa variabler på ett effektivt och robust sätt presenteras. Mer information om den här tekniken finns i den angivna länken.

>[!NOTE]
>I den här genom gången är fokus på att använda Count-tabeller för att skapa komprimerade representationer av avancerade kategoriska funktioner. Detta är inte det enda sättet att koda kategoriska-funktioner. Om du vill ha mer information om andra tekniker kan intresserade användare titta på [en-frekvent kodning](https://en.wikipedia.org/wiki/One-hot) och [funktion-hash](https://en.wikipedia.org/wiki/Feature_hashing).
>

Om du vill bygga antalet tabeller i Count-data använder du datan i mappen RAW/Count. I avsnittet modellering visas användare hur man skapar dessa Count-tabeller för kategoriska-funktioner från grunden, eller om du vill använda en fördefinierad beräknings tabell för deras utforskningar. I det som följer, när "förskapade antal tabeller" refereras till, betyder vi att du använder antalet tabeller som har angetts. Detaljerade anvisningar om hur du kommer åt dessa tabeller finns i nästa avsnitt.

## <a name="build-a-model-with-azure-machine-learning"></a><a name="aml"></a> Bygg en modell med Azure Machine Learning
Vår modell skapande process i Azure Machine Learning följande steg:

1. [Hämta data från Hive-tabeller till Azure Machine Learning](#step1)
2. [Skapa experimentet: Rensa data och gör dem till en funktion med räkna tabeller](#step2)
3. [Bygg, träna och betygs ätt modellen](#step3)
4. [Utvärdera modellen](#step4)
5. [Publicera modellen som en webb tjänst](#step5)

Nu är du redo att bygga modeller i Azure Machine Learning Studio. Våra exempel data sparas som Hive-tabeller i klustret. Använd modulen Azure Machine Learning **Importera data** för att läsa dessa data. Autentiseringsuppgifterna för att komma åt lagrings kontot för det här klustret tillhandahålls i det som följer.

### <a name="step-1-get-data-from-hive-tables-into-azure-machine-learning-using-the-import-data-module-and-select-it-for-a-machine-learning-experiment"></a><a name="step1"></a> Steg 1: Hämta data från Hive-tabeller till Azure Machine Learning använda modulen importera data och välj den för ett Machine Learning-experiment
Börja med att välja ett **+ nytt**  ->  **experiment**  ->  **tomt experiment**. Sedan söker du efter "Importera data" från **sökrutan längst** upp till vänster. Dra och släpp modulen **Importera data** till arbets ytan för experimentet (den mittersta delen av skärmen) om du vill använda modulen för data åtkomst.

Så här ser **import data** ut som när data hämtas från Hive-tabellen:

![Importera data hämtar data](./media/hive-criteo-walkthrough/i3zRaoj.png)

För modulen **Importera data** är värdena för de parametrar som anges i grafiken bara exempel på den typ av värden som du behöver ange. Här följer några allmänna anvisningar om hur du fyller i parameter uppsättningen för modulen **Importera data** .

1. Välj Hive-fråga för **data källa**
2. I rutan **Hive-databasfrågor** , en enkel Select * från <ditt \_ databas \_ namn. \_ tabell \_ namnet>-är tillräckligt.
3. **HCatalog Server-URI**: om klustret är "ABC" är detta bara: https: \/ /ABC.azurehdinsight.net
4. **Hadoop-användar konto namn**: det användar namn som valdes vid tidpunkten för att ställa in klustret. (Inte användar namn för fjärråtkomst!)
5. **Lösen ord för Hadoop-användarkonto**: lösen ordet för det användar namn som valts vid tiden för klustret. (Inte lösen ordet för fjärråtkomst!)
6. **Plats för utgående data**: Välj "Azure"
7. **Azure Storage konto namn**: det lagrings konto som är kopplat till klustret
8. **Azure Storage konto nyckel**: nyckeln för det lagrings konto som är kopplat till klustret.
9. **Namn på Azure-behållare**: om kluster namnet är "ABC" är detta bara "ABC", vanligt vis.

När data har hämtats av **importen** (du ser det gröna skalet i modulen) sparar du dessa data som en data uppsättning (med ett namn som du väljer). Så här ser det ut:

![Importera data Spara data](./media/hive-criteo-walkthrough/oxM73Np.png)

Högerklicka på utdataporten för modulen **Importera data** . Detta visar alternativet **Spara som data uppsättning** och ett **visualiserings** alternativ. Alternativet **visualisera** , om du klickar på, visar 100 rader med data, tillsammans med en höger panel som är användbar för viss sammanfattnings statistik. Om du vill spara data väljer du **Spara som data uppsättning** och följer instruktionerna.

Om du vill välja den sparade data uppsättningen som ska användas i ett Machine Learning-experiment, letar du upp data uppsättningarna med **hjälp av sökrutan som** visas i följande figur. Ange sedan bara det namn som du gav data uppsättningen delvis för att komma åt den och dra data uppsättningen till huvud panelen. Om du släpper den på huvud panelen väljer du den för användning i Machine Learning-modellering.

![Dra data uppsättningen till huvud panelen](./media/hive-criteo-walkthrough/cl5tpGw.png)

> [!NOTE]
> Gör detta för både tåget och test data uppsättningarna. Kom också ihåg att använda de databas namn och tabell namn som du har gett för detta ändamål. Värdena som används i bilden är endast avsedda som illustration. * *
>
>

### <a name="step-2-create-an-experiment-in-azure-machine-learning-to-predict-clicks--no-clicks"></a><a name="step2"></a> Steg 2: skapa ett experiment i Azure Machine Learning för att förutse klickningar/inga klick
Vårt Azure Machine Learning Studio (klassiska) experiment ser ut så här:

![Machine Learning experiment](./media/hive-criteo-walkthrough/xRpVfrY.png)

Granska nu viktiga komponenter i det här experimentet. Dra våra sparade tåg-och test data uppsättningar till arbets ytan för experimentet först.

#### <a name="clean-missing-data"></a>Rensa data som saknas
Modulen **Rensa data som saknas** gör vad dess namn föreslår: den rensar saknade data på sätt som kan vara användardefinierade. Titta i den här modulen för att se följande:

![Rensa saknade data](./media/hive-criteo-walkthrough/0ycXod6.png)

Här väljer du om du vill ersätta alla saknade värden med 0. Det finns även andra alternativ, som du kan se genom att titta på list rutorna i modulen.

#### <a name="feature-engineering-on-the-data"></a>Funktions teknik för data
Det kan finnas miljon tals unika värden för vissa kategoriska-funktioner i stora data mängder. Det är helt omöjligt att använda naïve-metoder som enkel kodning för att representera sådana avancerade kategoriska-funktioner. Den här genom gången visar hur du använder funktionen Count med inbyggda Azure Machine Learning moduler för att generera kompakta representationer av dessa kategoriska variabler. Slut resultatet är en mindre modell storlek, snabbare inlärnings tider och prestanda mått som är jämförbara med andra tekniker.

##### <a name="building-counting-transforms"></a>Skapa räknings transformeringar
Om du vill ha fler funktioner använder du modulen för att **räkna upp omvandling** som är tillgänglig i Azure Machine Learning. Modulen ser ut så här:

![Bygga upp räknar egenskaper för Transform-modul i ](./media/hive-criteo-walkthrough/e0eqKtZ.png)
 ![ transformering](./media/hive-criteo-walkthrough/OdDN0vw.png)

> [!IMPORTANT]
> I rutan **antal kolumner** anger du de kolumner som du vill utföra antal. Detta är vanligt vis (som nämnt) kategoriska kolumner med hög dimensionellhet. Kom ihåg att Criteo-datauppsättningen har 26 kategoriska kolumner: från Col15 till Col40. Här är antalet på alla dessa och ger sina index (från 15 till 40 avgränsade med kommatecken som visas).
>

Om du vill använda modulen i MapReduce-läget (lämpligt för stora data uppsättningar) måste du ha åtkomst till ett HDInsight Hadoop-kluster (det som används för funktions utforskningen kan också återanvändas för det här syftet) och dess autentiseringsuppgifter. Föregående siffror visar hur de fyllda värdena ser ut (ersätt värdena som anges för illustrationen med de som är relevanta för ditt eget användnings fall).

![Parametrar för modul](./media/hive-criteo-walkthrough/05IqySf.png)

Föregående bild visar hur du anger den angivna BLOB-platsen. Den här platsen har de data som är reserverade för att skapa antal tabeller.

När den här modulen har körts klart sparar du transformeringen för senare genom att högerklicka på modulen och välja alternativet **Spara som omvandling** :

![Alternativet "Spara som transformering"](./media/hive-criteo-walkthrough/IcVgvHR.png)

I vår experiment arkitektur som visas ovan motsvarar data uppsättningen "ytransform2" exakt en transformering av antal sparade mängder. För resten av det här experimentet förutsätts att läsaren använde en transformering för att **skapa en räkning** på vissa data för att generera antal, och kan sedan använda dessa antal för att generera antal funktioner i data uppsättningarna träna och test.

##### <a name="choosing-what-count-features-to-include-as-part-of-the-train-and-test-datasets"></a>Välja vilka antal funktioner som ska inkluderas som en del av data uppsättningarna träna och test
När ett antal omvandlingar är klart kan användaren välja vilka funktioner som ska ingå i sina träna-och test data uppsättningar med modulen **ändra antal tabell parametrar** . För klar ande visas den här modulen här. Men i intresse av enkelhetens skull används den inte i vårt experiment.

![Ändra räkna tabell parametrar](./media/hive-criteo-walkthrough/PfCHkVg.png)

I det här fallet, som kan visas, så kan strider användas och den andra kolumnen ignoreras. Du kan också ange parametrar som tröskelvärdet för skräp plats, hur många pseudo-tidigare exempel som ska läggas till för utjämning och om du vill använda ett Laplacian brus eller inte. Allt detta är avancerade funktioner och det är att notera att standardvärdena är en lämplig utgångs punkt för användare som är nya för den här typen av funktions skapande.

##### <a name="data-transformation-before-generating-the-count-features"></a>Datatransformering innan du genererar Count-funktionerna
Nu är fokus på en viktig punkt om att transformera våra träna-och test data innan de faktiskt genererar funktioner. Det finns två **köra R-skript** moduler som används innan Count-transformeringen tillämpas på våra data.

![Köra R-skript moduler](./media/hive-criteo-walkthrough/aF59wbc.png)

Här är det första R-skriptet:

![Första R-skriptet](./media/hive-criteo-walkthrough/3hkIoMx.png)

Detta R-skript byter namn på våra kolumner till namn "Col1" till "Col40". Detta beror på att transformering av antal förväntar sig namn på det här formatet.

Det andra R-skriptet balanserar fördelningen mellan positiva och negativa klasser (klasserna 1 respektive 0) genom att ta sampling av den negativa klassen. R-skriptet visar hur du gör detta:

![Andra R-skriptet](./media/hive-criteo-walkthrough/91wvcwN.png)

I det här enkla R-skriptet används "POS \_ neg \_ ratio" för att ange graden av balans mellan positiva och negativa klasser. Detta är viktigt att göra eftersom förbättring av klassen vanligt vis har prestanda förmåner för klassificerings problem där klass fördelningen är skevad (du kan i det här fallet ha 3,3% positiv klass och 96,7% negativ klass).

##### <a name="applying-the-count-transformation-on-our-data"></a>Använda omvandlingen av antal på våra data
Slutligen kan du använda modulen **Använd omvandling** för att tillämpa antalet transformeringar i våra träna-och test-datauppsättningar. I den här modulen tas transformeringen för antal sparade som indata och träna-eller test data uppsättningarna som andra indata och returnerar data med Count-funktioner. Den visas här:

![Använd omvandlings modul](./media/hive-criteo-walkthrough/xnQvsYf.png)

##### <a name="an-excerpt-of-what-the-count-features-look-like"></a>Ett utdrag av vad Count-funktionerna ser ut
Det är en instruktion att se hur antalet funktioner ser ut som i vårt fall. Här är ett utdrag av detta:

![Räkna funktioner](./media/hive-criteo-walkthrough/FO1nNfw.png)

Detta utdrag visar att för de kolumner som räknas på, får du antalet och logg strider utöver eventuella relevanta återförsök.

Nu är du redo att skapa en Azure Machine Learning modell med dessa omvandlade data uppsättningar. I nästa avsnitt visas hur detta kan göras.

### <a name="step-3-build-train-and-score-the-model"></a><a name="step3"></a> Steg 3: skapa, träna och Poäng modellen

#### <a name="choice-of-learner"></a>Val av lärare
Först måste du välja en elev. Använd ett besluts träd med två klasser som vår elev. Här är standard alternativen för den här eleven:

![Two-Class utökat besluts träds parametrar](./media/hive-criteo-walkthrough/bH3ST2z.png)

För experimentet väljer du standardvärdena. Standardvärdena är meningsfulla och ett bra sätt att få snabba bas linjer om prestanda. Du kan förbättra prestanda genom att svepa parametrarna om du väljer att använda en bas linje.

#### <a name="train-the-model"></a>Träna modellen
För utbildning ska du bara anropa en modul för **träna modell** . De två inmatningarna till den är Two-Class utökat besluts trädare och vår träna data uppsättning. Detta visas här:

![Träna modell modul](./media/hive-criteo-walkthrough/2bZDZTy.png)

#### <a name="score-the-model"></a>Poängsätta modellen
När du har en tränad modell är du redo att få poäng på test data uppsättningen och utvärdera dess prestanda. Gör detta med hjälp av modulen **Poäng modell** som visas i följande figur, tillsammans med modulen **utvärdera modell** :

![Modulen Poängsätta modell](./media/hive-criteo-walkthrough/fydcv6u.png)

### <a name="step-4-evaluate-the-model"></a><a name="step4"></a> Steg 4: utvärdera modellen
Slutligen bör du analysera modell prestanda. För två klassificerings problem för klass (Binary) är vanligt vis ett lyckat mått AUC. Om du vill visualisera den här kurvan ansluter du **Poäng modellens** modul till en **modell för utvärderings modell** . Att klicka på **visualisera** i modulen **utvärdera modell** ger en bild som liknar följande:

![Utvärdera modulen BDT modell](./media/hive-criteo-walkthrough/0Tl0cdg.png)

I binära (eller två klass-) klassificerings problem är ett utmärkt mått på förutsägelse noggrannhet den yta under kurvan (AUC). I följande avsnitt visas våra resultat med den här modellen i vår test data uppsättning. Högerklicka på utdataporten för modulen **utvärdera modell** och **visualisera** sedan.

![Visualisera modell modul för bedömning](./media/hive-criteo-walkthrough/IRfc7fH.png)

### <a name="step-5-publish-the-model-as-a-web-service"></a><a name="step5"></a> Steg 5: publicera modellen som en webb tjänst
Möjligheten att publicera en Azure Machine Learning modell som webb tjänster med ett minimum av återställnings bara är en värdefull funktion för att göra den mycket tillgänglig. När den är färdig kan vem som helst ringa till webb tjänsten med indata som de behöver förutsägelse för, och webb tjänsten använder modellen för att returnera dessa förutsägelser.

Spara först vår utbildade modell som ett tränat modell objekt genom att högerklicka på modulen **träna modell** och använda alternativet **Spara som tränad modell** .

Skapa sedan indata-och utgående portar för vår webb tjänst:

* en indataport tar data i samma formulär som de data som du behöver förutsägelser för
* en utgående port returnerar de poäng etiketter och de associerade sannolikheterna.

#### <a name="select-a-few-rows-of-data-for-the-input-port"></a>Välj några rader med data för Indataporten
Det är praktiskt att använda modulen Använd **SQL-omvandling** för att välja bara 10 rader som ska fungera som indataports data. Välj bara dessa rader med data för vår dataport med SQL-frågan som visas här:

![Port data för indata](./media/hive-criteo-walkthrough/XqVtSxu.png)

#### <a name="web-service"></a>Webbtjänst
Nu är du redo att köra ett litet experiment som kan användas för att publicera vår webb tjänst.

#### <a name="generate-input-data-for-webservice"></a>Generera indata för WebService
Som ett Zeroth-steg, eftersom Count-tabellen är stor, tar några rader med test data och genererar utdata från den med Count-funktioner. Dessa utdata kan fungera som indata-format för vår WebService, som du ser här:

![Skapa BDT-indata](./media/hive-criteo-walkthrough/OEJMmst.png)

> [!NOTE]
> För indata-format använder du utdata från modulen **Count upplärda** . När experimentet är klart sparar du resultatet från modulen **Count upplärda** som en data uppsättning. Den här data uppsättningen används för indata i WebService.
>
>

#### <a name="scoring-experiment-for-publishing-webservice"></a>Poäng experiment för publicering av WebService
Först är den viktigaste strukturen en **modell för Poäng modell** som accepterar vårt utbildade modell objekt och några rader med indata som genererades i föregående steg med upplärda-modulen **Count** . Använd "Välj kolumner i data uppsättning" för att projicera ut de resultat etiketter och poängen som är trolig.

![Välja kolumner i datauppsättning](./media/hive-criteo-walkthrough/kRHrIbe.png)

Observera hur **Välj kolumner i data uppsättnings** modulen kan användas för ' filtrering '-data från en data uppsättning. Innehållet visas här:

![Filtrering med modulen Välj kolumner i data uppsättning](./media/hive-criteo-walkthrough/oVUJC9K.png)

Om du vill hämta de blå portarna för indata och utdata klickar du bara på **Förbered WebService** längst ned till höger. Genom att köra det här experimentet kan vi också publicera webb tjänsten: Klicka på ikonen **publicera webb tjänst** längst ned till höger, som visas här:

![Publicera webb tjänst](./media/hive-criteo-walkthrough/WO0nens.png)

När webb tjänsten har publicerats omdirigerar du till en sida som ser ut så här:

![Instrumentpanel för webbtjänsten](./media/hive-criteo-walkthrough/YKzxAA5.png)

Lägg märke till de två länkarna för WebServices på vänster sida:

* Tjänsten **Request/Response** (eller RR) är avsedd för enskilda förutsägelser och är det som har utnyttjats i den här workshopen.
* BES ( **batch EXECUTION** service) används för batch-förutsägelser och kräver att indata som används för att göra förutsägelser finns i Azure Blob Storage.

Om du klickar på länken **förfrågan/svar** tas vi till en sida som ger oss före konserverad kod i C#, python och R. Den här koden kan enkelt användas för att ringa till webbtjänsten. API-nyckeln på den här sidan måste användas för autentisering.

Det är enkelt att kopiera den här python-koden till en ny cell i IPython Notebook.

Här är ett segment med python-kod med rätt API-nyckel.

![Python-kod](./media/hive-criteo-walkthrough/f8N4L4g.png)

Standard-API-nyckeln har ersatts med vår API-nyckel för WebService. Om du klickar på **Kör** på den här cellen i en IPython Notebook får du följande svar:

![IPython-svar](./media/hive-criteo-walkthrough/KSxmia2.png)

De två test exemplen som meddelas om i python-skriptet JSON Framework får du tillbaka svar i formatet "Poäng etiketter, betygs ätt". I det här fallet har standardvärdena valts som den före konserverande koden innehåller (0 gäller för alla numeriska kolumner och strängen "värde" för alla kategoriska-kolumner).

I slutet visar vår genom gång hur du hanterar storskalig data uppsättning med Azure Machine Learning. Du började med en terabyte data, konstruerat en förutsägelse modell och distribuerat den som en webb tjänst i molnet.