---
title: Azure Arc Enabled Kubernetes-agent arkitektur
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Den här artikeln innehåller en arkitektur översikt över Azure Arc-aktiverade Kubernetes-agenter
keywords: Kubernetes, båge, Azure, behållare
ms.openlocfilehash: f59a897e4868d7b16d0a50c28ce2142320992f71
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2021
ms.locfileid: "106442549"
---
# <a name="azure-arc-enabled-kubernetes-agent-architecture"></a>Azure Arc Enabled Kubernetes-agent arkitektur

[Kubernetes](https://kubernetes.io/) kan på egen hand distribuera arbets belastningar i behållare konsekvent i miljöer med hybrid miljö och flera moln. Azure Arc-aktiverade Kubernetes fungerar dock som ett centraliserat, konsekvent kontroll plan som hanterar principer, styrning och säkerhet i heterogena-miljöer. Den här artikeln innehåller:

* En arkitektur översikt över att ansluta ett kluster till Azure-bågen.
* Anslutnings mönstret följt av agenter.
* En beskrivning av de data som utbyts mellan kluster miljön och Azure.

## <a name="deploy-agents-to-your-cluster"></a>Distribuera agenter till klustret

De flesta lokal data Center tillämpar strikta nätverks regler som förhindrar inkommande kommunikation i brand väggen för nätverks gränser. Azure Arc-aktiverade Kubernetes fungerar med dessa begränsningar genom att inte kräva inkommande portar i brand väggen och bara aktivera selektiva slut punkter för utgående kommunikation. Azure Arc-aktiverade Kubernetes-agenter initierar denna utgående kommunikation. 

![Översikt över arkitekturen](./media/architectural-overview.png)

### <a name="connect-a-cluster-to-azure-arc"></a>Ansluta ett kluster till Azure-bågen

1. Skapa ett Kubernetes-kluster på ditt val av infrastruktur (VMware vSphere, Amazon Web Services, Google Cloud Platform osv.). 

    > [!NOTE]
    > Eftersom Azure Arc-aktiverade Kubernetes för närvarande endast stöder koppling av befintliga Kubernetes-kluster till Azure Arc, krävs det att kunder skapar och hanterar livs cykeln för själva Kubernetes-klustret.  

1. Starta registreringen av Azure-bågen för ditt kluster med hjälp av Azure CLI.
    * Azure CLI använder Helm för att distribuera agent Helm-diagrammet på klustret.
    * Klusternoderna initierar en utgående kommunikation till [Microsoft container Registry](https://github.com/microsoft/containerregistry) och hämtar de avbildningar som behövs för att skapa följande agenter i `azure-arc` namn området:

        | Agent | Beskrivning |
        | ----- | ----------- |
        | `deployment.apps/clusteridentityoperator` | Azure Arc-aktiverade Kubernetes stöder för närvarande endast [systemtilldelade identiteter](../../active-directory/managed-identities-azure-resources/overview.md). `clusteridentityoperator` initierar den första utgående kommunikationen. Den här första kommunikationen hämtar det MSI-certifikat (Hanterad tjänstidentitet) som används av andra agenter för kommunikation med Azure. |
        | `deployment.apps/config-agent` | Bevakar det anslutna klustret för käll kontrollens konfigurations resurser som tillämpas på klustret. Uppdaterar kompatibilitetstillstånd. |
        | `deployment.apps/controller-manager` | Operatorer som dirigerar interaktioner mellan Azure båg-komponenter. |    
        | `deployment.apps/metrics-agent` | Samlar in Mät värden för andra Arc-agenter för att verifiera optimala prestanda. |
        | `deployment.apps/cluster-metadata-operator` | Samlar in metadata om kluster, däribland klusterversion, antal noder och version av Azure Arc-agenten. |
        | `deployment.apps/resource-sync-agent` | Synkroniserar ovan nämnda klustrade metadata till Azure. |
        | `deployment.apps/flux-logs-agent` | Samlar in loggar från flödes operatörer som distribueras som en del av käll kontroll konfigurationen. |
        | `deployment.apps/extension-manager` | Installerar och hanterar livs cykeln för tillägg Helm-diagram |  
        | `deployment.apps/clusterconnect-agent` | Reverse proxy agent som aktiverar funktionen kluster anslutning för att ge åtkomst till `apiserver` klustret. Det här är en valfri komponent som bara distribueras om `cluster-connect` funktionen är aktive rad i klustret   |
        | `deployment.apps/guard` | Autentiserings-och auktoriserings-webhook-server som används för AAD RBAC-funktion. Det här är en valfri komponent som bara distribueras om `azure-rbac` funktionen är aktive rad i klustret   |

1. När alla Azure-Arc-aktiverade Kubernetes-poddar har `Running` statusen kontrollerar du att klustret är anslutet till Azure-bågen. Du bör se:
    * En Azure Arc-aktiverad Kubernetes-resurs i [Azure Resource Manager](../../azure-resource-manager/management/overview.md). Azure spårar den här resursen som en projektion av det Kundhanterade Kubernetes-klustret, inte själva själva Kubernetes-klustret.
    * Kluster-metadata (t. ex. Kubernetes-version, agent version och antal noder) visas på den Azure Arc-aktiverade Kubernetes-resursen som metadata.

## <a name="data-exchange-between-cluster-environment-and-azure"></a>Data utbyte mellan kluster miljö och Azure

| Datatyp | Scenario | Kommunikations läge |
| --------- | -------- | ------------------ |
| Kubernetes-klusterversion | Kluster-metadata | Agenten pushar till Azure |
| Antal noder i klustret | Kluster-metadata | Agenten pushar till Azure |
| Agent version | Kluster-metadata | Agenten pushar till Azure |
| Kubernetes distributions typ | Kluster-metadata | Azure CLI push-meddelanden till Azure |
| Infrastruktur typ (AWS/GCP/vSphere/...) | Kluster-metadata | Azure CLI push-meddelanden till Azure |
| vCPU antal noder i klustret | Fakturering | Azure CLI push-meddelanden till Azure |
| Agent pulsslag | Resource Health | Agenten pushar till Azure |
| Resursförbrukning (minne/CPU) efter agenter | Diagnostik och support | Agenten pushar till Azure |
| Loggar för alla agent behållare | Diagnostik och support | Agenten pushar till Azure |
| Tillgänglighet för agent uppgradering | Agent uppgradering | Agenten hämtar från Azure |
| Önskat tillstånd för konfiguration: git-lagringsplatsens URL, flödes operator parametrar, privat nyckel, kända värdar innehåll, HTTPS-användarnamn, token eller lösen ord | Konfiguration | Agenten hämtar från Azure |
| Status för flödes operatörs installation | Konfiguration | Agenten pushar till Azure |
| Azure Policy tilldelningar som kräver Gatekeeper i klustret | Azure Policy | Agenten hämtar från Azure |
| Gransknings-och kompatibilitetsstatus för kluster princip framställningar | Azure Policy | Agenten pushar till Azure |
| Mått och loggar för kund arbets belastningar | Azure Monitor | Agenten pushar till Log Analytics arbets ytans resurs i kundens klient organisation och prenumeration |

## <a name="connectivity-status"></a>Anslutnings status

| Status | Beskrivning |
| ------ | ----------- |
| Ansluta | Azure Arc-aktiverad Kubernetes-resurs skapas i Azure Resource Manager, men tjänsten har inte tagit emot agentens pulsslag än. |
| Ansluten | Azure Arc Enabled Kubernetes service tog emot ett agent pulsslag någon gång under de senaste 15 minuterna. |
| Offline | Azure Arc-aktiverad Kubernetes-resurs har tidigare varit ansluten, men tjänsten har inte tagit emot några agent pulsslag i 15 minuter. |
| Upphörd | MSI-certifikat har ett förfallo period på 90 dagar efter att det utfärdats. När certifikatet har gått ut beaktas resursen `Expired` och alla funktioner som konfiguration, övervakning och principer slutar att fungera på det här klustret. Mer information om hur du kan adressera utgången Azure Arc-aktiverade Kubernetes-resurser finns [i artikeln FAQ (vanliga frågor och svar](./faq.md#how-to-address-expired-azure-arc-enabled-kubernetes-resources)). |

## <a name="understand-connectivity-modes"></a>Förstå anslutnings lägen

| Anslutnings läge | Beskrivning |
| ----------------- | ----------- |
| Fullständigt ansluten | Agenter kan kommunicera konsekvent med Azure med liten fördröjning i spridningen av GitOps-konfigurationer, tillämpa principer för Azure Policy och Gatekeeper och samla in arbets belastnings mått och loggar i Azure Monitor. |
| Halv anslutning | MSI-certifikatet som hämtas av `clusteridentityoperator` är giltigt i upp till 90 dagar innan certifikatet upphör att gälla. Vid förfallo datum slutar Azure Arc-Kubernetes resursen att fungera. Om du vill återaktivera alla Azure ARC-funktioner i klustret tar du bort och återskapar den Azure Arc-aktiverade Kubernetes-resursen och agenterna. Under 90 dagar ansluter du klustret minst en gång var 30: e dag. |
| Frånkopplad | Kubernetes-kluster i frånkopplade miljöer som inte kan komma åt Azure stöds för närvarande inte av Azure Arc-aktiverade Kubernetes. Om den här funktionen är av intresse för dig, skicka in eller rösta en idé om [Azure Arc: s UserVoice-forum](https://feedback.azure.com/forums/925690-azure-arc).

## <a name="next-steps"></a>Nästa steg

* Gå igenom vår snabb start för att [ansluta ett Kubernetes-kluster till Azure-bågen](./quickstart-connect-cluster.md).
* Lär dig mer om att skapa anslutningar mellan klustret och en git-lagringsplats som en [konfigurations resurs med Azure Arc-aktiverade Kubernetes](./conceptual-configurations.md).