---
title: Hög tillgänglighet för Azure SQL Edge-behållare – Azure SQL Edge
description: Lär dig mer om hög tillgänglighet för Azure SQL Edge-behållare
keywords: SQL Edge, behållare, hög tillgänglighet
services: sql-edge
ms.service: sql-edge
ms.topic: conceptual
author: SQLSourabh
ms.author: sourabha
ms.reviewer: sstein
ms.date: 09/22/2020
ms.openlocfilehash: 56b9f06547f737bc05d573f98ce1dbac2ba48758
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "90937624"
---
# <a name="high-availability-for-azure-sql-edge-containers"></a>Hög tillgänglighet för Azure SQL Edge-behållare

Skapa och hantera dina Azure SQL Edge-instanser internt i Kubernetes. Distribuera Azure SQL Edge till Docker-behållare som hanteras av [Kubernetes](https://kubernetes.io/). I Kubernetes kan en behållare med en Azure SQL Edge-instans automatiskt återställas om en klusternod Miss lyckas. Du kan konfigurera avbildningen av SQL Edge-behållaren med ett Kubernetes-beständigt volym anspråk (PVC). Kubernetes övervakar Azure SQL Edge-processen i behållaren. Om processen, pod, containern eller noden kraschar, startar Kubernetes automatiskt en annan instans och återansluter till lagringen.

## <a name="azure-sql-edge-containers-on-kubernetes"></a>Azure SQL Edge-behållare på Kubernetes

Kubernetes 1,6 och senare har stöd för [*lagrings klasser*](https://kubernetes.io/docs/concepts/storage/storage-classes/)och [*beständiga volym anspråk*](https://kubernetes.io/docs/concepts/storage/storage-classes/#persistentvolumeclaims).

I den här konfigurationen spelar Kubernetes rollen som behållar-Orchestrator. 

![Diagram över Azure SQL Edge i ett Kubernetes-kluster](media/deploy-kubernetes/kubernetes-sql-edge.png)

I föregående diagram `azure-sql-edge` är en behållare i en [Pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/). Kubernetes dirigerar resurserna i klustret. En [replik uppsättning](https://kubernetes.io/docs/concepts/workloads/controllers/replicaset/) garanterar att Pod återställs automatiskt efter ett nodfel. Program ansluter till tjänsten. I det här fallet representerar tjänsten en belastningsutjämnare som är värd för en IP-adress som är oförändrad efter felet `azure-sql-edge` .

I följande diagram `azure-sql-edge` har behållaren misslyckats. Som Orchestrator garanterar Kubernetes rätt antal felfria instanser i replik uppsättningen och startar en ny behållare enligt konfigurationen. Orchestrator startar en ny Pod på samma nod och `azure-sql-edge` återansluter till samma permanenta lagrings utrymme. Tjänsten ansluter till den nya återskapade `azure-sql-edge` .

![Azure SQL Edge i ett Kubernetes-kluster när Pod har misslyckats](media/deploy-kubernetes/kubernetes-sql-edge-after-pod-fail.png)

I följande diagram misslyckades noden som är värd för `azure-sql-edge` behållaren. Orchestrator startar den nya Pod på en annan nod och `azure-sql-edge` återansluter till samma permanenta lagrings utrymme. Tjänsten ansluter till den nya återskapade `azure-sql-edge` .

![Azure SQL Edge i ett Kubernetes-kluster efter att noden har misslyckats](media/deploy-kubernetes/kubernetes-sql-edge-after-node-fail.png).

Information om hur du skapar en behållare i Kubernetes finns i [distribuera en Azure SQL Edge-behållare i Kubernetes](deploy-Kubernetes.md)

## <a name="next-steps"></a>Nästa steg

Information om hur du distribuerar Azure SQL Edge-behållare i Azure Kubernetes service (AKS) finns i följande artiklar:
- [Distribuera en Azure SQL Edge-behållare i Kubernetes](deploy-Kubernetes.md)
- [Machine Learning och artificiell intelligens med ONNX i SQL Edge](onnx-overview.md).
- [Skapa en IoT-lösning från slut punkt till slut punkt med SQL Edge med hjälp av IoT Edge](tutorial-deploy-azure-resources.md).
- [Data strömning i Azure SQL Edge](stream-data.md)