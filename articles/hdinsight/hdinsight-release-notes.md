---
title: Viktig information om Azure HDInsight
description: Senaste viktig information för Azure HDInsight. Få utvecklings tips och information för Hadoop, Spark, R Server, Hive med mera.
ms.custom: references_regions
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: a648ff3aa0c042aaefe16eaae0f9d73953241b3d
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106065505"
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

### <a name="eav4-series-support"></a>Stöd för Eav4-serien
HDInsight har lagt till stöd för Eav4-serien i den här versionen. Läs mer om [Dav4-serien här](../virtual-machines/eav4-easv4-series.md). Serien har gjorts tillgänglig i följande regioner: 

* Australien, östra
* Brasilien, södra
* Central US
* Asien, östra
* East US
* Japan, östra
* Sydostasien
* Storbritannien, södra
* Europa, västra
* USA, västra 2

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

### <a name="basic-support-for-hdinsight-36-starting-july-1-2021"></a>Basic Support för HDInsight 3,6 från 1 juli 2021
Från och med den 1 juli 2021 kommer Microsoft att erbjuda [Basic support](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) för vissa typer av HDInsight 3,6-kluster. Basic Supports planen kommer att vara tillgänglig fram till 3 april 2022. Du registreras automatiskt i Basic Support som börjar den 1 juli 2021. Ingen åtgärd krävs för att du ska kunna välja. Se [vår dokumentation](hdinsight-36-component-versioning.md) för vilka kluster typer som ingår under Basic support. 

Vi rekommenderar att du inte skapar några nya lösningar i HDInsight 3,6 och fryser ändringar i befintliga 3,6-miljöer. Vi rekommenderar att du [migrerar dina kluster till HDInsight 4,0](hdinsight-version-release.md#how-to-upgrade-to-hdinsight-40). Lär dig mer om [vad som är nytt i HDInsight 4,0](hdinsight-version-release.md#whats-new-in-hdinsight-40).

## <a name="bug-fixes"></a>Felkorrigeringar
HDInsight fortsätter att göra kluster tillförlitlighet och prestanda förbättringar. 

## <a name="component-version-change"></a>Komponent versions ändring
Stöd har lagts till för Spark 3.0.0 och Kafka 2.4.1 som för hands version. Du hittar de aktuella komponent versionerna för HDInsight 4,0 och HDInsight 3,6 i [det här dokumentet](./hdinsight-component-versioning.md).

## <a name="recommanded-features"></a>Omkommandoade funktioner
### <a name="service-tags"></a>Tjänsttaggar
Service märken fören klar begränsningen av nätverks åtkomsten till Azure-tjänsterna för Azure Virtual Machines och Azure Virtual Networks. Service märken i NSG-regler (Network Security Group) tillåter eller nekar trafik till en enskild Azure-tjänst. Regeln kan ställas in globalt eller per Azure-region. Azure tillhandahåller underhåll av IP-adresser som är underliggande för varje tagg. HDInsight Service-taggar för nätverks säkerhets grupper (NSG: er) är grupper med IP-adresser för hälso-och hanterings tjänster. Dessa grupper bidrar till att minimera komplexiteten vid skapande av säkerhets regler. HDInsight-kunder kan aktivera Service tag genom Azure Portal, PowerShell och REST API. Mer information finns i [tjänst taggar för nätverks säkerhets grupper (NSG) för Azure HDInsight](./hdinsight-service-tags.md).
