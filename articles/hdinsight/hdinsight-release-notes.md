---
title: Viktig information om Azure HDInsight
description: Senaste versionen för Azure HDInsight. Få utvecklingstips och information om Hadoop, Spark, R Server, Hive med mera.
ms.custom: references_regions
ms.service: hdinsight
ms.topic: conceptual
ms.date: 03/23/2021
ms.openlocfilehash: 838eb517697c0625139058a19c7def764e869ed5
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588181"
---
# <a name="azure-hdinsight-release-notes"></a>Azure HDInsight den här versionen

Den här artikeln innehåller information om **de Azure HDInsight** senaste uppdateringarna. Information om tidigare versioner finns i [HDInsight Release Notes Archive](hdinsight-release-notes-archive.md).

## <a name="summary"></a>Sammanfattning

Azure HDInsight är en av de mest populära tjänsterna bland företagskunder för analys med öppen källkod i Azure.

Om du vill prenumerera på versionerna kan du titta på versioner på den här [GitHub-lagringsplatsen.](https://github.com/hdinsight/release-notes/releases)

## <a name="release-date-03242021"></a>Utgivningsdatum: 2021-03-24

Den här versionen gäller för både HDInsight 3.6 och HDInsight 4.0. HDInsight-versionen görs tillgänglig för alla regioner under flera dagar. Utgivningsdatumet här anger det första datumet för lanseringen i regionen. Om du inte ser ändringarna nedan väntar du på att versionen ska vara live i din region om flera dagar.

## <a name="new-features"></a>Nya funktioner
### <a name="spark-30-preview"></a>Förhandsversion av Spark 3.0
HDInsight lade [till Stöd för Spark 3.0.0](https://spark.apache.org/docs/3.0.0/) i HDInsight 4.0 som en förhandsgranskningsfunktion. 

### <a name="kafka-24-preview"></a>Kafka 2.4 preview
HDInsight lade [till Kafka 2.4.1-stöd](http://kafka.apache.org/24/documentation.html) för HDInsight 4.0 som en förhandsgranskningsfunktion.

### <a name="eav4-series-support"></a>Stöd för Eav4-serien
HDInsight har lagt till stöd för Eav4-serien i den här versionen. Läs mer om [Dav4-serien här.](../virtual-machines/eav4-easv4-series.md) Serien har gjorts tillgänglig i följande regioner: 

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

### <a name="moving-to-azure-virtual-machine-scale-sets"></a>Flytta till skalningsuppsättningar för virtuella Azure-datorer
HDInsight använder nu virtuella Azure-datorer för att etablera klustret. Tjänsten migrerar gradvis till Skalningsuppsättningar för [virtuella Azure-datorer.](../virtual-machine-scale-sets/overview.md) Hela processen kan ta flera månader. När dina regioner och prenumerationer har migrerats körs nyligen skapade HDInsight-kluster på VM-skalningsuppsättningar utan kundåtgärder. Ingen stor ändring förväntas.

## <a name="deprecation"></a>Utfasning
Ingen utfasning i den här versionen.

## <a name="behavior-changes"></a>Beteendeändringar
### <a name="default-cluster-version-is-changed-to-40"></a>Standardklusterversionen ändras till 4.0
Standardversionen av HDInsight-klustret ändras från 3.6 till 4.0. Mer information om tillgängliga versioner finns i [tillgängliga versioner.](./hdinsight-component-versioning.md) Läs mer om vad som är nytt i [HDInsight 4.0](./hdinsight-version-release.md).

### <a name="default-cluster-vm-sizes-are-changed-to-ev3-series"></a>Standardstorlekarna för virtuella datorer i kluster ändras till Ev3-serien 
Standardstorlekarna för virtuella datorer i kluster ändras från D-serien till Ev3-serien. Den här ändringen gäller huvudnoder och arbetsnoder. För att undvika att den här ändringen påverkar dina testade arbetsflöden anger du de VM-storlekar som du vill använda i ARM-mallen.

### <a name="network-interface-resource-not-visible-for-clusters-running-on-azure-virtual-machine-scale-sets"></a>Nätverksgränssnittsresursen visas inte för kluster som körs på skalningsuppsättningar för virtuella Azure-datorer
HDInsight migreras gradvis till skalningsuppsättningar för virtuella Azure-datorer. Nätverksgränssnitt för virtuella datorer är inte längre synliga för kunder för kluster som använder skalningsuppsättningar för virtuella Azure-datorer.

## <a name="upcoming-changes"></a>Kommande ändringar
Följande ändringar kommer att ske i kommande versioner.

### <a name="os-version-upgrade"></a>Uppgradering av operativsystemversion
HDInsight-kluster körs för närvarande på Ubuntu 16.04 LTS. Som vi [refererar till](https://ubuntu.com/about/release-cycle)i Ubuntus lanseringscykel når Ubuntu 16.04-kerneln EOL (End of Life) i april 2021. Vi börjar lansera den nya HDInsight 4.0-klusteravbildningen som körs på Ubuntu 18.04 i maj 2021. Nyligen skapade HDInsight 4.0-kluster körs på Ubuntu 18.04 som standard när de är tillgängliga. Befintliga kluster på Ubuntu 16.04 kommer att köras som de är med fullständigt stöd.

HDInsight 3.6 fortsätter att köras på Ubuntu 16.04. Standardsupporten kommer att upphöra den 30 juni 2021 och kommer att ändras till Basic Support från och med 1 juli 2021. Mer information om datum och supportalternativ finns i [Azure HDInsight versioner.](https://docs.microsoft.com/azure/hdinsight/hdinsight-component-versioning#supported-hdinsight-versions) Ubuntu 18.04 stöds inte för HDInsight 3.6. Om du vill använda Ubuntu 18.04 måste du migrera dina kluster till HDInsight 4.0. 

Du måste släppa och återskapa dina kluster om du vill flytta befintliga kluster till Ubuntu 18.04. Planera att skapa eller återskapa klustret när stöd för Ubuntu 18.04 blir tillgängligt. Vi skickar ett nytt meddelande när den nya avbildningen blir tillgänglig i alla regioner.

Vi rekommenderar starkt att du testar dina skriptåtgärder och anpassade program som distribueras på gränsnoder på en virtuell Ubuntu 18.04-dator (VM) i förväg. Du kan skapa en enkel [Ubuntu Linux VM på 18.04-LTS](https://azure.microsoft.com/resources/templates/101-vm-simple-linux/)och sedan skapa och använda [ett SSH-nyckelpar (Secure Shell)](https://docs.microsoft.com/azure/virtual-machines/linux/mac-create-ssh-keys#ssh-into-your-vm) på den virtuella datorn för att köra och testa dina skriptåtgärder och anpassade program som distribueras på gränsnoder.

### <a name="disable-stardard_a5-vm-size-as-head-node-for-hdinsgiht-40"></a>Inaktivera Stardard_A5 VM-storlek som huvudnod för HDInsgiht 4.0
HDInsight-klustrets huvudnod ansvarar för att initiera och hantera klustret. Standard_A5 VM-storleken har tillförlitlighetsproblem som huvudnod för HDInsight 4.0. Från och med nästa version i maj 2021 kommer kunderna inte att kunna skapa nya kluster med Standard_A5 VM-storlek som huvudnod. Du kan använda andra 2-kärniga virtuella datorer som E2_v3 eller E2s_v3. Befintliga kluster körs som de är. En virtuell dator med 4 kärnor rekommenderas starkt för huvudnoden för att säkerställa hög tillgänglighet och tillförlitlighet för dina HDInsight-produktionskluster.

### <a name="basic-support-for-hdinsight-36-starting-july-1-2021"></a>Basic Support HDInsight 3.6 från och med 1 juli 2021
Från och med 1 juli 2021 erbjuder Microsoft [Basic Support](hdinsight-component-versioning.md#support-options-for-hdinsight-versions) vissa TYPER av HDInsight 3.6-kluster. Den Basic Support planen är tillgänglig fram till den 3 april 2022. Du registreras automatiskt i den Basic Support 1 juli 2021. Ingen åtgärd krävs av dig för att anmäla dig. Se [vår dokumentation](hdinsight-36-component-versioning.md) för vilka klustertyper som ingår i Basic Support. 

Vi rekommenderar inte att du skapar några nya lösningar på HDInsight 3.6 och låser ändringar i befintliga 3.6-miljöer. Vi rekommenderar att du [migrerar dina kluster till HDInsight 4.0](hdinsight-version-release.md#how-to-upgrade-to-hdinsight-40). Läs mer om [vad som är nytt i HDInsight 4.0](hdinsight-version-release.md#whats-new-in-hdinsight-40).

## <a name="bug-fixes"></a>Felkorrigeringar
HDInsight fortsätter att förbättra klustrets tillförlitlighet och prestanda. 

## <a name="component-version-change"></a>Ändring av komponentversion
Stöd har lagts till för Spark 3.0.0 och Kafka 2.4.1 som förhandsversion. Du hittar de aktuella komponentversionerna för HDInsight 4.0 och HDInsight 3.6 i [det här dokumentet.](./hdinsight-component-versioning.md)

## <a name="recommanded-features"></a>Rekommendationer för funktioner
### <a name="service-tags"></a>Tjänsttaggar
Tjänsttaggar förenklar begränsningen av nätverksåtkomsten till Azure-tjänsterna för virtuella Azure-datorer och virtuella Azure-nätverk. Tjänsttaggar i reglerna för nätverkssäkerhetsgruppen (NSG) tillåter eller nekar trafik till en specifik Azure-tjänst. Regeln kan anges globalt eller per Azure-region. Azure tillhandahåller underhåll av IP-adresser som ligger under varje tagg. HDInsight-tjänsttaggar för nätverkssäkerhetsgrupper (NSG:er) är grupper av IP-adresser för hälso- och hanteringstjänster. Dessa grupper hjälper till att minimera komplexiteten vid skapande av säkerhetsregel. HDInsight-kunder kan aktivera tjänsttaggar via Azure Portal, PowerShell och REST API. Mer information finns i [Tjänsttaggar för nätverkssäkerhetsgrupp (NSG) för Azure HDInsight](./hdinsight-service-tags.md).
