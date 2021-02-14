---
title: Använda den automatiska skalnings tjänsten för kluster i Azure Kubernetes service (AKS)
description: Lär dig hur du använder kluster autoskalning för att automatiskt skala klustret så att det uppfyller program kraven i ett Azure Kubernetes service-kluster (AKS).
services: container-service
ms.topic: article
ms.date: 07/18/2019
ms.openlocfilehash: c0564dc3b394b4a65e70a487b6f6989cb306bdda
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373257"
---
# <a name="automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>Skala ett kluster automatiskt för att uppfylla programbehov i Azure Kubernetes Service (AKS)

För att hålla dig uppdaterad med program krav i Azure Kubernetes service (AKS) kan du behöva justera antalet noder som kör arbets belastningarna. Komponenten för kluster autoskalning kan bevaka poddar i klustret som inte kan schemaläggas på grund av resurs begränsningar. När problem upptäcks ökas antalet noder i en Node-pool för att uppfylla programmets krav. Noderna kontrol leras också regelbundet för att det inte ska finnas någon poddar, med antalet noder som sedan kan minskas efter behov. Med den här möjligheten att automatiskt skala upp eller ned antalet noder i AKS-klustret kan du köra ett effektivt, kostnads effektivt kluster.

Den här artikeln visar hur du aktiverar och hanterar klustrets autoskalning i ett AKS-kluster.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln kräver att du kör Azure CLI-version 2.0.76 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="about-the-cluster-autoscaler"></a>Om klustrets autoskalning

För att justera för att ändra program krav, till exempel mellan arbets dagar och kväll eller på en helg, behöver kluster ofta ett sätt att skala automatiskt. AKS-kluster kan skalas på ett av två sätt:

* Den **automatiska skalnings** funktionen i klustret söker efter poddar som inte kan schemaläggas på noder på grund av resurs begränsningar. Klustret ökar sedan automatiskt antalet noder.
* Den **horisontella Pod-autoskalning** använder mått servern i ett Kubernetes-kluster för att övervaka resurs behovet för poddar. Om ett program behöver fler resurser ökas antalet poddar automatiskt för att uppfylla behovet.

![Autoskalning av klustret och horisontell Pod-autoskalning fungerar ofta tillsammans för att stödja nödvändiga program krav](media/autoscaler/cluster-autoscaler.png)

Både den automatiska skalnings tjänsten för Pod och klustret kan också minska antalet poddar och noder efter behov. Klustrets autoskalning minskar antalet noder när det finns outnyttjad kapacitet under en tids period. Poddar på en nod som ska tas bort av klustrets autoskalning är säkert schemalagt i klustret. Klustrets autoskalning kan inte skalas ned om poddar inte kan flyttas, till exempel i följande situationer:

* En POD skapas direkt och backas inte upp av ett kontroll enhets objekt, till exempel en distribution eller replik uppsättning.
* En POD störnings budget (PDB) är för begränsad och tillåter inte att antalet poddar kan falla under ett visst tröskelvärde.
* En POD använder Node-väljare eller anti-tillhörighet som inte kan användas om de är schemalagda på en annan nod.

För mer information om hur klustrets autoskalning inte kan skalas ned, se [vilka typer av poddar kan förhindra att klustret autoskalning tar bort en nod?][autoscaler-scaledown]

Klustrets autoskalning använder start parametrar för saker som tidsintervall mellan skalnings händelser och resurs trösklar. Mer information om vilka parametrar som används av klustret finns i [använda autoskalning-profilen](#using-the-autoscaler-profile).

De automatiska skalningarna i kluster och horisontella Pod kan fungera tillsammans och båda är ofta distribuerade i ett kluster. När den här funktionen kombineras, fokuserar den horisontella Pod autoskalning på att köra antalet poddar som krävs för att uppfylla programmets krav. Klustrets autoskalning fokuserar på att köra antalet noder som krävs för att stödja den schemalagda poddar.

> [!NOTE]
> Manuell skalning är inaktive rad när du använder klustret autoskalning. Låt klustret autoscaler fastställa antalet noder som krävs. Om du vill skala klustret manuellt [inaktiverar du klustrets autoskalning](#disable-the-cluster-autoscaler).

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>Skapa ett AKS-kluster och aktivera kluster autoskalning

Om du behöver skapa ett AKS-kluster använder du kommandot [AZ AKS Create][az-aks-create] . Om du vill aktivera och konfigurera klustrets autoskalning på nodens resurspool använder du parametern och `--enable-cluster-autoscaler` anger en nod `--min-count` och `--max-count` .

> [!IMPORTANT]
> Klustrets autoskalning är en Kubernetes-komponent. Även om AKS-klustret använder en skalnings uppsättning för virtuella datorer för noderna, ska du inte manuellt aktivera eller redigera inställningarna för skalnings uppsättning autoskalning i Azure Portal eller med hjälp av Azure CLI. Låt Kubernetes-klustret hantera de nödvändiga skalnings inställningarna. Mer information finns i [kan jag ändra AKS-resurserna i noden resurs grupp?][aks-faq-node-resource-group]

I följande exempel skapas ett AKS-kluster med en pool med en enda nod som backas upp av en skalnings uppsättning för virtuella datorer. Det aktiverar också klustrets autoskalning på nodens nod för klustret och anger minst *1* och högst *3* noder:

```azurecli-interactive
# First create a resource group
az group create --name myResourceGroup --location eastus

# Now create the AKS cluster and enable the cluster autoscaler
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --vm-set-type VirtualMachineScaleSets \
  --load-balancer-sku standard \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

Det tar några minuter att skapa klustret och konfigurera inställningarna för den automatiska skalnings inställningen i klustret.

## <a name="update-an-existing-aks-cluster-to-enable-the-cluster-autoscaler"></a>Uppdatera ett befintligt AKS-kluster så att klustret autoskalning aktive ras

Använd kommandot [AZ AKS Update][az-aks-update] för att aktivera och konfigurera klustrets autoskalning i Node-poolen för det befintliga klustret. Använd `--enable-cluster-autoscaler` parametern och ange en nod `--min-count` och `--max-count` .

> [!IMPORTANT]
> Klustrets autoskalning är en Kubernetes-komponent. Även om AKS-klustret använder en skalnings uppsättning för virtuella datorer för noderna, ska du inte manuellt aktivera eller redigera inställningarna för skalnings uppsättning autoskalning i Azure Portal eller med hjälp av Azure CLI. Låt Kubernetes-klustret hantera de nödvändiga skalnings inställningarna. Mer information finns i [kan jag ändra AKS-resurserna i noden resurs grupp?][aks-faq-node-resource-group]

I följande exempel uppdateras ett befintligt AKS-kluster för att aktivera den automatiska skalnings funktionen för klustret i nodens resurspool för klustret och ställer in minst *1* och högst *tre* noder:

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

Det tar några minuter att uppdatera klustret och konfigurera inställningarna för den automatiska skalnings inställningen i klustret.

## <a name="change-the-cluster-autoscaler-settings"></a>Ändra inställningarna för kluster autoskalning

> [!IMPORTANT]
> Om du har flera resurspooler i ditt AKS-kluster går du vidare till [avsnittet autoskalning med flera agenter](#use-the-cluster-autoscaler-with-multiple-node-pools-enabled). Kluster med flera agenter kräver `az aks nodepool` att kommando uppsättningen använder kommandot för att ändra egenskaper för Node-pooler i stället för `az aks` .

I föregående steg för att skapa ett AKS-kluster eller uppdatera en befintlig Node-pool, angavs det lägsta antalet noder för klustrets Autoskala till *1*, och det maximala antalet noder hade angetts till *3*. När dina program kräver ändringar kan du behöva justera antalet noder i den automatiska skalnings tjänsten för klustret.

Om du vill ändra antalet noder använder du kommandot [AZ AKS Update][az-aks-update] .

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

Exemplet ovan uppdaterar kluster autoskalning på den enskild Node-poolen i *myAKSCluster* till minst *1* och högst *5* noder.

> [!NOTE]
> Med automatisk skalning i klustret kan du skala beslut baserat på de lägsta och högsta antal som angetts för varje nod, men det tvingar inte dem efter uppdatering av minsta eller högsta antal. Om du till exempel anger ett minsta antal på 5 när det aktuella antalet noder är 3, skalar inte den poolen direkt till upp till 5. Om det minsta antalet i Node-poolen har ett värde som är högre än det aktuella antalet noder, kommer de nya inställningarna för min eller Max att respekteras när det finns tillräckligt många unschedulable-poddar som skulle kräva 2 nya ytterligare noder och utlösa en autoskalning-händelse. Efter skalnings händelsen respekteras de nya antal gränserna.

Övervaka prestanda för dina program och tjänster och justera antalet noder i den automatiska skalnings tjänsten för klustret så att de matchar de nödvändiga prestanda.

## <a name="using-the-autoscaler-profile"></a>Använda den automatiska skalnings profilen

Du kan också konfigurera mer detaljerad information om klustrets autoskalning genom att ändra standardvärdena i den globala profilen för autoskalning i klustret. Till exempel inträffar en händelsen skala ned efter att noderna har utnyttjats efter 10 minuter. Om du har arbets belastningar som kördes var 15: e minut kanske du vill ändra profilen för autoskalning för att skala ned under använda noder efter 15 eller 20 minuter. När du aktiverar klustret autoskalning används en standard profil om du inte anger andra inställningar. Klustrets profil för autoskalning har följande inställningar som du kan uppdatera:

| Inställning                          | Beskrivning                                                                              | Standardvärde |
|----------------------------------|------------------------------------------------------------------------------------------|---------------|
| genomsökning – intervall                    | Hur ofta klustret utvärderas för att skala upp eller ned                                    | 10 sekunder    |
| skala ned-fördröjning – efter-tillägg       | Hur lång tid det tar att skala ned utvärderingen återupptas                               | 10 minuter    |
| skala ned-fördröjning – efter-borttagning    | Hur lång tid efter att noden har tagits bort återupptar utvärderingen                          | genomsökning – intervall |
| skala ned-fördröjning – efter-Failure   | Hur lång tid det tar för fel på att skala ned utvärderings versionen återupptas                     | 3 minuter     |
| skalning – inte nödvändig-tid         | Hur länge en nod ska vara onödiga innan den kan skalas ned                  | 10 minuter    |
| skalar ned-oläst, tid          | Hur länge en oläsbar nod ska vara onödiga innan den kan skalas ned         | 20 minuter    |
| skalning – användning – tröskel | Nodens användnings nivå, definieras som summan av de begärda resurserna dividerat med kapaciteten, under vilken en nod kan övervägas för skalning nedåt | 0,5 |
| Max--Termination-s     | Maximalt antal sekunder som kluster autoskalning väntar på pod avslutning vid försök att skala ned en nod | 600 sekunder   |
| balans-liknande-Node-Groups      | Identifierar liknande noders pooler och balanserar antalet noder mellan dem                 | falskt         |
| utökningspilen                         | Typ av [Expander](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-expanders) för resurspool som ska användas vid skalning. Möjliga värden: `most-pods` , `random` , `least-waste` , `priority` | slumpmässig | 
| hoppa över-noder-med-lokal lagring    | Om sant kluster autoskalning aldrig tar bort noder med poddar med lokal lagring, till exempel EmptyDir eller HostPath | true |
| hoppa över-noder-med-system-poddar      | Om sant kluster autoskalning aldrig tar bort noder med poddar från Kube-system (förutom DaemonSet eller spegel poddar) | true | 
| Max-tom-Mass-ta bort            | Maximalt antal tomma noder som kan tas bort samtidigt                       | 10 noder      |
| New-Pod-Scale-in-Delay           | För scenarier som burst/batch-skala där du inte vill att CA ska fungera innan Kubernetes Scheduler kan schemalägga alla poddar, kan du se till att CA: n ignorerar oplanerad poddar innan de är en viss ålder.                                                                                                                | 0 sekunder    |
| Max-total-Unready-procent     | Högsta procent andel olästa noder i klustret. När den här procent andelen överskrids stoppar CA åtgärder | 45 % |
| Max-nod-etablering-tid          | Längsta tid som autoskalning väntar på att en nod ska tillhandahållas                           | 15 minuter    |   
| OK-totalt-ej skrivskyddat-antal           | Antal tillåtna olästa noder, oberoende av Max-total-Unready-procent            | tre noder       |

> [!IMPORTANT]
> Klustrets profil för autoskalning påverkar alla resurspooler som använder klustrets autoskalning. Det går inte att ange en autoskalning-profil per Node-pool.
>
> Klustrets profil för autoskalning kräver version *2.11.1* eller senare av Azure CLI. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

### <a name="set-the-cluster-autoscaler-profile-on-an-existing-aks-cluster"></a>Ställ in klustrets profil för autoskalning i ett befintligt AKS-kluster

Använd kommandot [AZ AKS Update][az-aks-update-preview] med parametern *cluster-autoscaler – profil* för att ställa in klustrets autoskalning-profil i klustret. I följande exempel konfigureras inställningen för genomsöknings intervallet som 30 s i profilen.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile scan-interval=30s
```

När du aktiverar klustrets automatiska skalning på nodkonfigurationer i klustret, använder dessa kluster även profilen för autoskalning i klustret. Exempel:

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name mynodepool \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

> [!IMPORTANT]
> När du ställer in klustrets profil för autoskalning börjar alla befintliga noder med den aktiverade klustret att använda profilen omedelbart.

### <a name="set-the-cluster-autoscaler-profile-when-creating-an-aks-cluster"></a>Ställ in klustrets profil för autoskalning när du skapar ett AKS-kluster

Du kan också använda *klustret-autoskalning-profil* parameter när du skapar klustret. Exempel:

```azurecli-interactive
az aks create \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --node-count 1 \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3 \
  --cluster-autoscaler-profile scan-interval=30s
```

Kommandot ovan skapar ett AKS-kluster och definierar genomsöknings intervallet som 30 sekunder för den automatiska skalnings profilen i klustret. Kommandot aktiverar också klustrets autoskalning på den första noden, anger det lägsta antalet noder till 1 och det maximala antalet noder till 3.

### <a name="reset-cluster-autoscaler-profile-to-default-values"></a>Återställ profil för automatiskt skalnings profil till standardvärdena

Använd kommandot [AZ AKS Update][az-aks-update-preview] för att återställa klustrets autoskalning-profil i klustret.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile ""
```

## <a name="disable-the-cluster-autoscaler"></a>Inaktivera klustrets autoskalning

Om du inte längre vill använda klustrets autoskalning kan du inaktivera det med kommandot [AZ AKS Update][az-aks-update-preview] och ange `--disable-cluster-autoscaler` parametern. Noderna tas inte bort när klustrets autoskalning är inaktive rad.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

Du kan skala klustret manuellt när du har inaktiverat kluster autoskalning med kommandot [AZ AKS Scale][az-aks-scale] . Om du använder den vågräta Pod autoskalning, fortsätter funktionen att köras med den automatiska skalnings funktionen för klustret inaktive rad, men poddar kan inte schemaläggas om alla resurs resurser används.

## <a name="re-enable-a-disabled-cluster-autoscaler"></a>Återaktivera en inaktive rad kluster autoskalning

Om du vill återaktivera klustrets autoskalning i ett befintligt kluster kan du återaktivera det med kommandot [AZ AKS Update][az-aks-update-preview] och ange `--enable-cluster-autoscaler` `--min-count` parametrarna, och `--max-count` .

## <a name="retrieve-cluster-autoscaler-logs-and-status"></a>Hämta loggar och status för kluster för autoskalning

För att diagnostisera och felsöka autoskalning-händelser kan loggar och status hämtas från autoskalning-tillägget.

AKS hanterar klustrets automatiska skalning åt dig och kör det i det hanterade kontroll planet. Du kan aktivera noden kontroll plan för att se loggar och åtgärder från CA.

Följ dessa steg om du vill konfigurera loggar som ska överföras från klustrets autoskalning till Log Analytics.

1. Konfigurera en regel för resurs loggar för att skicka ett push-kluster med automatiska skalnings loggar till Log Analytics. [Instruktioner beskrivs här][aks-view-master-logs], se till att du markerar kryss rutan `cluster-autoscaler` när du väljer alternativ för "loggar".
1. Välj avsnittet "loggar" i klustret via Azure Portal.
1. Mata in följande exempel fråga i Log Analytics:

```
AzureDiagnostics
| where Category == "cluster-autoscaler"
```

Du bör se loggar som liknar följande exempel så länge det finns loggar att hämta.

![Log Analytics loggar](media/autoscaler/autoscaler-logs.png)

Klustrets autoskalning kommer också att skriva ut hälso status till ett `configmap` namngivet `cluster-autoscaler-status` . Kör följande kommando för att hämta loggarna `kubectl` . En hälso status rapporteras för varje noduppsättning som kon figurer ATS med klustrets autoskalning.

```
kubectl get configmap -n kube-system cluster-autoscaler-status -o yaml
```

Om du vill veta mer om vad som loggas från autoskalning läser du vanliga frågor och svar om [Kubernetes/autoskalning GitHub-projektet][kubernetes-faq].

## <a name="use-the-cluster-autoscaler-with-multiple-node-pools-enabled"></a>Använd kluster autoskalning med flera noder aktiverade

Klustrets autoskalning kan användas tillsammans med [flera noder][aks-multiple-node-pools] aktiverade. Följ det dokumentet för att lära dig hur du aktiverar flera resurspooler och lägger till ytterligare resurspooler i ett befintligt kluster. När du använder båda funktionerna tillsammans aktiverar du klustrets autoskalning på varje enskild nod i klustret och kan skicka unika regler för automatisk skalning till var och en.

Kommandot nedan förutsätter att du följde de [inledande anvisningarna](#create-an-aks-cluster-and-enable-the-cluster-autoscaler) tidigare i det här dokumentet och du vill uppdatera en befintlig nods Pools Max antal från *3* till *5*. Använd kommandot [AZ AKS nodepool Update][az-aks-nodepool-update] för att uppdatera en befintlig Node-uppsättnings inställningar.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

Klustret autoskalning kan inaktive ras med [AZ AKS nodepool Update][az-aks-nodepool-update] och skicka `--disable-cluster-autoscaler` parametern.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --disable-cluster-autoscaler
```

Om du vill återaktivera klustrets autoskalning i ett befintligt kluster kan du återaktivera det med kommandot [AZ AKS nodepool Update][az-aks-nodepool-update] och ange `--enable-cluster-autoscaler` `--min-count` parametrarna, och `--max-count` .

> [!NOTE]
> Om du planerar att använda den automatiska skalnings tjänsten för kluster med nodepools som sträcker sig över flera zoner och utnyttjar schemaläggnings funktioner som är relaterade till zoner som volym Top-schemaläggning, är rekommendationen att ha en nodepool per zon och aktiverar den `--balance-similar-node-groups` automatiska skalnings profilen. Detta säkerställer att autoskalning kan skalas upp och prövas och samtidigt hålla storlekarna på nodepools balanserade.

## <a name="next-steps"></a>Nästa steg

I den här artikeln visade vi hur du automatiskt skalar antalet AKS-noder. Du kan också använda den vågräta Pod autoskalning för att automatiskt justera antalet poddar som kör programmet. Anvisningar om hur du använder den vågräta Pod-autoskalning finns i [skala program i AKS][aks-scale-apps].

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-faq-node-resource-group]: faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group
[aks-multiple-node-pools]: use-multiple-node-pools.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[aks-view-master-logs]: ./view-master-logs.md#enable-resource-logs
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-update]: /cli/azure/aks#az-aks-update
[az-aks-scale]: /cli/azure/aks#az-aks-scale
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register

<!-- LINKS - external -->
[az-aks-update-preview]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
[kubernetes-faq]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why
