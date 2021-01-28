---
title: Apache Spark åtgärder som stöds av Hive Warehouse Connector i Azure HDInsight
description: Lär dig mer om de olika funktionerna i Hive Warehouse Connector i Azure HDInsight.
author: nis-goel
ms.author: nisgoel
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/22/2020
ms.openlocfilehash: 20567a1e38686b5d452a5353bc459e7e1125f499
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98941297"
---
# <a name="apache-spark-operations-supported-by-hive-warehouse-connector-in-azure-hdinsight"></a>Apache Spark åtgärder som stöds av Hive Warehouse Connector i Azure HDInsight

Den här artikeln visar Spark-baserade åtgärder som stöds av Hive Warehouse Connector (INSTANSEN). Alla exempel som visas nedan kommer att köras via Apache Spark Shell.

## <a name="prerequisite"></a>Förutsättning

Slutför installations stegen för [Hive-distributions lager kopplingen](./apache-hive-warehouse-connector.md#hive-warehouse-connector-setup) .

## <a name="getting-started"></a>Komma igång

Gör så här för att starta en spark-Shell-session:

1. Använd [SSH-kommandot](../hdinsight-hadoop-linux-use-ssh-unix.md) för att ansluta till ditt Apache Spark-kluster. Redigera kommandot nedan genom att ersätta kluster namn med namnet på klustret och ange sedan kommandot:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. Kör följande kommando från SSH-sessionen för att anteckna `hive-warehouse-connector-assembly` versionen:

    ```bash
    ls /usr/hdp/current/hive_warehouse_connector
    ```

1. Redigera koden nedan med den `hive-warehouse-connector-assembly` version som identifieras ovan. Kör sedan kommandot för att starta Spark-gränssnittet:

    ```bash
    spark-shell --master yarn \
    --jars /usr/hdp/current/hive_warehouse_connector/hive-warehouse-connector-assembly-<STACK_VERSION>.jar \
    --conf spark.security.credentials.hiveserver2.enabled=false
    ```

1. När du har startat Spark-Shell kan du starta en Hive-instans med hjälp av följande kommandon:

    ```scala
    import com.hortonworks.hwc.HiveWarehouseSession
    val hive = HiveWarehouseSession.session(spark).build()
    ```

## <a name="creating-spark-dataframes-using-hive-queries"></a>Skapa Spark-DataFrames med hjälp av Hive-frågor

Resultaten av alla frågor som använder INSTANSEN-biblioteket returneras som en DataFrame. Följande exempel visar hur du skapar en grundläggande Hive-fråga.

```scala
hive.setDatabase("default")
val df = hive.executeQuery("select * from hivesampletable")
df.filter("state = 'Colorado'").show()
```

Resultatet av frågan är Spark-DataFrames, som kan användas med Spark-bibliotek som MLIB och SparkSQL.

## <a name="writing-out-spark-dataframes-to-hive-tables"></a>Skriva ut Spark-DataFrames till Hive-tabeller

Spark stöder inte internt skrivning till Hive-tabeller med hanterade syror. Med hjälp av INSTANSEN kan du dock skriva ut valfri DataFrame till en Hive-tabell. Du kan se den här funktionen på jobbet i följande exempel:

1. Skapa en tabell `sampletable_colorado` med namnet och ange dess kolumner med följande kommando:

    ```scala
    hive.createTable("sampletable_colorado").column("clientid","string").column("querytime","string").column("market","string").column("deviceplatform","string").column("devicemake","string").column("devicemodel","string").column("state","string").column("country","string").column("querydwelltime","double").column("sessionid","bigint").column("sessionpagevieworder","bigint").create()
    ```

1. Filtrera tabellen `hivesampletable` där kolumnen `state` är lika med `Colorado` . Den här Hive-frågan returnerar en spark-DataFrame ans SIS som sparats i Hive-tabellen `sampletable_colorado` med `write` funktionen.

    ```scala
    hive.table("hivesampletable").filter("state = 'Colorado'").write.format("com.hortonworks.spark.sql.hive.llap.HiveWarehouseConnector").mode("append").option("table","sampletable_colorado").save()
    ```

1. Visa resultaten med följande kommando:

    ```scala
    hive.table("sampletable_colorado").show()
    ```
    
    ![Hive-dist. lager koppling visar Hive-tabell](./media/apache-hive-warehouse-connector/hive-warehouse-connector-show-hive-table.png)


## <a name="structured-streaming-writes"></a>Strukturerade strömmande skrivningar

Med hjälp av Hive Warehouse Connector kan du använda Spark-direktuppspelning för att skriva data till Hive-tabeller.

> [!IMPORTANT]
> Strukturerade strömnings skrivningar stöds inte i ESP-aktiverade Spark 4,0-kluster.

Följ stegen nedan för att mata in data från en spark-dataström på localhost-port 9999 i en Hive-tabell via. Hive lager koppling.

1. Starta en spark-ström från ditt öppna Spark-gränssnitt med följande kommando:

    ```scala
    val lines = spark.readStream.format("socket").option("host", "localhost").option("port",9999).load()
    ```

1. Generera data för Spark-dataströmmen som du skapade genom att utföra följande steg:
    1. Öppna en andra SSH-session i samma Spark-kluster.
    1. Skriv i kommando tolken `nc -lk 9999` . Det här kommandot använder verktyget netcat för att skicka data från kommando raden till den angivna porten.

1. Gå tillbaka till den första SSH-sessionen och skapa en ny Hive-tabell för att lagra strömmande data. I Spark-Shell anger du följande kommando:

    ```scala
    hive.createTable("stream_table").column("value","string").create()
    ```

1. Skriv sedan strömmande data till den nyligen skapade tabellen med hjälp av följande kommando:

    ```scala
    lines.filter("value = 'HiveSpark'").writeStream.format("com.hortonworks.spark.sql.hive.llap.streaming.HiveStreamingDataSource").option("database", "default").option("table","stream_table").option("metastoreUri",spark.conf.get("spark.datasource.hive.warehouse.metastoreUri")).option("checkpointLocation","/tmp/checkpoint1").start()
    ```

    >[!Important]
    > `metastoreUri`Alternativen och `database` måste anges manuellt på grund av ett känt problem i Apache Spark. Mer information om det här problemet finns i [Spark-25460](https://issues.apache.org/jira/browse/SPARK-25460).

1. Gå tillbaka till den andra SSH-sessionen och ange följande värden:

    ```bash
    foo
    HiveSpark
    bar
    ```

1. Gå tillbaka till den första SSH-sessionen och anteckna den korta aktiviteten. Använd följande kommando för att visa data:

    ```scala
    hive.table("stream_table").show()
    ```

Använd **CTRL + C** för att stoppa netcat på den andra SSH-sessionen. Används `:q` för att avsluta Spark-Shell på den första SSH-sessionen.

## <a name="next-steps"></a>Nästa steg

* [HWC-integrering med Apache Spark och Apache Hive](./apache-hive-warehouse-connector.md)
* [Använda Interactive Query i HDInsight](./apache-interactive-query-get-started.md)
* [HWC-integrering med Apache Zeppelin](./apache-hive-warehouse-connector-zeppelin.md)
