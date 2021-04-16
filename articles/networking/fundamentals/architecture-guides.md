---
title: Azure-nätverkstjänster arkitekturdokumentation
description: Läs mer om referensarkitekturdokumentationen som är tillgänglig för Azure-nätverkstjänster.
services: networking
author: KumudD
ms.service: virtual-network
ms.topic: article
ms.date: 03/30/2021
ms.author: kumud
ms.openlocfilehash: c98bdbb9fba2a6ba01e4ce590c36d57e68390f17
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484803"
---
# <a name="azure-networking-architecture-documentation"></a>Azure-nätverkstjänster arkitekturdokumentation

Den här artikeln innehåller information om arkitekturguider som kan hjälpa dig att utforska de olika nätverkstjänster i Azure som du kan använda för att skapa dina program.

## <a name="networking-overview"></a>Nätverksöversikt

Följande tabell innehåller artiklar som ger en nätverksöversikt över ett virtuellt datacenter och en topologi med nav och ekrar i Azure.

|Rubrik |Beskrivning  |
|---------|---------|
|[Virtuella datacenter](/azure/architecture/vdc/networking-virtual-datacenter)   | Ger ett nätverksperspektiv för ett virtuellt datacenter i Azure.       |
|[Topologi av typen hub-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)  |Innehåller en översikt över nätverkstopologin för nav och ekrar i Azure tillsammans med information om prenumerationsgränser och flera hubbar.          |

## <a name="connect-to-azure-resources"></a>Ansluta till Azure-resurser

Följande tabell innehåller artiklar om Azure-nätverkstjänster-tjänster som ger anslutning mellan Azure-resurser, anslutning från ett lokalt nätverk till Azure-resurser och gren-till-gren-anslutning i Azure.

|Rubrik |Beskrivning  |
|---------|---------|
|[Lägga till IP-adressutrymmen i peer-baserade virtuella nätverk](/azure/architecture/networking/prefixes/add-ip-space-peered-vnet)     | Innehåller skript som hjälper till att lägga till IP-adressutrymmen i peer-baserade virtuella nätverk.        |
|[Ansluta fristående servrar med hjälp av Azure-nätverkskort](/azure/architecture/hybrid/azure-network-adapter)   | Visar hur du ansluter en lokal fristående server till Microsoft Azure virtuella nätverk med hjälp av Det Azure-nätverkskort som du distribuerar via Windows Admin Center.        |
|[Välj mellan peering för virtuella nätverk och VPN-gatewayer](/azure/architecture/reference-architectures/hybrid-networking/vnet-peering)   | Jämför två sätt att ansluta virtuella nätverk i Azure: peering för virtuella nätverk och VPN-gatewayer.        |
|[Ansluta ett lokalt nätverk till Azure](/azure/architecture/reference-architectures/hybrid-networking/)  | Jämför alternativen för att ansluta ett lokalt nätverk till ett Azure Virtual Network (VNet). För varje alternativ finns en mer detaljerad referensarkitektur tillgänglig.        |
|[SD-WAN-anslutningsarkitektur med Azure Virtual WAN](../../virtual-wan/sd-wan-connectivity-architecture.md)|Beskriver de olika anslutningsalternativen för att ansluta ett privat programvarudefinierat WAN (SD-WAN) med Azure Virtual WAN.|

## <a name="deploy-highly-available-applications"></a>Distribuera program med hög tillgängliga

Följande tabell innehåller artiklar som beskriver hur du distribuerar dina program för hög tillgänglighet med en kombination av Azure-nätverkstjänster tjänster.

|Rubrik |Beskrivning  |
|---------|---------|
|[N-nivåprogram för flera regioner](/azure/architecture/reference-architectures/n-tier/multi-region-sql-server))  | Beskriver ett N-nivåprogram för flera regioner som använder Traffic Manager för att dirigera inkommande begäranden till en primär region och om den regionen blir otillgänglig Traffic Manager till den sekundära regionen.      |
| [SaaS med flera klientorganisationer på Azure](https://docs.microsoft.com/azure/architecture/example-scenario/multi-saas/multitenant-saas)       |   Använder en lösning för flera innehavare som innehåller en kombination av Front Door och Application Gateway.  Front Door hjälper till att belastningsutjämna trafik mellan regioner och Application Gateway vägar och belastningsutjämna trafik internt i programmet till de olika tjänster som uppfyller klientens affärsbehov.  |
| [Webbprogram med flera nivåer som skapats för hög tillgänglighet och haveriberedskap ](https://docs.microsoft.com/azure/architecture/example-scenario/infrastructure/multi-tier-app-disaster-recovery)        |      Distribuerar elastiska flernivåprogram som skapats för hög tillgänglighet och haveriberedskap. Om den primära regionen blir otillgänglig Traffic Manager till den sekundära regionen.  |
|[IaaS: Webbprogram med relationsdatabas](/azure/architecture/high-availability/ref-arch-iaas-web-and-db)    |   Beskriver hur du använder resurser som är utspridda över flera zoner för att tillhandahålla en arkitektur med hög tillgänglighet som värd för en IaaS-webbapp (Infrastruktur som en tjänst) och SQL Server databas.     |
|[Delning av plats i realtid med hjälp av serverlösa Azure-tjänster till en låg kostnad](/azure/architecture/example-scenario/signalr/#azure-front-door)       |   Använder Azure Front Door för att ge högre tillgänglighet för dina program än att distribuera till en enda region. Om ett regionalt avbrott påverkar den primära regionen kan du använda Front Door för att redundansväxla till den sekundära regionen.      |
|[Virtuella nätverksinstallationer med hög tillgänglighet](/azure/architecture/reference-architectures/dmz/nva-ha)     | Visar hur du distribuerar en uppsättning virtuella nätverkstillverk för hög tillgänglighet i Azure.        |
|[Belastningsutjämning i flera regioner med Traffic Manager och Application Gateway](/azure/architecture/high-availability/reference-architecture-traffic-manager-application-gateway)     | Beskriver hur du distribuerar elastiska flernivåprogram i flera Azure-regioner för att uppnå tillgänglighet och en robust infrastruktur för haveriberedskap.        |

## <a name="secure-your-network-resources"></a>Skydda dina nätverksresurser

Följande tabell innehåller artiklar som beskriver hur du skyddar dina nätverksresurser med hjälp Azure-nätverkstjänster tjänster.

|Rubrik |Beskrivning  |
|---------|---------|
|[Metodtips för nätverkssäkerhet](../../security/fundamentals/network-best-practices.md) |Beskriver en samling metodtips för Azure för att förbättra nätverkssäkerheten.         |
[Arkitekturguide för Azure Firewall](/azure/architecture/example-scenario/firewalls/) | Tillhandahåller en strukturerad metod för att utforma brandväggar med hög tillgång i Azure med hjälp av virtuella installationer från tredje part.        |
|[Implementera ett säkert hybridnätverk](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz)     | Beskriver en arkitektur som implementerar en DMZ, även kallat ett perimeternätverk, mellan det lokala nätverket och ett virtuellt Azure-nätverk. All inkommande och utgående trafik passerar genom Azure Firewall.        |
|[Skydda och styra arbetsbelastningar med segmentering på nätverksnivå](/azure/architecture/reference-architectures/hybrid-networking/network-level-segmentation) | Beskriver de tre vanliga mönster som används för att organisera arbetsbelastningar i Azure ur ett nätverksperspektiv.   Vart och ett av dessa mönster ger olika typer av isolering och anslutningsmöjligheter.      |
|[Brandvägg och Application Gateway för virtuella nätverk](/azure/architecture/example-scenario/gateway/firewall-application-gateway) | Beskriver Azure Virtual Network säkerhetstjänster som Azure Firewall och Azure Application Gateway, när du ska använda varje tjänst och alternativ för nätverksdesign som kombinerar båda.      |

## <a name="next-steps"></a>Nästa steg

Läs mer [om Azure Virtual Network](../../virtual-network/virtual-networks-overview.md).
