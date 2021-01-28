---
title: Mass inläsning till Apache Phoenix med psql – Azure HDInsight
description: Använd psql-verktyget för att läsa in Mass inläsnings data i Apache Phoenix tabeller i Azure HDInsight
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: d56c9b33d53be0e30769aef8934a2dbf43d06867
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944839"
---
# <a name="bulk-load-data-into-apache-phoenix-using-psql"></a>Massinläsning av data till Apache Phoenix med psql

[Apache Phoenix](https://phoenix.apache.org/) är en öppen källa, massivt parallell Relations databas som bygger på [Apache HBase](../hbase/apache-hbase-overview.md). Phoenix innehåller SQL-liknande frågor via HBase. Phoenix använder JDBC-drivrutiner för att göra det möjligt för användare att skapa, ta bort och ändra SQL-tabeller, index, vyer och sekvenser och upsert-rader individuellt och i grupp. Phoenix använder noSQL intern kompilering i stället för att använda MapReduce för att kompilera frågor, för att skapa program med låg latens ovanpå HBase. Phoenix lägger till processorer som stöder körning av kundlevererad kod i serverns adress utrymme och kör koden som samplacerade med data. Detta minimerar överföringen av klient/server-data.  Om du vill arbeta med data med hjälp av Phoenix i HDInsight måste du först skapa tabeller och sedan läsa in data i dem.

## <a name="bulk-loading-with-apache-phoenix"></a>Mass inläsning med Apache Phoenix

Det finns flera sätt att hämta data till HBase, inklusive att använda klient-API: er, ett MapReduce-jobb med TableOutputFormat eller mata in data manuellt med HBase-gränssnittet. Phoenix innehåller två metoder för att läsa in CSV-data till Phoenix-tabeller: ett klient inläsnings verktyg med namnet `psql` och ett MapReduce-baserat Mass inläsnings verktyg.

`psql`Verktyget är en enkel tråd och lämpar sig bäst för att läsa in megabyte eller flera gigabyte med data. Alla CSV-filer som ska läsas in måste ha fil namns tillägget. csv.  Du kan också ange SQL-skriptfiler i `psql` kommando raden med fil namns tillägget. SQL.

Mass inläsning med MapReduce används för mycket större data volymer, vanligt vis i produktions scenarier, eftersom MapReduce använder flera trådar.

Innan du börjar läsa in data kontrollerar du att Phoenix är aktiverat och att inställningarna för tids gräns för frågor är som förväntat.  Gå till ditt HDInsight-kluster [Apache Ambari](https://ambari.apache.org/) -instrumentpanelen, Välj HBase och sedan fliken konfiguration.  Rulla nedåt för att kontrol lera att Apache Phoenix är inställt på `enabled` som visas:

![Apache Phoenix inställningar för HDInsight-kluster](./media/apache-hbase-phoenix-psql/apache-ambari-phoenix.png)

### <a name="use-psql-to-bulk-load-tables"></a>Använd `psql` till Mass inläsnings tabeller

1. Skapa en fil med namnet `createCustomersTable.sql` och kopiera koden nedan till filen. Spara sedan och stäng filen.

    ```sql
    CREATE TABLE Customers (
        ID varchar NOT NULL PRIMARY KEY,
        Name varchar,
        Income decimal,
        Age INTEGER,
        Country varchar);
    ```

1. Skapa en fil med namnet `listCustomers.sql` och kopiera koden nedan till filen. Spara sedan och stäng filen.

    ```sql
    SELECT * from Customers;
    ```

1. Skapa en fil med namnet `customers.csv` och kopiera koden nedan till filen. Spara sedan och stäng filen.

    ```txt
    1,Samantha,260000.0,18,US
    2,Sam,10000.5,56,US
    3,Anton,550150.0,42,Norway
    ```

1. Skapa en fil med namnet `customers2.csv` och kopiera koden nedan till filen. Spara sedan och stäng filen.

    ```txt
    4,Nicolle,180000.0,22,US
    5,Kate,210000.5,24,Canada
    6,Ben,45000.0,32,Poland
    ```

1. Öppna en kommando tolk och ändra katalogen till platsen för de nyligen skapade filerna. Ersätt kluster namn nedan med det faktiska namnet på ditt HBase-kluster. Kör sedan koden för att ladda upp filerna till huvudnoden för klustret:

    ```cmd
    scp customers.csv customers2.csv createCustomersTable.sql listCustomers.sql sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/tmp
    ```

1. Använd [SSH-kommandot](../hdinsight-hadoop-linux-use-ssh-unix.md) för att ansluta till klustret. Redigera kommandot nedan genom att ersätta kluster namn med namnet på klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Ändra katalogen till platsen för **psql** -verktyget från SSH-sessionen. Kör kommandot nedan:

    ```bash
    cd /usr/hdp/current/phoenix-client/bin
    ```

1. Läs in data. Koden nedan skapar tabellen **kunder** och laddar sedan upp data.

    ```bash
    python psql.py /tmp/createCustomersTable.sql /tmp/customers.csv
    ```

    När `psql` åtgärden har slutförts bör du se ett meddelande som liknar följande:

    ```output
    csv columns from database.
    CSV Upsert complete. 3 rows upserted
    Time: 0.081 sec(s)
    ```

1. Du kan fortsätta att använda `psql` för att visa innehållet i tabellen kunder. Kör koden nedan:

    ```bash
    python psql.py /tmp/listCustomers.sql
    ```

    Du kan också använda [HBase Shell](./query-hbase-with-hbase-shell.md)eller [Apache Zeppelin](./apache-hbase-phoenix-zeppelin.md) för att fråga data.

1. Ladda upp ytterligare data. Nu när tabellen redan finns anger kommandot tabellen. Kör kommandot nedan:

    ```bash
    python psql.py -t CUSTOMERS /tmp/customers2.csv
    ```

## <a name="use-mapreduce-to-bulk-load-tables"></a>Använda MapReduce till Mass inläsnings tabeller

Använd inläsnings verktyget MapReduce för större data flöden som är distribuerade över klustret. Den här inläsaren konverterar först alla data till HFiles och ger sedan den skapade HFiles till HBase.

1. Det här avsnittet fortsätter med SSH-sessionen och objekt som skapats tidigare. Skapa tabellen **kunder** och **customers.csv** fil efter behov med hjälp av stegen ovan. Återupprätta ssh-anslutningen om det behövs.

1. Trunkera innehållet i tabellen **kunder** . Kör följande kommandon från din öppna SSH-session:

    ```bash
    hbase shell
    truncate 'CUSTOMERS'
    exit
    ```

1. Kopiera `customers.csv` filen från din huvudnoden till Azure Storage.

    ```bash
    hdfs dfs -put /tmp/customers.csv wasbs:///tmp/customers.csv
    ```

1. Ändra till körnings katalogen för MapReduce Mass inläsnings kommando:

    ```bash
    cd /usr/hdp/current/phoenix-client
    ```

1. Starta MapReduce-inläsaren genom att använda `hadoop` kommandot med Phoenix-klientens jar:

    ```bash
    HADOOP_CLASSPATH=/usr/hdp/current/hbase-client/lib/hbase-protocol.jar:/etc/hbase/conf hadoop jar phoenix-client.jar org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input /tmp/customers.csv
    ```

    När uppladdningen är klar bör du se ett meddelande som liknar följande:

    ```output
    19/12/18 18:30:57 INFO client.ConnectionManager$HConnectionImplementation: Closing master protocol: MasterService
    19/12/18 18:30:57 INFO client.ConnectionManager$HConnectionImplementation: Closing zookeeper sessionid=0x26f15dcceff02c3
    19/12/18 18:30:57 INFO zookeeper.ZooKeeper: Session: 0x26f15dcceff02c3 closed
    19/12/18 18:30:57 INFO zookeeper.ClientCnxn: EventThread shut down
    19/12/18 18:30:57 INFO mapreduce.AbstractBulkLoadTool: Incremental load complete for table=CUSTOMERS
    19/12/18 18:30:57 INFO mapreduce.AbstractBulkLoadTool: Removing output directory /tmp/50254426-aba6-400e-88eb-8086d3dddb6
    ```

1. Om du vill använda MapReduce med Azure Data Lake Storage letar du upp Data Lake Storage rot katalogen, vilket är `hbase.rootdir` värdet i `hbase-site.xml` . I följande kommando är Data Lake Storage rot katalogen `adl://hdinsightconf1.azuredatalakestore.net:443/hbase1` . I det här kommandot anger du Data Lake Storage in-och utdata-mappar som parametrar:

    ```bash
    cd /usr/hdp/current/phoenix-client

    $HADOOP_CLASSPATH=$(hbase mapredcp):/etc/hbase/conf hadoop jar /usr/hdp/2.4.2.0-258/phoenix/phoenix-4.4.0.2.4.2.0-258-client.jar

    org.apache.phoenix.mapreduce.CsvBulkLoadTool --table Customers --input adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/temp/input/customers.csv –zookeeper ZookeeperQuorum:2181:/hbase-unsecure --output  adl://hdinsightconf1.azuredatalakestore.net:443/hbase1/data/hbase/output1
    ```

1. Om du vill fråga efter och visa data kan du använda **psql** enligt beskrivningen ovan. Du kan också använda [HBase Shell](./query-hbase-with-hbase-shell.md)eller [Apache Zeppelin](./apache-hbase-phoenix-zeppelin.md).

## <a name="recommendations"></a>Rekommendationer

* Använd samma lagrings medium för både in-och utdata-mappar, antingen Azure Storage (WASB) eller Azure Data Lake Storage (ADL). Om du vill överföra data från Azure Storage till Data Lake Storage kan du använda `distcp` kommandot:

    ```bash
    hadoop distcp wasb://@.blob.core.windows.net/example/data/gutenberg adl://.azuredatalakestore.net:443/myfolder
    ```

* Använd större arbetsnoder i arbets storlek. Kart processerna för MapReduce Mass kopiering genererar stora mängder temporära utdata som fyller upp det tillgängliga icke-DFS-utrymmet. För en stor mängd Mass inläsning använder du fler och större arbetsnoder för arbetare. Antalet arbetsnoder som du allokerar till klustret påverkar direkt bearbetnings hastigheten.

* Dela in indatafiler i ~ 10 GB-segment. Mass inläsning är en lagrings intensiv åtgärd så att du kan dela in dina indatafiler i flera segment, vilket ger bättre prestanda.

* Undvik hotspots på regions servern. Om rad nyckeln är monotont ökning kan HBase sekventiella skrivningar orsaka region Server Hotspotting. När rad nyckeln *saltas* minskas sekventiella skrivningar. Phoenix är ett sätt att transparent salt rad nyckel med en saltad byte för en viss tabell enligt nedan.

## <a name="next-steps"></a>Nästa steg

* [Mass inläsning av data med Apache Phoenix](https://phoenix.apache.org/bulk_dataload.html)
* [Använda Apache Phoenix med Linux-baserade Apache HBase-kluster i HDInsight](../hbase/apache-hbase-query-with-phoenix.md)
* [Saltade tabeller](https://phoenix.apache.org/salted.html)
* [Apache Phoenix grammatik](https://phoenix.apache.org/language/index.html)
