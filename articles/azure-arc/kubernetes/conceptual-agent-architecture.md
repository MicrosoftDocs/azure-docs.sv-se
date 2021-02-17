---
title: Azure Arc Enabled Kubernetes-agent arkitektur
services: azure-arc
ms.service: azure-arc
ms.date: 02/15/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Den här artikeln innehåller en arkitektur översikt över Azure Arc-aktiverade Kubernetes-agenter
keywords: Kubernetes, båge, Azure, behållare
ms.openlocfilehash: e1f51f066598b59501b30704cb1475dd5332160e
ms.sourcegitcommit: de98cb7b98eaab1b92aa6a378436d9d513494404
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/17/2021
ms.locfileid: "100561807"
---
# <a name="azure-arc-enabled-kubernetes-agent-architecture"></a>Azure Arc Enabled Kubernetes-agent arkitektur

[Kubernetes](https://kubernetes.io/) kan användas för att distribuera arbets belastningar i behållare i hybrid miljöer och miljöer med flera moln på ett konsekvent sätt. Azure Arc-aktiverade Kubernetes kan användas som ett centraliserat kontroll plan för att konsekvent hantera principer, styrning och säkerhet i de här heterogena-miljöerna. Den här artikeln innehåller:

* En arkitektur översikt över att ansluta ett kluster till Azure-bågen.
* Anslutnings mönstret följt av agenter.
* En beskrivning av de data som utbyts mellan kluster miljön och Azure.

## <a name="deploy-agents-to-your-cluster"></a>Distribuera agenter till klustret

De flesta lokal data Center tillämpar strikta nätverks regler som förhindrar inkommande kommunikation i brand väggen som används på nätverks gränser. Azure Arc-aktiverade Kubernetes fungerar med dessa begränsningar genom att endast aktivera selektiva utgående slut punkter för utgående kommunikation och inte kräva några inkommande portar i brand väggen. Azure Arc-aktiverade Kubernetes-agenter initierar de utgående anslutningarna.

![Översikt över arkitekturen](./media/architectural-overview.png)

Anslut ett kluster till Azure-bågen med följande steg:

1. Skapa ett Kubernetes-kluster på ditt val av infrastruktur (VMware vSphere, Amazon Web Services, Google Cloud Platform osv.). 

    > [!NOTE]
    > Kunder måste skapa och hantera livs cykeln för Kubernetes-klustret själva eftersom Azure Arc-aktiverad Kubernetes för närvarande endast stöder koppling av befintliga Kubernetes-kluster till Azure-bågen.  

1. Initiera registreringen av Azure-bågen för klustret med hjälp av Azure CLI.
    * Azure CLI använder Helm för att distribuera agent Helm-diagrammet på klustret.
    * Klusternoderna initierar en utgående kommunikation till [Microsoft container Registry](https://github.com/microsoft/containerregistry) och hämtar de avbildningar som behövs för att skapa följande agenter i `azure-arc` namn området:

        | Agent | Beskrivning |
        | ----- | ----------- |
        | `deployment.apps/clusteridentityoperator` | Azure Arc-aktiverade Kubernetes stöder för närvarande endast [systemtilldelade identiteter](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). clusteridentityoperator gör den första utgående kommunikationen nödvändig för att hämta det MSI-certifikat (Managed Service Identity) som används av andra agenter för kommunikation med Azure. |
        | `deployment.apps/config-agent` | Bevakar det anslutna klustret för käll kontrollens konfigurations resurser som tillämpas på klustret och uppdaterar kompatibilitetstillstånd |
        | `deployment.apps/controller-manager` | Operatorer som dirigerar interaktioner mellan Azure båg-komponenter |    
        | `deployment.apps/metrics-agent` | Samlar in Mät värden för andra Arc-agenter för att säkerställa att dessa agenter visar optimala prestanda |
        | `deployment.apps/cluster-metadata-operator` | Samlar in kluster-metadata-kluster version, antal noder och Azure Arc agent-version |
        | `deployment.apps/resource-sync-agent` | Synkroniserar ovanstående klustrade metadata till Azure |
        | `deployment.apps/flux-logs-agent` | Samlar in loggar från flödes operatörer som distribueras som en del av käll kontroll konfigurationen |
    
1. När alla Azure-Arc-Kubernetes har Aktiver ATS i `Running` status kontrollerar du att klustret är anslutet till Azure-bågen. Du bör se:
    * En Azure Arc-aktiverad Kubernetes-resurs i [Azure Resource Manager](../../azure-resource-manager/management/overview.md). Den här resursen spåras i Azure som en projektion av det Kundhanterade Kubernetes-klustret, inte själva själva Kubernetes-klustret.
    * Klustrade metadata, t. ex. Kubernetes version, agent version och antal noder, visas på den Azure Arc-aktiverade Kubernetes-resursen som metadata.

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
| Önskat tillstånd för Configuration-git lagrings platsen-URL, flödes operator parametrar, privat nyckel, kända värdar innehåll, HTTPS-användarnamn, token/Password | Konfiguration | Agenten hämtar från Azure |
| Status för flödes operatörs installation | Konfiguration | Agenten pushar till Azure |
| Azure Policy tilldelningar som kräver Gatekeeper i klustret | Azure Policy | Agenten hämtar från Azure |
| Gransknings-och kompatibilitetsstatus för kluster princip framställningar | Azure Policy | Agenten pushar till Azure |
| Mått och loggar för kund arbets belastningar | Azure Monitor | Agenten pushar till Log Analytics arbets ytans resurs i kundens klient organisation och prenumeration |

## <a name="connectivity-status"></a>Anslutnings status

| Status | Beskrivning |
| ------ | ----------- |
| Ansluta | Azure Arc-aktiverad Kubernetes-resurs skapades i Azure Resource Manager, men tjänsten har inte tagit emot agent pulsslag än. |
| Ansluten | Azure Arc Enabled Kubernetes service tog emot ett agent pulsslag någon gång under de senaste 15 minuterna. |
| Offline | Azure Arc-aktiverad Kubernetes-resurs har tidigare varit ansluten, men tjänsten har inte tagit emot några agent pulsslag i 15 minuter. |
| Upphörd | Förvaltad tjänst identitet (MSI)-certifikat har ett förfallo period på 90 dagar efter att det har utfärdats. När certifikatet har gått ut beaktas resursen `Expired` och alla funktioner som konfiguration, övervakning och principer slutar att fungera på det här klustret. Mer information om hur du kan adressera utgången Azure Arc-aktiverade Kubernetes-resurser finns [här](./faq.md#how-to-address-expired-azure-arc-enabled-kubernetes-resources) |

## <a name="understand-connectivity-modes"></a>Förstå anslutnings lägen

| Anslutnings läge | Description |
| ----------------- | ----------- |
| Fullständigt ansluten | Agenter kan alltid kontakta Azure. Upplevelsen är idealisk i det här fallet eftersom det finns en liten fördröjning vid spridningen av konfigurationer (för GitOps), tillämpning av principer (i Azure Policy och Gatekeeper) och insamling av mått och loggar för arbets belastningar (i Azure Monitor) |
| Halv anslutning | MSI-certifikat som hämtas av `clusteridentityoperator` är giltigt i 90 dagar innan certifikatet upphör att gälla. När certifikatet har gått ut slutar Azure Arc-aktiverade Kubernetes-resursen att fungera. Ta bort och återskapa Azure Arc-aktiverade Kubernetes-resurser och agenter för att få alla ARC-funktioner att fungera i klustret. Under 90 dagar rekommenderar vi att du ansluter klustret minst en gång var 30: e dag. |
| Frånkopplad | Kubernetes-kluster i frånkopplade miljöer utan åtkomst till Azure stöds för närvarande inte av Azure Arc-aktiverade Kubernetes. Om den här funktionen är av intresse för dig, skicka in eller rösta en idé om [Azure Arc: s UserVoice-forum](https://feedback.azure.com/forums/925690-azure-arc).

## <a name="next-steps"></a>Nästa steg

* [Ansluta ett kluster till Azure-bågen](./connect-cluster.md)
* [Konceptuell översikt över konfigurationer](./conceptual-configurations.md)