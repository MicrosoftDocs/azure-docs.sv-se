---
title: Vad är Azure Application Gateway ingress ingångs kontroll?
description: Den här artikeln innehåller en introduktion till Application Gateway ingångs kontroll av kontrollanten.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: article
ms.date: 03/02/2021
ms.author: caya
ms.openlocfilehash: 2564fd38056241fd48f58f5f6039bf64f92b6741
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2021
ms.locfileid: "101714416"
---
# <a name="what-is-application-gateway-ingress-controller"></a>Vad är Application Gateway ingress-styrenhet?
Application Gateway ingress (AGIC) är ett Kubernetes-program som gör det möjligt för [Azure Kubernetes service-kunder (AKS)](https://azure.microsoft.com/services/kubernetes-service/) att utnyttja Azures inbyggda [Application Gateway](https://azure.microsoft.com/services/application-gateway/) L7-belastningsutjämnare för att exponera moln program vara till Internet. AGIC övervakar det Kubernetes-kluster som det finns på och uppdaterar kontinuerligt en Application Gateway, så att valda tjänster exponeras för Internet.

Ingångs styrenheten körs i sin egen Pod på kundens AKS. AGIC övervakar en delmängd av Kubernetes-resurser för ändringar. Status för AKS-klustret översätts till Application Gateway speciell konfiguration och tillämpas på [Azure Resource Manager (arm)](../azure-resource-manager/management/overview.md).

## <a name="benefits-of-application-gateway-ingress-controller"></a>Fördelar med Application Gateway ingress-kontrollant
AGIC bidrar till att eliminera behovet av att ha en annan belastningsutjämnare/offentlig IP framför AKS-klustret och undvika flera hopp i din Datapath innan begär Anden når AKS-klustret. Application Gateway pratar med poddar med sin privata IP-adress direkt och kräver inte NodePort-eller KubeProxy-tjänster. Detta ger också bättre prestanda för dina distributioner.

Ingångs kontroll stöds exklusivt av Standard_v2 och WAF_v2 SKU: er som också ger dig automatiska skalnings förmåner. Application Gateway kan reagera som svar på en ökning eller minskning av trafikbelastningen och skala därefter, utan att behöva använda några resurser från ditt AKS-kluster.

Genom att använda Application Gateway utöver AGIC bidrar också till att skydda ditt AKS-kluster genom att tillhandahålla TLS-princip och WAF-funktioner (Web Application Firewall).

![Azure Application Gateway + AKS](./media/application-gateway-ingress-controller-overview/architecture.png)

AGIC konfigureras via Kubernetes [ingress-resursen](https://kubernetes.io/docs/user-guide/ingress/), tillsammans med tjänsten och distributioner/poddar. Det innehåller ett antal funktioner som använder Azures inbyggda Application Gateway L7-belastningsutjämnare. Så här namnger du några:
  - URL-routning
  - Cookie-baserad tillhörighet
  - TLS-terminering
  - Slut punkt till slut punkt – TLS
  - Stöd för offentliga, privata och hybrid webbplatser
  - Integrerad brand vägg för webbaserade program

## <a name="difference-between-helm-deployment-and-aks-add-on"></a>Skillnaden mellan Helm-distribution och AKS Add-On
Det finns två sätt att distribuera AGIC för ditt AKS-kluster. Det första sättet är genom Helm; den andra är via AKS som ett tillägg. Den främsta fördelen med att distribuera AGIC som ett AKS-tillägg är att det är mycket enklare än att distribuera genom Helm. För en ny installation kan du distribuera en ny Application Gateway och ett nytt AKS-kluster med AGIC aktiverat som tillägg på en rad i Azure CLI. Tillägget är också en fullständigt hanterad tjänst som ger ytterligare fördelar som automatiska uppdateringar och ökad support. Båda sätten att distribuera AGIC (Helm och AKS-tillägg) stöds fullt ut av Microsoft. Tillägget möjliggör dessutom bättre integrering med AKS som ett första klass tillägg.

AGIC-tillägget distribueras fortfarande som en POD i kundens AKS-kluster, men det finns några skillnader mellan Helm-distributions versionen och tilläggs versionen av AGIC. Nedan visas en lista över skillnaderna mellan de två versionerna: 
  - Det går inte att ändra distributions värden för Helm i AKS-tillägget:
    - `verbosityLevel` sätts till 5 som standard
    - `usePrivateIp` anges som standard till false. Detta kan skrivas över av den [använda-privata-IP-anteckningen](ingress-controller-annotations.md#use-private-ip)
    - `shared` stöds inte för tillägg 
    - `reconcilePeriodSeconds` stöds inte för tillägg
    - `armAuth.type` stöds inte för tillägg
  - AGIC som distribueras via Helm stöder ProhibitedTargets, vilket innebär att AGIC kan konfigurera Application Gateway specifikt för AKS-kluster utan att påverka andra befintliga Server delar. AGIC-tillägget har för närvarande inte stöd för detta. 
  - Eftersom AGIC-tillägg är en hanterad tjänst kommer kunderna automatiskt att uppdateras till den senaste versionen av AGIC-tillägget, till skillnad från AGIC som distribueras via Helm där kunden måste uppdatera AGIC manuellt. 

> [!NOTE]
> Kunder kan bara distribuera ett AGIC-tillägg per AKS-kluster och varje AGIC-tillägg kan för närvarande endast ha ett Application Gateway. För distributioner som kräver mer än en AGIC per kluster eller flera AGICs som riktar sig mot en Application Gateway kan du fortsätta att använda AGIC som distribueras via Helm. 

## <a name="next-steps"></a>Nästa steg
- [**AKS Add-On Bygg-distribution**](tutorial-ingress-controller-add-on-new.md): instruktioner om hur du installerar AGIC-tillägg, AKS och Application Gateway på en tom infrastruktur.
- [**AKS Add-On brownfield-distribution**](tutorial-ingress-controller-add-on-existing.md): installera AGIC-tillägg på ett AKS-kluster med en befintlig Application Gateway.
- [**Helm Bygg-distribution**](ingress-controller-install-new.md): installera AGIC via Helm, nya AKS-kluster och nya Application Gateway på en tom infrastruktur.
- [**Helm brownfield-distribution**](ingress-controller-install-existing.md): Distribuera AGIC via Helm på ett befintligt AKS-kluster och Application Gateway.