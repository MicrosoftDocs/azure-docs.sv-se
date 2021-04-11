---
title: Spark streaming-jobb med hög tillgänglighet i garn – Azure HDInsight
description: Konfigurera Apache Spark strömning för ett scenario med hög tillgänglighet i Azure HDInsight
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/29/2019
ms.openlocfilehash: 602fa1cab71a797dd25aca263e0c6a9f2aa616bb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104870236"
---
# <a name="create-high-availability-apache-spark-streaming-jobs-with-yarn"></a>Skapa Apache Spark strömnings jobb med hög tillgänglighet med garn

[Apache Spark](https://spark.apache.org/) Med direkt uppspelning kan du implementera skalbara, stora data flöden, feltoleranta program för bearbetning av data strömmar. Du kan ansluta Spark streaming-program till ett HDInsight Spark-kluster till olika typer av data källor, till exempel Azure Event Hubs, Azure IoT Hub, [Apache Kafka](https://kafka.apache.org/), [Apache FLUME](https://flume.apache.org/), Twitter, [ZeroMQ](http://zeromq.org/), RAW TCP-sockets eller genom att övervaka [Apache Hadoop HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html) -filsystem för ändringar. Spark-direktuppspelning har stöd för fel tolerans med garanti för att en specifik händelse bearbetas exakt en gång, även om ett nodfel uppstår.

Spark streaming skapar långvariga jobb där du kan tillämpa omvandlingar för data och sedan skicka ut resultatet till fil system, databaser, instrument paneler och konsolen. Spark streaming bearbetar mikrobatchar av data genom att först samla in en batch med händelser under ett definierat tidsintervall. Därefter skickas den batchen för bearbetning och utdata. Batch-tidsintervall definieras vanligt vis i bråk delar av en sekund.

:::image type="content" source="./media/apache-spark-streaming-high-availability/apache-spark-streaming.png" alt-text="Spark-direktuppspelning" border="false":::

## <a name="dstreams"></a>DStreams

Spark streaming representerar en kontinuerlig data ström med hjälp av en *diskretiserade-ström* (DStream). Den här DStream kan skapas från indatamängder som Event Hubs eller Kafka, eller genom att använda transformationer på en annan DStream. När en händelse anländer till ditt Spark streaming-program, lagras händelsen på ett tillförlitligt sätt. Det vill säga att händelse data replikeras så att flera noder har en kopia av den. Detta säkerställer att felet för en enskild nod inte leder till att händelsen går förlorad.

Spark-kärnan använder *elastiska distribuerade data uppsättningar* (RDD). RDD distribuerar data över flera noder i klustret, där varje nod i allmänhet behåller sina data helt i minnet för bästa prestanda. Varje RDD representerar händelser som samlas in över ett batch-intervall. När batch-intervallet förflyter skapar Spark streaming en ny RDD som innehåller alla data i intervallet. Den här kontinuerliga uppsättningen RDD samlas in i en DStream. Ett Spark streaming-program bearbetar de data som lagras i varje Batchs RDD.

:::image type="content" source="./media/apache-spark-streaming-high-availability/apache-spark-dstream.png" alt-text="Spark-DStream" border="false":::

## <a name="spark-structured-streaming-jobs"></a>Spark-strukturerade strömmande jobb

Spark-strukturerad strömning introducerades i Spark 2,0 som en analys motor för användning på strömmande strukturerade data. Spark-strukturerad direkt uppspelning använder API: erna för SparkSQL batching-motor. Precis som med Spark streaming kör Spark Structured streaming sina beräkningar för kontinuerlig inkommande mikrobatchar av data. Spark-strukturerad strömning representerar en data ström som en inmatnings tabell med obegränsade rader. Det vill säga att inmatnings tabellen fortsätter att växa när nya data tas emot. Den här inmatnings tabellen bearbetas kontinuerligt av en tids krävande fråga och resultatet skrivs ut till en utgående tabell.

:::image type="content" source="./media/apache-spark-streaming-high-availability/structured-streaming.png" alt-text="Spark-strukturerad strömning" border="false":::

I strukturerad strömning tas data emot i systemet och matas direkt in i inmatnings tabellen. Du skriver frågor som utför åtgärder mot denna indataparameter. Frågeresultatet ger till gång till en annan tabell, som kallas resultat tabellen. Resultat tabellen innehåller resultat från din fråga, från vilken du ritar data som ska skickas till ett externt data lager som en Relations databas. *Utlösarens intervall* anger tids inställningen för när data bearbetas från inmatnings tabellen. Som standard bearbetar strukturerad strömning data så fort de anländer. Du kan dock också konfigurera utlösaren så att den körs vid ett längre intervall, så att strömmande data bearbetas i tidsbaserade batchar. Data i resultat tabellen kan uppdateras varje gång som det finns nya data så att de innehåller alla utdata sedan den strömmade frågan började (*fullständigt läge*), eller så kan den bara bara innehålla de data som är nya sedan frågan senast bearbetades (*tilläggs läge*).

## <a name="create-fault-tolerant-spark-streaming-jobs"></a>Skapa feltoleranta Spark-strömmande jobb

Om du vill skapa en miljö med hög tillgänglighet för dina Spark streaming-jobb börjar du med att koda dina enskilda jobb för återställning i händelse av ett haveri. Sådana Självåterställande jobb är feltoleranta.

RDD har flera egenskaper som hjälper hög tillgängliga och feltoleranta Spark-strömmande jobb:

* Batchar av indata som lagras i RDD som en DStream replikeras automatiskt i minnet för fel tolerans.
* Data som förlorats på grund av ett arbets haveri kan inte beräknas om från replikerade indata på olika arbetare, så länge de är tillgängliga.
* Snabb fel återställning kan ske inom en sekund, eftersom återställningen från fel-/stragglers sker via beräkning i minnet.

### <a name="exactly-once-semantics-with-spark-streaming"></a>Exakt en gång med Spark-direktuppspelning

Om du vill skapa ett program som bearbetar varje händelse en gång (och bara en gång) bör du fundera över hur alla system punkter av fel startar om efter ett problem och hur du kan undvika data förlust. Exakt en gång kräver att inga data förloras när som helst och att meddelande bearbetningen kan startas om, oavsett var fel uppstår. Se [skapa Spark streaming-jobb med exakt en händelse bearbetning](apache-spark-streaming-exactly-once.md).

## <a name="spark-streaming-and-apache-hadoop-yarn"></a>Spark-strömning och Apache Hadoop garn

I HDInsight samordnas kluster arbetet av *ännu en annan resurs Negotiator* (garn). Att designa hög tillgänglighet för Spark streaming innehåller tekniker för Spark streaming och även för garn komponenter.  En exempel konfiguration som använder garn visas nedan.

:::image type="content" source="./media/apache-spark-streaming-high-availability/hdi-yarn-architecture.png" alt-text="GARN arkitektur" border="false":::

I följande avsnitt beskrivs design överväganden för den här konfigurationen.

### <a name="plan-for-failures"></a>Planera för problem

Om du vill skapa en garn konfiguration för hög tillgänglighet bör du planera för ett möjligt utförar eller driv rutins haveri. Vissa spark streaming-jobb omfattar även data garanti krav som behöver ytterligare konfiguration och installation. Ett strömmande program kan till exempel ha ett affärs krav för en noll-data förlust garanti trots eventuella fel som uppstår i värdbaserade strömmande system eller HDInsight-kluster.

Om ett **utförar** Miss lyckas startas dess uppgifter och mottagare om av Spark automatiskt, så det finns ingen konfigurations ändring som behövs.

Men om en **driv rutin** Miss lyckas, Miss lyckas alla tillhör ande körningar och alla mottagna block och beräknings resultat går förlorade. Om du vill återställa från ett driv rutins fel använder du *DStream-kontrollpunktering* enligt beskrivningen i [skapa Spark streaming-jobb med exakt en händelse bearbetning](apache-spark-streaming-exactly-once.md#use-checkpoints-for-drivers). DStream för kontroll punkter sparar regelbundet det *riktade diagrammet* (dag) för DStreams till feltolerant lagring som Azure Storage.  Med kontroll punkter tillåts Spark-strukturerad strömning för att starta om den felande driv rutinen från kontroll punkts informationen.  Den här driv rutinen startar om nya körningar och startar även om mottagare.

Återställa driv rutiner med DStream-kontroll punkter:

* Konfigurera automatisk omstart av driv rutin på garn med konfigurations inställningen `yarn.resourcemanager.am.max-attempts` .
* Ange en kontroll punkts katalog i ett HDFS-kompatibelt fil system med `streamingContext.checkpoint(hdfsDirectory)` .
* Omstrukturera käll koden för att använda kontroll punkter för återställning, till exempel:

    ```scala
        def creatingFunc() : StreamingContext = {
            val context = new StreamingContext(...)
            val lines = KafkaUtils.createStream(...)
            val words = lines.flatMap(...)
            ...
            context.checkpoint(hdfsDir)
        }

        val context = StreamingContext.getOrCreate(hdfsDir, creatingFunc)
        context.start()
    ```

* Konfigurera förlorad återställning av data genom att aktivera loggen för Skriv åtgärder (WAL) med `sparkConf.set("spark.streaming.receiver.writeAheadLog.enable","true")` och inaktivera minnes intern replikering för indata-DStreams med `StorageLevel.MEMORY_AND_DISK_SER` .

För att sammanfatta, med hjälp av kontroll punkter + WAL + Reliable receiver, kan du leverera "minst en gång" Data återställning:

* Exakt en gång, så länge mottagna data inte går förlorade och utmatningarna är antingen idempotenta eller transaktionell.
* Exakt en gång, med den nya Kafka Direct-metoden, som använder Kafka som en replikerad logg, i stället för att använda mottagare eller WALs.

### <a name="typical-concerns-for-high-availability"></a>Vanliga problem för hög tillgänglighet

* Det är svårare att övervaka strömnings jobb än batch-jobb. Spark streaming-jobb är vanligt vis långvariga och garn sammanställer inte loggar förrän ett jobb har slutförts.  Spark-kontrollpunkter går förlorade vid program-eller Spark-uppgraderingar och du måste rensa kontroll punkts katalogen under en uppgradering.

* Konfigurera ditt garn kluster läge så att driv rutiner körs, även om en klient slutar fungera. Konfigurera automatisk omstart för driv rutiner:

    ```
    spark.yarn.maxAppAttempts = 2
    spark.yarn.am.attemptFailuresValidityInterval=1h
    ```

* Spark-och Spark streaming-gränssnittet har ett konfigurerbart mått system. Du kan också använda ytterligare bibliotek som graf-/Grafana för att hämta instrument panels mått som "antal poster bearbetade", "minnes/GC-användning på driv rutin & körningar", "total fördröjning", "användning av klustret" och så vidare. I strukturerad strömmande version 2,1 eller senare kan du använda `StreamingQueryListener` för att samla in ytterligare mått.

* Du bör segmentera tids krävande jobb.  När ett Spark streaming-program skickas till klustret måste den garn kö där jobbet körs måste definieras. Du kan använda en [plan för garn kapacitet](https://hadoop.apache.org/docs/stable/hadoop-yarn/hadoop-yarn-site/CapacityScheduler.html) för att skicka långvariga jobb till separata köer.

* Stäng av ditt strömnings program på ett smidigt sätt. Om dina förskjutningar är kända och alla program tillstånd lagras externt kan du program mässigt stoppa strömnings programmet på rätt plats. En teknik är att använda "tråd krokar" i Spark genom att söka efter en extern flagga var *n* : e sekund. Du kan också använda en *markör fil* som skapas på HDFS när du startar programmet och sedan tas bort när du vill stoppa. Använd en separat tråd i Spark-programmet som anropar kod som liknar detta för en markör fil metod:

    ```scala
    streamingContext.stop(stopSparkContext = true, stopGracefully = true)
    // to be able to recover on restart, store all offsets in an external database
    ```

## <a name="next-steps"></a>Nästa steg

* [Översikt över Apache Spark strömning](apache-spark-streaming-overview.md)
* [Skapa Apache Spark strömmande jobb med exakt en händelse bearbetning](apache-spark-streaming-exactly-once.md)
* [Tids krävande Apache Spark strömnings jobb på garn](https://mkuthan.github.io/blog/2016/09/30/spark-streaming-on-yarn/)
* [Strukturerad strömning: feltolerant semantik](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html#fault-tolerance-semantics)
* [Diskretiserade-strömmar: en Fault-Tolerant modell för skalbar data ström bearbetning](https://www2.eecs.berkeley.edu/Pubs/TechRpts/2012/EECS-2012-259.pdf)
