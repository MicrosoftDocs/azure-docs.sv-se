---
title: Använda Spark för att läsa och skriva HBase-data – Azure HDInsight
description: Använd Spark HBase-anslutningen för att läsa och skriva data från ett Spark-kluster till ett HBase-kluster.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 08/12/2020
ms.openlocfilehash: 344caf4080380f5d9dfdaf452798ada6d1dc9f1c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98931217"
---
# <a name="use-apache-spark-to-read-and-write-apache-hbase-data"></a>Använda Apache Spark för att läsa och skriva Apache HBase-data

Apache HBase frågas vanligt vis antingen med dess lågnivå-API (genomsökningar, hämtar och placerar) eller med en SQL-syntax som använder Apache Phoenix. Apache tillhandahåller även Apache Spark HBase-anslutningsprogrammet. Anslutningen är ett bekvämt och effektivt alternativ för att fråga och ändra data som lagras av HBase.

## <a name="prerequisites"></a>Förutsättningar

* Två separata HDInsight-kluster distribuerade i samma [virtuella nätverk](./hdinsight-plan-virtual-network-deployment.md). En HBase och en spark med minst Spark 2,1 (HDInsight 3,6) installerad. Mer information finns i [skapa Linux-baserade kluster i HDInsight med hjälp av Azure Portal](hdinsight-hadoop-create-linux-clusters-portal.md).

* URI-schemat för klustrets primära lagring. Det här schemat är wasb://för Azure Blob Storage, `abfs://` för Azure Data Lake Storage Gen2 eller ADL://för Azure Data Lake Storage gen1. Om säker överföring har Aktiver ATS för Blob Storage är URI: n `wasbs://` .  Se även [säker överföring](../storage/common/storage-require-secure-transfer.md).

## <a name="overall-process"></a>Övergripande process

Den övergripande processen för att aktivera Spark-klustret för att fråga ditt HBase-kluster ser ut så här:

1. Förbered några exempel data i HBase.
2. Hämta hbase-site.xml-filen från HBase (/etc/HBase/conf) och placera en kopia av hbase-site.xml i din spark 2-installationsmapp (/etc/spark2/conf). (Valfritt: Använd skript som tillhandahålls av HDInsight-teamet för att automatisera den här processen)
4. Kör `spark-shell` referens för Spark HBase-anslutningen med dess maven-koordinater i `packages` alternativet.
5. Definiera en katalog som mappar schemat från Spark till HBase.
6. Interagera med HBase-data med antingen RDD-eller DataFrame-API: erna.

## <a name="prepare-sample-data-in-apache-hbase"></a>Förbereda exempel data i Apache HBase

I det här steget skapar du och fyller i en tabell i Apache HBase som du sedan kan fråga med Spark.

1. Använd `ssh` kommandot för att ansluta till ditt HBase-kluster. Redigera kommandot nedan genom att ersätta `HBASECLUSTER` med namnet på ditt HBase-kluster och ange sedan kommandot:

    ```cmd
    ssh sshuser@HBASECLUSTER-ssh.azurehdinsight.net
    ```

2. Använd `hbase shell` kommandot för att starta det interaktiva HBase-gränssnittet. Ange följande kommando i SSH-anslutningen:

    ```bash
    hbase shell
    ```

3. Använd `create` kommandot för att skapa en HBase-tabell med två kolumn serier. Ange följande kommando:

    ```hbase
    create 'Contacts', 'Personal', 'Office'
    ```

4. Använd `put` kommandot för att infoga värden i en angiven kolumn på en angiven rad i en viss tabell. Ange följande kommando:

    ```hbase
    put 'Contacts', '1000', 'Personal:Name', 'John Dole'
    put 'Contacts', '1000', 'Personal:Phone', '1-425-000-0001'
    put 'Contacts', '1000', 'Office:Phone', '1-425-000-0002'
    put 'Contacts', '1000', 'Office:Address', '1111 San Gabriel Dr.'
    put 'Contacts', '8396', 'Personal:Name', 'Calvin Raji'
    put 'Contacts', '8396', 'Personal:Phone', '230-555-0191'
    put 'Contacts', '8396', 'Office:Phone', '230-555-0191'
    put 'Contacts', '8396', 'Office:Address', '5415 San Gabriel Dr.'
    ```

5. Använd `exit` kommandot för att stoppa det interaktiva HBase-gränssnittet. Ange följande kommando:

    ```hbase
    exit
    ```
    
## <a name="run-scripts-to-set-up-connection-between-clusters"></a>Kör skript för att konfigurera anslutning mellan kluster

Om du vill konfigurera kommunikationen mellan kluster följer du stegen nedan för att köra två skript i klustren. Dessa skript automatiserar processen med fil kopiering som beskrivs i avsnittet "Konfigurera kommunikation manuellt" nedan. 

* Skriptet som du kör från HBase-klustret överför `hbase-site.xml` och HBase IP-mappnings information till standard lagrings utrymmet som är anslutet till ditt Spark-kluster. 
* Skriptet som du kör från Spark-klustret ställer in två cron-jobb för att köra två hjälp skript med jämna mellanrum:  
    1.  HBase cron-jobb – Ladda ned nya `hbase-site.xml` filer och HBase IP-mappning från Spark standard Storage-kontot till den lokala noden
    2.  Spark cron-jobb – kontrollerar om en spark-skalning inträffade och om klustret är säkert. I så fall, redigera `/etc/hosts` för att inkludera HBase-IP-mappning som lagras lokalt

__Obs__: innan du fortsätter måste du kontrol lera att du har lagt till Spark-klustrets lagrings konto till ditt HBase-kluster som sekundärt lagrings konto. Se till att skripten är i ordning enligt beskrivningen nedan.


1. Använd [skript åtgärd](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster) på ditt HBase-kluster för att tillämpa ändringarna med följande överväganden: 


    |Egenskap | Värde |
    |---|---|
    |Bash-skript-URI|`https://hdiconfigactions.blob.core.windows.net/hbasesparkconnectorscript/connector-hbase.sh`|
    |Node-typ (er)|Region|
    |Parametrar|`-s SECONDARYS_STORAGE_URL`|
    |Beständiga|ja|

    * `SECONDARYS_STORAGE_URL` är URL: en till standard lagrings platsen för Spark-sidan. Parameter exempel: `-s wasb://sparkcon-2020-08-03t18-17-37-853z@sparkconhdistorage.blob.core.windows.net`


2.  Använd skript åtgärd i Spark-klustret för att tillämpa ändringarna med följande överväganden:

    |Egenskap | Värde |
    |---|---|
    |Bash-skript-URI|`https://hdiconfigactions.blob.core.windows.net/hbasesparkconnectorscript/connector-spark.sh`|
    |Node-typ (er)|Head, Worker, Zookeeper|
    |Parametrar|`-s "SPARK-CRON-SCHEDULE"` (valfritt) `-h "HBASE-CRON-SCHEDULE"` valfritt|
    |Beständiga|ja|


    * Du kan ange hur ofta du vill att det här klustret ska kontrol leras automatiskt om uppdateringen. Standard:-s "*/1 * * * *"-h 0 (i det här exemplet körs Spark-cron varje minut, medan HBase-cron inte körs)
    * Eftersom HBase cron inte har kon figurer ATS som standard, måste du köra om skriptet när du gör en skalning till ditt HBase-kluster. Om ditt HBase-kluster skalas ofta kan du välja att konfigurera HBase cron-jobbet automatiskt. Exempel: `-h "*/30 * * * *"` konfigurerar skriptet för att utföra kontroller var 30: e minut. Detta kommer att köra HBase cron-schema regelbundet för att automatisera nedladdning av ny HBase-information på det gemensamma lagrings kontot till en lokal nod.
    
    

## <a name="set-up-communication-manually-optional-if-provided-script-in-above-step-fails"></a>Konfigurera kommunikation manuellt (valfritt, om det medföljande skriptet i ovanstående steg Miss lyckas)

__Obs:__ De här stegen måste utföras varje gång ett av klustren genomgår en skalnings aktivitet.

1. Kopiera hbase-site.xml från lokal lagring till roten av ditt Spark-klusters standard lagring.  Redigera kommandot nedan för att avspegla konfigurationen.  Sedan anger du kommandot från den öppna SSH-sessionen till HBase-klustret:

    | Syntax-värde | Nytt värde|
    |---|---|
    |[URI-schema](hdinsight-hadoop-linux-information.md#URI-and-scheme) | Ändra för att återspegla din lagring.  Syntaxen nedan är för Blob Storage med säker överföring aktiverat.|
    |`SPARK_STORAGE_CONTAINER`|Ersätt med namnet på den standard lagrings behållare som används för Spark-klustret.|
    |`SPARK_STORAGE_ACCOUNT`|Ersätt med det standard lagrings konto namn som används för Spark-klustret.|

    ```bash
    hdfs dfs -copyFromLocal /etc/hbase/conf/hbase-site.xml wasbs://SPARK_STORAGE_CONTAINER@SPARK_STORAGE_ACCOUNT.blob.core.windows.net/
    ```

2. Avsluta sedan ssh-anslutningen till ditt HBase-kluster.

    ```bash
    exit
    ```


3. Anslut till Head-noden i Spark-klustret med hjälp av SSH. Redigera kommandot nedan genom att ersätta `SPARKCLUSTER` med namnet på ditt Spark-kluster och ange sedan kommandot:

    ```cmd
    ssh sshuser@SPARKCLUSTER-ssh.azurehdinsight.net
    ```

4. Ange kommandot nedan för att kopiera `hbase-site.xml` från ditt Spark-klusters standard lagring till mappen Spark 2 i klustrets lokala lagrings plats:

    ```bash
    sudo hdfs dfs -copyToLocal /hbase-site.xml /etc/spark2/conf
    ```

## <a name="run-spark-shell-referencing-the-spark-hbase-connector"></a>Kör Spark-gränssnitt som refererar till Spark HBase-anslutningen

När du har slutfört föregående steg bör du kunna köra Spark-gränssnittet, som refererar till rätt version av Spark HBase Connector. För att hitta den senaste lämpliga Spark HBase Connector Core-versionen för ditt kluster scenario, se [SHC Core-lagringsplatsen](https://repo.hortonworks.com/content/groups/public/com/hortonworks/shc/shc-core/).

I följande tabell listas exempelvis två versioner och motsvarande kommandon som HDInsight-teamet använder för närvarande. Du kan använda samma versioner av klustren om versionerna av HBase och Spark är desamma som anges i tabellen. 


1. I den öppna SSH-sessionen till Spark-klustret anger du följande kommando för att starta ett Spark-gränssnitt:

    |Spark-version| HDI HBase-version  | SHC-version    |  Kommando  |
    | :-----------:| :----------: | :-----------: |:----------- |
    |      2.1    | HDI 3,6 (HBase 1,1) | 1.1.1-2.1-s_2.11    | `spark-shell --packages com.hortonworks:shc-core:1.1.1-2.1-s_2.11 --repositories https://repo.hortonworks.com/content/groups/public/` |
    |      2.4    | HDI 4,0 (HBase 2,0) | 1.1.0.3.1.2.2-1  | `spark-shell --packages com.hortonworks.shc:shc-core:1.1.0.3.1.2.2-1 --repositories http://repo.hortonworks.com/content/groups/public/` |

2. Behåll den här Spark Shell-instansen öppen och fortsätt att [definiera en katalog och fråga](#define-a-catalog-and-query). Om du inte hittar den jar v7 som motsvarar dina versioner i SHC Core-lagringsplatsen fortsätter du att läsa. 

Du kan bygga jar v7 direkt från GitHub-grenen [Spark-HBase-Connector](https://github.com/hortonworks-spark/shc) . Om du till exempel kör med Spark 2,3 och HBase 1,1 slutför du följande steg:

1. Klona lagringsplatsen:

    ```bash
    git clone https://github.com/hortonworks-spark/shc
    ```
    
2. Gå till grenen – 2,3:

    ```bash
    git checkout branch-2.3
    ```

3. Skapa från grenen (skapar en. jar-fil):

    ```bash
    mvn clean package -DskipTests
    ```
    
3. Kör följande kommando (Glöm inte att ändra det. jar-namn som motsvarar den jar-fil som du har skapat):

    ```bash
    spark-shell --jars <path to your jar>,/usr/hdp/current/hbase-client/lib/htrace-core-3.1.0-incubating.jar,/usr/hdp/current/hbase-client/lib/hbase-client.jar,/usr/hdp/current/hbase-client/lib/hbase-common.jar,/usr/hdp/current/hbase-client/lib/hbase-server.jar,/usr/hdp/current/hbase-client/lib/hbase-protocol.jar,/usr/hdp/current/hbase-client/lib/htrace-core-3.1.0-incubating.jar
    ```
    
4. Behåll den här Spark Shell-instansen öppen och fortsätt till nästa avsnitt. 



## <a name="define-a-catalog-and-query"></a>Definiera en katalog och fråga

I det här steget definierar du ett katalog objekt som mappar schemat från Apache Spark till Apache HBase.  

1. I ditt öppna Spark-gränssnitt anger du följande `import` instruktioner:

    ```scala
    import org.apache.spark.sql.{SQLContext, _}
    import org.apache.spark.sql.execution.datasources.hbase._
    import org.apache.spark.{SparkConf, SparkContext}
    import spark.sqlContext.implicits._
    ```  

1. Ange kommandot nedan för att definiera en katalog för den kontakt tabell som du skapade i HBase:

    ```scala
    def catalog = s"""{
        |"table":{"namespace":"default", "name":"Contacts"},
        |"rowkey":"key",
        |"columns":{
        |"rowkey":{"cf":"rowkey", "col":"key", "type":"string"},
        |"officeAddress":{"cf":"Office", "col":"Address", "type":"string"},
        |"officePhone":{"cf":"Office", "col":"Phone", "type":"string"},
        |"personalName":{"cf":"Personal", "col":"Name", "type":"string"},
        |"personalPhone":{"cf":"Personal", "col":"Phone", "type":"string"}
        |}
    |}""".stripMargin
    ```

    Koden:  

    1. Definierar ett katalog schema för HBase-tabellen med namnet `Contacts` .  
    1. Identifierar rowkey som `key` och mappar kolumn namnen som används i Spark till kolumn serien, kolumn namnet och kolumn typen som används i HBase.  
    1. Definierar rowkey i detalj som en namngiven kolumn ( `rowkey` ) som har en speciell kolumn familj `cf` av `rowkey` .  

1. Ange kommandot nedan för att definiera en metod som tillhandahåller en DataFrame runt din `Contacts` tabell i HBase:

    ```scala
    def withCatalog(cat: String): DataFrame = {
        spark.sqlContext
        .read
        .options(Map(HBaseTableCatalog.tableCatalog->cat))
        .format("org.apache.spark.sql.execution.datasources.hbase")
        .load()
     }
    ```

1. Skapa en instans av DataFrame:

    ```scala
    val df = withCatalog(catalog)
    ```  

1. Fråga DataFrame:

    ```scala
    df.show()
    ```

    Du bör se två rader med data:

    ```output
    +------+--------------------+--------------+-------------+--------------+
    |rowkey|       officeAddress|   officePhone| personalName| personalPhone|
    +------+--------------------+--------------+-------------+--------------+
    |  1000|1111 San Gabriel Dr.|1-425-000-0002|    John Dole|1-425-000-0001|
    |  8396|5415 San Gabriel Dr.|  230-555-0191|  Calvin Raji|  230-555-0191|
    +------+--------------------+--------------+-------------+--------------+
    ```

1. Registrera en temporär tabell så att du kan fråga HBase-tabellen med Spark SQL:

    ```scala
    df.createTempView("contacts")
    ```

1. Utfärda en SQL-fråga mot `contacts` tabellen:

    ```scala
    spark.sqlContext.sql("select personalName, officeAddress from contacts").show
    ```

    Du bör se resultatet så här:

    ```output
    +-------------+--------------------+
    | personalName|       officeAddress|
    +-------------+--------------------+
    |    John Dole|1111 San Gabriel Dr.|
    |  Calvin Raji|5415 San Gabriel Dr.|
    +-------------+--------------------+
    ```

## <a name="insert-new-data"></a>Infoga nya data

1. Om du vill infoga en ny kontakt post definierar du en `ContactRecord` klass:

    ```scala
    case class ContactRecord(
        rowkey: String,
        officeAddress: String,
        officePhone: String,
        personalName: String,
        personalPhone: String
        )
    ```

1. Skapa en instans av `ContactRecord` och Lägg den i en matris:

    ```scala
    val newContact = ContactRecord("16891", "40 Ellis St.", "674-555-0110", "John Jackson","230-555-0194")

    var newData = new Array[ContactRecord](1)
    newData(0) = newContact
    ```

1. Spara matrisen med nya data till HBase:

    ```scala
    sc.parallelize(newData).toDF.write.options(Map(HBaseTableCatalog.tableCatalog -> catalog, HBaseTableCatalog.newTable -> "5")).format("org.apache.spark.sql.execution.datasources.hbase").save()
    ```

1. Granska resultaten:

    ```scala  
    df.show()
    ```

    Du bör se utdata som ser ut så här:

    ```output
    +------+--------------------+--------------+------------+--------------+
    |rowkey|       officeAddress|   officePhone|personalName| personalPhone|
    +------+--------------------+--------------+------------+--------------+
    |  1000|1111 San Gabriel Dr.|1-425-000-0002|   John Dole|1-425-000-0001|
    | 16891|        40 Ellis St.|  674-555-0110|John Jackson|  230-555-0194|
    |  8396|5415 San Gabriel Dr.|  230-555-0191| Calvin Raji|  230-555-0191|
    +------+--------------------+--------------+------------+--------------+
    ```

1. Stäng Spark-gränssnittet genom att ange följande kommando:

    ```scala
    :q
    ```

## <a name="next-steps"></a>Nästa steg

* [Apache Spark HBase-anslutning](https://github.com/hortonworks-spark/shc)
