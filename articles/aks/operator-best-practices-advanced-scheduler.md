---
title: Metod tips för Scheduler-funktioner
titleSuffix: Azure Kubernetes Service
description: Lär dig metod tipsen för kluster operatorn för att använda avancerade Scheduler-funktioner som till exempel för-och-förfalskningar, Node-väljare och tillhörighet, eller mellan Pod tillhörighet och anti-tillhörighet i Azure Kubernetes service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 11/26/2018
ms.openlocfilehash: 1a8138b4b2fdab2cdef8d2cb4c27de8d12ef38cd
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2020
ms.locfileid: "97107354"
---
# <a name="best-practices-for-advanced-scheduler-features-in-azure-kubernetes-service-aks"></a>Bästa praxis för avancerade schemaläggningsfunktioner i Azure Kubernetes Service (AKS)

När du hanterar kluster i Azure Kubernetes service (AKS) behöver du ofta isolera team och arbets belastningar. Kubernetes Scheduler innehåller avancerade funktioner som låter dig styra vilka poddar som kan schemaläggas på vissa noder, eller hur program med flera Pod kan distribueras på ett lämpligt sätt i klustret. 

I den här artikeln fokuserar vi på avancerade funktioner för Kubernetes-schemaläggning för kluster operatörer. I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Använd bismakar och tolererar för att begränsa vilka poddar som kan schemaläggas på noder
> * Ange att poddar ska köras på vissa noder med Node Selector eller Node tillhörighet
> * Dela upp eller gruppera ihop poddar med interpod tillhörighet eller anti-tillhörighet

## <a name="provide-dedicated-nodes-using-taints-and-tolerations"></a>Tillhandahålla dedikerade noder med hjälp av utsmakar och tolererande

**Vägledning för bästa praxis** – begränsa åtkomsten för resurs intensiva program, till exempel ingångs styrenheter, till vissa noder. Se till att Node-resurserna är tillgängliga för arbets belastningar som kräver dem, och Tillåt inte schemaläggning av andra arbets belastningar på noderna.

När du skapar ditt AKS-kluster kan du distribuera noder med GPU-stöd eller ett stort antal kraftfulla processorer. Dessa noder används ofta för stora data bearbetnings arbets belastningar som Machine Learning (ML) eller artificiell intelligens (AI). Eftersom den här typen av maskin vara vanligt vis är en dyr resurs som du kan distribuera, begränsar du arbets belastningarna som kan schemaläggas på dessa noder. Du kanske vill tilldela vissa noder i klustret för att köra ingress-tjänster och förhindra andra arbets belastningar.

Detta stöd för olika noder tillhandahålls genom att använda flera noder i pooler. Ett AKS-kluster innehåller en eller flera noder i en pool.

Kubernetes Scheduler kan använda utsmakar och tolereras för att begränsa vilka arbets belastningar som kan köras på noder.

* En **bismak** tillämpas på en nod som endast anger att vissa poddar kan schemaläggas på dem.
* En **tolererande** tillämpas sedan på en pod som gör det möjligt för dem att *tolerera* en nods smak.

När du distribuerar en POD till ett AKS-kluster kan Kubernetes bara schemalägga poddar på noder där en tolererad av bismaken är justerad. Anta till exempel att du har en Node-pool i ditt AKS-kluster för noder med GPU-stöd. Du definierar namn, till exempel *GPU*, och sedan ett värde för schemaläggning. Om du ställer in det här värdet på *noschema* kan Kubernetes Scheduler inte schemalägga poddar på noden om Pod inte definierar lämplig tolerera.

```console
kubectl taint node aks-nodepool1 sku=gpu:NoSchedule
```

Med en-smak som tillämpas på noderna definierar du sedan en tolerera i pod-specifikationen som tillåter schemaläggning på noderna. I följande exempel definieras `sku: gpu` och `effect: NoSchedule` för att tolerera den smak som tillämpas på noden i föregående steg:

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

När den här Pod distribueras, till exempel `kubectl apply -f gpu-toleration.yaml` att använda, kan Kubernetes schemalägga Pod på noderna med den använda bismaken. Med den här logiska isoleringen kan du kontrol lera åtkomsten till resurser i ett kluster.

När du använder smakarna arbetar du med dina programutvecklare och ägare för att tillåta dem att definiera de nödvändiga tolererarna i sina distributioner.

Mer information om hur du använder flera Node-pooler i AKS finns i [skapa och hantera flera noder för ett kluster i AKS][use-multiple-node-pools].

### <a name="behavior-of-taints-and-tolerations-in-aks"></a>Beteende för bismaker och tolererar i AKS

När du uppgraderar en Node-pool i AKS följer smakarna och tolererarna ett uppsättnings mönster som de tillämpas på nya noder:

- **Standard kluster som använder skalnings uppsättningar för virtuella datorer**
  - Du kan använda [en nodepool][taint-node-pool] från AKS-API: n, så att nyligen utskalade noder får den API som anges för nodens smak.
  - Vi antar att du har ett kluster med två noder, *Nod1* och *NOD2*. Du uppgraderar Node-poolen.
  - Två ytterligare noder skapas, *nod3* och *nod4* och smakarna skickas på respektive.
  - De ursprungliga *Nod1* och *NOD2* tas bort.

- **Kluster utan stöd för skalnings uppsättning för virtuella datorer**
  - Nu antar vi att du har ett kluster med två noder, *Nod1* och *NOD2*. När du uppgraderar skapas en ytterligare nod (*nod3*).
  - De bismakerna från *Nod1* tillämpas på *nod3*. därefter raderas *Nod1* .
  - En annan ny nod skapas (med namnet *Nod1*, eftersom den tidigare *Nod1* togs bort) och *NOD2* -smakarna tillämpas på den nya *Nod1*. Sedan tas *NOD2* bort.
  - I själva verket *Nod1* blir *nod3* och *NOD2* blir *Nod1*.

När du skalar en Node-pool i AKS överförs inte smakarna och tolererarna genom design.

## <a name="control-pod-scheduling-using-node-selectors-and-affinity"></a>Styr Pod-schemaläggning med hjälp av Node-väljare och tillhörighet

**Vägledning för bästa praxis** – styr schemaläggningen av poddar på noder med hjälp av Node-väljare, tillhörigheter för noder eller mellan-Pod tillhörighet. De här inställningarna gör det möjligt för Kubernetes Scheduler att logiskt isolera arbets belastningar, t. ex. efter maskin vara i noden.

Bismakar och tolererande används för att logiskt isolera resurser med en hård avhuggning – om Pod inte tolererar en nods smak, är den inte schemalagd på noden. En annan metod är att använda Node-väljare. Du kan märka noder, till exempel för att indikera lokalt anslutna SSD-lagring eller en stor mängd minne och sedan definiera i pod-specifikationen för en Node-selektor. Kubernetes schemalägger sedan dessa poddar på en matchande nod. Till skillnad från tolererar kan poddar utan en matchande Node-väljare schemaläggas på namngivna noder. Det här beteendet tillåter att oanvända resurser på noderna använder, men ger prioritet till poddar som definierar matchande Node-selektorn.

Nu ska vi titta på ett exempel på noder med stor mängd minne. Dessa noder kan ge preferenser poddar som kräver en stor mängd minne. För att se till att resurserna inte är i inaktiva läge, tillåter de också att andra poddar körs.

```console
kubectl label node aks-nodepool1 hardware=highmem
```

En POD-specifikation lägger sedan till `nodeSelector` egenskapen för att definiera en Node-selektor som matchar etikett uppsättningen på en nod:

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

När du använder dessa alternativ för Schemaläggaren arbetar du med dina programutvecklare och ägare så att de kan definiera sina Pod-specifikationer korrekt.

Mer information om hur du använder Node-väljare finns i [tilldela poddar till noder][k8s-node-selector].

### <a name="node-affinity"></a>Node-tillhörighet

En Node-selektor är ett grundläggande sätt att tilldela poddar till en specifik nod. Mer flexibilitet är tillgänglig med hjälp av *Node tillhörighet*. Med Node Affinity definierar du vad som händer om Pod inte kan matchas med en nod. Du kan *kräva* att Kubernetes Scheduler matchar en POD med en etikettad värd. Du kan också *föredra* en matchning men tillåta att Pod schemaläggs på en annan värd om ingen matchning är tillgänglig.

I följande exempel anges nodens tillhörighet till *requiredDuringSchedulingIgnoredDuringExecution*. Den här tillhörigheten kräver att Kubernetes-schemat använder en nod med en matchande etikett. Om ingen nod är tillgänglig, måste Pod vänta på schemaläggning för att fortsätta. Om du vill tillåta att Pod schemaläggs på en annan nod kan du i stället ange värdet till *preferredDuringSchedulingIgnoreDuringExecution*:

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

*IgnoredDuringExecution* -delen av inställningen indikerar att om nodens etiketter ändras, bör Pod inte avlägsnas från noden. Kubernetes Scheduler använder bara uppdaterade Node-etiketter för nya poddar som har schemalagts, inte poddar som redan har schemalagts för noderna.

Mer information finns i [tillhörighet och anti-tillhörighet][k8s-affinity].

### <a name="inter-pod-affinity-and-anti-affinity"></a>Inter-Pod tillhörighet och anti-tillhörighet

En sista metod för Schemaläggaren av Kubernetes-Schemaläggaren för att logiskt isolera arbets belastningar använder sig av interpod tillhörighet eller anti-tillhörighet. Inställningarna definierar *att poddar inte ska* schemaläggas på en nod som har en befintlig matchande POD eller att de *ska* schemaläggas. Som standard försöker Kubernetes Scheduler schemalägga flera poddar i en replik uppsättning mellan noder. Du kan definiera mer speciella regler kring det här beteendet.

Ett exempel är ett webb program som också använder en Azure-cache för Redis. Du kan använda Pod som skydds regler för att begära att Kubernetes Scheduler distribuerar repliker över flera noder. Du kan sedan använda tillhörighets regler för att kontrol lera att varje webb program komponent är schemalagd på samma värd som motsvarande cache. Fördelningen av poddar över noder ser ut som i följande exempel:

| **Nod 1** | **Nod 2** | **Nod 3** |
|------------|------------|------------|
| webapp-1   | webapp-2   | webapp-3   |
| cache-1    | cache-2    | cache-3    |

Det här exemplet är en mer komplex distribution än att använda Node-väljare eller Node-tillhörighet. I distributionen får du kontroll över hur Kubernetes schemalägger poddar på noder och kan isolera resurser logiskt. Ett komplett exempel på detta webb program med Azure cache för Redis-exempel finns i [samordna poddar på samma nod][k8s-pod-affinity].

## <a name="next-steps"></a>Nästa steg

Den här artikeln fokuserar på avancerade funktioner i Kubernetes Scheduler. Mer information om kluster åtgärder i AKS finns i följande metod tips:

* [Flera innehavare och isolering av kluster][aks-best-practices-scheduler]
* [Basic Kubernetes Scheduler-funktioner][aks-best-practices-scheduler]
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
