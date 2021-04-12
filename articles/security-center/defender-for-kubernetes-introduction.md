---
title: Azure Defender för Kubernetes – fördelar och funktioner
description: Lär dig mer om fördelarna och funktionerna i Azure Defender för Kubernetes.
author: memildin
ms.author: memildin
ms.date: 04/07/2021
ms.topic: overview
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: c500c7b7afb36ffbe04fb63551c3a7d17c1347d9
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029088"
---
# <a name="introduction-to-azure-defender-for-kubernetes"></a>Introduktion till Azure Defender för Kubernetes

Azure Defender för Kubernetes är Azure Defender-planen som tillhandahåller skydd för dina Kubernetes-kluster oavsett var de körs. 

Vi kan försvara kluster i:

- **Azure Kubernetes service (AKS)** – Microsofts hanterade tjänst för utveckling, distribution och hantering av program i behållare

- **Lokala miljöer och miljöer med flera moln** – använda ett [tillägg för Arc-aktiverade Kubernetes](defender-for-kubernetes-azure-arc.md)

Azure Security Center-och AKS utgör ett Kubernetes säkerhets erbjudande i molnet med miljö härdning, skydd av arbets belastning och körnings skydd som beskrivs i [behållar säkerhet i Security Center](container-security.md).

Hot identifiering på värdnivå för dina Linux AKS-noder är tillgängligt om du aktiverar [Azure Defender för servrar](defender-for-servers-introduction.md) och dess Log Analytics-agent. Men om klustret har distribuerats på en skal uppsättning för virtuella datorer stöds inte Log Analytics agent för närvarande.



## <a name="availability"></a>Tillgänglighet

|Aspekt|Information|
|----|:----|
|Versions tillstånd:|Allmän tillgänglighet (GA)|
|Priset|**Azure Defender for Kubernetes** faktureras enligt [Security Center prissättning](https://azure.microsoft.com/pricing/details/security-center/)|
|Nödvändiga roller och behörigheter:|**Säkerhets administratören** kan stänga aviseringar.<br>**Säkerhets läsaren** kan visa resultat.|
|Moln|![Ja](./media/icons/yes-icon.png) Kommersiella moln<br>![Ja](./media/icons/yes-icon.png) National/suverän (US Gov, Kina gov, andra gov)|
|||

## <a name="what-are-the-benefits-of-azure-defender-for-kubernetes"></a>Vilka är fördelarna med Azure Defender för Kubernetes?

Azure Defender för Kubernetes tillhandahåller **hot skydd på kluster nivå** genom att övervaka dina kluster loggar.

Exempel på säkerhets händelser som Azure Defender för Kubernetes-övervakare inkluderar exponerade Kubernetes-instrumentpaneler, skapande av hög privilegierade roller och skapande av känsliga monteringar. En fullständig lista över aviseringar på kluster nivå finns i [referens tabellen för aviseringar](alerts-reference.md#alerts-akscluster).

> [!TIP]
> Du kan simulera behållar aviseringar genom att följa anvisningarna i [det här blogg inlägget](https://techcommunity.microsoft.com/t5/azure-security-center/how-to-demonstrate-the-new-containers-features-in-azure-security/ba-p/1011270).

Dessutom övervakar vårt globala team av säkerhets forskare det hot landskapet. De lägger till maskinvaruspecifika aviseringar och sårbarheter när de upptäcks.

>[!NOTE]
> Azure Defender genererar säkerhets aviseringar för åtgärder och distributioner som inträffar när du har aktiverat Defender for Kubernetes-planen i din prenumeration.




## <a name="azure-defender-for-kubernetes---faq"></a>Azure Defender för Kubernetes – vanliga frågor och svar

### <a name="can-i-still-get-cluster-protections-without-the-log-analytics-agent"></a>Kan jag fortfarande få kluster skydd utan Log Analytics-agenten?

**Azure Defender for Kubernetes** -plan ger skydd på kluster nivå. Om du också distribuerar Log Analytics agenten för **Azure Defender för servrar** får du skydd mot hot för dina noder som medföljer planen. Läs mer i [Introduktion till Azure Defender för servrar](defender-for-servers-introduction.md).

Vi rekommenderar att du distribuerar båda, för det mest kompletta skyddet.

Om du väljer att inte installera agenten på dina värdar får du bara en del av fördelarna med skydd mot hot och säkerhets aviseringar. Du får fortfarande aviseringar om nätverks analyser och kommunikation med skadliga servrar.

### <a name="does-aks-allow-me-to-install-custom-vm-extensions-on-my-aks-nodes"></a>Tillåter AKS att jag installerar anpassade VM-tillägg på mina AKS-noder?
För att Azure Defender ska kunna övervaka dina AKS-noder måste de köra Log Analytics-agenten. 

AKS är en hanterad tjänst och eftersom Log Analytics-agenten är ett Microsoft-hanterat tillägg, stöds det också i AKS-kluster.

### <a name="if-my-cluster-is-already-running-an-azure-monitor-for-containers-agent-do-i-need-the-log-analytics-agent-too"></a>Behöver jag Log Analytics-agenten om mitt kluster redan kör en Azure Monitor för container agent?
För att Azure Defender ska kunna övervaka dina noder måste de köra Log Analytics-agenten.

Om dina kluster redan kör Azure Monitor för behållare agent kan du installera Log Analytics agenten för och de två agenterna kan arbeta tillsammans med varandra utan problem.

[Läs mer om Azure Monitor for containers agent](../azure-monitor/containers/container-insights-manage-agent.md).


## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om Security Center Kubernetes Protection, inklusive Azure Defender för Kubernetes. 

> [!div class="nextstepaction"]
> [Aktivera Azure Defender](enable-azure-defender.md)

Information om relaterade material finns i följande artiklar: 

- [Strömma aviseringar till en SIEM, SOAR eller IT Service Management-lösning](export-to-siem.md)
- [Referens tabell för aviseringar](alerts-reference.md)
