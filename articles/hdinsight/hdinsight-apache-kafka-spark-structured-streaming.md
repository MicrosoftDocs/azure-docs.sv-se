---
title: 'Självstudie: Apache Spark streaming & Apache Kafka – Azure HDInsight'
description: Lär dig använda Apache Spark-strömning till att hämta data till eller från Apache Kafka. I den här självstudien strömmar du data med hjälp av en Jupyter Notebook från Spark på HDInsight.
ms.service: hdinsight
ms.topic: tutorial
ms.custom: hdinsightactive,seodec18,seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: d352fc86f4451f60951b84929ad5d9450c1003d6
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942792"
---
# <a name="tutorial-use-apache-spark-structured-streaming-with-apache-kafka-on-hdinsight"></a>Självstudie: Använda Apache Spark Structured Streaming med Apache Kafka i HDInsight

Den här självstudien visar hur du använder [Apache Spark Structured Streaming](https://spark.apache.org/docs/latest/structured-streaming-programming-guide) till att läsa och skriva data med [Apache Kafka](./kafka/apache-kafka-introduction.md) i Azure HDInsight.

Spark-strukturerad strömning är en data Ströms bearbetnings motor som bygger på Spark SQL. Med den kan du uttrycka strömmande beräkningar på samma sätt som batchberäkningar av statiska data.  

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Använd en Azure Resource Manager mall för att skapa kluster
> * Använda Spark-strukturerad strömning med Kafka

När du är klar med stegen i det här dokumentet måste du komma ihåg att ta bort klustren för att undvika överskotts kostnader.

## <a name="prerequisites"></a>Förutsättningar

* JQ, en JSON-processor med kommando rad.  Se [https://stedolan.github.io/jq/](https://stedolan.github.io/jq/) .

* Kunskaper om [Jupyter Notebooks](https://jupyter.org/) med Spark på HDInsight. Mer information finns i dokumentet [Läsa in data och köra frågor med Apache Spark på HDInsight](spark/apache-spark-load-data-run-query.md).

* Kunskaper i programmeringsspråket Scala. Koden som används i den här självstudien är skriven i Scala.

* Om du vet hur man skapar Kafka-avsnitt. Mer information finns i dokumentet [Snabbstart för Apache Kafka i HDInsight](kafka/apache-kafka-get-started.md).

> [!IMPORTANT]  
> Stegen i det här dokumentet kräver en Azure-resursgrupp som innehåller både en Apache Spark på HDInsight och en Kafka på HDInsight-klustret. Båda dessa kluster finns i ett virtuellt Azure-nätverk, vilket innebär att Apache Spark-klustret kan kommunicera direkt med Kafka-klustret.
>
> Dokumentet innehåller länkar till en mall som kan skapa alla nödvändiga Azure-resurser.
>
> Mer information om hur du använder HDInsight i ett virtuellt nätverk finns i [planen ett virtuellt nätverk för HDInsight](hdinsight-plan-virtual-network-deployment.md) -dokument.

## <a name="structured-streaming-with-apache-kafka"></a>Structured Streaming med Apache Kafka

Apache Spark Structured Streaming är en bearbetningsmotor för dataströmmar som bygger på Apache Spark SQL-motorn. När du använder strukturerad strömning kan du skriva direkt uppspelnings frågor på samma sätt som du skriver batch-frågor.

Följande kodfragment visar läsning från Kafka och lagring på en fil. Den första är en batchåtgärd och den andra är en strömningsåtgärd:

```scala
// Read a batch from Kafka
val kafkaDF = spark.read.format("kafka")
                .option("kafka.bootstrap.servers", kafkaBrokers)
                .option("subscribe", kafkaTopic)
                .option("startingOffsets", "earliest")
                .load()

// Select data and write to file
kafkaDF.select(from_json(col("value").cast("string"), schema) as "trip")
                .write
                .format("parquet")
                .option("path","/example/batchtripdata")
                .option("checkpointLocation", "/batchcheckpoint")
                .save()
```

```scala
// Stream from Kafka
val kafkaStreamDF = spark.readStream.format("kafka")
                .option("kafka.bootstrap.servers", kafkaBrokers)
                .option("subscribe", kafkaTopic)
                .option("startingOffsets", "earliest")
                .load()

// Select data from the stream and write to file
kafkaStreamDF.select(from_json(col("value").cast("string"), schema) as "trip")
                .writeStream
                .format("parquet")
                .option("path","/example/streamingtripdata")
                .option("checkpointLocation", "/streamcheckpoint")
                .start.awaitTermination(30000)
```

I båda kodfragmenten läses data från Kafka och skrivs till en fil. Skillnader mellan exemplen är:

| Batch | Strömning |
| --- | --- |
| `read` | `readStream` |
| `write` | `writeStream` |
| `save` | `start` |

Streaming-åtgärden använder också `awaitTermination(30000)` , vilket stoppar strömmen efter 30 000 MS.

Om du vill använda Structured Streaming med Kafka måste ditt projekt ha ett beroende på paketet `org.apache.spark : spark-sql-kafka-0-10_2.11`. Versionen av det här paketet ska överensstämma med version på Spark på HDInsight. För Spark-2.2.0 (tillgängligt i HDInsight 3,6) kan du hitta beroende informationen för olika projekt typer på [https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar](https://search.maven.org/#artifactdetails%7Corg.apache.spark%7Cspark-sql-kafka-0-10_2.11%7C2.2.0%7Cjar) .

I den Jupyter Notebook som används i den här självstudien laddar följande cell detta paket beroende:

```
%%configure -f
{
    "conf": {
        "spark.jars.packages": "org.apache.spark:spark-sql-kafka-0-10_2.11:2.2.0",
        "spark.jars.excludes": "org.scala-lang:scala-reflect,org.apache.spark:spark-tags_2.11"
    }
}
```

## <a name="create-the-clusters"></a>Skapa kluster

Apache Kafka i HDInsight ger inte åtkomst till Kafka-utjämnare via det offentliga Internet. Allt som använder Kafka måste finnas i samma virtuella Azure-nätverk. I den här självstudien finns både Kafka- och Apache Spark-klustren i samma virtuella Azure-nätverk.

Följande diagram visar hur kommunikation flödar mellan Apache Spark och Kafka:

![Diagram över Apache Spark- och Kafka-kluster i ett virtuellt Azure-nätverk](./media/hdinsight-apache-kafka-spark-structured-streaming/apache-spark-kafka-vnet.png)

> [!NOTE]  
> Kafka-tjänsten är begränsad till kommunikation inom det virtuella nätverket. Andra tjänster på klustret, till exempel SSH och Ambari, kan nås via Internet. Mer information om de offentliga portar som är tillgängliga med HDInsight finns i [Portar och URI:er som används av HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Om du vill skapa ett Azure Virtual Network och sedan skapa Kafka- och Spark-kluster i den, använder du följande steg:

1. Använd följande knapp för att logga in på Azure och öppna mallen i Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-kafka-structured-streaming%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-kafka-spark-structured-streaming/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Mallen Azure Resource Manager finns på **https://raw.githubusercontent.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming/master/azuredeploy.json** .

    Den här mallen skapar följande resurser:

   * En Kafka på HDInsight 3.6-klustret.
   * En Apache Spark 2.2.0 på HDInsight 3.6-klustret.
   * Ett virtuellt Azure-nätverk som innehåller HDInsight-klustren.

     > [!IMPORTANT]  
     > Anteckningsboken för Structured Streaming som används i den här självstudien kräver Apache Spark 2.2.0 i HDInsight 3.6. Om du använder en tidigare version av Apache Spark i HDInsight får du ett felmeddelande när du använder anteckningsboken.

2. Använd följande information för att fylla i posterna i avsnittet **Anpassad mall**:

    | Inställning | Värde |
    | --- | --- |
    | Prenumeration | Din Azure-prenumeration |
    | Resursgrupp | Resursgruppen som innehåller resurserna. |
    | Location | Azure-regionen som resurserna skapas i. |
    | Apache Spark-klusternamn | Namnet på Apache Spark-klustret. De första sex tecknen får inte vara samma som Kafka-klusternamnet. |
    | Kafka-klusternamn | Namnet på Kafka-klustret. De första sex tecknen får inte vara samma som Spark-klusternamnet. |
    | Användarnamn för klusterinloggning | Ett administratörsanvändarnamn för klustren. |
    | Lösenord för klusterinloggning | Ett administratörslösenord för klustren. |
    | SSH-användarnamn | SSH-användare som ska skapas för klustren. |
    | SSH-lösenord | Lösenord för SSH-användaren. |

    ![Skärmbild av den anpassade mallen](./media/hdinsight-apache-kafka-spark-structured-streaming/spark-kafka-template.png)

3. Läs de **allmänna** villkoren och välj sedan **Jag godkänner villkoren som anges ovan**.

4. Välj **Köp**.

> [!NOTE]  
> Det kan ta upp till 20 minuter att skapa klustren.

## <a name="use-spark-structured-streaming"></a>Använda Spark-strukturerad strömning

Det här exemplet visar hur du använder Spark Structured streaming med Kafka på HDInsight. Den använder data i taxi resor, som tillhandahålls av New York City.  Data uppsättningen som används av den här antecknings boken är från [2016 grön taxi rese data](https://data.cityofnewyork.us/Transportation/2016-Green-Taxi-Trip-Data/hvrh-b6nb).

1. Samla in information om värden. Använd kommandona för vändning och [JQ](https://stedolan.github.io/jq/) nedan för att hämta information om Kafka-ZooKeeper och Broker-värdar. Kommandona är utformade för en kommando tolk i Windows, små variationer krävs för andra miljöer. Ersätt `KafkaCluster` med namnet på ditt Kafka-kluster och `KafkaPassword` med lösen ordet för kluster inloggning. Ersätt också `C:\HDI\jq-win64.exe` med den faktiska sökvägen till din JQ-installation. Ange kommandona i kommando tolken i Windows och spara utdata för användning i senare steg.

    ```cmd
    REM Enter cluster name in lowercase

    set CLUSTERNAME=KafkaCluster
    set PASSWORD=KafkaPassword

    curl -u admin:%PASSWORD% -G "https://%CLUSTERNAME%.azurehdinsight.net/api/v1/clusters/%CLUSTERNAME%/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | C:\HDI\jq-win64.exe -r "["""\(.host_components[].HostRoles.host_name):2181"""] | join(""",""")"

    curl -u admin:%PASSWORD% -G "https://%CLUSTERNAME%.azurehdinsight.net/api/v1/clusters/%CLUSTERNAME%/services/KAFKA/components/KAFKA_BROKER" | C:\HDI\jq-win64.exe -r "["""\(.host_components[].HostRoles.host_name):9092"""] | join(""",""")"
    ```

1. I en webbläsare går du till `https://CLUSTERNAME.azurehdinsight.net/jupyter` , där `CLUSTERNAME` är namnet på klustret. Ange klusterinloggningen (administratör) och det lösenord som användes när du skapade klustret.

1. Välj **ny > Spark** för att skapa en antecknings bok.

1. Spark streaming har mikrobatchering, vilket innebär att data kommer som batchar och körningar som körs i batchar med data. Om utförar har en tids gräns för inaktivitet som är kortare än den tid det tar att bearbeta batchen, så kommer körningarna att läggas till hela tiden och tas bort. Om tids gränsen för inaktiva inaktivitet är större än batchens varaktighet tas utförar aldrig bort. **Vi rekommenderar därför att du inaktiverar dynamisk allokering genom att ställa in Spark. dynamicAllocation. enabled på false när du kör strömmande program.**

    Läs in paket som används av antecknings boken genom att ange följande information i en Notebook-cell. Kör kommandot genom att trycka på **CTRL + RETUR**.

    ```configuration
    %%configure -f
    {
        "conf": {
            "spark.jars.packages": "org.apache.spark:spark-sql-kafka-0-10_2.11:2.2.0",
            "spark.jars.excludes": "org.scala-lang:scala-reflect,org.apache.spark:spark-tags_2.11",
            "spark.dynamicAllocation.enabled": false
        }
    }
    ```

1. Skapa Kafka-avsnittet. Redigera kommandot nedan genom att ersätta `YOUR_ZOOKEEPER_HOSTS` med Zookeeper-värd information som extraherats i det första steget. Skapa ämnet genom att ange det redigerade kommandot i Jupyter Notebook `tripdata` .

    ```scala
    %%bash
    export KafkaZookeepers="YOUR_ZOOKEEPER_HOSTS"

    /usr/hdp/current/kafka-broker/bin/kafka-topics.sh --create --replication-factor 3 --partitions 8 --topic tripdata --zookeeper $KafkaZookeepers
    ```

1. Hämta data om taxi resor. Ange kommandot i nästa cell för att läsa in data i taxi resor i New York City. Data läses in i en dataframe och sedan visas dataframe som cellens utdata.

    ```scala
    import spark.implicits._

    // Load the data from the New York City Taxi data REST API for 2016 Green Taxi Trip Data
    val url="https://data.cityofnewyork.us/resource/pqfs-mqru.json"
    val result = scala.io.Source.fromURL(url).mkString

    // Create a dataframe from the JSON data
    val taxiDF = spark.read.json(Seq(result).toDS)

    // Display the dataframe containing trip data
    taxiDF.show()
    ```

1. Ange information om Kafka-Broker-värdar. Ersätt `YOUR_KAFKA_BROKER_HOSTS` med Service Broker-informationen som du extraherade i steg 1.  Ange det redigerade kommandot i nästa Jupyter Notebook cell.

    ```scala
    // The Kafka broker hosts and topic used to write to Kafka
    val kafkaBrokers="YOUR_KAFKA_BROKER_HOSTS"
    val kafkaTopic="tripdata"

    println("Finished setting Kafka broker and topic configuration.")
    ```

1. Skicka data till Kafka. I följande kommando `vendorid` används fältet som nyckel värde för Kafka-meddelandet. Nyckeln används av Kafka när data partitioneras. Alla fält lagras i Kafka-meddelandet som ett JSON-sträng värde. Ange följande kommando i Jupyter för att spara data till Kafka med hjälp av en batch-fråga.

    ```scala
    // Select the vendorid as the key and save the JSON string as the value.
    val query = taxiDF.selectExpr("CAST(vendorid AS STRING) as key", "to_JSON(struct(*)) AS value").write.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("topic", kafkaTopic).save()

    println("Data sent to Kafka")
    ```

1. Deklarera ett schema. Följande kommando visar hur du använder ett schema vid läsning av JSON-data från Kafka. Ange kommandot i nästa Jupyter-cell.

    ```scala
    // Import bits useed for declaring schemas and working with JSON data
    import org.apache.spark.sql._
    import org.apache.spark.sql.types._
    import org.apache.spark.sql.functions._

    // Define a schema for the data
    val schema = (new StructType).add("dropoff_latitude", StringType).add("dropoff_longitude", StringType).add("extra", StringType).add("fare_amount", StringType).add("improvement_surcharge", StringType).add("lpep_dropoff_datetime", StringType).add("lpep_pickup_datetime", StringType).add("mta_tax", StringType).add("passenger_count", StringType).add("payment_type", StringType).add("pickup_latitude", StringType).add("pickup_longitude", StringType).add("ratecodeid", StringType).add("store_and_fwd_flag", StringType).add("tip_amount", StringType).add("tolls_amount", StringType).add("total_amount", StringType).add("trip_distance", StringType).add("trip_type", StringType).add("vendorid", StringType)
    // Reproduced here for readability
    //val schema = (new StructType)
    //   .add("dropoff_latitude", StringType)
    //   .add("dropoff_longitude", StringType)
    //   .add("extra", StringType)
    //   .add("fare_amount", StringType)
    //   .add("improvement_surcharge", StringType)
    //   .add("lpep_dropoff_datetime", StringType)
    //   .add("lpep_pickup_datetime", StringType)
    //   .add("mta_tax", StringType)
    //   .add("passenger_count", StringType)
    //   .add("payment_type", StringType)
    //   .add("pickup_latitude", StringType)
    //   .add("pickup_longitude", StringType)
    //   .add("ratecodeid", StringType)
    //   .add("store_and_fwd_flag", StringType)
    //   .add("tip_amount", StringType)
    //   .add("tolls_amount", StringType)
    //   .add("total_amount", StringType)
    //   .add("trip_distance", StringType)
    //   .add("trip_type", StringType)
    //   .add("vendorid", StringType)

    println("Schema declared")
    ```

1. Välj data och starta strömmen. Följande kommando visar hur du hämtar data från Kafka med hjälp av en batch-fråga. Och skriv sedan resultatet ut till HDFS i Spark-klustret. I det här exemplet `select` hämtar meddelandet (värde fält) från Kafka och tillämpar schemat på det. Data skrivs sedan till HDFS (WASB eller ADL) i Parquet-format. Ange kommandot i nästa Jupyter-cell.

    ```scala
    // Read a batch from Kafka
    val kafkaDF = spark.read.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("subscribe", kafkaTopic).option("startingOffsets", "earliest").load()

    // Select data and write to file
    val query = kafkaDF.select(from_json(col("value").cast("string"), schema) as "trip").write.format("parquet").option("path","/example/batchtripdata").option("checkpointLocation", "/batchcheckpoint").save()

    println("Wrote data to file")
    ```

1. Du kan kontrol lera att filerna har skapats genom att ange kommandot i nästa Jupyter-cell. Den listar filerna i `/example/batchtripdata` katalogen.

    ```scala
    %%bash
    hdfs dfs -ls /example/batchtripdata
    ```

1. När föregående exempel använde en batch-fråga visar följande kommando hur du gör samma sak med en strömmande fråga. Ange kommandot i nästa Jupyter-cell.

    ```scala
    // Stream from Kafka
    val kafkaStreamDF = spark.readStream.format("kafka").option("kafka.bootstrap.servers", kafkaBrokers).option("subscribe", kafkaTopic).option("startingOffsets", "earliest").load()

    // Select data from the stream and write to file
    kafkaStreamDF.select(from_json(col("value").cast("string"), schema) as "trip").writeStream.format("parquet").option("path","/example/streamingtripdata").option("checkpointLocation", "/streamcheckpoint").start.awaitTermination(30000)
    println("Wrote data to file")
    ```

1. Kör följande cell för att kontrol lera att filerna skrevs av direkt uppspelnings frågan.

    ```scala
    %%bash
    hdfs dfs -ls /example/streamingtripdata
    ```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill rensa resurserna som har skapats med den här självstudien kan du ta bort resursgruppen. Om du tar bort resurs gruppen raderas även det associerade HDInsight-klustret. Och andra resurser som är kopplade till resurs gruppen.

Ta bort en resursgrupp med Azure Portal:

1. I [Azure Portal](https://portal.azure.com/)expanderar du menyn på vänster sida för att öppna menyn med tjänster och väljer sedan __resurs grupper__ för att visa listan över dina resurs grupper.
2. Leta reda på den resursgrupp du vill ta bort och högerklicka på knappen __Mer__ (...) till höger om listan.
3. Välj __Ta bort resursgrupp__ och bekräfta.

> [!WARNING]  
> Debiteringen för HDInsight-klustret börjar när ett kluster skapas och stoppas när klustret tas bort. Debiteringen görs i förväg per minut, så du ska alltid ta bort ditt kluster när det inte används.
>
> Om du tar bort en Kafka i ett HDInsight-kluster tas alla data som lagrats i Kafka bort.

## <a name="next-steps"></a>Nästa steg

I den här självstudien har du lärt dig hur du använder Apache Spark strukturerad strömning. Skriva och läsa data från Apache Kafka i HDInsight. Via följande länk kan du lära dig att använda Apache Storm med Kafka.

> [!div class="nextstepaction"]
> [Använda Apache Storm med Apache Kafka](hdinsight-apache-storm-with-kafka.md)
