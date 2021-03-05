---
title: Skala ut ett AKS-kluster (Azure Kubernetes Service)
description: Lär dig hur du skalar antalet noder i ett Azure Kubernetes service-kluster (AKS).
services: container-service
ms.topic: article
ms.date: 09/16/2020
ms.openlocfilehash: fdb61bf090351894329c24eb1a3c73d627e622e8
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102173774"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>Skala ut nodantalet i ett Azure Kubernetes Service-kluster

Om resurs behoven för dina program ändras kan du manuellt skala ett AKS-kluster för att köra ett annat antal noder. När du skalar ned är noderna noggrant [avspärradea och töms][kubernetes-drain] för att minimera störningar i program som körs. När du skalar upp väntar AKS tills noderna markeras `Ready` av Kubernetes-klustret innan poddar schemaläggs på dem.

## <a name="scale-the-cluster-nodes"></a>Skala klusternoderna

Börja med att hämta *namnet* på Node-poolen med kommandot [AZ AKS show][az-aks-show] . I följande exempel hämtas Node-namnet för klustret med namnet *myAKSCluster* i resurs gruppen *myResourceGroup* :

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

Följande exempel på utdata visar att *namnet* är *nodepool1*:

```output
[
  {
    "count": 1,
    "maxPods": 110,
    "name": "nodepool1",
    "osDiskSizeGb": 30,
    "osType": "Linux",
    "storageProfile": "ManagedDisks",
    "vmSize": "Standard_DS2_v2"
  }
]
```

Använd kommandot [AZ AKS Scale][az-aks-scale] för att skala klusternoderna. I följande exempel skalas ett kluster med namnet *myAKSCluster* till en enda nod. Ange din egen `--nodepool-name` från föregående kommando, till exempel *nodepool1*:

```azurecli-interactive
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 1 --nodepool-name <your node pool name>
```

Följande exempel på utdata visar att klustret har skalats till en nod, som du ser i avsnittet *agentPoolProfiles* :

```json
{
  "aadProfile": null,
  "addonProfiles": null,
  "agentPoolProfiles": [
    {
      "count": 1,
      "maxPods": 110,
      "name": "nodepool1",
      "osDiskSizeGb": 30,
      "osType": "Linux",
      "storageProfile": "ManagedDisks",
      "vmSize": "Standard_DS2_v2",
      "vnetSubnetId": null
    }
  ],
  [...]
}
```


## <a name="scale-user-node-pools-to-0"></a>Skala `User` nodkonfigurationer till 0

Till skillnad från `System` Node-pooler som alltid kräver noder, kan `User` du skala till 0 med noder. Om du vill veta mer om skillnaderna mellan poolerna för system-och användar-noder, se [pooler för system-och användar-noder](use-system-pools.md).

Om du vill skala en adresspool till 0 kan du använda [AZ AKS nodepool Scale][az-aks-nodepool-scale] i alternativ till `az aks scale` kommandot ovan och ange 0 som antal noder.


```azurecli-interactive
az aks nodepool scale --name <your node pool name> --cluster-name myAKSCluster --resource-group myResourceGroup  --node-count 0 
```

Du kan också Autoskala `User` nodkonfigurationer till 0 noder genom att ställa in `--min-count` parametern för [klustrets autoskalning](cluster-autoscaler.md) till 0.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du manuellt skalat ett AKS-kluster för att öka eller minska antalet noder. Du kan också använda automatisk skalning av [klustret][cluster-autoscaler] för att automatiskt skala klustret.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[cluster-autoscaler]: cluster-autoscaler.md
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool#az-aks-nodepool-scale