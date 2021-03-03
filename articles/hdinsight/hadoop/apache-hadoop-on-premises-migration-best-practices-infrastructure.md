---
title: 'Infrastruktur: lokal Apache Hadoop till Azure HDInsight'
description: Lär dig metod tips för infrastruktur för migrering av lokala Hadoop-kluster till Azure HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 309053c2d7a0f9482016f1bd83e0c61dcd31bec5
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101740672"
---
# <a name="migrate-on-premises-apache-hadoop-clusters-to-azure-hdinsight---infrastructure-best-practices"></a>Migrera lokala Apache Hadoop-kluster till Azure HDInsight – metod tips för infrastruktur

Den här artikeln innehåller rekommendationer för att hantera infrastrukturen i Azure HDInsight-kluster. Den ingår i en serie som ger bästa praxis för att hjälpa till att migrera lokala Apache Hadoop system till Azure HDInsight.

## <a name="plan-for-hdinsight-cluster-capacity"></a>Planera för kapacitet för HDInsight-kluster

De viktigaste alternativen för att planera prestanda för HDInsight-kluster är följande:

**Region**  
Azure-regionen fastställer var klustret är fysiskt allokerat. För att minimera svars tiden för läsningar och skrivningar bör klustret vara i samma region som data.

**Lagrings plats och storlek**  
Standard lagringen måste finnas i samma region som klustret. För ett kluster med 48 noder rekommenderar vi att du har 4 till 8 lagrings konton. Även om det kanske redan finns tillräckligt med lagrings utrymme ger varje lagrings konto ytterligare nätverks bandbredd för Compute-noderna. När det finns flera lagrings konton använder du ett slumpmässigt namn för varje lagrings konto utan ett prefix. Syftet med slumpmässig namngivning minskar risken för Flask halsar i lagring (begränsning) eller vanliga läges problem i alla konton. Använd endast en behållare per lagrings konto för bättre prestanda.

**VM-storlek och-typ (stöder nu G-serien)**  
Varje kluster typ har en uppsättning nodtyper och varje nodtyp har olika alternativ för deras VM-storlek och-typ. VM-storlek och-typ bestäms av processor processor kraft, RAM-storlek och nätverks fördröjning. En simulerad arbets belastning kan användas för att fastställa den optimala storleken och typen för den virtuella datorn för varje Node-typ.

**Antal arbetsnoder**  
Det första antalet arbetsnoder kan bestämmas med hjälp av de simulerade arbets belastningarna. Klustret kan skalas senare genom att lägga till fler arbetsnoder för att uppfylla högsta belastnings krav. Klustret kan senare skalas tillbaka när ytterligare arbetsnoder inte krävs.

Mer information finns i artikeln [kapacitets planering för HDInsight-kluster](../hdinsight-capacity-planning.md).

## <a name="use-recommended-virtual-machine-type-for-cluster"></a>Använd Rekommenderad typ av virtuell dator för kluster

Se [standard konfiguration av noder och virtuella dator storlekar för kluster](../hdinsight-supported-node-configuration.md) för rekommenderade typer av virtuella datorer för varje typ av HDInsight-kluster.

## <a name="check-hadoop-components-availability-in-hdinsight"></a>Kontrol lera om Hadoop-komponenter är tillgängliga i HDInsight

Varje HDInsight-version är en moln distribution av en uppsättning Hadoop miljö system komponenter. Mer information om alla HDInsight-komponenter och deras aktuella versioner finns i [versions hantering av HDInsight](../hdinsight-component-versioning.md) -komponenter.

Du kan också använda Apache Ambari-gränssnittet eller Ambari-REST API för att kontrol lera Hadoop-komponenterna och-versionerna i HDInsight.

Program eller komponenter som var tillgängliga i lokala kluster, men som inte ingår i HDInsight-kluster, kan läggas till på en Edge-nod eller på en virtuell dator i samma VNet som HDInsight-klustret. Ett Hadoop-program från tredje part som inte är tillgängligt på Azure HDInsight kan installeras med alternativet "program" i HDInsight-klustret. Anpassade Hadoop-program kan installeras på HDInsight-kluster med hjälp av skript åtgärder. I följande tabell visas några av de vanligaste programmen och deras HDInsight-integrerings alternativ:

|**Program**|**Integrering**
|---|---|
|Luft flöde|IaaS-eller HDInsight Edge-nod
|Alluxio|IaaS  
|Arcadia|IaaS 
|Tamazight|Ingen (endast HDP)
|Datameer|HDInsight Edge-nod
|DataStax (Cassandra)|IaaS (CosmosDB ett alternativ i Azure)
|DataTorrent|IaaS 
|Drill|IaaS 
|Ignite|IaaS
|Jethro|IaaS 
|Mapador|IaaS 
|Mongo|IaaS (CosmosDB ett alternativ i Azure)
|NiFi|IaaS 
|Presto|IaaS-eller HDInsight Edge-nod
|Python 2|PaaS 
|Python 3|PaaS 
|R|PaaS 
|SAS|IaaS 
|Vertica|IaaS (SQLDW ett alternativ i Azure)
|Tableau|IaaS 
|Linje|HDInsight Edge-nod
|StreamSets|HDInsight Edge 
|Palantir|IaaS 
|Sailpoint|IaaS 

Mer information finns i artikeln Apache Hadoop- [komponenter som är tillgängliga med olika HDInsight-versioner](../hdinsight-component-versioning.md)

## <a name="customize-hdinsight-clusters-using-script-actions"></a>Anpassa HDInsight-kluster med skript åtgärder

HDInsight tillhandahåller en metod för kluster konfiguration som kallas en **skript åtgärd**. En skript åtgärd är Bash-skript som körs på noderna i ett HDInsight-kluster och som kan användas för att installera ytterligare komponenter och ändra konfigurations inställningar.

Skript åtgärder måste lagras på en URI som är tillgänglig från HDInsight-klustret. De kan användas under eller efter att klustret har skapats och kan också begränsas till att endast köras på vissa nodtyper.

Skriptet kan vara beständigt eller köras en stund. De bestående skripten används för att anpassa nya arbetsnoder som läggs till i klustret genom skalnings åtgärder. Ett beständigt skript kan också tillämpa ändringar av en annan nodtyp, till exempel en head-nod, när skalnings åtgärder sker.

HDInsight innehåller i förväg skrivna skript för att installera följande komponenter i HDInsight-kluster:

- Lägg till ett Azure Storage konto
- Installera nyans
- Installera Presto
- Installera solr
- Installera Giraph
- För inläsning av Hive-bibliotek
- Installera eller uppdatera Mono

> [!Note]  
> HDInsight tillhandahåller inte direkt stöd för anpassade Hadoop-komponenter eller komponenter som installeras med skript åtgärder.

Skript åtgärder kan också publiceras på Azure Marketplace som ett HDInsight-program.

Mer information finns i följande artiklar:

- [Installera Apache Hadoop program från tredje part i HDInsight](../hdinsight-apps-install-applications.md)
- [Anpassa HDInsight-kluster med skript åtgärder](../hdinsight-hadoop-customize-cluster-linux.md)
- [Publicera ett HDInsight-program på Azure Marketplace](../hdinsight-apps-publish-applications.md)

## <a name="customize-hdinsight-configs-using-bootstrap"></a>Anpassa HDInsight-konfiguration med start

Ändringar i konfigurationerna i config-filerna som `core-site.xml` `hive-site.xml` och `oozie-env.xml` kan göras med hjälp av bootstrap. Följande skript är ett exempel som använder PowerShell [AZ-modulen](/powershell/azure/new-azureps-module-az) cmdlet [New-AzHDInsightClusterConfig](/powershell/module/az.hdinsight/new-azhdinsightcluster):

```powershell
# hive-site.xml configuration
$hiveConfigValues = @{"hive.metastore.client.socket.timeout"="90"}

$config = New—AzHDInsightClusterConfig '
    | Set—AzHDInsightDefaultStorage
    —StorageAccountName "$defaultStorageAccountName.blob. core.windows.net" `
    —StorageAccountKey "defaultStorageAccountKey " `
    | Add—AzHDInsightConfigValues `
        —HiveSite $hiveConfigValues

New—AzHDInsightCluster `
    —ResourceGroupName $existingResourceGroupName `
    —Cluster-Name $clusterName `
    —Location $location `
    —ClusterSizeInNodes $clusterSizeInNodes `
    —ClusterType Hadoop `
    —OSType Linux `
    —Version "3.6" `
    —HttpCredential $httpCredential `
    —Config $config
```

Mer information finns i artikeln [Anpassa HDInsight-kluster med hjälp av bootstrap](../hdinsight-hadoop-customize-cluster-bootstrap.md).  Se även [Hantera HDInsight-kluster med hjälp av Apache Ambari REST API](../hdinsight-hadoop-manage-ambari-rest-api.md).

## <a name="access-client-tools-from-hdinsight-hadoop-cluster-edge-nodes"></a>Få åtkomst till klient verktyg från HDInsight Hadoop Cluster Edge-noder

En tom Edge-nod är en virtuell Linux-dator med samma klient verktyg installerade och konfigurerade som på huvudnoderna, men utan att några Hadoop-tjänster körs. Du kan använda Edge-noden i följande syfte:

- åtkomst till klustret
- testa klient program
- värdbaserade klient program

Edge-noder kan skapas och tas bort via Azure Portal och kan användas under eller efter att klustret har skapats. När Edge-noden har skapats kan du ansluta till Edge-noden med SSH och köra klient verktyg för att komma åt Hadoop-klustret i HDInsight. SSH-slutpunkten för Edge-noden är `<EdgeNodeName>.<ClusterName>-ssh.azurehdinsight.net:22` .


Mer information finns i artikeln [använda tomma Edge-noder i Apache Hadoop kluster i HDInsight](../hdinsight-apps-use-edge-node.md).


## <a name="use-scale-up-and-scale-down-feature-of-clusters"></a>Använda funktionen skala upp och skala ned i kluster

HDInsight tillhandahåller elastiskhet genom att ge dig möjlighet att skala upp och ned antalet arbetsnoder i klustren. Med den här funktionen kan du krympa ett kluster efter timmar eller helger och expandera det under de högsta affärs behoven. Mer information finns i:

* [Skala HDInsight-kluster](../hdinsight-scaling-best-practices.md).
* [Skala kluster](../hdinsight-administer-use-portal-linux.md#scale-clusters).

## <a name="use-hdinsight-with-azure-virtual-network"></a>Använda HDInsight med Azure Virtual Network

Azure virtuella nätverk gör det möjligt för Azure-resurser, till exempel Azure Virtual Machines, att kommunicera på ett säkert sätt med varandra, Internet och lokala nätverk, genom att filtrera och dirigera nätverks trafik.

Med hjälp av Azure Virtual Network med HDInsight kan du använda följande scenarier:

- Ansluta till HDInsight direkt från ett lokalt nätverk.
- Ansluta HDInsight till data lager i ett virtuellt Azure-nätverk.
- Direkt åtkomst till Hadoop-tjänster som inte är tillgängliga offentligt via Internet. Till exempel Kafka-API: er eller HBase Java API.

HDInsight kan antingen läggas till i en ny eller befintlig Azure-Virtual Network. Om HDInsight läggs till i en befintlig Virtual Network, måste befintliga nätverks säkerhets grupper och användardefinierade vägar uppdateras för att tillåta obegränsad åtkomst till [flera IP-adresser](../hdinsight-management-ip-addresses.md) i Azure Data Center. Se också till att inte blockera trafik till [portarna](../control-network-traffic.md#required-ports)som används av HDInsight-tjänster.

> [!Note]  
> HDInsight har för närvarande inte stöd för Tvingad tunnel trafik. Tvingad tunnel trafik är en under näts inställning som framtvingar utgående Internet trafik till en enhet för inspektion och loggning. Ta antingen bort Tvingad tunnel trafik innan du installerar HDInsight i ett undernät eller skapa ett nytt undernät för HDInsight. HDInsight stöder inte heller begränsning av utgående nätverks anslutning.

Mer information finns i följande artiklar:

- [Azure Virtual-Networks – översikt](../../virtual-network/virtual-networks-overview.md)
- [Utöka HDInsight med ett Azure Virtual Network](../hdinsight-plan-virtual-network-deployment.md)

## <a name="securely-connect-to-azure-services-with-azure-virtual-network-service-endpoints"></a>Anslut säkert till Azure-tjänster med Azure Virtual Network Service-slutpunkter

HDInsight stöder [tjänst slut punkter för virtuella nätverk](../../virtual-network/virtual-network-service-endpoints-overview.md), vilket gör att du kan ansluta till Azure Blob Storage, Azure Data Lake Storage Gen2, Cosmos DB och SQL-databaser på ett säkert sätt. Genom att aktivera en tjänst slut punkt för Azure HDInsight flödar trafiken genom en skyddad väg från Azure Data Center. Med den här utökade säkerhets nivån på nätverks lagret kan du låsa stora data lagrings konton till sina angivna virtuella nätverk (virtuella nätverk) och fortfarande använda HDInsight-kluster sömlöst för att komma åt och bearbeta dessa data.

Mer information finns i följande artiklar:

- [Tjänstslutpunkter för virtuellt nätverk](../../virtual-network/virtual-network-service-endpoints-overview.md)
- [Förbättra HDInsight-säkerheten med tjänst slut punkter](https://azure.microsoft.com/blog/enhance-hdinsight-security-with-service-endpoints/)

## <a name="connect-hdinsight-to-the-on-premises-network"></a>Ansluta HDInsight till det lokala nätverket

HDInsight kan anslutas till det lokala nätverket med hjälp av virtuella Azure-nätverk och en VPN-gateway. Följande steg kan användas för att upprätta anslutningar:

- Använd HDInsight i en Azure-Virtual Network som har anslutning till det lokala nätverket.
- Konfigurera DNS-namnmatchning mellan det virtuella nätverket och det lokala nätverket.
- Konfigurera nätverks säkerhets grupper eller användardefinierade vägar (UDR) för att kontrol lera nätverks trafiken.

Mer information finns i artikeln [ansluta HDInsight till ditt lokala nätverk](../connect-on-premises-network.md)

## <a name="next-steps"></a>Nästa steg

Läs nästa artikel i den här serien: [metod tips för lagring för lokal att Azure HDInsight Hadoop migrering](apache-hadoop-on-premises-migration-best-practices-storage.md).