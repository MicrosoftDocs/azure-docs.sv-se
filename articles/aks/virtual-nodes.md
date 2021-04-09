---
title: Använda virtuella noder
titleSuffix: Azure Kubernetes Service
description: Översikt över hur du använder virtuell nod med Azure Kubernetes Services (AKS)
services: container-service
ms.topic: conceptual
ms.date: 02/17/2021
ms.custom: references_regions
ms.openlocfilehash: 3bba1155ec57db67968aec95d1d3386fc6cda006
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "100634455"
---
# <a name="create-and-configure-an-azure-kubernetes-services-aks-cluster-to-use-virtual-nodes"></a>Skapa och konfigurera ett Azure Kubernetes Services-kluster (AKS) för att använda virtuella noder

Om du snabbt vill skala program arbets belastningar i ett AKS-kluster kan du använda virtuella noder. Med virtuella noder har du snabb etablering av poddar och betalar bara per sekund för körnings tiden. Du behöver inte vänta på att Kubernetes-klustret ska distribuera virtuella dator beräknings noder för att köra ytterligare poddar. Virtuella noder stöds bara med Linux-poddar och noder.

Tillägg för virtuella noder för AKS baseras på det [virtuella Kubelet][virtual-kubelet-repo]för Project med öppen källkod.

Den här artikeln ger en översikt över regions tillgänglighet och nätverks krav för att använda virtuella noder, samt de kända begränsningarna.

## <a name="regional-availability"></a>Regional tillgänglighet

Alla regioner där ACI stöder VNET SKU: er stöds för distributioner av virtuella noder.

För tillgängliga CPU-och minnes-SKU: er i varje region kontrollerar du [Azure Container instances resurs tillgänglighet för Azure Container instances i Azure-regioner – Linux container Groups](../container-instances/container-instances-region-availability.md#linux-container-groups)

## <a name="network-requirements"></a>Nätverkskrav

Virtuella noder möjliggör nätverkskommunikation mellan poddar som körs i Azure Container Instances (ACI) och AKS-klustret. För att tillhandahålla den här kommunikationen skapas ett virtuellt nätverks under nät och delegerade behörigheter tilldelas. Virtuella noder fungerar bara med AKS-kluster som skapats med *Advanced* Networking (Azure cni). Som standard skapas AKS-kluster med *Basic* Networking (Kubernetes).

Poddar som körs i Azure Container Instances (ACI) behöver åtkomst till AKS API-serverns slut punkt för att kunna konfigurera nätverk.

## <a name="known-limitations"></a>Kända begränsningar

Funktioner för virtuella noder är kraftigt beroende av ACI funktions uppsättning. Förutom [kvoter och gränser för Azure Container instances](../container-instances/container-instances-quotas.md), stöds inte följande scenarier ännu med virtuella noder:

* Använda tjänstens huvud namn för att hämta ACR-avbildningar. [Lösning](https://github.com/virtual-kubelet/azure-aci/blob/master/README.md#private-registry) är att använda [Kubernetes hemligheter](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-line)
* [Virtual Network begränsningar](../container-instances/container-instances-vnet.md) , inklusive VNet-peering, Kubernetes nätverks principer och utgående trafik till Internet med nätverks säkerhets grupper.
* Init-behållare
* [Värd Ali Aset](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)
* [Argument](../container-instances/container-instances-exec.md#restrictions) för exec i Aci
* [DaemonSets](concepts-clusters-workloads.md#statefulsets-and-daemonsets) kommer inte att distribuera poddar till de virtuella noderna
* Virtuella noder stöder schemaläggning av Linux-poddar. Du kan manuellt installera den [virtuella KUBELET ACI](https://github.com/virtual-kubelet/azure-aci) -providern med öppen källkod för att schemalägga Windows Server-behållare till ACI.
* Virtuella noder kräver AKS-kluster med Azure CNI-nätverk.
* Använda API-serverns auktoriserade IP-intervall för AKS.
* Volym montering Azure Files dela stöd för [allmän användning v1](../storage/common/storage-account-overview.md#types-of-storage-accounts). Följ anvisningarna för att montera [en volym med Azure Files resurs](azure-files-volume.md)
* Användning av IPv6 stöds inte.

## <a name="next-steps"></a>Nästa steg

Konfigurera virtuella noder för dina kluster:

- [Skapa virtuella noder med Azure CLI](virtual-nodes-cli.md)
- [Skapa virtuella noder med portalen i Azure Kubernetes Services (AKS)](virtual-nodes-portal.md)

Virtuella noder är ofta en komponent i en skalnings lösning i AKS. Mer information om skalnings lösningar finns i följande artiklar:

- [Använd Kubernetes horisontell Pod autoskalning][aks-hpa]
- [Använda Kubernetes-kluster autoskalning][aks-cluster-autoscaler]
- [Kolla in autoskalning-exemplet för virtuella noder][virtual-node-autoscale]
- [Läs mer om det virtuella Kubelet-biblioteket med öppen källkod][virtual-kubelet-repo]

<!-- LINKS - external -->
[aks-hpa]: tutorial-kubernetes-scale.md
[aks-cluster-autoscaler]: ./cluster-autoscaler.md
[virtual-node-autoscale]: https://github.com/Azure-Samples/virtual-node-autoscale
[virtual-kubelet-repo]: https://github.com/virtual-kubelet/virtual-kubelet
