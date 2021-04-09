---
title: Vad är Apache Hive och HiveQL – Azure HDInsight
description: Apache Hive är ett informations lager system för Apache Hadoop. Du kan fråga data som lagras i Hive med HiveQL, som liknar Transact-SQL. I det här dokumentet får du lära dig hur du använder Hive och HiveQL med Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 02/28/2020
ms.openlocfilehash: 4e8c6b25055dfc38d56509e1744b8c7fcac40700
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "98944298"
---
# <a name="what-is-apache-hive-and-hiveql-on-azure-hdinsight"></a>Vad är Apache Hive och HiveQL på Azure HDInsight?

[Apache Hive](https://hive.apache.org/) är ett informations lager system för Apache Hadoop. Hive möjliggör data sammanfattning, frågor och analys av data. Hive-frågor skrivs i HiveQL, vilket är ett frågespråk som liknar SQL.

Med Hive kan du projicera strukturen på mycket ostrukturerade data. När du har definierat strukturen kan du använda HiveQL för att fråga data utan kunskaper om Java eller MapReduce.

HDInsight tillhandahåller flera kluster typer, som är anpassade för vissa arbets belastningar. Följande kluster typer används oftast för Hive-frågor:

|Kluster typ |Beskrivning|
|---|---|
|Interaktiv fråga|Ett Hadoop-kluster som tillhandahåller [LLAP-funktioner (Low latens Analytical Processing)](https://cwiki.apache.org/confluence/display/Hive/LLAP) för att förbättra svars tiderna för interaktiva frågor. Mer information finns i dokumentet [starta med interaktiv fråga i HDInsight](../interactive-query/apache-interactive-query-get-started.md) .|
|Hadoop|Ett Hadoop-kluster som är anpassat för arbets belastningar för batch-bearbetning. Mer information finns i dokumentet [starta med Apache Hadoop i HDInsight](../hadoop/apache-hadoop-linux-tutorial-get-started.md) .|
|Spark|Apache Spark har inbyggda funktioner för att arbeta med Hive. Mer information finns i dokumentet [starta med Apache Spark på HDInsight](../spark/apache-spark-jupyter-spark-sql.md) .|
|HBase|HiveQL kan användas för att fråga data som lagras i Apache HBase. Mer information finns i dokumentet [starta med Apache HBase på HDInsight](../hbase/apache-hbase-tutorial-get-started-linux.md) .|

## <a name="how-to-use-hive"></a>Använda Hive

Använd följande tabell för att identifiera de olika sätten att använda Hive med HDInsight:

| **Använd den här metoden** om du vill... | ... **interaktiva** frågor | ... **Batchbearbetning** | ... från det här **klient operativ systemet** |
|:--- |:---:|:---:|:--- |:--- |
| [HDInsight Tools för Visual Studio Code](../hdinsight-for-vscode.md) |✔ |✔ | Linux, UNIX, Mac OS X eller Windows |
| [HDInsight Tools för Visual Studio](../hadoop/apache-hadoop-use-hive-visual-studio.md) |✔ |✔ |Windows |
| [Hive-vy](../hadoop/apache-hadoop-use-hive-ambari-view.md) |✔ |✔ |Valfri (webbläsare baserad) |
| [Beeline-klient](../hadoop/apache-hadoop-use-hive-beeline.md) |✔ |✔ |Linux, UNIX, Mac OS X eller Windows |
| [REST-API](../hadoop/apache-hadoop-use-hive-curl.md) |&nbsp; |✔ |Linux, UNIX, Mac OS X eller Windows |
| [Windows PowerShell](../hadoop/apache-hadoop-use-hive-powershell.md) |&nbsp; |✔ |Windows |

## <a name="hiveql-language-reference"></a>Språk referens för HiveQL

Språk referens för HiveQL finns på [språk hand boken](https://cwiki.apache.org/confluence/display/Hive/LanguageManual).

## <a name="hive-and-data-structure"></a>Hive och data struktur

Hive förstår hur du arbetar med strukturerade och delvis strukturerade data. Till exempel textfiler där fälten avgränsas med särskilda tecken. Följande HiveQL-instruktion skapar en tabell över utrymmes avgränsade data:

```hiveql
CREATE EXTERNAL TABLE log4jLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE LOCATION '/example/data/';
```

Hive stöder också anpassad **serialisering/deserialiserare (SerDe)** för komplexa eller oregelbundet strukturerade data. Mer information finns i avsnittet [använda en anpassad JSON-SerDe med HDInsight](https://web.archive.org/web/20190217104719/https://blogs.msdn.microsoft.com/bigdatasupport/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight/) -dokument.

Mer information om fil format som stöds av Hive finns i [språk hand boken ( https://cwiki.apache.org/confluence/display/Hive/LanguageManual) ](https://cwiki.apache.org/confluence/display/Hive/LanguageManual)

### <a name="hive-internal-tables-vs-external-tables"></a>Interna Hive-tabeller jämfört med externa tabeller

Det finns två typer av tabeller som du kan skapa med Hive:

* __Internt__: data lagras i data lagret för Hive. Informations lagret finns `/hive/warehouse/` på standard lagrings utrymmet för klustret.

    Använd interna tabeller när något av följande villkor gäller:

    * Data är temporära.
    * Du vill att Hive ska hantera livs cykeln för tabellen och data.

* __Externt__: data lagras utanför data lagret. Data kan lagras på alla lagrings enheter som är tillgängliga för klustret.

    Använd externa tabeller när något av följande villkor gäller:

    * Data används också utanför Hive. Datafilerna uppdateras till exempel av en annan process (som inte låser filerna.)
    * Data måste finnas kvar på den underliggande platsen, även när tabellen har släppts.
    * Du behöver en anpassad plats, till exempel ett lagrings konto som inte är standard.
    * Ett annat program än Hive hanterar data formatet, platsen och så vidare.

Mer information finns i blogg inlägget om [interna och externa tabeller i Hive](/archive/blogs/cindygross/hdinsight-hive-internal-and-external-tables-intro) .

## <a name="user-defined-functions-udf"></a>Användardefinierade funktioner (UDF)

Hive kan också utökas genom **användardefinierade funktioner (UDF)**. Med en UDF kan du implementera funktioner eller logik som inte är lätt att modellera i HiveQL. Ett exempel på hur du använder UDF: er med Hive finns i följande dokument:

* [Använd en användardefinierad Java-funktion med Apache Hive](../hadoop/apache-hadoop-hive-java-udf.md)

* [Använd en användardefinierad python-funktion med Apache Hive](../hadoop/python-udf-hdinsight.md)

* [Använd en C#-användardefinierad funktion med Apache Hive](../hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md)

* [Så här lägger du till en anpassad Apache Hive användardefinierad funktion i HDInsight](/archive/blogs/bigdatasupport/how-to-add-custom-hive-udfs-to-hdinsight)

* [Ett exempel Apache Hive användardefinierad funktion för att konvertera datum-/tids format till Hive-tidsstämpel](https://github.com/Azure-Samples/hdinsight-java-hive-udf)

## <a name="example-data"></a>Exempeldata

Hive i HDInsight har förinstallerats med en intern tabell med namnet `hivesampletable` . HDInsight tillhandahåller också exempel data uppsättningar som kan användas med Hive. Dessa data uppsättningar lagras i `/example/data` `/HdiSamples` katalogerna och. Dessa kataloger finns i standard lagrings utrymmet för klustret.

## <a name="example-hive-query"></a>Exempel på Hive-fråga

Följande HiveQL-projekt kolumner till `/example/data/sample.log` filen:

```hiveql
DROP TABLE log4jLogs;
CREATE EXTERNAL TABLE log4jLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
STORED AS TEXTFILE LOCATION '/example/data/';
SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs
    WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log'
    GROUP BY t4;
```

I föregående exempel utför HiveQL-instruktionerna följande åtgärder:

|Uttryck |Beskrivning |
|---|---|
|DROP TABLE|Om tabellen redan finns tar du bort den.|
|SKAPA EXTERN TABELL|Skapar en ny **extern** tabell i Hive. Externa tabeller lagrar bara tabell definitionen i Hive. Data placeras kvar på den ursprungliga platsen och i det ursprungliga formatet.|
|RAD FORMAT|Anger Hive hur data formateras. I det här fallet separeras fälten i varje logg med ett blank steg.|
|LAGRAD SOM TEXTFILE-PLATS|Anger Hive var data lagras ( `example/data` katalogen) och lagras som text. Data kan finnas i en fil eller spridas över flera filer i katalogen.|
|SELECT|Väljer ett antal rader där kolumnen **T4** innehåller värdet **[Error]**. Den här instruktionen returnerar värdet **3** eftersom det finns tre rader som innehåller det här värdet.|
|INPUT__FILE__NAME som%. log|Hive försöker tillämpa schemat på alla filer i katalogen. I det här fallet innehåller katalogen filer som inte matchar schemat. För att förhindra skräp data i resultaten anger den här instruktionen Hive att vi bara ska returnera data från filer som slutar med. log.|

> [!NOTE]  
> Externa tabeller bör användas när du förväntar dig att underliggande data ska uppdateras av en extern källa. Till exempel en automatiserad data överförings process eller en MapReduce-åtgärd.
>
> Om du släpper en extern tabell raderas **inte** data, utan endast tabell definitionen tas bort.

Om du vill skapa en **intern** tabell i stället för externa använder du följande HiveQL:

```hiveql
CREATE TABLE IF NOT EXISTS errorLogs (
    t1 string,
    t2 string,
    t3 string,
    t4 string,
    t5 string,
    t6 string,
    t7 string)
STORED AS ORC;
INSERT OVERWRITE TABLE errorLogs
SELECT t1, t2, t3, t4, t5, t6, t7 
    FROM log4jLogs WHERE t4 = '[ERROR]';
```

Dessa instruktioner utför följande åtgärder:

|Uttryck |Beskrivning |
|---|---|
|CREATE TABLE OM DEN INTE FINNS|Om tabellen inte finns skapar du den. Eftersom det **externa** nyckelordet inte används skapar den här instruktionen en intern tabell. Tabellen lagras i Hive-datalagret och hanteras fullständigt av Hive.|
|LAGRAD SOM ORC|Lagrar data i optimerade rad kolumners (ORC)-format. ORC är ett mycket optimerat och effektivt format för att lagra Hive-data.|
|INFOGA ÖVERSKRIVNING... SELECT|Markerar rader från tabellen **log4jLogs** som innehåller **[Error]** och infogar sedan data i tabellen **errorLogs** .|

> [!NOTE]  
> Till skillnad från externa tabeller raderas även underliggande data när en intern tabell släpps.

## <a name="improve-hive-query-performance"></a>Förbättra prestanda för Hive-frågor

### <a name="apache-tez"></a>Apache Tez

[Apache Tez](https://tez.apache.org) är ett ramverk som gör det möjligt för data intensiva program, till exempel Hive, att köra mycket mer effektivt i skala. Tez är aktiverat som standard.  [Apache Hive design dokument för Tez](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) innehåller information om implementerings alternativen och justering av konfigurationer.

### <a name="low-latency-analytical-processing-llap"></a>Analytisk bearbetning av låg latens (LLAP)

[LLAP](https://cwiki.apache.org/confluence/display/Hive/LLAP) (ibland kallat Live Long and process) är en ny funktion i Hive 2,0 som tillåter cachelagring i cacheminnet av frågor. LLAP gör Hive-frågor mycket snabbare, upp till [26x snabbare än Hive 1. x i vissa fall](https://hortonworks.com/blog/announcing-apache-hive-2-1-25x-faster-queries-much/).

HDInsight tillhandahåller LLAP i kluster typen interaktiv fråga. Mer information finns i [Start med Interactive Query](../interactive-query/apache-interactive-query-get-started.md) -dokument.

## <a name="scheduling-hive-queries"></a>Schemalägga Hive-frågor

Det finns flera tjänster som kan användas för att köra Hive-frågor som en del av ett schemalagt eller ett arbets flöde på begäran.

### <a name="azure-data-factory"></a>Azure Data Factory

Azure Data Factory gör att du kan använda HDInsight som en del av en Data Factory pipeline. Mer information om hur du använder Hive från en pipeline finns i [transformera data med Hive-aktivitet i Azure Data Factory](../../data-factory/transform-data-using-hadoop-hive.md) -dokument.

### <a name="hive-jobs-and-sql-server-integration-services"></a>Hive-jobb och SQL Server Integration Services

Du kan använda SQL Server Integration Services (SSIS) för att köra ett Hive-jobb. Azure Feature Pack för SSIS innehåller följande komponenter som fungerar med Hive-jobb i HDInsight.

* [Azure HDInsight Hive-uppgift](/sql/integration-services/control-flow/azure-hdinsight-hive-task)

* [Anslutnings hanteraren för Azure-prenumeration](/sql/integration-services/connection-manager/azure-subscription-connection-manager)

Mer information finns i dokumentationen till [Azure Feature Pack](/sql/integration-services/azure-feature-pack-for-integration-services-ssis) .

### <a name="apache-oozie"></a>Apache Oozie

Apache Oozie är ett arbets flödes-och samordnings system som hanterar Hadoop-jobb. Mer information om hur du använder Oozie med Hive finns i [använda Apache Oozie för att definiera och köra ett arbets flödes](../hdinsight-use-oozie-linux-mac.md) dokument.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig vad Hive är och hur du använder det med Hadoop i HDInsight kan du använda följande länkar för att utforska andra sätt att arbeta med Azure HDInsight.

* [Överföra data till HDInsight](../hdinsight-upload-data.md)
* [Använda python-användardefinierade funktioner (UDF) med Apache Hive och Apache-gris i HDInsight](./python-udf-hdinsight.md)
* [Använda MapReduce-jobb med HDInsight](hdinsight-use-mapreduce.md)