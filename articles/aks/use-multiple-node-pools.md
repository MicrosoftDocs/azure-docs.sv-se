---
title: Använda flera nodpooler i Azure Kubernetes Service (AKS)
description: Lär dig hur du skapar och hanterar flera nodpooler för ett kluster Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 02/11/2021
ms.openlocfilehash: b7b54ccf6662e172ebfe95a84189df5e8e6e990f
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832256"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Skapa och hantera flera nodpooler för ett kluster i Azure Kubernetes Service (AKS)

I Azure Kubernetes Service (AKS) grupperas noder i samma konfiguration tillsammans i *nodpooler*. Dessa nodpooler innehåller de underliggande virtuella datorer som kör dina program. Det första antalet noder och deras storlek (SKU) definieras när du skapar ett AKS-kluster, vilket skapar en [systemnodpool][use-system-pool]. För att stödja program som har olika beräknings- eller lagringskrav kan du skapa ytterligare *användarnodpooler.* Systemnodpooler har det primära syftet att vara värd för kritiska systempoddar, till exempel CoreDNS och tunnelfront. Användarnodpooler är det primära syftet med att vara värd för dina programpoddar. Programpoddar kan dock schemaläggas i systemnodpooler om du bara vill ha en pool i ditt AKS-kluster. Användarnodpooler är den plats där du placerar dina programspecifika poddar. Använd till exempel dessa ytterligare användarnodpooler för att tillhandahålla GPU:er för beräkningsintensiva program eller åtkomst till högpresterande SSD-lagring.

> [!NOTE]
> Den här funktionen ger högre kontroll över hur du skapar och hanterar flera nodpooler. Därför krävs separata kommandon för att skapa/uppdatera/ta bort. Tidigare klusteråtgärder via eller använde managedCluster-API:et och var det enda alternativet för att `az aks create` `az aks update` ändra kontrollplanet och en enskild nodpool. Den här funktionen exponerar en separat åtgärdsuppsättning för agentpooler via agentPool-API:et och kräver användning av kommandouppsättningen för att köra `az aks nodepool` åtgärder på en enskild nodpool.

Den här artikeln visar hur du skapar och hanterar flera nodpooler i ett AKS-kluster.

## <a name="before-you-begin"></a>Innan du börjar

Azure CLI version 2.2.0 eller senare måste vara installerat och konfigurerat. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="limitations"></a>Begränsningar

Följande begränsningar gäller när du skapar och hanterar AKS-kluster som stöder flera nodpooler:

* Se [Kvoter, storleksbegränsningar för virtuella datorer och regionstillgänglighet i Azure Kubernetes Service (AKS).][quotas-skus-regions]
* Du kan ta bort systemnodpooler, förutsatt att du har en annan systemnodpool att ta plats i AKS-klustret.
* Systempooler måste innehålla minst en nod och användarnodpooler kan innehålla noll eller flera noder.
* AKS-klustret måste använda standard-SKU-lastbalanserare för att använda flera nodpooler. Funktionen stöds inte med Basic SKU-lastbalanserare.
* AKS-klustret måste använda VM-skalningsuppsättningar för noderna.
* Namnet på en nodpool får bara innehålla gemena alfanumeriska tecken och måste börja med en gemen bokstav. För Linux-nodpooler måste längden vara mellan 1 och 12 tecken. För Windows-nodpooler måste längden vara mellan 1 och 6 tecken.
* Alla nodpooler måste finnas i samma virtuella nätverk.
* När du skapar flera nodpooler när klustret skapas måste alla Kubernetes-versioner som används av nodpooler matcha den version som angetts för kontrollplanet. Detta kan uppdateras när klustret har etablerats med hjälp av åtgärder per nodpool.

## <a name="create-an-aks-cluster"></a>Skapa ett AKS-kluster

> [!Important]
> Om du kör en enda systemnodpool för ditt AKS-kluster i en produktionsmiljö rekommenderar vi att du använder minst tre noder för nodpoolen.

Kom igång genom att skapa ett AKS-kluster med en enda nodpool. I följande exempel används kommandot [az group create][az-group-create] för att skapa en resursgrupp med namnet *myResourceGroup* i *regionen eastus.* Sedan skapas ett *AKS-kluster med namnet myAKSCluster* med kommandot [az aks][az-aks-create] create.

> [!NOTE]
> *SKU:n* för basic-lastbalanserare **stöds inte när** du använder flera nodpooler. Som standard skapas AKS-kluster  med standard-SKU:n för lastbalanserare från Azure CLI och Azure Portal.

```azurecli-interactive
# Create a resource group in East US
az group create --name myResourceGroup --location eastus

# Create a basic single-node AKS cluster
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --vm-set-type VirtualMachineScaleSets \
    --node-count 2 \
    --generate-ssh-keys \
    --load-balancer-sku standard
```

Det tar några minuter att skapa klustret.

> [!NOTE]
> För att säkerställa att klustret fungerar på ett tillförlitligt sätt bör du köra minst 2 (två) noder i standardnodpoolen, eftersom viktiga systemtjänster körs i den här nodpoolen.

När klustret är klart använder du kommandot [az aks get-credentials][az-aks-get-credentials] för att hämta autentiseringsuppgifterna för klustret för användning med `kubectl` :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Lägga till en nodpool

Klustret som skapades i föregående steg har en enda nodpool. Nu ska vi lägga till en andra nodpool med [kommandot az aks nodepool add.][az-aks-nodepool-add] I följande exempel skapas en nodpool med *namnet mynodepool* som kör *3* noder:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3
```

> [!NOTE]
> Namnet på en nodpool måste börja med en gemen och får bara innehålla alfanumeriska tecken. För Linux-nodpooler måste längden vara mellan 1 och 12 tecken. För Windows-nodpooler måste längden vara mellan 1 och 6 tecken.

Om du vill se status för dina nodpooler använder du [kommandot az aks node pool list][az-aks-nodepool-list] och anger resursgruppen och klusternamnet:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

Följande exempelutdata visar att *mynodepool* har skapats med tre noder i nodpoolen. När AKS-klustret skapades i föregående steg skapades en *standardnodpool1* med antalet noder *på 2*.

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

> [!TIP]
> Om ingen *VmSize* anges när du lägger till en nodpool är standardstorleken *Standard_D2s_v3* för Windows-nodpooler *och Standard_DS2_v2* För Linux-nodpooler. Om ingen *OrchestratorVersion* har angetts får den som standard samma version som kontrollplanet.

### <a name="add-a-node-pool-with-a-unique-subnet-preview"></a>Lägga till en nodpool med ett unikt undernät (förhandsversion)

En arbetsbelastning kan kräva delning av ett klusters noder i separata pooler för logisk isolering. Den här isoleringen kan stödjas med separata undernät som är dedikerade till varje nodpool i klustret. Detta kan hantera krav som att ha ett icke-sammanhängande adressutrymme för virtuella nätverk som ska delas upp mellan nodpooler.

#### <a name="limitations"></a>Begränsningar

* Alla undernät som tilldelats till nodepools måste tillhöra samma virtuella nätverk.
* Systempoddar måste ha åtkomst till alla noder/poddar i klustret för att kunna tillhandahålla kritiska funktioner som DNS-upplösning och tunneling av kubectl-loggar/exec/port-forward proxy.
* Om du expanderar ditt virtuella nätverk när du har skapat klustret måste du uppdatera klustret (utföra en hanterad clster-åtgärd men nodpoolåtgärderna räknas inte) innan du lägger till ett undernät utanför den ursprungliga cidr-platsen. AKS kommer att få ett felmeddelande om agentpoolens tillägg nu, även om vi ursprungligen tillät det. Om du inte vet hur du ska stämma av ditt kluster kan du skapa en supportbiljett. 
* Det finns inte stöd för Nätverksprincip förCoco. 
* Azure Network Policy stöds inte.
* Kube-proxy förväntar sig en enda sammanhängande cidr och använder den för tre optmizations. Se [denna K.E.P.](https://github.com/kubernetes/enhancements/tree/master/keps/sig-network/2450-Remove-knowledge-of-pod-cluster-CIDR-from-iptables-rules) och --cluster-cidr [här för](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-proxy/) mer information. I azure cni ges den första nodpoolens undernät till kube-proxy. 

Om du vill skapa en nodpool med ett dedikerat undernät skickar du undernätets resurs-ID som en extra parameter när du skapar en nodpool.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --vnet-subnet-id <YOUR_SUBNET_RESOURCE_ID>
```

## <a name="upgrade-a-node-pool"></a>Uppgradera en nodpool

> [!NOTE]
> Uppgraderings- och skalningsåtgärder i ett kluster eller en nodpool kan inte ske samtidigt, om ett fel returneras. I stället måste varje åtgärdstyp slutföras på målresursen före nästa begäran för samma resurs. Läs mer om detta i vår [felsökningsguide.](./troubleshooting.md#im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-upgraded-or-has-failed-upgrade)

Kommandona i det här avsnittet förklarar hur du uppgraderar en enskild specifik nodpool. Relationen mellan uppgradering av Kubernetes-versionen av kontrollplanet och nodpoolen beskrivs i [avsnittet nedan](#upgrade-a-cluster-control-plane-with-multiple-node-pools).

> [!NOTE]
> Nodpoolens OS-avbildningsversion är kopplad till Kubernetes-versionen av klustret. Du får bara uppgraderingar av OPERATIVSYSTEMavbildningar efter en klusteruppgradering.

Eftersom det finns två nodpooler i det här exemplet måste vi använda [az aks nodepool upgrade för][az-aks-nodepool-upgrade] att uppgradera en nodpool. Om du vill se tillgängliga uppgraderingar [använder du az aks get-upgrades][az-aks-get-upgrades]

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Nu ska vi uppgradera *mynodepool*. Använd kommandot [az aks nodepool upgrade][az-aks-nodepool-upgrade] för att uppgradera nodpoolen, som du ser i följande exempel:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version KUBERNETES_VERSION \
    --no-wait
```

Visa status för dina nodpooler igen med kommandot [az aks node pool list.][az-aks-nodepool-list] I följande exempel visas att *mynodepool* är i *uppgraderingstillståndet* för *att KUBERNETES_VERSION*:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 3,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "KUBERNETES_VERSION",
    ...
    "provisioningState": "Upgrading",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Det tar några minuter att uppgradera noderna till den angivna versionen.

Som bästa praxis bör du uppgradera alla nodpooler i ett AKS-kluster till samma Kubernetes-version. Standardbeteendet för `az aks upgrade` är att uppgradera alla nodpooler tillsammans med kontrollplanet för att uppnå den här justeringen. Med möjligheten att uppgradera enskilda nodpooler kan du utföra en löpande uppgradering och schemalägga poddar mellan nodpooler för att upprätthålla programmets drifttid inom ovanstående begränsningar.

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>Uppgradera ett klusterkontrollplan med flera nodpooler

> [!NOTE]
> Kubernetes använder [standardschemat för semantisk](https://semver.org/) versionshantering. Versionsnumret uttrycks som *x.y.z*, där *x* är huvudversionen, *y* är delversionen och *z* är korrigeringsversionen. I version *1.12.6* är till exempel 1 huvudversionen, 12 är delversionen och 6 är korrigeringsversionen. Kubernetes-versionen av kontrollplanet och den första nodpoolen anges när klustret skapas. Alla ytterligare nodpooler har sina Kubernetes-versioner inställda när de läggs till i klustret. Kubernetes-versionerna kan skilja sig åt mellan nodpooler samt mellan en nodpool och kontrollplanet.

Ett AKS-kluster har två klusterresursobjekt med associerade Kubernetes-versioner.

1. En Kubernetes-version för klusterkontrollplanet.
2. En nodpool med en Kubernetes-version.

Ett kontrollplan mappar till en eller flera nodpooler. Beteendet för en uppgraderingsåtgärd beror på vilket Azure CLI-kommando som används.

Uppgradering av ett AKS-kontrollplan kräver att du använder `az aks upgrade` . Det här kommandot uppgraderar kontrollplanets version och alla nodpooler i klustret.

När kommandot `az aks upgrade` utfärdas med flaggan `--control-plane-only` uppgraderas endast kontrollplanet för klustret. Ingen av de associerade nodpoolerna i klustret ändras.

Uppgradering av enskilda nodpooler kräver att du använder `az aks nodepool upgrade` . Det här kommandot uppgraderar endast målnodpoolen med den angivna Kubernetes-versionen

### <a name="validation-rules-for-upgrades"></a>Verifieringsregler för uppgraderingar

Giltiga Kubernetes-uppgraderingar för ett klusters kontrollplan och nodpooler verifieras av följande regeluppsättningar.

* Regler för giltiga versioner för uppgradering av nodpooler:
   * Nodpoolversionen måste ha samma *huvudversion* som kontrollplanet.
   * Delversionen av *nodpoolen* måste finnas *i två mindre* versioner av kontrollplansversionen.
   * Nodpoolversionen får inte vara större än `major.minor.patch` kontrollversionen.

* Regler för att skicka en uppgraderingsåtgärd:
   * Du kan inte nedgradera kontrollplanet eller en Kubernetes-version för en nodpool.
   * Om ingen Kubernetes-version anges för en nodpool beror beteendet på den klient som används. Deklarationen i Resource Manager-mallarna faller tillbaka på den befintliga version som definierats för nodpoolen om den används, om ingen har angetts används kontrollplansversionen för att användas igen.
   * Du kan antingen uppgradera eller skala ett kontrollplan eller en nodpool vid en viss tidpunkt. Du kan inte skicka flera åtgärder på ett enda kontrollplan eller en nodpoolresurs samtidigt.

## <a name="scale-a-node-pool-manually"></a>Skala en nodpool manuellt

När programmets arbetsbelastningskrav ändras kan du behöva skala antalet noder i en nodpool. Antalet noder kan skalas upp eller ned.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

Om du vill skala antalet noder i en nodpool använder du [kommandot az aks node pool scale.][az-aks-nodepool-scale] I följande exempel skalas antalet noder i *mynodepool* till *5*:

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

Visa status för dina nodpooler igen med kommandot [az aks node pool list.][az-aks-nodepool-list] I följande exempel visas *att mynodepool* är i *skalningstillstånd* med ett nytt antal *5* noder:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Scaling",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Det tar några minuter för skalningsåtgärden att slutföras.

## <a name="scale-a-specific-node-pool-automatically-by-enabling-the-cluster-autoscaler"></a>Skala en specifik nodpool automatiskt genom att aktivera autoskalning av kluster

AKS erbjuder en separat funktion för automatisk skalning av nodpooler med en funktion som [kallas autoskalning av kluster.](cluster-autoscaler.md) Den här funktionen kan aktiveras per nodpool med unikt minsta och högsta skalningsantal per nodpool. Lär dig hur [du använder autoskalning av kluster per nodpool.](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled)

## <a name="delete-a-node-pool"></a>Ta bort en nodpool

Om du inte längre behöver en pool kan du ta bort den och ta bort de underliggande VM-noderna. Om du vill ta bort en nodpool använder du [kommandot az aks node pool delete][az-aks-nodepool-delete] och anger namnet på nodpoolen. I följande exempel tas *mynoodepool som* skapades i föregående steg bort:

> [!CAUTION]
> Det finns inga återställningsalternativ för dataförlust som kan uppstå när du tar bort en nodpool. Om poddar inte kan schemaläggas i andra nodpooler är dessa program inte tillgängliga. Se till att du inte tar bort en nodpool när program som används inte har säkerhetskopior av data eller möjligheten att köras på andra nodpooler i klustret.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

Följande exempelutdata från kommandot [az aks node pool list][az-aks-nodepool-list] visar att *mynodepool* är i tillståndet *Borttagning:*

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 5,
    ...
    "name": "mynodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Deleting",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Det tar några minuter att ta bort noderna och nodpoolen.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Ange en VM-storlek för en nodpool

I de föregående exemplen för att skapa en nodpool användes en standardstorlek för virtuella datorer för noderna som skapades i klustret. Ett vanligare scenario är att du skapar nodpooler med olika storlekar och funktioner för virtuella datorer. Du kan till exempel skapa en nodpool som innehåller noder med stora mängder cpu eller minne, eller en nodpool som ger GPU-stöd. I nästa steg använder du taints och toleranser för att berätta för Kubernetes-schemaläggaren hur åtkomsten till poddar som kan köras på dessa noder ska [begränsas.](#setting-nodepool-taints)

I följande exempel skapar du en GPU-baserad  nodpool som använder Standard_NC6 VM-storlek. De här virtuella datorerna drivs av KORTET NVIDIA Tesla K80. Information om tillgängliga VM-storlekar finns i [Storlekar för virtuella Linux-datorer i Azure.][vm-sizes]

Skapa en nodpool med [kommandot az aks node pool add][az-aks-nodepool-add] igen. Den här gången anger du namnet *gpunodepool* och använder `--node-vm-size` parametern för att ange *Standard_NC6* storlek:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

Följande exempelutdata från [kommandot az aks node pool list][az-aks-nodepool-list] visar att *gpunodepool* är *Creating* nodes with the specified *VmSize*:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "gpunodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "vmSize": "Standard_NC6",
    ...
  },
  {
    ...
    "count": 2,
    ...
    "name": "nodepool1",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Succeeded",
    ...
    "vmSize": "Standard_DS2_v2",
    ...
  }
]
```

Det tar några minuter för *gpunodepoolen* att skapas.

## <a name="specify-a-taint-label-or-tag-for-a-node-pool"></a>Ange en taint, etikett eller tagg för en nodpool

När du skapar en nodpool kan du lägga till taints, etiketter eller taggar till nodpoolen. När du lägger till en taint, etikett eller tagg får alla noder i nodpoolen även den taint-, etikett- eller taggen.

> [!IMPORTANT]
> Att lägga till taints, etiketter eller taggar till noder bör göras för hela nodpoolen med hjälp av `az aks nodepool` . Vi rekommenderar inte att du tillämpar taints, lablels eller taggar på enskilda noder i en `kubectl` nodpool med hjälp av .  

### <a name="setting-nodepool-taints"></a>Ange nodepool-taints

Om du vill skapa en nodpool med en taint använder [du az aks nodepool add][az-aks-nodepool-add]. Ange namnet *taintnp och* använd `--node-taints` parametern för att ange *sku=gpu:NoSchedule* för taint.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name taintnp \
    --node-count 1 \
    --node-taints sku=gpu:NoSchedule \
    --no-wait
```

> [!NOTE]
> En taint kan bara anges för nodpooler när nodpoolen skapas.

Följande exempelutdata från [kommandot az aks nodepool list][az-aks-nodepool-list] visar att *taintnp* är *Creating* nodes with the *specified nodeTaints*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "taintnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeTaints":  [
      "sku=gpu:NoSchedule"
    ],
    ...
  },
 ...
]
```

Taint-informationen visas i Kubernetes för hantering av schemaläggningsregler för noder. Kubernetes-schemaläggaren kan använda taints och toleranser för att begränsa vilka arbetsbelastningar som kan köras på noder.

* En **taint** tillämpas på en nod som anger att endast specifika poddar kan schemaläggas på dem.
* En **tolerans tillämpas sedan** på en podd som gör att de kan *tolerera* en nods taint.

Mer information om hur du använder avancerade schemalagda Kubernetes-funktioner finns i [Metodtips för avancerade schemaläggningsfunktioner i AKS][taints-tolerations]

I föregående steg tillämpade du *sku=gpu:NoSchedule* taint när du skapade nodpoolen. I följande grundläggande YAML-exempelmanifest används en tolerans som gör att Kubernetes-schemaläggaren kan köra en NGINX-podd på en nod i nodpoolen.

Skapa en fil med `nginx-toleration.yaml` namnet och kopiera i följande YAML-exempel:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: mypod
spec:
  containers:
  - image: mcr.microsoft.com/oss/nginx/nginx:1.15.9-alpine
    name: mypod
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 1
        memory: 2G
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

Schemalägg podden med `kubectl apply -f nginx-toleration.yaml` kommandot :

```console
kubectl apply -f nginx-toleration.yaml
```

Det tar några sekunder att schemalägga podden och hämta NGINX-avbildningen. Använd [kommandot kubectl describe pod][kubectl-describe] för att visa poddstatusen. Följande komprimerade exempelutdata visar *att toleransen sku=gpu:NoSchedule* tillämpas. I avsnittet händelser har schemaläggaren tilldelat podden till *noden aks-taintnp-28993262-vmss000000:*

```console
kubectl describe pod mypod
```

```output
[...]
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                 node.kubernetes.io/unreachable:NoExecute for 300s
                 sku=gpu:NoSchedule
Events:
  Type    Reason     Age    From                Message
  ----    ------     ----   ----                -------
  Normal  Scheduled  4m48s  default-scheduler   Successfully assigned default/mypod to aks-taintnp-28993262-vmss000000
  Normal  Pulling    4m47s  kubelet             pulling image "mcr.microsoft.com/oss/nginx/nginx:1.15.9-alpine"
  Normal  Pulled     4m43s  kubelet             Successfully pulled image "mcr.microsoft.com/oss/nginx/nginx:1.15.9-alpine"
  Normal  Created    4m40s  kubelet             Created container
  Normal  Started    4m40s  kubelet             Started container
```

Endast poddar som har den här toleransen tillämpad kan schemaläggas på noder *i taintnp*. Alla andra poddar schemaläggs i *nodepool1-nodpoolen.* Om du skapar ytterligare nodpooler kan du använda ytterligare taints och toleranser för att begränsa vilka poddar som kan schemaläggas på dessa nodresurser.

### <a name="setting-nodepool-labels"></a>Ställa in etiketter för nodpool

Du kan också lägga till etiketter i en nodpool när nodpoolen skapas. Etiketter som anges i nodpoolen läggs till i varje nod i nodpoolen. De [här etiketterna visas i Kubernetes för][kubernetes-labels] hantering av schemaläggningsregler för noder.

Om du vill skapa en nodpool med en etikett använder du [az aks nodepool add][az-aks-nodepool-add]. Ange namnet *labelnp och använd* parametern för att ange `--labels` *dept=IT* och *costcenter=9999* för etiketter.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name labelnp \
    --node-count 1 \
    --labels dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> Etiketten kan bara anges för nodpooler när nodpoolen skapas. Etiketter måste också vara ett nyckel/värde-par och ha en [giltig syntax][kubernetes-label-syntax].

Följande exempelutdata från [kommandot az aks nodepool list][az-aks-nodepool-list] visar att *labelnp* är *Creating* nodes with the specified *nodeLabels*:

```console
$ az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster

[
  {
    ...
    "count": 1,
    ...
    "name": "labelnp",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "nodeLabels":  {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

### <a name="setting-nodepool-azure-tags"></a>Ange Azure-taggar för nodepool

Du kan använda en Azure-tagg för nodpooler i ditt AKS-kluster. Taggar som tillämpas på en nodpool tillämpas på varje nod i nodpoolen och bevaras genom uppgraderingar. Taggar tillämpas också på nya noder som läggs till i en nodpool under utskalningsåtgärder. Att lägga till en tagg kan hjälpa till med uppgifter som principspårning eller kostnadsuppskattning.

Azure-taggar har nycklar som är icke-känsliga för åtgärder, till exempel när du hämtar en tagg genom att söka i nyckeln. I det här fallet uppdateras eller hämtas en tagg med den angivna nyckeln oavsett hölje. Taggvärden är fallkänsliga.

Om flera taggar har angetts med identiska nycklar men ett annat hölje i AKS är taggen som används den första i alfabetisk ordning. Till exempel resulterar `{"Key1": "val1", "kEy1": "val2", "key1": "val3"}` i `Key1` och `val1` anges.

Skapa en nodpool med [hjälp av az aks nodepool add][az-aks-nodepool-add]. Ange namnet *tagnodepool och* använd parametern för att ange `--tag` *dept=IT* och *costcenter=9999* för taggar.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name tagnodepool \
    --node-count 1 \
    --tags dept=IT costcenter=9999 \
    --no-wait
```

> [!NOTE]
> Du kan också använda `--tags` parametern när du använder [kommandot az aks nodepool update][az-aks-nodepool-update] samt när klustret skapas. När klustret skapas tillämpar `--tags` parametern taggen på den första nodpoolen som skapades med klustret. Alla taggnamn måste följa begränsningarna i Använda taggar [för att organisera Dina Azure-resurser.][tag-limitation] När du uppdaterar en `--tags` nodpool med parametern uppdateras eventuella befintliga taggvärden och eventuella nya taggar läggs till. Om nodpoolen till exempel hade *dept=IT* och *costcenter=9999* för taggar och du uppdaterade den med *team=dev* och *costcenter=111* för taggar, skulle nodepoolen ha *dept=IT*, *costcenter=111* och *team=dev* för taggar.

Följande exempelutdata från kommandot [az aks nodepool list][az-aks-nodepool-list] visar att *tagnodepool* är *Creating* nodes with the specified *tag*:

```azurecli
az aks nodepool list -g myResourceGroup --cluster-name myAKSCluster
```

```output
[
  {
    ...
    "count": 1,
    ...
    "name": "tagnodepool",
    "orchestratorVersion": "1.15.7",
    ...
    "provisioningState": "Creating",
    ...
    "tags": {
      "dept": "IT",
      "costcenter": "9999"
    },
    ...
  },
 ...
]
```

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Hantera nodpooler med en Resource Manager mall

När du använder en Azure Resource Manager för att skapa och hantera resurser kan du vanligtvis uppdatera inställningarna i mallen och distribuera om för att uppdatera resursen. Med nodpooler i AKS kan den första nodpoolprofilen inte uppdateras när AKS-klustret har skapats. Det här beteendet innebär att du inte kan uppdatera en Resource Manager en mall, göra en ändring i nodpoolerna och distribuera om. I stället måste du skapa en separat Resource Manager som bara uppdaterar nodpoolerna för ett befintligt AKS-kluster.

Skapa en mall som och `aks-agentpools.json` klistra in följande exempelmanifest. Den här exempelmallen konfigurerar följande inställningar:

* Uppdaterar *Linux-nodpoolen* med *namnet myagentpool* för att köra tre noder.
* Anger noderna i nodpoolen för att köra Kubernetes version *1.15.7.*
* Definierar nodstorleken *som Standard_DS2_v2*.

Redigera dessa värden när du behöver uppdatera, lägga till eller ta bort nodpooler efter behov:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "clusterName": {
            "type": "string",
            "metadata": {
                "description": "The name of your existing AKS cluster."
            }
        },
        "location": {
            "type": "string",
            "metadata": {
                "description": "The location of your existing AKS cluster."
            }
        },
        "agentPoolName": {
            "type": "string",
            "defaultValue": "myagentpool",
            "metadata": {
                "description": "The name of the agent pool to create or update."
            }
        },
        "vnetSubnetId": {
            "type": "string",
            "defaultValue": "",
            "metadata": {
                "description": "The Vnet subnet resource ID for your existing AKS cluster."
            }
        }
    },
    "variables": {
        "apiVersion": {
            "aks": "2020-01-01"
        },
        "agentPoolProfiles": {
            "maxPods": 30,
            "osDiskSizeGB": 0,
            "agentCount": 3,
            "agentVmSize": "Standard_DS2_v2",
            "osType": "Linux",
            "vnetSubnetId": "[parameters('vnetSubnetId')]"
        }
    },
    "resources": [
        {
            "apiVersion": "2020-01-01",
            "type": "Microsoft.ContainerService/managedClusters/agentPools",
            "name": "[concat(parameters('clusterName'),'/', parameters('agentPoolName'))]",
            "location": "[parameters('location')]",
            "properties": {
                "maxPods": "[variables('agentPoolProfiles').maxPods]",
                "osDiskSizeGB": "[variables('agentPoolProfiles').osDiskSizeGB]",
                "count": "[variables('agentPoolProfiles').agentCount]",
                "vmSize": "[variables('agentPoolProfiles').agentVmSize]",
                "osType": "[variables('agentPoolProfiles').osType]",
                "storageProfile": "ManagedDisks",
                "type": "VirtualMachineScaleSets",
                "vnetSubnetID": "[variables('agentPoolProfiles').vnetSubnetId]",
                "orchestratorVersion": "1.15.7"
            }
        }
    ]
}
```

Distribuera den här mallen med [kommandot az deployment group create,][az-deployment-group-create] som du ser i följande exempel. Du uppmanas att ange det befintliga AKS-klusternamnet och platsen:

```azurecli-interactive
az deployment group create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

> [!TIP]
> Du kan lägga till en tagg  i nodpoolen genom att lägga till taggegenskapen i mallen, som du ser i följande exempel.
> 
> ```json
> ...
> "resources": [
> {
>   ...
>   "properties": {
>     ...
>     "tags": {
>       "name1": "val1"
>     },
>     ...
>   }
> }
> ...
> ```

Det kan ta några minuter att uppdatera ditt AKS-kluster beroende på inställningarna och åtgärderna för nodpoolen som du definierar i Resource Manager mallen.

## <a name="assign-a-public-ip-per-node-for-your-node-pools"></a>Tilldela en offentlig IP-adress per nod för dina nodpooler

AKS-noder kräver inte sina egna offentliga IP-adresser för kommunikation. Scenarier kan dock kräva att noder i en nodpool tar emot sina egna dedikerade offentliga IP-adresser. Ett vanligt scenario är för spelarbetsbelastningar, där en konsol måste upprätta en direktanslutning till en virtuell dator i molnet för att minimera hopp. Det här scenariot kan uppnås på AKS med hjälp av Node Public IP.

Skapa först en ny resursgrupp.

```azurecli-interactive
az group create --name myResourceGroup2 --location eastus
```

Skapa ett nytt AKS-kluster och koppla en offentlig IP-adress för dina noder. Var och en av noderna i nodpoolen får en unik offentlig IP-adress. Du kan kontrollera detta genom att titta på instanserna av VM-skalningsuppsättningen.

```azurecli-interactive
az aks create -g MyResourceGroup2 -n MyManagedCluster -l eastus  --enable-node-public-ip
```

För befintliga AKS-kluster kan du också lägga till en ny nodpool och koppla en offentlig IP-adress för dina noder.

```azurecli-interactive
az aks nodepool add -g MyResourceGroup2 --cluster-name MyManagedCluster -n nodepool2 --enable-node-public-ip
```

### <a name="use-a-public-ip-prefix"></a>Använda ett offentligt IP-prefix

Det finns ett antal fördelar [med att använda ett offentligt IP-prefix][public-ip-prefix-benefits]. AKS stöder användning av adresser från ett befintligt offentligt IP-prefix för dina noder genom att skicka resurs-ID:t med flaggan när du skapar ett nytt kluster eller lägger till `node-public-ip-prefix` en nodpool.

Skapa först ett offentligt IP-prefix med [az network public-ip prefix create][az-public-ip-prefix-create]:

```azurecli-interactive
az network public-ip prefix create --length 28 --location eastus --name MyPublicIPPrefix --resource-group MyResourceGroup3
```

Visa utdata och anteckna `id` för prefixet :

```output
{
  ...
  "id": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup3/providers/Microsoft.Network/publicIPPrefixes/MyPublicIPPrefix",
  ...
}
```

När du skapar ett nytt kluster eller lägger till en ny nodpool använder du slutligen flaggan och skickar `node-public-ip-prefix` prefixets resurs-ID:

```azurecli-interactive
az aks create -g MyResourceGroup3 -n MyManagedCluster -l eastus --enable-node-public-ip --node-public-ip-prefix /subscriptions/<subscription-id>/resourcegroups/MyResourceGroup3/providers/Microsoft.Network/publicIPPrefixes/MyPublicIPPrefix
```

### <a name="locate-public-ips-for-nodes"></a>Hitta offentliga IP-adresser för noder

Du kan hitta offentliga IP-adresser för dina noder på olika sätt:

* Använd Azure CLI-kommandot [az vmss list-instance-public-ips][az-list-ips].
* Använd [PowerShell- eller Bash-kommandon.][vmss-commands] 
* Du kan också visa offentliga IP-adresser i Azure Portal genom att visa instanserna i VM-skalningsuppsättningen.

> [!Important]
> [Nodresursgruppen][node-resource-group] innehåller noderna och deras offentliga IP-adresser. Använd nodresursgruppen när du kör kommandon för att hitta offentliga IP-adresser för dina noder.

```azurecli
az vmss list-instance-public-ips -g MC_MyResourceGroup2_MyManagedCluster_eastus -n YourVirtualMachineScaleSetName
```

## <a name="clean-up-resources"></a>Rensa resurser

I den här artikeln har du skapat ett AKS-kluster som innehåller GPU-baserade noder. För att minska onödiga kostnader kanske du vill ta bort *gpunodepoolen*, eller hela AKS-klustret.

Om du vill ta bort den GPU-baserade nodpoolen använder du kommandot [az aks nodepool delete][az-aks-nodepool-delete] enligt följande exempel:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Om du vill ta bort själva klustret använder du [kommandot az group delete][az-group-delete] för att ta bort AKS-resursgruppen:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

Du kan också ta bort det ytterligare kluster som du skapade för scenariot med offentliga IP-adresser för nodpooler.

```azurecli-interactive
az group delete --name myResourceGroup2 --yes --no-wait
```

## <a name="next-steps"></a>Nästa steg

Läs mer om [systemnodpooler.][use-system-pool]

I den här artikeln har du lärt dig hur du skapar och hanterar flera nodpooler i ett AKS-kluster. Mer information om hur du styr poddar över nodpooler finns [i Metodtips för avancerade schemaläggarfunktioner i AKS.][operator-best-practices-advanced-scheduler]

Information om hur du skapar och använder nodpooler för Windows Server-containrar finns [i Skapa en Windows Server-container i AKS.][aks-windows]

Använd [närhetsplaceringsgrupper][reduce-latency-ppg] för att minska svarstiden för dina AKS-program.

<!-- EXTERNAL LINKS -->
[kubernetes-drain]: https://kubernetes.io/docs/tasks/administer-cluster/safely-drain-node/
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-taint]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#taint
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubernetes-labels]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/
[kubernetes-label-syntax]: https://kubernetes.io/docs/concepts/overview/working-with-objects/labels/#syntax-and-character-set

<!-- INTERNAL LINKS -->
[aks-windows]: windows-container-cli.md
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_get_credentials
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_create
[az-aks-get-upgrades]: /cli/azure/aks?view=azure-cli-latest&preserve-view=true#az_aks_get_upgrades
[az-aks-nodepool-add]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_add
[az-aks-nodepool-list]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_list
[az-aks-nodepool-update]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_update
[az-aks-nodepool-upgrade]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_upgrade
[az-aks-nodepool-scale]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_scale
[az-aks-nodepool-delete]: /cli/azure/aks/nodepool?view=azure-cli-latest&preserve-view=true#az_aks_nodepool_delete
[az-extension-add]: /cli/azure/extension?view=azure-cli-latest&preserve-view=true#az_extension_add
[az-extension-update]: /cli/azure/extension?view=azure-cli-latest&preserve-view=true#az_extension_update
[az-group-create]: /cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_create
[az-group-delete]: /cli/azure/group?view=azure-cli-latest&preserve-view=true#az_group_delete
[az-deployment-group-create]: /cli/azure/deployment/group?view=azure-cli-latest&preserve-view=true#az_deployment_group_create
[gpu-cluster]: gpu-cluster.md
[install-azure-cli]: /cli/azure/install-azure-cli
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[quotas-skus-regions]: quotas-skus-regions.md
[supported-versions]: supported-kubernetes-versions.md
[tag-limitation]: ../azure-resource-manager/management/tag-resources.md
[taints-tolerations]: operator-best-practices-advanced-scheduler.md#provide-dedicated-nodes-using-taints-and-tolerations
[vm-sizes]: ../virtual-machines/sizes.md
[use-system-pool]: use-system-pools.md
[ip-limitations]: ../virtual-network/virtual-network-ip-addresses-overview-arm#standard
[node-resource-group]: faq.md#why-are-two-resource-groups-created-with-aks
[vmss-commands]: ../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine
[az-list-ips]: /cli/azure/vmss?view=azure-cli-latest&preserve-view=true#az_vmss_list_instance_public_ips
[reduce-latency-ppg]: reduce-latency-ppg.md
[public-ip-prefix-benefits]: ../virtual-network/public-ip-address-prefix.md#why-create-a-public-ip-address-prefix
[az-public-ip-prefix-create]: /cli/azure/network/public-ip/prefix?view=azure-cli-latest&preserve-view=true#az_network_public_ip_prefix_create
