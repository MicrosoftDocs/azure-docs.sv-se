---
title: Konfigurera PV-övervakning med Azure Monitor för behållare | Microsoft Docs
description: I den här artikeln beskrivs hur du kan konfigurera övervakning av Kubernetes-kluster med beständiga volymer med Azure Monitor för behållare.
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: d7da6bc88e7c8526e3940714502d3c92d2f37dd8
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2021
ms.locfileid: "98704481"
---
# <a name="configure-pv-monitoring-with-azure-monitor-for-containers"></a>Konfigurera PV-övervakning med Azure Monitor för behållare

Från och med agent version *ciprod10052020* har Azure Monitor for containers Integrated agent nu stöd för övervakning av PV (beständig volym).

## <a name="pv-metrics"></a>PV-mått

Azure Monitor för behållare börjar automatiskt att övervaka PV genom att samla in följande mått vid 60sec-intervall och lagra dem i **InsightMetrics** -tabellen.

|Måttnamn |Mått dimension (Taggar) |Beskrivning |
|------------|------------------------|------------|
| `pvUsedBytes`|`container.azm.ms/pv`|Använt utrymme i byte för en speciell permanent volym med ett anspråk som används av en speciell pod. `pvCapacityBytes` viks som en dimension i fältet taggar för att minska kostnaderna för data inmatning och för att förenkla frågor.|

## <a name="monitor-persistent-volumes"></a>Övervaka beständiga volymer

Azure Monitor för behållare innehåller förkonfigurerade diagram för måttet i en arbets bok för varje kluster. Du hittar diagrammen på fliken beständig volym i arbets **belastnings informations** arbets boken direkt från ett AKS-kluster genom att välja arbets böcker i rutan till vänster och från List rutan **Visa arbets böcker** i insikten. Du kan också aktivera en rekommenderad avisering för PV-användning, samt fråga dessa mått i Log Analytics.  

![Exempel på arbets belastning i Azure Monitor PV](./media/container-insights-persistent-volumes/pv-workload-example.PNG)

## <a name="next-steps"></a>Nästa steg

- Läs mer om de insamlade PV-måtten [här](./container-insights-agent-config.md).
