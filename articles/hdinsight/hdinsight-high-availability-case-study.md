---
title: Fallstudie av hög tillgänglighet för Azure HDInsight-lösning
description: Den här artikeln är en fiktiv fallstudie av en möjlig lösnings arkitektur med hög tillgänglighet i Azure HDInsight.
keywords: Hadoop hög tillgänglighet
ms.service: hdinsight
ms.topic: conceptual
ms.date: 10/08/2020
ms.openlocfilehash: 6b995e2ab5ba663f6e33b009062859eb32928cc1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "99508599"
---
# <a name="azure-hdinsight-highly-available-solution-architecture-case-study"></a>Fallstudie av hög tillgänglighet för Azure HDInsight-lösning

Azure HDInsights mekanismer för replikering kan integreras i en lösnings arkitektur med hög tillgänglighet. I den här artikeln används en fiktiv fallstudie för Contoso-detaljister för att förklara möjliga katastrof återställnings metoder för hög tillgänglighet, kostnads överväganden och deras respektive design.

Rekommendationer för haveri beredskap med hög tillgänglighet kan ha många permutationer och kombinationer. Dessa lösningar är till för att komma in efter att ha överhoppat till de olika alternativen. Den här artikeln beskriver bara en möjlig lösning.

## <a name="customer-architecture"></a>Kund arkitektur

Följande bild illustrerar den primära contoso-arkitekturen. Arkitekturen består av en strömnings arbets belastning, batch-arbetsbelastning, betjäna lager, förbruknings lager, lagrings lager och versions kontroll.

:::image type="content" source="./media/hdinsight-high-availability-case-study/contoso-architecture.png" alt-text="Contoso återförsäljarversion-arkitektur":::

### <a name="streaming-workload"></a>Strömnings arbets belastning

Enheter och sensorer producerar data till HDInsight-Kafka, vilket utgör meddelande ramverket. An-HDInsight Spark-konsument läser från Kafka ämnen. Spark transformerar inkommande meddelanden och skriver det till ett HDInsight HBase-kluster i betjänar skiktet.

### <a name="batch-workload"></a>Batch-arbetsbelastning

An-HDInsight Hadoop-kluster som kör Hive och MapReduce matar in data från lokala transaktions system. Rå data som transformeras av Hive och MapReduce lagras i Hive-tabeller på en logisk partition av data Lake som stöds av Azure Data Lake Storage Gen2. Data som lagras i Hive-tabeller görs också tillgängliga för Spark-SQL, vilket innebär att batch-transformeringar innan de hanterade data lagras i HBase för att betjäna.

### <a name="serving-layer"></a>Betjänande lager

An-HDInsight HBase-kluster med Apache Phoenix används för att hantera data för webb program och visualiserings instrument paneler. An-HDInsight LLAP-kluster används för att uppfylla interna rapporterings krav.

### <a name="consumption-layer"></a>Förbruknings lager

Ett Azure-API Apps och API Management skikt tillbaka en offentlig webb sida. Interna rapporterings krav uppfylls av Power BI.

### <a name="storage-layer"></a>Lagrings lager

Logiskt partitionerade Azure Data Lake Storage Gen2 används som ett företags data Lake. HDInsight-metastores backas upp av Azure SQL DB.

### <a name="version-control-system"></a>Versions kontroll system

Ett versions kontroll system integrerat i en Azure-pipeline och finns utanför Azure.

## <a name="customer-business-continuity-requirements"></a>Krav för kund affärs kontinuitet

Det är viktigt att fastställa de minimala affärs funktioner du behöver om det är en katastrof.

### <a name="contoso-retails-business-continuity-requirements"></a>Contosos affärs kontinuitets krav

* Vi måste skyddas mot ett regionalt fel eller ett regionalt tjänst hälso problem.
* Mina kunder får aldrig se ett 404-fel. Offentligt innehåll måste alltid hanteras. (RTO = 0)  
* För de flesta delar av året kan vi visa offentligt innehåll som är inaktuellt med 5 timmar. (Återställnings punkt = 5 timmar)
* Under semester säsongen måste vårt offentligt innehåll alltid vara aktuellt. (ÅTERSTÄLLNINGS PUNKT = 0)
* Mina interna rapporterings krav anses inte vara kritiska för affärs kontinuitet.
* Optimera kostnader för verksamhets kontinuitet.

## <a name="proposed-solution"></a>Föreslagen lösning

Följande bild visar Contosos åter betalnings arkitektur för hög tillgänglighets återställning.

:::image type="content" source="./media/hdinsight-high-availability-case-study/contoso-solution.png" alt-text="Contoso-lösning":::

**Kafka** använder [aktiv – passiv](hdinsight-business-continuity-architecture.md#apache-kafka) replikering för att spegla Kafka-ämnen från den primära regionen till den sekundära regionen. Ett alternativ till Kafka-replikering kan vara att producera till Kafka i båda regionerna.

**Hive och Spark** använder [aktiva primära, primära replikeringspartner på begäran](hdinsight-business-continuity-architecture.md#apache-spark) under normala tider. Hive-replikeringen körs regelbundet och medföljer Hive Azure SQL-metaarkiv och Hive-lagrings kontots replikering. Spark Storage-kontot replikeras regelbundet med hjälp av ADF-DistCP. De här klusternas tillfälliga egenskaper bidrar till att optimera kostnaderna. Replikeringar är schemalagda var fjärde timme för att komma till en återställnings punkt i kravet på fem timmar.

**HBase** -replikering använder [ledare – följar](hdinsight-business-continuity-architecture.md#apache-hbase) modell under normal tid för att säkerställa att data alltid betjänas oavsett region och återställningen är mycket låg.

Om det finns ett regionalt haveri i den primära regionen, hanteras webb sidan och Server delen från den sekundära regionen i 5 timmar med en viss grad av föråldrad. Om instrument panelen för Azure-tjänstens hälso tillstånd inte anger en återställnings tid i fönstret fem timmar skapas Hive-och Spark-omvandlings lagret i den sekundära regionen, och sedan pekar alla överordnade data källor i den sekundära regionen. Att göra den sekundära regionen skrivbar kan orsaka en återställning efter fel som innebär replikering tillbaka till den primära.

Under en säsongs kunds säsong är hela den sekundära pipelinen alltid aktiv och körs. Kafka-producenter producerar till båda regionerna och HBase-replikeringen skulle ändras från Leader-Follower till Leader-Leader för att säkerställa att offentligt innehåll alltid är uppdaterat.

Ingen redundans måste vara utformad för intern rapportering eftersom den inte är avgörande för verksamhets kontinuitet.

## <a name="next-steps"></a>Nästa steg

Mer information om de objekt som beskrivs i den här artikeln finns i:

* [Affärs kontinuitet i Azure HDInsight](./hdinsight-business-continuity.md)
* [Azure HDInsight affärs kontinuitets arkitekturer](./hdinsight-business-continuity-architecture.md)
* [Vad är Apache Hive och HiveQL på Azure HDInsight?](./hadoop/hdinsight-use-hive.md)
