---
title: Spark-direktuppspelning & händelse bearbetning exakt en gång – Azure HDInsight
description: Så här konfigurerar du Apache Spark streaming för att bearbeta en händelse en gång och bara en gång.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 11/15/2018
ms.openlocfilehash: 4ba7df665b24a3eba2cd185d85a17bd0ef456b0b
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98929663"
---
# <a name="create-apache-spark-streaming-jobs-with-exactly-once-event-processing"></a>Skapa Apache Spark strömmande jobb med exakt en händelse bearbetning

Program för strömnings bearbetning tar olika metoder för hur de hanterar ombearbetning av meddelanden efter fel i systemet:

* Minst en gång: varje meddelande garanterat bearbetas, men det kan bearbetas mer än en gång.
* Högst en gång: varje meddelande kanske inte bearbetas. Om ett meddelande bearbetas bearbetas det bara en gång.
* Exakt en gång: varje meddelande garanteras att bearbetas en gång och bara en gång.

Den här artikeln visar hur du konfigurerar Spark-direktuppspelning för att uppnå exakt bearbetning.

## <a name="exactly-once-semantics-with-apache-spark-streaming"></a>Exakt en semantik med Apache Spark strömning

Först bör du tänka på hur alla system punkter av fel startar om efter ett problem och hur du kan undvika data förlust. Ett Spark streaming-program har:

* En indatakälla.
* En eller flera mottagar processer som hämtar data från Indatakällan.
* Aktiviteter som bearbetar data.
* En utgående mottagare.
* En driv rutins process som hanterar tids krävande jobb.

Exakt en semantik kräver att inga data förloras när som helst och att meddelande bearbetningen kan startas om, oavsett var fel uppstår.

### <a name="replayable-sources"></a>Omspelnings bara källor

Källan ditt Spark streaming-program läser dina händelser från måste kunna *spelas upp*. Det innebär att i de fall där meddelandet hämtades, men systemet misslyckades innan meddelandet kunde sparas eller bearbetas, måste källan ange samma meddelande igen.

I Azure tillhandahåller både Azure-Event Hubs och [Apache Kafka](https://kafka.apache.org/) i HDInsight omuppspelnings bara källor. Ett annat exempel på en åter uppspelnings bara källa är ett feltolerant fil system som [Apache HADOOP HDFS](https://hadoop.apache.org/docs/r1.2.1/hdfs_design.html), Azure Storage blobbar eller Azure Data Lake Storage, där alla data hålls alltid och när som helst kan du läsa om data i sin helhet.

### <a name="reliable-receivers"></a>Pålitliga mottagare

I Spark-direktuppspelning har källor som Event Hubs och Kafka *pålitliga mottagare*, där varje mottagare håller koll på sin status vid läsning av källan. En tillförlitlig mottagare behåller sitt tillstånd i feltolerant lagring, antingen inom [Apache ZooKeeper](https://zookeeper.apache.org/) eller i Spark-kontrollpunkter som skrivits till HDFS. Om en sådan mottagare Miss lyckas och senare startas om, kan den fortsätta där den slutade.

### <a name="use-the-write-ahead-log"></a>Använd Write-Ahead loggen

Spark Streaming stöder användningen av en Write-Ahead logg där varje mottagen händelse skrivs till Spark-katalogen i feltolerant lagring och sedan lagras i en elastisk distribuerad data uppsättning (RDD). I Azure har den feltoleranta lagringen HDFS som backas upp av antingen Azure Storage eller Azure Data Lake Storage. I ditt Spark streaming-program är Write-Ahead loggen aktive rad för alla mottagare genom att ställa in `spark.streaming.receiver.writeAheadLog.enable` konfigurations inställningen på `true` . Write-Ahead loggen ger fel tolerans för fel i både driv rutinen och körningarna.

För arbets uppgifter som kör uppgifter mot händelse data sker varje RDD av definition både replikerad och fördelad över flera arbetare. Om en aktivitet Miss lyckas eftersom den arbets rutin som körs kraschar, kommer aktiviteten att startas om på en annan arbets tagare som har en replik av händelse data, så händelsen förloras inte.

### <a name="use-checkpoints-for-drivers"></a>Använda kontroll punkter för driv rutiner

Jobb driv rutinerna måste startas om. Om driv rutinen som kör den Spark-strömmande applikationen kraschar, tar den bort alla aktiva mottagare, uppgifter och alla RDD som lagrar händelse data. I så fall måste du kunna spara förloppet för jobbet så att du kan återuppta det senare. Detta åstadkoms genom att kontrol lera att det riktade acykliska diagrammets DAG (DAG) i DStream regelbundet är feltolerant lagrings utrymme. DAG-metadata innehåller den konfiguration som används för att skapa strömnings programmet, de åtgärder som definierar programmet och alla batchar som har placerats i kö men ännu inte slutförts. Med dessa metadata kan en misslyckad driv rutin startas om från kontroll punkts informationen. När driv rutinen startas om, kommer den att starta nya mottagare som själva återställer händelse data till RDD från Write-Ahead loggen.

Kontroll punkter aktive ras i Spark-direktuppspelning i två steg.

1. I StreamingContext-objektet konfigurerar du lagrings Sök vägen för kontroll punkterna:

    ```Scala
    val ssc = new StreamingContext(spark, Seconds(1))
    ssc.checkpoint("/path/to/checkpoints")
    ```

    I HDInsight bör dessa kontroll punkter sparas till standard lagrings utrymmet som är kopplat till klustret, antingen Azure Storage eller Azure Data Lake Storage.

2. Ange sedan ett kontroll punkts intervall (i sekunder) på DStream. Vid varje intervall sparas tillstånds data som härletts från indata-händelsen till lagringen. Beständiga tillstånds data kan minska den beräkning som krävs när du återskapar status från käll händelsen.

    ```Scala
    val lines = ssc.socketTextStream("hostname", 9999)
    lines.checkpoint(30)
    ssc.start()
    ssc.awaitTermination()
    ```

### <a name="use-idempotent-sinks"></a>Använd idempotenta-mottagare

Den destinations mottagare som jobbet skriver resultat för måste kunna hantera situationen där det får samma resultat mer än en gång. Mottagaren måste kunna identifiera sådana dubbletter av resultaten och ignorera dem. En *idempotenta* -mottagare kan anropas flera gånger med samma data utan status ändring.

Du kan skapa idempotenta-mottagare genom att implementera logik som först kontrollerar om det inkommande resultatet finns i data lagret. Om resultatet redan finns bör skrivningen lyckas från Spark-jobbets perspektiv, men i verkligheten ignorerar data lagret dubblettdata. Om resultatet inte finns bör sinken infoga det nya resultatet i dess lagring.

Du kan till exempel använda en lagrad procedur med Azure SQL Database som infogar händelser i en tabell. Den här lagrade proceduren letar först efter händelsen efter nyckel fält och endast när ingen matchande händelse hittas är posten infogad i tabellen.

Ett annat exempel är att använda ett partitionerat fil system, t. ex. Azure Storage blobbar eller Azure Data Lake Storage. I det här fallet behöver mottagar logiken inte kontrol lera om det finns någon fil. Om filen som representerar händelsen finns skrivs den bara över med samma data. Annars skapas en ny fil på den beräknade sökvägen.

## <a name="next-steps"></a>Nästa steg

* [Översikt över Apache Spark strömning](apache-spark-streaming-overview.md)
* [Skapa Apache Spark strömnings jobb med hög tillgänglighet i Apache Hadoop garn](apache-spark-streaming-high-availability.md)
