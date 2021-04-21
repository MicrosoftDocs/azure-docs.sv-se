---
title: Använda Azure Monitor loggar för att övervaka Azure HDInsight kluster
description: Lär dig hur du använder Azure Monitor för att övervaka jobb som körs i ett HDInsight-kluster.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-azurecli, devx-track-azurepowershell
ms.date: 05/13/2020
ms.openlocfilehash: 2a81b25b08708a878fc8ff83cf19c643036b8f90
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107770335"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Använda Azure Monitor-loggar för att övervaka HDInsight-kluster

Lär dig hur du aktiverar Azure Monitor för att övervaka Hadoop-klusteråtgärder i HDInsight. Och hur du lägger till en HDInsight-övervakningslösning.

[Azure Monitor är](../azure-monitor/logs/log-query-overview.md) en Azure Monitor tjänst som övervakar dina molnmiljöer och lokala miljöer. Övervakningen är att upprätthålla deras tillgänglighet och prestanda. Den samlar in data som genereras av resurser i molnet, lokala miljöer och från andra övervakningsverktyg. Data används för att tillhandahålla analys över flera källor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* En Log Analytics-arbetsyta. Du kan se den här arbetsytan som en unik Azure Monitor med en egen datalager, datakällor och lösningar. Anvisningar finns i Skapa [en Log Analytics-arbetsyta.](../azure-monitor/vm/quick-collect-azurevm.md#create-a-workspace)

* Ett Azure HDInsight-kluster. För närvarande kan du använda Azure Monitor loggar med följande typer av HDInsight-kluster:

  * Hadoop
  * HBase
  * Interaktiv fråga
  * Kafka
  * Spark
  * Storm

  Anvisningar om hur du skapar ett HDInsight-kluster finns i [Kom igång med Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* Om du använder PowerShell behöver du [Az-modulen](/powershell/azure/). Kontrollera att du har den senaste versionen. Om det behövs kör du `Update-Module -Name Az` .

* Om du vill använda Azure CLI och inte har installerat det än kan du gå [till Installera Azure CLI.](/cli/azure/install-azure-cli)

> [!NOTE]  
> Vi rekommenderar att du placerar både HDInsight-klustret och Log Analytics-arbetsytan i samma region för bättre prestanda. Azure Monitor loggar är inte tillgängliga i alla Azure-regioner.

## <a name="enable-azure-monitor-using-the-portal"></a>Aktivera Azure Monitor med hjälp av portalen

I det här avsnittet konfigurerar du ett befintligt HDInsight Hadoop-kluster att använda en Azure Log Analytics-arbetsyta för att övervaka jobb, felsökningsloggar och så vidare.

1. Från [Azure Portal](https://portal.azure.com/)väljer du klustret. Klustret öppnas på en ny portalsida.

1. Till vänster under **Övervakning väljer** du **Azure Monitor**.

1. I huvudvyn går du till **Azure Monitor Integration och** väljer **Aktivera**.

1. I **listrutan Välj en** arbetsyta väljer du en befintlig Log Analytics-arbetsyta.

1. Välj **Spara**.  Det tar en stund att spara inställningen.

    :::image type="content" source="./media/hdinsight-hadoop-oms-log-analytics-tutorial/azure-portal-monitoring.png" alt-text="Aktivera övervakning för HDInsight-kluster":::

## <a name="enable-azure-monitor-using-azure-powershell"></a>Aktivera Azure Monitor med Azure PowerShell

Du kan aktivera Azure Monitor-loggar med hjälp Azure PowerShell [Az-modulen Enable-AzHDInsightMonitoring-cmdlet.](/powershell/module/az.hdinsight/enable-azhdinsightmonitoring)

```powershell
# Enter user information
$resourceGroup = "<your-resource-group>"
$cluster = "<your-cluster>"
$LAW = "<your-Log-Analytics-workspace>"
# End of user input

# obtain workspace id for defined Log Analytics workspace
$WorkspaceId = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW).CustomerId

# obtain primary key for defined Log Analytics workspace
$PrimaryKey = (Get-AzOperationalInsightsWorkspace `
                    -ResourceGroupName $resourceGroup `
                    -Name $LAW | Get-AzOperationalInsightsWorkspaceSharedKeys).PrimarySharedKey

# Enables monitoring and relevant logs will be sent to the specified workspace.
Enable-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster `
    -WorkspaceId $WorkspaceId `
    -PrimaryKey $PrimaryKey

# Gets the status of monitoring installation on the cluster.
Get-AzHDInsightMonitoring `
    -ResourceGroupName $resourceGroup `
    -Name $cluster
```

Om du vill inaktivera använder du [cmdleten Disable-AzHDInsightMonitoring:](/powershell/module/az.hdinsight/disable-azhdinsightmonitoring)

```powershell
Disable-AzHDInsightMonitoring -Name "<your-cluster>"
```

## <a name="enable-azure-monitor-using-azure-cli"></a>Aktivera Azure Monitor med Azure CLI

Du kan aktivera Azure Monitor loggar med hjälp av kommandot Azure CLI `[az hdinsight monitor enable` ](/cli/azure/hdinsight/monitor#az_hdinsight_monitor_enable).

```azurecli
# set variables
export resourceGroup=RESOURCEGROUPNAME
export cluster=CLUSTERNAME
export LAW=LOGANALYTICSWORKSPACENAME

# Enable the Azure Monitor logs integration on an HDInsight cluster.
az hdinsight monitor enable --name $cluster --resource-group $resourceGroup --workspace $LAW

# Get the status of Azure Monitor logs integration on an HDInsight cluster.
az hdinsight monitor show --name $cluster --resource-group $resourceGroup
```

Om du vill inaktivera använder du [`az hdinsight monitor disable`](/cli/azure/hdinsight/monitor#az_hdinsight_monitor_disable) kommandot .

```azurecli
az hdinsight monitor disable --name $cluster --resource-group $resourceGroup
```

## <a name="install-hdinsight-cluster-management-solutions"></a>Installera HDInsight-klusterhanteringslösningar

HDInsight tillhandahåller klusterspecifika hanteringslösningar som du kan lägga till för Azure Monitor loggar. [Hanteringslösningar lägger](../azure-monitor/insights/solutions.md) till funktioner i Azure Monitor loggar, vilket ger ytterligare data- och analysverktyg. Dessa lösningar samlar in viktiga prestandamått från dina HDInsight-kluster. Och tillhandahålla verktyg för att söka i måtten. Dessa lösningar tillhandahåller även visualiseringar och instrumentpaneler för de flesta klustertyper som stöds i HDInsight. Genom att använda de mått som du samlar in med lösningen kan du skapa anpassade övervakningsregler och aviseringar.

Tillgängliga HDInsight-lösningar:

* HDInsight Hadoop-övervakning
* HDInsight HBase–övervakning
* HDInsight Interaktiv fråga övervakning
* HDInsight Kafka-övervakning
* HDInsight Spark-övervakning
* HDInsight Storm-övervakning

Anvisningar för hanteringslösningen finns [i Hanteringslösningar i Azure](../azure-monitor/insights/solutions.md#install-a-monitoring-solution). Om du vill experimentera installerar du en HDInsight Hadoop-övervakningslösning. När det är klart visas en **HDInsightHadoop-panel** under **Sammanfattning.** Välj **panelen HDInsightHadoop.** HDInsightHadoop-lösningen ser ut så här:

:::image type="content" source="media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png" alt-text="Vy över HDInsight-övervakningslösning":::

Eftersom klustret är ett helt nytt kluster visas inga aktiviteter i rapporten.

## <a name="configuring-performance-counters"></a>Konfigurera prestandaräknare

Azure Monitor stöder insamling och analys av prestandamått för noderna i klustret. Mer information finns i [Datakällor för Linux-prestanda i Azure Monitor](../azure-monitor/agents/data-sources-performance-counters.md#linux-performance-counters).

## <a name="cluster-auditing"></a>Klustergranskning

HDInsight stöder klustergranskning med Azure Monitor loggar genom att importera följande typer av loggar:

* `log_gateway_audit_CL` – den här tabellen innehåller granskningsloggar från klustergatewaynoder som visar lyckade och misslyckade inloggningsförsök.
* `log_auth_CL` – den här tabellen innehåller SSH-loggar med lyckade och misslyckade inloggningsförsök.
* `log_ambari_audit_CL` – den här tabellen innehåller granskningsloggar från Ambari.
* `log_ranger_audti_CL` – den här tabellen innehåller granskningsloggar från Apache Ranger på ESP-kluster.

## <a name="next-steps"></a>Nästa steg

* [Fråga Azure Monitor loggar för att övervaka HDInsight-kluster](hdinsight-hadoop-oms-log-analytics-use-queries.md)
* [Övervaka klustertillgänglighet med Apache Ambari och Azure Monitor loggar](./hdinsight-cluster-availability.md)