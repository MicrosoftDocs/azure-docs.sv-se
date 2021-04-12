---
title: Bästa praxis för Operator – grundläggande Scheduler-funktioner i Azure Kubernetes Services (AKS)
description: Lär dig metod tips för kluster operatörer för att använda grundläggande funktioner i Schemaläggaren, till exempel resurs kvoter och Pod avbrott i Azure Kubernetes service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 03/09/2021
ms.openlocfilehash: 8c0f1d0cda61638abe03b92c627a5ea0455c31cb
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107104906"
---
# <a name="best-practices-for-basic-scheduler-features-in-azure-kubernetes-service-aks"></a>Metod tips för grundläggande funktioner i Schemaläggaren i Azure Kubernetes service (AKS)

När du hanterar kluster i Azure Kubernetes service (AKS) behöver du ofta isolera team och arbets belastningar. Med Kubernetes Scheduler kan du styra distributionen av beräknings resurser eller begränsa påverkan av underhålls händelser.

I den här artikeln fokuserar vi på grundläggande funktioner för Kubernetes-schemaläggning för kluster operatörer. I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Använd resurs kvoter för att tillhandahålla en fast mängd resurser till team eller arbets belastningar
> * Begränsa effekten av planerat underhåll med hjälp av Pod-avbrott i budgetar
> * Kontrol lera om det saknas Pod resurs begär Anden och begränsningar med `kube-advisor` verktyget

## <a name="enforce-resource-quotas"></a>Framtvinga resurs kvoter

> **Vägledning och metodtips** 
> 
> Planera och tillämpa resurs kvoter på namn områdes nivå. Om poddar inte definierar resurs begär Anden och begränsningar avvisar du distributionen. Övervaka resursanvändningen och justera kvoterna efter behov.

Resurs förfrågningar och-gränser placeras i pod-specifikationen. Gränser används av Kubernetes Scheduler vid distributions tiden för att hitta en tillgänglig nod i klustret. Gränser och begär Anden fungerar på individuell Pod-nivå. Mer information om hur du definierar dessa värden finns i [definiera resurs begär Anden och begränsningar för Pod][resource-limits]

För att tillhandahålla ett sätt att reservera och begränsa resurser i en utvecklings grupp eller ett projekt, bör du använda *resurs kvoter*. Dessa kvoter definieras i ett namn område och kan användas för att ange kvoter på följande sätt:

* **Beräknings resurser**, till exempel processor och minne, eller GPU: er.
* **Lagrings resurser**, inklusive det totala antalet volymer eller mängden disk utrymme för en specifik lagrings klass.
* **Antal objekt**, till exempel maximalt antal hemligheter, tjänster och jobb kan skapas.

Kubernetes överallokerar inte resurser. När din sammanlagda resurs begär ande totalt skickar den tilldelade kvoten, kommer alla ytterligare distributioner att Miss lyckas.

När du definierar resurs kvoter måste alla poddar som skapats i namn rymden tillhandahålla gränser eller begär anden i sina Pod-specifikationer. Om de inte anger dessa värden kan du avvisa distributionen. I stället kan du [Konfigurera standardvärden och gränser för ett namn område][configure-default-quotas].

Följande exempel på YAML-manifest med namnet *dev-app-team-kvoter. yaml* ställer in en hård gräns på totalt *10* processorer, *20Gi* minne och *10* poddar:

```yaml
apiVersion: v1
kind: ResourceQuota
metadata:
  name: dev-app-team
spec:
  hard:
    cpu: "10"
    memory: 20Gi
    pods: "10"
```

Den här resurs kvoten kan tillämpas genom att ange namn området, till exempel *dev-Apps*:

```console
kubectl apply -f dev-app-team-quotas.yaml --namespace dev-apps
```

Samar beta med dina program utvecklare och ägare för att förstå deras behov och tillämpa lämpliga resurs kvoter.

Mer information om tillgängliga resurs objekt, omfattningar och prioriteringar finns [i resurs kvoter i Kubernetes][k8s-resource-quotas].

## <a name="plan-for-availability-using-pod-disruption-budgets"></a>Planera för tillgänglighet med hjälp av Pod-avbrott i budgetar

> **Vägledning och metodtips** 
>
> Om du vill behålla tillgängligheten för program definierar du Pod-avbrott (PDBs) för att se till att det finns ett minsta antal poddar i klustret.

Det finns två störande händelser som gör att poddar tas bort:

### <a name="involuntary-disruptions"></a>Infrivilliga avbrott

*Infrivilliga avbrott* är händelser utöver den typiska kontrollen av kluster operatören eller program ägaren. Omfattar
* Maskin varu problem på den fysiska datorn
* Kernel-panik
* Borttagning av en virtuell Node-dator

Infrivilliga avbrott kan begränsas av:
* Använda flera repliker av din poddar i en distribution. 
* Köra flera noder i AKS-klustret. 

### <a name="voluntary-disruptions"></a>Frivilliga avbrott

*Frivilliga avbrott* är händelser som begärs av kluster operatören eller program ägaren. Omfattar
* Kluster uppgraderingar
* Uppdaterad distributionsmall
* Ta bort en Pod av misstag

Kubernetes tillhandahåller *Pod avbrott i budget* för frivilliga avbrott, så att du kan planera för hur distributioner eller replik uppsättningar reagerar när en frivillig störnings händelse inträffar. Med hjälp av Pod avbrotts budget kan kluster operatörer definiera minst antal otillgängliga resurser eller maximalt antal otillgängliga resurser. 

Om du uppgraderar ett kluster eller uppdaterar en distributionsmall, schemalägger Kubernetes Scheduler extra poddar på andra noder innan du tillåter att frivilliga avbrott inträffar. Scheduler väntar på att starta om en nod tills det definierade antalet poddar har schemalagts på andra noder i klustret.

Nu ska vi titta på ett exempel på en replik uppsättning med fem poddar som kör NGINX. Poddar i replik uppsättningen tilldelas etiketten `app: nginx-frontend` . Under en frivillig störnings händelse, till exempel en kluster uppgradering, vill du se till att minst tre poddar fortsätter att köras. Följande YAML-manifest för ett *PodDisruptionBudget* -objekt definierar dessa krav:

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   minAvailable: 3
   selector:
    matchLabels:
      app: nginx-frontend
```

Du kan också definiera ett procent värde, till exempel *60%*, som gör att du automatiskt kan kompensera för replik uppsättningen för att skala upp antalet poddar.

Du kan definiera ett maximalt antal otillgängliga instanser i en replik uppsättning. En procents ATS för den högsta otillgängliga poddar kan också definieras. Följande Pod-avbrott i budget YAML-manifestet definierar att det inte finns fler än två poddar i replik uppsättningen:

```yaml
apiVersion: policy/v1beta1
kind: PodDisruptionBudget
metadata:
   name: nginx-pdb
spec:
   maxUnavailable: 2
   selector:
    matchLabels:
      app: nginx-frontend
```

När din POD-störning har definierats skapar du den i ditt AKS-kluster som med andra Kubernetes-objekt:

```console
kubectl apply -f nginx-pdb.yaml
```

Samar beta med dina program utvecklare och ägare för att förstå deras behov och tillämpa lämpliga Pod-avbrott i budgeten.

Mer information om hur du använder Pod avbrotts budgetar finns i [Ange en avbrotts budget för programmet][k8s-pdbs].

## <a name="regularly-check-for-cluster-issues-with-kube-advisor"></a>Sök regelbundet efter kluster problem med Kube-Advisor

> **Vägledning och metodtips** 
>
> Kör regelbundet den senaste versionen av `kube-advisor` verktyget för öppen källkod för att identifiera problem i klustret. Om du använder resurs kvoter i ett befintligt AKS-kluster, kör `kube-advisor` först för att hitta poddar som inte har några resurs begär Anden och gränser definierade.

Verktyget [Kube-Advisor][kube-advisor] är ett associerat AKS-projekt med öppen källkod som söker igenom ett Kubernetes-kluster och rapporterar problem som identifierats. `kube-advisor` visar att det är användbart att identifiera poddar utan resurs begär Anden och begränsningar.

`kube-advisor`Verktyget kan rapportera om resurs förfrågningar och gränser som saknas i PodSpecs för Windows-och Linux-program, men själva verktyget måste vara schemalagt på en Linux-pod. Schemalägg en pod som ska köras på en adresspool med ett särskilt operativ system med hjälp av en [Node-selektor][k8s-node-selector] i pod-konfigurationen.

Det kan vara svårt att spåra poddar utan att ange resurs begär Anden och begränsningar i ett AKS-kluster som är värd för flera utvecklings team och program. Vi rekommenderar `kube-advisor` att du regelbundet kör AKS-kluster, särskilt om du inte tilldelar resurs kvoter till namn områden.

## <a name="next-steps"></a>Nästa steg

Den här artikeln fokuserar på Basic Kubernetes Scheduler-funktioner. Mer information om kluster åtgärder i AKS finns i följande metod tips:

* [Flera innehavare och isolering av kluster][aks-best-practices-cluster-isolation]
* [Avancerade funktioner i Kubernetes Scheduler][aks-best-practices-advanced-scheduler]
* [Autentisering och auktorisering][aks-best-practices-identity]

<!-- EXTERNAL LINKS -->
[k8s-resource-quotas]: https://kubernetes.io/docs/concepts/policy/resource-quotas/
[configure-default-quotas]: https://kubernetes.io/docs/tasks/administer-cluster/manage-resources/memory-default-namespace/
[kube-advisor]: https://github.com/Azure/kube-advisor
[k8s-pdbs]: https://kubernetes.io/docs/tasks/run-application/configure-pdb/

<!-- INTERNAL LINKS -->
[resource-limits]: developer-best-practices-resource-management.md#define-pod-resource-requests-and-limits
[aks-best-practices-cluster-isolation]: operator-best-practices-cluster-isolation.md
[aks-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[aks-best-practices-identity]: operator-best-practices-identity.md
[k8s-node-selector]: concepts-clusters-workloads.md#node-selectors
