---
title: Vad är Apache Storm – Azure HDInsight
description: Med Apache Storm kan du bearbeta dataströmmar i realtid. Med Azure HDInsight kan du enkelt skapa Storm-kluster på Azure-molnet. Med Visual Studio kan du skapa Storm-lösningar med hjälp av C# och sedan distribuera dem till dina HDInsight Storm-kluster.
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/20/2020
ms.openlocfilehash: 867f042332457ebc5fdd6b1f10ce7fb636309ba8
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2021
ms.locfileid: "104865340"
---
# <a name="what-is-apache-storm-on-azure-hdinsight"></a>Vad är Apache Storm på Azure HDInsight?

[Apache Storm](https://storm.apache.org/) är ett distribuerat, feltolerant beräkningssystem med öppen källkod. Du kan använda Storm för att bearbeta data strömmar i real tid med [Apache Hadoop](../hadoop/apache-hadoop-introduction.md). Storm-lösningar kan även tillhandahålla garanterad bearbetning av data, med möjlighet att på nytt spela upp data som inte kunde bearbetas första gången.

## <a name="why-use-apache-storm-on-hdinsight"></a>Varför ska jag använda Apache Storm på HDInsight?

Storm på HDInsight har följande funktioner:

* __99% serviceavtal (SLA) för Storm drift tid__: storm på HDInsight levereras med fullständig kontinuerlig support. Storm i HDInsight har även ett SLA med 99,9 % drifttid. Det innebär att Microsoft garanterar att ett Storm-kluster har en extern anslutning minst 99,9 procent av tiden. Mer information finns på [Azure-supporten](https://azure.microsoft.com/support/options/). Se även [SLA-information för HDInsight](https://azure.microsoft.com/support/legal/sla/hdinsight/v1_0/) -dokument.

* Stöder enkel anpassning genom skriptkörning mot ett Storm-kluster när det skapas eller i efterhand. Mer information finns i [Customize HDInsight clusters using script action](../hdinsight-hadoop-customize-cluster-linux.md) (Anpassa HDInsight-kluster med skriptåtgärder).

* **Skapa lösningar på flera språk**: Du kan skriva Storm-komponenter på valfritt språk, till exempel Java, C# och Python.

    * Integrerar Visual Studio med HDInsight för utveckling, hantering och övervakning av C#-topologier. Mer information finns i [Develop C# Storm topologies with the HDInsight Tools for Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md) (Utveckla Storm-topologier i C# med HDInsight Tools för Visual Studio).

    * Stöder Java-gränssnittet Trident. Du kan skapa Storm-topologier som stöder engångsbearbetning av meddelanden, transaktionell datalagringsbeständighet och en uppsättning vanliga Stream Analytics-åtgärder.

* **Dynamisk skalning**: Du kan lägga till eller ta bort arbetsnoder utan att det påverkar de Storm-topologier som körs. Inaktivera och återaktivera topologier som körs för att dra nytta av nya noder som lagts till genom skalnings åtgärder.

* **Skapa strömmande pipelines med flera Azure-tjänster**: storm på HDInsight integreras med andra Azure-tjänster. Till exempel Event Hubs, SQL Database, Azure Storage och Azure Data Lake Storage. En exempel lösning som kan integreras med Azure-tjänster finns i [process händelser från Event Hubs med Apache storm på HDInsight](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub).

En lista över företag som använder Apache Storm för sina lösningar för analys i realtid finns i [Företag som använder Apache Storm](https://storm.apache.org/Powered-By.html).

Information om hur du kommer igång med storm finns i [skapa och övervaka en Apache Storm topologi i Azure HDInsight](apache-storm-quickstart.md).

## <a name="how-does-apache-storm-work"></a>Hur fungerar Apache Storm

Storm kör topologier i stället för de [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html)  -jobb som du kanske känner till. Storm-topologier består av flera komponenter som är ordnade i en riktad acyklisk graf (DAG). Data flödar mellan komponenter i diagrammet. Varje komponent använder en eller flera dataströmmar och kan du generera en eller flera strömmar. Följande diagram illustrerar hur data flödar mellan komponenterna i en grundläggande ordräkningstopologi:

:::image type="content" source="./media/apache-storm-overview/example-apache-storm-topology-diagram.png" alt-text="Exempel på hur komponenter är ordnade i en Storm-topologi" border="false":::

* Kanalkomponenter överför data till en topologi. De sänder en eller flera strömmar till topologin.

* Bultkomponenter förbrukar strömmar som sänts ut från kanaler eller andra bultar. Bultar kan eventuellt sända strömmar till topologin. Bultar ansvarar för att skriva data till extern lagring, till exempel HDFS, Kafka eller HBase.

## <a name="reliability"></a>Tillförlitlighet

Apache Storm ser till att varje inkommande meddelande alltid bearbetas helt, även om dataanalysen är utspridd över hundratals noder.

Nimbus-noden innehåller funktioner som liknar Apache Hadoop JobTracker. Nimbus tilldelar uppgifter till andra noder i ett kluster via Apache ZooKeeper. Zookeeper-noder tillhandahåller samordning för ett kluster och underlättar kommunikationen mellan Nimbus och övervaknings processen på arbetsnoderna. Om en nod för bearbetning kraschar informeras Nimbus-noden och uppgiften och tillhörande data tilldelas till en annan nod.

Standardkonfigurationen för Apache Storm är att bara ha en Nimbus-nod. Storm på HDInsight kör två Nimbus-noder. Om den primära noden kraschar växlar Storm-klustret till den sekundära noden medan den primära noden återställs. Följande diagram illustrerar konfigurationen av uppgiftsflödet för Storm i HDInsight:

:::image type="content" source="./media/apache-storm-overview/storm-diagram-nimbus.png" alt-text="Diagram över nimbus och zookeeper och övervakaren" border="false":::

## <a name="ease-of-use"></a>Enkel att använda

|Användning |Beskrivning |
|---|---|
|Secure Shell (SSH)-anslutning|Du kan komma åt huvudnoderna i ditt Storm-kluster via Internet med hjälp av SSH. Du kan köra kommandon direkt i klustret med hjälp av SSH. Mer information finns i [använda SSH med HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).|
|Webb anslutning|Alla HDInsight-kluster tillhandahåller Ambari-webbgränssnittet. Du kan enkelt övervaka, konfigurera och hantera tjänster i klustret med hjälp av Ambari-webbanvändargränssnittet. Storm-kluster ger också Storm-användargränssnitt. Du kan övervaka och hantera Storm-topologier som körs från din webbläsare med hjälp av Storm-användargränssnittet. Mer information finns i [Hantera HDInsight med hjälp av Apache Ambari-WEBBgränssnittet](../hdinsight-hadoop-manage-ambari.md) och [övervaka och hantera med hjälp av Apache Storm UI](apache-storm-deploy-monitor-topology-linux.md#monitor-and-manage-a-topology-using-the-storm-ui) -dokument.|
|Azure PowerShell och Azure CLI|PowerShell och Azure CLI tillhandahåller båda kommando rads verktyg som du kan använda från ditt klient system för att arbeta med HDInsight och andra Azure-tjänster.|
|Integrering med Visual Studio|Azure Data Lake Tools för Visual Studio innehåller projektmallar för att skapa C# Storm-topologier med SCP.NET-ramverket. Data Lake Tools innehåller också verktyg för att distribuera, övervaka och hantera lösningar med Storm i HDInsight. Mer information finns i [Develop C# Storm topologies with the HDInsight Tools for Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md) (Utveckla Storm-topologier i C# med HDInsight Tools för Visual Studio).|

## <a name="integration-with-other-azure-services"></a>Integration med andra Azure-tjänster

* __Azure Data Lake Storage__: se [använda Azure Data Lake Storage med Apache storm i HDInsight](apache-storm-write-data-lake-store.md).

* __Event Hubs__: Ett exempel på hur du använder Event Hubs med Storm-kluster finns i följande exempel:

    * [Bearbeta händelser från Azure Event Hubs med Apache Storm på HDInsight (Java)](https://github.com/Azure-Samples/hdinsight-java-storm-eventhub)

    * [Bearbeta händelser från Azure Event Hubs med Apache Storm på HDInsight (C#)](apache-storm-develop-csharp-event-hub-topology.md)

* __SQL Database__, __Cosmos DB__, __Event Hubs__ och __HBase__: Mallexempel ingår i Data Lake Tools för Visual Studio. Mer information finns i [utveckla en C#-topologi för Apache storm i HDInsight](apache-storm-develop-csharp-visual-studio-topology.md).

## <a name="apache-storm-use-cases"></a>Apache Storm användningsområden

Nedan följer några vanliga scenarier där du kan använda Storm i HDInsight:

* Sakernas Internet (IoT)
* Upptäckt av bedrägerier
* Sociala analyser
* Extrahering, transformering och inläsning (ETL)
* Nätverksövervakning
* Sök
* Mobile engagement

Information om verkliga scenarier finns i [hur företag använder Apache Storm](https://storm.apache.org/Powered-By.html) -dokument.

## <a name="development"></a>Utveckling

.NET-utvecklare kan utforma och implementera topologier i C# med hjälp av Data Lake Tools för Visual Studio. Du kan också skapa hybridtopologier som använder Java- och C#-komponenter. Mer information finns i [Utveckla C#-topologier för Apache Storm på HDInsight med Visual Studio](apache-storm-develop-csharp-visual-studio-topology.md).

Du kan också skapa Java-lösningar med hjälp av valfri IDE. Mer information finns i [utveckla Java-topologier för Apache storm i HDInsight](apache-storm-develop-java-topology.md).

Du kan också utveckla Storm-komponenter med Python. Mer information finns i [utveckla Apache Storm topologier med python i HDInsight](apache-storm-develop-python-topology.md).

## <a name="common-development-patterns"></a>Vanliga utvecklingsmönster

### <a name="guaranteed-message-processing"></a>Garanterad meddelandebehandling

Apache Storm kan ge olika nivåer av garanterad meddelandebehandling. Till exempel garanterar ett grundläggande Storm-program minst en gång och Trident kan garantera exakt en gång efter bearbetningen. Se [garantier för data bearbetning](https://storm.apache.org/about/guarantees-data-processing.html) på Apache.org.

### <a name="ibasicbolt"></a>IBasicBolt

Mönstret för läsning av en tupel, som avger noll eller flera tupler och sedan bekräftar att indataströmmen omedelbart i slutet av Execute-metoden är vanlig. Storm innehåller ett [IBasicBolt](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/IBasicBolt.html)-gränssnitt för att automatisera detta mönster.

### <a name="joins"></a>Kopplingar

Hur dataströmmar kopplas varierar mellan olika program. Du kan till exempel koppla varje tupel från flera strömmar till en ny ström eller bara koppla batchar av tupler för ett speciellt fönster. Oavsett vilket kan du ansluta med hjälp av [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-). Fältetgruppering är ett sätt att definiera hur tupplar skickas till bultar.

I Java-exemplet nedan används fieldsGrouping för att dirigera tupplar som kommer från komponenterna ”1”, ”2” och ”3” till bulten MyJoiner:

```java
builder.setBolt("join", new MyJoiner(), parallelism) .fieldsGrouping("1", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("2", new Fields("joinfield1", "joinfield2")) .fieldsGrouping("3", new Fields("joinfield1", "joinfield2"));
```

### <a name="batches"></a>Batchar

Apache Storm tillhandahåller en intern tidsmekanism som kallas ”tidstuppel”. Du kan ange hur ofta en tidstuppel har genererats i topologin.

Ett exempel på hur du använder en tidstuppel från en C#-komponent finns i [PartialBoltCount.cs](https://github.com/hdinsight/hdinsight-storm-examples/blob/3b2c960549cac122e8874931df4801f0934fffa7/EventCountExample/EventCountTopology/src/main/java/com/microsoft/hdinsight/storm/examples/PartialCountBolt.java).

### <a name="caches"></a>Cachelagring

Minnesintern cachelagring används ofta som en mekanism för snabbare bearbetning eftersom den bevarar ofta använda tillgångar i minnet. Eftersom en topologi distribueras över flera noder, och har flera processer i varje nod, bör du överväga att använda [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-). Använd `fieldsGrouping` för att se till att tupplar som innehåller fälten som används för cachesökning alltid dirigeras till samma process. Denna grupperingsfunktion förhindrar duplicering av cacheposter mellan processer.

### <a name="stream-top-n"></a>Dataström för ”högsta N”

Om topologin är beroende av att ett värde för ”högsta N” beräknas, bör du beräkna värdet för högsta N parallellt. Koppla sedan utdata från dessa beräkningar till ett globalt värde. Den här åtgärden kan utföras med hjälp av [fieldsGrouping](https://storm.apache.org/releases/current/javadocs/org/apache/storm/topology/InputDeclarer.html#fieldsGrouping-java.lang.String-org.apache.storm.tuple.Fields-) för att ställa in fältruttning för parallell bearbetning. Du kan sedan vidarebefordra till en bult som globalt avgör det högsta N-värdet.

Ett exempel på en beräkning av ett ”högsta N”-värde finns i [RollingTopWords](https://github.com/apache/storm/blob/master/examples/storm-starter/src/jvm/org/apache/storm/starter/RollingTopWords.java).

## <a name="logging"></a>Loggning

Storm använder Apache log4j 2 för att logga information. Stora mängder data loggas som standard och det kan vara svårt att gå igenom informationen. Du kan ta med en konfigurationsfil för loggning som en del av Storm-topologin för att styra loggningsbeteendet.

En exempeltopologi som visar hur du konfigurerar loggning finns i ett exempel på [Java-baserad WordCount](apache-storm-develop-java-topology.md) för Storm på HDInsight.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om analys lösningar i real tid med Apache Storm på HDInsight:

* [Skapa och övervaka en Apache Storm topologi i Azure HDInsight](apache-storm-quickstart.md)
* [Exempeltopologier för Apache Storm på HDInsight](apache-storm-example-topology.md)
