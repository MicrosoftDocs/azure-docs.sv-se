---
title: Reparera AKS-noder (Azure Kubernetes service) automatiskt
description: Lär dig mer om funktionen för automatisk reparation av noder och hur AKS åtgärdar brutna arbetsnoder.
services: container-service
ms.topic: conceptual
ms.date: 03/11/2021
ms.openlocfilehash: 341aef394a3784edbc0acd91dad396c9794da3d0
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105212"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Automatisk reparation av Azure Kubernetes service-noden (AKS)

AKS övervakar kontinuerligt hälso tillståndet för arbetsnoder och utför automatisk reparation av noden om de blir felaktiga. Azure Virtual Machine (VM)-plattformen [utför underhåll på virtuella datorer][vm-updates] som har problem. 

AKS och virtuella Azure-datorer fungerar tillsammans för att minimera tjänst avbrott i kluster.

I det här dokumentet får du lära dig hur automatisk reparation av noder fungerar för både Windows-och Linux-noder. 

## <a name="how-aks-checks-for-unhealthy-nodes"></a>Hur AKS söker efter felaktiga noder

AKS använder följande regler för att avgöra om en nod är felfri och behöver reparera: 
* Noden rapporterar **Notrappstegs** status på efterföljande kontroller inom en tidsram på 10 minuter.
* Noden rapporterar ingen status inom 10 minuter.

Du kan kontrol lera hälso tillståndet för dina noder manuellt med kubectl.

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>Så här fungerar automatisk reparation

> [!Note]
> AKS initierar reparations åtgärder med användar kontot **AKS-remediator**.

Om AKS identifierar en felaktig nod som inte är felfri i 10 minuter vidtar AKS följande åtgärder:

1. Starta om noden.
1. Om omstarten Miss lyckas kan du återställa avbildningen av noden.
1. Om avbildningen Miss lyckas skapar du och återskapar en ny nod.

Alternativa reparationer undersökas av AKS-tekniker om automatisk reparation Miss lyckas. 

Om AKS hittar flera felaktiga noder under en hälso kontroll repare ras varje nod individuellt innan en ny reparation påbörjas.

## <a name="next-steps"></a>Nästa steg

Använd [Tillgänglighetszoner][availability-zones] för att öka hög tillgänglighet med AKS-klustrets arbets belastningar.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
