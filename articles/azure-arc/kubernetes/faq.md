---
title: Azure Arc-aktiverade Kubernetes vanliga frågor och svar
services: azure-arc
ms.service: azure-arc
ms.date: 02/19/2021
ms.topic: conceptual
author: shashankbarsin
ms.author: shasb
description: Den här artikeln innehåller en lista med vanliga frågor om Azure Arc-aktiverade Kubernetes
keywords: Kubernetes, båge, Azure, behållare, konfiguration, GitOps, vanliga frågor och svar
ms.openlocfilehash: d7facb842509bdf852957d46d91dfcb3912e99ba
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121719"
---
# <a name="frequently-asked-questions---azure-arc-enabled-kubernetes"></a>Vanliga frågor och svar om Azure Arc-aktiverade Kubernetes

Den här artikeln behandlar vanliga frågor om Azure Arc-aktiverade Kubernetes.

## <a name="what-is-the-difference-between-azure-arc-enabled-kubernetes-and-azure-kubernetes-service-aks"></a>Vad är skillnaden mellan Azure Arc-aktiverade Kubernetes och Azure Kubernetes service (AKS)?

AKS är det hanterade Kubernetes-erbjudandet av Azure. AKS fören klar distributionen av ett hanterat Kubernetes-kluster i Azure genom att avlasta mycket av komplexiteten och drifts kostnaderna för Azure. Eftersom Kubernetes-huvudmasters hanteras av Azure kan du bara hantera och underhålla agent-noderna.

Med Azure Arc-aktiverade Kubernetes kan du utöka Azures hanterings funktioner (t. ex. Azure Monitor och Azure Policy) genom att ansluta Kubernetes-kluster till Azure. Du behåller själva underliggande Kubernetes-kluster.

## <a name="do-i-need-to-connect-my-aks-clusters-running-on-azure-to-azure-arc"></a>Måste jag ansluta mina AKS-kluster som körs på Azure till Azure-bågen?

Nej. Alla Azure Arc-aktiverade Kubernetes-funktioner, inklusive Azure Monitor och Azure Policy (Gatekeeper), är tillgängliga på AKS (en intern resurs i Azure Resource Manager).
    
## <a name="should-i-connect-my-aks-hci-cluster-and-kubernetes-clusters-on-azure-stack-hub-and-azure-stack-edge-to-azure-arc"></a>Bör jag ansluta mitt AKS-kluster och Kubernetes-kluster på Azure Stack hubben och Azure Stack Edge till Azure-bågen?

Ja, om du ansluter ditt AKS-HCI-kluster eller Kubernetes-kluster på Azure Stack Edge eller Azure Stack Hub till Azure Arc tillhandahåller kluster med resurs representation i Azure Resource Manager. Den här resurs representationen utökar funktioner som kluster konfiguration, Azure Monitor och Azure Policy (Gatekeeper) till anslutna Kubernetes-kluster.

Om Azure Arc-Kubernetes klustret är på Azure Stack Edge, AKS på Azure Stack HCI (>= 2021 uppdatering) eller AKS på Windows Server 2019 Data Center (>= april 2021 Update) ingår Kubernetes-konfigurationen utan kostnad.

## <a name="how-to-address-expired-azure-arc-enabled-kubernetes-resources"></a>Hur ska du adressera utgången Azure Arc-aktiverade Kubernetes-resurser?

Det MSI-certifikat (Hanterad tjänstidentitet) som är associerat med din Azure Arc-aktiverad Kubernetes har ett förfallo fönster på 90 dagar. När certifikatet har gått ut beaktas resursen `Expired` och alla funktioner (till exempel konfiguration, övervakning och princip) upphör att fungera i det här klustret. För att få ditt Kubernetes-kluster att arbeta med Azure Arc igen:

1. Ta bort Azure Arc-aktiverade Kubernetes-resurser och agenter i klustret. 

    ```console
    az connectedk8s delete -n <name> -g <resource-group>
    ```

1. Återskapa den Azure Arc-aktiverade Kubernetes-resursen genom att distribuera agenter i klustret.
    
    ```console
    az connectedk8s connect -n <name> -g <resource-group>
    ```

> [!NOTE]
> `az connectedk8s delete` tar även bort konfigurationer ovanpå klustret. När `az connectedk8s connect` du har kört måste du återskapa konfigurationerna på klustret, antingen manuellt eller med Azure policy.

## <a name="if-i-am-already-using-cicd-pipelines-can-i-still-use-azure-arc-enabled-kubernetes-and-configurations"></a>Kan jag fortfarande använda Azure Arc-aktiverade Kubernetes och konfigurationer om jag redan använder CI/CD-pipeliner?

Ja, du kan fortfarande använda konfigurationer i ett kluster som tar emot distributioner via en CI/CD-pipeline. Jämfört med traditionella CI/CD-pipeliner har konfigurationer två extra fördelar:

**Avvikelse avstämning**

CI/CD-pipeline tillämpar ändringar endast en gång under pipeline-körningen. GitOps-operatören i klustret avsöker dock kontinuerligt den git-lagringsplatsen för att hämta det önskade läget för Kubernetes-resurser i klustret. Om GitOps-operatören hittar det önskade resurs läget som skiljer sig från det faktiska resurs läget i klustret, är den här driften avstämd.

**Använd GitOps i skala**

CI/CD-pipeliner är användbara för händelse drivna distributioner till ditt Kubernetes-kluster (till exempel en push-överföring till en git-lagringsplats). Men om du vill distribuera samma konfiguration till alla Kubernetes-kluster måste du manuellt konfigurera varje Kubernetes-klusters autentiseringsuppgifter till CI/CD-pipelinen. 

För Azure Arc-aktiverade Kubernetes, eftersom Azure Resource Manager hanterar dina konfigurationer, kan du automatisera skapandet av samma konfiguration för alla Azure Arc-aktiverade Kubernetes-resurser med Azure Policy, inom en prenumerations omfattning eller en resurs grupp. Den här funktionen gäller även för Azure Arc-aktiverade Kubernetes-resurser som skapats efter princip tilldelningen.

Den här funktionen använder konfigurations bas linjer (t. ex. nätverks principer, roll bindningar och Pod säkerhets principer) över hela Kubernetes-klustrets inventering för att uppfylla kraven för efterlevnad och styrning.

## <a name="next-steps"></a>Nästa steg

* Gå igenom vår snabb start för att [ansluta ett Kubernetes-kluster till Azure-bågen](./connect-cluster.md).
* Har du redan ett Kubernetes-kluster anslutet Azure Arc? [Skapa konfigurationer på ditt Arc-aktiverade Kubernetes-kluster](./use-gitops-connected-cluster.md).
* Lär dig hur du [använder Azure policy för att tillämpa konfigurationer i stor skala](./use-azure-policy.md).