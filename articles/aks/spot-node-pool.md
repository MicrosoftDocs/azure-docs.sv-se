---
title: Lägga till en skalningsuppsättningsnodpool för VM med oanvänd kapacitet till ett Azure Kubernetes Service-kluster (AKS)
description: Lär dig hur du lägger till en adresspool för en plats i ett Azure Kubernetes service-kluster (AKS).
services: container-service
ms.service: container-service
ms.topic: article
ms.date: 10/19/2020
ms.openlocfilehash: 7f838b2a78f1c6993aa247f2944d4f2a9b1e9556
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2021
ms.locfileid: "102181133"
---
# <a name="add-a-spot-node-pool-to-an-azure-kubernetes-service-aks-cluster"></a>Lägga till en skalningsuppsättningsnodpool för VM med oanvänd kapacitet till ett Azure Kubernetes Service-kluster (AKS)

En adresspool för plats är en resurspool som backas upp av en [skalnings uppsättning för en virtuell dator][vmss-spot]. Genom att använda virtuella dator datorer för noder med ditt AKS-kluster kan du dra nytta av outnyttjad kapacitet i Azure till en betydande kostnads besparingar. Mängden tillgänglig outnyttjad kapacitet kan variera beroende på många faktorer, inklusive Node-storlek, region och tid på dagen.

När du distribuerar en adresspool, allokerar Azure dekor noderna om det finns tillgänglig kapacitet. Men det finns inget service avtal för de här plats-noderna. En punkt skalnings uppsättning som backar upp lagringspoolen distribueras i en enskild feldomän och ger inga garantier för hög tillgänglighet. När som helst när Azure behöver kapaciteten tillbaka, tar Azure-infrastrukturen bort dekor noder.

Dekor noder är fantastiska för arbets belastningar som kan hantera avbrott, tidiga uppsägningar eller avvisningar. Till exempel kan arbets belastningar som batch-bearbetning av jobb, utvecklings-och testnings miljöer och stora beräknings arbets belastningar vara bra för att schemaläggas på en Node-pool.

I den här artikeln lägger du till en sekundär Node-adresspool i ett befintligt Azure Kubernetes service-kluster (AKS).

Den här artikeln förutsätter grundläggande kunskaper om Kubernetes och Azure Load Balancer koncept. Mer information finns i [Viktiga koncept för Azure Kubernetes Service (AKS)][kubernetes-concepts].

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="before-you-begin"></a>Innan du börjar

När du skapar ett kluster för att använda en adresspool, måste klustret också använda Virtual Machine Scale Sets för Node-pooler och *standard* -SKU-belastningsutjämnaren. Du måste också lägga till ytterligare en Node-pool när du har skapat klustret för att använda en resurspool för en plats. Att lägga till ytterligare en Node-pool beskrivs i ett senare steg.

Den här artikeln kräver att du kör Azure CLI version 2,14 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

### <a name="limitations"></a>Begränsningar

Följande begränsningar gäller när du skapar och hanterar AKS-kluster med en adresspool för en plats:

* En adresspool för en plats kan inte vara klustrets standardnode-pool. En adresspool för en plats kan bara användas för en sekundär pool.
* Du kan inte uppgradera en adresspool för en resurspool eftersom det inte går att garantera Cordon och tömma noden. Du måste ersätta din befintliga Node-adresspool med en ny för att utföra åtgärder som att uppgradera Kubernetes-versionen. Om du vill ersätta en adresspool för flera noder skapar du en ny Kubernetes med en annan version av, väntar tills dess status är *klar* och tar sedan bort den gamla noden.
* Det går inte att uppgradera kontroll planet och Node-pooler samtidigt. Du måste uppgradera dem separat eller ta bort noden för att kunna uppgradera kontroll planet och återstående noder på samma tid.
* En Node-pool måste använda Virtual Machine Scale Sets.
* Du kan inte ändra ScaleSetPriority eller SpotMaxPrice när du har skapat.
* När du anger SpotMaxPrice måste värdet vara-1 eller ett positivt värde med upp till fem decimaler.
* En kubernetes.azure.com/scalesetpriority:spot har etiketten , *Kubernetes.Azure.com/scalesetpriority=spot:NoSchedule* och system poddar har anti-tillhörighet.
* Du måste lägga till en [motsvarande tolererande][spot-toleration] för att schemalägga arbets belastningar på en resurspool för en plats.

## <a name="add-a-spot-node-pool-to-an-aks-cluster"></a>Lägga till en skalningsuppsättningsnodpool för VM med oanvänd kapacitet i ett AKS-kluster

Du måste lägga till en adresspool för en plats i ett befintligt kluster som har flera aktiverade noder. Mer information om hur du skapar ett AKS-kluster med flera noder finns [här][use-multiple-node-pools].

Skapa en Node-pool med hjälp av [AZ AKS nodepool Add][az-aks-nodepool-add].
```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name spotnodepool \
    --priority Spot \
    --eviction-policy Delete \
    --spot-max-price -1 \
    --enable-cluster-autoscaler \
    --min-count 1 \
    --max-count 3 \
    --no-wait
```

Som standard skapar du en Node-pool med en *prioritet* som är *vanlig* i ditt AKS-kluster när du skapar ett kluster med flera noder i pooler. Kommandot ovan lägger till en extra Node-pool i ett befintligt AKS-kluster med en *prioritet* på *plats*. *Prioriteten* för *dekor* gör Node-poolen till en plats för Node. Parametern Delete *-policy* är inställd på *ta bort* i exemplet ovan, vilket är standardvärdet. När du *anger borttagnings* [principen][eviction-policy] tas noderna i den underliggande skalnings uppsättningen i noden bort när de avlägsnas. Du kan också ange att principen för borttagning ska frigöras. När du ställer in principen för att *frigöra*, anges noder i den underliggande skalnings uppsättningen till stoppad-frigjord-tillstånd vid borttagning. Noder i det stoppade-friallokerade tillstånds antalet mot din beräknings kvot och kan orsaka problem med kluster skalning eller uppgradering. *Prioriteten* och *avtagningen – princip* värden kan bara anges när du skapar en resurspool. Dessa värden kan inte uppdateras senare.

Kommandot aktiverar också [klustrets autoskalning][cluster-autoscaler], vilket rekommenderas att användas med resurspooler för plats-noder. Med hjälp av de arbets belastningar som körs i klustret skalar klustret autoskalning upp och skalar upp antalet noder i Node-poolen. För resurspooler för flera noder skalar klustret autoskalning upp antalet noder efter en avtagning om ytterligare noder fortfarande behövs. Om du ändrar det maximala antalet noder som en Node-pool kan ha måste du också justera `maxCount` värdet som är associerat med klustrets autoskalning. Om du inte använder en kluster autoskalning, vid borttagning, kommer lagringspoolen att minska till noll och kräver en manuell åtgärd för att ta emot ytterligare noder.

> [!Important]
> Schemalägg bara arbets belastningar på platsbaserade noder som kan hantera avbrott, till exempel jobb för batch-bearbetning och testnings miljöer. Vi rekommenderar att du konfigurerar färgare [och tolererar][taints-tolerations] på din plats för att se till att endast arbets belastningar som kan hantera nod-avvisningar schemaläggs på en adresspool. Till exempel kan kommandot ovan vara standard lägga till en *Kubernetes.Azure.com/scalesetpriority=spot:NoSchedule* , så att endast poddar med motsvarande tolerera är schemalagda på den här noden.

## <a name="verify-the-spot-node-pool"></a>Verifiera lagringspoolen för den här noden

Så här kontrollerar du att Node-poolen har lagts till som en adresspool för plats:

```azurecli
az aks nodepool show --resource-group myResourceGroup --cluster-name myAKSCluster --name spotnodepool
```

Bekräfta att *scaleSetPriority* är *dekor*.

Om du vill schemalägga en pod som ska köras på en-nod, lägger du till ett tolererande som motsvarar den färg som används på din datornod. I följande exempel visas en del av en yaml-fil som definierar en tolererande som motsvarar en *Kubernetes.Azure.com/scalesetpriority=spot:NoSchedule* -smak som används i föregående steg.

```yaml
spec:
  containers:
  - name: spot-example
  tolerations:
  - key: "kubernetes.azure.com/scalesetpriority"
    operator: "Equal"
    value: "spot"
    effect: "NoSchedule"
   ...
```

När en POD med denna tolerera har distribuerats kan Kubernetes schemalägga Pod på noderna med den använda bismaken.

## <a name="max-price-for-a-spot-pool"></a>Högsta pris för en lagringspool
[Priser för punkt instanser är varierande][pricing-spot], baserat på region och SKU. Mer information finns i avsnittet om priser för [Linux][pricing-linux] och [Windows][pricing-windows].

Med varierande priser har du möjlighet att ange ett högsta pris i USD (USD) med upp till 5 decimaler. Värdet *0,98765* skulle till exempel vara ett max pris på $0,98765 USD per timme. Om du ställer in Max priset på *-1* avlägsnas instansen utifrån pris. Priset för instansen är det aktuella priset för dekor pris eller priset för en standard instans, beroende på vilket som är mindre, så länge det finns kapacitet och tillgänglig kvot.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du lägger till en adresspool för en plats i ett AKS-kluster. Mer information om hur du styr poddar över Node-pooler finns i [metod tips för avancerade funktioner i Schemaläggaren i AKS][operator-best-practices-advanced-scheduler].

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az-aks-nodepool-add
[cluster-autoscaler]: cluster-autoscaler.md
[eviction-policy]: ../virtual-machine-scale-sets/use-spot.md#eviction-policy
[kubernetes-concepts]: concepts-clusters-workloads.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[pricing-linux]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/linux/
[pricing-spot]: ../virtual-machine-scale-sets/use-spot.md#pricing
[pricing-windows]: https://azure.microsoft.com/pricing/details/virtual-machine-scale-sets/windows/
[spot-toleration]: #verify-the-spot-node-pool
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[use-multiple-node-pools]: use-multiple-node-pools.md
[vmss-spot]: ../virtual-machine-scale-sets/use-spot.md