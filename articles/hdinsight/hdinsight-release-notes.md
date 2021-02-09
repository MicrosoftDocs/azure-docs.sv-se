---
title: Viktig information om Azure HDInsight
description: Senaste viktig information för Azure HDInsight. Få utvecklings tips och information för Hadoop, Spark, R Server, Hive med mera.
ms.custom: hdinsightactive
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/08/2021
ms.openlocfilehash: f6b7000812f1adfe6ff7bd93711c9b8fe4ff9adc
ms.sourcegitcommit: 7e117cfec95a7e61f4720db3c36c4fa35021846b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2021
ms.locfileid: "99988364"
---
# <a name="azure-hdinsight-release-notes"></a>Viktig information om Azure HDInsight

Den här artikeln innehåller information om de **senaste** versionerna av Azure HDInsight-utgåvor. Information om tidigare versioner finns i avsnittet om [versions anmärkningar för HDInsight](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Sammanfattning

Azure HDInsight är en av de populäraste tjänsterna mellan företags kunder för analys med öppen källkod på Azure.

Om du vill prenumerera på viktig information tittar du på versioner på [den här GitHub-lagringsplatsen](https://github.com/hdinsight/release-notes/releases).

## <a name="release-date-02052021"></a>Utgivnings datum: 02/05/2021

Den här versionen gäller både HDInsight 3,6 och HDInsight 4,0. HDInsight-versionen görs tillgänglig för alla regioner över flera dagar. Lanserings datumet här anger den första regionens utgivnings datum. Om du inte ser ändringarna nedan väntar du tills lanseringen är aktiv i din region under flera dagar.

## <a name="new-features"></a>Nya funktioner
### <a name="dav4-series-support"></a>Stöd för Dav4-serien
HDInsight har lagt till stöd för Dav4-serien i den här versionen. Läs mer om [Dav4-serien här](https://docs.microsoft.com/azure/virtual-machines/dav4-dasv4-series).

### <a name="kafka-rest-proxy-ga"></a>Kafka REST-proxy GA 
Med Kafka REST proxy kan du interagera med ditt Kafka-kluster via en REST API över HTTPS. Kafka rest proxy är allmänt tillgänglig från den här versionen. Läs mer om [KAFKA rest proxy här](https://docs.microsoft.com/azure/hdinsight/kafka/rest-proxy).

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Flytta till skalnings uppsättningar för virtuella Azure-datorer
HDInsight använder nu virtuella Azure-datorer för att etablera klustret. Tjänsten migreras gradvis till [skalnings uppsättningar för virtuella Azure-datorer](../virtual-machine-scale-sets/overview.md). Hela processen kan ta månader. När dina regioner och prenumerationer migreras, kommer nyligen skapade HDInsight-kluster att köras på virtuella datorers skalnings uppsättningar utan kund åtgärder. Ingen avbrytande ändring förväntas.

## <a name="deprecation"></a>Utfasning
### <a name="disabled-vm-sizes"></a>Inaktiverade VM-storlekar
Från och med den 9 2021 januari kommer HDInsight att blockera alla kunder som skapar kluster med hjälp av standand_A8, standand_A9 standand_A10 och standand_A11 VM-storlekar. Befintliga kluster kommer att köras som de är. Överväg att flytta till HDInsight 4,0 för att undvika eventuellt system-och support avbrott.

## <a name="behavior-changes"></a>Beteende ändringar
### <a name="default-cluster-vm-size-changes-to-ev3-series"></a>Standard storleken för virtuella kluster datorer ändras till Ev3-serien 
Standard storlekarna för virtuella kluster datorer ändras från D-serien till Ev3-serien. Den här ändringen gäller för huvudnoder och arbetsnoder. För att undvika den här ändringen som påverkar dina testade arbets flöden, anger du de VM-storlekar som du vill använda i ARM-mallen.

### <a name="network-interface-resource-not-visible-for-clusters-running-on-azure-virtual-machine-scale-sets"></a>Nätverks gränssnitts resurs är inte synlig för kluster som körs på virtuella Azure-dators skalnings uppsättningar
HDInsight migreras gradvis till skalnings uppsättningar för virtuella Azure-datorer. Nätverks gränssnitt för virtuella datorer är inte längre synliga för kunder i kluster som använder skalnings uppsättningar för virtuella Azure-datorer.


### <a name="breaking-change-for-net-for-apache-spark-100"></a>Överändrad ändring för .NET för Apache Spark 1.0.0
HDInsight introducerar den första betydande officiella versionen av .NET för Apache Spark i nästa version. Den ger DataFrame API-slutförande för Spark 2.4. x och Spark 3.0. x tillsammans med andra funktioner. Det kommer att gå att bryta ändringar för den här huvud versionen, se [den här migreringsguiden](https://github.com/dotnet/spark/blob/master/docs/migration-guide.md#upgrading-from-microsoftspark-0x-to-10) för att förstå de steg som krävs för att uppdatera din kod och dina pipeliner. Läs mer [här](https://docs.microsoft.com/azure/hdinsight/spark/spark-dotnet-version-update#using-net-for-apache-spark-v10-in-hdinsight).

## <a name="upcoming-changes"></a>Kommande ändringar
Följande ändringar sker i kommande versioner.

### <a name="default-cluster-version-will-be-changed-to-40"></a>Standard kluster versionen kommer att ändras till 4,0
Från och med 2021 februari kommer standard versionen av HDInsight-klustret att ändras från 3,6 till 4,0. Mer information om tillgängliga versioner finns i [tillgängliga versioner](./hdinsight-component-versioning.md#available-versions). Läs mer om vad som är nytt i [HDInsight 4,0](./hdinsight-version-release.md).

### <a name="os-version-upgrade"></a>Uppgradering av operativ system version
HDInsight uppgraderar OS-versionen från Ubuntu 16,04 till 18,04. Uppgraderingen kommer att slutföras före 2021 april.

### <a name="hdinsight-36-end-of-support-on-june-30-2021"></a>HDInsight 3,6-slut för support den 30 2021 juni
HDInsight 3,6 är slut på support. Från och med juni 30 2021 kan kunder inte skapa nya HDInsight 3,6-kluster. Befintliga kluster kommer att köras i befintligt skick utan support från Microsoft. Överväg att flytta till HDInsight 4,0 för att undvika eventuellt system-och support avbrott.

## <a name="bug-fixes"></a>Felkorrigeringar
HDInsight fortsätter att göra kluster tillförlitlighet och prestanda förbättringar. 

## <a name="component-version-change"></a>Komponent versions ändring
Ingen komponent versions ändring för den här versionen. Du hittar de aktuella komponent versionerna för HDInsight 4,0 och HDInsight 3,6 i [det här dokumentet](./hdinsight-component-versioning.md).

