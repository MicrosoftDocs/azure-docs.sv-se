---
title: Behållar instanser och behållar dirigering
description: Förstå hur Azure Container instances samverkar med behållar dirigering.
ms.topic: article
ms.date: 04/15/2019
ms.custom: mvc
ms.openlocfilehash: c7faeecfcc3a1d006cc923b48339a1242a0b9e9b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "92148591"
---
# <a name="azure-container-instances-and-container-orchestrators"></a>Azure Container Instances-och behållar dirigering

På grund av deras små storlek och program orientering är behållare lämpade för smidiga leverans miljöer och mikrotjänstbaserade arkitekturer. Uppgiften att automatisera och hantera ett stort antal behållare och hur de interagerar kallas för *dirigering*. Populära behållar dirigeringar omfattar Kubernetes, DC/OS och Docker Swarm.

Azure Container Instances innehåller några av de grundläggande schemaläggnings funktionerna i Orchestration-plattformar. Och även om det inte omfattar de tjänster med högt värde som plattformarna tillhandahåller Azure Container Instances kan komplettera dem. I den här artikeln beskrivs omfattningen av vad Azure Container Instances hanterar och hur fullständiga behållar dirigeringar kan interagera med det.

## <a name="traditional-orchestration"></a>Traditionellt dirigering

Standard definitionen för Orchestration innehåller följande uppgifter:

- **Schemaläggning**: du kan använda en behållar avbildning och en resurs förfrågan för att hitta en lämplig dator där behållaren ska köras.
- **Tillhörighet/** tilldelning av tillhörighet: ange att en uppsättning behållare ska köras nära varandra (för prestanda) eller tillräckligt långt ifrån varandra (för tillgänglighet).
- **Hälso övervakning**: Titta efter behållar fel och Schemalägg dem automatiskt.
- **Redundans**: Håll koll på vad som körs på varje dator och Schemalägg sedan om behållare från misslyckade datorer till felfria noder.
- **Skalning**: Lägg till eller ta bort behållar instanser för att matcha efter frågan, antingen manuellt eller automatiskt.
- **Nätverk**: Ange ett överordnat nätverk för att koordinera behållare att kommunicera över flera värddatorer.
- **Tjänst identifiering**: Aktivera behållare för att hitta varandra automatiskt, även när de flyttas mellan värddatorer och ändra IP-adresser.
- **Koordinerade program uppgraderingar**: hantera uppgradering av behållare för att undvika avbrott i programmet och aktivera återställning om något går fel.

## <a name="orchestration-with-azure-container-instances-a-layered-approach"></a>Dirigering med Azure Container Instances: ett skiktat tillvägagångs sätt

Azure Container Instances aktiverar ett skiktat tillvägagångs sätt för att dirigera, vilket ger alla schemaläggnings-och hanterings funktioner som krävs för att köra en enda behållare, samtidigt som Orchestrator-plattformarna hanterar aktiviteter med flera behållare ovanpå den.

Eftersom den underliggande infrastrukturen för container instances hanteras av Azure behöver en Orchestrator-plattform inte oroa sig för att hitta en lämplig värddator som kör en enda behållare. Molnets elastiskhet säkerställer att det alltid är tillgängligt. I stället kan Orchestrator fokusera på de uppgifter som fören klar utvecklingen av arkitekturer i flera behållare, inklusive skalning och koordinerade uppgraderingar.

## <a name="scenarios"></a>Scenarier

Även om Orchestrator-integrering med Azure Container Instances fortfarande är Nascent, förväntar vi dig att några olika miljöer kommer att framställas:

### <a name="orchestration-of-container-instances-exclusively"></a>Dirigering av container instanser exklusivt

Eftersom de startar snabbt och faktureras med den andra, erbjuder en miljö som baseras uteslutande på Azure Container Instances det snabbaste sättet att komma igång och hantera mycket varierande arbets belastningar.

### <a name="combination-of-container-instances-and-containers-in-virtual-machines"></a>Kombination av behållar instanser och behållare i Virtual Machines

För långvariga, stabila arbets belastningar är det vanligt vis billigare att dirigera behållare i ett kluster med dedikerade virtuella datorer än att köra samma behållare med Azure Container Instances. Behållar instanser erbjuder dock en bra lösning för att snabbt utöka och uppfylla den övergripande kapaciteten för att hantera oväntade eller korta toppar i användningen.

I stället för att skala ut antalet virtuella datorer i klustret, och sedan distribuera ytterligare behållare till dessa datorer, kan Orchestrator bara schemalägga ytterligare behållare i Azure Container Instances och ta bort dem när de inte längre behövs.

## <a name="sample-implementation-virtual-nodes-for-azure-kubernetes-service-aks"></a>Exempel på implementering: virtuella noder för Azure Kubernetes service (AKS)

Om du snabbt vill skala program arbets belastningar i ett [Azure Kubernetes service](../aks/intro-kubernetes.md) -kluster (AKS) kan du använda *virtuella noder* som skapats dynamiskt i Azure Container instances. Virtuella noder möjliggör nätverkskommunikation mellan poddar som körs i ACI och AKS-klustret. 

Virtuella noder har för närvarande stöd för Linux container instances. Kom igång med virtuella noder med hjälp av [Azure CLI](../aks/virtual-nodes-cli.md) eller [Azure Portal](../aks/virtual-nodes-portal.md).

Virtuella noder använder den [virtuella Kubelet][aci-connector-k8s] med öppen källkod för att efterlikna Kubernetes- [Kubelet][kubelet-doc] genom att registrera sig som en nod med obegränsad kapacitet. Den virtuella Kubelet skickar skapandet av [poddar][pod-doc] som behållar grupper i Azure Container instances.

I det [virtuella Kubelet](https://github.com/virtual-kubelet/virtual-kubelet) -projektet finns ytterligare exempel på hur du utökar Kubernetes-API: et till Server lös behållar plattformar.

## <a name="next-steps"></a>Nästa steg

Skapa din första behållare med Azure Container Instances med hjälp av [snabb starts guiden](container-instances-quickstart.md).

<!-- IMAGES -->

<!-- LINKS -->
[aci-connector-k8s]: https://github.com/virtual-kubelet/azure-aci
[kubelet-doc]: https://kubernetes.io/docs/admin/kubelet/
[pod-doc]: https://kubernetes.io/docs/concepts/workloads/pods/pod/