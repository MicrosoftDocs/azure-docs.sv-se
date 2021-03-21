---
title: Använd närhets placerings grupper för att minska svars tiden för Azure Kubernetes service-kluster (AKS)
description: Lär dig hur du använder närhets placerings grupper för att minska svars tiden för AKS-klustrets arbets belastningar.
services: container-service
manager: gwallace
ms.topic: article
ms.date: 10/19/2020
ms.openlocfilehash: c0c1bf83bf5a816debe61cab0ceab856bfbd062f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102181184"
---
# <a name="reduce-latency-with-proximity-placement-groups"></a>Minska latens med närhets placerings grupper

> [!Note]
> När du använder närhets placerings grupper på AKS gäller samplaceringen endast för agent-noderna. Noden till noden och motsvarande värdbaserade Pod till Pod-fördröjningen har förbättrats. Samplaceringen påverkar inte placeringen av ett klusters kontroll plan.

När du distribuerar ditt program i Azure skapar spridning av virtuella datorer i regioner och tillgänglighets zoner nätverks svars tid, vilket kan påverka programmets övergripande prestanda. En närhets placerings grupp är en logisk gruppering som används för att se till att Azure Compute-resurser är fysiskt placerade nära varandra. Vissa program som spel, teknik simuleringar och handel med hög frekvens (HFT) kräver låg latens och uppgifter som slutförs snabbt. För högpresterande data behandlings scenarier (HPC), till exempel dessa, bör du överväga att använda [närhets placerings grupper](../virtual-machines/co-location.md#proximity-placement-groups) (PPG) för klustrets noder.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln kräver att du kör Azure CLI version 2,14 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

### <a name="limitations"></a>Begränsningar

* En närhets placerings grupp kan mappas till högst en tillgänglighets zon.
* En Node-pool måste använda Virtual Machine Scale Sets för att associera en närhets placerings grupp.
* En Node-pool kan bara associera en närhets placerings grupp i nodens skapande tid.

## <a name="node-pools-and-proximity-placement-groups"></a>Placerings grupper för resurspooler och närhet

Den första resurs som du distribuerar med en närhets placerings grupp kopplas till ett Data Center. Ytterligare resurser som distribueras med samma närhets placerings grupp finns i samma data Center. När alla resurser som använder närhets placerings gruppen har stoppats (frigjorts) eller tagits bort är den inte längre ansluten.

* Många noder i pooler kan associeras med en enda närhets placerings grupp.
* En Node-pool kan bara associeras med en enda närhets placerings grupp.

### <a name="configure-proximity-placement-groups-with-availability-zones"></a>Konfigurera närhets placerings grupper med tillgänglighets zoner

> [!NOTE]
> Även om placerings grupper för närhet kräver att en Node-pool används i de flesta en tillgänglighets zon, gäller [bas linjen för Azure VM-SLA på 99,9%](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) fortfarande för virtuella datorer i en enda zon.

Placerings grupper för närhet är ett koncept för Node-pool och associeras med varje enskild Node-pool. Användning av en PPG-resurs påverkar inte tillgängligheten för AKS kontroll plan. Detta kan påverka hur ett kluster ska utformas med zoner. Följande design rekommenderas för att säkerställa att ett kluster sprids över flera zoner.

* Etablera ett kluster med den första mediepoolen med tre zoner och ingen närhets placerings grupp kopplad. Detta säkerställer att systemet poddar land i en dedikerad Node-pool som kommer att spridas över flera zoner.
* Lägg till ytterligare resurspooler för användare med en unik zon och närhets placerings grupp kopplad till varje pool. Ett exempel är nodepool1 i zon 1 och PPG1, nodepool2 i zon 2 och PPG2, nodepool3 i zon 3 med PPG3. På så sätt kan noderna spridas över flera zoner och varje enskild Node-pool befinner sig i den angivna zonen med en dedikerad PPG-resurs.

## <a name="create-a-new-aks-cluster-with-a-proximity-placement-group"></a>Skapa ett nytt AKS-kluster med en närhets placerings grupp

I följande exempel används kommandot [AZ Group Create][az-group-create] för att skapa en resurs grupp med namnet *myResourceGroup* i den *centrala* regionen. Ett AKS-kluster med namnet *myAKSCluster* skapas sedan med kommandot [AZ AKS Create][az-aks-create] .

Accelererat nätverk förbättrar nätverks prestandan för virtuella datorer avsevärt. Vi rekommenderar att du använder närhets placerings grupper tillsammans med accelererat nätverk. Som standard använder AKS accelererat nätverk på [virtuella dator instanser som stöds](../virtual-network/create-vm-accelerated-networking-cli.md?toc=/azure/virtual-machines/linux/toc.json#limitations-and-constraints), som inkluderar de flesta virtuella Azure-datorer med två eller flera virtuella processorer.

Skapa ett nytt AKS-kluster med en närhets placerings grupp kopplad till den första noden i systemet:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```
Kör följande kommando och lagra det ID som returneras:

```azurecli-interactive
# Create proximity placement group
az ppg create -n myPPG -g myResourceGroup -l centralus -t standard
```

Kommandot ger utdata som innehåller det *ID-* värde du behöver för kommande CLI-kommandon:

```output
{
  "availabilitySets": null,
  "colocationStatus": null,
  "id": "/subscriptions/yourSubscriptionID/resourceGroups/myResourceGroup/providers/Microsoft.Compute/proximityPlacementGroups/myPPG",
  "location": "centralus",
  "name": "myPPG",
  "proximityPlacementGroupType": "Standard",
  "resourceGroup": "myResourceGroup",
  "tags": {},
  "type": "Microsoft.Compute/proximityPlacementGroups",
  "virtualMachineScaleSets": null,
  "virtualMachines": null
}
```

Använd resurs-ID för närhets placerings grupp för värdet *myPPGResourceID* i nedanstående kommando:

```azurecli-interactive
# Create an AKS cluster that uses a proximity placement group for the initial system node pool only. The PPG has no effect on the cluster control plane.
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --ppg myPPGResourceID
```

## <a name="add-a-proximity-placement-group-to-an-existing-cluster"></a>Lägg till en närhets placerings grupp i ett befintligt kluster

Du kan lägga till en närhets placerings grupp i ett befintligt kluster genom att skapa en ny Node-pool. Du kan sedan välja att migrera befintliga arbets belastningar till den nya noden och sedan ta bort den ursprungliga Node-poolen.

Använd samma närhets placerings grupp som du skapade tidigare och detta säkerställer att agent-noderna i båda noderna i AKS-klustret är fysiskt placerade i samma data Center.

Använd resurs-ID: t från den närhets placering som du skapade tidigare och Lägg till en ny Node-pool med [`az aks nodepool add`][az-aks-nodepool-add] kommandot:

```azurecli-interactive
# Add a new node pool that uses a proximity placement group, use a --node-count = 1 for testing
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 1 \
    --ppg myPPGResourceID
```

## <a name="clean-up"></a>Rensa

Ta bort klustret genom att använda [`az group delete`][az-group-delete] kommandot för att ta bort resurs gruppen AKS:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Nästa steg

* Lär dig mer om [närhets placerings grupper][proximity-placement-groups].

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[az-aks-show]: /cli/azure/aks#az-aks-show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[proximity-placement-groups]: ../virtual-machines/co-location.md#proximity-placement-groups
[az-aks-create]: /cli/azure/aks#az-aks-create
[system-pool]: ./use-system-pools.md
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az-aks-nodepool-add
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-group-create]: /cli/azure/group#az-group-create
[az-group-delete]: /cli/azure/group#az-group-delete