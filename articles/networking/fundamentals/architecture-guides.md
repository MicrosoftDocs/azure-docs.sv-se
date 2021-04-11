---
title: Dokumentation om Azure Network Architecture
description: Lär dig mer om referens arkitektur dokumentationen som är tillgänglig för Azure nätverks tjänster.
services: networking
author: KumudD
ms.service: virtual-network
ms.topic: article
ms.date: 03/30/2021
ms.author: kumud
ms.openlocfilehash: 9b608312d66e6a3e7455c4577ea4644b33e4e82e
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080398"
---
# <a name="azure-networking-architecture-documentation"></a>Dokumentation om Azure Network Architecture

Den här artikeln innehåller information om arkitektur guider som kan hjälpa dig att utforska de olika nätverks tjänsterna i Azure som du kan använda för att skapa dina program.

## <a name="networking-overview"></a>Nätverksöversikt

Följande tabell innehåller artiklar som innehåller en översikt över en nätverks översikt över ett virtuellt Data Center och en topologi för nav och ekrar i Azure.

|Rubrik |Beskrivning  |
|---------|---------|
|[Virtuella datacenter](/azure/architecture/vdc/networking-virtual-datacenter)   | Tillhandahåller ett nätverks perspektiv för ett virtuellt Data Center i Azure.       |
|[Topologi av typen hub-spoke](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke)  |Ger en översikt över nätverk sto pol Ogin för hubb och ekrar i Azure tillsammans med information om prenumerations gränser och flera hubbar.          |

## <a name="connect-to-azure-resources"></a>Ansluta till Azure-resurser

Följande tabell innehåller artiklar om Azures nätverks tjänster som ger anslutning mellan Azure-resurser, anslutning från ett lokalt nätverk till Azure-resurser och gren anslutning till förgrening i Azure.

|Rubrik |Beskrivning  |
|---------|---------|
|[Lägg till IP-adressutrymme i peer-kopplat virtuella nätverk](/azure/architecture/networking/prefixes/add-ip-space-peered-vnet)     | Innehåller skript som hjälper till att lägga till IP-adressutrymme i peer-kopplat virtuella nätverk.        |
|[Ansluta fristående servrar med hjälp av Azure-nätverkskort](/azure/architecture/hybrid/azure-network-adapter)   | Visar hur du ansluter en lokal fristående server till Microsoft Azure virtuella nätverk med hjälp av det Azure-nätverkskort som du distribuerar via Windows administrations Center.        |
|[Välj mellan peering för virtuella nätverk och VPN-gatewayer](/azure/architecture/reference-architectures/hybrid-networking/vnet-peering)   | Jämför två sätt att ansluta till virtuella nätverk i Azure: peering för virtuella nätverk och VPN-gatewayer.        |
|[Ansluta ett lokalt nätverk till Azure](/azure/architecture/reference-architectures/hybrid-networking/)  | Jämför alternativ för att ansluta ett lokalt nätverk till ett Azure-Virtual Network (VNet). För varje alternativ finns en mer detaljerad referensarkitektur tillgänglig.        |
|[SD – WAN-anslutning med Azure Virtual WAN](../../virtual-wan/sd-wan-connectivity-architecture.md)|Beskriver de olika anslutnings alternativen för att koppla samman ett privat program som definierats med en skyddad WAN-anslutning (SD-WAN) med Azure Virtual WAN.|

## <a name="deploy-highly-available-applications"></a>Distribuera program med hög tillgänglighet

Följande tabell innehåller artiklar som beskriver hur du distribuerar dina program för hög tillgänglighet med hjälp av en kombination av Azure nätverks tjänster.

|Rubrik |Beskrivning  |
|---------|---------|
|[N-Nivåprogram med flera regioner](/azure/architecture/reference-architectures/n-tier/multi-region-sql-server))  | Beskriver ett program på N-nivå med flera regioner som använder Traffic Manager för att dirigera inkommande begär anden till en primär region och om regionen blir otillgänglig, Traffic Manager växlar över till den sekundära regionen.      |
| [SaaS med flera klientorganisationer på Azure](https://docs.microsoft.com/azure/architecture/example-scenario/multi-saas/multitenant-saas)       |   Använder en lösning för flera klienter som innehåller en kombination av frontend-och Application Gateway.  Front dörren hjälper till att belastningsutjämna trafik mellan regioner och Application Gateway vägar och belastnings Utjämnings trafik internt i programmet till de olika tjänsterna som uppfyller klientens affärs behov.  |
| [Webb program med flera nivåer som skapats för hög tillgänglighet och haveri beredskap ](https://docs.microsoft.com/azure/architecture/example-scenario/infrastructure/multi-tier-app-disaster-recovery)        |      Distribuerar elastiska program på flera nivåer som skapats för hög tillgänglighet och haveri beredskap. Om den primära regionen blir otillgänglig, Traffic Manager växlar över till den sekundära regionen.  |
|[IaaS: webb program med relations databas](/azure/architecture/high-availability/ref-arch-iaas-web-and-db)    |   Beskriver hur du använder resurser som sprids över flera zoner för att tillhandahålla en arkitektur med hög tillgänglighet som är värd för en IaaS-webbapp (Infrastructure as a Service) och SQL Server Database.     |
|[Delning av plats i realtid med hjälp av serverlösa Azure-tjänster till en låg kostnad](/azure/architecture/example-scenario/signalr/#azure-front-door)       |   Använder Azures front dörr för att ge högre tillgänglighet för dina program än att distribuera till en enda region. Om ett regionalt avbrott påverkar den primära regionen kan du använda Front Door för att redundansväxla till den sekundära regionen.      |
|[Virtuella nätverksinstallationer med hög tillgänglighet](/azure/architecture/reference-architectures/dmz/nva-ha)     | Visar hur du distribuerar en uppsättning virtuella nätverks enheter (NVA) för hög tillgänglighet i Azure.        |

## <a name="secure-your-network-resources"></a>Skydda dina nätverks resurser

Följande tabell innehåller artiklar som beskriver hur du skyddar dina nätverks resurser med hjälp av Azure nätverks tjänster.

|Rubrik |Beskrivning  |
|---------|---------|
|[Metodtips för nätverkssäkerhet](../../security/fundamentals/network-best-practices.md) |I artikeln beskrivs en samling av Azures metod tips för att förbättra nätverks säkerheten.         |
[Arkitekturguide för Azure Firewall](/azure/architecture/example-scenario/firewalls/) | Innehåller en strukturerad metod för att utforma hög tillgängliga brand väggar i Azure med hjälp av virtuella enheter från tredje part.        |
|[Implementera ett säkert hybridnätverk](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz)     | Beskriver en arkitektur som implementerar en DMZ, även kallat ett perimeternätverk, mellan det lokala nätverket och ett virtuellt Azure-nätverk. All inkommande och utgående trafik passerar genom Azure-brandväggen.        |
|[Skydda och styra arbetsbelastningar med segmentering på nätverksnivå](/azure/architecture/reference-architectures/hybrid-networking/network-level-segmentation) | Beskriver de tre vanliga mönstren som används för att organisera arbets belastningar i Azure från ett nätverks perspektiv.   Var och en av dessa mönster ger en annan typ av isolering och anslutning.      |
|[Brandvägg och Application Gateway för virtuella nätverk](/azure/architecture/example-scenario/gateway/firewall-application-gateway) | Beskriver Azure Virtual Network säkerhets tjänster som Azure Firewall och Azure Application Gateway, när du ska använda varje tjänst och nätverks design alternativ som kombinerar båda.      |

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [Azure Virtual Network](../../virtual-network/virtual-networks-overview.md).
