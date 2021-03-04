---
title: Azure Defender för Kubernetes – fördelar och funktioner
description: Lär dig mer om fördelarna och funktionerna i Azure Defender för Kubernetes.
author: memildin
ms.author: memildin
ms.date: 02/07/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 83d0215ebca9d60d61937cb20bb82c7ccb30aac1
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2021
ms.locfileid: "102100635"
---
# <a name="introduction-to-azure-defender-for-kubernetes"></a>Introduktion till Azure Defender för Kubernetes

Azure Kubernetes service (AKS) är Microsofts hanterade tjänst för utveckling, distribution och hantering av program i behållare.

Azure Security Center-och AKS utgör ett Kubernetes säkerhets erbjudande i molnet med miljö härdning, skydd av arbets belastning och körnings skydd som beskrivs i [behållar säkerhet i Security Center](container-security.md).

För hot identifiering för dina Kubernetes-kluster aktiverar du **Azure Defender för Kubernetes**.

Hot identifiering på värdnivå för dina Linux AKS-noder är tillgängligt om du aktiverar [Azure Defender för servrar](defender-for-servers-introduction.md) och dess Log Analytics-agent. Men om ditt AKS-kluster har distribuerats på en skalnings uppsättning för virtuella datorer, stöds inte Log Analytics agent för närvarande.

## <a name="availability"></a>Tillgänglighet

|Aspekt|Information|
|----|:----|
|Versions tillstånd:|Allmän tillgänglighet (GA)|
|Priset|**Azure Defender for Kubernetes** faktureras enligt [Security Center prissättning](https://azure.microsoft.com/pricing/details/security-center/)|
|Nödvändiga roller och behörigheter:|**Säkerhets administratören** kan stänga aviseringar.<br>**Säkerhets läsaren** kan visa resultat.|
|Moln|![Ja](./media/icons/yes-icon.png) Kommersiella moln<br>![Ja](./media/icons/yes-icon.png) National/suverän (US Gov, Kina gov, andra gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-kubernetes"></a>Vilka är fördelarna med Azure Defender för Kubernetes?

Azure Defender för Kubernetes tillhandahåller **hot skydd på kluster nivå** genom att övervaka dina AKS tjänster genom de loggar som hämtas av Azure Kubernetes service (AKS).

Exempel på säkerhets händelser som Azure Defender för Kubernetes-övervakare inkluderar exponerade Kubernetes-instrumentpaneler, skapande av hög privilegierade roller och skapande av känsliga monteringar. En fullständig lista över AKS-aviseringar på kluster nivå finns i [referens tabellen för aviseringar](alerts-reference.md#alerts-akscluster).

> [!TIP]
> Du kan simulera behållar aviseringar genom att följa anvisningarna i [det här blogg inlägget](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270).

Dessutom övervakar vårt globala team av säkerhets forskare det hot landskapet. De lägger till maskinvaruspecifika aviseringar och sårbarheter när de upptäcks.

>[!NOTE]
> Security Center genererar säkerhets aviseringar för Azure Kubernetes service-åtgärder och-distributioner som inträffar **när** du har aktiverat Azure Defender för Kubernetes.




## <a name="azure-defender-for-kubernetes---faq"></a>Azure Defender för Kubernetes – vanliga frågor och svar

### <a name="can-i-still-get-aks-protections-without-the-log-analytics-agent"></a>Kan jag fortfarande få AKS-skydd utan Log Analytics-agenten?

**Azure Defender for Kubernetes** -plan ger skydd på kluster nivå. Om du också distribuerar Log Analytics agenten för **Azure Defender för servrar** får du skydd mot hot för dina noder som medföljer planen. Läs mer i [Introduktion till Azure Defender för servrar](defender-for-servers-introduction.md).

Vi rekommenderar att du distribuerar båda, för det mest kompletta skyddet.

Om du väljer att inte installera agenten på dina värdar får du bara en del av fördelarna med skydd mot hot och säkerhets aviseringar. Du får fortfarande aviseringar om nätverks analyser och kommunikation med skadliga servrar.

### <a name="does-aks-allow-me-to-install-custom-vm-extensions-on-my-aks-nodes"></a>Tillåter AKS att jag installerar anpassade VM-tillägg på mina AKS-noder?
För att Azure Defender ska kunna övervaka dina AKS-noder måste de köra Log Analytics-agenten. 

AKS är en hanterad tjänst och eftersom Log Analytics-agenten är ett Microsoft-hanterat tillägg, stöds det också i AKS-kluster.

### <a name="if-my-cluster-is-already-running-an-azure-monitor-for-containers-agent-do-i-need-the-log-analytics-agent-too"></a>Behöver jag Log Analytics-agenten om mitt kluster redan kör en Azure Monitor för container agent?
För att Azure Defender ska kunna övervaka dina AKS-noder måste de köra Log Analytics-agenten.

Om dina kluster redan kör Azure Monitor för behållare agent kan du installera Log Analytics agenten för och de två agenterna kan arbeta tillsammans med varandra utan problem.

[Läs mer om Azure Monitor for containers agent](../azure-monitor/containers/container-insights-manage-agent.md).


## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om Security Center Kubernetes Protection, inklusive Azure Defender för Kubernetes. 

> [!div class="nextstepaction"]
> [Aktivera Azure Defender](enable-azure-defender.md)

Information om relaterade material finns i följande artiklar: 

- [Strömma aviseringar till en SIEM, SOAR eller IT Service Management-lösning](export-to-siem.md)
- [Referens tabell för aviseringar](alerts-reference.md)
