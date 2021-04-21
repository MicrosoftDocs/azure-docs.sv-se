---
title: Metodtips för schemaläggningsfunktioner
titleSuffix: Azure Kubernetes Service
description: Lär dig metodtips för klusteroperatorn för att använda avancerade schemaläggningsfunktioner som taints och toleranser, nodväljare och tillhörighet eller tillhörighet mellan poddar och antitillhörighet i Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: 971916c3fc903ff5d69db2e0f82fd884acf807b3
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2021
ms.locfileid: "107831590"
---
# <a name="best-practices-for-advanced-scheduler-features-in-azure-kubernetes-service-aks"></a>Bästa praxis för avancerade schemaläggningsfunktioner i Azure Kubernetes Service (AKS)

När du hanterar kluster i Azure Kubernetes Service (AKS) behöver du ofta isolera team och arbetsbelastningar. Med avancerade funktioner som tillhandahålls av Kubernetes-schemaläggaren kan du styra:
* Vilka poddar som kan schemaläggas på vissa noder.
* Hur program med flera poddar kan distribueras korrekt i klustret. 

Den här artikeln om bästa praxis fokuserar på avancerade Funktioner för Kubernetes-schemaläggning för klusteroperatörer. I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Använd taints och toleranser för att begränsa vilka poddar som kan schemaläggas på noder.
> * Ge prioritet till poddar som ska köras på vissa noder med nodväljare eller nodtillhörighet.
> * Dela upp eller gruppera ihop poddar med tillhörighet mellan poddar eller antitillhörighet.

## <a name="provide-dedicated-nodes-using-taints-and-tolerations"></a>Tillhandahålla dedikerade noder med hjälp av taints och toleranser

> **Metodvägledning:** 
>
> Begränsa åtkomsten för resursintensiva program, till exempel ingress-kontrollanter, till specifika noder. Håll nodresurser tillgängliga för arbetsbelastningar som kräver dem och tillåt inte schemaläggning av andra arbetsbelastningar på noderna.

När du skapar ditt AKS-kluster kan du distribuera noder med GPU-stöd eller ett stort antal kraftfulla processorer. Du kan använda dessa noder för stora arbetsbelastningar för databehandling, till exempel maskininlärning (ML) eller artificiell intelligens (AI). 

Eftersom den här nodresursmaskinvaran vanligtvis är dyr att distribuera begränsar du de arbetsbelastningar som kan schemaläggas på dessa noder. I stället dedikerar du vissa noder i klustret för att köra ingresstjänster och förhindra andra arbetsbelastningar.

Det här stödet för olika noder tillhandahålls med hjälp av flera nodpooler. Ett AKS-kluster tillhandahåller en eller flera nodpooler.

Kubernetes-schemaläggaren använder taints och toleranser för att begränsa vilka arbetsbelastningar som kan köras på noder.

* Tillämpa en **taint på** en nod för att ange att endast specifika poddar kan schemaläggas på dem.
* Tillämpa sedan en **tolerans på en** podd så att de *tolererar* en nods taint.

När du distribuerar en podd till ett AKS-kluster schemalägger Kubernetes endast poddar på noder vars taint överensstämmer med toleransen. Anta till exempel att du har lagt till en nodpool i ditt AKS-kluster för noder med GPU-stöd. Du definierar namn, till exempel *gpu* och sedan ett värde för schemaläggning. Om du anger det *här värdet till NoSchedule* hindras Kubernetes-schemaläggaren från att schemalägga poddar med odefinierad tolerans på noden.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name taintnp \
    --node-taints sku=gpu:NoSchedule \
    --no-wait
```

När en taint tillämpas på noder i nodpoolen definierar du en tolerans i poddspecifikationen som tillåter schemaläggning på noderna. I följande exempel definieras `sku: gpu` och för att tolerera den `effect: NoSchedule` taint som tillämpades på nodpoolen i föregående steg:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  tolerations:
  - key: "sku"
    operator: "Equal"
    value: "gpu"
    effect: "NoSchedule"
```

När den här podden distribueras `kubectl apply -f gpu-toleration.yaml` med kan Kubernetes schemalägga podden på noderna med taint tillämpad. Med den här logiska isoleringen kan du styra åtkomsten till resurser i ett kluster.

När du tillämpar taints kan du samarbeta med dina programutvecklare och ägare så att de kan definiera de toleranser som krävs i deras distributioner.

Mer information om hur du använder flera nodpooler i AKS finns i Skapa och hantera flera nodpooler [för ett kluster i AKS.][use-multiple-node-pools]

### <a name="behavior-of-taints-and-tolerations-in-aks"></a>Beteende för taints och toleranser i AKS

När du uppgraderar en nodpool i AKS följer taints och toleranser ett anropsmönster när de tillämpas på nya noder:

#### <a name="default-clusters-that-use-vm-scale-sets"></a>Standardkluster som använder VM Scale Sets
Du kan [taint en nodpool från][taint-node-pool] AKS-API:et så att nyligen utskalade noder får API-angivna nod-taints.

Vi antar att:
1. Du börjar med ett kluster med två noder: *node1* och *node2.* 
1. Du uppgraderar nodpoolen.
1. Två ytterligare noder skapas: *node3* och *node4.* 
1. Taints skickas vidare.
1. Den ursprungliga *node1* *och node2* tas bort.

#### <a name="clusters-without-vm-scale-set-support"></a>Kluster utan stöd för VM-skalningsuppsättning

Vi antar återigen att:
1. Du har ett kluster med två noder: *node1* och *node2.* 
1. Du uppgraderar sedan nodpoolen.
1. En ytterligare nod skapas: *node3*.
1. Taints från *node1* tillämpas på *node3*.
1. *node1* tas bort.
1. En ny *node1* skapas för att ersätta till den ursprungliga *node1*.
1. Node2-taints tillämpas på den nya *noden1*.  
1. *node2* tas bort.

I princip *blir node1* *node3* och *node2 blir* den nya *noden1*.

När du skalar en nodpool i AKS förs inte taints och toleranser över av design.

## <a name="control-pod-scheduling-using-node-selectors-and-affinity"></a>Styra poddschemaläggning med nodväljare och tillhörighet

> **Vägledning och metodtips** 
> 
> Kontrollera schemaläggningen av poddar på noder med hjälp av nodväljare, nodtillhörighet eller tillhörighet mellan poddar. Med de här inställningarna kan Kubernetes-schemaläggaren logiskt isolera arbetsbelastningar, till exempel efter maskinvara i noden.

Taints och toleranser isolerar resurser logiskt med en hård skärning. Om podden inte tolererar en nods taint schemaläggs den inte på noden.

Du kan också använda nodväljare. Du kan till exempel märka noder för att ange lokalt ansluten SSD-lagring eller en stor mängd minne och sedan definiera en nodväljare i poddspecifikationen. Kubernetes schemalägger dessa poddar på en matchande nod.

Till skillnad från toleranser kan poddar utan en matchande nodväljare fortfarande schemaläggas på märkta noder. Det här beteendet gör att oanvända resurser på noderna kan använda, men prioriterar poddar som definierar den matchande nodväljaren.

Nu ska vi titta på ett exempel på noder med en stor mängd minne. Dessa noder prioriterar poddar som begär en stor mängd minne. För att säkerställa att resurserna inte är inaktiva tillåter de även att andra poddar körs. Följande exempelkommando lägger till en nodpool med etiketten *hardware=highmem* till *myAKSCluster* i *myResourceGroup*. Alla noder i nodpoolen har den här etiketten.

```azurecli-interactive
az aks nodepool add \
    --resource-group myResourceGroup \
    --cluster-name myAKSCluster \
    --name labelnp \
    --node-count 1 \
    --labels hardware=highmem \
    --no-wait
```

En poddspecifikation lägger sedan `nodeSelector` till egenskapen för att definiera en nodväljare som matchar etiketten som angetts på en nod:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  nodeSelector:
      hardware: highmem
```

När du använder dessa alternativ för schemaläggaren kan du samarbeta med dina programutvecklare och ägare så att de kan definiera poddspecifikationerna korrekt.

Mer information om hur du använder nodväljare finns [i Tilldela poddar till noder.][k8s-node-selector]

### <a name="node-affinity"></a>Nodtillhörighet

En nodväljare är en grundläggande lösning för att tilldela poddar till en viss nod. *Nodtillhörighet ger* mer flexibilitet, så att du kan definiera vad som händer om podden inte kan matchas med en nod. Du kan: 
* *Kräv* att Kubernetes-schemaläggaren matchar en podd med en märkt värd. Eller:
* *Föredrar* en matchning men tillåter att podden schemaläggs på en annan värd om ingen matchning är tillgänglig.

I följande exempel anges nodtillhörigheten *till requiredDuringSchedulingIgnoredDuringExecution*. Den här tillhörigheten kräver att Kubernetes-schemat använder en nod med en matchande etikett. Om ingen nod är tillgänglig måste podden vänta på att schemaläggningen ska fortsätta. Om du vill tillåta att podden schemaläggs på en annan nod kan du i stället ange värdet till ***önskad** UnderSchedulingIgnoreDuringExecution*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: tf-mnist
spec:
  containers:
  - name: tf-mnist
    image: mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu
    resources:
      requests:
        cpu: 0.5
        memory: 2Gi
      limits:
        cpu: 4.0
        memory: 16Gi
  affinity:
    nodeAffinity:
      requiredDuringSchedulingIgnoredDuringExecution:
        nodeSelectorTerms:
        - matchExpressions:
          - key: hardware
            operator: In
            values: highmem
```

Delen *IgnoredDuringExecution* i inställningen anger att podden inte ska tas bort från noden om nodetiketterna ändras. Kubernetes-schemaläggaren använder bara uppdaterade nodetiketter för nya poddar som schemaläggs, inte poddar som redan har schemalagts på noderna.

Mer information finns i [Tillhörighet och antitillhörighet][k8s-affinity].

### <a name="inter-pod-affinity-and-anti-affinity"></a>Tillhörighet mellan poddar och antitillhörighet

En sista metod för Kubernetes-schemaläggaren att logiskt isolera arbetsbelastningar är att använda tillhörighet mellan poddar eller antitillhörighet. De här inställningarna definierar att *poddar antingen inte ska* eller *ska* schemaläggas på en nod som har en befintlig matchande podd. Som standard försöker Kubernetes-schemaläggaren schemalägga flera poddar i en replikuppsättning mellan noder. Du kan definiera mer specifika regler för det här beteendet.

Du kan till exempel ha en webbapp som också använder en Azure Cache for Redis. 
1. Du använder regler för podd-antitillhörighet för att begära att Kubernetes-schemaläggaren distribuerar repliker mellan noder. 
1. Du använder tillhörighetsregler för att se till att varje webbappskomponent schemaläggs på samma värd som en motsvarande cache. 

Distributionen av poddar mellan noder ser ut som i följande exempel:

| **Nod 1** | **Nod 2** | **Nod 3** |
|------------|------------|------------|
| webapp-1   | webapp-2   | webapp-3   |
| cache-1    | cache-2    | cache-3    |

Tillhörighet mellan poddar och anti-tillhörighet ger en mer komplex distribution än nodväljare eller nodtillhörighet. Med distributionen isolerar du logiskt resurser och styr hur Kubernetes schemalägger poddar på noder. 

Ett fullständigt exempel på den här webbappen med Azure Cache for Redis finns i [Samplats poddar på samma nod.][k8s-pod-affinity]

## <a name="next-steps"></a>Nästa steg

Den här artikeln fokuserar på avancerade funktioner i Kubernetes-schemaläggaren. Mer information om klusteråtgärder i AKS finns i följande metodtips:

* [Flera innehavare och isolering av kluster][aks-best-practices-scheduler]
* [Grundläggande funktioner i Kubernetes-schemaläggaren][aks-best-practices-scheduler]
* [Autentisering och auktorisering][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->
[k8s-taints-tolerations]: https://kubernetes.io/docs/concepts/configuration/taint-and-toleration/
[k8s-node-selector]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/
[k8s-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#affinity-and-anti-affinity
[k8s-pod-affinity]: https://kubernetes.io/docs/concepts/configuration/assign-pod-node/#always-co-located-in-the-same-node

<!-- INTERNAL LINKS -->
[aks-best-practices-scheduler]: operator-best-practices-scheduler.md
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-identity]: operator-best-practices-identity.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[taint-node-pool]: use-multiple-node-pools.md#specify-a-taint-label-or-tag-for-a-node-pool
