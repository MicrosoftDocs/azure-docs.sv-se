---
title: Använda närhetsplaceringsgrupper för att minska svarstiden för Azure Kubernetes Service-kluster (AKS)
description: Lär dig hur du använder närhetsplaceringsgrupper för att minska svarstiden för dina AKS-klusterarbetsbelastningar.
services: container-service
manager: gwallace
ms.topic: article
ms.date: 10/19/2020
ms.openlocfilehash: fbcff37185b2cba71c405e917653d52397479007
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779605"
---
# <a name="reduce-latency-with-proximity-placement-groups"></a>Minska svarstiden med närhetsplaceringsgrupper

> [!Note]
> När du använder närhetsplaceringsgrupper i AKS gäller samplacering endast för agentnoderna. Nod-till-nod och motsvarande värd-till-podd-svarstid har förbättrats. Samlokaliseringen påverkar inte placeringen av ett klusters kontrollplan.

När du distribuerar ditt program i Azure skapar spridning av VM-instanser mellan regioner eller tillgänglighetszoner nätverksfördröjning, vilket kan påverka programmets övergripande prestanda. En närhetsplaceringsgrupp är en logisk gruppering som används för att se till att Azure-beräkningsresurser finns fysiskt nära varandra. Vissa program som spel, tekniska simuleringar och HANDEL (High-Frequency Trading) kräver kort svarstid och uppgifter som slutförs snabbt. För HPC-scenarier (databehandling med höga prestanda) [](../virtual-machines/co-location.md#proximity-placement-groups) som dessa bör du överväga att använda närhetsplaceringsgrupper (PPG) för klustrets nodpooler.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln kräver att du kör Azure CLI version 2.14 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

### <a name="limitations"></a>Begränsningar

* En närhetsplaceringsgrupp kan mappa till högst en tillgänglighetszon.
* En nodpool måste använda Virtual Machine Scale Sets för att associera en närhetsplaceringsgrupp.
* En nodpool kan endast associera en närhetsplaceringsgrupp vid tidpunkten då nodpoolen skapas.

## <a name="node-pools-and-proximity-placement-groups"></a>Nodpooler och närhetsplaceringsgrupper

Den första resursen som du distribuerar med en närhetsplaceringsgrupp kopplas till ett visst datacenter. Ytterligare resurser som distribueras med samma närhetsplaceringsgrupp samplaceras i samma datacenter. När alla resurser som använder närhetsplaceringsgruppen har stoppats (frisatts) eller tagits bort, kopplas de inte längre.

* Många nodpooler kan associeras med en enda närhetsplaceringsgrupp.
* En nodpool kan bara associeras med en enda närhetsplaceringsgrupp.

### <a name="configure-proximity-placement-groups-with-availability-zones"></a>Konfigurera närhetsplaceringsgrupper med tillgänglighetszoner

> [!NOTE]
> Närhetsplaceringsgrupper kräver att en nodpool använder högst en tillgänglighetszon, men baslinjeavtalet för [virtuella Azure-datorer på 99,9 %](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_9/) gäller fortfarande för virtuella datorer i en enda zon.

Närhetsplaceringsgrupper är ett begrepp för nodpooler som är associerade med varje enskild nodpool. Användning av en PPG-resurs påverkar inte tillgängligheten för AKS-kontrollplanet. Detta kan påverka hur ett kluster ska utformas med zoner. För att säkerställa att ett kluster sprids över flera zoner rekommenderas följande design.

* Etablera ett kluster med den första systempoolen med 3 zoner och ingen närhetsplaceringsgrupp är associerad. Detta säkerställer att systempoddarna hamnar i en dedikerad nodpool som sprids över flera zoner.
* Lägg till ytterligare användarnodpooler med en unik zon och närhetsplaceringsgrupp som är associerad med varje pool. Ett exempel är nodepool1 i zon 1 och PPG1, nodepool2 i zon 2 och PPG2, nodepool3 i zon 3 med PPG3. Detta säkerställer på klusternivå, noder sprids över flera zoner och varje enskild nodpool samplaceras i den avsedda zonen med en dedikerad PPG-resurs.

## <a name="create-a-new-aks-cluster-with-a-proximity-placement-group"></a>Skapa ett nytt AKS-kluster med en närhetsplaceringsgrupp

I följande exempel används kommandot [az group create][az-group-create] för att skapa en resursgrupp med namnet *myResourceGroup* i *regionen centralus.* Ett AKS-kluster *med namnet myAKSCluster* skapas sedan med kommandot [az aks create.][az-aks-create]

Accelererat nätverk förbättrar nätverkets prestanda avsevärt för virtuella datorer. Vi rekommenderar att du använder närhetsplaceringsgrupper tillsammans med accelererat nätverk. Som standard använder AKS accelererat nätverk på instanser av virtuella datorer som [stöds,](../virtual-network/create-vm-accelerated-networking-cli.md?toc=/azure/virtual-machines/linux/toc.json#limitations-and-constraints)vilket omfattar de flesta virtuella Azure-datorer med två eller flera virtuella processorer.

Skapa ett nytt AKS-kluster med en närhetsplaceringsgrupp som är associerad med den första systemnodpoolen:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```
Kör följande kommando och lagra ID:t som returneras:

```azurecli-interactive
# Create proximity placement group
az ppg create -n myPPG -g myResourceGroup -l centralus -t standard
```

Kommandot genererar utdata, som innehåller det *ID-värde* som du behöver för kommande CLI-kommandon:

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

Använd resurs-ID:t för närhetsplaceringsgruppen *för värdet myPPGResourceID* i kommandot nedan:

```azurecli-interactive
# Create an AKS cluster that uses a proximity placement group for the initial system node pool only. The PPG has no effect on the cluster control plane.
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --ppg myPPGResourceID
```

## <a name="add-a-proximity-placement-group-to-an-existing-cluster"></a>Lägga till en närhetsplaceringsgrupp i ett befintligt kluster

Du kan lägga till en närhetsplaceringsgrupp i ett befintligt kluster genom att skapa en ny nodpool. Du kan sedan migrera befintliga arbetsbelastningar till den nya nodpoolen och sedan ta bort den ursprungliga nodpoolen.

Använd samma närhetsplaceringsgrupp som du skapade tidigare, vilket säkerställer att agentnoderna i båda nodpoolerna i AKS-klustret finns fysiskt i samma datacenter.

Använd resurs-ID:t från närhetsplaceringsgruppen som du skapade tidigare och lägg till en ny nodpool med [`az aks nodepool add`][az-aks-nodepool-add] kommandot :

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

Om du vill ta bort klustret använder du [`az group delete`][az-group-delete] kommandot för att ta bort AKS-resursgruppen:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

## <a name="next-steps"></a>Nästa steg

* Läs mer om [närhetsplaceringsgrupper.][proximity-placement-groups]

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
[aks-tutorial-prepare-app]: ./tutorial-kubernetes-prepare-app.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-get-upgrades]: /cli/azure/aks#az_aks_get_upgrades
[az-aks-upgrade]: /cli/azure/aks#az_aks_upgrade
[az-aks-show]: /cli/azure/aks#az_aks_show
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[proximity-placement-groups]: ../virtual-machines/co-location.md#proximity-placement-groups
[az-aks-create]: /cli/azure/aks#az_aks_create
[system-pool]: ./use-system-pools.md
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az_aks_nodepool_add
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-group-create]: /cli/azure/group#az_group_create
[az-group-delete]: /cli/azure/group#az_group_delete