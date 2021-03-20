---
title: Använda Apache Beeline med Apache Hive – Azure HDInsight
description: Lär dig hur du använder Beeline-klienten för att köra Hive-frågor med Hadoop på HDInsight. Beeline är ett verktyg för att arbeta med HiveServer2 över JDBC.
ms.service: hdinsight
ms.topic: how-to
ms.date: 10/28/2020
ms.custom: contperf-fy21q1, contperf-fy21q2
ms.openlocfilehash: e8b7478ba64da0f99a9b7a710222ff2953795adf
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98943195"
---
# <a name="use-the-apache-beeline-client-with-apache-hive"></a>Använda Apache Beeline-klienten med Apache Hive

Den här artikeln beskriver hur du använder en kommando rad [Apache Beeline](https://cwiki.apache.org/confluence/display/Hive/HiveServer2+Clients#HiveServer2Clients-Beeline–NewCommandLineShell) -klient för att skapa och köra apache Hive frågor över en SSH-anslutning.

## <a name="background"></a>Bakgrund

Beeline är en Hive-klient som ingår i head-noderna i HDInsight-klustret. Om du vill ansluta till Beeline-klienten som är installerad i HDInsight-klustret eller installera Beeline lokalt, se [ansluta till eller installera Apache Beeline](connect-install-beeline.md). Beeline använder JDBC för att ansluta till HiveServer2, en tjänst som finns i HDInsight-klustret. Du kan också använda Beeline för att få åtkomst till Hive i HDInsight via Internet. I följande exempel finns de vanligaste anslutnings strängarna som används för att ansluta till HDInsight från Beeline.

## <a name="prerequisites-for-examples"></a>Krav för exempel

* Ett Hadoop-kluster i HDInsight. Se [Kom igång med HDInsight på Linux](./apache-hadoop-linux-tutorial-get-started.md).

* Lägg märke till URI-schemat för klustrets primära lagring. Till exempel  `wasb://` för Azure Storage för `abfs://` Azure Data Lake Storage Gen2 eller `adl://` för Azure Data Lake Storage gen1. Om säker överföring har Aktiver ATS för Azure Storage är URI: n `wasbs://` . Mer information finns i [säker överföring](../../storage/common/storage-require-secure-transfer.md).

* En SSH-klient. Mer information finns i [Ansluta till HDInsight (Apache Hadoop) med hjälp av SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). De flesta av stegen i det här dokumentet förutsätter att du använder Beeline från en SSH-session till klustret. Du kan också använda en lokal Beeline-klient, men de här stegen beskrivs inte i den här artikeln.

## <a name="run-a-hive-query"></a>Köra en Hive-fråga

Det här exemplet baseras på användningen av Beeline-klienten från en SSH-anslutning.

1. Öppna en SSH-anslutning till klustret med koden nedan. Ersätt `sshuser` med SSH-användare för klustret och ersätt `CLUSTERNAME` med namnet på klustret. När du uppmanas till det anger du lösen ordet för SSH-användarkontot.

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

2. Anslut till HiveServer2 med Beeline-klienten från den öppna SSH-sessionen genom att ange följande kommando:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http'
    ```

3. Beeline-kommandon börjar med ett `!` Character, till exempel `!help` visar hjälp. Du `!` kan dock utelämna vissa kommandon. Till exempel `help` fungerar också.

    Det finns `!sql` , som används för att köra HiveQL-instruktioner. HiveQL används dock ofta som du kan utelämna föregående `!sql` . Följande två uttryck är likvärdiga:

    ```hiveql
    !sql show tables;
    show tables;
    ```

    I ett nytt kluster visas endast en tabell: **hivesampletable**.

4. Använd följande kommando för att visa schemat för hivesampletable:

    ```hiveql
    describe hivesampletable;
    ```

    Det här kommandot returnerar följande information:

    ```output
    +-----------------------+------------+----------+--+
    |       col_name        | data_type  | comment  |
    +-----------------------+------------+----------+--+
    | clientid              | string     |          |
    | querytime             | string     |          |
    | market                | string     |          |
    | deviceplatform        | string     |          |
    | devicemake            | string     |          |
    | devicemodel           | string     |          |
    | state                 | string     |          |
    | country               | string     |          |
    | querydwelltime        | double     |          |
    | sessionid             | bigint     |          |
    | sessionpagevieworder  | bigint     |          |
    +-----------------------+------------+----------+--+
    ```

    Den här informationen beskriver kolumnerna i tabellen.

5. Ange följande instruktioner för att skapa en tabell med namnet **log4jLogs** med hjälp av exempel data från HDInsight-klustret: (ändra efter behov baserat på ditt URI-schema).

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
    STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs
        WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log'
        GROUP BY t4;
    ```

    Dessa uttryck gör följande:

    |Uttryck |Beskrivning |
    |---|---|
    |DROP TABLE|Om tabellen finns, tas den bort.|
    |SKAPA EXTERN TABELL|Skapar en **extern** tabell i Hive. Externa tabeller lagrar bara tabell definitionen i Hive. Data finns kvar på den ursprungliga platsen.|
    |RAD FORMAT|Hur data formateras. I det här fallet separeras fälten i varje logg med ett blank steg.|
    |LAGRAD SOM TEXTFILE-PLATS|Var data lagras och i vilket fil format.|
    |SELECT|Väljer ett antal rader där kolumnen **T4** innehåller värdet **[Error]**. Den här frågan returnerar värdet **3** eftersom det finns tre rader som innehåller det här värdet.|
    |INPUT__FILE__NAME som%. log|Hive försöker tillämpa schemat på alla filer i katalogen. I det här fallet innehåller katalogen filer som inte matchar schemat. För att förhindra skräp data i resultaten anger den här instruktionen Hive att den bara ska returnera data från filer som slutar med. log.|

   > [!NOTE]  
   > Externa tabeller bör användas när du förväntar dig att underliggande data ska uppdateras av en extern källa. Till exempel en automatiserad data överförings process eller en MapReduce-åtgärd.
   >
   > Att släppa en extern tabell tar **inte** bort data, endast tabell definitionen.

    Utdata från det här kommandot liknar följande text:

    ```output
    INFO  : Tez session hasn't been created yet. Opening session
    INFO  :

    INFO  : Status: Running (Executing on YARN cluster with App id application_1443698635933_0001)

    INFO  : Map 1: -/-      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0/1      Reducer 2: 0/1
    INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
    INFO  : Map 1: 0(+1)/1  Reducer 2: 0/1
    INFO  : Map 1: 1/1      Reducer 2: 0/1
    INFO  : Map 1: 1/1      Reducer 2: 0(+1)/1
    INFO  : Map 1: 1/1      Reducer 2: 1/1
    +----------+--------+--+
    |   sev    | count  |
    +----------+--------+--+
    | [ERROR]  | 3      |
    +----------+--------+--+
    1 row selected (47.351 seconds)
    ```

6. Avsluta Beeline:

    ```bash
    !exit
    ```

## <a name="run-a-hiveql-file"></a>Köra en HiveQL-fil

Det här exemplet är en fortsättning från föregående exempel. Använd följande steg för att skapa en fil och kör den med hjälp av Beeline.

1. Använd följande kommando för att skapa en fil med namnet **Query. HQL**:

    ```bash
    nano query.hql
    ```

1. Använd följande text som filens innehåll. Den här frågan skapar en ny intern tabell med namnet **errorLogs**:

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';
    ```

    Dessa uttryck gör följande:

    |Uttryck |Beskrivning |
    |---|---|
    |CREATE TABLE OM DEN INTE FINNS|Om tabellen inte redan finns skapas den. Eftersom det **externa** nyckelordet inte används skapar den här instruktionen en intern tabell. Interna tabeller lagras i Hive-datalagret och hanteras fullständigt av Hive.|
    |LAGRAD SOM ORC|Lagrar data i optimerade rad kolumners (ORC)-format. ORC-formatet är ett mycket optimerat och effektivt format för att lagra Hive-data.|
    |INFOGA ÖVERSKRIVNING... SELECT|Markerar rader från tabellen **log4jLogs** som innehåller **[Error]** och infogar sedan data i **errorLogs** -tabellen.|

    > [!NOTE]  
    > Till skillnad från externa tabeller, tar en intern tabell bort även underliggande data.

1. Om du vill spara filen använder du **CTRL** + **X** och anger sedan **Y** och slutligen **RETUR**.

1. Använd följande för att köra filen med Beeline:

    ```bash
    beeline -u 'jdbc:hive2://headnodehost:10001/;transportMode=http' -i query.hql
    ```

    > [!NOTE]  
    > `-i`Parametern startar Beeline och kör instruktionerna i `query.hql` filen. När frågan har slutförts kommer du till `jdbc:hive2://headnodehost:10001/>` prompten. Du kan också köra en fil med hjälp av `-f` parametern som avslutar Beeline när frågan har slutförts.

1. Verifiera att tabellen **errorLogs** har skapats genom att använda följande instruktion för att returnera alla rader från **errorLogs**:

    ```hiveql
    SELECT * from errorLogs;
    ```

    Tre rader med data ska returneras, alla innehåller **[Error]** i kolumnen T4:

    ```output
    +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
    | errorlogs.t1  | errorlogs.t2  | errorlogs.t3  | errorlogs.t4  | errorlogs.t5  | errorlogs.t6  | errorlogs.t7  |
    +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
    | 2012-02-03    | 18:35:34      | SampleClass0  | [ERROR]       | incorrect     | id            |               |
    | 2012-02-03    | 18:55:54      | SampleClass1  | [ERROR]       | incorrect     | id            |               |
    | 2012-02-03    | 19:25:27      | SampleClass4  | [ERROR]       | incorrect     | id            |               |
    +---------------+---------------+---------------+---------------+---------------+---------------+---------------+--+
    3 rows selected (0.813 seconds)
    ```

## <a name="next-steps"></a>Nästa steg

* Mer allmän information om Hive i HDInsight finns i [använda Apache Hive med Apache Hadoop på HDInsight](hdinsight-use-hive.md)

* Mer information om andra sätt att arbeta med Hadoop i HDInsight finns i [använda MapReduce med Apache Hadoop på HDInsight](hdinsight-use-mapreduce.md)
