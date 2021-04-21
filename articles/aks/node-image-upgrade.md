---
title: Uppgradera AKS Azure Kubernetes Service nodavbildningar (Upgrade Azure Kubernetes Service)
description: Lär dig hur du uppgraderar avbildningarna på AKS-klusternoder och nodpooler.
ms.service: container-service
ms.topic: conceptual
ms.date: 11/25/2020
ms.author: jpalma
ms.openlocfilehash: 4f6ac01c1d4df288c823142abbc93e981048d8db
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767537"
---
# <a name="azure-kubernetes-service-aks-node-image-upgrade"></a>Azure Kubernetes Service (AKS)-nodavbildningsuppgradering

AKS stöder uppgradering av avbildningarna på en nod så att du är uppdaterad med de senaste os- och körningsuppdateringarna. AKS tillhandahåller en ny avbildning per vecka med de senaste uppdateringarna, så det är fördelaktigt att uppgradera nodens avbildningar regelbundet för de senaste funktionerna, inklusive Linux- eller Windows-korrigeringar. Den här artikeln visar hur du uppgraderar avbildningar av AKS-klusternoder och hur du uppdaterar avbildningar av nodpooler utan att uppgradera versionen av Kubernetes.

Mer information om de senaste avbildningarna från AKS finns i [viktig information om AKS.](https://github.com/Azure/AKS/releases)

Information om hur du uppgraderar Kubernetes-versionen för klustret finns i [Uppgradera ett AKS-kluster.][upgrade-cluster]

> [!NOTE]
> AKS-klustret måste använda VM-skalningsuppsättningar för noderna.

## <a name="check-if-your-node-pool-is-on-the-latest-node-image"></a>Kontrollera om nodpoolen finns på den senaste nodavbildningen

Du kan se vilken version av nodavbildningen som är tillgänglig för nodpoolen med följande kommando: 

```azurecli
az aks nodepool get-upgrades \
    --nodepool-name mynodepool \
    --cluster-name myAKSCluster \
    --resource-group myResourceGroup
```

I utdata kan du se `latestNodeImageVersion` like i exemplet nedan:

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

Så för `nodepool1` den senaste tillgängliga nodavbildningen är `AKSUbuntu-1604-2020.10.28` . Du kan nu jämföra den med den aktuella nodavbildningsversion som används av nodpoolen genom att köra:

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --query nodeImageVersion
```

Ett exempel på utdata är:

```output
"AKSUbuntu-1604-2020.10.08"
```

I det här exemplet kan du därför uppgradera från den aktuella `AKSUbuntu-1604-2020.10.08` avbildningsversionen till den senaste versionen `AKSUbuntu-1604-2020.10.28` . 

## <a name="upgrade-all-nodes-in-all-node-pools"></a>Uppgradera alla noder i alla nodpooler

Uppgradering av nodavbildningen görs med `az aks upgrade` . Om du vill uppgradera nodavbildningen använder du följande kommando:

```azurecli
az aks upgrade \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-image-only
```

Under uppgraderingen kontrollerar du statusen för nodavbildningarna med följande kommando för att hämta etiketterna och `kubectl` filtrera bort den aktuella nodavbildningsinformationen:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

När uppgraderingen är klar använder du för att `az aks show` hämta information om den uppdaterade nodpoolen. Den aktuella nodbilden visas i `nodeImageVersion` egenskapen .

```azurecli
az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster
```

## <a name="upgrade-a-specific-node-pool"></a>Uppgradera en specifik nodpool

Att uppgradera avbildningen i en nodpool påminner om att uppgradera avbildningen i ett kluster.

Om du vill uppdatera OS-avbildningen av nodpoolen utan att uppgradera Kubernetes-klustret `--node-image-only` använder du alternativet i följande exempel:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-image-only
```

Under uppgraderingen kontrollerar du statusen för nodavbildningarna med följande kommando för att hämta etiketterna och `kubectl` filtrera bort den aktuella nodavbildningsinformationen:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

När uppgraderingen är klar använder du för att `az aks nodepool show` hämta information om den uppdaterade nodpoolen. Den aktuella nodbilden visas i `nodeImageVersion` egenskapen .

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="upgrade-node-images-with-node-surge"></a>Uppgradera nodavbildningar med en ökning av noden

Om du vill påskynda uppgraderingen av nodavbildningen kan du uppgradera dina nodavbildningar med hjälp av ett anpassningsbart värde för nodtoppar. Som standard använder AKS ytterligare en nod för att konfigurera uppgraderingar.

Om du vill öka uppgraderingarna använder du värdet för att konfigurera antalet noder som ska användas för uppgraderingar så att de `--max-surge` slutförs snabbare. Mer information om avvägningarna för olika inställningar finns i Anpassa uppgradering `--max-surge` [av nodtoppar.][max-surge]

Följande kommando anger det högsta högsta värdet för att utföra en nodavbildningsuppgradering:

```azurecli
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --max-surge 33% \
    --node-image-only \
    --no-wait
```

Under uppgraderingen kontrollerar du statusen för nodavbildningarna med följande kommando för att hämta etiketterna och `kubectl` filtrera bort den aktuella nodavbildningsinformationen:

```azurecli
kubectl get nodes -o jsonpath='{range .items[*]}{.metadata.name}{"\t"}{.metadata.labels.kubernetes\.azure\.com\/node-image-version}{"\n"}{end}'
```

Använd `az aks nodepool show` för att hämta information om den uppdaterade nodpoolen. Den aktuella nodbilden visas i `nodeImageVersion` egenskapen .

```azurecli
az aks nodepool show \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool
```

## <a name="next-steps"></a>Nästa steg

- Se [viktig information om AKS för](https://github.com/Azure/AKS/releases) information om de senaste nodavbildningarna.
- Lär dig hur du uppgraderar Kubernetes-versionen med [Uppgradera ett AKS-kluster.][upgrade-cluster]
- [Tillämpa automatiskt uppgraderingar av kluster- och nodpooler med GitHub Actions][github-schedule]
- Läs mer om flera nodpooler och hur du uppgraderar nodpooler [med Skapa och hantera flera nodpooler.][use-multiple-node-pools]

<!-- LINKS - internal -->
[upgrade-cluster]: upgrade-cluster.md
[github-schedule]: node-upgrade-github-actions.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[max-surge]: upgrade-cluster.md#customize-node-surge-upgrade
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
