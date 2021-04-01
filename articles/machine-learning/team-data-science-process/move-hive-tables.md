---
title: Skapa Hive-tabeller och läsa in data från Blob Storage – team data science process
description: Använd Hive-frågor för att skapa Hive-tabeller och läsa in data från Azure Blob Storage. Partitionera Hive-tabeller och Använd den optimerade rad kolumnerna (ORC) för att förbättra prestanda för frågor.
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
ms.openlocfilehash: 5d61c0f5f26bc46b9c4a5bc4a793df1e10710004
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "96006737"
---
# <a name="create-hive-tables-and-load-data-from-azure-blob-storage"></a>Skapa Hive-tabeller och läsa in data från Azure Blob Storage

Den här artikeln visar allmänna Hive-frågor som skapar Hive-tabeller och läser in data från Azure Blob Storage. Det finns också en del av rikt linjerna för att partitionera Hive-tabeller och använda den optimerade rad kolumnerna (ORC) för att förbättra prestanda för frågor.

## <a name="prerequisites"></a>Förutsättningar
Den här artikeln förutsätter att du har:

* Ett Azure Storage-konto har skapats. Om du behöver instruktioner, se [om Azure Storage-konton](../../storage/common/storage-introduction.md).
* Etablerade ett anpassat Hadoop-kluster med HDInsight-tjänsten.  Om du behöver instruktioner, se [installations kluster i HDInsight](../../hdinsight/hdinsight-hadoop-provision-linux-clusters.md).
* Fjärråtkomst till klustret, inloggad och öppnade Hadoop Command-Line-konsolen. Om du behöver instruktioner, se [hantera Apache Hadoop kluster](../../hdinsight/hdinsight-administer-use-portal-linux.md).

## <a name="upload-data-to-azure-blob-storage"></a>Ladda upp data till Azure Blob Storage
Om du har skapat en virtuell Azure-dator genom att följa anvisningarna i [Konfigurera en virtuell Azure-dator för avancerad analys](../../machine-learning/data-science-virtual-machine/overview.md), bör den här skript filen ha hämtats till katalogen *C: \\ användare \\ \<user name\> \\ dokument \\ data science scripts* på den virtuella datorn. Dessa Hive-frågor kräver bara att du anger ett data schema och Azure Blob Storage-konfigurationen i lämpliga fält för att kunna skickas.

Vi antar att data för Hive-tabeller är i ett **okomprimerat** tabell format och att data har överförts till standard (eller till en ytterligare) behållare för det lagrings konto som används av Hadoop-klustret.

Om du vill öva på **NYC taxi-rese data** måste du:

* **Hämta** 24 [NYC taxi](https://www.andresmh.com/nyctaxitrips) -datafiler (12-reseinformation och 12-pris per pris).
* **zippa** upp alla filer till CSV-filer och sedan
* **överför** dem till standardvärdet (eller lämplig container) för det Azure Storage kontot. alternativ för ett sådant konto visas i avsnittet [använda Azure Storage med Azure HDInsight-kluster](../../hdinsight/hdinsight-hadoop-use-blob-storage.md) . Processen att ladda upp CSV-filerna till standard behållaren på lagrings kontot finns på den här [sidan](hive-walkthrough.md#upload).

## <a name="how-to-submit-hive-queries"></a><a name="submit"></a>Så här skickar du Hive-frågor
Hive-frågor kan skickas med:

* [Skicka Hive-frågor via Hadoop-kommandoraden i huvudnoden för Hadoop-kluster](#headnode)
* [Skicka Hive-frågor med Hive-redigeraren](#hive-editor)
* [Skicka Hive-frågor med Azure PowerShell-kommandon](#ps)

Hive-frågor är SQL-liknande. Om du är bekant med SQL kan du hitta [Hive för SQL-användare lathund-bladet](https://hortonworks.com/wp-content/uploads/2013/05/hql_cheat_sheet.pdf) .

När du skickar en Hive-fråga kan du också styra målet för utdata från Hive-frågor, oavsett om den finns på skärmen eller till en lokal fil på Head-noden eller i en Azure-blob.

### <a name="submit-hive-queries-through-hadoop-command-line-in-headnode-of-hadoop-cluster"></a><a name="headnode"></a>Skicka Hive-frågor via Hadoop-kommandoraden i huvudnoden för Hadoop-kluster
Om Hive-frågan är komplex kan du skicka den direkt i noden Head i Hadoop-klustret så att den blir snabbare än att skicka den till en Hive-redigerare eller Azure PowerShell skript.

Logga in på noden Head i Hadoop-klustret, öppna Hadoop-kommandoraden på Skriv bordet i head-noden och ange kommandot `cd %hive_home%\bin` .

Det finns tre sätt att skicka Hive-frågor på Hadoop-kommando raden:

* rakt
* använda. HQL-filer
* med kommando konsolen för Hive

#### <a name="submit-hive-queries-directly-in-hadoop-command-line"></a>Skicka Hive-frågor direkt i Hadoop-kommandoraden.
Du kan köra kommandot som `hive -e "<your hive query>;` att skicka enkla Hive-frågor direkt i Hadoop-kommandoraden. Här är ett exempel där den röda rutan visar kommandot som skickar Hive-frågan, och den gröna rutan beskriver utdata från Hive-frågan.

![Kommando för att skicka Hive-fråga med utdata från Hive-fråga](./media/move-hive-tables/run-hive-queries-1.png)

#### <a name="submit-hive-queries-in-hql-files"></a>Skicka Hive-frågor i '. HQL '-filer
När Hive-frågan är mer komplicerad och innehåller flera rader är det inte praktiskt att redigera frågor på kommando raden eller i Hive-konsolen. Ett alternativ är att använda en text redigerare i noden Head i Hadoop-klustret för att spara Hive-frågor i en. HQL-fil i en lokal katalog i head-noden. Sedan kan Hive-frågan i filen ". HQL" skickas genom att använda `-f` argumentet enligt följande:

```console
hive -f "<path to the '.hql' file>"
```

![Hive-fråga i en. HQL-fil](./media/move-hive-tables/run-hive-queries-3.png)

**Ignorera förlopps status skärm utskrift av Hive-frågor**

Efter att Hive-frågan har skickats i Hadoop-kommandoraden visas som standard förloppet för utskriften på skärmen. Om du inte vill att skärmen ska skrivas ut, kan du använda ett argument `-S` ("S" i versaler) i kommando raden enligt följande:

```console
hive -S -f "<path to the '.hql' file>"
hive -S -e "<Hive queries>"
```

#### <a name="submit-hive-queries-in-hive-command-console"></a>Skicka Hive-frågor i Hive-kommandorads konsol.
Du kan också först ange Hive-kommandoraden genom att köra kommandot `hive` i Hadoop-kommandoraden och sedan skicka Hive-frågor i Hive-Kommandotolken. Här är ett exempel. I det här exemplet markeras de två röda rutorna som används för att ange Hive-kommando konsolen och Hive-frågan som skickas i Hive-kommando konsol. Den gröna rutan markerar utdata från Hive-frågan.

![Öppna Hive kommando konsol och ange kommando, Visa Hive-fråga utdata](./media/move-hive-tables/run-hive-queries-2.png)

I föregående exempel visas utdata från Hive-frågan direkt på skärmen. Du kan också skriva utdata till en lokal fil på Head-noden eller till en Azure-blob. Sedan kan du använda andra verktyg för att ytterligare analysera utdata från Hive-frågor.

**Resulterande Hive-frågeresultat i en lokal fil.**
Om du vill returnera Hive-frågeresultaten till en lokal katalog på Head-noden måste du skicka Hive-frågan på Hadoop-kommandoraden enligt följande:

```console
hive -e "<hive query>" > <local path in the head node>
```

I följande exempel skrivs utdata från Hive-frågan till i en fil `hivequeryoutput.txt` i katalogen `C:\apps\temp` .

![Skärm bild som visar utdata från Hive-frågan i ett Hadoop-kommando rads fönster.](./media/move-hive-tables/output-hive-results-1.png)

**Returnera utdata för Hive-frågor till en Azure-Blob**

Du kan också spara Hive-frågeresultaten till en Azure-Blob, inom standard behållaren för Hadoop-klustret. Hive-frågan för detta är följande:

```console
insert overwrite directory wasb:///<directory within the default container> <select clause from ...>
```

I följande exempel skrivs utdata från Hive-frågan till en BLOB-katalog `queryoutputdir` inom standard behållaren för Hadoop-klustret. Här behöver du bara ange katalog namnet utan BLOB-namnet. Ett fel uppstår om du anger både katalog-och blob-namn, till exempel `wasb:///queryoutputdir/queryoutput.txt` .

![Skärm bild som visar föregående kommando i kommando rads fönstret för Hadoop.](./media/move-hive-tables/output-hive-results-2.png)

Om du öppnar standard behållaren för Hadoop-klustret med hjälp av Azure Storage Explorer kan du se utdata för Hive-frågan som visas i följande bild. Du kan använda filtret (markerat med röd ruta) för att bara hämta blobben med angivna bokstäver i namn.

![Azure Storage Explorer visar utdata från Hive-frågan](./media/move-hive-tables/output-hive-results-3.png)

### <a name="submit-hive-queries-with-the-hive-editor"></a><a name="hive-editor"></a>Skicka Hive-frågor med Hive-redigeraren
Du kan också använda fråga konsolen (Hive-redigeraren) genom att ange en URL för formatet *https: \/ / \<Hadoop cluster name> . azurehdinsight.net/Home/HiveEditor* i en webbläsare. Du måste vara inloggad i se den här konsolen och så att du behöver autentiseringsuppgifterna för Hadoop-klustret här.

### <a name="submit-hive-queries-with-azure-powershell-commands"></a><a name="ps"></a>Skicka Hive-frågor med Azure PowerShell-kommandon
Du kan också använda PowerShell för att skicka Hive-frågor. Instruktioner finns i [Skicka Hive-jobb med PowerShell](../../hdinsight/hadoop/apache-hadoop-use-hive-powershell.md).

## <a name="create-hive-database-and-tables"></a><a name="create-tables"></a>Skapa Hive-databas och tabeller
Hive-frågorna delas i GitHub- [lagringsplatsen](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_db_tbls_load_data_generic.hql) och kan laddas ned därifrån.

Här är den Hive-fråga som skapar en Hive-tabell.

```hiveql
create database if not exists <database name>;
CREATE EXTERNAL TABLE if not exists <database name>.<table name>
(
    field1 string,
    field2 int,
    field3 float,
    field4 double,
    ...,
    fieldN string
)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' lines terminated by '<line separator>'
STORED AS TEXTFILE LOCATION '<storage location>' TBLPROPERTIES("skip.header.line.count"="1");
```

Här följer beskrivningar av de fält som du behöver för att ansluta till och andra konfigurationer:

* **\<database name\>**: namnet på den databas som du vill skapa. Om du bara vill använda standard databasen kan frågan "*skapa databas...*" utelämnas.
* **\<table name\>**: namnet på den tabell som du vill skapa i den angivna databasen. Om du vill använda standard databasen kan tabellen direkt refereras av *\<table name\>* utan \<database name\> .
* **\<field separator\>**: avgränsaren som avgränsar fält i data filen som ska överföras till Hive-tabellen.
* **\<line separator\>**: avgränsaren som avgränsar rader i data filen.
* **\<storage location\>**: Azure Storage plats där du vill spara data för Hive-tabeller. Om du inte anger *plats \<storage location\>* lagras databasen och tabellerna i *Hive/lager/* katalog i standard behållaren för Hive-klustret som standard. Om du vill ange lagrings platsen måste lagrings platsen finnas i standard behållaren för databasen och tabellerna. Den här platsen måste refereras till platsen i förhållande till standard behållaren för klustret i formatet *"wasb:/// \<directory 1> /"* eller *"wasb:/// \<directory 1> / \<directory 2> /"* osv. När frågan har körts skapas de relativa katalogerna i standard behållaren.
* **TBLPROPERTIES ("Skip. header. line. Count" = "1")**: om data filen har en rubrik rad måste du lägga till den här egenskapen **i slutet** av frågan *CREATE TABLE* . Annars läses rubrik raden in som en post i tabellen. Om data filen inte har någon rubrik rad kan den här konfigurationen utelämnas i frågan.

## <a name="load-data-to-hive-tables"></a><a name="load-data"></a>Läs in data till Hive-tabeller
Här är den Hive-fråga som läser in data i en Hive-tabell.

```hiveql
LOAD DATA INPATH '<path to blob data>' INTO TABLE <database name>.<table name>;
```

* **\<path to blob data\>**: Om BLOB-filen som ska överföras till Hive-tabellen finns i standard behållaren för HDInsight Hadoop-klustret ska den *\<path to blob data\>* vara i formatet *' wasb:// \<directory in this container> / \<blob file name> '*. BLOB-filen kan också finnas i ytterligare en behållare för HDInsight Hadoop-klustret. I det här fallet *\<path to blob data\>* ska ha formatet *' wasb:// \<container name> @ \<storage account name> . blob.Core.Windows.net/ \<blob file name> '*.

  > [!NOTE]
  > BLOB-data som ska överföras till Hive-tabellen måste finnas i standard eller ytterligare behållare för lagrings kontot för Hadoop-klustret. I annat fall Miss lyckas *inläsnings data* frågan så att den inte kan komma åt data.
  >
  >

## <a name="advanced-topics-partitioned-table-and-store-hive-data-in-orc-format"></a><a name="partition-orc"></a>Avancerade ämnen: partitionerad tabell och lagra Hive-data i ORC-format
Om data är stora är det bra att partitionera tabellen för frågor som bara behöver söka igenom några partitioner i tabellen. Det är till exempel rimligt att partitionera logg data för en webbplats efter datum.

Förutom att partitionera Hive-tabeller, är det också fördelaktigt att lagra Hive-data i optimerade rad kolumners (ORC)-format. Mer information om ORC-formatering finns i <a href="https://cwiki.apache.org/confluence/display/Hive/LanguageManual+ORC#LanguageManualORC-ORCFiles" target="_blank">använda Orc-filer ger bättre prestanda när Hive läser, skriver och bearbetar data</a>.

### <a name="partitioned-table"></a>Partitionerad tabell
Här är Hive-frågan som skapar en partitionerad tabell och läser in data i den.

```hiveql
CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<table name>
(field1 string,
...
fieldN string
)
PARTITIONED BY (<partitionfieldname> vartype) ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
    lines terminated by '<line separator>' TBLPROPERTIES("skip.header.line.count"="1");
LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<partitioned table name>
    PARTITION (<partitionfieldname>=<partitionfieldvalue>);
```

När du frågar efter partitionerade tabeller rekommenderar vi att du lägger till ett partitionsschema i **början** av `where` -satsen, vilket förbättrar Sök effektiviteten.

```hiveql
select
    field1, field2, ..., fieldN
from <database name>.<partitioned table name>
where <partitionfieldname>=<partitionfieldvalue> and ...;
```

### <a name="store-hive-data-in-orc-format"></a><a name="orc"></a>Lagra Hive-data i ORC-format
Du kan inte direkt läsa in data från Blob Storage till Hive-tabeller som lagras i ORC-format. Här följer de steg som du måste vidta för att läsa in data från Azure-blobbar till Hive-tabeller som lagras i ORC-format.

Skapa en extern tabell **som lagras som textfile** och Läs in data från Blob Storage till tabellen.

```hiveql
CREATE EXTERNAL TABLE IF NOT EXISTS <database name>.<external textfile table name>
(
    field1 string,
    field2 int,
    ...
    fieldN date
)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>'
    lines terminated by '<line separator>' STORED AS TEXTFILE
    LOCATION 'wasb:///<directory in Azure blob>' TBLPROPERTIES("skip.header.line.count"="1");

LOAD DATA INPATH '<path to the source file>' INTO TABLE <database name>.<table name>;
```

Skapa en intern tabell med samma schema som den externa tabellen i steg 1, med samma fält avgränsare och lagra Hive-data i ORC-formatet.

```hiveql
CREATE TABLE IF NOT EXISTS <database name>.<ORC table name>
(
    field1 string,
    field2 int,
    ...
    fieldN date
)
ROW FORMAT DELIMITED FIELDS TERMINATED BY '<field separator>' STORED AS ORC;
```

Välj data från den externa tabellen i steg 1 och infoga dem i ORC-tabellen

```hiveql
INSERT OVERWRITE TABLE <database name>.<ORC table name>
    SELECT * FROM <database name>.<external textfile table name>;
```

> [!NOTE]
> Om tabellen TEXTFILE *\<database name\> . \<external textfile table name\>* har partitioner, i steg 3, `SELECT * FROM <database name>.<external textfile table name>` väljer kommandot variabeln partition som ett fält i den returnerade data uppsättningen. Infoga den i *\<database name\> . \<ORC table name\>* Miss lyckas sedan *\<database name\> . \<ORC table name\>* har inte variabeln partition som ett fält i tabell schemat. I så fall måste du specifikt välja de fält som ska infogas i *\<database name\> . \<ORC table name\>* så här:
>
>

```hiveql
INSERT OVERWRITE TABLE <database name>.<ORC table name> PARTITION (<partition variable>=<partition value>)
    SELECT field1, field2, ..., fieldN
    FROM <database name>.<external textfile table name>
    WHERE <partition variable>=<partition value>;
```

Det är säkert att släppa *\<external text file table name\>* när du använder följande fråga när alla data har infogats i *\<database name\> . \<ORC table name\>*:

```hiveql
    DROP TABLE IF EXISTS <database name>.<external textfile table name>;
```

När du har använt den här proceduren bör du ha en tabell med data i ORC-format som är redo att använda.  
