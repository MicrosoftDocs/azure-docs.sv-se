---
title: Planera och Distribuera Azure Arc-aktiverade Kubernetes
services: azure-arc
ms.service: azure-arc
ms.date: 04/12/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Registrera ett stort antal kluster i Azure Arc-aktiverade Kubernetes för konfigurations hantering
ms.openlocfilehash: 4b34cee08db508728f01d262ae4b1ee4ed4754e1
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315618"
---
# <a name="plan-and-deploy-azure-arc-enabled-kubernetes"></a>Planera och Distribuera Azure Arc-aktiverade Kubernetes

Distribution av en IT-infrastruktur eller ett affärs program är en utmaning för alla företag. För att förhindra eventuella oväntade överraskningar eller oplanerade kostnader måste du noggrant planera för det för att se till att du är så klar som möjligt. En sådan plan bör identifiera design-och distributions kriterier som måste uppfyllas för att slutföra uppgifterna.

För att distributionen ska fortsätta smidigt bör ditt abonnemang skapa en tydlig förståelse av:

* Roller och ansvars områden.
* Inventering av alla Kubernetes-kluster
* Uppfylla nätverks kraven.
* Den kunskaps uppsättning och utbildning som krävs för att aktivera lyckad distribution och pågående hantering.
* Godkännande kriterier och hur du spårar dess framgång.
* Verktyg eller metoder som ska användas för att automatisera distributioner.
* Identifierade risker och minsknings planer för att undvika fördröjningar och störningar.
* Undvik avbrott under distributionen.
* Vad är eskaleringen när ett stort problem uppstår?

Syftet med den här artikeln är att se till att du är för beredd för en lyckad distribution av Azure Arc-aktiverade Kubernetes över flera produktions kluster i din miljö.

## <a name="prerequisites"></a>Förutsättningar

* Ett befintligt Kubernetes-kluster. Om du inte har ett kan du skapa ett kluster med något av följande alternativ:
    - [Kubernetes i Docker (Natura)](https://kind.sigs.k8s.io/)
    - Skapa ett Kubernetes-kluster med Docker för [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) eller [Windows](https://docs.docker.com/docker-for-windows/#kubernetes)
    - Självhanterat Kubernetes-kluster med hjälp av [kluster-API](https://cluster-api.sigs.k8s.io/user/quick-start.html)

* Datorerna har anslutning från ditt lokala nätverk eller en annan moln miljö till resurser i Azure, antingen direkt eller via en proxyserver. Mer information finns under [nätverks krav](quickstart-connect-cluster.md#meet-network-requirements).

* En `kubeconfig` fil som pekar på det kluster som du vill ansluta till Azure-bågen.
* Läs-och skriv behörigheter för användarens eller tjänstens huvud namn skapar resurs typen Azure Arc Enabled för Kubernetes `Microsoft.Kubernetes/connectedClusters` .

## <a name="pilot"></a>Pilot

Innan du distribuerar till alla produktions kluster börjar du med att utvärdera den här distributions processen innan du inför den i stor miljö. För en pilot kan du identifiera en representativ sampling av kluster som inte är viktiga för dina företag som kan driva verksamhet. Du bör vara säker på att få tillräckligt med tid för att köra piloten och bedöma dess påverkan: Vi rekommenderar cirka 30 dagar.

Upprätta en formell plan som beskriver omfattningen och detaljerna i piloten. Följande exempel plan bör hjälpa dig att komma igång.

* **Mål** – beskriver de affärs-och teknik driv rutiner som ledde till beslutet om att en pilot är nödvändig.
* **Urvalskriterier** – anger de kriterier som används för att välja vilka aspekter av lösningen som visas via en pilot.
* **Omfattning** – täcker lösnings komponenter, förväntat schema, varaktigheten för piloten och antalet kluster som ska riktas mot målet.
* **Framgångs kriterier och mått** – definiera pilotens lyckade kriterier och de åtgärder som används för att fastställa nivån på framgång.
* **Utbildnings plan** – beskriver planen för utbildning av system tekniker, administratörer osv. som är nya för Azure och IT-tjänster under piloten.
* **Över gångs plan** – beskriver strategin och kriterierna som används för att vägleda över gången från pilot till produktion.
* **Ångring** – beskriver procedurerna för att återställa en pilot till för distributions tillstånd.
* **Risker** – visar alla identifierade risker för att genomföra piloten och kopplad till produktions distributionen.

## <a name="phase-1-build-a-foundation"></a>Fas 1: bygga en grund

I den här fasen utför system tekniker eller administratörer viktiga aktiviteter som skapar resurs grupper, taggar, roll tilldelningar så att Azure-bågen aktiverade Kubernetes-resurser kan sedan skapas och hanteras.

|Uppgift |Detalj |Varaktighet |
|-----|-------|---------|
| [Skapa en resurs grupp](../../azure-resource-manager/management/manage-resource-groups-portal.md#create-resource-groups) | En dedikerad resurs grupp som endast inkluderar Azure Arc-aktiverade Kubernetes-resurser och centraliserad hantering och övervakning av dessa resurser. | En timme |
| Använd [taggar](../../azure-resource-manager/management/tag-resources.md) för att organisera datorer. | Utvärdera och utveckla en anpassad [taggnings strategi](/azure/cloud-adoption-framework/decision-guides/resource-tagging/). På så sätt kan du minska komplexiteten med att hantera Azure Arc-aktiverade Kubernetes-resurser och förenkla fatta beslut om hantering. | En dag |
| Identifiera [konfigurationer](tutorial-use-gitops-connected-cluster.md) för GitOps | Identifiera de program-eller konfigurations bas `PodSecurityPolicy` linjer `NetworkPolicy` som du vill distribuera till dina kluster | En dag |
| [Utveckla en Azure policy](../../governance/policy/overview.md) styrnings plan | Ta reda på hur du implementerar styrning av Azure Arc-aktiverade Kubernetes-kluster i prenumerations-eller resurs grupps omfånget med Azure Policy. | En dag |
| Konfigurera [rollbaserad åtkomst kontroll](../../role-based-access-control/overview.md) (RBAC) | Utveckla en åtkomst plan för att identifiera vem som har Läs-/Skriv behörighet för dina kluster | En dag |

## <a name="phase-2-deploy-azure-arc-enabled-kubernetes"></a>Fas 2: Distribuera Azure Arc-aktiverade Kubernetes

I den här fasen ansluter vi dina Kubernetes-kluster till Azure:

|Uppgift |Detalj |Varaktighet |
|-----|-------|---------|
| [Anslut ditt första Kubernetes-kluster till Azure-bågen](quickstart-connect-cluster.md) | Som en del av att ansluta ditt första kluster till Azure-bågen konfigurerar du din onboarding-miljö med alla verktyg som krävs, till exempel Azure CLI, Helm och `connectedk8s` tillägg för Azure CLI. | 15 minuter |
| [Skapa tjänstens huvudnamn](create-onboarding-service-principal.md) | Skapa ett huvud namn för tjänsten för att ansluta Kubernetes-kluster icke-interaktivt med Azure CLI eller PowerShell. | En timme |


## <a name="phase-3-manage-and-operate"></a>Fas 3: hantera och arbeta

I den här fasen distribuerar vi program och konfigurations bas linjer till dina Kubernetes-kluster.

|Uppgift |Detalj |Varaktighet |
|-----|-------|---------|
|[Skapa konfigurationer](tutorial-use-gitops-connected-cluster.md) i klustren | Skapa konfigurationer för att distribuera dina program på din Azure Arc-aktiverad Kubernetes-resurs. | 15 minuter |
|[Använd Azure policy](use-azure-policy.md) för at-skalad tillämpning av konfigurationer | Skapa princip tilldelningar för att automatisera distributionen av konfigurations bas linjer i alla kluster under en prenumeration eller resurs grupps omfång. | 15 minuter |
| [Uppgradera Azure-båg agenter](agent-upgrade.md) | Om du har inaktiverat automatisk uppgradering av agenter i klustren uppdaterar du agenterna manuellt till den senaste versionen för att se till att du har de senaste säkerhets-och fel korrigeringarna. | 15 minuter |

## <a name="next-steps"></a>Nästa steg

* Använd vår snabb start för att [ansluta ett Kubernetes-kluster till Azure-bågen](./quickstart-connect-cluster.md).
* [Skapa konfigurationer](./tutorial-use-gitops-connected-cluster.md) på ditt Azure Arc-aktiverade Kubernetes-kluster.
* [Använd Azure policy för att tillämpa konfigurationer i skala](./use-azure-policy.md).