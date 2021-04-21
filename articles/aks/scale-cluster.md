---
title: Skala ut ett AKS-kluster (Azure Kubernetes Service)
description: Lär dig hur du skalar antalet noder i ett Azure Kubernetes Service(AKS)-kluster.
services: container-service
ms.topic: article
ms.date: 09/16/2020
ms.openlocfilehash: 1468f9a0a23935022ed14488dfb65d789828d310
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782895"
---
# <a name="scale-the-node-count-in-an-azure-kubernetes-service-aks-cluster"></a>Skala ut nodantalet i ett Azure Kubernetes Service-kluster

Om programmets resursbehov ändras kan du manuellt skala ett AKS-kluster för att köra ett annat antal noder. När du skalar ned blir noderna noggrant [avspärrade och tömda för][kubernetes-drain] att minimera störningar i program som körs. När du skalar upp väntar AKS tills noderna har markerats av `Ready` Kubernetes-klustret innan poddar schemaläggs på dem.

## <a name="scale-the-cluster-nodes"></a>Skala klusternoderna

Börja med att hämta *namnet* på nodpoolen med [kommandot az aks show.][az-aks-show] I följande exempel hämtar nodpoolnamnet för klustret med namnet *myAKSCluster* i *resursgruppen myResourceGroup:*

```azurecli-interactive
az aks show --resource-group myResourceGroup --name myAKSCluster --query agentPoolProfiles
```

Följande exempelutdata visar att *namnet är* *nodepool1*:

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

Använd kommandot [az aks scale][az-aks-scale] för att skala klusternoderna. I följande exempel skalas ett kluster med namnet *myAKSCluster till* en enda nod. Ange en egen `--nodepool-name` från föregående kommando, till exempel *nodepool1*:

```azurecli-interactive
az aks scale --resource-group myResourceGroup --name myAKSCluster --node-count 1 --nodepool-name <your node pool name>
```

Följande exempelutdata visar att klustret har skalats till en nod, som du ser i *avsnittet agentPoolProfiles:*

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


## <a name="scale-user-node-pools-to-0"></a>Skala `User` nodpooler till 0

Till `System` skillnad från nodpooler som alltid kräver noder som körs kan `User` du skala till 0 med nodpooler. Mer information om skillnaderna mellan system- och användarnodpooler finns i [System- och användarnodpooler.](use-system-pools.md)

Om du vill skala en användarpool till 0 kan du använda skalningsuppsättningen [az aks nodepool][az-aks-nodepool-scale] som alternativ till kommandot ovan och `az aks scale` ange 0 som antal noder.


```azurecli-interactive
az aks nodepool scale --name <your node pool name> --cluster-name myAKSCluster --resource-group myResourceGroup  --node-count 0 
```

Du kan också autoskala nodpooler till 0 noder genom att ange `User` `--min-count` parametern [för Autoskalning av kluster](cluster-autoscaler.md) till 0.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du manuellt skalat ett AKS-kluster för att öka eller minska antalet noder. Du kan också använda [autoskalning av kluster för][cluster-autoscaler] att automatiskt skala klustret.

<!-- LINKS - external -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/

<!-- LINKS - internal -->
[aks-tutorial]: ./tutorial-kubernetes-prepare-app.md
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-aks-scale]: /cli/azure/aks#az_aks_scale
[cluster-autoscaler]: cluster-autoscaler.md
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool#az_aks_nodepool_scale