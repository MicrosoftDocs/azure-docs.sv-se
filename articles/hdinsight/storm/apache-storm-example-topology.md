---
title: Exempel Apache Storm topologier i Azure HDInsight
description: En lista över exempel på Storm-topologier som skapats och testats med Apache Storm på HDInsight, inklusive Basic C#-och Java-topologier, och arbetar med Event Hubs.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/27/2019
ms.openlocfilehash: 8dabfec18cb904fa72518428220991b817b53529
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98928915"
---
# <a name="example-apache-storm-topologies-and-components-for-apache-storm-on-hdinsight"></a>Exempel Apache Storm topologier och komponenter för Apache Storm i HDInsight

Följande är en lista över exempel som skapats och underhålls av Microsoft för användning med [Apache Storm](https://storm.apache.org/) i HDInsight. De här exemplen innehåller flera olika ämnen, från att skapa Basic C#-och Java-topologier för att arbeta med Azure-tjänster som Event Hubs, Cosmos DB, SQL Database, [Apache HBase](https://hbase.apache.org/) på HDInsight och Azure Storage. Några exempel visar hur du arbetar med icke-Azure, eller till och med icke-Microsoft-tekniker, till exempel SignalR och Socket.IO.

| Beskrivning | Demonstrationer | Språk/ramverk |
|:--- |:--- |:--- |
| [Skriv till Azure Data Lake Storage från Apache Storm](apache-storm-write-data-lake-store.md) |Skriver till Azure Data Lake Storage |Java |
| [Event Hub-kanalen och bult-källa](https://github.com/apache/storm/tree/master/external/storm-eventhubs) |Källa för Event Hub-kanalen och bult |Java |
| [Utveckla Java-baserade topologier för Apache Storm i HDInsight][5797064f] |Maven |Java |
| [Utveckla C#-topologier för Apache Storm på HDInsight med Visual Studio][16fce2d1] |HDInsight Tools för Visual Studio |C#, Java |
| [Bearbeta händelser från Azure Event Hubs med Apache Storm på HDInsight (C#)][844d1d81] |Event Hubs |C# och Java |
| [Process events from Azure Event Hubs with Storm on HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub) (Bearbeta händelser från Azure Event Hubs med Storm i HDInsight (Java)) |Event Hubs |Java |
| [Bearbeta fordons sensor data från Event Hubs med Apache Storm i HDInsight][246ee964] |Event Hubs, Cosmos DB, Azure Storage Blob (WASB) |C#, Java |
| [Extrahera, transformera och läsa in (ETL) från Azure Event Hubs till Apache HBase med Apache Storm på HDInsight][b4b68194] |Event Hubs, HBase |C# |
| [Mall C# Storm Topology Project för att arbeta med Azure-tjänster från Apache Storm på HDInsight][ce0c02a2] |Event Hubs, Cosmos DB, SQL Database, HBase, SignalR |C#, Java |
| [Prestandatester för att läsa från Azure Event Hubs att använda Apache Storm på HDInsight][d6c540e3] |Meddelande data flöde, Event Hubs SQL Database |C#, Java |
| [Använda Apache Kafka med Apache Storm på HDInsight](../hdinsight-apache-storm-with-kafka.md) | Apache Storm läsning och skrivning till Apache Kafka | Java |

> [!WARNING]  
> C#-exemplen i den här listan har ursprungligen skapats och testats med Windows-baserade HDInsight och kanske inte fungerar korrekt med Linux-baserade HDInsight-kluster. Linux-baserade kluster använder mono för att köra .NET-kod och kan ha kompatibilitetsproblem med de ramverk och paket som används i exemplet.
>
> Linux är det enda operativ system som används på HDInsight version 3,4 eller senare.

## <a name="python-only"></a>Endast Python

Se [använda python med Apache storm på HDInsight](apache-storm-develop-python-topology.md) för att se ett exempel på python-komponenter med en flödes sto pol Ogin.

## <a name="next-steps"></a>Nästa steg

* [Skapa och övervaka en Apache Storm topologi i Azure HDInsight](./apache-storm-quickstart.md)
* [Lär dig hur du distribuerar och hanterar Apache Storm topologier med Apache Storm på HDInsight][6eb0d3b8]

[6eb0d3b8]:apache-storm-deploy-monitor-topology-linux.md "Lär dig hur du distribuerar och hanterar topologier med hjälp av den webbaserade Apache Storm instrument panelen och Storm-ANVÄNDARGRÄNSSNITTET eller HDInsight-verktygen för Visual Studio."
[16fce2d1]:apache-storm-develop-csharp-visual-studio-topology.md "Lär dig hur du skapar C# Storm-topologier med hjälp av HDInsight-verktyg för Visual Studio."
[5797064f]:apache-storm-develop-java-topology.md "Lär dig hur du skapar Storm-topologier i Java med maven, genom att skapa en grundläggande WORDCOUNT-topologi."
[844d1d81]:apache-storm-develop-csharp-event-hub-topology.md "Lär dig att läsa och skriva data från Azure Event Hubs med storm på HDInsight."
[246ee964]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/IotExample/README.md "Lär dig hur du använder en Storm-topologi för att läsa meddelanden från Azure Event Hubs, läsa dokument från Azure Cosmos DB för data som refererar till och spara data till Azure Storage."
[d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Flera topologier för att demonstrera data flöde vid läsning från Azure Event Hubs och lagring till SQL Database med Apache Storm i HDInsight."
[b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Lär dig hur du läser data från Azure Event Hubs, agg regerings & transformera data och lagra dem på HBase i HDInsight."
[ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Det här projektet innehåller mallar för kanaler, bultar och topologier för att interagera med olika Azure-tjänster som Event Hubs, Cosmos DB och SQL Database."
