---
title: Konfigurera GPU-övervakning med container Insights | Microsoft Docs
description: Den här artikeln beskriver hur du kan konfigurera övervakning av Kubernetes-kluster med NVIDIA-och AMD GPU-aktiverade noder med behållar insikter.
ms.topic: conceptual
ms.date: 03/27/2020
ms.openlocfilehash: 2958b000ac0dabcd7fddf75a58f553b705a95e9a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/03/2021
ms.locfileid: "101731875"
---
# <a name="configure-gpu-monitoring-with-container-insights"></a>Konfigurera GPU-övervakning med container Insights

Från och med agent version *ciprod03022019* har den integrerade agenten för behållar insikter nu stöd för övervakning av GPU (grafik bearbetnings enheter) på GPU-medvetna Kubernetes klusternoder och övervakar poddar/behållare som begär och använder GPU-resurser.

## <a name="supported-gpu-vendors"></a>GPU-leverantörer som stöds

Container Insights stöder övervakning av GPU-kluster från följande GPU-leverantörer:

- [NVIDIA](https://developer.nvidia.com/kubernetes-gpu)

- [EFFEKTIV](https://github.com/RadeonOpenCompute/k8s-device-plugin)

Behållar insikter startar automatiskt övervakning av GPU-användning på noder, och GPU begär poddar och arbets belastningar genom att samla in följande mått vid 60sec-intervall och lagra dem i **InsightMetrics** -tabellen.

>[!NOTE]
>När du har upprättat klustret med GPU-noder kontrollerar du att [GPU-drivrutinen](../../aks/gpu-cluster.md) installeras som krävs av AKS för att köra GPU-arbetsbelastningar. Container Insights samlar in GPU-mått via GPU-drivrutinen poddar som körs i noden. 

|Måttnamn |Mått dimension (Taggar) |Beskrivning |
|------------|------------------------|------------|
|containerGpuDutyCycle |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor|Procent andel av tiden under den senaste samplings perioden (60 sekunder) under vilken GPU var upptagen/aktivt bearbetas för en behållare. Månads cykel är ett tal mellan 1 och 100. |
|containerGpuLimits |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName |Varje behållare kan ange gränser som en eller flera GPU: er. Det går inte att begära eller begränsa en bråkdel av en GPU. |
|containerGpuRequests |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName |Varje behållare kan begära en eller flera GPU: er. Det går inte att begära eller begränsa en bråkdel av en GPU.|
|containerGpumemoryTotalBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor |Mängden GPU-minne i byte som är tillgängligt för användning för en speciell behållare. |
|containerGpumemoryUsedBytes |container.azm.ms/clusterId, container.azm.ms/clusterName, containerName, gpuId, gpuModel, gpuVendor |Mängden GPU-minne i byte som används av en speciell behållare. |
|nodeGpuAllocatable |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Antal GPU: er i en nod som kan användas av Kubernetes. |
|nodeGpuCapacity |container.azm.ms/clusterId, container.azm.ms/clusterName, gpuVendor |Totalt antal GPU: er i en nod. |

## <a name="gpu-performance-charts"></a>Diagram över GPU-prestanda 

Behållar insikter innehåller förkonfigurerade diagram för de mått som anges tidigare i tabellen som en GPU-arbetsbok för varje kluster. Se [arbets böcker i behållar insikter](../insights/container-insights-reports.md) för en beskrivning av arbets böckerna som är tillgängliga för behållar insikter.

## <a name="next-steps"></a>Nästa steg

- Se [använda GPU: er för beräknings intensiva arbets belastningar i Azure Kubernetes service](../../aks/gpu-cluster.md) (AKS) för att lära dig hur du distribuerar ett AKS-kluster som innehåller GPU-aktiverade noder.

- Lär dig mer om [GPU-optimerade VM SKU: er i Microsoft Azure](../../virtual-machines/sizes-gpu.md).

- Granska [GPU-stödet i Kubernetes](https://kubernetes.io/docs/tasks/manage-gpus/scheduling-gpus/) för att lära dig mer om stöd för Kubernetes-experiment för hantering av GPU: er på en eller flera noder i ett kluster.
