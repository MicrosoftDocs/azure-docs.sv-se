---
title: Konfigurera PV-övervakning med container Insights | Microsoft Docs
description: I den här artikeln beskrivs hur du kan konfigurera övervakning av Kubernetes-kluster med beständiga volymer med behållar insikter.
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 16c87177a8911a7b88284606f54f7bf6e0e0daa3
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2021
ms.locfileid: "105026112"
---
# <a name="configure-pv-monitoring-with-container-insights"></a>Konfigurera PV-övervakning med container Insights

Från och med agent version *ciprod10052020* har Azure Monitor for containers Integrated agent nu stöd för övervakning av PV (beständig volym). Med agent version *ciprod01112021* har agenten stöd för övervakning av PV-inventering, inklusive information om status, lagrings klass, typ, åtkomst lägen och annan information.
## <a name="pv-metrics"></a>PV-mått

Behållar insikter startar automatiskt övervakning av PV-användning genom att samla in följande mått vid 60-sec-intervall och lagra dem i **InsightMetrics** -tabellen.

| Mått namn | Mått dimension (Taggar) | Mått Beskrivning | | `pvUsedBytes`| podUID, podName, pvcName, pvcNamespace, capacityBytes, clusterId, kluster namn | Använt utrymme i byte för en speciell permanent volym med ett anspråk som används av en speciell pod. `capacityBytes` är viks i som en dimension i fältet taggar för att minska kostnaderna för data inmatning och för att förenkla frågor. |

Lär dig mer om att konfigurera insamlade PV-mått [här](./container-insights-agent-config.md).

## <a name="pv-inventory"></a>PV-inventering

Azure Monitor for containers startar automatiskt övervakning av PVs genom att samla in följande information vid 60-sec-intervall och lagra dem i **KubePVInventory** -tabellen.

|Data |Datakälla| Datatyp| Fält|
|-----|-----------|----------|-------|
|Inventering av beständiga volymer i ett Kubernetes-kluster |Kube-API |`KubePVInventory` | PVName, PVCapacityBytes, PVCName, PVCNamespace, PVStatus, PVAccessModes, PVType, PVTypeInfo, PVStorageClassName, PVCreationTimestamp, TimeGenerated, ClusterId, kluster namn, _ResourceId |

## <a name="monitor-persistent-volumes"></a>Övervaka beständiga volymer

Azure Monitor för behållare innehåller förkonfigurerade diagram för det här användnings måttet och inventerings information i arbetsboksfiler för varje kluster. Du kan också aktivera en rekommenderad avisering för PV-användning och fråga dessa mått i Log Analytics.  

### <a name="workload-details-workbook"></a>Arbets belastnings informations arbets bok

Du kan hitta användnings diagram för olika arbets belastningar på fliken beständig volym i arbets ytans **informations** arbets bok direkt från ett AKS-kluster genom att välja arbets böcker i den vänstra rutan, i list rutan **Visa arbets böcker** i fönstret insikter eller från **fliken rapporter (förhands granskning)** i fönstret insikter.


:::image type="content" source="./media/container-insights-persistent-volumes/pv-workload-example.PNG" alt-text="Exempel på arbets belastning i Azure Monitor PV":::

### <a name="persistent-volume-details-workbook"></a>Beständig volym informations arbets bok

Du hittar en översikt över beständig volym inventering i arbets boken **beständig volym information** direkt från ett AKS-kluster genom att välja arbets böcker i den vänstra rutan i list rutan **Visa arbets böcker** i fönstret insikter eller från fliken **rapporter (förhands granskning)** i fönstret insikter.


:::image type="content" source="./media/container-insights-persistent-volumes/pv-details-workbook-example.PNG" alt-text="Exempel på kalkyl blad för Azure Monitor PV-information":::

### <a name="persistent-volume-usage-recommended-alert"></a>Avisering om beständig volym användning
Du kan aktivera en rekommenderad avisering som varnar dig när den genomsnittliga PV-användningen för en pod är över 80%. Läs mer om avisering [här](./container-insights-metric-alerts.md) och hur du åsidosätter standard tröskelvärdet [här](./container-insights-metric-alerts.md#configure-alertable-metrics-in-configmaps).
## <a name="next-steps"></a>Nästa steg

- Läs mer om de insamlade PV-måtten [här](./container-insights-agent-config.md).