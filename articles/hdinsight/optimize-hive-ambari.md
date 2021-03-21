---
title: Optimera Apache Hive med Apache Ambari i Azure HDInsight
description: Använd webb gränssnittet Apache Ambari för att konfigurera och optimera Apache Hive.
ms.service: hdinsight
ms.topic: how-to
ms.date: 05/04/2020
ms.openlocfilehash: 349f58720e6fff52191dfff65108cd1320e41eed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "98939251"
---
# <a name="optimize-apache-hive-with-apache-ambari-in-azure-hdinsight"></a>Optimera Apache Hive med Apache Ambari i Azure HDInsight

Apache Ambari är ett webb gränssnitt för att hantera och övervaka HDInsight-kluster. En introduktion till Ambari-webbgränssnitt finns i [Hantera HDInsight-kluster med hjälp av Apache Ambari Web UI](hdinsight-hadoop-manage-ambari.md).

I följande avsnitt beskrivs konfigurations alternativ för att optimera övergripande Apache Hive prestanda.

1. Om du vill ändra konfigurations parametrar för Hive väljer du **Hive** från tjänsternas sid List.
1. Navigera till fliken **konfigurationer** .

## <a name="set-the-hive-execution-engine"></a>Ange körnings motor för Hive

Hive innehåller två körnings motorer: Apache Hadoop MapReduce och Apache TEZ. Tez är snabbare än MapReduce. HDInsight Linux-kluster har Tez som standard körnings motor. Ändra körnings motorn:

1. Skriv **körnings motor** i rutan filter på fliken Hive- **konfigurationer** .

    ![Ambari för att söka körnings motor för Apache](./media/optimize-hive-ambari/ambari-search-execution.png)

1. Standardvärdet för egenskapen **Optimization** är **Tez**.

    ![Optimering – Apache Tez-motor](./media/optimize-hive-ambari/optimization-apache-tez.png)

## <a name="tune-mappers"></a>Finjustera mappningar

Hadoop försöker dela (*Mappa*) en enskild fil i flera filer och bearbeta de resulterande filerna parallellt. Antalet mappningar beror på antalet delningar. Följande två konfigurations parametrar driver antalet delningar för Tez körnings motor:

* `tez.grouping.min-size`: Sänk gränsen för en grupperad delnings storlek med standardvärdet 16 MB (16 777 216 byte).
* `tez.grouping.max-size`: Den övre gränsen för en grupperad delnings storlek med standardvärdet 1 GB (1 073 741 824 byte).

Som en prestanda rikt linje sänker du båda parametrarna för att förbättra svars tiden, öka för mer data flöde.

Om du till exempel vill ange fyra Mapper-uppgifter för en data storlek på 128 MB anger du båda parametrarna till 32 MB vardera (33 554 432 byte).

1. Om du vill ändra gräns parametrarna navigerar du till fliken **configs** i Tez-tjänsten. Expandera panelen **Allmänt** och leta upp `tez.grouping.max-size` `tez.grouping.min-size` parametrarna och.

1. Ange båda parametrarna till **33 554 432** byte (32 MB).

    ![Apache Ambari Tez-grupperingsinställningar](./media/optimize-hive-ambari/apache-tez-grouping-size.png)

Dessa ändringar påverkar alla Tez-jobb på servern. Om du vill få ett optimalt resultat väljer du lämpliga parameter värden.

## <a name="tune-reducers"></a>Finjustera dämpare

Apache ORC och fästfunktionen ger både höga prestanda. Hive kan dock ha för få Minskare som standard, vilket orsakar Flask halsar.

Anta till exempel att du har en indata-storlek på 50 GB. Dessa data i ORC-format med Fästnings komprimering är 1 GB. Hive beräknar antalet dereducerare som behövs som: (antal byte indata till Mapper/ `hive.exec.reducers.bytes.per.reducer` ).

Med standardinställningarna är det här exemplet fyra reducerare.

`hive.exec.reducers.bytes.per.reducer`Parametern anger antalet byte som bearbetas per minskning. Standardvärdet är 64 MB. Att justera det här värdet nedåt ökar parallellt och kan förbättra prestandan. Om du finjusterar för lågt kan du också skapa för många reducerare, vilket kan påverka prestanda negativt. Den här parametern baseras på dina specifika data krav, komprimerings inställningar och andra miljö faktorer.

1. Om du vill ändra parametern navigerar du till fliken Hive- **konfiguration** och letar upp **data per minsknings** parameter på sidan Inställningar.

    ![Apache Ambari-data per minskning](./media/optimize-hive-ambari/ambari-data-per-reducer.png)

1. Välj **Redigera** för att ändra värdet till 128 MB (134 217 728 byte) och tryck sedan på **RETUR** för att spara.

    ![Ambari data per minsknings-redige rad](./media/optimize-hive-ambari/data-per-reducer-edited.png)
  
    Med en indata storlek på 1 024 MB, med 128 MB data per minskning, finns det åtta Minskare (1024/128).

1. Ett felaktigt värde för **data per minsknings** parameter kan resultera i ett stort antal avreducerare, vilket negativt påverkar frågans prestanda. Ange ett lämpligt värde om du vill begränsa det maximala antalet avreducerare `hive.exec.reducers.max` . Standardvärdet är 1009.

## <a name="enable-parallel-execution"></a>Aktivera parallell körning

En Hive-fråga körs i en eller flera steg. Om de oberoende faserna kan köras parallellt ökar frågans prestanda.

1. Om du vill aktivera parallell frågekörningen går du till fliken Hive- **konfiguration** och söker efter `hive.exec.parallel` egenskapen. Standardvärdet är false. Ändra värdet till sant och spara värdet genom att trycka på **RETUR** .

1. Om du vill begränsa antalet jobb som ska köras parallellt ändrar du `hive.exec.parallel.thread.number` egenskapen. Standardvärdet är 8.

    ![Parallell visning för Apache Hive exec](./media/optimize-hive-ambari/apache-hive-exec-parallel.png)

## <a name="enable-vectorization"></a>Aktivera vectorization

Hive bearbetar data rad för rad. Vectorization dirigerar Hive för att bearbeta data i block om 1 024 rader i stället för en rad i taget. Vectorization kan bara användas för fil formatet ORC.

1. Om du vill aktivera en Vector-frågekörningen navigerar du till fliken Hive- **konfiguration** och söker efter- `hive.vectorized.execution.enabled` parametern. Standardvärdet är true för Hive 0.13.0 eller senare.

1. Om du vill aktivera vektorbaserad körning för frågans minsknings sida anger du `hive.vectorized.execution.reduce.enabled` parametern till true. Standardvärdet är false.

    ![Apache Hive vektorbaserad körning](./media/optimize-hive-ambari/hive-vectorized-execution.png)

## <a name="enable-cost-based-optimization-cbo"></a>Aktivera kostnads baserad optimering (CBO)

Som standard följer Hive en uppsättning regler för att hitta en optimal plan för frågekörningen. Kostnads baserad optimering (CBO) utvärderar flera planer för att köra en fråga. Och tilldelar varje plan en kostnad och bestämmer sedan billigaste-planen för att köra en fråga.

Om du vill aktivera CBO navigerar du till inställningar för **Hive**-  >  **konfiguration**  >   och letar reda på **Aktivera kostnads baserad optimering** och byter sedan växlings knappen till **på**.

![Kostnads baserad, HDInsight-baserad optimering](./media/optimize-hive-ambari/hdinsight-cbo-config.png)

Följande ytterligare konfigurations parametrar ökar prestanda för Hive-frågor när CBO är aktiverat:

* `hive.compute.query.using.stats`

    När värdet är true använder Hive statistik som lagrats i dess metaarkiv för att besvara enkla frågor som `count(*)` .

    ![Apache Hive beräknings fråga med statistik](./media/optimize-hive-ambari/hive-compute-query-using-stats.png)

* `hive.stats.fetch.column.stats`

    Kolumn statistik skapas när CBO är aktive rad. Hive använder kolumn statistik, som lagras i metaarkiv, för att optimera frågor. Att hämta kolumn statistik för varje kolumn tar längre tid när antalet kolumner är högt. Om värdet är false inaktiverar den här inställningen hämtning av kolumn statistik från metaarkiv.

    ![Kolumn statistik för Apache Hive stats uppsättning](./media/optimize-hive-ambari/hive-stats-fetch-column-stats.png)

* `hive.stats.fetch.partition.stats`

    Grundläggande standardpartitions statistik, till exempel antal rader, data storlek och fil storlek, lagras i metaarkiv. Om värdet är true hämtas partitionsstrukturen från metaarkiv. När värdet är false hämtas fil storleken från fil systemet. Antalet rader hämtas från radens schema.

    ![Registrerings statistik för Hive-uppsättning](./media/optimize-hive-ambari/hive-stats-fetch-partition-stats.png)

## <a name="enable-intermediate-compression"></a>Aktivera mellanliggande komprimering

Kart aktiviteter skapar mellanliggande filer som används av minsknings aktiviteterna. Mellanliggande komprimering minskar storleken på mellanliggande fil.

Hadoop-jobb är vanligt vis I/O-Flask halsar. Komprimering av data kan påskynda I/O och övergripande nätverks överföring.

De tillgängliga komprimerings typerna är:

| Format | Verktyg | Integritetsalgoritm | Fil namns tillägg | Delbara? |
| --- | --- | --- | --- | --- |
| Gzip | Gzip | DEFLATE | `.gz` | Inga |
| Bzip2 | Bzip2 | Bzip2 |`.bz2` | Ja |
| LZO | `Lzop` | LZO | `.lzo` | Ja, om det är indexerat |
| Snappy | Ej tillämpligt | Snappy | Snappy | Inga |

Som en allmän regel är det viktigt att det går att dela upp komprimerings metoden, annars får några av mapparna skapats. Om indata är text, `bzip2` är det bästa alternativet. För ORC-format är fästfunktionen det snabbaste komprimerings alternativet.

1. Om du vill aktivera mellanliggande komprimering navigerar du till fliken Hive- **konfigurationer** och anger sedan `hive.exec.compress.intermediate` parametern till true. Standardvärdet är false.

    ![Hive exec komprimera mellanliggande](./media/optimize-hive-ambari/hive-exec-compress-intermediate.png)

    > [!NOTE]  
    > Om du vill komprimera mellanliggande filer väljer du en komprimerings-codec med lägre CPU-kostnad, även om codecen inte har en hög komprimerings effekt.

1. Om du vill ange den mellanliggande komprimerings-codecen lägger du till den anpassade egenskapen `mapred.map.output.compression.codec` i `hive-site.xml` `mapred-site.xml` filen eller.

1. Så här lägger du till en anpassad inställning:

    a. Navigera till **Hive**  >  -**konfigurationar**  >  **Avancerad**  >  **anpassad Hive-plats**.

    b. Välj **Lägg till egenskap...** längst ned i fönstret för den anpassade Hive-platsen.

    c. I fönstret Lägg till egenskap anger du `mapred.map.output.compression.codec` som nyckel och `org.apache.hadoop.io.compress.SnappyCodec` som värde.

    d. Välj **Lägg till**.

    ![Apache Hive anpassad egenskap Lägg till](./media/optimize-hive-ambari/hive-custom-property.png)

    Den här inställningen komprimerar den mellanliggande filen med hjälp av Fästnings komprimering. När egenskapen har lagts till visas den i fönstret anpassad Hive-webbplats.

    > [!NOTE]  
    > Den här proceduren ändrar `$HADOOP_HOME/conf/hive-site.xml` filen.

## <a name="compress-final-output"></a>Komprimera slutgiltiga utdata

Den slutliga Hive-utdata kan också komprimeras.

1. Om du vill komprimera den slutliga Hive-utdata navigerar du till fliken Hive- **konfiguration** och anger sedan `hive.exec.compress.output` parametern till true. Standardvärdet är false.

1. Om du vill välja komprimerings-codecen för utdata lägger du till den `mapred.output.compression.codec` anpassade egenskapen i fönstret anpassad Hive-plats, enligt beskrivningen i föregående avsnitt: steg 3.

    ![Apache Hive anpassad egenskap ADD2](./media/optimize-hive-ambari/hive-custom-property2.png)

## <a name="enable-speculative-execution"></a>Aktivera spekulativ körning

Spekulativ körning startar ett visst antal dubbla aktiviteter för att identifiera och neka en lista över den långsamma aktivitetens spårare. Förbättra den övergripande jobb körningen genom att optimera enskilda aktivitets resultat.

Spekulativ körning bör inte aktive ras för långvariga MapReduce-uppgifter med stora mängder ingångar.

* Om du vill aktivera spekulativ körning navigerar du till fliken Hive- **konfiguration** och anger sedan `hive.mapred.reduce.tasks.speculative.execution` parametern till true. Standardvärdet är false.

    ![Hive-mapred minskar antalet aktiviteter för spekulativ körning](./media/optimize-hive-ambari/hive-mapred-reduce-tasks-speculative-execution.png)

## <a name="tune-dynamic-partitions"></a>Finjustera dynamiska partitioner

Hive gör det möjligt att skapa dynamiska partitioner när poster infogas i en tabell, utan att fördefiniera varje partition. Den här möjligheten är en kraftfull funktion. Även om det kan resultera i att ett stort antal partitioner skapas. Och ett stort antal filer för varje partition.

1. För att Hive ska kunna utföra dynamiska partitioner `hive.exec.dynamic.partition` ska parametervärdet vara sant (standardvärdet).

1. Ändra läget för dynamisk partition till *strict*. I strikt läge måste minst en partition vara statisk. Den här inställningen förhindrar frågor utan partitions filtret i WHERE-satsen, det vill säga *strikt* förhindrar frågor som genomsöker alla partitioner. Gå till fliken Hive- **konfiguration** och ange sedan `hive.exec.dynamic.partition.mode` **strikt**. Standardvärdet är icke- **strikt**.

1. Ändra parametern om du vill begränsa antalet dynamiska partitioner som ska skapas `hive.exec.max.dynamic.partitions` . Standardvärdet är 5000.

1. Om du vill begränsa det totala antalet dynamiska partitioner per nod ändrar du `hive.exec.max.dynamic.partitions.pernode` . Standardvärdet är 2000.

## <a name="enable-local-mode"></a>Aktivera lokalt läge

Lokalt läge gör det möjligt för Hive att utföra alla aktiviteter för ett jobb på en enskild dator. Eller ibland i en enda process. Den här inställningen förbättrar frågans prestanda om indata är små. När du startar uppgifter för frågor förbrukar vi en betydande del av den övergripande körningen av frågan.

Om du vill aktivera lokalt läge lägger du till `hive.exec.mode.local.auto` parametern i den anpassade Hive-plats panelen, enligt beskrivningen i steg 3 i avsnittet [Aktivera mellanliggande komprimering](#enable-intermediate-compression) .

![Apache Hive exec-läge lokalt Auto](./media/optimize-hive-ambari/hive-exec-mode-local-auto.png)

## <a name="set-single-mapreduce-multigroup-by"></a>Ange Single MapReduce GROUP BY

När den här egenskapen har angetts till True genererar en multigroup BY-fråga med vanliga Group-by-nycklar ett enda MapReduce-jobb.  

Om du vill aktivera det här beteendet lägger du till `hive.multigroupby.singlereducer` parametern i fönstret anpassad Hive-plats, enligt beskrivningen i steg 3 i avsnittet [Aktivera mellanliggande komprimering](#enable-intermediate-compression) .

![Hive-uppsättning enkel MapReduce multigroup BY](./media/optimize-hive-ambari/hive-multigroupby-singlereducer.png)

## <a name="additional-hive-optimizations"></a>Ytterligare Hive-optimeringar

I följande avsnitt beskrivs ytterligare Hive-relaterade optimeringar som du kan ange.

### <a name="join-optimizations"></a>Delta i optimeringar

Standard kopplings typen i Hive är en *blandad koppling*. I Hive läser särskilda mappningar indata och genererar ett kopplings nyckel/värde-par till en mellanliggande fil. Hadoop sorterar och sammanfogar dessa par i en blandad fas. Den här blandade fasen är dyr. Om du väljer höger koppling baserat på dina data kan du förbättra prestanda avsevärt.

| Kopplings typ | När | Hur | Hive-inställningar | Kommentarer |
| --- | --- | --- | --- | --- |
| Blanda koppling | <ul><li>Standard alternativ</li><li>Fungerar alltid</li></ul> | <ul><li>Läser från en del av en av tabellerna</li><li>Buckets och sorteras efter kopplings nyckel</li><li>Skickar en Bucket till varje minskning</li><li>Kopplingen görs på den minimerade Sidan</li></ul> | Ingen väsentlig registrerings inställning krävs | Fungerar varje gång |
| Kart koppling | <ul><li>En tabell får plats i minnet</li></ul> | <ul><li>Läser liten tabell i hash-tabellen för minnet</li><li>Strömmar genom en del av den stora filen</li><li>Kopplar varje post från hash-tabellen</li><li>Kopplingar är endast av mapper</li></ul> | `hive.auto.confvert.join=true` | Snabb, men begränsad |
| Sortera sammanslagnings Bucket | Om båda tabellerna är: <ul><li>Sorterade samma</li><li>Bucket samma</li><li>Ansluter till den sorterade/Bucket kolumnen</li></ul> | Varje process: <ul><li>Läser en Bucket från varje tabell</li><li>Bearbetar raden med det lägsta värdet</li></ul> | `hive.auto.convert.sortmerge.join=true` | Friktionskoefficienten |

### <a name="execution-engine-optimizations"></a>Optimeringar för körnings motor

Ytterligare rekommendationer för att optimera Hive-körnings motorn:

| Inställning | Rekommenderas | HDInsight-standard |
| --- | --- | --- |
| `hive.mapjoin.hybridgrace.hashtable` | Sant = säkrare, långsammare; falskt = snabbare | falskt |
| `tez.am.resource.memory.mb` | 4 GB övre gränser för de flesta | Automatiskt justerad |
| `tez.session.am.dag.submit.timeout.secs` | 300 och | 300 |
| `tez.am.container.idle.release-timeout-min.millis` | 20000 + | 10000 |
| `tez.am.container.idle.release-timeout-max.millis` | 40000 + | 20000 |

## <a name="next-steps"></a>Nästa steg

* [Hantera HDInsight-kluster med webbgränssnittet Apache Ambari](hdinsight-hadoop-manage-ambari.md)
* [Apache Ambari-REST API](hdinsight-hadoop-manage-ambari-rest-api.md)
* [Optimera Apache Hive-frågor i Azure HDInsight](./hdinsight-hadoop-optimize-hive-query.md)
* [Optimera kluster](./optimize-hive-ambari.md)
* [Optimera Apache HBase](./optimize-hbase-ambari.md)
* [Optimera Apache Pig](./optimize-pig-ambari.md)
