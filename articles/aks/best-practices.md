---
title: Metod tips för Azure Kubernetes service (AKS)
description: Samling av kluster operatör och bästa metoder för utvecklare för att bygga och hantera program i Azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 12/07/2018
ms.openlocfilehash: e8f9c1946567ac3c3ea1045a216d6b0486fd959a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "94686267"
---
# <a name="cluster-operator-and-developer-best-practices-to-build-and-manage-applications-on-azure-kubernetes-service-aks"></a>Kluster operatör och bästa metoder för utvecklare för att bygga och hantera program i Azure Kubernetes service (AKS)

För att kunna skapa och köra program i Azure Kubernetes service (AKS) finns det några viktiga aspekter att förstå och implementera. Dessa områden omfattar funktioner för flera innehavare och Scheduler, kluster-och Pod säkerhet, eller verksamhets kontinuitet och haveri beredskap. Följande metod tips är grupperade för att hjälpa kluster operatörer och utvecklare att förstå överväganden för var och en av dessa områden och implementera lämpliga funktioner.

Dessa metod tips och konceptuella artiklar har skrivits tillsammans med AKS produkt grupp, teknik team och fält team, inklusive globala svarta remmar (GBBs).

## <a name="cluster-operator-best-practices"></a>Metod tips för kluster operator

Som kluster operatör arbetar tillsammans med program ägare och utvecklare för att förstå deras behov. Du kan sedan använda följande metod tips för att konfigurera AKS-kluster efter behov.

**Flera innehavare**

* [Metodtips för isolering av kluster](operator-best-practices-cluster-isolation.md)
    * Innehåller kärn komponenter för flera innehavare och logisk isolering med namn områden.
* [Metodtips för grundläggande schemaläggningsfunktioner](operator-best-practices-scheduler.md)
    * Inkluderar användning av resurs kvoter och Pod-avbrott i budgetar.
* [Metodtips för avancerade schemaläggningsfunktioner](operator-best-practices-advanced-scheduler.md)
    * Inkluderar användning av utsmakar och tolererar, Node Selector och tillhörighet och Pod tillhörighet och skydds tilldelning.
* [Metodtips för autentisering och auktorisering](operator-best-practices-identity.md)
    * Inkluderar integrering med Azure Active Directory med hjälp av Kubernetes-rollbaserad åtkomst kontroll (Kubernetes RBAC), med Azure RBAC-och Pod-identiteter.

**Säkerhet**

* [Metodtips för klustersäkerhet och uppgraderingar](operator-best-practices-cluster-security.md)
    * Innehåller skydd av åtkomst till API-servern, begränsning av behållar åtkomst och hantering av uppgraderingar och omstarter av noder.
* [Metod tips för hantering och säkerhet för behållar avbildningar](operator-best-practices-container-image-management.md)
    * I ingår att skydda avbildningen och körningarna och automatiserade versioner av bas avbildnings uppdateringar.
* [Metod tips för Pod-säkerhet](developer-best-practices-pod-security.md)
    * Innefattar att skydda åtkomsten till resurser, begränsa exponeringen av autentiseringsuppgifter och använda Pod-identiteter och digitala nyckel valv.

**Nätverk och lagring**

* [Metod tips för nätverks anslutning](operator-best-practices-network.md)
    * Innehåller olika nätverks modeller, med ingångs-och WAF-brandväggar (Web Application Firewall) och skyddar SSH-åtkomsten för noden.
* [Metodtips för lagring och säkerhetskopiering](operator-best-practices-storage.md)
    * Inkluderar att välja lämplig lagrings typ och Node-storlek, dynamiskt konfigurera volymer och säkerhets kopior av data.

**Köra företags klara arbets belastningar**

* [Metod tips för verksamhets kontinuitet och haveri beredskap](operator-best-practices-multi-region.md)
    * Inkluderar användning av region par, flera kluster med Azure Traffic Manager och geo-replikering av behållar avbildningar.

## <a name="developer-best-practices"></a>Metod tips för utvecklare

Som utvecklare eller program ägare kan du förenkla utvecklings upplevelsen och definiera behovet av program prestanda.

* [Metodtips för programutvecklare för resurshantering](developer-best-practices-resource-management.md)
    * Innefattar att definiera Pod resurs begär Anden och begränsningar, konfigurera utvecklingsverktyg och söka efter program problem.
* [Metod tips för Pod-säkerhet](developer-best-practices-pod-security.md)
    * Innefattar att skydda åtkomsten till resurser, begränsa exponeringen av autentiseringsuppgifter och använda Pod-identiteter och digitala nyckel valv.

## <a name="kubernetes--aks-concepts"></a>Kubernetes/AKS-koncept

För att hjälpa till att förstå några av funktionerna och komponenterna i dessa bästa metoder kan du även se följande konceptuella artiklar för kluster i Azure Kubernetes service (AKS):

* [Kubernetes Core-koncept](concepts-clusters-workloads.md)
* [Åtkomst och identitet](concepts-identity.md)
* [Säkerhetskoncept](concepts-security.md)
* [Nätverks koncept](concepts-network.md)
* [Lagringsalternativ](concepts-storage.md)
* [Skalnings alternativ](concepts-scale.md)

## <a name="next-steps"></a>Nästa steg

Om du behöver komma igång med AKS följer du en av snabb starterna för att distribuera ett Azure Kubernetes service-kluster (AKS) med hjälp av [Azure CLI](kubernetes-walkthrough.md) eller [Azure Portal](kubernetes-walkthrough-portal.md).
