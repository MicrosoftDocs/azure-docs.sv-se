---
title: Distributions & HPA mått med container Insights | Microsoft Docs
description: I den här artikeln beskrivs vilken distribution & HPA (horisontell Pod autoskalning) som samlas in med container Insights.
ms.topic: conceptual
ms.date: 08/09/2020
ms.openlocfilehash: c8bb100b756ea92d73e1c3a698f119b4f8157930
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101717714"
---
# <a name="deployment--hpa-metrics-with-container-insights"></a>Distributions & HPA mått med container Insights

Från och med agent version *ciprod08072020* samlar behållar insikter – integrerad agent nu in mått för distributioner & HPAs.

## <a name="deployment-metrics"></a>Distributions mått

Behållar insikter startar automatiskt övervakning av distributioner, genom att samla in följande mått vid 60 sekunders intervall och lagra dem i tabellen **InsightMetrics** :

|Måttnamn |Mått dimension (Taggar) |Beskrivning |
|------------|------------------------|------------|
|kube_deployment_status_replicas_ready |container.azm.ms/clusterId, container.azm.ms/clusterName, creationTime, Deployment, deploymentStrategy, k8sNamespace, spec_replicas, status_replicas_available, status_replicas_updated (status. updatedReplicas) | Totalt antal klara poddar som är riktade till den här distributionen (status. readyReplicas). Nedan visas måtten för det här måttet. <ul> <li> distribution – distributions namn </li> <li> k8sNamespace – Kubernetes-namnrymd för distributionen </li> <li> deploymentStrategy – distributions strategi som ska användas för att ersätta poddar med nya (spec. strategi. Type)</li><li> creationTime – tidsstämpel för skapande av distribution </li> <li> spec_replicas-antal önskade poddar (spec. Replicas) </li> <li>status_replicas_available-totalt antal tillgängliga poddar (redo för minst minReadySeconds) som är målet för den här distributionen (status. availableReplicas)</li><li>status_replicas_updated-totalt antal icke-avslutade poddar som är riktade till den här distributionen och som har den önskade mal Lav specifikationen (status. updatedReplicas) </li></ul>|

## <a name="hpa-metrics"></a>HPA mått

Behållar insikter startar automatiskt övervakning av HPAs, genom att samla in följande mått vid 60 sekunders intervall och lagra dem i **InsightMetrics** -tabellen:

|Måttnamn |Mått dimension (Taggar) |Beskrivning |
|------------|------------------------|------------|
|kube_hpa_status_current_replicas |container.azm.ms/clusterId, container.azm.ms/clusterName, creationTime, hPa, k8sNamespace, lastScaleTime, spec_max_replicas, spec_min_replicas, status_desired_replicas, targetKind, targetName | Aktuellt antal repliker av poddar som hanteras av den här autoskalning (status. currentReplicas). Nedan visas måtten för det här måttet. <ul> <li> hPa-namnet på HPA </li> <li> k8sNamespace – Kubernetes-namnrymd för HPA </li> <li> lastScaleTime-senaste gången HPA skalades antalet poddar (status. lastScaleTime)</li><li> creationTime – HPA-skapande av tidsstämpel </li> <li> spec_max_replicas övre gräns för antalet poddar som kan anges av autoskalning (spec. maxReplicas) </li> <li> spec_min_replicas-nedre gräns för antalet repliker som autoskalning kan skalas ned till (spec. minReplicas) </li><li>status_desired_replicas-önskat antal repliker av poddar som hanteras av den här autoskalning (status. desiredReplicas)</li><li>targetKind-typ av HPA mål (spec. scaleTargetRef. Natura) </li><li>targetName-namn på HPA Target (spec.scaleTargetRef.name) </li></ul>|

## <a name="deployment--hpa-charts"></a>HPA-diagram för distributions & 

Behållar insikter innehåller förkonfigurerade diagram för de mått som anges tidigare i tabellen som en arbets bok för varje kluster. Du hittar distributioner & HPA arbets boks **distributioner & hPa** direkt från ett AKS-kluster genom att välja **arbets böcker** i den vänstra rutan och från List rutan **Visa arbets böcker** i insikten.

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Kube i Kubernetes](https://github.com/kubernetes/kube-state-metrics/tree/master/docs) om du vill veta mer om Kube-tillstånds mått.