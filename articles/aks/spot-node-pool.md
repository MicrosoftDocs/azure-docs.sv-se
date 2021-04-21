---
title: Lägga till en skalningsuppsättningsnodpool för VM med oanvänd kapacitet till ett Azure Kubernetes Service-kluster (AKS)
description: Lär dig hur du lägger till en skalpunktsnodpool för oanade Azure Kubernetes Service kluster (AKS).
services: container-service
ms.service: container-service
ms.topic: article
ms.date: 10/19/2020
ms.openlocfilehash: f46a421ae2ad1a4d9c590c7e0b47784760ebcb9f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782809"
---
# <a name="add-a-spot-node-pool-to-an-azure-kubernetes-service-aks-cluster"></a>Lägga till en skalningsuppsättningsnodpool för VM med oanvänd kapacitet till ett Azure Kubernetes Service-kluster (AKS)

En skalningsuppsättningsnodpool för VM med VM med VM för VM med vm [-skalning för VM med skalning för VM med vm-skalningsuppsättning för VM][vmss-spot] Genom att använda virtuella datorer för oskadlighet för noder med ditt AKS-kluster kan du dra nytta av outnyttjad kapacitet i Azure till en betydande kostnadsbesparing. Mängden tillgänglig outnyttjad kapacitet varierar beroende på många faktorer, inklusive nodstorlek, region och tid på dagen.

När du distribuerar en nodpool för punkt punkt allokerar Azure noder för punkt om det finns tillgänglig kapacitet. Men det finns inget serviceavtal för noderna för punkt-till-plats. En skalningsuppsättning för skalning för punkt som backar upp skalningsuppsättningen för skalningsuppsättning för skalning för skalningsuppsättning med skalning för skalning för skalningsuppsättning för skalning för skalningsuppsättning med skalning för skalning för skalningsuppsättning med skalning för skalning för skalningsuppsättning med skalning för skalningsuppsättning för skal När Azure behöver kapaciteten igen avlägsnar Azure-infrastrukturen noder för punktenheter.

Noder för punkt-till-plats är bra för arbetsbelastningar som kan hantera avbrott, tidig avslutning eller avlägsning. Arbetsbelastningar som batchbearbetningsjobb, utvecklings- och testningsmiljöer och stora beräkningsarbetsbelastningar kan till exempel vara lämpliga kandidater för schemalagda i en nodpool för spot.

I den här artikeln lägger du till en sekundär skalningsplatsnodpool för oanade data i ett Azure Kubernetes Service(AKS)-kluster.

Den här artikeln förutsätter grundläggande kunskaper om Kubernetes och Azure Load Balancer begrepp. Mer information finns i [Viktiga koncept för Azure Kubernetes Service (AKS)][kubernetes-concepts].

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="before-you-begin"></a>Innan du börjar

När du skapar ett kluster för att använda en nodpool för opå  nytt måste klustret även använda Virtual Machine Scale Sets för nodpooler och standard-SKU-lastbalanserare. Du måste också lägga till ytterligare en nodpool när du har skapat klustret för att använda en nodpool för o.s.o.s. Vi går vidare med att lägga till ytterligare en nodpool i ett senare steg.

Den här artikeln kräver att du kör Azure CLI version 2.14 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

### <a name="limitations"></a>Begränsningar

Följande begränsningar gäller när du skapar och hanterar AKS-kluster med en skalpunktsnodpool för skalning för skalning:

* En nodpool för spot kan inte vara klustrets standardnodpool. En nodpool för punkt-till-plats kan bara användas för en sekundär pool.
* Du kan inte uppgradera en nodpool för oskadd med oskadd data eftersom nodpooler för oskadd punkt inte kan garantera avspärrning och tömning. Du måste ersätta din befintliga nodpool för punkt-till-plats med en ny för att göra-åtgärder, till exempel uppgradera Kubernetes-versionen. Om du vill ersätta en nodpool för spot-noder skapar du en ny nodpool för punkt med en annan version av Kubernetes, väntar tills dess status är *Klar* och tar sedan bort den gamla nodpoolen.
* Kontrollplanet och nodpoolerna kan inte uppgraderas samtidigt. Du måste uppgradera dem separat eller ta bort nodpoolen för punkt för att uppgradera kontrollplanet och återstående nodpooler på samma gång.
* En nodpool för datorer med Virtual Machine Scale Sets.
* Du kan inte ändra ScaleSetPriority eller SpotMaxPrice när du har skapat.
* När du anger SpotMaxPrice måste värdet vara -1 eller ett positivt värde med upp till fem decimaler.
* En nodpool för spot får *etiketten kubernetes.azure.com/scalesetpriority:spot*, taint-kubernetes.azure.com/scalesetpriority=spot:NoSchedule och systempoddar har antitillhörighet. 
* Du måste lägga till en [motsvarande tolerans för att schemalägga arbetsbelastningar][spot-toleration] i en nodpool för spot.

## <a name="add-a-spot-node-pool-to-an-aks-cluster"></a>Lägga till en skalningsuppsättningsnodpool för VM med oanvänd kapacitet i ett AKS-kluster

Du måste lägga till en nodpool för o.s.o.s. i ett befintligt kluster som har flera nodpooler aktiverade. Mer information om hur du skapar ett AKS-kluster med flera nodpooler finns [här.][use-multiple-node-pools]

Skapa en nodpool med [hjälp av az aks nodepool add][az-aks-nodepool-add].
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

Som standard skapar du en  nodpool med prioriteten *Vanlig* i AKS-klustret när du skapar ett kluster med flera nodpooler. Kommandot ovan lägger till en extra nodpool i ett befintligt AKS-kluster med *prioriteten* *Spot*. Prioriteten *för* punkt *gör nodpoolen* till en nodpool för spot. Parametern *eviction-policy* är inställd *på Ta* bort i exemplet ovan, vilket är standardvärdet. När du ställer in [borttagningsprincipen på][eviction-policy] *Ta* bort tas noder i den underliggande skalningsuppsättningen i nodpoolen bort när de avlägsnas. Du kan också ange avlägsningsprincipen *till Frisa*. När du anger avlägsningsprincipen till Frisänd *,* ställs noderna i den underliggande skalningsuppsättningen in på tillståndet stoppad frisgiven vid avlägsning. Antalet noder i tillståndet stoppad-frisatt mot beräkningskvoten och kan orsaka problem med klusterskalning eller uppgradering. *Prioritets-* *och avlägsningsprincipens* värden kan bara anges när nodpoolen skapas. Dessa värden kan inte uppdateras senare.

Kommandot aktiverar även autoskalning av [kluster, vilket][cluster-autoscaler]rekommenderas att använda med skalningsnodpooler för oskadlig användning. Baserat på de arbetsbelastningar som körs i klustret skalar autoskalning av kluster upp och ned antalet noder i nodpoolen. För skalningsnodpooler för oskadlig plats skalar autoskalningen upp antalet noder efter en avlägsning om ytterligare noder fortfarande behövs. Om du ändrar det maximala antalet noder som en nodpool kan ha måste du också justera värdet som `maxCount` är associerat med autoskalning av kluster. Om du inte använder en autoskalning av kluster minskar skalningsplatsens pool till slut till noll vid avlägsning och kräver en manuell åtgärd för att ta emot ytterligare noder för oskadlig plats.

> [!Important]
> Schemalägg endast arbetsbelastningar på nodpooler för punkt som kan hantera avbrott, till exempel batchbearbetningsjobb och testmiljöer. Vi rekommenderar att du ställer in [taints][taints-tolerations] och toleranser på din nodpool för punkt för att säkerställa att endast arbetsbelastningar som kan hantera nodförlägsningar schemaläggs i en nodpool för spot. Till exempel lägger kommandot ovan ny default till en taint på *kubernetes.azure.com/scalesetpriority=spot:NoSchedule* så att endast poddar med motsvarande tolerans schemaläggs på den här noden.

## <a name="verify-the-spot-node-pool"></a>Verifiera nodpoolen för punkt

Så här kontrollerar du att nodpoolen har lagts till som en nodpool för punkt:

```azurecli
az aks nodepool show --resource-group myResourceGroup --cluster-name myAKSCluster --name spotnodepool
```

Bekräfta *att scaleSetPriority* är *Spot*.

Om du vill schemalägga en podd som ska köras på en spot-nod lägger du till en tolerans som motsvarar taint-värdet som tillämpas på din spot-nod. I följande exempel visas en del av en yaml-fil som definierar en tolerans som motsvarar *en kubernetes.azure.com/scalesetpriority=spot:NoSchedule* taint som användes i föregående steg.

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

När en podd med den här toleransen distribueras kan Kubernetes schemalägga podden på noderna med taint tillämpad.

## <a name="max-price-for-a-spot-pool"></a>Högsta pris för en spotpool
[Prissättningen för spotinstanser är variabel,][pricing-spot]baserat på region och SKU. Mer information finns i priser för [Linux][pricing-linux] och [Windows.][pricing-windows]

Med variabelprissättning har du möjlighet att ange ett maxpris i AMERIKANSKA dollar (USD) med upp till 5 decimaler. Till exempel skulle värdet *0,98765* vara ett maxpris på 0,98765 USD per timme. Om du anger maxpriset *till -1* avlägsnas inte instansen baserat på priset. Priset för instansen blir det aktuella priset för punkt eller priset för en standardinstans, beroende på vilket som är mindre, så länge det finns tillgänglig kapacitet och kvot.

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du lägger till en skalning av skalbar skalning i ett AKS-kluster. Mer information om hur du styr poddar över nodpooler finns [i Metodtips för avancerade schemaläggarfunktioner i AKS.][operator-best-practices-advanced-scheduler]

<!-- LINKS - External -->
[kubernetes-services]: https://kubernetes.io/docs/concepts/services-networking/service/

<!-- LINKS - Internal -->
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-nodepool-add]: /cli/azure/aks/nodepool#az_aks_nodepool_add
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