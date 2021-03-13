---
title: Översikt över översikt över Azure Security benchmark Foundation-exempel
description: Översikt och arkitektur för skiss exemplet för Azure Security benchmark Foundation.
ms.date: 03/12/2021
ms.topic: sample
ms.openlocfilehash: 9630915328b430c409c48e13e0d22f64dbcc99ea
ms.sourcegitcommit: ec39209c5cbef28ade0badfffe59665631611199
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2021
ms.locfileid: "103232077"
---
# <a name="overview-of-the-azure-security-benchmark-foundation-blueprint-sample"></a>Översikt över skiss exemplet för Azure Security benchmark Foundation

Skiss exemplet för Azure Security benchmark Foundation innehåller en uppsättning mönster för bas linje infrastruktur som hjälper dig att skapa en säker och kompatibel Azure-miljö. Skissen hjälper dig att distribuera en molnbaserad arkitektur som erbjuder lösningar till scenarier som har ackrediterings-eller efterlevnads krav. Det här grundläggande skiss exemplet är ett tillägg till [utkastet av Azure Security benchmark-exempel](../azure-security-benchmark.md). Den distribuerar och konfigurerar nätverks gränser, övervakning och andra resurser i enlighet med principerna och andra guardrails som definierats i [Azures säkerhets benchmark](../../../../security/benchmarks/index.yml).

## <a name="architecture"></a>Arkitektur

Den grundläggande miljön som skapas av det här skiss exemplet baseras på arkitekturens huvud namn för en [nav-och ekrars modell](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke).
Skissen distribuerar ett virtuellt hubb nätverk som innehåller gemensamma och delade resurser, tjänster och artefakter som Azure skydds, gateway och brand vägg för anslutning, hantering och hopp Box-undernät som värd för ytterligare/valfria hantering, underhåll, administration och anslutnings infrastruktur. Ett eller flera eker-virtuella nätverk distribueras till värdbaserade program arbets belastningar, till exempel webb-och databas tjänster. Ekers virtuella nätverk är anslutna till det virtuella Hubbs nätverket med hjälp av Azure Virtual Network-peering för sömlös och säker anslutning. Du kan lägga till ytterligare ekrar genom att omtilldela exempel ritningen eller manuellt skapa ett virtuellt Azure-nätverk och peer-koppla det med det virtuella hubb nätverket. All extern anslutning till ekrarnas virtuella nätverk och undernät har kon figurer ATS för att vidarebefordras via hopp rutorna hubb virtuellt nätverk och, via brand vägg, gateway och hantering.

:::image type="content" source="../../media/azure-security-benchmark-foundation/architecture.png" alt-text="Exempel arkitektur diagram för Azure Security benchmark Foundation-skiss" border="false":::

Den här skissen distribuerar flera Azure-tjänster för att tillhandahålla en säker, övervakad, företags färdig grund. Den här miljön består av:

- [Azure Monitor loggar](../../../../azure-monitor/logs/data-platform-logs.md) och ett Azure Storage-konto för att säkerställa resurs loggar, aktivitets loggar, Mät värden och nätverk trafikflöden lagras på en central plats för enkel frågor, analys, arkivering och avisering.
- [Azure Security Center](../../../../security-center/security-center-introduction.md) (standard version) för att skydda hot mot Azure-resurser.
- [Azure Virtual Network](../../../../virtual-network/virtual-networks-overview.md) i hubben som stöder undernät för anslutning tillbaka till ett lokalt nätverk, en ingångs-och utgångs stack till/för Internet anslutning och valfria undernät för distribution av ytterligare administrativa tjänster eller hanterings tjänster. Virtual Network i eker innehåller undernät som är värdar för program arbets belastningar. Ytterligare undernät kan skapas efter distributionen efter behov för att stödja tillämpliga scenarier.
- [Azure-brandväggen](../../../../firewall/overview.md) för att dirigera all utgående Internet trafik och aktivera inkommande Internet trafik via hopp box. (Standard brand Väggs regler blockerar all inkommande och utgående trafik och regler måste konfigureras efter distributionen, efter vad som är tillämpligt.)
- [Nätverks säkerhets grupper](../../../../virtual-network/network-security-group-how-it-works.md) (NSG: er) som är tilldelade till alla undernät (förutom tjänsteägda undernät som Azure skydds, gateway och Azure Firewall) konfigurerade att blockera all inkommande och utgående trafik i Internet.
- [Program säkerhets grupper](../../../../virtual-network/application-security-groups.md) som möjliggör gruppering av virtuella Azure-datorer för att tillämpa vanliga nätverks säkerhets principer.
- [Dirigera tabeller](../../../../virtual-network/manage-route-table.md) för att dirigera all utgående Internet trafik från undernät genom brand väggen. (Azure Firewall och NSG-regler måste konfigureras efter distributionen till öppen anslutning.)
- [Azure Network Watcher](../../../../network-watcher/network-watcher-monitoring-overview.md) för att övervaka, diagnostisera och visa mått för resurser i det virtuella Azure-nätverket.
- [Azure DDoS Protection standard](../../../../ddos-protection/ddos-protection-overview.md) för att skydda Azure-resurser mot DDoS-attacker.
- [Azure skydds](../../../../bastion/bastion-overview.md) för att tillhandahålla sömlös och säker anslutning till en virtuell dator som inte kräver en offentlig IP-adress, agent eller speciell klient program vara.
- [Azure VPN gateway](../../../../vpn-gateway/vpn-gateway-about-vpngateways.md) för att aktivera krypterad trafik mellan ett virtuellt Azure-nätverk och en lokal plats via det offentliga Internet.

> [!NOTE] 
> Azure Security benchmark Foundation innehåller en grundläggande arkitektur för arbets belastningar. Arkitektur diagrammet ovan innehåller flera olika slags resurser för att demonstrera potentiell användning av undernät. Du måste fortfarande distribuera arbets belastningar i den här grundläggande arkitekturen.

## <a name="next-steps"></a>Nästa steg

Du har granskat översikten och arkitekturen i skiss exemplet för Azure Security benchmark Foundation.

> [!div class="nextstepaction"]
> [Azure Security benchmark Foundation-skiss – distribuera steg](./deploy.md)

Ytterligare artiklar om skisser och hur de används:

- Mer information om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).