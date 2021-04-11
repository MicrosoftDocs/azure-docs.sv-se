---
title: Använda flera noder i Azure Kubernetes service (AKS)
description: Lär dig hur du skapar och hanterar flera Node-pooler för ett kluster i Azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 02/11/2021
ms.openlocfilehash: bb10e2023187c74a9e8b9a2e4c72115841e89a84
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "106552605"
---
# <a name="create-and-manage-multiple-node-pools-for-a-cluster-in-azure-kubernetes-service-aks"></a>Skapa och hantera flera nodpooler för ett kluster i Azure Kubernetes Service (AKS)

I Azure Kubernetes service (AKS) grupperas noderna i samma konfiguration tillsammans i *noder i pooler*. De här noderna innehåller de underliggande virtuella datorerna som kör dina program. Det ursprungliga antalet noder och deras storlek (SKU) definieras när du skapar ett AKS-kluster, vilket skapar en [pool för system-Node][use-system-pool]. För att stödja program som har olika beräknings-eller lagrings krav kan du skapa ytterligare *pooler för användar-noder*. System Node-pooler fungerar som ett primärt syfte att vara värd för kritiska system poddar, till exempel CoreDNS och tunnelfront. Pooler för användar-noder fungerar som ett primärt syfte att vara värd för din applikations poddar. Programpoddar kan dock schemaläggas på system-nodkonfigurationer om du bara vill ha en pool i ditt AKS-kluster. Användar-resurspooler är där du placerar dina programspecifika poddar. Du kan till exempel använda dessa ytterligare resurspooler för användare för att tillhandahålla GPU: er för beräknings intensiva program eller åtkomst till SSD-lagring med höga prestanda.

> [!NOTE]
> Den här funktionen ger bättre kontroll över hur du skapar och hanterar flera noder i pooler. Därför krävs separata kommandon för att skapa/uppdatera/ta bort. Tidigare kluster åtgärder via `az aks create` eller `az aks update` använde managedCluster API och var det enda alternativet att ändra ditt kontroll plan och en enskild Node-pool. Den här funktionen exponerar en separat åtgärds uppsättning för agent-pooler via Agentpoolegenskap-API: et och kräver `az aks nodepool` att kommando uppsättningen används för att köra åtgärder på en enskild Node-pool.

Den här artikeln visar hur du skapar och hanterar flera resurspooler i ett AKS-kluster.

## <a name="before-you-begin"></a>Innan du börjar

Du behöver Azure CLI-versionen 2.2.0 eller senare installerad och konfigurerad. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][install-azure-cli].

## <a name="limitations"></a>Begränsningar

Följande begränsningar gäller när du skapar och hanterar AKS-kluster som stöder flera Node-pooler:

* Se [kvoter, storleks begränsningar för virtuella datorer och regions tillgänglighet i Azure Kubernetes service (AKS)][quotas-skus-regions].
* Du kan ta bort systemnoder, förutsatt att du har en annan adresspool som tar sitt ställe i AKS-klustret.
* Systempooler måste innehålla minst en nod och användar-noder kan innehålla noll eller flera noder.
* AKS-klustret måste använda standard-SKU: n för att använda flera noder, och funktionen stöds inte med Basic SKU-belastningsutjämnare.
* AKS-klustret måste använda skalnings uppsättningar för virtuella datorer för noderna.
* Namnet på en Node-pool får bara innehålla gemena alfanumeriska tecken och måste börja med en gemen bokstav. För Linux-nodkonfigurationer måste längden vara mellan 1 och 12 tecken, och längden måste vara mellan 1 och 6 tecken för Windows-noder.
* Alla noder i pooler måste finnas i samma virtuella nätverk.
* När du skapar flera noder i klustrets skapande tid måste alla Kubernetes-versioner som används av Node-pooler matcha versions uppsättningen för kontroll planet. Detta kan uppdateras efter att klustret har etablerats med hjälp av åtgärder per nod.

## <a name="create-an-aks-cluster"></a>Skapa ett AKS-kluster

> [!Important]
> Om du kör en pool med en enda pool för AKS-klustret i en produktions miljö rekommenderar vi att du använder minst tre noder för Node-poolen.

Kom igång genom att skapa ett AKS-kluster med en enda Node-pool. I följande exempel används kommandot [AZ Group Create][az-group-create] för att skapa en resurs grupp med namnet *myResourceGroup* i regionen *östra* . Ett AKS-kluster med namnet *myAKSCluster* skapas sedan med kommandot [AZ AKS Create][az-aks-create] .

> [!NOTE]
> SKU: n för *Basic* Load Balancer **stöds inte** när du använder flera noder i en pool. Som standard skapas AKS-kluster med SKU: n för *standard* belastnings utjämning från Azure CLI och Azure Portal.

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
> För att säkerställa att klustret fungerar tillförlitligt bör du köra minst två (två) noder i standardnoden, eftersom viktiga system tjänster körs i den här noden.

När klustret är klart använder du kommandot [AZ AKS get-credentials][az-aks-get-credentials] för att hämta autentiseringsuppgifter för klustret för användning med `kubectl` :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster
```

## <a name="add-a-node-pool"></a>Lägga till en nodpool

Klustret som skapades i föregående steg har en pool med flera noder. Nu ska vi lägga till en andra Node-pool med hjälp av kommandot [AZ AKS nodepool Add][az-aks-nodepool-add] . I följande exempel skapas en Node-pool med namnet *mynodepool* som kör *3* noder:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3
```

> [!NOTE]
> Namnet på en Node-pool måste börja med en gemen bokstav och får bara innehålla alfanumeriska tecken. För Linux-nodkonfigurationer måste längden vara mellan 1 och 12 tecken, och längden måste vara mellan 1 och 6 tecken för Windows-noder.

Om du vill se status för dina nodkonfigurationer använder du kommandot [AZ AKS Node pool List][az-aks-nodepool-list] och anger resurs grupp och kluster namn:

```azurecli-interactive
az aks nodepool list --resource-group myResourceGroup --cluster-name myAKSCluster
```

Följande exempel på utdata visar att *mynodepool* har skapats med tre noder i Node-poolen. När AKS-klustret skapades i föregående steg skapades en standard- *nodepool1* med antalet noder *2*.

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
> Om ingen *VmSize* anges när du lägger till en Node-pool, är standard storleken *Standard_D2s_v3* för Windows-nodkonfigurationer och *Standard_DS2_v2* för Linux-adresspooler. Om ingen *OrchestratorVersion* anges används samma version som kontroll planet som standard.

### <a name="add-a-node-pool-with-a-unique-subnet-preview"></a>Lägga till en Node-pool med ett unikt undernät (för hands version)

En arbets belastning kan kräva delning av ett klusters noder i separata pooler för logisk isolering. Den här isoleringen kan stödjas med separata undernät som är dedikerade till varje nods pool i klustret. Detta kan användas för att hantera krav som att det inte är sammanhängande virtuella nätverks adress utrymme som delas mellan noder.

#### <a name="limitations"></a>Begränsningar

* Alla undernät som tilldelats nodepools måste tillhöra samma virtuella nätverk.
* System-poddar måste ha åtkomst till alla noder/poddar i klustret för att tillhandahålla kritiska funktioner som DNS-matchning och Tunneling kubectl-loggar/exec/Port-Forward proxy.
* Om du expanderar ditt VNET när du har skapat klustret måste du uppdatera klustret (utför alla hanterade clster-åtgärder, men noder i noden räknas inte) innan du lägger till ett undernät utanför den ursprungliga CIDR. AKS kommer att tas bort från agenten Lägg till nu, trots att vi ursprungligen tillät den. Om du inte vet hur du ska stämma av kluster filen med ett support ärende. 
* Calico nätverks princip stöds inte. 
* Azure-nätverks principen stöds inte.
* Kube-proxy förväntar sig en enda kontinuerlig CIDR och använder den för tre optmizations. Se det här [K.E.P.](https://github.com/kubernetes/enhancements/tree/master/keps/sig-network/2450-Remove-knowledge-of-pod-cluster-CIDR-from-iptables-rules) och--cluster-CIDR [här](https://kubernetes.io/docs/reference/command-line-tools-reference/kube-proxy/) för mer information. I Azure cni kommer din första nod i ett undernät att ges till Kube-proxy. 

Om du vill skapa en Node-pool med ett dedikerat undernät skickar du under nätets resurs-ID som en extra parameter när du skapar en Node-pool.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 3 \
    --vnet-subnet-id <YOUR_SUBNET_RESOURCE_ID>
```

## <a name="upgrade-a-node-pool"></a>Uppgradera en Node-pool

> [!NOTE]
> Uppgraderings-och skalnings åtgärder i ett kluster eller en nods pool kan inte inträffa samtidigt, om ett försök till ett fel returneras. I stället måste varje åtgärds typ slutföras på mål resursen innan nästa begäran om samma resurs. Läs mer om detta i vår [fel söknings guide](./troubleshooting.md#im-receiving-errors-when-trying-to-upgrade-or-scale-that-state-my-cluster-is-being-upgraded-or-has-failed-upgrade).

Kommandona i det här avsnittet beskriver hur du uppgraderar en enskild viss Node-pool. Relationen mellan att uppgradera Kubernetes-versionen av kontroll planet och Node-poolen beskrivs i [avsnittet nedan](#upgrade-a-cluster-control-plane-with-multiple-node-pools).

> [!NOTE]
> Operativ system avbildnings versionen för Node-poolen är kopplad till Kubernetes-versionen av klustret. Du kan bara hämta uppgraderingar av operativ Systems avbildningar efter en kluster uppgradering.

Eftersom det finns två nodkonfigurationer i det här exemplet måste vi använda [AZ AKS nodepool Upgrade][az-aks-nodepool-upgrade] för att uppgradera en Node-pool. Om du vill se tillgängliga uppgraderingar använder du [AZ AKS get-uppgraderingar][az-aks-get-upgrades]

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Nu ska vi uppgradera *mynodepool*. Använd kommandot [AZ AKS nodepool Upgrade][az-aks-nodepool-upgrade] för att uppgradera Node-poolen, som visas i följande exempel:

```azurecli-interactive
az aks nodepool upgrade \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --kubernetes-version KUBERNETES_VERSION \
    --no-wait
```

Ange status för dina nodkonfigurationer igen med kommandot [AZ AKS Node pool List][az-aks-nodepool-list] . I följande exempel visas att *mynodepool* är i *uppgraderings* läge för att *KUBERNETES_VERSION*:

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

Som bästa praxis bör du uppgradera alla resurspooler i ett AKS-kluster till samma Kubernetes-version. Standard beteendet för `az aks upgrade` är att uppgradera alla resurspooler tillsammans med kontroll planet för att uppnå den här justeringen. Möjligheten att uppgradera enskilda noder i pooler gör att du kan utföra en löpande uppgradering och schemalägga poddar mellan noder för att upprätthålla drift tiden för programmet inom ovannämnda begränsningar.

## <a name="upgrade-a-cluster-control-plane-with-multiple-node-pools"></a>Uppgradera ett kluster kontroll plan med flera noder i pooler

> [!NOTE]
> Kubernetes använder standard versions schema för [semantisk versions hantering](https://semver.org/) . Versions numret uttrycks som *x. y. z*, där *x* är huvud versionen, *y* är den lägre versionen och *z* är korrigerings versionen. I version *1.12.6* 1 är till exempel den högre versionen, 12 är den lägre versionen och 6 är korrigerings versionen. Kubernetes-versionen av kontroll planet och den första nodens resurspool anges när klustret skapas. Alla ytterligare noder i pooler har Kubernetes-versionen när de läggs till i klustret. Kubernetes-versionerna kan variera mellan olika resurspooler samt mellan en Node-pool och kontroll planet.

Ett AKS-kluster har två kluster resurs objekt med associerade Kubernetes-versioner.

1. En kluster kontroll plan Kubernetes-version.
2. En Node-pool med en Kubernetes-version.

Ett kontroll plan mappar till en eller flera Node-pooler. Beteendet för en uppgraderings åtgärd beror på vilket Azure CLI-kommando som används.

Att uppgradera ett kontroll plan för AKS kräver att du använder `az aks upgrade` . Det här kommandot uppgraderar kontroll Plans versionen och alla noder i klustret.

Att utfärda `az aks upgrade` kommandot med `--control-plane-only` flaggan uppgraderar bara kluster kontroll planet. Ingen av de associerade noderna i klustret har ändrats.

Du måste använda för att uppgradera enskilda noder `az aks nodepool upgrade` . Det här kommandot uppgraderar bara målnoden med den angivna Kubernetes-versionen

### <a name="validation-rules-for-upgrades"></a>Verifierings regler för uppgraderingar

Giltiga Kubernetes-uppgraderingar för ett klusters kontroll plan och nodkonfigurationer verifieras av följande regel uppsättningar.

* Regler för giltiga versioner för att uppgradera Node-pooler:
   * Node-versionen måste ha samma *huvud* version som kontroll planet.
   * Den *lägre* versionen av Node-poolen måste ligga inom två *lägre* versioner av kontroll Plans versionen.
   * Node-poolens version får inte vara större än kontroll `major.minor.patch` versionen.

* Regler för att skicka en uppgraderings åtgärd:
   * Du kan inte nedgradera kontroll planet eller en Kubernetes-version för Node-poolen.
   * Om en Kubernetes version av en nod inte anges, beror beteendet på vilken klient som används. Deklaration i Resource Manager-mallar går tillbaka till den befintliga versionen som definierats för Node-poolen om den används, om ingen är inställd på kontroll Plans versionen används för att gå vidare.
   * Du kan antingen uppgradera eller skala ett kontroll plan eller en Node-pool vid en viss tidpunkt, du kan inte skicka flera åtgärder på ett enda kontroll plan eller en resurs för en resurspool samtidigt.

## <a name="scale-a-node-pool-manually"></a>Skala en adresspool manuellt

När ditt programs arbets belastnings behov ändras kan du behöva skala antalet noder i en Node-pool. Antalet noder kan skalas upp eller ned.

<!--If you scale down, nodes are carefully [cordoned and drained][kubernetes-drain] to minimize disruption to running applications.-->

Om du vill skala antalet noder i en Node-pool använder du kommandot [AZ AKS Node pool Scale][az-aks-nodepool-scale] . I följande exempel skalas antalet noder i *mynodepool* till *5*:

```azurecli-interactive
az aks nodepool scale \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name mynodepool \
    --node-count 5 \
    --no-wait
```

Ange status för dina nodkonfigurationer igen med kommandot [AZ AKS Node pool List][az-aks-nodepool-list] . I följande exempel visas att *mynodepool* är i *skalnings* tillstånd med ett nytt antal *5* noder:

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

Det tar några minuter för skalnings åtgärden att slutföras.

## <a name="scale-a-specific-node-pool-automatically-by-enabling-the-cluster-autoscaler"></a>Skala en speciell Node-pool automatiskt genom att aktivera kluster autoskalning

AKS erbjuder en separat funktion för att automatiskt skala Node-pooler med en funktion som kallas för [kluster autoskalning](cluster-autoscaler.md). Den här funktionen kan aktive ras per adresspool med unika minimi-och Max skalnings antal per Node-pool. Lär dig hur du [använder kluster autoskalning per Node-pool](cluster-autoscaler.md#use-the-cluster-autoscaler-with-multiple-node-pools-enabled).

## <a name="delete-a-node-pool"></a>Ta bort en Node-pool

Om du inte längre behöver en pool kan du ta bort den och ta bort de underliggande VM-noderna. Om du vill ta bort en Node-pool använder du kommandot [AZ AKS Node pool Delete][az-aks-nodepool-delete] och anger namnet på noden. I följande exempel tar bort *mynoodepool* som skapats i föregående steg:

> [!CAUTION]
> Det finns inga återställnings alternativ för data förlust som kan uppstå när du tar bort en Node-pool. Om poddar inte kan schemaläggas på andra nodkonfigurationer är dessa program inte tillgängliga. Se till att du inte tar bort en resurspool när det inte finns säkerhets kopior av data i användnings program eller om du inte kan köra dem på andra noder i klustret.

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name mynodepool --no-wait
```

I följande exempel visas utdata från kommandot [AZ AKS Node pool List][az-aks-nodepool-list] som visar att *Mynodepool* är i *borttagnings* läge:

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

Det tar några minuter att ta bort noderna och Node-poolen.

## <a name="specify-a-vm-size-for-a-node-pool"></a>Ange en VM-storlek för en Node-pool

I föregående exempel för att skapa en Node-pool användes en standard storlek för virtuella datorer för de noder som skapades i klustret. Ett vanligt scenario är att du kan skapa nodkonfigurationer med olika storlekar och kapaciteter för virtuella datorer. Du kan till exempel skapa en noduppsättning som innehåller noder med stora mängder CPU eller minne, eller en Node-pool som tillhandahåller GPU-stöd. I nästa steg ska du [använda utsmaker och tolererar](#setting-nodepool-taints) för att meddela Kubernetes Scheduler hur du begränsar åtkomsten till poddar som kan köras på dessa noder.

I följande exempel skapar du en GPU-baserad Node-pool som använder *Standard_NC6* VM-storlek. De här virtuella datorerna drivs av NVIDIA Tesla K80-kortet. Information om tillgängliga VM-storlekar finns i [storlekar för virtuella Linux-datorer i Azure][vm-sizes].

Skapa en Node-pool med kommandot [AZ AKS Node pool Add][az-aks-nodepool-add] . Den här gången anger du namnet *gpunodepool* och använder `--node-vm-size` parametern för att ange *Standard_NC6* storlek:

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name gpunodepool \
    --node-count 1 \
    --node-vm-size Standard_NC6 \
    --no-wait
```

I följande exempel visas utdata från kommandot [AZ AKS Node pool List][az-aks-nodepool-list] som visar att *gpunodepool* *skapar* noder med den angivna *VmSize*:

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

Det tar några minuter innan *gpunodepool* har skapats.

## <a name="specify-a-taint-label-or-tag-for-a-node-pool"></a>Ange en smak, etikett eller tagg för en Node-pool

### <a name="setting-nodepool-taints"></a>Ställa in nodepool-smakar

När du skapar en Node-pool kan du lägga till utsmaker, etiketter eller taggar i den Node-poolen. När du lägger till en utsmak, etikett eller tagg, får alla noder i den noden även denna smak, etikett eller tagg.

Om du vill skapa en Node-pool med en-bismak använder du [AZ AKS nodepool Add][az-aks-nodepool-add]. Ange namnet *taintnp* och Använd `--node-taints` parametern för att ange *SKU = GPU: noschema* för bismaken.

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
> En bismak kan bara ställas in för Node-pooler när en resurspool skapas.

I följande exempel utdata från kommandot [AZ AKS nodepool List][az-aks-nodepool-list] visas att *taintnp* *skapar* noder med angivna *nokvarhållning*:

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

Informationen om bismaken visas i Kubernetes för hantering av schema regler för noder. Kubernetes Scheduler kan använda utsmakar och tolereras för att begränsa vilka arbets belastningar som kan köras på noder.

* En **bismak** tillämpas på en nod som endast anger att vissa poddar kan schemaläggas på dem.
* En **tolererande** tillämpas sedan på en pod som gör det möjligt för dem att *tolerera* en nods smak.

Mer information om hur du använder avancerade Kubernetes schemalagda funktioner finns i [metod tips för avancerade funktioner i Schemaläggaren i AKS][taints-tolerations]

I föregående steg har du tillämpat *SKU = GPU: Noschema* -smak när du skapade din Node-pool. Följande grundläggande exempel på YAML-manifest använder en tolererare för att tillåta att Schemaläggaren för Kubernetes kör en NGINX-Pod på en nod i den noden.

Skapa en fil med namnet `nginx-toleration.yaml` och kopiera i följande exempel yaml:

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

Schemalägg Pod med `kubectl apply -f nginx-toleration.yaml` kommandot:

```console
kubectl apply -f nginx-toleration.yaml
```

Det tar några sekunder att schemalägga Pod och hämta NGINX-avbildningen. Använd kommandot [kubectl beskriver Pod][kubectl-describe] för att Visa Pod-status. I följande komprimerade exempel utdata visas *SKU = GPU: Noschema* -tolerera används. I avsnittet Events har Scheduler tilldelat Pod till *AKS-taintnp-28993262-vmss000000-* noden:

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

Endast poddar som har den här tolereraren som används kan schemaläggas på noder i *taintnp*. Andra pod skulle schemaläggas i *nodepool1* Node-pool. Om du skapar ytterligare noder kan du använda ytterligare utsmakar och tolererar för att begränsa vilka poddar som kan schemaläggas för dessa resurs resurser.

### <a name="setting-nodepool-labels"></a>Ange nodepool-etiketter

Du kan också lägga till etiketter i en Node-pool när du skapar en resurspool. Etiketter som ställts in i Node-poolen läggs till i varje nod i Node-poolen. Dessa [etiketter visas i Kubernetes][kubernetes-labels] för hantering av schema regler för noder.

Om du vill skapa en Node-pool med en etikett använder du [AZ AKS nodepool Add][az-aks-nodepool-add]. Ange namnet *labelnp* och Använd `--labels` parametern för att ange *avd = IT* och *CostCenter = 9999* för etiketter.

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
> Det går bara att ange etiketten för Node-pooler när en resurspool skapas. Etiketter måste också vara ett nyckel/värde-par och ha en [giltig syntax][kubernetes-label-syntax].

I följande exempel utdata från kommandot [AZ AKS nodepool List][az-aks-nodepool-list] visas att *labelnp* *skapar* noder med angivet *nodeLabels*:

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

### <a name="setting-nodepool-azure-tags"></a>Ange nodepool Azure-Taggar

Du kan använda en Azure-tagg för resurspooler i ditt AKS-kluster. Taggar som tillämpas på en Node-pool tillämpas på varje nod i Node-poolen och bevaras genom uppgraderingar. Taggar används också för nya noder som läggs till i en Node-pool vid skalnings åtgärder. Att lägga till en tagg kan hjälpa till med uppgifter som princip spårning eller kostnads uppskattning.

Azure-Taggar har nycklar som inte är Skift läges känsliga för åtgärder, t. ex. När en tagg hämtas genom att söka i nyckeln. I det här fallet kommer en tagg med den aktuella nyckeln att uppdateras eller hämtas oavsett Skift läge. Tagg värden är Skift läges känsliga.

I AKS, om flera taggar har angetts med identiska nycklar men olika Skift läge, är taggen som används den första i alfabetisk ordning. Till exempel `{"Key1": "val1", "kEy1": "val2", "key1": "val3"}` resulterar i `Key1` och `val1` ställs in.

Skapa en Node-pool med hjälp av [AZ AKS nodepool Add][az-aks-nodepool-add]. Ange namnet *tagnodepool* och Använd `--tag` parametern för att ange *avd = IT* och *CostCenter = 9999* för taggar.

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
> Du kan också använda `--tags` parametern när du använder kommandot [AZ AKS nodepool Update][az-aks-nodepool-update] och när klustret skapas. När klustret skapas `--tags` använder parametern taggen på den första noden som skapats med klustret. Alla taggnamn måste följa de begränsningar som [används vid användning av taggar för att organisera dina Azure-resurser][tag-limitation]. Uppdatering av en Node-pool med `--tags` parametern uppdaterar eventuella befintliga tagg värden och lägger till nya taggar. Om till exempel din Node-pool hade *avd = IT* och *CostCenter = 9999* för taggar och du har uppdaterat den med *team = dev* och *CostCenter = 111* for taggar kan du nodepool ha *avd = IT*, *CostCenter = 111* och *team = dev* för taggar.

I följande exempel utdata från kommandot [AZ AKS nodepool List][az-aks-nodepool-list] visas att *tagnodepool* *skapar* noder med den angivna *taggen*:

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

## <a name="manage-node-pools-using-a-resource-manager-template"></a>Hantera Node-pooler med en Resource Manager-mall

När du använder en Azure Resource Manager mall för att skapa och hanterade resurser, kan du vanligt vis uppdatera inställningarna i mallen och distribuera om för att uppdatera resursen. Med Node-pooler i AKS kan den ursprungliga nodens profil för poolen inte uppdateras när AKS-klustret har skapats. Det innebär att du inte kan uppdatera en befintlig Resource Manager-mall, göra en ändring i Node-poolerna och distribuera om. I stället måste du skapa en separat Resource Manager-mall som endast uppdaterar Node-poolerna för ett befintligt AKS-kluster.

Skapa en mall som `aks-agentpools.json` och klistra in följande exempel manifest. I den här exempel mal len konfigureras följande inställningar:

* Uppdaterar *Linux* Node-poolen med namnet *myagentpool* för att köra tre noder.
* Ställer in noderna i Node-poolen att köra Kubernetes-version *1.15.7*.
* Definierar nodens storlek som *Standard_DS2_v2*.

Redigera de här värdena som behövs för att uppdatera, lägga till eller ta bort noder i pooler efter behov:

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

Distribuera den här mallen med kommandot [AZ Deployment Group Create][az-deployment-group-create] , som du ser i följande exempel. Du uppmanas att ange det befintliga AKS-kluster namnet och platsen:

```azurecli-interactive
az deployment group create \
    --resource-group myResourceGroup \
    --template-file aks-agentpools.json
```

> [!TIP]
> Du kan lägga till en tagg i Node-poolen genom att lägga till egenskapen *tag* i mallen, som du ser i följande exempel.
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

Det kan ta några minuter att uppdatera ditt AKS-kluster beroende på de inställningar och åtgärder för Node-poolen som du definierar i Resource Manager-mallen.

## <a name="assign-a-public-ip-per-node-for-your-node-pools"></a>Tilldela en offentlig IP-adress per nod för dina nodkonfigurationer

AKS-noder kräver inte sina egna offentliga IP-adresser för kommunikation. Scenarier kan dock kräva att noder i en Node-pool tar emot sina egna dedikerade offentliga IP-adresser. Ett vanligt scenario är för spel arbets belastningar, där en konsol behöver upprätta en direkt anslutning till en virtuell dator i molnet för att minimera hopp. Det här scenariot kan uppnås på AKS med hjälp av nodens offentliga IP-adress.

Börja med att skapa en ny resurs grupp.

```azurecli-interactive
az group create --name myResourceGroup2 --location eastus
```

Skapa ett nytt AKS-kluster och koppla en offentlig IP-adress för noderna. Varje nod i Node-poolen får en unik offentlig IP-adress. Du kan kontrol lera detta genom att titta på instanserna för skalnings uppsättning för virtuella datorer.

```azurecli-interactive
az aks create -g MyResourceGroup2 -n MyManagedCluster -l eastus  --enable-node-public-ip
```

För befintliga AKS-kluster kan du också lägga till en ny Node-pool och koppla en offentlig IP-adress för noderna.

```azurecli-interactive
az aks nodepool add -g MyResourceGroup2 --cluster-name MyManagedCluster -n nodepool2 --enable-node-public-ip
```

### <a name="use-a-public-ip-prefix"></a>Använd ett offentligt IP-prefix

Det finns ett antal [fördelar med att använda ett offentligt IP-prefix][public-ip-prefix-benefits]. AKS stöder användning av adresser från ett befintligt offentligt IP-prefix för dina noder genom att skicka resurs-ID: t med flaggan `node-public-ip-prefix` när du skapar ett nytt kluster eller lägger till en adresspool.

Börja med att skapa ett offentligt IP-prefix med [AZ Network Public-IP prefix Create][az-public-ip-prefix-create]:

```azurecli-interactive
az network public-ip prefix create --length 28 --location eastus --name MyPublicIPPrefix --resource-group MyResourceGroup3
```

Visa utdata och anteckna `id` för prefixet:

```output
{
  ...
  "id": "/subscriptions/<subscription-id>/resourceGroups/myResourceGroup3/providers/Microsoft.Network/publicIPPrefixes/MyPublicIPPrefix",
  ...
}
```

När du skapar ett nytt kluster eller lägger till en ny Node-pool använder du slutligen `node-public-ip-prefix` -flaggan och skickar i prefixets resurs-ID:

```azurecli-interactive
az aks create -g MyResourceGroup3 -n MyManagedCluster -l eastus --enable-node-public-ip --node-public-ip-prefix /subscriptions/<subscription-id>/resourcegroups/MyResourceGroup3/providers/Microsoft.Network/publicIPPrefixes/MyPublicIPPrefix
```

### <a name="locate-public-ips-for-nodes"></a>Hitta offentliga IP-adresser för noder

Du kan hitta de offentliga IP-adresserna för dina noder på olika sätt:

* Använd Azure CLI-kommandot [AZ VMSS List-instance-Public-IP-adresser][az-list-ips].
* Använd [PowerShell-eller bash-kommandon][vmss-commands]. 
* Du kan också Visa offentliga IP-adresser i Azure Portal genom att Visa instanserna i den virtuella datorns skal uppsättning.

> [!Important]
> [Resurs gruppen för noden][node-resource-group] innehåller noderna och deras offentliga IP-adresser. Använd resurs gruppen nod när du kör kommandon för att hitta de offentliga IP-adresserna för dina noder.

```azurecli
az vmss list-instance-public-ips -g MC_MyResourceGroup2_MyManagedCluster_eastus -n YourVirtualMachineScaleSetName
```

## <a name="clean-up-resources"></a>Rensa resurser

I den här artikeln har du skapat ett AKS-kluster som innehåller GPU-baserade noder. För att minska onödig kostnad kanske du vill ta bort *gpunodepool* eller hela AKS-klustret.

Om du vill ta bort GPU-baserad Node-pool använder du kommandot [AZ AKS nodepool Delete][az-aks-nodepool-delete] , som visas i följande exempel:

```azurecli-interactive
az aks nodepool delete -g myResourceGroup --cluster-name myAKSCluster --name gpunodepool
```

Ta bort själva klustret genom att använda kommandot [AZ Group Delete][az-group-delete] för att ta bort resurs gruppen AKS:

```azurecli-interactive
az group delete --name myResourceGroup --yes --no-wait
```

Du kan också ta bort det ytterligare kluster som du skapade för den offentliga IP-adressen för ett scenario med Node-pooler.

```azurecli-interactive
az group delete --name myResourceGroup2 --yes --no-wait
```

## <a name="next-steps"></a>Nästa steg

Läs mer om [system Node-pooler][use-system-pool].

I den här artikeln har du lärt dig hur du skapar och hanterar flera Node-pooler i ett AKS-kluster. Mer information om hur du styr poddar över Node-pooler finns i [metod tips för avancerade funktioner i Schemaläggaren i AKS][operator-best-practices-advanced-scheduler].

Information om hur du skapar och använder Windows Server container Node-pooler finns i [skapa en Windows Server-behållare i AKS][aks-windows].

Använd [närhets placerings grupper][reduce-latency-ppg] för att minska svars tiden för dina AKS-program.

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
