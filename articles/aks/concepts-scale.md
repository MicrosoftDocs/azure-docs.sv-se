---
title: Koncept – skala program i Azure Kubernetes Services (AKS)
description: Lär dig mer om skalning i Azure Kubernetes service (AKS), inklusive horisontell Pod-autoskalning, kluster autoskalning och Azure Container Instances anslutningen.
services: container-service
ms.topic: conceptual
ms.date: 02/28/2019
ms.openlocfilehash: f6d2121403361c4225a7e0e64411875d83d93194
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107305743"
---
# <a name="scaling-options-for-applications-in-azure-kubernetes-service-aks"></a>Skalningsalternativ för program i Azure Kubernetes Service

När du kör program i Azure Kubernetes service (AKS) kan du behöva öka eller minska mängden beräknings resurser. Antalet underliggande Kubernetes-noder kan också behöva ändras som antalet program instanser som du behöver ändra. Du kan också behöva etablera ett stort antal ytterligare program instanser.

Den här artikeln beskriver de viktigaste begreppen som hjälper dig att skala program i AKS:

- [Skala manuellt](#manually-scale-pods-or-nodes)
- [Horisontell Pod autoskalning (HPA)](#horizontal-pod-autoscaler)
- [Autoskalning av kluster](#cluster-autoscaler)
- [Azure Container instance-integrering (ACI) med AKS](#burst-to-azure-container-instances)

## <a name="manually-scale-pods-or-nodes"></a>Skala poddar eller noder manuellt

Du kan skala repliker (poddar) och noder manuellt för att testa hur programmet svarar på en ändring i tillgängliga resurser och tillstånd. Genom att skala resurser manuellt kan du också definiera en uppsättning resurser som ska användas för att upprätthålla en fast kostnad, till exempel antalet noder. Om du vill skala manuellt definierar du repliken eller antalet noder. Kubernetes-API: et schemalägger sedan att skapa ytterligare poddar eller tömma noder baserat på replikering eller antal noder.

Vid skalning av noder anropar Kubernetes-API: t det relevanta Azure Compute-API: et som är kopplat till den beräknings typ som används av klustret. Exempel: för kluster som bygger på VM Scale Sets logiken för att välja vilka noder som ska tas bort bestäms av VM Scale Sets-API: et. Mer information om hur noder väljs för borttagning vid nedskalning finns i [vanliga frågor och svar om VMSS](../virtual-machine-scale-sets/virtual-machine-scale-sets-faq.yml#if-i-reduce-my-scale-set-capacity-from-20-to-15--which-vms-are-removed-).

För att komma igång med manuell skalning av poddar och noder, se [skala program i AKS][aks-scale].

## <a name="horizontal-pod-autoscaler"></a>Horisontell autoskalning av poddar

Kubernetes använder den vågräta Pod autoskalning (HPA) för att övervaka resurs behovet och skalar automatiskt antalet repliker. Som standard kontrollerar autoskalning i horisontella Pod mått-API var 30: e sekund för alla nödvändiga ändringar i replik antalet. När ändringar krävs ökas eller minskas antalet repliker i enlighet med detta. Horisontell Pod autoskalning fungerar med AKS-kluster som har distribuerat mått servern för Kubernetes 1.8 +.

![Kubernetes horisontell Pod autoskalning](media/concepts-scale/horizontal-pod-autoscaling.png)

När du konfigurerar den horisontella Pod autoskalning för en specifik distribution definierar du det lägsta och högsta antalet repliker som kan köras. Du definierar också måttet för att övervaka och basera eventuella skalnings beslut, till exempel processor användning.

För att komma igång med den vågräta Pod-autoskalning i AKS, se [AutoScale-poddar i AKS][aks-hpa].

### <a name="cooldown-of-scaling-events"></a>Cooldown för skalnings händelser

I takt med att den vågräta Pod-autoskalning kontrollerar Metrics API var 30: e sekund kanske tidigare skalnings händelser inte har slutförts innan en annan kontroll görs. Det här problemet kan orsaka att den vågräta Pod automatiskt skalar för att ändra antalet repliker innan föregående skalnings händelse kan ta emot program arbets belastning och resurs kraven för att justera detta.

För att minimera tävlings händelser anges ett fördröjnings värde. Det här värdet anger hur länge det vågräta Pod måste vänta efter en skalnings händelse innan en annan skalnings händelse kan utlösas. Detta innebär att det nya replik antalet börjar gälla och att API: et för att avspegla den distribuerade arbets belastningen. Det finns [ingen fördröjning för att skala upp händelser från och med Kubernetes 1,12](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/#support-for-cooldown-delay), men fördröjningen för att skala ned händelser är standardvärdet 5 minuter.

För närvarande kan du inte finjustera dessa cooldown-värden från standardvärdet.

## <a name="cluster-autoscaler"></a>Autoskalning av kluster

För att svara på ändring av Pod-krav har Kubernetes en kluster autoskalning som justerar antalet noder baserat på de begärda beräknings resurserna i Node-poolen. Som standard kontrollerar kluster autoskalning den metriska API-servern var 10: e sekund för alla nödvändiga ändringar i antalet noder. Om klustrets autoskalning avgör att en ändring krävs, ökar eller minskar du antalet noder i ditt AKS-kluster. Kluster autoskalning fungerar med Kubernetes RBAC-aktiverade AKS-kluster som kör Kubernetes 1.10. x eller senare.

![Kubernetes-kluster autoskalning](media/concepts-scale/cluster-autoscaler.png)

Kluster autoskalning används vanligt vis tillsammans med den horisontella Pod autoskalning. När den vågräta Pod används ökas eller minskas antalet poddar baserat på program efter frågan och klustrets autoskalning justerar antalet noder som behövs för att köra ytterligare poddar.

För att komma igång med klustrets autoskalning i AKS, se [kluster autoskalning på AKS][aks-cluster-autoscaler].

### <a name="scale-out-events"></a>Skala ut händelser

Om en nod inte har tillräckligt med beräknings resurser för att köra en begärd pod, kan Pod inte passera genom schemaläggnings processen. Pod kan inte startas om inte ytterligare beräknings resurser är tillgängliga i Node-poolen.

När poddar i klustret inte kan schemaläggas på grund av resurs begränsningar för en resurspool, ökar antalet noder i noden för att tillhandahålla ytterligare beräknings resurser. När de ytterligare noderna har distribuerats och är tillgängliga för användning i Node-poolen, schemaläggs poddar sedan att köras på dem.

Om ditt program behöver skala snabbt kan vissa poddar finnas kvar i ett tillstånd som väntar på att schemaläggas tills de ytterligare noder som distribueras av klustret autoskalning kan godkänna den schemalagda poddar. För program som har höga burst-krav kan du skala med virtuella noder och Azure Container Instances.

### <a name="scale-in-events"></a>Skala i händelser

Klustrets autoskalning övervakar också Pod schemaläggnings status för noder som inte nyligen har tagit emot nya tids planerings begär Anden. Det här scenariot indikerar att Node-poolen har fler beräknings resurser än vad som krävs och antalet noder kan minskas.

En nod som skickar ett tröskelvärde som inte längre behövs i 10 minuter är schemalagd för borttagning. När den här situationen inträffar är poddar schemalagda att köras på andra noder i Node-poolen och klustrets autoskalning minskar antalet noder.

Dina program kan drabbas av vissa avbrott när poddar är schemalagda för olika noder när den automatiska skalnings tjänsten för klustret minskar antalet noder. Undvik avbrott genom att undvika program som använder en enda Pod-instans.

## <a name="burst-to-azure-container-instances"></a>Burst till Azure Container Instances

Om du snabbt vill skala ditt AKS-kluster kan du integrera med Azure Container Instances (ACI). Kubernetes har inbyggda komponenter för skalning av antalet repliker och noder. Men om ditt program behöver skala snabbt, kan den horisontella Pod autoskalning schemalägga mer poddar än vad som finns i de befintliga beräknings resurserna i Node-poolen. Om det här scenariot har kon figurer ATS utlöses det här scenariot för att distribuera ytterligare noder i Node-poolen, men det kan ta några minuter för noderna att etablera och tillåta Kubernetes Scheduler att köra poddar på dem.

![Kubernetes burst-skalning till ACI](media/concepts-scale/burst-scaling.png)

Med ACI kan du snabbt distribuera behållar instanser utan ytterligare infrastruktur belastning. När du ansluter med AKS blir ACI ett säkert, logiskt tillägg för ditt AKS-kluster. Komponenten [virtuella noder][virtual-nodes-cli] , som baseras på [Virtual Kubelet][virtual-kubelet], installeras i ditt AKS-kluster som presenterar ACI som en virtuell Kubernetes-nod. Kubernetes kan sedan schemalägga poddar som körs som ACI-instanser via virtuella noder, inte som poddar på VM-noder direkt i ditt AKS-kluster.

Programmet kräver ingen ändring för att använda virtuella noder. Distributioner kan skalas över AKS och ACI och utan fördröjning som kluster autoskalning distribuerar nya noder i ditt AKS-kluster.

Virtuella noder distribueras till ett extra undernät i samma virtuella nätverk som ditt AKS-kluster. Med den här virtuella nätverks konfigurationen kan trafiken mellan ACI och AKS skyddas. Precis som ett AKS-kluster är en ACI-instans en säker, logisk beräknings resurs som är isolerad från andra användare.

## <a name="next-steps"></a>Nästa steg

För att komma igång med skalnings program, följ först [snabb starten för att skapa ett AKS-kluster med Azure CLI][aks-quickstart]. Du kan sedan starta manuellt eller automatiskt skala program i ditt AKS-kluster:

- Skala [poddar][aks-manually-scale-pods] eller [noder][aks-manually-scale-nodes] manuellt
- Använd den [vågräta Pod autoskalning][aks-hpa]
- Använd [kluster autoskalning][aks-cluster-autoscaler]

Mer information om kärn Kubernetes-och AKS-koncept finns i följande artiklar:

- [Kubernetes/AKS-kluster och arbets belastningar][aks-concepts-clusters-workloads]
- [Kubernetes/AKS-åtkomst och identitet][aks-concepts-identity]
- [Kubernetes/AKS-säkerhet][aks-concepts-security]
- [Kubernetes/AKS virtuella nätverk][aks-concepts-network]
- [Kubernetes/AKS-lagring][aks-concepts-storage]

<!-- LINKS - external -->
[virtual-kubelet]: https://virtual-kubelet.io/

<!-- LINKS - internal -->
[aks-quickstart]: kubernetes-walkthrough.md
[aks-hpa]: tutorial-kubernetes-scale.md#autoscale-pods
[aks-scale]: tutorial-kubernetes-scale.md
[aks-manually-scale-pods]: tutorial-kubernetes-scale.md#manually-scale-pods
[aks-manually-scale-nodes]: tutorial-kubernetes-scale.md#manually-scale-aks-nodes
[aks-cluster-autoscaler]: ./cluster-autoscaler.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-network]: concepts-network.md
[virtual-nodes-cli]: virtual-nodes-cli.md
