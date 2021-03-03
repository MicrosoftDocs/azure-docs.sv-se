---
title: Konfigurera PV-övervakning med container Insights | Microsoft Docs
description: I den här artikeln beskrivs hur du kan konfigurera övervakning av Kubernetes-kluster med beständiga volymer med behållar insikter.
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: 0afbeab49a6909a0011cd75a0419f7325ca68132
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713736"
---
# <a name="configure-pv-monitoring-with-container-insights"></a>Konfigurera PV-övervakning med container Insights

Från och med agent version *ciprod10052020* har den integrerade agenten för container Insights nu stöd för övervakning av PV (beständig volym).

## <a name="pv-metrics"></a>PV-mått

Container Insights börjar automatiskt att övervaka PV genom att samla in följande mått vid 60sec-intervall och lagra dem i **InsightMetrics** -tabellen.

|Måttnamn |Mått dimension (Taggar) |Beskrivning |
|------------|------------------------|------------|
| `pvUsedBytes`|`container.azm.ms/pv`|Använt utrymme i byte för en speciell permanent volym med ett anspråk som används av en speciell pod. `pvCapacityBytes` viks som en dimension i fältet taggar för att minska kostnaderna för data inmatning och för att förenkla frågor.|

## <a name="monitor-persistent-volumes"></a>Övervaka beständiga volymer

Behållar insikter innehåller förkonfigurerade diagram för måttet i en arbets bok för varje kluster. Du hittar diagrammen på fliken beständig volym i arbets **belastnings informations** arbets boken direkt från ett AKS-kluster genom att välja arbets böcker i rutan till vänster och från List rutan **Visa arbets böcker** i insikten. Du kan också aktivera en rekommenderad avisering för PV-användning, samt fråga dessa mått i Log Analytics.  

![Exempel på arbets belastning i Azure Monitor PV](./media/container-insights-persistent-volumes/pv-workload-example.PNG)

## <a name="next-steps"></a>Nästa steg

- Läs mer om de insamlade PV-måtten [här](./container-insights-agent-config.md).
