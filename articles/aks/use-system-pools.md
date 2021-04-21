---
title: Använda systemnodpooler i Azure Kubernetes Service (AKS)
description: Lär dig hur du skapar och hanterar systemnodpooler i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 06/18/2020
ms.author: mlearned
ms.custom: fasttrack-edit, devx-track-azurecli
ms.openlocfilehash: 8b41b43c70f72ab327de2f1d59415cc1f49e5a5b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767411"
---
# <a name="manage-system-node-pools-in-azure-kubernetes-service-aks"></a>Hantera systemnodpooler i Azure Kubernetes Service (AKS)

I Azure Kubernetes Service (AKS) grupperas noder i samma konfiguration tillsammans i *nodpooler*. Nodpooler innehåller de underliggande virtuella datorer som kör dina program. Systemnodpooler och användarnodpooler är två olika nodpoollägen för dina AKS-kluster. Systemnodpooler har det primära syftet att vara värd för kritiska systempoddar som `CoreDNS` och `metrics-server` . Användarnodpooler är det primära syftet med att vara värd för dina programpoddar. Programpoddar kan dock schemaläggas i systemnodpooler om du bara vill ha en pool i ditt AKS-kluster. Varje AKS-kluster måste innehålla minst en systemnodpool med minst en nod.

> [!Important]
> Om du kör en enda systemnodpool för ditt AKS-kluster i en produktionsmiljö rekommenderar vi att du använder minst tre noder för nodpoolen.

## <a name="before-you-begin"></a>Innan du börjar

* Azure CLI version 2.3.1 eller senare måste vara installerat och konfigurerat. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="limitations"></a>Begränsningar

Följande begränsningar gäller när du skapar och hanterar AKS-kluster som stöder systemnodpooler.

* Se [Kvoter, storleksbegränsningar för virtuella datorer och regionstillgänglighet i Azure Kubernetes Service (AKS).][quotas-skus-regions]
* AKS-klustret måste byggas med VM-skalningsuppsättningar  som VM-typ och standard-SKU-lastbalanserare.
* Namnet på en nodpool får bara innehålla gemener alfanumeriska tecken och måste börja med en gemen. För Linux-nodpooler måste längden vara mellan 1 och 12 tecken. För Windows-nodpooler måste längden vara mellan 1 och 6 tecken.
* En API-version av 2020-03-01 eller senare måste användas för att ställa in ett nodpoolläge. Kluster som skapats på API-versioner som är äldre än 2020-03-01 innehåller endast användarnodpooler, men kan migreras till att innehålla systemnodpooler genom att följa stegen i [uppdateringspoolläget](#update-existing-cluster-system-and-user-node-pools).
* Läget för en nodpool är en obligatorisk egenskap och måste anges explicit när du använder ARM-mallar eller direkta API-anrop.

## <a name="system-and-user-node-pools"></a>System- och användarnodpooler

För en systemnodpool tilldelar AKS automatiskt **kubernetes.azure.com/mode: systemet** till sina noder. Detta gör att AKS föredrar schemaläggning av systempoddar i nodpooler som innehåller den här etiketten. Den här etiketten hindrar dig inte från att schemalägga programpoddar i systemnodpooler. Vi rekommenderar dock att du isolerar kritiska systempoddar från dina programpoddar för att förhindra felkonfigurerade eller falska programpoddar från att oavsiktligt ta bort systempoddar. Du kan tillämpa det här beteendet genom att skapa en dedikerad systemnodpool. Använd `CriticalAddonsOnly=true:NoSchedule` taint för att förhindra att programpoddar schemaläggs i systemnodpooler.

Systemnodpooler har följande begränsningar:

* Systempoolerna osType måste vara Linux.
* OsType för användarnodpooler kan vara Linux eller Windows.
* Systempooler måste innehålla minst en nod och användarnodpooler kan innehålla noll eller flera noder.
* Systemnodpooler kräver en VM-SKU på minst 2 virtuella processorer och 4 GB minne. Men burstable-VM (B-serien) rekommenderas inte.
* Minst två noder 4 vCPU:er rekommenderas (t.ex. Standard_DS4_v2), särskilt för stora kluster (flera CoreDNS-poddrepliker, 3–4+ tillägg osv.).
* Systemnodpooler måste ha stöd för minst 30 poddar enligt beskrivningen i [formeln för lägsta och högsta värde för poddar][maximum-pods].
* Nodpooler för punkt-till-plats kräver användarnodpooler.
* Om du lägger till ytterligare en systemnodpool eller ändrar vilken nodpool som är en systemnodpool *flyttas* INTE systempoddar automatiskt. Systempoddar kan fortsätta att köras i samma nodpool även om du ändrar den till en användarnodpool. Om du tar bort eller skalar ned en nodpool som kör systempoddar som tidigare var en systemnodpool, omdistribueras dessa systempoddar med prioriterad schemaläggning till den nya systemnodpoolen.

Du kan göra följande åtgärder med nodpooler:

* Skapa en dedikerad systemnodpool (prioritera schemaläggning av systempoddar till nodpooler för `mode:system` )
* Ändra en systemnodpool till en användarnodpool, förutsatt att du har en annan systemnodpool som ska ta dess plats i AKS-klustret.
* Ändra en användarnodpool till en systemnodpool.
* Ta bort användarnodpooler.
* Du kan ta bort systemnodpooler, förutsatt att du har en annan systemnodpool att ta över i AKS-klustret.
* Ett AKS-kluster kan ha flera systemnodpooler och kräver minst en systemnodpool.
* Om du vill ändra olika oföränderliga inställningar för befintliga nodpooler kan du skapa nya nodpooler som ersätter dem. Ett exempel är att lägga till en ny nodpool med en ny maxPods-inställning och ta bort den gamla nodpoolen.

## <a name="create-a-new-aks-cluster-with-a-system-node-pool"></a>Skapa ett nytt AKS-kluster med en systemnodpool

När du skapar ett nytt AKS-kluster skapar du automatiskt en systemnodpool med en enda nod. Den inledande nodpoolen har som standard ett typlägessystem. När du skapar nya nodpooler med `az aks nodepool add` är dessa nodpooler användarnodpooler om du inte uttryckligen anger lägesparametern.

I följande exempel skapas en resursgrupp med namnet *myResourceGroup* i *regionen eastus.*

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Använd kommandot [az aks create][az-aks-create] för att skapa ett AKS-kluster. I följande exempel skapas ett kluster med namnet *myAKSCluster med* en dedikerad systempool som innehåller en nod. Se till att du använder systemnodpooler med minst tre noder för dina produktionsarbetsbelastningar. Den här åtgärden kan ta flera minuter att slutföra.

```azurecli-interactive
# Create a new AKS cluster with a single system pool
az aks create -g myResourceGroup --name myAKSCluster --node-count 1 --generate-ssh-keys
```

## <a name="add-a-dedicated-system-node-pool-to-an-existing-aks-cluster"></a>Lägga till en dedikerad systemnodpool i ett befintligt AKS-kluster

> [!Important]
> Du kan inte ändra nod-taints via CLI när nodpoolen har skapats.

Du kan lägga till en eller flera systemnodpooler i befintliga AKS-kluster. Vi rekommenderar att du schemalägger programpoddar i användarnodpooler och dedikerar systemnodpooler till endast kritiska systempoddar. Detta förhindrar att otillåtna programpoddar oavsiktligt av misstag avlivar systempoddar. Framtvinga det här `CriticalAddonsOnly=true:NoSchedule` [beteendet med taint för][aks-taints] systemets nodpooler. 

Följande kommando lägger till en dedikerad nodpool av lägestypsystem med ett standardantal på tre noder.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name systempool \
    --node-count 3 \
    --node-taints CriticalAddonsOnly=true:NoSchedule \
    --mode System
```
## <a name="show-details-for-your-node-pool"></a>Visa information om nodpoolen

Du kan kontrollera informationen om nodpoolen med följande kommando.  

```azurecli-interactive
az aks nodepool show -g myResourceGroup --cluster-name myAKSCluster -n systempool
```

Ett läge av typen **System definieras** för systemnodpooler och ett läge av typen **Användare definieras** för användarnodpooler. För en systempool kontrollerar du att taint är inställt på , vilket förhindrar att programpoddar finns schemalagda `CriticalAddonsOnly=true:NoSchedule` för den här nodpoolen.

```output
{
  "agentPoolType": "VirtualMachineScaleSets",
  "availabilityZones": null,
  "count": 1,
  "enableAutoScaling": null,
  "enableNodePublicIp": false,
  "id": "/subscriptions/yourSubscriptionId/resourcegroups/myResourceGroup/providers/Microsoft.ContainerService/managedClusters/myAKSCluster/agentPools/systempool",
  "maxCount": null,
  "maxPods": 110,
  "minCount": null,
  "mode": "System",
  "name": "systempool",
  "nodeImageVersion": "AKSUbuntu-1604-2020.06.30",
  "nodeLabels": {},
  "nodeTaints": [
    "CriticalAddonsOnly=true:NoSchedule"
  ],
  "orchestratorVersion": "1.16.10",
  "osDiskSizeGb": 128,
  "osType": "Linux",
  "provisioningState": "Failed",
  "proximityPlacementGroupId": null,
  "resourceGroup": "myResourceGroup",
  "scaleSetEvictionPolicy": null,
  "scaleSetPriority": null,
  "spotMaxPrice": null,
  "tags": null,
  "type": "Microsoft.ContainerService/managedClusters/agentPools",
  "upgradeSettings": {
    "maxSurge": null
  },
  "vmSize": "Standard_DS2_v2",
  "vnetSubnetId": null
}
```

## <a name="update-existing-cluster-system-and-user-node-pools"></a>Uppdatera befintligt klustersystem och användarnodpooler

> [!NOTE]
> En API-version av 2020-03-01 eller senare måste användas för att ställa in ett systemnodpoolläge. Kluster som skapats på API-versioner som är äldre än 2020-03-01 innehåller därför endast användarnodpooler. Om du vill ta emot systemnodpoolens funktioner och fördelar med äldre kluster uppdaterar du läget för befintliga nodpooler med följande kommandon i den senaste Azure CLI-versionen.

Du kan ändra lägen för både system- och användarnodpooler. Du kan bara ändra en systemnodpool till en användarpool om det redan finns en annan systemnodpool i AKS-klustret.

Det här kommandot ändrar en systemnodpool till en användarnodpool.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode user
```

Det här kommandot ändrar en användarnodpool till en systemnodpool.

```azurecli-interactive
az aks nodepool update -g myResourceGroup --cluster-name myAKSCluster -n mynodepool --mode system
```

## <a name="delete-a-system-node-pool"></a>Ta bort en systemnodpool

> [!Note]
> Om du vill använda systemnodpooler i AKS-kluster före API-version 2020-03-02 lägger du till en ny systemnodpool och tar sedan bort den ursprungliga standardnodpoolen.

Du måste ha minst två systemnodpooler i ditt AKS-kluster innan du kan ta bort en av dem.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster -n mynodepool
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort klustret använder du [kommandot az group delete][az-group-delete] för att ta bort AKS-resursgruppen:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```



## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du skapar och hanterar systemnodpooler i ett AKS-kluster. Mer information om hur du använder flera nodpooler finns i [Använda flera nodpooler.][use-multiple-node-pools]

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-taints]: use-multiple-node-pools.md#setting-nodepool-taints
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az_aks_nodepool_add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool#az_aks_nodepool_list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool#az_aks_nodepool_update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool#az_aks_nodepool_upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool#az_aks_nodepool_scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool#az_aks_nodepool_delete
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete
[az-group-deployment-create]: /cli/azure/group/deployment#az_group_deployment_create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/management/tag-resources.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/sizes.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[maximum-pods]: configure-azure-cni.md#maximum-pods-per-node
