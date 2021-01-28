---
title: Azure HDInsight affärs kontinuitets arkitekturer
description: Den här artikeln beskriver de olika möjliga affärs kontinuitets arkitekturerna för HDInsight
keywords: Hadoop hög tillgänglighet
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/07/2020
ms.openlocfilehash: f74309370f1489714013344116e7feb9551fbfd5
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98933452"
---
# <a name="azure-hdinsight-business-continuity-architectures"></a>Azure HDInsight affärs kontinuitets arkitekturer

Den här artikeln innehåller några exempel på affärs kontinuitets arkitekturer som du kan tänka på för Azure HDInsight. Tolerans för reducerade funktioner under en katastrof är ett affärs beslut som varierar från ett program till nästa. Det kan vara acceptabelt att vissa program inte är tillgängliga eller delvis tillgängliga med begränsade funktioner eller fördröjd bearbetning under en period. För andra program kan eventuella begränsade funktioner vara oacceptabla.

> [!NOTE]
> Arkitekturerna som presenteras i den här artikeln är inte så omfattande. Du bör utforma dina egna unika arkitekturer när du har gjort objektiva beslut kring förväntad affärs kontinuitet, drifts komplexitet och ägande kostnad.

## <a name="apache-hive-and-interactive-query"></a>Apache Hive och interaktiv fråga

[Hive-replikering v2](https://cwiki.apache.org/confluence/display/Hive/HiveReplicationv2Development#HiveReplicationv2Development-REPLSTATUS) rekommenderas för affärs kontinuitet i HDInsight Hive och interaktiva Query-kluster. De beständiga avsnitten i ett fristående Hive-kluster som behöver replikeras är lagrings skiktet och Hive-metaarkiv. Hive-kluster i ett scenario med flera användare med Enterprise Security Package behöver Azure Active Directory Domain Services-och Ranger-Metaarkiv.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hive-interactive-query.png" alt-text="Arkitektur för Hive och interaktiv fråga":::

Den händelsebaserade Hive-replikeringen konfigureras mellan de primära och sekundära klustren. Detta består av två distinkta faser, start och stegvisa körningar:

* Start replikerar hela Hive-lagret, inklusive Hive-metaarkiv information från primär till sekundär.

* Stegvisa körningar automatiseras på det primära klustret och de händelser som genereras under de stegvisa körningarna spelas upp på det sekundära klustret. Det sekundära klustret fångar upp med de händelser som genereras från det primära klustret, vilket säkerställer att det sekundära klustret är konsekvent med det primära klustrets händelser efter att replikeringen har körts.

Det sekundära klustret behövs bara vid den tidpunkt då replikeringen ska köras, `DistCp` men lagrings-och metastores måste vara permanenta. Du kan välja att sätta upp ett skriptat sekundärt kluster på begäran innan replikering, köra det och sedan avbryta det när replikeringen har slutförts.

Det sekundära klustret är vanligt vis skrivskyddat. Du kan göra det sekundära klustret skrivskyddat, men detta ger ytterligare komplexitet som innebär att replikera ändringarna från det sekundära klustret till det primära klustret.

### <a name="hive-event-based-replication-rpo--rto"></a>RTO för händelsebaserade Hive-replikering &

* Återställnings punkt: data förlust är begränsad till den senaste lyckade stegvisa replikeringen från primär till sekundär.

* RTO: tiden mellan felen och återupptandet av överordnade och underordnade transaktioner med den sekundära.

### <a name="apache-hive-and-interactive-query-architectures"></a>Apache Hive och interaktiva Query-arkitekturer

#### <a name="hive-active-primary-with-on-demand-secondary"></a>Hive aktiv primär primär med sekundär på begäran

I en *aktiv primär klient med sekundär arkitektur på begäran* , skriver program till den aktiva primära regionen medan inget kluster tillhandahålls i den sekundära regionen under normal drift. SQL-Metaarkiv och lagring i den sekundära regionen är permanenta, medan HDInsight-klustret är skriptat och distribueras på begäran bara innan den schemalagda Hive-replikeringen körs.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-on-demand-secondary.png" alt-text="aktiv primär med sekundär på begäran":::

#### <a name="hive-active-primary-with-standby-secondary"></a>Hive aktiv primär med sekundär vänte läge

I en *aktiv primär med sekundär vänte läge* kan program skriva till den aktiva primära regionen medan ett vänte läge som skalas ned sekundärt kluster i skrivskyddat läge körs under normal drift. Under normal drift kan du välja att avlasta regions information läs åtgärder till sekundär.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-standby-secondary.png" alt-text="aktiv primär med sekundär vänte läge":::

Mer information om Hive-replikering och kod exempel hittar [Apache Hive replikering i Azure HDInsight-kluster](./interactive-query/apache-hive-replication.md)

## <a name="apache-spark"></a>Apache Spark

Spark-arbetsbelastningar kan eventuellt inte omfatta en Hive-komponent. Om du vill aktivera Spark SQL-arbetsbelastningar för att läsa och skriva data från Hive delar HDInsight Spark-kluster resursens anpassade metastores från Hive/interaktiva fråge kluster i samma region. I sådana fall måste replikering av Spark-arbetsbelastningar mellan regioner även åtfölja replikering av Hive-metastores och lagring. Failover-scenarierna i det här avsnittet gäller för båda:

* [Spark SQL on sur-tabeller med hjälp av instansen-installation (Hive Warehouse Connector)](./interactive-query/apache-hive-warehouse-connector.md) med hjälp av ett HDInsight-kluster med interaktiva frågor.
* Spark SQL-belastning på icke-syror tabeller med ett HDInsight Hadoop-kluster.

För scenarier där Spark fungerar i fristående läge måste granskade data och lagrade Spark-jar v7 (för livy-jobb) replikeras från den primära regionen till den sekundära regionen regelbundet med hjälp av Azure Data Factory `DistCP` .

Vi rekommenderar att du använder versions kontroll system för att lagra Spark-anteckningsböcker och-bibliotek där de enkelt kan distribueras på primära eller sekundära kluster. Se till att de notebook-baserade och icke-anteckningsbokbaserade lösningarna är för beredda för att läsa in rätt data monteringar i den primära eller sekundära arbets ytan.

Om det finns kundspecifika bibliotek som är utanför det som HDInsight tillhandahåller internt måste de spåras och regelbundet läsas in i det sekundära vänte klustret.  

### <a name="apache-spark-replication-rpo--rto"></a>Apache Spark replikering av återställnings & RTO

* Återställnings punkt: data förlust är begränsad till den senaste lyckade stegvisa replikeringen (Spark och Hive) från primär till sekundär.

* RTO: tiden mellan felen och återupptandet av överordnade och underordnade transaktioner med den sekundära.

### <a name="apache-spark-architectures"></a>Apache Spark arkitekturer

#### <a name="spark-active-primary-with-on-demand-secondary"></a>Spark aktiv primär med sekundär på begäran

Program läser och skriver till Spark-och Hive-kluster i den primära regionen medan inga kluster tillhandahålls i den sekundära regionen under normal drift. SQL-Metaarkiv, Hive-lagring och Spark-lagring är permanenta i den sekundära regionen. Spark-och Hive-klustren är skriptade och distribuerade på begäran. Hive-replikering används för att replikera Hive-lagring och Hive-metastores medan Azure Data Factory `DistCP` kan användas för att kopiera fristående Spark-lagring. Hive-kluster måste distribueras innan varje Hive-replikering körs på grund av beroende `DistCp` beräkningen.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-on-demand-secondary-spark.png" alt-text="aktiv primär Apache Spark arkitektur med sekundär på begäran":::

#### <a name="spark-active-primary-with-standby-secondary"></a>Spark aktiv primär med sekundär vänte läge

Program har Läs-och Skriv behörighet till Spark-och Hive-kluster i den primära regionen medan standby-expanderade Hive-och Spark-kluster i skrivskyddat läge körs i den sekundära regionen under normal drift. Under normal drift kan du välja att avlasta regions struktur och Spark Läs åtgärder till sekundär.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/active-primary-standby-secondary-spark.png" alt-text="aktiv sekundär Apache Spark primär vänte läge ":::

## <a name="apache-hbase"></a>Apache HBase

HBase-export och HBase-replikering är vanliga sätt att aktivera affärs kontinuitet mellan HDInsight HBase-kluster.

HBase-export är en batch-replikeringsrelation som använder export verktyget HBase för att exportera tabeller från det primära HBase-klustret till dess underliggande Azure Data Lake Storage gen 2-lagring. Exporterade data kan sedan nås från det sekundära HBase-klustret och importeras till tabeller som måste finnas i den sekundära. Medan HBase export erbjuder granularitet på tabell nivå, i stegvisa uppdaterings situationer, styr export automatiserings motorn intervallet av stegvisa rader som ska inkluderas i varje körning. Mer information finns i [säkerhets kopiering och replikering av HDInsight-HBase](./hbase/apache-hbase-backup-replication.md#export-then-import).

HBase-replikering använder nära real tids replikering mellan HBase-kluster på ett helt automatiserat sätt. Replikeringen görs på tabell nivå. Antingen alla tabeller eller specifika tabeller kan vara riktade till replikering. HBase-replikeringen är till slut konsekvent, vilket innebär att de senaste ändringarna i en tabell i den primära regionen inte är tillgängliga för alla sekundära zoner omedelbart. Sekundära zoner är garanterat att de blir konsekventa med den primära. HBase-replikering kan konfigureras mellan två eller flera HDInsight HBase-kluster om:

* Primär och sekundär finns i samma virtuella nätverk.
* Primär och sekundär finns i olika peer-virtuella nätverk i samma region.
* Primär och sekundär finns i olika peer-virtuella nätverk i olika regioner.

Mer information finns i [Konfigurera Apache HBase Cluster Replication i Azure Virtual Networks](./hbase/apache-hbase-replication.md).

Det finns några andra sätt att säkerhetskopiera HBase-kluster, t. ex. [Kopiera mappen HBase](./hbase/apache-hbase-backup-replication.md#copy-the-hbase-folder), [Kopiera tabeller](./hbase/apache-hbase-backup-replication.md#copy-tables) och [ögonblicks bilder](./hbase/apache-hbase-backup-replication.md#snapshots).

### <a name="hbase-rpo--rto"></a>HBase & RTO

#### <a name="hbase-export"></a>HBase-export

* Återställnings punkt: data förlust är begränsad till den senaste lyckade batch-stegvis import av den sekundära från den primära.
* RTO: tiden mellan att det inte gick att utföra primära och återuppta i/O-åtgärder på den sekundära.

#### <a name="hbase-replication"></a>HBase-replikering

* Återställningen: data förlust är begränsad till den sista WalEdit-försändelsen som togs emot på den sekundära.
* RTO: tiden mellan att det inte gick att utföra primära och återuppta i/O-åtgärder på den sekundära.

### <a name="hbase-architectures"></a>HBase-arkitekturer

HBase-replikering kan ställas in i tre lägen: utfyllnadstecken-Follower, Leader-Leader och cykliskt.

#### <a name="hbase-replication--leader--follower-model"></a>HBase-replikering: ledare – uppföljnings modell

I den här uppsättningen över regioner är replikering enkelriktad från den primära regionen till den sekundära regionen. Antingen alla tabeller eller vissa tabeller på den primära kan identifieras för enkelriktad replikering. Under normal drift kan det sekundära klustret användas för att betjäna Läs begär anden i sin egen region.

Det sekundära klustret fungerar som ett normalt HBase-kluster som kan vara värd för sina egna tabeller och som kan hantera läsningar och skrivningar från regionala program. Skrivningar i de replikerade tabellerna eller tabellerna som är inbyggda i den sekundära replikeras dock inte tillbaka till den primära.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hbase-leader-follower.png" alt-text="HBase ledare, uppföljnings modell":::

#### <a name="hbase-replication--leader--leader-model"></a>HBase-replikering: ledare – ledar modell

Detta är mycket likt den enkelriktade inställningen, förutom att replikeringen sker i båda riktningarna mellan den primära och den sekundära regionen. Program kan använda båda klustren i Read-Write-lägen och uppdateringar utbyter asynkront mellan dem.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hbase-leader-leader.png" alt-text="HBase ledare, ledar modell":::

#### <a name="hbase-replication-multi-region-or-cyclic"></a>HBase-replikering: flera regioner eller cykliska

Modellen för flera regioner/cykliska replikeringar är en utökning av HBase-replikering och kan användas för att skapa en globalt redundant HBase-arkitektur med flera program som läser och skriver till landsspecifika HBase-kluster. Klustren kan konfigureras i olika kombinationer av ledare/ledare eller ledare/följare beroende på affärs krav.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hbase-cyclic.png" alt-text="HBase cyklisk modell":::

## <a name="apache-kafka"></a>Apache Kafka

Om du vill aktivera tillgänglighet mellan regioner HDInsight 4,0 stöder Kafka-MirrorMaker som kan användas för att underhålla en sekundär replik av det primära Kafka-klustret i en annan region. MirrorMaker fungerar som ett högnivå konsument-och producent par, använder sig av ett särskilt ämne i det primära klustret och genererar till ett ämne med samma namn i den sekundära. Replikering mellan kluster för haveri beredskap med hög tillgänglighet med hjälp av MirrorMaker medföljer antagandet att producenter och konsumenter måste redundansväxla till replik klustret. Mer information finns i [använda MirrorMaker för att replikera Apache Kafka ämnen med Kafka på HDInsight](./kafka/apache-kafka-mirroring.md)

Beroende på ämnets livs längd när replikeringen startades kan MirrorMaker-ämnes replikering leda till olika förskjutningar mellan käll-och replik ämnen. HDInsight Kafka-kluster stöder även avsnitt partition replikering som är en funktion för hög tillgänglighet på den enskilda kluster nivån.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/kafka-replication.png" alt-text="Apache Kafka replikering":::

### <a name="apache-kafka-architectures"></a>Apache Kafka arkitekturer

#### <a name="kafka-replication-active--passive"></a>Kafka-replikering: aktiv – passiv

Active-Passive installationen möjliggör asynkron enkelriktad spegling från aktiv till passiv. Tillverkare och konsumenter måste vara medvetna om att det finns ett aktivt och passivt kluster och måste vara redo att redundansväxla till passivt om det aktiva inträffar. Nedan visas några fördelar och nack delar med Active-Passive-installationen.

Fördelar:

* Nätverks fördröjningen mellan kluster påverkar inte det aktiva klustrets prestanda.
* Enkel replikering av enkelriktad replikering.

Nackdelar:

* Det passiva klustret kan vara underutnyttjat.
* Utforma komplexitet i att införliva medvetenheten om redundans i program producenter och konsumenter.
* Möjlig data förlust under fel i det aktiva klustret.
* Eventuell konsekvens mellan ämnen mellan aktiva och passiva kluster.
* Förlita till primär kan leda till inkonsekvens i meddelanden i ämnen.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/kafka-active-passive.png" alt-text="Apache Kafka aktiv passiv modell":::

#### <a name="kafka-replication-active--active"></a>Kafka-replikering: aktiv – aktiv

Active-Active konfiguration omfattar två regionala, VNet-peer-Kafka kluster med dubbelriktad asynkron replikering med MirrorMaker. I den här designen blir meddelanden som konsumeras av konsumenter i den primära också tillgängliga för användare i sekundär och vice versa. Nedan visas några fördelar och nack delar med Active-Active-installationen.

Fördelar:

* Det är enklare att köra redundans och förlita på grund av deras dubbla tillstånd.

Nackdelar:

* Konfiguration, hantering och övervakning är mer komplext än aktiv-passiv.
* Problemet med cirkulär replikering måste åtgärdas.  
* Dubbelriktad replikering leder till högre kostnader för utgående data från regionala data.

:::image type="content" source="./media/hdinsight-business-continuity-architecture/kafka-active-active.png" alt-text="Apache Kafka aktiv aktiv modell":::

## <a name="hdinsight-enterprise-security-package"></a>HDInsight-Enterprise Security Package

Den här inställningen används för att aktivera funktioner för flera användare i både primär och sekundär, samt för [Azure AD DS-repliker](../active-directory-domain-services/tutorial-create-replica-set.md) för att säkerställa att användarna kan autentisera till båda klustren. Vid normal drift måste Ranger-principer konfigureras i den sekundära för att säkerställa att användarna är begränsade till Läs åtgärder. I nedanstående arkitektur förklaras hur en ESP aktive rad Hive-aktiv primär uppsättning kan se ut.

Ranger Metaarkiv-replikering:

Ranger Metaarkiv används för att lagra och betjäna Ranger-principer för att styra dataauktorisering. Vi rekommenderar att du upprätthåller oberoende Ranger-principer i primär och sekundär och upprätthåller den sekundära som en Läs replik.
  
Om kravet är att behålla Ranger-principer i synkronisering mellan primär och sekundär använder du [Ranger import/export](https://cwiki.apache.org/confluence/display/RANGER/User+Guide+For+Import-Export) för att regelbundet säkerhetskopiera och importera Ranger-principer från primär till sekundär.

Att replikera Ranger-principer mellan primär och sekundär kan orsaka att den sekundära är skrivnings aktive rad, vilket kan leda till oavsiktlig skrivning på den sekundära data inkonsekvenserna.  

:::image type="content" source="./media/hdinsight-business-continuity-architecture/hdinsight-enterprise-security-package.png" alt-text="HDInsight Enterprise Security Package-arkitektur":::

## <a name="next-steps"></a>Nästa steg

Mer information om de objekt som beskrivs i den här artikeln finns i:

* [Affärs kontinuitet i Azure HDInsight](./hdinsight-business-continuity.md)
* [Fallstudie av hög tillgänglighet för Azure HDInsight-lösning](./hdinsight-high-availability-case-study.md)
* [Vad är Apache Hive och HiveQL på Azure HDInsight?](./hadoop/hdinsight-use-hive.md)