---
title: Använda tillgänglighetszoner i Azure Kubernetes Service (AKS)
description: Lär dig hur du skapar ett kluster som distribuerar noder mellan tillgänglighetszoner i Azure Kubernetes Service (AKS)
services: container-service
ms.custom: fasttrack-edit, references_regions, devx-track-azurecli
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: 796cb0e2b76dc2a04834df61c053c5ad2ceb25fd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769633"
---
# <a name="create-an-azure-kubernetes-service-aks-cluster-that-uses-availability-zones"></a>Skapa ett Azure Kubernetes Service (AKS) som använder tillgänglighetszoner

Ett Azure Kubernetes Service (AKS)-kluster distribuerar resurser som noder och lagring över logiska delar av den underliggande Azure-infrastrukturen. Den här distributionsmodellen ser till att noder i en viss tillgänglighetszon är fysiskt åtskilda från de som definieras i en annan tillgänglighetszon när du använder tillgänglighetszoner. AKS-kluster som distribueras med flera tillgänglighetszoner som konfigurerats i ett kluster ger en högre tillgänglighetsnivå för att skydda mot maskinvarufel eller planerat underhåll.

Genom att definiera nodpooler i ett kluster för att sträcka sig över flera zoner kan noder i en viss nodpool fortsätta fungera även om en enda zon har försvunnit. Dina program kan fortsätta att vara tillgängliga även om det uppstår ett fysiskt fel i ett enda datacenter om de orkestreras för att tolerera fel på en delmängd noder.

Den här artikeln visar hur du skapar ett AKS-kluster och distribuerar nodkomponenterna mellan tillgänglighetszoner.

## <a name="before-you-begin"></a>Innan du börjar

Du måste ha installerat och konfigurerat Azure CLI version 2.0.76 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="limitations-and-region-availability"></a>Begränsningar och regionstillgänglighet

AKS-kluster kan för närvarande skapas med hjälp av tillgänglighetszoner i följande regioner:

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

Följande begränsningar gäller när du skapar ett AKS-kluster med hjälp av tillgänglighetszoner:

* Du kan bara definiera tillgänglighetszoner när klustret eller nodpoolen skapas.
* Det går inte att uppdatera inställningarna för tillgänglighetszoner när klustret har skapats. Du kan inte heller uppdatera ett befintligt zonkluster som inte är tillgängligt om du vill använda tillgänglighetszoner.
* Den valda nodstorleken (VM SKU) som valts måste vara tillgänglig för alla tillgänglighetszoner som valts.
* Kluster med tillgänglighetszoner aktiverade kräver användning av Azure Standard Load Balancers för distribution mellan zoner. Den här typen av lastbalanserare kan bara definieras när klustret skapas. Mer information och begränsningar för standardlastbalanserare finns i [Azure Load Balancer Standard SKU-begränsningar.][standard-lb-limitations]

### <a name="azure-disks-limitations"></a>Begränsningar för Azure-diskar

Volymer som använder Hanterade Azure-diskar är för närvarande inte zonredundanta resurser. Volymer kan inte kopplas mellan zoner och måste finnas i samma zon som en viss nod som är värd för målpodden.

Kubernetes känner till Azure-tillgänglighetszoner sedan version 1.12. Du kan distribuera ett PersistentVolumeClaim-objekt som refererar till en Azure Managed Disk i ett AKS-kluster med flera zoner och [Kubernetes](https://kubernetes.io/docs/setup/best-practices/multiple-zones/#storage-access-for-zones) tar hand om schemaläggningen av alla poddar som gör anspråk på denna PV i rätt tillgänglighetszon.

## <a name="overview-of-availability-zones-for-aks-clusters"></a>Översikt över tillgänglighetszoner för AKS-kluster

Tillgänglighetszoner är ett erbjudande med hög tillgänglighet som skyddar dina program och data mot datacenterfel. Zoner är unika fysiska platser i en Azure-region. Varje zon utgörs av ett eller flera datacenter som är utrustade med oberoende kraft, kylning och nätverk. För att säkerställa återhämtning finns det alltid fler än en zon i alla zonaktiverade regioner. Den fysiska avgränsningen av tillgänglighetszonerna inom en region skyddar program och data mot datacenterfel.

Mer information finns i Vad [är tillgänglighetszoner i Azure?][az-overview].

AKS-kluster som distribueras med hjälp av tillgänglighetszoner kan distribuera noder över flera zoner inom en enda region. Ett kluster i regionen USA, östra  *2 kan* till exempel skapa noder i alla   tre tillgänglighetszonerna i *USA, östra 2.* Den här distributionen av AKS-klusterresurser förbättrar klustertillgängligheten eftersom de är motståndskraftiga mot fel i en viss zon.

![AKS-noddistribution mellan tillgänglighetszoner](media/availability-zones/aks-availability-zones.png)

Om en enskild zon blir otillgänglig fortsätter dina program att köras om klustret är fördelat över flera zoner.

## <a name="create-an-aks-cluster-across-availability-zones"></a>Skapa ett AKS-kluster mellan tillgänglighetszoner

När du skapar ett kluster med kommandot [az aks create][az-aks-create] definierar `--zones` parametern vilka zonagentnoder som distribueras till. Kontrollplanskomponenterna, till exempel etcd eller API:et, sprids över de tillgängliga zonerna i regionen om du `--zones` definierar parametern när klustret skapas. De specifika zoner som kontrollplanskomponenterna är utspridda över är oberoende av vilka explicita zoner som väljs för den första nodpoolen.

Om du inte definierar några zoner för standardagentpoolen när du skapar ett AKS-kluster är det inte säkert att kontrollplanskomponenterna sprids över tillgänglighetszonerna. Du kan lägga till ytterligare nodpooler med kommandot [az aks nodepool add][az-aks-nodepool-add] och ange för nya noder, men det ändrar inte hur kontrollplanet har `--zones` spridits mellan zoner. Inställningar för tillgänglighetszoner kan bara definieras när klustret eller nodpoolen skapas.

I följande exempel skapas ett AKS-kluster med *namnet myAKSCluster* i resursgruppen med namnet *myResourceGroup*. Totalt skapas *3* noder – en agent i zon *1,* en i *2* och sedan en i *3*.

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

När du bestämmer vilken zon en ny nod ska tillhöra använder en viss AKS-nodpool zonbalansering med bästa möjliga resultat som erbjuds av [underliggande Azure Virtual Machine Scale Sets][vmss-zone-balancing]. En aks-nodpool betraktas som "balanserad" om varje zon har samma antal virtuella datorer eller + 1 virtuell dator i alla andra zoner för \- skalningsuppsättningen.

## <a name="verify-node-distribution-across-zones"></a>Verifiera noddistribution mellan zoner

När klustret är klart listar du agentnoderna i skalningsuppsättningen för att se vilken tillgänglighetszon de är distribuerade i.

Hämta först autentiseringsuppgifterna för AKS-klustret med hjälp av [kommandot az aks get-credentials:][az-aks-get-credentials]

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

Använd sedan kommandot [kubectl describe][kubectl-describe] för att visa en lista över noderna i klustret och filtrera *failure-domain.beta.kubernetes.io/zone* värdet. Följande exempel är för ett Bash-gränssnitt.

```console
kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"
```

Följande exempelutdata visar de tre noderna som är distribuerade i den angivna regionen och tillgänglighetszonerna, till exempel *eastus2-1* för den första tillgänglighetszonen och *eastus2-2* för den andra tillgänglighetszonen:

```console
Name:       aks-nodepool1-28993262-vmss000000
            failure-domain.beta.kubernetes.io/zone=eastus2-1
Name:       aks-nodepool1-28993262-vmss000001
            failure-domain.beta.kubernetes.io/zone=eastus2-2
Name:       aks-nodepool1-28993262-vmss000002
            failure-domain.beta.kubernetes.io/zone=eastus2-3
```

När du lägger till ytterligare noder i en agentpool distribuerar Azure-plattformen automatiskt de underliggande virtuella datorerna mellan de angivna tillgänglighetszonerna.

Observera att I nyare Kubernetes-versioner (1.17.0 och senare) använder AKS den nyare etiketten utöver den `topology.kubernetes.io/zone` inaktuella `failure-domain.beta.kubernetes.io/zone` . Du kan få samma resultat som ovan genom att köra följande skript:

```console
kubectl get nodes -o custom-columns=NAME:'{.metadata.name}',REGION:'{.metadata.labels.topology\.kubernetes\.io/region}',ZONE:'{metadata.labels.topology\.kubernetes\.io/zone}'
```

Vilket ger dig mer kortfattade utdata:

```console
NAME                                REGION   ZONE
aks-nodepool1-34917322-vmss000000   eastus   eastus-1
aks-nodepool1-34917322-vmss000001   eastus   eastus-2
aks-nodepool1-34917322-vmss000002   eastus   eastus-3
```

## <a name="verify-pod-distribution-across-zones"></a>Verifiera podddistribution mellan zoner

Enligt informationen i Välkända etiketter, Anteckningar och [Taints][kubectl-well_known_labels]använder Kubernetes etiketten för att automatiskt distribuera poddar i en replikeringskontrollant eller -tjänst i de olika zoner som `failure-domain.beta.kubernetes.io/zone` är tillgängliga. För att testa detta kan du skala upp klustret från 3 till 5 noder för att verifiera korrekt poddspridning:

```azurecli-interactive
az aks scale \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --node-count 5
```

När skalningsåtgärden slutförs efter några minuter bör kommandot i `kubectl describe nodes | grep -e "Name:" -e "failure-domain.beta.kubernetes.io/zone"` ett Bash-gränssnitt ge utdata som liknar det här exemplet:

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

Nu har vi två ytterligare noder i zon 1 och 2. Du kan distribuera ett program som består av tre repliker. Vi använder NGINX som exempel:

```console
kubectl create deployment nginx --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
kubectl scale deployment nginx --replicas=3
```

När du visar noderna där poddarna körs ser du att poddar körs på noderna som motsvarar tre olika tillgänglighetszoner. Med kommandot i ett Bash-gränssnitt får du till `kubectl describe pod | grep -e "^Name:" -e "^Node:"` exempel utdata som liknar följande:

```console
Name:         nginx-6db489d4b7-ktdwg
Node:         aks-nodepool1-28993262-vmss000000/10.240.0.4
Name:         nginx-6db489d4b7-v7zvj
Node:         aks-nodepool1-28993262-vmss000002/10.240.0.6
Name:         nginx-6db489d4b7-xz6wj
Node:         aks-nodepool1-28993262-vmss000004/10.240.0.8
```

Som du ser i tidigare utdata körs den första podden på nod 0, som finns i tillgänglighetszonen `eastus2-1` . Den andra podden körs på nod 2, som motsvarar `eastus2-3` och den tredje i nod 4 i `eastus2-2` . Utan ytterligare konfiguration sprider Kubernetes poddarna korrekt över alla tre tillgänglighetszonerna.

## <a name="next-steps"></a>Nästa steg

Den här artikeln beskriver hur du skapar ett AKS-kluster som använder tillgänglighetszoner. Mer information om kluster med hög tillgång finns i [Metodtips för affärskontinui och haveriberedskap i AKS.][best-practices-bc-dr]

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-overview]: ../availability-zones/az-overview.md
[best-practices-bc-dr]: operator-best-practices-multi-region.md
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[standard-lb-limitations]: load-balancer-standard.md#limitations
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-aks-nodepool-add]: /cli/azure/ext/aks-preview/aks/nodepool#ext-aks-preview-az-aks-nodepool-add
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[vmss-zone-balancing]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-use-availability-zones.md#zone-balancing

<!-- LINKS - external -->
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-well_known_labels]: https://kubernetes.io/docs/reference/labels-annotations-taints/
