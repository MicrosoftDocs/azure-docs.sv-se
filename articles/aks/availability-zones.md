---
title: Använda tillgänglighets zoner i Azure Kubernetes service (AKS)
description: Lär dig hur du skapar ett kluster som distribuerar noder över tillgänglighets zoner i Azure Kubernetes service (AKS)
services: container-service
ms.custom: fasttrack-edit, references_regions, devx-track-azurecli
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: 4c5b0ceb3f8e0b96f18a67ed0c7dbf1b56ac30da
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "104583555"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>Skapa ett Azure Kubernetes service-kluster (AKS) som använder tillgänglighets zoner

Ett Azure Kubernetes service-kluster (AKS) distribuerar resurser som noder och lagring över logiska delar av den underliggande Azure-infrastrukturen. Den här distributions modellen när du använder tillgänglighets zoner, säkerställer att noder i en viss tillgänglighets zon är fysiskt åtskilda från de som definierats i en annan tillgänglighets zon AKS-kluster som distribueras med flera tillgänglighets zoner som kon figurer ATS i ett kluster ger en högre tillgänglighets nivå för att skydda mot ett maskin varu fel eller ett planerat underhålls evenemang.

Genom att definiera resurspooler i ett kluster för att omfatta flera zoner kan noder i en viss Node-pool fortsätta att fungera även om en enda zon har varit avstängd. Dina program kan fortsätta att vara tillgängliga även om det uppstår ett fysiskt haveri i ett enda data Center om det har dirigerats för att tolerera en delmängd av noderna.

Den här artikeln visar hur du skapar ett AKS-kluster och distribuerar nodens komponenter över tillgänglighets zoner.

## <a name="before-you-begin"></a>Innan du börjar

Du behöver Azure CLI-versionen 2.0.76 eller senare installerad och konfigurerad. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="limitations-and-region-availability"></a>Begränsningar och region tillgänglighet

AKS-kluster kan för närvarande skapas med tillgänglighets zoner i följande regioner:

* Australien, östra
* Brasilien, södra
* Kanada, centrala
* Central US
* East US 
* USA, östra 2
* Frankrike, centrala
* Tyskland, västra centrala
* Japan, östra
* Norra Europa
* Sydostasien
* USA, södra centrala
* Storbritannien, södra
* US Gov, Virginia
* Europa, västra
* USA, västra 2

Följande begränsningar gäller när du skapar ett AKS-kluster med hjälp av tillgänglighets zoner:

* Du kan bara definiera tillgänglighets zoner när klustret eller Node-poolen skapas.
* Det går inte att uppdatera inställningarna för tillgänglighets zonen när klustret har skapats. Du kan inte heller uppdatera ett befintligt, icke-tillgänglighets zon kluster för att använda tillgänglighets zoner.
* Vald Node-storlek (VM-SKU) måste vara tillgänglig i alla valda tillgänglighets zoner.
* Kluster med aktiverade tillgänglighets zoner kräver användning av Azures standard belastnings utjämning för distribution mellan zoner. Den här typen av belastnings utjämning kan bara definieras i klustrets skapande tid. Mer information och begränsningarna för standard Load Balancer finns i begränsningar för [Azure Load Balancer standard SKU][standard-lb-limitations]: er.

### <a name="azure-disks-limitations"></a>Begränsningar för Azure disks

Volymer som använder Azure Managed disks är för närvarande inte zoner-redundanta resurser. Det går inte att koppla volymer mellan zoner och måste vara samplacerade i samma zon som en nod som är värd för mål-pod.

Kubernetes är medveten om tillgänglighets zoner i Azure sedan version 1,12. Du kan distribuera ett PersistentVolumeClaim-objekt som refererar till en Azure-hanterad disk i ett kluster med flera zoner och [Kubernetes kommer att ta hand om schemaläggning av](https://kubernetes.io/docs/setup/best-practices/multiple-zones/#storage-access-for-zones) alla Pod som hävdar denna PVC i rätt tillgänglighets zon.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Översikt över tillgänglighets zoner för AKS-kluster

Tillgänglighets zoner är ett erbjudande med hög tillgänglighet som skyddar dina program och data från data Center problem. Zoner är unika fysiska platser inom en Azure-region. Varje zon utgörs av ett eller flera datacenter som är utrustade med oberoende kraft, kylning och nätverk. För att garantera återhämtning finns det alltid mer än en zon i alla zoner som är aktiverade. Den fysiska avgränsningen av tillgänglighetszonerna inom en region skyddar program och data mot datacenterfel.

Mer information finns i [Vad är tillgänglighets zoner i Azure?][az-overview].

AKS-kluster som distribueras med hjälp av tillgänglighets zoner kan distribuera noder över flera zoner inom en enda region. Ett kluster i regionen  *USA, östra 2*   kan till exempel skapa noder i alla tre tillgänglighets zoner i *USA, östra 2*. Den här distributionen av AKS kluster resurser ger bättre kluster tillgänglighet eftersom de är elastiska till fel i en speciell zon.

![AKS Node-fördelning över tillgänglighets zoner](media/availability-zones/aks-availability-zones.png)

Om en enskild zon blir otillgänglig fortsätter dina program att köras om klustret sprids över flera zoner.

## <a name="create-an-aks-cluster-across-availability-zones"></a>Skapa ett AKS-kluster mellan tillgänglighets zoner

När du skapar ett kluster med kommandot [AZ AKS Create][az-aks-create] `--zones` definierar parametern vilka zoner som agent-noder distribueras till. Kontroll Plans komponenterna som etcd eller API: t sprids över de tillgängliga zonerna i regionen om du definierar `--zones` parametern vid skapande av klustret. De specifika zoner som kontroll Plans komponenterna sprids över är oberoende av vilka explicita zoner som väljs för den inledande Node-poolen.

Om du inte definierar några zoner för standard agenten när du skapar ett AKS-kluster, garanteras inte kontroll Plans komponenter att spridas över tillgänglighets zoner. Du kan lägga till ytterligare resurspooler med kommandot [AZ AKS nodepool Add][az-aks-nodepool-add] och ange `--zones` för nya noder, men det påverkar inte hur kontroll planet har spridits mellan zoner. Det går bara att definiera inställningar för tillgänglighets zonen i klustret eller i nodens skapande tid.

I följande exempel skapas ett AKS-kluster med namnet *myAKSCluster* i resurs gruppen med namnet *myResourceGroup*. Totalt *3* noder skapas – en agent i zon *1*, en i *2*, och sedan en i *3*.

```azurecli-interactive
az group create --name myResourceGroup --location eastus2

az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --generate-ssh-keys \
    --vm-set-type VirtualMachineScaleSets \
    --load-balancer-sku standard \
    --node-count 3 \
    --zones 1 2 3
```

Det tar några minuter att skapa AKS-klustret.

När du bestämmer vilken zon en ny nod ska tillhöra, använder en specifik AKS-adresspool en [utjämning av den bästa belastnings zonen som erbjuds av underliggande Azure-Virtual Machine Scale Sets][vmss-zone-balancing]. En angiven AKS Node-pool anses vara "bal anse rad" om varje zon har samma antal virtuella datorer eller + \- 1 virtuell dator i alla andra zoner för skalnings uppsättningen.

## <a name="verify-node-distribution-across-zones"></a>Verifiera fördelning av noder mellan zoner

När klustret är klart kan du ange vilken tillgänglighets zon de har distribuerats till i skalnings uppsättningen.

Börja med att hämta autentiseringsuppgifter för AKS-klustret med kommandot [AZ AKS get-credentials][az-aks-get-credentials] :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Använd sedan kommandot [kubectl beskriver][kubectl-describe] för att visa noderna i klustret och filtrera på *Failure-Domain.beta.Kubernetes.io/Zone* -värdet. Följande exempel gäller för ett bash-gränssnitt.

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

Följande exempel på utdata visar de tre noder som distribueras i den angivna regionen och tillgänglighets zonerna, till exempel *eastus2-1* för den första tillgänglighets zonen och *eastus2-2* för den andra tillgänglighets zonen:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

När du lägger till ytterligare noder i en agent distribuerar Azure-plattformen automatiskt de underliggande virtuella datorerna i de angivna tillgänglighets zonerna.

Observera att i senare Kubernetes-versioner (1.17.0 och senare) använder AKS den nyare etiketten `topology.kubernetes.io/zone` förutom den inaktuella `failure-domain.beta.kubernetes.io/zone` . Du kan få samma resultat som ovan med genom att köra följande skript:

```console
kubectl get nodes -o custom-columns=NAME:'{.metadata.name}',REGION:'{.metadata.labels.topology\.kubernetes\.io/region}',ZONE:'{metadata.labels.topology\.kubernetes\.io/zone}'
```

Du får en mer kortfattad utdata:

```console
NAME                                REGION   ZONE
aks-nodepool1-34917322-vmss000000   eastus   eastus-1
aks-nodepool1-34917322-vmss000001   eastus   eastus-2
aks-nodepool1-34917322-vmss000002   eastus   eastus-3
```

## <a name="verify-pod-distribution-across-zones"></a>Verifiera Pod-distribution mellan zoner

Som dokumenterade på [välkända etiketter, anteckningar och Utsmaker][kubectl-well_known_labels], använder Kubernetes `failure-domain.beta.kubernetes.io/zone` etiketten för att automatiskt distribuera poddar i en replikeringsprovider eller tjänst över olika zoner som är tillgängliga. För att testa detta kan du skala upp klustret från 3 till 5 noder för att verifiera korrekt Pod-spridning:

```azurecli-interactive
az aks scale \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 5
```

När skalnings åtgärden slutförs efter några minuter `kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"` bör kommandot i ett bash-gränssnitt ge utdata som liknar det här exemplet:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
Name:       aks-nodepool1-28993262-vmss000003
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000004
            failure-domain.beta.kubernetes.io/zone=eastus2-2
```

Nu har vi två ytterligare noder i zon 1 och 2. Du kan distribuera ett program som består av tre repliker. Vi kommer att använda NGINX som exempel:

```console
kubectl create deployment nginx --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
kubectl scale deployment nginx --replicas=3
```

Genom att Visa noder där dina poddar körs, ser du att poddar körs på noderna som motsvarar tre olika tillgänglighets zoner. Med kommandot `kubectl describe pod | grep -e "^Name:" -e "^Node:"` i ett bash-gränssnitt skulle du till exempel få utdata som liknar detta:

```console
Name:         nginx-6db489d4b7-ktdwg
Node:         aks-nodepool1-28993262-vmss000000/10.240.0.4
Name:         nginx-6db489d4b7-v7zvj
Node:         aks-nodepool1-28993262-vmss000002/10.240.0.6
Name:         nginx-6db489d4b7-xz6wj
Node:         aks-nodepool1-28993262-vmss000004/10.240.0.8
```

Som du kan se från föregående utdata körs den första Pod på nod 0, som finns i tillgänglighets zonen `eastus2-1` . Den andra Pod körs på nod 2, som motsvarar `eastus2-3` och den tredje i nod 4 i `eastus2-2` . Utan ytterligare konfiguration, Kubernetes spridningen av poddar korrekt i alla tre tillgänglighets zoner.

## <a name="next-steps"></a>Nästa steg

Den här artikeln innehåller information om hur du skapar ett AKS-kluster som använder tillgänglighets zoner. Mer information om kluster med hög tillgänglighet finns i [metod tips för verksamhets kontinuitet och haveri beredskap i AKS][best-practices-bc-dr].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-overview]: ../availability-zones/az-overview.md
[best-practices-bc-dr]: operator-best-practices-multi-region.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[standard-lb-limitations]: load-balancer-standard.md#limitations
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[vmss-zone-balancing]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing

<!-- LINKS - external -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-well_known_labels]: https://kubernetes.io/docs/reference/labels-annotations-taints/
