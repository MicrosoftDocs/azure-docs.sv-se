---
title: Behållar säkerhet i Azure Security Center | Microsoft Docs
description: Läs mer om Azure Security Center behållar säkerhetsfunktioner.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: ea66bb5bcdd6132809804632919a120f5c93353f
ms.sourcegitcommit: 431bf5709b433bb12ab1f2e591f1f61f6d87f66c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/12/2021
ms.locfileid: "98132725"
---
# <a name="container-security-in-security-center"></a>Containersäkerhet i Security Center

Azure Security Center är en Azure-inbyggd lösning för att skydda dina behållare.

Security Center kan skydda följande behållare resurs typer:

| Resurstyp | Skydd som erbjuds av Security Center |
|:--------------------:|-----------|
| ![Kubernetes-tjänst](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png)<br>**Azure Kubernetes service-kluster (AKS)** | -Kontinuerlig utvärdering av dina AKS-klusters konfigurationer för att ge insyn i fel konfiguration och rikt linjer som hjälper dig att lösa eventuella identifierade problem.<br>[Lär dig mer om miljö härdning genom säkerhets rekommendationer](#environment-hardening).<br><br>-Hot skydd för AKS-kluster och Linux-noder. Aviseringar för misstänkta aktiviteter tillhandahålls av de valfria  [Azure Defender-Kubernetes](defender-for-kubernetes-introduction.md).<br>[Läs mer om kör tids skydd för AKS-noder och-kluster](#run-time-protection-for-aks-nodes-and-clusters).|
| ![Container värd](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png)<br>**Behållar värdar**<br>(Virtuella datorer som kör Docker) | – Kontinuerlig utvärdering av Docker-konfigurationerna för att ge insyn i fel konfiguration och rikt linjer som hjälper dig att lösa eventuella upptäckta problem med de valfria  [Azure Defender-servrarna](defender-for-servers-introduction.md).<br>[Lär dig mer om miljö härdning genom säkerhets rekommendationer](#environment-hardening).|
| ![Container Registry](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png)<br>**Azure Container Registry-register (ACR)** | – Sårbarhets bedömning och hanterings verktyg för avbildningarna i dina Azure Resource Manager-baserade ACR-register med valfria [Azure Defender för behållar register](defender-for-container-registries-introduction.md).<br>[Läs mer om att skanna behållar avbildningar för sårbarheter](#vulnerability-management---scanning-container-images). |
|||

Den här artikeln beskriver hur du kan använda Security Center, tillsammans med de valfria Azure Defender-planerna för behållar register,-servrar och Kubernetes, för att förbättra, övervaka och upprätthålla säkerheten för dina behållare och deras appar.

Du lär dig hur Security Center hjälper till med dessa kärn aspekter av behållar säkerhet:

- [Sårbarhets hantering – Skanna behållar avbildningar](#vulnerability-management---scanning-container-images)
- [Miljö härdning](#environment-hardening)
- [Kör tids skydd för AKS-noder och-kluster](#run-time-protection-for-aks-nodes-and-clusters)

Följande skärm bild visar sidan till gångs inventering och de olika behållar resurs typerna som skyddas av Security Center.

:::image type="content" source="./media/container-security/container-security-tab.png" alt-text="Behållar resurser i Security Center till gångs inventerings Sidan" lightbox="./media/container-security/container-security-tab.png":::

## <a name="vulnerability-management---scanning-container-images"></a>Sårbarhets hantering – Skanna behållar avbildningar

Aktivera [Azure Defender för behållar register](defender-for-container-registries-introduction.md)för att övervaka avbildningar i dina Azure Resource Manager-baserade Azure Container register. Security Center genomsöker alla bilder som hämtats under de senaste 30 dagarna, skickas till registret eller importeras. Den integrerade skannern tillhandahålls av den branschledande sårbara genomsöknings leverantören, Qualys.

När problem påträffas – av Qualys eller Security Center – får du ett meddelande på [Azure Defender-instrumentpanelen](azure-defender-dashboard.md). För varje sårbarhet ger Security Center rekommendationer som kan åtgärdas, tillsammans med en allvarlighets grad och vägledning för hur du kan åtgärda problemet. Mer information om Security Centers rekommendationer för behållare finns i [referens listan för rekommendationer](recommendations-reference.md#recs-compute).

Security Center filtrerar och klassificerar resultat från skannern. När en bild är felfri, Security Center Markera den som sådan. Security Center skapar endast säkerhets rekommendationer för avbildningar som har problem att lösa. Genom att meddela om det uppstår problem kan Security Center minska risken för oönskade informations aviseringar.

## <a name="environment-hardening"></a>Miljö härdning

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Kontinuerlig övervakning av Docker-konfigurationen

Azure Security Center identifierar ohanterade behållare som finns på virtuella IaaS Linux-datorer eller andra Linux-datorer som kör Docker-behållare. Security Center kontinuerligt bedömer konfigurationen av dessa behållare. Den jämför sedan dem med [Center for Internet Security (CIS) Docker-benchmark](https://www.cisecurity.org/benchmark/docker/).

Security Center innehåller hela ruleset av CIS Docker-benchmark och varnar dig om dina behållare inte uppfyller någon av kontrollerna. När det hittar felkonfigurationer genererar Security Center säkerhets rekommendationer. Använd Security Center **rekommendationer** för att se rekommendationer och åtgärda problem. Benchmark-kontroller för CIS körs inte på AKS-hanterade instanser eller Databricks-hanterade virtuella datorer.

Mer information om relevanta Security Center rekommendationer som kan visas för den här funktionen finns i [avsnittet Compute](recommendations-reference.md#recs-compute) i referens tabellen rekommendationer.

När du utforskar säkerhets problemen för en virtuell dator innehåller Security Center ytterligare information om behållarna på datorn. Sådan information omfattar Docker-versionen och antalet avbildningar som körs på värden. 

Om du vill övervaka ohanterade behållare som finns på virtuella IaaS Linux-datorer aktiverar du de valfria [Azure Defender för-servrarna](defender-for-servers-introduction.md).


### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>Kontinuerlig övervakning av dina Kubernetes-kluster
Security Center fungerar tillsammans med Azure Kubernetes service (AKS), Microsofts hanterade behållar Orchestration-tjänst för utveckling, distribution och hantering av program i behållare.

AKS tillhandahåller säkerhets kontroller och insyn i säkerhets position i dina kluster. Security Center använder dessa funktioner för att:
* Övervaka kontinuerligt konfigurationen av dina AKS-kluster
* Skapa säkerhets rekommendationer som är justerade med bransch standarder

Mer information om relevanta Security Center rekommendationer som kan visas för den här funktionen finns i [avsnittet Compute](recommendations-reference.md#recs-compute) i referens tabellen rekommendationer.

###  <a name="workload-protection-best-practices-using-kubernetes-admission-control"></a>Bästa metoder för arbets belastnings skydd med Kubernetes-åtkomstkontroll

För ett paket med rekommendationer för att skydda arbets belastningarna för dina Kubernetes-behållare, installerar du  **Azure policy-tillägget för Kubernetes**. Du kan också distribuera tillägget automatiskt enligt beskrivningen i [Aktivera automatisk etablering av tillägg](security-center-enable-data-collection.md#enable-auto-provisioning-of-extensions). När automatisk etablering för tillägget har angetts till "på" aktive ras tillägget som standard i alla befintliga och framtida kluster (som uppfyller installations kraven för tillägg).

Som det förklaras på [sidan Azure policy för Kubernetes](../governance/policy/concepts/policy-for-kubernetes.md), utökar tillägget en [Gatekeeper v3](https://github.com/open-policy-agent/gatekeeper)-   åtkomst kontroll för öppen källkod för [öppen Policy Agent](https://www.openpolicyagent.org/). Kubernetes-styrenheter är plugin-program som framtvingar hur klustren används. Tillägget registrerar sig som en webhook för att Kubernetes-åtkomstkontroll och gör det möjligt att tillämpa i skala och skydda dina kluster på ett centraliserat, konsekvent sätt. 

Med tillägget i AKS-klustret kommer varje begäran till Kubernetes API-servern att övervakas mot den fördefinierade uppsättningen rekommenderade metoder innan den sparas i klustret. Du kan sedan konfigurera för att **tillämpa** bästa praxis och bestämma dem för framtida arbets belastningar. 

Du kan till exempel bestämma att privilegierade behållare inte ska skapas och eventuella framtida förfrågningar om detta kommer att blockeras.

Läs mer i [skydda dina Kubernetes-arbetsbelastningar](kubernetes-workload-protections.md).


## <a name="run-time-protection-for-aks-nodes-and-clusters"></a>Kör tids skydd för AKS-noder och-kluster

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]



## <a name="next-steps"></a>Nästa steg

I den här översikten har du lärt dig om kärn elementen i behållar säkerhet i Azure Security Center. Information om relaterade material finns i:

- [Introduktion till Azure Defender för Kubernetes](defender-for-kubernetes-introduction.md)
- [Introduktion till Azure Defender för containerregister](defender-for-container-registries-introduction.md)