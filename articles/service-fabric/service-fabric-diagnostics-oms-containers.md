---
title: Övervaka behållare med Azure Monitor loggar
description: Använd Azure Monitor loggar för att övervaka behållare som körs på Azure Service Fabric-kluster.
ms.topic: conceptual
ms.date: 02/25/2019
ms.openlocfilehash: 6217569dc50517c88a5a8a7bc0f3752e7e327f4e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2021
ms.locfileid: "105626664"
---
# <a name="monitor-containers-with-azure-monitor-logs"></a>Övervaka behållare med Azure Monitor loggar
 
Den här artikeln beskriver de steg som krävs för att ställa in övervaknings lösningen Azure Monitor loggar container för att Visa behållar händelser. Information om hur du konfigurerar klustret för att samla in behållar händelser finns i den här [steg-för-steg-självstudien](service-fabric-tutorial-monitoring-wincontainers.md). 

[!INCLUDE [log-analytics-agent-note.md](../../includes/log-analytics-agent-note.md)]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="set-up-the-container-monitoring-solution"></a>Konfigurera lösningen för övervakning av behållare

> [!NOTE]
> Du måste ha Azure Monitor loggar som har kon figurer ATS för klustret samt ha Log Analytics-agenten distribuerad på noderna. Om du inte gör det följer du stegen i [konfigurera Azure Monitor loggar](service-fabric-diagnostics-oms-setup.md) och [lägger till Log Analytics-agenten i ett kluster](service-fabric-diagnostics-oms-agent.md) först.

1. När klustret har kon figurer ATS med Azure Monitor loggar och Log Analytics agent distribuerar du dina behållare. Vänta tills dina behållare har distribuerats innan du går vidare till nästa steg.

2. På Azure Marketplace söker du efter *lösning för övervakning av behållare* och klickar på den **behållare för övervakning av behållare** som visas under kategorin övervakning och hantering.

    ![Lägga till containerlösning](./media/service-fabric-diagnostics-event-analysis-oms/containers-solution.png)

3. Skapa lösningen inuti samma arbets yta som redan har skapats för klustret. Den här ändringen utlöser automatiskt agenten för att börja samla in Docker-data på behållarna. Om 15 minuter eller så bör lösningen visas med inkommande loggar och statistik, som du ser i bilden nedan.

    ![Grundläggande Log Analytics-instrumentpanel](./media/service-fabric-diagnostics-event-analysis-oms/oms-containers-dashboard.png)

Agenten aktiverar insamling av flera behållar-/regionsspecifika loggar som kan frågas i Azure Monitor loggar eller som används för att visualisera prestanda indikatorer. De logg typer som samlas in är:

* ContainerInventory: visar information om container plats, namn och bilder
* ContainerImageInventory: information om distribuerade avbildningar, inklusive ID: n eller storlekar
* ContainerLog: vissa fel loggar, Docker-loggar (STDOUT osv.) och andra poster
* ContainerServiceLog: Docker daemon-kommandon som har körts
* Perf: prestanda räknare, inklusive processor CPU, minne, nätverks trafik, disk-i/o och anpassade mått från värddatorerna



## <a name="next-steps"></a>Nästa steg
* Läs mer om [behållar lösningen för Azure Monitor loggar](../azure-monitor/containers/containers.md).
* Läs mer om behållar dirigering på Service Fabric- [Service Fabric och behållare](service-fabric-containers-overview.md)
* Bekanta dig med funktionerna för [loggs ökning och frågor](../azure-monitor/logs/log-query-overview.md) som erbjuds som en del av Azure Monitor loggar
* Konfigurera Azure Monitor loggar för att ställa in [automatiserade varnings](../azure-monitor/alerts/alerts-overview.md) regler som hjälper till att upptäcka och diagnostik
