---
title: Prestanda övervakning med Azure Monitor loggar
description: Lär dig hur du konfigurerar Log Analytics agent för övervakning av behållare och prestanda räknare för Azure Service Fabric-kluster.
author: srrengar
ms.topic: conceptual
ms.date: 04/16/2018
ms.author: srrengar
ms.openlocfilehash: 47017fdb5f1fbaba800e71dea21afe2c39bd91e7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "100570153"
---
# <a name="performance-monitoring-with-azure-monitor-logs"></a>Prestanda övervakning med Azure Monitor loggar

Den här artikeln beskriver hur du lägger till Log Analytics agenten som ett tillägg för skalnings uppsättning för virtuella datorer i klustret och ansluter det till din befintliga Azure Log Analytics-arbetsyta. Detta möjliggör insamling av diagnostikdata om behållare, program och prestanda övervakning. Genom att lägga till det som ett tillägg för den virtuella datorns skalnings uppsättnings resurs, ser Azure Resource Manager till att den installeras på varje nod, även när du skalar klustret.

> [!NOTE]
> Den här artikeln förutsätter att du redan har skapat en Azure Log Analytics-arbetsyta. Om du inte gör det kan du [konfigurera Azure Monitor loggar](service-fabric-diagnostics-oms-setup.md)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="add-the-agent-extension-via-azure-cli"></a>Lägg till agent tillägget via Azure CLI

Det bästa sättet att lägga till Log Analytics agent i klustret är via de API: er för skalnings uppsättningen för virtuella datorer som är tillgängliga med Azure CLI. Om du inte har konfigurerat Azure CLI ännu går du till Azure Portal och öppnar en [Cloud Shell](../cloud-shell/overview.md) instans eller [installerar Azure CLI](/cli/azure/install-azure-cli).

1. När din Cloud Shell har begärts kontrollerar du att du arbetar i samma prenumeration som din resurs. Kontrol lera detta med `az account show` och se till att värdet "name" stämmer överens med klustrets prenumeration.

2. I portalen navigerar du till resurs gruppen där Log Analytics arbets ytan finns. Klicka i Log Analytics-resursen (resurs typen kommer att Log Analytics arbets ytan). När du är på resurs översikts sidan klickar du på **Avancerade inställningar** under avsnittet Inställningar på den vänstra menyn.

    ![Egenskaps sida för Log Analytics](media/service-fabric-diagnostics-oms-agent/oms-advanced-settings.png)

3. Klicka på **Windows-servrar** om du skapar ett Windows-kluster och **Linux-servrar** om du skapar ett Linux-kluster. På den här sidan visas dina `workspace ID` och `workspace key` (visas som primär nyckel i portalen). Du behöver båda för nästa steg.

4. Kör kommandot för att installera Log Analytics agenten på klustret med hjälp av `vmss extension set` API: et:

    För ett Windows-kluster:

    ```azurecli
    az vmss extension set --name MicrosoftMonitoringAgent --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    För ett Linux-kluster:

    ```azurecli
    az vmss extension set --name OmsAgentForLinux --publisher Microsoft.EnterpriseCloud.Monitoring --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType> --settings "{'workspaceId':'<Log AnalyticsworkspaceId>'}" --protected-settings "{'workspaceKey':'<Log AnalyticsworkspaceKey>'}"
    ```

    Här är ett exempel på Log Analytics-agenten som läggs till i ett Windows-kluster.

    ![Kommandot Log Analytics agent cli](media/service-fabric-diagnostics-oms-agent/cli-command.png)

5. Detta bör ta mindre än 15 min för att lägga till agenten i noderna. Du kan kontrol lera att agenterna har lagts till med hjälp av `az vmss extension list` API: et:

    ```azurecli
    az vmss extension list --resource-group <nameOfResourceGroup> --vmss-name <nameOfNodeType>
    ```

## <a name="add-the-agent-via-the-resource-manager-template"></a>Lägg till agenten via Resource Manager-mallen

Exempel på Resource Manager-mallar som distribuerar en Azure Log Analytics-arbetsyta och lägger till en agent till varje nod är tillgänglig för [Windows](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure) eller [Linux](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeType-Secure-OMS).

Du kan hämta och ändra den här mallen för att distribuera ett kluster som passar dina behov bäst.

## <a name="view-performance-counters"></a>Visa prestanda räknare

Nu när du har lagt till Log Analytics-agenten går du till Log Analytics Portal och väljer vilka prestanda räknare som du vill samla in.

1. I Azure Portal går du till resurs gruppen där du skapade Service Fabric-analys-lösningen. Välj **ServiceFabric \<nameOfLog AnalyticsWorkspace\>**.

2. Klicka på **Log Analytics**.

3. Klicka på **Avancerade inställningar**.

4. Klicka på **data** och sedan på **Windows-eller Linux-prestandaräknare**. Det finns en lista med standard räknare som du kan välja att aktivera och du kan ange intervallet för samling. Du kan också lägga till [ytterligare prestanda räknare](service-fabric-diagnostics-event-generation-perf.md) som ska samlas in. Rätt format refereras till i den här [artikeln](/windows/win32/perfctrs/specifying-a-counter-path).

5. Klicka på **Spara** och sedan på **OK**.

6. Stäng bladet avancerade inställningar.

7. Under rubriken Allmänt klickar du på **arbets ytan Sammanfattning**.

8. Paneler visas i form av en graf för var och en av de lösningar som är aktiverade, inklusive en för Service Fabric. Fortsätt till Service Fabric-analys-lösningen genom att klicka på **Service Fabric** graf.

9. Du kommer att se några paneler med grafer på drifts kanal och Reliable Services-händelser. Den grafiska åter givningen av de data som flödar in för de räknare du har valt visas under Node-mått.

10. Klicka på ett diagram för container mått om du vill visa mer information. Du kan också fråga om prestanda räknar data på liknande sätt som kluster händelser och filter på noderna, prestanda räknarens namn och värden med hjälp av Kusto-frågespråket.

![Fråga för Log Analytics prestanda räknare](media/service-fabric-diagnostics-event-analysis-oms/oms_node_metrics_table.PNG)

## <a name="next-steps"></a>Nästa steg

* Samla in relevanta [prestanda räknare](service-fabric-diagnostics-event-generation-perf.md). Om du vill konfigurera Log Analytics agenten för att samla in vissa prestanda räknare granskar du [konfigurationen av data källor](../azure-monitor/agents/agent-data-sources.md#configuring-data-sources).
* Konfigurera Azure Monitor loggar för att ställa in [automatiserad avisering](../azure-monitor/alerts/alerts-overview.md) till hjälp vid identifiering och diagnostik
* Alternativt kan du samla in prestanda räknare via [Azure-diagnostik tillägget och skicka dem till Application Insights](service-fabric-diagnostics-event-aggregation-wad.md#add-the-application-insights-sink-to-the-resource-manager-template)
