---
title: Använda autoskalning av kluster i Azure Kubernetes Service (AKS)
description: Lär dig hur du använder autoskalning av kluster för att automatiskt skala klustret för att uppfylla programkrav i ett Azure Kubernetes Service-kluster (AKS).
services: container-service
ms.topic: article
ms.date: 07/18/2019
ms.openlocfilehash: f02b91f73320786716e356639d8134280325dc19
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776005"
---
# <a name="automatically-scale-a-cluster-to-meet-application-demands-on-azure-kubernetes-service-aks"></a>Skala ett kluster automatiskt för att uppfylla programbehov i Azure Kubernetes Service (AKS)

För att hålla dig till programkraven i Azure Kubernetes Service (AKS) kan du behöva justera antalet noder som kör dina arbetsbelastningar. Autoskalningskomponenten för kluster kan hålla utkik efter poddar i klustret som inte kan schemaläggas på grund av resursbegränsningar. När problem identifieras ökar antalet noder i en nodpool för att uppfylla programbehovet. Noder kontrolleras också regelbundet för att se om det inte finns några poddar som körs, och sedan minskar antalet noder efter behov. Med den här möjligheten att automatiskt skala upp eller ned antalet noder i ditt AKS-kluster kan du köra ett effektivt och kostnadseffektivt kluster.

Den här artikeln visar hur du aktiverar och hanterar autoskalning av kluster i ett AKS-kluster.

## <a name="before-you-begin"></a>Innan du börjar

Den här artikeln kräver att du kör Azure CLI version 2.0.76 eller senare. Kör `az --version` för att hitta versionen. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

## <a name="about-the-cluster-autoscaler"></a>Om autoskalning av kluster

För att anpassa sig till föränderliga programkrav, till exempel mellan arbetsdag och kväll eller helger, behöver kluster ofta ett sätt att skala automatiskt. AKS-kluster kan skalas på något av två sätt:

* **Autoskalning av kluster söker** efter poddar som inte kan schemaläggas på noder på grund av resursbegränsningar. Klustret ökar sedan automatiskt antalet noder.
* Horisontell **autoskalning av poddar** använder Metrics Server i ett Kubernetes-kluster för att övervaka resursbehovet för poddar. Om ett program behöver fler resurser ökar antalet poddar automatiskt för att möta efterfrågan.

![Autoskalning av kluster och horisontell autoskalning av poddar fungerar ofta tillsammans för att stödja de programkrav som krävs](media/autoscaler/cluster-autoscaler.png)

Både horisontell autoskalning av poddar och autoskalning av kluster kan också minska antalet poddar och noder efter behov. Autoskalning av kluster minskar antalet noder när det har funnits outnyttjad kapacitet under en viss tidsperiod. Poddar på en nod som ska tas bort av autoskalning av kluster schemaläggs på ett säkert sätt någon annanstans i klustret. Autoskalning av kluster kan kanske inte skala ned om poddar inte kan flyttas, till exempel i följande situationer:

* En podd skapas direkt och backas inte upp av ett kontrollantobjekt, till exempel en distribution eller replikuppsättning.
* En budget för poddavbrott (PDB) är för begränsad och tillåter inte att antalet poddar hamnar under ett visst tröskelvärde.
* En podd använder nodväljare eller antitillhörighet som inte kan respekteras om den schemaläggs på en annan nod.

Mer information om hur autoskalning av kluster kan inte skalas ned finns i Vilka typer av poddar kan förhindra att autoskalning av kluster tar [bort en nod?][autoscaler-scaledown]

Autoskalning av kluster använder startparametrar för exempelvis tidsintervall mellan skalningshändelser och resurströsklar. Mer information om vilka parametrar som autoskalning av kluster använder finns i [Använda autoskalningsprofilen](#using-the-autoscaler-profile).

Klustret och horisontella autoskalning av poddar kan fungera tillsammans och distribueras ofta i ett kluster. I kombination fokuserar horisontell autoskalning av poddar på att köra det antal poddar som krävs för att möta programbehovet. Autoskalning av kluster fokuserar på att köra det antal noder som krävs för att stödja schemalagda poddar.

> [!NOTE]
> Manuell skalning inaktiveras när du använder autoskalning av kluster. Låt autoskalning av kluster fastställa det antal noder som krävs. Om du vill skala klustret manuellt inaktiverar [du autoskalning av kluster.](#disable-the-cluster-autoscaler)

## <a name="create-an-aks-cluster-and-enable-the-cluster-autoscaler"></a>Skapa ett AKS-kluster och aktivera autoskalning av kluster

Om du behöver skapa ett AKS-kluster använder du [kommandot az aks create.][az-aks-create] Om du vill aktivera och konfigurera autoskalning av kluster i nodpoolen för klustret använder du `--enable-cluster-autoscaler` parametern och anger en nod `--min-count` och `--max-count` .

> [!IMPORTANT]
> Autoskalning av kluster är en Kubernetes-komponent. Även om AKS-klustret använder en VM-skalningsuppsättning för noderna ska du inte manuellt aktivera eller redigera inställningar för autoskalning av skalningsuppsättning i Azure Portal eller använda Azure CLI. Låt Autoskalning av Kubernetes-kluster hantera de skalningsinställningar som krävs. Mer information finns i [Kan jag ändra AKS-resurserna i nodresursgruppen?][aks-faq-node-resource-group]

I följande exempel skapas ett AKS-kluster med en enskild nodpool som backas upp av en VM-skalningsuppsättning. Det aktiverar även autoskalning av kluster i nodpoolen för klustret och anger minst *1* och högst *3* noder:

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

Det tar några minuter att skapa klustret och konfigurera inställningarna för autoskalning av kluster.

## <a name="update-an-existing-aks-cluster-to-enable-the-cluster-autoscaler"></a>Uppdatera ett befintligt AKS-kluster för att aktivera autoskalning av kluster

Använd kommandot [az aks update][az-aks-update] för att aktivera och konfigurera autoskalning av kluster i nodpoolen för det befintliga klustret. Använd `--enable-cluster-autoscaler` parametern och ange en nod `--min-count` och `--max-count` .

> [!IMPORTANT]
> Autoskalning av kluster är en Kubernetes-komponent. Även om AKS-klustret använder en VM-skalningsuppsättning för noderna ska du inte manuellt aktivera eller redigera inställningar för autoskalning av skalningsuppsättning i Azure Portal eller använda Azure CLI. Låt Autoskalning av Kubernetes-kluster hantera de skalningsinställningar som krävs. Mer information finns i [Kan jag ändra AKS-resurserna i nodresursgruppen?][aks-faq-node-resource-group]

I följande exempel uppdateras ett befintligt AKS-kluster för att aktivera autoskalning av kluster i nodpoolen för klustret och anger minst *1* och högst *3* noder:

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --enable-cluster-autoscaler \
  --min-count 1 \
  --max-count 3
```

Det tar några minuter att uppdatera klustret och konfigurera inställningarna för autoskalning av kluster.

## <a name="change-the-cluster-autoscaler-settings"></a>Ändra inställningarna för autoskalning av kluster

> [!IMPORTANT]
> Om du har flera nodpooler i ditt AKS-kluster går du vidare till [avsnittet autoskalning med flera agentpooler.](#use-the-cluster-autoscaler-with-multiple-node-pools-enabled) Kluster med flera agentpooler kräver att kommandouppsättningen används `az aks nodepool` för att ändra nodpoolens specifika egenskaper i stället för `az aks` .

I föregående steg för att skapa ett AKS-kluster eller uppdatera en befintlig nodpool, var det minsta antalet noder för autoskalning av kluster inställt på *1* och det maximala antalet noder var inställt på *3*. När programmets krav ändras kan du behöva justera antalet noder för autoskalning av kluster.

Om du vill ändra antalet noder använder du [kommandot az aks update.][az-aks-update]

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

Exemplet ovan uppdaterar autoskalning av kluster på poolen med en nod *i myAKSCluster* till *minst 1* och högst *5* noder.

> [!NOTE]
> Autoskalning av kluster fattar skalningsbeslut baserat på det lägsta och högsta antal som angetts för varje nodpool, men den framtvingar dem inte efter uppdatering av det lägsta eller högsta antalet. Om du till exempel anger ett minsta antal på 5 när det aktuella antalet noder är 3 skalas inte poolen omedelbart upp till 5. Om det minsta antalet noder i nodpoolen har ett högre värde än det aktuella antalet noder, kommer de nya inställningarna för lägsta eller högsta antal att respekteras när det finns tillräckligt många oplanerade poddar som kräver två nya ytterligare noder och utlöser en autoskalningshändelse. Efter skalningshändelsen respekteras de nya antalsgränserna.

Övervaka prestanda för dina program och tjänster och justera antalet autoskalningsnoder för kluster så att de matchar den prestanda som krävs.

## <a name="using-the-autoscaler-profile"></a>Använda autoskalningsprofilen

Du kan också konfigurera mer detaljerad information om autoskalning av kluster genom att ändra standardvärdena i profilen för autoskalning för hela klustret. Till exempel inträffar en nedskalningshändelse när noderna undertänds efter 10 minuter. Om du hade arbetsbelastningar som kördes var 15:e minut kanske du vill ändra autoskalningsprofilen så att den skalas ned under använda noder efter 15 eller 20 minuter. När du aktiverar autoskalning av kluster används en standardprofil om du inte anger olika inställningar. Autoskalningsprofilen för kluster har följande inställningar som du kan uppdatera:

| Inställning                          | Beskrivning                                                                              | Standardvärde |
|----------------------------------|------------------------------------------------------------------------------------------|---------------|
| genomsökningsintervall                    | Hur ofta klustret omvärderas för upp- eller nedskalning                                    | 10 sekunder    |
| scale-down-delay-after-add       | Hur lång tid efter uppskalning som nedskalningsutvärderingen återupptas                               | 10 minuter    |
| scale-down-delay-after-delete    | Hur lång tid efter nodborttagningen som nedskalningsutvärderingen återupptas                          | genomsökningsintervall |
| scale-down-delay-after-failure   | Hur lång tid efter nedskalningsfelet som nedskalningsutvärderingen återupptas                     | 3 minuter     |
| scale-down-unneeded-time         | Hur länge en nod ska vara obevakad innan den kan skalas ned                  | 10 minuter    |
| skala ned oläslig tid          | Hur länge en oläslig nod ska vara obevakad innan den kan skalas ned         | 20 minuter    |
| tröskelvärde för nedskalning av användning | Nodanvändningsnivå, definierad som summan av begärda resurser dividerat med kapacitet, under vilken en nod kan övervägas för nedskalning | 0,5 |
| max-graceful-termination-sec     | Maximalt antal sekunder som autoskalning av kluster väntar på poddavslut vid försök att skala ned en nod | 600 sekunder   |
| balance-similar-node-groups      | Identifierar liknande nodpooler och balanserar antalet noder mellan dem                 | falskt         |
| Expander                         | Typ av [nodpools expanderare](https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-expanders) som ska användas för att skala upp. Möjliga värden: `most-pods` , `random` , `least-waste` , `priority` | slumpmässig | 
| skip-nodes-with-local-storage    | Om true cluster autoscaler aldrig tar bort noder med poddar med lokal lagring, till exempel EmptyDir eller HostPath | true |
| skip-nodes-with-system-pods      | Om true cluster autoscaler aldrig tar bort noder med poddar från kube-system (förutom DaemonSet eller speglingspoddar) | true | 
| max-empty-bulk-delete            | Maximalt antal tomma noder som kan tas bort samtidigt                       | 10 noder      |
| new-pod-scale-up-delay           | För scenarier som burst-/batchskalning där du inte vill att certifikatutfärdaren ska agera innan Kubernetes-schemaläggaren kan schemalägga alla poddar kan du be CERTIFIKATutfärdaren att ignorera oplanerade poddar innan de har en viss ålder.                                                                                                                | 0 sekunder    |
| max-total-unready-percentage     | Maximal procentandel olästa noder i klustret. När den här procentandelen har överskridits stoppar ca-certifikatutfärdaren åtgärder | 45 % |
| max-node-provision-time          | Maximal tid som autoskalningen väntar på att en nod ska etableras                           | 15 minuter    |   
| ok-total-unready-count           | Antal tillåtna olästa noder, oavsett maximalt antal oläslig procentandel            | 3 noder       |

> [!IMPORTANT]
> Profilen för autoskalning av kluster påverkar alla nodpooler som använder autoskalning av kluster. Du kan inte ange en autoskalningsprofil per nodpool.
>
> Profilen för autoskalning av kluster kräver version *2.11.1* eller senare av Azure CLI. Om du behöver installera eller uppgradera kan du läsa [Installera Azure CLI][azure-cli-install].

### <a name="set-the-cluster-autoscaler-profile-on-an-existing-aks-cluster"></a>Ange profilen för autoskalning av kluster i ett befintligt AKS-kluster

Använd kommandot [az aks update][az-aks-update-preview] med *parametern cluster-autoscaler-profile* för att ange autoskalningsprofilen för klustret. I följande exempel konfigureras inställningen för genomsökningsintervall som 30 sekunder i profilen.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile scan-interval=30s
```

När du aktiverar autoskalning av kluster i nodpooler i klustret använder klustren även profilen för autoskalning av kluster. Exempel:

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
> När du anger profilen för autoskalning av kluster börjar alla befintliga nodpooler med autoskalning av kluster att börja använda profilen omedelbart.

### <a name="set-the-cluster-autoscaler-profile-when-creating-an-aks-cluster"></a>Ange profilen för autoskalning av kluster när du skapar ett AKS-kluster

Du kan också använda *parametern cluster-autoscaler-profile* när du skapar klustret. Exempel:

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

Kommandot ovan skapar ett AKS-kluster och definierar genomsökningsintervallet som 30 sekunder för autoskalningsprofilen för hela klustret. Kommandot aktiverar även autoskalning av kluster i den första nodpoolen, anger det minsta antalet noder till 1 och det maximala antalet noder till 3.

### <a name="reset-cluster-autoscaler-profile-to-default-values"></a>Återställa autoskalningsprofilen för kluster till standardvärden

Använd kommandot [az aks update][az-aks-update-preview] för att återställa klustrets autoskalningsprofil.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --cluster-autoscaler-profile ""
```

## <a name="disable-the-cluster-autoscaler"></a>Inaktivera autoskalning av kluster

Om du inte längre vill använda autoskalning av kluster kan du inaktivera den med kommandot [az aks update][az-aks-update-preview] och ange `--disable-cluster-autoscaler` parametern . Noder tas inte bort när autoskalning av kluster är inaktiverat.

```azurecli-interactive
az aks update \
  --resource-group myResourceGroup \
  --name myAKSCluster \
  --disable-cluster-autoscaler
```

Du kan skala klustret manuellt när du har inaktiverat autoskalning av kluster med hjälp av [kommandot az aks scale.][az-aks-scale] Om du använder horisontell autoskalning av poddar fortsätter funktionen att köras med autoskalning av kluster inaktiverad, men poddar kanske inte kan schemaläggas om alla nodresurser används.

## <a name="re-enable-a-disabled-cluster-autoscaler"></a>Återaktivera en inaktiverad autoskalning av kluster

Om du vill återaktivera autoskalning av kluster i ett befintligt kluster kan du återaktivera det med kommandot [az aks update][az-aks-update-preview] och ange `--enable-cluster-autoscaler` parametrarna , och `--min-count` `--max-count` .

## <a name="retrieve-cluster-autoscaler-logs-and-status"></a>Hämta loggar och status för autoskalning av kluster

Om du vill diagnostisera och felsöka autoskalningshändelser kan loggar och status hämtas från autoskalnings-tillägget.

AKS hanterar autoskalning av kluster för din räkning och kör den i det hanterade kontrollplanet. Du kan aktivera kontrollplansnoden för att se loggar och åtgärder från CERTIFIKATutfärdaren.

Följ dessa steg om du vill konfigurera loggar som ska skickas från autoskalning av kluster till Log Analytics.

1. Konfigurera en regel för resursloggar för att skicka loggar för kluster-autoskalning till Log Analytics. [Anvisningarna beskrivs här.][aks-view-master-logs]Se till att du markerar kryssrutan `cluster-autoscaler` för när du väljer alternativ för "Loggar".
1. Välj avsnittet "Loggar" i klustret via Azure Portal.
1. Ange följande exempelfråga i Log Analytics:

```
AzureDiagnostics
| where Category == "cluster-autoscaler"
```

Du bör se loggar som liknar följande exempel så länge det finns loggar att hämta.

![Log Analytics-loggar](media/autoscaler/autoscaler-logs.png)

Autoskalning av kluster skriver också ut hälsostatus till en `configmap` med namnet `cluster-autoscaler-status` . Kör följande kommando för att hämta `kubectl` loggarna. En hälsostatus rapporteras för varje nodpool som konfigurerats med autoskalning av kluster.

```
kubectl get configmap -n kube-system cluster-autoscaler-status -o yaml
```

Mer information om vad som loggas från autoskalningen finns i Vanliga frågor och svar i [Kubernetes/autoscaler GitHub-projektet][kubernetes-faq].

## <a name="use-the-cluster-autoscaler-with-multiple-node-pools-enabled"></a>Använda autoskalning av kluster med flera nodpooler aktiverade

Autoskalning av kluster kan användas tillsammans med flera [aktiverade nodpooler.][aks-multiple-node-pools] Följ det dokumentet för att lära dig hur du aktiverar flera nodpooler och lägger till ytterligare nodpooler i ett befintligt kluster. När du använder båda funktionerna tillsammans aktiverar du autoskalning av kluster på varje enskild nodpool i klustret och kan skicka unika regler för automatisk skalning till var och en.

Kommandot nedan förutsätter att [](#create-an-aks-cluster-and-enable-the-cluster-autoscaler) du följde de första anvisningarna tidigare i det här dokumentet och att du vill uppdatera en befintlig nodpools maxantal från *3* till *5*. Använd kommandot [az aks nodepool update][az-aks-nodepool-update] för att uppdatera inställningarna för en befintlig nodpool.

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --update-cluster-autoscaler \
  --min-count 1 \
  --max-count 5
```

Autoskalning av kluster kan inaktiveras med [az aks nodepool update][az-aks-nodepool-update] och skicka `--disable-cluster-autoscaler` parametern .

```azurecli-interactive
az aks nodepool update \
  --resource-group myResourceGroup \
  --cluster-name myAKSCluster \
  --name nodepool1 \
  --disable-cluster-autoscaler
```

Om du vill återaktivera autoskalning av kluster i ett befintligt kluster kan du återaktivera det med kommandot [az aks nodepool update][az-aks-nodepool-update] och ange `--enable-cluster-autoscaler` parametrarna , och `--min-count` `--max-count` .

> [!NOTE]
> Om du planerar att använda autoskalning av kluster med nodpooler som sträcker sig över flera zoner och använder schemaläggningsfunktioner relaterade till zoner, till exempel topologisk schemaläggning av volymer, är rekommendationen att ha en nodpool per zon och aktivera via `--balance-similar-node-groups` autoskalningsprofilen. Detta säkerställer att autoskalningen skalas upp på ett bra sätt och försöker hålla storlekarna på nodpoolerna balanserade.

## <a name="next-steps"></a>Nästa steg

Den här artikeln visade hur du automatiskt skalar antalet AKS-noder. Du kan också använda horisontell autoskalning av poddar för att automatiskt justera antalet poddar som kör ditt program. Anvisningar om hur du använder horizontal pod autoscaler finns i [Skala program i AKS.][aks-scale-apps]

<!-- LINKS - internal -->
[aks-faq]: faq.md
[aks-faq-node-resource-group]: faq.md#can-i-modify-tags-and-other-properties-of-the-aks-resources-in-the-node-resource-group
[aks-multiple-node-pools]: use-multiple-node-pools.md
[aks-scale-apps]: tutorial-kubernetes-scale.md
[aks-support-policies]: support-policies.md
[aks-upgrade]: upgrade-cluster.md
[aks-view-master-logs]: ./view-control-plane-logs.md#enable-resource-logs
[autoscaler-profile-properties]: #using-the-autoscaler-profile
[azure-cli-install]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-update]: /cli/azure/aks#az_aks_update
[az-aks-scale]: /cli/azure/aks#az_aks_scale
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register

<!-- LINKS - external -->
[az-aks-update-preview]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview
[az-aks-nodepool-update]: https://github.com/Azure/azure-cli-extensions/tree/master/src/aks-preview#enable-cluster-auto-scaler-for-a-node-pool
[autoscaler-scaledown]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-types-of-pods-can-prevent-ca-from-removing-a-node
[autoscaler-parameters]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#what-are-the-parameters-to-ca
[kubernetes-faq]: https://github.com/kubernetes/autoscaler/blob/master/cluster-autoscaler/FAQ.md#ca-doesnt-work-but-it-used-to-work-yesterday-why