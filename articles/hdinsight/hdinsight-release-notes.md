---
title: Viktig information om Azure HDInsight
description: Senaste viktig information för Azure HDInsight. Få utvecklings tips och information för Hadoop, Spark, R Server, Hive med mera.
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: 324d8b4c9fc53ca24e62fe339065d4452577cb1f
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2021
ms.locfileid: "105607226"
---
# <a name="azure-hdinsight-release-notes"></a>Viktig information om Azure HDInsight

Den här artikeln innehåller information om de **senaste** versionerna av Azure HDInsight-utgåvor. Information om tidigare versioner finns i avsnittet om [versions anmärkningar för HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Sammanfattning

Azure HDInsight är en av de populäraste tjänsterna mellan företags kunder för analys med öppen källkod på Azure.

Om du vill prenumerera på viktig information tittar du på versioner på [den här GitHub-lagringsplatsen](https://github.com/hdinsight/release-notes/releases).

## <a name="release-date-03242021"></a>Utgivnings datum: 03/24/2021

Den här versionen gäller både HDInsight 3,6 och HDInsight 4,0. HDInsight-versionen görs tillgänglig för alla regioner över flera dagar. Lanserings datumet här anger den första regionens utgivnings datum. Om du inte ser ändringarna nedan väntar du tills lanseringen är aktiv i din region under flera dagar.

## <a name="new-features"></a>Nya funktioner
### <a name="spark-30-preview"></a>Spark 3,0-förhandsgranskning
HDInsight har lagt till [Spark 3.0.0](https://spark.apache.org/docs/3.0.0/) -stöd för HDInsight 4,0 som en förhands gransknings funktion. 

### <a name="kafka-24-preview"></a>Kafka 2,4-förhandsgranskning
HDInsight har lagt till [Kafka 2.4.1](http://kafka.apache.org/24/documentation.html) -stöd till HDInsight 4,0 som en förhands gransknings funktion.

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Flytta till skalnings uppsättningar för virtuella Azure-datorer
HDInsight använder nu virtuella Azure-datorer för att etablera klustret. Tjänsten migreras gradvis till [skalnings uppsättningar för virtuella Azure-datorer](../virtual-machine-scale-sets/overview.md). Hela processen kan ta månader. När dina regioner och prenumerationer migreras, kommer nyligen skapade HDInsight-kluster att köras på virtuella datorers skalnings uppsättningar utan kund åtgärder. Ingen avbrytande ändring förväntas.

## <a name="deprecation"></a>Utfasning
Ingen utfasning i den här versionen.

## <a name="behavior-changes"></a>Beteende ändringar
### <a name="default-cluster-version-is-changed-to-40"></a>Standard kluster versionen ändras till 4,0
Standard versionen av HDInsight-klustret ändras från 3,6 till 4,0. Mer information om tillgängliga versioner finns i [tillgängliga versioner](./hdinsight-component-versioning.md). Läs mer om vad som är nytt i [HDInsight 4,0](./hdinsight-version-release.md).

### <a name="default-cluster-vm-sizes-are-changed-to-ev3-series"></a>Standard storleken på virtuella kluster datorer ändras till Ev3-serien 
Standard storleken för virtuella kluster datorer ändras från D-serien till Ev3-serien. Den här ändringen gäller för huvudnoder och arbetsnoder. För att undvika den här ändringen som påverkar dina testade arbets flöden, anger du de VM-storlekar som du vill använda i ARM-mallen.

### <a name="network-interface-resource-not-visible-for-clusters-running-on-azure-virtual-machine-scale-sets"></a>Nätverks gränssnitts resurs är inte synlig för kluster som körs på virtuella Azure-dators skalnings uppsättningar
HDInsight migreras gradvis till skalnings uppsättningar för virtuella Azure-datorer. Nätverks gränssnitt för virtuella datorer är inte längre synliga för kunder i kluster som använder skalnings uppsättningar för virtuella Azure-datorer.

## <a name="upcoming-changes"></a>Kommande ändringar
Följande ändringar sker i kommande versioner.

### <a name="os-version-upgrade"></a>Uppgradering av operativ system version
HDInsight kommer att uppgradera OS-versionen från Ubuntu 16,04 till 18,04. Uppgraderingen kommer att slutföras före 2021 april.

### <a name="hdinsight-36-end-of-support-on-june-30-2021"></a>HDInsight 3,6-slut för support den 30 2021 juni
HDInsight 3,6 är slut på support. Från och med juni 30 2021 kan kunder inte skapa nya HDInsight 3,6-kluster. Befintliga kluster kommer att köras i befintligt skick utan support från Microsoft. Överväg att flytta till HDInsight 4,0 för att undvika eventuellt system-och support avbrott.

## <a name="bug-fixes"></a>Felkorrigeringar
HDInsight fortsätter att göra kluster tillförlitlighet och prestanda förbättringar. 

## <a name="component-version-change"></a>Komponent versions ändring
Stöd har lagts till för Spark 3.0.0 och Kafka 2.4.1 som för hands version. Du hittar de aktuella komponent versionerna för HDInsight 4,0 och HDInsight 3,6 i [det här dokumentet](./hdinsight-component-versioning.md).
