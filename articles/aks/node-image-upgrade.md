---
title: Uppgradera Node-avbildningar för Azure Kubernetes service (AKS)
description: Lär dig hur du uppgraderar avbildningarna på AKS-klusternoder och Node-pooler.
ms.service: container-service
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: jpalma
ms.openlocfilehash: 83d7d48922806334e2b49494fe0ef1d15e1a7a6a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "96531487"
---
# <a name="azure-kubernetes-service-aks-node-image-upgrade"></a>Uppgradering av noden Azure Kubernetes service (AKS)

AKS stöder uppgradering av avbildningar på en nod så att du är uppdaterad med de senaste uppdateringarna för operativ systemet och körning. AKS tillhandahåller en ny avbildning per vecka med de senaste uppdateringarna, så det är bra att uppgradera nodens avbildningar regelbundet för de senaste funktionerna, inklusive Linux-eller Windows-korrigeringsfiler. Den här artikeln visar hur du uppgraderar AKS för klusternoder och hur du uppdaterar avbildningar av noder utan att uppgradera versionen av Kubernetes.

Mer information om de senaste avbildningarna som tillhandahålls av AKS finns i [viktig](https://github.com/Azure/AKS/releases)information för AKS.

Information om hur du uppgraderar Kubernetes-versionen för klustret finns i [uppgradera ett AKS-kluster][upgrade-cluster].

> [!NOTE]
> AKS-klustret måste använda skalnings uppsättningar för virtuella datorer för noderna.

## <a name="check-if-your-node-pool-is-on-the-latest-node-image"></a>Kontrol lera att Node-poolen finns på den senaste noden

Du kan se vad är den senaste versionen av Node-avbildningen som är tillgänglig för Node-poolen med följande kommando: 

```azurecli
az aks nodepool get-upgrades \
    --nodepool-name mynodepool \
    --cluster-name myAKSCluster \
    --resource-group myResourceGroup
```

I utdata kan du se `latestNodeImageVersion` precis som i exemplet nedan:

```output
{
  "id": "/subscriptions/XXXX-XXX-XXX-XXX-XXXXX/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/nodepool1/upgradeProfiles/default",
  "kubernetesVersion": "1.17.11",
  "latestNodeImageVersion": "AKSUbuntu-1604-2020.10.28",
  "name": "default",
  "osType": "Linux",
  "resourceGroup": "myResourceGroup",
  "type": "Microsoft.ContainerService/managedClusters/agentPools/upgradeProfiles",
  "upgrades": null
}
```

Så för `nodepool1` den senaste tillgängliga nod-avbildningen `AKSUbuntu-1604-2020.10.28` . Nu kan du jämföra det med den aktuella nodens avbildnings version som används av Node-poolen genom att köra:

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --query nodeImageVersion
```

Ett exempel på utdata skulle vara:

```output
"AKSUbuntu-1604-2020.10.08"
```

I det här exemplet kan du uppgradera från den aktuella `AKSUbuntu-1604-2020.10.08` avbildnings versionen till den senaste versionen `AKSUbuntu-1604-2020.10.28` . 

## <a name="upgrade-all-nodes-in-all-node-pools"></a>Uppgradera alla noder i alla noder i pooler

Uppgradering av Node-avbildningen görs med `az aks upgrade` . Använd följande kommando för att uppgradera Node-avbildningen:

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-image-only
```

Under uppgraderingen kontrollerar du status för Node-avbildningarna med följande `kubectl` kommando för att hämta etiketterna och filtrera ut den aktuella nodens avbildnings information:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

När uppgraderingen är klar använder `az aks show` du för att hämta den uppdaterade nodens resurspool. Den aktuella nodens avbildning visas i `nodeImageVersion` egenskapen.

```azurecli
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster
```

## <a name="upgrade-a-specific-node-pool"></a>Uppgradera en angiven Node-pool

Att uppgradera avbildningen på en Node-pool liknar att uppgradera avbildningen på ett kluster.

Om du vill uppdatera operativ system avbildningen i Node-poolen utan att göra en Kubernetes-uppgradering av klustret, använder du `--node-image-only` alternativet i följande exempel:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-image-only
```

Under uppgraderingen kontrollerar du status för Node-avbildningarna med följande `kubectl` kommando för att hämta etiketterna och filtrera ut den aktuella nodens avbildnings information:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

När uppgraderingen är klar använder `az aks nodepool show` du för att hämta den uppdaterade nodens resurspool. Den aktuella nodens avbildning visas i `nodeImageVersion` egenskapen.

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="upgrade-node-images-with-node-surge"></a>Uppgradera Node-avbildningar med överspänning för nod

Om du vill påskynda uppgraderings processen för Node-avbildningen kan du uppgradera dina Node-avbildningar med hjälp av ett anpassningsbart värde för en nod. Som standard använder AKS ytterligare en nod för att konfigurera uppgraderingar.

Om du vill öka hastigheten på uppgraderingar använder du `--max-surge` värdet för att konfigurera antalet noder som ska användas för uppgraderingar så att de blir snabbare. Mer information om kompromisser med olika `--max-surge` inställningar finns i [Anpassa uppgradering av nod överspänning][max-surge].

Följande kommando anger Max värdet för överspänning för att utföra en uppgradering av noden avbildning:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --max-surge 33% \
    --node-image-only \
    --no-wait
```

Under uppgraderingen kontrollerar du status för Node-avbildningarna med följande `kubectl` kommando för att hämta etiketterna och filtrera ut den aktuella nodens avbildnings information:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Använd `az aks nodepool show` för att hämta den uppdaterade informationen om Node-poolen. Den aktuella nodens avbildning visas i `nodeImageVersion` egenskapen.

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="next-steps"></a>Nästa steg

- Mer information om de senaste Node-avbildningarna finns i [AKS-versions anteckningarna](https://github.com/Azure/AKS/releases) .
- Lär dig hur du uppgraderar Kubernetes-versionen med [uppgradera ett AKS-kluster][upgrade-cluster].
- [Tillämpa automatiskt kluster-och nodens pool uppgraderingar med GitHub-åtgärder][github-schedule]
- Lär dig mer om flera noder och hur du uppgraderar noder med [skapa och hantera flera noder][use-multiple-node-pools]i pooler.

<!-- LINKS - internal -->
[upgrade-cluster]: upgrade-cluster.md
[github-schedule]: node-upgrade-github-actions.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
