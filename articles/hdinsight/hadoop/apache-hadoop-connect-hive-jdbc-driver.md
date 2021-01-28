---
title: Fråga Apache Hive genom JDBC-drivrutinen – Azure HDInsight
description: Använd JDBC-drivrutinen från ett Java-program för att skicka Apache Hive frågor till Hadoop på HDInsight. Anslut program mässigt och från SQuirrel SQL-klienten.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: d23b376384262c208fed70306e62634592d0b46b
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98946765"
---
# <a name="query-apache-hive-through-the-jdbc-driver-in-hdinsight"></a>Fråga Apache Hive via JDBC-drivrutinen i HDInsight

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Lär dig hur du använder JDBC-drivrutinen från ett Java-program. För att skicka Apache Hive frågor till Apache Hadoop i Azure HDInsight. Informationen i det här dokumentet visar hur du ansluter program mässigt och från SQuirreL SQL-klienten.

Mer information om Hive JDBC-gränssnittet finns i [HiveJDBCInterface](https://cwiki.apache.org/confluence/display/Hive/HiveJDBCInterface).

## <a name="prerequisites"></a>Förutsättningar

* An-HDInsight Hadoop-kluster. Information om hur du skapar ett finns i [Kom igång med Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md). Se till att tjänstens HiveServer2 körs.
* [Java Developer Kit (JDK) version 11](https://www.oracle.com/technetwork/java/javase/downloads/jdk11-downloads-5066655.html) eller senare.
* [SQUIRREL SQL](http://squirrel-sql.sourceforge.net/). SQuirreL är ett JDBC-klientprogram.

## <a name="jdbc-connection-string"></a>JDBC-anslutningssträng

JDBC-anslutningar till ett HDInsight-kluster på Azure görs via port 443. Trafiken skyddas med TLS/SSL. Den offentliga gateway som klustren är bakom omdirigerar trafiken till den port som HiveServer2 faktiskt lyssnar på. Följande anslutnings sträng visar det format som ska användas för HDInsight:

```http
    jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2
```

Ersätt `CLUSTERNAME` med namnet på HDInsight-klustret.

Du kan också få anslutningen via **AMBARI UI > Hive > config > Avancerat**.

![Hämta JDBC-anslutningssträng via Ambari](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-get-connection-string-through-ambari.png)

### <a name="host-name-in-connection-string"></a>Värd namnet i anslutnings strängen

Värd namnet ' CLUSTERNAME.azurehdinsight.net ' i anslutnings strängen är detsamma som din kluster-URL. Du kan hämta det genom att Azure Portal.

### <a name="port-in-connection-string"></a>Port i anslutnings sträng

Du kan bara använda **port 443** för att ansluta till klustret från några platser utanför det virtuella Azure-nätverket. HDInsight är en hanterad tjänst, vilket innebär att alla anslutningar till klustret hanteras via en säker gateway. Du kan inte ansluta till HiveServer 2 direkt på portarna 10001 eller 10000. Portarna visas inte utanför.

## <a name="authentication"></a>Autentisering

När du upprättar anslutningen använder du HDInsight-klustrets administratörs namn och lösen ord för att autentisera. Från JDBC-klienter som SQuirreL SQL anger du administratörs namn och lösen ord i klient inställningar.

Från ett Java-program måste du använda namnet och lösen ordet när du upprättar en anslutning. Följande Java-kod öppnar till exempel en ny anslutning:

```java
DriverManager.getConnection(connectionString,clusterAdmin,clusterPassword);
```

## <a name="connect-with-squirrel-sql-client"></a>Anslut med SQuirreL SQL-klient

SQuirreL SQL är en JDBC-klient som kan användas för att fjärrköra Hive-frågor med ditt HDInsight-kluster. Följande steg förutsätter att du redan har installerat SQuirreL SQL.

1. Skapa en katalog som innehåller vissa filer som ska kopieras från klustret.

2. Ersätt `sshuser` med namnet på SSH-användarnamnet för klustret i följande skript.  Ersätt `CLUSTERNAME` med HDInsight-klustrets namn.  Ändra arbets katalogen till den som skapades i föregående steg från en kommando rad och ange sedan följande kommando för att kopiera filer från ett HDInsight-kluster:

    ```cmd
    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hadoop-client/{hadoop-auth.jar,hadoop-common.jar,lib/log4j-*.jar,lib/slf4j-*.jar,lib/curator-*.jar} .

    scp sshuser@CLUSTERNAME-ssh.azurehdinsight.net:/usr/hdp/current/hive-client/lib/{commons-codec*.jar,commons-logging-*.jar,hive-*-*.jar,httpclient-*.jar,httpcore-*.jar,libfb*.jar,libthrift-*.jar} .
    ```

3. Starta SQuirreL SQL-programmet. Välj **driv rutiner** från vänster i fönstret.

    ![Fliken driv rutiner till vänster i fönstret](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-squirreldrivers.png)

4. Från ikonerna överst i dialog rutan **driv rutiner** väljer du **+** ikonen för att skapa en driv rutin.

    ![Ikonen SQuirreL för SQL-programdriv rutiner](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-driversicons.png)

5. I dialog rutan Lägg till driv rutin lägger du till följande information:

    |Egenskap | Värde |
    |---|---|
    |Namn|Hive|
    |Exempel-URL|`jdbc:hive2://localhost:443/default;transportMode=http;ssl=true;httpPath=/hive2`|
    |Extra klass Sök väg|Använd knappen **Lägg** till för att lägga till alla jar-filer som hämtats tidigare.|
    |Klassnamn|org. apache. Hive. JDBC. HiveDriver|

   ![dialog rutan Lägg till driv rutin med parametrar](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-add-driver.png)

   Välj **OK** för att spara inställningarna.

6. Till vänster om SQL-fönstret SQuirreL väljer du **alias**. Välj sedan **+** ikonen för att skapa ett alias för anslutning.

    ![SQuirreL SQL Lägg till nytt alias dialog ruta](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-new-aliases.png)

7. Använd följande värden för dialog rutan **Lägg till alias** :

    |Egenskap |Värde |
    |---|---|
    |Namn|Hive i HDInsight|
    |Drivrutinen|Använd List rutan för att välja **Hive** -drivrutinen.|
    |URL|`jdbc:hive2://CLUSTERNAME.azurehdinsight.net:443/default;transportMode=http;ssl=true;httpPath=/hive2`. Ersätt **KLUSTERNAMN** med namnet på ditt HDInsight-kluster.|
    |Användarnamn|Kluster inloggnings konto namnet för ditt HDInsight-kluster. Standardvärdet är **admin**.|
    |Lösenord|Lösen ordet för klustrets inloggnings konto.|

    ![dialog rutan Lägg till alias med parametrar](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-addalias-dialog.png)

    > [!IMPORTANT]
    > Använd **test** knappen för att kontrol lera att anslutningen fungerar. När dialog rutan **Anslut till: Hive i HDInsight** visas väljer du **Anslut** för att utföra testet. Om testet lyckas visas en dialog ruta med en **anslutning** . Om ett fel inträffar, se [fel sökning](#troubleshooting).

    Om du vill spara anslutnings Ali Aset använder du knappen **OK** längst ned i dialog rutan **Lägg till alias** .

8. I list rutan **Anslut till** överst i SQuirreL SQL väljer du **Hive i HDInsight**. När du uppmanas väljer du **Anslut**.

    ![dialog rutan anslutning med parametrar](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-connect-dialog.png)

9. När du är ansluten, ange följande fråga i dialog rutan SQL-fråga och välj sedan ikonen **Kör** (en person som körs). Resultat ytan bör visa resultatet av frågan.

    ```hiveql
    select * from hivesampletable limit 10;
    ```

    ![dialog rutan SQL-fråga, inklusive resultat](./media/apache-hadoop-connect-hive-jdbc-driver/hdinsight-sqlquery-dialog.png)

## <a name="connect-from-an-example-java-application"></a>Ansluta från ett Java-exempel till ett Java-program

Ett exempel på att använda en Java-klient för att fråga Hive i HDInsight finns på [https://github.com/Azure-Samples/hdinsight-java-hive-jdbc](https://github.com/Azure-Samples/hdinsight-java-hive-jdbc) . Följ instruktionerna i lagrings platsen för att skapa och köra exemplet.

## <a name="troubleshooting"></a>Felsökning

### <a name="unexpected-error-occurred-attempting-to-open-an-sql-connection"></a>Ett oväntat fel uppstod vid försök att öppna en SQL-anslutning

**Symptom**: när du ansluter till ett HDInsight-kluster som är version 3,3 eller senare kan du få ett fel meddelande om att ett oväntat fel har uppstått. Stack spårningen för det här felet börjar med följande rader:

```java
java.util.concurrent.ExecutionException: java.lang.RuntimeException: java.lang.NoSuchMethodError: org.apache.commons.codec.binary.Base64.<init>(I)V
at java.util.concurrent.FutureTas...(FutureTask.java:122)
at java.util.concurrent.FutureTask.get(FutureTask.java:206)
```

**Orsak**: det här felet orsakas av en äldre version Commons-codec. jar-fil som ingår i SQuirreL.

**Lösning**: åtgärda det här felet med hjälp av följande steg:

1. Avsluta SQuirreL och gå sedan till den katalog där SQuirreL är installerat på datorn, kanske `C:\Program Files\squirrel-sql-4.0.0\lib` . I katalogen SquirreL, under `lib` katalogen, ersätter du den befintliga Commons-codec. jar med den som laddats ned från HDInsight-klustret.

1. Starta om SQuirreL. Felet bör inte längre uppstå när du ansluter till Hive i HDInsight.

### <a name="connection-disconnected-by-hdinsight"></a>Anslutningen kopplades från HDInsight

**Symptom**: när du försöker hämta enorma mängder data (t. ex. flera GB) via JDBC/ODBC, kopplas anslutningen inte från HDInsight av HDInsight vid hämtning.

**Orsak**: det här felet orsakas av begränsningen på Gateway-noder. När data hämtas från JDBC/ODBC måste alla data släppas genom Gateway-noden. En gateway är dock inte utformad för att ladda ned en stor mängd data, så gatewayen kan stänga anslutningen om den inte kan hantera trafiken.

**Lösning**: Undvik att använda JDBC/ODBC-drivrutinen för att hämta stora mängder data. Kopiera data direkt från Blob Storage i stället.

## <a name="next-steps"></a>Nästa steg

Nu när du har lärt dig hur du använder JDBC för att arbeta med Hive kan du använda följande länkar för att utforska andra sätt att arbeta med Azure HDInsight.

* [Visualisera Apache Hive data med Microsoft Power BI i Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Visualisera interaktiva Hive-frågedata med Power BI i Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Anslut Excel till HDInsight med Microsoft HIVE ODBC-drivrutinen](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Anslut Excel till Apache Hadoop med hjälp av Power Query](apache-hadoop-connect-excel-power-query.md).
* [Använda Apache Hive med HDInsight](hdinsight-use-hive.md)
* [Använda Apache Hive med HDInsight](../use-pig.md)
* [Använda MapReduce-jobb med HDInsight](hdinsight-use-mapreduce.md)
