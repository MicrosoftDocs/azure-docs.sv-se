---
title: Spark-direktuppspelning i Azure HDInsight
description: Använda Apache Spark strömmande program i HDInsight Spark-kluster.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,seoapr2020
ms.date: 04/23/2020
ms.openlocfilehash: bde6c5b2bad12df8642dd3c9b4a49548f7bc9a6d
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98929517"
---
# <a name="overview-of-apache-spark-streaming"></a>Översikt över Apache Spark strömning

[Apache Spark](https://spark.apache.org/) Streaming tillhandahåller bearbetning av data strömmar på HDInsight Spark-kluster. Med en garanti att indatamängden bearbetas exakt en gång, även om ett nodfel inträffar. En spark-dataström är ett långvarigt jobb som tar emot indata från en mängd olika källor, inklusive Azure Event Hubs. Dessutom: Azure IoT Hub, Apache Kafka, Apache FLUME, Twitter, `ZeroMQ` , RAW TCP-Sockets eller från övervakning Apache HADOOP garn fil system. Till skillnad från en enda händelse driven process, tar en spark-Stream in indata i tid i Windows. Till exempel en 2-sekunds sektor och transformerar varje batch med data med hjälp av kart-, minska-, kopplings-och extraherings åtgärder. Spark-dataströmmen skriver sedan transformerade data till fil system, databaser, instrument paneler och konsolen.

![Strömnings bearbetning med HDInsight och Spark streaming](./media/apache-spark-streaming-overview/hdinsight-spark-streaming.png)

Spark streaming-program måste vänta en bråkdel av en sekund för att samla in varje `micro-batch` händelse innan du skickar batchen för bearbetning. Ett händelse drivet program bearbetar däremot varje händelse omedelbart. Fördröjning av Spark-direktuppspelning är vanligt vis under några sekunder. Fördelarna med mikrobatch-metoden är mer effektiv data behandling och enklare mängd beräkningar.

## <a name="introducing-the-dstream"></a>Vi presenterar DStream

Spark streaming representerar en kontinuerlig ström med inkommande data med hjälp av en *diskretiserade-dataström* som kallas DStream. En DStream kan skapas från ingångs källor som Event Hubs eller Kafka. Eller genom att tillämpa omvandlingar på en annan DStream.

En DStream tillhandahåller ett skikt med abstraktion ovanpå rå händelse data.

Börja med en enda händelse, anta en temperatur läsning från en ansluten termostat. När den här händelsen kommer till ditt Spark streaming-program, lagras händelsen på ett tillförlitligt sätt, där den replikeras på flera noder. Den här fel toleransen garanterar att fel på en enskild nod inte leder till att händelsen går förlorad. Spark-kärnan använder en data struktur som distribuerar data över flera noder i klustret. Var varje nod i allmänhet behåller sina egna data i minnet för bästa prestanda. Den här data strukturen kallas för en *elastisk distribuerad data uppsättning* (RDD).

Varje RDD representerar händelser som samlas in över en användardefinierad tidsram som kallas *batch-intervallet*. När varje batch-intervall förflyter skapas en ny RDD som innehåller alla data från intervallet. Den kontinuerliga uppsättningen RDD samlas in i en DStream. Om batch-intervallet till exempel är en sekund lång, avger DStream en batch varje sekund som innehåller en RDD som innehåller alla data som matas in under den andra. När DStream bearbetas visas temperatur händelsen i någon av dessa batchar. Ett Spark streaming-program bearbetar batcharna som innehåller händelserna och som slutligen agerar på de data som lagras i varje RDD.

![Exempel på DStream med temperatur händelser](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-example.png)

## <a name="structure-of-a-spark-streaming-application"></a>Struktur för ett Spark streaming-program

Ett Spark-strömmande program är ett långvarigt program som tar emot data från inmatnings källor. Tillämpar omvandlingar för att bearbeta data och skickar sedan ut data till en eller flera mål. Strukturen för ett Spark streaming-program har en statisk del och en dynamisk del. Den statiska delen definierar var data kommer från, vilken bearbetning som ska utföras på data. Och var resultatet ska placeras. Den dynamiska delen kör programmet oändligt, vilket väntar på en stopp signal.

Till exempel får följande enkla program en rad text över en TCP-socket och räknar antalet gånger som varje ord visas.

### <a name="define-the-application"></a>Definiera programmet

Definitionen av program logiken består av fyra steg:

1. Skapa en StreamingContext.
2. Skapa en DStream från StreamingContext.
3. Tillämpa transformeringar på DStream.
4. Resultatet visas.

Den här definitionen är statisk och inga data behandlas förrän du kör programmet.

#### <a name="create-a-streamingcontext"></a>Skapa en StreamingContext

Skapa en StreamingContext från SparkContext som pekar på klustret. När du skapar en StreamingContext anger du storleken på batchen på några sekunder, till exempel:  

```
import org.apache.spark._
import org.apache.spark.streaming._

val ssc = new StreamingContext(sc, Seconds(1))
```

#### <a name="create-a-dstream"></a>Skapa en DStream

Med StreamingContext-instansen skapar du en indatamängds-DStream för din indatakälla. I det här fallet tittar programmet efter utseendet på nya filer i den anslutna standard lagringen.

```
val lines = ssc.textFileStream("/uploads/Test/")
```

#### <a name="apply-transformations"></a>Använda transformeringar

Du implementerar bearbetningen genom att tillämpa omvandlingar på DStream. Det här programmet tar emot en rad text i taget från filen och delar varje rad i orden. Och använder sedan ett mönster för att minska antalet gånger som varje ord visas.

```
val words = lines.flatMap(_.split(" "))
val pairs = words.map(word => (word, 1))
val wordCounts = pairs.reduceByKey(_ + _)
```

#### <a name="output-results"></a>Resultat för utdata

Push-överför omvandlingen till mål systemen genom att använda utmatnings åtgärder. I det här fallet skrivs resultatet av varje körning genom beräkningen i konsolens utdata.

```
wordCounts.print()
```

### <a name="run-the-application"></a>Kör programmet

Starta streaming-programmet och kör det tills en avslutnings signal tas emot.

```
ssc.start()
ssc.awaitTermination()
```

Mer information om Spark Stream API finns i [Apache Spark streaming Programming guide](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html).

Följande exempel program är fristående, så du kan köra det inuti en [Jupyter Notebook](apache-spark-jupyter-notebook-kernels.md). I det här exemplet skapas en modell data källa i klassen DummySource som matar ut värdet för en räknare och den aktuella tiden i millisekunder var femte sekund. Ett nytt StreamingContext-objekt har ett batch-intervall på 30 sekunder. Varje gång en batch skapas, undersöker streaming-programmet RDD som produceras. Konverterar sedan RDD till en spark-DataFrame och skapar en tillfällig tabell över DataFrame.

```
class DummySource extends org.apache.spark.streaming.receiver.Receiver[(Int, Long)](org.apache.spark.storage.StorageLevel.MEMORY_AND_DISK_2) {

    /** Start the thread that simulates receiving data */
    def onStart() {
        new Thread("Dummy Source") { override def run() { receive() } }.start()
    }

    def onStop() {  }

    /** Periodically generate a random number from 0 to 9, and the timestamp */
    private def receive() {
        var counter = 0  
        while(!isStopped()) {
            store(Iterator((counter, System.currentTimeMillis)))
            counter += 1
            Thread.sleep(5000)
        }
    }
}

// A batch is created every 30 seconds
val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

// Set the active SQLContext so that we can access it statically within the foreachRDD
org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

// Create the stream
val stream = ssc.receiverStream(new DummySource())

// Process RDDs in the batch
stream.foreachRDD { rdd =>

    // Access the SQLContext and create a table called demo_numbers we can query
    val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
    _sqlContext.createDataFrame(rdd).toDF("value", "time")
        .registerTempTable("demo_numbers")
}

// Start the stream processing
ssc.start()
```

Vänta i cirka 30 sekunder efter att du startat programmet ovan.  Sedan kan du efter fråga DataFrame med jämna mellanrum för att se den aktuella uppsättningen värden som finns i batchen, till exempel med hjälp av SQL-fr åga:

```sql
%%sql
SELECT * FROM demo_numbers
```

Resultatet ser ut som följande utdata:

| värde | time |
| --- | --- |
|10 | 1497314465256 |
|11 | 1497314470272 |
|12 | 1497314475289 |
|13 | 1497314480310 |
|14 | 1497314485327 |
|15 | 1497314490346 |

Det finns sex värden eftersom DummySource skapar ett värde var femte sekund och programmet avger en batch var 30: e sekund.

## <a name="sliding-windows"></a>Glidande fönster

Om du vill utföra sammanställda beräkningar i DStream under en viss tids period, till exempel för att få en genomsnitts temperatur under de senaste två Sekunderna, använder du `sliding window` åtgärderna som ingår i Spark-direktuppspelning. Ett glidande fönster har en varaktighet (fönster längd) och det intervall då fönstrets innehåll utvärderas (bild intervallet).

Glidande fönster kan överlappa, du kan till exempel definiera ett fönster med en längd på två sekunder, som bilderna var tredje. Den här åtgärden innebär varje gång som du utför en agg regerings beräkning, innehåller fönstret data från den sista och den andra av föregående fönster. Och alla nya data i nästa sekund.

![Exempel på inledande fönster med temperatur händelser](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-01.png)

![Exempel fönster med temperatur händelser efter glidning](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-window-02.png)

I följande exempel uppdateras koden som använder DummySource för att samla in batcharna i ett fönster med en varaktighet på en minut och en en minuts-bild.

```
class DummySource extends org.apache.spark.streaming.receiver.Receiver[(Int, Long)](org.apache.spark.storage.StorageLevel.MEMORY_AND_DISK_2) {

    /** Start the thread that simulates receiving data */
    def onStart() {
        new Thread("Dummy Source") { override def run() { receive() } }.start()
    }

    def onStop() {  }

    /** Periodically generate a random number from 0 to 9, and the timestamp */
    private def receive() {
        var counter = 0  
        while(!isStopped()) {
            store(Iterator((counter, System.currentTimeMillis)))
            counter += 1
            Thread.sleep(5000)
        }
    }
}

// A batch is created every 30 seconds
val ssc = new org.apache.spark.streaming.StreamingContext(spark.sparkContext, org.apache.spark.streaming.Seconds(30))

// Set the active SQLContext so that we can access it statically within the foreachRDD
org.apache.spark.sql.SQLContext.setActive(spark.sqlContext)

// Create the stream
val stream = ssc.receiverStream(new DummySource())

// Process batches in 1 minute windows
stream.window(org.apache.spark.streaming.Minutes(1)).foreachRDD { rdd =>

    // Access the SQLContext and create a table called demo_numbers we can query
    val _sqlContext = org.apache.spark.sql.SQLContext.getOrCreate(rdd.sparkContext)
    _sqlContext.createDataFrame(rdd).toDF("value", "time")
    .registerTempTable("demo_numbers")
}

// Start the stream processing
ssc.start()
```

Efter den första minuten finns 12 poster-sex poster från var och en av de två batcharna som samlats in i fönstret.

| värde | time |
| --- | --- |
| 1 | 1497316294139 |
| 2 | 1497316299158
| 3 | 1497316304178
| 4 | 1497316309204
| 5 | 1497316314224
| 6 | 1497316319243
| 7 | 1497316324260
| 8 | 1497316329278
| 9 | 1497316334293
| 10 | 1497316339314
| 11 | 1497316344339
| 12 | 1497316349361

De glidande fönster funktionerna som är tillgängliga i Spark streaming API inkluderar Window, countByWindow, reduceByWindow och countByValueAndWindow. Mer information om dessa funktioner finns i [transformeringar på DStreams](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html#transformations-on-dstreams).

## <a name="checkpointing"></a>Kontrollpunkter

För att leverera återhämtnings-och fel tolerans är Spark-direktuppspelning beroende av kontroll punkter för att säkerställa att Stream-bearbetningen kan fortsätta oavbrutet, även om det är fel på noderna. Spark skapar en kontroll punkt för beständig lagring (Azure Storage eller Data Lake Storage). Dessa kontroll punkter lagrar strömmande programmetadata, till exempel konfigurationen, och de åtgärder som definierats av programmet. Även alla batchar som köade men ännu inte bearbetats. Ibland inkluderar kontroll punkterna även att spara data i RDD för att snabbare återskapa statusen för data från vad som finns i RDD som hanteras av Spark.

## <a name="deploying-spark-streaming-applications"></a>Distribuera program för Spark-direktuppspelning

Du skapar vanligt vis ett Spark streaming-program lokalt i en JAR-fil. Distribuera den sedan till Spark på HDInsight genom att kopiera JAR-filen till den anslutna standard lagringen. Du kan starta ditt program med de LIVY REST API: er som är tillgängliga från klustret med en POST-åtgärd. Innehållet i inlägget innehåller ett JSON-dokument som ger din JAR-sökväg. Namnet på klassen vars huvudsakliga metod definierar och kör Streaming-programmet och eventuellt resurs kraven för jobbet (till exempel antalet körningar, minne och kärnor). Eventuella konfigurations inställningar för program koden kräver också.

![Distribuera ett Spark streaming-program](./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png)

Status för alla program kan också kontrol leras med en GET-begäran mot en LIVY-slutpunkt. Slutligen kan du avsluta ett program som körs genom att utfärda en DELETE-begäran mot LIVY-slutpunkten. Mer information om LIVY-API: et finns i [Fjärrjobb med Apache LIVY](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>Nästa steg

* [Skapa ett Apache Spark-kluster i HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Programmerings guide för Apache Spark strömning](https://people.apache.org/~pwendell/spark-releases/latest/streaming-programming-guide.html)
* [Översikt över Apache Spark strukturerad strömning](apache-spark-structured-streaming-overview.md)
