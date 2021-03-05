---
title: Metod tips för kluster hantering – Azure HDInsight
description: Lär dig metod tips för att hantera HDInsight-kluster.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: d326dfe3a44e5d9e9b55a888762f3878cd418850
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102210962"
---
# <a name="hdinsight-cluster-management-best-practices"></a>Metod tips för HDInsight-kluster hantering

Lär dig metod tips för att hantera HDInsight-kluster.

## <a name="how-do-i-create-hdinsight-clusters"></a>Hur gör jag för att skapar du HDInsight-kluster?

| Alternativ | Dokument |
|---|---|
| Azure Data Factory | [Skapa Apache Hadoop-kluster på begäran i HDInsight med hjälp av Azure Data Factory](./hdinsight-hadoop-create-linux-clusters-adf.md) |
| Anpassad Resource Manager-mall | [Skapa Apache Hadoop kluster i HDInsight med hjälp av Resource Manager-mallar](./hdinsight-hadoop-create-linux-clusters-arm-templates.md) |
| Snabbstartsmallar | [HDInsight snabb starts mallar](https://azure.microsoft.com/resources/templates/?term=hdinsight) |
| Azure-exempel | [HDInsight Azure-exempel](/samples/browse/?products=azure-hdinsight) |
| Azure Portal | [Skapa Linux-baserade kluster i HDInsight med hjälp av Azure Portal](./spark/apache-spark-intellij-tool-plugin.md) |
| Azure CLI | [Skapa HDInsight-kluster med Azure CLI](./hdinsight-hadoop-create-linux-clusters-azure-cli.md) |
| Azure PowerShell | [Skapa Linux-baserade kluster i HDInsight med hjälp av Azure PowerShell](./hdinsight-hadoop-create-linux-clusters-azure-powershell.md) |
| cURL | [Skapa Apache Hadoop kluster med Azure-REST API](./hdinsight-hadoop-create-linux-clusters-curl-rest.md) |
| SDK: er (.NET, python, Java) | [.Net](/dotnet/api/overview/azure/hdinsight?view=azure-dotnet&preserve-view=true), [python](/python/api/overview/azure/hdinsight?preserve-view=true&view=azure-python), [Java](/java/api/overview/azure/hdinsight), [Go](./hdinsight-go-sdk-overview.md) |

> [!Note]
> Om du skapar ett kluster och återanvänder kluster namnet från ett tidigare skapat kluster väntar du tills föregående kluster borttagning har slutförts innan du skapar klustret.

## <a name="how-do-i-customize-hdinsight-clusters"></a>Hur gör jag för att du anpassa HDInsight-kluster?

| Alternativ | Dokument |
|---|---|
| Skript åtgärder | [Anpassa Azure HDInsight-kluster med hjälp av skript åtgärder](./hdinsight-hadoop-customize-cluster-linux.md) |
| Bootstrap | [Anpassa HDInsight-kluster med hjälp av bootstrap](./hdinsight-hadoop-customize-cluster-bootstrap.md) |
| Extern metastores | [Använda extern metadatalagring i Azure HDInsight](./hdinsight-use-external-metadata-stores.md) |
| Anpassad Ambari-databas | [Konfigurera HDInsight-kluster med en anpassad Ambari-databas](./hdinsight-custom-ambari-db.md) |

## <a name="what-are-some-errors-i-might-face-when-creating-clusters"></a>Vilka fel kan jag stöta på när jag skapar kluster?

| Fel | Mer information |
|---|---|
| Ingen kvot | Det finns kvoter för antalet kärnor som du kan skapa i din prenumeration i varje region. Mer information finns i [kapacitets planering: kvoter](./hdinsight-capacity-planning.md). |
| Inga fler IP-adresser är tillgängliga | Varje VNet har ett begränsat antal IP-adresser. När du skapar ett HDInsight-kluster använder varje nod (inklusive Zookeeper och gateway-noder) vissa av dessa tilldelade IP-adresser. När alla IP-adresser används kommer du att upptäcka det här felet.  |
| Regler för nätverks säkerhets grupper (NSG) tillåter inte kommunikation med HDInsight-resurs leverantörer | Om du använder NSG: er eller användardefinierade vägar (UDR) för att styra inkommande trafik till ditt HDInsight-kluster, måste du se till att klustret kan kommunicera med kritiska Azure-tjänster för hälso tillstånd och hantering. Mer information finns i [tjänst taggar för nätverks säkerhets grupper (NSG) för Azure HDInsight](./hdinsight-service-tags.md) |
| Åter användning av kluster namn | När du använder ett kluster namn som du har använt tidigare måste du vänta X antal minuter innan du skapar klustret igen. Annars visas ett meddelande om att resursen redan finns. |

## <a name="how-do-i-manage-running-hdinsight-clusters"></a>Hur gör jag för att hantera att köra HDInsight-kluster?

| Alternativ | Dokument |
|---|---|
| Automatisk skalning | [Skala Azure HDInsight-kluster automatiskt](./hdinsight-autoscale-clusters.md) |
| Manuell skalning | [Skala Azure HDInsight-kluster](./hdinsight-scaling-best-practices.md) |
| Övervakning med Ambari| [Övervaka kluster prestanda i Azure HDInsight](./hdinsight-key-scenarios-to-monitor.md) |
| Övervakning med Azure Monitor loggar | [Använda Azure Monitor-loggar för att övervaka HDInsight-kluster](./hdinsight-hadoop-oms-log-analytics-tutorial.md) |
| Tjänst problem, planerat underhåll, hälso & säkerhets rådgivare | [Prenumerera på prenumerations aviseringar för viss tjänst](../service-health/alerts-activity-log-service-notifications-portal.md) |


## <a name="how-do-i-check-on-deleted-hdinsight-clusters"></a>Hur gör jag för att kontroll av borttagna HDInsight-kluster?

### <a name="azure-monitor-logs"></a>Azure Monitor-loggar

Du kan använda följande fråga med Azure Monitor loggar för att övervaka borttagna kluster.

```loganalytics
AzureActivity
| where ResourceProvider == "Microsoft.HDInsight" and (OperationName == "Create or Update Cluster" or OperationName == "Delete Cluster") and ActivityStatus == "Succeeded"
```

## <a name="next-steps"></a>Nästa steg

* [Kapacitetsplanering för HDInsight-kluster](./hdinsight-capacity-planning.md)
* [Vilka är standard och rekommenderade nodkonfigurationer för Azure HDInsight?](./hdinsight-supported-node-configuration.md)
