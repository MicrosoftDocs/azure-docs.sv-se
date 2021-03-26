---
title: Migrera Azure HDInsight 3,6 Apache Storm till HDInsight 4,0 Apache Spark
description: Skillnader och migreringsjobb för migrering av Apache Storm arbets belastningar till Spark streaming eller Spark-strukturerad strömning.
ms.service: hdinsight
ms.topic: how-to
ms.date: 01/16/2019
ms.openlocfilehash: b8b054d06c9c0987508abfdf03bbcf9470572bd1
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104868774"
---
# <a name="migrate-azure-hdinsight-36-apache-storm-to-hdinsight-40-apache-spark"></a>Migrera Azure HDInsight 3,6 Apache Storm till HDInsight 4,0 Apache Spark

Det här dokumentet beskriver hur du migrerar Apache Storm-arbetsbelastningar i HDInsight 3,6 till HDInsight 4,0. HDInsight 4,0 har inte stöd för Apache Storm kluster typ och du måste migrera till en annan strömmande data plattform. Två lämpliga alternativ är Apache Spark strömning och Spark-strukturerad strömning. Det här dokumentet beskriver skillnaderna mellan dessa plattformar och rekommenderar också ett arbets flöde för migrering av Apache Storm-arbetsbelastningar.

## <a name="storm-migration-paths-in-hdinsight"></a>Sökvägar för Storm-migrering i HDInsight

Om du vill migrera från Apache Storm på HDInsight 3,6 har du flera alternativ:

* Spark-direktuppspelning på HDInsight 4,0
* Spark-strukturerad strömning på HDInsight 4,0
* Azure Stream Analytics

Det här dokumentet innehåller en guide för migrering från Apache Storm till Spark streaming och Spark-strukturerad strömning.

:::image type="content" source="./media/migrate-storm-to-spark/storm-migration-path.png" alt-text="Sökväg till HDInsight Storm-migrering" border="false":::

## <a name="comparison-between-apache-storm-and-spark-streaming-spark-structured-streaming"></a>Jämförelse mellan Apache Storm och Spark-direktuppspelning, Spark-strukturerad strömning

Apache Storm kan ge olika nivåer av garanterad meddelandebehandling. Ett Basic Storm-program kan till exempel garantera minst en gång och [Trident](https://storm.apache.org/releases/current/Trident-API-Overview.html) kan garantera exakt en gång. Spark streaming och Spark Structured streaming garanterar att alla indata-händelser bearbetas exakt en gång, även om ett nodfel inträffar. Storm har en modell som behandlar varje enskild händelse och du kan även använda Micro batch-modellen med Trident. Spark streaming och Spark Structured streaming tillhandahåller Micro-Batch bearbetnings modell.

|  |Storm |Spark-strömning | Spark-strukturerad strömning|
|---|---|---|---|
|**Händelse bearbetnings garanti**|Minst en gång <br> Exakt en gång (Trident) |[Exakt en gång](https://spark.apache.org/docs/latest/streaming-programming-guide.html)|[Exakt en gång](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**Bearbetar modell**|Real tids <br> Micro batch (Trident) |Micro batch |Micro batch |
|**Tids stöd för händelse**|[Ja](https://storm.apache.org/releases/2.0.0/Windowing.html)|Inga|[Ja](https://spark.apache.org/docs/latest/structured-streaming-programming-guide.html)|
|**Språk**|Java osv.|Scala, Java, python|Python, R, Scala, Java, SQL|

### <a name="spark-streaming-vs-spark-structured-streaming"></a>Spark-direktuppspelning vs Spark-strukturerad strömning

Spark-strukturerad strömning ersätter Spark streaming (DStreams). Strukturerad direkt uppspelning kommer att fortsätta att ta emot förbättringar och underhåll, medan DStreams endast är i underhålls läge. **Obs! du behöver Länkar för att framhäva den här punkten**. Strukturerad direkt uppspelning har inte lika många funktioner som DStreams för källorna och de handfat som den stöder utanför lådan, så du kan utvärdera dina krav för att välja lämpligt bearbetnings alternativ för Spark-strömmar.

## <a name="streaming-single-event-processing-vs-micro-batch-processing"></a>Strömning (Single event)-bearbetning jämfört Micro-Batch bearbetning

Storm tillhandahåller en modell som behandlar varje enskild händelse. Det innebär att alla inkommande poster kommer att bearbetas så fort de tas emot. Spark streaming-program måste vänta en bråkdel av en sekund för att samla in varje mikrobatch av händelser innan du skickar den batchen för bearbetning. Ett händelse drivet program bearbetar däremot varje händelse omedelbart. Fördröjning av Spark-direktuppspelning är vanligt vis under några sekunder. Fördelarna med mikrobatch-metoden är mer effektiv data behandling och enklare mängd beräkningar.

:::image type="content" source="./media/migrate-storm-to-spark/streaming-and-micro-batch-processing.png" alt-text="strömning och bearbetning av mikrobatch" border="false":::

## <a name="storm-architecture-and-components"></a>Storm-arkitektur och-komponenter

Storm-topologier består av flera komponenter som är ordnade i en riktad acyklisk graf (DAG). Data flödar mellan komponenter i diagrammet. Varje komponent använder en eller flera dataströmmar och kan du generera en eller flera strömmar.

|Komponent |Beskrivning |
|---|---|
|Kanalen|Hämtar data till en topologi. De sänder en eller flera strömmar till topologin.|
|Markering|Använder strömmar som har avsänts från kanaler eller andra bultar. Bultar kan eventuellt sända strömmar till topologin. Bultar ansvarar för att skriva data till extern lagring, till exempel HDFS, Kafka eller HBase.|

:::image type="content" source="./media/migrate-storm-to-spark/apache-storm-components.png" alt-text="interaktion av storm-komponenter" border="false":::

Storm består av följande tre daemonar som håller Storm-klustret fungerande.

|Program |Beskrivning |
|---|---|
|Nimbus|I likhet med Hadoop JobTracker ansvarar det att distribuera kod runt klustret och tilldela uppgifter till datorer och övervakning av fel.|
|Zookeeper|Används för kluster samordning.|
|Chef|Lyssnar efter arbete som har tilldelats till datorn och startar och stoppar arbets processer baserade på direktiv från Nimbus. Varje arbets process kör en delmängd av en topologi. Användarens program logik (kanaler och bult) kör här.|

:::image type="content" source="./media/migrate-storm-to-spark/nimbus-zookeeper-supervisor.png" alt-text="Nimbus-, Zookeeper-och övervaknings-daemonar" border="false":::

## <a name="spark-streaming-architecture-and-components"></a>Arkitektur och komponenter för Spark-direktuppspelning

Följande steg sammanfattar hur komponenter fungerar tillsammans i Spark streaming (DStreams) och Spark-strukturerad strömning:

* När Spark-direktuppspelning startas startar driv rutinen uppgiften i utförar.
* Utförar tar emot en ström från en strömmande data källa.
* När utförar tar emot data strömmar delar den upp strömmen i block och behåller dem i minnet.
* Data block replikeras till andra körningar.
* Bearbetade data lagras sedan i mål data lagret.

:::image type="content" source="./media/migrate-storm-to-spark/spark-streaming-to-output.png" alt-text="Spark streaming-sökväg till utdata" border="false":::

## <a name="spark-streaming-dstream-workflow"></a>DStream-arbetsflöde (Spark streaming)

När varje batch-intervall förflyter skapas en ny RDD som innehåller alla data från intervallet. De kontinuerliga uppsättningarna av RDD samlas in i en DStream. Om batch-intervallet till exempel är en sekund lång, avger DStream en batch varje sekund som innehåller en RDD som innehåller alla data som matas in under den andra. När DStream bearbetas visas temperatur händelsen i någon av dessa batchar. Ett Spark streaming-program bearbetar batcharna som innehåller händelserna och som slutligen agerar på de data som lagras i varje RDD.

:::image type="content" source="./media/migrate-storm-to-spark/spark-streaming-batches.png" alt-text="bearbetnings grupper för Spark-direktuppspelning" border="false":::

Mer information om olika omvandlingar som är tillgängliga med Spark streaming finns i [transformeringar på DStreams](https://spark.apache.org/docs/latest/streaming-programming-guide.html#transformations-on-dstreams).

## <a name="spark-structured-streaming"></a>Spark-strukturerad strömning

Spark-strukturerad strömning representerar en data ström som en tabell som är obegränsad i djupet. Tabellen fortsätter att växa när nya data tas emot. Den här inmatnings tabellen bearbetas kontinuerligt av en lång körnings fråga och resultatet skickas till en utgående tabell.

I strukturerad strömning tas data emot i systemet och matas direkt in i en inmatnings tabell. Du skriver frågor (med hjälp av DataFrame och data uppsättnings-API: er) som utför åtgärder mot den här indata-tabellen.

Frågeresultatet ger till gång till en *resultat tabell* som innehåller resultatet från frågan. Du kan rita data från resultat tabellen för ett externt data lager, till exempel en Relations databas.

Tiden för när data bearbetas från inmatnings tabellen styrs av utlösarens intervall. Som standard är utlösnings intervallet noll, så att strukturerad strömning försöker bearbeta data så fort de anländer. I praktiken innebär det att så fort Structured streaming utförs bearbetningen av den föregående frågans körning, startas en annan bearbetnings körning mot eventuella nyligen mottagna data. Du kan konfigurera utlösaren att köras med ett intervall, så att strömmande data bearbetas i tidsbaserade batchar.

:::image type="content" source="./media/migrate-storm-to-spark/structured-streaming-data-processing.png" alt-text="bearbetning av data i strukturerad strömning" border="false":::

:::image type="content" source="./media/migrate-storm-to-spark/structured-streaming-model.png" alt-text="programmerings modell för strukturerad strömning" border="false":::

## <a name="general-migration-flow"></a>Flöde för allmän migrering

Det rekommenderade migreringsjobbet från storm till Spark förutsätter följande inledande arkitektur:

* Kafka används som data källa för strömning
* Kafka och Storm distribueras i samma virtuella nätverk
* De data som bearbetas av storm skrivs till en data mottagare, till exempel Azure Storage eller Azure Data Lake Storage Gen2.

   :::image type="content" source="./media/migrate-storm-to-spark/presumed-current-environment.png" alt-text="diagram över förmodad aktuell miljö"  border="false":::

Gör så här om du vill migrera programmet från storm till en av Spark streaming-API: erna:

1. **Distribuera ett nytt kluster.** Distribuera ett nytt HDInsight 4,0 Spark-kluster i samma virtuella nätverk och distribuera Spark-direktuppspelning eller Spark-strukturerade strömnings program på den och testa det noggrant.

   :::image type="content" source="./media/migrate-storm-to-spark/new-spark-deployment.png" alt-text="ny Spark-distribution i HDInsight" border="false":::

1. **Sluta förbruka det gamla Storm-klustret.** I den befintliga Storm slutar du att förbruka data från den strömmande data källan och väntar på att data ska skrivas till mål mottagaren.

   :::image type="content" source="./media/migrate-storm-to-spark/stop-consuming-current-cluster.png" alt-text="sluta använda det aktuella klustret" border="false":::

1. **Börja använda det nya Spark-klustret.** Starta strömmande data från ett nyligen distribuerat HDInsight 4,0 Spark-kluster. Vid detta tillfälle tas processen över genom att använda den senaste Kafka-förskjutningen.

   :::image type="content" source="./media/migrate-storm-to-spark/start-consuming-new-cluster.png" alt-text="börja använda nya kluster" border="false":::

1. **Ta bort det gamla klustret efter behov.** När växeln har slutförts och fungerar korrekt, tar du bort det gamla HDInsight 3,6 Storm-klustret vid behov.

   :::image type="content" source="./media/migrate-storm-to-spark/remove-old-clusters1.png" alt-text="ta bort gamla HDInsight-kluster efter behov" border="false":::

## <a name="next-steps"></a>Nästa steg

Mer information om Storm, Spark streaming och Spark-strukturerad strömning finns i följande dokument:

* [Översikt över Apache Spark strömning](../spark/apache-spark-streaming-overview.md)
* [Översikt över Apache Spark strukturerad strömning](../spark/apache-spark-structured-streaming-overview.md)
