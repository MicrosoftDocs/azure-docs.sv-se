---
title: Spark-strukturerad strömning i Azure HDInsight
description: Använda Spark-strukturerade strömnings program i HDInsight Spark-kluster.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/24/2019
ms.openlocfilehash: fd65177fb6202b0396545043c2e63a87c7f01bbb
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "104864609"
---
# <a name="overview-of-apache-spark-structured-streaming"></a>Översikt över Apache Spark strukturerad strömning

[Apache Spark](https://spark.apache.org/) Strukturerad direkt uppspelning gör det möjligt att implementera skalbara, stora data flöden, feltoleranta program för bearbetning av data strömmar. Strukturerad strömning bygger på Spark SQL-motorn och förbättrar konstruktionerna från Spark SQL data-ramar och data uppsättningar så att du kan skriva direkt uppspelnings frågor på samma sätt som du skulle skriva batch-frågor.  

Strukturerade strömmande program körs på HDInsight Spark-kluster och ansluter till strömmande data från [Apache Kafka](https://kafka.apache.org/), en TCP-socket (för fel söknings syfte), Azure Storage eller Azure Data Lake Storage. De två sistnämnda alternativen, som är beroende av externa lagrings tjänster, gör att du kan se om nya filer har lagts till i lagringen och bearbeta innehållet som om de var strömmande.

Strukturerad direkt uppspelning skapar en långvarig fråga där du kan tillämpa åtgärder på indata, t. ex. urval, projektion, agg regering, fönster och anslutning av strömnings DataFrame med referens DataFrames. Därefter skickar du resultaten till File Storage (Azure Storage blobbar eller Data Lake Storage) eller till alla data lager med hjälp av anpassad kod (till exempel SQL Database eller Power BI). Strukturerad direkt uppspelning ger också utdata till-konsolen för fel sökning lokalt och till en InMemory-tabell så att du kan se de data som genereras för fel sökning i HDInsight.

:::image type="content" source="./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming.png" alt-text="Strömnings bearbetning med HDInsight och Spark strukturerad strömning" border="false":::

> [!NOTE]  
> Spark-strukturerad strömning ersätter Spark streaming (DStreams). I framtiden kommer strukturerad strömning att få förbättringar och underhåll, medan DStreams endast är i underhålls läge. Strukturerad direkt uppspelning är för närvarande inte som funktion – fullständig som DStreams för källorna och de handfat som den stöder utanför lådan, så du kan utvärdera dina krav för att välja lämpligt bearbetnings alternativ för Spark-strömmar.

## <a name="streams-as-tables"></a>Strömmar som tabeller

Spark-strukturerad direkt uppspelning representerar en data ström som en tabell som är obegränsad i djup, det vill säga att tabellen fortsätter att växa när nya data tas emot. Den här *inmatnings tabellen* bearbetas kontinuerligt av en lång körnings fråga och resultatet som skickas till en *utgående tabell*:

:::image type="content" source="./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-concept.png" alt-text="Strukturerat strömnings begrepp" border="false":::

I strukturerad strömning tas data emot i systemet och matas direkt in i en inmatnings tabell. Du skriver frågor (med hjälp av DataFrame och data uppsättnings-API: er) som utför åtgärder mot den här indata-tabellen. Frågeresultatet ger till gång till en annan tabell, *resultat tabellen*. Resultat tabellen innehåller resultatet från frågan, från vilken du ritar data för ett externt data lager, till exempel en Relations databas. Tiden för när data bearbetas från inmatnings tabellen styrs av *utlösarens intervall*. Som standard är utlösnings intervallet noll, så att strukturerad strömning försöker bearbeta data så fort de anländer. I praktiken innebär det att så fort Structured streaming utförs bearbetningen av den föregående frågans körning, startas en annan bearbetnings körning mot eventuella nyligen mottagna data. Du kan konfigurera utlösaren att köras med ett intervall, så att strömmande data bearbetas i tidsbaserade batchar.

Data i resultat tabellerna får bara innehålla data som är nya sedan frågan senast bearbetades (*tilläggs läge*), eller så kan tabellen uppdateras varje gång det finns nya data så att tabellen innehåller alla utdata sedan den strömmade frågan startade (*slutfört läge*).

### <a name="append-mode"></a>Tilläggs läge

I läget append finns bara de rader som har lagts till i resultat tabellen sedan den senaste frågan som kördes i resultat tabellen och som skrivs till extern lagring. Den enklaste frågan kopierar till exempel bara alla data från inmatnings tabellen till tabellen Results utan att ändra den. Varje gången ett utlösnings intervall förflutit bearbetas de nya data och raderna som representerar nya data visas i resultat tabellen.

Överväg ett scenario där du bearbetar telemetri från temperatur sensorer, till exempel en termostat. Anta att den första utlösaren bearbetade en händelse vid tiden 00:01 för enhet 1 med en temperatur läsning på 95 grader. I den första utlösaren av frågan visas bara raden med tiden 00:01 i resultat tabellen. Vid tid 00:02 när en annan händelse anländer är den enda nya raden raden med tiden 00:02 och så att tabellen result bara innehåller en rad.

:::image type="content" source="./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-append-mode.png" alt-text="Tilläggs läge för strukturerad strömning" border="false":::

När du använder läget för tillägg skulle din fråga tillämpa projektioner (Markera de kolumner som det värnar om), filtrera (plocka bara rader som matchar vissa villkor) eller Anslut (utöka data med data från en statisk uppslags tabell). Append-läge gör det enkelt att bara skicka relevanta nya data punkter ut till extern lagring.

### <a name="complete-mode"></a>Fullständigt läge

Överväg samma scenario, den här gången med slutfört läge. I komplett läge uppdateras hela utdatatabellen vid varje utlösare, så att tabellen innehåller data som inte är från den senaste utlösaren, men från alla körningar. Du kan använda fullständig läge för att kopiera data som inte har ändrats från inmatnings tabellen till resultat tabellen. Vid varje utlöst körning visas nya resultat rader tillsammans med alla föregående rader. Resultat tabellen för utdata kommer att lagra alla data som samlats in sedan frågan började och du skulle då få slut på minne. Fullständigt läge är avsett för användning med sammanställda frågor som sammanfattar inkommande data på ett visst sätt, så vid varje utlösare uppdateras resultat tabellen med en ny sammanfattning.

Anta att det finns fem sekunders värd för data som redan har bearbetats och det är dags att bearbeta data för den sjätte sekunden. Indatalist-tabellen innehåller händelser för tid 00:01 och tid 00:03. Målet för den här exempel frågan är att ge enhetens genomsnittliga temperatur var femte sekund. Implementeringen av den här frågan använder en agg regering som tar alla värden som ingår i varje 5-sekunders fönster, beräknar genomsnitts temperatur och genererar en rad för den genomsnittliga temperaturen under intervallet. I slutet av det första fem-andra fönstret finns det två tupler: (00:01, 1, 95) och (00:03, 1, 98). Så för Window 00:00-00:05 genererar agg regeringen en tupel med genomsnitts temperaturen på 96,5 grader. I nästa 5-Second-fönster finns det bara en data punkt vid tiden 00:06, så den resulterande genomsnitts temperaturen är 98 grader. Vid tid 00:10, med slutfört läge, innehåller resultat tabellen raderna för både Windows 00:00-00:05 och 00:05-00:10 eftersom frågan matar ut alla aggregerade rader, inte bara de nya. Därför fortsätter tabellen resultat att växa när nya fönster läggs till.

:::image type="content" source="./media/apache-spark-structured-streaming-overview/hdinsight-spark-structured-streaming-complete-mode.png" alt-text="Fullständigt läge för strukturerad strömning" border="false":::

Alla frågor som använder fullständigt läge gör att tabellen växer utan gränser.  Överväg i föregående exempel i stället för att beräkna genomsnitts perioden för temperatur per tid i stället för enhets-ID. Resultat tabellen innehåller ett fast antal rader (en per enhet) med genomsnitts temperaturen för enheten över alla data punkter som tas emot från enheten. När nya temperaturer tas emot uppdateras resultat tabellen så att medelvärdena i tabellen alltid är aktuella.

## <a name="components-of-a-spark-structured-streaming-application"></a>Komponenter i ett Spark-strukturerat strömmande program

En enkel exempel fråga kan sammanfatta temperatur avläsningar per timme-lång fönster. I det här fallet lagras data i JSON-filer i Azure Storage (bifogas som standard lagring för HDInsight-klustret):

```json
{"time":1469501107,"temp":"95"}
{"time":1469501147,"temp":"95"}
{"time":1469501202,"temp":"95"}
{"time":1469501219,"temp":"95"}
{"time":1469501225,"temp":"95"}
```

Dessa JSON-filer lagras i `temps` undermappen under HDInsight-klustrets behållare.

### <a name="define-the-input-source"></a>Definiera Indatakällan

Konfigurera först en DataFrame som beskriver data källan och de inställningar som krävs av källan. Det här exemplet drar från JSON-filerna i Azure Storage och använder ett schema för dem vid Läs tillfället.

```sql
import org.apache.spark.sql.types._
import org.apache.spark.sql.functions._

//Cluster-local path to the folder containing the JSON files
val inputPath = "/temps/" 

//Define the schema of the JSON files as having the "time" of type TimeStamp and the "temp" field of type String
val jsonSchema = new StructType().add("time", TimestampType).add("temp", StringType)

//Create a Streaming DataFrame by calling readStream and configuring it with the schema and path
val streamingInputDF = spark.readStream.schema(jsonSchema).json(inputPath)
``` 

#### <a name="apply-the-query"></a>Tillämpa frågan

Sedan använder du en fråga som innehåller önskade åtgärder mot strömnings DataFrame. I det här fallet har en agg regering grupper alla rader i en timmes Windows-period och sedan beräknar de lägsta, genomsnittliga och högsta temperaturerna i det 1-timmarsformat.

```sql
val streamingAggDF = streamingInputDF.groupBy(window($"time", "1 hour")).agg(min($"temp"), avg($"temp"), max($"temp"))
```

### <a name="define-the-output-sink"></a>Definiera utgående Sink

Definiera sedan målet för de rader som läggs till i resultat tabellen inom varje utlösnings intervall. I det här exemplet matas bara alla rader till en InMemory-tabell `temps` som du senare kan fråga med SparkSQL. Fullständig utmatnings läge ser till att alla rader för alla fönster skrivs ut varje gång.

```sql
val streamingOutDF = streamingAggDF.writeStream.format("memory").queryName("temps").outputMode("complete")
``` 

### <a name="start-the-query"></a>Starta frågan

Starta direkt uppspelnings frågan och kör tills en avslutnings signal tas emot.

```sql
val query = streamingOutDF.start() 
``` 

### <a name="view-the-results"></a>Visa resultatet

Även om frågan körs, i samma SparkSession, kan du köra en SparkSQL-fråga mot den `temps` tabell där frågeresultatet lagras.

```sql
select * from temps
```

Den här frågan ger resultat som liknar följande:

| fönster |  min (temp) | AVG (temp) | Max (temp) |
| --- | --- | --- | --- |
|{u'start ': u ' 2016-07-26T02:00:00.000 Z ', u'end '... |    95 |    95,231579 | 99 |
|{u'start ': u ' 2016-07-26T03:00:00.000 Z ', u'end '...  |95 |   96,023048 | 99 |
|{u'start ': u ' 2016-07-26T04:00:00.000 Z ', u'end '...  |95 |   96,797133 | 99 |
|{u'start ': u ' 2016-07-26T05:00:00.000 Z ', u'end '...  |95 |   96,984639 | 99 |
|{u'start ': u ' 2016-07-26T06:00:00.000 Z ', u'end '...  |95 |   97,014749 | 99 |
|{u'start ': u ' 2016-07-26T07:00:00.000 Z ', u'end '...  |95 |   96,980971 | 99 |
|{u'start ': u ' 2016-07-26T08:00:00.000 Z ', u'end '...  |95 |   96,965997 | 99 |  

Mer information om Spark-API: et för Spark, tillsammans med de inmatnings data källor, åtgärder och utgående mottagare som stöds, finns i [Apache Spark strukturerad programmerings guide](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html).

## <a name="checkpointing-and-write-ahead-logs"></a>Kontroll punkts-och skriv loggar

För att leverera återhämtnings-och fel tolerans är strukturerad strömning beroende av *kontroll punkter* för att säkerställa att Stream-bearbetningen kan fortsätta oavbrutet, även vid nodfel. I HDInsight skapar Spark kontroll punkter till varaktig lagring, antingen Azure Storage eller Data Lake Storage. Dessa kontroll punkter lagrar förlopps informationen om direkt uppspelnings frågan. Dessutom använder Structured streaming en logg för att *skriva framåt* (Wal). WAL fångar in inmatade data som har tagits emot men ännu inte bearbetats av en fråga. Om ett fel uppstår och bearbetningen startas om från WAL går inga händelser som tas emot från källan förlorade.

## <a name="deploying-spark-streaming-applications"></a>Distribuera program för Spark-direktuppspelning

Du skapar vanligt vis ett Spark streaming-program lokalt i en JAR-fil och distribuerar det sedan till Spark på HDInsight genom att kopiera JAR-filen till standard lagringen som är kopplad till ditt HDInsight-kluster. Du kan starta ditt program med de [Apache livy](https://livy.incubator.apache.org/) REST API: er som finns tillgängliga från klustret med en post-åtgärd. Innehållet i inlägget innehåller ett JSON-dokument som ger din JAR-sökväg, namnet på klassen vars huvudsakliga metod definierar och kör strömnings programmet, och eventuellt resurs kraven för jobbet (till exempel antalet körningar, minne och kärnor) och alla konfigurations inställningar som program koden kräver.

:::image type="content" source="./media/apache-spark-streaming-overview/hdinsight-spark-streaming-livy.png" alt-text="Distribuera ett Spark streaming-program" border="false":::

Status för alla program kan också kontrol leras med en GET-begäran mot en LIVY-slutpunkt. Slutligen kan du avsluta ett program som körs genom att utfärda en DELETE-begäran mot LIVY-slutpunkten. Mer information om LIVY-API: et finns i [Fjärrjobb med Apache LIVY](apache-spark-livy-rest-interface.md)

## <a name="next-steps"></a>Nästa steg

* [Skapa ett Apache Spark-kluster i HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md)
* [Programmerings guide för Apache Spark strukturerad strömning](https://spark.apache.org/docs/2.1.0/structured-streaming-programming-guide.html)
* [Starta Apache Spark jobb via en fjärr anslutning med Apache LIVY](apache-spark-livy-rest-interface.md)
