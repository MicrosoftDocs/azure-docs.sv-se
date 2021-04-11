---
title: Kluster tillägg – Azure Arc-aktiverade Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Den här artikeln innehåller en översikt över funktionen kluster tillägg för Azure Arc-aktiverade Kubernetes
ms.openlocfilehash: 4b9a3991b51ec45e7a64acd546c031d4241c97af
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451211"
---
# <a name="cluster-extensions-on-azure-arc-enabled-kubernetes"></a>Kluster tillägg på Azure Arc-aktiverade Kubernetes

[Helm-diagram](https://helm.sh/) hjälper dig att hantera Kubernetes-program genom att tillhandahålla de bygg stenar som behövs för att definiera, installera och uppgradera även de mest komplexa Kubernetes-programmen. Funktionen kluster tillägg syftar till att bygga ovanpå paket komponenterna i Helm. Det gör det genom att tillhandahålla en Azure Resource Manager driven upplevelse för installation och livs cykel hantering av kluster tillägg som Azure Monitor och Azure Defender för Kubernetes. Funktionen kluster tillägg ger följande extra fördelar jämfört med och ovanför vad som redan finns tillgängligt i Helm-diagram:

- Få en inventering av alla kluster och de tillägg som är installerade på dessa kluster.
- Använd Azure Policy för att automatisera storskalig distribution av kluster tillägg.
- Prenumerera på versions tåg i varje tillägg.
- Konfigurera automatisk uppgradering för tillägg.
- Stöd för skapande av tilläggs instanser och livs cykel hanterings händelser för uppdatering och borttagning.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="architecture"></a>Arkitektur

[![Arkitektur ](./media/conceptual-extensions.png) för kluster tillägg](./media/conceptual-extensions.png#lightbox)

Kluster tilläggs instansen skapas som ett tillägg Azure Resource Manager resurs ( `Microsoft.KubernetesConfiguration/extensions` ) ovanpå den Azure Arc-aktiverade Kubernetes-resursen (representeras av `Microsoft.Kubernetes/connectedClusters` ) i Azure Resource Manager. Med representation i Azure Resource Manager kan du skapa en princip som söker efter alla Azure-Arc-aktiverade Kubernetes-resurser med eller utan ett särskilt kluster tillägg. När du har fastställt vilka kluster som saknar kluster tillägg med önskade egenskaps värden kan du åtgärda dessa icke-kompatibla resurser med hjälp av Azure Policy.

Den som `config-agent` körs i klustret spårar nya eller uppdaterade tilläggs resurser på den Azure Arc-aktiverade Kubernetes-resursen. Det som `extensions-manager` körs i klustret hämtar Helm-diagrammet från Azure Container Registry eller Microsoft container Registry och installerar det i klustret. 

Både- `config-agent` och- `extensions-manager` komponenterna som körs i kluster hanterar versions uppdateringar och tilläggs instansen tas bort.

> [!NOTE]
> * `config-agent` Övervakare för nya eller uppdaterade tilläggs resurser som ska vara tillgängliga på den Arc-aktiverade Kubernetes-resursen. Därför kräver agenter anslutning för det önskade läget för att kunna hämtas till klustret. Om agenter inte kan ansluta till Azure fördröjs spridningen av det önskade läget till klustret.
> * Skyddade konfigurations inställningar för ett tillägg lagras i upp till 48 timmar i Azure Arc-aktiverade Kubernetes-tjänster. Det innebär att om klustret fortfarande är frånkopplat under 48 timmar efter det att tilläggs resursen skapades på Azure, övergår tillägget över från ett `Pending` tillstånd till `Failed` State. Vi rekommenderar att du tar klustret online så regelbundet som möjligt.

## <a name="next-steps"></a>Nästa steg

* Använd vår snabb start för att [ansluta ett Kubernetes-kluster till Azure-bågen](./quickstart-connect-cluster.md).
* [Distribuera kluster tillägg](./extensions.md) på ditt Azure Arc-aktiverade Kubernetes-kluster.