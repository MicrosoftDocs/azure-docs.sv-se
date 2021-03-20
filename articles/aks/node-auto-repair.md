---
title: Reparera AKS-noder (Azure Kubernetes service) automatiskt
description: Lär dig mer om funktionen för automatisk reparation av noder och hur AKS åtgärdar brutna arbetsnoder.
services: container-service
ms.topic: conceptual
ms.date: 08/24/2020
ms.openlocfilehash: 781a1ffebb40b0cce9f18699d308db90633e8626
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2021
ms.locfileid: "89490113"
---
# <a name="azure-kubernetes-service-aks-node-auto-repair"></a>Automatisk reparation av Azure Kubernetes service-noden (AKS)

AKS kontrollerar kontinuerligt hälso tillståndet för arbetsnoder och utför automatisk reparation av noder om de blir felaktiga. Det här dokumentet informerar operatörer om hur automatiska reparations funktioner för noder beter sig för både Windows-och Linux-noder. Förutom AKS-reparationer utför Azure VM-plattformen [underhåll av Virtual Machines][vm-updates] som upplever problem. AKS och virtuella Azure-datorer fungerar tillsammans för att minimera tjänst avbrott i kluster.

## <a name="how-aks-checks-for-unhealthy-nodes"></a>Hur AKS söker efter felaktiga noder

AKS använder regler för att avgöra om en nod är ohälsosam och behöver repare ras. AKS använder följande regler för att avgöra om automatisk reparation krävs.

* Noden rapporterar status för **notrappsteg** på efterföljande kontroller inom en 10-minuters tidsram
* Noden rapporterar ingen status inom 10 minuter

Du kan kontrol lera hälso tillståndet för dina noder manuellt med kubectl.

```
kubectl get nodes
```

## <a name="how-automatic-repair-works"></a>Så här fungerar automatisk reparation

> [!Note]
> AKS initierar reparations åtgärder med användar kontot **AKS-remediator**.

Om en nod är ohälsosam enligt reglerna ovan och fortfarande är i fel tillstånd i 10 i följd, vidtas följande åtgärder.

1. Starta om noden
1. Om omstarten Miss lyckas kan du återställa avbildningen av noden
1. Om avbildningen Miss lyckas skapar du och återskapar en ny nod

Om ingen av åtgärderna lyckas, undersökas ytterligare åtgärder av AKS-tekniker. Om flera noder är i fel tillstånd under en hälso kontroll repare ras varje nod individuellt innan en ny reparation påbörjas.

## <a name="next-steps"></a>Nästa steg

Använd [Tillgänglighetszoner][availability-zones] för att öka hög tillgänglighet med AKS-klustrets arbets belastningar.

<!-- LINKS - External -->

<!-- LINKS - Internal -->
[availability-zones]: ./availability-zones.md
[vm-updates]: ../virtual-machines/maintenance-and-updates.md
