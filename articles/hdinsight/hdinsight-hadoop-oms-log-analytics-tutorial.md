---
title: Använd Azure Monitor loggar för att övervaka Azure HDInsight-kluster
description: Lär dig hur du använder Azure Monitor loggar för att övervaka jobb som körs i ett HDInsight-kluster.
ms.service: hdinsight
ms.topic: how-to
ms.custom: seoapr2020, devx-track-azurecli, devx-track-azurepowershell
ms.date: 05/13/2020
ms.openlocfilehash: 5f849248021de3c17b69517d214f6f3fd6e4ee3a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/28/2021
ms.locfileid: "98945637"
---
# <a name="use-azure-monitor-logs-to-monitor-hdinsight-clusters"></a>Använda Azure Monitor-loggar för att övervaka HDInsight-kluster

Lär dig hur du aktiverar Azure Monitor loggar för att övervaka Hadoop-kluster åtgärder i HDInsight. Och hur du lägger till en HDInsight-övervaknings lösning.

[Azure Monitor loggar](../azure-monitor/log-query/log-query-overview.md) är en Azure Monitor tjänst som övervakar molnet och lokala miljöer. Övervakningen är att upprätthålla deras tillgänglighet och prestanda. Den samlar in data som genererats av resurser i molnet, i lokala miljöer och från andra övervaknings verktyg. Data används för att tillhandahålla analyser över flera källor.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Om du inte har en Azure-prenumeration kan du [skapa ett kostnadsfritt konto ](https://azure.microsoft.com/free/) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

* En Log Analytics-arbetsyta. Du kan tänka på den här arbets ytan som en unik Azure Monitor loggar miljö med ett eget data lager, data källor och lösningar. Anvisningar finns i [skapa en Log Analytics-arbetsyta](../azure-monitor/learn/quick-collect-azurevm.md#create-a-workspace).

* Ett Azure HDInsight-kluster. För närvarande kan du använda Azure Monitor loggar med följande HDInsight-kluster typer:

  * Hadoop
  * HBase
  * Interaktiv fråga
  * Kafka
  * Spark
  * Storm

  Instruktioner för hur du skapar ett HDInsight-kluster finns i [Kom igång med Azure HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md).  

* Om du använder PowerShell behöver du AZ- [modulen](/powershell/azure/). Se till att du har den senaste versionen. Om det behövs kör du `Update-Module -Name Az` .

* Om du vill använda Azure CLI och du ännu inte har installerat det kan du läsa [Installera Azure CLI](/cli/azure/install-azure-cli).

> [!NOTE]  
> Vi rekommenderar att du placerar både HDInsight-klustret och Log Analytics arbets ytan i samma region för bättre prestanda. Azure Monitor-loggar är inte tillgängliga i alla Azure-regioner.

## <a name="enable-azure-monitor-using-the-portal"></a>Aktivera Azure Monitor med hjälp av portalen

I det här avsnittet konfigurerar du ett befintligt HDInsight Hadoop-kluster för att använda en Azure Log Analytics-arbetsyta för att övervaka jobb, fel söknings loggar och så vidare.

1. Välj ditt kluster från [Azure Portal](https://portal.azure.com/). Klustret öppnas på en ny Portal sida.

1. Gå till vänster och välj **Azure Monitor** under **övervakning**.

1. I huvudvyn väljer du **Aktivera** under **Azure Monitor-integration**.

1. I list rutan **Välj en arbets yta** väljer du en befintlig Log Analytics-arbetsyta.

1. Välj **Spara**.  Det tar en stund att spara inställningen.

    ![Aktivera övervakning för HDInsight-kluster](./media/hdinsight-hadoop-oms-log-analytics-tutorial/azure-portal-monitoring.png "Aktivera övervakning för HDInsight-kluster")

## <a name="enable-azure-monitor-using-azure-powershell"></a>Aktivera Azure Monitor med Azure PowerShell

Du kan aktivera Azure Monitor loggar med hjälp av cmdleten [Enable-AzHDInsightMonitoring](/powershell/module/az.hdinsight/enable-azhdinsightmonitoring) i Azure PowerShell AZ-modulen.

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

Om du vill inaktivera använder du cmdleten [disable-AzHDInsightMonitoring](/powershell/module/az.hdinsight/disable-azhdinsightmonitoring) :

```powershell
Disable-AzHDInsightMonitoring -Name "<your-cluster>"
```

## <a name="enable-azure-monitor-using-azure-cli"></a>Aktivera Azure Monitor med Azure CLI

Du kan aktivera Azure Monitor loggar med kommandot Azure CLI `[az hdinsight monitor enable` ] (/CLI/Azure/HDInsight/Monitor # AZ-HDInsight-Monitor-Enable).

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

Om du vill inaktivera använder du [`az hdinsight monitor disable`](/cli/azure/hdinsight/monitor#az-hdinsight-monitor-disable) kommandot.

```azurecli
az hdinsight monitor disable --name $cluster --resource-group $resourceGroup
```

## <a name="install-hdinsight-cluster-management-solutions"></a>Installera hanterings lösningar för HDInsight-kluster

HDInsight tillhandahåller klusterbaserade hanterings lösningar som du kan lägga till för Azure Monitor loggar. [Hanterings lösningar](../azure-monitor/insights/solutions.md) lägger till funktioner i Azure Monitor loggar, vilket ger ytterligare data-och analys verktyg. Dessa lösningar samlar in viktiga prestanda mått från dina HDInsight-kluster. Och ange verktyg för att söka i måtten. Dessa lösningar tillhandahåller också visualiseringar och instrument paneler för de flesta kluster typer som stöds i HDInsight. Genom att använda de mått som du samlar in med lösningen kan du skapa anpassade övervaknings regler och aviseringar.

Tillgängliga HDInsight-lösningar:

* HDInsight Hadoop-övervakning
* HDInsight HBase–övervakning
* Interaktiva frågor och övervakning av HDInsight
* HDInsight Kafka-övervakning
* HDInsight Spark-övervakning
* HDInsight Storm-övervakning

Anvisningar för hanterings lösningen finns [i hanterings lösningar i Azure](../azure-monitor/insights/solutions.md#install-a-monitoring-solution). Du kan experimentera genom att installera en HDInsight Hadoop-övervaknings lösning. När den är färdig visas en **HDInsightHadoop** -panel under **Sammanfattning**. Välj panelen **HDInsightHadoop** . HDInsightHadoop-lösningen ser ut så här:

![HDInsight övervaknings lösnings vy](media/hdinsight-hadoop-oms-log-analytics-tutorial/hdinsight-oms-hdinsight-hadoop-monitoring-solution.png)

Eftersom klustret är ett helt nytt kluster visas inga aktiviteter i rapporten.

## <a name="configuring-performance-counters"></a>Konfigurera prestanda räknare

Azure Monitor stöder insamling och analys av prestanda mått för noderna i klustret. Mer information finns i [data källor för Linux-prestanda i Azure Monitor](../azure-monitor/platform/data-sources-performance-counters.md#linux-performance-counters).

## <a name="cluster-auditing"></a>Kluster granskning

HDInsight support kluster granskning med Azure Monitor loggar genom att importera följande typer av loggar:

* `log_gateway_audit_CL` – den här tabellen innehåller gransknings loggar från klusternoder som visar lyckade och misslyckade inloggnings försök.
* `log_auth_CL` – den här tabellen innehåller SSH-loggar med lyckade och misslyckade inloggnings försök.
* `log_ambari_audit_CL` – den här tabellen innehåller gransknings loggar från Ambari.
* `log_ranger_audti_CL` – den här tabellen innehåller gransknings loggar från Apache Ranger på ESP-kluster.

## <a name="next-steps"></a>Nästa steg

* [Fråga Azure Monitor loggar för att övervaka HDInsight-kluster](hdinsight-hadoop-oms-log-analytics-use-queries.md)
* [Övervaka kluster tillgänglighet med Apache Ambari och Azure Monitor loggar](./hdinsight-cluster-availability.md)